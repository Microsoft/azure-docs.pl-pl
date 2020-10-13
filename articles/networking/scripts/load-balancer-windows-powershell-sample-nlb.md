---
title: Równoważenie obciążenia ruchu do maszyn wirtualnych na potrzeby Azure PowerShell HA
description: Przykład skryptu Azure PowerShell — Równoważenie obciążenia ruchem do maszyn wirtualnych w celu zapewnienia wysokiej dostępności
services: load-balancer
documentationcenter: load-balancer
author: asudbring
manager: KumudD
ms.service: load-balancer
ms.devlang: powershell
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 05/16/2017
ms.author: allensu
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: e8663a5c86a4d3a247209fb7aedaa422eb6ec9e4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89079017"
---
# <a name="load-balance-traffic-to-vms-for-high-availability"></a>Równoważenie obciążenia ruchem maszyn wirtualnych w celu uzyskania wysokiej dostępności

Ten przykładowy skrypt tworzy wszystko, co jest potrzebne do uruchomienia kilku maszyn wirtualnych z systemem Windows skonfigurowanych w konfiguracji o wysokiej dostępności i o zrównoważonym obciążeniu. Po uruchomieniu skryptu będziesz mieć trzy maszyny wirtualne dołączone do zestawu dostępności platformy Azure i dostępne za pośrednictwem usługi Azure Load Balancer.

W razie potrzeby zainstaluj Azure PowerShell przy użyciu instrukcji znajdującej się w [przewodniku Azure PowerShell](https://docs.microsoft.com/powershell/azure/), a następnie uruchom polecenie, `Connect-AzAccount` Aby utworzyć połączenie z platformą Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Przykładowy skrypt

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-vm-nlb/create-vm-nlb.ps1 "Quick Create VM")]

## <a name="clean-up-deployment"></a>Czyszczenie wdrożenia 

Uruchom następujące polecenie, aby usunąć grupę zasobów, maszynę wirtualną i wszystkie powiązane zasoby.

```powershell
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>Objaśnienia dla skryptu

Ten skrypt zawiera następujące polecenia służące do tworzenia grupy zasobów, maszyny wirtualnej, zestawu dostępności, modułu równoważenia obciążenia i wszystkich powiązanych zasobów. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Tworzy grupę zasobów, w której są przechowywane wszystkie zasoby. |
| [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) | Tworzy konfigurację podsieci. Ta konfiguracja jest używana podczas procesu tworzenia sieci wirtualnej. |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Tworzy sieć wirtualną i podsieć platformy Azure. |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress)  | Tworzy publiczny adres IP przy użyciu statycznego adresu IP i skojarzonej nazwy DNS. |
| [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer)  | Tworzy moduł równoważenia obciążenia platformy Azure. |
| [New-AzLoadBalancerProbeConfig](/powershell/module/az.network/new-azloadbalancerprobeconfig) | Tworzy sondę modułu równoważenia obciążenia. Sonda modułu równoważenia obciążenia służy do monitorowania każdej maszyny wirtualnej w zestawie modułu równoważenia obciążenia. Jeśli maszyna wirtualna stanie się niedostępna, ruch nie będzie do niej kierowany. |
| [New-AzLoadBalancerRuleConfig](/powershell/module/az.network/new-azloadbalancerruleconfig) | Tworzy regułę modułu równoważenia obciążenia. W tym przykładzie tworzona jest reguła dla portu 80. Ruch HTTP docierający do modułu równoważenia obciążenia jest kierowany do portu 80 jednej z maszyn wirtualnych w zestawie modułu równoważenia obciążenia. |
| [New-AzLoadBalancerInboundNatRuleConfig](/powershell/module/az.network/new-azloadbalancerinboundnatruleconfig) | Tworzy regułę translatora adresów sieciowych modułu równoważenia obciążenia.  Reguły translatora adresów sieciowych mapują port modułu równoważenia obciążenia na port na maszynie wirtualnej. W tym przykładzie tworzona jest reguła translatora adresów sieciowych dla ruchu SSH do każdej maszyny wirtualnej w zestawie modułu równoważenia obciążenia.  |
| [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) | Tworzy sieciową grupę zabezpieczeń, czyli granicę zabezpieczeń między Internetem i maszyną wirtualną. |
| [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig) | Tworzy regułę sieciowej grupy zabezpieczeń zezwalającą na ruch przychodzący. W tym przykładzie port 22 jest otwierany dla ruchu protokołu SSH. |
| [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) | Tworzy wirtualną kartę sieciową i dołącza ją do sieci wirtualnej, podsieci i sieciowej grupy zabezpieczeń. |
| [New-AzAvailabilitySet](/powershell/module/az.compute/new-azavailabilityset) | Tworzy zestaw dostępności. Zestawy dostępności zapewniają działanie aplikacji bez przestojów dzięki rozmieszczeniu maszyn wirtualnych w ramach zasobów fizycznych tak, aby niepowodzenie nie wpływało na cały zestaw. |
| [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig) | Tworzy konfigurację maszyny wirtualnej. Ta konfiguracja zawiera informacje, takie jak nazwa maszyny wirtualnej, system operacyjny i poświadczenia administracyjne. Jest ona używana podczas tworzenia maszyny wirtualnej. |
| [New-AzVM](/powershell/module/az.compute/new-azvm)  | Tworzy maszynę wirtualną i łączy ją z kartą sieciową, siecią wirtualną, podsiecią i sieciową grupą zabezpieczeń. To polecenie określa również obraz maszyny wirtualnej do użycia oraz poświadczenia administracyjne.  |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Usuwa grupę zasobów wraz ze wszystkimi zagnieżdżonymi zasobami. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat programu Azure PowerShell, zobacz [dokumentację programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/).

Dodatkowe przykłady skryptów programu PowerShell dla sieci można znaleźć w [dokumentacji i omówieniu sieci platformy Azure](../powershell-samples.md?toc=%2fazure%2fnetworking%2ftoc.json).
