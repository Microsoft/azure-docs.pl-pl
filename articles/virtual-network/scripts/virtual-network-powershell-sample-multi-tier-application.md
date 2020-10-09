---
title: Tworzenie sieci wirtualnej dla aplikacji wielowarstwowych — przykład skryptu Azure PowerShell
description: Tworzenie sieci wirtualnej dla aplikacji wielowarstwowych — przykład skryptu Azure PowerShell.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: twooley
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: powershell
ms.topic: sample
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 12/13/2018
ms.author: kumud
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 51cafb0a344c5b6226b3b033d8c60327eaf2c05f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91267959"
---
# <a name="create-a-network-for-multi-tier-applications-script-sample"></a>Przykładowy skrypt służący do tworzenia sieci dla aplikacji wielowarstwowych

Ten przykładowy skrypt tworzy sieć wirtualną z podsieciami frontonu i zaplecza. Ruch do podsieci frontonu jest ograniczony do protokołów HTTP i SSH, a ruch do podsieci zaplecza jest ograniczony do portu 3306 środowiska MySQL. Po uruchomieniu skryptu będziesz mieć dwie maszyny wirtualne, po jednej w każdej podsieci, w której można wdrożyć serwer internetowy i oprogramowanie MySQL.

Skrypt można wykonać z poziomu usługi Azure [Cloud Shell](https://shell.azure.com/powershell) lub z lokalnej instalacji programu PowerShell. Jeśli używasz programu PowerShell lokalnie, ten skrypt wymaga modułu Azure PowerShell w wersji 1.0.0 lub nowszej. Aby dowiedzieć się, jaka wersja została zainstalowana, uruchom polecenie `Get-Module -ListAvailable Az`. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-az-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Connect-AzAccount`, aby utworzyć połączenie z platformą Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Przykładowy skrypt

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

<!-- gitHub issue https://github.com/MicrosoftDocs/azure-docs/issues/17748 -->
 Identyfikator podsieci jest przypisywany po utworzeniu sieci wirtualnej; w szczególności za pomocą polecenia cmdlet New-AzVirtualNetwork z opcją -Subnet. Jeśli skonfigurujesz podsieć przy użyciu polecenia cmdlet New-AzVirtualNetworkSubnetConfig przed wywołaniem polecenia New-AzVirtualNetwork, identyfikator podsieci nie będzie widoczny, dopóki nie zostanie wywołane polecenie New-AzVirtualNetwork.

[!code-azurepowershell-interactive[main](../../../powershell_scripts/virtual-network/virtual-network-multi-tier-application/virtual-network-multi-tier-application.ps1  "Virtual network for multi-tier application")]

## <a name="clean-up-deployment"></a>Czyszczenie wdrożenia

Uruchom następujące polecenie, aby usunąć grupę zasobów, maszynę wirtualną i wszystkie powiązane zasoby:

```powershell
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="script-explanation"></a>Objaśnienia dla skryptu

Ten skrypt zawiera następujące polecenia służące do tworzenia grupy zasobów, sieci wirtualnej i sieciowych grup zabezpieczeń. Każde polecenie w poniższej tabeli stanowi link do dokumentacji polecenia:

| Polecenie | Uwagi |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Tworzy grupę zasobów, w której są przechowywane wszystkie zasoby. |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Tworzy sieć wirtualną i podsieć frontonu platformy Azure. |
| [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) | Tworzy podsieć zaplecza. |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Tworzy publiczny adres IP umożliwiający uzyskiwanie dostępu do maszyny wirtualnej z Internetu. |
| [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) | Tworzy interfejsy sieci wirtualnej i dołącza je do podsieci frontonu i zaplecza sieci wirtualnej. |
| [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) | Tworzy sieciowe grupy zabezpieczeń, które zostały skojarzone z podsieciami frontonu i zaplecza. |
| [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig) |Tworzy reguły sieciowych grup zabezpieczeń, które zezwalają na użycie określonych portów w wybranych sieciach lub blokują te porty. |
| [New-AzVM](/powershell/module/az.compute/new-azvm) | Tworzy maszyny wirtualne i dołącza kartę sieciową do każdej maszyny wirtualnej. To polecenie określa również obraz maszyny wirtualnej do użycia oraz poświadczenia administracyjne. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Usuwa grupę zasobów i wszystkie zasoby, które zawiera. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat programu Azure PowerShell, zobacz [dokumentację programu Azure PowerShell](/powershell/azure/).

Więcej przykładowych skryptów programu PowerShell dla sieci wirtualnej można znaleźć w temacie [Virtual network PowerShell samples](../powershell-samples.md) (Przykładowe skrypty programu PowerShell dla sieci wirtualnej).
