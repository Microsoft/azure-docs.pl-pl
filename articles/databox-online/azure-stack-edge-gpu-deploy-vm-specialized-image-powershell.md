---
title: Tworzenie obrazów maszyn wirtualnych z wyspecjalizowanego obrazu wirtualnego dysku twardego systemu Windows dla Azure Stack Edge Pro GPU
description: Opisuje sposób tworzenia obrazów maszyn wirtualnych z wyspecjalizowanych obrazów, począwszy od dysku VHD systemu Windows lub dysku VHDX. Ten wyspecjalizowany obraz umożliwia tworzenie obrazów maszyn wirtualnych do użycia z maszynami wirtualnymi wdrożonymi na Azure Stack Edge Pro GPU.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 04/15/2021
ms.author: alkohli
ms.openlocfilehash: 6bfa42e99f295b429eba40a27eb59becb8aa80a1
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107575964"
---
# <a name="deploy-a-vm-from-a-specialized-image-on-your-azure-stack-edge-pro-gpu-device-via-azure-powershell"></a>Wdrażanie maszyny wirtualnej na podstawie wyspecjalizowanego obrazu na urządzeniu Azure Stack Edge Pro GPU za pośrednictwem Azure PowerShell 

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

W tym artykule opisano kroki wymagane do wdrożenia maszyny wirtualnej na urządzeniu Azure Stack Edge Pro GPU z wyspecjalizowanego obrazu. 

Aby przygotować uogólniony obraz do wdrażania maszyn wirtualnych w procesorze GPU w systemie Azure Stack Edge Pro, zobacz Przygotowanie [uogólnionych](azure-stack-edge-gpu-prepare-windows-vhd-generalized-image.md) obrazów z dysku VHD systemu Windows lub Przygotowanie uogólnionych obrazów z [obrazu ISO.](azure-stack-edge-gpu-prepare-windows-generalized-image-iso.md)

## <a name="about-vm-images"></a>Informacje o obrazach maszyn wirtualnych

Dysk VHD lub VHDX systemu Windows może służyć do tworzenia *wyspecjalizowanego* obrazu lub *uogólniony obraz.* W poniższej tabeli podsumowano kluczowe różnice między *wyspecjalizowanymi* i *uogólniaymi obrazami.*

[!INCLUDE [about-vm-images-for-azure-stack-edge](../../includes/azure-stack-edge-about-vm-images.md)]

## <a name="workflow"></a>Przepływ pracy

Przepływ pracy wysokiego poziomu do wdrażania maszyny wirtualnej z wyspecjalizowanego obrazu to:

1. Skopiuj dysk VHD na konto magazynu lokalnego na urządzeniu Azure Stack Edge Pro GPU.
1. Utwórz nowy dysk zarządzany z dysku VHD.
1. Utwórz nową maszynę wirtualną z dysku zarządzanego i dołącz dysk zarządzany.

## <a name="prerequisites"></a>Wymagania wstępne

Przed wdrożeniem maszyny wirtualnej na urządzeniu za pomocą programu PowerShell upewnij się, że:

- Masz dostęp do klienta, który będzie umożliwiał nawiązywanie połączenia z urządzeniem.
    - Na kliencie działa [obsługiwany system operacyjny.](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device)
    - Klient jest skonfigurowany do nawiązywania połączenia z lokalną Azure Resource Manager zgodnie z instrukcjami w tece Nawiązywanie połączenia [Azure Resource Manager z urządzeniem.](azure-stack-edge-gpu-connect-resource-manager.md)

## <a name="verify-the-local-azure-resource-manager-connection"></a>Weryfikowanie połączenia Azure Resource Manager lokalnego

Sprawdź, czy klient może nawiązać połączenie z lokalną Azure Resource Manager. 

1. Wywołaj interfejsy API urządzeń lokalnych w celu uwierzytelnienia:

    ```powershell
    Login-AzureRMAccount -EnvironmentName <Environment Name>
    ```

2. Podaj nazwę użytkownika `EdgeArmUser` i hasło do nawiązania połączenia za pośrednictwem Azure Resource Manager. Jeśli nie pamiętasz hasła, [zresetuj](azure-stack-edge-gpu-set-azure-resource-manager-password.md) hasło dla Azure Resource Manager i użyj tego hasła do zalogowania.

## <a name="deploy-vm-from-specialized-image"></a>Wdrażanie maszyny wirtualnej z wyspecjalizowanego obrazu

Poniższe sekcje zawierają instrukcje krok po kroku dotyczące wdrażania maszyny wirtualnej z wyspecjalizowanego obrazu.

## <a name="copy-vhd-to-local-storage-account-on-device"></a>Kopiowanie wirtualnego dysku twardego na konto magazynu lokalnego na urządzeniu

Wykonaj następujące kroki, aby skopiować wirtualny dysk twardy na konto magazynu lokalnego:

1. Skopiuj źródłowy wirtualny dysk twardy na lokalne konto magazynu obiektów blob na Azure Stack Edge.

1. Zanotuj wynikowy URI. Użyjesz tego URI w kolejnym kroku.

    Aby utworzyć konto magazynu lokalnego i [](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md#create-a-storage-account) uzyskać do [](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md#upload-a-vhd) niego dostęp, zobacz sekcje Tworzenie konta magazynu za pośrednictwem sekcji Przekazywanie wirtualnego dysku twardego w artykule: Wdrażanie maszyn wirtualnych na urządzeniu [Azure Stack Edge za](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md)pośrednictwem Azure PowerShell . 

## <a name="create-a-managed-disk-from-vhd"></a>Tworzenie dysku zarządzanego z wirtualnego dysku twardego

Wykonaj następujące kroki, aby utworzyć dysk zarządzany z wirtualnego dysku twardego przekazanego wcześniej do konta magazynu:

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

    Oto przykładowe dane wyjściowe. Lokalizacja w tym miejscu jest ustawiona na lokalizację lokalnego konta magazynu i jest zawsze dla wszystkich kont magazynu lokalnego na urządzeniu `DBELocal` Azure Stack Edge Pro GPU. 

    ```powershell
    PS C:\WINDOWS\system32> $DiskConfig = New-AzureRmDiskConfig -Location DBELocal -CreateOption Import -SourceUri $VHDURI
    PS C:\WINDOWS\system32> $disk = New-AzureRMDisk -ResourceGroupName $DiskRG -DiskName $DiskName -Disk $DiskConfig
    PS C:\WINDOWS\system32>    
    ```
## <a name="create-a-vm-from-managed-disk"></a>Tworzenie maszyny wirtualnej na dysku zarządzanym

Wykonaj następujące kroki, aby utworzyć maszynę wirtualną na dysku zarządzanym:

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
    > Parametr `PrivateIP` jest opcjonalny. Użyj tego parametru, aby przypisać statyczny adres IP. W innym przypadku wartością domyślną jest dynamiczny adres IP przy użyciu protokołu DHCP.

    Oto przykładowe dane wyjściowe. W tym przykładzie ta sama grupa zasobów jest określona dla wszystkich zasobów maszyny wirtualnej, ale w razie potrzeby można utworzyć i określić oddzielne grupy zasobów dla zasobów.

    ```powershell
    PS C:\WINDOWS\system32> $NicRG = "myasevm1rg"
    PS C:\WINDOWS\system32> $NicName = "myasevmnic1"
    PS C:\WINDOWS\system32> $IPConfigName = "myaseipconfig1" 

    PS C:\WINDOWS\system32> $VMRG = "myasevm1rg"
    PS C:\WINDOWS\system32> $VMName = "myasetestvm1"
    PS C:\WINDOWS\system32> $VMSize = "Standard_D1_v2"   
    ```

1. Uzyskaj informacje o sieci wirtualnej i utwórz nowy interfejs sieciowy.

    W tym przykładzie przyjęto założenie, że tworzysz pojedynczy interfejs sieciowy w domyślnej sieci wirtualnej, która jest skojarzona `ASEVNET` z domyślną grupą `ASERG` zasobów. W razie potrzeby można określić alternatywną sieć wirtualną lub utworzyć wiele interfejsów sieciowych. Aby uzyskać więcej informacji, zobacz [Dodawanie interfejsu sieciowego do maszyny wirtualnej za pośrednictwem Azure Portal](azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal.md).

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
    Ostatnią flagą w tym poleceniu będzie lub w zależności od tego, którego systemu operacyjnego `-Windows` `-Linux` używasz dla maszyny wirtualnej.

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

W tym artykule wykorzystano tylko jedną grupę zasobów do utworzenia całego zasobu maszyny wirtualnej. Usunięcie tej grupy zasobów spowoduje usunięcie maszyny wirtualnej i wszystkich skojarzonych zasobów. 

1. Najpierw wyświetl wszystkie zasoby utworzone w grupie zasobów.

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

1. Usuń grupę zasobów i wszystkie skojarzone zasoby.

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

- [Przygotowywanie uogólnionych obrazów z wirtualnego dysku twardego systemu Windows do wdrażania maszyn wirtualnych na Azure Stack Edge Pro GPU](azure-stack-edge-gpu-prepare-windows-vhd-generalized-image.md)
- [Przygotowywanie uogólnionych obrazów z obrazu ISO do wdrażania maszyn](azure-stack-edge-gpu-prepare-windows-generalized-image-iso.md) wirtualnych na Azure Stack Edge Pro GPU d