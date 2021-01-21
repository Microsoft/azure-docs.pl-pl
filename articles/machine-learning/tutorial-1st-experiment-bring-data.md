---
title: 'Samouczek: korzystanie z własnych danych'
titleSuffix: Azure Machine Learning
description: Część 4 serii Azure Machine Learning Get-Started pokazuje, jak korzystać z własnych danych w ramach zdalnego treningu szkoleniowego.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: aminsaied
ms.author: amsaied
ms.reviewer: sgilley
ms.date: 09/15/2020
ms.custom: tracking-python
ms.openlocfilehash: c08d9ee6704203d0634d7a1b90a57de9c6a99d31
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/21/2021
ms.locfileid: "98622798"
---
# <a name="tutorial-use-your-own-data-part-4-of-4"></a>Samouczek: korzystanie z własnych danych (część 4 z 4)

W tym samouczku przedstawiono sposób przekazywania i używania własnych danych w celu uczenia modeli uczenia maszynowego w Azure Machine Learning.

Ten samouczek jest *częścią 4 wieloczęściowej serii samouczków* , w której przedstawiono podstawowe informacje na temat Azure Machine Learning i zakończenia zadań uczenia maszynowego opartych na zadaniach na platformie Azure. Ten samouczek kompiluje pracę wykonaną w [części 1: Konfiguracja](tutorial-1st-experiment-sdk-setup-local.md), [część 2: uruchomienie "Hello World!"](tutorial-1st-experiment-hello-world.md), a [część 3: uczenie modelu](tutorial-1st-experiment-sdk-train.md).

W [części 3: uczenie modelu](tutorial-1st-experiment-sdk-train.md)dane zostały pobrane za pomocą metody wbudowanej `torchvision.datasets.CIFAR10` w interfejsie API PyTorch. Jednak w wielu przypadkach chcesz użyć własnych danych w zdalnym przebiegu szkoleniowym. W tym artykule przedstawiono przepływ pracy, którego można użyć do pracy ze swoimi danymi w Azure Machine Learning.

W tym samouczku zostały wykonane następujące czynności:

> [!div class="checklist"]
> * Skonfiguruj skrypt szkoleniowy do korzystania z danych w katalogu lokalnym.
> * Przetestuj skrypt szkoleniowy lokalnie.
> * Przekazywanie danych na platformę Azure.
> * Utwórz skrypt kontrolny.
> * Poznaj nowe koncepcje Azure Machine Learning (przekazywanie parametrów, zestawów danych i magazynów).
> * Prześlij i uruchom skrypt szkoleniowy.
> * Wyświetlanie danych wyjściowych kodu w chmurze.

## <a name="prerequisites"></a>Wymagania wstępne

* Zakończenie [części 3](tutorial-1st-experiment-sdk-train.md) serii.

## <a name="adjust-the-training-script"></a>Dostosuj skrypt szkoleniowy

Teraz masz skrypt szkoleniowy (samouczek/src/uczenie. PR) działający w Azure Machine Learning i można monitorować wydajność modelu. Sparametryzujmy skrypt szkoleniowy, wprowadzając argumenty. Argumenty using umożliwiają łatwe porównywanie różnych parametrów.

Nasz skrypt szkoleniowy jest teraz ustawiany do pobierania zestawu danych CIFAR10 w każdym uruchomieniu. Następujący kod języka Python został dostosowany w celu odczytania danych z katalogu.

>[!NOTE] 
> Użycie `argparse` skryptu parameterizes.

:::code language="python" source="~/MachineLearningNotebooks/tutorials/get-started-day1/code/pytorch-cifar10-your-data/train.py":::

### <a name="understanding-the-code-changes"></a>Informacje o zmianach w kodzie

Kod w programie `train.py` używa `argparse` biblioteki do konfigurowania `data_path` , `learning_rate` , i `momentum` .

```python
# .... other code
parser = argparse.ArgumentParser()
parser.add_argument('--data_path', type=str, help='Path to the training data')
parser.add_argument('--learning_rate', type=float, default=0.001, help='Learning rate for SGD')
parser.add_argument('--momentum', type=float, default=0.9, help='Momentum for SGD')
args = parser.parse_args()
# ... other code
```

Ponadto `train.py` skrypt został dostosowany w celu zaktualizowania Optymalizatora, aby korzystał z parametrów zdefiniowanych przez użytkownika:

```python
optimizer = optim.SGD(
    net.parameters(),
    lr=args.learning_rate,     # get learning rate from command-line argument
    momentum=args.momentum,    # get momentum from command-line argument
)
```
> [!div class="nextstepaction"]
> [Został dostosowany skrypt szkoleniowy, w którym](?success=adjust-training-script#test-locally) [Wystąpił problem](https://www.research.net/r/7C6W7BQ?issue=adjust-training-script)

## <a name="test-the-script-locally"></a><a name="test-locally"></a> Lokalne testowanie skryptu

Skrypt teraz akceptuje _ścieżkę danych_ jako argument. Aby rozpocząć pracę z programem, przetestuj go lokalnie. Dodaj do swojej struktury katalogów samouczka folderu o nazwie `data` . Struktura katalogów powinna wyglądać następująco:

```txt
tutorial
└──.azureml
|  └──config.json
|  └──pytorch-env.yml
└──data
└──src
|  └──hello.py
|  └──model.py
|  └──train.py
└──01-create-workspace.py
└──02-create-compute.py
└──03-run-hello.py
└──04-run-pytorch.py
```

Jeśli nie uruchomiono `train.py` lokalnie w poprzednim samouczku, nie będziesz mieć `data/` katalogu. W takim przypadku należy uruchomić `torchvision.datasets.CIFAR10` metodę lokalnie `download=True` w `train.py` skrypcie.

Ponadto, aby uruchomić program na komputerze lokalnym, upewnij się, że zamknięto środowisko samouczka i aktywuj nowe środowisko Conda:

```bash
conda deactivate                # If you are still using the tutorial environment, exit it
```

```bash
conda env create -f .azureml/pytorch-env.yml    # create the new conda environment with updated dependencies
```

```bash
conda activate pytorch-aml-env          # activate new conda environment
```

Aby uruchomić zmodyfikowany skrypt szkoleniowy lokalnie, wywołaj:

```bash
python src/train.py --data_path ./data --learning_rate 0.003 --momentum 0.92
```

Należy unikać pobierania zestawu danych CIFAR10, przekazując ścieżkę lokalną do danych. Możesz również eksperymentować z różnymi wartościami dla _współczynnika uczenia_ _i niedopełnienia, bez_ konieczności podawania kodu w skrypcie szkoleniowym.

> [!div class="nextstepaction"]
> [Skrypt został pomyślnie przetestowany](?success=test-locally#upload) [w ramach problemu](https://www.research.net/r/7C6W7BQ?issue=test-locally)

## <a name="upload-the-data-to-azure"></a><a name="upload"></a> Przekazywanie danych na platformę Azure

Aby uruchomić ten skrypt w Azure Machine Learning, musisz udostępnić dane szkoleniowe na platformie Azure. Obszar roboczy Azure Machine Learning jest dostępny z _domyślnym_ magazynem danych. To jest konto usługi Azure Blob Storage, w którym można przechowywać dane szkoleniowe.

>[!NOTE] 
> Azure Machine Learning umożliwia łączenie innych magazynów danych opartych na chmurze, które przechowują dane. Aby uzyskać więcej informacji, zobacz [dokumentację magazynów](./concept-data.md)danych.  

Utwórz nowy skrypt kontrolki języka Python o nazwie `05-upload-data.py` w `tutorial` katalogu:

:::code language="python" source="~/MachineLearningNotebooks/tutorials/get-started-day1/IDE-users/05-upload-data.py":::

`target_path`Wartość określa ścieżkę do magazynu danych, w którym zostaną przekazane dane CIFAR10.

>[!TIP] 
> Gdy używasz Azure Machine Learning do przekazywania danych, możesz użyć [Eksplorator usługi Azure Storage](https://azure.microsoft.com/features/storage-explorer/) do przekazywania plików ad hoc. Jeśli potrzebujesz narzędzia ETL, możesz użyć [Azure Data Factory](../data-factory/introduction.md) do pozyskiwania danych na platformie Azure.

W oknie, które ma aktywowane środowisko *tutorial1* Conda, uruchom plik Python w celu przekazania danych. (Przekazywanie powinno być szybkie, mniejsze niż 60 sekund).

```bash
python 05-upload-data.py
```

Powinny zostać wyświetlone następujące standardowe dane wyjściowe:

```txt
Uploading ./data\cifar-10-batches-py\data_batch_2
Uploaded ./data\cifar-10-batches-py\data_batch_2, 4 files out of an estimated total of 9
.
.
Uploading ./data\cifar-10-batches-py\data_batch_5
Uploaded ./data\cifar-10-batches-py\data_batch_5, 9 files out of an estimated total of 9
Uploaded 9 files
```

> [!div class="nextstepaction"]
> Pomyślnie [przekazano dane,](?success=upload-data#control-script) których dotyczy [problem](https://www.research.net/r/7C6W7BQ?issue=upload-data)

## <a name="create-a-control-script"></a><a name="control-script"></a> Utwórz skrypt kontrolny

Tak jak wcześniej, Utwórz nowy skrypt kontrolki języka Python o nazwie `06-run-pytorch-data.py` :

```python
# 06-run-pytorch-data.py
from azureml.core import Workspace
from azureml.core import Experiment
from azureml.core import Environment
from azureml.core import ScriptRunConfig
from azureml.core import Dataset

if __name__ == "__main__":
    ws = Workspace.from_config()
    datastore = ws.get_default_datastore()
    dataset = Dataset.File.from_files(path=(datastore, 'datasets/cifar10'))

    experiment = Experiment(workspace=ws, name='day1-experiment-data')

    config = ScriptRunConfig(
        source_directory='./src',
        script='train.py',
        compute_target='cpu-cluster',
        arguments=[
            '--data_path', dataset.as_named_input('input').as_mount(),
            '--learning_rate', 0.003,
            '--momentum', 0.92],
    )
    # set up pytorch environment
    env = Environment.from_conda_specification(
        name='pytorch-env',
        file_path='./.azureml/pytorch-env.yml'
    )
    config.run_config.environment = env

    run = experiment.submit(config)
    aml_url = run.get_portal_url()
    print("Submitted to compute cluster. Click link below")
    print("")
    print(aml_url)
```

### <a name="understand-the-code-changes"></a>Zrozumienie zmian w kodzie

Skrypt kontrolny jest podobny do jednego z [części 3 tej serii](tutorial-1st-experiment-sdk-train.md), z następującymi nowymi wierszami:

:::row:::
   :::column span="":::
      `dataset = Dataset.File.from_files( ... )`
   :::column-end:::
   :::column span="2":::
      [Zestaw danych](/python/api/azureml-core/azureml.core.dataset.dataset?preserve-view=true&view=azure-ml-py) służy do odwoływania się do danych przekazanych do usługi Azure Blob Storage. Zbiory danych są warstwą abstrakcji na podstawie dane, które są przeznaczone do poprawy niezawodności i wiarygodności.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      `config = ScriptRunConfig(...)`
   :::column-end:::
   :::column span="2":::
      [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig?preserve-view=true&view=azure-ml-py) jest modyfikowany w celu uwzględnienia listy argumentów, do których zostanie przekazane `train.py` . `dataset.as_named_input('input').as_mount()`Argument oznacza, że określony katalog zostanie _zainstalowany_ do elementu docelowego obliczeń.
   :::column-end:::
:::row-end:::

> [!div class="nextstepaction"]
> [Utworzono skrypt kontrolny,](?success=control-script#submit-to-cloud) [w którym wystąpił problem](https://www.research.net/r/7C6W7BQ?issue=control-script)

## <a name="submit-the-run-to-azure-machine-learning"></a><a name="submit-to-cloud"></a> Prześlij przebieg do Azure Machine Learning

Teraz ponownie prześlij przebieg, aby użyć nowej konfiguracji:

```bash
python 06-run-pytorch-data.py
```

Ten kod będzie drukował adres URL do eksperymentu w Azure Machine Learning Studio. Jeśli przejdziesz do tego linku, będzie można zobaczyć, że Twój kod jest uruchomiony.

> [!div class="nextstepaction"]
> Pomyślnie [przesłano przebieg](?success=submit-to-cloud#inspect-log) [problemu](https://www.research.net/r/7C6W7BQ?issue=submit-to-cloud)

### <a name="inspect-the-log-file"></a><a name="inspect-log"></a> Inspekcja pliku dziennika

W programie Studio przejdź do przebiegu eksperymentu (wybierając poprzednie adresy URL), a następnie pozycję Output **+ Logs**. Wybierz `70_driver_log.txt` plik. Powinny zostać wyświetlone następujące dane wyjściowe:

```txt
Processing 'input'.
Processing dataset FileDataset
{
  "source": [
    "('workspaceblobstore', 'datasets/cifar10')"
  ],
  "definition": [
    "GetDatastoreFiles"
  ],
  "registration": {
    "id": "XXXXX",
    "name": null,
    "version": null,
    "workspace": "Workspace.create(name='XXXX', subscription_id='XXXX', resource_group='X')"
  }
}
Mounting input to /tmp/tmp9kituvp3.
Mounted input to /tmp/tmp9kituvp3 as folder.
Exit __enter__ of DatasetContextManager
Entering Run History Context Manager.
Current directory:  /mnt/batch/tasks/shared/LS_root/jobs/dsvm-aml/azureml/tutorial-session-3_1600171983_763c5381/mounts/workspaceblobstore/azureml/tutorial-session-3_1600171983_763c5381
Preparing to call script [ train.py ] with arguments: ['--data_path', '$input', '--learning_rate', '0.003', '--momentum', '0.92']
After variable expansion, calling script [ train.py ] with arguments: ['--data_path', '/tmp/tmp9kituvp3', '--learning_rate', '0.003', '--momentum', '0.92']

Script type = None
===== DATA =====
DATA PATH: /tmp/tmp9kituvp3
LIST FILES IN DATA PATH...
['cifar-10-batches-py', 'cifar-10-python.tar.gz']
```

Wzory

- Azure Machine Learning automatycznie zainstalował Blob Storage do klastra obliczeniowego.
- ``dataset.as_named_input('input').as_mount()``Użycie w skrypcie kontrolnym jest rozpoznawane jako punkt instalacji.

> [!div class="nextstepaction"]
> [Został zbadany plik dziennika, w którym](?success=inspect-log#clean-up-resources) [Wystąpił problem](https://www.research.net/r/7C6W7BQ?issue=inspect-log)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

Możesz też zachować grupę zasobów i usunąć jeden obszar roboczy. Wyświetl właściwości obszaru roboczego i wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono sposób przekazywania danych na platformę Azure za pomocą programu `Datastore` . Magazyn danych, który jest obsługiwany jako magazyn w chmurze dla Twojego obszaru roboczego, dzięki stałemu i elastycznemu miejscu, w którym można przechowywać dane.

Przedstawiono sposób modyfikacji skryptu szkoleniowego w celu zaakceptowania ścieżki danych przy użyciu wiersza polecenia. Korzystając z programu `Dataset` , można zainstalować katalog dla zdalnego uruchomienia. 

Teraz, gdy masz już model, zapoznaj się z tematem:

* Jak [wdrażać modele przy użyciu Azure Machine Learning](how-to-deploy-and-where.md).
