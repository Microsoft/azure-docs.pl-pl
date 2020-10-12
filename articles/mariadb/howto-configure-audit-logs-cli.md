---
title: Dzienniki inspekcji dostępu — interfejs wiersza polecenia platformy Azure — Azure Database for MariaDB
description: W tym artykule opisano sposób konfigurowania i uzyskiwania dostępu do dzienników inspekcji w programie Azure Database for MariaDB z poziomu interfejsu wiersza polecenia platformy Azure.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: how-to
ms.date: 6/24/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 0aba88c10304cf7d87277ad851ae38eae8eb5bf3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87497124"
---
# <a name="configure-and-access-azure-database-for-maria-db-audit-logs-in-the-azure-cli"></a>Konfigurowanie i uzyskiwanie dostępu do dzienników inspekcji bazy danych Azure Database for Maria DB w interfejsie wiersza polecenia platformy Azure

[Dzienniki inspekcji Azure Database for MariaDB](concepts-audit-logs.md) można skonfigurować przy użyciu interfejsu wiersza polecenia platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne

Aby krokowo poprowadzić ten przewodnik, musisz:

- [Serwer Azure Database for MariaDB](quickstart-create-mariadb-server-database-using-azure-portal.md)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!IMPORTANT]
> Ten przewodnik zawiera informacje wymagające użycia interfejsu wiersza polecenia platformy Azure w wersji 2,0 lub nowszej. Aby potwierdzić wersję, w wierszu polecenia platformy Azure wpisz polecenie `az --version` . Aby uzyskać informacje o instalowaniu lub uaktualnianiu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli).

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
