---
title: 'Interfejs wiersza polecenia platformy Azure: dodawanie wystąpienia zarządzanego do grupy trybu failover'
description: Dowiedz się, jak utworzyć dwa wystąpienia zarządzane, dodać je do grupy trybu failover, a następnie przetestować tryb failover.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: devx-track-azurecli
ms.devlang: azurecli
ms.topic: sample
author: MashaMSFT
ms.author: mathoma
ms.reviewer: carlrab
ms.date: 07/16/2019
ms.openlocfilehash: afefc556bcac096d67051f3014c31e449dbbca32
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91323570"
---
# <a name="use-cli-to-create-an-azure-sql-managed-instance-to-a-failover-group"></a>Tworzenie wystąpienia zarządzanego usługi Azure SQL w grupie trybu failover przy użyciu interfejsu wiersza polecenia

Ten przykład interfejsu wiersza polecenia platformy Azure tworzy dwa wystąpienia zarządzane, dodaje je do grupy trybu failover, a następnie testuje tryb failover z podstawowego wystąpienia zarządzanego do pomocniczego wystąpienia zarządzanego.

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten artykuł będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.0 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie interfejsu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

## <a name="sample-scripts"></a>Przykładowe skrypty

### <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

### <a name="run-the-script"></a>Uruchamianie skryptu

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/failover-groups/add-managed-instance-to-failover-group-az-cli.sh "Add managed instance to a failover group")]

### <a name="clean-up-deployment"></a>Czyszczenie wdrożenia

Użyj poniższego polecenia, aby usunąć grupę zasobów i wszystkie skojarzone z nią zasoby. Należy dwukrotnie usunąć grupę zasobów. Usunięcie grupy zasobów po raz pierwszy spowoduje usunięcie wystąpienia zarządzanego i klastrów wirtualnych, ale zakończy się niepowodzeniem z komunikatem o błędzie `az group delete : Long running operation failed with status 'Conflict'.` . Uruchom polecenie AZ Group DELETE po raz drugi, aby usunąć wszystkie zasoby pozostałe oraz grupę zasobów.

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>Przykładowe odwołanie

W tym skrypcie użyto następujących poleceń. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Opis |
|---|---|
| [AZ Network VNET](/cli/azure/network/vnet) | Polecenia sieci wirtualnej.  |
| [AZ Network VNET Subnet](/cli/azure/network/vnet/subnet) | Polecenia podsieci sieci wirtualnej. |
| [AZ Network sieciowej grupy zabezpieczeń](/cli/azure/network/nsg) | Polecenia sieciowe grupy zabezpieczeń. |
| [AZ Network sieciowej grupy zabezpieczeń Rule](/cli/azure/network/nsg/rule)| Polecenia reguły zabezpieczeń sieci. |
| [AZ Network Route-Table](/cli/azure/network/route-table) | Kierowanie poleceń tabeli. |
| [AZ SQL mi](/cli/azure/sql/mi) | Polecenia wystąpienia zarządzanego SQL. |
| [AZ Network Public-IP](/cli/azure/network/public-ip) | Polecenia sieciowego publicznego adresu IP. |
| [AZ Network VNET-Gateway](/cli/azure/network/vnet-gateway) | Polecenia bramy Virtual Network |
| [AZ SQL instance-failover-Group](/cli/azure/sql/instance-failover-group) | Polecenia grupy trybu failover wystąpienia zarządzanego SQL. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](/cli/azure).

Więcej przykładowych skryptów interfejsu wiersza polecenia dla usługi SQL Database można znaleźć w [dokumentacji usługi Azure SQL Database](../../azure-sql/database/az-cli-script-samples-content-guide.md).
