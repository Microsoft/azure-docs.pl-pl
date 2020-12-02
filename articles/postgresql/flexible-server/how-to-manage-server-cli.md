---
title: Zarządzanie serwerem — interfejs wiersza polecenia platformy Azure — Azure Database for PostgreSQL-elastyczny serwer
description: Dowiedz się, jak zarządzać serwerem elastycznym Azure Database for PostgreSQL z poziomu interfejsu wiersza polecenia platformy Azure.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 278f8f816909a7e365d7e45d04c5169950e79a65
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96493682"
---
# <a name="manage-an-azure-database-for-postgresql---flexible-server-by-using-the-azure-cli"></a>Zarządzanie serwerem elastycznym Azure Database for PostgreSQL przy użyciu interfejsu wiersza polecenia platformy Azure

> [!IMPORTANT]
> Azure Database for PostgreSQL — serwer elastyczny jest w wersji zapoznawczej.

W tym artykule pokazano, jak zarządzać elastycznym serwerem wdrożonym na platformie Azure. Zadania zarządzania obejmują skalowanie obliczeniowe i magazynowe, Resetowanie hasła administratora oraz wyświetlanie szczegółów serwera.

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto. 

Musisz uruchomić interfejs wiersza polecenia platformy Azure w wersji 2,0 lub nowszej lokalnie. Aby sprawdzić zainstalowaną wersję, uruchom polecenie `az --version`. Jeśli konieczna będzie instalacja lub uaktualnienie interfejsu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

Zaloguj się do swojego konta za pomocą polecenia [AZ login](/cli/azure/reference-index#az-login) . 

```azurecli-interactive
az login
```

Wybierz swoją subskrypcję za pomocą polecenia [AZ Account Set](/cli/azure/account) . Zanotuj wartość **identyfikatora** z polecenia **AZ login** Output, aby użyć jako wartości argumentu **Subscription** w poniższym poleceniu. Jeśli masz wiele subskrypcji, wybierz subskrypcję, dla której chcesz obciążyć zasób. Aby zidentyfikować wszystkie subskrypcje, użyj polecenia [AZ Account List](/cli/azure/account#az-account-list) .

```azurecli
az account set --subscription <subscription id>
```

> [!Important]
> Jeśli nie utworzono jeszcze elastycznego serwera, należy to zrobić, aby wykonać czynności opisane w tym przewodniku.

## <a name="scale-compute-and-storage"></a>Skalowanie zasobów obliczeniowych i magazynu

Możesz łatwo skalować warstwę obliczeniową, rdzeni wirtualnych i magazyn przy użyciu poniższego polecenia. Aby uzyskać listę wszystkich operacji serwera, które można uruchomić, zobacz [AZ Postgres elastyczny-Server](/cli/azure/postgres/flexible-server) Overview.

```azurecli-interactive
az postgres flexible-server update --resource-group myresourcegroup --name mydemoserver --sku-name Standard_D4ds_v3 --storage-size 6144
```

Poniżej znajdują się szczegółowe informacje dotyczące argumentów w poprzednim kodzie:

**Ustawienie** | **Wartość przykładowa** | **Opis**
---|---|---
name | mydemoserver | Wprowadź unikatową nazwę serwera. Nazwa serwera może zawierać tylko małe litery, cyfry i znaki łącznika (-). Musi zawierać od 3 do 63 znaków.
resource-group | myresourcegroup | Podaj nazwę grupy zasobów platformy Azure.
sku-name|Standard_D4ds_v3|Wprowadź nazwę warstwy obliczeniowej i jej rozmiaru. Wartość jest zgodna z Konwencją *Standard_ {rozmiar maszyny wirtualnej}* w postaci skróconej. Aby uzyskać więcej informacji, zobacz [warstwy cenowe](../concepts-pricing-tiers.md) .
storage-size | 6144 | Wprowadź pojemność magazynu serwera (w megabajtach). Wartość minimalna to 5120, zwiększająca się o przyrosty 1024.

> [!IMPORTANT]
> Nie można skalować magazynu w dół. 

## <a name="manage-postgresql-databases-on-a-server"></a>Zarządzanie bazami danych PostgreSQL na serwerze

Istnieje wiele aplikacji, za pomocą których można nawiązać połączenie z serwerem usługi Azure Database for PostgreSQL. Jeśli na komputerze klienckim jest zainstalowany program PostgreSQL, możesz użyć lokalnego wystąpienia programu [PSQL](https://www.postgresql.org/docs/current/static/app-psql.html). Użyjmy teraz narzędzia wiersza polecenia PSQL, aby nawiązać połączenie z serwerem Azure Database for PostgreSQL.

1. Uruchom następujące polecenie **PSQL** :

   ```bash
   psql --host=<servername> --port=<port> --username=<user> --dbname=<dbname>
   ```

   Na przykład następujące polecenie nawiązuje połączenie z domyślną bazą danych o nazwie **Postgres** na serwerze PostgreSQL **mydemoserver.Postgres.Database.Azure.com** za pomocą poświadczeń dostępu. Po wyświetleniu monitu wprowadź `<server_admin_password>` wybraną opcję.
  
   ```bash
   psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=myadmin --dbname=postgres
   ```

   Po nawiązaniu połączenia narzędzie PSQL wyświetli monit **Postgres** , gdzie można wprowadzić polecenia SQL. Gdy używana wersja programu PSQL różni się od wersji na serwerze Azure Database for PostgreSQL, zostanie wyświetlone ostrzeżenie.

   Przykład danych wyjściowych narzędzia psql:

   ```bash
   psql (11.3, server 12.1)
   WARNING: psql major version 11, server major version 12.
            Some psql features might not work.
   SSL connection (protocol: TLSv1.2, cipher: ECDHE-RSA-AES256-GCM-SHA384, bits: 256, compression: off)
   Type "help" for help.

   postgres=>
   ```

   > [!TIP]
   > Jeśli konfiguracja zapory nie umożliwia dostępu do adresu IP klienta, wystąpi następujący błąd:
   >
   > "PSQL: błąd krytyczny: brak wpisu pg_hba. conf dla hosta `<IP address>` , użytkownik" Administrator ", baza danych" Postgres ", SSL w przypadku krytycznym: wymagane jest połączenie SSL. Określ opcje protokołu SSL i spróbuj ponownie. "
   >
   > Upewnij się, że adres IP klienta jest dozwolony w regułach zapory.

2. Utwórz pustą bazę danych o nazwie **postgresdb** , wpisując w wierszu polecenia następujące polecenie:

    ```bash
    CREATE DATABASE postgresdb;
    ```

3. W wierszu polecenia Uruchom następujące polecenie, aby przełączyć połączenia na nowo utworzoną bazę danych **postgresdb**:

    ```bash
    \c postgresdb
    ```

4. Wpisz  `\q` , a następnie naciśnij klawisz ENTER, aby zakończyć PSQL.

W tej sekcji połączono się z serwerem Azure Database for PostgreSQL za pośrednictwem programu PSQL i utworzono pustą bazę danych użytkownika.

## <a name="reset-the-admin-password"></a>Zresetuj hasło administratora

Hasło roli administratora można zmienić za pomocą następującego polecenia:

```azurecli-interactive
az postgres flexible-server update --resource-group myresourcegroup --name mydemoserver --admin-password <new-password>
```

> [!IMPORTANT]
> Wybierz hasło, które zawiera co najmniej 8 znaków i maksymalnie 128 znaków. Hasło musi zawierać znaki z trzech z następujących kategorii: 
> - Wielkie litery angielskie
> - Małe litery angielskie
> - Liczby
> - Znaki inne niż alfanumeryczne

## <a name="delete-a-server"></a>Usuwanie serwera

Aby usunąć serwer elastyczny Azure Database for PostgreSQL, uruchom polecenie [AZ Postgres elastyczny-Server Delete](/cli/azure/postgres/flexible-server#az-PostgreSQL-flexible-server-delete) .

```azurecli-interactive
az postgres flexible-server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>Następne kroki

- [Informacje o pojęciach dotyczących tworzenia kopii zapasowych i przywracania](concepts-backup-restore.md)
- [Dostrajanie i monitorowanie serwera](concepts-monitoring.md)