---
title: 'Samouczek: korzystanie z konfiguracji dynamicznej konfiguracji aplikacji w ASP.NET Core'
titleSuffix: Azure App Configuration
description: Z tego samouczka dowiesz się, jak dynamicznie aktualizować dane konfiguracji dla aplikacji platformy ASP.NET Core
services: azure-app-configuration
documentationcenter: ''
author: AlexandraKemperMS
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 09/1/2020
ms.author: alkemper
ms.custom: devx-track-csharp, mvc
ms.openlocfilehash: 1fd495083f5f9be367dd0f125883b181e3bed27b
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/09/2020
ms.locfileid: "96930555"
---
# <a name="tutorial-use-dynamic-configuration-in-an-aspnet-core-app"></a>Samouczek: używanie konfiguracji dynamicznej w aplikacji ASP.NET Core

ASP.NET Core ma podłączany System konfiguracyjny, który może odczytywać dane konfiguracji z różnych źródeł. Może dynamicznie obsługiwać zmiany bez powodowania ponownego uruchomienia aplikacji. ASP.NET Core obsługuje powiązanie ustawień konfiguracji z silnie wpisanąmi klasami platformy .NET. Wprowadza je do kodu przy użyciu różnych `IOptions<T>` wzorców. Jeden z tych wzorców, w `IOptionsSnapshot<T>` odmierzeniu, automatycznie ładuje konfigurację aplikacji, gdy zmieniają się dane bazowe. Wzorzec `IOptionsSnapshot<T>` możesz wprowadzać do kontrolerów w aplikacji, aby uzyskiwać dostęp do najnowszej konfiguracji przechowywanej w usłudze Azure App Configuration.

Możesz również skonfigurować konfigurację aplikacji ASP.NET Coreą bibliotekę klienta, aby odświeżyć zestaw ustawień konfiguracji dynamicznie przy użyciu oprogramowania pośredniczącego. Ustawienia konfiguracji są aktualizowane z magazynem konfiguracji za każdym razem, gdy aplikacja sieci Web otrzymuje żądania.

Konfiguracja aplikacji automatycznie buforuje każde ustawienie, aby uniknąć zbyt wielu wywołań magazynu konfiguracji. Operacja odświeżania czeka, aż wygaśnie wartość ustawienia w celu zaktualizowania tego ustawienia, nawet w przypadku zmiany jego wartości w magazynie konfiguracji. Domyślny czas wygaśnięcia pamięci podręcznej wynosi 30 sekund. W razie potrzeby można zastąpić ten czas wygaśnięcia.

W tym samouczku pokazano, jak zaimplementować dynamiczne aktualizacje konfiguracji w swoim kodzie. Opiera się on na aplikacji internetowej wprowadzonej w przewodnikach Szybki start. Przed kontynuowaniem najpierw Zakończ [Tworzenie aplikacji ASP.NET Coreej z konfiguracją aplikacji](./quickstart-aspnet-core-app.md) .

Aby wykonać kroki opisane w tym samouczku, można użyć dowolnego edytora kodu. [Visual Studio Code](https://code.visualstudio.com/) jest doskonałym rozwiązaniem dostępnym na platformach Windows, MacOS i Linux.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Skonfiguruj swoją aplikację w celu zaktualizowania jej konfiguracji w odpowiedzi na zmiany w magazynie konfiguracji aplikacji.
> * Wstaw najnowszą konfigurację z kontrolerów aplikacji.

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać ten samouczek, zainstaluj [zestaw .NET Core SDK](https://dotnet.microsoft.com/download).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Przed kontynuowaniem najpierw Zakończ [Tworzenie aplikacji ASP.NET Coreej z konfiguracją aplikacji](./quickstart-aspnet-core-app.md) .

## <a name="add-a-sentinel-key"></a>Dodawanie klucza wskaźnikowego

*Klucz wskaźnikowy* jest specjalnym kluczem służącym do sygnalizowania, kiedy konfiguracja została zmieniona. Aplikacja monitoruje klucz wskaźnikowy pod kątem zmian. Po wykryciu zmiany należy odświeżyć wszystkie wartości konfiguracyjne. Takie podejście zmniejsza ogólną liczbę żądań dokonanych przez aplikację do konfiguracji aplikacji w porównaniu do monitorowania wszystkich kluczy pod kątem zmian.

1. W Azure Portal wybierz pozycję **Eksplorator konfiguracji > utwórz > klucz-wartość**.
1. Dla **klucza** wpisz *TestApp: Settings: wskaźnik*. Dla **wartości wprowadź wartość** 1. Pozostaw pustą **etykietę** i **Typ zawartości** .
1. Wybierz przycisk **Zastosuj**.

> [!NOTE]
> Jeśli nie używasz klucza wskaźnikowego, musisz ręcznie zarejestrować każdy klucz, który ma być monitorowany.

## <a name="reload-data-from-app-configuration"></a>Ponowne ładowanie danych z usługi App Configuration

1. Dodaj odwołanie do `Microsoft.Azure.AppConfiguration.AspNetCore` pakietu NuGet, uruchamiając następujące polecenie:

    ```dotnetcli
    dotnet add package Microsoft.Azure.AppConfiguration.AspNetCore
    ```

1. Otwórz *program.cs* i zaktualizuj metodę, `CreateWebHostBuilder` Aby dodać `config.AddAzureAppConfiguration()` metodę.

    #### <a name="net-core-2x"></a>[.NET Core 2. x](#tab/core2x)

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();

                config.AddAzureAppConfiguration(options =>
                {
                    options.Connect(settings["ConnectionStrings:AppConfig"])
                           .ConfigureRefresh(refresh =>
                                {
                                    refresh.Register("TestApp:Settings:Sentinel", refreshAll: true)
                                           .SetCacheExpiration(new TimeSpan(0, 5, 0));
                                });
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
                    config.AddAzureAppConfiguration(options =>
                    {
                        options.Connect(settings["ConnectionStrings:AppConfig"])
                               .ConfigureRefresh(refresh =>
                                    {
                                        refresh.Register("TestApp:Settings:Sentinel", refreshAll: true)
                                               .SetCacheExpiration(new TimeSpan(0, 5, 0));
                                    });
                    });
                })
            .UseStartup<Startup>());
    ```
    ---

    Ta `ConfigureRefresh` Metoda służy do określania ustawień służących do aktualizowania danych konfiguracji z magazynem konfiguracji aplikacji w przypadku wyzwolenia operacji odświeżania. `refreshAll`Parametr do `Register` metody wskazuje, że wszystkie wartości konfiguracyjne powinny być odświeżane w przypadku zmiany klucza wskaźnikowego.

    Ponadto `SetCacheExpiration` Metoda zastępuje domyślny czas wygaśnięcia pamięci podręcznej wynoszący 30 sekund, określając w zamian czas 5 minut. Zmniejsza to liczbę żądań wysyłanych do konfiguracji aplikacji.

    > [!NOTE]
    > W celach testowych można obniżyć czas wygaśnięcia pamięci podręcznej.

    Aby faktycznie wyzwolić operację odświeżania, należy skonfigurować oprogramowanie pośredniczące odświeżania dla aplikacji w celu odświeżenia danych konfiguracji, gdy wystąpi jakakolwiek zmiana. Zobaczysz, jak to zrobić w późniejszym kroku.

2. Dodaj plik *Settings.cs*, który definiuje i implementuje nową klasę `Settings`.

    ```csharp
    namespace TestAppConfig
    {
        public class Settings
        {
            public string BackgroundColor { get; set; }
            public long FontSize { get; set; }
            public string FontColor { get; set; }
            public string Message { get; set; }
        }
    }
    ```

3. Otwórz *Startup.cs* i Użyj `IServiceCollection.Configure<T>` metody, `ConfigureServices` Aby powiązać dane konfiguracji z `Settings` klasą.

    #### <a name="net-core-2x"></a>[.NET Core 2. x](#tab/core2x)

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.Configure<Settings>(Configuration.GetSection("TestApp:Settings"));
        services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
    }
    ```

    #### <a name="net-core-3x"></a>[.NET Core 3. x](#tab/core3x)

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.Configure<Settings>(Configuration.GetSection("TestApp:Settings"));
        services.AddControllersWithViews();
    }
    ```
    ---
    > [!Tip]
    > Aby dowiedzieć się więcej na temat wzorca opcji podczas odczytywania wartości konfiguracyjnych, zobacz [Opcje wzorców w ASP.NET Core](/aspnet/core/fundamentals/configuration/options?view=aspnetcore-3.1).

4. Zaktualizuj `Configure` metodę, dodając `UseAzureAppConfiguration` oprogramowanie pośredniczące, aby umożliwić aktualizowanie ustawień konfiguracji na potrzeby odświeżania, gdy aplikacja sieci Web ASP.NET Core nadal otrzymuje żądania.


    #### <a name="net-core-2x"></a>[.NET Core 2. x](#tab/core2x)

    ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        app.UseAzureAppConfiguration();

        services.Configure<CookiePolicyOptions>(options =>
        {
            options.CheckConsentNeeded = context => true;
            options.MinimumSameSitePolicy = SameSiteMode.None;
        });

        app.UseMvc();
    }
    ```

    #### <a name="net-core-3x"></a>[.NET Core 3. x](#tab/core3x)

    ```csharp
    public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
    {
            if (env.IsDevelopment())
            {
                app.UseDeveloperExceptionPage();
            }
            else
            {
                app.UseExceptionHandler("/Home/Error");
                // The default HSTS value is 30 days. You may want to change this for production scenarios, see https://aka.ms/aspnetcore-hsts.
                app.UseHsts();
            }

            // Add the following line:
            app.UseAzureAppConfiguration();

            app.UseHttpsRedirection();
            
            app.UseStaticFiles();

            app.UseRouting();

            app.UseAuthorization();

            app.UseEndpoints(endpoints =>
            {
                endpoints.MapControllerRoute(
                    name: "default",
                    pattern: "{controller=Home}/{action=Index}/{id?}");
            });
    }
    ```
    ---
    
    Oprogramowanie pośredniczące używa konfiguracji odświeżania określonej w `AddAzureAppConfiguration` metodzie w programie `Program.cs` , aby wyzwolić odświeżanie dla każdego żądania odebranego przez aplikację sieci Web ASP.NET Core. Dla każdego żądania jest wyzwalana operacja odświeżania, a Biblioteka klienta sprawdza, czy wartość pamięci podręcznej dla zarejestrowanego ustawienia konfiguracji wygasła. Jeśli jego ważność wygasła, jest odświeżana.

    > [!NOTE]
    > Aby upewnić się, że konfiguracja jest odświeżana, należy dodać oprogramowanie pośredniczące tak wcześnie, jak jest to odpowiednie dla potoku żądania, aby nie było to krótkie obwody przez inne oprogramowanie pośredniczące w aplikacji.

## <a name="use-the-latest-configuration-data"></a>Używanie najnowszych danych konfiguracji

1. Otwórz *HomeController.cs* w katalogu controllers i Dodaj odwołanie do `Microsoft.Extensions.Options` pakietu.

    ```csharp
    using Microsoft.Extensions.Options;
    ```

2. Zaktualizuj `HomeController` klasę, aby otrzymywać dane `Settings` przy użyciu iniekcji zależności, i użyj jej wartości.

    #### <a name="net-core-2x"></a>[.NET Core 2. x](#tab/core2x)

    ```csharp
    public class HomeController : Controller
    {
        private readonly Settings _settings;
        public HomeController(IOptionsSnapshot<Settings> settings)
        {
            _settings = settings.Value;
        }

        public IActionResult Index()
        {
            ViewData["BackgroundColor"] = _settings.BackgroundColor;
            ViewData["FontSize"] = _settings.FontSize;
            ViewData["FontColor"] = _settings.FontColor;
            ViewData["Message"] = _settings.Message;

            return View();
        }
    }
    ```

    #### <a name="net-core-3x"></a>[.NET Core 3. x](#tab/core3x)

    ```csharp
    public class HomeController : Controller
    {
        private readonly Settings _settings;
        private readonly ILogger<HomeController> _logger;

        public HomeController(ILogger<HomeController> logger, IOptionsSnapshot<Settings> settings)
        {
            _logger = logger;
            _settings = settings.Value;
        }

        public IActionResult Index()
        {
            ViewData["BackgroundColor"] = _settings.BackgroundColor;
            ViewData["FontSize"] = _settings.FontSize;
            ViewData["FontColor"] = _settings.FontColor;
            ViewData["Message"] = _settings.Message;

            return View();
        }

        // ...
    }
    ```
    ---



3. Otwórz *index. cshtml* w widokach > katalog macierzysty i Zastąp jego zawartość następującym skryptem:

    ```html
    <!DOCTYPE html>
    <html lang="en">
    <style>
        body {
            background-color: @ViewData["BackgroundColor"]
        }
        h1 {
            color: @ViewData["FontColor"];
            font-size: @ViewData["FontSize"]px;
        }
    </style>
    <head>
        <title>Index View</title>
    </head>
    <body>
        <h1>@ViewData["Message"]</h1>
    </body>
    </html>
    ```

## <a name="build-and-run-the-app-locally"></a>Lokalne kompilowanie i uruchamianie aplikacji

1. Aby skompilować aplikację przy użyciu interfejs wiersza polecenia platformy .NET Core, uruchom następujące polecenie w powłoce poleceń:

    ```console
        dotnet build
    ```

1. Po pomyślnym zakończeniu kompilacji Uruchom następujące polecenie, aby uruchomić aplikację sieci Web lokalnie:

    ```console
        dotnet run
    ```

1. Otwórz okno przeglądarki i przejdź do adresu URL pokazanego w `dotnet run` danych wyjściowych.

    ![Uruchamianie aplikacji szybkiego startu lokalnie](./media/quickstarts/aspnet-core-app-launch-local-before.png)

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Wybierz pozycję **wszystkie zasoby**, a następnie wybierz wystąpienie magazynu konfiguracji aplikacji utworzone w ramach przewodnika Szybki Start.

1. Wybierz pozycję **Eksplorator konfiguracji** i zaktualizuj wartości następujących kluczy:

    | Klucz | Wartość |
    |---|---|
    | TestApp:Settings:BackgroundColor | green |
    | TestApp:Settings:FontColor | lightGray |
    | TestApp:Settings:Message | Dane z usługi Azure App Configuration — teraz z aktualizacjami na żywo! |
    | TestApp: Settings: wskaźnik kontrolny | 2 |

1. Odśwież stronę przeglądarki, aby zobaczyć nowe ustawienia konfiguracji. Aby zmiany zostały odzwierciedlone, może być konieczne odświeżenie więcej niż raz.

    ![Uruchamianie zaktualizowanej aplikacji szybkiego startu lokalnie](./media/quickstarts/aspnet-core-app-launch-local-after.png)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Następne kroki

W tym samouczku włączono aplikację sieci Web ASP.NET Core w celu dynamicznego odświeżania ustawień konfiguracji z poziomu konfiguracji aplikacji. Aby dowiedzieć się, jak za pomocą tożsamości zarządzanej przez platformę Azure usprawnić dostęp do konfiguracji aplikacji, przejdź do następnego samouczka.

> [!div class="nextstepaction"]
> [Integracja tożsamości zarządzanej](./howto-integrate-azure-managed-service-identity.md)
