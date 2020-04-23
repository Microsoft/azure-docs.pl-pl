---
title: Otwieranie portów na maszynie wirtualnej przy użyciu programu Azure PowerShell
description: Dowiedz się, jak otworzyć port / utworzyć punkt końcowy dla maszyny Wirtualnej systemu Windows przy użyciu trybu wdrażania menedżera zasobów platformy Azure i programu Azure PowerShell
author: cynthn
ms.service: virtual-machines-windows
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 12/13/2017
ms.author: cynthn
ms.openlocfilehash: a0dcc53d84edb4dd697213106c02626df24acfd8
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81869394"
---
# <a name="how-to-open-ports-and-endpoints-to-a-vm-in-azure-using-powershell"></a>Jak otworzyć porty i punkty końcowe na maszynie Wirtualnej na platformie Azure przy użyciu programu PowerShell
[!INCLUDE [virtual-machines-common-nsg-quickstart](../../../includes/virtual-machines-common-nsg-quickstart.md)]

## <a name="quick-commands"></a>Szybkie polecenia
Aby utworzyć sieciową grupę zabezpieczeń i reguły listy ACL, potrzebujesz [zainstalowanej najnowszej wersji programu Azure PowerShell](/powershell/azureps-cmdlets-docs). Można również [wykonać te kroki za pomocą witryny Azure portal](nsg-quickstart-portal.md).

Zaloguj się do konta platformy Azure:

```powershell
Connect-AzAccount
```

W poniższych przykładach zamień nazwy parametrów na własne wartości. Przykładowe nazwy parametrów obejmowały *myResourceGroup*, *myNetworkSecurityGroup*i *myVnet*.

Utwórz regułę za pomocą [pliku New-AzNetworkSecurityRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecurityruleconfig). Poniższy przykład tworzy regułę o nazwie *myNetworkSecurityGroupRule,* aby zezwolić na ruch *tcp* na porcie *80:*

```powershell
$httprule = New-AzNetworkSecurityRuleConfig `
    -Name "myNetworkSecurityGroupRule" `
    -Description "Allow HTTP" `
    -Access "Allow" `
    -Protocol "Tcp" `
    -Direction "Inbound" `
    -Priority "100" `
    -SourceAddressPrefix "Internet" `
    -SourcePortRange * `
    -DestinationAddressPrefix * `
    -DestinationPortRange 80
```

Następnie utwórz grupę zabezpieczeń sieciowych za pomocą [new-AzNetworkSecurityGroup](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecuritygroup) i przypisz utworzoną właśnie regułę HTTP w następujący sposób. Poniższy przykład tworzy grupę zabezpieczeń sieci o nazwie *myNetworkSecurityGroup:*

```powershell
$nsg = New-AzNetworkSecurityGroup `
    -ResourceGroupName "myResourceGroup" `
    -Location "EastUS" `
    -Name "myNetworkSecurityGroup" `
    -SecurityRules $httprule
```

Teraz przypiszmy grupę zabezpieczeń sieci do podsieci. Poniższy przykład przypisuje istniejącą sieć wirtualną o nazwie *myVnet* do zmiennej *$vnet* za pomocą [Get-AzVirtualNetwork:](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork)

```powershell
$vnet = Get-AzVirtualNetwork `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVnet"
```

Skojarz swoją grupę zabezpieczeń sieciowych z podsiecią z [Set-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/set-azvirtualnetworksubnetconfig). Poniższy przykład kojarzy podsieć o nazwie *mySubnet* z sieciową grupą zabezpieczeń:

```powershell
$subnetPrefix = $vnet.Subnets|?{$_.Name -eq 'mySubnet'}

Set-AzVirtualNetworkSubnetConfig `
    -VirtualNetwork $vnet `
    -Name "mySubnet" `
    -AddressPrefix $subnetPrefix.AddressPrefix `
    -NetworkSecurityGroup $nsg
```

Na koniec zaktualizuj sieć wirtualną za pomocą [sieci Set-AzVirtualNetwork,](https://docs.microsoft.com/powershell/module/az.network/set-azvirtualnetwork) aby zmiany zostały wprowadzone:

```powershell
Set-AzVirtualNetwork -VirtualNetwork $vnet
```


## <a name="more-information-on-network-security-groups"></a>Więcej informacji na temat grup zabezpieczeń sieci
Szybkie polecenia w tym miejscu pozwalają rozpocząć i działać z ruchem płynącym do maszyny Wirtualnej. Sieciowe grupy zabezpieczeń zapewniają wiele wspaniałych funkcji i szczegółowość do kontrolowania dostępu do zasobów. Więcej informacji na temat [tworzenia sieciowej grupy zabezpieczeń i reguł listy ACL](tutorial-virtual-network.md#secure-network-traffic)można znaleźć tutaj .

W przypadku aplikacji sieci Web o wysokiej dostępności należy umieścić maszyny wirtualne za modułem równoważenia obciążenia platformy Azure. Moduł równoważenia obciążenia rozdziela ruch do maszyn wirtualnych z sieciową grupą zabezpieczeń, która zapewnia filtrowanie ruchu. Aby uzyskać więcej informacji, zobacz [Jak załadować równoważenie maszyn wirtualnych systemu Linux na platformie Azure, aby utworzyć aplikację o wysokiej dostępności](tutorial-load-balancer.md).

## <a name="next-steps"></a>Następne kroki
W tym przykładzie utworzono prostą regułę zezwalania na ruch HTTP. Informacje na temat tworzenia bardziej szczegółowych środowisk można znaleźć w następujących artykułach:

* [Omówienie usługi Azure Resource Manager](../../azure-resource-manager/management/overview.md)
* [Co to jest grupa zabezpieczeń sieci?](../../virtual-network/security-overview.md)
* [Omówienie równoważenia obciążenia platformy Azure](../../load-balancer/load-balancer-overview.md)

