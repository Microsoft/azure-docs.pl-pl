---
title: Migrowanie bazy danych Azure Database for PostgreSQL-pojedynczego serwera
description: Opisuje sposób wyodrębniania bazy danych PostgreSQL do pliku skryptu i importowania danych do docelowej bazy danych z tego pliku.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 080f444d50dcdf17be15d940002b745624b2f6a0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91708530"
---
# <a name="migrate-your-postgresql-database-using-export-and-import"></a>Migrowanie bazy danych PostgreSQL przy użyciu funkcji eksportowania i importowania
[!INCLUDE[applies-to-postgres-single-flexible-server](includes/applies-to-postgres-single-flexible-server.md)]
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
psql --file=<database>.sql --host=<server name> --port=5432 --username=<user> --dbname=<target database name>
```
Ten przykład używa narzędzia PSQL i pliku skryptu o nazwie **TestDB. SQL** z poprzedniego kroku, aby zaimportować dane do bazy danych **mypgsqldb** na serwerze docelowym **mydemoserver.Postgres.Database.Azure.com**.

W przypadku **pojedynczego serwera**Użyj tego polecenia 
```bash
psql --file=testdb.sql --host=mydemoserver.database.windows.net --port=5432 --username=mylogin@mydemoserver --dbname=mypgsqldb
```

W przypadku **serwera elastycznego**Użyj tego polecenia  
```bash
psql --file=testdb.sql --host=mydemoserver.database.windows.net --port=5432 --username=mylogin --dbname=mypgsqldb
```



## <a name="next-steps"></a>Następne kroki
- Aby przeprowadzić migrację bazy danych PostgreSQL przy użyciu funkcji zrzutów i przywracania, zobacz [Migrowanie bazy danych PostgreSQL przy użyciu zrzutów i przywracania](howto-migrate-using-dump-and-restore.md).
- Aby uzyskać więcej informacji na temat migrowania baz danych do Azure Database for PostgreSQL, zobacz [Przewodnik po migracji bazy danych](https://aka.ms/datamigration).
