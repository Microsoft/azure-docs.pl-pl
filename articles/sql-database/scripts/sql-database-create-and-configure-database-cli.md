---
title: Przykład interfejsu wiersza polecenia — Tworzenie bazy danych Azure SQL Database
description: Ten przykładowy skrypt interfejsu wiersza polecenia platformy Azure umożliwia utworzenie bazy danych SQL.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: azurecli
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 06/25/2019
ms.openlocfilehash: ecfa61f0680e20dd37a1fedc988664afa6e2a83b
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80067457"
---
# <a name="use-cli-to-create-a-single-azure-sql-database-and-configure-a-firewall-rule"></a>Tworzenie jednej bazy danych Azure SQL Database i konfigurowanie reguły zapory przy użyciu interfejsu wiersza polecenia

Ten przykładowy skrypt interfejsu wiersza polecenia platformy Azure tworzy bazę danych Azure SQL Database i konfiguruje regułę zapory na poziomie serwera. Po pomyślnym wykonaniu tego skryptu można uzyskać dostęp do bazy danych SQL z poziomu wszystkich usług platformy Azure przy użyciu skonfigurowanego adresu IP.

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten temat będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.0 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie interfejsu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli).

## <a name="sample-script"></a>Przykładowy skrypt

### <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

### <a name="run-the-script"></a>Uruchamianie skryptu

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/create-and-configure-database/create-and-configure-database.sh "Create SQL Database")]

### <a name="clean-up-deployment"></a>Czyszczenie wdrożenia

Użyj poniższego polecenia, aby usunąć grupę zasobów i wszystkie skojarzone z nią zasoby.

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>Przykładowe odwołanie

W tym skrypcie użyto następujących poleceń. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| | |
|---|---|
| [az sql server](/cli/azure/sql/server#az-sql-server-create) | Polecenia serwera |
| [AZ SQL Server firewall](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-create) | Polecenia zapory serwera. |
| [AZ SQL DB](/cli/azure/sql/db#az-sql-db-create) | Polecenia bazy danych. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](/cli/azure).

Więcej przykładowych skryptów interfejsu wiersza polecenia dla usługi SQL Database można znaleźć w [dokumentacji usługi Azure SQL Database](../sql-database-cli-samples.md).
