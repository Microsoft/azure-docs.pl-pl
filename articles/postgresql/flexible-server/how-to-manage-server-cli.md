---
title: Zarządzanie serwerem — interfejs wiersza polecenia platformy Azure — Azure Database for PostgreSQL — serwer elastyczny
description: Dowiedz się, jak zarządzać serwerem Azure Database for PostgreSQL — elastycznym z interfejsu wiersza polecenia platformy Azure.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 9a7e16bf85293a412baf5015af825377438ebb7b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107778503"
---
# <a name="manage-an-azure-database-for-postgresql---flexible-server-by-using-the-azure-cli"></a>Zarządzanie serwerem Azure Database for PostgreSQL — elastyczny serwer przy użyciu interfejsu wiersza polecenia platformy Azure

> [!IMPORTANT]
> Azure Database for PostgreSQL — serwer elastyczny jest w wersji zapoznawczej.

W tym artykule pokazano, jak zarządzać serwerem elastycznym wdrożonym na platformie Azure. Zadania zarządzania obejmują skalowanie obliczeń i magazynu, resetowanie hasła administratora i wyświetlanie szczegółów serwera.

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto. 

Musisz lokalnie uruchamiać interfejs wiersza polecenia platformy Azure w wersji 2.0 lub nowszej. Aby sprawdzić zainstalowaną wersję, uruchom polecenie `az --version`. Jeśli konieczna będzie instalacja lub uaktualnienie interfejsu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

Zaloguj się do swojego konta przy użyciu [polecenia az login.](/cli/azure/reference-index#az_login) 

```azurecli-interactive
az login
```

Wybierz subskrypcję za pomocą [polecenia az account set.](/cli/azure/account) Zanotuj wartość **identyfikatora z** danych wyjściowych polecenia **az login,** która będzie wartością **argumentu** subskrypcji w poniższym poleceniu. Jeśli masz wiele subskrypcji, wybierz subskrypcję, w ramach której mają być naliczane opłaty za zasób. Aby zidentyfikować wszystkie subskrypcje, użyj [polecenia az account list.](/cli/azure/account#az_account_list)

```azurecli
az account set --subscription <subscription id>
```

> [!Important]
> Jeśli nie utworzono jeszcze serwera elastycznego, należy to zrobić, aby postępować zgodnie z tym przewodnikiem.

## <a name="scale-compute-and-storage"></a>Skalowanie zasobów obliczeniowych i magazynu

Możesz łatwo skalować w górę warstwę obliczeniową, rdzenie wirtualne i magazyn przy użyciu następującego polecenia. Aby uzyskać listę wszystkich operacji serwera, które można uruchomić, zobacz [az postgres flexible-server overview (Az postgres flexible-server](/cli/azure/postgres/flexible-server) overview).

```azurecli-interactive
az postgres flexible-server update --resource-group myresourcegroup --name mydemoserver --sku-name Standard_D4ds_v3 --storage-size 6144
```

Poniżej przedstawiono szczegóły argumentów w poprzednim kodzie:

**Ustawienie** | **Wartość przykładowa** | **Opis**
---|---|---
name | mydemoserver | Wprowadź unikatową nazwę serwera. Nazwa serwera może zawierać tylko małe litery, cyfry i znaki łącznika (-). Musi zawierać od 3 do 63 znaków.
resource-group | myresourcegroup | Podaj nazwę grupy zasobów platformy Azure.
sku-name|Standard_D4ds_v3|Wprowadź nazwę warstwy obliczeniowej i jej rozmiar. Wartość jest zgodna z *konwencją Standard_{rozmiar maszyny wirtualnej}* w skrócie. Zobacz warstwy [cenowe, aby](../concepts-pricing-tiers.md) uzyskać więcej informacji.
storage-size | 6144 | Wprowadź pojemność magazynu serwera w megabajtach. Minimalna wartość to 5120, co zwiększa się o 1024.

> [!IMPORTANT]
> Nie można skalować magazynu w dół. 

## <a name="manage-postgresql-databases-on-a-server"></a>Zarządzanie bazami danych PostgreSQL na serwerze

Istnieje wiele aplikacji, za pomocą których można nawiązać połączenie z serwerem usługi Azure Database for PostgreSQL. Jeśli na komputerze klienckim jest zainstalowany program PostgreSQL, możesz użyć lokalnego wystąpienia [programu psql](https://www.postgresql.org/docs/current/static/app-psql.html). Użyjmy teraz narzędzia wiersza polecenia psql, aby nawiązać połączenie z Azure Database for PostgreSQL serwera.

1. Uruchom następujące polecenie **psql:**

   ```bash
   psql --host=<servername> --port=<port> --username=<user> --dbname=<dbname>
   ```

   Na przykład następujące polecenie łączy się z domyślną bazą danych o nazwie **postgres** na serwerze PostgreSQL mydemoserver.postgres.database.azure.com **przy** użyciu poświadczeń dostępu. Po wyświetleniu monitu wprowadź `<server_admin_password>` wybraną wartość.
  
   ```bash
   psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=myadmin --dbname=postgres
   ```

   Po nawiązania połączenia narzędzie psql wyświetli wiersz **polecenia postgres,** w którym można wprowadzić polecenia SQL. W początkowych danych wyjściowych połączenia zostanie wyświetlone ostrzeżenie, jeśli wersja narzędzia psql, którego używasz, różni się od wersji na Azure Database for PostgreSQL serwera.

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
   > "psql: BŁĄD KRYTYCZNY: brak wpisu pg_hba.conf dla `<IP address>` hosta, użytkownika "myadmin", bazy danych "postgres", BŁĄD KRYTYCZNY protokołu SSL: wymagane jest połączenie SSL. Określ opcje protokołu SSL i ponów próbę".
   >
   > Upewnij się, że adres IP klienta jest dozwolony w regułach zapory.

2. Utwórz pustą bazę danych **o nazwie postgresdb,** wpisując następujące polecenie w wierszu polecenia:

    ```bash
    CREATE DATABASE postgresdb;
    ```

3. Po wyświetleniu monitu uruchom następujące polecenie, aby przełączyć połączenia na nowo utworzoną bazę **danych postgresdb:**

    ```bash
    \c postgresdb
    ```

4. Wpisz  `\q` i wybierz klawisz Enter, aby zamknąć program psql.

W tej sekcji nawiązliśmy połączenie z serwerem Azure Database for PostgreSQL za pomocą programu psql i utworzono pustą bazę danych użytkownika.

## <a name="reset-the-admin-password"></a>Resetowanie hasła administratora

Hasło roli administratora można zmienić za pomocą następującego polecenia:

```azurecli-interactive
az postgres flexible-server update --resource-group myresourcegroup --name mydemoserver --admin-password <new-password>
```

> [!IMPORTANT]
> Wybierz hasło, które ma co najmniej 8 znaków i maksymalnie 128 znaków. Hasło musi zawierać znaki z trzech z następujących kategorii: 
> - Wielkie litery angielskie
> - Małe litery angielskie
> - Liczby
> - Znaki inne niż alfanumeryczne

## <a name="delete-a-server"></a>Usuwanie serwera

Aby usunąć Azure Database for PostgreSQL serwera elastycznego, uruchom [polecenie az postgres flexible-server delete.](/cli/azure/postgres/flexible-server#az_postgresql_flexible_server_delete)

```azurecli-interactive
az postgres flexible-server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>Następne kroki

- [Opis pojęć związanych z tworzeniem i przywracaniem kopii zapasowych](concepts-backup-restore.md)
- [Dostrajanie i monitorowanie serwera](concepts-monitoring.md)