---
title: Przewodnik Szybki Start dotyczący dodawania flag funkcji do ASP.NET Core
description: Dodawanie flag funkcji do ASP.NET Core aplikacji i zarządzanie nimi przy użyciu konfiguracji aplikacji platformy Azure
author: lisaguthrie
ms.service: azure-app-configuration
ms.custom: devx-track-csharp
ms.topic: quickstart
ms.date: 01/14/2020
ms.author: lcozzens
ms.openlocfilehash: c59bf14c03d9f7c0c835c5a19cf5ed92acea3501
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/14/2020
ms.locfileid: "88214212"
---
# <a name="quickstart-add-feature-flags-to-an-aspnet-core-app"></a>Szybki Start: Dodawanie flag funkcji do aplikacji ASP.NET Core

W tym przewodniku szybki start utworzysz kompleksową implementację zarządzania funkcjami w aplikacji ASP.NET Core przy użyciu usługi Azure App Configuration. Za pomocą usługi konfiguracji aplikacji można centralnie przechowywać wszystkie flagi funkcji i kontrolować ich Stany. 

Biblioteki zarządzania funkcjami platformy .NET Core poszerzają platformę Dzięki kompleksowemu obsłudze flagi funkcji. Te biblioteki są zbudowane na podstawie systemu konfiguracji platformy .NET Core. Zapewniają one bezproblemowe integrację z konfiguracją aplikacji za pomocą dostawcy konfiguracji platformy .NET Core.

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/)
- [Zestaw .NET Core SDK](https://dotnet.microsoft.com/download).

## <a name="create-an-app-configuration-store"></a>Tworzenie magazynu konfiguracji aplikacji

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Wybierz pozycję **Menedżer funkcji**  >  **+ Dodaj** , aby dodać flagę funkcji o nazwie `Beta` .

    > [!div class="mx-imgBorder"]
    > ![Włącz flagę funkcji o nazwie beta](media/add-beta-feature-flag.png)

    `label`Dla tej pory pozostaw niezdefiniowane. Wybierz pozycję **Zastosuj** , aby zapisać nową flagę funkcji.

## <a name="create-an-aspnet-core-web-app"></a>Tworzenie aplikacji internetowej ASP.NET Core

Użyj [interfejsu wiersza polecenia platformy .NET Core (CLI)](https://docs.microsoft.com/dotnet/core/tools/) , aby utworzyć nowy projekt aplikacji sieci Web ASP.NET Core MVC. Zaletą korzystania z interfejs wiersza polecenia platformy .NET Core zamiast programu Visual Studio jest to, że interfejs wiersza polecenia platformy .NET Core jest dostępny na platformach Windows, macOS i Linux.

1. Utwórz nowy folder dla projektu. W tym przewodniku szybki start nadaj mu nazwę *TestFeatureFlags*.

1. W nowym folderze Uruchom następujące polecenie, aby utworzyć nowy projekt aplikacji sieci Web ASP.NET Core MVC:

   ```    
   dotnet new mvc --no-https
   ```

## <a name="add-secret-manager"></a>Dodawanie narzędzia Secret Manager

Aby użyć Menedżera wpisów tajnych, Dodaj `UserSecretsId` element do pliku *. csproj* .

1. Otwórz plik *. csproj* .

1.  Dodaj `UserSecretsId` element, jak pokazano tutaj. Możesz użyć tego samego identyfikatora GUID lub można zastąpić tę wartość własną.

    > [!IMPORTANT]
    > `CreateHostBuilder` zastępuje `CreateWebHostBuilder` w programie .NET Core 3,0.  Wybierz poprawną składnię opartą na Twoim środowisku.

    #### <a name="net-core-2x"></a>[.NET Core 2. x](#tab/core2x)

    ```xml
    <Project Sdk="Microsoft.NET.Sdk.Web">

        <PropertyGroup>
            <TargetFramework>netcoreapp2.1</TargetFramework>
            <UserSecretsId>79a3edd0-2092-40a2-a04d-dcb46d5ca9ed</UserSecretsId>
        </PropertyGroup>

        <ItemGroup>
            <PackageReference Include="Microsoft.AspNetCore.App" />
            <PackageReference Include="Microsoft.AspNetCore.Razor.Design" Version="2.1.2" PrivateAssets="All" />
        </ItemGroup>

    </Project>
    ```

    #### <a name="net-core-3x"></a>[.NET Core 3. x](#tab/core3x)

    ```xml
    <Project Sdk="Microsoft.NET.Sdk.Web">

        <PropertyGroup>
            <TargetFramework>netcoreapp3.1</TargetFramework>
            <UserSecretsId>79a3edd0-2092-40a2-a04d-dcb46d5ca9ed</UserSecretsId>
        </PropertyGroup>

    </Project>
    ```
    ---

1. Zapisz plik *. csproj* .

Narzędzie Secret manager przechowuje poufne dane potrzebne w pracy deweloperskiej poza Twoim drzewem projektu. Takie podejście zapobiega przypadkowemu ujawnieniu wpisów tajnych aplikacji w kodzie źródłowym.

> [!TIP]
> Aby dowiedzieć się więcej na temat programu Secret Manager, zobacz [bezpieczne przechowywanie wpisów tajnych aplikacji w programie Development w ASP.NET Core](https://docs.microsoft.com/aspnet/core/security/app-secrets).

## <a name="connect-to-an-app-configuration-store"></a>Nawiązywanie połączenia z magazynem konfiguracji aplikacji

1. Dodaj odwołanie do `Microsoft.Azure.AppConfiguration.AspNetCore` i `Microsoft.FeatureManagement.AspNetCore` pakiety NuGet, uruchamiając następujące polecenia:

    ```dotnetcli
    dotnet add package Microsoft.Azure.AppConfiguration.AspNetCore
    dotnet add package Microsoft.FeatureManagement.AspNetCore
    ```

1. Uruchom następujące polecenie, aby przywrócić pakiety dla projektu:

    ```dotnetcli
    dotnet restore
    ```

1. Dodaj wpis tajny o nazwie **ConnectionStrings:AppConfig** do narzędzia Secret Manager.

    Ten wpis tajny zawiera parametry połączenia w celu uzyskania dostępu do magazynu konfiguracji aplikacji. Zastąp `<your_connection_string>` wartość w poniższym poleceniu parametrami połączenia dla magazynu konfiguracji aplikacji. Parametry połączenia w obszarze **klucze dostępu** można znaleźć w Azure Portal.

    To polecenie należy wykonać w tym samym katalogu, w którym znajduje się plik *csproj*.

    ```dotnetcli
    dotnet user-secrets set ConnectionStrings:AppConfig <your_connection_string>
    ```

    Do lokalnego testowania aplikacji sieci Web służy program Secret Manager. Po wdrożeniu aplikacji do [Azure App Service](https://azure.microsoft.com/services/app-service)można na przykład użyć ustawienia aplikacji o nazwie **parametry połączenia** w App Service zamiast używać Menedżera wpisów tajnych do przechowywania parametrów połączenia.

    Możesz uzyskać dostęp do tego wpisu tajnego za pomocą interfejsu API konfiguracji aplikacji. Dwukropek (:) działa w nazwie konfiguracji z interfejsem API konfiguracji aplikacji na wszystkich obsługiwanych platformach. Zobacz [Konfiguracja według środowiska](https://docs.microsoft.com/aspnet/core/fundamentals/configuration).

1. W programie *program.cs*zaktualizuj `CreateWebHostBuilder` metodę, aby użyć konfiguracji aplikacji przez wywołanie `config.AddAzureAppConfiguration()` metody.

    > [!IMPORTANT]
    > `CreateHostBuilder` zastępuje `CreateWebHostBuilder` w programie .NET Core 3,0.  Wybierz poprawną składnię opartą na Twoim środowisku.

    #### <a name="net-core-2x"></a>[.NET Core 2. x](#tab/core2x)

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();
                config.AddAzureAppConfiguration(options => {
                    options.Connect(settings["ConnectionStrings:AppConfig"])
                        .UseFeatureFlags();
                });
            })
            .UseStartup<Startup>();
    ```

    #### <a name="net-core-3x"></a>[.NET Core 3. x](#tab/core3x)

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        webBuilder.ConfigureAppConfiguration((hostingContext, config) =>
        {
            var settings = config.Build();
            config.AddAzureAppConfiguration(options => {
                options.Connect(settings["ConnectionStrings:AppConfig"])
                    .UseFeatureFlags();
            });
        })
        .UseStartup<Startup>());
    ```
    ---

1. Otwórz *Startup.cs*i Dodaj odwołania do programu .NET Core Feature Manager:

    ```csharp
    using Microsoft.FeatureManagement;
    ```

1. Zaktualizuj `ConfigureServices` metodę, aby dodać obsługę flagi funkcji przez wywołanie `services.AddFeatureManagement()` metody. Opcjonalnie możesz dołączyć dowolny filtr, który ma być używany z flagami funkcji przez wywołanie `services.AddFeatureFilter<FilterType>()` :

    #### <a name="net-core-2x"></a>[.NET Core 2. x](#tab/core2x)
    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_2_2);        
        services.AddFeatureManagement();
    }
    ```
    #### <a name="net-core-3x"></a>[.NET Core 3. x](#tab/core3x)
    ```csharp    
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddControllersWithViews();
        services.AddFeatureManagement();
    }

    ---

1. Update the `Configure` method to add a middleware to allow the feature flag values to be refreshed at a recurring interval while the ASP.NET Core web app continues to receive requests.

    #### [.NET Core 2.x](#tab/core2x)
    ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
            if (env.IsDevelopment())
            {
                app.UseDeveloperExceptionPage();
            }
            else
            {
                app.UseExceptionHandler("/Home/Error");
            }

            app.UseStaticFiles();
            app.UseCookiePolicy();
            app.UseAzureAppConfiguration();
            app.UseMvc(routes =>
            {
                routes.MapRoute(
                    name: "default",
                    template: "{controller=Home}/{action=Index}/{id?}");
            });
    }
    ```
    #### <a name="net-core-3x"></a>[.NET Core 3. x](#tab/core3x)
    ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
            if (env.IsDevelopment())
            {
                app.UseDeveloperExceptionPage();
            }
            else
            {
                app.UseExceptionHandler("/Home/Error");
            }
            app.UseStaticFiles();
            app.UseRouting();
            app.UseAuthorization();
            app.UseEndpoints(endpoints =>
            {
                endpoints.MapControllerRoute(
                    name: "default",
                    pattern: "{controller=Home}/{action=Index}/{id?}");
            });
            app.UseAzureAppConfiguration();
    }
    ```
    ---

1. Dodaj plik *MyFeatureFlags.cs* :

    ```csharp
    namespace TestFeatureFlags
    {
        public enum MyFeatureFlags
        {
            Beta
        }
    }
    ```

1. Dodaj *BetaController.cs* do katalogu *controllers* :

    ```csharp
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.FeatureManagement;
    using Microsoft.FeatureManagement.Mvc;

    namespace TestFeatureFlags.Controllers
    {
        public class BetaController: Controller
        {
            private readonly IFeatureManager _featureManager;

            public BetaController(IFeatureManagerSnapshot featureManager)
            {
                _featureManager = featureManager;
            }

            [FeatureGate(MyFeatureFlags.Beta)]
            public IActionResult Index()
            {
                return View();
            }
        }
    }
    ```

1. Otwórz *_ViewImports. cshtml* w katalogu *widoków* i Dodaj pomocnika tagów programu Feature Manager:

    ```html
    @addTagHelper *, Microsoft.FeatureManagement.AspNetCore
    ```

1. Otwórz *_Layout. cshtml* w *Views* \\ *udostępnionym* katalogu widoki i Zastąp `<nav>` kod kreskowy poniżej `<body>`  >  `<header>` następującym kodem:

    ```html
    <nav class="navbar navbar-expand-sm navbar-toggleable-sm navbar-light bg-white border-bottom box-shadow mb-3">
        <div class="container">
            <a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">TestFeatureFlags</a>
            <button class="navbar-toggler" type="button" data-toggle="collapse" data-target=".navbar-collapse" aria-controls="navbarSupportedContent"
            aria-expanded="false" aria-label="Toggle navigation">
            <span class="navbar-toggler-icon"></span>
            </button>
            <div class="navbar-collapse collapse d-sm-inline-flex flex-sm-row-reverse">
                <ul class="navbar-nav flex-grow-1">
                    <li class="nav-item">
                        <a class="nav-link text-dark" asp-area="" asp-controller="Home" asp-action="Index">Home</a>
                    </li>
                    <feature name="Beta">
                    <li class="nav-item">
                        <a class="nav-link text-dark" asp-area="" asp-controller="Beta" asp-action="Index">Beta</a>
                    </li>
                    </feature>
                    <li class="nav-item">
                        <a class="nav-link text-dark" asp-area="" asp-controller="Home" asp-action="Privacy">Privacy</a>
                    </li>
                </ul>
            </div>
        </div>
    </nav>
    ```

1. Utwórz katalog *beta* w obszarze *widoki* i Dodaj do niego plik *index. cshtml* :

    ```html
    @{
        ViewData["Title"] = "Beta Home Page";
    }

    <h1>
        This is the beta website.
    </h1>
    ```

## <a name="build-and-run-the-app-locally"></a>Lokalne kompilowanie i uruchamianie aplikacji

1. Aby skompilować aplikację przy użyciu interfejs wiersza polecenia platformy .NET Core, uruchom następujące polecenie w powłoce poleceń:

    ```
    dotnet build
    ```

1. Po pomyślnym zakończeniu kompilacji Uruchom następujące polecenie, aby uruchomić aplikację sieci Web lokalnie:

    ```
    dotnet run
    ```

1. Otwórz okno przeglądarki i przejdź do `https://localhost:5000` , który jest domyślnym adresem URL aplikacji sieci Web hostowanej lokalnie.
    Jeśli pracujesz w Azure Cloud Shell, wybierz przycisk *Podgląd sieci Web* , a następnie pozycję *Konfiguruj*.  Po wyświetleniu monitu wybierz pozycję port 5000.

    ![Znajdź przycisk Podgląd sieci Web](./media/quickstarts/cloud-shell-web-preview.png)

    W przeglądarce powinna zostać wyświetlona strona podobna do poniższej ilustracji.
    ![Lokalne uruchamianie aplikacji z przewodnika Szybki start](./media/quickstarts/aspnet-core-feature-flag-local-before.png)

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Wybierz pozycję **wszystkie zasoby**, a następnie wybierz wystąpienie magazynu konfiguracji aplikacji utworzone w ramach przewodnika Szybki Start.

1. Wybierz pozycję **Menedżer funkcji**, a następnie Zmień stan klucza **beta** na **włączone**.

1. Wróć do wiersza polecenia i Anuluj uruchomiony `dotnet` proces przez naciśnięcie klawisza `Ctrl-C` .  Uruchom ponownie aplikację przy użyciu programu `dotnet run` .

1. Odśwież stronę przeglądarki, aby zobaczyć nowe ustawienia konfiguracji.

    ![Lokalne uruchamianie aplikacji z przewodnika Szybki start](./media/quickstarts/aspnet-core-feature-flag-local-after.png)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start utworzono nowy magazyn konfiguracji aplikacji, który będzie używany do zarządzania funkcjami w ASP.NET Core aplikacji sieci Web za pośrednictwem [bibliotek zarządzania](https://go.microsoft.com/fwlink/?linkid=2074664)funkcjami.

- Dowiedz się więcej o [zarządzaniu funkcjami](./concept-feature-management.md).
- [Zarządzaj flagami funkcji](./manage-feature-flags.md).
- [Użyj flag funkcji w aplikacji ASP.NET Core](./use-feature-flags-dotnet-core.md).
- [Używanie dynamicznej konfiguracji w aplikacji platformy ASP.NET Core](./enable-dynamic-configuration-aspnet-core.md)
