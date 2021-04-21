---
title: Monitorowanie Azure Machine Learning | Microsoft Docs
description: Dowiedz się, jak używać Azure Monitor do wyświetlania, analizowania i tworzenia alertów dotyczących metryk z Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: larryfr
ms.author: aashishb
author: aashishb
ms.custom: subject-monitoring
ms.date: 10/01/2020
ms.openlocfilehash: e5fd0fdd5a6f9a4a7537a844b096efdfef253638
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107816858"
---
# <a name="monitor-azure-machine-learning"></a>Monitorowanie usługi Azure Machine Learning

Jeśli masz krytyczne aplikacje i procesy biznesowe zależne od zasobów platformy Azure, chcesz monitorować te zasoby pod względu na ich dostępność, wydajność i działanie. W tym artykule opisano dane monitorowania generowane przez Azure Machine Learning oraz sposób analizowania i generowania alertów dotyczących tych danych za pomocą Azure Monitor.

> [!TIP]
> Informacje zawarte w tym dokumencie są przeznaczone głównie __dla__ administratorów, ponieważ opisano w nim monitorowanie Azure Machine Learning service i skojarzonych usług platformy Azure. Jeśli jesteś analitykiem __danych__ lub deweloperem __i__ chcesz monitorować informacje specyficzne dla przebiegów trenowania *modelu,* zobacz następujące dokumenty:
>
> * [Uruchamianie, monitorowanie i anulowanie przebiegów trenowania](how-to-track-monitor-analyze-runs.md)
> * [Rejestrowanie metryk dla przebiegów treningowych](how-to-log-view-metrics.md)
> * [Śledzenie eksperymentów za pomocą platformy MLflow](how-to-use-mlflow.md)
> * [Wizualizacja przebiegów za pomocą narzędzia TensorBoard](how-to-monitor-tensorboard.md)
>
> Jeśli chcesz monitorować informacje generowane przez modele wdrożone jako usługi internetowe lub moduły IoT Edge, zobacz Zbieranie danych [modelu](how-to-enable-data-collection.md) i Monitorowanie za pomocą [Application Insights](how-to-enable-app-insights.md).

## <a name="what-is-azure-monitor"></a>Co to jest Azure Monitor?

Azure Machine Learning danych monitorowania przy użyciu [Azure Monitor](../azure-monitor/overview.md), która jest usługą monitorowania pełnego stosu na platformie Azure. Azure Monitor udostępnia kompletny zestaw funkcji do monitorowania zasobów platformy Azure. Może również monitorować zasoby w innych chmurach i lokalnie.

Zacznij od artykułu [Monitorowanie zasobów platformy Azure za pomocą Azure Monitor](../azure-monitor/essentials/monitor-azure-resource.md), który opisuje następujące pojęcia:

- Co to jest Azure Monitor?
- Koszty związane z monitorowaniem
- Monitorowanie danych zebranych na platformie Azure
- Konfigurowanie zbierania danych
- Standardowe narzędzia na platformie Azure do analizowania danych monitorowania i tworzenia alertów

Poniższe sekcje są kompilowane na podstawie tego artykułu, opisując konkretne dane zebrane dla Azure Machine Learning. Te sekcje zawierają również przykłady konfigurowania zbierania danych i analizowania tych danych za pomocą narzędzi platformy Azure.

> [!TIP]
> Aby zrozumieć koszty związane z Azure Monitor, zobacz [Użycie i szacowane koszty](../azure-monitor//usage-estimated-costs.md). Aby zrozumieć, jak długo dane mają być wyświetlane w Azure Monitor, zobacz [Czas pozyskiwania danych dziennika](../azure-monitor/logs/data-ingestion-time.md).

## <a name="monitoring-data-from-azure-machine-learning"></a>Monitorowanie danych z Azure Machine Learning

Azure Machine Learning zbiera te same rodzaje danych monitorowania co inne zasoby platformy Azure, które opisano w tesłudze [monitorowania danych z zasobów platformy Azure.](../azure-monitor/essentials/monitor-azure-resource.md#monitoring-data) 

Zobacz [Azure Machine Learning danych monitorowania,](monitor-resource-reference.md) aby uzyskać szczegółowe informacje na temat dzienników i metryk utworzonych przez Azure Machine Learning.

<a id="configuration"></a>

## <a name="collection-and-routing"></a>Zbieranie i routing

Metryki platformy i dziennik aktywności są zbierane i przechowywane automatycznie, ale mogą być kierowane do innych lokalizacji przy użyciu ustawienia diagnostycznego.  

Dzienniki zasobów nie są zbierane i przechowywane, dopóki nie utworzysz ustawienia diagnostycznego i nie rozsyłasz ich do co najmniej jednej lokalizacji.

Zobacz [Create diagnostic setting to collect platform logs and metrics in Azure](../azure-monitor/essentials/diagnostic-settings.md) (Tworzenie ustawienia diagnostycznego w celu zbierania dzienników i metryk platformy na platformie Azure), aby uzyskać szczegółowy opis procesu tworzenia ustawienia diagnostycznego przy użyciu Azure Portal, interfejsu wiersza polecenia lub programu PowerShell. Podczas tworzenia ustawienia diagnostycznego należy określić kategorie dzienników do zebrania. Kategorie kategorii dla Azure Machine Learning są wymienione w te Azure Machine Learning [danych monitorowania.](monitor-resource-reference.md#resource-logs)

> [!IMPORTANT]
> Włączenie tych ustawień wymaga dodatkowych usług platformy Azure (konta magazynu, centrum zdarzeń lub usługi Log Analytics), co może zwiększyć koszt. Aby obliczyć szacowany koszt, odwiedź kalkulator [cen platformy Azure.](https://azure.microsoft.com/pricing/calculator)

Można skonfigurować następujące dzienniki dla Azure Machine Learning:

| Kategoria | Opis |
|:---|:---|
| AmlComputeClusterEvent | Zdarzenia z Azure Machine Learning klastrów obliczeniowych. |
| AmlComputeClusterNodeEvent | Zdarzenia z węzłów w Azure Machine Learning obliczeniowym. |
| AmlComputeJobEvent | Zdarzenia z zadań uruchomionych w Azure Machine Learning obliczeniowej. |

> [!NOTE]
> Po włączeniu metryk w ustawieniu diagnostycznym informacje o wymiarach nie są obecnie uwzględniane jako część informacji wysyłanych do konta magazynu, centrum zdarzeń lub analizy dzienników.

Metryki i dzienniki, które można zbierać, zostały omówione w poniższych sekcjach.

## <a name="analyzing-metrics"></a>Analizowanie metryk

Możesz analizować metryki dla Azure Machine Learning, a także metryki z innych usług platformy Azure, otwierając pozycję **Metryki** z Azure Monitor **menu.** Zobacz [Wprowadzenie do usługi Azure Eksplorator metryk,](../azure-monitor/essentials/metrics-getting-started.md) aby uzyskać szczegółowe informacje na temat korzystania z tego narzędzia.

Aby uzyskać listę zebranych metryk platformy, zobacz Monitorowanie metryk Azure Machine Learning [danych referencyjnych.](monitor-resource-reference.md#metrics)

Wszystkie metryki dla Azure Machine Learning znajdują się w przestrzeni nazw **Machine Learning Service Workspace**.

![Eksplorator metryk z wybranym obszarem roboczym Machine Learning Service](./media/monitor-azure-machine-learning/metrics.png)

Aby uzyskać informacje, możesz wyświetlić listę wszystkich metryk zasobów obsługiwanych w [programie Azure Monitor](../azure-monitor/essentials/metrics-supported.md).

> [!TIP]
> Azure Monitor metryki są dostępne przez 90 dni. Jednak podczas tworzenia wykresów można wizualizować tylko 30 dni. Jeśli na przykład chcesz zwizualizować okres 90 dni, musisz rozbić go na trzy wykresy po 30 dniach w okresie 90 dni.
### <a name="filtering-and-splitting"></a>Filtrowanie i dzielenie

W przypadku metryk, które obsługują wymiary, można zastosować filtry przy użyciu wartości wymiaru. Na przykład filtrowanie **aktywnych rdzeni dla** nazwy **klastra** o wartości `cpu-cluster` . 

Możesz również podzielić metrykę według wymiaru, aby zwizualizować porównanie różnych segmentów metryki. Na przykład podziel typ **kroku potoku,** aby wyświetlić liczbę typów kroków używanych w potoku.

Aby uzyskać więcej informacji na temat filtrowania i dzielenia, zobacz [Zaawansowane funkcje Azure Monitor](../azure-monitor/essentials/metrics-charts.md).

<a id="analyzing-log-data"></a>
## <a name="analyzing-logs"></a>Analizowanie dzienników

Korzystanie Azure Monitor log Analytics wymaga utworzenia konfiguracji diagnostycznej i włączenia opcji __Wyślij informacje do usługi Log Analytics.__ Aby uzyskać więcej informacji, zobacz [sekcję Zbieranie i routing.](#collection-and-routing)

Dane w Azure Monitor są przechowywane w tabelach, a każda tabela ma własny zestaw unikatowych właściwości. Azure Machine Learning przechowuje dane w następujących tabelach:

| Tabela | Opis |
|:---|:---|
| AmlComputeClusterEvent | Zdarzenia z Azure Machine Learning klastrów obliczeniowych. |
| AmlComputeClusterNodeEvent | Zdarzenia z węzłów w Azure Machine Learning obliczeniowym. |
| AmlComputeJobEvent | Zdarzenia z zadań uruchomionych w Azure Machine Learning obliczeniowej. |

> [!IMPORTANT]
> Po wybraniu pozycji **Dzienniki** z menu Azure Machine Learning usługa Log Analytics zostanie otwarta z zakresem zapytania ustawionym na bieżący obszar roboczy. Oznacza to, że zapytania dziennika będą zawierać tylko dane z tego zasobu. Jeśli chcesz uruchomić zapytanie, które zawiera dane z innych baz  danych lub danych z innych usług platformy Azure, wybierz pozycję Dzienniki z Azure Monitor **menu.** Aby [uzyskać szczegółowe informacje, zobacz](../azure-monitor/logs/scope.md) Zakres i zakres czasu zapytania Azure Monitor Log Analytics.

Aby uzyskać szczegółowe informacje na temat dzienników i metryk, zobacz [Azure Machine Learning danych monitorowania](monitor-resource-reference.md).

### <a name="sample-kusto-queries"></a>Przykładowe zapytania Kusto

> [!IMPORTANT]
> Po wybraniu pozycji **Dzienniki** z menu [nazwa usługi] usługa Log Analytics zostanie otwarta z zakresem zapytania ustawionym na bieżący Azure Machine Learning roboczy. Oznacza to, że zapytania dziennika będą zawierać tylko dane z tego zasobu. Jeśli chcesz uruchomić zapytanie, które zawiera dane z innych obszarów  roboczych lub dane z innych usług platformy Azure, wybierz pozycję Dzienniki Azure Monitor **menu.** Aby [uzyskać szczegółowe informacje, zobacz](../azure-monitor/logs/scope.md) Zakres i zakres czasu zapytania Azure Monitor Log Analytics.

Poniżej przedstawiono zapytania, których można użyć do monitorowania Azure Machine Learning zasobów: 

+ Uzyskiwanie zadań, które zakończyły się niepowodzeniem w ciągu ostatnich pięciu dni:

    ```Kusto
    AmlComputeJobEvent
    | where TimeGenerated > ago(5d) and EventType == "JobFailed"
    | project  TimeGenerated , ClusterId , EventType , ExecutionState , ToolType
    ```

+ Pobierz rekordy dla określonej nazwy zadania:

    ```Kusto
    AmlComputeJobEvent
    | where JobName == "automl_a9940991-dedb-4262-9763-2fd08b79d8fb_setup"
    | project  TimeGenerated , ClusterId , EventType , ExecutionState , ToolType
    ```

+ Pobierz zdarzenia klastra z ostatnich pięciu dni dla klastrów, w których rozmiar maszyny wirtualnej jest Standard_D1_V2:

    ```Kusto
    AmlComputeClusterEvent
    | where TimeGenerated > ago(4d) and VmSize == "STANDARD_D1_V2"
    | project  ClusterName , InitialNodeCount , MaximumNodeCount , QuotaAllocated , QuotaUtilized
    ```

+ Pobierz węzły przydzielone w ciągu ostatnich ośmiu dni:

    ```Kusto
    AmlComputeClusterNodeEvent
    | where TimeGenerated > ago(8d) and NodeAllocationTime  > ago(8d)
    | distinct NodeId
    ```

## <a name="alerts"></a>Alerty

Dostęp do alertów dotyczących Azure Machine Learning można uzyskać, otwierając pozycję **Alerty** **z** Azure Monitor menu. Aby [uzyskać szczegółowe informacje na temat tworzenia alertów, zobacz](../azure-monitor/alerts/alerts-metric.md) Tworzenie i wyświetlanie alertów dotyczących metryk Azure Monitor zarządzanie nimi przy użyciu usługi .

W poniższej tabeli wymieniono typowe i zalecane reguły alertów dotyczących metryk Azure Machine Learning:

| Typ alertu | Warunek | Opis |
|:---|:---|:---|
| Wdrażanie modelu nie powiodło się | Typ agregacji: Suma, Operator: Większe niż, Wartość progowa: 0 | Jeśli co najmniej jedno wdrożenie modelu zakończyło się niepowodzeniem |
| Procent wykorzystania limitu przydziału | Typ agregacji: Średnia, Operator: Większe niż, Wartość progowa: 90| Gdy procent wykorzystania limitu przydziału jest większy niż 90% |
| Węzły bezużytelne | Typ agregacji: Suma, Operator: Większe niż, Wartość progowa: 0 | W przypadku co najmniej jednego węzła bezużytecznego |

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać odwołanie do dzienników i metryk, zobacz [Temat monitorowania Azure Machine Learning danych.](monitor-resource-reference.md)
- Aby uzyskać informacje na temat pracy z limitami przydziału związanymi z Azure Machine Learning, zobacz Zarządzanie przydziałami zasobów platformy Azure i żądanie [ich.](how-to-manage-quotas.md)
- Aby uzyskać szczegółowe informacje na temat monitorowania zasobów platformy Azure, zobacz [Monitorowanie zasobów platformy Azure za pomocą Azure Monitor.](../azure-monitor/essentials/monitor-azure-resource.md)
