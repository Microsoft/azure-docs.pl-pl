---
title: Konfigurowanie parametrów-Azure Database for PostgreSQL — pojedynczy serwer
description: W tym artykule opisano sposób konfigurowania parametrów Postgres na pojedynczym serwerze Azure Database for PostgreSQL przy użyciu interfejsu wiersza polecenia platformy Azure.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.devlang: azurecli
ms.topic: how-to
ms.date: 06/19/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: cadf21423ac7eb997db5bb42005ee307d9813331
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105604196"
---
# <a name="customize-server-configuration-parameters-for-azure-database-for-postgresql---single-server-using-azure-cli"></a>Dostosowywanie parametrów konfiguracji serwera dla Azure Database for PostgreSQL-pojedynczego serwera przy użyciu interfejsu wiersza polecenia platformy Azure
Można wyświetlić, wyświetlić i zaktualizować parametry konfiguracji dla serwera usługi Azure PostgreSQL za pomocą interfejsu wiersza polecenia (CLI). Podzestaw konfiguracji aparatu jest narażony na poziomie serwera i można go modyfikować. 

## <a name="prerequisites"></a>Wymagania wstępne
Aby krokowo poprowadzić ten przewodnik, musisz:
- Utwórz serwer Azure Database for PostgreSQL i bazę danych, wykonując następujące czynności: [utwórz Azure Database for PostgreSQL](quickstart-create-server-database-azure-cli.md)
- Zainstaluj interfejs wiersza polecenia [platformy Azure](/cli/azure/install-azure-cli) na maszynie lub Użyj [Azure Cloud Shell](../cloud-shell/overview.md) w Azure Portal za pomocą przeglądarki.

## <a name="list-server-configuration-parameters-for-azure-database-for-postgresql-server"></a>Wyświetlanie listy parametrów konfiguracji serwera dla Azure Database for PostgreSQL Server
Aby wyświetlić listę wszystkich modyfikowalnych parametrów na serwerze i ich wartości, uruchom polecenie [AZ Postgres Server Configuration list](/cli/azure/postgres/server/configuration) .

Dla serwera **mydemoserver.Postgres.Database.Azure.com** można wyświetlić listę parametrów konfiguracji serwera w **obszarze Grupa zasobów**.
```azurecli-interactive
az postgres server configuration list --resource-group myresourcegroup --server mydemoserver
```
## <a name="show-server-configuration-parameter-details"></a>Pokaż szczegóły parametru konfiguracji serwera
Aby wyświetlić szczegóły dotyczące określonego parametru konfiguracji dla serwera, uruchom polecenie [AZ Postgres Server Configuration show](/cli/azure/postgres/server/configuration)  .

W tym przykładzie przedstawiono szczegóły parametru konfiguracja serwera **\_ \_ komunikatów min dziennika** dla serwera **mydemoserver.Postgres.Database.Azure.com** w obszarze Grupa zasobów **.**
```azurecli-interactive
az postgres server configuration show --name log_min_messages --resource-group myresourcegroup --server mydemoserver
```
## <a name="modify-server-configuration-parameter-value"></a>Modyfikuj wartość parametru konfiguracji serwera
Można również zmodyfikować wartość określonego parametru konfiguracji serwera, który aktualizuje podstawową wartość konfiguracyjną dla aparatu serwera PostgreSQL. Aby zaktualizować konfigurację, użyj polecenia [AZ Postgres Server Configuration Set](/cli/azure/postgres/server/configuration) . 

Aby zaktualizować parametr konfiguracja serwera **\_ minimalnych \_ komunikatów w dzienniku** **mydemoserver.Postgres.Database.Azure.com** w obszarze Grupa zasobów **.**
```azurecli-interactive
az postgres server configuration set --name log_min_messages --resource-group myresourcegroup --server mydemoserver --value INFO
```
Jeśli chcesz zresetować wartość parametru konfiguracji, po prostu wybierz opcję opuszczenia parametru opcjonalnego `--value` , a usługa zastosuje wartość domyślną. W powyższym przykładzie będzie wyglądać następująco:
```azurecli-interactive
az postgres server configuration set --name log_min_messages --resource-group myresourcegroup --server mydemoserver
```
To polecenie Resetuje konfigurację **\_ minimalnych \_ komunikatów dziennika** do wartości domyślnej **Ostrzeżenie**. Aby uzyskać więcej informacji na temat konfiguracji serwera i dozwolonych wartości, zobacz dokumentację PostgreSQL na [serwerze konfiguracji](https://www.postgresql.org/docs/9.6/static/runtime-config.html).

## <a name="next-steps"></a>Następne kroki
- [Dowiedz się, jak ponownie uruchomić serwer](howto-restart-server-cli.md)
- Aby skonfigurować i uzyskać dostęp do dzienników serwera, zobacz [Dzienniki serwera w Azure Database for PostgreSQL](concepts-server-logs.md)
