---
title: Migrowanie danych z usługi Amazon S3 do Azure Data Lake Storage Gen2
description: Dowiedz się, jak za pomocą szablonu rozwiązania przeprowadzić migrację danych z usługi Amazon S3 przy użyciu zewnętrznej tabeli formantów do przechowywania listy partycji na AWS S3 z Azure Data Factory.
services: data-factory
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/07/2019
ms.openlocfilehash: e25299c2ce5d31da8f3caa5b02ab8def816b31ee
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91398224"
---
# <a name="migrate-data-from-amazon-s3-to-azure-data-lake-storage-gen2"></a>Migrowanie danych z usługi Amazon S3 do Azure Data Lake Storage Gen2

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Za pomocą szablonów można migrować petabajtów danych składające się z setek milionów plików z usługi Amazon S3 do Azure Data Lake Storage Gen2. 

 > [!NOTE]
 > Jeśli chcesz skopiować małe ilości danych z AWS S3 na platformę Azure (na przykład mniej niż 10 TB), jest to wydajniejsze i łatwe w użyciu [narzędzie Azure Data Factory kopiowanie danych](copy-data-tool.md). Szablon, który jest opisany w tym artykule, jest większy niż to, czego potrzebujesz.

## <a name="about-the-solution-templates"></a>Informacje o szablonach rozwiązań

Partycja danych jest zalecana szczególnie w przypadku migrowania więcej niż 10 TB danych. Aby podzielić dane na partycje, Skorzystaj z ustawienia "prefiks" w celu odfiltrowania folderów i plików w usłudze Amazon S3 według nazwy, a następnie każde zadanie kopiowania ADF może skopiować jedną partycję w danym momencie. Aby zapewnić lepszą przepływność, można uruchomić wiele zadań kopiowania ADF współbieżnie.

Migracja danych zwykle wymaga jednorazowej, historycznej migracji danych oraz okresowe synchronizowanie zmian z AWS S3 na platformę Azure. Poniżej znajdują się dwa szablony, w których jeden szablon obejmuje jednokrotną migrację danych historycznych, a inny szablon obejmuje synchronizowanie zmian z AWS S3 na platformie Azure.

### <a name="for-the-template-to-migrate-historical-data-from-amazon-s3-to-azure-data-lake-storage-gen2"></a>Szablon służący do migrowania danych historycznych z usługi Amazon S3 do Azure Data Lake Storage Gen2

Ten szablon (*Nazwa szablonu: Migruj dane historyczne z AWS S3 do Azure Data Lake Storage Gen2*) zakłada, że Zapisano listę partycji w zewnętrznej tabeli formantów w Azure SQL Database. Dlatego użyje działania *Lookup* do pobrania listy partycji z tabeli formantów zewnętrznych, iteracji na każdą partycję, a każde zadanie kopiowania ADF kopiuje jedną partycję w danym momencie. Po zakończeniu każdego zadania kopiowania korzysta ono z działania *procedury składowanej* w celu zaktualizowania stanu kopiowania każdej partycji w tabeli kontroli.

Szablon zawiera pięć działań:
- Funkcja **Lookup** pobiera partycje, które nie zostały skopiowane do Azure Data Lake Storage Gen2 z tabeli formantów zewnętrznych. Nazwa tabeli jest *s3_partition_control_table* a zapytanie do ładowania danych z tabeli to *"SELECT PartitionPrefix from S3_partition_control_table, gdzie SuccessOrFailure = 0"*.
- Instrukcja **foreach** pobiera listę partycji z działania *Lookup* i iteruje każdą partycję do działania *TriggerCopy* . Można ustawić *batchCount* do uruchamiania wielu zadań kopiowania ADF współbieżnie. W tym szablonie ustawimy 2.
- **ExecutePipeline** wykonuje potok *CopyFolderPartitionFromS3* . Należy utworzyć kolejny potok, aby każde zadanie kopiowania skopiował partycję, ponieważ ułatwia ponowne uruchomienie zadania kopiowania zakończonego niepowodzeniem w celu ponownego załadowania określonej partycji z AWS S3. Nie wpłynie to na wszystkie inne zadania kopiowania ładujące inne partycje.
- **Kopiuj** kopiuje każdą partycję z AWS S3 do Azure Data Lake Storage Gen2.
- **SqlServerStoredProcedure** zaktualizować stan kopiowania każdej partycji w tabeli kontroli.

Szablon zawiera dwa parametry:
- **AWS_S3_bucketName** to nazwa zasobnika w AWS S3, w którym chcesz migrować dane. Jeśli chcesz przeprowadzić migrację danych z wielu przedziałów w AWS S3, możesz dodać jedną kolumnę w tabeli formantów zewnętrznych do przechowywania nazwy zasobnika dla każdej partycji, a także zaktualizować potok, aby odpowiednio pobrać dane z tej kolumny.
- **Azure_Storage_fileSystem** jest nazwą systemu plików na Azure Data Lake Storage Gen2, do której chcesz migrować dane.

### <a name="for-the-template-to-copy-changed-files-only-from-amazon-s3-to-azure-data-lake-storage-gen2"></a>Szablon do kopiowania zmienionych plików tylko z usługi Amazon S3 do Azure Data Lake Storage Gen2

Ten szablon (*Nazwa szablonu: Kopiuj dane różnicowe z AWS S3 do Azure Data Lake Storage Gen2*) używa LastModifiedTime każdego pliku do kopiowania nowych lub zaktualizowanych plików tylko z AWS S3 na platformę Azure. Należy pamiętać, że jeśli pliki lub foldery zostały już podzielone na partycje za pomocą timeslice informacji w ramach nazwy pliku lub folderu w AWS S3 (na przykład/yyyy/mm/dd/file.csv), możesz przejść do tego [samouczka](tutorial-incremental-copy-partitioned-file-name-copy-data-tool.md) , aby uzyskać bardziej wydajne podejście do przyrostowego ładowania nowych plików. W tym szablonie przyjęto założenie, że lista partycji została zapisywana w tabeli formantów zewnętrznych w Azure SQL Database. Dlatego użyje działania *Lookup* do pobrania listy partycji z tabeli formantów zewnętrznych, iteracji na każdą partycję, a każde zadanie kopiowania ADF kopiuje jedną partycję w danym momencie. Gdy każde zadanie kopiowania rozpocznie kopiowanie plików z AWS S3, opiera się on na właściwości LastModifiedTime, aby identyfikować i kopiować tylko nowe lub zaktualizowane pliki. Po zakończeniu każdego zadania kopiowania korzysta ono z działania *procedury składowanej* w celu zaktualizowania stanu kopiowania każdej partycji w tabeli kontroli.

Szablon zawiera siedem działań:
- Funkcja **Lookup** pobiera partycje z tabeli formantów zewnętrznych. Nazwa tabeli jest *s3_partition_delta_control_table* a zapytanie do ładowania danych z tabeli to *"Select distinct PartitionPrefix from s3_partition_delta_control_table"*.
- Instrukcja **foreach** pobiera listę partycji z działania *Lookup* i iteruje każdą partycję do działania *TriggerDeltaCopy* . Można ustawić *batchCount* do uruchamiania wielu zadań kopiowania ADF współbieżnie. W tym szablonie ustawimy 2.
- **ExecutePipeline** wykonuje potok *DeltaCopyFolderPartitionFromS3* . Należy utworzyć kolejny potok, aby każde zadanie kopiowania skopiował partycję, ponieważ ułatwia ponowne uruchomienie zadania kopiowania zakończonego niepowodzeniem w celu ponownego załadowania określonej partycji z AWS S3. Nie wpłynie to na wszystkie inne zadania kopiowania ładujące inne partycje.
- Funkcja **Lookup** Pobiera czas wykonywania zadania ostatniego kopiowania z tabeli formantów zewnętrznych, dzięki czemu nowe lub zaktualizowane pliki można zidentyfikować za pośrednictwem LastModifiedTime. Nazwa tabeli jest *s3_partition_delta_control_table* a zapytanie do ładowania danych z tabeli to *"Select Max (JobRunTime) as LastModifiedTime from s3_partition_delta_control_table WHERE PartitionPrefix =" @ {potok (). Parameters. prefixStr} "i SuccessOrFailure = 1"*.
- **Kopiuj** kopiuje nowe lub zmienione pliki tylko dla każdej partycji z AWS S3 do Azure Data Lake Storage Gen2. Właściwość *modifiedDatetimeStart* jest ustawiona na ostatni czas wykonywania zadania kopiowania. Właściwość *modifiedDatetimeEnd* jest ustawiona na bieżącą godzinę wykonywania zadania kopiowania. Należy pamiętać, że czas jest stosowany do strefy czasowej UTC.
- **SqlServerStoredProcedure** Aktualizuj stan kopiowania każdej partycji i skopiuj czas wykonywania w tabeli kontroli po jej pomyślnym zakończeniu. Kolumna SuccessOrFailure ma ustawioną wartość 1.
- **SqlServerStoredProcedure** aktualizowanie stanu kopiowania każdej partycji i kopiowanie czasu wykonywania w tabeli kontroli, gdy nie powiedzie się. Kolumna SuccessOrFailure jest ustawiona na 0.

Szablon zawiera dwa parametry:
- **AWS_S3_bucketName** to nazwa zasobnika w AWS S3, w którym chcesz migrować dane. Jeśli chcesz przeprowadzić migrację danych z wielu przedziałów w AWS S3, możesz dodać jedną kolumnę w tabeli formantów zewnętrznych do przechowywania nazwy zasobnika dla każdej partycji, a także zaktualizować potok, aby odpowiednio pobrać dane z tej kolumny.
- **Azure_Storage_fileSystem** jest nazwą systemu plików na Azure Data Lake Storage Gen2, do której chcesz migrować dane.

## <a name="how-to-use-these-two-solution-templates"></a>Jak używać tych dwóch szablonów rozwiązań

### <a name="for-the-template-to-migrate-historical-data-from-amazon-s3-to-azure-data-lake-storage-gen2"></a>Szablon służący do migrowania danych historycznych z usługi Amazon S3 do Azure Data Lake Storage Gen2 

1. Utwórz tabelę formantów w Azure SQL Database, aby zapisać listę partycji AWS S3. 

    > [!NOTE]
    > Nazwa tabeli jest s3_partition_control_table.
    > Schemat tabeli formantów to PartitionPrefix i SuccessOrFailure, gdzie PartitionPrefix jest ustawieniem prefiksu w S3 do filtrowania folderów i plików w usłudze Amazon S3 według nazwy, a SuccessOrFailure jest stanem kopiowania każdej partycji: 0 oznacza, że ta partycja nie została skopiowana na platformę Azure i 1 oznacza, że ta partycja została pomyślnie skopiowana na platformę Azure.
    > W tabeli formantów zdefiniowano 5 partycji i domyślny stan kopiowania każdej partycji wynosi 0.

    ```sql
    CREATE TABLE [dbo].[s3_partition_control_table](
        [PartitionPrefix] [varchar](255) NULL,
        [SuccessOrFailure] [bit] NULL
    )

    INSERT INTO s3_partition_control_table (PartitionPrefix, SuccessOrFailure)
    VALUES
    ('a', 0),
    ('b', 0),
    ('c', 0),
    ('d', 0),
    ('e', 0);
    ```

2. Utwórz procedurę przechowywaną na tym samym Azure SQL Database tabeli formantów. 

    > [!NOTE]
    > Nazwa procedury składowanej jest sp_update_partition_success. Będzie on wywoływany przez działanie SqlServerStoredProcedure w potoku ADF.

    ```sql
    CREATE PROCEDURE [dbo].[sp_update_partition_success] @PartPrefix varchar(255)
    AS
    BEGIN
    
        UPDATE s3_partition_control_table
        SET [SuccessOrFailure] = 1 WHERE [PartitionPrefix] = @PartPrefix
    END
    GO
    ```

3. Przejdź do szablonu " **Migrowanie danych historycznych z AWS S3 do Azure Data Lake Storage Gen2** . Wprowadź połączenia z tabelą formantów zewnętrznych, AWS S3 jako magazyny danych i Azure Data Lake Storage Gen2 jako magazyn docelowy. Należy pamiętać, że zewnętrzna tabela formantów i procedura składowana odnoszą się do tego samego połączenia.

    ![Zrzut ekranu pokazujący dane historyczne migracji z AWS S3 do szablonu Azure Data Lake Storage Gen2.](media/solution-template-migration-s3-azure/historical-migration-s3-azure1.png)

4. Wybierz pozycję **Użyj tego szablonu**.

    ![Zrzut ekranu, który podświetla przycisk Użyj tego szablonu.](media/solution-template-migration-s3-azure/historical-migration-s3-azure2.png)
    
5. Zobaczysz 2 potoki i 3 zestawy danych, jak pokazano w następującym przykładzie:

    ![Zrzut ekranu pokazujący dwa potoki i trzy zestawy danych, które zostały utworzone przy użyciu szablonu.](media/solution-template-migration-s3-azure/historical-migration-s3-azure3.png)

6. Wybierz pozycję **Debuguj**, wprowadź **Parametry**, a następnie wybierz pozycję **Zakończ**.

    ![Zrzut ekranu pokazujący, gdzie wybrać opcję Debuguj i wprowadzić parametry przed wybraniem opcji Zakończ.](media/solution-template-migration-s3-azure/historical-migration-s3-azure4.png)

7. Zobaczysz wyniki podobne do poniższego przykładu:

    ![Zrzut ekranu pokazujący zwrócone wyniki.](media/solution-template-migration-s3-azure/historical-migration-s3-azure5.png)


### <a name="for-the-template-to-copy-changed-files-only-from-amazon-s3-to-azure-data-lake-storage-gen2"></a>Szablon do kopiowania zmienionych plików tylko z usługi Amazon S3 do Azure Data Lake Storage Gen2

1. Utwórz tabelę formantów w Azure SQL Database, aby zapisać listę partycji AWS S3. 

    > [!NOTE]
    > Nazwa tabeli jest s3_partition_delta_control_table.
    > Schemat tabeli formantów to PartitionPrefix, JobRunTime i SuccessOrFailure, gdzie PartitionPrefix jest ustawieniem prefiksu w S3 do filtrowania folderów i plików w usłudze Amazon S3 według nazwy, JobRunTime jest wartością DateTime, gdy zadania kopiowania są uruchamiane, a SuccessOrFailure to stan kopiowania każdej partycji: 0 oznacza, że ta partycja nie została skopiowana na platformę Azure i 1 oznacza pomyślne skopiowanie tej partycji na platformę Azure.
    > W tabeli kontrolek zdefiniowano 5 partycji. Wartość domyślna parametru JobRunTime to czas, po którym rozpocznie się jednokrotne Migrowanie danych historycznych. Działanie kopiowania ADF skopiuje pliki w AWS S3, które zostały ostatnio zmodyfikowane po tym czasie. Domyślnym stanem kopiowania każdej partycji jest 1.

    ```sql
    CREATE TABLE [dbo].[s3_partition_delta_control_table](
        [PartitionPrefix] [varchar](255) NULL,
        [JobRunTime] [datetime] NULL,
        [SuccessOrFailure] [bit] NULL
        )

    INSERT INTO s3_partition_delta_control_table (PartitionPrefix, JobRunTime, SuccessOrFailure)
    VALUES
    ('a','1/1/2019 12:00:00 AM',1),
    ('b','1/1/2019 12:00:00 AM',1),
    ('c','1/1/2019 12:00:00 AM',1),
    ('d','1/1/2019 12:00:00 AM',1),
    ('e','1/1/2019 12:00:00 AM',1);
    ```

2. Utwórz procedurę przechowywaną na tym samym Azure SQL Database tabeli formantów. 

    > [!NOTE]
    > Nazwa procedury składowanej jest sp_insert_partition_JobRunTime_success. Będzie on wywoływany przez działanie SqlServerStoredProcedure w potoku ADF.

    ```sql
        CREATE PROCEDURE [dbo].[sp_insert_partition_JobRunTime_success] @PartPrefix varchar(255), @JobRunTime datetime, @SuccessOrFailure bit
        AS
        BEGIN
            INSERT INTO s3_partition_delta_control_table (PartitionPrefix, JobRunTime, SuccessOrFailure)
            VALUES
            (@PartPrefix,@JobRunTime,@SuccessOrFailure)
        END
        GO
    ```


3. Przejdź do szablonu **Kopiuj dane różnicowe z AWS S3 do Azure Data Lake Storage Gen2** . Wprowadź połączenia z tabelą formantów zewnętrznych, AWS S3 jako magazyny danych i Azure Data Lake Storage Gen2 jako magazyn docelowy. Należy pamiętać, że zewnętrzna tabela formantów i procedura składowana odnoszą się do tego samego połączenia.

    ![Tworzenie nowego połączenia](media/solution-template-migration-s3-azure/delta-migration-s3-azure1.png)

4. Wybierz pozycję **Użyj tego szablonu**.

    ![Użyj tego szablonu](media/solution-template-migration-s3-azure/delta-migration-s3-azure2.png)
    
5. Zobaczysz 2 potoki i 3 zestawy danych, jak pokazano w następującym przykładzie:

    ![Przeglądanie potoku](media/solution-template-migration-s3-azure/delta-migration-s3-azure3.png)

6. Wybierz pozycję **Debuguj**, wprowadź **Parametry**, a następnie wybierz pozycję **Zakończ**.

    ![Kliknij pozycję * * Debuguj * *](media/solution-template-migration-s3-azure/delta-migration-s3-azure4.png)

7. Zobaczysz wyniki podobne do poniższego przykładu:

    ![Przejrzyj wynik](media/solution-template-migration-s3-azure/delta-migration-s3-azure5.png)

8. Wyniki z tabeli formantów można także sprawdzić za pomocą zapytania *"select * from s3_partition_delta_control_table"*, ale dane wyjściowe są podobne do następującego przykładu:

    ![Zrzut ekranu, który wyświetla wyniki z tabeli formantów po uruchomieniu zapytania.](media/solution-template-migration-s3-azure/delta-migration-s3-azure6.png)
    
## <a name="next-steps"></a>Następne kroki

- [Kopiowanie plików z wielu kontenerów](solution-template-copy-files-multiple-containers.md)
- [Przenoszenie plików](solution-template-move-files.md)