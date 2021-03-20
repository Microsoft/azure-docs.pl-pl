---
title: 'Szybki Start: Łączenie przy użyciu języka PHP-Azure Database for MySQL-elastyczny serwer'
description: Ten przewodnik Szybki Start zawiera kilka przykładów kodu PHP, których można użyć do nawiązywania połączeń i wysyłania zapytań dotyczących danych z serwera Azure Database for MySQL-elastycznym.
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.custom: mvc
ms.topic: quickstart
ms.date: 9/21/2020
ms.openlocfilehash: dc6b069e3c7686ec6964dab890e503aa193cf6fe
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92545110"
---
# <a name="quickstart-use-php-to-connect-and-query-data-in-azure-database-for-mysql---flexible-server"></a>Szybki Start: używanie języka PHP do nawiązywania połączeń i wykonywania zapytań dotyczących danych w Azure Database for MySQL-elastycznym serwerze

> [!IMPORTANT]
> Serwer elastyczny Azure Database for MySQL jest obecnie w publicznej wersji zapoznawczej.

Ten przewodnik Szybki Start przedstawia sposób nawiązywania połączeń z serwerem elastycznym Azure Database for MySQL przy użyciu aplikacji języka [php](https://secure.php.net/manual/intro-whatis.php) . Pokazano w nim, jak używać instrukcji języka SQL w celu wysyłania zapytań o dane oraz wstawiania, aktualizowania i usuwania danych w bazie danych. W tym artykule założono, że znasz już Programowanie przy użyciu języka PHP i dopiero zaczynasz pracę z Azure Database for MySQL elastycznym serwerem.

## <a name="prerequisites"></a>Wymagania wstępne
Ten przewodnik Szybki start jako punktu wyjścia używa zasobów utworzonych w jednym z tych przewodników:

- [Utwórz Azure Database for MySQL elastyczny serwer przy użyciu Azure Portal](./quickstart-create-server-portal.md)
- [Tworzenie Azure Database for MySQL elastycznego serwera przy użyciu interfejsu wiersza polecenia platformy Azure](./quickstart-create-server-cli.md)

## <a name="preparing-your-client-workstation"></a>Przygotowywanie stacji roboczej klienta
1. Jeśli serwer elastyczny został utworzony z *dostępem prywatnym (Integracja z siecią wirtualną)*, należy połączyć się z serwerem z zasobu w ramach tej samej sieci wirtualnej co serwer. Można utworzyć maszynę wirtualną i dodać ją do sieci wirtualnej utworzonej przy użyciu elastycznego serwera. Zapoznaj się z tematem [Tworzenie i zarządzanie Azure Database for MySQL elastyczną siecią wirtualną serwera przy użyciu interfejsu wiersza polecenia platformy Azure](./how-to-manage-virtual-network-cli.md).

2. Jeśli został utworzony serwer elastyczny z *dostępem publicznym (dozwolone adresy IP)*, możesz dodać lokalny adres IP do listy reguł zapory na serwerze. Zapoznaj się z tematem [Tworzenie i zarządzanie Azure Database for MySQL elastycznych reguł zapory serwera przy użyciu interfejsu wiersza polecenia platformy Azure](./how-to-manage-firewall-cli.md).

### <a name="install-php"></a>Instalowanie języka PHP

Zainstaluj język PHP na własnym serwerze lub utwórz [aplikację internetową](../../app-service/overview.md), która zawiera język PHP.  Aby dowiedzieć się, jak utworzyć reguły zapory, zobacz temat [Tworzenie i zarządzanie regułami zapory](./how-to-manage-firewall-portal.md) .

#### <a name="macos"></a>macOS

1. Pobierz [wersję 7.1.4 języka PHP](https://secure.php.net/downloads.php).
2. Zainstaluj język PHP i zapoznaj się z [podręcznikiem języka PHP](https://secure.php.net/manual/install.macosx.php) w celu przeprowadzenia dalszej konfiguracji.

#### <a name="linux-ubuntu"></a>Linux (Ubuntu)

1. Pobierz [wersję php 7.1.4 non-thread safe (x64)](https://secure.php.net/downloads.php).
2. Zainstaluj język PHP i zapoznaj się z [podręcznikiem języka PHP](https://secure.php.net/manual/install.unix.php) w celu przeprowadzenia dalszej konfiguracji.

#### <a name="windows"></a>Windows

1. Pobierz [wersję php 7.1.4 non-thread safe (x64)](https://windows.php.net/download#php-7.1).
2. Zainstaluj język PHP i zapoznaj się z [podręcznikiem języka PHP](https://secure.php.net/manual/install.windows.php) w celu przeprowadzenia dalszej konfiguracji.

## <a name="get-connection-information"></a>Pobieranie informacji o połączeniu

Pobierz informacje o połączeniu, które są konieczne do nawiązania połączenia z serwerem elastycznym Azure Database for MySQL. Potrzebna jest w pełni kwalifikowana nazwa serwera i poświadczenia logowania.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2. Z menu po lewej stronie w obszarze Azure Portal wybierz pozycję **wszystkie zasoby**, a następnie wyszukaj utworzony serwer (na przykład **mydemoserver**).
3. Wybierz nazwę serwera.
4. Po przejściu do panelu **Przegląd** serwera zanotuj **nazwę serwera** i **nazwę logowania administratora serwera**. Jeśli zapomnisz hasła, możesz również je zresetować z poziomu tego panelu.
 <!---:::image type="content" source="./media/connect-php/1_server-overview-name-login.png" alt-text="Azure Database for MySQL Flexible Server name":::--->

## <a name="connecting-to-flexible-server-using-tlsssl-in-php"></a>Nawiązywanie połączenia z elastycznym serwerem przy użyciu protokołu TLS/SSL w języku PHP

Aby nawiązać szyfrowane połączenie z elastycznym serwerem za pośrednictwem protokołu TLS/SSL z aplikacji, zapoznaj się z poniższymi przykładami kodu. Możesz pobrać certyfikat wymagany do komunikacji za pośrednictwem protokołu TLS/SSL z [https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem)

```php using SSL
$conn = mysqli_init();
mysqli_ssl_set($conn,NULL,NULL, "/var/www/html/DigiCertGlobalRootCA.crt.pem", NULL, NULL);
mysqli_real_connect($conn, 'mydemoserver.mysql.database.azure.com', 'myadmin', 'yourpassword', 'quickstartdb', 3306, MYSQLI_CLIENT_SSL);
if (mysqli_connect_errno($conn)) {
die('Failed to connect to MySQL: '.mysqli_connect_error());
}
```

## <a name="connect-and-create-a-table"></a>Łączenie i tworzenie tabeli

Użyj poniższego kodu w celu nawiązania połączenia i utworzenia tabeli za pomocą instrukcji **CREATE TABLE** języka SQL.

Kod używa klasy **rozszerzenia MySQL Improved** (mysqli) uwzględnionej w języku PHP. Kod wywołuje metody [mysqli_init](https://secure.php.net/manual/mysqli.init.php) i [mysqli_real_connect](https://secure.php.net/manual/mysqli.real-connect.php) w celu połączenia z systemem MySQL. Następnie wywołuje on metodę [mysqli_query](https://secure.php.net/manual/mysqli.query.php) w celu uruchomienia zapytania. W kolejnym kroku kod wywołuje metodę [mysqli_close](https://secure.php.net/manual/mysqli.close.php) w celu zamknięcia połączenia.

Zastąp parametry hosta, nazwy użytkownika, hasła i nazwy bazy danych własnymi wartościami.

```php
<?php
$host = 'mydemoserver.mysql.database.azure.com';
$username = 'myadmin';
$password = 'your_password';
$db_name = 'your_database';

//Establishes the connection
$conn = mysqli_init();
mysqli_real_connect($conn, $host, $username, $password, $db_name, 3306);
if (mysqli_connect_errno($conn)) {
die('Failed to connect to MySQL: '.mysqli_connect_error());
}

// Run the create table query
if (mysqli_query($conn, '
CREATE TABLE Products (
`Id` INT NOT NULL AUTO_INCREMENT ,
`ProductName` VARCHAR(200) NOT NULL ,
`Color` VARCHAR(50) NOT NULL ,
`Price` DOUBLE NOT NULL ,
PRIMARY KEY (`Id`)
);
')) {
printf("Table created\n");
}

//Close the connection
mysqli_close($conn);
?>
```

## <a name="insert-data"></a>Wstawianie danych

Użyj poniższego kodu, aby nawiązać połączenie i wstawić dane za pomocą instrukcji **INSERT** języka SQL.

Kod używa klasy **rozszerzenia MySQL Improved** (mysqli) uwzględnionej w języku PHP. Kod używa metody [mysqli_prepare](https://secure.php.net/manual/mysqli.prepare.php) w celu utworzenia przygotowanej instrukcji insert, a następnie tworzy powiązania parametrów dla każdej wstawionej wartości kolumny za pomocą metody [mysqli_stmt_bind_param](https://secure.php.net/manual/mysqli-stmt.bind-param.php). Kod uruchamia instrukcję, używając metody [mysqli_stmt_execute](https://secure.php.net/manual/mysqli-stmt.execute.php), a następnie zamyka instrukcję przy użyciu metody [mysqli_stmt_close](https://secure.php.net/manual/mysqli-stmt.close.php).

Zastąp parametry hosta, nazwy użytkownika, hasła i nazwy bazy danych własnymi wartościami.

```php
<?php
$host = 'mydemoserver.mysql.database.azure.com';
$username = 'myadmin';
$password = 'your_password';
$db_name = 'your_database';

//Establishes the connection
$conn = mysqli_init();
mysqli_real_connect($conn, $host, $username, $password, $db_name, 3306);
if (mysqli_connect_errno($conn)) {
die('Failed to connect to MySQL: '.mysqli_connect_error());
}

//Create an Insert prepared statement and run it
$product_name = 'BrandNewProduct';
$product_color = 'Blue';
$product_price = 15.5;
if ($stmt = mysqli_prepare($conn, "INSERT INTO Products (ProductName, Color, Price) VALUES (?, ?, ?)")) {
mysqli_stmt_bind_param($stmt, 'ssd', $product_name, $product_color, $product_price);
mysqli_stmt_execute($stmt);
printf("Insert: Affected %d rows\n", mysqli_stmt_affected_rows($stmt));
mysqli_stmt_close($stmt);
}

// Close the connection
mysqli_close($conn);
?>
```

## <a name="read-data"></a>Odczyt danych

Użyj poniższego kodu, aby nawiązać połączenie i odczytać dane za pomocą instrukcji **SELECT** języka SQL.  Kod używa klasy **rozszerzenia MySQL Improved** (mysqli) uwzględnionej w języku PHP. Kod używa metody [mysqli_query](https://secure.php.net/manual/mysqli.query.php) w celu wykonania zapytania SQL oraz metody [mysqli_fetch_assoc](https://secure.php.net/manual/mysqli-result.fetch-assoc.php) w celu pobrania wynikowych wierszy.

Zastąp parametry hosta, nazwy użytkownika, hasła i nazwy bazy danych własnymi wartościami.

```php
<?php
$host = 'mydemoserver.mysql.database.azure.com';
$username = 'myadmin';
$password = 'your_password';
$db_name = 'your_database';

//Establishes the connection
$conn = mysqli_init();
mysqli_real_connect($conn, $host, $username, $password, $db_name, 3306);
if (mysqli_connect_errno($conn)) {
die('Failed to connect to MySQL: '.mysqli_connect_error());
}

//Run the Select query
printf("Reading data from table: \n");
$res = mysqli_query($conn, 'SELECT * FROM Products');
while ($row = mysqli_fetch_assoc($res)) {
var_dump($row);
}

//Close the connection
mysqli_close($conn);
?>
```

## <a name="update-data"></a>Aktualizowanie danych

Użyj poniższego kodu, aby nawiązać połączenie i zaktualizować dane za pomocą instrukcji **UPDATE** języka SQL.

Kod używa klasy **rozszerzenia MySQL Improved** (mysqli) uwzględnionej w języku PHP. Kod używa metody [mysqli_prepare](https://secure.php.net/manual/mysqli.prepare.php) w celu utworzenia przygotowanej instrukcji update, a następnie tworzy powiązania parametrów dla każdej zaktualizowanej wartości kolumny za pomocą metody [mysqli_stmt_bind_param](https://secure.php.net/manual/mysqli-stmt.bind-param.php). Kod uruchamia instrukcję, używając metody [mysqli_stmt_execute](https://secure.php.net/manual/mysqli-stmt.execute.php), a następnie zamyka instrukcję przy użyciu metody [mysqli_stmt_close](https://secure.php.net/manual/mysqli-stmt.close.php).

Zastąp parametry hosta, nazwy użytkownika, hasła i nazwy bazy danych własnymi wartościami.

```php
<?php
$host = 'mydemoserver.mysql.database.azure.com';
$username = 'myadmin';
$password = 'your_password';
$db_name = 'your_database';

//Establishes the connection
$conn = mysqli_init();
mysqli_real_connect($conn, $host, $username, $password, $db_name, 3306);
if (mysqli_connect_errno($conn)) {
die('Failed to connect to MySQL: '.mysqli_connect_error());
}

//Run the Update statement
$product_name = 'BrandNewProduct';
$new_product_price = 15.1;
if ($stmt = mysqli_prepare($conn, "UPDATE Products SET Price = ? WHERE ProductName = ?")) {
mysqli_stmt_bind_param($stmt, 'ds', $new_product_price, $product_name);
mysqli_stmt_execute($stmt);
printf("Update: Affected %d rows\n", mysqli_stmt_affected_rows($stmt));

//Close the connection
mysqli_stmt_close($stmt);
}

mysqli_close($conn);
?>
```


## <a name="delete-data"></a>Usuwanie danych
Użyj poniższego kodu, aby nawiązać połączenie i odczytać dane za pomocą instrukcji **DELETE** języka SQL.

Kod używa klasy **rozszerzenia MySQL Improved** (mysqli) uwzględnionej w języku PHP. Kod używa metody [mysqli_prepare](https://secure.php.net/manual/mysqli.prepare.php) w celu utworzenia przygotowanej instrukcji delete, a następnie tworzy powiązania parametrów dla klauzuli where w instrukcji przy użyciu metody [mysqli_stmt_bind_param](https://secure.php.net/manual/mysqli-stmt.bind-param.php). Kod uruchamia instrukcję, używając metody [mysqli_stmt_execute](https://secure.php.net/manual/mysqli-stmt.execute.php), a następnie zamyka instrukcję przy użyciu metody [mysqli_stmt_close](https://secure.php.net/manual/mysqli-stmt.close.php).

Zastąp parametry hosta, nazwy użytkownika, hasła i nazwy bazy danych własnymi wartościami.

```php
<?php
$host = 'mydemoserver.mysql.database.azure.com';
$username = 'myadmin';
$password = 'your_password';
$db_name = 'your_database';

//Establishes the connection
$conn = mysqli_init();
mysqli_real_connect($conn, $host, $username, $password, $db_name, 3306);
if (mysqli_connect_errno($conn)) {
die('Failed to connect to MySQL: '.mysqli_connect_error());
}

//Run the Delete statement
$product_name = 'BrandNewProduct';
if ($stmt = mysqli_prepare($conn, "DELETE FROM Products WHERE ProductName = ?")) {
mysqli_stmt_bind_param($stmt, 's', $product_name);
mysqli_stmt_execute($stmt);
printf("Delete: Affected %d rows\n", mysqli_stmt_affected_rows($stmt));
mysqli_stmt_close($stmt);
}

//Close the connection
mysqli_close($conn);
?>
```
## <a name="next-steps"></a>Następne kroki
- [Połączenie szyfrowane przy użyciu Transport Layer Security (TLS 1,2) w Azure Database for MySQL-elastycznym serwerze](./how-to-connect-tls-ssl.md).
- Dowiedz się więcej o [sieci w Azure Database for MySQL elastycznym serwerze](./concepts-networking.md).
- [Utwórz i zarządzaj Azure Database for MySQL elastycznymi regułami zapory serwera przy użyciu Azure Portal](./how-to-manage-firewall-portal.md).
- [Utwórz Azure Database for MySQL elastyczną sieć wirtualną serwera i zarządzaj nią przy użyciu Azure Portal](./how-to-manage-virtual-network-portal.md).