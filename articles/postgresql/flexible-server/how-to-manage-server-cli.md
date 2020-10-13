---
title: Zarządzanie serwerem — interfejs wiersza polecenia platformy Azure — Azure Database for PostgreSQL-elastyczny serwer
description: Dowiedz się, jak zarządzać serwerem elastycznym Azure Database for PostgreSQL z poziomu interfejsu wiersza polecenia platformy Azure.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 6c088188802590139ee895c45fb26959138db07e
ms.sourcegitcommit: 541bb46e38ce21829a056da880c1619954678586
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2020
ms.locfileid: "91939804"
---
# <a name="manage-an-azure-database-for-postgresql---flexible-server-using-the-azure-cli"></a>Zarządzanie serwerem elastycznym Azure Database for PostgreSQL przy użyciu interfejsu wiersza polecenia platformy Azure

> [!IMPORTANT]
> Azure Database for PostgreSQL — serwer elastyczny jest w wersji zapoznawczej

W tym artykule pokazano, jak zarządzać elastycznym serwerem wdrożonym na platformie Azure. Zadania zarządzania obejmują skalowanie obliczeniowe i magazynowe, Resetowanie hasła administratora oraz wyświetlanie szczegółów serwera.

## <a name="prerequisites"></a>Wymagania wstępne
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto. Ten artykuł wymaga uruchomienia interfejsu wiersza polecenia platformy Azure w wersji 2,0 lub nowszej. Aby sprawdzić zainstalowaną wersję, uruchom polecenie `az --version`. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

Musisz zalogować się na swoje konto za pomocą polecenia [AZ login](https://docs.microsoft.com/cli/azure/reference-index#az-login) . Zanotuj Właściwość **ID** , która odwołuje się do **identyfikatora subskrypcji** dla Twojego konta platformy Azure.

```azurecli-interactive
az login
```

Wybierz określoną subskrypcję na koncie za pomocą polecenia [AZ Account Set](/cli/azure/account) . Zanotuj wartość **identyfikatora** z polecenia **AZ login** Output to use jako wartość argumentu **Subscription** w poleceniu. Jeśli masz wiele subskrypcji, wybierz odpowiednią subskrypcję, w ramach której powinny być naliczane opłaty za ten zasób. Aby uzyskać całą subskrypcję, użyj [AZ Account List](https://docs.microsoft.com/cli/azure/account#az-account-list).

```azurecli
az account set --subscription <subscription id>
```

> [!Important]
> Jeśli nie utworzono jeszcze elastycznego serwera, utwórz go, aby rozpocząć pracę z tym przewodnikiem.

## <a name="scale-compute-and-storage"></a>Skalowanie zasobów obliczeniowych i magazynu

Możesz łatwo skalować w górę warstwę obliczeniową, rdzeni wirtualnych i magazyn przy użyciu poniższego polecenia. Można wyświetlić wszystkie operacje serwera, które można uruchomić [AZ Postgres elastyczna-](https://azure.microsoft.com/services/postgresql/) Server — Omówienie

```azurecli-interactive
az postgres flexible-server update --resource-group myresourcegroup --name mydemoserver --sku-name Standard_D4ds_v3 --storage-size 6144
```

Poniżej znajdują się szczegółowe informacje dotyczące argumentów:

**Ustawienie** | **Wartość przykładowa** | **Opis**
---|---|---
name | mydemoserver | Wprowadź unikatową nazwę serwera. Nazwa serwera może zawierać tylko małe litery, cyfry i znaki łącznika (-). Musi zawierać od 3 do 63 znaków.
resource-group | myresourcegroup | Podaj nazwę grupy zasobów platformy Azure.
sku-name|Standard_D4ds_v3|Wprowadź nazwę warstwy obliczeniowej i jej rozmiaru. Postępuj zgodnie z Konwencją Standard_ {rozmiar maszyny wirtualnej} w postaci skróconej. Aby uzyskać więcej informacji, zobacz [warstwy cenowe](../concepts-pricing-tiers.md) .
storage-size | 6144 | Pojemność magazynu serwera (w megabajtach). Minimum 5120 i zwiększa się w przyrostach 1024.

> [!IMPORTANT]
> Nie można skalować magazynu w dół. 

## <a name="manage-postgresql-databases-on-a-server"></a>Zarządzanie bazami danych PostgreSQL na serwerze

Istnieje wiele aplikacji, za pomocą których można nawiązać połączenie z serwerem usługi Azure Database for PostgreSQL. Jeśli na Twoim komputerze klienckim jest zainstalowany program PostgreSQL, możesz użyć lokalnego wystąpienia narzędzia [psql](https://www.postgresql.org/docs/current/static/app-psql.html), aby nawiązać połączenie z serwerem Azure PostgreSQL. Użyj teraz narzędzia wiersza polecenia psql, aby nawiązać połączenie z serwerem Azure PostgreSQL.

1. Uruchom poniższe polecenie psql w celu nawiązania połączenia z serwerem usługi Azure Database for PostgreSQL

   ```bash
   psql --host=<servername> --port=<port> --username=<user> --dbname=<dbname>
   ```

   Na przykład poniższe polecenie służy do nawiązywania połączenia z domyślną bazą danych o nazwie **postgres** na Twoim serwerze PostgreSQL **mydemoserver.postgres.database.azure.com** za pomocą poświadczeń dostępu. Gdy zostanie wyświetlony monit o podanie hasła, wprowadź wybrane hasło `<server_admin_password>`.
  
   ```bash
   psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=myadmin --dbname=postgres
   ```

   Po nawiązaniu połączenia narzędzie psql wyświetli wiersz polecenia postgres, w którym można wpisywać polecenia sql. W początkowych danych wyjściowych połączenia może być wyświetlane ostrzeżenie, ponieważ używana wersja narzędzia psql może być inna niż wersja na serwerze usługi Azure Database for PostgreSQL.

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
   > "PSQL: błąd krytyczny: brak wpisu pg_hba. conf dla hosta `<IP address>` , użytkownik" Administrator ", baza danych" Postgres ", SSL w przypadku krytycznym: wymagane jest połączenie SSL. Określ opcje protokołu SSL i spróbuj ponownie.
   >
   > Upewnij się, że adres IP klienta jest dozwolony w powyższym kroku reguł zapory.

2. Utwórz pustą bazę danych o nazwie "postgresdb" w wierszu polecenia, wpisując następujące polecenie:

    ```bash
    CREATE DATABASE postgresdb;
    ```

3. W wierszu polecenia wykonaj następujące polecenie, aby przełączyć połączenia na nowo utworzoną bazę danych **postgresdb**:

    ```bash
    \c postgresdb
    ```

4. Wpisz polecenie `\q`, a następnie naciśnij klawisz Enter, aby zamknąć narzędzie psql.

Nawiązano połączenie z serwerem usługi Azure Database for PostgreSQL za pośrednictwem narzędzia psql i utworzono pustą bazę danych użytkownika.

## <a name="reset-admin-password"></a>Zresetuj hasło administratora
Hasło roli administratora można zmienić za pomocą tego polecenia
```azurecli-interactive
az postgres flexible-server update --resource-group myresourcegroup --name mydemoserver --admin-password <new-password>
```

> [!IMPORTANT]
> Upewnij się, że hasło zawiera co najmniej 8 znaków i maksymalnie 128 znaków.
> Hasło musi zawierać znaki z trzech z następujących kategorii: wielkie litery angielskie, małe litery angielskie, cyfry i znaki inne niż alfanumeryczne.

## <a name="delete-a-server"></a>Usuwanie serwera

Jeśli chcesz usunąć PostgreSQL elastyczny serwer, możesz uruchomić polecenie [AZ Postgres elastyczny-Server Delete](/cli/azure/postgres/flexible-server#az-PostgreSQL-flexible-server-delete) .

```azurecli-interactive
az postgres flexible-server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>Następne kroki

- [Informacje o pojęciach dotyczących tworzenia kopii zapasowych i przywracania](concepts-backup-restore.md)
- [Dostrajanie i monitorowanie serwera](concepts-monitoring.md)
