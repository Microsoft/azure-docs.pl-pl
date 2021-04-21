---
title: 'Interfejs wiersza polecenia platformy Azure: importowanie pliku BACPAC do bazy danych w Azure SQL Database'
description: Przykładowy skrypt interfejsu wiersza polecenia platformy Azure do importowania pliku BACPAC do bazy danych w Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: load & move data, devx-track-azurecli
ms.devlang: azurecli
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 05/24/2019
ms.openlocfilehash: a76a2e72533068f37613d801e39f9451098b89e5
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107786607"
---
# <a name="use-cli-to-import-a-bacpac-file-into-a-database-in-sql-database"></a>Importowanie pliku BACPAC do bazy danych w programie przy użyciu interfejsu wiersza SQL Database

Ten przykładowy skrypt interfejsu wiersza polecenia platformy Azure importuje bazę danych z pliku *bacpac* do bazy danych w SQL Database.  

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten artykuł będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.0 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie interfejsu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Przykładowy skrypt

### <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

### <a name="run-the-script"></a>Uruchamianie skryptu

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/import-from-bacpac/import-from-bacpac.sh "Create SQL Database")]

### <a name="clean-up-deployment"></a>Czyszczenie wdrożenia

Użyj następującego polecenia, aby usunąć grupę zasobów i wszystkie skojarzone z nią zasoby.

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>Przykładowa odwołanie

W tym skrypcie użyto następujących poleceń. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Opis |
|---|---|
| [az sql server](/cli/azure/sql/server) | Polecenia serwera. |
| [az sql db import](/cli/azure/sql/db#az_sql_db_import) | Polecenie importu bazy danych. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](/cli/azure).

Więcej przykładowych skryptów interfejsu wiersza polecenia dla usługi SQL Database można znaleźć w [dokumentacji usługi Azure SQL Database](../../azure-sql/database/az-cli-script-samples-content-guide.md).
