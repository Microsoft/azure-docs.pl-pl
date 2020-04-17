---
title: Pisanie aplikacji sieci Web, która loguje się/wylogowywanie użytkowników - Platforma tożsamości firmy Microsoft | Azure
description: Dowiedz się, jak utworzyć aplikację internetową, która loguje się do/wylogowywania użytkowników
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
ms.openlocfilehash: 2ab5697ceff612e65174fdb7f9ef6137e2c8b9a5
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537070"
---
# <a name="web-app-that-signs-in-users-sign-in-and-sign-out"></a>Aplikacja sieci Web, która loguje się do użytkowników: Logowanie i wylogowywanie się

Dowiedz się, jak dodać logowanie do kodu aplikacji sieci web, która loguje się do użytkowników. Następnie dowiedz się, jak pozwolić im się wylogować.

## <a name="sign-in"></a>Logowanie

Logowanie składa się z dwóch części:

- Przycisk logowania na stronie HTML
- Akcja logowania w kodzie w kontrolerze

### <a name="sign-in-button"></a>Przycisk logowania

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

W ASP.NET Core przycisk logowania jest wymie . `Views\Shared\_LoginPartial.cshtml` Jest wyświetlany tylko wtedy, gdy nie ma uwierzytelnionego konta. Oznacza to, że jest wyświetlany, gdy użytkownik jeszcze się nie zalogował lub wylogował się.

```html
@using Microsoft.Identity.Web
@if (User.Identity.IsAuthenticated)
{
 // Code omitted code for clarity
}
else
{
    <ul class="nav navbar-nav navbar-right">
        <li><a asp-area="AzureAD" asp-controller="Account" asp-action="SignIn">Sign in</a></li>
    </ul>
}
```

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

W ASP.NET MVC przycisk wylogowywania jest `Views\Shared\_LoginPartial.cshtml`eksponowane w pliku . Jest wyświetlany tylko wtedy, gdy istnieje uwierzytelnione konto. Oznacza to, że jest wyświetlany, gdy użytkownik wcześniej zalogował się.

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

W naszym przewodniku Szybki start w języku Java przycisk logowania znajduje się w pliku [main/resources/templates/index.html.](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/master/src/main/resources/templates/index.html)

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

W przewodniku Szybki start języka Python nie ma przycisku logowania. Po wykonaniu kodu automatycznie monituje użytkownika o zalogowanie się, gdy dociera do katalogu głównego aplikacji sieci web. Zobacz [app.py#L14-L18](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/0.1.0/app.py#L14-L18).

```Python
@app.route("/")
def index():
    if not session.get("user"):
        return redirect(url_for("login"))
    return render_template('index.html', user=session["user"])
```

---

### <a name="signin-action-of-the-controller"></a>`SignIn`działania administratora danych

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

W ASP.NET wybranie przycisku **Logowania w** aplikacji sieci web `SignIn` wyzwala `AccountController` akcję na kontrolerze. W poprzednich wersjach szablonów ASP.NET podstawowych `Account` kontroler został osadzony w aplikacji sieci web. Nie jest to już przypadek, ponieważ kontroler jest teraz częścią ASP.NET core framework.

Kod jest `AccountController` dostępny w repozytorium ASP.NET Core w [AccountController.cs](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Controllers/AccountController.cs). Kontrola konta wyzywa użytkownika, przekierowując do punktu końcowego platformy tożsamości firmy Microsoft. Aby uzyskać szczegółowe informacje, zobacz [SignIn](https://github.com/aspnet/AspNetCore/blob/f3e6b74623d42d5164fd5f97a288792c8ad877b6/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Controllers/AccountController.cs#L23-L31) metody pod warunkiem, że jako część ASP.NET Core.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

W ASP.NET wylogowywanie się jest `SignOut()` wyzwalane z metody na kontrolerze (na przykład [AccountController.cs#L16-L23](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Controllers/AccountController.cs#L16-L23)). Ta metoda nie jest częścią ASP.NET framework (w przeciwieństwie do tego, co dzieje się w ASP.NET Core). Wysyła openid wyzwanie logowania po zaproponowaniu przekierowania identyfikatora URI.

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

W języku Java wylogowywanie jest obsługiwane `logout` przez bezpośrednie wywołanie `post_logout_redirect_uri` punktu końcowego platformy tożsamości firmy Microsoft i podanie wartości. Aby uzyskać szczegółowe informacje, zobacz [AuthPageController.java#L30-L48](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthPageController.java#L30-L48).

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

W przeciwieństwie do innych platform, MSAL Python dba o umożliwienie użytkownikowi zalogować się ze strony logowania. Zobacz [app.py#L20-L28](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L20-L28).

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

Metoda `_build_msal_app()` jest zdefiniowana w [app.py#L81-L88](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L81-L88) w następujący sposób:

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

Po zalogowaniu się użytkownika do aplikacji należy włączyć opcję wyloguj się.

## <a name="sign-out"></a>Wylogowywanie

Wylogowywanie się z aplikacji sieci web obejmuje więcej niż usunięcie informacji o koncie logowania ze stanu aplikacji sieci web.
Aplikacja sieci web musi również przekierować `logout` użytkownika do punktu końcowego platformy tożsamości firmy Microsoft, aby się wylogować.

Gdy aplikacja sieci web przekierowuje użytkownika do punktu końcowego, `logout` ten punkt końcowy czyści sesję użytkownika z przeglądarki. Jeśli aplikacja nie została dojdą do punktu końcowego, `logout` użytkownik ponownie uwierzytelnić do aplikacji bez wprowadzania ich poświadczenia ponownie. Powodem jest to, że będą one miały prawidłową sesję logowania jednokrotnego z punktem końcowym platformy tożsamości firmy Microsoft.

Aby dowiedzieć się więcej, zobacz [sekcję Wyślij żądanie wylogowania](v2-protocols-oidc.md#send-a-sign-out-request) na platformie tożsamości firmy Microsoft oraz dokumentację [protokołu OpenID Connect.](v2-protocols-oidc.md)

### <a name="application-registration"></a>Rejestracja aplikacji

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Podczas rejestracji aplikacji rejestrujesz identyfikator URI po wylogowaniu. W naszym samouczku `https://localhost:44321/signout-oidc` zarejestrowałeś się w polu **Adres URL wylogowania** w sekcji **Ustawienia zaawansowane** na stronie **Uwierzytelnianie.** Aby uzyskać szczegółowe informacje, zobacz [Rejestrowanie aplikacji webApp](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-1-MyOrg#register-the-webapp-app-webapp).

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

Podczas rejestracji aplikacji rejestrujesz identyfikator URI po wylogowaniu. W naszym samouczku `https://localhost:44308/Account/EndSession` zarejestrowałeś się w polu **Adres URL wylogowania** w sekcji **Ustawienia zaawansowane** na stronie **Uwierzytelnianie.** Aby uzyskać szczegółowe informacje, zobacz [Rejestrowanie aplikacji webApp](https://github.com/Azure-Samples/active-directory-dotnet-web-single-sign-out#register-the-service-app-webapp-distributedsignout-dotnet).

# <a name="java"></a>[Java](#tab/java)

Podczas rejestracji aplikacji rejestrujesz identyfikator URI po wylogowaniu. W naszym samouczku `http://localhost:8080/msal4jsample/sign_out` zarejestrowałeś się w polu **Adres URL wylogowania** w sekcji **Ustawienia zaawansowane** na stronie **Uwierzytelnianie.**

# <a name="python"></a>[Python](#tab/python)

Podczas rejestracji aplikacji nie trzeba rejestrować dodatkowego adresu URL wylogowania. Aplikacja zostanie wywołana z powrotem na głównym adresie URL.

---

### <a name="sign-out-button"></a>Przycisk Wyloguj

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

W ASP.NET Core przycisk wylogowywania jest `Views\Shared\_LoginPartial.cshtml`wymieciony w pliku . Jest wyświetlany tylko wtedy, gdy istnieje uwierzytelnione konto. Oznacza to, że jest wyświetlany, gdy użytkownik wcześniej zalogował się.

```html
@using Microsoft.Identity.Web
@if (User.Identity.IsAuthenticated)
{
    <ul class="nav navbar-nav navbar-right">
        <li class="navbar-text">Hello @User.GetDisplayName()!</li>
        <li><a asp-area="AzureAD" asp-controller="Account" asp-action="SignOut">Sign out</a></li>
    </ul>
}
else
{
    <ul class="nav navbar-nav navbar-right">
        <li><a asp-area="AzureAD" asp-controller="Account" asp-action="SignIn">Sign in</a></li>
    </ul>
}
```

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

W ASP.NET MVC przycisk wylogowywania jest `Views\Shared\_LoginPartial.cshtml`eksponowane w pliku . Jest wyświetlany tylko wtedy, gdy istnieje uwierzytelnione konto. Oznacza to, że jest wyświetlany, gdy użytkownik wcześniej zalogował się.

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

W naszym przewodniku Java Szybki start przycisk wylogowywania znajduje się w pliku main/resources/templates/auth_page.html.

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

W przewodniku Szybki start języka Python przycisk wylogowywania znajduje się w pliku [templates/index.html#L10.](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/templates/index.html#L10)

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

### <a name="signout-action-of-the-controller"></a>`SignOut`działania administratora danych

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

W ASP.NET wybranie przycisku **Wyloguj** w aplikacji `SignOut` sieci web `AccountController` wyzwala akcję na kontrolerze. W poprzednich wersjach szablonów ASP.NET Core `Account` kontroler został osadzony w aplikacji sieci web. Nie jest to już przypadek, ponieważ kontroler jest teraz częścią ASP.NET core framework.

Kod dla `AccountController` jest dostępny w repozytorium ASP.NET core w [AccountController.cs](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Controllers/AccountController.cs). Kontrola konta:

- Ustawia identyfikator URI przekierowania OpenID do tak, aby `/Account/SignedOut` kontroler jest wywoływany z powrotem po zakończeniu wylogowywania usługi Azure AD.
- Wywołania `Signout()`, które umożliwiają oprogramowaniu pośredniczącemu `logout` OpenID Connect kontaktowanie się z punktem końcowym platformy tożsamości firmy Microsoft. Punkt końcowy następnie:

  - Czyści plik cookie sesji z przeglądarki.
  - Odwołuje adres URL wylogowania. Domyślnie adres URL wylogowania wyświetla stronę widoku [wylogowywanejus.html](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Pages/Account/SignedOut.cshtml). Ta strona jest również dostępna jako część ASP.NET Core.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

W ASP.NET wylogowywanie się jest `SignOut()` wyzwalane z metody na kontrolerze (na przykład [AccountController.cs#L25-L31](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Controllers/AccountController.cs#L25-L31)). Ta metoda nie jest częścią ASP.NET framework, w przeciwieństwie do tego, co dzieje się w ASP.NET Core. To:

- Wysyła wyzwanie wylogowywania OpenID.
- Czyści pamięć podręczną.
- Przekierowuje do strony, która chce.

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

W języku Java wylogowywanie jest obsługiwane `logout` przez bezpośrednie wywołanie `post_logout_redirect_uri` punktu końcowego platformy tożsamości firmy Microsoft i podanie wartości. Aby uzyskać szczegółowe informacje, zobacz [AuthPageController.java#L50-L60](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthPageController.java#L50-L60).

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

Kod, który wyloguje użytkownika jest w [app.py#L46-L52](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/48637475ed7d7733795ebeac55c5d58663714c60/app.py#L47-L48).

```Python
@app.route("/logout")
def logout():
    session.clear()  # Wipe out the user and the token cache from the session
    return redirect(  # Also need to log out from the Microsoft Identity platform
        "https://login.microsoftonline.com/common/oauth2/v2.0/logout"
        "?post_logout_redirect_uri=" + url_for("index", _external=True))
```

---

### <a name="intercepting-the-call-to-the-logout-endpoint"></a>Przechwytywanie wywołania `logout` do punktu końcowego

Identyfikator URI po wylogowaniu umożliwia aplikacjom uczestnictwo w globalnym wylogowaniu.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Oprogramowanie pośredniczące core openid connect ASP.NET umożliwia aplikacji przechwytywanie połączenia `logout` do punktu końcowego platformy `OnRedirectToIdentityProviderForSignOut`tożsamości firmy Microsoft, udostępniając zdarzenie OpenID Connect o nazwie . Na przykład sposobu subskrybowania tego zdarzenia (aby wyczyścić pamięć podręczną tokenów), zobacz [Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L151-L156](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/faa94fd49c2da46b22d6694c4f5c5895795af26d/Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L151-L156).

```csharp
    // Handling the global sign-out
    options.Events.OnRedirectToIdentityProviderForSignOut = async context =>
    {
        // Forget about the signed-in user
    };
```

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

W ASP.NET, można delegować do oprogramowania pośredniczącego do wykonania wylogowania, czyszczenie pliku cookie sesji:

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

W przewodniku Szybki start języka Java funkcja przekierowania po wylogowaniu jest wyświetlana tylko na stronie index.html.

# <a name="python"></a>[Python](#tab/python)

W przewodniku Szybki start języka Python przekierowanie po wylogowaniu URI wyświetla tylko stronę index.html.

---

## <a name="protocol"></a>Protocol (Protokół)

Jeśli chcesz dowiedzieć się więcej o wylogowywania się, przeczytaj dokumentację protokołu dostępną w [open id connect](./v2-protocols-oidc.md).

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Przenoszenie do środowiska produkcyjnego](scenario-web-app-sign-user-production.md)
