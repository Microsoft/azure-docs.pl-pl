---
title: Przyrostowo kopiuj wiele tabel za pomocą witryny Azure Portal
description: W tym samouczku utworzysz potok usługi Azure Data Factory służący do przyrostowego kopiowania danych różnicowych z wielu tabel w lokalnej bazie danych SQL Server do bazy danych Azure SQL Database.
services: data-factory
ms.author: yexu
author: dearandyxu
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 01/20/2018
ms.openlocfilehash: 290ddf9a99d421bbf6303675fd544e81b637d070
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81419276"
---
# <a name="incrementally-load-data-from-multiple-tables-in-sql-server-to-an-azure-sql-database"></a>Przyrostowe ładowanie danych z wielu tabel w programie SQL Server do bazy danych Azure SQL Database

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

W tym samouczku utworzysz fabrykę danych Azure Data Factory z potokiem służącym do ładowania danych różnicowych z wielu tabel na lokalnym serwerze SQL Server do bazy danych Azure SQL Database.    

Ten samouczek obejmuje następujące procedury:

> [!div class="checklist"]
> * Przygotowanie źródłowych i docelowych magazynów danych
> * Tworzenie fabryki danych.
> * Utwórz własne środowisko Integration Runtime.
> * Instalowanie środowiska Integration Runtime. 
> * Tworzenie połączonych usług. 
> * Tworzenie zestawów danych źródła, ujścia i limitu.
> * Tworzenie, uruchamianie i monitorowanie potoku
> * Przejrzyj wyniki.
> * Dodawanie lub aktualizowanie danych w tabelach źródłowych.
> * Ponowne uruchamianie i monitorowanie potoku.
> * Przegląd wyników końcowych.

## <a name="overview"></a>Omówienie
Poniżej przedstawiono ważne czynności związane z tworzeniem tego rozwiązania: 

1. **Wybierz kolumnę limitu**.
    
    Wybierz jedną kolumnę dla każdej tabeli w magazynie danych źródłowych, która może służyć do identyfikowania nowych lub zaktualizowanych rekordów dla każdego uruchomienia. Zazwyczaj dane w tej wybranej kolumnie (na przykład last_modify_time lub ID) rosną wraz z tworzeniem i aktualizacją wierszy. Maksymalna wartość w tej kolumnie jest używana jako limit.

1. **Przygotuj magazyn danych do przechowywania wartości limitu**.   
    
    W tym samouczku wartość limitu jest przechowywana w bazie danych SQL.

1. **Utwórz potok z następującymi działaniami**: 
    
    a. Utwórz działanie ForEach służące do przeprowadzania iteracji po liście nazw tabel źródłowych przekazywanych jako parametr do potoku. Dla każdej tabeli źródłowej wywołuje ono następujące działania służące do wykonywania ładowania przyrostowego dla tej tabeli.

    b. Utwórz dwa działania lookup. Użyj pierwszego działania Lookup do pobrania ostatniej wartości limitu. Użyj drugiego działania Lookup do pobrania nowej wartości limitu. Te wartości limitu są przekazywane do działania Copy.

    d. Utwórz działanie Copy, które kopiuje wiersze z magazynu danych źródłowych o wartości kolumny limitu większej niż poprzednia wartość limitu i mniejszej niż nowa wartość limitu. Następnie kopiuje dane różnicowe ze źródłowego magazynu danych do usługi Azure Blob Storage jako nowy plik.

    d. Utwórz działanie StoredProcedure, które aktualizuje wartość limitu dla potoku przy następnym uruchomieniu. 

    Diagram ogólny rozwiązania wygląda następująco: 

    ![Przyrostowe ładowanie danych](media/tutorial-incremental-copy-multiple-tables-portal/high-level-solution-diagram.png)


Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne](https://azure.microsoft.com/free/) konto przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne
* **SQL Server**. Użyj lokalnej bazy danych SQL Server jako źródłowego magazynu danych w tym samouczku. 
* **Baza danych SQL platformy Azure**. Jako magazyn danych ujścia używana jest baza danych SQL. Jeśli nie masz bazy danych SQL, utwórz ją, wykonując czynności przedstawione w artykule [Tworzenie bazy danych Azure SQL Database](../sql-database/sql-database-get-started-portal.md). 

### <a name="create-source-tables-in-your-sql-server-database"></a>Tworzenie tabel źródłowych w bazie danych SQL Server

1. Otwórz program SQL Server Management Studio, a następnie nawiąż połączenie z lokalną bazą danych programu SQL Server.

1. W **Eksploratorze serwera**kliknij prawym przyciskiem myszy bazę danych i wybierz polecenie **Nowe zapytanie**.

1. Uruchom następujące polecenie SQL względem bazy danych w celu utworzenia tabel o nazwach `customer_table` i `project_table`:

    ```sql
    create table customer_table
    (
        PersonID int,
        Name varchar(255),
        LastModifytime datetime
    );
    
    create table project_table
    (
        Project varchar(255),
        Creationtime datetime
    );
        
    INSERT INTO customer_table
    (PersonID, Name, LastModifytime)
    VALUES
    (1, 'John','9/1/2017 12:56:00 AM'),
    (2, 'Mike','9/2/2017 5:23:00 AM'),
    (3, 'Alice','9/3/2017 2:36:00 AM'),
    (4, 'Andy','9/4/2017 3:21:00 AM'),
    (5, 'Anny','9/5/2017 8:06:00 AM');
    
    INSERT INTO project_table
    (Project, Creationtime)
    VALUES
    ('project1','1/1/2015 0:00:00 AM'),
    ('project2','2/2/2016 1:23:00 AM'),
    ('project3','3/4/2017 5:16:00 AM');
    
    ```

### <a name="create-destination-tables-in-your-azure-sql-database"></a>Tworzenie tabel docelowych w bazie danych Azure SQL Database
1. Otwórz program SQL Server Management Studio i nawiąż połączenie z bazą danych Azure SQL Database.

1. W **Eksploratorze serwera**kliknij prawym przyciskiem myszy bazę danych i wybierz polecenie **Nowe zapytanie**.

1. Uruchom następujące polecenie SQL względem bazy danych `customer_table` SQL `project_table`platformy Azure, aby utworzyć tabele o nazwie i:  
    
    ```sql
    create table customer_table
    (
        PersonID int,
        Name varchar(255),
        LastModifytime datetime
    );
    
    create table project_table
    (
        Project varchar(255),
        Creationtime datetime
    );

    ```

### <a name="create-another-table-in-the-azure-sql-database-to-store-the-high-watermark-value"></a>Tworzenie innej tabeli w bazie danych Azure SQL Database do przechowywania wartości górnego limitu
1. Uruchom następujące polecenie SQL względem bazy danych SQL `watermarktable` platformy Azure, aby utworzyć tabelę o nazwie do przechowywania wartości znaku wodnego: 
    
    ```sql
    create table watermarktable
    (
    
        TableName varchar(255),
        WatermarkValue datetime,
    );
    ```
1. Wstaw początkowe wartości limitu dla obu tabel źródłowych w tabeli wartości limitu.

    ```sql

    INSERT INTO watermarktable
    VALUES 
    ('customer_table','1/1/2010 12:00:00 AM'),
    ('project_table','1/1/2010 12:00:00 AM');
    
    ```

### <a name="create-a-stored-procedure-in-the-azure-sql-database"></a>Tworzenie procedury składowanej w bazie danych Azure SQL Database 

Uruchom następujące polecenie, aby utworzyć procedurę składowaną w bazie danych Azure SQL Database. Ta procedura składowana służy do aktualizowania wartość limitu po każdym uruchomieniu potoku. 

```sql
CREATE PROCEDURE usp_write_watermark @LastModifiedtime datetime, @TableName varchar(50)
AS

BEGIN

    UPDATE watermarktable
    SET [WatermarkValue] = @LastModifiedtime 
WHERE [TableName] = @TableName

END

```

### <a name="create-data-types-and-additional-stored-procedures-in-azure-sql-database"></a>Tworzenie typów danych i dodatkowych procedur składowanych w bazie danych Azure SQL Database
Uruchom następującą kwerendę, aby utworzyć dwie procedury przechowywane i dwa typy danych w bazie danych SQL platformy Azure. Służą one do scalania danych z tabel źródłowych w tabelach docelowych.

Aby ułatwić podróż, używamy bezpośrednio tych procedur przechowywanych przekazując dane delta za pośrednictwem zmiennej tabeli, a następnie scalając je do magazynu docelowego. Należy zachować ostrożność, że nie oczekuje "dużą" liczbę wierszy delta (więcej niż 100) do przechowywania w zmiennej tabeli.  

Jeśli trzeba scalić dużą liczbę wierszy delta do magazynu docelowego, sugerujemy użycie działania kopiowania, aby skopiować wszystkie dane delta do tymczasowej tabeli "przejściowe" w magazynie docelowym, a następnie skonsformować własną procedurę składowaną bez użycia zmiennej tabeli, aby scalić je z tabeli "przejściowe" do tabeli "final". 


```sql
CREATE TYPE DataTypeforCustomerTable AS TABLE(
    PersonID int,
    Name varchar(255),
    LastModifytime datetime
);

GO

CREATE PROCEDURE usp_upsert_customer_table @customer_table DataTypeforCustomerTable READONLY
AS

BEGIN
  MERGE customer_table AS target
  USING @customer_table AS source
  ON (target.PersonID = source.PersonID)
  WHEN MATCHED THEN
      UPDATE SET Name = source.Name,LastModifytime = source.LastModifytime
  WHEN NOT MATCHED THEN
      INSERT (PersonID, Name, LastModifytime)
      VALUES (source.PersonID, source.Name, source.LastModifytime);
END

GO

CREATE TYPE DataTypeforProjectTable AS TABLE(
    Project varchar(255),
    Creationtime datetime
);

GO

CREATE PROCEDURE usp_upsert_project_table @project_table DataTypeforProjectTable READONLY
AS

BEGIN
  MERGE project_table AS target
  USING @project_table AS source
  ON (target.Project = source.Project)
  WHEN MATCHED THEN
      UPDATE SET Creationtime = source.Creationtime
  WHEN NOT MATCHED THEN
      INSERT (Project, Creationtime)
      VALUES (source.Project, source.Creationtime);
END

```

## <a name="create-a-data-factory"></a>Tworzenie fabryki danych

1. Uruchom przeglądarkę internetową **Microsoft Edge** lub **Google Chrome**. Obecnie interfejs użytkownika usługi Data Factory jest obsługiwany tylko przez przeglądarki internetowe Microsoft Edge i Google Chrome.
2. W menu po lewej stronie wybierz pozycję **Utwórz fabrykę** > **danych****analizy** > zasobów: 
   
   ![Wybór usługi Data Factory w okienku „Nowy”](./media/doc-common-process/new-azure-data-factory-menu.png)

3. Na stronie **Nowa fabryka danych** wprowadź wartość **ADFMultiIncCopyTutorialDF** w polu **nazwa**. 
 
   Nazwa fabryki danych platformy Azure musi być **unikatowa globalnie.** Jeśli pojawi się czerwony wykrzyknik z poniższym błędem, zmień nazwę fabryki danych (np. twojanazwaADFIncCopyTutorialDF) i spróbuj utworzyć ją ponownie. Artykuł [Data Factory — Naming Rules (Usługa Data Factory — reguły nazewnictwa)](naming-rules.md) zawiera reguły nazewnictwa artefaktów usługi Data Factory.
  
   `Data factory name "ADFIncCopyTutorialDF" is not available`

4. Wybierz **subskrypcję** Azure, w której chcesz utworzyć fabrykę danych. 
5. Dla opcji **Grupa zasobów** wykonaj jedną z następujących czynności:
     
    - Wybierz pozycję **Użyj istniejącej**, a następnie wybierz istniejącą grupę zasobów z listy rozwijanej. 
    - Wybierz **pozycję Utwórz nowy**i wprowadź nazwę grupy zasobów.   
    Informacje na temat grup zasobów znajdują się w artykule [Using resource groups to manage your Azure resources](../azure-resource-manager/management/overview.md) (Używanie grup zasobów do zarządzania zasobami platformy Azure).  
6. Wybierz opcję **V2** w obszarze **Wersja**.
7. Na liście **lokalizacja** wybierz lokalizację fabryki danych. Na liście rozwijanej są wyświetlane tylko obsługiwane lokalizacje. Magazyny danych (Azure Storage, Azure SQL Database itp.) i jednostki obliczeniowe (HDInsight itp.) używane przez fabrykę danych mogą mieścić się w innych regionach.
8. Kliknij przycisk **Utwórz**.      
9. Po zakończeniu tworzenia zostanie wyświetlona strona **Fabryka danych**, jak pokazano na poniższej ilustracji.
   
   ![Strona główna fabryki danych](./media/doc-common-process/data-factory-home-page.png)
10. Kliknij kafelek **Tworzenie i monitorowanie**, aby w osobnej karcie uruchomić interfejs użytkownika usługi Azure Data Factory.

## <a name="create-self-hosted-integration-runtime"></a>Tworzenie własnego środowiska Integration Runtime
Podczas przenoszenia danych z magazynu danych w sieci prywatnej (lokalnej) do magazynu danych platformy Azure zainstaluj własne środowisko Integration Runtime (IR) w środowisku lokalnym. Własne środowisko IR przenosi dane między siecią prywatną a platformą Azure. 

1. Kliknij pozycję **Połączenia** u dołu okienka po lewej stronie i przejdź do obszaru **Produkty Integration Runtime** w oknie **Połączenia**. 

1. Na karcie **Produkty Integration Runtime** kliknij pozycję **+ Nowy**. 

1. W oknie **Ustawienia środowiska wykonawczego integracji** wybierz pozycję **Wykonaj działania związane z przenoszeniem i wysyłaniem danych do obliczeń zewnętrznych,** a następnie kliknij przycisk **Kontynuuj**. 

1. Wybierz **pozycję Hostowany samodzielnie**i kliknij przycisk **Kontynuuj**. 
1. Wprowadź **pozycję MySelfHostedIR** dla **name**, a następnie kliknij przycisk **Utwórz**. 

1. Kliknij pozycję **Click here to launch the express setup for this computer** (Kliknij tutaj, aby uruchomić instalację ekspresową dla tego komputera) w sekcji **Opcja 1: Instalacja ekspresowa**. 

   ![Kliknij link instalacji ekspresowej](./media/tutorial-incremental-copy-multiple-tables-portal/click-express-setup.png)
1. W oknie **Instalacja ekspresowa środowiska Integration Runtime (Self-hosted)** kliknij przycisk **Zamknij**. 

   ![Instalacja środowiska Integration Runtime — pomyślna](./media/tutorial-incremental-copy-multiple-tables-portal/integration-runtime-setup-successful.png)
1. W przeglądarce internetowej w oknie **instalatora środowiska Integration Runtime** kliknij przycisk **Zakończ**. 

 
1. Upewnij się, że na liście środowisk Integration Runtime jest widoczna pozycja **MySelfHostedIR**.

## <a name="create-linked-services"></a>Tworzenie połączonych usług
Połączone usługi tworzy się w fabryce danych w celu połączenia magazynów danych i usług obliczeniowych z fabryką danych. W tej sekcji utworzysz usługi połączone z używaną, lokalną bazą danych SQL Server i bazą danych Azure SQL Database. 

### <a name="create-the-sql-server-linked-service"></a>Tworzenie usługi połączonej z serwerem SQL Server
W tym kroku połączysz lokalną bazę danych programu SQL Server z fabryką danych.

1. W oknie **Połączenia** przejdź z karty **Środowiska Integration Runtime** do karty **Połączone usługi**, a następnie kliknij pozycję **+ Nowa**.

1. W oknie **Nowa połączona usługa** wybierz pozycję **SQL Server**, a następnie kliknij pozycję **Kontynuuj**. 

1. W oknie **Nowa połączona usługa** wykonaj następujące czynności:

    1. Wprowadź jako **nazwę** wartość **SqlServerLinkedService**. 
    1. Wybierz pozycję **MySelfHostedIR** w polu **Połącz za pośrednictwem środowiska Integration Runtime**. Jest to **ważny** krok. Domyślne środowisko Integration Runtime nie może połączyć się z lokalnym magazynem danych. Użyj własnego środowiska Integration Runtime utworzonego wcześniej. 
    1. W polu **Nazwa serwera** wprowadź nazwę komputera, na którym znajduje się baza danych programu SQL Server.
    1. W polu **Nazwa bazy danych** wprowadź nazwę bazy danych programu SQL Server, która zawiera dane źródłowe. Tworzenie tabeli i wstawienie danych do tej bazy danych zostało przeprowadzone w ramach wymagań wstępnych. 
    1. W polu **Typ uwierzytelniania** wybierz **typ uwierzytelniania**, którego chcesz używać podczas łączenia się z bazą danych. 
    1. W polu **Nazwa użytkownika** wprowadź nazwę użytkownika mającego dostęp do bazy danych programu SQL Server. Jeśli musisz użyć znaku ukośnika (`\`) w nazwie konta użytkownika lub nazwie serwera, użyj znaku ucieczki (`\`). Może to być na przykład `mydomain\\myuser`.
    1. W przypadku **hasła**wprowadź **hasło** użytkownika. 
    1. Aby sprawdzić, czy fabryka danych może połączyć się z bazą danych programu SQL Server, kliknij pozycję **Testuj połączenie**. Usuń wszelkie błędy, tak aby połączenie zostało nawiązane pomyślnie. 
    1. Aby zapisać usługę połączony, kliknij przycisk **Zakończ**.

### <a name="create-the-azure-sql-database-linked-service"></a>Tworzenie połączonej usługi Azure SQL Database
W ostatnim kroku utworzysz połączoną usługę w celu połączenia źródłowej bazy danych programu SQL Server z fabryką danych. W tym kroku połączysz docelową bazę danych (bazę danych ujścia) Azure SQL Database z fabryką danych. 

1. W oknie **Połączenia** przejdź z karty **Środowiska Integration Runtime** do karty **Połączone usługi**, a następnie kliknij pozycję **+ Nowa**.
1. W oknie **Nowa połączona usługa** wybierz pozycję **Azure SQL Database**, a następnie kliknij pozycję **Kontynuuj**. 
1. W oknie **Nowa połączona usługa** wykonaj następujące czynności:

    1. Wprowadź wartość **AzureSqlDatabaseLinkedService** w polu **Nazwa**. 
    1. Z listy rozwijanej **Nazwa serwera** wybierz nazwę serwera Azure SQL Database. 
    1. W polu **Nazwa bazy danych** wybierz bazę danych Azure SQL Database, w której zostały utworzone tabele customer_table i project_table w ramach wymagań wstępnych. 
    1. W polu **Nazwa użytkownika** wprowadź nazwę użytkownika mającego dostęp do bazy danych Azure SQL Database. 
    1. W przypadku **hasła**wprowadź **hasło** użytkownika. 
    1. Aby sprawdzić, czy fabryka danych może połączyć się z bazą danych programu SQL Server, kliknij pozycję **Testuj połączenie**. Usuń wszelkie błędy, tak aby połączenie zostało nawiązane pomyślnie. 
    1. Aby zapisać usługę połączony, kliknij przycisk **Zakończ**.

1. Upewnij się, że lista zawiera dwie połączone usługi. 
   
    ![Dwie połączone usługi](./media/tutorial-incremental-copy-multiple-tables-portal/two-linked-services.png) 

## <a name="create-datasets"></a>Tworzenie zestawów danych
W tym kroku utworzysz zestawy danych reprezentujące źródło danych, docelową lokalizację danych i lokalizację, w której będzie przechowywana wartość limitu.

### <a name="create-a-source-dataset"></a>Tworzenie zestawu danych źródłowych

1. W lewym okienku kliknij pozycję **+ (plus)**, a następnie kliknij pozycję **Zestaw danych**.

1. W oknie **Nowy zestaw danych** wybierz pozycję SQL **Server**, kliknij przycisk **Kontynuuj**. 

1. W przeglądarce sieci Web zostanie otwarta nowa karta służąca do konfigurowania zestawu danych. W widoku drzewa jest również widoczny zestaw danych. U dołu karty **Ogólne** w oknie właściwości wprowadź wartość **SourceDataset** w polu **Nazwa**. 

1. Przejdź do karty **Połączenie** w oknie właściwości, a następnie wybierz pozycję **SqlServerLinkedService** w polu **Połączona usługa**. Nie należy wybierać tabeli w tym miejscu. Działanie Copy w potoku korzysta z zapytania SQL do załadowania danych, a nie całej tabeli.

   ![Źródłowy zestaw danych — połączenie](./media/tutorial-incremental-copy-multiple-tables-portal/source-dataset-connection.png)


### <a name="create-a-sink-dataset"></a>Tworzenie ujścia zestawu danych
1. W lewym okienku kliknij pozycję **+ (plus)**, a następnie kliknij pozycję **Zestaw danych**.

1. W oknie **Nowy zestaw danych** wybierz pozycję Azure SQL **Database**i kliknij przycisk **Kontynuuj**. 

1. W przeglądarce sieci Web zostanie otwarta nowa karta służąca do konfigurowania zestawu danych. W widoku drzewa jest również widoczny zestaw danych. U dołu karty **Ogólne** w oknie właściwości wprowadź wartość **SinkDataset** w polu **Nazwa**.

1. Przejdź do karty **Parametry** w oknie Właściwości i wykonaj następujące czynności: 

    1. Kliknij pozycję **Nowy** w sekcji **Parametry tworzenia/aktualizacji**. 
    1. Wprowadź wartość **SinkTableName** w polu **nazwa** i wartość **Ciąg** w polu **typ**. Ten zestaw danych otrzymuje wartość **SinkTableName** jako parametr. Parametr SinkTableName jest ustawiany dynamicznie przez potok w czasie wykonywania. Działanie ForEach w potoku przeprowadza iterację po liście nazw i przekazuje nazwę tabeli do tego zestawu danych w każdej iteracji.
   
    ![Zestaw danych będący ujściem — właściwości](./media/tutorial-incremental-copy-multiple-tables-portal/sink-dataset-parameters.png)
1. Przełącz się do karty **Połączenie** w oknie Właściwości i wybierz pozycję **AzureSqlDatabaseLinkedService** for **Linked service**. W obszarze właściwości **Tabela** kliknij pozycję **Dodaj zawartość dynamiczną**.   
    
1. W oknie **Dodawanie zawartości dynamicznej** wybierz pozycję **SinkTableName** w sekcji **Parametry.** 
 
1. Po kliknięciu **przycisku**@datasetZakończ , zobaczysz " (). SinkTableName" jako nazwa tabeli.

   ![Zestaw danych będący ujściem — połączenie](./media/tutorial-incremental-copy-multiple-tables-portal/sink-dataset-connection-completion.png)

### <a name="create-a-dataset-for-a-watermark"></a>Tworzenie zestawu danych dla limitu
W tym kroku utworzysz zestaw danych do przechowywania wartości górnego limitu. 

1. W lewym okienku kliknij pozycję **+ (plus)**, a następnie kliknij pozycję **Zestaw danych**.

1. W oknie **Nowy zestaw danych** wybierz pozycję Azure SQL **Database**i kliknij przycisk **Kontynuuj**. 

1. U dołu karty **Ogólne** w oknie właściwości wprowadź wartość **WatermarkDataset** w polu **Nazwa**.
1. Przejdź do karty **Połączenie** i wykonaj następujące czynności: 

    1. Wybierz wartość **AzureSqlDatabaseLinkedService** w polu **Połączona usługa**.
    1. Wybierz element **[dbo].[watermarktable]** dla pozycji **Tabela**.

    ![Zestaw danych limitu — połączenie](./media/tutorial-incremental-copy-multiple-tables-portal/watermark-dataset-connection.png)

## <a name="create-a-pipeline"></a>Tworzenie potoku
Potok przyjmuje listę nazw tabel jako parametr. Działanie ForEach służy do przeprowadzania iteracji po liście nazw tabel i wykonywania następujących operacji: 

1. Użyj działania Lookup do pobrania starej wartość limitu (wartości początkowej lub wartości użytej w ostatniej iteracji).

1. Użyj działania Lookup do pobrania nowej wartości limitu (maksymalnej wartości kolumny limitu w tabeli źródłowej).

1. Użyj działania Copy do skopiowania danych między tymi dwiema wartościami limitu ze źródłowej bazy danych do docelowej bazy danych.

1. Użyj działania StoredProcedure do zaktualizowania starej wartości limitu, która zostanie użyta w pierwszym kroku następnej iteracji. 

### <a name="create-the-pipeline"></a>Tworzenie potoku

1. W lewym okienku kliknij pozycję **+ (plus)**, a następnie kliknij pozycję **Potok**.

1. Na karcie **Ogólne** wprowadź pozycję **IncrementalCopyPipeline** for **Name**. 

1. Na karcie **Parametry** wykonaj następujące czynności: 

    1. Kliknij pozycję **+ Nowy**. 
    1. Wprowadź ciąg **tableList** jako **nazwę** parametru. 
    1. Wybierz **tablicę** dla **typu**parametru .

1. W przyborniku **Działania** rozwiń pozycję **Iteracja i warunki**, a następnie przeciągnij i upuść działanie **ForEach** na powierzchni projektanta potoku. Na karcie **Ogólne** w oknie **Właściwości** wprowadź wartość **IterateSQLTables**. 

1. Przejdź do karty **Ustawienia**, a następnie wprowadź wartość `@pipeline().parameters.tableList` w polu **Elementy**. Działanie ForEach przeprowadza iterację po liście tabel i wykonuje operację kopiowania przyrostowego. 

    ![Działanie ForEach — ustawienia](./media/tutorial-incremental-copy-multiple-tables-portal/foreach-settings.png)

1. Wybierz działanie **ForEach** w potoku, jeśli jeszcze nie zostało wybrane. Kliknij przycisk **Edytuj (ikonę ołówka)**.

1. W przyborniku **Działania** rozwiń pozycję **SQL Database**, przeciągnij i upuść działanie **Lookup** (Wyszukiwanie) na powierzchni projektanta potoku, a następnie wprowadź wartość **LookupOldWaterMarkActivity** w polu **Nazwa**.

1. Przejdź do karty **Ustawienia** w oknie **Właściwości** i wykonaj następujące czynności: 

    1. Wybierz pozycję **WatermarkDataset** w polu **Zestaw danych będący źródłem**.
    1. Wybierz pozycję **Zapytanie** w polu **Użyj zapytania**. 
    1. W obszarze **Zapytanie** wprowadź następujące zapytanie SQL. 

        ```sql
        select * from watermarktable where TableName  =  '@{item().TABLE_NAME}'
        ```

        ![Pierwsze działanie Lookup — ustawienia](./media/tutorial-incremental-copy-multiple-tables-portal/first-lookup-settings.png)
1. Przeciągnij działanie **Lookup** z przybornika **Działania** i wprowadź wartość **LookupNewWaterMarkActivity** w polu **Nazwa**.
        
1. Przełącz się do karty **Ustawienia.**

    1. Wybierz pozycję **SourceDataset** w obszarze **Zestaw danych będący źródłem**. 
    1. Wybierz pozycję **Zapytanie** w polu **Użyj zapytania**.
    1. W obszarze **Zapytanie** wprowadź następujące zapytanie SQL.

        ```sql    
        select MAX(@{item().WaterMark_Column}) as NewWatermarkvalue from @{item().TABLE_NAME}
        ```
    
        ![Drugie działanie Lookup — ustawienia](./media/tutorial-incremental-copy-multiple-tables-portal/second-lookup-settings.png)
1. Przeciągnij działanie **Copy** (Kopiuj) z przybornika **Działania** i wprowadź wartość **IncrementalCopyActivity** w polu **Nazwa**. 

1. Kolejno połącz działania **Lookup** z działaniem **Copy**. Aby utworzyć połączenie, zacznij przeciąganie w **zielonym** polu połączonym z działaniem **Lookup** i upuść je na działaniu **Copy**. Po zmianie koloru obramowania działania Copy na **niebieski** zwolnij przycisk myszy.

    ![Łączenie działań Lookup z działaniem Copy](./media/tutorial-incremental-copy-multiple-tables-portal/connect-lookup-to-copy.png)
1. Wybierz działanie **Copy** w potoku. Przejdź do karty **Źródło** w oknie **Właściwości**. 

    1. Wybierz pozycję **SourceDataset** w obszarze **Zestaw danych będący źródłem**. 
    1. Wybierz pozycję **Zapytanie** w polu **Użyj zapytania**. 
    1. W obszarze **Zapytanie** wprowadź następujące zapytanie SQL.

        ```sql
        select * from @{item().TABLE_NAME} where @{item().WaterMark_Column} > '@{activity('LookupOldWaterMarkActivity').output.firstRow.WatermarkValue}' and @{item().WaterMark_Column} <= '@{activity('LookupNewWaterMarkActivity').output.firstRow.NewWatermarkvalue}'        
        ```

        ![Działanie Copy (Kopiowanie) — ustawienia źródła](./media/tutorial-incremental-copy-multiple-tables-portal/copy-source-settings.png)
1. Przejdź do karty **Ujście** i wybierz pozycję **SinkDataset** w polu **Zestaw danych będący ujściem**. 
        
1. Wykonaj następujące czynności:

    1. We **właściwościach zestawu danych**dla **parametru SinkTableName** wprowadź . `@{item().TABLE_NAME}`
    1. W **yd.** `@{item().StoredProcedureNameForMergeOperation}`
    1. W obszarze Typ `@{item().TableType}` **tabeli** należy wprowadzić polecenie .
    1. W obszarze **Nazwa parametru typu tabela**wprowadź `@{item().TABLE_NAME}`.

    ![Działanie Copy — parametry](./media/tutorial-incremental-copy-multiple-tables-portal/copy-activity-parameters.png)
1. Przeciągnij działanie **Stored Procedure** (Procedura składowana) z przybornika **Działania** do powierzchni projektanta potoku. Połącz działanie **Copy** z działaniem **Stored Procedure**. 

1. Wybierz działanie **Stored Procedure** w potoku, a następnie wprowadź wartość **StoredProceduretoWriteWatermarkActivity** w polu **Nazwa** na karcie **Ogólne** w oknie **Właściwości**. 

1. Przełącz się na kartę **Konto SQL** i wybierz pozycję **AzureSqlDatabaseLinkedService** for **Linked Service**.

    ![Działanie Stored Procedure (Procedura składowana) — konto SQL](./media/tutorial-incremental-copy-multiple-tables-portal/sproc-activity-sql-account.png)
1. Przejdź do karty **Procedura składowana** i wykonaj następujące czynności:

    1. W polu **Nazwa procedury składowanej** wybierz wartość `[dbo].[usp_write_watermark]`. 
    1. Wybierz pozycję **Importuj parametr**. 
    1. Określ wartości następujących parametrów: 

        | Nazwa | Typ | Wartość | 
        | ---- | ---- | ----- |
        | LastModifiedtime | DateTime | `@{activity('LookupNewWaterMarkActivity').output.firstRow.NewWatermarkvalue}` |
        | TableName | Ciąg | `@{activity('LookupOldWaterMarkActivity').output.firstRow.TableName}` |
    
        ![Działanie procedury składowanej — ustawienia procedury składowanej](./media/tutorial-incremental-copy-multiple-tables-portal/sproc-activity-sproc-settings.png)
1. Wybierz **pozycję Opublikuj wszystko,** aby opublikować jednostki utworzone w usłudze Data Factory. 

1. Poczekaj na wyświetlenie komunikatu **Pomyślnie opublikowano**. Aby wyświetlić powiadomienia, kliknij link **Pokaż powiadomienia**. Zamknij okno powiadomień, klikając przycisk **X**.

 
## <a name="run-the-pipeline"></a>Uruchamianie potoku

1. Na pasku narzędzi potoku kliknij pozycję **Dodaj wyzwalacz**i kliknij przycisk **Wyzwalaj teraz**.     

1. W oknie **Uruchamianie potoku** wprowadź następującą wartość dla parametru **tableList**, a następnie kliknij pozycję **Zakończ**. 

    ```
    [
        {
            "TABLE_NAME": "customer_table",
            "WaterMark_Column": "LastModifytime",
            "TableType": "DataTypeforCustomerTable",
            "StoredProcedureNameForMergeOperation": "usp_upsert_customer_table"
        },
        {
            "TABLE_NAME": "project_table",
            "WaterMark_Column": "Creationtime",
            "TableType": "DataTypeforProjectTable",
            "StoredProcedureNameForMergeOperation": "usp_upsert_project_table"
        }
    ]
    ```

    ![Argumenty uruchomienia potoku](./media/tutorial-incremental-copy-multiple-tables-portal/pipeline-run-arguments.png)

## <a name="monitor-the-pipeline"></a>Monitorowanie potoku

1. Przejdź do karty **Monitorowanie** po lewej stronie. Zostanie wyświetlone uruchomienie potoku, które zostało wyzwolone za pomocą **wyzwalacza ręcznego**. Kliknij przycisk **Odśwież**, aby odświeżyć listę. Linki w kolumnie **Działania** umożliwiają wyświetlanie uruchomień działań skojarzonych z uruchomieniem potoku oraz ponowne uruchamianie potoku. 

    ![Uruchomienia potoków](./media/tutorial-incremental-copy-multiple-tables-portal/pipeline-runs.png)
1. Kliknij link **Wyświetl uruchomienia działania** w kolumnie **Akcje**. Zostaną wyświetlone wszystkie uruchomienia działania skojarzone z wybranym uruchomieniem potoku. 

## <a name="review-the-results"></a>Sprawdzanie wyników
W programu SQL Server Management Studio uruchom następujące zapytania względem docelowej bazy danych Azure SQL Database, aby sprawdzić, czy dane zostały skopiowane z tabel źródłowych do tabel docelowych: 

**Zapytanie** 
```sql
select * from customer_table
```

**Dane wyjściowe**
```
===========================================
PersonID    Name    LastModifytime
===========================================
1           John    2017-09-01 00:56:00.000
2           Mike    2017-09-02 05:23:00.000
3           Alice   2017-09-03 02:36:00.000
4           Andy    2017-09-04 03:21:00.000
5           Anny    2017-09-05 08:06:00.000
```

**Zapytanie**

```sql
select * from project_table
```

**Dane wyjściowe**

```
===================================
Project     Creationtime
===================================
project1    2015-01-01 00:00:00.000
project2    2016-02-02 01:23:00.000
project3    2017-03-04 05:16:00.000
```

**Zapytanie**

```sql
select * from watermarktable
```

**Dane wyjściowe**

```
======================================
TableName       WatermarkValue
======================================
customer_table  2017-09-05 08:06:00.000
project_table   2017-03-04 05:16:00.000
```

Należy zauważyć, że wartości limitu dla obu tabel zostały zaktualizowane. 

## <a name="add-more-data-to-the-source-tables"></a>Dodawanie większej ilości danych do tabel źródłowych

Uruchom następujące zapytanie względem źródłowej bazy danych programu SQL Server, aby zaktualizować istniejący wiersz w tabeli customer_table. Wstaw nowy wiersz do tabeli project_table. 

```sql
UPDATE customer_table
SET [LastModifytime] = '2017-09-08T00:00:00Z', [name]='NewName' where [PersonID] = 3

INSERT INTO project_table
(Project, Creationtime)
VALUES
('NewProject','10/1/2017 0:00:00 AM');
``` 

## <a name="rerun-the-pipeline"></a>Ponowne uruchamianie potoku
1. W oknie przeglądarki sieci Web przejdź do karty **Edycja** po lewej stronie. 
1. Na pasku narzędzi potoku kliknij pozycję **Dodaj wyzwalacz**i kliknij przycisk **Wyzwalaj teraz**.   
1. W oknie **Uruchamianie potoku** wprowadź następującą wartość dla parametru **tableList**, a następnie kliknij pozycję **Zakończ**. 

    ```
    [
        {
            "TABLE_NAME": "customer_table",
            "WaterMark_Column": "LastModifytime",
            "TableType": "DataTypeforCustomerTable",
            "StoredProcedureNameForMergeOperation": "usp_upsert_customer_table"
        },
        {
            "TABLE_NAME": "project_table",
            "WaterMark_Column": "Creationtime",
            "TableType": "DataTypeforProjectTable",
            "StoredProcedureNameForMergeOperation": "usp_upsert_project_table"
        }
    ]
    ```

## <a name="monitor-the-pipeline-again"></a>Ponowne monitorowanie potoku

1. Przejdź do karty **Monitorowanie** po lewej stronie. Zostanie wyświetlone uruchomienie potoku, które zostało wyzwolone za pomocą **wyzwalacza ręcznego**. Kliknij przycisk **Odśwież**, aby odświeżyć listę. Linki w kolumnie **Działania** umożliwiają wyświetlanie uruchomień działań skojarzonych z uruchomieniem potoku oraz ponowne uruchamianie potoku. 

1. Kliknij link **Wyświetl uruchomienia działania** w kolumnie **Akcje**. Zostaną wyświetlone wszystkie uruchomienia działania skojarzone z wybranym uruchomieniem potoku. 

## <a name="review-the-final-results"></a>Przegląd wyników końcowych
W programie SQL Server Management Studio uruchom następujące kwerendy względem docelowej bazy danych SQL, aby sprawdzić, czy zaktualizowane/nowe dane zostały skopiowane z tabel źródłowych do tabel docelowych. 

**Zapytanie** 
```sql
select * from customer_table
```

**Dane wyjściowe**
```
===========================================
PersonID    Name    LastModifytime
===========================================
1           John    2017-09-01 00:56:00.000
2           Mike    2017-09-02 05:23:00.000
3           NewName 2017-09-08 00:00:00.000
4           Andy    2017-09-04 03:21:00.000
5           Anny    2017-09-05 08:06:00.000
```

Zwróć uwagę na nowe wartości właściwości **Name** i **LastModifytime** dla identyfikatora **PersonID** numeru 3. 

**Zapytanie**

```sql
select * from project_table
```

**Dane wyjściowe**

```
===================================
Project     Creationtime
===================================
project1    2015-01-01 00:00:00.000
project2    2016-02-02 01:23:00.000
project3    2017-03-04 05:16:00.000
NewProject  2017-10-01 00:00:00.000
```

Zwróć uwagę, że do tabeli project_table dodano pozycję **NewProject**. 

**Zapytanie**

```sql
select * from watermarktable
```

**Dane wyjściowe**

```
======================================
TableName       WatermarkValue
======================================
customer_table  2017-09-08 00:00:00.000
project_table   2017-10-01 00:00:00.000
```

Należy zauważyć, że wartości limitu dla obu tabel zostały zaktualizowane.
     
## <a name="next-steps"></a>Następne kroki
W ramach tego samouczka wykonano następujące procedury: 

> [!div class="checklist"]
> * Przygotowanie źródłowych i docelowych magazynów danych
> * Tworzenie fabryki danych.
> * Tworzenie własnego środowiska Integration Runtime.
> * Instalowanie środowiska Integration Runtime.
> * Tworzenie połączonych usług. 
> * Tworzenie zestawów danych źródła, ujścia i limitu.
> * Tworzenie, uruchamianie i monitorowanie potoku
> * Przejrzyj wyniki.
> * Dodawanie lub aktualizowanie danych w tabelach źródłowych.
> * Ponowne uruchamianie i monitorowanie potoku.
> * Przegląd wyników końcowych.

Przejdź do następującego samouczka, aby dowiedzieć się więcej o przekształcaniu danych za pomocą klastra Spark na platformie Azure:

> [!div class="nextstepaction"]
>[Przyrostowe ładowanie danych z bazy danych Azure SQL Database do magazynu Azure Blob Storage z użyciem technologii Change Tracking](tutorial-incremental-copy-change-tracking-feature-portal.md)


