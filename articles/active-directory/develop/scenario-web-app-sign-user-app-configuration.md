---
title: Konfigurowanie aplikacji sieci Web, która loguje się do użytkowników — platforma tożsamości firmy Microsoft | Azure
description: Dowiedz się, jak utworzyć aplikację internetową, która loguje się do użytkowników (Konfiguracja kodu)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/14/2020
ms.author: jmprieur
ms.custom: aaddev, devx-track-python
ms.openlocfilehash: ea0312cd8129fce342f94cfab5701d1773aca309
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91728339"
---
# <a name="web-app-that-signs-in-users-code-configuration"></a>Aplikacja internetowa, która loguje użytkowników: Konfiguracja kodu

Dowiedz się, jak skonfigurować kod dla aplikacji sieci Web, która loguje się w użytkownikach.

## <a name="libraries-for-protecting-web-apps"></a>Biblioteki do ochrony aplikacji sieci Web

<!-- This section can be in an include for web app and web APIs -->
Biblioteki używane do ochrony aplikacji sieci Web (i internetowego interfejsu API) są następujące:

| Platforma | Biblioteka | Opis |
|----------|---------|-------------|
| ![.NET](media/sample-v2-code/logo_NET.png) | [Rozszerzenia modelu tożsamości dla platformy .NET](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/wiki) | Używane bezpośrednio przez ASP.NET i ASP.NET Core, rozszerzenia modelu tożsamości Microsoft dla platformy .NET proponują zestaw bibliotek DLL działających na .NET Framework i .NET Core. Z aplikacji sieci Web ASP.NET lub ASP.NET Core można kontrolować sprawdzanie poprawności tokenu przy użyciu klasy **TokenValidationParameters** (w szczególności w niektórych scenariuszach partnerskich). W tej rzeczywistości złożoność jest hermetyzowana w bibliotece [Microsoft. Identity. Web](https://aka.ms/ms-identity-web) Library |
| ![Java](media/sample-v2-code/small_logo_java.png) | [MSAL Java](https://github.com/AzureAD/microsoft-authentication-library-for-java/wiki) | Obsługa aplikacji sieci Web w języku Java |
| ![Python](media/sample-v2-code/small_logo_python.png) | [MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python/wiki) | Obsługa aplikacji sieci Web w języku Python |

Wybierz kartę odpowiadającą wybranej platformie:

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Fragmenty kodu w tym artykule i następujące zostały wyodrębnione z [samouczka ASP.NET Core aplikacji sieci Web, rozdział 1](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-1-MyOrg).

Warto zapoznać się z tym samouczkiem, aby uzyskać szczegółowe informacje dotyczące implementacji.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

Fragmenty kodu w tym artykule i poniższe elementy są wyodrębniane z [przykładowej aplikacji sieci web ASP.NET](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect).

Warto zapoznać się z tym przykładem, aby uzyskać szczegółowe informacje dotyczące implementacji.

# <a name="java"></a>[Java](#tab/java)

Fragmenty kodu w tym artykule i poniższe zostały wyodrębnione z [aplikacji sieci Web Java wywołującej przykład Microsoft Graph](https://github.com/Azure-Samples/ms-identity-java-webapp) w języku Java MSAL.

Warto zapoznać się z tym przykładem, aby uzyskać szczegółowe informacje dotyczące implementacji.

# <a name="python"></a>[Python](#tab/python)

Fragmenty kodu w tym artykule i następujące elementy zostały wyodrębnione z [aplikacji sieci Web w języku Python wywołującej przykład programu Microsoft Graph](https://github.com/Azure-Samples/ms-identity-python-webapp) w języku Python MSAL.

Warto zapoznać się z tym przykładem, aby uzyskać szczegółowe informacje dotyczące implementacji.

---

## <a name="configuration-files"></a>Pliki konfiguracji

Aplikacje sieci Web, które logują użytkowników przy użyciu platformy tożsamości firmy Microsoft, są konfigurowane za pomocą plików konfiguracji. Ustawienia, które należy wypełnić:

- Wystąpienie w chmurze ( `Instance` ), jeśli chcesz, aby aplikacja działała w chmurach narodowych, na przykład
- Odbiorcy w IDENTYFIKATORze dzierżawy ( `TenantId` )
- Identyfikator klienta ( `ClientId` ) dla aplikacji, jak skopiowano z Azure Portal

Czasami aplikacje mogą być parametryczne przez `Authority` , co jest połączeniem z `Instance` i `TenantId` .

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

W ASP.NET Core ustawienia te znajdują się w pliku [appsettings.js](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/1-WebApp-OIDC/1-1-MyOrg/appsettings.json#L2-L8) w sekcji "AzureAd".

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
    "SignedOutCallbackPath": "/signout-oidc"
  }
}
```

W ASP.NET Core inny plik ([properties\launchSettings.json](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/1-WebApp-OIDC/1-1-MyOrg/Properties/launchSettings.json#L6-L7)) zawiera adres URL ( `applicationUrl` ) oraz port TLS/SSL ( `sslPort` ) dla aplikacji i różnych profilów.

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

W Azure Portal identyfikatory URI odpowiedzi, które należy zarejestrować na stronie **uwierzytelniania** dla aplikacji, muszą być zgodne z tymi adresami URL. W przypadku dwóch wcześniejszych plików konfiguracji mogą one być `https://localhost:44321/signin-oidc` . Przyczyną jest `applicationUrl` `http://localhost:3110` , ale `sslPort` określono (44321). `CallbackPath` jest `/signin-oidc` , zgodnie z definicją w `appsettings.json` .

W ten sam sposób identyfikator URI wylogowania zostanie ustawiony na `https://localhost:44321/signout-oidc` .

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

W programie ASP.NET aplikacja jest konfigurowana za pomocą pliku [Web.config](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Web.config#L12-L15) , wierszy od 12 do 15.

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

W Azure Portal identyfikatory URI odpowiedzi, które należy zarejestrować na stronie **uwierzytelniania** dla aplikacji, muszą być zgodne z tymi adresami URL. Oznacza to, że powinny być `https://localhost:44326/` .

# <a name="java"></a>[Java](#tab/java)

W języku Java konfiguracja znajduje się w pliku [Application. Properties](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/resources/application.properties) znajdującym się w sekcji `src/main/resources` .

```Java
aad.clientId=Enter_the_Application_Id_here
aad.authority=https://login.microsoftonline.com/Enter_the_Tenant_Info_Here/
aad.secretKey=Enter_the_Client_Secret_Here
aad.redirectUriSignin=http://localhost:8080/msal4jsample/secure/aad
aad.redirectUriGraph=http://localhost:8080/msal4jsample/graph/me
```

W Azure Portal identyfikatory URI odpowiedzi, które należy zarejestrować na stronie **uwierzytelniania** dla aplikacji, muszą być zgodne z `redirectUri` wystąpieniami zdefiniowanymi przez aplikację. Oznacza to, że powinny być `http://localhost:8080/msal4jsample/secure/aad` i `http://localhost:8080/msal4jsample/graph/me` .

# <a name="python"></a>[Python](#tab/python)

Oto plik konfiguracyjny języka Python w [app_config. PR](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/0.1.0/app_config.py):

```Python
CLIENT_SECRET = "Enter_the_Client_Secret_Here"
AUTHORITY = "https://login.microsoftonline.com/common""
CLIENT_ID = "Enter_the_Application_Id_here"
ENDPOINT = 'https://graph.microsoft.com/v1.0/users'
SCOPE = ["User.ReadBasic.All"]
SESSION_TYPE = "filesystem"  # So the token cache will be stored in a server-side session
```

> [!NOTE]
> Ten przewodnik Szybki Start proponuje przechowywanie klucza tajnego klienta w pliku konfiguracyjnym dla uproszczenia. W aplikacji produkcyjnej warto używać innych metod przechowywania danych tajnych, takich jak Magazyn kluczy lub zmienna środowiskowa, zgodnie z opisem w [dokumentacji kolby](https://flask.palletsprojects.com/en/1.1.x/config/#configuring-from-environment-variables).
>
> ```python
> CLIENT_SECRET = os.getenv("CLIENT_SECRET")
> if not CLIENT_SECRET:
>     raise ValueError("Need to define CLIENT_SECRET environment variable")
> ```

---

## <a name="initialization-code"></a>Kod inicjalizacji

Kod inicjalizacji różni się w zależności od platformy. W przypadku ASP.NET Core i ASP.NET Logowanie użytkowników jest delegowane do oprogramowania pośredniczącego OpenID Connect Connect. Szablon ASP.NET lub ASP.NET Core generuje aplikacje sieci Web dla punktu końcowego Azure Active Directory (Azure AD) v 1.0. Aby dostosować je do punktu końcowego Microsoft Identity platform (v 2.0), wymagana jest pewna konfiguracja. W przypadku środowiska Java jest ono obsługiwane przez źródło współpracy aplikacji.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

W ASP.NET Core aplikacje sieci Web (i interfejsy API sieci Web) aplikacja jest chroniona, ponieważ masz `[Authorize]` atrybut na kontrolerach lub akcjach kontrolera. Ten atrybut sprawdza, czy użytkownik jest uwierzytelniony. Kod inicjujący aplikację znajduje się w pliku *Startup.cs* .

Aby dodać uwierzytelnianie za pomocą platformy tożsamości firmy Microsoft (dawniej usługi Azure AD v 2.0), należy dodać następujący kod. Komentarze w kodzie nie powinny mieć wyjaśnień.

> [!NOTE]
> Jeśli chcesz zacząć bezpośrednio od nowych szablonów ASP.NET Core dla platformy tożsamości firmy Microsoft, która korzysta z Microsoft. Identity. Web, możesz pobrać pakiet NuGet wersji zapoznawczej zawierający szablony projektów dla platformy .NET Core 3,1 i .NET 5,0. Następnie po zainstalowaniu można bezpośrednio utworzyć wystąpienie ASP.NET Core aplikacji sieci Web (MVC lub Blazor). Aby uzyskać szczegółowe informacje, zobacz [Szablony projektów aplikacji sieci Web Microsoft. Identity. Web](https://aka.ms/ms-id-web/webapp-project-templates) . Jest to najprostszy sposób, jaki wykona wszystkie poniższe kroki.
>
> Jeśli wolisz rozpocząć projekt przy użyciu bieżącego domyślnego projektu sieci Web ASP.NET Core w programie Visual Studio lub za pomocą `dotnet new mvc --auth SingleAuth` lub `dotnet new webapp --auth SingleAuth` , zobaczysz kod podobny do następującego:
>
>```c#
>  services.AddAuthentication(AzureADDefaults.AuthenticationScheme)
>          .AddAzureAD(options => Configuration.Bind("AzureAd", options));
> ```
>
> Ten kod używa starszego pakietu NuGet **Microsoft. AspNetCore. Authentication. AzureAD. UI** , który jest używany do tworzenia aplikacji usługi Azure AD w wersji 1.0. W tym artykule wyjaśniono, jak utworzyć aplikację platformy tożsamości firmy Microsoft (Azure AD v 2.0), która zastępuje ten kod.
>

1. Dodaj pakiety NuGet [Microsoft. Identity. Web](https://www.nuget.org/packages/Microsoft.Identity.Web) i [Microsoft. Identity. Web. UI](https://www.nuget.org/packages/Microsoft.Identity.Web.UI) do projektu. Usuń pakiet NuGet Microsoft. AspNetCore. Authentication. AzureAD. UI, jeśli jest obecny.

2. Zaktualizuj kod w `ConfigureServices` tak, aby korzystał z `AddMicrosoftIdentityWebAppAuthentication` metod i `AddMicrosoftIdentityUI` .

   ```c#
   public class Startup
   {
    ...
    // This method gets called by the runtime. Use this method to add services to the container.
    public void ConfigureServices(IServiceCollection services)
    {
     services.AddMicrosoftIdentityWebAppAuthentication(Configuration, "AzureAd");

     services.AddRazorPages().AddMvcOptions(options =>
     {
      var policy = new AuthorizationPolicyBuilder()
                    .RequireAuthenticatedUser()
                    .Build();
      options.Filters.Add(new AuthorizeFilter(policy));
     }).AddMicrosoftIdentityUI();
    ```

3. W `Configure` metodzie w *Startup.cs*Włącz uwierzytelnianie z wywołaniem do `app.UseAuthentication();`

   ```c#
   // This method gets called by the runtime. Use this method to configure the HTTP request pipeline.
   public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
   {
    // more code here
    app.UseAuthentication();
    app.UseAuthorization();
    // more code here
   }
   ```

W powyższym kodzie:
- `AddMicrosoftIdentityWebAppAuthentication`Metoda rozszerzenia jest zdefiniowana w **Microsoft. Identity. Web**. Go
  - Dodaje usługę uwierzytelniania.
  - Konfiguruje opcje odczytywania pliku konfiguracji (w tym miejscu z sekcji "AzureAD")
  - Konfiguruje opcje połączenia OpenID Connect, dzięki czemu Urząd jest punktem końcowym Microsoft Identity platform.
  - Sprawdza poprawność wystawcy tokenu.
  - Zapewnia, że oświadczenia odpowiadające nazwie są mapowane z `preferred_username` oświadczenia w tokenie ID.

- Oprócz obiektu konfiguracji można określić nazwę sekcji konfiguracji podczas wywoływania `AddMicrosoftIdentityWebAppAuthentication` . Domyślnie jest to `AzureAd` .

- `AddMicrosoftIdentityWebAppAuthentication` ma inne parametry dla zaawansowanych scenariuszy. Na przykład śledzenie zdarzeń oprogramowania pośredniczącego OpenID Connect może pomóc w rozwiązywaniu problemów z aplikacją sieci Web, jeśli uwierzytelnianie nie działa. Ustawienie parametru opcjonalnego `subscribeToOpenIdConnectMiddlewareDiagnosticsEvents` na `true` pokazuje, jak informacje są przetwarzane przez zestaw ASP.NET Core oprogramowania pośredniczącego w miarę postępu z odpowiedzi HTTP na tożsamość użytkownika w programie `HttpContext.User` .

- `AddMicrosoftIdentityUI`Metoda rozszerzenia jest zdefiniowana w **Microsoft. Identity. Web. UI**. Udostępnia on domyślny kontroler do obsługi logowania i wylogowywania.

Więcej informacji o sposobie tworzenia aplikacji sieci Web w programie Microsoft. Identity. Web można znaleźć w temacie <https://aka.ms/ms-id-web/webapp>

> [!WARNING]
> Obecnie firma Microsoft. Identity. Web nie obsługuje scenariusza **poszczególnych kont użytkowników** (przechowywanie kont użytkowników w aplikacji) w przypadku korzystania z usługi Azure AD jako dostawcy logowania zewnętrznego. Aby uzyskać szczegółowe informacje, zobacz: [AzureAD/Microsoft-Identity-Web # 133](https://github.com/AzureAD/microsoft-identity-web/issues/133)

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

Kod związany z uwierzytelnianiem w aplikacji sieci Web ASP.NET i interfejsy API sieci Web znajdują się w pliku [App_Start/Startup.auth.cs](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/App_Start/Startup.Auth.cs#L17-L61) .

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

Przykład Java używa struktury sprężynowej. Aplikacja jest chroniona, ponieważ implementuje się filtr, który przechwytuje poszczególne odpowiedzi HTTP. W przypadku aplikacji sieci Web szybkiego startu dla języka Java ten filtr znajduje się `AuthFilter` w temacie `src/main/java/com/microsoft/azure/msalwebsample/AuthFilter.java` .

Filtr przetwarza przepływ kodu autoryzacji OAuth 2,0 i sprawdza, czy użytkownik jest uwierzytelniony ( `isAuthenticated()` Metoda). Jeśli użytkownik nie jest uwierzytelniony, oblicza adres URL punktów końcowych autoryzacji usługi Azure AD i przekierowuje przeglądarkę do tego identyfikatora URI.

Po nadejściu odpowiedzi, zawierający kod autoryzacji, uzyskuje token przy użyciu języka Java MSAL. Gdy ostatecznie otrzyma token z punktu końcowego tokenu (w identyfikatorze URI przekierowania), użytkownik jest zalogowany.

Aby uzyskać szczegółowe informacje, zobacz `doFilter()` metodę w [AuthFilter. Java](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/master/msal-java-webapp-sample/src/main/java/com/microsoft/azure/msalwebsample/AuthFilter.java).

> [!NOTE]
> Kod `doFilter()` jest zapisywana w niewielkim stopniu inaczej, ale przepływ jest opisany.

Aby uzyskać szczegółowe informacje na temat przepływu kodu autoryzacji, który jest wyzwalany przez tę metodę, zobacz [przepływ kodu autoryzacji Microsoft Identity platform i OAuth 2,0](v2-oauth2-auth-code-flow.md).

# <a name="python"></a>[Python](#tab/python)

Przykład języka Python używa kolby. Inicjalizacja kolb i MSAL Python jest wykonywana w [App. PR # L1-L28](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L1-L28).

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

W następnym artykule dowiesz się, jak wyzwolić logowanie i wylogować.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

> [!div class="nextstepaction"]
> [Logowanie i wylogowywanie](./scenario-web-app-sign-user-sign-in.md?tabs=aspnetcore)

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

> [!div class="nextstepaction"]
> [Logowanie i wylogowywanie](./scenario-web-app-sign-user-sign-in.md?tabs=aspnet)

# <a name="java"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [Logowanie i wylogowywanie](./scenario-web-app-sign-user-sign-in.md?tabs=java)

# <a name="python"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [Logowanie i wylogowywanie](./scenario-web-app-sign-user-sign-in.md?tabs=python)

---
