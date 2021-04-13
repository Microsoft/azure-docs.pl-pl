---
title: Połączenie szyfrowane przy użyciu protokołu TLS/SSL w Azure Database for MySQL-elastycznym serwerze
description: Instrukcje i informacje dotyczące sposobu nawiązywania połączenia przy użyciu protokołu TLS/SSL w Azure Database for MySQL-elastycznym serwerze.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 09/21/2020
ms.openlocfilehash: 6dbb1b46aef40986fc2d601aee152aed02591ac0
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107312607"
---
# <a name="connect-to-azure-database-for-mysql---flexible-server-with-encrypted-connections"></a>Nawiązywanie połączenia z serwerem elastycznym Azure Database for MySQL z połączeniami szyfrowanymi

> [!IMPORTANT]
> Azure Database for MySQL elastyczny serwer jest obecnie w publicznej wersji zapoznawczej

Azure Database for MySQL elastyczny serwer obsługuje łączenie aplikacji klienckich z serwerem MySQL przy użyciu protokołu Secure Sockets Layer (SSL) z szyfrowaniem TLS (Transport Layer Security). TLS jest standardowym protokołem, który zapewnia szyfrowane połączenia sieciowe między serwerem bazy danych i aplikacjami klienckimi, co pozwala na przestrzeganie wymagań dotyczących zgodności.

Azure Database for MySQL elastyczny serwer obsługuje połączenia szyfrowane przy użyciu Transport Layer Security (TLS 1,2) domyślnie, a wszystkie połączenia przychodzące z protokołem TLS 1,0 i TLS 1,1 będą domyślnie odrzucane. Konfigurację wymuszania połączenia szyfrowanego lub konfiguracji protokołu TLS na serwerze elastycznym można zmienić zgodnie z opisem w tym artykule. 

Poniżej wymieniono różne konfiguracje protokołów SSL i TLS dostępne dla serwera elastycznego:

| Scenariusz   | Ustawienia parametrów serwera      | Opis                                    |
|------------|--------------------------------|------------------------------------------------|
|Wyłącz wymuszanie protokołu SSL | require_secure_transport = wyłączone |Jeśli Starsza aplikacja nie obsługuje szyfrowanych połączeń z serwerem MySQL, można wyłączyć wymuszanie szyfrowanych połączeń na serwerze elastycznym przez ustawienie require_secure_transport = OFF.|
|Wymuszanie protokołu SSL w wersji TLS < 1,2 | require_secure_transport = ON i tls_version = TLSV1 lub TLSV 1.1| Jeśli Starsza aplikacja obsługuje połączenia szyfrowane, ale wymaga wersji TLS < 1,2, można włączyć połączenia szyfrowane, ale skonfigurować elastyczny serwer w taki sposób, aby zezwalał na połączenia z wersją protokołu TLS (v 1.0 lub v 1.1) obsługiwaną przez aplikację|
|Wymuszaj protokół SSL z wersją protokołu TLS = 1.2 (Konfiguracja domyślna)|require_secure_transport = ON i tls_version = TLSV 1.2| Jest to zalecana i domyślna konfiguracja dla elastycznego serwera.|
|Wymuszanie protokołu SSL w wersji 1.3 (obsługiwanej przez program MySQL v 8.0 lub nowszy)| require_secure_transport = ON i tls_version = TLSV 1.3| Jest to przydatne i zalecane w przypadku tworzenia nowych aplikacji|

> [!Note]
> Zmiany szyfrowania SSL na serwerze elastycznym nie są obsługiwane. Mechanizmy szyfrowania FIPS są wymuszane domyślnie, gdy tls_version jest ustawiona na TLS w wersji 1,2. W przypadku wersji TLS innych niż 1,2 szyfrowanie SSL jest ustawione na domyślne ustawienia, które są dostarczane z instalacją społeczności MySQL.

W tym artykule dowiesz się, jak:
* Skonfiguruj elastyczny serwer 
  * Z wyłączonym protokołem SSL 
  * Z protokołem SSL wymuszania przy użyciu protokołu TLS w wersji < 1,2
* Nawiązywanie połączenia z elastycznym serwerem przy użyciu wiersza polecenia MySQL 
  * Z wyłączonymi szyfrowanymi połączeniami
  * Z włączonymi szyfrowanymi połączeniami
* Sprawdź stan szyfrowania połączenia
* Łączenie się z elastycznym serwerem z szyfrowanymi połączeniami przy użyciu różnych platform aplikacji

## <a name="disable-ssl-enforcement-on-your-flexible-server"></a>Wyłącz wymuszanie protokołu SSL na elastycznym serwerze
Jeśli aplikacja kliencka nie obsługuje szyfrowanych połączeń, należy wyłączyć wymuszanie połączeń szyfrowanych na serwerze elastycznym. Aby wyłączyć wymuszanie połączeń szyfrowanych, należy ustawić parametr require_secure_transport Server na wartość OFF, jak pokazano na zrzucie ekranu, i zapisać konfigurację parametrów serwera, aby zaczęła obowiązywać. require_secure_transport to **dynamiczny parametr serwera** , który zacznie obowiązywać natychmiast i nie wymaga ponownego uruchomienia serwera.

> :::image type="content" source="./media/how-to-connect-tls-ssl/disable-ssl.png" alt-text="Zrzut ekranu przedstawiający sposób wyłączania protokołu SSL z serwerem elastycznym Azure Database for MySQL.":::

### <a name="connect-using-mysql-command-line-client-with-ssl-disabled"></a>Nawiązywanie połączenia przy użyciu klienta wiersza polecenia MySQL z wyłączonym protokołem SSL

Poniższy przykład pokazuje, jak nawiązać połączenie z serwerem za pomocą interfejsu wiersza polecenia MySQL. `--ssl-mode=DISABLED`Aby wyłączyć połączenie TLS/SSL z klienta MySQL, użyj ustawienia parametrów połączenia. Zastąp wartości rzeczywistą nazwą serwera i hasłem. 

```bash
 mysql.exe -h mydemoserver.mysql.database.azure.com -u myadmin -p --ssl-mode=DISABLED 
```
Należy pamiętać, że ustawienie require_secure_transport wyłączone nie oznacza, że połączenia szyfrowane nie będą obsługiwane po stronie serwera. Jeśli ustawisz wartość require_secure_transport na OFF na serwerze elastycznym, ale jeśli klient nawiąże połączenie z szyfrowanym połączeniem, nadal zostanie zaakceptowany. Poniższe połączenie korzystające z klienta MySQL na elastycznym serwerze skonfigurowanym z require_secure_transport = OFF również będzie działało, jak pokazano poniżej.

```bash
 mysql.exe -h mydemoserver.mysql.database.azure.com -u myadmin -p --ssl-mode=REQUIRED
```
```output
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 17
Server version: 5.7.29-log MySQL Community Server (GPL)

Copyright (c) 2000, 2020, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> show global variables like '%require_secure_transport%';
+--------------------------+-------+
| Variable_name            | Value |
+--------------------------+-------+
| require_secure_transport | OFF   |
+--------------------------+-------+
1 row in set (0.02 sec)
```

Podsumowując, require_secure_transport = OFF ustawienie wyłącza wymuszanie szyfrowanych połączeń na elastycznym serwerze i zezwala na nieszyfrowane połączenia z serwerem od klienta oprócz szyfrowanych połączeń.

## <a name="enforce-ssl-with-tls-version--12"></a>Wymuszanie protokołu SSL w wersji TLS < 1,2

Jeśli aplikacja obsługuje połączenia z serwerem MySQL przy użyciu protokołu SSL, ale obsługuje protokół TLS w < wersji 1,2, należy ustawić parametr serwera protokołu TLS na serwerze elastycznym. Aby ustawić wersje protokołu TLS, które mają być obsługiwane przez elastyczny serwer, należy ustawić parametr tls_version Server na TLSV1, TLSV 1.1 lub TLSV1 i TLSV 1.1, jak pokazano na zrzucie ekranu, i zapisać konfigurację parametrów serwera, aby zaczęła obowiązywać. tls_version to **statyczny parametr serwera** , który będzie wymagał ponownego uruchomienia serwera, aby parametr został uwzględniony.

> :::image type="content" source="./media/how-to-connect-tls-ssl/tls-version.png" alt-text="Zrzut ekranu przedstawiający sposób ustawiania wersji protokołu TLS dla Azure Database for MySQL elastycznego serwera.":::

## <a name="connect-using-mysql-command-line-client-with-tlsssl"></a>Nawiązywanie połączenia przy użyciu klienta wiersza polecenia MySQL z protokołem TLS/SSL

### <a name="download-the-public-ssl-certificate"></a>Pobieranie publicznego certyfikatu SSL
Aby korzystać z szyfrowanych połączeń z aplikacjami klienckimi, należy pobrać [publiczny certyfikat SSL](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem) , który jest również dostępny w bloku sieci Azure Portal, jak pokazano na poniższym zrzucie ekranu.

> :::image type="content" source="./media/how-to-connect-tls-ssl/download-ssl.png" alt-text="Zrzut ekranu przedstawiający sposób pobierania publicznego certyfikatu SSL z Azure Portal.":::

Zapisz plik certyfikatu w preferowanej lokalizacji. Na przykład w tym samouczku `c:\ssl` jest używane lub `\var\www\html\bin` w środowisku lokalnym lub w środowisku klienta, w którym aplikacja jest hostowana. Dzięki temu aplikacje mogą bezpiecznie łączyć się z bazą danych za pośrednictwem protokołu SSL.

Jeśli serwer elastyczny został utworzony z *dostępem prywatnym (Integracja z siecią wirtualną)*, należy połączyć się z serwerem z zasobu w ramach tej samej sieci wirtualnej co serwer. Można utworzyć maszynę wirtualną i dodać ją do sieci wirtualnej utworzonej przy użyciu elastycznego serwera.

Jeśli został utworzony serwer elastyczny z *dostępem publicznym (dozwolone adresy IP)*, możesz dodać lokalny adres IP do listy reguł zapory na serwerze.

Aby nawiązać połączenie z serwerem ze środowiska lokalnego, możesz wybrać opcję [mysql.exe](https://dev.mysql.com/doc/refman/8.0/en/mysql.html) lub [MySQL Workbench](./connect-workbench.md)-->. 

Poniższy przykład pokazuje, jak nawiązać połączenie z serwerem za pomocą interfejsu wiersza polecenia MySQL. Użyj `--ssl-mode=REQUIRED` Ustawienia parametrów połączenia, aby wymusić weryfikację certyfikatu TLS/SSL. Przekaż ścieżkę pliku certyfikatu lokalnego do `--ssl-ca` parametru. Zastąp wartości rzeczywistą nazwą serwera i hasłem. 

```bash
sudo apt-get install mysql-client
wget --no-check-certificate https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem
mysql -h mydemoserver.mysql.database.azure.com -u mydemouser -p --ssl-mode=REQUIRED --ssl-ca=DigiCertGlobalRootCA.crt.pem
```
> [!Note]
> Upewnij się, że wartość jest `--ssl-ca` zgodna z ścieżką pliku dla zapisywanych certyfikatów.

W przypadku próby nawiązania połączenia z serwerem z nieszyfrowanymi połączeniami zostanie wyświetlony komunikat o błędzie informujący o połączeniach korzystających z niezabezpieczonego transportu, podobny do poniższego:

```output
ERROR 3159 (HY000): Connections using insecure transport are prohibited while --require_secure_transport=ON.
```

## <a name="verify-the-tlsssl-connection"></a>Weryfikowanie połączenia TLS/SSL

Uruchom polecenie MySQL **status** , aby sprawdzić, czy nawiązano połączenie z serwerem MySQL przy użyciu protokołu TLS/SSL:

```dos
mysql> status
```
Upewnij się, że połączenie zostało zaszyfrowane, przeglądając dane wyjściowe, które powinny być wyświetlane: * * SSL: szyfr w użyciu jest * *. Ten mechanizm szyfrowania pokazuje przykład i oparty na kliencie, można zobaczyć inny mechanizm szyfrowania.

## <a name="connect-to-your-flexible-server-with-encrypted-connections-using-various-application-frameworks"></a>Łączenie się z elastycznym serwerem z szyfrowanymi połączeniami przy użyciu różnych platform aplikacji

Parametry połączenia, które są wstępnie zdefiniowane na stronie "parametry połączenia" dostępne dla Twojego serwera w Azure Portal zawierają wymagania parametrów dla wspólnych języków do łączenia się z serwerem bazy danych przy użyciu protokołu TLS/SSL. Parametry protokołu TLS/SSL różnią się w zależności od łącznika. Na przykład "useSSL = true", "sslmode = Required" lub "ssl_verify_cert = true" oraz inne różnice.

Aby nawiązać szyfrowane połączenie z elastycznym serwerem za pośrednictwem protokołu TLS/SSL z aplikacji, zapoznaj się z poniższymi przykładami kodu:

### <a name="wordpress"></a>WordPress
Pobierz [publiczny certyfikat SSL](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem) i Dodaj następujące wiersze w wp-config. php po wierszu ```// ** MySQL settings - You can get this info from your web host ** //``` .

```php
//** Connect with SSL** //
define('MYSQL_CLIENT_FLAGS', MYSQLI_CLIENT_SSL);
//** SSL CERT **//
define('MYSQL_SSL_CERT','/FULLPATH/on-client/to/DigiCertGlobalRootCA.crt.pem');
```

### <a name="php"></a>PHP

```php
$conn = mysqli_init();
mysqli_ssl_set($conn,NULL,NULL, "/var/www/html/DigiCertGlobalRootCA.crt.pem", NULL, NULL);
mysqli_real_connect($conn, 'mydemoserver.mysql.database.azure.com', 'myadmin', 'yourpassword', 'quickstartdb', 3306, MYSQLI_CLIENT_SSL);
if (mysqli_connect_errno($conn)) {
die('Failed to connect to MySQL: '.mysqli_connect_error());
}
```

### <a name="php-using-pdo"></a>PHP (przy użyciu PDO)

```phppdo
$options = array(
    PDO::MYSQL_ATTR_SSL_CA => '/var/www/html/DigiCertGlobalRootCA.crt.pem'
);
$db = new PDO('mysql:host=mydemoserver.mysql.database.azure.com;port=3306;dbname=databasename', 'myadmin', 'yourpassword', $options);
```

### <a name="python-mysqlconnector-python"></a>Python (MySQLConnector Python)

```python
try:
    conn = mysql.connector.connect(user='myadmin',
                                   password='yourpassword',
                                   database='quickstartdb',
                                   host='mydemoserver.mysql.database.azure.com',
                                   ssl_ca='/var/www/html/DigiCertGlobalRootCA.crt.pem')
except mysql.connector.Error as err:
    print(err)
```

### <a name="python-pymysql"></a>Python (PyMySQL)

```python
conn = pymysql.connect(user='myadmin',
                       password='yourpassword',
                       database='quickstartdb',
                       host='mydemoserver.mysql.database.azure.com',
                       ssl={'ca': '/var/www/html/DigiCertGlobalRootCA.crt.pem'})
```

### <a name="django-pymysql"></a>Django (PyMySQL)

```python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'quickstartdb',
        'USER': 'myadmin',
        'PASSWORD': 'yourpassword',
        'HOST': 'mydemoserver.mysql.database.azure.com',
        'PORT': '3306',
        'OPTIONS': {
            'ssl': {'ca': '/var/www/html/DigiCertGlobalRootCA.crt.pem'}
        }
    }
}
```

### <a name="ruby"></a>Ruby

```ruby
client = Mysql2::Client.new(
        :host     => 'mydemoserver.mysql.database.azure.com',
        :username => 'myadmin',
        :password => 'yourpassword',
        :database => 'quickstartdb',
        :sslca => '/var/www/html/DigiCertGlobalRootCA.crt.pem'
    )
```

### <a name="golang"></a>Golang

```go
rootCertPool := x509.NewCertPool()
pem, _ := ioutil.ReadFile("/var/www/html/DigiCertGlobalRootCA.crt.pem")
if ok := rootCertPool.AppendCertsFromPEM(pem); !ok {
    log.Fatal("Failed to append PEM.")
}
mysql.RegisterTLSConfig("custom", &tls.Config{RootCAs: rootCertPool})
var connectionString string
connectionString = fmt.Sprintf("%s:%s@tcp(%s:3306)/%s?allowNativePasswords=true&tls=custom",'myadmin' , 'yourpassword', 'mydemoserver.mysql.database.azure.com', 'quickstartdb')
db, _ := sql.Open("mysql", connectionString)
```

### <a name="java-mysql-connector-for-java"></a>Java (łącznik MySQL dla języka Java)

```java
# generate truststore and keystore in code
String importCert = " -import "+
    " -alias mysqlServerCACert "+
    " -file " + ssl_ca +
    " -keystore truststore "+
    " -trustcacerts " +
    " -storepass password -noprompt ";
String genKey = " -genkey -keyalg rsa " +
    " -alias mysqlClientCertificate -keystore keystore " +
    " -storepass password123 -keypass password " +
    " -dname CN=MS ";
sun.security.tools.keytool.Main.main(importCert.trim().split("\\s+"));
sun.security.tools.keytool.Main.main(genKey.trim().split("\\s+"));

# use the generated keystore and truststore
System.setProperty("javax.net.ssl.keyStore","path_to_keystore_file");
System.setProperty("javax.net.ssl.keyStorePassword","password");
System.setProperty("javax.net.ssl.trustStore","path_to_truststore_file");
System.setProperty("javax.net.ssl.trustStorePassword","password");

url = String.format("jdbc:mysql://%s/%s?serverTimezone=UTC&useSSL=true", 'mydemoserver.mysql.database.azure.com', 'quickstartdb');
properties.setProperty("user", 'myadmin');
properties.setProperty("password", 'yourpassword');
conn = DriverManager.getConnection(url, properties);
```

### <a name="java-mariadb-connector-for-java"></a>Java (MariaDB Connector for Java)

```java
# generate truststore and keystore in code
String importCert = " -import "+
    " -alias mysqlServerCACert "+
    " -file " + ssl_ca +
    " -keystore truststore "+
    " -trustcacerts " +
    " -storepass password -noprompt ";
String genKey = " -genkey -keyalg rsa " +
    " -alias mysqlClientCertificate -keystore keystore " +
    " -storepass password123 -keypass password " +
    " -dname CN=MS ";
sun.security.tools.keytool.Main.main(importCert.trim().split("\\s+"));
sun.security.tools.keytool.Main.main(genKey.trim().split("\\s+"));

# use the generated keystore and truststore
System.setProperty("javax.net.ssl.keyStore","path_to_keystore_file");
System.setProperty("javax.net.ssl.keyStorePassword","password");
System.setProperty("javax.net.ssl.trustStore","path_to_truststore_file");
System.setProperty("javax.net.ssl.trustStorePassword","password");

url = String.format("jdbc:mariadb://%s/%s?useSSL=true&trustServerCertificate=true", 'mydemoserver.mysql.database.azure.com', 'quickstartdb');
properties.setProperty("user", 'myadmin');
properties.setProperty("password", 'yourpassword');
conn = DriverManager.getConnection(url, properties);
```

### <a name="net-mysqlconnector"></a>.NET (MySqlConnector)

```csharp
var builder = new MySqlConnectionStringBuilder
{
    Server = "mydemoserver.mysql.database.azure.com",
    UserID = "myadmin",
    Password = "yourpassword",
    Database = "quickstartdb",
    SslMode = MySqlSslMode.VerifyCA,
    SslCa = "DigiCertGlobalRootCA.crt.pem",
};
using (var connection = new MySqlConnection(builder.ConnectionString))
{
    connection.Open();
}
```

## <a name="next-steps"></a>Następne kroki
- [Łączenie i wykonywanie zapytań dotyczących danych za pomocą programu MySQL Workbench w Azure Database for MySQL elastycznym serwerze](./connect-workbench.md)
- [Łączenie i wykonywanie zapytań dotyczących danych w Azure Database for MySQL elastycznym serwerze przy użyciu języka PHP](./connect-php.md)
- [Utwórz Azure Database for MySQL elastyczną sieć wirtualną serwera i zarządzaj nią przy użyciu interfejsu wiersza polecenia platformy Azure](./how-to-manage-virtual-network-cli.md).
- Dowiedz się więcej o [sieci w Azure Database for MySQL elastycznym serwerze](./concepts-networking.md)
- Dowiedz się więcej o [Azure Database for MySQL elastycznych regułach zapory serwera](./concepts-networking.md#public-access-allowed-ip-addresses)
