---
title: Analiza zdarzeń Service Fabric platformy Azure z dziennikami Azure Monitor
description: Informacje na temat wizualizacji i analizowania zdarzeń przy użyciu dzienników Azure Monitor na potrzeby monitorowania i diagnostyki klastrów Service Fabric platformy Azure.
author: srrengar
ms.topic: conceptual
ms.date: 02/21/2019
ms.author: srrengar
ms.openlocfilehash: f44426103b8f0fce275f33682edbc3b84a08344b
ms.sourcegitcommit: 03713bf705301e7f567010714beb236e7c8cee6f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/21/2020
ms.locfileid: "92329563"
---
# <a name="event-analysis-and-visualization-with-azure-monitor-logs"></a>Analiza zdarzeń i wizualizacja z dziennikami Azure Monitor
 Dzienniki Azure Monitor zbierają i analizują dane telemetryczne z aplikacji i usług hostowanych w chmurze oraz udostępniają narzędzia do analizy ułatwiające maksymalizowanie ich dostępności i wydajności. W tym artykule opisano sposób uruchamiania zapytań w dziennikach Azure Monitor w celu uzyskania szczegółowych informacji i rozwiązywania problemów, co dzieje się w klastrze. Następujące często zadawane pytania są rozkierowane:

* Jak mogę rozwiązać problemy związane z kondycją?
* Jak mogę wiedzieć, kiedy węzeł ulegnie awarii?
* Jak mogę wiedzieć, czy usługi mojej aplikacji zostały uruchomione lub zatrzymane?

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="overview-of-the-log-analytics-workspace"></a>Przegląd obszaru roboczego Log Analytics

>[!NOTE] 
>Mimo że magazyn diagnostyczny jest domyślnie włączony podczas tworzenia klastra, należy skonfigurować obszar roboczy Log Analytics do odczytywania z magazynu diagnostycznego.

Dzienniki Azure Monitor zbierają dane z zarządzanych zasobów, w tym tabelę usługi Azure Storage lub agenta, i utrzymują je w centralnym repozytorium. Dane mogą być następnie używane do analizy, alertów i wizualizacji lub do dalszej eksportu. Dzienniki Azure Monitor obsługują zdarzenia, dane dotyczące wydajności lub inne dane niestandardowe. Zapoznaj [się z instrukcjami, aby skonfigurować rozszerzenie diagnostyki do agregowania zdarzeń](service-fabric-diagnostics-event-aggregation-wad.md) i [kroków w celu utworzenia log Analyticsego obszaru roboczego do odczytu ze zdarzeń w magazynie](service-fabric-diagnostics-oms-setup.md) , aby upewnić się, że dane przepływają do dzienników Azure monitor.

Po odebraniu danych przez dzienniki Azure Monitor, platforma Azure ma kilka *rozwiązań do monitorowania* , które są pakietami lub operacyjnymi pulpitami nawigacyjnymi do monitorowania danych przychodzących, które są dostosowane do kilku scenariuszy. Obejmują one rozwiązanie *Service Fabric Analytics* i rozwiązanie *kontenerów* , które są dwa z nich najbardziej odpowiednie do diagnostyki i monitorowania w przypadku korzystania z klastrów Service Fabric. W tym artykule opisano sposób korzystania z rozwiązania Service Fabric Analytics utworzonego za pomocą obszaru roboczego.

## <a name="access-the-service-fabric-analytics-solution"></a>Uzyskaj dostęp do rozwiązania Service Fabric Analytics

W [witrynie Azure Portal](https://portal.azure.com)przejdź do grupy zasobów, w której utworzono rozwiązanie Service Fabric Analytics.

Wybierz pozycję zasób **servicefabric \<nameOfOMSWorkspace\> **.

W programie `Summary` zobaczysz kafelki w formie wykresu dla każdego z włączonych rozwiązań, w tym jeden dla Service Fabric. Kliknij wykres **Service Fabric** , aby kontynuować Service Fabric Analytics rozwiązanie.

![Service Fabric rozwiązanie](media/service-fabric-diagnostics-event-analysis-oms/oms_service_fabric_summary.PNG)

Na poniższej ilustracji przedstawiono stronę główną rozwiązania Service Fabric Analytics. Ta strona główna zawiera widok migawek, co dzieje się w klastrze.

![Zrzut ekranu przedstawiający stronę główną rozwiązania Service Fabric Analytics.](media/service-fabric-diagnostics-event-analysis-oms/oms_service_fabric_solution.PNG)

 Jeśli po utworzeniu klastra włączono diagnostykę, można zobaczyć zdarzenia dla 

* [Zdarzenia klastra Service Fabric](service-fabric-diagnostics-event-generation-operational.md)
* [Zdarzenia modelu programowania Reliable Actors](service-fabric-reliable-actors-diagnostics.md)
* [Zdarzenia modelu programowania Reliable Services](service-fabric-reliable-services-diagnostics.md)

>[!NOTE]
>Oprócz Service Fabric zdarzeń z pola, bardziej szczegółowe zdarzenia systemowe można zbierać, [aktualizując konfigurację rozszerzenia diagnostyki](service-fabric-diagnostics-event-aggregation-wad.md#log-collection-configurations).

## <a name="view-service-fabric-events-including-actions-on-nodes"></a>Wyświetlanie zdarzeń Service Fabric, w tym akcji w węzłach

Na stronie Service Fabric Analytics kliknij Graf **Service Fabric zdarzeń**.

![Kanał operacyjny rozwiązania Service Fabric](media/service-fabric-diagnostics-event-analysis-oms/oms_service_fabric_events_selection.png)

Kliknij pozycję **Lista** , aby wyświetlić zdarzenia na liście. W tym miejscu zostaną wyświetlone wszystkie zebrane zdarzenia systemowe. Z tego względu są one z **WADServiceFabricSystemEventsTable** na koncie usługi Azure Storage, a podobnie zdarzenia niezawodnych usług i aktorów, które zobaczysz, są z tych tabel.
    
![Kanał operacyjny zapytania](media/service-fabric-diagnostics-event-analysis-oms/oms_service_fabric_events.png)

Alternatywnie możesz kliknąć lupę po lewej stronie i użyć języka zapytań Kusto, aby znaleźć to, czego szukasz. Na przykład aby znaleźć wszystkie akcje wykonywane na węzłach w klastrze, można użyć poniższego zapytania. Identyfikatory zdarzeń używane poniżej znajdują się w [dokumentacji zdarzenia dotyczącego kanału operacyjnego](service-fabric-diagnostics-event-generation-operational.md).

```kusto
ServiceFabricOperationalEvent
| where EventId < 25627 and EventId > 25619 
```

Można wykonać zapytanie dotyczące wielu innych pól, takich jak określone węzły (komputer) usługa systemowa (TASKNAME).

## <a name="view-service-fabric-reliable-service-and-actor-events"></a>Wyświetl Service Fabric niezawodne zdarzenia usługi i aktora

Na stronie Service Fabric Analytics kliknij wykres **Reliable Services**.

![Reliable Services Service Fabric rozwiązania](media/service-fabric-diagnostics-event-analysis-oms/oms_reliable_services_events_selection.png)

Kliknij pozycję **Lista** , aby wyświetlić zdarzenia na liście. W tym miejscu możesz zobaczyć zdarzenia z niezawodnych usług. Można zobaczyć różne zdarzenia dla momentu rozpoczęcia i ukończenia usługi RunAsync, która zwykle odbywa się w przypadku wdrożeń i uaktualnień. 

![Reliable Services zapytań](media/service-fabric-diagnostics-event-analysis-oms/oms_reliable_service_events.png)

Niezawodne zdarzenia aktora można wyświetlać w podobny sposób. Aby skonfigurować bardziej szczegółowe zdarzenia dla niezawodnych aktorów, należy zmienić `scheduledTransferKeywordFilter` konfigurację dla rozszerzenia diagnostyki (pokazanego poniżej). Szczegółowe informacje na temat tych wartości znajdują się w [odniesieniu do zdarzeń niezawodnych aktorów](service-fabric-reliable-actors-diagnostics.md#keywords).

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

Język zapytań Kusto jest zaawansowany. Innym cennym zapytaniem, które można uruchomić, jest znalezienie, które węzły generują najwięcej zdarzeń. Zapytanie na poniższym zrzucie ekranu pokazuje Service Fabric zdarzenia operacyjne zagregowane z określoną usługą i węzłem.

![Zdarzenia zapytania na węzeł](media/service-fabric-diagnostics-event-analysis-oms/oms_kusto_query.png)

## <a name="next-steps"></a>Następne kroki

* Aby włączyć monitorowanie infrastruktury, np. liczniki wydajności, przejdź do [dodawania agenta log Analytics](service-fabric-diagnostics-oms-agent.md). Agent zbiera liczniki wydajności i dodaje je do istniejącego obszaru roboczego.
* W przypadku klastrów lokalnych dzienniki Azure Monitor oferują bramę (serwer proxy przesyłania dalej HTTP), za pomocą której można wysyłać dane do dzienników Azure Monitor. Dowiedz się więcej na temat [łączenia komputerów bez dostępu do Internetu do dzienników Azure monitor przy użyciu bramy log Analytics](../azure-monitor/platform/gateway.md).
* Skonfiguruj  [Automatyczne alerty](../azure-monitor/platform/alerts-overview.md) w celu ułatwienia wykrywania i diagnostyki.
* Zapoznaj się z funkcjami [przeszukiwania dzienników i wykonywania zapytań](../azure-monitor/log-query/log-query-overview.md) , które są oferowane w ramach dzienników Azure monitor.
* Zapoznaj się z bardziej szczegółowym omówieniem dzienników Azure Monitor i ofert, które oferuje, Przeczytaj [co to jest Azure monitor Logs?](../azure-monitor/overview.md).
