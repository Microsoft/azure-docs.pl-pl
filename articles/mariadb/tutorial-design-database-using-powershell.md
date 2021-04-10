---
title: 'Samouczek: projektowanie serwera Azure PowerShell-Azure Database for MariaDB'
description: W tym samouczku wyjaśniono, jak utworzyć serwer Azure Database for MariaDB i bazę danych oraz zarządzać nim przy użyciu programu PowerShell.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.devlang: azurepowershell
ms.topic: tutorial
ms.date: 05/26/2020
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: 9b4500df459e4d4ef67f97dc4fa923988f30401b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98664505"
---
# <a name="tutorial-design-an-azure-database-for-mariadb-using-powershell"></a>Samouczek: projektowanie Azure Database for MariaDB przy użyciu programu PowerShell

Usługa Azure Database for MariaDB jest usługą relacyjnej bazy danych w chmurze firmy Microsoft opartą na aparacie bazy danych MariaDB Community Edition. W tym samouczku użyjesz programu PowerShell i innych narzędzi, aby dowiedzieć się, jak:

> [!div class="checklist"]
> - Tworzenie usługi Azure Database for MariaDB
> - Konfigurowanie zapory serwera
> - Tworzenie bazy danych za pomocą [narzędzia wiersza polecenia MySQL](https://dev.mysql.com/doc/refman/5.6/en/mysql.html)
> - Ładowanie przykładowych danych
> - Zapytania o dane
> - Aktualizowanie danych
> - Przywracanie danych

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

Jeśli zdecydujesz się używać programu PowerShell lokalnie, ten artykuł będzie wymagał instalacji modułu AZ PowerShell i nawiązania połączenia z kontem platformy Azure przy użyciu polecenia cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) . Aby uzyskać więcej informacji na temat instalowania modułu AZ PowerShell module, zobacz [Install Azure PowerShell](/powershell/azure/install-az-ps).

> [!IMPORTANT]
> Mimo że moduł AZ. MariaDb PowerShell jest w wersji zapoznawczej, należy go zainstalować niezależnie od modułu AZ PowerShell przy użyciu następującego polecenia: `Install-Module -Name Az.MariaDb -AllowPrerelease` .
> Po ogólnym udostępnieniu modułu AZ. MariaDb PowerShell jest on częścią przyszłych wersji modułu AZ PowerShell i dostępne natywnie z poziomu Azure Cloud Shell.

Jeśli używasz usługi Azure Database for MariaDB po raz pierwszy, musisz zarejestrować dostawcę zasobów **Microsoft. DBforMariaDB** .

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.DBforMariaDB
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

## <a name="create-an-azure-database-for-mariadb-server"></a>Tworzenie serwera usługi Azure Database for MariaDB

Utwórz serwer Azure Database for MariaDB przy użyciu `New-AzMariaDbServer` polecenia cmdlet. Serwer umożliwia zarządzanie wieloma bazami danych. Zwykle dla każdego projektu lub użytkownika używana jest oddzielna baza danych.

W poniższym przykładzie jest tworzony serwer MariaDB w regionie **zachodnie stany USA** o nazwie **mydemoserver** w **grupie zasobów z** identyfikatorem **logowania administratora serwera.** Jest to serwer generacji 5 w warstwie cenowej ogólnego przeznaczenia z obsługą dwóch rdzeni wirtualnych i geograficznie nadmiarowych kopii zapasowych. Zanotuj hasło używane w pierwszym wierszu przykładu jako hasło dla konta administratora serwera MariaDB.

> [!TIP]
> Nazwa serwera jest mapowana na nazwę usługi DNS i musi być globalnie unikatowa na platformie Azure.

```azurepowershell-interactive
$Password = Read-Host -Prompt 'Please enter your password' -AsSecureString
New-AzMariaDbServer -Name mydemoserver -ResourceGroupName myresourcegroup -Sku GP_Gen5_2 -GeoRedundantBackup Enabled -Location westus -AdministratorUsername myadmin -AdministratorLoginPassword $Password
```

Wartość parametru **SKU** jest zgodna z Konwencją **\_ \_ rdzeni wirtualnych generacji obliczeń warstwy** , jak pokazano w poniższych przykładach.

- `-Sku B_Gen5_1` — warstwa podstawowa, 5. generacja, 1 rdzeń wirtualny. Ta opcja to najmniejsza dostępna jednostka SKU.
- `-Sku GP_Gen5_32` — warstwa ogólnego przeznaczenia, 5. generacja, 32 rdzenie wirtualne.
- `-Sku MO_Gen5_2` — warstwa zoptymalizowana pod kątem pamięci, 5. generacja, 2 rdzenie wirtualne.

Aby uzyskać informacje na temat prawidłowych wartości **jednostki SKU** według regionów i warstw, zobacz [Azure Database for MariaDB warstwy cenowe](./concepts-pricing-tiers.md).

Należy rozważyć użycie warstwy cenowej Basic, jeśli lekkie obliczenia i we/wy są odpowiednie dla obciążenia.

> [!IMPORTANT]
> Serwery utworzone w warstwie cenowej podstawowa nie mogą być później skalowane do ogólnego przeznaczenia ani zoptymalizowane pod kątem pamięci i nie mogą być replikowane geograficznie.

## <a name="configure-a-firewall-rule"></a>Konfigurowanie reguły zapory

Utwórz regułę zapory na poziomie serwera Azure Database for MariaDB przy użyciu `New-AzMariaDbFirewallRule` polecenia cmdlet. Reguła zapory na poziomie serwera pozwala aplikacji zewnętrznej, takiej jak `mysql` Narzędzie wiersza polecenia lub MariaDB Workbench, nawiązać połączenie z serwerem za pomocą zapory usługi Azure Database for MariaDB.

Poniższy przykład tworzy regułę zapory o nazwie **AllowMyIP** , która umożliwia nawiązywanie połączeń z określonego adresu IP (192.168.0.1). Zastąp adres IP lub zakres adresów IP odpowiadających lokalizacji, z której nawiązujesz połączenie.

```azurepowershell-interactive
New-AzMariaDbFirewallRule -Name AllowMyIP -ResourceGroupName myresourcegroup -ServerName mydemoserver -StartIPAddress 192.168.0.1 -EndIPAddress 192.168.0.1
```

> [!NOTE]
> Połączenia z usługą Azure Database for MariaDB korzystają z portu 3306. Jeśli próbujesz nawiązać połączenie z sieci firmowej, ruch wychodzący na porcie 3306 może być zablokowany. W tym scenariuszu można nawiązać połączenie z serwerem tylko wtedy, gdy dział IT otworzy port 3306.

## <a name="get-the-connection-information"></a>Pobieranie informacji o połączeniu

Aby nawiązać połączenie z serwerem, musisz podać informacje o hoście i poświadczenia dostępu. Użyj poniższego przykładu, aby określić informacje o połączeniu. Zanotuj wartości **FullyQualifiedDomainName** i **AdministratorLogin**.

```azurepowershell-interactive
Get-AzMariaDbServer -Name mydemoserver -ResourceGroupName myresourcegroup |
  Select-Object -Property FullyQualifiedDomainName, AdministratorLogin
```

```Output
FullyQualifiedDomainName                    AdministratorLogin
------------------------                    ------------------
mydemoserver.mariadb.database.azure.com       myadmin
```

## <a name="connect-to-the-server-using-the-mysql-command-line-tool"></a>Nawiązywanie połączenia z serwerem za pomocą narzędzia wiersza polecenia MySQL

Połącz się z serwerem za pomocą `mysql` narzędzia wiersza polecenia. Aby pobrać i zainstalować narzędzie wiersza polecenia, zobacz [MariaDB Community downloads](https://dev.mysql.com/downloads/shell/). Możesz również uzyskać dostęp do wstępnie zainstalowanej wersji `mysql` narzędzia wiersza polecenia w Azure Cloud Shell, wybierając przycisk **Wypróbuj** dla przykładu kodu w tym artykule. Innymi sposobami dostępu do Azure Cloud Shell jest wybranie przycisku **>_** w prawym górnym rogu Azure Portal lub przez odwiedzenie [Shell.Azure.com](https://shell.azure.com/).

```azurepowershell-interactive
mysql -h mydemoserver.mariadb.database.azure.com -u myadmin@mydemoserver -p
```

## <a name="create-a-database"></a>Tworzenie bazy danych

Po nawiązaniu połączenia z serwerem utwórz pustą bazę danych.

```sql
mysql> CREATE DATABASE mysampledb;
```

W wierszu polecenia uruchom poniższe polecenie, aby przełączyć połączenie na tę nowo utworzoną bazę danych:

```sql
mysql> USE mysampledb;
```

## <a name="create-tables-in-the-database"></a>Tworzenie tabel w bazie danych

Teraz, gdy wiesz, jak nawiązać połączenie z bazą danych Azure Database for MariaDB, wykonaj niektóre podstawowe zadania.

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

Aby przywrócić serwer programu, należy użyć `Restore-AzMariaDbServer` polecenia cmdlet programu PowerShell.

### <a name="run-the-restore-command"></a>Uruchom polecenie Restore

Aby przywrócić serwer, uruchom poniższy przykład z programu PowerShell.

```azurepowershell-interactive
$restorePointInTime = (Get-Date).AddMinutes(-10)
Get-AzMariaDbServer -Name mydemoserver -ResourceGroupName myresourcegroup |
  Restore-AzMariaDbServer -Name mydemoserver-restored -ResourceGroupName myresourcegroup -RestorePointInTime $restorePointInTime -UsePointInTimeRestore
```

Po przywróceniu serwera do wcześniejszego punktu w czasie zostanie utworzony nowy serwer. Oryginalny serwer i jego bazy danych z określonego punktu w czasie są kopiowane na nowy serwer.

Wartości lokalizacji i warstwy cenowej dla przywróconego serwera pozostają takie same, jak oryginalny serwer.

Po zakończeniu procesu przywracania Znajdź nowy serwer i sprawdź, czy dane są przywracane zgodnie z oczekiwaniami. Nowy serwer ma taką samą nazwę logowania administratora serwera i hasło, które były prawidłowe dla istniejącego serwera w momencie uruchomienia przywracania. Hasło można zmienić na stronie **Przegląd** nowego serwera.

Nowy serwer utworzony podczas przywracania nie ma punktów końcowych usługi sieci wirtualnej, które istniały na oryginalnym serwerze. Te reguły należy osobno skonfigurować dla nowego serwera. Przywrócono reguły zapory z oryginalnego serwera.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Jak utworzyć kopię zapasową i przywrócić serwer Azure Database for MariaDB przy użyciu programu PowerShell](howto-restore-server-powershell.md)