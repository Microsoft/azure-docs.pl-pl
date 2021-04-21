---
title: 'Interfejs wiersza polecenia platformy Azure: tworzenie pojedynczej bazy danych'
description: Użyj tego przykładowego skryptu interfejsu wiersza polecenia platformy Azure, aby utworzyć pojedynczą bazę danych.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: sqldbrb=1, devx-track-azurecli
ms.devlang: azurecli
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 06/25/2019
ms.openlocfilehash: 00346accbccd67f542a8496ac097b0236c56a08c
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107773823"
---
# <a name="use-the-azure-cli-to-create-a-single-database-and-configure-a-firewall-rule"></a>Tworzenie pojedynczej bazy danych i konfigurowanie reguły zapory przy użyciu interfejsu wiersza polecenia platformy Azure

[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqldb.md)]

Ten przykładowy skrypt interfejsu wiersza polecenia platformy Azure tworzy pojedynczą bazę danych w Azure SQL Database i konfiguruje regułę zapory na poziomie serwera. Po pomyślnym uruchomieniu skryptu można uzyskać dostęp do bazy danych ze wszystkich usług platformy Azure i skonfigurowanego adresu IP.

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten temat będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.0 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie interfejsu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli).

## <a name="sample-script"></a>Przykładowy skrypt

### <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

### <a name="run-the-script"></a>Uruchamianie skryptu

[!code-azurecli-interactive[main](../../../../cli_scripts/sql-database/create-and-configure-database/create-and-configure-database.sh "Create SQL Database")]

### <a name="clean-up-deployment"></a>Czyszczenie wdrożenia

Użyj następującego polecenia, aby usunąć grupę zasobów i wszystkie skojarzone z nią zasoby.

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>Przykładowa odwołanie

W tym skrypcie użyto następujących poleceń. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Opis |
|---|---|
| [az sql server](/cli/azure/sql/server#az_sql_server_create) | Polecenia serwera |
| [az sql server firewall](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_create) | Polecenia zapory serwera. |
| [az sql db](/cli/azure/sql/db#az_sql_db_create) | Polecenia bazy danych. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](/cli/azure).

Więcej przykładowych skryptów interfejsu wiersza polecenia dla usługi SQL Database można znaleźć w [dokumentacji usługi Azure SQL Database](../az-cli-script-samples-content-guide.md).
