---
title: 'Szybki start: nawiązywanie połączenia przy użyciu interfejsu wiersza polecenia platformy Azure — Azure Database for MySQL — elastyczny serwer'
description: Ten przewodnik Szybki start zawiera kilka sposobów nawiązywania połączeń za pomocą interfejsu wiersza polecenia platformy Azure Azure Database for MySQL — elastyczny serwer.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.custom: mvc, devx-track-azurecli
ms.topic: quickstart
ms.date: 03/01/2021
ms.openlocfilehash: e0fd5969a3c4f84b6e8f98e99335bf120179e7af
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107481093"
---
# <a name="quickstart-connect-and-query-with-azure-cli--with-azure-database-for-mysql---flexible-server"></a>Szybki start: nawiązywanie połączeń i wykonywanie zapytań za pomocą interfejsu wiersza polecenia platformy Azure Azure Database for MySQL — elastyczny serwer

> [!IMPORTANT]
> Azure Database for MySQL — serwer elastyczny jest obecnie dostępny w publicznej wersji zapoznawczej.

W tym przewodniku Szybki start pokazano, jak nawiązać połączenie z Azure Database for MySQL elastycznym serwerem przy użyciu interfejsu wiersza polecenia platformy Azure za pomocą ```az mysql flexible-server connect``` polecenia . To polecenie umożliwia testowanie łączności z serwerem bazy danych i uruchamianie zapytań bezpośrednio na serwerze.  Można również użyć uruchom polecenie w trybie interaktywnym do uruchamiania wielu zapytań.

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure. Jeśli go nie masz, uzyskaj [bezpłatną wersję próbną](https://azure.microsoft.com/free/).
- Zainstaluj [najnowszą](/cli/azure/install-azure-cli) wersję interfejsu wiersza polecenia platformy Azure (2.20.0 lub nowszą)
- Logowanie przy użyciu interfejsu wiersza polecenia platformy Azure za pomocą ```az login``` polecenia 
- Włącz trwałość parametru za pomocą funkcji ```az config param-persist on``` . Trwałość parametrów pomoże Ci użyć kontekstu lokalnego bez konieczności powtarzania wielu argumentów, takich jak grupa zasobów lub lokalizacja itp.

## <a name="create-an-mysql-flexible-server"></a>Tworzenie serwera elastycznego MySQL

Pierwszym krokiem jest utworzenie zarządzanego serwera MySQL. W [Azure Cloud Shell](https://shell.azure.com/)uruchom następujący skrypt i zanotuj nazwę serwera  **,** nazwę **użytkownika** i hasło wygenerowane na podstawie tego polecenia.

```azurecli
az mysql flexible-server create --public-access <your-ip-address>
```

Możesz podać dodatkowe argumenty dla tego polecenia, aby je dostosować. Zobacz wszystkie argumenty polecenia [az mysql flexible-server create](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_create).

## <a name="create-a-database"></a>Tworzenie bazy danych
Uruchom następujące polecenie, aby utworzyć bazę danych **newdatabase,** jeśli nie została jeszcze utworzona.

```azurecli
az mysql flexible-server db create -d newdatabase
```

## <a name="view-all-the-arguments"></a>Wyświetl wszystko argumenty
Można wyświetlić wszystkie argumenty dla tego polecenia z ```--help``` argumentem. 

```azurecli
az mysql flexible-server connect --help
```

## <a name="test-database-server-connection"></a>Testowanie połączenia z serwerem bazy danych
Uruchom następujący skrypt, aby przetestować i zweryfikować połączenie z bazą danych ze środowiska projektowego.

```azurecli
az mysql flexible-server connect -n <servername> -u <username> -p <password> -d <databasename>
```

**Przykład:**
```azurecli
az mysql flexible-server connect -n mysqldemoserver1 -u dbuser -p "dbpassword" -d newdatabase
```

Powinny zostać wyświetlony następujące dane wyjściowe dla pomyślnego połączenia:

```output
Command group 'mysql flexible-server' is in preview and under development. Reference and support levels: https://aka.ms/CLI_refstatus
Connecting to newdatabase database.
Successfully connected to mysqldemoserver1.
```
Jeśli połączenie nie powiodło się, wypróbuj następujące rozwiązania:
- Sprawdź, czy port 3306 jest otwarty na komputerze klienckim.
- Jeśli nazwa użytkownika i hasło administratora serwera są poprawne
- Jeśli skonfigurowano regułę zapory dla maszyny klienckiej
- Jeśli skonfigurowano serwer z dostępem prywatnym w sieci wirtualnej, upewnij się, że maszyna klienta znajduje się w tej samej sieci wirtualnej.

## <a name="run-single-query"></a>Uruchamianie pojedynczego zapytania
Uruchom następujące polecenie, aby wykonać pojedyncze zapytanie przy użyciu ```--querytext``` argumentu ```-q``` .

```azurecli
az mysql flexible-server connect -n <server-name> -u <username> -p "<password>" -d <database-name> --querytext "<query text>"
```

**Przykład:**
```azurecli
az mysql flexible-server connect -n mysqldemoserver1 -u dbuser -p "dbpassword" -d newdatabase -q "select * from table1;" --output table
```

Zostaną wyświetlone dane wyjściowe, jak pokazano poniżej:

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

## <a name="run-multiple-queries-using-interactive-mode"></a>Uruchamianie wielu zapytań przy użyciu trybu interaktywnego
Można uruchomić wiele zapytań przy użyciu trybu **interaktywnego.** Aby włączyć tryb interaktywny, uruchom następujące polecenie

```azurecli
az mysql flexible-server connect -n <server-name> -u <username> -p <password> --interactive
```

**Przykład:**
```azurecli
az mysql flexible-server connect -n mysqldemoserver1 -u dbuser -p "dbpassword" -d newdatabase --interactive
```

Zostanie wyświetlone środowisko **powłoki MySQL,** jak pokazano poniżej:

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
* [Nawiązywanie połączenia Azure Database for MySQL — elastyczny serwer z zaszyfrowanymi połączeniami](how-to-connect-tls-ssl.md)
* [Zarządzanie serwerem](./how-to-manage-server-cli.md)

