---
title: 'Szybki Start: Łączenie przy użyciu języka PHP-Azure Database for MySQL'
description: Ten przewodnik Szybki start zawiera kilka przykładów kodu PHP, których można używać do nawiązywania połączeń z danymi usługi Azure Database for MySQL i wykonywania zapytań względem nich.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.custom: mvc
ms.topic: quickstart
ms.date: 5/26/2020
ms.openlocfilehash: 9b05149515f2d40ad8043cd65c2ec5589440713e
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "90882522"
---
# <a name="quickstart-use-php-to-connect-and-query-data-in-azure-database-for-mysql"></a>Szybki Start: używanie języka PHP do łączenia i wykonywania zapytań dotyczących danych w Azure Database for MySQL
Ten przewodnik Szybki start przedstawia sposób nawiązywania połączeń z usługą Azure Database for MySQL przy użyciu aplikacji języka [PHP](https://secure.php.net/manual/intro-whatis.php). Pokazano w nim, jak używać instrukcji języka SQL w celu wysyłania zapytań o dane oraz wstawiania, aktualizowania i usuwania danych w bazie danych. W tym temacie założono, że wiesz już, jak opracowywać zawartość za pomocą języka PHP, i dopiero zaczynasz pracę z usługą Azure Database for MySQL.

## <a name="prerequisites"></a>Wymagania wstępne
Ten przewodnik Szybki start jako punktu wyjścia używa zasobów utworzonych w jednym z tych przewodników:
- [Tworzenie serwera usługi Azure Database for MySQL za pomocą witryny Azure Portal](./quickstart-create-mysql-server-database-using-azure-portal.md)
- [Tworzenie serwera usługi Azure Database for MySQL za pomocą interfejsu wiersza polecenia platformy Azure](./quickstart-create-mysql-server-database-using-azure-cli.md)

> [!IMPORTANT] 
> Upewnij się, że adres IP, z którego nawiązywane jest połączenie, został dodany przy użyciu [Azure Portal](./howto-manage-firewall-using-portal.md) lub [interfejsu wiersza polecenia platformy Azure](./howto-manage-firewall-using-cli.md)

## <a name="install-php"></a>Instalowanie języka PHP
Zainstaluj język PHP na własnym serwerze lub utwórz [aplikację internetową](../app-service/overview.md), która zawiera język PHP.

### <a name="macos"></a>MacOS
- Pobierz [wersję 7.1.4 języka PHP](https://secure.php.net/downloads.php).
- Zainstaluj język PHP i zapoznaj się z [podręcznikiem języka PHP](https://secure.php.net/manual/install.macosx.php) w celu przeprowadzenia dalszej konfiguracji.

### <a name="linux-ubuntu"></a>Linux (Ubuntu)
- Pobierz [wersję php 7.1.4 non-thread safe (x64)](https://secure.php.net/downloads.php).
- Zainstaluj język PHP i zapoznaj się z [podręcznikiem języka PHP](https://secure.php.net/manual/install.unix.php) w celu przeprowadzenia dalszej konfiguracji.

### <a name="windows"></a>Windows
- Pobierz [wersję php 7.1.4 non-thread safe (x64)](https://windows.php.net/download#php-7.1).
- Zainstaluj język PHP i zapoznaj się z [podręcznikiem języka PHP](https://secure.php.net/manual/install.windows.php) w celu przeprowadzenia dalszej konfiguracji.

## <a name="get-connection-information"></a>Pobieranie informacji o połączeniu
Pobierz informacje o połączeniu potrzebne do nawiązania połączenia z usługą Azure Database for MySQL. Potrzebna jest w pełni kwalifikowana nazwa serwera i poświadczenia logowania.

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com/).
2. W menu po lewej stronie w witrynie Azure Portal kliknij pozycję **Wszystkie zasoby** i wyszukaj utworzony serwer, taki jak **mydemoserver**.
3. Kliknij nazwę serwera.
4. Po przejściu do panelu **Przegląd** serwera zanotuj **nazwę serwera** i **nazwę logowania administratora serwera**. Jeśli zapomnisz hasła, możesz również je zresetować z poziomu tego panelu.
 :::image type="content" source="./media/connect-php/1_server-overview-name-login.png" alt-text="Nazwa serwera usługi Azure Database for MySQL":::

## <a name="connect-and-create-a-table"></a>Łączenie i tworzenie tabeli
Użyj poniższego kodu w celu nawiązania połączenia i utworzenia tabeli za pomocą instrukcji **CREATE TABLE** języka SQL. 

Kod używa klasy **rozszerzenia MySQL Improved** (mysqli) uwzględnionej w języku PHP. Kod wywołuje metody [mysqli_init](https://secure.php.net/manual/mysqli.init.php) i [mysqli_real_connect](https://secure.php.net/manual/mysqli.real-connect.php) w celu połączenia z systemem MySQL. Następnie wywołuje on metodę [mysqli_query](https://secure.php.net/manual/mysqli.query.php) w celu uruchomienia zapytania. W kolejnym kroku kod wywołuje metodę [mysqli_close](https://secure.php.net/manual/mysqli.close.php) w celu zamknięcia połączenia.

Zastąp parametry hosta, nazwy użytkownika, hasła i nazwy bazy danych własnymi wartościami. 

```php
<?php
$host = 'mydemoserver.mysql.database.azure.com';
$username = 'myadmin@mydemoserver';
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
$username = 'myadmin@mydemoserver';
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
$username = 'myadmin@mydemoserver';
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
$username = 'myadmin@mydemoserver';
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
$username = 'myadmin@mydemoserver';
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
> [!div class="nextstepaction"]
> [Łączenie z usługą Azure Database for MySQL za pomocą protokołu SSL](howto-configure-ssl.md)
