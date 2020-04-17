---
title: Konfigurowanie aplikacji sieci Web, która loguje się do użytkowników — platforma tożsamości firmy Microsoft | Azure
description: Dowiedz się, jak utworzyć aplikację internetową, która loguje się do użytkowników (konfiguracja kodu)
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
ms.openlocfilehash: 3cb51a57baa87849e97f7b05762dc4d6eba787a6
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537115"
---
# <a name="web-app-that-signs-in-users-code-configuration"></a>Aplikacja sieci Web, która loguje się do użytkowników: Konfiguracja kodu

Dowiedz się, jak skonfigurować kod aplikacji sieci Web, który loguje się do użytkowników.

## <a name="libraries-for-protecting-web-apps"></a>Biblioteki do ochrony aplikacji internetowych

<!-- This section can be in an include for web app and web APIs -->
Biblioteki, które są używane do ochrony aplikacji sieci web (i interfejsu API sieci web) są:

| Platforma | Biblioteka | Opis |
|----------|---------|-------------|
| ![.NET](media/sample-v2-code/logo_net.png) | [Rozszerzenia modelu tożsamości dla platformy .NET](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/wiki) | Rozszerzenia modelu tożsamości firmy Microsoft dla platformy .NET, używane bezpośrednio przez ASP.NET i ASP.NET Core, proponują zestaw bibliotek DLL uruchomionych zarówno w programie .NET Framework, jak i w programie .NET Core. Z ASP.NET lub ASP.NET Core aplikacji sieci web, można kontrolować sprawdzanie poprawności tokenu przy użyciu **TokenValidationParameters** klasy (w szczególności w niektórych scenariuszach partnerów). |
| ![Java](media/sample-v2-code/small_logo_java.png) | [MSAL Java](https://github.com/AzureAD/microsoft-authentication-library-for-java/wiki) | Obsługa aplikacji internetowych Java |
| ![Python](media/sample-v2-code/small_logo_python.png) | [MĘTÓW MSAL](https://github.com/AzureAD/microsoft-authentication-library-for-python/wiki) | Obsługa aplikacji sieci Web języka Python |

Wybierz kartę odpowiadającą platformie, która Cię interesuje:

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Fragmenty kodu w tym artykule i następujące są wyodrębniane z [ASP.NET Core aplikacji sieci web przyrostowe samouczek, rozdział 1](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-1-MyOrg).

Aby uzyskać szczegółowe informacje na temat implementacji, należy zapoznać się z tym samouczka.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

Fragmenty kodu w tym artykule i następujące są wyodrębniane z [ASP.NET przykładu aplikacji sieci web](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect).

Można odwołać się do tego przykładu, aby uzyskać szczegółowe informacje na temat implementacji.

# <a name="java"></a>[Java](#tab/java)

Fragmenty kodu w tym artykule i następujące są wyodrębniane z aplikacji sieci Web Java wywołując przykład [wykresu firmy Microsoft](https://github.com/Azure-Samples/ms-identity-java-webapp) w języku MSAL Java.

Można odwołać się do tego przykładu, aby uzyskać szczegółowe informacje na temat implementacji.

# <a name="python"></a>[Python](#tab/python)

Fragmenty kodu w tym artykule i następujące są wyodrębniane z aplikacji sieci web języka Python wywołując przykład [wykresu firmy Microsoft](https://github.com/Azure-Samples/ms-identity-python-webapp) w programie MSAL Python.

Można odwołać się do tego przykładu, aby uzyskać szczegółowe informacje na temat implementacji.

---

## <a name="configuration-files"></a>Pliki konfiguracyjne

Aplikacje sieci Web, które logują się do użytkowników przy użyciu platformy tożsamości firmy Microsoft, są zwykle konfigurowane za pomocą plików konfiguracyjnych. Ustawienia, które należy wypełnić, to:

- Wystąpienie chmury`Instance`( ), jeśli chcesz, aby aplikacja działała w chmurach krajowych, na przykład
- Publiczność w identyfikatorze`TenantId`najemcy ( )
- Identyfikator klienta (`ClientId`) dla aplikacji, skopiowany z witryny Azure portal

Czasami aplikacje mogą być `Authority`parametryzowane przez , `Instance` który `TenantId`jest łączenie i .

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

W ASP.NET Core te ustawienia znajdują się w pliku [appsettings.json](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/1-WebApp-OIDC/1-1-MyOrg/appsettings.json#L2-L8) w sekcji "AzureAd".

```Json
{
  "AzureAd": {
    // Azure cloud instance among:
    // - "https://login.microsoftonline.com/" for Azure public cloud
    // - "https://login.microsoftonline.us/" for Azure US government
    // - "https://login.microsoftonline.de/" for Azure AD Germany
    // - "https://login.chinacloudapi.cn/" for Azure AD China operated by 21Vianet
    "Instance": "https://login.microsoftonline.com/",

    // Azure AD audience among:
    // - "TenantId" as a GUID obtained from the Azure portal to sign in users in your organization
    // - "organizations" to sign in users in any work or school account
    // - "common" to sign in users with any work or school account or Microsoft personal account
    // - "consumers" to sign in users with a Microsoft personal account only
    "TenantId": "[Enter the tenantId here]",

    // Client ID (application ID) obtained from the Azure portal
    "ClientId": "[Enter the Client Id]",
    "CallbackPath": "/signin-oidc",
    "SignedOutCallbackPath ": "/signout-callback-oidc"
  }
}
```

W ASP.NET Core inny plik[(properties\launchSettings.json](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/1-WebApp-OIDC/1-1-MyOrg/Properties/launchSettings.json#L6-L7)) zawiera`applicationUrl`adres URL ( ) i`sslPort`port TLS/SSL ( ) dla aplikacji i różnych profili.

```Json
{
  "iisSettings": {
    "windowsAuthentication": false,
    "anonymousAuthentication": true,
    "iisExpress": {
      "applicationUrl": "http://localhost:3110/",
      "sslPort": 44321
    }
  },
  "profiles": {
    "IIS Express": {
      "commandName": "IISExpress",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development"
      }
    },
    "webApp": {
      "commandName": "Project",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development"
      },
      "applicationUrl": "http://localhost:3110/"
    }
  }
}
```

W witrynie Azure portal identyfikatory URI odpowiedzi, które należy zarejestrować na stronie **uwierzytelniania** dla aplikacji, muszą być zgodne z tymi adresami URL. Dla dwóch poprzednich plików konfiguracyjnych `https://localhost:44321/signin-oidc`będą one . Powodem jest `applicationUrl` `http://localhost:3110`to, `sslPort` ale jest określony (44321). `CallbackPath`jest `/signin-oidc`, zgodnie `appsettings.json`z definicją w .

W ten sam sposób identyfikator URI wylogowywania zostanie ustawiony na `https://localhost:44321/signout-callback-oidc`.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

W ASP.NET aplikacja jest konfigurowana za pośrednictwem pliku [Web.config,](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Web.config#L12-L15) wiersze od 12 do 15.

```XML
<?xml version="1.0" encoding="utf-8"?>
<!--
  For more information on how to configure your ASP.NET application, visit
  https://go.microsoft.com/fwlink/?LinkId=301880
  -->
<configuration>
  <appSettings>
    <add key="webpages:Version" value="3.0.0.0" />
    <add key="webpages:Enabled" value="false" />
    <add key="ClientValidationEnabled" value="true" />
    <add key="UnobtrusiveJavaScriptEnabled" value="true" />
    <add key="ida:ClientId" value="[Enter your client ID, as obtained from the app registration portal]" />
    <add key="ida:ClientSecret" value="[Enter your client secret, as obtained from the app registration portal]" />
    <add key="ida:AADInstance" value="https://login.microsoftonline.com/{0}{1}" />
    <add key="ida:RedirectUri" value="https://localhost:44326/" />
    <add key="vs:EnableBrowserLink" value="false" />
  </appSettings>
```

W witrynie Azure portal identyfikatory URI odpowiedzi, które należy zarejestrować na stronie **uwierzytelniania** dla aplikacji, muszą być zgodne z tymi adresami URL. Oznacza to, że `https://localhost:44326/`powinny być .

# <a name="java"></a>[Java](#tab/java)

W języku Java konfiguracja znajduje się w pliku `src/main/resources` [application.properties](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/resources/application.properties) znajdującym się pod plikiem .

```Java
aad.clientId=Enter_the_Application_Id_here
aad.authority=https://login.microsoftonline.com/Enter_the_Tenant_Info_Here/
aad.secretKey=Enter_the_Client_Secret_Here
aad.redirectUriSignin=http://localhost:8080/msal4jsample/secure/aad
aad.redirectUriGraph=http://localhost:8080/msal4jsample/graph/me
```

W witrynie Azure portal identyfikatory URI odpowiedzi, które należy zarejestrować na `redirectUri` stronie **uwierzytelniania** dla aplikacji, muszą być zgodne z wystąpieniami zdefiniowanymi przez aplikację. Oznacza to, że `http://localhost:8080/msal4jsample/secure/aad` `http://localhost:8080/msal4jsample/graph/me`powinny być i .

# <a name="python"></a>[Python](#tab/python)

Oto plik konfiguracyjny Pythona w [app_config.py:](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/0.1.0/app_config.py)

```Python
CLIENT_SECRET = "Enter_the_Client_Secret_Here"
AUTHORITY = "https://login.microsoftonline.com/common""
CLIENT_ID = "Enter_the_Application_Id_here"
ENDPOINT = 'https://graph.microsoft.com/v1.0/users'
SCOPE = ["User.ReadBasic.All"]
SESSION_TYPE = "filesystem"  # So the token cache will be stored in a server-side session
```

> [!NOTE]
> Ten przewodnik Szybki start proponuje przechowywanie klucza tajnego klienta w pliku konfiguracyjnym dla uproszczenia. W aplikacji produkcyjnej warto użyć innych sposobów przechowywania klucza tajnego, takich jak magazyn kluczy lub zmienna [środowiskowa opisana w dokumentacji Flask.](https://flask.palletsprojects.com/en/1.1.x/config/#configuring-from-environment-variables)
>
> ```python
> CLIENT_SECRET = os.getenv("CLIENT_SECRET")
> if not CLIENT_SECRET:
>     raise ValueError("Need to define CLIENT_SECRET environment variable")
> ```

---

## <a name="initialization-code"></a>Kod inicjowania

Kod inicjowania różni się w zależności od platformy. W przypadku ASP.NET Core i ASP.NET logowania użytkownicy są delegowani do oprogramowania pośredniczącego OpenID Connect. Szablon ASP.NET lub ASP.NET Core generuje aplikacje sieci web dla punktu końcowego usługi Azure Active Directory (Azure AD) w wersji 1.0. Niektóre konfiguracje są wymagane, aby dostosować je do punktu końcowego platformy tożsamości firmy Microsoft (wersja 2.0). W przypadku Javy jest obsługiwany przez Wiosnę przy współpracy aplikacji.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

W ASP.NET core aplikacji sieci web (i interfejsów API sieci web), aplikacja jest chroniona, ponieważ masz `[Authorize]` atrybut na kontrolerach lub akcji kontrolera. Ten atrybut sprawdza, czy użytkownik jest uwierzytelniony. Kod, który inicjuje aplikację znajduje się w pliku Startup.cs.

Aby dodać uwierzytelnianie za pomocą platformy tożsamości firmy Microsoft (dawniej Usługi Azure AD w wersji 2.0), należy dodać następujący kod. Uwagi w kodzie powinny być oczywiste.

> [!NOTE]
> Jeśli projekt zostanie uruchomiony z domyślnym projektem sieci web ASP.NET `dotnet new mvc`Core `AddAzureAD` w programie Visual Studio lub przy użyciu metody jest domyślnie dostępna. To dlatego, że powiązane pakiety są ładowane automatycznie.
>
> Jeśli tworzysz projekt od podstaw i próbujesz użyć następującego kodu, sugerujemy dodanie pakietu NuGet **Microsoft.AspNetCore.Authentication.AzureAD.UI** do projektu, aby udostępnić `AddAzureAD` tę metodę.

Poniższy kod jest dostępny w [pliku Startup.cs#L33-L34](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/faa94fd49c2da46b22d6694c4f5c5895795af26d/1-WebApp-OIDC/1-1-MyOrg/Startup.cs#L33-L34).

```csharp
public class Startup
{
 ...

  // This method is called by the runtime. Use this method to add services to the container.
  public void ConfigureServices(IServiceCollection services)
  {
    ...
      // Sign in users with the Microsoft identity platform
      services.AddMicrosoftIdentityPlatformAuthentication(Configuration);

      services.AddMvc(options =>
      {
          var policy = new AuthorizationPolicyBuilder()
              .RequireAuthenticatedUser()
              .Build();
            options.Filters.Add(new AuthorizeFilter(policy));
            })
        .SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
    }
```

Metoda `AddMicrosoftIdentityPlatformAuthentication` rozszerzenia jest zdefiniowana w pliku [Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L23](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/faa94fd49c2da46b22d6694c4f5c5895795af26d/Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L23). To:

- Dodaje usługę uwierzytelniania.
- Konfiguruje opcje odczytu pliku konfiguracyjnego.
- Konfiguruje opcje OpenID Connect, tak aby używanym urzędem jest punkt końcowy platformy tożsamości firmy Microsoft (dawniej Azure AD w wersji 2.0).
- Sprawdza poprawność wystawcy tokenu.
- Zapewnia, że oświadczenia odpowiadające nazwie są `preferred_username` mapowane z oświadczenia w tokenie identyfikatora.

Oprócz konfiguracji można określić nazwę sekcji konfiguracji podczas `AddMicrosoftIdentityPlatformAuthentication`wywoływania . Domyślnie jest `AzureAd`to .

Śledzenie zdarzeń oprogramowania pośredniczącego OpenId Connect może pomóc w rozwiązywaniu problemów z aplikacją sieci web, jeśli uwierzytelnianie nie działa. `subscribeToOpenIdConnectMiddlewareDiagnosticsEvents` Ustawienie, `true` aby pokazać, jak informacje są rozwijane przez zestaw ASP.NET Core oprogramowania pośredniczącego w miarę `HttpContext.User`postępów od odpowiedzi HTTP do tożsamości użytkownika w .

```csharp
/// <summary>
/// Add authentication with the Microsoft identity platform.
/// This method expects the configuration file to have a section named "AzureAd" with the necessary settings to initialize authentication options.
/// </summary>
/// <param name="services">Service collection to which to add this authentication scheme</param>
/// <param name="configuration">The Configuration object</param>
/// <param name="subscribeToOpenIdConnectMiddlewareDiagnosticsEvents">
/// Set to true if you want to debug, or just understand the OpenID Connect events.
/// </param>
/// <returns></returns>
public static IServiceCollection AddMicrosoftIdentityPlatformAuthentication(
  this IServiceCollection services,
  IConfiguration configuration,
  string configSectionName = "AzureAd",
  bool subscribeToOpenIdConnectMiddlewareDiagnosticsEvents = false)
{
  services.AddAuthentication(AzureADDefaults.AuthenticationScheme)
      .AddAzureAD(options => configuration.Bind(configSectionName, options));
  services.Configure<AzureADOptions>(options => configuration.Bind(configSectionName, options));

  services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
  {
      // Per the following code, this application signs in users in any work or school
      // accounts and any Microsoft personal accounts.
      // If you want to direct Azure AD to restrict the users who can sign in, change
      // the tenant value of the appsettings.json file in the following way:
      // - Only work or school accounts => 'organizations'
      // - Only Microsoft personal accounts => 'consumers'
      // - Work or school and personal accounts => 'common'
      // If you want to restrict the users who can sign in to only one tenant,
      // set the tenant value in the appsettings.json file to the tenant ID
      // or domain of this organization.
      options.Authority = options.Authority + "/v2.0/";

      // If you want to restrict the users who can sign in to several organizations,
      // set the tenant value in the appsettings.json file to 'organizations', and add the
      // issuers you want to accept to the options.TokenValidationParameters.ValidIssuers collection.
      options.TokenValidationParameters.IssuerValidator = AadIssuerValidator.GetIssuerValidator(options.Authority).Validate;

      // Set nameClaimType to be preferred_username.
      // This change is needed because certain token claims from the Azure AD v1 endpoint
      // (on which the original .NET Core template is based) are different from the Microsoft identity platform endpoint.
      // For more details, see [ID tokens](https://docs.microsoft.com/azure/active-directory/develop/id-tokens)
      // and [Access tokens](https://docs.microsoft.com/azure/active-directory/develop/access-tokens).
      options.TokenValidationParameters.NameClaimType = "preferred_username";

      // ...

      if (subscribeToOpenIdConnectMiddlewareDiagnosticsEvents)
      {
          OpenIdConnectMiddlewareDiagnostics.Subscribe(options.Events);
      }
  });
  return services;
}
  ...
```

Klasa `AadIssuerValidator` umożliwia wystawcy tokenu do sprawdzenia poprawności w wielu przypadkach. Ta klasa działa z tokenem w wersji 1.0 lub v2.0, aplikacją z jedną dzierżawą lub wielodostępną lub aplikacją, która loguje się do użytkowników za pomocą ich osobistych kont Microsoft w chmurze publicznej platformy Azure lub chmurach krajowych. Jest on dostępny w witrynie [Microsoft.Identity.Web/Resource/AadIssuerValidator.cs](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/Microsoft.Identity.Web/Resource/AadIssuerValidator.cs).

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

Kod związany z uwierzytelnianiem w ASP.NET aplikacji sieci Web i internetowych interfejsów API znajduje się w pliku [App_Start/Startup.Auth.cs.](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/App_Start/Startup.Auth.cs#L17-L61)

```csharp
 public void ConfigureAuth(IAppBuilder app)
 {
  app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

  app.UseCookieAuthentication(new CookieAuthenticationOptions());

  app.UseOpenIdConnectAuthentication(
    new OpenIdConnectAuthenticationOptions
    {
     // `Authority` represents the identity platform endpoint - https://login.microsoftonline.com/common/v2.0.
     // `Scope` describes the initial permissions that your app will need.
     //  See https://azure.microsoft.com/documentation/articles/active-directory-v2-scopes/.
     ClientId = clientId,
     Authority = String.Format(CultureInfo.InvariantCulture, aadInstance, "common", "/v2.0"),
     RedirectUri = redirectUri,
     Scope = "openid profile",
     PostLogoutRedirectUri = redirectUri,
    });
 }
```

# <a name="java"></a>[Java](#tab/java)

Przykład Języka Java używa struktury Wiosna. Aplikacja jest chroniona, ponieważ implementujesz filtr, który przechwytuje każdą odpowiedź HTTP. W przewodniku Szybki start dla aplikacji `AuthFilter` `src/main/java/com/microsoft/azure/msalwebsample/AuthFilter.java`internetowych Java ten filtr jest w pliku .

Filtr przetwarza przepływ kodu autoryzacji OAuth 2.0 i sprawdza, czy użytkownik jest uwierzytelniony (metoda).`isAuthenticated()` Jeśli użytkownik nie jest uwierzytelniony, oblicza adres URL punktów końcowych autoryzacji usługi Azure AD i przekierowuje przeglądarkę do tego identyfikatora URI.

Po odebraniu odpowiedzi zawierającej kod autoryzacji pobiera token przy użyciu msal java. Po ostatecznym odebraniu tokenu z punktu końcowego tokenu (w identyfikatorze URI przekierowania), użytkownik jest zalogowany.

Aby uzyskać szczegółowe `doFilter()` informacje, zobacz metodę w [pliku AuthFilter.java](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/master/src/main/java/com/microsoft/azure/msalwebsample/AuthFilter.java).

> [!NOTE]
> Kod `doFilter()` jest napisany w nieco innej kolejności, ale przepływ jest opisany.

Aby uzyskać szczegółowe informacje na temat przepływu kodu autoryzacji wyzwalającego tę metodę, zobacz [Platformę tożsamości firmy Microsoft i przepływ kodu autoryzacji OAuth 2.0.](v2-oauth2-auth-code-flow.md)

# <a name="python"></a>[Python](#tab/python)

Przykład Pythona używa Flask. Inicjalizacja Flask i MSAL Python odbywa się w [app.py#L1-L28](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L1-L28).

```Python
import uuid
import requests
from flask import Flask, render_template, session, request, redirect, url_for
from flask_session import Session  # https://pythonhosted.org/Flask-Session
import msal
import app_config


app = Flask(__name__)
app.config.from_object(app_config)
Session(app)
```

---

## <a name="next-steps"></a>Następne kroki

W następnym artykule dowiesz się, jak wyzwolić logowanie i wylogowywanie się.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

> [!div class="nextstepaction"]
> [Logowanie i wylogowywanie](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-sign-in?tabs=aspnetcore)

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

> [!div class="nextstepaction"]
> [Logowanie i wylogowywanie](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-sign-in?tabs=aspnet)

# <a name="java"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [Logowanie i wylogowywanie](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-sign-in?tabs=java)

# <a name="python"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [Logowanie i wylogowywanie](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-sign-in?tabs=python)

---
