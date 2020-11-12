---
title: Tworzenie użytkowników — Azure Database for MariaDB
description: W tym artykule opisano sposób tworzenia nowych kont użytkowników w celu współdziałania z serwerem Azure Database for MariaDB.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: how-to
ms.date: 10/1/2020
ms.openlocfilehash: 882c8365bda87e97bfbc3bee9bdd320b312b4114
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/12/2020
ms.locfileid: "94542715"
---
# <a name="create-users-in-azure-database-for-mariadb"></a>Tworzenie kont użytkowników w usłudze Azure Database for MariaDB 
W tym artykule opisano, jak można tworzyć użytkowników w Azure Database for MariaDB.

> [!NOTE]
> Komunikacja bezpłatna bez opłat
>
> Firma Microsoft obsługuje różnorodne i dołączane środowiska. Ten artykuł zawiera odwołania do programu Word _podrzędny_. Przewodnik po [stylu firmy Microsoft dla komunikacji bezpłatnej](https://github.com/MicrosoftDocs/microsoft-style-guide/blob/master/styleguide/bias-free-communication.md) jest rozpoznawany jako wykluczony wyraz. Słowo jest używane w tym artykule w celu zapewnienia spójności, ponieważ jest to obecnie słowo, które jest wyświetlane w oprogramowaniu. W przypadku zaktualizowania oprogramowania w celu usunięcia wyrazu ten artykuł zostanie zaktualizowany w celu wyrównania.
>

Podczas pierwszego tworzenia Azure Database for MariaDB należy podać nazwę użytkownika i hasło administratora serwera. Aby uzyskać więcej informacji, możesz skorzystać z [przewodnika Szybki Start](quickstart-create-mariadb-server-database-using-azure-portal.md). Nazwę użytkownika nazwy logowania administratora serwera można zlokalizować z Azure Portal.

Administrator serwera uzyskuje pewne uprawnienia do serwera na liście: SELECT, INSERT, UPDATE, DELETE, CREATE, DROP, RELOAD, PROCESS, REFERENCEs, INDEX, ALTER, SHOW Databases, CREATE Tables Table, LOCK TABLEs, EXECUTE, CREATE USER, EVENT, TRIGGER

Po utworzeniu serwera Azure Database for MariaDB można użyć pierwszego konta użytkownika administratora serwera, aby utworzyć dodatkowych użytkowników i udzielić im dostępu administratora. Ponadto konto administratora serwera może służyć do tworzenia mniej uprzywilejowanych użytkowników, którzy mają dostęp do poszczególnych schematów bazy danych.

> [!NOTE]
> Rola administratorów i DBA nie jest obsługiwana. Zapoznaj się z [uprawnieniami](concepts-limits.md#privileges--data-manipulation-support) w artykule ograniczenia, aby zrozumieć, co nie jest obsługiwane w usłudze.<br><br>
> Wtyczki hasła, takie jak "validate_password" i "caching_sha2_password", nie są obsługiwane przez usługę.

## <a name="create-additional-admin-users"></a>Tworzenie dodatkowych użytkowników administracyjnych
1. Pobierz informacje o połączeniu i nazwa użytkownika administratora.
   Aby można było nawiązać połączenie z serwerem bazy danych, potrzebne są pełna nazwa serwera i poświadczenia logowania administratora. Możesz łatwo znaleźć nazwę serwera i informacje dotyczące logowania na stronie **Przegląd** serwera lub stronie **Właściwości** w Azure Portal. 

2. Aby nawiązać połączenie z serwerem bazy danych, użyj konta administratora i hasła. Korzystaj z preferowanego narzędzia klienckiego, takiego jak MySQL Workbench, mysql.exe, HeidiSQL lub inne. 
   Jeśli nie wiesz, jak nawiązać połączenie, zobacz temat [Korzystanie z programu MySQL Workbench do nawiązywania połączeń i wysyłania zapytań dotyczących danych](./connect-workbench.md)

3. Edytuj i uruchom następujący kod SQL. Zamień nową nazwę użytkownika na wartość symbolu zastępczego `new_master_user` . Ta składnia przyznaje listę uprawnień dla wszystkich schematów bazy danych ( *.* ) do nazwy użytkownika (new_master_user w tym przykładzie). 

   ```sql
   CREATE USER 'new_master_user'@'%' IDENTIFIED BY 'StrongPassword!';
   
   GRANT SELECT, INSERT, UPDATE, DELETE, CREATE, DROP, RELOAD, PROCESS, REFERENCES, INDEX, ALTER, SHOW DATABASES, CREATE TEMPORARY TABLES, LOCK TABLES, EXECUTE, REPLICATION SLAVE, REPLICATION CLIENT, CREATE VIEW, SHOW VIEW, CREATE ROUTINE, ALTER ROUTINE, CREATE USER, EVENT, TRIGGER ON *.* TO 'new_master_user'@'%' WITH GRANT OPTION; 
   
   FLUSH PRIVILEGES;
   ```

4. Weryfikowanie dotacji 
   ```sql
   USE sys;
   
   SHOW GRANTS FOR 'new_master_user'@'%';
   ```

## <a name="create-database-users"></a>Tworzenie użytkowników baz danych

1. Pobierz informacje o połączeniu i nazwa użytkownika administratora.
   Aby można było nawiązać połączenie z serwerem bazy danych, potrzebne są pełna nazwa serwera i poświadczenia logowania administratora. Możesz łatwo znaleźć nazwę serwera i informacje dotyczące logowania na stronie **Przegląd** serwera lub stronie **Właściwości** w Azure Portal. 

2. Aby nawiązać połączenie z serwerem bazy danych, użyj konta administratora i hasła. Korzystaj z preferowanego narzędzia klienckiego, takiego jak MySQL Workbench, mysql.exe, HeidiSQL lub inne. 
   Jeśli nie wiesz, jak nawiązać połączenie, zobacz temat [Korzystanie z programu MySQL Workbench do nawiązywania połączeń i wysyłania zapytań dotyczących danych](./connect-workbench.md)

3. Edytuj i uruchom następujący kod SQL. Zastąp wartość symbolu zastępczego `db_user` zamierzoną nową nazwą użytkownika i wartość symbolu zastępczego `testdb` własną nazwą bazy danych.

   Ta składnia kodu SQL tworzy na przykład nową bazę danych o nazwie TestDB. Następnie tworzy nowego użytkownika w usłudze Azure Database for MariaDB i przyznaje wszystkie uprawnienia do nowego schematu bazy danych (TestDB. \* ) dla tego użytkownika. 

   ```sql
   CREATE DATABASE testdb;
   
   CREATE USER 'db_user'@'%' IDENTIFIED BY 'StrongPassword!';
   
   GRANT ALL PRIVILEGES ON testdb . * TO 'db_user'@'%';
   
   FLUSH PRIVILEGES;
   ```

4. Sprawdź dotacje w ramach bazy danych.
   ```sql
   USE testdb;
   
   SHOW GRANTS FOR 'db_user'@'%';
   ```

5. Zaloguj się na serwerze, określając wydaną bazę danych przy użyciu nowej nazwy użytkownika i hasła. Ten przykład pokazuje wiersz polecenia MySQL. Za pomocą tego polecenia zostanie wyświetlony monit o podanie hasła dla nazwy użytkownika. Zastąp własną nazwę serwera, nazwę bazy danych i nazwę użytkownika.

   ```bash
   mysql --host mydemoserver.mariadb.database.azure.com --database testdb --user db_user@mydemoserver -p
   ```
   Aby uzyskać więcej informacji na temat zarządzania kontami użytkowników, zobacz dokumentację MariaDB dotyczącą [zarządzania kontami użytkowników](https://mariadb.com/kb/en/library/user-account-management/), [przyznawania składni](https://mariadb.com/kb/en/library/grant/)i [uprawnień](https://mariadb.com/kb/en/library/grant/#privilege-levels).

## <a name="azure_superuser"></a>azure_superuser

Wszystkie serwery Azure Database for MySQL są tworzone przy użyciu użytkownika o nazwie "azure_superuser". Jest to konto systemowe utworzone przez firmę Microsoft do zarządzania serwerem do monitorowania, wykonywania kopii zapasowych i innej regularnej konserwacji. Inżynierowie połączeń mogą również używać tego konta do uzyskiwania dostępu do serwera podczas zdarzenia z uwierzytelnianiem certyfikatu i muszą żądać dostępu przy użyciu procesów JIT (just-in-Time).

## <a name="next-steps"></a>Następne kroki
Otwórz Zaporę dla adresów IP nowych maszyn użytkowników, aby umożliwić im nawiązanie połączenia: [Tworzenie reguł zapory Azure Database for MariaDB i zarządzanie nimi za pomocą Azure Portal](howto-manage-firewall-portal.md)  

<!--or [Azure CLI](howto-manage-firewall-using-cli.md).-->
