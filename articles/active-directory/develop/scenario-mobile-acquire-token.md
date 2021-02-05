---
title: Uzyskaj token do wywoływania internetowego interfejsu API (aplikacje mobilne) | Azure
titleSuffix: Microsoft identity platform
description: Dowiedz się, jak utworzyć aplikację mobilną, która wywołuje interfejsy API sieci Web. (Pobierz token dla aplikacji).
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
ms.openlocfilehash: 6e6eda3d711710ea7450165ab02d7a260067bfcb
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/05/2021
ms.locfileid: "99582557"
---
# <a name="get-a-token-for-a-mobile-app-that-calls-web-apis"></a>Uzyskaj token dla aplikacji mobilnej, która wywołuje interfejsy API sieci Web

Aby aplikacja mogła wywoływać chronione interfejsy API sieci Web, musi mieć token dostępu. W tym artykule omówiono proces uzyskiwania tokenu przy użyciu biblioteki uwierzytelniania firmy Microsoft (MSAL).

## <a name="define-a-scope"></a>Zdefiniuj zakres

Podczas żądania tokenu Zdefiniuj zakres. Zakres określa, jakie dane może uzyskać dostęp do aplikacji.

Najprostszym sposobem zdefiniowania zakresu jest połączenie żądanych interfejsów API sieci Web `App ID URI` z zakresem `.default` . Ta definicja informuje platformę tożsamości firmy Microsoft, że aplikacja wymaga wszystkich zakresów ustawionych w portalu.

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

## <a name="get-tokens"></a>Pobierz tokeny

### <a name="acquire-tokens-via-msal"></a>Uzyskiwanie tokenów za pośrednictwem MSAL

MSAL umożliwia aplikacjom uzyskiwanie tokenów dyskretnie i interaktywnie. Po wywołaniu `AcquireTokenSilent()` lub `AcquireTokenInteractive()` MSAL zwraca token dostępu dla żądanych zakresów. Prawidłowym wzorcem jest wykonanie żądania dyskretnego, a następnie powrót do interaktywnego żądania.

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

Najpierw spróbuj uzyskać token w trybie dyskretnym:

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

Jeśli MSAL zwraca `MSALErrorInteractionRequired` , a następnie spróbuj uzyskać tokeny interaktywnie:

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

MSAL dla systemów iOS i macOS obsługuje różne modyfikatory, aby uzyskać token interaktywnie lub dyskretnie:
* [Typowe parametry uzyskiwania tokenu](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALTokenParameters.html#/Configuration%20parameters)
* [Parametry pobierania tokenu interakcyjnego](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALInteractiveTokenParameters.html#/Configuring%20MSALInteractiveTokenParameters)
* [Parametry pobierania tokenu dyskretnego](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALSilentTokenParameters.html)

#### <a name="xamarin"></a>Xamarin

Poniższy przykład pokazuje minimalny kod umożliwiający interaktywny dostęp do tokenu. W przykładzie używa się Microsoft Graph do odczytywania profilu użytkownika.

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

#### <a name="mandatory-parameters-in-msalnet"></a>Obowiązkowe parametry w MSAL.NET

`AcquireTokenInteractive` ma tylko jeden obowiązkowy parametr: `scopes` . `scopes`Parametr wylicza ciągi definiujące zakresy, dla których wymagany jest token. Jeśli token jest przeznaczony dla Microsoft Graph, można znaleźć wymagane zakresy w dokumentacji interfejsu API dla każdego Microsoft Graph interfejsu API. W odwołaniu przejdź do sekcji "uprawnienia".

Na przykład aby [wyświetlić listę kontaktów użytkownika](/graph/api/user-list-contacts), należy użyć zakresu "User. Read", "Contacts. Read". Aby uzyskać więcej informacji, zobacz [Microsoft Graph informacje o uprawnieniach](/graph/permissions-reference).

W systemie Android podczas tworzenia aplikacji za pomocą programu można określić działanie nadrzędne `PublicClientApplicationBuilder` . Jeśli nie określisz działania nadrzędnego w tym czasie, możesz je określić za pomocą `.WithParentActivityOrWindow` , jak w poniższej sekcji. W przypadku określenia działania nadrzędnego token wraca do tego działania nadrzędnego po interakcji. Jeśli nie zostanie on określony, `.ExecuteAsync()` wywołanie zgłosi wyjątek.

#### <a name="specific-optional-parameters-in-msalnet"></a>Określone parametry opcjonalne w MSAL.NET

W poniższych sekcjach wyjaśniono parametry opcjonalne w MSAL.NET.

##### <a name="withprompt"></a>WithPrompt

`WithPrompt()`Parametr steruje interakcją z użytkownikiem przez określenie monitu.

![Obraz przedstawiający pola w strukturze monitu. Te wartości stałe kontrolują interakcję z użytkownikiem przez zdefiniowanie typu monitu wyświetlanego przez parametr WithPrompt ().](https://user-images.githubusercontent.com/13203188/53438042-3fb85700-39ff-11e9-9a9e-1ff9874197b3.png)

Klasa definiuje następujące stałe:

- `SelectAccount` wymusza, aby usługa tokenu zabezpieczającego zaprezentować okno dialogowe wyboru konta. Okno dialogowe zawiera konta, dla których użytkownik ma sesję. Możesz użyć tej opcji, jeśli chcesz zezwolić użytkownikowi na wybór różnych tożsamości. Ta opcja umożliwia MSAL wysyłanie `prompt=select_account` do dostawcy tożsamości.

    `SelectAccount`Stała jest wartością domyślną i efektywnie zapewnia najlepsze możliwe środowisko na podstawie dostępnych informacji. Dostępne informacje mogą obejmować konto, obecność sesji dla użytkownika i tak dalej. Nie zmieniaj tego ustawienia domyślnego, chyba że masz dobry powód, aby to zrobić.
- `Consent` pozwala na monitowanie użytkownika o zgodę nawet w przypadku udzielenia zgody. W takim przypadku MSAL wysyła `prompt=consent` do dostawcy tożsamości.

    Możesz chcieć użyć `Consent` stałej w aplikacjach ukierunkowanych na zabezpieczenia, gdy organizacja organizacji wymaga, aby użytkownicy widzieli okno dialogowe zgody za każdym razem, gdy korzystają z aplikacji.
- `ForceLogin` umożliwia usłudze monitowanie użytkownika o podanie poświadczeń, nawet jeśli monit nie jest wymagany.

    Ta opcja może być przydatna w przypadku niepowodzenia pozyskiwania tokenu i poinformowania użytkownika o konieczności ponownego zalogowania. W takim przypadku MSAL wysyła `prompt=login` do dostawcy tożsamości. Warto użyć tej opcji w aplikacjach ukierunkowanych na zabezpieczenia, w których administrator organizacji wymaga, aby użytkownik mógł się zalogować przy każdej próbie dostępu do określonych części aplikacji.
- `Never` dotyczy tylko programu .NET 4,5 i środowisko wykonawcze systemu Windows (WinRT). Ta stała nie monituje użytkownika, ale podejmie próbę użycia pliku cookie przechowywanego w ukrytym osadzonym widoku sieci Web. Aby uzyskać więcej informacji, zobacz [Korzystanie z przeglądarek sieci Web w programie MSAL.NET](./msal-net-web-browsers.md).

    Jeśli ta opcja zakończy się niepowodzeniem, program `AcquireTokenInteractive` zgłosi wyjątek, aby powiadomić o konieczności interakcji z interfejsem użytkownika. Następnie użyj innego `Prompt` parametru.
- `NoPrompt` nie wysyła monitu do dostawcy tożsamości.

    Ta opcja jest przydatna tylko w przypadku zasad edytowanie profilu w programie Azure Active Directory B2C. Aby uzyskać więcej informacji, zobacz [szczegóły dotyczące B2C](https://aka.ms/msal-net-b2c-specificities).

##### <a name="withextrascopetoconsent"></a>WithExtraScopeToConsent

Użyj `WithExtraScopeToConsent` modyfikatora w zaawansowanym scenariuszu, w którym użytkownik powinien zapewnić z góry zgodę na kilka zasobów. Tego modyfikatora można użyć, jeśli nie chcesz używać wyrażenia przyrostowego, które jest zwykle używane z MSAL.NET lub platformą tożsamości firmy Microsoft. Aby uzyskać więcej informacji, zobacz artykuł czy [użytkownik ma zgodę na dostęp do kilku zasobów](scenario-desktop-production.md#have-the-user-consent-upfront-for-several-resources).

Oto przykład kodu:

```csharp
var result = await app.AcquireTokenInteractive(scopesForCustomerApi)
                     .WithExtraScopeToConsent(scopesForVendorApi)
                     .ExecuteAsync();
```

##### <a name="other-optional-parameters"></a>Inne parametry opcjonalne

Aby dowiedzieć się więcej na temat innych parametrów opcjonalnych dla programu `AcquireTokenInteractive` , zobacz [dokumentację referencyjną AcquireTokenInteractiveParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokeninteractiveparameterbuilder#methods).

### <a name="acquire-tokens-via-the-protocol"></a>Uzyskiwanie tokenów za pośrednictwem protokołu

Nie zalecamy bezpośrednio korzystania z protokołu w celu uzyskania tokenów. Jeśli to zrobisz, aplikacja nie będzie obsługiwać niektórych scenariuszy obejmujących Logowanie jednokrotne (SSO), zarządzanie urządzeniami i dostęp warunkowy.

W przypadku korzystania z protokołu do uzyskiwania tokenów dla aplikacji mobilnych wykonaj dwa żądania:

* Pobierz kod autoryzacji.
* Wymienia kod dla tokenu.

#### <a name="get-an-authorization-code"></a>Pobieranie kodu autoryzacji

```
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=<CLIENT_ID>
&response_type=code
&redirect_uri=<ENCODED_REDIRECT_URI>
&response_mode=query
&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2F.default
&state=12345
```

#### <a name="get-access-and-refresh-the-token"></a>Uzyskiwanie dostępu i odświeżanie tokenu

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

Przejdź do następnego artykułu w tym scenariuszu, [wywołując interfejs API sieci Web](scenario-mobile-call-api.md).