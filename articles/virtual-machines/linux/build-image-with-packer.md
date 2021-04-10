---
title: Tworzenie obrazów maszyn wirtualnych z systemem Linux przy użyciu programu Packer
description: Dowiedz się, jak tworzyć obrazy maszyn wirtualnych z systemem Linux na platformie Azure przy użyciu programu Packer
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 05/07/2019
ms.author: cynthn
ms.collection: linux
ms.openlocfilehash: 4d85106cb78b5f4799a78d18463d83594f54dbac
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102556744"
---
# <a name="how-to-use-packer-to-create-linux-virtual-machine-images-in-azure"></a>Jak używać programu pakujący do tworzenia obrazów maszyn wirtualnych z systemem Linux na platformie Azure
Każda maszyna wirtualna na platformie Azure jest tworzona na podstawie obrazu, który definiuje dystrybucję i wersję systemu operacyjnego Linux. Obrazy mogą zawierać wstępnie zainstalowane aplikacje i konfiguracje. Portal Azure Marketplace udostępnia wiele obrazów pierwszej i innej firmy dla typowych dystrybucji i środowisk aplikacji. można też tworzyć własne niestandardowe obrazy dostosowane do Twoich potrzeb. W tym artykule szczegółowo opisano sposób użycia pakietu narzędzi open source [Pack](https://www.packer.io/) do definiowania i tworzenia obrazów niestandardowych na platformie Azure.

> [!NOTE]
> Platforma Azure ma teraz usługę, program Azure Image Builder (wersja zapoznawcza) służącą do definiowania i tworzenia własnych obrazów niestandardowych. Konstruktor obrazów platformy Azure jest oparty na programie Packer, dzięki czemu możesz nawet używać istniejących skryptów aprowizacji powłoki programu Packer. Aby rozpocząć pracę z konstruktorem obrazów platformy Azure, zobacz [Tworzenie maszyny wirtualnej z systemem Linux przy użyciu programu Azure Image Builder](image-builder.md).


## <a name="create-azure-resource-group"></a>Utwórz grupę zasobów platformy Azure
W trakcie procesu kompilacji program Packer tworzy tymczasowe zasoby platformy Azure, ponieważ kompiluje źródłową maszynę wirtualną. Aby przechwycić źródłową maszynę wirtualną do użycia jako obraz, należy zdefiniować grupę zasobów. Dane wyjściowe procesu kompilacji programu Packer są przechowywane w tej grupie zasobów.

Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group). Poniższy przykład tworzy grupę zasobów o nazwie Moja *zasobów* w lokalizacji *Wschodnie* :

```azurecli
az group create -n myResourceGroup -l eastus
```


## <a name="create-azure-credentials"></a>Tworzenie poświadczeń platformy Azure
Pakiet Pack jest uwierzytelniany na platformie Azure przy użyciu nazwy głównej usługi. Jednostka usługi platformy Azure to tożsamość zabezpieczeń, której można używać z aplikacjami, usługami i narzędziami automatyzacji, takimi jak pakujący. Użytkownik kontroluje i definiuje uprawnienia do działania, które może wykonywać jednostka usługi na platformie Azure.

Utwórz nazwę główną usługi za pomocą polecenia [AZ AD Sp Create-for-RBAC](/cli/azure/ad/sp) i Wyprowadź poświadczenia wymagane przez pakiet Pack:

```azurecli
az ad sp create-for-rbac --query "{ client_id: appId, client_secret: password, tenant_id: tenant }"
```

Przykład danych wyjściowych z poprzednich poleceń jest następujący:

```output
{
    "client_id": "f5b6a5cf-fbdf-4a9f-b3b8-3c2cd00225a4",
    "client_secret": "0e760437-bf34-4aad-9f8d-870be799c55d",
    "tenant_id": "72f988bf-86f1-41af-91ab-2d7cd011db47"
}
```

Aby uwierzytelnić się na platformie Azure, musisz również uzyskać identyfikator subskrypcji platformy Azure za pomocą [AZ Account show](/cli/azure/account):

```azurecli
az account show --query "{ subscription_id: id }"
```

Dane wyjściowe z tych dwóch poleceń są używane w następnym kroku.


## <a name="define-packer-template"></a>Definiowanie szablonu pakietu Packer
Aby tworzyć obrazy, należy utworzyć szablon jako plik JSON. W szablonie należy zdefiniować konstruktory i aprowizacji, które wykonują rzeczywisty proces kompilacji. Program Packer ma [provisioner dla platformy Azure](https://www.packer.io/docs/builders/azure.html) , który umożliwia definiowanie zasobów platformy Azure, takich jak poświadczenia nazwy głównej usługi utworzone w poprzednim kroku.

Utwórz plik o nazwie *ubuntu.jsna* i wklej następującą zawartość. Wprowadź własne wartości w następujący sposób:

| Parametr                           | Skąd uzyskać |
|-------------------------------------|----------------------------------------------------|
| *client_id*                         | Pierwszy wiersz danych wyjściowych `az ad sp` polecenia Create — *AppID* |
| *client_secret*                     | Drugi wiersz danych wyjściowych `az ad sp` polecenia Create — *hasło* |
| *tenant_id*                         | Trzeci wiersz danych wyjściowych `az ad sp` polecenia Create- *dzierżawca* |
| *subscription_id*                   | Dane wyjściowe `az account show` polecenia |
| *managed_image_resource_group_name* | Nazwa grupy zasobów utworzonej w pierwszym kroku |
| *managed_image_name*                | Nazwa tworzonego obrazu dysku zarządzanego |


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

Ten szablon służy do kompilowania obrazu Ubuntu 16,04 LTS, instalowania NGINX, a następnie wyrezerwowania maszyny wirtualnej.

> [!NOTE]
> Po rozwinięciu tego szablonu, aby udostępnić poświadczenia użytkownika, Dostosuj polecenie aprowizacji, które umożliwia odczytanie agenta platformy Azure, `-deprovision` a nie `deprovision+user` .
> `+user`Flaga usuwa wszystkie konta użytkowników ze źródłowej maszyny wirtualnej.


## <a name="build-packer-image"></a>Obraz pakietu Build Pack
Jeśli nie masz jeszcze zainstalowanego programu Packer na komputerze lokalnym, [postępuj zgodnie z instrukcjami instalacji programu Packer](https://www.packer.io/docs/install).

Utwórz obraz, określając plik szablonu programu Packer w następujący sposób:

```bash
./packer build ubuntu.json
```

Przykład danych wyjściowych z poprzednich poleceń jest następujący:

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

Kompilowanie maszyny wirtualnej może zająć kilka minut, uruchomić program, a następnie wyczyścić wdrożenie.


## <a name="create-vm-from-azure-image"></a>Tworzenie maszyny wirtualnej na podstawie obrazu platformy Azure
Teraz można utworzyć maszynę wirtualną na podstawie obrazu za pomocą [AZ VM Create](/cli/azure/vm). Określ obraz utworzony za pomocą `--image` parametru. Poniższy przykład tworzy maszynę wirtualną o nazwie *myVM* z *myPackerImage* i generuje klucze SSH, jeśli jeszcze nie istnieją:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image myPackerImage \
    --admin-username azureuser \
    --generate-ssh-keys
```

Jeśli chcesz utworzyć maszyny wirtualne w innej grupie zasobów lub regionie niż obraz programu Packer, określ identyfikator obrazu, a nie nazwę obrazu. Identyfikator obrazu można uzyskać za pomocą [AZ Image show](/cli/azure/image#az-image-show).

Utworzenie maszyny wirtualnej może potrwać kilka minut. Po utworzeniu maszyny wirtualnej Zanotuj wyświetlane dane w `publicIpAddress` interfejsie wiersza polecenia platformy Azure. Ten adres służy do uzyskiwania dostępu do witryny NGINX za pośrednictwem przeglądarki sieci Web.

Aby zezwolić na ruch internetowy do maszyny wirtualnej, otwórz port 80 z Internetu za pomocą polecenia [az vm open-port](/cli/azure/vm):

```azurecli
az vm open-port \
    --resource-group myResourceGroup \
    --name myVM \
    --port 80
```

## <a name="test-vm-and-nginx"></a>Testowanie maszyn wirtualnych i NGINX
Teraz możesz otworzyć przeglądarkę internetową i wprowadzić ciąg `http://publicIpAddress` na pasku adresu. Podaj własny publiczny adres IP z procesu tworzenia maszyny wirtualnej. Domyślna strona NGINX jest wyświetlana jak w poniższym przykładzie:

![Domyślna witryna serwera NGINX](./media/build-image-with-packer/nginx.png) 


## <a name="next-steps"></a>Następne kroki
Możesz również używać istniejących skryptów aprowizacji programu pakujący w programie [Azure Image Builder](image-builder.md).
