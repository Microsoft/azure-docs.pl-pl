---
title: Konfigurowanie monitorowania PV przy użyciu usługi Container Insights | Microsoft Docs
description: W tym artykule opisano, jak można skonfigurować monitorowanie klastrów Kubernetes z trwałymi woluminami przy użyciu usługi Container Insights.
ms.topic: conceptual
ms.date: 03/03/2021
ms.openlocfilehash: 16c87177a8911a7b88284606f54f7bf6e0e0daa3
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2021
ms.locfileid: "105026108"
---
# <a name="configure-pv-monitoring-with-container-insights"></a>Konfigurowanie monitorowania PV przy użyciu usługi Container Insights

Począwszy od wersji *ciprod10052020* agenta, Azure monitor dla kontenerów zintegrowany Agent obsługuje teraz użycie funkcji monitorowania PV (trwałego woluminu). W przypadku wersji agenta *ciprod01112021* Agent obsługuje monitorowanie spisu PV, w tym informacje o stanie, klasie magazynu, typie, trybach dostępu i innych szczegółach.
## <a name="pv-metrics"></a>Metryki PV

Usługi Container Insights automatycznie zaczynają monitorować użycie PV przez gromadzenie następujących metryk w interwałach 60-sekund i przechowywanie ich w tabeli **InsightMetrics** .

| Nazwa metryki | Wymiar metryki (Tagi) | Opis metryki | | `pvUsedBytes`| podUID, podName, pvcName, pvcNamespace, capacityBytes, clusterId, ClusterName | Zajęte miejsce w bajtach dla określonego woluminu trwałego z wnioskiem używanym przez określony element. `capacityBytes` jest składany jako wymiar w polu Tagi, aby zmniejszyć koszty pozyskiwania danych i uprościć zapytania. |

Dowiedz się więcej o konfigurowaniu zebranych metryk PV [tutaj](./container-insights-agent-config.md).

## <a name="pv-inventory"></a>Spis PV

Azure Monitor dla kontenerów automatycznie uruchamia monitorowanie PVs przez zbieranie następujących informacji w odstępach 60-sekund i przechowywanie ich w tabeli **KubePVInventory** .

|Dane |Źródło danych| Typ danych| Pola|
|-----|-----------|----------|-------|
|Spis woluminów trwałych w klastrze Kubernetes |Interfejs API polecenia |`KubePVInventory` | PVName, PVCapacityBytes, PVCName, PVCNamespace, PVStatus, PVAccessModes, PVType, PVTypeInfo, PVStorageClassName, PVCreationTimestamp, TimeGenerated, ClusterId, ClusterName, _ResourceId |

## <a name="monitor-persistent-volumes"></a>Monitorowanie woluminów trwałych

Azure Monitor kontenerów zawiera wstępnie skonfigurowane wykresy dla tej metryki użycia i informacje spisu w szablonach skoroszytów dla każdego klastra. Możesz również włączyć zalecany alert dla użycia PV i zbadać te metryki w Log Analytics.  

### <a name="workload-details-workbook"></a>Skoroszyt szczegółów obciążenia

Wykresy użycia dla konkretnych obciążeń można znaleźć na karcie trwały wolumin skoroszytu **szczegóły obciążenia** bezpośrednio z klastra AKS, wybierając opcję skoroszyty z okienka po lewej stronie, z listy rozwijanej **Wyświetl skoroszyty** w okienku szczegółowe dane lub z poziomu **karty raporty (wersja zapoznawcza)** w okienku szczegółowe informacje.


:::image type="content" source="./media/container-insights-persistent-volumes/pv-workload-example.PNG" alt-text="Przykładowy skoroszyt obciążenia Azure Monitor PV":::

### <a name="persistent-volume-details-workbook"></a>Skoroszyt szczegółów woluminu trwałego

Przegląd trwałego spisu woluminów można znaleźć w skoroszycie ze **szczegółowymi informacjami o woluminie trwałym** bezpośrednio z klastra AKS, wybierając pozycję skoroszyty z okienka po lewej stronie, z listy rozwijanej **Wyświetl skoroszyty** w okienku szczegółowe dane lub na karcie **raporty (wersja zapoznawcza)** w okienku szczegółowe informacje.


:::image type="content" source="./media/container-insights-persistent-volumes/pv-details-workbook-example.PNG" alt-text="Przykład skoroszytu szczegółów Azure Monitor PV":::

### <a name="persistent-volume-usage-recommended-alert"></a>Alert zalecanego użycia woluminu trwałego
Można włączyć alert zalecany, gdy średnie użycie PV dla elementu pod jest powyżej 80%. Dowiedz się więcej o alertach [tutaj](./container-insights-metric-alerts.md) i jak zastąpić domyślny próg w [tym miejscu](./container-insights-metric-alerts.md#configure-alertable-metrics-in-configmaps).
## <a name="next-steps"></a>Następne kroki

- Więcej informacji na temat zebranych metryk PV [znajdziesz tutaj](./container-insights-agent-config.md).