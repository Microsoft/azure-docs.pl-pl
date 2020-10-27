---
title: Łączenie i zapytania — pojedynczy serwer PostgreSQL
description: Linki do przewodników szybki start przedstawiające sposób nawiązywania połączenia z Azure Database for PostgreSQL jednym serwerem i uruchamiania zapytań.
services: postgresql
ms.service: postgresql
ms.topic: how-to
author: mksuni
ms.author: sumuth
ms.date: 09/21/2020
ms.openlocfilehash: 413f5fc1f6579102b62042c1470816470c2e1449
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/26/2020
ms.locfileid: "92546555"
---
# <a name="connect-and-query-overview-for-azure-database-for-postgresql--single-server"></a>Przegląd połączeń i zapytań dotyczących usługi Azure Database for PostgreSQL — pojedynczy serwer

Poniższy dokument zawiera linki do przykładów przedstawiających sposób nawiązywania połączeń z usługą Azure Database for PostgreSQL pojedynczego serwera i wykonywania w niej zapytań. Ten przewodnik zawiera również zalecenia i rozszerzenia protokołu TLS, których można użyć do nawiązania połączenia z serwerem w obsługiwanych językach poniżej.

## <a name="quickstarts"></a>Przewodniki Szybki start

| Szybki start | Opis |
|---|---|
|[PgAdmin](https://www.pgadmin.org/)|Możesz użyć pgAdmin, aby nawiązać połączenie z serwerem i uprościć proces tworzenia, obsługi i używania obiektów bazy danych.|
|[PSQL w Azure Cloud Shell](quickstart-create-server-database-azure-cli.md#connect-to-the-azure-database-for-postgresql-server-by-using-psql)|W tym artykule pokazano, jak uruchomić program [**PSQL**](https://www.postgresql.org/docs/current/static/app-psql.html) w [Azure Cloud Shell](../cloud-shell/overview.md) , aby nawiązać połączenie z serwerem, a następnie uruchomić instrukcje w celu wykonywania zapytań, wstawiania, aktualizowania i usuwania danych w bazie danych. Jeśli jest zainstalowany w środowisku deweloperskim, można uruchomić **PSQL**|
|[PostgreSQL z VS Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb)|Rozszerzenie bazy danych platformy Azure dla VS Code (wersja zapoznawcza) umożliwia przeglądanie i wykonywanie zapytań dotyczących serwera PostgreSQL zarówno lokalnie, jak i w chmurze za pomocą zapoznawczych funkcji IntelliSense. |
|[PHP](connect-php.md)|W tym przewodniku szybki start pokazano, jak używać języka PHP do tworzenia programu w celu nawiązania połączenia z bazą danych i używania pracy z obiektami bazy danych w celu wykonywania zapytań dotyczących danych.|
|[Java](connect-java.md)|Ten przewodnik Szybki Start przedstawia sposób nawiązywania połączeń z bazą danych przy użyciu języka Java, a następnie używania programu Work with Database Objects w celu wykonywania zapytań dotyczących danych.|
|[Node.js](connect-nodejs.md)|W tym przewodniku szybki start pokazano, jak za pomocą Node.js utworzyć program do łączenia się z bazą danych i używać pracy z obiektami bazy danych w celu wykonywania zapytań dotyczących danych.|
|[.NET (C#)](connect-csharp.md)|Ten przewodnik Szybki Start przedstawia sposób use.NET (C#) w celu utworzenia programu w języku C# do łączenia się z bazą danych i używania pracy z obiektami bazy danych w celu wykonywania zapytań dotyczących danych.|
|[Przejdź](connect-go.md)|W tym przewodniku szybki start pokazano, jak używać języka go do łączenia się z bazą danych. Ponadto przedstawiono instrukcje języka Transact-SQL umożliwiające wykonywanie zapytań i modyfikowanie danych.|
|[Python](connect-python.md)|Ten przewodnik Szybki Start przedstawia sposób nawiązywania połączeń z bazą danych przy użyciu języka Python i używania programu Work with Database Objects w celu wykonywania zapytań dotyczących danych. |
|[Ruby](connect-ruby.md)|Ten przewodnik Szybki Start przedstawia sposób tworzenia programu w celu nawiązania połączenia z bazą danych przy użyciu języka Ruby oraz używania pracy z obiektami bazy danych w celu wykonywania zapytań dotyczących danych.|

## <a name="tls-considerations-for-database-connectivity"></a>Zagadnienia dotyczące łączności z bazą danych TLS

Transport Layer Security (TLS) jest używany przez wszystkie sterowniki dostarczone lub obsługiwane przez firmę Microsoft w celu łączenia się z bazami danych w programie Azure Database for PostgreSQL. Nie jest wymagana żadna specjalna konfiguracja, ale należy wymusić użycie protokołu TLS 1,2 dla nowo utworzonych serwerów. Zaleca się, aby w przypadku używania protokołów TLS 1,0 i 1,1 zaktualizować wersję protokołu TLS dla serwerów. Zobacz [jak skonfigurować protokół TLS](howto-tls-configurations.md)

## <a name="postgresql-extensions"></a>Rozszerzenia PostgreSQL

PostgreSQL zapewnia możliwość rozszerzenia funkcjonalności bazy danych przy użyciu rozszerzeń. Rozszerzenia zapewniają wiele powiązanych obiektów SQL razem w jednym pakiecie, który można załadować lub usunąć z bazy danych za pomocą jednego polecenia. Po załadowaniu do bazy danych rozszerzenia działają jak wbudowane funkcje.

- [Rozszerzenia Postgres 11](./concepts-extensions.md#postgres-11-extensions)
- [Rozszerzenia Postgres 10](./concepts-extensions.md#postgres-10-extensions)
- [Rozszerzenia Postgres 9,6](./concepts-extensions.md#postgres-96-extensions)
- [Rozszerzenia Postgres 9,5](./concepts-extensions.md#postgres-95-extensions)

Aby uzyskać więcej informacji, zobacz [jak używać rozszerzeń PostgreSQL na jednym serwerze](concepts-extensions.md).

## <a name="next-steps"></a>Następne kroki

- [Migrowanie danych przy użyciu zrzutów i przywracania](howto-migrate-using-dump-and-restore.md)
- [Migrowanie danych przy użyciu funkcji importowania i eksportowania](howto-migrate-using-export-and-import.md)
