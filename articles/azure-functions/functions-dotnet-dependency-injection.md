---
title: Use dependency injection in .NET Azure Functions (Korzystanie z wstrzykiwania zależności w usłudze Azure Functions na platformie .NET)
description: Dowiedz się, jak używać iniekcji zależności do rejestrowania i używania usług w funkcjach .NET
author: craigshoemaker
ms.topic: reference
ms.date: 09/05/2019
ms.author: cshoe
ms.reviewer: jehollan
ms.openlocfilehash: a1ff8e0aedce5d3a6acc9a39084cf0839efdd88e
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81678455"
---
# <a name="use-dependency-injection-in-net-azure-functions"></a>Use dependency injection in .NET Azure Functions (Korzystanie z wstrzykiwania zależności w usłudze Azure Functions na platformie .NET)

Usługa Azure Functions obsługuje wzorzec projektowania oprogramowania iniekcji zależności (DI), który jest techniką osiągnięcia [inwersji kontroli (IoC)](https://docs.microsoft.com/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) między klasami i ich zależnościami.

- Iniekcja zależności w usłudze Azure Functions jest oparta na funkcjach iniekcji zależności .NET Core. Zaleca się znajomość [iniekcji zależności .NET Core.](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection) Istnieją różnice w sposobie zastępowania zależności i jak wartości konfiguracji są odczytywane za pomocą usługi Azure Functions w planie zużycia.

- Obsługa iniekcji zależności rozpoczyna się od usługi Azure Functions 2.x.

## <a name="prerequisites"></a>Wymagania wstępne

Przed użyciem iniekcji zależności należy zainstalować następujące pakiety NuGet:

- [Microsoft.Azure.Functions.Extensions](https://www.nuget.org/packages/Microsoft.Azure.Functions.Extensions/)

- [Pakiet Microsoft.NET.Sdk.Functions](https://www.nuget.org/packages/Microsoft.NET.Sdk.Functions/) w wersji 1.0.28 lub nowszej

## <a name="register-services"></a>Rejestrowanie usług

Aby zarejestrować usługi, należy utworzyć metodę konfigurowania i dodawania składników do wystąpienia. `IFunctionsHostBuilder`  Host usługi Azure Functions `IFunctionsHostBuilder` tworzy wystąpienie i przekazuje je bezpośrednio do metody.

Aby zarejestrować metodę, `FunctionsStartup` należy dodać atrybut zestawu, który określa nazwę typu używanego podczas uruchamiania.

```csharp
using System;
using Microsoft.Azure.Functions.Extensions.DependencyInjection;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Http;
using Microsoft.Extensions.Logging;

[assembly: FunctionsStartup(typeof(MyNamespace.Startup))]

namespace MyNamespace
{
    public class Startup : FunctionsStartup
    {
        public override void Configure(IFunctionsHostBuilder builder)
        {
            builder.Services.AddHttpClient();

            builder.Services.AddSingleton((s) => {
                return new MyService();
            });

            builder.Services.AddSingleton<ILoggerProvider, MyLoggerProvider>();
        }
    }
}
```

### <a name="caveats"></a>Zastrzeżenia

Seria kroków rejestracji uruchamianych przed i po zakończeniu środowiska wykonawczego przetwarza klasę uruchamiania. W związku z tym należy pamiętać o następujących elementach:

- *Klasa uruchamiania jest przeznaczona tylko do konfiguracji i rejestracji.* Należy unikać korzystania z usług zarejestrowanych podczas uruchamiania podczas procesu uruchamiania. Na przykład nie próbuj rejestrować komunikat w rejestratorze, który jest rejestrowany podczas uruchamiania. Ten punkt procesu rejestracji jest zbyt wcześnie, aby twoje usługi były dostępne do użytku. Po `Configure` uruchomieniu metody środowisko wykonawcze Functions kontynuuje rejestrowanie dodatkowych zależności, co może mieć wpływ na sposób działania usług.

- *Kontener iniekcji zależności zawiera tylko jawnie zarejestrowane typy.* Jedynymi usługami dostępnymi jako typy `Configure` iniekcji są ustawienia w metodzie. W rezultacie typy specyficzne `BindingContext` dla `ExecutionContext` funkcji, takie jak i nie są dostępne podczas instalacji lub jako typy iniekcji.

## <a name="use-injected-dependencies"></a>Używanie wstrzykniętych zależności

Iniekcji konstruktora jest używany do udostępnienia zależności w funkcji. Użycie iniekcji konstruktora wymaga, aby nie używać klas statycznych.

W poniższym przykładzie `IMyService` `HttpClient` pokazano, jak i zależności są wstrzykiwane do funkcji wyzwalane przez HTTP. W tym przykładzie użyto pakietu [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) wymaganego do zarejestrowania podczas uruchamiania. `HttpClient`

```csharp
using System;
using System.IO;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;
using System.Net.Http;

namespace MyNamespace
{
    public class HttpTrigger
    {
        private readonly IMyService _service;
        private readonly HttpClient _client;

        public HttpTrigger(IMyService service, HttpClient httpClient)
        {
            _service = service;
            _client = httpClient;
        }

        [FunctionName("GetPosts")]
        public async Task<IActionResult> Get(
            [HttpTrigger(AuthorizationLevel.Function, "get", Route = "posts")] HttpRequest req,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");
            var res = await _client.GetAsync("https://microsoft.com");
            await _service.AddResponse(res);

            return new OkResult();
        }
    }
}
```

## <a name="service-lifetimes"></a>Okresy istnienia usługi

Aplikacje usługi Azure Functions zapewniają te same okresy istnienia [usługi, co ASP.NET iniekcji zależności.](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection#service-lifetimes) W przypadku aplikacji Functions różne okresy istnienia usługi zachowują się w następujący sposób:

- **Przejściowe:** Usługi przejściowe są tworzone na każde żądanie usługi.
- **Zakres:** okres istnienia usługi o określonym zakresie odpowiada okresowi istnienia wykonania funkcji. Usługi o określonym zakresie są tworzone raz na wykonanie. Późniejsze żądania dla tej usługi podczas wykonywania ponownie użyć istniejącego wystąpienia usługi.
- **Singleton:** okres istnienia usługi singleton odpowiada okres istnienia hosta i jest ponownie używany przez wykonywanie funkcji w tym wystąpieniu. Usługi okresu istnienia singleton są zalecane dla `SqlConnection` `HttpClient` połączeń i klientów, na przykład lub wystąpień.

Wyświetlanie lub pobieranie [próbki różnych okresów istnienia usługi](https://aka.ms/functions/di-sample) w usłudze GitHub.

## <a name="logging-services"></a>Usługi rejestrowania

Jeśli potrzebujesz własnego dostawcy rejestrowania, zarejestruj typ `ILoggerProvider` niestandardowy jako wystąpienie. Usługa Application Insights jest dodawana przez usługę Azure Functions automatycznie.

> [!WARNING]
> - Nie należy `AddApplicationInsightsTelemetry()` dodawać do kolekcji usług, ponieważ rejestruje usługi, które są w konflikcie z usługami świadczonymi przez środowisko.
> - Nie rejestruj `TelemetryConfiguration` własnych `TelemetryClient` lub jeśli używasz wbudowanej funkcji usługi Application Insights. Jeśli chcesz skonfigurować własne `TelemetryClient` wystąpienie, utwórz `TelemetryConfiguration` je za pomocą wstrzykniętych, jak pokazano w [monitorze funkcji platformy Azure](./functions-monitoring.md#version-2x-and-later-2).

### <a name="iloggert-and-iloggerfactory"></a>ILogger<T> i ILoggerFactory

Host wstrzyknie `ILogger<T>` i `ILoggerFactory` usługi do konstruktorów.  Jednak domyślnie te nowe filtry rejestrowania będą odfiltrowywały z dzienników funkcji.  Musisz zmodyfikować plik, aby zdecydować się na `host.json` dodatkowe filtry i kategorie.  Poniższy przykład pokazuje `ILogger<HttpTrigger>` dodanie z dzienników, które będą udostępniane przez hosta.

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

I `host.json` plik, który dodaje filtr dziennika.

```json
{
    "version": "2.0",
    "logging": {
        "applicationInsights": {
            "samplingExcludedTypes": "Request",
            "samplingSettings": {
                "isEnabled": true
            }
        },
        "logLevel": {
            "MyNamespace.HttpTrigger": "Information"
        }
    }
}
```

## <a name="function-app-provided-services"></a>Usługi świadczone w aplikacji funkcyjnej

Host funkcji rejestruje wiele usług. Następujące usługi są bezpieczne do podjęcia jako zależność w aplikacji:

|Typ usługi|Okres istnienia|Opis|
|--|--|--|
|`Microsoft.Extensions.Configuration.IConfiguration`|Singleton|Konfiguracja środowiska wykonawczego|
|`Microsoft.Azure.WebJobs.Host.Executors.IHostIdProvider`|Singleton|Odpowiedzialny za podanie identyfikatora wystąpienia hosta|

Jeśli istnieją inne usługi, na których chcesz się uzależnić, [utwórz problem i zaproponuj je w usłudze GitHub](https://github.com/azure/azure-functions-host).

### <a name="overriding-host-services"></a>Zastępowanie usług hosta

Zastępowanie usług świadczonych przez hosta nie jest obecnie obsługiwane.  Jeśli istnieją usługi, które chcesz zastąpić, [utwórz problem i zaproponuj je w usłudze GitHub](https://github.com/azure/azure-functions-host).

## <a name="working-with-options-and-settings"></a>Praca z opcjami i ustawieniami

Wartości zdefiniowane w [ustawieniach aplikacji](./functions-how-to-use-azure-function-app-settings.md#settings) są dostępne w wystąpieniu, `IConfiguration` które umożliwia odczytywanie wartości ustawień aplikacji w klasie uruchamiania.

Wartości z wystąpienia `IConfiguration` można wyodrębnić do typu niestandardowego. Kopiowanie wartości ustawień aplikacji do typu niestandardowego ułatwia testowanie usług przez wprowadzenie tych wartości. Ustawienia odczytywane w wystąpieniu konfiguracji muszą być prostymi parami klucz/wartość.

Należy wziąć pod uwagę następującą klasę, która zawiera właściwość o nazwie zgodne z ustawieniem aplikacji:

```csharp
public class MyOptions
{
    public string MyCustomSetting { get; set; }
}
```

I `local.settings.json` plik, który może struktury ustawienia niestandardowego w następujący sposób:
```json
{
  "IsEncrypted": false,
  "Values": {
    "MyOptions:MyCustomSetting": "Foobar"
  }
}
```

Wewnątrz `Startup.Configure` metody można wyodrębnić wartości `IConfiguration` z wystąpienia do typu niestandardowego przy użyciu następującego kodu:

```csharp
builder.Services.AddOptions<MyOptions>()
                .Configure<IConfiguration>((settings, configuration) =>
                                           {
                                                configuration.GetSection("MyOptions").Bind(settings);
                                           });
```

Wywołanie `Bind` kopiuje wartości, które mają pasujące nazwy właściwości z konfiguracji do wystąpienia niestandardowego. Opcja wystąpienie jest teraz dostępna w kontenerze IoC, aby wstrzyknąć do funkcji.

Obiekt opcji jest wstrzykiwany do funkcji jako `IOptions` wystąpienie interfejsu ogólnego. Użyj `Value` właściwości, aby uzyskać dostęp do wartości znalezionych w konfiguracji.

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

Więcej informacji na temat pracy z opcjami można znaleźć [w obszarze Wzorzec opcji w ASP.NET Core.](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/options)

> [!WARNING]
> Unikaj prób odczytywania wartości z plików, takich jak *local.settings.json* lub *appsettings.{ środowiska}.json* w planie zużycia. Wartości odczytywane z tych plików związanych z połączeniami wyzwalacza nie są dostępne, ponieważ aplikacja jest skalowana, ponieważ infrastruktura hostingu nie ma dostępu do informacji o konfiguracji.

## <a name="next-steps"></a>Następne kroki

Więcej informacji zawierają następujące zasoby:

- [Jak monitorować aplikację funkcji](functions-monitoring.md)
- [Najważniejsze wskazówki dotyczące funkcji](functions-best-practices.md)
