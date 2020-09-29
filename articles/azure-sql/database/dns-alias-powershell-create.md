---
title: Alias DNS (PowerShell & interfejs wiersza polecenia platformy Azure)
description: Za pomocą poleceń cmdlet programu PowerShell i interfejsu wiersza polecenia platformy Azure można przekierować nowe połączenia klientów do innego programu SQL Server na platformie Azure bez konieczności dotykania konfiguracji klienta.
keywords: Baza danych SQL DNS
ms.custom: seo-lt-2019 sqldbrb=1
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.devlang: PowerShell
ms.topic: how-to
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: genemi, amagarwa, maboja, jrasnick, vanto
ms.date: 05/14/2019
ms.openlocfilehash: c1006cce4125adf67b9e3009bf8e27c0123acfd1
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/29/2020
ms.locfileid: "91443634"
---
# <a name="powershell-for-dns-alias-to-azure-sql-database"></a>Program PowerShell dla aliasu DNS do Azure SQL Database
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

Ten artykuł zawiera skrypt programu PowerShell, który demonstruje sposób zarządzania aliasem DNS dla [programu SQL Server](logical-servers.md) , na którym znajduje się Azure SQL Database.

> [!NOTE]
> Ten artykuł został zaktualizowany tak, aby korzystał z Azure PowerShell AZ module lub interfejsu wiersza polecenia platformy Azure. Nadal możesz używać modułu AzureRM, który będzie nadal otrzymywać poprawki błędów do co najmniej grudnia 2020 r.
>
> Aby dowiedzieć się więcej na temat zgodności AZ module i AzureRM, zobacz [wprowadzenie do Azure PowerShell AZ module](/powershell/azure/new-azureps-module-az). Aby uzyskać instrukcje dotyczące instalacji, zobacz [instalowanie Azure PowerShell](/powershell/azure/install-az-ps) lub [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

## <a name="dns-alias-in-connection-string"></a>Alias DNS w parametrach połączenia

Aby połączyć [logiczny serwer SQL](logical-servers.md), klient, taki jak SQL Server Management Studio (SSMS), może podać nazwę aliasu DNS zamiast prawdziwej nazwy serwera. W poniższym przykładowym ciągu serwera alias any- *Unique-alias-Name* zastępuje pierwszy węzeł rozdzielony kropką w ciągu serwera z czterema węzłami:

   `<yourServer>.database.windows.net`

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli chcesz uruchomić pokazowy skrypt programu PowerShell przedstawiony w tym artykule, obowiązują następujące wymagania wstępne:

- Subskrypcja i konto platformy Azure dla bezpłatnej wersji próbnej zobacz [Azure — wersje próbne](https://azure.microsoft.com/free/)
- Dwa serwery

## <a name="example"></a>Przykład

Poniższy przykład kodu jest uruchamiany przez przypisanie wartości literału do kilku zmiennych.

Aby uruchomić kod, Edytuj wartości zastępcze w celu dopasowania wartości rzeczywistych w systemie.

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Używane polecenia cmdlet są następujące:

- [New-AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/New-azSqlServerDnsAlias): tworzy alias DNS w systemie usługi Azure SQL Database. Alias odwołuje się do serwera 1.
- [Get-AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/Get-azSqlServerDnsAlias): Pobierz i Wyświetl listę wszystkich aliasów przypisanych do serwera 1.
- [Set-AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/Set-azSqlServerDnsAlias): modyfikuje nazwę serwera, do którego odwołuje się alias, z serwera 1 na serwer 2.
- [Remove-AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/Remove-azSqlServerDnsAlias): usuwanie aliasu z serwera 2 przy użyciu nazwy aliasu.

Aby przeprowadzić instalację lub uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-az-ps).

`Get-Module -ListAvailable Az`Aby znaleźć wersję, użyj polecenia w programie *PowerShell \_ise.exe*.

```powershell
$subscriptionName = '<subscriptionName>';
$sqlServerDnsAliasName = '<aliasName>';
$resourceGroupName = '<resourceGroupName>';  
$sqlServerName = '<sqlServerName>';
$resourceGroupName2 = '<resourceGroupNameTwo>'; # can be same or different than $resourceGroupName
$sqlServerName2 = '<sqlServerNameTwo>'; # must be different from $sqlServerName.

# login to Azure
Connect-AzAccount -SubscriptionName $subscriptionName;
$subscriptionId = Get-AzSubscription -SubscriptionName $subscriptionName;

Write-Host 'Assign an alias to server 1...';
New-AzSqlServerDnsAlias –ResourceGroupName $resourceGroupName -ServerName $sqlServerName `
    -Name $sqlServerDnsAliasName;

Write-Host 'Get the aliases assigned to server 1...';
Get-AzSqlServerDnsAlias –ResourceGroupName $resourceGroupName -ServerName $sqlServerName;

Write-Host 'Move the alias from server 1 to server 2...';
Set-AzSqlServerDnsAlias –ResourceGroupName $resourceGroupName2 -TargetServerName $sqlServerName2 `
    -Name $sqlServerDnsAliasName `
    -SourceServerResourceGroup $resourceGroupName -SourceServerName $sqlServerName `
    -SourceServerSubscriptionId $subscriptionId.Id;

Write-Host 'Get the aliases assigned to server 2...';
Get-AzSqlServerDnsAlias –ResourceGroupName $resourceGroupName2 -ServerName $sqlServerName2;

Write-Host 'Remove the alias from server 2...';
Remove-AzSqlServerDnsAlias –ResourceGroupName $resourceGroupName2 -ServerName $sqlServerName2 `
    -Name $sqlServerDnsAliasName;
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Używane polecenia są następujące:

- [AZ SQL Server DNS-alias Create](https://docs.microsoft.com/powershell/module/az.Sql/New-azSqlServerDnsAlias): tworzy alias DNS dla serwera. Alias odwołuje się do serwera 1.
- [AZ SQL Server DNS-alias show](https://docs.microsoft.com/powershell/module/az.Sql/Get-azSqlServerDnsAlias): pobieranie i wyświetlanie listy wszystkich aliasów przypisanych do serwera 1.
- [AZ SQL Server DNS-alias Set](https://docs.microsoft.com/powershell/module/az.Sql/Set-azSqlServerDnsAlias): modyfikuje nazwę serwera, do którego odwołuje się alias, z serwera 1 na serwer 2.
- [AZ SQL Server DNS-alias DELETE](https://docs.microsoft.com/powershell/module/az.Sql/Remove-azSqlServerDnsAlias): usuwanie aliasu z serwera 2 przy użyciu nazwy aliasu.

Aby uzyskać informacje o instalowaniu lub uaktualnianiu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

```azurecli-interactive
$subscriptionName = '<subscriptionName>';
$sqlServerDnsAliasName = '<aliasName>';
$resourceGroupName = '<resourceGroupName>';  
$sqlServerName = '<sqlServerName>';
$resourceGroupName2 = '<resourceGroupNameTwo>'; # can be same or different than $resourceGroupName
$sqlServerName2 = '<sqlServerNameTwo>'; # must be different from $sqlServerName.

# login to Azure
az login -SubscriptionName $subscriptionName;
$subscriptionId = az account list[0].i -SubscriptionName $subscriptionName;

Write-Host 'Assign an alias to server 1...';
az sql server dns-alias create –-resource-group $resourceGroupName --server $sqlServerName `
    --name $sqlServerDnsAliasName;

Write-Host 'Get the aliases assigned to server 1...';
az sql server dns-alias show –-resource-group $resourceGroupName --server $sqlServerName;

Write-Host 'Move the alias from server 1 to server 2...';
az sql server dns-alias set –-resource-group $resourceGroupName2 --server $sqlServerName2 `
    --name $sqlServerDnsAliasName `
    --original-resource-group $resourceGroupName --original-server $sqlServerName `
    --original-subscription-id $subscriptionId.Id;

Write-Host 'Get the aliases assigned to server 2...';
az sql server dns-alias show –-resource-group $resourceGroupName2 --server $sqlServerName2;

Write-Host 'Remove the alias from server 2...';
az sql server dns-alias delete –-resource-group $resourceGroupName2 --server $sqlServerName2 `
    --name $sqlServerDnsAliasName;
```

* * *

## <a name="next-steps"></a>Następne kroki

Aby uzyskać pełne wyjaśnienie funkcji aliasu DNS dla SQL Database, zobacz [alias usługi DNS dla Azure SQL Database](../../sql-database/dns-alias-overview.md).
