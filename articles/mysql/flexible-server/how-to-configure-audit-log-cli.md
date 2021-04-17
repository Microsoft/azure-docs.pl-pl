---
title: Konfigurowanie dzienników inspekcji za pomocą interfejsu wiersza polecenia platformy Azure — Azure Database for MySQL — elastyczny serwer
description: W tym artykule opisano sposób konfigurowania dzienników inspekcji i uzyskiwania do nich dostępu Azure Database for MySQL serwerze elastycznym z interfejsu wiersza polecenia platformy Azure.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 03/30/2021
ms.openlocfilehash: 757d765f44f09eeb6f7a24644abeb1ce4577f664
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107509095"
---
# <a name="configure-and-access-audit-logs-for-azure-database-for-mysql---flexible-server-using-the-azure-cli"></a>Konfigurowanie dzienników inspekcji dla usługi Azure Database for MySQL — elastyczny serwer przy użyciu interfejsu wiersza polecenia platformy Azure

> [!IMPORTANT]
> Azure Database for MySQL — serwer elastyczny jest obecnie dostępny w publicznej wersji zapoznawczej.

W tym artykule pokazano, jak skonfigurować [dzienniki inspekcji](concepts-audit-logs.md) dla serwera elastycznego MySQL przy użyciu interfejsu wiersza polecenia platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne
- Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.
- Zainstaluj lub uaktualnij interfejs wiersza polecenia platformy Azure do najnowszej wersji. Zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure.](/cli/azure/install-azure-cli)
-  Zaloguj się do konta platformy Azure przy użyciu [polecenia az login.](/cli/azure/reference-index#az-login) **Zanotuj właściwość id,** która odwołuje się do **identyfikatora subskrypcji** dla twojego konta platformy Azure.

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

## <a name="configure-audit-logging"></a>Konfigurowanie rejestrowania inspekcji

>[!IMPORTANT]
> Zaleca się rejestrowanie tylko typów zdarzeń i użytkowników wymaganych do celów inspekcji, aby upewnić się, że wydajność serwera nie ma w dużym stopniu wpływu.

Włącz i skonfiguruj rejestrowanie inspekcji.

```azurecli
# Enable audit logs
az mysql flexible-server parameter set \
--name audit_log_enabled \
--resource-group myresourcegroup \
--server-name mydemoserver \
--value ON
```

## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o [dziennikach inspekcji](concepts-audit-logs.md)
