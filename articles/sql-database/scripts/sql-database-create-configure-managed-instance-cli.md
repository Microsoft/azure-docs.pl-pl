---
title: Przykład interfejsu wiersza polecenia — Tworzenie wystąpienia zarządzanego w Azure SQL Database
description: Przykładowy skrypt interfejsu wiersza polecenia platformy Azure do utworzenia wystąpienia zarządzanego w Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: azurecli
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 03/25/2019
ms.openlocfilehash: f52a460be33b23da3da0cb32075ab5f75bb8d129
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/21/2020
ms.locfileid: "83772628"
---
# <a name="use-cli-to-create-an-azure-sql-database-managed-instance"></a>Tworzenie Azure SQL Database wystąpienia zarządzanego przy użyciu interfejsu wiersza polecenia

Ten przykładowy skrypt interfejsu wiersza polecenia platformy Azure tworzy Azure SQL Database wystąpienie zarządzane w dedykowanej podsieci w ramach nowej sieci wirtualnej. Konfiguruje również tabelę tras i grupę zabezpieczeń sieci dla sieci wirtualnej. Po pomyślnym uruchomieniu skryptu można uzyskać dostęp do wystąpienia zarządzanego z poziomu sieci wirtualnej lub ze środowiska lokalnego. Zobacz [Konfigurowanie maszyny wirtualnej platformy Azure, aby nawiązać połączenie z wystąpieniem zarządzanym Azure SQL Database](../sql-database-managed-instance-configure-vm.md) i [skonfigurować połączenie punkt-lokacja z wystąpieniem Azure SQL Database zarządzanym w środowisku lokalnym](../sql-database-managed-instance-configure-p2s.md).

> [!IMPORTANT]
> Aby uzyskać ograniczenia, zobacz [Obsługiwane regiony](../sql-database-managed-instance-resource-limits.md#supported-regions) i [obsługiwane typy subskrypcji](../sql-database-managed-instance-resource-limits.md#supported-subscription-types).

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten artykuł będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.0 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie interfejsu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Przykładowy skrypt

### <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

### <a name="run-the-script"></a>Uruchamianie skryptu

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/managed-instance/create-managed-instance.sh "Create managed instance")]

### <a name="clean-up-deployment"></a>Czyszczenie wdrożenia

Użyj poniższego polecenia, aby usunąć grupę zasobów i wszystkie skojarzone z nią zasoby.

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>Przykładowe odwołanie

W tym skrypcie użyto następujących poleceń. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| | |
|---|---|
| [AZ Network VNET](/cli/azure/network/vnet) | Polecenia sieci wirtualnej. |
| [AZ Network VNET Subnet](/cli/azure/network/vnet/subnet) | Polecenia podsieci sieci wirtualnej. |
| [AZ Network Route-Table](/cli/azure/network/route-table) | Polecenia tabeli tras sieciowych. |
| [AZ SQL mi](/cli/azure/sql/mi) | Polecenia wystąpienia zarządzanego. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](/cli/azure).

Więcej przykładowych skryptów interfejsu wiersza polecenia dla usługi SQL Database można znaleźć w [dokumentacji usługi Azure SQL Database](../sql-database-cli-samples.md).
