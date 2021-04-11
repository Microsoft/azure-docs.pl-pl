---
title: Zrzuć i Przywróć-Azure Database for PostgreSQL-pojedynczy serwer
description: Bazę danych PostgreSQL można wyodrębnić do pliku zrzutu. Następnie można przywrócić z pliku utworzonego przez pg_dump w Azure Database for PostgreSQL pojedynczym serwerze.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.subservice: migration-guide
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 809ff06fe460a06a689d7bbc11cdbd5ee247f585
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2021
ms.locfileid: "106450059"
---
# <a name="migrate-your-postgresql-database-by-using-dump-and-restore"></a>Migrowanie bazy danych PostgreSQL przy użyciu zrzutów i przywracania
[!INCLUDE[applies-to-postgres-single-flexible-server](includes/applies-to-postgres-single-flexible-server.md)]

Za pomocą [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) można wyodrębnić bazę danych PostgreSQL do pliku zrzutu. Następnie użyj [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html) , aby przywrócić bazę danych PostgreSQL z pliku archiwum utworzonego przez `pg_dump` .

## <a name="prerequisites"></a>Wymagania wstępne

Aby krokowo poprowadzić ten przewodnik, musisz:
- [Serwer Azure Database for PostgreSQL](quickstart-create-server-database-portal.md), w tym reguły zapory, aby zezwolić na dostęp.
- [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) i [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html) zainstalowanych narzędzi wiersza polecenia.

## <a name="create-a-dump-file-that-contains-the-data-to-be-loaded"></a>Utwórz plik zrzutu zawierający dane do załadowania

Aby utworzyć kopię zapasową istniejącej bazy danych PostgreSQL lokalnie lub na maszynie wirtualnej, uruchom następujące polecenie:

```bash
pg_dump -Fc -v --host=<host> --username=<name> --dbname=<database name> -f <database>.dump
```
Jeśli na przykład masz serwer lokalny i bazę danych o nazwie **TestDB** , uruchom polecenie:

```bash
pg_dump -Fc -v --host=localhost --username=masterlogin --dbname=testdb -f testdb.dump
```

## <a name="restore-the-data-into-the-target-database"></a>Przywracanie danych do docelowej bazy danych

Po utworzeniu docelowej bazy danych można użyć `pg_restore` polecenia i  `--dbname` parametru, aby przywrócić dane do docelowej bazy danych z pliku zrzutu.

```bash
pg_restore -v --no-owner --host=<server name> --port=<port> --username=<user-name> --dbname=<target database name> <database>.dump
```

Uwzględnienie `--no-owner` parametru powoduje, że wszystkie obiekty utworzone podczas przywracania będą własnością użytkownika określonego za pomocą `--username` . Aby uzyskać więcej informacji, zapoznaj się z [dokumentacją PostgreSQL](https://www.postgresql.org/docs/9.6/static/app-pgrestore.html).

> [!NOTE]
> Na serwerach Azure Database for PostgreSQL połączenia TLS/SSL są domyślnie włączone. Jeśli serwer PostgreSQL wymaga połączeń TLS/SSL, ale ich nie ma, Ustaw zmienną środowiskową `PGSSLMODE=require` tak, aby narzędzie pg_restore łączyło się z protokołem TLS. Bez protokołu TLS błąd może zostać odczytany: "KRYTYCZNy: połączenie SSL jest wymagane. Określ opcje protokołu SSL i spróbuj ponownie. " W wierszu polecenia systemu Windows uruchom polecenie `SET PGSSLMODE=require` przed uruchomieniem `pg_restore` polecenia. W systemie Linux lub bash Uruchom polecenie `export PGSSLMODE=require` przed uruchomieniem `pg_restore` polecenia. 
>

W tym przykładzie Przywróć dane z pliku zrzutu **TestDB. dump** do bazy danych **mypgsqldb** na serwerze docelowym **mydemoserver.Postgres.Database.Azure.com**.

Oto przykład użycia tego elementu `pg_restore` dla pojedynczego serwera:

```bash
pg_restore -v --no-owner --host=mydemoserver.postgres.database.azure.com --port=5432 --username=mylogin@mydemoserver --dbname=mypgsqldb testdb.dump
```

Oto przykład użycia tej metody `pg_restore` dla elastycznego serwera:

```bash
pg_restore -v --no-owner --host=mydemoserver.postgres.database.azure.com --port=5432 --username=mylogin --dbname=mypgsqldb testdb.dump
```

## <a name="optimize-the-migration-process"></a>Optymalizowanie procesu migracji

Jednym ze sposobów migrowania istniejącej bazy danych PostgreSQL do Azure Database for PostgreSQL jest utworzenie kopii zapasowej bazy danych na serwerze źródłowym i przywrócenie jej na platformie Azure. Aby zminimalizować czas wymagany do przeprowadzenia migracji, należy rozważyć użycie następujących parametrów za pomocą poleceń tworzenia kopii zapasowej i przywracania.

> [!NOTE]
> Aby uzyskać szczegółowe informacje o składni, zobacz [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) i [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html).
>

### <a name="for-the-backup"></a>Dla kopii zapasowej

Utwórz kopię zapasową za pomocą `-Fc` przełącznika, aby można było wykonać przywracanie równolegle w celu przyspieszenia jego działania. Na przykład:

```bash
pg_dump -h my-source-server-name -U source-server-username -Fc -d source-databasename -f Z:\Data\Backups\my-database-backup.dump
```

### <a name="for-the-restore"></a>Dla przywracania

- Przenieś plik kopii zapasowej na maszynę wirtualną platformy Azure w tym samym regionie co serwer Azure Database for PostgreSQL, do którego chcesz przeprowadzić migrację. `pg_restore`Aby zmniejszyć opóźnienie sieci, należy wykonać z tej maszyny wirtualnej. Utwórz maszynę wirtualną z włączoną obsługą [przyspieszonej sieci](../virtual-network/create-vm-accelerated-networking-powershell.md) .

- Otwórz plik zrzutu, aby sprawdzić, czy instrukcje CREATE INDEX są po wstawieniu danych. Jeśli tak nie jest, Przenieś instrukcje tworzenia indeksu po wstawieniu danych. Powinno to być już wykonywane domyślnie, ale dobrym pomysłem jest potwierdzenie.

- Przywróć przy użyciu przełączników `-Fc` i `-j` (z liczbą), aby zrównoleglanie przywracanie. Określona liczba jest liczbą rdzeni na serwerze docelowym. Można również ustawić dwukrotnie liczbę rdzeni serwera docelowego, aby zobaczyć wpływ.

    Oto przykład użycia tego elementu `pg_restore` dla pojedynczego serwera:

    ```bash
     pg_restore -h my-target-server.postgres.database.azure.com -U azure-postgres-username@my-target-server -Fc -j 4 -d my-target-databasename Z:\Data\Backups\my-database-backup.dump
    ```

    Oto przykład użycia tej metody `pg_restore` dla elastycznego serwera:

    ```bash
     pg_restore -h my-target-server.postgres.database.azure.com -U azure-postgres-username@my-target-server -Fc -j 4 -d my-target-databasename Z:\Data\Backups\my-database-backup.dump
    ```

- Możesz również edytować plik zrzutu, dodając polecenie `set synchronous_commit = off;` na początku i polecenie `set synchronous_commit = on;` na końcu. Nie włączaj go na końcu, zanim aplikacje zmienią dane, mogą spowodować utratę danych.

- Na serwerze docelowym Azure Database for PostgreSQL rozważ wykonanie następujących czynności przed przystąpieniem do przywracania:
    
  - Wyłącz śledzenie wydajności zapytań. Te dane statystyczne nie są potrzebne podczas migracji. Można to zrobić przez ustawienie `pg_stat_statements.track` , `pg_qs.query_capture_mode` , i `pgms_wait_sampling.query_capture_mode` do `NONE` .

  - Aby przyspieszyć migrację, należy użyć dużej i dużej ilości pamięci SKU, na przykład 32 rdzeń wirtualny zoptymalizowanej pod kątem pamięci. Po zakończeniu przywracania można łatwo skalować z powrotem do preferowanej jednostki SKU. Im wyższa jednostka SKU, tym bardziej równoległości można osiągnąć przez zwiększenie odpowiedniego `-j` parametru w `pg_restore` poleceniu.

  - Większa liczba operacji we/wy na serwerze docelowym może poprawić wydajność przywracania. Aby zapewnić większą liczbę operacji we/wy, można zwiększyć rozmiar magazynu na serwerze. To ustawienie nie jest odwracalne, ale należy rozważyć, czy wyższe liczby operacji we/wy byłyby korzystne dla rzeczywistego obciążenia w przyszłości.

Pamiętaj, aby testować i sprawdzać poprawność tych poleceń w środowisku testowym przed użyciem ich w produkcji.

## <a name="next-steps"></a>Następne kroki

- Aby przeprowadzić migrację bazy danych PostgreSQL przy użyciu funkcji eksportowania i importowania, zobacz [Migrowanie bazy danych PostgreSQL przy użyciu funkcji eksportowania i importowania](howto-migrate-using-export-and-import.md).
- Aby uzyskać więcej informacji na temat migrowania baz danych do Azure Database for PostgreSQL, zobacz [Przewodnik po migracji bazy danych](https://aka.ms/datamigration).


