---
title: Przykład interfejsu wiersza polecenia — włączanie BYOK TDE — wystąpienie zarządzane Azure SQL
description: Dowiedz się, jak skonfigurować wystąpienie zarządzane usługi Azure SQL, aby rozpocząć korzystanie z BYOK Transparent Data Encryption (TDE) na potrzeby szyfrowania w środowisku REST przy użyciu programu PowerShell.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: azurecli
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: vanto
ms.date: 11/05/2019
ms.openlocfilehash: 2b948161633569d629dfb048a7d7dee6a9946f43
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91323692"
---
# <a name="manage-transparent-data-encryption-in-a-managed-instance-using-your-own-key-from-azure-key-vault"></a>Zarządzanie funkcją Transparent Data Encryption w wystąpieniu zarządzanym przy użyciu własnego klucza z usługi Azure Key Vault

Ten przykładowy skrypt interfejsu wiersza polecenia platformy Azure konfiguruje Transparent Data Encryption (TDE) z kluczem zarządzanym przez klienta dla wystąpienia zarządzanego usługi Azure SQL przy użyciu klucza z Azure Key Vault. Jest to często określane jako Bring Your Own Key scenariusz dla TDE. Aby dowiedzieć się więcej na temat TDE z kluczem zarządzanym przez klienta, zobacz [TDE Bring Your Own Key do usługi Azure SQL](../../azure-sql/database/transparent-data-encryption-byok-overview.md).

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten artykuł będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.0 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie interfejsu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Przykładowy skrypt

### <a name="prerequisites"></a>Wymagania wstępne

Istniejące wystąpienie zarządzane, zobacz [Używanie interfejsu wiersza polecenia platformy Azure do tworzenia wystąpienia zarządzanego Azure SQL](sql-database-create-configure-managed-instance-cli.md).

### <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

```azurecli-interactive
$subscription = "<subscriptionId>" # add subscription here

az account set -s $subscription # ...or use 'az login'
```

### <a name="run-the-script"></a>Uruchamianie skryptu

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/transparent-data-encryption/setup-tde-byok-sqlmi.sh "Set up BYOK TDE for SQL Managed Instance")]

### <a name="clean-up-deployment"></a>Czyszczenie wdrożenia

Użyj poniższego polecenia, aby usunąć grupę zasobów i wszystkie skojarzone z nią zasoby.

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>Przykładowe odwołanie

W tym skrypcie użyto następujących poleceń. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Opis |
|---|---|
| [AZ SQL DB](/cli/azure/sql/db) | Polecenia bazy danych. |
| [AZ SQL failover-Group](/cli/azure/sql/failover-group) | Polecenia grupy trybu failover. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](/cli/azure).

Więcej przykładowych skryptów interfejsu wiersza polecenia dla usługi SQL Database można znaleźć w [dokumentacji usługi Azure SQL Database](../../azure-sql/database/az-cli-script-samples-content-guide.md).
