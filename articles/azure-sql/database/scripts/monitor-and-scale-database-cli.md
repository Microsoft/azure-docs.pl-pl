---
title: 'Interfejs wiersza polecenia platformy Azure: monitorowanie i skalowanie pojedynczej bazy danych w Azure SQL Database'
description: Użyj przykładowego skryptu interfejsu wiersza polecenia platformy Azure, aby monitorować i skalować pojedynczą bazę danych w Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: sqldbrb=1, devx-track-azurecli
ms.devlang: azurecli
ms.topic: sample
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: sstein
ms.date: 06/25/2019
ms.openlocfilehash: bd982ab6dc66674e705f080511282bcfeb909872
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107787183"
---
# <a name="use-the-azure-cli-to-monitor-and-scale-a-single-database-in-azure-sql-database"></a>Używanie interfejsu wiersza polecenia platformy Azure do monitorowania i skalowania pojedynczej bazy danych w Azure SQL Database

[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqldb.md)]

Ten przykładowy skrypt interfejsu wiersza polecenia platformy Azure skaluje pojedynczą bazę danych w Azure SQL Database do innego rozmiaru obliczeniowego po zapytaniu o informacje o rozmiarze bazy danych.

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia platformy Azure i używać go lokalnie, ten artykuł wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie interfejsu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Przykładowy skrypt

### <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

```azurecli-interactive
$subscription = "<subscriptionId>" # add subscription here

az account set -s $subscription # ...or use 'az login'
```

### <a name="run-the-script"></a>Uruchamianie skryptu

[!code-azurecli-interactive[main](../../../../cli_scripts/sql-database/monitor-and-scale-database/monitor-and-scale-database.sh "Monitor and scale a database in Azure SQL Database")]

> [!TIP]
> Użyj [polecenia az sql db op list,](/cli/azure/sql/db/op?#az_sql_db_op_list) aby uzyskać listę operacji wykonywanych na bazie danych, a następnie użyj polecenia az sql db op [cancel,](/cli/azure/sql/db/op#az_sql_db_op_cancel) aby anulować operację aktualizacji bazy danych.

### <a name="clean-up-deployment"></a>Czyszczenie wdrożenia

Użyj następującego polecenia, aby usunąć grupę zasobów i wszystkie skojarzone z nią zasoby.

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>Przykładowa odwołanie

W tym skrypcie użyto następujących poleceń. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Skrypt | Opis |
|---|---|
| [az sql server](/cli/azure/sql/server) | Polecenia serwera. |
| [az sql db show-usage](/cli/azure/sql#az_sql_show_usage) | Pokazuje informacje o użyciu rozmiaru dla bazy danych. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](/cli/azure).

Więcej przykładowych skryptów interfejsu wiersza polecenia można znaleźć w [przykładowych skryptach interfejsu wiersza polecenia platformy Azure.](../az-cli-script-samples-content-guide.md)
