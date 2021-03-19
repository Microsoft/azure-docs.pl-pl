---
title: Połączenie szyfrowane przy użyciu protokołu TLS/SSL w Azure Database for MySQL-elastycznym serwerze
description: Instrukcje i informacje dotyczące sposobu nawiązywania połączenia przy użyciu protokołu TLS/SSL w Azure Database for MySQL-elastycznym serwerze.
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: conceptual
ms.date: 09/21/2020
ms.openlocfilehash: 24a8dd4d21cb6ab6edeb985db4e6e6a1349a758d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "90940490"
---
# <a name="encrypted-connectivity-using-transport-layer-security-tls-12-in-azure-database-for-mysql---flexible-server"></a>Połączenie szyfrowane przy użyciu Transport Layer Security (TLS 1,2) w Azure Database for MySQL-elastycznym serwerze

> [!IMPORTANT]
> Azure Database for MySQL elastyczny serwer jest obecnie w publicznej wersji zapoznawczej

Azure Database for MySQL elastyczny serwer obsługuje łączenie aplikacji klienckich z usługą MySQL przy użyciu protokołu Transport Layer Security (TLS), wcześniej znanego jako Secure Sockets Layer (SSL). TLS jest standardowym protokołem, który zapewnia szyfrowane połączenia sieciowe między serwerem bazy danych i aplikacjami klienckimi, co pozwala na przestrzeganie wymagań dotyczących zgodności.

Azure Database for MySQL elastyczny serwer obsługuje tylko połączenia szyfrowane przy użyciu Transport Layer Security (TLS 1,2) i wszystkie połączenia przychodzące z protokołem TLS 1,0 i TLS 1,1 zostaną odrzucone. W przypadku wszystkich elastycznych serwerów wymuszania połączeń TLS jest włączone i nie można wyłączyć protokołu TLS/SSL w celu nawiązania połączenia z serwerem elastycznym.

## <a name="applications-that-require-certificate-verification-for-tlsssl-connectivity"></a>Aplikacje wymagające weryfikacji certyfikatu dla połączeń TLS/SSL
W niektórych przypadkach aplikacje wymagają lokalnego pliku certyfikatu wygenerowanego na podstawie pliku certyfikatu zaufanego urzędu certyfikacji w celu bezpiecznego nawiązywania połączenia. Azure Database for MySQL elastyczny serwer używa *globalnego głównego urzędu certyfikacji DigiCert*. Pobierz ten certyfikat wymagany do komunikacji za pośrednictwem protokołu SSL z [globalnego głównego urzędu certyfikacji DigiCert](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem) i Zapisz plik certyfikatu w preferowanej lokalizacji. Na przykład w tym samouczku używamy `c:\ssl` .

### <a name="connect-using-mysql-command-line-client-with-tlsssl"></a>Nawiązywanie połączenia przy użyciu klienta wiersza polecenia MySQL z protokołem TLS/SSL

Jeśli serwer elastyczny został utworzony z *dostępem prywatnym (Integracja z siecią wirtualną)*, należy połączyć się z serwerem z zasobu w ramach tej samej sieci wirtualnej co serwer. Można utworzyć maszynę wirtualną i dodać ją do sieci wirtualnej utworzonej przy użyciu elastycznego serwera.

Jeśli został utworzony serwer elastyczny z *dostępem publicznym (dozwolone adresy IP)*, możesz dodać lokalny adres IP do listy reguł zapory na serwerze.

Aby nawiązać połączenie z serwerem ze środowiska lokalnego, możesz wybrać opcję [mysql.exe](https://dev.mysql.com/doc/refman/8.0/en/mysql.html) lub [MySQL Workbench](./connect-workbench.md)-->. 

Poniższy przykład pokazuje, jak nawiązać połączenie z serwerem za pomocą interfejsu wiersza polecenia MySQL. Użyj `--ssl-mode=REQUIRED` Ustawienia parametrów połączenia, aby wymusić weryfikację certyfikatu TLS/SSL. Przekaż ścieżkę pliku certyfikatu lokalnego do `--ssl-ca` parametru. Zastąp wartości rzeczywistą nazwą serwera i hasłem. 

```bash
 mysql.exe -h mydemoserver.mysql.database.azure.com -u myadmin -p --ssl-mode=REQUIRED --ssl-ca=c:\ssl\DigiCertGlobalRootCA.crt.pem
```
> [!Note]
> Upewnij się, że wartość jest `--ssl-ca` zgodna z ścieżką pliku dla zapisywanych certyfikatów.

### <a name="verify-the-tlsssl-connection"></a>Weryfikowanie połączenia TLS/SSL

Uruchom polecenie MySQL **status** , aby sprawdzić, czy nawiązano połączenie z serwerem MySQL przy użyciu protokołu TLS/SSL:

```dos
mysql> status
```
Upewnij się, że połączenie jest zaszyfrowane, przeglądając dane wyjściowe, które powinny być wyświetlane:  **SSL: szyfr w użyciu jest AES256-SHA**. Ten mechanizm szyfrowania pokazuje przykład i oparty na kliencie, można zobaczyć inny mechanizm szyfrowania.

## <a name="ensure-your-application-or-framework-supports-tls-connections"></a>Upewnij się, że aplikacja lub platforma obsługuje połączenia TLS

Niektóre struktury aplikacji korzystające z programu MySQL dla usług baz danych nie domyślnie włączają TLS podczas instalacji. Serwer MySQL wymusza połączenia protokołu TLS, ale jeśli aplikacja nie jest skonfigurowana dla protokołu TLS, nawiązanie połączenia z serwerem bazy danych przez aplikację może zakończyć się niepowodzeniem. Zapoznaj się z dokumentacją aplikacji, aby dowiedzieć się, jak włączyć połączenia TLS.

## <a name="sample-code"></a>Przykładowy kod
Parametry połączenia, które są wstępnie zdefiniowane na stronie "parametry połączenia" dostępne dla Twojego serwera w Azure Portal zawierają wymagania parametrów dla wspólnych języków do łączenia się z serwerem bazy danych przy użyciu protokołu TLS/SSL. Parametry protokołu TLS/SSL różnią się w zależności od łącznika. Na przykład "useSSL = true", "sslmode = Required" lub "ssl_verify_cert = true" oraz inne różnice.

Aby nawiązać szyfrowane połączenie z elastycznym serwerem za pośrednictwem protokołu TLS/SSL z aplikacji, zapoznaj się z poniższymi przykładami kodu:

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
