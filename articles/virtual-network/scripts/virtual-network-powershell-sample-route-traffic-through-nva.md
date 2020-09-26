---
title: Kierowanie ruchu za pośrednictwem przykładu skryptu urządzenie WUS Azure PowerShell
description: Przykładowy skrypt Azure PowerShell — kierowanie ruchu przez urządzenie WUS zapory.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: powershell
ms.topic: sample
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/20/2018
ms.author: kumud
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 19bebb5c1817882dcb641a284332926e51a8dd2f
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91313998"
---
# <a name="route-traffic-through-a-network-virtual-appliance-script-sample"></a>Przykładowy skrypt służący do kierowania ruchu przez wirtualne urządzenie sieciowe

Ten przykładowy skrypt tworzy sieć wirtualną z podsieciami frontonu i zaplecza. Tworzy również maszynę wirtualną z włączonym przekazywaniem adresu IP, aby przekierowywać ruch między dwiema podsieciami. Po uruchomieniu skryptu można wdrażać oprogramowanie sieciowe, takie jak aplikacja zapory, na maszynie wirtualnej.

Skrypt można wykonać z poziomu usługi Azure [Cloud Shell](https://shell.azure.com/powershell) lub z lokalnej instalacji programu PowerShell. Jeśli używasz programu PowerShell lokalnie, ten skrypt wymaga modułu Az PowerShell w wersji 5.4.1 lub nowszej. Aby dowiedzieć się, jaka wersja została zainstalowana, uruchom polecenie `Get-Module -ListAvailable Az`. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-Az-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Connect-AzAccount`, aby utworzyć połączenie z platformą Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Przykładowy skrypt

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-azurepowershell-interactive[main](../../../powershell_scripts/virtual-network/route-traffic-through-nva/route-traffic-through-nva.ps1 "Route traffic through a network virtual appliance")]

## <a name="clean-up-deployment"></a>Czyszczenie wdrożenia

Uruchom następujące polecenie, aby usunąć grupę zasobów, maszynę wirtualną i wszystkie powiązane zasoby:

```powershell
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="script-explanation"></a>Objaśnienia dla skryptu

Ten skrypt zawiera następujące polecenia służące do tworzenia grupy zasobów, sieci wirtualnej i sieciowych grup zabezpieczeń. Każde polecenie w poniższej tabeli stanowi link do dokumentacji polecenia:

| Polecenie | Uwagi |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)  | Tworzy grupę zasobów, w której są przechowywane wszystkie zasoby. |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Tworzy sieć wirtualną i podsieć frontonu platformy Azure. |
| [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) | Tworzy podsieci zaplecza i strefy DMZ. |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Tworzy publiczny adres IP umożliwiający uzyskiwanie dostępu do maszyny wirtualnej z Internetu. |
| [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) | Tworzy interfejs sieci wirtualnej i włącza przekazywanie adresów IP. |
| [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) | Tworzy sieciową grupę zabezpieczeń. |
| [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig) | Tworzy reguły sieciowych grup zabezpieczeń, które zezwalają na użycie portów HTTP i HTTPS dla ruchu przychodzącego do maszyny wirtualnej. |
| [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig)| Tworzy skojarzenie sieciowych grup zabezpieczeń i tabel tras z podsieciami. |
| [New-AzRouteTable](/powershell/module/az.network/new-azroutetable)| Tworzy tabelę tras dla wszystkich tras. |
| [New-AzRouteConfig](/powershell/module/az.network/new-azrouteconfig)| Tworzy trasy na potrzeby kierowania ruchem między podsieciami i Internetem za pośrednictwem maszyny wirtualnej. |
| [New-AzVM](/powershell/module/az.compute/new-azvm) | Tworzy maszynę wirtualną i dołącza do niej kartę sieciową. To polecenie określa również obraz maszyny wirtualnej do użycia oraz poświadczenia administracyjne. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup)  | Usuwa grupę zasobów i wszystkie zasoby, które zawiera. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat programu Azure PowerShell, zobacz [dokumentację programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/).

Więcej przykładowych skryptów programu PowerShell dla sieci wirtualnej można znaleźć w temacie [Virtual network PowerShell samples](../powershell-samples.md) (Przykładowe skrypty programu PowerShell dla sieci wirtualnej).
