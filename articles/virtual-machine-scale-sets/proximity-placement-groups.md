---
title: Podgląd grup umieszczania w sąsiedztwie dla zestawów skalowania maszyn wirtualnych
description: Dowiedz się więcej na temat tworzenia i używania grup umieszczania sąsiedztwa dla zestawów skalowania maszyn wirtualnych z systemem Windows na platformie Azure.
author: cynthn
ms.author: cynthn
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: proximity-placement-groups
ms.date: 07/01/2019
ms.reviewer: zivr
ms.custom: mimckitt
ms.openlocfilehash: eeb0dd6cd5ce838c0f3a68763c40612038ee82f0
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105933495"
---
# <a name="preview-creating-and-using-proximity-placement-groups-using-powershell"></a>Wersja zapoznawcza: Tworzenie i używanie grup umieszczania zbliżeniowe przy użyciu programu PowerShell

Aby zapewnić, że maszyny wirtualne będą możliwie jak najbliżej, osiągnięcie najmniejszego możliwego opóźnienia, należy wdrożyć zestaw skalowania w obrębie [grupy umieszczania sąsiedztwa](../virtual-machines/co-location.md#proximity-placement-groups).

Grupa umieszczania bliskości jest grupą logiczną używaną w celu upewnienia się, że zasoby obliczeniowe platformy Azure znajdują się fizycznie blisko siebie. Grupy umieszczania zbliżeniowe są przydatne w przypadku obciążeń, w których jest wymagane małe opóźnienia.

> [!IMPORTANT]
> Grupy umieszczania w sąsiedztwie są obecnie dostępne w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>
> Grupy umieszczania w sąsiedztwie nie są dostępne w tych regionach w ramach wersji zapoznawczej: **Japonia Wschodnia**, **Australia Wschodnia** i **Indie Środkowe**.


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


## <a name="create-a-scale-set"></a>Tworzenie zestawu skalowania

Utwórz skalę w grupie umieszczania sąsiedztwa przy użyciu polecenia, `-ProximityPlacementGroup $ppg.Id` Aby odwołać się do identyfikatora grupy umieszczania bliskości podczas tworzenia zestawu skalowania przy użyciu polecenia [New-AzVMSS](/powershell/module/az.compute/new-azvmss) .

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
