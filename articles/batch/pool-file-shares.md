---
title: Udział plików platformy Azure dla pul Azure Batch
description: Jak zainstalować udział Azure Files z węzłów obliczeniowych w puli systemu Linux lub Windows w Azure Batch.
ms.topic: how-to
ms.date: 05/24/2018
ms.openlocfilehash: 0aa8c8f6dcf1114688e6abaf1a17f2e8af6fa4fe
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/21/2020
ms.locfileid: "83723497"
---
# <a name="use-an-azure-file-share-with-a-batch-pool"></a>Korzystanie z udziału plików platformy Azure z pulą usługi Batch

[Azure Files](../storage/files/storage-files-introduction.md) oferuje w pełni zarządzane udziały plików w chmurze, które są dostępne za pośrednictwem protokołu bloku komunikatów serwera (SMB). Ten artykuł zawiera informacje i przykłady kodu służące do instalowania i używania udziału plików platformy Azure w węzłach obliczeniowych puli. Przykłady kodu korzystają z zestawów SDK usługi Batch .NET i Python, ale można wykonywać podobne operacje przy użyciu innych zestawów SDK i narzędzi usługi Batch.

Usługa Batch oferuje natywną obsługę interfejsu API, która umożliwia odczytywanie i zapisywanie danych za pomocą obiektów BLOB usługi Azure Storage. Jednak w niektórych przypadkach możesz chcieć uzyskać dostęp do udziału plików platformy Azure z węzłów obliczeniowych puli. Na przykład istnieje starsze obciążenie, które zależy od udziału plików SMB, lub zadania muszą uzyskiwać dostęp do udostępnionych danych lub generować udostępnione dane wyjściowe. 

## <a name="considerations-for-use-with-batch"></a>Zagadnienia dotyczące korzystania z usługi Batch

* Rozważ użycie udziału plików platformy Azure, gdy masz pule, w których jest uruchamiana stosunkowo niska liczba zadań równoległych. Przejrzyj [elementy docelowe wydajności i skalowania](../storage/files/storage-files-scale-targets.md) , aby określić, czy należy używać Azure Files (używających konta usługi Azure Storage), uwzględniając rozmiar oczekiwanej puli i liczbę plików zasobów. 

* Udziały plików platformy Azure są [opłacalne](https://azure.microsoft.com/pricing/details/storage/files/) i można je skonfigurować za pomocą replikacji danych w innym regionie, tak aby były globalnie nadmiarowe. 

* Udział plików platformy Azure można zainstalować jednocześnie na komputerze lokalnym.

* Zobacz również ogólne [zagadnienia dotyczące planowania](../storage/files/storage-files-planning.md) udziałów plików platformy Azure.


## <a name="create-a-file-share"></a>Tworzenie udziału plików

[Utwórz udział plików](../storage/files/storage-how-to-create-file-share.md) na koncie magazynu, które jest połączone z kontem w usłudze Batch, lub na osobnym koncie magazynu.

## <a name="mount-a-share-on-a-windows-pool"></a>Instalowanie udziału w puli systemu Windows

Ta sekcja zawiera kroki i przykłady kodu służące do instalowania udziału plików platformy Azure i korzystania z niego w puli węzłów systemu Windows. Aby uzyskać dodatkowe tło, zapoznaj się z [dokumentacją](../storage/files/storage-how-to-use-files-windows.md) dotyczącą instalowania udziału plików platformy Azure w systemie Windows. 

W usłudze Batch należy zainstalować udział za każdym razem, gdy zadanie jest uruchamiane w węźle systemu Windows. Obecnie nie jest możliwe utrzymywanie połączenia sieciowego między zadaniami w węzłach systemu Windows.

Na przykład Dołącz polecenie, `net use` Aby zainstalować udział plików w ramach każdego wiersza polecenia zadania. Aby można było zainstalować udział plików, potrzebne są następujące poświadczenia:

* **Nazwa użytkownika**: Azure \\ \< STORAGEACCOUNTNAME \> , na przykład Azure \\ *mystorageaccountname*
* **Hasło**: \< StorageAccountKeyWhichEnds in = =>, na przykład *XXXXXXXXXXXXXXXXXXXXX = =*

Następujące polecenie instaluje udział plików *myfileshare* na koncie magazynu *mystorageaccountname* jako dysk *S:*

```
net use S: \\mystorageaccountname.file.core.windows.net\myfileshare /user:AZURE\mystorageaccountname XXXXXXXXXXXXXXXXXXXXX==
```

W przypadku uproszczenia poniższe przykłady przekazują poświadczenia bezpośrednio do tekstu. W przypadku zdecydowanie zalecamy zarządzanie poświadczeniami przy użyciu zmiennych środowiskowych, certyfikatów lub rozwiązania, takiego jak Azure Key Vault.

Aby uprościć operację instalacji, opcjonalnie Utrwalaj poświadczenia w węzłach. Następnie można zainstalować udział bez poświadczeń. Wykonaj następujące dwa kroki:

1. Uruchom `cmdkey` Narzędzie wiersza polecenia przy użyciu zadania uruchamiania w konfiguracji puli. Dzięki temu poświadczenia są utrwalane w każdym węźle systemu Windows. Wiersz polecenia Uruchom zadanie jest podobny do:

   ```
   cmd /c "cmdkey /add:mystorageaccountname.file.core.windows.net /user:AZURE\mystorageaccountname /pass:XXXXXXXXXXXXXXXXXXXXX=="

   ```

2. Zainstaluj udział w każdym węźle w ramach każdego zadania przy użyciu polecenia `net use` . Na przykład poniższy wiersz polecenia zadania instaluje udział plików jako dysk *S:* . Następnie następuje polecenie lub skrypt, który odwołuje się do udziału. Poświadczenia w pamięci podręcznej są używane w wywołaniu metody `net use` . W tym kroku przyjęto założenie, że używasz tej samej tożsamości użytkownika do zadań, które zostały użyte w zadania podrzędnego uruchamiania w puli, co nie jest odpowiednie dla wszystkich scenariuszy.

   ```
   cmd /c "net use S: \\mystorageaccountname.file.core.windows.net\myfileshare" 
   ```

### <a name="c-example"></a>Przykład w języku C#
W poniższym przykładzie w języku C# przedstawiono sposób utrwalania poświadczeń w puli systemu Windows przy użyciu zadania uruchamiania. Nazwa usługi plików magazynu i poświadczenia magazynu są przesyłane zgodnie ze zdefiniowanymi stałymi. W tym miejscu zadanie uruchamiania jest uruchamiane w ramach standardowego konta użytkownika (nie będącego administratorem) z zakresem puli.

```csharp
...
CloudPool pool = batchClient.PoolOperations.CreatePool(
    poolId: PoolId,
    targetDedicatedComputeNodes: PoolNodeCount,
    virtualMachineSize: PoolVMSize,
    virtualMachineConfiguration: virtualMachineConfiguration);

// Start task to store credentials to mount file share
string startTaskCommandLine = String.Format("cmd /c \"cmdkey /add:{0} /user:AZURE\\{1} /pass:{2}\"", StorageFileService, StorageAccountName, StorageAccountKey);

pool.StartTask = new StartTask
{
    CommandLine = startTaskCommandLine,
    UserIdentity = new UserIdentity(new AutoUserSpecification(
        elevationLevel: ElevationLevel.NonAdmin, 
        scope: AutoUserScope.Pool))
};

pool.Commit();
```

Po zapisaniu poświadczeń Użyj linii poleceń zadań, aby zainstalować udział i odwołać się do udziału w operacjach odczytu lub zapisu. Jako podstawowy przykład wiersz polecenia zadania w poniższym fragmencie kodu używa `dir` polecenia, aby wyświetlić listę plików w udziale plików. Upewnij się, że uruchomiono każde zadanie zadania przy użyciu [tożsamości użytkownika](batch-user-accounts.md) użytej do uruchomienia zadania uruchamiania w puli. 

```csharp
...
string taskId = "myTask";
string taskCommandLine = String.Format("cmd /c \"net use {0} {1} & dir {2}\"", ShareMountPoint, StorageFileShare, ShareMountPoint);

CloudTask task = new CloudTask(taskId, taskCommandLine);
task.UserIdentity = new UserIdentity(new AutoUserSpecification(
    elevationLevel: ElevationLevel.NonAdmin,
    scope: AutoUserScope.Pool));
tasks.Add(task);
```

## <a name="mount-a-share-on-a-linux-pool"></a>Instalowanie udziału w puli systemu Linux

Udziały plików platformy Azure można instalować w dystrybucjach systemu Linux przy użyciu [klienta jądra CIFS](https://wiki.samba.org/index.php/LinuxCIFS). Poniższy przykład pokazuje, jak zainstalować udział plików w puli węzłów obliczeniowych Ubuntu 16,04 LTS. Jeśli używasz innej dystrybucji systemu Linux, ogólne kroki są podobne, ale Użyj Menedżera pakietów odpowiedniego do dystrybucji. Aby uzyskać szczegółowe informacje i dodatkowe przykłady, zobacz [używanie Azure Files z systemem Linux](../storage/files/storage-how-to-use-files-linux.md).

Najpierw w obszarze tożsamość użytkownika Administrator zainstaluj `cifs-utils` pakiet i Utwórz punkt instalacji (na przykład */mnt/MyAzureFileShare*) w lokalnym systemie plików. Folder dla punktu instalacji można utworzyć w dowolnym miejscu w systemie plików, ale jest to typowa Konwencja do utworzenia tego folderu w `/mnt` folderze. Nie należy tworzyć punktu instalacji bezpośrednio w `/mnt` (na Ubuntu) lub `/mnt/resource` (w innych dystrybucjach).

```
apt-get update && apt-get install cifs-utils && sudo mkdir -p /mnt/MyAzureFileShare
```

Następnie uruchom polecenie, `mount` Aby zainstalować udział plików, podając następujące poświadczenia:

* **Nazwa użytkownika**: \< storageaccountname \> , na przykład *mystorageaccountname*
* **Hasło**: \< StorageAccountKeyWhichEnds in = =>, na przykład *XXXXXXXXXXXXXXXXXXXXX = =*

Następujące polecenie instaluje udział plików *myfileshare* na koncie magazynu *mystorageaccountname* o godzinie */mnt/MyAzureFileShare*: 

```
mount -t cifs //mystorageaccountname.file.core.windows.net/myfileshare /mnt/MyAzureFileShare -o vers=3.0,username=mystorageaccountname,password=XXXXXXXXXXXXXXXXXXXXX==,dir_mode=0777,file_mode=0777,serverino && ls /mnt/MyAzureFileShare
```

W przypadku uproszczenia poniższe przykłady przekazują poświadczenia bezpośrednio do tekstu. W przypadku zdecydowanie zalecamy zarządzanie poświadczeniami przy użyciu zmiennych środowiskowych, certyfikatów lub rozwiązania, takiego jak Azure Key Vault.

W puli systemu Linux wszystkie te kroki można połączyć w ramach pojedynczego zadania uruchamiania lub uruchomić je w skrypcie. Uruchom zadanie uruchamiania jako użytkownik administrator w puli. Przed uruchomieniem dalszych zadań w puli, która odwołuje się do udziału, należy ustawić zadanie uruchamiania.

### <a name="python-example"></a>Przykład języka Python

Poniższy przykład kodu w języku Python pokazuje, jak skonfigurować pulę Ubuntu do instalowania udziału w zadaniu startowym. Punkt instalacji, punkt końcowy udziału plików i poświadczenia magazynu są przesyłane jako zdefiniowane stałe. Zadanie uruchamiania jest uruchamiane w ramach konta użytkownika administratora z zakresem puli.

```python
pool = batch.models.PoolAddParameter(
    id=pool_id,
    virtual_machine_configuration=batchmodels.VirtualMachineConfiguration(
        image_reference=batchmodels.ImageReference(
            publisher="Canonical",
            offer="UbuntuServer",
            sku="16.04.0-LTS",
            version="latest"),
        node_agent_sku_id="batch.node.ubuntu 16.04"),
    vm_size=_POOL_VM_SIZE,
    target_dedicated_nodes=_POOL_NODE_COUNT,
    start_task=batchmodels.StartTask(
        command_line="/bin/bash -c \"apt-get update && apt-get install cifs-utils && mkdir -p {} && mount -t cifs {} {} -o vers=3.0,username={},password={},dir_mode=0777,file_mode=0777,serverino\"".format(
            _COMPUTE_NODE_MOUNT_POINT, _STORAGE_ACCOUNT_SHARE_ENDPOINT, _COMPUTE_NODE_MOUNT_POINT, _STORAGE_ACCOUNT_NAME, _STORAGE_ACCOUNT_KEY),
        wait_for_success=True,
        user_identity=batchmodels.UserIdentity(
            auto_user=batchmodels.AutoUserSpecification(
                scope=batchmodels.AutoUserScope.pool,
                elevation_level=batchmodels.ElevationLevel.admin)),
    )
)
batch_service_client.pool.add(pool)
```

Po zainstalowaniu udziału i zdefiniowaniu zadania użyj udziału w wierszach poleceń zadań. Na przykład następujące podstawowe polecenie używa `ls` do wyświetlania listy plików w udziale plików.

```python
...
task = batch.models.TaskAddParameter(
    id='mytask',
    command_line="/bin/bash -c \"ls {}\"".format(_COMPUTE_NODE_MOUNT_POINT))

batch_service_client.task.add(job_id, task)
```


## <a name="next-steps"></a>Następne kroki

* Aby poznać inne opcje odczytywania i zapisywania danych w usłudze Batch, zobacz [Omówienie funkcji usługi Batch](batch-api-basics.md) i [zadania trwałe oraz dane wyjściowe zadania](batch-task-output.md)podrzędnego.

* Zobacz również zestaw narzędzi do tworzenia [pakietów wsadowych usługi Batch](https://github.com/Azure/batch-shipyard) , który obejmuje [przepisy stoczniowe](https://github.com/Azure/batch-shipyard/tree/master/recipes) do wdrażania systemów plików dla obciążeń kontenerów usługi Batch.
