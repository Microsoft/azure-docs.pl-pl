---
title: Samouczek — wydawanie i weryfikowanie weryfikowalnych poświadczeń przy użyciu dzierżawy platformy Azure (wersja zapoznawcza)
description: Zmiana przykładowego kodu weryfikowalnych poświadczeń w celu współpracy z dzierżawą platformy Azure
documentationCenter: ''
author: barclayn
manager: daveba
ms.service: identity
ms.topic: how-to
ms.subservice: verifiable-credentials
ms.date: 04/01/2021
ms.author: barclayn
ms.reviewer: ''
ms.openlocfilehash: 310c821bf102d267d0b5f77dbf206b896ab2f1c7
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739228"
---
# <a name="tutorial---issue-and-verify-verifiable-credentials-using-your-tenant-preview"></a>Samouczek — wydawanie i weryfikowanie weryfikowalnych poświadczeń przy użyciu dzierżawy (wersja zapoznawcza)

> [!IMPORTANT]
> Azure Active Directory poświadczenia weryfikowalne są obecnie dostępne w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Teraz, po skonfigurowaniu dzierżawy platformy Azure przy użyciu usługi weryfikowalnych poświadczeń, przechodzimy przez kroki niezbędne do umożliwienia usłudze Azure Active Directory (Azure AD) wydawania weryfikowalnych poświadczeń przy użyciu przykładowej aplikacji.

W tym artykule:

> [!div class="checklist"]
> * Rejestrowanie przykładowej aplikacji w usłudze Azure AD
> * Tworzenie reguł i wyświetlanie pliku
> * Przekazywanie reguł i wyświetlanie plików
> * Konfigurowanie usługi wystawcy weryfikowalnych poświadczeń do używania Azure Key Vault
> * Zaktualizuj przykładowy kod przy użyciu informacji o dzierżawie.

Nasz przykładowy kod wymaga, aby użytkownicy uwierzytelnili się u dostawcy tożsamości, Azure AD B2C, zanim będzie można uzyskać weryfikację credential expert VC. Nie wszyscy wystawcy poświadczeń weryfikowalnych wymagają uwierzytelniania przed wystawieniem poświadczeń.

Uwierzytelnianie tokenów identyfikatorów pozwala użytkownikom udowodnić, kim są przed otrzymaniem poświadczeń. Po pomyślnym zalogowaniu się użytkowników dostawca tożsamości zwraca token zabezpieczający zawierający oświadczenia dotyczące użytkownika. Usługa wystawcy przekształca następnie te tokeny zabezpieczające i ich oświadczenia na poświadczenia weryfikowalne. Weryfikowalne poświadczenie jest podpisane za pomocą did wystawcy.

Obsługiwany jest każdy dostawca tożsamości obsługujący OpenID Connect tożsamości. Przykładami obsługiwanych dostawców tożsamości [są Azure Active Directory](../fundamentals/active-directory-whatis.md)i [Azure AD B2C](../../active-directory-b2c/overview.md). W tym samouczku używamy usługi AAD.

## <a name="prerequisites"></a>Wymagania wstępne

W tym samouczku założono, że zostały już wykonane kroki opisane w poprzednim [samouczku](enable-your-tenant-verifiable-credentials.md) i masz dostęp do używanego środowiska.

## <a name="register-an-app-to-enable-did-wallets-to-sign-in-users"></a>Rejestrowanie aplikacji w celu włączenia portfeli DID w celu logowania użytkowników

Aby wydać weryfikowalne poświadczenia, musisz zarejestrować aplikację, aby aplikacja Authenticator lub dowolny inny weryfikowalny portfel poświadczeń mógł logować użytkowników.  

Zarejestruj aplikację o nazwie "Aplikacja VC Portfel" w usłudze Azure AD i uzyskaj identyfikator klienta.

1. Postępuj zgodnie z instrukcjami dotyczącymi rejestrowania aplikacji w usłudze [Azure AD](../develop/quickstart-register-app.md) Podczas rejestrowania użyj poniższych wartości.

   - Nazwa: "Aplikacja VC Portfel"
   - Obsługiwane typy kont: konta tylko w tym katalogu organizacyjnym
   - Przekieruj URI: vcclient://openid/

   ![rejestrowanie aplikacji](media/issue-verify-verifable-credentials-your-tenant/register-application.png)

2. Po zarejestrowaniu aplikacji zapisz identyfikator aplikacji (klienta). Ta wartość będzie potrzebna później.

   ![identyfikator klienta aplikacji](media/issue-verify-verifable-credentials-your-tenant/client-id.png)

3. Wybierz przycisk **Punkty końcowe** i skopiuj OpenID Connect URI dokumentu metadanych. Te informacje będą potrzebne w następnej sekcji. 

   ![punkty końcowe wystawcy](media/issue-verify-verifable-credentials-your-tenant/application-endpoints.png)

## <a name="set-up-your-node-app-with-access-to-azure-key-vault"></a>Konfigurowanie aplikacji Node z dostępem do Azure Key Vault

Aby uwierzytelnić żądanie wystawienia poświadczeń użytkownika, witryna internetowa wystawcy używa kluczy kryptograficznych w Azure Key Vault. Aby uzyskać Azure Key Vault, witryna internetowa musi mieć identyfikator klienta i klucz tajny klienta, których można użyć do uwierzytelniania w Azure Key Vault.

1. Podczas wyświetlania strony przeglądu aplikacji portfel VC wybierz pozycję Certyfikaty i **& tajne.**
    ![certyfikaty i wpisy tajne](media/issue-verify-verifable-credentials-your-tenant/vc-wallet-app-certs-secrets.png)
1. W sekcji **Klucze tajne klienta** wybierz pozycję Nowy klucz tajny **klienta**
    1. Dodaj opis, taki jak "Klucz tajny klienta Node VC"
    1. Wygasa: za jeden rok.
  ![Klucz tajny aplikacji z jednym rokiem wygaśnięcia](media/issue-verify-verifable-credentials-your-tenant/add-client-secret.png)
1. Skopiuj klucz SECRET. Te informacje są potrzebne do zaktualizowania przykładowej aplikacji Node.

>[!WARNING]
> Masz jedną szansę skopiowania tego tajnego. Klucz tajny jest jednym ze sposobów wyznaczania wartości skrótu po tym. Nie kopiuj identyfikatora. 

Po utworzeniu aplikacji i tajnego klienta w usłudze Azure AD należy udzielić aplikacji uprawnień niezbędnych do wykonywania operacji na Key Vault. Wprowadzenie tych zmian uprawnień jest wymagane, aby umożliwić witrynie internetowej dostęp do przechowywanych w nim kluczy prywatnych i korzystanie z nich.

1. Przejdź do Key Vault.
2. Wybierz magazyn kluczy, z których korzystamy w tych samouczkach.
3. Wybieranie **zasad dostępu w** lewym okienku nawiązań
4. Wybierz **pozycję +Dodaj zasady dostępu.**
5. W sekcji **Uprawnienia klucza wybierz** pozycję **Pobierz** i **podpisz**.
6. Wybierz **pozycję Principal** (Podmiot zabezpieczeń) i użyj identyfikatora aplikacji, aby wyszukać zarejestrowaną wcześniej aplikację. Wybierz ją.
7. Wybierz pozycję **Dodaj**.
8. Wybierz **pozycję SAVE (ZAPISZ).**

Aby uzyskać więcej informacji na temat Key Vault kontroli dostępu i kontroli dostępu, przeczytaj przewodnik [RBAC magazynu kluczy](../../key-vault/general/rbac-guide.md)

![przypisywanie uprawnień magazynu kluczy](media/issue-verify-verifable-credentials-your-tenant/key-vault-permissions.png)
## <a name="make-changes-to-match-your-environment"></a>Wprowadzaj zmiany w celu dopasowania do środowiska

Do tej pory pracowaliśmy z naszą przykładową aplikacją. Aplikacja używa usługi Azure Active Directory B2C i teraz przełączamy się do korzystania z usługi Azure AD, więc musimy wprowadzić pewne zmiany nie tylko w celu dopasowania do twojego środowiska, [ale](../../active-directory-b2c/overview.md) również obsługi dodatkowych oświadczeń, które nie były wcześniej używane.

1. Skopiuj poniższy plik reguł i zapisz go w pliku **modified-expertRules.jsna stronie**. 

    > [!NOTE]
    > **"zakres": "profil openid"** znajduje się w tym pliku reguł i nie został uwzględniony w pliku reguł przykładowej aplikacji. W następnej sekcji wyjaśniono, jak włączyć opcjonalne oświadczenia w dzierżawie Azure Active Directory dzierżawy. 
    
    ```json
    {
      "attestations": {
        "idTokens": [
          {
            "mapping": {
              "firstName": { "claim": "given_name" },
              "lastName": { "claim": "family_name" }
            },
            "configuration": "https://dIdPlayground.b2clogin.com/dIdPlayground.onmicrosoft.com/B2C_1_sisu/v2.0/.well-known/openid-configuration",
            "client_id": "8d5b446e-22b2-4e01-bb2e-9070f6b20c90",
            "redirect_uri": "vcclient://openid/",
             "scope": "openid profile"
          }
        ]
      },
      "validityInterval": 2592000,
      "vc": {
        "type": ["VerifiedCredentialExpert"]
      }
    }
    ```

2. Otwórz plik i  zastąp wartości client_id **konfiguracji** dwiema wartościami skopiowaymi w poprzedniej sekcji.

    ![wyróżnianie dwóch wartości, które należy zmodyfikować w ramach tego kroku](media/issue-verify-verifable-credentials-your-tenant/rules-file.png)

  Wartość **Configuration (Konfiguracja)** jest OpenID Connect URI dokumentu metadanych.

  Ponieważ przykładowy kod korzysta z usługi Azure Active Directory B2C i używamy usługi Azure Active Directory, musimy dodać opcjonalne oświadczenia za pośrednictwem zakresów, aby te oświadczenia zostały uwzględnione w tokenie identyfikatora, aby zostały zapisane w weryfikowalnym poświadczeniu. 

3. Po powrocie do Azure Portal otwórz Azure Active Directory.
4. Wybierz **Rejestracje aplikacji**.
5. Otwórz utworzoną wcześniej aplikację VC Portfel.
6. Wybierz **pozycję Konfiguracja tokenu.**
7. Wybierz **pozycję + Dodaj opcjonalne oświadczenie**

     ![w obszarze konfiguracja tokenu dodaj opcjonalne oświadczenie](media/issue-verify-verifable-credentials-your-tenant/token-configuration.png)

8. Z **listy Typ tokenu** wybierz **identyfikator,** a z listy dostępnych oświadczeń **wybierz** given_name i **family_name**

     ![dodawanie opcjonalnych oświadczeń](media/issue-verify-verifable-credentials-your-tenant/add-optional-claim.png)

9. Kliknij przycisk **Dodaj**.
10. Jeśli zostanie wyświetlone ostrzeżenie o uprawnieniach, jak pokazano poniżej, zaznacz pole wyboru i wybierz pozycję **Dodaj**.

     ![dodawanie uprawnień dla opcjonalnych oświadczeń](media/issue-verify-verifable-credentials-your-tenant/add-optional-claim-permissions.png)

Teraz, gdy użytkownik zostanie przedstawiony z "logowaniem", aby uzyskać poświadczenia weryfikowalne, aplikacja VC Portfel wie, że uwzględnia określone oświadczenia za pośrednictwem parametru zakresu, który ma zostać zapisany w poświadczeniach weryfikowalnych.

## <a name="create-new-vc-with-this-rules-file-and-the-old-display-file"></a>Utwórz nową VC z tym plikiem reguł i starym plikiem wyświetlania

1. Przekazywanie nowego pliku reguł do kontenera
1. Na stronie weryfikowalnych poświadczeń utwórz nowe poświadczenie o nazwie **modifiedCredentialExpert** przy użyciu starego pliku wyświetlania i nowego pliku reguł (modified-credentialExpert.js **pliku**).
1. Po zakończeniu procesu tworzenia poświadczeń na **stronie** Przegląd skopiuj adres **URL** poświadczenia problemu i zapisz go, ponieważ będzie on potrzebny w następnej sekcji.

## <a name="before-we-continue"></a>Przed kontynuowaniem

Zanim będziemy w stanie wprowadzić niezbędne zmiany w kodzie, musimy złożyć ze sobą kilka wartości. Użyjemy tych wartości w następnej sekcji, aby przykładowy kod używał własnych kluczy przechowywanych w magazynie. Do tej pory następujące wartości powinny być gotowe.

- **Contract URI** from the credential that we just created (Problem z adresem URL poświadczeń)
- **Identyfikator klienta aplikacji** Dostaliśmy to podczas rejestrowania aplikacji Node.
- **Klucz tajny klienta** Utworzono je wcześniej, gdy przyznaliśmy aplikacji dostęp do magazynu kluczy.

Istnieje kilka innych wartości, które musimy uzyskać, zanim będzie można wprowadzić zmiany jeden raz w naszej przykładowej aplikacji. Pobierzmy je teraz!

### <a name="verifiable-credentials-settings"></a>Weryfikowalne ustawienia poświadczeń

1. Przejdź do strony Poświadczenia weryfikowalne i wybierz pozycję **Ustawienia.**  
1. Skopiuj następujące wartości:

    - Identyfikator dzierżawy 
    - Identyfikator wystawcy (Did)
    - Magazyn kluczy (URI)

1. W obszarze Identyfikator klucza podpisywania znajduje się identyfikator URI, ale potrzebujemy tylko jego części. Skopiuj z części o wartości **issuerSigningKeyION** wyróżnioną czerwonym prostokątem na poniższej ilustracji.

   ![identyfikator klucza logowania](media/issue-verify-verifable-credentials-your-tenant/issuer-signing-key-ion.png)

### <a name="did-document"></a>DID document (DOKUMENT DID)

1. Otwórz okno [dif ION Eksplorator sieci](https://identity.foundation/ion/explorer/)

2. Wklej swoją did na pasku wyszukiwania.

4. W sformatowanych odpowiedziach znajdź sekcję o nazwie **verificationMethod**
5. W obszarze "verificationMethod" skopiuj identyfikator i oznacz go jako kvSigningKeyId
    
    ```json=
    "verificationMethod": [
          {
            "id": "#sig_25e48331",
    ```

Teraz mamy wszystko, czego potrzebujemy, aby wprowadzić zmiany w naszym przykładowym kodzie.

- **Wystawca:** app.js poświadczenia const przy użyciu nowego uri kontraktu
- **Weryfikator:** app.js zaktualizować identyfikator issuerDid przy użyciu identyfikatora wystawcy
- **Wystawca i weryfikator** aktualizują didconfig.jso następujące wartości:


```json=
{
    "azTenantId": "Your tenant ID",
    "azClientId": "Your client ID",
    "azClientSecret": "Your client secret",
    "kvVaultUri": "your keyvault uri",
    "kvSigningKeyId": "The verificationMethod ID from your DID Document",
    "kvRemoteSigningKeyId" : "The snippet of the issuerSigningKeyION we copied ",
    "did": "Your DID"
}
```

>[!IMPORTANT]
>Jest to aplikacja demonstracyjna i zwykle nigdy nie należy bezpośrednio podawać aplikacji tajnego.


Teraz masz wszystko na miejscu, aby wystawiać i weryfikować własne poświadczenia weryfikowalne z dzierżawy Azure Active Directory przy użyciu własnych kluczy. 

## <a name="issue-and-verify-the-vc"></a>Wydawanie i weryfikowanie wzorca VC

Wykonaj kroki opisane w poprzednim samouczku, aby wydać weryfikowalne poświadczenie i zweryfikować je za pomocą aplikacji. Po pomyślnym ukończeniu procesu weryfikacji możesz kontynuować poznawczy dostęp do weryfikowalnych poświadczeń.

1. Otwórz wiersz polecenia i otwórz folder wystawcy.
2. Uruchom zaktualizowaną aplikację node.

    ```terminal
    node app.js
    ```

3. Przy użyciu innego wiersza polecenia uruchom ngrok, aby skonfigurować adres URL na adresie 8081

    ```terminal
    ngrok http 8081
    ```
    
    >[!IMPORTANT]
    > Możesz również zauważyć ostrzeżenie, że ta aplikacja lub witryna internetowa może być ryzykowna. Komunikat jest obecnie oczekiwany, ponieważ nie powiązyliśmy jeszcze Twojej aplikacji DID z Twoją domeną. Aby to [skonfigurować, postępuj](how-to-dnsbind.md) zgodnie z instrukcjami dotyczącymi powiązań DNS.

    
4. Otwórz adres URL protokołu HTTPS wygenerowany przez ngrok.

    ![Punkty końcowe przekazywania NGROK](media/enable-your-tenant-verifiable-credentials/ngrok-url-screen.png)

5. Wybierz pozycję **GET CREDENTIAL (POBIERZ POŚWIADCZENIE)**
6. W aplikacji Authenticator zeskanuj kod QR.
7. Na **stronie Ta aplikacja lub witryna internetowa może być ryzykowny komunikat** ostrzegawczy wybierz pozycję **Zaawansowane.**

  ![Ostrzeżenie początkowe](media/enable-your-tenant-verifiable-credentials/site-warning.png)

8. W ostrzeżeniu ryzykownych witryn internetowych wybierz **pozycję Kontynuuj mimo to (niebezpieczne)**

  ![Drugie ostrzeżenie dotyczące wystawcy](media/enable-your-tenant-verifiable-credentials/site-warning-proceed.png)


9. Na **ekranie Dodawanie poświadczeń** zwróć uwagę na kilka rzeczy: 
    1. W górnej części ekranu jest wyświetlany czerwony komunikat **Nie zweryfikowano**
    1. Poświadczenia są dostosowywane na podstawie zmian wprowadzonych w pliku wyświetlania.
    1. Opcja **Zaloguj się do swojego konta** oznacza stronę logowania usługi Azure AD.
    
   ![ekran dodawania poświadczeń z ostrzeżeniem](media/enable-your-tenant-verifiable-credentials/add-credential-not-verified.png)

10. Wybierz **pozycję Zaloguj się do swojego konta i** uwierzytelnij się przy użyciu użytkownika w dzierżawie usługi Azure AD.
11. Po pomyślnym uwierzytelnieniu **przycisk Dodaj** nie jest już wyszrzany. Wybierz **pozycję Dodaj**.

  ![ekran dodawania poświadczeń po uwierzytelnieniu](media/enable-your-tenant-verifiable-credentials/add-credential-not-verified-authenticated.png)

Wydaliśmy weryfikowalne poświadczenie przy użyciu dzierżawy w celu wygenerowania pliku vc, nadal używając oryginalnej dzierżawy B2C do uwierzytelniania.

  ![vc wystawiony przez usługę Azure AD i uwierzytelniony przez nasze wystąpienie usługi Azure B2C](media/enable-your-tenant-verifiable-credentials/my-vc-b2c.png)


## <a name="test-verifying-the-vc-using-the-sample-app"></a>Testowanie weryfikowania wzorca VC przy użyciu przykładowej aplikacji

Teraz, gdy wystawiliśmy weryfikowalne poświadczenia z Twojej własnej dzierżawy z oświadczeniami z usługi Azure AD, zweryfikujmy je przy użyciu przykładowej aplikacji.

1. Zatrzymaj uruchamianie usługi wystawcy ngrok.

    ```cmd
    control-c
    ```

2. Teraz uruchom ngrok z portem weryfikatora 8082.

    ```cmd
    ngrok http 8082
    ```

3. W innym oknie terminalu przejdź do aplikacji weryfikatora i uruchom ją w podobny sposób, jak uruchomiliśmy aplikację wystawcy.

    ```cmd
    cd ..
    cd verifier
    node app.js
    ```

4. Otwórz adres URL narzędzia ngrok w przeglądarce i zeskanuj kod QR przy użyciu aplikacji Authenticator na urządzeniu przenośnym.
5. Na urządzeniu przenośnym wybierz pozycję **Zezwalaj** na **ekranie Nowe żądanie** uprawnień.

   >[!IMPORTANT]
    > Ponieważ przykładowa aplikacja również używa Twojej funkcji DID do podpisania żądania prezentacji, zauważysz ostrzeżenie, że ta aplikacja lub witryna internetowa może być ryzykowna. Komunikat jest obecnie oczekiwany, ponieważ nie powiązyliśmy jeszcze Twojej domeny z Domeną. Aby to [skonfigurować, postępuj](how-to-dnsbind.md) zgodnie z instrukcjami dotyczącymi powiązań DNS.
    
   ![nowe żądanie uprawnień](media/enable-your-tenant-verifiable-credentials/new-permission-request.png)

8. Po pomyślnym zweryfikowaniu poświadczeń w witrynie internetowej powinno być wyświetlane Twoje imię i nazwisko z konta użytkownika usługi Azure AD. 

Ukończenie tego samouczka jest już oficjalnie specjalistą ds. zweryfikowanych poświadczeń. Przykładowa aplikacja używa twojej funkcji DID zarówno do wystawiania, jak i weryfikowania, podczas zapisywania oświadczeń w weryfikowalnych poświadczeniach z usługi Azure AD. 

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak utworzyć [poświadczenia niestandardowe](credential-design.md)
- Przykłady komunikacji z usługami [wystawców](issuer-openid.md)
