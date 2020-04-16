---
title: Kopia delta z bazy danych przy użyciu tabeli formantów
description: Dowiedz się, jak używać szablonu rozwiązania do stopniowego kopiowania nowych lub zaktualizowanych wierszy tylko z bazy danych za pomocą usługi Azure Data Factory.
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
ms.openlocfilehash: 01a6d796a9a8306da5bb707111b07786136a66cc
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414784"
---
# <a name="delta-copy-from-a-database-with-a-control-table"></a>Kopia delta z bazy danych z tabelą kontrolną
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym artykule opisano szablon, który jest dostępny do przyrostowego ładowania nowych lub zaktualizowanych wierszy z tabeli bazy danych na platformę Azure przy użyciu tabeli kontroli zewnętrznej, która przechowuje wartość znaku wodnego o wysokiej wartości.

Ten szablon wymaga, aby schemat źródłowej bazy danych zawierał kolumnę sygnatury czasowej lub klucz przyrostowy w celu zidentyfikowania nowych lub zaktualizowanych wierszy.

>[!NOTE]
> Jeśli masz kolumnę sygnatury czasowej w źródłowej bazie danych do identyfikowania nowych lub zaktualizowanych wierszy, ale nie chcesz tworzyć zewnętrznej tabeli sterowania do użycia do kopiowania delta, możesz zamiast tego użyć [narzędzia Azure Data Factory Copy Data,](copy-data-tool.md) aby uzyskać potok. To narzędzie używa czasu zaplanowanego wyzwalacza jako zmiennej do odczytu nowych wierszy ze źródłowej bazy danych.

## <a name="about-this-solution-template"></a>Informacje o tym szablonie rozwiązania

Ten szablon najpierw pobiera starą wartość znaku wodnego i porównuje ją z bieżącą wartością znaku wodnego. Następnie kopiuje tylko zmiany z źródłowej bazy danych, na podstawie porównania między dwiema wartościami znaku wodnego. Na koniec przechowuje nową wartość wysokiego znaku wodnego do zewnętrznej tabeli sterowania dla ładowania danych delta następnym razem.

Szablon zawiera cztery działania:
- **Wyszukiwanie** pobiera starą wartość wysokiego znaku wodnego, która jest przechowywana w tabeli kontroli zewnętrznej.
- Inne działanie **odnośnika** pobiera bieżącą wartość znaku wodnego o wysokiej wartości z źródłowej bazy danych.
- **Kopiowanie** tylko zmiany ze źródłowej bazy danych do magazynu docelowego. Kwerenda identyfikująca zmiany w źródłowej bazie danych jest podobna do "SELECT * FROM Data_Source_Table WHERE TIMESTAMP_Column > "last high-watermark" i TIMESTAMP_Column <= "current high-watermark".
- **SqlServerStoredProcedure** zapisuje bieżącą wartość znaku wodnego wysokiego poziomu wody w zewnętrznej tabeli sterowania dla kopii delta następnym razem.

Szablon definiuje następujące parametry:
- *Data_Source_Table_Name* jest tabela w źródłowej bazie danych, z której chcesz załadować dane.
- *Data_Source_WaterMarkColumn* jest nazwą kolumny w tabeli źródłowej, która jest używana do identyfikowania nowych lub zaktualizowanych wierszy. Typ tej kolumny jest zazwyczaj *datetime*, *INT*lub podobne.
- *Data_Destination_Container* jest ścieżką główną miejsca, do którego dane są kopiowane w magazynie docelowym.
- *Data_Destination_Directory* jest ścieżką katalogu pod katalogiem głównym miejsca, do którego dane są kopiowane w magazynie docelowym.
- *Data_Destination_Table_Name* jest miejscem, do którego dane są kopiowane w magazynie docelowym (dotyczy gdy "Azure Synapse Analytics (dawniej SQL DW)" jest zaznaczone jako miejsce docelowe danych).
- *Data_Destination_Folder_Path* jest miejscem, do którego dane są kopiowane w magazynie docelowym (dotyczy wybrania jako miejsca docelowego usługi Data Storage gen1.
- *Control_Table_Table_Name* jest tabelą kontroli zewnętrznej, która przechowuje wartość znaku wodnego o wysokiej wartości.
- *Control_Table_Column_Name* jest kolumną w tabeli formantu zewnętrznego, która przechowuje wartość znaku wodnego o wysokiej wartości.

## <a name="how-to-use-this-solution-template"></a>Jak korzystać z tego szablonu rozwiązania

1. Zapoznaj się z tabelą źródłową, którą chcesz załadować, i zdefiniuj kolumnę wysokiego znaku wodnego, która może służyć do identyfikowania nowych lub zaktualizowanych wierszy. Typ tej kolumny może być *datetime*, *INT*lub podobny. Wartość tej kolumny wzrasta wraz z dodaniem nowych wierszy. Z poniższej tabeli źródłowej przykładowej (data_source_table) możemy użyć kolumny *LastModifytime* jako kolumny wysokiego znaku wodnego.

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
    
2. Utwórz tabelę sterowania w programie SQL Server lub usłudze Azure SQL Database, aby przechowywać wartość znaku wodnego o wysokiej wartości podczas ładowania danych różnicowych. W poniższym przykładzie nazwa tabeli kontrolnej jest *znakiem wodnym*. W tej tabeli *WatermarkValue* jest kolumną, która przechowuje wartość znaku wodnego, a jej typem jest *datetime*.

    ```sql
            create table watermarktable
            (
            WatermarkValue datetime,
            );
            INSERT INTO watermarktable
            VALUES ('1/1/2010 12:00:00 AM')
    ```
    
3. Utwórz procedurę składowaną w tym samym wystąpieniu programu SQL Server lub Azure SQL Database, które zostało użyte do utworzenia tabeli sterowania. Procedura składowana służy do zapisywania nowej wartości wysokiego znaku wodnego w tabeli kontroli zewnętrznej w celu następnego ładowania danych różnicowych.

    ```sql
            CREATE PROCEDURE update_watermark @LastModifiedtime datetime
            AS

            BEGIN

                UPDATE watermarktable
                SET [WatermarkValue] = @LastModifiedtime 

            END
    ```
    
4. Przejdź do **kopii delta z szablonu bazy danych.** Utwórz **nowe** połączenie ze źródłową bazą danych, z której chcesz skopiować dane.

    ![Tworzenie nowego połączenia z tabelą źródłową](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable4.png)

5. Utwórz **nowe** połączenie z docelowym magazynem danych, do którego chcesz skopiować dane.

    ![Tworzenie nowego połączenia z tabelą docelową](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable5.png)

6. Utwórz **nowe** połączenie z tabelą formantów zewnętrznych i procedurą składowaną utworzoną w krokach 2 i 3.

    ![Tworzenie nowego połączenia z magazynem danych tabeli sterowania](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable6.png)

7. Wybierz pozycję **Użyj tego szablonu**.
    
8. Zostanie wyświetlony dostępny potok, jak pokazano w poniższym przykładzie:
  
    ![Przejrzyj potok](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable8.png)

9. Wybierz **opcję Procedura składowana**. Dla **nazwy procedury składowanej**wybierz **[dbo].[ update_watermark]**. Wybierz **polecenie Importuj parametr**, a następnie wybierz pozycję Dodaj zawartość **dynamiczną**.  

    ![Ustawianie działania procedury składowanej](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable9.png)  

10. Napisz zawartość ** \@{activity('LookupCurrentWaterMark').output.firstRow.NewWatermarkValue}**, a następnie wybierz pozycję **Zakończ**.  

    ![Zapisz zawartość parametrów procedury składowanej](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable10.png)       
     
11. Wybierz **debugowanie**, wprowadź **parametry**, a następnie wybierz **zakończ**.

    ![Wybierz **Debugowanie**](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable11.png)

12. Wyświetlane są wyniki podobne do poniższego przykładu:

    ![Przejrzyj wynik](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable12.png)

13. W tabeli źródłej można tworzyć nowe wiersze. Oto przykładowy język SQL do tworzenia nowych wierszy:

    ```sql
            INSERT INTO data_source_table
            VALUES (10, 'newdata','9/10/2017 2:23:00 AM')

            INSERT INTO data_source_table
            VALUES (11, 'newdata','9/11/2017 9:01:00 AM')
    ```

14. Aby ponownie uruchomić potok, wybierz **debugowanie**, wprowadź **parametry**, a następnie wybierz pozycję **Zakończ**.

    Zobaczysz, że tylko nowe wiersze zostały skopiowane do miejsca docelowego.

15. (Opcjonalnie:) Jeśli wybierzesz usługę Azure Synapse Analytics (dawniej SQL DW) jako miejsce docelowe danych, należy również zapewnić połączenie z magazynem obiektów blob platformy Azure w celu przeprowadzenia przemieszczania, co jest wymagane przez polybase magazynu danych SQL. Szablon wygeneruje ścieżkę kontenera dla Ciebie. Po uruchomieniu potoku sprawdź, czy kontener został utworzony w magazynie obiektów Blob.
    
    ![Konfigurowanie bazy polybase](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable15.png)
    
## <a name="next-steps"></a>Następne kroki

- [Kopiowanie zbiorcze z bazy danych przy użyciu tabeli sterowania z usługą Azure Data Factory](solution-template-bulk-copy-with-control-table.md)
- [Kopiowanie plików z wielu kontenerów za pomocą usługi Azure Data Factory](solution-template-copy-files-multiple-containers.md)
