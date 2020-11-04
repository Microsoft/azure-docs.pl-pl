---
title: 'Samouczek: projektowanie Azure Database for PostgreSQL-pojedynczego serwera — Azure PowerShell'
description: W tym samouczku pokazano, jak utworzyć, skonfigurować i wykonać zapytania dotyczące pierwszego serwera Azure Database for PostgreSQL-pojedynczego przy użyciu Azure PowerShell.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.devlang: azurepowershell
ms.topic: tutorial
ms.date: 06/08/2020
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: 70edf224494fdf1395d59d1c591d0369b9b20557
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2020
ms.locfileid: "93333022"
---
# <a name="tutorial-design-an-azure-database-for-postgresql---single-server-using-powershell"></a>Samouczek: projektowanie serwera Azure Database for PostgreSQL-pojedynczego przy użyciu programu PowerShell

Azure Database for PostgreSQL jest usługą relacyjnej bazy danych w chmurze firmy Microsoft opartą na aparacie bazy danych PostgreSQL Community Edition. W tym samouczku użyjesz programu PowerShell i innych narzędzi, aby dowiedzieć się, jak:

> [!div class="checklist"]
> - Tworzenie serwera usługi Azure Database for PostgreSQL
> - Konfigurowanie zapory serwera
> - Użycie narzędzia [**psql**](https://www.postgresql.org/docs/9.6/static/app-psql.html) do utworzenia bazy danych
> - Ładowanie przykładowych danych
> - Zapytania o dane
> - Aktualizowanie danych
> - Przywracanie danych

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

Jeśli zdecydujesz się używać programu PowerShell lokalnie, ten artykuł będzie wymagał instalacji modułu AZ PowerShell i nawiązania połączenia z kontem platformy Azure przy użyciu polecenia cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) . Aby uzyskać więcej informacji na temat instalowania modułu AZ PowerShell module, zobacz [Install Azure PowerShell](/powershell/azure/install-az-ps).

> [!IMPORTANT]
> Mimo że moduł AZ. PostgreSql PowerShell jest w wersji zapoznawczej, należy go zainstalować niezależnie od modułu AZ PowerShell przy użyciu następującego polecenia: `Install-Module -Name Az.PostgreSql -AllowPrerelease` .
> Po ogólnym udostępnieniu modułu AZ. PostgreSql PowerShell jest on częścią przyszłych wersji modułu AZ PowerShell i dostępne natywnie z poziomu Azure Cloud Shell.

Jeśli używasz usługi Azure Database for PostgreSQL po raz pierwszy, musisz zarejestrować dostawcę zasobów **Microsoft. DBforPostgreSQL** .

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.DBforPostgreSQL
```

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Jeśli masz wiele subskrypcji platformy Azure, wybierz odpowiednią subskrypcję, w której będą naliczane opłaty za zasoby. Wybierz określony identyfikator subskrypcji za pomocą polecenia cmdlet [Set-AzContext](/powershell/module/az.accounts/set-azcontext) .

```azurepowershell-interactive
Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Utwórz [grupę zasobów platformy Azure](../azure-resource-manager/management/overview.md) za pomocą polecenia cmdlet [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) . Grupa zasobów to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi w formie grupy.

Poniższy przykład tworzy grupę zasobów o nazwie Moja **zasobów** w regionie **zachodnie stany USA** .

```azurepowershell-interactive
New-AzResourceGroup -Name myresourcegroup -Location westus
```

## <a name="create-an-azure-database-for-postgresql-server"></a>Tworzenie serwera usługi Azure Database for PostgreSQL

Utwórz serwer Azure Database for PostgreSQL przy użyciu `New-AzPostgreSqlServer` polecenia cmdlet. Serwer umożliwia zarządzanie wieloma bazami danych. Zwykle dla każdego projektu lub użytkownika używana jest oddzielna baza danych.

W poniższym przykładzie jest tworzony serwer PostgreSQL w regionie **zachodnie stany USA** o nazwie **mydemoserver** w **grupie zasobów z** identyfikatorem **logowania administratora serwera.** Jest to serwer generacji 5 w warstwie cenowej ogólnego przeznaczenia z obsługą dwóch rdzeni wirtualnych i geograficznie nadmiarowych kopii zapasowych. Zanotuj hasło używane w pierwszym wierszu przykładu jako hasło dla konta administratora serwera PostgreSQL.

> [!TIP]
> Nazwa serwera jest mapowana na nazwę usługi DNS i musi być globalnie unikatowa na platformie Azure.

```azurepowershell-interactive
$Password = Read-Host -Prompt 'Please enter your password' -AsSecureString
New-AzPostgreSqlServer -Name mydemoserver -ResourceGroupName myresourcegroup -Sku GP_Gen5_2 -GeoRedundantBackup Enabled -Location westus -AdministratorUsername myadmin -AdministratorLoginPassword $Password
```

Wartość parametru **SKU** jest zgodna z Konwencją **\_ \_ rdzeni wirtualnych generacji obliczeń warstwy** , jak pokazano w poniższych przykładach.

- `-Sku B_Gen5_1` — warstwa podstawowa, 5. generacja, 1 rdzeń wirtualny. Ta opcja to najmniejsza dostępna jednostka SKU.
- `-Sku GP_Gen5_32` — warstwa ogólnego przeznaczenia, 5. generacja, 32 rdzenie wirtualne.
- `-Sku MO_Gen5_2` — warstwa zoptymalizowana pod kątem pamięci, 5. generacja, 2 rdzenie wirtualne.

Aby uzyskać informacje na temat prawidłowych wartości **jednostki SKU** według regionów i warstw, zobacz [Azure Database for PostgreSQL warstwy cenowe](./concepts-pricing-tiers.md).

Należy rozważyć użycie warstwy cenowej Basic, jeśli lekkie obliczenia i we/wy są odpowiednie dla obciążenia.

> [!IMPORTANT]
> Serwery utworzone w warstwie cenowej podstawowa nie mogą być później skalowane do ogólnego przeznaczenia ani zoptymalizowane pod kątem pamięci i nie mogą być replikowane geograficznie.

## <a name="configure-a-firewall-rule"></a>Konfigurowanie reguły zapory

Utwórz regułę zapory na poziomie serwera Azure Database for PostgreSQL przy użyciu `New-AzPostgreSqlFirewallRule` polecenia cmdlet. Reguła zapory na poziomie serwera pozwala aplikacji zewnętrznej, takiej jak `psql` Narzędzie wiersza polecenia lub PostgreSQL Workbench, nawiązać połączenie z serwerem za pomocą zapory usługi Azure Database for PostgreSQL.

Poniższy przykład tworzy regułę zapory o nazwie **AllowMyIP** , która umożliwia nawiązywanie połączeń z określonego adresu IP (192.168.0.1). Zastąp adres IP lub zakres adresów IP odpowiadających lokalizacji, z której nawiązujesz połączenie.

```azurepowershell-interactive
New-AzPostgreSqlFirewallRule -Name AllowMyIP -ResourceGroupName myresourcegroup -ServerName mydemoserver -StartIPAddress 192.168.0.1 -EndIPAddress 192.168.0.1
```

> [!NOTE]
> Połączenia z Azure Database for PostgreSQLą komunikację przez port 3306. Jeśli próbujesz nawiązać połączenie z sieci firmowej, ruch wychodzący na porcie 3306 może być zablokowany. W tym scenariuszu można nawiązać połączenie z serwerem tylko wtedy, gdy dział IT otworzy port 3306.

## <a name="get-the-connection-information"></a>Pobieranie informacji o połączeniu

Aby nawiązać połączenie z serwerem, musisz podać informacje o hoście i poświadczenia dostępu. Użyj poniższego przykładu, aby określić informacje o połączeniu. Zanotuj wartości **FullyQualifiedDomainName** i **AdministratorLogin**.

```azurepowershell-interactive
Get-AzPostgreSqlServer -Name mydemoserver -ResourceGroupName myresourcegroup |
  Select-Object -Property FullyQualifiedDomainName, AdministratorLogin
```

```Output
FullyQualifiedDomainName                    AdministratorLogin
------------------------                    ------------------
mydemoserver.postgresql.database.azure.com       myadmin
```

## <a name="connect-to-postgresql-database-using-psql"></a>Nawiązywanie połączenia z bazą danych PostgreSQL za pomocą narzędzia psql

Jeśli na Twoim komputerze klienckim jest zainstalowany program PostgreSQL, możesz użyć lokalnego wystąpienia narzędzia [psql](https://www.postgresql.org/docs/current/static/app-psql.html), aby nawiązać połączenie z serwerem Azure PostgreSQL. Możesz również uzyskać dostęp do wstępnie zainstalowanej wersji `psql` narzędzia wiersza polecenia w Azure Cloud Shell, wybierając przycisk **Wypróbuj** dla przykładu kodu w tym artykule. Innymi sposobami dostępu do Azure Cloud Shell jest wybranie przycisku **>_** w prawym górnym rogu Azure Portal lub przez odwiedzenie [Shell.Azure.com](https://shell.azure.com/).

1. Nawiąż połączenie z serwerem usługi Azure PostgreSQL za pomocą `psql` narzędzia wiersza polecenia.

   ```azurepowershell-interactive
   psql --host=<servername> --port=<port> --username=<user@servername> --dbname=<dbname>
   ```

   Na przykład następujące polecenie nawiązuje połączenie z domyślną bazą danych o nazwie **Postgres** na serwerze PostgreSQL `mydemoserver.postgres.database.azure.com` przy użyciu poświadczeń dostępu. Gdy zostanie wyświetlony monit o podanie hasła, wprowadź wybrane hasło `<server_admin_password>`.

   ```azurepowershell-interactive
   psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=myadmin@mydemoserver --dbname=postgres
   ```

   > [!TIP]
   > Jeśli wolisz używać ścieżki URL w celu nawiązania połączenia z usługą Postgres, adres URL Koduj znak @ w nazwie użytkownika za pomocą `%40` . Na przykład parametry połączenia dla PSQL byłyby, `psql postgresql://myadmin%40mydemoserver@mydemoserver.postgres.database.azure.com:5432/postgres`

1. Po nawiązaniu połączenia z serwerem utwórz pustą bazę danych za pomocą wiersza polecenia.

   ```sql
   CREATE DATABASE mypgsqldb;
   ```

1. W wierszu polecenia wykonaj następujące polecenie, aby przełączyć połączenie na nowo utworzoną bazę danych **mypgsqldb** :

   ```sql
   \c mypgsqldb
   ```

## <a name="create-tables-in-the-database"></a>Tworzenie tabel w bazie danych

Teraz, gdy wiesz, jak nawiązać połączenie z bazą danych Azure Database for PostgreSQL, wykonaj niektóre podstawowe zadania.

Najpierw utwórz tabelę i załaduj do niej dane. Utwórz tabelę z informacjami o spisie.

```sql
CREATE TABLE inventory (
  id serial PRIMARY KEY,
  name VARCHAR(50),
  quantity INTEGER
);
```

## <a name="load-data-into-the-tables"></a>Ładowanie danych do tabel

Teraz, gdy masz tabelę, wstaw do niej dane. W otwartym oknie wiersza polecenia uruchom następujące zapytanie, aby wstawić wiersze danych.

```sql
INSERT INTO inventory (id, name, quantity) VALUES (1, 'banana', 150);
INSERT INTO inventory (id, name, quantity) VALUES (2, 'orange', 154);
```

W utworzonej wcześniej tabeli masz teraz dwa wiersze przykładowych danych.

## <a name="query-and-update-the-data-in-the-tables"></a>Wykonywanie zapytania względem danych w tabelach i aktualizowanie tych danych

Wykonaj następujące zapytanie, aby pobrać informacje z tabeli bazy danych.

```sql
SELECT * FROM inventory;
```

Możesz także zaktualizować dane w tabelach.

```sql
UPDATE inventory SET quantity = 200 WHERE name = 'banana';
```

Wiersz jest aktualizowany podczas pobierania danych.

```sql
SELECT * FROM inventory;
```

## <a name="restore-a-database-to-a-previous-point-in-time"></a>Przywracanie bazy danych do określonego punktu w czasie

Serwer można przywrócić do wcześniejszego punktu w czasie. Przywrócone dane są kopiowane na nowy serwer, a istniejący serwer pozostaje niezmieniony. Na przykład jeśli tabela zostanie przypadkowo porzucona, można przywrócić ją do godziny, w której nastąpiło odrzucanie. Następnie można pobrać brakującą tabelę i dane z przywróconej kopii serwera.

Aby przywrócić serwer programu, należy użyć `Restore-AzPostgreSqlServer` polecenia cmdlet programu PowerShell.

### <a name="run-the-restore-command"></a>Uruchom polecenie Restore

Aby przywrócić serwer, uruchom poniższy przykład z programu PowerShell.

```azurepowershell-interactive
$restorePointInTime = (Get-Date).AddMinutes(-10)
Get-AzPostgreSqlServer -Name mydemoserver -ResourceGroupName myresourcegroup |
  Restore-AzPostgreSqlServer -Name mydemoserver-restored -ResourceGroupName myresourcegroup -RestorePointInTime $restorePointInTime -UsePointInTimeRestore
```

Po przywróceniu serwera do wcześniejszego punktu w czasie zostanie utworzony nowy serwer. Oryginalny serwer i jego bazy danych z określonego punktu w czasie są kopiowane na nowy serwer.

Wartości lokalizacji i warstwy cenowej dla przywróconego serwera pozostają takie same, jak oryginalny serwer.

Po zakończeniu procesu przywracania Znajdź nowy serwer i sprawdź, czy dane są przywracane zgodnie z oczekiwaniami. Nowy serwer ma taką samą nazwę logowania administratora serwera i hasło, które były prawidłowe dla istniejącego serwera w momencie uruchomienia przywracania. Hasło można zmienić na stronie **Przegląd** nowego serwera.

Nowy serwer utworzony podczas przywracania nie ma punktów końcowych usługi sieci wirtualnej, które istniały na oryginalnym serwerze. Te reguły należy osobno skonfigurować dla nowego serwera. Przywrócono reguły zapory z oryginalnego serwera.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

W poprzednich krokach zostały utworzone zasoby platformy Azure w grupie serwerów. Jeśli nie chcesz potrzebować tych zasobów w przyszłości, Usuń grupę serwerów. Naciśnij przycisk *Usuń* na stronie *Przegląd* dla swojej grupy serwerów. Po wyświetleniu monitu na stronie podręcznej Potwierdź nazwę grupy serwerów, a następnie kliknij przycisk *Usuń* końcowego.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Jak utworzyć kopię zapasową i przywrócić serwer Azure Database for PostgreSQL przy użyciu programu PowerShell](howto-restore-server-powershell.md)