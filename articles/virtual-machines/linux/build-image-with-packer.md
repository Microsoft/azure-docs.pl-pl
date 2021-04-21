---
title: Tworzenie obrazów maszyn wirtualnych platformy Azure z systemem Linux przy użyciu narzędzia Packer
description: Dowiedz się, jak używać narzędzia Packer do tworzenia obrazów maszyn wirtualnych z systemem Linux na platformie Azure
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 05/07/2019
ms.author: cynthn
ms.collection: linux
ms.openlocfilehash: 1b40646109265b803945b43d7cc855688c5b47c5
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107764661"
---
# <a name="how-to-use-packer-to-create-linux-virtual-machine-images-in-azure"></a>Jak używać narzędzia Packer do tworzenia obrazów maszyn wirtualnych z systemem Linux na platformie Azure
Każda maszyna wirtualna na platformie Azure jest tworzona na podstawie obrazu, który definiuje dystrybucję systemu Linux i wersję systemu operacyjnego. Obrazy mogą zawierać wstępnie zainstalowane aplikacje i konfiguracje. Aplikacja Azure Marketplace wiele obrazów firmy Microsoft i innych firm dla najpopularniejszych dystrybucji i środowisk aplikacji. Można też tworzyć własne obrazy niestandardowe dostosowane do twoich potrzeb. W tym artykule szczegółowo opisano, jak używać narzędzia open source [do](https://www.packer.io/) definiowania i kompilowania obrazów niestandardowych na platformie Azure.

> [!NOTE]
> Platforma Azure ma teraz usługę Azure Image Builder (wersja zapoznawcza) do definiowania i tworzenia własnych obrazów niestandardowych. Usługa Azure Image Builder jest zbudowana na platformie Packer, dzięki czemu można nawet używać z nim istniejących skryptów aprowrowatora powłoki usługi Packer. Aby rozpocząć pracę z usługą Azure Image Builder, zobacz Tworzenie maszyny wirtualnej z systemem [Linux przy użyciu usługi Azure Image Builder.](image-builder.md)


## <a name="create-azure-resource-group"></a>Tworzenie grupy zasobów platformy Azure
Podczas procesu kompilacji narzędzie Packer tworzy tymczasowe zasoby platformy Azure podczas kompilowania źródłowej maszyny wirtualnej. Aby przechwycić źródłową maszynę wirtualną do użycia jako obraz, należy zdefiniować grupę zasobów. Dane wyjściowe z procesu kompilacji programu Packer są przechowywane w tej grupie zasobów.

Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group). Poniższy przykład tworzy grupę zasobów o *nazwie myResourceGroup* w *lokalizacji eastus:*

```azurecli
az group create -n myResourceGroup -l eastus
```


## <a name="create-azure-credentials"></a>Tworzenie poświadczeń platformy Azure
Usługa Packer uwierzytelnia się na platformie Azure przy użyciu jednostki usługi. Jednostki usługi platformy Azure to tożsamość zabezpieczeń, których można używać z aplikacjami, usługami i narzędziami automatyzacji, np. Packer. Możesz kontrolować i definiować uprawnienia do operacji, które może wykonywać jednostki usługi na platformie Azure.

Utwórz jednostkę usługi za pomocą [az ad sp create-for-rbac](/cli/azure/ad/sp) i wyprowadz poświadczenia wymagane przez usługę Packer:

```azurecli
az ad sp create-for-rbac --query "{ client_id: appId, client_secret: password, tenant_id: tenant }"
```

Przykładowe dane wyjściowe z poprzednich poleceń są następujące:

```output
{
    "client_id": "f5b6a5cf-fbdf-4a9f-b3b8-3c2cd00225a4",
    "client_secret": "0e760437-bf34-4aad-9f8d-870be799c55d",
    "tenant_id": "72f988bf-86f1-41af-91ab-2d7cd011db47"
}
```

Aby uwierzytelnić się na platformie Azure, musisz również uzyskać identyfikator subskrypcji platformy Azure za pomocą [narzędzia az account show](/cli/azure/account):

```azurecli
az account show --query "{ subscription_id: id }"
```

W następnym kroku użyjemy danych wyjściowych z tych dwóch poleceń.


## <a name="define-packer-template"></a>Definiowanie szablonu usługi Packer
Aby tworzyć obrazy, należy utworzyć szablon jako plik JSON. W szablonie definiuje się konstruktorów i aprowizatorów, którzy wykonują rzeczywisty proces kompilacji. Narzędzie Packer ma [aprowizator](https://www.packer.io/docs/builders/azure.html) dla platformy Azure, który umożliwia definiowanie zasobów platformy Azure, takich jak poświadczenia jednostki usługi utworzone w poprzednim kroku.

Utwórz plik o *nazwieubuntu.jsi* wklej następującą zawartość. Wprowadź własne wartości dla następujących wartości:

| Parametr                           | Gdzie można uzyskać |
|-------------------------------------|----------------------------------------------------|
| *client_id*                         | Pierwszy wiersz danych wyjściowych polecenia `az ad sp` create — *appId* |
| *client_secret*                     | Drugi wiersz danych wyjściowych polecenia `az ad sp` create — *hasło* |
| *tenant_id*                         | Trzeci wiersz danych wyjściowych polecenia `az ad sp` create — *tenant* |
| *subscription_id*                   | Dane `az account show` wyjściowe polecenia |
| *managed_image_resource_group_name* | Nazwa grupy zasobów utworzonej w pierwszym kroku |
| *managed_image_name*                | Nazwa utworzonego obrazu dysku zarządzanego |


```json
{
  "builders": [{
    "type": "azure-arm",

    "client_id": "f5b6a5cf-fbdf-4a9f-b3b8-3c2cd00225a4",
    "client_secret": "0e760437-bf34-4aad-9f8d-870be799c55d",
    "tenant_id": "72f988bf-86f1-41af-91ab-2d7cd011db47",
    "subscription_id": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",

    "managed_image_resource_group_name": "myResourceGroup",
    "managed_image_name": "myPackerImage",

    "os_type": "Linux",
    "image_publisher": "Canonical",
    "image_offer": "UbuntuServer",
    "image_sku": "16.04-LTS",

    "azure_tags": {
        "dept": "Engineering",
        "task": "Image deployment"
    },

    "location": "East US",
    "vm_size": "Standard_DS2_v2"
  }],
  "provisioners": [{
    "execute_command": "chmod +x {{ .Path }}; {{ .Vars }} sudo -E sh '{{ .Path }}'",
    "inline": [
      "apt-get update",
      "apt-get upgrade -y",
      "apt-get -y install nginx",

      "/usr/sbin/waagent -force -deprovision+user && export HISTSIZE=0 && sync"
    ],
    "inline_shebang": "/bin/sh -x",
    "type": "shell"
  }]
}
```

Ten szablon tworzy obraz systemu Ubuntu 16.04 LTS, instaluje pakiet NGINX, a następnie coprowizuje maszynę wirtualną.

> [!NOTE]
> Jeśli rozszerzysz ten szablon, aby aprowizować poświadczenia użytkownika, dostosuj polecenie aprowizatora, które coprowizuje agenta platformy Azure do odczytu, `-deprovision` a nie `deprovision+user` .
> Flaga `+user` usuwa wszystkie konta użytkowników ze źródłowej maszyny wirtualnej.


## <a name="build-packer-image"></a>Obraz pakietu Build Packer
Jeśli nie masz jeszcze zainstalowanego programu Packer na komputerze lokalnym, postępuj zgodnie z instrukcjami [instalacji programu Packer.](https://www.packer.io/docs/install)

Skompilowanie obrazu przez określenie pliku szablonu usługi Packer w następujący sposób:

```bash
./packer build ubuntu.json
```

Przykładowe dane wyjściowe z poprzednich poleceń są następujące:

```output
azure-arm output will be in this color.

==> azure-arm: Running builder ...
    azure-arm: Creating Azure Resource Manager (ARM) client ...
==> azure-arm: Creating resource group ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-swtxmqm7ly’
==> azure-arm:  -> Location          : ‘East US’
==> azure-arm:  -> Tags              :
==> azure-arm:  ->> dept : Engineering
==> azure-arm:  ->> task : Image deployment
==> azure-arm: Validating deployment template ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-swtxmqm7ly’
==> azure-arm:  -> DeploymentName    : ‘pkrdpswtxmqm7ly’
==> azure-arm: Deploying deployment template ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-swtxmqm7ly’
==> azure-arm:  -> DeploymentName    : ‘pkrdpswtxmqm7ly’
==> azure-arm: Getting the VM’s IP address ...
==> azure-arm:  -> ResourceGroupName   : ‘packer-Resource-Group-swtxmqm7ly’
==> azure-arm:  -> PublicIPAddressName : ‘packerPublicIP’
==> azure-arm:  -> NicName             : ‘packerNic’
==> azure-arm:  -> Network Connection  : ‘PublicEndpoint’
==> azure-arm:  -> IP Address          : ‘40.76.218.147’
==> azure-arm: Waiting for SSH to become available...
==> azure-arm: Connected to SSH!
==> azure-arm: Provisioning with shell script: /var/folders/h1/ymh5bdx15wgdn5hvgj1wc0zh0000gn/T/packer-shell868574263
    azure-arm: WARNING! The waagent service will be stopped.
    azure-arm: WARNING! Cached DHCP leases will be deleted.
    azure-arm: WARNING! root password will be disabled. You will not be able to login as root.
    azure-arm: WARNING! /etc/resolvconf/resolv.conf.d/tail and /etc/resolvconf/resolv.conf.d/original will be deleted.
    azure-arm: WARNING! packer account and entire home directory will be deleted.
==> azure-arm: Querying the machine’s properties ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-swtxmqm7ly’
==> azure-arm:  -> ComputeName       : ‘pkrvmswtxmqm7ly’
==> azure-arm:  -> Managed OS Disk   : ‘/subscriptions/guid/resourceGroups/packer-Resource-Group-swtxmqm7ly/providers/Microsoft.Compute/disks/osdisk’
==> azure-arm: Powering off machine ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-swtxmqm7ly’
==> azure-arm:  -> ComputeName       : ‘pkrvmswtxmqm7ly’
==> azure-arm: Capturing image ...
==> azure-arm:  -> Compute ResourceGroupName : ‘packer-Resource-Group-swtxmqm7ly’
==> azure-arm:  -> Compute Name              : ‘pkrvmswtxmqm7ly’
==> azure-arm:  -> Compute Location          : ‘East US’
==> azure-arm:  -> Image ResourceGroupName   : ‘myResourceGroup’
==> azure-arm:  -> Image Name                : ‘myPackerImage’
==> azure-arm:  -> Image Location            : ‘eastus’
==> azure-arm: Deleting resource group ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-swtxmqm7ly’
==> azure-arm: Deleting the temporary OS disk ...
==> azure-arm:  -> OS Disk : skipping, managed disk was used...
Build ‘azure-arm’ finished.

==> Builds finished. The artifacts of successful builds are:
--> azure-arm: Azure.ResourceManagement.VMImage:

ManagedImageResourceGroupName: myResourceGroup
ManagedImageName: myPackerImage
ManagedImageLocation: eastus
```

Skompilowanie maszyny wirtualnej, uruchomienie aprowizatorów i oczyszczenie wdrożenia zajmuje aplikacji Packer kilka minut.


## <a name="create-vm-from-azure-image"></a>Tworzenie maszyny wirtualnej na pomocą obrazu platformy Azure
Teraz możesz utworzyć maszynę wirtualną z obrazu za pomocą [az vm create](/cli/azure/vm). Określ utworzony obraz za pomocą `--image` parametru . Poniższy przykład tworzy maszynę wirtualną o nazwie *myVM* z *myPackerImage* i generuje klucze SSH, jeśli jeszcze nie istnieją:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image myPackerImage \
    --admin-username azureuser \
    --generate-ssh-keys
```

Jeśli chcesz utworzyć maszyny wirtualne w innej grupie zasobów lub regionie niż obraz pakietu Packer, określ identyfikator obrazu, a nie nazwę obrazu. Identyfikator obrazu można uzyskać za pomocą [az image show](/cli/azure/image#az_image_show).

Utworzenie maszyny wirtualnej może potrwać kilka minut. Po utworzeniu maszyny wirtualnej zanotuj identyfikator `publicIpAddress` wyświetlany przez interfejs wiersza polecenia platformy Azure. Ten adres jest używany do uzyskiwania dostępu do witryny NGINX za pośrednictwem przeglądarki internetowej.

Aby zezwolić na ruch internetowy do maszyny wirtualnej, otwórz port 80 z Internetu za pomocą polecenia [az vm open-port](/cli/azure/vm):

```azurecli
az vm open-port \
    --resource-group myResourceGroup \
    --name myVM \
    --port 80
```

## <a name="test-vm-and-nginx"></a>Testowanie maszyny wirtualnej i usługi NGINX
Teraz możesz otworzyć przeglądarkę internetową i wprowadzić ciąg `http://publicIpAddress` na pasku adresu. Podaj własny publiczny adres IP z procesu tworzenia maszyny wirtualnej. Zostanie wyświetlona domyślna strona NGINX, jak w poniższym przykładzie:

![Domyślna witryna serwera NGINX](./media/build-image-with-packer/nginx.png) 


## <a name="next-steps"></a>Następne kroki
Z usługą Azure Image Builder można również używać istniejących skryptów aprowrow Image Builder [Packer.](image-builder.md)
