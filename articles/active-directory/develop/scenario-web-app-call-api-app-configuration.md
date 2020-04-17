---
title: Konfigurowanie aplikacji sieci Web, która wywołuje internetowe interfejsy API — platforma tożsamości firmy Microsoft | Azure
description: Dowiedz się, jak skonfigurować kod aplikacji sieci Web, która wywołuje internetowe interfejsy API
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 7f05d33b43df85c49a0c92b60157e2a6448325ac
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537138"
---
# <a name="a-web-app-that-calls-web-apis-code-configuration"></a>Aplikacja internetowa, która wywołuje internetowe interfejsy API: konfiguracja kodu

Jak pokazano w [aplikacji sieci Web, która loguje się w](scenario-web-app-sign-user-overview.md) scenariuszu użytkowników, aplikacja sieci web używa przepływu kodu [autoryzacji OAuth 2.0](v2-oauth2-auth-code-flow.md) do logowania użytkownika. Ten przepływ ma dwa kroki:

1. Poproś o kod autoryzacji. Ta część deleguje prywatny dialog z użytkownikiem do platformy tożsamości firmy Microsoft. Podczas tego dialogu użytkownik loguje się i wyraża zgodę na korzystanie z internetowych interfejsów API. Po pomyślnym zakończeniu dialogu prywatnego aplikacja sieci web otrzymuje kod autoryzacji na jego przekierowanie identyfikatora URI.
1. Zażądaj tokenu dostępu dla interfejsu API, realizując kod autoryzacji.

[Aplikacja sieci Web, która loguje się w](scenario-web-app-sign-user-overview.md) scenariuszach użytkowników objęte tylko pierwszy krok. W tym miejscu dowiesz się, jak zmodyfikować aplikację sieci web, tak aby nie tylko loguje użytkowników, ale także teraz wywołuje internetowe interfejsy API.

## <a name="libraries-that-support-web-app-scenarios"></a>Biblioteki obsługujące scenariusze aplikacji internetowych

Następujące biblioteki w bibliotece uwierzytelniania firmy Microsoft (MSAL) obsługują przepływ kodu autoryzacji dla aplikacji sieci Web:

| Biblioteka MSAL | Opis |
|--------------|-------------|
| ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | Obsługa platform .NET Framework i .NET Core. Nie są obsługiwane są uniwersalne platformy systemu Windows (UWP), Xamarin.iOS i Xamarin.Android, ponieważ te platformy są używane do tworzenia publicznych aplikacji klienckich. |
| ![MĘTÓW MSAL](media/sample-v2-code/logo_python.png) <br/> Biblioteka MSAL dla języka Python | Obsługa aplikacji sieci Web języka Python. |
| ![MSAL Java](media/sample-v2-code/logo_java.png) <br/> Biblioteka MSAL dla języka Java | Obsługa aplikacji internetowych Java. |

Wybierz kartę dla interesującej Cię platformy:

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Ponieważ logowanie użytkownika jest delegowane do oprogramowania pośredniczącego Open ID connect (OIDC), należy wchodzić w interakcje z procesem OIDC. Sposób interakcji zależy od struktury, której używasz.

W przypadku ASP.NET Core zasubskrybuj zdarzenia OIDC oprogramowania pośredniczącego:

- Możesz pozwolić ASP.NET Core zażądać kodu autoryzacji za pomocą oprogramowania pośredniczącego Open ID Connect. ASP.NET lub ASP.NET Core pozwoli użytkownikowi zalogować się i wyrazić zgodę.
- Zasubskrybujesz aplikację internetową, aby otrzymać kod autoryzacji. Ta subskrypcja odbywa się przy użyciu delegata języka C#.
- Po odebraniu kodu autoryzacji użyjesz bibliotek MSAL, aby go zrealizować. Tokeny dostępu wynikowe i tokeny odświeżania są przechowywane w pamięci podręcznej tokenów. Pamięć podręczna może służyć w innych częściach aplikacji, takich jak kontrolery, do uzyskiwania innych tokenów po cichu.

Przykłady kodu w tym artykule i poniżej są wyodrębniane z [samouczka przyrostowego aplikacji sieci web ASP.NET Core, rozdział 2](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-1-Call-MSGraph). Aby uzyskać szczegółowe informacje na temat implementacji, można zapoznać się z tym samouczka.

> [!NOTE]
> Aby w pełni zrozumieć przykłady kodu tutaj, musisz znać [ASP.NET podstawowe podstawy,](https://docs.microsoft.com/aspnet/core/fundamentals)a w szczególności z [iniekcji zależności](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection) i [opcji.](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/options)

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

Ponieważ logowanie użytkownika jest delegowane do oprogramowania pośredniczącego Open ID connect (OIDC), należy wchodzić w interakcje z procesem OIDC. Sposób interakcji zależy od struktury, której używasz.

W przypadku ASP.NET zasubskrybujesz zdarzenia OIDC oprogramowania pośredniczącego:

- Możesz pozwolić ASP.NET Core zażądać kodu autoryzacji za pomocą oprogramowania pośredniczącego Open ID Connect. ASP.NET lub ASP.NET Core pozwoli użytkownikowi zalogować się i wyrazić zgodę.
- Zasubskrybujesz aplikację internetową, aby otrzymać kod autoryzacji. Ta subskrypcja odbywa się przy użyciu delegata języka C#.
- Po odebraniu kodu autoryzacji użyjesz bibliotek MSAL, aby go zrealizować. Tokeny dostępu wynikowe i tokeny odświeżania są przechowywane w pamięci podręcznej tokenów. Pamięć podręczna może służyć w innych częściach aplikacji, takich jak kontrolery, do uzyskiwania innych tokenów po cichu.

Przykłady kodu w tym artykule i następujące są wyodrębniane z [ASP.NET przykładu aplikacji sieci Web](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect). Można odwołać się do tego przykładu, aby uzyskać pełne szczegóły implementacji.

# <a name="java"></a>[Java](#tab/java)

Przykłady kodu w tym artykule i poniżej są wyodrębniane z [aplikacji sieci Web Java, która wywołuje Microsoft Graph](https://github.com/Azure-Samples/ms-identity-java-webapp), przykład aplikacji sieci web, która używa msal dla języka Java.
Próbka obecnie umożliwia MSAL dla języka Java produkcji adresu URL kodu autoryzacji i obsługuje nawigacji do punktu końcowego autoryzacji dla platformy tożsamości firmy Microsoft. Istnieje również możliwość użycia zabezpieczeń Sprint do logowania użytkownika. Aby uzyskać szczegółowe informacje na temat implementacji, można zapoznać się z przykładem.

# <a name="python"></a>[Python](#tab/python)

Przykłady kodu w tym artykule i poniżej są wyodrębniane z [aplikacji sieci web języka Python wywołującej microsoft graph](https://github.com/Azure-Samples/ms-identity-python-webapp), przykład aplikacji sieci web, która używa msal. Python.
Próbka obecnie pozwala MSAL. Python tworzy adres URL kodu autoryzacji i obsługuje nawigację do punktu końcowego autoryzacji dla platformy tożsamości firmy Microsoft. Aby uzyskać szczegółowe informacje na temat implementacji, można zapoznać się z przykładem.

---

## <a name="code-that-redeems-the-authorization-code"></a>Kod, który realizuje kod autoryzacji

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

### <a name="startupcs"></a>Startup.cs

W ASP.NET Core w `Startup.cs` pliku subskrybujesz zdarzenie `OnAuthorizationCodeReceived` OpenID Connect. Z tego zdarzenia wywołaj `AcquireTokenFromAuthorizationCode` metodę MSAL.NET. Ta metoda przechowuje następujące tokeny w pamięci podręcznej tokenu:

- *Token dostępu* dla żądanego `scopes`.
- *Token odświeżania*. Ten token będzie używany do odświeżania tokenu dostępu, gdy jest blisko wygaśnięcia lub aby uzyskać inny token w imieniu tego samego użytkownika, ale dla innego zasobu.

[Samouczek aplikacji ASP.NET Core Web](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2) app zapewnia kod wielokrotnego użytku dla aplikacji sieci web.

Poniżej znajduje się kod z [Startup.cs#L40-L42](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/2-WebApp-graph-user/2-1-Call-MSGraph/Startup.cs#L40-L42). Zawiera połączenia do:

- Metoda, `AddMicrosoftIdentityPlatformAuthentication` która dodaje uwierzytelnianie do aplikacji sieci web.
- Metoda, `AddMsal` która dodaje możliwość wywoływania interfejsów API sieci web.
- Metoda, `AddInMemoryTokenCaches` która polega na wyborze implementacji pamięci podręcznej tokenu.

```csharp
public class Startup
{
  // Code not shown here

  public void ConfigureServices(IServiceCollection services)
  {
      // Token acquisition service based on MSAL.NET
      // and chosen token-cache implementation
      services.AddMicrosoftIdentityPlatformAuthentication(Configuration)
          .AddMsal(Configuration, new string[] { Constants.ScopeUserRead })
          .AddInMemoryTokenCaches();
  }

  // Code not shown here
}
```

`Constants.ScopeUserRead`jest zdefiniowany w [constants.cs#L5](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/2-WebApp-graph-user/2-1-Call-MSGraph/Infrastructure/Constants.cs#L5):

```csharp
public static class Constants
{
    public const string ScopeUserRead = "User.Read";
}
```

Przeanalizowałeś już zawartość `AddMicrosoftIdentityPlatformAuthentication` w [aplikacji sieci Web, która loguje się do użytkowników — konfiguracja kodu](scenario-web-app-sign-user-app-configuration.md?tabs=aspnetcore#initialization-code).

### <a name="the-addmsal-method"></a>Metoda AddMsal

Kod znajduje `AddMsal` się w witrynie [Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L108-L159](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L108-L159).

```csharp

/// <summary>
/// Extensions for IServiceCollection for startup initialization
/// </summary>
public static class WebAppServiceCollectionExtensions
{
  // Code omitted here

  /// <summary>
  /// Add MSAL support to the web app or web API.
  /// </summary>
  /// <param name="services">Service collection to which to add authentication</param>
  /// <param name="initialScopes">Initial scopes to request at sign-in</param>
  /// <returns></returns>
  public static IServiceCollection AddMsal(this IServiceCollection services, IConfiguration configuration, IEnumerable<string> initialScopes, string configSectionName = "AzureAd")
  {
      // Ensure that configuration options for MSAL.NET, HttpContext accessor and the Token acquisition service
      // (encapsulating MSAL.NET) are available through dependency injection.
      services.Configure<ConfidentialClientApplicationOptions>(options => configuration.Bind(configSectionName, options));
      services.AddHttpContextAccessor();
      services.AddTokenAcquisition();

      services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
      {
          // Response type
          options.ResponseType = OpenIdConnectResponseType.CodeIdToken;

          // This scope is needed to get a refresh token when users sign in by using their personal Microsoft accounts.
          // (It's required by MSAL.NET and automatically provided when users sign in by using work or school accounts.)
          options.Scope.Add("offline_access");
          if (initialScopes != null)
          {
              foreach (string scope in initialScopes)
              {
                  if (!options.Scope.Contains(scope))
                  {
                      options.Scope.Add(scope);
                  }
              }
          }

          // Handle the auth redemption by MSAL.NET so that a token is available in the token cache,
          // where it will be usable from controllers later (by means of the TokenAcquisition service).
          var handler = options.Events.OnAuthorizationCodeReceived;
          options.Events.OnAuthorizationCodeReceived = async context =>
          {
              var tokenAcquisition = context.HttpContext.RequestServices.GetRequiredService<ITokenAcquisition>();
              await tokenAcquisition.AddAccountToCacheFromAuthorizationCodeAsync(context, options.Scope).ConfigureAwait(false);
              await handler(context).ConfigureAwait(false);
          };
      });
      return services;
  }
}
```

Metoda `AddMsal` zapewnia, że:

- Aplikacja sieci Web ASP.NET Core żąda zarówno tokenu identyfikatora użytkownika, jak i kodu uwierzytelniania (`options.ResponseType = OpenIdConnectResponseType.CodeIdToken`).
- Zakres `offline_access` jest dodawany. Ten zakres uzyskuje zgodę użytkownika dla aplikacji, aby uzyskać token odświeżania.
- Aplikacja subskrybuje zdarzenie OIDC `OnAuthorizationCodeReceived` i realizuje połączenie za pomocą MSAL.NET, który jest hermetyzowany w tym `ITokenAcquisition`miejscu w składniku wielokrotnego użycia implementując .

### <a name="the-tokenacquisitionaddaccounttocachefromauthorizationcodeasync-method"></a>Metoda TokenAcquisition.AddAccountToCacheFromAuthorizationCodeAsync

Metoda `TokenAcquisition.AddAccountToCacheFromAuthorizationCodeAsync` znajduje się w witrynie [Microsoft.Identity.Web/TokenAcquisition.cs#L101-L145](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/4b12ba02e73f62e3e3137f5f4b9ef43cec7c14fd/Microsoft.Identity.Web/TokenAcquisition.cs#L101-L145). Zapewnia ona, że:

- ASP.NET nie próbuje zrealizować kodu uwierzytelniacego równolegle do`context.HandleCodeRedemption();`MSAL.NET ( ).
- Oświadczenia w tokenie identyfikatora są dostępne dla msal obliczyć klucz pamięci podręcznej tokenu dla konta użytkownika.
- W razie potrzeby zostanie utworzone wystąpienie aplikacji MSAL.NET.
- Kod jest realizowany przez aplikację MSAL.NET.
- Nowy token identyfikatora jest udostępniany ASP.NET Core `context.HandleCodeRedemption(null, result.IdToken);`podczas wywołania . Token dostępu nie jest współużytkowana z ASP.NET Core. Pozostaje w pamięci podręcznej tokenu MSAL.NET skojarzony z użytkownikiem, gdzie jest gotowy do użycia w ASP.NET kontrolerów Core.

Oto odpowiedni kod dla: `TokenAcquisition`

```csharp
public class TokenAcquisition : ITokenAcquisition
{
  string[] scopesRequestedByMsalNet = new string[]{ "openid", "profile", "offline_access" };

  // Code omitted here for clarity


  public async Task AddAccountToCacheFromAuthorizationCodeAsync(AuthorizationCodeReceivedContext context, IEnumerable<string> scopes)
  {
   // Code omitted here for clarity

    try
    {
      // Because AcquireTokenByAuthorizationCodeAsync is asynchronous, we tell ASP.NET core that we're handing the code
      // even if it's not done yet, so that it doesn't concurrently call the token endpoint. Otherwise, there will be a
      // race condition that causes an Azure AD error message ("code already redeemed").
      context.HandleCodeRedemption();

      // The cache needs the claims from the ID token.
      // If they're not yet in the HttpContext.User's claims, add them here.
      if (!context.HttpContext.User.Claims.Any())
      {
          (context.HttpContext.User.Identity as ClaimsIdentity).AddClaims(context.Principal.Claims);
      }

      var application = GetOrBuildConfidentialClientApplication();

      // Don't share the access token with ASP.NET Core. If we share it, ASP.NET will cache it and won't send the OAuth 2.0 request if
      // a further call to AcquireTokenByAuthorizationCodeAsync is required later for incremental consent (getting a code requesting more scopes).
      // Do share the ID token, however.
      var result = await application
          .AcquireTokenByAuthorizationCode(scopes.Except(_scopesRequestedByMsalNet), context.ProtocolMessage.Code)
          .ExecuteAsync()
          .ConfigureAwait(false);

      context.HandleCodeRedemption(null, result.IdToken);
  }
  catch (MsalException ex)
  {
      Debug.WriteLine(ex.Message);
      throw;
  }
 }
```

### <a name="the-tokenacquisitionbuildconfidentialclientapplication-method"></a>Metoda tokenacquisition.BuildConfidentialClientApplication

W ASP.NET Core tworzenie poufnej aplikacji klienckiej wykorzystuje informacje `HttpContext`zawarte w pliku . Skojarzone `HttpContext` z żądaniem jest dostępny `CurrentHttpContext` przy użyciu właściwości. `HttpContext`posiada informacje o adresie URL aplikacji internetowej i o `ClaimsPrincipal`zalogowanym użytkowniku (w ).

Metoda `BuildConfidentialClientApplication` używa również ASP.NET konfiguracji Core. Konfiguracja ma sekcję "AzureAD", a także jest powiązana z obu następujących elementów:

- Struktura `_applicationOptions` danych typu [ConfidentialClientApplicationOptions](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.confidentialclientapplicationoptions?view=azure-dotnet).
- Wystąpienie `azureAdOptions` typu [AzureAdOptions](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/AzureADOptions.cs), zdefiniowane w `Authentication.AzureAD.UI`ASP.NET Core .

Na koniec aplikacja musi obsługiwać pamięci podręczne tokenów. Więcej informacji na ten temat dowiesz się w następnej sekcji.

Kod `GetOrBuildConfidentialClientApplication()` tej metody znajduje się w pliku [Microsoft.Identity.Web/TokenAcquisition.cs#L290-L333](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/4b12ba02e73f62e3e3137f5f4b9ef43cec7c14fd/Microsoft.Identity.Web/TokenAcquisition.cs#L290-L333). Używa elementów członkowskich, które zostały wstrzyknięte przez `TokenAcquisition` iniekcję zależności (przekazywane w konstruktorze w [microsoft.Identity.Web/TokenAcquisition.cs#L47-L59](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/4b12ba02e73f62e3e3137f5f4b9ef43cec7c14fd/Microsoft.Identity.Web/TokenAcquisition.cs#L47-L59)).

Oto kod dla: `GetOrBuildConfidentialClientApplication`

```csharp
public class TokenAcquisition : ITokenAcquisition
{
  // Code omitted here for clarity

  // Members
  private IConfidentialClientApplication application;
  private HttpContext CurrentHttpContext => _httpContextAccessor.HttpContext;

  // The following members are set by dependency injection in the TokenAcquisition constructor.
  private readonly AzureADOptions _azureAdOptions;
  private readonly ConfidentialClientApplicationOptions _applicationOptions;
  private readonly IMsalAppTokenCacheProvider _appTokenCacheProvider;
  private readonly IMsalUserTokenCacheProvider _userTokenCacheProvider;
  private readonly IHttpContextAccessor _httpContextAccessor;

  /// <summary>
  /// Creates an MSAL confidential client application, if needed.
  /// </summary>
  private IConfidentialClientApplication GetOrBuildConfidentialClientApplication()
  {
    if (application == null)
    {
        application = BuildConfidentialClientApplication();
    }
    return application;
  }

  /// <summary>
  /// Creates an MSAL Confidential client application
  /// </summary>
  /// <param name="claimsPrincipal"></param>
  /// <returns></returns>
  private IConfidentialClientApplication BuildConfidentialClientApplication()
  {
    var request = CurrentHttpContext.Request;
    var azureAdOptions = _azureAdOptions;
    var applicationOptions = _applicationOptions;
    string currentUri = UriHelper.BuildAbsolute(
        request.Scheme,
        request.Host,
        request.PathBase,
        azureAdOptions.CallbackPath ?? string.Empty);

    string authority = $"{applicationOptions.Instance}{applicationOptions.TenantId}/";

    var app = ConfidentialClientApplicationBuilder
        .CreateWithApplicationOptions(applicationOptions)
        .WithRedirectUri(currentUri)
        .WithAuthority(authority)
        .Build();

    // Initialize token cache providers
    _appTokenCacheProvider?.InitializeAsync(app.AppTokenCache);
    _userTokenCacheProvider?.InitializeAsync(app.UserTokenCache);

    return app;
  }

```

### <a name="summary"></a>Podsumowanie

`AcquireTokenByAuthorizationCode`jest naprawdę metoda, która realizuje kod autoryzacji, który ASP.NET żądań i pobiera tokeny, które są dodawane do MSAL.NET pamięci podręcznej tokenu użytkownika. Z pamięci podręcznej tokeny są następnie używane w ASP.NET kontrolerów Core.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

ASP.NET obsługuje rzeczy podobnie jak ASP.NET Core, z tą różnicą, że konfiguracja `OnAuthorizationCodeReceived` OpenID Connect i subskrypcja zdarzenia odbywa się w pliku [App_Start\Startup.Auth.cs.](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/App_Start/Startup.Auth.cs) Pojęcia są również podobne do tych w ASP.NET Core, z tą różnicą, że w ASP.NET należy określić `RedirectUri` w [Web.config#L15](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/master/WebApp/Web.config#L15). Ta konfiguracja jest nieco mniej niezawodne niż w ASP.NET Core, ponieważ należy ją zmienić podczas wdrażania aplikacji.

Oto kod dla Startup.Auth.cs:

```csharp
public partial class Startup
{
  public void ConfigureAuth(IAppBuilder app)
  {
    app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

    app.UseCookieAuthentication(new CookieAuthenticationOptions());

    // Custom middleware initialization. This is activated when the code obtained from a code_grant is present in the query string (&code=<code>).
    app.UseOAuth2CodeRedeemer(
        new OAuth2CodeRedeemerOptions
        {
            ClientId = AuthenticationConfig.ClientId,
            ClientSecret = AuthenticationConfig.ClientSecret,
            RedirectUri = AuthenticationConfig.RedirectUri
        }
      );

  app.UseOpenIdConnectAuthentication(
      new OpenIdConnectAuthenticationOptions
      {
        // The `Authority` represents the v2.0 endpoint - https://login.microsoftonline.com/common/v2.0.
        Authority = AuthenticationConfig.Authority,
        ClientId = AuthenticationConfig.ClientId,
        RedirectUri = AuthenticationConfig.RedirectUri,
        PostLogoutRedirectUri = AuthenticationConfig.RedirectUri,
        Scope = AuthenticationConfig.BasicSignInScopes + " Mail.Read", // A basic set of permissions for user sign-in and profile access "openid profile offline_access"
        TokenValidationParameters = new TokenValidationParameters
        {
            ValidateIssuer = false,
            // In a real application, you would use IssuerValidator for additional checks, such as making sure the user's organization has signed up for your app.
            //     IssuerValidator = (issuer, token, tvp) =>
            //     {
            //        //if(MyCustomTenantValidation(issuer))
            //        return issuer;
            //        //else
            //        //    throw new SecurityTokenInvalidIssuerException("Invalid issuer");
            //    },
            //NameClaimType = "name",
        },
        Notifications = new OpenIdConnectAuthenticationNotifications()
        {
            AuthorizationCodeReceived = OnAuthorizationCodeReceived,
            AuthenticationFailed = OnAuthenticationFailed,
        }
      });
  }

  private async Task OnAuthorizationCodeReceived(AuthorizationCodeReceivedNotification context)
  {
      // Upon successful sign-in, get the access token and cache it by using MSAL.
      IConfidentialClientApplication clientApp = MsalAppBuilder.BuildConfidentialClientApplication(new ClaimsPrincipal(context.AuthenticationTicket.Identity));
      AuthenticationResult result = await clientApp.AcquireTokenByAuthorizationCode(new[] { "Mail.Read" }, context.Code).ExecuteAsync();
  }

  private Task OnAuthenticationFailed(AuthenticationFailedNotification<OpenIdConnectMessage, OpenIdConnectAuthenticationOptions> notification)
  {
      notification.HandleResponse();
      notification.Response.Redirect("/Error?message=" + notification.Exception.Message);
      return Task.FromResult(0);
  }
}
```

# <a name="java"></a>[Java](#tab/java)

Zobacz [aplikacji sieci Web, która loguje się do użytkowników: Konfiguracja kodu,](scenario-web-app-sign-user-app-configuration.md?tabs=java#initialization-code) aby zrozumieć, jak próbka Java pobiera kod autoryzacji. Po otrzymaniu kodu aplikacja [AuthFilter.java#L51-L56](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthFilter.java#L51-L56):

1. Delegaci `AuthHelper.processAuthenticationCodeRedirect` do metody w [AuthHelper.java#L67-L97](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthHelper.java#L67-L97).
1. Połączenia `getAuthResultByAuthCode`.

```Java
class AuthHelper {
  // Code omitted
  void processAuthenticationCodeRedirect(HttpServletRequest httpRequest, String currentUri, String fullUrl)
            throws Throwable {

  // Code omitted
  AuthenticationResponse authResponse = AuthenticationResponseParser.parse(new URI(fullUrl), params);

  // Code omitted
  IAuthenticationResult result = getAuthResultByAuthCode(
                    httpRequest,
                    oidcResponse.getAuthorizationCode(),
                    currentUri);

// Code omitted
  }
}
```

Metoda `getAuthResultByAuthCode` jest zdefiniowana w [pliku AuthHelper.java#L176](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthHelper.java#L176). Tworzy MSAL `ConfidentialClientApplication`, a `acquireToken()` następnie `AuthorizationCodeParameters` wywołuje z utworzone na podstawie kodu autoryzacji.

```Java
   private IAuthenticationResult getAuthResultByAuthCode(
            HttpServletRequest httpServletRequest,
            AuthorizationCode authorizationCode,
            String currentUri) throws Throwable {

        IAuthenticationResult result;
        ConfidentialClientApplication app;
        try {
            app = createClientApplication();

            String authCode = authorizationCode.getValue();
            AuthorizationCodeParameters parameters = AuthorizationCodeParameters.builder(
                    authCode,
                    new URI(currentUri)).
                    build();

            Future<IAuthenticationResult> future = app.acquireToken(parameters);

            result = future.get();
        } catch (ExecutionException e) {
            throw e.getCause();
        }

        if (result == null) {
            throw new ServiceUnavailableException("authentication result was null");
        }

        SessionManagementHelper.storeTokenCacheInSession(httpServletRequest, app.tokenCache().serialize());

        return result;
    }

    private ConfidentialClientApplication createClientApplication() throws MalformedURLException {
        return ConfidentialClientApplication.builder(clientId, ClientCredentialFactory.create(clientSecret)).
                authority(authority).
                build();
    }
```

# <a name="python"></a>[Python](#tab/python)

Przepływ kodu autoryzacji jest wymagany, jak pokazano w [aplikacji sieci Web, która loguje się w użytkowników: Konfiguracja kodu](scenario-web-app-sign-user-app-configuration.md?tabs=python#initialization-code). Kod jest następnie odbierany w `authorized` funkcji, która `/getAToken` Flask trasy z adresu URL. Pełny kontekst tego kodu można znaleźć na [platformie app.py#L30-L44:](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L30-L44)

```python
 @app.route("/getAToken")  # Its absolute URL must match your app's redirect_uri set in AAD.
def authorized():
    if request.args['state'] != session.get("state"):
        return redirect(url_for("login"))
    cache = _load_cache()
    result = _build_msal_app(cache).acquire_token_by_authorization_code(
        request.args['code'],
        scopes=app_config.SCOPE,  # Misspelled scope would cause an HTTP 400 error here.
        redirect_uri=url_for("authorized", _external=True))
    if "error" in result:
        return "Login failure: %s, %s" % (
            result["error"], result.get("error_description"))
    session["user"] = result.get("id_token_claims")
    _save_cache(cache)
    return redirect(url_for("index"))
```

---

Zamiast klucza tajnego klienta poufne aplikacji klienta można również udowodnić swoją tożsamość przy użyciu certyfikatu klienta lub potwierdzenia klienta.
Użycie potwierdzeń klienta jest scenariuszem zaawansowanym, szczegółowo opisanym w [potwierdzeniach klienta](msal-net-client-assertions.md).

## <a name="token-cache"></a>Pamięć podręczna tokenów

> [!IMPORTANT]
> Implementacja pamięci podręcznej tokenu dla aplikacji sieci web lub internetowych interfejsów API różni się od implementacji dla aplikacji klasycznych, która często jest [oparta na plikach.](scenario-desktop-acquire-token.md#file-based-token-cache)
> Ze względów bezpieczeństwa i wydajności należy upewnić się, że w przypadku aplikacji sieci web i internetowych interfejsów API istnieje jedna pamięć podręczna tokenu na konto użytkownika. Należy serializować pamięć podręczną tokenu dla każdego konta.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Samouczek ASP.NET core używa iniekcji zależności, aby umożliwić podjęcie decyzji o implementacji pamięci podręcznej tokenu w pliku Startup.cs dla aplikacji. Microsoft.Identity.Web jest dostarczany z wbudowanymi serializatorami pamięci tokenu opisanymi w [serializacji pamięci podręcznej tokenu.](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/Microsoft.Identity.Web/README.md#token-cache-serialization) Ciekawą możliwością jest wybór ASP.NET [rdzenia rozproszonych pamięci podręcznych:](https://docs.microsoft.com/aspnet/core/performance/caching/distributed#distributed-memory-cache)

```csharp
// Use a distributed token cache by adding:
    services.AddMicrosoftIdentityPlatformAuthentication(Configuration)
            .AddMsal(new string[] { scopesToRequest })
            .AddDistributedTokenCaches();

// Then, choose your implementation.

// For instance, the distributed in-memory cache (not cleared when you stop the app):
services.AddDistributedMemoryCache()

// Or a Redis cache:
services.AddStackExchangeRedisCache(options =>
{
 options.Configuration = "localhost";
 options.InstanceName = "SampleInstance";
});

// Or even a SQL Server token cache:
services.AddDistributedSqlServerCache(options =>
{
 options.ConnectionString = _config["DistCache_ConnectionString"];
 options.SchemaName = "dbo";
 options.TableName = "TestCache";
});
```

Aby uzyskać szczegółowe informacje na temat dostawców pamięci podręcznej tokenów, zobacz także [ASP.NET samouczki dotyczące aplikacji Core Web | Faza buforowania tokenu](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-2-TokenCache) samouczka.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

Implementacja pamięci podręcznej tokenu dla aplikacji sieci web lub internetowych interfejsów API różni się od implementacji dla aplikacji klasycznych, która często jest [oparta na plikach.](scenario-desktop-acquire-token.md#file-based-token-cache)

Implementacja aplikacji sieci web można użyć ASP.NET sesji lub pamięci serwera. Na przykład zobacz, jak implementacja pamięci podręcznej jest podłączona po utworzeniu aplikacji MSAL.NET w [msalAppBuilder.cs#L39-L51:](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Utils/MsalAppBuilder.cs#L39-L51)

```csharp
public static class MsalAppBuilder
{
 // Omitted code
    public static IConfidentialClientApplication BuildConfidentialClientApplication(ClaimsPrincipal currentUser)
    {
      IConfidentialClientApplication clientapp = ConfidentialClientApplicationBuilder.Create(AuthenticationConfig.ClientId)
            .WithClientSecret(AuthenticationConfig.ClientSecret)
            .WithRedirectUri(AuthenticationConfig.RedirectUri)
            .WithAuthority(new Uri(AuthenticationConfig.Authority))
            .Build();

      // After the ConfidentialClientApplication is created, we overwrite its default UserTokenCache with our implementation.
      MSALPerUserMemoryTokenCache userTokenCache = new MSALPerUserMemoryTokenCache(clientapp.UserTokenCache, currentUser ?? ClaimsPrincipal.Current);

      return clientapp;
  }
```

# <a name="java"></a>[Java](#tab/java)

MSAL Java udostępnia metody serializacji i deserializacji pamięci podręcznej tokenów. Przykład języka Java obsługuje serializacji z sesji, `getAuthResultBySilentFlow` jak pokazano w metodzie w [AuthHelper.java#L99-L122:](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthHelper.java#L99-L122)

```Java
IAuthenticationResult getAuthResultBySilentFlow(HttpServletRequest httpRequest, HttpServletResponse httpResponse)
      throws Throwable {

  IAuthenticationResult result =  SessionManagementHelper.getAuthSessionObject(httpRequest);

  IConfidentialClientApplication app = createClientApplication();

  Object tokenCache = httpRequest.getSession().getAttribute("token_cache");
  if (tokenCache != null) {
      app.tokenCache().deserialize(tokenCache.toString());
  }

  SilentParameters parameters = SilentParameters.builder(
          Collections.singleton("User.Read"),
          result.account()).build();

  CompletableFuture<IAuthenticationResult> future = app.acquireTokenSilently(parameters);
  IAuthenticationResult updatedResult = future.get();

  // Update session with latest token cache.
  SessionManagementHelper.storeTokenCacheInSession(httpRequest, app.tokenCache().serialize());

  return updatedResult;
}
```

Szczegóły klasy `SessionManagementHelper` znajduje się w [przykładzie MSAL dla języka Java](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/SessionManagementHelper.java).

# <a name="python"></a>[Python](#tab/python)

W przykładzie języka Python jedna pamięć podręczna na konto jest zapewniona przez odtworzenie poufnej aplikacji klienckiej dla każdego żądania, a następnie serialowanie jej w pamięci podręcznej sesji Flask:

```python
from flask import Flask, render_template, session, request, redirect, url_for
from flask_session import Session  # https://pythonhosted.org/Flask-Session
import msal
import app_config


app = Flask(__name__)
app.config.from_object(app_config)
Session(app)

# Code omitted here for simplicity

def _load_cache():
    cache = msal.SerializableTokenCache()
    if session.get("token_cache"):
        cache.deserialize(session["token_cache"])
    return cache

def _save_cache(cache):
    if cache.has_state_changed:
        session["token_cache"] = cache.serialize()

def _build_msal_app(cache=None):
    return msal.ConfidentialClientApplication(
        app_config.CLIENT_ID, authority=app_config.AUTHORITY,
        client_credential=app_config.CLIENT_SECRET, token_cache=cache)
```

---

## <a name="next-steps"></a>Następne kroki

W tym momencie, gdy użytkownik zaloguje się, token jest przechowywany w pamięci podręcznej tokenu. Zobaczmy, jak jest następnie używany w innych częściach aplikacji internetowej.

> [!div class="nextstepaction"]
> [Aplikacja internetowa, która wywołuje internetowe interfejsy API: usuwanie kont z pamięci podręcznej podczas globalnego wylogowywania](scenario-web-app-call-api-sign-in.md)
