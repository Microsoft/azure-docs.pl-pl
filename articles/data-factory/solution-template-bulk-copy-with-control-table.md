---
title: Kopiowanie zbiorcze z bazy danych przy użyciu tabeli formantów
description: Dowiedz się, jak używać szablonu rozwiązania do kopiowania danych zbiorczych z bazy danych przy użyciu zewnętrznej tabeli sterowania do przechowywania listy partycji tabel źródłowych przy użyciu usługi Azure Data Factory.
services: data-factory
author: dearandyxu
ms.author: yexu
ms.reviewer: douglasl
manager: anandsub
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 12/14/2018
ms.openlocfilehash: d6d634d9a32ae1728e1122d863ddabd94f73ee27
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414843"
---
# <a name="bulk-copy-from-a-database-with-a-control-table"></a>Kopiowanie zbiorcze z bazy danych z tabelą kontrolną
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Aby skopiować dane z magazynu danych w oracle server, Netezza, Teradata lub SQL Server do usługi Azure SQL Data Warehouse, należy załadować ogromne ilości danych z wielu tabel. Zazwyczaj dane muszą być podzielone na partycje w każdej tabeli, dzięki czemu można załadować wiersze z wieloma wątkami równolegle z jednej tabeli. W tym artykule opisano szablon do użycia w tych scenariuszach.

 >! UWAGA Jeśli chcesz skopiować dane z niewielkiej liczby tabel o stosunkowo małym wolumenie danych do magazynu SQL Data Warehouse, bardziej efektywne jest użycie [narzędzia Dane kopiowania danych z fabryki danych platformy Azure](copy-data-tool.md). Szablon, który jest opisany w tym artykule jest więcej niż potrzebujesz dla tego scenariusza.

## <a name="about-this-solution-template"></a>Informacje o tym szablonie rozwiązania

Ten szablon pobiera listę partycji źródłowej bazy danych do skopiowania z tabeli kontroli zewnętrznej. Następnie iteruje nad każdą partycją w źródłowej bazie danych i kopiuje dane do miejsca docelowego.

Szablon zawiera trzy działania:
- **Wyszukiwanie** pobiera listę pewnych partycji bazy danych z tabeli sterowania zewnętrznego.
- **ForEach** pobiera listę partycji z działania odnośników i iteruje każdą partycję do działania Kopiowanie.
- **Kopiuj** każdą partycję ze źródłowego magazynu bazy danych do magazynu docelowego.

Szablon definiuje następujące parametry:
- *Control_Table_Name* jest tabelą kontroli zewnętrznej, która przechowuje listę partycji dla źródłowej bazy danych.
- *Control_Table_Schema_PartitionID* jest nazwą nazwy kolumny w tabeli kontroli zewnętrznej, która przechowuje każdy identyfikator partycji. Upewnij się, że identyfikator partycji jest unikatowy dla każdej partycji w źródłowej bazie danych.
- *Control_Table_Schema_SourceTableName* jest tabela kontroli zewnętrznej, która przechowuje każdą nazwę tabeli ze źródłowej bazy danych.
- *Control_Table_Schema_FilterQuery* jest nazwą kolumny w tabeli kontroli zewnętrznej, która przechowuje kwerendę filtru, aby uzyskać dane z każdej partycji w źródłowej bazie danych. Na przykład, jeśli dane są podzielone na partycje według roku, kwerenda przechowywana w każdym wierszu może być podobna do 'select * z datasource where LastModifytime >= ''2015-01-01 00:00:00'' i LastModifytime <= ''2015-12-31 23:59:59.999'''.
- *Data_Destination_Folder_Path* to ścieżka, w której dane są kopiowane do magazynu docelowego (ma zastosowanie, gdy miejsce docelowe, które wybierzesz, to "System plików" lub "Usługa Azure Data Lake Storage Gen1"). 
- *Data_Destination_Container* jest ścieżką folderu głównego, do której dane są kopiowane w magazynie docelowym. 
- *Data_Destination_Directory* jest ścieżką katalogu pod katalogiem głównym, w którym dane są kopiowane do magazynu docelowego. 

Ostatnie trzy parametry, które definiują ścieżkę w magazynie docelowym są widoczne tylko wtedy, gdy miejsce docelowe, które wybierzesz, jest magazynem opartym na plikach. Jeśli wybierzesz "Azure Synapse Analytics (dawniej SQL DW)" jako magazyn docelowy, te parametry nie są wymagane. Ale nazwy tabel i schemat w magazynie danych SQL musi być taka sama jak te w źródłowej bazie danych.

## <a name="how-to-use-this-solution-template"></a>Jak korzystać z tego szablonu rozwiązania

1. Utwórz tabelę formantów w programie SQL Server lub usłudze Azure SQL Database, aby przechowywać listę partycji źródłowej bazy danych do kopiowania zbiorczego. W poniższym przykładzie istnieje pięć partycji w źródłowej bazie danych. Trzy partycje są dla *datasource_table,* a dwa są dla *project_table*. Kolumna *LastModifytime* służy do partycjonowania danych w tabeli *datasource_table* ze źródłowej bazy danych. Kwerenda używana do odczytu pierwszej partycji to 'select * from datasource_table where LastModifytime >= ''2015-01-01 00:00:00'' i LastModifytime <= ''2015-12-31 23:59:59.999'''. Podobnej kwerendy można użyć do odczytu danych z innych partycji.

     ```sql
            Create table ControlTableForTemplate
            (
            PartitionID int,
            SourceTableName  varchar(255),
            FilterQuery varchar(255)
            );

            INSERT INTO ControlTableForTemplate
            (PartitionID, SourceTableName, FilterQuery)
            VALUES
            (1, 'datasource_table','select * from datasource_table where LastModifytime >= ''2015-01-01 00:00:00'' and LastModifytime <= ''2015-12-31 23:59:59.999'''),
            (2, 'datasource_table','select * from datasource_table where LastModifytime >= ''2016-01-01 00:00:00'' and LastModifytime <= ''2016-12-31 23:59:59.999'''),
            (3, 'datasource_table','select * from datasource_table where LastModifytime >= ''2017-01-01 00:00:00'' and LastModifytime <= ''2017-12-31 23:59:59.999'''),
            (4, 'project_table','select * from project_table where ID >= 0 and ID < 1000'),
            (5, 'project_table','select * from project_table where ID >= 1000 and ID < 2000');
    ```

2. Przejdź do szablonu **Kopiowanie zbiorcze z bazy danych.** Utwórz **nowe** połączenie z tabelą sterowania zewnętrznego utworzoną w kroku 1.

    ![Tworzenie nowego połączenia z tabelą kontrolną](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable2.png)

3. Utwórz **nowe** połączenie ze źródłową bazą danych, z której kopiujesz dane.

    ![Tworzenie nowego połączenia ze źródłową bazą danych](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable3.png)
    
4. Utwórz **nowe** połączenie z docelowym magazynem danych, do którego kopiujesz dane.

    ![Tworzenie nowego połączenia z magazynem docelowym](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable4.png)

5. Wybierz pozycję **Użyj tego szablonu**.

6. Zostanie wyświetlony potok, jak pokazano w poniższym przykładzie:

    ![Przejrzyj potok](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable6.png)

7. Wybierz **debugowanie**, wprowadź **parametry**, a następnie wybierz **zakończ**.

    ![Kliknij przycisk **Debugowanie**](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable7.png)

8. Zostaną wyświetlenia wyników podobnych do poniższego przykładu:

    ![Przejrzyj wynik](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable8.png)

9. (Opcjonalnie) Jeśli jako miejsce docelowe danych wybrano opcję "Azure Synapse Analytics (dawniej SQL DW)", należy wprowadzić połączenie z magazynem obiektów blob platformy Azure w celu przeprowadzenia przemieszczania, zgodnie z wymaganiami bazy danych SQL Data Warehouse. Szablon automatycznie wygeneruje ścieżkę kontenera dla magazynu obiektów Blob. Sprawdź, czy kontener został utworzony po uruchomieniu potoku.
    
    ![Ustawienie bazy polibałowej](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable9.png)
       
## <a name="next-steps"></a>Następne kroki

- [Wprowadzenie do usługi Azure Data Factory](introduction.md)
