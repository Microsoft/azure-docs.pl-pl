---
title: Samouczek dotyczący używania flag funkcji w aplikacji .NET Core | Microsoft Docs
description: W tym samouczku dowiesz się, jak zaimplementować flagi funkcji w aplikacjach .NET Core.
services: azure-app-configuration
documentationcenter: ''
author: AlexandraKemperMS
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 09/17/2020
ms.author: alkemper
ms.custom: devx-track-csharp, mvc
ms.openlocfilehash: 701fe4ffc6147086dde740bfdb2dc7db92508e28
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100380240"
---
# <a name="tutorial-use-feature-flags-in-an-aspnet-core-app"></a>Samouczek: używanie flag funkcji w aplikacji ASP.NET Core

Biblioteki zarządzania funkcjami platformy .NET Core zapewniają obsługę idiomatyczne dla implementacji flag funkcji w aplikacji .NET lub ASP.NET Core. Te biblioteki umożliwiają deklaratywne Dodawanie flag funkcji do kodu, dzięki czemu nie trzeba ręcznie pisać kodu w celu włączenia lub wyłączenia funkcji za pomocą `if` instrukcji.

Biblioteki zarządzania funkcjami również zarządzają cyklami życia flagi funkcji w tle. Na przykład biblioteki odświeżają i buforują Stany flag i gwarantują, że stan flagi będzie niezmienny podczas wywołania żądania. Ponadto biblioteka ASP.NET Core oferuje wbudowaną integrację, w tym akcje kontrolera MVC, widoki, trasy i oprogramowanie pośredniczące.

[Dodawanie flag funkcji do aplikacji ASP.NET Core szybkiego startu](./quickstart-feature-flag-aspnet-core.md) pokazuje prosty przykład używania flag funkcji w aplikacji ASP.NET Core. W tym samouczku przedstawiono dodatkowe opcje instalacji i możliwości bibliotek zarządzania funkcjami. Możesz użyć przykładowej aplikacji utworzonej w przewodniku Szybki Start, aby wypróbować przykładowy kod przedstawiony w tym samouczku. 

Aby uzyskać dokumentację referencyjną interfejsu API zarządzania funkcjami ASP.NET Core, zobacz [przestrzeń nazw Microsoft. FeatureManagement](/dotnet/api/microsoft.featuremanagement).

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Dodaj flagi funkcji w kluczowych częściach aplikacji, aby kontrolować dostępność funkcji.
> * Integruj z konfiguracją aplikacji, gdy jest używana do zarządzania flagami funkcji.

## <a name="set-up-feature-management"></a>Konfigurowanie zarządzania funkcjami

Aby można było uzyskać dostęp do programu .NET Core Feature Manager, aplikacja musi mieć odwołania do `Microsoft.FeatureManagement.AspNetCore` pakietu NuGet.

Program .NET Core Feature Manager jest skonfigurowany z macierzystego systemu konfiguracji platformy. W związku z tym można zdefiniować ustawienia flagi funkcji aplikacji przy użyciu dowolnego źródła konfiguracji obsługiwanego przez platformę .NET Core, w tym *appsettings.jslokalnego dla* zmiennych plików lub środowiskowych.

Domyślnie program Feature Manager Pobiera konfigurację flagi funkcji z `"FeatureManagement"` sekcji danych konfiguracyjnych platformy .NET Core. Aby użyć domyślnej lokalizacji konfiguracji, wywołaj metodę [AddFeatureManagement](/dotnet/api/microsoft.featuremanagement.servicecollectionextensions.addfeaturemanagement) **IServiceCollection** , która została przeniesiona do metody **ConfigureServices** klasy **startowej** .


```csharp
using Microsoft.FeatureManagement;

public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        ...
        services.AddFeatureManagement();
    }
}
```

Można określić, że konfiguracja zarządzania funkcjami ma być pobierana z innej sekcji konfiguracji przez wywołanie metody [Configuration. GetSection](/dotnet/api/microsoft.web.administration.configuration.getsection) i przekazanie jej nazwy do odpowiedniej sekcji. W poniższym przykładzie nakazuje menedżerowi funkcji odczytywanie z innej sekcji o nazwie `"MyFeatureFlags"` :

```csharp
using Microsoft.FeatureManagement;

public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        ...
        services.AddFeatureManagement(Configuration.GetSection("MyFeatureFlags"));
    }
}
```


Jeśli używasz filtrów w flagach funkcji, musisz uwzględnić przestrzeń nazw [Microsoft. FeatureManagement. FeatureFilters](/dotnet/api/microsoft.featuremanagement.featurefilters) i dodać wywołanie do [AddFeatureFilters](/dotnet/api/microsoft.featuremanagement.ifeaturemanagementbuilder.addfeaturefilter) , określając nazwę typu filtru, który ma być używany jako typ ogólny metody. Aby uzyskać więcej informacji na temat używania filtrów funkcji do dynamicznego włączania i wyłączania funkcji, zobacz [Włączanie etapowego wdrażania funkcji dla docelowych odbiorców](/azure/azure-app-configuration/howto-targetingfilter-aspnet-core).

Poniższy przykład pokazuje, jak używać wbudowanego filtru funkcji o nazwie `PercentageFilter` :



```csharp
using Microsoft.FeatureManagement;
using Microsoft.FeatureManagement.FeatureFilters;

public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        ...
        services.AddFeatureManagement()
                .AddFeatureFilter<PercentageFilter>();
    }
}
```

Zamiast tworzyć twarde flagi funkcji w aplikacji, zalecamy zachowanie flag funkcji poza aplikacją i zarządzanie nimi oddzielnie. Dzięki temu można w dowolnym momencie zmodyfikować Stany flag i wprowadzić zmiany w aplikacji od razu. Usługa Azure App Configuration oferuje dedykowany interfejs użytkownika portalu do zarządzania wszystkimi flagami funkcji. Usługa konfiguracji aplikacji platformy Azure dostarcza również flagi funkcji do aplikacji bezpośrednio za pomocą bibliotek klienckich platformy .NET Core.

Najprostszym sposobem łączenia aplikacji ASP.NET Core z konfiguracją aplikacji jest użycie dostawcy konfiguracji zawartego w `Microsoft.Azure.AppConfiguration.AspNetCore` pakiecie NuGet. Po dołączeniu odwołania do pakietu wykonaj następujące kroki, aby użyć tego pakietu NuGet.

1. Otwórz plik *program.cs* i Dodaj następujący kod.
    > [!IMPORTANT]
    > `CreateHostBuilder` zastępuje program `CreateWebHostBuilder` .NET Core 3. x. Wybierz poprawną składnię opartą na Twoim środowisku.

    ### <a name="net-5x"></a>[.NET 5. x](#tab/core5x)
    
    ```csharp
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
                webBuilder.ConfigureAppConfiguration(config =>
                {
                    var settings = config.Build();
                    config.AddAzureAppConfiguration(options =>
                        options.Connect(settings["ConnectionStrings:AppConfig"]).UseFeatureFlags());
                }).UseStartup<Startup>());
    ```

    ### <a name="net-core-3x"></a>[.NET Core 3. x](#tab/core3x)
    
    ```csharp
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;

    public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
            webBuilder.ConfigureAppConfiguration(config =>
            {
                var settings = config.Build();
                config.AddAzureAppConfiguration(options =>
                    options.Connect(settings["ConnectionStrings:AppConfig"]).UseFeatureFlags());
            }).UseStartup<Startup>());
    ```
        
    ### <a name="net-core-2x"></a>[.NET Core 2. x](#tab/core2x)
    
    ```csharp
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
            WebHost.CreateDefaultBuilder(args)
                    .ConfigureAppConfiguration(config =>
                    {
                        var settings = config.Build();
                        config.AddAzureAppConfiguration(options =>
                            options.Connect(settings["ConnectionStrings:AppConfig"]).UseFeatureFlags());
                    }).UseStartup<Startup>();
    ```
    ---

2. Otwórz *Startup.cs* i zaktualizuj `Configure` metodę i, `ConfigureServices` Aby dodać wbudowane oprogramowanie pośredniczące o nazwie `UseAzureAppConfiguration` . To oprogramowanie pośredniczące umożliwia odświeżanie wartości flag funkcji w cyklicznym interwale, podczas gdy aplikacja sieci Web ASP.NET Core nadal otrzymuje żądania.



    ```csharp
    public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
    {
        app.UseAzureAppConfiguration();
    }
    ```

   ```csharp
   public void ConfigureServices(IServiceCollection services)
   {
       services.AddAzureAppConfiguration();
   }
   ```
   
W typowym scenariuszu wartości flag funkcji należy aktualizować okresowo podczas wdrażania i włączania i różnych funkcji aplikacji. Domyślnie wartości flagi funkcji są buforowane przez okres 30 sekund, więc operacja odświeżania wyzwalana, gdy oprogramowanie pośredniczące odbiera żądanie nie aktualizuje wartości do momentu wygaśnięcia wartości w pamięci podręcznej. Poniższy kod przedstawia sposób zmiany czasu wygaśnięcia pamięci podręcznej lub interwału sondowania na 5 minut przez ustawienie [CacheExpirationInterval](/dotnet/api/microsoft.extensions.configuration.azureappconfiguration.featuremanagement.featureflagoptions.cacheexpirationinterval) w wywołaniu **UseFeatureFlags**.


    
```csharp
config.AddAzureAppConfiguration(options =>
    options.Connect(settings["ConnectionStrings:AppConfig"]).UseFeatureFlags(featureFlagOptions => {
        featureFlagOptions.CacheExpirationInterval = TimeSpan.FromMinutes(5);
    }));
});
```


## <a name="feature-flag-declaration"></a>Deklaracja flagi funkcji

Każda deklaracja flagi funkcji ma dwie części: nazwę i listę co najmniej jednego filtru, który jest używany do obliczenia, czy stan funkcji jest *włączony* (to znaczy, gdy wartość jest równa `True` ). Filtr definiuje kryterium, kiedy funkcja powinna być włączona.

Gdy flaga funkcji ma wiele filtrów, lista filtrów jest przesunięta w kolejności, aż jeden z filtrów określi, że funkcja powinna być włączona. W tym momencie flaga funkcji jest *włączona*, a wszystkie pozostałe wyniki filtru są pomijane. Jeśli żaden filtr nie wskazuje, że funkcja powinna być włączona, flaga funkcji jest *wyłączona*.

Program Feature Manager obsługuje *appsettings.js* jako źródło konfiguracji dla flag funkcji. Poniższy przykład pokazuje, jak skonfigurować flagi funkcji w pliku JSON:

```JSON
"FeatureManagement": {
    "FeatureA": true, // Feature flag set to on
    "FeatureB": false, // Feature flag set to off
    "FeatureC": {
        "EnabledFor": [
            {
                "Name": "Percentage",
                "Parameters": {
                    "Value": 50
                }
            }
        ]
    }
}
```

Zgodnie z Konwencją, `FeatureManagement` sekcja tego dokumentu JSON jest używana dla ustawień flagi funkcji. W poprzednim przykładzie pokazano trzy flagi funkcji z filtrami zdefiniowanymi we `EnabledFor` Właściwości:

* `FeatureA` jest *włączony*.
* `FeatureB` jest *wyłączona*.
* `FeatureC` określa filtr o nazwie `Percentage` z `Parameters` właściwością. `Percentage` jest konfigurowalnym filtrem. W tym przykładzie `Percentage` określa 50-procentowe prawdopodobieństwo, że `FeatureC` flaga będzie *włączona*. Aby zapoznać się z przewodnikiem dotyczącym korzystania z filtrów funkcji, zobacz [Używanie filtrów funkcji do włączania flag funkcji warunkowych](/azure/azure-app-configuration/howto-feature-filters-aspnet-core).




## <a name="use-dependency-injection-to-access-ifeaturemanager"></a>Użyj iniekcji zależności, aby uzyskać dostęp do IFeatureManager 

W przypadku niektórych operacji, takich jak ręczne sprawdzanie wartości flag funkcji, należy uzyskać wystąpienie [IFeatureManager](https://docs.microsoft.com/dotnet/api/microsoft.featuremanagement.ifeaturemanager?view=azure-dotnet-preview). W ASP.NET Core MVC można uzyskać dostęp do Menedżera funkcji `IFeatureManager` za pomocą iniekcji zależności. W poniższym przykładzie argument typu `IFeatureManager` jest dodawany do podpisu konstruktora dla kontrolera. Środowisko uruchomieniowe automatycznie rozpoznaje odwołanie i udostępnia interfejs podczas wywoływania konstruktora. Jeśli używasz szablonu aplikacji, w którym kontroler ma już jeden lub więcej argumentów iniekcji zależności w konstruktorze, takich jak, możesz `ILogger` po prostu dodać `IFeatureManager` jako dodatkowy argument:

### <a name="net-5x"></a>[.NET 5. x](#tab/core5x)
    
```csharp
using Microsoft.FeatureManagement;

public class HomeController : Controller
{
    private readonly IFeatureManager _featureManager;

    public HomeController(ILogger<HomeController> logger, IFeatureManager featureManager)
    {
        _featureManager = featureManager;
    }
}
```

### <a name="net-core-3x"></a>[.NET Core 3. x](#tab/core3x)

```csharp
using Microsoft.FeatureManagement;

public class HomeController : Controller
{
    private readonly IFeatureManager _featureManager;

    public HomeController(ILogger<HomeController> logger, IFeatureManager featureManager)
    {
        _featureManager = featureManager;
    }
}
```
    
### <a name="net-core-2x"></a>[.NET Core 2. x](#tab/core2x)

```csharp
using Microsoft.FeatureManagement;

public class HomeController : Controller
{
    private readonly IFeatureManager _featureManager;

    public HomeController(IFeatureManager featureManager)
    {
        _featureManager = featureManager;
    }
}
```

---

## <a name="feature-flag-references"></a>Odwołania do flag funkcji

Zdefiniuj flagi funkcji jako zmienne ciągów, aby odwoływać się do nich z kodu:

```csharp
public static class MyFeatureFlags
{
    public const string FeatureA = "FeatureA";
    public const string FeatureB = "FeatureB";
    public const string FeatureC = "FeatureC";
}
```

## <a name="feature-flag-checks"></a>Sprawdzanie flag funkcji

Typowym wzorcem zarządzania funkcjami jest sprawdzenie, czy flaga funkcji jest ustawiona na wartość *włączone* , a jeśli tak, należy uruchomić sekcję kodu. Na przykład:

```csharp
IFeatureManager featureManager;
...
if (await featureManager.IsEnabledAsync(MyFeatureFlags.FeatureA))
{
    // Run the following code
}
```

## <a name="controller-actions"></a>Akcje kontrolera

Za pomocą kontrolerów MVC można użyć atrybutu, `FeatureGate` Aby określić, czy jest włączona cała klasa kontrolera lub określona akcja. Poniższy `HomeController` kontroler musi `FeatureA` być *włączony* , aby można było wykonać dowolną akcję, która zawiera klasy kontrolera:

```csharp
using Microsoft.FeatureManagement.Mvc;

[FeatureGate(MyFeatureFlags.FeatureA)]
public class HomeController : Controller
{
    ...
}
```

`Index` `FeatureA` Aby można było uruchomić następujące czynności  :

```csharp
using Microsoft.FeatureManagement.Mvc;

[FeatureGate(MyFeatureFlags.FeatureA)]
public IActionResult Index()
{
    return View();
}
```

Gdy kontroler MVC lub akcja jest blokowana, ponieważ flaga funkcji kontrolującej jest *wyłączona*, zostanie wywołany zarejestrowany Interfejs [IDisabledFeaturesHandler](/dotnet/api/microsoft.featuremanagement.mvc.idisabledfeatureshandler?view=azure-dotnet-preview) . Domyślny `IDisabledFeaturesHandler` interfejs zwraca kod stanu 404 do klienta bez treści odpowiedzi.

## <a name="mvc-views"></a>Widoki MVC

Otwórz *_ViewImports. cshtml* w katalogu *widoków* i Dodaj pomocnika tagów programu Feature Manager:

```html
@addTagHelper *, Microsoft.FeatureManagement.AspNetCore
```

W widokach MVC można użyć znacznika, `<feature>` Aby renderować zawartość w zależności od tego, czy flaga funkcji jest włączona:

```html
<feature name="FeatureA">
    <p>This can only be seen if 'FeatureA' is enabled.</p>
</feature>
```

Aby wyświetlić alternatywną zawartość, gdy wymagania nie są spełnione, `negate` można użyć atrybutu.

```html
<feature name="FeatureA" negate="true">
    <p>This will be shown if 'FeatureA' is disabled.</p>
</feature>
```

Tag funkcji `<feature>` może również służyć do wyświetlania zawartości, jeśli jest włączona jakakolwiek lub wszystkie funkcje na liście.

```html
<feature name="FeatureA, FeatureB" requirement="All">
    <p>This can only be seen if 'FeatureA' and 'FeatureB' are enabled.</p>
</feature>
<feature name="FeatureA, FeatureB" requirement="Any">
    <p>This can be seen if 'FeatureA', 'FeatureB', or both are enabled.</p>
</feature>
```

## <a name="mvc-filters"></a>Filtry MVC

Filtry MVC można skonfigurować tak, aby były aktywowane na podstawie stanu flagi funkcji. Ta funkcja jest ograniczona do filtrów, które implementują [IAsyncActionFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncactionfilter). Poniższy kod dodaje filtr MVC o nazwie `ThirdPartyActionFilter` . Ten filtr jest wyzwalany w ramach potoku MVC tylko wtedy, gdy `FeatureA` jest włączony.  

```csharp
using Microsoft.FeatureManagement.FeatureFilters;

IConfiguration Configuration { get; set;}

public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc(options => {
        options.Filters.AddForFeature<ThirdPartyActionFilter>(MyFeatureFlags.FeatureA);
    });
}
```

## <a name="middleware"></a>Oprogramowanie pośredniczące

Możesz również użyć flag funkcji, aby warunkowo dodawać gałęzie aplikacji i oprogramowanie pośredniczące. Poniższy kod wstawia składnik pośredniczący w potoku żądania tylko wtedy, gdy `FeatureA` jest włączony:

```csharp
app.UseMiddlewareForFeature<ThirdPartyMiddleware>(MyFeatureFlags.FeatureA);
```

Ten kod kompiluje bardziej ogólną funkcję w celu rozgałęzienia całej aplikacji na podstawie flagi funkcji:

```csharp
app.UseForFeature(featureName, appBuilder => {
    appBuilder.UseMiddleware<T>();
});
```

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono sposób implementacji flag funkcji w aplikacji ASP.NET Core przy użyciu `Microsoft.FeatureManagement` bibliotek. Aby uzyskać więcej informacji na temat obsługi zarządzania funkcjami w ASP.NET Core i konfiguracji aplikacji, zobacz następujące zasoby:

* [Przykładowy kod flagi funkcji ASP.NET Core](./quickstart-feature-flag-aspnet-core.md)
* [Dokumentacja programu Microsoft. FeatureManagement](/dotnet/api/microsoft.featuremanagement)
* [Zarządzanie flagami funkcji](./manage-feature-flags.md)
