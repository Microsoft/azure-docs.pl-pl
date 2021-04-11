---
title: Określ informacje o planie zakupu w witrynie Marketplace przy użyciu Azure PowerShell
description: Dowiedz się, jak określić szczegóły planu zakupów w portalu Azure Marketplace podczas tworzenia obrazów w udostępnionej galerii obrazów.
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.topic: how-to
ms.workload: infrastructure
ms.date: 07/07/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: 9df1f6b02e6572c8f2153016c0142912e24fcfe8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102562541"
---
# <a name="supply-azure-marketplace-purchase-plan-information-when-creating-images"></a>Dostarcz informacje o planie zakupu portalu Azure Marketplace podczas tworzenia obrazów

Jeśli tworzysz obraz w udostępnionej galerii, przy użyciu źródła, które było pierwotnie utworzone na podstawie obrazu portalu Azure Marketplace, może być konieczne śledzenie informacji o planie zakupu. W tym artykule pokazano, jak znaleźć informacje o planie zakupu dla maszyny wirtualnej, a następnie użyć tych informacji podczas tworzenia definicji obrazu. Firma Microsoft obejmuje również korzystanie z informacji z definicji obrazu w celu uproszczenia dostarczania informacji o planie zakupu podczas tworzenia maszyny wirtualnej dla obrazu.

Aby uzyskać więcej informacji na temat znajdowania i używania obrazów z portalu Marketplace, zobacz [Znajdowanie i używanie obrazów w portalu Azure Marketplace](./windows/cli-ps-findimage.md).


## <a name="get-the-source-vm-information"></a>Pobierz informacje o źródłowej maszynie wirtualnej
Jeśli nadal masz oryginalną maszynę wirtualną, możesz uzyskać od niej nazwę planu, wydawcę i informacje o produkcie przy użyciu polecenia Get-AzVM. Ten przykład pobiera maszynę wirtualną o nazwie *myVM* w grupie zasobów zasobu *webresources* , a następnie wyświetla informacje o planie zakupu dla maszyny wirtualnej.

```azurepowershell-interactive
$vm = Get-azvm `
   -ResourceGroupName myResourceGroup `
   -Name myVM
$vm.Plan
```

## <a name="create-the-image-definition"></a>Tworzenie definicji obrazu

Pobierz galerię obrazów, która ma być używana do przechowywania obrazu. Wszystkie galerie można wyświetlić w pierwszej kolejności.

```azurepowershell-interactive
Get-AzResource -ResourceType Microsoft.Compute/galleries | Format-Table
```

Następnie utwórz zmienne dla galerii, których chcesz użyć. W tym przykładzie tworzymy zmienną o nazwie `$gallery` dla *galerii* w grupie zasobów *myGalleryRG* .

```azurepowershell-interactive
$gallery = Get-AzGallery `
   -Name myGallery `
   -ResourceGroupName myGalleryRG
```

Utwórz definicję obrazu przy użyciu  `-PurchasePlanPublisher` `-PurchasePlanProduct` parametrów, i `-PurchasePlanName` .

```azurepowershell-interactive
 $imageDefinition = New-AzGalleryImageDefinition `
   -GalleryName $gallery.Name `
   -ResourceGroupName $gallery.ResourceGroupName `
   -Location $gallery.Location `
   -Name 'myImageDefinition' `
   -OsState specialized `
   -OsType Linux `
   -Publisher 'myPublisher' `
   -Offer 'myOffer' `
   -Sku 'mySKU' `
   -PurchasePlanPublisher $vm.Plan.Publisher `
   -PurchasePlanProduct $vm.Plan.Product `
   -PurchasePlanName  $vm.Plan.Name
```

Następnie Utwórz wersję obrazu przy użyciu polecenia [New-AzGalleryImageVersion](/powershell/module/az.compute/new-azgalleryimageversion). Możesz utworzyć wersję obrazu z [maszyny wirtualnej](image-version-vm-powershell.md#create-an-image-version), [obrazu zarządzanego](image-version-managed-image-powershell.md#create-an-image-version), [VHD\snapshot](image-version-snapshot-powershell.md#create-an-image-version)lub [innej wersji obrazu](image-version-another-gallery-powershell.md#create-the-image-version). 


## <a name="create-the-vm"></a>Tworzenie maszyny wirtualnej

Po przejściu do tworzenia maszyny wirtualnej z obrazu można użyć informacji z definicji obrazu do przekazania informacji o wydawcy przy użyciu polecenia [Set-AzVMPlan](/powershell/module/az.compute/set-azvmplan).


```azurepowershell-interactive
# Create some variables for the new VM.
$resourceGroup = "mySIGPubVM"
$location = "West Central US"
$vmName = "mySIGPubVM"

# Create a resource group
New-AzResourceGroup -Name $resourceGroup -Location $location

# Create the network resources.
$subnetConfig = New-AzVirtualNetworkSubnetConfig `
   -Name mySubnet `
   -AddressPrefix 192.168.1.0/24
$vnet = New-AzVirtualNetwork `
   -ResourceGroupName $resourceGroup `
   -Location $location `
   -Name MYvNET `
   -AddressPrefix 192.168.0.0/16 `
   -Subnet $subnetConfig
$pip = New-AzPublicIpAddress `
   -ResourceGroupName $resourceGroup `
   -Location $location `
  -Name "mypublicdns$(Get-Random)" `
  -AllocationMethod Static `
  -IdleTimeoutInMinutes 4
$nsgRuleRDP = New-AzNetworkSecurityRuleConfig `
   -Name myNetworkSecurityGroupRuleRDP  `
   -Protocol Tcp `
   -Direction Inbound `
   -Priority 1000 `
   -SourceAddressPrefix * `
   -SourcePortRange * `
   -DestinationAddressPrefix * `
   -DestinationPortRange 3389 -Access Allow
$nsg = New-AzNetworkSecurityGroup `
   -ResourceGroupName $resourceGroup `
   -Location $location `
   -Name myNetworkSecurityGroup `
   -SecurityRules $nsgRuleRDP
$nic = New-AzNetworkInterface `
   -Name $vmName `
   -ResourceGroupName $resourceGroup `
   -Location $location `
  -SubnetId $vnet.Subnets[0].Id `
  -PublicIpAddressId $pip.Id `
  -NetworkSecurityGroupId $nsg.Id

# Create a virtual machine configuration using Set-AzVMSourceImage -Id $imageDefinition.Id to use the latest available image version. Set-AZVMPlan is used to pass the plan information in for the VM.

$vmConfig = New-AzVMConfig `
   -VMName $vmName `
   -VMSize Standard_D1_v2   | `
   Set-AzVMSourceImage -Id $imageDefinition.Id | `
   Set-AzVMPlan `
     -Publisher $imageDefinition.PurchasePlan.Publisher `
     -Product $imageDefinition.PurchasePlan.Product `
     -Name $imageDefinition.PurchasePlan.Name | `
   Add-AzVMNetworkInterface -Id $nic.Id

# Create the virtual machine
New-AzVM `
   -ResourceGroupName $resourceGroup `
   -Location $location `
   -VM $vmConfig
```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat znajdowania i używania obrazów z portalu Marketplace, zobacz [Znajdowanie i używanie obrazów w portalu Azure Marketplace](./windows/cli-ps-findimage.md).
