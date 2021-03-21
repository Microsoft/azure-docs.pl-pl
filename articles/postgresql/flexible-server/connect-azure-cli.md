---
title: 'Szybki Start: Łączenie przy użyciu interfejsu wiersza polecenia platformy Azure — Azure Database for PostgreSQL — elastyczny serwer'
description: Ten przewodnik Szybki Start oferuje kilka sposobów nawiązywania połączenia z interfejsem wiersza polecenia platformy Azure z serwerem elastycznym Azure Database for PostgreSQL.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.custom: mvc
ms.topic: quickstart
ms.date: 03/06/2021
ms.openlocfilehash: f4eec89aadee1966271286b9280916af973e4b1c
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102614347"
---
# <a name="quickstart-connect-and-query-with-azure-cli--with-azure-database-for-postgresql---flexible-server"></a>Szybki Start: łączenie i wykonywanie zapytań przy użyciu interfejsu wiersza polecenia platformy Azure z serwerem elastycznym Azure Database for PostgreSQL

> [!IMPORTANT]
> Serwer elastyczny Azure Database for PostgreSQL jest obecnie w publicznej wersji zapoznawczej.

Ten przewodnik Szybki Start przedstawia sposób nawiązywania połączeń z serwerem elastycznym Azure Database for PostgreSQL przy użyciu interfejsu wiersza polecenia platformy Azure z ```az postgres flexible-server connect``` poleceniem. To polecenie umożliwia przetestowanie łączności z serwerem bazy danych i uruchamianie zapytań. Można również uruchomić wiele zapytań w trybie interaktywnym. 

## <a name="prerequisites"></a>Wymagania wstępne
- Konto platformy Azure. Jeśli go nie masz, [Uzyskaj bezpłatną wersję próbną](https://azure.microsoft.com/free/).
- Zainstaluj najnowszą wersję [interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) (2.20.0 lub nowszą)
- Logowanie przy użyciu interfejsu wiersza polecenia platformy Azure z ```az login``` poleceniem 
- Włącz trwałość parametru za pomocą ```az config param-persist on``` . Trwałość parametru ułatwi korzystanie z kontekstu lokalnego bez konieczności powtarzania wielu argumentów, takich jak grupa zasobów lub lokalizacja.

## <a name="create-an-postgresql-flexible-server"></a>Utwórz serwer elastyczny PostgreSQL

Pierwszym krokiem jest utworzenie zarządzanego serwera PostgreSQL. W [Azure Cloud Shell](https://shell.azure.com/)uruchom następujący skrypt, a następnie zanotuj **nazwę serwera**, nazwę **użytkownika** i  **hasło** wygenerowane na podstawie tego polecenia.

```azurecli
az postgres flexible-server create --public-access <your-ip-address>
```
Możesz podać dodatkowe argumenty dla tego polecenia, aby je dostosować. Zobacz wszystkie argumenty dla [AZ Postgres elastyczny-Server Create](/cli/azure/postgres/flexible-server#az_postgres_flexible_server_create).

## <a name="view-all-the-arguments"></a>Wyświetl wszystkie argumenty
Można wyświetlić wszystkie argumenty dla tego polecenia z ```--help``` argumentem. 

```azurecli
az postgresql flexible-server connect --help
```

## <a name="test-database-server-connection"></a>Testuj połączenie z serwerem bazy danych
Możesz testować i weryfikować połączenie z bazą danych ze środowiska deweloperskiego za pomocą polecenia.

```azurecli
az postgres flexible-server connect -n <servername> -u <username> -p "<password>" -d <databasename>
```
**Przykład:** 
```azurecli
az postgres flexible-server connect -n postgresdemoserver -u dbuser -p "dbpassword" -d postgres
```
Dane wyjściowe będą wyświetlane w przypadku pomyślnego nawiązania połączenia.
```output
Command group 'postgres flexible-server' is in preview and under development. Reference and support levels: https://aka.ms/CLI_refstatus
Successfully connected to postgresdemoserver.
Local context is turned on. Its information is saved in working directory C:\mydir. You can run `az local-context off` to turn it off.
Your preference of  are now saved to local context. To learn more, type in `az local-context --help`
```

Jeśli nawiązanie połączenia nie powiodło się, wypróbuj następujące rozwiązania:
- Sprawdź, czy na komputerze klienckim jest otwarty port 5432.
- Jeśli nazwa użytkownika i hasło administratora serwera są poprawne
- Jeśli skonfigurowano regułę zapory dla komputera klienckiego
- Jeśli serwer został skonfigurowany z dostępem prywatnym w sieci wirtualnej, upewnij się, że komputer kliencki znajduje się w tej samej sieci wirtualnej.

## <a name="run-single-query"></a>Uruchom pojedyncze zapytanie
Można wykonać pojedyncze zapytanie przy użyciu ```--querytext``` argumentu polecenie, ```-q``` .

```azurecli
az postgres flexible-server connect -n <server-name> -u <username> -p "<password>" -d <database-name> -q "<query-text>"
```

**Przykład:** 
```azurecli
az postgresql flexible-server connect -n postgresdemoserver -u dbuser -p "dbpassword" -d flexibleserverdb -q "select * from table1;" --output table
```

Zobaczysz dane wyjściowe, jak pokazano poniżej:

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

## <a name="run-multiple-queries-using-interactive-mode"></a>Uruchamianie wielu zapytań w trybie interaktywnym
Można uruchomić wiele zapytań w trybie **interaktywnym** . Aby włączyć tryb interaktywny, uruchom następujące polecenie

```azurecli
az postgres flexible-server connect -n <servername> -u <username> -p "<password>" -d <databasename>
```

**Przykład:**

```azurecli
az postgresql flexible-server connect -n postgresdemoserver -u dbuser -p "dbpassword" -d flexibleserverdb --interactive
```

Zobaczysz środowisko powłoki **PSQL** , jak pokazano poniżej:

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
