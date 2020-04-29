---
title: Przykład programu PowerShell — Grupa trybu failover — Azure SQL Database wystąpienie zarządzane
description: Azure PowerShell przykładowy skrypt do utworzenia Azure SQL Database wystąpienia zarządzanego, dodania go do grupy trybu failover i przetestowania trybu failover.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: PowerShell
ms.topic: sample
author: MashaMSFT
ms.author: mathoma
ms.reviewer: carlrab
ms.date: 07/16/2019
ms.openlocfilehash: e50877f6f3194885b139683fe865144384716b48
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "73691763"
---
# <a name="use-powershell-to-add-an-azure-sql-database-managed-instance-to-a-failover-group"></a>Dodawanie Azure SQL Database wystąpienia zarządzanego do grupy trybu failover przy użyciu programu PowerShell 

Ten przykładowy skrypt programu PowerShell tworzy dwa wystąpienia zarządzane, dodaje je do grupy trybu failover, a następnie testuje tryb failover z podstawowego wystąpienia zarządzanego do pomocniczego wystąpienia zarządzanego. 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować program PowerShell i używać go lokalnie, ten samouczek wymaga AZ PowerShell 1.4.0 lub nowszego. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-az-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Connect-AzAccount`, aby utworzyć połączenie z platformą Azure.

## <a name="sample-scripts"></a>Przykładowe skrypty

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/failover-groups/add-managed-instance-to-failover-group-az-ps.ps1 "Add managed instance to a failover group")]

## <a name="clean-up-deployment"></a>Czyszczenie wdrożenia

Użyj poniższego polecenia, aby usunąć grupę zasobów i wszystkie skojarzone z nią zasoby. Należy dwukrotnie usunąć grupę zasobów. Usunięcie grupy zasobów po raz pierwszy spowoduje usunięcie wystąpienia zarządzanego i klastrów wirtualnych, ale zakończy się niepowodzeniem z komunikatem `Remove-AzResourceGroup : Long running operation failed with status 'Conflict'.`o błędzie. Uruchom polecenie Remove-AzResourceGroup po raz drugi, aby usunąć wszystkie zasoby pozostałe oraz grupę zasobów.

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
```

## <a name="script-explanation"></a>Objaśnienia dla skryptu

W tym skrypcie użyto następujących poleceń. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Tworzy grupę zasobów platformy Azure.  |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Tworzy sieć wirtualną.  |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | Dodaje konfigurację podsieci do sieci wirtualnej. | 
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) | Pobiera sieć wirtualną w grupie zasobów. | 
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | Pobiera podsieć w sieci wirtualnej. | 
| [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) | Tworzy sieciową grupę zabezpieczeń. | 
| [New-AzRouteTable](/powershell/module/az.network/new-azroutetable) | Tworzy tabelę tras. |
| [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) | Aktualizuje konfigurację podsieci dla sieci wirtualnej.  |
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | Aktualizuje sieć wirtualną.  |
| [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup) | Pobiera sieciową grupę zabezpieczeń. |
| [Add-AzNetworkSecurityRuleConfig](/powershell/module/az.network/add-aznetworksecurityruleconfig)| Dodaje konfigurację reguły zabezpieczeń sieci do sieciowej grupy zabezpieczeń. |
| [Set-AzNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup) | Aktualizuje sieciową grupę zabezpieczeń.  | 
| [Add-AzRouteConfig](/powershell/module/az.network/add-azrouteconfig) | Dodaje trasę do tabeli tras. |
| [Set-AzRouteTable](/powershell/module/az.network/set-azroutetable) | Aktualizuje tabelę tras.  |
| [New-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance) | Tworzy Azure SQL Database wystąpienie zarządzane.  |
| [Get-AzSqlInstance](/powershell/module/az.sql/get-azsqlinstance)| Zwraca informacje o wystąpieniu zarządzanej bazy danych Azure SQL. |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Tworzy publiczny adres IP.  | 
| [New-AzVirtualNetworkGatewayIpConfig](/powershell/module/az.network/new-azvirtualnetworkgatewayipconfig) | Tworzy konfigurację adresu IP bramy Virtual Network |
| [New-AzVirtualNetworkGateway](/powershell/module/az.network/new-azvirtualnetworkgateway) | Tworzy bramę Virtual Network |
| [New-AzVirtualNetworkGatewayConnection](/powershell/module/az.network/new-azvirtualnetworkgatewayconnection) | Tworzy połączenie między dwoma bramami sieci wirtualnej.   |
| [New-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/new-azsqldatabaseinstancefailovergroup)| Tworzy nową grupę Azure SQL Database trybu failover wystąpienia zarządzanego.  |
| [Get-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/get-azsqldatabaseinstancefailovergroup) | Pobiera lub wyświetla grupy trybu failover wystąpienia zarządzanego.| 
| [Przełącznik-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/switch-azsqldatabaseinstancefailovergroup) | Wykonuje tryb failover grupy trybu failover wystąpienia zarządzanego. | 
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Usuwa grupę zasobów. | 

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat programu Azure PowerShell, zobacz [dokumentację programu Azure PowerShell](/powershell/azure/overview).

Więcej przykładowych skryptów programu PowerShell dla usługi SQL Database można znaleźć w [skryptach programu PowerShell dla usługi Azure SQL Database](../sql-database-powershell-samples.md).
