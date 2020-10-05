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
ms.openlocfilehash: bf80a15131a8808359d21d5a9655ef04db236178
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "91613496"
---
# <a name="quickstart-add-sign-in-with-microsoft-to-an-aspnet-core-web-app"></a>Szybki Start: Dodawanie logowania z firmą Microsoft do aplikacji internetowej ASP.NET Core

W tym przewodniku szybki start użyjesz przykładowego kodu, aby dowiedzieć się, w jaki sposób aplikacja sieci Web ASP.NET Core może się zalogować na kontach osobistych (hotmail.com, outlook.com, inne) i służbowych z dowolnego wystąpienia Azure Active Directory (Azure AD). (Zobacz [, jak działa przykład](#how-the-sample-works) dla ilustracji).

> [!div renderon="docs"]
> ## <a name="prerequisites"></a>Wymagania wstępne
>
> * [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) lub [Visual Studio Code](https://code.visualstudio.com/)
> * [Zestaw .NET Core SDK 3.1 +](https://dotnet.microsoft.com/download)
>
> ## <a name="register-and-download-your-quickstart-app"></a>Rejestrowanie i pobieranie aplikacji Szybki start
> Istnieją dwie opcje uruchamiania aplikacji Szybki start:
> * [Ekspresowe] [Opcja 1. Zarejestrowanie i automatyczne skonfigurowanie aplikacji, a następnie pobranie przykładowego kodu](#option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample)
> * [Ręczne] [Opcja 2. Zarejestrowanie i ręczne skonfigurowanie aplikacji oraz przykładowego kodu](#option-2-register-and-manually-configure-your-application-and-code-sample)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Opcja 1. Zarejestrowanie i automatyczne skonfigurowanie aplikacji, a następnie pobranie przykładowego kodu
>
> 1. Przejdź do [Rejestracje aplikacji Azure Portal](https://aka.ms/aspnetcore2-1-aad-quickstart-v2).
> 1. Wprowadź nazwę aplikacji i wybierz pozycję **Zarejestruj**.
> 1. Postępuj zgodnie z instrukcjami, aby jednym kliknięciem pobrać i automatycznie skonfigurować nową aplikację.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Opcja 2. Zarejestrowanie i ręczne skonfigurowanie aplikacji oraz przykładowego kodu
>
> #### <a name="step-1-register-your-application"></a>Krok 1. Rejestrowanie aplikacji
> Aby zarejestrować aplikację i ręcznie dodać informacje na temat rejestracji aplikacji do rozwiązania, wykonaj następujące czynności:
>
> 1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
> 1. Jeśli masz dostęp do wielu dzierżawców, Użyj filtru **katalogów i subskrypcji** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: w górnym menu, aby wybrać dzierżawcę, w którym chcesz zarejestrować aplikację.
> 1. Wyszukaj i wybierz pozycję **Azure Active Directory**.
> 1. W obszarze **Zarządzaj**wybierz opcję **rejestracje aplikacji**, a następnie pozycję **Nowa rejestracja**.
> 1. Wprowadź **nazwę** aplikacji, na przykład `AspNetCore-Quickstart` . Użytkownicy Twojej aplikacji mogą zobaczyć tę nazwę i można ją później zmienić.
> 1. Wprowadź **Identyfikator URI przekierowania**`https://localhost:44321/`
> 1. Wybierz pozycję **Zarejestruj**.
> 1. W obszarze **Zarządzaj**wybierz pozycję **uwierzytelnianie**.
> 1. W obszarze **identyfikatory URI przekierowania**wybierz pozycję **Dodaj identyfikator URI**, a następnie wprowadź `https://localhost:44321/signin-oidc`
> 1. Wprowadź **adres URL wylogowywania**`https://localhost:44321/signout-oidc`
> 1. W obszarze **Niejawne przyznanie** wybierz pozycję **Tokeny identyfikatorów**.
> 1. Wybierz pozycję **Zapisz**.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Krok 1. Konfigurowanie aplikacji w witrynie Azure Portal
> Aby przykład kodu w tym przewodniku Szybki Start działał, należy dodać adresy URL odpowiedzi jako `https://localhost:44321/` i `https://localhost:44321/signin-oidc`, dodać adres URL wylogowywania jako `https://localhost:44321/signout-oidc`, a następnie zażądać, aby tokeny Identyfikatorów były wystawiane przez punkt końcowy autoryzacji.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Wprowadź tę zmianę automatycznie]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Już skonfigurowano](media/quickstart-v2-aspnet-webapp/green-check.png) Twoja aplikacja została skonfigurowana za pomocą tych atrybutów.

#### <a name="step-2-download-your-aspnet-core-project"></a>Krok 2. Pobieranie projektu ASP.NET Core

> [!div renderon="docs"]
> [Pobierz rozwiązanie ASP.NET Core](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/archive/aspnetcore3-1.zip)

> [!div renderon="portal" class="sxs-lookup"]
> Uruchom projekt.

> [!div renderon="portal" class="sxs-lookup" id="autoupdate" class="nextstepaction"]
> [Pobierz przykład kodu](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/archive/aspnetcore3-1.zip)

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
>    ```
>
>    - Zamień na `Enter_the_Application_Id_here` **Identyfikator aplikacji (klienta)** , która została zarejestrowana w Azure Portal. **Identyfikator aplikacji (klienta)** możesz znaleźć na stronie aplikacji **Przegląd** aplikacji.
>    - Zamień `common` na jedną z następujących wartości:
>       - Jeśli aplikacja obsługuje **konta tylko w tym katalogu organizacji**, Zastąp tę wartość **identyfikatorem katalogu (dzierżawy)** (identyfikatorem GUID) lub **nazwą dzierżawy** (na przykład `contoso.onmicrosoft.com` ). **Identyfikator katalogu (dzierżawcy)** można znaleźć na stronie **przeglądu** aplikacji.
>       - Jeśli aplikacja obsługuje tryb**Konta w dowolnym katalogu organizacyjnym**, zastąp tę wartość za pomocą wartości `organizations`
>       - Jeśli aplikacja obsługuje **wszystkich konto Microsoft użytkowników**, pozostaw tę wartość jako `common`
>
> W ramach tego przewodnika Szybki Start nie zmieniaj żadnych innych wartości w *appsettings.js* pliku.
>
> #### <a name="step-4-build-and-run-the-application"></a>Krok4. Skompiluj i uruchom aplikację
>
> Kompiluj i uruchamiaj aplikację w programie Visual Studio, wybierając menu **Debuguj** , > **rozpocząć debugowanie**lub naciskając klawisz `F5` .
>
> Zostanie wyświetlony monit o podanie poświadczeń, a następnie prośba o zgodę na uprawnienia wymagane przez aplikację. Wybierz pozycję **Akceptuj** w monicie o zgodę.
>
> :::image type="content" source="media/quickstart-v2-aspnet-core-webapp/webapp-01-consent.png" alt-text="Okno dialogowe zgody zawierające uprawnienia, które aplikacja żąda od użytkownika >":::
>
> Po zapoznaniu się z żądanymi uprawnieniami aplikacja wyświetli komunikat, że użytkownik zalogował się pomyślnie przy użyciu poświadczeń Azure Active Directory.
>
> :::image type="content" source="media/quickstart-v2-aspnet-core-webapp/webapp-02-signed-in.png" alt-text="Okno dialogowe zgody zawierające uprawnienia, które aplikacja żąda od użytkownika >":::

## <a name="more-information"></a>Więcej informacji

Ta sekcja zawiera omówienie kodu wymaganego do zalogowania użytkowników. Ten przegląd może być przydatny do zrozumienia sposobu działania kodu, głównych argumentów oraz, jeśli chcesz dodać logowanie do istniejącej aplikacji ASP.NET Core.

### <a name="how-the-sample-works"></a>Jak działa przykład
![Pokazuje sposób działania przykładowej aplikacji wygenerowanej przez ten przewodnik Szybki Start](media/quickstart-v2-aspnet-core-webapp/aspnetcorewebapp-intro.svg)

### <a name="startup-class"></a>Klasa początkowa

Oprogramowanie pośredniczące *Microsoft. AspNetCore. Authentication* używa `Startup` klasy, która jest wykonywana po zainicjowaniu procesu hostingu:

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

`AddAuthentication()`Metoda konfiguruje usługę w celu dodania uwierzytelniania opartego na plikach cookie, który jest używany w scenariuszach przeglądarki i do ustawiania wyzwania na OpenID Connect Connect.

Wiersz zawierający `.AddMicrosoftIdentityWebApp` Dodawanie uwierzytelniania platformy tożsamości firmy Microsoft do aplikacji. Następnie jest skonfigurowany do logowania się przy użyciu punktu końcowego platformy tożsamości firmy Microsoft na podstawie informacji w `AzureAD` sekcji *appsettings.jsw* pliku konfiguracyjnym:

| *appsettings.js* klucza | Opis                                                                                                                                                          |
|------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `ClientId`             | **Identyfikator aplikacji (klienta)** zarejestrowany w Azure Portal.                                                                                       |
| `Instance`             | Punkt końcowy usługi tokenu zabezpieczającego (STS) dla użytkownika do uwierzytelnienia. Ta wartość jest zazwyczaj `https://login.microsoftonline.com/` wskazywana na chmurę publiczną platformy Azure. |
| `TenantId`             | Nazwa dzierżawy lub jej identyfikatora dzierżawy (GUID) lub *wspólne* Logowanie użytkowników przy użyciu kont służbowych lub kont osobistych firmy Microsoft.                             |

`Configure()`Metoda zawiera dwie istotne metody, `app.UseCookiePolicy()` `app.UseAuthentication()` które pozwalają na ich nazwanych funkcji.

```csharp
// This method gets called by the runtime. Use this method to configure the HTTP request pipeline.
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    // more code
    app.UseAuthentication();
    app.UseAuthorization();
    // more code
}
```

### <a name="protect-a-controller-or-a-controllers-method"></a>Ochrona kontrolera lub metody kontrolera

Kontroler lub jego metody można chronić za pomocą atrybutu `[Authorize]`. Ten atrybut ogranicza dostęp do kontrolera lub metod przez zezwolenie tylko uwierzytelnionym użytkownikom, co oznacza, że można uruchomić wyzwanie uwierzytelniania w celu uzyskania dostępu do kontrolera, jeśli użytkownik nie jest uwierzytelniony.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Następne kroki

Repozytorium GitHub zawierające ten ASP.NET Core samouczek zawiera instrukcje i więcej przykładów kodu, które pokazują, jak:

- Dodawanie uwierzytelniania do nowej aplikacji sieci Web ASP.NET Core
- Wywołaj Microsoft Graph, inne interfejsy API firmy Microsoft lub własne interfejsy API sieci Web
- Dodaj autoryzację
- Logowanie użytkowników w chmurach narodowych lub przy użyciu tożsamości społecznościowych

> [!div class="nextstepaction"]
> [ASP.NET Core samouczków aplikacji sieci Web w witrynie GitHub](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/)
