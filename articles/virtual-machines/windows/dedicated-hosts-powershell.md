---
title: Wdrażanie dedykowanych hostów platformy Azure przy użyciu Azure PowerShell
description: Wdróż maszyny wirtualne na dedykowanych hostach przy użyciu Azure PowerShell.
author: cynthn
ms.service: virtual-machines
ms.subservice: dedicated-hosts
ms.topic: how-to
ms.workload: infrastructure
ms.date: 11/12/2020
ms.author: cynthn
ms.reviewer: zivr
ms.openlocfilehash: ed6319d5374db56cfe85e7ef9413480e523d9a34
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102050889"
---
# <a name="deploy-vms-to-dedicated-hosts-using-the-azure-powershell"></a>Wdrażanie maszyn wirtualnych na dedykowanych hostach przy użyciu Azure PowerShell

W tym artykule opisano sposób tworzenia [dedykowanego hosta](../dedicated-hosts.md) platformy Azure do hostowania maszyn wirtualnych. 

Upewnij się, że zainstalowano program Azure PowerShell w wersji 2.8.0 lub nowszej oraz że zalogowano się na koncie platformy Azure w usłudze `Connect-AzAccount` . 

## <a name="limitations"></a>Ograniczenia

- Rozmiary i typy sprzętu dostępne dla dedykowanych hostów różnią się w zależności od regionu. Więcej informacji można znaleźć na [stronie cennika](https://aka.ms/ADHPricing) hosta.

## <a name="create-a-host-group"></a>Tworzenie grupy hostów

**Grupa hostów** jest zasobem, który reprezentuje kolekcję dedykowanych hostów. Można utworzyć grupę hostów w regionie i strefie dostępności, a następnie dodać do niej hosty. W przypadku planowania wysokiej dostępności dostępne są dodatkowe opcje. W przypadku dedykowanych hostów można użyć jednej lub obu następujących opcji: 
- Obejmuje wiele stref dostępności. W takim przypadku konieczne jest posiadanie grupy hostów w każdej ze stref, które mają być używane.
- Obejmuje wiele domen błędów, które są mapowane na stojaki fizyczne. 
 
W obu przypadkach należy podać liczbę domen błędów dla grupy hostów. Jeśli nie chcesz obejmować domen błędów w grupie, użyj liczby domen błędów wynoszącej 1. 

Możesz również zdecydować się na korzystanie ze stref dostępności i domen błędów. Ten przykład umożliwia utworzenie grupy hostów w strefie 1 z 2 domenami błędów. 


```azurepowershell-interactive
$rgName = "myDHResourceGroup"
$location = "EastUS"

New-AzResourceGroup -Location $location -Name $rgName
$hostGroup = New-AzHostGroup `
   -Location $location `
   -Name myHostGroup `
   -PlatformFaultDomain 2 `
   -ResourceGroupName $rgName `
   -Zone 1
```


Dodaj `-SupportAutomaticPlacement true` parametr, aby maszyny wirtualne i wystąpienia zestawu skalowania były automatycznie umieszczane na hostach w grupie hostów. Aby uzyskać więcej informacji, zobacz sekcję [ręczne i automatyczne umieszczanie ](../dedicated-hosts.md#manual-vs-automatic-placement).


## <a name="create-a-host"></a>Tworzenie hosta

Teraz Utwórzmy dedykowanego hosta w grupie hostów. Oprócz nazwy hosta, należy podać jednostkę SKU dla hosta. Jednostka SKU hosta przechwytuje obsługiwaną serię maszyn wirtualnych oraz generowanie sprzętu dla dedykowanego hosta.

Aby uzyskać więcej informacji o jednostkach SKU i cenach hosta, zobacz [Cennik dedykowanego hosta platformy Azure](https://aka.ms/ADHPricing).

Jeśli ustawisz liczbę domen błędów dla grupy hostów, zostanie wyświetlony monit o określenie domeny błędów dla hosta. W tym przykładzie ustawimy domenę błędów dla hosta na 1.


```azurepowershell-interactive
$dHost = New-AzHost `
   -HostGroupName $hostGroup.Name `
   -Location $location -Name myHost `
   -ResourceGroupName $rgName `
   -Sku DSv3-Type1 `
   -AutoReplaceOnFailure 1 `
   -PlatformFaultDomain 1
```

## <a name="create-a-vm"></a>Tworzenie maszyny wirtualnej

Utwórz maszynę wirtualną na dedykowanym hoście. 

Jeśli podczas tworzenia grupy hostów została określona strefa dostępności, należy użyć tej samej strefy podczas tworzenia maszyny wirtualnej. W tym przykładzie, ponieważ nasza grupa hostów znajduje się w strefie 1, musimy utworzyć maszynę wirtualną w strefie 1.  


```azurepowershell-interactive
$cred = Get-Credential
New-AzVM `
   -Credential $cred `
   -ResourceGroupName $rgName `
   -Location $location `
   -Name myVM `
   -HostId $dhost.Id `
   -Image Win2016Datacenter `
   -Zone 1 `
   -Size Standard_D4s_v3
```

> [!WARNING]
> Jeśli utworzono maszynę wirtualną na hoście, który nie ma wystarczającej ilości zasobów, maszyna wirtualna zostanie utworzona w stanie niepowodzenia. 

## <a name="check-the-status-of-the-host"></a>Sprawdź stan hosta

Można sprawdzić stan kondycji hosta oraz liczbę maszyn wirtualnych, które można wdrożyć na hoście przy użyciu [GetAzHost](/powershell/module/az.compute/get-azhost) z `-InstanceView` parametrem.

```azurepowershell-interactive
Get-AzHost `
   -ResourceGroupName $rgName `
   -Name myHost `
   -HostGroupName $hostGroup.Name `
   -InstanceView
```

Dane wyjściowe będą wyglądać podobnie do tego:

```
ResourceGroupName      : myDHResourceGroup
PlatformFaultDomain    : 1
AutoReplaceOnFailure   : True
HostId                 : 12345678-1234-1234-abcd-abc123456789
ProvisioningTime       : 7/28/2019 5:31:01 PM
ProvisioningState      : Succeeded
InstanceView           : 
  AssetId              : abc45678-abcd-1234-abcd-123456789abc
  AvailableCapacity    : 
    AllocatableVMs[0]  : 
      VmSize           : Standard_D2s_v3
      Count            : 32
    AllocatableVMs[1]  : 
      VmSize           : Standard_D4s_v3
      Count            : 16
    AllocatableVMs[2]  : 
      VmSize           : Standard_D8s_v3
      Count            : 8
    AllocatableVMs[3]  : 
      VmSize           : Standard_D16s_v3
      Count            : 4
    AllocatableVMs[4]  : 
      VmSize           : Standard_D32-8s_v3
      Count            : 2
    AllocatableVMs[5]  : 
      VmSize           : Standard_D32-16s_v3
      Count            : 2
    AllocatableVMs[6]  : 
      VmSize           : Standard_D32s_v3
      Count            : 2
    AllocatableVMs[7]  : 
      VmSize           : Standard_D64-16s_v3
      Count            : 1
    AllocatableVMs[8]  : 
      VmSize           : Standard_D64-32s_v3
      Count            : 1
    AllocatableVMs[9]  : 
      VmSize           : Standard_D64s_v3
      Count            : 1
  Statuses[0]          : 
    Code               : ProvisioningState/succeeded
    Level              : Info
    DisplayStatus      : Provisioning succeeded
    Time               : 7/28/2019 5:31:01 PM
  Statuses[1]          : 
    Code               : HealthState/available
    Level              : Info
    DisplayStatus      : Host available
Sku                    : 
  Name                 : DSv3-Type1
Id                     : /subscriptions/10101010-1010-1010-1010-101010101010/re
sourceGroups/myDHResourceGroup/providers/Microsoft.Compute/hostGroups/myHostGroup/hosts
/myHost
Name                   : myHost
Location               : eastus
Tags                   : {}
```

## <a name="create-a-scale-set"></a>Tworzenie zestawu skalowania 

Podczas wdrażania zestawu skalowania należy określić grupę hostów.

```azurepowershell-interactive
New-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -Location "EastUS" `
  -VMScaleSetName "myDHScaleSet" `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer" `
  -UpgradePolicyMode "Automatic"`
  -HostGroupId $hostGroup.Id
```

Jeśli chcesz ręcznie wybrać host, na którym ma zostać wdrożony zestaw skalowania, Dodaj `--host` i nazwę hosta.



## <a name="add-an-existing-vm"></a>Dodawanie istniejącej maszyny wirtualnej 

Możesz dodać istniejącą maszynę wirtualną do dedykowanego hosta, ale najpierw należy Stop\Deallocated. maszynę wirtualną Przed przeniesieniem maszyny wirtualnej do dedykowanego hosta upewnij się, że konfiguracja maszyny wirtualnej jest obsługiwana:

- Rozmiar maszyny wirtualnej musi znajdować się w tej samej rodzinie rozmiarów co dedykowany host. Na przykład jeśli dedykowany host to DSv3, rozmiar maszyny wirtualnej może być Standard_D4s_v3, ale nie Standard_A4_v2. 
- Maszyna wirtualna musi znajdować się w tym samym regionie co dedykowany host.
- Maszyna wirtualna nie może być częścią grupy umieszczania sąsiedztwa. Usuń maszynę wirtualną z grupy położenia zbliżeniowe przed przeniesieniem jej do dedykowanego hosta. Aby uzyskać więcej informacji, zobacz [Przenoszenie maszyny wirtualnej z grupy umieszczania sąsiedztwa](./proximity-placement-groups.md#move-an-existing-vm-out-of-a-proximity-placement-group)
- Maszyna wirtualna nie może znajdować się w zestawie dostępności.
- Jeśli maszyna wirtualna znajduje się w strefie dostępności, musi to być ta sama strefa dostępności co grupa hostów. Ustawienia strefy dostępności dla maszyny wirtualnej i grupy hostów muszą być zgodne.

Zastąp wartości zmiennych własnymi informacjami.

```azurepowershell-interactive
$vmRGName = "movetohost"
$vmName = "myVMtoHost"
$dhRGName = "myDHResourceGroup"
$dhGroupName = "myHostGroup"
$dhName = "myHost"

$myDH = Get-AzHost `
   -HostGroupName $dhGroupName `
   -ResourceGroupName $dhRGName `
   -Name $dhName
   
$myVM = Get-AzVM `
   -ResourceGroupName $vmRGName `
   -Name $vmName
   
$myVM.Host = New-Object Microsoft.Azure.Management.Compute.Models.SubResource

$myVM.Host.Id = "$myDH.Id"

Stop-AzVM `
   -ResourceGroupName $vmRGName `
   -Name $vmName -Force
   
Update-AzVM `
   -ResourceGroupName $vmRGName `
   -VM $myVM -Debug
   
Start-AzVM `
   -ResourceGroupName $vmRGName `
   -Name $vmName
```


## <a name="clean-up"></a>Czyszczenie

Opłaty są naliczane za dedykowane hosty nawet wtedy, gdy nie wdrożono żadnych maszyn wirtualnych. Należy usunąć wszystkie hosty, których obecnie nie używasz, aby zaoszczędzić koszty.  

Hosta można usunąć tylko wtedy, gdy nie ma dłużej używanych maszyn wirtualnych. Usuń maszyny wirtualne za pomocą polecenia [Remove-AzVM](/powershell/module/az.compute/remove-azvm).

```azurepowershell-interactive
Remove-AzVM -ResourceGroupName $rgName -Name myVM
```

Po usunięciu maszyn wirtualnych można usunąć hosta za pomocą polecenia [Remove-AzHost](/powershell/module/az.compute/remove-azhost).

```azurepowershell-interactive
Remove-AzHost -ResourceGroupName $rgName -Name myHost
```

Po usunięciu wszystkich hostów można usunąć grupę hostów za pomocą polecenia [Remove-AzHostGroup](/powershell/module/az.compute/remove-azhostgroup). 

```azurepowershell-interactive
Remove-AzHost -ResourceGroupName $rgName -Name myHost
```

Możesz również usunąć całą grupę zasobów w pojedynczym poleceniu za pomocą polecenia [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup). Spowoduje to usunięcie wszystkich zasobów utworzonych w grupie, w tym wszystkich maszyn wirtualnych, hostów i grup hostów.
 
```azurepowershell-interactive
Remove-AzResourceGroup -Name $rgName
```


## <a name="next-steps"></a>Następne kroki

- Istnieje przykładowy szablon, który znajduje się w [tym miejscu](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md), który używa stref i domen błędów w celu uzyskania maksymalnej odporności w regionie.

- Możesz również wdrożyć dedykowane hosty przy użyciu [Azure Portal](../dedicated-hosts-portal.md).
