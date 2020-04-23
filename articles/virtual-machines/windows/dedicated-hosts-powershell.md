---
title: Wdrażanie dedykowanych hostów platformy Azure przy użyciu programu Azure PowerShell
description: Wdrażanie maszyn wirtualnych do dedykowanych hostów przy użyciu programu Azure PowerShell.
author: cynthn
ms.service: virtual-machines-windows
ms.topic: article
ms.workload: infrastructure
ms.date: 08/01/2019
ms.author: cynthn
ms.reviewer: zivr
ms.openlocfilehash: b90189c6ba5e51a24d0c248b5aa08e9a5e4bbd9b
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2020
ms.locfileid: "82082853"
---
# <a name="deploy-vms-to-dedicated-hosts-using-the-azure-powershell"></a>Wdrażanie maszyn wirtualnych na dedykowanych hostach przy użyciu programu Azure PowerShell

W tym artykule opisano, jak utworzyć [dedykowany host](dedicated-hosts.md) platformy Azure do obsługi maszyn wirtualnych (maszyn wirtualnych). 

Upewnij się, że zainstalowano usługę Azure PowerShell w wersji 2.8.0 lub `Connect-AzAccount`nowszej, a użytkownik jest zalogowany do konta platformy Azure za pomocą programu . 

## <a name="limitations"></a>Ograniczenia

- Zestawy skalowania maszyny wirtualnej nie są obecnie obsługiwane na dedykowanych hostach.
- Rozmiary i typy sprzętu dostępne dla dedykowanych hostów różnią się w zależności od regionu. Więcej informacji można znaleźć na [stronie z cennikiem hosta.](https://aka.ms/ADHPricing)

## <a name="create-a-host-group"></a>Tworzenie grupy hostów

**Grupa hostów** jest zasobem reprezentującym kolekcję dedykowanych hostów. Tworzysz grupę hostów w regionie i strefie dostępności i dodajesz do niej hosty. Podczas planowania wysokiej dostępności, istnieją dodatkowe opcje. Z dedykowanymi hostami można użyć jednej lub obu następujących opcji: 
- Obejmuje wiele stref dostępności. W takim przypadku musisz mieć grupę hostów w każdej ze stref, których chcesz użyć.
- Obejmują wiele domen błędów, które są mapowane do stojaków fizycznych. 
 
W obu przypadkach należy podać liczbę domen błędów dla grupy hosta. Jeśli nie chcesz obejmować domen błędów w grupie, użyj liczby domen błędów 1. 

Można również zdecydować się na użycie zarówno stref dostępności, jak i domen błędów. W tym przykładzie tworzy grupę hostów w strefie 1, z 2 domen błędów. 


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

## <a name="create-a-host"></a>Tworzenie hosta

Teraz utwórzmy dedykowanego hosta w grupie hostów. Oprócz nazwy hosta, należy podać jednostkę SKU dla hosta. Jednostka SKU hosta przechwytuje obsługiwaną serię maszyn wirtualnych, a także generowanie sprzętu dla dedykowanego hosta.

Aby uzyskać więcej informacji na temat jednostek SKU hosta i cen, zobacz [Cennik hosta dedykowanego platformy Azure](https://aka.ms/ADHPricing).

Jeśli ustawisz liczbę domen błędów dla grupy hostów, zostaniesz poproszony o określenie domeny błędów dla hosta. W tym przykładzie ustawiamy domenę błędów dla hosta na 1.


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

Jeśli podczas tworzenia grupy hosta określono strefę dostępności, podczas tworzenia maszyny wirtualnej jest wymagana użycia tej samej strefy. W tym przykładzie, ponieważ nasza grupa hostów znajduje się w strefie 1, musimy utworzyć maszynę wirtualną w strefie 1.  


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
> Jeśli utworzysz maszynę wirtualną na hoście, który nie ma wystarczającej ilości zasobów, maszyna wirtualna zostanie utworzona w stanie FAILED. 

## <a name="check-the-status-of-the-host"></a>Sprawdź stan hosta

Można sprawdzić stan kondycji hosta i ile maszyn wirtualnych można nadal wdrożyć `-InstanceView` na hoście przy użyciu [GetAzHost](/powershell/module/az.compute/get-azhost) z parametrem.

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

## <a name="add-an-existing-vm"></a>Dodawanie istniejącej maszyny wirtualnej 

Do dedykowanego hosta można dodać istniejącą maszynę wirtualną, ale najpierw maszyna wirtualna musi być zatrzymana\przydziałem. Przed przeniesieniem maszyny Wirtualnej na dedykowany host upewnij się, że konfiguracja maszyny Wirtualnej jest obsługiwana:

- Rozmiar maszyny Wirtualnej musi znajdować się w tej samej rodzinie rozmiarów co dedykowany host. Na przykład jeśli dedykowany host jest DSv3, rozmiar maszyny Wirtualnej może być Standard_D4s_v3, ale nie może być Standard_A4_v2. 
- Maszyna wirtualna musi znajdować się w tym samym regionie co dedykowany host.
- Maszyna wirtualna nie może być częścią grupy miejsc docelowych zbliżeniowych. Usuń maszynę wirtualną z grupy miejsc docelowych zbliżeniowych przed przeniesieniem jej do dedykowanego hosta. Aby uzyskać więcej informacji, zobacz [Przenoszenie maszyny Wirtualnej z grupy miejsc docelowych zbliżeniowych](https://docs.microsoft.com/azure/virtual-machines/windows/proximity-placement-groups#move-an-existing-vm-out-of-a-proximity-placement-group)
- Maszyna wirtualna nie może być w zestawie dostępności.
- Jeśli maszyna wirtualna znajduje się w strefie dostępności, musi być tą samą strefą dostępności co grupa hosta. Ustawienia strefy dostępności dla maszyny Wirtualnej i grupy hostów muszą być zgodne.

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

Opłaty są naliczane za dedykowane hosty, nawet jeśli nie są wdrażane żadne maszyny wirtualne. Należy usunąć wszystkie hosty, których obecnie nie używasz, aby zaoszczędzić koszty.  

Hosta można usunąć tylko wtedy, gdy nie ma już maszyn wirtualnych korzystających z niego. Usuń maszyny wirtualne za pomocą [remove-AzVM](/powershell/module/az.compute/remove-azvm).

```azurepowershell-interactive
Remove-AzVM -ResourceGroupName $rgName -Name myVM
```

Po usunięciu maszyn wirtualnych można usunąć hosta za pomocą [funkcji Usuń-AzHost](/powershell/module/az.compute/remove-azhost).

```azurepowershell-interactive
Remove-AzHost -ResourceGroupName $rgName -Name myHost
```

Po usunięciu wszystkich hostów można usunąć grupę hostów za pomocą [programu Remove-AzHostGroup](/powershell/module/az.compute/remove-azhostgroup). 

```azurepowershell-interactive
Remove-AzHost -ResourceGroupName $rgName -Name myHost
```

Można również usunąć całą grupę zasobów w jednym poleceniu za pomocą [polecenia Usuń AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup). Spowoduje to usunięcie wszystkich zasobów utworzonych w grupie, w tym wszystkich maszyn wirtualnych, hostów i grup hostów.
 
```azurepowershell-interactive
Remove-AzResourceGroup -Name $rgName
```


## <a name="next-steps"></a>Następne kroki

- Istnieje przykładowy szablon, znaleziono [tutaj,](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md)który używa zarówno stref i domen błędów dla maksymalnej odporności w regionie.

- Dedykowane hosty można również wdrażać za pomocą [portalu Azure.](dedicated-hosts-portal.md)
