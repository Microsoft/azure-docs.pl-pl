---
title: Jak wyświetlić zagregowaną kondycję jednostek usługi Azure Service Fabric
description: Opisuje sposób wykonywania zapytań, wyświetlania i szacowania zagregowanych kondycji jednostek Service Fabric platformy Azure za pomocą zapytań dotyczących kondycji i ogólnych zapytań.
author: georgewallace
ms.topic: conceptual
ms.date: 2/28/2018
ms.author: gwallace
ms.custom: devx-track-csharp
ms.openlocfilehash: eeb2fd94e6b98bc9d89be22501406db9a8ba7773
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89013167"
---
# <a name="view-service-fabric-health-reports"></a>Wyświetlanie raportów o kondycji Service Fabric
Usługa Azure Service Fabric wprowadza [model kondycji](service-fabric-health-introduction.md) z jednostkami kondycji, na których składniki systemowe i alarmy mogą raportować lokalne warunki, które są monitorowane. [Magazyn kondycji](service-fabric-health-introduction.md#health-store) agreguje wszystkie dane dotyczące kondycji, aby określić, czy jednostki są w dobrej kondycji.

Klaster jest automatycznie wypełniany raportami kondycji wysyłanymi przez składniki systemowe. Więcej informacji można znaleźć w tematach [dotyczących rozwiązywania problemów przy użyciu raportów kondycji systemu](service-fabric-understand-and-troubleshoot-with-system-health-reports.md).

Service Fabric zapewnia wiele sposobów uzyskania zagregowanej kondycji jednostek:

* [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) lub inne narzędzia do wizualizacji
* Zapytania dotyczące kondycji (za poorednictwem programu PowerShell, interfejsu API lub REST)
* Ogólne zapytania, które zwracają listę jednostek, które mają kondycję jako jedną z właściwości (za pomocą programu PowerShell, interfejsu API lub REST)

Aby zademonstrować te opcje, użyjmy lokalnego klastra z pięcioma węzłami i [sieci szkieletowej:/WORDCOUNT](https://github.com/Azure-Samples/service-fabric-wordcount/raw/master/WordCountV1.sfpkg). Aplikacja **sieci szkieletowej:/WORDCOUNT** zawiera dwie domyślne usługi, stanowe usługi typu `WordCountServiceType` i bezstanową usługę typu `WordCountWebServiceType` . Zmieniono żądanie, `ApplicationManifest.xml` Aby wymagało siedmiu replik docelowych dla usługi stanowej i jednej partycji. Ze względu na to, że w klastrze znajduje się tylko pięć węzłów, składniki systemowe zgłaszają ostrzeżenie na partycji usługi, ponieważ jest niższa od liczby docelowej.

```xml
<Service Name="WordCountService">
  <StatefulService ServiceTypeName="WordCountServiceType" TargetReplicaSetSize="7" MinReplicaSetSize="2">
    <UniformInt64Partition PartitionCount="[WordCountService_PartitionCount]" LowKey="1" HighKey="26" />
  </StatefulService>
</Service>
```

## <a name="health-in-service-fabric-explorer"></a>Kondycja w Service Fabric Explorer
Service Fabric Explorer udostępnia widok wizualizacji klastra. Na poniższej ilustracji widać, że:

* **Sieć szkieletowa aplikacji:/WORDCOUNT** jest czerwona (w przypadku błędu), ponieważ zawiera zdarzenie błędu zgłoszone przez **telealarmy** dla **dostępności**właściwości.
* Jedna z jej usług, **Sieć szkieletowa:/WORDCOUNT/usługi wordcountservice uległa** jest żółta (w ostrzeżeniu). Usługa jest skonfigurowana z siedmiu replik, a klaster ma pięć węzłów, więc nie można umieścić dwóch replik. Chociaż nie jest to tutaj widoczne, partycja usługi jest żółta ze względu na Raport systemowy `System.FM` informujący o tym, że `Partition is below target replica or instance count` . Żółta partycja wyzwala żółtą usługę.
* Ten klaster jest czerwony ze względu na czerwoną aplikację.

Ocena używa domyślnych zasad z manifestu klastra i manifestu aplikacji. Są to ścisłe zasady i nie dopuszczają żadnego błędu.

Widok klastra z Service Fabric Explorer:

![Widok klastra z Service Fabric Explorer.][1]

[1]: ./media/service-fabric-view-entities-aggregated-health/servicefabric-explorer-cluster-health.png


> [!NOTE]
> Przeczytaj więcej na temat [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).
>
>

## <a name="health-queries"></a>Zapytania dotyczące kondycji
Service Fabric udostępnia zapytania dotyczące kondycji dla każdego z obsługiwanych [typów jednostek](service-fabric-health-introduction.md#health-entities-and-hierarchy). Dostęp do nich uzyskuje się za pośrednictwem interfejsu API, przy użyciu metod w [FabricClient. HealthManager](/dotnet/api/system.fabric.fabricclient.healthmanager?view=azure-dotnet), poleceń cmdlet programu POWERSHELL i REST. Te zapytania zwracają kompletne informacje o kondycji jednostki: zagregowany stan kondycji, zdarzenia kondycji jednostki, podrzędne Stany kondycji (gdy ma to zastosowanie), oceny w złej kondycji (gdy jednostka nie jest w dobrej kondycji) i podrzędne statystyki kondycji (jeśli ma to zastosowanie).

> [!NOTE]
> Jednostka kondycji jest zwracana, gdy jest w pełni wypełniana w magazynie kondycji. Jednostka musi być aktywna (nieusunięta) i mieć Raport systemowy. Jego jednostki nadrzędne w łańcuchu hierarchii również muszą zawierać Raporty systemowe. Jeśli którykolwiek z tych warunków nie zostanie spełniony, zapytania o kondycji zwracają element [fabricexception](/dotnet/api/system.fabric.fabricexception) z [FabricErrorCode](/dotnet/api/system.fabric.fabricerrorcode) `FabricHealthEntityNotFound` , który pokazuje, dlaczego jednostka nie została zwrócona.
>
>

Zapytania o kondycję muszą być przekazywane w identyfikatorze jednostki, który zależy od typu jednostki. Zapytania akceptują opcjonalne parametry zasad dotyczących kondycji. Jeśli nie określono żadnych zasad dotyczących kondycji, do oceny są używane [zasady dotyczące kondycji](service-fabric-health-introduction.md#health-policies) z klastra lub manifestu aplikacji. Jeśli manifesty nie zawierają definicji zasad dotyczących kondycji, do oceny są używane domyślne zasady dotyczące kondycji. Domyślne zasady kondycji nie dopuszczają żadnych błędów. Zapytania akceptują również filtry zwracające tylko częściowe elementy podrzędne lub zdarzenia — te, które respektują określone filtry. Inny filtr umożliwia wykluczenie statystyk podrzędnych.

> [!NOTE]
> Filtry wyjściowe są stosowane po stronie serwera, więc rozmiar odpowiedzi komunikatu jest zmniejszany. Zalecamy użycie filtrów wyjściowych w celu ograniczenia zwracanych danych, a nie zastosowania filtrów po stronie klienta.
>
>

Kondycja jednostki zawiera:

* Zagregowany stan kondycji jednostki. Obliczane przez magazyn kondycji na podstawie raportów kondycji jednostki, podrzędnych Stanów kondycji (jeśli dotyczy) i zasad dotyczących kondycji. Przeczytaj więcej na temat [oceny kondycji jednostek](service-fabric-health-introduction.md#health-evaluation).  
* Zdarzenia dotyczące kondycji w jednostce.
* Kolekcja Stanów kondycji wszystkich elementów podrzędnych dla jednostek, które mogą mieć elementy podrzędne. Stany kondycji zawierają identyfikatory jednostek i zagregowany stan kondycji. Aby uzyskać pełną kondycję dla elementu podrzędnego, należy wywołać kondycję zapytania dla typu jednostki podrzędnej i przekazać identyfikator podrzędny.
* Oceny złej kondycji wskazujące na raport, który wyzwolił stan jednostki, jeśli jednostka nie jest w dobrej kondycji. Obliczenia są cykliczne, zawierające oceny kondycji dzieci, które wyzwoliły bieżący stan kondycji. Na przykład licznik alarm zgłosił błąd dla repliki. Kondycja aplikacji pokazuje ocenę złej kondycji z powodu usługi złej kondycji; Usługa jest w złej kondycji z powodu błędu partycji. partycja jest w złej kondycji ze względu na błędną replikę. Replika jest w złej kondycji ze względu na raport o kondycji błędów licznika alarmów.
* Statystyki kondycji dla wszystkich typów elementów podrzędnych obiektów, które mają elementy podrzędne. Na przykład kondycja klastra pokazuje całkowitą liczbę aplikacji, usług, partycji, replik i wdrożonych jednostek w klastrze. Kondycja usługi pokazuje łączną liczbę partycji i replik w ramach określonej usługi.

## <a name="get-cluster-health"></a>Pobierz kondycję klastra
Zwraca kondycję jednostki klastra i zawiera Stany kondycji aplikacji i węzłów (elementy podrzędne klastra). Dane wejściowe:

* Obowiązkowe Zasady kondycji klastra używane do szacowania węzłów i zdarzeń klastra.
* Obowiązkowe Mapa zasad kondycji aplikacji z zasadami kondycji używanymi do przesłania zasad manifestu aplikacji.
* Obowiązkowe Filtry dla zdarzeń, węzłów i aplikacji, które określają, które wpisy są interesujące i powinny być zwracane w wyniku (na przykład tylko błędy lub ostrzeżenia i błędy). Wszystkie zdarzenia, węzły i aplikacje są używane do oszacowania zagregowanej kondycji jednostki, niezależnie od filtra.
* Obowiązkowe Filtr, aby wykluczyć statystyki kondycji.
* Obowiązkowe Filtr w celu uwzględnienia statystyk kondycji systemu w statystykach kondycji. Dotyczy tylko sytuacji, gdy statystyki kondycji nie są wykluczone. Domyślnie statystyki kondycji obejmują tylko statystyki dla aplikacji użytkownika, a nie do aplikacji systemowej.

### <a name="api"></a>Interfejs API
Aby uzyskać kondycję klastra, Utwórz `FabricClient` i Wywołaj metodę [GetClusterHealthAsync](/dotnet/api/system.fabric.fabricclient.healthclient.getclusterhealthasync) na swoim serwerze **kondycji**.

Następujące wywołanie pobiera kondycję klastra:

```csharp
ClusterHealth clusterHealth = await fabricClient.HealthManager.GetClusterHealthAsync();
```

Poniższy kod pobiera kondycję klastra przy użyciu niestandardowych zasad dotyczących kondycji klastra i filtrów dla węzłów i aplikacji. Określa, że statystyki kondycji obejmują statystykę sieci szkieletowej:/System. Tworzy [ClusterHealthQueryDescription](/dotnet/api/system.fabric.description.clusterhealthquerydescription), który zawiera informacje wejściowe.

```csharp
var policy = new ClusterHealthPolicy()
{
    MaxPercentUnhealthyNodes = 20
};
var nodesFilter = new NodeHealthStatesFilter()
{
    HealthStateFilterValue = HealthStateFilter.Error | HealthStateFilter.Warning
};
var applicationsFilter = new ApplicationHealthStatesFilter()
{
    HealthStateFilterValue = HealthStateFilter.Error
};
var healthStatisticsFilter = new ClusterHealthStatisticsFilter()
{
    ExcludeHealthStatistics = false,
    IncludeSystemApplicationHealthStatistics = true
};
var queryDescription = new ClusterHealthQueryDescription()
{
    HealthPolicy = policy,
    ApplicationsFilter = applicationsFilter,
    NodesFilter = nodesFilter,
    HealthStatisticsFilter = healthStatisticsFilter
};

ClusterHealth clusterHealth = await fabricClient.HealthManager.GetClusterHealthAsync(queryDescription);
```

### <a name="powershell"></a>Program PowerShell
Polecenie cmdlet umożliwiające uzyskanie kondycji klastra to [Get-ServiceFabricClusterHealth](/powershell/module/servicefabric/get-servicefabricclusterhealth). Najpierw Połącz się z klastrem przy użyciu polecenia cmdlet [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) .

Stan klastra to pięć węzłów, aplikacja systemowa i sieć szkieletowa:/WordCount skonfigurowane zgodnie z opisem.

Poniższe polecenie cmdlet pobiera kondycję klastra przy użyciu domyślnych zasad kondycji. Zagregowany stan kondycji to ostrzeżenie, ponieważ aplikacja sieci szkieletowej:/WordCount jest w ostrzeżeniu. Zwróć uwagę na to, jak oceny złej kondycji zawierają szczegóły dotyczące warunków, które wyzwoliły zagregowany stan kondycji.

```xml
PS D:\ServiceFabric> Get-ServiceFabricClusterHealth


AggregatedHealthState   : Warning
UnhealthyEvaluations    : 
                          Unhealthy applications: 100% (1/1), MaxPercentUnhealthyApplications=0%.

                          Unhealthy application: ApplicationName='fabric:/WordCount', AggregatedHealthState='Warning'.

                            Unhealthy services: 100% (1/1), ServiceType='WordCountServiceType', MaxPercentUnhealthyServices=0%.

                            Unhealthy service: ServiceName='fabric:/WordCount/WordCountService', AggregatedHealthState='Warning'.

                                Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                Unhealthy partition: PartitionId='af2e3e44-a8f8-45ac-9f31-4093eb897600', AggregatedHealthState='Warning'.

                                    Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=false.


NodeHealthStates        : 
                          NodeName              : _Node_4
                          AggregatedHealthState : Ok

                          NodeName              : _Node_3
                          AggregatedHealthState : Ok

                          NodeName              : _Node_2
                          AggregatedHealthState : Ok

                          NodeName              : _Node_1
                          AggregatedHealthState : Ok

                          NodeName              : _Node_0
                          AggregatedHealthState : Ok

ApplicationHealthStates : 
                          ApplicationName       : fabric:/System
                          AggregatedHealthState : Ok

                          ApplicationName       : fabric:/WordCount
                          AggregatedHealthState : Warning

HealthEvents            : None
HealthStatistics        : 
                          Node                  : 5 Ok, 0 Warning, 0 Error
                          Replica               : 6 Ok, 0 Warning, 0 Error
                          Partition             : 1 Ok, 1 Warning, 0 Error
                          Service               : 1 Ok, 1 Warning, 0 Error
                          DeployedServicePackage : 6 Ok, 0 Warning, 0 Error
                          DeployedApplication   : 5 Ok, 0 Warning, 0 Error
                          Application           : 0 Ok, 1 Warning, 0 Error
```

Następujące polecenie cmdlet programu PowerShell pobiera kondycję klastra przy użyciu niestandardowych zasad aplikacji. Filtruje wyniki, aby uzyskać tylko aplikacje i węzły z błędem lub ostrzeżeniem. W związku z tym nie są zwracane żadne węzły, ponieważ są one w dobrej kondycji. Tylko aplikacja Fabric:/WordCount uwzględnia filtr aplikacji. Ponieważ zasady niestandardowe określają, że są uwzględniane ostrzeżenia jako błędy dla aplikacji sieci szkieletowej:/WordCount, aplikacja jest szacowana jako błąd i dlatego jest klastrem.

```powershell
PS D:\ServiceFabric> $appHealthPolicy = New-Object -TypeName System.Fabric.Health.ApplicationHealthPolicy
$appHealthPolicy.ConsiderWarningAsError = $true
$appHealthPolicyMap = New-Object -TypeName System.Fabric.Health.ApplicationHealthPolicyMap
$appUri1 = New-Object -TypeName System.Uri -ArgumentList "fabric:/WordCount"
$appHealthPolicyMap.Add($appUri1, $appHealthPolicy)
Get-ServiceFabricClusterHealth -ApplicationHealthPolicyMap $appHealthPolicyMap -ApplicationsFilter "Warning,Error" -NodesFilter "Warning,Error" -ExcludeHealthStatistics


AggregatedHealthState   : Error
UnhealthyEvaluations    : 
                          Unhealthy applications: 100% (1/1), MaxPercentUnhealthyApplications=0%.

                          Unhealthy application: ApplicationName='fabric:/WordCount', AggregatedHealthState='Error'.

                            Unhealthy services: 100% (1/1), ServiceType='WordCountServiceType', MaxPercentUnhealthyServices=0%.

                            Unhealthy service: ServiceName='fabric:/WordCount/WordCountService', AggregatedHealthState='Error'.

                                Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                Unhealthy partition: PartitionId='af2e3e44-a8f8-45ac-9f31-4093eb897600', AggregatedHealthState='Error'.

                                    Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=true.


NodeHealthStates        : None
ApplicationHealthStates : 
                          ApplicationName       : fabric:/WordCount
                          AggregatedHealthState : Error

HealthEvents            : None
```

### <a name="rest"></a>REST
Kondycję klastra można uzyskać za pomocą [żądania GET](/rest/api/servicefabric/get-the-health-of-a-cluster) lub [żądania post](/rest/api/servicefabric/get-the-health-of-a-cluster-by-using-a-health-policy) , które zawiera zasady dotyczące kondycji opisane w treści.

## <a name="get-node-health"></a>Pobierz kondycję węzła
Zwraca kondycję jednostki węzła i zawiera zdarzenia kondycji zgłoszone w węźle. Dane wejściowe:

* Potrzeb Nazwa węzła, który identyfikuje węzeł.
* Obowiązkowe Ustawienia zasad kondycji klastra służące do oszacowania kondycji.
* Obowiązkowe Filtry dla zdarzeń, które określają, które wpisy są interesujące i powinny być zwracane w wyniku (na przykład tylko błędy lub ostrzeżenia i błędy). Wszystkie zdarzenia są używane do obliczania kondycji zagregowanej jednostki, niezależnie od filtru.

### <a name="api"></a>Interfejs API
Aby uzyskać kondycję węzłów za pomocą interfejsu API, Utwórz `FabricClient` i Wywołaj metodę [GetNodeHealthAsync](/dotnet/api/system.fabric.fabricclient.healthclient.getnodehealthasync) na swoim serwerze kondycji.

Poniższy kod pobiera kondycję węzła dla określonej nazwy węzła:

```csharp
NodeHealth nodeHealth = await fabricClient.HealthManager.GetNodeHealthAsync(nodeName);
```

Poniższy kod pobiera kondycję węzła dla określonej nazwy węzła i przekazuje je do filtru zdarzeń i zasad niestandardowych za pomocą [NodeHealthQueryDescription](/dotnet/api/system.fabric.description.nodehealthquerydescription):

```csharp
var queryDescription = new NodeHealthQueryDescription(nodeName)
{
    HealthPolicy = new ClusterHealthPolicy() {  ConsiderWarningAsError = true },
    EventsFilter = new HealthEventsFilter() { HealthStateFilterValue = HealthStateFilter.Warning },
};

NodeHealth nodeHealth = await fabricClient.HealthManager.GetNodeHealthAsync(queryDescription);
```

### <a name="powershell"></a>Program PowerShell
Polecenie cmdlet do pobierania kondycji węzła to [Get-ServiceFabricNodeHealth](/powershell/module/servicefabric/get-servicefabricnodehealth). Najpierw Połącz się z klastrem przy użyciu polecenia cmdlet [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) .
Następujące polecenie cmdlet pobiera kondycję węzła przy użyciu domyślnych zasad kondycji:

```powershell
PS D:\ServiceFabric> Get-ServiceFabricNodeHealth _Node_1


NodeName              : _Node_1
AggregatedHealthState : Ok
HealthEvents          : 
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 3
                        SentAt                : 7/13/2017 4:39:23 PM
                        ReceivedAt            : 7/13/2017 4:40:47 PM
                        TTL                   : Infinite
                        Description           : Fabric node is up.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 7/13/2017 4:40:47 PM, LastWarning = 1/1/0001 12:00:00 AM
```

Następujące polecenie cmdlet pobiera kondycję wszystkich węzłów w klastrze:

```powershell
PS D:\ServiceFabric> Get-ServiceFabricNode | Get-ServiceFabricNodeHealth | select NodeName, AggregatedHealthState | ft -AutoSize

NodeName AggregatedHealthState
-------- ---------------------
_Node_4                     Ok
_Node_3                     Ok
_Node_2                     Ok
_Node_1                     Ok
_Node_0                     Ok
```

### <a name="rest"></a>REST
Kondycję węzła można uzyskać za pomocą [żądania GET](/rest/api/servicefabric/get-the-health-of-a-node) lub [żądania post](/rest/api/servicefabric/get-the-health-of-a-node-by-using-a-health-policy) , które zawiera zasady dotyczące kondycji opisane w treści.

## <a name="get-application-health"></a>Pobierz kondycję aplikacji
Zwraca kondycję jednostki aplikacji. Zawiera Stany kondycji wdrożonej aplikacji i elementów podrzędnych usługi. Dane wejściowe:

* Potrzeb Nazwa aplikacji (URI) identyfikująca aplikację.
* Obowiązkowe Zasady dotyczące kondycji aplikacji używane do przesłania zasad manifestu aplikacji.
* Obowiązkowe Filtry dla zdarzeń, usług i wdrożonych aplikacji, które określają, które wpisy są interesujące i powinny być zwracane w wyniku (na przykład tylko błędy lub ostrzeżenia i błędy). Wszystkie zdarzenia, usługi i wdrożone aplikacje są używane do obliczania kondycji zagregowanej jednostki, niezależnie od filtra.
* Obowiązkowe Filtr, aby wykluczyć statystyki kondycji. Jeśli nie zostanie określony, statystyki kondycji obejmują wartość OK, ostrzeżenie i liczbę błędów dla wszystkich elementów podrzędnych aplikacji: usługi, partycje, repliki, wdrożone aplikacje i wdrożone pakiety usług.

### <a name="api"></a>Interfejs API
Aby uzyskać kondycję aplikacji, Utwórz `FabricClient` i Wywołaj metodę [GetApplicationHealthAsync](/dotnet/api/system.fabric.fabricclient.healthclient.getapplicationhealthasync) na swoim serwerze kondycji.

Poniższy kod pobiera kondycję aplikacji dla określonej nazwy aplikacji (URI):

```csharp
ApplicationHealth applicationHealth = await fabricClient.HealthManager.GetApplicationHealthAsync(applicationName);
```

Poniższy kod pobiera kondycję aplikacji dla określonej nazwy aplikacji (URI) z filtrami i zasadami niestandardowymi określonymi za pośrednictwem [ApplicationHealthQueryDescription](/dotnet/api/system.fabric.description.applicationhealthquerydescription).

```csharp
HealthStateFilter warningAndErrors = HealthStateFilter.Error | HealthStateFilter.Warning;
var serviceTypePolicy = new ServiceTypeHealthPolicy()
{
    MaxPercentUnhealthyPartitionsPerService = 0,
    MaxPercentUnhealthyReplicasPerPartition = 5,
    MaxPercentUnhealthyServices = 0,
};
var policy = new ApplicationHealthPolicy()
{
    ConsiderWarningAsError = false,
    DefaultServiceTypeHealthPolicy = serviceTypePolicy,
    MaxPercentUnhealthyDeployedApplications = 0,
};

var queryDescription = new ApplicationHealthQueryDescription(applicationName)
{
    HealthPolicy = policy,
    EventsFilter = new HealthEventsFilter() { HealthStateFilterValue = warningAndErrors },
    ServicesFilter = new ServiceHealthStatesFilter() { HealthStateFilterValue = warningAndErrors },
    DeployedApplicationsFilter = new DeployedApplicationHealthStatesFilter() { HealthStateFilterValue = warningAndErrors },
};

ApplicationHealth applicationHealth = await fabricClient.HealthManager.GetApplicationHealthAsync(queryDescription);
```

### <a name="powershell"></a>Program PowerShell
Polecenie cmdlet umożliwiające uzyskanie kondycji aplikacji to [Get-ServiceFabricApplicationHealth](/powershell/module/servicefabric/get-servicefabricapplicationhealth?view=azureservicefabricps). Najpierw Połącz się z klastrem przy użyciu polecenia cmdlet [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) .

Następujące polecenie cmdlet zwraca kondycję **sieci szkieletowej:/WORDCOUNT** :

```powershell
PS D:\ServiceFabric> Get-ServiceFabricApplicationHealth fabric:/WordCount


ApplicationName                 : fabric:/WordCount
AggregatedHealthState           : Warning
UnhealthyEvaluations            : 
                                  Unhealthy services: 100% (1/1), ServiceType='WordCountServiceType', MaxPercentUnhealthyServices=0%.

                                  Unhealthy service: ServiceName='fabric:/WordCount/WordCountService', AggregatedHealthState='Warning'.

                                    Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                    Unhealthy partition: PartitionId='af2e3e44-a8f8-45ac-9f31-4093eb897600', AggregatedHealthState='Warning'.

                                        Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=false.

ServiceHealthStates             : 
                                  ServiceName           : fabric:/WordCount/WordCountWebService
                                  AggregatedHealthState : Ok

                                  ServiceName           : fabric:/WordCount/WordCountService
                                  AggregatedHealthState : Warning

DeployedApplicationHealthStates : 
                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_4
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_3
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_0
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_2
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_1
                                  AggregatedHealthState : Ok

HealthEvents                    : 
                                  SourceId              : System.CM
                                  Property              : State
                                  HealthState           : Ok
                                  SequenceNumber        : 282
                                  SentAt                : 7/13/2017 5:57:05 PM
                                  ReceivedAt            : 7/13/2017 5:57:05 PM
                                  TTL                   : Infinite
                                  Description           : Application has been created.
                                  RemoveWhenExpired     : False
                                  IsExpired             : False
                                  Transitions           : Error->Ok = 7/13/2017 5:57:05 PM, LastWarning = 1/1/0001 12:00:00 AM

HealthStatistics                : 
                                  Replica               : 6 Ok, 0 Warning, 0 Error
                                  Partition             : 1 Ok, 1 Warning, 0 Error
                                  Service               : 1 Ok, 1 Warning, 0 Error
                                  DeployedServicePackage : 6 Ok, 0 Warning, 0 Error
                                  DeployedApplication   : 5 Ok, 0 Warning, 0 Error
```

Następujące polecenie cmdlet programu PowerShell kończy się w zasadach niestandardowych. Filtruje również elementy podrzędne i zdarzenia.

```powershell
PS D:\ServiceFabric> Get-ServiceFabricApplicationHealth -ApplicationName fabric:/WordCount -ConsiderWarningAsError $true -ServicesFilter Error -EventsFilter Error -DeployedApplicationsFilter Error -ExcludeHealthStatistics


ApplicationName                 : fabric:/WordCount
AggregatedHealthState           : Error
UnhealthyEvaluations            : 
                                  Unhealthy services: 100% (1/1), ServiceType='WordCountServiceType', MaxPercentUnhealthyServices=0%.

                                  Unhealthy service: ServiceName='fabric:/WordCount/WordCountService', AggregatedHealthState='Error'.

                                    Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                    Unhealthy partition: PartitionId='af2e3e44-a8f8-45ac-9f31-4093eb897600', AggregatedHealthState='Error'.

                                        Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=true.

ServiceHealthStates             : 
                                  ServiceName           : fabric:/WordCount/WordCountService
                                  AggregatedHealthState : Error

DeployedApplicationHealthStates : None
HealthEvents                    : None
```

### <a name="rest"></a>REST
Kondycję aplikacji można uzyskać za pomocą [żądania GET](/rest/api/servicefabric/get-the-health-of-an-application) lub [żądania post](/rest/api/servicefabric/get-the-health-of-an-application-by-using-an-application-health-policy) , które zawiera zasady dotyczące kondycji opisane w treści.

## <a name="get-service-health"></a>Pobierz kondycję usługi
Zwraca kondycję jednostki usługi. Zawiera Stany kondycji partycji. Dane wejściowe:

* Potrzeb Nazwa usługi (URI) identyfikująca usługę.
* Obowiązkowe Zasady dotyczące kondycji aplikacji używane do przesłania zasad manifestu aplikacji.
* Obowiązkowe Filtry zdarzeń i partycji, które określają, które wpisy są interesujące i powinny być zwracane w wyniku (na przykład tylko błędy lub ostrzeżenia i błędy). Wszystkie zdarzenia i partycje są używane do oszacowania zagregowanej kondycji jednostki, niezależnie od filtra.
* Obowiązkowe Filtr, aby wykluczyć statystyki kondycji. Jeśli nie zostanie określony, statystyki kondycji wyświetlają wartość OK, ostrzeżenie i licznik błędów dla wszystkich partycji i replik usługi.

### <a name="api"></a>Interfejs API
Aby uzyskać kondycję usługi za pomocą interfejsu API, Utwórz `FabricClient` i Wywołaj metodę [GetServiceHealthAsync](/dotnet/api/system.fabric.fabricclient.healthclient.getservicehealthasync) na swoim serwerze kondycji.

Poniższy przykład pobiera kondycję usługi z określoną nazwą usługi (URI):

```csharp
ServiceHealth serviceHealth = await fabricClient.HealthManager.GetServiceHealthAsync(serviceName);
```

Poniższy kod pobiera kondycję usługi dla określonej nazwy usługi (URI), określając filtry i zasady niestandardowe za pośrednictwem [ServiceHealthQueryDescription](/dotnet/api/system.fabric.description.servicehealthquerydescription):

```csharp
var queryDescription = new ServiceHealthQueryDescription(serviceName)
{
    EventsFilter = new HealthEventsFilter() { HealthStateFilterValue = HealthStateFilter.All },
    PartitionsFilter = new PartitionHealthStatesFilter() { HealthStateFilterValue = HealthStateFilter.Error },
};

ServiceHealth serviceHealth = await fabricClient.HealthManager.GetServiceHealthAsync(queryDescription);
```

### <a name="powershell"></a>Program PowerShell
Polecenie cmdlet umożliwiające uzyskanie kondycji usługi to [Get-ServiceFabricServiceHealth](/powershell/module/servicefabric/get-servicefabricservicehealth). Najpierw Połącz się z klastrem przy użyciu polecenia cmdlet [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) .

Następujące polecenie cmdlet pobiera kondycję usługi przy użyciu domyślnych zasad kondycji:

```powershell
PS D:\ServiceFabric> Get-ServiceFabricServiceHealth -ServiceName fabric:/WordCount/WordCountService


ServiceName           : fabric:/WordCount/WordCountService
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                        Unhealthy partition: PartitionId='af2e3e44-a8f8-45ac-9f31-4093eb897600', AggregatedHealthState='Warning'.

                            Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=false.

PartitionHealthStates : 
                        PartitionId           : af2e3e44-a8f8-45ac-9f31-4093eb897600
                        AggregatedHealthState : Warning

HealthEvents          : 
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 15
                        SentAt                : 7/13/2017 5:57:05 PM
                        ReceivedAt            : 7/13/2017 5:57:18 PM
                        TTL                   : Infinite
                        Description           : Service has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 7/13/2017 5:57:18 PM, LastWarning = 1/1/0001 12:00:00 AM

HealthStatistics      : 
                        Replica               : 5 Ok, 0 Warning, 0 Error
                        Partition             : 0 Ok, 1 Warning, 0 Error
```

### <a name="rest"></a>REST
Kondycję usługi można uzyskać za pomocą [żądania GET](/rest/api/servicefabric/get-the-health-of-a-service) lub [żądania post](/rest/api/servicefabric/get-the-health-of-a-service-by-using-a-health-policy) , które zawiera zasady dotyczące kondycji opisane w treści.

## <a name="get-partition-health"></a>Pobierz kondycję partycji
Zwraca kondycję jednostki partycji. Zawiera ona Stany kondycji repliki. Dane wejściowe:

* Potrzeb Identyfikator partycji (GUID) identyfikujący partycję.
* Obowiązkowe Zasady dotyczące kondycji aplikacji używane do przesłania zasad manifestu aplikacji.
* Obowiązkowe Filtry zdarzeń i replik, które określają, które wpisy są interesujące i powinny być zwracane w wyniku (na przykład tylko błędy lub ostrzeżenia i błędy). Wszystkie zdarzenia i repliki są używane do oszacowania zagregowanej kondycji jednostki, niezależnie od filtra.
* Obowiązkowe Filtr, aby wykluczyć statystyki kondycji. Jeśli nie zostanie określony, statystyki kondycji pokazują, ile replik jest prawidłowych, ostrzeżeń i Stanów błędów.

### <a name="api"></a>Interfejs API
Aby uzyskać kondycję partycji za pomocą interfejsu API, Utwórz `FabricClient` i Wywołaj metodę [GetPartitionHealthAsync](/dotnet/api/system.fabric.fabricclient.healthclient.getpartitionhealthasync) na swoim serwerze kondycji. Aby określić parametry opcjonalne, Utwórz [PartitionHealthQueryDescription](/dotnet/api/system.fabric.description.partitionhealthquerydescription).

```csharp
PartitionHealth partitionHealth = await fabricClient.HealthManager.GetPartitionHealthAsync(partitionId);
```

### <a name="powershell"></a>Program PowerShell
Polecenie cmdlet pobierania kondycji partycji to [Get-ServiceFabricPartitionHealth](/powershell/module/servicefabric/get-servicefabricpartitionhealth). Najpierw Połącz się z klastrem przy użyciu polecenia cmdlet [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) .

Poniższe polecenie cmdlet pobiera kondycję wszystkich partycji **sieci szkieletowej:/WORDCOUNT/usługi wordcountservice uległa** i filtruje stan kondycji repliki:

```powershell
PS D:\ServiceFabric> Get-ServiceFabricPartition fabric:/WordCount/WordCountService | Get-ServiceFabricPartitionHealth -ReplicasFilter None

PartitionId           : af2e3e44-a8f8-45ac-9f31-4093eb897600
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=false.

ReplicaHealthStates   : None
HealthEvents          : 
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Warning
                        SequenceNumber        : 72
                        SentAt                : 7/13/2017 5:57:29 PM
                        ReceivedAt            : 7/13/2017 5:57:48 PM
                        TTL                   : Infinite
                        Description           : Partition is below target replica or instance count.
                        fabric:/WordCount/WordCountService 7 2 af2e3e44-a8f8-45ac-9f31-4093eb897600
                          N/P RD _Node_2 Up 131444422260002646
                          N/S RD _Node_4 Up 131444422293113678
                          N/S RD _Node_3 Up 131444422293113679
                          N/S RD _Node_1 Up 131444422293118720
                          N/S RD _Node_0 Up 131444422293118721
                          (Showing 5 out of 5 replicas. Total available replicas: 5.)

                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Ok->Warning = 7/13/2017 5:57:48 PM, LastError = 1/1/0001 12:00:00 AM

                        SourceId              : System.PLB
                        Property              : ServiceReplicaUnplacedHealth_Secondary_af2e3e44-a8f8-45ac-9f31-4093eb897600
                        HealthState           : Warning
                        SequenceNumber        : 131444445174851664
                        SentAt                : 7/13/2017 6:35:17 PM
                        ReceivedAt            : 7/13/2017 6:35:18 PM
                        TTL                   : 00:01:05
                        Description           : The Load Balancer was unable to find a placement for one or more of the Service's Replicas:
                        Secondary replica could not be placed due to the following constraints and properties:  
                        TargetReplicaSetSize: 7
                        Placement Constraint: N/A
                        Parent Service: N/A

                        Constraint Elimination Sequence:
                        Existing Secondary Replicas eliminated 4 possible node(s) for placement -- 1/5 node(s) remain.
                        Existing Primary Replica eliminated 1 possible node(s) for placement -- 0/5 node(s) remain.

                        Nodes Eliminated By Constraints:

                        Existing Secondary Replicas -- Nodes with Partition's Existing Secondary Replicas/Instances:
                        --
                        FaultDomain:fd:/4 NodeName:_Node_4 NodeType:NodeType4 UpgradeDomain:4 UpgradeDomain: ud:/4 Deactivation Intent/Status: None/None
                        FaultDomain:fd:/3 NodeName:_Node_3 NodeType:NodeType3 UpgradeDomain:3 UpgradeDomain: ud:/3 Deactivation Intent/Status: None/None
                        FaultDomain:fd:/1 NodeName:_Node_1 NodeType:NodeType1 UpgradeDomain:1 UpgradeDomain: ud:/1 Deactivation Intent/Status: None/None
                        FaultDomain:fd:/0 NodeName:_Node_0 NodeType:NodeType0 UpgradeDomain:0 UpgradeDomain: ud:/0 Deactivation Intent/Status: None/None

                        Existing Primary Replica -- Nodes with Partition's Existing Primary Replica or Secondary Replicas:
                        --
                        FaultDomain:fd:/2 NodeName:_Node_2 NodeType:NodeType2 UpgradeDomain:2 UpgradeDomain: ud:/2 Deactivation Intent/Status: None/None


                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : Error->Warning = 7/13/2017 5:57:48 PM, LastOk = 1/1/0001 12:00:00 AM

HealthStatistics      : 
                        Replica               : 5 Ok, 0 Warning, 0 Error
```

### <a name="rest"></a>REST
Kondycję partycji można uzyskać za pomocą [żądania GET](/rest/api/servicefabric/sfclient-api-getpartitionhealth) lub [żądania post](/rest/api/servicefabric/get-the-health-of-a-partition-by-using-a-health-policy) , które zawiera zasady dotyczące kondycji opisane w treści.

## <a name="get-replica-health"></a>Pobierz kondycję repliki
Zwraca prawidłowość repliki usługi stanowej lub wystąpienia usługi bezstanowej. Dane wejściowe:

* Potrzeb Identyfikator partycji (GUID) i identyfikator repliki, która identyfikuje replikę.
* Obowiązkowe Parametry zasad dotyczących kondycji aplikacji używane do przesłania zasad manifestu aplikacji.
* Obowiązkowe Filtry dla zdarzeń, które określają, które wpisy są interesujące i powinny być zwracane w wyniku (na przykład tylko błędy lub ostrzeżenia i błędy). Wszystkie zdarzenia są używane do obliczania kondycji zagregowanej jednostki, niezależnie od filtru.

### <a name="api"></a>Interfejs API
Aby uzyskać kondycję repliki za pomocą interfejsu API, Utwórz `FabricClient` i Wywołaj metodę [GetReplicaHealthAsync](/dotnet/api/system.fabric.fabricclient.healthclient.getreplicahealthasync) na swoim serwerze kondycji. Aby określić parametry zaawansowane, użyj [ReplicaHealthQueryDescription](/dotnet/api/system.fabric.description.replicahealthquerydescription).

```csharp
ReplicaHealth replicaHealth = await fabricClient.HealthManager.GetReplicaHealthAsync(partitionId, replicaId);
```

### <a name="powershell"></a>Program PowerShell
Polecenie cmdlet umożliwiające uzyskanie kondycji repliki to [Get-ServiceFabricReplicaHealth](/powershell/module/servicefabric/get-servicefabricreplicahealth). Najpierw Połącz się z klastrem przy użyciu polecenia cmdlet [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) .

Poniższe polecenie cmdlet pobiera kondycję repliki podstawowej dla wszystkich partycji usługi:

```powershell
PS D:\ServiceFabric> Get-ServiceFabricPartition fabric:/WordCount/WordCountService | Get-ServiceFabricReplica | where {$_.ReplicaRole -eq "Primary"} | Get-ServiceFabricReplicaHealth


PartitionId           : af2e3e44-a8f8-45ac-9f31-4093eb897600
ReplicaId             : 131444422260002646
AggregatedHealthState : Ok
HealthEvents          : 
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 131444422263668344
                        SentAt                : 7/13/2017 5:57:06 PM
                        ReceivedAt            : 7/13/2017 5:57:18 PM
                        TTL                   : Infinite
                        Description           : Replica has been created._Node_2
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 7/13/2017 5:57:18 PM, LastWarning = 1/1/0001 12:00:00 AM
```

### <a name="rest"></a>REST
Kondycję repliki można uzyskać za pomocą [żądania GET](/rest/api/servicefabric/get-the-health-of-a-replica) lub [żądania post](/rest/api/servicefabric/get-the-health-of-a-replica-by-using-a-health-policy) , które zawiera zasady dotyczące kondycji opisane w treści.

## <a name="get-deployed-application-health"></a>Pobierz kondycję wdrożonej aplikacji
Zwraca kondycję aplikacji wdrożonej w jednostce węzła. Zawiera wdrożone Stany kondycji pakietów usługi. Dane wejściowe:

* Potrzeb Nazwa aplikacji (URI) i nazwa węzła (ciąg), które identyfikują wdrożoną aplikację.
* Obowiązkowe Zasady dotyczące kondycji aplikacji używane do przesłania zasad manifestu aplikacji.
* Obowiązkowe Filtry zdarzeń i wdrożonych pakietów usług, które określają, które wpisy są interesujące i powinny być zwracane w wyniku (na przykład tylko błędy lub ostrzeżenia i błędy). Wszystkie zdarzenia i wdrożone pakiety usług są używane do obliczania kondycji zagregowanej jednostki, niezależnie od filtra.
* Obowiązkowe Filtr, aby wykluczyć statystyki kondycji. Jeśli nie zostanie określony, statystyki kondycji przedstawiają liczbę wdrożonych pakietów usługi w Stanach prawidłowy, ostrzegawczy i kondycji błędów.

### <a name="api"></a>Interfejs API
Aby uzyskać kondycję aplikacji wdrożonej w węźle za pomocą interfejsu API, Utwórz `FabricClient` i Wywołaj metodę [GetDeployedApplicationHealthAsync](/dotnet/api/system.fabric.fabricclient.healthclient.getdeployedapplicationhealthasync) na swoim serwerze kondycji. Aby określić parametry opcjonalne, użyj [DeployedApplicationHealthQueryDescription](/dotnet/api/system.fabric.description.deployedapplicationhealthquerydescription).

```csharp
DeployedApplicationHealth health = await fabricClient.HealthManager.GetDeployedApplicationHealthAsync(
    new DeployedApplicationHealthQueryDescription(applicationName, nodeName));
```

### <a name="powershell"></a>Program PowerShell
Polecenie cmdlet umożliwiające uzyskanie wdrożonej kondycji aplikacji to [Get-ServiceFabricDeployedApplicationHealth](/powershell/module/servicefabric/get-servicefabricdeployedapplicationhealth?view=azureservicefabricps). Najpierw Połącz się z klastrem przy użyciu polecenia cmdlet [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) . Aby dowiedzieć się, gdzie jest wdrożona aplikacja, uruchom polecenie [Get-ServiceFabricApplicationHealth](/powershell/module/servicefabric/get-servicefabricapplicationhealth?view=azureservicefabricps) i poszukaj elementów podrzędnych wdrożonych aplikacji.

Poniższe polecenie cmdlet pobiera kondycję aplikacji **sieci szkieletowej:/WORDCOUNT** wdrożoną na **_Node_2**.

```powershell
PS D:\ServiceFabric> Get-ServiceFabricDeployedApplicationHealth -ApplicationName fabric:/WordCount -NodeName _Node_0


ApplicationName                    : fabric:/WordCount
NodeName                           : _Node_0
AggregatedHealthState              : Ok
DeployedServicePackageHealthStates : 
                                     ServiceManifestName   : WordCountServicePkg
                                     ServicePackageActivationId : 
                                     NodeName              : _Node_0
                                     AggregatedHealthState : Ok

                                     ServiceManifestName   : WordCountWebServicePkg
                                     ServicePackageActivationId : 
                                     NodeName              : _Node_0
                                     AggregatedHealthState : Ok

HealthEvents                       : 
                                     SourceId              : System.Hosting
                                     Property              : Activation
                                     HealthState           : Ok
                                     SequenceNumber        : 131444422261848308
                                     SentAt                : 7/13/2017 5:57:06 PM
                                     ReceivedAt            : 7/13/2017 5:57:17 PM
                                     TTL                   : Infinite
                                     Description           : The application was activated successfully.
                                     RemoveWhenExpired     : False
                                     IsExpired             : False
                                     Transitions           : Error->Ok = 7/13/2017 5:57:17 PM, LastWarning = 1/1/0001 12:00:00 AM

HealthStatistics                   : 
                                     DeployedServicePackage : 2 Ok, 0 Warning, 0 Error
```

### <a name="rest"></a>REST
Możesz uzyskać wdrożoną kondycję aplikacji za pomocą [żądania GET](/rest/api/servicefabric/get-the-health-of-a-deployed-application) lub [żądania post](/rest/api/servicefabric/get-the-health-of-a-deployed-application-by-using-a-health-policy) , które zawiera zasady dotyczące kondycji opisane w treści.

## <a name="get-deployed-service-package-health"></a>Pobierz kondycję wdrożonego pakietu usługi
Zwraca kondycję wdrożonej jednostki pakietu usługi. Dane wejściowe:

* Potrzeb Nazwa aplikacji (URI), nazwa węzła (ciąg) i nazwa manifestu usługi (ciąg), które identyfikują wdrożony pakiet usługi.
* Obowiązkowe Zasady dotyczące kondycji aplikacji używane do przesłania zasad manifestu aplikacji.
* Obowiązkowe Filtry dla zdarzeń, które określają, które wpisy są interesujące i powinny być zwracane w wyniku (na przykład tylko błędy lub ostrzeżenia i błędy). Wszystkie zdarzenia są używane do obliczania kondycji zagregowanej jednostki, niezależnie od filtru.

### <a name="api"></a>Interfejs API
Aby uzyskać kondycję wdrożonego pakietu usługi za pomocą interfejsu API, Utwórz `FabricClient` i Wywołaj metodę [GetDeployedServicePackageHealthAsync](/dotnet/api/system.fabric.fabricclient.healthclient.getdeployedservicepackagehealthasync) na swoim serwerze kondycji. Aby określić parametry opcjonalne, użyj [DeployedServicePackageHealthQueryDescription](/dotnet/api/system.fabric.description.deployedservicepackagehealthquerydescription).

```csharp
DeployedServicePackageHealth health = await fabricClient.HealthManager.GetDeployedServicePackageHealthAsync(
    new DeployedServicePackageHealthQueryDescription(applicationName, nodeName, serviceManifestName));
```

### <a name="powershell"></a>Program PowerShell
Polecenie cmdlet pobrania kondycji pakietu wdrożonej usługi ma wartość [Get-ServiceFabricDeployedServicePackageHealth](/powershell/module/servicefabric/get-servicefabricdeployedservicepackagehealth). Najpierw Połącz się z klastrem przy użyciu polecenia cmdlet [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) . Aby zobaczyć, gdzie wdrożono aplikację, uruchom polecenie [Get-ServiceFabricApplicationHealth](/powershell/module/servicefabric/get-servicefabricapplicationhealth?view=azureservicefabricps) i przyjrzyj się wdrożonym aplikacjom. Aby zobaczyć, które pakiety usługi znajdują się w aplikacji, zapoznaj się ze wdrożonymi elementami podrzędnymi pakietu usług w danych wyjściowych [Get-ServiceFabricDeployedApplicationHealth](/powershell/module/servicefabric/get-servicefabricdeployedapplicationhealth?view=azureservicefabricps) .

Poniższe polecenie cmdlet pobiera kondycję pakietu usługi **WordCountServicePkg** z aplikacji **sieci szkieletowej:/WORDCOUNT** wdrożoną na **_Node_2**. Jednostka zawiera raporty **System. hostowania** dotyczące pomyślnej aktywacji pakietu i punktu wejścia oraz pomyślnej rejestracji typu usługi.

```powershell
PS D:\ServiceFabric> Get-ServiceFabricDeployedApplication -ApplicationName fabric:/WordCount -NodeName _Node_2 | Get-ServiceFabricDeployedServicePackageHealth -ServiceManifestName WordCountServicePkg


ApplicationName            : fabric:/WordCount
ServiceManifestName        : WordCountServicePkg
ServicePackageActivationId : 
NodeName                   : _Node_2
AggregatedHealthState      : Ok
HealthEvents               : 
                             SourceId              : System.Hosting
                             Property              : Activation
                             HealthState           : Ok
                             SequenceNumber        : 131444422267693359
                             SentAt                : 7/13/2017 5:57:06 PM
                             ReceivedAt            : 7/13/2017 5:57:18 PM
                             TTL                   : Infinite
                             Description           : The ServicePackage was activated successfully.
                             RemoveWhenExpired     : False
                             IsExpired             : False
                             Transitions           : Error->Ok = 7/13/2017 5:57:18 PM, LastWarning = 1/1/0001 12:00:00 AM

                             SourceId              : System.Hosting
                             Property              : CodePackageActivation:Code:EntryPoint
                             HealthState           : Ok
                             SequenceNumber        : 131444422267903345
                             SentAt                : 7/13/2017 5:57:06 PM
                             ReceivedAt            : 7/13/2017 5:57:18 PM
                             TTL                   : Infinite
                             Description           : The CodePackage was activated successfully.
                             RemoveWhenExpired     : False
                             IsExpired             : False
                             Transitions           : Error->Ok = 7/13/2017 5:57:18 PM, LastWarning = 1/1/0001 12:00:00 AM

                             SourceId              : System.Hosting
                             Property              : ServiceTypeRegistration:WordCountServiceType
                             HealthState           : Ok
                             SequenceNumber        : 131444422272458374
                             SentAt                : 7/13/2017 5:57:07 PM
                             ReceivedAt            : 7/13/2017 5:57:18 PM
                             TTL                   : Infinite
                             Description           : The ServiceType was registered successfully.
                             RemoveWhenExpired     : False
                             IsExpired             : False
                             Transitions           : Error->Ok = 7/13/2017 5:57:18 PM, LastWarning = 1/1/0001 12:00:00 AM
```

### <a name="rest"></a>REST
Możesz uzyskać wdrożoną kondycję pakietu usługi za pomocą [żądania GET](/rest/api/servicefabric/get-the-health-of-a-service-package) lub [żądania post](/rest/api/servicefabric/get-the-health-of-a-service-package-by-using-a-health-policy) , które zawiera zasady dotyczące kondycji opisane w treści.

## <a name="health-chunk-queries"></a>Zapytania dotyczące fragmentów kondycji
Zapytania dotyczące fragmentów kondycji mogą zwracać wielopoziomowe elementy klastra (cyklicznie) dla filtrów wejściowych. Obsługuje Zaawansowane filtry, które umożliwiają dużą elastyczność w wyborze elementów podrzędnych do zwrócenia. Filtry mogą określać elementy podrzędne według unikatowego identyfikatora lub innych identyfikatorów grup i/lub Stanów kondycji. Domyślnie żadne elementy podrzędne nie są uwzględniane, w przeciwieństwie do poleceń kondycji, które zawsze zawierają elementy podrzędne pierwszego poziomu.

[Zapytania dotyczące kondycji](service-fabric-view-entities-aggregated-health.md#health-queries) zwracają tylko elementy podrzędne pierwszego poziomu określonej jednostki dla wymaganych filtrów. Aby uzyskać elementy podrzędne elementów podrzędnych, należy wywołać dodatkowe interfejsy API kondycji dla każdej interesującej Cię jednostki. Podobnie w celu uzyskania kondycji określonych jednostek należy wywołać jeden interfejs API kondycji dla każdej żądanej jednostki. Filtrowanie zaawansowane zapytania fragmentu umożliwia zażądanie wielu interesujących elementów w jednym zapytaniu, co minimalizuje rozmiar wiadomości i liczbę komunikatów.

Wartość zapytania fragmentu polega na tym, że można uzyskać stan kondycji większej liczby jednostek klastra (potencjalnie wszystkie jednostki klastra zaczynające się w wymaganym katalogu głównym) w jednym wywołaniu. Możliwe jest wyrażenie złożonej kwerendy dotyczącej kondycji, na przykład:

* Zwróć tylko aplikacje z błędami, a dla tych aplikacji są dostępne wszystkie usługi z ostrzeżeniem lub błędem. W przypadku zwracanych usług Uwzględnij wszystkie partycje.
* Zwróć tylko kondycję czterech aplikacji, które są określone przez ich nazwy.
* Zwróć tylko kondycję aplikacji żądanego typu aplikacji.
* Zwróć wszystkie wdrożone jednostki w węźle. Zwraca wszystkie aplikacje, wszystkie wdrożone aplikacje w określonym węźle i wszystkie wdrożone pakiety usługi w tym węźle.
* Zwróć wszystkie repliki z błędami. Zwraca wszystkie aplikacje, usługi, partycje i tylko repliki z błędami.
* Zwróć wszystkie aplikacje. W przypadku określonej usługi Dołącz wszystkie partycje.

Obecnie zapytanie dotyczące fragmentu kondycji jest widoczne tylko dla jednostki klastra. Zwraca fragment kondycji klastra, który zawiera następujące:

* Zagregowany stan kondycji klastra.
* Lista fragmentów stanu kondycji węzłów odnoszących się do filtrów wejściowych.
* Lista fragmentów stanu kondycji aplikacji, które respektują filtry wejściowe. Każdy fragment stanu kondycji aplikacji zawiera listę fragmentów z wszystkimi usługami, które respektują filtry wejściowe i listę fragmentów ze wszystkimi wdrożonymi aplikacjami, które respektują filtry. Ta sama dla elementów podrzędnych usług i wdrożonych aplikacji. W ten sposób wszystkie jednostki w klastrze mogą być potencjalnie zwracane, jeśli będzie to wymagane, w sposób hierarchiczny.

### <a name="cluster-health-chunk-query"></a>Zapytanie dotyczące fragmentu kondycji klastra
Zwraca kondycję jednostki klastra i zawiera fragmenty hierarchicznych Stanów kondycji wymaganych elementów podrzędnych. Dane wejściowe:

* Obowiązkowe Zasady kondycji klastra używane do szacowania węzłów i zdarzeń klastra.
* Obowiązkowe Mapa zasad kondycji aplikacji z zasadami kondycji używanymi do przesłania zasad manifestu aplikacji.
* Obowiązkowe Filtry dla węzłów i aplikacji, które określają, które wpisy są interesujące i powinny być zwracane w wyniku. Filtry są specyficzne dla jednostki/grupy jednostek lub mają zastosowanie do wszystkich jednostek na tym poziomie. Lista filtrów może zawierać jeden filtr ogólny i/lub filtry dla określonych identyfikatorów w celu uściślenia jednostek zwracanych przez zapytanie. Jeśli puste, elementy podrzędne nie są zwracane domyślnie.
  Przeczytaj więcej na temat filtrów w [NodeHealthStateFilter](/dotnet/api/system.fabric.health.nodehealthstatefilter) i [ApplicationHealthStateFilter](/dotnet/api/system.fabric.health.applicationhealthstatefilter). Filtry aplikacji mogą rekursywnie określić filtry zaawansowane dla elementów podrzędnych.

Wynik fragmentu zawiera elementy podrzędne, które respektują filtry.

Obecnie zapytanie fragmentu nie zwraca ocen w złej kondycji ani zdarzeń jednostek. Dodatkowe informacje można uzyskać przy użyciu istniejącej kwerendy kondycji klastra.

### <a name="api"></a>Interfejs API
Aby uzyskać fragment kondycji klastra, Utwórz `FabricClient` i Wywołaj metodę [GetClusterHealthChunkAsync](/dotnet/api/system.fabric.fabricclient.healthclient.getclusterhealthchunkasync) na swoim serwerze **kondycji**. Można przekazać w [ClusterHealthQueryDescription](/dotnet/api/system.fabric.description.clusterhealthchunkquerydescription) , aby opisać zasady dotyczące kondycji i filtry zaawansowane.

Poniższy kod pobiera fragment kondycji klastra z filtrami zaawansowanymi.

```csharp
var queryDescription = new ClusterHealthChunkQueryDescription();
queryDescription.ApplicationFilters.Add(new ApplicationHealthStateFilter()
    {
        // Return applications only if they are in error
        HealthStateFilter = HealthStateFilter.Error
    });

// Return all replicas
var wordCountServiceReplicaFilter = new ReplicaHealthStateFilter()
    {
        HealthStateFilter = HealthStateFilter.All
    };

// Return all replicas and all partitions
var wordCountServicePartitionFilter = new PartitionHealthStateFilter()
    {
        HealthStateFilter = HealthStateFilter.All
    };
wordCountServicePartitionFilter.ReplicaFilters.Add(wordCountServiceReplicaFilter);

// For specific service, return all partitions and all replicas
var wordCountServiceFilter = new ServiceHealthStateFilter()
{
    ServiceNameFilter = new Uri("fabric:/WordCount/WordCountService"),
};
wordCountServiceFilter.PartitionFilters.Add(wordCountServicePartitionFilter);

// Application filter: for specific application, return no services except the ones of interest
var wordCountApplicationFilter = new ApplicationHealthStateFilter()
    {
        // Always return fabric:/WordCount application
        ApplicationNameFilter = new Uri("fabric:/WordCount"),
    };
wordCountApplicationFilter.ServiceFilters.Add(wordCountServiceFilter);

queryDescription.ApplicationFilters.Add(wordCountApplicationFilter);

var result = await fabricClient.HealthManager.GetClusterHealthChunkAsync(queryDescription);
```

### <a name="powershell"></a>Program PowerShell
Polecenie cmdlet umożliwiające uzyskanie kondycji klastra to [Get-ServiceFabricClusterChunkHealth](/powershell/module/servicefabric/get-servicefabricclusterhealthchunk). Najpierw Połącz się z klastrem przy użyciu polecenia cmdlet [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) .

Poniższy kod pobiera węzły tylko wtedy, gdy występują błędy z wyjątkiem określonego węzła, który powinien być zawsze zwracany.

```xml
PS D:\ServiceFabric> $errorFilter = [System.Fabric.Health.HealthStateFilter]::Error;
$allFilter = [System.Fabric.Health.HealthStateFilter]::All;

$nodeFilter1 = New-Object System.Fabric.Health.NodeHealthStateFilter -Property @{HealthStateFilter=$errorFilter}
$nodeFilter2 = New-Object System.Fabric.Health.NodeHealthStateFilter -Property @{NodeNameFilter="_Node_1";HealthStateFilter=$allFilter}
# Create node filter list that will be passed in the cmdlet
$nodeFilters = New-Object System.Collections.Generic.List[System.Fabric.Health.NodeHealthStateFilter]
$nodeFilters.Add($nodeFilter1)
$nodeFilters.Add($nodeFilter2)

Get-ServiceFabricClusterHealthChunk -NodeFilters $nodeFilters


HealthState                  : Warning
NodeHealthStateChunks        : 
                               TotalCount            : 1

                               NodeName              : _Node_1
                               HealthState           : Ok

ApplicationHealthStateChunks : None
```

Poniższe polecenie cmdlet pobiera fragment klastra z filtrami aplikacji.

```xml
PS D:\ServiceFabric> $errorFilter = [System.Fabric.Health.HealthStateFilter]::Error;
$allFilter = [System.Fabric.Health.HealthStateFilter]::All;

# All replicas
$replicaFilter = New-Object System.Fabric.Health.ReplicaHealthStateFilter -Property @{HealthStateFilter=$allFilter}

# All partitions
$partitionFilter = New-Object System.Fabric.Health.PartitionHealthStateFilter -Property @{HealthStateFilter=$allFilter}
$partitionFilter.ReplicaFilters.Add($replicaFilter)

# For WordCountService, return all partitions and all replicas
$svcFilter1 = New-Object System.Fabric.Health.ServiceHealthStateFilter -Property @{ServiceNameFilter="fabric:/WordCount/WordCountService"}
$svcFilter1.PartitionFilters.Add($partitionFilter)

$svcFilter2 = New-Object System.Fabric.Health.ServiceHealthStateFilter -Property @{HealthStateFilter=$errorFilter}

$appFilter = New-Object System.Fabric.Health.ApplicationHealthStateFilter -Property @{ApplicationNameFilter="fabric:/WordCount"}
$appFilter.ServiceFilters.Add($svcFilter1)
$appFilter.ServiceFilters.Add($svcFilter2)

$appFilters = New-Object System.Collections.Generic.List[System.Fabric.Health.ApplicationHealthStateFilter]
$appFilters.Add($appFilter)

Get-ServiceFabricClusterHealthChunk -ApplicationFilters $appFilters


HealthState                  : Error
NodeHealthStateChunks        : None
ApplicationHealthStateChunks : 
                               TotalCount            : 1

                               ApplicationName       : fabric:/WordCount
                               ApplicationTypeName   : WordCount
                               HealthState           : Error
                               ServiceHealthStateChunks : 
                                TotalCount            : 1

                                ServiceName           : fabric:/WordCount/WordCountService
                                HealthState           : Error
                                PartitionHealthStateChunks : 
                                    TotalCount            : 1

                                    PartitionId           : af2e3e44-a8f8-45ac-9f31-4093eb897600
                                    HealthState           : Error
                                    ReplicaHealthStateChunks : 
                                        TotalCount            : 5

                                        ReplicaOrInstanceId   : 131444422293118720
                                        HealthState           : Ok

                                        ReplicaOrInstanceId   : 131444422293118721
                                        HealthState           : Ok

                                        ReplicaOrInstanceId   : 131444422293113678
                                        HealthState           : Ok

                                        ReplicaOrInstanceId   : 131444422293113679
                                        HealthState           : Ok

                                        ReplicaOrInstanceId   : 131444422260002646
                                        HealthState           : Error
```

Poniższe polecenie cmdlet zwraca wszystkie wdrożone jednostki w węźle.

```xml
PS D:\ServiceFabric> $errorFilter = [System.Fabric.Health.HealthStateFilter]::Error;
$allFilter = [System.Fabric.Health.HealthStateFilter]::All;

$dspFilter = New-Object System.Fabric.Health.DeployedServicePackageHealthStateFilter -Property @{HealthStateFilter=$allFilter}
$daFilter =  New-Object System.Fabric.Health.DeployedApplicationHealthStateFilter -Property @{HealthStateFilter=$allFilter;NodeNameFilter="_Node_2"}
$daFilter.DeployedServicePackageFilters.Add($dspFilter)

$appFilter = New-Object System.Fabric.Health.ApplicationHealthStateFilter -Property @{HealthStateFilter=$allFilter}
$appFilter.DeployedApplicationFilters.Add($daFilter)

$appFilters = New-Object System.Collections.Generic.List[System.Fabric.Health.ApplicationHealthStateFilter]
$appFilters.Add($appFilter)
Get-ServiceFabricClusterHealthChunk -ApplicationFilters $appFilters


HealthState                  : Error
NodeHealthStateChunks        : None
ApplicationHealthStateChunks : 
                               TotalCount            : 2

                               ApplicationName       : fabric:/System
                               HealthState           : Ok
                               DeployedApplicationHealthStateChunks : 
                                TotalCount            : 1

                                NodeName              : _Node_2
                                HealthState           : Ok
                                DeployedServicePackageHealthStateChunks :
                                    TotalCount            : 1

                                    ServiceManifestName   : FAS
                                    ServicePackageActivationId : 
                                    HealthState           : Ok



                               ApplicationName       : fabric:/WordCount
                               ApplicationTypeName   : WordCount
                               HealthState           : Error
                               DeployedApplicationHealthStateChunks : 
                                TotalCount            : 1

                                NodeName              : _Node_2
                                HealthState           : Ok
                                DeployedServicePackageHealthStateChunks :
                                    TotalCount            : 1

                                    ServiceManifestName   : WordCountServicePkg
                                    ServicePackageActivationId : 
                                    HealthState           : Ok
```

### <a name="rest"></a>REST
Można pobrać fragment kondycji klastra z [żądaniem Get](/rest/api/servicefabric/get-the-health-of-a-cluster-using-health-chunks) lub [żądanie post](/rest/api/servicefabric/health-of-cluster) , które zawiera zasady dotyczące kondycji i filtry zaawansowane opisane w treści.

## <a name="general-queries"></a>Zapytania ogólne
Ogólne zapytania zwracają listę Service Fabric jednostek określonego typu. Są one udostępniane za pośrednictwem interfejsu API (przy użyciu metod w **FabricClient. querymanager**), poleceń cmdlet programu POWERSHELL i REST. Te zapytania agregują podzapytania z wielu składników. Jeden z nich to [Magazyn kondycji](service-fabric-health-introduction.md#health-store), który wypełnia zagregowany stan kondycji dla każdego wyniku zapytania.  

> [!NOTE]
> Ogólne zapytania zwracają zagregowany stan kondycji jednostki i nie zawierają rozbudowanych danych dotyczących kondycji. Jeśli jednostka nie jest w dobrej kondycji, możesz wykonać zapytania o kondycję, aby uzyskać wszystkie informacje o kondycji, w tym zdarzenia, podrzędne Stany kondycji i oceny złej kondycji.
>
>

Jeśli ogólne zapytania zwracają nieznany stan kondycji dla jednostki, istnieje możliwość, że magazyn kondycji nie ma kompletnych danych dotyczących jednostki. Istnieje również możliwość, że podzapytanie do magazynu kondycji nie powiodło się (na przykład wystąpił błąd komunikacji lub włączono ograniczenie magazynu kondycji). Wykonaj zapytanie o kondycję dla jednostki. Jeśli podzapytanie napotkało błędy przejściowe, takie jak problemy z siecią, wykonanie tej czynności może powieść się. Może również dawać więcej szczegółowych informacji z magazynu kondycji, dlaczego jednostka nie została ujawniona.

Zapytania zawierające wartość **HealthState** dla jednostek są następujące:

* Lista węzłów: zwraca węzły listy w klastrze (stronicowane).
  * Interfejs API: [FabricClient. QueryClient. GetNodeListAsync](/dotnet/api/system.fabric.fabricclient.queryclient.getnodelistasync)
  * PowerShell: Get-ServiceFabricNode
* Lista aplikacji: zwraca listę aplikacji w klastrze (stronicowana).
  * Interfejs API: [FabricClient. QueryClient. GetApplicationListAsync](/dotnet/api/system.fabric.fabricclient.queryclient.getapplicationlistasync)
  * PowerShell: Get-ServiceFabricApplication
* Lista usług: zwraca listę usług w aplikacji (stronicowana).
  * Interfejs API: [FabricClient. QueryClient. GetServiceListAsync](/dotnet/api/system.fabric.fabricclient.queryclient.getservicelistasync)
  * PowerShell: Get-ServiceFabricService
* Lista partycji: zwraca listę partycji w usłudze (stronicowana).
  * Interfejs API: [FabricClient. QueryClient. GetPartitionListAsync](/dotnet/api/system.fabric.fabricclient.queryclient.getpartitionlistasync)
  * PowerShell: Get-ServiceFabricPartition
* Lista replik: zwraca listę replik w partycji (stronicowanej).
  * Interfejs API: [FabricClient. QueryClient. GetReplicaListAsync](/dotnet/api/system.fabric.fabricclient.queryclient.getreplicalistasync)
  * PowerShell: Get-ServiceFabricReplica
* Lista wdrożonych aplikacji: zwraca listę wdrożonych aplikacji w węźle.
  * Interfejs API: [FabricClient. QueryClient. GetDeployedApplicationListAsync](/dotnet/api/system.fabric.fabricclient.queryclient.getdeployedapplicationlistasync)
  * PowerShell: Get-ServiceFabricDeployedApplication
* Lista wdrożonych pakietów usługi: zwraca listę pakietów usługi w wdrożonej aplikacji.
  * Interfejs API: [FabricClient. QueryClient. GetDeployedServicePackageListAsync](/dotnet/api/system.fabric.fabricclient.queryclient.getdeployedservicepackagelistasync)
  * PowerShell: Get-ServiceFabricDeployedApplication

> [!NOTE]
> Niektóre zapytania zwracają stronicowane wyniki. Zwrot tych zapytań jest listą pochodzącą od [PagedList \<T> ](/dotnet/api/system.fabric.query.pagedlist-1). Jeśli wyniki nie pasują do wiadomości, zwracana jest tylko strona i ContinuationToken, która śledzi, gdzie Wyliczenie zostało zatrzymane. Kontynuuj wywoływanie tego samego zapytania i przekaż token kontynuacji z poprzedniego zapytania, aby uzyskać następne wyniki.

### <a name="examples"></a>Przykłady
Poniższy kod pobiera aplikacje w złej kondycji w klastrze:

```csharp
var applications = fabricClient.QueryManager.GetApplicationListAsync().Result.Where(
  app => app.HealthState == HealthState.Error);
```

Następujące polecenie cmdlet pobiera szczegóły aplikacji sieci szkieletowej:/WordCount. Zwróć uwagę, że stan kondycji to ostrzeżenie.

```powershell
PS C:\> Get-ServiceFabricApplication -ApplicationName fabric:/WordCount

ApplicationName        : fabric:/WordCount
ApplicationTypeName    : WordCount
ApplicationTypeVersion : 1.0.0
ApplicationStatus      : Ready
HealthState            : Warning
ApplicationParameters  : { "WordCountWebService_InstanceCount" = "1";
                         "_WFDebugParams_" = "[{"ServiceManifestName":"WordCountWebServicePkg","CodePackageName":"Code","EntryPointType":"Main","Debug
                         ExePath":"C:\\Program Files (x86)\\Microsoft Visual Studio
                         14.0\\Common7\\Packages\\Debugger\\VsDebugLaunchNotify.exe","DebugArguments":" {74f7e5d5-71a9-47e2-a8cd-1878ec4734f1} -p
                         [ProcessId] -tid [ThreadId]","EnvironmentBlock":"_NO_DEBUG_HEAP=1\u0000"},{"ServiceManifestName":"WordCountServicePkg","CodeP
                         ackageName":"Code","EntryPointType":"Main","DebugExePath":"C:\\Program Files (x86)\\Microsoft Visual Studio
                         14.0\\Common7\\Packages\\Debugger\\VsDebugLaunchNotify.exe","DebugArguments":" {2ab462e6-e0d1-4fda-a844-972f561fe751} -p
                         [ProcessId] -tid [ThreadId]","EnvironmentBlock":"_NO_DEBUG_HEAP=1\u0000"}]" }
```

Następujące polecenie cmdlet pobiera usługi ze stanem kondycji błędu:

```powershell
PS D:\ServiceFabric> Get-ServiceFabricApplication | Get-ServiceFabricService | where {$_.HealthState -eq "Error"}


ServiceName            : fabric:/WordCount/WordCountService
ServiceKind            : Stateful
ServiceTypeName        : WordCountServiceType
IsServiceGroup         : False
ServiceManifestVersion : 1.0.0
HasPersistedState      : True
ServiceStatus          : Active
HealthState            : Error
```

## <a name="cluster-and-application-upgrades"></a>Uaktualnianie klastra i aplikacji
Podczas monitorowania uaktualnienia klastra i aplikacji Service Fabric sprawdza kondycję, aby upewnić się, że wszystko pozostaje w dobrej kondycji. Jeśli jednostka jest w złej kondycji zgodnie z oceną przy użyciu skonfigurowanych zasad dotyczących kondycji, uaktualnienie stosuje zasady specyficzne dla uaktualnienia, aby określić następną akcję. Uaktualnienie może być wstrzymane, aby zezwolić na interakcję użytkownika (np. naprawianie warunków błędów lub zmiana zasad) lub automatyczne wycofanie do poprzedniej prawidłowej wersji.

Podczas uaktualniania *klastra* można uzyskać stan uaktualnienia klastra. Stan uaktualnienia obejmuje ocenę złej kondycji, co wskazuje na to, co jest złej kondycji w klastrze. Jeśli uaktualnienie zostanie wycofane ze względu na problemy z kondycją, stan uaktualnienia dotyczy ostatnich nieprawidłowych przyczyn. Te informacje mogą pomóc administratorom w zbadaniu, co poszło źle po zakończeniu uaktualnienia lub zatrzymaniu go.

Podobnie podczas uaktualniania *aplikacji* wszystkie oceny w złej kondycji są zawarte w stanie uaktualnienia aplikacji.

Poniżej przedstawiono stan uaktualnienia aplikacji dla zmodyfikowanej aplikacji sieci szkieletowej:/WordCount. Licznik alarm zgłosił błąd dla jednej z jej replik. Uaktualnianie jest wycofywane z powodu nieprzestrzegania kontroli kondycji.

```powershell
PS C:\> Get-ServiceFabricApplicationUpgrade fabric:/WordCount

ApplicationName               : fabric:/WordCount
ApplicationTypeName           : WordCount
TargetApplicationTypeVersion  : 1.0.0.0
ApplicationParameters         : {}
StartTimestampUtc             : 4/21/2017 5:23:26 PM
FailureTimestampUtc           : 4/21/2017 5:23:37 PM
FailureReason                 : HealthCheck
UpgradeState                  : RollingBackInProgress
UpgradeDuration               : 00:00:23
CurrentUpgradeDomainDuration  : 00:00:00
CurrentUpgradeDomainProgress  : UD1

                                NodeName            : _Node_1
                                UpgradePhase        : Upgrading

                                NodeName            : _Node_2
                                UpgradePhase        : Upgrading

                                NodeName            : _Node_3
                                UpgradePhase        : PreUpgradeSafetyCheck
                                PendingSafetyChecks :
                                EnsurePartitionQuorum - PartitionId: 30db5be6-4e20-4698-8185-4bd7ca744020
NextUpgradeDomain             : UD2
UpgradeDomainsStatus          : { "UD1" = "Completed";
                                "UD2" = "Pending";
                                "UD3" = "Pending";
                                "UD4" = "Pending" }
UnhealthyEvaluations          :
                                Unhealthy services: 100% (1/1), ServiceType='WordCountServiceType', MaxPercentUnhealthyServices=0%.

                                  Unhealthy service: ServiceName='fabric:/WordCount/WordCountService', AggregatedHealthState='Error'.

                                      Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                      Unhealthy partition: PartitionId='a1f83a35-d6bf-4d39-b90d-28d15f39599b', AggregatedHealthState='Error'.

                                          Unhealthy replicas: 20% (1/5), MaxPercentUnhealthyReplicasPerPartition=0%.

                                          Unhealthy replica: PartitionId='a1f83a35-d6bf-4d39-b90d-28d15f39599b',
                                  ReplicaOrInstanceId='131031502346844058', AggregatedHealthState='Error'.

                                              Error event: SourceId='DiskWatcher', Property='Disk'.

UpgradeKind                   : Rolling
RollingUpgradeMode            : UnmonitoredAuto
ForceRestart                  : False
UpgradeReplicaSetCheckTimeout : 00:15:00
```

Dowiedz się więcej o [uaktualnianiu aplikacji Service Fabric](service-fabric-application-upgrade.md).

## <a name="use-health-evaluations-to-troubleshoot"></a>Korzystanie z ocen kondycji w celu rozwiązywania problemów
Za każdym razem, gdy występuje problem z klastrem lub aplikacją, Sprawdź kondycję klastra lub aplikacji, aby ustalić, co jest błędne. Oceny złej kondycji zawierają szczegółowe informacje o tym, co wyzwoliło bieżący stan złej kondycji. Jeśli chcesz, możesz przejść do szczegółów jednostek podrzędnych w złej kondycji, aby zidentyfikować główną przyczynę.

Rozważmy na przykład, że aplikacja jest w złej kondycji, ponieważ na jednej z jej replik występuje raport o błędach. Następujące polecenie cmdlet programu PowerShell pokazuje oceny złej kondycji:

```powershell
PS D:\ServiceFabric> Get-ServiceFabricApplicationHealth fabric:/WordCount -EventsFilter None -ServicesFilter None -DeployedApplicationsFilter None -ExcludeHealthStatistics


ApplicationName                 : fabric:/WordCount
AggregatedHealthState           : Error
UnhealthyEvaluations            : 
                                  Unhealthy services: 100% (1/1), ServiceType='WordCountServiceType', MaxPercentUnhealthyServices=0%.

                                  Unhealthy service: ServiceName='fabric:/WordCount/WordCountService', AggregatedHealthState='Error'.

                                    Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                    Unhealthy partition: PartitionId='af2e3e44-a8f8-45ac-9f31-4093eb897600', AggregatedHealthState='Error'.

                                        Unhealthy replicas: 20% (1/5), MaxPercentUnhealthyReplicasPerPartition=0%.

                                        Unhealthy replica: PartitionId='af2e3e44-a8f8-45ac-9f31-4093eb897600', ReplicaOrInstanceId='131444422260002646', AggregatedHealthState='Error'.

                                            Error event: SourceId='MyWatchdog', Property='Memory'.

ServiceHealthStates             : None
DeployedApplicationHealthStates : None
HealthEvents                    : None
```

Aby uzyskać więcej informacji, możesz zapoznać się z repliką:

```powershell
PS D:\ServiceFabric> Get-ServiceFabricReplicaHealth -ReplicaOrInstanceId 131444422260002646 -PartitionId af2e3e44-a8f8-45ac-9f31-4093eb897600


PartitionId           : af2e3e44-a8f8-45ac-9f31-4093eb897600
ReplicaId             : 131444422260002646
AggregatedHealthState : Error
UnhealthyEvaluations  : 
                        Error event: SourceId='MyWatchdog', Property='Memory'.

HealthEvents          : 
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 131444422263668344
                        SentAt                : 7/13/2017 5:57:06 PM
                        ReceivedAt            : 7/13/2017 5:57:18 PM
                        TTL                   : Infinite
                        Description           : Replica has been created._Node_2
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 7/13/2017 5:57:18 PM, LastWarning = 1/1/0001 12:00:00 AM

                        SourceId              : MyWatchdog
                        Property              : Memory
                        HealthState           : Error
                        SequenceNumber        : 131444451657749403
                        SentAt                : 7/13/2017 6:46:05 PM
                        ReceivedAt            : 7/13/2017 6:46:05 PM
                        TTL                   : Infinite
                        Description           : 
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Warning->Error = 7/13/2017 6:46:05 PM, LastOk = 1/1/0001 12:00:00 AM
```

> [!NOTE]
> Oceny złej kondycji przedstawiają pierwszy powód, w którym jednostka jest oceniana jako bieżący stan kondycji. Może istnieć wiele innych zdarzeń wyzwalających ten stan, ale nie są one odzwierciedlone w obliczeniach. Aby uzyskać więcej informacji, przejdź do szczegółów jednostek kondycji, aby ustalić wszystkie raporty w złej kondycji w klastrze.
>
>

## <a name="next-steps"></a>Następne kroki
[Używanie raportów kondycji systemu do rozwiązywania problemów](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)

[Dodawanie niestandardowych raportów o kondycji Service Fabric](service-fabric-report-health.md)

[Jak raportować i sprawdzać kondycję usługi](service-fabric-diagnostics-how-to-report-and-check-service-health.md)

[Lokalne monitorowanie i diagnozowanie usług](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Service Fabric uaktualniania aplikacji](service-fabric-application-upgrade.md)
