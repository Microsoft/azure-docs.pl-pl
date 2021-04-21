---
title: Zarządzanie serwerem — interfejs wiersza polecenia platformy Azure — Azure Database for PostgreSQL
description: Dowiedz się, jak zarządzać serwerem Azure Database for PostgreSQL z interfejsu wiersza polecenia platformy Azure.
author: ajlam
ms.author: andrela
ms.service: postgresql
ms.topic: how-to
ms.date: 9/22/2020
ms.openlocfilehash: 36151a9afda0bb23ee244ee778a30f30a41243ee
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107791467"
---
# <a name="manage-an-azure-database-for-postgresql-single-server-using-the-azure-cli"></a>Zarządzanie serwerem Azure Database for PostgreSQL za pomocą interfejsu wiersza polecenia platformy Azure

W tym artykule pokazano, jak zarządzać pojedynczymi serwerami wdrożonym na platformie Azure. Zadania zarządzania obejmują skalowanie obliczeń i magazynu, resetowanie hasła administratora i wyświetlanie szczegółów serwera.

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto. Ten artykuł wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0 lub nowszej lokalnie. Aby sprawdzić zainstalowaną wersję, uruchom polecenie `az --version`. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

Musisz zalogować się do swojego konta przy użyciu [polecenia az login.](/cli/azure/reference-index#az_login) **Zanotuj właściwość id,** która odwołuje się do **identyfikatora subskrypcji** konta platformy Azure.

```azurecli-interactive
az login
```

Wybierz określoną subskrypcję w ramach swojego konta za pomocą [polecenia az account set.](/cli/azure/account) Zanotuj wartość **identyfikatora** z danych wyjściowych **polecenia az login,** która będzie wartością **argumentu** subskrypcji w poleceniu . Jeśli masz wiele subskrypcji, wybierz odpowiednią subskrypcję, w ramach której powinny być naliczane opłaty za ten zasób. Aby uzyskać całą subskrypcję, użyj [az account list](/cli/azure/account#az_account_list).

```azurecli
az account set --subscription <subscription id>
```

Jeśli jeszcze nie utworzono serwera, zapoznaj się z tym przewodnikem [Szybki start,](quickstart-create-server-database-azure-cli.md) aby go utworzyć.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

## <a name="scale-compute-and-storage"></a>Skalowanie zasobów obliczeniowych i magazynu

Możesz łatwo skalować warstwę cenową, zasoby obliczeniowe i magazyn przy użyciu następującego polecenia. Możesz zobaczyć wszystkie operacji serwera, które można wykonać [az postgres server overview](/cli/azure/mysql/server)

```azurecli-interactive
az postgres server update --resource-group myresourcegroup --name mydemoserver --sku-name GP_Gen5_4 --storage-size 6144
```

Poniżej znajdują się szczegółowe informacje dotyczące argumentów powyżej:

**Ustawienie** | **Wartość przykładowa** | **Opis**
---|---|---
name | mydemoserver | Wprowadź unikatową nazwę serwera Azure Database for PostgreSQL serwera. Nazwa serwera może zawierać tylko małe litery, cyfry i znaki łącznika (-). Musi zawierać od 3 do 63 znaków.
resource-group | myresourcegroup | Podaj nazwę grupy zasobów platformy Azure.
sku-name|GP_Gen5_2|Wprowadź nazwę warstwy cenowej i konfiguracji obliczeniowej. Zgodnie z konwencją {warstwa cenowa}_{generacja obliczeniowa}_{rdzenie wirtualne} w skrócie. Zobacz warstwy [cenowe, aby](./concepts-pricing-tiers.md) uzyskać więcej informacji.
storage-size | 6144 | Pojemność magazynu serwera (w megabajtach). Minimum 5120 i wzrosty o 1024 przyrosty.

> [!Important]
> - Magazyn można skalować w górę (nie można jednak skalować magazynu w dół)
> - Skalowanie w górę z warstwy Podstawowa do warstwy ogólnego przeznaczenia lub Zoptymalizowane pod kątem pamięci nie jest obsługiwane. Możesz ręcznie skalować w górę za pomocą skryptu  [powłoki bash](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/upgrade-from-basic-to-general-purpose-or-memory-optimized-tiers/ba-p/830404) lub [aplikacji PostgreSQL Workbench](https://techcommunity.microsoft.com/t5/azure-database-support-blog/how-to-scale-up-azure-database-for-mysql-from-basic-tier-to/ba-p/369134)


## <a name="manage-postgresql-databases-on-a-server"></a>Zarządzanie bazami danych PostgreSQL na serwerze.
Za pomocą dowolnego z tych poleceń można tworzyć, usuwać, wyświetlać i wyświetlać właściwości bazy danych na serwerze

| Polecenie cmdlet | Użycie| Opis |
| --- | ---| --- |
|[az postgres db create](/cli/azure/sql/db#az_mysql_db_create)|```az postgres db create -g myresourcegroup -s mydemoserver -n mydatabasename``` |Tworzy bazę danych|
|[az postgres db delete](/cli/azure/sql/db#az_mysql_db_delete)|```az postgres db delete -g myresourcegroup -s mydemoserver -n mydatabasename```|Usuń bazę danych z serwera. To polecenie nie powoduje usunięcia serwera. |
|[az postgres db list](/cli/azure/sql/db#az_mysql_db_list)|```az postgres db list -g myresourcegroup -s mydemoserver```|Wyświetla listę wszystkich baz danych na serwerze|
|[az postgres db show](/cli/azure/sql/db#az_mysql_db_show)|```az postgres db show -g myresourcegroup -s mydemoserver -n mydatabasename```|Przedstawia więcej szczegółów bazy danych|

## <a name="update-admin-password"></a>Aktualizowanie hasła administratora
To polecenie umożliwia zmianę hasła roli administratora
```azurecli-interactive
az postgres server update --resource-group myresourcegroup --name mydemoserver --admin-password <new-password>
```

> [!Important]
>  Upewnij się, że hasło ma co najmniej 8 znaków i maksymalnie 128 znaków.
> Hasło musi zawierać znaki z trzech z następujących kategorii: wielkie litery angielskie, małe litery angielskie, cyfry i znaki inne niż alfanumeryczne.

## <a name="delete-a-server"></a>Usuwanie serwera
Jeśli chcesz usunąć pojedynczy serwer PostgreSQL, możesz uruchomić [polecenie az postgres server delete.](/cli/azure/mysql/server#az_mysql_server_delete)

```azurecli-interactive
az postgres server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>Następne kroki
- [Ponowne uruchamianie serwera](howto-restart-server-cli.md)
- [Przywracanie serwera w złym stanie](howto-restore-server-cli.md)
- [Monitorowanie i strojenie serwera](concepts-monitoring.md)
