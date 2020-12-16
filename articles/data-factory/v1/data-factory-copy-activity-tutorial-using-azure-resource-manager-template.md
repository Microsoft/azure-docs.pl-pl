---
title: 'Samouczek: Tworzenie potoku przy użyciu szablonu Menedżer zasobów '
description: W tym samouczku przedstawiono tworzenie potoku usługi Azure Data Factory przy użyciu szablonu usługi Azure Resource Manager. Ten potok kopiuje dane z usługi Azure Blob Storage do Azure SQL Database.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: ''
editor: ''
ms.assetid: 1274e11a-e004-4df5-af07-850b2de7c15e
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: a007e64a7bd034397c2030c435a5ad349bd4acc7
ms.sourcegitcommit: e15c0bc8c63ab3b696e9e32999ef0abc694c7c41
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/16/2020
ms.locfileid: "97608752"
---
# <a name="tutorial-use-azure-resource-manager-template-to-create-a-data-factory-pipeline-to-copy-data"></a>Samouczek: korzystanie z szablonu usługi Azure Resource Manager w celu utworzenia potoku kopiowania danych w usłudze Data Factory 
> [!div class="op_single_selector"]
> * [Przegląd i wymagania wstępne](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Kreator kopiowania](data-factory-copy-data-wizard-tutorial.md)
> * [Program Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
> * [Program PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
> * [Szablon usługi Azure Resource Manager](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
> * [Interfejs API REST](data-factory-copy-activity-tutorial-using-rest-api.md)
> * [Interfejs API .NET](data-factory-copy-activity-tutorial-using-dotnet-api.md)
> 

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli korzystasz z bieżącej wersji usługi Data Factory, zobacz [samouczek dotyczący działania kopiowania](../quickstart-create-data-factory-dot-net.md). 

W tym samouczku wyjaśniono, jak skorzystać z szablonu usługi Azure Resource Manager w celu utworzenia fabryki danych na platformie Azure. Potok danych przedstawiony w tym samouczku kopiuje dane ze źródłowego do docelowego magazynu danych. Nie przekształca on danych wejściowych w celu wygenerowania danych wyjściowych. Aby zapoznać się z samouczkiem dotyczącym przekształcania danych za pomocą usługi Azure Data Factory, zobacz [Tutorial: Build a pipeline to transform data using Hadoop cluster](data-factory-build-your-first-pipeline.md) (Samouczek: Tworzenie potoku przekształcającego dane przy użyciu klastra Hadoop).

W tym samouczku opisano tworzenie potoku z jednym działaniem (Działanie kopiowania). Działanie kopiowania kopiuje dane z obsługiwanego magazynu danych do obsługiwanego magazynu danych ujścia. Aby zapoznać się z listą magazynów danych obsługiwanych jako źródła i ujścia, zobacz [obsługiwane magazyny danych](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Działanie jest obsługiwane przez globalnie dostępną usługę, która może kopiować dane między różnymi magazynami danych w sposób bezpieczny, niezawodny i skalowalny. Więcej informacji o działaniu kopiowania znajduje się w temacie [działania dotyczące przenoszenia danych](data-factory-data-movement-activities.md).

Potok może obejmować więcej niż jedno działanie. Dwa działania można połączyć w łańcuch (uruchomić jedno działanie po drugim), ustawiając wyjściowy zestaw danych jednego działania jako zestaw wejściowy drugiego. Aby uzyskać więcej informacji, zobacz sekcję dotyczącą [wielu działań w potoku](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline). 

> [!NOTE] 
> Potok danych przedstawiony w tym samouczku kopiuje dane ze źródłowego do docelowego magazynu danych. Aby zapoznać się z samouczkiem dotyczącym przekształcania danych za pomocą usługi Azure Data Factory, zobacz [Tutorial: Build a pipeline to transform data using Hadoop cluster](data-factory-build-your-first-pipeline.md) (Samouczek: Tworzenie potoku przekształcającego dane przy użyciu klastra Hadoop). 

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

* Zapoznaj się z artykułem [Tutorial Overview and Prerequisites](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) (Samouczek: Przegląd i wymagania wstępne) i wykonaj kroki **wymagań wstępnych**.
* Postępuj zgodnie z instrukcjami w artykule [How to install and configure Azure PowerShell](/powershell/azure/) (Instalowanie i konfigurowanie programu Azure PowerShell), aby zainstalować najnowszą wersję programu Azure PowerShell na komputerze. W tym samouczku program PowerShell służy do wdrażania jednostek usługi Data Factory. 
* (Opcjonalnie) Informacje na temat szablonów usługi Azure Resource Manager zawiera temat [Tworzenie szablonów usługi Azure Resource Manager](../../azure-resource-manager/templates/template-syntax.md).

## <a name="in-this-tutorial"></a>Informacje o tym samouczku
W ramach tego samouczka jest tworzona fabryka danych za pomocą następujących jednostek usługi Data Factory:

| Jednostka | Opis |
| --- | --- |
| Połączona usługa Azure Storage |Łączy konto usługi Azure Storage z fabryką danych. Usługa Azure Storage to źródłowy magazyn danych, a Azure SQL Database to magazyn danych ujścia dla działania kopiowania w samouczku. Określa ona konto magazynu, które zawiera dane wejściowe dla działania kopiowania. |
| Połączona usługa Azure SQL Database |Łączy nasze bazy danych w Azure SQL Database z fabryką danych. Określa bazę danych, która przechowuje dane wyjściowe dla działania kopiowania. |
| Wejściowy zestaw danych obiektów blob platformy Azure |Przywołuje połączoną usługę Azure Storage. Połączona usługa przywołuje konto usługi Azure Storage, a zestaw danych obiektów blob platformy Azure określa kontener, folder i nazwę pliku w magazynie, który przechowuje dane wejściowe. |
| Wyjściowy zestaw danych usługi Azure SQL |Przywołuje połączoną usługę Azure SQL. Połączona usługa Azure SQL odwołuje się do logicznego serwera SQL, a zestaw danych usługi Azure SQL określa nazwę tabeli zawierającej dane wyjściowe. |
| Potok danych |Potok ma jedno działanie typu Kopiuj, które pobiera zestaw danych obiektów blob platformy Azure jako dane wejściowe i przekazuje zestaw danych usługi Azure SQL jako dane wyjściowe. Działanie kopiowania kopiuje dane z obiektu blob platformy Azure do tabeli w Azure SQL Database. |

Fabryka danych może obejmować jeden lub wiele potoków. Potok może obejmować jedno lub wiele działań. Istnieją dwa typy działań: [przenoszenia danych](data-factory-data-movement-activities.md) i [przekształcania danych](data-factory-data-transformation-activities.md). W tym samouczku jest tworzony potok z jednym działaniem (kopiowania).

![Kopiowanie z obiektu blob platformy Azure do usługi Azure SQL Database](media/data-factory-copy-activity-tutorial-using-azure-resource-manager-template/CopyBlob2SqlDiagram.png) 

W poniższej sekcji przedstawiono pełny szablon usługi Resource Manager umożliwiający definiowanie jednostek usługi Data Factory, dzięki czemu można szybko przejść przez samouczek i przetestować szablon. Aby dowiedzieć się, jak jest zdefiniowana każda jednostka usługi Data Factory, zobacz sekcję [Jednostki usługi Data Factory w szablonie](#data-factory-entities-in-the-template).

## <a name="data-factory-json-template"></a>Szablon JSON usługi Data Factory
Szablon najwyższego poziomu usługi Resource Manager umożliwiający zdefiniowanie fabryki danych to: 

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": { ...
    },
    "variables": { ...
    },
    "resources": [
        {
            "name": "[parameters('dataFactoryName')]",
            "apiVersion": "[variables('apiVersion')]",
            "type": "Microsoft.DataFactory/datafactories",
            "location": "westus",
            "resources": [
                { ... },
                { ... },
                { ... },
                { ... }
            ]
        }
    ]
}
```
Utwórz plik JSON o nazwie **ADFCopyTutorialARM.json** w folderze **C:\ADFGetStarted** o następującej zawartości:

```json
{
    "contentVersion": "1.0.0.0",
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "parameters": {
      "storageAccountName": { "type": "string", "metadata": { "description": "Name of the Azure storage account that contains the data to be copied." } },
      "storageAccountKey": { "type": "securestring", "metadata": { "description": "Key for the Azure storage account." } },
      "sourceBlobContainer": { "type": "string", "metadata": { "description": "Name of the blob container in the Azure Storage account." } },
      "sourceBlobName": { "type": "string", "metadata": { "description": "Name of the blob in the container that has the data to be copied to Azure SQL Database table" } },
      "sqlServerName": { "type": "string", "metadata": { "description": "Name of the logical SQL server that will hold the output/copied data." } },
      "databaseName": { "type": "string", "metadata": { "description": "Name of the Azure SQL Database in the logical SQL server." } },
      "sqlServerUserName": { "type": "string", "metadata": { "description": "Name of the user that has access to the logical SQL server." } },
      "sqlServerPassword": { "type": "securestring", "metadata": { "description": "Password for the user." } },
      "targetSQLTable": { "type": "string", "metadata": { "description": "Table in the Azure SQL Database that will hold the copied data." } 
      } 
    },
    "variables": {
      "dataFactoryName": "[concat('AzureBlobToAzureSQLDatabaseDF', uniqueString(resourceGroup().id))]",
      "azureSqlLinkedServiceName": "AzureSqlLinkedService",
      "azureStorageLinkedServiceName": "AzureStorageLinkedService",
      "blobInputDatasetName": "BlobInputDataset",
      "sqlOutputDatasetName": "SQLOutputDataset",
      "pipelineName": "Blob2SQLPipeline"
    },
    "resources": [
      {
        "name": "[variables('dataFactoryName')]",
        "apiVersion": "2015-10-01",
        "type": "Microsoft.DataFactory/datafactories",
        "location": "West US",
        "resources": [
          {
            "type": "linkedservices",
            "name": "[variables('azureStorageLinkedServiceName')]",
            "dependsOn": [
              "[variables('dataFactoryName')]"
            ],
            "apiVersion": "2015-10-01",
            "properties": {
              "type": "AzureStorage",
              "description": "Azure Storage linked service",
              "typeProperties": {
                "connectionString": "[concat('DefaultEndpointsProtocol=https;AccountName=',parameters('storageAccountName'),';AccountKey=',parameters('storageAccountKey'))]"
              }
            }
          },
          {
            "type": "linkedservices",
            "name": "[variables('azureSqlLinkedServiceName')]",
            "dependsOn": [
              "[variables('dataFactoryName')]"
            ],
            "apiVersion": "2015-10-01",
            "properties": {
              "type": "AzureSqlDatabase",
              "description": "Azure SQL linked service",
              "typeProperties": {
                "connectionString": "[concat('Server=tcp:',parameters('sqlServerName'),'.database.windows.net,1433;Database=', parameters('databaseName'), ';User ID=',parameters('sqlServerUserName'),';Password=',parameters('sqlServerPassword'),';Trusted_Connection=False;Encrypt=True;Connection Timeout=30')]"
              }
            }
          },
          {
            "type": "datasets",
            "name": "[variables('blobInputDatasetName')]",
            "dependsOn": [
              "[variables('dataFactoryName')]",
              "[variables('azureStorageLinkedServiceName')]"
            ],
            "apiVersion": "2015-10-01",
            "properties": {
              "type": "AzureBlob",
              "linkedServiceName": "[variables('azureStorageLinkedServiceName')]",
              "structure": [
                {
                  "name": "Column0",
                  "type": "String"
                },
                {
                  "name": "Column1",
                  "type": "String"
                }
              ],
              "typeProperties": {
                "folderPath": "[concat(parameters('sourceBlobContainer'), '/')]",
                "fileName": "[parameters('sourceBlobName')]",
                "format": {
                  "type": "TextFormat",
                  "columnDelimiter": ","
                }
              },
              "availability": {
                "frequency": "Hour",
                "interval": 1
              },
              "external": true
            }
          },
          {
            "type": "datasets",
            "name": "[variables('sqlOutputDatasetName')]",
            "dependsOn": [
              "[variables('dataFactoryName')]",
              "[variables('azureSqlLinkedServiceName')]"
            ],
            "apiVersion": "2015-10-01",
            "properties": {
              "type": "AzureSqlTable",
              "linkedServiceName": "[variables('azureSqlLinkedServiceName')]",
              "structure": [
                {
                  "name": "FirstName",
                  "type": "String"
                },
                {
                  "name": "LastName",
                  "type": "String"
                }
              ],
              "typeProperties": {
                "tableName": "[parameters('targetSQLTable')]"
              },
              "availability": {
                "frequency": "Hour",
                "interval": 1
              }
            }
          },
          {
            "type": "datapipelines",
            "name": "[variables('pipelineName')]",
            "dependsOn": [
              "[variables('dataFactoryName')]",
              "[variables('azureStorageLinkedServiceName')]",
              "[variables('azureSqlLinkedServiceName')]",
              "[variables('blobInputDatasetName')]",
              "[variables('sqlOutputDatasetName')]"
            ],
            "apiVersion": "2015-10-01",
            "properties": {
              "activities": [
                {
                  "name": "CopyFromAzureBlobToAzureSQL",
                  "description": "Copy data frm Azure blob to Azure SQL",
                  "type": "Copy",
                  "inputs": [
                    {
                      "name": "[variables('blobInputDatasetName')]"
                    }
                  ],
                  "outputs": [
                    {
                      "name": "[variables('sqlOutputDatasetName')]"
                    }
                  ],
                  "typeProperties": {
                    "source": {
                      "type": "BlobSource"
                    },
                    "sink": {
                      "type": "SqlSink",
                      "sqlWriterCleanupScript": "$$Text.Format('DELETE FROM {0}', 'emp')"
                    },
                    "translator": {
                      "type": "TabularTranslator",
                      "columnMappings": "Column0:FirstName,Column1:LastName"
                    }
                  },
                  "Policy": {
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 3,
                    "timeout": "01:00:00"
                  }
                }
              ],
              "start": "2017-05-11T00:00:00Z",
              "end": "2017-05-12T00:00:00Z"
            }
          }
        ]
      }
    ]
  }
```

## <a name="parameters-json"></a>Parametry JSON
Utwórz plik JSON o nazwie **ADFCopyTutorialARM-Parameters.json** zawierający parametry szablonu usługi Azure Resource Manager. 

> [!IMPORTANT]
> Określ nazwę i klucz konta usługi Azure Storage za pomocą parametrów storageAccountName i storageAccountKey.  
> 
> Określ logicznego serwera SQL, bazy danych, użytkownika i hasła dla parametrów SqlServerName, databaseName, sqlServerUserName i sqlServerPassword.  

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": { 
        "storageAccountName": { "value": "<Name of the Azure storage account>"    },
        "storageAccountKey": {
            "value": "<Key for the Azure storage account>"
        },
        "sourceBlobContainer": { "value": "adftutorial" },
        "sourceBlobName": { "value": "emp.txt" },
        "sqlServerName": { "value": "<Name of the logical SQL server>" },
        "databaseName": { "value": "<Name of the database>" },
        "sqlServerUserName": { "value": "<Name of the user who has access to the database>" },
        "sqlServerPassword": { "value": "<password for the user>" },
        "targetSQLTable": { "value": "emp" }
    }
}
```

> [!IMPORTANT]
> Można używać oddzielnych plików parametrów JSON dla środowisk programistycznego, testowego i produkcyjnego do użycia z tym samym szablonem JSON usługi Data Factory. Za pomocą skryptu programu Power Shell można zautomatyzować wdrażanie jednostek usługi Data Factory w tych środowiskach.  
> 
> 

## <a name="create-data-factory"></a>Tworzenie fabryki danych
1. Uruchom **Azure PowerShell** i uruchom następujące polecenie:
   * Uruchom poniższe polecenie i wprowadź nazwę użytkownika oraz hasło, których używasz do logowania się w witrynie Azure Portal.
   
    ```PowerShell
    Connect-AzAccount       
    ```  
   * Uruchom poniższe polecenie, aby wyświetlić wszystkie subskrypcje dla tego konta.
   
    ```PowerShell
    Get-AzSubscription
    ```   
   * Uruchom poniższe polecenie, aby wybrać subskrypcję, z którą chcesz pracować.
    
    ```PowerShell
    Get-AzSubscription -SubscriptionName <SUBSCRIPTION NAME> | Set-AzContext
    ```    
2. Uruchom następujące polecenie, aby wdrożyć jednostki usługi Data Factory przy użyciu szablonu usługi Resource Manager utworzonego w kroku 1.

    ```PowerShell   
    New-AzResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile C:\ADFGetStarted\ADFCopyTutorialARM.json -TemplateParameterFile C:\ADFGetStarted\ADFCopyTutorialARM-Parameters.json
    ```

## <a name="monitor-pipeline"></a>Monitorowanie potoku

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com) przy użyciu konta Azure.

1. Kliknij pozycję **Fabryki danych** w menu po lewej lub pozycję **Wszystkie usługi**, a następnie pozycję **Fabryki danych** w obszarze **ZBIERANIE DANYCH I ANALIZA**.
   
    ![Menu fabryk danych](media/data-factory-copy-activity-tutorial-using-azure-resource-manager-template/data-factories-menu.png)

1. Na stronie **Fabryki danych** wyszukaj fabrykę danych (AzureBlobToAzureSQLDatabaseDF). 
   
    ![Wyszukiwanie fabryki danych](media/data-factory-copy-activity-tutorial-using-azure-resource-manager-template/search-for-data-factory.png)  

1. Kliknij fabrykę danych platformy Azure. Zostanie wyświetlona strona główna fabryki danych.
   
    ![Strona główna fabryki danych](media/data-factory-copy-activity-tutorial-using-azure-resource-manager-template/data-factory-home-page.png)  

1. Aby uzyskać instrukcje dotyczące monitorowania potoku i zestawów danych utworzonych w ramach tego samouczka, zobacz temat [Monitorowanie zestawów danych i potoku](data-factory-monitor-manage-pipelines.md). Obecnie program Visual Studio nie obsługuje monitorowania potoków usługi Data Factory.

1. Gdy wycinek jest w stanie **gotowe** , sprawdź, czy dane zostały skopiowane do tabeli **EMP** w Azure SQL Database.

Aby uzyskać więcej informacji dotyczących korzystania z bloków w witrynie Azure Portal w celu monitorowania potoku i zestawów danych utworzonych przez siebie w ramach tego samouczka, zobacz temat [Monitorowanie zestawów danych i potoku](data-factory-monitor-manage-pipelines.md).

Aby uzyskać więcej informacji na temat korzystania z aplikacji Monitorowanie i zarządzanie w celu monitorowania potoków danych, zobacz temat [Monitor and manage Azure Data Factory pipelines using Monitoring App](data-factory-monitor-manage-app.md) (Monitorowanie potoków usługi Azure Data Factory oraz zarządzanie nimi za pomocą Aplikacji do monitorowania).

## <a name="data-factory-entities-in-the-template"></a>Jednostki usługi Data Factory w szablonie

### <a name="define-data-factory"></a>Definiowanie fabryki danych

Fabrykę danych definiuje się w szablonie usługi Resource Manager jak pokazano w następującym przykładzie:

```json
{
  "resources": [
    {
      "name": "[variables('dataFactoryName')]",
      "apiVersion": "2015-10-01",
      "type": "Microsoft.DataFactory/datafactories",
      "location": "West US"
    }
  ]
}
```

Parametr dataFactoryName jest zdefiniowany jako: 

```json
{
    "dataFactoryName": "[concat('AzureBlobToAzureSQLDatabaseDF', uniqueString(resourceGroup().id))]"
}
```

To jest unikatowy ciąg oparty na identyfikatorze grupy zasobów.

### <a name="defining-data-factory-entities"></a>Definiowanie jednostek usługi Data Factory

Następujące jednostki usługi Data Factory są zdefiniowane w szablonie JSON: 

1. [Połączona usługa Azure Storage](#azure-storage-linked-service)
2. [Połączona usługa Azure SQL](#azure-sql-database-linked-service)
3. [Zestaw danych obiektów blob platformy Azure](#azure-blob-dataset)
4. [Zestaw danych usługi Azure SQL](#azure-sql-dataset)
5. [Potok danych z działaniem kopiowania](#data-pipeline)

#### <a name="azure-storage-linked-service"></a>Połączona usługa Azure Storage

Polecenie AzureStorageLinkedService łączy konto usługi Azure Storage z fabryką danych. W ramach [wymagań wstępnych](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) utworzono kontener i przekazano dane na to konto magazynu. W tej sekcji określa się nazwę i klucz konta magazynu platformy Azure. Szczegóły dotyczące właściwości JSON używanych do definiowania połączonej usługi Azure Storage zawiera temat [Połączona usługa Azure Storage](data-factory-azure-blob-connector.md#azure-storage-linked-service). 

```json
{
    "type": "linkedservices",
    "name": "[variables('azureStorageLinkedServiceName')]",
    "dependsOn": [
        "[variables('dataFactoryName')]"
    ],
    "apiVersion": "2015-10-01",
    "properties": {
        "type": "AzureStorage",
        "description": "Azure Storage linked service",
        "typeProperties": {
            "connectionString": "[concat('DefaultEndpointsProtocol=https;AccountName=',parameters('storageAccountName'),';AccountKey=',parameters('storageAccountKey'))]"
        }
    }
}
```

Parametr connectionString używa parametrów storageAccountName i storageAccountKey. Wartości tych parametrów są przekazywane przy użyciu pliku konfiguracji. Definicja używa także zmiennych azureStorageLinkedService i dataFactoryName zdefiniowanych w szablonie. 

#### <a name="azure-sql-database-linked-service"></a>Połączona usługa Azure SQL Database

AzureSqlLinkedService łączy swoją bazę danych w Azure SQL Database z fabryką danych. W tej bazie danych są przechowywane dane skopiowane z magazynu obiektów blob. Tabelę emp w tej bazie danych utworzono w ramach [wymagań wstępnych](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md). W tej sekcji należy określić nazwę logicznego serwera SQL, nazwę bazy danych, nazwę użytkownika i hasło użytkownika. Szczegóły dotyczące właściwości JSON używanych do definiowania połączonej usługi Azure SQL zawiera temat [Połączona usługa Azure SQL](data-factory-azure-sql-connector.md#linked-service-properties).  

```json
{
    "type": "linkedservices",
    "name": "[variables('azureSqlLinkedServiceName')]",
    "dependsOn": [
      "[variables('dataFactoryName')]"
    ],
    "apiVersion": "2015-10-01",
    "properties": {
      "type": "AzureSqlDatabase",
      "description": "Azure SQL linked service",
      "typeProperties": {
        "connectionString": "[concat('Server=tcp:',parameters('sqlServerName'),'.database.windows.net,1433;Database=', parameters('databaseName'), ';User ID=',parameters('sqlServerUserName'),';Password=',parameters('sqlServerPassword'),';Trusted_Connection=False;Encrypt=True;Connection Timeout=30')]"
      }
    }
}
```

Parametr connectionString używa parametrów sqlServerName, databaseName, sqlServerUserName i sqlServerPassword, których wartości są przekazywane za pomocą pliku konfiguracji. Definicja używa także następujących zmiennych z szablonu: azureSqlLinkedServiceName i dataFactoryName.

#### <a name="azure-blob-dataset"></a>Zestaw danych obiektów blob platformy Azure
Połączona usługa magazynu Azure określa parametry połączenia, z których korzysta usługa Data Factory w czasie wykonywania, aby połączyć się z kontem magazynu Azure. W definicji zestawu danych obiektów blob Azure określane są nazwy kontenera obiektów blob, folderu i pliku, który zawiera dane wejściowe. Szczegóły dotyczące właściwości JSON używanych do definiowania zestawu danych obiektów blob platformy Azure zawiera temat [Azure Blob dataset properties](data-factory-azure-blob-connector.md#dataset-properties) (Właściwości zestawu danych obiektów blob plaformy Azure). 

```json
{
    "type": "datasets",
    "name": "[variables('blobInputDatasetName')]",
    "dependsOn": [
      "[variables('dataFactoryName')]",
      "[variables('azureStorageLinkedServiceName')]"
    ],
    "apiVersion": "2015-10-01",
    "properties": {
        "type": "AzureBlob",
          "linkedServiceName": "[variables('azureStorageLinkedServiceName')]",
        "structure": [
        {
              "name": "Column0",
              "type": "String"
        },
        {
              "name": "Column1",
              "type": "String"
        }
          ],
          "typeProperties": {
            "folderPath": "[concat(parameters('sourceBlobContainer'), '/')]",
            "fileName": "[parameters('sourceBlobName')]",
            "format": {
                  "type": "TextFormat",
                  "columnDelimiter": ","
            }
          },
          "availability": {
            "frequency": "Hour",
            "interval": 1
          },
          "external": true
    }
}
```

#### <a name="azure-sql-dataset"></a>Zestaw danych usługi Azure SQL
Należy określić nazwę tabeli w Azure SQL Database, która zawiera skopiowane dane z usługi Azure Blob Storage. Szczegóły dotyczące właściwości JSON używanych do definiowania zestawu danych usługi Azure SQL zawiera temat [Azure SQL dataset properties](data-factory-azure-sql-connector.md#dataset-properties) (Właściwości zestawu danych usługi Azure SQL). 

```json
{
    "type": "datasets",
    "name": "[variables('sqlOutputDatasetName')]",
    "dependsOn": [
        "[variables('dataFactoryName')]",
          "[variables('azureSqlLinkedServiceName')]"
    ],
    "apiVersion": "2015-10-01",
    "properties": {
          "type": "AzureSqlTable",
          "linkedServiceName": "[variables('azureSqlLinkedServiceName')]",
          "structure": [
        {
              "name": "FirstName",
              "type": "String"
        },
        {
              "name": "LastName",
              "type": "String"
        }
          ],
          "typeProperties": {
            "tableName": "[parameters('targetSQLTable')]"
          },
          "availability": {
            "frequency": "Hour",
            "interval": 1
          }
    }
}
```

#### <a name="data-pipeline"></a>Potok danych
Definiuje się potok, który kopiuje dane z zestawu danych obiektów blob platformy Azure do zestawu danych usługi Azure SQL. Opisy elementów JSON używanych do definiowania potoku w tym przykładzie zawiera temat [Pipeline JSON](data-factory-create-pipelines.md#pipeline-json) (Kod JSON potoku). 

```json
{
    "type": "datapipelines",
    "name": "[variables('pipelineName')]",
    "dependsOn": [
        "[variables('dataFactoryName')]",
          "[variables('azureStorageLinkedServiceName')]",
          "[variables('azureSqlLinkedServiceName')]",
          "[variables('blobInputDatasetName')]",
          "[variables('sqlOutputDatasetName')]"
    ],
    "apiVersion": "2015-10-01",
    "properties": {
          "activities": [
        {
              "name": "CopyFromAzureBlobToAzureSQL",
              "description": "Copy data frm Azure blob to Azure SQL",
              "type": "Copy",
              "inputs": [
            {
                  "name": "[variables('blobInputDatasetName')]"
            }
              ],
              "outputs": [
            {
                  "name": "[variables('sqlOutputDatasetName')]"
            }
              ],
              "typeProperties": {
                "source": {
                      "type": "BlobSource"
                },
                "sink": {
                      "type": "SqlSink",
                      "sqlWriterCleanupScript": "$$Text.Format('DELETE FROM {0}', 'emp')"
                },
                "translator": {
                      "type": "TabularTranslator",
                      "columnMappings": "Column0:FirstName,Column1:LastName"
                }
              },
              "Policy": {
                "concurrency": 1,
                "executionPriorityOrder": "NewestFirst",
                "retry": 3,
                "timeout": "01:00:00"
              }
        }
          ],
          "start": "2017-05-11T00:00:00Z",
          "end": "2017-05-12T00:00:00Z"
    }
}
```

## <a name="reuse-the-template"></a>Ponowne użycie szablonu
W ramach samouczka został utworzony szablon służący do definiowania jednostek usługi Data Factory i szablon służący do przekazywania wartości dla parametrów. Potok kopiuje dane z konta usługi Azure Storage do Azure SQL Database określonych za pośrednictwem parametrów. Aby użyć tego samego szablonu do wdrożenia jednostek usługi Data Factory w różnych środowiskach, należy utworzyć plik parametrów dla każdego środowiska i użyć go podczas wdrażania do środowiska.     

Przykład:  

```PowerShell
New-AzResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFCopyTutorialARM.json -TemplateParameterFile ADFCopyTutorialARM-Parameters-Dev.json
```
```PowerShell
New-AzResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFCopyTutorialARM.json -TemplateParameterFile ADFCopyTutorialARM-Parameters-Test.json
```
```PowerShell
New-AzResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFCopyTutorialARM.json -TemplateParameterFile ADFCopyTutorialARM-Parameters-Production.json
```

Należy zauważyć, że pierwsze polecenie używa pliku parametrów dla środowiska programistycznego, drugie dla środowiska testowego, a trzecie dla środowiska produkcyjnego.  

Można także ponownie użyć szablonu do wykonywania powtarzających się zadań. Na przykład w sytuacji, gdy jest potrzebne utworzenie wielu fabryk danych z co najmniej jednym potokiem, które implementują tę samą logikę, lecz każda fabryka danych używa innych kont usług Storage i SQL Database. W tym scenariuszu do tworzenia fabryk danych jest używany ten sam szablon w tym samym środowisku (programistycznym, testowym lub produkcyjnym) w połączeniu z różnymi plikami parametrów.   

## <a name="next-steps"></a>Następne kroki
W tym samouczku użyto magazynu obiektów blob platformy Azure jako źródłowego magazynu danych i Azure SQL Database jako docelowy magazyn danych w operacji kopiowania. Poniższa tabela zawiera listę magazynów danych obsługiwanych przez działanie kopiowania jako źródła i lokalizacje docelowe: 

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

Aby uzyskać informacje dotyczące kopiowania danych do/z magazynu danych, kliknij link do magazynu danych w tabeli.
