---
title: Azure Service Bus kompleksowe śledzenie i Diagnostyka | Microsoft Docs
description: Przegląd Service Bus diagnostyki klienta i kompleksowego śledzenia (klient przez wszystkie usługi, które są związane z przetwarzaniem).
ms.topic: article
ms.date: 02/03/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: 19b284aceb83fbbc2bcf662b2b58941e6a5b36f9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99539217"
---
# <a name="distributed-tracing-and-correlation-through-service-bus-messaging"></a>Śledzenie rozproszone i korelacja za poorednictwem Service Bus Messaging

Jednym z typowych problemów związanych z programowaniem mikrousług jest możliwość śledzenia operacji klienta za pośrednictwem wszystkich usług, które są związane z przetwarzaniem. Jest to przydatne w przypadku debugowania, analizy wydajności, testowania A/B i innych typowych scenariuszy diagnostyki.
Jedna z części tego problemu śledzi elementy logiczne pracy. Obejmuje to wynik przetwarzania komunikatów i opóźnienia oraz wywołania zależności zewnętrznych. Inna część to korelacja tych zdarzeń diagnostycznych poza granicami procesów.

Gdy producent wysyła komunikat za pomocą kolejki, zwykle odbywa się to w zakresie innej operacji logicznej inicjowanej przez innego klienta lub usługę. Ta sama operacja jest kontynuowana przez odbiorcę po odebraniu komunikatu. Zarówno producent, jak i konsument (oraz inne usługi, które przetwarzają operację), najprawdopodobniej emitują zdarzenia telemetrii do śledzenia przepływu operacji i wyniku. W celu skorelowania takich zdarzeń i operacji śledzenia, każda usługa, która raportuje dane telemetryczne, ma sygnaturę każdego zdarzenia z kontekstem śledzenia.

Microsoft Azure Service Bus Messaging ma zdefiniowane właściwości ładunku, których producenci i konsumenci powinni używać do przekazywania takiego kontekstu śledzenia.
Protokół jest oparty na [protokole korelacji http](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md).

# <a name="azuremessagingservicebus-sdk-latest"></a>[Azure. Messaging. ServiceBus SDK (Najnowsza wersja)](#tab/net-standard-sdk-2)
| Nazwa właściwości        | Opis                                                 |
|----------------------|-------------------------------------------------------------|
|  Diagnostic-Id       | Unikatowy identyfikator wywołania zewnętrznego od producenta do kolejki. Zapoznaj się z [identyfikatorem żądania w protokole HTTP](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md#request-id) , aby uzyskać racjonalne informacje, zagadnienia i format |

## <a name="service-bus-net-client-autotracing"></a>Service Bus autośledzenie klienta platformy .NET
`ServiceBusProcessor`Klasa [klienta usługi Azure Messaging Service Bus dla platformy .NET](/dotnet/api/azure.messaging.servicebus.servicebusprocessor) oferuje śledzenie punktów instrumentacji, które mogą być podłączane przez systemy śledzenia lub fragment kodu klienta. Instrumentacja umożliwia śledzenie wszystkich wywołań usługi Service Bus Messaging po stronie klienta. Jeśli przetwarzanie komunikatów odbywa się przy użyciu [ `ProcessMessageAsync` programu `ServiceBusProcessor` ](/dotnet/api/azure.messaging.servicebus.servicebusprocessor.processmessageasync) (wzorzec obsługi komunikatów), przetwarzanie wiadomości jest również Instrumentacją.

### <a name="tracking-with-azure-application-insights"></a>Śledzenie za pomocą usługi Azure Application Insights

[Firma Microsoft Application Insights](https://azure.microsoft.com/services/application-insights/) oferuje bogate możliwości monitorowania wydajności, w tym żądanie AUTOMAGIC i śledzenie zależności.

W zależności od typu projektu Zainstaluj Application Insights SDK:
- [ASP.NET](../azure-monitor/app/asp-net.md) — zainstaluj wersję 2,5-beta2 lub nowszą
- [ASP.NET Core](../azure-monitor/app/asp-net-core.md) — Zainstaluj wersję 2.2.0-beta2 lub nowszą.
Te linki zawierają szczegółowe informacje dotyczące instalowania zestawu SDK, tworzenia zasobów i konfigurowania zestawu SDK (w razie konieczności). W przypadku aplikacji non-ASP.NET zapoznaj się z artykułem [Application Insights platformy Azure dla aplikacji konsoli](../azure-monitor/app/console.md) .

Jeśli używasz [ `ProcessMessageAsync` programu `ServiceBusProcessor` ](/dotnet/api/azure.messaging.servicebus.servicebusprocessor.processmessageasync) (wzorca obsługi komunikatów) do przetwarzania komunikatów, przetwarzanie wiadomości jest również Instrumentacją. Wszystkie wywołania Service Bus wykonywane przez usługę są automatycznie śledzone i skorelowane z innymi elementami telemetrii. W przeciwnym razie zapoznaj się z poniższym przykładem śledzenia ręcznego przetwarzania komunikatów.

#### <a name="trace-message-processing"></a>Przetwarzanie komunikatów śledzenia

```csharp
async Task ProcessAsync(ProcessMessageEventArgs args)
{
    ServiceBusReceivedMessage message = args.Message;
    if (message.ApplicationProperties.TryGetValue("Diagnostic-Id", out var objectId) && objectId is string diagnosticId)
    {
        var activity = new Activity("ServiceBusProcessor.ProcessMessage");
        activity.SetParentId(diagnosticId);
        // If you're using Microsoft.ApplicationInsights package version 2.6-beta or higher, you should call StartOperation<RequestTelemetry>(activity) instead
        using (var operation = telemetryClient.StartOperation<RequestTelemetry>("Process", activity.RootId, activity.ParentId))
        {
            telemetryClient.TrackTrace("Received message");
            try 
            {
            // process message
            }
            catch (Exception ex)
            {
                telemetryClient.TrackException(ex);
                operation.Telemetry.Success = false;
                throw;
            }

            telemetryClient.TrackTrace("Done");
        }
    }
}
```

W tym przykładzie dane telemetryczne żądania są raportowane dla każdego przetworzonego komunikatu, który ma sygnaturę czasową, czas trwania i wynik (powodzenie). Dane telemetryczne zawierają również zestaw właściwości korelacji. Zagnieżdżone dane śledzenia i wyjątki zgłoszone podczas przetwarzania komunikatu są również sygnaturą z właściwościami korelacji reprezentującymi je jako elementy podrzędne elementu `RequestTelemetry` .

W przypadku tworzenia wywołań do obsługiwanych składników zewnętrznych podczas przetwarzania komunikatów są one również automatycznie śledzone i skorelowane. Zapoznaj się z tematem [śledzenie operacji niestandardowych przy użyciu zestawu SDK platformy Application Insights .NET](../azure-monitor/app/custom-operations-tracking.md) do ręcznego śledzenia i korelacji.

Jeśli korzystasz z dowolnego kodu zewnętrznego oprócz zestawu SDK Application Insights, prawdopodobnie zobaczysz dłuższy **czas trwania** podczas wyświetlania dzienników Application Insights. 

![Dłuższy czas trwania w dzienniku Application Insights](./media/service-bus-end-to-end-tracing/longer-duration.png)

Nie oznacza to, że Odebrano komunikat. W tym scenariuszu wiadomość została już odebrana od momentu, gdy wiadomość zostanie przeniesiona jako parametr do kodu zestawu SDK. Natomiast tag **name** w dziennikach usługi App Insights (**proces**) wskazuje, że komunikat jest teraz przetwarzany przez zewnętrzny kod przetwarzania zdarzeń. Ten problem nie jest powiązany z platformą Azure. Zamiast tego metryki odnoszą się do wydajności kodu zewnętrznego, dzięki czemu komunikat został już odebrany z Service Bus. 

### <a name="tracking-without-tracing-system"></a>Śledzenie bez systemu śledzenia
Na wypadek, gdy system śledzenia nie obsługuje automatycznego śledzenia wywołań Service Bus, może zajrzeć do dodawania takiego wsparcia do systemu śledzenia lub do aplikacji. W tej sekcji opisano zdarzenia diagnostyczne wysyłane przez program Service Bus klienta programu .NET.  

Klient platformy .NET Service Bus jest Instrumentacją przy użyciu elementów podstawowych śledzenia .NET [System. Diagnostics. Activity](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) i [System. Diagnostics. DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md).

`Activity` służy jako kontekst śledzenia, gdy `DiagnosticSource` jest mechanizmem powiadamiania. 

Jeśli nie ma żadnych odbiorników dla zdarzeń DiagnosticSource, Instrumentacja jest wyłączona, utrzymując koszty Instrumentacji. DiagnosticSource daje wszystkie sterowanie odbiornikiem:
- odbiornik steruje źródłami i zdarzeniami, do których nasłuchuje
- szybkość zdarzeń i próbkowanie kontrolek odbiornika
- zdarzenia są wysyłane z ładunkiem, który zapewnia pełen kontekst, dzięki czemu można uzyskiwać dostęp do obiektu komunikatu i modyfikować go podczas zdarzenia.

Przed kontynuowaniem implementacji zapoznaj się z [podręcznikiem użytkownika DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md) .

Utwórzmy odbiornik dla zdarzeń Service Bus w aplikacji ASP.NET Core, która zapisuje dzienniki przy użyciu Microsoft. Extension. rejestratora.
Używa ona biblioteki [System. Reactive. Core](https://www.nuget.org/packages/System.Reactive.Core) , aby subskrybować DiagnosticSource (jest to również łatwe subskrybowanie DiagnosticSource bez użycia)

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory factory, IApplicationLifetime applicationLifetime)
{
    // configuration...

    var serviceBusLogger = factory.CreateLogger("Azure.Messaging.ServiceBus");

    IDisposable innerSubscription = null;
    IDisposable outerSubscription = DiagnosticListener.AllListeners.Subscribe(delegate (DiagnosticListener listener)
    {
        // subscribe to the Service Bus DiagnosticSource
        if (listener.Name == "Azure.Messaging.ServiceBus")
        {
            // receive event from Service Bus DiagnosticSource
            innerSubscription = listener.Subscribe(delegate (KeyValuePair<string, object> evnt)
            {
                // Log operation details once it's done
                if (evnt.Key.EndsWith("Stop"))
                {
                    Activity currentActivity = Activity.Current;
                    serviceBusLogger.LogInformation($"Operation {currentActivity.OperationName} is finished, Duration={currentActivity.Duration}, Id={currentActivity.Id}, StartTime={currentActivity.StartTimeUtc}");
                }
            });
        }
    });

    applicationLifetime.ApplicationStopping.Register(() =>
    {
        outerSubscription?.Dispose();
        innerSubscription?.Dispose();
    });
}
```

W tym przykładzie, odbiornik rejestruje czas trwania, wynik, unikatowy identyfikator i godzinę rozpoczęcia dla każdej operacji Service Bus.

### <a name="events"></a>Zdarzenia
Wszystkie zdarzenia będą miały następujące właściwości, które są zgodne ze specyfikacją Open telemetrii: https://github.com/open-telemetry/opentelemetry-specification/blob/master/specification/trace/api.md .

- `message_bus.destination` — ścieżka kolejki/tematu/subskrypcji
- `peer.address` — w pełni kwalifikowana przestrzeń nazw
- `kind` — Producent, konsument lub klient. Producent jest używany podczas wysyłania wiadomości, odbiorcy po odebraniu i klienta podczas rozliczania.
- `component` – `servicebus`

Wszystkie zdarzenia mają również właściwości "Entity" i "Endpoint", które zostały pominięte w poniższej tabeli
  * `string Entity` --Nazwa jednostki (kolejki, tematu itd.)
  * `Uri Endpoint` -Service Bus adres URL punktu końcowego

### <a name="instrumented-operations"></a>Operacje Instrumentacji
Oto pełna lista operacji Instrumentacji:

| Nazwa operacji | Śledzony interfejs API |
| -------------- | ----------- | 
| ServiceBusSender. Send | ServiceBusSender.SendMessageAsync<br/>ServiceBusSender.SendMessagesAsync |
| ServiceBusSender. Schedule | ServiceBusSender.ScheduleMessageAsync<br/>ServiceBusSender.ScheduleMessagesAsync | 
| ServiceBusSender. Cancel | ServiceBusSender.CancelScheduledMessageAsync<br/>ServiceBusSender.CancelScheduledMessagesAsync |
| ServiceBusReceiver. Receive | ServiceBusReceiver.ReceiveMessageAsync<br/>ServiceBusReceiver.ReceiveMessagesAsync |
| ServiceBusReceiver.ReceiveDeferred | ServiceBusReceiver.ReceiveDeferredMessagesAsync |
| ServiceBusReceiver. Peek | ServiceBusReceiver.PeekMessageAsync<br/>ServiceBusReceiver.PeekMessagesAsync |
| ServiceBusReceiver. Abandon | ServiceBusReceiver.AbandonMessagesAsync |
| ServiceBusReceiver. Complete | ServiceBusReceiver.CompleteMessagesAsync |
| ServiceBusReceiver. utraconych wiadomości | ServiceBusReceiver.DeadLetterMessagesAsync |
| ServiceBusReceiver. Odłóż |  ServiceBusReceiver.DeferMessagesAsync |
| ServiceBusReceiver.RenewMessageLock | ServiceBusReceiver.RenewMessageLockAsync |
| ServiceBusSessionReceiver.RenewSessionLock | ServiceBusSessionReceiver.RenewSessionLockAsync |
| ServiceBusSessionReceiver.GetSessionState | ServiceBusSessionReceiver.GetSessionStateAsync |
| ServiceBusSessionReceiver.SetSessionState | ServiceBusSessionReceiver.SetSessionStateAsync |
| ServiceBusProcessor.ProcessMessage | Wywołanie zwrotne procesora ustawione na ServiceBusProcessor. Właściwość ProcessMessageAsync |
| ServiceBusSessionProcessor.ProcessSessionMessage | Wywołanie zwrotne procesora ustawione na ServiceBusSessionProcessor. Właściwość ProcessMessageAsync |

### <a name="filtering-and-sampling"></a>Filtrowanie i próbkowanie

W niektórych przypadkach wskazane jest zarejestrowanie tylko części zdarzeń, aby zmniejszyć obciążenie związane z wydajnością lub użycie magazynu. Można rejestrować tylko zdarzenia "Stop" (jak w powyższym przykładzie) lub próbkować procent zdarzeń. 
`DiagnosticSource` Określ sposób osiągnięcia tego celu z `IsEnabled` predykatem. Aby uzyskać więcej informacji, zobacz [filtrowanie na podstawie kontekstu w DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md#context-based-filtering).

`IsEnabled` może być wywoływana wiele razy dla jednej operacji, aby zminimalizować wpływ na wydajność.

`IsEnabled` jest wywoływana w następującej kolejności:

1. `IsEnabled(<OperationName>, string entity, null)` na przykład `IsEnabled("ServiceBusSender.Send", "MyQueue1")` . Uwaga nie ma na końcu elementu "Start" ani "Stop". Służy do filtrowania określonych operacji lub kolejek. Jeśli metoda wywołania zwrotnego zwróci metodę `false` , zdarzenia dla operacji nie są wysyłane.

   * W przypadku operacji "Process" i "ProcessSession" otrzymasz również `IsEnabled(<OperationName>, string entity, Activity activity)` wywołanie zwrotne. Służy do filtrowania zdarzeń na podstawie `activity.Id` Właściwości tagów lub.
  
2. `IsEnabled(<OperationName>.Start)` na przykład `IsEnabled("ServiceBusSender.Send.Start")` . Sprawdza, czy zdarzenie "Start" powinno być wyzwalane. Wynik dotyczy tylko zdarzenia "Start", ale dalsze Instrumentacja nie jest od niego zależne.

Nie ma `IsEnabled` dla zdarzenia "Stop".

Jeśli wynikiem operacji jest wyjątek, `IsEnabled("ServiceBusSender.Send.Exception")` jest wywoływana. Można zasubskrybować tylko zdarzenia "Exception" i uniemożliwić resztę Instrumentacji. W takim przypadku nadal trzeba obsługiwać takie wyjątki. Ponieważ inne Instrumentacja jest wyłączona, nie należy oczekiwać kontekstu śledzenia do przepływu z komunikatami od konsumenta do producenta.

Można użyć `IsEnabled` również strategii próbkowania. Próbkowanie oparte na `Activity.Id` lub `Activity.RootId` zapewnia spójne próbkowanie na wszystkich oponach (o ile jest propagowane przez system śledzenia lub przez własny kod).

W obecności wielu `DiagnosticSource` odbiorników dla tego samego źródła wystarczy tylko jeden odbiornik do zaakceptowania zdarzenia, dlatego nie ma żadnej gwarancji, która `IsEnabled` jest wywoływana.



# <a name="microsoftazureservicebus-sdk"></a>[Zestaw SDK Microsoft. Azure. ServiceBus](#tab/net-standard-sdk)

| Nazwa właściwości        | Opis                                                 |
|----------------------|-------------------------------------------------------------|
|  Diagnostic-Id       | Unikatowy identyfikator wywołania zewnętrznego od producenta do kolejki. Zapoznaj się z [identyfikatorem żądania w protokole HTTP](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md#request-id) , aby uzyskać racjonalne informacje, zagadnienia i format |
|  Correlation-Context | Kontekst operacji, który jest propagowany dla wszystkich usług, które są objęte przetwarzaniem operacji. Aby uzyskać więcej informacji, zobacz temat [korelacja w protokole HTTP](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md#correlation-context) |

## <a name="service-bus-net-client-autotracing"></a>Service Bus autośledzenie klienta platformy .NET

Począwszy od wersji 3.0.0 [Microsoft Azure ServiceBus Client for .NET](/dotnet/api/microsoft.azure.servicebus.queueclient) oferuje punkty Instrumentacji śledzenia, które mogą być podłączane przez systemy śledzenia lub fragment kodu klienta.
Instrumentacja umożliwia śledzenie wszystkich wywołań usługi Service Bus Messaging po stronie klienta. Jeśli przetwarzanie komunikatów odbywa się przy użyciu [wzorca obsługi komunikatów](/dotnet/api/microsoft.azure.servicebus.queueclient.registermessagehandler), przetwarzanie komunikatów jest również instrumentem

### <a name="tracking-with-azure-application-insights"></a>Śledzenie za pomocą usługi Azure Application Insights

[Firma Microsoft Application Insights](https://azure.microsoft.com/services/application-insights/) oferuje bogate możliwości monitorowania wydajności, w tym żądanie AUTOMAGIC i śledzenie zależności.

W zależności od typu projektu Zainstaluj Application Insights SDK:
- [ASP.NET](../azure-monitor/app/asp-net.md) — zainstaluj wersję 2,5-beta2 lub nowszą
- [ASP.NET Core](../azure-monitor/app/asp-net-core.md) — Zainstaluj wersję 2.2.0-beta2 lub nowszą.
Te linki zawierają szczegółowe informacje dotyczące instalowania zestawu SDK, tworzenia zasobów i konfigurowania zestawu SDK (w razie konieczności). W przypadku aplikacji non-ASP.NET zapoznaj się z artykułem [Application Insights platformy Azure dla aplikacji konsoli](../azure-monitor/app/console.md) .

Jeśli używasz [wzorca obsługi komunikatów](/dotnet/api/microsoft.azure.servicebus.queueclient.registermessagehandler) do przetwarzania komunikatów, wszystko jest gotowe: wszystkie Service Bus wywołania wykonywane przez usługę są automatycznie śledzone i skorelowane z innymi elementami telemetrii. W przeciwnym razie zapoznaj się z poniższym przykładem śledzenia ręcznego przetwarzania komunikatów.

#### <a name="trace-message-processing"></a>Przetwarzanie komunikatów śledzenia

```csharp
private readonly TelemetryClient telemetryClient;

async Task ProcessAsync(Message message)
{
    var activity = message.ExtractActivity();
    
    // If you're using Microsoft.ApplicationInsights package version 2.6-beta or higher, you should call StartOperation<RequestTelemetry>(activity) instead
    using (var operation = telemetryClient.StartOperation<RequestTelemetry>("Process", activity.RootId, activity.ParentId))
    {
        telemetryClient.TrackTrace("Received message");
        try 
        {
           // process message
        }
        catch (Exception ex)
        {
             telemetryClient.TrackException(ex);
             operation.Telemetry.Success = false;
             throw;
        }

        telemetryClient.TrackTrace("Done");
   }
}
```

W tym przykładzie `RequestTelemetry` jest raportowany dla każdego przetworzonego komunikatu, który ma sygnaturę czasową, czas trwania i wynik (powodzenie). Dane telemetryczne zawierają również zestaw właściwości korelacji.
Zagnieżdżone dane śledzenia i wyjątki zgłoszone podczas przetwarzania komunikatu są również sygnaturą z właściwościami korelacji reprezentującymi je jako elementy podrzędne elementu `RequestTelemetry` .

W przypadku tworzenia wywołań do obsługiwanych składników zewnętrznych podczas przetwarzania komunikatów są one również automatycznie śledzone i skorelowane. Zapoznaj się z tematem [śledzenie operacji niestandardowych przy użyciu zestawu SDK platformy Application Insights .NET](../azure-monitor/app/custom-operations-tracking.md) do ręcznego śledzenia i korelacji.

Jeśli korzystasz z dowolnego kodu zewnętrznego oprócz zestawu SDK Application Insights, prawdopodobnie zobaczysz dłuższy **czas trwania** podczas wyświetlania dzienników Application Insights. 

![Dłuższy czas trwania w dzienniku Application Insights](./media/service-bus-end-to-end-tracing/longer-duration.png)

Nie oznacza to, że Odebrano komunikat. W tym scenariuszu wiadomość została już odebrana od momentu, gdy wiadomość zostanie przeniesiona jako parametr do kodu zestawu SDK. Natomiast tag **name** w dziennikach usługi App Insights (**proces**) wskazuje, że komunikat jest teraz przetwarzany przez zewnętrzny kod przetwarzania zdarzeń. Ten problem nie jest powiązany z platformą Azure. Zamiast tego metryki odnoszą się do wydajności kodu zewnętrznego, dzięki czemu komunikat został już odebrany z Service Bus. [Ten plik można znaleźć w witrynie GitHub](https://github.com/Azure/azure-sdk-for-net/blob/4bab05144ce647cc9e704d46d3763de5f9681ee0/sdk/servicebus/Microsoft.Azure.ServiceBus/src/ServiceBusDiagnosticsSource.cs) , aby **zobaczyć, gdzie** jest generowany i przypisany po odebraniu komunikatu od Service Bus. 

### <a name="tracking-without-tracing-system"></a>Śledzenie bez systemu śledzenia
Na wypadek, gdy system śledzenia nie obsługuje automatycznego śledzenia wywołań Service Bus, może zajrzeć do dodawania takiego wsparcia do systemu śledzenia lub do aplikacji. W tej sekcji opisano zdarzenia diagnostyczne wysyłane przez program Service Bus klienta programu .NET.  

Klient platformy .NET Service Bus jest Instrumentacją przy użyciu elementów podstawowych śledzenia .NET [System. Diagnostics. Activity](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) i [System. Diagnostics. DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md).

`Activity` służy jako kontekst śledzenia, gdy `DiagnosticSource` jest mechanizmem powiadamiania. 

Jeśli nie ma żadnych odbiorników dla zdarzeń DiagnosticSource, Instrumentacja jest wyłączona, utrzymując koszty Instrumentacji. DiagnosticSource daje wszystkie sterowanie odbiornikiem:
- odbiornik steruje źródłami i zdarzeniami, do których nasłuchuje
- szybkość zdarzeń i próbkowanie kontrolek odbiornika
- zdarzenia są wysyłane z ładunkiem, który zapewnia pełen kontekst, dzięki czemu można uzyskiwać dostęp do obiektu komunikatu i modyfikować go podczas zdarzenia.

Przed kontynuowaniem implementacji zapoznaj się z [podręcznikiem użytkownika DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md) .

Utwórzmy odbiornik dla zdarzeń Service Bus w aplikacji ASP.NET Core, która zapisuje dzienniki przy użyciu Microsoft. Extension. rejestratora.
Używa ona biblioteki [System. Reactive. Core](https://www.nuget.org/packages/System.Reactive.Core) , aby subskrybować DiagnosticSource (jest to również łatwe subskrybowanie DiagnosticSource bez użycia)

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory factory, IApplicationLifetime applicationLifetime)
{
    // configuration...

    var serviceBusLogger = factory.CreateLogger("Microsoft.Azure.ServiceBus");

    IDisposable innerSubscription = null;
    IDisposable outerSubscription = DiagnosticListener.AllListeners.Subscribe(delegate (DiagnosticListener listener)
    {
        // subscribe to the Service Bus DiagnosticSource
        if (listener.Name == "Microsoft.Azure.ServiceBus")
        {
            // receive event from Service Bus DiagnosticSource
            innerSubscription = listener.Subscribe(delegate (KeyValuePair<string, object> evnt)
            {
                // Log operation details once it's done
                if (evnt.Key.EndsWith("Stop"))
                {
                    Activity currentActivity = Activity.Current;
                    TaskStatus status = (TaskStatus)evnt.Value.GetProperty("Status");
                    serviceBusLogger.LogInformation($"Operation {currentActivity.OperationName} is finished, Duration={currentActivity.Duration}, Status={status}, Id={currentActivity.Id}, StartTime={currentActivity.StartTimeUtc}");
                }
            });
        }
    });

    applicationLifetime.ApplicationStopping.Register(() =>
    {
        outerSubscription?.Dispose();
        innerSubscription?.Dispose();
    });
}
```

W tym przykładzie, odbiornik rejestruje czas trwania, wynik, unikatowy identyfikator i godzinę rozpoczęcia dla każdej operacji Service Bus.

#### <a name="events"></a>Zdarzenia

Dla każdej operacji są wysyłane dwa zdarzenia: "Start" i "Stop". Najprawdopodobniej interesuje Cię tylko zdarzenia "Stop". Zapewniają one wynik operacji oraz czas rozpoczęcia i czas trwania jako właściwości działania.

Ładunek zdarzenia udostępnia odbiornik z kontekstem operacji, replikuje parametry przychodzące interfejsu API i wartość zwracaną. Ładunek zdarzenia "Stop" ma wszystkie właściwości ładunku zdarzenia "Start", więc można zignorować wydarzenie "Start" w całości.

Wszystkie zdarzenia mają również właściwości "Entity" i "Endpoint", które zostały pominięte w poniższej tabeli
  * `string Entity` --Nazwa jednostki (kolejki, tematu itp.)
  * `Uri Endpoint` -Service Bus adres URL punktu końcowego

Każde zdarzenie "Stop" ma `Status` Właściwość z `TaskStatus` operacją asynchroniczną została ukończona z, która jest również pomijana w poniższej tabeli dla uproszczenia.

Oto pełna lista operacji Instrumentacji:

| Nazwa operacji | Śledzony interfejs API | Określone właściwości ładunku|
|----------------|-------------|---------|
| Microsoft. Azure. ServiceBus. Send | [MessageSender. SendAsync](/dotnet/api/microsoft.azure.servicebus.core.messagesender.sendasync) | `IList<Message> Messages` -Lista wysyłanych komunikatów |
| Microsoft. Azure. ServiceBus. ScheduleMessage | [MessageSender. ScheduleMessageAsync](/dotnet/api/microsoft.azure.servicebus.core.messagesender.schedulemessageasync) | `Message Message` -Trwa przetwarzanie komunikatu<br/>`DateTimeOffset ScheduleEnqueueTimeUtc` — Przesunięcia zaplanowanego komunikatu<br/>`long SequenceNumber` -Sekwencja numerów zaplanowanych komunikatów ("Zatrzymaj") |
| Microsoft. Azure. ServiceBus. Cancel | [MessageSender. CancelScheduledMessageAsync](/dotnet/api/microsoft.azure.servicebus.core.messagesender.cancelscheduledmessageasync) | `long SequenceNumber` -Numer sekwencyjny wiadomości, która ma zostać anulowana | 
| Microsoft. Azure. ServiceBus. Receive | [MessageReceiver.ReceiveAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.receiveasync) | `int RequestedMessageCount` — Maksymalna liczba komunikatów, które mogą zostać odebrane.<br/>`IList<Message> Messages` -Lista odebranych komunikatów ("Zatrzymaj" ładunku zdarzenia) |
| Microsoft. Azure. ServiceBus. Peek | [MessageReceiver.PeekAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.peekasync) | `int FromSequenceNumber` -Punkt początkowy, od którego należy przeglądać partię komunikatów.<br/>`int RequestedMessageCount` — Liczba komunikatów do pobrania.<br/>`IList<Message> Messages` -Lista odebranych komunikatów ("Zatrzymaj" ładunku zdarzenia) |
| Microsoft. Azure. ServiceBus. ReceiveDeferred | [MessageReceiver.ReceiveDeferredMessageAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.receivedeferredmessageasync) | `IEnumerable<long> SequenceNumbers` — Lista zawierająca numery sekwencji do odebrania.<br/>`IList<Message> Messages` -Lista odebranych komunikatów ("Zatrzymaj" ładunku zdarzenia) |
| Microsoft. Azure. ServiceBus. Complete | [MessageReceiver.CompleteAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.completeasync) | `IList<string> LockTokens` -Lista zawierająca tokeny blokady odpowiednich komunikatów do ukończenia.|
| Microsoft. Azure. ServiceBus. Abandon | [MessageReceiver.AbandonAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.abandonasync) | `string LockToken` -Token blokady odpowiadającego komunikatu do porzucenia. |
| Microsoft. Azure. ServiceBus. Ustąp | [MessageReceiver.DeferAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.deferasync) | `string LockToken` -Token blokady odpowiadającego komunikatu do odroczenia. | 
| Microsoft. Azure. ServiceBus. utracony | [MessageReceiver.DeadLetterAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.deadletterasync) | `string LockToken` -Token blokady odpowiadającego komunikatu na wiadomość utraconą. | 
| Microsoft. Azure. ServiceBus. RenewLock | [MessageReceiver.RenewLockAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.renewlockasync) | `string LockToken` -Token blokady odpowiedniej wiadomości, aby odnowić blokadę.<br/>`DateTime LockedUntilUtc` -Nowa Data i godzina wygaśnięcia tokenu blokady w formacie UTC. ("Zatrzymaj" ładunek zdarzenia)|
| Microsoft. Azure. ServiceBus. Process | Funkcja lambda obsługi komunikatów podana w [IReceiverClient. RegisterMessageHandler](/dotnet/api/microsoft.azure.servicebus.core.ireceiverclient.registermessagehandler) | `Message Message` -Komunikat jest przetwarzany. |
| Microsoft. Azure. ServiceBus. ProcessSession | Funkcja lambda obsługi sesji komunikatów podana w [IQueueClient. RegisterSessionHandler](/dotnet/api/microsoft.azure.servicebus.iqueueclient.registersessionhandler) | `Message Message` -Komunikat jest przetwarzany.<br/>`IMessageSession Session` -Trwa przetwarzanie sesji |
| Microsoft. Azure. ServiceBus. AddRule | [SubscriptionClient. AddRuleAsync](/dotnet/api/microsoft.azure.servicebus.subscriptionclient.addruleasync) | `RuleDescription Rule` -Opis reguły, która zapewnia regułę do dodania. |
| Microsoft. Azure. ServiceBus. RemoveRule | [SubscriptionClient. RemoveRuleAsync](/dotnet/api/microsoft.azure.servicebus.subscriptionclient.removeruleasync) | `string RuleName` -Nazwa reguły do usunięcia. |
| Microsoft. Azure. ServiceBus. GetRules | [SubscriptionClient. GetRulesAsync](/dotnet/api/microsoft.azure.servicebus.subscriptionclient.getrulesasync) | `IEnumerable<RuleDescription> Rules` — Wszystkie reguły skojarzone z subskrypcją. (Tylko ładunek "Stop") |
| Microsoft. Azure. ServiceBus. AcceptMessageSession | [ISessionClient.AcceptMessageSessionAsync](/dotnet/api/microsoft.azure.servicebus.isessionclient.acceptmessagesessionasync) | `string SessionId` -Identyfikator sesji znajdujący się w wiadomościach. |
| Microsoft. Azure. ServiceBus. GetSessionState | [IMessageSession.GetStateAsync](/dotnet/api/microsoft.azure.servicebus.imessagesession.getstateasync) | `string SessionId` -Identyfikator sesji znajdujący się w wiadomościach.<br/>`byte [] State` -Stan sesji ("Zatrzymaj" ładunku zdarzenia) |
| Microsoft. Azure. ServiceBus. SetSessionState | [IMessageSession.SetStateAsync](/dotnet/api/microsoft.azure.servicebus.imessagesession.setstateasync) | `string SessionId` -Identyfikator sesji znajdujący się w wiadomościach.<br/>`byte [] State` -Stan sesji |
| Microsoft. Azure. ServiceBus. RenewSessionLock | [IMessageSession.RenewSessionLockAsync](/dotnet/api/microsoft.azure.servicebus.imessagesession.renewsessionlockasync) | `string SessionId` -Identyfikator sesji znajdujący się w wiadomościach. |
| Microsoft. Azure. ServiceBus. Exception | dowolny interfejs API instrumentacji| `Exception Exception` -Wystąpienie wyjątku |

W każdym zdarzeniu można uzyskać dostęp do `Activity.Current` , który zawiera bieżący kontekst operacji.

#### <a name="logging-more-properties"></a>Rejestrowanie większej liczby właściwości

`Activity.Current` zawiera szczegółowy kontekst bieżącej operacji i jej obiektów nadrzędnych. Aby uzyskać więcej informacji, zobacz [dokumentację dotyczącą działania](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md).
Instrumentacja Service Bus zapewnia więcej informacji o tym, że są `Activity.Current.Tags` one przechowywane `MessageId` i `SessionId` zawsze, gdy są dostępne.

Działania śledzące zdarzenie "Receive", "Peek" i "ReceiveDeferred" mogą także mieć `RelatedTo` tag. Zawiera ona odrębną listę `Diagnostic-Id` (y) komunikatów, które zostały odebrane w wyniku.
Takie działanie może spowodować odebranie kilku niepowiązanych komunikatów. Ponadto `Diagnostic-Id` nie jest znana, gdy operacja zostanie uruchomiona, więc operacje "Receive" mogą być skorelowane dla operacji "Process" tylko przy użyciu tego tagu. Jest to przydatne podczas analizowania problemów z wydajnością, aby sprawdzić, jak długo trwało odbieranie komunikatów.

Wydajnym sposobem rejestrowania tagów jest przetestowanie nad nimi, więc dodanie tagów do powyższego przykładu wygląda jak 

```csharp
Activity currentActivity = Activity.Current;
TaskStatus status = (TaskStatus)evnt.Value.GetProperty("Status");

var tagsList = new StringBuilder();
foreach (var tags in currentActivity.Tags)
{
    tagsList.Append($", {tags.Key}={tags.Value}");
}

serviceBusLogger.LogInformation($"{currentActivity.OperationName} is finished, Duration={currentActivity.Duration}, Status={status}, Id={currentActivity.Id}, StartTime={currentActivity.StartTimeUtc}{tagsList}");
```

#### <a name="filtering-and-sampling"></a>Filtrowanie i próbkowanie

W niektórych przypadkach wskazane jest zarejestrowanie tylko części zdarzeń, aby zmniejszyć obciążenie związane z wydajnością lub użycie magazynu. Można rejestrować tylko zdarzenia "Stop" (jak w powyższym przykładzie) lub próbkować procent zdarzeń. 
`DiagnosticSource` Określ sposób osiągnięcia tego celu z `IsEnabled` predykatem. Aby uzyskać więcej informacji, zobacz [filtrowanie na podstawie kontekstu w DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md#context-based-filtering).

`IsEnabled` może być wywoływana wiele razy dla jednej operacji, aby zminimalizować wpływ na wydajność.

`IsEnabled` jest wywoływana w następującej kolejności:

1. `IsEnabled(<OperationName>, string entity, null)` na przykład `IsEnabled("Microsoft.Azure.ServiceBus.Send", "MyQueue1")` . Uwaga nie ma na końcu elementu "Start" ani "Stop". Służy do filtrowania określonych operacji lub kolejek. Jeśli metoda wywołania zwrotnego zwraca `false` , zdarzenia dla operacji nie są wysyłane

   * W przypadku operacji "Process" i "ProcessSession" otrzymasz również `IsEnabled(<OperationName>, string entity, Activity activity)` wywołanie zwrotne. Służy do filtrowania zdarzeń na podstawie `activity.Id` Właściwości tagów lub.
  
2. `IsEnabled(<OperationName>.Start)` na przykład `IsEnabled("Microsoft.Azure.ServiceBus.Send.Start")` . Sprawdza, czy zdarzenie "Start" powinno być wyzwalane. Wynik dotyczy tylko zdarzenia "Start", ale dalsze Instrumentacja nie jest od niego zależne.

Nie ma `IsEnabled` dla zdarzenia "Stop".

Jeśli wynikiem operacji jest wyjątek, `IsEnabled("Microsoft.Azure.ServiceBus.Exception")` jest wywoływana. Można zasubskrybować tylko zdarzenia "Exception" i uniemożliwić resztę Instrumentacji. W takim przypadku nadal trzeba obsługiwać takie wyjątki. Ponieważ inne Instrumentacja jest wyłączona, nie należy oczekiwać kontekstu śledzenia do przepływu z komunikatami od konsumenta do producenta.

Można użyć `IsEnabled` również strategii próbkowania. Próbkowanie oparte na `Activity.Id` lub `Activity.RootId` zapewnia spójne próbkowanie na wszystkich oponach (o ile jest propagowane przez system śledzenia lub przez własny kod).

W obecności wielu `DiagnosticSource` odbiorników dla tego samego źródła wystarczy tylko jeden odbiornik do zaakceptowania zdarzenia, dlatego nie ma żadnej gwarancji, która `IsEnabled` jest wywoływana.

---

## <a name="next-steps"></a>Następne kroki

* [Application Insights korelacji](../azure-monitor/app/correlation.md)
* [Application Insights Monitoruj zależności](../azure-monitor/app/asp-net-dependencies.md) , aby zobaczyć, czy REST, SQL lub inne zasoby zewnętrzne spowalniają pracę.
* [Śledzenie operacji niestandardowych przy użyciu zestawu SDK platformy Application Insights .NET](../azure-monitor/app/custom-operations-tracking.md)

