---
title: Kopiuj dane zbiorczo przy użyciu Azure Portal
description: Użyj działania Azure Data Factory i Kopiuj, aby skopiować dane ze źródłowego magazynu danych do docelowego magazynu danych.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 01/12/2021
ms.openlocfilehash: 2fcb8f6d22e93f3a95be26b7bc61f3b5226ba090
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/12/2021
ms.locfileid: "98117133"
---
# <a name="copy-multiple-tables-in-bulk-by-using-azure-data-factory-in-the-azure-portal"></a>Kopiuj wiele tabel zbiorczo przy użyciu Azure Data Factory w Azure Portal

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym samouczku przedstawiono **kopiowanie wielu tabel z Azure SQL Database do usługi Azure Synapse Analytics**. Tego samego wzorca można użyć także w innych scenariuszach kopiowania. Na przykład kopiowanie tabel z SQL Server/Oracle do Azure SQL Database/Azure Synapse Analytics/Azure Blob, kopiowanie różnych ścieżek z obiektów BLOB do tabel Azure SQL Database.

> [!NOTE]
> Jeśli jesteś nowym użytkownikiem usługi Azure Data Factory, zobacz [Wprowadzenie do usługi Azure Data Factory](introduction.md).

Na poziomie ogólnym ten samouczek obejmuje następujące kroki:

> [!div class="checklist"]
> * Tworzenie fabryki danych.
> * Twórz połączone usługi Azure SQL Database, Azure Synapse Analytics i Azure Storage.
> * Utwórz zestawy danych Azure SQL Database i usługi Azure Synapse Analytics.
> * Utwórz potok, aby wyszukać tabele, które mają zostać skopiowane, i inny potok, aby wykonać rzeczywistą operację kopiowania. 
> * Uruchom potok.
> * Monitorowanie uruchomień potoku i działań.

W tym samouczku jest używana witryna Azure Portal. Aby dowiedzieć się więcej o zastosowaniu innych narzędzi/zestawów SDK do tworzenia fabryki danych, zapoznaj się z przewodnikami [Szybki start](quickstart-create-data-factory-dot-net.md). 

## <a name="end-to-end-workflow"></a>Kompletny przepływ pracy
W tym scenariuszu masz kilka tabel w Azure SQL Database, które chcesz skopiować do usługi Azure Synapse Analytics. Oto sekwencja logiczna kroków przepływu pracy, który następuje w potokach:

![Przepływ pracy](media/tutorial-bulk-copy-portal/tutorial-copy-multiple-tables.png)

* Pierwszy potok wyszukuje listę tabel, które należy skopiować do magazynów danych ujścia.  Alternatywnie można utrzymywać tabelę metadanych, która zawiera listę wszystkich tabel do skopiowania do magazynu danych ujścia. Następnie potok wywołuje inny potok, który działa na wszystkich tabelach w bazie danych i wykonuje operację kopiowania danych.
* Drugi potok przeprowadza rzeczywiste kopiowanie. Pobiera listę tabel jako parametr. Dla każdej tabeli na liście Skopiuj określoną tabelę w Azure SQL Database do odpowiedniej tabeli w usłudze Azure Synapse Analytics przy użyciu [kopii przygotowanej za pośrednictwem usługi BLOB Storage i bazy danych Base](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-synapse-analytics) , aby uzyskać najlepszą wydajność. W tym przykładzie pierwszy potok przekazuje listę tabel jako wartość parametru. 

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne
* **Konto usługi Azure Storage**. Konto usługi Azure Storage jest używane jako przejściowy magazyn obiektów blob w operacji kopiowania zbiorczego. 
* **Azure SQL Database**. Ta baza danych zawiera dane źródłowe. Utwórz bazę danych w SQL Database z danymi przykładowymi Adventure Works LT poniżej [Utwórz bazę danych w Azure SQL Database](../azure-sql/database/single-database-create-quickstart.md) artykule. Ten samouczek kopiuje wszystkie tabele z tej przykładowej bazy danych do usługi Azure Synapse Analytics.
* **Analiza usługi Azure Synapse**. Ten magazyn danych służy do przechowywania danych skopiowanych z bazy SQL Database. Jeśli nie masz obszaru roboczego analiz usługi Azure Synapse, zobacz artykuł [Rozpoczynanie pracy z usługą Azure Synapse Analytics](..\synapse-analytics\get-started.md) , aby uzyskać instrukcje.

## <a name="azure-services-to-access-sql-server"></a>Usługi platformy Azure umożliwiające dostęp do serwera SQL

W przypadku SQL Database i usługi Azure Synapse Analytics Zezwól usługom platformy Azure na dostęp do programu SQL Server. Upewnij się, **że dla Twojego** serwera włączono **opcję Zezwól usługom i zasobom platformy Azure na dostęp do tego ustawienia serwera** . To ustawienie umożliwia usłudze Data Factory odczytywanie danych z Azure SQL Database i zapisywanie danych w usłudze Azure Synapse Analytics. 

Aby sprawdzić i włączyć to ustawienie, przejdź do serwera > zabezpieczenia > zapory i sieci wirtualne > **Ustaw opcję** **Zezwól usługom i zasobom platformy Azure na dostęp do tego serwera** .

## <a name="create-a-data-factory"></a>Tworzenie fabryki danych

1. Uruchom przeglądarkę internetową **Microsoft Edge** lub **Google Chrome**. Obecnie interfejs użytkownika usługi Data Factory jest obsługiwany tylko przez przeglądarki internetowe Microsoft Edge i Google Chrome.
1. Przejdź do witryny [Azure Portal](https://portal.azure.com). 
1. Po lewej stronie menu Azure Portal wybierz pozycję **Utwórz zasób**  >  **integracja**  >  **Data Factory**. 

   ![Wybór usługi Data Factory w okienku „Nowy”](./media/doc-common-process/new-azure-data-factory-menu.png)
1. Na stronie **Nowa fabryka danych** wprowadź **ADFTutorialBulkCopyDF** w polu **Nazwa**. 
 
   Nazwa fabryki danych Azure musi być **globalnie unikatowa**. Jeśli dla pola nazwy wystąpi poniższy błąd, zmień nazwę fabryki danych (np. twojanazwaADFTutorialBulkCopyDF). Artykuł [Data Factory — Naming Rules (Usługa Data Factory — reguły nazewnictwa)](naming-rules.md) zawiera reguły nazewnictwa artefaktów usługi Data Factory.
  
    ```text
    Data factory name "ADFTutorialBulkCopyDF" is not available
    ```
1. Wybierz **subskrypcję** Azure, w której chcesz utworzyć fabrykę danych. 
1. Dla opcji **Grupa zasobów** wykonaj jedną z następujących czynności:
     
   - Wybierz pozycję **Użyj istniejącej**, a następnie wybierz istniejącą grupę zasobów z listy rozwijanej. 
   - Wybierz pozycję **Utwórz nową**, a następnie wprowadź nazwę grupy zasobów.   
         
     Informacje na temat grup zasobów znajdują się w artykule [Using resource groups to manage your Azure resources](../azure-resource-manager/management/overview.md) (Używanie grup zasobów do zarządzania zasobami platformy Azure).  
1. Wybierz opcję **V2** w obszarze **Wersja**.
1. Na liście **lokalizacja** wybierz lokalizację fabryki danych. Aby uzyskać listę regionów platformy Azure, w których obecnie jest dostępna usługa Data Factory, wybierz dane regiony na poniższej stronie, a następnie rozwiń węzeł **Analiza**, aby zlokalizować pozycję **Data Factory**: [Produkty dostępne według regionu](https://azure.microsoft.com/global-infrastructure/services/). Magazyny danych (Azure Storage, Azure SQL Database itp.) i jednostki obliczeniowe (HDInsight itp.) używane przez fabrykę danych mogą mieścić się w innych regionach.
1. Kliknij pozycję **Utwórz**.
1. Po zakończeniu tworzenia wybierz pozycję **Przejdź do zasobu** , aby przejść do strony **Data Factory** . 
   
1. Kliknij kafelek **Tworzenie i monitorowanie**, aby w osobnej karcie uruchomić aplikację interfejsu użytkownika usługi Data Factory.


## <a name="create-linked-services"></a>Tworzenie połączonych usług
Połączone usługi są tworzone w celu połączenia magazynów danych i obliczeń z fabryką danych. Połączona usługa ma informacje o połączeniu, których usługa Data Factory używa do nawiązywania połączenia z magazynem danych w środowisku uruchomieniowym. 

W ramach tego samouczka nawiążesz połączenie Azure SQL Database, usługi Azure Synapse Analytics i magazynów danych platformy Azure Blob Storage z fabryką danych. Usługa Azure SQL Database to magazyn danych będący źródłem. Usługa Azure Synapse Analytics to magazyn danych ujścia/docelowy. Usługa Azure Blob Storage polega na przygotowaniu danych przed załadowaniem danych do usługi Azure Synapse Analytics przy użyciu bazy. 

### <a name="create-the-source-azure-sql-database-linked-service"></a>Tworzenie źródłowej połączonej usługi Azure SQL Database
W tym kroku utworzysz połączoną usługę służącą do łączenia bazy danych w Azure SQL Database z fabryką danych. 

1. Otwórz [kartę Zarządzanie](./author-management-hub.md) w okienku po lewej stronie.

1. Na stronie połączone usługi wybierz pozycję **+ Nowy** , aby utworzyć nową połączoną usługę.

   ![Nowa połączona usługa](./media/doc-common-process/new-linked-service.png)
1. W oknie **Nowa połączona usługa** wybierz pozycję **Azure SQL Database**, a następnie kliknij pozycję **Kontynuuj**. 
1. W oknie **Nowa połączona usługa (Azure SQL Database)** wykonaj następujące czynności: 

    a. Wprowadź wartość **AzureSqlDatabaseLinkedService** w polu **Nazwa**.

    b. Wybierz serwer dla **nazwy serwera**
    
    c. Wybierz bazę danych dla **nazwy bazy danych**. 
    
    d. Wprowadź **nazwę użytkownika,** aby połączyć się z bazą danych. 
    
    e. Wprowadź **hasło** dla użytkownika. 

    f. Aby przetestować połączenie z bazą danych przy użyciu określonych informacji, kliknij przycisk **Test connection**.
  
    przykład Kliknij przycisk **Utwórz** , aby zapisać połączoną usługę.


### <a name="create-the-sink-azure-synapse-analytics-linked-service"></a>Tworzenie połączonej usługi Azure Synapse Analytics

1. Na karcie **Połączenia** kliknij ponownie pozycję **+ Nowy** na pasku narzędzi. 
1. W oknie **Nowa połączona usługa** wybierz pozycję **Azure Synapse Analytics**, a następnie kliknij pozycję **Kontynuuj**. 
1. W oknie **Nowa połączona usługa (Azure Synapse Analytics)** wykonaj następujące czynności: 
   
    a. Wprowadź wartość **AzureSqlDWLinkedService** w polu **Nazwa**.
     
    b. Wybierz serwer dla **nazwy serwera**
     
    c. Wybierz bazę danych dla **nazwy bazy danych**. 
     
    d. Wprowadź **nazwę użytkownika** , aby nawiązać połączenie z bazą danych. 
     
    e. Wprowadź **hasło** dla użytkownika. 
     
    f. Aby przetestować połączenie z bazą danych przy użyciu określonych informacji, kliknij przycisk **Test connection**.
     
    przykład Kliknij pozycję **Utwórz**.

### <a name="create-the-staging-azure-storage-linked-service"></a>Tworzenie przejściowej połączonej usługi Azure Storage
W tym samouczku magazyn obiektów blob platformy Azure służy jako obszar przejściowy, pozwalający na włączenie programu PolyBase w celu podniesienia wydajności kopiowania.

1. Na karcie **Połączenia** kliknij ponownie pozycję **+ Nowy** na pasku narzędzi. 
1. W oknie **Nowa połączona usługa** wybierz pozycję **Azure Blob Storage**, a następnie kliknij pozycję **Kontynuuj**. 
1. W oknie **Nowa połączona usługa (Azure Blob Storage)** wykonaj następujące czynności: 

    a. Wprowadź wartość **AzureStorageLinkedService** w polu **Nazwa**.                                                 
    b. Wybierz swoje **konto usługi Azure Storage** w polu **Nazwa konta magazynu**.
    
    c. Kliknij pozycję **Utwórz**.

## <a name="create-datasets"></a>Tworzenie zestawów danych
W tym samouczku utworzysz zestawy danych będące źródłem i ujściem, określające lokalizację przechowywania danych. 

Wejściowy zestaw danych **AzureSqlDatabaseDataset** odwołuje się do elementu **AzureSqlDatabaseLinkedService**. Połączona usługa określa parametry połączenia w celu nawiązania połączenia z bazą danych. Zestaw danych określa nazwę bazy danych i tabelę, która zawiera dane źródłowe. 

Wyjściowy zestaw danych **AzureSqlDWDataset** odwołuje się do elementu **AzureSqlDWLinkedService**. Połączona usługa określa parametry połączenia w celu nawiązania połączenia z usługą Azure Synapse Analytics. Zestaw danych określa bazę danych i tabelę, do którego dane są kopiowane. 

W tym samouczku źródłowe i docelowe tabele SQL nie są ustalone w definicjach zestawów danych. W zamian działanie ForEach przekazuje nazwę tabeli w czasie wykonywania do działania kopiowania. 

### <a name="create-a-dataset-for-source-sql-database"></a>Tworzenie zestawu danych źródłowej bazy danych SQL Database

1. W okienku po lewej stronie wybierz pozycję Karta **autora** .

1. Wybierz pozycję **+** (plus) w lewym okienku, a następnie wybierz pozycję **zestaw danych**. 

    ![Menu Nowy zestaw danych](./media/tutorial-bulk-copy-portal/new-dataset-menu.png)
1. W oknie **Nowy zestaw danych** wybierz pozycję **Azure SQL Database**, a następnie kliknij przycisk **Kontynuuj**. 
    
1. W oknie **dialogowym Ustawianie właściwości** w obszarze **Nazwa** wprowadź **AzureSqlDatabaseDataset**. W obszarze **połączona usługa** wybierz pozycję **AzureSqlDatabaseLinkedService**. Następnie kliknij przycisk **OK**.

1. Przejdź do karty **połączenie** , a następnie wybierz dowolną tabelę dla **tabeli**. Jest to tabela fikcyjna. Zapytanie dotyczące źródłowego zestawu danych jest określane podczas tworzenia potoku. Zapytanie służy do wyodrębniania danych z bazy danych. Alternatywnie możesz kliknąć przycisk **Edytuj** , a następnie w polu Nazwa tabeli wprowadzić nazwę **dbo. fikcyjnname.** 
 

### <a name="create-a-dataset-for-sink-azure-synapse-analytics"></a>Tworzenie zestawu danych dla ujścia usługi Azure Synapse Analytics 

1. Kliknij pozycję **+ (plus)** w lewym okienku, a następnie kliknij pozycję **Zestaw danych**. 
1. W oknie **Nowy zestaw danych** wybierz pozycję **Azure Synapse Analytics**, a następnie kliknij przycisk **Kontynuuj**.
1. W oknie **dialogowym Ustawianie właściwości** w obszarze **Nazwa** wprowadź **AzureSqlDWDataset**. W obszarze **połączona usługa** wybierz pozycję **AzureSqlDWLinkedService**. Następnie kliknij przycisk **OK**.
1. Przejdź do karty **Parametry**, a następnie kliknij pozycję **+ Nowy** i wprowadź ciąg **DWTableName** jako nazwę parametru. Kliknij pozycję **+ Nowy** ponownie, a następnie wprowadź **DWSchema** dla nazwy parametru. Jeśli skopiujesz/wkleisz tę nazwę ze strony, upewnij się, że na końcu *DWTableName* i *DWSchema* nie ma końcowego **znaku spacji** . 
1. Przejdź do karty **Połączenie**. 

    1. W polu **tabela** zaznacz opcję **Edytuj** . Zaznacz pole wyboru w pierwszym polu wejściowym i kliknij link **Dodaj zawartość dynamiczną** poniżej. Na stronie **Dodaj zawartość dynamiczną** kliknij pozycję **DWSchema** w obszarze **Parametry**, która spowoduje automatyczne wypełnienie pola tekstowego wyrażenie Top `@dataset().DWSchema` , a następnie kliknij przycisk **Zakończ**.  
    
        ![Zestaw danych ConnectionName](./media/tutorial-bulk-copy-portal/dataset-connection-tablename.png)

    1. Zaznacz pole wyboru w drugim polu wejściowym i kliknij link **Dodaj zawartość dynamiczną** poniżej. Na stronie **Dodaj zawartość dynamiczną** kliknij pozycję **DWTAbleName** w obszarze **Parametry**, która spowoduje automatyczne wypełnienie pola tekstowego wyrażenie Top `@dataset().DWTableName` , a następnie kliknij przycisk **Zakończ**. 
    
    1. Właściwość **TableName** zestawu danych jest ustawiona na wartości, które są przekazane jako argumenty dla parametrów **DWSchema** i **DWTableName** . Działanie ForEach iteruje w obrębie listy tabel i przekazuje je po jednej do działania Copy (Kopiowanie). 
    

## <a name="create-pipelines"></a>Tworzenie potoków
W tym samouczku tworzysz dwa potoki: **IterateAndCopySQLTables** i **GetTableListAndTriggerCopyData**. 

Potok **GetTableListAndTriggerCopyData** wykonuje dwie czynności:

* Wyszukuje tabelę systemową bazy Azure SQL Database w celu pobrania listy tabel do skopiowania.
* Wyzwala **IterateAndCopySQLTables** potoku w celu wykonania rzeczywistej kopii danych.

Potok  **IterateAndCopySQLTables** pobiera listę tabel jako parametr. Dla każdej tabeli na liście kopiuje dane z tabeli w Azure SQL Database do usługi Azure Synapse Analytics przy użyciu kopii etapowej i bazy.

### <a name="create-the-pipeline-iterateandcopysqltables"></a>Tworzenie potoku IterateAndCopySQLTables

1. W lewym okienku kliknij pozycję **+ (plus)**, a następnie kliknij pozycję **Potok**.

    ![Menu Nowy potok](./media/tutorial-bulk-copy-portal/new-pipeline-menu.png)
 
1. W panelu Ogólne w obszarze **Właściwości** Określ **IterateAndCopySQLTables** dla **nazwy**. Następnie Zwiń panel, klikając ikonę właściwości w prawym górnym rogu.

1. Przejdź do karty **Parametry** i wykonaj następujące czynności: 

    a. Kliknij pozycję **+ Nowy**. 
    
    b. Wprowadź **ciąg tablelist jako** dla **nazwy** parametru.
    
    c. Wybierz wartość **Array** w polu **Typ**.

1. W przyborniku **Działania** rozwiń pozycję **Iteracja i warunki**, a następnie przeciągnij i upuść działanie **ForEach** do powierzchni projektu potoku. Możesz również wyszukać działania w przyborniku **Działania**. 

    a. Na karcie **Ogólne** u dołu wprowadź wartość **IterateSQLTables** w polu **Nazwa**. 

    b. Przejdź do karty **Ustawienia** , kliknij pole wejściowe dla **pozycji elementy**, a następnie kliknij link **Dodaj zawartość dynamiczną** poniżej. 

    c. Na stronie **Dodaj zawartość dynamiczną** Zwiń sekcje **systemowe** i **funkcje** , kliknij **ciąg tablelist jako** w obszarze **Parametry**, co spowoduje automatyczne wypełnienie pola tekstowego pierwsze wyrażenie jako `@pipeline().parameter.tableList` . Następnie kliknij przycisk **Zakończ**. 

    ![Konstruktor parametru ForEach](./media/tutorial-bulk-copy-portal/for-each-parameter-builder.png)
    
    d. Przejdź do karty **działania** , kliknij **ikonę ołówka** , aby dodać działanie podrzędne do działania **foreach** .
    
    ![Konstruktor działań foreach](./media/tutorial-bulk-copy-portal/for-each-activity-builder.png)

1. W przyborniku **działania** rozwiń pozycję **Przenieś & transfer**, a następnie przeciągnij i upuść działanie **Kopiuj dane** do powierzchni projektanta potoku. Zwróć uwagę na menu linków do stron nadrzędnych w górnej części. **IterateAndCopySQLTable** jest nazwą potoku, a **wartość iteratesqltables** jest nazwą działania foreach. Projektant należy do zakresu działania. Aby przełączyć się z powrotem do edytora potoku z edytora ForEach, można kliknąć link w menu łączy. 

    ![Kopiowanie w działaniu ForEach](./media/tutorial-bulk-copy-portal/copy-in-for-each.png)

1. Przejdź do karty **Źródło** i wykonaj następujące czynności:

    1. Wybierz wartość **AzureSqlDatabaseDataset** w polu **Źródło danych**. 
    1. Wybierz opcję **zapytania** dla opcji **Użyj zapytania**. 
    1. Kliknij pole wprowadzania **Zapytanie** -> wybierz pozycję **Dodaj zawartość dynamiczną** poniżej -> wprowadź poniższe wyrażenie w polu **Zapytanie** -> wybierz przycisk **Zakończ**.

        ```sql
        SELECT * FROM [@{item().TABLE_SCHEMA}].[@{item().TABLE_NAME}]
        ``` 

1. Przejdź do karty **Ujście** i wykonaj następujące czynności: 

    1. Wybierz pozycję **AzureSqlDWDataset** w polu **Zestaw danych będący ujściem**.
    1. Kliknij pole wejściowe dla wartości parametru DWTableName-> wybierz pozycję **Dodaj zawartość dynamiczną** poniżej, wprowadź `@item().TABLE_NAME` wyrażenie jako skrypt,-> wybierz pozycję **Zakończ**.
    1. Kliknij pole wejściowe dla wartości parametru DWSchema-> wybierz pozycję **Dodaj zawartość dynamiczną** poniżej, wprowadź `@item().TABLE_SCHEMA` wyrażenie jako skrypt,-> wybierz pozycję **Zakończ**.
    1. W przypadku metody copy wybierz opcję **Base**. 
    1. Wyczyść opcję **Użyj opcji domyślnej** . 
    1. Dla opcji tabeli, ustawieniem domyślnym jest "Brak". Jeśli nie masz wstępnie utworzonych tabel w zlewce usługi Azure Synapse Analytics, Włącz opcję **Automatyczne tworzenie tabeli** , działanie Copy spowoduje następnie automatyczne utworzenie tabel na podstawie danych źródłowych. Aby uzyskać szczegółowe informacje, zobacz [AutoCreate ujścia Tables](copy-activity-overview.md#auto-create-sink-tables). 
    1. Kliknij pole wprowadzania **Skrypt wstępnego kopiowania** -> wybierz pozycję **Dodaj zawartość dynamiczną** poniżej -> wprowadź poniższe wyrażenie jako skrypt -> wybierz przycisk **Zakończ**. 

        ```sql
        TRUNCATE TABLE [@{item().TABLE_SCHEMA}].[@{item().TABLE_NAME}]
        ```

        ![Ustawienia ujścia kopiowania](./media/tutorial-bulk-copy-portal/copy-sink-settings.png)


1. Przejdź do karty **Ustawienia** i wykonaj następujące czynności: 

    1. Zaznacz pole wyboru **Włącz przemieszczanie**.
    1. Wybierz wartość **AzureStorageLinkedService** w polu **Połączona usługa konta magazynu**.

1. Aby zweryfikować ustawienia potoku, kliknij pozycję **Weryfikuj** na górnym pasku narzędzi dla potoku. Upewnij się, że nie wystąpił błąd sprawdzania poprawności. Aby zamknąć **raport weryfikacji potoku**, kliknij podwójne nawiasy ostre **>>** .

### <a name="create-the-pipeline-gettablelistandtriggercopydata"></a>Tworzenie potoku GetTableListAndTriggerCopyData

Ten potok wykonuje dwie czynności:

* Wyszukuje tabelę systemową bazy Azure SQL Database w celu pobrania listy tabel do skopiowania.
* Wyzwala potok „IterateAndCopySQLTables”, który przeprowadza rzeczywiste kopiowanie danych.

Poniżej przedstawiono procedurę tworzenia potoku:

1. W lewym okienku kliknij pozycję **+ (plus)**, a następnie kliknij pozycję **Potok**.
1. W panelu Ogólne w obszarze **Właściwości** Zmień nazwę potoku na **GetTableListAndTriggerCopyData**. 

1. W przyborniku **działania** rozwiń węzeł **Ogólne**, a następnie przeciągnij i upuść działanie **Lookup (wyszukiwanie** ) do powierzchni projektanta potoku i wykonaj następujące czynności:

    1. Wprowadź wartość **LookupTableList** w polu **Nazwa**. 
    1. Wprowadź **wartość w polu Pobierz listę tabel z bazy danych** **.**

1. Przejdź do karty **Ustawienia** i wykonaj następujące czynności:

    1. Wybierz wartość **AzureSqlDatabaseDataset** w polu **Źródło danych**. 
    1. Wybierz pozycję **zapytanie** dla opcji **Użyj zapytania**. 
    1. W obszarze **Zapytanie** wprowadź następujące zapytanie SQL.

        ```sql
        SELECT TABLE_SCHEMA, TABLE_NAME FROM information_schema.TABLES WHERE TABLE_TYPE = 'BASE TABLE' and TABLE_SCHEMA = 'SalesLT' and TABLE_NAME <> 'ProductModel'
        ```
    1. Usuń zaznaczenie pola wyboru **Tylko pierwszy wiersz**.

        ![Działanie Lookup (Wyszukiwanie) — strona ustawień](./media/tutorial-bulk-copy-portal/lookup-settings-page.png)
1. Przeciągnij i upuść działanie **Wykonaj potoku** z przybornika działania na powierzchnię projektanta potoku i Ustaw nazwę na **TriggerCopy**.

1. Aby **połączyć** działanie **Lookup** z działaniem **Wykonaj potok** , przeciągnij **zielony pole** dołączone do działania Lookup (wyszukiwanie) na lewo od działania wykonaj potoku.

    ![Łączenie działań Lookup (Wyszukiwanie) i Execute Pipeline (Wykonywanie potoku)](./media/tutorial-bulk-copy-portal/connect-lookup-execute-pipeline.png)

1. Przejdź do karty **Ustawienia** działania **wykonywania potoku** i wykonaj następujące czynności: 

    1. Wybierz wartość **IterateAndCopySQLTables** w polu **Wywołany potok**. 
    1. Usuń zaznaczenie pola wyboru, aby **czekać po zakończeniu**.
    1. W sekcji **Parametry** kliknij pole dane wejściowe w obszarze wartość-> wybierz pozycję **Dodaj zawartość dynamiczną** poniżej — > wprowadź `@activity('LookupTableList').output.value` jako wartość Nazwa tabeli, > wybierz pozycję **Zakończ**. Ustawiasz listę wyników działania wyszukiwania jako dane wejściowe do drugiego potoku. Lista wyników zawiera listę tabel, których dane trzeba skopiować do miejsca docelowego. 

        ![Działanie Execute Pipeline (Wykonywanie potoku) — strona Ustawienia](./media/tutorial-bulk-copy-portal/execute-pipeline-settings-page.png)

1. Aby zweryfikować potok, kliknij pozycję **Weryfikuj** na pasku narzędzi. Potwierdź, że weryfikacja nie zwróciła błędów. Aby zamknąć okno **Raport weryfikacji potoku**, kliknij pozycję **>>**.

1. Aby opublikować jednostki (zestawy danych, potoki itp.) w usłudze Data Factory, kliknij przycisk **Opublikuj wszystko** w górnej części okna. Poczekaj na pomyślne zakończenie publikowania. 

## <a name="trigger-a-pipeline-run"></a>Wyzwalanie uruchomienia potoku

1. Przejdź do **GetTableListAndTriggerCopyData** potoku, kliknij pozycję **Dodaj wyzwalacz** na górnym pasku narzędzi potoku, a następnie kliknij pozycję **Wyzwól teraz**. 

1. Potwierdź uruchomienie na stronie **uruchomienia potoku** , a następnie wybierz pozycję **Zakończ**.

## <a name="monitor-the-pipeline-run"></a>Monitorowanie działania potoku

1. Przejdź do karty **monitorowanie** . Kliknij przycisk **Odśwież** , dopóki nie zobaczysz przebiegów dla potoków w rozwiązaniu. Kontynuuj odświeżanie listy do momentu wyświetlenia stanu **Powodzenie**. 

1. Aby wyświetlić uruchomienia działań skojarzone z potoku **GetTableListAndTriggerCopyData** , kliknij link Nazwa potoku dla potoku. Powinny zostać wyświetlone dwa uruchomienia działania dla tego uruchomienia potoku. 
    ![Monitorowanie uruchomienia potoku](./media/tutorial-bulk-copy-portal/monitor-pipeline.png)
1. Aby wyświetlić dane wyjściowe działania **Lookup (wyszukiwanie** ), kliknij link **wyjściowy** obok działania w kolumnie **Nazwa działania** . Okno **Dane wyjściowe** można maksymalizować i przywracać. Po przejrzeniu kliknij znak **X**, aby zamknąć okno **Dane wyjściowe**.

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
1. Aby przełączyć się z powrotem do widoku **uruchomienia potoków** , kliknij link **wszystkie uruchomienia potoków** w górnej części menu stron nadrzędnych. Kliknij link **IterateAndCopySQLTables** (w kolumnie **Nazwa potoku** ), aby wyświetlić uruchomienia działania potoku. Zauważ, że dla każdej tabeli w danych wyjściowych działania **Lookup** jest uruchomione jedno działanie **kopiowania** . 

1. Upewnij się, że dane zostały skopiowane do docelowej analizy usługi Azure Synapse, która została użyta w tym samouczku. 

## <a name="next-steps"></a>Następne kroki
W ramach tego samouczka wykonano następujące procedury: 

> [!div class="checklist"]
> * Tworzenie fabryki danych.
> * Twórz połączone usługi Azure SQL Database, Azure Synapse Analytics i Azure Storage.
> * Utwórz zestawy danych Azure SQL Database i usługi Azure Synapse Analytics.
> * Tworzenie potoku w celu wyszukania tabel do skopiowania i innego potoku w celu wykonania samej operacji kopiowania. 
> * Uruchom potok.
> * Monitorowanie uruchomień potoku i działań.

Przejdź do poniższego samouczka, aby dowiedzieć się, jak przyrostowo kopiować dane z lokalizacji źródłowej do docelowej:
> [!div class="nextstepaction"]
>[Przyrostowe kopiowanie danych](tutorial-incremental-copy-portal.md)
