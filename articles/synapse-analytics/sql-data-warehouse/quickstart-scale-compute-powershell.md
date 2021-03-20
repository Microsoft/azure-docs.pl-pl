---
title: 'Szybki Start: skalowanie obliczeniowe dla dedykowanej puli SQL (dawniej SQL DW) (Azure PowerShell)'
description: Obliczenia można skalować dla dedykowanej puli SQL (dawniej SQL DW) przy użyciu Azure PowerShell.
services: synapse-analytics
author: Antvgski
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql-dw
ms.date: 04/17/2018
ms.author: anvang
ms.reviewer: igorstan
ms.custom: seo-lt-2019, devx-track-azurepowershell
ms.openlocfilehash: 87e10740e6081431bad96daa930f61238ca495bd
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96921914"
---
# <a name="quickstart-scale-compute-for-dedicated-sql-pool-formerly-sql-dw-with-azure-powershell"></a>Szybki Start: skalowanie zasobów obliczeniowych dla dedykowanej puli SQL (dawniej SQL DW) za pomocą Azure PowerShell

Obliczenia można skalować dla dedykowanej puli SQL (dawniej SQL DW) przy użyciu Azure PowerShell. [Skalowanie zasobów obliczeniowych w poziomie](sql-data-warehouse-manage-compute-overview.md) zapewnia lepszą wydajność, a zmniejszenie ich skali pozwala ograniczyć koszty.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

## <a name="before-you-begin"></a>Zanim rozpoczniesz

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

W tym przewodniku szybki start założono, że masz już dedykowaną pulę SQL (dawniej SQL DW), którą można skalować. Jeśli trzeba ją utworzyć, użyj instrukcji [Create i Connect-Portal](create-data-warehouse-portal.md) , aby utworzyć dedykowaną pulę SQL (dawniej SQL DW) o nazwie **mySampleDataWarehouse**.

## <a name="log-in-to-azure"></a>Zaloguj się do platformy Azure.

Zaloguj się do subskrypcji platformy Azure za pomocą polecenia [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) i postępuj zgodnie z instrukcjami wyświetlanymi na ekranie.

```powershell
Connect-AzAccount
```

Aby sprawdzić, której subskrypcji używasz, uruchom polecenie [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

```powershell
Get-AzSubscription
```

Jeśli musisz użyć innej subskrypcji niż domyślna, uruchom polecenie [Set-AzContext](/powershell/module/az.accounts/set-azcontext?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

```powershell
Set-AzContext -SubscriptionName "MySubscription"
```

## <a name="look-up-data-warehouse-information"></a>Wyszukiwanie informacji w magazynie danych

Znajdź nazwę bazy danych, nazwę serwera oraz grupę zasobów magazynu danych, który chcesz wstrzymać i wznowić.

Wykonaj następujące kroki, aby znaleźć informacje o lokalizacji dla magazynu danych.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2. Kliknij pozycję **Azure Synapse Analytics (wcześniej SQL DW)** na lewej stronie nawigacyjnej Azure Portal.
3. Wybierz pozycję **mySampleDataWarehouse** na stronie **usługi Azure Synapse Analytics (dawniej SQL DW)** , aby otworzyć magazyn danych.

    ![Nazwa serwera i grupa zasobów](./media/quickstart-scale-compute-powershell/locate-data-warehouse-information.png)

4. Zanotuj nazwę magazynu danych, która będzie używana jako nazwa bazy danych. Pamiętaj, że magazyn danych jest jednym z typów bazy danych. Ponadto zanotuj nazwę serwera i grupy zasobów. W poleceniach Wstrzymaj i Wznów zostanie użyta nazwa serwera i nazwa grupy zasobów.
5. Użyj tylko pierwszej części nazwy serwera w poleceniach cmdlet programu PowerShell. Na powyższym obrazie pełna nazwa serwera to sqlpoolservername.database.windows.net. Używamy **sqlpoolservername** jako nazwy serwera w poleceniu cmdlet programu PowerShell.

## <a name="scale-compute"></a>Skalowanie zasobów obliczeniowych

W dedykowanej puli SQL (dawniej SQL DW) można zwiększyć lub zmniejszyć zasoby obliczeniowe przez dostosowanie jednostek magazynu danych. Postępując według czynności opisanych w artykule [Tworzenie i łączenie — portal](create-data-warehouse-portal.md) utworzono bazę danych **mySampleDataWarehouse** z 400 jednostkami DWU. Poniższe kroki umożliwiają dostosowanie liczby jednostek DWU dla bazy danych **mySampleDataWarehouse**.

Aby zmienić jednostki magazynu danych, użyj polecenia cmdlet [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) programu PowerShell. Poniższy przykład ustawia jednostki magazynu danych DW300c dla bazy danych **mySampleDataWarehouse**, która jest hostowana w grupie zasobów **ResourceGroupName** na serwerze **sqlpoolservername**.

```Powershell
Set-AzSqlDatabase -ResourceGroupName "resourcegroupname" -DatabaseName "mySampleDataWarehouse" -ServerName "sqlpoolservername" -RequestedServiceObjectiveName "DW300c"
```

## <a name="check-data-warehouse-state"></a>Sprawdzanie stanu magazynu danych

Aby wyświetlić bieżący stan magazynu danych, użyj polecenia cmdlet [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) programu PowerShell. To polecenie cmdlet wyświetla stan bazy danych **mySampleDataWarehouse** w obszarze Resources **resourcegroupname** i Server **sqlpoolservername.Database.Windows.NET**.

```powershell
$database = Get-AzSqlDatabase -ResourceGroupName resourcegroupname -ServerName sqlpoolservername -DatabaseName mySampleDataWarehouse
```

Wynik operacji będzie podobny do poniższego:

```powershell
ResourceGroupName             : resourcegroupname
ServerName                    : sqlpoolservername
DatabaseName                  : mySampleDataWarehouse
Location                      : North Europe
DatabaseId                    : 34d2ffb8-b70a-40b2-b4f9-b0a39833c974
Edition                       : DataWarehouse
CollationName                 : SQL_Latin1_General_CP1_CI_AS
CatalogCollation              :
MaxSizeBytes                  : 263882790666240
Status                        : Online
CreationDate                  : 11/20/2017 9:18:12 PM
CurrentServiceObjectiveId     : 284f1aff-fee7-4d3b-a211-5b8ebdd28fea
CurrentServiceObjectiveName   : DW300c
RequestedServiceObjectiveId   : 284f1aff-fee7-4d3b-a211-5b8ebdd28fea
RequestedServiceObjectiveName :
ElasticPoolName               :
EarliestRestoreDate           :
Tags                          :
ResourceId                    : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/
                                resourceGroups/resourcegroupname/providers/Microsoft.Sql/servers/sqlpoolservername/databases/mySampleDataWarehouse
CreateMode                    :
ReadScale                     : Disabled
ZoneRedundant                 : False
```

**Stan** bazy danych jest widoczny w danych wyjściowych. W tym przypadku możesz zobaczyć, że baza danych jest w trybie online.  Po uruchomieniu tego polecenia użytkownik powinien otrzymać wartość stanu Online, Wstrzymywanie, Wznawianie, Skalowanie lub Wstrzymana.

Aby wyświetlić stan samodzielnie, użyj następującego polecenia:

```powershell
$database | Select-Object DatabaseName,Status
```

## <a name="next-steps"></a>Następne kroki

Teraz wiesz już, jak skalować obliczenia dla dedykowanej puli SQL (dawniej SQL DW). Aby dowiedzieć się więcej na temat dedykowanej puli SQL (dawniej SQL DW), przejdź do samouczka dotyczącego ładowania danych.

> [!div class="nextstepaction"]
>[Ładowanie danych do dedykowanej puli SQL](load-data-from-azure-blob-storage-using-copy.md)
