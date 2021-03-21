---
title: Przyrostowe kopiowanie wielu tabel przy użyciu programu PowerShell
description: W tym samouczku utworzysz Azure Data Factory z potokiem, który ładuje dane różnicowe z wielu tabel w bazie danych SQL Server do Azure SQL Database.
ms.author: yexu
author: dearandyxu
ms.reviewer: douglasl, maghan
ms.service: data-factory
ms.topic: tutorial
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 02/18/2021
ms.openlocfilehash: bd29c91efe419ec36b2adc337ecfd1ea7fd71f71
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101739321"
---
# <a name="incrementally-load-data-from-multiple-tables-in-sql-server-to-azure-sql-database-using-powershell"></a>Przyrostowe ładowanie danych z wielu tabel w SQL Server do Azure SQL Database przy użyciu programu PowerShell

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym samouczku utworzysz Azure Data Factory z potokiem, który ładuje dane różnicowe z wielu tabel w bazie danych SQL Server do Azure SQL Database.    

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

    Wybierz jedną kolumnę dla każdej tabeli w źródłowym magazynie danych, która umożliwia zidentyfikowanie nowych lub zaktualizowanych rekordów dla każdego przebiegu. Zazwyczaj dane w tej wybranej kolumnie (na przykład last_modify_time lub ID) rosną wraz z tworzeniem i aktualizacją wierszy. Maksymalna wartość w tej kolumnie jest używana jako limit.

2. **Przygotuj magazyn danych do przechowywania wartości limitu**.

    W tym samouczku wartość limitu jest przechowywana w bazie danych SQL.

3. **Utwórz potok z następującymi działaniami**:
    
    a. Utwórz działanie ForEach służące do przeprowadzania iteracji po liście nazw tabel źródłowych przekazywanych jako parametr do potoku. Dla każdej tabeli źródłowej wywołuje ono następujące działania służące do wykonywania ładowania przyrostowego dla tej tabeli.

    b. Utwórz dwa działania lookup. Użyj pierwszego działania Lookup do pobrania ostatniej wartości limitu. Użyj drugiego działania Lookup do pobrania nowej wartości limitu. Te wartości limitu są przekazywane do działania Copy.

    c. Utwórz działanie Copy, które kopiuje wiersze z magazynu danych źródłowych o wartości kolumny limitu większej niż poprzednia wartość limitu i mniejszej niż nowa wartość limitu. Następnie kopiuje dane różnicowe ze źródłowego magazynu danych do usługi Azure Blob Storage jako nowy plik.

    d. Utwórz działanie StoredProcedure, które aktualizuje wartość limitu dla potoku przy następnym uruchomieniu. 

    Diagram ogólny rozwiązania wygląda następująco: 

    ![Przyrostowe ładowanie danych](media/tutorial-incremental-copy-multiple-tables-powershell/high-level-solution-diagram.png)


Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

## <a name="prerequisites"></a>Wymagania wstępne

* **SQL Server**. Baza danych SQL Server jest używana jako źródłowy magazyn danych w tym samouczku. 
* **Azure SQL Database**. Baza danych programu jest używana w Azure SQL Database jako magazyn danych ujścia. Jeśli nie masz bazy danych SQL, zapoznaj się z tematem [Tworzenie bazy danych w Azure SQL Database](../azure-sql/database/single-database-create-quickstart.md) . 

### <a name="create-source-tables-in-your-sql-server-database"></a>Tworzenie tabel źródłowych w bazie danych SQL Server

1. Otwórz [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms) lub [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio)i nawiąż połączenie z bazą danych SQL Server.

2. W **Eksplorator serwera (SSMS)** lub w **okienku połączenia (Azure Data Studio)** kliknij prawym przyciskiem myszy bazę danych i wybierz polecenie **nowe zapytanie**.

3. Uruchom następujące polecenie SQL względem bazy danych w celu utworzenia tabel o nazwach `customer_table` i `project_table`:

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

### <a name="create-destination-tables-in-your-azure-sql-database"></a>Tworzenie tabel docelowych w Azure SQL Database

1. Otwórz [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms) lub [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio)i nawiąż połączenie z bazą danych SQL Server.

2. W **Eksplorator serwera (SSMS)** lub w **okienku połączenia (Azure Data Studio)** kliknij prawym przyciskiem myszy bazę danych i wybierz polecenie **nowe zapytanie**.

3. Uruchom następujące polecenie SQL względem bazy danych w celu utworzenia tabel o nazwach `customer_table` i `project_table`:  

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

### <a name="create-another-table-in-azure-sql-database-to-store-the-high-watermark-value"></a>Utwórz kolejną tabelę w Azure SQL Database do przechowywania wartości górnego limitu

1. Uruchom następujące polecenie SQL względem bazy danych, aby utworzyć tabelę o nazwie `watermarktable` do przechowywania wartości limitu: 
    
    ```sql
    create table watermarktable
    (
    
        TableName varchar(255),
        WatermarkValue datetime,
    );
    ```
2. Wstaw początkowe wartości limitu dla obu tabel źródłowych w tabeli wartości limitu.

    ```sql

    INSERT INTO watermarktable
    VALUES 
    ('customer_table','1/1/2010 12:00:00 AM'),
    ('project_table','1/1/2010 12:00:00 AM');
    
    ```

### <a name="create-a-stored-procedure-in-the-azure-sql-database"></a>Utwórz procedurę przechowywaną w Azure SQL Database 

Uruchom następujące polecenie, aby utworzyć procedurę przechowywaną w bazie danych. Ta procedura składowana służy do aktualizowania wartość limitu po każdym uruchomieniu potoku. 

```sql
CREATE PROCEDURE usp_write_watermark @LastModifiedtime datetime, @TableName varchar(50)
AS

BEGIN

UPDATE watermarktable
SET [WatermarkValue] = @LastModifiedtime 
WHERE [TableName] = @TableName

END

```

### <a name="create-data-types-and-additional-stored-procedures-in-azure-sql-database"></a>Tworzenie typów danych i dodatkowych procedur składowanych w Azure SQL Database

Uruchom następujące zapytanie, aby utworzyć dwie procedury składowane i dwa typy danych w bazie danych. Służą one do scalania danych z tabel źródłowych w tabelach docelowych. 

Aby ułatwić rozpoczęcie podróży, należy bezpośrednio użyć tych procedur składowanych przekazujących dane różnicowe za pośrednictwem zmiennej tabeli, a następnie scalić je z magazynem docelowym. Należy zachować ostrożność, ponieważ w zmiennej tabeli nie jest oczekiwana "duża liczba wierszy różnicowych (więcej niż 100).  

Jeśli konieczne jest scalenie dużej liczby wierszy różnicowych z magazynem docelowym, sugerujemy użycie działania kopiowania w celu skopiowania wszystkich danych różnicowych do tymczasowej tabeli "przemieszczania" w magazynie docelowym, a następnie skompilowanie własnej procedury składowanej bez użycia zmiennej tabeli, aby scalić je z tabeli "przemieszczanie" do tabeli "Final". 


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

### <a name="azure-powershell"></a>Azure PowerShell

Zainstaluj najnowsze moduły programu Azure PowerShell, wykonując instrukcje podane w temacie [Instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/azurerm/install-azurerm-ps).

## <a name="create-a-data-factory"></a>Tworzenie fabryki danych

1. Zdefiniuj zmienną nazwy grupy zasobów, której użyjesz później w poleceniach programu PowerShell. Skopiuj poniższy tekst polecenia do programu PowerShell, podaj nazwę [grupy zasobów platformy Azure](../azure-resource-manager/management/overview.md) w podwójnych cudzysłowach, a następnie uruchom polecenie. Może to być na przykład `"adfrg"`.

    ```powershell
    $resourceGroupName = "ADFTutorialResourceGroup";
    ```

    Jeśli grupa zasobów już istnieje, jej zastąpienie może być niewskazane. Przypisz inną wartość do zmiennej `$resourceGroupName` i ponownie uruchom polecenie.

2. Zdefiniuj zmienną lokalizacji fabryki danych. 

    ```powershell
    $location = "East US"
    ```
3. Aby utworzyć grupę zasobów platformy Azure, uruchom następujące polecenie: 

    ```powershell
    New-AzResourceGroup $resourceGroupName $location
    ``` 
    Jeśli grupa zasobów już istnieje, jej zastąpienie może być niewskazane. Przypisz inną wartość do zmiennej `$resourceGroupName` i ponownie uruchom polecenie.

4. Zdefiniuj zmienną nazwy fabryki danych. 

    > [!IMPORTANT]
    >  Zaktualizuj nazwę fabryki danych, aby była unikatowa w skali globalnej. Może to być na przykład ADFIncMultiCopyTutorialFactorySP1127. 

    ```powershell
    $dataFactoryName = "ADFIncMultiCopyTutorialFactory";
    ```
5. Aby utworzyć fabrykę danych, uruchom następujące polecenie cmdlet **Set-AzDataFactoryV2** : 
    
    ```powershell
    Set-AzDataFactoryV2 -ResourceGroupName $resourceGroupName -Location $location -Name $dataFactoryName 
    ```

Pamiętaj o następujących kwestiach:

* Nazwa fabryki danych musi być globalnie unikatowa. Jeśli zostanie wyświetlony następujący błąd, zmień nazwę i spróbuj ponownie:

    ```powershell
    Set-AzDataFactoryV2 : HTTP Status Code: Conflict
    Error Code: DataFactoryNameInUse
    Error Message: The specified resource name 'ADFIncMultiCopyTutorialFactory' is already in use. Resource names must be globally unique.
    ```

* Aby utworzyć wystąpienia usługi Data Factory, konto użytkownika używane do logowania się na platformie Azure musi być członkiem roli współautora lub właściciela albo administratorem subskrypcji platformy Azure.

* Aby uzyskać listę regionów platformy Azure, w których obecnie jest dostępna usługa Data Factory, wybierz dane regiony na poniższej stronie, a następnie rozwiń węzeł **Analiza**, aby zlokalizować pozycję **Data Factory**: [Produkty dostępne według regionu](https://azure.microsoft.com/global-infrastructure/services/). Magazyny danych (Azure Storage, SQL Database, wystąpienie zarządzane SQL itd.) i obliczenia (Azure HDInsight itp.) używane przez fabrykę danych mogą znajdować się w innych regionach.

[!INCLUDE [data-factory-create-install-integration-runtime](../../includes/data-factory-create-install-integration-runtime.md)]

## <a name="create-linked-services"></a>Tworzenie połączonych usług

Połączone usługi tworzy się w fabryce danych w celu połączenia magazynów danych i usług obliczeniowych z fabryką danych. W tej sekcji utworzysz połączone usługi do bazy danych SQL Server i bazy danych programu w Azure SQL Database. 

### <a name="create-the-sql-server-linked-service"></a>Tworzenie usługi połączonej z serwerem SQL Server

W tym kroku połączysz bazę danych SQL Server z fabryką danych.

1. Utwórz plik JSON o nazwie **SqlServerLinkedService.js** w folderze C:\ADFTutorials\IncCopyMultiTableTutorial (Utwórz foldery lokalne, jeśli jeszcze nie istnieją) z następującą zawartością. Wybierz właściwą sekcję na podstawie uwierzytelniania używanego do nawiązywania połączenia z programem SQL Server.  

    > [!IMPORTANT]
    > Wybierz właściwą sekcję na podstawie uwierzytelniania używanego do nawiązywania połączenia z programem SQL Server.

    Jeśli używasz uwierzytelniania SQL, skopiuj następującą definicję JSON:

    ```json
    {  
        "name":"SqlServerLinkedService",
        "properties":{  
            "annotations":[  
    
            ],
            "type":"SqlServer",
            "typeProperties":{  
                "connectionString":"integrated security=False;data source=<servername>;initial catalog=<database name>;user id=<username>;Password=<password>"
            },
            "connectVia":{  
                "referenceName":"<integration runtime name>",
                "type":"IntegrationRuntimeReference"
            }
        }
    } 
   ```    
    Jeśli używasz uwierzytelniania systemu Windows, skopiuj następującą definicję JSON:

    ```json
    {  
        "name":"SqlServerLinkedService",
        "properties":{  
            "annotations":[  
    
            ],
            "type":"SqlServer",
            "typeProperties":{  
                "connectionString":"integrated security=True;data source=<servername>;initial catalog=<database name>",
                "userName":"<username> or <domain>\\<username>",
                "password":{  
                    "type":"SecureString",
                    "value":"<password>"
                }
            },
            "connectVia":{  
                "referenceName":"<integration runtime name>",
                "type":"IntegrationRuntimeReference"
            }
        }
    }
    ```
    > [!IMPORTANT]
    > - Wybierz właściwą sekcję na podstawie uwierzytelniania używanego do nawiązywania połączenia z programem SQL Server.
    > - Zastąp wartość &lt;integration runtime name> nazwą Twojego środowiska Integration Runtime.
    > - Przed zapisaniem pliku zastąp wartości &lt;servername>, &lt;databasename>, &lt;username> i &lt;password> wartościami odpowiednimi dla bazy danych programu SQL Server.
    > - Jeśli musisz użyć znaku ukośnika (`\`) w nazwie konta użytkownika lub nazwie serwera, użyj znaku ucieczki (`\`). Może to być na przykład `mydomain\\myuser`.

2. W programie PowerShell uruchom następujące polecenie cmdlet, aby przełączyć się do folderu C:\ADFTutorials\IncCopyMultiTableTutorial.

    ```powershell
    Set-Location 'C:\ADFTutorials\IncCopyMultiTableTutorial'
    ```

3. Uruchom polecenie cmdlet **Set-AzDataFactoryV2LinkedService** , aby utworzyć połączoną usługę AzureStorageLinkedService. W poniższym przykładzie są przekazywane wartości parametrów *ResourceGroupName* i *datafactoryname* : 

    ```powershell
    Set-AzDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SqlServerLinkedService" -File ".\SqlServerLinkedService.json"
    ```

    Oto przykładowe dane wyjściowe:

    ```console
    LinkedServiceName : SqlServerLinkedService
    ResourceGroupName : <ResourceGroupName>
    DataFactoryName   : <DataFactoryName>
    Properties        : Microsoft.Azure.Management.DataFactory.Models.SqlServerLinkedService
    ```

### <a name="create-the-sql-database-linked-service"></a>Tworzenie połączonej usługi SQL Database

1. Utwórz plik JSON o nazwie **AzureSQLDatabaseLinkedService.js** w folderze C:\ADFTutorials\IncCopyMultiTableTutorial o następującej zawartości. (Utwórz folder ADF, jeśli jeszcze nie istnieje). &lt; &gt; &lt; Przed zapisaniem pliku Zastąp wartości ServerName, nazwa bazy danych &gt; , &lt; Nazwa użytkownika &gt; i &lt; hasło &gt; nazwą bazy danych SQL Server, nazwą bazy danych, nazwą użytkownika i hasłem. 

    ```json
    {  
        "name":"AzureSQLDatabaseLinkedService",
        "properties":{  
            "annotations":[  
    
            ],
            "type":"AzureSqlDatabase",
            "typeProperties":{  
                "connectionString":"integrated security=False;encrypt=True;connection timeout=30;data source=<servername>.database.windows.net;initial catalog=<database name>;user id=<user name>;Password=<password>;"
            }
        }
    }
    ```
2. W programie PowerShell uruchom polecenie cmdlet **Set-AzDataFactoryV2LinkedService** , aby utworzyć połączoną usługę AzureSQLDatabaseLinkedService. 

    ```powershell
    Set-AzDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureSQLDatabaseLinkedService" -File ".\AzureSQLDatabaseLinkedService.json"
    ```

    Oto przykładowe dane wyjściowe:

    ```console
    LinkedServiceName : AzureSQLDatabaseLinkedService
    ResourceGroupName : <ResourceGroupName>
    DataFactoryName   : <DataFactoryName>
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlDatabaseLinkedService
    ```

## <a name="create-datasets"></a>Tworzenie zestawów danych

W tym kroku utworzysz zestawy danych reprezentujące źródło danych, docelową lokalizację danych i lokalizację, w której będzie przechowywana wartość limitu.

### <a name="create-a-source-dataset"></a>Tworzenie zestawu danych źródłowych

1. Utwórz plik JSON o nazwie **SourceDataset.js** w tym samym folderze o następującej zawartości: 

    ```json
    {  
        "name":"SourceDataset",
        "properties":{  
            "linkedServiceName":{  
                "referenceName":"SqlServerLinkedService",
                "type":"LinkedServiceReference"
            },
            "annotations":[  
    
            ],
            "type":"SqlServerTable",
            "schema":[  
    
            ]
        }
    }
   
    ```

    Działanie Copy w potoku korzysta z zapytania SQL do załadowania danych, a nie całej tabeli.

2. Uruchom polecenie cmdlet **Set-AzDataFactoryV2Dataset** , aby utworzyć zestaw danych SourceDataset.
    
    ```powershell
    Set-AzDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SourceDataset" -File ".\SourceDataset.json"
    ```

    Oto przykładowe dane wyjściowe polecenia cmdlet:
    
    ```json
    DatasetName       : SourceDataset
    ResourceGroupName : <ResourceGroupName>
    DataFactoryName   : <DataFactoryName>
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.SqlServerTableDataset
    ```

### <a name="create-a-sink-dataset"></a>Tworzenie ujścia zestawu danych

1. Utwórz plik JSON o nazwie **SinkDataset.js** w tym samym folderze o następującej zawartości. Element tableName jest dynamicznie ustawiany przez potok w czasie wykonywania. Działanie ForEach w potoku przeprowadza iterację po liście nazw i przekazuje nazwę tabeli do tego zestawu danych w każdej iteracji. 

    ```json
    {  
        "name":"SinkDataset",
        "properties":{  
            "linkedServiceName":{  
                "referenceName":"AzureSQLDatabaseLinkedService",
                "type":"LinkedServiceReference"
            },
            "parameters":{  
                "SinkTableName":{  
                    "type":"String"
                }
            },
            "annotations":[  
    
            ],
            "type":"AzureSqlTable",
            "typeProperties":{  
                "tableName":{  
                    "value":"@dataset().SinkTableName",
                    "type":"Expression"
                }
            }
        }
    }
    ```

2. Uruchom polecenie cmdlet **Set-AzDataFactoryV2Dataset** , aby utworzyć zestaw danych SinkDataset.
    
    ```powershell
    Set-AzDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SinkDataset" -File ".\SinkDataset.json"
    ```

    Oto przykładowe dane wyjściowe polecenia cmdlet:
    
    ```json
    DatasetName       : SinkDataset
    ResourceGroupName : <ResourceGroupName>
    DataFactoryName   : <DataFactoryName>
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlTableDataset
    ```

### <a name="create-a-dataset-for-a-watermark"></a>Tworzenie zestawu danych dla limitu

W tym kroku utworzysz zestaw danych do przechowywania wartości górnego limitu. 

1. Utwórz plik JSON o nazwie **WatermarkDataset.js** w tym samym folderze o następującej zawartości: 

    ```json
    {
        "name": " WatermarkDataset ",
        "properties": {
            "type": "AzureSqlTable",
            "typeProperties": {
                "tableName": "watermarktable"
            },
            "linkedServiceName": {
                "referenceName": "AzureSQLDatabaseLinkedService",
                "type": "LinkedServiceReference"
            }
        }
    }    
    ```
2. Uruchom polecenie cmdlet **Set-AzDataFactoryV2Dataset** , aby utworzyć zestaw danych WatermarkDataset.
    
    ```powershell
    Set-AzDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "WatermarkDataset" -File ".\WatermarkDataset.json"
    ```

    Oto przykładowe dane wyjściowe polecenia cmdlet:
    
    ```json
    DatasetName       : WatermarkDataset
    ResourceGroupName : <ResourceGroupName>
    DataFactoryName   : <DataFactoryName>
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlTableDataset    
    ```

## <a name="create-a-pipeline"></a>Tworzenie potoku

Potok przyjmuje listę nazw tabel jako parametr. **Działanie foreach** iteruje przez listę nazw tabel i wykonuje następujące operacje: 

1. Użyj **działania Lookup** do pobrania starej wartości limitu (wartość początkowa lub taka, która była używana w ostatniej iteracji).

2. Użyj **działania Lookup** do pobrania nowej wartości limitu (wartość maksymalna kolumny limitu w tabeli źródłowej).

3. Użyj **działania kopiowania** do skopiowania danych między tymi dwiema wartościami limitu ze źródłowej bazy danych do docelowej bazy danych.

4. Użyj **działania StoredProcedure** do zaktualizowania starej wartości limitu, która zostanie użyta w pierwszym kroku następnej iteracji. 

### <a name="create-the-pipeline"></a>Tworzenie potoku

1. Utwórz plik JSON o nazwie **IncrementalCopyPipeline.js** w tym samym folderze o następującej zawartości: 

    ```json
    {  
        "name":"IncrementalCopyPipeline",
        "properties":{  
            "activities":[  
                {  
                    "name":"IterateSQLTables",
                    "type":"ForEach",
                    "dependsOn":[  
    
                    ],
                    "userProperties":[  
    
                    ],
                    "typeProperties":{  
                        "items":{  
                            "value":"@pipeline().parameters.tableList",
                            "type":"Expression"
                        },
                        "isSequential":false,
                        "activities":[  
                            {  
                                "name":"LookupOldWaterMarkActivity",
                                "type":"Lookup",
                                "dependsOn":[  
    
                                ],
                                "policy":{  
                                    "timeout":"7.00:00:00",
                                    "retry":0,
                                    "retryIntervalInSeconds":30,
                                    "secureOutput":false,
                                    "secureInput":false
                                },
                                "userProperties":[  
    
                                ],
                                "typeProperties":{  
                                    "source":{  
                                        "type":"AzureSqlSource",
                                        "sqlReaderQuery":{  
                                            "value":"select * from watermarktable where TableName  =  '@{item().TABLE_NAME}'",
                                            "type":"Expression"
                                        }
                                    },
                                    "dataset":{  
                                        "referenceName":"WatermarkDataset",
                                        "type":"DatasetReference"
                                    }
                                }
                            },
                            {  
                                "name":"LookupNewWaterMarkActivity",
                                "type":"Lookup",
                                "dependsOn":[  
    
                                ],
                                "policy":{  
                                    "timeout":"7.00:00:00",
                                    "retry":0,
                                    "retryIntervalInSeconds":30,
                                    "secureOutput":false,
                                    "secureInput":false
                                },
                                "userProperties":[  
    
                                ],
                                "typeProperties":{  
                                    "source":{  
                                        "type":"SqlServerSource",
                                        "sqlReaderQuery":{  
                                            "value":"select MAX(@{item().WaterMark_Column}) as NewWatermarkvalue from @{item().TABLE_NAME}",
                                            "type":"Expression"
                                        }
                                    },
                                    "dataset":{  
                                        "referenceName":"SourceDataset",
                                        "type":"DatasetReference"
                                    },
                                    "firstRowOnly":true
                                }
                            },
                            {  
                                "name":"IncrementalCopyActivity",
                                "type":"Copy",
                                "dependsOn":[  
                                    {  
                                        "activity":"LookupOldWaterMarkActivity",
                                        "dependencyConditions":[  
                                            "Succeeded"
                                        ]
                                    },
                                    {  
                                        "activity":"LookupNewWaterMarkActivity",
                                        "dependencyConditions":[  
                                            "Succeeded"
                                        ]
                                    }
                                ],
                                "policy":{  
                                    "timeout":"7.00:00:00",
                                    "retry":0,
                                    "retryIntervalInSeconds":30,
                                    "secureOutput":false,
                                    "secureInput":false
                                },
                                "userProperties":[  
    
                                ],
                                "typeProperties":{  
                                    "source":{  
                                        "type":"SqlServerSource",
                                        "sqlReaderQuery":{  
                                            "value":"select * from @{item().TABLE_NAME} where @{item().WaterMark_Column} > '@{activity('LookupOldWaterMarkActivity').output.firstRow.WatermarkValue}' and @{item().WaterMark_Column} <= '@{activity('LookupNewWaterMarkActivity').output.firstRow.NewWatermarkvalue}'",
                                            "type":"Expression"
                                        }
                                    },
                                    "sink":{  
                                        "type":"AzureSqlSink",
                                        "sqlWriterStoredProcedureName":{  
                                            "value":"@{item().StoredProcedureNameForMergeOperation}",
                                            "type":"Expression"
                                        },
                                        "sqlWriterTableType":{  
                                            "value":"@{item().TableType}",
                                            "type":"Expression"
                                        },
                                        "storedProcedureTableTypeParameterName":{  
                                            "value":"@{item().TABLE_NAME}",
                                            "type":"Expression"
                                        },
                                        "disableMetricsCollection":false
                                    },
                                    "enableStaging":false
                                },
                                "inputs":[  
                                    {  
                                        "referenceName":"SourceDataset",
                                        "type":"DatasetReference"
                                    }
                                ],
                                "outputs":[  
                                    {  
                                        "referenceName":"SinkDataset",
                                        "type":"DatasetReference",
                                        "parameters":{  
                                            "SinkTableName":{  
                                                "value":"@{item().TABLE_NAME}",
                                                "type":"Expression"
                                            }
                                        }
                                    }
                                ]
                            },
                            {  
                                "name":"StoredProceduretoWriteWatermarkActivity",
                                "type":"SqlServerStoredProcedure",
                                "dependsOn":[  
                                    {  
                                        "activity":"IncrementalCopyActivity",
                                        "dependencyConditions":[  
                                            "Succeeded"
                                        ]
                                    }
                                ],
                                "policy":{  
                                    "timeout":"7.00:00:00",
                                    "retry":0,
                                    "retryIntervalInSeconds":30,
                                    "secureOutput":false,
                                    "secureInput":false
                                },
                                "userProperties":[  
    
                                ],
                                "typeProperties":{  
                                    "storedProcedureName":"[dbo].[usp_write_watermark]",
                                    "storedProcedureParameters":{  
                                        "LastModifiedtime":{  
                                            "value":{  
                                                "value":"@{activity('LookupNewWaterMarkActivity').output.firstRow.NewWatermarkvalue}",
                                                "type":"Expression"
                                            },
                                            "type":"DateTime"
                                        },
                                        "TableName":{  
                                            "value":{  
                                                "value":"@{activity('LookupOldWaterMarkActivity').output.firstRow.TableName}",
                                                "type":"Expression"
                                            },
                                            "type":"String"
                                        }
                                    }
                                },
                                "linkedServiceName":{  
                                    "referenceName":"AzureSQLDatabaseLinkedService",
                                    "type":"LinkedServiceReference"
                                }
                            }
                        ]
                    }
                }
            ],
            "parameters":{  
                "tableList":{  
                    "type":"array"
                }
            },
            "annotations":[  
    
            ]
        }
    }
    ```
2. Uruchom polecenie cmdlet **Set-AzDataFactoryV2Pipeline** , aby utworzyć potok IncrementalCopyPipeline.
    
   ```powershell
   Set-AzDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "IncrementalCopyPipeline" -File ".\IncrementalCopyPipeline.json"
   ``` 

   Oto przykładowe dane wyjściowe: 

   ```console
    PipelineName      : IncrementalCopyPipeline
    ResourceGroupName : <ResourceGroupName>
    DataFactoryName   : <DataFactoryName>
    Activities        : {IterateSQLTables}
    Parameters        : {[tableList, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification]}
   ```
 
## <a name="run-the-pipeline"></a>Uruchamianie potoku

1. Utwórz plik parametrów o nazwie **Parameters.js** w tym samym folderze o następującej zawartości:

    ```json
    {
        "tableList": 
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
    }
    ```
2. Uruchom potok IncrementalCopyPipeline za pomocą polecenia cmdlet **Invoke-AzDataFactoryV2Pipeline** . Zastąp symbole zastępcze własną nazwą grupy zasobów i fabryki danych.

    ```powershell
    $RunId = Invoke-AzDataFactoryV2Pipeline -PipelineName "IncrementalCopyPipeline" -ResourceGroup $resourceGroupName -dataFactoryName $dataFactoryName -ParameterFile ".\Parameters.json"        
    ``` 

## <a name="monitor-the-pipeline"></a>Monitorowanie potoku

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. Wybierz pozycję **Wszystkie usługi**, przeprowadź wyszukiwanie za pomocą słowa kluczowego *Fabryki danych*, a następnie wybierz pozycję **Fabryki danych**. 

3. Wyszukaj używaną fabrykę danych na liście fabryk danych, a następnie wybierz ją, aby otworzyć stronę **Fabryka danych**. 

4. Na stronie **Fabryka danych** wybierz pozycję **Utwórz & monitor** , aby uruchomić Azure Data Factory na oddzielnej karcie.

5. **Na stronie Wprowadzenie** wybierz pozycję **Monitoruj** po lewej stronie. 
![Zrzut ekranu przedstawia stronę wprowadzenie do Azure Data Factory.](media/doc-common-process/get-started-page-monitor-button.png)    

6. Wyświetlone zostaną wszystkie uruchomienia potoków wraz z ich stanami. Zwróć uwagę, że w poniższym przykładzie stan uruchomienia potoku to **Powodzenie**. Wybierz link w kolumnie **Parametry**, aby sprawdzić parametry przekazywane do potoku. Jeśli wystąpił błąd, w kolumnie **Błąd** zostanie wyświetlony link.

    ![Zrzut ekranu przedstawia uruchomienia potoków dla fabryki danych, w tym potoku.](media/tutorial-incremental-copy-multiple-tables-powershell/monitor-pipeline-runs-4.png)    
7. Po wybraniu linku w kolumnie **Akcje** zobaczysz wszystkie uruchomienia działań dla potoku. 

8. Aby powrócić do widoku **uruchomienia potoków** , wybierz pozycję **wszystkie uruchomienia potoków**. 

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

1. Teraz ponownie uruchom proces, wykonując następujące polecenie programu PowerShell:

    ```powershell
    $RunId = Invoke-AzDataFactoryV2Pipeline -PipelineName "IncrementalCopyPipeline" -ResourceGroup $resourceGroupname -dataFactoryName $dataFactoryName -ParameterFile ".\Parameters.json"
    ```
2. Monitoruj uruchomienia potoków, postępując zgodnie z instrukcjami w sekcji [Monitorowanie potoku](#monitor-the-pipeline). Gdy stan potoku jest **w toku**, w obszarze **Akcje** zostanie wyświetlony inny link do anulowania uruchomienia potoku. 

3. Wybierz pozycję **Odśwież**, aby odświeżać listę do momentu, aż uruchomienie potoku zakończy się pomyślnie. 

4. Opcjonalnie wybierz link **Wyświetl uruchomienia działań** w kolumnie **Akcje**, aby wyświetlić wszystkie uruchomienia działań skojarzone z tym uruchomieniem potoku. 

## <a name="review-the-final-results"></a>Przegląd wyników końcowych

W programu SQL Server Management Studio uruchom następujące zapytania względem docelowej bazy danych, aby sprawdzić, czy zaktualizowane lub nowe dane zostały skopiowane z tabel źródłowych do tabel docelowych. 

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
>[Przyrostowe ładowanie danych z bazy danych Azure SQL Database do magazynu Azure Blob Storage z użyciem technologii Change Tracking](tutorial-incremental-copy-change-tracking-feature-powershell.md)
