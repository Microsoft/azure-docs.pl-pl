---
title: Śledzenie operacji niestandardowych przy użyciu zestawu Azure Application Insights .NET SDK
description: Śledzenie operacji niestandardowych przy użyciu zestawu Azure Application Insights .NET SDK
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 11/26/2019
ms.reviewer: sergkanz
ms.openlocfilehash: 42a5318325f9961483465357403089755feb130d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88933311"
---
# <a name="track-custom-operations-with-application-insights-net-sdk"></a>Śledzenie operacji niestandardowych przy użyciu zestawu SDK platformy Application Insights .NET

Zestawy SDK platformy Azure Application Insights automatycznie śledzą przychodzące żądania HTTP i wywołania do usług zależnych, takich jak żądania HTTP i zapytania SQL. Śledzenie i korelacja żądań i zależności zapewnia wgląd w całą wydajność i niezawodność wszystkich mikrousług, które łączą tę aplikację. 

Istnieje Klasa wzorców aplikacji, których nie można ogólnie obsłużyć. Odpowiednie monitorowanie takich wzorców wymaga ręcznego Instrumentacji kodu. W tym artykule opisano kilka wzorców, które mogą wymagać ręcznego instrumentacji, takich jak przetwarzanie kolejki niestandardowej i uruchamianie długotrwałych zadań w tle.

Ten dokument zawiera wskazówki dotyczące śledzenia niestandardowych operacji przy użyciu zestawu SDK Application Insights. Ta dokumentacja dotyczy:

- Application Insights dla platformy .NET (znanego również jako podstawowy zestaw SDK) w wersji 2.4 +.
- Application Insights aplikacji sieci Web (z systemem ASP.NET) w wersji 2.4 +.
- Application Insights ASP.NET Core wersja 2.1 +.

## <a name="overview"></a>Omówienie
Operacja jest logicznym elementem pracy wykonywanym przez aplikację. Ma nazwę, czas rozpoczęcia, czas trwania, wynik i kontekst wykonania, takie jak nazwa użytkownika, właściwości i wynik. Jeśli operacja A została zainicjowana przez operację B, operacja B jest ustawiana jako element nadrzędny dla elementu. Operacja może mieć tylko jeden element nadrzędny, ale może mieć wiele operacji podrzędnych. Aby uzyskać więcej informacji na temat korelacji operacji i telemetrii, zobacz [korelacja telemetrii Azure Application Insights](correlation.md).

W Application Insights .NET SDK operacja jest opisywana przez klasę abstrakcyjną [OperationTelemetry](https://github.com/microsoft/ApplicationInsights-dotnet/blob/7633ae849edc826a8547745b6bf9f3174715d4bd/BASE/src/Microsoft.ApplicationInsights/Extensibility/Implementation/OperationTelemetry.cs) i jej elementy podrzędne [RequestTelemetry](https://github.com/microsoft/ApplicationInsights-dotnet/blob/7633ae849edc826a8547745b6bf9f3174715d4bd/BASE/src/Microsoft.ApplicationInsights/DataContracts/RequestTelemetry.cs) i [DependencyTelemetry](https://github.com/microsoft/ApplicationInsights-dotnet/blob/7633ae849edc826a8547745b6bf9f3174715d4bd/BASE/src/Microsoft.ApplicationInsights/DataContracts/DependencyTelemetry.cs).

## <a name="incoming-operations-tracking"></a>Śledzenie operacji przychodzących 
Zestaw SDK sieci Web Application Insights automatycznie zbiera żądania HTTP dla aplikacji ASP.NET, które działają w potoku usług IIS i wszystkich aplikacji ASP.NET Core. Istnieją rozwiązania obsługiwane przez społeczność dla innych platform i struktur. Jeśli jednak aplikacja nie jest obsługiwana przez żadne z rozwiązań obsługiwanych przez społeczność lub standard, można instrumentować ją ręcznie.

Innym przykładem, który wymaga śledzenia niestandardowego, jest proces roboczy, który odbiera elementy z kolejki. W przypadku niektórych kolejek wywołanie dodania komunikatu do tej kolejki jest śledzone jako zależność. Jednak w przypadku operacji wysokiego poziomu opisującej przetwarzanie komunikatów nie są zbierane automatycznie.

Zobaczmy, jak te operacje mogą być śledzone.

Na wysokim poziomie zadanie jest tworzone `RequestTelemetry` i ustawia znane właściwości. Po zakończeniu operacji Śledź dane telemetryczne. Poniższy przykład ilustruje to zadanie.

### <a name="http-request-in-owin-self-hosted-app"></a>Żądanie HTTP w aplikacji samohostowanej Owin
W tym przykładzie kontekst śledzenia jest propagowany zgodnie z [protokołem HTTP dla korelacji](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md). Należy oczekiwać, że nagłówki zostały opisane w tym miejscu.

```csharp
public class ApplicationInsightsMiddleware : OwinMiddleware
{
    // you may create a new TelemetryConfiguration instance, reuse one you already have
    // or fetch the instance created by Application Insights SDK.
    private readonly TelemetryConfiguration telemetryConfiguration = TelemetryConfiguration.CreateDefault();
    private readonly TelemetryClient telemetryClient = new TelemetryClient(telemetryConfiguration);
    
    public ApplicationInsightsMiddleware(OwinMiddleware next) : base(next) {}

    public override async Task Invoke(IOwinContext context)
    {
        // Let's create and start RequestTelemetry.
        var requestTelemetry = new RequestTelemetry
        {
            Name = $"{context.Request.Method} {context.Request.Uri.GetLeftPart(UriPartial.Path)}"
        };

        // If there is a Request-Id received from the upstream service, set the telemetry context accordingly.
        if (context.Request.Headers.ContainsKey("Request-Id"))
        {
            var requestId = context.Request.Headers.Get("Request-Id");
            // Get the operation ID from the Request-Id (if you follow the HTTP Protocol for Correlation).
            requestTelemetry.Context.Operation.Id = GetOperationId(requestId);
            requestTelemetry.Context.Operation.ParentId = requestId;
        }

        // StartOperation is a helper method that allows correlation of 
        // current operations with nested operations/telemetry
        // and initializes start time and duration on telemetry items.
        var operation = telemetryClient.StartOperation(requestTelemetry);

        // Process the request.
        try
        {
            await Next.Invoke(context);
        }
        catch (Exception e)
        {
            requestTelemetry.Success = false;
            telemetryClient.TrackException(e);
            throw;
        }
        finally
        {
            // Update status code and success as appropriate.
            if (context.Response != null)
            {
                requestTelemetry.ResponseCode = context.Response.StatusCode.ToString();
                requestTelemetry.Success = context.Response.StatusCode >= 200 && context.Response.StatusCode <= 299;
            }
            else
            {
                requestTelemetry.Success = false;
            }

            // Now it's time to stop the operation (and track telemetry).
            telemetryClient.StopOperation(operation);
        }
    }
    
    public static string GetOperationId(string id)
    {
        // Returns the root ID from the '|' to the first '.' if any.
        int rootEnd = id.IndexOf('.');
        if (rootEnd < 0)
            rootEnd = id.Length;

        int rootStart = id[0] == '|' ? 1 : 0;
        return id.Substring(rootStart, rootEnd - rootStart);
    }
}
```

Protokół HTTP dla korelacji deklaruje również `Correlation-Context` nagłówek. Jest to jednak pomijane w tym miejscu dla uproszczenia.

## <a name="queue-instrumentation"></a>Instrumentacja kolejki
Chociaż istnieją [konteksty śledzenia W3C](https://www.w3.org/TR/trace-context/) i [protokół HTTP dla korelacji](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md) do przekazywania szczegółów korelacji z żądaniem http, każdy protokół kolejki musi określać, jak te same szczegóły są przekazywane wzdłuż komunikatu kolejki. Niektóre protokoły kolejek (takie jak AMQP) umożliwiają przekazywanie dodatkowych metadanych i niektórych innych (takich jak Kolejka usługi Azure Storage) Wymagaj kodowania kontekstu do ładunku komunikatów.

> [!NOTE]
> * **Śledzenie między składnikami nie jest jeszcze obsługiwane w przypadku kolejek** W przypadku protokołu HTTP, jeśli producent i konsument wysyłają dane telemetryczne do różnych zasobów Application Insights, środowisko diagnostyki transakcji i mapa aplikacji pokazują transakcje i zamapowane na zakończenie. W przypadku kolejek to nie jest jeszcze obsługiwane. 

### <a name="service-bus-queue"></a>Kolejka usługi Service Bus
Application Insights śledzi połączenia Service Bus komunikatów z nowym [klientem Microsoft Azure ServiceBus dla platformy .NET w](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus/) wersji 3.0.0 lub nowszej.
W przypadku używania [wzorca obsługi komunikatów](/dotnet/api/microsoft.azure.servicebus.queueclient.registermessagehandler) do przetwarzania komunikatów: wszystkie wywołania Service Bus wykonywane przez usługę są automatycznie śledzone i skorelowane z innymi elementami telemetrii. Zapoznaj się z [Service Bus śledzeniem klienta przy użyciu programu Microsoft Application Insights w](../../service-bus-messaging/service-bus-end-to-end-tracing.md) przypadku ręcznego przetwarzania komunikatów.

Jeśli używasz pakietu [windowsazure. ServiceBus](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) , przeczytaj dalsze następujące przykłady przedstawiają sposób śledzenia (i skorelowania) wywołań do Service Bus jako kolejka Service Bus używa protokołu AMQP, a Application Insights nie śledzi automatycznie operacji w kolejce.
Identyfikatory korelacji są przesyłane we właściwościach komunikatu.

#### <a name="enqueue"></a>Dodawania

```csharp
public async Task Enqueue(string payload)
{
    // StartOperation is a helper method that initializes the telemetry item
    // and allows correlation of this operation with its parent and children.
    var operation = telemetryClient.StartOperation<DependencyTelemetry>("enqueue " + queueName);
    
    operation.Telemetry.Type = "Azure Service Bus";
    operation.Telemetry.Data = "Enqueue " + queueName;

    var message = new BrokeredMessage(payload);
    // Service Bus queue allows the property bag to pass along with the message.
    // We will use them to pass our correlation identifiers (and other context)
    // to the consumer.
    message.Properties.Add("ParentId", operation.Telemetry.Id);
    message.Properties.Add("RootId", operation.Telemetry.Context.Operation.Id);

    try
    {
        await queue.SendAsync(message);
        
        // Set operation.Telemetry Success and ResponseCode here.
        operation.Telemetry.Success = true;
    }
    catch (Exception e)
    {
        telemetryClient.TrackException(e);
        // Set operation.Telemetry Success and ResponseCode here.
        operation.Telemetry.Success = false;
        throw;
    }
    finally
    {
        telemetryClient.StopOperation(operation);
    }
}
```

#### <a name="process"></a>Proces
```csharp
public async Task Process(BrokeredMessage message)
{
    // After the message is taken from the queue, create RequestTelemetry to track its processing.
    // It might also make sense to get the name from the message.
    RequestTelemetry requestTelemetry = new RequestTelemetry { Name = "process " + queueName };

    var rootId = message.Properties["RootId"].ToString();
    var parentId = message.Properties["ParentId"].ToString();
    // Get the operation ID from the Request-Id (if you follow the HTTP Protocol for Correlation).
    requestTelemetry.Context.Operation.Id = rootId;
    requestTelemetry.Context.Operation.ParentId = parentId;

    var operation = telemetryClient.StartOperation(requestTelemetry);

    try
    {
        await ProcessMessage();
    }
    catch (Exception e)
    {
        telemetryClient.TrackException(e);
        throw;
    }
    finally
    {
        // Update status code and success as appropriate.
        telemetryClient.StopOperation(operation);
    }
}
```

### <a name="azure-storage-queue"></a>Kolejka usługi Azure Storage
Poniższy przykład przedstawia sposób śledzenia operacji w [kolejce usługi Azure Storage](../../storage/queues/storage-dotnet-how-to-use-queues.md) i skorelowania telemetrii między producentem, klientem i usługą Azure Storage. 

Kolejka magazynu ma interfejs API protokołu HTTP. Wszystkie wywołania kolejki są śledzone przez moduł zbierający zależności Application Insights dla żądań HTTP.
Jest ona konfigurowana domyślnie w aplikacjach ASP.NET i ASP.NET Core, z innymi rodzajami aplikacji, można zapoznać się z [dokumentacją aplikacji konsolowych](./console.md)

Istnieje również możliwość skorelowania identyfikatora operacji Application Insights z IDENTYFIKATORem żądania magazynu. Aby uzyskać informacje na temat sposobu ustawiania i uzyskiwania klienta żądania magazynu oraz identyfikatora żądania serwera, zobacz [monitorowanie, diagnozowanie i rozwiązywanie problemów z usługą Azure Storage](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md#end-to-end-tracing).

#### <a name="enqueue"></a>Dodawania
Ponieważ kolejki magazynu obsługują interfejs API protokołu HTTP, wszystkie operacje z kolejką są automatycznie śledzone przez Application Insights. W wielu przypadkach ta Instrumentacja powinna być wystarczająca. Jednak aby skorelować ślady po stronie konsumenta ze śladami producenta, należy przekazać jakiś kontekst korelacji podobnie jak w protokole HTTP dla korelacji. 

Ten przykład pokazuje, jak śledzić `Enqueue` operację. Można:

 - **Skorelowanie ponownych prób (jeśli istnieją)**: wszystkie mają jeden wspólny element nadrzędny, który jest `Enqueue` operacją. W przeciwnym razie są one śledzone jako elementy podrzędne żądania przychodzącego. Jeśli kolejka zawiera wiele żądań logicznych, może być trudne do znalezienia, które wywołanie spowodowało ponowną próbę.
 - **Skorelowanie dzienników magazynu (jeśli**są one i w razie konieczności): są skorelowane z Application Insights telemetrii.

`Enqueue`Operacja jest elementem podrzędnym operacji nadrzędnej (na przykład przychodzące żądanie HTTP). Wywołanie zależności HTTP jest elementem podrzędnym `Enqueue` operacji i grandchild żądania przychodzącego:

```csharp
public async Task Enqueue(CloudQueue queue, string message)
{
    var operation = telemetryClient.StartOperation<DependencyTelemetry>("enqueue " + queue.Name);
    operation.Telemetry.Type = "Azure queue";
    operation.Telemetry.Data = "Enqueue " + queue.Name;

    // MessagePayload represents your custom message and also serializes correlation identifiers into payload.
    // For example, if you choose to pass payload serialized to JSON, it might look like
    // {'RootId' : 'some-id', 'ParentId' : '|some-id.1.2.3.', 'message' : 'your message to process'}
    var jsonPayload = JsonConvert.SerializeObject(new MessagePayload
    {
        RootId = operation.Telemetry.Context.Operation.Id,
        ParentId = operation.Telemetry.Id,
        Payload = message
    });
    
    CloudQueueMessage queueMessage = new CloudQueueMessage(jsonPayload);

    // Add operation.Telemetry.Id to the OperationContext to correlate Storage logs and Application Insights telemetry.
    OperationContext context = new OperationContext { ClientRequestID = operation.Telemetry.Id};

    try
    {
        await queue.AddMessageAsync(queueMessage, null, null, new QueueRequestOptions(), context);
    }
    catch (StorageException e)
    {
        operation.Telemetry.Properties.Add("AzureServiceRequestID", e.RequestInformation.ServiceRequestID);
        operation.Telemetry.Success = false;
        operation.Telemetry.ResultCode = e.RequestInformation.HttpStatusCode.ToString();
        telemetryClient.TrackException(e);
    }
    finally
    {
        // Update status code and success as appropriate.
        telemetryClient.StopOperation(operation);
    }
}  
```

Aby zmniejszyć ilość danych telemetrycznych raportowanych przez aplikację lub jeśli nie chcesz śledzić `Enqueue` operacji z innych powodów, użyj `Activity` interfejsu API bezpośrednio:

- Utwórz (i Rozpocznij) nowy `Activity` zamiast uruchamiania Application Insights operacji. *Nie* trzeba przypisywać do niego żadnych właściwości, z wyjątkiem nazwy operacji.
- Serializacja `yourActivity.Id` do ładunku komunikatu zamiast `operation.Telemetry.Id` . Można również użyć `Activity.Current.Id` .


#### <a name="dequeue"></a>Z kolejki
Podobnie `Enqueue` , rzeczywiste żądanie HTTP do kolejki magazynu jest automatycznie śledzone przez Application Insights. Jednakże operacja powinna być `Enqueue` wykonywana w kontekście nadrzędnym, takim jak kontekst żądania przychodzącego. Application Insights zestawy SDK automatycznie skorelowane takie operacje (i ich części protokołu HTTP) z żądaniem nadrzędnym i inną telemetrię raportowaną w tym samym zakresie.

Operacja jest w tej samej `Dequeue` lewie. Zestaw Application Insights SDK automatycznie śledzi żądania HTTP. Jednak nie wie kontekstu korelacji do momentu przeanalizowania komunikatu. Nie można skorelować żądania HTTP, aby uzyskać komunikat z resztą danych telemetrycznych, zwłaszcza w przypadku otrzymania więcej niż jednego komunikatu.

```csharp
public async Task<MessagePayload> Dequeue(CloudQueue queue)
{
    var operation = telemetryClient.StartOperation<DependencyTelemetry>("dequeue " + queue.Name);
    operation.Telemetry.Type = "Azure queue";
    operation.Telemetry.Data = "Dequeue " + queue.Name;
    
    try
    {
        var message = await queue.GetMessageAsync();
    }
    catch (StorageException e)
    {
        operation.telemetry.Properties.Add("AzureServiceRequestID", e.RequestInformation.ServiceRequestID);
        operation.telemetry.Success = false;
        operation.telemetry.ResultCode = e.RequestInformation.HttpStatusCode.ToString();
        telemetryClient.TrackException(e);
    }
    finally
    {
        // Update status code and success as appropriate.
        telemetryClient.StopOperation(operation);
    }

    return null;
}
```

#### <a name="process"></a>Proces

W poniższym przykładzie komunikat przychodzący jest śledzony w sposób podobny do przychodzącego żądania HTTP:

```csharp
public async Task Process(MessagePayload message)
{
    // After the message is dequeued from the queue, create RequestTelemetry to track its processing.
    RequestTelemetry requestTelemetry = new RequestTelemetry { Name = "process " + queueName };
    
    // It might also make sense to get the name from the message.
    requestTelemetry.Context.Operation.Id = message.RootId;
    requestTelemetry.Context.Operation.ParentId = message.ParentId;

    var operation = telemetryClient.StartOperation(requestTelemetry);

    try
    {
        await ProcessMessage();
    }
    catch (Exception e)
    {
        telemetryClient.TrackException(e);
        throw;
    }
    finally
    {
        // Update status code and success as appropriate.
        telemetryClient.StopOperation(operation);
    }
}
```

Podobnie inne operacje w kolejce mogą być Instrumentacją. Operację wglądu należy przeprowadzić w podobny sposób, jak w przypadku operacji usuwania z kolejki. Operacje zarządzania kolejką instrumentacji nie są konieczne. Application Insights śledzi operacje, takie jak HTTP, i w większości przypadków wystarczą.

W przypadku usuwania komunikatów z Instrumentacji upewnij się, że ustawisz identyfikatory operacji (korelacji). Alternatywnie możesz użyć `Activity` interfejsu API. Następnie nie musisz ustawiać identyfikatorów operacji na elementach telemetrii, ponieważ Application Insights SDK:

- Utwórz nowy `Activity` po uzyskaniu elementu z kolejki.
- Służy `Activity.SetParentId(message.ParentId)` do skorelowania dzienników odbiorców i producentów.
- Uruchom `Activity` .
- Śledź operacje usunięcia z kolejki, procesu i usuwania przy użyciu `Start/StopOperation` pomocników. Zrób to w tym samym przepływie kontroli asynchronicznej (kontekst wykonywania). W ten sposób są one skorelowane prawidłowo.
- Zatrzymaj `Activity` .
- Użyj `Start/StopOperation` lub ręcznie Wywołaj dane `Track` telemetryczne.

### <a name="dependency-types"></a>Typy zależności

Application Insights używa typu zależności do dostosowywania środowiska interfejsu użytkownika. W przypadku kolejek rozpoznaje następujące typy `DependencyTelemetry` , które zwiększają [możliwości diagnostyki transakcji](./transaction-diagnostics.md):
- `Azure queue` w przypadku kolejek usługi Azure Storage
- `Azure Event Hubs` dla usługi Azure Event Hubs
- `Azure Service Bus` dla Azure Service Bus

### <a name="batch-processing"></a>Przetwarzanie wsadowe
W przypadku niektórych kolejek można usunąć z kolejki wiele komunikatów z jednym żądaniem. Przetwarzanie takich komunikatów jest uznawane za niezależne i należy do różnych operacji logicznych. Nie jest możliwe skorelowanie `Dequeue` operacji z przetworzonym komunikatem.

Każdy komunikat powinien być przetwarzany we własnym przepływie kontroli asynchronicznej. Aby uzyskać więcej informacji, zobacz sekcję [Śledzenie zależności wychodzących](#outgoing-dependencies-tracking) .

## <a name="long-running-background-tasks"></a>Długotrwałe zadania w tle

Niektóre aplikacje uruchamiają długotrwałe operacje, które mogą być spowodowane żądaniami użytkowników. W perspektywie śledzenie/Instrumentacja nie różni się od programu Request lub Instrumentacji zależności: 

```csharp
async Task BackgroundTask()
{
    var operation = telemetryClient.StartOperation<DependencyTelemetry>(taskName);
    operation.Telemetry.Type = "Background";
    try
    {
        int progress = 0;
        while (progress < 100)
        {
            // Process the task.
            telemetryClient.TrackTrace($"done {progress++}%");
        }
        // Update status code and success as appropriate.
    }
    catch (Exception e)
    {
        telemetryClient.TrackException(e);
        // Update status code and success as appropriate.
        throw;
    }
    finally
    {
        telemetryClient.StopOperation(operation);
    }
}
```

W tym przykładzie `telemetryClient.StartOperation` tworzy `DependencyTelemetry` i wypełnia kontekst korelacji. Załóżmy, że masz operację nadrzędną, która została utworzona przez żądania przychodzące, które zaplanowali operację. Tak długo `BackgroundTask` , jak zaczyna się w tym samym przepływie kontroli asynchronicznej jako żądanie przychodzące, jest skorelowane z tą operacją nadrzędną. `BackgroundTask` wszystkie zagnieżdżone elementy telemetrii są automatycznie skorelowane z żądaniem, które je spowodowało, nawet po zakończeniu żądania.

Gdy zadanie zaczyna się od wątku w tle, które nie ma `Activity` skojarzonej operacji (), `BackgroundTask` nie ma żadnego elementu nadrzędnego. Może jednak mieć zagnieżdżone operacje. Wszystkie elementy telemetrii zgłoszone przez zadanie są skorelowane z elementem `DependencyTelemetry` utworzonym w `BackgroundTask` .

## <a name="outgoing-dependencies-tracking"></a>Śledzenie zależności wychodzących
Można śledzić własny rodzaj zależności lub operację, która nie jest obsługiwana przez Application Insights.

`Enqueue`Metoda w kolejce Service Bus lub Kolejka magazynu może obsłużyć jako przykłady takich niestandardowych śledzenia.

Ogólnym podejściem do niestandardowego śledzenia zależności jest:

- Wywołaj `TelemetryClient.StartOperation` metodę (rozszerzenie), która wypełnia `DependencyTelemetry` właściwości, które są zbędne dla korelacji i inne właściwości (Sygnatura czasowa rozpoczęcia, czas trwania).
- Ustaw inne niestandardowe właściwości na `DependencyTelemetry` , takie jak nazwa i dowolny inny kontekst, którego potrzebujesz.
- Wykonaj wywołanie zależności i poczekaj na jego zakończenie.
- Zatrzymaj operację `StopOperation` po zakończeniu operacji.
- Obsługa wyjątków.

```csharp
public async Task RunMyTaskAsync()
{
    using (var operation = telemetryClient.StartOperation<DependencyTelemetry>("task 1"))
    {
        try 
        {
            var myTask = await StartMyTaskAsync();
            // Update status code and success as appropriate.
        }
        catch(...) 
        {
            // Update status code and success as appropriate.
        }
    }
}
```

Operacja usuwania powoduje zatrzymanie operacji, więc można ją wykonać, a nie wywołując `StopOperation` .

*Ostrzeżenie*: w niektórych przypadkach wyjątek [niedozwolony](/dotnet/csharp/language-reference/keywords/try-finally) może `finally` nie być wywoływany, więc operacje mogą nie być śledzone.

### <a name="parallel-operations-processing-and-tracking"></a>Równoległe przetwarzanie operacji i śledzenie

`StopOperation` powoduje zatrzymanie tylko uruchomionej operacji. Jeśli bieżąca uruchomiona operacja nie jest zgodna z tą, która ma zostać zatrzymana, `StopOperation` nic nie robi. Taka sytuacja może wystąpić, jeśli uruchamiasz wiele operacji równolegle w tym samym kontekście wykonywania:

```csharp
var firstOperation = telemetryClient.StartOperation<DependencyTelemetry>("task 1");
var firstTask = RunMyTaskAsync();

var secondOperation = telemetryClient.StartOperation<DependencyTelemetry>("task 2");
var secondTask = RunMyTaskAsync();

await firstTask;

// FAILURE!!! This will do nothing and will not report telemetry for the first operation
// as currently secondOperation is active.
telemetryClient.StopOperation(firstOperation); 

await secondTask;
```

Upewnij się, że zawsze `StartOperation` są wywoływane i przetwarzane operacje w tej samej metodzie **asynchronicznej** w celu izolowania działających równolegle operacji. Jeśli operacja jest synchroniczna (lub nie Async), Zawijaj proces i śledź przy użyciu `Task.Run` :

```csharp
public void RunMyTask(string name)
{
    using (var operation = telemetryClient.StartOperation<DependencyTelemetry>(name))
    {
        Process();
        // Update status code and success as appropriate.
    }
}

public async Task RunAllTasks()
{
    var task1 = Task.Run(() => RunMyTask("task 1"));
    var task2 = Task.Run(() => RunMyTask("task 2"));
    
    await Task.WhenAll(task1, task2);
}
```

## <a name="applicationinsights-operations-vs-systemdiagnosticsactivity"></a>ApplicationInsights Operations vs system. Diagnostics. Activity
`System.Diagnostics.Activity` reprezentuje kontekst śledzenia rozproszonego i jest używany przez struktury i biblioteki do tworzenia i propagowania kontekstu wewnątrz i na zewnątrz procesu i skorelowania elementów telemetrycznych. Działanie działa razem z `System.Diagnostics.DiagnosticSource` — mechanizm powiadomień między strukturą/biblioteką w celu powiadamiania o interesujących zdarzeniach (żądania przychodzące lub wychodzące, wyjątki itp.).

Działania to obywatele pierwszej klasy w Application Insights i automatyczna zależność, a kolekcja żądań jest w dużym stopniu oparta na `DiagnosticSource` zdarzeniach. W przypadku utworzenia działania w aplikacji — nie spowoduje to utworzenia telemetrii Application Insights. Application Insights musi otrzymywać zdarzenia DiagnosticSource i znać nazwy zdarzeń i ładunki, aby przetłumaczyć aktywność na telemetrię.

Każda operacja Application Insights (żądanie lub zależność) obejmuje `Activity` — gdy `StartOperation` jest wywoływana, tworzy działanie poniżej. `StartOperation` jest zalecanym sposobem ręcznego śledzenia żądań lub telemetrii zależności i zapewnienia, że wszystkie elementy są skorelowane.

## <a name="next-steps"></a>Następne kroki

- Poznaj podstawy [korelacji telemetrii](correlation.md) w Application Insights.
- Sprawdź, jak skorelowane dane mają wpływ na [środowisko diagnostyki transakcji](./transaction-diagnostics.md) i [mapę aplikacji](./app-map.md).
- Zobacz [model danych](./data-model.md) dla typów Application Insights i modelu danych.
- Zgłoś niestandardowe [zdarzenia i metryki](./api-custom-events-metrics.md) do Application Insights.
- Zapoznaj się z [konfiguracją](configuration-with-applicationinsights-config.md#telemetry-initializers-aspnet) standardową dla kolekcji właściwości kontekstu.
- Sprawdź [Przewodnik użytkownika System. Diagnostics. Activity](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) , aby zobaczyć, jak skorelować dane telemetryczne.

