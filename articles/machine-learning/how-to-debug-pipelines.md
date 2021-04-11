---
title: Rozwiązywanie problemów z potokami ML
titleSuffix: Azure Machine Learning
description: Jak rozwiązywać problemy w przypadku wystąpienia błędów z uruchomionym potokiem uczenia maszynowego. Często pułapek i porady ułatwiające debugowanie skryptów przed i podczas wykonywania zdalnego.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: lobrien
ms.author: laobri
ms.date: 10/22/2020
ms.topic: troubleshooting
ms.custom: troubleshooting, devx-track-python, contperf-fy21q2
ms.openlocfilehash: 195942d1787cdef51ee480fa5c5595db99bc7c78
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102522091"
---
# <a name="troubleshooting-machine-learning-pipelines"></a>Rozwiązywanie problemów z potokami uczenia maszynowego

W tym artykule dowiesz się, jak rozwiązywać problemy w przypadku wystąpienia błędów uruchamiania [potoku uczenia maszynowego](concept-ml-pipelines.md) w [zestawach SDK Azure Machine Learning](/python/api/overview/azure/ml/intro) i [Azure Machine Learning Designer](./concept-designer.md). 

## <a name="troubleshooting-tips"></a>Wskazówki dotyczące rozwiązywania problemów

Poniższa tabela zawiera typowe problemy podczas tworzenia potoku z potencjalnymi rozwiązaniami.

| Problem | Możliwe rozwiązanie |
|--|--|
| Nie można przekazać danych do `PipelineData` katalogu | Upewnij się, że utworzono katalog w skrypcie, który odpowiada miejscu, w którym potok oczekuje danych wyjściowych kroku. W większości przypadków argument wejściowy zdefiniuje katalog wyjściowy, a następnie jawnie utworzysz katalog. Użyj polecenia `os.makedirs(args.output_dir, exist_ok=True)`, aby utworzyć katalog wyjściowy. Zapoznaj się z [samouczkiem](tutorial-pipeline-batch-scoring-classification.md#write-a-scoring-script) przykładowego skryptu oceniania, który pokazuje ten Wzorzec projektowy. |
| Błędy zależności | Jeśli w potoku zdalnym widoczne są błędy zależności, które nie wystąpiły podczas testowania lokalnego, potwierdź, że zależności i wersje w środowisku zdalnym są zgodne z tymi w środowisku testowym. (Zobacz [Tworzenie środowiska, buforowanie i ponowne używanie](./concept-environments.md#environment-building-caching-and-reuse)|
| Niejednoznaczne błędy z obiektami docelowymi obliczeń | Spróbuj usunąć i ponownie utworzyć cele obliczeniowe. Ponowne tworzenie obiektów docelowych obliczeń jest szybkie i może rozwiązać pewne problemy przejściowe. |
| Potok nie wykorzystał się z kroków | Ponowne użycie kroku jest włączone domyślnie, ale upewnij się, że nie zostało wyłączone w kroku potoku. Jeśli ponowne użycie jest wyłączone, `allow_reuse` parametr w kroku zostanie ustawiony na `False` . |
| Potok jest niepotrzebny. | Aby zapewnić, że kroki mają zostać uruchomione ponownie tylko wtedy, gdy ich dane podstawowe lub skrypty zmienią się, należy rozdzielić katalogi kodu źródłowego dla każdego kroku. Jeśli używasz tego samego katalogu źródłowego dla wielu kroków, może wystąpić niepotrzebne wykonanie ponownie. Użyj `source_directory` parametru w obiekcie krok potoku, aby wskazać odizolowany katalog dla tego kroku, i upewnij się, że nie używasz tej samej `source_directory` ścieżki dla wielu kroków. |
| Krok spowalniają pracę nad epoką szkoleniową lub innym zachowaniem zapętlenia | Spróbuj przełączyć wszystkie zapisy plików, w tym rejestrowanie, z `as_mount()` do `as_upload()` . Tryb **instalacji** używa zdalnego zwirtualizowanego systemu plików i przekazuje cały plik przy każdym dołączeniu do niego. |
| Rozpoczęcie wykonywania obliczeń trwa długo | Obrazy platformy Docker dla obiektów docelowych obliczeń są ładowane z Azure Container Registry (ACR). Domyślnie Azure Machine Learning tworzy ACR, który korzysta z warstwy usługi *podstawowa* . Zmiana ACR dla obszaru roboczego na warstwę Standardowa lub Premium może skrócić czas potrzebny do kompilowania i ładowania obrazów. Aby uzyskać więcej informacji, zobacz [Azure Container Registry warstwy usług](../container-registry/container-registry-skus.md). |

### <a name="authentication-errors"></a>Błędy uwierzytelniania

W przypadku wykonywania operacji zarządzania na obiekcie docelowym obliczeń z zadania zdalnego zostanie wyświetlony jeden z następujących błędów: 

```json
{"code":"Unauthorized","statusCode":401,"message":"Unauthorized","details":[{"code":"InvalidOrExpiredToken","message":"The request token was either invalid or expired. Please try again with a valid token."}]}
```

```json
{"error":{"code":"AuthenticationFailed","message":"Authentication failed."}}
```

Na przykład, jeśli spróbujesz utworzyć lub dołączyć obiekt docelowy obliczeń z potoku, który został przesłany do wykonania zdalnego, zostanie wyświetlony komunikat o błędzie.
## <a name="troubleshooting-parallelrunstep"></a>Rozwiązywanie problemów z `ParallelRunStep` 

Skrypt dla a `ParallelRunStep` *musi zawierać* dwie funkcje:
- `init()`: Ta funkcja jest używana do dowolnych kosztownych lub wspólnych przygotowań do późniejszego wnioskowania. Na przykład użyj go do załadowania modelu do obiektu globalnego. Ta funkcja zostanie wywołana tylko raz na początku procesu.
-  `run(mini_batch)`: Funkcja zostanie uruchomiona dla każdego `mini_batch` wystąpienia.
    -  `mini_batch`: `ParallelRunStep` spowoduje wywołanie metody Run i przekazanie listy lub Pandas `DataFrame` jako argumentu do metody. Każdy wpis w mini_batch będzie ścieżką pliku, jeśli dane wejściowe to, `FileDataset` lub Pandas, `DataFrame` Jeśli dane wejściowe to `TabularDataset` .
    -  `response`: Metoda Run () powinna zwracać element Pandas `DataFrame` lub tablicę. W przypadku append_row output_action te zwrócone elementy są dołączane do wspólnego pliku wyjściowego. W przypadku summary_only zawartość elementów jest ignorowana. Dla wszystkich akcji wyjściowych każdy zwrócony element wyjściowy wskazuje jeden udany przebieg elementu wejściowego w danych wejściowych. Upewnij się, że wystarczająca ilość danych jest uwzględniona w wyniku uruchomienia, aby zamapować dane wejściowe w celu uruchomienia wyniku. Dane wyjściowe uruchamiania będą zapisywane w pliku wyjściowym i nie będą gwarantowane w kolejności, dlatego należy użyć pewnego klucza w danych wyjściowych, aby zamapować go na dane wejściowe.

```python
%%writefile digit_identification.py
# Snippets from a sample script.
# Refer to the accompanying digit_identification.py
# (https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/machine-learning-pipelines/parallel-run)
# for the implementation script.

import os
import numpy as np
import tensorflow as tf
from PIL import Image
from azureml.core import Model


def init():
    global g_tf_sess

    # Pull down the model from the workspace
    model_path = Model.get_model_path("mnist")

    # Construct a graph to execute
    tf.reset_default_graph()
    saver = tf.train.import_meta_graph(os.path.join(model_path, 'mnist-tf.model.meta'))
    g_tf_sess = tf.Session()
    saver.restore(g_tf_sess, os.path.join(model_path, 'mnist-tf.model'))


def run(mini_batch):
    print(f'run method start: {__file__}, run({mini_batch})')
    resultList = []
    in_tensor = g_tf_sess.graph.get_tensor_by_name("network/X:0")
    output = g_tf_sess.graph.get_tensor_by_name("network/output/MatMul:0")

    for image in mini_batch:
        # Prepare each image
        data = Image.open(image)
        np_im = np.array(data).reshape((1, 784))
        # Perform inference
        inference_result = output.eval(feed_dict={in_tensor: np_im}, session=g_tf_sess)
        # Find the best probability, and add it to the result list
        best_result = np.argmax(inference_result)
        resultList.append("{}: {}".format(os.path.basename(image), best_result))

    return resultList
```

Jeśli masz inny plik lub folder w tym samym katalogu, co skrypt wnioskowania, możesz odwoływać się do niego, wyszukując bieżący katalog roboczy.

```python
script_dir = os.path.realpath(os.path.join(__file__, '..',))
file_path = os.path.join(script_dir, "<file_name>")
```

### <a name="parameters-for-parallelrunconfig"></a>Parametry dla ParallelRunConfig

`ParallelRunConfig` jest to główna konfiguracja `ParallelRunStep` wystąpienia w potoku Azure Machine Learning. Służy do zawijania skryptu i konfigurowania niezbędnych parametrów, w tym wszystkich następujących wpisów:
- `entry_script`: Skrypt użytkownika jako ścieżka do pliku lokalnego, która będzie uruchamiana równolegle na wielu węzłach. Jeśli `source_directory` jest obecny, należy użyć ścieżki względnej. W przeciwnym razie użyj dowolnej ścieżki dostępnej na komputerze.
- `mini_batch_size`: Rozmiar mini-Batch przeszedł do pojedynczego `run()` wywołania. (opcjonalnie; wartość domyślna to `10` pliki dla `FileDataset` i `1MB` dla `TabularDataset` .)
    - Dla `FileDataset` , jest to liczba plików o minimalnej wartości `1` . Można połączyć wiele plików w jedną minimalną partię.
    - W przypadku `TabularDataset` , jest to rozmiar danych. Przykładowe wartości to `1024` , `1024KB` , `10MB` , i `1GB` . Zalecana wartość to `1MB` . Mini-Batch z `TabularDataset` nigdy nie będzie przekraczać granic plików. Jeśli na przykład pliki CSV mają różne rozmiary, najmniejszy plik to 100 KB, a największy to 10 MB. Jeśli ustawisz `mini_batch_size = 1MB` , pliki o rozmiarze mniejszym niż 1 MB będą traktowane jako jedna mini-Batch. Pliki o rozmiarze większym niż 1 MB zostaną podzielone na wiele kart Mini-Part.
- `error_threshold`: Liczba błędów rekordu `TabularDataset` i błędów plików dla `FileDataset` , które powinny zostać zignorowane podczas przetwarzania. Jeśli liczba błędów dla całego danych wejściowych spadnie powyżej tej wartości, zadanie zostanie przerwane. Próg błędu dotyczy całego danych wejściowych, a nie dla pojedynczego elementu mini-Batch wysyłanego do `run()` metody. Zakresem jest `[-1, int.max]` . `-1`Część wskazuje, że wszystkie błędy zostaną zignorowane podczas przetwarzania.
- `output_action`: Jedna z następujących wartości wskazuje, w jaki sposób dane wyjściowe będą zorganizowane:
    - `summary_only`: Skrypt użytkownika będzie przechowywał dane wyjściowe. `ParallelRunStep` będzie używać danych wyjściowych tylko dla obliczeń progu błędu.
    - `append_row`: W przypadku wszystkich danych wejściowych w folderze wyjściowym zostanie utworzony tylko jeden plik, aby dołączyć wszystkie dane wyjściowe rozdzielone wierszami.
- `append_row_file_name`: Aby dostosować nazwę pliku wyjściowego dla append_row output_action (opcjonalnie wartość domyślna to `parallel_run_step.txt` ).
- `source_directory`: Ścieżki do folderów zawierających wszystkie pliki do wykonania na obiekcie docelowym obliczeń (opcjonalnie).
- `compute_target`: `AmlCompute` Obsługiwane są tylko.
- `node_count`: Liczba węzłów obliczeniowych, które mają być używane do uruchamiania skryptu użytkownika.
- `process_count_per_node`: Liczba procesów na węzeł. Najlepszym rozwiązaniem jest ustawienie liczby procesorów GPU lub CPU jednego węzła (opcjonalnie; wartość domyślna to `1` ).
- `environment`: Definicja środowiska języka Python. Można skonfigurować go tak, aby korzystał z istniejącego środowiska Python lub skonfigurować środowisko tymczasowe. Definicja jest również odpowiedzialna za ustawianie wymaganych zależności aplikacji (opcjonalnie).
- `logging_level`: Poziom szczegółowości dziennika. Wartości zwiększające poziom szczegółowości to: `WARNING` , `INFO` , i `DEBUG` . (opcjonalnie; wartość domyślna to `INFO` )
- `run_invocation_timeout`: `run()` Limit czasu wywołania metody (w sekundach). (opcjonalnie; wartość domyślna to `60` )
- `run_max_try`: Maksymalna liczba prób `run()` dla typu mini-Batch. A `run()` nie powiodło się, jeśli wystąpił wyjątek lub nie jest zwracany, gdy `run_invocation_timeout` zostanie osiągnięty błąd (opcjonalnie; wartość domyślna to `3` ). 

Można określić,,,, `mini_batch_size` `node_count` i tak `process_count_per_node` `logging_level` `run_invocation_timeout` `run_max_try` `PipelineParameter` , aby po ponownym przesłaniu uruchomienia potoku można dostosować wartości parametrów. W tym przykładzie użyto `PipelineParameter` dla i, `mini_batch_size` `Process_count_per_node` a następnie zmienisz te wartości w przypadku ponownego przesłania przebiegu później. 

### <a name="parameters-for-creating-the-parallelrunstep"></a>Parametry tworzenia ParallelRunStep

Utwórz ParallelRunStep za pomocą skryptu, konfiguracji środowiska i parametrów. Określ miejsce docelowe obliczeń, które zostało już dołączone do obszaru roboczego jako element docelowy wykonywania skryptu wnioskowania. Użyj `ParallelRunStep` do utworzenia kroku potoku wnioskowania partii, który przyjmuje wszystkie następujące parametry:
- `name`: Nazwa kroku z następującymi ograniczeniami nazewnictwa: Unique, 3-32 znaków i wyrażenie regularne ^ \[ a-z \] ([-a-Z0-9] * [a-Z0-9])? $.
- `parallel_run_config`: `ParallelRunConfig` Obiekt, zgodnie z definicją wcześniejszą.
- `inputs`: Co najmniej jeden zestaw danych z jednym Azure Machine Learning typem, który ma być podzielony na partycje na potrzeby przetwarzania równoległego.
- `side_inputs`: Co najmniej jeden z danych referencyjnych lub zestawów DataSet używany jako dane wejściowe bez konieczności partycjonowania.
- `output`: `OutputFileDatasetConfig` Obiekt, który odpowiada katalogowi wyjściowemu.
- `arguments`: Lista argumentów przenoszona do skryptu użytkownika. Użyj unknown_args, aby pobrać je w skrypcie wprowadzania (opcjonalnie).
- `allow_reuse`: Czy krok ma ponownie używać poprzednich wyników w przypadku uruchamiania z tymi samymi ustawieniami/danymi wejściowymi. Jeśli ten parametr ma wartość `False` , nowy przebieg będzie zawsze generowany dla tego kroku podczas wykonywania potoku. (opcjonalnie; wartość domyślna to `True` ).

```python
from azureml.pipeline.steps import ParallelRunStep

parallelrun_step = ParallelRunStep(
    name="predict-digits-mnist",
    parallel_run_config=parallel_run_config,
    inputs=[input_mnist_ds_consumption],
    output=output_dir,
    allow_reuse=True
)
```

## <a name="debugging-techniques"></a>Techniki debugowania

Istnieją trzy główne techniki dla potoków debugowania: 

* Debuguj poszczególne etapy potoku na komputerze lokalnym
* Użyj rejestrowania i Application Insights, aby wyizolować i zdiagnozować źródło problemu
* Dołącz zdalny debuger do potoku uruchomionego na platformie Azure

### <a name="debug-scripts-locally"></a>Lokalne debugowanie skryptów

Jeden z najczęstszych błędów w potoku polega na tym, że skrypt domeny nie działa zgodnie z oczekiwaniami lub zawiera błędy środowiska uruchomieniowego w zdalnym kontekście obliczeniowym, które są trudne do debugowania.

Potoki same nie mogą być uruchamiane lokalnie, ale uruchamianie skryptów w izolacji na komputerze lokalnym pozwala na szybsze debugowanie, ponieważ nie trzeba czekać na proces tworzenia i kompilowania środowiska. Aby to zrobić, należy wykonać następujące czynności:

* Jeśli dane są w magazynie danych w chmurze, musisz pobrać dane i udostępnić je dla skryptu. Użycie małego przykładu danych jest dobrym sposobem na wycinanie w czasie wykonywania i szybkie uzyskanie opinii na temat zachowania skryptu
* Jeśli podjęto próbę symulowania pośredniego etapu potoku, może być konieczne ręczne skompilowanie typów obiektów, których konkretny skrypt oczekuje od poprzedniego kroku
* Należy również zdefiniować własne środowisko i zreplikować zależności zdefiniowane w zdalnym środowisku obliczeniowym

Po skonfigurowaniu skryptu do uruchomienia w środowisku lokalnym można znacznie łatwiej wykonywać zadania debugowania, takie jak:

* Dołączanie konfiguracji debugowania niestandardowego
* Wstrzymywanie wykonywania i sprawdzania stanu obiektu
* Typ przechwytywania lub błędy logiczne, które nie zostaną ujawnione do czasu wykonania

> [!TIP] 
> Po sprawdzeniu, czy skrypt działa zgodnie z oczekiwaniami, dobrym następnym krokiem jest uruchomienie skryptu w potoku jednoetapowym przed podjęciem próby uruchomienia go w potoku z wieloma krokami.

## <a name="configure-write-to-and-review-pipeline-logs"></a>Konfigurowanie, zapisywanie i przeglądanie dzienników potoku

Testowanie skryptów lokalnie to doskonały sposób na Debugowanie głównych fragmentów kodu i złożonej logiki przed rozpoczęciem tworzenia potoku, ale w pewnym momencie prawdopodobnie trzeba będzie debugować skrypty w trakcie rzeczywistego uruchomienia potoku, szczególnie podczas diagnozowania zachowań występujących podczas interakcji między krokami potoku. Zalecamy korzystanie z niezliberalizowanych `print()` instrukcji w skryptach kroków, dzięki czemu można zobaczyć stan obiektu i oczekiwane wartości podczas wykonywania zdalnego, podobnie jak w przypadku debugowania kodu JavaScript.

### <a name="logging-options-and-behavior"></a>Opcje rejestrowania i zachowanie

Poniższa tabela zawiera informacje dotyczące różnych opcji debugowania potoków. Nie jest to pełna lista, ponieważ istnieją inne opcje oprócz Azure Machine Learning, Python i OpenCensus.

| Biblioteka                    | Typ   | Przykład                                                          | Element docelowy                                  | Zasoby                                                                                                                                                                                                                                                                                                                    |
|----------------------------|--------|------------------------------------------------------------------|----------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Azure Machine Learning SDK | Metric | `run.log(name, val)`                                             | Interfejs użytkownika portalu Azure Machine Learning             | [Jak śledzić eksperymenty](how-to-track-experiments.md)<br>[Azure. Core. Run — Klasa](/python/api/azureml-core/azureml.core.run%28class%29)                                                                                                                                                 |
| Drukowanie w języku Python/rejestrowanie    | Dziennik    | `print(val)`<br>`logging.info(message)`                          | Dzienniki sterowników, Azure Machine Learning Designer | [Jak śledzić eksperymenty](how-to-track-experiments.md)<br><br>[Rejestrowanie w języku Python](https://docs.python.org/2/library/logging.html)                                                                                                                                                                       |
| Zestaw OpenCensus Python          | Dziennik    | `logger.addHandler(AzureLogHandler())`<br>`logging.log(message)` | Application Insights — ślady                | [Debugowanie potoków w usłudze Application Insights](./how-to-log-pipelines-application-insights.md)<br><br>[Eksporterzy biblioteki OpenCensus usługi Azure Monitor](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-azure)<br>[Rejestrowanie w języku Python Cookbook](https://docs.python.org/3/howto/logging-cookbook.html) |

#### <a name="logging-options-example"></a>Przykład opcji rejestrowania

```python
import logging

from azureml.core.run import Run
from opencensus.ext.azure.log_exporter import AzureLogHandler

run = Run.get_context()

# Azure ML Scalar value logging
run.log("scalar_value", 0.95)

# Python print statement
print("I am a python print statement, I will be sent to the driver logs.")

# Initialize python logger
logger = logging.getLogger(__name__)
logger.setLevel(args.log_level)

# Plain python logging statements
logger.debug("I am a plain debug statement, I will be sent to the driver logs.")
logger.info("I am a plain info statement, I will be sent to the driver logs.")

handler = AzureLogHandler(connection_string='<connection string>')
logger.addHandler(handler)

# Python logging with OpenCensus AzureLogHandler
logger.warning("I am an OpenCensus warning statement, find me in Application Insights!")
logger.error("I am an OpenCensus error statement with custom dimensions", {'step_id': run.id})
``` 

## <a name="azure-machine-learning-designer"></a>Projektant usługi Azure Machine Learning

W przypadku potoków utworzonych w projektancie można znaleźć plik **70_driver_log** na stronie Tworzenie lub na stronie szczegółów uruchomienia potoku.

### <a name="enable-logging-for-real-time-endpoints"></a>Włączanie rejestrowania dla punktów końcowych w czasie rzeczywistym

W celu rozwiązywania problemów i debugowania punktów końcowych w czasie rzeczywistym w projektancie należy włączyć rejestrowanie w usłudze Application Insights przy użyciu zestawu SDK. Rejestrowanie umożliwia rozwiązywanie problemów i debugowanie modeli oraz problemów z użyciem. Aby uzyskać więcej informacji, zobacz [Rejestrowanie wdrożonych modeli](./how-to-enable-app-insights.md). 

### <a name="get-logs-from-the-authoring-page"></a>Pobieranie dzienników ze strony tworzenia

Po przesłaniu uruchomienia potoku i pozostawania na stronie Tworzenie można znaleźć pliki dziennika wygenerowane dla każdego modułu, ponieważ każdy moduł kończy działanie.

1. Wybierz moduł, który zakończył działanie na kanwie tworzenia.
1. W prawym okienku modułu przejdź do karty dane  **wyjściowe i dzienniki** .
1. Rozwiń okienko po prawej stronie i wybierz **70_driver_log.txt** , aby wyświetlić plik w przeglądarce. Możesz również pobrać dzienniki lokalnie.

    ![Rozwinięte okienko danych wyjściowych w projektancie](./media/how-to-debug-pipelines/designer-logs.png)

### <a name="get-logs-from-pipeline-runs"></a>Pobierz dzienniki z przebiegów potoku

Pliki dziennika dla określonych przebiegów można również znaleźć na stronie szczegółów uruchomienia potoku, która znajduje się w sekcji **potoki** lub **eksperymenty** w programie Studio.

1. Wybierz uruchomienie potoku utworzone w projektancie.

    ![Strona uruchomienia potoku](./media/how-to-debug-pipelines/designer-pipelines.png)

1. Wybierz moduł w okienku podglądu.
1. W prawym okienku modułu przejdź do karty dane  **wyjściowe i dzienniki** .
1. Rozwiń okienko po prawej stronie, aby wyświetlić plik **70_driver_log.txt** w przeglądarce, lub wybierz plik, aby pobrać dzienniki lokalnie.

> [!IMPORTANT]
> Aby zaktualizować potok na stronie szczegółów uruchomienia potoku, należy **sklonować** uruchomienie potoku do nowej wersji roboczej potoku. Uruchomienie potoku jest migawką potoku. Jest on podobny do pliku dziennika i nie można go zmienić. 

## <a name="application-insights"></a>Application Insights
Aby uzyskać więcej informacji na temat korzystania z biblioteki OpenCensus Python w ten sposób, zobacz ten przewodnik: [debugowanie i rozwiązywanie problemów z potokami uczenia maszynowego w Application Insights](./how-to-log-pipelines-application-insights.md)

## <a name="interactive-debugging-with-visual-studio-code"></a>Interaktywny debugowanie za pomocą Visual Studio Code

W niektórych przypadkach może być konieczne interaktywne Debugowanie kodu w języku Python używanego w potoku. Korzystając z Visual Studio Code (VS Code) i debugpy, można dołączać do kodu w trakcie jego działania w środowisku szkoleniowym. Aby uzyskać więcej informacji, zobacz [interaktywny debugowanie w przewodniku vs Code](how-to-debug-visual-studio-code.md#debug-and-troubleshoot-machine-learning-pipelines).

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać pełny samouczek przy użyciu `ParallelRunStep` , zobacz [Samouczek: Tworzenie potoku Azure Machine Learning na potrzeby oceniania partii](tutorial-pipeline-batch-scoring-classification.md).

* Aby uzyskać kompletny przykład przedstawiający automatyczne Uczenie maszynowe w potokach ML, zobacz [Korzystanie z zautomatyzowanej ml w potoku Azure Machine Learning w języku Python](how-to-use-automlstep-in-pipelines.md).

* Zapoznaj się z dokumentacją zestawu SDK, aby uzyskać pomoc dotyczącą pakietu [Azure-Pipelines-Core](/python/api/azureml-pipeline-core/) oraz pakietem [kroków dla potoków usługi Azure](/python/api/azureml-pipeline-steps/) .

* Zapoznaj się z listą [wyjątków projektanta i kodów błędów](algorithm-module-reference/designer-error-codes.md).