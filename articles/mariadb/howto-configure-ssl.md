---
title: Konfigurowanie usługi SSL — usługa Azure Database dla bazy danych MariaDB
description: Instrukcje dotyczące prawidłowego konfigurowania usługi Azure Database dla mariadb i skojarzonych aplikacji do prawidłowego korzystania z połączeń SSL
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 4/21/2020
ms.openlocfilehash: 0af94d04f4e50fd039f01c5746b3a5d47a3698b3
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81769942"
---
# <a name="configure-ssl-connectivity-in-your-application-to-securely-connect-to-azure-database-for-mariadb"></a>Konfigurowanie łączności SSL w aplikacji w celu bezpiecznego łączenia się z usługą Azure Database for MariaDB
Usługa Azure Database for MariaDB obsługuje łączenie serwera usługi Azure Database dla mariadb z aplikacjami klienckimi przy użyciu warstwy SSL (Secure Sockets Layer). Wymuszanie połączeń SSL między serwerem bazy danych a aplikacją kliencką ułatwia ochronę przed atakami typu man-in-the-middle dzięki szyfrowaniu strumienia danych między serwerem a aplikacją.

## <a name="obtain-ssl-certificate"></a>Uzyskiwanie certyfikatu SSL
Pobierz certyfikat potrzebny do komunikowania się za pomocą ssl z usługą Azure Database dla serwera MariaDB i [https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) zapisać plik certyfikatu na dysku lokalnym (w tym samouczku używa c:\ssl na przykład).
**W przypadku programów Microsoft Internet Explorer i Microsoft Edge:** Po zakończeniu pobierania zmień nazwę certyfikatu na BaltimoreCyberTrustRoot.crt.pem.

## <a name="bind-ssl"></a>Powiąż SSL

### <a name="connecting-to-server-using-mysql-workbench-over-ssl"></a>Łączenie się z serwerem przy użyciu mysql workbench przez SSL
Skonfiguruj mysql workbench, aby bezpiecznie łączyć się za pomocą ssl. 

1. W oknie dialogowym Konfigurowanie nowego połączenia przejdź do karty **SSL.** 

1. Zaktualizuj pole **Użyj protokołu SSL** do "Wymagaj".

1. W polu **Plik urzędu certyfikacji SSL wprowadź** lokalizację pliku pliku **BaltimoreCyberTrustRoot.crt.pem**. 
    
    ![Zapisz konfigurację SSL](./media/howto-configure-ssl/mysql-workbench-ssl.png)

W przypadku istniejących połączeń można powiązać SSL, klikając prawym przyciskiem myszy ikonę połączenia i wybrać edytuj. Następnie przejdź do karty **SSL** i powiąż plik cert.

### <a name="connecting-to-server-using-the-mysql-cli-over-ssl"></a>Łączenie się z serwerem przy użyciu interfejsu wiersza polecenia MySQL przez SSL
Innym sposobem powiązania certyfikatu SSL jest użycie interfejsu wiersza polecenia MySQL przez wykonanie następujących poleceń. 

```bash
mysql.exe -h mydemoserver.mariadb.database.azure.com -u Username@mydemoserver -p --ssl-mode=REQUIRED --ssl-ca=c:\ssl\BaltimoreCyberTrustRoot.crt.pem
```

> [!NOTE]
> Podczas korzystania z interfejsu wiersza polecenia MySQL w `SSL connection error: Certificate signature check failed`systemie Windows może pojawić się błąd . W takim przypadku `--ssl-mode=REQUIRED --ssl-ca={filepath}` należy `--ssl`zastąpić parametry parametrem .

## <a name="enforcing-ssl-connections-in-azure"></a>Wymuszanie połączeń SSL na platformie Azure 
### <a name="using-the-azure-portal"></a>Korzystanie z witryny Azure Portal
Korzystając z witryny Azure portal, odwiedź usługę Azure Database dla serwera MariaDB, a następnie kliknij pozycję **Zabezpieczenia połączenia**. Użyj przycisku przełączania, aby włączyć lub wyłączyć ustawienie **Wymuszanie połączenia SSL,** a następnie kliknij przycisk **Zapisz**. Firma Microsoft zaleca, aby zawsze włączać ustawienie **Wymuszanie połączenia SSL** w celu zwiększenia bezpieczeństwa.
![enable-ssl](./media/howto-configure-ssl/enable-ssl.png)

### <a name="using-azure-cli"></a>Korzystanie z interfejsu wiersza polecenia platformy Azure
Parametr **wymuszania ssl można** włączyć lub wyłączyć, używając odpowiednio wartości Włączone lub Wyłączone w usłudze Azure CLI.
```azurecli-interactive
az mariadb server update --resource-group myresource --name mydemoserver --ssl-enforcement Enabled
```

## <a name="verify-the-ssl-connection"></a>Sprawdź połączenie SSL
Wykonaj polecenie **stanu** mysql, aby sprawdzić, czy masz połączenie z serwerem MariaDB przy użyciu ssl:
```sql
status
```
Potwierdź, że połączenie jest szyfrowane przez przejrzenie danych wyjściowych, które powinny być wyświetlane: **SSL: Szyfr w użyciu jest AES256-SHA** 

## <a name="sample-code"></a>Przykładowy kod
Aby ustanowić bezpieczne połączenie z usługą Azure Database for MariaDB za pomocą ssl z aplikacji, zapoznaj się z następującymi przykładami kodu:

### <a name="php"></a>PHP
```php
$conn = mysqli_init();
mysqli_ssl_set($conn,NULL,NULL, "/var/www/html/BaltimoreCyberTrustRoot.crt.pem", NULL, NULL) ; 
mysqli_real_connect($conn, 'mydemoserver.mariadb.database.azure.com', 'myadmin@mydemoserver', 'yourpassword', 'quickstartdb', 3306, MYSQLI_CLIENT_SSL, MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT);
if (mysqli_connect_errno($conn)) {
die('Failed to connect to MySQL: '.mysqli_connect_error());
}
```
### <a name="python-mysqlconnector-python"></a>Python (Python MySQLConnector)
```python
try:
    conn = mysql.connector.connect(user='myadmin@mydemoserver',
                                   password='yourpassword',
                                   database='quickstartdb',
                                   host='mydemoserver.mariadb.database.azure.com',
                                   ssl_ca='/var/www/html/BaltimoreCyberTrustRoot.crt.pem')
except mysql.connector.Error as err:
    print(err)
```
### <a name="python-pymysql"></a>Python (PyMySQL)
```python
conn = pymysql.connect(user='myadmin@mydemoserver',
                       password='yourpassword',
                       database='quickstartdb',
                       host='mydemoserver.mariadb.database.azure.com',
                       ssl={'ssl': {'ca': '/var/www/html/BaltimoreCyberTrustRoot.crt.pem'}})
```

### <a name="ruby"></a>Ruby
```ruby
client = Mysql2::Client.new(
        :host     => 'mydemoserver.mariadb.database.azure.com', 
        :username => 'myadmin@mydemoserver',      
        :password => 'yourpassword',    
        :database => 'quickstartdb',
        :sslca => '/var/www/html/BaltimoreCyberTrustRoot.crt.pem'
        :ssl_mode => 'required'
    )
```
#### <a name="ruby-on-rails"></a>Ruby on Rails
```ruby
default: &default
  adapter: mysql2
  username: username@mydemoserver
  password: yourpassword
  host: mydemoserver.mariadb.database.azure.com
  sslca: BaltimoreCyberTrustRoot.crt.pem
  sslverify: true
```

### <a name="golang"></a>Golang
```go
rootCertPool := x509.NewCertPool()
pem, _ := ioutil.ReadFile("/var/www/html/BaltimoreCyberTrustRoot.crt.pem")
if ok := rootCertPool.AppendCertsFromPEM(pem); !ok {
    log.Fatal("Failed to append PEM.")
}
mysql.RegisterTLSConfig("custom", &tls.Config{RootCAs: rootCertPool})
var connectionString string
connectionString = fmt.Sprintf("%s:%s@tcp(%s:3306)/%s?allowNativePasswords=true&tls=custom",'myadmin@mydemoserver' , 'yourpassword', 'mydemoserver.mariadb.database.azure.com', 'quickstartdb') 
db, _ := sql.Open("mysql", connectionString)
```
### <a name="java-jdbc"></a>Java (JDBC)
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

url = String.format("jdbc:mysql://%s/%s?serverTimezone=UTC&useSSL=true", 'mydemoserver.mariadb.database.azure.com', 'quickstartdb');
properties.setProperty("user", 'myadmin@mydemoserver');
properties.setProperty("password", 'yourpassword');
conn = DriverManager.getConnection(url, properties);
```
### <a name="java-mariadb"></a>Java (MariaDB)
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

url = String.format("jdbc:mariadb://%s/%s?useSSL=true&trustServerCertificate=true", 'mydemoserver.mariadb.database.azure.com', 'quickstartdb');
properties.setProperty("user", 'myadmin@mydemoserver');
properties.setProperty("password", 'yourpassword');
conn = DriverManager.getConnection(url, properties);
```

### <a name="net-mysqlconnector"></a>.NET (Połączenie MySql)
```csharp
var builder = new MySqlConnectionStringBuilder
{
    Server = "mydemoserver.mysql.database.azure.com",
    UserID = "myadmin@mydemoserver",
    Password = "yourpassword",
    Database = "quickstartdb",
    SslMode = MySqlSslMode.VerifyCA,
    CACertificateFile = "BaltimoreCyberTrustRoot.crt.pem",
};
using (var connection = new MySqlConnection(builder.ConnectionString))
{
    connection.Open();
}
```

<!-- 
## Next steps
Review various application connectivity options following [Connection libraries for Azure Database for MariaDB](concepts-connection-libraries.md)
-->
