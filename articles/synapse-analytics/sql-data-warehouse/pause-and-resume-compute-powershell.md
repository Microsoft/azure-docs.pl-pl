---
title: 'Szybki Start: Wstrzymywanie i wznawianie obliczeń w puli Synapse SQL z Azure PowerShell'
description: Za pomocą Azure PowerShell można wstrzymywać i wznawiać pulę SQL Synapse (magazyn danych). zasoby obliczeniowe.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql-dw
ms.date: 03/20/2019
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse, devx-track-azurepowershell
ms.openlocfilehash: 08ddb6077887043d798af9790a7b66f1b8ebb95c
ms.sourcegitcommit: f796e1b7b46eb9a9b5c104348a673ad41422ea97
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/30/2020
ms.locfileid: "91570705"
---
# <a name="quickstart-pause-and-resume-compute-in-synapse-sql-pool-with-azure-powershell"></a>Szybki Start: Wstrzymywanie i wznawianie obliczeń w puli Synapse SQL z Azure PowerShell

Za pomocą Azure PowerShell można wstrzymywać i wznawiać zasoby obliczeniowe puli SQL (Data Warehouse) Synapse.
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

## <a name="before-you-begin"></a>Przed rozpoczęciem

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

W tym przewodniku szybki start założono, że masz już pulę SQL, którą można wstrzymywać i wznawiać. Jeśli trzeba ją utworzyć, możesz użyć [Utwórz i Połącz Portal](create-data-warehouse-portal.md) , aby utworzyć pulę SQL o nazwie **mySampleDataWarehouse**.

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

## <a name="look-up-sql-pool-information"></a>Wyszukiwanie informacji o puli SQL

Znajdź nazwę bazy danych, nazwę serwera i grupę zasobów dla puli SQL, którą planujesz wstrzymać i wznowić.

Wykonaj następujące kroki, aby znaleźć informacje o lokalizacji dla puli SQL:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
1. Kliknij pozycję **Azure Synapse Analytics (wcześniej SQL DW)** na lewej stronie Azure Portal.
1. Wybierz pozycję **mySampleDataWarehouse** na stronie **usługi Azure Synapse Analytics (dawniej SQL DW)** . Zostanie otwarta Pula SQL.

    ![Nazwa serwera i grupa zasobów](./media/pause-and-resume-compute-powershell/locate-data-warehouse-information.png)

1. Zapisz nazwę puli SQL, która jest nazwą bazy danych. Ponadto zanotuj nazwę serwera i grupy zasobów.
1. Użyj tylko pierwszej części nazwy serwera w poleceniach cmdlet programu PowerShell. Na powyższym obrazie pełna nazwa serwera to sqlpoolservername.database.windows.net. Używamy **sqlpoolservername** jako nazwy serwera w poleceniu cmdlet programu PowerShell.

## <a name="pause-compute"></a>Wstrzymywanie obliczeń

Aby zaoszczędzić koszty, możesz wstrzymywać i wznawiać zasoby obliczeniowe na żądanie. Na przykład jeśli baza danych nie jest używana w porze nocnej i w weekendy, możesz ją wstrzymać w tych godzinach i wznowić ją w ciągu dnia.

>[!NOTE]
>Nie jest naliczana opłata za zasoby obliczeniowe, gdy baza danych jest wstrzymana. Opłata za magazyn jest jednak nadal naliczana.

Aby wstrzymać bazę danych, należy użyć polecenia cmdlet [Suspend-AzSqlDatabase](/powershell/module/az.sql/suspend-azsqldatabase?toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) . Poniższy przykład wstrzymuje pulę SQL o nazwie **mySampleDataWarehouse** hostowaną na serwerze o nazwie **sqlpoolservername**. Serwer należy do grupy zasobów platformy Azure o nazwie Moja **resourceName**.

```Powershell
Suspend-AzSqlDatabase –ResourceGroupName "myResourceGroup" `
–ServerName "nsqlpoolservername" –DatabaseName "mySampleDataWarehouse"
```

Poniższy przykład pobiera bazę danych do obiektu $database. Następnie przekazuje obiekt do [zawieszenia-AzSqlDatabase](/powershell/module/az.sql/suspend-azsqldatabase?toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). Wyniki są przechowywane w obiekcie resultDatabase. Końcowe polecenie wyświetla wyniki.

```Powershell
$database = Get-AzSqlDatabase –ResourceGroupName "myResourceGroup" `
–ServerName "sqlpoolservername" –DatabaseName "mySampleDataWarehouse"
$resultDatabase = $database | Suspend-AzSqlDatabase
$resultDatabase
```

## <a name="resume-compute"></a>Wznów Obliczanie

Aby uruchomić bazę danych, należy użyć polecenia cmdlet [Resume-AzSqlDatabase](/powershell/module/az.sql/resume-azsqldatabase?toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) . W poniższym przykładzie jest uruchamiana baza danych o nazwie **mySampleDataWarehouse** hostowana na serwerze o nazwie **sqlpoolservername**. Serwer należy do grupy zasobów platformy Azure o nazwie Moja **resourceName**.

```Powershell
Resume-AzSqlDatabase –ResourceGroupName "myResourceGroup" `
–ServerName "sqlpoolservername" -DatabaseName "mySampleDataWarehouse"
```

W następnym przykładzie baza danych jest pobierana do obiektu $database. Następnie przekazuje obiekt do [Resume-AzSqlDatabase](/powershell/module/az.sql/resume-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) i zapisuje wyniki w $resultDatabase. Końcowe polecenie wyświetla wyniki.

```Powershell
$database = Get-AzSqlDatabase –ResourceGroupName "myResourceGroup" `
–ServerName "sqlpoolservername" –DatabaseName "mySampleDataWarehouse"
$resultDatabase = $database | Resume-AzSqlDatabase
$resultDatabase
```

## <a name="check-status-of-your-sql-pool-operation"></a>Sprawdź stan operacji puli SQL

Aby sprawdzić stan puli SQL, użyj polecenia cmdlet [Get-AzSqlDatabaseActivity](/powershell/module/az.sql/Get-AzSqlDatabaseActivity?toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) .

```Powershell
Get-AzSqlDatabaseActivity -ResourceGroupName "myResourceGroup" -ServerName "sqlpoolservername" -DatabaseName "mySampleDataWarehouse"
```

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Opłaty są naliczane za jednostki magazynu danych i dane przechowywane w puli SQL. Opłaty za te zasoby obliczeniowe i magazynowe są naliczane osobno.

- Jeśli chcesz zachować dane w magazynie, Wstrzymaj obliczenia.
- Jeśli chcesz usunąć przyszłe opłaty, możesz usunąć pulę SQL.

Wykonaj następujące kroki, aby wyczyścić zasoby zgodnie z potrzebami.

1. Zaloguj się do [Azure Portal](https://portal.azure.com)i kliknij pulę SQL.

    ![Czyszczenie zasobów](./media/load-data-from-azure-blob-storage-using-polybase/clean-up-resources.png)

2. Aby wstrzymać obliczenia, kliknij przycisk **Wstrzymaj**. Gdy pula SQL jest wstrzymana, zobaczysz przycisk **Start** .  Aby wznowić obliczenia, kliknij przycisk **Uruchom**.

3. Aby usunąć pulę SQL, aby nie naliczać opłat za zasoby obliczeniowe i magazynowanie, kliknij przycisk **Usuń**.

4. Aby usunąć utworzony serwer SQL, kliknij pozycję **sqlpoolservername.Database.Windows.NET**, a następnie kliknij pozycję **Usuń**.  Należy zachować ostrożność podczas usuwania, ponieważ usunięcie serwera spowoduje również usunięcie wszystkich baz danych przypisanych do tego serwera.

5. Aby usunąć grupę zasobów, kliknij pozycję **myResourceGroup**, a następnie kliknij pozycję **Usuń grupę zasobów**.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o puli SQL, przejdź do artykułu [ładowanie danych do puli SQL](load-data-from-azure-blob-storage-using-polybase.md) . Aby uzyskać dodatkowe informacje na temat zarządzania możliwościami obliczeniowymi, zobacz artykuł [Zarządzanie obliczeniami obliczeniowymi](sql-data-warehouse-manage-compute-overview.md) .
