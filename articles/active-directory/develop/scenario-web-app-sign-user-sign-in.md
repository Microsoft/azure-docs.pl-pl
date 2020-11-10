---
title: Napisz aplikację sieci Web, która loguje się/out użytkowników — platforma tożsamości firmy Microsoft | Azure
description: Dowiedz się, jak utworzyć aplikację sieci Web, która loguje użytkowników z/z zewnątrz
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
ms.openlocfilehash: e7397f6d02d71a6344953b8210b0349b9ee26360
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/10/2020
ms.locfileid: "94443555"
---
# <a name="web-app-that-signs-in-users-sign-in-and-sign-out"></a>Aplikacja internetowa, która loguje użytkowników: Logowanie i wylogowywanie

Dowiedz się, jak dodać logowanie do kodu aplikacji sieci Web, które logują się w użytkownikach. Następnie Dowiedz się, jak umożliwić im wylogowywanie się.

## <a name="sign-in"></a>Logowanie

Logowanie składa się z dwóch części:

- Przycisk logowania na stronie HTML
- Akcja logowania w kodzie w kontrolerze

### <a name="sign-in-button"></a>Przycisk logowania

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

W ASP.NET Core w przypadku aplikacji platformy tożsamości firmy Microsoft przycisk **logowania** jest widoczny w `Views\Shared\_LoginPartial.cshtml` (dla aplikacji MVC) lub `Pages\Shared\_LoginPartial.cshtm` (dla aplikacji Razor). Jest on wyświetlany tylko wtedy, gdy użytkownik nie jest uwierzytelniony. Oznacza to, że jest wyświetlany, gdy użytkownik jeszcze się nie zalogował lub wylogował się. W przeciwieństwie, przycisk **Wyloguj** jest wyświetlany, gdy użytkownik jest już zalogowany. Należy pamiętać, że kontroler konta jest zdefiniowany w pakiecie NuGet **Microsoft. Identity. Web. UI** w obszarze o nazwie **MicrosoftIdentity**

```html
<ul class="navbar-nav">
  @if (User.Identity.IsAuthenticated)
  {
    <li class="nav-item">
        <span class="navbar-text text-dark">Hello @User.Identity.Name!</span>
    </li>
    <li class="nav-item">
        <a class="nav-link text-dark" asp-area="MicrosoftIdentity" asp-controller="Account" asp-action="SignOut">Sign out</a>
    </li>
  }
  else
  {
    <li class="nav-item">
        <a class="nav-link text-dark" asp-area="MicrosoftIdentity" asp-controller="Account" asp-action="SignIn">Sign in</a>
    </li>
  }
</ul>
```

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

W ASP.NET MVC przycisk Wyloguj jest uwidoczniony w `Views\Shared\_LoginPartial.cshtml` . Jest on wyświetlany tylko wtedy, gdy istnieje uwierzytelnione konto. Oznacza to, że jest wyświetlany, gdy użytkownik zalogował się wcześniej.

```html
@if (Request.IsAuthenticated)
{
 // Code omitted code for clarity
}
else
{
    <ul class="nav navbar-nav navbar-right">
        <li>@Html.ActionLink("Sign in", "SignIn", "Account", routeValues: null, htmlAttributes: new { id = "loginLink" })</li>
    </ul>
}
```

# <a name="java"></a>[Java](#tab/java)

W naszym przewodniku szybki start dla języka Java przycisk logowania znajduje się w pliku [Main/sources/templates/index.html](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/master/msal-java-webapp-sample/src/main/resources/templates/index.html) .

```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>HomePage</title>
</head>
<body>
<h3>Home Page</h3>

<form action="/msal4jsample/secure/aad">
    <input type="submit" value="Login">
</form>

</body>
</html>
```

# <a name="python"></a>[Python](#tab/python)

W przewodniku szybki start dla języka Python nie ma przycisku logowania. Kod zostanie wyświetlony automatycznie Monituj użytkownika o zalogowanie się, gdy dociera do katalogu głównego aplikacji sieci Web. Zobacz [App. PR # L14-L18](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/0.1.0/app.py#L14-L18).

```Python
@app.route("/")
def index():
    if not session.get("user"):
        return redirect(url_for("login"))
    return render_template('index.html', user=session["user"])
```

---

### <a name="signin-action-of-the-controller"></a>`SignIn` Akcja kontrolera

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

W ASP.NET Wybieranie przycisku **logowania** w aplikacji sieci Web wyzwala `SignIn` akcję na `AccountController` kontrolerze. W poprzednich wersjach szablonów ASP.NET Core `Account` kontroler został osadzony w aplikacji sieci Web. To nie jest już przypadek, ponieważ kontroler jest teraz częścią pakietu NuGet **Microsoft. Identity. Web. UI** . Aby uzyskać szczegółowe informacje, zobacz [AccountController.cs](https://github.com/AzureAD/microsoft-identity-web/blob/master/src/Microsoft.Identity.Web.UI/Areas/MicrosoftIdentity/Controllers/AccountController.cs) .

Ten kontroler obsługuje również aplikacje Azure AD B2C.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

W ASP.NET wylogowanie jest wyzwalane z `SignOut()` metody na kontrolerze (na przykład [elementu AccountController. cs # L16-L23](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Controllers/AccountController.cs#L16-L23)). Ta metoda nie jest częścią ASP.NET Framework (w przeciwieństwie do co dzieje się w ASP.NET Core). Wysyła wyzwanie logowania OpenID Connect po zaproponowaniu identyfikatora URI przekierowania.

```csharp
public void SignIn()
{
    // Send an OpenID Connect sign-in request.
    if (!Request.IsAuthenticated)
    {
        HttpContext.GetOwinContext().Authentication.Challenge(new AuthenticationProperties { RedirectUri = "/" }, OpenIdConnectAuthenticationDefaults.AuthenticationType);
    }
}
```

# <a name="java"></a>[Java](#tab/java)

W języku Java wylogowanie jest obsługiwane przez wywołanie punktu końcowego platformy tożsamości firmy Microsoft `logout` bezpośrednio i podanie `post_logout_redirect_uri` wartości. Aby uzyskać szczegółowe informacje, zobacz [AuthPageController. Java # L30-L48](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthPageController.java#L30-L48).

```Java
@Controller
public class AuthPageController {

    @Autowired
    AuthHelper authHelper;

    @RequestMapping("/msal4jsample")
    public String homepage(){
        return "index";
    }

    @RequestMapping("/msal4jsample/secure/aad")
    public ModelAndView securePage(HttpServletRequest httpRequest) throws ParseException {
        ModelAndView mav = new ModelAndView("auth_page");

        setAccountInfo(mav, httpRequest);

        return mav;
    }

    // More code omitted for simplicity
```

# <a name="python"></a>[Python](#tab/python)

W przeciwieństwie do innych platform, MSAL Python wymaga, aby użytkownik zalogować się na stronie logowania. Zobacz [App. PR # L20-L28](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L20-L28).

```Python
@app.route("/login")
def login():
    session["state"] = str(uuid.uuid4())
    auth_url = _build_msal_app().get_authorization_request_url(
        app_config.SCOPE,  # Technically we can use an empty list [] to just sign in
                           # Here we choose to also collect user consent up front
        state=session["state"],
        redirect_uri=url_for("authorized", _external=True))
    return "<a href='%s'>Login with Microsoft Identity</a>" % auth_url
```

`_build_msal_app()`Metoda jest zdefiniowana w [App. PR # L81-L88](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L81-L88) w następujący sposób:

```Python
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

def _get_token_from_cache(scope=None):
    cache = _load_cache()  # This web app maintains one cache per session
    cca = _build_msal_app(cache)
    accounts = cca.get_accounts()
    if accounts:  # So all accounts belong to the current signed-in user
        result = cca.acquire_token_silent(scope, account=accounts[0])
        _save_cache(cache)
        return result

```

---

Po zalogowaniu się użytkownika do aplikacji należy włączyć ich wylogowywanie.

## <a name="sign-out"></a>Wylogowywanie

Wylogowywanie z aplikacji sieci Web obejmuje więcej niż usuwanie informacji o koncie zalogowanym ze stanu aplikacji sieci Web.
Aplikacja sieci Web musi również przekierować użytkownika do punktu końcowego platformy tożsamości firmy Microsoft, `logout` Aby się wylogować.

Gdy aplikacja internetowa przekierowuje użytkownika do `logout` punktu końcowego, ten punkt końcowy czyści sesję użytkownika z przeglądarki. Jeśli Twoja aplikacja nie przejdzie do `logout` punktu końcowego, użytkownik będzie ponownie uwierzytelniać się w aplikacji bez konieczności ponownego wprowadzania poświadczeń. Przyczyną jest to, że będzie ona mieć prawidłową sesję logowania jednokrotnego z punktem końcowym platformy tożsamości firmy Microsoft.

Aby dowiedzieć się więcej, zobacz sekcję [Wysyłanie żądania wylogowania](v2-protocols-oidc.md#send-a-sign-out-request) z [platformy tożsamości firmy Microsoft i dokumentacji protokołu OpenID Connect Connect](v2-protocols-oidc.md) .

### <a name="application-registration"></a>Rejestracja aplikacji

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Podczas rejestracji aplikacji rejestrujesz identyfikator URI po wylogowaniu. W naszym samouczku zarejestrowano `https://localhost:44321/signout-oidc` się w polu **adres URL wylogowania** w sekcji **Ustawienia zaawansowane** na stronie **uwierzytelnianie** . Aby uzyskać szczegółowe informacje, zobacz [ Rejestrowanie aplikacji webApp](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-1-MyOrg#register-the-webapp-app-webapp).

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

Podczas rejestracji aplikacji rejestrujesz identyfikator URI po wylogowaniu. W naszym samouczku zarejestrowano `https://localhost:44308/Account/EndSession` się w polu **adres URL wylogowania** w sekcji **Ustawienia zaawansowane** na stronie **uwierzytelnianie** . Aby uzyskać szczegółowe informacje, zobacz [Rejestrowanie aplikacji webApp](https://github.com/Azure-Samples/active-directory-dotnet-web-single-sign-out#register-the-service-app-webapp-distributedsignout-dotnet).

# <a name="java"></a>[Java](#tab/java)

Podczas rejestracji aplikacji rejestrujesz identyfikator URI po wylogowaniu. W naszym samouczku zarejestrowano `http://localhost:8080/msal4jsample/sign_out` się w polu **adres URL wylogowania** w sekcji **Ustawienia zaawansowane** na stronie **uwierzytelnianie** .

# <a name="python"></a>[Python](#tab/python)

Podczas rejestracji aplikacji nie trzeba rejestrować dodatkowego adresu URL wylogowywania. Aplikacja zostanie wywołana z powrotem na swoim głównym adresie URL.

---

### <a name="sign-out-button"></a>Przycisk Wyloguj

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

W ASP.NET, wybierając przycisk **Wyloguj** w aplikacji sieci Web wyzwala `SignOut` akcję na `AccountController` kontrolerze (patrz poniżej)

```html
<ul class="navbar-nav">
  @if (User.Identity.IsAuthenticated)
  {
    <li class="nav-item">
        <span class="navbar-text text-dark">Hello @User.Identity.Name!</span>
    </li>
    <li class="nav-item">
        <a class="nav-link text-dark" asp-area="MicrosoftIdentity" asp-controller="Account" asp-action="SignOut">Sign out</a>
    </li>
  }
  else
  {
    <li class="nav-item">
        <a class="nav-link text-dark" asp-area="MicrosoftIdentity" asp-controller="Account" asp-action="SignIn">Sign in</a>
    </li>
  }
</ul>
```

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

W ASP.NET MVC przycisk Wyloguj jest uwidoczniony w `Views\Shared\_LoginPartial.cshtml` . Jest on wyświetlany tylko wtedy, gdy istnieje uwierzytelnione konto. Oznacza to, że jest wyświetlany, gdy użytkownik zalogował się wcześniej.

```html
@if (Request.IsAuthenticated)
{
    <text>
        <ul class="nav navbar-nav navbar-right">
            <li class="navbar-text">
                Hello, @User.Identity.Name!
            </li>
            <li>
                @Html.ActionLink("Sign out", "SignOut", "Account")
            </li>
        </ul>
    </text>
}
else
{
    <ul class="nav navbar-nav navbar-right">
        <li>@Html.ActionLink("Sign in", "SignIn", "Account", routeValues: null, htmlAttributes: new { id = "loginLink" })</li>
    </ul>
}
```

# <a name="java"></a>[Java](#tab/java)

W naszym przewodniku szybki start dla języka Java przycisk Wyloguj znajduje się w pliku Main/sources/templates/auth_page.html.

```html
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
<body>

<form action="/msal4jsample/sign_out">
    <input type="submit" value="Sign out">
</form>
...
```

# <a name="python"></a>[Python](#tab/python)

W przewodniku szybki start w języku Python przycisk Wyloguj znajduje się w pliku [templates/index.html # L10](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/templates/index.html#L10) .

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
</head>
<body>
    <h1>Microsoft Identity Python web app</h1>
    Welcome {{ user.get("name") }}!
    <li><a href='/graphcall'>Call Microsoft Graph API</a></li>
    <li><a href="/logout">Logout</a></li>
</body>
</html>
```

---

### <a name="signout-action-of-the-controller"></a>`SignOut` Akcja kontrolera

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

W poprzednich wersjach szablonów ASP.NET Core `Account` kontroler został osadzony w aplikacji sieci Web. To nie jest już przypadek, ponieważ kontroler jest teraz częścią pakietu NuGet **Microsoft. Identity. Web. UI** . Aby uzyskać szczegółowe informacje, zobacz [AccountController.cs](https://github.com/AzureAD/microsoft-identity-web/blob/master/src/Microsoft.Identity.Web.UI/Areas/MicrosoftIdentity/Controllers/AccountController.cs) .

- Ustawia identyfikator URI przekierowania OpenID Connect na `/Account/SignedOut` tak, aby kontroler został wywołany po zakończeniu wylogowywania w usłudze Azure AD.
- Wywołania `Signout()` , dzięki którym program OpenID Connect nawiązuje połączenie z punktem końcowym platformy tożsamości firmy Microsoft `logout` . Następnie punkt końcowy:

  - Czyści plik cookie sesji z przeglądarki.
  - Wywołuje adres URL wylogowania. Domyślnie adres URL wylogowywania wyświetla stronę widoku wylogowanego [SignedOut.cshtml.cs](https://github.com/AzureAD/microsoft-identity-web/blob/master/src/Microsoft.Identity.Web.UI/Areas/MicrosoftIdentity/Pages/Account/SignedOut.cshtml.cs). Ta strona jest również dostępna jako część elementu MIcrosoft. Identity. Web.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

W ASP.NET wylogowanie jest wyzwalane z `SignOut()` metody na kontrolerze (na przykład [elementu AccountController. cs # L25-L31](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Controllers/AccountController.cs#L25-L31)). Ta metoda nie jest częścią ASP.NET Framework, w przeciwieństwie do działania w ASP.NET Core. Go

- Wysyła wyzwanie wylogowania OpenID Connect.
- Czyści pamięć podręczną.
- Przekierowuje do strony, którą chce.

```csharp
/// <summary>
/// Send an OpenID Connect sign-out request.
/// </summary>
public void SignOut()
{
 HttpContext.GetOwinContext()
            .Authentication
            .SignOut(CookieAuthenticationDefaults.AuthenticationType);
 Response.Redirect("/");
}
```

# <a name="java"></a>[Java](#tab/java)

W języku Java wylogowanie jest obsługiwane przez wywołanie punktu końcowego platformy tożsamości firmy Microsoft `logout` bezpośrednio i podanie `post_logout_redirect_uri` wartości. Aby uzyskać szczegółowe informacje, zobacz [AuthPageController. Java # L50-L60](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthPageController.java#L50-L60).

```Java
@RequestMapping("/msal4jsample/sign_out")
    public void signOut(HttpServletRequest httpRequest, HttpServletResponse response) throws IOException {

        httpRequest.getSession().invalidate();

        String endSessionEndpoint = "https://login.microsoftonline.com/common/oauth2/v2.0/logout";

        String redirectUrl = "http://localhost:8080/msal4jsample/";
        response.sendRedirect(endSessionEndpoint + "?post_logout_redirect_uri=" +
                URLEncoder.encode(redirectUrl, "UTF-8"));
    }
```

# <a name="python"></a>[Python](#tab/python)

Kod, który wyloguje użytkownika, znajduje się w [aplikacji App. PR # L46-L52](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/48637475ed7d7733795ebeac55c5d58663714c60/app.py#L47-L48).

```Python
@app.route("/logout")
def logout():
    session.clear()  # Wipe out the user and the token cache from the session
    return redirect(  # Also need to log out from the Microsoft Identity platform
        "https://login.microsoftonline.com/common/oauth2/v2.0/logout"
        "?post_logout_redirect_uri=" + url_for("index", _external=True))
```

---

### <a name="intercepting-the-call-to-the-logout-endpoint"></a>Przechwytywanie wywołania do `logout` punktu końcowego

Identyfikator URI po wylogowaniu umożliwia aplikacjom uczestnictwo w globalnym wylogowaniu.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

ASP.NET Core OpenID Connect łączy oprogramowanie pośredniczące umożliwia aplikacji przechwycenie wywołania do punktu końcowego platformy tożsamości firmy Microsoft `logout` przez podanie zdarzenia połączenia OpenID Connect o nazwie `OnRedirectToIdentityProviderForSignOut` . Jest to obsługiwane automatycznie przez Microsoft. Identity. Web (co czyści konta w przypadku, gdy aplikacja sieci Web wywołuje interfejsy API sieci Web)

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

W ASP.NET można delegować do oprogramowania pośredniczącego w celu przeprowadzenia wylogowania, czyszcząc plik cookie sesji:

```csharp
public class AccountController : Controller
{
 ...
 public void EndSession()
 {
  Request.GetOwinContext().Authentication.SignOut();
  Request.GetOwinContext().Authentication.SignOut(Microsoft.AspNet.Identity.DefaultAuthenticationTypes.ApplicationCookie);
  this.HttpContext.GetOwinContext().Authentication.SignOut(CookieAuthenticationDefaults.AuthenticationType);
 }
}
```

# <a name="java"></a>[Java](#tab/java)

W przewodniku szybki start w języku Java identyfikator URI przekierowania po wylogowaniu po prostu wyświetla stronę index.html.

# <a name="python"></a>[Python](#tab/python)

W przewodniku szybki start w języku Python identyfikator URI przekierowania po wylogowaniu po prostu wyświetla stronę index.html.

---

## <a name="protocol"></a>Protokół

Jeśli chcesz dowiedzieć się więcej o wylogowaniu, Przeczytaj dokumentację dotyczącą protokołu dostępną w programie [Open ID Connect](./v2-protocols-oidc.md).

## <a name="next-steps"></a>Następne kroki

Przejdź do następnego artykułu w tym scenariuszu, [Przejdź do środowiska produkcyjnego](scenario-web-app-sign-user-production.md).