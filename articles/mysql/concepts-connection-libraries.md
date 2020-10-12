---
title: Biblioteki połączeń — Azure Database for MySQL
description: W tym artykule wymieniono wszystkie biblioteki lub sterowniki, których programy klienckie mogą używać podczas nawiązywania połączenia z usługą Azure Database for MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 8/3/2020
ms.openlocfilehash: 9d4d862389a607b18ee5f2440069fa2eb6cf7bc4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87553040"
---
# <a name="connection-libraries-for-azure-database-for-mysql"></a>Biblioteki połączeń dla Azure Database for MySQL
W tym artykule wymieniono wszystkie biblioteki lub sterowniki, których programy klienckie mogą używać podczas nawiązywania połączenia z usługą Azure Database for MySQL.

## <a name="client-interfaces"></a>Interfejsy klienta
Program MySQL oferuje standardową łączność z sterownikiem bazy danych dla programu MySQL z aplikacjami i narzędziami zgodnymi ze standardami branżowymi ODBC i JDBC. Wszystkie systemy, które współdziałają z ODBC lub JDBC, mogą korzystać z programu MySQL.

| **Język** | **Platforma** | **Dodatkowy zasób** | **Pobieranie** |
| :----------- | :------------| :-----------------------| :------------|
| PHP | Windows, Linux | [Natywny sterownik MySQL dla języka PHP — mysqlnd](https://dev.mysql.com/downloads/connector/php-mysqlnd/) | [Pobieranie](https://secure.php.net/downloads.php) |
| ODBC | Platformy Windows, Linux, Mac OS X i UNIX | [Łącznik MySQL/Przewodnik dla deweloperów ODBC](https://dev.mysql.com/doc/connector-odbc/en/) | [Pobieranie](https://dev.mysql.com/downloads/connector/odbc/) |
| ADO.NET | Windows | [Łącznik MySQL/Przewodnik dla deweloperów sieci](https://dev.mysql.com/doc/connector-net/en/) | [Pobieranie](https://dev.mysql.com/downloads/connector/net/) |
| JDBC | Niezależna od platformy | [Przewodnik dla deweloperów w programie MySQL Connector/J 5,1](https://dev.mysql.com/doc/connector-j/5.1/en/) | [Pobieranie](https://dev.mysql.com/downloads/connector/j/) |
| Node.js | Windows, Linux, Mac OS X | [sidorares/Node-mysql2](https://github.com/sidorares/node-mysql2/tree/master/documentation) | [Pobieranie](https://github.com/sidorares/node-mysql2) |
| Python | Windows, Linux, Mac OS X | [Łącznik MySQL/Przewodnik dla deweloperów języka Python](https://dev.mysql.com/doc/connector-python/en/) | [Pobieranie](https://dev.mysql.com/downloads/connector/python/) |
| C++ | Windows, Linux, Mac OS X | [Łącznik MySQL — Przewodnik dla deweloperów C++](https://dev.mysql.com/doc/connector-cpp/en/) | [Pobieranie](https://dev.mysql.com/downloads/connector/python/) |
| C | Windows, Linux, Mac OS X | [Przewodnik dewelopera dla deweloperów i łącznika MySQL](https://dev.mysql.com/doc/c-api/8.0/en/) | [Pobieranie](https://dev.mysql.com/downloads/connector/c/)
| Perl | Platformy Windows, Linux, Mac OS X i UNIX | [DBD:: MySQL](https://metacpan.org/pod/DBD::mysql) | [Pobieranie](https://metacpan.org/pod/DBD::mysql) |


## <a name="next-steps"></a>Następne kroki
Przeczytaj te Przewodniki Szybki Start dotyczące sposobu nawiązywania połączenia z Azure Database for MySQL i wykonywania zapytań przy użyciu wybranego języka:

- [PHP](./connect-php.md)
- [Java](./connect-java.md)
- [.NET (C#)](./connect-csharp.md)
- [Python](./connect-python.md)
- [Node.JS](./connect-nodejs.md)
- [Ruby](./connect-ruby.md)
- [C++](connect-cpp.md)
- [Przejdź](./connect-go.md)
