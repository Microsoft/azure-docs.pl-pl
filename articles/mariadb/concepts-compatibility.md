---
title: Zgodność sterowników i narzędzi — Azure Database for MariaDB
description: W tym artykule opisano sterowniki MariaDB i narzędzia do zarządzania, które są zgodne z Azure Database for MariaDB.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 79c91fff837e3869eda0f45ce892e63b6611a1d0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86107210"
---
# <a name="mariadb-drivers-and-management-tools-compatible-with-azure-database-for-mariadb"></a>Sterowniki MariaDB i narzędzia do zarządzania zgodne z Azure Database for MariaDB

W tym artykule opisano sterowniki i narzędzia do zarządzania, które są zgodne z programem Azure Database for MariaDB.

## <a name="mariadb-drivers"></a>Sterowniki MariaDB

Azure Database for MariaDB używa wersji Community programu MariaDB Server. W związku z tym jest zgodny z szeroką gamą języków programowania i sterowników. Interfejs API i protokół MariaDB są zgodne z używanymi przez MySQL. Oznacza to, że łączniki, które współpracują z bazą danych MySQL, powinny również współpracować z usługą MariaDB.

Celem jest wsparcie trzech najnowszych wersji sterowników MariaDB oraz podejmowanie wysiłków z członkami społeczności typu open source, aby stale ulepszać funkcje i użyteczność sterowników MariaDB. W poniższej tabeli przedstawiono listę sterowników, które zostały przetestowane i zostały uznane za zgodne z Azure Database for MariaDB 10,2:

**Sterownik** | **Linki** | **Zgodne wersje** | **Niezgodne wersje** | **Uwagi**
---|---|---|---|---
PHP | https://secure.php.net/downloads.php | 5,5, 5,6, 7. x | 5.3 | W przypadku połączenia PHP 7,0 z protokołem SSL MySQLi Dodaj MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT w parametrach połączenia. <br> ```mysqli_real_connect($conn, $host, $username, $password, $db_name, 3306, NULL, MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT);```<br> Zestaw PDO: ```PDO::MYSQL_ATTR_SSL_VERIFY_SERVER_CERT``` opcja false.
.NET | [MySqlConnector w serwisie GitHub](https://github.com/mysql-net/MySqlConnector) <br> [Pakiet instalacyjny z narzędzia NuGet](https://www.nuget.org/packages/MySqlConnector/) | 0,27 i po | 0.26.5 i przed |
Łącznik MySQL/NET | [Łącznik MySQL/NET](https://github.com/mysql/mysql-connector-net) | 8,0, 7,0, 6,10 |  | Usterka kodowania może spowodować niepowodzenie połączeń w niektórych systemach Windows bez kodowania UTF8.
Node.js |  [MySQLjs w serwisie GitHub](https://github.com/mysqljs/mysql/) <br> Pakiet instalacyjny z NPM:<br> Uruchom `npm install mysql` z npm | 2.15 | 2.14.1 i przed
JĘZYK GO | https://github.com/go-sql-driver/mysql/releases | 1,3, 1,4 | 1,2 i przed | Użyj `allowNativePasswords=true` w parametrach połączenia dla wersji 1,3. Wersja 1,4 zawiera poprawkę i `allowNativePasswords=true` nie jest już wymagana.
Python | https://pypi.python.org/pypi/mysql-connector-python | 1.2.3, 2,0, 2,1, 2,2 | 1.2.2 i przed |
Java | https://downloads.mariadb.org/connector-java/ | 2,1, 2,0, 1,6 | 1.5.5 i przed |

## <a name="management-tools"></a>Narzędzia do zarządzania

Zalety zgodności rozszerzają się również do narzędzi do zarządzania bazami danych. Istniejące narzędzia powinny nadal działać z Azure Database for MariaDB, o ile manipulowanie bazami danych działa w ramach ograniczonych uprawnień użytkownika. Trzy popularne narzędzia do zarządzania bazami danych, które zostały przetestowane i zostały uznane za zgodne z Azure Database for MariaDB 10,2, są wymienione w poniższej tabeli:

| Akcja | **MySQL Workbench 6. x i w górę** | **Navicat 12** | **PHPMyAdmin 4. x i w górę**
---|---|---|---
Tworzenie, aktualizowanie, Odczyt, zapis, usuwanie | X | X | X
Połączenie SSL | X | X | X
Autouzupełnianie zapytań SQL | X | X |
Importowanie i eksportowanie danych | X | X | X
Eksportuj do wielu formatów | X | X | X
Wykonywanie kopii zapasowych i przywracanie |  | X |
Wyświetl parametry serwera | X | X | X
Wyświetlanie połączeń klientów | X | X | X

## <a name="next-steps"></a>Następne kroki

- [Rozwiązywanie problemów z połączeniem z usługą Azure Database for MariaDB](howto-troubleshoot-common-connection-issues.md)