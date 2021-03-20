---
title: 'PowerShell: Tworzenie wystąpienia zarządzanego'
titleSuffix: Azure SQL Managed Instance
description: Ten artykuł zawiera przykładowy skrypt Azure PowerShell tworzenia wystąpienia zarządzanego.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: PowerShell
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/25/2019
ms.openlocfilehash: fd091cedb67c07b7de2c7e8540e99c3e8daf7dcf
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91323817"
---
# <a name="use-powershell-to-create-a-managed-instance"></a>Tworzenie wystąpienia zarządzanego przy użyciu programu PowerShell

[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqlmi.md)]

Ten przykładowy skrypt programu PowerShell tworzy wystąpienie zarządzane w dedykowanej podsieci w ramach nowej sieci wirtualnej. Konfiguruje również tabelę tras i grupę zabezpieczeń sieci dla sieci wirtualnej. Po pomyślnym uruchomieniu skryptu można uzyskać dostęp do wystąpienia zarządzanego z poziomu sieci wirtualnej lub ze środowiska lokalnego. Zobacz [Konfigurowanie maszyny wirtualnej platformy Azure, aby nawiązać połączenie z wystąpieniem zarządzanym Azure SQL Database](../connect-vm-instance-configure.md) i [skonfigurować połączenie punkt-lokacja z wystąpieniem zarządzanym usługi Azure SQL z lokalnego](../point-to-site-p2s-configure.md).

> [!IMPORTANT]
> Aby uzyskać ograniczenia, zobacz [Obsługiwane regiony](../resource-limits.md#supported-regions) i [obsługiwane typy subskrypcji](../resource-limits.md#supported-subscription-types).

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować program PowerShell i używać go lokalnie, ten samouczek wymaga Azure PowerShell 1.4.0 lub nowszego. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-az-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Connect-AzAccount`, aby utworzyć połączenie z platformą Azure.

## <a name="sample-script"></a>Przykładowy skrypt

[!code-powershell-interactive[main](../../../../powershell_scripts/sql-database/managed-instance/create-and-configure-managed-instance.ps1 "Create managed instance")]

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
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Tworzy sieć wirtualną. |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/Add-AzVirtualNetworkSubnetConfig) | Dodaje konfigurację podsieci do sieci wirtualnej. |
| [Get-AzVirtualNetwork](/powershell/module/az.network/Get-AzVirtualNetwork) | Pobiera sieć wirtualną w grupie zasobów. |
| [Set-AzVirtualNetwork](/powershell/module/az.network/Set-AzVirtualNetwork) | Ustawia stan celu dla sieci wirtualnej. |
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/Get-AzVirtualNetworkSubnetConfig) | Pobiera podsieć w sieci wirtualnej. |
| [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/Set-AzVirtualNetworkSubnetConfig) | Konfiguruje stan celu dla konfiguracji podsieci w sieci wirtualnej. |
| [New-AzRouteTable](/powershell/module/az.network/New-AzRouteTable) | Tworzy tabelę tras. |
| [Get-AzRouteTable](/powershell/module/az.network/Get-AzRouteTable) | Pobiera tabele tras. |
| [Set-AzRouteTable](/powershell/module/az.network/Set-AzRouteTable) | Ustawia stan celu dla tabeli tras. |
| [New-AzSqlInstance](/powershell/module/az.sql/New-AzSqlInstance) | Tworzy wystąpienie zarządzane. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Usuwa grupę zasobów wraz ze wszystkimi zagnieżdżonymi zasobami. |
|||

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat Azure PowerShell, zobacz [dokumentację dotyczącą Azure PowerShell](/powershell/azure/).

Dodatkowe przykłady skryptów programu PowerShell dla wystąpienia zarządzanego usługi Azure SQL można znaleźć w [skryptach programu PowerShell wystąpienia zarządzanego Azure SQL](../../database/powershell-script-content-guide.md).
