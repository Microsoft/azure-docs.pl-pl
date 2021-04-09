---
title: Rozwiązywanie problemów z klasą ParallelRunStep
titleSuffix: Azure Machine Learning
description: Porady dotyczące rozwiązywania problemów w przypadku wystąpienia błędów przy użyciu ParallelRunStep w potokach uczenia maszynowego.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: troubleshooting
ms.custom: troubleshooting
ms.reviewer: larryfr, vaidyas, laobri, tracych
ms.author: trmccorm
author: tmccrmck
ms.date: 09/23/2020
ms.openlocfilehash: b5511c8ecc33238e0409b5ee4c1c7a11adddeac5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102522159"
---
# <a name="troubleshooting-the-parallelrunstep"></a>Rozwiązywanie problemów z klasą ParallelRunStep

W tym artykule dowiesz się, jak rozwiązywać problemy w przypadku wystąpienia błędów przy użyciu klasy [ParallelRunStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.parallel_run_step.parallelrunstep) z [zestawu SDK Azure Machine Learning](/python/api/overview/azure/ml/intro).

Ogólne porady dotyczące rozwiązywania problemów z potokiem można znaleźć w temacie [Rozwiązywanie problemów z potokami uczenia maszynowego](how-to-debug-pipelines.md).

## <a name="testing-scripts-locally"></a>Lokalne testowanie skryptów

 ParallelRunStep działa jako krok w potokach ML. Możesz chcieć [przetestować skrypty lokalnie](how-to-debug-visual-studio-code.md#debug-and-troubleshoot-machine-learning-pipelines) jako pierwszy krok.

##  <a name="script-requirements"></a>Wymagania dotyczące skryptu

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
- `output`: `OutputFileDatasetConfig` Obiekt reprezentujący ścieżkę do katalogu, w którym będą przechowywane dane wyjściowe.
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

## <a name="debugging-scripts-from-remote-context"></a>Debugowanie skryptów z kontekstu zdalnego

Przejście od debugowania skryptu oceniania lokalnie w celu debugowania skryptu oceniania w rzeczywistym potoku może być trudne. Aby uzyskać informacje dotyczące znajdowania dzienników w portalu, zobacz  [sekcję potoki uczenia maszynowego w przypadku debugowania skryptów z kontekstu zdalnego](how-to-debug-pipelines.md). Informacje zawarte w tej sekcji dotyczą również ParallelRunStep.

Na przykład plik dziennika `70_driver_log.txt` zawiera informacje z kontrolera, który uruchamia kod ParallelRunStep.

Ze względu na dystrybuowany charakter zadań ParallelRunStep istnieją dzienniki z kilku różnych źródeł. Jednak tworzone są dwa skonsolidowane pliki, które udostępniają informacje wysokiego poziomu:

- `~/logs/job_progress_overview.txt`: Ten plik zawiera ogólne informacje o liczbie pośrednich partii (nazywanych również zadaniami) utworzonych do tej pory oraz liczbę partii, które zostały przetworzone do tej pory. Na tym końcu zostanie wyświetlony wynik zadania. Jeśli zadanie nie powiodło się, zostanie wyświetlony komunikat o błędzie i miejsce, w którym należy rozpocząć rozwiązywanie problemów.

- `~/logs/sys/master_role.txt`: Ten plik udostępnia węzeł główny (znany również jako koordynator) w uruchomionym zadaniu. Obejmuje tworzenie zadań, monitorowanie postępu, wynik uruchomienia.

Dzienniki wygenerowane ze skryptu wprowadzania przy użyciu pomocnika EntryScript i instrukcje Print są dostępne w następujących plikach:

- `~/logs/user/entry_script_log/<ip_address>/<process_name>.log.txt`: Te pliki są dziennikami zapisanymi na podstawie entry_script przy użyciu pomocnika EntryScript.

- `~/logs/user/stdout/<ip_address>/<process_name>.stdout.txt`: Te pliki są dziennikami z strumienia stdout (np. Print statement) entry_script.

- `~/logs/user/stderr/<ip_address>/<process_name>.stderr.txt`: Te pliki są dziennikami z stderr dla entry_script.

Zwięzłe zrozumienie błędów w skrypcie:

- `~/logs/user/error.txt`: Ten plik podejmie próbę podsumowania błędów w skrypcie.

Aby uzyskać więcej informacji o błędach w skrypcie, istnieje:

- `~/logs/user/error/`: Zawiera pełne ślady stosu dla wyjątków zgłoszonych podczas ładowania i uruchamiania skryptu wprowadzania.

Jeśli potrzebujesz pełnego zrozumieć, jak każdy węzeł wykonał skrypt wynikowy, sprawdź poszczególne dzienniki procesów dla każdego węzła. Dzienniki procesów można znaleźć w `sys/node` folderze pogrupowanym według węzłów procesu roboczego:

- `~/logs/sys/node/<ip_address>/<process_name>.txt`: Ten plik zawiera szczegółowe informacje o każdej z grup minimalnej, które są pobierane lub wykonywane przez proces roboczy. Dla każdej grupy mini-Batch ten plik zawiera:

    - Adres IP i Identyfikator PID procesu roboczego. 
    - Całkowita liczba elementów, liczba pomyślnie przetworzonych elementów i liczba elementów zakończonych niepowodzeniem.
    - Czas rozpoczęcia, czas trwania, czas procesu i Metoda uruchomienia.

Można również wyświetlić wyniki okresowych kontroli użycia zasobów dla każdego węzła. Pliki dziennika i pliki instalacyjne znajdują się w tym folderze:

- `~/logs/perf`: Ustaw `--resource_monitor_interval` , aby zmienić interwał sprawdzania w sekundach. Domyślny interwał to `600` , czyli około 10 minut. Aby zatrzymać monitorowanie, ustaw wartość na `0` . Każdy `<ip_address>` folder zawiera:

    - `os/`: Informacje o wszystkich uruchomionych procesach w węźle. Jedno sprawdzenie uruchamia polecenie systemu operacyjnego i zapisuje wynik do pliku. W systemie Linux polecenie to `ps` . W systemie Windows Użyj `tasklist` .
        - `%Y%m%d%H`: Nazwa folderu podrzędnego to godzina do godziny.
            - `processes_%M`: Plik zostaje zakończony na minutę czasu sprawdzania.
    - `node_disk_usage.csv`: Szczegóły użycia dysku w węźle.
    - `node_resource_usage.csv`: Przegląd użycia zasobów węzła.
    - `processes_resource_usage.csv`: Przegląd użycia zasobów dla każdego procesu.

### <a name="how-do-i-log-from-my-user-script-from-a-remote-context"></a>Jak mogę dziennika ze skryptu użytkownika ze zdalnego kontekstu?

ParallelRunStep może uruchamiać wiele procesów na jednym węźle na podstawie process_count_per_node. Aby zorganizować dzienniki z każdego procesu w węźle i połączyć je z instrukcją Print i log, zalecamy użycie rejestratora ParallelRunStep, jak pokazano poniżej. Uzyskasz Rejestrator z usługi EntryScript i Wyrejestruj dzienniki w folderze **Logs/User** w portalu.

**Przykładowy skrypt wprowadzania przy użyciu rejestratora:**
```python
from azureml_user.parallel_run import EntryScript

def init():
    """ Initialize the node."""
    entry_script = EntryScript()
    logger = entry_script.logger
    logger.debug("This will show up in files under logs/user on the Azure portal.")


def run(mini_batch):
    """ Accept and return the list back."""
    # This class is in singleton pattern and will return same instance as the one in init()
    entry_script = EntryScript()
    logger = entry_script.logger
    logger.debug(f"{__file__}: {mini_batch}.")
    ...

    return mini_batch
```

### <a name="how-could-i-pass-a-side-input-such-as-a-file-or-files-containing-a-lookup-table-to-all-my-workers"></a>Jak można przekazać dane wejściowe po stronie, takie jak plik lub pliki zawierające tabelę odnośników, do wszystkich pracowników?

Użytkownik może przekazać dane referencyjne do skryptu przy użyciu parametru side_inputs ParalleRunStep. Wszystkie zestawy danych podane jako side_inputs zostaną zainstalowane na każdym węźle procesu roboczego. Użytkownik może uzyskać lokalizację instalacji, przekazując argument.

Utwórz [zestaw](/python/api/azureml-core/azureml.core.dataset.dataset) danych zawierający dane referencyjne i zarejestruj go w obszarze roboczym. Przekaż go do `side_inputs` parametru `ParallelRunStep` . Ponadto możesz dodać swoją ścieżkę w `arguments` sekcji, aby łatwo uzyskać dostęp do jej zainstalowanej ścieżki:

```python
label_config = label_ds.as_named_input("labels_input")
batch_score_step = ParallelRunStep(
    name=parallel_step_name,
    inputs=[input_images.as_named_input("input_images")],
    output=output_dir,
    arguments=["--labels_dir", label_config],
    side_inputs=[label_config],
    parallel_run_config=parallel_run_config,
)
```

Po uzyskaniu dostępu do niego w skrypcie wnioskowania (na przykład w metodzie init ()) w następujący sposób:

```python
parser = argparse.ArgumentParser()
parser.add_argument('--labels_dir', dest="labels_dir", required=True)
args, _ = parser.parse_known_args()

labels_path = args.labels_dir
```

### <a name="how-to-use-input-datasets-with-service-principal-authentication"></a>Jak używać wejściowych zestawów danych z uwierzytelnianiem jednostki usługi?

Użytkownik może przekazać wejściowe zestawy danych z uwierzytelnianiem jednostki usługi używanym w obszarze roboczym. Użycie takiego zestawu danych w ParallelRunStep wymaga zarejestrowania zestawu danych w celu skonstruowania konfiguracji ParallelRunStep.

```python
service_principal = ServicePrincipalAuthentication(
    tenant_id="***",
    service_principal_id="***",
    service_principal_password="***")
 
ws = Workspace(
    subscription_id="***",
    resource_group="***",
    workspace_name="***",
    auth=service_principal
    )
 
default_blob_store = ws.get_default_datastore() # or Datastore(ws, '***datastore-name***') 
ds = Dataset.File.from_files(default_blob_store, '**path***')
registered_ds = ds.register(ws, '***dataset-name***', create_new_version=True)
```

## <a name="next-steps"></a>Następne kroki

* Zobacz te [notesy Jupyter pokazujące potoki Azure Machine Learning](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/machine-learning-pipelines)

* Zapoznaj się z dokumentacją zestawu SDK, aby uzyskać pomoc dotyczącą pakietu [Azure-Pipeline-etaps](/python/api/azureml-pipeline-steps/azureml.pipeline.steps) . Wyświetl [dokumentację](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.parallelrunstep) referencyjną dla klasy ParallelRunStep.

* Postępuj zgodnie z [zaawansowanym samouczkiem](tutorial-pipeline-batch-scoring-classification.md) dotyczącym używania potoków z ParallelRunStep. W tym samouczku pokazano, jak przekazać inny plik jako dane wejściowe.
