---
title: Usługa Azure Service Fabric diagnozowanie typowych scenariuszy
description: Dowiedz się więcej na temat rozwiązywania typowych scenariuszy monitorowania i diagnostyki w aplikacjach Service Fabric platformy Azure.
ms.topic: article
ms.date: 02/25/2019
ms.openlocfilehash: f92bc02082d8bcd9d917f05c93d3da413f772cd0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86257740"
---
# <a name="diagnose-common-scenarios-with-service-fabric"></a>Diagnozuj typowe scenariusze za pomocą Service Fabric

W tym artykule przedstawiono typowe scenariusze napotykane przez użytkowników w obszarze monitorowania i diagnostyki przy użyciu Service Fabric. Przedstawione scenariusze obejmują wszystkie 3 warstwy usługi Service Fabric: aplikacje, klaster i infrastruktura. Każde rozwiązanie używa dzienników Application Insights i Azure Monitor, narzędzi monitorowania platformy Azure, aby wykonać każdy scenariusz. Kroki opisane w poszczególnych rozwiązaniach umożliwiają użytkownikom wprowadzenie do korzystania z dzienników Application Insights i Azure Monitor w kontekście Service Fabric.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites-and-recommendations"></a>Wymagania wstępne i zalecenia

Rozwiązania w tym artykule będą korzystać z następujących narzędzi. Zalecamy skonfigurowanie i skonfigurowanie następujących opcji:

* [Application Insights z Service Fabric](service-fabric-tutorial-monitoring-aspnet.md)
* [Włączanie Diagnostyka Azure w klastrze](service-fabric-diagnostics-event-aggregation-wad.md)
* [Konfigurowanie obszaru roboczego Log Analytics](service-fabric-diagnostics-oms-setup.md)
* [Log Analytics agenta do śledzenia liczników wydajności](service-fabric-diagnostics-oms-agent.md)

## <a name="how-can-i-see-unhandled-exceptions-in-my-application"></a>Jak można zobaczyć Nieobsłużone wyjątki w mojej aplikacji?

1. Przejdź do zasobu Application Insights, z którym aplikacja jest skonfigurowana.
2. Kliknij pozycję *Wyszukaj* w lewym górnym rogu. Następnie kliknij przycisk Filtruj na następnym panelu.

    ![Podgląd AI](media/service-fabric-diagnostics-common-scenarios/ai-search-filter.png)

3. Zobaczysz wiele typów zdarzeń (ślady, żądania, zdarzenia niestandardowe). Wybierz "wyjątek" jako filtr.

    ![Lista filtrów AI](media/service-fabric-diagnostics-common-scenarios/ai-filter-list.png)

    Klikając wyjątek na liście, możesz zapoznać się z tematem więcej szczegółów, w tym kontekstu usługi, jeśli używasz zestawu SDK Service Fabric Application Insights.

    ![Wyjątek AI](media/service-fabric-diagnostics-common-scenarios/ai-exception.png)

## <a name="how-do-i-view-which-http-calls-are-used-in-my-services"></a>Jak mogę wyświetlić, które wywołania HTTP są używane w ramach usług?

1. W tym samym zasobie Application Insights można filtrować według "żądań" zamiast wyjątków i wyświetlać wszystkie żądania
2. W przypadku korzystania z zestawu SDK Service Fabric Application Insights można zobaczyć wizualną reprezentację Twoich usług, a także liczbę żądań zakończonych powodzeniem i niepowodzeniem. Po lewej stronie kliknij pozycję "Mapa aplikacji".

    ![Mapa aplikacji w bloku mapy aplikacji AI ](media/service-fabric-diagnostics-common-scenarios/app-map-blade.png) ![](media/service-fabric-diagnostics-common-scenarios/app-map-new.png)

    Aby uzyskać więcej informacji na temat mapy aplikacji, zapoznaj się z [dokumentacją mapy aplikacji](../azure-monitor/app/app-map.md)

## <a name="how-do-i-create-an-alert-when-a-node-goes-down"></a>Jak mogę utworzyć alert, gdy węzeł ulegnie awarii

1. Zdarzenia węzłów są śledzone przez klaster Service Fabric. Przejdź do zasobu rozwiązania Service Fabric Analytics o nazwie **servicefabric (NameofResourceGroup)**
2. Kliknij wykres u dołu bloku zatytułowanego "Podsumowanie"

    ![Azure Monitor dzienniki](media/service-fabric-diagnostics-common-scenarios/oms-solution-azure-portal.png)

3. Oto wiele wykresów i kafelków zawierających różne metryki. Kliknij jeden z wykresów i przejdziesz do przeszukiwania dzienników. W tym miejscu można wykonywać zapytania dotyczące wszelkich zdarzeń klastra lub liczników wydajności.
4. Wprowadź następujące zapytanie. Te identyfikatory zdarzeń znajdują się w temacie [zdarzenia dotyczące węzła](service-fabric-diagnostics-event-generation-operational.md#application-events)

    ```kusto
    ServiceFabricOperationalEvent
    | where EventID >= 25622 and EventID <= 25626
    ```

5. Kliknij pozycję "Nowa reguła alertu" u góry i teraz po nadejściu zdarzenia na podstawie tego zapytania otrzymasz alert w wybranej metodzie komunikacji.

    ![Nowy alert dotyczący Azure Monitor dzienników](media/service-fabric-diagnostics-common-scenarios/oms-create-alert.png)

## <a name="how-can-i-be-alerted-of-application-upgrade-rollbacks"></a>Jak można otrzymywać alerty o wycofaniu uaktualnienia aplikacji?

1. W tym samym oknie przeszukiwania dzienników co przed wprowadź następujące zapytanie dotyczące wycofywania uaktualnienia. Te identyfikatory zdarzeń znajdują się w sekcji [Informacje o zdarzeniach aplikacji](service-fabric-diagnostics-event-generation-operational.md#application-events)

    ```kusto
    ServiceFabricOperationalEvent
    | where EventID == 29623 or EventID == 29624
    ```

2. Kliknij pozycję "Nowa reguła alertu" u góry i teraz po nadejściu zdarzenia na podstawie tego zapytania otrzymasz alert.

## <a name="how-do-i-see-container-metrics"></a>Jak mogę wyświetlić metryki kontenerów?

W tym samym widoku, w którym znajdują się wszystkie wykresy, zobaczysz niektóre kafelki wydajności kontenerów. Aby te kafelki były wypełniane, musisz mieć Log Analytics rozwiązanie agenta i [monitorowanie kontenerów](service-fabric-diagnostics-oms-containers.md) .

![Metryki kontenera Log Analytics](media/service-fabric-diagnostics-common-scenarios/containermetrics.png)

>[!NOTE]
>Aby instrumentować dane telemetryczne z **wewnątrz** kontenera, należy dodać [Application Insights pakiet NuGet dla kontenerów](https://github.com/Microsoft/ApplicationInsights-servicefabric#microsoftapplicationinsightsservicefabric--for-service-fabric-lift-and-shift-scenarios).

## <a name="how-can-i-monitor-performance-counters"></a>Jak można monitorować liczniki wydajności?

1. Po dodaniu agenta Log Analytics do klastra należy dodać określone liczniki wydajności, które mają być śledzone. Przejdź do strony obszaru roboczego Log Analytics w portalu — na stronie rozwiązania na karcie obszar roboczy znajduje się w menu po lewej stronie.

    ![Karta Log Analytics obszaru roboczego](media/service-fabric-diagnostics-common-scenarios/workspacetab.png)

2. Gdy jesteś na stronie obszaru roboczego, kliknij pozycję "Ustawienia zaawansowane" w tym samym menu po lewej stronie.

    ![Log Analytics ustawień zaawansowanych](media/service-fabric-diagnostics-common-scenarios/advancedsettingsoms.png)

3. Kliknij pozycję dane > licznikami wydajności systemu Windows (licznikami wydajności Data > Linux dla maszyn z systemem Linux), aby rozpocząć zbieranie określonych liczników z węzłów za pośrednictwem agenta Log Analytics. Oto przykłady formatu dla liczników do dodania

   * `.NET CLR Memory(<ProcessNameHere>)\\# Total committed Bytes`
   * `Processor(_Total)\\% Processor Time`

     We właściwościach szybkiego startu VotingData i VotingWeb są używane nazwy procesów, dlatego śledzenie tych liczników będzie wyglądać następująco

   * `.NET CLR Memory(VotingData)\\# Total committed Bytes`
   * `.NET CLR Memory(VotingWeb)\\# Total committed Bytes`

     ![Liczniki wydajności Log Analytics](media/service-fabric-diagnostics-common-scenarios/omsperfcounters.png)

4. Pozwoli to zobaczyć, jak Twoja infrastruktura obsługuje Twoje obciążenia, i ustawić odpowiednie alerty na podstawie wykorzystania zasobów. Na przykład możesz chcieć ustawić alert, jeśli całkowite użycie procesora przekracza 90% lub poniżej 5%. Nazwa licznika, która będzie używana dla tego elementu to "czas procesora (%)". Można to zrobić, tworząc regułę alertu dla następującej kwerendy:

    ```kusto
    Perf | where CounterName == "% Processor Time" and InstanceName == "_Total" | where CounterValue >= 90 or CounterValue <= 5.
    ```

## <a name="how-do-i-track-performance-of-my-reliable-services-and-actors"></a>Jak mogę śledzić wydajność moich Reliable Services i uczestników?

Aby śledzić wydajność Reliable Services lub aktorów w aplikacjach, należy również zebrać liczniki Service Fabric aktora, metody aktora, usługi i metody usługi. Oto przykłady niezawodnych liczników wydajności usługi i aktora do zebrania

>[!NOTE]
>Service Fabric liczniki wydajności nie mogą być zbierane przez agenta Log Analytics obecnie, ale mogą być zbierane przez [inne rozwiązania diagnostyczne](service-fabric-diagnostics-partners.md)

* `Service Fabric Service(*)\\Average milliseconds per request`
* `Service Fabric Service Method(*)\\Invocations/Sec`
* `Service Fabric Actor(*)\\Average milliseconds per request`
* `Service Fabric Actor Method(*)\\Invocations/Sec`

Sprawdź te linki, aby uzyskać pełną listę liczników wydajności dla niezawodnych [usług](service-fabric-reliable-serviceremoting-diagnostics.md) i [aktorów](service-fabric-reliable-actors-diagnostics.md)

## <a name="next-steps"></a>Następne kroki

* [Wyszukaj typowe błędy aktywacji pakietu kodu](./service-fabric-diagnostics-code-package-errors.md)
* [Konfigurowanie alertów w programie AI](../azure-monitor/platform/alerts-log.md) do powiadamiania o zmianach wydajności lub użycia
* [Inteligentne wykrywanie w Application Insights](../azure-monitor/app/proactive-diagnostics.md) wykonuje aktywną analizę danych telemetrycznych wysyłanych do systemu AI w celu ostrzegania o potencjalnych problemach z wydajnością
* Dowiedz się więcej o dziennikach Azure Monitor [alerty](../azure-monitor/platform/alerts-overview.md) w celu ułatwienia wykrywania i diagnostyki.
* W przypadku klastrów lokalnych dzienniki Azure Monitor oferują bramę (serwer proxy przesyłania dalej HTTP), za pomocą której można wysyłać dane do dzienników Azure Monitor. Dowiedz się więcej na temat [łączenia komputerów bez dostępu do Internetu do dzienników Azure monitor przy użyciu bramy log Analytics](../azure-monitor/platform/gateway.md)
* Zapoznaj się z funkcjami [przeszukiwania dzienników i wykonywania zapytań](../azure-monitor/log-query/log-query-overview.md) , które są oferowane w ramach dzienników Azure monitor
* Zapoznaj się z bardziej szczegółowym omówieniem dzienników Azure Monitor i ofert, które oferuje, Przeczytaj [co to jest Azure monitor Logs?](../azure-monitor/overview.md)
