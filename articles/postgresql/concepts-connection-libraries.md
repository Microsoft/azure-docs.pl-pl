---
title: Biblioteki połączeń — Azure Database for PostgreSQL — pojedynczy serwer
description: W tym artykule opisano kilka bibliotek i sterowników, których można używać podczas kodowania aplikacji do nawiązywania połączeń i wykonywania zapytań Azure Database for PostgreSQL — pojedynczy serwer.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: b8a1526605195b5eb24d8044f42b70ca5336bf7c
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107878312"
---
# <a name="connection-libraries-for-azure-database-for-postgresql---single-server"></a>Biblioteki połączeń dla Azure Database for PostgreSQL — pojedynczy serwer
W tym artykule wymieniono biblioteki i sterowniki, których deweloperzy mogą używać do tworzenia aplikacji w celu nawiązywania połączeń z programem i wykonywania Azure Database for PostgreSQL.

## <a name="client-interfaces"></a>Interfejsy klienta
Większość bibliotek klienta języka używanych do nawiązywania połączenia z serwerem PostgreSQL są projektami zewnętrznymi i są one dystrybuowane niezależnie. Wymienione biblioteki są obsługiwane na platformach Windows, Linux i Mac w celu łączenia się z Azure Database for PostgreSQL. Kilka przykładów przewodnika Szybki start znajduje się w sekcji Następne kroki.

| **Język** | **Interfejs klienta** | **Dodatkowe informacje** | **Pobieranie** |
|--------------|----------------------------------------------------------------|-------------------------------------|--------------------------------------------------------------------|
| Python | [psycopg](http://initd.org/psycopg/) | Interfejs API bazy danych zgodny z interfejsem API 2.0 | [Pobieranie](http://initd.org/psycopg/download/) |
| PHP | [php-pgsql](https://secure.php.net/manual/en/book.pgsql.php) | Rozszerzenie bazy danych | [Instalacja](https://secure.php.net/manual/en/pgsql.installation.php) |
| Node.js | [Pakiet npm pg](https://www.npmjs.com/package/pg) | Klient czystych kodów JavaScript, który nie blokuje | [Instalacja](https://www.npmjs.com/package/pg) |
| Java | [JDBC](https://jdbc.postgresql.org/) | Sterownik JDBC typu 4 | [Pobieranie](https://jdbc.postgresql.org/download.html)  |
| Ruby | [Pg gem](https://deveiate.org/code/pg/) | Ruby, interfejs | [Pobieranie](https://rubygems.org/downloads/pg-0.20.0.gem) |
| Go | [Pakiet pq](https://godoc.org/github.com/lib/pq) | Sterownik postgres pure Go | [Instalacja](https://github.com/lib/pq/blob/master/README.md) |
| C \# / .NET | [Npgsql](https://www.npgsql.org/) | ADO.NET Dostawca danych | [Pobieranie](https://dotnet.microsoft.com/download) |
| ODBC | [psqlODBC](https://odbc.postgresql.org/) | Sterownik ODBC | [Pobieranie](https://www.postgresql.org/ftp/odbc/versions/) |
| C | [libpq](https://www.postgresql.org/docs/9.6/static/libpq.html) | Podstawowy interfejs języka C | Dołączono |
| C++ | [libpqxx](http://pqxx.org/) | Interfejs języka C++ w nowym stylu | [Pobieranie](http://pqxx.org/download/software/) |

## <a name="next-steps"></a>Następne kroki
Zapoznaj się z tymi przewodnikami Szybki start, aby dowiedzieć się, jak nawiązywać połączenia z Azure Database for PostgreSQL zapytaniami przy użyciu wybranego języka:

[Python](./connect-python.md)  |  [Node.JS](./connect-nodejs.md)  |  [Java](./connect-java.md)  |  [Ruby](./connect-ruby.md)  |  [PHP](./connect-php.md)  |  [.NET (C#)](./connect-csharp.md)  |  [Przejdź do](./connect-go.md)
