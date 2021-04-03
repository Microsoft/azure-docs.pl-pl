---
title: Konfigurowanie logowania za pomocą firmy Apple (wersja zapoznawcza)
description: Dowiedz się, jak skonfigurować logowanie za pomocą firmy Apple jako dostawcy tożsamości dla aplikacji App Service lub Azure Functions.
ms.topic: article
ms.date: 11/19/2020
ms.reviewer: mikarmar
ms.openlocfilehash: b77e0613f502d003b5e4651e34be4cadbd4209a9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96603361"
---
# <a name="configure-your-app-service-or-azure-functions-app-to-sign-in-using-a-sign-in-with-apple-provider-preview"></a>Skonfiguruj App Service lub aplikację Azure Functions, aby zalogować się przy użyciu logowania za pomocą dostawcy firmy Apple (wersja zapoznawcza)

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

W tym artykule opisano sposób konfigurowania Azure App Service lub Azure Functions do korzystania z logowania za pomocą firmy Apple jako dostawcy uwierzytelniania. 

Aby wykonać procedurę opisaną w tym artykule, musisz zarejestrować się w programie Apple Developer. Aby zarejestrować się w programie Apple Developer, przejdź do [developer.Apple.com/programs/Enroll](https://developer.apple.com/programs/enroll/).

> [!CAUTION]
> Włączenie logowania za pomocą firmy Apple spowoduje wyłączenie zarządzania funkcją uwierzytelniania App Service/autoryzacją dla aplikacji za pomocą niektórych klientów, takich jak Azure Portal, interfejs wiersza polecenia platformy Azure i Azure PowerShell. Ta funkcja opiera się na nowej powierzchni interfejsu API, która w trakcie korzystania z wersji zapoznawczej nie jest jeszcze uwzględniona we wszystkich środowiskach zarządzania.

[comment]: <Remove this caution block when V2 becomes available for use.> 

## <a name="create-an-application-in-the-apple-developer-portal"></a><a name="createApplication"> </a>Tworzenie aplikacji w portalu dla deweloperów firmy Apple
Musisz utworzyć identyfikator aplikacji i identyfikator usługi w portalu dla deweloperów firmy Apple.

1. W portalu dla deweloperów firmy Apple przejdź do pozycji **certyfikaty, identyfikatory, & profile**.
2. Na karcie **identyfikatory** wybierz przycisk **(+)** .
3. Na stronie **Zarejestruj nowy identyfikator** wybierz pozycję **identyfikatory aplikacji** , a następnie wybierz pozycję **Kontynuuj**. (Identyfikatory aplikacji obejmują co najmniej jeden identyfikator usługi). ![Rejestrowanie nowego identyfikatora aplikacji w portalu dla deweloperów firmy Apple](media/configure-authentication-provider-apple/apple-register-app.jpg)
4. Na stronie **Rejestrowanie identyfikatora aplikacji** Podaj opis i identyfikator pakietu, a następnie wybierz pozycję **Zaloguj się przy użyciu firmy Apple** z listy możliwości. Następnie wybierz pozycję **Kontynuuj**. Zwróć uwagę na **prefiks identyfikatora aplikacji (identyfikator zespołu)** z tego kroku. będzie on potrzebny później.
![Konfigurowanie nowego identyfikatora aplikacji w portalu dla deweloperów firmy Apple](media/configure-authentication-provider-apple/apple-configure-app.jpg)
5. Przejrzyj informacje o rejestracji aplikacji i wybierz pozycję **zarejestruj**.
6. Na karcie **identyfikatory** wybierz przycisk **(+)** .
![Tworzenie nowego identyfikatora usługi w portalu dla deweloperów firmy Apple](media/configure-authentication-provider-apple/apple-new-app.jpg) 
7. Na stronie **Zarejestruj nowy identyfikator** wybierz pozycję **usługi identyfikatory** i wybierz pozycję **Kontynuuj**.
![Rejestrowanie nowego identyfikatora usługi w portalu dla deweloperów firmy Apple](media/configure-authentication-provider-apple/apple-register-service.jpg)
8. Na stronie **Rejestrowanie identyfikatora usług** Podaj opis i identyfikator. Opis jest pokazywany użytkownikowi na ekranie wyrażania zgody. Identyfikator będzie IDENTYFIKATORem klienta używanym podczas konfigurowania dostawcy firmy Apple w usłudze App Service. Następnie wybierz pozycję **Konfiguruj**.
![Podanie opisu i identyfikatora](media/configure-authentication-provider-apple/apple-configure-service-1.jpg)
9. W oknie podręcznym ustaw identyfikator aplikacji głównej na utworzony wcześniej identyfikator aplikacji. Określ domenę aplikacji w sekcji domena. W polu adres URL zwrotu Użyj adresu URL `<app-url>/.auth/login/apple/callback` . Na przykład `https://contoso.azurewebsites.net/.auth/login/apple/callback`. Następnie wybierz pozycję **Dodaj** i **Zapisz**.
![Określanie domeny i zwrotny adres URL dla rejestracji](media/configure-authentication-provider-apple/apple-configure-service-2.jpg)
10. Przejrzyj informacje o rejestracji usługi i wybierz pozycję **Zapisz**.

## <a name="generate-the-client-secret"></a><a name="generateClientSecret"> </a>Generuj klucz tajny klienta
Firma Apple wymaga, aby deweloperzy aplikacji mogli utworzyć i podpisać token JWT jako wartość klucza tajnego klienta. Aby wygenerować ten klucz tajny, najpierw Wygeneruj i Pobierz klucz prywatny krzywej eliptyczna z portalu dla deweloperów firmy Apple. Następnie użyj tego klucza do [podpisania tokenu JWT](#sign-the-client-secret-jwt) z [określonym ładunkiem](#structure-the-client-secret-jwt).

### <a name="create-and-download-the-private-key"></a>Utwórz i Pobierz klucz prywatny
1. Na karcie **klucze** w portalu dla deweloperów firmy Apple wybierz pozycję **Utwórz klucz** lub wybierz przycisk **(+)** .
2. Na stronie **Zarejestruj nowy klucz** Podaj nazwę klucza, zaznacz pole wyboru obok pozycji **Zaloguj się przy użyciu firmy Apple** i wybierz pozycję **Konfiguruj**.
3. Na stronie **Konfigurowanie klucza** Podłącz klucz do utworzonego wcześniej identyfikatora aplikacji głównej, a następnie wybierz pozycję **Zapisz**.
4. Zakończ tworzenie klucza, potwierdzając informacje i wybierając pozycję **Kontynuuj** , a następnie przeglądając informacje i wybierając pozycję **zarejestruj**.
5. Na stronie **Pobieranie klucza** Pobierz klucz. Zostanie on pobrany jako `.p8` plik (PKCS # 8) — zostanie użyta zawartość pliku do podpisania JWT klucza tajnego klienta.

### <a name="structure-the-client-secret-jwt"></a>Tworzenie struktury JWT klucza tajnego klienta
Firma Apple wymaga, aby klucz tajny klienta był kodowaniem Base64 tokenu JWT. Zdekodowany token JWT powinien mieć strukturę o strukturze podobnej do tego przykładu:
```json
{
  "alg": "ES256",
  "kid": "URKEYID001",
}.{
  "sub": "com.yourcompany.app1",
  "nbf": 1560203207,
  "exp": 1560289607,
  "iss": "ABC123DEFG",
  "aud": "https://appleid.apple.com"
}.[Signature]
```
- **Sub**: identyfikator klienta firmy Apple (również identyfikator usługi)
- **ISS**: Identyfikator zespołu deweloperów firmy Apple
- **AUD**: Firma Apple otrzymuje token, więc są odbiorcami
- **EXP**: nie więcej niż sześć miesięcy po **NBF**

Wersja powyższego zakodowanego w formacie Base64 jest następująca: ```eyJhbGciOiJFUzI1NiIsImtpZCI6IlVSS0VZSUQwMDEifQ.eyJzdWIiOiJjb20ueW91cmNvbXBhbnkuYXBwMSIsIm5iZiI6MTU2MDIwMzIwNywiZXhwIjoxNTYwMjg5NjA3LCJpc3MiOiJBQkMxMjNERUZHIiwiYXVkIjoiaHR0cHM6Ly9hcHBsZWlkLmFwcGxlLmNvbSJ9.ABSXELWuTbgqfrIUz7bLi6nXvkXAz5O8vt0jB2dSHTQTib1x1DSP4__4UrlKI-pdzNg1sgeocolPNTmDKazO8-BHAZCsdeeTNlgFEzBytIpMKFfVEQbEtGRkam5IeclUK7S9oOva4EK4jV4VmgDrr-LGWWO3TaAxAvy3_ZoKohvFFkVG```

_Uwaga: Firma Apple nie akceptuje klucza tajnego klienta JWTs z datą wygaśnięcia późniejszą niż sześć miesięcy od daty utworzenia (lub NBF). Oznacza to, że trzeba będzie obrócić klucz tajny klienta, co najmniej co sześć miesięcy._

Więcej informacji o generowaniu i weryfikowaniu tokenów można znaleźć w [dokumentacji dla deweloperów firmy Apple](https://developer.apple.com/documentation/sign_in_with_apple/generate_and_validate_tokens). 

### <a name="sign-the-client-secret-jwt"></a>Podpisywanie JWT klucza tajnego klienta
Użyjesz `.p8` pobranego wcześniej pliku do podpisania klucza tajnego klienta. Ten plik jest [plikiem PCKS # 8](https://en.wikipedia.org/wiki/PKCS_8) zawierającym prywatny klucz podpisywania w formacie PEM. Istnieje wiele bibliotek, które mogą utworzyć i podpisać token JWT. 

Istnieją różne rodzaje bibliotek typu open source dostępnych w trybie online na potrzeby tworzenia i podpisywania tokenów JWT. Aby uzyskać więcej informacji na temat generowania tokenów JWT, zobacz jwt.io. Na przykład jeden ze sposobów generowania klucza tajnego klienta polega na zaimportowaniu [pakietu NuGet Microsoft. IdentityModel. Tokens](https://www.nuget.org/packages/Microsoft.IdentityModel.Tokens/) i uruchomieniu niewielkiej ilości kodu w języku C# pokazanego poniżej.

```csharp
using Microsoft.IdentityModel.Tokens;

public static string GetAppleClientSecret(string teamId, string clientId, string keyId, string p8key)
{
    string audience = "https://appleid.apple.com";

    string issuer = teamId;
    string subject = clientId;
    string kid = keyId;

    IList<Claim> claims = new List<Claim> {
        new Claim ("sub", subject)
    };

    CngKey cngKey = CngKey.Import(Convert.FromBase64String(p8key), CngKeyBlobFormat.Pkcs8PrivateBlob);

    SigningCredentials signingCred = new SigningCredentials(
        new ECDsaSecurityKey(new ECDsaCng(cngKey)),
        SecurityAlgorithms.EcdsaSha256
    );

    JwtSecurityToken token = new JwtSecurityToken(
        issuer,
        audience,
        claims,
        DateTime.Now,
        DateTime.Now.AddDays(180),
        signingCred
    );
    token.Header.Add("kid", kid);
    token.Header.Remove("typ");

    JwtSecurityTokenHandler tokenHandler = new JwtSecurityTokenHandler();

    return tokenHandler.WriteToken(token);
}
```
- **użytkownika**: Identyfikator zespołu deweloperów firmy Apple
- **clientId**: identyfikator klienta firmy Apple (również identyfikator usługi)
- **p8key**: klucz formatu PEM — możesz uzyskać klucz, otwierając `.p8` plik w edytorze tekstu i kopiując wszystko między `-----BEGIN PRIVATE KEY-----` i `-----END PRIVATE KEY-----` bez podziałów wierszy.
- **keyId**: Identyfikator pobranego klucza

Zwróconym tokenem jest wartość klucza tajnego klienta, która będzie używana do konfigurowania dostawcy firmy Apple.

> [!IMPORTANT]
> Klucz tajny klienta jest ważnym poświadczeniem zabezpieczeń. Nie należy udostępniać tego klucza tajnego nikomu ani rozpowszechniać go w aplikacji klienckiej.
>

Dodaj wpis tajny klienta jako [Ustawienia aplikacji](./configure-common.md#configure-app-settings) dla aplikacji przy użyciu wybranej nazwy ustawienia. Zanotuj tę nazwę jako nowszą.

## <a name="add-provider-information-to-your-application"></a><a name="configure"> </a>Dodawanie informacji o dostawcy do aplikacji

> [!NOTE]
> Wymagana konfiguracja ma nowy format interfejsu API, który jest obecnie obsługiwany tylko przez [konfigurację opartą na plikach (wersja zapoznawcza)](.\app-service-authentication-how-to.md#config-file). Należy postępować zgodnie z poniższymi krokami, używając takiego pliku.

Ta sekcja przeprowadzi Cię przez proces aktualizowania konfiguracji w celu uwzględnienia nowej dostawcy tożsamości. Poniżej przedstawiono przykładową konfigurację.

1. W `identityProviders` obiekcie Dodaj `apple` obiekt, jeśli taki jeszcze nie istnieje.
2. Przypisz obiekt do tego klucza z `registration` obiektem w tym obiekcie i opcjonalnie `login` obiekt:
    
    ```json
    "apple" : {
       "registration" : {
            "clientId": "<client id>",
            "clientSecretSettingName": "APP_SETTING_CONTAINING_APPLE_CLIENT_SECRET" 
        },
       "login": {
             "scopes": []
       }
    }
    ```
    a. W obrębie `registration` obiektu ustaw wartość na `clientId` Identyfikator klienta, który został zebrany.
    
    b. W `registration` obiekcie, ustaw `clientSecretSettingName` na nazwę ustawienia aplikacji, w którym zapisano klucz tajny klienta.
    
    c. W `login` obiekcie można ustawić `scopes` tablicę w taki sposób, aby zawierała listę zakresów używanych podczas uwierzytelniania za pomocą firmy Apple, np. "name" i "poczta e-mail". W przypadku skonfigurowania zakresów zostaną one jawnie zażądane na ekranie zgody, gdy użytkownicy logują się po raz pierwszy.

Po ustawieniu tej konfiguracji możesz użyć swojego dostawcy firmy Apple do uwierzytelniania w aplikacji.

Kompletna konfiguracja może wyglądać podobnie do poniższego przykładu (gdzie ustawienie APPLE_GENERATED_CLIENT_SECRET wskazuje na ustawienie aplikacji zawierające wygenerowany token JWT):

```json
{
    "platform": {
        "enabled": true
    },
    "globalValidation": {
        "redirectToProvider": "apple",
        "unauthenticatedClientAction": "RedirectToLoginPage"
    },
    "identityProviders": {
        "apple": {
            "registration": {
                "clientId": "com.contoso.example.client",
                "clientSecretSettingName": "APPLE_GENERATED_CLIENT_SECRET"
            },
            "login": {
                "scopes": []
            }
        }
    },
    "login": {
        "tokenStore": {
            "enabled": true
        }
    }     
}
```

## <a name="next-steps"></a><a name="related-content"> </a>Następne kroki

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]
