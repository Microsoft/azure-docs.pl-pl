---
title: Tworzenie zestawu skalowania na podstawie obrazu specjalistycznego
description: Utwórz zestaw skalowania za pomocą wyspecjalizowanego obrazu w galerii obrazów udostępnionych.
author: cynthn
ms.service: virtual-machine-scale-sets
ms.subservice: imaging
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: 8fd23e52f045ba007d4b3b48b5e2e4269df53975
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91336109"
---
# <a name="create-a-scale-set-from-a-specialized-image-using-powershell"></a>Tworzenie zestawu skalowania na podstawie wyspecjalizowanego obrazu przy użyciu programu PowerShell 

Utwórz maszynę wirtualną na podstawie wyspecjalizowanej wersji obrazu zapisanej w [galerii obrazów udostępnionych](shared-image-galleries.md) przy użyciu Azure PowerShell. Jeśli chcesz utworzyć zestaw skalowania przy użyciu uogólnionej wersji obrazu, zobacz [Tworzenie wystąpień zestawu skalowania na podstawie uogólnionej wersji obrazu](instance-generalized-image-version-powershell.md).

Po umieszczeniu wyspecjalizowanego obrazu w galerii można utworzyć zestaw skalowania maszyn wirtualnych za pomocą polecenia cmdlet [New-AzVmss](/powershell/module/az.compute/new-azvmss) . 

W tym przykładzie używamy identyfikatora definicji obrazu, aby upewnić się, że nowa maszyna wirtualna będzie używać najnowszej wersji obrazu. Można również użyć określonej wersji za pomocą identyfikatora wersji obrazu dla programu `-ImageReferenceId` . Na przykład, aby użyć *1.0.0* wersji obrazu typu: `-ImageReferenceId "/subscriptions/<subscription ID where the gallery is located>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition/versions/1.0.0"` . 

Należy pamiętać, że użycie określonej wersji obrazu oznacza, że Automatyzacja może się nie powieść, jeśli określona wersja obrazu jest niedostępna, ponieważ została usunięta lub usunięta z regionu. Zalecamy użycie identyfikatora definicji obrazu do tworzenia nowej maszyny wirtualnej, o ile nie jest wymagana określona wersja obrazu.

W poniższych przykładach przedstawiono tworzenie zestawu skalowania o nazwie *myScaleSet*w grupie zasobów *MyVMSSRG* w lokalizacji *SouthCentralUS* . Zestaw skalowania zostanie utworzony na podstawie obrazu *myImageDefinition* w *galerii obrazów galerii* w grupie zasobów *myGalleryRG* . Po wyświetleniu monitu Ustaw własne poświadczenia administracyjne dla wystąpień maszyn wirtualnych w zestawie skalowania.



```azurepowershell-interactive
# Get the image definition

$imageDefinition = Get-AzGalleryImageDefinition `
   -GalleryName myGallery `
   -ResourceGroupName myGalleryRG `
   -Name myImageDefinition
   
# Define variables for the scale set
$resourceGroupName = "myVMSSRG"
$scaleSetName = "myScaleSet"
$location = "South Central US"

# Create a resource group
New-AzResourceGroup -ResourceGroupName $resourceGroupName -Location $location

# Create a networking pieces
$subnet = New-AzVirtualNetworkSubnetConfig `
  -Name "mySubnet" `
  -AddressPrefix 10.0.0.0/24
$vnet = New-AzVirtualNetwork `
  -ResourceGroupName $resourceGroupName `
  -Name "myVnet" `
  -Location $location `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $subnet
$publicIP = New-AzPublicIpAddress `
  -ResourceGroupName $resourceGroupName `
  -Location $location `
  -AllocationMethod Static `
  -Name "myPublicIP"
$frontendIP = New-AzLoadBalancerFrontendIpConfig `
  -Name "myFrontEndPool" `
  -PublicIpAddress $publicIP
$backendPool = New-AzLoadBalancerBackendAddressPoolConfig -Name "myBackEndPool"
$inboundNATPool = New-AzLoadBalancerInboundNatPoolConfig `
  -Name "myRDPRule" `
  -FrontendIpConfigurationId $frontendIP.Id `
  -Protocol TCP `
  -FrontendPortRangeStart 50001 `
  -FrontendPortRangeEnd 50010 `
  -BackendPort 3389
# Create the load balancer and health probe
$lb = New-AzLoadBalancer `
  -ResourceGroupName $resourceGroupName `
  -Name "myLoadBalancer" `
  -Location $location `
  -FrontendIpConfiguration $frontendIP `
  -BackendAddressPool $backendPool `
  -InboundNatPool $inboundNATPool
Add-AzLoadBalancerProbeConfig -Name "myHealthProbe" `
  -LoadBalancer $lb `
  -Protocol TCP `
  -Port 80 `
  -IntervalInSeconds 15 `
  -ProbeCount 2
Add-AzLoadBalancerRuleConfig `
  -Name "myLoadBalancerRule" `
  -LoadBalancer $lb `
  -FrontendIpConfiguration $lb.FrontendIpConfigurations[0] `
  -BackendAddressPool $lb.BackendAddressPools[0] `
  -Protocol TCP `
  -FrontendPort 80 `
  -BackendPort 80 `
  -Probe (Get-AzLoadBalancerProbeConfig -Name "myHealthProbe" -LoadBalancer $lb)
Set-AzLoadBalancer -LoadBalancer $lb

# Create IP address configurations
$ipConfig = New-AzVmssIpConfig `
  -Name "myIPConfig" `
  -LoadBalancerBackendAddressPoolsId $lb.BackendAddressPools[0].Id `
  -LoadBalancerInboundNatPoolsId $inboundNATPool.Id `
  -SubnetId $vnet.Subnets[0].Id

# Create a configuration 
$vmssConfig = New-AzVmssConfig `
    -Location $location `
    -SkuCapacity 2 `
    -SkuName "Standard_DS2" `
    -UpgradePolicyMode "Automatic"

# Reference the image version
Set-AzVmssStorageProfile $vmssConfig `
  -OsDiskCreateOption "FromImage" `
  -ImageReferenceId $imageDefinition.Id

# Complete the configuration
 
Add-AzVmssNetworkInterfaceConfiguration `
  -VirtualMachineScaleSet $vmssConfig `
  -Name "network-config" `
  -Primary $true `
  -IPConfiguration $ipConfig

# Create the scale set 
New-AzVmss `
  -ResourceGroupName $resourceGroupName `
  -Name $scaleSetName `
  -VirtualMachineScaleSet $vmssConfig
```

Utworzenie i skonfigurowanie wszystkich zasobów zestawu skalowania i maszyn wirtualnych trwa kilka minut.

## <a name="next-steps"></a>Następne kroki
[Usługa Azure Image Builder (wersja zapoznawcza)](../virtual-machines/linux/image-builder-overview.md) ułatwia automatyzację tworzenia wersji obrazu. można nawet użyć jej do aktualizacji i [tworzenia nowej wersji obrazu z istniejącej wersji obrazu](../virtual-machines/linux/image-builder-gallery-update-image-version.md).  

Możesz również utworzyć zasób udostępnionej galerii obrazów przy użyciu szablonów. Dostępnych jest kilka szablonów szybkiego startu platformy Azure: 

- [Tworzenie galerii obrazów udostępnionych](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Tworzenie definicji obrazu w usłudze Shared Image Gallery](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Tworzenie wersji obrazu w usłudze Shared Image Gallery](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)

Aby uzyskać więcej informacji na temat udostępnionych galerii obrazów, zobacz [Omówienie](shared-image-galleries.md). Jeśli występują problemy, zobacz temat [Rozwiązywanie problemów z udostępnionymi galeriami obrazów](../virtual-machines/troubleshooting-shared-images.md).

