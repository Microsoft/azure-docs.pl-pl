---
title: Biblioteki połączeń — Azure Database for MySQL
description: W tym artykule wymieniono wszystkie biblioteki lub sterowniki, których programy klienckie mogą używać podczas nawiązywania połączenia z usługą Azure Database for MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 5f83f937b8d9ec50ec7dc6ec781bff1f435eb45a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79537197"
---
# <a name="connection-libraries-for-azure-database-for-mysql"></a>Biblioteki połączeń dla Azure Database for MySQL
W tym artykule wymieniono wszystkie biblioteki lub sterowniki, których programy klienckie mogą używać podczas nawiązywania połączenia z usługą Azure Database for MySQL.

## <a name="client-interfaces"></a>Interfejsy klienta
Program MySQL oferuje standardową łączność z sterownikiem bazy danych dla programu MySQL z aplikacjami i narzędziami zgodnymi ze standardami branżowymi ODBC i JDBC. Wszystkie systemy, które współdziałają z ODBC lub JDBC, mogą korzystać z programu MySQL.

| **Język** | **Platforma** | **Dodatkowy zasób** | **Pobierz** |
| :----------- | :------------| :-----------------------| :------------|
| PHP | Windows, Linux | [Natywny sterownik MySQL dla języka PHP — mysqlnd](https://dev.mysql.com/downloads/connector/php-mysqlnd/) | [Pobierz](https://secure.php.net/downloads.php) |
| ODBC | Platformy Windows, Linux, Mac OS X i UNIX | [Łącznik MySQL/Przewodnik dla deweloperów ODBC](https://dev.mysql.com/doc/connector-odbc/en/) | [Pobierz](https://dev.mysql.com/downloads/connector/odbc/) |
| ADO.NET | Windows | [Łącznik MySQL/Przewodnik dla deweloperów sieci](https://dev.mysql.com/doc/connector-net/en/) | [Pobierz](https://dev.mysql.com/downloads/connector/net/) |
| JDBC | Niezależna od platformy | [Przewodnik dla deweloperów w programie MySQL Connector/J 5,1](https://dev.mysql.com/doc/connector-j/5.1/en/) | [Pobierz](https://dev.mysql.com/downloads/connector/j/) |
| Node.js | Windows, Linux, Mac OS X | [sidorares/Node-mysql2](https://github.com/sidorares/node-mysql2/tree/master/documentation) | [Pobierz](https://github.com/sidorares/node-mysql2) |
| Python | Windows, Linux, Mac OS X | [Łącznik MySQL/Przewodnik dla deweloperów języka Python](https://dev.mysql.com/doc/connector-python/en/) | [Pobierz](https://dev.mysql.com/downloads/connector/python/) |
| C++ | Windows, Linux, Mac OS X | [Łącznik MySQL — Przewodnik dla deweloperów C++](https://dev.mysql.com/doc/connector-cpp/en/) | [Pobierz](https://dev.mysql.com/downloads/connector/python/) |
| C | Windows, Linux, Mac OS X | [Przewodnik dewelopera dla deweloperów i łącznika MySQL](https://dev.mysql.com/doc/refman/8.0/en/c-api.html) | [Pobierz](https://dev.mysql.com/downloads/connector/c/)
| Języku | Platformy Windows, Linux, Mac OS X i UNIX | [DBD:: MySQL](https://metacpan.org/pod/DBD::mysql) | [Pobierz](https://metacpan.org/pod/DBD::mysql) |


## <a name="next-steps"></a>Następne kroki
Przeczytaj te Przewodniki Szybki Start dotyczące sposobu nawiązywania połączenia z Azure Database for MySQL i wykonywania zapytań przy użyciu wybranego języka:

[PHP](./connect-php.md) | [Java](./connect-java.md) | [Ruby](./connect-ruby.md)[Go](./connect-go.md) [Python](./connect-python.md) |  |  | [C++](connect-cpp.md)[.NET (C#)](./connect-csharp.md) | [Node.JS](./connect-nodejs.md).NET (C#) Python Node. js języka C++ |  

