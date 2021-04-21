---
title: Zarządzanie serwerem — interfejs wiersza polecenia platformy Azure — Azure Database for MySQL elastyczny serwer
description: Dowiedz się, jak zarządzać serwerem elastycznym Azure Database for MySQL interfejsie wiersza polecenia platformy Azure.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 9/21/2020
ms.openlocfilehash: 4ef1408d5f7afc3b78ab021cdd25eedd75110849
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107776936"
---
# <a name="manage-an-azure-database-for-mysql---flexible-server-preview-using-the-azure-cli"></a>Zarządzanie serwerem Azure Database for MySQL — elastyczny (wersja zapoznawcza) przy użyciu interfejsu wiersza polecenia platformy Azure

> [!IMPORTANT]
> Azure Database for MySQL — serwer elastyczny jest obecnie w publicznej wersji zapoznawczej.

W tym artykule pokazano, jak zarządzać serwerem elastycznym (wersja zapoznawcza) wdrożonym na platformie Azure. Zadania zarządzania obejmują skalowanie obliczeń i magazynu, resetowanie hasła administratora i wyświetlanie szczegółów serwera.

## <a name="prerequisites"></a>Wymagania wstępne
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto. Ten artykuł wymaga lokalnego uruchomienia interfejsu wiersza polecenia platformy Azure w wersji 2.0 lub nowszej. Aby sprawdzić zainstalowaną wersję, uruchom polecenie `az --version`. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

Musisz zalogować się do swojego konta przy użyciu [polecenia az login.](/cli/azure/reference-index#az_login) **Zanotuj właściwość id,** która odnosi się do **identyfikatora subskrypcji** dla konta platformy Azure.

```azurecli-interactive
az login
```

Wybierz określoną subskrypcję w ramach swojego konta za [pomocą polecenia az account set.](/cli/azure/account) Zanotuj wartość **identyfikatora z** danych wyjściowych polecenia **az login,** która ma być wartością **argumentu** subskrypcji w poleceniu . Jeśli masz wiele subskrypcji, wybierz odpowiednią subskrypcję, w ramach której powinny być naliczane opłaty za ten zasób. Aby uzyskać całą subskrypcję, użyj [az account list](/cli/azure/account#az_account_list).

```azurecli
az account set --subscription <subscription id>
```

> [!Important]
> Jeśli jeszcze nie utworzono serwera elastycznego, utwórz go, aby rozpocząć pracę z tym przewodnikiem.

## <a name="scale-compute-and-storage"></a>Skalowanie zasobów obliczeniowych i magazynu

Możesz łatwo skalować warstwę obliczeniową, rdzenie wirtualne i magazyn przy użyciu następującego polecenia. Zobaczysz całą operację serwera, która można wykonać [az mysql flexible-server update](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_update)

```azurecli-interactive
az mysql flexible-server update --resource-group myresourcegroup --name mydemoserver --sku-name Standard_D4ds_v4 --storage-size 6144
```

Poniżej znajdują się szczegółowe informacje dotyczące argumentów powyżej:

**Ustawienie** | **Wartość przykładowa** | **Opis**
---|---|---
name | mydemoserver | Wprowadź unikatową nazwę serwera Azure Database for MySQL serwera. Nazwa serwera może zawierać tylko małe litery, cyfry i znaki łącznika (-). Musi zawierać od 3 do 63 znaków.
resource-group | myresourcegroup | Podaj nazwę grupy zasobów platformy Azure.
sku-name|Standard_D4ds_v4|Wprowadź nazwę warstwy obliczeniowej i jej rozmiar. Zgodnie z konwencją Standard_{rozmiar maszyny wirtualnej} w skrócie. Zobacz warstwy [cenowe, aby](../concepts-pricing-tiers.md) uzyskać więcej informacji.
storage-size | 6144 | Pojemność magazynu serwera (w megabajtach). Minimum 5120 i wzrosty o 1024 przyrosty.

> [!Important]
> - Magazyn można skalować w górę (nie można jednak skalować magazynu w dół)


## <a name="manage-mysql-databases-on-a-server"></a>Zarządzanie bazami danych MySQL na serwerze.
Za pomocą dowolnego z tych poleceń można tworzyć, usuwać, wyświetlać i wyświetlać właściwości bazy danych na serwerze

| Polecenie cmdlet | Użycie| Opis |
| --- | ---| --- |
|[az mysql flexible-server db create](/cli/azure/mysql/flexible-server/db#az_mysql_flexible_server_db_create)|```az mysql flexible-server db create -g myresourcegroup -s mydemoserver -n mydatabasename``` |Tworzy bazę danych|
|[az mysql flexible-server db delete](/cli/azure/mysql/flexible-server/db#az_mysql_flexible_server_db_delete)|```az mysql flexible-server db delete -g myresourcegroup -s mydemoserver -n mydatabasename```|Usuń bazę danych z serwera. To polecenie nie powoduje usunięcia serwera. |
|[az mysql flexible-server db list](/cli/azure/mysql/flexible-server/db#az_mysql_flexible_server_db_list)|```az mysql flexible-server db list -g myresourcegroup -s mydemoserver```|Wyświetla listę wszystkich baz danych na serwerze|
|[az mysql flexible-server db show](/cli/azure/mysql/flexible-server/db#az_mysql_flexible_server_db_show)|```az mysql flexible-server db show -g myresourcegroup -s mydemoserver -n mydatabasename```|Przedstawia więcej szczegółów bazy danych|

## <a name="update-admin-password"></a>Aktualizowanie hasła administratora
To polecenie umożliwia zmianę hasła roli administratora
```azurecli-interactive
az mysql flexible-server update --resource-group myresourcegroup --name mydemoserver --admin-password <new-password>
```

> [!Important]
>  Upewnij się, że hasło ma co najmniej 8 znaków i maksymalnie 128 znaków.
> Hasło musi zawierać znaki z trzech z następujących kategorii: wielkie litery angielskie, małe litery angielskie, cyfry i znaki inne niż alfanumeryczne.

## <a name="delete-a-server"></a>Usuwanie serwera
Jeśli chcesz usunąć serwer elastyczny MySQL, możesz uruchomić polecenie [az mysql flexible-server delete.](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_delete)

```azurecli-interactive
az mysql flexible-server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>Następne kroki
- [Dowiedz się, jak uruchomić lub zatrzymać serwer](how-to-stop-start-server-portal.md)
- [Dowiedz się, jak zarządzać siecią wirtualną](how-to-manage-virtual-network-cli.md)
- [Rozwiązywanie problemów z połączeniem](how-to-troubleshoot-common-connection-issues.md)
- [Tworzenie zapory i zarządzanie nimi](how-to-manage-firewall-cli.md)