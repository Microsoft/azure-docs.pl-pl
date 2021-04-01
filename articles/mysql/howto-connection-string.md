---
title: Parametry połączenia — Azure Database for MySQL
description: Ten dokument zawiera listę obecnie obsługiwanych parametrów połączenia dla aplikacji, które łączą się z Azure Database for MySQL, w tym ADO.NET (C#), JDBC, Node.js, ODBC, PHP, Python i Ruby.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 3/18/2020
ms.custom: devx-track-python, devx-track-js
ms.openlocfilehash: 40af2660f0052a876ef9310bc2426295ba67558b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94541491"
---
# <a name="how-to-connect-applications-to-azure-database-for-mysql"></a>Jak połączyć aplikacje z usługą Azure Database for MySQL
W tym temacie wymieniono typy parametrów połączenia, które są obsługiwane przez Azure Database for MySQL wraz z szablonami i przykładami. W parametrach połączenia mogą istnieć inne parametry i ustawienia.

- Aby uzyskać certyfikat, zobacz [jak skonfigurować protokół SSL](./howto-configure-ssl.md).
- {your_host} = \<servername> . MySQL.Database.Azure.com
- {your_user} @ {ServerName} = format identyfikatora użytkownika w celu poprawnego uwierzytelniania.  Jeśli używasz tylko identyfikatora użytkownika, uwierzytelnienie zakończy się niepowodzeniem.

## <a name="adonet"></a>ADO.NET
```ado.net
Server={your_host};Port={your_port};Database={your_database};Uid={username@servername};Pwd={your_password};[SslMode=Required;]
```

W tym przykładzie nazwa serwera to, nazwa `mydemoserver` bazy danych, `wpdb` Nazwa użytkownika `WPAdmin` i hasło `mypassword!2` . W związku z tym parametry połączenia powinny być następujące:

```ado.net
Server= "mydemoserver.mysql.database.azure.com"; Port=3306; Database= "wpdb"; Uid= "WPAdmin@mydemoserver"; Pwd="mypassword!2"; SslMode=Required;
```

## <a name="jdbc"></a>JDBC
```jdbc
String url ="jdbc:mysql://%s:%s/%s[?verifyServerCertificate=true&useSSL=true&requireSSL=true]",{your_host},{your_port},{your_database}"; myDbConn = DriverManager.getConnection(url, {username@servername}, {your_password}";
```

## <a name="nodejs"></a>Node.js
```node.js
var conn = mysql.createConnection({host: {your_host}, user: {username@servername}, password: {your_password}, database: {your_database}, Port: {your_port}[, ssl:{ca:fs.readFileSync({ca-cert filename})}}]);
```

## <a name="odbc"></a>ODBC
```odbc
DRIVER={MySQL ODBC 5.3 UNICODE Driver};Server={your_host};Port={your_port};Database={your_database};Uid={username@servername};Pwd={your_password}; [sslca={ca-cert filename}; sslverify=1; Option=3;]
```

## <a name="php"></a>PHP
```php
$con=mysqli_init(); [mysqli_ssl_set($con, NULL, NULL, {ca-cert filename}, NULL, NULL);] mysqli_real_connect($con, {your_host}, {username@servername}, {your_password}, {your_database}, {your_port});
```

## <a name="python"></a>Python
```python
cnx = mysql.connector.connect(user={username@servername}, password={your_password}, host={your_host}, port={your_port}, database={your_database}[, ssl_ca={ca-cert filename}, ssl_verify_cert=true])
```

## <a name="ruby"></a>Ruby
```ruby
client = Mysql2::Client.new(username: {username@servername}, password: {your_password}, database: {your_database}, host: {your_host}, port: {your_port}[, sslca:{ca-cert filename}, sslverify:false, sslcipher:'AES256-SHA'])
```

## <a name="get-the-connection-string-details-from-the-azure-portal"></a>Pobierz szczegóły parametrów połączenia z Azure Portal
W [Azure Portal](https://portal.azure.com)przejdź do serwera Azure Database for MySQL, a następnie kliknij pozycję **Parametry połączenia** , aby uzyskać listę ciągów dla wystąpienia: :::image type="content" source="./media/howto-connection-strings/connection-strings-on-portal.png" alt-text="okienko parametry połączenia w Azure Portal":::

Ten ciąg zawiera szczegółowe informacje, takie jak sterownik, serwer i inne parametry połączenia z bazą danych. Zmodyfikuj te przykłady, aby użyć własnych parametrów, takich jak nazwa bazy danych, hasło i tak dalej. Tego ciągu można użyć do nawiązania połączenia z serwerem z poziomu kodu i aplikacji.

## <a name="next-steps"></a>Następne kroki
- Aby uzyskać więcej informacji o bibliotekach połączeń, zobacz temat [koncepcje — biblioteki połączeń](./concepts-connection-libraries.md).
