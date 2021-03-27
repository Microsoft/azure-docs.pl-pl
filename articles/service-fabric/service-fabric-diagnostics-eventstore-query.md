---
title: Zapytanie o zdarzenia klastra przy użyciu interfejsów API EventStore
description: Dowiedz się, jak używać interfejsów API usługi Azure Service Fabric EventStore do wykonywania zapytań dotyczących zdarzeń platformy
ms.topic: conceptual
ms.date: 02/25/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: bc8909a6f25c3adcba60de639d822769baff703f
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2021
ms.locfileid: "105628811"
---
# <a name="query-eventstore-apis-for-cluster-events"></a>Wykonywanie zapytań dotyczących interfejsów API EventStore dla zdarzeń klastra

W tym artykule opisano, jak wykonywać zapytania dotyczące interfejsów API EventStore, które są dostępne w Service Fabric w wersji 6,2 i nowszych — Jeśli chcesz dowiedzieć się więcej na temat usługi EventStore, zobacz [Omówienie usługi EventStore](service-fabric-diagnostics-eventstore.md). Obecnie usługa EventStore może uzyskać dostęp tylko do danych w ciągu ostatnich 7 dni (jest to zależne od zasad przechowywania danych diagnostycznych klastra).

>[!NOTE]
>Interfejsy API EventStore są dostępne dla Service Fabric w wersji 6,4 tylko dla klastrów systemu Windows działających na platformie Azure.

Do interfejsów API EventStore można uzyskać dostęp bezpośrednio za pośrednictwem punktu końcowego REST lub programowo. W zależności od zapytania istnieje kilka parametrów, które są wymagane do zebrania odpowiednich danych. Te parametry zwykle obejmują:
* `api-version`: wersja interfejsów API EventStore, których używasz
* `StartTimeUtc`: określa początek okresu, na którym chcesz się zainteresować
* `EndTimeUtc`: koniec okresu

Oprócz tych parametrów dostępne są również parametry opcjonalne, takie jak:
* `timeout`: zastępowanie domyślnego limitu 60 sekund na potrzeby wykonywania operacji żądania
* `eventstypesfilter`: umożliwia filtrowanie dla określonych typów zdarzeń
* `ExcludeAnalysisEvents`: nie zwracaj zdarzeń "Analysis". Domyślnie zapytania EventStore będą zwracały ze zdarzeniami "Analysis", jeśli jest to możliwe. Zdarzenia analizy to bogatsze zdarzenia kanału operacyjnego, które zawierają dodatkowy kontekst lub informacje poza regularnym zdarzeniem Service Fabric i zapewniają większą głębokość.
* `SkipCorrelationLookup`: nie Szukaj potencjalnych zdarzeń skorelowanych w klastrze. Domyślnie EventStore podejmie próbę skorelowania zdarzeń w klastrze i łączy zdarzenia ze sobą, gdy jest to możliwe. 

Każda jednostka w klastrze może być kwerendą dla zdarzeń. Można także wysyłać zapytania o zdarzenia dla wszystkich jednostek typu. Można na przykład wykonać zapytania o zdarzenia dla określonego węzła lub dla wszystkich węzłów w klastrze. Bieżący zestaw jednostek, dla których można wykonywać zapytania o zdarzenia, to (z możliwością uporządkowania zapytania):
* Hosta `/EventsStore/Cluster/Events`
* Nich `/EventsStore/Nodes/Events`
* Większości `/EventsStore/Nodes/<NodeName>/$/Events`
* Zastosowania `/EventsStore/Applications/Events`
* Aplikacja `/EventsStore/Applications/<AppName>/$/Events`
* Services `/EventsStore/Services/Events`
* Usługi `/EventsStore/Services/<ServiceName>/$/Events`
* Partition `/EventsStore/Partitions/Events`
* Podzielić `/EventsStore/Partitions/<PartitionID>/$/Events`
* Replik `/EventsStore/Partitions/<PartitionID>/$/Replicas/Events`
* Niesiona `/EventsStore/Partitions/<PartitionID>/$/Replicas/<ReplicaID>/$/Events`

>[!NOTE]
>W przypadku odwoływania się do nazwy aplikacji lub usługi zapytanie nie musi zawierać "sieci szkieletowej:/" prefiks. Ponadto, jeśli nazwy aplikacji lub usług mają w nich "/", przełącz je na "~", aby zachować działanie zapytania. Jeśli na przykład aplikacja jest wyświetlana jako "Sieć szkieletowa:/APP1/FrontendApp", zapytania specyficzne dla aplikacji byłyby uporządkowane jako `/EventsStore/Applications/App1~FrontendApp/$/Events` .
>Ponadto raporty kondycji dotyczące usług są wyświetlane dzisiaj w ramach odpowiedniej aplikacji, więc należy wykonać zapytanie o `DeployedServiceHealthReportCreated` zdarzenia dla odpowiedniej jednostki aplikacji. 

## <a name="query-the-eventstore-via-rest-api-endpoints"></a>Wysyłanie zapytań do EventStore za pośrednictwem punktów końcowych interfejsu API REST

Możesz wysyłać zapytania do EventStore bezpośrednio za pośrednictwem punktu końcowego REST, wykonując `GET` żądania do: `<your cluster address>/EventsStore/<entity>/Events/` .

Na przykład w celu zbadania wszystkich zdarzeń klastra między `2018-04-03T18:00:00Z` i `2018-04-04T18:00:00Z` , żądanie będzie wyglądać następująco:

```
Method: GET 
URL: http://mycluster:19080/EventsStore/Cluster/Events?api-version=6.4&StartTimeUtc=2018-04-03T18:00:00Z&EndTimeUtc=2018-04-04T18:00:00Z
```

Może to oznaczać brak zdarzeń lub listę zdarzeń zwróconych w formacie JSON:

```json
Response: 200
Body:
[
  {
    "Kind": "ClusterUpgradeStart",
    "CurrentClusterVersion": "0.0.0.0:",
    "TargetClusterVersion": "6.2:1.0",
    "UpgradeType": "Rolling",
    "RollingUpgradeMode": "UnmonitoredAuto",
    "FailureAction": "Manual",
    "EventInstanceId": "090add3c-8f56-4d35-8d57-a855745b6064",
    "TimeStamp": "2018-04-03T20:18:59.4313064Z",
    "HasCorrelatedEvents": false
  },
  {
    "Kind": "ClusterUpgradeDomainComplete",
    "TargetClusterVersion": "6.2:1.0",
    "UpgradeState": "RollingForward",
    "UpgradeDomains": "(0 1 2)",
    "UpgradeDomainElapsedTimeInMs": "78.5288",
    "EventInstanceId": "090add3c-8f56-4d35-8d57-a855745b6064",
    "TimeStamp": "2018-04-03T20:19:59.5729953Z",
    "HasCorrelatedEvents": false
  },
  {
    "Kind": "ClusterUpgradeDomainComplete",
    "TargetClusterVersion": "6.2:1.0",
    "UpgradeState": "RollingForward",
    "UpgradeDomains": "(3 4)",
    "UpgradeDomainElapsedTimeInMs": "0",
    "EventInstanceId": "090add3c-8f56-4d35-8d57-a855745b6064",
    "TimeStamp": "2018-04-03T20:20:59.6271949Z",
    "HasCorrelatedEvents": false
  },
  {
    "Kind": "ClusterUpgradeComplete",
    "TargetClusterVersion": "6.2:1.0",
    "OverallUpgradeElapsedTimeInMs": "120196.5212",
    "EventInstanceId": "090add3c-8f56-4d35-8d57-a855745b6064",
    "TimeStamp": "2018-04-03T20:20:59.8134457Z",
    "HasCorrelatedEvents": false
  }
]
```

W tym miejscu można zobaczyć, że między `2018-04-03T18:00:00Z` `2018-04-04T18:00:00Z` programem i, ten klaster pomyślnie zakończył swoje pierwsze uaktualnienie, gdy został po raz pierwszy postawiliśmy `"CurrentClusterVersion": "0.0.0.0:"` `"TargetClusterVersion": "6.2:1.0"` w programie do wersji `"OverallUpgradeElapsedTimeInMs": "120196.5212"` .

## <a name="query-the-eventstore-programmatically"></a>EventStore programowo zapytania

Można również programowo zbadać EventStore za pomocą [biblioteki klienta Service Fabric](/dotnet/api/overview/azure/service-fabric#client-library).

Po skonfigurowaniu klienta Service Fabric możesz wysyłać zapytania o zdarzenia, uzyskując dostęp do EventStore w następujący sposób: `sfhttpClient.EventStore.<request>`

Oto przykładowe żądanie dla wszystkich zdarzeń klastra między `2018-04-03T18:00:00Z` i `2018-04-04T18:00:00Z` , za pomocą `GetClusterEventListAsync` funkcji.

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

## <a name="sample-scenarios-and-queries"></a>Przykładowe scenariusze i zapytania

Poniżej przedstawiono kilka przykładów dotyczących sposobu wywoływania interfejsów API REST magazynu zdarzeń w celu zrozumienia stanu klastra.

*Uaktualnienia klastra:*

Aby zobaczyć, kiedy ostatnio klaster został pomyślnie lub podjęto próbę uaktualnienia do ostatniego tygodnia, można wysłać zapytanie do interfejsów API w celu uzyskania ostatnio zakończonych uaktualnień do klastra, badając zapytania dotyczące zdarzeń "ClusterUpgradeCompleted" w EventStore: `https://mycluster.cloudapp.azure.com:19080/EventsStore/Cluster/Events?api-version=6.4&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z&EventsTypesFilter=ClusterUpgradeCompleted`

*Problemy z uaktualnianiem klastra:*

Podobnie, jeśli wystąpiły problemy z ostatnim uaktualnieniem klastra, można wykonać zapytanie o wszystkie zdarzenia dla jednostki klastra. Zobaczysz różne zdarzenia, w tym inicjowanie uaktualnień i każdy UD, dla którego uaktualnienie zostało przebudowane pomyślnie. Zobaczysz również zdarzenia dla punktu, w którym rozpoczęto wycofywanie i odpowiednie zdarzenia dotyczące kondycji. Oto zapytanie, którego można użyć w tym celu: `https://mycluster.cloudapp.azure.com:19080/EventsStore/Cluster/Events?api-version=6.4&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z`

*Zmiany stanu węzła:*

Aby zobaczyć zmiany stanu węzła w ciągu ostatnich kilku dni — gdy węzły przestaną się w górę lub w dół albo zostały aktywowane lub zdezaktywowane (za pomocą platformy, usługi chaos lub z danych wejściowych użytkownika), użyj następującego zapytania: `https://mycluster.cloudapp.azure.com:19080/EventsStore/Nodes/Events?api-version=6.4&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z`

*Zdarzenia aplikacji:*

Możesz również śledzić najnowsze wdrożenia i uaktualnienia aplikacji. Użyj następującego zapytania, aby wyświetlić wszystkie zdarzenia aplikacji w klastrze: `https://mycluster.cloudapp.azure.com:19080/EventsStore/Applications/Events?api-version=6.4&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z`

*Historyczna kondycja dla aplikacji:*

Oprócz tylko wyświetlania zdarzeń cyklu życia aplikacji można także zobaczyć historyczne dane dotyczące kondycji określonej aplikacji. Można to zrobić, określając nazwę aplikacji, dla której chcesz zebrać dane. Użyj tego zapytania, aby pobrać wszystkie zdarzenia dotyczące kondycji aplikacji: `https://mycluster.cloudapp.azure.com:19080/EventsStore/Applications/myApp/$/Events?api-version=6.4&starttimeutc=2018-03-24T17:01:51Z&endtimeutc=2018-03-29T17:02:51Z&EventsTypesFilter=ApplicationNewHealthReport` . Jeśli chcesz uwzględnić zdarzenia dotyczące kondycji, które mogły wygasnąć (przeszedł czas wygaśnięcia (TTL)), Dodaj `,ApplicationHealthReportExpired` do końca zapytania, aby odfiltrować dwa typy zdarzeń.

*Historyczna kondycja wszystkich usług w "MojaApl":*

Obecnie zdarzenia raportów kondycji dla usług są wyświetlane jako `DeployedServicePackageNewHealthReport` zdarzenia w ramach odpowiedniej jednostki aplikacji. Aby dowiedzieć się, jak usługi zostały wdrożone dla "APP1", należy użyć następującego zapytania: `https://mycluster.cloudapp.azure.com:19080/EventsStore/Applications/myapp/$/Events?api-version=6.4&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z&EventsTypesFilter=DeployedServicePackageNewHealthReport`

*Ponowna konfiguracja partycji:*

Aby wyświetlić wszystkie przesunięcia partycji, które wystąpiły w klastrze, zapytaj o `PartitionReconfigured` zdarzenie. Może to pomóc ustalić, jakie obciążenia są wykonywane w danym węźle w określonym czasie podczas diagnozowania problemów w klastrze. Oto przykładowe zapytanie, które wykonuje następujące działanie: `https://mycluster.cloudapp.azure.com:19080/EventsStore/Partitions/Events?api-version=6.4&starttimeutc=2018-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z&EventsTypesFilter=PartitionReconfigured`

*Usługa chaos:*

Istnieje zdarzenie, dla którego usługa Chaos jest uruchomiona lub zatrzymana, która jest dostępna na poziomie klastra. Aby zobaczyć ostatnie użycie usługi chaos, użyj następującego zapytania: `https://mycluster.cloudapp.azure.com:19080/EventsStore/Cluster/Events?api-version=6.4&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z&EventsTypesFilter=ChaosStarted,ChaosStopped`
