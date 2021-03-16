---
title: Zrzuć i Przywróć-Azure Database for PostgreSQL-pojedynczy serwer
description: Opisuje sposób wyodrębniania bazy danych PostgreSQL do pliku zrzutu i przywracania z pliku utworzonego przez pg_dump w ramach Azure Database for PostgreSQL-jednego serwera.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.subservice: migration-guide
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 16166183b56b371fe8338894f83dbacf2e659c53
ms.sourcegitcommit: 18a91f7fe1432ee09efafd5bd29a181e038cee05
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/16/2021
ms.locfileid: "103563559"
---
# <a name="migrate-your-postgresql-database-using-dump-and-restore"></a>Migrowanie bazy danych PostgreSQL metodą zrzutu i przywracania
[!INCLUDE[applies-to-postgres-single-flexible-server](includes/applies-to-postgres-single-flexible-server.md)]

[Pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) można użyć do wyodrębnienia bazy danych PostgreSQL do pliku zrzutu i [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html) do przywrócenia bazy danych PostgreSQL z pliku archiwum utworzonego przez pg_dump.

## <a name="prerequisites"></a>Wymagania wstępne
Aby krokowo poprowadzić ten przewodnik, musisz:
- [Serwer Azure Database for PostgreSQL](quickstart-create-server-database-portal.md) z regułami zapory, aby umożliwić dostęp do niego i bazę danych.
- narzędzia wiersza polecenia [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) i [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html) zainstalowane

Wykonaj następujące kroki, aby zrzucić i przywrócić bazę danych PostgreSQL:

## <a name="create-a-dump-file-using-pg_dump-that-contains-the-data-to-be-loaded"></a>Utwórz plik zrzutu za pomocą pg_dump, który zawiera dane do załadowania
Aby utworzyć kopię zapasową istniejącej bazy danych PostgreSQL lokalnie lub na maszynie wirtualnej, uruchom następujące polecenie:
```bash
pg_dump -Fc -v --host=<host> --username=<name> --dbname=<database name> -f <database>.dump
```
Na przykład jeśli masz serwer lokalny i bazę danych o nazwie **TestDB** w niej
```bash
pg_dump -Fc -v --host=localhost --username=masterlogin --dbname=testdb -f testdb.dump
```


## <a name="restore-the-data-into-the-target-azure-database-for-postgresql-using-pg_restore"></a>Przywróć dane do Azure Database for PostgreSQL docelowego przy użyciu pg_restore
Po utworzeniu docelowej bazy danych można użyć polecenia pg_restore oraz parametru-d,--dbname do przywrócenia danych do docelowej bazy danych z pliku zrzutu.
```bash
pg_restore -v --no-owner --host=<server name> --port=<port> --username=<user-name> --dbname=<target database name> <database>.dump
```

Dołączenie parametru--No-Owner powoduje, że wszystkie obiekty utworzone podczas przywracania będą własnością użytkownika określonego przy użyciu parametru--username. Aby uzyskać więcej informacji, zapoznaj się z oficjalną dokumentacją PostgreSQL na [pg_restore](https://www.postgresql.org/docs/9.6/static/app-pgrestore.html).

> [!NOTE]
> Jeśli serwer PostgreSQL wymaga połączeń TLS/SSL (domyślnie na serwerach Azure Database for PostgreSQL), Ustaw zmienną środowiskową `PGSSLMODE=require` tak, aby narzędzie pg_restore łączyło się z protokołem TLS. Bez protokołu TLS błąd może zostać odczytany  `FATAL:  SSL connection is required. Please specify SSL options and retry.`
>
> W wierszu polecenia systemu Windows uruchom polecenie `SET PGSSLMODE=require` przed uruchomieniem pg_restore polecenie. W systemie Linux lub bash Uruchom polecenie `export PGSSLMODE=require` przed uruchomieniem polecenia pg_restore.
>

W tym przykładzie Przywróć dane z pliku zrzutu **TestDB. dump** do bazy danych **mypgsqldb** na serwerze docelowym **mydemoserver.Postgres.Database.Azure.com**.

Oto przykład użycia tego **pg_restore** dla **pojedynczego serwera**:

```bash
pg_restore -v --no-owner --host=mydemoserver.postgres.database.azure.com --port=5432 --username=mylogin@mydemoserver --dbname=mypgsqldb testdb.dump
```
Oto przykład użycia tego **pg_restore** dla **elastycznego serwera**:

```bash
pg_restore -v --no-owner --host=mydemoserver.postgres.database.azure.com --port=5432 --username=mylogin --dbname=mypgsqldb testdb.dump
```
---

## <a name="optimizing-the-migration-process"></a>Optymalizowanie procesu migracji

Jednym ze sposobów migrowania istniejącej bazy danych PostgreSQL do usługi Azure Database for PostgreSQL jest wykonanie kopii zapasowej bazy danych na serwerze źródłowym i przywrócenie jej na platformie Azure. Aby zminimalizować czas wymagany do przeprowadzenia migracji, należy rozważyć użycie następujących parametrów za pomocą poleceń tworzenia kopii zapasowej i przywracania.

> [!NOTE]
> Aby uzyskać szczegółowe informacje o składni, zobacz artykuły [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) i [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html).
>

### <a name="for-the-backup"></a>Dla kopii zapasowej
- Utwórz kopię zapasową za pomocą przełącznika-FC, aby można było wykonać przywracanie równolegle w celu przyspieszenia jego działania. Na przykład:

    ```bash
    pg_dump -h my-source-server-name -U source-server-username -Fc -d source-databasename -f Z:\Data\Backups\my-database-backup.dump
    ```

### <a name="for-the-restore"></a>Dla przywracania
- Zalecamy przeniesienie pliku kopii zapasowej na maszynę wirtualną platformy Azure w tym samym regionie co serwer Azure Database for PostgreSQL, na którym przeprowadzana jest migracja, i przeprowadzenie pg_restore z tej maszyny wirtualnej w celu zmniejszenia opóźnień sieci. Zalecamy również, aby maszyna wirtualna została utworzona z włączoną obsługą [przyspieszonej sieci](../virtual-network/create-vm-accelerated-networking-powershell.md) .

- Powinno to być już wykonywane domyślnie, ale otworzyć plik zrzutu, aby sprawdzić, czy instrukcje CREATE INDEX są po wstawieniu danych. Jeśli tak nie jest, Przenieś instrukcje tworzenia indeksu po wstawieniu danych.

- Przywróć przy użyciu przełączników-FC i-j, *#* Aby zrównoleglanie przywracanie. *#* jest liczbą rdzeni na serwerze docelowym. Możesz również spróbować z *#* ustawionym dwukrotnie liczbą rdzeni serwera docelowego, aby zobaczyć wpływ. Na przykład:

Oto przykład użycia tego **pg_restore** dla **pojedynczego serwera**:
```bash
 pg_restore -h my-target-server.postgres.database.azure.com -U azure-postgres-username@my-target-server -Fc -j 4 -d my-target-databasename Z:\Data\Backups\my-database-backup.dump
```
Oto przykład użycia tego **pg_restore** dla **elastycznego serwera**:
```bash
 pg_restore -h my-target-server.postgres.database.azure.com -U azure-postgres-username@my-target-server -Fc -j 4 -d my-target-databasename Z:\Data\Backups\my-database-backup.dump
 ```

- Możesz również edytować plik zrzutu, dodając zestaw poleceń *synchronous_commit = off;* na początku, a polecenie *Set synchronous_commit = on;* na końcu. Nie włączaj go na końcu, zanim aplikacje zmienią dane, może to spowodować utratę danych.

- Na serwerze docelowym Azure Database for PostgreSQL rozważ wykonanie następujących czynności przed przystąpieniem do przywracania:
    - Wyłącz śledzenie wydajności zapytań, ponieważ te statystyki nie są zbędne podczas migracji. Można to zrobić, ustawiając wartości pg_stat_statements. Track, pg_qs. query_capture_mode i pgms_wait_sampling. query_capture_mode na NONE.

    - Aby przyspieszyć migrację, należy użyć dużej i dużej ilości pamięci SKU, na przykład 32 rdzeń wirtualny zoptymalizowanej pod kątem pamięci. Po zakończeniu przywracania można łatwo skalować z powrotem do preferowanej jednostki SKU. Im wyższa jednostka SKU, tym bardziej równoległości można osiągnąć przez zwiększenie odpowiedniego `-j` parametru w pg_restore polecenie.

    - Większa liczba operacji we/wy na serwerze docelowym może poprawić wydajność przywracania. Aby zapewnić większą liczbę operacji we/wy, można zwiększyć rozmiar magazynu na serwerze. To ustawienie nie jest odwracalne, ale należy rozważyć, czy wyższe liczby operacji we/wy byłyby korzystne dla rzeczywistego obciążenia w przyszłości.

Pamiętaj, aby testować i sprawdzać poprawność tych poleceń w środowisku testowym przed użyciem ich w produkcji.

## <a name="next-steps"></a>Następne kroki
- Aby przeprowadzić migrację bazy danych PostgreSQL przy użyciu funkcji eksportowania i importowania, zobacz [Migrowanie bazy danych PostgreSQL przy użyciu funkcji eksportowania i importowania](howto-migrate-using-export-and-import.md).
- Aby uzyskać więcej informacji na temat migrowania baz danych do Azure Database for PostgreSQL, zobacz [Przewodnik po migracji bazy danych](https://aka.ms/datamigration).
