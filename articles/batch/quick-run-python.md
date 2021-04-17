---
title: Szybki start — używanie interfejsu API języka Python do uruchamiania Azure Batch zadań
description: W tym przewodniku Szybki start uruchamiasz przykładowe Azure Batch i zadania przy użyciu biblioteki klienta języka Python dla usługi Batch. Poznaj kluczowe pojęcia dotyczące usługi Batch.
ms.date: 08/17/2020
ms.topic: quickstart
ms.custom:
- seo-python-october2019
- mvc
- devx-track-python
- mode-api
ms.openlocfilehash: 75f83e0ea4823796ace348084bab0915babc8979
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107535563"
---
# <a name="quickstart-use-python-api-to-run-an-azure-batch-job"></a>Szybki start: uruchamianie zadania aplikacji przy użyciu interfejsu API Azure Batch Python

Rozpoczynanie pracy z Azure Batch przy użyciu interfejsu API języka Python do uruchamiania Azure Batch zadań z aplikacji. Aplikacja przekaże pliki danych wejściowych do usługi Azure Storage i utworzy pulę węzłów obliczeniowych usługi Batch (maszyn wirtualnych). Następnie tworzy zadanie, które uruchamia zadania w celu przetwarzania każdego pliku wejściowego w puli przy użyciu podstawowego polecenia.

Po ukończeniu tego przewodnika Szybki start będziesz rozumieć kluczowe pojęcia dotyczące usługi Batch i wszystko będzie gotowe do wypróbowania usługi Batch z bardziej realistycznymi obciążeniami na większą skalę.

![Omówienie przepływu pracy Azure Batch przepływu pracy](./media/quick-run-python/overview-of-the-azure-batch-workflow.png)

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

- Konto usługi Batch i połączone konto usługi Azure Storage. Aby utworzyć te konta, skorzystaj z przewodników Szybki start dla usługi Batch i [witryny Azure Portal](quick-create-portal.md) lub [interfejsu wiersza polecenia platformy Azure](quick-create-cli.md).

- [Python](https://python.org/downloads) w wersji 2.7 lub 3.6, w tym [menedżer pakietów pip](https://pip.pypa.io/en/stable/installing/)

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com).

[!INCLUDE [batch-common-credentials](../../includes/batch-common-credentials.md)]

## <a name="download-the-sample"></a>Pobieranie przykładu

[Pobierz lub sklonuj przykładową aplikację](https://github.com/Azure-Samples/batch-python-quickstart) z usługi GitHub. Aby sklonować repozytorium przykładowej aplikacji za pomocą klienta Git, użyj następującego polecenia:

```bash
git clone https://github.com/Azure-Samples/batch-python-quickstart.git
```

Przejdź do katalogu zawierającego skrypt języka Python `python_quickstart_client.py` .

W środowisku projektowym Python zainstaluj wymagane pakiety przy użyciu menedżera `pip`.

```bash
pip install -r requirements.txt
```

Otwórz plik `config.py`. Zaktualizuj ciągi poświadczeń konta usługi Batch i konta magazynu, podając wartości uzyskane dla Twoich kont. Na przykład:

```Python
_BATCH_ACCOUNT_NAME = 'mybatchaccount'
_BATCH_ACCOUNT_KEY = 'xxxxxxxxxxxxxxxxE+yXrRvJAqT9BlXwwo1CwF+SwAYOxxxxxxxxxxxxxxxx43pXi/gdiATkvbpLRl3x14pcEQ=='
_BATCH_ACCOUNT_URL = 'https://mybatchaccount.mybatchregion.batch.azure.com'
_STORAGE_ACCOUNT_NAME = 'mystorageaccount'
_STORAGE_ACCOUNT_KEY = 'xxxxxxxxxxxxxxxxy4/xxxxxxxxxxxxxxxxfwpbIC5aAWA8wDu+AFXZB827Mt9lybZB1nUcQbQiUrkPtilK5BQ=='
```

## <a name="run-the-app"></a>Uruchamianie aplikacji

Aby zobaczyć przepływ pracy usługi Batch w akcji, uruchom skrypt:

```bash
python python_quickstart_client.py
```

Po uruchomieniu skryptu przejrzyj kod, aby poznać działanie poszczególnych części aplikacji.

Po uruchomieniu aplikacji przykładowej dane wyjściowe w konsoli będą wyglądać mniej więcej następująco. W czasie wykonywania nastąpi wstrzymanie operacji w momencie wyświetlenia komunikatu `Monitoring all tasks for 'Completed' state, timeout in 00:30:00...` podczas uruchamiania węzłów obliczeniowych puli. Zadania podrzędne zostaną umieszczone w kolejce do uruchomienia zaraz po uruchomieniu pierwszego węzła obliczeniowego. Przejdź do konta usługi Batch w witrynie [Azure Portal](https://portal.azure.com), aby monitorować pulę, węzły obliczeniowe, zadanie i zadania podrzędne w ramach konta usługi Batch.

```output
Sample start: 11/26/2018 4:02:54 PM

Container [input] created.
Uploading file taskdata0.txt to container [input]...
Uploading file taskdata1.txt to container [input]...
Uploading file taskdata2.txt to container [input]...
Creating pool [PythonQuickstartPool]...
Creating job [PythonQuickstartJob]...
Adding 3 tasks to job [PythonQuickstartJob]...
Monitoring all tasks for 'Completed' state, timeout in 00:30:00...
```

Po zakończeniu zadań podrzędnych dla każdego z nich zostaną wyświetlone dane wyjściowe podobne do następujących:

```output
Printing task output...
Task: Task0
Node: tvm-2850684224_3-20171205t000401z
Standard out:
Batch processing began with mainframe computers and punch cards. Today it still plays a central role in business, engineering, science, and other pursuits that require running lots of automated tasks....
...
```

Typowy czas wykonywania wynosi mniej więcej 3 minuty w przypadku uruchomienia aplikacji w konfiguracji domyślnej. Początkowa konfiguracja puli zajmuje najwięcej czasu.

## <a name="review-the-code"></a>Przeglądanie kodu

Aplikacja w języku Python omawiana w tym przewodniku Szybki start wykonuje następujące akcje:

- Przekazuje trzy małe pliki tekstowe do kontenera obiektów blob na koncie usługi Azure Storage. Te pliki to dane wejściowe do przetworzenia przez zadania podrzędne usługi Batch.
- Tworzy pulę dwóch węzłów obliczeniowych z systemem Ubuntu 18.04 LTS.
- Tworzy zadanie i trzy zadania podrzędne do uruchomienia w tych węzłach. Każde zadanie podrzędne przetwarza jeden z plików wejściowych przy użyciu wiersza polecenia powłoki Bash.
* Wyświetla pliki zwrócone przez zadania podrzędne.

Aby uzyskać szczegółowe informacje, zapoznaj się z plikiem `python_quickstart_client.py` i poniższymi sekcjami.

### <a name="preliminaries"></a>Akcje wstępne

Aby nawiązać interakcję z kontem magazynu, aplikacja używa pakietu [azure-storage-blob](https://pypi.python.org/pypi/azure-storage-blob) do utworzenia obiektu [BlockBlobService](/python/api/azure-storage-blob/azure.storage.blob.blockblobservice.blockblobservice).

```python
blob_client = azureblob.BlockBlobService(
    account_name=config._STORAGE_ACCOUNT_NAME,
    account_key=config._STORAGE_ACCOUNT_KEY)
```

Za pomocą odwołania `blob_client` aplikacja tworzy kontener w ramach konta magazynu i przekazuje pliki danych do kontenera. Pliki w magazynie są zdefiniowane jako obiekty [ResourceFile](/python/api/azure-batch/azure.batch.models.resourcefile) usługi Batch, które następnie mogą zostać pobrane przez tę usługę do węzłów obliczeniowych.

```python
input_file_paths = [os.path.join(sys.path[0], 'taskdata0.txt'),
                    os.path.join(sys.path[0], 'taskdata1.txt'),
                    os.path.join(sys.path[0], 'taskdata2.txt')]

input_files = [
    upload_file_to_container(blob_client, input_container_name, file_path)
    for file_path in input_file_paths]
```

Aplikacja tworzy obiekt [BatchServiceClient](/python/api/azure.batch.batchserviceclient) na potrzeby tworzenia pul, zadań i zadań podrzędnych w usłudze Batch oraz zarządzania nimi. Klient usługi Batch w przykładzie korzysta z uwierzytelniania za pomocą klucza wspólnego. Usługa Batch obsługuje też uwierzytelnianie za pomocą usługi Azure Active Directory.

```python
credentials = batch_auth.SharedKeyCredentials(config._BATCH_ACCOUNT_NAME,
                                              config._BATCH_ACCOUNT_KEY)

batch_client = batch.BatchServiceClient(
    credentials,
    batch_url=config._BATCH_ACCOUNT_URL)
```

### <a name="create-a-pool-of-compute-nodes"></a>Tworzenie puli węzłów obliczeniowych

Aby utworzyć pulę usługi Batch, aplikacja ustawia liczbę węzłów, rozmiar maszyny wirtualnej i konfigurację puli za pomocą klasy [PoolAddParameter](/python/api/azure-batch/azure.batch.models.pooladdparameter). W tym przypadku obiekt [VirtualMachineConfiguration](/python/api/azure-batch/azure.batch.models.virtualmachineconfiguration) określa parametr [ImageReference](/python/api/azure-batch/azure.batch.models.imagereference) z odwołaniem do obrazu systemu Ubuntu Server 18.04 LTS opublikowanego w witrynie Azure Marketplace. Usługa Batch obsługuje szeroki zakres obrazów systemów Linux i Windows Server pochodzących z witryny Azure Marketplace, a także niestandardowe obrazy maszyn wirtualnych.

Liczba węzłów (`_POOL_NODE_COUNT`) i rozmiar maszyny wirtualnej (`_POOL_VM_SIZE`) to zdefiniowane stałe. Domyślnie przykładowa aplikacja tworzy pulę składającą się z 2 węzłów o rozmiarze *Standard_A1_v2*. Sugerowany rozmiar oferuje dobry kompromis między wydajnością a kosztem na potrzeby tego krótkiego przykładu.

Metoda [pool.add](/python/api/azure-batch/azure.batch.operations.pooloperations) przesyła pulę do usługi Batch.

```python
new_pool = batch.models.PoolAddParameter(
    id=pool_id,
    virtual_machine_configuration=batchmodels.VirtualMachineConfiguration(
        image_reference=batchmodels.ImageReference(
            publisher="Canonical",
            offer="UbuntuServer",
            sku="18.04-LTS",
            version="latest"
        ),
        node_agent_sku_id="batch.node.ubuntu 18.04"),
    vm_size=config._POOL_VM_SIZE,
    target_dedicated_nodes=config._POOL_NODE_COUNT
)
batch_service_client.pool.add(new_pool)
```

### <a name="create-a-batch-job"></a>Utworzenie zadania usługi Batch

Zadanie usługi Batch to logiczna grupa zawierająca co najmniej jedno zadanie podrzędne. Zadanie uwzględnia wspólne ustawienia zadań podrzędnych, takie jak priorytet i pula, w której zadania podrzędne mają być uruchamiane. Aplikacja tworzy zadanie w puli za pomocą klasy [JobAddParameter](/python/api/azure-batch/azure.batch.models.jobaddparameter). Metoda [job.add](/python/api/azure-batch/azure.batch.operations.joboperations) dodaje zadanie do określonego konta usługi Batch. Początkowo zadanie nie zawiera zadań podrzędnych.

```python
job = batch.models.JobAddParameter(
    id=job_id,
    pool_info=batch.models.PoolInformation(pool_id=pool_id))
batch_service_client.job.add(job)
```

### <a name="create-tasks"></a>Tworzenie zadań

Aplikacja tworzy listę obiektów zadań podrzędnych przy użyciu klasy [TaskAddParameter](/python/api/azure-batch/azure.batch.models.taskaddparameter). Każde zadanie podrzędne przetwarza wejściowy obiekt `resource_files` przy użyciu parametru `command_line`. W tym przykładzie wiersz polecenia uruchamia polecenie `cat` powłoki Bash, aby wyświetlić plik tekstowy. To polecenie to prosty przykład dla celów demonstracyjnych. Podczas korzystania z usługi Batch aplikację lub skrypt określa się w wierszu polecenia. Usługa Batch udostępnia kilka sposobów wdrażania aplikacji i skryptów w węzłach obliczeniowych.

Następnie aplikacja dodaje zadania podrzędne do zadania za pomocą metody [task.add_collection](/python/api/azure-batch/azure.batch.operations.taskoperations), która tworzy kolejkę zadań podrzędnych do uruchomienia w węzłach obliczeniowych.

```python
tasks = list()

for idx, input_file in enumerate(input_files):
    command = "/bin/bash -c \"cat {}\"".format(input_file.file_path)
    tasks.append(batch.models.TaskAddParameter(
        id='Task{}'.format(idx),
        command_line=command,
        resource_files=[input_file]
    )
    )
batch_service_client.task.add_collection(job_id, tasks)
```

### <a name="view-task-output"></a>Wyświetlanie danych wyjściowych zadania podrzędnego

Aplikacja monitoruje stan zadań podrzędnych, aby upewnić się, że zostaną one ukończone. Następnie aplikacja wyświetla plik `stdout.txt` generowany przez każde ukończone zadanie podrzędne. Po pomyślnym uruchomieniu zadania podrzędnego dane wyjściowe polecenia tego zadania zostaną zapisane w pliku `stdout.txt`:

```python
tasks = batch_service_client.task.list(job_id)

for task in tasks:

    node_id = batch_service_client.task.get(job_id, task.id).node_info.node_id
    print("Task: {}".format(task.id))
    print("Node: {}".format(node_id))

    stream = batch_service_client.file.get_from_task(
        job_id, task.id, config._STANDARD_OUT_FILE_NAME)

    file_text = _read_stream_as_string(
        stream,
        encoding)
    print("Standard output:")
    print(file_text)
```

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Aplikacja automatycznie usuwa utworzony kontener magazynu, a także udostępnia opcję usunięcia puli i zadania usługi Batch. Opłaty za pulę są naliczane, dopóki węzły działają, nawet jeśli nie zostały zaplanowane żadne zadania. Gdy pula nie jest już potrzebna, usuń ją. W przypadku usunięcia puli usuwane są również wszystkie dane wyjściowe zadań podrzędnych w węzłach. 

Gdy grupa zasobów, konto usługi Batch i konto magazynu nie będą już potrzebne, usuń je. Aby to zrobić w Azure Portal, wybierz grupę zasobów dla konta usługi Batch i wybierz **pozycję Usuń grupę zasobów.**

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start uruchomiono niewielką aplikację utworzoną za pomocą interfejsu API języka Python usługi Batch w celu utworzenia puli i zadania usługi Batch. Zadanie uruchomiło przykładowe zadania podrzędne i pobrało dane wyjściowe utworzone w węzłach. Teraz, gdy już rozumiesz kluczowe pojęcia związane z usługą Batch, możesz wypróbować tę usługę z bardziej realistycznymi obciążeniami na większą skalę. Aby dowiedzieć się więcej o usłudze Azure Batch i zapoznać się z przewodnikiem dotyczącym obciążenia równoległego o rzeczywistym zastosowaniu, kontynuuj naukę w ramach samouczka języka Python usługi Batch.

> [!div class="nextstepaction"]
> [Przetwarzanie obciążenia równoległego za pomocą języka Python](tutorial-parallel-python.md)
