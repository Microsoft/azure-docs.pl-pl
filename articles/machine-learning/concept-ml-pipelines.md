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
ms.openlocfilehash: 584e421b6beac0e4ecfab5b3e3cb735b8465e1b4
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102503525"
---
# <a name="what-are-azure-machine-learning-pipelines"></a>Co to są Azure Machine Learning potoki?

W tym artykule dowiesz się, jak potok uczenia maszynowego pomaga w tworzeniu i optymalizowaniu przepływu pracy uczenia maszynowego oraz zarządzania nim. 

<a name="compare"></a>
## <a name="which-azure-pipeline-technology-should-i-use"></a>Której technologii potoku platformy Azure należy używać?

Chmura systemu Azure oferuje kilka typów potoków, z których każdy ma inny cel. W poniższej tabeli wymieniono różne potoki i ich zastosowania:

| Scenariusz | Podstawowa osoba | Oferta platformy Azure | Oferta OSS | Rura kanoniczna | Zalety | 
| -------- | --------------- | -------------- | ------------ | -------------- | --------- | 
| Aranżacja modelu (Uczenie maszynowe) | Analityk danych | Potoki Azure Machine Learning | Potoki Kubeflow | Model > danych | Dystrybucja, buforowanie, kod — pierwsze, ponowne użycie | 
| Aranżacja danych (przygotowanie danych) | Inżynier danych | [Potoki usługi Azure Data Factory](../data-factory/concepts-pipelines-activities.md) | Przepływ dla oprogramowania Apache | Dane > danych | Silnie wpisana aktywność, działania zorientowane na dane |
| Aranżacja aplikacji kodu & (CI/CD) | Deweloper aplikacji/Ops | [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/) | Jenkins | Code + Model — > App/Service | Większość otwartych i elastycznych obsługi działań, kolejek zatwierdzania, faz z kontroli | 

## <a name="what-can-machine-learning-pipelines-do"></a>Co to są potoki uczenia maszynowego?

Potok Azure Machine Learning to niezależnie wykonywalny przepływ pracy kompletnego zadania uczenia maszynowego. Podzadania są zamknięte jako serie kroków w potoku. Potok Azure Machine Learning może być taki sam jak taki, który wywołuje skrypt w języku Python, więc _może_ dochodzić do wszystkiego. Potoki _powinny_ skupić się na zadaniach uczenia maszynowego, takich jak:

+ Przygotowywanie danych, takie jak importowanie, weryfikowanie i czyszczenie, zniekształcanie i przekształcanie, normalizacja i przemieszczanie
+ Konfigurowanie trenowania obejmujące parametryzację argumentów, ścieżki plików i konfiguracje rejestrowania/raportowania
+ Wydajne i wielokrotne szkolenie i sprawdzanie poprawności. Wydajność może wymagać określenia określonych podzestawów danych, różnych zasobów obliczeniowych sprzętu, przetwarzania rozproszonego i monitorowania postępu
+ Wdrażanie, w tym przechowywanie wersji, skalowanie, aprowizowanie i kontrola dostępu

Niezależne kroki umożliwiają wielu analitykom danych jednoczesne działanie w tym samym potoku bez zasobów obliczeniowych. Osobne kroki ułatwiają również korzystanie z różnych typów i rozmiarów obliczeniowych dla każdego kroku.

Po zaprojektowaniu potoku jest często bardziej precyzyjne dostosowywanie pętli szkoleniowej potoku. Po ponownym uruchomieniu potoku przebieg jest przeskakuje do kroków, które należy uruchomić ponownie, takich jak zaktualizowany skrypt szkoleniowy. Kroki, które nie wymagają ponownego uruchomienia, są pomijane. 

Przy użyciu potoków można użyć innego sprzętu do różnych zadań. Platforma Azure koordynuje różne używane [cele obliczeń](concept-azure-machine-learning-architecture.md) , dzięki czemu dane pośrednie są bezproblemowo przesyłane do podrzędnych docelowych obliczeń.

[Metryki dla eksperymentów potoku można śledzić](./how-to-track-experiments.md) bezpośrednio w Azure Portal lub na [stronie docelowej obszaru roboczego (wersja zapoznawcza)](https://ml.azure.com). Po opublikowaniu potoku można skonfigurować punkt końcowy REST, który umożliwia ponowne uruchomienie potoku z poziomu dowolnej platformy lub stosu.

Krótko mówiąc, wszystkie złożone zadania cyklu życia uczenia maszynowego mogą być przydatne w przypadku potoków. Inne technologie potoku platformy Azure mają własne zalety. [Azure Data Factory potoki](../data-factory/concepts-pipelines-activities.md) programu Excel w pracy z danymi i [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/) to odpowiednie narzędzie do ciągłej integracji i wdrażania. Ale jeśli fokus jest uczeniem maszynowym, Azure Machine Learning potoki mogą być najlepszym wyborem dla potrzeb przepływu pracy. 

### <a name="analyzing-dependencies"></a>Analizowanie zależności

Wiele ekosystemów programistycznych ma narzędzia, które organizują zależności zasobów, bibliotek lub kompilacji. Ogólnie rzecz biorąc, te narzędzia używają sygnatur czasowych plików do obliczenia zależności. Gdy plik zostanie zmieniony, tylko jego elementy zależne są aktualizowane (pobrane, ponownie skompilowane lub spakowane). Potoki Azure Machine Learning rozszerać tę koncepcję. Podobnie jak w przypadku tradycyjnych narzędzi do kompilowania, potoki obliczają zależności między krokami i wykonują wymagane ponowne obliczenia. 

Analiza zależności w Azure Machine Learning potoków jest bardziej skomplikowane niż proste sygnatury czasowe. Każdy krok może działać w innym środowisku sprzętu i oprogramowania. Przygotowanie danych może być czasochłonnym procesem, ale nie musi być uruchamiane na sprzęcie z zaawansowanymi procesorami GPU, niektóre kroki mogą wymagać oprogramowania specyficznego dla systemu operacyjnego, dlatego warto używać szkolenia rozproszonego i tak dalej. 

Azure Machine Learning automatycznie organizuje wszystkie zależności między krokami potoku. Ta aranżacja może obejmować nadzielenie i zmniejszenie obrazów platformy Docker, dołączanie i odłączanie zasobów obliczeniowych oraz przeniesienie danych między krokami w sposób spójny i automatyczny.

### <a name="coordinating-the-steps-involved"></a>Koordynacja pozostałych kroków

Podczas tworzenia i uruchamiania `Pipeline` obiektu należy wykonać następujące czynności wysokiego poziomu:

+ Dla każdego kroku usługa oblicza wymagania dla:
    + Zasoby obliczeniowe sprzętu
    + Zasoby systemu operacyjnego (obrazy platformy Docker)
    + Zasoby oprogramowania (zależności Conda/virtualenv)
    + Dane wejściowe 
+ Usługa określa zależności między krokami, co prowadzi do dynamicznego wykresu wykonawczego
+ Po uruchomieniu każdego węzła w grafie wykonywania:
    + Usługa konfiguruje niezbędne środowisko sprzętowe i programowe (prawdopodobnie przy użyciu istniejących zasobów)
    + Ten krok jest uruchamiany, dostarczając informacje o rejestrowaniu i monitorowaniu do zawierającego go `Experiment` obiektu
    + Po zakończeniu kroku jego wyniki są przygotowywane jako dane wejściowe do następnego kroku i/lub zapisywane w magazynie
    + Zasoby, które nie są już potrzebne, są finalizowane i odłączone

![Kroki potoku](./media/concept-ml-pipelines/run_an_experiment_as_a_pipeline.png)

## <a name="building-pipelines-with-the-python-sdk"></a>Kompilowanie potoków przy użyciu zestawu SDK języka Python

W [zestawie Azure Machine Learning Python SDK](/python/api/overview/azure/ml/install)potok jest obiektem języka Python zdefiniowanym w `azureml.pipeline.core` module. Obiekt [potoku](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline%28class%29) zawiera uporządkowaną sekwencję co najmniej jednego obiektu [PipelineStep](/python/api/azureml-pipeline-core/azureml.pipeline.core.builder.pipelinestep) . `PipelineStep`Klasa jest abstrakcyjna i rzeczywiste kroki będą podklasami, takimi jak [EstimatorStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.estimatorstep), [PythonScriptStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.pythonscriptstep)lub [DataTransferStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.datatransferstep). Klasa [ModuleStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.modulestep) przechowuje sekwencję kroków wielokrotnego użytku, które mogą być współużytkowane między potokami. `Pipeline`Działa jako część elementu `Experiment` .

Potok usługi Azure Machine Learning jest skojarzony z obszarem roboczym Azure Machine Learning, a krok potoku jest skojarzony z elementem docelowym obliczeń dostępnym w tym obszarze roboczym. Aby uzyskać więcej informacji, zobacz temat [Tworzenie obszarów roboczych Azure Machine Learning i zarządzanie nimi w Azure Portal](./how-to-manage-workspace.md) lub [co to są cele obliczeniowe w Azure Machine Learning?](./concept-compute-target.md).

### <a name="a-simple-python-pipeline"></a>Prosty potok Python

Ten fragment kodu przedstawia obiekty i wywołania, które są konieczne do utworzenia i uruchomienia `Pipeline` :

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

Fragment kodu rozpoczyna się od typowego Azure Machine Learning obiektów, a `Workspace` , a `Datastore` , [ComputeTarget](/python/api/azureml-core/azureml.core.computetarget)i `Experiment` . Następnie kod tworzy obiekty do zatrzymania `input_data` i `prepped_data_path` . `input_data`Jest wystąpieniem elementu [FileDataset](/python/api/azureml-core/azureml.data.filedataset) i `prepped_data_path` jest wystąpieniem elementu [OutputFileDatasetConfig](/python/api/azureml-core/azureml.data.output_dataset_config.outputfiledatasetconfig). W przypadku `OutputFileDatasetConfig` domyślnego zachowania należy skopiować dane wyjściowe do magazynu danych `workspaceblobstore` pod ścieżką `/dataset/{run-id}/{output-name}` , gdzie `run-id` jest identyfikatorem uruchomienia i `output-name` jest wartością wygenerowaną automatycznie, jeśli nie została określona przez dewelopera.

Kod przygotowywania danych (niepokazywany) — zapisuje pliki rozdzielane do `prepped_data_path` . Te wyjścia z kroku przygotowywania danych są przenoszone `prepped_data` do etapu szkolenia. 

Tablica `steps` zawiera dwa `PythonScriptStep` s `dataprep_step` i `train_step` . Azure Machine Learning będzie analizować zależność danych `prepped_data` i uruchamiać ją `dataprep_step` przed `train_step` . 

Następnie kod tworzy wystąpienie `Pipeline` samego obiektu, przekazując w obszarze roboczym i w tablicy kroków. Wywołanie `experiment.submit(pipeline)` rozpoczynające uruchomienie potoku usługi Azure ml. Wywołanie bloków do `wait_for_completion()` momentu zakończenia potoku. 

Aby dowiedzieć się więcej na temat łączenia potoku z danymi, zobacz artykuł [Data Access (dostęp do danych) w Azure Machine Learning](concept-data.md) i [przeniesienie danych do i między etapami potoku (Python)](how-to-move-data-in-out-of-pipelines.md). 

## <a name="building-pipelines-with-the-designer"></a>Kompilowanie potoków przy użyciu narzędzia Projektant

Deweloperzy, którzy preferują powierzchnię projektowania wizualizacji, mogą używać projektanta Azure Machine Learning do tworzenia potoków. Możesz uzyskać dostęp do tego narzędzia z poziomu wybranego **projektanta** na stronie głównej obszaru roboczego.  Projektant umożliwia przeciąganie i upuszczanie kroków na powierzchnię projektu. 

Podczas wizualnego projektowania potoków dane wejściowe i wyjściowe kroku są wyświetlane w sposób widoczny. Możesz przeciągać i upuszczać dane, co pozwala szybko zrozumieć i zmodyfikować przepływu danych potoku.

![Przykład projektanta Azure Machine Learning](./media/concept-designer/designer-drag-and-drop.gif)

## <a name="key-advantages"></a>Zalety najważniejszych

Najważniejsze zalety używania potoków dla przepływów pracy usługi Machine Learning to:

|Najważniejsze zalety|Opis|
|:-------:|-----------|
|**Nienadzorowane &nbsp; uruchomienia**|Zaplanuj wykonywanie kroków równolegle lub w kolejności w sposób niezawodny i nienadzorowany. Przygotowanie i modelowanie danych może trwać ostatni dzień lub tygodnie, a potoki umożliwiają skoncentrowanie się na innych zadaniach w trakcie działania procesu. |
|**Obliczenia heterogenicznych**|Używaj wielu potoków, które są niezawodne, skoordynowane w heterogenicznych i skalowalnych zasobach obliczeniowych i lokalizacjach magazynu. Wydajnie korzystaj z dostępnych zasobów obliczeniowych, uruchamiając poszczególne etapy potoku w różnych obiektach docelowych obliczeń, takich jak HDInsight, maszyny wirtualne do nauki o danych procesora GPU i kostki datacegły.|
|**Możliwość ponownego wykorzystania**|Tworzenie szablonów potoku dla konkretnych scenariuszy, takich jak ponowne szkolenie i ocenianie partii. Wyzwalaj opublikowane potoki z systemów zewnętrznych za pośrednictwem prostych wywołań REST.|
|**Śledzenie i przechowywanie wersji**|Zamiast ręcznego śledzenia danych i ścieżek wyników podczas iteracji Użyj zestawu SDK potoków, aby jawnie nazwać i w wersji źródła danych, dane wejściowe i wyjściowe. Możesz również zarządzać skryptami i danymi osobno w celu zwiększenia produktywności.|
| **Modułowość** | Rozdzielenie obszarów problemów i izolacja zmian pozwala na szybsze rozwijanie się oprogramowania o wyższej jakości. | 
|**Współpraca**|Potoki umożliwiają analitykom danych współpracę we wszystkich obszarach procesu projektowania uczenia maszynowego, jednocześnie umożliwiając jednocześnie pracę nad krokami potoku.|

## <a name="next-steps"></a>Następne kroki

Potoki Azure Machine Learning są zaawansowaną funkcją, która rozpoczyna dostarczanie wartości na wczesnych etapach tworzenia oprogramowania. Wartość zwiększa się wraz ze wzrostem zespołu i projektu. W tym artykule wyjaśniono, jak potoki są określone za pomocą zestawu SDK języka Python Azure Machine Learning i zorganizowane na platformie Azure. Widzisz prosty kod źródłowy i został wprowadzony do kilku `PipelineStep` dostępnych klas. W przypadku używania potoków Azure Machine Learning i sposobu ich uruchamiania na platformie Azure należy mieć sens. 

+ Dowiedz się, jak [utworzyć pierwszy potok](./how-to-create-machine-learning-pipelines.md).

+ Dowiedz się, jak [uruchamiać przewidywania wsadowe w przypadku dużych ilości danych](tutorial-pipeline-batch-scoring-classification.md ).

+ Zapoznaj się z dokumentacją zestawu SDK dotyczącej głównych [kroków i etapów](/python/api/azureml-pipeline-steps/) [potoku](/python/api/azureml-pipeline-core/) .

+ Wypróbuj przykładowe notesy Jupyter, które przedstawiają [potoki Azure Machine Learning](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines). Dowiedz się, jak [uruchamiać notesy w celu eksplorowania tej usługi](samples-notebooks.md).