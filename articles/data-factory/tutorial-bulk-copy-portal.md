---
title: Kopiowanie danych zbiorczo za pomocą witryny Azure portal
description: Dowiedz się, jak używać usługi Azure Data Factory i działania kopiowania do zbiorczego kopiowania danych ze źródłowego magazynu danych do docelowego magazynu danych.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 02/27/2020
ms.openlocfilehash: 04469fa1bd0473710d9fa0bf0190c6459f1f8a07
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81418783"
---
# <a name="copy-multiple-tables-in-bulk-by-using-azure-data-factory"></a>Zbiorcze kopiowanie wielu tabel przy użyciu usługi Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym samouczku pokazano **kopiowanie wielu tabel z usługi Azure SQL Database do usługi Azure Synapse Analytics (dawniej SQL DW).** Tego samego wzorca można użyć także w innych scenariuszach kopiowania. Na przykład kopiowanie tabel z programu SQL Server/Oracle do usługi Azure SQL Database/Azure Synapse Analytics (dawniej SQL DW)/Azure Blob, kopiowanie różnych ścieżek z obiektu Blob do tabel bazy danych SQL azure.

> [!NOTE]
> - Jeśli jesteś nowym użytkownikiem usługi Azure Data Factory, zobacz [Wprowadzenie do usługi Azure Data Factory](introduction.md).

Na poziomie ogólnym ten samouczek obejmuje następujące kroki:

> [!div class="checklist"]
> * Tworzenie fabryki danych.
> * Tworzenie usługi Azure SQL Database, Usługi Azure Synapse Analytics (dawniej SQL DW) i usług połączonych usługi Azure Storage.
> * Tworzenie zestawów danych usługi Azure SQL Database i Azure Synapse Analytics (dawniej SQL DW).
> * Tworzenie potoku w celu wyszukania tabel do skopiowania i innego potoku w celu wykonania samej operacji kopiowania. 
> * Uruchom potok.
> * Monitorowanie uruchomień potoku i działań.

W tym samouczku jest używana witryna Azure Portal. Aby dowiedzieć się więcej o zastosowaniu innych narzędzi/zestawów SDK do tworzenia fabryki danych, zapoznaj się z przewodnikami [Szybki start](quickstart-create-data-factory-dot-net.md). 

## <a name="end-to-end-workflow"></a>Kompletny przepływ pracy
W tym scenariuszu masz kilka tabel w usłudze Azure SQL Database, które chcesz skopiować do usługi Azure Synapse Analytics (dawniej SQL DW). Oto sekwencja logiczna kroków przepływu pracy, który następuje w potokach:

![Przepływ pracy](media/tutorial-bulk-copy-portal/tutorial-copy-multiple-tables.png)

* Pierwszy potok wyszukuje listę tabel, które należy skopiować do magazynów danych ujścia.  Alternatywnie można utrzymywać tabelę metadanych, która zawiera listę wszystkich tabel do skopiowania do magazynu danych ujścia. Następnie potok wywołuje inny potok, który działa na wszystkich tabelach w bazie danych i wykonuje operację kopiowania danych.
* Drugi potok przeprowadza rzeczywiste kopiowanie. Pobiera listę tabel jako parametr. Dla każdej tabeli na liście skopiuj określoną tabelę w usłudze Azure SQL Database do odpowiedniej tabeli w usłudze Azure Synapse Analytics (dawniej SQL DW) przy użyciu [kopii etapowej za pośrednictwem magazynu obiektów Blob i PolyBase](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse) w celu uzyskania najlepszej wydajności. W tym przykładzie pierwszy potok przekazuje listę tabel jako wartość parametru. 

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne
* **Konto usługi Azure Storage**. Konto usługi Azure Storage jest używane jako przejściowy magazyn obiektów blob w operacji kopiowania zbiorczego. 
* **Baza danych SQL platformy Azure**. Ta baza danych zawiera dane źródłowe. 
* **Usługa Azure Synapse Analytics (dawniej SQL DW)**. Ten magazyn danych służy do przechowywania danych skopiowanych z bazy SQL Database. 

### <a name="prepare-sql-database-and-azure-synapse-analytics-formerly-sql-dw"></a>Przygotowanie bazy danych SQL i usługi Azure Synapse Analytics (dawniej SQL DW)

**Przygotowywanie źródłowej bazy Azure SQL Database**:

Utwórz bazę Azure SQL Database z przykładowymi danymi Adventure Works LT zgodnie z artykułem [Tworzenie bazy danych Azure SQL Database](../sql-database/sql-database-get-started-portal.md). Ten samouczek kopiuje wszystkie tabele z tej przykładowej bazy danych do usługi Azure Synapse Analytics (dawniej SQL DW).

**Przygotowanie ujścia usługi Azure Synapse Analytics (dawniej SQL DW)**:

1. Jeśli nie masz usługi Azure Synapse Analytics (dawniej SQL DW), zobacz Tworzenie magazynu [danych SQL artykuł](../sql-data-warehouse/sql-data-warehouse-get-started-tutorial.md) kroki, aby go utworzyć.

1. Utwórz odpowiednie schematy tabel w usłudze Azure Synapse Analytics (dawniej SQL DW). Usługa Azure Data Factory służy do migrowania/kopiowania danych na późniejszym etapie.

## <a name="azure-services-to-access-sql-server"></a>Usługi platformy Azure umożliwiające dostęp do serwera SQL

Zarówno dla bazy danych SQL i usługi Azure Synapse Analytics (dawniej SQL DW) zezwalaj usługom platformy Azure na dostęp do serwera SQL. Upewnij się, że zezwalaj na dostęp do tego ustawienia **serwera w usłudze Azure usługi i zasoby,** jest **włączone** dla serwera SQL platformy Azure. To ustawienie umożliwia usłudze Data Factory odczytywanie danych z bazy danych SQL azure i zapisywanie danych w usłudze Azure Synapse Analytics (dawniej SQL DW). 

Aby zweryfikować i włączyć to ustawienie, przejdź do serwera SQL platformy Azure > zapory > zabezpieczeń i sieci wirtualne, > ustawić **dostęp do tego serwera na włączony,** aby umożliwić dostęp do usług i zasobów platformy Azure na **włączony**.

## <a name="create-a-data-factory"></a>Tworzenie fabryki danych
1. Uruchom przeglądarkę internetową **Microsoft Edge** lub **Google Chrome**. Obecnie interfejs użytkownika usługi Data Factory jest obsługiwany tylko przez przeglądarki internetowe Microsoft Edge i Google Chrome.
1. Przejdź do [witryny Azure portal](https://portal.azure.com). 
1. Po lewej stronie menu portalu platformy Azure wybierz pozycję **Utwórz fabrykę** > **danych****analizy** > zasobów . 
   ![Wybór usługi Data Factory w okienku „Nowy”](./media/doc-common-process/new-azure-data-factory-menu.png)
1. Na stronie **Nowa fabryka danych** wprowadź nazwę **ADFTutorialBulkCopyDF** **.** 
 
   Nazwa fabryki danych platformy Azure musi być **unikatowa globalnie.** Jeśli dla pola nazwy wystąpi poniższy błąd, zmień nazwę fabryki danych (np. twojanazwaADFTutorialBulkCopyDF). Artykuł [Data Factory — Naming Rules (Usługa Data Factory — reguły nazewnictwa)](naming-rules.md) zawiera reguły nazewnictwa artefaktów usługi Data Factory.
  
       `Data factory name "ADFTutorialBulkCopyDF" is not available`
1. Wybierz **subskrypcję** Azure, w której chcesz utworzyć fabrykę danych. 
1. Dla opcji **Grupa zasobów** wykonaj jedną z następujących czynności:
     
   - Wybierz pozycję **Użyj istniejącej**, a następnie wybierz istniejącą grupę zasobów z listy rozwijanej. 
   - Wybierz **pozycję Utwórz nowy**i wprowadź nazwę grupy zasobów.   
         
     Informacje na temat grup zasobów znajdują się w artykule [Using resource groups to manage your Azure resources](../azure-resource-manager/management/overview.md) (Używanie grup zasobów do zarządzania zasobami platformy Azure).  
1. Wybierz opcję **V2** w obszarze **Wersja**.
1. Na liście **lokalizacja** wybierz lokalizację fabryki danych. Aby uzyskać listę regionów platformy Azure, w których obecnie jest dostępna usługa Data Factory, wybierz dane regiony na poniższej stronie, a następnie rozwiń węzeł **Analiza**, aby zlokalizować pozycję **Data Factory**: [Produkty dostępne według regionu](https://azure.microsoft.com/global-infrastructure/services/). Magazyny danych (Azure Storage, Azure SQL Database itp.) i jednostki obliczeniowe (HDInsight itp.) używane przez fabrykę danych mogą mieścić się w innych regionach.
1. Kliknij przycisk **Utwórz**.
1. Po zakończeniu tworzenia wybierz pozycję **Przejdź do zasobu,** aby przejść do strony **Fabryka danych.** 
   
1. Kliknij kafelek **Tworzenie i monitorowanie**, aby w osobnej karcie uruchomić aplikację interfejsu użytkownika usługi Data Factory.
1. Na stronie **Rozpocznijmy pracę** przełącz się na kartę **Autor** w lewym panelu, jak pokazano na poniższej ilustracji:

     ![Strona Wprowadzenie](./media/doc-common-process/get-started-page-author-button.png)

## <a name="create-linked-services"></a>Tworzenie połączonych usług
Połączone usługi są tworzone w celu połączenia magazynów danych i obliczeń z fabryką danych. Połączona usługa ma informacje o połączeniu, których usługa Data Factory używa do nawiązywania połączenia z magazynem danych w środowisku uruchomieniowym. 

W tym samouczku należy połączyć swoją bazę danych SQL Azure, usługę Azure Synapse Analytics (dawniej SQL DW) i magazyny danych usługi Azure Blob Storage z fabryką danych. Usługa Azure SQL Database to magazyn danych będący źródłem. Usługa Azure Synapse Analytics (dawniej SQL DW) jest magazynem danych ujścia/miejsca docelowego. Usługa Azure Blob Storage jest etap danych przed załadowaniem danych do usługi Azure Synapse Analytics (dawniej SQL DW) przy użyciu PolyBase. 

### <a name="create-the-source-azure-sql-database-linked-service"></a>Tworzenie źródłowej połączonej usługi Azure SQL Database
W tym kroku utworzysz połączoną usługę służącą do łączenia bazy danych Azure SQL Database z fabryką danych. 

1. Kliknij pozycję **Połączenia** u dołu okna, a następnie kliknij przycisk **+ Nowy** na pasku narzędzi (Przycisk**Połączenia** znajduje się u dołu lewej kolumny w obszarze **Zasoby fabryczne).** 

1. W oknie **Nowa połączona usługa** wybierz pozycję **Azure SQL Database**, a następnie kliknij pozycję **Kontynuuj**. 
1. W oknie **Nowa usługa połączona (Azure SQL Database)** wykonaj następujące czynności: 

    a. Wprowadź wartość **AzureSqlDatabaseLinkedService** w polu **Nazwa**.
    
    b. W polu **Nazwa serwera** wybierz swój serwer usługi Azure SQL.
    
    d. W polu **Nazwa bazy danych** wybierz swoją bazę danych Azure SQL Database. 
    
    d. Wprowadź **nazwę użytkownika**, aby nawiązać połączenie z bazą danych Azure SQL Database. 
    
    e. Wprowadź **hasło** dla użytkownika. 

    f. Aby przetestować połączenie z bazą danych Azure SQL Database przy użyciu określonych informacji, kliknij pozycję **Testuj połączenie**.
  
    g. Kliknij **przycisk Utwórz,** aby zapisać usługę połączony.


### <a name="create-the-sink-azure-synapse-analytics-formerly-sql-dw-linked-service"></a>Tworzenie usługi połączonej usługi Azure Synapse Analytics (dawniej SQL DW)

1. Na karcie **Połączenia** kliknij ponownie pozycję **+ Nowy** na pasku narzędzi. 
1. W oknie **Nowa usługa połączona** wybierz pozycję **Azure Synapse Analytics (dawniej SQL DW)** i kliknij przycisk **Kontynuuj**. 
1. W oknie **Nowa usługa połączona (Usługa Azure Synapse Analytics (dawniej SQL DW))** wykonaj następujące czynności: 
   
    a. Wprowadź wartość **AzureSqlDWLinkedService** w polu **Nazwa**.
     
    b. W polu **Nazwa serwera** wybierz swój serwer usługi Azure SQL.
     
    d. W polu **Nazwa bazy danych** wybierz swoją bazę danych Azure SQL Database. 
     
    d. Wprowadź **nazwę użytkownika,** aby połączyć się z bazą danych SQL platformy Azure. 
     
    e. Wprowadź **hasło** użytkownika. 
     
    f. Aby przetestować połączenie z bazą danych Azure SQL Database przy użyciu określonych informacji, kliknij pozycję **Testuj połączenie**.
     
    g. Kliknij przycisk **Utwórz**.

### <a name="create-the-staging-azure-storage-linked-service"></a>Tworzenie przejściowej połączonej usługi Azure Storage
W tym samouczku magazyn obiektów blob platformy Azure służy jako obszar przejściowy, pozwalający na włączenie programu PolyBase w celu podniesienia wydajności kopiowania.

1. Na karcie **Połączenia** kliknij ponownie pozycję **+ Nowy** na pasku narzędzi. 
1. W oknie **Nowa połączona usługa** wybierz pozycję **Azure Blob Storage**, a następnie kliknij pozycję **Kontynuuj**. 
1. W oknie **Nowa usługa połączona (usługa Azure Blob Storage)** wykonaj następujące czynności: 

    a. Wprowadź wartość **AzureStorageLinkedService** w polu **Nazwa**.                                                 
    b. Wybierz swoje **konto usługi Azure Storage** w polu **Nazwa konta magazynu**.
    
    d. Kliknij przycisk **Utwórz**.


## <a name="create-datasets"></a>Tworzenie zestawów danych
W tym samouczku utworzysz zestawy danych będące źródłem i ujściem, określające lokalizację przechowywania danych. 

Wejściowy zestaw danych **AzureSqlDatabaseDataset** odwołuje się do elementu **AzureSqlDatabaseLinkedService**. Połączona usługa określa parametry połączenia w celu nawiązania połączenia z bazą danych. Zestaw danych określa nazwę bazy danych i tabelę, która zawiera dane źródłowe. 

Wyjściowy zestaw danych **AzureSqlDWDataset** odwołuje się do elementu **AzureSqlDWLinkedService**. Usługa połączona określa parametry połączenia, aby połączyć się z usługą Azure Synapse Analytics (dawniej SQL DW). Zestaw danych określa bazę danych i tabelę, do którego dane są kopiowane. 

W tym samouczku źródłowe i docelowe tabele SQL nie są ustalone w definicjach zestawów danych. W zamian działanie ForEach przekazuje nazwę tabeli w czasie wykonywania do działania kopiowania. 

### <a name="create-a-dataset-for-source-sql-database"></a>Tworzenie zestawu danych źródłowej bazy danych SQL Database

1. Kliknij **przycisk + (plus)** w lewym okienku, a następnie kliknij pozycję **Zestaw danych**. 

    ![Menu Nowy zestaw danych](./media/tutorial-bulk-copy-portal/new-dataset-menu.png)
1. W oknie **Nowy zestaw danych** wybierz pozycję Azure SQL **Database**, a następnie kliknij przycisk **Kontynuuj**. 
    
1. W oknie **Ustaw właściwości** w obszarze **Nazwa**wprowadź pozycję **AzureSqlDatabaseDataset**. W obszarze **Usługa połączona**wybierz pozycję **AzureSqlDatabaseLinkedService**. Następnie kliknij przycisk **OK**.

1. Przełącz się na kartę **Połączenie,** wybierz dowolną tabelę dla **tabeli**. Jest to tabela fikcyjna. Zapytanie dotyczące źródłowego zestawu danych jest określane podczas tworzenia potoku. Zapytanie służy do wyodrębniania danych z bazy danych Azure SQL Database. Alternatywnie można kliknąć pole wyboru **Edytuj** i wprowadzić **nazwę dbo.dummyName** jako nazwę tabeli. 
 

### <a name="create-a-dataset-for-sink-azure-synapse-analytics-formerly-sql-dw"></a>Tworzenie zestawu danych dla ujścia usługi Azure Synapse Analytics (dawniej SQL DW)

1. Kliknij pozycję **+ (plus)** w lewym okienku, a następnie kliknij pozycję **Zestaw danych**. 
1. W oknie **Nowy zestaw danych** wybierz pozycję Azure **Synapse Analytics (dawniej SQL DW),** a następnie kliknij przycisk **Kontynuuj**.
1. W oknie **Ustaw właściwości** w obszarze **Nazwa**wprowadź pozycję **AzureSqlDWDataset**. W obszarze **Usługa połączona**wybierz pozycję **AzureSqlDWLinkedService**. Następnie kliknij przycisk **OK**.
1. Przejdź do karty **Parametry**, a następnie kliknij pozycję **+ Nowy** i wprowadź ciąg **DWTableName** jako nazwę parametru. Jeśli skopiujesz/wklejasz tę nazwę ze strony, upewnij się, że na końcu **pliku DWTableName**nie ma **znaku spacji końcowego.**
1. Przejdź do karty **Połączenie**. 

    a. W obszarze **Tabela**sprawdź opcję **Edytuj.** Wprowadź **dbo** w polu wprowadzania nazwy pierwszej tabeli. A następnie wybierz drugie pole wprowadzania i kliknij link **Dodaj zawartość dynamiczną** poniżej. 

    ![Nazwa tabeli połączenia zestawu danych](./media/tutorial-bulk-copy-portal/dataset-connection-tablename.png)

    b. Na stronie **Dodawanie zawartości dynamicznej** kliknij **pozycję DWTAbleName** w obszarze **Parametry**, `@dataset().DWTableName`które automatycznie wypełni pole tekstowe górnego wyrażenia, a następnie kliknij przycisk **Zakończ**. Właściwość **tableName** zestawu danych jest ustawiana na wartość przekazywaną jako argument parametru **DWTableName**. Działanie ForEach iteruje w obrębie listy tabel i przekazuje je po jednej do działania Copy (Kopiowanie). 

    ![Konstruktor parametrów zestawu danych](./media/tutorial-bulk-copy-portal/dataset-parameter-builder.png)
 
## <a name="create-pipelines"></a>Tworzenie potoków
W tym samouczku tworzysz dwa potoki: **IterateAndCopySQLTables** i **GetTableListAndTriggerCopyData**. 

Potok **GetTableListAndTriggerCopyData** wykonuje dwie akcje:

* Wyszukuje tabelę systemową bazy Azure SQL Database w celu pobrania listy tabel do skopiowania.
* Wyzwala potok **IteracjiAndCopySQLTables** do wykonania rzeczywistej kopii danych.

**IteracjiAndCopySQLTables** potoku przyjmuje listę tabel jako parametr. Dla każdej tabeli na liście kopiuje dane z tabeli w usłudze Azure SQL Database do usługi Azure Synapse Analytics (dawniej SQL DW) przy użyciu kopii etapowej i PolyBase.

### <a name="create-the-pipeline-iterateandcopysqltables"></a>Tworzenie potoku IterateAndCopySQLTables

1. W lewym okienku kliknij pozycję **+ (plus)**, a następnie kliknij pozycję **Potok**.

    ![Menu Nowy potok](./media/tutorial-bulk-copy-portal/new-pipeline-menu.png)
1. Na karcie **Ogólne** określ jako nazwę wartość **IterateAndCopySQLTables**. 

1. Przejdź do karty **Parametry** i wykonaj następujące czynności: 

    a. Kliknij pozycję **+ Nowy**. 
    
    b. Wprowadź **tableList** dla parametru **Nazwa**.
    
    d. Wybierz wartość **Array** w polu **Typ**.

1. W przyborniku **Działania** rozwiń pozycję **Iteracja i warunki**, a następnie przeciągnij i upuść działanie **ForEach** do powierzchni projektu potoku. Możesz również wyszukać działania w przyborniku **Działania**. 

    a. Na karcie **Ogólne** u dołu wprowadź wartość **IterateSQLTables** w polu **Nazwa**. 

    b. Przełącz się na kartę **Ustawienia,** kliknij pole wprowadzania **dla elementów,** a następnie kliknij poniżej łącze **Dodaj zawartość dynamiczną.** 

    d. Na stronie **Dodawanie zawartości dynamicznej** zwiń **sekcję Zmienne** i **funkcje** systemowe kliknij **tabelęNaświetlenie** `@pipeline().parameter.tableList`w obszarze **Parametry**, które automatycznie wypełni pole tekstowe górnego wyrażenia jako . Następnie kliknij przycisk **Zakończ**. 

    ![Konstruktor parametru ForEach](./media/tutorial-bulk-copy-portal/for-each-parameter-builder.png)
    
    d. Przełącz **się** do aktywności kartę, kliknij **ikonę ołówka,** aby dodać aktywność podrzędną do **forEach** działania.
    ![Budowniczy aktywności Foreach](./media/tutorial-bulk-copy-portal/for-each-activity-builder.png)

1. W **przyborniku Działania** rozwiń pozycję Przenieś **& transfer**i przeciągnij i upuść **Kopiuj** dane na powierzchnię projektanta potoku. Zwróć uwagę na menu linków do stron nadrzędnych w górnej części. **IteracjiAndCopySQLTable** jest nazwa potoku i **IteracjiSQLTables** jest ForEach nazwę działania. Projektant należy do zakresu działania. Aby przełączyć się z powrotem do edytora potoku z edytora ForEach, można kliknąć łącze w menu naciągu. 

    ![Kopiowanie w działaniu ForEach](./media/tutorial-bulk-copy-portal/copy-in-for-each.png)

1. Przejdź do karty **Źródło** i wykonaj następujące czynności:

    1. Wybierz wartość **AzureSqlDatabaseDataset** w polu **Źródło danych**. 
    1. Wybierz opcję **Kwerenda** dla **użyj kwerendy**. 
    1. Kliknij pole wprowadzania **Zapytanie** -> wybierz pozycję **Dodaj zawartość dynamiczną** poniżej -> wprowadź poniższe wyrażenie w polu **Zapytanie** -> wybierz przycisk **Zakończ**.

        ```sql
        SELECT * FROM [@{item().TABLE_SCHEMA}].[@{item().TABLE_NAME}]
        ``` 


1. Przejdź do karty **Ujście** i wykonaj następujące czynności: 

    1. Wybierz pozycję **AzureSqlDWDataset** w polu **Zestaw danych będący ujściem**.
    1. Kliknij pole wprowadzania dla wartości parametru DWTableName -> wybierz opcję `[@{item().TABLE_SCHEMA}].[@{item().TABLE_NAME}]` Dodaj zawartość **dynamiczną** poniżej, wprowadź wyrażenie jako skrypt, -> wybierz opcję **Zakończ**.
    1. W obszarze Kopiuj metodę wybierz pozycję **PolyBase**. 
    1. Wyczyść opcję **Użyj domyślnego typu.** 
    1. Kliknij pole wprowadzania **Skrypt wstępnego kopiowania** -> wybierz pozycję **Dodaj zawartość dynamiczną** poniżej -> wprowadź poniższe wyrażenie jako skrypt -> wybierz przycisk **Zakończ**. 

        ```sql
        TRUNCATE TABLE [@{item().TABLE_SCHEMA}].[@{item().TABLE_NAME}]
        ```

        ![Ustawienia ujścia kopiowania](./media/tutorial-bulk-copy-portal/copy-sink-settings.png)
1. Przejdź do karty **Ustawienia** i wykonaj następujące czynności: 

    1. Zaznacz pole wyboru **Włącz przemieszczanie**.
    1. Wybierz wartość **AzureStorageLinkedService** w polu **Połączona usługa konta magazynu**.

1. Aby zweryfikować ustawienia potoku, kliknij pozycję **Weryfikuj** na górnym pasku narzędzi dla potoku. Upewnij się, że nie ma błędu sprawdzania poprawności. Aby zamknąć okno **Raport weryfikacji potoku**, kliknij pozycję **>>**.

### <a name="create-the-pipeline-gettablelistandtriggercopydata"></a>Tworzenie potoku GetTableListAndTriggerCopyData

Ten potok wykonuje dwie akcje:

* Wyszukuje tabelę systemową bazy Azure SQL Database w celu pobrania listy tabel do skopiowania.
* Wyzwala potok „IterateAndCopySQLTables”, który przeprowadza rzeczywiste kopiowanie danych.

1. W lewym okienku kliknij pozycję **+ (plus)**, a następnie kliknij pozycję **Potok**.
1. Na karcie **Ogólne** zmień nazwę potoku na **GetTableListAndTriggerCopyData**. 

1. W **przyborniku Działania** rozwiń rozwiń **Ogólne**i przeciągnij i upuść działanie **odnośnika** na powierzchni projektanta potoku i wykonaj następujące czynności:

    1. Wprowadź wartość **LookupTableList** w polu **Nazwa**. 
    1. Wprowadź wartość **Pobieranie listy tabel z bazy danych Azure SQL Database** w polu **Opis**.

1. Przejdź do karty **Ustawienia** i wykonaj następujące czynności:

    1. Wybierz wartość **AzureSqlDatabaseDataset** w polu **Źródło danych**. 
    1. Wybierz **pozycję Kwerenda** do **użycia kwerendy**. 
    1. W obszarze **Zapytanie** wprowadź następujące zapytanie SQL.

        ```sql
        SELECT TABLE_SCHEMA, TABLE_NAME FROM information_schema.TABLES WHERE TABLE_TYPE = 'BASE TABLE' and TABLE_SCHEMA = 'SalesLT' and TABLE_NAME <> 'ProductModel'
        ```
    1. Usuń zaznaczenie pola wyboru **Tylko pierwszy wiersz**.

        ![Działanie Lookup (Wyszukiwanie) — strona ustawień](./media/tutorial-bulk-copy-portal/lookup-settings-page.png)
1. Przeciągnij i upuść **Wykonaj działanie potoku** z przybornika Działania do powierzchni projektanta potoku i ustaw nazwę **triggercopy**.

1. Aby **połączyć** działanie **odnośnika** z działaniem **Wykonaj potok,** przeciągnij **zielone pole** dołączone do działania odnośnika po lewej stronie działania Wykonaj potok.

    ![Łączenie działań Lookup (Wyszukiwanie) i Execute Pipeline (Wykonywanie potoku)](./media/tutorial-bulk-copy-portal/connect-lookup-execute-pipeline.png)

1. Przełącz się do karty **Ustawienia** działania **Wykonaj potok** i wykonaj następujące czynności: 

    1. Wybierz wartość **IterateAndCopySQLTables** w polu **Wywołany potok**. 
    1. Rozwiń sekcję **Zaawansowane** i wyczyść pole wyboru **Czekaj po zakończeniu**.
    1. Kliknij pozycję **+ Nowy** w sekcji **Parametry**. 
    1. Wprowadź **tableList** dla **parametru Nazwa**.
    1. Kliknij pole wprowadzania WARTOŚĆ -> wybierz pozycję **Dodaj zawartość dynamiczną** poniżej -> wprowadź `@activity('LookupTableList').output.value` jako wartość nazwy tabeli -> wybierz przycisk **Zakończ**. Ustawiasz listę wyników z działania odnośnika jako dane wejściowe do drugiego potoku. Lista wyników zawiera listę tabel, których dane trzeba skopiować do miejsca docelowego. 

        ![Działanie Execute Pipeline (Wykonywanie potoku) — strona Ustawienia](./media/tutorial-bulk-copy-portal/execute-pipeline-settings-page.png)

1. Aby zweryfikować potok, kliknij pozycję **Weryfikuj** na pasku narzędzi. Potwierdź, że weryfikacja nie zwróciła błędów. Aby zamknąć okno **Raport weryfikacji potoku**, kliknij pozycję **>>**.

1. Aby opublikować jednostki (zestawy danych, potoki itp.) w usłudze Data Factory, kliknij pozycję **Opublikuj wszystko** na górze okna. Poczekaj na pomyślne zakończenie publikowania. 

## <a name="trigger-a-pipeline-run"></a>Wyzwalanie uruchomienia potoku

1. Przejdź do potoku **GetTableListAndTriggerCopyData**, kliknij pozycję **Dodaj wyzwalacz** na górnym pasku narzędzi potoku , a następnie kliknij przycisk **Wyzwalaj teraz**. 

1. Potwierdź przebieg na stronie **Przebieg potoku,** a następnie wybierz pozycję **Zakończ**.

## <a name="monitor-the-pipeline-run"></a>Monitorowanie działania potoku

1. Przełącz się do karty **Monitor.** Kliknij **przycisk Odśwież,** aż zobaczysz uruchomienia dla obu potoków w rozwiązaniu. Kontynuuj odświeżanie listy do momentu wyświetlenia stanu **Powodzenie**. 

1. Aby wyświetlić uruchomienia działania skojarzone z potoku **GetTableListAndTriggerCopyData,** kliknij łącze nazwa potoku. Powinny zostać wyświetlone dwa uruchomienia działania dla tego uruchomienia potoku. 
    ![Monitorowanie przebiegu potoku](./media/tutorial-bulk-copy-portal/monitor-pipeline.png)
1. Aby wyświetlić dane wyjściowe działania **odnośnika,** kliknij **łącze Dane wyjściowe** obok działania w kolumnie **NAZWA DZIAŁANIA.** Okno **Dane wyjściowe** można maksymalizować i przywracać. Po przejrzeniu kliknij znak **X**, aby zamknąć okno **Dane wyjściowe**.

    ```json
    {
        "count": 9,
        "value": [
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "Customer"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "ProductDescription"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "Product"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "ProductModelProductDescription"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "ProductCategory"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "Address"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "CustomerAddress"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "SalesOrderDetail"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "SalesOrderHeader"
            }
        ],
        "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (East US)",
        "effectiveIntegrationRuntimes": [
            {
                "name": "DefaultIntegrationRuntime",
                "type": "Managed",
                "location": "East US",
                "billedDuration": 0,
                "nodes": null
            }
        ]
    }
    ```    
1. Aby przełączyć się z powrotem do widoku **Przebiegi potoku,** kliknij łącze **Wszystkie uruchomienia potoku** u góry menu naciągu. Kliknij **łącza Iteracji icopySQLTables** (w **kolumnie NAZWA POTOKU),** aby wyświetlić przebiegi działania potoku. Należy zauważyć, że istnieje jedno działanie **kopiowania** uruchamiane dla każdej tabeli w danych wyjściowych działania **odnośnika.** 

1. Upewnij się, że dane zostały skopiowane do docelowej usługi Azure Synapse Analytics (dawniej SQL DW) używanej w tym samouczku. 

## <a name="next-steps"></a>Następne kroki
W ramach tego samouczka wykonano następujące procedury: 

> [!div class="checklist"]
> * Tworzenie fabryki danych.
> * Tworzenie usługi Azure SQL Database, Usługi Azure Synapse Analytics (dawniej SQL DW) i usług połączonych usługi Azure Storage.
> * Tworzenie zestawów danych usługi Azure SQL Database i Azure Synapse Analytics (dawniej SQL DW).
> * Tworzenie potoku w celu wyszukania tabel do skopiowania i innego potoku w celu wykonania samej operacji kopiowania. 
> * Uruchom potok.
> * Monitorowanie uruchomień potoku i działań.

Przejdź do poniższego samouczka, aby dowiedzieć się, jak przyrostowo kopiować dane z lokalizacji źródłowej do docelowej:
> [!div class="nextstepaction"]
>[Przyrostowe kopiowanie danych](tutorial-incremental-copy-portal.md)
