---
title: Kopiowanie przyrostowe z bazy danych przy użyciu tabeli formantów
description: Dowiedz się, jak używać szablonu rozwiązania do przyrostowego kopiowania nowych lub zaktualizowanych wierszy tylko z bazy danych z Azure Data Factory.
services: data-factory
documentationcenter: ''
author: dearandyxu
ms.author: yexu
ms.reviewer: douglasl
manager: anandsub
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 12/24/2018
ms.openlocfilehash: 255e4085e24ee7520c603f8a00b3e46c23367a77
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96000831"
---
# <a name="delta-copy-from-a-database-with-a-control-table"></a>Kopiowanie przyrostowe z bazy danych z tabelą formantów

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

W tym artykule opisano szablon, który jest dostępny do przyrostowego ładowania nowych lub zaktualizowanych wierszy z tabeli bazy danych na platformę Azure przy użyciu zewnętrznej tabeli formantów, która przechowuje wartość górnego limitu.

Ten szablon wymaga, aby schemat źródłowej bazy danych zawierał kolumnę znaczników czasu lub przyrostowy klawisz, aby identyfikować nowe lub zaktualizowane wiersze.

>[!NOTE]
> Jeśli masz kolumnę znaczników czasu w źródłowej bazie danych, aby identyfikować nowe lub zaktualizowane wiersze, ale nie chcesz tworzyć tabeli formantów zewnętrznych do użycia na potrzeby kopiowania różnicowego, możesz zamiast tego użyć [narzędzia Azure Data Factory kopiowanie danych](copy-data-tool.md) do uzyskania potoku. To narzędzie używa czasu zaplanowanego wyzwalacza jako zmiennej do odczytywania nowych wierszy ze źródłowej bazy danych.

## <a name="about-this-solution-template"></a>Informacje o tym szablonie rozwiązania

Ten szablon najpierw pobiera starą wartość limitu i porównuje ją z bieżącą wartością limitu. Następnie kopiuje tylko zmiany ze źródłowej bazy danych, na podstawie porównania między dwoma wartościami limitu. Na koniec przechowuje nową wartość górnego limitu w zewnętrznej tabeli formantów na potrzeby ładowania danych różnicowych przy następnym czasie.

Szablon zawiera cztery działania:
- Funkcja **Lookup** pobiera starą wartość górnego limitu, która jest przechowywana w zewnętrznej tabeli formantów.
- Inne działanie **Lookup** pobiera bieżącą wartość górnego limitu ze źródłowej bazy danych.
- **Kopiuj** kopiuje tylko zmiany ze źródłowej bazy danych do magazynu docelowego. Zapytanie, które identyfikuje zmiany w źródłowej bazie danych, jest podobne do "SELECT * FROM Data_Source_Table, gdzie TIMESTAMP_Column >" ostatni górny znak wodny "i TIMESTAMP_Column <=" bieżący górny limit "".
- **SqlServerStoredProcedure** zapisuje bieżącą wartość górnego limitu do zewnętrznej tabeli formantów dla kopiowania przyrostowego.

Szablon definiuje następujące parametry:
- *Data_Source_Table_Name* to tabela w źródłowej bazie danych, z której mają zostać załadowane dane.
- *Data_Source_WaterMarkColumn* to nazwa kolumny w tabeli źródłowej, która jest używana do identyfikowania nowych lub zaktualizowanych wierszy. Typem tej kolumny jest zwykle *DateTime*, *int* lub podobny.
- *Data_Destination_Container* jest ścieżką katalogu głównego miejsca, w którym dane są kopiowane do magazynu docelowego.
- *Data_Destination_Directory* jest ścieżką katalogu w katalogu głównym miejsca, w którym dane są kopiowane do magazynu docelowego.
- *Data_Destination_Table_Name* to miejsce, w którym dane są kopiowane do magazynu docelowego (ma zastosowanie w przypadku wybrania "usługa Azure Synapse Analytics (wcześniej SQL DW)" jako miejsca docelowego danych).
- *Data_Destination_Folder_Path* to miejsce, w którym dane są kopiowane do magazynu docelowego (odpowiednie w przypadku wybrania "system plików" lub "Azure Data Lake Storage Gen1" jako miejsca docelowego danych).
- *Control_Table_Table_Name* jest tabelą formantów zewnętrznych, która przechowuje wartość górnego limitu.
- *Control_Table_Column_Name* jest kolumną w tabeli formantów zewnętrznych, która przechowuje wartość górnego limitu.

## <a name="how-to-use-this-solution-template"></a>Jak używać tego szablonu rozwiązania

1. Zapoznaj się z tabelą źródłową, którą chcesz załadować, i zdefiniuj kolumnę High-limit, która może służyć do identyfikowania nowych lub zaktualizowanych wierszy. Typem tej kolumny może być *DateTime*, *int* lub podobny. Wartość tej kolumny zwiększa się w miarę dodawania nowych wierszy. Z poniższej przykładowej tabeli źródłowej (data_source_table) można użyć kolumny *LastModifytime* jako kolumny górnego limitu.

    ```sql
            PersonID    Name    LastModifytime
            1   aaaa    2017-09-01 00:56:00.000
            2   bbbb    2017-09-02 05:23:00.000
            3   cccc    2017-09-03 02:36:00.000
            4   dddd    2017-09-04 03:21:00.000
            5   eeee    2017-09-05 08:06:00.000
            6   fffffff 2017-09-06 02:23:00.000
            7   gggg    2017-09-07 09:01:00.000
            8   hhhh    2017-09-08 09:01:00.000
            9   iiiiiiiii   2017-09-09 09:01:00.000
    ```
    
2. Utwórz tabelę formantów w SQL Server lub Azure SQL Database do przechowywania wartości górnego limitu ładowania danych różnicowych. W poniższym przykładzie nazwa tabeli formantów jest *znakiem wodnym*. W tej tabeli *WatermarkValue* jest kolumną, która przechowuje wartość górnego limitu, a jej typem jest *DateTime*.

    ```sql
            create table watermarktable
            (
            WatermarkValue datetime,
            );
            INSERT INTO watermarktable
            VALUES ('1/1/2010 12:00:00 AM')
    ```
    
3. Utwórz procedurę składowaną w tym samym SQL Server lub wystąpieniu Azure SQL Database, które zostało użyte do utworzenia tabeli formantów. Procedura składowana służy do zapisywania nowej wartości górnego limitu w tabeli formantów zewnętrznych na potrzeby ładowania danych różnicowych przy następnym czasie.

    ```sql
            CREATE PROCEDURE update_watermark @LastModifiedtime datetime
            AS

            BEGIN

                UPDATE watermarktable
                SET [WatermarkValue] = @LastModifiedtime 

            END
    ```
    
4. Przejdź do **kopii przyrostowej z szablonu bazy danych** . Utwórz **nowe** połączenie z źródłową bazą danych, z której mają zostać skopiowane dane.

    ![Utwórz nowe połączenie z tabelą źródłową](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable4.png)

5. Utwórz **nowe** połączenie z docelowym magazynem danych, do którego chcesz skopiować dane.

    ![Utwórz nowe połączenie z tabelą docelową](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable5.png)

6. Utwórz **nowe** połączenie z tabelą formantów zewnętrznych i procedurą przechowywaną utworzoną w krokach 2 i 3.

    ![Utwórz nowe połączenie z magazynem danych tabeli formantów](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable6.png)

7. Wybierz pozycję **Użyj tego szablonu**.
    
8. Zobaczysz dostępny potok, jak pokazano w następującym przykładzie:
  
    ![Przeglądanie potoku](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable8.png)

9. Wybierz **procedurę składowaną**. W obszarze **nazwa procedury składowanej** wybierz pozycję **[dbo]. [ update_watermark]**. Wybierz pozycję **Importuj parametr**, a następnie wybierz pozycję **Dodaj zawartość dynamiczną**.  

    ![Ustaw działanie procedury składowanej](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable9.png)  

10. Napisz zawartość **\@ {Activity ("LookupCurrentWaterMark"). Output. FirstRow. NewWatermarkValue}**, a następnie wybierz pozycję **Zakończ**.  

    ![Napisz zawartość dla parametrów procedury składowanej](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable10.png)       
     
11. Wybierz pozycję **Debuguj**, wprowadź **Parametry**, a następnie wybierz pozycję **Zakończ**.

    ![SELECT * * Debug * *](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable11.png)

12. Wyświetlane są wyniki podobne do następującego przykładu:

    ![Przejrzyj wynik](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable12.png)

13. W tabeli źródłowej można tworzyć nowe wiersze. Oto przykładowy język SQL do tworzenia nowych wierszy:

    ```sql
            INSERT INTO data_source_table
            VALUES (10, 'newdata','9/10/2017 2:23:00 AM')

            INSERT INTO data_source_table
            VALUES (11, 'newdata','9/11/2017 9:01:00 AM')
    ```

14. Aby ponownie uruchomić potok, wybierz pozycję **Debuguj**, wprowadź **Parametry**, a następnie wybierz pozycję **Zakończ**.

    Zobaczysz, że tylko nowe wiersze zostały skopiowane do lokalizacji docelowej.

15. Obowiązkowe W przypadku wybrania usługi Azure Synapse Analytics (dawniej SQL DW) jako miejsca docelowego danych należy również udostępnić połączenie z magazynem obiektów blob platformy Azure na potrzeby przemieszczania, które jest wymagane przez usługę Azure Synapse Analytics (dawniej SQL Data Warehouse). Szablon spowoduje wygenerowanie ścieżki kontenera. Po uruchomieniu potoku Sprawdź, czy kontener został utworzony w usłudze BLOB Storage.
    
    ![Skonfiguruj bazę](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable15.png)
    
## <a name="next-steps"></a>Następne kroki

- [Kopiowanie masowe z bazy danych przy użyciu tabeli formantów z Azure Data Factory](solution-template-bulk-copy-with-control-table.md)
- [Kopiowanie plików z wielu kontenerów za pomocą Azure Data Factory](solution-template-copy-files-multiple-containers.md)
