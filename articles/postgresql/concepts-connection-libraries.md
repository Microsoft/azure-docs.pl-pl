---
title: Biblioteki połączeń — Azure Database for PostgreSQL — pojedynczy serwer
description: W tym artykule opisano kilka bibliotek i sterowników, których można używać podczas tworzenia aplikacji do nawiązywania połączeń i wykonywania zapytań na serwerze Azure Database for PostgreSQL-pojedynczym.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: e182ef6c5fb7bf1b76424fffdbc862775e93e29c
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105606304"
---
# <a name="connection-libraries-for-azure-database-for-postgresql---single-server"></a>Biblioteki połączeń dla Azure Database for PostgreSQL — pojedynczy serwer
W tym artykule wymieniono biblioteki i sterowniki, których deweloperzy mogą używać do tworzenia aplikacji do łączenia się z Azure Database for PostgreSQLami i wykonywania z nich zapytań.

## <a name="client-interfaces"></a>Interfejsy klienta
Większość bibliotek klienta języka używanych do nawiązywania połączenia z serwerem PostgreSQL są projektami zewnętrznymi i są one dystrybuowane niezależnie. Wymienione biblioteki są obsługiwane na platformach Windows, Linux i Mac w celu nawiązania połączenia z Azure Database for PostgreSQL. Kilka przykładów przewodnika Szybki Start znajduje się w sekcji Następne kroki.

| **Język** | **Interfejs klienta** | **Dodatkowe informacje** | **Pobieranie** |
|--------------|----------------------------------------------------------------|-------------------------------------|--------------------------------------------------------------------|
| Python | [psycopg](http://initd.org/psycopg/) | Baza danych API 2,0 — zgodna | [Pobieranie](http://initd.org/psycopg/download/) |
| PHP | [php — pgsql](https://secure.php.net/manual/en/book.pgsql.php) | Rozszerzenie bazy danych | [Instalacja](https://secure.php.net/manual/en/pgsql.installation.php) |
| Node.js | [Pakiet npm PG](https://www.npmjs.com/package/pg) | Czysty klient nieblokujący języka JavaScript | [Instalacja](https://www.npmjs.com/package/pg) |
| Java | [JDBC](https://jdbc.postgresql.org/) | Sterownik JDBC typu 4 | [Pobieranie](https://jdbc.postgresql.org/download.html)  |
| Ruby | [PG rozwiązania Gem](https://deveiate.org/code/pg/) | Interfejs Ruby | [Pobieranie](https://rubygems.org/downloads/pg-0.20.0.gem) |
| Go | [PQ pakietu](https://godoc.org/github.com/lib/pq) | Czysty sterownik Postgres | [Instalacja](https://github.com/lib/pq/blob/master/README.md) |
| C \# /.NET | [Npgsql](https://www.npgsql.org/) | ADO.NET Dostawca danych | [Pobieranie](https://www.microsoft.com/net/) |
| ODBC | [psqlODBC](https://odbc.postgresql.org/) | Sterownik ODBC | [Pobieranie](https://www.postgresql.org/ftp/odbc/versions/) |
| C | [libpq](https://www.postgresql.org/docs/9.6/static/libpq.html) | Podstawowy interfejs języka C | Dołączono |
| C++ | [libpqxx](http://pqxx.org/) | Interfejs języka C++ w nowym stylu | [Pobieranie](http://pqxx.org/download/software/) |

## <a name="next-steps"></a>Następne kroki
Przeczytaj te Przewodniki Szybki Start dotyczące sposobu nawiązywania połączenia z Azure Database for PostgreSQL i wykonywania zapytań przy użyciu wybranego języka:

Język [Python](./connect-python.md)  |  [Node.JS](./connect-nodejs.md)  |  [Język Java](./connect-java.md)  |  [Ruby](./connect-ruby.md)  |  Język [php](./connect-php.md)  |  [.NET (C#)](./connect-csharp.md)  |  [Przejdź](./connect-go.md)
