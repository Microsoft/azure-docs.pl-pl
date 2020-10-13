---
title: Konfigurowanie parametrów-Azure Database for PostgreSQL — elastyczny serwer
description: W tym artykule opisano sposób konfigurowania parametrów Postgres na serwerze elastycznym Azure Database for PostgreSQL przy użyciu interfejsu wiersza polecenia platformy Azure.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.devlang: azurecli
ms.topic: how-to
ms.date: 9/22/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 43b14858cc99cac41e277b03171fd4cac4d6eafa
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90940735"
---
# <a name="customize-server-parameters-for-azure-database-for-postgresql---flexible-server-using-azure-cli"></a>Dostosowywanie parametrów serwera dla Azure Database for PostgreSQL-elastyczny serwer przy użyciu interfejsu wiersza polecenia platformy Azure

Można wyświetlić, wyświetlić i zaktualizować parametry konfiguracji dla serwera usługi Azure PostgreSQL za pomocą interfejsu wiersza polecenia (CLI). Podzestaw parametrów aparatu jest uwidoczniony na poziomie serwera i można go modyfikować. 

## <a name="prerequisites"></a>Wymagania wstępne

Aby krokowo poprowadzić ten przewodnik, musisz:
- Utwórz serwer Azure Database for PostgreSQL i bazę danych, wykonując następujące czynności: [utwórz Azure Database for PostgreSQL](quickstart-create-server-cli.md)
- Zainstaluj interfejs wiersza polecenia [platformy Azure](/cli/azure/install-azure-cli) na maszynie lub Użyj [Azure Cloud Shell](../../cloud-shell/overview.md) w Azure Portal za pomocą przeglądarki.

## <a name="list-server-parameters-for-a-flexible-server"></a>Wyświetlanie listy parametrów serwera dla elastycznego serwera

Aby wyświetlić listę wszystkich modyfikowalnych parametrów na serwerze i ich wartości, uruchom polecenie [AZ Postgres elastyczny-Server Parameter List](/cli/azure/postgres/flexible-server/parameter) .

Dla serwera **mydemoserver.Postgres.Database.Azure.com** można wyświetlić listę parametrów serwera w **obszarze Grupa zasobów**.

```azurecli-interactive
az postgres flexible-server parameter list --resource-group myresourcegroup --server-name mydemoserver
```

## <a name="show-server-parameter-details"></a>Pokaż szczegóły parametru serwera

Aby wyświetlić szczegóły dotyczące określonego parametru dla serwera, uruchom polecenie [AZ Postgres elastyczny-Server Parameter show](/cli/azure/postgres/flexible-server/parameter)  .

W tym przykładzie przedstawiono szczegóły parametru **serwera \_ \_ komunikatów min dziennika** dla serwera **mydemoserver.Postgres.Database.Azure.com** w obszarze Grupa zasobów **.**

```azurecli-interactive
az postgres flexible-server parameter show --name log_min_messages --resource-group myresourcegroup --server-name mydemoserver
```

## <a name="modify-server-parameter-value"></a>Modyfikuj wartość parametru serwera

Można również zmodyfikować wartość określonego parametru serwera, który aktualizuje podstawową wartość konfiguracyjną dla aparatu serwera PostgreSQL. Aby zaktualizować parametr, użyj polecenia [AZ Postgres elastyczny-Server Parameter Set](/cli/azure/postgres/flexible-server/parameter) . 

Aby zaktualizować parametr **serwera \_ \_ komunikatów min dziennika** **mydemoserver.Postgres.Database.Azure.com** serwera w obszarze Grupa zasobów **.**

```azurecli-interactive
az postgres flexible-server parameter set --name log_min_messages --value INFO --resource-group myresourcegroup --server-name mydemoserver
```

Jeśli chcesz zresetować wartość parametru, po prostu wybierz opcję opuszczenia parametru opcjonalnego `--value` , a usługa stosuje wartość domyślną. W powyższym przykładzie będzie wyglądać następująco:

```azurecli-interactive
az postgres flexible-server parameter set --name log_min_messages --resource-group myresourcegroup --server-name mydemoserver
```

To polecenie resetuje parametr ** \_ min log \_ messages** do wartości domyślnej **Ostrzeżenie**. Aby uzyskać więcej informacji o parametrach serwera i dozwolonych wartościach, zobacz dokumentację PostgreSQL dotyczącą [ustawiania parametrów](https://www.postgresql.org/docs/12/config-setting.html).

## <a name="next-steps"></a>Następne kroki

- Aby skonfigurować i uzyskać dostęp do dzienników serwera, zobacz [Dzienniki serwera w Azure Database for PostgreSQL](concepts-logging.md)
