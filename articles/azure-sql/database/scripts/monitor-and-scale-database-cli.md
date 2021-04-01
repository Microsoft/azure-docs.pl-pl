---
title: 'Interfejs wiersza polecenia platformy Azure: monitorowanie i skalowanie pojedynczej bazy danych w Azure SQL Database'
description: Użyj przykładowego skryptu interfejsu wiersza polecenia platformy Azure do monitorowania i skalowania pojedynczej bazy danych w Azure SQL Database.
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
ms.openlocfilehash: 0a72bea08052e5cd17502a21a98b41cb5582a3b9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96493631"
---
# <a name="use-the-azure-cli-to-monitor-and-scale-a-single-database-in-azure-sql-database"></a>Używanie interfejsu wiersza polecenia platformy Azure do monitorowania i skalowania pojedynczej bazy danych w Azure SQL Database

[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqldb.md)]

Ten przykładowy skrypt interfejsu wiersza polecenia platformy Azure skaluje pojedynczą bazę danych w Azure SQL Database do innego rozmiaru obliczeniowego po wykonaniu zapytania o informacje o rozmiarze bazy danych.

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia platformy Azure i korzystać z niego lokalnie, ten artykuł będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2,0 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie interfejsu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

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
> Użyj [AZ SQL DB op list](/cli/azure/sql/db/op?#az-sql-db-op-list) , aby uzyskać listę operacji wykonywanych w bazie danych, i Użyj [AZ SQL DB op Cancel](/cli/azure/sql/db/op#az-sql-db-op-cancel) , aby anulować operację aktualizacji w bazie danych.

### <a name="clean-up-deployment"></a>Czyszczenie wdrożenia

Użyj poniższego polecenia, aby usunąć grupę zasobów i wszystkie skojarzone z nią zasoby.

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>Przykładowe odwołanie

W tym skrypcie użyto następujących poleceń. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Skrypt | Opis |
|---|---|
| [az sql server](/cli/azure/sql/server) | Polecenia serwera. |
| [az sql db show-usage](/cli/azure/sql#az-sql-show-usage) | Pokazuje informacje o użyciu rozmiaru dla bazy danych. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](/cli/azure).

Dodatkowe przykłady skryptów interfejsu wiersza polecenia można znaleźć w [przykładowych skryptach interfejsu wiersza polecenia platformy Azure](../az-cli-script-samples-content-guide.md).
