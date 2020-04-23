---
title: Uzyskaj token, aby wywołać internetowy interfejs API (aplikacje mobilne) | Azure
titleSuffix: Microsoft identity platform
description: Dowiedz się, jak utworzyć aplikację mobilną, która wywołuje internetowe interfejsy API. (Pobierz token dla aplikacji.)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviewer: brandwe
ms.custom: aaddev
ms.openlocfilehash: a77e6c9086a745804c23f431f633d530e2655f16
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81868891"
---
# <a name="get-a-token-for-a-mobile-app-that-calls-web-apis"></a>Pobierz token dla aplikacji mobilnej, która wywołuje internetowe interfejsy API

Zanim aplikacja będzie mogła wywoływać chronione interfejsy API sieci Web, potrzebuje tokenu dostępu. W tym artykule otrzymasz od użytkownika proces, aby uzyskać token przy użyciu biblioteki uwierzytelniania firmy Microsoft (MSAL).

## <a name="define-a-scope"></a>Definiowanie zakresu

Podczas żądania tokenu, należy zdefiniować zakres. Zakres określa, jakie dane aplikacja może uzyskać dostęp.

Najprostszym sposobem zdefiniowania zakresu jest połączenie żądanego `App ID URI` internetowego `.default`interfejsu API z zakresem. Ta definicja informuje platformę tożsamości firmy Microsoft, że aplikacja wymaga wszystkich zakresów, które są ustawione w portalu.

### <a name="android"></a>Android
```Java
String[] SCOPES = {"https://graph.microsoft.com/.default"};
```

### <a name="ios"></a>iOS
```swift
let scopes = ["https://graph.microsoft.com/.default"]
```

### <a name="xamarin"></a>Xamarin
```csharp
var scopes = new [] {"https://graph.microsoft.com/.default"};
```

## <a name="get-tokens"></a>Zdobądź żetony

### <a name="acquire-tokens-via-msal"></a>Pozyskiwanie tokenów za pośrednictwem protokołu MSAL

MSAL umożliwia aplikacjom pobieranie tokenów w trybie cichym i interaktywnym. Podczas `AcquireTokenSilent()` wywoływania `AcquireTokenInteractive()`lub , MSAL zwraca token dostępu dla żądanych zakresów. Prawidłowy wzorzec jest, aby ciche żądanie, a następnie wrócić do żądania interaktywnego.

#### <a name="android"></a>Android

```Java
String[] SCOPES = {"https://graph.microsoft.com/.default"};
PublicClientApplication sampleApp = new PublicClientApplication(
                    this.getApplicationContext(),
                    R.raw.auth_config);

// Check if there are any accounts we can sign in silently.
// Result is in the silent callback (success or error).
sampleApp.getAccounts(new PublicClientApplication.AccountsLoadedCallback() {
    @Override
    public void onAccountsLoaded(final List<IAccount> accounts) {

        if (accounts.isEmpty() && accounts.size() == 1) {
            // TODO: Create a silent callback to catch successful or failed request.
            sampleApp.acquireTokenSilentAsync(SCOPES, accounts.get(0), getAuthSilentCallback());
        } else {
            /* No accounts or > 1 account. */
        }
    }
});

[...]

// No accounts found. Interactively request a token.
// TODO: Create an interactive callback to catch successful or failed requests.
sampleApp.acquireToken(getActivity(), SCOPES, getAuthInteractiveCallback());
```

#### <a name="ios"></a>iOS

Najpierw spróbuj po cichu nabyć token:

```objc

NSArray *scopes = @[@"https://graph.microsoft.com/.default"];
NSString *accountIdentifier = @"my.account.id";

MSALAccount *account = [application accountForIdentifier:accountIdentifier error:nil];

MSALSilentTokenParameters *silentParams = [[MSALSilentTokenParameters alloc] initWithScopes:scopes account:account];
[application acquireTokenSilentWithParameters:silentParams completionBlock:^(MSALResult *result, NSError *error) {

    if (!error)
    {
        // You'll want to get the account identifier to retrieve and reuse the account
        // for later acquireToken calls
        NSString *accountIdentifier = result.account.identifier;

        // Access token to call the web API
        NSString *accessToken = result.accessToken;
    }

    // Check the error
    if (error && [error.domain isEqual:MSALErrorDomain] && error.code == MSALErrorInteractionRequired)
    {
        // Interactive auth will be required, call acquireTokenWithParameters:error:
        return;
    }
}];
```

```swift

let scopes = ["https://graph.microsoft.com/.default"]
let accountIdentifier = "my.account.id"

guard let account = try? application.account(forIdentifier: accountIdentifier) else { return }
let silentParameters = MSALSilentTokenParameters(scopes: scopes, account: account)
application.acquireTokenSilent(with: silentParameters) { (result, error) in

    guard let authResult = result, error == nil else {

    let nsError = error! as NSError

    if (nsError.domain == MSALErrorDomain &&
        nsError.code == MSALError.interactionRequired.rawValue) {

            // Interactive auth will be required, call acquireToken()
            return
         }
         return
     }

    // You'll want to get the account identifier to retrieve and reuse the account
    // for later acquireToken calls
    let accountIdentifier = authResult.account.identifier

    // Access token to call the web API
    let accessToken = authResult.accessToken
}
```

Jeśli msal `MSALErrorInteractionRequired`zwraca , a następnie spróbuj nabycia tokenów interaktywnie:

```objc
UIViewController *viewController = ...; // Pass a reference to the view controller that should be used when getting a token interactively
MSALWebviewParameters *webParameters = [[MSALWebviewParameters alloc] initWithAuthPresentationViewController:viewController];
MSALInteractiveTokenParameters *interactiveParams = [[MSALInteractiveTokenParameters alloc] initWithScopes:scopes webviewParameters:webParameters];
[application acquireTokenWithParameters:interactiveParams completionBlock:^(MSALResult *result, NSError *error) {
    if (!error)
    {
        // You'll want to get the account identifier to retrieve and reuse the account
        // for later acquireToken calls
        NSString *accountIdentifier = result.account.identifier;

        NSString *accessToken = result.accessToken;
    }
}];
```

```swift
let viewController = ... // Pass a reference to the view controller that should be used when getting a token interactively
let webviewParameters = MSALWebviewParameters(authPresentationViewController: viewController)
let interactiveParameters = MSALInteractiveTokenParameters(scopes: scopes, webviewParameters: webviewParameters)
application.acquireToken(with: interactiveParameters, completionBlock: { (result, error) in

    guard let authResult = result, error == nil else {
        print(error!.localizedDescription)
        return
    }

    // Get access token from result
    let accessToken = authResult.accessToken
})
```

Msal dla systemu iOS i macOS obsługuje różne modyfikatory, aby uzyskać token interaktywnie lub cicho:
* [Typowe parametry uzyskiwania tokenu](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALTokenParameters.html#/Configuration%20parameters)
* [Parametry uzyskiwania tokenu interaktywnego](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALInteractiveTokenParameters.html#/Configuring%20MSALInteractiveTokenParameters)
* [Parametry uzyskiwania cichego tokenu](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALSilentTokenParameters.html)

#### <a name="xamarin"></a>Xamarin

W poniższym przykładzie przedstawiono minimalny kod, aby uzyskać token interaktywnie. W przykładzie użyto programu Microsoft Graph do odczytu profilu użytkownika.

```csharp
string[] scopes = new string[] {"user.read"};
var app = PublicClientApplicationBuilder.Create(clientId).Build();
var accounts = await app.GetAccountsAsync();
AuthenticationResult result;
try
{
 result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
             .ExecuteAsync();
}
catch(MsalUiRequiredException)
{
 result = await app.AcquireTokenInteractive(scopes)
             .ExecuteAsync();
}
```

#### <a name="mandatory-parameters-in-msalnet"></a>Parametry obowiązkowe w MSAL.NET

`AcquireTokenInteractive`ma tylko jeden parametr `scopes`obowiązkowy: . Parametr `scopes` wylicza ciągi, które definiują zakresy, dla których wymagany jest token. Jeśli token jest dla programu Microsoft Graph, można znaleźć wymagane zakresy w odwołaniu do interfejsu API każdego interfejsu API programu Microsoft Graph. W odwołaniu przejdź do sekcji "Uprawnienia".

Na przykład, aby [wyświetlić listę kontaktów użytkownika,](https://developer.microsoft.com/graph/docs/api-reference/v1.0/api/user_list_contacts)należy użyć zakresu "User.Read", "Contacts.Read". Aby uzyskać więcej informacji, zobacz [Odwołanie do uprawnień programu Microsoft Graph](https://developer.microsoft.com/graph/docs/concepts/permissions_reference).

W systemie Android można określić aktywność nadrzędną podczas tworzenia aplikacji za pomocą programu `PublicClientApplicationBuilder`. Jeśli nie określisz działania nadrzędnego w tym czasie, później `.WithParentActivityOrWindow` można określić go za pomocą jak w poniższej sekcji. Jeśli określisz działanie nadrzędne, token powraca do tego działania nadrzędnego po interakcji. Jeśli go nie określisz, `.ExecuteAsync()` wywołanie zgłasza wyjątek.

#### <a name="specific-optional-parameters-in-msalnet"></a>Specyficzne parametry opcjonalne w MSAL.NET

W poniższych sekcjach wyjaśniono parametry opcjonalne w MSAL.NET.

##### <a name="withprompt"></a>ZPrompt

Parametr `WithPrompt()` steruje interaktywnością z użytkownikiem, określając monit.

<img src="https://user-images.githubusercontent.com/13203188/53438042-3fb85700-39ff-11e9-9a9e-1ff9874197b3.png" width="25%" />

Klasa definiuje następujące stałe:

- `SelectAccount`wymusza, aby usługa tokenu zabezpieczającego (STS) zaprezentowała okno dialogowe wyboru konta. Okno dialogowe zawiera konta, dla których użytkownik ma sesję. Tej opcji można użyć, gdy użytkownik ma wybierać spośród różnych tożsamości. Ta opcja umożliwia msal do wysyłania `prompt=select_account` do dostawcy tożsamości.

    Stała `SelectAccount` jest domyślna i skutecznie zapewnia najlepsze możliwe środowisko na podstawie dostępnych informacji. Dostępne informacje mogą obejmować konto, obecność sesji dla użytkownika i tak dalej. Nie zmieniaj tego ustawienia domyślnego, chyba że masz ku temu dobry powód.
- `Consent`umożliwia monitowanie użytkownika o zgodę, nawet jeśli zgoda została udzielona wcześniej. W takim przypadku MSAL wysyła `prompt=consent` do dostawcy tożsamości.

    Można użyć stałej `Consent` w aplikacjach z naciskiem na zabezpieczenia, w których zarządzanie organizacją wymaga od użytkowników, aby mogli zobaczyć okno dialogowe zgody za każdym razem, gdy korzystają z aplikacji.
- `ForceLogin`umożliwia usłudze monitowanie użytkownika o poświadczenia, nawet jeśli monit nie jest potrzebny.

    Ta opcja może być przydatna, jeśli pobieranie tokenu nie powiedzie się i chcesz umożliwić użytkownikowi ponowne zalogowanie się. W takim przypadku MSAL wysyła `prompt=login` do dostawcy tożsamości. Można użyć tej opcji w aplikacjach z uwzględnieniem zabezpieczeń, gdzie zarządzanie organizacją wymaga od użytkownika, aby zalogować się za każdym razem, gdy uzyskują dostęp do określonych części aplikacji.
- `Never`jest tylko dla .NET 4.5 i Windows Runtime (WinRT). Ta stała nie monituje użytkownika, ale spróbuje użyć pliku cookie, który jest przechowywany w ukrytym osadzonym widoku sieci Web. Aby uzyskać więcej informacji, zobacz [Korzystanie z przeglądarek internetowych z MSAL.NET](https://docs.microsoft.com/azure/active-directory/develop/msal-net-web-browsers).

    Jeśli ta opcja `AcquireTokenInteractive` nie powiedzie się, a następnie zgłasza wyjątek, aby powiadomić, że interakcja interfejsu użytkownika jest potrzebna. Następnie należy użyć `Prompt` innego parametru.
- `NoPrompt`nie wysyła monitu do dostawcy tożsamości.

    Ta opcja jest przydatna tylko w przypadku zasad profilu edycji w usłudze Azure Active Directory B2C. Aby uzyskać więcej informacji, zobacz [B2C specyfiki](https://aka.ms/msal-net-b2c-specificities).

##### <a name="withextrascopetoconsent"></a>ZExtraScopeToConsent

Użyj `WithExtraScopeToConsent` modyfikatora w zaawansowanym scenariuszu, w którym chcesz, aby użytkownik udzielił zgody z góry na kilka zasobów. Tego modyfikatora można użyć, gdy nie chcesz używać zgody przyrostowej, która jest zwykle używana z MSAL.NET lub platformą tożsamości firmy Microsoft 2.0. Aby uzyskać więcej informacji, zobacz [Wyrażanie zgody użytkownika z góry na kilka zasobów.](scenario-desktop-production.md#have-the-user-consent-upfront-for-several-resources)

Oto przykład kodu:

```csharp
var result = await app.AcquireTokenInteractive(scopesForCustomerApi)
                     .WithExtraScopeToConsent(scopesForVendorApi)
                     .ExecuteAsync();
```

##### <a name="other-optional-parameters"></a>Inne parametry opcjonalne

Aby dowiedzieć się więcej `AcquireTokenInteractive`o innych parametrach opcjonalnych dla , zobacz [dokumentację referencyjną dla AcquireTokenInteractiveParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokeninteractiveparameterbuilder?view=azure-dotnet-preview#methods).

### <a name="acquire-tokens-via-the-protocol"></a>Pozyskiwanie tokenów za pośrednictwem protokołu

Nie zaleca się bezpośrednio przy użyciu protokołu, aby uzyskać tokeny. Jeśli to zrobisz, aplikacja nie będzie obsługiwać niektórych scenariuszy, które obejmują logowanie jednokrotne (logowanie jednokrotne), zarządzanie urządzeniami i dostęp warunkowy.

Gdy używasz protokołu do pobieranie tokenów dla aplikacji mobilnych, należy wykonać dwa żądania:

* Pobierz kod autoryzacji.
* Wymiana kodu dla tokenu.

#### <a name="get-an-authorization-code"></a>Uzyskaj kod autoryzacji

```
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=<CLIENT_ID>
&response_type=code
&redirect_uri=<ENCODED_REDIRECT_URI>
&response_mode=query
&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2F.default
&state=12345
```

#### <a name="get-access-and-refresh-the-token"></a>Uzyskaj dostęp i odśwież token

```HTTP
POST /{tenant}/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=<CLIENT_ID>
&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&code=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq3n8b2JRLk4OxVXr...
&redirect_uri=<ENCODED_REDIRECT_URI>
&grant_type=authorization_code
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Wywoływanie internetowego interfejsu API](scenario-mobile-call-api.md)
