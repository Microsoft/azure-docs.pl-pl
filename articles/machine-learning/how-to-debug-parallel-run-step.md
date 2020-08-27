---
title: Debugowanie i rozwiązywanie problemów z ParallelRunStep
titleSuffix: Azure Machine Learning
description: Debugowanie i rozwiązywanie problemów z ParallelRunStep w potokach uczenia maszynowego w zestawie SDK Azure Machine Learning dla języka Python. Poznaj typowe pułapek na potrzeby programowania przy użyciu potoków oraz porady ułatwiające debugowanie skryptów przed i podczas wykonywania zdalnego.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: troubleshooting
ms.reviewer: jmartens, larryfr, vaidyas, laobri, tracych
ms.author: trmccorm
author: tmccrmck
ms.date: 07/16/2020
ms.openlocfilehash: 0b914059567e9a513b605ce733407465b625a909
ms.sourcegitcommit: e69bb334ea7e81d49530ebd6c2d3a3a8fa9775c9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/27/2020
ms.locfileid: "88950021"
---
# <a name="debug-and-troubleshoot-parallelrunstep"></a>Debugowanie i rozwiązywanie problemów z ParallelRunStep
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

W tym artykule dowiesz się, jak debugować i rozwiązywać problemy z klasą [ParallelRunStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.parallel_run_step.parallelrunstep?view=azure-ml-py) z poziomu [zestawu SDK Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).

## <a name="testing-scripts-locally"></a>Lokalne testowanie skryptów

Zobacz [sekcję testowanie skryptów lokalnie](how-to-debug-pipelines.md#debug-scripts-locally) dla potoków uczenia maszynowego. ParallelRunStep działa jako krok w potokach ML, dlatego ta sama odpowiedź ma zastosowanie do obu tych metod.

## <a name="debugging-scripts-from-remote-context"></a>Debugowanie skryptów z kontekstu zdalnego

Przejście od debugowania skryptu oceniania lokalnie w celu debugowania skryptu oceniania w rzeczywistym potoku może być trudne. Aby uzyskać informacje na temat wyszukiwania dzienników w portalu, należy zapoznać się z [sekcją potoki uczenia maszynowego w przypadku debugowania skryptów z kontekstu zdalnego](how-to-debug-pipelines.md#finding-and-reading-pipeline-log-files). Informacje zawarte w tej sekcji dotyczą również ParallelRunStep.

Na przykład plik dziennika `70_driver_log.txt` zawiera informacje z kontrolera, który uruchamia kod ParallelRunStep.

Ze względu na dystrybuowany charakter zadań ParallelRunStep istnieją dzienniki z kilku różnych źródeł. Jednak tworzone są dwa skonsolidowane pliki, które udostępniają informacje wysokiego poziomu:

- `~/logs/overview.txt`: Ten plik zawiera ogólne informacje o liczbie pośrednich partii (nazywanych również zadaniami) utworzonych do tej pory oraz liczbę partii, które zostały przetworzone do tej pory. Na tym końcu zostanie wyświetlony wynik zadania. Jeśli zadanie nie powiodło się, zostanie wyświetlony komunikat o błędzie i miejsce, w którym należy rozpocząć rozwiązywanie problemów.

- `~/logs/sys/master.txt`: Ten plik udostępnia węzeł główny (znany również jako koordynator) w uruchomionym zadaniu. Obejmuje tworzenie zadań, monitorowanie postępu, wynik uruchomienia.

Dzienniki wygenerowane ze skryptu wprowadzania przy użyciu pomocnika EntryScript i instrukcje Print są dostępne w następujących plikach:

- `~/logs/user/<ip_address>/<node_name>.log.txt`: Te pliki są dziennikami zapisanymi na podstawie entry_script przy użyciu pomocnika EntryScript. Zawiera również instrukcję Print Statement (stdout) z entry_script.

Zwięzłe zrozumienie błędów w skrypcie:

- `~/logs/user/error.txt`: Ten plik podejmie próbę podsumowania błędów w skrypcie.

Aby uzyskać więcej informacji o błędach w skrypcie, istnieje:

- `~/logs/user/error/`: Zawiera wszystkie zgłoszone błędy i pełne ślady stosu uporządkowane według węzła.

Jeśli potrzebujesz pełnego zrozumieć, jak każdy węzeł wykonał skrypt wynikowy, sprawdź poszczególne dzienniki procesów dla każdego węzła. Dzienniki procesów można znaleźć w `sys/node` folderze pogrupowanym według węzłów procesu roboczego:

- `~/logs/sys/node/<node_name>.txt`: Ten plik zawiera szczegółowe informacje o każdej z grup minimalnej, które są pobierane lub wykonywane przez proces roboczy. Dla każdej grupy mini-Batch ten plik zawiera:

    - Adres IP i Identyfikator PID procesu roboczego. 
    - Całkowita liczba elementów, liczba pomyślnie przetworzonych elementów i liczba elementów zakończonych niepowodzeniem.
    - Czas rozpoczęcia, czas trwania, czas procesu i Metoda uruchomienia.

Możesz również znaleźć informacje o użyciu zasobów procesów dla każdego pracownika. Te informacje są w formacie CSV i znajdują się w lokalizacji `~/logs/sys/perf/overview.csv` . Informacje o każdym procesie są dostępne w sekcji `~logs/sys/processes.csv` .

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

Utwórz [zestaw](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py) danych zawierający dane referencyjne i zarejestruj go w obszarze roboczym. Przekaż go do `side_inputs` parametru `ParallelRunStep` . Ponadto możesz dodać swoją ścieżkę w `arguments` sekcji, aby łatwo uzyskać dostęp do jej zainstalowanej ścieżki:

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

## <a name="next-steps"></a>Kolejne kroki

* Zapoznaj się z dokumentacją zestawu SDK, aby uzyskać pomoc dotyczącą pakietu [Azure-Pipeline-etaps](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps?view=azure-ml-py) . Wyświetl [dokumentację](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.parallelrunstep?view=azure-ml-py) referencyjną dla klasy ParallelRunStep.

* Postępuj zgodnie z [zaawansowanym samouczkiem](tutorial-pipeline-batch-scoring-classification.md) dotyczącym używania potoków z ParallelRunStep. W tym samouczku pokazano, jak przekazać inny plik jako dane wejściowe. 
