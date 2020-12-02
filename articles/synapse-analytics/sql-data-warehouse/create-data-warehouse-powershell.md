---
title: 'Szybki Start: Tworzenie dedykowanej puli SQL (dawniej SQL DW) przy użyciu Azure PowerShell'
description: Szybko Utwórz dedykowaną pulę SQL (wcześniej SQL DW) z regułą zapory na poziomie serwera przy użyciu Azure PowerShell.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql-dw
ms.date: 4/11/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse    , devx-track-azurepowershell
ms.openlocfilehash: 0ce94b62d67048896cdf7355043ec2dde7f2df79
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96456580"
---
# <a name="quickstart-create-a-dedicated-sql-pool-formerly-sql-dw-with-azure-powershell"></a>Szybki Start: Tworzenie dedykowanej puli SQL (dawniej SQL DW) przy użyciu Azure PowerShell

Tworzenie dedykowanej puli SQL (dawniej SQL DW) w usłudze Azure Synapse Analytics przy użyciu Azure PowerShell.

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

> [!IMPORTANT]
> Utworzenie dedykowanej puli SQL (dawniej SQL DW) może spowodować powstanie nowej usługi do obciążania.  Aby uzyskać więcej informacji, zobacz [Cennik usługi Azure Synapse Analytics](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do subskrypcji platformy Azure za pomocą polecenia [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) i postępuj zgodnie z instrukcjami wyświetlanymi na ekranie.

```powershell
Connect-AzAccount
```

Aby sprawdzić, która subskrypcja jest używana, uruchom polecenie [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

```powershell
Get-AzSubscription
```

Jeśli musisz użyć innej subskrypcji niż domyślna, uruchom polecenie [Set-AzContext](/powershell/module/az.accounts/set-azcontext?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

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

Utwórz [grupę zasobów platformy Azure](../../azure-resource-manager/management/overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) za pomocą polecenia [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) . Grupa zasobów to kontener, w którym zasoby platformy Azure są wdrażane i zarządzane jako Grupa. Poniższy przykład obejmuje tworzenie grupy zasobów o nazwie `myResourceGroup` w lokalizacji `westeurope`.

```powershell
New-AzResourceGroup -Name $resourcegroupname -Location $location
```

## <a name="create-a-server"></a>Tworzenie serwera

Utwórz [logiczny serwer SQL](../../azure-sql/database/logical-servers.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) przy użyciu polecenia [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) . Serwer zawiera grupę baz danych zarządzanych jako grupa. Poniższy przykład tworzy losowo nazwany serwer w grupie zasobów przy użyciu administratora o nazwie `ServerAdmin` i hasła `ChangeYourAdminPassword1` . Zastąp te wstępnie zdefiniowane wartości zgodnie z potrzebami.

```powershell
New-AzSqlServer -ResourceGroupName $resourcegroupname `
    -ServerName $servername `
    -Location $location `
    -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
```

## <a name="configure-a-server-level-firewall-rule"></a>Konfigurowanie reguły zapory na poziomie serwera

Utwórz [regułę zapory na poziomie serwera](../../azure-sql/database/firewall-configure.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) za pomocą polecenia [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) . Reguła zapory na poziomie serwera pozwala aplikacji zewnętrznej, takiej jak SQL Server Management Studio lub Narzędzie SQLCMD, łączyć się z dedykowaną pulą SQL (dawniej SQL DW) za pomocą dedykowanej zapory usługi puli SQL.

W poniższym przykładzie zapora jest otwarta tylko dla innych zasobów platformy Azure. Aby włączyć łączność zewnętrzną, zmień adres IP na adres odpowiedni dla danego środowiska. Aby otworzyć wszystkie adresy IP, użyj wartości 0.0.0.0 jako początkowego adresu IP i wartości 255.255.255.255 jako adresu końcowego.

```powershell
New-AzSqlServerFirewallRule -ResourceGroupName $resourcegroupname `
    -ServerName $servername `
    -FirewallRuleName "AllowSome" -StartIpAddress $startip -EndIpAddress $endip
```

> [!NOTE]
> Punkty końcowe SQL komunikują się za pośrednictwem portu 1433. Jeśli próbujesz nawiązać połączenie z sieci firmowej, ruch wychodzący na porcie 1433 może być zablokowany przez zaporę sieciową. W takim przypadku nie będzie można nawiązać połączenia z serwerem, chyba że dział IT otworzy port 1433.
>

## <a name="create-a-dedicated-sql-pool-formerly-sql-dw"></a>Tworzenie dedykowanej puli SQL (dawniej SQL DW)

Poniższy przykład tworzy dedykowaną pulę SQL (dawniej SQL DW) przy użyciu wcześniej zdefiniowanych zmiennych.  Określa cel usługi jako DW100c, który jest tańszym punktem wyjścia dla dedykowanej puli SQL (dawniej SQL DW).

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

* **RequestedServiceObjectiveName**: ilość żądanych [jednostek magazynu danych](what-is-a-data-warehouse-unit-dwu-cdwu.md) . Zwiększenie tej kwoty zwiększa koszt obliczeń. Aby uzyskać listę obsługiwanych wartości, zobacz [limity pamięci i współbieżności](memory-concurrency-limits.md).
* **DatabaseName**: Nazwa dedykowanej puli SQL (dawniej SQL DW), która jest tworzona.
* **Servername**: Nazwa serwera, który jest używany do tworzenia.
* **ResourceGroupName**: Grupa zasobów, której używasz. Aby znaleźć grupy zasobów dostępne w ramach subskrypcji, użyj polecenia cmdlet Get-AzureResource.
* **Edition**: musi mieć wartość "DataWarehouse", aby utworzyć dedykowaną pulę SQL (dawniej SQL DW).

Opcjonalne parametry:

* **CollationName**: sortowanie domyślne, gdy sortowanie nie jest określone, to COLLATE SQL_Latin1_General_CP1_CI_AS. Nie można zmienić sortowania w bazie danych.
* **MaxSizeBytes**: domyślny maksymalny rozmiar bazy danych to 240TB. Maksymalny rozmiar ogranicza dane magazynu wierszy. Istnieje nieograniczony magazyn dla danych kolumnowych.

Aby uzyskać więcej informacji na temat opcji parametrów, zobacz polecenie [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Inne samouczki Szybki start w tej kolekcji bazują na tym przewodniku Szybki start.

> [!TIP]
> Jeśli planujesz kontynuować pracę z nowszymi samouczkami Szybki Start, nie czyść zasobów utworzonych w tym przewodniku Szybki Start. Jeśli nie planujesz kontynuować pracy, wykonaj następujące kroki, aby usunąć wszystkie zasoby utworzone w ramach tego przewodnika Szybki Start w Azure Portal.
>

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourcegroupname
```

## <a name="next-steps"></a>Następne kroki

Utworzono dedykowaną pulę SQL (dawniej SQL DW), utworzono regułę zapory i połączono ją z dedykowaną pulą SQL. Aby dowiedzieć się więcej, przejdź do artykułu [ładowanie danych do dedykowanej puli SQL](load-data-from-azure-blob-storage-using-polybase.md) .
