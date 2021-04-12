---
title: Pobieranie powiadomień dotyczących konserwacji maszyn wirtualnych platformy Azure przy użyciu programu PowerShell
description: Wyświetl powiadomienia dotyczące konserwacji maszyn wirtualnych działających na platformie Azure i uruchom konserwację samoobsługową przy użyciu programu PowerShell.
author: shants123
ms.service: virtual-machines
ms.subservice: maintenance-control
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 11/19/2019
ms.author: shants
ms.openlocfilehash: 4278aa563d76b783e02e67ee964764864931fd68
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102552375"
---
# <a name="handling-planned-maintenance-using-powershell"></a>Obsługa planowanej konserwacji przy użyciu programu PowerShell

**Ten artykuł ma zastosowanie do maszyn wirtualnych z systemami Linux i Windows.**

Możesz użyć Azure PowerShell, aby sprawdzić, kiedy maszyny wirtualne są zaplanowane do [konserwacji](maintenance-notifications.md). Informacje o planowanej konserwacji są dostępne w poleceniu cmdlet [Get-AzVM](/powershell/module/az.compute/get-azvm) podczas korzystania z `-status` parametru.
  
Informacje o konserwacji są zwracane tylko wtedy, gdy jest planowana konserwacja. Jeśli żadna konserwacja nie zostanie zaplanowana na maszynę wirtualną, polecenie cmdlet nie zwróci żadnych informacji o konserwacji. 


```powershell
Get-AzVM -ResourceGroupName myResourceGroup -Name myVM -Status
```

Dane wyjściowe

```
MaintenanceRedeployStatus               : 
  IsCustomerInitiatedMaintenanceAllowed : True
  PreMaintenanceWindowStartTime         : 5/14/2018 12:30:00 PM
  PreMaintenanceWindowEndTime           : 5/19/2018 12:30:00 PM
  MaintenanceWindowStartTime            : 5/21/2018 4:30:00 PM
  MaintenanceWindowEndTime              : 6/4/2018 4:30
  LastOperationResultCode               : None 
```

Następujące właściwości są zwracane w obszarze MaintenanceRedeployStatus: 

| Wartość | Opis   |
|-------|---------------|
| IsCustomerInitiatedMaintenanceAllowed | Wskazuje, czy w tej chwili można rozpocząć konserwację maszyny wirtualnej |
| PreMaintenanceWindowStartTime         | Początek okna samoobsługowego konserwacji, gdy można zainicjować konserwację na maszynie wirtualnej |
| PreMaintenanceWindowEndTime           | Koniec okna samoobsługowego konserwacji, gdy można zainicjować konserwację na maszynie wirtualnej |
| MaintenanceWindowStartTime            | Rozpoczęcie konserwacji zaplanowanej, w której platforma Azure inicjuje konserwację na maszynie wirtualnej |
| MaintenanceWindowEndTime              | Koniec okna zaplanowanej konserwacji, w którym platforma Azure inicjuje konserwację na maszynie wirtualnej |
| LastOperationResultCode               | Wynik ostatniej próby zainicjowania konserwacji na maszynie wirtualnej |



Możesz również uzyskać stan konserwacji dla wszystkich maszyn wirtualnych w grupie zasobów przy użyciu polecenia [Get-AzVM](/powershell/module/az.compute/get-azvm) i nie OKREŚLAJ maszyny wirtualnej.
 
```powershell
Get-AzVM -ResourceGroupName myResourceGroup -Status
```

Poniższy przykład programu PowerShell przyjmuje Identyfikator subskrypcji i zwraca listę maszyn wirtualnych, które są zaplanowane do konserwacji.

```powershell

function MaintenanceIterator
{
    Select-AzSubscription -SubscriptionId $args[0]

    $rgList= Get-AzResourceGroup 

    for ($rgIdx=0; $rgIdx -lt $rgList.Length ; $rgIdx++)
    {
        $rg = $rgList[$rgIdx]        
    $vmList = Get-AzVM -ResourceGroupName $rg.ResourceGroupName 
        for ($vmIdx=0; $vmIdx -lt $vmList.Length ; $vmIdx++)
        {
            $vm = $vmList[$vmIdx]
            $vmDetails = Get-AzVM -ResourceGroupName $rg.ResourceGroupName -Name $vm.Name -Status
              if ($vmDetails.MaintenanceRedeployStatus )
            {
                Write-Output "VM: $($vmDetails.Name)  IsCustomerInitiatedMaintenanceAllowed: $($vmDetails.MaintenanceRedeployStatus.IsCustomerInitiatedMaintenanceAllowed) $($vmDetails.MaintenanceRedeployStatus.LastOperationMessage)"               
            }
          }
    }
}

```

### <a name="start-maintenance-on-your-vm-using-powershell"></a>Rozpocznij konserwację na maszynie wirtualnej przy użyciu programu PowerShell

Przy użyciu informacji z funkcji w poprzedniej sekcji, następujące polecenie uruchamia konserwację na maszynie wirtualnej, jeśli **IsCustomerInitiatedMaintenanceAllowed** jest ustawiona na wartość true.

```powershell
Restart-AzVM -PerformMaintenance -name $vm.Name -ResourceGroupName $rg.ResourceGroupName 
```

## <a name="classic-deployments"></a>Klasyczne wdrożenia

[!INCLUDE [classic-vm-deprecation](../../includes/classic-vm-deprecation.md)]

Jeśli nadal masz starsze maszyny wirtualne wdrożone przy użyciu klasycznego modelu wdrażania, możesz użyć programu PowerShell do wykonywania zapytań dotyczących maszyn wirtualnych i inicjowania obsługi.

Aby uzyskać stan konserwacji maszyny wirtualnej, wpisz:

```
Get-AzureVM -ServiceName <Service name> -Name <VM name>
```

Aby rozpocząć konserwację klasycznej maszyny wirtualnej, wpisz:

```
Restart-AzureVM -InitiateMaintenance -ServiceName <service name> -Name <VM name>
```

## <a name="next-steps"></a>Następne kroki

Możesz również obsługiwać zaplanowaną konserwację przy użyciu [interfejsu wiersza polecenia platformy Azure](maintenance-notifications-cli.md) lub [portalu](maintenance-notifications-portal.md).
