---
title: 'Interfejs wiersza polecenia platformy Azure: Tworzenie wystąpienia zarządzanego'
description: Przykładowy skrypt interfejsu wiersza polecenia platformy Azure służący do tworzenia wystąpienia zarządzanego w wystąpieniu zarządzanym Azure SQL
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: devx-track-azurecli
ms.devlang: azurecli
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 03/25/2019
ms.openlocfilehash: 54e1aa993f177a4d3bc255287ae8c2fb14cf65af
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "87497277"
---
# <a name="use-cli-to-create-an-azure-sql-managed-instance"></a>Tworzenie wystąpienia zarządzanego usługi Azure SQL przy użyciu interfejsu wiersza polecenia

Ten przykładowy skrypt interfejsu wiersza polecenia platformy Azure tworzy wystąpienie zarządzane Azure SQL w dedykowanej podsieci w ramach nowej sieci wirtualnej. Konfiguruje również tabelę tras i grupę zabezpieczeń sieci dla sieci wirtualnej. Po pomyślnym uruchomieniu skryptu można uzyskać dostęp do wystąpienia zarządzanego z poziomu sieci wirtualnej lub ze środowiska lokalnego. Zobacz [Konfigurowanie maszyny wirtualnej platformy Azure, aby nawiązać połączenie z wystąpieniem zarządzanym usługi Azure SQL].. /.. /Azure-SQL/Managed-instance/Connect-VM-instance-Configure.MD) i [Skonfiguruj połączenie typu punkt-lokacja z wystąpieniem zarządzanym usługi Azure SQL w środowisku lokalnym](../../azure-sql/managed-instance/point-to-site-p2s-configure.md).

> [!IMPORTANT]
> Aby uzyskać ograniczenia, zobacz [Obsługiwane regiony](../../azure-sql/managed-instance/resource-limits.md#supported-regions) i [obsługiwane typy subskrypcji](../../azure-sql/managed-instance/resource-limits.md#supported-subscription-types).

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

| Polecenie | Opis |
|---|---|
| [AZ Network VNET](/cli/azure/network/vnet) | Polecenia sieci wirtualnej. |
| [AZ Network VNET Subnet](/cli/azure/network/vnet/subnet) | Polecenia podsieci sieci wirtualnej. |
| [AZ Network Route-Table](/cli/azure/network/route-table) | Polecenia tabeli tras sieciowych. |
| [AZ SQL mi](/cli/azure/sql/mi) | Polecenia wystąpienia zarządzanego SQL. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](/cli/azure).

Więcej przykładowych skryptów interfejsu wiersza polecenia dla usługi SQL Database można znaleźć w [dokumentacji usługi Azure SQL Database](../../azure-sql/database/az-cli-script-samples-content-guide.md).
