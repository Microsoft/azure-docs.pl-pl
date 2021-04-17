---
title: Gdzie zapisać plik & zapisu plików eksperymentu
titleSuffix: Azure Machine Learning
description: Dowiedz się, gdzie zapisać pliki wejściowe i wyjściowe, aby zapobiec błędom ograniczania magazynu i opóźnieniu eksperymentów.
services: machine-learning
author: rastala
ms.author: roastala
manager: danielsc
ms.reviewer: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.date: 03/10/2020
ms.openlocfilehash: 59955b291ce706a77d0dd5ab052809fe725166d9
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107387891"
---
# <a name="where-to-save-and-write-files-for-azure-machine-learning-experiments"></a>Gdzie zapisywać i zapisywać pliki na Azure Machine Learning eksperymentów


W tym artykule dowiesz się, gdzie zapisywać pliki wejściowe i gdzie zapisywać pliki wyjściowe z eksperymentów, aby zapobiec błędom limitu magazynu i opóźnieniu eksperymentów.

Podczas uruchamiania przebiegów trenowania w docelowym [środowisku obliczeniowym](concept-compute-target.md)są one odizolowane od środowisk zewnętrznych. Celem tego projektu jest zapewnienie powtarzalności i przenośności eksperymentu. Jeśli ten sam skrypt zostanie uruchomiony dwa razy w tym samym lub innym docelowym zamierzeń obliczeniowych, otrzymasz te same wyniki. Dzięki temu projektowi można traktować docelowe obiekty obliczeniowe jako bez stanowe zasoby obliczeniowe, z których każdy nie ma koligacji z zadaniami uruchomionymi po zakończeniu.

## <a name="where-to-save-input-files"></a>Gdzie zapisywać pliki wejściowe

Zanim będzie można zainicjować eksperyment na docelowym obiektem obliczeniowym lub komputerze lokalnym, należy upewnić się, że wymagane pliki są dostępne dla tego docelowego obiektu obliczeniowego, takie jak pliki zależności i pliki danych, które należy uruchomić w kodzie.

Azure Machine Learning uruchamia skrypty trenowania, kopiując cały katalog źródłowy. Jeśli masz poufne dane, których nie chcesz przekazywać, użyj pliku [.ignore](how-to-save-write-experiment-files.md#storage-limits-of-experiment-snapshots) lub nie dołączaj go do katalogu źródłowego. Zamiast tego należy uzyskać dostęp do danych przy użyciu [magazynu danych](/python/api/azureml-core/azureml.data).

Limit magazynu migawek eksperymentu wynosi 300 MB i/lub 2000 plików.

Z tego powodu zalecamy:

* **Przechowywanie plików w zestawie danych Azure Machine Learning [danych](/python/api/azureml-core/azureml.data).** Zapobiega to problemom z opóźnieniami eksperymentów i ma zalety uzyskiwania dostępu do danych ze zdalnego docelowego obiektu obliczeniowego, co oznacza, że uwierzytelnianie i instalowanie są zarządzane przez Azure Machine Learning. Dowiedz się więcej na temat określania zestawu danych jako źródła danych wejściowych w skrypcie trenowania za pomocą polecenia [Train with datasets (Trenuj przy użyciu zestawów danych).](how-to-train-with-datasets.md)

* **Jeśli potrzebujesz tylko kilku** plików danych i skryptów zależności i nie możesz użyć magazynu danych, umieść pliki w tym samym katalogu folderu, w którym znajduje się skrypt szkoleniowy. Określ ten folder jako `source_directory` swój bezpośrednio w skrypcie trenowania lub w kodzie, który wywołuje skrypt trenowania.

<a name="limits"></a>

### <a name="storage-limits-of-experiment-snapshots"></a>Limity magazynu migawek eksperymentów

W przypadku Azure Machine Learning automatycznie tworzy migawkę eksperymentu kodu na podstawie katalogu sugerowanego podczas konfigurowania uruchomienia. Łączny limit wynosi 300 MB i/lub 2000 plików. W przypadku przekroczenia tego limitu zostanie wyświetlony następujący błąd:

```Python
While attempting to take snapshot of .
Your total snapshot size exceeds the limit of 300.0 MB
```

Aby usunąć ten błąd, przechowuj pliki eksperymentu w magazynie danych. Jeśli nie możesz użyć magazynu danych, w poniższej tabeli przedstawiono możliwe alternatywne rozwiązania.

Opis &nbsp; eksperymentu|Rozwiązanie dotyczące limitu magazynu
---|---
Mniej niż 2000 & nie może używać magazynu danych| Zastąp limit rozmiaru migawki za pomocą <br> `azureml._restclient.snapshots_client.SNAPSHOT_MAX_SIZE_BYTES = 'insert_desired_size'`<br> Może to potrwać kilka minut w zależności od liczby i rozmiaru plików.
Musi używać określonego katalogu skryptu| [!INCLUDE [amlinclude-info](../../includes/machine-learning-amlignore-gitignore.md)]
Potok|Użyj innego podkatalogu dla każdego kroku
Notesy programu Jupyter| Utwórz plik lub przenieś notes do nowego, pustego `.amlignore` podkatalogu i ponownie uruchom kod.

## <a name="where-to-write-files"></a>Gdzie zapisywać pliki

Ze względu na izolację eksperymentów szkoleniowych zmiany w plikach, które mają miejsce podczas przebiegów, nie muszą być utrwalane poza środowiskiem. Jeśli skrypt modyfikuje pliki lokalne do obliczeń, zmiany nie są utrwalane dla następnego uruchomienia eksperymentu i nie są automatycznie propagowane z powrotem do maszyny klienckiej. W związku z tym zmiany wprowadzone podczas pierwszego uruchomienia eksperymentu nie mają i nie powinny mieć wpływu na te w drugim.

Podczas zapisywania zmian zalecamy zapisywanie plików w magazynie za pośrednictwem Azure Machine Learning danych z [obiektem OutputFileDatasetConfig](/python/api/azureml-core/azureml.data.output_dataset_config.outputfiledatasetconfig). Zobacz, [jak utworzyć plik OutputFileDatasetConfig.](how-to-train-with-datasets.md#where-to-write-training-output)

W przeciwnym razie zapisz pliki w `./outputs` folderze `./logs` i/lub .

>[!Important]
> Dwa foldery, *dane wyjściowe* i *dzienniki*, otrzymują specjalne traktowanie przez Azure Machine Learning. Podczas trenowania, gdy zapisujesz pliki w folderach i , pliki zostaną automatycznie przesłane do historii uruchamiania, dzięki czemu będziesz mieć do nich dostęp po `./outputs` `./logs` zakończeniu uruchamiania.

* **W przypadku danych wyjściowych,** takich jak komunikaty o stanie lub wyniki oceniania, zapisz pliki w folderze, aby były one utrwalane jako `./outputs` artefakty w historii uruchamiania. Należy pamiętać o liczbie i rozmiarze plików zapisywanych w tym folderze, ponieważ może wystąpić opóźnienie podczas przesłania zawartości do historii uruchamiania. Jeśli opóźnienie jest problemem, zaleca się zapisywanie plików w magazynze danych.

* **Aby zapisać zapisany plik jako dzienniki w historii uruchamiania,** zapisz pliki w `./logs` folderze. Dzienniki są przekazywane w czasie rzeczywistym, więc ta metoda jest odpowiednia do przesyłania strumieniowego aktualizacji na żywo z uruchomienia zdalnego.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [uzyskiwaniu dostępu do danych z magazynu](how-to-access-data.md).

* Dowiedz się więcej o [tworzeniu docelowych obiektów obliczeniowych do trenowania i wdrażania modelu](how-to-create-attach-compute-studio.md)