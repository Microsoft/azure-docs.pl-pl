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
ms.date: 03/05/2020
ms.openlocfilehash: eb4f46322bec57fb4412d3ddebb345640556ca5c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "78399109"
---
# <a name="monitoring-azure-machine-learning"></a>Azure Machine Learning monitorowania

W tym artykule opisano dane monitorowania wygenerowane przez Azure Machine Learning. Opisano w nim również, jak można użyć Azure Monitor do analizowania danych i definiowania alertów.

> [!TIP]
> Informacje przedstawione w tym dokumencie są przeznaczone głównie dla administratorów, ponieważ opisują monitorowanie Azure Machine Learning. Jeśli jesteś analitykiem danych lub deweloperem, a chcesz monitorować informacje specyficzne dla przebiegów szkoleniowych modelu, zobacz następujące dokumenty:
>
> * [Uruchamianie, monitorowanie i anulowanie przebiegów szkoleniowych](how-to-manage-runs.md)
> * [Rejestrowanie metryk dla przebiegów treningowych](how-to-track-experiments.md)
> * [Śledzenie eksperymentów za pomocą platformy MLflow](how-to-use-mlflow.md)
> * [Wizualizacja przebiegów za pomocą narzędzia TensorBoard](how-to-monitor-tensorboard.md)

## <a name="azure-monitor"></a>Azure Monitor

Azure Machine Learning dzienniki danych monitorowania przy użyciu Azure Monitor, który jest pełną usługą monitorowania stosu na platformie Azure. Azure Monitor zapewnia pełen zestaw funkcji do monitorowania zasobów platformy Azure. Może również monitorować zasoby w innych chmurach i lokalnie.

Rozpocznij od artykułu [Azure monitor przegląd](/azure/azure-monitor/overview), który zawiera przegląd możliwości monitorowania. Poniższe sekcje zawierają informacje dotyczące korzystania z Azure Monitor z Azure Machine Learning.

Aby zrozumieć koszty związane z Azure Monitor, zobacz [użycie i szacowane koszty](/azure/azure-monitor/platform/usage-estimated-costs). Aby zrozumieć czas, w jakim dane mają być wyświetlane w Azure Monitor, zobacz czas pozyskiwania [danych dziennika](/azure/azure-monitor/platform/data-ingestion-time).

## <a name="monitoring-data-from-azure-machine-learning"></a>Monitorowanie danych z Azure Machine Learning

Azure Machine Learning gromadzi te same rodzaje danych monitorowania jak inne zasoby platformy Azure, które są opisane w temacie [monitorowanie danych z zasobów platformy Azure](/azure/azure-monitor/insights/monitor-azure-resource#monitoring-data). Aby uzyskać szczegółowe informacje o dziennikach i metrykach utworzonych przez Azure Machine Learning, zobacz [Informacje o danych monitorowania Azure Machine Learning](monitor-resource-reference.md) .

## <a name="analyzing-metric-data"></a>Analizowanie danych metryki

Metryki dla Azure Machine Learning można analizować, otwierając **metryki** z menu **Azure monitor** . Aby uzyskać szczegółowe informacje na temat korzystania z tego narzędzia, zobacz [Rozpoczynanie pracy z usługą Azure Eksplorator metryk](/azure/azure-monitor/platform/metrics-getting-started) .

Wszystkie metryki dla Azure Machine Learning znajdują się w **obszarze roboczym usługi**przestrzeń nazw Machine Learning.

![Eksplorator metryk z wybranym obszarem roboczym usługi Machine Learning](./media/monitor-azure-machine-learning/metrics.png)

### <a name="filtering-and-splitting"></a>Filtrowanie i dzielenie

W przypadku metryk, które obsługują wymiary, można zastosować filtry przy użyciu wartości wymiaru. Na przykład filtrowanie **aktywnych rdzeni** dla **nazwy klastra** `cpu-cluster` . 

Możesz również podzielić metrykę według wymiaru, aby wizualizować różne segmenty porównania z innymi. Na przykład należy podzielić **Typ etapu potoku** , aby zobaczyć liczbę typów kroków użytych w potoku.

Więcej informacji o filtrowaniu i dzieleniu można znaleźć w temacie [Advanced Features of Azure monitor](/azure/azure-monitor/platform/metrics-charts).

## <a name="alerts"></a>Alerty

Możesz uzyskać dostęp do alertów dla Azure Machine Learning, otwierając **alerty** z menu **Azure monitor** . Aby uzyskać szczegółowe informacje na temat tworzenia alertów, zobacz [Tworzenie i wyświetlanie alertów metryk oraz zarządzanie nimi za pomocą Azure monitor](/azure/azure-monitor/platform/alerts-metric) .

Poniższa tabela zawiera listę typowych i zalecanych reguł alertów dotyczących metryk dla Azure Machine Learning:

| Typ alertu | Warunek | Opis |
|:---|:---|:---|
| Wdrażanie modelu nie powiodło się | Typ agregacji: łącznie, operator: większy niż, wartość progowa: 0 | Gdy co najmniej jedno wdrożenie modelu nie powiodło się |
| Procent wykorzystania przydziałów | Typ agregacji: Average, operator: większe niż, wartość progowa: 90| Gdy wartość procentowa wykorzystania przydziałów jest większa niż 90% |
| Węzły niezdatne do użytku | Typ agregacji: łącznie, operator: większy niż, wartość progowa: 0 | Gdy istnieje co najmniej jeden niezdatny do użytku węzeł |

## <a name="configuration"></a>Konfiguracja

> [!IMPORTANT]
> __Nie trzeba konfigurować metryk dla Azure Machine Learning__, są one zbierane automatycznie i są dostępne w Eksplorator metryk do monitorowania i wysyłania alertów.

Możesz dodać ustawienie diagnostyczne, aby skonfigurować następujące funkcje:

* Archiwizuj informacje o dziennikach i metrykach na koncie usługi Azure Storage.
* Przesyłaj strumieniowo informacje o dziennikach i metrykach do centrum zdarzeń platformy Azure.
* Wyślij informacje o dziennikach i metrykach do Log Analytics Azure Monitor.

Włączenie tych ustawień wymaga dodatkowych usług platformy Azure (konta magazynu, centrum zdarzeń lub Log Analytics), co może zwiększyć koszt. Aby obliczyć szacowany koszt, odwiedź [Kalkulator cen platformy Azure](https://azure.microsoft.com/pricing/calculator).

Aby uzyskać więcej informacji na temat tworzenia ustawień diagnostycznych, zobacz [Tworzenie ustawień diagnostycznych w celu zbierania dzienników platformy i metryk na platformie Azure](/azure/azure-monitor/platform/diagnostic-settings).

Następujące dzienniki można skonfigurować dla Azure Machine Learning:

| Kategoria | Opis |
|:---|:---|
| AmlComputeClusterEvent | Zdarzenia z Azure Machine Learning klastrów obliczeniowych. |
| AmlComputeClusterNodeEvent | Zdarzenia z węzłów w klastrze obliczeniowym Azure Machine Learning. |
| AmlComputeJobEvent | Zdarzenia z zadań uruchomionych na Azure Machine Learning COMPUTE. |

> [!NOTE]
> Po włączeniu metryk w ustawieniu diagnostycznym informacje o wymiarach nie są obecnie uwzględniane jako część informacji wysyłanych do konta magazynu, centrum zdarzeń lub usługi log Analytics.

## <a name="analyzing-log-data"></a>Analizowanie danych dziennika

Użycie Log Analytics Azure Monitor wymaga utworzenia konfiguracji diagnostycznej i włączenia __informacji wysyłanych do log Analytics__. Aby uzyskać więcej informacji, zobacz sekcję [Konfiguracja](#configuration) .

Dane w dziennikach Azure Monitor są przechowywane w tabelach, w których każda tabela ma swój własny zestaw unikatowych właściwości. Azure Machine Learning przechowuje dane w następujących tabelach:

| Tabela | Opis |
|:---|:---|
| AmlComputeClusterEvent | Zdarzenia z Azure Machine Learning klastrów obliczeniowych. |
| AmlComputeClusterNodeEvent | Zdarzenia z węzłów w klastrze obliczeniowym Azure Machine Learning. |
| AmlComputeJobEvent | Zdarzenia z zadań uruchomionych na Azure Machine Learning COMPUTE. |

> [!IMPORTANT]
> Po wybraniu opcji **dzienniki** z menu Azure Machine Learning, log Analytics zostanie otwarty z zakresem zapytania ustawionym na bieżący obszar roboczy. Oznacza to, że zapytania dziennika będą zawierać tylko dane z tego zasobu. Jeśli chcesz uruchomić zapytanie, które zawiera dane z innych baz danych lub danych z innych usług platformy Azure, wybierz pozycję **dzienniki** z menu **Azure monitor** . Aby uzyskać szczegółowe informacje [, zobacz zakres zapytań dzienników i zakres czasu w Azure Monitor Log Analytics](/azure/azure-monitor/log-query/scope/) .

Aby uzyskać szczegółowe informacje na temat dzienników i metryk, zobacz [Azure Machine Learning informacje o monitorowaniu danych](monitor-resource-reference.md).

### <a name="sample-queries"></a>Przykładowe zapytania

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

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać informacje na temat informacji o dziennikach i metrykach, zobacz [Azure Machine Learning informacje dotyczące monitorowania](monitor-resource-reference.md).
- Aby uzyskać informacje na temat pracy z przydziałami związanymi z Azure Machine Learning, zobacz [Zarządzanie przydziałami zasobów platformy Azure i ich żądania](how-to-manage-quotas.md).
- Aby uzyskać szczegółowe informacje na temat monitorowania zasobów platformy Azure, zobacz [monitorowanie zasobów platformy Azure za pomocą Azure monitor](/azure/azure-monitor/insights/monitor-azure-resource).
