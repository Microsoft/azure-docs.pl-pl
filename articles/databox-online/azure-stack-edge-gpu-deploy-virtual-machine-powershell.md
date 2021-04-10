---
title: Wdrażaj maszyny wirtualne na urządzeniu Azure Stack Edge za pośrednictwem Azure PowerShell
description: Opisuje sposób tworzenia i zarządzania maszynami wirtualnymi na Azure Stack urządzeniu brzegowym przy użyciu Azure PowerShell.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: 90925f30e84ec3c06e715669ebd982c823dfaf5a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105568328"
---
# <a name="deploy-vms-on-your-azure-stack-edge-device-via-azure-powershell"></a>Wdrażaj maszyny wirtualne na urządzeniu Azure Stack Edge za pośrednictwem Azure PowerShell

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

W tym artykule opisano sposób tworzenia maszyny wirtualnej (VM) na urządzeniu Azure Stack brzegowej oraz zarządzania nią przy użyciu Azure PowerShell. Informacje dotyczą Azure Stack EDGE Pro z procesora GPU (procesorem graficznym), Azure Stack EDGE Pro R i Azure Stack Edge mini R urządzenia.

## <a name="vm-deployment-workflow"></a>Przepływ pracy wdrożenia maszyny wirtualnej

Przepływ pracy wdrażania jest wyświetlany na poniższym diagramie:

![Diagram przepływu pracy wdrożenia maszyny wirtualnej.](media/azure-stack-edge-gpu-deploy-virtual-machine-powershell/vm-workflow-r.svg)

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [azure-stack-edge-gateway-deploy-vm-prerequisites](../../includes/azure-stack-edge-gateway-deploy-virtual-machine-prerequisites.md)]


## <a name="query-for-a-built-in-subscription-on-the-device"></a>Zapytanie o wbudowaną subskrypcję na urządzeniu

W przypadku Azure Resource Manager obsługiwana jest tylko jedna stała subskrypcja, która jest widoczna dla użytkownika. Ta subskrypcja jest unikatowa dla urządzenia, a nie można zmienić nazwy subskrypcji i identyfikatora subskrypcji.

Subskrypcja zawiera wszystkie zasoby, które są wymagane do utworzenia maszyny wirtualnej. 

> [!IMPORTANT]
> Subskrypcja jest tworzona po włączeniu maszyn wirtualnych na podstawie Azure Portal i lokalnie na urządzeniu.

Subskrypcja służy do wdrażania maszyn wirtualnych.

1.  Aby wyświetlić listę subskrypcji, uruchom następujące polecenie:

    ```powershell
    Get-AzureRmSubscription
    ```
    
    Oto przykładowe dane wyjściowe:

    ```powershell
    PS C:\windows\system32> Get-AzureRmSubscription
    
    Name                 Id                 TenantId          State
    ----                 --                --------           -----
    Default Provider Subscription A4257FDE-B946-4E01-ADE7-674760B8D1A3 c0257de7-538f-415c-993a-1b87a031879d Enabled
    
    PS C:\windows\system32>
    ```
        
1. Pobierz listę zarejestrowanych dostawców zasobów uruchomionych na urządzeniu. Lista zazwyczaj obejmuje obliczenia, Sieć i magazyn.

    ```powershell
    Get-AzureRMResourceProvider
    ```

    > [!NOTE]
    > Dostawcy zasobów są wstępnie zarejestrowani i nie można ich modyfikować ani zmieniać.
    
    Oto przykładowe dane wyjściowe:

    ```powershell
    Get-AzureRmResourceProvider
    ProviderNamespace : Microsoft.Compute
    RegistrationState : Registered
    ResourceTypes     : {virtualMachines, virtualMachines/extensions, locations, operations...}
    Locations         : {DBELocal}
    ZoneMappings      :
    
    ProviderNamespace : Microsoft.Network
    RegistrationState : Registered
    ResourceTypes     : {operations, locations, locations/operations, locations/usages...}
    Locations         : {DBELocal}
    ZoneMappings      :
    
    ProviderNamespace : Microsoft.Resources
    RegistrationState : Registered
    ResourceTypes     : {tenants, locations, providers, checkresourcename...}
    Locations         : {DBELocal}
    ZoneMappings      :
    
    ProviderNamespace : Microsoft.Storage
    RegistrationState : Registered
    ResourceTypes     : {storageaccounts, storageAccounts/blobServices, storageAccounts/tableServices,
                        storageAccounts/queueServices...}
    Locations         : {DBELocal}
    ZoneMappings      :
    ```
    
## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Utwórz grupę zasobów platformy Azure za pomocą polecenia [New-AzureRmResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Grupa zasobów to logiczny kontener, w którym są wdrażane i zarządzane zasoby platformy Azure, takie jak konto magazynu, dysk i dysk zarządzany.

> [!IMPORTANT]
> Wszystkie zasoby są tworzone w tej samej lokalizacji, w której znajduje się urządzenie, a lokalizacja jest ustawiona na **DBELocal**.

```powershell
New-AzureRmResourceGroup -Name <Resource group name> -Location DBELocal
```

Oto przykładowe dane wyjściowe:

```powershell
New-AzureRmResourceGroup -Name rg191113014333 -Location DBELocal 
Successfully created Resource Group:rg191113014333
```

## <a name="create-a-storage-account"></a>Tworzenie konta magazynu

Utwórz nowe konto magazynu za pomocą grupy zasobów utworzonej w poprzednim kroku. To jest lokalne konto magazynu używane do przekazywania obrazu dysku wirtualnego dla maszyny wirtualnej.

```powershell
New-AzureRmStorageAccount -Name <Storage account name> -ResourceGroupName <Resource group name> -Location DBELocal -SkuName Standard_LRS
```

> [!NOTE]
> Za pomocą Azure Resource Manager można tworzyć tylko lokalne konta magazynu, takie jak Magazyn lokalnie nadmiarowy (standardowa lub Premium). Aby utworzyć konta magazynu warstwowego, zobacz [Samouczek: transfer danych za pośrednictwem kont magazynu za pomocą Azure Stack EDGE Pro z procesorem GPU](./azure-stack-edge-gpu-deploy-add-storage-accounts.md).

Oto przykładowe dane wyjściowe:

```powershell
New-AzureRmStorageAccount -Name sa191113014333  -ResourceGroupName rg191113014333 -SkuName Standard_LRS -Location DBELocal

ResourceGroupName      : rg191113014333
StorageAccountName     : sa191113014333
Id                     : /subscriptions/a4257fde-b946-4e01-ade7-674760b8d1a3/resourceGroups/rg191113014333/providers/Microsoft.Storage/storageaccounts/sa191113014333
Location               : DBELocal
Sku                    : Microsoft.Azure.Management.Storage.Models.Sku
Kind                   : Storage
Encryption             : Microsoft.Azure.Management.Storage.Models.Encryption
AccessTier             :
CreationTime           : 11/13/2019 9:43:49 PM
CustomDomain           :
Identity               :
LastGeoFailoverTime    :
PrimaryEndpoints       : Microsoft.Azure.Management.Storage.Models.Endpoints
PrimaryLocation        : DBELocal
ProvisioningState      : Succeeded
SecondaryEndpoints     :
SecondaryLocation      :
StatusOfPrimary        : Available
StatusOfSecondary      :
Tags                   :
EnableHttpsTrafficOnly : False
NetworkRuleSet         :
Context                : Microsoft.WindowsAzure.Commands.Common.Storage.LazyAzureStorageContext
ExtendedProperties     : {}
```

Aby uzyskać klucz konta magazynu, uruchom `Get-AzureRmStorageAccountKey` polecenie. Oto przykładowe dane wyjściowe:

```powershell
PS C:\Users\Administrator> Get-AzureRmStorageAccountKey

cmdlet Get-AzureRmStorageAccountKey at command pipeline position 1
Supply values for the following parameters:
(Type !? for Help.)
ResourceGroupName: my-resource-ase
Name:myasestoracct

KeyName Value
------- -----
key1 /IjVJN+sSf7FMKiiPLlDm8mc9P4wtcmhhbnCa7...
key2 gd34TcaDzDgsY9JtDNMUgLDOItUU0Qur3CBo6Q...
```

## <a name="add-the-blob-uri-to-the-host-file"></a>Dodawanie identyfikatora URI obiektu BLOB do pliku hosta

Identyfikator URI obiektu BLOB w pliku hosts został już dodany przez klienta używanego do nawiązywania połączenia z platformą Azure Blob Storage w sekcji "krok 5: Modyfikowanie pliku hosta pod kątem rozpoznawania nazw punktów końcowych" [na potrzeby wdrażania maszyn wirtualnych na urządzeniu brzegowym Azure Stack za pośrednictwem Azure PowerShell](./azure-stack-edge-gpu-connect-resource-manager.md#step-5-modify-host-file-for-endpoint-name-resolution). Ten wpis został użyty do dodania identyfikatora URI obiektu BLOB:

\<Azure consistent network services VIP \>\<storage name\>. blob. \<appliance name\> .\<dnsdomain\>

## <a name="install-certificates"></a>Instalowanie certyfikatów

W przypadku korzystania z protokołu HTTPS należy zainstalować odpowiednie certyfikaty na urządzeniu. W tym miejscu należy zainstalować certyfikat punktu końcowego obiektu BLOB. Aby uzyskać więcej informacji, zobacz [Korzystanie z certyfikatów przy użyciu usługi Azure Stack EDGE Pro z urządzeniem GPU](azure-stack-edge-gpu-manage-certificates.md).

## <a name="upload-a-vhd"></a>Przekazywanie wirtualnego dysku twardego

Skopiuj wszystkie obrazy dysków, które mają być używane do stronicowych obiektów BLOB na lokalnym koncie magazynu, które zostało utworzone wcześniej. Możesz użyć narzędzia, takiego jak [AzCopy](../storage/common/storage-use-azcopy-v10.md) , aby przekazać wirtualny dysk twardy (VHD) do konta magazynu. 

<!--Before you use AzCopy, make sure that the [AzCopy is configured correctly](#configure-azcopy) for use with the blob storage REST API version that you're using with your Azure Stack Edge Pro device.

```powershell
AzCopy /Source:<sourceDirectoryForVHD> /Dest:<blobContainerUri> /DestKey:<storageAccountKey> /Y /S /V /NC:32  /BlobType:page /destType:blob 
```

> [!NOTE]
> Set `BlobType` to `page` for creating a managed disk out of VHD. Set `BlobType` to `block` when you're writing to tiered storage accounts by using AzCopy.

You can download the disk images from Azure Marketplace. For more information, see [Get the virtual disk image from Azure Marketplace](azure-stack-edge-j-series-create-virtual-machine-image.md).

Here's some example output that uses AzCopy 7.3. For more information about this command, see [Upload VHD file to storage account by using AzCopy](../devtest-labs/devtest-lab-upload-vhd-using-azcopy.md).


```powershell
AzCopy /Source:\\hcsfs\scratch\vm_vhds\linux\ /Dest:http://sa191113014333.blob.dbe-1dcmhq2.microsoftdatabox.com/vmimages /DestKey:gJKoyX2Amg0Zytd1ogA1kQ2xqudMHn7ljcDtkJRHwMZbMK== /Y /S /V /NC:32 /BlobType:page /destType:blob /z:2e7d7d27-c983-410c-b4aa-b0aa668af0c6
```-->
Użyj następujących poleceń z AzCopy 10:  

```powershell
$StorageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName <ResourceGroupName> -Name <StorageAccountName>)[0].Value

$endPoint = (Get-AzureRmStorageAccount -name <StorageAccountName> -ResourceGroupName <ResourceGroupName>).PrimaryEndpoints.Blob

$StorageAccountContext = New-AzureStorageContext -StorageAccountName <StorageAccountName> -StorageAccountKey <StorageAccountKey> -Endpoint <Endpoint>

$StorageAccountSAS = New-AzureStorageAccountSASToken -Service Blob,File,Queue,Table -ResourceType Container,Service,Object -Permission "acdlrw" -Context <StorageAccountContext> -Protocol HttpsOnly

<AzCopy exe path> cp "Full VHD path" "<BlobEndPoint>/<ContainerName><StorageAccountSAS>"
```

Oto przykładowe dane wyjściowe: 

```powershell
$ContainerName = <ContainerName>
$ResourceGroupName = <ResourceGroupName>
$StorageAccountName = <StorageAccountName>
$VHDPath = "Full VHD Path"
$VHDFile = <VHDFileName>

$StorageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $ResourceGroupName -Name $StorageAccountName)[0].Value

$endPoint = (Get-AzureRmStorageAccount -name $StorageAccountName -resourcegroupname $ResourceGroupName).PrimaryEndpoints.Blob

$StorageAccountContext = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey -Endpoint $endPoint

$StorageAccountSAS = New-AzureStorageAccountSASToken -Service Blob,File,Queue,Table -ResourceType Container,Service,Object -Permission "acdlrw" -Context $StorageAccountContext -Protocol HttpsOnly

C:\AzCopy.exe  cp "$VHDPath\$VHDFile" "$endPoint$ContainerName$StorageAccountSAS"
```

## <a name="create-a-managed-disk-from-the-vhd"></a>Tworzenie dysku zarządzanego na podstawie dysku VHD

Aby utworzyć dysk zarządzany na podstawie przekazanego wirtualnego dysku twardego, uruchom następujące polecenie:

```powershell
$DiskConfig = New-AzureRmDiskConfig -Location DBELocal -CreateOption Import -SourceUri "Source URL for your VHD"
```
Oto przykładowe dane wyjściowe: 

<code>
$DiskConfig = New-AzureRmDiskConfig -Location DBELocal -CreateOption Import –SourceUri http://</code><code>sa191113014333.blob.dbe-1dcmhq2.microsoftdatabox.com/vmimages/ubuntu13.vhd</code> 

```powershell
New-AzureRMDisk -ResourceGroupName <Resource group name> -DiskName <Disk name> -Disk $DiskConfig
```

Oto przykładowe dane wyjściowe. Aby uzyskać więcej informacji na temat tego polecenia cmdlet, zobacz polecenie [New-AzureRmDisk](/powershell/module/azurerm.compute/new-azurermdisk?view=azurermps-6.13.0&preserve-view=true).

```powershell
Tags               :
New-AzureRmDisk -ResourceGroupName rg191113014333 -DiskName ld191113014333 -Disk $DiskConfig

ResourceGroupName  : rg191113014333
ManagedBy          :
Sku                : Microsoft.Azure.Management.Compute.Models.DiskSku
Zones              :
TimeCreated        : 11/13/2019 1:49:07 PM
OsType             :
CreationData       : Microsoft.Azure.Management.Compute.Models.CreationData
DiskSizeGB         : 30
EncryptionSettings :
ProvisioningState  : Succeeded
Id                 : /subscriptions/a4257fde-b946-4e01-ade7-674760b8d1a3/resourceGroups/rg191113014333/providers/Micros
                     oft.Compute/disks/ld191113014333
Name               : ld191113014333
Type               : Microsoft.Compute/disks
Location           : DBELocal
Tags               : {}
```

## <a name="create-a-vm-image-from-the-image-managed-disk"></a>Tworzenie obrazu maszyny wirtualnej na podstawie dysku zarządzanego obrazu

Aby utworzyć obraz maszyny wirtualnej na podstawie dysku zarządzanego, uruchom następujące polecenie. Zastąp *\<Disk name>* *\<OS type>* wartości, i *\<Disk size>* wartościami rzeczywistymi.

```powershell
$imageConfig = New-AzureRmImageConfig -Location DBELocal
$ManagedDiskId = (Get-AzureRmDisk -Name <Disk name> -ResourceGroupName <Resource group name>).Id
Set-AzureRmImageOsDisk -Image $imageConfig -OsType '<OS type>' -OsState 'Generalized' -DiskSizeGB <Disk size> -ManagedDiskId $ManagedDiskId 

The supported OS types are Linux and Windows.

For OS Type=Linux, for example:
Set-AzureRmImageOsDisk -Image $imageConfig -OsType 'Linux' -OsState 'Generalized' -DiskSizeGB <Disk size> -ManagedDiskId $ManagedDiskId
New-AzureRmImage -Image $imageConfig -ImageName <Image name>  -ResourceGroupName <Resource group name>
```

Oto przykładowe dane wyjściowe. Aby uzyskać więcej informacji na temat tego polecenia cmdlet, zobacz polecenie [New-AzureRmImage](/powershell/module/azurerm.compute/new-azurermimage?view=azurermps-6.13.0&preserve-view=true).

```powershell
New-AzureRmImage -Image Microsoft.Azure.Commands.Compute.Automation.Models.PSImage -ImageName ig191113014333  -ResourceGroupName rg191113014333
ResourceGroupName    : rg191113014333
SourceVirtualMachine :
StorageProfile       : Microsoft.Azure.Management.Compute.Models.ImageStorageProfile
ProvisioningState    : Succeeded
Id                   : /subscriptions/a4257fde-b946-4e01-ade7-674760b8d1a3/resourceGroups/rg191113014333/providers/Micr
                       osoft.Compute/images/ig191113014333
Name                 : ig191113014333
Type                 : Microsoft.Compute/images
Location             : dbelocal
Tags                 : {}
```

## <a name="create-your-vm-with-previously-created-resources"></a>Tworzenie maszyny wirtualnej przy użyciu wcześniej utworzonych zasobów

Przed utworzeniem i wdrożeniem maszyny wirtualnej należy utworzyć jedną sieć wirtualną i skojarzyć z nią interfejs sieci wirtualnej.

> [!IMPORTANT]
> Mają zastosowanie następujące zasady:
> - Można utworzyć tylko jedną sieć wirtualną, nawet w grupach zasobów. Sieć wirtualna musi mieć dokładnie taką samą przestrzeń adresową co sieć logiczna.
> - Sieć wirtualna może mieć tylko jedną podsieć. Podsieć musi mieć dokładnie taką samą przestrzeń adresową co sieć wirtualna.
> - Podczas tworzenia karty sieci wirtualnej można użyć tylko metody alokacji statycznej. Użytkownik musi podać prywatny adres IP.

### <a name="query-the-automatically-created-virtual-network"></a>Zbadaj automatycznie utworzoną sieć wirtualną

Po włączeniu obliczeń z poziomu lokalnego interfejsu użytkownika urządzenia Sieć wirtualna o nazwie `ASEVNET` zostanie utworzona automatycznie, w obszarze `ASERG` Grupa zasobów. 

Użyj następującego polecenia, aby wykonać zapytanie do istniejącej sieci wirtualnej:

```powershell
$aRmVN = Get-AzureRMVirtualNetwork -Name ASEVNET -ResourceGroupName ASERG 
```

<!--```powershell
$subNetId=New-AzureRmVirtualNetworkSubnetConfig -Name <Subnet name> -AddressPrefix <Address Prefix>
$aRmVN = New-AzureRmVirtualNetwork -ResourceGroupName <Resource group name> -Name <Vnet name> -Location DBELocal -AddressPrefix <Address prefix> -Subnet $subNetId
```-->

### <a name="create-a-virtual-network-interface-card"></a>Tworzenie wirtualnej karty sieciowej

Aby utworzyć kartę sieci wirtualnej przy użyciu identyfikatora podsieci sieci wirtualnej, uruchom następujące polecenie:

```powershell
$ipConfig = New-AzureRmNetworkInterfaceIpConfig -Name <IP config Name> -SubnetId $aRmVN.Subnets[0].Id -PrivateIpAddress <Private IP>
$Nic = New-AzureRmNetworkInterface -Name <Nic name> -ResourceGroupName <Resource group name> -Location DBELocal -IpConfiguration $ipConfig
```

Oto przykładowe dane wyjściowe:

```powershell
PS C:\Users\Administrator> $subNetId=New-AzureRmVirtualNetworkSubnetConfig -Name my-ase-subnet -AddressPrefix "5.5.0.0/16"

PS C:\Users\Administrator> $aRmVN = New-AzureRmVirtualNetwork -ResourceGroupName Resource-my-ase -Name my-ase-virtualnetwork -Location DBELocal -AddressPrefix "5.5.0.0/16" -Subnet $subNetId
WARNING: The output object type of this cmdlet will be modified in a future release.
PS C:\Users\Administrator> $ipConfig = New-AzureRmNetworkInterfaceIpConfig -Name my-ase-ip -SubnetId $aRmVN.Subnets[0].Id
PS C:\Users\Administrator> $Nic = New-AzureRmNetworkInterface -Name my-ase-nic -ResourceGroupName Resource-my-ase -Location DBELocal -IpConfiguration $ipConfig
WARNING: The output object type of this cmdlet will be modified in a future release.

PS C:\Users\Administrator> $Nic

PS C:\Users\Administrator> (Get-AzureRmNetworkInterface)[0]

Name                        : nic200108020444
ResourceGroupName           : rg200108020444
Location                    : dbelocal
Id                          : /subscriptions/a4257fde-b946-4e01-ade7-674760b8d1a3/resourceGroups/rg200108020444/providers/Microsoft.Network/networ
                              kInterfaces/nic200108020444
Etag                        : W/"f9d1759d-4d49-42fa-8826-e218e0b1d355"
ResourceGuid                : 3851ae62-c13e-4416-9386-e21d9a2fef0f
ProvisioningState           : Succeeded
Tags                        :
VirtualMachine              : {
                                "Id": "/subscriptions/a4257fde-b946-4e01-ade7-674760b8d1a3/resourceGroups/rg200108020444/providers/Microsoft.Compu
                              te/virtualMachines/VM200108020444"
                              }
IpConfigurations            : [
                                {
                                  "Name": "ip200108020444",
                                  "Etag": "W/\"f9d1759d-4d49-42fa-8826-e218e0b1d355\"",
                                  "Id": "/subscriptions/a4257fde-b946-4e01-ade7-674760b8d1a3/resourceGroups/rg200108020444/providers/Microsoft.Net
                              work/networkInterfaces/nic200108020444/ipConfigurations/ip200108020444",
                                  "PrivateIpAddress": "5.5.166.65",
                                  "PrivateIpAllocationMethod": "Static",
                                  "Subnet": {
                                    "Id": "/subscriptions/a4257fde-b946-4e01-ade7-674760b8d1a3/resourceGroups/DbeSystemRG/providers/Microsoft.Netw
                              ork/virtualNetworks/vSwitch1/subnets/subnet123",
                                    "ResourceNavigationLinks": [],
                                    "ServiceEndpoints": []
                                  },
                                  "ProvisioningState": "Succeeded",
                                  "PrivateIpAddressVersion": "IPv4",
                                  "LoadBalancerBackendAddressPools": [],
                                  "LoadBalancerInboundNatRules": [],
                                  "Primary": true,
                                  "ApplicationGatewayBackendAddressPools": [],
                                  "ApplicationSecurityGroups": []
                                }
                              ]
DnsSettings                 : {
                                "DnsServers": [],
                                "AppliedDnsServers": []
                              }
EnableIPForwarding          : False
EnableAcceleratedNetworking : False
NetworkSecurityGroup        : null
Primary                     : True
MacAddress                  : 00155D18E432                :
```

Opcjonalnie podczas tworzenia karty interfejsu sieci wirtualnej dla maszyny wirtualnej można przekazać publiczny adres IP. W tym przypadku publiczny adres IP zwraca prywatny adres IP. 

```powershell
New-AzureRmPublicIPAddress -Name <Public IP> -ResourceGroupName <ResourceGroupName> -AllocationMethod Static -Location DBELocal
$publicIP = (Get-AzureRmPublicIPAddress -Name <Public IP> -ResourceGroupName <Resource group name>).Id
$ipConfig = New-AzureRmNetworkInterfaceIpConfig -Name <ConfigName> -PublicIpAddressId $publicIP -SubnetId $subNetId
```

### <a name="create-a-vm"></a>Tworzenie maszyny wirtualnej

Teraz można użyć obrazu maszyny wirtualnej do utworzenia maszyny wirtualnej i dołączenia jej do utworzonej wcześniej sieci wirtualnej.

```powershell
$pass = ConvertTo-SecureString "<Password>" -AsPlainText -Force;
$cred = New-Object System.Management.Automation.PSCredential("<Enter username>", $pass)
```

Po utworzeniu i włączeniu maszyny wirtualnej w celu zalogowania się do niej zostanie użyta następująca nazwa użytkownika i hasło.

```powershell
$VirtualMachine = New-AzureRmVMConfig -VMName <VM name> -VMSize "Standard_D1_v2"

$VirtualMachine = Set-AzureRmVMOperatingSystem -VM $VirtualMachine -<OS type> -ComputerName <Your computer Name> -Credential $cred

$VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -Name <OS Disk Name> -Caching "ReadWrite" -CreateOption "FromImage" -Linux -StorageAccountType StandardLRS

$nicID = (Get-AzureRmNetworkInterface -Name <nic name> -ResourceGroupName <Resource Group Name>).Id

$VirtualMachine = Add-AzureRmVMNetworkInterface -VM $VirtualMachine -Id $nicID

$image = (Get-AzureRmImage -ResourceGroupName <Resource Group Name> -ImageName $ImageName).Id

$VirtualMachine = Set-AzureRmVMSourceImage -VM $VirtualMachine -Id $image

New-AzureRmVM -ResourceGroupName <Resource Group Name> -Location DBELocal -VM $VirtualMachine -Verbose
```

## <a name="connect-to-the-vm"></a>Łączenie z maszyną wirtualną

W zależności od tego, czy utworzono maszynę wirtualną z systemem Windows, czy maszynę wirtualną z systemem Linux, instrukcje dotyczące połączenia mogą się różnić.

### <a name="connect-to-a-linux-vm"></a>Nawiązywanie połączenia z maszyną wirtualną z systemem Linux

Aby nawiązać połączenie z maszyną wirtualną z systemem Linux, wykonaj następujące czynności:

[!INCLUDE [azure-stack-edge-gateway-connect-vm](../../includes/azure-stack-edge-gateway-connect-virtual-machine-linux.md)]

### <a name="connect-to-a-windows-vm"></a>Nawiązywanie połączenia z maszyną wirtualną z systemem Windows

Aby nawiązać połączenie z maszyną wirtualną z systemem Windows, wykonaj następujące czynności:

[!INCLUDE [azure-stack-edge-gateway-connect-vm](../../includes/azure-stack-edge-gateway-connect-virtual-machine-windows.md)]


<!--Connect to the VM by using the private IP that you passed during the VM creation.

Open an SSH session to connect with the IP address.

`ssh -l <username> <ip address>`

When you're prompted, provide the password that you used when creating the VM.

If you need to provide the SSH key, use this command:

ssh -i c:/users/Administrator/.ssh/id_rsa Administrator@5.5.41.236

If you used a public IP address during VM creation, you can use that IP to connect to the VM. To get the public IP: 

```powershell
$publicIp = Get-AzureRmPublicIpAddress -Name <Public IP> -ResourceGroupName <Resource group name>
```
The public IP in this instance is the same as the private IP that you passed during the virtual network interface creation.-->


## <a name="manage-the-vm"></a>Zarządzanie maszyną wirtualną

W poniższych sekcjach opisano niektóre typowe operacje, które można utworzyć na urządzeniu z systemem Azure Stack brzeg Pro.

### <a name="list-vms-that-are-running-on-the-device"></a>Wyświetlanie listy maszyn wirtualnych uruchomionych na urządzeniu

Aby zwrócić listę wszystkich maszyn wirtualnych, które są uruchomione na urządzeniu Azure Stack Edge, uruchom następujące polecenie:


`Get-AzureRmVM -ResourceGroupName <String> -Name <String>`
 

### <a name="turn-on-the-vm"></a>Włącz maszynę wirtualną

Aby włączyć maszynę wirtualną działającą na urządzeniu, uruchom następujące polecenie cmdlet:

`Start-AzureRmVM [-Name] <String> [-ResourceGroupName] <String>`

Aby uzyskać więcej informacji na temat tego polecenia cmdlet, zobacz [Start-AzureRmVM](/powershell/module/azurerm.compute/start-azurermvm?view=azurermps-6.13.0&preserve-view=true).

### <a name="suspend-or-shut-down-the-vm"></a>Wstrzymywanie lub wyłączanie maszyny wirtualnej

Aby zatrzymać lub wyłączyć maszynę wirtualną, która jest uruchomiona na urządzeniu, uruchom następujące polecenie cmdlet:


```powershell
Stop-AzureRmVM [-Name] <String> [-StayProvisioned] [-ResourceGroupName] <String>
```

Aby uzyskać więcej informacji na temat tego polecenia cmdlet, zobacz [polecenie cmdlet Stop-AzureRmVM](/powershell/module/azurerm.compute/stop-azurermvm?view=azurermps-6.13.0&preserve-view=true).

### <a name="add-a-data-disk"></a>Dodawanie dysku z danymi

Jeśli wymagania dotyczące obciążenia maszyny wirtualnej rosną, może być konieczne dodanie dysku z danymi. W tym celu uruchom następujące polecenie:

```powershell
Add-AzureRmVMDataDisk -VM $VirtualMachine -Name "disk1" -VhdUri "https://contoso.blob.core.windows.net/vhds/diskstandard03.vhd" -LUN 0 -Caching ReadOnly -DiskSizeinGB 1 -CreateOption Empty 
 
Update-AzureRmVM -ResourceGroupName "<Resource Group Name string>" -VM $VirtualMachine
```

### <a name="delete-the-vm"></a>Usuwanie maszyny wirtualnej

Aby usunąć maszynę wirtualną z urządzenia, uruchom następujące polecenie cmdlet:

```powershell
Remove-AzureRmVM [-Name] <String> [-ResourceGroupName] <String>
```

Aby uzyskać więcej informacji na temat tego polecenia cmdlet, zobacz [polecenie cmdlet Remove-AzureRmVm](/powershell/module/azurerm.compute/remove-azurermvm?view=azurermps-6.13.0&preserve-view=true).

## <a name="next-steps"></a>Następne kroki

[Polecenia cmdlet Azure Resource Manager](/powershell/module/azurerm.resources/?view=azurermps-6.13.0&preserve-view=true)