---
title: Samouczek konfigurowania Azure Active Directory B2C przy użyciu Jumio
titleSuffix: Azure AD B2C
description: W tym samouczku skonfigurujesz Azure Active Directory B2C przy użyciu Jumio na potrzeby zautomatyzowanej weryfikacji identyfikatorów, chroniąc dane klientów.
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/20/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 66ec0d4b09dc983eb898d63d45b3dd7cab291c4c
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/09/2020
ms.locfileid: "96928668"
---
# <a name="tutorial-for-configuring-jumio-with-azure-active-directory-b2c"></a>Samouczek dotyczący konfigurowania Jumio z Azure Active Directory B2C

W tym przykładowym samouczku przedstawiono wskazówki dotyczące integrowania Azure Active Directory B2C (Azure AD B2C) z [Jumio](https://www.jumio.com/). Jumio to usługa weryfikacji identyfikatora, która umożliwia automatyczne weryfikację identyfikatora w czasie rzeczywistym w celu zapewnienia lepszej ochrony danych klientów.

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, musisz:

- Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).

- [Dzierżawa Azure AD B2C](./tutorial-create-tenant.md) , która jest połączona z subskrypcją platformy Azure.

## <a name="scenario-description"></a>Opis scenariusza

Integracja Jumio obejmuje następujące składniki:

- Azure AD B2C: serwer autoryzacji, który jest odpowiedzialny za Weryfikowanie poświadczeń użytkownika. Jest on również znany jako dostawca tożsamości.

- Jumio: usługa, która pobiera szczegóły identyfikatora dostarczone przez użytkownika i weryfikuje je.

- Pośredni interfejs API REST: interfejs API implementujący integrację między Azure AD B2C i usługą Jumio.

- Azure Blob Storage: usługa dostarczająca niestandardowe pliki interfejsu użytkownika do zasad Azure AD B2C.

Na poniższym diagramie architektury przedstawiono implementację.

![Diagram architektury integracji Azure AD B2C z Jumio.](./media/partner-jumio/jumio-architecture-diagram.png)

|Krok | Opis |
|:-----| :-----------|
| 1. | Użytkownik dociera do strony, aby się zalogować, lub zarejestrować się, aby utworzyć konto. Azure AD B2C zbiera atrybuty użytkownika.
| 2. | Azure AD B2C wywołuje interfejs API warstwy środkowej i przekazuje atrybuty użytkownika.
| 3. | Interfejs API warstwy środkowej zbiera atrybuty użytkownika i przekształca je w format, który może zużywać interfejs API Jumio. Następnie wysyła atrybuty do Jumio.
| 4. | Gdy Jumio zużywa informacje i przetwarza je, zwraca wynik do interfejsu API warstwy środkowej.
| 5. | Interfejs API warstwy środkowej przetwarza informacje i wysyła odpowiednie informacje do Azure AD B2C.
| 6. | Azure AD B2C odbiera z powrotem informacje z interfejsu API warstwy środkowej. Jeśli zostanie wyświetlona odpowiedź na awarię, zostanie wyświetlony komunikat o błędzie dla użytkownika. Jeśli zostanie wyświetlona odpowiedź o powodzeniu, użytkownik zostanie uwierzytelniony i zapisany w katalogu.

## <a name="sign-up-with-jumio"></a>Zarejestruj się w usłudze Jumio

Aby utworzyć konto Jumio, skontaktuj się z [Jumio](https://www.jumio.com/contact/).

## <a name="configure-azure-ad-b2c-with-jumio"></a>Konfigurowanie Azure AD B2C przy użyciu Jumio

Po utworzeniu konta Jumio należy użyć konta do skonfigurowania Azure AD B2C. W poniższych sekcjach opisano proces w kolejności.

### <a name="deploy-the-api"></a>Wdrażanie interfejsu API

Wdróż podany [kod interfejsu API](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Jumio/API/Jumio.Api) w usłudze platformy Azure. Kod można opublikować z programu Visual Studio, wykonując poniższe [instrukcje](/visualstudio/deployment/quickstart-deploy-to-azure).

>[!NOTE]
>Aby skonfigurować usługę Azure AD przy użyciu wymaganych ustawień, należy podać adres URL wdrożonej usługi.

### <a name="deploy-the-client-certificate"></a>Wdróż certyfikat klienta

1. Certyfikat klienta pomaga chronić wywołanie interfejsu API Jumio. Utwórz certyfikat z podpisem własnym za pomocą następującego przykładowego kodu programu PowerShell:

   ``` PowerShell
   $cert = New-SelfSignedCertificate -Type Custom -Subject "CN=Demo-SigningCertificate" -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.3") -KeyUsage DigitalSignature -KeyAlgorithm RSA -KeyLength 2048 -NotAfter (Get-Date).AddYears(2) -CertStoreLocation "Cert:\CurrentUser\My"
   $cert.Thumbprint
   $pwdText = "Your password"
   $pwd = ConvertTo-SecureString -String $pwdText -Force -AsPlainText
   Export-PfxCertificate -Cert $Cert -FilePath "{your-local-path}\Demo-SigningCertificate.pfx" -Password $pwd.

   ```

   Certyfikat jest następnie eksportowany do lokalizacji określonej dla ``{your-local-path}`` .

3. Zaimportuj certyfikat do Azure App Service, postępując zgodnie z instrukcjami w [tym artykule](../app-service/configure-ssl-certificate.md#upload-a-private-certificate).

### <a name="create-a-signingencryption-key"></a>Tworzenie klucza podpisywania/szyfrowania

Utwórz losowy ciąg o długości większej niż 64 znaków, która zawiera tylko litery i cyfry.

Na przykład: ``C9CB44D98642A7062A0D39B94B6CDC1E54276F2E7CFFBF44288CEE73C08A8A65``

Aby utworzyć ciąg, użyj następującego skryptu programu PowerShell:

```PowerShell
-join ((0x30..0x39) + ( 0x41..0x5A) + ( 0x61..0x7A) + ( 65..90 ) | Get-Random -Count 64  | % {[char]$_})

```

### <a name="configure-the-api"></a>Konfigurowanie interfejsu API

[Ustawienia aplikacji można skonfigurować w Azure App Service](../app-service/configure-common.md#configure-app-settings). Za pomocą tej metody można bezpiecznie skonfigurować ustawienia bez sprawdzania ich w repozytorium. Należy podać następujące ustawienia w interfejsie API REST:

| Ustawienia aplikacji | Element źródłowy | Uwagi |
| :-------- | :------------| :-----------|
|JumioSettings:AuthUsername | Konfiguracja konta Jumio |     |
|JumioSettings:AuthPassword | Konfiguracja konta Jumio |     |
|AppSettings: SigningCertThumbprint|Odcisk palca utworzonego certyfikatu z podpisem własnym|  |
|AppSettings: IdTokenSigningKey| Klucz podpisywania utworzony przy użyciu programu PowerShell | |
| AppSettings: IdTokenEncryptionKey |Klucz szyfrowania utworzony przy użyciu programu PowerShell
| AppSettings: IdTokenIssuer | Wystawca, który ma być używany dla tokenu JWT (preferowana jest wartość identyfikatora GUID) |
| AppSettings: IdTokenAudience  | Odbiorcy, którzy mają być używani dla tokenu JWT (preferowana jest wartość identyfikatora GUID) |
|AppSettings: BaseRedirectUrl | Podstawowy adres URL zasad Azure AD B2C | https://{Nazwa dzierżawy}. b2clogin. com/{ID aplikacji}|
| WEBSITE_LOAD_CERTIFICATES| Odcisk palca utworzonego certyfikatu z podpisem własnym |

### <a name="deploy-the-ui"></a>Wdróż interfejs użytkownika

1. Skonfiguruj [kontener magazynu obiektów BLOB na koncie magazynu](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container).

2. Przechowywanie plików interfejsu użytkownika z [folderu interfejsu użytkownika](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Jumio/UI) w kontenerze obiektów BLOB.

#### <a name="update-ui-files"></a>Aktualizowanie plików interfejsu użytkownika

1. W plikach interfejsu użytkownika przejdź do folderu [ocean_blue](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Jumio/UI/ocean_blue).

2. Otwórz każdy plik HTML.

3. Znajdź i Zamień na `{your-ui-blob-container-url}` adres URL kontenera obiektów BLOB.

4. Znajdź i Zamień na `{your-intermediate-api-url}` adres URL pośredniej usługi aplikacji interfejsu API.

>[!NOTE]
> Najlepszym rozwiązaniem jest dodanie powiadomienia o zgodzie na stronie kolekcji atrybutów. Powiadom użytkowników, że informacje będą wysyłane do usług innych firm w celu weryfikacji tożsamości.

### <a name="configure-the-azure-ad-b2c-policy"></a>Konfigurowanie zasad Azure AD B2C

1. Przejdź do [zasad Azure AD B2C](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Jumio/Policies) w folderze zasady.

2. Postępuj zgodnie z [tym artykułem](./custom-policy-get-started.md?tabs=applications#custom-policy-starter-pack) , aby pobrać [pakiet LocalAccounts Starter](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/LocalAccounts).

3. Skonfiguruj zasady dla dzierżawy Azure AD B2C.

>[!NOTE]
>Aktualizowanie podanych zasad w celu odłożenia względem określonej dzierżawy.

## <a name="test-the-user-flow"></a>Testowanie przepływu użytkownika

1. Otwórz dzierżawcę Azure AD B2C. W obszarze **zasady** wybierz pozycję **platforma obsługi tożsamości**.

2. Wybierz wcześniej utworzone **SignUpSignIn**.

3. Wybierz pozycję **Uruchom przepływ użytkownika** , a następnie:

   a. W przypadku **aplikacji** wybierz zarejestrowaną aplikację (przykład: JWT).

   b. W polu **adres URL odpowiedzi** wybierz **adres URL przekierowania**.

   c. Wybierz pozycję **Uruchom przepływ użytkownika**.

4. Przejdź do przepływu rejestracji i Utwórz konto.

5. Usługa Jumio zostanie wywołana w przepływie, po utworzeniu atrybutu użytkownika. Jeśli przepływ jest niekompletny, sprawdź, czy użytkownik nie jest zapisany w katalogu.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać dodatkowe informacje, zapoznaj się z następującymi artykułami:

- [Zasady niestandardowe w usłudze Azure AD B2C](./custom-policy-overview.md)

- [Wprowadzenie do zasad niestandardowych w Azure AD B2C](./custom-policy-get-started.md?tabs=applications)
