---
title: 'Szybki start: nawiązywanie połączenia przy użyciu interfejsu wiersza polecenia platformy Azure — Azure Database for PostgreSQL — elastyczny serwer'
description: Ten przewodnik Szybki start zawiera kilka sposobów nawiązywania połączeń za pomocą interfejsu wiersza polecenia platformy Azure Azure Database for PostgreSQL — elastyczny serwer.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.custom: mvc, devx-track-azurecli
ms.topic: quickstart
ms.date: 03/06/2021
ms.openlocfilehash: 7526644e02b0ed4d0522ad00a27b691ece98754a
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107479240"
---
# <a name="quickstart-connect-and-query-with-azure-cli--with-azure-database-for-postgresql---flexible-server"></a>Szybki start: nawiązywanie połączeń i wykonywanie zapytań za pomocą interfejsu wiersza polecenia platformy Azure Azure Database for PostgreSQL — elastyczny serwer

> [!IMPORTANT]
> Azure Database for PostgreSQL — serwer elastyczny jest obecnie dostępny w publicznej wersji zapoznawczej.

W tym przewodniku Szybki start pokazano, jak nawiązać połączenie z Azure Database for PostgreSQL elastycznym serwerem przy użyciu interfejsu wiersza polecenia platformy Azure za pomocą ```az postgres flexible-server connect``` polecenia . To polecenie umożliwia testowanie łączności z serwerem bazy danych i uruchamianie zapytań. Można również uruchamiać wiele zapytań przy użyciu trybu interaktywnego. 

## <a name="prerequisites"></a>Wymagania wstępne
- Konto platformy Azure. Jeśli go nie masz, uzyskaj [bezpłatną wersję próbną](https://azure.microsoft.com/free/).
- Zainstaluj [najnowszą](/cli/azure/install-azure-cli) wersję interfejsu wiersza polecenia platformy Azure (2.20.0 lub nowszą)
- Logowanie przy użyciu interfejsu wiersza polecenia platformy Azure za pomocą ```az login``` polecenia 
- Włącz trwałość parametru za pomocą funkcji ```az config param-persist on``` . Trwałość parametrów pomoże Ci użyć kontekstu lokalnego bez konieczności powtarzania wielu argumentów, takich jak grupa zasobów lub lokalizacja.

## <a name="create-an-postgresql-flexible-server"></a>Tworzenie serwera elastycznego PostgreSQL

Najpierw utworzymy zarządzany serwer PostgreSQL. W [Azure Cloud Shell](https://shell.azure.com/)uruchom następujący skrypt i zanotuj nazwę serwera  **,** nazwę **użytkownika** i hasło wygenerowane na podstawie tego polecenia.

```azurecli
az postgres flexible-server create --public-access <your-ip-address>
```
Możesz podać dodatkowe argumenty dla tego polecenia, aby je dostosować. Zobacz wszystkie argumenty polecenia [az postgres flexible-server create](/cli/azure/postgres/flexible-server#az_postgres_flexible_server_create).

## <a name="view-all-the-arguments"></a>Wyświetl wszystko argumenty
Można wyświetlić wszystkie argumenty dla tego polecenia z ```--help``` argumentem. 

```azurecli
az postgresql flexible-server connect --help
```

## <a name="test-database-server-connection"></a>Testowanie połączenia z serwerem bazy danych
Możesz przetestować i zweryfikować połączenie z bazą danych ze środowiska projektowego przy użyciu polecenia .

```azurecli
az postgres flexible-server connect -n <servername> -u <username> -p "<password>" -d <databasename>
```
**Przykład:** 
```azurecli
az postgres flexible-server connect -n postgresdemoserver -u dbuser -p "dbpassword" -d postgres
```
Dane wyjściowe zostaną wyświetlony, jeśli połączenie zostało nawiązaniu pomyślnie.
```output
Command group 'postgres flexible-server' is in preview and under development. Reference and support levels: https://aka.ms/CLI_refstatus
Successfully connected to postgresdemoserver.
Local context is turned on. Its information is saved in working directory C:\mydir. You can run `az local-context off` to turn it off.
Your preference of  are now saved to local context. To learn more, type in `az local-context --help`
```

Jeśli połączenie nie powiodło się, wypróbuj następujące rozwiązania:
- Sprawdź, czy port 5432 jest otwarty na komputerze klienckim.
- jeśli nazwa użytkownika i hasło administratora serwera są poprawne
- Jeśli skonfigurowano regułę zapory dla maszyny klienckiej
- Jeśli skonfigurowano serwer z dostępem prywatnym w sieci wirtualnej, upewnij się, że maszyna klienta znajduje się w tej samej sieci wirtualnej.

## <a name="run-single-query"></a>Uruchamianie pojedynczego zapytania
Pojedyncze zapytanie można wykonać za pomocą polecenia , używając ```--querytext``` argumentu ```-q``` .

```azurecli
az postgres flexible-server connect -n <server-name> -u <username> -p "<password>" -d <database-name> -q "<query-text>"
```

**Przykład:** 
```azurecli
az postgresql flexible-server connect -n postgresdemoserver -u dbuser -p "dbpassword" -d flexibleserverdb -q "select * from table1;" --output table
```

Zostaną wyświetlone dane wyjściowe, jak pokazano poniżej:

```output
Command group 'postgres flexible-server' is in preview and under development. Reference and support levels: https://aka.ms/CLI_refstatus
Successfully connected to postgresdemoserver.
Ran Database Query: 'select * from table1;'
Retrieving first 30 rows of query output, if applicable.
Closed the connection postgresdemoserver.
Local context is turned on. Its information is saved in working directory C:\mydir. You can run `az local-context off` to turn it off.
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
Można uruchomić wiele zapytań przy użyciu trybu **interaktywnego** . Aby włączyć tryb interaktywny, uruchom następujące polecenie

```azurecli
az postgres flexible-server connect -n <servername> -u <username> -p "<password>" -d <databasename>
```

**Przykład:**

```azurecli
az postgresql flexible-server connect -n postgresdemoserver -u dbuser -p "dbpassword" -d flexibleserverdb --interactive
```

Zostanie wyświetlone środowisko **powłoki psql,** jak pokazano poniżej:

```bash
Command group 'postgres flexible-server' is in preview and under development. Reference and support levels: https://aka.ms/CLI_refstatus
Password for earthyTurtle7:
Server: PostgreSQL 12.5
Version: 3.0.0
Chat: https://gitter.im/dbcli/pgcli
Home: http://pgcli.com
postgres> create database pollsdb;
CREATE DATABASE
Time: 0.308s
postgres> exit
Goodbye!
Local context is turned on. Its information is saved in working directory C:\sunitha. You can run `az local-context off` to turn it off.
Your preference of  are now saved to local context. To learn more, type in `az local-context --help`
```


## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Zarządzanie serwerem](./how-to-manage-server-cli.md)
