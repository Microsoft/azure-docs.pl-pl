---
title: Obciążenia kontenera
description: Dowiedz się, jak uruchamiać i skalować aplikacje z obrazów kontenerów na Azure Batch. Utwórz pulę węzłów obliczeniowych, które obsługują Uruchamianie zadań kontenera.
ms.topic: how-to
ms.date: 10/06/2020
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: 9d8776ba8e683cd14c766fead1e7238a6c24d000
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91843451"
---
# <a name="run-container-applications-on-azure-batch"></a>Uruchamianie aplikacji kontenera na Azure Batch

Azure Batch umożliwia uruchamianie i skalowanie wielu zadań przetwarzania wsadowego na platformie Azure. Zadania wsadowe mogą być uruchamiane bezpośrednio na maszynach wirtualnych (węzłach) w puli wsadowej, ale można również skonfigurować pulę wsadową do uruchamiania zadań w kontenerach zgodnych z platformą Docker w węzłach. W tym artykule pokazano, jak utworzyć pulę węzłów obliczeniowych, które obsługują Uruchamianie zadań kontenera, a następnie uruchamiać zadania kontenera w puli.

Przykłady kodu korzystają z zestawów SDK programu Batch .NET i języka Python. Można również użyć innych zestawów SDK i narzędzi partii, w tym Azure Portal, aby utworzyć pule wsadowe z obsługą kontenerów i uruchamiać zadania kontenera.

## <a name="why-use-containers"></a>Dlaczego warto korzystać z kontenerów?

Używanie kontenerów zapewnia łatwy sposób uruchamiania zadań wsadowych bez konieczności zarządzania środowiskiem i zależnościami w celu uruchamiania aplikacji. Kontenery wdrażają aplikacje jako uproszczone, przenośne, samoobsługowe jednostki, które mogą działać w kilku różnych środowiskach. Na przykład Utwórz i przetestuj kontener lokalnie, a następnie Przekaż obraz kontenera do rejestru na platformie Azure lub w innym miejscu. Model wdrażania kontenerów zapewnia, że środowisko uruchomieniowe aplikacji jest zawsze prawidłowo zainstalowane i skonfigurowane wszędzie tam, gdzie hostuje aplikację. Zadania oparte na kontenerach w usłudze Batch mogą również korzystać z funkcji zadań nienależących do kontenera, w tym pakietów aplikacji i zarządzania plikami zasobów i plików wyjściowych.

## <a name="prerequisites"></a>Wymagania wstępne

Należy zapoznać się z pojęciami dotyczącymi kontenera i sposobami tworzenia puli i zadań wsadowych.

- **Wersje zestawu SDK**: w usłudze Batch SDK są obsługiwane obrazy kontenerów w następujących wersjach:
  - Interfejs API REST usługi Batch w wersji 2017 -09 — 01.6.0
  - Zestaw SDK platformy .NET w wersji 8.0.0
  - Zestaw SDK języka Python w wersji 4,0
  - Zestaw SDK dla języka Java w wersji 3,0
  - Zestaw SDK programu Batch Node.js w wersji 3,0

- **Konta**: w ramach subskrypcji platformy Azure należy utworzyć konto usługi Batch i opcjonalnie konto magazynu platformy Azure.

- **Obsługiwany obraz maszyny wirtualnej**: kontenery są obsługiwane tylko w pulach utworzonych przy użyciu konfiguracji maszyny wirtualnej, od obrazów opisanych w poniższej sekcji "obsługiwane obrazy maszyn wirtualnych". W przypadku podania obrazu niestandardowego zapoznaj się z zagadnieniami w poniższej sekcji i wymaganiami w temacie [Używanie zarządzanego obrazu niestandardowego do tworzenia puli maszyn wirtualnych](batch-custom-images.md).

Należy pamiętać o następujących ograniczeniach:

- Funkcja Batch zapewnia obsługę funkcji RDMA tylko w przypadku kontenerów działających w pulach systemu Linux.

- W przypadku obciążeń kontenera systemu Windows zalecamy wybranie wielordzeniowego rozmiaru maszyny wirtualnej dla puli.

## <a name="supported-virtual-machine-images"></a>Obsługiwane obrazy maszyn wirtualnych

Użyj jednego z następujących obsługiwanych obrazów systemu Windows lub Linux, aby utworzyć pulę węzłów obliczeniowych maszyny wirtualnej dla obciążeń kontenerów. Aby uzyskać więcej informacji o obrazach w portalu Marketplace, które są zgodne z usługą Batch, zobacz [listę obrazów maszyn wirtualnych](batch-linux-nodes.md#list-of-virtual-machine-images).

### <a name="windows-support"></a>Obsługa systemu Windows

Program Batch obsługuje obrazy systemu Windows Server, które mają projekty obsługi kontenerów. Zazwyczaj te nazwy jednostek SKU obrazów są sufiksem z `-with-containers` lub `-with-containers-smalldisk` . Ponadto [interfejs API, aby wyświetlić listę wszystkich obsługiwanych obrazów w usłudze Batch](batch-linux-nodes.md#list-of-virtual-machine-images) , oznacza `DockerCompatible` możliwość, jeśli obraz obsługuje kontenery platformy Docker.

Możesz również tworzyć obrazy niestandardowe z maszyn wirtualnych z systemem Docker w systemie Windows.

### <a name="linux-support"></a>Obsługa systemu Linux

W przypadku obciążeń kontenerów systemu Linux usługa Batch obecnie obsługuje następujące obrazy systemu Linux opublikowane przez Microsoft Azure Batch w portalu Azure Marketplace bez potrzeby niestandardowego obrazu.

#### <a name="vm-sizes-without-rdma"></a>Rozmiary maszyn wirtualnych bez funkcji RDMA

- Dawc `microsoft-azure-batch`
  - Sprzedaży `centos-container`
  - Sprzedaży `ubuntu-server-container`

#### <a name="vm-sizes-with-rdma"></a>Rozmiary maszyn wirtualnych przy użyciu funkcji RDMA

- Dawc `microsoft-azure-batch`
  - Sprzedaży `centos-container-rdma`
  - Sprzedaży `ubuntu-server-container-rdma`

Te obrazy są obsługiwane tylko w pulach Azure Batch i są przeznaczone do wykonywania kontenera Docker. Funkcje te:

- Wstępnie zainstalowany, zgodny z platformą Docker środowisko uruchomieniowe kontenera [Moby](https://github.com/moby/moby)

- Wstępnie zainstalowane sterowniki procesora GPU NVIDIA i środowisko uruchomieniowe kontenera NVIDIA, aby usprawnić wdrażanie na maszynach wirtualnych z serii N systemu Azure

- Wstępnie zainstalowany/wstępnie skonfigurowany obraz obsługujący rozmiary maszyn wirtualnych InfiniBand RDMA dla obrazów z sufiksem `-rdma` . Obecnie te obrazy nie obsługują rozmiarów maszyn wirtualnych typu SR-IOV IB/RDMA.

Można również tworzyć obrazy niestandardowe z maszyn wirtualnych z systemem Docker na jednym z dystrybucji systemu Linux, które są zgodne z usługą Batch. Jeśli wybierzesz opcję udostępnienia niestandardowego obrazu systemu Linux, zobacz instrukcje w temacie [Używanie zarządzanego obrazu niestandardowego do tworzenia puli maszyn wirtualnych](batch-custom-images.md).

Aby obsłużyć obsługę platformy Docker w obrazie niestandardowym, zainstaluj [platformę Docker Community Edition (CE)](https://www.docker.com/community-edition) lub [platformę Docker Enterprise Edition (EE)](https://www.docker.com/enterprise-edition).

Dodatkowe zagadnienia dotyczące korzystania z niestandardowego obrazu systemu Linux:

- Aby skorzystać z wydajności procesora GPU dla rozmiarów serii N w przypadku korzystania z obrazu niestandardowego, Zainstaluj wstępnie sterowniki firmy NVIDIA. Ponadto należy zainstalować narzędzie aparat platformy Docker dla procesorów GPU firmy NVIDIA, [platformy Docker firmy NVIDIA](https://github.com/NVIDIA/nvidia-docker).

- Aby uzyskać dostęp do sieci usługi Azure RDMA, Użyj rozmiaru maszyny wirtualnej z obsługą funkcji RDMA. Niezbędne sterowniki RDMA są instalowane w obrazach CentOS HPC i Ubuntu obsługiwanych przez program Batch. Do uruchomienia obciążeń MPI może być wymagana dodatkowa konfiguracja. Zobacz [Używanie wystąpień obsługujących funkcję RDMA lub GPU w puli wsadowej](batch-pool-compute-intensive-sizes.md).

## <a name="container-configuration-for-batch-pool"></a>Konfiguracja kontenera dla puli usługi Batch

Aby umożliwić puli wsadowej uruchamianie obciążeń kontenera, należy określić ustawienia [ContainerConfiguration](/dotnet/api/microsoft.azure.batch.containerconfiguration) w obiekcie [VirtualMachineConfiguration](/dotnet/api/microsoft.azure.batch.virtualmachineconfiguration) puli. (Ten artykuł zawiera linki do dokumentacji interfejsu API programu .NET usługi Batch. Odpowiednie ustawienia znajdują się w interfejsie API usługi [Batch Python](/python/api/overview/azure/batch) .

Można utworzyć pulę z obsługą kontenerów z wstępnie pobranymi obrazami kontenerów, jak pokazano w poniższych przykładach. Proces ściągania (lub pobierania z wyprzedzeniem) umożliwia wstępne ładowanie obrazów kontenerów z koncentratora platformy Docker lub innego rejestru kontenerów w Internecie. Aby uzyskać najlepszą wydajność, użyj [usługi Azure Container Registry](../container-registry/container-registry-intro.md) w tym samym regionie, w którym jest to konto usługi Batch.

Zaletą pobierania obrazów kontenerów jest to, że podczas pierwszego uruchamiania zadań nie trzeba czekać na pobranie obrazu kontenera. Konfiguracja kontenera powoduje ściąganie obrazów kontenera do maszyn wirtualnych podczas tworzenia puli. Zadania uruchamiane w puli mogą następnie odwoływać się do listy obrazów kontenerów i opcji przebiegu kontenera.

### <a name="pool-without-prefetched-container-images"></a>Pula bez pobranych z obrazu kontenera

Aby skonfigurować pulę z obsługą kontenerów bez wstępnie pobranych obrazów kontenerów, zdefiniuj `ContainerConfiguration` i `VirtualMachineConfiguration` obiekty, jak pokazano w poniższych przykładach. W poniższych przykładach zamieszczono Ubuntu serwer Azure Batch programu na potrzeby obrazu pul kontenerów z portalu Marketplace.

```python
image_ref_to_use = batch.models.ImageReference(
    publisher='microsoft-azure-batch',
    offer='ubuntu-server-container',
    sku='16-04-lts',
    version='latest')

"""
Specify container configuration. This is required even though there are no prefetched images.
"""

container_conf = batch.models.ContainerConfiguration()

new_pool = batch.models.PoolAddParameter(
    id=pool_id,
    virtual_machine_configuration=batch.models.VirtualMachineConfiguration(
        image_reference=image_ref_to_use,
        container_configuration=container_conf,
        node_agent_sku_id='batch.node.ubuntu 16.04'),
    vm_size='STANDARD_D1_V2',
    target_dedicated_nodes=1)
...
```

```csharp
ImageReference imageReference = new ImageReference(
    publisher: "microsoft-azure-batch",
    offer: "ubuntu-server-container",
    sku: "16-04-lts",
    version: "latest");

// Specify container configuration. This is required even though there are no prefetched images.
ContainerConfiguration containerConfig = new ContainerConfiguration();

// VM configuration
VirtualMachineConfiguration virtualMachineConfiguration = new VirtualMachineConfiguration(
    imageReference: imageReference,
    nodeAgentSkuId: "batch.node.ubuntu 16.04");
virtualMachineConfiguration.ContainerConfiguration = containerConfig;

// Create pool
CloudPool pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    targetDedicatedComputeNodes: 1,
    virtualMachineSize: "STANDARD_D1_V2",
    virtualMachineConfiguration: virtualMachineConfiguration);
```

### <a name="prefetch-images-for-container-configuration"></a>Obrazy pobierania z wyprzedzeniem dla konfiguracji kontenera

Aby uzyskać pobieranie z wyprzedzeniem obrazów kontenerów w puli, Dodaj listę obrazów kontenerów ( `container_image_names` , w języku Python) do programu `ContainerConfiguration` .

Poniższy podstawowy przykład języka Python przedstawia sposób pobierania z wyprzedzeniem standardowego obrazu kontenera Ubuntu z usługi [Docker Hub](https://hub.docker.com).

```python
image_ref_to_use = batch.models.ImageReference(
    publisher='microsoft-azure-batch',
    offer='ubuntu-server-container',
    sku='16-04-lts',
    version='latest')

"""
Specify container configuration, fetching the official Ubuntu container image from Docker Hub.
"""

container_conf = batch.models.ContainerConfiguration(
    container_image_names=['ubuntu'])

new_pool = batch.models.PoolAddParameter(
    id=pool_id,
    virtual_machine_configuration=batch.models.VirtualMachineConfiguration(
        image_reference=image_ref_to_use,
        container_configuration=container_conf,
        node_agent_sku_id='batch.node.ubuntu 16.04'),
    vm_size='STANDARD_D1_V2',
    target_dedicated_nodes=1)
...
```

W poniższym przykładzie w języku C# założono, że chcesz wykonać pobieranie z obrazu TensorFlow z usługi [Docker Hub](https://hub.docker.com). Ten przykład zawiera zadanie uruchamiania uruchomione na hoście maszyny wirtualnej w węzłach puli. Zadanie uruchamiania można uruchomić na hoście, na przykład w celu zainstalowania serwera plików, do którego można uzyskać dostęp z kontenerów.

```csharp
ImageReference imageReference = new ImageReference(
    publisher: "microsoft-azure-batch",
    offer: "ubuntu-server-container",
    sku: "16-04-lts",
    version: "latest");

ContainerRegistry containerRegistry = new ContainerRegistry(
    registryServer: "https://hub.docker.com",
    userName: "UserName",
    password: "YourPassword"                
);

// Specify container configuration, prefetching Docker images
ContainerConfiguration containerConfig = new ContainerConfiguration();
containerConfig.ContainerImageNames = new List<string> { "tensorflow/tensorflow:latest-gpu" };
containerConfig.ContainerRegistries = new List<ContainerRegistry> { containerRegistry };

// VM configuration
VirtualMachineConfiguration virtualMachineConfiguration = new VirtualMachineConfiguration(
    imageReference: imageReference,
    nodeAgentSkuId: "batch.node.ubuntu 16.04");
virtualMachineConfiguration.ContainerConfiguration = containerConfig;

// Set a native host command line start task
StartTask startTaskContainer = new StartTask( commandLine: "<native-host-command-line>" );

// Create pool
CloudPool pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    virtualMachineSize: "Standard_NC6",
    virtualMachineConfiguration: virtualMachineConfiguration);

// Start the task in the pool
pool.StartTask = startTaskContainer;
...
```

### <a name="prefetch-images-from-a-private-container-registry"></a>Pobieranie z wyprzedzeniem obrazów z prywatnego rejestru kontenerów

Można również uzyskać pobieranie z wyprzedzeniem obrazów kontenerów przez uwierzytelnienie na prywatnym serwerze rejestru kontenerów. W poniższych przykładach obiekty i pobierają z `ContainerConfiguration` `VirtualMachineConfiguration` wyprzedzeniem prywatny obraz TensorFlow z prywatnego rejestru kontenerów platformy Azure. Odwołanie do obrazu jest takie samo jak w poprzednim przykładzie.

```python
image_ref_to_use = batch.models.ImageReference(
        publisher='microsoft-azure-batch',
        offer='ubuntu-server-container',
        sku='16-04-lts',
        version='latest')

# Specify a container registry
container_registry = batch.models.ContainerRegistry(
        registry_server="myRegistry.azurecr.io",
        user_name="myUsername",
        password="myPassword")

# Create container configuration, prefetching Docker images from the container registry
container_conf = batch.models.ContainerConfiguration(
        container_image_names = ["myRegistry.azurecr.io/samples/myImage"],
        container_registries =[container_registry])

new_pool = batch.models.PoolAddParameter(
            id="myPool",
            virtual_machine_configuration=batch.models.VirtualMachineConfiguration(
                image_reference=image_ref_to_use,
                container_configuration=container_conf,
                node_agent_sku_id='batch.node.ubuntu 16.04'),
            vm_size='STANDARD_D1_V2',
            target_dedicated_nodes=1)
```

```csharp
// Specify a container registry
ContainerRegistry containerRegistry = new ContainerRegistry(
    registryServer: "myContainerRegistry.azurecr.io",
    userName: "myUserName",
    password: "myPassword");

// Create container configuration, prefetching Docker images from the container registry
ContainerConfiguration containerConfig = new ContainerConfiguration();
containerConfig.ContainerImageNames = new List<string> {
        "myContainerRegistry.azurecr.io/tensorflow/tensorflow:latest-gpu" };
containerConfig.ContainerRegistries = new List<ContainerRegistry> { containerRegistry } );

// VM configuration
VirtualMachineConfiguration virtualMachineConfiguration = new VirtualMachineConfiguration(
    imageReference: imageReference,
    nodeAgentSkuId: "batch.node.ubuntu 16.04");
virtualMachineConfiguration.ContainerConfiguration = containerConfig;

// Create pool
CloudPool pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    targetDedicatedComputeNodes: 4,
    virtualMachineSize: "Standard_NC6",
    virtualMachineConfiguration: virtualMachineConfiguration);
...
```

## <a name="container-settings-for-the-task"></a>Ustawienia kontenera dla zadania

Aby uruchomić zadanie kontenera w puli z obsługą kontenerów, określ ustawienia specyficzne dla kontenera. Ustawienia obejmują obraz do użycia, rejestr i opcje przebiegu kontenera.

- Użyj `ContainerSettings` właściwości klas zadań, aby skonfigurować ustawienia specyficzne dla kontenera. Te ustawienia są definiowane przez klasę [TaskContainerSettings](/dotnet/api/microsoft.azure.batch.taskcontainersettings) . Należy pamiętać, że `--rm` opcja kontenera nie wymaga dodatkowej `--runtime` opcji, ponieważ jest ona traktowana przez partię.

- W przypadku uruchamiania zadań na obrazach kontenerów zadanie w [chmurze](/dotnet/api/microsoft.azure.batch.cloudtask) i [Menedżer zadań](/dotnet/api/microsoft.azure.batch.cloudjob.jobmanagertask) wymagają ustawień kontenera. Jednak zadanie [Uruchom zadanie](/dotnet/api/microsoft.azure.batch.starttask), [zadanie przygotowania zadania](/dotnet/api/microsoft.azure.batch.cloudjob.jobpreparationtask)i [zwolnienia zadania](/dotnet/api/microsoft.azure.batch.cloudjob.jobreleasetask) nie wymaga ustawień kontenera (oznacza to, że mogą być uruchamiane w ramach kontekstu kontenera lub bezpośrednio w węźle).

- W przypadku systemu Windows zadania muszą być uruchamiane z [ElevationLevel](/rest/api/batchservice/task/add#elevationlevel) ustawionym na `admin` . 

- W przypadku systemu Linux program Batch mapuje uprawnienie użytkownika/grupy do kontenera. Jeśli dostęp do dowolnego folderu w kontenerze wymaga uprawnień administratora, może być konieczne uruchomienie zadania jako zakresu puli z poziomem podniesienia uprawnień administratora. Pozwoli to zagwarantować, że partia uruchomi zadanie jako element główny w kontekście kontenera. W przeciwnym razie użytkownik niebędący administratorem może nie mieć dostępu do tych folderów.

- W przypadku pul kontenerów z sprzętem opartym na procesorach GPU funkcja Batch automatycznie włączy procesor GPU dla zadań kontenera, więc nie należy uwzględniać `–gpus` argumentu.

### <a name="container-task-command-line"></a>Wiersz polecenia zadania kontenera

Po uruchomieniu zadania kontenera usługi Batch automatycznie używa polecenia [Docker Create](https://docs.docker.com/engine/reference/commandline/create/) w celu utworzenia kontenera przy użyciu obrazu określonego w zadaniu. Następnie usługa Batch kontroluje wykonywanie zadań w kontenerze.

Podobnie jak w przypadku zadań wsadowych nienależących do kontenera, należy ustawić wiersz polecenia dla zadania kontenera. Ponieważ usługa Batch automatycznie tworzy kontener, wiersz polecenia określa tylko polecenie lub polecenia, które zostaną uruchomione w kontenerze.

Jeśli obraz kontenera zadania usługi Batch jest skonfigurowany za pomocą skryptu [punktu wejścia](https://docs.docker.com/engine/reference/builder/#exec-form-entrypoint-example) , można ustawić wiersz polecenia, aby używał domyślnego punktu wejścia lub zastąpić go:

- Aby użyć domyślnego punktu wejścia obrazu kontenera, należy ustawić wiersz polecenia zadania na pusty ciąg `""` .

- Aby zastąpić domyślny punkt wejścia lub, jeśli obraz nie ma punktu wejścia, ustaw wiersz polecenia odpowiedni dla kontenera, na przykład `/app/myapp` lub `/bin/sh -c python myscript.py` .

Opcjonalne [ContainerRunOptions](/dotnet/api/microsoft.azure.batch.taskcontainersettings.containerrunoptions) są dodatkowymi argumentami `docker create` , które są używane przez partię do tworzenia i uruchamiania kontenera. Na przykład aby ustawić katalog roboczy dla kontenera, należy ustawić `--workdir <directory>` opcję. Dodatkowe opcje można znaleźć w temacie The [Docker Create](https://docs.docker.com/engine/reference/commandline/create/) Reference.

### <a name="container-task-working-directory"></a>Katalog roboczy zadania kontenera

Zadanie kontenera wsadowego jest wykonywane w katalogu roboczym w kontenerze, który jest bardzo podobny do wsadowego tworzenia zadań (nie kontener). Należy zauważyć, że ten katalog roboczy różni się od [WORKDIR](https://docs.docker.com/engine/reference/builder/#workdir) , jeśli jest skonfigurowany na obrazie, lub domyślnego katalogu roboczego kontenera ( `C:\`  w kontenerze systemu Windows lub `/` w kontenerze Linux).

Zadanie kontenera usługi Batch:

- Wszystkie katalogi cyklicznie poniżej `AZ_BATCH_NODE_ROOT_DIR` węzła w węźle hosta (katalogu głównego katalogów Azure Batch) są mapowane do kontenera
- Wszystkie zmienne środowiskowe zadań są mapowane do kontenera
- Katalog roboczy zadania `AZ_BATCH_TASK_WORKING_DIR` w węźle jest ustawiany tak samo jak w przypadku zwykłego zadania i mapowany do kontenera.

Mapowania te umożliwiają pracy z zadaniami kontenera w taki sam sposób, jak zadania inne niż kontenery. Na przykład zainstaluj aplikacje przy użyciu pakietów aplikacji, uzyskaj dostęp do plików zasobów z usługi Azure Storage, Użyj ustawień środowiska zadania i Utrwalaj pliki wyjściowe zadania po zatrzymaniu kontenera.

### <a name="troubleshoot-container-tasks"></a>Rozwiązywanie problemów z zadaniami kontenera

Jeśli zadanie kontenera nie działa zgodnie z oczekiwaniami, może być konieczne uzyskanie informacji na temat konfiguracji WORKDIR lub punktu wejścia obrazu kontenera. Aby wyświetlić konfigurację, uruchom polecenie [Docker Image inspekcji](https://docs.docker.com/engine/reference/commandline/image_inspect/) .

W razie konieczności dostosuj ustawienia zadania kontenera na podstawie obrazu:

- Określ ścieżkę bezwzględną w wierszu polecenia zadania. Jeśli domyślny punkt wejścia obrazu jest używany dla wiersza polecenia zadania, upewnij się, że ustawiono ścieżkę bezwzględną.
- W opcjach przebiegu kontenera zadania Zmień katalog roboczy, aby pasował do WORKDIR w obrazie. Na przykład ustaw `--workdir /app` .

## <a name="container-task-examples"></a>Przykłady zadań kontenera

Poniższy fragment kodu w języku Python przedstawia podstawowy wiersz polecenia uruchamiany w kontenerze utworzonym na podstawie fikcyjnego obrazu pobranego z usługi Docker Hub. W tym miejscu `--rm` opcja kontenera usuwa kontener po zakończeniu zadania, a `--workdir` opcja ustawia katalog roboczy. Wiersz polecenia zastępuje punkt wejścia kontenera za pomocą prostego polecenia powłoki, które zapisuje niewielki plik w katalogu roboczym zadania na hoście.

```python
task_id = 'sampletask'
task_container_settings = batch.models.TaskContainerSettings(
    image_name='myimage',
    container_run_options='--rm --workdir /')
task = batch.models.TaskAddParameter(
    id=task_id,
    command_line='/bin/sh -c \"echo \'hello world\' > $AZ_BATCH_TASK_WORKING_DIR/output.txt\"',
    container_settings=task_container_settings
)
```

W poniższym przykładzie w języku C# przedstawiono podstawowe ustawienia kontenera dla zadania w chmurze:

```csharp
// Simple container task command
string cmdLine = "c:\\app\\myApp.exe";

TaskContainerSettings cmdContainerSettings = new TaskContainerSettings (
    imageName: "myimage",
    containerRunOptions: "--rm --workdir c:\\app"
    );

CloudTask containerTask = new CloudTask (
    id: "Task1",
    commandline: cmdLine);
containerTask.ContainerSettings = cmdContainerSettings;
```

## <a name="next-steps"></a>Następne kroki

- Aby ułatwić wdrażanie obciążeń kontenerów na Azure Batch za pomocą [przepisów stoczniowych](https://github.com/Azure/batch-shipyard/tree/master/recipes), zobacz zestaw narzędzi usługi [Batch stoczni](https://github.com/Azure/batch-shipyard) .
- Aby uzyskać informacje na temat instalowania i używania platformy Docker CE w systemie Linux, zobacz dokumentację [platformy Docker](https://docs.docker.com/engine/installation/) .
- Dowiedz się, jak [utworzyć pulę maszyn wirtualnych przy użyciu zarządzanego obrazu niestandardowego](batch-custom-images.md).
- Dowiedz się więcej o [projekcie Moby](https://mobyproject.org/), strukturze do tworzenia systemów opartych na kontenerach.
