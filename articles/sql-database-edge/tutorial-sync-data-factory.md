---
title: Synchronizuj dane z Azure SQL Database Edge przy użyciu Azure Data Factory | Microsoft Docs
description: Informacje o synchronizowaniu danych między Azure SQL Database Edge i magazynem obiektów blob platformy Azure
keywords: Baza danych SQL Database, synchronizacja danych z usługi SQL Database Edge, Fabryka danych programu SQL Database
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: tutorial
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 11/04/2019
ms.openlocfilehash: e6fd9e6431137708ba93328a8ed1359b93b4ee1f
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "74851710"
---
# <a name="tutorial-sync-data-from-sql-database-edge-to-azure-blob-storage-by-using-azure-data-factory"></a>Samouczek: synchronizacja danych z SQL Database Edge do usługi Azure Blob Storage za pomocą Azure Data Factory

W tym samouczku użyjesz Azure Data Factory do przyrostowej synchronizacji danych z magazynem obiektów blob platformy Azure z tabeli w wystąpieniu Azure SQL Database Edge.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Jeśli baza danych lub tabela nie została jeszcze utworzona w ramach wdrożenia Azure SQL Database Edge, użyj jednej z następujących metod, aby ją utworzyć:

* Użyj [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms/) lub [Azure Data Studio](/sql/azure-data-studio/download/) , aby nawiązać połączenie z SQL Database Edge. Uruchom skrypt SQL, aby utworzyć bazę danych i tabelę.
* Tworzenie bazy danych SQL i tabeli przy użyciu narzędzia [sqlcmd](/sql/tools/sqlcmd-utility/) przez bezpośrednie łączenie się z modułem SQL Database Edge. Aby uzyskać więcej informacji, zobacz [nawiązywanie połączenia z aparatem bazy danych przy użyciu narzędzia sqlcmd](/sql/ssms/scripting/sqlcmd-connect-to-the-database-engine/).
* Użyj programu sqlpackage. exe, aby wdrożyć plik pakietu DAC do kontenera SQL Database Edge. Proces ten można zautomatyzować, określając identyfikator URI pliku sqlpackage jako część konfiguracji żądanych właściwości modułu. Można również bezpośrednio użyć narzędzia klienta sqlpackage. exe do wdrożenia pakietu DAC do SQL Database Edge.

    Aby uzyskać informacje na temat pobierania pliku sqlpackage. exe, zobacz [Pobierz i zainstaluj pakiet sqlpackage](/sql/tools/sqlpackage-download/). Poniżej przedstawiono kilka przykładowych poleceń dla elementu sqlpackage. exe. Aby uzyskać więcej informacji, zobacz dokumentację programu sqlpackage. exe.

    **Tworzenie pakietu DAC**

    ```cmd
    sqlpackage /Action:Extract /SourceConnectionString:"Data Source=<Server_Name>,<port>;Initial Catalog=<DB_name>;User ID=<user>;Password=<password>" /TargetFile:<dacpac_file_name>
    ```

    **Zastosuj pakiet DAC**

    ```cmd
    sqlpackage /Action:Publish /Sourcefile:<dacpac_file_name> /TargetServerName:<Server_Name>,<port> /TargetDatabaseName:<DB_Name> /TargetUser:<user> /TargetPassword:<password>
    ```

## <a name="create-a-sql-table-and-procedure-to-store-and-update-the-watermark-levels"></a>Utwórz tabelę SQL i procedurę służącą do przechowywania i aktualizowania poziomów znaku wodnego

Tabela znaków wodnych służy do przechowywania ostatniej sygnatury czasowej, do której dane zostały już zsynchronizowane z usługą Azure Storage. Procedura składowana języka Transact-SQL (T-SQL) służy do aktualizowania tabeli znaków wodnych po każdej synchronizacji.

Uruchom te polecenia na SQL Database wystąpieniu krawędzi:

```sql
    Create table [dbo].[watermarktable]
    (
    TableName varchar(255),
    WatermarkValue datetime,
    )
    GO

    CREATE PROCEDURE usp_write_watermark @timestamp datetime, @TableName varchar(50)  
    AS  
    BEGIN
    UPDATE [dbo].[watermarktable]
    SET [WatermarkValue] = @timestamp WHERE [TableName] = @TableName
    END
    Go
```

## <a name="create-a-data-factory-pipeline"></a>Tworzenie potoku Data Factory

W tej sekcji utworzysz potok Azure Data Factory do synchronizowania danych z magazynem obiektów blob platformy Azure z tabeli w Azure SQL Database Edge.

### <a name="create-a-data-factory-by-using-the-data-factory-ui"></a>Tworzenie fabryki danych przy użyciu interfejsu użytkownika Data Factory

Utwórz fabrykę danych, wykonując instrukcje podane w [tym samouczku](../data-factory/quickstart-create-data-factory-portal.md#create-a-data-factory).

### <a name="create-a-data-factory-pipeline"></a>Tworzenie potoku Data Factory

1. Na stronie **wprowadzenie w** interfejsie użytkownika Data Factory wybierz pozycję **Utwórz potok**.

    ![Tworzenie potoku Data Factory](media/tutorial-sync-data-factory/data-factory-get-started.png)

2. Na stronie **Ogólne** okna **Właściwości** potoku wpisz **PeriodicSync** dla nazwy.

3. Dodaj działanie Lookup (wyszukiwanie), aby uzyskać starą wartość limitu. W okienku **działania** rozwiń pozycję **Ogólne** i przeciągnij działanie **Lookup (wyszukiwanie** ) do powierzchni projektanta potoku. Zmień nazwę działania na **OldWatermark**.

    ![Dodaj wyszukiwanie starego znaku wodnego](media/tutorial-sync-data-factory/create-old-watermark-lookup.png)

4. Przejdź do karty **Ustawienia** , a następnie wybierz pozycję **Nowy** dla **źródłowego zestawu danych**. Teraz utworzysz zestaw danych reprezentujący dane w tabeli znaków wodnych. Ta tabela zawiera stary limit, który był używany w poprzedniej operacji kopiowania.

5. W oknie **Nowy zestaw danych** wybierz pozycję **Azure SQL Server**, a następnie wybierz pozycję **Kontynuuj**.  

6. W oknie **dialogowym Ustawianie właściwości** dla zestawu danych w obszarze **Nazwa**wprowadź **WatermarkDataset**.

7. W obszarze **połączona usługa**wybierz pozycję **Nowy**, a następnie wykonaj następujące kroki:

    1. W polu **Nazwa**wprowadź **SQLDBEdgeLinkedService**.

    2. W obszarze **Nazwa serwera**wprowadź szczegóły serwera SQL Database Edge.

    3. Wybierz **nazwę bazy danych** z listy.

    4. Wprowadź **nazwę użytkownika** i **hasło**.

    5. Aby przetestować połączenie z wystąpieniem SQL Database krawędzi, wybierz pozycję **Test connection**.

    6. Wybierz przycisk **Utwórz**.

    ![Tworzenie usługi połączonej](media/tutorial-sync-data-factory/create-linked-service.png)

    7. Wybierz przycisk **OK**.

8. Na karcie **Ustawienia** wybierz pozycję **Edytuj**.

9. Na karcie **połączenie** wybierz pozycję **[dbo]. [ znaki wodne]** dla **tabeli**. Jeśli chcesz wyświetlić podgląd danych w tabeli, wybierz pozycję **Podgląd danych**.

10. Przejdź do edytora potoku, wybierając kartę potok u góry lub wybierając nazwę potoku w widoku drzewa po lewej stronie. W oknie właściwości działania Lookup upewnij się, że wybrano pozycję **WatermarkDataset** na liście **źródłowych zestawów danych** .

11. W okienku **działania** rozwiń pozycję **Ogólne** i przeciągnij kolejną aktywność **wyszukiwania** do powierzchni projektanta potoku. Ustaw nazwę na **NewWatermark** na karcie **Ogólne** w oknie właściwości. To działanie Lookup Pobiera wartość nowej wartości limitu z tabeli zawierającej dane źródłowe, dlatego można ją skopiować do lokalizacji docelowej.

12. W oknie właściwości drugiego działania wyszukiwania przejdź do karty **Ustawienia** i wybierz pozycję **Nowy** , aby utworzyć zestaw danych, który będzie wskazywał tabelę źródłową, która zawiera nową wartość limitu.

13. W oknie **Nowy zestaw danych** wybierz pozycję **SQL Database wystąpienie krawędzi**, a następnie wybierz pozycję **Kontynuuj**.

    1. W oknie **dialogowym Ustawianie właściwości** w obszarze **Nazwa**wprowadź **SourceDataset**. W obszarze **połączona usługa**wybierz pozycję **SQLDBEdgeLinkedService**.

    2. W obszarze **tabela**wybierz tabelę, którą chcesz synchronizować. Możesz również określić zapytanie dla tego zestawu danych, zgodnie z opisem w dalszej części tego samouczka. Zapytanie ma pierwszeństwo przed tabelą określoną w tym kroku.

    3. Wybierz przycisk **OK**.

14. Przejdź do edytora potoku, wybierając kartę potok u góry lub wybierając nazwę potoku w widoku drzewa po lewej stronie. W oknie właściwości działania Lookup upewnij się, że wybrano pozycję **SourceDataset** na liście **źródłowych zestawów danych** .

15. Wybierz **zapytanie** w obszarze **Użyj zapytania**. Zaktualizuj nazwę tabeli w poniższym zapytaniu, a następnie wprowadź zapytanie. Wybierasz tylko wartość `timestamp` maksymalną z tabeli. Upewnij się, że wybrano opcję **tylko pierwszy wiersz**.

    ```sql
    select MAX(timestamp) as NewWatermarkvalue from [TableName]
    ```

    ![Wybierz zapytanie](media/tutorial-sync-data-factory/select-query-data-factory.png)

16. W okienku **działania** rozwiń pozycję **Przenieś & Przekształć** i przeciągnij działanie **kopiowania** z okienka **działania** do powierzchni projektanta. Ustaw nazwę działania na **IncrementalCopy**.

17. Połącz oba działania wyszukiwania z działaniem kopiowania, przeciągając do działania kopiowania zielony przycisk dołączony do działania wyszukiwania. Zwolnij przycisk myszy, gdy kolor obramowania działania kopiowania zmieni się na niebieski.

18. Wybierz działanie kopiowania i upewnij się, że w oknie **Właściwości** widać właściwości działania.

19. Przejdź do karty **Źródło** w oknie **Właściwości** i wykonaj następujące kroki:

    1. W polu **źródłowy zestaw danych** wybierz pozycję **SourceDataset**.

    2. W obszarze **Użyj zapytania**wybierz opcję **zapytanie**.

    3. W polu **zapytania** wprowadź zapytanie SQL. Oto przykładowe zapytanie:

    ```sql
    select * from TemperatureSensor where timestamp > '@{activity('OldWaterMark').output.firstRow.WatermarkValue}' and timestamp <= '@{activity('NewWaterMark').output.firstRow.NewWatermarkvalue}'
    ```

20. Na karcie **ujścia** wybierz pozycję **Nowy** w obszarze **zestaw danych ujścia**.

21. W tym samouczku magazyn danych ujścia jest magazynem danych usługi Azure Blob Storage. Wybierz pozycję **Azure Blob Storage**, a następnie wybierz pozycję **Kontynuuj** w oknie **Nowy zestaw danych** .

22. W oknie **Wybieranie formatu** wybierz format danych, a następnie wybierz pozycję **Kontynuuj**.

23. W oknie **dialogowym Ustawianie właściwości** w obszarze **Nazwa**wprowadź **SinkDataset**. W obszarze **połączona usługa**wybierz pozycję **Nowy**. Teraz utworzysz połączenie (połączoną usługę) z magazynem obiektów blob platformy Azure.

24. W oknie **Nowa połączona usługa (Azure Blob Storage)** wykonaj następujące czynności:

    1. W polu **Nazwa** wprowadź **AzureStorageLinkedService**.

    2. W polu **nazwa konta magazynu**wybierz konto usługi Azure Storage dla subskrypcji platformy Azure.

    3. Przetestuj połączenie, a następnie wybierz pozycję **Zakończ**.

25. W oknie **Ustawianie właściwości** upewnij się, że w obszarze **połączona usługa**jest wybrana opcja **AzureStorageLinkedService** . Wybierz pozycję **Utwórz** i **przycisk OK**.

26. Na karcie **ujścia** wybierz pozycję **Edytuj**.

27. Przejdź do karty **połączenie** w SinkDataset i wykonaj następujące kroki:

    1. W obszarze **ścieżka pliku**wpisz *asdedatasync/incrementalcopy*, gdzie *asdedatasync* jest nazwą kontenera obiektów blob, a *incrementalcopy* to nazwa folderu. Utwórz kontener, jeśli nie istnieje, lub użyj nazwy istniejącej. Azure Data Factory automatycznie tworzy folder wyjściowy *incrementalcopy* , jeśli nie istnieje. Można również za pomocą przycisku **Przeglądaj** pola **Ścieżka pliku** przejść do folderu w kontenerze obiektów blob.

    2. W polu część **pliku** **ścieżka pliku**wybierz pozycję **Dodaj zawartość dynamiczną [Alt + P]**, a następnie wprowadź ** @CONCATwartość ("przyrost-", Potok (). RunId, ". txt")** w otwartym oknie. Wybierz pozycję **Zakończ**. Nazwa pliku jest generowana dynamicznie przez wyrażenie. Każde uruchomienie potoku ma unikatowy identyfikator. Działanie kopiowania używa identyfikatora uruchomienia do wygenerowania nazwy pliku.

28. Przejdź do edytora potoku, wybierając kartę potok u góry lub wybierając nazwę potoku w widoku drzewa po lewej stronie.

29. W okienku **działania** rozwiń pozycję **Ogólne** i przeciągnij działanie **procedury składowanej** z okienka **działania** do powierzchni projektanta potoku. Połącz zielone (pomyślne) dane wyjściowe działania kopiowania z działaniem procedury składowanej.

30. Wybierz **działanie procedury składowanej** w projektancie potoku i zmień jego nazwę na **SPtoUpdateWatermarkActivity**.

31. Przejdź do karty **konto SQL** , a następnie wybierz pozycję ***QLDBEdgeLinkedService** w obszarze **połączona usługa**.

32. Przejdź do karty **procedura składowana** i wykonaj następujące czynności:

    1. W obszarze **nazwa procedury składowanej**wybierz pozycję **[dbo]. [ usp_write_watermark]**.

    2. Aby określić wartości parametrów procedury składowanej, wybierz pozycję **Importuj parametr** i wprowadź następujące wartości parametrów:

    |Nazwa|Typ|Wartość|
    |-----|----|-----|
    |LastModifiedtime|DateTime|@ {Activity ("NewWaterMark"). Output. firstRow. NewWatermarkvalue}|
    |TableName|String|@ {Activity ("OldWaterMark"). Output. firstRow. TableName}|

33. Aby sprawdzić poprawność ustawień potoku, wybierz pozycję **Weryfikuj** na pasku narzędzi. Potwierdź, że weryfikacja nie zwróciła błędów. Aby zamknąć okno **raport weryfikacji potoku** , wybierz **>>** opcję.

34. Opublikuj jednostki (połączone usługi, zestawy danych i potoki) do usługi Azure Data Factory, wybierając przycisk **Opublikuj wszystko** . Poczekaj, aż zobaczysz komunikat potwierdzający, że operacja publikowania zakończyła się pomyślnie.

## <a name="trigger-a-pipeline-based-on-a-schedule"></a>Wyzwalanie potoku na podstawie harmonogramu

1. Na pasku narzędzi potoku wybierz pozycję **Dodaj wyzwalacz**, wybierz pozycję **Nowy/Edytuj**, a następnie wybierz pozycję **Nowy**.

2. Nazwij wyzwalacz **HourlySync**. W obszarze **Typ**wybierz pozycję **harmonogram**. Ustaw **cykl** na co 1 godzinę.

3. Wybierz przycisk **OK**.

4. Wybierz pozycję **Opublikuj wszystkie**.

5. Wybierz pozycję **Wyzwól teraz**.

6. Przejdź do karty **Monitorowanie** po lewej stronie. Widoczny będzie stan uruchomienia potoku, który został wyzwolony za pomocą wyzwalacza ręcznego. Wybierz pozycję **Odśwież**, aby odświeżyć listę.

## <a name="next-steps"></a>Następne kroki

Potok Azure Data Factory w tym samouczku kopiuje dane z tabeli w wystąpieniu SQL Database Edge do lokalizacji w usłudze Azure Blob Storage co godzinę. Aby dowiedzieć się więcej o używaniu Data Factory w innych scenariuszach, zobacz te [samouczki](../data-factory/tutorial-copy-data-portal.md).
