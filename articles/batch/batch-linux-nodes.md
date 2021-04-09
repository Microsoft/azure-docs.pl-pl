---
title: Uruchamianie systemu Linux na węzłach obliczeniowych maszyny wirtualnej
description: Dowiedz się, jak przetwarzać obciążenia obliczeniowe równoległe dla pul maszyn wirtualnych z systemem Linux w Azure Batch.
ms.topic: how-to
ms.date: 01/21/2021
ms.custom: H1Hack27Feb2017, devx-track-python, devx-track-csharp
ms.openlocfilehash: c711ec0d035b9b59ec7628a51fe3cff26de358bc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98683704"
---
# <a name="provision-linux-compute-nodes-in-batch-pools"></a>Inicjowanie obsługi węzłów obliczeniowych systemu Linux w pulach wsadowym

Za pomocą Azure Batch można uruchamiać obciążenia obliczeń równoległych na maszynach wirtualnych z systemem Linux i Windows. W tym artykule szczegółowo opisano sposób tworzenia pul węzłów obliczeniowych systemu Linux w usłudze Batch przy użyciu bibliotek klienckich [Python](https://pypi.python.org/pypi/azure-batch) i [Batch platformy .NET](/dotnet/api/microsoft.azure.batch) . 

## <a name="virtual-machine-configuration"></a>Konfiguracja maszyny wirtualnej

Podczas tworzenia puli węzłów obliczeniowych w usłudze Batch dostępne są dwie opcje, z których można wybrać rozmiar węzła i system operacyjny: konfigurację Cloud Services i konfigurację maszyny wirtualnej. Pule [konfiguracji maszyny wirtualnej](nodes-and-pools.md#virtual-machine-configuration) składają się z maszyn wirtualnych platformy Azure, które mogą być tworzone na podstawie obrazów systemu Linux lub Windows. Podczas tworzenia puli z konfiguracją maszyny wirtualnej należy określić [dostępny rozmiar węzła obliczeniowego](../virtual-machines/sizes.md), odwołanie do obrazu maszyny wirtualnej, które ma zostać zainstalowane w węzłach, oraz jednostkę SKU agenta węzła usługi Batch (program uruchomiony w każdym węźle i udostępnia interfejs między węzłem a usługą Batch).

### <a name="virtual-machine-image-reference"></a>Odwołanie do obrazu maszyny wirtualnej

Usługa Batch używa [zestawów skalowania maszyn wirtualnych](../virtual-machine-scale-sets/overview.md) w celu zapewnienia węzłów obliczeniowych w konfiguracji maszyny wirtualnej. Możesz określić obraz z [witryny Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/compute?filters=virtual-machine-images&page=1)lub [użyć galerii obrazów udostępnionych w celu przygotowania obrazu niestandardowego](batch-sig-images.md).

Gdy tworzysz odwołanie do obrazu maszyny wirtualnej, musisz określić następujące właściwości:

| **Właściwość odwołania do obrazu** | **Przykład** |
| --- | --- |
| Publisher |Canonical |
| Oferta |UbuntuServer |
| SKU |18,04 – LTS |
| Wersja |latest |

> [!TIP]
> Więcej informacji o tych właściwościach i sposobach określania obrazów z witryny Marketplace w temacie [Znajdowanie obrazów maszyn wirtualnych z systemem Linux w portalu Azure Marketplace przy użyciu interfejsu wiersza polecenia platformy Azure](../virtual-machines/linux/cli-ps-findimage.md). Należy pamiętać, że niektóre obrazy z witryny Marketplace nie są obecnie zgodne z usługą Batch.

### <a name="list-of-virtual-machine-images"></a>Lista obrazów maszyn wirtualnych

Nie wszystkie obrazy z portalu Marketplace są zgodne z aktualnie dostępnymi agentami węzłów partii. Aby wyświetlić listę wszystkich obsługiwanych obrazów maszyn wirtualnych w portalu Marketplace dla usługi Batch i odpowiednich jednostek SKU agenta węzła, użyj [list_supported_images](/python/api/azure-batch/azure.batch.operations.AccountOperations#list-supported-images-account-list-supported-images-options-none--custom-headers-none--raw-false----operation-config-) (Python), [ListSupportedImages](/dotnet/api/microsoft.azure.batch.pooloperations.listsupportedimages) (Batch .NET) lub odpowiedniego interfejsu API w innym zestawie SDK języka.

### <a name="node-agent-sku"></a>Jednostka SKU agenta węzła

[Agent węzła usługi Batch](https://github.com/Azure/Batch/blob/master/changelogs/nodeagent/CHANGELOG.md) jest programem uruchamianym w każdym węźle w puli i udostępnia interfejs poleceń i kontroli między węzłem a usługą Batch. Istnieją różne implementacje agenta węzła, nazywane jednostkami SKU dla różnych systemów operacyjnych. Zasadniczo podczas tworzenia konfiguracji maszyny wirtualnej należy najpierw określić odwołanie do obrazu maszyny wirtualnej, a następnie określić agenta węzła, który ma zostać zainstalowany na obrazie. Zazwyczaj każda jednostka SKU agenta węzła jest zgodna z wieloma obrazami maszyn wirtualnych. Poniżej przedstawiono kilka przykładów jednostek SKU agenta węzła:

- Batch. Node. Ubuntu 18,04
- Batch. Node. CentOS 7
- Batch. Node. Windows amd64

## <a name="create-a-linux-pool-batch-python"></a>Tworzenie puli systemu Linux: Batch Python

Poniższy fragment kodu przedstawia przykład korzystania z [biblioteki klienta Microsoft Azure Batch dla języka Python](https://pypi.python.org/pypi/azure-batch) w celu utworzenia puli węzłów obliczeniowych serwera Ubuntu. Aby uzyskać więcej informacji na temat modułu Batch Python, zobacz [dokumentację referencyjną](/python/api/overview/azure/batch).

Ten fragment kodu tworzy [elementu imagereference](/python/api/azure-mgmt-batch/azure.mgmt.batch.models.imagereference) jawnie i określa każdą z jej właściwości (Wydawca, oferta, jednostka SKU, wersja). Jednak w kodzie produkcyjnym zalecamy użycie metody [list_supported_images](/python/api/azure-batch/azure.batch.operations.AccountOperations#list-supported-images-account-list-supported-images-options-none--custom-headers-none--raw-false----operation-config-) , aby wybrać spośród dostępnych kombinacji jednostki SKU agenta węzła w czasie wykonywania.

```python
# Import the required modules from the
# Azure Batch Client Library for Python
import azure.batch.batch_service_client as batch
import azure.batch.batch_auth as batchauth
import azure.batch.models as batchmodels

# Specify Batch account credentials
account = "<batch-account-name>"
key = "<batch-account-key>"
batch_url = "<batch-account-url>"

# Pool settings
pool_id = "LinuxNodesSamplePoolPython"
vm_size = "STANDARD_D2_V3"
node_count = 1

# Initialize the Batch client
creds = batchauth.SharedKeyCredentials(account, key)
config = batch.BatchServiceClientConfiguration(creds, batch_url)
client = batch.BatchServiceClient(creds, batch_url)

# Create the unbound pool
new_pool = batchmodels.PoolAddParameter(id=pool_id, vm_size=vm_size)
new_pool.target_dedicated = node_count

# Configure the start task for the pool
start_task = batchmodels.StartTask()
start_task.run_elevated = True
start_task.command_line = "printenv AZ_BATCH_NODE_STARTUP_DIR"
new_pool.start_task = start_task

# Create an ImageReference which specifies the Marketplace
# virtual machine image to install on the nodes
ir = batchmodels.ImageReference(
    publisher="Canonical",
    offer="UbuntuServer",
    sku="18.04-LTS",
    version="latest")

# Create the VirtualMachineConfiguration, specifying
# the VM image reference and the Batch node agent
# to install on the node
vmc = batchmodels.VirtualMachineConfiguration(
    image_reference=ir,
    node_agent_sku_id="batch.node.ubuntu 18.04")

# Assign the virtual machine configuration to the pool
new_pool.virtual_machine_configuration = vmc

# Create pool in the Batch service
client.pool.add(new_pool)
```

Jak wspomniano wcześniej, zalecamy użycie metody [list_supported_images](/python/api/azure-batch/azure.batch.operations.AccountOperations#list-supported-images-account-list-supported-images-options-none--custom-headers-none--raw-false----operation-config-) , aby dynamicznie wybierać z aktualnie obsługiwane kombinacje agenta węzłów/obrazu portalu Marketplace (zamiast bezpośrednio tworzyć [elementu imagereference](/python/api/azure-mgmt-batch/azure.mgmt.batch.models.imagereference) ). Poniższy fragment kodu w języku Python pokazuje, jak używać tej metody.

```python
# Get the list of supported images from the Batch service
images = client.account.list_supported_images()

# Obtain the desired image reference
image = None
for img in images:
  if (img.image_reference.publisher.lower() == "canonical" and
        img.image_reference.offer.lower() == "ubuntuserver" and
        img.image_reference.sku.lower() == "18.04-lts"):
    image = img
    break

if image is None:
  raise RuntimeError('invalid image reference for desired configuration')

# Create the VirtualMachineConfiguration, specifying the VM image
# reference and the Batch node agent to be installed on the node
vmc = batchmodels.VirtualMachineConfiguration(
    image_reference=image.image_reference,
    node_agent_sku_id=image.node_agent_sku_id)
```

## <a name="create-a-linux-pool-batch-net"></a>Tworzenie puli systemu Linux: Batch .NET

Poniższy fragment kodu przedstawia przykład użycia biblioteki klienta [programu Batch .NET](https://www.nuget.org/packages/Microsoft.Azure.Batch/) do tworzenia puli węzłów obliczeniowych serwera Ubuntu. Aby uzyskać więcej informacji na temat usługi Batch .NET, zobacz [dokumentację referencyjną](/dotnet/api/microsoft.azure.batch).

Poniższy fragment kodu używa metody [PoolOperations. ListSupportedImages](/dotnet/api/microsoft.azure.batch.pooloperations.listsupportedimages) do wybrania z listy obecnie obsługiwanych kombinacji jednostek SKU agenta i węzła w portalu Marketplace. Ta technika jest zalecana, ponieważ lista obsługiwanych kombinacji może ulec zmianie od czasu do czasu. Najczęściej są dodawane obsługiwane kombinacje.

```csharp
// Pool settings
const string poolId = "LinuxNodesSamplePoolDotNet";
const string vmSize = "STANDARD_D2_V3";
const int nodeCount = 1;

// Obtain a collection of all available node agent SKUs.
// This allows us to select from a list of supported
// VM image/node agent combinations.
List<ImageInformation> images =
    batchClient.PoolOperations.ListSupportedImages().ToList();

// Find the appropriate image information
ImageInformation image = null;
foreach (var img in images)
{
    if (img.ImageReference.Publisher == "Canonical" &&
        img.ImageReference.Offer == "UbuntuServer" &&
        img.ImageReference.Sku == "18.04-LTS")
    {
        image = img;
        break;
    }
}

// Create the VirtualMachineConfiguration for use when actually
// creating the pool
VirtualMachineConfiguration virtualMachineConfiguration =
    new VirtualMachineConfiguration(image.ImageReference, image.NodeAgentSkuId);

// Create the unbound pool object using the VirtualMachineConfiguration
// created above
CloudPool pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    virtualMachineSize: vmSize,
    virtualMachineConfiguration: virtualMachineConfiguration,
    targetDedicatedComputeNodes: nodeCount);

// Commit the pool to the Batch service
await pool.CommitAsync();
```

Mimo że poprzedni fragment kodu używa metody [PoolOperations. istSupportedImages](/dotnet/api/microsoft.azure.batch.pooloperations.listsupportedimages) do dynamicznego wyświetlania i wybierania obsługiwanych kombinacji jednostek SKU agenta i węzła (zalecane), można również skonfigurować [elementu imagereference](/dotnet/api/microsoft.azure.batch.imagereference) jawnie:

```csharp
ImageReference imageReference = new ImageReference(
    publisher: "Canonical",
    offer: "UbuntuServer",
    sku: "18.04-LTS",
    version: "latest");
```

## <a name="connect-to-linux-nodes-using-ssh"></a>Łączenie z węzłami systemu Linux przy użyciu protokołu SSH

Podczas programowania lub rozwiązywania problemów może być konieczne zalogowanie się do węzłów w puli. W przeciwieństwie do węzłów obliczeniowych systemu Windows nie można używać Remote Desktop Protocol (RDP) do nawiązywania połączenia z węzłami z systemem Linux. Zamiast tego usługa Batch umożliwia dostęp do protokołu SSH w każdym węźle dla połączenia zdalnego.

Poniższy fragment kodu w języku Python tworzy użytkownika na każdym węźle w puli, który jest wymagany do połączenia zdalnego. Następnie drukuje informacje o połączeniu Secure Shell (SSH) dla każdego węzła.

```python
import datetime
import getpass
import azure.batch.batch_service_client as batch
import azure.batch.batch_auth as batchauth
import azure.batch.models as batchmodels

# Specify your own account credentials
batch_account_name = ''
batch_account_key = ''
batch_account_url = ''

# Specify the ID of an existing pool containing Linux nodes
# currently in the 'idle' state
pool_id = ''

# Specify the username and prompt for a password
username = 'linuxuser'
password = getpass.getpass()

# Create a BatchClient
credentials = batchauth.SharedKeyCredentials(
    batch_account_name,
    batch_account_key
)
batch_client = batch.BatchServiceClient(
    credentials,
    base_url=batch_account_url
)

# Create the user that will be added to each node in the pool
user = batchmodels.ComputeNodeUser(username)
user.password = password
user.is_admin = True
user.expiry_time = \
    (datetime.datetime.today() + datetime.timedelta(days=30)).isoformat()

# Get the list of nodes in the pool
nodes = batch_client.compute_node.list(pool_id)

# Add the user to each node in the pool and print
# the connection information for the node
for node in nodes:
    # Add the user to the node
    batch_client.compute_node.add_user(pool_id, node.id, user)

    # Obtain SSH login information for the node
    login = batch_client.compute_node.get_remote_login_settings(pool_id,
                                                                node.id)

    # Print the connection info for the node
    print("{0} | {1} | {2} | {3}".format(node.id,
                                         node.state,
                                         login.remote_login_ip_address,
                                         login.remote_login_port))
```

Ten kod będzie zawierał dane wyjściowe podobne do poniższego przykładu. W takim przypadku pula zawiera cztery węzły systemu Linux.

```
Password:
tvm-1219235766_1-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50000
tvm-1219235766_2-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50003
tvm-1219235766_3-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50002
tvm-1219235766_4-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50001
```

Zamiast hasła można określić klucz publiczny SSH podczas tworzenia użytkownika w węźle. W zestawie SDK języka Python Użyj parametru **ssh_public_key** w [ComputeNodeUser](/python/api/azure-batch/azure.batch.models.computenodeuser). W programie .NET Użyj właściwości [ComputeNodeUser. SshPublicKey](/dotnet/api/microsoft.azure.batch.computenodeuser.sshpublickey#Microsoft_Azure_Batch_ComputeNodeUser_SshPublicKey) .

## <a name="pricing"></a>Ceny

Azure Batch jest oparta na platformie Azure Cloud Services i technologii Azure Virtual Machines. Sama usługa Batch jest oferowana bezpłatnie, co oznacza, że opłaty są naliczone wyłącznie za zasoby obliczeniowe (i powiązane z nimi koszty, które wiążą się z tym, że korzystasz z rozwiązań wsadowych). Po wybraniu opcji **Konfiguracja maszyny wirtualnej** opłata jest naliczana na podstawie [Virtual Machinesj struktury cenowej](https://azure.microsoft.com/pricing/details/virtual-machines/) .

W przypadku wdrażania aplikacji w węzłach usługi Batch przy użyciu [pakietów aplikacji](batch-application-packages.md)opłaty są naliczone również za zasoby magazynu platformy Azure zużywane przez pakiety aplikacji.

## <a name="next-steps"></a>Następne kroki

- Zapoznaj się z [przykładami kodu](https://github.com/Azure/azure-batch-samples/tree/master/Python/Batch) w języku Python w [repozytorium Azure-Batch-Samples](https://github.com/Azure/azure-batch-samples) w witrynie GitHub, aby dowiedzieć się, jak wykonywać typowe operacje wsadowe, takie jak pule, zadania i tworzenie zadań. [Plik Readme](https://github.com/Azure/azure-batch-samples/blob/master/Python/Batch/README.md) zawierający przykłady języka Python zawiera szczegółowe informacje o sposobie instalowania wymaganych pakietów.
- Dowiedz się więcej o korzystaniu z [maszyn wirtualnych o niskim priorytecie](batch-low-pri-vms.md) w usłudze Batch.
