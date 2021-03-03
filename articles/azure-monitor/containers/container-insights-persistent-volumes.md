---
title: Konfigurowanie monitorowania PV przy użyciu usługi Container Insights | Microsoft Docs
description: W tym artykule opisano, jak można skonfigurować monitorowanie klastrów Kubernetes z trwałymi woluminami przy użyciu usługi Container Insights.
ms.topic: conceptual
ms.date: 10/20/2020
ms.openlocfilehash: 0afbeab49a6909a0011cd75a0419f7325ca68132
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101713732"
---
# <a name="configure-pv-monitoring-with-container-insights"></a>Konfigurowanie monitorowania PV przy użyciu usługi Container Insights

Począwszy od wersji agenta *ciprod10052020*, zintegrowany Agent usługi Container Insights obsługuje teraz użycie funkcji monitorowania PV (trwałego woluminu).

## <a name="pv-metrics"></a>Metryki PV

Usługi Container Insights automatycznie zaczynają monitorować WB przez zbieranie następujących metryk w interwałach 60sec i przechowywanie ich w tabeli **InsightMetrics** .

|Nazwa metryki |Wymiar metryki (Tagi) |Opis |
|------------|------------------------|------------|
| `pvUsedBytes`|`container.azm.ms/pv`|Zajęte miejsce w bajtach dla określonego woluminu trwałego z wnioskiem używanym przez określony element. `pvCapacityBytes` jest składany jako wymiar w polu Tagi, aby zmniejszyć koszty pozyskiwania danych i uprościć zapytania.|

## <a name="monitor-persistent-volumes"></a>Monitorowanie woluminów trwałych

Informacje o kontenerze zawierają wstępnie skonfigurowane wykresy dla tej metryki w skoroszycie dla każdego klastra. Wykresy można znaleźć na karcie trwały wolumin skoroszytu **szczegóły obciążenia** bezpośrednio z klastra AKS, wybierając pozycję skoroszyty z okienka po lewej stronie, a następnie z listy rozwijanej **Wyświetl skoroszyty** w szczegółowe informacje. Możesz również włączyć zalecany alert do użycia PV, a także zbadać te metryki w Log Analytics.  

![Przykładowy skoroszyt obciążenia Azure Monitor PV](./media/container-insights-persistent-volumes/pv-workload-example.PNG)

## <a name="next-steps"></a>Następne kroki

- Więcej informacji na temat zebranych metryk PV [znajdziesz tutaj](./container-insights-agent-config.md).
