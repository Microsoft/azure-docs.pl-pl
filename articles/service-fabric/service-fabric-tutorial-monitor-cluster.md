---
title: Monitorowanie klastra Service Fabric na platformie Azure
description: W tym samouczku dowiesz się, jak monitorować klaster, wyświetlając Service Fabric zdarzenia, wykonując zapytania dotyczące interfejsów API EventStore, monitorowania liczników wydajności i wyświetlania raportów o kondycji.
ms.topic: tutorial
ms.date: 07/22/2019
ms.custom: mvc, devx-track-csharp
ms.openlocfilehash: 3e9c8011647567b6f07423e113f98493d80d8402
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105628785"
---
# <a name="tutorial-monitor-a-service-fabric-cluster-in-azure"></a>Samouczek: monitorowanie klastra Service Fabric na platformie Azure

Monitorowanie i Diagnostyka mają kluczowe znaczenie dla opracowywania, testowania i wdrażania obciążeń w dowolnym środowisku chmury. Ten samouczek jest drugą częścią serii i pokazuje, jak monitorować i diagnozować klaster Service Fabric przy użyciu zdarzeń, liczników wydajności i raportów kondycji.   Aby uzyskać więcej informacji, zapoznaj się z omówieniem [monitorowanie klastra](service-fabric-diagnostics-overview.md#platform-cluster-monitoring) i [monitorowanie infrastruktury](service-fabric-diagnostics-overview.md#infrastructure-performance-monitoring).

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Wyświetlanie zdarzeń Service Fabric
> * Wykonywanie zapytań dotyczących interfejsów API EventStore dla zdarzeń klastra
> * Monitorowanie infrastruktury/Zbieranie liczników wydajności
> * Wyświetlanie raportów o kondycji klastra

Ta seria samouczków zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Tworzenie bezpiecznego [klastra systemu Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md) na platformie Azure przy użyciu szablonu
> * Monitorowanie klastra
> * [Skalowanie klastra na zewnątrz lub do wewnątrz](service-fabric-tutorial-scale-cluster.md)
> * [Uaktualnianie środowiska uruchomieniowego klastra](service-fabric-tutorial-upgrade-cluster.md)
> * [Usuwanie klastra](service-fabric-tutorial-delete-cluster.md)


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem tego samouczka:

* Jeśli nie masz subskrypcji platformy Azure, Utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Zainstaluj [Azure PowerShell](/powershell/azure/install-az-ps) lub [interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).
* Tworzenie bezpiecznego [klastra systemu Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md) 
* Konfiguracja [zbierania danych diagnostycznych](service-fabric-tutorial-create-vnet-and-windows-cluster.md#configurediagnostics_anchor) dla klastra
* Włączanie [usługi EventStore](service-fabric-tutorial-create-vnet-and-windows-cluster.md#configureeventstore_anchor) w klastrze
* Konfigurowanie [dzienników Azure monitor i agenta log Analytics](service-fabric-tutorial-create-vnet-and-windows-cluster.md#configureloganalytics_anchor) dla klastra

## <a name="view-service-fabric-events-using-azure-monitor-logs"></a>Wyświetlanie zdarzeń Service Fabric przy użyciu dzienników Azure Monitor

Dzienniki Azure Monitor zbierają i analizują dane telemetryczne z aplikacji i usług hostowanych w chmurze oraz udostępniają narzędzia do analizy ułatwiające maksymalizowanie ich dostępności i wydajności. Można uruchamiać zapytania w dziennikach Azure Monitor, aby uzyskać szczegółowe informacje i rozwiązywać problemy związane z tym, co dzieje się w klastrze.

Aby uzyskać dostęp do rozwiązania Service Fabric Analytics, przejdź do [Azure Portal](https://portal.azure.com) i wybierz grupę zasobów, w której utworzono rozwiązanie Service Fabric Analytics.

Wybierz pozycję Resource **webfabric (mysfomsworkspace)**.

W obszarze **Przegląd** widoczne są kafelki w formie wykresu dla każdego z włączonych rozwiązań, w tym jeden dla Service Fabric. Kliknij wykres **Service Fabric** , aby kontynuować Service Fabric Analytics rozwiązanie.

![Zrzut ekranu przedstawiający wykres Service Fabric.](media/service-fabric-tutorial-monitor-cluster/oms-service-fabric-summary.png)

Na poniższej ilustracji przedstawiono stronę główną rozwiązania Service Fabric Analytics. Ta strona główna zawiera widok migawek, co dzieje się w klastrze.

![Service Fabric rozwiązanie](media/service-fabric-tutorial-monitor-cluster/oms-service-fabric-solution.png)

 Jeśli po utworzeniu klastra włączono diagnostykę, można zobaczyć zdarzenia dla 

* [Zdarzenia klastra Service Fabric](service-fabric-diagnostics-event-generation-operational.md)
* [Zdarzenia modelu programowania Reliable Actors](service-fabric-reliable-actors-diagnostics.md)
* [Zdarzenia modelu programowania Reliable Services](service-fabric-reliable-services-diagnostics.md)

>[!NOTE]
>Oprócz Service Fabric zdarzeń z pola, bardziej szczegółowe zdarzenia systemowe można zbierać, [aktualizując konfigurację rozszerzenia diagnostyki](service-fabric-diagnostics-event-aggregation-wad.md#log-collection-configurations).

### <a name="view-service-fabric-events-including-actions-on-nodes"></a>Wyświetlanie zdarzeń Service Fabric, w tym akcji w węzłach

Na stronie Service Fabric Analytics kliknij wykres dla **zdarzeń klastra**.  Zostaną wyświetlone dzienniki dla wszystkich zebranych zdarzeń systemowych. Z tego względu są one z **WADServiceFabricSystemEventsTable** na koncie usługi Azure Storage, a podobnie zdarzenia niezawodnych usług i aktorów, które zobaczysz, są z tych tabel.
    
![Kanał operacyjny zapytania](media/service-fabric-tutorial-monitor-cluster/oms-service-fabric-events.png)

Zapytanie używa języka zapytań Kusto, który można modyfikować w celu uściślenia tego, czego szukasz. Na przykład aby znaleźć wszystkie akcje wykonywane na węzłach w klastrze, można użyć poniższego zapytania. Identyfikatory zdarzeń używane poniżej znajdują się w [dokumentacji zdarzenia dotyczącego kanału operacyjnego](service-fabric-diagnostics-event-generation-operational.md).

```kusto
ServiceFabricOperationalEvent
| where EventId < 25627 and EventId > 25619 
```

Język zapytań Kusto jest zaawansowany. Oto kilka innych przydatnych zapytań.

Tworzenie tabeli odnośników *ServiceFabricEvent* jako funkcji zdefiniowanej przez użytkownika przez zapisanie zapytania jako funkcji z aliasem ServiceFabricEvent:

```kusto
let ServiceFabricEvent = datatable(EventId: int, EventName: string)
[
    ...
    18603, 'NodeUpOperational',
    18604, 'NodeDownOperational',
    ...
];
ServiceFabricEvent
```

Zwróć zdarzenia operacyjne zarejestrowane w ciągu ostatniej godziny:
```kusto
ServiceFabricOperationalEvent
| where TimeGenerated > ago(1h)
| join kind=leftouter ServiceFabricEvent on EventId
| project EventId, EventName, TaskName, Computer, ApplicationName, EventMessage, TimeGenerated
| sort by TimeGenerated
```

Zwróć zdarzenia operacyjne z EventId = = 18604 i EventName = = ' NodeDownOperational ':
```kusto
ServiceFabricOperationalEvent
| where EventId == 18604
| project EventId, EventName = 'NodeDownOperational', TaskName, Computer, EventMessage, TimeGenerated
| sort by TimeGenerated 
```

Zwróć zdarzenia operacyjne z EventId = = 18604 i EventName = = ' NodeUpOperational ':
```kusto
ServiceFabricOperationalEvent
| where EventId == 18603
| project EventId, EventName = 'NodeUpOperational', TaskName, Computer, EventMessage, TimeGenerated
| sort by TimeGenerated 
``` 

Zwraca raporty kondycji z atrybutem HealthState = = 3 (error) i wyodrębnienie dodatkowych właściwości z pola EventMessage:

```kusto
ServiceFabricOperationalEvent
| join kind=leftouter ServiceFabricEvent on EventId
| extend HealthStateId = extract(@"HealthState=(\S+) ", 1, EventMessage, typeof(int))
| where TaskName == 'HM' and HealthStateId == 3
| extend SourceId = extract(@"SourceId=(\S+) ", 1, EventMessage, typeof(string)),
         Property = extract(@"Property=(\S+) ", 1, EventMessage, typeof(string)),
         HealthState = case(HealthStateId == 0, 'Invalid', HealthStateId == 1, 'Ok', HealthStateId == 2, 'Warning', HealthStateId == 3, 'Error', 'Unknown'),
         TTL = extract(@"TTL=(\S+) ", 1, EventMessage, typeof(string)),
         SequenceNumber = extract(@"SequenceNumber=(\S+) ", 1, EventMessage, typeof(string)),
         Description = extract(@"Description='([\S\s, ^']+)' ", 1, EventMessage, typeof(string)),
         RemoveWhenExpired = extract(@"RemoveWhenExpired=(\S+) ", 1, EventMessage, typeof(bool)),
         SourceUTCTimestamp = extract(@"SourceUTCTimestamp=(\S+)", 1, EventMessage, typeof(datetime)),
         ApplicationName = extract(@"ApplicationName=(\S+) ", 1, EventMessage, typeof(string)),
         ServiceManifest = extract(@"ServiceManifest=(\S+) ", 1, EventMessage, typeof(string)),
         InstanceId = extract(@"InstanceId=(\S+) ", 1, EventMessage, typeof(string)),
         ServicePackageActivationId = extract(@"ServicePackageActivationId=(\S+) ", 1, EventMessage, typeof(string)),
         NodeName = extract(@"NodeName=(\S+) ", 1, EventMessage, typeof(string)),
         Partition = extract(@"Partition=(\S+) ", 1, EventMessage, typeof(string)),
         StatelessInstance = extract(@"StatelessInstance=(\S+) ", 1, EventMessage, typeof(string)),
         StatefulReplica = extract(@"StatefulReplica=(\S+) ", 1, EventMessage, typeof(string))
```

Zwróć wykres czasu zdarzeń z EventId! = 17523:

```kusto
ServiceFabricOperationalEvent
| join kind=leftouter ServiceFabricEvent on EventId
| where EventId != 17523
| summarize Count = count() by Timestamp = bin(TimeGenerated, 1h), strcat(tostring(EventId), " - ", case(EventName != "", EventName, "Unknown"))
| render timechart 
```

Pobierz Service Fabric zdarzenia operacyjne zagregowane z określoną usługą i węzłem:

```kusto
ServiceFabricOperationalEvent
| where ApplicationName  != "" and ServiceName != ""
| summarize AggregatedValue = count() by ApplicationName, ServiceName, Computer 
```

Renderuj liczbę zdarzeń Service Fabric przez EventId/EventName przy użyciu zapytania obejmującego wiele zasobów:

```kusto
app('PlunkoServiceFabricCluster').traces
| where customDimensions.ProviderName == 'Microsoft-ServiceFabric'
| extend EventId = toint(customDimensions.EventId), TaskName = tostring(customDimensions.TaskName)
| where EventId != 17523
| join kind=leftouter ServiceFabricEvent on EventId
| extend EventName = case(EventName != '', EventName, 'Undocumented')
| summarize ["Event Count"]= count() by bin(timestamp, 30m), EventName = strcat(tostring(EventId), " - ", EventName)
| render timechart
```

### <a name="view-service-fabric-application-events"></a>Wyświetlanie Service Fabric zdarzeń aplikacji

Można wyświetlać zdarzenia dotyczące niezawodnych usług i niezawodnych aplikacji wdrożonych w klastrze.  Na stronie Service Fabric Analytics kliknij wykres dla **zdarzeń aplikacji**.

Uruchom następujące zapytanie, aby wyświetlić zdarzenia z aplikacji niezawodnych usług:
```kusto
ServiceFabricReliableServiceEvent
| sort by TimeGenerated desc
```

Można zobaczyć różne zdarzenia dla momentu `runasync` uruchomienia i ukończenia usługi, która zwykle odbywa się w przypadku wdrożeń i uaktualnień.

![Reliable Services Service Fabric rozwiązania](media/service-fabric-tutorial-monitor-cluster/oms-reliable-services-events-selection.png)

Możesz również wyszukać zdarzenia dla niezawodnej usługi za pomocą ServiceName = = "Fabric:/alarm/WatchdogService":

```kusto
ServiceFabricReliableServiceEvent
| where ServiceName == "fabric:/Watchdog/WatchdogService"
| project TimeGenerated, EventMessage
| order by TimeGenerated desc  
```
 
Niezawodne zdarzenia aktora można wyświetlać w podobny sposób:

```kusto
ServiceFabricReliableActorEvent
| sort by TimeGenerated desc
```
Aby skonfigurować bardziej szczegółowe zdarzenia dla niezawodnych aktorów, można zmienić `scheduledTransferKeywordFilter` w pliku config dla rozszerzenia diagnostyki w szablonie klastra. Szczegółowe informacje na temat tych wartości znajdują się w [odniesieniu do zdarzeń niezawodnych aktorów](service-fabric-reliable-actors-diagnostics.md#keywords).

```json
"EtwEventSourceProviderConfiguration": [
                {
                    "provider": "Microsoft-ServiceFabric-Actors",
                    "scheduledTransferKeywordFilter": "1",
                    "scheduledTransferPeriod": "PT5M",
                    "DefaultEvents": {
                    "eventDestination": "ServiceFabricReliableActorEventTable"
                    }
                },
```

## <a name="view-performance-counters-with-azure-monitor-logs"></a>Wyświetlanie liczników wydajności za pomocą dzienników Azure Monitor
Aby wyświetlić liczniki wydajności, przejdź do [Azure Portal](https://portal.azure.com) i grupy zasobów, w której utworzono rozwiązanie Service Fabric Analytics. 

Wybierz pozycję Resource **Fabric (mysfomsworkspace)**, a następnie **log Analytics obszarze roboczym**, a następnie kliknij pozycję **Ustawienia zaawansowane**.

Kliknij pozycję **dane**, a następnie kliknij pozycję **liczniki wydajności systemu Windows**. Istnieje lista domyślnych liczników, które można włączyć, i można ustawić interwał dla kolekcji. Możesz również dodać [Dodatkowe liczniki wydajności](service-fabric-diagnostics-event-generation-perf.md) do zbierania. W tym [artykule](/windows/desktop/PerfCtrs/specifying-a-counter-path)jest przywoływany właściwy format. Kliknij przycisk **Zapisz**, a następnie kliknij przycisk **OK**.

Zamknij blok ustawienia zaawansowane i wybierz pozycję **Podsumowanie obszaru roboczego** pod nagłówkiem **Ogólne** . Dla każdego z włączonych rozwiązań istnieje kafelek graficzny, w tym jeden dla Service Fabric. Kliknij wykres **Service Fabric** , aby kontynuować Service Fabric Analytics rozwiązanie.

Istnieją graficzne kafelki dla zdarzeń kanałów operacyjnych i niezawodnych usług. Graficzna reprezentacja danych przepływających dla wybranych liczników będzie wyświetlana w obszarze **metryki węzła**. 

Wybierz wykres **metryki kontenera** , aby wyświetlić dodatkowe szczegóły. Można również wykonywać zapytania dotyczące danych licznika wydajności podobnie jak zdarzenia klastra i filtrować w węzłach, nazwach liczników wydajności i wartościach przy użyciu języka zapytań Kusto.

## <a name="query-the-eventstore-service"></a>Wysyłanie zapytań do usługi EventStore
[Usługa EventStore](service-fabric-diagnostics-eventstore.md) zapewnia sposób zrozumienia stanu klastra lub obciążeń w danym punkcie w czasie. EventStore jest usługą stanową Service Fabric, która przechowuje zdarzenia z klastra. Zdarzenia są udostępniane za pomocą [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md), REST i interfejsów API. EventStore wysyła zapytanie bezpośrednio do klastra w celu uzyskania danych diagnostycznych w dowolnej jednostce w klastrze, aby wyświetlić pełną listę zdarzeń dostępnych w EventStore, zobacz [Service Fabric Events](service-fabric-diagnostics-event-generation-operational.md).

Do interfejsów API EventStore można wykonywać zapytania programowo przy użyciu [biblioteki klienta Service Fabric](/dotnet/api/overview/azure/service-fabric#client-library).

Oto przykładowe żądanie dotyczące wszystkich zdarzeń klastra między 2018 r-04-03T18:00:00Z i 2018 r-04-04T18:00:00Z, za pomocą funkcji GetClusterEventListAsync.

```csharp
var sfhttpClient = ServiceFabricClientFactory.Create(clusterUrl, settings);

var clstrEvents = sfhttpClient.EventsStore.GetClusterEventListAsync(
    "2018-04-03T18:00:00Z",
    "2018-04-04T18:00:00Z")
    .GetAwaiter()
    .GetResult()
    .ToList();
```

Oto inny przykład, który wykonuje zapytania dotyczące kondycji klastra i wszystkich zdarzeń węzła we wrześniu 2018 i drukuje je na zewnątrz.

```csharp
const int timeoutSecs = 60;
var clusterUrl = new Uri(@"http://localhost:19080"); // This example is for a Local cluster
var sfhttpClient = ServiceFabricClientFactory.Create(clusterUrl);

var clusterHealth = sfhttpClient.Cluster.GetClusterHealthAsync().GetAwaiter().GetResult();
Console.WriteLine("Cluster Health: {0}", clusterHealth.AggregatedHealthState.Value.ToString());


Console.WriteLine("Querying for node events...");
var nodesEvents = sfhttpClient.EventsStore.GetNodesEventListAsync(
    "2018-09-01T00:00:00Z",
    "2018-09-30T23:59:59Z",
    timeoutSecs,
    "NodeDown,NodeUp")
    .GetAwaiter()
    .GetResult()
    .ToList();
Console.WriteLine("Result Count: {0}", nodesEvents.Count());

foreach (var nodeEvent in nodesEvents)
{
    Console.Write("Node event happened at {0}, Node name: {1} ", nodeEvent.TimeStamp, nodeEvent.NodeName);
    if (nodeEvent is NodeDownEvent)
    {
        var nodeDownEvent = nodeEvent as NodeDownEvent;
        Console.WriteLine("(Node is down, and it was last up at {0})", nodeDownEvent.LastNodeUpAt);
    }
    else if (nodeEvent is NodeUpEvent)
    {
        var nodeUpEvent = nodeEvent as NodeUpEvent;
        Console.WriteLine("(Node is up, and it was last down at {0})", nodeUpEvent.LastNodeDownAt);
    }
}
```


## <a name="monitor-cluster-health"></a>Monitorowanie kondycji klastra
Service Fabric wprowadza [model kondycji](service-fabric-health-introduction.md) z jednostkami kondycji, na których składniki systemowe i alarmy mogą raportować lokalne warunki, które są monitorowane. [Magazyn kondycji](service-fabric-health-introduction.md#health-store) agreguje wszystkie dane dotyczące kondycji, aby określić, czy jednostki są w dobrej kondycji.

Klaster jest automatycznie wypełniany raportami kondycji wysyłanymi przez składniki systemowe. Więcej informacji można znaleźć w tematach [dotyczących rozwiązywania problemów przy użyciu raportów kondycji systemu](service-fabric-understand-and-troubleshoot-with-system-health-reports.md).

Service Fabric udostępnia zapytania dotyczące kondycji dla każdego z obsługiwanych [typów jednostek](service-fabric-health-introduction.md#health-entities-and-hierarchy). Dostęp do nich uzyskuje się za pośrednictwem interfejsu API, przy użyciu metod w [FabricClient. HealthManager](/dotnet/api/system.fabric.fabricclient.healthmanager), poleceń cmdlet programu POWERSHELL i REST. Te zapytania zwracają kompletne informacje o kondycji jednostki: zagregowany stan kondycji, zdarzenia kondycji jednostki, podrzędne Stany kondycji (gdy ma to zastosowanie), oceny w złej kondycji (gdy jednostka nie jest w dobrej kondycji) i podrzędne statystyki kondycji (jeśli ma to zastosowanie).

### <a name="get-cluster-health"></a>Pobierz kondycję klastra
[Polecenie cmdlet Get-ServiceFabricClusterHealth](/powershell/module/servicefabric/get-servicefabricclusterhealth) zwraca kondycję jednostki klastra i zawiera Stany kondycji aplikacji i węzłów (elementy podrzędne klastra).  Najpierw Połącz się z klastrem przy użyciu [polecenia cmdlet Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster).

Stan klastra to 11 węzłów, aplikacja systemowa i sieć szkieletowa:/głos skonfigurowany zgodnie z opisem.

Poniższy przykład pobiera kondycję klastra przy użyciu domyślnych zasad kondycji. 11 węzłów jest w dobrej kondycji, ale zagregowany stan kondycji klastra jest błąd, ponieważ aplikacja sieci szkieletowej:/głosuje błąd. Zwróć uwagę na to, jak oceny złej kondycji zawierają szczegóły dotyczące warunków, które wyzwoliły zagregowany stan kondycji.

```powershell
Get-ServiceFabricClusterHealth

AggregatedHealthState   : Error
UnhealthyEvaluations    : 
                          100% (1/1) applications are unhealthy. The evaluation tolerates 0% unhealthy applications.
                          
                          Application 'fabric:/Voting' is in Error.
                          
                            33% (1/3) deployed applications are unhealthy. The evaluation tolerates 0% unhealthy deployed applications.
                          
                            Deployed application on node '_nt2vm_3' is in Error.
                          
                                50% (1/2) deployed service packages are unhealthy.
                          
                                Service package for manifest 'VotingWebPkg' and service package activation ID '8723eb73-9b83-406b-9de3-172142ba15f3' is in Error.
                          
                                    'System.Hosting' reported Error for property 'CodePackageActivation:Code:SetupEntryPoint:131959376195593305'.
                                    There was an error during CodePackage activation.The service host terminated with exit code:1
                          
                          
NodeHealthStates        : 
                          NodeName              : _nt2vm_3
                          AggregatedHealthState : Ok
                          
                          NodeName              : _nt1vm_4
                          AggregatedHealthState : Ok
                          
                          NodeName              : _nt2vm_2
                          AggregatedHealthState : Ok
                          
                          NodeName              : _nt1vm_3
                          AggregatedHealthState : Ok
                          
                          NodeName              : _nt2vm_1
                          AggregatedHealthState : Ok
                          
                          NodeName              : _nt1vm_2
                          AggregatedHealthState : Ok
                          
                          NodeName              : _nt2vm_0
                          AggregatedHealthState : Ok
                          
                          NodeName              : _nt1vm_1
                          AggregatedHealthState : Ok
                          
                          NodeName              : _nt1vm_0
                          AggregatedHealthState : Ok
                          
                          NodeName              : _nt3vm_0
                          AggregatedHealthState : Ok
                          
                          NodeName              : _nt2vm_4
                          AggregatedHealthState : Ok
                          
ApplicationHealthStates : 
                          ApplicationName       : fabric:/System
                          AggregatedHealthState : Ok
                          
                          ApplicationName       : fabric:/Voting
                          AggregatedHealthState : Error
                          
HealthEvents            : None
HealthStatistics        : 
                          Node                  : 11 Ok, 0 Warning, 0 Error
                          Replica               : 4 Ok, 0 Warning, 0 Error
                          Partition             : 2 Ok, 0 Warning, 0 Error
                          Service               : 2 Ok, 0 Warning, 0 Error
                          DeployedServicePackage : 3 Ok, 1 Warning, 1 Error
                          DeployedApplication   : 1 Ok, 1 Warning, 1 Error
                          Application           : 0 Ok, 0 Warning, 1 Error
```

Poniższy przykład pobiera kondycję klastra przy użyciu niestandardowych zasad aplikacji. Filtruje wyniki, aby uzyskać tylko aplikacje i węzły z błędem lub ostrzeżeniem. W tym przykładzie nie są zwracane żadne węzły, ponieważ są one w dobrej kondycji. Tylko aplikacja sieci szkieletowej:/głosu szanuje filtr aplikacji. Ze względu na to, że zasady niestandardowe określają, jak są wyświetlane ostrzeżenia jako błędy aplikacji sieci szkieletowej:/głosowania, aplikacja jest szacowana jako błąd i dlatego jest klastrem.

```powershell
$appHealthPolicy = New-Object -TypeName System.Fabric.Health.ApplicationHealthPolicy
$appHealthPolicy.ConsiderWarningAsError = $true
$appHealthPolicyMap = New-Object -TypeName System.Fabric.Health.ApplicationHealthPolicyMap
$appUri1 = New-Object -TypeName System.Uri -ArgumentList "fabric:/Voting"
$appHealthPolicyMap.Add($appUri1, $appHealthPolicy)
Get-ServiceFabricClusterHealth -ApplicationHealthPolicyMap $appHealthPolicyMap -ApplicationsFilter "Warning,Error" -NodesFilter "Warning,Error" -ExcludeHealthStatistics

AggregatedHealthState   : Error
UnhealthyEvaluations    : 
                          100% (1/1) applications are unhealthy. The evaluation tolerates 0% unhealthy applications.
                          
                          Application 'fabric:/Voting' is in Error.
                          
                            100% (5/5) deployed applications are unhealthy. The evaluation tolerates 0% unhealthy deployed applications.
                          
                            Deployed application on node '_nt2vm_3' is in Error.
                          
                                50% (1/2) deployed service packages are unhealthy.
                          
                                Service package for manifest 'VotingWebPkg' and service package activation ID '8723eb73-9b83-406b-9de3-172142ba15f3' is in Error.
                          
                                    'System.Hosting' reported Error for property 'CodePackageActivation:Code:SetupEntryPoint:131959376195593305'.
                                    There was an error during CodePackage activation.The service host terminated with exit code:1
                          
                            Deployed application on node '_nt2vm_2' is in Error.
                          
                                50% (1/2) deployed service packages are unhealthy.
                          
                                Service package for manifest 'VotingWebPkg' and service package activation ID '2466f2f9-d5fd-410c-a6a4-5b1e00630cca' is in Error.
                          
                                    'System.Hosting' reported Error for property 'CodePackageActivation:Code:SetupEntryPoint:131959376486201388'.
                                    There was an error during CodePackage activation.The service host terminated with exit code:1
                          
                            Deployed application on node '_nt2vm_4' is in Error.
                          
                                100% (1/1) deployed service packages are unhealthy.
                          
                                Service package for manifest 'VotingWebPkg' and service package activation ID '5faa5201-eede-400a-865f-07f7f886aa32' is in Error.
                          
                                    'System.Hosting' reported Warning for property 'CodePackageActivation:Code:SetupEntryPoint:131959376207396204'. The evaluation treats 
                          Warning as Error.
                                    There was an error during CodePackage activation.The service host terminated with exit code:1
                          
                            Deployed application on node '_nt2vm_0' is in Error.
                          
                                100% (1/1) deployed service packages are unhealthy.
                          
                                Service package for manifest 'VotingWebPkg' and service package activation ID '204f1783-f774-4f3a-b371-d9983afaf059' is in Error.
                          
                                    'System.Hosting' reported Error for property 'CodePackageActivation:Code:SetupEntryPoint:131959375885791093'.
                                    There was an error during CodePackage activation.The service host terminated with exit code:1
                          
                            Deployed application on node '_nt3vm_0' is in Error.
                          
                                50% (1/2) deployed service packages are unhealthy.
                          
                                Service package for manifest 'VotingWebPkg' and service package activation ID '2533ae95-2d2a-4f8b-beef-41e13e4c0081' is in Error.
                          
                                    'System.Hosting' reported Error for property 'CodePackageActivation:Code:SetupEntryPoint:131959376108346272'.
                                    There was an error during CodePackage activation.The service host terminated with exit code:1                         
                          
NodeHealthStates        : None
ApplicationHealthStates : 
                          ApplicationName       : fabric:/Voting
                          AggregatedHealthState : Error
                          
HealthEvents            : None
```

### <a name="get-node-health"></a>Pobierz kondycję węzła
[Polecenie cmdlet Get-ServiceFabricNodeHealth](/powershell/module/servicefabric/get-servicefabricnodehealth) zwraca kondycję jednostki węzła i zawiera zdarzenia kondycji zgłoszone w węźle. Najpierw Połącz się z klastrem przy użyciu [polecenia cmdlet Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster). Poniższy przykład pobiera kondycję określonego węzła przy użyciu domyślnych zasad kondycji:

```powershell
Get-ServiceFabricNodeHealth _nt1vm_3
```

Poniższy przykład pobiera kondycję wszystkich węzłów w klastrze:
```powershell
Get-ServiceFabricNode | Get-ServiceFabricNodeHealth | select NodeName, AggregatedHealthState | ft -AutoSize
```

### <a name="get-system-service-health"></a>Pobierz kondycję usługi systemowej 

Uzyskaj zagregowaną kondycję usług systemowych:

```powershell
Get-ServiceFabricService -ApplicationName fabric:/System | Get-ServiceFabricServiceHealth | select ServiceName, AggregatedHealthState | ft -AutoSize
```

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Wyświetlanie zdarzeń Service Fabric
> * Wykonywanie zapytań dotyczących interfejsów API EventStore dla zdarzeń klastra
> * Monitorowanie infrastruktury/Zbieranie liczników wydajności
> * Wyświetlanie raportów o kondycji klastra

Następnie przejdź do następującego samouczka, aby dowiedzieć się, jak skalować klaster.
> [!div class="nextstepaction"]
> [Skalowanie klastra](service-fabric-tutorial-scale-cluster.md)

[durability]: service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster
[template]: https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Windows-3-NodeTypes-Secure-NSG/AzureDeploy.json
