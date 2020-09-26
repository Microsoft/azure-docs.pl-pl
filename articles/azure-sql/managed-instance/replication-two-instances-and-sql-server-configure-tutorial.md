---
title: Skonfiguruj replikację transakcyjną między wystąpieniem zarządzanym usługi Azure SQL i SQL Server
description: Samouczek, który konfiguruje replikację między wystąpieniem zarządzanym przez wydawcę, wystąpieniem zarządzanym przez dystrybutora i subskrybentem SQL Server na maszynie wirtualnej platformy Azure oraz niezbędnymi składnikami sieciowymi, takimi jak prywatna strefa DNS i Komunikacja równorzędna sieci VPN.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: security
ms.custom: sqldbrb=1
ms.topic: tutorial
author: MashaMSFT
ms.author: mathoma
ms.reviewer: sstein
ms.date: 11/21/2019
ms.openlocfilehash: 9d6592ccfb3ba5236a660d689d8b5d2cd1600c48
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91283194"
---
# <a name="tutorial-configure-transactional-replication-between-azure-sql-managed-instance-and-sql-server"></a>Samouczek: Konfigurowanie replikacji transakcyjnej między wystąpieniem zarządzanym usługi Azure SQL i SQL Server
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Replikacja transakcyjna umożliwia replikowanie danych z jednej bazy danych do innej hostowanej na SQL Server lub [wystąpieniu zarządzanym usługi Azure SQL](sql-managed-instance-paas-overview.md). Wystąpienie zarządzane SQL może być wydawcą, dystrybutorem lub subskrybentem w topologii replikacji. Zobacz [konfiguracje replikacji transakcyjnej](replication-transactional-overview.md#common-configurations) dla dostępnych konfiguracji. 

Replikacja transakcyjna jest obecnie dostępna w publicznej wersji zapoznawczej dla wystąpienia zarządzanego SQL. 

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
>
> - Skonfiguruj wystąpienie zarządzane jako wydawcę replikacji.
> - Skonfiguruj wystąpienie zarządzane jako dystrybutora replikacji.
> - Skonfiguruj SQL Server jako subskrybenta.

![Replikacja między wydawcą wystąpienia zarządzanego, dystrybutorem wystąpienia zarządzanego i subskrybentem SQL Server](./media/replication-two-instances-and-sql-server-configure-tutorial/sqlmi-to-sql-replication.png)

Ten samouczek jest przeznaczony dla doświadczonych odbiorców i zakłada, że użytkownik zna wdrażanie i nawiązywanie połączenia z zarówno wystąpieniami zarządzanymi, jak i SQL Server maszynami wirtualnymi na platformie Azure. 


> [!NOTE]
> W tym artykule opisano sposób korzystania z [replikacji transakcyjnej](https://docs.microsoft.com/sql/relational-databases/replication/transactional/transactional-replication) w wystąpieniu zarządzanym usługi Azure SQL. Nie jest on związany z [grupami trybu failover](https://docs.microsoft.com/azure/sql-database/sql-database-auto-failover-group), funkcja wystąpienia zarządzanego Azure SQL, która umożliwia tworzenie kompletnych możliwych do odczytu replik poszczególnych wystąpień. Podczas konfigurowania [replikacji transakcyjnej przy użyciu grup trybu failover](replication-transactional-overview.md#with-failover-groups)należy uwzględnić dodatkowe zagadnienia.

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten samouczek, upewnij się, że dysponujesz następującymi elementami:

- [Subskrypcja platformy Azure](https://azure.microsoft.com/free/).
- Środowisko pracy z wdrażaniem dwóch wystąpień zarządzanych w ramach tej samej sieci wirtualnej.
- Subskrybent SQL Server, lokalnie lub na maszynie wirtualnej platformy Azure. Ten samouczek używa maszyny wirtualnej platformy Azure.  
- [SQL Server Management Studio (SSMS) 18,0 lub nowszy](/sql/ssms/download-sql-server-management-studio-ssms).
- Najnowsza wersja [Azure PowerShell](/powershell/azure/install-az-ps?view=azps-1.7.0).
- Porty 445 i 1433 zezwalają na ruch SQL zarówno w zaporze platformy Azure, jak i w zaporze systemu Windows.

## <a name="1---create-the-resource-group"></a>1 — Tworzenie grupy zasobów

Aby utworzyć nową grupę zasobów, użyj następującego fragmentu kodu programu PowerShell:

```powershell-interactive
# set variables
$ResourceGroupName = "SQLMI-Repl"
$Location = "East US 2"

# Create a new resource group
New-AzResourceGroup -Name  $ResourceGroupName -Location $Location
```

## <a name="2---create-two-managed-instances"></a>2 — Tworzenie dwóch wystąpień zarządzanych

Utwórz dwa wystąpienia zarządzane w ramach tej nowej grupy zasobów przy użyciu [Azure Portal](https://portal.azure.com).

- Nazwa wystąpienia zarządzanego przez wydawcę powinna być `sql-mi-publisher` (wraz z kilkoma znakami w przypadku losowego), a nazwą sieci wirtualnej powinna być `vnet-sql-mi-publisher` .
- Nazwa wystąpienia zarządzanego dystrybutora powinna być `sql-mi-distributor` (wraz z kilkoma znakami w przypadku losowego) i powinna znajdować się _w tej samej sieci wirtualnej co wystąpienie zarządzane wydawcy_.

   ![Korzystanie z sieci wirtualnej wydawcy dla dystrybutora](./media/replication-two-instances-and-sql-server-configure-tutorial/use-same-vnet-for-distributor.png)

Aby uzyskać więcej informacji na temat tworzenia wystąpienia zarządzanego, zobacz [Tworzenie wystąpienia zarządzanego w portalu](instance-create-quickstart.md).

  > [!NOTE]
  > W celu uproszczenia i ponieważ jest to najbardziej powszechna konfiguracja, w tym samouczku sugeruje się umieszczenie wystąpienia zarządzanego dystrybutora w tej samej sieci wirtualnej co Wydawca. Można jednak utworzyć dystrybutora w oddzielnej sieci wirtualnej. W tym celu należy skonfigurować komunikację równorzędną sieci VPN między sieciami wirtualnymi wydawcy i dystrybutora, a następnie skonfigurować komunikację równorzędną sieci VPN między sieciami wirtualnymi dystrybutora i subskrybenta.

## <a name="3---create-a-sql-server-vm"></a>3 — Tworzenie maszyny wirtualnej SQL Server

Utwórz SQL Server maszynę wirtualną przy użyciu [Azure Portal](https://portal.azure.com). Maszyna wirtualna SQL Server powinna mieć następującą charakterystykę:

- Nazwa: `sql-vm-sub`
- Obraz: SQL Server 2016 lub większy
- Grupa zasobów: taka sama jak w przypadku wystąpienia zarządzanego
- Sieć wirtualna: `sql-vm-sub-vnet`

Aby uzyskać więcej informacji na temat wdrażania SQL Server maszyny wirtualnej na platformie Azure, zobacz [Szybki Start: Tworzenie maszyny wirtualnej SQL Server](../virtual-machines/windows/sql-vm-create-portal-quickstart.md).

## <a name="4---configure-vpn-peering"></a>4 — Konfigurowanie komunikacji równorzędnej sieci VPN

Skonfiguruj komunikację równorzędną sieci VPN w celu umożliwienia komunikacji między siecią wirtualną dwóch wystąpień zarządzanych i sieci wirtualnej SQL Server. Aby to zrobić, użyj tego fragmentu kodu programu PowerShell:

```powershell-interactive
# Set variables
$SubscriptionId = '<SubscriptionID>'
$resourceGroup = 'SQLMI-Repl'
$pubvNet = 'sql-mi-publisher-vnet'
$subvNet = 'sql-vm-sub-vnet'
$pubsubName = 'Pub-to-Sub-Peer'
$subpubName = 'Sub-to-Pub-Peer'

$virtualNetwork1 = Get-AzVirtualNetwork `
  -ResourceGroupName $resourceGroup `
  -Name $pubvNet

 $virtualNetwork2 = Get-AzVirtualNetwork `
  -ResourceGroupName $resourceGroup `
  -Name $subvNet  

# Configure VPN peering from publisher to subscriber
Add-AzVirtualNetworkPeering `
  -Name $pubsubName `
  -VirtualNetwork $virtualNetwork1 `
  -RemoteVirtualNetworkId $virtualNetwork2.Id

# Configure VPN peering from subscriber to publisher
Add-AzVirtualNetworkPeering `
  -Name $subpubName `
  -VirtualNetwork $virtualNetwork2 `
  -RemoteVirtualNetworkId $virtualNetwork1.Id

# Check status of peering on the publisher VNet; should say connected
Get-AzVirtualNetworkPeering `
 -ResourceGroupName $resourceGroup `
 -VirtualNetworkName $pubvNet `
 | Select PeeringState

# Check status of peering on the subscriber VNet; should say connected
Get-AzVirtualNetworkPeering `
 -ResourceGroupName $resourceGroup `
 -VirtualNetworkName $subvNet `
 | Select PeeringState

```

Po ustanowieniu komunikacji równorzędnej sieci VPN Przetestuj połączenie, uruchamiając SQL Server Management Studio (SSMS) na SQL Server i łącząc się z obydwoma wystąpieniami zarządzanymi. Aby uzyskać więcej informacji na temat nawiązywania połączenia z wystąpieniem zarządzanym za pomocą programu SSMS, zobacz temat [Używanie programu SSMS do łączenia z wystąpieniem zarządzanym SQL](point-to-site-p2s-configure.md#connect-with-ssms).

![Testowanie łączności z wystąpieniami zarządzanymi](./media/replication-two-instances-and-sql-server-configure-tutorial/test-connectivity-to-mi.png)

## <a name="5---create-a-private-dns-zone"></a>5 — Tworzenie prywatnej strefy DNS

Prywatna strefa DNS umożliwia routing DNS między wystąpieniami zarządzanymi i SQL Server.

### <a name="create-a-private-dns-zone"></a>Tworzenie prywatnej strefy DNS

1. Zaloguj się do [Azure Portal](https://portal.azure.com).
1. Wybierz pozycję **Utwórz zasób** , aby utworzyć nowy zasób platformy Azure.
1. Wyszukaj `private dns zone` w witrynie Azure Marketplace.
1. Wybierz zasób **strefy prywatna strefa DNS** Opublikowany przez firmę Microsoft, a następnie wybierz pozycję **Utwórz** , aby utworzyć strefę DNS.
1. Wybierz subskrypcję i grupę zasobów z listy rozwijanej.
1. Podaj arbitralną nazwę strefy DNS, na przykład `repldns.com` .

   ![Utwórz prywatną strefę DNS](./media/replication-two-instances-and-sql-server-configure-tutorial/create-private-dns-zone.png)

1. Wybierz pozycję **Przeglądanie + tworzenie**. Przejrzyj parametry prywatnej strefy DNS, a następnie wybierz pozycję **Utwórz** , aby utworzyć zasób.

### <a name="create-an-a-record"></a>Tworzenie rekordu A

1. Przejdź do nowej **strefy prywatna strefa DNS** i wybierz pozycję **Przegląd**.
1. Wybierz pozycję **+ zestaw rekordów** , aby utworzyć nowy rekord a.
1. Podaj nazwę maszyny wirtualnej SQL Server, a także prywatny wewnętrzny adres IP.

   ![Konfigurowanie rekordu A](./media/replication-two-instances-and-sql-server-configure-tutorial/configure-a-record.png)

1. Wybierz **przycisk OK** , aby utworzyć rekord A.

### <a name="link-the-virtual-network"></a>Łączenie sieci wirtualnej

1. Przejdź do nowej **strefy prywatna strefa DNS** i wybierz pozycję **linki sieci wirtualnej**.
1. Wybierz pozycję **+ Dodaj**.
1. Podaj nazwę łącza, na przykład `Pub-link` .
1. Wybierz subskrypcję z listy rozwijanej, a następnie wybierz sieć wirtualną dla wystąpienia zarządzanego wydawcy.
1. Zaznacz pole wyboru obok pozycji **Włącz autorejestrację**.

   ![Utwórz łącze sieci wirtualnej](./media/replication-two-instances-and-sql-server-configure-tutorial/configure-vnet-link.png)

1. Wybierz **przycisk OK** , aby połączyć sieć wirtualną.
1. Powtórz te kroki, aby dodać łącze dla sieci wirtualnej subskrybenta z nazwą, taką jak `Sub-link` .

## <a name="6---create-an-azure-storage-account"></a>6 — Tworzenie konta usługi Azure Storage

[Utwórz konto usługi Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account#create-a-storage-account) dla katalogu roboczego, a następnie Utwórz [udział plików](../../storage/files/storage-how-to-create-file-share.md) na koncie magazynu.

Skopiuj ścieżkę udziału plików w formacie: `\\storage-account-name.file.core.windows.net\file-share-name`

Przykład: `\\replstorage.file.core.windows.net\replshare`

Skopiuj parametry połączenia klucza dostępu do magazynu w formacie: `DefaultEndpointsProtocol=https;AccountName=<Storage-Account-Name>;AccountKey=****;EndpointSuffix=core.windows.net`

Przykład: `DefaultEndpointsProtocol=https;AccountName=replstorage;AccountKey=dYT5hHZVu9aTgIteGfpYE64cfis0mpKTmmc8+EP53GxuRg6TCwe5eTYWrQM4AmQSG5lb3OBskhg==;EndpointSuffix=core.windows.net`

Aby uzyskać więcej informacji, zobacz [Zarządzanie kluczami dostępu do konta magazynu](../../storage/common/storage-account-keys-manage.md).

## <a name="7---create-a-database"></a>7. Tworzenie bazy danych

Utwórz nową bazę danych w wystąpieniu zarządzanym wydawcy. Aby to zrobić, wykonaj następujące kroki:

1. Uruchom SQL Server Management Studio w SQL Server.
1. Nawiąż połączenie z `sql-mi-publisher` wystąpieniem zarządzanym.
1. Otwórz **nowe okno zapytania** i wykonaj następujące zapytanie T-SQL, aby utworzyć bazę danych.

```sql
-- Create the databases
USE [master]
GO

-- Drop database if it exists
IF EXISTS (SELECT * FROM sys.sysdatabases WHERE name = 'ReplTutorial')
BEGIN
    DROP DATABASE ReplTutorial
END
GO

-- Create new database
CREATE DATABASE [ReplTutorial]
GO

-- Create table
USE [ReplTutorial]
GO
CREATE TABLE ReplTest (
   ID INT NOT NULL PRIMARY KEY,
   c1 VARCHAR(100) NOT NULL,
   dt1 DATETIME NOT NULL DEFAULT getdate()
)
GO

-- Populate table with data
USE [ReplTutorial]
GO

INSERT INTO ReplTest (ID, c1) VALUES (6, 'pub')
INSERT INTO ReplTest (ID, c1) VALUES (2, 'pub')
INSERT INTO ReplTest (ID, c1) VALUES (3, 'pub')
INSERT INTO ReplTest (ID, c1) VALUES (4, 'pub')
INSERT INTO ReplTest (ID, c1) VALUES (5, 'pub')
GO
SELECT * FROM ReplTest
GO
```

## <a name="8---configure-distribution"></a>8 — Konfigurowanie dystrybucji

Po nawiązaniu połączenia z przykładową bazą danych można skonfigurować dystrybucję na `sql-mi-distributor` zarządzanym wystąpieniu. Aby to zrobić, wykonaj następujące kroki:

1. Uruchom SQL Server Management Studio w SQL Server.
1. Nawiąż połączenie z `sql-mi-distributor` wystąpieniem zarządzanym.
1. Otwórz **nowe okno zapytania** i uruchom następujący kod języka Transact-SQL w celu skonfigurowania dystrybucji na wystąpieniu zarządzanym dystrybutora:

   ```sql
   EXEC sp_adddistributor @distributor = 'sql-mi-distributor.b6bf57.database.windows.net', @password = '<distributor_admin_password>'
   
   EXEC sp_adddistributiondb @database = N'distribution'
   
   EXEC sp_adddistpublisher @publisher = 'sql-mi-publisher.b6bf57.database.windows.net', -- primary publisher
        @distribution_db = N'distribution',
        @security_mode = 0,
        @login = N'azureuser',
        @password = N'<publisher_password>',
        @working_directory = N'\\replstorage.file.core.windows.net\replshare',
        @storage_connection_string = N'<storage_connection_string>'
        -- example: @storage_connection_string = N'DefaultEndpointsProtocol=https;AccountName=replstorage;AccountKey=dYT5hHZVu9aTgIteGfpYE64cfis0mpKTmmc8+EP53GxuRg6TCwe5eTYWrQM4AmQSG5lb3OBskhg==;EndpointSuffix=core.windows.net'

   ```

   > [!NOTE]
   > Upewnij się, że dla parametru są używane tylko ukośniki odwrotne ( `\` ) @working_directory . Użycie ukośnika ( `/` ) może spowodować błąd podczas nawiązywania połączenia z udziałem plików.

1. Nawiąż połączenie z `sql-mi-publisher` wystąpieniem zarządzanym.
1. Otwórz **nowe okno zapytania** i uruchom następujący kod języka Transact-SQL w celu zarejestrowania dystrybutora na wydawcy:

   ```sql
   Use MASTER
   EXEC sys.sp_adddistributor @distributor = 'sql-mi-distributor.b6bf57.database.windows.net', @password = '<distributor_admin_password>'
   ```

## <a name="9---create-the-publication"></a>9 — Tworzenie publikacji

Po skonfigurowaniu dystrybucji możesz teraz utworzyć publikację. Aby to zrobić, wykonaj następujące kroki:

1. Uruchom SQL Server Management Studio w SQL Server.
1. Nawiąż połączenie z `sql-mi-publisher` wystąpieniem zarządzanym.
1. W **Eksplorator obiektów**rozwiń węzeł **replikacja** , a następnie kliknij prawym przyciskiem myszy folder **publikacja lokalna** . Wybierz **nową publikację.**..
1. Wybierz pozycję **dalej** , aby przejść poza stronę powitalną.
1. Na stronie **baza danych publikacji** wybierz `ReplTutorial` utworzoną wcześniej bazę danych. Wybierz opcję **Dalej**.
1. Na stronie **Typ publikacji** wybierz pozycję **publikacja transakcyjna**. Wybierz opcję **Dalej**.
1. Na stronie **artykuły** zaznacz pole wyboru obok pozycji **tabele**. Wybierz opcję **Dalej**.
1. Na stronie **Filtruj wiersze tabeli** wybierz pozycję **dalej** bez dodawania filtrów.
1. Na stronie **Agent migawek** zaznacz pole wyboru obok pozycji **Utwórz migawkę natychmiast i Zachowaj dostępność migawki w celu zainicjowania subskrypcji**. Wybierz opcję **Dalej**.
1. Na stronie **zabezpieczenia agenta** wybierz pozycję **Ustawienia zabezpieczeń..**.. Podaj poświadczenia logowania SQL Server, które mają być używane dla agenta migawek, i Połącz się z wydawcą. Wybierz **przycisk OK** , aby zamknąć stronę **zabezpieczenia agenta migawek** . Wybierz opcję **Dalej**.

   ![Konfiguruj zabezpieczenia agenta migawek](./media/replication-two-instances-and-sql-server-configure-tutorial/snapshot-agent-security.png)

1. Na stronie **Akcje kreatora** wybierz opcję **utworzenia publikacji** i (opcjonalnie) wybierz opcję **wygenerowania pliku skryptu z krokami utworzenia publikacji** , jeśli chcesz zapisać ten skrypt w późniejszym czasie.
1. Na stronie **Kończenie pracy Kreatora** Nadaj nazwę publikacji `ReplTest` i wybierz pozycję **dalej** , aby utworzyć publikację.
1. Po utworzeniu publikacji Odśwież węzeł **replikacja** w **Eksplorator obiektów** i rozwiń pozycję **Publikacje lokalne** , aby wyświetlić nową publikację.

## <a name="10---create-the-subscription"></a>10 — Tworzenie subskrypcji

Po utworzeniu publikacji można utworzyć subskrypcję. Aby to zrobić, wykonaj następujące kroki:

1. Uruchom SQL Server Management Studio w SQL Server.
1. Nawiąż połączenie z `sql-mi-publisher` wystąpieniem zarządzanym.
1. Otwórz **nowe okno zapytania** i uruchom następujący kod języka Transact-SQL, aby dodać subskrypcję i agenta dystrybucji. Użyj usługi DNS jako części nazwy subskrybenta.

```sql
use [ReplTutorial]
exec sp_addsubscription
@publication = N'ReplTest',
@subscriber = N'sql-vm-sub.repldns.com', -- include the DNS configured in the private DNS zone
@destination_db = N'ReplSub',
@subscription_type = N'Push',
@sync_type = N'automatic',
@article = N'all',
@update_mode = N'read only',
@subscriber_type = 0

exec sp_addpushsubscription_agent
@publication = N'ReplTest',
@subscriber = N'sql-vm-sub.repldns.com', -- include the DNS configured in the private DNS zone
@subscriber_db = N'ReplSub',
@job_login = N'azureuser',
@job_password = '<Complex Password>',
@subscriber_security_mode = 0,
@subscriber_login = N'azureuser',
@subscriber_password = '<Complex Password>',
@dts_package_location = N'Distributor'
GO
```

## <a name="11---test-replication"></a>replikacja 11-testowa

Po skonfigurowaniu replikacji można testować ją, wstawiając nowe elementy na wydawcy i obserwując zmiany propagowane do subskrybenta.

Uruchom Poniższy fragment kodu T-SQL, aby wyświetlić wiersze na subskrybencie:

```sql
Use ReplSub
select * from dbo.ReplTest
```

Uruchom Poniższy fragment kodu T-SQL, aby wstawić dodatkowe wiersze na wydawcy, a następnie ponownie sprawdzić wiersze na subskrybencie.

```sql
Use ReplTutorial
INSERT INTO ReplTest (ID, c1) VALUES (15, 'pub')
```

## <a name="clean-up-resources"></a>Czyszczenie zasobów

1. Przejdź do grupy zasobów w [Azure Portal](https://portal.azure.com).
1. Wybierz wystąpienia zarządzane, a następnie wybierz pozycję **Usuń**. Wpisz `yes` w polu tekstowym, aby potwierdzić, że chcesz usunąć zasób, a następnie wybierz pozycję **Usuń**. Ten proces może potrwać trochę czasu w tle, a do momentu ukończenia nie będzie można usunąć *klastra wirtualnego* ani żadnych innych zasobów zależnych. Monitoruj pozycję Usuń na karcie **działanie** , aby potwierdzić, że zarządzane wystąpienie zostało usunięte.
1. Po usunięciu wystąpienia zarządzanego Usuń *klaster wirtualny* , wybierając go w grupie zasobów, a następnie wybierając pozycję **Usuń**. Wpisz `yes` w polu tekstowym, aby potwierdzić, że chcesz usunąć zasób, a następnie wybierz pozycję **Usuń**.
1. Usuń wszystkie pozostałe zasoby. Wpisz `yes` w polu tekstowym, aby potwierdzić, że chcesz usunąć zasób, a następnie wybierz pozycję **Usuń**.
1. Aby usunąć grupę zasobów, wybierz pozycję **Usuń grupę zasobów**, wpisz nazwę grupy zasobów, `myResourceGroup` a następnie wybierz pozycję **Usuń**.

## <a name="known-errors"></a>Znane błędy

### <a name="windows-logins-are-not-supported"></a>Nazwy logowania systemu Windows nie są obsługiwane

`Exception Message: Windows logins are not supported in this version of SQL Server.`

Agent został skonfigurowany przy użyciu logowania systemu Windows i w zamian musi używać logowania SQL Server. Na stronie **zabezpieczenia agenta** **Właściwości publikacji** można zmienić poświadczenia logowania na nazwę logowania SQL Server.

### <a name="failed-to-connect-to-azure-storage"></a>Nie można nawiązań połączenia z usługą Azure Storage

`Connecting to Azure Files Storage '\\replstorage.file.core.windows.net\replshare' Failed to connect to Azure Storage '' with OS error: 53.`

2019-11-19 02:21:05.07 uzyskano parametry połączenia usługi Azure Storage dla replstorage 2019-11-19 02:21:05.07 łączenie z usługą Azure Files Storage " \\ replstorage. plik. Core. Windows. net\replshare" 2019-11-19 02:21:31.21 nie można nawiązać połączenia z usługą Azure Storage "" z powodu błędu systemu operacyjnego: 53.

Jest to możliwe, ponieważ port 445 jest zamknięty w zaporze platformy Azure, w zaporze systemu Windows lub w obu tych przypadkach.

`Connecting to Azure Files Storage '\\replstorage.file.core.windows.net\replshare' Failed to connect to Azure Storage '' with OS error: 55.`

Użycie ukośnika zamiast ukośnika odwrotnego w ścieżce pliku dla udziału plików może spowodować wystąpienie tego błędu.
  
  - Jest to dobry sposób: `\\replstorage.file.core.windows.net\replshare`
  - Może to spowodować błąd systemu operacyjnego 55: `'\\replstorage.file.core.windows.net/replshare'`

### <a name="could-not-connect-to-subscriber"></a>Nie można nawiązać połączenia z subskrybentem

`The process could not connect to Subscriber 'SQL-VM-SUB`
`Could not open a connection to SQL Server [53].`
`A network-related or instance-specific error has occurred while establishing a connection to SQL Server. Server is not found or not accessible. Check if instance name is correct and if SQL Server is configured to allow remote connections.`

Możliwe rozwiązania:

- Upewnij się, że port 1433 jest otwarty.
- Upewnij się, że na subskrybencie jest włączony protokół TCP/IP.
- Potwierdź, że nazwa DNS została użyta podczas tworzenia subskrybenta.
- Sprawdź, czy sieci wirtualne są prawidłowo połączone w prywatnej strefie DNS.
- Sprawdź, czy rekord został prawidłowo skonfigurowany.
- Sprawdź, czy Komunikacja równorzędna sieci VPN została prawidłowo skonfigurowana.

### <a name="no-publications-to-which-you-can-subscribe"></a>Brak publikacji, do których można subskrybować

Gdy dodajesz nową subskrypcję za pomocą kreatora **nowej subskrypcji** , na stronie **publikacji** może się okazać, że nie ma żadnych baz danych i publikacji wymienionych jako dostępne opcje i może zostać wyświetlony następujący komunikat o błędzie:

`There are no publications to which you can subscribe, either because this server has no publications or because you do not have sufficient privileges to access the publications.`

Chociaż jest możliwe, że ten komunikat o błędzie jest dokładny i naprawdę nie są dostępne publikacje na wydawcy, z którym nawiązano połączenie lub nie masz wystarczających uprawnień, ten błąd może być również spowodowany przez starszą wersję SQL Server Management Studio. Spróbuj przeprowadzić uaktualnienie do wersji SQL Server Management Studio 18,0 lub nowszej, aby wyrównać ją jako główną przyczynę.

## <a name="next-steps"></a>Następne kroki

### <a name="enable-security-features"></a>Włączanie funkcji zabezpieczeń.

Zapoznaj się z artykułem [co to jest wystąpienie zarządzane usługi Azure SQL?](sql-managed-instance-paas-overview.md#advanced-security-and-compliance) , aby zapoznać się z kompleksową listą sposobów zabezpieczania bazy danych. Omówione są następujące funkcje zabezpieczeń:

- [Inspekcja wystąpienia zarządzanego SQL](auditing-configure.md)
- [Zawsze szyfrowane](/sql/relational-databases/security/encryption/always-encrypted-database-engine)
- [Wykrywanie zagrożeń](threat-detection-configure.md)
- [Dynamiczne maskowanie danych](/sql/relational-databases/security/dynamic-data-masking)
- [Zabezpieczenia na poziomie wiersza](/sql/relational-databases/security/row-level-security)
- [Przezroczyste szyfrowanie danych (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)

### <a name="sql-managed-instance-capabilities"></a>Możliwości wystąpienia zarządzanego SQL

Aby zapoznać się z pełnym omówieniem możliwości wystąpienia zarządzanego, zobacz:

> [!div class="nextstepaction"]
> [Możliwości wystąpienia zarządzanego SQL](sql-managed-instance-paas-overview.md)
