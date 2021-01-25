---
title: 'Szybki Start: ASP.NET Core aplikacji sieci Web, która umożliwia logowanie użytkowników i wywołań Microsoft Graph | Azure'
titleSuffix: Microsoft identity platform
description: W tym przewodniku szybki start dowiesz się, jak aplikacja korzysta z programu Microsoft. Identity. Web w celu zaimplementowania logowania firmy Microsoft ASP.NET Core w aplikacji sieci Web przy użyciu usługi OpenID Connect Connect i wywołań Microsoft Graph
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 12/10/2020
ms.author: jmprieur
ms.custom: devx-track-csharp, aaddev, scenarios:getting-started, languages:aspnet-core
ms.openlocfilehash: 8e54f71ef58b3ea76a5fe55347a1caa173046320
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/25/2021
ms.locfileid: "98754499"
---
# <a name="quickstart-aspnet-core-web-app-that-signs-in-users-and-calls-microsoft-graph-on-their-behalf"></a>Szybki Start: ASP.NET Core aplikacji sieci Web, która umożliwia logowanie użytkowników i wywoływanie Microsoft Graph w ich imieniu

W tym przewodniku szybki start pobierasz i uruchamiasz przykładowy kod, który pokazuje, jak aplikacja sieci Web ASP.NET Core może zalogować użytkowników z dowolnej organizacji Azure Active Directory (Azure AD) i wywołań Microsoft Graph.  

Zobacz [, jak działa Przykładowa](#how-the-sample-works) ilustracja.

> [!div renderon="docs"]
> ## <a name="prerequisites"></a>Wymagania wstępne
>
> * [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) lub [Visual Studio Code](https://code.visualstudio.com/)
> * [Zestaw .NET Core SDK 3.1 +](https://dotnet.microsoft.com/download)
>
> ## <a name="register-and-download-the-quickstart-app"></a>Rejestrowanie i pobieranie aplikacji szybkiego startu
> Istnieją dwie opcje uruchamiania aplikacji Szybki start:
> * [Ekspresowe] [Opcja 1. Zarejestrowanie i automatyczne skonfigurowanie aplikacji, a następnie pobranie przykładowego kodu](#option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample)
> * [Ręczne] [Opcja 2. Zarejestrowanie i ręczne skonfigurowanie aplikacji oraz przykładowego kodu](#option-2-register-and-manually-configure-your-application-and-code-sample)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Opcja 1. Zarejestrowanie i automatyczne skonfigurowanie aplikacji, a następnie pobranie przykładowego kodu
>
> 1. Przejdź do środowiska szybkiego startu w <a href="https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/AspNetCoreWebAppQuickstartPage/sourceType/docs" target="_blank">Azure Portal rejestracje aplikacji <span class="docon docon-navigate-external x-hidden-focus"></span> </a> .
> 1. Wprowadź nazwę aplikacji i wybierz pozycję **Zarejestruj**.
> 1. Postępuj zgodnie z instrukcjami, aby jednym kliknięciem pobrać i automatycznie skonfigurować nową aplikację.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Opcja 2. Zarejestrowanie i ręczne skonfigurowanie aplikacji oraz przykładowego kodu
>
> #### <a name="step-1-register-your-application"></a>Krok 1. Rejestrowanie aplikacji
> Aby ręcznie zarejestrować aplikację i dodać informacje na temat rejestracji aplikacji do rozwiązania, wykonaj następujące czynności:
>
> 1. Zaloguj się do <a href="https://portal.azure.com/" target="_blank">Azure Portal <span class="docon docon-navigate-external x-hidden-focus"></span> </a>.
> 1. Jeśli masz dostęp do wielu dzierżawców, Użyj filtru **katalogów i subskrypcji** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: w górnym menu, aby wybrać dzierżawcę, w którym chcesz zarejestrować aplikację.
> 1. Wyszukaj i wybierz pozycję **Azure Active Directory**.
> 1. W obszarze **Zarządzaj** wybierz pozycję **rejestracje aplikacji**  >  **Nowa rejestracja**.
> 1. Wprowadź **nazwę** aplikacji, na przykład `AspNetCoreWebAppCallsGraph-Quickstart` . Użytkownicy Twojej aplikacji mogą zobaczyć tę nazwę i można ją później zmienić.
> 1. Wprowadź **Identyfikator URI przekierowania** `https://localhost:44321/signin-oidc` .
> 1. Wybierz pozycję **Zarejestruj**.
> 1. W obszarze **Zarządzaj** wybierz pozycję **uwierzytelnianie**.
> 1. Wprowadź **adres URL wylogowywania** `https://localhost:44321/signout-oidc` .
> 1. Wybierz pozycję **Zapisz**.
> 1. W obszarze **Zarządzaj** wybierz pozycję **Certyfikaty &** wpisy tajne  >  **nowy klucz tajny klienta**.
> 1. Wprowadź na przykład **Opis** `clientsecret1` .
> 1. W polu wygaśnięcie klucza tajnego wybierz pozycję **1 rok** .
> 1. Wybierz pozycję **Dodaj** i natychmiast Zapisz **wartość** wpisu tajnego do użycia w późniejszym kroku. Wartość wpisu tajnego *nigdy nie jest ponownie wyświetlana* i jest irretrievable w inny sposób. Zapisz go w bezpiecznej lokalizacji, podobnie jak każde hasło.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Krok 1. Konfigurowanie aplikacji w witrynie Azure Portal
> Aby przykład kodu dla tego przewodnika Szybki Start działał, musisz dodać adresy URL odpowiedzi jako `https://localhost:44321/signin-oidc` , Dodaj adres URL wylogowywania jako `https://localhost:44321/signout-oidc` .
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Wprowadź tę zmianę automatycznie]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Już skonfigurowano](media/quickstart-v2-aspnet-webapp/green-check.png) Twoja aplikacja została skonfigurowana za pomocą tych atrybutów.

#### <a name="step-2-download-the-aspnet-core-project"></a>Krok 2. Pobieranie projektu ASP.NET Core

> [!div renderon="docs"]
> [Pobierz rozwiązanie ASP.NET Core](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/archive/aspnetcore3-1-callsgraph.zip)

> [!div renderon="portal" class="sxs-lookup"]
> Uruchom projekt.

> [!div renderon="portal" class="sxs-lookup" id="autoupdate" class="nextstepaction"]
> [Pobierz przykład kodu](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/archive/aspnetcore3-1-callsgraph.zip)

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>Krok 3. Twoja aplikacja jest skonfigurowana i gotowa do uruchomienia
> Twój projekt został skonfigurowany z wartościami właściwości aplikacji i jest gotowy do uruchomienia.
> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`
> [!div renderon="docs"]
> #### <a name="step-3-configure-your-aspnet-core-project"></a>Krok 3. Konfigurowanie projektu ASP.NET Core
> 1. Wyodrębnij archiwum zip do folderu lokalnego znajdującego się w katalogu głównym dysku. Na przykład do *C:\Azure-Samples*.
> 1. Otwórz rozwiązanie w programie Visual Studio 2019.
> 1. Otwórz *appsettings.js* pliku i zmodyfikuj następujące elementy:
>
>    ```json
>    "ClientId": "Enter_the_Application_Id_here",
>    "TenantId": "common",
>    "clientSecret": "Enter_the_Client_Secret_Here"
>    ```
>
>    - Zamień na `Enter_the_Application_Id_here` **Identyfikator aplikacji (klienta)** , która została zarejestrowana w Azure Portal. **Identyfikator aplikacji (klienta)** możesz znaleźć na stronie aplikacji **Przegląd** aplikacji.
>    - Zamień `common` na jedną z następujących wartości:
>       - Jeśli aplikacja obsługuje **konta tylko w tym katalogu organizacji**, Zastąp tę wartość **identyfikatorem katalogu (dzierżawy)** (identyfikatorem GUID) lub **nazwą dzierżawy** (na przykład `contoso.onmicrosoft.com` ). **Identyfikator katalogu (dzierżawcy)** można znaleźć na stronie **przeglądu** aplikacji.
>       - Jeśli aplikacja obsługuje tryb **Konta w dowolnym katalogu organizacyjnym**, zastąp tę wartość za pomocą wartości `organizations`
>       - Jeśli aplikacja obsługuje **wszystkich konto Microsoft użytkowników**, pozostaw tę wartość jako `common`
>    - Zamień na `Enter_the_Client_Secret_Here` **wpis tajny klienta** utworzony i zarejestrowany we wcześniejszym kroku.
> 
> W ramach tego przewodnika Szybki Start nie zmieniaj żadnych innych wartości w *appsettings.js* pliku.
>
> #### <a name="step-4-build-and-run-the-application"></a>Krok4. Skompiluj i uruchom aplikację
>
> Kompiluj i uruchamiaj aplikację w programie Visual Studio, wybierając menu **Debuguj** , > **rozpocząć debugowanie** lub naciskając klawisz `F5` .
>
> Zostanie wyświetlony monit o podanie poświadczeń, a następnie prośba o zgodę na uprawnienia wymagane przez aplikację. Wybierz pozycję **Akceptuj** w monicie o zgodę.
>
> :::image type="content" source="media/quickstart-v2-aspnet-core-webapp-calls-graph/webapp-01-consent.png" alt-text="Okno dialogowe zgody zawierające uprawnienia, które aplikacja żąda od użytkownika >":::
>
> Po zapoznaniu się z żądanymi uprawnieniami aplikacja wyświetli komunikat, że użytkownik zalogował się pomyślnie przy użyciu poświadczeń Azure Active Directory i zobaczysz swój adres e-mail w sekcji "wynik interfejsu API" strony. Ta wartość została wyodrębniona przy użyciu Microsoft Graph.
>
> :::image type="content" source="media/quickstart-v2-aspnet-core-webapp-calls-graph/webapp-02-signed-in.png" alt-text="Przeglądarka sieci Web wyświetlająca uruchomioną aplikację sieci Web i zalogowany użytkownik":::

## <a name="about-the-code"></a>Informacje o kodzie

Ta sekcja zawiera omówienie kodu wymaganego do zalogowania użytkowników i wywołania interfejsu API Microsoft Graph w ich imieniu. Ten przegląd może być przydatny do zrozumienia sposobu działania kodu, głównych argumentów oraz, jeśli chcesz dodać logowanie do istniejącej aplikacji ASP.NET Core i wywoływać Microsoft Graph. Używa ona elementu [Microsoft. Identity. Web](microsoft-identity-web.md), który jest otoką wokół [MSAL.NET](msal-overview.md).

### <a name="how-the-sample-works"></a>Jak działa przykład
![Pokazuje sposób działania przykładowej aplikacji wygenerowanej przez ten przewodnik Szybki Start](media/quickstart-v2-aspnet-core-webapp-calls-graph/aspnetcorewebapp-intro.svg)

### <a name="startup-class"></a>Klasa początkowa

Oprogramowanie pośredniczące *Microsoft. AspNetCore. Authentication* używa `Startup` klasy, która jest wykonywana po zainicjowaniu procesu hostingu:

```csharp
  // Get the scopes from the configuration (appsettings.json)
  var initialScopes = Configuration.GetValue<string>("DownstreamApi:Scopes")?.Split(' ');

  public void ConfigureServices(IServiceCollection services)
  {
      // Add sign-in with Microsoft
      services.AddAuthentication(OpenIdConnectDefaults.AuthenticationScheme)
        .AddMicrosoftIdentityWebApp(Configuration.GetSection("AzureAd"))

            // Add the possibility of acquiring a token to call a protected web API
            .EnableTokenAcquisitionToCallDownstreamApi(initialScopes)

                // Enables controllers and pages to get GraphServiceClient by dependency injection
                // And use an in memory token cache
                .AddMicrosoftGraph(Configuration.GetSection("DownstreamApi"))
                .AddInMemoryTokenCaches();

      services.AddControllersWithViews(options =>
      {
          var policy = new AuthorizationPolicyBuilder()
              .RequireAuthenticatedUser()
              .Build();
          options.Filters.Add(new AuthorizeFilter(policy));
      });

      // Enables a UI and controller for sign in and sign out.
      services.AddRazorPages()
          .AddMicrosoftIdentityUI();
  }
```

`AddAuthentication()`Metoda konfiguruje usługę w celu dodania uwierzytelniania opartego na plikach cookie, który jest używany w scenariuszach przeglądarki i do ustawiania wyzwania na OpenID Connect Connect.

W wierszu zawierającym `.AddMicrosoftIdentityWebApp` Dodano uwierzytelnianie platformy tożsamości firmy Microsoft do aplikacji. Jest to obsługiwane przez [firmę Microsoft. Identity. Web](microsoft-identity-web.md). Następnie jest skonfigurowany do logowania się przy użyciu platformy tożsamości firmy Microsoft na podstawie informacji w `AzureAD` sekcji *appsettings.jsw* pliku konfiguracyjnym:

| *appsettings.js* klucza | Opis                                                                                                                                                          |
|------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `ClientId`             | **Identyfikator aplikacji (klienta)** zarejestrowany w Azure Portal.                                                                                       |
| `Instance`             | Punkt końcowy usługi tokenu zabezpieczającego (STS) dla użytkownika do uwierzytelnienia. Ta wartość jest zazwyczaj `https://login.microsoftonline.com/` wskazywana na chmurę publiczną platformy Azure. |
| `TenantId`             | Nazwa dzierżawy lub jej identyfikatora dzierżawy (GUID) lub *wspólne* Logowanie użytkowników przy użyciu kont służbowych lub kont osobistych firmy Microsoft.                             |

`EnableTokenAcquisitionToCallDownstreamApi`Metoda umożliwia aplikacji uzyskanie tokenu w celu wywołania chronionych interfejsów API sieci Web. `AddMicrosoftGraph` umożliwia administratorom i stronom Razor korzystanie bezpośrednio z programu `GraphServiceClient` (przez wstrzyknięcie zależności), a `AddInMemoryTokenCaches` metody umożliwiają aplikacji korzystanie z pamięci podręcznej tokenów.

`Configure()`Metoda zawiera dwie istotne metody, `app.UseAuthentication()` `app.UseAuthorization()` które pozwalają na ich nazwanych funkcji. Ponadto w `Configure()` metodzie należy zarejestrować trasy sieci Web tożsamości firmy Microsoft z co najmniej jednym wywołaniem `endpoints.MapControllerRoute()` lub wywołaniem do `endpoints.MapControllers()` .

```csharp
app.UseAuthentication();
app.UseAuthorization();

app.UseEndpoints(endpoints =>
{

    endpoints.MapControllerRoute(
        name: "default",
        pattern: "{controller=Home}/{action=Index}/{id?}");
    endpoints.MapRazorPages();
});

// endpoints.MapControllers(); // REQUIRED if MapControllerRoute() isn't called.
```

### <a name="protect-a-controller-or-a-controllers-method"></a>Ochrona kontrolera lub metody kontrolera

Można chronić kontroler lub jego metody przez zastosowanie `[Authorize]` atrybutu do klasy kontrolera lub jednej lub więcej z jej metod. Ten `[Authorize]` atrybut ogranicza dostęp przez umożliwienie tylko uwierzytelnionym użytkownikom. Jeśli użytkownik nie jest już uwierzytelniony, można uruchomić wyzwanie uwierzytelniania w celu uzyskania dostępu do kontrolera. W tym przewodniku szybki start zakresy są odczytywane z pliku konfiguracji:

```CSharp
[AuthorizeForScopes(ScopeKeySection = "DownstreamApi:Scopes")]
public async Task<IActionResult> Index()
{
    var user = await _graphServiceClient.Me.Request().GetAsync();
    ViewData["ApiResult"] = user.DisplayName;

    return View();
}
 ```

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Następne kroki

Repozytorium GitHub zawierające przykład kodu ASP.NET Core, do którego odwołuje się ten przewodnik Szybki Start, zawiera instrukcje i więcej przykładów kodu, które pokazują, jak:

- Dodawanie uwierzytelniania do nowej aplikacji sieci Web ASP.NET Core
- Wywołaj Microsoft Graph, inne interfejsy API firmy Microsoft lub własne interfejsy API sieci Web
- Dodaj autoryzację
- Logowanie użytkowników w chmurach narodowych lub przy użyciu tożsamości społecznościowych

> [!div class="nextstepaction"]
> [ASP.NET Core samouczków aplikacji sieci Web w witrynie GitHub](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/)
