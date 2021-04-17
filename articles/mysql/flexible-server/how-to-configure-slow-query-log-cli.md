---
title: Konfigurowanie dzienników inspekcji i dzienników wolnych zapytań za pomocą interfejsu wiersza polecenia platformy Azure — Azure Database for MySQL — elastyczny serwer
description: W tym artykule opisano sposób konfigurowania dzienników wolnych zapytań i uzyskiwania do nich dostępu Azure Database for MySQL serwerze elastycznym z interfejsu wiersza polecenia platformy Azure.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 03/30/2021
ms.openlocfilehash: b42aba84dcbff795ee4ca1f8d622527e8039f27a
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107509143"
---
# <a name="configure-slow-query-logs-for-azure-database-for-mysql---flexible-server-using-the-azure-cli"></a>Konfigurowanie dzienników wolnych zapytań dla Azure Database for MySQL — elastyczny serwer przy użyciu interfejsu wiersza polecenia platformy Azure

> [!IMPORTANT]
> Azure Database for MySQL — serwer elastyczny jest obecnie w publicznej wersji zapoznawczej.

W tym artykule pokazano, jak skonfigurować [dzienniki wolnych zapytań](concepts-slow-query-logs.md) dla serwera elastycznego MySQL przy użyciu interfejsu wiersza polecenia platformy Azure. 

## <a name="prerequisites"></a>Wymagania wstępne
- Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.
- Zainstaluj interfejs wiersza polecenia platformy Azure lub uaktualnij go do najnowszej wersji. Zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure.](/cli/azure/install-azure-cli)
-  Zaloguj się do konta platformy Azure przy użyciu [polecenia az login.](/cli/azure/reference-index#az-login) **Zanotuj właściwość id,** która odwołuje się do **identyfikatora subskrypcji** konta platformy Azure.

    ```azurecli-interactive
    az login
    ````

- Jeśli masz wiele subskrypcji, wybierz odpowiednią subskrypcję, w której chcesz utworzyć serwer, używając ```az account set``` polecenia .
`
    ```azurecli
    az account set --subscription <subscription id>
    ```

- Utwórz serwer elastyczny MySQL, jeśli jeszcze go nie utworzono za pomocą ```az mysql flexible-server create``` polecenia .

    ```azurecli
    az mysql flexible-server create --resource-group myresourcegroup --name myservername
    ```

## <a name="configure-slow-query-logs"></a>Konfigurowanie dzienników wolnych zapytań

>[!IMPORTANT]
> Zaleca się rejestrowanie tylko typów zdarzeń i użytkowników wymaganych do celów inspekcji, aby upewnić się, że wydajność serwera nie ma w dużym stopniu wpływu.

Włącz i skonfiguruj dzienniki wolnych zapytań dla serwera.

```azurecli
# Turn on statement level log
az mysql flexible-server parameter set \
--name log_statement \
--resource-group myresourcegroup \
--server-name mydemoserver \
--value all


# Set log_min_duration_statement time to 10 sec
# This setting will log all queries executing for more than 10 sec. Please adjust this threshold based on your definition for slow queries
az mysql server configuration set \
--name log_min_duration_statement \
--resource-group myresourcegroup \
--server mydemoserver \
--value 10000

# Enable Slow query logs
az mysql flexible-server parameter set \
--name slow_query_log \
--resource-group myresourcegroup \
--server-name mydemoserver \
--value ON

```

## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o [dziennikach wolnych zapytań](concepts-slow-query-logs.md)
