---
title: Samouczek konfigurowania Azure Active Directory B2C przy użyciu Jumio
titleSuffix: Azure AD B2C
description: Samouczek konfigurowania Azure Active Directory B2C przy użyciu usługi Jumio na potrzeby zautomatyzowanej weryfikacji identyfikatora, ochrony danych klienta
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/20/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 47e06c84eb2e0463b31b7bdea5897ceca4339419
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/25/2020
ms.locfileid: "88817550"
---
# <a name="tutorial-for-configuring-jumio-with-azure-active-directory-b2c"></a>Samouczek dotyczący konfigurowania Jumio z Azure Active Directory B2C

W tym przykładowym samouczku przedstawiono wskazówki dotyczące integrowania Azure AD B2C z usługą [Jumio](https://www.jumio.com/). Jumio to usługa weryfikacji identyfikatora, która umożliwia automatyczne weryfikację identyfikatora w czasie rzeczywistym, chroniąc dane klientów.

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, musisz:

- Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).

- [Dzierżawa Azure AD B2C](https://review.docs.microsoft.com/azure/active-directory-b2c/tutorial-create-tenant). Dzierżawca jest połączony z subskrypcją platformy Azure.

## <a name="scenario-description"></a>Opis scenariusza

Integracja Jumio obejmuje następujące składniki:

- Azure AD B2C — serwer autoryzacji odpowiedzialny za Weryfikowanie poświadczeń użytkownika, znanego również jako dostawca tożsamości

- Jumio — usługa, która pobiera szczegóły identyfikatora dostarczone przez użytkownika i weryfikuje je.

- Pośredni interfejs API REST — ten interfejs API implementuje integrację między Azure AD B2C a usługą Jumio.

- BLOB Storage — służy do dostarczania niestandardowych plików interfejsu użytkownika do zasad Azure AD B2C.

Na poniższym diagramie architektury przedstawiono implementację.

![Zrzut ekranu przedstawiający diagram jumio-Architecture](./media/partner-jumio/jumio-architecture-diagram.png)

|Krok | Opis |
|:-----| :-----------|
| 1. | Użytkownik dotarł do strony logowania. Użytkownicy wybierają konto, aby utworzyć nowe konto, a następnie wprowadzić informacje na stronie. Azure AD B2C zbiera atrybuty użytkownika.
| 2. | Azure AD B2C wywołuje interfejs API warstwy środkowej i przekazuje atrybuty użytkownika.
| 3. | Interfejs API warstwy środkowej zbiera atrybuty użytkownika i przekształca je w format, który może zużywać interfejs API Jumio. Następnie po wysłaniu go do Jumio.
| 4. | Gdy Jumio zużywa informacje i przetwarza je, zwraca wynik do interfejsu API warstwy środkowej.
| 5. | Interfejs API warstwy środkowej przetwarza informacje i wysyła odpowiednie informacje do Azure AD B2C.
| 6. | Azure AD B2C otrzymuje informacje z z interfejsu API platformy środkowej. Jeśli zostanie wyświetlona odpowiedź na awarię, zostanie wyświetlony komunikat o błędzie dla użytkownika. Jeśli zostanie wyświetlona odpowiedź o powodzeniu, użytkownik zostanie uwierzytelniony i zapisany w katalogu.

## <a name="onboard-with-jumio"></a>Dołączanie do Jumio

1. Aby utworzyć konto Jumio, skontaktuj się z [Jumio](https://www.jumio.com/contact/)

2. Po utworzeniu konta informacje są używane w konfiguracji interfejsu API. W poniższych sekcjach opisano proces.

## <a name="configure-azure-ad-b2c-with-jumio"></a>Konfigurowanie Azure AD B2C przy użyciu Jumio

### <a name="part-1---deploy-the-api"></a>Część 1 — wdrażanie interfejsu API

Wdróż podany [kod interfejsu API](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Jumio/API/Jumio.Api) w usłudze platformy Azure. Kod można opublikować z programu Visual Studio, postępując zgodnie z tymi [instrukcjami](https://docs.microsoft.com/visualstudio/deployment/quickstart-deploy-to-azure?view=vs-2019).

>[!NOTE]
>Aby skonfigurować usługę Azure AD przy użyciu wymaganych ustawień, należy podać adres URL wdrożonej usługi.

### <a name="part-2---deploy-the-client-certificate"></a>Część 2 — Wdrażanie certyfikatu klienta

1. Wywołanie interfejsu API Jumio jest chronione przez certyfikat klienta. Utwórz certyfikat z podpisem własnym za pomocą przykładowego kodu programu PowerShell wymienionego poniżej:

``` PowerShell
$cert = New-SelfSignedCertificate -Type Custom -Subject "CN=Demo-SigningCertificate" -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.3") -KeyUsage DigitalSignature -KeyAlgorithm RSA -KeyLength 2048 -NotAfter (Get-Date).AddYears(2) -CertStoreLocation "Cert:\CurrentUser\My"
$cert.Thumbprint
$pwdText = "Your password"
$pwd = ConvertTo-SecureString -String $pwdText -Force -AsPlainText
Export-PfxCertificate -Cert $Cert -FilePath "{your-local-path}\Demo-SigningCertificate.pfx" -Password $pwd.

```

2. Certyfikat zostanie następnie wyeksportowany do lokalizacji określonej dla elementu { ``your-local-path`` }.

3. Postępując zgodnie z instrukcjami wymienionymi w tym [dokumencie](https://docs.microsoft.com/azure/app-service/configure-ssl-certificate#upload-a-private-certificate), zaimportuj certyfikat do usługi Azure App Service.

### <a name="part-3---create-a-signingencryption-key"></a>Część 3 — Tworzenie klucza podpisywania/szyfrowania

Utwórz losowy ciąg o długości większej niż 64 znaków, która zawiera tylko litery lub cyfry.

Na przykład: ``C9CB44D98642A7062A0D39B94B6CDC1E54276F2E7CFFBF44288CEE73C08A8A65``

Użyj poniższego skryptu programu PowerShell, aby utworzyć wartość alfanumeryczną o długości 64 znaków.

```PowerShell
-join ((0x30..0x39) + ( 0x41..0x5A) + ( 0x61..0x7A) + ( 65..90 ) | Get-Random -Count 64  | % {[char]$_})

```

### <a name="part-4---configure-the-api"></a>Część 4 — Konfigurowanie interfejsu API

Ustawienia aplikacji można [skonfigurować w usłudze App Service na platformie Azure](https://docs.microsoft.com/azure/app-service/configure-common#configure-app-settings). Za pomocą tej metody ustawienia można bezpiecznie skonfigurować bez sprawdzania ich w repozytorium. Należy podać następujące ustawienia w interfejsie API REST:

| Ustawienia aplikacji | Element źródłowy | Uwagi |
| :-------- | :------------| :-----------|
|JumioSettings:AuthUsername | Konfiguracja konta Jumio |     |
|JumioSettings:AuthPassword | Konfiguracja konta Jumio |     |
|AppSettings: SigningCertThumbprint|Utworzono odcisk palca certyfikatu z podpisem własnym|  |
|AppSettings: IdTokenSigningKey| Klucz podpisywania utworzony przy użyciu programu PowerShell | |
| AppSettings: IdTokenEncryptionKey |Klucz szyfrowania utworzony przy użyciu programu PowerShell
| AppSettings: IdTokenIssuer | Wystawca, który ma być używany dla tokenu JWT (preferowana jest wartość identyfikatora GUID) |
| AppSettings: IdTokenAudience  | Odbiorcy, którzy mają być używani dla tokenu JWT (preferowana jest wartość identyfikatora GUID) |
|AppSettings: BaseRedirectUrl | Podstawowy adres URL zasad B2Cymi | https://{Nazwa dzierżawy}. b2clogin. com/{ID aplikacji}|
| WEBSITE_LOAD_CERTIFICATES| Utworzono odcisk palca certyfikatu z podpisem własnym |

### <a name="part-5---deploy-the-ui"></a>Część 5 — wdrażanie interfejsu użytkownika

1. Skonfiguruj [kontener magazynu obiektów BLOB na koncie magazynu](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal#create-a-container).

2. Zapisz pliki interfejsu użytkownika z [folderu UI](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Jumio/UI) do kontenera obiektów BLOB.

#### <a name="update-ui-files"></a>Aktualizowanie plików interfejsu użytkownika

1. W plikach interfejsu użytkownika przejdź do folderu [ocean_blue](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Jumio/UI/ocean_blue)

2. Otwórz każdy plik HTML.

3. Znajdź i Zamień {interfejs użytkownika-obiekt BLOB-Container-URL} z adresem URL kontenera obiektów BLOB.

4. Znajdź i Zamień ciąg {pośredni-API-URL} adresem URL pośredniczącej usługi aplikacji interfejsu API.

>[!NOTE]
> Najlepszym rozwiązaniem jest, aby klienci mogli dodać powiadomienie o zgodzie na stronie kolekcji atrybutów. Powiadom użytkowników, że informacje będą wysyłane do usług innych firm w celu weryfikacji tożsamości.

### <a name="part-6---configure-the-azure-ad-b2c-policy"></a>Część 6 — Konfigurowanie zasad Azure AD B2C

1. Przejdź do [zasad Azure AD B2C](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Jumio/Policies) w folderze zasady.

2. Postępuj zgodnie z tym [dokumentem](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started?tabs=applications#custom-policy-starter-pack) , aby pobrać [LocalAccounts Starter Pack](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/LocalAccounts)

3. Skonfiguruj zasady dla dzierżawy Azure AD B2C.

>[!NOTE]
>Aktualizowanie podanych zasad w celu odłożenia względem określonej dzierżawy.

## <a name="test-the-user-flow"></a>Testowanie przepływu użytkownika

1. Otwórz dzierżawcę Azure AD B2C i w obszarze zasady wybierz pozycję **platforma obsługi tożsamości**.

2. Wybierz wcześniej utworzone **SignUpSignIn**.

3. Wybierz pozycję **Uruchom przepływ użytkownika** i wybierz ustawienia:

   a. **Aplikacja**: wybór zarejestrowanej aplikacji (przykład: JWT)

   b. **Adres URL odpowiedzi**: Wybierz **adres URL przekierowania**

   c. Wybierz pozycję **Uruchom przepływ użytkownika**.

4. Przejdź przez przepływ rejestracji i Utwórz konto

5. Usługa Jumio zostanie wywołana w przepływie, po utworzeniu atrybutu użytkownika. Jeśli przepływ jest niekompletny, sprawdź, czy użytkownik nie jest zapisany w katalogu.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać dodatkowe informacje, zapoznaj się z następującymi artykułami:

- [Zasady niestandardowe w usłudze Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-overview)

- [Wprowadzenie do zasad niestandardowych w Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started?tabs=applications)
