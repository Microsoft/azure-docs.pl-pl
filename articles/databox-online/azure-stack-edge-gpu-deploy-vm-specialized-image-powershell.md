---
title: Tworzenie obrazów maszyn wirtualnych z wyspecjalizowanego obrazu wirtualnego dysku twardego systemu Windows dla urządzenia z systemem Azure Stack EDGE Pro GPU
description: Opisuje sposób tworzenia obrazów maszyn wirtualnych z wyspecjalizowanych obrazów, zaczynając od wirtualnego dysku twardego systemu Windows lub dysku VHDX. Ten wyspecjalizowany obraz służy do tworzenia obrazów maszyn wirtualnych, które mają być używane z maszynami wirtualnymi wdrożonymi na urządzeniu z systemem Azure Stack Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/30/2021
ms.author: alkohli
ms.openlocfilehash: d03aeb9759fb321b580fa65e06dc09ccde4a44a0
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/07/2021
ms.locfileid: "106556132"
---
# <a name="deploy-a-vm-from-a-specialized-image-on-your-azure-stack-edge-pro-device-via-azure-powershell"></a>Wdróż maszynę wirtualną na podstawie wyspecjalizowanego obrazu na urządzeniu Azure Stack EDGE Pro za pośrednictwem Azure PowerShell 

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

W tym artykule opisano kroki wymagane do wdrożenia maszyny wirtualnej na urządzeniu z systemem Azure Stack Edge z poziomu obrazu specjalistycznego. 

## <a name="about-specialized-images"></a>Informacje o wyspecjalizowanych obrazach

Plik VHD lub VHDX systemu Windows może służyć do tworzenia *wyspecjalizowanego* obrazu lub *uogólnionego* obrazu. Poniższa tabela zawiera podsumowanie najważniejszych różnic między *wyspecjalizowanymi* a *uogólnionymi* obrazami.


|Typ obrazu  |Uogólniony  |Wyspecjalizowany  |
|---------|---------|---------|
|Cel     |Wdrożone w dowolnym systemie         | Przeznaczony dla określonego systemu        |
|Instalacja po rozruchu     | Instalacja jest wymagana podczas pierwszego rozruchu maszyny wirtualnej.          | Instalacja nie jest wymagana. <br> Platforma włącza maszynę wirtualną.        |
|Konfigurowanie     |Nazwa hosta, użytkownik administracyjny i inne wymagane ustawienia specyficzne dla maszyny wirtualnej.         |Wstępnie skonfigurowane.         |
|Używane do     |Utwórz wiele nowych maszyn wirtualnych na podstawie tego samego obrazu.         |Migruj konkretną maszynę lub przywracaj maszynę wirtualną z poprzedniej kopii zapasowej.         |


W tym artykule opisano kroki wymagane do wdrożenia z wyspecjalizowanego obrazu. Aby wdrożyć z uogólnionego obrazu, zobacz [Używanie uogólnionego wirtualnego dysku twardego systemu Windows](azure-stack-edge-gpu-prepare-windows-vhd-generalized-image.md) dla urządzenia.


## <a name="vm-image-workflow"></a>Przepływ pracy obrazu maszyny wirtualnej

Przepływ pracy wysokiego poziomu służący do wdrożenia maszyny wirtualnej na podstawie wyspecjalizowanego obrazu to:

1. Skopiuj wirtualny dysk twardy na konto magazynu lokalnego na urządzeniu z systemem Azure Stack Edge.
1. Utwórz nowy dysk zarządzany na podstawie dysku VHD.
1. Utwórz nową maszynę wirtualną z dysku zarządzanego i Dołącz dysk zarządzany.


## <a name="prerequisites"></a>Wymagania wstępne

Aby można było wdrożyć maszynę wirtualną na urządzeniu za pośrednictwem programu PowerShell, należy się upewnić, że:

- Masz dostęp do klienta, który będzie używany do nawiązywania połączenia z urządzeniem.
    - Klient uruchamia [obsługiwany system operacyjny](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device).
    - Klient jest skonfigurowany do łączenia się z lokalną Azure Resource Manager urządzenia zgodnie z instrukcjami w temacie [Connect to Azure Resource Manager dla urządzenia](azure-stack-edge-gpu-connect-resource-manager.md).

## <a name="verify-the-local-azure-resource-manager-connection"></a>Weryfikowanie lokalnego połączenia Azure Resource Manager

Sprawdź, czy klient może połączyć się z lokalną Azure Resource Manager. 

1. Wywołaj interfejsy API urządzeń lokalnych w celu uwierzytelnienia:

    ```powershell
    Login-AzureRMAccount -EnvironmentName <Environment Name>
    ```

2. Podaj nazwę użytkownika `EdgeArmUser` i hasło, aby połączyć się za pośrednictwem Azure Resource Manager. Jeśli nie odwołujesz hasła, [zresetuj hasło dla Azure Resource Manager](azure-stack-edge-gpu-set-azure-resource-manager-password.md) i Użyj tego hasła do logowania.
 

## <a name="deploy-vm-from-specialized-image"></a>Wdróż maszynę wirtualną z obrazu wyspecjalizowanego

Poniższe sekcje zawierają instrukcje krok po kroku dotyczące wdrażania maszyny wirtualnej na podstawie wyspecjalizowanego obrazu.

## <a name="copy-vhd-to-local-storage-account-on-device"></a>Kopiuj dysk VHD do konta magazynu lokalnego na urządzeniu

Wykonaj następujące kroki, aby skopiować wirtualny dysk twardy do konta magazynu lokalnego:

1. Skopiuj źródłowy wirtualny dysk twardy na konto lokalnego magazynu obiektów BLOB na Azure Stack Edge. 

1. Zwróć uwagę na otrzymany identyfikator URI. Ten identyfikator URI zostanie użyty w późniejszym kroku.
    
    Aby utworzyć konto magazynu lokalnego i uzyskać do niego dostęp, zobacz sekcję [Tworzenie konta magazynu](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md#create-a-storage-account) za pośrednictwem [przekazywania wirtualnego dysku twardego](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md#upload-a-vhd) w artykule: [wdrażanie maszyn wirtualnych na urządzeniu Azure Stack Edge za pośrednictwem Azure PowerShell](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md). 

## <a name="create-a-managed-disk-from-vhd"></a>Tworzenie dysku zarządzanego na podstawie dysku VHD

Wykonaj następujące kroki, aby utworzyć dysk zarządzany na podstawie wirtualnego dysku twardego, który został wcześniej przekazany do konta magazynu:

1. Ustaw niektóre parametry.

    ```powershell
    $VhdURI = <URI of VHD in local storage account>
    $DiskRG = <managed disk resource group>
    $DiskName = <managed disk name>    
    ```
    Oto przykładowe dane wyjściowe.

    ```powershell
    PS C:\WINDOWS\system32> $VHDURI = "https://myasevmsa.blob.myasegpudev.wdshcsso.com/vhds/WindowsServer2016Datacenter.vhd"
    PS C:\WINDOWS\system32> $DiskRG = "myasevm1rg"
    PS C:\WINDOWS\system32> $DiskName = "myasemd1"
    ```
1. Utwórz nowy dysk zarządzany.

    ```powershell
    $DiskConfig = New-AzureRmDiskConfig -Location DBELocal -CreateOption Import -SourceUri $VhdURI
    $disk = New-AzureRMDisk -ResourceGroupName $DiskRG -DiskName $DiskName -Disk $DiskConfig
    ```

    Oto przykładowe dane wyjściowe. Lokalizacja w tym miejscu jest ustawiona na lokalizację lokalnego konta magazynu i zawsze dotyczy `DBELocal` wszystkich kont magazynu lokalnego na urządzeniu z systemem Azure Stack Edge. 

    ```powershell
    PS C:\WINDOWS\system32> $DiskConfig = New-AzureRmDiskConfig -Location DBELocal -CreateOption Import -SourceUri $VHDURI
    PS C:\WINDOWS\system32> $disk = New-AzureRMDisk -ResourceGroupName $DiskRG -DiskName $DiskName -Disk $DiskConfig
    PS C:\WINDOWS\system32>    
    ```
## <a name="create-a-vm-from-managed-disk"></a>Tworzenie maszyny wirtualnej na podstawie dysku zarządzanego

Wykonaj następujące kroki, aby utworzyć maszynę wirtualną na podstawie dysku zarządzanego:

1. Ustaw niektóre parametry.

    ```powershell
    $NicRG = <NIC resource group>
    $NicName = <NIC name>
    $IPConfigName = <IP config name>
    $PrivateIP = <IP address> #Optional
    
    $VMRG = <VM resource group>
    $VMName = <VM name>
    $VMSize = <VM size> 
    ```

    >[!NOTE]
    > `PrivateIP`Parametr jest opcjonalny. Użyj tego parametru, aby przypisać statyczny adres IP, w przeciwnym razie wartość domyślna to dynamiczny adres IP przy użyciu protokołu DHCP.

    Oto przykładowe dane wyjściowe. W tym przykładzie ta sama Grupa zasobów została określona dla wszystkich zasobów maszyny wirtualnej, ale w razie potrzeby można utworzyć i określić osobne grupy zasobów dla zasobów.

    ```powershell
    PS C:\WINDOWS\system32> $NicRG = "myasevm1rg"
    PS C:\WINDOWS\system32> $NicName = "myasevmnic1"
    PS C:\WINDOWS\system32> $IPConfigName = "myaseipconfig1" 

    PS C:\WINDOWS\system32> $VMRG = "myasevm1rg"
    PS C:\WINDOWS\system32> $VMName = "myasetestvm1"
    PS C:\WINDOWS\system32> $VMSize = "Standard_D1_v2"   
    ```

1. Pobierz informacje o sieci wirtualnej i Utwórz nowy interfejs sieciowy.

    W tym przykładzie przyjęto założenie, że tworzysz pojedynczy interfejs sieciowy w domyślnej sieci wirtualnej `ASEVNET` skojarzonej z domyślną grupą zasobów `ASERG` . W razie konieczności można określić alternatywną sieć wirtualną lub utworzyć wiele interfejsów sieciowych. Aby uzyskać więcej informacji, zobacz [Dodawanie interfejsu sieciowego do maszyny wirtualnej za pośrednictwem Azure Portal](azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal.md).

    ```powershell
    $armVN = Get-AzureRMVirtualNetwork -Name ASEVNET -ResourceGroupName ASERG
    $ipConfig = New-AzureRmNetworkInterfaceIpConfig -Name $IPConfigName -SubnetId $armVN.Subnets[0].Id [-PrivateIpAddress $PrivateIP]
    $nic = New-AzureRmNetworkInterface -Name $NicName -ResourceGroupName $NicRG -Location DBELocal -IpConfiguration $ipConfig
    ```

    Oto przykładowe dane wyjściowe.

    ```powershell
    PS C:\WINDOWS\system32> $armVN = Get-AzureRMVirtualNetwork -Name ASEVNET -ResourceGroupName ASERG
    PS C:\WINDOWS\system32> $ipConfig = New-AzureRmNetworkInterfaceIpConfig -Name $IPConfigName -SubnetId $armVN.Subnets[0].Id
    PS C:\WINDOWS\system32> $nic = New-AzureRmNetworkInterface -Name $NicName -ResourceGroupName $NicRG -Location DBELocal -IpConfiguration $ipConfig
    WARNING: The output object type of this cmdlet will be modified in a future release.
    PS C:\WINDOWS\system32>    
    ```
1. Utwórz nowy obiekt konfiguracji maszyny wirtualnej.

    ```powershell
    $vmConfig = New-AzureRmVMConfig -VMName $VMName -VMSize $VMSize
    ```

    
1. Dodaj interfejs sieciowy do maszyny wirtualnej.

    ```powershell
    $vm = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $nic.Id
    ```

1. Ustaw właściwości dysku systemu operacyjnego na maszynie wirtualnej.

    ```powershell
    $vm = Set-AzureRmVMOSDisk -VM $vm -ManagedDiskId $disk.Id -StorageAccountType StandardLRS -CreateOption Attach –[Windows/Linux]
    ```
    Ostatnia flaga w tym poleceniu będzie albo `-Windows` `-Linux` w zależności od systemu operacyjnego używanego przez maszynę wirtualną.

1. Utwórz maszynę wirtualną.

    ```powershell
    New-AzureRmVM -ResourceGroupName $VMRG -Location DBELocal -VM $vm 
    ```

    Oto przykładowe dane wyjściowe. 

    ```powershell
    PS C:\WINDOWS\system32> $vmConfig = New-AzureRmVMConfig -VMName $VMName -VMSize $VMSize
    PS C:\WINDOWS\system32> $vm = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $nic.Id
    PS C:\WINDOWS\system32> $vm = Set-AzureRmVMOSDisk -VM $vm -ManagedDiskId $disk.Id -StorageAccountType StandardLRS -CreateOption Attach -Windows
    PS C:\WINDOWS\system32> New-AzureRmVM -ResourceGroupName $VMRG -Location DBELocal -VM $vm
    WARNING: Since the VM is created using premium storage or managed disk, existing standard storage account, myasevmsa, is used for
    boot diagnostics.    
    RequestId IsSuccessStatusCode StatusCode ReasonPhrase
    --------- ------------------- ---------- ------------
                             True         OK OK        
    PS C:\WINDOWS\system32>
    ```

## <a name="delete-vm-and-resources"></a>Usuwanie maszyny wirtualnej i zasobów

W tym artykule użyto tylko jednej grupy zasobów do utworzenia wszystkich zasobów maszyny wirtualnej. Usunięcie tej grupy zasobów spowoduje usunięcie maszyny wirtualnej i wszystkich skojarzonych zasobów. 

1. Najpierw Wyświetl wszystkie zasoby utworzone w ramach grupy zasobów.

    ```powershell
    Get-AzureRmResource -ResourceGroupName <Resource group name>
    ```
    Oto przykładowe dane wyjściowe.
    
    ```powershell
    PS C:\WINDOWS\system32> Get-AzureRmResource -ResourceGroupName myasevm1rg
    
    
    Name              : myasemd1
    ResourceGroupName : myasevm1rg
    ResourceType      : Microsoft.Compute/disks
    Location          : dbelocal
    ResourceId        : /subscriptions/992601bc-b03d-4d72-598e-d24eac232122/resourceGroups/myasevm1rg/providers/Microsoft.Compute/disk
                        s/myasemd1
    
    Name              : myasetestvm1
    ResourceGroupName : myasevm1rg
    ResourceType      : Microsoft.Compute/virtualMachines
    Location          : dbelocal
    ResourceId        : /subscriptions/992601bc-b03d-4d72-598e-d24eac232122/resourceGroups/myasevm1rg/providers/Microsoft.Compute/virt
                        ualMachines/myasetestvm1
    
    Name              : myasevmnic1
    ResourceGroupName : myasevm1rg
    ResourceType      : Microsoft.Network/networkInterfaces
    Location          : dbelocal
    ResourceId        : /subscriptions/992601bc-b03d-4d72-598e-d24eac232122/resourceGroups/myasevm1rg/providers/Microsoft.Network/netw
                        orkInterfaces/myasevmnic1
    
    Name              : myasevmsa
    ResourceGroupName : myasevm1rg
    ResourceType      : Microsoft.Storage/storageaccounts
    Location          : dbelocal
    ResourceId        : /subscriptions/992601bc-b03d-4d72-598e-d24eac232122/resourceGroups/myasevm1rg/providers/Microsoft.Storage/stor
                        ageaccounts/myasevmsa
    
    PS C:\WINDOWS\system32>
    ```

1. Usuń grupę zasobów i wszystkie skojarzone z nią zasoby.

    ```powershell
    Remove-AzureRmResourceGroup -ResourceGroupName <Resource group name>
    ```
    Oto przykładowe dane wyjściowe.
    
    ```powershell
    PS C:\WINDOWS\system32> Remove-AzureRmResourceGroup -ResourceGroupName myasevm1rg
    
    Confirm
    Are you sure you want to remove resource group 'myasevm1rg'
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y
    True
    PS C:\WINDOWS\system32>
    ```

1. Sprawdź, czy grupa zasobów została usunięta. Pobierz wszystkie grupy zasobów, które istnieją na urządzeniu. 

    ```powershell
    Get-AzureRmResourceGroup
    ```
    Oto przykładowe dane wyjściowe.

    ```powershell
    PS C:\WINDOWS\system32> Get-AzureRmResourceGroup

    ResourceGroupName : ase-image-resourcegroup
    Location          : dbelocal
    ProvisioningState : Succeeded
    Tags              :
    ResourceId        : /subscriptions/992601bc-b03d-4d72-598e-d24eac232122/resourceGroups/ase-image-resourcegroup
    
    ResourceGroupName : ASERG
    Location          : dbelocal
    ProvisioningState : Succeeded
    Tags              :
    ResourceId        : /subscriptions/992601bc-b03d-4d72-598e-d24eac232122/resourceGroups/ASERG
    
    ResourceGroupName : myaserg
    Location          : dbelocal
    ProvisioningState : Succeeded
    Tags              :
    ResourceId        : /subscriptions/992601bc-b03d-4d72-598e-d24eac232122/resourceGroups/myaserg
        
    PS C:\WINDOWS\system32>
    ```

## <a name="next-steps"></a>Następne kroki

W zależności od rodzaju wdrożenia można wybrać jedną z poniższych procedur.

- [Wdróż maszynę wirtualną na podstawie uogólnionego obrazu za pomocą Azure PowerShell](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md)  
- [Wdróż maszynę wirtualną za pomocą Azure Portal](azure-stack-edge-gpu-deploy-virtual-machine-portal.md)
