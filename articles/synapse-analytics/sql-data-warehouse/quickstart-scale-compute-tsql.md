---
title: 'Szybki Start: skalowanie zasobów obliczeniowych w usłudze Azure Synapse Analytics — T-SQL'
description: Skalowanie zasobów obliczeniowych w usłudze Azure Synapse Analytics przy użyciu języka T-SQL i SQL Server Management Studio (SSMS). Skalowanie zasobów obliczeniowych w poziomie zapewnia lepszą wydajność, a zmniejszenie ich skali pozwala ograniczyć koszty.
services: synapse-analytics
author: Antvgski
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql-dw
ms.date: 04/17/2018
ms.author: anvang
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: d11474a3f3b5d8c314f67260fddbbe0a98fe5196
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "91569895"
---
# <a name="quickstart-scale-compute-in-azure-synapse-analytics-using-t-sql"></a>Szybki Start: skalowanie zasobów obliczeniowych w usłudze Azure Synapse Analytics przy użyciu języka T-SQL

Skalowanie obliczeniowe w usłudze Azure Synapse Analytics (dawniej SQL DW) przy użyciu języka T-SQL i SQL Server Management Studio (SSMS). [Skalowanie zasobów obliczeniowych w poziomie](sql-data-warehouse-manage-compute-overview.md) zapewnia lepszą wydajność, a zmniejszenie ich skali pozwala ograniczyć koszty.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

## <a name="before-you-begin"></a>Zanim rozpoczniesz

Pobierz i zainstaluj najnowszą wersję programu [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) (SSMS).

## <a name="create-a-data-warehouse"></a>Tworzenie magazynu danych

Wykonaj czynności opisane w przewodniku [Szybki start: tworzenie i łączenie — portal](create-data-warehouse-portal.md), aby utworzyć magazyn danych o nazwie **mySampleDataWarehouse**. Ukończ Przewodnik Szybki Start, aby upewnić się, że masz regułę zapory i można nawiązać połączenie z magazynem danych z poziomu SQL Server Management Studio.

## <a name="connect-to-the-server-as-server-admin"></a>Nawiąż połączenie z serwerem jako administrator serwera

W tej sekcji używany jest program [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) (SSMS) do nawiązywania połączenia z serwerem SQL platformy Azure.

1. Otwórz program SQL Server Management Studio.

2. W oknie dialogowym **Połącz z serwerem** wprowadź następujące informacje:

   | Ustawienie       | Sugerowana wartość | Opis |
   | ------------ | ------------------ | ------------------------------------------------- |
   | Typ serwera | Aparat bazy danych | Ta wartość jest wymagana |
   | Nazwa serwera | W pełni kwalifikowana nazwa serwera | Oto przykład: **mySampleDataWarehouseservername.Database.Windows.NET**. |
   | Uwierzytelnianie | Uwierzytelnianie programu SQL Server | Uwierzytelnianie SQL to jedyny typ uwierzytelniania skonfigurowany w tym samouczku. |
   | Zaloguj się | Konto administratora serwera | Konto określone podczas tworzenia serwera. |
   | Hasło | Hasło konta administratora serwera | Hasło określone podczas tworzenia serwera. |

    ![Połącz z serwerem](./media/quickstart-scale-compute-tsql/connect-to-server.png)

3. Kliknij przycisk **Połącz**. W programie SSMS zostanie otwarte okno Eksplorator obiektów.

4. W Eksploratorze obiektów rozwiń pozycję **Bazy danych**. Następnie rozwiń węzeł **mySampleDataWarehouse** , aby wyświetlić obiekty w nowej bazie danych.

    ![Obiekty bazy danych](./media/quickstart-scale-compute-tsql/connected.png)

## <a name="view-service-objective"></a>Wyświetlanie celu usługi

Ustawienie dotyczące celu usługi zawiera liczbę jednostek magazynu danych dla magazynu danych.

Aby wyświetlić bieżące jednostki magazynu danych dla magazynu danych:

1. W obszarze połączenie z usługą **mySampleDataWarehouseservername.Database.Windows.NET**rozwiń pozycję **systemowe bazy danych**.
2. Kliknij prawym przyciskiem myszy pozycję **master**, a następnie wybierz pozycję **New Query** (Nowe zapytanie). Otworzy się okno nowego zapytania.
3. Uruchom następujące zapytanie, aby wybrać odpowiedni element z dynamicznego widoku zarządzania sys.database_service_objectives.

    ```sql
    SELECT
        db.name [Database]
    ,    ds.edition [Edition]
    ,    ds.service_objective [Service Objective]
    FROM
         sys.database_service_objectives ds
    JOIN
        sys.databases db ON ds.database_id = db.database_id
    WHERE
        db.name = 'mySampleDataWarehouse'
    ```

4. Następujące wyniki wskazują, że cel usługi dla bazy danych **mySampleDataWarehouse** przyjmuje wartość DW400.

    ![każ-Current-jednostek dwu](./media/quickstart-scale-compute-tsql/view-current-dwu.png)

## <a name="scale-compute"></a>Skalowanie zasobów obliczeniowych

W usłudze Azure Synapse można zwiększyć lub zmniejszyć zasoby obliczeniowe przez dostosowanie jednostek magazynu danych. Postępując według czynności opisanych w artykule [Tworzenie i łączenie — portal](create-data-warehouse-portal.md) utworzono bazę danych **mySampleDataWarehouse** z 400 jednostkami DWU. Poniższe kroki umożliwiają dostosowanie liczby jednostek DWU dla bazy danych **mySampleDataWarehouse**.

Aby zmienić liczbę jednostek magazynu danych:

1. Kliknij prawym przyciskiem myszy pozycję **master**, a następnie wybierz pozycję **New Query** (Nowe zapytanie).
2. Użyj instrukcji języka T-SQL [ALTER DATABASE](/sql/t-sql/statements/alter-database-azure-sql-database?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest), aby zmodyfikować cel usługi. Uruchom następujące zapytanie, aby zmienić celu usługi na wartość DW300.

    ```Sql
    ALTER DATABASE mySampleDataWarehouse
    MODIFY (SERVICE_OBJECTIVE = 'DW300c');
    ```

## <a name="monitor-scale-change-request"></a>Monitorowanie żądania zmiany skali

Aby wyświetlić postęp poprzedniego żądania zmiany, możesz użyć składni T-SQL `WAITFORDELAY` do sondowania dynamicznego widoku zarządzania (DMV, dynamic management view) sys.dm_operation_status.

Aby sondować stan zmiany obiektu usługi:

1. Kliknij prawym przyciskiem myszy pozycję **master**, a następnie wybierz pozycję **New Query** (Nowe zapytanie).
2. Uruchom następujące zapytanie, aby sondować widok DMV sys.dm_operation_status.

    ```sql
    WHILE
    (
        SELECT TOP 1 state_desc
        FROM sys.dm_operation_status
        WHERE
            1=1
            AND resource_type_desc = 'Database'
            AND major_resource_id = 'mySampleDataWarehouse'
            AND operation = 'ALTER DATABASE'
        ORDER BY
            start_time DESC
    ) = 'IN_PROGRESS'
    BEGIN
        RAISERROR('Scale operation in progress',0,0) WITH NOWAIT;
        WAITFOR DELAY '00:00:05';
    END
    PRINT 'Complete';
    ```

3. Dane wyjściowe pokazują dziennik sondowania stanu.

    ![Stan operacji](./media/quickstart-scale-compute-tsql/polling-output.png)

## <a name="check-data-warehouse-state"></a>Sprawdzanie stanu magazynu danych

Gdy magazyn danych jest wstrzymany, nawiązanie z nim połączenia za pomocą języka T-SQL nie jest możliwe. Aby wyświetlić bieżący stan magazynu danych, można użyć polecenia cmdlet programu PowerShell. Aby zapoznać się z przykładem, zobacz [Sprawdzanie stanu magazynu danych — PowerShell](quickstart-scale-compute-powershell.md#check-data-warehouse-state).

## <a name="check-operation-status"></a>Sprawdzanie stanu operacji

Aby zwrócić informacje dotyczące różnych operacji zarządzania na platformie Azure Synapse, uruchom następujące zapytanie na [sys.dm_operation_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) DMV. Na przykład zwraca operację i typ stanu operacji: IN_PROGRESS (W toku) lub COMPLETED (Ukończono).

```sql
SELECT *
FROM
    sys.dm_operation_status
WHERE
    resource_type_desc = 'Database'
AND
    major_resource_id = 'mySampleDataWarehouse'
```

## <a name="next-steps"></a>Następne kroki

Teraz już wiesz, jak skalować zasoby obliczeniowe na potrzeby magazynu danych. Aby dowiedzieć się więcej o usłudze Azure Synapse, przejdź do samouczka dotyczącego ładowania danych.

> [!div class="nextstepaction"]
>[Ładowanie danych do usługi Azure Synapse Analytics](load-data-from-azure-blob-storage-using-polybase.md)
