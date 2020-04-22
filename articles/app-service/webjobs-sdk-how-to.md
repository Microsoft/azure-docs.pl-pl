---
title: Jak korzystać z sdk WebJobs
description: Dowiedz się więcej o tym, jak pisać kod dla sdk WebJobs. Tworzenie zadań przetwarzania w tle opartych na zdarzeniach, które uzyskują dostęp do danych na platformie Azure i w usługach innych firm.
author: ggailey777
ms.devlang: dotnet
ms.topic: article
ms.date: 02/18/2019
ms.author: glenga
ms.openlocfilehash: 5a8d5f96449cfecd4628c38fa2788a1e06e96b07
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81758894"
---
# <a name="how-to-use-the-azure-webjobs-sdk-for-event-driven-background-processing"></a>How to use the Azure WebJobs SDK for event-driven background processing (Jak używać zestawu SDK usługi Azure WebJobs w celu opartego na zdarzeniach przetwarzania w tle)

Ten artykuł zawiera wskazówki dotyczące pracy z zestawem SDK usługi Azure WebJobs. Aby od razu rozpocząć korzystanie z aplikacji WebJobs, zobacz [Wprowadzenie do sdk Azure WebJobs sdk do przetwarzania w tle opartego na zdarzeniach.](webjobs-sdk-get-started.md) 

## <a name="webjobs-sdk-versions"></a>Wersje SDK webjobs

Są to kluczowe różnice między wersją 3. *x* i wersja 2. *x* sdk WebJobs:

* Wersja 3. *x* dodaje obsługę .NET Core.
* W wersji 3. *x*, należy jawnie zainstalować rozszerzenie powiązania magazynu wymagane przez pakiet SDK webjobs. W wersji 2. *x*, powiązania magazynu zostały uwzględnione w zestawie SDK.
* Narzędzia programu Visual Studio dla platformy .NET Core (3.* x)* projekty różnią się od narzędzi dla programu .NET Framework (2.* x)* projektów. Aby dowiedzieć się więcej, zobacz [Tworzenie i wdrażanie aplikacji WebJobs przy użyciu programu Visual Studio — Usługa Azure App Service](webjobs-dotnet-deploy-vs.md).

Jeśli to możliwe, przykłady są dostarczane dla obu wersji 3. *x* i wersja 2. *x*.

> [!NOTE]
> [Usługa Azure Functions](../azure-functions/functions-overview.md) jest oparta na sdk WebJobs, a ten artykuł zawiera łącza do dokumentacji usługi Azure Functions dla niektórych tematów. Należy zwrócić uwagę na te różnice między funkcjami i webjobs SDK:
> * Usługa Azure Functions w wersji 2. *x* odpowiada WebJobs SDK w wersji 3. *x*i Azure Functions 1. *x* odpowiada WebJobs SDK 2. *x*. Repozytoria kodu źródłowego używają numerowania zestawu SDK WebJobs.
> * Przykładowy kod bibliotek klas usługi Azure Functions C# jest podobny do kodu SDK WebJobs, z tą różnicą, że nie potrzebujesz atrybutu `FunctionName` w projekcie SDK webjobs.
> * Niektóre typy powiązań są obsługiwane tylko w funkcjach, takich jak HTTP (Webhooks) i Event Grid (który jest oparty na HTTP).
>
> Aby uzyskać więcej informacji, zobacz [Porównanie zestawów SDK webjobs i usług Azure Functions](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md#compare-functions-and-webjobs).

## <a name="webjobs-host"></a>Host WebJobs

Host jest kontenerem środowiska wykonawczego dla funkcji.  Nasłuchuje wyzwalaczy i wywołuje funkcje. W wersji 3. *x*, host jest `IHost`implementacją . W wersji 2. *x*, należy `JobHost` użyć obiektu. Utworzyć wystąpienie hosta w kodzie i napisać kod, aby dostosować jego zachowanie.

Jest to kluczowa różnica między przy użyciu webjobs SDK bezpośrednio i przy użyciu go pośrednio za pośrednictwem usługi Azure Functions. W usłudze Azure Functions usługa kontroluje hosta i nie można dostosować hosta, pisząc kod. Usługa Azure Functions umożliwia dostosowanie zachowania hosta za pomocą ustawień w pliku host.json. Te ustawienia są ciągi, a nie kod, a to ogranicza rodzaje dostosowań, które można zrobić.

### <a name="host-connection-strings"></a>Parametry połączenia hosta

Zestaw WebJobs SDK wyszukuje parametry połączeń usługi Azure Storage i Azure Service Bus w pliku local.settings.json podczas uruchamiania lokalnie lub w środowisku usługi WebJob po uruchomieniu na platformie Azure. Domyślnie wymagane jest ustawienie `AzureWebJobsStorage` ciągu połączenia magazynu o nazwie.  

Wersja 2. *x* sdk umożliwia użycie własnych nazw dla tych ciągów połączeń lub przechowywać je w innym miejscu. Nazwy w kodzie można [`JobHostConfiguration`]ustawić za pomocą , jak pokazano poniżej:

```cs
static void Main(string[] args)
{
    var _storageConn = ConfigurationManager
        .ConnectionStrings["MyStorageConnection"].ConnectionString;

    //// Dashboard logging is deprecated; use Application Insights.
    //var _dashboardConn = ConfigurationManager
    //    .ConnectionStrings["MyDashboardConnection"].ConnectionString;

    JobHostConfiguration config = new JobHostConfiguration();
    config.StorageConnectionString = _storageConn;
    //config.DashboardConnectionString = _dashboardConn;
    JobHost host = new JobHost(config);
    host.RunAndBlock();
}
```

Ponieważ wersja 3. *x* używa domyślnych interfejsów API konfiguracji rdzenia .NET, nie ma interfejsu API do zmiany nazw ciągów połączeń.

### <a name="host-development-settings"></a>Ustawienia rozwoju hosta

Hosta można uruchomić w trybie rozwoju, aby uczynić rozwój lokalny bardziej wydajnym. Oto niektóre z ustawień, które są zmieniane po uruchomieniu w trybie programowania:

| Właściwość | Ustawienie rozwoju |
| ------------- | ------------- |
| `Tracing.ConsoleLevel` | `TraceLevel.Verbose`, aby zmaksymalizować wydajność dziennika. |
| `Queues.MaxPollingInterval`  | Niska wartość, aby upewnić się, że metody kolejki są wyzwalane natychmiast.  |
| `Singleton.ListenerLockPeriod` | 15 sekund, aby pomóc w szybkim rozwoju iteracyjnej. |

Proces włączania trybu programowania zależy od wersji SDK. 

#### <a name="version-3x"></a>Wersja 3. *x*

Wersja 3. *x* używa standardowych ASP.NET interfejsów API core. Wywołanie [`UseEnvironment`](/dotnet/api/microsoft.extensions.hosting.hostinghostbuilderextensions.useenvironment) metody [`HostBuilder`](/dotnet/api/microsoft.extensions.hosting.hostbuilder) w wystąpieniu. Przekaż ciąg `development`o nazwie , jak w tym przykładzie:

```cs
static async Task Main()
{
    var builder = new HostBuilder();
    builder.UseEnvironment("development");
    builder.ConfigureWebJobs(b =>
            {
                b.AddAzureStorageCoreServices();
            });
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

#### <a name="version-2x"></a>Wersja 2. *x*

Klasa `JobHostConfiguration` ma `UseDevelopmentSettings` metodę, która włącza tryb programowania.  W poniższym przykładzie pokazano, jak używać ustawień rozwoju. Aby `config.IsDevelopment` powrócić, `true` gdy działa lokalnie, należy `AzureWebJobsEnv` ustawić lokalną `Development`zmienną środowiskową o nazwie o wartości .

```cs
static void Main()
{
    config = new JobHostConfiguration();

    if (config.IsDevelopment)
    {
        config.UseDevelopmentSettings();
    }

    var host = new JobHost(config);
    host.RunAndBlock();
}
```

### <a name="managing-concurrent-connections-version-2x"></a><a name="jobhost-servicepointmanager-settings"></a>Zarządzanie równoczesnych połączeń (wersja 2.* x*)

W wersji 3. *x*, limit połączenia domyślnie ma wartość połączeń nieskończonych. Jeśli z jakiegoś powodu trzeba zmienić ten [`MaxConnectionsPerServer`](/dotnet/api/system.net.http.winhttphandler.maxconnectionsperserver) limit, [`WinHttpHandler`](/dotnet/api/system.net.http.winhttphandler) można użyć właściwości klasy.

W wersji 2. *x*, można kontrolować liczbę równoczesnych połączeń z hostem za pomocą interfejsu [API ServicePointManager.DefaultConnectionLimit.](/dotnet/api/system.net.servicepointmanager.defaultconnectionlimit#System_Net_ServicePointManager_DefaultConnectionLimit) W 2. *x*, należy zwiększyć tę wartość z domyślnej wartości 2 przed uruchomieniem hosta WebJobs.

Wszystkie wychodzące żądania HTTP, które `HttpClient` można `ServicePointManager`wykonać z funkcji przy użyciu przepływu przez . Po osiągnięciu wartości ustawionej w `DefaultConnectionLimit`, `ServicePointManager` rozpoczyna kolejkowanie żądań przed wysłaniem ich. Załóżmy, że twój `DefaultConnectionLimit` jest ustawiony na 2, a kod powoduje 1000 żądań HTTP. Początkowo tylko dwa żądania są dozwolone za pośrednictwem systemu operacyjnego. Pozostałe 998 są w kolejce, dopóki nie ma dla nich miejsca. Oznacza to, że może `HttpClient` limit czasu, ponieważ wydaje się, że złożył żądanie, ale żądanie nigdy nie został wysłany przez system operacyjny do serwera docelowego. Więc może pojawić się zachowanie, które nie wydaje `HttpClient` się mieć sens: lokalne trwa 10 sekund, aby zakończyć żądanie, ale usługa zwraca każde żądanie w 200 ms. 

Domyślną wartością dla ASP.NET `Int32.MaxValue`aplikacji jest , i to może działać dobrze dla WebJobs uruchomionych w planie usługi aplikacji podstawowych lub wyższych. WebJobs zazwyczaj potrzebują ustawienia Zawsze włączone, a to jest obsługiwane tylko przez plany usługi podstawowej i wyższej.

Jeśli twój webjob jest uruchomiony w planie bezpłatnej lub udostępnionej usługi aplikacji, aplikacja jest ograniczona przez obszar izolowania usługi App Service, który obecnie ma [limit połączenia 300](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#per-sandbox-per-appper-site-numerical-limits). Przy niezwiązanym `ServicePointManager`limicie połączeń w programie jest większe prawdopodobieństwo, że próg połączenia piaskownicy zostanie osiągnięty, a witryna zostanie zamknięta. W takim przypadku `DefaultConnectionLimit` ustawienie na coś niższego, takiego jak 50 lub 100, może zapobiec temu i nadal umożliwiać wystarczającą przepustowość.

Ustawienie musi być skonfigurowane przed dokonaniem jakichkolwiek żądań HTTP. Z tego powodu host WebJobs nie powinien automatycznie dostosowywać ustawienia. Mogą istnieć żądania HTTP, które występują przed uruchomieniem hosta, co może prowadzić do nieoczekiwanego zachowania. Najlepszym rozwiązaniem jest ustawienie wartości natychmiast `Main` w metodzie przed zainicjowaniem `JobHost`, jak pokazano poniżej:

```csharp
static void Main(string[] args)
{
    // Set this immediately so that it's used by all requests.
    ServicePointManager.DefaultConnectionLimit = Int32.MaxValue;

    var host = new JobHost();
    host.RunAndBlock();
}
```

## <a name="triggers"></a>Wyzwalacze

Funkcje muszą być metodami publicznymi i [`NoAutomaticTrigger`](#manual-triggers) muszą mieć jeden atrybut wyzwalacza lub atrybut.

### <a name="automatic-triggers"></a>Automatyczne wyzwalacze

Automatyczne wyzwalacze wywołać funkcję w odpowiedzi na zdarzenie. Rozważmy ten przykład funkcji, która jest wyzwalana przez komunikat dodany do usługi Azure Queue storage. Odpowiada, odczytając obiekt blob z magazynu obiektów Blob platformy Azure:

```cs
public static void Run(
    [QueueTrigger("myqueue-items")] string myQueueItem,
    [Blob("samples-workitems/{queueTrigger}", FileAccess.Read)] Stream myBlob,
    ILogger log)
{
    log.LogInformation($"BlobInput processed blob\n Name:{myQueueItem} \n Size: {myBlob.Length} bytes");
}
```

Atrybut `QueueTrigger` informuje środowisko wykonawcze, aby wywołać funkcję, gdy `myqueue-items` komunikat kolejki pojawia się w kolejce. Atrybut `Blob` informuje środowisko uruchomieniowe, aby użyć komunikatu kolejki do odczytu obiektu blob w kontenerze *przykładu pracy.* Nazwa elementu obiektu blob `samples-workitems` w kontenerze jest uzyskiwana bezpośrednio`{queueTrigger}`z wyzwalacza kolejki jako wyrażenie wiążące ( ).

[!INCLUDE [webjobs-always-on-note](../../includes/webjobs-always-on-note.md)]

### <a name="manual-triggers"></a>Ręczne wyzwalacze

Aby wyzwolić funkcję ręcznie, `NoAutomaticTrigger` użyj atrybutu, jak pokazano poniżej:

```cs
[NoAutomaticTrigger]
public static void CreateQueueMessage(
ILogger logger,
string value,
[Queue("outputqueue")] out string message)
{
    message = value;
    logger.LogInformation("Creating queue message: ", message);
}
```

Proces ręcznego wyzwalania funkcji zależy od wersji SDK.

#### <a name="version-3x"></a>Wersja 3. *x*

```cs
static async Task Main(string[] args)
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
        b.AddAzureStorage();
    });
    var host = builder.Build();
    using (host)
    {
        var jobHost = host.Services.GetService(typeof(IJobHost)) as JobHost;
        var inputs = new Dictionary<string, object>
        {
            { "value", "Hello world!" }
        };

        await host.StartAsync();
        await jobHost.CallAsync("CreateQueueMessage", inputs);
        await host.StopAsync();
    }
}
```

#### <a name="version-2x"></a>Wersja 2. *x*

```cs
static void Main(string[] args)
{
    JobHost host = new JobHost();
    host.Call(typeof(Program).GetMethod("CreateQueueMessage"), new { value = "Hello world!" });
}
```

## <a name="input-and-output-bindings"></a>Powiązania wejściowe i wyjściowe

Powiązania wejściowe zapewniają deklaratywny sposób udostępniania kodu do udostępniania kodu danym z platformy Azure lub usług innych firm. Powiązania danych wyjściowych umożliwiają aktualizowanie danych. W artykule [Wprowadzenie](webjobs-sdk-get-started.md) przedstawiono przykład każdego z nich.

Można użyć wartości zwracanej metody dla powiązania danych wyjściowych, stosując atrybut do wartości zwracanej metody. Zobacz przykład w [użyciu wartości zwracanej funkcji platformy Azure](../azure-functions/functions-bindings-return-value.md).

## <a name="binding-types"></a>Typy wiązań

Proces instalowania typów powiązań i zarządzania nimi zależy od tego, czy używasz wersji 3. *x* lub wersja 2. *x* sdk. Pakiet do zainstalowania dla określonego typu powiązania można znaleźć w sekcji "Pakiety" tego typu powiązania w [artykule odwołania](#binding-reference-information)do usługi Azure Functions. Wyjątkiem jest wyzwalacz plików i powiązanie (dla lokalnego systemu plików), który nie jest obsługiwany przez usługę Azure Functions.

#### <a name="version-3x"></a>Wersja 3. *x*

W wersji 3. *x*, powiązania magazynu są zawarte `Microsoft.Azure.WebJobs.Extensions.Storage` w pakiecie. Wywołanie `AddAzureStorage` metody rozszerzenia `ConfigureWebJobs` w metodzie, jak pokazano poniżej:

```cs
static async Task Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
            {
                b.AddAzureStorageCoreServices();
                b.AddAzureStorage();
            });
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

Aby użyć innych typów wyzwalaczy i powiązań, zainstaluj `Add<binding>` pakiet NuGet, który je zawiera i wywołaj metodę rozszerzenia zaimplementowana w rozszerzeniu. Na przykład, jeśli chcesz użyć powiązania usługi Azure `Microsoft.Azure.WebJobs.Extensions.CosmosDB` Cosmos `AddCosmosDB`DB, zainstaluj i wywołaj , w ten sposób:

```cs
static async Task Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
            {
                b.AddAzureStorageCoreServices();
                b.AddCosmosDB();
            });
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

Aby użyć wyzwalacza czasomierza lub powiązania Pliki, które `AddTimers` `AddFiles` są częścią podstawowych usług, należy wywołać lub rozszerzenia metody, odpowiednio.

#### <a name="version-2x"></a>Wersja 2. *x*

Te typy wyzwalaczy i powiązania są zawarte w wersji 2. *x* `Microsoft.Azure.WebJobs` opakowania:

* Blob Storage
* Queue Storage
* Table Storage

Aby użyć innych typów wyzwalacza i powiązania, należy zainstalować `Use<binding>` pakiet `JobHostConfiguration` NuGet, który je zawiera i wywołać metodę na obiekcie. Na przykład, jeśli chcesz użyć wyzwalacza `Microsoft.Azure.WebJobs.Extensions` czasomierza, zainstaluj i zadzwoń `UseTimers` w metodzie, `Main` jak pokazano poniżej:

```cs
static void Main()
{
    config = new JobHostConfiguration();
    config.UseTimers();
    var host = new JobHost(config);
    host.RunAndBlock();
}
```

Aby użyć powiązania Pliki, `Microsoft.Azure.WebJobs.Extensions` `UseFiles`zainstaluj i zadzwoń .

### <a name="executioncontext"></a>Executioncontext

WebJobs umożliwia powiązanie [`ExecutionContext`]z . Za pomocą tego powiązania [`ExecutionContext`] można uzyskać dostęp do jako parametr w podpisie funkcji. Na przykład poniższy kod używa obiektu kontekstu, aby uzyskać dostęp do identyfikatora wywołania, który służy do skorelowania wszystkich dzienników wyprodukowanych przez wywołanie danej funkcji.  

```cs
public class Functions
{
    public static void ProcessQueueMessage([QueueTrigger("queue")] string message,
        ExecutionContext executionContext,
        ILogger logger)
    {
        logger.LogInformation($"{message}\n{executionContext.InvocationId}");
    }
}
```

Proces wiązania z [`ExecutionContext`] wersją SDK zależy od wersji.

#### <a name="version-3x"></a>Wersja 3. *x*

Wywołanie `AddExecutionContextBinding` metody rozszerzenia `ConfigureWebJobs` w metodzie, jak pokazano poniżej:

```cs
static async Task Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
            {
                b.AddAzureStorageCoreServices();
                b.AddExecutionContextBinding();
            });
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

#### <a name="version-2x"></a>Wersja 2. *x*

Pakiet, `Microsoft.Azure.WebJobs.Extensions` o którym mowa wcześniej zawiera również specjalny typ `UseCore` powiązania, które można zarejestrować, wywołując metodę. To powiązanie umożliwia [`ExecutionContext`] zdefiniowanie parametru w podpisie funkcji, który jest włączony w ten sposób:

```cs
class Program
{
    static void Main()
    {
        config = new JobHostConfiguration();
        config.UseCore();
        var host = new JobHost(config);
        host.RunAndBlock();
    }
}
```

## <a name="binding-configuration"></a>Konfiguracja wiązania

Można skonfigurować zachowanie niektórych wyzwalaczy i powiązań. Proces ich konfigurowania zależy od wersji SDK.

* **Wersja 3. *x*: Ustaw** `Add<Binding>` konfigurację, `ConfigureWebJobs`gdy metoda jest wywoływana w .
* **Wersja 2. *x*: Ustaw** konfigurację, ustawiając właściwości obiektu `JobHost`konfiguracyjnego, do którego przechodzisz.

Te ustawienia specyficzne dla powiązania są równoważne z ustawieniami w [pliku projektu host.json](../azure-functions/functions-host-json.md) w usłudze Azure Functions.

Można skonfigurować następujące powiązania:

* [Wyzwalacz usługi Azure CosmosDB](#azure-cosmosdb-trigger-configuration-version-3x)
* [Wyzwalacz Centrów zdarzeń](#event-hubs-trigger-configuration-version-3x)
* [Wyzwalacz magazynu kolejek](#queue-storage-trigger-configuration)
* [Wiązanie SendGrid](#sendgrid-binding-configuration-version-3x)
* [Wyzwalacz usługi Service Bus](#service-bus-trigger-configuration-version-3x)

### <a name="azure-cosmosdb-trigger-configuration-version-3x"></a>Konfiguracja wyzwalacza usługi Azure CosmosDB (wersja 3.* x*)

W tym przykładzie pokazano, jak skonfigurować wyzwalacz usługi Azure Cosmos DB:

```cs
static async Task Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
        b.AddCosmosDB(a =>
        {
            a.ConnectionMode = ConnectionMode.Gateway;
            a.Protocol = Protocol.Https;
            a.LeaseOptions.LeasePrefix = "prefix1";

        });
    });
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

Aby uzyskać więcej informacji, zobacz artykuł [wiązania usługi Azure CosmosDB.](../azure-functions/functions-bindings-cosmosdb-v2-output.md#hostjson-settings)

### <a name="event-hubs-trigger-configuration-version-3x"></a>Konfiguracja wyzwalania centrów zdarzeń (wersja 3.* x*)

W tym przykładzie pokazano, jak skonfigurować wyzwalacz Centrum zdarzeń:

```cs
static async Task Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
        b.AddEventHubs(a =>
        {
            a.BatchCheckpointFrequency = 5;
            a.EventProcessorOptions.MaxBatchSize = 256;
            a.EventProcessorOptions.PrefetchCount = 512;
        });
    });
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

Aby uzyskać więcej informacji, zobacz event [hubs powiązanie](../azure-functions/functions-bindings-event-hubs-trigger.md#host-json) artykułu.

### <a name="queue-storage-trigger-configuration"></a>Konfiguracja wyzwalacza magazynu kolejki

W tych przykładach pokazano, jak skonfigurować wyzwalacz magazynu kolejki:

#### <a name="version-3x"></a>Wersja 3. *x*

```cs
static async Task Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
        b.AddAzureStorage(a => {
            a.BatchSize = 8;
            a.NewBatchThreshold = 4;
            a.MaxDequeueCount = 4;
            a.MaxPollingInterval = TimeSpan.FromSeconds(15);
        });
    });
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

Aby uzyskać więcej informacji, zobacz artykuł [powiązania magazynu kolejki.](../azure-functions/functions-bindings-storage-queue-trigger.md#hostjson-properties)

#### <a name="version-2x"></a>Wersja 2. *x*

```cs
static void Main(string[] args)
{
    JobHostConfiguration config = new JobHostConfiguration();
    config.Queues.BatchSize = 8;
    config.Queues.NewBatchThreshold = 4;
    config.Queues.MaxDequeueCount = 4;
    config.Queues.MaxPollingInterval = TimeSpan.FromSeconds(15);
    JobHost host = new JobHost(config);
    host.RunAndBlock();
}
```

Aby uzyskać więcej informacji, zobacz [odwołanie host.json v1.x](../azure-functions/functions-host-json-v1.md#queues).

### <a name="sendgrid-binding-configuration-version-3x"></a>Konfiguracja wiązania SendGrid (wersja 3.* x*)

W tym przykładzie pokazano, jak skonfigurować powiązanie danych wyjściowych SendGrid:

```cs
static async Task Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
        b.AddSendGrid(a =>
        {
            a.FromAddress.Email = "samples@functions.com";
            a.FromAddress.Name = "Azure Functions";
        });
    });
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

Aby uzyskać więcej informacji, zobacz [SendGrid wiązania](../azure-functions/functions-bindings-sendgrid.md#hostjson-settings) artykułu.

### <a name="service-bus-trigger-configuration-version-3x"></a>Konfiguracja wyzwalacza usługi Service Bus (wersja 3.* x*)

W tym przykładzie pokazano, jak skonfigurować wyzwalacz usługi Service Bus:

```cs
static async Task Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
        b.AddServiceBus(sbOptions =>
        {
            sbOptions.MessageHandlerOptions.AutoComplete = true;
            sbOptions.MessageHandlerOptions.MaxConcurrentCalls = 16;
        });
    });
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

Aby uzyskać więcej informacji, zobacz artykuł [wiązania usługi Service Bus.](../azure-functions/functions-bindings-service-bus-output.md#hostjson-settings)

### <a name="configuration-for-other-bindings"></a>Konfiguracja dla innych powiązań

Niektóre typy wyzwalaczy i powiązań definiują własne typy konfiguracji niestandardowej. Na przykład wyzwalacz Plik umożliwia określenie ścieżki głównej do monitorowania, jak w poniższych przykładach:

#### <a name="version-3x"></a>Wersja 3. *x*

```cs
static async Task Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
        b.AddFiles(a => a.RootPath = @"c:\data\import");
    });
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

#### <a name="version-2x"></a>Wersja 2. *x*

```cs
static void Main()
{
    config = new JobHostConfiguration();
    var filesConfig = new FilesConfiguration
    {
        RootPath = @"c:\data\import"
    };
    config.UseFiles(filesConfig);
    var host = new JobHost(config);
    host.RunAndBlock();
}
```

## <a name="binding-expressions"></a>Wyrażenia powiązania

W parametrach konstruktora atrybutów można użyć wyrażeń, które rozpoznają wartości z różnych źródeł. Na przykład w poniższym kodzie `BlobTrigger` ścieżka dla atrybutu `filename`tworzy wyrażenie o nazwie . Gdy jest używany do `filename` wiązania danych wyjściowych, rozpoznaje nazwę wyzwalającego obiektu blob.

```cs
public static void CreateThumbnail(
    [BlobTrigger("sample-images/{filename}")] Stream image,
    [Blob("sample-images-sm/{filename}", FileAccess.Write)] Stream imageSmall,
    string filename,
    ILogger logger)
{
    logger.Info($"Blob trigger processing: {filename}");
    // ...
}
```

Aby uzyskać więcej informacji na temat wyrażeń wiązania, zobacz [Powiązanie wyrażeń i wzorców](../azure-functions/functions-bindings-expressions-patterns.md) w dokumentacji usługi Azure Functions.

### <a name="custom-binding-expressions"></a>Niestandardowe wyrażenia wiązania

Czasami chcesz określić nazwę kolejki, nazwę obiektu blob lub kontenera lub nazwę tabeli w kodzie, a nie jej kodowanie na twardo. Na przykład można określić nazwę kolejki `QueueTrigger` dla atrybutu w pliku konfiguracji lub zmiennej środowiskowej.

Można to zrobić, `NameResolver` przekazując obiekt `JobHostConfiguration` do obiektu. Symbole zastępcze należy uwzględnić w parametrach konstruktora atrybutów wyzwalacza lub powiązania, a `NameResolver` kod zawiera rzeczywiste wartości, które mają być używane zamiast tych symboli zastępczych. Symbole zastępcze można zidentyfikować, otaczając je procentem (%) jak pokazano poniżej:

```cs
public static void WriteLog([QueueTrigger("%logqueue%")] string logMessage)
{
    Console.WriteLine(logMessage);
}
```

Ten kod umożliwia użycie `logqueuetest` kolejki o nazwie w `logqueueprod` środowisku testowym i jednej nazwanej w środowisku produkcyjnym. Zamiast nazwy kolejki zakodowane, należy określić nazwę wpisu `appSettings` w kolekcji.

`NameResolver` Domyślnie, jeśli nie podasz niestandardowego, nie podasz ustawienia. Wartość domyślna pobiera wartości z ustawień aplikacji lub zmiennych środowiskowych.

Twoja `NameResolver` klasa pobiera nazwę `appSettings`kolejki od , jak pokazano tutaj:

```cs
public class CustomNameResolver : INameResolver
{
    public string Resolve(string name)
    {
        return ConfigurationManager.AppSettings[name].ToString();
    }
}
```

#### <a name="version-3x"></a>Wersja 3. *x*

Program rozpoznawania nazw można skonfigurować przy użyciu iniekcji zależności. Te próbki wymagają `using` następującej instrukcji:

```cs
using Microsoft.Extensions.DependencyInjection;
```

Program rozpoznawania nazw można [`ConfigureServices`] dodać, [`HostBuilder`](/dotnet/api/microsoft.extensions.hosting.hostbuilder)wywołując metodę rozszerzenia na , jak w tym przykładzie:

```cs
static async Task Main(string[] args)
{
    var builder = new HostBuilder();
    var resolver = new CustomNameResolver();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
    });
    builder.ConfigureServices(s => s.AddSingleton<INameResolver>(resolver));
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

#### <a name="version-2x"></a>Wersja 2. *x*

Przekaż `NameResolver` swoją klasę `JobHost` do obiektu, jak pokazano poniżej:

```cs
 static void Main(string[] args)
{
    JobHostConfiguration config = new JobHostConfiguration();
    config.NameResolver = new CustomNameResolver();
    JobHost host = new JobHost(config);
    host.RunAndBlock();
}
```

Usługa Azure `INameResolver` Functions implementuje, aby uzyskać wartości z ustawień aplikacji, jak pokazano w przykładzie. Korzystając bezpośrednio z sdk WebJobs, można napisać niestandardową implementację, która pobiera zastępcze wartości zastępcze z dowolnego źródła wolisz.

## <a name="binding-at-runtime"></a>Powiązanie w czasie wykonywania

Jeśli musisz wykonać jakąś pracę w funkcji przed użyciem `Blob`atrybutu wiązania, `IBinder` takiego jak `Queue`, lub `Table`, możesz użyć interfejsu.

W poniższym przykładzie przyjmuje komunikat kolejki wejściowej i tworzy nową wiadomość z tej samej zawartości w kolejce wyjściowej. Nazwa kolejki wyjściowej jest ustawiana przez kod w treści funkcji.

```cs
public static void CreateQueueMessage(
    [QueueTrigger("inputqueue")] string queueMessage,
    IBinder binder)
{
    string outputQueueName = "outputqueue" + DateTime.Now.Month.ToString();
    QueueAttribute queueAttribute = new QueueAttribute(outputQueueName);
    CloudQueue outputQueue = binder.Bind<CloudQueue>(queueAttribute);
    outputQueue.AddMessageAsync(new CloudQueueMessage(queueMessage));
}
```

Aby uzyskać więcej informacji, zobacz [Powiązanie w czasie wykonywania](../azure-functions/functions-dotnet-class-library.md#binding-at-runtime) w dokumentacji usługi Azure Functions.

## <a name="binding-reference-information"></a>Wiążące informacje referencyjne

Dokumentacja usługi Azure Functions zawiera informacje referencyjne dotyczące każdego typu powiązania. W każdym artykule referencyjnym wiązania znajdziesz następujące informacje. (Ten przykład jest oparty na kolejce magazynu).

* [Pakiety](../azure-functions/functions-bindings-storage-queue.md). Pakiet, który należy zainstalować, aby uwzględnić obsługę powiązania w projekcie zestawu SDK WebJobs.
* [Przykłady](../azure-functions/functions-bindings-storage-queue-trigger.md). Przykłady kodu. Przykład biblioteki klas języka C# dotyczy sdk WebJobs. Wystarczy pominąć `FunctionName` atrybut.
* [Atrybuty](../azure-functions/functions-bindings-storage-queue-trigger.md#attributes-and-annotations). Atrybuty, które mają być używane dla typu powiązania.
* [Konfiguracja](../azure-functions/functions-bindings-storage-queue-trigger.md#configuration). Objaśnienia właściwości atrybutu i parametry konstruktora.
* [Użycie](../azure-functions/functions-bindings-storage-queue-trigger.md#usage). Typy, które można powiązać i informacje o tym, jak działa powiązanie. Na przykład: algorytm sondowania, przetwarzanie kolejki truciątej.
  
Aby uzyskać listę artykułów referencyjnych wiązania, zobacz "Obsługiwane powiązania" w [wyzwalacza i powiązania](../azure-functions/functions-triggers-bindings.md#supported-bindings) artykułu dla usługi Azure Functions. Na tej liście powiązania HTTP, Webhooks i Event Grid są obsługiwane tylko przez usługi Azure Functions, a nie przez zestaw SDK WebJobs.

## <a name="disable-attribute"></a>Wyłączanie atrybutu 

Atrybut [`Disable`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/DisableAttribute.cs) pozwala kontrolować, czy funkcja może być wyzwalana. 

W poniższym przykładzie, `Disable_TestJob` jeśli ustawienie `1` aplikacji `True` ma wartość lub (bez uwzględniania wielkości liter), funkcja nie zostanie uruchomiony. W takim przypadku środowisko wykonawcze tworzy komunikat dziennika *Funkcja "Functions.TestJob" jest wyłączona*.

```cs
[Disable("Disable_TestJob")]
public static void TestJob([QueueTrigger("testqueue2")] string message)
{
    Console.WriteLine("Function with Disable attribute executed!");
}
```

Po zmianie wartości ustawień aplikacji w witrynie Azure portal, WebJob uruchamia się ponownie, aby odebrać nowe ustawienie.

Atrybut można zadeklarować na poziomie parametru, metody lub klasy. Nazwa ustawienia może również zawierać wyrażenia wiązania.

## <a name="timeout-attribute"></a>Atrybut Limit czasu

Atrybut [`Timeout`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/TimeoutAttribute.cs) powoduje, że funkcja ma zostać anulowana, jeśli nie zakończy się w określonym czasie. W poniższym przykładzie funkcja będzie działać przez jeden dzień bez atrybutu Timeout. Limit czasu powoduje, że funkcja ma zostać anulowana po 15 sekundach.

```cs
[Timeout("00:00:15")]
public static async Task TimeoutJob(
    [QueueTrigger("testqueue2")] string message,
    CancellationToken token,
    TextWriter log)
{
    await log.WriteLineAsync("Job starting");
    await Task.Delay(TimeSpan.FromDays(1), token);
    await log.WriteLineAsync("Job completed");
}
```

Można zastosować atrybut Timeout na poziomie klasy lub metody, a za pomocą `JobHostConfiguration.FunctionTimeout`programu Można określić globalny limit czasu za pomocą programu . Limity czasu na poziomie klasy lub poziomu metody zastępują globalne limity czasu.

## <a name="singleton-attribute"></a>Atrybut Singleton

Atrybut [`Singleton`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/SingletonAttribute.cs) zapewnia, że tylko jedno wystąpienie funkcji jest uruchamiane, nawet jeśli istnieje wiele wystąpień aplikacji sieci web hosta. Czyni to za pomocą [blokady rozproszonej](#viewing-lease-blobs).

W tym przykładzie tylko jedno `ProcessImage` wystąpienie funkcji jest uruchamiane w danym momencie:

```cs
[Singleton]
public static async Task ProcessImage([BlobTrigger("images")] Stream image)
{
     // Process the image.
}
```

### <a name="singletonmodelistener"></a>SingletonMode.Słuchacz

Niektóre wyzwalacze mają wbudowaną obsługę zarządzania współbieżnością:

* **QueueTrigger**. Ustaw `JobHostConfiguration.Queues.BatchSize` `1`na .
* **ServiceBusTrigger**. Ustaw `ServiceBusConfiguration.MessageOptions.MaxConcurrentCalls` `1`na .
* **FileTrigger**. Ustaw `FileProcessor.MaxDegreeOfParallelism` `1`na .

Można użyć tych ustawień, aby upewnić się, że funkcja działa jako singleton w jednym wystąpieniu. Aby upewnić się, że tylko jedno wystąpienie funkcji jest uruchomione, gdy aplikacja internetowa jest skalowana w`[Singleton(Mode = SingletonMode.Listener)]`poziomie do wielu wystąpień, zastosuj blokadę pojedynczego pojedynczego poziomu odbiornika na funkcji ( ). Blokady odbiornika są nabywane po uruchomieniu JobHost. Jeśli trzy skalowane w poziomie wystąpienia wszystkie rozpoczynają się w tym samym czasie, tylko jedno z wystąpień uzyskuje blokadę i uruchamia tylko jeden odbiornik.

### <a name="scope-values"></a>Wartości zakresu

Można określić *wyrażenie/wartość zakresu* na pojedynczym. Wyrażenie/wartość zapewnia, że wszystkie wykonania funkcji w określonym zakresie będą serializowane. Implementowanie bardziej szczegółowe blokowanie w ten sposób może umożliwić pewien poziom równoległości dla funkcji podczas serializacji innych wywołań, zgodnie z wymaganiami. Na przykład w poniższym kodzie wyrażenie zakresu `Region` wiąże się z wartością wiadomości przychodzącej. Gdy kolejka zawiera trzy komunikaty w regionach Wschód, Wschód i Zachód odpowiednio, wiadomości, które mają region wschód są uruchamiane szeregowo, podczas gdy wiadomość z regionu Zachód jest uruchamiany równolegle z tymi na wschodzie.

```csharp
[Singleton("{Region}")]
public static async Task ProcessWorkItem([QueueTrigger("workitems")] WorkItem workItem)
{
     // Process the work item.
}

public class WorkItem
{
     public int ID { get; set; }
     public string Region { get; set; }
     public int Category { get; set; }
     public string Description { get; set; }
}
```

### <a name="singletonscopehost"></a>SingletonScope.Host

Domyślnym zakresem blokady jest `SingletonScope.Function`, co oznacza, że zakres blokady (ścieżka dzierżawy obiektu blob) jest powiązany z w pełni kwalifikowaną nazwą funkcji. Aby zablokować funkcje, należy określić `SingletonScope.Host` i użyć nazwy identyfikatora zakresu, która jest taka sama we wszystkich funkcjach, które nie mają być uruchamiane jednocześnie. W poniższym przykładzie tylko `AddItem` `RemoveItem` jedno wystąpienie lub uruchamia się w czasie:

```csharp
[Singleton("ItemsLock", SingletonScope.Host)]
public static void AddItem([QueueTrigger("add-item")] string message)
{
     // Perform the add operation.
}

[Singleton("ItemsLock", SingletonScope.Host)]
public static void RemoveItem([QueueTrigger("remove-item")] string message)
{
     // Perform the remove operation.
}
```

### <a name="viewing-lease-blobs"></a>Wyświetlanie obiektów blob dzierżawy

Zestaw WebJobs SDK używa [dzierżaw obiektów blob platformy Azure](../storage/common/storage-concurrency.md#pessimistic-concurrency-for-blobs) w ramach okładek do implementowania blokady rozproszonej. Obiekty blob dzierżawy używane przez Singleton można znaleźć w `azure-webjobs-host` kontenerze na koncie `AzureWebJobsStorage` magazynu w ścieżce "blokuje". Na przykład ścieżka obiektu blob `ProcessImage` dzierżawy dla `locks/061851c758f04938a4426aa9ab3869c0/WebJobs.Functions.ProcessImage`pierwszego przykładu pokazanego wcześniej może być . Wszystkie ścieżki zawierają identyfikator JobHost, w tym przypadku 061851c758f04938a4426a9ab3869c0.

## <a name="async-functions"></a>Funkcje asynchronizowe

Aby uzyskać informacje dotyczące kodowania funkcji asynchroniiowych, zobacz [dokumentację usługi Azure Functions](../azure-functions/functions-dotnet-class-library.md#async).

## <a name="cancellation-tokens"></a>Tokeny anulowania

Aby uzyskać informacje dotyczące obsługi tokenów anulowania, zobacz dokumentację usługi Azure Functions dotyczącą [tokenów anulowania i bezpiecznego zamykania.](../azure-functions/functions-dotnet-class-library.md#cancellation-tokens)

## <a name="multiple-instances"></a>Wiele wystąpień

Jeśli aplikacja sieci web działa w wielu wystąpieniach, ciągły WebJob działa na każdym wystąpieniu, nasłuchiwanie wyzwalaczy i wywoływania funkcji. Różne powiązania wyzwalacza są przeznaczone do efektywnego udostępniania pracy wspólnie między wystąpieniami, dzięki czemu skalowanie w poziomie do większej liczby wystąpień pozwala na obsługę większego obciążenia.

Podczas gdy niektóre wyzwalacze mogą spowodować podwójne przetwarzanie, wyzwalacze magazynu kolejek i obiektów blob automatycznie uniemożliwiają funkcji przetwarzanie wiadomości kolejki lub obiektu blob więcej niż raz. Aby uzyskać więcej informacji, zobacz [Projektowanie dla identycznych danych wejściowych](../azure-functions/functions-idempotent.md) w dokumentacji usługi Azure Functions.

Wyzwalacz czasomierza automatycznie zapewnia, że tylko jedno wystąpienie czasomierza działa, więc nie otrzymasz więcej niż jedno wystąpienie funkcji uruchomione w danym zaplanowanym czasie.

Jeśli chcesz upewnić się, że tylko jedno wystąpienie funkcji jest uruchamiane nawet wtedy, [`Singleton`](#singleton-attribute) gdy istnieje wiele wystąpień hosta aplikacji sieci web, można użyć atrybutu.

## <a name="filters"></a>Filtry

Filtry funkcji (wersja zapoznawcza) umożliwiają dostosowanie potoku wykonywania WebJobs za pomocą własnej logiki. Filtry są podobne do [filtrów ASP.NET Core](https://docs.microsoft.com/aspnet/core/mvc/controllers/filters). Można je zaimplementować jako atrybuty deklaratywne, które są stosowane do funkcji lub klas. Aby uzyskać więcej informacji, zobacz [Filtry funkcji](https://github.com/Azure/azure-webjobs-sdk/wiki/Function-Filters).

## <a name="logging-and-monitoring"></a>Rejestrowanie i monitorowanie

Firma Microsoft zaleca platformę rejestrowania, która została opracowana dla ASP.NET. W artykule [Wprowadzenie](webjobs-sdk-get-started.md) pokazano, jak z niego korzystać. 

### <a name="log-filtering"></a>Filtrowanie dzienników

Każdy dziennik utworzony `ILogger` przez wystąpienie `Category` `Level`ma skojarzony i . [`LogLevel`](/dotnet/api/microsoft.extensions.logging.loglevel)jest wyliczeniem, a kod liczby całkowitej wskazuje względne znaczenie:

|LogLevel    |Code|
|------------|---|
|Ślad       | 0 |
|Debugowanie       | 1 |
|Informacje | 2 |
|Ostrzeżenie     | 3 |
|Błąd       | 4 |
|Krytyczny    | 5 |
|Brak        | 6 |

Można niezależnie filtrować każdą kategorię [`LogLevel`](/dotnet/api/microsoft.extensions.logging.loglevel)do określonej . Na przykład można wyświetlić wszystkie dzienniki do przetwarzania `Error` wyzwalacza obiektów blob, ale tylko i wyższe dla wszystkich innych.

#### <a name="version-3x"></a>Wersja 3. *x*

Wersja 3. *x* sdk opiera się na filtrowaniu wbudowanym w .NET Core. Klasa `LogCategories` umożliwia definiowanie kategorii dla określonych funkcji, wyzwalaczy lub użytkowników. Definiuje również filtry dla określonych stanów `Startup` `Results`hosta, takich jak i . Dzięki temu można dostosować dane wyjściowe rejestrowania. Jeśli w zdefiniowanych kategoriach nie zostanie znalezione żadne `Default` dopasowanie, filtr powróci do wartości przy podejmowaniu decyzji o przefiltrowaniu wiadomości.

`LogCategories`wymaga następującej instrukcji:

```cs
using Microsoft.Azure.WebJobs.Logging; 
```

Poniższy przykład konstruuje filtr, który domyślnie filtruje `Warning` wszystkie dzienniki na poziomie. I `Function` `results` kategorie (odpowiednik `Host.Results` w wersji 2.* x)* są filtrowane `Error` na poziomie. Filtr porównuje bieżącą kategorię ze wszystkimi `LogCategories` zarejestrowanymi poziomami w wystąpieniu i wybiera najdłuższe dopasowanie. Oznacza to, `Debug` że `Host.Triggers` poziom `Host.Triggers.Queue` `Host.Triggers.Blob`zarejestrowany dla meczów lub . Dzięki temu można kontrolować szersze kategorie bez konieczności dodawania każdego z nich.

```cs
static async Task Main(string[] args)
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
    });
    builder.ConfigureLogging(logging =>
            {
                logging.SetMinimumLevel(LogLevel.Warning);
                logging.AddFilter("Function", LogLevel.Error);
                logging.AddFilter(LogCategories.CreateFunctionCategory("MySpecificFunctionName"),
                    LogLevel.Debug);
                logging.AddFilter(LogCategories.Results, LogLevel.Error);
                logging.AddFilter("Host.Triggers", LogLevel.Debug);
            });
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

#### <a name="version-2x"></a>Wersja 2. *x*

W wersji 2. *x* sdk, należy użyć `LogCategoryFilter` klasy do sterowania filtrowania. Ma `LogCategoryFilter` `Default` właściwość o wartości `Information`początkowej , co `Information`oznacza, że wszystkie wiadomości na , `Warning`, `Error`, lub `Critical` poziomy są rejestrowane, ale wszystkie wiadomości na poziomie `Debug` lub `Trace` są filtrowane.

Podobnie `LogCategories` jak w wersji 3. *x*, `CategoryLevels` właściwość umożliwia określenie poziomów dziennika dla określonych kategorii, dzięki czemu można dostosować dane wyjściowe rejestrowania. Jeśli w słowniku `CategoryLevels` nie zostanie znalezione żadne dopasowanie, filtr powróci do `Default` wartości przy podejmowaniu decyzji o przefiltrowaniu wiadomości.

Poniższy przykład tworzy filtr, który domyślnie filtruje `Warning` wszystkie dzienniki na poziomie. Kategorie `Function` `Host.Results` i kategorie są filtrowane na `Error` poziomie. Porównuje `LogCategoryFilter` bieżącą kategorię ze `CategoryLevels` wszystkimi zarejestrowanymi i wybiera najdłuższe dopasowanie. Tak `Debug` więc poziom `Host.Triggers` zarejestrowany `Host.Triggers.Queue` `Host.Triggers.Blob`będzie pasować lub . Dzięki temu można kontrolować szersze kategorie bez konieczności dodawania każdego z nich.

```csharp
var filter = new LogCategoryFilter();
filter.DefaultLevel = LogLevel.Warning;
filter.CategoryLevels[LogCategories.Function] = LogLevel.Error;
filter.CategoryLevels[LogCategories.Results] = LogLevel.Error;
filter.CategoryLevels["Host.Triggers"] = LogLevel.Debug;

config.LoggerFactory = new LoggerFactory()
    .AddApplicationInsights(instrumentationKey, filter.Filter)
    .AddConsole(filter.Filter);
```

### <a name="custom-telemetry-for-application-insights"></a>Niestandardowe dane telemetryczne dla aplikacji Insights

Proces implementowania niestandardowych danych telemetrycznych dla [usługi Application Insights](../azure-monitor/app/app-insights-overview.md) zależy od wersji SDK. Aby dowiedzieć się, jak skonfigurować usługa Application Insights, zobacz [Dodawanie rejestrowania usługi Application Insights](webjobs-sdk-get-started.md#add-application-insights-logging).

#### <a name="version-3x"></a>Wersja 3. *x*

Ponieważ wersja 3. *x* sdk WebJobs opiera się na hoście ogólnym .NET Core, niestandardowa fabryka telemetrii nie jest już dostępna. Ale można dodać dane telemetryczne niestandardowe do potoku przy użyciu iniekcji zależności. Przykłady w tej sekcji `using` wymagają następujących instrukcji:

```cs
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.ApplicationInsights.Channel;
```

Poniższa niestandardowa [`ITelemetryInitializer`] implementacja umożliwia [`ITelemetry`](/dotnet/api/microsoft.applicationinsights.channel.itelemetry) dodanie [`TelemetryConfiguration`]własnego do domyślnego .

```cs
internal class CustomTelemetryInitializer : ITelemetryInitializer
{
    public void Initialize(ITelemetry telemetry)
    {
        // Do something with telemetry.
    }
}
```

Zadzwoń [`ConfigureServices`] do konstruktora, [`ITelemetryInitializer`] aby dodać niestandardowe do potoku.

```cs
static async Task Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
    });
    builder.ConfigureLogging((context, b) =>
    {
        // Add logging providers.
        b.AddConsole();

        // If this key exists in any config, use it to enable Application Insights.
        string appInsightsKey = context.Configuration["APPINSIGHTS_INSTRUMENTATIONKEY"];
        if (!string.IsNullOrEmpty(appInsightsKey))
        {
            // This uses the options callback to explicitly set the instrumentation key.
            b.AddApplicationInsights(o => o.InstrumentationKey = appInsightsKey);
        }
    });
    builder.ConfigureServices(services =>
        {
            services.AddSingleton<ITelemetryInitializer, CustomTelemetryInitializer>();
        });
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

Podczas [`TelemetryConfiguration`] konstruowania, wszystkie zarejestrowane typy [`ITelemetryInitializer`] są uwzględniane. Aby dowiedzieć się więcej, zobacz [Interfejs API usługi Application Insights dla niestandardowych zdarzeń i metryk](../azure-monitor/app/api-custom-events-metrics.md).

W wersji 3. *x*, nie trzeba już [`TelemetryClient`] opróżniać po zatrzymaniu hosta. System iniekcji zależności .NET Core automatycznie `ApplicationInsightsLoggerProvider`usuwa zarejestrowany [`TelemetryClient`]system , który opróżnia .

#### <a name="version-2x"></a>Wersja 2. *x*

W wersji 2. *x*, [`TelemetryClient`] utworzone wewnętrznie przez dostawcę usługi Application Insights dla [`ServerTelemetryChannel`](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/develop/src/ServerTelemetryChannel/ServerTelemetryChannel.cs)sieci WebJobs SDK używa . Gdy punkt końcowy usługi Application Insights jest niedostępny lub ogranicza przychodzące żądania, ten kanał [zapisuje żądania w systemie plików aplikacji sieci web i przesyła je ponownie później.](https://apmtips.com/blog/2015/09/03/more-telemetry-channels)

Jest [`TelemetryClient`] tworzony przez klasę, `ITelemetryClientFactory`która implementuje . Domyślnie jest to [`DefaultTelemetryClientFactory`](https://github.com/Azure/azure-webjobs-sdk/blob/dev/src/Microsoft.Azure.WebJobs.Logging.ApplicationInsights/DefaultTelemetryClientFactory.cs)plik .

Jeśli chcesz zmodyfikować dowolną część potoku usługi Application `ITelemetryClientFactory`Insights, możesz podać własną , [`TelemetryClient`]a host użyje twojej klasy do skonstruowania . Na przykład ten kod `DefaultTelemetryClientFactory` zastępuje, aby `ServerTelemetryChannel`zmodyfikować właściwość:

```csharp
private class CustomTelemetryClientFactory : DefaultTelemetryClientFactory
{
    public CustomTelemetryClientFactory(string instrumentationKey, Func<string, LogLevel, bool> filter)
        : base(instrumentationKey, new SamplingPercentageEstimatorSettings(), filter)
    {
    }

    protected override ITelemetryChannel CreateTelemetryChannel()
    {
        ServerTelemetryChannel channel = new ServerTelemetryChannel();

        // Change the default from 30 seconds to 15 seconds.
        channel.MaxTelemetryBufferDelay = TimeSpan.FromSeconds(15);

        return channel;
    }
}
```

Obiekt `SamplingPercentageEstimatorSettings` konfiguruje [adaptacyjne próbkowanie](https://docs.microsoft.com/azure/application-insights/app-insights-sampling). Oznacza to, że w niektórych scenariuszach dużej ilości usługa Applications Insights wysyła do serwera wybrany podzbiór danych telemetrycznych.

Po utworzeniu fabryki danych telemetrycznych należy przekazać ją do dostawcy rejestrowania usługi Application Insights:

```csharp
var clientFactory = new CustomTelemetryClientFactory(instrumentationKey, filter.Filter);

config.LoggerFactory = new LoggerFactory()
    .AddApplicationInsights(clientFactory);
```

## <a name="next-steps"></a><a id="nextsteps"></a>Kolejne kroki

W tym artykule podano fragmenty kodu, które pokazują, jak obsługiwać typowe scenariusze pracy z webjobs SDK. Aby uzyskać kompletne przykłady, zobacz [azure-webjobs-sdk-samples](https://github.com/Azure/azure-webjobs-sdk/tree/dev/sample/SampleHost).

["ExecutionContext"]: https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions/Extensions/Core/ExecutionContext.cs
['TelemetryClient']: /dotnet/api/microsoft.applicationinsights.telemetryclient
["Konfigurowanie usług"]: /dotnet/api/microsoft.extensions.hosting.hostinghostbuilderextensions.configureservices
["ITelemetryInitializer"]: /dotnet/api/microsoft.applicationinsights.extensibility.itelemetryinitializer
["Konfiguracja telemetryczna"]: /dotnet/api/microsoft.applicationinsights.extensibility.telemetryconfiguration
['JobHostConfiguration']: https://github.com/Azure/azure-webjobs-sdk/blob/v2.x/src/Microsoft.Azure.WebJobs.Host/JobHostConfiguration.cs
