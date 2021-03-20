---
title: Zarządzanie serwerem — interfejs wiersza polecenia platformy Azure — Azure Database for MySQL elastyczny serwer
description: Dowiedz się, jak zarządzać serwerem elastycznym Azure Database for MySQL z poziomu interfejsu wiersza polecenia platformy Azure.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 9/21/2020
ms.openlocfilehash: b58a9dd7901f85c59b09bc4ccb197d012bce2200
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92545059"
---
# <a name="manage-an-azure-database-for-mysql---flexible-server-preview-using-the-azure-cli"></a>Zarządzanie serwerem elastycznym Azure Database for MySQL (wersja zapoznawcza) przy użyciu interfejsu wiersza polecenia platformy Azure

> [!IMPORTANT]
> Serwer elastyczny Azure Database for MySQL jest obecnie w publicznej wersji zapoznawczej.

W tym artykule pokazano, jak zarządzać elastycznym serwerem (wersja zapoznawcza) wdrożonym na platformie Azure. Zadania zarządzania obejmują skalowanie obliczeniowe i magazynowe, Resetowanie hasła administratora oraz wyświetlanie szczegółów serwera.

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

> [!Important]
> Jeśli nie utworzono jeszcze elastycznego serwera, utwórz go, aby rozpocząć pracę z tym przewodnikiem.

## <a name="scale-compute-and-storage"></a>Skalowanie zasobów obliczeniowych i magazynu

Możesz łatwo skalować w górę warstwę obliczeniową, rdzeni wirtualnych i magazyn przy użyciu poniższego polecenia. Można wyświetlić wszystkie operacje serwera, które można wykonać [AZ MySQL elastyczny — Server Update](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_update)

```azurecli-interactive
az mysql flexible-server update --resource-group myresourcegroup --name mydemoserver --sku-name Standard_D4ds_v4 --storage-size 6144
```

Poniżej znajdują się szczegółowe informacje dotyczące argumentów:

**Ustawienie** | **Wartość przykładowa** | **Opis**
---|---|---
name | mydemoserver | Wprowadź unikatową nazwę serwera Azure Database for MySQL. Nazwa serwera może zawierać tylko małe litery, cyfry i znaki łącznika (-). Musi zawierać od 3 do 63 znaków.
resource-group | myresourcegroup | Podaj nazwę grupy zasobów platformy Azure.
sku-name|Standard_D4ds_v4|Wprowadź nazwę warstwy obliczeniowej i jej rozmiaru. Postępuj zgodnie z Konwencją Standard_ {rozmiar maszyny wirtualnej} w postaci skróconej. Aby uzyskać więcej informacji, zobacz [warstwy cenowe](../concepts-pricing-tiers.md) .
storage-size | 6144 | Pojemność magazynu serwera (w megabajtach). Minimum 5120 i zwiększa się w przyrostach 1024.

> [!Important]
> - Magazyn można skalować w górę (nie można jednak skalować w dół)


## <a name="manage-mysql-databases-on-a-server"></a>Zarządzanie bazami danych MySQL na serwerze programu.
Za pomocą dowolnego z tych poleceń można tworzyć, usuwać, wyświetlać i przeglądać właściwości bazy danych na serwerze

| Polecenie cmdlet | Użycie| Opis |
| --- | ---| --- |
|[AZ MySQL elastyczne — Tworzenie serwera DB](/cli/azure/mysql/flexible-server/db#az_mysql_flexible_server_db_create)|```az mysql flexible-server db create -g myresourcegroup -s mydemoserver -n mydatabasename``` |Tworzy bazę danych|
|[AZ MySQL elastyczne — usuwanie serwera bazy danych](/cli/azure/mysql/flexible-server/db#az_mysql_flexible_server_db_delete)|```az mysql flexible-server db delete -g myresourcegroup -s mydemoserver -n mydatabasename```|Usuń bazę danych z serwera. To polecenie nie powoduje usunięcia serwera. |
|[AZ MySQL elastyczna-Server DB list](/cli/azure/mysql/flexible-server/db#az_mysql_flexible_server_db_list)|```az mysql flexible-server db list -g myresourcegroup -s mydemoserver```|Wyświetla listę wszystkich baz danych na serwerze|
|[AZ MySQL elastyczny — serwer bazy danych show](/cli/azure/mysql/flexible-server/db#az_mysql_flexible_server_db_show)|```az mysql flexible-server db show -g myresourcegroup -s mydemoserver -n mydatabasename```|Pokazuje więcej szczegółów dotyczących bazy danych|

## <a name="update-admin-password"></a>Aktualizowanie hasła administratora
Hasło roli administratora można zmienić za pomocą tego polecenia
```azurecli-interactive
az mysql flexible-server update --resource-group myresourcegroup --name mydemoserver --admin-password <new-password>
```

> [!Important]
>  Upewnij się, że hasło zawiera co najmniej 8 znaków i maksymalnie 128 znaków.
> Hasło musi zawierać znaki z trzech z następujących kategorii: wielkie litery angielskie, małe litery angielskie, cyfry i znaki inne niż alfanumeryczne.

## <a name="delete-a-server"></a>Usuwanie serwera
Jeśli chcesz usunąć elastyczny serwer MySQL, możesz uruchomić polecenie [AZ MySQL elastyczny-Server Server Delete](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_delete) .

```azurecli-interactive
az mysql flexible-server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>Następne kroki
- [Dowiedz się, jak uruchomić lub zatrzymać serwer](how-to-stop-start-server-portal.md)
- [Dowiedz się, jak zarządzać siecią wirtualną](how-to-manage-virtual-network-cli.md)
- [Rozwiązywanie problemów z połączeniem](how-to-troubleshoot-common-connection-issues.md)
- [Tworzenie i Zarządzanie zaporą](how-to-manage-firewall-cli.md)