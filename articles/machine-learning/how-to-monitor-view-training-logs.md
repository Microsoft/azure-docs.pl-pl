---
title: Monitorowanie i przeglądanie dzienników przebiegów w ML & metryki
titleSuffix: Azure Machine Learning
description: Monitoruj eksperymenty z wodą i wyświetlaj metryki uruchamiania z Jupyter widżetów i Azure Machine Learning Studio.
services: machine-learning
author: likebupt
ms.author: keli19
ms.reviewer: peterlu
ms.service: machine-learning
ms.subservice: core
ms.date: 07/30/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: c258ac62617cb6ac954e0b8c59928225c7f477b1
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105935581"
---
# <a name="monitor-and-view-ml-run-logs-and-metrics"></a>Monitorowanie i wyświetlanie dzienników i metryk przebiegów ML

Dowiedz się, jak monitorować uruchomienia Azure Machine Learning i przeglądać ich dzienniki. 

Po uruchomieniu eksperymentu dzienniki i metryki są przesyłane strumieniowo.  Ponadto możesz dodać własne.  Aby dowiedzieć się, jak to zrobić, zobacz [Włączanie rejestrowania w usłudze Azure ml Training](how-to-track-experiments.md).

Dzienniki mogą pomóc w diagnozowaniu błędów i ostrzeżeń dotyczących przebiegu. Metryki wydajności, takie jak parametry i dokładność modelu, ułatwiają śledzenie i monitorowanie przebiegów.

W tym artykule przedstawiono sposób wyświetlania dzienników przy użyciu następujących metod:

> [!div class="checklist"]
> * Monitorowanie przebiegów w Studio
> * Monitoruj przebiegi przy użyciu widżetu Jupyter Notebook
> * Monitoruj zautomatyzowane uruchomienia uczenia maszynowego
> * Wyświetlanie dzienników wyjściowych po zakończeniu
> * Wyświetlanie dzienników wyjściowych w programie Studio

Aby uzyskać ogólne informacje na temat sposobu zarządzania eksperymentami, zobacz [Uruchamianie, monitorowanie i anulowanie przebiegów szkoleniowych](how-to-manage-runs.md).

## <a name="monitor-runs-using-the-jupyter-notebook-widget"></a>Monitorowanie uruchomień przy użyciu widżetu Notes Jupyter

Gdy używasz metody **ScriptRunConfig** do przesyłania przebiegów, możesz obserwować postęp przebiegu przy użyciu [widżetu Jupyter](/python/api/azureml-widgets/azureml.widgets). Podobnie jak przesyłanie przebiegu, widżet jest asynchroniczny i udostępnia aktualizacje na bieżąco co 10–15 sekund aż do zakończenia zadania.

Wyświetl widżet Jupyter podczas oczekiwania na ukończenie przebiegu.
    
```python
from azureml.widgets import RunDetails
RunDetails(run).show()
```

![Zrzut ekranu widżetu Jupyter Notes](./media/how-to-track-experiments/run-details-widget.png)

Możesz również uzyskać link do tego samego ekranu w obszarze roboczym.

```python
print(run.get_portal_url())
```

## <a name="monitor-automated-machine-learning-runs"></a>Monitoruj zautomatyzowane uruchomienia uczenia maszynowego

W przypadku zautomatyzowanych przebiegów uczenia maszynowego w celu uzyskania dostępu do wykresów z poprzedniego przebiegu Zastąp `<<experiment_name>>` odpowiednią nazwą eksperymentu:

```python
from azureml.widgets import RunDetails
from azureml.core.run import Run

experiment = Experiment (workspace, <<experiment_name>>)
run_id = 'autoML_my_runID' #replace with run_ID
run = Run(experiment, run_id)
RunDetails(run).show()
```

![Widżet Jupyter Notes dla zautomatyzowanych Machine Learning](./media/how-to-track-experiments/azure-machine-learning-auto-ml-widget.png)

## <a name="show-output-upon-completion"></a>Pokaż dane wyjściowe po zakończeniu

Gdy korzystasz z **ScriptRunConfig**, możesz użyć, ```run.wait_for_completion(show_output = True)``` Aby pokazać, kiedy szkolenie modelu zostało zakończone. ```show_output```Flaga zapewnia pełne dane wyjściowe. Aby uzyskać więcej informacji, zobacz sekcję ScriptRunConfig, [jak włączyć rejestrowanie](how-to-track-experiments.md#scriptrun-logs).

<a id="queryrunmetrics"></a>

## <a name="view-run-metrics"></a>Wyświetl metryki uruchamiania

## <a name="via-the-sdk"></a>Za pośrednictwem zestawu SDK
Możesz wyświetlić metryki modelu przeszkolonego za pomocą ```run.get_metrics()``` . Zobacz przykład poniżej. 

```python
from azureml.core import Run
run = Run.get_context()
run.log('metric-name', metric_value)

metrics = run.get_metrics()
# metrics is of type Dict[str, List[float]] mapping mertic names
# to a list of the values for that metric in the given run.

metrics.get('metric-name')
# list of metrics in the order they were recorded
```

<a name="view-the-experiment-in-the-web-portal"></a>

## <a name="view-run-records-in-the-studio"></a>Wyświetlanie rekordów uruchamiania w Studio

W programie [Azure Machine Learning Studio](https://ml.azure.com)można przeglądać ukończone rekordy przebiegu, w tym zarejestrowane metryki.

Przejdź do karty **eksperymenty** . Aby wyświetlić wszystkie uruchomienia w obszarze roboczym w ramach eksperymentów, wybierz kartę **wszystkie uruchomienia** . Możesz przejść do szczegółów przebiegów dla konkretnych eksperymentów, stosując filtr eksperymentów na górnym pasku menu.

W widoku pojedynczego eksperymentu wybierz kartę **wszystkie eksperymenty** . Na pulpicie nawigacyjnym uruchamiania eksperymentu można zobaczyć śledzone metryki i dzienniki dla każdego przebiegu. 

Możesz również edytować tabelę listy uruchamiania, aby wybrać wiele uruchomień i wyświetlić ostatnią, minimalną lub maksymalną zarejestrowana wartość dla przebiegów. Dostosuj wykresy, aby porównać wartości zarejestrowanej metryk i agregacje w wielu przebiegach. 

![Szczegóły uruchamiania w programie Azure Machine Learning Studio](media/how-to-track-experiments/experimentation-tab.gif)

### <a name="view-and-download-log-files-for-a-run"></a>Wyświetlanie i pobieranie plików dzienników dla przebiegu 

Pliki dziennika są podstawowym zasobem do debugowania obciążeń związanych z platformą Azure ML. Przejdź do szczegółów określonego uruchomienia, aby wyświetlić jego dzienniki i dane wyjściowe:  

1. Przejdź do karty **eksperymenty** .
1. Wybierz runID dla określonego uruchomienia.
1. W górnej części strony wybierz pozycję dane **wyjściowe i dzienniki** .
2. Wybierz pozycję **Pobierz wszystko** , aby pobrać wszystkie dzienniki do folderu zip.

:::image type="content" source="media/how-to-monitor-view-training-logs/download-logs.png" alt-text="Zrzut ekranu przedstawiający sekcję danych wyjściowych i dzienników przebiegu.":::

W poniższych tabelach przedstawiono zawartość plików dziennika w folderach, które znajdują się w tej sekcji.

> [!NOTE]
> Każdy plik nie musi być widoczny dla każdego przebiegu. Na przykład 20_image_build_log *. txt pojawia się tylko wtedy, gdy tworzony jest nowy obraz (np. w przypadku zmiany środowiska).

#### <a name="azureml-logs-folder"></a>`azureml-logs` system32\drivers\etc

|Plik  |Opis  |
|---------|---------|
|20_image_build_log.txt     | Dziennik kompilowania obrazu platformy Docker dla środowiska szkoleniowego, opcjonalnie, jeden na uruchomienie. Dotyczy tylko podczas aktualizowania środowiska. W przeciwnym razie AML będzie ponownie używany w pamięci podręcznej. Jeśli powodzenie, zawiera szczegóły rejestru obrazu dla odpowiedniego obrazu.         |
|55_azureml-<node_id # C1.txt     | Dziennik stdout/stderr narzędzia hosta, jeden na węzeł. Pobiera obraz do elementu docelowego obliczeń. Należy pamiętać, że ten dziennik pojawia się tylko wtedy, gdy masz zabezpieczonych zasobów obliczeniowych.         |
|65_job_prep-<node_id # C1.txt     |   Dziennik stdout/stderr skryptu przygotowania zadania, jeden na węzeł. Pobierz swój kod na element docelowy obliczeń i magazyny danych (jeśli jest to wymagane).       |
|70_driver_log (_x). txt      |  Dziennik stdout/stderr ze skryptu kontroli AML i skryptu szkoleniowego klienta — jeden na każdy proces. **To są standardowe dane wyjściowe ze skryptu. Jest to miejsce, w którym są wyświetlane dzienniki kodu (np. Print Statements).** W większości przypadków monitorowanie dzienników będzie możliwe.       |
|70_mpi_log.txt     |   Dziennik struktury MPI, opcjonalny, jeden na uruchomienie. Tylko w przypadku uruchamiania MPI.   |
|75_job_post-<node_id # C1.txt     |  Dziennik stdout/stderr skryptu zwolnienia zadania, jeden na węzeł. Wyślij dzienniki, zwolnij zasoby obliczeniowe z powrotem na platformę Azure.        |
|process_info.jsna      |   pokazuje, który proces jest uruchomiony, w którym węźle.  |
|process_status.jsna      | Pokaż stan procesu, np. Jeśli proces nie został uruchomiony, jest uruchomiony lub zakończony.         |

#### <a name="logs--azureml-folder"></a>`logs > azureml` system32\drivers\etc

|Plik  |Opis  |
|---------|---------|
|110_azureml. log      |         |
|job_prep_azureml. log     |   Dziennik systemu na potrzeby przygotowania zadania        |
|job_release_azureml. log     | Dziennik systemowy dla wydania zadania        |

#### <a name="logs--azureml--sidecar--node_id-folder"></a>`logs > azureml > sidecar > node_id` system32\drivers\etc

Po włączeniu przyczepki — skrypty przygotowania i zwolnienia zadań zostaną uruchomione w kontenerze przyczepki.  Istnieje jeden folder dla każdego węzła. 

|Plik  |Opis  |
|---------|---------|
|start_cms.txt     |  Dziennik procesu, który jest uruchamiany po rozpoczęciu kontenera przyczepki       |
|prep_cmd.txt      |   Dziennik dla ContextManagers wprowadzonych podczas `job_prep.py` uruchamiania (niektóre z nich zostaną przesłane strumieniowo do `azureml-logs/65-job_prep` )       |
|release_cmd.txt     |  Dziennik dla ComtextManagers został zakończony, gdy `job_release.py` jest uruchomiony        |

#### <a name="other-folders"></a>Inne foldery

W przypadku zadań szkoleniowych dotyczących klastrów wieloskładnikowych dzienniki są obecne dla każdego węzła IP. Struktura dla każdego węzła jest taka sama jak zadania jednego węzła. Istnieje jeden folder dodatkowych dzienników dla dzienników ogólnych wykonywania, stderr i stdout.

Azure Machine Learning rejestruje informacje z różnych źródeł podczas szkolenia, takie jak AutoML lub kontener platformy Docker, który uruchamia zadanie szkoleniowe. Wiele z tych dzienników nie jest udokumentowane. Jeśli wystąpią problemy i skontaktuje się z działem pomocy technicznej firmy Microsoft, mogą oni korzystać z tych dzienników podczas rozwiązywania problemów.

## <a name="monitor-a-compute-cluster"></a>Monitorowanie klastra obliczeniowego

Aby monitorować przebiegi dla określonego elementu docelowego obliczeń z przeglądarki, wykonaj następujące czynności:

1. W [Azure Machine Learning Studio](https://ml.azure.com/), wybierz obszar roboczy, a następnie wybierz pozycję __Oblicz__ z lewej strony.

1. Wybierz pozycję __klastry szkoleniowe__ , aby wyświetlić listę celów obliczeniowych używanych na potrzeby szkolenia. Następnie wybierz klaster.

    ![Wybierz klaster szkoleniowy](./media/how-to-track-experiments/select-training-compute.png)

1. Wybierz pozycję __uruchomienia__. Zostanie wyświetlona lista przebiegów korzystających z tego klastra. Aby wyświetlić szczegóły określonego uruchomienia, Użyj linku w kolumnie __Run__ . Aby wyświetlić szczegóły eksperymentu, Użyj linku w kolumnie __eksperymenty__ .

    ![Wybierz przebiegi dla klastra szkoleniowego](./media/how-to-track-experiments/show-runs-for-compute.png)
    
    > [!TIP]
    > Ponieważ docelowe cele obliczeniowe są udostępnionym zasobem, mogą mieć wiele uruchomionych w kolejce lub aktywnych w danym momencie.
    > 
    > Przebieg może zawierać przebiegi podrzędne, więc jedno zadanie szkoleniowe może spowodować powstanie wielu wpisów.

Po zakończeniu przebiegu nie jest on już wyświetlany na tej stronie. Aby wyświetlić informacje o zakończonych przebiegach, odwiedź sekcję __eksperymenty__ w programie Studio i wybierz eksperyment i Uruchom. Aby uzyskać więcej informacji, zobacz sekcję [wyświetlanie metryk dla ukończonych przebiegów](#view-the-experiment-in-the-web-portal).


## <a name="next-steps"></a>Następne kroki

Spróbuj wykonać następujące kroki, aby dowiedzieć się, jak używać Azure Machine Learning:

* Dowiedz się [, jak śledzić eksperymenty i włączać dzienniki w projektancie Azure Machine Learning](how-to-track-designer-experiments.md).

* Zobacz w samouczku [Trenowanie modelu klasyfikacji obrazów za pomocą usługi Azure Machine Learning](tutorial-train-models-with-aml.md) przykład sposobu rejestrowania najlepszego modelu i wdrożenia go.
