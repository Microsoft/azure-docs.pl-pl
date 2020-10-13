---
title: Tworzenie baz danych i użytkowników — Azure Database for MySQL
description: W tym artykule opisano sposób tworzenia nowych kont użytkowników w celu współdziałania z serwerem Azure Database for MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: how-to
ms.date: 10/1/2020
ms.openlocfilehash: 3e1f24b3ae6133241660751293f52fec63dfbe73
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91766872"
---
# <a name="create-databases-and-users-in-azure-database-for-mysql"></a>Tworzenie baz danych i użytkowników w Azure Database for MySQL

[!INCLUDE[applies-to-single-flexible-server](includes/applies-to-single-flexible-server.md)]

W tym artykule opisano sposób tworzenia użytkowników w Azure Database for MySQL.

> [!NOTE]
> **Komunikacja bezpłatna bez opłat**
>
> Firma Microsoft obsługuje różnorodne i dołączane środowiska. Ten artykuł zawiera odwołania do programu Word *podrzędny*. Przewodnik po [stylu firmy Microsoft dla komunikacji bezpłatnej](https://github.com/MicrosoftDocs/microsoft-style-guide/blob/master/styleguide/bias-free-communication.md) jest rozpoznawany jako wykluczony wyraz. Słowo jest używane w tym artykule w celu zapewnienia spójności, ponieważ jest to słowo, które jest aktualnie wyświetlane w oprogramowaniu. W przypadku zaktualizowania oprogramowania w celu usunięcia wyrazu ten artykuł zostanie zaktualizowany w celu wyrównania.
>

Podczas pierwszego tworzenia serwera Azure Database for MySQL należy podać nazwę użytkownika i hasło administratora serwera. Aby uzyskać więcej informacji, zobacz ten [Przewodnik Szybki Start](quickstart-create-mysql-server-database-using-azure-portal.md). Możesz określić nazwę użytkownika administratora serwera w Azure Portal.

Użytkownik administrator serwera ma następujące uprawnienia: 

   SELECT, INSERT, UPDATE, DELETE, CREATE, DROP, RELOAD, PROCESS, REFERENCES, ALTER, SHOW DATABASES, CREATE TABLES, LOCK TABLES, EXECUTE, SLAVE, CREATE USER, EVENT, TRIGGER


Po utworzeniu serwera Azure Database for MySQL można użyć pierwszego konta administratora serwera, aby utworzyć dodatkowych użytkowników i udzielić im dostępu administratora. Można również użyć konta administratora serwera, aby utworzyć mniej uprzywilejowanych użytkowników, którzy mają dostęp do poszczególnych schematów bazy danych.

> [!NOTE]
> Rola administratorów i DBA nie jest obsługiwana. Zapoznaj się z [uprawnieniami](concepts-limits.md#privileges--data-manipulation-support) w artykule ograniczenia, aby zrozumieć, co nie jest obsługiwane w usłudze.
>
> Wtyczki hasła, takie jak `validate_password` i, `caching_sha2_password` nie są obsługiwane przez usługę.


## <a name="to-create-a-database-with-a-non-admin-user-in-azure-database-for-mysql"></a>Aby utworzyć bazę danych niebędącą administratorem w programie Azure Database for MySQL

1. Pobierz informacje o połączeniu i nazwa użytkownika administratora.
   Aby można było nawiązać połączenie z serwerem bazy danych, potrzebne są pełna nazwa serwera i poświadczenia logowania administratora. Możesz łatwo znaleźć nazwę serwera i informacje dotyczące logowania na stronie **Przegląd** serwera lub na stronie **Właściwości** w Azure Portal.

2. Aby nawiązać połączenie z serwerem bazy danych, użyj konta administratora i hasła. Skorzystaj z preferowanego narzędzia klienckiego, takiego jak MySQL Workbench, mysql.exe lub HeidiSQL.
   
   Jeśli nie masz pewności, jak nawiązać połączenie, zobacz [łączenie i wykonywanie zapytań dotyczących danych pojedynczego serwera](./connect-workbench.md) lub [łączenie i wykonywanie zapytań dotyczących danych na serwerze elastycznym](./flexible-server/connect-workbench.md).

3. Edytuj i uruchom następujący kod SQL. Zastąp wartość symbolu zastępczego `db_user` zamierzoną nową nazwą użytkownika. Zamień wartość symbolu zastępczego na `testdb` nazwę bazy danych.

   Ten kod SQL tworzy nową bazę danych o nazwie TestDB. Następnie tworzy nowego użytkownika w usłudze MySQL i przyznaje wszystkim użytkownikom uprawnienia do nowego schematu bazy danych (TestDB. \* ).

   ```sql
   CREATE DATABASE testdb;

   CREATE USER 'db_user'@'%' IDENTIFIED BY 'StrongPassword!';

   GRANT ALL PRIVILEGES ON testdb . * TO 'db_user'@'%';

   FLUSH PRIVILEGES;
   ```

4. Sprawdź dotacje w bazie danych:

   ```sql
   USE testdb;

   SHOW GRANTS FOR 'db_user'@'%';
   ```

5. Zaloguj się na serwerze, określ wydaną bazę danych i użyj nowej nazwy użytkownika i hasła. Ten przykład pokazuje wiersz polecenia MySQL. Korzystając z tego polecenia, zostanie wyświetlony monit o hasło użytkownika. Użyj własnej nazwy serwera, nazwy bazy danych i nazwy użytkownika.

   # <a name="single-server"></a>[Pojedynczy serwer](#tab/single-server)

   ```azurecli-interactive
   mysql --host mydemoserver.mysql.database.azure.com --database testdb --user db_user@mydemoserver -p
   ```
   # <a name="flexible-server"></a>[Elastyczny serwer](#tab/flexible-server)

   ```azurecli-interactive
   mysql --host mydemoserver.mysql.database.azure.com --database testdb --user db_user -p
   ```
 ---

## <a name="to-create-additional-admin-users-in-azure-database-for-mysql"></a>Aby utworzyć dodatkowych użytkowników administracyjnych w Azure Database for MySQL

1. Pobierz informacje o połączeniu i nazwa użytkownika administratora.
   Aby można było nawiązać połączenie z serwerem bazy danych, potrzebne są pełna nazwa serwera i poświadczenia logowania administratora. Możesz łatwo znaleźć nazwę serwera i informacje dotyczące logowania na stronie **Przegląd** serwera lub na stronie **Właściwości** w Azure Portal.

2. Aby nawiązać połączenie z serwerem bazy danych, użyj konta administratora i hasła. Skorzystaj z preferowanego narzędzia klienckiego, takiego jak MySQL Workbench, mysql.exe lub HeidiSQL.
   
   Jeśli nie masz pewności, jak nawiązać połączenie, zobacz temat [Używanie programu MySQL Workbench do nawiązywania połączeń i wykonywania zapytań dotyczących danych](./connect-workbench.md).

3. Edytuj i uruchom następujący kod SQL. Zastąp wartość symbolu zastępczego `new_master_user` nową nazwą użytkownika. Ta składnia przyznaje wymienione uprawnienia na wszystkich schematach bazy danych (*.*) użytkownikowi ( `new_master_user` w tym przykładzie).

   ```sql
   CREATE USER 'new_master_user'@'%' IDENTIFIED BY 'StrongPassword!';

   GRANT SELECT, INSERT, UPDATE, DELETE, CREATE, DROP, RELOAD, PROCESS, REFERENCES, INDEX, ALTER, SHOW DATABASES, CREATE TEMPORARY TABLES, LOCK TABLES, EXECUTE, REPLICATION SLAVE, REPLICATION CLIENT, CREATE VIEW, SHOW VIEW, CREATE ROUTINE, ALTER ROUTINE, CREATE USER, EVENT, TRIGGER ON *.* TO 'new_master_user'@'%' WITH GRANT OPTION;

   FLUSH PRIVILEGES;
   ```

4. Sprawdź dotacje:

   ```sql
   USE sys;

   SHOW GRANTS FOR 'new_master_user'@'%';
   ```

## <a name="azure_superuser"></a>azure_superuser

Wszystkie serwery Azure Database for MySQL są tworzone przy użyciu użytkownika o nazwie "azure_superuser". Jest to konto systemowe utworzone przez firmę Microsoft do zarządzania serwerem do monitorowania, wykonywania kopii zapasowych i innej regularnej konserwacji. Inżynierowie połączeń mogą również używać tego konta do uzyskiwania dostępu do serwera podczas zdarzenia z uwierzytelnianiem certyfikatu i muszą żądać dostępu przy użyciu procesów JIT (just-in-Time).

## <a name="next-steps"></a>Następne kroki

Otwórz Zaporę dla adresów IP nowych maszyn użytkowników, aby umożliwić im nawiązanie połączenia:
- [Tworzenie reguł zapory na jednym serwerze i zarządzanie nimi](howto-manage-firewall-using-portal.md) 
- [ Tworzenie reguł zapory na serwerze elastycznym i zarządzanie nimi](flexible-server/how-to-connect-tls-ssl.md)

Aby uzyskać więcej informacji o zarządzaniu kontami użytkowników, zapoznaj się z dokumentacją produktu MySQL, aby [zarządzać kontami użytkowników](https://dev.mysql.com/doc/refman/5.7/en/access-control.html), uzyskać [składnię](https://dev.mysql.com/doc/refman/5.7/en/grant.html)i [uprawnienia](https://dev.mysql.com/doc/refman/5.7/en/privileges-provided.html).
