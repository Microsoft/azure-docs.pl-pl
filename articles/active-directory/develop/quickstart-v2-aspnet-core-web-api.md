---
title: 'Szybki Start: Ochrona ASP.NET Core internetowego interfejsu API za pomocą platformy tożsamości firmy Microsoft | Azure'
titleSuffix: Microsoft identity platform
description: W tym przewodniku szybki start pobierasz i modyfikujesz przykładowy kod, który pokazuje, jak chronić ASP.NET Core Web API przy użyciu platformy tożsamości firmy Microsoft na potrzeby autoryzacji.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 09/22/2020
ms.author: jmprieur
ms.custom: devx-track-csharp, scenarios:getting-started, languages:aspnet-core
ms.openlocfilehash: 30593c51f17b99989409ddd22c9c1caa28468039
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104720835"
---
# <a name="quickstart-protect-an-aspnet-core-web-api-with-the-microsoft-identity-platform"></a>Szybki Start: Ochrona ASP.NET Core internetowego interfejsu API za pomocą platformy tożsamości firmy Microsoft

W tym przewodniku szybki start pobrano przykład kodu internetowego interfejsu API ASP.NET Core i zapoznaj się ze sposobem, w jaki ogranicza dostęp do zasobów tylko do autoryzowanych kont. Przykład obsługuje autoryzację osobistych kont Microsoft i kont w dowolnej organizacji Azure Active Directory (Azure AD).

> [!div renderon="docs"]
> ## <a name="prerequisites"></a>Wymagania wstępne
>
> - Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
> - [Dzierżawa Azure Active Directory](quickstart-create-new-tenant.md)
> - [Zestaw .NET Core SDK 3.1 +](https://dotnet.microsoft.com/)
> - [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) lub [Visual Studio Code](https://code.visualstudio.com/)
>
> ## <a name="step-1-register-the-application"></a>Krok 1. rejestrowanie aplikacji
>
> Najpierw Zarejestruj internetowy interfejs API w dzierżawie usługi Azure AD i Dodaj zakres, wykonując następujące czynności:
>
> 1. Zaloguj się w witrynie <a href="https://portal.azure.com/" target="_blank">Azure Portal</a>.
> 1. Jeśli masz dostęp do wielu dzierżawców, Użyj filtru **katalogów i subskrypcji** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: w górnym menu, aby wybrać dzierżawcę, w którym chcesz zarejestrować aplikację.
> 1. Wyszukaj i wybierz pozycję **Azure Active Directory**.
> 1. W obszarze **Zarządzaj** wybierz pozycję **rejestracje aplikacji**  >  **Nowa rejestracja**.
> 1. W obszarze **Nazwa** wprowadź nazwę aplikacji. Na przykład wprowadź **AspNetCoreWebApi-szybkiego startu**. Użytkownicy Twojej aplikacji będą widzieć tę nazwę i można ją później zmienić.
> 1. Wybierz pozycję **Zarejestruj**.
> 1. W obszarze **Zarządzaj** wybierz opcję **Uwidocznij interfejs API**  >  **Dodaj zakres**. W przypadku **identyfikatora URI aplikacji** Zaakceptuj wartość domyślną, wybierając pozycję **Zapisz i Kontynuuj**, a następnie wprowadź następujące szczegóły:
>    - **Nazwa zakresu**: `access_as_user`
>    - **Kto może wyrazić zgodę?**: **Administratorzy i użytkownicy**
>    - **Nazwa wyświetlana zgody administratora**: `Access AspNetCoreWebApi-Quickstart`
>    - **Opis zgody administratora**: `Allows the app to access AspNetCoreWebApi-Quickstart as the signed-in user.`
>    - **Nazwa wyświetlana zgody użytkownika**: `Access AspNetCoreWebApi-Quickstart`
>    - **Opis zgody użytkownika**: `Allow the application to access AspNetCoreWebApi-Quickstart on your behalf.`
>    - **Stan**: **włączone**
> 1. Wybierz pozycję **Dodaj zakres** , aby zakończyć dodawanie zakresu.

## <a name="step-2-download-the-aspnet-core-project"></a>Krok 2. Pobieranie projektu ASP.NET Core

> [!div renderon="docs"]
> [Pobierz rozwiązanie ASP.NET Core](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/archive/aspnetcore3-1.zip) z usługi GitHub.

[!INCLUDE [active-directory-develop-path-length-tip](../../../includes/active-directory-develop-path-length-tip.md)]

> [!div renderon="docs"]
> ## <a name="step-3-configure-the-aspnet-core-project"></a>Krok 3. Konfigurowanie projektu ASP.NET Core
>
> W tym kroku Skonfiguruj przykładowy kod do pracy z utworzoną wcześniej rejestracją aplikacji.
>
> 1. Wyodrębnij archiwum zip do folderu w folderze głównym dysku. Na przykład Wyodrębnij do *C:\Azure-Samples*.
>
>    Zalecamy wyodrębnienie archiwum do katalogu w sąsiedztwie katalogu głównego dysku, aby uniknąć błędów spowodowanych ograniczeniami długości ścieżki w systemie Windows.
>
> 1. Otwórz rozwiązanie w folderze *WebAPI* w edytorze kodu.
> 1. Otwórz *appsettings.js* pliku i zmodyfikuj następujący kod:
>
>    ```json
>    "ClientId": "Enter_the_Application_Id_here",
>    "TenantId": "Enter_the_Tenant_Info_Here"
>    ```
>
>    - Zamień na `Enter_the_Application_Id_here` Identyfikator aplikacji (klienta), która została zarejestrowana w Azure Portal. Identyfikator aplikacji (klienta) można znaleźć na stronie **Przegląd** aplikacji.
>    - Zamień `Enter_the_Tenant_Info_Here` na jedną z następujących wartości:
>       - Jeśli aplikacja obsługuje **konta tylko w tym katalogu organizacji**, Zastąp tę wartość identyfikatorem katalogu (dzierżawy) (identyfikatorem GUID) lub nazwą dzierżawy (na przykład `contoso.onmicrosoft.com` ). Identyfikator katalogu (dzierżawcy) można znaleźć na stronie **przeglądu** aplikacji.
>       - Jeśli aplikacja obsługuje **konta w dowolnym katalogu organizacyjnym**, Zastąp tę wartość wartością `organizations` .
>       - Jeśli aplikacja obsługuje **wszystkich konto Microsoft użytkowników**, pozostaw tę wartość jako `common` .
>
> W ramach tego przewodnika Szybki Start nie zmieniaj żadnych innych wartości w *appsettings.js* pliku.

## <a name="how-the-sample-works"></a>Jak działa przykład

Internetowy interfejs API odbiera token z aplikacji klienckiej, a kod w internetowym interfejsie API sprawdza token. Ten scenariusz został szczegółowo opisany w [scenariuszu: chroniony internetowy interfejs API](scenario-protected-web-api-overview.md).

### <a name="startup-class"></a>Klasa początkowa

Oprogramowanie pośredniczące *Microsoft. AspNetCore. Authentication* używa `Startup` klasy, która jest wykonywana, gdy rozpocznie się proces hostingu. W swojej `ConfigureServices` metodzie `AddMicrosoftIdentityWebApi` wywoływana jest metoda rozszerzająca dostarczona przez *Microsoft. Identity. Web* .

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
                .AddMicrosoftIdentityWebApi(Configuration, "AzureAd");
    }
```

`AddAuthentication()`Metoda konfiguruje usługę do dodawania uwierzytelniania opartego na JwtBearer.

Wiersz, który zawiera `.AddMicrosoftIdentityWebApi` dodaje autoryzację platformy tożsamości firmy Microsoft do internetowego interfejsu API. Następnie jest skonfigurowany do weryfikowania tokenów dostępu wystawionych przez platformę tożsamości firmy Microsoft na podstawie informacji w `AzureAD` sekcji *appsettings.jsw* pliku konfiguracyjnym:

| *appsettings.js* klucza | Opis                                                                                                                                                          |
|------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `ClientId`             | Identyfikator aplikacji (klienta) zarejestrowany w Azure Portal.                                                                                       |
| `Instance`             | Punkt końcowy usługi tokenu zabezpieczającego (STS) dla użytkownika do uwierzytelnienia. Ta wartość jest zazwyczaj `https://login.microsoftonline.com/` wskazywana na chmurę publiczną platformy Azure. |
| `TenantId`             | Nazwa dzierżawy lub jej identyfikatora dzierżawy (GUID) lub `common` logowania użytkowników przy użyciu kont służbowych lub kont osobistych firmy Microsoft.                             |

`Configure()`Metoda zawiera dwie istotne metody `app.UseAuthentication()` i `app.UseAuthorization()` , które umożliwiają ich nazwane funkcje:

```csharp
// The runtime calls this method. Use this method to configure the HTTP request pipeline.
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    // more code
    app.UseAuthentication();
    app.UseAuthorization();
    // more code
}
```

### <a name="protecting-a-controller-a-controllers-method-or-a-razor-page"></a>Ochrona kontrolera, metody kontrolera lub strony Razor

Można chronić kontroler lub metody kontrolera przy użyciu `[Authorize]` atrybutu. Ten atrybut ogranicza dostęp do kontrolera lub metod przez umożliwienie tylko uwierzytelnionym użytkownikom. Wyzwanie uwierzytelniania można uruchomić w celu uzyskania dostępu do kontrolera, jeśli użytkownik nie jest uwierzytelniony.

```csharp
namespace webapi.Controllers
{
    [Authorize]
    [ApiController]
    [Route("[controller]")]
    public class WeatherForecastController : ControllerBase
```

### <a name="validation-of-scope-in-the-controller"></a>Walidacja zakresu w kontrolerze

Kod w interfejsie API sprawdza, czy wymagane zakresy znajdują się w tokenie przy użyciu `HttpContext.VerifyUserHasAnyAcceptedScope(scopeRequiredByApi);` :

```csharp
namespace webapi.Controllers
{
    [Authorize]
    [ApiController]
    [Route("[controller]")]
    public class WeatherForecastController : ControllerBase
    {
        // The web API will only accept tokens 1) for users, and 2) having the "access_as_user" scope for this API
        static readonly string[] scopeRequiredByApi = new string[] { "access_as_user" };

        [HttpGet]
        public IEnumerable<WeatherForecast> Get()
        {
            HttpContext.VerifyUserHasAnyAcceptedScope(scopeRequiredByApi);

            // some code here
        }
    }
}
```

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Następne kroki

Repozytorium GitHub zawierające ten ASP.NET Core przykład kodu internetowego interfejsu API zawiera instrukcje i więcej przykładów kodu, które pokazują, jak:

- Dodaj uwierzytelnianie do nowego internetowego interfejsu API ASP.NET Core.
- Wywołaj interfejs API sieci Web z aplikacji klasycznej.
- Wywołaj interfejsy API podrzędne, takie jak Microsoft Graph i inne interfejsy API firmy Microsoft.

> [!div class="nextstepaction"]
> [ASP.NET Core samouczków interfejsu API sieci Web w witrynie GitHub](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2)
