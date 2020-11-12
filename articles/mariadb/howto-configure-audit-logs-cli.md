---
title: Dzienniki inspekcji dostępu — interfejs wiersza polecenia platformy Azure — Azure Database for MariaDB
description: W tym artykule opisano sposób konfigurowania i uzyskiwania dostępu do dzienników inspekcji w programie Azure Database for MariaDB z poziomu interfejsu wiersza polecenia platformy Azure.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: how-to
ms.date: 6/24/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: c1d446d8ee2863077ad84c361876758336f5a3cb
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/12/2020
ms.locfileid: "94540981"
---
# <a name="configure-and-access-azure-database-for-maria-db-audit-logs-in-the-azure-cli"></a>Konfigurowanie i uzyskiwanie dostępu do dzienników inspekcji bazy danych Azure Database for Maria DB w interfejsie wiersza polecenia platformy Azure

[Dzienniki inspekcji Azure Database for MariaDB](concepts-audit-logs.md) można skonfigurować przy użyciu interfejsu wiersza polecenia platformy Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik:

- Potrzebny jest [serwer Azure Database for MariaDB](quickstart-create-mariadb-server-database-using-azure-portal.md).

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- Ten artykuł wymaga wersji 2,0 lub nowszej interfejsu wiersza polecenia platformy Azure. W przypadku korzystania z Azure Cloud Shell Najnowsza wersja jest już zainstalowana.

## <a name="configure-audit-logging"></a>Konfigurowanie rejestrowania inspekcji

>[!IMPORTANT]
> Zaleca się tylko rejestrowanie typów zdarzeń i użytkowników wymaganych do celów inspekcji, aby upewnić się, że wydajność serwera nie jest w dużym stopniu zagrożona.

Włącz i skonfiguruj rejestrowanie inspekcji, wykonując następujące czynności: 

1. Włącz dzienniki inspekcji, ustawiając parametr **audit_logs_enabled** na wartość "on". 
    ```azurecli-interactive
    az mariadb server configuration set --name audit_log_enabled --resource-group myresourcegroup --server mydemoserver --value ON
    ```

1. Wybierz [typy zdarzeń](concepts-audit-logs.md#configure-audit-logging) , które mają być rejestrowane, aktualizując parametr **audit_log_events** .
    ```azurecli-interactive
    az mariadb server configuration set --name audit_log_events --resource-group myresourcegroup --server mydemoserver --value "ADMIN,CONNECTION"
    ```

1. Dodaj wszystkich użytkowników MariaDB do wykluczenia z rejestrowania przez zaktualizowanie parametru **audit_log_exclude_users** . Określ użytkowników, podając ich nazwę użytkownika MariaDB.
    ```azurecli-interactive
    az mariadb server configuration set --name audit_log_exclude_users --resource-group myresourcegroup --server mydemoserver --value "azure_superuser"
    ```

1. Dodaj określonych użytkowników MariaDB do uwzględnienia w rejestrowaniu, aktualizując parametr **audit_log_include_users** . Określ użytkowników, podając ich nazwę użytkownika MariaDB.
    ```azurecli-interactive
    az mariadb server configuration set --name audit_log_include_users --resource-group myresourcegroup --server mydemoserver --value "sampleuser"
    ```

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [dziennikach inspekcji](concepts-audit-logs.md) w Azure Database for MariaDB
- Dowiedz się, jak skonfigurować dzienniki inspekcji w [Azure Portal](howto-configure-audit-logs-portal.md)
