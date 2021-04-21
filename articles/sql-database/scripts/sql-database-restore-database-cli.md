---
title: 'Interfejs wiersza polecenia platformy Azure: przywracanie kopii zapasowej'
description: Przykładowy skrypt interfejsu wiersza polecenia platformy Azure do przywracania bazy danych Azure SQL Database do wcześniejszego punktu w czasie z automatycznych kopii zapasowych.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: devx-track-azurecli
ms.devlang: azurecli
ms.topic: sample
author: mashamsft
ms.author: mathoma
ms.reviewer: carlrab
ms.date: 03/27/2019
ms.openlocfilehash: f4054d19ccf4d136e30896bc066e51c92a4a4ad9
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107783471"
---
# <a name="use-cli-to-restore-a-single-database-in-azure-sql-database-to-an-earlier-point-in-time"></a>Przywracanie pojedynczej bazy danych w Azure SQL Database do wcześniejszego punktu w czasie przy użyciu interfejsu wiersza polecenia

Ten przykład interfejsu wiersza polecenia platformy Azure przywraca pojedynczą bazę danych Azure SQL Database do określonego punktu w czasie.  

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten artykuł będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.0 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie interfejsu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli).

## <a name="sample-script"></a>Przykładowy skrypt

### <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

```azurecli-interactive
$subscription = "<subscriptionId>" # add subscription here

az account set -s $subscription # ...or use 'az login'
```

### <a name="run-the-script"></a>Uruchamianie skryptu

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/restore-database/restore-database.sh "Restore SQL Database")]

### <a name="clean-up-deployment"></a>Czyszczenie wdrożenia

Użyj następującego polecenia, aby usunąć grupę zasobów i wszystkie skojarzone z nią zasoby.

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>Przykładowa odwołanie

W tym skrypcie użyto następujących poleceń. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Opis |
|---|---|
| [az sql db restore](/cli/azure/sql/db#az_sql_db_restore) | Polecenie Przywróć bazę danych. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](/cli/azure).

Więcej przykładowych skryptów interfejsu wiersza polecenia dla usługi SQL Database można znaleźć w [dokumentacji usługi Azure SQL Database](../../azure-sql/database/az-cli-script-samples-content-guide.md).
