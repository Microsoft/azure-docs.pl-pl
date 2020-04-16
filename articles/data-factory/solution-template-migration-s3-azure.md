---
title: Migrowanie danych z usługi Amazon S3 do usługi Azure Data Lake Storage Gen2
description: Dowiedz się, jak używać szablonu rozwiązania do migracji danych z amazon S3 przy użyciu zewnętrznej tabeli sterowania do przechowywania listy partycji w Usłudze AWS S3 w usłudze Azure Data Factory.
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
ms.openlocfilehash: 23d799f84cb3ac3ca911a5669041b0a25394a7ff
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414772"
---
# <a name="migrate-data-from-amazon-s3-to-azure-data-lake-storage-gen2"></a>Migrowanie danych z usługi Amazon S3 do usługi Azure Data Lake Storage Gen2

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Szablony można migrować petabajtami danych składających się z setek milionów plików z amazon s3 do usługi Azure Data Lake Storage Gen2. 

 > [!NOTE]
 > Jeśli chcesz skopiować mały wolumin danych z usługi AWS S3 na platformę Azure (na przykład mniej niż 10 TB), jest to bardziej wydajne i łatwe w użyciu [narzędzie Azure Data Factory Copy Data](copy-data-tool.md). Szablon opisany w tym artykule jest więcej niż to, czego potrzebujesz.

## <a name="about-the-solution-templates"></a>Szablony rozwiązań — informacje

Partycja danych jest zalecane szczególnie podczas migracji więcej niż 10 TB danych. Aby podzielić dane na partycje, wykorzystaj ustawienie "prefiks", aby filtrować foldery i pliki na Amazon S3 według nazwy, a następnie każde zadanie kopiowania ADF może kopiować jedną partycję naraz. Można uruchomić wiele zadań kopiowania podajnika ADF jednocześnie w celu uzyskania lepszej przepływności.

Migracja danych zwykle wymaga jednorazowej migracji danych historycznych oraz okresowej synchronizacji zmian z usługi AWS S3 na platformę Azure. Poniżej znajdują się dwa szablony, w których jeden szablon obejmuje jednorazową migrację danych historycznych, a drugi szablon obejmuje synchronizowanie zmian z usługi AWS S3 na platformę Azure.

### <a name="for-the-template-to-migrate-historical-data-from-amazon-s3-to-azure-data-lake-storage-gen2"></a>Aby szablon migrować dane historyczne z amazon S3 do usługi Azure Data Lake Storage Gen2

Ten szablon *(nazwa szablonu: migrowanie danych historycznych z usługi AWS S3 do usługi Azure Data Lake Storage Gen2)* zakłada, że została napisana lista partycji w tabeli kontroli zewnętrznej w usłudze Azure SQL Database. Dlatego użyje działania *odnośników,* aby pobrać listę partycji z tabeli kontroli zewnętrznej, iterować na każdej partycji i sprawić, że każde zadanie kopiowania kopii adf po jednej partycji naraz. Po zakończeniu dowolnego zadania kopiowania używa działania *procedura składowana,* aby zaktualizować stan kopiowania każdej partycji w tabeli sterowania.

Szablon zawiera pięć działań:
- **Wyszukiwanie** pobiera partycje, które nie zostały skopiowane do usługi Azure Data Lake Storage Gen2 z zewnętrznej tabeli sterowania. Nazwa tabeli jest *s3_partition_control_table,* a kwerenda do ładowania danych z tabeli to *"SELECT PartitionPrefix FROM s3_partition_control_table WHERE SuccessOrFailure = 0"*.
- **ForEach** pobiera listę partycji z *działania odnośnika* i iteruje każdą partycję do *triggercopy* działania. Można ustawić *batchCount* do uruchamiania wielu zadań kopiowania podajnika ADF jednocześnie. Ustawiliśmy 2 w tym szablonie.
- **ExecutePipeline** wykonuje potok *CopyFolderPartitionFromS3.* Powodem, dla którego tworzymy inny potok, aby każda kopia zadania kopiowania partycji jest, ponieważ ułatwi ponowne uruchomienie zadania kopiowania nie powiodło się ponownie, aby ponownie załadować tę określoną partycję z AWS S3. Wszystkie inne zadania kopiowania ładowania innych partycji nie będzie miało wpływu.
- **Kopiuj** każdą partycję z usługi AWS S3 do usługi Azure Data Lake Storage Gen2.
- **SqlServerStoredProcedure** zaktualizować stan kopiowania każdej partycji w tabeli kontroli.

Szablon zawiera dwa parametry:
- **AWS_S3_bucketName** to nazwa zasobnika w AWS S3, z którego chcesz przeprowadzić migrację danych. Jeśli chcesz przeprowadzić migrację danych z wielu zasobników w systemie AWS S3, możesz dodać jeszcze jedną kolumnę w tabeli sterowania zewnętrznego, aby zapisać nazwę zasobnika dla każdej partycji, a także zaktualizować potok, aby odpowiednio pobrać dane z tej kolumny.
- **Azure_Storage_fileSystem** jest nazwą fileSystem w usłudze Azure Data Lake Storage Gen2, do której chcesz przeprowadzić migrację danych.

### <a name="for-the-template-to-copy-changed-files-only-from-amazon-s3-to-azure-data-lake-storage-gen2"></a>Aby szablon skopiował zmienione pliki tylko z Amazon S3 na Azure Data Lake Storage Gen2

Ten szablon *(nazwa szablonu: skopiować dane delta z AWS S3 do usługi Azure Data Lake Storage Gen2)* używa LastModifiedTime każdego pliku, aby skopiować nowe lub zaktualizowane pliki tylko z AWS S3 na platformę Azure. Należy pamiętać, jeśli pliki lub foldery zostały już czas podzielony z informacjami timeslice jako część nazwy pliku lub folderu na AWS S3 (na przykład /yyyy/mm/dd/file.csv), możesz przejść do tego [samouczka,](tutorial-incremental-copy-partitioned-file-name-copy-data-tool.md) aby uzyskać bardziej wydajne podejście do przyrostowego ładowania nowych plików. Ten szablon zakłada, że została napisana lista partycji w tabeli kontroli zewnętrznej w usłudze Azure SQL Database. Dlatego użyje działania *odnośników,* aby pobrać listę partycji z tabeli kontroli zewnętrznej, iterować na każdej partycji i sprawić, że każde zadanie kopiowania kopii adf po jednej partycji naraz. Podczas każdy kopiować zadanie wzdrygnąć się wobec kopiować ten akta z AWS S3, ono opiera się u LastModifiedTime majątek wobec zidentyfikować i kopiować ten nowy albo zaktualizować akta tylko. Po zakończeniu dowolnego zadania kopiowania używa działania *procedura składowana,* aby zaktualizować stan kopiowania każdej partycji w tabeli sterowania.

Szablon zawiera siedem działań:
- **Wyszukiwanie** pobiera partycje z zewnętrznej tabeli sterowania. Nazwa tabeli jest *s3_partition_delta_control_table,* a kwerenda do ładowania danych z tabeli to *"wybierz odrębny PartitionPrefix z s3_partition_delta_control_table"*.
- **ForEach** pobiera listę partycji z działania *odnośnika* i iteruje każdą partycję do *triggerdeltacopy* działania. Można ustawić *batchCount* do uruchamiania wielu zadań kopiowania podajnika ADF jednocześnie. Ustawiliśmy 2 w tym szablonie.
- **ExecutePipeline** wykonuje potok *DeltaCopyFolderPartitionFromS3.* Powodem, dla którego tworzymy inny potok, aby każda kopia zadania kopiowania partycji jest, ponieważ ułatwi ponowne uruchomienie zadania kopiowania nie powiodło się ponownie, aby ponownie załadować tę określoną partycję z AWS S3. Wszystkie inne zadania kopiowania ładowania innych partycji nie będzie miało wpływu.
- **Wyszukiwanie** pobiera czas wykonywania zadania ostatniej kopii z tabeli kontroli zewnętrznej, dzięki czemu nowe lub zaktualizowane pliki mogą być identyfikowane za pośrednictwem LastModifiedTime. Nazwa tabeli jest *s3_partition_delta_control_table,* a kwerenda do ładowania danych z tabeli to *"select max(JobRunTime) as LastModifiedTime from s3_partition_delta_control_table where PartitionPrefix = '@{pipeline().parameters.prefixStr}' i SuccessOrFailure = 1"*.
- **Kopiowanie** kopiuje nowe lub zmienione pliki tylko dla każdej partycji z AWS S3 do usługi Azure Data Lake Storage Gen2. Właściwość *modifiedDatetimeStart* jest ustawiona na czas wykonywania zadania ostatniej kopii. Właściwość *modifiedDatetimeEnd* jest ustawiona na bieżący czas wykonywania zadania kopiowania. Należy pamiętać, że czas jest stosowany do strefy czasowej UTC.
- **SqlServerStoredProcedure** zaktualizować stan kopiowania każdej partycji i skopiować czas wykonywania w tabeli sterowania, gdy powiedzie się. Kolumna SuccessOrFailure jest ustawiona na 1.
- **SqlServerStoredProcedure** zaktualizować stan kopiowania każdej partycji i skopiować czas wykonywania w tabeli sterowania, gdy nie powiedzie się. Kolumna SuccessOrFailure jest ustawiona na 0.

Szablon zawiera dwa parametry:
- **AWS_S3_bucketName** to nazwa zasobnika w AWS S3, z którego chcesz przeprowadzić migrację danych. Jeśli chcesz przeprowadzić migrację danych z wielu zasobników w systemie AWS S3, możesz dodać jeszcze jedną kolumnę w tabeli sterowania zewnętrznego, aby zapisać nazwę zasobnika dla każdej partycji, a także zaktualizować potok, aby odpowiednio pobrać dane z tej kolumny.
- **Azure_Storage_fileSystem** jest nazwą fileSystem w usłudze Azure Data Lake Storage Gen2, do której chcesz przeprowadzić migrację danych.

## <a name="how-to-use-these-two-solution-templates"></a>Jak korzystać z tych dwóch szablonów rozwiązań

### <a name="for-the-template-to-migrate-historical-data-from-amazon-s3-to-azure-data-lake-storage-gen2"></a>Aby szablon migrować dane historyczne z amazon S3 do usługi Azure Data Lake Storage Gen2 

1. Utwórz tabelę formantów w usłudze Azure SQL Database, aby przechowywać listę partycji AWS S3. 

    > [!NOTE]
    > Nazwa tabeli jest s3_partition_control_table.
    > Schemat tabeli sterowania jest PartitionPrefix i SuccessOrFailure, gdzie PartitionPrefix jest ustawienie prefiksu w S3 do filtrowania folderów i plików w Amazon S3 według nazwy i SuccessOrFailure jest stan kopiowania każdej partycji: 0 oznacza, że ta partycja nie została skopiowana na platformę Azure i 1 oznacza, że ta partycja została pomyślnie skopiowana do platformy Azure.
    > Istnieje 5 partycji zdefiniowanych w tabeli sterowania i domyślny stan kopiowania każdej partycji jest 0.

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

2. Tworzenie procedury składowanej w tej samej bazie danych SQL platformy Azure dla tabeli sterowania. 

    > [!NOTE]
    > Nazwa procedury składowanej jest sp_update_partition_success. Zostanie on wywołany przez działanie SqlServerStoredProcedure w potoku usługi ADF.

    ```sql
    CREATE PROCEDURE [dbo].[sp_update_partition_success] @PartPrefix varchar(255)
    AS
    BEGIN
    
        UPDATE s3_partition_control_table
        SET [SuccessOrFailure] = 1 WHERE [PartitionPrefix] = @PartPrefix
    END
    GO
    ```

3. Przejdź do szablonu **Migracji danych historycznych z usługi AWS S3 do usługi Azure Data Lake Storage Gen2.** Wprowadź połączenia do zewnętrznej tabeli sterowania, AWS S3 jako magazynu źródeł danych i usługi Azure Data Lake Storage Gen2 jako magazynu docelowego. Należy pamiętać, że zewnętrzna tabela sterowania i procedura składowana są odwołania do tego samego połączenia.

    ![Tworzenie nowego połączenia](media/solution-template-migration-s3-azure/historical-migration-s3-azure1.png)

4. Wybierz pozycję **Użyj tego szablonu**.

    ![Użyj tego szablonu](media/solution-template-migration-s3-azure/historical-migration-s3-azure2.png)
    
5. 2 potoki i 3 zestawy danych zostały utworzone, jak pokazano w poniższym przykładzie:

    ![Przejrzyj potok](media/solution-template-migration-s3-azure/historical-migration-s3-azure3.png)

6. Wybierz **debugowanie**, wprowadź **parametry**, a następnie wybierz **zakończ**.

    ![Kliknij przycisk **Debugowanie**](media/solution-template-migration-s3-azure/historical-migration-s3-azure4.png)

7. Zostaną wyświetlenia wyników podobnych do poniższego przykładu:

    ![Przejrzyj wynik](media/solution-template-migration-s3-azure/historical-migration-s3-azure5.png)


### <a name="for-the-template-to-copy-changed-files-only-from-amazon-s3-to-azure-data-lake-storage-gen2"></a>Aby szablon skopiował zmienione pliki tylko z Amazon S3 na Azure Data Lake Storage Gen2

1. Utwórz tabelę formantów w usłudze Azure SQL Database, aby przechowywać listę partycji AWS S3. 

    > [!NOTE]
    > Nazwa tabeli jest s3_partition_delta_control_table.
    > Schemat tabeli sterowania jest PartitionPrefix, JobRunTime i SuccessOrFailure, gdzie PartitionPrefix jest ustawieniem prefiksu w S3 do filtrowania folderów i plików w Amazon S3 według nazwy, JobRunTime jest datetime wartość po uruchomieniu zadań kopiowania i SuccessOrFailure jest stan kopiowania każdej partycji: 0 oznacza, że ta partycja nie została skopiowana na platformę Azure i 1 oznacza, że ta partycja została pomyślnie skopiowana do platformy Azure.
    > W tabeli sterowania zdefiniowano 5 partycji. Domyślną wartością jobruntime może być czas, kiedy rozpoczyna się jednorazowa migracja danych historycznych. ADF skopiuje pliki na AWS S3, które zostały ostatnio zmodyfikowane po tym czasie. Domyślny stan kopiowania każdej partycji wynosi 1.

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

2. Tworzenie procedury składowanej w tej samej bazie danych SQL platformy Azure dla tabeli sterowania. 

    > [!NOTE]
    > Nazwa procedury składowanej jest sp_insert_partition_JobRunTime_success. Zostanie on wywołany przez działanie SqlServerStoredProcedure w potoku usługi ADF.

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


3. Przejdź do **szablonu Kopiowanie danych delta z usługi AWS S3 do usługi Azure Data Lake Storage Gen2.** Wprowadź połączenia do zewnętrznej tabeli sterowania, AWS S3 jako magazynu źródeł danych i usługi Azure Data Lake Storage Gen2 jako magazynu docelowego. Należy pamiętać, że zewnętrzna tabela sterowania i procedura składowana są odwołania do tego samego połączenia.

    ![Tworzenie nowego połączenia](media/solution-template-migration-s3-azure/delta-migration-s3-azure1.png)

4. Wybierz pozycję **Użyj tego szablonu**.

    ![Użyj tego szablonu](media/solution-template-migration-s3-azure/delta-migration-s3-azure2.png)
    
5. 2 potoki i 3 zestawy danych zostały utworzone, jak pokazano w poniższym przykładzie:

    ![Przejrzyj potok](media/solution-template-migration-s3-azure/delta-migration-s3-azure3.png)

6. Wybierz **debugowanie**, wprowadź **parametry**, a następnie wybierz **zakończ**.

    ![Kliknij przycisk **Debugowanie**](media/solution-template-migration-s3-azure/delta-migration-s3-azure4.png)

7. Zostaną wyświetlenia wyników podobnych do poniższego przykładu:

    ![Przejrzyj wynik](media/solution-template-migration-s3-azure/delta-migration-s3-azure5.png)

8. Wyniki z tabeli kontrolnej można również sprawdzić za pomocą kwerendy *"wybierz * z s3_partition_delta_control_table",* zobaczysz dane wyjściowe podobne do następującego przykładu:

    ![Przejrzyj wynik](media/solution-template-migration-s3-azure/delta-migration-s3-azure6.png)
    
## <a name="next-steps"></a>Następne kroki

- [Kopiowanie plików z wielu kontenerów](solution-template-copy-files-multiple-containers.md)
- [Przenoszenie plików](solution-template-move-files.md)