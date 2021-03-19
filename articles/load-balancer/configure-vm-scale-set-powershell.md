---
title: Skonfiguruj zestaw skalowania maszyn wirtualnych przy użyciu istniejącego Azure Load Balancer-Azure PowerShell
description: Dowiedz się, jak skonfigurować zestaw skalowania maszyn wirtualnych przy użyciu istniejącego Azure Load Balancer.
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: how-to
ms.date: 03/26/2020
ms.openlocfilehash: 919883025075ca38bcef6c23ac744528e7bd5502
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "88510066"
---
# <a name="configure-a-virtual-machine-scale-set-with-an-existing-azure-load-balancer-using-azure-powershell"></a>Skonfiguruj zestaw skalowania maszyn wirtualnych za pomocą istniejącego Azure Load Balancer przy użyciu Azure PowerShell

W tym artykule dowiesz się, jak skonfigurować zestaw skalowania maszyn wirtualnych przy użyciu istniejącego Azure Load Balancer.

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure.
- Istniejący moduł równoważenia obciążenia standardowej jednostki SKU w subskrypcji, w której zostanie wdrożony zestaw skalowania maszyn wirtualnych.
- Virtual Network platformy Azure dla zestawu skalowania maszyn wirtualnych.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure-cli"></a>Logowanie do interfejsu wiersza polecenia platformy Azure

Zaloguj się do platformy Azure.

```azurepowershell-interactive
Connect-AzAccount
```

## <a name="deploy-a-virtual-machine-scale-set-with-existing-load-balancer"></a>Wdrażanie zestawu skalowania maszyn wirtualnych z istniejącym modułem równoważenia obciążenia

Zastąp wartości w nawiasach nazwami zasobów w konfiguracji.

```azurepowershell-interactive

$rsg = <resource-group>
$loc = <location>
$vms = <vm-scale-set-name>
$vnt = <virtual-network>
$sub = <subnet-name>
$lbn = <load-balancer-name>
$pol = <upgrade-policy-mode>

$lb = Get-AzLoadBalancer -ResourceGroupName $rsg -Name $lbn

New-AzVmss -ResourceGroupName $rsg -Location $loc -VMScaleSetName $vms -VirtualNetworkName $vnt -SubnetName $sub -LoadBalancerName $lb -UpgradePolicyMode $pol

```

Poniższy przykład służy do wdrażania zestawu skalowania maszyn wirtualnych przy użyciu:

- Zestaw skalowania maszyn wirtualnych o nazwie **myVMSS**
- Azure Load Balancer o nazwie **myLoadBalancer**
- Pula zaplecza modułu równoważenia obciążenia o nazwie **myBackendPool**
- Virtual Network platformy Azure o nazwie **myVnet**
- Podsieć o nazwie **Nosubnet**
- Grupa zasobów o nazwie Moja **zasobów**

```azurepowershell-interactive

$rsg = "myResourceGroup"
$loc = "East US 2"
$vms = "myVMSS"
$vnt = "myVnet"
$sub = "mySubnet"
$pol = "Automatic"
$lbn = "myLoadBalancer"

$lb = Get-AzLoadBalancer -ResourceGroupName $rsg -Name $lbn

New-AzVmss -ResourceGroupName $rsg -Location $loc -VMScaleSetName $vms -VirtualNetworkName $vnt -SubnetName $sub -LoadBalancerName $lb -UpgradePolicyMode $pol
```
> [!NOTE]
> Po utworzeniu zestawu skalowania nie można zmodyfikować portu zaplecza dla reguły równoważenia obciążenia używanej przez sondę kondycji modułu równoważenia obciążenia. Aby zmienić port, można usunąć sondę kondycji, aktualizując zestaw skalowania maszyn wirtualnych platformy Azure, zaktualizować port, a następnie ponownie skonfiguruj sondę kondycji.

## <a name="next-steps"></a>Następne kroki

Ten artykuł zawiera wdrożenie zestawu skalowania maszyn wirtualnych z istniejącym Azure Load Balancer.  Aby dowiedzieć się więcej na temat usługi Virtual Machine Scale Sets i modułu równoważenia obciążenia, zobacz:

- [Co to jest usługa Azure Load Balancer?](load-balancer-overview.md)
- [Co to są zestawy skalowania maszyn wirtualnych?](../virtual-machine-scale-sets/overview.md)
