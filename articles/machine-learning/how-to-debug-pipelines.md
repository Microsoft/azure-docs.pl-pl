---
title: Rozwiązywanie problemów z potokami uczenia maszynowego
titleSuffix: Azure Machine Learning
description: Jak rozwiązywać problemy w przypadku błędów podczas uruchamiania potoku uczenia maszynowego. Typowe pułapki i porady, które ułatwiają debugowanie skryptów przed i podczas zdalnego wykonywania.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: lobrien
ms.author: laobri
ms.date: 10/22/2020
ms.topic: troubleshooting
ms.custom: troubleshooting, devx-track-python, contperf-fy21q2
ms.openlocfilehash: 3a85566f395e97bbe88d52a8b306c7e0aa15669d
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107817345"
---
# <a name="troubleshooting-machine-learning-pipelines"></a>Rozwiązywanie problemów z potokami uczenia maszynowego

Z tego artykułu dowiesz się, jak rozwiązywać problemy w przypadku błędów podczas uruchamiania potoku uczenia maszynowego w zestawie SDK usługi [Azure Machine Learning](/python/api/overview/azure/ml/intro) i [w Azure Machine Learning projektanta.](./concept-designer.md) [](concept-ml-pipelines.md) 

## <a name="troubleshooting-tips"></a>Wskazówki dotyczące rozwiązywania problemów

W poniższej tabeli przedstawiono typowe problemy podczas tworzenia potoku wraz z potencjalnymi rozwiązaniami.

| Problem | Możliwe rozwiązanie |
|--|--|
| Nie można przekazać danych do `PipelineData` katalogu | Upewnij się, że utworzono katalog w skrypcie, który odpowiada miejscu, w którym potok oczekuje danych wyjściowych kroku. W większości przypadków argument wejściowy zdefiniuje katalog wyjściowy, a następnie jawnie utworzysz katalog. Użyj polecenia `os.makedirs(args.output_dir, exist_ok=True)`, aby utworzyć katalog wyjściowy. Zobacz [samouczek,](tutorial-pipeline-batch-scoring-classification.md#write-a-scoring-script) aby uzyskać przykładowy skrypt oceniania, który pokazuje ten wzorzec projektowy. |
| Błędy zależności | Jeśli w potoku zdalnym widoczne są błędy zależności, które nie wystąpiły podczas testowania lokalnego, potwierdź, że zależności i wersje w środowisku zdalnym są zgodne z tymi w środowisku testowym. (Zobacz [Tworzenie, buforowanie i](./concept-environments.md#environment-building-caching-and-reuse) ponowne używanie środowiska|
| Niejednoznaczne błędy dotyczące docelowych obiektów obliczeniowych | Spróbuj usunąć i ponownie utworzyć docelowe obiekty obliczeniowe. Ponowne tworzenie docelowych obiektów obliczeniowych jest szybkie i może rozwiązać niektóre przejściowe problemy. |
| Potok nie może ponownie wykonać kroków | Ponowne użycie kroku jest domyślnie włączone, ale upewnij się, że nie zostało ono wyłączone w kroku potoku. Jeśli ponowne użycie jest wyłączone, `allow_reuse` parametr w kroku zostanie ustawiony na wartość `False` . |
| Potok jest niepotrzebnie ponownie uruchomić | Aby upewnić się, że kroki są ponownie uruchamiane tylko wtedy, gdy zmienią się ich dane bazowe lub skrypty, należy oddziel katalogi kodu źródłowego dla każdego kroku. Jeśli używasz tego samego katalogu źródłowego dla wielu kroków, mogą wystąpić niepotrzebne ponowne uruchomić. Użyj parametru w obiekcie kroku potoku, aby wskazać katalog izolowany dla tego kroku i upewnić się, że nie używasz tej samej ścieżki `source_directory` `source_directory` dla wielu kroków. |
| Krok spowalnia w epoce trenowania lub innym zachowaniu pętli | Spróbuj przełączyć wszystkie zapisu plików, w tym rejestrowanie, z `as_mount()` na `as_upload()` . Tryb **instalacji** używa zdalnego zwirtualizowanego systemu plików i za każdym razem, gdy jest dołączany do niego cały plik. |
| Uruchomienie docelowego obiektu obliczeniowego zajmuje dużo czasu | Obrazy platformy Docker dla docelowych obiektów obliczeniowych są ładowane z Azure Container Registry (ACR). Domyślnie program Azure Machine Learning usługę ACR, która używa *podstawowej warstwy* usług. Zmiana usługi ACR dla obszaru roboczego na warstwę Standardowa lub Premium może skrócić czas kompilowania i ładowania obrazów. Aby uzyskać więcej informacji, [zobacz Azure Container Registry warstwy usług](../container-registry/container-registry-skus.md). |

### <a name="authentication-errors"></a>Błędy uwierzytelniania

W przypadku wykonania operacji zarządzania na docelowym obiektem obliczeniowym z zadania zdalnego wystąpi jeden z następujących błędów: 

```json
{"code":"Unauthorized","statusCode":401,"message":"Unauthorized","details":[{"code":"InvalidOrExpiredToken","message":"The request token was either invalid or expired. Please try again with a valid token."}]}
```

```json
{"error":{"code":"AuthenticationFailed","message":"Authentication failed."}}
```

Na przykład podczas próby utworzenia lub dołączenia docelowego obiektu obliczeniowego z potoku uczenia maszynowego przesłanego do zdalnego wykonania zostanie wyświetlony błąd.
## <a name="troubleshooting-parallelrunstep"></a>Rozwiązywanie problemów `ParallelRunStep` 

Skrypt dla funkcji `ParallelRunStep` *musi zawierać* dwie funkcje:
- `init()`: Użyj tej funkcji do wszelkich kosztownych lub typowych przygotowań do późniejszego wnioskowania. Na przykład użyj go do załadowania modelu do obiektu globalnego. Ta funkcja zostanie wywołana tylko raz na początku procesu.
-  `run(mini_batch)`: funkcja będzie uruchamiana dla każdego `mini_batch` wystąpienia.
    -  `mini_batch`: `ParallelRunStep` wywoła metodę run i przekaże listę lub pandas jako argument do metody `DataFrame` . Każdy wpis w mini_batch będzie ścieżką pliku, jeśli dane wejściowe to a lub `FileDataset` pandas, jeśli dane wejściowe to `DataFrame` `TabularDataset` .
    -  `response`: metoda run() powinna zwrócić pandas `DataFrame` lub tablicę. Na append_row output_action zwracane elementy są dołączane do wspólnego pliku wyjściowego. Na summary_only zawartość elementów jest ignorowana. Dla wszystkich akcji wyjściowych każdy zwrócony element wyjściowy wskazuje jeden pomyślny przebieg elementu wejściowego w wejściowej mini partii. Upewnij się, że w wyniku uruchomienia uwzględniono wystarczającą ilość danych, aby zamapować dane wejściowe, aby uruchomić wynik wyjściowy. Uruchom dane wyjściowe zostaną zapisane w pliku wyjściowym i nie musi być w kolejności. Należy użyć jakiegoś klucza w danych wyjściowych, aby zamapować je na dane wejściowe.

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

Jeśli masz inny plik lub folder w tym samym katalogu, w którym znajduje się skrypt wnioskowania, możesz odwoływać się do niego, znajdując bieżący katalog roboczy.

```python
script_dir = os.path.realpath(os.path.join(__file__, '..',))
file_path = os.path.join(script_dir, "<file_name>")
```

### <a name="parameters-for-parallelrunconfig"></a>Parametry dla ParallelRunConfig

`ParallelRunConfig` jest główną konfiguracją `ParallelRunStep` dla wystąpienia w potoku Azure Machine Learning potoku. Umożliwia opakowywanie skryptu i konfigurowanie niezbędnych parametrów, w tym wszystkich następujących wpisów:
- `entry_script`: skrypt użytkownika jako ścieżka pliku lokalnego, który będzie uruchamiany równolegle w wielu węzłach. Jeśli `source_directory` istnieje, użyj ścieżki względnej. W przeciwnym razie użyj dowolnej ścieżki dostępnej na maszynie.
- `mini_batch_size`: rozmiar mini partii przekazanej do pojedynczego `run()` wywołania. (opcjonalnie; wartość domyślna to `10` pliki `FileDataset` dla i `1MB` `TabularDataset` ).
    - W `FileDataset` przypadku wartości jest to liczba plików o minimalnej wartości `1` . Można połączyć wiele plików w jedną mini partię.
    - W `TabularDataset` przypadku pliku jest to rozmiar danych. Przykładowe wartości `1024` to , , i `1024KB` `10MB` `1GB` . Zalecana wartość to `1MB` . Mini partię z `TabularDataset` nigdy nie przekroczy granic plików. Jeśli na przykład masz pliki csv o różnych rozmiarach, najmniejszy plik ma rozmiar 100 KB, a największy 10 MB. Jeśli ustawisz wartość , pliki o rozmiarze mniejszym niż 1 MB będą `mini_batch_size = 1MB` traktowane jako jedna mini partia. Pliki o rozmiarze większym niż 1 MB zostaną podzielone na wiele mini partii.
- `error_threshold`: liczba niepowodzeń rekordów dla programu i błędów plików, które powinny `TabularDataset` `FileDataset` być ignorowane podczas przetwarzania. Jeśli liczba błędów dla wszystkich danych wejściowych przekracza tę wartość, zadanie zostanie przerwane. Próg błędu jest dla wszystkich danych wejściowych, a nie dla poszczególnych minisad wysyłanych do `run()` metody. Zakres to `[-1, int.max]` . Część `-1` wskazuje ignorowanie wszystkich błędów podczas przetwarzania.
- `output_action`: Jedna z następujących wartości wskazuje sposób zorganizowania danych wyjściowych:
    - `summary_only`: skrypt użytkownika będzie przechowywać dane wyjściowe. `ParallelRunStep` Użyje danych wyjściowych tylko do obliczenia progu błędu.
    - `append_row`: dla wszystkich danych wejściowych w folderze output zostanie utworzony tylko jeden plik, aby dołączyć wszystkie dane wyjściowe oddzielone wierszami.
- `append_row_file_name`: aby dostosować nazwę pliku wyjściowego dla append_row output_action (opcjonalnie; wartość domyślna to `parallel_run_step.txt` ).
- `source_directory`: ścieżki do folderów zawierających wszystkie pliki do wykonania na docelowym obiektem obliczeniowym (opcjonalnie).
- `compute_target`: obsługiwane `AmlCompute` są tylko te urządzenia.
- `node_count`: liczba węzłów obliczeniowych używanych do uruchamiania skryptu użytkownika.
- `process_count_per_node`: liczba procesów na węzeł. Najlepszym rozwiązaniem jest ustawienie na liczbę procesorów GPU lub CPU, które ma jeden węzeł (opcjonalnie; wartość domyślna to `1` ).
- `environment`: definicja środowiska Języka Python. Można je skonfigurować do używania istniejącego środowiska języka Python lub do konfigurowania środowiska tymczasowego. Definicja jest również odpowiedzialna za ustawianie wymaganych zależności aplikacji (opcjonalnie).
- `logging_level`: szczegółowość dziennika. Wartości zwiększające szczegółowość to: `WARNING` `INFO` , i `DEBUG` . (opcjonalnie; wartość domyślna to `INFO` )
- `run_invocation_timeout`: `run()` limit czasu wywołania metody w sekundach. (opcjonalnie; wartość domyślna to `60` )
- `run_max_try`: maksymalna liczba prób `run()` dla mini partii. A nie powiedzie się, jeśli zostanie zgłoszony wyjątek lub nic nie zostanie zwrócone po osiągnięciu `run()` `run_invocation_timeout` (opcjonalnie; wartość domyślna to `3` ). 

Można określić , , , , i jako , aby podczas ponownego prześlij uruchomienia potoku można było dostosować `mini_batch_size` `node_count` wartości `process_count_per_node` `logging_level` `run_invocation_timeout` `run_max_try` `PipelineParameter` parametrów. W tym przykładzie użyjemy parametrów dla parametrów i i zmienisz te wartości podczas późniejszego ponownego prześlij `PipelineParameter` `mini_batch_size` `Process_count_per_node` uruchomienia. 

### <a name="parameters-for-creating-the-parallelrunstep"></a>Parametry do tworzenia parallelRunStep

Utwórz krok ParallelRunStep przy użyciu skryptu, konfiguracji środowiska i parametrów. Określ docelowy obiekt obliczeniowy, który został już dołączony do obszaru roboczego, jako element docelowy wykonywania skryptu wnioskowania. Użyj polecenia , aby utworzyć krok potoku wnioskowania `ParallelRunStep` wsadowego, który przyjmuje wszystkie następujące parametry:
- `name`: nazwa kroku z następującymi ograniczeniami nazewnictwa: unique, 3-32 characters i regex ^ \[ a-z \] ([-a-z0-9]*[a-z0-9])?$.
- `parallel_run_config`: `ParallelRunConfig` obiekt zgodnie z wcześniejszym definicją.
- `inputs`: co najmniej jeden typ zestawu danych Azure Machine Learning do partycjonowania w celu przetwarzania równoległego.
- `side_inputs`: co najmniej jeden referencyjny zestaw danych lub zestaw danych używany jako dane wejściowe po stronie użytkownika bez konieczności partycjonowania.
- `output`: `OutputFileDatasetConfig` obiekt, który odpowiada katalogowi wyjściowemu.
- `arguments`: lista argumentów przekazanych do skryptu użytkownika. Użyj unknown_args, aby pobrać je w skrypcie wejściowym (opcjonalnie).
- `allow_reuse`: czy krok powinien ponownie używać poprzednich wyników po uruchomieniu z tym samymi ustawieniami/wejściami. Jeśli ten parametr ma wartość , dla tego kroku podczas wykonywania potoku zawsze zostanie wygenerowane nowe `False` uruchomienie. (opcjonalnie; wartość domyślna to `True` ).

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

Istnieją trzy główne techniki debugowania potoków: 

* Debugowanie poszczególnych kroków potoku na komputerze lokalnym
* Używanie rejestrowania i Application Insights do izolowania i diagnozowania źródła problemu
* Dołączanie zdalnego debugera do potoku uruchomionego na platformie Azure

### <a name="debug-scripts-locally"></a>Lokalne debugowanie skryptów

Jednym z najbardziej typowych błędów w potoku jest to, że skrypt domeny nie działa zgodnie z zamierzeniami lub zawiera błędy środowiska uruchomieniowego w zdalnym kontekście obliczeniowym, które są trudne do debugowania.

Same potoki nie mogą być uruchamiane lokalnie, ale uruchamianie skryptów w izolacji na komputerze lokalnym umożliwia szybsze debugowanie, ponieważ nie trzeba czekać na proces kompilacji obliczeń i środowiska. W tym celu należy wykonać pewne prace programowe:

* Jeśli dane są w magazynach danych w chmurze, musisz pobrać dane i udostępnić je skryptowi. Użycie niewielkiej próbki danych to dobry sposób na obcięcie środowiska uruchomieniowego i szybkie uzyskiwanie opinii na temat zachowania skryptu
* Jeśli próbujesz zasymulować krok potoku pośredniego, może być konieczne ręczne skompilowanie typów obiektów, których oczekuje określony skrypt od poprzedniego kroku
* Należy również zdefiniować własne środowisko i zreplikować zależności zdefiniowane w zdalnym środowisku obliczeniowym

Po uruchomieniu skryptu w środowisku lokalnym znacznie łatwiej jest wykonać zadania debugowania, takie jak:

* Dołączanie niestandardowej konfiguracji debugowania
* Wsłuchianie wykonywania i sprawdzanie stanu obiektu
* Przechwytując błędy typu lub logiczne, które nie zostaną ujawnione do czasu uruchomienia

> [!TIP] 
> Po sprawdzeniu, czy skrypt działa zgodnie z oczekiwaniami, dobrym następnym krokiem jest uruchomienie skryptu w potoku jednoetapowego przed podjęciem próby uruchomienia go w potoku z wieloma krokami.

## <a name="configure-write-to-and-review-pipeline-logs"></a>Konfigurowanie, zapis i przeglądanie dzienników potoku

Testowanie skryptów lokalnie jest doskonałym sposobem debugowania głównych fragmentów kodu i złożonej logiki przed rozpoczęciem tworzenia potoku, ale w pewnym momencie prawdopodobnie będzie konieczne debugowanie skryptów podczas samego uruchomienia potoku, szczególnie w przypadku diagnozowania zachowania, które występuje podczas interakcji między krokami potoku. Zalecamy używanie instrukcji w skryptach kroku, aby podczas zdalnego wykonywania można było zobaczyć stan obiektu i oczekiwane wartości, podobnie jak w przypadku `print()` debugowania kodu JavaScript.

### <a name="logging-options-and-behavior"></a>Opcje rejestrowania i zachowanie

W poniższej tabeli przedstawiono informacje dotyczące różnych opcji debugowania dla potoków. Nie jest to wyczerpująca lista, ponieważ istnieją inne opcje oprócz tych, Azure Machine Learning, Python i OpenCensus pokazane tutaj.

| Biblioteka                    | Typ   | Przykład                                                          | Element docelowy                                  | Zasoby                                                                                                                                                                                                                                                                                                                    |
|----------------------------|--------|------------------------------------------------------------------|----------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Azure Machine Learning SDK | Metric | `run.log(name, val)`                                             | Azure Machine Learning użytkownika portalu             | [Jak śledzić eksperymenty](how-to-log-view-metrics.md)<br>[azureml.core.Run, klasa](/python/api/azureml-core/azureml.core.run%28class%29)                                                                                                                                                 |
| Drukowanie/rejestrowanie w języku Python    | Dziennik    | `print(val)`<br>`logging.info(message)`                          | Dzienniki sterowników, Azure Machine Learning projektanta | [Jak śledzić eksperymenty](how-to-log-view-metrics.md)<br><br>[Rejestrowanie w języku Python](https://docs.python.org/2/library/logging.html)                                                                                                                                                                       |
| Zestaw OpenCensus Python          | Dziennik    | `logger.addHandler(AzureLogHandler())`<br>`logging.log(message)` | Application Insights — ślady                | [Debugowanie potoków w usłudze Application Insights](./how-to-log-pipelines-application-insights.md)<br><br>[Eksporterzy biblioteki OpenCensus usługi Azure Monitor](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-azure)<br>[Podręcznik rejestrowania języka Python](https://docs.python.org/3/howto/logging-cookbook.html) |

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

W przypadku potoków utworzonych w projektancie plik 70_driver_log **można** znaleźć na stronie tworzenia lub na stronie szczegółów uruchomienia potoku.

### <a name="enable-logging-for-real-time-endpoints"></a>Włączanie rejestrowania dla punktów końcowych w czasie rzeczywistym

Aby rozwiązywać problemy z punktami końcowymi w czasie rzeczywistym i debugować je w projektancie, należy włączyć rejestrowanie usługi Application Insights przy użyciu zestawu SDK. Rejestrowanie umożliwia rozwiązywanie i debugowanie problemów z wdrażaniem i użyciem modelu. Aby uzyskać więcej informacji, zobacz [Rejestrowanie dla wdrożonych modeli.](./how-to-enable-app-insights.md) 

### <a name="get-logs-from-the-authoring-page"></a>Uzyskiwanie dzienników ze strony tworzenia

Po przesłaniu uruchomienia potoku i pozostaniu na stronie tworzenia można znaleźć pliki dziennika wygenerowane dla każdego modułu po zakończeniu działania każdego modułu.

1. Wybierz moduł, który zakończył działanie na kanwie tworzenia.
1. W okienku po prawej stronie modułu przejdź do karty **Dane wyjściowe i dzienniki.**
1. Rozwiń prawe okienko, a następnie wybierz **70_driver_log.txt,** aby wyświetlić plik w przeglądarce. Dzienniki można również pobrać lokalnie.

    ![Rozwinięte okienko danych wyjściowych w projektancie](./media/how-to-debug-pipelines/designer-logs.png)

### <a name="get-logs-from-pipeline-runs"></a>Uzyskiwanie dzienników z przebiegów potoków

Pliki dziennika dla określonych przebiegów można również znaleźć na stronie szczegółów uruchomienia  potoku, która znajduje się w sekcji Potoki lub **Eksperymenty** w studio.

1. Wybierz uruchomienie potoku utworzone w projektancie.

    ![Strona uruchamiania potoku](./media/how-to-debug-pipelines/designer-pipelines.png)

1. Wybierz moduł w okienku podglądu.
1. W okienku po prawej stronie modułu przejdź do karty **Dane wyjściowe i dzienniki.**
1. Rozwiń prawe okienko, aby **wyświetlić70_driver_log.txt** w przeglądarce, lub wybierz plik, aby pobrać dzienniki lokalnie.

> [!IMPORTANT]
> Aby zaktualizować potok ze strony szczegółów uruchomienia potoku, należy **sklonować** uruchomienie potoku do nowej wersji roboczej potoku. Uruchomienie potoku to migawka potoku. Jest on podobny do pliku dziennika i nie można go zmienić. 

## <a name="application-insights"></a>Application Insights
Aby uzyskać więcej informacji na temat używania biblioteki OpenCensus w języku Python w ten sposób, zobacz ten przewodnik: [Debugowanie](./how-to-log-pipelines-application-insights.md) i rozwiązywanie problemów z potokami uczenia maszynowego w Application Insights

## <a name="interactive-debugging-with-visual-studio-code"></a>Debugowanie interakcyjne za pomocą Visual Studio Code

W niektórych przypadkach może być konieczne interaktywne debugowanie kodu języka Python używanego w potoku uczenia maszynowego. Za pomocą Visual Studio Code (VS Code) i debugpy można dołączyć do kodu podczas jego pracy w środowisku szkoleniowym. Aby uzyskać więcej informacji, odwiedź stronę [interaktywnego debugowania w VS Code przewodniku](how-to-debug-visual-studio-code.md#debug-and-troubleshoot-machine-learning-pipelines).

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać kompletny samouczek dotyczący korzystania z usługi `ParallelRunStep` , zobacz [Samouczek: tworzenie potoku Azure Machine Learning dla oceniania wsadowego.](tutorial-pipeline-batch-scoring-classification.md)

* Pełny przykład przedstawiający zautomatyzowane uczenie maszynowe w potokach uczenia maszynowego można znaleźć w temacie Use automated ML in an Azure Machine Learning pipeline in Python (Używanie zautomatyzowanego uczenia maszynowego w potoku uczenia maszynowego [w języku Python).](how-to-use-automlstep-in-pipelines.md)

* Zobacz informacje dotyczące zestawu SDK, aby uzyskać pomoc na temat [pakietu azureml-pipelines-core](/python/api/azureml-pipeline-core/) i [pakietu azureml-pipelines-steps.](/python/api/azureml-pipeline-steps/)

* Zobacz listę [wyjątków projektanta i kodów błędów](algorithm-module-reference/designer-error-codes.md).