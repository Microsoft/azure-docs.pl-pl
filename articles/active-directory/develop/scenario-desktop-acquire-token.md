---
title: Uzyskaj token do wywoływania internetowego interfejsu API (aplikacja klasyczna) | Azure
titleSuffix: Microsoft identity platform
description: Dowiedz się, jak utworzyć aplikację klasyczną wywołującą interfejsy API sieci Web w celu uzyskania tokenu dla aplikacji
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/18/2020
ms.author: jmprieur
ms.custom: aaddev, devx-track-python
ms.openlocfilehash: 26f0c18fee8fe56a9bc0fa163ef59dfe0977cad5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91825333"
---
# <a name="desktop-app-that-calls-web-apis-acquire-a-token"></a>Aplikacja klasyczna, która wywołuje interfejsy API sieci Web: uzyskiwanie tokenu

Po skompilowaniu wystąpienia publicznej aplikacji klienckiej użyjesz go, aby uzyskać token, który będzie używany do wywoływania internetowego interfejsu API.

## <a name="recommended-pattern"></a>Zalecany wzorzec

Internetowy interfejs API jest definiowany przez `scopes` . Bez względu na to, jakie środowisko zostało wprowadzone w aplikacji, wzorzec do użycia to:

- Systematyczne podejmowanie prób uzyskania tokenu z pamięci podręcznej tokenów przez wywołanie metody `AcquireTokenSilent` .
- Jeśli to wywołanie zakończy się niepowodzeniem, użyj przepływu, którego `AcquireToken` chcesz użyć, które jest reprezentowane tutaj przez `AcquireTokenXX` .

# <a name="net"></a>[.NET](#tab/dotnet)

### <a name="in-msalnet"></a>W MSAL.NET

```csharp
AuthenticationResult result;
var accounts = await app.GetAccountsAsync();
IAccount account = ChooseAccount(accounts); // for instance accounts.FirstOrDefault
                                            // if the app manages is at most one account
try
{
 result = await app.AcquireTokenSilent(scopes, account)
                   .ExecuteAsync();
}
catch(MsalUiRequiredException ex)
{
  result = await app.AcquireTokenXX(scopes, account)
                    .WithOptionalParameterXXX(parameter)
                    .ExecuteAsync();
}
```

# <a name="java"></a>[Java](#tab/java)

```java

Set<IAccount> accountsInCache = pca.getAccounts().join();
// Take first account in the cache. In a production application, you would filter
// accountsInCache to get the right account for the user authenticating.
IAccount account = accountsInCache.iterator().next();

IAuthenticationResult result;
try {
    SilentParameters silentParameters =
            SilentParameters
                    .builder(SCOPE, account)
                    .build();

    // try to acquire token silently. This call will fail since the token cache
    // does not have any data for the user you are trying to acquire a token for
    result = pca.acquireTokenSilently(silentParameters).join();
} catch (Exception ex) {
    if (ex.getCause() instanceof MsalException) {

        InteractiveRequestParameters parameters = InteractiveRequestParameters
                .builder(new URI("http://localhost"))
                .scopes(SCOPE)
                .build();

        // Try to acquire a token interactively with system browser. If successful, you should see
        // the token and account information printed out to console
        result = pca.acquireToken(parameters).join();
    } else {
        // Handle other exceptions accordingly
        throw ex;
    }
}
return result;

```

# <a name="python"></a>[Python](#tab/python)

```Python
result = None

# Firstly, check the cache to see if this end user has signed in before
accounts = app.get_accounts(username=config["username"])
if accounts:
    result = app.acquire_token_silent(config["scope"], account=accounts[0])

if not result:
    result = app.acquire_token_by_xxx(scopes=config["scope"])
```

# <a name="macos"></a>[macOS](#tab/macOS)

### <a name="in-msal-for-ios-and-macos"></a>W MSAL dla systemów iOS i macOS

Cel-C:

```objc
MSALAccount *account = [application accountForIdentifier:accountIdentifier error:nil];

MSALSilentTokenParameters *silentParams = [[MSALSilentTokenParameters alloc] initWithScopes:scopes account:account];
[application acquireTokenSilentWithParameters:silentParams completionBlock:^(MSALResult *result, NSError *error) {

    // Check the error
    if (error && [error.domain isEqual:MSALErrorDomain] && error.code == MSALErrorInteractionRequired)
    {
        // Interactive auth will be required, call acquireTokenWithParameters:error:
    }
}];
```
Adres

```swift
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
}
```
---

Poniżej przedstawiono różne sposoby uzyskiwania tokenów w aplikacji klasycznej.

## <a name="acquire-a-token-interactively"></a>Interaktywny pozyskiwanie tokenu

Poniższy przykład pokazuje minimalny kod, aby interaktywnie uzyskać token do odczytywania profilu użytkownika przy użyciu Microsoft Graph.

# <a name="net"></a>[.NET](#tab/dotnet)
### <a name="in-msalnet"></a>W MSAL.NET

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

### <a name="mandatory-parameters"></a>Parametry obowiązkowe

`AcquireTokenInteractive` ma tylko jeden obowiązkowy parametr, ``scopes`` który zawiera Wyliczenie ciągów definiujących zakresy, dla których wymagany jest token. Jeśli token dotyczy Microsoft Graph, wymagane zakresy można znaleźć w dokumentacji interfejsu API poszczególnych Microsoft Graph API w sekcji o nazwie "uprawnienia". Na przykład aby [wyświetlić listę kontaktów użytkownika](/graph/api/user-list-contacts), należy użyć zakresu "User. Read", "Contacts. Read". Aby uzyskać więcej informacji, zobacz [Microsoft Graph informacje o uprawnieniach](/graph/permissions-reference).

W systemie Android należy również określić działanie nadrzędne przy użyciu `.WithParentActivityOrWindow` , jak pokazano, aby token odwracał do tego działania nadrzędnego po interakcji. Jeśli nie zostanie on określony, wyjątek jest zgłaszany podczas wywoływania `.ExecuteAsync()` .

### <a name="specific-optional-parameters-in-msalnet"></a>Określone parametry opcjonalne w MSAL.NET

#### <a name="withparentactivityorwindow"></a>WithParentActivityOrWindow

Interfejs użytkownika jest ważny, ponieważ jest interaktywny. `AcquireTokenInteractive` ma jeden określony opcjonalny parametr, który może określać, dla platform, które go obsługują, nadrzędny interfejs użytkownika. Gdy jest używany w aplikacji klasycznej, `.WithParentActivityOrWindow` ma inny typ, który zależy od platformy. Alternatywnie możesz pominąć opcjonalny parametr okna nadrzędnego, aby utworzyć okno, jeśli nie chcesz kontrolować miejsca, w którym okno dialogowe logowania pojawia się na ekranie. Ma to zastosowanie w przypadku aplikacji, które są oparte na wierszu polecenia, służących do przekazywania wywołań do innej usługi zaplecza i nie są potrzebne żadne okna do interakcji z użytkownikiem. 

```csharp
// net45
WithParentActivityOrWindow(IntPtr windowPtr)
WithParentActivityOrWindow(IWin32Window window)

// Mac
WithParentActivityOrWindow(NSWindow window)

// .Net Standard (this will be on all platforms at runtime, but only on NetStandard at build time)
WithParentActivityOrWindow(object parent).
```

Uwagi

- Na .NET Standard oczekiwano `object` w systemie `Activity` Android, `UIViewController` w systemie iOS, `NSWindow` na komputerach Mac i `IWin32Window` `IntPr` w systemie Windows.
- W systemie Windows należy wywołać `AcquireTokenInteractive` z wątku interfejsu użytkownika, aby przeglądarka osadzona pobiera odpowiedni kontekst synchronizacji interfejsu użytkownika. Nie wywoływanie z wątku interfejsu użytkownika może powodować, że komunikaty nie są prawidłowo i zakleszczenie w scenariuszach z interfejsem użytkownika. Jednym ze sposobów wywoływania bibliotek uwierzytelniania firmy Microsoft (MSALs) z wątku interfejsu użytkownika, jeśli nie jesteś w wątku interfejsu użytkownika, jest już użycie `Dispatcher` w WPF.
- Jeśli używasz WPF, aby uzyskać okno z formantu WPF, możesz użyć `WindowInteropHelper.Handle` klasy. Następnie wywołanie pochodzi z formantu WPF ( `this` ):

  ```csharp
  result = await app.AcquireTokenInteractive(scopes)
                    .WithParentActivityOrWindow(new WindowInteropHelper(this).Handle)
                    .ExecuteAsync();
  ```

#### <a name="withprompt"></a>WithPrompt

`WithPrompt()` służy do kontrolowania interakcji z użytkownikiem przez określenie monitu.

![Obraz przedstawiający pola w strukturze monitu. Te wartości stałe kontrolują interakcję z użytkownikiem przez zdefiniowanie typu monitu wyświetlanego przez metodę WithPrompt ().](https://user-images.githubusercontent.com/13203188/53438042-3fb85700-39ff-11e9-9a9e-1ff9874197b3.png)

Klasa definiuje następujące stałe:

- ``SelectAccount`` wymusza zaprezentowanie przez usługę STS okna dialogowego wyboru konta zawierającego konta, dla których użytkownik ma sesję. Ta opcja jest przydatna, gdy deweloperzy aplikacji chcą zezwolić użytkownikom na wybór różnych tożsamości. Ta opcja umożliwia MSAL wysyłanie ``prompt=select_account`` do dostawcy tożsamości. Ta opcja jest domyślnie zaznaczona. Jest to dobre zadanie zapewniające najlepsze możliwe środowisko na podstawie dostępnych informacji, takich jak konto i obecność sesji dla użytkownika. Nie zmieniaj go, chyba że masz dobry powód do tego celu.
- ``Consent`` umożliwia deweloperowi aplikacji wymuszenie monitowania użytkownika o zgodę, nawet jeśli zgoda została udzielona wcześniej. W takim przypadku MSAL wysyła `prompt=consent` do dostawcy tożsamości. Ta opcja może być używana w niektórych aplikacjach ukierunkowanych na zabezpieczenia, w których organizacja organizacji wymaga, aby użytkownik był prezentowany przy użyciu okna dialogowego wyrażanie zgody za każdym razem, gdy aplikacja jest używana.
- ``ForceLogin`` umożliwia deweloperowi aplikacji wyświetlenie monitu o podanie poświadczeń przez usługę, nawet jeśli ten monit użytkownika może nie być wymagany. Ta opcja może być przydatna, aby umożliwić użytkownikowi ponowne zalogowanie się w przypadku niepowodzenia uzyskiwania tokenu. W takim przypadku MSAL wysyła `prompt=login` do dostawcy tożsamości. Czasami jest używany w aplikacjach ukierunkowanych na zabezpieczenia, w których organizacja organizacji wymaga, aby użytkownik ponownie podlogować się do określonych części aplikacji.
- ``Never`` (dotyczy tylko programu .NET 4,5 i WinRT) nie monituje użytkownika, ale zamiast tego próbuje użyć pliku cookie przechowywanego w ukrytym osadzonym widoku sieci Web. Aby uzyskać więcej informacji, zobacz widoki sieci Web w MSAL.NET. Użycie tej opcji może zakończyć się niepowodzeniem. W takim przypadku `AcquireTokenInteractive` zgłasza wyjątek, aby powiadomić o konieczności interakcji z interfejsem użytkownika. Musisz użyć innego `Prompt` parametru.
- ``NoPrompt`` nie wyśle żadnego monitu do dostawcy tożsamości. Ta opcja jest przydatna tylko w przypadku Azure Active Directory (Azure AD) B2C edytowanie zasad profilu. Aby uzyskać więcej informacji, zobacz [Azure AD B2C szczegóły](https://aka.ms/msal-net-b2c-specificities).

#### <a name="withextrascopetoconsent"></a>WithExtraScopeToConsent

Ten modyfikator jest używany w zaawansowanym scenariuszu, w którym użytkownik powinien wstępnie wyrazić zgodę na kilka zasobów z wyprzedzeniem i nie chce używać powyższej zgody, która zwykle jest używana z MSAL.NET/the Microsoft Identity platform. Aby uzyskać więcej informacji, zobacz artykuł czy [użytkownik ma zgodę na dostęp do kilku zasobów](scenario-desktop-production.md#have-the-user-consent-upfront-for-several-resources).

```csharp
var result = await app.AcquireTokenInteractive(scopesForCustomerApi)
                     .WithExtraScopeToConsent(scopesForVendorApi)
                     .ExecuteAsync();
```

#### <a name="withcustomwebui"></a>WithCustomWebUi

Interfejs użytkownika sieci Web to mechanizm wywoływania przeglądarki. Mechanizm ten może być dedykowanym formantem WebBrowser interfejsu użytkownika lub sposobem delegowania otwierania przeglądarki.
MSAL zapewnia implementacje interfejsu użytkownika sieci Web dla większości platform, ale zdarzają się sytuacje, w których może być konieczne samodzielne hostowanie przeglądarki:

- Platformy, które nie są jawnie objęte usługą MSAL, na przykład Blazor, Unity i mono na komputerach stacjonarnych.
- Chcesz testować aplikację i korzystać z zautomatyzowanej przeglądarki, która może być używana z selenem.
- Przeglądarka i aplikacja z systemem MSAL są w oddzielnych procesach.

##### <a name="at-a-glance"></a>Na pierwszy rzut oka

Aby to osiągnąć, nadajesz MSAL `start Url` , który musi być wyświetlany w przeglądarce, aby użytkownik końcowy mógł wprowadzać elementy, takie jak ich nazwy użytkownika.
Po zakończeniu uwierzytelniania aplikacja musi przekazać z powrotem do MSAL `end Url` , który zawiera kod dostarczony przez usługę Azure AD.
Host `end Url` jest zawsze `redirectUri` . Aby przechwycić `end Url` , wykonaj jedną z następujących czynności:

- Monitoruj przekierowania przeglądarki do momentu, aż `redirect Url` zostanie osiągnięty.
- Przekieruj przeglądarkę do adresu URL, który jest monitorowany.

##### <a name="withcustomwebui-is-an-extensibility-point"></a>WithCustomWebUi jest punktem rozszerzalności

`WithCustomWebUi` jest punktem rozszerzalności, którego można użyć w celu zapewnienia własnego interfejsu użytkownika w publicznych aplikacjach klienckich. Możesz również zezwolić użytkownikowi na przechodzenie przez punkt końcowy/Authorize dostawcy tożsamości i Zezwalanie na logowanie się i wyrażanie zgody. MSAL.NET może następnie zrealizować kod uwierzytelniania i uzyskać token. Na przykład jest używany w programie Visual Studio do korzystania z aplikacji Electrons (na przykład opinii programu Visual Studio), aby zapewnić interakcję sieci Web, ale pozostawić ją do MSAL.NET w celu wykonania większości zadań. Można go również użyć, jeśli chcesz zapewnić automatyzację interfejsu użytkownika. W publicznych aplikacjach klienckich MSAL.NET korzysta z klucza testowego standardu Code Exchange (PKCE), aby zapewnić przestrzeganie zabezpieczeń. Tylko MSAL.NET może zrealizować kod. Aby uzyskać więcej informacji, zobacz [dokument RFC 7636-test Key dla wymiany kodu przez klientów publicznych uwierzytelniania OAuth](https://tools.ietf.org/html/rfc7636).

  ```csharp
  using Microsoft.Identity.Client.Extensions;
  ```

##### <a name="use-withcustomwebui"></a>Użyj WithCustomWebUi

Aby użyć programu `.WithCustomWebUI` , wykonaj następujące kroki.

  1. Zaimplementuj interfejs `ICustomWebUi`. Aby uzyskać więcej informacji, zobacz [tę witrynę sieci Web](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/blob/053a98d16596be7e9ca1ab916924e5736e341fe8/src/Microsoft.Identity.Client/Extensibility/ICustomWebUI.cs#L32-L70). Zaimplementuj jedną `AcquireAuthorizationCodeAsync` metodę i zaakceptuj adres URL kodu autoryzacji obliczony przez MSAL.NET. Następnie pozwól, aby użytkownik przeszedł przez interakcję z dostawcą tożsamości i zwracał adres URL, za pomocą którego dostawca tożsamości wywołał swoją implementację, wraz z kodem autoryzacji. Jeśli masz problemy, implementacja powinna zgłosić `MsalExtensionException` wyjątek, aby dobrze współpracę z MSAL.
  2. W `AcquireTokenInteractive` wywołaniu użyj `.WithCustomUI()` modyfikatora przekazującego wystąpienie niestandardowego interfejsu użytkownika sieci Web.

     ```csharp
     result = await app.AcquireTokenInteractive(scopes)
                       .WithCustomWebUi(yourCustomWebUI)
                       .ExecuteAsync();
     ```

##### <a name="examples-of-implementation-of-icustomwebui-in-test-automation-seleniumwebui"></a>Przykłady implementacji ICustomWebUi w automatyzacji testów: SeleniumWebUI

Zespół MSAL.NETy ponownie zapisał testy interfejsu użytkownika, aby użyć tego mechanizmu rozszerzalności. Jeśli interesuje Cię, spójrz na klasę [SeleniumWebUI](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/blob/053a98d16596be7e9ca1ab916924e5736e341fe8/tests/Microsoft.Identity.Test.Integration/Infrastructure/SeleniumWebUI.cs#L15-L160) w kodzie źródłowym MSAL.NET.

##### <a name="provide-a-great-experience-with-systemwebviewoptions"></a>Zapewnianie doskonałej obsługi SystemWebViewOptions

Z MSAL.NET 4,1 [`SystemWebViewOptions`](/dotnet/api/microsoft.identity.client.systemwebviewoptions?view=azure-dotnet) można określić:

- Identyfikator URI do przechodzenia do ( `BrowserRedirectError` ) lub fragmentu kodu HTML do wyświetlenia ( `HtmlMessageError` ) w przypadku błędów logowania lub wyrażania zgody w przeglądarce sieci Web systemu.
- Identyfikator URI, aby przejść do ( `BrowserRedirectSuccess` ) lub fragment kodu HTML do wyświetlenia ( `HtmlMessageSuccess` ) w przypadku pomyślnego logowania lub zgody.
- Akcja do uruchomienia w celu uruchomienia przeglądarki systemowej. Możesz wprowadzić własną implementację, ustawiając `OpenBrowserAsync` delegata. Klasa udostępnia również domyślną implementację dwóch przeglądarek: `OpenWithEdgeBrowserAsync` i `OpenWithChromeEdgeBrowserAsync` dla przeglądarki Microsoft Edge i [przeglądarki Microsoft Edge na chromie](https://www.windowscentral.com/faq-edge-chromium).

Aby użyć tej struktury, Napisz coś podobne do poniższego przykładu:

```csharp
IPublicClientApplication app;
...

options = new SystemWebViewOptions
{
 HtmlMessageError = "<b>Sign-in failed. You can close this tab ...</b>",
 BrowserRedirectSuccess = "https://contoso.com/help-for-my-awesome-commandline-tool.html"
};

var result = app.AcquireTokenInteractive(scopes)
                .WithEmbeddedWebView(false)       // The default in .NET Core
                .WithSystemWebViewOptions(options)
                .Build();
```

#### <a name="other-optional-parameters"></a>Inne parametry opcjonalne

Aby dowiedzieć się więcej na temat wszystkich innych parametrów opcjonalnych dla programu `AcquireTokenInteractive` , zobacz [AcquireTokenInteractiveParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokeninteractiveparameterbuilder?view=azure-dotnet-preview#methods).

# <a name="java"></a>[Java](#tab/java)

```java
private static IAuthenticationResult acquireTokenInteractive() throws Exception {

    // Load token cache from file and initialize token cache aspect. The token cache will have
    // dummy data, so the acquireTokenSilently call will fail.
    TokenCacheAspect tokenCacheAspect = new TokenCacheAspect("sample_cache.json");

    PublicClientApplication pca = PublicClientApplication.builder(CLIENT_ID)
            .authority(AUTHORITY)
            .setTokenCacheAccessAspect(tokenCacheAspect)
            .build();

    Set<IAccount> accountsInCache = pca.getAccounts().join();
    // Take first account in the cache. In a production application, you would filter
    // accountsInCache to get the right account for the user authenticating.
    IAccount account = accountsInCache.iterator().next();

    IAuthenticationResult result;
    try {
        SilentParameters silentParameters =
                SilentParameters
                        .builder(SCOPE, account)
                        .build();

        // try to acquire token silently. This call will fail since the token cache
        // does not have any data for the user you are trying to acquire a token for
        result = pca.acquireTokenSilently(silentParameters).join();
    } catch (Exception ex) {
        if (ex.getCause() instanceof MsalException) {

            InteractiveRequestParameters parameters = InteractiveRequestParameters
                    .builder(new URI("http://localhost"))
                    .scopes(SCOPE)
                    .build();

            // Try to acquire a token interactively with system browser. If successful, you should see
            // the token and account information printed out to console
            result = pca.acquireToken(parameters).join();
        } else {
            // Handle other exceptions accordingly
            throw ex;
        }
    }
    return result;
}
```

# <a name="python"></a>[Python](#tab/python)

MSAL Python nie zapewnia bezpośredniej metody tokenu pozyskiwania. Zamiast tego wymaga, aby aplikacja wysłała żądanie autoryzacji w ramach implementacji przepływu interakcji użytkownika w celu uzyskania kodu autoryzacji. Ten kod można następnie przesłać do `acquire_token_by_authorization_code` metody w celu uzyskania tokenu.

```Python
result = None

# Firstly, check the cache to see if this end user has signed in before
accounts = app.get_accounts(username=config["username"])
if accounts:
    result = app.acquire_token_silent(config["scope"], account=accounts[0])

if not result:
    result = app.acquire_token_by_authorization_code(
         request.args['code'],
         scopes=config["scope"])

```

# <a name="macos"></a>[macOS](#tab/macOS)

### <a name="in-msal-for-ios-and-macos"></a>W MSAL dla systemów iOS i macOS

Cel-C:

```objc
MSALInteractiveTokenParameters *interactiveParams = [[MSALInteractiveTokenParameters alloc] initWithScopes:scopes webviewParameters:[MSALWebviewParameters new]];
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

Adres

```swift
let interactiveParameters = MSALInteractiveTokenParameters(scopes: scopes, webviewParameters: MSALWebviewParameters())
application.acquireToken(with: interactiveParameters, completionBlock: { (result, error) in

    guard let authResult = result, error == nil else {
        print(error!.localizedDescription)
        return
    }

    // Get access token from result
    let accessToken = authResult.accessToken
})
```
---

## <a name="integrated-windows-authentication"></a>Zintegrowane uwierzytelnianie systemu Windows

Aby zalogować użytkownika domeny w domenie lub na komputerze przyłączonym do usługi Azure AD, użyj zintegrowanego uwierzytelniania systemu Windows (IWA).

### <a name="constraints"></a>Ograniczenia

- Zintegrowane uwierzytelnianie systemu Windows jest możliwe do użycia tylko w przypadku *federacyjnych* i użytkowników, czyli użytkowników utworzonych w Active Directory i objętych usługą Azure AD. Nie można używać tego przepływu uwierzytelniania przez użytkowników utworzonych bezpośrednio w usłudze Azure AD bez Active Directory kopii zapasowych, znanych jako użytkownicy *zarządzani* . To ograniczenie nie ma wpływu na przepływ nazwy użytkownika i hasła.
- IWA jest przeznaczony dla aplikacji pisanych na platformach .NET Framework, .NET Core i platforma uniwersalna systemu Windows (platformy UWP).
- IWA nie pomija [uwierzytelniania wieloskładnikowego (MFA)](../authentication/concept-mfa-howitworks.md). W przypadku skonfigurowania usługi MFA IWA może zakończyć się niepowodzeniem, jeśli wymagane jest wyzwanie usługi MFA, ponieważ usługa MFA wymaga interakcji z użytkownikiem.
  > [!NOTE]
  > Jest to jedna z nich. IWA nie jest interaktywny, ale uwierzytelnianie wieloskładnikowe wymaga interakcji z użytkownikiem. Nie kontrolujesz, kiedy dostawca tożsamości żąda wykonania uwierzytelniania MFA, Administrator dzierżawy. Z naszych zagadnień uwierzytelnianie wieloskładnikowe jest wymagane, gdy użytkownik loguje się z innego kraju/regionu, gdy nie jest połączony za pośrednictwem sieci VPN z siecią firmową, a czasami nawet w przypadku połączenia za pośrednictwem sieci VPN. Nie oczekiwano deterministycznego zestawu reguł. Usługa Azure AD używa systemu AI do ciągłego uczenia się, czy uwierzytelnianie wieloskładnikowe jest wymagane. Powróć do monitu użytkownika, takiego jak uwierzytelnianie interaktywne lub przepływ kodu urządzenia, jeśli IWA nie powiedzie się.

- Przesłany urząd `PublicClientApplicationBuilder` musi być:
  - Dzierżawca formularza `https://login.microsoftonline.com/{tenant}/` , gdzie `tenant` jest identyfikator GUID reprezentujący identyfikator dzierżawy lub domenę skojarzoną z dzierżawcą.
  - Dla wszystkich kont służbowych: `https://login.microsoftonline.com/organizations/` .
  - Konta osobiste firmy Microsoft nie są obsługiwane. Nie można używać dzierżawców/typowe ani/consumers.

- Ponieważ zintegrowane uwierzytelnianie systemu Windows jest dyskretnym przepływem:
  - Użytkownik Twojej aplikacji musi być wcześniej wyraził zgodę na korzystanie z aplikacji.
  - Lub Administrator dzierżawy musi być wcześniej wysłany do wszystkich użytkowników w dzierżawie, aby mógł korzystać z aplikacji.
  - Innymi słowy:
    - Deweloper zaznaczył przycisk **Udziel** w Azure Portal dla siebie.
    - Lub Administrator dzierżawy zaznaczył przycisk **Udziel/odwołaj zgodę administratora dla {domena dzierżawy}** na karcie **uprawnienia interfejsu API** rejestracji aplikacji. Aby uzyskać więcej informacji, zobacz [Dodawanie uprawnień w celu uzyskania dostępu do internetowego interfejsu API](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-your-web-api).
    - Możesz też zapewnić użytkownikom zgodę na stosowanie aplikacji. Aby uzyskać więcej informacji, zobacz [żądanie zgody poszczególnych użytkowników](./v2-permissions-and-consent.md#requesting-individual-user-consent).
    - Możesz też zapewnić, że administrator dzierżawy wyrazi zgodę na stosowanie aplikacji. Aby uzyskać więcej informacji, zobacz [zgoda administratora](./v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant).

- Ten przepływ jest włączony dla aplikacji klasycznych .NET, .NET Core i platformy UWP.

Aby uzyskać więcej informacji na temat zgody, zobacz [uprawnienia platformy tożsamości firmy Microsoft i wyrażanie zgody](./v2-permissions-and-consent.md).

### <a name="learn-how-to-use-it"></a>Dowiedz się, jak z niej korzystać

# <a name="net"></a>[.NET](#tab/dotnet)

W MSAL.NET należy użyć:

```csharp
AcquireTokenByIntegratedWindowsAuth(IEnumerable<string> scopes)
```

Zwykle potrzebny jest tylko jeden parametr ( `scopes` ). W zależności od sposobu skonfigurowania zasad przez administratora systemu Windows aplikacje na komputerze z systemem Windows mogą nie być w stanie wyszukiwać zalogowanego użytkownika. W takim przypadku należy użyć drugiej metody, `.WithUsername()` , i przekazać nazwę zalogowanego użytkownika jako format nazwy UPN, na przykład `joe@contoso.com` . W przypadku platformy .NET Core dostępna jest tylko Przeciążenie z nazwą użytkownika, ponieważ platforma .NET Core nie może zażądać nazwy użytkownika w systemie operacyjnym.

W poniższym przykładzie przedstawiono najbardziej aktualne informacje o rodzaju wyjątków, jakie można uzyskać, i ich ograniczeniach.

```csharp
static async Task GetATokenForGraph()
{
 string authority = "https://login.microsoftonline.com/contoso.com";
 string[] scopes = new string[] { "user.read" };
 IPublicClientApplication app = PublicClientApplicationBuilder
      .Create(clientId)
      .WithAuthority(authority)
      .Build();

 var accounts = await app.GetAccountsAsync();

 AuthenticationResult result = null;
 if (accounts.Any())
 {
  result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
      .ExecuteAsync();
 }
 else
 {
  try
  {
   result = await app.AcquireTokenByIntegratedWindowsAuth(scopes)
      .ExecuteAsync(CancellationToken.None);
  }
  catch (MsalUiRequiredException ex)
  {
   // MsalUiRequiredException: AADSTS65001: The user or administrator has not consented to use the application
   // with ID '{appId}' named '{appName}'.Send an interactive authorization request for this user and resource.

   // you need to get user consent first. This can be done, if you are not using .NET Core (which does not have any Web UI)
   // by doing (once only) an AcquireToken interactive.

   // If you are using .NET core or don't want to do an AcquireTokenInteractive, you might want to suggest the user to navigate
   // to a URL to consent: https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id={clientId}&response_type=code&scope=user.read

   // AADSTS50079: The user is required to use multi-factor authentication.
   // There is no mitigation - if MFA is configured for your tenant and AAD decides to enforce it,
   // you need to fallback to an interactive flows such as AcquireTokenInteractive or AcquireTokenByDeviceCode
   }
   catch (MsalServiceException ex)
   {
    // Kind of errors you could have (in ex.Message)

    // MsalServiceException: AADSTS90010: The grant type is not supported over the /common or /consumers endpoints. Please use the /organizations or tenant-specific endpoint.
    // you used common.
    // Mitigation: as explained in the message from Azure AD, the authority needs to be tenanted or otherwise organizations

    // MsalServiceException: AADSTS70002: The request body must contain the following parameter: 'client_secret or client_assertion'.
    // Explanation: this can happen if your application was not registered as a public client application in Azure AD
    // Mitigation: in the Azure portal, edit the manifest for your application and set the `allowPublicClient` to `true`
   }
   catch (MsalClientException ex)
   {
      // Error Code: unknown_user Message: Could not identify logged in user
      // Explanation: the library was unable to query the current Windows logged-in user or this user is not AD or AAD
      // joined (work-place joined users are not supported).

      // Mitigation 1: on UWP, check that the application has the following capabilities: Enterprise Authentication,
      // Private Networks (Client and Server), User Account Information

      // Mitigation 2: Implement your own logic to fetch the username (e.g. john@contoso.com) and use the
      // AcquireTokenByIntegratedWindowsAuth form that takes in the username

      // Error Code: integrated_windows_auth_not_supported_managed_user
      // Explanation: This method relies on an a protocol exposed by Active Directory (AD). If a user was created in Azure
      // Active Directory without AD backing ("managed" user), this method will fail. Users created in AD and backed by
      // AAD ("federated" users) can benefit from this non-interactive method of authentication.
      // Mitigation: Use interactive authentication
   }
 }


 Console.WriteLine(result.Account.Username);
}
```

Aby uzyskać listę możliwych modyfikatorów w AcquireTokenByIntegratedWindowsAuthentication, zobacz [AcquireTokenByIntegratedWindowsAuthParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokenbyintegratedwindowsauthparameterbuilder?view=azure-dotnet-preview#methods).

# <a name="java"></a>[Java](#tab/java)

Ten wyciąg pochodzi z [przykładów deweloperskich MSAL Java](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/public-client/).

```Java
private static IAuthenticationResult acquireTokenIwa() throws Exception {

    // Load token cache from file and initialize token cache aspect. The token cache will have
    // dummy data, so the acquireTokenSilently call will fail.
    TokenCacheAspect tokenCacheAspect = new TokenCacheAspect("sample_cache.json");

    PublicClientApplication pca = PublicClientApplication.builder(CLIENT_ID)
            .authority(AUTHORITY)
            .setTokenCacheAccessAspect(tokenCacheAspect)
            .build();

    Set<IAccount> accountsInCache = pca.getAccounts().join();
    // Take first account in the cache. In a production application, you would filter
    // accountsInCache to get the right account for the user authenticating.
    IAccount account = accountsInCache.iterator().next();

    IAuthenticationResult result;
    try {
        SilentParameters silentParameters =
                SilentParameters
                        .builder(SCOPE, account)
                        .build();

        // try to acquire token silently. This call will fail since the token cache
        // does not have any data for the user you are trying to acquire a token for
        result = pca.acquireTokenSilently(silentParameters).join();
    } catch (Exception ex) {
        if (ex.getCause() instanceof MsalException) {

            IntegratedWindowsAuthenticationParameters parameters =
                    IntegratedWindowsAuthenticationParameters
                            .builder(SCOPE, USER_NAME)
                            .build();

            // Try to acquire a IWA. You will need to generate a Kerberos ticket.
            // If successful, you should see the token and account information printed out to
            // console
            result = pca.acquireToken(parameters).join();
        } else {
            // Handle other exceptions accordingly
            throw ex;
        }
    }
    return result;
}
```

# <a name="python"></a>[Python](#tab/python)

Ten przepływ nie jest jeszcze obsługiwany w języku Python MSAL.

# <a name="macos"></a>[macOS](#tab/macOS)

Ten przepływ nie ma zastosowania do macOS.

---

## <a name="username-and-password"></a>Nazwa użytkownika i hasło

Możesz również uzyskać token, podając nazwę użytkownika i hasło. Ten przepływ jest ograniczony i nie jest zalecany, ale nadal istnieją przypadki, w których jest to konieczne.

### <a name="this-flow-isnt-recommended"></a>Ten przepływ nie jest zalecany

Ten przepływ *nie jest zalecany* , ponieważ aplikacja nie poprosiła użytkownika o podanie hasła nie jest bezpieczna. Aby uzyskać więcej informacji, zobacz [co to jest rozwiązanie w przypadku rosnącego problemu z hasłami?](https://news.microsoft.com/features/whats-solution-growing-problem-passwords-says-microsoft/). Preferowany przepływ do uzyskiwania tokenu w trybie dyskretnym na komputerach przyłączonych do domeny systemu Windows jest [zintegrowanym uwierzytelnianiem systemu Windows](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Integrated-Windows-Authentication). Można również użyć [przepływu kodu urządzenia](https://aka.ms/msal-net-device-code-flow).

> [!NOTE]
> Korzystanie z nazwy użytkownika i hasła jest przydatne w niektórych przypadkach, takich jak scenariusze DevOps. Jeśli jednak chcesz używać nazwy użytkownika i hasła w scenariuszach interaktywnych, w których udostępniasz własny interfejs użytkownika, pomyśl o tym, jak przejść z niego. Przy użyciu nazwy użytkownika i hasła podajesz wiele rzeczy:
>
> - Rdzeń założenia nowoczesnej tożsamości. Hasło można pobrać i odtworzyć, ponieważ można przechwycić wspólny klucz tajny. Jest on niezgodny z hasłem.
> - Użytkownicy, którzy muszą wykonywać uwierzytelnianie wieloskładnikowe, nie mogą się zalogować, ponieważ nie ma żadnej interakcji.
> - Użytkownicy nie mogą wykonywać rejestracji jednokrotnej (SSO).

### <a name="constraints"></a>Ograniczenia

Obowiązują również następujące ograniczenia:

- Przepływ nazwy użytkownika i hasła nie jest zgodny z dostępem warunkowym i uwierzytelnianiem wieloskładnikowym. W związku z tym, jeśli aplikacja działa w dzierżawie usługi Azure AD, w której Administrator dzierżawy wymaga uwierzytelniania wieloskładnikowego, nie można użyć tego przepływu. Wiele organizacji to.
- Działa tylko w przypadku kont służbowych (nie jako MSA).
- Przepływ jest dostępny w programie .NET Desktop i .NET Core, ale nie w systemie platformy UWP.

### <a name="b2c-specifics"></a>B2C

Aby uzyskać więcej informacji, zobacz [poświadczenia hasła właściciela zasobu (ROPC) z B2C](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-specifics#resource-owner-password-credentials-ropc-with-b2c).

### <a name="use-it"></a>Korzystanie z niego

# <a name="net"></a>[.NET](#tab/dotnet)

`IPublicClientApplication`zawiera metodę `AcquireTokenByUsernamePassword` .

W poniższym przykładzie przedstawiono uproszczony przypadek.

```csharp
static async Task GetATokenForGraph()
{
 string authority = "https://login.microsoftonline.com/contoso.com";
 string[] scopes = new string[] { "user.read" };
 IPublicClientApplication app;
 app = PublicClientApplicationBuilder.Create(clientId)
       .WithAuthority(authority)
       .Build();
 var accounts = await app.GetAccountsAsync();

 AuthenticationResult result = null;
 if (accounts.Any())
 {
  result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
                    .ExecuteAsync();
 }
 else
 {
  try
  {
   var securePassword = new SecureString();
   foreach (char c in "dummy")        // you should fetch the password
    securePassword.AppendChar(c);  // keystroke by keystroke

   result = await app.AcquireTokenByUsernamePassword(scopes,
                                                    "joe@contoso.com",
                                                     securePassword)
                      .ExecuteAsync();
  }
  catch(MsalException)
  {
   // See details below
  }
 }
 Console.WriteLine(result.Account.Username);
}
```

W poniższym przykładzie przedstawiono najbardziej aktualne informacje o rodzaju wyjątków, jakie można uzyskać, i ich ograniczeniach.

```csharp
static async Task GetATokenForGraph()
{
 string authority = "https://login.microsoftonline.com/contoso.com";
 string[] scopes = new string[] { "user.read" };
 IPublicClientApplication app;
 app = PublicClientApplicationBuilder.Create(clientId)
                                   .WithAuthority(authority)
                                   .Build();
 var accounts = await app.GetAccountsAsync();

 AuthenticationResult result = null;
 if (accounts.Any())
 {
  result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
                    .ExecuteAsync();
 }
 else
 {
  try
  {
   var securePassword = new SecureString();
   foreach (char c in "dummy")        // you should fetch the password keystroke
    securePassword.AppendChar(c);  // by keystroke

   result = await app.AcquireTokenByUsernamePassword(scopes,
                                                    "joe@contoso.com",
                                                    securePassword)
                    .ExecuteAsync();
  }
  catch (MsalUiRequiredException ex) when (ex.Message.Contains("AADSTS65001"))
  {
   // Here are the kind of error messages you could have, and possible mitigations

   // ------------------------------------------------------------------------
   // MsalUiRequiredException: AADSTS65001: The user or administrator has not consented to use the application
   // with ID '{appId}' named '{appName}'. Send an interactive authorization request for this user and resource.

   // Mitigation: you need to get user consent first. This can be done either statically (through the portal),
   /// or dynamically (but this requires an interaction with Azure AD, which is not possible with
   // the username/password flow)
   // Statically: in the portal by doing the following in the "API permissions" tab of the application registration:
   // 1. Click "Add a permission" and add all the delegated permissions corresponding to the scopes you want (for instance
   // User.Read and User.ReadBasic.All)
   // 2. Click "Grant/revoke admin consent for <tenant>") and click "yes".
   // Dynamically, if you are not using .NET Core (which does not have any Web UI) by
   // calling (once only) AcquireTokenInteractive.
   // remember that Username/password is for public client applications that is desktop/mobile applications.
   // If you are using .NET core or don't want to call AcquireTokenInteractive, you might want to:
   // - use device code flow (See https://aka.ms/msal-net-device-code-flow)
   // - or suggest the user to navigate to a URL to consent: https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id={clientId}&response_type=code&scope=user.read
   // ------------------------------------------------------------------------

   // ------------------------------------------------------------------------
   // ErrorCode: invalid_grant
   // SubError: basic_action
   // MsalUiRequiredException: AADSTS50079: The user is required to use multi-factor authentication.
   // The tenant admin for your organization has chosen to oblige users to perform multi-factor authentication.
   // Mitigation: none for this flow
   // Your application cannot use the Username/Password grant.
   // Like in the previous case, you might want to use an interactive flow (AcquireTokenInteractive()),
   // or Device Code Flow instead.
   // Note this is one of the reason why using username/password is not recommended;
   // ------------------------------------------------------------------------

   // ------------------------------------------------------------------------
   // ex.ErrorCode: invalid_grant
   // subError: null
   // Message = "AADSTS70002: Error validating credentials.
   // AADSTS50126: Invalid username or password
   // In the case of a managed user (user from an Azure AD tenant opposed to a
   // federated user, which would be owned
   // in another IdP through ADFS), the user has entered the wrong password
   // Mitigation: ask the user to re-enter the password
   // ------------------------------------------------------------------------

   // ------------------------------------------------------------------------
   // ex.ErrorCode: invalid_grant
   // subError: null
   // MsalServiceException: ADSTS50034: To sign into this application the account must be added to
   // the {domainName} directory.
   // or The user account does not exist in the {domainName} directory. To sign into this application,
   // the account must be added to the directory.
   // The user was not found in the directory
   // Explanation: wrong username
   // Mitigation: ask the user to re-enter the username.
   // ------------------------------------------------------------------------
  }
  catch (MsalServiceException ex) when (ex.ErrorCode == "invalid_request")
  {
   // ------------------------------------------------------------------------
   // AADSTS90010: The grant type is not supported over the /common or /consumers endpoints.
   // Please use the /organizations or tenant-specific endpoint.
   // you used common.
   // Mitigation: as explained in the message from Azure AD, the authority you use in the application needs
   // to be tenanted or otherwise "organizations". change the
   // "Tenant": property in the appsettings.json to be a GUID (tenant Id), or domain name (contoso.com)
   // if such a domain is registered with your tenant
   // or "organizations", if you want this application to sign-in users in any Work and School accounts.
   // ------------------------------------------------------------------------

  }
  catch (MsalServiceException ex) when (ex.ErrorCode == "unauthorized_client")
  {
   // ------------------------------------------------------------------------
   // AADSTS700016: Application with identifier '{clientId}' was not found in the directory '{domain}'.
   // This can happen if the application has not been installed by the administrator of the tenant or consented
   // to by any user in the tenant.
   // You may have sent your authentication request to the wrong tenant
   // Cause: The clientId in the appsettings.json might be wrong
   // Mitigation: check the clientId and the app registration
   // ------------------------------------------------------------------------
  }
  catch (MsalServiceException ex) when (ex.ErrorCode == "invalid_client")
  {
   // ------------------------------------------------------------------------
   // AADSTS70002: The request body must contain the following parameter: 'client_secret or client_assertion'.
   // Explanation: this can happen if your application was not registered as a public client application in Azure AD
   // Mitigation: in the Azure portal, edit the manifest for your application and set the `allowPublicClient` to `true`
   // ------------------------------------------------------------------------
  }
  catch (MsalServiceException)
  {
   throw;
  }

  catch (MsalClientException ex) when (ex.ErrorCode == "unknown_user_type")
  {
   // Message = "Unsupported User Type 'Unknown'. Please see https://aka.ms/msal-net-up"
   // The user is not recognized as a managed user, or a federated user. Azure AD was not
   // able to identify the IdP that needs to process the user
   throw new ArgumentException("U/P: Wrong username", ex);
  }
  catch (MsalClientException ex) when (ex.ErrorCode == "user_realm_discovery_failed")
  {
   // The user is not recognized as a managed user, or a federated user. Azure AD was not
   // able to identify the IdP that needs to process the user. That's for instance the case
   // if you use a phone number
   throw new ArgumentException("U/P: Wrong username", ex);
  }
  catch (MsalClientException ex) when (ex.ErrorCode == "unknown_user")
  {
   // the username was probably empty
   // ex.Message = "Could not identify the user logged into the OS. See https://aka.ms/msal-net-iwa for details."
   throw new ArgumentException("U/P: Wrong username", ex);
  }
  catch (MsalClientException ex) when (ex.ErrorCode == "parsing_wstrust_response_failed")
  {
   // ------------------------------------------------------------------------
   // In the case of a Federated user (that is owned by a federated IdP, as opposed to a managed user owned in an Azure AD tenant)
   // ID3242: The security token could not be authenticated or authorized.
   // The user does not exist or has entered the wrong password
   // ------------------------------------------------------------------------
  }
 }

 Console.WriteLine(result.Account.Username);
}
```

Aby uzyskać więcej informacji na temat wszystkich modyfikatorów, które można zastosować do `AcquireTokenByUsernamePassword` , zobacz [AcquireTokenByUsernamePasswordParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokenbyusernamepasswordparameterbuilder?view=azure-dotnet-preview#methods).

# <a name="java"></a>[Java](#tab/java)

Poniższy wyciąg pochodzi z [przykładów deweloperskich MSAL Java](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/public-client/).

```Java
private static IAuthenticationResult acquireTokenUsernamePassword() throws Exception {

    // Load token cache from file and initialize token cache aspect. The token cache will have
    // dummy data, so the acquireTokenSilently call will fail.
    TokenCacheAspect tokenCacheAspect = new TokenCacheAspect("sample_cache.json");

    PublicClientApplication pca = PublicClientApplication.builder(CLIENT_ID)
            .authority(AUTHORITY)
            .setTokenCacheAccessAspect(tokenCacheAspect)
            .build();

    Set<IAccount> accountsInCache = pca.getAccounts().join();
    // Take first account in the cache. In a production application, you would filter
    // accountsInCache to get the right account for the user authenticating.
    IAccount account = accountsInCache.iterator().next();

    IAuthenticationResult result;
    try {
        SilentParameters silentParameters =
                SilentParameters
                        .builder(SCOPE, account)
                        .build();
        // try to acquire token silently. This call will fail since the token cache
        // does not have any data for the user you are trying to acquire a token for
        result = pca.acquireTokenSilently(silentParameters).join();
    } catch (Exception ex) {
        if (ex.getCause() instanceof MsalException) {

            UserNamePasswordParameters parameters =
                    UserNamePasswordParameters
                            .builder(SCOPE, USER_NAME, USER_PASSWORD.toCharArray())
                            .build();
            // Try to acquire a token via username/password. If successful, you should see
            // the token and account information printed out to console
            result = pca.acquireToken(parameters).join();
        } else {
            // Handle other exceptions accordingly
            throw ex;
        }
    }
    return result;
}
```

# <a name="python"></a>[Python](#tab/python)

Ten wyciąg pochodzi z [przykładów deweloperskich MSAL języka Python](https://github.com/AzureAD/microsoft-authentication-library-for-python/blob/dev/sample/).

```Python
# Create a preferably long-lived app instance which maintains a token cache.
app = msal.PublicClientApplication(
    config["client_id"], authority=config["authority"],
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.rtfd.io/en/latest/#msal.SerializableTokenCache
    )

# The pattern to acquire a token looks like this.
result = None

# Firstly, check the cache to see if this end user has signed in before
accounts = app.get_accounts(username=config["username"])
if accounts:
    logging.info("Account(s) exists in cache, probably with token too. Let's try.")
    result = app.acquire_token_silent(config["scope"], account=accounts[0])

if not result:
    logging.info("No suitable token exists in cache. Let's get a new one from AAD.")
    # See this page for constraints of Username Password Flow.
    # https://github.com/AzureAD/microsoft-authentication-library-for-python/wiki/Username-Password-Authentication
    result = app.acquire_token_by_username_password(
        config["username"], config["password"], scopes=config["scope"])
```

# <a name="macos"></a>[macOS](#tab/macOS)

Ten przepływ nie jest obsługiwany w MSAL dla macOS.

---

## <a name="command-line-tool-without-a-web-browser"></a>Narzędzie wiersza polecenia bez przeglądarki sieci Web

### <a name="device-code-flow"></a>Przepływ kodu urządzenia

Jeśli piszesz narzędzie wiersza polecenia, które nie ma formantów sieci Web, a nie możesz lub nie chcesz używać powyższych przepływów, musisz użyć przepływu kodu urządzenia.

Interaktywne uwierzytelnianie w usłudze Azure AD wymaga przeglądarki sieci Web. Aby uzyskać więcej informacji, zobacz [użycie przeglądarek sieci Web](https://aka.ms/msal-net-uses-web-browser). W celu uwierzytelniania użytkowników na urządzeniach lub w systemach operacyjnych, które nie zapewniają przeglądarki sieci Web, przepływ kodu urządzenia umożliwia użytkownikowi korzystanie z innego urządzenia, takiego jak komputer lub telefon komórkowy, aby zalogować się interaktywnie. Za pomocą przepływu kodu urządzenia aplikacja uzyskuje tokeny w ramach dwuetapowego procesu, który jest przeznaczony dla tych urządzeń lub systemów operacyjnych. Przykładami takich aplikacji są aplikacje uruchamiane w ramach iOT lub narzędzi wiersza polecenia (CLI). Pomysł polega na tym, że:

1. Za każdym razem, gdy wymagane jest uwierzytelnienie użytkownika, aplikacja udostępnia kod dla użytkownika. Użytkownik zostanie poproszony o użycie innego urządzenia, takiego jak smartfon połączony z Internetem, aby przejść do adresu URL, na przykład `https://microsoft.com/devicelogin` . Następnie użytkownik zostanie poproszony o wprowadzenie kodu. W tym celu strona sieci Web kieruje użytkownika za pomocą normalnego środowiska uwierzytelniania, w tym w razie potrzeby zawiera również polecenia zgody i uwierzytelniania wieloskładnikowego.

2. Po pomyślnym uwierzytelnieniu aplikacja wiersza polecenia otrzymuje wymagane tokeny za pośrednictwem kanału zaplecza i używa ich do wykonywania wywołań interfejsu API sieci Web.

### <a name="use-it"></a>Korzystanie z niego

# <a name="net"></a>[.NET](#tab/dotnet)

`IPublicClientApplication`zawiera metodę o nazwie `AcquireTokenWithDeviceCode` .

```csharp
 AcquireTokenWithDeviceCode(IEnumerable<string> scopes,
                            Func<DeviceCodeResult, Task> deviceCodeResultCallback)
```

Ta metoda przyjmuje jako parametry:

- `scopes`Do żądania tokenu dostępu dla.
- Wywołanie zwrotne, które odbiera `DeviceCodeResult` .

  ![Właściwości DeviceCodeResult](https://user-images.githubusercontent.com/13203188/56024968-7af1b980-5d11-11e9-84c2-5be2ef306dc5.png)

Poniższy przykładowy kod przedstawia najbardziej aktualny przypadek, z objaśnieniami rodzaju wyjątków, jakie można uzyskać, oraz ich ograniczenia.

```csharp
private const string ClientId = "<client_guid>";
private const string Authority = "https://login.microsoftonline.com/contoso.com";
private readonly string[] Scopes = new string[] { "user.read" };

static async Task<AuthenticationResult> GetATokenForGraph()
{
    IPublicClientApplication pca = PublicClientApplicationBuilder
            .Create(ClientId)
            .WithAuthority(Authority)
            .WithDefaultRedirectUri()
            .Build();

    var accounts = await pca.GetAccountsAsync();

    // All AcquireToken* methods store the tokens in the cache, so check the cache first
    try
    {
        return await pca.AcquireTokenSilent(Scopes, accounts.FirstOrDefault())
            .ExecuteAsync();
    }
    catch (MsalUiRequiredException ex)
    {
        // No token found in the cache or AAD insists that a form interactive auth is required (e.g. the tenant admin turned on MFA)
        // If you want to provide a more complex user experience, check out ex.Classification

        return await AcquireByDeviceCodeAsync(pca);
    }
}

private async Task<AuthenticationResult> AcquireByDeviceCodeAsync(IPublicClientApplication pca)
{
    try
    {
        var result = await pca.AcquireTokenWithDeviceCode(scopes,
            deviceCodeResult =>
            {
                    // This will print the message on the console which tells the user where to go sign-in using
                    // a separate browser and the code to enter once they sign in.
                    // The AcquireTokenWithDeviceCode() method will poll the server after firing this
                    // device code callback to look for the successful login of the user via that browser.
                    // This background polling (whose interval and timeout data is also provided as fields in the
                    // deviceCodeCallback class) will occur until:
                    // * The user has successfully logged in via browser and entered the proper code
                    // * The timeout specified by the server for the lifetime of this code (typically ~15 minutes) has been reached
                    // * The developing application calls the Cancel() method on a CancellationToken sent into the method.
                    //   If this occurs, an OperationCanceledException will be thrown (see catch below for more details).
                    Console.WriteLine(deviceCodeResult.Message);
                return Task.FromResult(0);
            }).ExecuteAsync();

        Console.WriteLine(result.Account.Username);
        return result;
    }
    // TODO: handle or throw all these exceptions depending on your app
    catch (MsalServiceException ex)
    {
        // Kind of errors you could have (in ex.Message)

        // AADSTS50059: No tenant-identifying information found in either the request or implied by any provided credentials.
        // Mitigation: as explained in the message from Azure AD, the authoriy needs to be tenanted. you have probably created
        // your public client application with the following authorities:
        // https://login.microsoftonline.com/common or https://login.microsoftonline.com/organizations

        // AADSTS90133: Device Code flow is not supported under /common or /consumers endpoint.
        // Mitigation: as explained in the message from Azure AD, the authority needs to be tenanted

        // AADSTS90002: Tenant <tenantId or domain you used in the authority> not found. This may happen if there are
        // no active subscriptions for the tenant. Check with your subscription administrator.
        // Mitigation: if you have an active subscription for the tenant this might be that you have a typo in the
        // tenantId (GUID) or tenant domain name.
    }
    catch (OperationCanceledException ex)
    {
        // If you use a CancellationToken, and call the Cancel() method on it, then this *may* be triggered
        // to indicate that the operation was cancelled.
        // See https://docs.microsoft.com/dotnet/standard/threading/cancellation-in-managed-threads
        // for more detailed information on how C# supports cancellation in managed threads.
    }
    catch (MsalClientException ex)
    {
        // Possible cause - verification code expired before contacting the server
        // This exception will occur if the user does not manage to sign-in before a time out (15 mins) and the
        // call to `AcquireTokenWithDeviceCode` is not cancelled in between
    }
}
```
# <a name="java"></a>[Java](#tab/java)

Ten wyciąg pochodzi z [przykładów deweloperskich MSAL Java](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/public-client/).

```java
private static IAuthenticationResult acquireTokenDeviceCode() throws Exception {

    // Load token cache from file and initialize token cache aspect. The token cache will have
    // dummy data, so the acquireTokenSilently call will fail.
    TokenCacheAspect tokenCacheAspect = new TokenCacheAspect("sample_cache.json");

    PublicClientApplication pca = PublicClientApplication.builder(CLIENT_ID)
            .authority(AUTHORITY)
            .setTokenCacheAccessAspect(tokenCacheAspect)
            .build();

    Set<IAccount> accountsInCache = pca.getAccounts().join();
    // Take first account in the cache. In a production application, you would filter
    // accountsInCache to get the right account for the user authenticating.
    IAccount account = accountsInCache.iterator().next();

    IAuthenticationResult result;
    try {
        SilentParameters silentParameters =
                SilentParameters
                        .builder(SCOPE, account)
                        .build();

        // try to acquire token silently. This call will fail since the token cache
        // does not have any data for the user you are trying to acquire a token for
        result = pca.acquireTokenSilently(silentParameters).join();
    } catch (Exception ex) {
        if (ex.getCause() instanceof MsalException) {

            Consumer<DeviceCode> deviceCodeConsumer = (DeviceCode deviceCode) ->
                    System.out.println(deviceCode.message());

            DeviceCodeFlowParameters parameters =
                    DeviceCodeFlowParameters
                            .builder(SCOPE, deviceCodeConsumer)
                            .build();

            // Try to acquire a token via device code flow. If successful, you should see
            // the token and account information printed out to console, and the sample_cache.json
            // file should have been updated with the latest tokens.
            result = pca.acquireToken(parameters).join();
        } else {
            // Handle other exceptions accordingly
            throw ex;
        }
    }
    return result;
}
```

# <a name="python"></a>[Python](#tab/python)

Ten wyciąg pochodzi z [przykładów deweloperskich MSAL języka Python](https://github.com/AzureAD/microsoft-authentication-library-for-python/blob/dev/sample/).

```Python
# Create a preferably long-lived app instance which maintains a token cache.
app = msal.PublicClientApplication(
    config["client_id"], authority=config["authority"],
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.rtfd.io/en/latest/#msal.SerializableTokenCache
    )

# The pattern to acquire a token looks like this.
result = None

# Note: If your device-flow app does not have any interactive ability, you can
#   completely skip the following cache part. But here we demonstrate it anyway.
# We now check the cache to see if we have some end users signed in before.
accounts = app.get_accounts()
if accounts:
    logging.info("Account(s) exists in cache, probably with token too. Let's try.")
    print("Pick the account you want to use to proceed:")
    for a in accounts:
        print(a["username"])
    # Assuming the end user chose this one
    chosen = accounts[0]
    # Now let's try to find a token in cache for this account
    result = app.acquire_token_silent(config["scope"], account=chosen)

if not result:
    logging.info("No suitable token exists in cache. Let's get a new one from AAD.")

    flow = app.initiate_device_flow(scopes=config["scope"])
    if "user_code" not in flow:
        raise ValueError(
            "Fail to create device flow. Err: %s" % json.dumps(flow, indent=4))

    print(flow["message"])
    sys.stdout.flush()  # Some terminal needs this to ensure the message is shown

    # Ideally you should wait here, in order to save some unnecessary polling
    # input("Press Enter after signing in from another device to proceed, CTRL+C to abort.")

    result = app.acquire_token_by_device_flow(flow)  # By default it will block
        # You can follow this instruction to shorten the block time
        #    https://msal-python.readthedocs.io/en/latest/#msal.PublicClientApplication.acquire_token_by_device_flow
        # or you may even turn off the blocking behavior,
        # and then keep calling acquire_token_by_device_flow(flow) in your own customized loop
```

# <a name="macos"></a>[macOS](#tab/macOS)

Ten przepływ nie ma zastosowania do macOS.

---

## <a name="file-based-token-cache"></a>Pamięć podręczna tokenów opartych na plikach

W MSAL.NET jest udostępniana pamięć podręczna tokenów w pamięci.

### <a name="serialization-is-customizable-in-windows-desktop-apps-and-web-apps-or-web-apis"></a>Serializacja można dostosowywać w aplikacjach klasycznych systemu Windows i aplikacjach sieci Web lub interfejsach API sieci Web

W przypadku .NET Framework i .NET Core, jeśli nie wykonujesz żadnych dodatkowych czynności, pamięć podręczna tokenów w pamięci wystarcza na czas trwania aplikacji. Aby zrozumieć, dlaczego Serializacja nie jest dostarczana, należy pamiętać, że aplikacje programu .NET Desktop lub .NET Core mogą być aplikacjami konsolowymi lub systemu Windows (które mają dostęp do systemu plików), *ale także* aplikacje sieci Web lub interfejsy API sieci Web. Te aplikacje sieci Web i interfejsy API sieci Web mogą korzystać z określonych mechanizmów pamięci podręcznej, takich jak bazy danych, rozproszone pamięci podręczne i pamięci podręczne Redis. Aby można było korzystać z trwałej pamięci podręcznej tokenów w programie .NET Desktop lub .NET Core, należy dostosować serializację.

Klasy i interfejsy wykorzystywane w serializacji pamięci podręcznej tokenu są następującymi typami:

- ``ITokenCache``, który definiuje zdarzenia subskrybowania żądań serializacji pamięci podręcznej tokenów i metody serializacji lub deserializacji pamięci podręcznej w różnych formatach (ADAL v 3.0, MSAL 2. x i MSAL 3. x = ADAL v 5.0).
- ``TokenCacheCallback`` to wywołanie zwrotne przesłane do zdarzeń, aby można było obsłużyć serializacji. Zostaną one wywołane z argumentami typu ``TokenCacheNotificationArgs`` .
- ``TokenCacheNotificationArgs`` zapewnia tylko aplikację ``ClientId`` i odwołanie do użytkownika, dla którego token jest dostępny.

  ![Diagram serializacji pamięci podręcznej tokenów](https://user-images.githubusercontent.com/13203188/56027172-d58d1480-5d15-11e9-8ada-c0292f1800b3.png)

> [!IMPORTANT]
> MSAL.NET tworzy pamięć podręczną tokenów i udostępnia `IToken` pamięć podręczną, gdy wywołasz aplikację `UserTokenCache` i `AppTokenCache` właściwości. Nie zaleca się zaimplementowania interfejsu samodzielnie. Podczas implementowania serializacji pamięci podręcznej tokenów niestandardowych należy wykonać następujące zadanie:
>
> - Reagowanie `BeforeAccess` na `AfterAccess` zdarzenia i lub ich odpowiedniki *asynchroniczne* . `BeforeAccess`Delegat jest odpowiedzialny za deserializowanie pamięci podręcznej. `AfterAccess`Delegat jest odpowiedzialny za Serializowanie pamięci podręcznej.
> - Zrozumienie, że część tych zdarzeń przechowuje lub ładuje obiekty blob, które są przesyłane przez argument zdarzenia do dowolnego potrzebnego miejsca.

Strategie różnią się w zależności od tego, czy piszesz serializacji pamięci podręcznej tokenów dla publicznej aplikacji klienckiej, takiej jak komputer stacjonarny, czy poufna aplikacja kliencka, np. aplikacja sieci Web lub interfejs API sieci Web lub aplikacja demona.

Ponieważ MSAL v2. x, istnieje kilka opcji. Wybór zależy od tego, czy chcesz serializować pamięć podręczną tylko do formatu MSAL.NET, który jest ujednoliconą pamięcią podręczną formatu, która jest wspólna z MSAL, ale również na platformach. Lub można także zapewnić obsługę serializacji [starszej](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization) pamięci podręcznej tokenów biblioteki ADAL v3.

Dostosowanie serializacji pamięci podręcznej tokenów w celu udostępnienia stanu logowania jednokrotnego między ADAL.NET 3. x, ADAL.NET 5. x i MSAL.NET jest wyjaśnione w części przykładowej [usługi Active Directory-dotnet-V1-to-v2](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2).

### <a name="simple-token-cache-serialization-msal-only"></a>Serializacja prostej pamięci podręcznej tokenów (tylko MSAL)

Poniższy przykład to algorytmie implementacja niestandardowej serializacji pamięci podręcznej tokenów dla aplikacji klasycznych. W tym miejscu pamięć podręczna tokenów użytkownika znajduje się w pliku w tym samym folderze, w którym znajduje się aplikacja.

Po skompilowaniu aplikacji należy włączyć serializację, wywołując ``TokenCacheHelper.EnableSerialization()`` i przekazując aplikację `UserTokenCache` .

```csharp
app = PublicClientApplicationBuilder.Create(ClientId)
    .Build();
TokenCacheHelper.EnableSerialization(app.UserTokenCache);
```

Ta klasa pomocnika wygląda jak w poniższym fragmencie kodu:

```csharp
static class TokenCacheHelper
 {
  public static void EnableSerialization(ITokenCache tokenCache)
  {
   tokenCache.SetBeforeAccess(BeforeAccessNotification);
   tokenCache.SetAfterAccess(AfterAccessNotification);
  }

  /// <summary>
  /// Path to the token cache
  /// </summary>
  public static readonly string CacheFilePath = System.Reflection.Assembly.GetExecutingAssembly().Location + ".msalcache.bin3";

  private static readonly object FileLock = new object();


  private static void BeforeAccessNotification(TokenCacheNotificationArgs args)
  {
   lock (FileLock)
   {
    args.TokenCache.DeserializeMsalV3(File.Exists(CacheFilePath)
            ? ProtectedData.Unprotect(File.ReadAllBytes(CacheFilePath),
                                      null,
                                      DataProtectionScope.CurrentUser)
            : null);
   }
  }

  private static void AfterAccessNotification(TokenCacheNotificationArgs args)
  {
   // if the access operation resulted in a cache update
   if (args.HasStateChanged)
   {
    lock (FileLock)
    {
     // reflect changesgs in the persistent store
     File.WriteAllBytes(CacheFilePath,
                         ProtectedData.Protect(args.TokenCache.SerializeMsalV3(),
                                                 null,
                                                 DataProtectionScope.CurrentUser)
                         );
    }
   }
  }
 }
```

Wersja zapoznawcza serializatora opartego na plikach pamięci podręcznej tokenu jakości produktu dla publicznych aplikacji klienckich dla aplikacji klasycznych działających w systemach Windows, Mac i Linux jest dostępna w bibliotece [Microsoft. Identity. Client. Extensions. Msal](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/tree/master/src/Microsoft.Identity.Client.Extensions.Msal) Open-Source. Można go uwzględnić w aplikacjach z następującego pakietu NuGet: [Microsoft. Identity. Client. Extensions. Msal](https://www.nuget.org/packages/Microsoft.Identity.Client.Extensions.Msal/).

> [!NOTE]
> Zastrzeżenie: Biblioteka Microsoft. Identity. Client. Extensions. Msal jest rozszerzeniem od MSAL.NET. Klasy w tych bibliotekach mogą sprawiać, że MSAL.NET w przyszłości, w zależności od tego, czy są istotne zmiany.

### <a name="dual-token-cache-serialization-msal-unified-cache--adal-v3"></a>Serializacja podwójnego tokenu pamięci podręcznej (MSAL Unified cache + ADAL v3)

Możesz chcieć zaimplementować serializacji pamięci podręcznej tokenu przy użyciu ujednoliconego formatu pamięci podręcznej. Ten format jest powszechny dla ADAL.NET 4. x i MSAL.NET 2. x, a inne MSALs tej samej generacji lub starszej na tej samej platformie. Zapoznaj się z poniższym kodem:

```csharp
string appLocation = Path.GetDirectoryName(Assembly.GetEntryAssembly().Location;
string cacheFolder = Path.GetFullPath(appLocation) + @"..\..\..\..");
string adalV3cacheFileName = Path.Combine(cacheFolder, "cacheAdalV3.bin");
string unifiedCacheFileName = Path.Combine(cacheFolder, "unifiedCache.bin");

IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
                                    .Build();
FilesBasedTokenCacheHelper.EnableSerialization(app.UserTokenCache,
                                               unifiedCacheFileName,
                                               adalV3cacheFileName);

```

Tym razem Klasa pomocnika wygląda następująco:

```csharp
using System;
using System.IO;
using System.Security.Cryptography;
using Microsoft.Identity.Client;

namespace CommonCacheMsalV3
{
 /// <summary>
 /// Simple persistent cache implementation of the dual cache serialization (ADAL V3 legacy
 /// and unified cache format) for a desktop applications (from MSAL 2.x)
 /// </summary>
 static class FilesBasedTokenCacheHelper
 {
  /// <summary>
  /// Get the user token cache
  /// </summary>
  /// <param name="adalV3CacheFileName">File name where the cache is serialized with the
  /// ADAL V3 token cache format. Can
  /// be <c>null</c> if you don't want to implement the legacy ADAL V3 token cache
  /// serialization in your MSAL 2.x+ application</param>
  /// <param name="unifiedCacheFileName">File name where the cache is serialized
  /// with the Unified cache format, common to
  /// ADAL V4 and MSAL V2 and above, and also across ADAL/MSAL on the same platform.
  ///  Should not be <c>null</c></param>
  /// <returns></returns>
  public static void EnableSerialization(ITokenCache cache, string unifiedCacheFileName, string adalV3CacheFileName)
  {
   UnifiedCacheFileName = unifiedCacheFileName;
   AdalV3CacheFileName = adalV3CacheFileName;

   cache.SetBeforeAccess(BeforeAccessNotification);
   cache.SetAfterAccess(AfterAccessNotification);
  }

  /// <summary>
  /// File path where the token cache is serialized with the unified cache format
  /// (ADAL.NET V4, MSAL.NET V3)
  /// </summary>
  public static string UnifiedCacheFileName { get; private set; }

  /// <summary>
  /// File path where the token cache is serialized with the legacy ADAL V3 format
  /// </summary>
  public static string AdalV3CacheFileName { get; private set; }

  private static readonly object FileLock = new object();

  public static void BeforeAccessNotification(TokenCacheNotificationArgs args)
  {
   lock (FileLock)
   {
    args.TokenCache.DeserializeAdalV3(ReadFromFileIfExists(AdalV3CacheFileName));
    try
    {
     args.TokenCache.DeserializeMsalV3(ReadFromFileIfExists(UnifiedCacheFileName));
    }
    catch(Exception ex)
    {
     // Compatibility with the MSAL v2 cache if you used one
     args.TokenCache.DeserializeMsalV2(ReadFromFileIfExists(UnifiedCacheFileName));
    }
   }
  }

  public static void AfterAccessNotification(TokenCacheNotificationArgs args)
  {
   // if the access operation resulted in a cache update
   if (args.HasStateChanged)
   {
    lock (FileLock)
    {
     WriteToFileIfNotNull(UnifiedCacheFileName, args.TokenCache.SerializeMsalV3());
     if (!string.IsNullOrWhiteSpace(AdalV3CacheFileName))
     {
      WriteToFileIfNotNull(AdalV3CacheFileName, args.TokenCache.SerializeAdalV3());
     }
    }
   }
  }

  /// <summary>
  /// Read the content of a file if it exists
  /// </summary>
  /// <param name="path">File path</param>
  /// <returns>Content of the file (in bytes)</returns>
  private static byte[] ReadFromFileIfExists(string path)
  {
   byte[] protectedBytes = (!string.IsNullOrEmpty(path) && File.Exists(path))
       ? File.ReadAllBytes(path) : null;
   byte[] unprotectedBytes = encrypt ?
       ((protectedBytes != null) ? ProtectedData.Unprotect(protectedBytes, null, DataProtectionScope.CurrentUser) : null)
       : protectedBytes;
   return unprotectedBytes;
  }

  /// <summary>
  /// Writes a blob of bytes to a file. If the blob is <c>null</c>, deletes the file
  /// </summary>
  /// <param name="path">path to the file to write</param>
  /// <param name="blob">Blob of bytes to write</param>
  private static void WriteToFileIfNotNull(string path, byte[] blob)
  {
   if (blob != null)
   {
    byte[] protectedBytes = encrypt
      ? ProtectedData.Protect(blob, null, DataProtectionScope.CurrentUser)
      : blob;
    File.WriteAllBytes(path, protectedBytes);
   }
   else
   {
    File.Delete(path);
   }
  }

  // Change if you want to test with an un-encrypted blob (this is a json format)
  private static bool encrypt = true;
 }
}
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Wywoływanie interfejsu API sieci Web z poziomu aplikacji klasycznej](scenario-desktop-call-api.md)
