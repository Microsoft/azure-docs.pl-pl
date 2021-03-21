---
title: Zbiorcze kopiowanie danych za pomocą programu PowerShell
description: Użyj Azure Data Factory z działaniem kopiowania, aby skopiować dane z magazynu danych źródłowych do docelowego magazynu danych zbiorczo.
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.topic: tutorial
ms.custom: seo-lt-2019
ms.date: 02/18/2021
ms.openlocfilehash: fc539ababf4cb240fbe78de0d87b1f127807f604
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101740436"
---
# <a name="copy-multiple-tables-in-bulk-by-using-azure-data-factory-using-powershell"></a>Kopiowanie wielu tabel zbiorczo przy użyciu Azure Data Factory przy użyciu programu PowerShell

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym samouczku przedstawiono **kopiowanie wielu tabel z Azure SQL Database do usługi Azure Synapse Analytics**. Tego samego wzorca można użyć także w innych scenariuszach kopiowania. Na przykład kopiowanie tabel z programu SQL Server/Oracle do usługi Azure SQL Database/Data Warehouse/Azure Blob, kopiowanie różnych ścieżek z obiektów blob do tabeli bazy danych Azure SQL Database.

Na poziomie ogólnym ten samouczek obejmuje następujące kroki:

> [!div class="checklist"]
> * Tworzenie fabryki danych.
> * Twórz połączone usługi Azure SQL Database, Azure Synapse Analytics i Azure Storage.
> * Utwórz zestawy danych Azure SQL Database i usługi Azure Synapse Analytics.
> * Tworzenie potoku w celu wyszukania tabel do skopiowania i innego potoku w celu wykonania samej operacji kopiowania. 
> * Uruchom potok.
> * Monitorowanie uruchomień potoku i działań.

W tym samouczku jest używany program Azure PowerShell. Aby dowiedzieć się więcej o zastosowaniu innych narzędzi/zestawów SDK do tworzenia fabryki danych, zapoznaj się z przewodnikami [Szybki start](quickstart-create-data-factory-dot-net.md). 

## <a name="end-to-end-workflow"></a>Kompletny przepływ pracy
W tym scenariuszu mamy kilka tabel w Azure SQL Database, które chcemy skopiować do usługi Azure Synapse Analytics. Oto sekwencja logiczna kroków przepływu pracy, który następuje w potokach:

![Przepływ pracy](media/tutorial-bulk-copy/tutorial-copy-multiple-tables.png)

* Pierwszy potok wyszukuje listę tabel, które należy skopiować do magazynów danych ujścia.  Alternatywnie można utrzymywać tabelę metadanych, która zawiera listę wszystkich tabel do skopiowania do magazynu danych ujścia. Następnie potok wywołuje inny potok, który działa na wszystkich tabelach w bazie danych i wykonuje operację kopiowania danych.
* Drugi potok przeprowadza rzeczywiste kopiowanie. Pobiera listę tabel jako parametr. Dla każdej tabeli na liście Skopiuj określoną tabelę w Azure SQL Database do odpowiedniej tabeli w usłudze Azure Synapse Analytics przy użyciu [kopii przygotowanej za pośrednictwem usługi BLOB Storage i bazy danych Base](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-synapse-analytics) , aby uzyskać najlepszą wydajność. W tym przykładzie pierwszy potok przekazuje listę tabel jako wartość parametru. 

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* Zainstalowanie programu **Azure PowerShell**. Wykonaj instrukcje podane w temacie [Instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/install-Az-ps).
* **Konto usługi Azure Storage**. Konto usługi Azure Storage jest używane jako przejściowy magazyn obiektów blob w operacji kopiowania zbiorczego. 
* **Azure SQL Database**. Ta baza danych zawiera dane źródłowe. 
* **Analiza usługi Azure Synapse**. Ten magazyn danych służy do przechowywania danych skopiowanych z bazy SQL Database. 

### <a name="prepare-sql-database-and-azure-synapse-analytics"></a>Przygotowanie SQL Database i usługi Azure Synapse Analytics

**Przygotowywanie źródłowej bazy Azure SQL Database**:

Utwórz bazę danych z przykładowymi danymi firmy Adventure Works LT w SQL Database, wykonując następujące czynności: [Utwórz bazę danych w Azure SQL Database](../azure-sql/database/single-database-create-quickstart.md) artykule. Ten samouczek kopiuje wszystkie tabele z tej przykładowej bazy danych do usługi Azure Synapse Analytics.

**Przygotuj ujścia usługi Azure Synapse Analytics**:

1. Jeśli nie masz obszaru roboczego analiz usługi Azure Synapse, zobacz artykuł [Rozpoczynanie pracy z usługą Azure Synapse Analytics](..\synapse-analytics\get-started.md) , aby uzyskać instrukcje.

2. Utwórz odpowiednie schematy tabel w usłudze Azure Synapse Analytics. Usługa Azure Data Factory służy do migrowania/kopiowania danych na późniejszym etapie.

## <a name="azure-services-to-access-sql-server"></a>Usługi platformy Azure umożliwiające dostęp do serwera SQL

W przypadku SQL Database i usługi Azure Synapse Analytics Zezwól usługom platformy Azure na dostęp do programu SQL Server. Upewnij się, że ustawienie **Zezwalaj na dostęp do usług platformy Azure** jest **włączone dla serwera** . To ustawienie umożliwia usłudze Data Factory odczytywanie danych z Azure SQL Database i zapisywanie danych w usłudze Azure Synapse Analytics. W celu sprawdzenia i włączenia tego ustawienia wykonaj następujące kroki:

1. Kliknij pozycję **Wszystkie usługi** po lewej stronie, a następnie kliknij pozycję **Serwery SQL**.
2. Wybierz swój serwer, a następnie kliknij przycisk **Zapora** w obszarze **USTAWIENIA**.
3. Na stronie **Ustawienia zapory** kliknij pozycję **WŁĄCZ** dla ustawienia **Zezwalaj na dostęp do usług platformy Azure**.

## <a name="create-a-data-factory"></a>Tworzenie fabryki danych

1. Uruchom program **PowerShell**. Nie zamykaj programu Azure PowerShell, zanim nie wykonasz wszystkich instrukcji z tego samouczka. Jeśli go zamkniesz i otworzysz ponownie, musisz uruchomić te polecenia jeszcze raz.

    Uruchom poniższe polecenie i wprowadź nazwę użytkownika oraz hasło, których używasz do logowania się w witrynie Azure Portal:
        
    ```powershell
    Connect-AzAccount
    ```
    Uruchom poniższe polecenie, aby wyświetlić wszystkie subskrypcje dla tego konta:

    ```powershell
    Get-AzSubscription
    ```
    Uruchom poniższe polecenie, aby wybrać subskrypcję, z którą chcesz pracować. Zastąp **Identyfikator subskrypcji ID** subskrypcji platformy Azure:

    ```powershell
    Select-AzSubscription -SubscriptionId "<SubscriptionId>"
    ```
2. Uruchom polecenie cmdlet **Set-AzDataFactoryV2** , aby utworzyć fabrykę danych. Przed uruchomieniem polecenia zastąp symbol zastępczy własnymi wartościami. 

    ```powershell
    $resourceGroupName = "<your resource group to create the factory>"
    $dataFactoryName = "<specify the name of data factory to create. It must be globally unique.>"
    Set-AzDataFactoryV2 -ResourceGroupName $resourceGroupName -Location "East US" -Name $dataFactoryName
    ```

    Pamiętaj o następujących kwestiach:

    * Nazwa fabryki danych Azure musi być globalnie unikatowa. Jeśli zostanie wyświetlony następujący błąd, zmień nazwę i spróbuj ponownie.

        ```
        The specified Data Factory name 'ADFv2QuickStartDataFactory' is already in use. Data Factory names must be globally unique.
        ```

    * Aby tworzyć wystąpienia usługi Data Factory, musisz być współautorem lub administratorem subskrypcji platformy Azure.
    * Aby uzyskać listę regionów platformy Azure, w których obecnie jest dostępna usługa Data Factory, wybierz dane regiony na poniższej stronie, a następnie rozwiń węzeł **Analiza**, aby zlokalizować pozycję **Data Factory**: [Produkty dostępne według regionu](https://azure.microsoft.com/global-infrastructure/services/). Magazyny danych (Azure Storage, Azure SQL Database itp.) i jednostki obliczeniowe (HDInsight itp.) używane przez fabrykę danych mogą mieścić się w innych regionach.

## <a name="create-linked-services"></a>Tworzenie połączonych usług

W tym samouczku utworzysz trzy połączone usługi dla źródłowego, ujściowego i przejściowego magazynu obiektów blob, co obejmuje połączenia z magazynami danych:

### <a name="create-the-source-azure-sql-database-linked-service"></a>Tworzenie źródłowej połączonej usługi Azure SQL Database

1. Utwórz plik JSON o nazwie **AzureSQLDatabaseLinkedService.json** w folderze **C:\ADFv2TutorialBulkCopy** o następującej zawartości: (utwórz folder ADFv2TutorialBulkCopy, jeśli jeszcze nie istnieje).

    > [!IMPORTANT]
    > Zastąp wartości &lt;servername&gt;, &lt;databasename&gt;, &lt;username&gt;@&lt;servername&gt; i &lt;password&gt; wartościami z bazy Azure SQL Database przed zapisaniem pliku.

    ```json
    {
        "name": "AzureSqlDatabaseLinkedService",
        "properties": {
            "type": "AzureSqlDatabase",
            "typeProperties": {
                "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
            }
        }
    }
    ```

2. W programie **Azure PowerShell** przejdź do folderu **ADFv2TutorialBulkCopy**.

3. Uruchom polecenie cmdlet **Set-AzDataFactoryV2LinkedService** , aby utworzyć połączoną usługę: **AzureSqlDatabaseLinkedService**. 

    ```powershell
    Set-AzDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureSqlDatabaseLinkedService" -File ".\AzureSqlDatabaseLinkedService.json"
    ```

    Oto przykładowe dane wyjściowe:

    ```console
    LinkedServiceName : AzureSqlDatabaseLinkedService
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlDatabaseLinkedService
    ```

### <a name="create-the-sink-azure-synapse-analytics-linked-service"></a>Tworzenie połączonej usługi Azure Synapse Analytics

1. W folderze **C:\ADFv2TutorialBulkCopy** utwórz plik JSON o nazwie **AzureSqlDWLinkedService.json** z następującą zawartością:

    > [!IMPORTANT]
    > Zastąp wartości &lt;servername&gt;, &lt;databasename&gt;, &lt;username&gt;@&lt;servername&gt; i &lt;password&gt; wartościami z bazy Azure SQL Database przed zapisaniem pliku.

    ```json
    {
        "name": "AzureSqlDWLinkedService",
        "properties": {
            "type": "AzureSqlDW",
            "typeProperties": {
                "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
            }
        }
    }
    ```

2. Aby utworzyć połączoną usługę: **AzureSqlDWLinkedService**, uruchom polecenie cmdlet **Set-AzDataFactoryV2LinkedService** .

    ```powershell
    Set-AzDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureSqlDWLinkedService" -File ".\AzureSqlDWLinkedService.json"
    ```

    Oto przykładowe dane wyjściowe:

    ```console
    LinkedServiceName : AzureSqlDWLinkedService
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlDWLinkedService
    ```

### <a name="create-the-staging-azure-storage-linked-service"></a>Tworzenie przejściowej połączonej usługi Azure Storage

W tym samouczku magazyn obiektów blob platformy Azure służy jako obszar przejściowy, pozwalający na włączenie programu PolyBase w celu podniesienia wydajności kopiowania.

1. W folderze **C:\ADFv2TutorialBulkCopy** utwórz plik JSON o nazwie **AzureStorageLinkedService.json** z następującą zawartością:

    > [!IMPORTANT]
    > Przed zapisaniem pliku zastąp wartości &lt;accountName&gt; i &lt;accountKey&gt; nazwą i kluczem konta usługi Azure Storage.

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

2. Aby utworzyć połączoną usługę: **AzureStorageLinkedService**, uruchom polecenie cmdlet **Set-AzDataFactoryV2LinkedService** .

    ```powershell
    Set-AzDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureStorageLinkedService" -File ".\AzureStorageLinkedService.json"
    ```

    Oto przykładowe dane wyjściowe:

    ```console
    LinkedServiceName : AzureStorageLinkedService
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureStorageLinkedService
    ```

## <a name="create-datasets"></a>Tworzenie zestawów danych

W tym samouczku utworzysz zestawy danych źródła i ujścia, określające lokalizację przechowywania danych:

### <a name="create-a-dataset-for-source-sql-database"></a>Tworzenie zestawu danych źródłowej bazy danych SQL Database

1. W folderze **C:\ADFv2TutorialBulkCopy** utwórz plik JSON o nazwie **AzureSqlDatabaseDataset.json** z następującą zawartością. Nazwa „tableName” to nazwa tymczasowa, ponieważ w późniejszym czasie do pobierania danych używane jest zapytanie SQL w działaniu kopiowania.

    ```json
    {
        "name": "AzureSqlDatabaseDataset",
        "properties": {
            "type": "AzureSqlTable",
            "linkedServiceName": {
                "referenceName": "AzureSqlDatabaseLinkedService",
                "type": "LinkedServiceReference"
            },
            "typeProperties": {
                "tableName": "dummy"
            }
        }
    }
    ```

2. Aby utworzyć zestaw danych: **AzureSqlDatabaseDataset**, uruchom polecenie cmdlet **Set-AzDataFactoryV2Dataset** .

    ```powershell
    Set-AzDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureSqlDatabaseDataset" -File ".\AzureSqlDatabaseDataset.json"
    ```

    Oto przykładowe dane wyjściowe:

    ```console
    DatasetName       : AzureSqlDatabaseDataset
    ResourceGroupName : <resourceGroupname>
    DataFactoryName   : <dataFactoryName>
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlTableDataset
    ```

### <a name="create-a-dataset-for-sink-azure-synapse-analytics"></a>Tworzenie zestawu danych dla ujścia usługi Azure Synapse Analytics

1. Utwórz plik JSON o nazwie **AzureSqlDWDataset.json** w folderze **C:\ADFv2TutorialBulkCopy**, z następującą zawartością: „tableName” ustawione jako parametr, następnie działanie kopiowania odwołujące się do tego zestawu danych przekazuje rzeczywistą wartość do zestawu danych.

    ```json
    {
        "name": "AzureSqlDWDataset",
        "properties": {
            "type": "AzureSqlDWTable",
            "linkedServiceName": {
                "referenceName": "AzureSqlDWLinkedService",
                "type": "LinkedServiceReference"
            },
            "typeProperties": {
                "tableName": {
                    "value": "@{dataset().DWTableName}",
                    "type": "Expression"
                }
            },
            "parameters":{
                "DWTableName":{
                    "type":"String"
                }
            }
        }
    }
    ```

2. Aby utworzyć zestaw danych: **AzureSqlDWDataset**, uruchom polecenie cmdlet **Set-AzDataFactoryV2Dataset** .

    ```powershell
    Set-AzDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureSqlDWDataset" -File ".\AzureSqlDWDataset.json"
    ```

    Oto przykładowe dane wyjściowe:

    ```console
    DatasetName       : AzureSqlDWDataset
    ResourceGroupName : <resourceGroupname>
    DataFactoryName   : <dataFactoryName>
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlDwTableDataset
    ```

## <a name="create-pipelines"></a>Tworzenie potoków

W tym samouczku utworzysz dwa potoki:

### <a name="create-the-pipeline-iterateandcopysqltables"></a>Tworzenie potoku „IterateAndCopySQLTables”

Ten potok pobiera listę tabel jako parametr. Dla każdej tabeli na liście kopiuje dane z tabeli w Azure SQL Database do usługi Azure Synapse Analytics przy użyciu kopii etapowej i bazy.

1. W folderze **C:\ADFv2TutorialBulkCopy** utwórz plik JSON o nazwie **IterateAndCopySQLTables.json** z następującą zawartością:

    ```json
    {
        "name": "IterateAndCopySQLTables",
        "properties": {
            "activities": [
                {
                    "name": "IterateSQLTables",
                    "type": "ForEach",
                    "typeProperties": {
                        "isSequential": "false",
                        "items": {
                            "value": "@pipeline().parameters.tableList",
                            "type": "Expression"
                        },
                        "activities": [
                            {
                                "name": "CopyData",
                                "description": "Copy data from Azure SQL Database to Azure Synapse Analytics",
                                "type": "Copy",
                                "inputs": [
                                    {
                                        "referenceName": "AzureSqlDatabaseDataset",
                                        "type": "DatasetReference"
                                    }
                                ],
                                "outputs": [
                                    {
                                        "referenceName": "AzureSqlDWDataset",
                                        "type": "DatasetReference",
                                        "parameters": {
                                            "DWTableName": "[@{item().TABLE_SCHEMA}].[@{item().TABLE_NAME}]"
                                        }
                                    }
                                ],
                                "typeProperties": {
                                    "source": {
                                        "type": "SqlSource",
                                        "sqlReaderQuery": "SELECT * FROM [@{item().TABLE_SCHEMA}].[@{item().TABLE_NAME}]"
                                    },
                                    "sink": {
                                        "type": "SqlDWSink",
                                        "preCopyScript": "TRUNCATE TABLE [@{item().TABLE_SCHEMA}].[@{item().TABLE_NAME}]",
                                        "allowPolyBase": true
                                    },
                                    "enableStaging": true,
                                    "stagingSettings": {
                                        "linkedServiceName": {
                                            "referenceName": "AzureStorageLinkedService",
                                            "type": "LinkedServiceReference"
                                        }
                                    }
                                }
                            }
                        ]
                    }
                }
            ],
            "parameters": {
                "tableList": {
                    "type": "Object"
                }
            }
        }
    }
    ```

2. Aby utworzyć potok: **IterateAndCopySQLTables**, uruchom polecenie cmdlet **Set-AzDataFactoryV2Pipeline** .

    ```powershell
    Set-AzDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "IterateAndCopySQLTables" -File ".\IterateAndCopySQLTables.json"
    ```

    Oto przykładowe dane wyjściowe:

    ```console
    PipelineName      : IterateAndCopySQLTables
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Activities        : {IterateSQLTables}
    Parameters        : {[tableList, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification]}
    ```

### <a name="create-the-pipeline-gettablelistandtriggercopydata"></a>Tworzenie potoku „GetTableListAndTriggerCopyData”

Ten potok wykonuje dwie czynności:

* Wyszukuje tabelę systemową bazy Azure SQL Database w celu pobrania listy tabel do skopiowania.
* Wyzwala potok „IterateAndCopySQLTables”, który przeprowadza rzeczywiste kopiowanie danych.

1. W folderze **C:\ADFv2TutorialBulkCopy** utwórz plik JSON o nazwie **GetTableListAndTriggerCopyData.json** z następującą zawartością:

    ```json
    {
        "name":"GetTableListAndTriggerCopyData",
        "properties":{
            "activities":[
                { 
                    "name": "LookupTableList",
                    "description": "Retrieve the table list from Azure SQL dataabse",
                    "type": "Lookup",
                    "typeProperties": {
                        "source": {
                            "type": "SqlSource",
                            "sqlReaderQuery": "SELECT TABLE_SCHEMA, TABLE_NAME FROM information_schema.TABLES WHERE TABLE_TYPE = 'BASE TABLE' and TABLE_SCHEMA = 'SalesLT' and TABLE_NAME <> 'ProductModel'"
                        },
                        "dataset": {
                            "referenceName": "AzureSqlDatabaseDataset",
                            "type": "DatasetReference"
                        },
                        "firstRowOnly": false
                    }
                },
                {
                    "name": "TriggerCopy",
                    "type": "ExecutePipeline",
                    "typeProperties": {
                        "parameters": {
                            "tableList": {
                                "value": "@activity('LookupTableList').output.value",
                                "type": "Expression"
                            }
                        },
                        "pipeline": {
                            "referenceName": "IterateAndCopySQLTables",
                            "type": "PipelineReference"
                        },
                        "waitOnCompletion": true
                    },
                    "dependsOn": [
                        {
                            "activity": "LookupTableList",
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

2. Aby utworzyć potok: **GetTableListAndTriggerCopyData**, uruchom polecenie cmdlet **Set-AzDataFactoryV2Pipeline** .

    ```powershell
    Set-AzDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "GetTableListAndTriggerCopyData" -File ".\GetTableListAndTriggerCopyData.json"
    ```

    Oto przykładowe dane wyjściowe:

    ```console
    PipelineName      : GetTableListAndTriggerCopyData
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Activities        : {LookupTableList, TriggerCopy}
    Parameters        :
    ```

## <a name="start-and-monitor-pipeline-run"></a>Uruchamianie i monitorowanie działania potoku

1. Uruchom działanie potoku dla głównego potoku „GetTableListAndTriggerCopyData” i zarejestruj identyfikator uruchomienia potoku do przyszłego monitorowania. Potok dodatkowo uruchamia potok „IterateAndCopySQLTables” zgodnie z definicją w działaniu ExecutePipeline.

    ```powershell
    $runId = Invoke-AzDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineName 'GetTableListAndTriggerCopyData'
    ```

2.  Uruchom następujący skrypt, aby stale sprawdzać stan uruchomienia potoku **GetTableListAndTriggerCopyData** oraz wydrukować ostateczny wynik uruchomienia potoku i działania.

    ```powershell
    while ($True) {
        $run = Get-AzDataFactoryV2PipelineRun -ResourceGroupName $resourceGroupName -DataFactoryName $DataFactoryName -PipelineRunId $runId

        if ($run) {
            if ($run.Status -ne 'InProgress') {
                Write-Host "Pipeline run finished. The status is: " $run.Status -foregroundcolor "Yellow"
                Write-Host "Pipeline run details:" -foregroundcolor "Yellow"
                $run
                break
            }
            Write-Host  "Pipeline is running...status: InProgress" -foregroundcolor "Yellow"
        }

        Start-Sleep -Seconds 15
    }

    $result = Get-AzDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $runId -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)
    Write-Host "Activity run details:" -foregroundcolor "Yellow"
    $result
    ```

    Oto dane wyjściowe przykładowego uruchomienia:

    ```console
    Pipeline run details:
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    RunId             : 0000000000-00000-0000-0000-000000000000
    PipelineName      : GetTableListAndTriggerCopyData
    LastUpdated       : 9/18/2017 4:08:15 PM
    Parameters        : {}
    RunStart          : 9/18/2017 4:06:44 PM
    RunEnd            : 9/18/2017 4:08:15 PM
    DurationInMs      : 90637
    Status            : Succeeded
    Message           : 

    Activity run details:
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    ActivityName      : LookupTableList
    PipelineRunId     : 0000000000-00000-0000-0000-000000000000
    PipelineName      : GetTableListAndTriggerCopyData
    Input             : {source, dataset, firstRowOnly}
    Output            : {count, value, effectiveIntegrationRuntime}
    LinkedServiceName : 
    ActivityRunStart  : 9/18/2017 4:06:46 PM
    ActivityRunEnd    : 9/18/2017 4:07:09 PM
    DurationInMs      : 22995
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}

    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    ActivityName      : TriggerCopy
    PipelineRunId     : 0000000000-00000-0000-0000-000000000000
    PipelineName      : GetTableListAndTriggerCopyData
    Input             : {pipeline, parameters, waitOnCompletion}
    Output            : {pipelineRunId}
    LinkedServiceName : 
    ActivityRunStart  : 9/18/2017 4:07:11 PM
    ActivityRunEnd    : 9/18/2017 4:08:14 PM
    DurationInMs      : 62581
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}
    ```

3. Możesz uzyskać identyfikator uruchomienia potoku „**IterateAndCopySQLTables**” i sprawdzić szczegółowe wyniki uruchomienia działania zgodnie z poniższymi instrukcjami.

    ```powershell
    Write-Host "Pipeline 'IterateAndCopySQLTables' run result:" -foregroundcolor "Yellow"
    ($result | Where-Object {$_.ActivityName -eq "TriggerCopy"}).Output.ToString()
    ```

    Oto dane wyjściowe przykładowego uruchomienia:

    ```json
    {
        "pipelineRunId": "7514d165-14bf-41fb-b5fb-789bea6c9e58"
    }
    ```

    ```powershell
    $result2 = Get-AzDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId <copy above run ID> -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)
    $result2
    ```

3. Połącz się ze swoim ujściam usługi Azure Synapse Analytics i Potwierdź, że dane zostały skopiowane z Azure SQL Database poprawna.

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
>[Przyrostowe kopiowanie danych](tutorial-incremental-copy-powershell.md)
