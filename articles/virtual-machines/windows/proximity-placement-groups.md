---
title: 'PowerShell: używanie grup umieszczania w sąsiedztwie'
description: Dowiedz się więcej na temat tworzenia i używania grup umieszczania w sąsiedztwie przy użyciu Azure PowerShell.
services: virtual-machines
ms.service: virtual-machines
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 01/27/2020
ms.author: cynthn
ms.reviewer: zivr
ms.openlocfilehash: 4de71be8c88264d2cfb513a7f0214515058b5185
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98878328"
---
# <a name="deploy-vms-to-proximity-placement-groups-using-powershell"></a>Wdrażanie maszyn wirtualnych w grupach umieszczania sąsiedztwa przy użyciu programu PowerShell


Aby zapewnić, że maszyny wirtualne będą możliwie jak najbliżej, osiągając najniższe możliwe opóźnienie, należy wdrożyć je w obrębie [grupy umieszczania sąsiedztwa](../co-location.md#proximity-placement-groups).

Grupa umieszczania bliskości jest grupą logiczną używaną w celu upewnienia się, że zasoby obliczeniowe platformy Azure znajdują się fizycznie blisko siebie. Grupy umieszczania zbliżeniowe są przydatne w przypadku obciążeń, w których jest wymagane małe opóźnienia.


## <a name="create-a-proximity-placement-group"></a>Tworzenie grupy umieszczania w pobliżu
Utwórz grupę umieszczania sąsiedztwa przy użyciu polecenia cmdlet [New-AzProximityPlacementGroup](/powershell/module/az.compute/new-azproximityplacementgroup) . 

```azurepowershell-interactive
$resourceGroup = "myPPGResourceGroup"
$location = "East US"
$ppgName = "myPPG"
New-AzResourceGroup -Name $resourceGroup -Location $location
$ppg = New-AzProximityPlacementGroup `
   -Location $location `
   -Name $ppgName `
   -ResourceGroupName $resourceGroup `
   -ProximityPlacementGroupType Standard
```

## <a name="list-proximity-placement-groups"></a>Wyświetl listę grup umieszczania zbliżeniowe

Wszystkie grupy położenia zbliżeniowe można wyświetlić za pomocą polecenia cmdlet [Get-AzProximityPlacementGroup](/powershell/module/az.compute/get-azproximityplacementgroup) .

```azurepowershell-interactive
Get-AzProximityPlacementGroup
```


## <a name="create-a-vm"></a>Tworzenie maszyny wirtualnej

Utwórz maszynę wirtualną w grupie położenia sąsiedztwa przy użyciu polecenia, `-ProximityPlacementGroup $ppg.Id` Aby odwołać się do identyfikatora grupy umieszczania bliskości podczas tworzenia maszyny wirtualnej przy użyciu polecenia [New-AzVM](/powershell/module/az.compute/new-azvm) .

```azurepowershell-interactive
$vmName = "myVM"

New-AzVm `
  -ResourceGroupName $resourceGroup `
  -Name $vmName `
  -Location $location `
  -OpenPorts 3389 `
  -ProximityPlacementGroup $ppg.Id
```

Maszynę wirtualną można zobaczyć w grupie umieszczania za pomocą polecenia [Get-AzProximityPlacementGroup](/powershell/module/az.compute/get-azproximityplacementgroup).

```azurepowershell-interactive
Get-AzProximityPlacementGroup -ResourceId $ppg.Id |
    Format-Table -Property VirtualMachines -Wrap
```

### <a name="move-an-existing-vm-into-a-proximity-placement-group"></a>Przenoszenie istniejącej maszyny wirtualnej do grupy umieszczania zbliżeniowego

Istnieje również możliwość dodania istniejącej maszyny wirtualnej do grupy umieszczania sąsiedztwa. Musisz najpierw stop\deallocate maszynę wirtualną, a następnie zaktualizować maszynę wirtualną i ponownie ją uruchomić.

```azurepowershell-interactive
$ppg = Get-AzProximityPlacementGroup -ResourceGroupName myPPGResourceGroup -Name myPPG
$vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM
Stop-AzVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName
Update-AzVM -VM $vm -ResourceGroupName $vm.ResourceGroupName -ProximityPlacementGroupId $ppg.Id
Start-AzVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName
```

### <a name="move-an-existing-vm-out-of-a-proximity-placement-group"></a>Przenoszenie istniejącej maszyny wirtualnej z grupy umieszczania sąsiedztwa

Aby usunąć maszynę wirtualną z grupy umieszczania sąsiedztwa, musisz najpierw stop\deallocate maszynę wirtualną, a następnie zaktualizować maszynę wirtualną i ponownie ją uruchomić.

```azurepowershell-interactive
$ppg = Get-AzProximityPlacementGroup -ResourceGroupName myPPGResourceGroup -Name myPPG
$vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM
Stop-AzVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName
$vm.ProximityPlacementGroup = ""
Update-AzVM -VM $vm -ResourceGroupName $vm.ResourceGroupName 
Start-AzVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName
```


## <a name="availability-sets"></a>Zestawy dostępności
Zestaw dostępności można również utworzyć w grupie umieszczania sąsiedztwa. Użyj tego samego `-ProximityPlacementGroup` parametru z poleceniem cmdlet [New-AzAvailabilitySet](/powershell/module/az.compute/new-azavailabilityset) , aby utworzyć zestaw dostępności, a wszystkie maszyny wirtualne utworzone w zestawie dostępności również zostaną utworzone w tej samej grupie umieszczania sąsiedztwa.

Aby dodać lub usunąć istniejący zestaw dostępności do grupy umieszczania w sąsiedztwie, należy najpierw zatrzymać wszystkie maszyny wirtualne w zestawie dostępności. 

### <a name="move-an-existing-availability-set-into-a-proximity-placement-group"></a>Przenoszenie istniejącego zestawu dostępności do grupy umieszczania w sąsiedztwie

```azurepowershell-interactive
$resourceGroup = "myResourceGroup"
$avSetName = "myAvailabilitySet"
$avSet = Get-AzAvailabilitySet -ResourceGroupName $resourceGroup -Name $avSetName
$vmIds = $avSet.VirtualMachinesReferences
foreach ($vmId in $vmIDs){
    $string = $vmID.Id.Split("/")
    $vmName = $string[8]
    Stop-AzVM -ResourceGroupName $resourceGroup -Name $vmName -Force
    } 

$ppg = Get-AzProximityPlacementGroup -ResourceGroupName myPPG -Name myPPG
Update-AzAvailabilitySet -AvailabilitySet $avSet -ProximityPlacementGroupId $ppg.Id
foreach ($vmId in $vmIDs){
    $string = $vmID.Id.Split("/")
    $vmName = $string[8]
    Start-AzVM -ResourceGroupName $resourceGroup -Name $vmName 
    } 
```

### <a name="move-an-existing-availability-set-out-of-a-proximity-placement-group"></a>Przenoszenie istniejącego zestawu dostępności z grupy umieszczania sąsiedztwa

```azurepowershell-interactive
$resourceGroup = "myResourceGroup"
$avSetName = "myAvailabilitySet"
$avSet = Get-AzAvailabilitySet -ResourceGroupName $resourceGroup -Name $avSetName
$vmIds = $avSet.VirtualMachinesReferences
foreach ($vmId in $vmIDs){
    $string = $vmID.Id.Split("/")
    $vmName = $string[8]
    Stop-AzVM -ResourceGroupName $resourceGroup -Name $vmName -Force
    } 

$avSet.ProximityPlacementGroup = ""
Update-AzAvailabilitySet -AvailabilitySet $avSet 
foreach ($vmId in $vmIDs){
    $string = $vmID.Id.Split("/")
    $vmName = $string[8]
    Start-AzVM -ResourceGroupName $resourceGroup -Name $vmName 
    } 
```

## <a name="scale-sets"></a>Zestawy skalowania

Zestaw skalowania można również utworzyć w grupie umieszczania sąsiedztwa. Użyj tego samego `-ProximityPlacementGroup` parametru z parametrem [New-AzVmss](/powershell/module/az.compute/new-azvmss) , aby utworzyć zestaw skalowania, a wszystkie wystąpienia zostaną utworzone w tej samej grupie umieszczania sąsiedztwa.


Aby dodać lub usunąć istniejący zestaw skalowania do grupy położenia zbliżeniowe, należy najpierw zatrzymać zestaw skalowania. 

### <a name="move-an-existing-scale-set-into-a-proximity-placement-group"></a>Przenoszenie istniejącego zestawu skalowania do grupy umieszczania zbliżeniowego

```azurepowershell-interactive
$ppg = Get-AzProximityPlacementGroup -ResourceGroupName myPPG -Name myPPG
$vmss = Get-AzVmss -ResourceGroupName myVMSSResourceGroup -VMScaleSetName myScaleSet
Stop-AzVmss -VMScaleSetName $vmss.Name -ResourceGroupName $vmss.ResourceGroupName
Update-AzVmss -VMScaleSetName $vmss.Name -ResourceGroupName $vmss.ResourceGroupName -ProximityPlacementGroupId $ppg.Id
Start-AzVmss -VMScaleSetName $vmss.Name -ResourceGroupName $vmss.ResourceGroupName
```

### <a name="move-an-existing-scale-set-out-of-a-proximity-placement-group"></a>Przenoszenie istniejącego zestawu skalowania z grupy umieszczania sąsiedztwa

```azurepowershell-interactive
$vmss = Get-AzVmss -ResourceGroupName myVMSSResourceGroup -VMScaleSetName myScaleSet
Stop-AzVmss -VMScaleSetName $vmss.Name -ResourceGroupName $vmss.ResourceGroupName
$vmss.ProximityPlacementGroup = ""
Update-AzVmss -VirtualMachineScaleSet $vmss -VMScaleSetName $vmss.Name -ResourceGroupName $vmss.ResourceGroupName  
Start-AzVmss -VMScaleSetName $vmss.Name -ResourceGroupName $vmss.ResourceGroupName
```

## <a name="next-steps"></a>Następne kroki

Za pomocą [interfejsu wiersza polecenia platformy Azure](../linux/proximity-placement-groups.md) można także tworzyć grupy umieszczania sąsiedztwa.