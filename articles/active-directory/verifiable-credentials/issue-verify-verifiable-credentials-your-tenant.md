---
title: Samouczek — problem i weryfikacja zweryfikowanych poświadczeń przy użyciu dzierżawy platformy Azure (wersja zapoznawcza)
description: Zmiana przykładowego kodu poświadczeń do zweryfikowania do pracy z dzierżawą platformy Azure
documentationCenter: ''
author: barclayn
manager: daveba
ms.service: identity
ms.topic: how-to
ms.subservice: verifiable-credentials
ms.date: 04/01/2021
ms.author: barclayn
ms.reviewer: ''
ms.openlocfilehash: e4772b6701065a44416d849faa9a501bd7895f27
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/07/2021
ms.locfileid: "106553383"
---
# <a name="tutorial---issue-and-verify-verifiable-credentials-using-your-tenant-preview"></a>Samouczek — problem i weryfikacja zweryfikowanych poświadczeń przy użyciu dzierżawy (wersja zapoznawcza)

> [!IMPORTANT]
> Azure Active Directory poświadczenia do zweryfikowania są obecnie dostępne w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Po skonfigurowaniu dzierżawy platformy Azure przy użyciu zweryfikowanej usługi poświadczeń przeprowadzimy Cię przez kroki niezbędne do umożliwienia Azure Active Directory (Azure AD) w celu wystawienia zweryfikowanych poświadczeń przy użyciu przykładowej aplikacji.

W tym artykule:

> [!div class="checklist"]
> * Rejestrowanie przykładowej aplikacji w usłudze Azure AD
> * Tworzenie reguł i plików wyświetlanych
> * Przekazywanie reguł i plików wyświetlanych
> * Skonfiguruj usługę wystawcy poświadczeń do zweryfikowania pod kątem używania Azure Key Vault
> * Zaktualizuj przykładowy kod przy użyciu informacji o dzierżawie.

Nasz przykładowy kod wymaga, aby użytkownicy uwierzytelniali się u dostawcy tożsamości, w Azure AD B2C przed wystawieniem zweryfikowanego eksperta Credential. Nie wszyscy wystawcy poświadczeń do zweryfikowania wymagają uwierzytelnienia przed wystawieniem poświadczeń.

Uwierzytelnianie tokenów identyfikatorów pozwala użytkownikom potwierdzić, kto przed otrzymaniem poświadczeń. Po pomyślnym zalogowaniu się użytkowników dostawca tożsamości zwraca token zabezpieczający zawierający oświadczenia dotyczące użytkownika. Następnie usługa wystawcy przekształca te tokeny zabezpieczające i ich oświadczenia w zweryfikowane poświadczenia. Zweryfikowane poświadczenia są podpisane przy użyciu wystawcy.

Obsługiwany jest dowolny dostawca tożsamości obsługujący protokół OpenID Connect Connect. Przykłady obsługiwanych dostawców tożsamości obejmują [Azure Active Directory](../fundamentals/active-directory-whatis.md)i [Azure AD B2C](../../active-directory-b2c/overview.md). W tym samouczku korzystamy z usługi AAD.

## <a name="prerequisites"></a>Wymagania wstępne

W tym samouczku założono, że wykonano już kroki opisane w [poprzednim samouczku](enable-your-tenant-verifiable-credentials.md) i masz dostęp do używanego środowiska.

## <a name="register-an-app-to-enable-did-wallets-to-sign-in-users"></a>Zarejestruj aplikację, aby umożliwić użytkownikom logowanie się do użytkowników

Aby możliwe było zweryfikowanie poświadczenia, należy zarejestrować aplikację w celu uwierzytelnienia lub dowolnego innego, zweryfikowanego portfela poświadczeń, aby móc logować użytkowników.  

Zarejestruj aplikację o nazwie "aplikacja portfela VC" w usłudze Azure AD i uzyskaj identyfikator klienta.

1. Postępuj zgodnie z instrukcjami dotyczącymi rejestrowania aplikacji w usłudze [Azure AD](../develop/quickstart-register-app.md) podczas rejestrowania, Użyj poniższych wartości.

   - Name: "aplikacja dla portfela VC"
   - Obsługiwane typy kont: konta tylko w tym katalogu organizacji
   - Identyfikator URI przekierowania: vcclient://openid/

   ![rejestrowanie aplikacji](media/issue-verify-verifable-credentials-your-tenant/register-application.png)

2. Po zarejestrowaniu aplikacji Zapisz identyfikator aplikacji (klienta). Ta wartość będzie potrzebna później.

   ![Identyfikator klienta aplikacji](media/issue-verify-verifable-credentials-your-tenant/client-id.png)

3. Wybierz przycisk **punkty końcowe** i skopiuj identyfikator URI dokumentu metadanych OpenID Connect Connect. Te informacje są potrzebne w następnej sekcji. 

   ![punkty końcowe wystawcy](media/issue-verify-verifable-credentials-your-tenant/application-endpoints.png)

## <a name="set-up-your-node-app-with-access-to-azure-key-vault"></a>Konfigurowanie aplikacji node z dostępem do Azure Key Vault

Aby uwierzytelnić żądanie wystawiania poświadczeń użytkownika, witryna sieci Web wystawcy używa kluczy kryptograficznych w programie Azure Key Vault. Aby uzyskać dostęp do Azure Key Vault, witryna internetowa wymaga identyfikatora klienta i klucza tajnego klienta, którego można użyć do uwierzytelnienia w Azure Key Vault.

1. Podczas wyświetlania strony przegląd aplikacji portfela VC wybierz pozycję **certyfikaty & wpisy tajne**.
    ![Certyfikaty i wpisy tajne](media/issue-verify-verifable-credentials-your-tenant/vc-wallet-app-certs-secrets.png)
1. W sekcji wpisy **tajne klienta** wybierz pozycję **Nowy wpis tajny klienta**
    1. Dodaj opis, taki jak "klucz tajny klienta Node VC"
    1. Wygasa: w ciągu roku.
  ![Wpis tajny aplikacji z jednoletnim okresem ważności](media/issue-verify-verifable-credentials-your-tenant/add-client-secret.png)
1. Skopiuj wpis TAJNy. Te informacje są potrzebne do zaktualizowania przykładowej aplikacji node.

>[!WARNING]
> Istnieje jedna szansa, aby skopiować wpis tajny. Wpis tajny ma jeden ze sposobów mieszania po tym elemencie. Nie Kopiuj identyfikatora. 

Po utworzeniu aplikacji i wpisu tajnego klienta w usłudze Azure AD należy udzielić aplikacji uprawnień niezbędnych do wykonywania operacji na Key Vault. Wprowadzanie tych zmian uprawnień jest wymagane, aby witryna internetowa mogła uzyskać dostęp do przechowywanych w niej kluczy prywatnych.

1. Przejdź do Key Vault.
2. Wybierz magazyn kluczy używany przez nas dla tych samouczków.
3. Wybieranie **zasad dostępu** przy lewej nawigacji
4. Wybierz pozycję **+ Dodaj zasady dostępu**.
5. W sekcji **uprawnienia klucza** wybierz pozycję **Pobierz** i **Podpisz**.
6. Wybierz pozycję **podmiot zabezpieczeń** i użyj identyfikatora aplikacji, aby wyszukać zarejestrowaną wcześniej aplikację. Wybierz ją.
7. Wybierz pozycję **Dodaj**.
8. Wybierz pozycję **Zapisz**.

Aby uzyskać więcej informacji na temat uprawnień Key Vault i kontroli dostępu Odczytaj [Przewodnik RBAC magazynu kluczy](../../key-vault/general/rbac-guide.md)

![Przypisywanie uprawnień magazynu kluczy](media/issue-verify-verifable-credentials-your-tenant/key-vault-permissions.png)
## <a name="make-changes-to-match-your-environment"></a>Wprowadzanie zmian w celu dopasowania do środowiska

Do tej pory pracujemy z naszą przykładową aplikacją. Aplikacja używa [Azure Active Directory B2C](../../active-directory-b2c/overview.md) i teraz przełączamy się do korzystania z usługi Azure AD, dlatego musimy wprowadzić pewne zmiany, które nie są zgodne ze środowiskiem, ale również w celu obsługi dodatkowych oświadczeń, które nie były używane wcześniej.

1. Skopiuj plik reguł poniżej i Zapisz go w **modified-expertRules.js**. 

    > [!NOTE]
    > **"Scope": "profil OpenID Connect"** jest zawarty w tym pliku reguł i nie został uwzględniony w pliku reguł aplikacji przykładowej. W następnej sekcji wyjaśniono, jak włączyć opcjonalne oświadczenia w dzierżawie Azure Active Directory. 
    
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

2. Otwórz plik i Zastąp wartości **client_id** i **konfiguracji** dwiema wartościami, które zostały skopiowane w poprzedniej sekcji.

    ![Wyróżnianie dwóch wartości, które należy zmodyfikować w ramach tego kroku](media/issue-verify-verifable-credentials-your-tenant/rules-file.png)

  Wartość **konfiguracji** jest identyfikator URI dokumentu metadanych OpenID Connect Connect.

  Ponieważ przykładowy kod używa Azure Active Directory B2C i używamy Azure Active Directory, musimy dodać opcjonalne oświadczenia za pośrednictwem zakresów, aby te oświadczenia mogły być zawarte w tokenie identyfikatora w celu zapisania się do zweryfikowanego poświadczenia. 

3. W Azure Portal Otwórz Azure Active Directory.
4. Wybierz **rejestracje aplikacji**.
5. Otwórz utworzoną wcześniej aplikację dla portfela VC.
6. Wybierz pozycję **Konfiguracja tokenu**.
7. Wybieranie **i Dodawanie opcjonalnego żądania**

     ![w obszarze Konfiguracja tokenu Dodaj opcjonalne](media/issue-verify-verifable-credentials-your-tenant/token-configuration.png)

8. W polu **Typ tokenu** wybierz pozycję **Identyfikator** i z listy dostępnych oświadczeń wybierz pozycję **given_name** i **family_name**

     ![Dodaj opcjonalne oświadczenia](media/issue-verify-verifable-credentials-your-tenant/add-optional-claim.png)

9. Kliknij przycisk **Dodaj**.
10. Jeśli zostanie wyświetlone ostrzeżenie o uprawnieniach, jak pokazano poniżej, zaznacz pole wyboru i wybierz pozycję **Dodaj**.

     ![Dodawanie uprawnień do opcjonalnych oświadczeń](media/issue-verify-verifable-credentials-your-tenant/add-optional-claim-permissions.png)

Teraz, gdy użytkownik jest prezentowany jako "Zaloguj", aby wystawić zweryfikowane poświadczenia, aplikacja portfela VC wie o uwzględnieniu określonych oświadczeń za pośrednictwem parametru scope, który ma zostać zapisany do zweryfikowanego poświadczenia.

## <a name="create-new-vc-with-this-rules-file-and-the-old-display-file"></a>Utwórz nowy obwód wirtualny z tym plikiem reguł i starym plikiem wyświetlanym

1. Przekaż nowy plik reguł do naszego kontenera
1. Na stronie zweryfikowane poświadczenia Utwórz nowe poświadczenie o nazwie **modifiedCredentialExpert** przy użyciu starego pliku wyświetlania i pliku nowych reguł (**modified-credentialExpert.json**).
1. Po zakończeniu procesu tworzenia poświadczeń na stronie **Przegląd** Skopiuj **adres URL poświadczeń problemu** i Zapisz go, ponieważ będzie on potrzebny w następnej sekcji.

## <a name="before-we-continue"></a>Przed kontynuowaniem

Przed wprowadzeniem niezbędnych zmian w kodzie musimy umieścić kilka wartości. Te wartości są używane w następnej sekcji, aby kod przykładowy używał własnych kluczy przechowywanych w magazynie. Do tej pory powinny być gotowe następujące wartości.

- **Identyfikator URI kontraktu** dla właśnie utworzonego poświadczenia (adres URL poświadczeń problemu)
- **Identyfikator klienta aplikacji** Mamy to zrobić po zarejestrowaniu aplikacji węzła.
- **Klucz tajny klienta** Te dane zostały utworzone wcześniej, gdy przyznano aplikacji dostęp do magazynu kluczy.

Przed wprowadzeniem zmian w naszej przykładowej aplikacji należy wziąć pod sobą kilka innych wartości. Zacznijmy teraz!

### <a name="verifiable-credentials-settings"></a>Ustawienia zweryfikowanych poświadczeń

1. Przejdź do strony zweryfikowane poświadczenia i wybierz pozycję **Ustawienia**.  
1. Skopiuj następujące wartości:

    - Identyfikator dzierżawy 
    - Identyfikator wystawcy (Twój)
    - Magazyn kluczy (URI)

1. W obszarze identyfikatora klucza podpisywania istnieje identyfikator URI, ale potrzebujemy tylko jego części. Skopiuj z części **issuerSigningKeyION** jako wyróżniony czerwonym prostokątem na poniższej ilustracji.

   ![Identyfikator klucza logowania](media/issue-verify-verifable-credentials-your-tenant/issuer-signing-key-ion.png)

### <a name="did-document"></a>Dokument został przeszedł

1. Otwórz [Eksplorator sieci jonu DIF](https://identity.foundation/ion/explorer/)

2. Wklej swoją zawartość na pasku wyszukiwania.

4. Na podstawie sformatowanej odpowiedzi Znajdź sekcję o nazwie **verificationMethod**
5. W obszarze "verificationMethod" Skopiuj identyfikator i oznacz go jako kvSigningKeyId
    
    ```json=
    "verificationMethod": [
          {
            "id": "#sig_25e48331",
    ```

Teraz mamy wszystko, czego potrzebujemy, aby wprowadzić zmiany w naszym przykładowym kodzie.

- **Wystawca:** app.js zaktualizować poświadczenia const przy użyciu nowego identyfikatora URI kontraktu
- **Weryfikator:** app.js zaktualizować issuerDid przy użyciu identyfikatora wystawcy
- **Emitent i weryfikatorer** aktualizują didconfig.jsna następujących wartościach:


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
>Jest to aplikacja demonstracyjna i zazwyczaj nigdy nie należy podawać bezpośrednio swojej aplikacji.


Teraz wszystko jest gotowe do wystawienia i zweryfikowania własnych poświadczeń do zweryfikowania z dzierżawy usługi Azure Active Directory z własnymi kluczami. 

## <a name="issue-and-verify-the-vc"></a>Wystawianie i weryfikowanie VC

Wykonaj te same czynności, które zostały wykonane w poprzednim samouczku, aby wydać zweryfikowane poświadczenia i zweryfikować je w aplikacji. Po pomyślnym zakończeniu procesu weryfikacji możesz teraz kontynuować uczenie się do zweryfikowania poświadczeń.

1. Otwórz wiersz polecenia i Otwórz folder wystawcy.
2. Uruchom zaktualizowaną aplikację węzła.

    ```terminal
    node app.js
    ```

3. Korzystanie z innego wiersza polecenia ngrok w celu skonfigurowania adresu URL na 8081

    ```terminal
    ngrok http 8081
    ```
    
    >[!IMPORTANT]
    > Możesz również zauważyć, że ta aplikacja lub witryna sieci Web może być ryzykowna. Komunikat jest w tej chwili oczekiwany, ponieważ nie został jeszcze połączony z Twoją domeną. Postępuj zgodnie z instrukcjami dotyczącymi [powiązań DNS](how-to-dnsbind.md) , aby je skonfigurować.

    
4. Otwórz adres URL protokołu HTTPS wygenerowany przez ngrok.

    ![Punkty końcowe przekazywania NGROK](media/enable-your-tenant-verifiable-credentials/ngrok-url-screen.png)

5. Wybierz pozycję **Pobierz POświadczenie**
6. W programie Authenticator Skanuj kod QR.
7. W **tej aplikacji lub witrynie sieci Web może być ryzykowny** komunikat z ostrzeżeniem wybierz pozycję **Zaawansowane**.

  ![Początkowe ostrzeżenie](media/enable-your-tenant-verifiable-credentials/site-warning.png)

8. W przypadku ostrzeżenia dotyczącego ryzykownej witryny sieci Web wybierz pozycję **nadal (niebezpieczne)**

  ![Drugie ostrzeżenie dotyczące wystawcy](media/enable-your-tenant-verifiable-credentials/site-warning-proceed.png)


9. Na ekranie **Dodawanie poświadczeń** Zwróć uwagę na kilka rzeczy: 
    1. W górnej części ekranu można zobaczyć czerwony komunikat **niezweryfikowany**
    1. Poświadczenie jest dostosowywane na podstawie zmian wprowadzonych w pliku wyświetlanym.
    1. Opcja **Zaloguj się do konta** wskazuje na stronę logowania do usługi Azure AD.
    
   ![ekran dodawania poświadczeń z ostrzeżeniem](media/enable-your-tenant-verifiable-credentials/add-credential-not-verified.png)

10. Wybierz pozycję **Zaloguj się do swojego konta** i Uwierzytelnij się przy użyciu użytkownika w dzierżawie usługi Azure AD.
11. Po pomyślnym uwierzytelnieniu przycisku **Dodaj** nie jest już wyszarzony. Wybierz pozycję **Dodaj**.

  ![Dodaj ekran poświadczeń po uwierzytelnieniu](media/enable-your-tenant-verifiable-credentials/add-credential-not-verified-authenticated.png)

Teraz wydano zweryfikowane poświadczenia przy użyciu naszej dzierżawy w celu wygenerowania naszego VC, przy jednoczesnym użyciu naszej dzierżawy usługi B2C na potrzeby uwierzytelniania.

  ![VC wystawiony przez usługę Azure AD i uwierzytelniany przez nasze wystąpienie usługi Azure B2C](media/enable-your-tenant-verifiable-credentials/my-vc-b2c.png)


## <a name="test-verifying-the-vc-using-the-sample-app"></a>Testowanie weryfikacji VC przy użyciu przykładowej aplikacji

Teraz, gdy wydano zweryfikowane poświadczenia z własnej dzierżawy z oświadczeniami z usługi Azure AD, zweryfikujmy je za pomocą naszej przykładowej aplikacji.

1. Zatrzymaj uruchamianie usługi wystawcy ngrok.

    ```cmd
    control-c
    ```

2. Teraz uruchom ngrok z portem weryfikatora 8082.

    ```cmd
    ngrok http 8082
    ```

3. W innym oknie terminalu przejdź do aplikacji weryfikatorer i uruchom ją podobnie jak w przypadku uruchomienia aplikacji wystawcy.

    ```cmd
    cd ..
    cd verifier
    node app.js
    ```

4. Otwórz adres URL ngrok w przeglądarce i Zeskanuj kod QR przy użyciu uwierzytelniania na urządzeniu przenośnym.
5. Na urządzeniu przenośnym wybierz pozycję **Zezwalaj** na **nowym ekranie żądania uprawnień** .

   >[!IMPORTANT]
    > Ponieważ Przykładowa aplikacja używa również do podpisywania żądania prezentacji, zobaczysz ostrzeżenie, że ta aplikacja lub witryna sieci Web może być ryzykowna. Komunikat jest w tej chwili oczekiwany, ponieważ nie został jeszcze połączony z Twoją domeną. Postępuj zgodnie z instrukcjami dotyczącymi [powiązań DNS](how-to-dnsbind.md) , aby je skonfigurować.
    
   ![nowe żądanie uprawnienia](media/enable-your-tenant-verifiable-credentials/new-permission-request.png)

8. Pomyślnie zweryfikowano poświadczenie, a w witrynie sieci Web powinna być wyświetlana imię i nazwisko z konta użytkownika usługi Azure AD. 

Teraz kończysz samouczek i jesteś oficjalnie zweryfikowanym ekspertem ds. poświadczeń! Twoja Przykładowa aplikacja korzysta z Twojej firmy w celu wystawiania i weryfikowania, podczas zapisywania oświadczeń do zweryfikowanego poświadczenia z usługi Azure AD. 

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak tworzyć [poświadczenia niestandardowe](credential-design.md)
- [Przykłady](issuer-openid.md) komunikacji usługi wystawcy
