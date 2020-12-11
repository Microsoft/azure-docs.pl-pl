---
title: Samouczek — uruchamianie równoległego obciążenia przy użyciu interfejsu API języka Python
description: Samouczek — Równoległe przetwarzanie plików multimedialnych przy użyciu narzędzia ffmpeg w usłudze Azure Batch z zastosowaniem biblioteki klienta Batch Python
ms.devlang: python
ms.topic: tutorial
ms.date: 11/29/2018
ms.custom: mvc, devx-track-python
ms.openlocfilehash: 7013ef1e9b6dda4554b665fa8199edb86a8a9adb
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/11/2020
ms.locfileid: "97106516"
---
# <a name="tutorial-run-a-parallel-workload-with-azure-batch-using-the-python-api"></a>Samouczek: uruchamianie równoległego obciążenia w usłudze Azure Batch przy użyciu interfejsu API Python

Usługa Azure Batch umożliwia wydajne uruchamianie równoległych zadań wsadowych oraz zadań wsadowych obliczeń o wysokiej wydajności na platformie Azure. W tym samouczku przedstawiono przykład uruchamiania równoległego obciążenia za pomocą usługi Azure Batch i języka Python. Poznasz prosty przepływ pracy aplikacji usługi Azure Batch i sposób pracy programowej z zasobami usług Azure Batch i Storage. Omawiane kwestie:

> [!div class="checklist"]
> * Uwierzytelnianie przy użyciu kont usług Batch i Storage
> * Przekazywanie plików wejściowych do usługi Storage
> * Tworzenie puli węzłów obliczeniowych w celu uruchomienia aplikacji
> * Tworzenie zadania i zadań podrzędnych przetwarzania plików wejściowych
> * Monitorowanie wykonania zadań podrzędnych
> * Pobieranie plików wyjściowych

W tym samouczku przekonwertujesz równolegle pliki multimedialne w formacie MP4 do formatu MP3 za pomocą narzędzia typu „open source”, [ffmpeg](https://ffmpeg.org/). 

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

* [Środowisko Python w wersji 2.7 lub 3.3 albo nowszej](https://www.python.org/downloads/)

* Menedżer pakietów [PIP](https://pip.pypa.io/en/stable/installing/)

* Konto usługi Azure Batch i połączone konto usługi Azure Storage. Aby utworzyć te konta, skorzystaj z przewodników Szybki start dla usługi Batch i [witryny Azure Portal](quick-create-portal.md) lub [interfejsu wiersza polecenia platformy Azure](quick-create-cli.md).

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com).

[!INCLUDE [batch-common-credentials](../../includes/batch-common-credentials.md)] 

## <a name="download-and-run-the-sample"></a>Pobieranie i uruchamianie aplikacji przykładowej

### <a name="download-the-sample"></a>Pobieranie przykładu

[Pobierz lub sklonuj przykładową aplikację](https://github.com/Azure-Samples/batch-python-ffmpeg-tutorial) z usługi GitHub. Aby sklonować repozytorium przykładowej aplikacji za pomocą klienta Git, użyj następującego polecenia:

```
git clone https://github.com/Azure-Samples/batch-python-ffmpeg-tutorial.git
```

Przejdź do katalogu zawierającego plik `batch_python_tutorial_ffmpeg.py`.

W środowisku Python zainstaluj wymagane pakiety przy użyciu menedżera `pip`.

```bash
pip install -r requirements.txt
```

Otwórz plik `config.py`. Zaktualizuj ciągi poświadczeń konta usługi Batch i konta magazynu, podając wartości unikatowe dla Twoich kont. Na przykład:


```Python
_BATCH_ACCOUNT_NAME = 'mybatchaccount'
_BATCH_ACCOUNT_KEY = 'xxxxxxxxxxxxxxxxE+yXrRvJAqT9BlXwwo1CwF+SwAYOxxxxxxxxxxxxxxxx43pXi/gdiATkvbpLRl3x14pcEQ=='
_BATCH_ACCOUNT_URL = 'https://mybatchaccount.mybatchregion.batch.azure.com'
_STORAGE_ACCOUNT_NAME = 'mystorageaccount'
_STORAGE_ACCOUNT_KEY = 'xxxxxxxxxxxxxxxxy4/xxxxxxxxxxxxxxxxfwpbIC5aAWA8wDu+AFXZB827Mt9lybZB1nUcQbQiUrkPtilK5BQ=='
```

### <a name="run-the-app"></a>Uruchamianie aplikacji

Aby uruchomić skrypt:

```
python batch_python_tutorial_ffmpeg.py
```

Po uruchomieniu aplikacji przykładowej dane wyjściowe w konsoli będą wyglądać mniej więcej następująco. W czasie wykonywania nastąpi wstrzymanie operacji w momencie wyświetlenia komunikatu `Monitoring all tasks for 'Completed' state, timeout in 00:30:00...` podczas uruchamiania węzłów obliczeniowych puli. 
   
```
Sample start: 11/28/2018 3:20:21 PM

Container [input] created.
Container [output] created.
Uploading file LowPriVMs-1.mp4 to container [input]...
Uploading file LowPriVMs-2.mp4 to container [input]...
Uploading file LowPriVMs-3.mp4 to container [input]...
Uploading file LowPriVMs-4.mp4 to container [input]...
Uploading file LowPriVMs-5.mp4 to container [input]...
Creating pool [LinuxFFmpegPool]...
Creating job [LinuxFFmpegJob]...
Adding 5 tasks to job [LinuxFFmpegJob]...
Monitoring all tasks for 'Completed' state, timeout in 00:30:00...
Success! All tasks completed successfully within the specified timeout period.
Deleting container [input]....

Sample end: 11/28/2018 3:29:36 PM
Elapsed time: 00:09:14.3418742
```

Przejdź do konta usługi Batch w witrynie Azure Portal, aby monitorować pulę, węzły obliczeniowe, zadanie i zadania podrzędne. Na przykład aby wyświetlić mapę cieplną węzłów obliczeniowych w puli, kliknij pozycję **Pule**  >  *LinuxFFmpegPool*.

Podczas wykonywania zadań podrzędnych mapa cieplna może wyglądać następująco:

![Mapa cieplna puli](./media/tutorial-parallel-python/pool.png)

Typowy czas wykonywania wynosi mniej więcej **5 minut** w przypadku uruchomienia aplikacji w konfiguracji domyślnej. Tworzenie puli zajmuje najwięcej czasu. 

[!INCLUDE [batch-common-tutorial-download](../../includes/batch-common-tutorial-download.md)]

## <a name="review-the-code"></a>Przeglądanie kodu

W poniższych sekcjach przykładowa aplikacja została podzielona na kroki wykonywane w celu przetworzenia obciążenia w usłudze Batch. Podczas czytania dalszej części tego artykułu obserwuj kod języka Python, ponieważ nie omówiono tu wszystkich wierszy kodu z próbki.

### <a name="authenticate-blob-and-batch-clients"></a>Uwierzytelnianie klientów obiektów blob i usługi Batch

Aby nawiązać interakcję z kontem magazynu, aplikacja używa pakietu [azure-storage-blob](https://pypi.python.org/pypi/azure-storage-blob) do utworzenia obiektu [BlockBlobService](/python/api/azure-storage-blob/azure.storage.blob.blockblobservice.blockblobservice).

```python
blob_client = azureblob.BlockBlobService(
    account_name=_STORAGE_ACCOUNT_NAME,
    account_key=_STORAGE_ACCOUNT_KEY)
```

Aplikacja tworzy obiekt [BatchServiceClient](/python/api/azure.batch.batchserviceclient) na potrzeby tworzenia pul, zadań i zadań podrzędnych w usłudze Batch oraz zarządzania nimi. Klient usługi Batch w przykładzie korzysta z uwierzytelniania za pomocą klucza wspólnego. Program Batch obsługuje również uwierzytelnianie za [Azure Active Directory](batch-aad-auth.md), aby uwierzytelniać poszczególnych użytkowników lub nienadzorowane aplikacje.

```python
credentials = batchauth.SharedKeyCredentials(_BATCH_ACCOUNT_NAME,
                                             _BATCH_ACCOUNT_KEY)

batch_client = batch.BatchServiceClient(
    credentials,
    base_url=_BATCH_ACCOUNT_URL)
```

### <a name="upload-input-files"></a>Przekazywanie plików wejściowych

W aplikacji odwołanie `blob_client` jest używane do utworzenia kontenera magazynu dla plików wejściowych w formacie MP4 oraz kontenera dla danych wyjściowych zadań podrzędnych. Następnie wywoływana jest funkcja `upload_file_to_container` w celu przekazania plików MP4 z lokalnego katalogu `InputFiles` do kontenera. Pliki w magazynie są zdefiniowane jako obiekty [ResourceFile](/python/api/azure-batch/azure.batch.models.resourcefile) usługi Batch, które następnie mogą zostać pobrane przez tę usługę do węzłów obliczeniowych.

```python
blob_client.create_container(input_container_name, fail_on_exist=False)
blob_client.create_container(output_container_name, fail_on_exist=False)
input_file_paths = []

for folder, subs, files in os.walk(os.path.join(sys.path[0], './InputFiles/')):
    for filename in files:
        if filename.endswith(".mp4"):
            input_file_paths.append(os.path.abspath(
                os.path.join(folder, filename)))

# Upload the input files. This is the collection of files that are to be processed by the tasks.
input_files = [
    upload_file_to_container(blob_client, input_container_name, file_path)
    for file_path in input_file_paths]
```

### <a name="create-a-pool-of-compute-nodes"></a>Tworzenie puli węzłów obliczeniowych

Następnie w przykładzie tworzona jest pula węzłów obliczeniowych na koncie usługi Batch z wywołaniem funkcji `create_pool`. Ta zdefiniowana funkcja określa liczbę węzłów, rozmiar maszyny wirtualnej i konfigurację puli za pomocą klasy [PoolAddParameter](/python/api/azure-batch/azure.batch.models.pooladdparameter) usługi Batch. W tym przypadku obiekt [VirtualMachineConfiguration](/python/api/azure-batch/azure.batch.models.virtualmachineconfiguration) określa parametr [ImageReference](/python/api/azure-batch/azure.batch.models.imagereference) z odwołaniem do obrazu systemu Ubuntu Server 18.04 LTS opublikowanego w witrynie Azure Marketplace. Usługa Batch obsługuje szeroki zakres obrazów maszyn wirtualnych z witryny Azure Marketplace oraz niestandardowe obrazy maszyn wirtualnych.

Liczba węzłów i rozmiar maszyny wirtualnej są ustawiane przy użyciu zdefiniowanych stałych. Usługa Batch obsługuje węzły dedykowane oraz [węzły o niskim priorytecie](batch-low-pri-vms.md). W puli możesz użyć dowolnego z tych typów węzłów lub obu. Węzły dedykowane są zarezerwowane dla Twojej puli. Węzły o niskim priorytecie są oferowane w obniżonej cenie i korzystają z nadwyżek pojemności maszyn wirtualnych na platformie Azure. Węzły o niskim priorytecie staną się niedostępne, jeśli pojemność platformy Azure będzie niewystarczająca. Domyślnie przykładowa aplikacja tworzy pulę zawierającą tylko 5 węzłów o niskim priorytecie i rozmiarze *Standardowa_A1_v2*. 

Oprócz fizycznych właściwości węzłów konfiguracja puli zawiera również obiekt [StartTask](/python/api/azure-batch/azure.batch.models.starttask). Funkcja StartTask jest wykonywana w każdym węźle, gdy tylko ten węzeł zostanie dołączony do puli, oraz za każdym razem, gdy węzeł będzie uruchamiany ponownie. W tym przykładzie obiekt StartTask uruchamia polecenia powłoki Bash w celu zainstalowania pakietu ffmpeg i jego zależności w węzłach.

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
    vm_size=_POOL_VM_SIZE,
    target_dedicated_nodes=_DEDICATED_POOL_NODE_COUNT,
    target_low_priority_nodes=_LOW_PRIORITY_POOL_NODE_COUNT,
    start_task=batchmodels.StartTask(
        command_line="/bin/bash -c \"apt-get update && apt-get install -y ffmpeg\"",
        wait_for_success=True,
        user_identity=batchmodels.UserIdentity(
            auto_user=batchmodels.AutoUserSpecification(
                scope=batchmodels.AutoUserScope.pool,
                elevation_level=batchmodels.ElevationLevel.admin)),
    )
)
batch_service_client.pool.add(new_pool)
```

### <a name="create-a-job"></a>Tworzenie zadania

Zadanie usługi Batch określa pulę, w której będą uruchamiane zadania podrzędne, wraz z ustawieniami opcjonalnymi, takimi jak priorytet i harmonogram pracy. Przykładowa aplikacja tworzy zadanie z wywołaniem `create_job`. Ta zdefiniowana funkcja tworzy zadanie w puli za pomocą klasy [JobAddParameter](/python/api/azure-batch/azure.batch.models.jobaddparameter). Metoda [job.add](/python/api/azure-batch/azure.batch.operations.joboperations) przesyła pulę do usługi Batch. Początkowo zadanie nie zawiera zadań podrzędnych.

```python
job = batch.models.JobAddParameter(
    id=job_id,
    pool_info=batch.models.PoolInformation(pool_id=pool_id))

batch_service_client.job.add(job)
```

### <a name="create-tasks"></a>Tworzenie zadań

Aplikacja tworzy zadania podrzędne w ramach zadania przy użyciu wywołania funkcji `add_tasks`. Ta zdefiniowana funkcja tworzy listę obiektów zadań podrzędnych przy użyciu klasy [TaskAddParameter](/python/api/azure-batch/azure.batch.models.taskaddparameter). Każde zadanie podrzędne uruchamia narzędzie ffmpeg w celu przetworzenia wejściowego obiektu `resource_files` za pomocą parametru `command_line`. Narzędzie ffmpeg zostało już zainstalowane na wszystkich węzłach podczas tworzenia puli. Tutaj wiersz polecenia jest używany do uruchomienia narzędzia ffmpeg w celu przekonwertowania każdego z plików wejściowych w formacie MP4 (wideo) na format MP3 (audio).

Przykładowa aplikacja tworzy obiekt [OutputFile](/python/api/azure-batch/azure.batch.models.outputfile) dla pliku MP3 po uruchomieniu wiersza polecenia. Pliki wyjściowe z każdego zadania podrzędnego (w tym przypadku jeden plik) są przekazywane do kontenera na połączonym koncie magazynu przy użyciu właściwości `output_files` w tym zadaniu podrzędnym.

Następnie aplikacja dodaje zadania podrzędne do zadania za pomocą metody [task.add_collection](/python/api/azure-batch/azure.batch.operations.taskoperations), która tworzy kolejkę zadań podrzędnych do uruchomienia w węzłach obliczeniowych. 

```python
tasks = list()

for idx, input_file in enumerate(input_files):
    input_file_path = input_file.file_path
    output_file_path = "".join((input_file_path).split('.')[:-1]) + '.mp3'
    command = "/bin/bash -c \"ffmpeg -i {} {} \"".format(
        input_file_path, output_file_path)
    tasks.append(batch.models.TaskAddParameter(
        id='Task{}'.format(idx),
        command_line=command,
        resource_files=[input_file],
        output_files=[batchmodels.OutputFile(
            file_pattern=output_file_path,
            destination=batchmodels.OutputFileDestination(
                container=batchmodels.OutputFileBlobContainerDestination(
                    container_url=output_container_sas_url)),
            upload_options=batchmodels.OutputFileUploadOptions(
                upload_condition=batchmodels.OutputFileUploadCondition.task_success))]
    )
    )
batch_service_client.task.add_collection(job_id, tasks)
```

### <a name="monitor-tasks"></a>Monitorowanie podzadań

Po dodaniu zadań podrzędnych do zadania usługa Batch automatycznie dodaje je do kolejki i planuje ich wykonanie w węzłach obliczeniowych powiązanej puli. Na podstawie określonych przez użytkownika ustawień usługa Batch obsługuje dodawanie zadań podrzędnych do kolejki, ich planowanie, ponawianie prób ich wykonania oraz inne czynności administracyjne. 

Istnieje wiele sposobów, w jakie można monitorować wykonanie podzadań. Funkcja `wait_for_tasks_to_complete` w tym przykładzie zawiera obiekt [TaskState](/python/api/azure-batch/azure.batch.models.taskstate) używany do monitorowania zadań podrzędnych pod kątem określonego stanu, w tym przypadku ukończenia, w wybranym limicie czasu.

```python
while datetime.datetime.now() < timeout_expiration:
    print('.', end='')
    sys.stdout.flush()
    tasks = batch_service_client.task.list(job_id)

    incomplete_tasks = [task for task in tasks if
                        task.state != batchmodels.TaskState.completed]
    if not incomplete_tasks:
        print()
        return True
    else:
        time.sleep(1)
...
```

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Po wykonaniu zadań podrzędnych aplikacja automatycznie usuwa utworzony wejściowy kontener magazynu, a opcjonalnie także pulę i zadanie usługi Batch. Dla obu klas [JobOperations](/python/api/azure-batch/azure.batch.operations.joboperations) i [PoolOperations](/python/api/azure-batch/azure.batch.operations.pooloperations) klienta BatchClient istnieją metody usuwania, które są wywoływane, jeśli potwierdzisz usunięcie. Mimo że nie są naliczane opłaty za same zadania i zadania podrzędne, są naliczane opłaty za węzły obliczeniowe. W związku z tym zaleca się przydzielanie pul stosownie do potrzeb. W przypadku usunięcia puli usuwane są również wszystkie dane wyjściowe zadań podrzędnych w węzłach. Pliki wejściowe i wyjściowe pozostają jednak na koncie magazynu.

Gdy grupa zasobów, konto usługi Batch i konto magazynu nie będą już potrzebne, usuń je. W tym celu w witrynie Azure Portal zaznacz grupę zasobów konta usługi Batch i kliknij pozycję **Usuń grupę zasobów**.

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Uwierzytelnianie przy użyciu kont usług Batch i Storage
> * Przekazywanie plików wejściowych do usługi Storage
> * Tworzenie puli węzłów obliczeniowych w celu uruchomienia aplikacji
> * Tworzenie zadania i zadań podrzędnych przetwarzania plików wejściowych
> * Monitorowanie wykonania zadań podrzędnych
> * Pobieranie plików wyjściowych

Aby zapoznać się z innymi przykładami planowania i przetwarzania obciążeń usługi Batch przy użyciu interfejsu API Python, zobacz przykłady w witrynie GitHub.

> [!div class="nextstepaction"]
> [Przykłady kodu Python dla usługi Batch](https://github.com/Azure/azure-batch-samples/tree/master/Python/Batch)

