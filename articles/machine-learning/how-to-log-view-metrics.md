---
title: Dziennik & wyświetlić metryki i pliki dziennika
titleSuffix: Azure Machine Learning
description: Włącz rejestrowanie w przebiegach trenowania uczenia maszynowego, aby monitorować metryki przebiegów w czasie rzeczywistym oraz ułatwić diagnozowanie błędów i ostrzeżeń.
services: machine-learning
author: swinner95
ms.author: shwinne
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.date: 04/19/2021
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 5bc7564a11f570833bd6cb10c14267e08c3b5753
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107820558"
---
# <a name="log--view-metrics-and-log-files"></a>Dziennik & wyświetlić metryki i pliki dziennika

Rejestruj informacje w czasie rzeczywistym przy użyciu domyślnego pakietu rejestrowania języka Python Azure Machine Learning funkcji specyficznych dla zestawu SDK języka Python. Możesz rejestrować lokalnie i wysyłać dzienniki do obszaru roboczego w portalu.

Dzienniki mogą ułatwić diagnozowanie błędów i ostrzeżeń lub śledzenie metryk wydajności, takich jak parametry i wydajność modelu. W tym artykule dowiesz się, jak włączyć rejestrowanie w następujących scenariuszach:

> [!div class="checklist"]
> * Metryki uruchamiania dziennika
> * Interaktywne sesje trenowania
> * Przesyłanie zadań trenowania przy użyciu polecenia ScriptRunConfig
> * Natywne ustawienia rejestrowania (`logging`) języka Python
> * Rejestrowanie z dodatkowych źródeł


> [!TIP]
> W tym artykule pokazano, jak monitorować proces trenowania modelu. Jeśli interesuje Cię monitorowanie użycia zasobów i zdarzeń z usługi Azure Machine Learning, takich jak przydziały, ukończone przebiegi trenowania lub ukończone wdrożenia modelu, zobacz [Monitorowanie usługi Azure Machine Learning](monitor-azure-machine-learning.md).

## <a name="data-types"></a>Typy danych

Można rejestrować wiele typów danych, w tym wartości skalarne, listy, tabele, obrazy, katalogi itd. Aby uzyskać więcej informacji oraz przykłady kodu w języku Python dla różnych typów danych, zobacz [stronę referencyjną uruchamiania klasy](/python/api/azureml-core/azureml.core.run%28class%29).

### <a name="logging-run-metrics"></a>Rejestrowanie metryk uruchamiania 

Użyj następujących metod w interfejsach API rejestrowania, aby wpłynąć na wizualizacje metryk. [Zanotuj limity](./resource-limits-quotas-capacity.md#metrics) usługi dla tych zarejestrowanych metryk. 

|Zarejestrowana wartość|Przykładowy kod| Format w portalu|
|----|----|----|
|Rejestrowanie tablicy wartości liczbowych| `run.log_list(name='Fibonacci', value=[0, 1, 1, 2, 3, 5, 8, 13, 21, 34, 55, 89])`|wykres liniowy z jedną zmienną|
|Rejestrowanie pojedynczej wartości liczbowej o tej samej nazwie metryki wielokrotnie używanej (na przykład z pętli for)| `for i in tqdm(range(-10, 10)):    run.log(name='Sigmoid', value=1 / (1 + np.exp(-i))) angle = i / 2.0`| Wykres liniowy z jedną zmienną|
|Wielokrotnie rejestruj wiersz z 2 kolumnami liczbowych|`run.log_row(name='Cosine Wave', angle=angle, cos=np.cos(angle))   sines['angle'].append(angle)      sines['sine'].append(np.sin(angle))`|Wykres liniowy z dwiema zmiennymi|
|Tabela dzienników z 2 kolumnami liczb|`run.log_table(name='Sine Wave', value=sines)`|Wykres liniowy z dwiema zmiennymi|
|Obraz dziennika|`run.log_image(name='food', path='./breadpudding.jpg', plot=None, description='desert')`|Ta metoda umożliwia rejestrowanie pliku obrazu lub wykresu matplotlib do uruchomienia. Te obrazy będą widoczne i porównywalne w rekordzie uruchomienia|

### <a name="logging-with-mlflow"></a>Rejestrowanie za pomocą przepływu MLflow
Rejestrowanie metryk za pomocą rejestratora MLFlowLogger.

```python
from azureml.core import Run
# connect to the workspace from within your running code
run = Run.get_context()
ws = run.experiment.workspace

# workspace has associated ml-flow-tracking-uri
mlflow_url = ws.get_mlflow_tracking_uri()

#Example: PyTorch Lightning
from pytorch_lightning.loggers import MLFlowLogger

mlf_logger = MLFlowLogger(experiment_name=run.experiment.name, tracking_uri=mlflow_url)
mlf_logger._run_id = run.id
```

## <a name="view-run-metrics"></a>Wyświetlanie metryk uruchamiania

## <a name="via-the-sdk"></a>Za pośrednictwem zestawu SDK
Metryki wytrenowany model można wyświetlić przy ```run.get_metrics()``` użyciu . Zobacz przykład poniżej. 

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

## <a name="view-run-metrics-in-aml-studio-ui"></a>Wyświetlanie metryk uruchamiania w interfejsie użytkownika programu AML Studio

Ukończone rekordy przebiegów, w tym zarejestrowane metryki, można przeglądać w [Azure Machine Learning studio](https://ml.azure.com).

Przejdź do karty **Experiments (Eksperymenty).** Aby wyświetlić wszystkie przebiegi w obszarze roboczym między eksperymentami, wybierz **kartę Wszystkie przebiegi.** Możesz przejść do szczegółów przebiegów dla określonych eksperymentów, stosując filtr Eksperyment na górnym pasku menu.

Dla pojedynczego widoku Eksperyment wybierz **kartę Wszystkie eksperymenty.** Na pulpicie nawigacyjnym przebiegów eksperymentu można zobaczyć śledzone metryki i dzienniki dla każdego uruchomienia. 

Możesz również edytować tabelę listy przebiegów, aby wybrać wiele przebiegów i wyświetlić ostatnią, minimalną lub maksymalną zarejestrowaną wartość dla przebiegów. Dostosuj wykresy, aby porównać wartości zarejestrowanych metryk i agregacje w wielu przebiegach. Możesz wykreślić wiele metryk na osi y wykresu i dostosować oś X, aby wykreślić zarejestrowane metryki. 


### <a name="view-and-download-log-files-for-a-run"></a>Wyświetlanie i pobieranie plików dziennika dla uruchomienia 

Pliki dziennika są podstawowym zasobem do debugowania obciążeń usługi Azure ML. Po przesłaniu zadania szkoleniowego przejdź do określonego uruchomienia, aby wyświetlić jego dzienniki i dane wyjściowe:  

1. Przejdź do karty **Experiments (Eksperymenty).**
1. Wybierz runID dla określonego uruchomienia.
1. Wybierz **pozycję Dane wyjściowe i dzienniki** w górnej części strony.
2. Wybierz **pozycję Pobierz wszystko,** aby pobrać wszystkie dzienniki do folderu zip.
3. Możesz również pobrać poszczególne pliki dziennika, wybierając plik dziennika i wybierając pozycję **Pobierz.**

:::image type="content" source="media/how-to-log-view-metrics/download-logs.png" alt-text="Zrzut ekranu przedstawiający sekcję Dane wyjściowe i dzienniki uruchomienia.":::

W poniższych tabelach przedstawiono zawartość plików dziennika w folderach, które będą wyświetlane w tej sekcji.

> [!NOTE]
> Nie zawsze zobaczysz każdy plik dla każdego uruchomienia. Na przykład plik 20_image_build_log*.txt jest wyświetlany tylko podczas budowy nowy obraz (np. podczas zmiany środowiska).

#### <a name="azureml-logs-folder"></a>`azureml-logs` Folder

|Plik  |Opis  |
|---------|---------|
|20_image_build_log.txt     | Dziennik budowania obrazu platformy Docker dla środowiska szkoleniowego ( opcjonalnie jeden na przebieg). Ma zastosowanie tylko podczas aktualizowania środowiska. W przeciwnym razie AML ponownie użyje buforowanych obrazów. W przypadku powodzenia program zawiera szczegóły rejestru obrazów dla odpowiedniego obrazu.         |
|55_azureml-execution-<node_id>.txt     | dziennik stdout/stderr narzędzia hosta, po jednym na węzeł. Ściąga obraz do docelowego obiektu obliczeniowego. Pamiętaj, że ten dziennik jest wyświetlany tylko po zabezpieczonych zasobach obliczeniowych.         |
|65_job_prep-<node_id>.txt     |   dziennik stdout/stderr skryptu przygotowania zadania, po jednym na węzeł. Pobierz kod do obliczeniowego obiektu docelowego i magazynów danych (jeśli jest to wymagane).       |
|70_driver_log(_x).txt      |  dziennik stdout/stderr ze skryptu sterowania AML i skryptu trenowania klienta, po jednym na proces. **Standardowe dane wyjściowe skryptu. W tym pliku są wyświetlane dzienniki kodu (na przykład instrukcje drukowania).** W większości przypadków będziesz monitorować dzienniki w tym miejscu.       |
|70_mpi_log.txt     |   Dziennik struktury MPI, opcjonalnie, jeden na przebieg. Tylko w przypadku uruchamiania mpi.   |
|75_job_post-<node_id>.txt     |  dziennik stdout/stderr skryptu wydania zadania, po jednym na węzeł. Wysyłanie dzienników i zwalnianie zasobów obliczeniowych z powrotem na platformę Azure.        |
|process_info.jswł.      |   Pokaż, który proces jest uruchomiony w którym węźle.  |
|process_status.jswł.      | wyświetlanie stanu procesu, na przykład jeśli proces nie został uruchomiony, uruchomiony lub ukończony.         |

#### <a name="logs--azureml-folder"></a>`logs > azureml` Folder

|Plik  |Opis  |
|---------|---------|
|110_azureml.log      |         |
|job_prep_azureml.log     |   dziennik systemu do przygotowania zadania        |
|job_release_azureml.log     | dziennik systemu dla wydania zadania        |

#### <a name="logs--azureml--sidecar--node_id-folder"></a>`logs > azureml > sidecar > node_id` Folder

Po włączeniu funkcji sidecar skrypty wstępnego i zwolnienia zadania będą uruchamiane w kontenerze sidecar.  Każdy węzeł ma jeden folder. 

|Plik  |Opis  |
|---------|---------|
|start_cms.txt     |  Dziennik procesu, który uruchamia się podczas uruchamiania kontenera sidecar       |
|prep_cmd.txt      |   Dziennik dla menedżerów kontekstu wprowadzony podczas uruchamiania (część tej zawartości będzie `job_prep.py` przesyłana strumieniowo do `azureml-logs/65-job_prep` )       |
|release_cmd.txt     |  Dziennik menedżerów comtextmanager został uruchomiony po `job_release.py` uruchomieniu        |

#### <a name="other-folders"></a>Inne foldery

W przypadku zadań trenowania w klastrach z wieloma obliczeniami dzienniki są dostępne dla każdego adresu IP węzła. Struktura każdego węzła jest taka sama jak w przypadku zadań z jednym węzłem. Istnieje jeszcze jeden folder dzienników dla ogólnego wykonywania, stderr i stdout.

Azure Machine Learning rejestruje informacje z różnych źródeł podczas trenowania, takich jak automl lub kontener platformy Docker, który uruchamia zadanie trenowania. Wiele z tych dzienników nie jest udokumentowanych. Jeśli napotkasz problemy i skontaktujemy się z pomocą techniczną firmy Microsoft, mogą oni mieć możliwość użycia tych dzienników podczas rozwiązywania problemów.


## <a name="interactive-logging-session"></a>Interaktywna sesja rejestrowania

Interaktywne sesje rejestrowania są zwykle używane w środowiskach notesów. Metoda [Experiment.start_logging()](/python/api/azureml-core/azureml.core.experiment%28class%29#start-logging--args----kwargs-) uruchamia interaktywną sesję rejestrowania. Wszystkie metryki zarejestrowane w trakcie sesji są dodawane do rekordu przebiegu w eksperymencie. Metoda [run.complete()](/python/api/azureml-core/azureml.core.run%28class%29#complete--set-status-true-) kończy sesje i oznacza przebieg jako zakończony.

## <a name="scriptrun-logs"></a>Dzienniki ScriptRun

W tej sekcji dowiesz się, jak dodać kod rejestrowania w ramach przebiegów utworzonych podczas konfigurowania za pomocą klasy ScriptRunConfig. Do hermetyzowania skryptów i środowisk na potrzeby powtarzalnych przebiegów można użyć klasy [**ScriptRunConfig**](/python/api/azureml-core/azureml.core.scriptrunconfig). Możesz również użyć tej opcji, aby wyświetlić widżet notesów Jupyter Notebook na potrzeby monitorowania.

Ten przykład wykonuje czyszczenie parametrów nad wartościami alfa i przechwytuje wyniki przy użyciu metody [run.log()](/python/api/azureml-core/azureml.core.run%28class%29#log-name--value--description----).

1. Utwórz skrypt trenowania zawierający logikę rejestrowania, skrypt `train.py`.

   [!code-python[](~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train.py)]


1. Prześlij skrypt ```train.py``` w celu uruchomienia go w środowisku zarządzanym przez użytkownika. Na potrzeby trenowania jest przesyłany cały folder skryptu.

   [!notebook-python[] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train-on-local.ipynb?name=src)]


   [!notebook-python[] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train-on-local.ipynb?name=run)]

    Parametr `show_output` włącza pełne rejestrowanie, które umożliwia wyświetlenie szczegółowych informacji z procesu trenowania, a także informacji o wszelkich zasobach zdalnych lub docelowych obiektach obliczeniowych. Użyj poniższego kodu, aby włączyć pełne rejestrowanie podczas przesyłania eksperymentu.

```python
run = exp.submit(src, show_output=True)
```

Można również użyć tego samego parametru w funkcji `wait_for_completion` w wynikowym przebiegu.

```python
run.wait_for_completion(show_output=True)
```

## <a name="native-python-logging"></a>Natywne rejestrowanie w języku Python

Niektóre dzienniki w zestawie SDK mogą zawierać błąd, który powoduje ustawienie poziomu rejestrowania na DEBUGOWANIE. Aby ustawić poziom rejestrowania, dodaj następujący kod do skryptu.

```python
import logging
logging.basicConfig(level=logging.DEBUG)
```

## <a name="other-logging-sources"></a>Inne źródła rejestrowania

Usługa Azure Machine Learning może również rejestrować informacje z innych źródeł podczas trenowania, takich jak przebiegi zautomatyzowanego uczenia maszynowego lub kontenery platformy Docker, które uruchamiają zadania. Te dzienniki nie są udokumentowane, ale jeśli wystąpią problemy i skontaktujesz się z pomocą techniczną firmy Microsoft, być może będą oni mogli wykorzystać te dzienniki podczas rozwiązywania problemów.

Informacje dotyczące rejestrowania metryk w projektancie usługi Azure Machine Learning znajdują się w temacie [Sposób rejestrowania metryk w projektancie](how-to-track-designer-experiments.md)

## <a name="example-notebooks"></a>Przykładowe notesy

W poniższych notesach przedstawiono pojęcia opisane w tym artykule:
* [how-to-use-azureml/training/train-on-local](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-on-local)
* [how-to-use-azureml/track-and-monitor-experiments/logging-api](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/logging-api)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Następne kroki

Zobacz następujące artykuły, aby dowiedzieć się więcej na temat korzystania z usługi Azure Machine Learning:

* Zobacz w samouczku [Trenowanie modelu klasyfikacji obrazów za pomocą usługi Azure Machine Learning](tutorial-train-models-with-aml.md) przykład sposobu rejestrowania najlepszego modelu i wdrożenia go.