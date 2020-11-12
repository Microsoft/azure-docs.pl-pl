---
title: Parametry połączenia — Azure Database for MariaDB
description: Ten dokument zawiera listę obecnie obsługiwanych parametrów połączenia dla aplikacji, które łączą się z Azure Database for MariaDB, w tym ADO.NET (C#), JDBC, Node.js, ODBC, PHP, Python i Ruby.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: how-to
ms.date: 3/18/2020
ms.custom: devx-track-python, devx-track-js, devx-track-csharp
ms.openlocfilehash: 8119f0bfd1f9007cab0df93ad2c7ef22b4db2199
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/12/2020
ms.locfileid: "94533008"
---
# <a name="how-to-connect-applications-to-azure-database-for-mariadb"></a>Jak połączyć aplikacje z usługą Azure Database for MariaDB
W tym temacie wymieniono typy parametrów połączenia, które są obsługiwane przez Azure Database for MariaDB wraz z szablonami i przykładami. W parametrach połączenia mogą istnieć inne parametry i ustawienia.

- Aby uzyskać certyfikat, zobacz [jak skonfigurować protokół SSL](./howto-configure-ssl.md).
- {your_host} = [ServerName]. MariaDB. Database. Azure. com
- {your_user} @ {ServerName} = format identyfikatora użytkownika w celu poprawnego uwierzytelniania.  Jeśli używasz tylko identyfikatora użytkownika, uwierzytelnienie zakończy się niepowodzeniem.

## <a name="adonet"></a>ADO.NET
```csharp
Server={your_host}; Port=3306; Database={your_database}; Uid={username@servername}; Pwd={your_password}; SslMode=Preferred;
```

W tym przykładzie nazwa serwera to, nazwa `mydemoserver` bazy danych, `wpdb` Nazwa użytkownika `WPAdmin` i hasło `mypassword!2` . W związku z tym parametry połączenia powinny być następujące:

```csharp
Server= "mydemoserver.mariadb.database.azure.com"; Port=3306; Database= "wpdb"; Uid= "WPAdmin@mydemoserver"; Pwd="mypassword!2"; SslMode=Required;
```

## <a name="jdbc"></a>JDBC
```java
String url ="jdbc:mariadb://{your_host}:3306/{your_database}?useSSL=true&trustServerCertificate=true"; myDbConn = DriverManager.getConnection(url, "{username@servername}", {your_password});
```

## <a name="nodejs"></a>Node.js
```javascript
var conn = mysql.createConnection({host: "{your_host}", user: "{your_username}", password: {your_password}, database: {your_database}, port: 3306, ssl:{ca:fs.readFileSync({ca-cert filename})}});
```

## <a name="odbc"></a>ODBC
```cpp
DRIVER={MARIADB ODBC 3.0 Driver}; Server="{your_host}"; Port=3306; Database={your_database}; Uid="{username@servername}"; Pwd={your_password}; sslca={ca-cert filename}; sslverify=1;
```

## <a name="php"></a>PHP
```php
$con=mysqli_init(); mysqli_ssl_set($con, NULL, NULL, {ca-cert filename}, NULL, NULL); mysqli_real_connect($con, "{your_host}", "{username@servername}", {your_password}, {your_database}, 3306);
```

## <a name="python"></a>Python
```python
cnx = mysql.connector.connect(user="{username@servername}", password={your_password}, host="{your_host}", port=3306, database={your_database}, ssl_ca={ca-cert filename}, ssl_verify_cert=true)
```

## <a name="ruby"></a>Ruby
```ruby
client = Mysql2::Client.new(username: "{username@servername}", password: {your_password}, database: {your_database}, host: "{your_host}", port: 3306, sslca:{ca-cert filename}, sslverify:false, sslcipher:'AES256-SHA')
```

## <a name="get-the-connection-string-details-from-the-azure-portal"></a>Pobierz szczegóły parametrów połączenia z Azure Portal
W [Azure Portal](https://portal.azure.com)przejdź do serwera Azure Database for MariaDB, a następnie kliknij pozycję **Parametry połączenia** , aby uzyskać listę ciągów dla wystąpienia: ![ okienko parametry połączenia w Azure Portal](./media/howto-connection-strings/connection-strings-on-portal.png)

Ten ciąg zawiera szczegółowe informacje, takie jak sterownik, serwer i inne parametry połączenia z bazą danych. Zmodyfikuj te przykłady, aby użyć własnych parametrów, takich jak nazwa bazy danych, hasło i tak dalej. Tego ciągu można użyć do nawiązania połączenia z serwerem z poziomu kodu i aplikacji.

<!-- 
## Next steps
- For more information about connection libraries, see [Concepts - Connection libraries](./concepts-connection-libraries.md).
- -->
