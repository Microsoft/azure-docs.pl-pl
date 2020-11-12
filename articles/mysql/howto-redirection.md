---
title: Łączenie z przekierowaniami — Azure Database for MySQL
description: W tym artykule opisano, jak można skonfigurować aplikację do łączenia się z Azure Database for MySQL przy użyciu przekierowania.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 6/8/2020
ms.openlocfilehash: bea618b34809544308caba46a0ffbdecee91d69f
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/12/2020
ms.locfileid: "94541814"
---
# <a name="connect-to-azure-database-for-mysql-with-redirection"></a>Nawiązywanie połączenia z usługą Azure Database for MySQL z użyciem przekierowywania

W tym temacie opisano sposób łączenia aplikacji Azure Database for MySQL serwerze z trybem przekierowywania. Przekierowywanie ma na celu zmniejszenie opóźnienia sieci między aplikacjami klienckimi a serwerami MySQL, umożliwiając aplikacjom bezpośrednie łączenie się z węzłami serwera zaplecza.

## <a name="before-you-begin"></a>Przed rozpoczęciem
Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Utwórz serwer Azure Database for MySQL przy użyciu aparatu w wersji 5,6, 5,7 lub 8,0. 

Aby uzyskać szczegółowe informacje, zobacz jak utworzyć serwer Azure Database for MySQL przy użyciu [Azure Portal](quickstart-create-mysql-server-database-using-azure-portal.md) lub [interfejsu wiersza polecenia platformy Azure](quickstart-create-mysql-server-database-using-azure-cli.md).

## <a name="enable-redirection"></a>Włącz przekierowanie

Na serwerze Azure Database for MySQL skonfiguruj parametr, aby `redirect_enabled` zezwolić na `ON` połączenia z trybem przekierowywania. Aby zaktualizować ten parametr serwera, użyj [Azure Portal](howto-server-parameters.md) lub [interfejsu wiersza polecenia platformy Azure](howto-configure-server-parameters-using-cli.md).

## <a name="php"></a>PHP

Obsługa przekierowywania w aplikacjach PHP jest dostępna za pomocą rozszerzenia [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) opracowanego przez firmę Microsoft. 

Rozszerzenie mysqlnd_azure jest dostępne do dodania do aplikacji PHP za pomocą PECL. zdecydowanie zaleca się zainstalowanie i skonfigurowanie rozszerzenia za pomocą oficjalnie opublikowanego [pakietu PECL](https://pecl.php.net/package/mysqlnd_azure).

> [!IMPORTANT]
> Obsługa przekierowania w rozszerzeniu [MYSQLND_AZURE](https://github.com/microsoft/mysqlnd_azure) php jest obecnie w wersji zapoznawczej.

### <a name="redirection-logic"></a>Logika przekierowania

>[!IMPORTANT]
> Logika przekierowania/zachowanie początkowa wersja 1.1.0 została zaktualizowana i **zaleca się użycie wersji 1.1.0 +**.

Zachowanie przekierowywania jest ustalane przez wartość `mysqlnd_azure.enableRedirect` . W poniższej tabeli przedstawiono zachowanie przekierowywania na podstawie wartości tego parametru, zaczynając od **wersji 1.1.0 +**.

Jeśli używasz starszej wersji rozszerzenia mysqlnd_azure (wersja 1.0.0-1.0.3), zachowanie przekierowania jest określane przez wartość `mysqlnd_azure.enabled` . Prawidłowe wartości to `off` (działają podobnie jak zachowanie opisane w poniższej tabeli) i `on` (działa jak `preferred` w poniższej tabeli).  

|**wartość mysqlnd_azure. enableRedirect**| **Zachowanie**|
|----------------------------------------|-------------|
|`off` lub `0`|Przekierowanie nie zostanie użyte. |
|`on` lub `1`|— Jeśli połączenie nie korzysta z protokołu SSL po stronie sterownika, połączenie nie zostanie nawiązane. Zostanie zwrócony następujący błąd: *"mysqlnd_azure. enableRedirect jest włączone, ale nie ustawiono opcji SSL w parametrach połączenia. Przekierowywanie jest możliwe tylko przy użyciu protokołu SSL. "*<br>-Jeśli protokół SSL jest używany po stronie sterownika, ale przekierowywanie nie jest obsługiwane na serwerze, pierwsze połączenie zostanie przerwane i zostanie zwrócony następujący błąd: *"połączenie zostało przerwane, ponieważ przekierowanie nie jest włączone na serwerze MySQL lub pakiet sieciowy nie jest zgodny z protokołem przekierowywania".*<br>-Jeśli serwer MySQL obsługuje przekierowania, ale połączenie przekierowane nie powiodło się z jakiegokolwiek powodu, należy również przerwać pierwsze połączenie serwera proxy. Zwróć błąd połączenia przekierowanego.|
|`preferred` lub `2`<br> (wartość domyślna)|-mysqlnd_azure będzie używać przekierowania, jeśli jest to możliwe.<br>— Jeśli połączenie nie korzysta z protokołu SSL po stronie sterownika, serwer nie obsługuje przekierowania lub połączenie przekierowane nie nawiąże połączenia z niekrytycznym powodem, gdy połączenie serwera proxy nadal jest prawidłowe, nastąpi powrót do pierwszego połączenia z serwerem proxy.|

Kolejne sekcje dokumentu przedstawiają sposób instalacji `mysqlnd_azure` rozszerzenia przy użyciu PECL i ustawiania wartości tego parametru.

### <a name="ubuntu-linux"></a>Ubuntu Linux

#### <a name="prerequisites"></a>Wymagania wstępne 
- Wersje PHP 7.2.15 + i 7.3.2 +
- PHP 
- PHP — MySQL
- Serwer Azure Database for MySQL

1. Zainstaluj [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) z [PECL](https://pecl.php.net/package/mysqlnd_azure). Zalecane jest użycie wersji 1.1.0 +.

    ```bash
    sudo pecl install mysqlnd_azure
    ```

2. Znajdź katalog rozszerzeń ( `extension_dir` ), uruchamiając następujące czynności:

    ```bash
    php -i | grep "extension_dir"
    ```

3. Zmień katalogi na zwracany folder i upewnij się, że znajduje się `mysqlnd_azure.so` w tym folderze. 

4. Zlokalizuj folder dla plików. ini, uruchamiając następujące czynności: 

    ```bash
    php -i | grep "dir for additional .ini files"
    ```

5. Zmień katalogi na ten zwrócony folder. 

6. Utwórz nowy plik. ini dla `mysqlnd_azure` . Upewnij się, że kolejność alfabetyczna nazwy jest po mysqnld, ponieważ moduły są ładowane zgodnie z kolejnością nazw plików ini. Na przykład, jeśli `mysqlnd` plik ini ma `10-mysqlnd.ini` nazwę, Nazwij plik mysqlnd ini jako `20-mysqlnd-azure.ini` .

7. W nowym pliku ini Dodaj następujące wiersze, aby włączyć przekierowywanie.

    ```bash
    extension=mysqlnd_azure
    mysqlnd_azure.enableRedirect = on/off/preferred
    ```

### <a name="windows"></a>Windows

#### <a name="prerequisites"></a>Wymagania wstępne 
- Wersje PHP 7.2.15 + i 7.3.2 +
- PHP — MySQL
- Serwer Azure Database for MySQL

1. Sprawdź, czy korzystasz z programu PHP w wersji x64 lub x86, uruchamiając następujące polecenie:

    ```cmd
    php -i | findstr "Thread"
    ```

2. Pobierz odpowiednią wersję x64 lub x86 [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) dll z [PECL](https://pecl.php.net/package/mysqlnd_azure) , która pasuje do używanej wersji języka PHP. Zalecane jest użycie wersji 1.1.0 +.

3. Wyodrębnij plik zip i Znajdź bibliotekę DLL o nazwie `php_mysqlnd_azure.dll` .

4. Znajdź katalog rozszerzeń ( `extension_dir` ), uruchamiając następujące polecenie:

    ```cmd
    php -i | find "extension_dir"
    ```

5. Skopiuj `php_mysqlnd_azure.dll` plik do katalogu zwróconego w kroku 4. 

6. Zlokalizuj folder PHP zawierający `php.ini` plik za pomocą następującego polecenia:

    ```cmd
    php -i | find "Loaded Configuration File"
    ```

7. Zmodyfikuj `php.ini` plik i Dodaj następujące dodatkowe wiersze, aby włączyć przekierowywanie. 

    W sekcji rozszerzenia dynamiczne: 
    ```cmd
    extension=mysqlnd_azure
    ```
    
    W sekcji Ustawienia modułu:     
    ```cmd 
    [mysqlnd_azure]
    mysqlnd_azure.enableRedirect = on/off/preferred
    ```

### <a name="confirm-redirection"></a>Potwierdź przekierowanie

Możesz również potwierdzić, że przekierowanie jest skonfigurowane przy użyciu poniższego przykładowego kodu w języku PHP. Utwórz plik PHP o nazwie `mysqlConnect.php` i wklej poniższy kod. Zaktualizuj nazwę serwera, nazwę użytkownika i hasło. 
 
 ```php
<?php
$host = '<yourservername>.mysql.database.azure.com';
$username = '<yourusername>@<yourservername>';
$password = '<yourpassword>';
$db_name = 'testdb';
  echo "mysqlnd_azure.enableRedirect: ", ini_get("mysqlnd_azure.enableRedirect"), "\n";
  $db = mysqli_init();
  //The connection must be configured with SSL for redirection test
  $link = mysqli_real_connect ($db, $host, $username, $password, $db_name, 3306, NULL, MYSQLI_CLIENT_SSL);
  if (!$link) {
     die ('Connect error (' . mysqli_connect_errno() . '): ' . mysqli_connect_error() . "\n");
  }
  else {
    echo $db->host_info, "\n"; //if redirection succeeds, the host_info will differ from the hostname you used used to connect
    $res = $db->query('SHOW TABLES;'); //test query with the connection
    print_r ($res);
    $db->close();
  }
?>
 ```

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji dotyczących parametrów połączenia, zobacz [Parametry połączenia](howto-connection-string.md).
