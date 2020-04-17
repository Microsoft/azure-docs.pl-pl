---
title: Dodawanie logowania do ASP.NET aplikacji sieci Web platformy tożsamości firmy Microsoft
titleSuffix: Microsoft identity platform
description: Implementowanie logowania firmy Microsoft w rozwiązaniu ASP.NET przy użyciu tradycyjnej aplikacji opartej na przeglądarce internetowej i standardu OpenID Connect
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 08/28/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 29f5a48feaaafee64a20745b3cdf09726a6372ac
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81533841"
---
# <a name="add-sign-in-to-microsoft-to-an-aspnet-web-app"></a>Dodawanie logowania do firmy Microsoft do ASP.NET aplikacji sieci Web

W tym przewodniku pokazano, jak zaimplementować logowanie do firmy Microsoft za pośrednictwem ASP.NET rozwiązania MVC przy użyciu tradycyjnej aplikacji opartej na przeglądarce sieci Web i OpenID Connect.

Po wypełnieniu tego przewodnika aplikacja będzie mogła akceptować logowania kont osobistych od outlook.com i live.com. Ponadto konta służbowe i szkolne z dowolnej firmy lub organizacji zintegrowanej z platformą tożsamości firmy Microsoft będą mogły logować się do aplikacji.

> Ten przewodnik wymaga programu Microsoft Visual Studio 2019.  Nie masz tego programu?  [Pobierz program Visual Studio 2019 za darmo](https://www.visualstudio.com/downloads/).

>[!NOTE]
> Jeśli jesteś nowy na platformie tożsamości firmy Microsoft, zalecamy rozpoczęcie logowania się do [platformy tożsamości Microsoft do aplikacji sieci Web ASP.NET](quickstart-v2-aspnet-webapp.md).

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>Jak działa przykładowa aplikacja generowana przez ten przewodnik

![Pokazuje, jak działa przykładowa aplikacja generowana przez ten samouczek](media/active-directory-develop-guidedsetup-aspnetwebapp-intro/aspnetbrowsergeneral.svg)

Przykładowa aplikacja, którą tworzysz, jest oparta na scenariuszu, w którym używasz przeglądarki do uzyskiwania dostępu do ASP.NET witryny sieci Web, która monituje użytkownika o uwierzytelnienie za pomocą przycisku logowania. W tym scenariuszu większość działań związanych z renderowaniem strony internetowej odbywa się po stronie serwera.

## <a name="libraries"></a>Biblioteki

W tym przewodniku używane są następujące biblioteki:

|Biblioteka|Opis|
|---|---|
|[Microsoft.Owin.Security.OpenIdConnect](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect/)|Oprogramowanie pośredniczące, które umożliwia aplikacji wykorzystywanie do uwierzytelniania protokołu OpenIdConnect|
|[Microsoft.Owin.Security.Cookies](https://www.nuget.org/packages/Microsoft.Owin.Security.Cookies)|Oprogramowanie pośredniczące, które umożliwia aplikacji utrzymanie sesji użytkownika przy użyciu plików cookie|
|[Microsoft.Owin.Host.SystemWeb](https://www.nuget.org/packages/Microsoft.Owin.Host.SystemWeb)|Oprogramowanie pośredniczące, które umożliwia uruchamianie aplikacji opartych na sieci OWIN w internetowych usługach informacyjnych (IIS) przy użyciu potoku żądania ASP.NET|

## <a name="set-up-your-project"></a>konfigurowanie projektu

W tej sekcji opisano sposób instalowania i konfigurowania potoku uwierzytelniania za pośrednictwem oprogramowania pośredniczącego OWIN w projekcie ASP.NET przy użyciu funkcji OpenID Connect.

> Wolisz zamiast tego przykładowego projektu programu Visual Studio? [Pobierz projekt](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-DotNet/archive/master.zip) i przejdź do [zarejestruj aplikację,](#register-your-application) aby skonfigurować przykład kodu przed wykonaniem.

### <a name="create-your-aspnet-project"></a>tworzenie projektu ASP.NET

1. W programie Visual Studio: przejdź do **pliku** > **nowego** > **projektu**.
2. W pozycji **Visual C#\Internet** wybierz opcję **Aplikacja internetowa ASP.NET (.NET Framework)**.
3. Nadaj nazwę aplikacji, a następnie wybierz przycisk **OK**.
4. Wybierz **pozycję Puste**, a następnie zaznacz pole wyboru, aby dodać odwołania **MVC.**

## <a name="add-authentication-components"></a>dodawanie składników uwierzytelniania

1. W programie Visual Studio: przejdź do**konsoli Menedżera pakietów menedżera** **pakietów** > narzędzia**Nuget Package Manager** > .
2. Dodaj *pakiety NuGet oprogramowania pośredniczącego OWIN*, wpisując następujące polecenie w oknie Konsola menedżera pakietów:

    ```powershell
    Install-Package Microsoft.Owin.Security.OpenIdConnect
    Install-Package Microsoft.Owin.Security.Cookies
    Install-Package Microsoft.Owin.Host.SystemWeb
    ```

<!--start-collapse-->
> ### <a name="about-these-libraries"></a>Informacje o tych bibliotekach
> Biblioteki te umożliwiają logowanie jednokrotne za pomocą openid connect za pomocą uwierzytelniania opartego na plikach cookie. Po zakończeniu uwierzytelniania i wysłaniu tokenu reprezentującego użytkownika do aplikacji oprogramowanie pośredniczące OWIN tworzy plik cookie sesji. Następnie przeglądarka używa tego pliku cookie w kolejnych żądaniach, aby użytkownik nie musiał ponownie wpisywać hasła i nie jest wymagana żadna dodatkowa weryfikacja.
<!--end-collapse-->

## <a name="configure-the-authentication-pipeline"></a>konfigurowanie potoku uwierzytelniania

Poniższe kroki są używane do tworzenia klasy uruchamiania oprogramowania pośredniczącego OWIN w celu skonfigurowania uwierzytelniania OpenID Connect. Ta klasa jest wykonywana automatycznie po uruchomieniu procesu IIS.

> [!TIP]
> Jeśli projekt nie ma pliku `Startup.cs` w folderze głównym:
> 1. Kliknij prawym przyciskiem myszy folder główny projektu, a następnie wybierz polecenie **Dodaj** > **nową** > **klasę uruchamiania OWIN**.<br/>
> 2. Nazwij go **Startup.cs**.
>
>> Upewnij się, że wybrana klasa jest klasą uruchamiania OWIN, a nie standardową klasą C#. Potwierdź to, sprawdzając, czy widzisz [assembly: OwinStartup(typeof({NameSpace}. Uruchamianie))] nad obszarem nazw.

1. Dodaj odwołania *OWIN* i *Microsoft.IdentityModel* do Startup.cs:

    ```csharp
    using Microsoft.Owin;
    using Owin;
    using Microsoft.IdentityModel.Protocols.OpenIdConnect;
    using Microsoft.IdentityModel.Tokens;
    using Microsoft.Owin.Security;
    using Microsoft.Owin.Security.Cookies;
    using Microsoft.Owin.Security.OpenIdConnect;
    using Microsoft.Owin.Security.Notifications;
    ```

2. Zastąp klasę początkową następującym kodem:

    ```csharp
    public class Startup
    {
        // The Client ID is used by the application to uniquely identify itself to Microsoft identity platform.
        string clientId = System.Configuration.ConfigurationManager.AppSettings["ClientId"];

        // RedirectUri is the URL where the user will be redirected to after they sign in.
        string redirectUri = System.Configuration.ConfigurationManager.AppSettings["RedirectUri"];

        // Tenant is the tenant ID (e.g. contoso.onmicrosoft.com, or 'common' for multi-tenant)
        static string tenant = System.Configuration.ConfigurationManager.AppSettings["Tenant"];

        // Authority is the URL for authority, composed by Microsoft identity platform endpoint and the tenant name (e.g. https://login.microsoftonline.com/contoso.onmicrosoft.com/v2.0)
        string authority = String.Format(System.Globalization.CultureInfo.InvariantCulture, System.Configuration.ConfigurationManager.AppSettings["Authority"], tenant);

        /// <summary>
        /// Configure OWIN to use OpenIdConnect
        /// </summary>
        /// <param name="app"></param>
        public void Configuration(IAppBuilder app)
        {
            app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

            app.UseCookieAuthentication(new CookieAuthenticationOptions());
            app.UseOpenIdConnectAuthentication(
                new OpenIdConnectAuthenticationOptions
                {
                    // Sets the ClientId, authority, RedirectUri as obtained from web.config
                    ClientId = clientId,
                    Authority = authority,
                    RedirectUri = redirectUri,
                    // PostLogoutRedirectUri is the page that users will be redirected to after sign-out. In this case, it is using the home page
                    PostLogoutRedirectUri = redirectUri,
                    Scope = OpenIdConnectScope.OpenIdProfile,
                    // ResponseType is set to request the id_token - which contains basic information about the signed-in user
                    ResponseType = OpenIdConnectResponseType.IdToken,
                    // ValidateIssuer set to false to allow personal and work accounts from any organization to sign in to your application
                    // To only allow users from a single organizations, set ValidateIssuer to true and 'tenant' setting in web.config to the tenant name
                    // To allow users from only a list of specific organizations, set ValidateIssuer to true and use ValidIssuers parameter
                    TokenValidationParameters = new TokenValidationParameters()
                    {
                        ValidateIssuer = false // This is a simplification
                    },
                    // OpenIdConnectAuthenticationNotifications configures OWIN to send notification of failed authentications to OnAuthenticationFailed method
                    Notifications = new OpenIdConnectAuthenticationNotifications
                    {
                        AuthenticationFailed = OnAuthenticationFailed
                    }
                }
            );
        }

        /// <summary>
        /// Handle failed authentication requests by redirecting the user to the home page with an error in the query string
        /// </summary>
        /// <param name="context"></param>
        /// <returns></returns>
        private Task OnAuthenticationFailed(AuthenticationFailedNotification<OpenIdConnectMessage, OpenIdConnectAuthenticationOptions> context)
        {
            context.HandleResponse();
            context.Response.Redirect("/?errormessage=" + context.Exception.Message);
            return Task.FromResult(0);
        }
    }
    ```

> [!NOTE]
> Ustawienie `ValidateIssuer = false` jest uproszczeniem dla tego szybkiego startu. W rzeczywistych aplikacjach należy sprawdzić poprawność wystawcy.
> Zobacz przykłady, aby dowiedzieć się, jak to zrobić.

<!--start-collapse-->
> ### <a name="more-information"></a>Więcej informacji
> Parametry, które można podać w *OpenIDConnectAuthenticationOptions* służyć jako współrzędne dla aplikacji do komunikowania się z platformą tożsamości firmy Microsoft. Ponieważ oprogramowanie pośredniczące OpenID Connect używa plików cookie w tle, należy również skonfigurować uwierzytelnianie plików cookie, jak pokazuje poprzedni kod. Wartość *ValidateIssuer* informuje OpenIdConnect, aby nie ograniczała dostępu do jednej określonej organizacji.
<!--end-collapse-->

## <a name="add-a-controller-to-handle-sign-in-and-sign-out-requests"></a>dodawanie kontrolera do obsługi żądań logowania i wylogowania

Aby utworzyć nowy kontroler, aby udostępnić metody logowania i wylogowania, wykonaj następujące kroki:

1.  Kliknij prawym przyciskiem myszy folder **Kontrolery** i wybierz polecenie **Dodaj** > **kontroler**.
2.  Wybierz pozycję **Kontroler MVC (wersja platformy .NET) — Pusty**.
3.  Wybierz pozycję **Dodaj**.
4.  Nazwij go **HomeController,** a następnie wybierz pozycję **Dodaj**.
5.  Dodaj odwołania OWIN do klasy:

    ```csharp
    using Microsoft.Owin.Security;
    using Microsoft.Owin.Security.Cookies;
    using Microsoft.Owin.Security.OpenIdConnect;
    ```

6. Dodaj następujące dwie metody obsługi logowania i wylogowania się do kontrolera, inicjując wyzwanie uwierzytelniania:

    ```csharp
    /// <summary>
    /// Send an OpenID Connect sign-in request.
    /// Alternatively, you can just decorate the SignIn method with the [Authorize] attribute
    /// </summary>
    public void SignIn()
    {
        if (!Request.IsAuthenticated)
        {
            HttpContext.GetOwinContext().Authentication.Challenge(
                new AuthenticationProperties{ RedirectUri = "/" },
                OpenIdConnectAuthenticationDefaults.AuthenticationType);
        }
    }

    /// <summary>
    /// Send an OpenID Connect sign-out request.
    /// </summary>
    public void SignOut()
    {
        HttpContext.GetOwinContext().Authentication.SignOut(
                OpenIdConnectAuthenticationDefaults.AuthenticationType,
                CookieAuthenticationDefaults.AuthenticationType);
    }
    ```

## <a name="create-the-apps-home-page-for-user-sign-in"></a>Tworzenie strony głównej aplikacji do logowania się użytkownika

W programie Visual Studio utwórz nowy widok, aby dodać przycisk logowania i wyświetlić informacje o użytkowniku po uwierzytelnieniu:

1.  Kliknij prawym przyciskiem myszy folder **Views\Home** i wybierz polecenie **Dodaj widok**.
2.  Nazwij nowy widok **Indeks**.
3.  Dodaj do pliku następujący kod HTML, który zawiera przycisk logowania:

    ```html
    <html>
    <head>
        <meta name="viewport" content="width=device-width" />
        <title>Sign in with Microsoft Guide</title>
    </head>
    <body>
    @if (!Request.IsAuthenticated)
    {
        <!-- If the user is not authenticated, display the sign-in button -->
        <a href="@Url.Action("SignIn", "Home")" style="text-decoration: none;">
            <svg xmlns="http://www.w3.org/2000/svg" xml:space="preserve" width="300px" height="50px" viewBox="0 0 3278 522" class="SignInButton">
            <style type="text/css">.fil0:hover {fill: #4B4B4B;} .fnt0 {font-size: 260px;font-family: 'Segoe UI Semibold', 'Segoe UI'; text-decoration: none;}</style>
            <rect class="fil0" x="2" y="2" width="3174" height="517" fill="black" />
            <rect x="150" y="129" width="122" height="122" fill="#F35325" />
            <rect x="284" y="129" width="122" height="122" fill="#81BC06" />
            <rect x="150" y="263" width="122" height="122" fill="#05A6F0" />
            <rect x="284" y="263" width="122" height="122" fill="#FFBA08" />
            <text x="470" y="357" fill="white" class="fnt0">Sign in with Microsoft</text>
            </svg>
        </a>
    }
    else
    {
        <span><br/>Hello @System.Security.Claims.ClaimsPrincipal.Current.FindFirst("name").Value;</span>
        <br /><br />
        @Html.ActionLink("See Your Claims", "Index", "Claims")
        <br /><br />
        @Html.ActionLink("Sign out", "SignOut", "Home")
    }
    @if (!string.IsNullOrWhiteSpace(Request.QueryString["errormessage"]))
    {
        <div style="background-color:red;color:white;font-weight: bold;">Error: @Request.QueryString["errormessage"]</div>
    }
    </body>
    </html>
    ```

<!--start-collapse-->
> ### <a name="more-information"></a>Więcej informacji
>  Ta strona dodaje przycisk logowania w formacie SVG z czarnym tłem:<br/>![Zaloguj się przy użyciu konta Microsoft](media/active-directory-develop-guidedsetup-aspnetwebapp-use/aspnetsigninbuttonsample.png)<br/> Aby uzyskać więcej przycisków logowania, przejdź do [wytycznych dotyczących znakowania](https://docs.microsoft.com/azure/active-directory/develop/active-directory-branding-guidelines "Wytyczne dotyczące oznaczania marką").
<!--end-collapse-->

## <a name="add-a-controller-to-display-users-claims"></a>Dodawanie kontrolera do wyświetlania oświadczeń użytkownika
Ten kontroler pokazuje wykorzystanie atrybutu `[Authorize]` do ochrony kontrolera. Ten atrybut ogranicza dostęp do kontrolera, zezwalając tylko uwierzytelnionym użytkownikom. Poniższy kod używa atrybutu do wyświetlania oświadczeń użytkownika, które zostały pobrane w ramach logowania:

1.  Kliknij prawym przyciskiem myszy folder **Kontrolery,** a następnie wybierz polecenie **Dodaj** > **kontroler**.
2.  Wybierz pozycję **Kontroler MVC {version} — Pusty**.
3.  Wybierz pozycję **Dodaj**.
4.  Nadaj mu nazwę **ClaimsController**.
5.  Zastąp kod klasy kontrolera następującym kodem. Spowoduje to `[Authorize]` dodanie atrybutu do klasy:

    ```csharp
    [Authorize]
    public class ClaimsController : Controller
    {
        /// <summary>
        /// Add user's claims to viewbag
        /// </summary>
        /// <returns></returns>
        public ActionResult Index()
        {
            var userClaims = User.Identity as System.Security.Claims.ClaimsIdentity;

            //You get the user’s first and last name below:
            ViewBag.Name = userClaims?.FindFirst("name")?.Value;

            // The 'preferred_username' claim can be used for showing the username
            ViewBag.Username = userClaims?.FindFirst("preferred_username")?.Value;

            // The subject/ NameIdentifier claim can be used to uniquely identify the user across the web
            ViewBag.Subject = userClaims?.FindFirst(System.Security.Claims.ClaimTypes.NameIdentifier)?.Value;

            // TenantId is the unique Tenant Id - which represents an organization in Azure AD
            ViewBag.TenantId = userClaims?.FindFirst("http://schemas.microsoft.com/identity/claims/tenantid")?.Value;

            return View();
        }
    }
    ```

<!--start-collapse-->
> ### <a name="more-information"></a>Więcej informacji
> Ze względu na `[Authorize]` użycie atrybutu wszystkie metody tego kontrolera mogą być wykonywane tylko wtedy, gdy użytkownik jest uwierzytelniony. Jeśli użytkownik nie jest uwierzytelniony i próbuje uzyskać dostęp do kontrolera, OWIN inicjuje wyzwanie uwierzytelniania i wymusza na użytkowniku uwierzytelnienie. Poprzedni kod analizuje listę oświadczeń dla określonych atrybutów użytkownika zawartych w tokenie identyfikatora użytkownika. Te atrybuty obejmują imię i nazwisko użytkownika oraz nazwę użytkownika, a także podmiot globalnego identyfikatora. Zawiera on także *identyfikator dzierżawy*, który reprezentuje identyfikator organizacji użytkownika.
<!--end-collapse-->

## <a name="create-a-view-to-display-the-users-claims"></a>tworzenie widoku w celu wyświetlenia oświadczeń użytkownika

W programie Visual Studio utwórz nowy widok w celu wyświetlenia oświadczeń użytkownika na stronie internetowej:

1.  Kliknij prawym przyciskiem myszy folder **Widoki\Oświadczenia,** a następnie wybierz polecenie **Dodaj widok**.
2.  Nazwij nowy widok **Indeks**.
3.  Dodaj następujący kod HTML do pliku:

    ```html
    <html>
    <head>
        <meta name="viewport" content="width=device-width" />
        <title>Sign in with Microsoft Sample</title>
        <link href="@Url.Content("~/Content/bootstrap.min.css")" rel="stylesheet" type="text/css" />
    </head>
    <body style="padding:50px">
        <h3>Main Claims:</h3>
        <table class="table table-striped table-bordered table-hover">
            <tr><td>Name</td><td>@ViewBag.Name</td></tr>
            <tr><td>Username</td><td>@ViewBag.Username</td></tr>
            <tr><td>Subject</td><td>@ViewBag.Subject</td></tr>
            <tr><td>TenantId</td><td>@ViewBag.TenantId</td></tr>
        </table>
        <br />
        <h3>All Claims:</h3>
        <table class="table table-striped table-bordered table-hover table-condensed">
        @foreach (var claim in System.Security.Claims.ClaimsPrincipal.Current.Claims)
        {
            <tr><td>@claim.Type</td><td>@claim.Value</td></tr>
        }
        </table>
        <br />
        <br />
        @Html.ActionLink("Sign out", "SignOut", "Home", null, new { @class = "btn btn-primary" })
    </body>
    </html>
    ```

## <a name="register-your-application"></a>Rejestrowanie aplikacji

Aby zarejestrować aplikację i dodać informacje rejestracyjne aplikacji do rozwiązania, masz dwie opcje:

### <a name="option-1-express-mode"></a>Opcja 1: Tryb ekspresowy

Aby szybko zarejestrować aplikację, wykonaj następujące czynności:

1. Przejdź do nowego okienka [Azure portal — rejestracje aplikacji.](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/AspNetWebAppQuickstartPage/sourceType/docs)
1. Wprowadź nazwę aplikacji i wybierz pozycję **Zarejestruj**.
1. Postępuj zgodnie z instrukcjami, aby pobrać i automatycznie skonfigurować nową aplikację za pomocą jednego kliknięcia.

### <a name="option-2-advanced-mode"></a>Opcja 2: Tryb zaawansowany

Aby ręcznie zarejestrować aplikację i dodać informacje na temat rejestracji aplikacji do rozwiązania, wykonaj następujące czynności:

1. Otwórz program Visual Studio, a następnie:
   1. w Eksploratorze rozwiązań wybierz projekt i wyświetl okno Właściwości (jeśli nie widzisz okna Właściwości, naciśnij klawisz F4).
   1. Zmień włączony protokół SSL na `True`.
   1. Kliknij prawym przyciskiem myszy projekt w programie Visual Studio, wybierz polecenie **Właściwości**, a następnie wybierz kartę **Sieć Web.** W sekcji **Serwery** zmień ustawienie **Adres URL projektu** na adres URL **SSL**.
   1. Skopiuj adres URL SSL. Ten adres URL zostanie dodasz do listy adresów URL przekierowań na liście adresów URL przekierowania w portalu rejestracji w następnym kroku.<br/><br/>![Właściwości projektu](media/active-directory-develop-guidedsetup-aspnetwebapp-configure/vsprojectproperties.png)<br />
1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta służbowego lub szkolnego lub przy użyciu osobistego konta Microsoft.
1. Jeśli twoje konto daje dostęp do więcej niż jednej dzierżawy, wybierz swoje konto w prawym górnym rogu i ustaw sesję portalu na dzierżawę usługi Azure AD, która ma.
1. Przejdź do platformy tożsamości firmy Microsoft dla deweloperów [Rejestracje aplikacji.](https://go.microsoft.com/fwlink/?linkid=2083908)
1. Wybierz **pozycję Nowa rejestracja**.
1. Po wyświetleniu strony **Rejestrowanie aplikacji** podaj informacje dotyczące rejestracji aplikacji:
   1. W sekcji **Nazwa** wprowadź znaczącą nazwę aplikacji, która będzie wyświetlana użytkownikom aplikacji, na przykład **ASPNET-Tutorial**.
   1. Dodaj adres URL SSL skopiowany z programu Visual Studio `https://localhost:44368/`w kroku 1 (na przykład) w **adresie URL odpowiedzi**i wybierz pozycję **Zarejestruj**.
1. Wybierz menu **Uwierzytelnianie,** wybierz **polecenie Tokeny identyfikatorów** w obszarze **Niejawna dotacja,** a następnie wybierz pozycję **Zapisz**.
1. Dodaj następujące elementy w pliku web.config, znajdującym `configuration\appSettings` się w folderze głównym w sekcji:

    ```xml
    <add key="ClientId" value="Enter_the_Application_Id_here" />
    <add key="redirectUri" value="Enter_the_Redirect_URL_here" />
    <add key="Tenant" value="common" />
    <add key="Authority" value="https://login.microsoftonline.com/{0}/v2.0" />
    ```

1. Zastąp `ClientId` identyfikator aplikacji, który właśnie zarejestrowałeś.
1. Zamień `redirectUri` go na adres SSL projektu.

## <a name="test-your-code"></a>testowanie kodu

Aby przetestować aplikację w programie Visual Studio, naciśnij klawisz F5, aby uruchomić projekt. Przeglądarka zostanie otwarta w<span></span>http:// lokalizacji localhost:{port}, a zostanie wyświetlony przycisk Zaloguj się za pomocą firmy **Microsoft.** Wybierz przycisk, aby rozpocząć proces logowania.

Gdy będziesz gotowy do uruchomienia testu, użyj konta usługi Azure AD (konta służbowego) lub osobistego konta Microsoft<span>(na żywo).</span> com lub <span>outlook.</span> com), aby się zalogować.

![Zaloguj się przy użyciu konta Microsoft](media/active-directory-develop-guidedsetup-aspnetwebapp-test/aspnetbrowsersignin.png)
<br/><br/>
![Logowanie się do konta Microsoft](media/active-directory-develop-guidedsetup-aspnetwebapp-test/aspnetbrowsersignin2.png)

<!--start-collapse-->
> ###  <a name="permissions-and-consent-in-the-microsoft-identity-platform-endpoint"></a>Uprawnienia i zgoda w punkcie końcowym platformy tożsamości firmy Microsoft
>  Aplikacje integrowane z platformą tożsamości firmy Microsoft są zgodne z modelem autoryzacji, który daje użytkownikom i administratorom kontrolę nad tym, jak można uzyskać dostęp do danych. Gdy użytkownik uwierzytelni się za pomocą platformy tożsamości firmy Microsoft, aby uzyskać dostęp do tej aplikacji, zostanie poproszony o wyrażenie zgody na uprawnienia wymagane przez aplikację ("Wyświetl swój profil podstawowy" i "Zachowaj dostęp do danych, do których użytkownik udzielił mu dostępu"). Po zaakceptowaniu tych uprawnień użytkownik będzie kontynuował wyniki aplikacji. Jednak użytkownik może zamiast tego zostać poproszony o stronę **Zgody administratora need,** jeśli wystąpi którakolwiek z następujących czynności:
>  > - Deweloper aplikacji dodaje wszelkie dodatkowe uprawnienia, które wymagają **zgody administratora**.
>  > - Lub dzierżawa jest skonfigurowana (w **aplikacji dla przedsiębiorstw -> ustawienia użytkownika),** gdzie użytkownicy nie mogą wyrazić zgody na aplikacje uzyskujące dostęp do danych firmy w ich imieniu.
>
> Aby uzyskać więcej informacji, zobacz [Uprawnienia i zgoda w punkcie końcowym platformy tożsamości firmy Microsoft](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent).
<!--end-collapse-->

#### <a name="view-application-results"></a>Wyświetlanie wyników aplikacji

Po zalogowaniu użytkownik zostanie przekierowany na stronę główną witryny. Strona główna to adres URL HTTPS określony w informacjach o rejestracji aplikacji w portalu rejestracji aplikacji firmy Microsoft. Strona główna zawiera wiadomość powitalną *"Hello \<user>",* łącze do wylogowania i łącze umożliwiające wyświetlenie oświadczeń użytkownika. Łącze oświadczeń użytkownika łączy się z kontrolerem oświadczeń utworzonym wcześniej.

### <a name="view-the-users-claims"></a>Wyświetlanie oświadczeń użytkownika

Aby wyświetlić oświadczenia użytkownika, wybierz łącze do przeglądania widoku kontrolera, który jest dostępny tylko dla uwierzytelnionych użytkowników.

#### <a name="view-the-claims-results"></a>Wyświetlanie wyników oświadczeń

Po przejściu do widoku kontrolera powinna zostać wyświetlone tabela zawierająca podstawowe właściwości użytkownika:

|Właściwość |Wartość |Opis |
|---|---|---|
|**Nazwa** |Imię i nazwisko użytkownika | Imię i nazwisko użytkownika
|**Nazwę użytkownika** |Użytkownika<span>@domain.com</span> | Nazwa użytkownika używana do identyfikacji użytkownika|
|**Temat** |Podmiot |Ciąg, który jednoznacznie identyfikuje użytkownika w sieci Web|
|**Tenant ID (Identyfikator dzierżawy)** |Guid (identyfikator GUID) | Identyfikator **guid,** który jednoznacznie reprezentuje organizację usługi Azure AD użytkownika|

Ponadto powinna zostać wyświetleni tabela wszystkich oświadczeń, które znajdują się w żądaniu uwierzytelniania. Aby uzyskać więcej informacji, zobacz [listę oświadczeń, które znajdują się w tokenie identyfikatora](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims).

### <a name="test-access-to-a-method-that-has-an-authorize-attribute-optional"></a>Testuj dostęp do metody, która ma atrybut Authorize (opcjonalnie)

Aby przetestować dostęp jako użytkownik anonimowy do kontrolera, który jest chroniony przez `Authorize` atrybut, wykonaj następujące kroki:

1. Wybierz łącze, aby wylogować użytkownika i zakończyć proces wylogowywania.
2. W przeglądarce wpisz http://<span></span>localhost:{port}/claims, aby uzyskać dostęp do `Authorize` kontrolera chronionego przez ten atrybut.

#### <a name="expected-results-after-access-to-a-protected-controller"></a>Oczekiwane wyniki po dostępie do chronionego kontrolera

Zostanie wyświetlony monit o uwierzytelnienie w celu użycia widoku chronionego kontrolera.

## <a name="advanced-options"></a>Opcje zaawansowane

<!--start-collapse-->
### <a name="protect-your-entire-website"></a>Chroń całą swoją witrynę
Aby chronić całą witrynę sieci Web, w `AuthorizeAttribute` pliku **Global.asax** dodaj atrybut do filtru `GlobalFilters` w metodzie: `Application_Start`

```csharp
GlobalFilters.Filters.Add(new AuthorizeAttribute());
```
<!--end-collapse-->

### <a name="restrict-who-can-sign-in-to-your-application"></a>Ograniczanie tego, kto może zalogować się do aplikacji

Domyślnie podczas tworzenia aplikacji utworzonej przez ten przewodnik aplikacja będzie akceptować logowania kont osobistych (w tym outlook.com, live.com i innych), a także kont służbowych i szkolnych z dowolnej firmy lub organizacji zintegrowanej z platformą tożsamości firmy Microsoft. Jest to zalecana opcja dla aplikacji SaaS.

Aby ograniczyć dostęp do logowania użytkownika dla aplikacji, dostępnych jest wiele opcji.

#### <a name="option-1-restrict-users-from-only-one-organizations-active-directory-instance-to-sign-in-to-your-application-single-tenant"></a>Opcja 1: Ograniczanie użytkownikom tylko jednego wystąpienia usługi Active Directory w organizacji do logowania się do aplikacji (pojedynczej dzierżawy)

Ta opcja jest często używana dla *aplikacji lob:* Jeśli chcesz, aby aplikacja akceptowała logowania tylko z kont należących do określonego wystąpienia usługi Azure AD (w tym *kont gościa* tego wystąpienia), wykonaj następujące kroki:

1. W pliku web.config zmień wartość `Tenant` parametru `Common` z nazwy dzierżawy organizacji, `contoso.onmicrosoft.com`na przykład .
2. W [klasie uruchamiania OWIN](#configure-the-authentication-pipeline)ustaw `true`argument na `ValidateIssuer` .

#### <a name="option-2-restrict-access-to-users-in-a-specific-list-of-organizations"></a>Opcja 2: Ograniczanie dostępu do użytkowników na określonej liście organizacji

Można ograniczyć dostęp do logowania tylko do tych kont użytkowników, które znajdują się w organizacji usługi Azure AD, która znajduje się na liście dozwolonych organizacji:
1. W [klasie uruchamiania OWIN](#configure-the-authentication-pipeline)ustaw `true`argument na `ValidateIssuer` .
2. Ustaw wartość parametru `ValidIssuers` na listę dozwolonych organizacji.

#### <a name="option-3-use-a-custom-method-to-validate-issuers"></a>Opcja 3: Użyj metody niestandardowej, aby zweryfikować wystawców

Można zaimplementować metodę niestandardową do sprawdzania poprawności wystawców przy użyciu **parametru IssuerValidator.** Aby uzyskać więcej informacji na temat używania tego [parametru, zobacz TokenValidationParameters class](/previous-versions/visualstudio/dn464192(v=vs.114)).

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak aplikacje sieci Web mogą wywoływać internetowe interfejsy API.

### <a name="learn-how-to-create-the-application-used-in-this-quickstart-guide"></a>Dowiedz się, jak utworzyć aplikację używaną w tym przewodniku szybki start

Dowiedz się więcej o aplikacjach sieci Web wywołujących internetowe interfejsy API za pomocą platformy tożsamości firmy Microsoft:

> [!div class="nextstepaction"]
> [Aplikacje sieci Web wywołujące internetowe interfejsy API](scenario-web-app-sign-user-overview.md)

Dowiedz się, jak tworzyć aplikacje sieci Web wywołujące program Microsoft Graph:

> [!div class="nextstepaction"]
> [Microsoft Graph ASP.NET samouczek](https://docs.microsoft.com/graph/tutorials/aspnet)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
