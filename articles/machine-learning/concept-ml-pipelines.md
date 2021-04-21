---
title: Co to są potoki uczenia maszynowego?
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak potoki uczenia maszynowego ułatwiają tworzenie i optymalizowanie przepływów pracy uczenia maszynowego oraz zarządzanie nimi.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: laobri
author: lobrien
ms.date: 02/26/2021
ms.custom: devx-track-python
ms.openlocfilehash: 57f5da06909436e0cbce92559c29c309ca9e20e3
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107819236"
---
# <a name="what-are-azure-machine-learning-pipelines"></a>Co to są Azure Machine Learning potoki?

Z tego artykułu dowiesz się, jak potok uczenia maszynowego ułatwia tworzenie i optymalizowanie przepływu pracy uczenia maszynowego oraz zarządzanie nimi. 

<a name="compare"></a>
## <a name="which-azure-pipeline-technology-should-i-use"></a>Której technologii potoku platformy Azure należy użyć?

Chmura platformy Azure udostępnia kilka typów potoków, z których każdy ma inne przeznaczenie. W poniższej tabeli wymieniono różne potoki i elementy, do których są używane:

| Scenariusz | Osoba podstawowa | Oferta platformy Azure | Oferta systemu operacyjnego | Kanoniczny potok | Mocnych | 
| -------- | --------------- | -------------- | ------------ | -------------- | --------- | 
| Aranżacja modelu (uczenie maszynowe) | Analityk danych | Azure Machine Learning potoków | Potoki Kubeflow | Model danych > danych | Dystrybucja, buforowanie, najpierw kod, ponowne użycie | 
| Aranżacja danych (data prep) | Inżynier danych | [Potoki usługi Azure Data Factory](../data-factory/concepts-pipelines-activities.md) | Apache Airflow | Dane — > danych | Silnie typowane ruchy, działania zorientowane na dane |
| Organizowanie & kodu (CI/CD) | Deweloper aplikacji/operacyjna | [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/) | Jenkins | Kod i model -> App/Service | Najbardziej otwarta i elastyczna obsługa działań, kolejki zatwierdzania, fazy z obsługą gating | 

## <a name="what-can-machine-learning-pipelines-do"></a>Do czego mogą używać potoki uczenia maszynowego?

Potok Azure Machine Learning to niezależnie wykonywalny przepływ pracy kompletnego zadania uczenia maszynowego. Podzadania są zamknięte jako serie kroków w potoku. Potok Azure Machine Learning może być tak prosty, jak ten, który wywołuje skrypt języka Python, więc może _robić_ tylko cokolwiek. Potoki _powinny koncentrować_ się na zadaniach uczenia maszynowego, takich jak:

+ Przygotowywanie danych, takie jak importowanie, weryfikowanie i czyszczenie, zniekształcanie i przekształcanie, normalizacja i przemieszczanie
+ Konfigurowanie trenowania obejmujące parametryzację argumentów, ścieżki plików i konfiguracje rejestrowania/raportowania
+ Trenowanie i sprawdzania poprawności w sposób wydajny i powtarzany. Wydajność może pochodzić z określania określonych podzestawów danych, różnych sprzętowych zasobów obliczeniowych, przetwarzania rozproszonego i monitorowania postępu
+ Wdrażanie, w tym przechowywanie wersji, skalowanie, aprowizowanie i kontrola dostępu

Niezależne kroki umożliwiają wielu badaczom danych pracę w tym samym potoku w tym samym czasie bez konieczności nawłacania zasobów obliczeniowych. Oddzielne kroki ułatwiają również korzystanie z różnych typów/rozmiarów obliczeniowych dla każdego kroku.

Po zaprojektowaniu potoku często występuje bardziej precyzyjne dostrajanie pętli trenowania potoku. Po uruchomieniu potoku ponownie przebieg przechodzi do kroków, które należy ponownie uruchomić, takich jak zaktualizowany skrypt trenowania. Kroki, które nie muszą być ponownie uruchomić, są pomijane. 

W przypadku potoków można użyć innego sprzętu do różnych zadań. Platforma Azure koordynuje [różne](concept-azure-machine-learning-architecture.md) docelowe obiekty obliczeniowe, których używasz, aby dane pośrednie bezproblemowo przepływały do docelowych obiektów obliczeniowych.

Metryki [dla eksperymentów potoku można](./how-to-log-view-metrics.md) śledzić bezpośrednio w oknie Azure Portal lub na stronie docelowej obszaru [roboczego (wersja zapoznawcza).](https://ml.azure.com) Po opublikowaniu potoku można skonfigurować punkt końcowy REST, który umożliwia ponowne uruchomić potok z dowolnej platformy lub stosu.

Krótko mówiąc, potokom można pomóc we wszystkich złożonych zadaniach cyklu życia uczenia maszynowego. Inne technologie potoku platformy Azure mają swoje mocne strony. [Azure Data Factory potoków doskonale](../data-factory/concepts-pipelines-activities.md) radzi sobie z pracą z danymi, a [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/) to odpowiednie narzędzie do ciągłej integracji i ciągłego wdrażania. Jeśli jednak koncentrujesz się na uczeniu maszynowym, Azure Machine Learning potoki będą prawdopodobnie najlepszym wyborem na potrzeby przepływu pracy. 

### <a name="analyzing-dependencies"></a>Analizowanie zależności

Wiele ekosystemów programowania ma narzędzia, które orkiestrują zależności zasobów, biblioteki lub kompilacji. Ogólnie rzecz biorąc, te narzędzia używają sygnatur czasowych plików do obliczania zależności. Gdy plik zostanie zmieniony, tylko jego i jego zależności zostaną zaktualizowane (pobrane, ponownie skompilowane lub spakowane). Azure Machine Learning potoki rozszerzają tę koncepcję. Podobnie jak w przypadku tradycyjnych narzędzi do kompilacji, potoki obliczają zależności między krokami i wykonują tylko niezbędne ponowne obliczenia. 

Analiza zależności w potokach Azure Machine Learning jest jednak bardziej zaawansowana niż proste znaczniki czasu. Każdy krok może być uruchamiany w innym środowisku sprzętowym i programowym. Przygotowywanie danych może być procesem czasochłonnym, ale nie trzeba go uruchamiać na sprzęcie z wydajnymi procesorami GPU. Niektóre kroki mogą wymagać oprogramowania specyficznego dla systemu operacyjnego, użycia trenowania rozproszonego itd. 

Azure Machine Learning automatycznie aranżuje wszystkie zależności między krokami potoku. Ta aranżacja może obejmować obracanie obrazów platformy Docker w górę i w dół, dołączanie i odłączanie zasobów obliczeniowych oraz przenoszenie danych między krokami w spójny i automatyczny sposób.

### <a name="coordinating-the-steps-involved"></a>Koordynowanie zaangażowanych kroków

Podczas tworzenia i uruchamiania obiektu są uruchamiane następujące kroki `Pipeline` wysokiego poziomu:

+ Dla każdego kroku usługa oblicza wymagania dla:
    + Sprzętowe zasoby obliczeniowe
    + Zasoby systemu operacyjnego (obrazy platformy Docker)
    + Zasoby oprogramowania (zależności Conda/virtualenv)
    + Dane wejściowe 
+ Usługa określa zależności między krokami, co spowoduje dynamiczny wykres wykonywania
+ Po wykonaniu każdego węzła na grafie wykonywania:
    + Usługa konfiguruje niezbędne środowisko sprzętowe i programowe (być może ponownie przywłaszając istniejące zasoby)
    + Krok zostanie uruchomiony, dostarczając informacje dotyczące rejestrowania i monitorowania do jego obiektu `Experiment` zawierającego
    + Po zakończeniu kroku jego dane wyjściowe są przygotowywane jako dane wejściowe do następnego kroku i/lub zapisywane w magazynie
    + Zasoby, które nie są już potrzebne, są finalizowane i odłączane

![Kroki potoku](./media/concept-ml-pipelines/run_an_experiment_as_a_pipeline.png)

## <a name="building-pipelines-with-the-python-sdk"></a>Tworzenie potoków przy użyciu zestawu SDK języka Python

W zestawie [SDK Azure Machine Learning Python](/python/api/overview/azure/ml/install)potok jest obiektem języka Python zdefiniowanym w module `azureml.pipeline.core` . Obiekt [Pipeline](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline%28class%29) zawiera uporządkowaną sekwencję jednego lub większej liczby [obiektów PipelineStep.](/python/api/azureml-pipeline-core/azureml.pipeline.core.builder.pipelinestep) Klasa jest abstrakcyjna, a rzeczywiste kroki będą z podklas, takich jak `PipelineStep` [EstimatorStep,](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.estimatorstep) [PythonScriptStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.pythonscriptstep)lub [DataTransferStep.](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.datatransferstep) Klasa [ModuleStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.modulestep) zawiera sekwencję kroków wielokrotnego użytku, które mogą być współdzielone przez potoki. Element `Pipeline` jest uruchamiany w ramach . `Experiment`

Potok usługi Azure Machine Learning jest skojarzony z obszarem roboczym Azure Machine Learning, a krok potoku jest skojarzony z docelowym obiektem obliczeniowym dostępnym w tym obszarze roboczym. Aby uzyskać więcej informacji, zobacz [Create and manage Azure Machine Learning workspaces in the Azure Portal](./how-to-manage-workspace.md) (Co to są docelowe obiekty obliczeniowe w [Azure Machine Learning?).](./concept-compute-target.md)

### <a name="a-simple-python-pipeline"></a>Prosty potok języka Python

Ten fragment kodu przedstawia obiekty i wywołania wymagane do utworzenia i uruchomienia obiektu `Pipeline` :

```python
ws = Workspace.from_config() 
blob_store = Datastore(ws, "workspaceblobstore")
compute_target = ws.compute_targets["STANDARD_NC6"]
experiment = Experiment(ws, 'MyExperiment') 

input_data = Dataset.File.from_files(
    DataPath(datastore, '20newsgroups/20news.pkl'))
prepped_data_path = OutputFileDatasetConfig(name="output_path")

dataprep_step = PythonScriptStep(
    name="prep_data",
    script_name="dataprep.py",
    source_directory="prep_src",
    compute_target=compute_target,
    arguments=["--prepped_data_path", prepped_data_path],
    inputs=[input_dataset.as_named_input('raw_data').as_mount() ]
    )

prepped_data = prepped_data_path.read_delimited_files()

train_step = PythonScriptStep(
    name="train",
    script_name="train.py",
    compute_target=compute_target,
    arguments=["--prepped_data", prepped_data],
    source_directory="train_src"
)
steps = [ dataprep_step, train_step ]

pipeline = Pipeline(workspace=ws, steps=steps)

pipeline_run = experiment.submit(pipeline)
pipeline_run.wait_for_completion()
```

Fragment kodu rozpoczyna się od typowych Azure Machine Learning obiektów, obiektów `Workspace` , `Datastore` , elementu [ComputeTarget](/python/api/azureml-core/azureml.core.computetarget)i `Experiment` elementu . Następnie kod tworzy obiekty do przechowywania i `input_data` `prepped_data_path` . Element `input_data` jest wystąpieniem [zestawu FileDataset,](/python/api/azureml-core/azureml.data.filedataset) a element `prepped_data_path` jest wystąpieniem klasy  [OutputFileDatasetConfig](/python/api/azureml-core/azureml.data.output_dataset_config.outputfiledatasetconfig). Zachowaniem domyślnym jest skopiowanie danych wyjściowych do magazynu danych w ścieżce , gdzie jest identyfikatorem uruchomienia i jest wartością wygenerowaną automatycznie, jeśli nie zostanie określona przez `OutputFileDatasetConfig` `workspaceblobstore` `/dataset/{run-id}/{output-name}` `run-id` `output-name` dewelopera.

Kod przygotowywania danych (nie jest wyświetlany), zapisuje rozdzielane pliki w pliku `prepped_data_path` . Te dane wyjściowe z kroku przygotowywania danych są przekazywane jako `prepped_data` do kroku trenowania. 

Tablica `steps` zawiera dwa `PythonScriptStep` typy i `dataprep_step` `train_step` . Azure Machine Learning analizuje zależność danych i `prepped_data` uruchamia przed `dataprep_step` programem `train_step` . 

Następnie kod iniekuje sam obiekt, przekazując tablicę kroków i `Pipeline` obszaru roboczego. Wywołanie do rozpoczyna `experiment.submit(pipeline)` uruchomienie potoku usługi Azure ML. Wywołanie bloków `wait_for_completion()` do momentu zakończeniu potoku. 

Aby dowiedzieć się więcej na temat łączenia potoku z danymi, zobacz artykuły [Data access in Azure Machine Learning](concept-data.md) and Moving data into and between ML pipeline steps (Python) (Dostęp do danych w usługach Azure Machine Learning i Przenoszenie danych do i między krokami potoku uczenia [maszynowego (Python).](how-to-move-data-in-out-of-pipelines.md) 

## <a name="building-pipelines-with-the-designer"></a>Tworzenie potoków za pomocą projektanta

Deweloperzy, którzy preferują powierzchnię projektową wizualizacji, mogą używać Azure Machine Learning do tworzenia potoków. Dostęp do tego narzędzia można uzyskać z wyboru **Projektant** na stronie głównej obszaru roboczego.  Projektant umożliwia przeciąganie i upuszczanie kroków na powierzchnię projektową. 

Podczas wizualnego projektowania potoków dane wejściowe i wyjściowe kroku są widoczne. Możesz przeciągać i upuszczać połączenia danych, co pozwala szybko zrozumieć i zmodyfikować przepływ danych potoku.

![Azure Machine Learning przykład projektanta](./media/concept-designer/designer-drag-and-drop.gif)

## <a name="key-advantages"></a>Najważniejsze zalety

Kluczowe zalety korzystania z potoków dla przepływów pracy uczenia maszynowego to:

|Kluczowa zaleta|Opis|
|:-------:|-----------|
|**Przebiegi nienadzorowane &nbsp;**|Planowanie kroków uruchamianych równolegle lub w sekwencji w niezawodny i nienadzorowany sposób. Przygotowywanie i modelowanie danych może trwać kilka dni lub tygodni, a potoki umożliwiają skoncentrowanie się na innych zadaniach, gdy proces jest uruchomiony. |
|**Heterogeniczne obliczenia**|Używaj wielu potoków, które są niezawodnie koordynowane w heterogenicznych i skalowalnych zasobach obliczeniowych i lokalizacjach magazynu. Efektywne wykorzystanie dostępnych zasobów obliczeniowych dzięki uruchamianiu poszczególnych kroków potoku na różnych docelowych zasobach obliczeniowych, takich jak hdinsight, maszyny wirtualne nauki o danych z procesorem GPU i usługę Databricks.|
|**Ponowne użyteczność**|Tworzenie szablonów potoków dla określonych scenariuszy, takich jak ponowne trenowanie i ocenianie wsadowe. Wyzwalanie opublikowanych potoków z systemów zewnętrznych za pomocą prostych wywołań REST.|
|**Śledzenie i wersjonarowanie**|Zamiast ręcznie śledzić dane i ścieżki wyników podczas iteruj, użyj zestawu SDK potoków, aby jawnie nazwać źródła danych, dane wejściowe i dane wyjściowe oraz określić ich wersje. Możesz również zarządzać skryptami i danymi oddzielnie, aby zwiększyć produktywność.|
| **Modułowość** | Rozdzielenie obszarów problemów i izolowanie zmian umożliwia szybsze ewolucję oprogramowania o wyższej jakości. | 
|**Współpraca**|Potoki umożliwiają badaczom danych współpracę we wszystkich obszarach procesu projektowania uczenia maszynowego przy jednoczesnym umożliwieniu współbieżnej pracy nad krokami potoku.|

## <a name="next-steps"></a>Następne kroki

Azure Machine Learning potoki to zaawansowana możliwość, która rozpoczyna dostarczanie wartości na wczesnych etapach opracowywania. Wartość zwiększa się w miarę rozwoju zespołu i projektu. W tym artykule wyjaśniono, jak potoki są określane za pomocą zestawu SDK Azure Machine Learning Python i orkiestrowane na platformie Azure. Widzieliśmy prosty kod źródłowy i wprowadziliśmy go do kilku dostępnych `PipelineStep` klas. Warto wiedzieć, kiedy używać potoków Azure Machine Learning i jak są one uruchamiane na platformie Azure. 

+ Dowiedz się, [jak utworzyć pierwszy potok.](./how-to-create-machine-learning-pipelines.md)

+ Dowiedz się, [jak uruchamiać przewidywania wsadowe na dużych danych.](tutorial-pipeline-batch-scoring-classification.md )

+ Zobacz dokumenty referencyjne zestawu SDK dotyczące kroków [podstawowych potoków](/python/api/azureml-pipeline-core/) [i potoków.](/python/api/azureml-pipeline-steps/)

+ Wypróbuj przykładowe notesy Jupyter, w których Azure Machine Learning [potoki.](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines) Dowiedz się, [jak uruchamiać notesy, aby eksplorować tę usługę.](samples-notebooks.md)