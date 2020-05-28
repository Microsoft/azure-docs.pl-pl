---
title: Przyrostowe Kopiowanie tabeli przy użyciu Azure Portal
description: W tym samouczku utworzysz potok usługi Azure Data Factory, który przyrostowo kopiuje dane z bazy danych Azure SQL Database do magazynu Azure Blob Storage.
services: data-factory
author: dearandyxu
ms.author: yexu
manager: anandsub
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.custom: seo-dt-2019
ms.date: 01/11/2018
ms.openlocfilehash: b1660c3a6d3bfe262493722c5aad0a08778b1964
ms.sourcegitcommit: 6a9f01bbef4b442d474747773b2ae6ce7c428c1f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/27/2020
ms.locfileid: "84119153"
---
# <a name="incrementally-load-data-from-an-azure-sql-database-to-azure-blob-storage-using-the-azure-portal"></a>Przyrostowe ładowanie danych z bazy danych Azure SQL Database do usługi Azure Blob Storage za pomocą Azure Portal

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

W tym samouczku utworzysz fabrykę danych Azure Data Factory z potokiem, który ładuje dane różnicowe z tabeli w bazie danych Azure SQL Database do magazynu Azure Blob Storage.

Ten samouczek obejmuje następujące procedury:

> [!div class="checklist"]
> * Przygotowywanie magazynu danych do przechowywania wartości limitu.
> * Tworzenie fabryki danych.
> * Tworzenie połączonych usług.
> * Tworzenie zestawów danych źródła, ujścia i limitu.
> * Tworzenie potoku.
> * Uruchamianie potoku.
> * Monitorowanie uruchomienia potoku.
> * Przegląd wyników
> * Dodawanie większej ilości danych do źródła.
> * Ponowne uruchamianie potoku.
> * Monitorowanie drugiego uruchomienia potoku
> * Przegląd wyników drugiego uruchomienia


## <a name="overview"></a>Omówienie
Diagram ogólny rozwiązania wygląda następująco:

![Przyrostowe ładowanie danych](media/tutorial-Incremental-copy-portal/incrementally-load.png)

Poniżej przedstawiono ważne czynności związane z tworzeniem tego rozwiązania:

1. **Wybierz kolumnę limitu**.
    Wybierz jedną kolumnę w magazynie danych źródłowych, która może służyć do tworzenia wycinków nowych lub zaktualizowanych rekordów dla każdego przebiegu. Zazwyczaj dane w tej wybranej kolumnie (na przykład last_modify_time lub ID) rosną wraz z tworzeniem i aktualizacją wierszy. Maksymalna wartość w tej kolumnie jest używana jako limit.

2. **Przygotuj magazyn danych do przechowywania wartości limitu**. W tym samouczku wartość limitu jest przechowywana w bazie danych SQL.

3. **Utwórz potok z następującym przepływem pracy**:

    Potok w tym rozwiązaniu obejmuje następujące działania:

    * Utwórz dwa działania Lookup. Użyj pierwszego działania Lookup do pobrania ostatniej wartości limitu. Użyj drugiego działania Lookup do pobrania nowej wartości limitu. Te wartości limitu są przekazywane do działania Copy.
    * Utwórz działanie Copy, które kopiuje wiersze z magazynu danych źródłowych o wartości kolumny limitu większej niż poprzednia wartość limitu i mniejszej niż nowa wartość limitu. Następnie kopiuje dane różnicowe ze źródłowego magazynu danych do usługi Blob Storage jako nowy plik.
    * Utwórz działanie StoredProcedure, które aktualizuje wartość limitu dla potoku przy następnym uruchomieniu.


Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

## <a name="prerequisites"></a>Wymagania wstępne
* **Azure SQL Database**. Baza danych jest używana jako źródłowy magazyn danych. Jeśli nie masz bazy danych SQL, utwórz ją, wykonując czynności przedstawione w artykule [Tworzenie bazy danych Azure SQL Database](../azure-sql/database/single-database-create-quickstart.md).
* **Usługa Azure Storage**. Magazyn obiektów blob jest używany jako magazyn danych ujścia. Jeśli nie masz konta magazynu, utwórz je, wykonując czynności przedstawione w artykule [Tworzenie konta magazynu](../storage/common/storage-account-create.md). Utwórz kontener o nazwie adftutorial. 

### <a name="create-a-data-source-table-in-your-sql-database"></a>Tworzenie tabeli danych źródłowych w bazie danych SQL
1. Otwórz program SQL Server Management Studio. W **Eksploratorze serwera** kliknij prawym przyciskiem myszy bazę danych, a następnie wybierz pozycję **Nowe zapytanie**.

2. Uruchom następujące polecenie SQL względem bazy danych SQL, aby utworzyć tabelę o nazwie `data_source_table` jako źródłowy magazyn danych:

    ```sql
    create table data_source_table
    (
        PersonID int,
        Name varchar(255),
        LastModifytime datetime
    );

    INSERT INTO data_source_table
    (PersonID, Name, LastModifytime)
    VALUES
    (1, 'aaaa','9/1/2017 12:56:00 AM'),
    (2, 'bbbb','9/2/2017 5:23:00 AM'),
    (3, 'cccc','9/3/2017 2:36:00 AM'),
    (4, 'dddd','9/4/2017 3:21:00 AM'),
    (5, 'eeee','9/5/2017 8:06:00 AM');
    ```
    W tym samouczku użyjesz kolumny LastModifytime jako kolumny limitu. Dane w źródłowym magazynie danych zostały przedstawione w poniższej tabeli:

    ```
    PersonID | Name | LastModifytime
    -------- | ---- | --------------
    1 | aaaa | 2017-09-01 00:56:00.000
    2 | bbbb | 2017-09-02 05:23:00.000
    3 | cccc | 2017-09-03 02:36:00.000
    4 | dddd | 2017-09-04 03:21:00.000
    5 | eeee | 2017-09-05 08:06:00.000
    ```

### <a name="create-another-table-in-your-sql-database-to-store-the-high-watermark-value"></a>Tworzenie innej tabeli w bazie danych SQL do przechowywania wartości górnego limitu
1. Uruchom następujące polecenie SQL względem bazy danych SQL, aby utworzyć tabelę o nazwie `watermarktable` w celu przechowywania wartości limitu:  

    ```sql
    create table watermarktable
    (

    TableName varchar(255),
    WatermarkValue datetime,
    );
    ```
2. Ustaw domyślną wartość górnego limitu z nazwą tabeli źródłowego magazynu danych. W tym samouczku nazwa tabeli to data_source_table.

    ```sql
    INSERT INTO watermarktable
    VALUES ('data_source_table','1/1/2010 12:00:00 AM')    
    ```
3. Sprawdź dane w tabeli `watermarktable`.

    ```sql
    Select * from watermarktable
    ```
    Dane wyjściowe:

    ```
    TableName  | WatermarkValue
    ----------  | --------------
    data_source_table | 2010-01-01 00:00:00.000
    ```

### <a name="create-a-stored-procedure-in-your-sql-database"></a>Tworzenie procedur składowanych w bazie danych SQL

Uruchom następujące polecenie, aby utworzyć procedurę składowaną w bazie danych SQL:

```sql
CREATE PROCEDURE usp_write_watermark @LastModifiedtime datetime, @TableName varchar(50)
AS

BEGIN

    UPDATE watermarktable
    SET [WatermarkValue] = @LastModifiedtime
WHERE [TableName] = @TableName

END
```

## <a name="create-a-data-factory"></a>Tworzenie fabryki danych

1. Uruchom przeglądarkę internetową **Microsoft Edge** lub **Google Chrome**. Obecnie interfejs użytkownika usługi Data Factory jest obsługiwany tylko przez przeglądarki internetowe Microsoft Edge i Google Chrome.
2. Z menu po lewej stronie wybierz pozycję **Utwórz**  >  **Analytics**  >  **Data Factory**analizy zasobów:

   ![Wybór usługi Data Factory w okienku „Nowy”](./media/doc-common-process/new-azure-data-factory-menu.png)

3. Na stronie **Nowa fabryka danych** jako **nazwę** wprowadź wartość **ADFIncCopyTutorialDF**.

   Nazwa fabryki danych Azure musi być **globalnie unikatowa**. Jeśli pojawi się czerwony wykrzyknik z poniższym błędem, zmień nazwę fabryki danych (np. twojanazwaADFIncCopyTutorialDF) i spróbuj utworzyć ją ponownie. Artykuł [Data Factory — Naming Rules (Usługa Data Factory — reguły nazewnictwa)](naming-rules.md) zawiera reguły nazewnictwa artefaktów usługi Data Factory.

       `Data factory name "ADFIncCopyTutorialDF" is not available`
4. Wybierz **subskrypcję** Azure, w której chcesz utworzyć fabrykę danych.
5. Dla opcji **Grupa zasobów** wykonaj jedną z następujących czynności:

      - Wybierz pozycję **Użyj istniejącej**, a następnie wybierz istniejącą grupę zasobów z listy rozwijanej.
      - Wybierz pozycję **Utwórz nową**, a następnie wprowadź nazwę grupy zasobów.   
         
        Informacje na temat grup zasobów znajdują się w artykule [Using resource groups to manage your Azure resources](../azure-resource-manager/management/overview.md) (Używanie grup zasobów do zarządzania zasobami platformy Azure).  
6. Wybierz opcję **V2** w obszarze **Wersja**.
7. Na liście **lokalizacja** wybierz lokalizację fabryki danych. Na liście rozwijanej są wyświetlane tylko obsługiwane lokalizacje. Magazyny danych (Azure Storage, Azure SQL Database itp.) i jednostki obliczeniowe (HDInsight itp.) używane przez fabrykę danych mogą mieścić się w innych regionach.
8. Kliknij przycisk **Utwórz**.      
9. Po zakończeniu tworzenia zostanie wyświetlona strona **Fabryka danych**, jak pokazano na poniższej ilustracji.

   ![Strona główna fabryki danych](./media/doc-common-process/data-factory-home-page.png)
10. Kliknij kafelek **Tworzenie i monitorowanie**, aby w osobnej karcie uruchomić interfejs użytkownika usługi Azure Data Factory.

## <a name="create-a-pipeline"></a>Tworzenie potoku
W tym samouczku utworzysz potok z dwoma działaniami Lookup, jednym działaniem Copy i jednym działaniem StoredProcedure, połączonymi w jednym potoku.

1. Na stronie **Wprowadzenie** interfejsu użytkownika usługi Data Factory kliknij kafelek **Utwórz potok**.

   ![Strona Wprowadzenie interfejsu użytkownika usługi Data Factory](./media/doc-common-process/get-started-page.png)    
3. Na stronie **Ogólne** w oknie **Właściwości** dla potoku wpisz nazwę **IncrementalCopyPipeline**.

4. Dodajmy pierwsze działanie wyszukiwania w celu pobrania starej wartości limitu. W przyborniku **Działania** rozwiń pozycję **Ogólne**, a następnie przeciągnij działanie **Lookup** (Wyszukiwanie) i upuść je na powierzchni projektanta potoku. Zmień nazwę działania na **LookupOldWaterMarkActivity**.

   ![Pierwsze działanie wyszukiwania — nazwa](./media/tutorial-incremental-copy-portal/first-lookup-name.png)
5. Przejdź do karty **Ustawienia**, a następnie kliknij pozycję **+Nowy** dla pozycji **Źródłowy zestaw danych**. W tym kroku utworzysz zestaw danych reprezentujący dane w tabeli **watermarktable**. Ta tabela zawiera stary limit, który był używany w poprzedniej operacji kopiowania.

6. W oknie **Nowy zestaw danych** wybierz pozycję **Azure SQL Database**, a następnie kliknij przycisk **Kontynuuj**. Zobaczysz nowe okno otwarte dla zestawu danych.

7. W oknie **Ustawianie właściwości** dla zestawu danych wprowadź **WatermarkDataset** dla **nazwy**.

8. W polu **połączona usługa**wybierz pozycję **Nowy**, a następnie wykonaj następujące czynności:

    1. Wprowadź wartość **AzureSqlDatabaseLinkedService** w polu **Nazwa**.
    2. Wybierz serwer dla **nazwy serwera**.
    3. Wybierz **nazwę bazy danych** z listy rozwijanej.
    4. Wprowadź hasło do **nazwy użytkownika**  &  **Password**.
    5. Aby przetestować połączenie z bazą danych Azure SQL Database, kliknij przycisk **Testuj połączenie**.
    6. Kliknij przycisk **Zakończ**.
    7. Upewnij się, że wybrano **AzureSqlDatabaseLinkedService** dla **połączonej usługi**.

        ![Okno Nowa połączona usługa](./media/tutorial-incremental-copy-portal/azure-sql-linked-service-settings.png)
    8. Wybierz pozycję **Zakończ**.
9. Na karcie **połączenie** wybierz pozycję **[dbo]. [ znaki wodne]** dla **tabeli**. Jeśli chcesz wyświetlić podgląd danych w tabeli, kliknij przycisk **Podgląd danych**.

    ![Zestaw danych limitu — ustawienia połączenia](./media/tutorial-incremental-copy-portal/watermark-dataset-connection-settings.png)
10. Przejdź do edytora potoku, klikając kartę potoku u góry lub klikając nazwę potoku w widoku drzewa po lewej stronie. W oknie dialogowym właściwości działania **Lookup** (Wyszukiwanie) upewnij się, że dla pola **Zestaw danych źródłowych** wybrano wartość **WatermarkDataset**.

11. W przyborniku **Działania** rozwiń pozycję **Ogólne**, a następnie przeciągnij i upuść kolejne działanie **Lookup** (Wyszukiwanie) na powierzchni projektanta potoku. Na karcie **Ogólne** okna właściwości ustaw nazwę **LookupNewWaterMarkActivity**. To działanie wyszukiwania pobiera nową wartość limitu z tabeli zawierającej dane źródłowe do skopiowania do miejsca docelowego.

12. W oknie właściwości dla drugiego działania **Lookup** (Wyszukiwania) przejdź do karty **Ustawienia**, a następnie kliknij pozycję **Nowy**. Utworzysz zestaw danych wskazujący tabelę źródłową zawierającą nową wartość limitu (wartość maksymalna elementu LastModifyTime).

13. W oknie **Nowy zestaw danych** wybierz pozycję **Azure SQL Database**, a następnie kliknij przycisk **Kontynuuj**.
14. W oknie **Ustawianie właściwości** wpisz **SourceDataset** ( **Nazwa**). Wybierz wartość **AzureSqlDatabaseLinkedService** w polu **Połączona usługa**.
15. Wybierz element **[dbo].[data_source_table]** dla pozycji Tabela. Zapytanie zostanie wprowadzone w tym zestawie danych później w tym samouczku. Zapytanie ma pierwszeństwo przed tabelą określaną w tym kroku.
16. Wybierz pozycję **Zakończ**.
17. Przejdź do edytora potoku, klikając kartę potoku u góry lub klikając nazwę potoku w widoku drzewa po lewej stronie. W oknie dialogowym właściwości działania **Lookup** (Wyszukiwanie) upewnij się, że dla pola **Zestaw danych źródłowych** wybrano wartość **SourceDataset**.
18. Wybierz wartość **Zapytanie** w polu **Użyj zapytania**, a następnie wprowadź następujące zapytanie: wybierasz tylko maksymalną wartość **LastModifytime** z tabeli **data_source_table**. Upewnij się, że zaznaczono również **tylko pierwszy wiersz**.

    ```sql
    select MAX(LastModifytime) as NewWatermarkvalue from data_source_table
    ```

    ![Drugie działanie wyszukiwania — zapytanie](./media/tutorial-incremental-copy-portal/query-for-new-watermark.png)
19. W przyborniku **działania** rozwiń pozycję **Przenieś & Przekształć**, a następnie przeciągnij i upuść działanie **Copy (kopiowanie** ) z przybornika działania, a następnie ustaw nazwę na **wartość incrementalcopyactivity**.

20. **Połącz oba działania wyszukiwania z działaniem kopiowania**, przeciągając do działania kopiowania **zielony przycisk** dołączony do działania wyszukiwania. Po zmianie koloru obramowania działania kopiowania na niebieski zwolnij przycisk myszy.

    ![Połączenie działań wyszukiwania z działaniem kopiowania](./media/tutorial-incremental-copy-portal/connection-lookups-to-copy.png)
21. Wybierz **działanie kopiowania** i upewnij się, że w oknie **Właściwości** widać właściwości działania.

22. Przejdź do karty **Źródło** w oknie **Właściwości** i wykonaj następujące czynności:

    1. Wybierz pozycję **SourceDataset** dla pola **Zestaw danych będący źródłem**.
    2. Wybierz pozycję **Zapytanie** dla pola **Użyj zapytania**.
    3. W polu **Zapytanie** wprowadź następujące zapytanie SQL.

        ```sql
        select * from data_source_table where LastModifytime > '@{activity('LookupOldWaterMarkActivity').output.firstRow.WatermarkValue}' and LastModifytime <= '@{activity('LookupNewWaterMarkActivity').output.firstRow.NewWatermarkvalue}'
        ```

        ![Działanie Copy (Kopiowanie) — źródło](./media/tutorial-incremental-copy-portal/copy-activity-source.png)
23. Przejdź do karty **Ujście** i kliknij pozycję **+Nowy** dla pola **Zestaw danych ujścia**.

24. W tym samouczku magazyn danych ujścia jest typu Azure Blob Storage. W związku z tym wybierz pozycję **Azure Blob Storage**i kliknij przycisk **Kontynuuj** w oknie **Nowy zestaw danych** .
25. W oknie **Wybieranie formatu** wybierz typ formatu danych, a następnie kliknij przycisk **Kontynuuj**.
25. W oknie **Ustawianie właściwości** wpisz **SinkDataset** ( **Nazwa**). W obszarze **połączona usługa**wybierz pozycję **+ Nowy**. W tym kroku tworzysz połączenie (usługę połączoną) z magazynem **Azure Blob Storage**.
26. W oknie **Nowa połączona usługa (Azure Blob Storage)** wykonaj następujące czynności:

    1. Wprowadź wartość **AzureStorageLinkedService** w polu **Nazwa**.
    2. Wybierz swoje konto usługi Azure Storage w polu **Nazwa konta magazynu**.
    3. Testuj połączenie, a następnie kliknij przycisk **Zakończ**.

27. W oknie **Ustawianie właściwości** upewnij się, że wybrano opcję **AzureStorageLinkedService** dla **połączonej usługi**. Następnie wybierz pozycję **Zakończ**.
28. Przejdź do karty **połączenie** w programie SinkDataset i wykonaj następujące czynności:
    1. W polu **ścieżka pliku** wprowadź **adftutorial/incrementalcopy**. Kontenerem obiektów blob jest **adftutorial**, a nazwą folderu — **incrementalcopy**. W tym fragmencie kodu założono, że w masz kontener obiektów blob o nazwie adftutorial w magazynie obiektów blob. Utwórz ten kontener, jeśli nie istnieje, lub zastąp go nazwą istniejącego kontenera. Jeśli folder wyjściowy **incrementalcopy** nie istnieje, usługa Azure Data Factory automatycznie go utworzy. Można również za pomocą przycisku **Przeglądaj** pola **Ścieżka pliku** przejść do folderu w kontenerze obiektów blob.
    2. Dla części **pliku** w polu **ścieżka pliku** wybierz pozycję **Dodaj zawartość dynamiczną [Alt + P]**, a następnie wprowadź `@CONCAT('Incremental-', pipeline().RunId, '.txt')` w otwartym oknie. Następnie wybierz pozycję **Zakończ**. Nazwa pliku jest generowana dynamicznie przy użyciu wyrażenia. Każde uruchomienie potoku ma unikatowy identyfikator. Działanie kopiowania używa identyfikatora uruchomienia do wygenerowania nazwy pliku.

28. Przejdź do edytora **potoku** , klikając kartę potoku u góry lub klikając nazwę potoku w widoku drzewa po lewej stronie.
29. W przyborniku **Działania** rozwiń pozycję **Ogólne**, a następnie przeciągnij i upuść działanie **Stored Procedure** (Procedura składowana) z przybornika **Działania** na powierzchnię projektanta potoku. **Połącz** zielone (Powodzenie) wyjście działania **kopiowania** z działaniem **procedury składowanej**.

24. Wybierz **działanie procedury składowanej** w Projektancie potoku i zmień jego nazwę na **StoredProceduretoWriteWatermarkActivity**.

25. Przejdź do karty **konto SQL** , a następnie wybierz pozycję **AzureSqlDatabaseLinkedService** dla **połączonej usługi**.

26. Przejdź do karty **Procedura składowana** i wykonaj następujące czynności:

    1. W polu **Nazwa procedury składowanej** wybierz wartość **usp_write_watermark**.
    2. Aby określić wartości parametrów procedury składowanej, kliknij pozycję **Importuj parametr**, a następnie wprowadź następujące wartości parametrów:

        | Nazwa | Typ | Wartość |
        | ---- | ---- | ----- |
        | LastModifiedtime | Data/godzina | @{activity('LookupNewWaterMarkActivity').output.firstRow.NewWatermarkvalue} |
        | TableName | String (ciąg) | @{activity('LookupOldWaterMarkActivity').output.firstRow.TableName} |

    ![Działanie procedury składowanej — ustawienia procedury składowanej](./media/tutorial-incremental-copy-portal/sproc-activity-stored-procedure-settings.png)
27. Aby zweryfikować ustawienia potoku, kliknij pozycję **Weryfikuj** na pasku narzędzi. Potwierdź, że weryfikacja nie zwróciła błędów. Aby zamknąć okno **Raport weryfikacji potoku**, kliknij pozycję >>.   

28. Opublikuj jednostki (usługi połączone, zestawy danych i potoki) w usłudze Azure Data Factory, wybierając przycisk **Opublikuj wszystko**. Poczekaj, aż zostanie wyświetlony komunikat o pomyślnym opublikowaniu.


## <a name="trigger-a-pipeline-run"></a>Wyzwalanie uruchomienia potoku
1. Na pasku narzędzi kliknij pozycję **Dodaj wyzwalacz** , a następnie kliknij pozycję **Wyzwól teraz**.

2. W oknie **Uruchomienie potoku** wybierz pozycję **Zakończ**.

## <a name="monitor-the-pipeline-run"></a>Monitorowanie działania potoku

1. Przejdź do karty **Monitorowanie** po lewej stronie. Widoczny będzie stan uruchomienia potoku, który został wyzwolony za pomocą wyzwalacza ręcznego. Kliknij przycisk **Odśwież**, aby odświeżyć listę.

2. Aby wyświetlić uruchomienia działań skojarzone z tym uruchomieniem potoku, kliknij pierwszy link (**Wyświetl uruchomienia działań**) w kolumnie **Akcje**. Do poprzedniego widoku można wrócić, klikając pozycję **Potoki** u góry. Kliknij przycisk **Odśwież**, aby odświeżyć listę.


## <a name="review-the-results"></a>Sprawdzanie wyników
1. Połącz się z kontem usługi Azure Storage za pomocą narzędzi, takich jak [Eksplorator usługi Azure Storage](https://azure.microsoft.com/features/storage-explorer/). Sprawdź, czy plik wyjściowy został utworzony w folderze **incrementalcopy** w kontenerze **adftutorial**.

    ![Pierwszy plik wyjściowy](./media/tutorial-incremental-copy-portal/first-output-file.png)
2. Otwórz plik wyjściowy i zwróć uwagę na to, że do pliku obiektu blob skopiowano wszystkie dane z tabeli **data_source_table**.

    ```
    1,aaaa,2017-09-01 00:56:00.0000000
    2,bbbb,2017-09-02 05:23:00.0000000
    3,cccc,2017-09-03 02:36:00.0000000
    4,dddd,2017-09-04 03:21:00.0000000
    5,eeee,2017-09-05 08:06:00.0000000
    ```
3. Sprawdź najnowszą wartość w tabeli `watermarktable`. Zobaczysz, że wartość limitu została zaktualizowana.

    ```sql
    Select * from watermarktable
    ```

    Oto dane wyjściowe:

    | TableName | WatermarkValue |
    | --------- | -------------- |
    | data_source_table | 2017-09-05    8:06:00.000 |

## <a name="add-more-data-to-source"></a>Dodawanie większej ilości danych do źródła

Wstaw nowe dane do bazy danych SQL (źródłowego magazynu danych źródłowych).

```sql
INSERT INTO data_source_table
VALUES (6, 'newdata','9/6/2017 2:23:00 AM')

INSERT INTO data_source_table
VALUES (7, 'newdata','9/7/2017 9:01:00 AM')
```

Zaktualizowane dane w bazie danych SQL są następujące:

```
PersonID | Name | LastModifytime
-------- | ---- | --------------
1 | aaaa | 2017-09-01 00:56:00.000
2 | bbbb | 2017-09-02 05:23:00.000
3 | cccc | 2017-09-03 02:36:00.000
4 | dddd | 2017-09-04 03:21:00.000
5 | eeee | 2017-09-05 08:06:00.000
6 | newdata | 2017-09-06 02:23:00.000
7 | newdata | 2017-09-07 09:01:00.000
```


## <a name="trigger-another-pipeline-run"></a>Wyzwalanie kolejnego uruchomienia potoku
1. Przejdź do karty **Edycja** . Kliknij potok w widoku drzewa, jeśli nie jest on otwarty w projektancie.

2. Na pasku narzędzi kliknij pozycję **Dodaj wyzwalacz** , a następnie kliknij pozycję **Wyzwól teraz**.


## <a name="monitor-the-second-pipeline-run"></a>Monitorowanie drugiego uruchomienia potoku

1. Przejdź do karty **Monitorowanie** po lewej stronie. Widoczny będzie stan uruchomienia potoku, który został wyzwolony za pomocą wyzwalacza ręcznego. Kliknij przycisk **Odśwież**, aby odświeżyć listę.

2. Aby wyświetlić uruchomienia działań skojarzone z tym uruchomieniem potoku, kliknij pierwszy link (**Wyświetl uruchomienia działań**) w kolumnie **Akcje**. Do poprzedniego widoku można wrócić, klikając pozycję **Potoki** u góry. Kliknij przycisk **Odśwież**, aby odświeżyć listę.


## <a name="verify-the-second-output"></a>Weryfikowanie drugiego zestawu danych wyjściowych

1. W magazynie obiektów blob zobaczysz, że utworzono kolejny plik. W tym samouczku nazwa nowego pliku to `Incremental-<GUID>.txt`. Otwórz ten plik — zobaczysz w nim dwa wiersze rekordów.

    ```
    6,newdata,2017-09-06 02:23:00.0000000
    7,newdata,2017-09-07 09:01:00.0000000    
    ```
2. Sprawdź najnowszą wartość w tabeli `watermarktable`. Zobaczysz, że wartość limitu została ponownie zaktualizowana.

    ```sql
    Select * from watermarktable
    ```
    Przykładowe dane wyjściowe:

    | TableName | WatermarkValue |
    | --------- | --------------- |
    | data_source_table | 2017-09-07 09:01:00.000 |



## <a name="next-steps"></a>Następne kroki
W ramach tego samouczka wykonano następujące procedury:

> [!div class="checklist"]
> * Przygotowywanie magazynu danych do przechowywania wartości limitu.
> * Tworzenie fabryki danych.
> * Tworzenie połączonych usług.
> * Tworzenie zestawów danych źródła, ujścia i limitu.
> * Tworzenie potoku.
> * Uruchamianie potoku.
> * Monitorowanie uruchomienia potoku.
> * Przegląd wyników
> * Dodawanie większej ilości danych do źródła.
> * Ponowne uruchamianie potoku.
> * Monitorowanie drugiego uruchomienia potoku
> * Przegląd wyników drugiego uruchomienia

W tym samouczku potok skopiował dane z jednej tabeli w bazie danych SQL do magazynu Blob Storage. Przejdź do poniższego samouczka, aby uzyskać informacje na temat kopiowania danych z wielu tabel w lokalnej bazie danych programu SQL Server do bazy danych SQL.

> [!div class="nextstepaction"]
>[Przyrostowe ładowanie danych z wielu tabel w programie SQL Server do bazy danych Azure SQL Database](tutorial-incremental-copy-multiple-tables-portal.md)
