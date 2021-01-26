---
title: Wdrażaj maszyny wirtualne na urządzeniu z systemem Azure Stack Edge przy użyciu procesora GPU Pro Azure PowerShell
description: W tym artykule opisano sposób tworzenia maszyn wirtualnych i zarządzania nimi na urządzeniu z systemem Azure Stack Edge w systemie GPU przy użyciu Azure PowerShell.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 01/22/2021
ms.author: alkohli
ms.openlocfilehash: 1d286e7661fa14dd63bd55b133c39414e04decc6
ms.sourcegitcommit: fc8ce6ff76e64486d5acd7be24faf819f0a7be1d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/26/2021
ms.locfileid: "98802987"
---
# <a name="deploy-vms-on-your-azure-stack-edge-pro-gpu-device-via-azure-powershell"></a>Wdrażaj maszyny wirtualne na urządzeniu z systemem Azure Stack Edge przy użyciu procesora GPU Pro Azure PowerShell

W tym artykule opisano sposób tworzenia i zarządzania maszyną wirtualną na urządzeniu Azure Stack EDGE Pro przy użyciu Azure PowerShell. Ten artykuł ma zastosowanie do Azure Stack brzegowych procesorów GPU, Azure Stack EDGE Pro R i Azure Stack Edge.

## <a name="vm-deployment-workflow"></a>Przepływ pracy wdrożenia maszyny wirtualnej

Przepływ pracy wdrażania przedstawiono na poniższym diagramie.

![Przepływ pracy wdrożenia maszyny wirtualnej](media/azure-stack-edge-gpu-deploy-virtual-machine-powershell/vm-workflow-r.svg)

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [azure-stack-edge-gateway-deploy-vm-prerequisites](../../includes/azure-stack-edge-gateway-deploy-virtual-machine-prerequisites.md)]


## <a name="query-for-built-in-subscription-on-the-device"></a>Zapytanie o wbudowaną subskrypcję na urządzeniu

W przypadku Azure Resource Manager obsługiwana jest tylko jedna subskrypcja stałych widocznych dla użytkownika. Ta subskrypcja jest unikatowa dla urządzenia, a nie można zmienić nazwy subskrypcji lub identyfikatora subskrypcji.

Ta subskrypcja zawiera wszystkie zasoby, które zostały utworzone w celu utworzenia maszyny wirtualnej. 

> [!IMPORTANT]
> Ta subskrypcja jest tworzona po włączeniu maszyn wirtualnych z poziomu Azure Portal i lokalnie na urządzeniu.

Ta subskrypcja służy do wdrażania maszyn wirtualnych.

1.  Aby wyświetlić listę tej subskrypcji, wpisz:

    ```powershell
    Get-AzureRmSubscription
    ```
    
    Poniżej pokazano przykładowe dane wyjściowe.

    ```powershell
    PS C:\windows\system32> Get-AzureRmSubscription
    
    Name                 Id                 TenantId          State
    ----                 --                --------           -----
    Default Provider Subscription A4257FDE-B946-4E01-ADE7-674760B8D1A3 c0257de7-538f-415c-993a-1b87a031879d Enabled
    
    PS C:\windows\system32>
    ```
        
3.  Pobierz listę zarejestrowanych dostawców zasobów uruchomionych na urządzeniu. Ta lista zawiera zazwyczaj obliczenia, Sieć i magazyn.

    ```powershell
    Get-AzureRMResourceProvider
    ```

    > [!NOTE]
    > Dostawcy zasobów są wstępnie zarejestrowani i nie można ich modyfikować ani zmieniać.
    
    Poniżej przedstawiono przykładowe dane wyjściowe:

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

Utwórz grupę zasobów platformy Azure za pomocą polecenia [New-AzureRmResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Grupa zasobów to logiczny kontener, w którym są wdrażane i zarządzane zasoby platformy Azure, takie jak konto magazynu, dysk zarządzany i zarządzany.

> [!IMPORTANT]
> Wszystkie zasoby są tworzone w tej samej lokalizacji, w której znajduje się urządzenie, a lokalizacja jest ustawiona na **DBELocal**.

```powershell
New-AzureRmResourceGroup -Name <Resource group name> -Location DBELocal
```

Poniżej pokazano przykładowe dane wyjściowe.

```powershell
New-AzureRmResourceGroup -Name rg191113014333 -Location DBELocal 
Successfully created Resource Group:rg191113014333
```

## <a name="create-a-storage-account"></a>Tworzenie konta magazynu

Utwórz nowe konto magazynu przy użyciu grupy zasobów utworzonej w poprzednim kroku. To konto jest kontem **magazynu lokalnego** , które zostanie użyte do przekazania obrazu dysku wirtualnego dla maszyny wirtualnej.

```powershell
New-AzureRmStorageAccount -Name <Storage account name> -ResourceGroupName <Resource group name> -Location DBELocal -SkuName Standard_LRS
```

> [!NOTE]
> Tylko lokalne konta magazynu, takie jak Magazyn lokalnie nadmiarowy (Standard_LRS lub Premium_LRS), można tworzyć za pośrednictwem Azure Resource Manager. Aby utworzyć konta magazynu warstwowego, zapoznaj się z instrukcjami w temacie [Dodawanie i nawiązywanie połączenia z kontami magazynu w witrynie Azure Stack EDGE Pro](azure-stack-edge-j-series-deploy-add-storage-accounts.md).

Poniżej pokazano przykładowe dane wyjściowe.

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

Aby uzyskać klucz konta magazynu, uruchom `Get-AzureRmStorageAccountKey` polecenie. Przykładowe dane wyjściowe tego polecenia przedstawiono poniżej.

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

## <a name="add-blob-uri-to-hosts-file"></a>Dodaj identyfikator URI obiektu BLOB do pliku hosts

Identyfikator URI obiektu BLOB w pliku hosts został już dodany przez klienta używanego do łączenia się z usługą BLOB Storage w sekcji [Modyfikowanie pliku hosta dla rozpoznawania nazw punktów końcowych](azure-stack-edge-j-series-connect-resource-manager.md#step-5-modify-host-file-for-endpoint-name-resolution). Ten wpis został użyty do dodania identyfikatora URI obiektu BLOB:

\<Azure consistent network services VIP \>\<storage name\>. blob. \<appliance name\> .\<dnsdomain\>


## <a name="install-certificates"></a>Instalowanie certyfikatów

Jeśli używasz *protokołu HTTPS*, musisz zainstalować odpowiednie certyfikaty na urządzeniu. W takim przypadku należy zainstalować certyfikat punktu końcowego obiektu BLOB. Aby uzyskać więcej informacji, zobacz jak tworzyć i przekazywać certyfikaty w artykule [Zarządzanie certyfikatami](azure-stack-edge-j-series-manage-certificates.md).

## <a name="upload-a-vhd"></a>Przekazywanie wirtualnego dysku twardego

Skopiuj wszystkie obrazy dysków, które mają być używane do stronicowych obiektów BLOB na lokalnym koncie magazynu utworzonym w poprzednich krokach. Możesz użyć narzędzia, takiego jak [AzCopy](../storage/common/storage-use-azcopy-v10.md) , aby przekazać dysk VHD do konta magazynu utworzonego w poprzednich krokach. 

<!--Before you use AzCopy, make sure that the [AzCopy is configured correctly](#configure-azcopy) for use with the blob storage REST API version that you are using with your Azure Stack Edge Pro device.

```powershell
AzCopy /Source:<sourceDirectoryForVHD> /Dest:<blobContainerUri> /DestKey:<storageAccountKey> /Y /S /V /NC:32  /BlobType:page /destType:blob 
```

> [!NOTE]
> Set `BlobType` to page for creating a managed disk out of VHD. Set `BlobType` to block when writing to tiered storage accounts using AzCopy.

You can download the disk images from the marketplace. For detailed steps, go to [Get the virtual disk image from Azure marketplace](azure-stack-edge-j-series-create-virtual-machine-image.md).

A sample output using AzCopy 7.3 is shown below. For more information on this command, go to [Upload VHD file to storage account using AzCopy](../devtest-labs/devtest-lab-upload-vhd-using-azcopy.md).


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

## <a name="create-managed-disks-from-the-vhd"></a>Tworzenie dysków zarządzanych na podstawie wirtualnego dysku twardego

Utwórz dysk zarządzany na podstawie przekazanego wirtualnego dysku twardego.

```powershell
$DiskConfig = New-AzureRmDiskConfig -Location DBELocal -CreateOption Import -SourceUri "Source URL for your VHD"
```
Poniżej przedstawiono przykładowe dane wyjściowe: 
<code>
$DiskConfig = New-AzureRmDiskConfig -Location DBELocal -CreateOption Import –SourceUri http://</code><code>sa191113014333.blob.dbe-1dcmhq2.microsoftdatabox.com/vmimages/ubuntu13.vhd</code> 

```powershell
New-AzureRMDisk -ResourceGroupName <Resource group name> -DiskName <Disk name> -Disk $DiskConfig
```

Poniżej pokazano przykładowe dane wyjściowe. Aby uzyskać więcej informacji na temat tego polecenia cmdlet, przejdź do pozycji [New-AzureRmDisk](/powershell/module/azurerm.compute/new-azurermdisk?view=azurermps-6.13.0&preserve-view=true).

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

Użyj następującego polecenia, aby utworzyć obraz maszyny wirtualnej z dysku zarządzanego. Zastąp wartości w ramach \< \> wybranych nazw.

```powershell
$imageConfig = New-AzureRmImageConfig -Location DBELocal
$ManagedDiskId = (Get-AzureRmDisk -Name <Disk name> -ResourceGroupName <Resource group name>).Id
Set-AzureRmImageOsDisk -Image $imageConfig -OsType '<OS type>' -OsState 'Generalized' -DiskSizeGB <Disk size> -ManagedDiskId $ManagedDiskId 

The supported OS types are Linux and Windows.

For OS Type=Linux, for example:
Set-AzureRmImageOsDisk -Image $imageConfig -OsType 'Linux' -OsState 'Generalized' -DiskSizeGB <Disk size> -ManagedDiskId $ManagedDiskId
New-AzureRmImage -Image $imageConfig -ImageName <Image name>  -ResourceGroupName <Resource group name>
```

Poniżej pokazano przykładowe dane wyjściowe. Aby uzyskać więcej informacji na temat tego polecenia cmdlet, przejdź do pozycji [New-AzureRmImage](/powershell/module/azurerm.compute/new-azurermimage?view=azurermps-6.13.0&preserve-view=true).

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

## <a name="create-vm-with-previously-created-resources"></a>Tworzenie maszyny wirtualnej z wcześniej utworzonymi zasobami

Przed utworzeniem i wdrożeniem maszyny wirtualnej należy utworzyć jedną sieć wirtualną i skojarzyć interfejs sieci wirtualnej.

> [!IMPORTANT]
> Podczas tworzenia sieci wirtualnej i interfejsu sieci wirtualnej są stosowane następujące reguły:
> - Można utworzyć tylko jedną sieć wirtualną (nawet w grupach zasobów) i musi ona dokładnie pasować do sieci logicznej pod względem przestrzeni adresowej.
> - W sieci wirtualnej będzie dozwolona tylko jedna podsieć. Podsieć musi być dokładną tą samą przestrzenią adresową co sieć wirtualna.
> - Podczas tworzenia wirtualnej karty sieciowej będzie dozwolona tylko statyczna metoda alokacji, a użytkownik musi podać prywatny adres IP.

 
**Zbadaj automatycznie utworzoną sieć wirtualną**

Po włączeniu obliczeń z poziomu lokalnego interfejsu użytkownika urządzenia Sieć wirtualna `ASEVNET` zostanie utworzona automatycznie w obszarze `ASERG` Grupa zasobów. Użyj następującego polecenia, aby wykonać zapytanie do istniejącej sieci wirtualnej:

```powershell
$aRmVN = Get-AzureRMVirtualNetwork -Name ASEVNET -ResourceGroupName ASERG 
```

<!--```powershell
$subNetId=New-AzureRmVirtualNetworkSubnetConfig -Name <Subnet name> -AddressPrefix <Address Prefix>
$aRmVN = New-AzureRmVirtualNetwork -ResourceGroupName <Resource group name> -Name <Vnet name> -Location DBELocal -AddressPrefix <Address prefix> -Subnet $subNetId
```-->

**Tworzenie wirtualnej karty sieciowej przy użyciu identyfikatora podsieci sieci wirtualnej**

```powershell
$ipConfig = New-AzureRmNetworkInterfaceIpConfig -Name <IP config Name> -SubnetId $aRmVN.Subnets[0].Id -PrivateIpAddress <Private IP>
$Nic = New-AzureRmNetworkInterface -Name <Nic name> -ResourceGroupName <Resource group name> -Location DBELocal -IpConfiguration $ipConfig
```

Poniżej przedstawiono przykładowe dane wyjściowe tych poleceń:

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

Opcjonalnie podczas tworzenia wirtualnej karty sieciowej dla maszyny wirtualnej można przekazać publiczny adres IP. W tym przypadku publiczny adres IP zwróci prywatny adres IP. 

```powershell
New-AzureRmPublicIPAddress -Name <Public IP> -ResourceGroupName <ResourceGroupName> -AllocationMethod Static -Location DBELocal
$publicIP = (Get-AzureRmPublicIPAddress -Name <Public IP> -ResourceGroupName <Resource group name>).Id
$ipConfig = New-AzureRmNetworkInterfaceIpConfig -Name <ConfigName> -PublicIpAddressId $publicIP -SubnetId $subNetId
```


**Tworzenie maszyny wirtualnej**

Teraz można użyć obrazu maszyny wirtualnej do utworzenia maszyny wirtualnej i dołączenia jej do utworzonej wcześniej sieci wirtualnej.

```powershell
$pass = ConvertTo-SecureString "<Password>" -AsPlainText -Force;
$cred = New-Object System.Management.Automation.PSCredential("<Enter username>", $pass)

You will use this username, password to login to the VM, once it is created and powered up.

$VirtualMachine = New-AzureRmVMConfig -VMName <VM name> -VMSize "Standard_D1_v2"

$VirtualMachine = Set-AzureRmVMOperatingSystem -VM $VirtualMachine -<OS type> -ComputerName <Your computer Name> -Credential $cred

$VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -Name <OS Disk Name> -Caching "ReadWrite" -CreateOption "FromImage" -Linux -StorageAccountType StandardLRS

$nicID = (Get-AzureRmNetworkInterface -Name <nic name> -ResourceGroupName <Resource Group Name>).Id

$VirtualMachine = Add-AzureRmVMNetworkInterface -VM $VirtualMachine -Id $nicID

$image = (Get-AzureRmImage -ResourceGroupName <Resource Group Name> -ImageName $ImageName).Id

$VirtualMachine = Set-AzureRmVMSourceImage -VM $VirtualMachine -Id $image

New-AzureRmVM -ResourceGroupName <Resource Group Name> -Location DBELocal -VM $VirtualMachine -Verbose
```

## <a name="connect-to-a-vm"></a>Łączenie z maszyną wirtualną

W zależności od tego, czy utworzono maszynę wirtualną z systemem Windows, czy z systemem Linux, kroki do połączenia mogą być różne.

### <a name="connect-to-linux-vm"></a>Nawiązywanie połączenia z maszyną wirtualną z systemem Linux

Wykonaj następujące kroki, aby nawiązać połączenie z maszyną wirtualną z systemem Linux.

[!INCLUDE [azure-stack-edge-gateway-connect-vm](../../includes/azure-stack-edge-gateway-connect-virtual-machine-linux.md)]

### <a name="connect-to-windows-vm"></a>Nawiązywanie połączenia z maszyną wirtualną z systemem Windows

Wykonaj następujące kroki, aby nawiązać połączenie z maszyną wirtualną z systemem Windows.

[!INCLUDE [azure-stack-edge-gateway-connect-vm](../../includes/azure-stack-edge-gateway-connect-virtual-machine-windows.md)]


<!--Connect to the VM using the private IP that you passed during the VM creation.

Open an SSH session to connect with the IP address.

`ssh -l <username> <ip address>`

When prompted, provide the password that you used when creating the VM.

If you need to provide the SSH key, use this command.

ssh -i c:/users/Administrator/.ssh/id_rsa Administrator@5.5.41.236

If you used a public IP address during VM creation, you can use that IP to connect to the VM. To get the public IP: 

```powershell
$publicIp = Get-AzureRmPublicIpAddress -Name <Public IP> -ResourceGroupName <Resource group name>
```
The public IP in this case will be the same as the private IP that you passed during virtual network interface creation.-->


## <a name="manage-vm"></a>Zarządzanie maszyną wirtualną

W poniższej sekcji opisano niektóre typowe operacje dotyczące maszyn wirtualnych, które zostaną utworzone na urządzeniu Azure Stack Edge.

### <a name="list-vms-running-on-the-device"></a>Wyświetlanie listy maszyn wirtualnych działających na urządzeniu

Aby zwrócić listę wszystkich maszyn wirtualnych uruchomionych na urządzeniu Azure Stack brzeg Pro, uruchom następujące polecenie.


`Get-AzureRmVM -ResourceGroupName <String> -Name <String>`
 

### <a name="turn-on-the-vm"></a>Włącz maszynę wirtualną

Uruchom następujące polecenie cmdlet, aby włączyć maszynę wirtualną działającą na urządzeniu:


`Start-AzureRmVM [-Name] <String> [-ResourceGroupName] <String>`


Aby uzyskać więcej informacji na temat tego polecenia cmdlet, przejdź do [menu Start-AzureRmVM](/powershell/module/azurerm.compute/start-azurermvm?view=azurermps-6.13.0&preserve-view=true).

### <a name="suspend-or-shut-down-the-vm"></a>Wstrzymywanie lub wyłączanie maszyny wirtualnej

Uruchom następujące polecenie cmdlet, aby zatrzymać lub zamknąć maszynę wirtualną działającą na urządzeniu:


```powershell
Stop-AzureRmVM [-Name] <String> [-StayProvisioned] [-ResourceGroupName] <String>
```


Aby uzyskać więcej informacji na temat tego polecenia cmdlet, przejdź do [polecenia cmdlet Stop-AzureRmVM](/powershell/module/azurerm.compute/stop-azurermvm?view=azurermps-6.13.0&preserve-view=true).

### <a name="add-a-data-disk"></a>Dodawanie dysku z danymi

Jeśli wymagania dotyczące obciążenia maszyny wirtualnej rosną, może być konieczne dodanie dysku z danymi.

```powershell
Add-AzureRmVMDataDisk -VM $VirtualMachine -Name "disk1" -VhdUri "https://contoso.blob.core.windows.net/vhds/diskstandard03.vhd" -LUN 0 -Caching ReadOnly -DiskSizeinGB 1 -CreateOption Empty 
 
Update-AzureRmVM -ResourceGroupName "<Resource Group Name string>" -VM $VirtualMachine
```

### <a name="delete-the-vm"></a>Usuwanie maszyny wirtualnej

Uruchom następujące polecenie cmdlet, aby usunąć maszynę wirtualną z urządzenia:

```powershell
Remove-AzureRmVM [-Name] <String> [-ResourceGroupName] <String>
```

Aby uzyskać więcej informacji na temat tego polecenia cmdlet, przejdź do [polecenia cmdlet Remove-AzureRmVm](/powershell/module/azurerm.compute/remove-azurermvm?view=azurermps-6.13.0&preserve-view=true).



## <a name="next-steps"></a>Następne kroki

[Polecenia cmdlet Azure Resource Manager](/powershell/module/azurerm.resources/?view=azurermps-6.13.0&preserve-view=true)