---
title: Przyrostowe kopiowanie danych przy użyciu funkcji przechwytywania zmian danych
description: W tym samouczku utworzysz potok Azure Data Factory, który przyrostowo kopiuje dane różnicowe z tabeli w bazie danych wystąpienia zarządzanego Azure SQL do usługi Azure Storage.
services: data-factory
ms.author: nihurt
author: hurtn
manager: ''
ms.reviewer: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.custom: ''
ms.date: 05/04/2020
ms.openlocfilehash: 06dd55ce400667939fca4b0f48159f8b7dde66c6
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/07/2020
ms.locfileid: "91825153"
---
# <a name="incrementally-load-data-from-azure-sql-managed-instance-to-azure-storage-using-change-data-capture-cdc"></a>Przyrostowe ładowanie danych z wystąpienia zarządzanego usługi Azure SQL do usługi Azure Storage przy użyciu funkcji przechwytywania zmian danych

W tym samouczku utworzysz fabrykę danych platformy Azure z potokiem, który ładuje dane różnicowe na podstawie informacji o **funkcji przechwytywania zmian danych** w źródłowej bazie danych wystąpienia zarządzanego Azure SQL do magazynu obiektów blob platformy Azure.  

Ten samouczek obejmuje następujące procedury:

> [!div class="checklist"]
> * Przygotowywanie magazynu danych źródłowych
> * Tworzenie fabryki danych.
> * Tworzenie połączonych usług.
> * Tworzenie zestawu danych źródła i ujścia.
> * Tworzenie, debugowanie i uruchamianie potoku w celu sprawdzenia zmienionych danych
> * Modyfikuj dane w tabeli źródłowej
> * Kończenie, uruchamianie i monitorowanie pełnego potoku kopiowania przyrostowego

## <a name="overview"></a>Omówienie
Technologia przechwytywania zmian danych obsługiwana przez magazyny danych, takie jak wystąpienia zarządzane usługi Azure SQL (MI) i SQL Server, może służyć do identyfikowania zmienionych danych.  W tym samouczku opisano sposób korzystania z Azure Data Factory z technologią przechwytywania zmian danych SQL w celu przyrostowego ładowania danych różnicowych z wystąpienia zarządzanego usługi Azure SQL do usługi Azure Blob Storage.  Aby uzyskać bardziej szczegółowe informacje na temat technologii przechwytywania zmian danych w usłudze SQL, zobacz [zmiana danych przechwytywania w SQL Server](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-data-capture-sql-server).

## <a name="end-to-end-workflow"></a>Kompletny przepływ pracy
Poniżej przedstawiono typowe kroki przepływu pracy służące do przyrostowego ładowania danych przy użyciu technologii przechwytywania zmian danych.

> [!NOTE]
> Zarówno usługa Azure SQL MI, jak i SQL Server obsługują technologię przechwytywania zmian danych. W tym samouczku jako źródłowy magazyn danych jest stosowane wystąpienie zarządzane Azure SQL. Możesz również użyć lokalnego serwera SQL Server.

## <a name="high-level-solution"></a>Rozwiązanie ogólne
W tym samouczku utworzysz potok, który wykonuje następujące operacje:  

   1. Utwórz **działanie Lookup** , aby policzyć liczbę zmienionych rekordów w SQL Database tabeli przestawnej i przekazać ją do działania warunku IF.
   2. Utwórz **warunek if** , aby sprawdzić, czy zostały zmienione rekordy, i jeśli tak, wywołaj działanie kopiowania.
   3. Utwórz **działanie kopiowania** , aby skopiować wstawione/zaktualizowane/usunięte dane między tabelą funkcji przechwytywania do BLOB Storage platformy Azure.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

## <a name="prerequisites"></a>Wymagania wstępne
* **Azure SQL Database wystąpienia zarządzanego**. Baza danych jest używana jako magazyn danych **źródłowych**. Jeśli nie masz wystąpienia zarządzanego Azure SQL Database, zapoznaj się z artykułem [tworzenie Azure SQL Database wystąpienia zarządzanego](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started) , aby zapoznać się z krokami.
* **Konto usługi Azure Storage**. Magazyn obiektów blob jest używany jako magazyn danych **źródłowych**. Jeśli nie masz konta usługi Azure Storage, utwórz je, wykonując czynności przedstawione w artykule [Tworzenie konta magazynu](../storage/common/storage-account-create.md). Utwórz kontener o nazwie **RAW**. 

### <a name="create-a-data-source-table-in-azure-sql-database"></a>Tworzenie tabeli źródła danych w Azure SQL Database

1. Uruchom **SQL Server Management Studio**i nawiąż połączenie z serwerem usługi Azure SQL Managed Instances.
2. W **Eksploratorze serwera** kliknij prawym przyciskiem używaną **bazę danych**, a następnie wybierz pozycję **Nowe zapytanie**.
3. Uruchom następujące polecenie SQL względem bazy danych wystąpień zarządzanych Azure SQL, aby utworzyć tabelę o nazwie `customers` jako magazyn źródła danych.  

    ```sql
    create table customers 
    (
    customer_id int, 
    first_name varchar(50), 
    last_name varchar(50), 
    email varchar(100), 
    city varchar(50), CONSTRAINT "PK_Customers" PRIMARY KEY CLUSTERED ("customer_id") 
     );
    ```
4. Włącz mechanizm **przechwytywania zmian danych** dla bazy danych i tabeli źródłowej (klientów), uruchamiając następujące zapytanie SQL:

    > [!NOTE]
    > - Zastąp &lt; nazwę schematu źródłowego własnym &gt; schematem platformy Azure SQL, który zawiera tabelę Customers.
    > - Przechwytywanie zmian danych nie wykonuje żadnych operacji w ramach transakcji, które zmieniają śledzoną tabelę. Zamiast tego operacje wstawiania, aktualizowania i usuwania są zapisywane w dzienniku transakcji. Dane, które są zdeponowane w tabelach zmian, zostaną rozbudowywane w sposób niezarządzany, jeśli nie zostaną okresowo i systematyczne oczyszczanie danych. Aby uzyskać więcej informacji, zobacz [Włączanie przechwytywania zmian danych dla bazy danych](https://docs.microsoft.com/sql/relational-databases/track-changes/enable-and-disable-change-data-capture-sql-server?enable-change-data-capture-for-a-database=&view=sql-server-ver15)

    ```sql
    EXEC sys.sp_cdc_enable_db 
    
    EXEC sys.sp_cdc_enable_table
    @source_schema = 'dbo',
    @source_name = 'customers', 
    @role_name = 'null',
    @supports_net_changes = 1
    ```
5. Wstaw dane do tabeli Customers, uruchamiając następujące polecenie:

    ```sql
     insert into customers 
        (customer_id, first_name, last_name, email, city) 
     values 
        (1, 'Chevy', 'Leward', 'cleward0@mapy.cz', 'Reading'),
        (2, 'Sayre', 'Ateggart', 'sateggart1@nih.gov', 'Portsmouth'),
        (3, 'Nathalia', 'Seckom', 'nseckom2@blogger.com', 'Portsmouth');
    ```

    > [!NOTE]
    > Żadne historyczne zmiany w tabeli nie są przechwytywane przed włączeniu funkcji przechwytywania zmian danych.

## <a name="create-a-data-factory"></a>Tworzenie fabryki danych

1. Uruchom przeglądarkę internetową **Microsoft Edge** lub **Google Chrome**. Obecnie interfejs użytkownika usługi Data Factory jest obsługiwany tylko przez przeglądarki internetowe Microsoft Edge i Google Chrome.
1. W menu po lewej stronie wybierz pozycję **Utwórz zasób**  >  **dane + analiza**  >  **Data Factory**:

   ![Wybór usługi Data Factory w okienku „Nowy”](./media/tutorial-incremental-copy-change-data-capture-feature-portal/new-azure-data-factory-menu.png)

2. Na stronie **Nowa fabryka danych** wprowadź wartość **ADFTutorialDataFactory** w polu **Nazwa**.

     ![Strona Nowa fabryka danych](./media/tutorial-incremental-copy-change-data-capture-feature-portal/new-azure-data-factory.png)

   Nazwa fabryki danych Azure musi być **globalnie unikatowa**. Jeśli wystąpi poniższy błąd, zmień nazwę fabryki danych (np. twojanazwaADFTutorialDataFactory) i spróbuj utworzyć ją ponownie. Artykuł [Data Factory — Naming Rules (Usługa Data Factory — reguły nazewnictwa)](naming-rules.md) zawiera reguły nazewnictwa artefaktów usługi Data Factory.

    *Nazwa fabryki danych "ADFTutorialDataFactory" jest niedostępna.*
3. Wybierz opcję **V2** w obszarze **Wersja**.
4. Wybierz **subskrypcję** Azure, w której chcesz utworzyć fabrykę danych.
5. Dla opcji **Grupa zasobów** wykonaj jedną z następujących czynności:

   1. Wybierz pozycję **Użyj istniejącej**, a następnie wybierz istniejącą grupę zasobów z listy rozwijanej.
   2. Wybierz pozycję **Utwórz nową**, a następnie wprowadź nazwę grupy zasobów.   
         
    Informacje na temat grup zasobów znajdują się w artykule [Using resource groups to manage your Azure resources](../azure-resource-manager/management/overview.md) (Używanie grup zasobów do zarządzania zasobami platformy Azure).  
5. Na liście **lokalizacja** wybierz lokalizację fabryki danych. Na liście rozwijanej są wyświetlane tylko obsługiwane lokalizacje. Magazyny danych (Azure Storage, Azure SQL Database itp.) i jednostki obliczeniowe (HDInsight itp.) używane przez fabrykę danych mogą mieścić się w innych regionach.
6. Usuń zaznaczenie opcji **Włącz git**.     
7. Kliknij przycisk **Utwórz**.
8. Po zakończeniu wdrażania kliknij pozycję **Przejdź do zasobu** .

   ![Zrzut ekranu przedstawia komunikat informujący, że wdrożenie zostało ukończone, i opcję przejścia do zasobów.](./media/tutorial-incremental-copy-change-data-capture-feature-portal/data-factory-deploy-complete.png)
9. Po zakończeniu tworzenia zostanie wyświetlona strona **Fabryka danych**, jak pokazano na poniższej ilustracji.

   ![Zrzut ekranu przedstawia wdrożoną fabrykę danych.](./media/tutorial-incremental-copy-change-data-capture-feature-portal/data-factory-home-page.png)
10. Kliknij kafelek **Tworzenie i monitorowanie**, aby w osobnej karcie uruchomić interfejs użytkownika usługi Azure Data Factory.
11. Na stronie **Wprowadzenie** przejdź do karty **Edycja** w lewym panelu, jak pokazano na poniższej ilustracji:

    ![Przycisk Utwórz potok](./media/tutorial-incremental-copy-change-data-capture-feature-portal/get-started-page.png)

## <a name="create-linked-services"></a>Tworzenie połączonych usług
Połączone usługi tworzy się w fabryce danych w celu połączenia magazynów danych i usług obliczeniowych z fabryką danych. W tej sekcji utworzysz połączone usługi na koncie usługi Azure Storage i usłudze Azure SQL.

### <a name="create-azure-storage-linked-service"></a>Utwórz połączoną usługę Azure Storage.
W tym kroku opisano łączenie konta usługi Azure Storage z fabryką danych.

1. Kliknij kolejno pozycje **Połączenia** i **+ Nowy**.

   ![Przycisk Nowe połączenie](./media/tutorial-incremental-copy-change-data-capture-feature-portal/new-connection-button-storage.png)
2. W oknie **Nowa połączona usługa** wybierz pozycję **Azure Blob Storage**, a następnie kliknij pozycję **Kontynuuj**.

   ![Wybieranie pozycji Azure Blob Storage](./media/tutorial-incremental-copy-change-data-capture-feature-portal/select-azure-storage.png)
3. W oknie **Nowa połączona usługa** wykonaj następujące czynności:

   1. Wprowadź wartość **AzureStorageLinkedService** w polu **Nazwa**.
   2. Wybierz swoje konto usługi Azure Storage w polu **Nazwa konta magazynu**.
   3. Kliknij przycisk **Zapisz**.

   ![Ustawienia konta usługi Azure Storage](./media/tutorial-incremental-copy-change-data-capture-feature-portal/azure-storage-linked-service-settings.png)


### <a name="create-azure-sql-mi-database-linked-service"></a>Utwórz połączoną usługę bazy danych Azure SQL.
W tym kroku połączysz bazę danych usługi Azure SQL MI z fabryką danych.

> [!NOTE]
> Aby uzyskać informacje dotyczące dostępu za pośrednictwem publicznego punktu końcowego i prywatnego programu SQL, zobacz w [tym miejscu](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-database-managed-instance#prerequisites) . W przypadku korzystania z prywatnego punktu końcowego należy uruchomić ten potok przy użyciu własnego środowiska Integration Runtime. Ta sama wartość zostałaby zastosowana do tych uruchomionych SQL Server w scenariuszach dotyczących maszyn wirtualnych lub sieci wirtualnej.

1. Kliknij kolejno pozycje **Połączenia** i **+ Nowy**.
2. W oknie **Nowa połączona usługa** wybierz pozycję **Azure SQL Database wystąpienie zarządzane**, a następnie kliknij przycisk **Kontynuuj**.
3. W oknie **Nowa połączona usługa** wykonaj następujące czynności:

   1. W polu **Nazwa** wprowadź **AzureSqlMI1** .
   2. W polu **Nazwa serwera** wybierz swój serwer SQL.
   4. Wybierz bazę danych SQL dla pola **Nazwa bazy danych** .
   5. W polu **Nazwa użytkownika** podaj nazwę użytkownika.
   6. W polu **Hasło** podaj hasło użytkownika.
   7. Kliknij pozycję **Testuj połączenie** w celu przetestowania połączenia.
   8. Kliknij przycisk **Zapisz**, aby zapisać połączoną usługę.

   ![Ustawienia usługi połączonej bazy danych Azure SQL](./media/tutorial-incremental-copy-change-data-capture-feature-portal/azure-sql-managed-instance-database-linked-service-settings.png)

## <a name="create-datasets"></a>Tworzenie zestawów danych
W tym kroku utworzysz zestawy danych reprezentujące źródło danych i miejsce docelowe danych.

### <a name="create-a-dataset-to-represent-source-data"></a>Tworzenie zestawu danych reprezentującego źródło danych
W tym kroku utworzysz zestaw danych reprezentujący źródło danych.

1. W widoku drzewa kliknij kolejno pozycje **+ (plus)** i **Zestaw danych**.

   ![Menu Nowy zestaw danych](./media/tutorial-incremental-copy-change-data-capture-feature-portal/new-dataset-menu.png)
2. Wybierz **Azure SQL Database wystąpienie zarządzane**, a następnie kliknij przycisk **Kontynuuj**.

   ![Typ zestawu danych będącego źródłem — Azure SQL Database](./media/tutorial-incremental-copy-change-data-capture-feature-portal/select-azure-sql-database.png)
   
3. Na karcie **Właściwości ustaw** wartość Nazwa zestawu danych i informacje o połączeniu:
 
   1. Wybierz pozycję **AzureSqlMI1** dla **połączonej usługi**.
   2. Wybierz pozycję **[dbo]. [ dbo_customers_CT]** dla **nazwy tabeli**.  Uwaga: Ta tabela została utworzona automatycznie, gdy włączono funkcję przechwytywania zmian w tabeli Customers. Zmiany danych nigdy nie są wysyłane z tej tabeli bezpośrednio, ale zamiast tego są wyodrębniane za pomocą [funkcji przechwytywania](https://docs.microsoft.com/sql/relational-databases/system-functions/change-data-capture-functions-transact-sql?view=sql-server-ver15)zmian.

   ![Połączenie ze źródłem](./media/tutorial-incremental-copy-change-data-capture-feature-portal/source-dataset-configuration.png)

### <a name="create-a-dataset-to-represent-data-copied-to-sink-data-store"></a>Utwórz zestaw danych reprezentujący dane skopiowane do magazynu danych będącego ujściem.
W tym kroku utworzysz zestaw danych reprezentujący dane skopiowane z magazynu danych źródłowych. Kontener Data Lake został utworzony w ramach usługi Azure Blob Storage w ramach wymagań wstępnych. Utwórz kontener, jeśli nie istnieje, lub zmień nazwę istniejącego kontenera. W tym samouczku nazwa pliku wyjściowego jest generowana dynamicznie przy użyciu czasu wyzwalacza, który zostanie później skonfigurowany.

1. W widoku drzewa kliknij kolejno pozycje **+ (plus)** i **Zestaw danych**.

   ![Menu Nowy zestaw danych](./media/tutorial-incremental-copy-change-data-capture-feature-portal/new-dataset-menu.png)
2. Wybierz pozycję **Azure Blob Storage**i kliknij przycisk **Kontynuuj**.

   ![Typ zestawu danych będącego ujściem — Azure Blob Storage](./media/tutorial-incremental-copy-change-data-capture-feature-portal/sink-dataset-type.png)
3. Wybierz pozycję **DelimitedText**, a następnie kliknij przycisk **Kontynuuj**.

   ![Format zestawu danych ujścia — DelimitedText](./media/tutorial-incremental-copy-change-data-capture-feature-portal/sink-dataset-format.png)
4. Na karcie **Właściwości ustaw** wartość Nazwa zestawu danych i informacje o połączeniu:

   1. Wybierz pozycję **AzureStorageLinkedService** w polu **Połączona usługa**.
   2. Wprowadź **RAW** dla części **kontenera** **ścieżki FilePath**.
   3. Włącz **pierwszy wiersz jako nagłówek**
   4. Kliknij przycisk **OK** .

   ![Zestaw danych będący ujściem — połączenie](./media/tutorial-incremental-copy-change-data-capture-feature-portal/sink-dataset-configuration.png)

## <a name="create-a-pipeline-to-copy-the-changed-data"></a>Tworzenie potoku w celu skopiowania zmienionych danych
W tym kroku utworzysz potok, który najpierw sprawdza liczbę zmienionych rekordów znajdujących się w tabeli zmian za pomocą **działania Lookup**. Działanie warunku IF sprawdza, czy liczba zmienionych rekordów jest większa od zera i uruchamia **działanie kopiowania** , aby skopiować wstawione/zaktualizowane/usunięte dane z Azure SQL Database do platformy Azure Blob Storage. Na koniec zostaje skonfigurowany wyzwalacz okna wirowania i godziny rozpoczęcia i zakończenia zostaną przesłane do działań jako parametry okna początkowego i końcowego. 

1. W interfejsie użytkownika Data Factory przejdź do karty **Edycja** . Kliknij pozycję **+ (plus)** w lewym okienku, a następnie kliknij pozycję **potok**.

    ![Menu Nowy potok](./media/tutorial-incremental-copy-change-data-capture-feature-portal/new-pipeline-menu.png)
2. Zostanie wyświetlona nowa karta służąca do konfigurowania potoku. Potok powinien być też widoczny w widoku drzewa. W oknie **Właściwości** zmień nazwę potoku na **IncrementalCopyPipeline**.

    ![Nazwa potoku](./media/tutorial-incremental-copy-change-data-capture-feature-portal/incremental-copy-pipeline-name.png)
3. W przyborniku **Działania** rozwiń pozycję **Ogólne**, a następnie przeciągnij działanie **Lookup** (Wyszukiwanie) i upuść je na powierzchni projektanta potoku. Ustaw nazwę działania na **GetChangeCount**. To działanie Pobiera liczbę rekordów w tabeli zmian dla danego przedziału czasu.

    ![Działanie Lookup (Wyszukiwanie) — nazwa](./media/tutorial-incremental-copy-change-data-capture-feature-portal/first-lookup-activity-name.png)
4. Przejdź do **ustawień** w oknie **Właściwości** :
   1. Określ nazwę zestawu danych SQL i dla pola **zestawu danych źródłowych** .
   2. Wybierz opcję zapytania i wprowadź następujące polecenie w polu zapytania:
    ```sql
    DECLARE  @from_lsn binary(10), @to_lsn binary(10);  
    SET @from_lsn =sys.fn_cdc_get_min_lsn('dbo_customers');  
    SET @to_lsn = sys.fn_cdc_map_time_to_lsn('largest less than or equal',  GETDATE());
    SELECT count(1) changecount FROM cdc.fn_cdc_get_all_changes_dbo_customers(@from_lsn, @to_lsn, 'all')
    ```
   3. Włącz **tylko pierwszy wiersz**

    ![Działanie Lookup (Wyszukiwanie) — ustawienia](./media/tutorial-incremental-copy-change-data-capture-feature-portal/first-lookup-activity-settings.png)
5. Kliknij przycisk **Podgląd danych** , aby upewnić się, że prawidłowe dane wyjściowe są uzyskiwane przez działanie Lookup

    ![Działanie Lookup (wyszukiwanie) — wersja zapoznawcza](./media/tutorial-incremental-copy-change-data-capture-feature-portal/first-lookup-activity-preview.png)
6. Rozwiń **& iteracji** w przyborniku **działania** , a następnie przeciągnij i upuść działanie **if Condition** do powierzchni projektanta potoku. Ustaw nazwę działania na **HasChangedRows**. 

    ![Działanie if Condition-Name](./media/tutorial-incremental-copy-change-data-capture-feature-portal/if-condition-activity-name.png)
7. Przejdź do **działań** w oknie **Właściwości** :

   1. Wprowadź następujące **wyrażenie**
   
    ```adf
    @greater(int(activity('GetChangeCount').output.firstRow.changecount),0)
    ```

   2. Kliknij ikonę ołówka, aby edytować prawdziwy warunek.

   ![Działanie warunkowe — ustawienia](./media/tutorial-incremental-copy-change-data-capture-feature-portal/if-condition-activity-setting.png)

   3. Rozwiń węzeł **Ogólne** w przyborniku **działania** , a następnie przeciągnij i upuść działanie **oczekiwania** na powierzchnię projektanta potoku. To jest działanie tymczasowe w celu debugowania warunku if i zostanie zmienione w dalszej części tego samouczka. 

   ![Jeśli warunek jest prawdziwy — oczekiwanie](./media/tutorial-incremental-copy-change-data-capture-feature-portal/if-condition-activity-wait.png)

   4. Kliknij pasek nawigacyjny IncrementalCopyPipeline, aby powrócić do głównego potoku.

8. Uruchom potok w trybie **debugowania** , aby sprawdzić, czy potok został pomyślnie wykonany. 

   ![Potok — debugowanie](./media/tutorial-incremental-copy-change-data-capture-feature-portal/incremental-copy-pipeline-debug.png)
9. Następnie wróć do kroku true warunek i Usuń działanie **oczekiwania** . W przyborniku **działania** rozwiń pozycję **Przenieś & Przekształć**, a następnie przeciągnij i upuść działanie **kopiowania** do powierzchni projektanta potoku. Ustaw nazwę działania na **IncrementalCopyActivity**. 

   ![Działanie Copy (Kopiowanie) — nazwa](./media/tutorial-incremental-copy-change-data-capture-feature-portal/copy-source-name.png)
10. Przejdź do karty **Źródło** w oknie **Właściwości** i wykonaj następujące czynności:

   1. Określ nazwę zestawu danych SQL i dla pola **zestawu danych źródłowych** . 
   2. Wybierz pozycję **Zapytanie** w polu **Użyj zapytania**.
   3. Wprowadź następujące **zapytanie**.

      ```sql
      DECLARE @from_lsn binary(10), @to_lsn binary(10); 
      SET @from_lsn =sys.fn_cdc_get_min_lsn('dbo_customers'); 
      SET @to_lsn = sys.fn_cdc_map_time_to_lsn('largest less than or equal', GETDATE());
      SELECT * FROM cdc.fn_cdc_get_all_changes_dbo_customers(@from_lsn, @to_lsn, 'all')
      ```

   ![Działanie Copy (Kopiowanie) — ustawienia źródła](./media/tutorial-incremental-copy-change-data-capture-feature-portal/copy-source-settings.png)

11. Kliknij przycisk Podgląd, aby sprawdzić, czy kwerenda zwróci prawidłowo zmienione wiersze.

    ![Zrzut ekranu przedstawia Podgląd do sprawdzenia zapytania.](./media/tutorial-incremental-copy-change-data-capture-feature-portal/copy-source-preview.png)
12. Przejdź do karty **ujścia** i określ zestaw danych usługi Azure Storage dla pola **zestaw danych ujścia** .

    ![Zrzut ekranu przedstawia kartę ujścia.](./media/tutorial-incremental-copy-change-data-capture-feature-portal/copy-sink-settings.png)
13. Kliknij przycisk z powrotem do głównej kanwy potoku i Połącz działanie **Lookup** z działaniem **warunku if** . Przeciągnij **zielony** przycisk dołączony do działania **odnośnik** do działania **Jeśli warunek** .

    ![Łączenie działań Lookup (Wyszukiwanie) i Copy (Kopiowanie)](./media/tutorial-incremental-copy-change-data-capture-feature-portal/connect-lookup-if.png)
14. Na pasku narzędzi kliknij pozycję **Weryfikuj**. Potwierdź, że weryfikacja nie zwróciła błędów. Zamknij okno **Raport weryfikacji potoku**, klikając pozycję **>>**.

    ![Przycisk Weryfikuj](./media/tutorial-incremental-copy-change-data-capture-feature-portal/validate-button.png)
15. Kliknij pozycję Debuguj, aby przetestować potok i sprawdzić, czy plik jest generowany w lokalizacji magazynu.

    ![Debuguj potoku przyrostowe — 2](./media/tutorial-incremental-copy-change-data-capture-feature-portal/incremental-copy-pipeline-debug-2.png)
16. Opublikuj jednostki (połączone usługi, zestawy danych i potoki) do usługi Data Factory, klikając przycisk **Opublikuj wszystko** . Poczekaj na wyświetlenie komunikatu **Publikowanie powiodło się**.

    ![Przycisk Publikuj](./media/tutorial-incremental-copy-change-data-capture-feature-portal/publish-button-2.png)    

### <a name="configure-the-tumbling-window-trigger-and-cdc-window-parameters"></a>Konfigurowanie wyzwalacza okna wirowania i parametrów okna przechwytywania zmian 
W tym kroku utworzysz wyzwalacz okna wirowania, aby uruchomić zadanie zgodnie z częstym harmonogramem. Będziesz używać zmiennych systemowych WindowStart i WindowEnd wyzwalacza okna wirowania i przekazać je jako parametry do potoku w celu użycia w zapytaniu przechwytywania.

1. Przejdź do karty **Parametry** potoku **IncrementalCopyPipeline** i przy użyciu przycisku **+ Nowy** Dodaj dwa parametry (**triggerStartTime** i **triggerEndTime**) do potoku, które będzie reprezentować okno wirowania i godzinę zakończenia. Na potrzeby debugowania Dodaj wartości domyślne w formacie **rrrr-mm-dd HH24: mi: SS. fff** , ale upewnij się, że triggerStartTime nie jest wcześniejsza niż włączona w tabeli, w przeciwnym razie spowoduje to wystąpienie błędu.

    ![Menu Wyzwól teraz](./media/tutorial-incremental-copy-change-data-capture-feature-portal/incremental-copy-pipeline-parameters.png)
2. Kliknij kartę Ustawienia działania **Wyszukiwanie** i skonfiguruj zapytanie tak, aby korzystało z parametrów początkowych i końcowych. Skopiuj następujący do zapytania:
    ```sql
    @concat('DECLARE @begin_time datetime, @end_time datetime, @from_lsn binary(10), @to_lsn binary(10); 
    SET @begin_time = ''',pipeline().parameters.triggerStartTime,''';
    SET @end_time = ''',pipeline().parameters.triggerEndTime,''';
    SET @from_lsn = sys.fn_cdc_map_time_to_lsn(''smallest greater than or equal'', @begin_time);
    SET @to_lsn = sys.fn_cdc_map_time_to_lsn(''largest less than'', @end_time);
    SELECT count(1) changecount FROM cdc.fn_cdc_get_all_changes_dbo_customers(@from_lsn, @to_lsn, ''all'')')
    ```

3. Przejdź do działania **kopiowania** w prawdziwym przypadku działania **warunkowego if** i kliknij kartę **Źródło** . Skopiuj następujący do zapytania:
    ```sql
    @concat('DECLARE @begin_time datetime, @end_time datetime, @from_lsn binary(10), @to_lsn binary(10); 
    SET @begin_time = ''',pipeline().parameters.triggerStartTime,''';
    SET @end_time = ''',pipeline().parameters.triggerEndTime,''';
    SET @from_lsn = sys.fn_cdc_map_time_to_lsn(''smallest greater than or equal'', @begin_time);
    SET @to_lsn = sys.fn_cdc_map_time_to_lsn(''largest less than'', @end_time);
    SELECT * FROM cdc.fn_cdc_get_all_changes_dbo_customers(@from_lsn, @to_lsn, ''all'')')
    ```
4. Kliknij kartę **ujścia** działania **kopiowania** , a następnie kliknij przycisk **Otwórz** , aby edytować właściwości zestawu danych. Kliknij kartę **Parametry** i Dodaj nowy parametr o nazwie **triggerStart**    

    ![Zrzut ekranu przedstawia Dodawanie nowego parametru do karty parametry.](./media/tutorial-incremental-copy-change-data-capture-feature-portal/sink-dataset-configuration-2.png)
5. Następnie skonfiguruj właściwości zestawu danych w celu przechowywania danych w podkatalogu **klienci/przyrostowy** z partycjami opartymi na dacie.
   1. Kliknij kartę **połączenie** we właściwościach zestawu danych i Dodaj zawartość dynamiczną zarówno dla **katalogu** , jak i **plików** . 
   2. W sekcji **katalog** wprowadź następujące wyrażenie, klikając link zawartość dynamiczna w obszarze pola tekstowego:
    
    ```sql
    @concat('customers/incremental/',formatDateTime(dataset().triggerStart,'yyyy/MM/dd'))
    ```
   3. W sekcji **plik** wprowadź następujące wyrażenie. Spowoduje to utworzenie nazw plików opartych na dacie i godzinie uruchomienia wyzwalacza z rozszerzeniem CSV:
    
    ```sql
    @concat(formatDateTime(dataset().triggerStart,'yyyyMMddHHmmssfff'),'.csv')
    ```
    ![Konfiguracja zestawu danych ujścia — 3](./media/tutorial-incremental-copy-change-data-capture-feature-portal/sink-dataset-configuration-3.png)

   4. Przejdź wstecz do ustawień **ujścia** w działaniu **kopiowania** , klikając kartę **IncrementalCopyPipeline** . 
   5. Rozwiń Właściwości zestawu danych i wprowadź zawartość dynamiczną w wartości parametru triggerStart, używając następującego wyrażenia:
     ```sql
     @pipeline().parameters.triggerStartTime
     ```
    ![Konfiguracja zestawu danych ujścia — 4](./media/tutorial-incremental-copy-change-data-capture-feature-portal/sink-dataset-configuration-4.png)

6. Kliknij pozycję Debuguj, aby przetestować potok i upewnić się, że struktura folderów i plik wyjściowy są generowane zgodnie z oczekiwaniami. Pobierz plik i otwórz go, aby zweryfikować jego zawartość. 

    ![Debugowanie przyrostowe — 3](./media/tutorial-incremental-copy-change-data-capture-feature-portal/incremental-copy-pipeline-debug-3.png)
7. Upewnij się, że parametry są wstrzykiwane do zapytania, przeglądając parametry wejściowe uruchomienia potoku.

    ![Debugowanie przyrostowe — 4](./media/tutorial-incremental-copy-change-data-capture-feature-portal/incremental-copy-pipeline-debug-4.png)
8. Opublikuj jednostki (połączone usługi, zestawy danych i potoki) do usługi Data Factory, klikając przycisk **Opublikuj wszystko** . Poczekaj na wyświetlenie komunikatu **Publikowanie powiodło się**.
9. Na koniec Skonfiguruj wyzwalacz okna wirowania, aby uruchomić potok w regularnych odstępach czasu i ustawić parametry czasu rozpoczęcia i zakończenia. 
   1. Kliknij przycisk **Dodaj wyzwalacz** , a następnie wybierz pozycję **Nowy/Edytuj** .

   ![Dodaj nowy wyzwalacz](./media/tutorial-incremental-copy-change-data-capture-feature-portal/add-trigger.png)

   2. Wprowadź nazwę wyzwalacza i określ godzinę rozpoczęcia, która jest równa godzinie zakończenia okna debugowania powyżej.

   ![Wyzwalacz okna wirowania](./media/tutorial-incremental-copy-change-data-capture-feature-portal/tumbling-window-trigger.png)

   3. Na następnym ekranie określ odpowiednio następujące wartości parametrów początkowych i końcowych.
    ```sql
    @formatDateTime(trigger().outputs.windowStartTime,'yyyy-MM-dd HH:mm:ss.fff')
    @formatDateTime(trigger().outputs.windowEndTime,'yyyy-MM-dd HH:mm:ss.fff')
    ```

   ![Wyzwalacz okna wirowania-2](./media/tutorial-incremental-copy-change-data-capture-feature-portal/tumbling-window-trigger-2.png)

> [!NOTE]
> Uwaga wyzwalacz zostanie uruchomiony tylko po opublikowaniu. Dodatkowo oczekiwane zachowanie okna wirowania polega na uruchomieniu wszystkich okresów historycznych od daty rozpoczęcia do chwili obecnej. Więcej informacji na temat wyzwalaczy okna wirowania można znaleźć [tutaj](https://docs.microsoft.com/azure/data-factory/how-to-create-tumbling-window-trigger). 
  
10. Za pomocą **SQL Server Management Studio** wprowadzić dodatkowe zmiany w tabeli klienta, uruchamiając następujące SQL:
    ```sql
    insert into customers (customer_id, first_name, last_name, email, city) values (4, 'Farlie', 'Hadigate', 'fhadigate3@zdnet.com', 'Reading');
    insert into customers (customer_id, first_name, last_name, email, city) values (5, 'Anet', 'MacColm', 'amaccolm4@yellowbook.com', 'Portsmouth');
    insert into customers (customer_id, first_name, last_name, email, city) values (6, 'Elonore', 'Bearham', 'ebearham5@ebay.co.uk', 'Portsmouth');
    update customers set first_name='Elon' where customer_id=6;
    delete from customers where customer_id=5;
    ```
11. Kliknij przycisk **Opublikuj wszystko** . Poczekaj na wyświetlenie komunikatu **Publikowanie powiodło się**.  
12. Po kilku minutach zostanie wyzwolony potok, a nowy plik zostanie załadowany do usługi Azure Storage


### <a name="monitor-the-incremental-copy-pipeline"></a>Monitorowanie potoku kopiowania przyrostowego
1. Kliknij kartę **Monitorowanie** po lewej stronie. Na liście zostanie wyświetlone uruchomienie potoku i jego stan. Aby odświeżyć listę, kliknij pozycję **Odśwież**. Umieść kursor w górnej części nazwy potoku, aby uzyskać dostęp do raportu ponowne uruchomienie i raport użycia.

    ![Uruchomienia potoków](./media/tutorial-incremental-copy-change-data-capture-feature-portal/copy-pipeline-runs.png)
2. Aby wyświetlić uruchomienia działań skojarzone z uruchomieniem potoku, kliknij nazwę potoku. W przypadku wykrycia zmienionych danych istnieją trzy działania z uwzględnieniem działania kopiowania, w przeciwnym razie na liście będą znajdować się tylko dwa wpisy. Aby przełączyć się z powrotem do widoku uruchomienia potoków, kliknij link **wszystkie potoki** u góry.

    ![Uruchomienia działania](./media/tutorial-incremental-copy-change-data-capture-feature-portal/copy-activity-runs.png)


### <a name="review-the-results"></a>Sprawdzanie wyników
W folderze `customers/incremental/YYYY/MM/DD` kontenera `raw` widoczny będzie drugi plik.

![Plik wyjściowy z kopii przyrostowej](media/tutorial-incremental-copy-change-data-capture-feature-portal/incremental-copy-pipeline-run.png)
 

## <a name="next-steps"></a>Następne kroki
Przejdź do poniższego samouczka, aby dowiedzieć się więcej o kopiowaniu nowych i zmienionych plików tylko na podstawie ich daty ostatniej modyfikacji:

> [!div class="nextstepaction"]
>[Kopiuj nowe pliki według LastModifiedDate](tutorial-incremental-copy-lastmodified-copy-data-tool.md)
