---
title: Informacje o monitorowaniu danych | Microsoft Docs
titleSuffix: Azure Machine Learning
description: Dowiedz się więcej na temat danych i zasobów zebranych dla Azure Machine Learning i dostępnych w Azure Monitor. Azure Monitor zbiera i przedstawia dane dotyczące obszaru roboczego Azure Machine Learning, a następnie pozwala wyświetlać metryki, ustawiać alerty i analizować zarejestrowane dane.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.reviewer: larryfr
ms.author: aashishb
author: aashishb
ms.date: 04/27/2020
ms.openlocfilehash: 405b0aa051d0d1142d7dd4ccbf2bca4ef9cc3545
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89650596"
---
# <a name="azure-machine-learning-monitoring-data-reference"></a>Informacje o monitorowaniu usługi Azure Machine Learning

Dowiedz się więcej o danych i zasobach zbieranych przez Azure Monitor z obszaru roboczego Azure Machine Learning. Aby uzyskać szczegółowe informacje na temat zbierania i analizowania danych monitorowania, zobacz [Azure Machine Learning monitorowania](monitor-azure-machine-learning.md) .

## <a name="resource-logs"></a>Dzienniki zasobów

W poniższej tabeli wymieniono właściwości dzienników zasobów Azure Machine Learning, które są zbierane w Azure Monitor dziennikach lub usłudze Azure Storage.

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
| Typ zdarzenia | Typ zdarzenia zadania, np., JobSubmitted, JobRunning, JobFailed, JobSucceeded itd. |
| ExecutionState | Stan zadania (przebieg), np., w kolejce, uruchomione, zakończone powodzeniem, zakończone niepowodzeniem |
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
| Publisher | Wydawca obrazu maszyny wirtualnej, np. Microsoft-dsvm |
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

### <a name="metrics"></a>Metryki

W poniższych tabelach przedstawiono metryki platformy zebrane dla Azure Machine Learning wszystkie metryki są przechowywane w **obszar roboczy usługi Azure Machine Learning**przestrzeni nazw.

**Model**

| Metryka | Jednostka | Opis |
| ----- | ----- | ----- |
| Nie można wdrożyć modelu | Liczba | Liczba wdrożeń modelu zakończonych niepowodzeniem. |
| Rozpoczęto Wdrażanie modelu | Liczba | Liczba uruchomionych wdrożeń modelu. |
| Wdrażanie modelu powiodło się | Liczba | Liczba wdrożeń modelu, które zakończyły się powodzeniem. |
| Nie można zarejestrować modelu | Liczba | Liczba rejestracji modelu zakończonych niepowodzeniem. |
| Rejestrowanie modelu powiodło się | Liczba | Liczba rejestracji modelu zakończonych powodzeniem. |

**limit przydziału**

Informacje o limicie przydziału są przeznaczone tylko dla Azure Machine Learning obliczeń.

| Metryka | Jednostka | Opis |
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

Poniżej znajdują się wymiary, których można użyć do filtrowania metryk przydziału:

| Wymiar | Metryki dostępne z | Opis |
| ---- | ---- | ---- |
| Nazwa klastra | Wszystkie metryki przydziału | Nazwa wystąpienia obliczeniowego. |
| Nazwa rodziny maszyn wirtualnych | Procent wykorzystania przydziałów | Nazwa rodziny maszyn wirtualnych używanej przez klaster. |
| Priorytet maszyny wirtualnej | Procent wykorzystania przydziałów | Priorytet maszyny wirtualnej.

**Zasób**

| Metryka | Jednostka | Opis |
| ----- | ----- | ----- |
| CpuUtilization | Procent | Procent użycia procesora CPU dla danego węzła podczas uruchamiania/zadania. Ta Metryka jest publikowana tylko wtedy, gdy zadanie jest uruchomione w węźle. Jedno zadanie może korzystać z co najmniej jednego węzła. Ta Metryka jest publikowana na węzeł. |
| GpuUtilization | Procent | Stopień użycia procesora GPU dla danego węzła podczas uruchamiania/zadania. Jeden węzeł może mieć jeden lub więcej procesorów GPU. Ta Metryka jest publikowana na procesor GPU na węzeł. |

Poniżej znajdują się wymiary, których można użyć do filtrowania metryk zasobów:

| Wymiar | Opis |
| ----- | ----- |
| CreatedTime | |
| DeviceId | Identyfikator urządzenia (GPU). Dostępne tylko dla GpuUtilization. |
| NodeId | IDENTYFIKATOR węzła utworzonego w przypadku, gdy zadanie jest uruchomione. |
| RunId | Identyfikator uruchomienia/zadania. |

**Uruchom**

Informacje o przebiegach szkoleniowych.

| Metryka | Jednostka | Opis |
| ----- | ----- | ----- |
| Ukończone uruchomienia | Liczba | Liczba ukończonych uruchomień. |
| Nieudane uruchomienia | Liczba | Liczba nieudanych uruchomień. |
| Uruchomione uruchomienia | Liczba | Liczba rozpoczętych uruchomień. |

Poniżej znajdują się wymiary, których można użyć do filtrowania metryk przebiegu:

| Wymiar | Opis |
| ---- | ---- |
| Computetype | Typ obliczeń używany do uruchomienia. |
| PipelineStepType | Typ [PipelineStep](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinestep?view=azure-ml-py&preserve-view=true) używany w przebiegu. |
| PublishedPipelineId | Identyfikator opublikowanego potoku używanego w przebiegu. |
| RunType | Typ uruchomienia. |

Prawidłowe wartości dla wymiaru RunType są następujące:

| Wartość | Opis |
| ----- | ----- |
| Experiment | Uruchomienia bez potoku. |
| PipelineRun | Uruchomienie potoku, które jest elementem nadrzędnym elementu StepRun. |
| StepRun | Przebieg kroku potoku. |
| ReusedStepRun | Przebieg kroku potoku, który ponownie używa poprzedniego przebiegu. |

## <a name="see-also"></a>Zobacz też

- Aby uzyskać opis Azure Machine Learning monitorowania, zobacz [Azure Machine Learning monitorowania](monitor-azure-machine-learning.md) .
- Aby uzyskać szczegółowe informacje na temat monitorowania zasobów platformy Azure, zobacz [monitorowanie zasobów platformy Azure za pomocą Azure monitor](/azure/azure-monitor/insights/monitor-azure-resource) .
