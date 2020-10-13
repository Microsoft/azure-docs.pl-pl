---
title: 'Samouczek: Tworzenie aplikacji sieci Web ASP.NET używającej platformy tożsamości firmy Microsoft do uwierzytelniania | Azure'
titleSuffix: Microsoft identity platform
description: W tym samouczku utworzysz aplikację sieci Web ASP.NET, która korzysta z platformy tożsamości firmy Microsoft i oprogramowania pośredniczącego OWIN w celu włączenia logowania użytkownika.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 08/28/2019
ms.author: jmprieur
ms.custom: devx-track-csharp, aaddev, identityplatformtop40
ms.openlocfilehash: 9ff43202bdace577024413c9cc177de2997a0ad5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91627961"
---
# <a name="tutorial-add-sign-in-to-microsoft-to-an-aspnet-web-app"></a>Samouczek: Dodawanie logowania do firmy Microsoft do aplikacji sieci Web ASP.NET

W tym przewodniku pokazano, jak zaimplementować logowanie do firmy Microsoft za pośrednictwem rozwiązania ASP.NET MVC przy użyciu tradycyjnej aplikacji opartej na przeglądarce sieci Web i usługi OpenID Connect Connect.

Po ukończeniu tego przewodnika aplikacja będzie mogła akceptować logowania do kont osobistych za pomocą polubień outlook.com i live.com. Ponadto konta służbowe z dowolnej firmy lub organizacji zintegrowanej z platformą tożsamości firmy Microsoft będą mogły zalogować się do aplikacji.

W tym samouczku:

> [!div class="checklist"]
> * Tworzenie projektu *aplikacji sieci Web ASP.NET* w programie Visual Studio
> * Dodaj składniki pośredniczące otwierania interfejsu sieci Web dla platformy .NET (OWIN)
> * Dodawanie kodu do obsługi logowania i wylogowywania użytkowników
> * Zarejestruj aplikację w Azure Portal
> * Testowanie aplikacji

## <a name="prerequisites"></a>Wymagania wstępne

* [Program Visual Studio 2019](https://visualstudio.microsoft.com/vs/) z zainstalowanym obciążeniem **ASP.NET i programowaniem w sieci Web**

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>Jak działa Przykładowa aplikacja generowana przez ten przewodnik

![Pokazuje sposób działania przykładowej aplikacji wygenerowanej przez ten samouczek](media/active-directory-develop-guidedsetup-aspnetwebapp-intro/aspnetbrowsergeneral.svg)

Utworzona Przykładowa aplikacja jest oparta na scenariuszu, w którym używasz przeglądarki do uzyskiwania dostępu do witryny sieci Web ASP.NET, która poprosi użytkownika o uwierzytelnienie za pośrednictwem przycisku logowania. W tym scenariuszu większość działań związanych z renderowaniem strony internetowej odbywa się po stronie serwera.

## <a name="libraries"></a>Biblioteki

W tym przewodniku są stosowane następujące biblioteki:

|Biblioteka|Opis|
|---|---|
|[Microsoft.Owin.Security.OpenIdConnect](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect/)|Oprogramowanie pośredniczące, które umożliwia aplikacji wykorzystywanie do uwierzytelniania protokołu OpenIdConnect|
|[Microsoft.Owin.Security.Cookies](https://www.nuget.org/packages/Microsoft.Owin.Security.Cookies)|Oprogramowanie pośredniczące, które umożliwia aplikacji obsługę sesji użytkownika przy użyciu plików cookie|
|[Microsoft.Owin.Host.SystemWeb](https://www.nuget.org/packages/Microsoft.Owin.Host.SystemWeb)|Oprogramowanie pośredniczące, które umożliwia uruchamianie aplikacji opartych na OWIN na Internet Information Services (IIS) za pomocą potoku żądań ASP.NET|

## <a name="set-up-your-project"></a>konfigurowanie projektu

W tej sekcji opisano sposób instalowania i konfigurowania potoku uwierzytelniania za pośrednictwem OWINego oprogramowania w projekcie ASP.NET przy użyciu programu OpenID Connect Connect.

> Wolisz pobrać projekt przykładowego programu Visual Studio? [Pobierz projekt](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-DotNet/archive/master.zip) i przejdź do [zarejestruj swoją aplikację](#register-your-application) , aby skonfigurować przykładowy kod przed wykonaniem.

### <a name="create-your-aspnet-project"></a>tworzenie projektu ASP.NET

1. W programie Visual Studio: Przejdź do **pliku**  >  **Nowy**  >  **projekt**.
2. W pozycji **Visual C#\Internet** wybierz opcję **Aplikacja internetowa ASP.NET (.NET Framework)**.
3. Nadaj nazwę aplikacji, a następnie wybierz przycisk **OK**.
4. Wybierz opcję **puste**, a następnie zaznacz pole wyboru, aby dodać odwołania **MVC** .

## <a name="add-authentication-components"></a>dodawanie składników uwierzytelniania

1. W programie Visual Studio: Przejdź do pozycji **Narzędzia**Menedżer  >  **pakietów NuGet**  >  **konsola Menedżera pakietów**.
2. Dodaj *pakiety NuGet oprogramowania pośredniczącego OWIN*, wpisując następujące polecenie w oknie Konsola menedżera pakietów:

    ```powershell
    Install-Package Microsoft.Owin.Security.OpenIdConnect
    Install-Package Microsoft.Owin.Security.Cookies
    Install-Package Microsoft.Owin.Host.SystemWeb
    ```

### <a name="about-these-libraries"></a>Informacje o tych bibliotekach
Te biblioteki umożliwiają logowanie jednokrotne (SSO) przy użyciu funkcji OpenID Connect Connect za pośrednictwem uwierzytelniania opartego na plikach cookie. Po zakończeniu uwierzytelniania i wysłaniu tokenu reprezentującego użytkownika do aplikacji oprogramowanie pośredniczące OWIN tworzy plik cookie sesji. Przeglądarka następnie używa tego pliku cookie w kolejnych żądaniach, tak aby użytkownik nie musiał ponownie wpisywać hasła, a dodatkowa weryfikacja nie jest wymagana.

## <a name="configure-the-authentication-pipeline"></a>konfigurowanie potoku uwierzytelniania

Poniższe kroki służą do tworzenia klasy startowej OWIN oprogramowania pośredniczącego w celu skonfigurowania uwierzytelniania OpenID Connect Connect. Ta klasa jest wykonywana automatycznie podczas uruchamiania procesu usług IIS.

> [!TIP]
> Jeśli projekt nie ma pliku `Startup.cs` w folderze głównym:
> 1. Kliknij prawym przyciskiem myszy folder główny projektu, a następnie wybierz pozycję **Dodaj**  >  **nowy element**  >  **Owin klasy startowej**.<br/>
> 2. Nadaj mu nazwę **Startup.cs**.
>
>> Upewnij się, że wybrana Klasa jest klasą początkową OWIN, a nie standardową klasą języka C#. Potwierdź to, sprawdzając, czy jest widoczny komunikat [assembly: OwinStartup (typeof ({NameSpace}). Uruchamianie)] powyżej przestrzeni nazw.

1. Dodaj odwołania *Owin* i *Microsoft. IdentityModel* do Startup.cs:

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
> Ustawienie to `ValidateIssuer = false` uproszczenie dla tego przewodnika Szybki Start. W rzeczywistych aplikacjach należy sprawdzić poprawność wystawcy.
> Zapoznaj się z przykładami, aby dowiedzieć się, jak to zrobić.

### <a name="more-information"></a>Więcej informacji

Parametry podane w *OpenIDConnectAuthenticationOptions* stanowią współrzędne dla aplikacji w celu komunikowania się z platformą tożsamości firmy Microsoft. Ponieważ program OpenID Connect Connecter używa plików cookie w tle, należy również skonfigurować uwierzytelnianie plików cookie jako powyższy kod. Wartość *ValidateIssuer* informuje OpenIdConnect, aby nie ograniczać dostępu do jednej konkretnej organizacji.

## <a name="add-a-controller-to-handle-sign-in-and-sign-out-requests"></a>dodawanie kontrolera do obsługi żądań logowania i wylogowania

Aby utworzyć nowy kontroler w celu udostępnienia metod logowania i wylogowania, wykonaj następujące kroki:

1.  Kliknij prawym przyciskiem myszy folder **controllers** , a następnie wybierz polecenie **Dodaj**  >  **kontroler**.
2.  Wybierz pozycję **Kontroler MVC (wersja platformy .NET) — Pusty**.
3.  Wybierz pozycję **Dodaj**.
4.  Nadaj jej nazwę **HomeController** , a następnie wybierz pozycję **Dodaj**.
5.  Dodaj odwołania OWIN do klasy:

    ```csharp
    using Microsoft.Owin.Security;
    using Microsoft.Owin.Security.Cookies;
    using Microsoft.Owin.Security.OpenIdConnect;
    ```

6. Dodaj następujące dwie metody, aby obsłużyć logowanie i wylogować się do kontrolera przez zainicjowanie wyzwania uwierzytelniania:

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

## <a name="create-the-apps-home-page-for-user-sign-in"></a>Tworzenie strony głównej aplikacji na potrzeby logowania użytkownika

W programie Visual Studio Utwórz nowy widok, aby dodać przycisk logowania i wyświetlić informacje o użytkowniku po uwierzytelnieniu:

1.  Kliknij prawym przyciskiem myszy folder **Views\Home** i wybierz polecenie **Dodaj widok**.
2.  Nazwij nowy **indeks**widoku.
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

### <a name="more-information"></a>Więcej informacji
 Ta strona dodaje przycisk logowania w formacie SVG z czarnym tłem:<br/>![Przycisk Zaloguj się przy użyciu konta Microsoft](media/active-directory-develop-guidedsetup-aspnetwebapp-use/aspnetsigninbuttonsample.png)<br/> Aby uzyskać więcej przycisków logowania, przejdź do [wskazówek dotyczących znakowania](./howto-add-branding-in-azure-ad-apps.md "Wytyczne dotyczące oznaczania marką").

## <a name="add-a-controller-to-display-users-claims"></a>Dodaj kontroler, aby wyświetlić oświadczenia użytkownika
Ten kontroler pokazuje wykorzystanie atrybutu `[Authorize]` do ochrony kontrolera. Ten atrybut ogranicza dostęp do kontrolera przez umożliwienie tylko uwierzytelnionym użytkownikom. Poniższy kod umożliwia użycie atrybutu w celu wyświetlenia oświadczeń użytkownika, które zostały pobrane w ramach logowania:

1.  Kliknij prawym przyciskiem myszy folder **controllers** , a następnie wybierz polecenie **Dodaj**  >  **kontroler**.
2.  Wybierz pozycję **Kontroler MVC {version} — Pusty**.
3.  Wybierz pozycję **Dodaj**.
4.  Nadaj mu nazwę **ClaimsController**.
5.  Zastąp kod klasy kontrolera następującym kodem. Spowoduje to dodanie `[Authorize]` atrybutu do klasy:

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

            //You get the user's first and last name below:
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

### <a name="more-information"></a>Więcej informacji
Ze względu na użycie `[Authorize]` atrybutu wszystkie metody tego kontrolera można wykonać tylko wtedy, gdy użytkownik jest uwierzytelniony. Jeśli użytkownik nie jest uwierzytelniony i próbuje uzyskać dostęp do kontrolera, OWIN inicjuje wyzwanie uwierzytelniania i wymusza uwierzytelnienie użytkownika. Poprzedni kod przegląda listę oświadczeń dla określonych atrybutów użytkownika zawartych w tokenie identyfikatora użytkownika. Te atrybuty obejmują pełną nazwę użytkownika i użytkownika, a także rolę globalnego identyfikatora użytkownika. Zawiera również *Identyfikator dzierżawy*, który reprezentuje identyfikator organizacji użytkownika.

## <a name="create-a-view-to-display-the-users-claims"></a>tworzenie widoku w celu wyświetlenia oświadczeń użytkownika

W programie Visual Studio utwórz nowy widok w celu wyświetlenia oświadczeń użytkownika na stronie internetowej:

1.  Kliknij prawym przyciskiem myszy folder **Views\Claims** , a następnie wybierz polecenie **Dodaj widok**.
2.  Nazwij nowy **indeks**widoku.
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

Aby zarejestrować aplikację i dodać do rozwiązania informacje o rejestracji aplikacji, dostępne są dwie opcje:

### <a name="option-1-express-mode"></a>Opcja 1: tryb ekspresowy

Aby szybko zarejestrować aplikację, wykonaj następujące kroki:

1. Przejdź do nowego okienka  [Azure Portal-rejestracje aplikacji](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/AspNetWebAppQuickstartPage/sourceType/docs) .
1. Wprowadź nazwę aplikacji i wybierz pozycję **Zarejestruj**.
1. Postępuj zgodnie z instrukcjami, aby pobrać i automatycznie skonfigurować nową aplikację przy użyciu jednego kliknięcia.

### <a name="option-2-advanced-mode"></a>Opcja 2: tryb zaawansowany

Aby ręcznie zarejestrować aplikację i dodać informacje na temat rejestracji aplikacji do rozwiązania, wykonaj następujące czynności:

1. Otwórz program Visual Studio, a następnie:
   1. w Eksplorator rozwiązań wybierz projekt i Wyświetl okno Właściwości (jeśli nie widzisz okno Właściwości, naciśnij klawisz F4).
   1. Zmień włączony protokół SSL na `True` .
   1. Kliknij prawym przyciskiem myszy projekt w programie Visual Studio, wybierz polecenie **Właściwości**, a następnie wybierz kartę **Sieć Web** . W sekcji **serwery** Zmień ustawienie **adres URL projektu** na **adres URL protokołu SSL**.
   1. Skopiuj adres URL protokołu SSL. Ten adres URL zostanie dodany do listy adresów URL przekierowania na liście adresów URL przekierowania portalu rejestracji w następnym kroku.<br/><br/>![Właściwości projektu](media/active-directory-develop-guidedsetup-aspnetwebapp-configure/vsprojectproperties.png)<br />
1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub za pomocą konto Microsoft osobistych.
1. Jeśli Twoje konto zapewnia dostęp do więcej niż jednej dzierżawy, wybierz swoje konto w prawym górnym rogu, a następnie ustaw sesję portalu z dzierżawą usługi Azure AD.
1. Przejdź do strony Microsoft Identity Platform for Developers [rejestracje aplikacji](https://go.microsoft.com/fwlink/?linkid=2083908) .
1. Wybierz pozycję **Nowa rejestracja**.
1. Po wyświetleniu strony **Rejestrowanie aplikacji** podaj informacje dotyczące rejestracji aplikacji:
   1. W sekcji **Nazwa** wprowadź zrozumiałą nazwę aplikacji, która będzie wyświetlana użytkownikom aplikacji, na przykład **ASPNET-samouczek**.
   1. Dodaj adres URL protokołu SSL skopiowany z programu Visual Studio w kroku 1 (na przykład `https://localhost:44368/` ) w polu **adres URL odpowiedzi**i wybierz pozycję **zarejestruj**.
1. Wybierz menu **uwierzytelnianie** , wybierz pozycję **identyfikatory tokeny** w obszarze **niejawne Grant**, a następnie wybierz pozycję **Zapisz**.
1. Dodaj następujące elementy w pliku web.config znajdującym się w folderze głównym w `configuration\appSettings` sekcji:

    ```xml
    <add key="ClientId" value="Enter_the_Application_Id_here" />
    <add key="redirectUri" value="Enter_the_Redirect_URL_here" />
    <add key="Tenant" value="common" />
    <add key="Authority" value="https://login.microsoftonline.com/{0}/v2.0" />
    ```

1. Zamień na `ClientId` zarejestrowany identyfikator aplikacji.
1. Zamień na `redirectUri` adres URL protokołu SSL projektu.

## <a name="test-your-code"></a>Testowanie kodu

Aby przetestować aplikację w programie Visual Studio, naciśnij klawisz F5, aby uruchomić projekt. Zostanie otwarta przeglądarka http:// <span></span> localhost: {port} i zobaczysz przycisk **Zaloguj się przy użyciu konta Microsoft** . Wybierz przycisk, aby uruchomić proces logowania.

Gdy wszystko będzie gotowe do uruchomienia testu, użyj konta usługi Azure AD (konta służbowego) lub osobistego konto Microsoft (na<span>żywo.</span> com lub <span>Outlook.</span> com), aby się zalogować.

![Przycisk Zaloguj się przy użyciu konta Microsoft wyświetlany na stronie logowania przeglądarki w przeglądarce](media/active-directory-develop-guidedsetup-aspnetwebapp-test/aspnetbrowsersignin.png)
<br/><br/>
![Zaloguj się do konto Microsoft](media/active-directory-develop-guidedsetup-aspnetwebapp-test/aspnetbrowsersignin2.png)

#### <a name="permissions-and-consent-in-the-microsoft-identity-platform-endpoint"></a>Uprawnienia i zgoda w punkcie końcowym platformy tożsamości firmy Microsoft

Aplikacje integrowane z platformą tożsamości firmy Microsoft są zgodne z modelem autoryzacji, który umożliwia użytkownikom i administratorom kontrolę nad sposobem uzyskiwania dostępu do danych. Po uwierzytelnieniu użytkownika przy użyciu platformy tożsamości firmy Microsoft w celu uzyskania dostępu do tej aplikacji zostanie wyświetlony monit o zgodę na uprawnienia wymagane przez aplikację ("Wyświetl swój profil podstawowy" i "Obsługuj dostęp do danych, do których masz dostęp"). Po zaakceptowaniu tych uprawnień użytkownik będzie kontynuował działanie aplikacji. Jednak użytkownik może zamiast tego monitować o **wymaganą stronę zgody administratora** , jeśli wystąpi jeden z następujących sytuacji:

- Deweloper aplikacji dodaje wszelkie dodatkowe uprawnienia, które wymagają **zgody administratora**.
- Lub dzierżawy są skonfigurowane (w **aplikacjach dla przedsiębiorstw — > ustawienia użytkownika**), w których użytkownicy nie mogą wyrazić zgody na aplikacje uzyskujące dostęp do danych firmy w ich imieniu.

Aby uzyskać więcej informacji, zobacz [uprawnienia i zgody w punkcie końcowym platformy tożsamości firmy Microsoft](./v2-permissions-and-consent.md).

### <a name="view-application-results"></a>Wyświetlanie wyników aplikacji

Po zalogowaniu użytkownik zostanie przekierowany do strony głównej witryny sieci Web. Strona główna to adres URL HTTPS określony w informacjach rejestracyjnych aplikacji w portalu rejestracji aplikacji firmy Microsoft. Strona główna zawiera komunikat powitalny *"Hello \<user> "* , link do wylogowania oraz link umożliwiający wyświetlenie oświadczeń użytkownika. Link do oświadczeń użytkownika nawiązuje połączenie z utworzonym wcześniej kontrolerem oświadczeń.

### <a name="view-the-users-claims"></a>Wyświetl oświadczenia użytkownika

Aby wyświetlić oświadczenia użytkownika, wybierz link, aby przejść do widoku kontrolera, który jest dostępny tylko dla uwierzytelnionych użytkowników.

#### <a name="view-the-claims-results"></a>Wyświetlanie wyników oświadczeń

Po przejściu do widoku kontrolera powinna zostać wyświetlona tabela zawierająca podstawowe właściwości użytkownika:

|Właściwość |Wartość |Opis |
|---|---|---|
|**Nazwa** |Pełna nazwa użytkownika | Imię i nazwisko użytkownika
|**Nazwa użytkownika** |Użytkownicy<span>@domain.com</span> | Nazwa użytkownika używana do identyfikowania użytkownika|
|**Temat** |Temat |Ciąg, który jednoznacznie identyfikuje użytkownika w sieci Web|
|**Identyfikator dzierżawy** |Guid (identyfikator GUID) | **Identyfikator GUID** , który jednoznacznie reprezentuje organizację usługi Azure AD użytkownika|

Ponadto powinna zostać wyświetlona tabela wszystkich oświadczeń, które znajdują się w żądaniu uwierzytelniania. Aby uzyskać więcej informacji, zobacz [listę oświadczeń, które znajdują się w tokenie identyfikatora](./id-tokens.md).

### <a name="test-access-to-a-method-that-has-an-authorize-attribute-optional"></a>Przetestuj dostęp do metody, która ma atrybut Autoryzuj (opcjonalnie)

Aby przetestować dostęp jako anonimowy użytkownik do kontrolera, który jest chroniony przez ten `Authorize` atrybut, wykonaj następujące kroki:

1. Wybierz link, aby wylogować użytkownika i zakończyć proces wylogowywania.
2. W przeglądarce wpisz http:// <span></span> localhost: {Port}/oświadczenia, aby uzyskać dostęp do kontrolera, który jest chroniony przez `Authorize` atrybut.

#### <a name="expected-results-after-access-to-a-protected-controller"></a>Oczekiwane wyniki po uzyskaniu dostępu do kontrolera chronionego

Zostanie wyświetlony monit o uwierzytelnienie w celu użycia widoku kontrolera chronionego.

## <a name="advanced-options"></a>Opcje zaawansowane

### <a name="protect-your-entire-website"></a>Ochrona całej witryny sieci Web

Aby chronić całą witrynę sieci Web, w pliku **Global. asax** Dodaj `AuthorizeAttribute` atrybut do `GlobalFilters` filtru w `Application_Start` metodzie:

```csharp
GlobalFilters.Filters.Add(new AuthorizeAttribute());
```

### <a name="restrict-who-can-sign-in-to-your-application"></a>Ogranicz, kto może logować się do aplikacji

Domyślnie podczas kompilowania aplikacji utworzonej przez ten przewodnik aplikacja będzie akceptować logowania do kont osobistych (w tym outlook.com, live.com i innych), a także kont służbowych z dowolnej firmy lub organizacji zintegrowanej z platformą tożsamości firmy Microsoft. Jest to zalecana opcja dla aplikacji SaaS.

Aby ograniczyć dostęp użytkowników do logowania do aplikacji, dostępne są różne opcje.

#### <a name="option-1-restrict-users-from-only-one-organizations-active-directory-instance-to-sign-in-to-your-application-single-tenant"></a>Opcja 1: Ogranicz użytkowników tylko z wystąpienia Active Directory w organizacji, aby zalogować się do aplikacji (pojedyncza dzierżawa)

Ta opcja jest często używana w przypadku *aplikacji biznesowych*: Jeśli chcesz, aby aplikacja akceptowała logowania tylko z kont należących do określonego wystąpienia usługi Azure AD (w tym *kont Gości* tego wystąpienia), wykonaj następujące kroki:

1. W pliku web.config Zmień wartość `Tenant` parametru z `Common` na nazwę dzierżawy organizacji, na przykład `contoso.onmicrosoft.com` .
2. W [klasie uruchomieniowej Owin](#configure-the-authentication-pipeline)Ustaw `ValidateIssuer` argument na `true` .

#### <a name="option-2-restrict-access-to-users-in-a-specific-list-of-organizations"></a>Opcja 2: ograniczanie dostępu do użytkowników z określoną listą organizacji

Możesz ograniczyć dostęp do logowania tylko do tych kont użytkowników, które znajdują się w organizacji usługi Azure AD, która znajduje się na liście dozwolonych organizacji:
1. W [klasie uruchomieniowej Owin](#configure-the-authentication-pipeline)Ustaw `ValidateIssuer` argument na `true` .
2. Ustaw wartość `ValidIssuers` parametru na listę dozwolonych organizacji.

#### <a name="option-3-use-a-custom-method-to-validate-issuers"></a>Opcja 3: Użyj niestandardowej metody do walidacji wystawców

Można zaimplementować metodę niestandardową w celu weryfikacji wystawców przy użyciu parametru **IssuerValidator** . Aby uzyskać więcej informacji na temat korzystania z tego parametru, zobacz [TokenValidationParameters](/dotnet/api/microsoft.identitymodel.tokens.tokenvalidationparameters) Class.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o wywoływaniu chronionych interfejsów API sieci Web z aplikacji sieci Web za pomocą platformy tożsamości firmy Microsoft:

> [!div class="nextstepaction"]
> [Aplikacje sieci Web wywołujące interfejsy API sieci Web](scenario-web-app-sign-user-overview.md)
