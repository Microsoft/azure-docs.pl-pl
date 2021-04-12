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
ms.date: 04/07/2021
ms.openlocfilehash: de4d934144d6721db8c00d7199061842e518e44f
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/07/2021
ms.locfileid: "107031073"
---
# <a name="monitoring-azure-machine-learning-data-reference"></a>Monitorowanie odwołań do danych usługi Azure Machine Learning

Dowiedz się więcej o danych i zasobach zbieranych przez Azure Monitor z obszaru roboczego Azure Machine Learning. Aby uzyskać szczegółowe informacje na temat zbierania i analizowania danych monitorowania, zobacz [Azure Machine Learning monitorowania](monitor-azure-machine-learning.md) .

## <a name="metrics"></a>Metryki

W tej sekcji wymieniono wszystkie automatycznie zbierane metryki platformy zebrane dla Azure Machine Learning. Dostawca zasobów dla tych metryk to [Microsoft. MachineLearningServices/Workspaces](../azure-monitor/essentials/metrics-supported.md#microsoftmachinelearningservicesworkspaces).

**Model**

| Metric | Jednostka | Opis |
|--|--|--|
| Rejestrowanie modelu powiodło się | Liczba | Liczba rejestracji modelu zakończonych powodzeniem w tym obszarze roboczym |
| Nie można zarejestrować modelu | Liczba | Liczba rejestracji modelu zakończonych niepowodzeniem w tym obszarze roboczym |
| Uruchomiono Wdrażanie modelu | Liczba | Liczba rozpoczętych wdrożeń modelu w tym obszarze roboczym |
| Wdrażanie modelu powiodło się | Liczba | Liczba wdrożeń modelu, które zakończyły się pomyślnie w tym obszarze roboczym |
| Wdrażanie modelu nie powiodło się | Liczba | Liczba wdrożeń modelu, które zakończyły się niepowodzeniem w tym obszarze roboczym |

**limit przydziału**

Informacje o limicie przydziału są przeznaczone tylko dla Azure Machine Learning obliczeń.

| Metric | Jednostka | Opis |
|--|--|--|
| Łączna liczba węzłów | Liczba | Łączna liczba węzłów. Ta suma obejmuje niektóre aktywne węzły, bezczynne węzły, węzły niezdatne do użytku, węzły zastępujące, pozostawiając węzły |
| Aktywne węzły | Liczba | Liczba aktywnych węzłów. Węzły, w których aktywnie uruchomiono zadanie. |
| Węzły bezczynne | Liczba | Liczba bezczynnych węzłów. Węzły bezczynne są węzłami, w których nie są uruchomione żadne zadania, ale mogą akceptować nowe zadanie, jeśli jest dostępne. |
| Węzły niezdatne do użytku | Liczba | Liczba nieużywanych węzłów. Węzły, które nie są użyteczne, nie działają z powodu problemu z nierozpoznawalne. Platforma Azure przeprowadzi odtwarzanie tych węzłów. |
| Zastępujące węzły | Liczba | Liczba przeniesiona węzłów. Te węzły są węzłami o niskim priorytecie, które znajdują się poza dostępną pulą węzłów. |
| Opuszczanie węzłów | Liczba | Liczba pozostałych węzłów. Opuszczenie węzłów to węzły, które po prostu zakończyły przetwarzanie zadania i przechodzą w stan bezczynności. |
| Łączna liczba rdzeni | Liczba | Łączna liczba rdzeni |
| Aktywne rdzenie | Liczba | Liczba aktywnych rdzeni |
| Rdzenie bezczynne | Liczba | Liczba rdzeni bezczynnych |
| Rdzenie, których nie można używać | Liczba | Liczba rdzeni, których nie można używać |
| Występujące rdzenie | Liczba | Liczba przeniesiona rdzeni |
| Opuszczanie rdzeni | Liczba | Liczba rdzeni wychodzących |
| Procent wykorzystania przydziałów | Liczba | Procent wykorzystania przydziałów |

**Zasób**

| Metric| Jednostka | Opis |
|--|--|--|
| CpuUtilization | Liczba | Procent użycia w węźle procesora CPU. Wykorzystanie jest raportowane w odstępach czasu jednej minuty. |
| GpuUtilization | Liczba | Procent użycia w węźle procesora GPU. Wykorzystanie jest raportowane w odstępach czasu jednej minuty. |
| GpuMemoryUtilization | Liczba | Procent użycia pamięci w węźle procesora GPU. Wykorzystanie jest raportowane w odstępach czasu jednej minuty. |
| GpuEnergyJoules | Liczba | Energia interwału w Joules w węźle procesora GPU. Energia jest raportowana w odstępach czasu jednej minuty. |

**Uruchom**

Informacje na temat szkoleń dla obszaru roboczego.

| Metric | Jednostka | Opis |
|--|--|--|
| Anulowane przebiegi | Liczba | Liczba anulowanych przebiegów dla tego obszaru roboczego. Licznik jest aktualizowany w przypadku pomyślnego anulowania przebiegu. |
| Anulowanie żądanych przebiegów | Liczba | Liczba uruchomień, dla których zażądano anulowania dla tego obszaru roboczego. Licznik jest aktualizowany po odebraniu żądania anulowania dla uruchomienia. |
| Ukończone uruchomienia | Liczba | Liczba przebiegów pomyślnie ukończonych dla tego obszaru roboczego. Licznik jest aktualizowany po zakończeniu przebiegu i zebraniu danych wyjściowych. |
| Nieudane uruchomienia | Liczba | Liczba uruchomień dla tego obszaru roboczego nie powiodła się. Licznik jest aktualizowany w przypadku niepowodzenia przebiegu. |
| Finalizowanie przebiegów | Liczba | Liczba przebiegów w trakcie finalizowania stanu dla tego obszaru roboczego. Licznik jest aktualizowany, gdy przebieg został ukończony, ale kolekcja wyjściowa jest nadal w toku. | 
| Przebiegi nie odpowiadają | Liczba | Liczba przebiegów, które nie odpowiadają w tym obszarze roboczym. Licznik jest aktualizowany, gdy przebieg nie odpowiada. |
| Uruchomienia nieuruchomione | Liczba | Liczba przebiegów w stanie nieuruchomionym dla tego obszaru roboczego. Licznik jest aktualizowany, gdy zostanie odebrane żądanie utworzenia przebiegu, ale informacje o uruchomieniu nie zostały jeszcze wypełnione. |
| Przygotowywanie przebiegów | Liczba | Liczba przebiegów przygotowywania dla tego obszaru roboczego. Licznik jest aktualizowany, gdy przebieg przechodzi do stanu przygotowywania podczas przygotowywania środowiska uruchomieniowego. |
| Uruchomienia aprowizacji | Liczba | Liczba uruchomień, które są inicjowane dla tego obszaru roboczego. Licznik jest aktualizowany, gdy przebieg oczekuje na utworzenie obiektu docelowego obliczeń lub inicjowanie obsługi. |
| Uruchomienia w kolejce | Liczba | Liczba uruchomień umieszczonych w kolejce dla tego obszaru roboczego. Licznik jest aktualizowany, gdy przebieg zostanie umieszczony w kolejce w celu obliczenia. Może wystąpić podczas oczekiwania na gotowość wymaganych węzłów obliczeniowych. |
| Uruchomione uruchomienia | Liczba | Liczba przebiegów uruchomionych dla tego obszaru roboczego. Licznik jest aktualizowany po rozpoczęciu uruchamiania w wymaganych zasobach. |
| Uruchamianie przebiegów | Liczba | Liczba rozpoczętych uruchomień dla tego obszaru roboczego. Licznik jest aktualizowany po zapisaniu żądania utworzenia i uruchomienia informacji, takich jak identyfikator przebiegu |
| błędy | Liczba | Liczba błędów uruchomienia w tym obszarze roboczym. Liczba jest aktualizowana za każdym razem, gdy wystąpi błąd. |
| Ostrzeżenia | Liczba | Liczba ostrzeżeń uruchamiania w tym obszarze roboczym. Liczba jest aktualizowana za każdym razem, gdy uruchomienie napotka ostrzeżenie. |

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
