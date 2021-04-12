---
title: Zgodność sterowników i narzędzi — Azure Database for MySQL
description: W tym artykule opisano sterowniki MySQL i narzędzia do zarządzania, które są zgodne z Azure Database for MySQL.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 59f70659f25f925f8f802b0fcaa1f61d4e7f7dc0
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2021
ms.locfileid: "107106942"
---
# <a name="mysql-drivers-and-management-tools-compatible-with-azure-database-for-mysql"></a>Sterowniki MySQL i narzędzia do zarządzania zgodne z Azure Database for MySQL

W tym artykule opisano sterowniki i narzędzia do zarządzania, które są zgodne z Azure Database for MySQL jednym serwerem.

>[!NOTE]
>Ten artykuł dotyczy tylko Azure Database for MySQL jednego serwera, aby zapewnić zgodność sterowników z [architekturą łączności](concepts-connectivity-architecture.md) jednego serwera. [Azure Database for MySQL elastyczny serwer](/../flexible-server/overview.md) jest zgodny ze wszystkimi sterownikami i narzędziami obsługiwanymi i zgodnymi z programem MySQL Community Edition. 

## <a name="mysql-drivers"></a>Sterowniki MySQL
Azure Database for MySQL korzysta z najbardziej popularnej wersji Community Database programu MySQL. W związku z tym jest zgodny z szeroką gamą języków programowania i sterowników. Celem jest wsparcie trzech najnowszych wersji sterowników MySQL oraz podejmowanie wysiłków z członkami społeczności open source, aby stale ulepszać funkcje i użyteczność sterowników MySQL. Lista sterowników, które zostały przetestowane i uznane za zgodne z Azure Database for MySQL 5,6 i 5,7, znajduje się w poniższej tabeli:

| **Język programowania** | **Sterownik** | **Linki** | **Zgodne wersje** | **Niezgodne wersje** | **Uwagi** |
| :----------------------- | :--------- | :-------- | :---------------------- | :------------------------ | :-------- |
| PHP | mysqli, pdo_mysql, mysqlnd | https://secure.php.net/downloads.php | 5,5, 5,6, 7. x | 5.3 | W przypadku połączenia PHP 7,0 z protokołem SSL MySQLi Dodaj MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT w parametrach połączenia. <br> ```mysqli_real_connect($conn, $host, $username, $password, $db_name, 3306, NULL, MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT);```<br> Zestaw PDO: ```PDO::MYSQL_ATTR_SSL_VERIFY_SERVER_CERT``` opcja false.|
| .NET | Łącznik Async MySQL dla platformy .NET | https://github.com/mysql-net/MySqlConnector <br> [Pakiet instalacyjny z narzędzia NuGet](https://www.nuget.org/packages/MySqlConnector/) | 0,27 i po | 0.26.5 i przed | |
| .NET | Łącznik MySQL/NET | https://github.com/mysql/mysql-connector-net | 6.6.3, 7,0, 8,0 |  | Usterka kodowania może spowodować niepowodzenie połączeń w niektórych systemach Windows bez kodowania UTF8. |
| Node.js | mysqljs | https://github.com/mysqljs/mysql/ <br> Pakiet instalacyjny z NPM:<br> Uruchom `npm install mysql` z npm | 2.15 | 2.14.1 i przed | |
| Node.js | Node-mysql2 | https://github.com/sidorares/node-mysql2 | 1.3.4 + | | |
| Go | Przejdź do sterownika MySQL | https://github.com/go-sql-driver/mysql/releases | 1,3, 1,4 | 1,2 i przed | Użyj `allowNativePasswords=true` w parametrach połączenia dla wersji 1,3. Wersja 1,4 zawiera poprawkę i `allowNativePasswords=true` nie jest już wymagana. |
| Python | Łącznik MySQL/Python | https://pypi.python.org/pypi/mysql-connector-python | 1.2.3, 2,0, 2,1, 2,2, używaj 8.0.16 + z MySQL 8,0  | 1.2.2 i przed | |
| Python | PyMySQL | https://pypi.org/project/PyMySQL/ | 0.7.11, 0.8.0, 0.8.1, 0.9.3 + | 0.9.0-0.9.2 (regresja w web2py) | |
| Java | Łącznik MariaDB/J | https://downloads.mariadb.org/connector-java/ | 2,1, 2,0, 1,6 | 1.5.5 i przed | | 
| Java | Łącznik MySQL/J | https://github.com/mysql/mysql-connector-j | 5.1.21 +, użyj 8.0.17 + z MySQL 8,0 | 5.1.20 i poniżej | |
| C | Łącznik MySQL/C (libmysqlclient) | https://dev.mysql.com/doc/refman/5.7/en/c-api-implementations.html | 6.0.2 + | | |
| C | Łącznik MySQL/ODBC (sterownik ODBC) | https://github.com/mysql/mysql-connector-odbc | 3.51.29 + | | |
| C++ | Łącznik MySQL/C++ | https://github.com/mysql/mysql-connector-cpp | 1.1.9 + | 1.1.3 i poniżej | | 
| C++ | MySQL + +| https://github.com/tangentsoft/mysqlpp | 3.2.3 + | | |
| Ruby | mysql2 | https://github.com/brianmario/mysql2 | 0.4.10 + | | |
| R | RMySQL | https://github.com/rstats-db/RMySQL | 0.10.16 + | | |
| Swift | MySQL — SWIFT | https://github.com/novi/mysql-swift | 0.7.2 + | | |
| Swift | Vapor/MySQL | https://github.com/vapor/mysql-kit | 2.0.1 + | | |

## <a name="management-tools"></a>Narzędzia do zarządzania
Zalety zgodności rozszerzają się również do narzędzi do zarządzania bazami danych. Istniejące narzędzia powinny nadal działać z Azure Database for MySQL, o ile manipulowanie bazami danych działa w ramach ograniczonych uprawnień użytkownika. Trzy popularne narzędzia do zarządzania bazami danych, które zostały przetestowane i zostały uznane za zgodne z Azure Database for MySQL 5,6 i 5,7, zostały wymienione w poniższej tabeli:

|                                     | **MySQL Workbench 6. x i w górę** | **Navicat 12** | **PHPMyAdmin 4. x i w górę** | **Baza danych dbfałszerstw Studio dla programu MySQL 9,0** |
| :---------------------------------- | :----------------------------- | :------------- | :-------------------------| :------------------------------- |
| **Tworzenie, aktualizowanie, Odczyt, zapis, usuwanie** | X | X | X | X |
| **Połączenie SSL** | X | X | X | X |
| **Autouzupełnianie zapytań SQL** | X | X |  | X |
| **Importowanie i eksportowanie danych** | X | X | X | X |
| **Eksportuj do wielu formatów** | X | X | X | X |
| **Wykonywanie kopii zapasowych i przywracanie** |  | X |  | X |
| **Wyświetl parametry serwera** | X | X | X | X |
| **Wyświetlanie połączeń klientów** | X | X | X | X |

## <a name="next-steps"></a>Następne kroki

- [Rozwiązywanie problemów z połączeniem z usługą Azure Database for MySQL](howto-troubleshoot-common-connection-issues.md)
