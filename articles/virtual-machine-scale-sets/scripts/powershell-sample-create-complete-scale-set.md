---
title: Przykłady Azure PowerShell — tworzenie kompletnego zestawu skalowania maszyn wirtualnych
description: Ten skrypt tworzy zestaw skalowania maszyn wirtualnych z systemem Windows Server 2016, w którym są skonfigurowane i tworzone poszczególne zasoby.
author: mimckitt
ms.author: mimckitt
ms.topic: sample
ms.service: virtual-machine-scale-sets
ms.subservice: powershell
ms.date: 05/29/2018
ms.reviewer: jushiman
ms.custom: mimckitt, devx-track-azurepowershell
ms.openlocfilehash: d37f5f624459db6bc336884987a16c60503492a8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "89078507"
---
# <a name="create-a-complete-virtual-machine-scale-set-with-powershell"></a>Tworzenie pełnego zestawu skalowania maszyn wirtualnych przy użyciu programu PowerShell

Ten skrypt tworzy zestaw skalowania maszyn wirtualnych z systemem Windows Server 2016. Poszczególne zasoby są konfigurowane i tworzone, a nie za pomocą [wbudowanych opcji tworzenia zasobów dostępnych tutaj w artykule New-AzVmss](powershell-sample-create-simple-scale-set.md). Po uruchomieniu skryptu możesz uzyskać dostęp do wystąpień maszyn wirtualnych za pośrednictwem protokołu RDP.


[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="sample-script"></a>Przykładowy skrypt

[!code-powershell[main](../../../powershell_scripts/virtual-machine-scale-sets/complete-scale-set/complete-scale-set.ps1 "Create a complete virtual machine scale set")]

## <a name="clean-up-deployment"></a>Czyszczenie wdrożenia
Uruchom następujące polecenie, aby usunąć grupę zasobów, zestaw skalowania i wszystkie powiązane zasoby.

```powershell
Remove-AzResourceGroup -Name $resourceGroupName
```

## <a name="script-explanation"></a>Objaśnienia dla skryptu
Ten skrypt używa następujących poleceń w celu utworzenia wdrożenia. Każda pozycja w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Tworzy grupę zasobów, w której są przechowywane wszystkie zasoby. |
| [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) | Tworzy konfigurację podsieci. Ta konfiguracja jest używana podczas procesu tworzenia sieci wirtualnej. |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Tworzy sieć wirtualną. |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Tworzy publiczny adres IP. |
| [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig) | Tworzy konfigurację adresu IP frontonu na potrzeby modułu równoważenia obciążenia. |
| [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig) | Tworzy konfigurację puli adresów zaplecza na potrzeby modułu równoważenia obciążenia. |
| [New-AzLoadBalancerInboundNatRuleConfig](/powershell/module/az.network/new-azloadbalancerinboundnatruleconfig) | Tworzy konfigurację reguły NAT dla ruchu przychodzącego na potrzeby modułu równoważenia obciążenia. |
| [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer) | Tworzy moduł równoważenia obciążenia. |
| [Add-AzLoadBalancerProbeConfig](/powershell/module/az.network/new-azloadbalancerprobeconfig) | Tworzy konfigurację sondowania na potrzeby modułu równoważenia obciążenia. |
| [Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/new-azloadbalancerruleconfig) | Tworzy konfigurację reguły na potrzeby modułu równoważenia obciążenia. |
| [Set-AzLoadBalancer](/powershell/module/az.Network/Set-azLoadBalancer) | Zaktualizuj usługę równoważenia obciążenia przy użyciu podanych informacji. |
| [New-AzVmssIpConfig](/powershell/module/az.Compute/New-azVmssIpConfig) | Utwórz konfigurację protokołu IP dla zestawu skalowania wystąpień maszyny wirtualnej. Wystąpienia maszyny wirtualnej są połączone z pulą zaplecza modułu równoważenia obciążenia, pulą translacji NAT i podsiecią sieci wirtualnej. |
| [New-AzVmssConfig](/powershell/module/az.Compute/New-azVmssConfig) | Tworzy konfigurację zestawu skali. Ta konfiguracja zawiera informacje, takie jak liczba tworzonych wystąpień maszyn wirtualnych, jednostka magazynowa maszyny wirtualnej (rozmiar) i tryb zasad uaktualniania. Konfiguracja jest dodawana przez dodatkowe polecenia cmdlet i jest używana podczas tworzenia zestawu skalowania. |
| [Set-AzVmssStorageProfile](/powershell/module/az.Compute/Set-azVmssStorageProfile) | Zdefiniuj obraz, który ma służyć do tworzenia wystąpień maszyn wirtualnych, i dodaj go do konfiguracji zestawu skalowania. |
| [Set-AzVmssOsProfile](/powershell/module/az.Compute/Set-azVmssStorageProfile) | Zdefiniuj nazwę i hasło użytkownika z uprawnieniami administracyjnymi oraz prefiks nazwy maszyny wirtualnej. Dodaj te wartości do konfiguracji zestawu skalowania. |
| [Add-AzVmssNetworkInterfaceConfiguration](/powershell/module/az.Compute/Add-azVmssNetworkInterfaceConfiguration) | Dodaj interfejs sieci wirtualnej do wystąpień maszyn wirtualnych w oparciu o konfigurację IP. Dodaj te wartości do konfiguracji zestawu skalowania. |
| [New-AzVmss](/powershell/module/az.Compute/New-azVmss) | W oparciu o informacje zawarte w konfiguracji zestawu skalowania utwórz zestaw skalowania. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Usuwa grupę zasobów i wszystkie zasoby w niej zawarte. |

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji na temat modułu Azure PowerShell, zobacz [dokumentację programu Azure PowerShell](/powershell/azure/).
