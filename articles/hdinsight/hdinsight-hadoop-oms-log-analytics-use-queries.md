---
title: Wykonywanie zapytań dotyczących dzienników Azure Monitor do monitorowania klastrów usługi Azure HDInsight
description: Dowiedz się, jak uruchamiać zapytania dotyczące dzienników Azure Monitor, aby monitorować zadania uruchomione w klastrze usługi HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/02/2019
ms.openlocfilehash: 3cf97039983ecec44a7c3a32e178fdcf9f9c45ff
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104872187"
---
# <a name="query-azure-monitor-logs-to-monitor-hdinsight-clusters"></a>Wykonywanie zapytań dotyczących dzienników Azure Monitor do monitorowania klastrów usługi HDInsight

Zapoznaj się z podstawowymi scenariuszami dotyczącymi używania dzienników Azure Monitor do monitorowania klastrów usługi Azure HDInsight:

* [Analizowanie metryk klastra usługi HDInsight](#analyze-hdinsight-cluster-metrics)
* [Tworzenie alertów dotyczących zdarzeń](#create-alerts-for-tracking-events)

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Należy skonfigurować klaster usługi HDInsight do używania dzienników Azure Monitor i dodać do obszaru roboczego usługi rejestrowania Azure Monitor specyficzne dla klastra usługi HDInsight. Aby uzyskać instrukcje, zobacz [Korzystanie z dzienników Azure monitor z klastrami usługi HDInsight](hdinsight-hadoop-oms-log-analytics-tutorial.md).

## <a name="analyze-hdinsight-cluster-metrics"></a>Analizowanie metryk klastra usługi HDInsight

Dowiedz się, jak szukać określonych metryk dla klastra usługi HDInsight.

1. Otwórz obszar roboczy Log Analytics, który jest skojarzony z klastrem usługi HDInsight z Azure Portal.
1. W obszarze **Ogólne** wybierz pozycję **Dzienniki**.
1. Wpisz następujące zapytanie w polu wyszukiwania, aby wyszukać wszystkie metryki dla wszystkich dostępnych metryk dla wszystkich klastrów usługi HDInsight skonfigurowanych do korzystania z dzienników Azure Monitor, a następnie wybierz pozycję **Uruchom**. Przejrzyj wyniki.

    ```kusto
    search *
    ```

    :::image type="content" source="./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-metrics.png" alt-text="Wyszukiwanie w usłudze Apache Ambari Analytics — wszystkie metryki":::

1. Z menu po lewej stronie wybierz kartę **Filtr** .

1. W obszarze **Typ** wybierz pozycję **puls**. Następnie wybierz pozycję **zastosuj & Uruchom**.

    :::image type="content" source="./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-metrics.png" alt-text="metryki wyszukiwania dla usługi log Analytics":::

1. Zwróć uwagę, że zapytanie w polu tekstowym zmieni się na:

    ```kusto
    search *
    | where Type == "Heartbeat"
    ```

1. Możesz Dig się bardziej szczegółowo przy użyciu opcji dostępnych w menu po lewej stronie. Na przykład:

   - Aby wyświetlić dzienniki z określonego węzła:

     :::image type="content" source="./media/hdinsight-hadoop-oms-log-analytics-use-queries/log-analytics-specific-node.png" alt-text="Wyszukaj określone błędy output1":::

   - Aby wyświetlić dzienniki w określonym czasie:

     :::image type="content" source="./media/hdinsight-hadoop-oms-log-analytics-use-queries/log-analytics-specific-time.png" alt-text="Wyszukaj określone błędy output2":::

1. Wybierz pozycję **zastosuj & Uruchom** i przejrzyj wyniki. Należy również pamiętać, że zapytanie zostało zaktualizowane do:

    ```kusto
    search *
    | where Type == "Heartbeat"
    | where (Computer == "zk2-myhado") and (TimeGenerated == "2019-12-02T23:15:02.69Z" or TimeGenerated == "2019-12-02T23:15:08.07Z" or TimeGenerated == "2019-12-02T21:09:34.787Z")
    ```

### <a name="additional-sample-queries"></a>Dodatkowe przykładowe zapytania

Przykładowe zapytanie bazujące na średniej ilości zasobów używanych w 10-minutowych interwałach, sklasyfikowane według nazwy klastra:

```kusto
search in (metrics_resourcemanager_queue_root_default_CL) * 
| summarize AggregatedValue = avg(UsedAMResourceMB_d) by ClusterName_s, bin(TimeGenerated, 10m)
```

Zamiast rafinacji na podstawie średniej używanych zasobów, można użyć następującego zapytania, aby uściślić wyniki w zależności od tego, kiedy zostały użyte maksymalne zasoby (a także 90 i używany 95. percentyl) w 10-minutowych oknach:

```kusto
search in (metrics_resourcemanager_queue_root_default_CL) * 
| summarize ["max(UsedAMResourceMB_d)"] = max(UsedAMResourceMB_d), ["pct95(UsedAMResourceMB_d)"] = percentile(UsedAMResourceMB_d, 95), ["pct90(UsedAMResourceMB_d)"] = percentile(UsedAMResourceMB_d, 90) by ClusterName_s, bin(TimeGenerated, 10m)
```

## <a name="create-alerts-for-tracking-events"></a>Tworzenie alertów dotyczących zdarzeń śledzenia

Pierwszym krokiem tworzenia alertu jest dotarcie do zapytania, na podstawie którego zostanie wyzwolony alert. Możesz użyć dowolnego zapytania, które chcesz utworzyć alert.

1. Otwórz obszar roboczy Log Analytics, który jest skojarzony z klastrem usługi HDInsight z Azure Portal.
1. W obszarze **Ogólne** wybierz pozycję **Dzienniki**.
1. Uruchom następujące zapytanie, dla którego chcesz utworzyć alert, a następnie wybierz pozycję **Uruchom**.

    ```kusto
    metrics_resourcemanager_queue_root_default_CL | where AppsFailed_d > 0
    ```

    Zapytanie zawiera listę aplikacji, które nie działają w klastrach usługi HDInsight.

1. Wybierz pozycję **Nowa reguła alertu** w górnej części strony.

    :::image type="content" source="./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-create-alert-query.png" alt-text="Nowa reguła alertu":::

1. W oknie **Tworzenie reguły** wprowadź zapytanie i inne szczegóły, aby utworzyć alert, a następnie wybierz pozycję **Utwórz regułę alertu**.

    :::image type="content" source="./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-create-alert.png" alt-text="Zdefiniuj warunek alertu.":::

### <a name="edit-or-delete-an-existing-alert"></a>Edytuj lub usuń istniejący alert

1. Otwórz obszar roboczy Log Analytics z Azure Portal.

1. W menu po lewej stronie w obszarze **monitorowanie** wybierz pozycję **alerty**.

1. W kierunku górnej części wybierz pozycję **Zarządzaj regułami alertów**.

1. Wybierz Alert, który chcesz edytować lub usunąć.

1. Dostępne są następujące opcje: **Zapisz**, **Odrzuć**, **Wyłącz** i **Usuń**.

    :::image type="content" source="media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-edit-alert.png" alt-text="Alert dziennika Azure Monitor usługi HDInsight usuwanie edycji":::

Aby uzyskać więcej informacji, zobacz [Tworzenie i wyświetlanie alertów metryk i zarządzanie nimi przy użyciu Azure monitor](../azure-monitor/alerts/alerts-metric.md).

## <a name="see-also"></a>Zobacz też

* [Wprowadzenie do zapytań dotyczących dzienników w usłudze Azure Monitor](../azure-monitor/logs/get-started-queries.md)
* [Tworzenie widoków niestandardowych za pomocą projektanta widoków w Azure Monitor](../azure-monitor/visualize/view-designer.md)
