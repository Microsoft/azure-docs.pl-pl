---
title: Migrowanie bazy danych Azure Database for PostgreSQL-pojedynczego serwera
description: Opisuje sposób wyodrębniania bazy danych PostgreSQL do pliku skryptu i importowania danych do docelowej bazy danych z tego pliku.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/24/2019
ms.openlocfilehash: f7cf5d245383b8a58f03e2e3610750866a2f4b5a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "74770207"
---
# <a name="migrate-your-postgresql-database-using-export-and-import"></a>Migrowanie bazy danych PostgreSQL przy użyciu funkcji eksportowania i importowania
Za pomocą [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) można wyodrębnić bazę danych PostgreSQL do pliku skryptu i [PSQL](https://www.postgresql.org/docs/current/static/app-psql.html) do zaimportowania danych do docelowej bazy danych z tego pliku.

## <a name="prerequisites"></a>Wymagania wstępne
Aby krokowo poprowadzić ten przewodnik, musisz:
- [Serwer Azure Database for PostgreSQL](quickstart-create-server-database-portal.md) z regułami zapory, aby umożliwić dostęp do niego i bazę danych.
- Narzędzie wiersza polecenia [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) zainstalowane
- Narzędzie wiersza polecenia [PSQL](https://www.postgresql.org/docs/current/static/app-psql.html)

Wykonaj następujące kroki, aby wyeksportować i zaimportować bazę danych PostgreSQL.

## <a name="create-a-script-file-using-pg_dump-that-contains-the-data-to-be-loaded"></a>Utwórz plik skryptu przy użyciu pg_dump, który zawiera dane do załadowania
Aby wyeksportować istniejącą bazę danych PostgreSQL lokalnie lub na maszynie wirtualnej do pliku skryptu SQL, uruchom następujące polecenie w istniejącym środowisku:
```bash
pg_dump –-host=<host> --username=<name> --dbname=<database name> --file=<database>.sql
```
Na przykład jeśli masz serwer lokalny i bazę danych o nazwie **TestDB** w niej:
```bash
pg_dump --host=localhost --username=masterlogin --dbname=testdb --file=testdb.sql
```

## <a name="import-the-data-on-target-azure-database-for-postgresql"></a>Importuj dane na Azure Database for PostgreSQL docelowym
Do zaimportowania danych do serwera Azure Database for PostgreSQL i załadowania danych z pliku SQL można użyć wiersza polecenia PSQL oraz parametru--dbname (-d).
```bash
psql --file=<database>.sql --host=<server name> --port=5432 --username=<user@servername> --dbname=<target database name>
```
Ten przykład używa narzędzia PSQL i pliku skryptu o nazwie **TestDB. SQL** z poprzedniego kroku, aby zaimportować dane do bazy danych **mypgsqldb** na serwerze docelowym **mydemoserver.Postgres.Database.Azure.com**.
```bash
psql --file=testdb.sql --host=mydemoserver.database.windows.net --port=5432 --username=mylogin@mydemoserver --dbname=mypgsqldb
```

## <a name="next-steps"></a>Następne kroki
- Aby przeprowadzić migrację bazy danych PostgreSQL przy użyciu funkcji zrzutów i przywracania, zobacz [Migrowanie bazy danych PostgreSQL przy użyciu zrzutów i przywracania](howto-migrate-using-dump-and-restore.md).
- Aby uzyskać więcej informacji na temat migrowania baz danych do Azure Database for PostgreSQL, zobacz [Przewodnik po migracji bazy danych](https://aka.ms/datamigration). 
