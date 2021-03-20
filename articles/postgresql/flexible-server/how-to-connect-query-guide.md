---
title: Łączenie i wykonywanie zapytań — elastyczny serwer PostgreSQL
description: Linki do przewodników szybki start przedstawiające sposób nawiązywania połączeń z serwerem elastycznym Azure Database for PostgreSQL i uruchamiania zapytań.
services: postgresql
ms.service: postgresql
ms.topic: how-to
author: mksuni
ms.author: sumuth
ms.date: 12/08/2020
ms.openlocfilehash: ee3b1f7db8bdafb1233b32579e032e8c864c37a9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97364582"
---
# <a name="connect-and-query-overview-for-azure-database-for-postgresql--flexible-server"></a>Przegląd połączeń i zapytań dotyczących usługi Azure Database for PostgreSQL — elastyczny serwer

Poniższy dokument zawiera linki do przykładów przedstawiających sposób nawiązywania połączeń z usługą Azure Database for PostgreSQL pojedynczego serwera i wykonywania w niej zapytań. Ten przewodnik zawiera również zalecenia i rozszerzenia protokołu TLS, których można użyć do nawiązania połączenia z serwerem w obsługiwanych językach poniżej.

>[!IMPORTANT]
> Azure Database for PostgreSQL elastyczny serwer jest w **wersji zapoznawczej**.

## <a name="quickstarts"></a>Przewodniki Szybki start

| Szybki start | Opis |
|---|---|
|[PgAdmin](https://www.pgadmin.org/)|Możesz użyć pgAdmin, aby nawiązać połączenie z serwerem i uprościć proces tworzenia, obsługi i używania obiektów bazy danych.|
|[PSQL w Azure Cloud Shell](./quickstart-create-server-cli.md#connect-using-postgresql-command-line-client)|W tym artykule pokazano, jak uruchomić program [**PSQL**](https://www.postgresql.org/docs/current/static/app-psql.html) w [Azure Cloud Shell](../../cloud-shell/overview.md) , aby nawiązać połączenie z serwerem, a następnie uruchomić instrukcje w celu wykonywania zapytań, wstawiania, aktualizowania i usuwania danych w bazie danych. Jeśli jest zainstalowany w środowisku deweloperskim, można uruchomić **PSQL**|
|[Python](connect-python.md)|Ten przewodnik Szybki Start przedstawia sposób nawiązywania połączeń z bazą danych przy użyciu języka Python i używania programu Work with Database Objects w celu wykonywania zapytań dotyczących danych. |
|[Django z App Service](tutorial-django-app-service-postgres.md)|W tym samouczku pokazano, jak za pomocą języka Ruby utworzyć program do łączenia się z bazą danych i używać pracy z obiektami bazy danych w celu wykonywania zapytań dotyczących danych.|

## <a name="tls-considerations-for-database-connectivity"></a>Zagadnienia dotyczące łączności z bazą danych TLS

Transport Layer Security (TLS) jest używany przez wszystkie sterowniki dostarczone lub obsługiwane przez firmę Microsoft w celu łączenia się z bazami danych w programie Azure Database for PostgreSQL. Nie jest wymagana żadna specjalna konfiguracja, ale należy wymusić użycie protokołu TLS 1,2 dla nowo utworzonych serwerów. Zaleca się, aby w przypadku używania protokołów TLS 1,0 i 1,1 zaktualizować wersję protokołu TLS dla serwerów. Zobacz [jak skonfigurować protokół TLS](how-to-connect-tls-ssl.md)

## <a name="postgresql-extensions"></a>Rozszerzenia PostgreSQL

PostgreSQL zapewnia możliwość rozszerzenia funkcjonalności bazy danych przy użyciu rozszerzeń. Rozszerzenia zapewniają wiele powiązanych obiektów SQL razem w jednym pakiecie, który można załadować lub usunąć z bazy danych za pomocą jednego polecenia. Po załadowaniu do bazy danych rozszerzenia działają jak wbudowane funkcje.

- [Rozszerzenia Postgres 12](./concepts-extensions.md#postgres-12-extensions)
- [Rozszerzenia Postgres 11](./concepts-extensions.md#postgres-11-extensions)
- [dblink i postgres_fdw](./concepts-extensions.md#dblink-and-postgres_fdw)
- [pg_prewarm](./concepts-extensions.md#pg_prewarm)
- [pg_stat_statements](./concepts-extensions.md#pg_stat_statements)

Aby uzyskać więcej informacji, zobacz [jak używać rozszerzeń PostgreSQL na serwerze elastycznym](concepts-extensions.md).

## <a name="next-steps"></a>Następne kroki

- [Migrowanie danych przy użyciu zrzutów i przywracania](../howto-migrate-using-dump-and-restore.md)
- [Migrowanie danych przy użyciu funkcji importowania i eksportowania](../howto-migrate-using-export-and-import.md)
