---
title: Use dependency injection in .NET Azure Functions (Korzystanie z wstrzykiwania zależności w usłudze Azure Functions na platformie .NET)
description: Dowiedz się, jak używać iniekcji zależności do rejestrowania i używania usług w usłudze .NET Functions
author: ggailey777
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 08/15/2020
ms.author: glenga
ms.reviewer: jehollan
ms.openlocfilehash: b2bf406dcab626b3ac08caf1a21ffea9332d3ca2
ms.sourcegitcommit: d79513b2589a62c52bddd9c7bd0b4d6498805dbe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/18/2020
ms.locfileid: "97672648"
---
# <a name="use-dependency-injection-in-net-azure-functions"></a>Use dependency injection in .NET Azure Functions (Korzystanie z wstrzykiwania zależności w usłudze Azure Functions na platformie .NET)

Azure Functions obsługuje wzorzec projektowania oprogramowania dla iniekcji zależności (DI), który jest techniką do osiągnięcia [niewersji kontroli (IOC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) między klasami i ich zależnościami.

- Iniekcja zależności w Azure Functions jest oparta na funkcjach wstrzykiwania zależności platformy .NET Core. Zaleca się zaznajomienie z [iniekcją zależności .NET Core](/aspnet/core/fundamentals/dependency-injection) . Istnieją różnice między zastępowaniem zależności i sposobem odczytywania wartości konfiguracji przy użyciu Azure Functions na planie zużycia.

- Obsługa iniekcji zależności rozpoczyna się od Azure Functions 2. x.

## <a name="prerequisites"></a>Wymagania wstępne

Aby można było użyć iniekcji zależności, należy zainstalować następujące pakiety NuGet:

- [Microsoft. Azure. Functions. Extensions](https://www.nuget.org/packages/Microsoft.Azure.Functions.Extensions/)

- Pakiet [Microsoft. NET. Sdk. Functions](https://www.nuget.org/packages/Microsoft.NET.Sdk.Functions/) w wersji 1.0.28 lub nowszej

- [Microsoft. rozszerzeń. DependencyInjection](https://www.nuget.org/packages/Microsoft.Extensions.DependencyInjection/) (obecnie jest obsługiwana tylko wersja 3. x i starsze)

## <a name="register-services"></a>Zarejestruj usługi

Aby zarejestrować usługi, należy utworzyć metodę konfigurowania i dodawania składników do `IFunctionsHostBuilder` wystąpienia.  Azure Functions host tworzy wystąpienie `IFunctionsHostBuilder` i przekazuje je bezpośrednio do metody.

Aby zarejestrować metodę, Dodaj `FunctionsStartup` atrybut zestawu, który określa nazwę typu używaną podczas uruchamiania.

```csharp
using Microsoft.Azure.Functions.Extensions.DependencyInjection;
using Microsoft.Extensions.DependencyInjection;

[assembly: FunctionsStartup(typeof(MyNamespace.Startup))]

namespace MyNamespace
{
    public class Startup : FunctionsStartup
    {
        public override void Configure(IFunctionsHostBuilder builder)
        {
            builder.Services.AddHttpClient();

            builder.Services.AddSingleton<IMyService>((s) => {
                return new MyService();
            });

            builder.Services.AddSingleton<ILoggerProvider, MyLoggerProvider>();
        }
    }
}
```

W tym przykładzie zastosowano pakiet [Microsoft. Extensions. http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) wymagany do zarejestrowania podczas `HttpClient` uruchamiania.

### <a name="caveats"></a>Zastrzeżenia

Seria kroków rejestracji jest uruchamiana przed i po przetworzeniu klasy startowej przez środowisko uruchomieniowe. W związku z tym należy pamiętać o następujących elementach:

- *Klasa startowa jest przeznaczona tylko do konfiguracji i rejestracji.* Unikaj korzystania z usług zarejestrowanych podczas uruchamiania podczas uruchamiania. Na przykład nie należy próbować rejestrować komunikatu w rejestratorze, który jest rejestrowany podczas uruchamiania. Ten punkt procesu rejestracji jest zbyt wczesny, aby Twoje usługi były dostępne do użycia. Po `Configure` uruchomieniu metody środowisko uruchomieniowe funkcji kontynuuje rejestrowanie dodatkowych zależności, co może wpłynąć na działanie usług.

- *Kontener iniekcji zależności zawiera tylko jawnie zarejestrowane typy*. Jedyne usługi dostępne jako typy z możliwością iniekcji to ustawienia konfiguracji w `Configure` metodzie. W efekcie typy specyficzne dla funkcji, takie jak `BindingContext` i, `ExecutionContext` nie są dostępne podczas instalacji lub jako typy wstrzykiwane.

## <a name="use-injected-dependencies"></a>Użyj wstrzykiwanych zależności

Iniekcja konstruktora służy do udostępniania zależności w funkcji. Użycie iniekcji konstruktora wymaga, aby nie używać klas statycznych dla wstrzykiwanych usług lub dla klas funkcji.

Poniższy przykład demonstruje, jak `IMyService` i `HttpClient` zależności są wstrzykiwane do funkcji wyzwalanej przez protokół http.

```csharp
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Extensions.Logging;
using System.Net.Http;
using System.Threading.Tasks;

namespace MyNamespace
{
    public class MyHttpTrigger
    {
        private readonly HttpClient _client;
        private readonly IMyService _service;

        public MyHttpTrigger(HttpClient httpClient, IMyService service)
        {
            this._client = httpClient;
            this._service = service;
        }

        [FunctionName("MyHttpTrigger")]
        public async Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Function, "get", "post", Route = null)] HttpRequest req,
            ILogger log)
        {
            var response = await _client.GetAsync("https://microsoft.com");
            var message = _service.GetMessage();

            return new OkObjectResult("Response from function with injected dependencies.");
        }
    }
}
```

W tym przykładzie zastosowano pakiet [Microsoft. Extensions. http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) wymagany do zarejestrowania podczas `HttpClient` uruchamiania.

## <a name="service-lifetimes"></a>Okresy istnienia usługi

Aplikacje Azure Functions zapewniają te same okresy istnienia usługi jak [iniekcja zależności ASP.NET](/aspnet/core/fundamentals/dependency-injection#service-lifetimes). W przypadku aplikacji funkcji różne okresy istnienia usługi działają w następujący sposób:

- **Przejściowe**: usługi przejściowe są tworzone podczas każdego rozpoznawania usługi.
- W **zakresie**: okres istnienia usługi w zakresie jest zgodny z okresem istnienia funkcji. Usługi o określonym zakresie są tworzone raz na wykonanie funkcji. Późniejsze żądania dla tej usługi podczas wykonywania ponownie użyją istniejącego wystąpienia usługi.
- **Pojedyncze**: okres istnienia usługi pojedynczej jest zgodny z okresem istnienia hosta i jest ponownie używany w ramach wykonywania funkcji w tym wystąpieniu. Pojedyncze usługi okresu istnienia są zalecane dla połączeń i klientów, na przykład `DocumentClient` lub dla `HttpClient` wystąpień.

Wyświetl lub Pobierz [przykład różnych okresów istnienia usługi](https://github.com/Azure/azure-functions-dotnet-extensions/tree/main/src/samples/DependencyInjection/Scopes) w serwisie GitHub.

## <a name="logging-services"></a>Usługi rejestrowania

Jeśli potrzebujesz własnego dostawcy rejestrowania, Zarejestruj niestandardowy typ jako wystąpienie [`ILoggerProvider`](/dotnet/api/microsoft.extensions.logging.iloggerfactory) , które jest dostępne za pomocą pakietu NuGet [Microsoft. Extensions. Logging. Abstracts](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/) .

Application Insights jest automatycznie dodawane przez Azure Functions.

> [!WARNING]
> - Nie dodawaj `AddApplicationInsightsTelemetry()` do kolekcji usług, która rejestruje usługi, które powodują konflikt z usługami udostępnianymi przez środowisko.
> - Nie rejestruj własnych `TelemetryConfiguration` ani nie `TelemetryClient` korzystasz z wbudowanej funkcji Application Insights. Jeśli trzeba skonfigurować własne `TelemetryClient` wystąpienie, należy utworzyć je za pośrednictwem iniekcji, `TelemetryConfiguration` jak pokazano na stronie [niestandardowe telemetrię w funkcjach języka C#](functions-dotnet-class-library.md?tabs=v2%2Ccmd#log-custom-telemetry-in-c-functions).

### <a name="iloggert-and-iloggerfactory"></a>ILogger <T> i ILoggerFactory

Host `ILogger<T>` `ILoggerFactory` wprowadza i usługi do konstruktorów.  Jednak domyślnie nowe filtry rejestrowania są filtrowane z dzienników funkcji.  Należy zmodyfikować plik, aby włączyć go `host.json` do dodatkowych filtrów i kategorii.

Poniższy przykład pokazuje, jak dodać `ILogger<HttpTrigger>` pliki z dziennikami, które są dostępne dla hosta.

```csharp
namespace MyNamespace
{
    public class HttpTrigger
    {
        private readonly ILogger<HttpTrigger> _log;

        public HttpTrigger(ILogger<HttpTrigger> log)
        {
            _log = log;
        }

        [FunctionName("HttpTrigger")]
        public async Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req)
        {
            _log.LogInformation("C# HTTP trigger function processed a request.");

            // ...
    }
}
```

Poniższy przykładowy `host.json` plik dodaje filtr dziennika.

```json
{
    "version": "2.0",
    "logging": {
        "applicationInsights": {
            "samplingSettings": {
                "isEnabled": true,
                "excludedTypes": "Request"
            }
        },
        "logLevel": {
            "MyNamespace.HttpTrigger": "Information"
        }
    }
}
```

Aby uzyskać więcej informacji na temat poziomów rejestrowania, zobacz [Konfigurowanie poziomów dzienników](configure-monitoring.md#configure-log-levels).

## <a name="function-app-provided-services"></a>Usługi funkcji dostarczone przez aplikację

Host funkcji rejestruje wiele usług. Następujące usługi są bezpieczne do podjęcia jako zależność w aplikacji:

|Typ usługi|Okres istnienia|Opis|
|--|--|--|
|`Microsoft.Extensions.Configuration.IConfiguration`|Pojedynczego|Konfiguracja środowiska uruchomieniowego|
|`Microsoft.Azure.WebJobs.Host.Executors.IHostIdProvider`|Pojedynczego|Osoba odpowiedzialna za podanie identyfikatora wystąpienia hosta|

Jeśli istnieją inne usługi, dla których chcesz wziąć zależność, [Utwórz problem i Zaproponuj je w serwisie GitHub](https://github.com/azure/azure-functions-host).

### <a name="overriding-host-services"></a>Zastępowanie usług hosta

Zastępowanie usług dostarczonych przez hosta nie jest obecnie obsługiwane.  Jeśli istnieją usługi, które chcesz przesłonić, [Utwórz problem i Zaproponuj je w serwisie GitHub](https://github.com/azure/azure-functions-host).

## <a name="working-with-options-and-settings"></a>Praca z opcjami i ustawieniami

Wartości zdefiniowane w [ustawieniach aplikacji](./functions-how-to-use-azure-function-app-settings.md#settings) są dostępne w `IConfiguration` wystąpieniu, co umożliwia odczytywanie wartości ustawień aplikacji w klasie startowej.

Można wyodrębnić wartości z `IConfiguration` wystąpienia do typu niestandardowego. Kopiowanie wartości ustawień aplikacji do typu niestandardowego ułatwia testowanie usług dzięki wprowadzeniu tych wartości do dodania. Ustawienia odczytywane w wystąpieniu konfiguracji muszą być prostymi parami klucz/wartość.

Rozważmy następującą klasę, która zawiera właściwość o nazwie zgodnej z ustawieniem aplikacji:

```csharp
public class MyOptions
{
    public string MyCustomSetting { get; set; }
}
```

I `local.settings.json` plik, który może zawierać strukturę niestandardowego ustawienia w następujący sposób:
```json
{
  "IsEncrypted": false,
  "Values": {
    "MyOptions:MyCustomSetting": "Foobar"
  }
}
```

Wewnątrz `Startup.Configure` metody można wyodrębnić wartości z `IConfiguration` wystąpienia do typu niestandardowego przy użyciu następującego kodu:

```csharp
builder.Services.AddOptions<MyOptions>()
    .Configure<IConfiguration>((settings, configuration) =>
    {
        configuration.GetSection("MyOptions").Bind(settings);
    });
```

Wywoływanie `Bind` kopii wartości, które mają pasujące nazwy właściwości z konfiguracji do wystąpienia niestandardowego. Wystąpienie opcji jest teraz dostępne w kontenerze IoC, aby wstrzyknąć do funkcji.

Obiekt options jest wstrzykiwany do funkcji jako wystąpienie interfejsu generycznego `IOptions` . Użyj `Value` właściwości, aby uzyskać dostęp do wartości znajdujących się w konfiguracji.

```csharp
using System;
using Microsoft.Extensions.Options;

public class HttpTrigger
{
    private readonly MyOptions _settings;

    public HttpTrigger(IOptions<MyOptions> options)
    {
        _settings = options.Value;
    }
}
```

Zapoznaj się z [wzorcem opcji w ASP.NET Core](/aspnet/core/fundamentals/configuration/options) , aby uzyskać więcej informacji dotyczących pracy z opcjami.

## <a name="customizing-configuration-sources"></a>Dostosowywanie źródeł konfiguracji

> [!NOTE]
> Dostosowanie źródła konfiguracji jest dostępne począwszy Azure Functions od wersji 2.0.14192.0 i 3.0.14191.0 hosta.

Aby określić dodatkowe źródła konfiguracji, Zastąp `ConfigureAppConfiguration` metodę w klasie aplikacji funkcji `StartUp` .

Poniższy przykład dodaje wartości konfiguracji z podstawowego i opcjonalnego pliku ustawień aplikacji specyficznych dla środowiska.

```csharp
using System.IO;
using Microsoft.Azure.Functions.Extensions.DependencyInjection;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.DependencyInjection;

[assembly: FunctionsStartup(typeof(MyNamespace.Startup))]

namespace MyNamespace
{
    public class Startup : FunctionsStartup
    {
        public override void ConfigureAppConfiguration(IFunctionsConfigurationBuilder builder)
        {
            FunctionsHostBuilderContext context = builder.GetContext();

            builder.ConfigurationBuilder
                .AddJsonFile(Path.Combine(context.ApplicationRootPath, "appsettings.json"), optional: true, reloadOnChange: false)
                .AddJsonFile(Path.Combine(context.ApplicationRootPath, $"appsettings.{context.EnvironmentName}.json"), optional: true, reloadOnChange: false)
                .AddEnvironmentVariables();
        }
    }
}
```

Dodaj dostawców konfiguracji do `ConfigurationBuilder` właściwości `IFunctionsConfigurationBuilder` . Aby uzyskać więcej informacji na temat korzystania z dostawców konfiguracji, zobacz [Konfiguracja w ASP.NET Core](/aspnet/core/fundamentals/configuration/#configuration-providers).

`FunctionsHostBuilderContext`Uzyskano z `IFunctionsConfigurationBuilder.GetContext()` . Użyj tego kontekstu, aby pobrać bieżącą nazwę środowiska i rozpoznać lokalizację plików konfiguracji w folderze aplikacji funkcji.

Domyślnie pliki konfiguracyjne, takie jak *appsettings.json* nie są automatycznie kopiowane do folderu wyjściowego aplikacji funkcji. Zaktualizuj plik *. csproj* , aby pasował do poniższego przykładu, aby upewnić się, że pliki zostały skopiowane.

```xml
<None Update="appsettings.json">
    <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>      
</None>
<None Update="appsettings.Development.json">
    <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>
    <CopyToPublishDirectory>Never</CopyToPublishDirectory>
</None>
```

> [!IMPORTANT]
> W przypadku aplikacji funkcji działających w planach zużycia lub Premium modyfikacje wartości konfiguracyjnych używanych w wyzwalaczach mogą spowodować błędy skalowania. Wszelkie zmiany tych właściwości przez `FunctionsStartup` klasę powoduje błąd uruchamiania aplikacji funkcji.

## <a name="next-steps"></a>Następne kroki

Więcej informacji można znaleźć w następujących zasobach:

- [Jak monitorować aplikację funkcji](functions-monitoring.md)
- [Najlepsze rozwiązania dotyczące funkcji](functions-best-practices.md)
