---
title: Wdrażanie maszyn wirtualnych na platformie Azure za pomocą programu PowerShell
description: Dowiedz się, w jaki sposób używać Azure PowerShell do wdrażania maszyn wirtualnych w celu oszczędzania kosztów.
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 06/26/2020
ms.author: cynthn
ms.reviewer: jagaveer
ms.openlocfilehash: 0ca3c99aed8160161c125a89da3cb176c6e745f6
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98202066"
---
# <a name="deploy-spot-vms-using-azure-powershell"></a>Wdrażanie maszyn wirtualnych przy użyciu Azure PowerShell


Korzystanie z [maszyn wirtualnych na miejscu](../spot-vms.md) pozwala korzystać z nieużywanej pojemności przy znaczącym obciążeniu kosztów. W dowolnym momencie, gdy platforma Azure wymaga przywrócenia pojemności, infrastruktura platformy Azure wyłączy maszyny wirtualne. W związku z tym maszyny wirtualne są doskonałe dla obciążeń, które mogą obsłużyć przerwy, takie jak zadania przetwarzania wsadowego, środowiska deweloperskie/testowe, duże obciążenia obliczeniowe i inne.

Ceny maszyn wirtualnych na miejscu są zmienne, na podstawie regionu i jednostki SKU. Aby uzyskać więcej informacji, zobacz cennik maszyn wirtualnych dla [systemów](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) i Windows. Aby uzyskać więcej informacji na temat ustawiania ceny maksymalnej, zobacz [punkt maszyny wirtualne — Cennik](../spot-vms.md#pricing).

Dla maszyny wirtualnej można ustawić maksymalną cenę, która ma być płacona za godzinę. Maksymalna cena maszyny wirtualnej na miejscu może być ustawiona w dolarach amerykańskich (USD) przy użyciu maksymalnie 5 miejsc dziesiętnych. Na przykład wartość będzie `0.98765` Cena maksymalna $0,98765 USD za godzinę. Jeśli ustawisz maksymalną cenę `-1` , maszyna wirtualna nie zostanie wykluczona na podstawie ceny. Cena maszyny wirtualnej to aktualna cena za ilość miejsca lub cena standardowej maszyny wirtualnej, która kiedykolwiek jest mniejsza, o ile jest dostępna pojemność i przydział.


## <a name="create-the-vm"></a>Tworzenie maszyny wirtualnej

Utwórz element spotVM przy użyciu polecenia [New-AzVmConfig](/powershell/module/az.compute/new-azvmconfig) , aby utworzyć konfigurację. Dołącz `-Priority Spot` i ustaw `-MaxPrice` jako:
- `-1` Dlatego, że maszyna wirtualna nie zostanie wykluczona na podstawie ceny.
- kwota dolara (do 5 cyfr). Na przykład `-MaxPrice .98765` oznacza, że zostanie cofnięta alokacja maszyny wirtualnej, gdy cena za spotVM wyniesie około USD. 98765 na godzinę.


Ten przykład tworzy spotVM, które nie zostaną cofnięte w oparciu o Cennik (tylko wtedy, gdy platforma Azure wymaga powrotu do tyłu pojemności). Zasada wykluczenia jest ustawiona na cofnięcie alokacji maszyny wirtualnej, dzięki czemu można ją uruchomić ponownie w późniejszym czasie. Jeśli chcesz usunąć maszynę wirtualną i dysk źródłowy podczas wykluczania maszyny wirtualnej, ustaw na wartość `-EvictionPolicy` `Delete` w `New-AzVMConfig` .


```azurepowershell-interactive
$resourceGroup = "mySpotRG"
$location = "eastus"
$vmName = "mySpotVM"
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."
New-AzResourceGroup -Name $resourceGroup -Location $location
$subnetConfig = New-AzVirtualNetworkSubnetConfig `
   -Name mySubnet -AddressPrefix 192.168.1.0/24
$vnet = New-AzVirtualNetwork -ResourceGroupName $resourceGroup `
   -Location $location -Name MYvNET -AddressPrefix 192.168.0.0/16 `
   -Subnet $subnetConfig
$pip = New-AzPublicIpAddress -ResourceGroupName $resourceGroup -Location $location `
  -Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4
$nsgRuleRDP = New-AzNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleRDP  -Protocol Tcp `
  -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 3389 -Access Allow
$nsg = New-AzNetworkSecurityGroup -ResourceGroupName $resourceGroup -Location $location `
  -Name myNetworkSecurityGroup -SecurityRules $nsgRuleRDP
$nic = New-AzNetworkInterface -Name myNic -ResourceGroupName $resourceGroup -Location $location `
  -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id

# Create a virtual machine configuration and set this to be a Spot VM

$vmConfig = New-AzVMConfig -VMName $vmName -VMSize Standard_D1 -Priority "Spot" -MaxPrice -1 -EvictionPolicy Deallocate | `
Set-AzVMOperatingSystem -Windows -ComputerName $vmName -Credential $cred | `
Set-AzVMSourceImage -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2016-Datacenter -Version latest | `
Add-AzVMNetworkInterface -Id $nic.Id

New-AzVM -ResourceGroupName $resourceGroup -Location $location -VM $vmConfig
```

Po utworzeniu maszyny wirtualnej można wykonać zapytanie, aby zobaczyć maksymalną cenę dla wszystkich maszyn wirtualnych w grupie zasobów.

```azurepowershell-interactive
Get-AzVM -ResourceGroupName $resourceGroup | `
   Select-Object Name,@{Name="maxPrice"; Expression={$_.BillingProfile.MaxPrice}}
```

## <a name="simulate-an-eviction"></a>Symulowanie wykluczenia

Możesz [symulować wykluczenie](/rest/api/compute/virtualmachines/simulateeviction) maszyny wirtualnej na miejscu, aby przetestować, w jaki sposób aplikacja będzie odistnieć do nagłego wykluczenia. 

Zastąp następujące informacje następującymi informacjami: 

- `subscriptionId`
- `resourceGroupName`
- `vmName`


```http
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}/simulateEviction?api-version=2020-06-01
```

## <a name="next-steps"></a>Następne kroki

Możesz również utworzyć maszynę wirtualną na miejscu przy użyciu [interfejsu wiersza polecenia platformy Azure](../linux/spot-cli.md), [portalu](../spot-portal.md) lub [szablonu](../linux/spot-template.md).

Zapoznaj się z bieżącymi informacjami o cenach przy użyciu [interfejsu API cen detalicznych platformy Azure](/rest/api/cost-management/retail-prices/azure-retail-prices) , aby uzyskać informacje o cenach dodatkowych. Elementy `meterName` i `skuName` będą zawierać `Spot` .

Jeśli wystąpi błąd, zobacz [kody błędów](../error-codes-spot.md).