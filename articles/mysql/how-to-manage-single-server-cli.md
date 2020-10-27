---
title: Zarządzanie serwerem — interfejs wiersza polecenia platformy Azure — Azure Database for MySQL
description: Dowiedz się, jak zarządzać serwerem Azure Database for MySQL przy użyciu interfejsu wiersza polecenia platformy Azure.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: how-to
ms.date: 9/22/2020
ms.openlocfilehash: 1f0b059a25cd9fc77a59dc0a78feb18c2c549057
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/26/2020
ms.locfileid: "92546861"
---
# <a name="manage-an-azure-database-for-mysql-single-server-using-the-azure-cli"></a>Zarządzanie Azure Database for MySQL jednym serwerem przy użyciu interfejsu wiersza polecenia platformy Azure

W tym artykule opisano sposób zarządzania pojedynczymi serwerami wdrożonymi na platformie Azure. Zadania zarządzania obejmują skalowanie obliczeniowe i magazynowe, Resetowanie hasła administratora oraz wyświetlanie szczegółów serwera.

## <a name="prerequisites"></a>Wymagania wstępne
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto. Ten artykuł wymaga uruchomienia interfejsu wiersza polecenia platformy Azure w wersji 2,0 lub nowszej. Aby sprawdzić zainstalowaną wersję, uruchom polecenie `az --version`. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

Musisz zalogować się na swoje konto za pomocą polecenia [AZ login](/cli/azure/reference-index#az-login) . Zanotuj Właściwość **ID** , która odwołuje się do **identyfikatora subskrypcji** dla Twojego konta platformy Azure.

```azurecli-interactive
az login
```

Wybierz określoną subskrypcję na koncie za pomocą polecenia [AZ Account Set](/cli/azure/account) . Zanotuj wartość **identyfikatora** z polecenia **AZ login** Output to use jako wartość argumentu **Subscription** w poleceniu. Jeśli masz wiele subskrypcji, wybierz odpowiednią subskrypcję, w ramach której powinny być naliczane opłaty za ten zasób. Aby uzyskać całą subskrypcję, użyj [AZ Account List](/cli/azure/account#az-account-list).

```azurecli
az account set --subscription <subscription id>
```

Jeśli nie utworzono jeszcze serwera, zapoznaj się z tym [przewodnikiem Szybki Start](quickstart-create-mysql-server-database-using-azure-cli.md) , aby go utworzyć.

## <a name="scale-compute-and-storage"></a>Skalowanie zasobów obliczeniowych i magazynu
Możesz łatwo skalować warstwę cenową, zasoby obliczeniowe i magazyn przy użyciu poniższego polecenia. Można wyświetlić wszystkie operacje serwera, które można wykonać [AZ MySQL Server Overview](/cli/azure/mysql/server)

```azurecli-interactive
az mysql server update --resource-group myresourcegroup --name mydemoserver --sku-name GP_Gen5_4 --storage-size 6144
```

Poniżej znajdują się szczegółowe informacje dotyczące argumentów:

**Ustawienie** | **Wartość przykładowa** | **Opis**
---|---|---
name | mydemoserver | Wprowadź unikatową nazwę serwera Azure Database for MySQL. Nazwa serwera może zawierać tylko małe litery, cyfry i znaki łącznika (-). Musi zawierać od 3 do 63 znaków.
resource-group | myresourcegroup | Podaj nazwę grupy zasobów platformy Azure.
sku-name|GP_Gen5_2|Wprowadź nazwę warstwy cenowej i konfiguracji obliczeniowej. Zgodnie z konwencją {warstwa cenowa} _{generacja obliczeniowa}_ {rdzenie wirtualne} w skrócie. Aby uzyskać więcej informacji, zobacz [warstwy cenowe](./concepts-pricing-tiers.md) .
storage-size | 6144 | Pojemność magazynu serwera (w megabajtach). Minimum 5120 i zwiększa się w przyrostach 1024.

> [!Important]
> - Magazyn można skalować w górę (nie można jednak skalować w dół)
> - Skalowanie w górę od warstwy Podstawowa do ogólnego przeznaczenia lub zoptymalizowanej pod kątem pamięci nie jest obsługiwane. Możesz ręcznie skalować w górę przy  [użyciu skryptu bash](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/upgrade-from-basic-to-general-purpose-or-memory-optimized-tiers/ba-p/830404) lub [za pomocą programu MySQL Workbench](https://techcommunity.microsoft.com/t5/azure-database-support-blog/how-to-scale-up-azure-database-for-mysql-from-basic-tier-to/ba-p/369134)


## <a name="manage-mysql-databases-on-a-server"></a>Zarządzanie bazami danych MySQL na serwerze
Za pomocą dowolnego z tych poleceń można tworzyć, usuwać, wyświetlać i przeglądać właściwości bazy danych na serwerze

| Polecenie cmdlet | Użycie| Opis |
| --- | ---| --- |
|[AZ MySQL DB Create](/cli/azure/sql/db#az-mysql-db-create)|```az mysql db create -g myresourcegroup -s mydemoserver -n mydatabasename``` |Tworzy bazę danych|
|[Usuń AZ MySQL DB](/cli/azure/sql/db#az-mysql-db-delete)|```az mysql db delete -g myresourcegroup -s mydemoserver -n mydatabasename```|Usuń bazę danych z serwera. To polecenie nie powoduje usunięcia serwera. |
|[Lista AZ MySQL DB](/cli/azure/sql/db#az-mysql-db-list)|```az mysql db list -g myresourcegroup -s mydemoserver```|Wyświetla listę wszystkich baz danych na serwerze|
|[AZ MySQL DB show](/cli/azure/sql/db#az-mysql-db-show)|```az mysql db show -g myresourcegroup -s mydemoserver -n mydatabasename```|Pokazuje więcej szczegółów dotyczących bazy danych|

## <a name="update-admin-password"></a>Aktualizowanie hasła administratora
Hasło roli administratora można zmienić za pomocą tego polecenia
```azurecli-interactive
az mysql server update --resource-group myresourcegroup --name mydemoserver --admin-password <new-password>
```

> [!Important]
>  Upewnij się, że hasło zawiera co najmniej 8 znaków i maksymalnie 128 znaków.
> Hasło musi zawierać znaki z trzech z następujących kategorii: wielkie litery angielskie, małe litery angielskie, cyfry i znaki inne niż alfanumeryczne.

## <a name="delete-a-server"></a>Usuwanie serwera
Jeśli chcesz usunąć pojedynczy serwer MySQL, możesz uruchomić polecenie [AZ MySQL Server Delete](/cli/azure/mysql/server#az-mysql-server-delete) .

```azurecli-interactive
az mysql server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>Następne kroki
- [Ponowne uruchamianie serwera](howto-restart-server-cli.md)
- [Przywracanie serwera w nieprawidłowym stanie](howto-restore-server-cli.md)
- [Monitorowanie i strojenie serwera](concepts-monitoring.md)