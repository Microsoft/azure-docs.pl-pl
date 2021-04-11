---
title: Generowanie zdarzeń dzienników z poziomu aplikacji .NET
description: Dowiedz się więcej na temat sposobu dodawania rejestrowania do aplikacji .NET Service Fabric hostowanej w klastrze platformy Azure lub w klastrze autonomicznym.
ms.topic: conceptual
ms.date: 03/27/2018
ms.custom: devx-track-csharp
ms.openlocfilehash: dd3afed85dc3eff81cbe60f252cdc16b0d4018c8
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105628122"
---
# <a name="add-logging-to-your-service-fabric-application"></a>Add logging to your Service Fabric application (Dodawanie rejestrowania do aplikacji usługi Service Fabric)

Aplikacja musi zapewnić wystarczającą ilość informacji, aby forensically debugowanie w przypadku wystąpienia problemów. Rejestrowanie jest jednym z najważniejszych rzeczy, które możesz dodać do aplikacji Service Fabric. Jeśli wystąpi awaria, dobre rejestrowanie może umożliwić zbadanie niepowodzeń. Analizując wzorce dzienników, można dowiedzieć się, jak zwiększyć wydajność lub projekt aplikacji. W tym dokumencie przedstawiono kilka różnych opcji rejestrowania.

## <a name="eventflow"></a>Użyciu struktury eventflow

Pakiet [biblioteki użyciu struktury eventflow](https://github.com/Azure/diagnostics-eventflow) umożliwia aplikacjom Definiowanie, które dane diagnostyczne mają być zbierane, oraz miejsce, do którego powinny zostać wyszukane. Dane diagnostyczne mogą być dowolne od liczników wydajności do śladów aplikacji. Działa w tym samym procesie co aplikacja, więc obciążanie komunikacji jest zminimalizowane. Aby uzyskać więcej informacji na temat użyciu struktury eventflow i Service Fabric, zobacz [Azure Service Fabric Event agregacji z użyciu struktury eventflow](service-fabric-diagnostics-event-aggregation-eventflow.md).

### <a name="using-structured-eventsource-events"></a>Używanie zdarzeń zdarzenia EventSource ze strukturą

Definiowanie zdarzeń komunikatów według przypadków użycia umożliwia spakowanie danych dotyczących zdarzenia w kontekście zdarzenia. Można łatwiej przeszukiwać i filtrować na podstawie nazw lub wartości określonych właściwości zdarzenia. Tworzenie struktury danych wyjściowych Instrumentacji ułatwia ich odczytywanie, ale wymaga większej liczby myśli i czasu, aby zdefiniować zdarzenie dla każdego przypadku użycia. 

Niektóre definicje zdarzeń mogą być współużytkowane przez całą aplikację. Na przykład zdarzenie uruchomienia lub zatrzymania metody będzie ponownie używane w wielu usługach w ramach aplikacji. Usługa specyficzna dla domeny, taka jak system zamówienia, może mieć zdarzenie w **porządku** , które ma własne wydarzenie unikatowe. Takie podejście może generować wiele zdarzeń i może wymagać koordynacji identyfikatorów w zespołach projektów. 

```csharp
[EventSource(Name = "MyCompany-VotingState-VotingStateService")]
internal sealed class ServiceEventSource : EventSource
{
    public static readonly ServiceEventSource Current = new ServiceEventSource();

    // The instance constructor is private to enforce singleton semantics.
    private ServiceEventSource() : base() { }

    ...

    // The ServiceTypeRegistered event contains a unique identifier, an event attribute that defined the event, and the code implementation of the event.
    private const int ServiceTypeRegisteredEventId = 3;
    [Event(ServiceTypeRegisteredEventId, Level = EventLevel.Informational, Message = "Service host process {0} registered service type {1}", Keywords = Keywords.ServiceInitialization)]
    public void ServiceTypeRegistered(int hostProcessId, string serviceType)
    {
        WriteEvent(ServiceTypeRegisteredEventId, hostProcessId, serviceType);
    }

    // The ServiceHostInitializationFailed event contains a unique identifier, an event attribute that defined the event, and the code implementation of the event.
    private const int ServiceHostInitializationFailedEventId = 4;
    [Event(ServiceHostInitializationFailedEventId, Level = EventLevel.Error, Message = "Service host initialization failed", Keywords = Keywords.ServiceInitialization)]
    public void ServiceHostInitializationFailed(string exception)
    {
        WriteEvent(ServiceHostInitializationFailedEventId, exception);
    }

    ...

```

### <a name="using-eventsource-generically"></a>Korzystanie z usługi EventSource ogólnie

Ponieważ Definiowanie określonych zdarzeń może być trudne, wiele osób definiuje kilka zdarzeń ze wspólnym zestawem parametrów, które zwykle wyprowadzają informacje jako ciąg. Większość strukturalnego aspektu jest tracona i trudniejsze jest wyszukiwanie i filtrowanie wyników. W tym podejściu definiowane są kilka zdarzeń, które zwykle odpowiadają poziomów rejestrowania. Poniższy fragment kodu definiuje debugowanie i komunikat o błędzie:

```csharp
[EventSource(Name = "MyCompany-VotingState-VotingStateService")]
internal sealed class ServiceEventSource : EventSource
{
    public static readonly ServiceEventSource Current = new ServiceEventSource();

    // The Instance constructor is private, to enforce singleton semantics.
    private ServiceEventSource() : base() { }

    ...

    private const int DebugEventId = 10;
    [Event(DebugEventId, Level = EventLevel.Verbose, Message = "{0}")]
    public void Debug(string msg)
    {
        WriteEvent(DebugEventId, msg);
    }

    private const int ErrorEventId = 11;
    [Event(ErrorEventId, Level = EventLevel.Error, Message = "Error: {0} - {1}")]
    public void Error(string error, string msg)
    {
        WriteEvent(ErrorEventId, error, msg);
    }

    ...

```

Użycie hybrydowej Instrumentacji strukturalnej i ogólnej może również współpracować. Instrumentacja strukturalna jest używana do raportowania błędów i metryk. Zdarzenia ogólne mogą służyć do szczegółowego rejestrowania używanego przez inżynierów w celu rozwiązywania problemów.

## <a name="microsoftextensionslogging"></a>Microsoft.Extensions.Logging

Rejestrowanie ASP.NET Core ([pakiet NuGet Microsoft. Extensions. rejestrowania](https://www.nuget.org/packages/Microsoft.Extensions.Logging)) to struktura rejestrowania, która udostępnia interfejs API rejestrowania standardowego dla aplikacji. Aby obsłużyć inne rejestracje, można podłączyć się do ASP.NET Core rejestrowania. Zapewnia to szeroką gamę obsługi rejestrowania w aplikacji, bez konieczności zmiany kodu.

1. Dodaj pakiet NuGet **Microsoft. Extensions. Logging** do projektu, który chcesz instrumentować. Ponadto Dodaj wszystkie pakiety dostawcy. Aby uzyskać więcej informacji, zobacz [Rejestrowanie w ASP.NET Core](/aspnet/core/fundamentals/logging).
2. Dodaj dyrektywę **using** dla **Microsoft. Extensions. Logging** do pliku usługi.
3. Zdefiniuj prywatną zmienną w klasie usługi.

   ```csharp
   private ILogger _logger = null;
   ```

4. W konstruktorze klasy usługi Dodaj następujący kod:

   ```csharp
   _logger = new LoggerFactory().CreateLogger<Stateless>();
   ```

5. Rozpocznij instrumentację kodu w metodach. Oto kilka przykładów:

   ```csharp
   _logger.LogDebug("Debug-level event from Microsoft.Logging");
   _logger.LogInformation("Informational-level event from Microsoft.Logging");

   // In this variant, we're adding structured properties RequestName and Duration, which have values MyRequest and the duration of the request.
   // Later in the article, we discuss why this step is useful.
   _logger.LogInformation("{RequestName} {Duration}", "MyRequest", requestDuration);
   ```

### <a name="using-other-logging-providers"></a>Korzystanie z innych dostawców rejestrowania

Niektórzy dostawcy innych firm używają podejścia opisanego w poprzedniej sekcji, w tym [Serilog](https://serilog.net/), [nLOG](https://nlog-project.org/)i [Loggr](https://github.com/imobile3/Loggr.Extensions.Logging). Każdy z nich można podłączyć do ASP.NET Core rejestrowania lub użyć ich osobno. Serilog zawiera funkcję, która wzbogaca wszystkie komunikaty wysyłane z rejestratora. Ta funkcja może być przydatna do wyprowadzania nazwy usługi, typu i informacji o partycji. Aby skorzystać z tej możliwości w infrastrukturze ASP.NET Core, wykonaj następujące czynności:

1. Dodaj **Serilog**, **Serilog. Extensions. Logging**, **Serilog. ujścias. Literate** i **Serilog. ujścia. zauważalne** pakiety NuGet do projektu. 
2. Utwórz `LoggerConfiguration` wystąpienie rejestratora i.

   ```csharp
   Log.Logger = new LoggerConfiguration().WriteTo.LiterateConsole().CreateLogger();
   ```

3. Dodaj `Serilog.ILogger` argument do konstruktora usługi i przekaż nowo utworzony rejestrator.

   ```csharp
   ServiceRuntime.RegisterServiceAsync("StatelessType", context => new Stateless(context, Log.Logger)).GetAwaiter().GetResult();
   ```

4. W konstruktorze usług tworzy wzbogacania właściwości dla **ServiceTypeName**, **ServiceName**, **PartitionID** i **InstanceId**.

   ```csharp
   public Stateless(StatelessServiceContext context, Serilog.ILogger serilog)
       : base(context)
   {
       PropertyEnricher[] properties = new PropertyEnricher[]
       {
           new PropertyEnricher("ServiceTypeName", context.ServiceTypeName),
           new PropertyEnricher("ServiceName", context.ServiceName),
           new PropertyEnricher("PartitionId", context.PartitionId),
           new PropertyEnricher("InstanceId", context.ReplicaOrInstanceId),
       };

       serilog.ForContext(properties);

       _logger = new LoggerFactory().AddSerilog(serilog.ForContext(properties)).CreateLogger<Stateless>();
   }
   ```

5. Instrumentacja kodu jest taka sama jak w przypadku używania ASP.NET Core bez Serilog.

   >[!NOTE]
   >Zalecamy, aby *nie* używać static w `Log.Logger` poprzednim przykładzie. Service Fabric może hostować wiele wystąpień tego samego typu usługi w ramach jednego procesu. Jeśli używasz statycznego `Log.Logger` , ostatni składnik służący do wzbogacania właściwości będzie wyświetlał wartości dla wszystkich wystąpień, które są uruchomione. Jest to jeden powód, dla którego zmienna _logger jest prywatną zmienną członkowską klasy usługi. Ponadto należy udostępnić `_logger` wspólny kod, który może być używany przez usługi.

## <a name="next-steps"></a>Następne kroki

- Przeczytaj więcej informacji [na temat monitorowania aplikacji w Service Fabric](service-fabric-diagnostics-event-generation-app.md).
- Przeczytaj o rejestrowaniu za pomocą [użyciu struktury eventflow](service-fabric-diagnostics-event-aggregation-eventflow.md) i [Windows Diagnostyka Azure](service-fabric-diagnostics-event-aggregation-wad.md).
