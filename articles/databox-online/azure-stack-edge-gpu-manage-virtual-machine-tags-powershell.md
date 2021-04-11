---
title: Zarządzaj tagami maszyn wirtualnych na urządzeniu z systemem Azure Stack Edge przy użyciu procesora GPU Pro Azure PowerShell
description: Opisuje sposób tworzenia tagów maszyn wirtualnych i zarządzania nimi dla maszyn wirtualnych działających w Azure Stack Edge przy użyciu Azure PowerShell.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 04/06/2021
ms.author: alkohli
ms.openlocfilehash: 566ca1598857f67e25faea9f19d134c4a91bfb18
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/07/2021
ms.locfileid: "106556262"
---
# <a name="manage-vm-tags-on-azure-stack-edge-via-azure-powershell"></a>Zarządzaj tagami maszyn wirtualnych na Azure Stack Edge za pośrednictwem Azure PowerShell

W tym artykule opisano sposób oznaczania maszyn wirtualnych uruchomionych na urządzeniach procesora GPU w systemie Azure Stack Edge przy użyciu Azure PowerShell.

## <a name="about-tags"></a>Informacje o tagach

Tagi to pary klucz-wartość zdefiniowane przez użytkownika, które można przypisać do zasobu lub grupy zasobów. Możesz zastosować Tagi do maszyn wirtualnych działających na urządzeniu, aby logicznie zorganizować je w taksonomię. Możesz umieścić znaczniki w zasobie w momencie tworzenia lub dodać je do istniejącego zasobu. Na przykład można zastosować nazwę `Organization` i wartość `Engineering` do wszystkich maszyn wirtualnych, które są używane przez dział inżynierii w organizacji.

Aby uzyskać więcej informacji na temat tagów, zobacz jak [zarządzać tagami za pośrednictwem programu AzureRM PowerShell](/powershell/module/azurerm.tags/?view=azurermps-6.13.0&preserve-view=true).

## <a name="prerequisites"></a>Wymagania wstępne

Aby można było wdrożyć maszynę wirtualną na urządzeniu za pośrednictwem programu PowerShell, należy się upewnić, że:

- Masz dostęp do klienta, który będzie używany do nawiązywania połączenia z urządzeniem.
    - Klient uruchamia [obsługiwany system operacyjny](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device).
    - Klient jest skonfigurowany do łączenia się z lokalną Azure Resource Manager urządzenia zgodnie z instrukcjami w temacie [Connect to Azure Resource Manager dla urządzenia](azure-stack-edge-gpu-connect-resource-manager.md).


## <a name="verify-connection-to-local-azure-resource-manager"></a>Sprawdź połączenie z Azure Resource Manager lokalnym

Upewnij się, że w celu uzyskania dostępu do urządzenia z poziomu klienta można użyć następujących kroków.

Sprawdź, czy klient może połączyć się z lokalną Azure Resource Manager. 

1. Wywołaj interfejsy API urządzeń lokalnych w celu uwierzytelnienia:

    ```powershell
    login-AzureRMAccount -EnvironmentName <Environment Name> -TenantId c0257de7-538f-415c-993a-1b87a031879d  
    ```

1. Podaj nazwę użytkownika `EdgeArmUser` i hasło, aby połączyć się za pośrednictwem Azure Resource Manager. Jeśli nie odwołujesz hasła, [zresetuj hasło dla Azure Resource Manager](azure-stack-edge-gpu-set-azure-resource-manager-password.md) i Użyj tego hasła do logowania.


## <a name="add-a-tag-to-a-vm"></a>Dodawanie tagu do maszyny wirtualnej

1. Ustaw niektóre parametry.

    ```powershell
    $VMName = <VM Name>
    $VMRG = <VM Resource Group>
    $TagName = <Tag Name>
    $TagValue = <Tag Value>   
    ```

    Oto przykładowe dane wyjściowe:

    ```powershell
    PS C:\WINDOWS\system32> $VMName = "myasetestvm1"
    PS C:\WINDOWS\system32> $VMRG = "myaserg2"
    PS C:\WINDOWS\system32> $TagName = "Organization"
    PS C:\WINDOWS\system32> $TagValue = "Sales"
    ```

2. Pobierz obiekt maszyny wirtualnej i jego Tagi.

   ```powershell
   $VirtualMachine = Get-AzureRmVM -ResourceGroupName $VMRG -Name $VMName
   $tags = $VirtualMachine.Tags
   ```

3. Dodaj znacznik i zaktualizuj maszynę wirtualną. Aktualizacja maszyny wirtualnej może potrwać kilka minut.

    Możesz użyć opcjonalnej flagi **-Force** , aby uruchomić polecenie bez potwierdzenia przez użytkownika.

    ```powershell
    $tags.Add($TagName, $TagValue)
    Set-AzureRmResource -ResourceId $VirtualMachine.Id -Tag $tags [-Force]
    ```

    Oto przykładowe dane wyjściowe:

    ```powershell
    PS C:\WINDOWS\system32> $VirtualMachine = Get-AzureRMVM -ResourceGroupName $VMRG -Name $VMName
    PS C:\WINDOWS\system32> $tags = $VirtualMachine.Tags
    PS C:\WINDOWS\system32> $tags.Add($TagName, $TagValue)
    PS C:\WINDOWS\system32> Set-AzureRmResource -ResourceID $VirtualMachine.ID -Tag $tags -Force
    
    Name              : myasetestvm1
    ResourceId        : /subscriptions/992601bc-b03d-4d72-598e-d24eac232122/resourceGroups/myaserg2/providers/Microsoft.Compute/virtua
                        lMachines/myasetestvm1
    ResourceName      : myasetestvm1
    ResourceType      : Microsoft.Compute/virtualMachines
    ResourceGroupName : myaserg2
    Location          : dbelocal
    SubscriptionId    : 992601bc-b03d-4d72-598e-d24eac232122
    Tags              : {Organization}
    Properties        : @{vmId=958c0baa-e143-4d8a-82bd-9c6b1ba45e86; hardwareProfile=; storageProfile=; osProfile=; networkProfile=;
                        provisioningState=Succeeded}
    
    PS C:\WINDOWS\system32>
    ```

Aby uzyskać więcej informacji, zobacz [Add-AzureRMTag](/powershell/module/azurerm.tags/remove-azurermtag?view=azurermps-6.13.0&preserve-view=true).

## <a name="view-tags-of-a-vm"></a>Wyświetlanie tagów maszyny wirtualnej

Możesz wyświetlić znaczniki zastosowane do określonej maszyny wirtualnej uruchomionej na urządzeniu. 

1. Zdefiniuj parametry skojarzone z maszyną wirtualną, której Tagi chcesz wyświetlić.

   ```powershell
   $VMName = <VM Name>
   $VMRG = <VM Resource Group>
   ```
    Oto przykładowe dane wyjściowe:

    ```powershell
    PS C:\WINDOWS\system32> $VMName = "myasetestvm1"
    PS C:\WINDOWS\system32> $VMRG = "myaserg2"
    PS C:\WINDOWS\system32> $TagName = "Organization"
    PS C:\WINDOWS\system32> $TagValue = "Sales"
    ```
1. Pobierz obiekt maszyny wirtualnej i Wyświetl jego Tagi.

   ```powershell
   $VirtualMachine = Get-AzureRmVM -ResourceGroupName $VMRG -Name $VMName
   $VirtualMachine.Tags
   ```
    Oto przykładowe dane wyjściowe:

    ```powershell
    PS C:\WINDOWS\system32> $VirtualMachine = Get-AzureRMVM -ResourceGroupName $VMRG -Name $VMName
    PS C:\WINDOWS\system32> $VirtualMachine

    ResourceGroupName : myaserg2
    Id                : /subscriptions/992601bc-b03d-4d72-598e-d24eac232122/resourceGroups/myaserg2/providers/Microsoft.Compute/virtua
    lMachines/myasetestvm1
    VmId              : 958c0baa-e143-4d8a-82bd-9c6b1ba45e86
    Name              : myasetestvm1
    Type              : Microsoft.Compute/virtualMachines
    Location          : dbelocal
    Tags              : {"Organization":"Sales"}
    HardwareProfile   : {VmSize}
    NetworkProfile    : {NetworkInterfaces}
    OSProfile         : {ComputerName, AdminUsername, LinuxConfiguration, Secrets}
    ProvisioningState : Succeeded
    StorageProfile    : {ImageReference, OsDisk, DataDisks}
    
    PS C:\WINDOWS\system32>
    ```
## <a name="view-tags-for-all-resources"></a>Wyświetl Tagi dla wszystkich zasobów

Aby wyświetlić bieżącą listę tagów dla wszystkich zasobów w lokalnej Azure Resource Manager subskrypcji (innej niż subskrypcja platformy Azure) Twojego urządzenia, użyj `Get-AzureRMTag` polecenia.


Oto przykładowe dane wyjściowe, gdy na urządzeniu jest uruchomionych wiele maszyn wirtualnych i chcesz wyświetlić wszystkie Tagi na wszystkich maszynach wirtualnych.

```powershell
PS C:\WINDOWS\system32> Get-AzureRMTag

Name         Count
----         -----
Organization 3

PS C:\WINDOWS\system32>
```

Powyższe dane wyjściowe wskazują, że `Organization` na maszynach wirtualnych uruchomionych na urządzeniu znajdują się trzy Tagi.

Aby wyświetlić więcej szczegółów, użyj `-Detailed` parametru.

```powershell
PS C:\WINDOWS\system32> Get-AzureRMTag -Detailed |fl

Name        : Organization
ValuesTable :
              Name         Count
              ===========  =====
              Engineering  2
              Sales        1

Count       : 3
Values      : {Engineering, Sales}

PS C:\WINDOWS\system32>
```

Powyższe dane wyjściowe wskazują, że z trzech tagów, 2 maszyny wirtualne są oznaczone jako `Engineering` należące do `Sales` .

## <a name="remove-a-tag-from-a-vm"></a>Usuwanie tagu z maszyny wirtualnej

1. Ustaw niektóre parametry. 

    ```powershell
    $VMName = <VM Name>
    $VMRG = <VM Resource Group>
    $TagName = <Tag Name>
   ``` 
    Oto przykładowe dane wyjściowe:

    ```powershell
    PS C:\WINDOWS\system32> $VMName = "myaselinuxvm1"
    PS C:\WINDOWS\system32> $VMRG = "myaserg1"
    PS C:\WINDOWS\system32> $TagName = "Organization" 
    ```
2. Pobierz obiekt maszyny wirtualnej.

    ```powershell
    $VirtualMachine = Get-AzureRmVM -ResourceGroupName $VMRG -Name $VMName
    $VirtualMachine   
    ```   

    Oto przykładowe dane wyjściowe:

    ```powershell
    PS C:\WINDOWS\system32> $VirtualMachine = Get-AzureRMVM -ResourceGroupName $VMRG -Name $VMName
    ResourceGroupName : myaserg1
    Id                : /subscriptions/992601bc-b03d-4d72-598e-d24eac232122/resourceGroups/myaserg1/providers/Microsoft.Compute/virtualMachines/myaselinuxvm1
    VmId              : 290b3fdd-0c99-4905-9ea1-cf93cd6f25ee
    Name              : myaselinuxvm1
    Type              : Microsoft.Compute/virtualMachines
    Location          : dbelocal
    Tags              : {"Organization":"Engineering"}
    HardwareProfile   : {VmSize}
    NetworkProfile    : {NetworkInterfaces}
    OSProfile         : {ComputerName, AdminUsername, LinuxConfiguration, Secrets}
    ProvisioningState : Succeeded
    StorageProfile    : {ImageReference, OsDisk, DataDisks}
                                                                              PS C:\WINDOWS\system32> 
    ```
3. Usuń tag i zaktualizuj maszynę wirtualną. Użyj opcjonalnej `-Force` flagi, aby uruchomić polecenie bez potwierdzenia przez użytkownika.

    ```powershell
    $tags = $VirtualMachine.Tags
    $tags.Remove($TagName)
    Set-AzureRmResource -ResourceId $VirtualMachine.Id -Tag $tags [-Force]
    ```
    Oto przykładowe dane wyjściowe:

    ```powershell
    PS C:\WINDOWS\system32> $tags = $Virtualmachine.Tags
    PS C:\WINDOWS\system32> $tags
    Key          Value
    ---          -----
    Organization Engineering
    PS C:\WINDOWS\system32> $tags.Remove($TagName)
    True
    PS C:\WINDOWS\system32> Set-AzureRMResource -ResourceID $VirtualMachine.ID -Tag $tags -Force
    Name              : myaselinuxvm1
    ResourceId        : /subscriptions/992601bc-b03d-4d72-598e-d24eac232122/resourceGrou
                        ps/myaserg1/providers/Microsoft.Compute/virtualMachines/myaselin
                        uxvm1
    ResourceName      : myaselinuxvm1
    ResourceType      : Microsoft.Compute/virtualMachines
    ResourceGroupName : myaserg1
    Location          : dbelocal
    SubscriptionId    : 992601bc-b03d-4d72-598e-d24eac232122
    Tags              : {}
    Properties        : @{vmId=290b3fdd-0c99-4905-9ea1-cf93cd6f25ee; hardwareProfile=;
                        storageProfile=; osProfile=; networkProfile=;
                        provisioningState=Succeeded}
    PS C:\WINDOWS\system32>
    ```


## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak [zarządzać tagami za pośrednictwem programu AzureRM PowerShell](/powershell/module/azurerm.tags/?view=azurermps-6.13.0&preserve-view=true).
