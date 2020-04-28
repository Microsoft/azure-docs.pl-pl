---
title: Podgląd grup umieszczania w sąsiedztwie dla zestawów skalowania maszyn wirtualnych
description: Dowiedz się więcej na temat tworzenia i używania grup umieszczania sąsiedztwa dla zestawów skalowania maszyn wirtualnych z systemem Windows na platformie Azure.
author: cynthn
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 07/01/2019
ms.author: cynthn
ms.openlocfilehash: 4fa2949e2a7e1b99ac26caa35f967e9dc9cf359a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "76273619"
---
# <a name="preview-creating-and-using-proximity-placement-groups-using-powershell"></a>Wersja zapoznawcza: Tworzenie i używanie grup umieszczania zbliżeniowe przy użyciu programu PowerShell

Aby zapewnić, że maszyny wirtualne będą możliwie jak najbliżej, osiągnięcie najmniejszego możliwego opóźnienia, należy wdrożyć zestaw skalowania w obrębie [grupy umieszczania sąsiedztwa](co-location.md#preview-proximity-placement-groups).

Grupa umieszczania bliskości jest grupą logiczną używaną w celu upewnienia się, że zasoby obliczeniowe platformy Azure znajdują się fizycznie blisko siebie. Grupy umieszczania zbliżeniowe są przydatne w przypadku obciążeń, w których jest wymagane małe opóźnienia.

> [!IMPORTANT]
> Grupy umieszczania w sąsiedztwie są obecnie dostępne w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>
> Grupy umieszczania w sąsiedztwie nie są dostępne w tych regionach w ramach wersji zapoznawczej: **Japonia Wschodnia**, **Australia Wschodnia** i **Indie Środkowe**.


## <a name="create-a-proximity-placement-group"></a>Tworzenie grupy umieszczania w pobliżu
Utwórz grupę umieszczania sąsiedztwa przy użyciu polecenia cmdlet [New-AzProximityPlacementGroup](https://docs.microsoft.com/powershell/module/az.compute/new-azproximityplacementgroup) . 

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


## <a name="create-a-scale-set"></a>Tworzenie zestawu skalowania

Utwórz skalę w grupie umieszczania sąsiedztwa przy `-ProximityPlacementGroup $ppg.Id` użyciu polecenia, aby odwołać się do identyfikatora grupy umieszczania bliskości podczas tworzenia zestawu skalowania przy użyciu polecenia [New-AzVMSS](https://docs.microsoft.com/powershell/module/az.compute/new-azvmss) .

```azurepowershell-interactive
$scalesetName = "myVM"

New-AzVmss `
  -ResourceGroupName $resourceGroup `
  -Location $location `
  -VMScaleSetName $scalesetName `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer" `
  -UpgradePolicyMode "Automatic" `
  -ProximityPlacementGroup $ppg.Id
```

Można zobaczyć wystąpienie w grupie umieszczania za pomocą polecenia [Get-AzProximityPlacementGroup](/powershell/module/az.compute/get-azproximityplacementgroup).

```azurepowershell-interactive
  Get-AzProximityPlacementGroup `
   -ResourceId $ppg.Id | Format-Table `
   -Wrap `
   -Property VirtualMachineScaleSets
```

## <a name="next-steps"></a>Następne kroki

Za pomocą [interfejsu wiersza polecenia platformy Azure](../virtual-machines/linux/proximity-placement-groups.md) można także tworzyć grupy umieszczania sąsiedztwa.
