---
title: Przykład programu PowerShell — Tworzenie wystąpienia zarządzanego w Azure SQL Database
description: Azure PowerShell przykładowy skrypt do tworzenia wystąpienia zarządzanego w programie Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: PowerShell
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 03/25/2019
ms.openlocfilehash: a4ba4d69b560307e52c277f5c695bbba718cd1d3
ms.sourcegitcommit: ac4a365a6c6ffa6b6a5fbca1b8f17fde87b4c05e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2020
ms.locfileid: "83005826"
---
# <a name="use-powershell-to-create-an-azure-sql-database-managed-instance"></a>Tworzenie Azure SQL Database wystąpienia zarządzanego przy użyciu programu PowerShell

Ten przykładowy skrypt programu PowerShell tworzy Azure SQL Database wystąpienie zarządzane w dedykowanej podsieci w ramach nowej sieci wirtualnej. Konfiguruje również tabelę tras i grupę zabezpieczeń sieci dla sieci wirtualnej. Po pomyślnym uruchomieniu skryptu można uzyskać dostęp do wystąpienia zarządzanego z poziomu sieci wirtualnej lub ze środowiska lokalnego. Zobacz [Konfigurowanie maszyny wirtualnej platformy Azure, aby nawiązać połączenie z wystąpieniem zarządzanym Azure SQL Database](../sql-database-managed-instance-configure-vm.md) i [skonfigurować połączenie punkt-lokacja z wystąpieniem Azure SQL Database zarządzanym w środowisku lokalnym](../sql-database-managed-instance-configure-p2s.md).

> [!IMPORTANT]
> Aby uzyskać ograniczenia, zobacz [Obsługiwane regiony](../sql-database-managed-instance-resource-limits.md#supported-regions) i [obsługiwane typy subskrypcji](../sql-database-managed-instance-resource-limits.md#supported-subscription-types).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować program PowerShell i używać go lokalnie, ten samouczek wymaga AZ PowerShell 1.4.0 lub nowszego. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-az-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Connect-AzAccount`, aby utworzyć połączenie z platformą Azure.

## <a name="sample-script"></a>Przykładowy skrypt

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/managed-instance/create-and-configure-managed-instance.ps1 "Create managed instance")]

## <a name="clean-up-deployment"></a>Czyszczenie wdrożenia

Użyj poniższego polecenia, aby usunąć grupę zasobów i wszystkie skojarzone z nią zasoby.

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourcegroupname
```

## <a name="script-explanation"></a>Objaśnienia dla skryptu

Ten skrypt używa następujących poleceń. Aby uzyskać więcej informacji o używanych i innych poleceniach w poniższej tabeli, kliknij link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Tworzy grupę zasobów, w której są przechowywane wszystkie zasoby.
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Tworzy sieć wirtualną |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/Add-AzVirtualNetworkSubnetConfig) | Dodaje konfigurację podsieci do sieci wirtualnej |
| [Get-AzVirtualNetwork](/powershell/module/az.network/Get-AzVirtualNetwork) | Pobiera sieć wirtualną w grupie zasobów |
| [Set-AzVirtualNetwork](/powershell/module/az.network/Set-AzVirtualNetwork) | Ustawia stan celu dla sieci wirtualnej |
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/Get-AzVirtualNetworkSubnetConfig) | Pobiera podsieć w sieci wirtualnej |
| [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/Set-AzVirtualNetworkSubnetConfig) | Konfiguruje stan celu dla konfiguracji podsieci w sieci wirtualnej |
| [New-AzRouteTable](/powershell/module/az.network/New-AzRouteTable) | Tworzy tabelę tras |
| [Get-AzRouteTable](/powershell/module/az.network/Get-AzRouteTable) | Pobiera tabele tras |
| [Set-AzRouteTable](/powershell/module/az.network/Set-AzRouteTable) | Ustawia stan celu dla tabeli tras |
| [New-AzSqlInstance](/powershell/module/az.sql/New-AzSqlInstance) | Tworzy wystąpienie zarządzane Azure SQL Database |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Usuwa grupę zasobów wraz ze wszystkimi zagnieżdżonymi zasobami. |
|||

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat programu Azure PowerShell, zobacz [dokumentację programu Azure PowerShell](/powershell/azure/overview).

Więcej przykładowych skryptów programu PowerShell dla usługi SQL Database można znaleźć w [skryptach programu PowerShell dla usługi Azure SQL Database](../sql-database-powershell-samples.md).
