---
title: 'Szybki start: tworzenie dedykowanej puli SQL (dawniej SQL DW) przy użyciu Azure PowerShell'
description: Szybko utwórz dedykowaną pulę SQL (dawniej SQL DW) z regułą zapory na poziomie serwera przy użyciu Azure PowerShell.
services: synapse-analytics
author: XiaoyuMSFT
ms.author: xiaoyul
manager: craigg
ms.reviewer: igorstan
ms.date: 4/11/2019
ms.topic: quickstart
ms.service: synapse-analytics
ms.subservice: sql-dw
ms.custom:
- seo-lt-2019
- azure-synapse
- devx-track-azurepowershell
- mode-api
ms.openlocfilehash: 65bf509c8eb654a9f7712fdf7b94ff7fa26a3d32
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107537453"
---
# <a name="quickstart-create-a-dedicated-sql-pool-formerly-sql-dw-with-azure-powershell"></a>Szybki start: tworzenie dedykowanej puli SQL (dawniej SQL DW) przy użyciu Azure PowerShell

Utwórz dedykowaną pulę SQL (dawniej SQL DW) w Azure Synapse Analytics przy użyciu Azure PowerShell.

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

> [!IMPORTANT]
> Utworzenie dedykowanej puli SQL (dawniej SQL DW) może spowodować utworzenie nowej rozliczanej usługi.  Aby uzyskać więcej informacji, [zobacz Azure Synapse Analytics cennika.](https://azure.microsoft.com/pricing/details/sql-data-warehouse/)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do subskrypcji platformy Azure przy użyciu polecenia [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) i postępuj zgodnie z instrukcjami na ekranie.

```powershell
Connect-AzAccount
```

Aby zobaczyć, której subskrypcji używasz, uruchom skrypt [Get-AzSubscription.](/powershell/module/az.accounts/get-azsubscription?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)

```powershell
Get-AzSubscription
```

Jeśli musisz użyć innej subskrypcji niż domyślna, uruchom [set-AzContext](/powershell/module/az.accounts/set-azcontext?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

```powershell
Set-AzContext -SubscriptionName "MySubscription"
```

## <a name="create-variables"></a>Tworzenie zmiennych

Zdefiniuj zmienne do wykorzystania w skryptach w tym przewodniku Szybki start.

```powershell
# The data center and resource name for your resources
$resourcegroupname = "myResourceGroup"
$location = "WestEurope"
# The server name: Use a random value or replace with your own value (don't capitalize)
$servername = "server-$(Get-Random)"
# Set an admin name and password for your database
# The sign-in information for the server
$adminlogin = "ServerAdmin"
$password = "ChangeYourAdminPassword1"
# The ip address range that you want to allow to access your server - change as appropriate
$startip = "0.0.0.0"
$endip = "0.0.0.0"
# The database name
$databasename = "mySampleDataWarehouse"
```

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Utwórz [grupę zasobów platformy Azure](../../azure-resource-manager/management/overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) za pomocą [polecenia New-AzResourceGroup.](/powershell/module/az.resources/new-azresourcegroup?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) Grupa zasobów to kontener, w którym zasoby platformy Azure są wdrażane i zarządzane jako grupa. Poniższy przykład obejmuje tworzenie grupy zasobów o nazwie `myResourceGroup` w lokalizacji `westeurope`.

```powershell
New-AzResourceGroup -Name $resourcegroupname -Location $location
```

## <a name="create-a-server"></a>Tworzenie serwera

Utwórz serwer [logiczny SQL przy](../../azure-sql/database/logical-servers.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) użyciu [polecenia New-AzSqlServer.](/powershell/module/az.sql/new-azsqlserver?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) Serwer zawiera grupę baz danych zarządzanych jako grupa. Poniższy przykład tworzy losowo nazwany serwer w grupie zasobów z administratorem o nazwie i `ServerAdmin` hasłem `ChangeYourAdminPassword1` . Zastąp te wstępnie zdefiniowane wartości zgodnie z potrzebami.

```powershell
New-AzSqlServer -ResourceGroupName $resourcegroupname `
    -ServerName $servername `
    -Location $location `
    -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
```

## <a name="configure-a-server-level-firewall-rule"></a>Konfigurowanie reguły zapory na poziomie serwera

Utwórz [regułę zapory na poziomie serwera](../../azure-sql/database/firewall-configure.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) za pomocą polecenia [New-AzSqlServerFirewallRule.](/powershell/module/az.sql/new-azsqlserverfirewallrule?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) Reguła zapory na poziomie serwera umożliwia aplikacji zewnętrznej, takiej jak SQL Server Management Studio lub narzędzie SQLCMD, łączenie się z dedykowaną pulą SQL (dawniej SQL DW) za pośrednictwem dedykowanej zapory usługi puli SQL.

W poniższym przykładzie zapora jest otwarta tylko dla innych zasobów platformy Azure. Aby włączyć łączność zewnętrzną, zmień adres IP na adres odpowiedni dla danego środowiska. Aby otworzyć wszystkie adresy IP, użyj wartości 0.0.0.0 jako początkowego adresu IP i wartości 255.255.255.255 jako adresu końcowego.

```powershell
New-AzSqlServerFirewallRule -ResourceGroupName $resourcegroupname `
    -ServerName $servername `
    -FirewallRuleName "AllowSome" -StartIpAddress $startip -EndIpAddress $endip
```

> [!NOTE]
> Punkty końcowe SQL komunikują się za pośrednictwem portu 1433. Jeśli próbujesz nawiązać połączenie z sieci firmowej, ruch wychodzący na porcie 1433 może być zablokowany przez zaporę sieciową. Jeśli tak, nie będzie można nawiązać połączenia z serwerem, chyba że dział IT otworzy port 1433.
>

## <a name="create-a-dedicated-sql-pool-formerly-sql-dw"></a>Tworzenie dedykowanej puli SQL (dawniej SQL DW)

Poniższy przykład tworzy dedykowaną pulę SQL (dawniej SQL DW) przy użyciu wcześniej zdefiniowanych zmiennych.  Określa cel usługi DW100c, czyli tańszy punkt początkowy dla dedykowanej puli SQL (dawniej SQL DW).

```Powershell
New-AzSqlDatabase `
    -ResourceGroupName $resourcegroupname `
    -ServerName $servername `
    -DatabaseName $databasename `
    -Edition "DataWarehouse" `
    -RequestedServiceObjectiveName "DW100c" `
    -CollationName "SQL_Latin1_General_CP1_CI_AS" `
    -MaxSizeBytes 10995116277760
```

Wymagane parametry:

* **RequestedServiceObjectiveName:** żądana [](what-is-a-data-warehouse-unit-dwu-cdwu.md) liczba jednostek magazynu danych. Zwiększenie tej kwoty zwiększa koszt obliczeń. Aby uzyskać listę obsługiwanych wartości, zobacz Limity pamięci i [współbieżności](memory-concurrency-limits.md).
* **DatabaseName:** nazwa dedykowanej puli SQL (dawniej SQL DW), która jest tworzymy.
* **ServerName:** nazwa serwera, który jest używaną do tworzenia.
* **ResourceGroupName:** grupa zasobów, z których korzystasz. Aby znaleźć grupy zasobów dostępne w ramach subskrypcji, użyj polecenia cmdlet Get-AzureResource.
* **Wersja:** musi być "DataWarehouse", aby utworzyć dedykowaną pulę SQL (wcześniej SQL DW).

Opcjonalne parametry:

* **CollationName**: sortowanie domyślne, gdy sortowanie nie jest określone, to COLLATE SQL_Latin1_General_CP1_CI_AS. Nie można zmienić sortowanie w bazie danych.
* **MaxSizeBytes:** domyślny maksymalny rozmiar bazy danych to 240 TB. Maksymalny rozmiar ogranicza dane magazynu wierszy. Istnieje nieograniczony magazyn dla danych kolumnowych.

Aby uzyskać więcej informacji na temat opcji parametrów, [zobacz New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Inne samouczki Szybki start w tej kolekcji bazują na tym przewodniku Szybki start.

> [!TIP]
> Jeśli planujesz kontynuować pracę z późniejszymi samouczkami Szybki start, nie czyść zasobów utworzonych w tym przewodniku Szybki start. Jeśli nie planujesz kontynuować pracy, skorzystaj z poniższych kroków, aby usunąć wszystkie zasoby utworzone w tym przewodniku Szybki start w Azure Portal.
>

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourcegroupname
```

## <a name="next-steps"></a>Następne kroki

Utworzono dedykowaną pulę SQL (wcześniej SQL DW), utworzono regułę zapory i nałączono do dedykowanej puli SQL. Aby dowiedzieć się więcej, przejdź do [artykułu Load data into a dedicated SQL pool (Ładowanie danych do dedykowanej puli SQL).](./load-data-from-azure-blob-storage-using-copy.md)
