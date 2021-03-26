---
title: 'Samouczek: projektowanie serwera Azure Database for PostgreSQL-pojedynczego — interfejs wiersza polecenia platformy Azure'
description: W tym samouczku przedstawiono sposób tworzenia, konfigurowania i wykonywania zapytań dotyczących pierwszego serwera Azure Database for PostgreSQL-pojedynczego przy użyciu interfejsu wiersza polecenia platformy Azure.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.custom: mvc, devx-track-azurecli
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 06/25/2019
ms.openlocfilehash: 478fc175304a24536d28807915f4a4ec05a7158e
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105605879"
---
# <a name="tutorial-design-an-azure-database-for-postgresql---single-server-using-azure-cli"></a>Samouczek: projektowanie serwera Azure Database for PostgreSQL-pojedynczego przy użyciu interfejsu wiersza polecenia platformy Azure 
W tym samouczku dowiesz się, jak wykonywać następujące czynności, używając interfejsu wiersza polecenia platformy Azure:
> [!div class="checklist"]
> * Tworzenie serwera usługi Azure Database for PostgreSQL
> * Konfigurowanie zapory serwera
> * Użycie narzędzia [**psql**](https://www.postgresql.org/docs/9.6/static/app-psql.html) do utworzenia bazy danych
> * Ładowanie przykładowych danych
> * Zapytania o dane
> * Aktualizowanie danych
> * Przywracanie danych

Aby uruchamiać polecenia z tego samouczka, możesz użyć usługi Azure Cloud Shell w przeglądarce lub [zainstalować interfejs wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli) na swoim komputerze.

## <a name="prerequisites"></a>Wymagania wstępne
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten artykuł będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.0 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli). 

Jeśli masz wiele subskrypcji, wybierz odpowiednią subskrypcję, w której zasób istnieje lub dla której są za niego naliczane opłaty. Wybierz określony identyfikator subskrypcji na Twoim koncie za pomocą polecenia [az account set](/cli/azure/account).
```azurecli-interactive
az account set --subscription 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów
Utwórz [grupę zasobów platformy Azure](../azure-resource-manager/management/overview.md) za pomocą polecenia [az group create](/cli/azure/group). Grupa zasobów to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi w formie grupy. Poniższy przykład obejmuje tworzenie grupy zasobów o nazwie `myresourcegroup` w lokalizacji `westus`.
```azurecli-interactive
az group create --name myresourcegroup --location westus
```

## <a name="create-an-azure-database-for-postgresql-server"></a>Tworzenie serwera usługi Azure Database for PostgreSQL
Utwórz [serwer usługi Azure Database for PostgreSQL](overview.md) za pomocą polecenia [az postgres server create](/cli/azure/postgres/server). Serwer zawiera grupę baz danych zarządzanych jako grupa. 

W poniższym przykładzie zostanie utworzony serwer o nazwie `mydemoserver` w grupie zasobów `myresourcegroup` z identyfikatorem logowania administratora serwera `myadmin`. Nazwa serwera jest mapowana na nazwę DNS i dlatego musi być globalnie unikatowa w ramach platformy Azure. Zastąp zmienną `<server_admin_password>` swoją własną wartością. Jest to serwer 5. generacji ogólnego przeznaczenia z 2 rdzeniami wirtualnymi.
```azurecli-interactive
az postgres server create --resource-group myresourcegroup --name mydemoserver --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen5_2 --version 9.6
```
Wartość parametru sku-name jest zgodna z konwencją {warstwa cenowa}\_{generacja obliczeniowa}\_{rdzenie wirtualne}, jak pokazano w przykładach poniżej:
+ `--sku-name B_Gen5_2` mapuje do warstwy Podstawowa, Gen 5 i 2 rdzeni wirtualnych.
+ `--sku-name GP_Gen5_32` — warstwa ogólnego przeznaczenia, 5. generacja, 32 rdzenie wirtualne.
+ `--sku-name MO_Gen5_2` — warstwa zoptymalizowana pod kątem pamięci, 5. generacja, 2 rdzenie wirtualne.

Aby ustalić poprawne wartości dla poszczególnych regionów i warstw, zapoznaj się z dokumentacją [warstw cenowych](./concepts-pricing-tiers.md).

> [!IMPORTANT]
> Nazwa logowania i hasło administratora serwera określone w tym miejscu będą wymagane do logowania do serwera i jego baz danych w późniejszej części tego przewodnika Szybki start. Zapamiętaj lub zapisz te informacje do wykorzystania w przyszłości.

Domyślnie baza danych **postgres** zostanie utworzona na Twoim serwerze. Baza danych [postgres](https://www.postgresql.org/docs/9.6/static/app-initdb.html) to domyślna baza danych przeznaczona do użycia dla użytkowników oraz na potrzeby narzędzi i aplikacji innych firm. 


## <a name="configure-a-server-level-firewall-rule"></a>Konfigurowanie reguły zapory na poziomie serwera

Utwórz regułę zapory na poziomie serwera Azure PostgreSQL za pomocą polecenia [az postgres server firewall-rule create](/cli/azure/postgres/server/firewall-rule). Reguła zapory na poziomie serwera pozwala aplikacji zewnętrznej, takiej jak narzędzie [psql](https://www.postgresql.org/docs/9.2/static/app-psql.html) lub [PgAdmin](https://www.pgadmin.org/), na nawiązywanie połączeń z Twoim serwerem przez zaporę usługi Azure PostgreSQL. 

Możesz ustawić regułę zapory uwzględniającą zakres adresów IP, aby mieć możliwość nawiązywania połączeń z Twojej sieci. W poniższym przykładzie użyto polecenia [az postgres server firewall-rule create](/cli/azure/postgres/server/firewall-rule) w celu utworzenia reguły zapory `AllowMyIP` zezwalającej na połączenia z pojedynczego adresu IP.

```azurecli-interactive
az postgres server firewall-rule create --resource-group myresourcegroup --server mydemoserver --name AllowMyIP --start-ip-address 192.168.0.1 --end-ip-address 192.168.0.1
```

Aby ograniczyć dostęp do serwera Azure PostgreSQL tylko do własnej sieci, możesz ustawić regułę zapory, która obejmuje tylko zakres adresów IP sieci firmowej.

> [!NOTE]
> Serwer Azure PostgreSQL komunikuje się przez port 5432. Podczas nawiązywania połączenia z sieci firmowej ruch wychodzący przez port 5432 może być blokowany przez zaporę sieciową. Aby było możliwe nawiązywanie połączenia z serwerem usługi Azure SQL Database, poproś dział IT o otwarcie portu 5432.
>

## <a name="get-the-connection-information"></a>Pobieranie informacji o połączeniu

Aby nawiązać połączenie z serwerem, musisz podać informacje o hoście i poświadczenia dostępu.
```azurecli-interactive
az postgres server show --resource-group myresourcegroup --name mydemoserver
```

Wynik jest w formacie JSON. Zanotuj wartości **administratorLogin** i **fullyQualifiedDomainName**.
```json
{
  "administratorLogin": "myadmin",
  "earliestRestoreDate": null,
  "fullyQualifiedDomainName": "mydemoserver.postgres.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforPostgreSQL/servers/mydemoserver",
  "location": "westus",
  "name": "mydemoserver",
  "resourceGroup": "myresourcegroup",
  "sku": {
    "capacity": 2,
    "family": "Gen5",
    "name": "GP_Gen5_2",
    "size": null,
    "tier": "GeneralPurpose"
  },
  "sslEnforcement": "Enabled",
  "storageProfile": {
    "backupRetentionDays": 7,
    "geoRedundantBackup": "Disabled",
    "storageMb": 5120
  },
  "tags": null,
  "type": "Microsoft.DBforPostgreSQL/servers",
  "userVisibleState": "Ready",
  "version": "9.6"

}
```

## <a name="connect-to-azure-database-for-postgresql-database-using-psql"></a>Nawiązywanie połączenia z bazą danych Azure Database for PostgreSQL za pomocą narzędzia psql
Jeśli na komputerze klienckim jest zainstalowany program PostgreSQL, możesz użyć lokalnego wystąpienia programu [PSQL](https://www.postgresql.org/docs/9.6/static/app-psql.html)lub konsoli Azure Cloud Console, aby nawiązać połączenie z serwerem Azure PostgreSQL. Użyj teraz narzędzia wiersza polecenia psql, aby nawiązać połączenie z serwerem usługi Azure Database for PostgreSQL.

1. Uruchom poniższe polecenie psql w celu nawiązania połączenia z bazą danych Azure Database for PostgreSQL:
   ```
   psql --host=<servername> --port=<port> --username=<user@servername> --dbname=<dbname>
   ```

   Na przykład poniższe polecenie służy do nawiązywania połączenia z domyślną bazą danych o nazwie **postgres** na Twoim serwerze PostgreSQL **mydemoserver.postgres.database.azure.com** za pomocą poświadczeń dostępu. Gdy zostanie wyświetlony monit o podanie hasła, wprowadź wybrane hasło `<server_admin_password>`.
  
   ```
   psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=myadmin@mydemoserver --dbname=postgres
   ```

   > [!TIP]
   > Jeśli wolisz używać ścieżki URL w celu nawiązania połączenia z usługą Postgres, adres URL Koduj znak @ w nazwie użytkownika za pomocą `%40` . Na przykład parametry połączenia dla PSQL byłyby,
   > ```
   > psql postgresql://myadmin%40mydemoserver@mydemoserver.postgres.database.azure.com:5432/postgres
   > ```

2. Po nawiązaniu połączenia z serwerem utwórz pustą bazę danych za pomocą wiersza polecenia:
   ```sql
   CREATE DATABASE mypgsqldb;
   ```

3. W wierszu polecenia wykonaj następujące polecenie, aby przełączyć połączenie na nowo utworzoną bazę danych **mypgsqldb**:
   ```sql
   \c mypgsqldb
   ```

## <a name="create-tables-in-the-database"></a>Tworzenie tabel w bazie danych
Teraz, gdy wiesz, jak nawiązać połączenie z usługą Azure Database for PostgreSQL, możesz wykonać niektóre podstawowe zadania:

Najpierw utwórz tabelę i załaduj do niej dane. Na przykład utwórz tabelę służącą do śledzenia informacji o spisie:
```sql
CREATE TABLE inventory (
    id serial PRIMARY KEY, 
    name VARCHAR(50), 
    quantity INTEGER
);
```

Teraz możesz wyświetlić nowo utworzoną tabelę na liście tabel, wpisując:
```sql
\dt
```

## <a name="load-data-into-the-table"></a>Ładowanie danych do tabel
Teraz, po utworzeniu tabeli, wstaw do niej trochę danych. W otwartym oknie wiersza polecenia uruchom następujące zapytanie, aby wstawić wiersze danych:
```sql
INSERT INTO inventory (id, name, quantity) VALUES (1, 'banana', 150); 
INSERT INTO inventory (id, name, quantity) VALUES (2, 'orange', 154);
```

Do utworzonej wcześniej tabeli dodano właśnie dwa wiersze przykładowych danych.

## <a name="query-and-update-the-data-in-the-tables"></a>Wykonywanie zapytania względem danych w tabelach i aktualizowanie tych danych
Wykonaj następujące zapytanie, aby pobrać informacje z tabeli ze spisem: 
```sql
SELECT * FROM inventory;
```

Możesz także zaktualizować dane w tabeli ze spisem:
```sql
UPDATE inventory SET quantity = 200 WHERE name = 'banana';
```

Po pobraniu danych możesz zobaczyć zaktualizowane wartości:
```sql
SELECT * FROM inventory;
```

## <a name="restore-a-database-to-a-previous-point-in-time"></a>Przywracanie bazy danych do określonego punktu w czasie
Załóżmy, że tabela została przypadkowo usunięta. W takiej sytuacji niełatwo jest odzyskać dane. Usługa Azure Database for PostgreSQL umożliwia powrót do dowolnego punktu w czasie, dla którego istnieją kopie zapasowe na serwerze (określa to skonfigurowany okres przechowywania kopii zapasowych), i przywrócenie tego punktu w czasie na nowym serwerze. Przy użyciu tego nowego serwera można odzyskać usunięte dane. 

Poniższe polecenie służy do przywrócenia przykładowego serwera do punktu przed dodaniem tabeli:
```azurecli-interactive
az postgres server restore --resource-group myresourcegroup --name mydemoserver-restored --restore-point-in-time 2017-04-13T13:59:00Z --source-server mydemoserver
```

Polecenie `az postgres server restore` wymaga podania następujących parametrów:

| Ustawienie | Sugerowana wartość | Opis  |
| --- | --- | --- |
| resource-group |  myresourcegroup |  Grupa zasobów, w której istnieje serwer źródłowy.  |
| name | mydemoserver-restored | Nazwa nowego serwera utworzonego za pomocą polecenie przywracania. |
| restore-point-in-time | 2017-04-13T13:59:00Z | Wybierz punkt w czasie, do którego ma zostać przeprowadzone przywrócenie. Ta data i godzina musi przypadać w okresie przechowywania kopii zapasowej serwera źródłowego. Użyj daty i godziny w formacie ISO8601. Na przykład możesz użyć własnej lokalnej strefy czasowej, takiej jak `2017-04-13T05:59:00-08:00`, lub użyć formatu UTC Zulu `2017-04-13T13:59:00Z`. |
| source-server | mydemoserver | Nazwa lub identyfikator serwera źródłowego, z którego ma zostać przeprowadzone przywrócenie. |

Przywrócenie serwera do określonego punktu w czasie powoduje utworzenie nowego serwera skopiowanego na podstawie oryginalnego serwera z określonego punktu w czasie. Wartości lokalizacji i warstwy cenowej dla przywróconego serwera są takie same, jak w przypadku serwera źródłowego.

Polecenie jest synchroniczne i zostanie zakończone po przywróceniu serwera. Po zakończeniu przywracania zlokalizuj nowy serwer, który został utworzony. Sprawdź, czy dane zostały przywrócone zgodnie z oczekiwaniami.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

W poprzednich krokach zostały utworzone zasoby platformy Azure w grupie serwerów. Jeśli nie chcesz potrzebować tych zasobów w przyszłości, Usuń grupę serwerów. Naciśnij przycisk *Usuń* na stronie *Przegląd* dla swojej grupy serwerów. Po wyświetleniu monitu na stronie podręcznej Potwierdź nazwę grupy serwerów, a następnie kliknij przycisk *Usuń* końcowego.


## <a name="next-steps"></a>Następne kroki
W tym samouczku omówiono sposób użycia interfejsu wiersza polecenia platformy Azure i innych narzędzi w celu wykonania następujących czynności:
> [!div class="checklist"]
> * Tworzenie serwera usługi Azure Database for PostgreSQL
> * Konfigurowanie zapory serwera
> * Tworzenie bazy danych za pomocą narzędzia **PSQL**
> * Ładowanie przykładowych danych
> * Zapytania o dane
> * Aktualizowanie danych
> * Przywracanie danych

> [!div class="nextstepaction"]
> [Projektowanie pierwszej bazy danych usługi Azure Database for PostgreSQL przy użyciu witryny Azure Portal](tutorial-design-database-using-azure-portal.md)
