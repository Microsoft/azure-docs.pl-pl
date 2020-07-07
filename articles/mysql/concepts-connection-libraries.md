---
title: Biblioteki połączeń — Azure Database for MySQL
description: W tym artykule wymieniono wszystkie biblioteki lub sterowniki, których programy klienckie mogą używać podczas nawiązywania połączenia z usługą Azure Database for MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 5f83f937b8d9ec50ec7dc6ec781bff1f435eb45a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "79537197"
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
| C | Windows, Linux, Mac OS X | [Przewodnik dewelopera dla deweloperów i łącznika MySQL](https://dev.mysql.com/doc/refman/8.0/en/c-api.html) | [Pobieranie](https://dev.mysql.com/downloads/connector/c/)
| Języku | Platformy Windows, Linux, Mac OS X i UNIX | [DBD:: MySQL](https://metacpan.org/pod/DBD::mysql) | [Pobieranie](https://metacpan.org/pod/DBD::mysql) |


## <a name="next-steps"></a>Następne kroki
Przeczytaj te Przewodniki Szybki Start dotyczące sposobu nawiązywania połączenia z Azure Database for MySQL i wykonywania zapytań przy użyciu wybranego języka:

Język [php](./connect-php.md)  |  [Język Java](./connect-java.md)  |   [.NET (C#)](./connect-csharp.md)  |  Język [Python](./connect-python.md)  |  [Node.JS](./connect-nodejs.md)  |  [Ruby](./connect-ruby.md)  |  Język [C++](connect-cpp.md)  |  [Przejdź](./connect-go.md)

