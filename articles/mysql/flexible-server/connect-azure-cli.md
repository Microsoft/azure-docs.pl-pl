---
title: 'Szybki Start: Łączenie przy użyciu interfejsu wiersza polecenia platformy Azure — Azure Database for MySQL — elastyczny serwer'
description: Ten przewodnik Szybki Start oferuje kilka sposobów nawiązywania połączenia z interfejsem wiersza polecenia platformy Azure z serwerem elastycznym Azure Database for MySQL.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.custom: mvc
ms.topic: quickstart
ms.date: 03/01/2021
ms.openlocfilehash: d40dfa9c8a79625910414409ac3a6df7045c31f2
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2021
ms.locfileid: "106490917"
---
# <a name="quickstart-connect-and-query-with-azure-cli--with-azure-database-for-mysql---flexible-server"></a>Szybki Start: łączenie i wykonywanie zapytań przy użyciu interfejsu wiersza polecenia platformy Azure z serwerem elastycznym Azure Database for MySQL

> [!IMPORTANT]
> Serwer elastyczny Azure Database for MySQL jest obecnie w publicznej wersji zapoznawczej.

Ten przewodnik Szybki Start przedstawia sposób nawiązywania połączeń z serwerem elastycznym Azure Database for MySQL przy użyciu interfejsu wiersza polecenia platformy Azure z ```az mysql flexible-server connect``` poleceniem. To polecenie umożliwia przetestowanie łączności z serwerem bazy danych i uruchamianie zapytań bezpośrednio względem serwera.  Można również użyć polecenia Uruchom w trybie interaktywnym do uruchamiania wielu zapytań.

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure. Jeśli go nie masz, [Uzyskaj bezpłatną wersję próbną](https://azure.microsoft.com/free/).
- Zainstaluj najnowszą wersję [interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) (2.20.0 lub nowszą)
- Logowanie przy użyciu interfejsu wiersza polecenia platformy Azure z ```az login``` poleceniem 
- Włącz trwałość parametru za pomocą ```az config param-persist on``` . Trwałość parametru ułatwi korzystanie z kontekstu lokalnego bez konieczności powtarzania wielu argumentów, takich jak grupa zasobów lub lokalizacja itp.

## <a name="create-an-mysql-flexible-server"></a>Tworzenie elastycznego serwera MySQL

Pierwszym krokiem jest utworzenie zarządzanego serwera MySQL. W [Azure Cloud Shell](https://shell.azure.com/)uruchom następujący skrypt, a następnie zanotuj **nazwę serwera**, nazwę **użytkownika** i  **hasło** wygenerowane na podstawie tego polecenia.

```azurecli
az mysql flexible-server create --public-access <your-ip-address>
```

Możesz podać dodatkowe argumenty dla tego polecenia, aby je dostosować. Zobacz wszystkie argumenty dla [AZ MySQL elastyczny-Server Create](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_create).

## <a name="create-a-database"></a>Tworzenie bazy danych
Uruchom następujące polecenie, aby utworzyć bazę danych, **newdatabase** , jeśli jeszcze jej nie utworzono.

```azurecli
az mysql flexible-server db create -d newdatabase
```

## <a name="view-all-the-arguments"></a>Wyświetl wszystkie argumenty
Można wyświetlić wszystkie argumenty dla tego polecenia z ```--help``` argumentem. 

```azurecli
az mysql flexible-server connect --help
```

## <a name="test-database-server-connection"></a>Testuj połączenie z serwerem bazy danych
Uruchom następujący skrypt, aby przetestować i zweryfikować połączenie z bazą danych ze środowiska deweloperskiego.

```azurecli
az mysql flexible-server connect -n <servername> -u <username> -p <password> -d <databasename>
```

**Przykład:**
```azurecli
az mysql flexible-server connect -n mysqldemoserver1 -u dbuser -p "dbpassword" -d newdatabase
```

W przypadku pomyślnego nawiązania połączenia powinny zostać wyświetlone następujące dane wyjściowe:

```output
Command group 'mysql flexible-server' is in preview and under development. Reference and support levels: https://aka.ms/CLI_refstatus
Connecting to newdatabase database.
Successfully connected to mysqldemoserver1.
```
Jeśli nawiązanie połączenia nie powiodło się, wypróbuj następujące rozwiązania:
- Sprawdź, czy na komputerze klienckim jest otwarty port 3306.
- Jeśli nazwa użytkownika i hasło administratora serwera są poprawne
- Jeśli skonfigurowano regułę zapory dla komputera klienckiego
- Jeśli serwer został skonfigurowany z dostępem prywatnym w sieci wirtualnej, upewnij się, że komputer kliencki znajduje się w tej samej sieci wirtualnej.

## <a name="run-single-query"></a>Uruchom pojedyncze zapytanie
Uruchom następujące polecenie, aby wykonać pojedyncze zapytanie przy użyciu ```--querytext``` argumentu, ```-q``` .

```azurecli
az mysql flexible-server connect -n <server-name> -u <username> -p "<password>" -d <database-name> --querytext "<query text>"
```

**Przykład:**
```azurecli
az mysql flexible-server connect -n mysqldemoserver1 -u dbuser -p "dbpassword" -d newdatabase -q "select * from table1;" --output table
```

Zobaczysz dane wyjściowe, jak pokazano poniżej:

```output
Command group 'mysql flexible-server' is in preview and under development. Reference and support levels: https://aka.ms/CLI_refstatus
Successfully connected to mysqldemoserver1.
Ran Database Query: 'select * from table1;'
Retrieving first 30 rows of query output, if applicable.
Closed the connection to mysqldemoserver1
Local context is turned on. Its information is saved in working directory C:\Users\sumuth. You can run `az local-context off` to turn it off.
Your preference of  are now saved to local context. To learn more, type in `az local-context --help`
Txt    Val
-----  -----
test   200
test   200
test   200
test   200
test   200
test   200
test   200
```

## <a name="run-multiple-queries-using-interactive-mode"></a>Uruchamianie wielu zapytań w trybie interaktywnym
Można uruchomić wiele zapytań w trybie **interaktywnym** . Aby włączyć tryb interaktywny, uruchom następujące polecenie

```azurecli
az mysql flexible-server connect -n <server-name> -u <username> -p <password> --interactive
```

**Przykład:**
```azurecli
az mysql flexible-server connect -n mysqldemoserver1 -u dbuser -p "dbpassword" -d newdatabase --interactive
```

Zobaczysz środowisko powłoki **MySQL** , jak pokazano poniżej:

```bash
Command group 'mysql flexible-server' is in preview and under development. Reference and support levels: https://aka.ms/CLI_refstatus
Password:
mysql 5.7.29-log
mycli 1.22.2
Chat: https://gitter.im/dbcli/mycli
Mail: https://groups.google.com/forum/#!forum/mycli-users
Home: http://mycli.net
Thanks to the contributor - Martijn Engler
newdatabase> CREATE TABLE table1 (id int NOT NULL, val int,txt varchar(200));
Query OK, 0 rows affected
Time: 2.290s
newdatabase1> INSERT INTO table1 values (1,100,'text1');
Query OK, 1 row affected
Time: 0.199s
newdatabase1> SELECT * FROM table1;
+----+-----+-------+
| id | val | txt   |
+----+-----+-------+
| 1  | 100 | text1 |
+----+-----+-------+
1 row in set
Time: 0.149s
newdatabase>exit;
Goodbye!
Local context is turned on. Its information is saved in working directory C:\mydir. You can run `az local-context off` to turn it off.
Your preference of  are now saved to local context. To learn more, type in `az local-context --help`
```


## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
* [Nawiązywanie połączenia z serwerem elastycznym Azure Database for MySQL z połączeniami szyfrowanymi](how-to-connect-tls-ssl.md)
* [Zarządzanie serwerem](./how-to-manage-server-cli.md)

