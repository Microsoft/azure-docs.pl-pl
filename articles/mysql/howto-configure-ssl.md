---
title: Konfigurowanie protokołu SSL Azure Database for MySQL
description: Instrukcje dotyczące prawidłowego konfigurowania Azure Database for MySQL i skojarzonych aplikacji w celu poprawnego używania połączeń SSL
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 07/08/2020
ms.custom: devx-track-python, devx-track-csharp
ms.openlocfilehash: 1c3e5a44e01f3fa43b82644103066f5a03684ad2
ms.sourcegitcommit: 1cf157f9a57850739adef72219e79d76ed89e264
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/13/2020
ms.locfileid: "94591584"
---
# <a name="configure-ssl-connectivity-in-your-application-to-securely-connect-to-azure-database-for-mysql"></a>Skonfiguruj połączenie SSL w aplikacji, aby bezpiecznie połączyć się z Azure Database for MySQL

Azure Database for MySQL obsługuje łączenie serwera Azure Database for MySQL z aplikacjami klienckimi przy użyciu SSL (SSL). Wymuszanie połączeń SSL między serwerem bazy danych a aplikacją kliencką ułatwia ochronę przed atakami typu man-in-the-middle dzięki szyfrowaniu strumienia danych między serwerem a aplikacją.

## <a name="step-1-obtain-ssl-certificate"></a>Krok 1. Uzyskiwanie certyfikatu SSL

Pobierz certyfikat wymagany do komunikacji za pośrednictwem protokołu SSL z serwerem Azure Database for MySQL z [https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) i Zapisz plik certyfikatu na dysku lokalnym (na przykład w tym samouczku jest używany program c:\ssl).
**Dla programu Microsoft Internet Explorer i Microsoft Edge:** Po zakończeniu pobierania Zmień nazwę certyfikatu na BaltimoreCyberTrustRoot. CRT. pem.

>[!NOTE]
> Na podstawie opinii klientów przedłużono przestarzałą certyfikat główny dla istniejącego głównego urzędu certyfikacji Baltimore do 15 lutego 2021 (02/15/2021).

> [!IMPORTANT] 
> Certyfikat główny SSL jest ustawiany na wygasa od 15 lutego 2021 (02/15/2021). Zaktualizuj swoją aplikację, aby używała [nowego certyfikatu](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem). Aby dowiedzieć się więcej, zobacz [aktualizacje dotyczące planowanych certyfikatów](concepts-certificate-rotation.md)

Zobacz następujące linki dla certyfikatów serwerów w chmurach suwerennych: [Azure Government](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem), [Azure Chiny](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem)i [Azure (Niemcy](https://www.d-trust.net/cgi-bin/D-TRUST_Root_Class_3_CA_2_2009.crt)).

## <a name="step-2-bind-ssl"></a>Krok 2. wiązanie SSL

W przypadku określonych parametrów połączenia języka programowania zapoznaj się z [przykładowym kodem](howto-configure-ssl.md#sample-code) poniżej.

### <a name="connecting-to-server-using-mysql-workbench-over-ssl"></a>Łączenie z serwerem przy użyciu programu MySQL Workbench za pośrednictwem protokołu SSL

Skonfiguruj Workbench MySQL w celu bezpiecznego nawiązywania połączenia za pośrednictwem protokołu SSL.

1. W oknie dialogowym Konfigurowanie nowego połączenia przejdź do karty **SSL** .

1. Zaktualizuj pole **Użyj protokołu SSL** do "Wymagaj".

1. W polu **plik urzędu certyfikacji SSL:** wprowadź lokalizację pliku **BaltimoreCyberTrustRoot. CRT. pem**.

   :::image type="content" source="./media/howto-configure-ssl/mysql-workbench-ssl.png" alt-text="Zapisz konfigurację protokołu SSL":::

W przypadku istniejących połączeń można powiązać protokół SSL, klikając prawym przyciskiem myszy ikonę połączenia i wybierając pozycję Edytuj. Następnie przejdź do karty **SSL** i powiąż plik certyfikatu.

### <a name="connecting-to-server-using-the-mysql-cli-over-ssl"></a>Łączenie z serwerem przy użyciu interfejsu wiersza polecenia MySQL za pośrednictwem protokołu SSL

Innym sposobem powiązania certyfikatu SSL jest użycie interfejsu wiersza polecenia MySQL przez wykonanie następujących poleceń.

```bash
mysql.exe -h mydemoserver.mysql.database.azure.com -u Username@mydemoserver -p --ssl-mode=REQUIRED --ssl-ca=c:\ssl\BaltimoreCyberTrustRoot.crt.pem
```

> [!NOTE]
> W przypadku korzystania z interfejsu wiersza polecenia MySQL w systemie Windows może zostać wyświetlony komunikat o błędzie `SSL connection error: Certificate signature check failed` . W takim przypadku należy zastąpić `--ssl-mode=REQUIRED --ssl-ca={filepath}` Parametry parametrem `--ssl` .

## <a name="step-3--enforcing-ssl-connections-in-azure"></a>Krok 3: wymuszanie połączeń SSL na platformie Azure

### <a name="using-the-azure-portal"></a>Za pomocą witryny Azure Portal

Korzystając z Azure Portal, odwiedź Azure Database for MySQL serwer, a następnie kliknij pozycję **zabezpieczenia połączeń**. Użyj przycisku przełącznika, aby włączyć lub wyłączyć ustawienie **Wymuszaj połączenie SSL** , a następnie kliknij przycisk **Zapisz**. Firma Microsoft zaleca, aby zawsze włączyć ustawienie **Wymuszaj połączenie SSL** w celu zwiększenia bezpieczeństwa.

:::image type="content" source="./media/howto-configure-ssl/enable-ssl.png" alt-text="Zrzut ekranu przedstawiający Azure Portal, aby wymusić połączenia SSL w Azure Database for MySQL":::

### <a name="using-azure-cli"></a>Korzystanie z interfejsu wiersza polecenia platformy Azure

Parametr **wymuszania SSL** można włączyć lub wyłączyć, używając wartości włączone lub wyłączone odpowiednio w interfejsie wiersza polecenia platformy Azure.

```azurecli-interactive
az mysql server update --resource-group myresource --name mydemoserver --ssl-enforcement Enabled
```

## <a name="step-4-verify-the-ssl-connection"></a>Krok 4. Weryfikowanie połączenia SSL

Wykonaj polecenie MySQL **status** , aby sprawdzić, czy nawiązano połączenie z serwerem MySQL przy użyciu protokołu SSL:

```dos
mysql> status
```

Upewnij się, że połączenie jest zaszyfrowane, przeglądając dane wyjściowe, które powinny być wyświetlane:  **SSL: szyfr w użyciu jest AES256-SHA**

## <a name="sample-code"></a>Przykładowy kod

Aby nawiązać bezpieczne połączenie z Azure Database for MySQL za pośrednictwem protokołu SSL z aplikacji, zapoznaj się z poniższymi przykładami kodu:

Zapoznaj się z listą [zgodnych sterowników](concepts-compatibility.md) obsługiwanych przez usługę Azure Database for MySQL.

### <a name="php"></a>PHP

```php
$conn = mysqli_init();
mysqli_ssl_set($conn,NULL,NULL, "/var/www/html/BaltimoreCyberTrustRoot.crt.pem", NULL, NULL);
mysqli_real_connect($conn, 'mydemoserver.mysql.database.azure.com', 'myadmin@mydemoserver', 'yourpassword', 'quickstartdb', 3306, MYSQLI_CLIENT_SSL);
if (mysqli_connect_errno($conn)) {
die('Failed to connect to MySQL: '.mysqli_connect_error());
}
```

### <a name="php-using-pdo"></a>PHP (przy użyciu PDO)

```phppdo
$options = array(
    PDO::MYSQL_ATTR_SSL_CA => '/var/www/html/BaltimoreCyberTrustRoot.crt.pem'
);
$db = new PDO('mysql:host=mydemoserver.mysql.database.azure.com;port=3306;dbname=databasename', 'username@mydemoserver', 'yourpassword', $options);
```

### <a name="python-mysqlconnector-python"></a>Python (MySQLConnector Python)

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
                       ssl={'ca': '/var/www/html/BaltimoreCyberTrustRoot.crt.pem'})
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
        :sslca => '/var/www/html/BaltimoreCyberTrustRoot.crt.pem'
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
properties.setProperty("user", 'myadmin@mydemoserver');
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
properties.setProperty("user", 'myadmin@mydemoserver');
properties.setProperty("password", 'yourpassword');
conn = DriverManager.getConnection(url, properties);
```

### <a name="net-mysqlconnector"></a>.NET (MySqlConnector)

```csharp
var builder = new MySqlConnectionStringBuilder
{
    Server = "mydemoserver.mysql.database.azure.com",
    UserID = "myadmin@mydemoserver",
    Password = "yourpassword",
    Database = "quickstartdb",
    SslMode = MySqlSslMode.VerifyCA,
    SslCa = "BaltimoreCyberTrustRoot.crt.pem",
};
using (var connection = new MySqlConnection(builder.ConnectionString))
{
    connection.Open();
}
```

## <a name="next-steps"></a>Następne kroki

Przejrzyj różne opcje łączności aplikacji następujące: [biblioteki połączeń dla Azure Database for MySQL](concepts-connection-libraries.md)
