---
title: Azure Machine Learning monitorowania | Microsoft Docs
description: Dowiedz się, jak używać Azure Monitor do wyświetlania, analizowania i tworzenia alertów w oparciu o metryki z Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: larryfr
ms.author: aashishb
author: aashishb
ms.custom: subject-monitoring
ms.date: 10/01/2020
ms.openlocfilehash: c9b7230ca734bba1527ec5ecd975e1bae141a9b4
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/05/2021
ms.locfileid: "99584689"
---
# <a name="monitor-azure-machine-learning"></a>Monitorowanie usługi Azure Machine Learning

Jeśli masz krytyczne aplikacje i procesy biznesowe polegające na zasobach platformy Azure, chcesz monitorować te zasoby pod kątem ich dostępności, wydajności i operacji. W tym artykule opisano dane monitorowania wygenerowane przez Azure Machine Learning oraz sposób analizowania i generowania alertów dotyczących tych danych przy użyciu Azure Monitor.

> [!TIP]
> Informacje przedstawione w tym dokumencie są przeznaczone głównie dla __administratorów__, ponieważ opisują monitorowanie usługi Azure Machine Learning i skojarzonych usług platformy Azure. Jeśli jesteś analitykiem __danych__ lub __deweloperem__, a chcesz monitorować informacje specyficzne dla *przebiegów szkoleniowych modelu*, zobacz następujące dokumenty:
>
> * [Uruchamianie, monitorowanie i anulowanie przebiegów szkoleniowych](how-to-manage-runs.md)
> * [Rejestrowanie metryk dla przebiegów treningowych](how-to-track-experiments.md)
> * [Śledzenie eksperymentów za pomocą platformy MLflow](how-to-use-mlflow.md)
> * [Wizualizacja przebiegów za pomocą narzędzia TensorBoard](how-to-monitor-tensorboard.md)
>
> Jeśli chcesz monitorować informacje generowane przez modele wdrożone jako usługi sieci Web lub moduły IoT Edge, zobacz [zbieranie danych modelu](how-to-enable-data-collection.md) i [monitorowanie z Application Insights](how-to-enable-app-insights.md).

## <a name="what-is-azure-monitor"></a>Co to jest Azure Monitor?

Azure Machine Learning tworzy dane monitorowania przy użyciu [Azure monitor](../azure-monitor/overview.md), który jest pełną usługą monitorowania stosu na platformie Azure. Azure Monitor zapewnia pełen zestaw funkcji do monitorowania zasobów platformy Azure. Może również monitorować zasoby w innych chmurach i lokalnie.

Zacznij od artykułu [monitorowanie zasobów platformy Azure za pomocą Azure monitor](../azure-monitor/insights/monitor-azure-resource.md), który opisuje następujące pojęcia:

- Co to jest Azure Monitor?
- Koszty związane z monitorowaniem
- Monitorowanie danych zebranych na platformie Azure
- Konfigurowanie zbierania danych
- Standardowe narzędzia na platformie Azure na potrzeby analizowania danych monitorowania i powiadamiania o nich

Poniższe sekcje zostały skompilowane w tym artykule, opisując szczegółowe dane zebrane dla Azure Machine Learning. Te sekcje zawierają również przykłady konfigurowania zbierania danych i analizowania tych danych za pomocą narzędzi platformy Azure.

> [!TIP]
> Aby zrozumieć koszty związane z Azure Monitor, zobacz [użycie i szacowane koszty](../azure-monitor/platform/usage-estimated-costs.md). Aby zrozumieć czas, w jakim dane mają być wyświetlane w Azure Monitor, zobacz czas pozyskiwania [danych dziennika](../azure-monitor/platform/data-ingestion-time.md).

## <a name="monitoring-data-from-azure-machine-learning"></a>Monitorowanie danych z Azure Machine Learning

Azure Machine Learning gromadzi te same rodzaje danych monitorowania, jak inne zasoby platformy Azure, które są opisane w temacie [monitorowanie danych z zasobów platformy Azure](../azure-monitor/insights/monitor-azure-resource.md#monitoring-data). 

Aby uzyskać szczegółowe informacje o dziennikach i metrykach utworzonych przez Azure Machine Learning, zobacz [Informacje o danych monitorowania Azure Machine Learning](monitor-resource-reference.md) .

<a id="configuration"></a>

## <a name="collection-and-routing"></a>Kolekcja i Routing

Metryki platformy i dziennik aktywności są zbierane i przechowywane automatycznie, ale mogą być kierowane do innych lokalizacji przy użyciu ustawień diagnostycznych.  

Dzienniki zasobów nie są zbierane i przechowywane do momentu utworzenia ustawienia diagnostycznego i skierowania ich do co najmniej jednej lokalizacji.

Zobacz [Tworzenie ustawień diagnostycznych, aby zbierać dzienniki platformy i metryki na platformie Azure](../azure-monitor/platform/diagnostic-settings.md) w celu uzyskania szczegółowego procesu tworzenia ustawień diagnostycznych przy użyciu Azure Portal, interfejsu wiersza polecenia lub programu PowerShell. Podczas tworzenia ustawienia diagnostycznego należy określić, które kategorie dzienników mają być zbierane. Kategorie Azure Machine Learning są wymienione w temacie [Azure Machine Learning monitorowanie danych](monitor-resource-reference.md#resource-logs).

> [!IMPORTANT]
> Włączenie tych ustawień wymaga dodatkowych usług platformy Azure (konta magazynu, centrum zdarzeń lub Log Analytics), co może zwiększyć koszt. Aby obliczyć szacowany koszt, odwiedź [Kalkulator cen platformy Azure](https://azure.microsoft.com/pricing/calculator).

Następujące dzienniki można skonfigurować dla Azure Machine Learning:

| Kategoria | Opis |
|:---|:---|
| AmlComputeClusterEvent | Zdarzenia z Azure Machine Learning klastrów obliczeniowych. |
| AmlComputeClusterNodeEvent | Zdarzenia z węzłów w klastrze obliczeniowym Azure Machine Learning. |
| AmlComputeJobEvent | Zdarzenia z zadań uruchomionych na Azure Machine Learning COMPUTE. |

> [!NOTE]
> Po włączeniu metryk w ustawieniu diagnostycznym informacje o wymiarach nie są obecnie uwzględniane jako część informacji wysyłanych do konta magazynu, centrum zdarzeń lub usługi log Analytics.

Zebrane metryki i dzienniki zostały omówione w poniższych sekcjach.

## <a name="analyzing-metrics"></a>Analizowanie metryk

Metryki dla Azure Machine Learning, a także metryki z innych usług platformy Azure, można analizować, otwierając **metryki** z menu **Azure monitor** . Aby uzyskać szczegółowe informacje na temat korzystania z tego narzędzia, zobacz [Rozpoczynanie pracy z usługą Azure Eksplorator metryk](../azure-monitor/platform/metrics-getting-started.md) .

Aby zapoznać się z listą zebranych metryk platformy, zobacz [monitorowanie Azure Machine Learning metryki odwołań danych](monitor-resource-reference.md#metrics).

Wszystkie metryki dla Azure Machine Learning znajdują się w **obszarze roboczym usługi** przestrzeń nazw Machine Learning.

![Eksplorator metryk z wybranym obszarem roboczym usługi Machine Learning](./media/monitor-azure-machine-learning/metrics.png)

W przypadku odwołania można zobaczyć listę [wszystkich metryk zasobów obsługiwanych w Azure monitor](../azure-monitor/platform/metrics-supported.md).

> [!TIP]
> Dane metryk Azure Monitor są dostępne przez 90 dni. Jednak podczas tworzenia wykresów można wizualizować tylko 30 dni. Na przykład jeśli chcesz wizualizować 90-dniowy okres, musisz go podzielić na trzy wykresy o 30 dni w okresie 90 dni.
### <a name="filtering-and-splitting"></a>Filtrowanie i dzielenie

W przypadku metryk, które obsługują wymiary, można zastosować filtry przy użyciu wartości wymiaru. Na przykład filtrowanie **aktywnych rdzeni** dla **nazwy klastra** `cpu-cluster` . 

Możesz również podzielić metrykę według wymiaru, aby wizualizować różne segmenty porównania z innymi. Na przykład należy podzielić **Typ etapu potoku** , aby zobaczyć liczbę typów kroków użytych w potoku.

Więcej informacji o filtrowaniu i dzieleniu można znaleźć w temacie [Advanced Features of Azure monitor](../azure-monitor/platform/metrics-charts.md).

<a id="analyzing-log-data"></a>
## <a name="analyzing-logs"></a>Analizowanie dzienników

Użycie Log Analytics Azure Monitor wymaga utworzenia konfiguracji diagnostycznej i włączenia __informacji wysyłanych do log Analytics__. Aby uzyskać więcej informacji, zobacz sekcję [zbieranie i Routing](#collection-and-routing) .

Dane w dziennikach Azure Monitor są przechowywane w tabelach, w których każda tabela ma swój własny zestaw unikatowych właściwości. Azure Machine Learning przechowuje dane w następujących tabelach:

| Tabela | Opis |
|:---|:---|
| AmlComputeClusterEvent | Zdarzenia z Azure Machine Learning klastrów obliczeniowych. |
| AmlComputeClusterNodeEvent | Zdarzenia z węzłów w klastrze obliczeniowym Azure Machine Learning. |
| AmlComputeJobEvent | Zdarzenia z zadań uruchomionych na Azure Machine Learning COMPUTE. |

> [!IMPORTANT]
> Po wybraniu opcji **dzienniki** z menu Azure Machine Learning, log Analytics zostanie otwarty z zakresem zapytania ustawionym na bieżący obszar roboczy. Oznacza to, że zapytania dziennika będą zawierać tylko dane z tego zasobu. Jeśli chcesz uruchomić zapytanie, które zawiera dane z innych baz danych lub danych z innych usług platformy Azure, wybierz pozycję **dzienniki** z menu **Azure monitor** . Aby uzyskać szczegółowe informacje [, zobacz zakres zapytań dzienników i zakres czasu w Azure Monitor Log Analytics](../azure-monitor/log-query/scope.md) .

Aby uzyskać szczegółowe informacje na temat dzienników i metryk, zobacz [Azure Machine Learning informacje o monitorowaniu danych](monitor-resource-reference.md).

### <a name="sample-kusto-queries"></a>Przykładowe zapytania Kusto

> [!IMPORTANT]
> Po wybraniu opcji **dzienniki** z menu [nazwa usługi] log Analytics jest otwierany z zakresem zapytania ustawionym na bieżący obszar roboczy Azure Machine Learning. Oznacza to, że zapytania dziennika będą zawierać tylko dane z tego zasobu. Jeśli chcesz uruchomić zapytanie, które zawiera dane z innych obszarów roboczych lub danych z innych usług platformy Azure, wybierz pozycję **dzienniki** z menu **Azure monitor** . Aby uzyskać szczegółowe informacje [, zobacz zakres zapytań dzienników i zakres czasu w Azure Monitor Log Analytics](../azure-monitor/log-query/scope.md) .

Poniżej przedstawiono zapytania, których można użyć w celu ułatwienia monitorowania zasobów Azure Machine Learning: 

+ Pobierz zadania zakończone niepowodzeniem w ciągu ostatnich pięciu dni:

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

+ Pobierz zdarzenia klastra w ciągu ostatnich pięciu dni dla klastrów, w których Standard_D1_V2 rozmiaru maszyny wirtualnej:

    ```Kusto
    AmlComputeClusterEvent
    | where TimeGenerated > ago(4d) and VmSize == "STANDARD_D1_V2"
    | project  ClusterName , InitialNodeCount , MaximumNodeCount , QuotaAllocated , QuotaUtilized
    ```

+ Pobierz węzły przydzieloną w ciągu ostatnich ośmiu dni:

    ```Kusto
    AmlComputeClusterNodeEvent
    | where TimeGenerated > ago(8d) and NodeAllocationTime  > ago(8d)
    | distinct NodeId
    ```

## <a name="alerts"></a>Alerty

Możesz uzyskać dostęp do alertów dla Azure Machine Learning, otwierając **alerty** z menu **Azure monitor** . Aby uzyskać szczegółowe informacje na temat tworzenia alertów, zobacz [Tworzenie i wyświetlanie alertów metryk oraz zarządzanie nimi za pomocą Azure monitor](../azure-monitor/platform/alerts-metric.md) .

Poniższa tabela zawiera listę typowych i zalecanych reguł alertów dotyczących metryk dla Azure Machine Learning:

| Typ alertu | Warunek | Opis |
|:---|:---|:---|
| Wdrażanie modelu nie powiodło się | Typ agregacji: łącznie, operator: większy niż, wartość progowa: 0 | Gdy co najmniej jedno wdrożenie modelu nie powiodło się |
| Procent wykorzystania przydziałów | Typ agregacji: Average, operator: większe niż, wartość progowa: 90| Gdy wartość procentowa wykorzystania przydziałów jest większa niż 90% |
| Węzły niezdatne do użytku | Typ agregacji: łącznie, operator: większy niż, wartość progowa: 0 | Gdy istnieje co najmniej jeden niezdatny do użytku węzeł |

## <a name="next-steps"></a>Następne kroki

- Informacje o dziennikach i metrykach znajdują się w temacie [Monitoring Azure Machine Learning Data Reference](monitor-resource-reference.md).
- Aby uzyskać informacje na temat pracy z przydziałami związanymi z Azure Machine Learning, zobacz [Zarządzanie przydziałami zasobów platformy Azure i ich żądania](how-to-manage-quotas.md).
- Aby uzyskać szczegółowe informacje na temat monitorowania zasobów platformy Azure, zobacz [monitorowanie zasobów platformy Azure za pomocą Azure monitor](../azure-monitor/insights/monitor-azure-resource.md).
