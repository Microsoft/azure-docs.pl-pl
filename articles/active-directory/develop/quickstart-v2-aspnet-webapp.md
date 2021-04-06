---
title: 'Szybki Start: Dodawanie logowania z firmą Microsoft do aplikacji sieci Web ASP.NET | Azure'
titleSuffix: Microsoft identity platform
description: W tym przewodniku szybki start dowiesz się, jak zaimplementować logowanie firmy Microsoft w aplikacji sieci Web ASP.NET za pomocą usługi OpenID Connect Connect.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 09/25/2020
ms.author: jmprieur
ms.custom: devx-track-csharp, aaddev, identityplatformtop40, scenarios:getting-started, languages:ASP.NET, contperf-fy21q1
ms.openlocfilehash: 87948ed04f7b50820d94993d4c4fbcf2dfd94b31
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104578689"
---
# <a name="quickstart-add-microsoft-identity-platform-sign-in-to-an-aspnet-web-app"></a>Szybki Start: Dodawanie logowania do platformy tożsamości firmy Microsoft do aplikacji sieci Web ASP.NET

W tym przewodniku szybki start pobierasz i uruchamiasz przykładowy kod, który pokazuje, jak aplikacja sieci Web ASP.NET może zalogować użytkowników z dowolnej organizacji Azure Active Directory (Azure AD). 

> [!div renderon="docs"]
> Na poniższym diagramie przedstawiono sposób działania przykładowej aplikacji:
>
> ![Diagram interakcji między przeglądarką sieci Web, aplikacją internetową i platformą tożsamości firmy Microsoft w przykładowej aplikacji.](media/quickstart-v2-aspnet-webapp/aspnetwebapp-intro.svg)
>
> ## <a name="prerequisites"></a>Wymagania wstępne
>
> * Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
> * [Visual Studio 2019](https://visualstudio.microsoft.com/vs/)
> * [.NET Framework 4.7.2 +](https://dotnet.microsoft.com/download/visual-studio-sdks)
>
> ## <a name="register-and-download-the-app"></a>Rejestrowanie i pobieranie aplikacji
> Dostępne są dwie opcje rozpoczęcia kompilowania aplikacji: Konfiguracja automatyczna lub ręczna.
>
> ### <a name="automatic-configuration"></a>Automatyczna konfiguracja
> Jeśli chcesz automatycznie skonfigurować aplikację, a następnie pobrać przykład kodu, wykonaj następujące kroki:
>
> 1. Przejdź do <a href="https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/AspNetWebAppQuickstartPage/sourceType/docs" target="_blank">strony Azure Portal na potrzeby rejestracji aplikacji</a>.
> 1. Wprowadź nazwę aplikacji i wybierz pozycję **Zarejestruj**.
> 1. Postępuj zgodnie z instrukcjami, aby pobrać i automatycznie skonfigurować nową aplikację w jednym kliknięciem.
>
> ### <a name="manual-configuration"></a>Konfiguracja ręczna
> Jeśli chcesz ręcznie skonfigurować aplikację i przykład kodu, Użyj poniższych procedur.
>
> #### <a name="step-1-register-your-application"></a>Krok 1. Rejestrowanie aplikacji
>
> 1. Zaloguj się w witrynie <a href="https://portal.azure.com/" target="_blank">Azure Portal</a>.
> 1. Jeśli masz dostęp do wielu dzierżawców, Użyj filtru **katalogów i subskrypcji** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: w górnym menu, aby wybrać dzierżawcę, w którym chcesz zarejestrować aplikację.
> 1. Wyszukaj i wybierz pozycję **Azure Active Directory**.
> 1. W obszarze **Zarządzaj** wybierz pozycję **rejestracje aplikacji**  >  **Nowa rejestracja**.
> 1. W obszarze **Nazwa** wprowadź nazwę aplikacji. Na przykład wprowadź **ASPNET-szybkiego startu**. Użytkownicy Twojej aplikacji będą widzieć tę nazwę i można ją później zmienić.
> 1. Dodaj **https://localhost:44368/** w obszarze **URI przekierowania** i wybierz pozycję **zarejestruj**.
> 1. W obszarze **Zarządzaj** wybierz pozycję **uwierzytelnianie**.
> 1. W sekcji **niejawne przyznanie i przepływy hybrydowe** wybierz pozycję **identyfikatory tokenów**.
> 1. Wybierz pozycję **Zapisz**.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Krok 1. Konfigurowanie aplikacji w witrynie Azure Portal
> Aby uzyskać przykładowy kod w tym przewodniku Szybki Start, wprowadź **https://localhost:44368/** dla **identyfikatora URI przekierowania**.
>
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Wprowadź tę zmianę automatycznie]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![](media/quickstart-v2-aspnet-webapp/green-check.png)Aplikacja została już skonfigurowana przy użyciu tego atrybutu.

#### <a name="step-2-download-the-project"></a>Krok 2. Pobieranie projektu

> [!div renderon="docs"]
> [Pobierz rozwiązanie Visual Studio 2019](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-DotNet/archive/master.zip)

> [!div renderon="portal" class="sxs-lookup"]
> Uruchom projekt przy użyciu programu Visual Studio 2019.
> [!div renderon="portal" id="autoupdate" class="sxs-lookup nextstepaction"]
> [Pobierz przykład kodu](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-DotNet/archive/master.zip)

[!INCLUDE [active-directory-develop-path-length-tip](../../../includes/active-directory-develop-path-length-tip.md)]

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>Krok 3. Twoja aplikacja jest skonfigurowana i gotowa do uruchomienia
> Twój projekt został skonfigurowany z wartościami właściwości aplikacji.

> [!div renderon="docs"]
> #### <a name="step-3-run-your-visual-studio-project"></a>Krok 3. uruchamianie projektu programu Visual Studio

1. Wyodrębnij plik zip do folderu lokalnego znajdującego się blisko folderu głównego. Na przykład Wyodrębnij do *C:\Azure-Samples*.
   
   Zalecamy wyodrębnienie archiwum do katalogu w sąsiedztwie katalogu głównego dysku, aby uniknąć błędów spowodowanych ograniczeniami długości ścieżki w systemie Windows.
2. Otwórz rozwiązanie w programie Visual Studio (*AppModelv2-webapp-OpenIDConnect-dotnet. sln*).
3. W zależności od wersji programu Visual Studio, może być konieczne kliknięcie prawym przyciskiem myszy projektu **AppModelv2-webapp-OpenIDConnect-dotnet** , a następnie wybranie opcji **Przywróć pakiety NuGet**.
4. Otwórz konsolę Menedżera pakietów, wybierając pozycję **Wyświetl**  >  **inną**  >  **konsolę Menedżera pakietów** systemu Windows. Następnie należy uruchomić polecenie `Update-Package Microsoft.CodeDom.Providers.DotNetCompilerPlatform -r`.

> [!div renderon="docs"]
> 5. Edytuj *Web.config* i Zastąp parametry `ClientId` , `Tenant` i `redirectUri` z:
>    ```xml
>    <add key="ClientId" value="Enter_the_Application_Id_here" />
>    <add key="Tenant" value="Enter_the_Tenant_Info_Here" />
>    <add key="redirectUri" value="https://localhost:44368/" />
>    ```
>    W tym kodzie:
>
>    - `Enter_the_Application_Id_here` to identyfikator aplikacji (klienta) utworzonej wcześniej rejestracji aplikacji. Znajdź identyfikator aplikacji (klienta) na stronie **Przegląd** aplikacji w **rejestracje aplikacji** w Azure Portal.
>    - `Enter_the_Tenant_Info_Here` jest jedną z następujących opcji:
>      - Jeśli aplikacja obsługuje **tylko moją organizację**, należy zamienić tę wartość na identyfikator katalogu (dzierżawy) lub nazwę dzierżawy (na przykład `contoso.onmicrosoft.com` ). Znajdź identyfikator katalogu (dzierżawa) na stronie **Przegląd** aplikacji w **rejestracje aplikacji** w Azure Portal.
>      - Jeśli aplikacja obsługuje **konta w dowolnym katalogu organizacyjnym**, Zastąp tę wartość wartością `organizations` .
>      - Jeśli aplikacja obsługuje **wszystkich konto Microsoft użytkowników**, Zastąp tę wartość wartością `common` .
>    - `redirectUri` to **Identyfikator URI przekierowania** wprowadzony wcześniej w **rejestracje aplikacji** w Azure Portal.
>

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

## <a name="more-information"></a>Więcej informacji

Ta sekcja zawiera omówienie kodu wymaganego do zalogowania użytkowników. Ten przegląd może być przydatny do zrozumienia, w jaki sposób działa kod, opis głównych argumentów i sposób dodawania logowania do istniejącej aplikacji ASP.NET.

> [!div class="sxs-lookup" renderon="portal"]
> ### <a name="how-the-sample-works"></a>Jak działa przykład
>
> ![Diagram interakcji między przeglądarką sieci Web, aplikacją internetową i platformą tożsamości firmy Microsoft w przykładowej aplikacji.](media/quickstart-v2-aspnet-webapp/aspnetwebapp-intro.svg)

### <a name="owin-middleware-nuget-packages"></a>Pakiety NuGet oprogramowania pośredniczącego OWIN

Potoku uwierzytelniania można skonfigurować przy użyciu uwierzytelniania opartego na plikach cookie za pomocą OpenID Connect Connect in ASP.NET z pakietami oprogramowania pośredniczącego OWIN. Te pakiety można zainstalować, uruchamiając następujące polecenia w konsoli Menedżera pakietów w programie Visual Studio:

```powershell
Install-Package Microsoft.Owin.Security.OpenIdConnect
Install-Package Microsoft.Owin.Security.Cookies
Install-Package Microsoft.Owin.Host.SystemWeb
```

### <a name="owin-startup-class"></a>Klasa początkowa OWIN

Oprogramowanie pośredniczące OWIN używa *klasy uruchamiania* , która jest uruchamiana podczas uruchamiania procesu hostingu. W tym przewodniku szybki *Start plik Startup. cs* znajduje się w folderze głównym. Poniższy kod przedstawia parametry, które są używane w tym przewodniku szybki start:

```csharp
public void Configuration(IAppBuilder app)
{
    app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

    app.UseCookieAuthentication(new CookieAuthenticationOptions());
    app.UseOpenIdConnectAuthentication(
        new OpenIdConnectAuthenticationOptions
        {
            // Sets the client ID, authority, and redirect URI as obtained from Web.config
            ClientId = clientId,
            Authority = authority,
            RedirectUri = redirectUri,
            // PostLogoutRedirectUri is the page that users will be redirected to after sign-out. In this case, it's using the home page
            PostLogoutRedirectUri = redirectUri,
            Scope = OpenIdConnectScope.OpenIdProfile,
            // ResponseType is set to request the code id_token, which contains basic information about the signed-in user
            ResponseType = OpenIdConnectResponseType.CodeIdToken,
            // ValidateIssuer set to false to allow personal and work accounts from any organization to sign in to your application
            // To only allow users from a single organization, set ValidateIssuer to true and the 'tenant' setting in Web.config to the tenant name
            // To allow users from only a list of specific organizations, set ValidateIssuer to true and use the ValidIssuers parameter
            TokenValidationParameters = new TokenValidationParameters()
            {
                ValidateIssuer = false // Simplification (see note below)
            },
            // OpenIdConnectAuthenticationNotifications configures OWIN to send notification of failed authentications to the OnAuthenticationFailed method
            Notifications = new OpenIdConnectAuthenticationNotifications
            {
                AuthenticationFailed = OnAuthenticationFailed
            }
        }
    );
}
```

> |Lokalizacja  | Opis |
> |---------|---------|
> | `ClientId`     | Identyfikator aplikacji z aplikacji zarejestrowanej w Azure Portal. |
> | `Authority`    | Punkt końcowy usługi tokenu zabezpieczającego (STS), dla którego użytkownik może się uwierzytelnić. Zwykle jest to `https://login.microsoftonline.com/{tenant}/v2.0` chmura publiczna. W tym adresie URL *{dzierżawca}* jest nazwą dzierżawy, identyfikatorem dzierżawy lub `common` odwołaniem do wspólnego punktu końcowego. (Wspólny punkt końcowy jest używany w przypadku aplikacji wielodostępnych). |
> | `RedirectUri`  | Adres URL, pod który użytkownicy są wysyłani po uwierzytelnieniu na platformie tożsamości firmy Microsoft. |
> | `PostLogoutRedirectUri`     | Adres URL, pod który są wysyłane użytkownicy po wylogowaniu. |
> | `Scope`     | Lista żądanych zakresów rozdzielonych spacjami. |
> | `ResponseType`     | Żądanie, które ma być uwierzytelniane przez odpowiedź, zawiera kod autoryzacji i token identyfikatora. |
> | `TokenValidationParameters`     | Lista parametrów na potrzeby weryfikacji tokenu. W tym przypadku `ValidateIssuer` jest ustawiona na, aby `false` wskazać, że może akceptować logowania z dowolnego typu konta osobistego, służbowego lub szkolnego. |
> | `Notifications`     | Lista delegatów, które mogą być uruchamiane w `OpenIdConnect` wiadomościach. |


> [!NOTE]
> Ustawienie to `ValidateIssuer = false` uproszczenie dla tego przewodnika Szybki Start. W rzeczywistych aplikacjach Sprawdź poprawność wystawcy. Zapoznaj się z przykładami, aby dowiedzieć się, jak to zrobić.

### <a name="authentication-challenge"></a>Wyzwanie uwierzytelniania

Możesz wymusić logowanie użytkownika, wysyłając żądanie uwierzytelnienia w kontrolerze:

```csharp
public void SignIn()
{
    if (!Request.IsAuthenticated)
    {
        HttpContext.GetOwinContext().Authentication.Challenge(
            new AuthenticationProperties{ RedirectUri = "/" },
            OpenIdConnectAuthenticationDefaults.AuthenticationType);
    }
}
```

> [!TIP]
> Żądanie uwierzytelnienia przy użyciu tej metody jest opcjonalne. Zwykle jest ona używana, gdy widok ma być dostępny zarówno dla użytkowników uwierzytelnionych, jak i nieuwierzytelnionych. Innym rozwiązaniem jest ochrona kontrolerów przy użyciu metody opisanej w następnej sekcji.

### <a name="attribute-for-protecting-a-controller-or-a-controller-actions"></a>Atrybut chroniący kontroler lub akcje kontrolera

Można chronić kontroler lub akcje kontrolera przy użyciu `[Authorize]` atrybutu. Ten atrybut ogranicza dostęp do kontrolera lub akcji przez zezwolenie tylko uwierzytelnionym użytkownikom na dostęp do akcji w kontrolerze. Gdy Użytkownik nieuwierzytelniony podejmie próbę uzyskania dostępu do jednej z akcji lub kontrolerów, które są oznaczone atrybutem, zostanie wykonane automatyczne wyzwanie uwierzytelniania `[Authorize]` .

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Następne kroki

Aby zapoznać się z kompletnym przewodnikiem krok po kroku dotyczącym tworzenia aplikacji i nowych funkcji, w tym pełne wyjaśnienie tego przewodnika Szybki Start, wypróbuj samouczek ASP.NET.

> [!div class="nextstepaction"]
> [Dodawanie logowania do aplikacji sieci Web ASP.NET](tutorial-v2-asp-webapp.md)
