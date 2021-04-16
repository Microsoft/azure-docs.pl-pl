---
title: Zarządzanie strefowo nadmiarową wysoką dostępnością — interfejs wiersza polecenia platformy Azure — Azure Database for MySQL elastyczny serwer
description: W tym artykule opisano sposób konfigurowania strefowo nadmiarowej wysokiej dostępności Azure Database for MySQL serwerze elastycznym za pomocą interfejsu wiersza polecenia platformy Azure.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 04/1/2021
ms.custom: references_regions
ms.openlocfilehash: fb53ad309c741fc898bcf3e27347038c0e382ea4
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107509148"
---
# <a name="manage-zone-redundant-high-availability-in-azure-database-for-mysql-flexible-server-with-azure-cli"></a>Zarządzanie strefowo nadmiarową wysoką dostępnością w Azure Database for MySQL elastycznym serwerze za pomocą interfejsu wiersza polecenia platformy Azure

> [!NOTE]
> Azure Database for MySQL elastyczny serwer jest w publicznej wersji zapoznawczej. 

W tym artykule opisano sposób włączania lub wyłączania strefowo nadmiarowej konfiguracji wysokiej dostępności w czasie tworzenia serwera na serwerze elastycznym. Strefowo nadmiarową wysoką dostępność można również wyłączyć po utworzeniu serwera. Włączanie strefowo nadmiarowej wysokiej dostępności po utworzeniu serwera nie jest obsługiwane.

Funkcja wysokiej dostępności apowiuje fizycznie oddzieloną replikę podstawową i rezerwową w różnych strefach. Aby uzyskać więcej informacji, zobacz [dokumentację dotyczącą pojęć dotyczących wysokiej dostępności.](./concepts/../concepts-high-availability.md) Włączenie lub wyłączenie wysokiej dostępności nie zmienia innych ustawień, takich jak konfiguracja sieci wirtualnej, ustawienia zapory i przechowywanie kopii zapasowych. Wyłączenie wysokiej dostępności nie ma wpływu na łączność i operacje aplikacji.

> [!IMPORTANT]
> Strefowo nadmiarowa wysoka dostępność jest dostępna w ograniczonym zestawie regionów. Zapoznaj się z obsługiwanymi regionami [tutaj.](https://docs.microsoft.com/azure/mysql/flexible-server/overview#azure-regions) 

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

## <a name="enable-high-availability-during-server-creation"></a>Włączanie wysokiej dostępności podczas tworzenia serwera
Serwer można utworzyć tylko przy użyciu warstwy cenowej Ogólnego przeznaczenia lub Zoptymalizowane pod kątem pamięci o wysokiej dostępności. Wysoką dostępność serwera można włączyć tylko podczas tworzenia.

**Użycia:**

```azurecli
az mysql flexible-server create [--high-availability {Disabled, Enabled}]
                                [--resource-group]
                                [--name]
```

**Przykład:**
```azurecli
az mysql flexible-server create --name myservername --sku-name Standard-D2ds_v4 --resource-group myresourcegroup --high-availability Enabled
```

## <a name="disable-high-availability"></a>Wyłączanie wysokiej dostępności

Wysoką dostępność można wyłączyć za pomocą [polecenia az mysql flexible-server update.](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_update) Należy pamiętać, że wyłączenie wysokiej dostępności jest obsługiwane tylko wtedy, gdy serwer został utworzony z wysoką dostępnością. 

```azurecli
az mysql flexible-server update [--high-availability {Disabled, Enabled}]
                                [--resource-group]
                                [--name]
```

**Przykład:**
```azurecli
az mysql flexible-server update --resource-group myresourcegroup --name myservername --high-availability Disabled
```


## <a name="next-steps"></a>Następne kroki

-   Dowiedz się więcej [o ciągłości działania](./concepts-business-continuity.md)
-   Dowiedz się więcej o [strefowo nadmiarowej wysokiej dostępności](./concepts-high-availability.md)
