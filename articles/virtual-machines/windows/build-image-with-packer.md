---
title: Jak tworzyć obrazy maszyn wirtualnych systemu Windows z programem Packer na platformie Azure
description: Dowiedz się, jak używać programu Packer do tworzenia obrazów maszyn wirtualnych z systemem Windows na platformie Azure
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 02/22/2019
ms.author: cynthn
ms.openlocfilehash: cb81cbb12605a9d4b8870aab4bb461c8af079cf5
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81460753"
---
# <a name="how-to-use-packer-to-create-windows-virtual-machine-images-in-azure"></a>Jak używać programu Packer do tworzenia obrazów maszyn wirtualnych systemu Windows na platformie Azure
Każda maszyna wirtualna (VM) na platformie Azure jest tworzona na podstawie obrazu definiujące dystrybucję systemu Windows i wersję systemu operacyjnego. Obrazy mogą zawierać wstępnie zainstalowane aplikacje i konfiguracje. Portal Azure Marketplace udostępnia wiele obrazów pierwszej i innej firmy dla większości typowych środowisk systemu operacyjnego i aplikacji lub można tworzyć własne obrazy niestandardowe dostosowane do twoich potrzeb. W tym artykule opisano, jak używać narzędzia typu open source [Packer](https://www.packer.io/) do definiowania i tworzenia obrazów niestandardowych na platformie Azure.

Ten artykuł został ostatnio przetestowany w dniu 21.02.2019 przy użyciu [modułu Az PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps) w wersji 1.3.0 i [Packer](https://www.packer.io/docs/install/index.html) w wersji 1.3.4.

> [!NOTE]
> Platforma Azure ma teraz usługę Azure Image Builder (preview), która do definiowania i tworzenia własnych obrazów niestandardowych. Usługa Azure Image Builder jest oparta na programie Packer, dzięki czemu można nawet używać istniejących skryptów aprowizacji powłoki Packer. Aby rozpocząć korzystanie z usługi Azure Image Builder, zobacz [Tworzenie maszyny Wirtualnej systemu Windows za pomocą programu Azure Image Builder](image-builder.md).

## <a name="create-azure-resource-group"></a>Tworzenie grupy zasobów platformy Azure
Podczas procesu kompilacji Packer tworzy tymczasowe zasoby platformy Azure podczas tworzenia źródłowej maszyny Wirtualnej. Aby przechwycić tę źródłową maszynę wirtualną do użycia jako obraz, należy zdefiniować grupę zasobów. Dane wyjściowe z procesu kompilacji packer jest przechowywany w tej grupie zasobów.

Utwórz grupę zasobów za pomocą polecenia [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup). Poniższy przykład tworzy grupę zasobów o nazwie *myResourceGroup* w lokalizacji *eastus:*

```azurepowershell
$rgName = "myResourceGroup"
$location = "East US"
New-AzResourceGroup -Name $rgName -Location $location
```

## <a name="create-azure-credentials"></a>Tworzenie poświadczeń platformy Azure
Packer uwierzytelnia się za pomocą platformy Azure przy użyciu jednostki usługi. Podmiot usługi platformy Azure to tożsamość zabezpieczeń, której można używać z aplikacjami, usługami i narzędziami automatyzacji, takimi jak Packer. Można kontrolować i zdefiniować uprawnienia, jakie operacje podmiotu zabezpieczeń usługi można wykonać na platformie Azure.

Utwórz jednostkę usługi za pomocą [new-azADServicePrincipal](https://docs.microsoft.com/powershell/module/az.resources/new-azadserviceprincipal) i przypisz uprawnienia podmiotu usługi do tworzenia zasobów i zarządzania nimi za pomocą [new-AzRoleAssignment](https://docs.microsoft.com/powershell/module/az.resources/new-azroleassignment). Wartość musi `-DisplayName` być unikatowa; w razie potrzeby zmienić z własną wartością.  

```azurepowershell
$sp = New-AzADServicePrincipal -DisplayName "PackerServicePrincipal"
$BSTR = [System.Runtime.InteropServices.Marshal]::SecureStringToBSTR($sp.Secret)
$plainPassword = [System.Runtime.InteropServices.Marshal]::PtrToStringAuto($BSTR)
New-AzRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $sp.ApplicationId
```

Następnie wyśmij hasło i identyfikator aplikacji.

```powershell
$plainPassword
$sp.ApplicationId
```


Aby uwierzytelnić się na platformie Azure, musisz również uzyskać identyfikatory dzierżawy i subskrypcji platformy Azure za pomocą [usługi Get-AzSubscription:](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription)

```powershell
Get-AzSubscription
```


## <a name="define-packer-template"></a>Zdefiniuj szablon Pakowacza
Aby utworzyć obrazy, należy utworzyć szablon jako plik JSON. W szablonie można zdefiniować konstruktorów i inicjantów, które wykonują rzeczywisty proces kompilacji. Packer ma [konstruktora platformy Azure,](https://www.packer.io/docs/builders/azure.html) który umożliwia definiowanie zasobów platformy Azure, takich jak poświadczenia jednostki usługi utworzone w poprzednim kroku.

Utwórz plik o nazwie *windows.json* i wklej następującą zawartość. Wprowadź własne wartości dla następujących elementów:

| Parametr                           | Gdzie można uzyskać |
|-------------------------------------|----------------------------------------------------|
| *client_id*                         | Wyświetl identyfikator jednostki usługi za pomocą`$sp.applicationId` |
| *client_secret*                     | Wyświetlanie automatycznie wygenerowanego hasła za pomocą`$plainPassword` |
| *tenant_id*                         | Wyjście `$sub.TenantId` z polecenia |
| *subscription_id*                   | Wyjście `$sub.SubscriptionId` z polecenia |
| *managed_image_resource_group_name* | Nazwa grupy zasobów utworzonej w pierwszym kroku |
| *managed_image_name*                | Nazwa utworzonego obrazu dysku zarządzanego |

```json
{
  "builders": [{
    "type": "azure-arm",

    "client_id": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
    "client_secret": "ppppppp-pppp-pppp-pppp-ppppppppppp",
    "tenant_id": "zzzzzzz-zzzz-zzzz-zzzz-zzzzzzzzzzzz",
    "subscription_id": "yyyyyyy-yyyy-yyyy-yyyy-yyyyyyyyyyy",

    "managed_image_resource_group_name": "myResourceGroup",
    "managed_image_name": "myPackerImage",

    "os_type": "Windows",
    "image_publisher": "MicrosoftWindowsServer",
    "image_offer": "WindowsServer",
    "image_sku": "2016-Datacenter",

    "communicator": "winrm",
    "winrm_use_ssl": true,
    "winrm_insecure": true,
    "winrm_timeout": "5m",
    "winrm_username": "packer",

    "azure_tags": {
        "dept": "Engineering",
        "task": "Image deployment"
    },

    "location": "East US",
    "vm_size": "Standard_DS2_v2"
  }],
  "provisioners": [{
    "type": "powershell",
    "inline": [
      "Add-WindowsFeature Web-Server",
      "& $env:SystemRoot\\System32\\Sysprep\\Sysprep.exe /oobe /generalize /quiet /quit",
      "while($true) { $imageState = Get-ItemProperty HKLM:\\SOFTWARE\\Microsoft\\Windows\\CurrentVersion\\Setup\\State | Select ImageState; if($imageState.ImageState -ne 'IMAGE_STATE_GENERALIZE_RESEAL_TO_OOBE') { Write-Output $imageState.ImageState; Start-Sleep -s 10  } else { break } }"
    ]
  }]
}
```

Ten szablon tworzy maszynę wirtualną systemu Windows Server 2016, instaluje usługi IIS, a następnie uogólnia maszynę wirtualną za pomocą programu Sysprep. Instalacja usług IIS pokazuje, jak można użyć aprowizacji programu PowerShell do uruchamiania dodatkowych poleceń. Ostateczny obraz Packera zawiera następnie wymaganą instalację i konfigurację oprogramowania.


## <a name="build-packer-image"></a>Tworzenie obrazu packera
Jeśli nie masz jeszcze zainstalowanego packera na komputerze lokalnym, [postępuj zgodnie z instrukcjami instalacji Packera.](https://www.packer.io/docs/install/index.html)

Skompiluj obraz, otwierając monit cmd i określając plik szablonu programu Packer w następujący sposób:

```
./packer build windows.json
```

Przykład danych wyjściowych z poprzednich poleceń jest następujący:

```bash
azure-arm output will be in this color.

==> azure-arm: Running builder ...
    azure-arm: Creating Azure Resource Manager (ARM) client ...
==> azure-arm: Creating resource group ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> Location          : ‘East US’
==> azure-arm:  -> Tags              :
==> azure-arm:  ->> task : Image deployment
==> azure-arm:  ->> dept : Engineering
==> azure-arm: Validating deployment template ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> DeploymentName    : ‘pkrdppq0mthtbtt’
==> azure-arm: Deploying deployment template ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> DeploymentName    : ‘pkrdppq0mthtbtt’
==> azure-arm: Getting the certificate’s URL ...
==> azure-arm:  -> Key Vault Name        : ‘pkrkvpq0mthtbtt’
==> azure-arm:  -> Key Vault Secret Name : ‘packerKeyVaultSecret’
==> azure-arm:  -> Certificate URL       : ‘https://pkrkvpq0mthtbtt.vault.azure.net/secrets/packerKeyVaultSecret/8c7bd823e4fa44e1abb747636128adbb'
==> azure-arm: Setting the certificate’s URL ...
==> azure-arm: Validating deployment template ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> DeploymentName    : ‘pkrdppq0mthtbtt’
==> azure-arm: Deploying deployment template ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> DeploymentName    : ‘pkrdppq0mthtbtt’
==> azure-arm: Getting the VM’s IP address ...
==> azure-arm:  -> ResourceGroupName   : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> PublicIPAddressName : ‘packerPublicIP’
==> azure-arm:  -> NicName             : ‘packerNic’
==> azure-arm:  -> Network Connection  : ‘PublicEndpoint’
==> azure-arm:  -> IP Address          : ‘40.76.55.35’
==> azure-arm: Waiting for WinRM to become available...
==> azure-arm: Connected to WinRM!
==> azure-arm: Provisioning with Powershell...
==> azure-arm: Provisioning with shell script: /var/folders/h1/ymh5bdx15wgdn5hvgj1wc0zh0000gn/T/packer-powershell-provisioner902510110
    azure-arm: #< CLIXML
    azure-arm:
    azure-arm: Success Restart Needed Exit Code      Feature Result
    azure-arm: ------- -------------- ---------      --------------
    azure-arm: True    No             Success        {Common HTTP Features, Default Document, D...
    azure-arm: <Objs Version=“1.1.0.1” xmlns=“http://schemas.microsoft.com/powershell/2004/04"><Obj S=“progress” RefId=“0"><TN RefId=“0”><T>System.Management.Automation.PSCustomObject</T><T>System.Object</T></TN><MS><I64 N=“SourceId”>1</I64><PR N=“Record”><AV>Preparing modules for first use.</AV><AI>0</AI><Nil /><PI>-1</PI><PC>-1</PC><T>Completed</T><SR>-1</SR><SD> </SD></PR></MS></Obj></Objs>
==> azure-arm: Querying the machine’s properties ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> ComputeName       : ‘pkrvmpq0mthtbtt’
==> azure-arm:  -> Managed OS Disk   : ‘/subscriptions/guid/resourceGroups/packer-Resource-Group-pq0mthtbtt/providers/Microsoft.Compute/disks/osdisk’
==> azure-arm: Powering off machine ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> ComputeName       : ‘pkrvmpq0mthtbtt’
==> azure-arm: Capturing image ...
==> azure-arm:  -> Compute ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> Compute Name              : ‘pkrvmpq0mthtbtt’
==> azure-arm:  -> Compute Location          : ‘East US’
==> azure-arm:  -> Image ResourceGroupName   : ‘myResourceGroup’
==> azure-arm:  -> Image Name                : ‘myPackerImage’
==> azure-arm:  -> Image Location            : ‘eastus’
==> azure-arm: Deleting resource group ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm: Deleting the temporary OS disk ...
==> azure-arm:  -> OS Disk : skipping, managed disk was used...
Build ‘azure-arm’ finished.

==> Builds finished. The artifacts of successful builds are:
--> azure-arm: Azure.ResourceManagement.VMImage:

ManagedImageResourceGroupName: myResourceGroup
ManagedImageName: myPackerImage
ManagedImageLocation: eastus
```

Trwa kilka minut dla packer do tworzenia maszyny Wirtualnej, uruchomić inicjatory i oczyścić wdrożenia.


## <a name="create-a-vm-from-the-packer-image"></a>Tworzenie maszyny Wirtualnej na podstawie obrazu pakującego
Teraz można utworzyć maszynę wirtualną z obrazu za pomocą [nowego pliku AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm). Pomocnicze zasoby sieciowe są tworzone, jeśli jeszcze nie istnieją. Po wyświetleniu monitu wprowadź nazwę użytkownika administracyjnego i hasło, które mają zostać utworzone na maszynie wirtualnej. Poniższy przykład tworzy maszynę wirtualną o nazwie *myVM* z *myPackerImage:*

```powershell
New-AzVm `
    -ResourceGroupName $rgName `
    -Name "myVM" `
    -Location $location `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -OpenPorts 80 `
    -Image "myPackerImage"
```

Jeśli chcesz utworzyć maszyny wirtualne w innej grupie zasobów lub regionie niż obraz Packer, określ identyfikator obrazu, a nie nazwę obrazu. Identyfikator obrazu można uzyskać za pomocą [pliku Get-AzImage](https://docs.microsoft.com/powershell/module/az.compute/Get-AzImage).

Utworzenie maszyny Wirtualnej na podstawie obrazu packera zajmuje kilka minut.


## <a name="test-vm-and-webserver"></a>Test VM i serwer www
Uzyskaj publiczny adres IP maszyny wirtualnej za pomocą polecenia [Get-AzPublicIPAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress). W poniższym przykładzie uzyskano utworzony wcześniej adres IP *myPublicIP*:

```powershell
Get-AzPublicIPAddress `
    -ResourceGroupName $rgName `
    -Name "myPublicIPAddress" | select "IpAddress"
```

Aby wyświetlić maszynę wirtualną, która obejmuje instalację usług IIS z programu inicjującego programu pakującego, w działaniu wprowadź publiczny adres IP w przeglądarce sieci Web.

![Domyślna witryna usług IIS](./media/build-image-with-packer/iis.png) 


## <a name="next-steps"></a>Następne kroki
Można również użyć istniejących skryptów aprowizacji programu Packer za pomocą [usługi Azure Image Builder](image-builder.md).
