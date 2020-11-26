---
title: Przewodnik Szybki Start dotyczący dodawania flag funkcji do ASP.NET Core
description: Dodawanie flag funkcji do ASP.NET Core aplikacji i zarządzanie nimi przy użyciu konfiguracji aplikacji platformy Azure
author: lisaguthrie
ms.service: azure-app-configuration
ms.custom: devx-track-csharp
ms.topic: quickstart
ms.date: 09/28/2020
ms.author: lcozzens
ms.openlocfilehash: 5179a619b1adba432910605c75fae0789efd3397
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/26/2020
ms.locfileid: "96182720"
---
# <a name="quickstart-add-feature-flags-to-an-aspnet-core-app"></a>Szybki Start: Dodawanie flag funkcji do aplikacji ASP.NET Core

W tym przewodniku szybki start utworzysz kompleksową implementację zarządzania funkcjami w aplikacji ASP.NET Core przy użyciu usługi Azure App Configuration. Użyjesz usługi konfiguracji aplikacji, aby centralnie przechowywać wszystkie flagi funkcji i kontrolować ich Stany. 

Biblioteki zarządzania funkcjami platformy .NET Core poszerzają platformę Dzięki kompleksowemu obsłudze flagi funkcji. Te biblioteki są zbudowane na podstawie systemu konfiguracji platformy .NET Core. Zapewniają one bezproblemowe integrację z konfiguracją aplikacji za pomocą dostawcy konfiguracji platformy .NET Core.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/dotnet)
* [Zestaw .NET Core SDK](https://dotnet.microsoft.com/download)

## <a name="create-an-app-configuration-store"></a>Tworzenie magazynu konfiguracji aplikacji

[!INCLUDE[Azure App Configuration resource creation steps](../../includes/azure-app-configuration-create.md)]

8. Wybierz pozycję **Operations**  >  **Manager**  >  **Dodaj** , aby dodać flagę funkcji o nazwie *beta*.

    > [!div class="mx-imgBorder"]
    > ![Włącz flagę funkcji o nazwie beta](media/add-beta-feature-flag.png)

    Pozostaw pustą **etykietę** dla tej pory. Wybierz pozycję **Zastosuj** , aby zapisać nową flagę funkcji.

## <a name="create-an-aspnet-core-web-app"></a>Tworzenie aplikacji internetowej ASP.NET Core

Użyj [interfejsu wiersza polecenia platformy .NET Core (CLI)](/dotnet/core/tools) , aby utworzyć nowy projekt ASP.NET Core MVC. Zaletą korzystania z interfejs wiersza polecenia platformy .NET Core zamiast programu Visual Studio jest to, że interfejs wiersza polecenia platformy .NET Core jest dostępny na platformach Windows, macOS i Linux.

Uruchom następujące polecenie, aby utworzyć projekt ASP.NET Core MVC w nowym folderze *TestFeatureFlags* :

```dotnetcli
dotnet new mvc --no-https --output TestFeatureFlags
```

[!INCLUDE[Add Secret Manager support to an ASP.NET Core project](../../includes/azure-app-configuration-add-secret-manager.md)]

## <a name="connect-to-an-app-configuration-store"></a>Nawiązywanie połączenia z magazynem konfiguracji aplikacji

1. Zainstaluj pakiety NuGet [Microsoft. Azure. AppConfiguration. AspNetCore](https://www.nuget.org/packages/Microsoft.Azure.AppConfiguration.AspNetCore) i [Microsoft. FeatureManagement. AspNetCore](https://www.nuget.org/packages/Microsoft.FeatureManagement.AspNetCore) , uruchamiając następujące polecenia:

    ```dotnetcli
    dotnet add package Microsoft.Azure.AppConfiguration.AspNetCore
    ```

    ```dotnetcli
    dotnet add package Microsoft.FeatureManagement.AspNetCore
    ```

1. Uruchom następujące polecenie w tym samym katalogu, w którym znajduje się plik *. csproj* . Polecenie używa Menedżera wpisów tajnych do przechowywania wpisu tajnego o nazwie `ConnectionStrings:AppConfig` , który przechowuje parametry połączenia dla magazynu konfiguracji aplikacji. Zastąp `<your_connection_string>` symbol zastępczy parametrami połączenia magazynu konfiguracji aplikacji. Parametry połączenia w obszarze **klucze dostępu** można znaleźć w Azure Portal.

    ```dotnetcli
    dotnet user-secrets set ConnectionStrings:AppConfig "<your_connection_string>"
    ```

    Menedżer wpisów tajnych służy tylko do lokalnego testowania aplikacji sieci Web. Po wdrożeniu aplikacji do [Azure App Service](https://azure.microsoft.com/services/app-service/web)należy użyć ustawienia aplikacji **parametry połączenia** w App Service zamiast tajnego Menedżera w celu przechowywania parametrów połączenia.

    Uzyskaj dostęp do tego wpisu tajnego za pomocą interfejsu API konfiguracji platformy .NET Core. Dwukropek ( `:` ) działa w nazwie konfiguracji z interfejsem API konfiguracji na wszystkich obsługiwanych platformach. Aby uzyskać więcej informacji, zobacz [klucze konfiguracji i wartości](/aspnet/core/fundamentals/configuration#configuration-keys-and-values).

1. W programie *program.cs* zaktualizuj `CreateWebHostBuilder` metodę, aby użyć konfiguracji aplikacji przez wywołanie `AddAzureAppConfiguration` metody.

    > [!IMPORTANT]
    > `CreateHostBuilder` zastępuje program `CreateWebHostBuilder` .NET Core 3. x. Wybierz poprawną składnię opartą na Twoim środowisku.

    #### <a name="net-core-3x"></a>[.NET Core 3. x](#tab/core3x)

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
                webBuilder.ConfigureAppConfiguration(config =>
                {
                    var settings = config.Build();
                    var connection = settings.GetConnectionString("AppConfig");
                    config.AddAzureAppConfiguration(options =>
                        options.Connect(connection).UseFeatureFlags());
                }).UseStartup<Startup>());
    ```

    #### <a name="net-core-2x"></a>[.NET Core 2. x](#tab/core2x)

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
               .ConfigureAppConfiguration(config =>
               {
                   var settings = config.Build();
                   var connection = settings.GetConnectionString("AppConfig");
                   config.AddAzureAppConfiguration(options =>
                       options.Connect(connection).UseFeatureFlags());
               }).UseStartup<Startup>();
    ```

    ---

    Po powyższej zmianie [dostawca konfiguracji dla konfiguracji aplikacji](/dotnet/api/Microsoft.Extensions.Configuration.AzureAppConfiguration) został zarejestrowany w interfejsie API konfiguracji platformy .NET Core.

1. W *Startup.cs*, Dodaj odwołanie do programu .NET Core Feature Manager:

    ```csharp
    using Microsoft.FeatureManagement;
    ```

1. Zaktualizuj `Startup.ConfigureServices` metodę, aby dodać obsługę flagi funkcji przez wywołanie `AddFeatureManagement` metody. Opcjonalnie możesz dołączyć dowolny filtr, który ma być używany z flagami funkcji przez wywołanie `AddFeatureFilter<FilterType>()` :

    #### <a name="net-core-3x"></a>[.NET Core 3. x](#tab/core3x)

    ```csharp    
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddControllersWithViews();
        services.AddFeatureManagement();
    }
    ```

    #### <a name="net-core-2x"></a>[.NET Core 2. x](#tab/core2x)

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddMvc()
            .SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
        services.AddFeatureManagement();
    }
    ```

    ---

1. Dodaj plik *MyFeatureFlags.cs* do głównego katalogu projektu przy użyciu następującego kodu:

    ```csharp
    namespace TestFeatureFlags
    {
        public enum MyFeatureFlags
        {
            Beta
        }
    }
    ```

1. Dodaj plik *BetaController.cs* do katalogu *controllers* przy użyciu następującego kodu:

    ```csharp
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.FeatureManagement;
    using Microsoft.FeatureManagement.Mvc;

    namespace TestFeatureFlags.Controllers
    {
        public class BetaController: Controller
        {
            private readonly IFeatureManager _featureManager;

            public BetaController(IFeatureManagerSnapshot featureManager) =>
                _featureManager = featureManager;

            [FeatureGate(MyFeatureFlags.Beta)]
            public IActionResult Index() => View();
        }
    }
    ```

1. W *widoku/_ViewImports. cshtml* Zarejestruj pomocnika tagów programu Feature Manager za pomocą `@addTagHelper` dyrektywy:

    ```cshtml
    @addTagHelper *, Microsoft.FeatureManagement.AspNetCore
    ```

    Poprzedni kod umożliwia `<feature>` pomocnikowi tagów używanie w plikach *. cshtml* projektu.

1. Otwórz *_Layout. cshtml* w *Views* \\ *udostępnionym* katalogu widoki. Znajdź `<nav>` kod kreskowy poniżej `<body>`  >  `<header>` . Wstaw nowy `<feature>` tag między elementami nawigacyjnymi w *domu* i *poufności informacji* , jak pokazano w wyróżnionych wierszach poniżej.

    :::code language="html" source="../../includes/azure-app-configuration-navbar.md" range="15-38" highlight="13-17":::

1. Utwórz katalog *widoki/beta* i plik *index. cshtml* zawierający następujące znaczniki:

    ```cshtml
    @{
        ViewData["Title"] = "Beta Home Page";
    }

    <h1>This is the beta website.</h1>
    ```

## <a name="build-and-run-the-app-locally"></a>Lokalne kompilowanie i uruchamianie aplikacji

1. Aby skompilować aplikację przy użyciu interfejs wiersza polecenia platformy .NET Core, uruchom następujące polecenie w powłoce poleceń:

    ```dotnetcli
    dotnet build
    ```

1. Po pomyślnym zakończeniu kompilacji Uruchom następujące polecenie, aby uruchomić aplikację sieci Web lokalnie:

    ```dotnetcli
    dotnet run
    ```

1. Otwórz okno przeglądarki i przejdź do `http://localhost:5000` , który jest domyślnym adresem URL aplikacji sieci Web hostowanej lokalnie. Jeśli pracujesz w Azure Cloud Shell, wybierz przycisk **Podgląd sieci Web** , a następnie pozycję **Konfiguruj**. Po wyświetleniu monitu wybierz pozycję port 5000.

    ![Znajdź przycisk Podgląd sieci Web](./media/quickstarts/cloud-shell-web-preview.png)

    W przeglądarce powinna zostać wyświetlona strona podobna do poniższej ilustracji.

    :::image type="content" source="media/quickstarts/aspnet-core-feature-flag-local-before.png" alt-text="Lokalna aplikacja szybkiego startu przed zmianą" border="true":::

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Wybierz pozycję **wszystkie zasoby**, a następnie wybierz wystąpienie magazynu konfiguracji aplikacji utworzone w ramach przewodnika Szybki Start.

1. Wybierz pozycję **Menedżer funkcji**. 

1. Włącz flagę *beta* , zaznaczając pole wyboru w obszarze **włączone**.

1. Wróć do powłoki poleceń. Anuluj uruchomiony `dotnet` proces, naciskając <kbd>klawisze CTRL + C</kbd>. Uruchom ponownie aplikację przy użyciu programu `dotnet run` .

1. Odśwież stronę przeglądarki, aby zobaczyć nowe ustawienia konfiguracji.

    :::image type="content" source="media/quickstarts/aspnet-core-feature-flag-local-after.png" alt-text="Lokalna aplikacja szybkiego startu po zmianie" border="true":::

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE[Azure App Configuration cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start utworzono nowy magazyn konfiguracji aplikacji, który będzie używany do zarządzania funkcjami w ASP.NET Core aplikacji sieci Web za pośrednictwem [bibliotek zarządzania](/dotnet/api/Microsoft.Extensions.Configuration.AzureAppConfiguration)funkcjami.

* Dowiedz się więcej o [zarządzaniu funkcjami](./concept-feature-management.md).
* [Zarządzaj flagami funkcji](./manage-feature-flags.md).
* [Użyj flag funkcji w aplikacji ASP.NET Core](./use-feature-flags-dotnet-core.md).
* [Używanie dynamicznej konfiguracji w aplikacji platformy ASP.NET Core](./enable-dynamic-configuration-aspnet-core.md)