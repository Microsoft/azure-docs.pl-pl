---
title: 'Szybki start: wstrzymywanie i wznawianie obliczeń w dedykowanej puli SQL (dawniej SQL DW) przy użyciu Azure PowerShell'
description: Za pomocą usługi Azure PowerShell wstrzymywać i wznawiać dedykowaną pulę SQL (dawniej SQL DW). zasobów obliczeniowych.
services: synapse-analytics
author: gaursa
ms.author: gaursa
manager: craigg
ms.reviewer: igorstan
ms.date: 03/20/2019
ms.topic: quickstart
ms.service: synapse-analytics
ms.subservice: sql-dw
ms.custom:
- seo-lt-2019
- azure-synapse
- devx-track-azurepowershell
- mode-api
ms.openlocfilehash: b204132a49a8790b35cc99af8eebf465fd90f041
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107536371"
---
# <a name="quickstart-pause-and-resume-compute-in-dedicated-sql-pool-formerly-sql-dw-with-azure-powershell"></a>Szybki start: wstrzymywanie i wznawianie obliczeń w dedykowanej puli SQL (dawniej SQL DW) przy użyciu Azure PowerShell

Za pomocą usługi Azure PowerShell wstrzymywać i wznawiać dedykowane zasoby obliczeniowe puli SQL (dawniej SQL DW).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

## <a name="before-you-begin"></a>Zanim rozpoczniesz

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

W tym przewodniku Szybki start założono, że masz już dedykowaną pulę SQL (dawniej SQL DW), która może być wstrzymywana i wznawiana. Jeśli musisz ją utworzyć, możesz użyć polecenia Utwórz i połącz — [portal,](create-data-warehouse-portal.md) aby utworzyć dedykowaną pulę SQL (dawniej SQL DW) o nazwie **mySampleDataWarehouse.**

## <a name="log-in-to-azure"></a>Zaloguj się do platformy Azure.

Zaloguj się do subskrypcji platformy Azure przy użyciu polecenia [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) i postępuj zgodnie z instrukcjami na ekranie.

```powershell
Connect-AzAccount
```

Aby zobaczyć, której subskrypcji używasz, uruchom [get-AzSubscription](/powershell/module/az.accounts/get-azsubscription?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

```powershell
Get-AzSubscription
```

Jeśli musisz użyć innej subskrypcji niż domyślna, uruchom [set-AzContext](/powershell/module/az.accounts/set-azcontext?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

```powershell
Set-AzContext -SubscriptionName "MySubscription"
```

## <a name="look-up-dedicated-sql-pool-formerly-sql-dw-information"></a>Wyszukiwania informacji o dedykowanej puli SQL (dawniej SQL DW)

Znajdź nazwę bazy danych, nazwę serwera i grupę zasobów dla dedykowanej puli SQL (dawniej SQL DW), która ma być wstrzymywana i wznawiana.

Wykonaj następujące kroki, aby znaleźć informacje o lokalizacji dla dedykowanej puli SQL (dawniej SQL DW):

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
1. Kliknij **Azure Synapse Analytics (dawniej SQL DW)** na lewej stronie Azure Portal.
1. Wybierz **pozycję mySampleDataWarehouse** na **stronie Azure Synapse Analytics (dawniej SQL DW).** Zostanie otwarta pula SQL.

    ![Nazwa serwera i grupa zasobów](./media/pause-and-resume-compute-powershell/locate-data-warehouse-information.png)

1. Zapisz nazwę dedykowanej puli SQL (dawniej SQL DW), która jest nazwą bazy danych. Ponadto zanotuj nazwę serwera i grupy zasobów.
1. Użyj tylko pierwszej części nazwy serwera w poleceniach cmdlet programu PowerShell. Na poprzedniej ilustracji pełna nazwa serwera jest sqlpoolservername.database.windows.net. W poleceniach cmdlet programu PowerShell używamy nazwy serwera **sqlpoolservername.**

## <a name="pause-compute"></a>Wstrzymywanie zasobów obliczeniowych

Aby zaoszczędzić koszty, możesz wstrzymać i wznowić zasoby obliczeniowe na żądanie. Jeśli na przykład nie używasz bazy danych w nocy i w weekendy, możesz ją wstrzymać w tych godzinach i wznowić ją w ciągu dnia.

>[!NOTE]
>Gdy baza danych jest wstrzymana, nie są naliczane opłaty za zasoby obliczeniowe. Jednak nadal będą naliczane opłaty za magazyn.

Aby wstrzymać bazę danych, użyj polecenia cmdlet [Suspend-AzSqlDatabase.](/powershell/module/az.sql/suspend-azsqldatabase?toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) Poniższy przykład wstrzymuje pulę SQL o **nazwie mySampleDataWarehouse** hostowaną na serwerze o **nazwie sqlpoolservername**. Serwer znajduje się w grupie zasobów platformy Azure o **nazwie myResourceGroup**.

```Powershell
Suspend-AzSqlDatabase –ResourceGroupName "myResourceGroup" `
–ServerName "sqlpoolservername" –DatabaseName "mySampleDataWarehouse"
```

Poniższy przykład pobiera bazę danych do obiektu $database danych. Następnie potokuje obiekt do [obiektu Suspend-AzSqlDatabase](/powershell/module/az.sql/suspend-azsqldatabase?toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). Wyniki są przechowywane w obiekcie resultDatabase. Końcowe polecenie wyświetla wyniki.

```Powershell
$database = Get-AzSqlDatabase –ResourceGroupName "myResourceGroup" `
–ServerName "sqlpoolservername" –DatabaseName "mySampleDataWarehouse"
$resultDatabase = $database | Suspend-AzSqlDatabase
$resultDatabase
```

## <a name="resume-compute"></a>Wznawianie obliczeń

Aby uruchomić bazę danych, użyj polecenia cmdlet [Resume-AzSqlDatabase.](/powershell/module/az.sql/resume-azsqldatabase?toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) Poniższy przykład uruchamia bazę danych o nazwie **mySampleDataWarehouse** hostowaną na serwerze o **nazwie sqlpoolservername**. Serwer znajduje się w grupie zasobów platformy Azure o **nazwie myResourceGroup**.

```Powershell
Resume-AzSqlDatabase –ResourceGroupName "myResourceGroup" `
–ServerName "sqlpoolservername" -DatabaseName "mySampleDataWarehouse"
```

W następnym przykładzie baza danych jest pobierana do obiektu $database . Następnie potokuje obiekt do [resume-AzSqlDatabase](/powershell/module/az.sql/resume-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) i zapisuje wyniki w $resultDatabase. Końcowe polecenie wyświetla wyniki.

```Powershell
$database = Get-AzSqlDatabase –ResourceGroupName "myResourceGroup" `
–ServerName "sqlpoolservername" –DatabaseName "mySampleDataWarehouse"
$resultDatabase = $database | Resume-AzSqlDatabase
$resultDatabase
```

## <a name="check-status-of-your-sql-pool-operation"></a>Sprawdzanie stanu operacji puli SQL

Aby sprawdzić stan dedykowanej puli SQL (dawniej SQL DW), użyj polecenia cmdlet [Get-AzSqlDatabaseActivity.](/powershell/module/az.sql/Get-AzSqlDatabaseActivity?toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)

```Powershell
Get-AzSqlDatabaseActivity -ResourceGroupName "myResourceGroup" -ServerName "sqlpoolservername" -DatabaseName "mySampleDataWarehouse"
```

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Opłaty są naliczane za jednostki magazynu danych i dane przechowywane w dedykowanej puli SQL (dawniej SQL DW). Opłaty za te zasoby obliczeniowe i magazynowe są naliczane osobno.

- Jeśli chcesz przechowywać dane w magazynie, wstrzymaj obliczenia.
- Jeśli chcesz usunąć przyszłe opłaty, możesz usunąć pulę SQL.

Wykonaj następujące kroki, aby wyczyścić zasoby zgodnie z potrzebami.

1. Zaloguj się do [Azure Portal](https://portal.azure.com)i kliknij pulę SQL.

    ![Czyszczenie zasobów](./media/load-data-from-azure-blob-storage-using-polybase/clean-up-resources.png)

2. Aby wstrzymać obliczenia, kliknij przycisk **Wstrzymaj**. Po wstrzymaniu puli SQL zostanie wyświetlony **przycisk** Uruchom.  Aby wznowić obliczenia, kliknij przycisk **Uruchom**.

3. Aby usunąć pulę SQL, aby nie ponosić opłat za zasoby obliczeniowe lub magazynowe, kliknij pozycję **Usuń.**

4. Aby usunąć utworzony serwer SQL, kliknij przycisk **sqlpoolservername.database.windows.net**, a następnie kliknij pozycję **Usuń.**  Należy zachować ostrożność podczas usuwania, ponieważ usunięcie serwera spowoduje również usunięcie wszystkich baz danych przypisanych do tego serwera.

5. Aby usunąć grupę zasobów, kliknij pozycję **myResourceGroup**, a następnie kliknij pozycję **Usuń grupę zasobów**.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat puli SQL, przejdź do artykułu Ładowanie danych do dedykowanej puli [SQL (dawniej SQL DW).](./load-data-from-azure-blob-storage-using-copy.md) Aby uzyskać dodatkowe informacje na temat zarządzania możliwościami obliczeniowymi, zobacz [artykuł Manage compute overview (Omówienie zarządzania obliczeniami).](sql-data-warehouse-manage-compute-overview.md)
