---
title: 'Szybki Start: Łączenie przy użyciu języka PHP-Azure Database for MySQL'
description: Ten przewodnik Szybki start zawiera kilka przykładów kodu PHP, których można używać do nawiązywania połączeń z danymi usługi Azure Database for MySQL i wykonywania zapytań względem nich.
author: savjani
ms.author: pariks
ms.service: mysql
ms.custom: mvc
ms.topic: quickstart
ms.date: 10/28/2020
ms.openlocfilehash: bf62eca26d846cc529df43b519b1377f88c8aede
ms.sourcegitcommit: 431bf5709b433bb12ab1f2e591f1f61f6d87f66c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/12/2021
ms.locfileid: "98132906"
---
# <a name="quickstart-use-php-to-connect-and-query-data-in-azure-database-for-mysql"></a>Szybki Start: używanie języka PHP do łączenia i wykonywania zapytań dotyczących danych w Azure Database for MySQL
Ten przewodnik Szybki start przedstawia sposób nawiązywania połączeń z usługą Azure Database for MySQL przy użyciu aplikacji języka [PHP](https://secure.php.net/manual/intro-whatis.php). Pokazano w nim, jak używać instrukcji języka SQL w celu wysyłania zapytań o dane oraz wstawiania, aktualizowania i usuwania danych w bazie danych.

## <a name="prerequisites"></a>Wymagania wstępne
W tym przewodniku Szybki Start są potrzebne następujące aplikacje:

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free).
- Tworzenie Azure Database for MySQL pojedynczego serwera przy użyciu [Azure Portal](./quickstart-create-mysql-server-database-using-azure-portal.md) <br/> lub [interfejs wiersza polecenia platformy Azure](./quickstart-create-mysql-server-database-using-azure-cli.md) , jeśli go nie masz.
- W zależności od tego, czy używasz dostępu publicznego, czy prywatnego, wykonaj **jedną** z poniższych czynności, aby włączyć łączność.

    |Akcja| Metoda łączności|Przewodnik z instrukcjami|
    |:--------- |:--------- |:--------- |
    | **Konfigurowanie reguł zapory** | Publiczne | [Portal](./howto-manage-firewall-using-portal.md) <br/> [Interfejs wiersza polecenia](./howto-manage-firewall-using-cli.md)|
    | **Konfigurowanie punktu końcowego usługi** | Publiczne | [Portal](./howto-manage-vnet-using-portal.md) <br/> [Interfejs wiersza polecenia](./howto-manage-vnet-using-cli.md)|
    | **Konfigurowanie linku prywatnego** | Prywatny | [Portal](./howto-configure-privatelink-portal.md) <br/> [Interfejs wiersza polecenia](./howto-configure-privatelink-cli.md) |

- [Tworzenie bazy danych i użytkownika niebędącego administratorem](./howto-create-users.md?tabs=single-server)
- Zainstaluj najnowszą wersję języka PHP dla systemu operacyjnego
    - [PHP w macOS](https://secure.php.net/manual/install.macosx.php)
    - [PHP w systemie Linux](https://secure.php.net/manual/install.unix.php)
    - [Środowisko PHP w systemie Windows](https://secure.php.net/manual/install.windows.php)

> [!NOTE]
> Korzystamy z biblioteki [mysqli](https://www.php.net/manual/en/book.mysqli.php) , aby zarządzać serwerem w tym przewodniku Szybki Start.

## <a name="get-connection-information"></a>Pobieranie informacji o połączeniu
Informacje o połączeniu z serwerem bazy danych można uzyskać z Azure Portal, wykonując następujące czynności:

1. Zaloguj się do [Azure Portal](https://portal.azure.com/).
2. Przejdź do strony bazy danych Azure Database for MySQL. Możesz wyszukać i wybrać **Azure Database for MySQL**.
:::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/find-azure-mysql-in-portal.png" alt-text="Znajdź Azure Database for MySQL":::

2. Wybierz serwer MySQL (na przykład **mydemoserver**).
3. Na stronie **Przegląd** Skopiuj w pełni kwalifikowaną nazwę serwera obok pozycji **Nazwa serwera** i nazwa użytkownika administratora obok **nazwy logowania administratora serwera**. Aby skopiować nazwę serwera lub hosta, umieść na niej wskaźnik myszy i wybierz ikonę **Kopiuj**.

> [!IMPORTANT]
> - Jeśli nie pamiętasz hasła, możesz [zresetować hasło](./howto-create-manage-server-portal.md#update-admin-password).
> - Zastąp parametry **host, username, Password** i **db_name** własnymi wartościami * *

## <a name="step-1-connect-to-the-server"></a>Krok 1. Nawiązywanie połączenia z serwerem
Protokół SSL jest domyślnie włączony. Może być konieczne pobranie [certyfikatu protokołu SSL DigiCertGlobalRootG2](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem) w celu nawiązania połączenia ze środowiskiem lokalnym. Ten kod wywołuje:
- [mysqli_init](https://secure.php.net/manual/mysqli.init.php) zainicjować mysqli.
- [mysqli_ssl_set](https://www.php.net/manual/en/mysqli.ssl-set.php) , aby wskazać ścieżkę certyfikatu SSL. Jest to wymagane w środowisku lokalnym, ale nie jest wymagane dla App Service aplikacji sieci Web ani maszyn wirtualnych platformy Azure.
- [mysqli_real_connect](https://secure.php.net/manual/mysqli.real-connect.php) połączyć się z bazą danych MySQL.
- [mysqli_close](https://secure.php.net/manual/mysqli.close.php) zamknąć połączenia.


```php
$host = 'mydemoserver.mysql.database.azure.com';
$username = 'myadmin@mydemoserver';
$password = 'your_password';
$db_name = 'your_database';

//Initializes MySQLi
$conn = mysqli_init();

mysqli_ssl_set($conn,NULL,NULL, "/var/www/html/DigiCertGlobalRootG2.crt.pem", NULL, NULL);

// Establish the connection
mysqli_real_connect($conn, 'mydemoserver.mysql.database.azure.com', 'myadmin@mydemoserver', 'yourpassword', 'quickstartdb', 3306, NULL, MYSQLI_CLIENT_SSL);

//If connection failed, show the error
if (mysqli_connect_errno($conn))
{
    die('Failed to connect to MySQL: '.mysqli_connect_error());
}
```
[Masz problemy? Daj nam znać](https://aka.ms/mysql-doc-feedback)

## <a name="step-2-create-a-table"></a>Krok 2. Tworzenie tabeli
Użyj poniższego kodu, aby nawiązać połączenie. Ten kod wywołuje:
- [mysqli_query](https://secure.php.net/manual/mysqli.query.php) uruchomić zapytanie.
```php
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
```

## <a name="step-3-insert-data"></a>Krok 3. Wstawianie danych
Użyj poniższego kodu, aby wstawić dane za pomocą instrukcji **INSERT** języka SQL. Ten kod używa metod:
- [mysqli_prepare](https://secure.php.net/manual/mysqli.prepare.php) utworzyć przygotowanej instrukcji INSERT
- [mysqli_stmt_bind_param](https://secure.php.net/manual/mysqli-stmt.bind-param.php) , aby powiązać parametry dla każdej wartości wstawionej kolumny.
- [mysqli_stmt_execute](https://secure.php.net/manual/mysqli-stmt.execute.php)
- [mysqli_stmt_close](https://secure.php.net/manual/mysqli-stmt.close.php) zamknąć instrukcji przy użyciu metody


```php
//Create an Insert prepared statement and run it
$product_name = 'BrandNewProduct';
$product_color = 'Blue';
$product_price = 15.5;
if ($stmt = mysqli_prepare($conn, "INSERT INTO Products (ProductName, Color, Price) VALUES (?, ?, ?)"))
{
    mysqli_stmt_bind_param($stmt, 'ssd', $product_name, $product_color, $product_price);
    mysqli_stmt_execute($stmt);
    printf("Insert: Affected %d rows\n", mysqli_stmt_affected_rows($stmt));
    mysqli_stmt_close($stmt);
}

```

## <a name="step-4-read-data"></a>Krok 4. odczyt danych
Użyj poniższego kodu, aby odczytać dane przy użyciu instrukcji **SELECT** języka SQL.  Kod używa metody:
- [mysqli_query](https://secure.php.net/manual/mysqli.query.php) wykonać zapytania **SELECT**
- [mysqli_fetch_assoc](https://secure.php.net/manual/mysqli-result.fetch-assoc.php) pobrać wierszy wyników.

```php
//Run the Select query
printf("Reading data from table: \n");
$res = mysqli_query($conn, 'SELECT * FROM Products');
while ($row = mysqli_fetch_assoc($res))
 {
    var_dump($row);
 }

```


## <a name="step-5-delete-data"></a>Krok 5. Usuwanie danych
Użyj następującego kodu, aby usunąć wiersze przy użyciu instrukcji **delete** języka SQL. Kod używa metod:
- [mysqli_prepare](https://secure.php.net/manual/mysqli.prepare.php) utworzyć przygotowanej instrukcji DELETE
- [mysqli_stmt_bind_param](https://secure.php.net/manual/mysqli-stmt.bind-param.php) wiąże parametry
- [mysqli_stmt_execute](https://secure.php.net/manual/mysqli-stmt.execute.php) wykonuje przygotowaną instrukcję delete
- [mysqli_stmt_close](https://secure.php.net/manual/mysqli-stmt.close.php) zamyka instrukcję

```php
//Run the Delete statement
$product_name = 'BrandNewProduct';
if ($stmt = mysqli_prepare($conn, "DELETE FROM Products WHERE ProductName = ?")) {
mysqli_stmt_bind_param($stmt, 's', $product_name);
mysqli_stmt_execute($stmt);
printf("Delete: Affected %d rows\n", mysqli_stmt_affected_rows($stmt));
mysqli_stmt_close($stmt);
}
```

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Aby wyczyścić wszystkie zasoby używane w ramach tego przewodnika Szybki Start, Usuń grupę zasobów przy użyciu następującego polecenia:

```azurecli
az group delete \
    --name $AZ_RESOURCE_GROUP \
    --yes
```

## <a name="next-steps"></a>Następne kroki
> [!div class="nextstepaction"]
> [Zarządzanie serwerem Azure Database for MySQL przy użyciu portalu](./howto-create-manage-server-portal.md)<br/>

> [!div class="nextstepaction"]
> [Zarządzanie serwerem Azure Database for MySQL przy użyciu interfejsu wiersza polecenia](./how-to-manage-single-server-cli.md)

[Nie możesz znaleźć tego, czego szukasz? Daj nam znać.](https://aka.ms/mysql-doc-feedback)
