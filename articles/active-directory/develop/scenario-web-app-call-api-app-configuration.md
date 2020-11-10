---
title: Konfigurowanie aplikacji sieci Web, która wywołuje interfejsy API sieci Web | Azure
titleSuffix: Microsoft identity platform
description: Dowiedz się, jak skonfigurować kod aplikacji sieci Web, która wywołuje interfejsy API sieci Web
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/25/2020
ms.author: jmprieur
ms.custom: aaddev, devx-track-python
ms.openlocfilehash: b24b95423adb271b8a4016430e7d2b381c386cd2
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/10/2020
ms.locfileid: "94443759"
---
# <a name="a-web-app-that-calls-web-apis-code-configuration"></a>Aplikacja sieci Web, która wywołuje interfejsy API sieci Web: Konfiguracja kodu

Jak pokazano w [aplikacji sieci Web, która loguje się w](scenario-web-app-sign-user-overview.md) scenariuszu użytkowników, aplikacja sieci Web używa [przepływu kodu autoryzacji OAuth 2,0](v2-oauth2-auth-code-flow.md) do podpisania użytkownika w programie. Ten przepływ ma dwie czynności:

1. Zażądaj kodu autoryzacji. Ta część deleguje prywatny dialog z użytkownikiem do platformy tożsamości firmy Microsoft. Podczas tego dialogu użytkownik loguje się i wyraża zgodę na korzystanie z interfejsów API sieci Web. Po pomyślnym zakończeniu dialogu prywatnego aplikacja internetowa otrzymuje kod autoryzacji w identyfikatorze URI przekierowania.
1. Zażądaj tokenu dostępu dla interfejsu API przez zrealizowanie kodu autoryzacji.

[Aplikacja sieci Web, która loguje się do scenariuszy użytkowników,](scenario-web-app-sign-user-overview.md) dotyczyła tylko pierwszego kroku. Tutaj dowiesz się, jak zmodyfikować aplikację sieci Web tak, aby nie tylko oznakować użytkowników w programie, ale również teraz wywołuje interfejsy API sieci Web.

## <a name="libraries-that-support-web-app-scenarios"></a>Biblioteki obsługujące scenariusze aplikacji sieci Web

Następujące biblioteki w bibliotece Microsoft Authentication Library (MSAL) obsługują przepływ kodu autoryzacji dla aplikacji sieci Web:

| Biblioteka MSAL | Opis |
|--------------|-------------|
| ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | Obsługa platform .NET Framework i .NET Core. Nieobsługiwane są platforma uniwersalna systemu Windows (platformy UWP), Xamarin. iOS i Xamarin. Android, ponieważ te platformy są używane do tworzenia publicznych aplikacji klienckich. <br/><br/>W przypadku ASP.NET Core aplikacji sieci Web i interfejsów API sieci Web MSAL.NET jest hermetyzowana w bibliotece wyższego poziomu o nazwie [Microsoft. Identity. Web](https://aka.ms/ms-identity-web). |
| ![MSAL Python](media/sample-v2-code/logo_python.png) <br/> Biblioteka MSAL dla języka Python | Obsługa aplikacji sieci Web w języku Python. |
| ![MSAL Java](media/sample-v2-code/logo_java.png) <br/> Biblioteka MSAL dla języka Java | Obsługa aplikacji sieci Web w języku Java. |

Wybierz kartę dla interesującej platformy:

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

## <a name="client-secrets-or-client-certificates"></a>Klucze tajne klienta lub certyfikaty klienta

Ponieważ aplikacja sieci Web wywołuje teraz podrzędny interfejs API sieci Web, należy podać klucz tajny klienta lub certyfikat klienta w *appsettings.js* pliku. Możesz również dodać sekcję, która określa:

- Adres URL podrzędnego interfejsu API sieci Web
- Zakresy wymagane do wywoływania interfejsu API

W poniższym przykładzie `GraphBeta` sekcja określa te ustawienia.

```JSON
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "ClientId": "[Client_id-of-web-app-eg-2ec40e65-ba09-4853-bcde-bcb60029e596]",
    "TenantId": "common"

   // To call an API
   "ClientSecret": "[Copy the client secret added to the app from the Azure portal]",
   "ClientCertificates": [
  ]
 },
 "GraphBeta": {
    "BaseUrl": "https://graph.microsoft.com/beta",
    "Scopes": "user.read"
    }
}
```

Zamiast klucza tajnego klienta można podać certyfikat klienta. Poniższy fragment kodu przedstawia użycie certyfikatu przechowywanego w Azure Key Vault.

```JSON
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "ClientId": "[Client_id-of-web-app-eg-2ec40e65-ba09-4853-bcde-bcb60029e596]",
    "TenantId": "common"

   // To call an API
   "ClientCertificates": [
      {
        "SourceType": "KeyVault",
        "KeyVaultUrl": "https://msidentitywebsamples.vault.azure.net",
        "KeyVaultCertificateName": "MicrosoftIdentitySamplesCert"
      }
   ]
  },
  "GraphBeta": {
    "BaseUrl": "https://graph.microsoft.com/beta",
    "Scopes": "user.read"
  }
}
```

*Microsoft. Identity. Web* oferuje kilka sposobów opisywania certyfikatów, zarówno według konfiguracji, jak i kodu. Aby uzyskać szczegółowe informacje, zobacz [Microsoft. Identity. Web — używanie certyfikatów](https://github.com/AzureAD/microsoft-identity-web/wiki/Using-certificates) w serwisie GitHub.

## <a name="startupcs"></a>Startup.cs

Aplikacja sieci Web będzie musiała uzyskać token dla podrzędnego interfejsu API. Należy ją określić, dodając `.EnableTokenAcquisitionToCallDownstreamApi()` wiersz po `.AddMicrosoftIdentityWebApi(Configuration)` . Ten wiersz `ITokenAcquisition` przedstawia usługę, której można użyć na stronie kontroler i akcje strony. Jednak jak widać w poniższych dwóch opcjach, można to zrobić dokładniej. Należy również wybrać implementację pamięci podręcznej tokenów, na przykład `.AddInMemoryTokenCaches()` w *Startup.cs* :

   ```csharp
   using Microsoft.Identity.Web;

   public class Startup
   {
     // ...
     public void ConfigureServices(IServiceCollection services)
     {
     // ...
     services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
             .AddMicrosoftIdentityWebApp(Configuration, Configuration.GetSection("AzureAd"))
               .EnableTokenAcquisitionToCallDownstreamApi(new string[]{"user.read" })
               .AddInMemoryTokenCaches();
      // ...
     }
     // ...
   }
   ```

Zakresy przenoszone do `EnableTokenAcquisitionToCallDownstreamApi` są opcjonalne i umożliwiają aplikacji sieci Web zażądać zakresów i zgody użytkownika na te zakresy podczas logowania. Jeśli nie określisz zakresów, *firma Microsoft. Identity. Web* umożliwi korzystanie ze stopniowej zgody.

Jeśli nie chcesz samodzielnie uzyskać tokenu, *firma Microsoft. Identity. Web* udostępnia dwa mechanizmy wywoływania internetowego interfejsu API z aplikacji sieci Web. Wybrana opcja zależy od tego, czy chcesz wywołać Microsoft Graph lub inny interfejs API.

### <a name="option-1-call-microsoft-graph"></a>Opcja 1: Wywołaj Microsoft Graph

Jeśli chcesz wywołać Microsoft Graph, *Microsoft. Identity. Web* umożliwia bezpośrednio korzystanie z programu `GraphServiceClient` (dostępnego w Microsoft Graph SDK) w akcjach interfejsu API. Aby uwidocznić Microsoft Graph:

1. Dodaj pakiet NuGet [Microsoft. Identity. Web. MicrosoftGraph](https://www.nuget.org/packages/Microsoft.Identity.Web.MicrosoftGraph) do projektu.
1. Dodaj `.AddMicrosoftGraph()` po `.EnableTokenAcquisitionToCallDownstreamApi()` pliku *Startup.cs* . `.AddMicrosoftGraph()` ma kilka zastąpień. Użycie przesłonięcia, które przyjmuje sekcję konfiguracyjną jako parametr, staje się następujące:

   ```csharp
   using Microsoft.Identity.Web;

   public class Startup
   {
     // ...
     public void ConfigureServices(IServiceCollection services)
     {
     // ...
     services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
             .AddMicrosoftIdentityWebApp(Configuration, Configuration.GetSection("AzureAd"))
               .EnableTokenAcquisitionToCallDownstreamApi(new string[]{"user.read" })
                  .AddMicrosoftGraph(Configuration.GetSection("GraphBeta"))
               .AddInMemoryTokenCaches();
      // ...
     }
     // ...
   }
   ```

### <a name="option-2-call-a-downstream-web-api-other-than-microsoft-graph"></a>Opcja 2: wywoływanie podrzędnego interfejsu API sieci Web innego niż Microsoft Graph

Aby wywołać internetowy interfejs API inny niż Microsoft Graph, *Microsoft. Identity. Web* udostępnia `.AddDownstreamWebApi()` , który żąda tokenów i wywołuje podrzędny interfejs API sieci Web.

   ```csharp
   using Microsoft.Identity.Web;

   public class Startup
   {
     // ...
     public void ConfigureServices(IServiceCollection services)
     {
     // ...
     services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
             .AddMicrosoftIdentityWebApp(Configuration, "AzureAd")
               .EnableTokenAcquisitionToCallDownstreamApi(new string[]{"user.read" })
                  .AddDownstreamWebApi("MyApi", Configuration.GetSection("GraphBeta"))
               .AddInMemoryTokenCaches();
      // ...
     }
     // ...
   }
   ```

### <a name="summary"></a>Podsumowanie

Podobnie jak w przypadku interfejsów API sieci Web, można wybrać różne implementacje pamięci podręcznej tokenów. Aby uzyskać szczegółowe informacje, zobacz [Microsoft. Identity. Web-Serialization](https://aka.ms/ms-id-web/token-cache-serialization) Detailing w serwisie GitHub.

Na poniższej ilustracji przedstawiono różne możliwości *Microsoft. Identity. Web* i ich wpływ na plik *Startup.cs* :

:::image type="content" source="media/scenarios/microsoft-identity-web-startup-cs.svg" alt-text="Diagram blokowy przedstawiający opcje konfiguracji usługi w kropce startowej C S na potrzeby wywoływania internetowego interfejsu API i określania implementacji pamięci podręcznej token":::

> [!NOTE]
> Aby w pełni zrozumieć przykłady kodu, należy zapoznać się z tematami [ASP.NET Core Fundamentals](/aspnet/core/fundamentals), a zwłaszcza z [iniekcją](/aspnet/core/fundamentals/dependency-injection) i [opcjami](/aspnet/core/fundamentals/configuration/options)zależności.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

Ponieważ logowanie użytkownika jest delegowane do oprogramowania pośredniczącego OpenID Connect Connect (OIDC), należy korzystać z procesu OIDC. Sposób działania zależy od używanej platformy.

W przypadku usługi ASP.NET zasubskrybujesz zdarzenia oprogramowania OIDC pośredniczące:

- Umożliwimy ASP.NET Core żądanie kodu autoryzacji za pomocą programu Open ID Connect. ASP.NET lub ASP.NET Core umożliwi użytkownikowi logowanie się i zgodę.
- Zasubskrybujesz aplikację sieci Web, aby otrzymać kod autoryzacji. Ta subskrypcja jest realizowana za pomocą delegata języka C#.
- Po odebraniu kodu autoryzacji będziesz używać bibliotek MSAL do ich zrealizowania. Tokeny dostępu i tokeny odświeżania są przechowywane w pamięci podręcznej tokenów. Pamięć podręczna może być używana w innych częściach aplikacji, takich jak kontrolery, aby uzyskać inne tokeny w trybie dyskretnym.

Przykłady kodu w tym artykule i poniższe zostały wyodrębnione z [przykładowej aplikacji internetowej ASP.NET](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect). Warto zapoznać się z tym przykładem, aby uzyskać szczegółowe informacje dotyczące implementacji.

# <a name="java"></a>[Java](#tab/java)

Przykłady kodu w tym artykule i następujące zostały wyodrębnione z [aplikacji sieci Web Java, która wywołuje Microsoft Graph](https://github.com/Azure-Samples/ms-identity-java-webapp), przykład aplikacji sieci Web, która używa MSAL for Java.
Przykład umożliwia obecnie MSAL for Java utworzenie adresu URL kodu autoryzacji i obsługę nawigacji do punktu końcowego autoryzacji platformy tożsamości firmy Microsoft. Istnieje również możliwość użycia zabezpieczeń przebiegu w celu podpisania użytkownika w programie. Warto zapoznać się z przykładem, aby uzyskać pełne szczegóły implementacji.

# <a name="python"></a>[Python](#tab/python)

Przykłady kodu w tym artykule i następujące zostały wyodrębnione z [aplikacji sieci Web](https://github.com/Azure-Samples/ms-identity-python-webapp)w języku Python, która wywołuje Microsoft Graph, przykład aplikacji internetowej korzystającej z MSAL. Python.
Przykład umożliwia obecnie MSAL. Język Python tworzy adres URL kodu autoryzacji i obsługuje nawigację do punktu końcowego autoryzacji platformy tożsamości firmy Microsoft. Warto zapoznać się z przykładem, aby uzyskać pełne szczegóły implementacji.

---

## <a name="code-that-redeems-the-authorization-code"></a>Kod, który zrealizuje kod autoryzacji

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Microsoft. Identity. Web upraszcza kod przez ustawienie poprawnych ustawień połączenia OpenID Connect, subskrybowanie zdarzenia otrzymało kod i zrealizowanie kodu. Do zrealizowania kodu autoryzacji nie jest wymagany żaden dodatkowy kod. Aby uzyskać szczegółowe informacje o tym, jak to działa, zobacz [Microsoft. Identity. Web Code Source](https://github.com/AzureAD/microsoft-identity-web/blob/c29f1a7950b940208440bebf0bcb524a7d6bee22/src/Microsoft.Identity.Web/WebAppExtensions/WebAppCallsWebApiAuthenticationBuilderExtensions.cs#L140) .

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

ASP.NET obsługuje elementy podobnie jak ASP.NET Core, z tą różnicą, że konfiguracja OpenID Connect Connect i subskrypcję `OnAuthorizationCodeReceived` zdarzenia wystąpi w pliku [App_Start \Startup.auth.cs](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/App_Start/Startup.Auth.cs) . Pojęcia są również podobne do tych w ASP.NET Core, z wyjątkiem tego, że w ASP.NET należy określić `RedirectUri` w [Web.config # L15](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/master/WebApp/Web.config#L15). Ta konfiguracja jest nieco mniej niezawodna niż ta w ASP.NET Core, ponieważ należy ją zmienić podczas wdrażania aplikacji.

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

Zobacz temat [aplikacja sieci Web, która loguje się w użytkownikach: Konfiguracja kodu](scenario-web-app-sign-user-app-configuration.md?tabs=java#initialization-code) , aby zrozumieć, jak przykład Java Pobiera kod autoryzacji. Gdy aplikacja otrzyma kod, [AuthFilter. Java # L51-L56](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthFilter.java#L51-L56):

1. Deleguje do `AuthHelper.processAuthenticationCodeRedirect` metody w [AuthHelper. Java # L67-L97](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthHelper.java#L67-L97).
1. Wywołania `getAuthResultByAuthCode` .

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

`getAuthResultByAuthCode`Metoda jest definiowana w [AuthHelper. Java # L176](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthHelper.java#L176). Tworzy element MSAL `ConfidentialClientApplication` , a następnie wywołuje metodę `acquireToken()` `AuthorizationCodeParameters` utworzoną na podstawie kodu autoryzacji.

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

Zażądano przepływu kodu autoryzacji, jak pokazano w [aplikacji sieci Web, która loguje się do użytkowników: Konfiguracja kodu](scenario-web-app-sign-user-app-configuration.md?tabs=python#initialization-code). Następnie kod jest odbierany przez `authorized` funkcję, która przeniesie trasy z `/getAToken` adresu URL. Zobacz [App. PR # L30-L44](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L30-L44) , aby uzyskać pełny kontekst tego kodu:

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

Zamiast wpisu tajnego klienta poufna aplikacja kliencka może również udowodnić swoją tożsamość przy użyciu certyfikatu klienta lub potwierdzenia klienta.
Użycie potwierdzeń klientów jest zaawansowanym scenariuszem, szczegółowym w przypadku [potwierdzeń klientów](msal-net-client-assertions.md).

## <a name="token-cache"></a>Pamięć podręczna tokenów

> [!IMPORTANT]
> Implementacja pamięci podręcznej tokenów dla aplikacji sieci Web lub interfejsów API sieci Web różni się od implementacji aplikacji klasycznych, która często jest [oparta na plikach](scenario-desktop-acquire-token.md#file-based-token-cache).
> Ze względu na bezpieczeństwo i wydajność warto upewnić się, że w przypadku aplikacji sieci Web i interfejsów API sieci Web istnieje jedna pamięć podręczna tokenów na konto użytkownika. Należy serializować pamięć podręczną tokenów dla każdego konta.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Samouczek ASP.NET Core używa iniekcji zależności, aby umożliwić określenie implementacji pamięci podręcznej tokenów w pliku Startup.cs dla aplikacji. Microsoft. Identity. Web zawiera wstępnie skompilowane serializatory pamięci podręcznej tokenów opisane w [serializacji buforu tokenów](msal-net-token-cache-serialization.md#token-cache-for-a-web-app-confidential-client-application). Interesującą możliwością jest wybranie ASP.NET Core pamięci [podręcznej pamięci rozproszonej](/aspnet/core/performance/caching/distributed#distributed-memory-cache):

```csharp
// Use a distributed token cache by adding:
    services.AddMicrosoftIdentityWebAppAuthentication(Configuration, "AzureAd")
            .EnableTokenAcquisitionToCallDownstreamApi(
                initialScopes: new string[] { "user.read" })
            .AddDistributedTokenCaches();

// Then, choose your implementation.
// For instance, the distributed in-memory cache (not cleared when you stop the app):
services.AddDistributedMemoryCache();

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

Aby uzyskać szczegółowe informacje o dostawcach pamięci podręcznej tokenów, zobacz również artykuł Microsoft. Identity. Web App detail [Serialization](https://aka.ms/ms-id-web/token-cache-serialization) , a także [samouczki aplikacji sieci Web ASP.NET Core | Faza pamięci podręcznej tokenów](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-2-TokenCache) samouczka aplikacji sieci Web.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

Implementacja pamięci podręcznej tokenów dla aplikacji sieci Web lub interfejsów API sieci Web różni się od implementacji aplikacji klasycznych, która często jest [oparta na plikach](scenario-desktop-acquire-token.md#file-based-token-cache).

Implementacja aplikacji sieci Web może korzystać z sesji ASP.NET lub pamięci serwera. Na przykład Zobacz, jak implementacja pamięci podręcznej jest podłączana po utworzeniu aplikacji MSAL.NET w [MsalAppBuilder. cs # L39-L51](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Utils/MsalAppBuilder.cs#L39-L51):

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

MSAL Java udostępnia metody serializacji i deserializacji pamięci podręcznej tokenów. Przykład Java obsługuje serializację z sesji, jak pokazano w `getAuthResultBySilentFlow` metodzie w [AuthHelper. Java # L99-L122](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthHelper.java#L99-L122):

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

Szczegóły `SessionManagementHelper` klasy są dostępne w [przykładzie MSAL dla języka Java](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/SessionManagementHelper.java).

# <a name="python"></a>[Python](#tab/python)

W przykładzie języka Python jedna pamięć podręczna na konto jest zapewniana przez ponowne utworzenie poufnej aplikacji klienckiej dla każdego żądania, a następnie Serializacja jej w pamięci podręcznej sesji kolb:

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

W tym momencie po zalogowaniu się użytkownika token jest przechowywany w pamięci podręcznej tokenów. Zobaczmy, w jaki sposób jest używany w innych częściach aplikacji sieci Web.

[Usuwanie kont z pamięci podręcznej na globalne wylogowywanie](scenario-web-app-call-api-sign-in.md)
