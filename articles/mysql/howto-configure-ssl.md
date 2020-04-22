---
title: Konfigurowanie usługi SSL — usługa Azure Database dla mysql
description: Instrukcje dotyczące prawidłowego konfigurowania usługi Azure Database dla mysql i skojarzonych aplikacji do prawidłowego korzystania z połączeń SSL
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 4/21/2020
ms.openlocfilehash: a68b63fa190aa67125b2bf8c09ae7f31aedc3905
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770265"
---
# <a name="configure-ssl-connectivity-in-your-application-to-securely-connect-to-azure-database-for-mysql"></a>Konfigurowanie łączności SSL w aplikacji w celu bezpiecznego łączenia się z usługą Azure Database for MySQL
Usługa Azure Database for MySQL obsługuje łączenie serwera usługi Azure Database dla MySQL z aplikacjami klienckimi przy użyciu warstwy SSL (Secure Sockets Layer). Wymuszanie połączeń SSL między serwerem bazy danych a aplikacją kliencką ułatwia ochronę przed atakami typu man-in-the-middle dzięki szyfrowaniu strumienia danych między serwerem a aplikacją.

## <a name="step-1-obtain-ssl-certificate"></a>Krok 1: Uzyskaj certyfikat SSL
Pobierz certyfikat potrzebny do komunikowania się za pomocą ssl [https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) z usługą Azure Database dla serwera MySQL i zapisz plik certyfikatu na dysku lokalnym (w tym samouczku używa c:\ssl na przykład).
**W przypadku programów Microsoft Internet Explorer i Microsoft Edge:** Po zakończeniu pobierania zmień nazwę certyfikatu na BaltimoreCyberTrustRoot.crt.pem.

## <a name="step-2-bind-ssl"></a>Krok 2: Powiąż SSL

Aby uzyskać określone parametry połączenia języka programowania, zapoznaj się z [poniższym przykładowym kodem.](howto-configure-ssl.md#sample-code)

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
mysql.exe -h mydemoserver.mysql.database.azure.com -u Username@mydemoserver -p --ssl-mode=REQUIRED --ssl-ca=c:\ssl\BaltimoreCyberTrustRoot.crt.pem
```

> [!NOTE]
> Podczas korzystania z interfejsu wiersza polecenia MySQL w `SSL connection error: Certificate signature check failed`systemie Windows może pojawić się błąd . W takim przypadku `--ssl-mode=REQUIRED --ssl-ca={filepath}` należy `--ssl`zastąpić parametry parametrem .

## <a name="step-3--enforcing-ssl-connections-in-azure"></a>Krok 3: Wymuszanie połączeń SSL na platformie Azure 
### <a name="using-the-azure-portal"></a>Korzystanie z witryny Azure Portal
Korzystając z witryny Azure portal, odwiedź swoją bazę danych platformy Azure dla serwera MySQL, a następnie kliknij pozycję **Zabezpieczenia połączenia**. Użyj przycisku przełączania, aby włączyć lub wyłączyć ustawienie **Wymuszanie połączenia SSL,** a następnie kliknij przycisk **Zapisz**. Firma Microsoft zaleca, aby zawsze włączać ustawienie **Wymuszanie połączenia SSL** w celu zwiększenia bezpieczeństwa.
![enable-ssl](./media/howto-configure-ssl/enable-ssl.png)

### <a name="using-azure-cli"></a>Korzystanie z interfejsu wiersza polecenia platformy Azure
Parametr **wymuszania ssl można** włączyć lub wyłączyć, używając odpowiednio wartości Włączone lub Wyłączone w usłudze Azure CLI.
```azurecli-interactive
az mysql server update --resource-group myresource --name mydemoserver --ssl-enforcement Enabled
```

## <a name="step-4-verify-the-ssl-connection"></a>Krok 4: Sprawdź połączenie SSL
Wykonaj polecenie **stanu** mysql, aby sprawdzić, czy masz połączenie z serwerem MySQL przy użyciu ssl:
```dos
mysql> status
```
Potwierdź, że połączenie jest szyfrowane przez przejrzenie danych wyjściowych, które powinny być wyświetlane: **SSL: Szyfr w użyciu jest AES256-SHA** 

## <a name="sample-code"></a>Przykładowy kod
Aby ustanowić bezpieczne połączenie z usługą Azure Database for MySQL za pomocą ssl z aplikacji, zapoznaj się z następującymi przykładami kodu:

Zapoznaj się z listą [zgodnych sterowników obsługiwanych](concepts-compatibility.md) przez usługę Azure Database for MySQL.

### <a name="php"></a>PHP
```php
$conn = mysqli_init();
mysqli_ssl_set($conn,NULL,NULL, "/var/www/html/BaltimoreCyberTrustRoot.crt.pem", NULL, NULL) ; 
mysqli_real_connect($conn, 'mydemoserver.mysql.database.azure.com', 'myadmin@mydemoserver', 'yourpassword', 'quickstartdb', 3306, MYSQLI_CLIENT_SSL);
if (mysqli_connect_errno($conn)) {
die('Failed to connect to MySQL: '.mysqli_connect_error());
}
```
### <a name="php-using-pdo"></a>PHP (korzystanie z ChNP)
```phppdo
$options = array(
    PDO::MYSQL_ATTR_SSL_CA => '/var/www/html/BaltimoreCyberTrustRoot.crt.pem'
);
$db = new PDO('mysql:host=mydemoserver.mysql.database.azure.com;port=3306;dbname=databasename', 'username@mydemoserver', 'yourpassword', $options);
```
### <a name="python-mysqlconnector-python"></a>Python (Python MySQLConnector)
```python
try:
    conn = mysql.connector.connect(user='myadmin@mydemoserver',
                                   password='yourpassword',
                                   database='quickstartdb',
                                   host='mydemoserver.mysql.database.azure.com',
                                   ssl_ca='/var/www/html/BaltimoreCyberTrustRoot.crt.pem')
except mysql.connector.Error as err:
    print(err)
```

### <a name="python-pymysql"></a>Python (PyMySQL)
```python
conn = pymysql.connect(user='myadmin@mydemoserver',
                       password='yourpassword',
                       database='quickstartdb',
                       host='mydemoserver.mysql.database.azure.com',
                       ssl={'ssl': {'ca': '/var/www/html/BaltimoreCyberTrustRoot.crt.pem'}})
```

### <a name="django-pymysql"></a>Django (PyMySQL)
```python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'quickstartdb',
        'USER': 'myadmin@mydemoserver',
        'PASSWORD': 'yourpassword',
        'HOST': 'mydemoserver.mysql.database.azure.com',
        'PORT': '3306',
        'OPTIONS': {
            'ssl': {'ca': '/var/www/html/BaltimoreCyberTrustRoot.crt.pem'}
        }
    }
}
```

### <a name="ruby"></a>Ruby
```ruby
client = Mysql2::Client.new(
        :host     => 'mydemoserver.mysql.database.azure.com',
        :username => 'myadmin@mydemoserver',
        :password => 'yourpassword',
        :database => 'quickstartdb',
        :ssl_ca => '/var/www/html/BaltimoreCyberTrustRoot.crt.pem'
    )
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
connectionString = fmt.Sprintf("%s:%s@tcp(%s:3306)/%s?allowNativePasswords=true&tls=custom",'myadmin@mydemoserver' , 'yourpassword', 'mydemoserver.mysql.database.azure.com', 'quickstartdb')   
db, _ := sql.Open("mysql", connectionString)
```
### <a name="java-mysql-connector-for-java"></a>Java (Łącznik MySQL dla Javy)
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
properties.setProperty("user", 'myadmin@mydemoserver');
properties.setProperty("password", 'yourpassword');
conn = DriverManager.getConnection(url, properties);
```
### <a name="java-mariadb-connector-for-java"></a>Java (Łącznik MariaDB dla Javy)
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

## <a name="next-steps"></a>Następne kroki
Przejrzyj różne opcje łączności aplikacji po [bibliotekach połączeń dla usługi Azure Database for MySQL](concepts-connection-libraries.md)
