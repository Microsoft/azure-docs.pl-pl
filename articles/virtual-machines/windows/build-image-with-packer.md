---
title: PowerShell — tworzenie obrazów maszyn wirtualnych przy użyciu programu Packer
description: Dowiedz się, jak tworzyć obrazy maszyn wirtualnych na platformie Azure przy użyciu programu pakujący lub PowerShell
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.collection: windows
ms.topic: how-to
ms.workload: infrastructure
ms.date: 08/05/2020
ms.author: cynthn
ms.openlocfilehash: a2cd6152f9c8e75223fcf1aab77fee7700223979
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102561011"
---
# <a name="powershell-how-to-use-packer-to-create-virtual-machine-images-in-azure"></a>PowerShell: jak używać programu pakujący do tworzenia obrazów maszyn wirtualnych na platformie Azure
Każda maszyna wirtualna na platformie Azure jest tworzona na podstawie obrazu, który definiuje dystrybucję systemu Windows i wersję systemu operacyjnego. Obrazy mogą zawierać wstępnie zainstalowane aplikacje i konfiguracje. Portal Azure Marketplace udostępnia wiele obrazów od początku i innych firm dla najpopularniejszych środowisk systemów operacyjnych i aplikacji. można też tworzyć własne niestandardowe obrazy dostosowane do Twoich potrzeb. W tym artykule szczegółowo opisano, jak używać pakietu narzędzi open source [Pack](https://www.packer.io/) do definiowania i tworzenia obrazów niestandardowych na platformie Azure.

Ten artykuł został ostatnio przetestowany w dniu 8/5/2020 przy użyciu programu [Packer](https://www.packer.io/docs/install) w wersji 1.6.1.

> [!NOTE]
> Platforma Azure ma teraz usługę, program Azure Image Builder (wersja zapoznawcza) służącą do definiowania i tworzenia własnych obrazów niestandardowych. Konstruktor obrazów platformy Azure jest oparty na programie Packer, dzięki czemu możesz nawet używać istniejących skryptów aprowizacji powłoki programu Packer. Aby rozpocząć pracę z konstruktorem obrazów platformy Azure, zobacz [Tworzenie maszyny wirtualnej z systemem Windows przy użyciu programu Azure Image Builder](image-builder.md).

## <a name="create-azure-resource-group"></a>Utwórz grupę zasobów platformy Azure
W trakcie procesu kompilacji program Packer tworzy tymczasowe zasoby platformy Azure, ponieważ kompiluje źródłową maszynę wirtualną. Aby przechwycić źródłową maszynę wirtualną do użycia jako obraz, należy zdefiniować grupę zasobów. Dane wyjściowe procesu kompilacji programu Packer są przechowywane w tej grupie zasobów.

Utwórz grupę zasobów za pomocą polecenia [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Poniższy przykład tworzy grupę zasobów o nazwie *myPackerGroup* w lokalizacji *Wschodnie* :

```azurepowershell
$rgName = "myPackerGroup"
$location = "East US"
New-AzResourceGroup -Name $rgName -Location $location
```

## <a name="create-azure-credentials"></a>Tworzenie poświadczeń platformy Azure
Pakiet Pack jest uwierzytelniany na platformie Azure przy użyciu nazwy głównej usługi. Jednostka usługi platformy Azure to tożsamość zabezpieczeń, której można używać z aplikacjami, usługami i narzędziami automatyzacji, takimi jak pakujący. Użytkownik kontroluje i definiuje uprawnienia do działania, które może wykonywać jednostka usługi na platformie Azure.

Utwórz nazwę główną usługi przy użyciu elementu [New-AzADServicePrincipal](/powershell/module/az.resources/new-azadserviceprincipal). Wartość dla `-DisplayName` musi być unikatowa; Zastąp wartość własną wartością w razie potrzeby.  

```azurepowershell
$sp = New-AzADServicePrincipal -DisplayName "PackerSP$(Get-Random)"
$BSTR = [System.Runtime.InteropServices.Marshal]::SecureStringToBSTR($sp.Secret)
$plainPassword = [System.Runtime.InteropServices.Marshal]::PtrToStringAuto($BSTR)
```

Następnie dane wyjściowe hasła i identyfikatora aplikacji.

```powershell
$plainPassword
$sp.ApplicationId
```


Aby uwierzytelnić się na platformie Azure, musisz również uzyskać dzierżawę i identyfikatory subskrypcji platformy Azure za pomocą [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription):

```powershell
Get-AzSubscription
```


## <a name="define-packer-template"></a>Definiowanie szablonu pakietu Packer
Aby tworzyć obrazy, należy utworzyć szablon jako plik JSON. W szablonie należy zdefiniować konstruktory i aprowizacji, które wykonują rzeczywisty proces kompilacji. Program Packer ma [konstruktora dla platformy Azure](https://www.packer.io/docs/builders/azure.html) , który umożliwia definiowanie zasobów platformy Azure, takich jak poświadczenia nazwy głównej usługi utworzone w poprzednim kroku.

Utwórz plik o nazwie *windows.jsna* i wklej następującą zawartość. Wprowadź własne wartości w następujący sposób:

| Parametr                           | Skąd uzyskać |
|-------------------------------------|----------------------------------------------------|
| *client_id*                         | Wyświetl identyfikator jednostki usługi za pomocą `$sp.applicationId` |
| *client_secret*                     | Wyświetl automatycznie generowane hasło przy użyciu `$plainPassword` |
| *tenant_id*                         | Dane wyjściowe `$sub.TenantId` polecenia |
| *subscription_id*                   | Dane wyjściowe `$sub.SubscriptionId` polecenia |
| *managed_image_resource_group_name* | Nazwa grupy zasobów utworzonej w pierwszym kroku |
| *managed_image_name*                | Nazwa tworzonego obrazu dysku zarządzanego |

```json
{
  "builders": [{
    "type": "azure-arm",

    "client_id": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
    "client_secret": "ppppppp-pppp-pppp-pppp-ppppppppppp",
    "tenant_id": "zzzzzzz-zzzz-zzzz-zzzz-zzzzzzzzzzzz",
    "subscription_id": "yyyyyyy-yyyy-yyyy-yyyy-yyyyyyyyyyy",

    "managed_image_resource_group_name": "myPackerGroup",
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

    "build_resource_group_name": "myPackerGroup",
    "vm_size": "Standard_D2_v2"
  }],
  "provisioners": [{
    "type": "powershell",
    "inline": [
      "Add-WindowsFeature Web-Server",
      "while ((Get-Service RdAgent).Status -ne 'Running') { Start-Sleep -s 5 }",
      "while ((Get-Service WindowsAzureGuestAgent).Status -ne 'Running') { Start-Sleep -s 5 }",
      "& $env:SystemRoot\\System32\\Sysprep\\Sysprep.exe /oobe /generalize /quiet /quit",
      "while($true) { $imageState = Get-ItemProperty HKLM:\\SOFTWARE\\Microsoft\\Windows\\CurrentVersion\\Setup\\State | Select ImageState; if($imageState.ImageState -ne 'IMAGE_STATE_GENERALIZE_RESEAL_TO_OOBE') { Write-Output $imageState.ImageState; Start-Sleep -s 10  } else { break } }"
    ]
  }]
}
```

Ten szablon tworzy maszynę wirtualną z systemem Windows Server 2016, instaluje usługi IIS, a następnie uogólniuje maszynę wirtualną przy użyciu programu Sysprep. W instalacji usług IIS pokazano, jak można użyć programu PowerShell do uruchamiania dodatkowych poleceń. Końcowy obraz programu Packer zawiera również wymaganą instalację i konfigurację oprogramowania.

Agent gościa systemu Windows uczestniczy w procesie Sysprep. Aby można było sysprep'ed maszynę wirtualną, Agent musi być w pełni zainstalowany. Aby mieć pewność, że ta wartość jest prawdziwa, przed wykonaniem sysprep.exe należy uruchomić wszystkie usługi agenta. Poprzedni fragment kodu JSON przedstawia jeden ze sposobów wykonania tej czynności w aprowizacji programu PowerShell. Ten fragment kodu jest wymagany tylko wtedy, gdy maszyna wirtualna jest skonfigurowana do zainstalowania agenta, co jest ustawieniem domyślnym.


## <a name="build-packer-image"></a>Obraz pakietu Build Pack
Jeśli nie masz jeszcze zainstalowanego programu Packer na komputerze lokalnym, [postępuj zgodnie z instrukcjami instalacji programu Packer](https://learn.hashicorp.com/packer/getting-started/install).

Utwórz obraz, otwierając wiersz polecenia cmd i określając plik szablonu programu Packer w następujący sposób:

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

Kompilowanie maszyny wirtualnej może zająć kilka minut, uruchomić program, a następnie wyczyścić wdrożenie.


## <a name="create-a-vm-from-the-packer-image"></a>Tworzenie maszyny wirtualnej na podstawie obrazu programu Packer
Teraz można utworzyć maszynę wirtualną na podstawie obrazu za pomocą [nowego AzVM](/powershell/module/az.compute/new-azvm). Dodatkowe zasoby sieciowe są tworzone, jeśli jeszcze nie istnieją. Po wyświetleniu monitu wprowadź nazwę użytkownika i hasło administracyjne, które mają zostać utworzone na maszynie wirtualnej. Poniższy przykład tworzy maszynę wirtualną o nazwie *myVM* z *myPackerImage*:

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

Jeśli chcesz utworzyć maszyny wirtualne w innej grupie zasobów lub regionie niż obraz programu Packer, określ identyfikator obrazu, a nie nazwę obrazu. Identyfikator obrazu można uzyskać za pomocą [Get-AzImage](/powershell/module/az.compute/get-azimage).

Tworzenie maszyny wirtualnej z obrazu programu Pack trwa kilka minut.


## <a name="test-vm-and-webserver"></a>Testowanie maszyn wirtualnych i serwera WebServer
Uzyskaj publiczny adres IP maszyny wirtualnej za pomocą polecenia [Get-AzPublicIPAddress](/powershell/module/az.network/get-azpublicipaddress). W poniższym przykładzie uzyskano utworzony wcześniej adres IP *myPublicIP*:

```powershell
Get-AzPublicIPAddress `
    -ResourceGroupName $rgName `
    -Name "myPublicIPAddress" | select "IpAddress"
```

Aby wyświetlić maszynę wirtualną, która obejmuje instalację usług IIS z programu Pakującyer, w obszarze Akcja wprowadź publiczny adres IP w przeglądarce internetowej.

![Domyślna witryna usług IIS](./media/build-image-with-packer/iis.png) 


## <a name="next-steps"></a>Następne kroki
Możesz również używać istniejących skryptów aprowizacji programu pakujący w programie [Azure Image Builder](image-builder.md).
