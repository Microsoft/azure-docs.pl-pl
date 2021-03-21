---
title: Informacje o monitorowaniu Azure Machine Learning danych | Microsoft Docs
description: Dokumentacja dotycząca monitorowania Azure Machine Learning. Dowiedz się więcej o zasobach & zbieranych i dostępnych w Azure Monitor.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.reviewer: larryfr
ms.author: aashishb
author: aashishb
ms.custom: subject-monitoring
ms.date: 10/02/2020
ms.openlocfilehash: f130fc0c65c49c33c838812fc2758619e0d1bca0
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102521343"
---
# <a name="monitoring-azure-machine-learning-data-reference"></a>Monitorowanie odwołań do danych usługi Azure Machine Learning

Dowiedz się więcej o danych i zasobach zbieranych przez Azure Monitor z obszaru roboczego Azure Machine Learning. Aby uzyskać szczegółowe informacje na temat zbierania i analizowania danych monitorowania, zobacz [Azure Machine Learning monitorowania](monitor-azure-machine-learning.md) .

## <a name="metrics"></a>Metryki

W tej sekcji wymieniono wszystkie automatycznie zbierane metryki platformy zebrane dla Azure Machine Learning. Dostawca zasobów dla tych metryk to [Microsoft. MachineLearningServices/Workspaces](../azure-monitor/essentials/metrics-supported.md#microsoftmachinelearningservicesworkspaces).

**Model**

| Metric | Jednostka | Opis |
| ----- | ----- | ----- |
| Nie można wdrożyć modelu | Liczba | Liczba wdrożeń modelu zakończonych niepowodzeniem. |
| Rozpoczęto Wdrażanie modelu | Liczba | Liczba uruchomionych wdrożeń modelu. |
| Wdrażanie modelu powiodło się | Liczba | Liczba wdrożeń modelu, które zakończyły się powodzeniem. |
| Nie można zarejestrować modelu | Liczba | Liczba rejestracji modelu zakończonych niepowodzeniem. |
| Rejestrowanie modelu powiodło się | Liczba | Liczba rejestracji modelu zakończonych powodzeniem. |

**limit przydziału**

Informacje o limicie przydziału są przeznaczone tylko dla Azure Machine Learning obliczeń.

| Metric | Jednostka | Opis |
| ----- | ----- | ----- |
| Aktywne rdzenie | Liczba | Liczba aktywnych rdzeni obliczeniowych. |
| Aktywne węzły | Liczba | Liczba aktywnych węzłów. |
| Rdzenie bezczynne | Liczba | Liczba rdzeni obliczeniowych w bezczynnym stanie. |
| Węzły bezczynne | Liczba | Liczba bezczynnych węzłów obliczeniowych. |
| Opuszczanie rdzeni | Liczba | Liczba rdzeni opuszczających. |
| Opuszczanie węzłów | Liczba | Liczba pozostałych węzłów. |
| Występujące rdzenie | Liczba | Liczba przeniesiona rdzeni. |
| Zastępujące węzły | Liczba | Liczba przeniesiona węzłów. |
| Procent wykorzystania przydziałów | Procent | Procent użytego przydziału. |
| Całkowita liczba rdzeni | Liczba | Łączna liczba rdzeni. |
| Łączna liczba węzłów | Liczba | Łączna liczba węzłów. |
| Rdzenie, których nie można używać | Liczba | Liczba rdzeni, których nie można używać. |
| Węzły niezdatne do użytku | Liczba | Liczba węzłów, które nie są używane. |

**Zasób**

| Metric | Jednostka | Opis |
| ----- | ----- | ----- |
| CpuUtilization | Procent | Procent użycia procesora CPU dla danego węzła podczas uruchamiania/zadania. Ta Metryka jest publikowana tylko wtedy, gdy zadanie jest uruchomione w węźle. Jedno zadanie może korzystać z co najmniej jednego węzła. Ta Metryka jest publikowana na węzeł. |
| GpuUtilization | Procent | Stopień użycia procesora GPU dla danego węzła podczas uruchamiania/zadania. Jeden węzeł może mieć jeden lub więcej procesorów GPU. Ta Metryka jest publikowana na procesor GPU na węzeł. |

**Uruchom**

Informacje o przebiegach szkoleniowych.

| Metric | Jednostka | Opis |
| ----- | ----- | ----- |
| Ukończone uruchomienia | Liczba | Liczba ukończonych uruchomień. |
| Nieudane uruchomienia | Liczba | Liczba nieudanych uruchomień. |
| Uruchomione uruchomienia | Liczba | Liczba rozpoczętych uruchomień. |

## <a name="metric-dimensions"></a>Wymiary metryk

Aby uzyskać więcej informacji na temat wymiarów metryk, zobacz [wielowymiarowe metryki](../azure-monitor/essentials/data-platform-metrics.md#multi-dimensional-metrics).

Azure Machine Learning ma powiązane z nimi wymiary.

| Wymiar | Opis |
| ---- | ---- |
| Nazwa klastra | Nazwa zasobu klastra obliczeniowego. Dostępne dla wszystkich metryk przydziału. |
| Nazwa rodziny maszyn wirtualnych | Nazwa rodziny maszyn wirtualnych używanej przez klaster. Dostępne dla procentu wykorzystania przydziałów. |
| Priorytet maszyny wirtualnej | Priorytet maszyny wirtualnej. Dostępne dla procentu wykorzystania przydziałów.
| CreatedTime | Dostępne tylko dla CpuUtilization i GpuUtilization. |
| DeviceId | Identyfikator urządzenia (GPU). Dostępne tylko dla GpuUtilization. |
| NodeId | IDENTYFIKATOR węzła utworzonego w przypadku, gdy zadanie jest uruchomione. Dostępne tylko dla CpuUtilization i GpuUtilization. |
| RunId | Identyfikator uruchomienia/zadania. Dostępne tylko dla CpuUtilization i GpuUtilization. |
| Computetype | Typ obliczeń używany do uruchomienia. Dostępne tylko dla ukończonych uruchomień, nieudanych uruchomień i rozpoczętych uruchomień. |
| PipelineStepType | Typ [PipelineStep](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinestep) używany w przebiegu. Dostępne tylko dla ukończonych uruchomień, nieudanych uruchomień i rozpoczętych uruchomień. |
| PublishedPipelineId | Identyfikator opublikowanego potoku używanego w przebiegu. Dostępne tylko dla ukończonych uruchomień, nieudanych uruchomień i rozpoczętych uruchomień. |
| RunType | Typ uruchomienia. Dostępne tylko dla ukończonych uruchomień, nieudanych uruchomień i rozpoczętych uruchomień. |

Prawidłowe wartości dla wymiaru RunType są następujące:

| Wartość | Opis |
| ----- | ----- |
| Experiment | Uruchomienia bez potoku. |
| PipelineRun | Uruchomienie potoku, które jest elementem nadrzędnym elementu StepRun. |
| StepRun | Przebieg kroku potoku. |
| ReusedStepRun | Przebieg kroku potoku, który ponownie używa poprzedniego przebiegu. |

## <a name="activity-log"></a>Dziennik aktywności

Poniższa tabela zawiera listę operacji związanych z Azure Machine Learning, które mogą zostać utworzone w dzienniku aktywności.

| Operacja | Opis |
|:---|:---|
| Tworzy lub aktualizuje obszar roboczy Machine Learning | Utworzono lub Zaktualizowano obszar roboczy |
| CheckComputeNameAvailability | Sprawdź, czy nazwa obliczeniowa jest już używana |
| Tworzy lub aktualizuje zasoby obliczeniowe | Zasób obliczeniowy został utworzony lub zaktualizowany |
| Usuwa zasoby obliczeniowe | Zasób obliczeniowy został usunięty |
| Wyświetlanie listy wpisów tajnych | Na liście tajnych operacji dla Machine Learning obszaru roboczego |

## <a name="resource-logs"></a>Dzienniki zasobów

Ta sekcja zawiera listę typów dzienników zasobów, które można zbierać dla Azure Machine Learning obszaru roboczego.

Dostawca zasobów i typ: [Microsoft. MachineLearningServices/Workspace](../azure-monitor/essentials/resource-logs-categories.md#microsoftmachinelearningservicesworkspaces).

| Kategoria | Nazwa wyświetlana |
| ----- | ----- |
| AmlComputeClusterEvent | AmlComputeClusterEvent |
| AmlComputeClusterNodeEvent | AmlComputeClusterNodeEvent |
| AmlComputeCpuGpuUtilization | AmlComputeCpuGpuUtilization |
| AmlComputeJobEvent | AmlComputeJobEvent |
| AmlRunStatusChangedEvent | AmlRunStatusChangedEvent |

## <a name="schemas"></a>Schematy

Następujące schematy są używane przez Azure Machine Learning

### <a name="amlcomputejobevents-table"></a>Tabela AmlComputeJobEvents

| Właściwość | Opis |
|:--- |:---|
| TimeGenerated | Godzina wygenerowania wpisu dziennika |
| OperationName | Nazwa operacji skojarzonej ze zdarzeniem dziennika |
| Kategoria | Nazwa zdarzenia dziennika, AmlComputeClusterNodeEvent |
| JobId | Identyfikator przesłanego zadania |
| ExperimentId | Identyfikator eksperymentu |
| Eksperymentname | Nazwa eksperymentu |
| CustomerSubscriptionId | Identyfikator subskrypcji, w której eksperyment i zadanie zostały przesłane |
| WorkspaceName | Nazwa obszaru roboczego uczenia maszynowego |
| NazwaKlastra | Nazwa klastra |
| ProvisioningState | Stan przesłania zadania |
| ResourceGroupName | Nazwa grupy zasobów |
| JobName | Nazwa zadania |
| ClusterId | Identyfikator klastra |
| Typ zdarzenia | Typ zdarzenia zadania. Na przykład JobSubmitted, JobRunning, JobFailed, JobSucceeded. |
| ExecutionState | Stan zadania (przebieg). Na przykład w kolejce, uruchomione, zakończone powodzeniem, zakończone niepowodzeniem |
| ErrorDetails | Szczegóły błędu zadania |
| CreationApiVersion | Wersja interfejsu API użyta do utworzenia zadania |
| ClusterResourceGroupName | Nazwa grupy zasobów klastra |
| TFWorkerCount | Liczba procesów roboczych TF |
| TFParameterServerCount | Liczba serwerów parametrów TF |
| — Narzędzie | Typ używanego narzędzia |
| RunInContainer | Flaga opisująca, czy zadanie powinno być uruchamiane wewnątrz kontenera |
| JobErrorMessage | szczegółowy komunikat o błędzie zadania |
| NodeId | IDENTYFIKATOR węzła utworzonego w przypadku, gdy zadanie jest uruchomione |

### <a name="amlcomputeclusterevents-table"></a>Tabela AmlComputeClusterEvents

| Właściwość | Opis |
|:--- |:--- |
| TimeGenerated | Godzina wygenerowania wpisu dziennika |
| OperationName | Nazwa operacji skojarzonej ze zdarzeniem dziennika |
| Kategoria | Nazwa zdarzenia dziennika, AmlComputeClusterNodeEvent |
| ProvisioningState | Stan aprowizacji klastra |
| NazwaKlastra | Nazwa klastra |
| Clustertype | Typ klastra |
| CreatedBy | Użytkownik, który utworzył klaster |
| CoreCount | Liczba rdzeni w klastrze |
| VmSize | Rozmiar maszyny wirtualnej klastra |
| VmPriority | Priorytet węzłów utworzonych w klastrze dedykowanym/LowPriority |
| Skalowanie | Typ ręcznego skalowania klastra/automatycznego |
| InitialNodeCount | Początkowa liczba węzłów klastra |
| MinimumNodeCount | Minimalna liczba węzłów klastra |
| MaximumNodeCount | Maksymalna liczba węzłów klastra |
| NodeDeallocationOption | Jak ma zostać cofnięty przydział węzła |
| Publisher | Wydawca typu klastra |
| Oferta | Oferta, w której tworzony jest klaster |
| SKU | Jednostka SKU utworzonego węzła/maszyny wirtualnej w klastrze |
| Wersja | Wersja obrazu używana podczas tworzenia węzła/maszyny wirtualnej |
| SubnetId | SubnetId klastra |
| AllocationState | Stan alokacji klastra |
| CurrentNodeCount | Bieżąca liczba węzłów klastra |
| TargetNodeCount | Liczba węzłów docelowych klastra podczas skalowania w górę/w dół |
| Typ zdarzenia | Typ zdarzenia podczas tworzenia klastra. |
| NodeIdleTimeSecondsBeforeScaleDown | Czas bezczynności (w sekundach), po którym klaster będzie skalowany w dół |
| PreemptedNodeCount | Liczba przeniesiona węzłów klastra |
| IsResizeGrow | Flaga oznaczająca, że klaster jest skalowany w górę |
| VmFamilyName | Nazwa rodziny maszyn wirtualnych węzłów, które mogą zostać utworzone w klastrze |
| LeavingNodeCount | Pozostawienie liczby węzłów klastra |
| UnusableNodeCount | Niezdatna do użycia liczba węzłów klastra |
| IdleNodeCount | Liczba węzłów bezczynności klastra |
| RunningNodeCount | Liczba uruchomionych węzłów klastra |
| PreparingNodeCount | Przygotowywanie liczby węzłów klastra |
| QuotaAllocated | Przydzielono przydział do klastra |
| QuotaUtilized | Wykorzystany limit przydziału klastra |
| AllocationStateTransitionTime | Czas przejścia z jednego stanu do innego |
| ClusterErrorCodes | Kod błędu otrzymany podczas tworzenia lub skalowania klastra |
| CreationApiVersion | Wersja interfejsu API użyta podczas tworzenia klastra |

### <a name="amlcomputeclusternodeevents-table"></a>Tabela AmlComputeClusterNodeEvents

| Właściwość | Opis |
|:--- |:--- |
| TimeGenerated | Godzina wygenerowania wpisu dziennika |
| OperationName | Nazwa operacji skojarzonej ze zdarzeniem dziennika |
| Kategoria | Nazwa zdarzenia dziennika, AmlComputeClusterNodeEvent |
| NazwaKlastra | Nazwa klastra |
| NodeId | Identyfikator utworzonego węzła klastra |
| VmSize | Rozmiar maszyny wirtualnej węzła |
| VmFamilyName | Rodzina maszyn wirtualnych, do której należy węzeł |
| VmPriority | Priorytet utworzonego węzła/LowPriority |
| Publisher | Wydawca obrazu maszyny wirtualnej. Na przykład Microsoft-dsvm |
| Oferta | Oferta skojarzona z tworzeniem maszyny wirtualnej |
| SKU | Utworzono jednostkę SKU węzła/maszyny wirtualnej |
| Wersja | Wersja obrazu używana podczas tworzenia węzła/maszyny wirtualnej |
| ClusterCreationTime | Czas utworzenia klastra |
| ResizeStartTime | Czas rozpoczęcia skalowania klastra w górę/w dół |
| ResizeEndTime | Czas zakończenia skalowania klastra w górę/w dół |
| NodeAllocationTime | Czas przydzielenia węzła |
| NodeBootTime | Czas uruchomienia węzła |
| StartTaskStartTime | Godzina, o której zadanie zostało przypisane do węzła i uruchomione |
| StartTaskEndTime | Czas zakończenia zadania przypisanego do węzła |
| TotalE2ETimeInSeconds | Łączny węzeł czasu był aktywny |


## <a name="see-also"></a>Zobacz też

- Aby uzyskać opis Azure Machine Learning monitorowania, zobacz [Azure Machine Learning monitorowania](monitor-azure-machine-learning.md) .
- Aby uzyskać szczegółowe informacje na temat monitorowania zasobów platformy Azure, zobacz [monitorowanie zasobów platformy Azure za pomocą Azure monitor](../azure-monitor/essentials/monitor-azure-resource.md) .
