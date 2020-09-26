---
title: Samouczek dotyczący używania flag funkcji w aplikacji .NET Core | Microsoft Docs
description: W tym samouczku dowiesz się, jak zaimplementować flagi funkcji w aplikacjach .NET Core.
services: azure-app-configuration
documentationcenter: ''
author: lisaguthrie
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 09/17/2020
ms.author: lcozzens
ms.custom: devx-track-csharp, mvc
ms.openlocfilehash: f863ca855ca36603085ed96b5aa17d277ae00516
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91317314"
---
# <a name="tutorial-use-feature-flags-in-an-aspnet-core-app"></a>Samouczek: używanie flag funkcji w aplikacji ASP.NET Core

Biblioteki zarządzania funkcjami platformy .NET Core zapewniają obsługę idiomatyczne dla implementacji flag funkcji w aplikacji .NET lub ASP.NET Core. Te biblioteki umożliwiają deklaratywne Dodawanie flag funkcji do kodu, dzięki czemu nie trzeba ręcznie pisać wszystkich `if` instrukcji dla nich.

Biblioteki zarządzania funkcjami również zarządzają cyklami życia flagi funkcji w tle. Na przykład biblioteki odświeżają i buforują Stany flag i gwarantują, że stan flagi będzie niezmienny podczas wywołania żądania. Ponadto biblioteka ASP.NET Core oferuje wbudowaną integrację, w tym akcje kontrolera MVC, widoki, trasy i oprogramowanie pośredniczące.

[Dodawanie flag funkcji do aplikacji ASP.NET Core szybkiego startu](./quickstart-feature-flag-aspnet-core.md) pokazuje kilka sposobów dodawania flag funkcji w aplikacji ASP.NET Core. Ten samouczek wyjaśnia te metody bardziej szczegółowo. Aby uzyskać pełne informacje, zobacz [dokumentację dotyczącą zarządzania funkcjami ASP.NET Core](https://go.microsoft.com/fwlink/?linkid=2091410).

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Dodaj flagi funkcji w kluczowych częściach aplikacji, aby kontrolować dostępność funkcji.
> * Integruj z konfiguracją aplikacji, gdy jest używana do zarządzania flagami funkcji.

## <a name="set-up-feature-management"></a>Konfigurowanie zarządzania funkcjami

Dodaj odwołanie do `Microsoft.FeatureManagement.AspNetCore` `Microsoft.FeatureManagement` pakietów NuGet i, aby użyć programu .NET Core Feature Manager.
    
Program .NET Core Feature Manager `IFeatureManager` Pobiera flagi funkcji z macierzystego systemu konfiguracji platformy. W związku z tym można zdefiniować flagi funkcji aplikacji przy użyciu dowolnego źródła konfiguracji obsługiwanego przez platformę .NET Core, w tym *appsettings.jslokalnego dla* zmiennych plików lub środowiskowych. `IFeatureManager` opiera się na iniekcji zależności .NET Core. Usługi zarządzania funkcjami można zarejestrować przy użyciu standardowych konwencji:

```csharp
using Microsoft.FeatureManagement;

public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddFeatureManagement();
    }
}
```

Domyślnie program Feature Manager pobiera flagi funkcji z `"FeatureManagement"` sekcji danych konfiguracyjnych platformy .NET Core. W poniższym przykładzie nakazuje menedżerowi funkcji odczytywanie z innej sekcji o nazwie `"MyFeatureFlags"` :

```csharp
using Microsoft.FeatureManagement;

public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddFeatureManagement(options =>
        {
                options.UseConfiguration(Configuration.GetSection("MyFeatureFlags"));
        });
    }
}
```

Jeśli używasz filtrów w flagach funkcji, musisz dołączyć dodatkową bibliotekę i zarejestrować ją. Poniższy przykład pokazuje, jak używać wbudowanego filtru funkcji o nazwie `PercentageFilter` :

```csharp
using Microsoft.FeatureManagement;
using Microsoft.FeatureManagement.FeatureFilters;

public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddFeatureManagement()
                .AddFeatureFilter<PercentageFilter>();
    }
}
```

Firma Microsoft zaleca, aby zachować flagi funkcji poza aplikacją i osobno zarządzać nimi. Dzięki temu można w dowolnym momencie zmodyfikować Stany flag i wprowadzić zmiany w aplikacji od razu. Konfiguracja aplikacji zapewnia scentralizowane miejsce do organizowania i kontrolowania wszystkich flag funkcji za pomocą dedykowanego interfejsu użytkownika portalu. Konfiguracja aplikacji udostępnia również flagi bezpośrednio do aplikacji za pomocą bibliotek klienckich platformy .NET Core.

Najprostszym sposobem łączenia aplikacji ASP.NET Core z konfiguracją aplikacji jest użycie dostawcy konfiguracji `Microsoft.Azure.AppConfiguration.AspNetCore` . Wykonaj następujące kroki, aby użyć tego pakietu NuGet.

1. Otwórz plik *program.cs* i Dodaj następujący kod.

   ```csharp
   using Microsoft.Extensions.Configuration.AzureAppConfiguration;

   public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
       WebHost.CreateDefaultBuilder(args)
              .ConfigureAppConfiguration((hostingContext, config) => {
                  var settings = config.Build();
                  config.AddAzureAppConfiguration(options => {
                      options.Connect(settings["ConnectionStrings:AppConfig"])
                             .UseFeatureFlags();
                   });
              })
              .UseStartup<Startup>();
   ```

2. Otwórz *Startup.cs* i zaktualizuj `Configure` metodę, aby dodać oprogramowanie pośredniczące, aby umożliwić odświeżanie wartości flagi funkcji w cyklicznym interwale, podczas gdy aplikacja sieci Web ASP.NET Core nadal otrzymuje żądania.

   ```csharp
   public void Configure(IApplicationBuilder app, IHostingEnvironment env)
   {
       app.UseAzureAppConfiguration();
       app.UseMvc();
   }
   ```

Oczekiwane wartości flagi funkcji są zmieniane z upływem czasu. Domyślnie wartości flagi funkcji są buforowane przez okres 30 sekund, więc operacja odświeżania wyzwalana, gdy oprogramowanie pośredniczące odbiera żądanie nie aktualizuje wartości do momentu wygaśnięcia wartości w pamięci podręcznej. Poniższy kod przedstawia sposób zmiany czasu wygaśnięcia pamięci podręcznej lub interwału sondowania na 5 minut w `options.UseFeatureFlags()` wywołaniu.

```csharp
config.AddAzureAppConfiguration(options => {
    options.Connect(settings["ConnectionStrings:AppConfig"])
           .UseFeatureFlags(featureFlagOptions => {
                featureFlagOptions.CacheExpirationTime = TimeSpan.FromMinutes(5);
           });
});
```

## <a name="feature-flag-declaration"></a>Deklaracja flagi funkcji

Każda flaga funkcji ma dwie części: nazwę i listę co najmniej jednego filtru, który jest używany do obliczenia, czy stan funkcji jest *włączony* (to znaczy, gdy wartość jest równa `True` ). Filtr definiuje przypadek użycia, gdy funkcja powinna być włączona.

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
* `FeatureC` określa filtr o nazwie `Percentage` z `Parameters` właściwością. `Percentage` jest konfigurowalnym filtrem. W tym przykładzie `Percentage` określa 50-procentowe prawdopodobieństwo, że `FeatureC` flaga będzie *włączona*.

## <a name="feature-flag-references"></a>Odwołania do flag funkcji

Aby można było łatwo odwoływać się do flag funkcji w kodzie, należy zdefiniować je jako `enum` zmienne:

```csharp
public enum MyFeatureFlags
{
    FeatureA,
    FeatureB,
    FeatureC
}
```

## <a name="feature-flag-checks"></a>Sprawdzanie flag funkcji

Podstawowym wzorcem zarządzania funkcjami jest najpierw sprawdzenie, czy flaga funkcji jest ustawiona na wartość *włączone*. Jeśli tak, Menedżer funkcji uruchamia następnie akcje, które zawiera funkcja. Na przykład:

```csharp
IFeatureManager featureManager;
...
if (await featureManager.IsEnabledAsync(nameof(MyFeatureFlags.FeatureA)))
{
    // Run the following code
}
```

## <a name="dependency-injection"></a>Wstrzykiwanie zależności

W ASP.NET Core MVC można uzyskać dostęp do Menedżera funkcji `IFeatureManager` za pomocą iniekcji zależności:

```csharp
public class HomeController : Controller
{
    private readonly IFeatureManager _featureManager;

    public HomeController(IFeatureManager featureManager)
    {
        _featureManager = featureManager;
    }
}
```

## <a name="controller-actions"></a>Akcje kontrolera

W kontrolerach MVC Użyj atrybutu, `FeatureGate` Aby określić, czy jest włączona cała klasa kontrolera lub określona akcja. Poniższy `HomeController` kontroler musi `FeatureA` być *włączony* , aby można było wykonać dowolną akcję, która zawiera klasy kontrolera:

```csharp
using Microsoft.FeatureManagement.Mvc;

[FeatureGate(MyFeatureFlags.FeatureA)]
public class HomeController : Controller
{
    ...
}
```

`Index` `FeatureA` Aby można było uruchomić następujące czynności *on* :

```csharp
using Microsoft.FeatureManagement.Mvc;

[FeatureGate(MyFeatureFlags.FeatureA)]
public IActionResult Index()
{
    return View();
}
```

Gdy kontroler MVC lub akcja jest blokowana, ponieważ flaga funkcji kontrolującej jest *wyłączona*, `IDisabledFeaturesHandler` zostanie wywołany zarejestrowany interfejs. Domyślny `IDisabledFeaturesHandler` interfejs zwraca kod stanu 404 do klienta bez treści odpowiedzi.

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

Filtry MVC można skonfigurować tak, aby były aktywowane na podstawie stanu flagi funkcji. Poniższy kod dodaje filtr MVC o nazwie `SomeMvcFilter` . Ten filtr jest wyzwalany w ramach potoku MVC tylko wtedy, gdy `FeatureA` jest włączony. Ta funkcja jest ograniczona do `IAsyncActionFilter` . 

```csharp
using Microsoft.FeatureManagement.FeatureFilters;

IConfiguration Configuration { get; set;}

public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc(options => {
        options.Filters.AddForFeature<SomeMvcFilter>(nameof(MyFeatureFlags.FeatureA));
    });
}
```

## <a name="middleware"></a>Oprogramowanie pośredniczące

Możesz również użyć flag funkcji, aby warunkowo dodawać gałęzie aplikacji i oprogramowanie pośredniczące. Poniższy kod wstawia składnik pośredniczący w potoku żądania tylko wtedy, gdy `FeatureA` jest włączony:

```csharp
app.UseMiddlewareForFeature<ThirdPartyMiddleware>(nameof(MyFeatureFlags.FeatureA));
```

Ten kod kompiluje bardziej ogólną funkcję w celu rozgałęzienia całej aplikacji na podstawie flagi funkcji:

```csharp
app.UseForFeature(featureName, appBuilder => {
    appBuilder.UseMiddleware<T>();
});
```

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono sposób implementacji flag funkcji w aplikacji ASP.NET Core przy użyciu `Microsoft.FeatureManagement` bibliotek. Aby uzyskać więcej informacji na temat obsługi zarządzania funkcjami w ASP.NET Core i konfiguracji aplikacji, zobacz następujące zasoby:

* [Przykładowy kod flagi funkcji ASP.NET Core](/azure/azure-app-configuration/quickstart-feature-flag-aspnet-core)
* [Dokumentacja programu Microsoft. FeatureManagement](https://docs.microsoft.com/dotnet/api/microsoft.featuremanagement)
* [Zarządzanie flagami funkcji](./manage-feature-flags.md)
