---
title: Magazyn zdarzeń Service Fabric platformy Azure
description: Dowiedz się więcej o usłudze Azure Service Fabric EventStore, sposobie zrozumienia i monitorowania stanu klastra lub obciążeń w dowolnym momencie.
ms.topic: conceptual
ms.date: 6/6/2019
ms.openlocfilehash: ef5049fd934a29fa1d96514c334b13358e6600cf
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105626558"
---
# <a name="eventstore-overview"></a>EventStore — Omówienie

>[!NOTE]
>Od Service Fabric wersja 6,4. Interfejsy API EventStore są dostępne tylko dla klastrów systemu Windows działających tylko na platformie Azure. Pracujemy nad przenoszeniem tej funkcji do systemu Linux oraz z naszych klastrów autonomicznych.

## <a name="overview"></a>Omówienie

Wprowadzona w wersji 6,2, usługa EventStore jest opcją monitorowania w Service Fabric. EventStore zapewnia sposób zrozumienia stanu klastra lub obciążeń w danym momencie.
EventStore jest usługą stanową Service Fabric, która przechowuje zdarzenia z klastra. Zdarzenie jest dostępne za pomocą Service Fabric Explorer, REST i interfejsów API. EventStore wysyła zapytanie bezpośrednio do klastra w celu uzyskania danych diagnostycznych w dowolnej jednostce w klastrze i powinno być używane do pomocy:

* Diagnozowanie problemów w programowaniu lub testowaniu lub w przypadku korzystania z potoku monitorowania
* Upewnij się, że akcje zarządzania wykonywane w klastrze są przetwarzane prawidłowo
* Uzyskiwanie "migawki" sposobu, w jaki Service Fabric współdziała z konkretną jednostką

![Zrzut ekranu przedstawia kartę zdarzenia w okienku węzły kilka zdarzeń, w tym zdarzenia NodeDown.](media/service-fabric-diagnostics-eventstore/eventstore.png)

Aby wyświetlić pełną listę zdarzeń dostępnych w EventStore, zobacz [zdarzenia Service Fabric](service-fabric-diagnostics-event-generation-operational.md).

>[!NOTE]
>Od Service Fabric wersja 6,4. Interfejsy API EventStore i środowisko użytkownika są ogólnie dostępne dla klastrów systemu Windows Azure. Pracujemy nad przenoszeniem tej funkcji do systemu Linux oraz z naszych klastrów autonomicznych.

Usługa EventStore może być kwerendą dla zdarzeń, które są dostępne dla każdego typu jednostki i jednostki w klastrze. Oznacza to, że można wykonywać zapytania o zdarzenia na następujących poziomach:
* Klaster: zdarzenia specyficzne dla samego klastra (np. uaktualnienie klastra)
* Węzły: wszystkie zdarzenia na poziomie węzła
* Węzeł: zdarzenia specyficzne dla jednego węzła identyfikowane przez `nodeName`
* Aplikacje: wszystkie zdarzenia na poziomie aplikacji
* Aplikacja: zdarzenia specyficzne dla jednej aplikacji identyfikowanej przez `applicationId`
* Usługi: zdarzenia ze wszystkich usług w klastrach
* Usługa: zdarzenia z określonej usługi identyfikowane przez `serviceId`
* Partycje: zdarzenia ze wszystkich partycji
* Partycja: zdarzenia z określonej partycji identyfikowanej przez `partitionId`
* Repliki partycji: zdarzenia ze wszystkich replik/wystąpień w określonej partycji identyfikowanej przez `partitionId`
* Replika partycji: zdarzenia z określonej repliki/wystąpienia identyfikowane przez `replicaId` i `partitionId`

Aby dowiedzieć się więcej na temat interfejsu API, zobacz [Dokumentacja interfejsu API EventStore](/rest/api/servicefabric/sfclient-index-eventsstore).

Usługa EventStore ma także możliwość skorelowania zdarzeń w klastrze. Przeglądając zdarzenia, które zostały zapisaną w tym samym czasie od różnych jednostek, które mogły mieć wpływ na siebie, usługa EventStore może połączyć te zdarzenia, aby pomóc w identyfikowaniu przyczyn działań w klastrze. Na przykład jeśli jedna z aplikacji stanie się zła w złej kondycji bez żadnych niewywołanych zmian, EventStore będzie również przeglądać inne zdarzenia uwidocznione przez platformę i mogły je skorelować ze `Error` `Warning` zdarzeniem lub. Dzięki temu szybciej wykrywaj awarie i główne przyczyny analizy.

## <a name="enable-eventstore-on-your-cluster"></a>Włączanie EventStore w klastrze

### <a name="local-cluster"></a>Klaster lokalny

W [fabricSettings.jsw klastrze](service-fabric-cluster-fabric-settings.md)Dodaj EventStoreService jako funkcję dodatku i wykonaj uaktualnienie klastra.

```json
    "addOnFeatures": [
        "EventStoreService"
    ],
```

### <a name="azure-cluster-version-65"></a>Klaster platformy Azure w wersji 6.5 +
Jeśli klaster platformy Azure zostanie uaktualniony do wersji 6,5 lub nowszej, EventStore zostanie automatycznie włączony w klastrze. Aby zrezygnować z programu, należy zaktualizować szablon klastra przy użyciu następujących opcji:

* Użyj wersji interfejsu API `2019-03-01` lub nowszej 
* Dodaj następujący kod do sekcji właściwości w klastrze
  ```json  
    "fabricSettings": [
      …
    ],
    "eventStoreServiceEnabled": false
  ```

### <a name="azure-cluster-version-64"></a>Klaster platformy Azure w wersji 6,4

Jeśli używasz wersji 6,4, możesz edytować szablon Azure Resource Manager, aby włączyć usługę EventStore. W tym celu należy wykonać [uaktualnienie konfiguracji klastra](service-fabric-cluster-config-upgrade-azure.md) i dodać następujący kod, można użyć PlacementConstraints, aby umieścić repliki usługi EventStore na określonym NodeType, np. NodeType dedykowanym dla usług systemowych. `upgradeDescription`Sekcja konfiguruje uaktualnienie konfiguracji, aby wyzwolić ponowne uruchomienie w węzłach. Sekcję w innej aktualizacji można usunąć.

```json
    "fabricSettings": [
          …
          …
          …,
         {
            "name": "EventStoreService",
            "parameters": [
              {
                "name": "TargetReplicaSetSize",
                "value": "3"
              },
              {
                "name": "MinReplicaSetSize",
                "value": "1"
              },
              {
                "name": "PlacementConstraints",
                "value": "(NodeType==<node_type_name_here>)"
              }
            ]
          }
        ],
        "upgradeDescription": {
          "forceRestart": true,
          "upgradeReplicaSetCheckTimeout": "10675199.02:48:05.4775807",
          "healthCheckWaitDuration": "00:01:00",
          "healthCheckStableDuration": "00:01:00",
          "healthCheckRetryTimeout": "00:5:00",
          "upgradeTimeout": "1:00:00",
          "upgradeDomainTimeout": "00:10:00",
          "healthPolicy": {
            "maxPercentUnhealthyNodes": 100,
            "maxPercentUnhealthyApplications": 100
          },
          "deltaHealthPolicy": {
            "maxPercentDeltaUnhealthyNodes": 0,
            "maxPercentUpgradeDomainDeltaUnhealthyNodes": 0,
            "maxPercentDeltaUnhealthyApplications": 0
          }
        }
```


## <a name="next-steps"></a>Następne kroki
* Wprowadzenie do interfejsu API EventStore — [Korzystanie z interfejsów API EventStore w klastrach usługi Azure Service Fabric](service-fabric-diagnostics-eventstore-query.md)
* Dowiedz się więcej na temat listy zdarzeń oferowanych przez zdarzenia EventStore- [Service Fabric](service-fabric-diagnostics-event-generation-operational.md)
* Przegląd monitorowania i diagnostyki w Service Fabric — [monitorowanie i diagnostyka Service Fabric](service-fabric-diagnostics-overview.md)
* Wyświetl pełną listę wywołań interfejsu API — [Dokumentacja interfejsu API REST EventStore](/rest/api/servicefabric/sfclient-index-eventsstore)
* Dowiedz się więcej o monitorowaniu klastra — [monitorowanie klastra i platformy](service-fabric-diagnostics-event-generation-infra.md).
