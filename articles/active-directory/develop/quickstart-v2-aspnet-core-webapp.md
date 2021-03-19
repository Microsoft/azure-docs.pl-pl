---
title: 'Szybki Start: Dodawanie logowania z firmą Microsoft do aplikacji internetowej ASP.NET Core | Azure'
titleSuffix: Microsoft identity platform
description: W tym przewodniku szybki start dowiesz się, jak aplikacja implementuje logowanie firmy Microsoft w aplikacji internetowej ASP.NET Core przy użyciu połączenia OpenID Connect
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 09/11/2020
ms.author: jmprieur
ms.custom: devx-track-csharp, aaddev, identityplatformtop40, scenarios:getting-started, languages:aspnet-core
ms.openlocfilehash: e7296b04e3e912e96ac8c2ed77b44288324c262f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104578706"
---
# <a name="quickstart-add-sign-in-with-microsoft-to-an-aspnet-core-web-app"></a>Szybki Start: Dodawanie logowania z firmą Microsoft do aplikacji internetowej ASP.NET Core

W tym przewodniku szybki start pobierasz i uruchamiasz przykładowy kod, który pokazuje, jak aplikacja sieci Web ASP.NET Core może zalogować użytkowników z dowolnej organizacji Azure Active Directory (Azure AD).  

> [!div renderon="docs"]
> Na poniższym diagramie przedstawiono sposób działania przykładowej aplikacji:
>
> ![Diagram interakcji między przeglądarką sieci Web, aplikacją internetową i platformą tożsamości firmy Microsoft w przykładowej aplikacji.](media/quickstart-v2-aspnet-core-webapp/aspnetcorewebapp-intro.svg)
>
> ## <a name="prerequisites"></a>Wymagania wstępne
>
> * [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) lub [Visual Studio Code](https://code.visualstudio.com/)
> * [Zestaw .NET Core SDK 3.1 +](https://dotnet.microsoft.com/download)
>
> ## <a name="register-and-download-the-app"></a>Rejestrowanie i pobieranie aplikacji
> Dostępne są dwie opcje rozpoczęcia kompilowania aplikacji: Konfiguracja automatyczna lub ręczna.
>
> ### <a name="automatic-configuration"></a>Automatyczna konfiguracja
> Jeśli chcesz automatycznie skonfigurować aplikację, a następnie pobrać przykład kodu, wykonaj następujące kroki:
>
> 1. Przejdź do <a href="https://aka.ms/aspnetcore2-1-aad-quickstart-v2/" target="_blank">strony Azure Portal na potrzeby rejestracji aplikacji</a>.
> 1. Wprowadź nazwę aplikacji i wybierz pozycję **Zarejestruj**.
> 1. Postępuj zgodnie z instrukcjami, aby pobrać i automatycznie skonfigurować nową aplikację w jednym kliknięciem.
>
> ### <a name="manual-configuration"></a>Konfiguracja ręczna
> Jeśli chcesz ręcznie skonfigurować aplikację i przykład kodu, Użyj poniższych procedur.
> #### <a name="step-1-register-your-application"></a>Krok 1. Rejestrowanie aplikacji
> 1. Zaloguj się w witrynie <a href="https://portal.azure.com/" target="_blank">Azure Portal</a>.
> 1. Jeśli masz dostęp do wielu dzierżawców, Użyj filtru **katalogów i subskrypcji** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: w górnym menu, aby wybrać dzierżawcę, w którym chcesz zarejestrować aplikację.
> 1. Wyszukaj i wybierz pozycję **Azure Active Directory**.
> 1. W obszarze **Zarządzaj** wybierz pozycję **rejestracje aplikacji**  >  **Nowa rejestracja**.
> 1. W obszarze **Nazwa** wprowadź nazwę aplikacji. Na przykład wprowadź **AspNetCore-szybkiego startu**. Użytkownicy Twojej aplikacji będą widzieć tę nazwę i można ją później zmienić.
> 1. Dla **identyfikatora URI przekierowania** wprowadź **https://localhost:44321/signin-oidc** .
> 1. Wybierz pozycję **Zarejestruj**.
> 1. W obszarze **Zarządzaj** wybierz pozycję **uwierzytelnianie**.
> 1. W przypadku **adresu URL wylogowywania z kanału frontonu** wprowadź **https://localhost:44321/signout-oidc** .
> 1. W obszarze **niejawne przyznanie i przepływy hybrydowe** wybierz pozycję **identyfikatory tokenów**.
> 1. Wybierz pozycję **Zapisz**.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Krok 1. Konfigurowanie aplikacji w witrynie Azure Portal
> Aby uzyskać przykładowy kod w tym przewodniku szybki start:
> - Dla **identyfikatora URI przekierowania** wprowadź **https://localhost:44321/** i **https://localhost:44321/signin-oidc** .
> - W przypadku **adresu URL wylogowywania z kanału frontonu** wprowadź **https://localhost:44321/signout-oidc** . 
>
> Punkt końcowy autoryzacji będzie wystawiał tokeny identyfikatora żądania.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Wprowadź tę zmianę automatycznie]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Już skonfigurowano](media/quickstart-v2-aspnet-webapp/green-check.png) Twoja aplikacja została skonfigurowana za pomocą tych atrybutów.

#### <a name="step-2-download-the-aspnet-core-project"></a>Krok 2. Pobieranie projektu ASP.NET Core

> [!div renderon="docs"]
> [Pobierz rozwiązanie ASP.NET Core](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/archive/aspnetcore3-1.zip)

> [!div renderon="portal" class="sxs-lookup"]
> Uruchom projekt.

> [!div renderon="portal" class="sxs-lookup" id="autoupdate" class="nextstepaction"]
> [Pobierz przykład kodu](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/archive/aspnetcore3-1.zip)

[!INCLUDE [active-directory-develop-path-length-tip](../../../includes/active-directory-develop-path-length-tip.md)]

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>Krok 3. Twoja aplikacja jest skonfigurowana i gotowa do uruchomienia
> Twój projekt został skonfigurowany z wartościami właściwości aplikacji i jest gotowy do uruchomienia.
> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`
> [!div renderon="docs"]
> #### <a name="step-3-configure-your-aspnet-core-project"></a>Krok 3. Konfigurowanie projektu ASP.NET Core
> 1. Wyodrębnij archiwum zip do folderu lokalnego znajdującego się w katalogu głównym dysku. Na przykład Wyodrębnij do *C:\Azure-Samples*.
> 
>    Zalecamy wyodrębnienie archiwum do katalogu w sąsiedztwie katalogu głównego dysku, aby uniknąć błędów spowodowanych ograniczeniami długości ścieżki w systemie Windows.
> 1. Otwórz rozwiązanie w programie Visual Studio 2019.
> 1. Otwórz *appsettings.js* pliku i zmodyfikuj następujący kod:
>
>    ```json
>    "Domain": "Enter the domain of your tenant, e.g. contoso.onmicrosoft.com",
>    "ClientId": "Enter_the_Application_Id_here",
>    "TenantId": "common",
>    ```
>
>    - Zamień na `Enter_the_Application_Id_here` Identyfikator aplikacji (klienta), która została zarejestrowana w Azure Portal. Wartość **identyfikatora aplikacji (klienta)** można znaleźć na stronie **Przegląd** aplikacji.
>    - Zamień `common` na jedną z następujących wartości:
>       - Jeśli aplikacja obsługuje **konta tylko w tym katalogu organizacji**, Zastąp tę wartość identyfikatorem katalogu (dzierżawy) (identyfikatorem GUID) lub nazwą dzierżawy (na przykład `contoso.onmicrosoft.com` ). Wartość **identyfikatora katalogu (dzierżawy)** można znaleźć na stronie **Przegląd** aplikacji.
>       - Jeśli aplikacja obsługuje **konta w dowolnym katalogu organizacyjnym**, Zastąp tę wartość wartością `organizations` .
>       - Jeśli aplikacja obsługuje **wszystkich konto Microsoft użytkowników**, pozostaw tę wartość jako `common` .
>
> W ramach tego przewodnika Szybki Start nie zmieniaj żadnych innych wartości w *appsettings.js* pliku.
>
> #### <a name="step-4-build-and-run-the-application"></a>Krok4. Skompiluj i uruchom aplikację
>
> Kompiluj i uruchamiaj aplikację w programie Visual Studio, wybierając menu **Debuguj** , > **rozpocząć debugowanie** lub naciskając klawisz F5.
>
> Zostanie wyświetlony monit o podanie poświadczeń, a następnie prośba o zgodę na uprawnienia wymagane przez aplikację. Wybierz pozycję **Akceptuj** w monicie o zgodę.
>
> :::image type="content" source="media/quickstart-v2-aspnet-core-webapp/webapp-01-consent.png" alt-text="Zrzut ekranu przedstawiający okno dialogowe wyrażanie zgody z uprawnieniami wymaganymi przez aplikację od użytkownika.":::
>
> Po zapoznaniu się z żądanymi uprawnieniami aplikacja wyświetli komunikat, że użytkownik zalogował się pomyślnie przy użyciu poświadczeń Azure Active Directory.
>
> :::image type="content" source="media/quickstart-v2-aspnet-core-webapp/webapp-02-signed-in.png" alt-text="Zrzut ekranu przedstawiający przeglądarkę internetową, w której jest wyświetlana uruchomiona aplikacja sieci Web i zalogowany użytkownik.":::

## <a name="more-information"></a>Więcej informacji

Ta sekcja zawiera omówienie kodu wymaganego do zalogowania użytkowników. Ten przegląd może być przydatny do zrozumienia, w jaki sposób działa kod, opis głównych argumentów i sposób dodawania logowania do istniejącej aplikacji ASP.NET Core.

> [!div class="sxs-lookup" renderon="portal"]
> ### <a name="how-the-sample-works"></a>Jak działa przykład
>
> ![Diagram interakcji między przeglądarką sieci Web, aplikacją internetową i platformą tożsamości firmy Microsoft w przykładowej aplikacji.](media/quickstart-v2-aspnet-core-webapp/aspnetcorewebapp-intro.svg)

### <a name="startup-class"></a>Klasa początkowa

Oprogramowanie pośredniczące *Microsoft. AspNetCore. Authentication* używa `Startup` klasy, która jest uruchamiana, gdy rozpocznie się proces hostingu:

```csharp
  public void ConfigureServices(IServiceCollection services)
  {
      services.AddAuthentication(OpenIdConnectDefaults.AuthenticationScheme)
          .AddMicrosoftIdentityWebApp(Configuration.GetSection("AzureAd"));

      services.AddControllersWithViews(options =>
      {
          var policy = new AuthorizationPolicyBuilder()
              .RequireAuthenticatedUser()
              .Build();
          options.Filters.Add(new AuthorizeFilter(policy));
      });
      services.AddRazorPages()
          .AddMicrosoftIdentityUI();
  }
```

`AddAuthentication()`Metoda konfiguruje usługę w celu dodania uwierzytelniania opartego na plikach cookie. To uwierzytelnianie jest używane w scenariuszach przeglądarki oraz do ustawiania wyzwania OpenID Connect Connect.

Wiersz, w którym znajduje `.AddMicrosoftIdentityWebApp` się dodawanie uwierzytelniania platformy tożsamości firmy Microsoft do aplikacji. Następnie aplikacja zostanie skonfigurowana w celu logowania użytkowników na podstawie następujących informacji w `AzureAD` sekcji *appsettings.jsw* pliku konfiguracyjnym:

| *appsettings.js* klucza | Opis                                                                                                                                                          |
|------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `ClientId`             | Identyfikator aplikacji (klienta) zarejestrowany w Azure Portal.                                                                                       |
| `Instance`             | Punkt końcowy usługi tokenu zabezpieczającego (STS) dla użytkownika do uwierzytelnienia. Ta wartość jest zazwyczaj `https://login.microsoftonline.com/` wskazywana na chmurę publiczną platformy Azure. |
| `TenantId`             | Nazwa dzierżawy lub identyfikator dzierżawy (GUID) lub `common` Logowanie użytkowników przy użyciu kont służbowych lub kont osobistych firmy Microsoft.                             |

`Configure()`Metoda zawiera dwie istotne metody, `app.UseAuthentication()` `app.UseAuthorization()` które pozwalają na ich nazwanych funkcji. Ponadto w `Configure()` metodzie należy zarejestrować trasy sieci Web tożsamości firmy Microsoft z co najmniej jednym wywołaniem `endpoints.MapControllerRoute()` lub wywołaniem `endpoints.MapControllers()` :

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

### <a name="attribute-for-protecting-a-controller-or-methods"></a>Atrybut do ochrony kontrolera lub metod

Można chronić kontroler lub metody kontrolera przy użyciu `[Authorize]` atrybutu. Ten atrybut ogranicza dostęp do kontrolera lub metod przez umożliwienie tylko uwierzytelnionym użytkownikom. Wyzwanie uwierzytelniania można następnie uruchomić w celu uzyskania dostępu do kontrolera, jeśli użytkownik nie jest uwierzytelniony.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Następne kroki

Repozytorium GitHub zawierające ten ASP.NET Core samouczek zawiera instrukcje i więcej przykładów kodu, które pokazują, jak:

- Dodaj uwierzytelnianie do nowej aplikacji sieci Web ASP.NET Core.
- Wywołaj Microsoft Graph, inne interfejsy API firmy Microsoft lub własne interfejsy API sieci Web.
- Dodaj autoryzację.
- Użytkownicy logują się w chmurach narodowych lub z tożsamościami społecznościowymi.

> [!div class="nextstepaction"]
> [ASP.NET Core samouczków aplikacji sieci Web w witrynie GitHub](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/)
