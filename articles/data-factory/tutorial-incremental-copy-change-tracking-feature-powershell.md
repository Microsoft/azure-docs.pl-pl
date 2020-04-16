---
title: Przyrostowe kopiowanie danych przy użyciu śledzenia zmian
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
ms.date: 01/22/2018
ms.openlocfilehash: 551cf909e6f78b26f3432f3ad9fdbe2140b9702b
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81415297"
---
# <a name="incrementally-load-data-from-azure-sql-database-to-azure-blob-storage-using-change-tracking-information"></a>Przyrostowe ładowanie danych z bazy danych Azure SQL Database do magazynu Azure Blob Storage z użyciem informacji o śledzeniu zmian

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

W tym samouczku utworzysz fabrykę usługi Azure Data Factory z potokiem służącym do ładowania danych przyrostowych na podstawie informacji o **śledzeniu zmian** w źródłowej bazie danych Azure SQL Database do magazynu Azure Blob Storage.  

Ten samouczek obejmuje następujące procedury:

> [!div class="checklist"]
> * Przygotowywanie magazynu danych źródłowych
> * Tworzenie fabryki danych.
> * Tworzenie połączonych usług.
> * Tworzenie zestawów danych źródła, ujścia i śledzenia zmian.
> * Tworzenie, uruchamianie i monitorowanie potoku pełnego kopiowania
> * Dodawanie lub aktualizowanie danych w tabeli źródłowej
> * Tworzenie, uruchamianie i monitorowanie potoku kopiowania przyrostowego

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>Omówienie
W rozwiązaniu integracji danych przyrostowe ładowanie danych po początkowych operacjach ładowania danych to powszechnie używany scenariusz. W niektórych przypadkach dane zmienione w określonym czasie w magazynie danych źródła można łatwo podzielić (na przykład na podstawie właściwości LastModifyTime i CreationTime). W niektórych przypadkach nie można jednak w jednoznaczny sposób zidentyfikować danych różnicowych pochodzących z ostatniej operacji przetwarzania danych. Technologia Change Tracking obsługiwana przez magazyny danych, takie jak baza danych Azure SQL Database i serwer SQL Server, może służyć do identyfikowania danych różnicowych.  W tym samouczku opisano sposób używania usługi Azure Data Factory do pracy z technologią Change Tracking w bazie danych SQL w celu przyrostowego ładowania danych różnicowych z bazy danych Azure SQL Database do magazynu Azure Blob Storage.  Aby uzyskać bardziej konkretne informacje na temat technologii Change Tracking w bazie danych SQL, zobacz [Technologia Change Tracking w programie SQL Server](/sql/relational-databases/track-changes/about-change-tracking-sql-server).

## <a name="end-to-end-workflow"></a>Kompletny przepływ pracy
Poniżej przedstawiono kroki kompleksowego przepływu pracy służące do przyrostowego ładowania danych przy użyciu technologii Change Tracking.

> [!NOTE]
> Technologia Change Tracking jest obsługiwana zarówno przez bazę danych Azure SQL Database, jak i serwer SQL Server. W tym samouczku baza danych Azure SQL Database jest używana jako magazyn danych źródłowych. Możesz również użyć lokalnego serwera SQL Server.

1. **Początkowe ładowanie danych historycznych** (uruchamiane raz):
    1. Włącz technologię Change Tracking w źródłowej bazie danych Azure SQL Database.
    2. Pobierz wartość początkową parametru SYS_CHANGE_VERSION w bazie danych Azure SQL Database jako linię bazową na potrzeby przechwytywania zmienionych danych.
    3. Załaduj pełne dane z bazy danych Azure SQL Database do magazynu Azure Blob Storage.
2. **Przyrostowe ładowanie danych różnicowych zgodnie z harmonogramem** (uruchamiane okresowo po początkowym załadowaniu danych):
    1. Pobierz starą i nową wartość parametru SYS_CHANGE_VERSION.
    3. Załaduj dane różnicowe, łącząc klucze podstawowe zmienionych wierszy (między dwiema wartościami parametru SYS_CHANGE_VERSION) z tabeli **sys.change_tracking_tables** z danymi w **tabeli źródłowej**, a następnie przenieś dane różnicowe do lokalizacji docelowej.
    4. Zaktualizuj wartość parametru SYS_CHANGE_VERSION na potrzeby następnego ładowania danych różnicowych.

## <a name="high-level-solution"></a>Rozwiązanie ogólne
W tym samouczku utworzysz dwa potoki, za pomocą których zostaną wykonane następujące dwie operacje:  

1. **Ładowanie początkowe:** utworzysz potok z działaniem kopiowania, które kopiuje wszystkie dane z magazynu danych źródłowych (baza danych Azure SQL Database) do docelowego magazynu danych (magazyn Azure Blob Storage).

    ![Pełne ładowanie danych](media/tutorial-incremental-copy-change-tracking-feature-powershell/full-load-flow-diagram.png)
1.  **Ładowanie przyrostowe:** utworzysz potok z następującymi działaniami, który będzie okresowo uruchamiany.
    1. Utwórz **dwa działania wyszukiwania**, aby pobrać starą i nową wartość parametru SYS_CHANGE_VERSION z bazy danych Azure SQL Database i przekazać ją do działania kopiowania.
    2. Utwórz **jedno działanie kopiowania**, aby skopiować wstawione, zaktualizowane lub usunięte dane między dwiema wartościami parametru SYS_CHANGE_VERSION z bazy danych Azure SQL Database do magazynu Azure Blob Storage.
    3. Utwórz **jedno działanie procedury składowanej**, aby zaktualizować wartość parametru SYS_CHANGE_VERSION na potrzeby następnego uruchomienia potoku.

    ![Diagram przepływu ładowania przyrostowego](media/tutorial-incremental-copy-change-tracking-feature-powershell/incremental-load-flow-diagram.png)


Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne](https://azure.microsoft.com/free/) konto przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

* Azure PowerShell. Zainstaluj najnowsze moduły programu Azure PowerShell, postępując zgodnie z instrukcjami w [temacie Jak zainstalować i skonfigurować program Azure PowerShell](/powershell/azure/install-Az-ps).
* **Baza danych SQL platformy Azure**. Baza danych jest używana jako magazyn danych **źródłowych**. Jeśli nie masz bazy danych Azure SQL Database, utwórz ją, wykonując czynności przedstawione w artykule [Create an Azure SQL database (Tworzenie bazy danych Azure SQL Database)](../sql-database/sql-database-get-started-portal.md).
* **Konto usługi Azure Storage**. Magazyn obiektów blob jest używany jako magazyn danych **źródłowych**. Jeśli nie masz konta usługi Azure Storage, utwórz je, wykonując czynności przedstawione w artykule [Tworzenie konta magazynu](../storage/common/storage-account-create.md). Utwórz kontener o nazwie **adftutorial**. 

### <a name="create-a-data-source-table-in-your-azure-sql-database"></a>Tworzenie tabeli danych źródłowych w bazie danych Azure SQL Database
1. Uruchom program **SQL Server Management Studio**, a następnie nawiąż połączenie z serwerem Azure SQL Server.
2. W **Eksploratorze serwera** kliknij prawym przyciskiem używaną **bazę danych**, a następnie wybierz pozycję **Nowe zapytanie**.
3. Uruchom poniższe polecenie SQL dla bazy danych Azure SQL Database, aby utworzyć tabelę o nazwie `data_source_table` jako magazyn danych źródłowych.  

    ```sql
    create table data_source_table
    (
        PersonID int NOT NULL,
        Name varchar(255),
        Age int
        PRIMARY KEY (PersonID)
    );

    INSERT INTO data_source_table
        (PersonID, Name, Age)
    VALUES
        (1, 'aaaa', 21),
        (2, 'bbbb', 24),
        (3, 'cccc', 20),
        (4, 'dddd', 26),
        (5, 'eeee', 22);

    ```
4. Włącz mechanizm **Change Tracking** w bazie danych i tabeli źródłowej (data_source_table), uruchamiając następujące zapytanie SQL:

    > [!NOTE]
    > - Zastąp &lt;nazwę bazy danych&gt; nazwą bazy danych Azure SQL Database zawierającą tabelę data_source_table.
    > - W bieżącym przykładzie zmienione dane są przechowywane przez dwa dni. W przypadku ładowania zmienionych danych nie rzadziej niż co trzy dni niektóre zmienione dane nie zostaną uwzględnione.  Jednym rozwiązaniem jest zmiana wartości parametru CHANGE_RETENTION na większą. Alternatywnie należy zapewnić, że okres ładowania zmienionych danych będzie wynosił maksymalnie dwa dni. Aby uzyskać więcej informacji, zobacz [Włącz śledzenie zmian dla bazy danych zmian](/sql/relational-databases/track-changes/enable-and-disable-change-tracking-sql-server#enable-change-tracking-for-a-database)

    ```sql
    ALTER DATABASE <your database name>
    SET CHANGE_TRACKING = ON  
    (CHANGE_RETENTION = 2 DAYS, AUTO_CLEANUP = ON)  

    ALTER TABLE data_source_table
    ENABLE CHANGE_TRACKING  
    WITH (TRACK_COLUMNS_UPDATED = ON)
    ```
5. Utwórz nową tabelę i ustaw wartość parametru ChangeTracking_version na wartość domyślną, uruchamiając następujące zapytanie:

    ```sql
    create table table_store_ChangeTracking_version
    (
        TableName varchar(255),
        SYS_CHANGE_VERSION BIGINT,
    );

    DECLARE @ChangeTracking_version BIGINT
    SET @ChangeTracking_version = CHANGE_TRACKING_CURRENT_VERSION();  

    INSERT INTO table_store_ChangeTracking_version
    VALUES ('data_source_table', @ChangeTracking_version)
    ```

    > [!NOTE]
    > Jeśli dane nie ulegną zmianie po włączeniu śledzenia zmian dla bazy danych SQL Database, wartość wersji śledzenia zmian to 0.
6. Uruchom następujące zapytanie, aby utworzyć procedurę składowaną w bazie danych Azure SQL Database. Potok wywołuje tę procedurę składowaną w celu zaktualizowania wersji śledzenia zmian w tabeli utworzonej w poprzednim kroku.

    ```sql
    CREATE PROCEDURE Update_ChangeTracking_Version @CurrentTrackingVersion BIGINT, @TableName varchar(50)
    AS

    BEGIN

        UPDATE table_store_ChangeTracking_version
        SET [SYS_CHANGE_VERSION] = @CurrentTrackingVersion
    WHERE [TableName] = @TableName

    END    
    ```

### <a name="azure-powershell"></a>Azure PowerShell
Zainstaluj najnowsze moduły programu Azure PowerShell, wykonując instrukcje podane w temacie [Instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/install-Az-ps).

## <a name="create-a-data-factory"></a>Tworzenie fabryki danych
1. Zdefiniuj zmienną nazwy grupy zasobów, której użyjesz później w poleceniach programu PowerShell. Skopiuj poniższy tekst polecenia do programu PowerShell, podaj nazwę [grupy zasobów platformy Azure](../azure-resource-manager/management/overview.md) w podwójnych cudzysłowach, a następnie uruchom polecenie. Na przykład: `"adfrg"`. 
   
     ```powershell
    $resourceGroupName = "ADFTutorialResourceGroup";
    ```

    Jeśli grupa zasobów już istnieje, możesz zrezygnować z jej zastąpienia. Przypisz inną wartość do zmiennej `$resourceGroupName` i ponownie uruchom polecenie.
2. Zdefiniuj zmienną lokalizacji fabryki danych:

    ```powershell
    $location = "East US"
    ```
3. Aby utworzyć grupę zasobów platformy Azure, uruchom następujące polecenie:

    ```powershell
    New-AzResourceGroup $resourceGroupName $location
    ```
    Jeśli grupa zasobów już istnieje, możesz zrezygnować z jej zastąpienia. Przypisz inną wartość do zmiennej `$resourceGroupName` i ponownie uruchom polecenie.
3. Zdefiniuj zmienną nazwy fabryki danych.

    > [!IMPORTANT]
    >  Zaktualizuj nazwę fabryki danych, aby była unikatowa w skali globalnej,  

    ```powershell
    $dataFactoryName = "IncCopyChgTrackingDF";
    ```
5. Aby utworzyć fabrykę danych, uruchom następujące polecenie cmdlet **Set-AzDataFactoryV2:**

    ```powershell       
    Set-AzDataFactoryV2 -ResourceGroupName $resourceGroupName -Location $location -Name $dataFactoryName
    ```

Pamiętaj o następujących kwestiach:

* Nazwa fabryki danych Azure musi być globalnie unikatowa. Jeśli zostanie wyświetlony następujący błąd, zmień nazwę i spróbuj ponownie.

    ```
    The specified Data Factory name 'ADFIncCopyChangeTrackingTestFactory' is already in use. Data Factory names must be globally unique.
    ```
* Aby utworzyć wystąpienia usługi Data Factory, konto użytkownika używane do logowania się na platformie Azure musi być członkiem roli **współautora** lub **właściciela** albo **administratorem** subskrypcji platformy Azure.
* Aby uzyskać listę regionów platformy Azure, w których obecnie jest dostępna usługa Data Factory, wybierz dane regiony na poniższej stronie, a następnie rozwiń węzeł **Analiza**, aby zlokalizować pozycję **Data Factory**: [Produkty dostępne według regionu](https://azure.microsoft.com/global-infrastructure/services/). Magazyny danych (Azure Storage, Azure SQL Database itp.) i jednostki obliczeniowe (HDInsight itp.) używane przez fabrykę danych mogą mieścić się w innych regionach.


## <a name="create-linked-services"></a>Tworzenie połączonych usług
Połączone usługi tworzy się w fabryce danych w celu połączenia magazynów danych i usług obliczeniowych z fabryką danych. W tej sekcji utworzysz usługi połączone ze swoim kontem usługi Azure Storage i bazą danych Azure SQL Database.

### <a name="create-azure-storage-linked-service"></a>Utwórz połączoną usługę Azure Storage.
W tym kroku opisano łączenie konta usługi Azure Storage z fabryką danych.

1. Utwórz plik JSON o nazwie **AzureStorageLinkedService.json** w folderze **C:\ADFTutorials\IncCopyChangeTrackingTutorial** o następującej zawartości: (utwórz folder, jeśli jeszcze nie istnieje). Przed zapisaniem pliku zastąp wartości `<accountName>` i `<accountKey>` nazwą i kluczem konta usługi Azure Storage.

    ```json
    {
        "name": "AzureStorageLinkedService",
        "properties": {
            "type": "AzureStorage",
            "typeProperties": {
                "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountName>;AccountKey=<accountKey>"
            }
        }
    }
    ```
2. W **programie Azure PowerShell**przełącz się do folderu **C:\ADFTutorials\IncCopyChangeTrackingTutorial.**
3. Uruchom polecenie cmdlet **Set-AzDataFactoryV2LinkedService** w celu utworzenia połączonej usługi: **AzureStorageLinkedService**. W poniższym przykładzie przekazujesz wartości dla parametrów **ResourceGroupName** i **DataFactoryName**.

    ```powershell
    Set-AzDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureStorageLinkedService" -File ".\AzureStorageLinkedService.json"
    ```

    Oto przykładowe dane wyjściowe:

    ```json
    LinkedServiceName : AzureStorageLinkedService
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : IncCopyChgTrackingDF
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureStorageLinkedService
    ```

### <a name="create-azure-sql-database-linked-service"></a>Utwórz połączoną usługę Azure SQL Database.
W tym kroku opisano sposób łączenia bazy danych Azure SQL Database z fabryką danych.

1. Przed zapisaniem pliku utwórz plik JSON o nazwie **AzureSQLDatabaseLinkedService.json** w folderze **C:\ADFTutorials\IncCopyChangeTrackingTutorial** o następującej zawartości: zastąp wartości **&lt;serwer, &gt; &lt;nazwa bazy danych&gt;, &lt;identyfikator użytkownika&gt; i &lt;hasło&gt;** nazwą używanego serwera Azure SQL Server, nazwą bazy danych, identyfikatorem użytkownika i hasłem.

    ```json
    {
        "name": "AzureSQLDatabaseLinkedService",
        "properties": {
            "type": "AzureSqlDatabase",
            "typeProperties": {
                "connectionString": "Server = tcp:<server>.database.windows.net,1433;Initial Catalog=<database name>; Persist Security Info=False; User ID=<user name>; Password=<password>; MultipleActiveResultSets = False; Encrypt = True; TrustServerCertificate = False; Connection Timeout = 30;"
            }
        }
    }
    ```
2. W **programie Azure PowerShell**uruchom polecenie cmdlet **Set-AzDataFactoryV2LinkedService** w celu utworzenia połączonej usługi: **AzureSQLDatabaseLinkedService**.

    ```powershell
    Set-AzDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureSQLDatabaseLinkedService" -File ".\AzureSQLDatabaseLinkedService.json"
    ```

    Oto przykładowe dane wyjściowe:

    ```json
    LinkedServiceName : AzureSQLDatabaseLinkedService
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : IncCopyChgTrackingDF
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlDatabaseLinkedService
    ```

## <a name="create-datasets"></a>Tworzenie zestawów danych
W tym kroku utworzysz zestawy danych reprezentujące źródło danych, docelową lokalizację danych. i lokalizację, w której będzie przechowywana wartość parametru SYS_CHANGE_VERSION.

### <a name="create-a-source-dataset"></a>Tworzenie zestawu danych źródłowych
W tym kroku utworzysz zestaw danych reprezentujący źródło danych.

1. Utwórz plik JSON o nazwie SourceDataset.json w tym samym folderze, o następującej zawartości:

    ```json
    {
        "name": "SourceDataset",
        "properties": {
            "type": "AzureSqlTable",
            "typeProperties": {
                "tableName": "data_source_table"
            },
            "linkedServiceName": {
                "referenceName": "AzureSQLDatabaseLinkedService",
                "type": "LinkedServiceReference"
            }
        }
    }   
    ```

2.  Uruchom polecenie cmdlet Set-AzDataFactoryV2Dataset, aby utworzyć zestaw danych: SourceDataset

    ```powershell
    Set-AzDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SourceDataset" -File ".\SourceDataset.json"
    ```

    Oto przykładowe dane wyjściowe polecenia cmdlet:

    ```json
    DatasetName       : SourceDataset
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : IncCopyChgTrackingDF
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlTableDataset
    ```

### <a name="create-a-sink-dataset"></a>Tworzenie ujścia zestawu danych
W tym kroku utworzysz zestaw danych reprezentujący dane skopiowane z magazynu danych źródłowych.

1. Utwórz plik JSON o nazwie SinkDataset.json w tym samym folderze, o następującej zawartości:

    ```json
    {
        "name": "SinkDataset",
        "properties": {
            "type": "AzureBlob",
            "typeProperties": {
                "folderPath": "adftutorial/incchgtracking",
                "fileName": "@CONCAT('Incremental-', pipeline().RunId, '.txt')",
                "format": {
                    "type": "TextFormat"
                }
            },
            "linkedServiceName": {
                "referenceName": "AzureStorageLinkedService",
                "type": "LinkedServiceReference"
            }
        }
    }
    ```

    Jako jedno z wymagań wstępnych w magazynie Azure Blob Storage zostanie utworzony kontener adftutorial. Utwórz kontener, jeśli nie istnieje, lub zmień nazwę istniejącego kontenera. W tym samouczku nazwa pliku wyjściowego jest generowana dynamicznie przy użyciu wyrażenia: @CONCAT('Incremental-', pipeline().RunId, '.txt').
2.  Uruchom polecenie cmdlet Set-AzDataFactoryV2Dataset, aby utworzyć zestaw danych: SinkDataset

    ```powershell
    Set-AzDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SinkDataset" -File ".\SinkDataset.json"
    ```

    Oto przykładowe dane wyjściowe polecenia cmdlet:

    ```json
    DatasetName       : SinkDataset
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : IncCopyChgTrackingDF
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureBlobDataset
    ```

### <a name="create-a-change-tracking-dataset"></a>Tworzenie zestawu danych śledzenia zmian
W tym kroku utworzysz zestaw danych służący do przechowywania wartości wersji śledzenia zmian.  

1. Utwórz plik JSON o nazwie ChangeTrackingDataset.json w tym samym folderze o następującej zawartości:

    ```json
    {
        "name": " ChangeTrackingDataset",
        "properties": {
            "type": "AzureSqlTable",
            "typeProperties": {
                "tableName": "table_store_ChangeTracking_version"
            },
            "linkedServiceName": {
                "referenceName": "AzureSQLDatabaseLinkedService",
                "type": "LinkedServiceReference"
            }
        }
    }
    ```

    Możesz utworzyć tabelę table_store_ChangeTracking_version jako część wymagań wstępnych.
2.  Uruchom polecenie cmdlet Set-AzDataFactoryV2Dataset, aby utworzyć zestaw danych: ChangeTrackingDataset

    ```powershell
    Set-AzDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "ChangeTrackingDataset" -File ".\ChangeTrackingDataset.json"
    ```

    Oto przykładowe dane wyjściowe polecenia cmdlet:

    ```json
    DatasetName       : ChangeTrackingDataset
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : IncCopyChgTrackingDF
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlTableDataset
    ```

## <a name="create-a-pipeline-for-the-full-copy"></a>Tworzenie potoku na potrzeby pełnego kopiowania
W tym kroku utworzysz potok z działaniem kopiowania, które kopiuje wszystkie dane z magazynu danych źródłowych (baza danych Azure SQL Database) do docelowego magazynu danych (magazyn Azure Blob Storage).

1. Utwórz plik JSON FullCopyPipeline.json w tym samym folderze o następującej zawartości:

    ```json
    {
        "name": "FullCopyPipeline",
        "properties": {
            "activities": [{
                "name": "FullCopyActivity",
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "SqlSource"
                    },
                    "sink": {
                        "type": "BlobSink"
                    }
                },

                "inputs": [{
                    "referenceName": "SourceDataset",
                    "type": "DatasetReference"
                }],
                "outputs": [{
                    "referenceName": "SinkDataset",
                    "type": "DatasetReference"
                }]
            }]
        }
    }
    ```
2. Uruchom polecenie cmdlet Set-AzDataFactoryV2Pipeline, aby utworzyć potok: FullCopyPipeline.

   ```powershell
    Set-AzDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "FullCopyPipeline" -File ".\FullCopyPipeline.json"
   ```

   Oto przykładowe dane wyjściowe:

   ```json
    PipelineName      : FullCopyPipeline
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : IncCopyChgTrackingDF
    Activities        : {FullCopyActivity}
    Parameters        :
   ```

### <a name="run-the-full-copy-pipeline"></a>Uruchamianie potoku pełnego kopiowania
Uruchom potok: **FullCopyPipeline** przy użyciu polecenia cmdlet **Invoke-AzDataFactoryV2Pipeline.**

```powershell
Invoke-AzDataFactoryV2Pipeline -PipelineName "FullCopyPipeline" -ResourceGroup $resourceGroupName -dataFactoryName $dataFactoryName        
```

### <a name="monitor-the-full-copy-pipeline"></a>Monitorowanie potoku pełnego kopiowania

1. Zaloguj się do [portalu Azure](https://portal.azure.com).
2. Kliknij pozycję **Wszystkie usługi**, przeprowadź wyszukiwanie za pomocą słowa kluczowego `data factories`, a następnie wybierz pozycję **Fabryki danych**.

    ![Menu fabryk danych](media/tutorial-incremental-copy-change-tracking-feature-powershell/monitor-data-factories-menu-1.png)
3. Wyszukaj **używaną fabrykę danych** na liście fabryk danych, a następnie wybierz ją, aby uruchomić stronę Fabryka danych.

    ![Wyszukiwanie fabryki danych](media/tutorial-incremental-copy-change-tracking-feature-powershell/monitor-search-data-factory-2.png)
4. Na stronie fabryki danych kliknij kafelek **Monitorowanie i zarządzanie**.

    ![Kafelek Monitorowanie i zarządzanie](media/tutorial-incremental-copy-change-tracking-feature-powershell/monitor-monitor-manage-tile-3.png)    
5. **Aplikacja integracji danych** uruchamia się w osobnej karcie. Możesz zobaczyć wszystkie **uruchomienia potoku** i ich stany. Zwróć uwagę, że w poniższym przykładzie stan uruchomienia potoku to **Powodzenie**. Klikając link w kolumnie **Parametry**, można sprawdzić parametry przekazywane do potoku. Jeśli wystąpił błąd, w kolumnie **Błąd** zostanie wyświetlony link. Kliknij link w kolumnie **Akcje**.

    ![Uruchomienia potoków](media/tutorial-incremental-copy-change-tracking-feature-powershell/monitor-pipeline-runs-4.png)    
6. Po kliknięciu linków w kolumnie **Akcje** zostanie wyświetlona następująca strona, na której będą się znajdować wszystkie **uruchomienia działań** dla potoku.

    ![Uruchomienia działania](media/tutorial-incremental-copy-change-tracking-feature-powershell/monitor-activity-runs-5.png)
7. Aby powrócić do widoku **Uruchomienia potoków**, kliknij pozycję **Potoki** w sposób przedstawiony na powyższym obrazie.


### <a name="review-the-results"></a>Sprawdzanie wyników
Zostanie wyświetlony plik o nazwie `incremental-<GUID>.txt` w folderze `incchgtracking` kontenera `adftutorial`.

![Plik wyjściowy z pełnego kopiowania](media/tutorial-incremental-copy-change-tracking-feature-powershell/full-copy-output-file.png)

Ten plik powinien zawierać dane z bazy danych Azure SQL Database:

```
1,aaaa,21
2,bbbb,24
3,cccc,20
4,dddd,26
5,eeee,22
```

## <a name="add-more-data-to-the-source-table"></a>Dodawanie większej ilości danych do tabeli źródłowej

Uruchom następujące zapytanie względem bazy danych Azure SQL Database, aby dodać wiersz, a następnie go zaktualizować.

```sql
INSERT INTO data_source_table
(PersonID, Name, Age)
VALUES
(6, 'new','50');


UPDATE data_source_table
SET [Age] = '10', [name]='update' where [PersonID] = 1

```

## <a name="create-a-pipeline-for-the-delta-copy"></a>Tworzenie potoku na potrzeby kopii przyrostowej
W tym kroku utworzysz potok z następującymi działaniami, który będzie okresowo uruchamiany. Za pomocą **działań wyszukiwania** zostanie pobrana stara i nowa wartość parametru SYS_CHANGE_VERSION z bazy danych Azure SQL Database, która zostanie następnie przekazana do działania kopiowania. Za pomocą **działania kopiowania** zostaną skopiowane wstawione, zaktualizowane lub usunięte dane między dwiema wartościami parametru SYS_CHANGE_VERSION z bazy danych Azure SQL Database do magazynu Azure Blob Storage. Za pomocą **działania procedury składowanej** zostanie zaktualizowana wartość parametru SYS_CHANGE_VERSION na potrzeby następnego uruchomienia potoku.

1. Utwórz plik JSON IncrementalCopyPipeline.json w tym samym folderze o następującej zawartości:

    ```json
    {
            "name": "IncrementalCopyPipeline",
            "properties": {
                "activities": [
                {
                        "name": "LookupLastChangeTrackingVersionActivity",
                        "type": "Lookup",
                        "typeProperties": {
                        "source": {
                            "type": "SqlSource",
                            "sqlReaderQuery": "select * from table_store_ChangeTracking_version"
                            },

                            "dataset": {
                            "referenceName": "ChangeTrackingDataset",
                            "type": "DatasetReference"
                            }
                        }
                    },
                    {
                        "name": "LookupCurrentChangeTrackingVersionActivity",
                        "type": "Lookup",
                        "typeProperties": {
                            "source": {
                                "type": "SqlSource",
                                "sqlReaderQuery": "SELECT CHANGE_TRACKING_CURRENT_VERSION() as CurrentChangeTrackingVersion"
                        },

                            "dataset": {
                            "referenceName": "SourceDataset",
                            "type": "DatasetReference"
                            }
                        }
                    },

                    {
                        "name": "IncrementalCopyActivity",
                        "type": "Copy",
                        "typeProperties": {
                            "source": {
                                "type": "SqlSource",
                                "sqlReaderQuery": "select data_source_table.PersonID,data_source_table.Name,data_source_table.Age, CT.SYS_CHANGE_VERSION, SYS_CHANGE_OPERATION from data_source_table RIGHT OUTER JOIN CHANGETABLE(CHANGES data_source_table, @{activity('LookupLastChangeTrackingVersionActivity').output.firstRow.SYS_CHANGE_VERSION}) as CT on data_source_table.PersonID = CT.PersonID where CT.SYS_CHANGE_VERSION <= @{activity('LookupCurrentChangeTrackingVersionActivity').output.firstRow.CurrentChangeTrackingVersion}"
                            },
                            "sink": {
                                "type": "BlobSink"
                            }
                        },
                        "dependsOn": [
                            {
                                "activity": "LookupLastChangeTrackingVersionActivity",
                                "dependencyConditions": [
                                    "Succeeded"
                                ]
                            },
                            {
                                "activity": "LookupCurrentChangeTrackingVersionActivity",
                                "dependencyConditions": [
                                    "Succeeded"
                                ]
                        }
                        ],

                        "inputs": [
                            {
                            "referenceName": "SourceDataset",
                                "type": "DatasetReference"
                        }
                        ],
                        "outputs": [
                            {
                                "referenceName": "SinkDataset",
                                "type": "DatasetReference"
                            }
                        ]
                    },

                {
                        "name": "StoredProceduretoUpdateChangeTrackingActivity",
                        "type": "SqlServerStoredProcedure",
                        "typeProperties": {

                            "storedProcedureName": "Update_ChangeTracking_Version",
                            "storedProcedureParameters": {
                            "CurrentTrackingVersion": {"value": "@{activity('LookupCurrentChangeTrackingVersionActivity').output.firstRow.CurrentChangeTrackingVersion}", "type": "INT64" },
                                "TableName":  { "value":"@{activity('LookupLastChangeTrackingVersionActivity').output.firstRow.TableName}", "type":"String"}
                            }
                    },

                        "linkedServiceName": {
                        "referenceName": "AzureSQLDatabaseLinkedService",
                            "type": "LinkedServiceReference"
                        },

                        "dependsOn": [
                        {
                                "activity": "IncrementalCopyActivity",
                            "dependencyConditions": [
                                    "Succeeded"
                                ]
                            }
                        ]
                    }
                ]

            }
    }

    ```
2. Uruchom polecenie cmdlet Set-AzDataFactoryV2Pipeline, aby utworzyć potok: FullCopyPipeline.

   ```powershell
    Set-AzDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "IncrementalCopyPipeline" -File ".\IncrementalCopyPipeline.json"
   ```

   Oto przykładowe dane wyjściowe:

   ```json
    PipelineName      : IncrementalCopyPipeline
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : IncCopyChgTrackingDF
    Activities        : {LookupLastChangeTrackingVersionActivity, LookupCurrentChangeTrackingVersionActivity, IncrementalCopyActivity, StoredProceduretoUpdateChangeTrackingActivity}
    Parameters        :
   ```

### <a name="run-the-incremental-copy-pipeline"></a>Uruchamianie potoku kopiowania przyrostowego
Uruchom potok: **IncrementalCopyPipeline** przy użyciu polecenia cmdlet **Invoke-AzDataFactoryV2Pipeline.**

```powershell
Invoke-AzDataFactoryV2Pipeline -PipelineName "IncrementalCopyPipeline" -ResourceGroup $resourceGroupName -dataFactoryName $dataFactoryName     
```


### <a name="monitor-the-incremental-copy-pipeline"></a>Monitorowanie potoku kopiowania przyrostowego
1. Na karcie **aplikacja Integracja danych** odśwież widok **Uruchomienia potoków**. Upewnij się, że na liście widoczny jest potok IncrementalCopyPipeline. Kliknij link w kolumnie **Akcje**.  

    ![Uruchomienia potoków](media/tutorial-incremental-copy-change-tracking-feature-powershell/monitor-pipeline-runs-6.png)    
2. Po kliknięciu linków w kolumnie **Akcje** zostanie wyświetlona następująca strona, na której będą się znajdować wszystkie **uruchomienia działań** dla potoku.

    ![Uruchomienia działania](media/tutorial-incremental-copy-change-tracking-feature-powershell/monitor-activity-runs-7.png)
3. Aby powrócić do widoku **Uruchomienia potoków**, kliknij pozycję **Potoki** w sposób przedstawiony na powyższym obrazie.

### <a name="review-the-results"></a>Sprawdzanie wyników
W folderze `incchgtracking` kontenera `adftutorial` widoczny będzie drugi plik.

![Plik wyjściowy z kopii przyrostowej](media/tutorial-incremental-copy-change-tracking-feature-powershell/incremental-copy-output-file.png)

Ten plik powinien zawierać tylko dane przyrostowe z bazy danych Azure SQL Database. Rekord z wartością `U` znajduje się w zaktualizowanym wierszu w bazie danych, a rekord z wartością `I` to jeden dodany wiersz.

```
1,update,10,2,U
6,new,50,1,I
```
Pierwsze trzy kolumny to zmienione dane z tabeli data_source_table. Ostatnie dwie kolumny to metadane z tabeli systemowej śledzenia zmian. Czwarta kolumna to wartość parametru SYS_CHANGE_VERSION dla każdego zmienionego wiersza. Piąta kolumna to wartość operacji: U — aktualizacja, I — wstawienie.  Aby uzyskać szczegółowe informacje o śledzeniu zmian, zobacz [CHANGETABLE](/sql/relational-databases/system-functions/changetable-transact-sql).

```
==================================================================
PersonID Name    Age    SYS_CHANGE_VERSION    SYS_CHANGE_OPERATION
==================================================================
1        update  10     2                     U
6        new     50     1                     I
```


## <a name="next-steps"></a>Następne kroki
Przejdź do poniższego samouczka, aby dowiedzieć się więcej o kopiowaniu nowych i zmienionych plików tylko na podstawie ich daty ostatniej modyfikacji:

> [!div class="nextstepaction"]
>[Kopiowanie nowych plików przez lastmodifieddate](tutorial-incremental-copy-lastmodified-copy-data-tool.md)
