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
ms.openlocfilehash: 13d43eb788c750a2f24033a6138ebf00ac57fffe
ms.sourcegitcommit: 5dbea4631b46d9dde345f14a9b601d980df84897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91372577"
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
* Wstępna wiedza dotycząca języka Python i przepływów pracy uczenia maszynowego.
* Lokalne środowisko programistyczne, takie jak Visual Studio Code, Jupyter lub platformy PyCharm itd.
* Python (wersja 3,5 do 3,7).

## <a name="adjust-the-training-script"></a>Dostosuj skrypt szkoleniowy
Teraz masz skrypt szkoleniowy (samouczek/src/uczenie. PR) działający w Azure Machine Learning i można monitorować wydajność modelu. Sparametryzujmy skrypt szkoleniowy, wprowadzając argumenty. Argumenty using umożliwiają łatwe porównywanie różnych parametrów.

Nasz skrypt szkoleniowy jest teraz ustawiany do pobierania zestawu danych CIFAR10 w każdym uruchomieniu. Następujący kod języka Python został dostosowany w celu odczytania danych z katalogu.

>[!NOTE] 
> Użycie `argparse` skryptu parameterizes.

```python
# tutorial/src/train.py
import os
import argparse
import torch
import torch.optim as optim
import torchvision
import torchvision.transforms as transforms

from model import Net
from azureml.core import Run

run = Run.get_context()

if __name__ == "__main__":
    parser = argparse.ArgumentParser()
    parser.add_argument('--data_path', type=str, help='Path to the training data')
    parser.add_argument('--learning_rate', type=float, default=0.001, help='Learning rate for SGD')
    parser.add_argument('--momentum', type=float, default=0.9, help='Momentum for SGD')
    args = parser.parse_args()
    
    print("===== DATA =====")
    print("DATA PATH: " + args.data_path)
    print("LIST FILES IN DATA PATH...")
    print(os.listdir(args.data_path))
    print("================")
    
    # prepare DataLoader for CIFAR10 data
    transform = transforms.Compose([transforms.ToTensor(), transforms.Normalize((0.5, 0.5, 0.5), (0.5, 0.5, 0.5))])
    trainset = torchvision.datasets.CIFAR10(
        root=args.data_path,
        train=True,
        download=False,
        transform=transform,
    )
    trainloader = torch.utils.data.DataLoader(trainset, batch_size=4, shuffle=True, num_workers=2)

    # define convolutional network
    net = Net()

    # set up pytorch loss /  optimizer
    criterion = torch.nn.CrossEntropyLoss()
    optimizer = optim.SGD(
        net.parameters(),
        lr=args.learning_rate,
        momentum=args.momentum,
    )

    # train the network
    for epoch in range(2):

        running_loss = 0.0
        for i, data in enumerate(trainloader, 0):
            # unpack the data
            inputs, labels = data

            # zero the parameter gradients
            optimizer.zero_grad()

            # forward + backward + optimize
            outputs = net(inputs)
            loss = criterion(outputs, labels)
            loss.backward()
            optimizer.step()

            # print statistics
            running_loss += loss.item()
            if i % 2000 == 1999:
                loss = running_loss / 2000
                run.log('loss', loss) # log loss metric to AML
                print(f'epoch={epoch + 1}, batch={i + 1:5}: loss {loss:.2f}')
                running_loss = 0.0

    print('Finished Training')
```

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

## <a name="test-the-script-locally"></a>Lokalne testowanie skryptu

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

Aby uruchomić zmodyfikowany skrypt szkoleniowy lokalnie, wywołaj:

```bash
python src/train.py --data_path ./data --learning_rate 0.003 --momentum 0.92
```

Należy unikać pobierania zestawu danych CIFAR10, przekazując ścieżkę lokalną do danych. Możesz również eksperymentować z różnymi wartościami dla _współczynnika uczenia_ _i niedopełnienia, bez_ konieczności podawania kodu w skrypcie szkoleniowym.

## <a name="upload-the-data-to-azure"></a>Przekazywanie danych na platformę Azure

Aby uruchomić ten skrypt w Azure Machine Learning, musisz udostępnić dane szkoleniowe na platformie Azure. Obszar roboczy Azure Machine Learning jest dostępny z _domyślnym_ magazynem danych. To jest konto usługi Azure Blob Storage, w którym można przechowywać dane szkoleniowe.

>[!NOTE] 
> Azure Machine Learning umożliwia łączenie innych magazynów danych opartych na chmurze, które przechowują dane. Aby uzyskać więcej informacji, zobacz [dokumentację magazynów](./concept-data.md)danych.  

Utwórz nowy skrypt kontrolki języka Python o nazwie `05-upload-data.py` w `tutorial` katalogu:

```python
# tutorial/05-upload-data.py
from azureml.core import Workspace
ws = Workspace.from_config()
datastore = ws.get_default_datastore()
datastore.upload(src_dir='./data', target_path='datasets/cifar10', overwrite=True)
```

`target_path`Wartość określa ścieżkę do magazynu danych, w którym zostaną przekazane dane CIFAR10.

>[!TIP] 
> Gdy używasz Azure Machine Learning do przekazywania danych, możesz użyć [Eksplorator usługi Azure Storage](https://azure.microsoft.com/features/storage-explorer/) do przekazywania plików ad hoc. Jeśli potrzebujesz narzędzia ETL, możesz użyć [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/introduction) do pozyskiwania danych na platformie Azure.

Uruchom plik Python, aby przekazać dane. (Przekazywanie powinno być szybkie, mniejsze niż 60 sekund).

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


## <a name="create-a-control-script"></a>Utwórz skrypt kontrolny

Tak jak wcześniej, Utwórz nowy skrypt kontrolki języka Python o nazwie `06-run-pytorch-data.py` :

```python
# tutorial/06-run-pytorch-data.py
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
    env = Environment.from_conda_specification(name='pytorch-env',file_path='.azureml/pytorch-env.yml')
    config.run_config.environment = env

    run = experiment.submit(config)
    aml_url = run.get_portal_url()
    print("Submitted to an Azure Machine Learning compute cluster. Click on the link below")
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
      [Zestaw danych](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py&preserve-view=true) służy do odwoływania się do danych przekazanych do usługi Azure Blob Storage. Zbiory danych są warstwą abstrakcji na podstawie dane, które są przeznaczone do poprawy niezawodności i wiarygodności.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      `config = ScriptRunConfig(...)`
   :::column-end:::
   :::column span="2":::
      [ScriptRunConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py&preserve-view=true) jest modyfikowany w celu uwzględnienia listy argumentów, do których zostanie przekazane `train.py` . `dataset.as_named_input('input').as_mount()`Argument oznacza, że określony katalog zostanie _zainstalowany_ do elementu docelowego obliczeń.
   :::column-end:::
:::row-end:::

## <a name="submit-the-run-to-azure-machine-learning"></a>Prześlij przebieg do Azure Machine Learning

Teraz ponownie prześlij przebieg, aby użyć nowej konfiguracji:

```bash
python 06-run-pytorch-data.py
```

Ten kod będzie drukował adres URL do eksperymentu w Azure Machine Learning Studio. Jeśli przejdziesz do tego linku, będzie można zobaczyć, że Twój kod jest uruchomiony.

### <a name="inspect-the-log-file"></a>Inspekcja pliku dziennika

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

## <a name="clean-up-resources"></a>Czyszczenie zasobów

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

Możesz też zachować grupę zasobów i usunąć jeden obszar roboczy. Wyświetl właściwości obszaru roboczego i wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono sposób przekazywania danych na platformę Azure za pomocą programu `Datastore` . Magazyn danych, który jest obsługiwany jako magazyn w chmurze dla Twojego obszaru roboczego, dzięki stałemu i elastycznemu miejscu, w którym można przechowywać dane.

Przedstawiono sposób modyfikacji skryptu szkoleniowego w celu zaakceptowania ścieżki danych przy użyciu wiersza polecenia. Korzystając z programu `Dataset` , można zainstalować katalog dla zdalnego uruchomienia. 

Teraz, gdy masz już model, zapoznaj się z tematem:

* Jak [wdrażać modele przy użyciu Azure Machine Learning](how-to-deploy-and-where.md).
