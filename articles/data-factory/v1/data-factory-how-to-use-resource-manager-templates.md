---
title: Korzystanie z szablonów Menedżer zasobów w programie Data Factory
description: Dowiedz się, jak tworzyć i używać Azure Resource Manager szablonów do tworzenia jednostek Data Factory.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: b5df2f9cd0a47e5a89d7ff2eb12aefe593cae7a8
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/27/2020
ms.locfileid: "92631482"
---
# <a name="use-templates-to-create-azure-data-factory-entities"></a>Tworzenie jednostek Azure Data Factory przy użyciu szablonów
> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. 

## <a name="overview"></a>Omówienie
Korzystając z Azure Data Factory na potrzeby integracji danych, można odróżnić się od siebie w różnych środowiskach lub wielokrotnie wdrażać te same zadania w ramach tego samego rozwiązania. Szablony ułatwiają wdrażanie tych scenariuszy i zarządzanie nimi w prosty sposób. Szablony w Azure Data Factory doskonale nadają się do scenariuszy, które wymagają ponownego wykorzystania i powtórzenia.

Rozważ sytuację, w której organizacja ma 10 zakładów produkcyjnych na całym świecie. Dzienniki z poszczególnych zakładów są przechowywane w oddzielnej bazie danych SQL Server. Firma chce utworzyć pojedynczy magazyn danych w chmurze na potrzeby analiz ad hoc. Chce również mieć taką samą logikę, ale różne konfiguracje dla środowisk programistycznych, testowych i produkcyjnych.

W takim przypadku należy powtórzyć zadanie w tym samym środowisku, ale z różnymi wartościami w ramach 10 fabryk danych dla każdego zakładu produkcyjnego. W efekcie występuje **powtarzanie** . Tworzenia szablonów umożliwia abstrakcję tego przepływu ogólnego (to jest potoki mające te same działania w każdej fabryce danych), ale używa osobnego pliku parametrów dla każdego zakładu produkcyjnego.

Ponadto, ponieważ organizacja chce wdrożyć te 10 fabryk danych wiele razy w różnych środowiskach, szablony mogą korzystać z tej samej **użyteczności** , wykorzystując osobne pliki parametrów do tworzenia, testowania i środowiska produkcyjnego.

## <a name="templating-with-azure-resource-manager"></a>Tworzenia szablonów z Azure Resource Manager
[Szablony Azure Resource Manager](../../azure-resource-manager/templates/overview.md) są doskonałym sposobem osiągnięcia tworzenia szablonów w Azure Data Factory. Szablony Menedżer zasobów definiują infrastrukturę i konfigurację rozwiązania platformy Azure za pomocą pliku JSON. Ponieważ szablony Azure Resource Manager współpracują ze wszystkimi/większością usług platformy Azure, może być szeroko używany do łatwego zarządzania wszystkimi zasobami zasobów platformy Azure. Zobacz [Tworzenie szablonów Azure Resource Manager](../../azure-resource-manager/templates/template-syntax.md) , aby dowiedzieć się więcej o szablonach Menedżer zasobów.

## <a name="tutorials"></a>Samouczki
Aby uzyskać instrukcje krok po kroku dotyczące tworzenia jednostek Data Factory przy użyciu szablonów Menedżer zasobów, zobacz następujące samouczki:

* [Samouczek: Tworzenie potoku w celu kopiowania danych przy użyciu szablonu Azure Resource Manager](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
* [Samouczek: Tworzenie potoku w celu przetwarzania danych przy użyciu szablonu Azure Resource Manager](data-factory-build-your-first-pipeline.md)

## <a name="data-factory-templates-on-github"></a>Szablony Data Factory w usłudze GitHub
Zapoznaj się z następującymi szablonami szybkiego startu platformy Azure w witrynie GitHub:

* [Utwórz fabrykę danych, aby skopiować dane z usługi Azure Blob Storage do Azure SQL Database](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-blob-to-sql-copy)
* [Tworzenie fabryki danych przy użyciu działania programu Hive w klastrze usługi Azure HDInsight](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-hive-transformation)
* [Tworzenie fabryki danych w celu kopiowania danych z usług Salesforce do obiektów blob platformy Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-salesforce-to-blob-copy)
* [Utwórz fabrykę danych, która tworzy łańcuch działań: kopiuje dane z serwera FTP do obiektów blob platformy Azure, wywołuje skrypt Hive w klastrze usługi HDInsight na żądanie w celu przekształcenia danych i kopiuje wyniki do Azure SQL Database](https://github.com/Azure/azure-quickstart-templates/tree/master/201-data-factory-ftp-hive-blob)

Możesz korzystać z szablonów Azure Data Factory na [platformie Azure — szybki start](https://azure.microsoft.com/documentation/templates/). Zapoznaj się z [przewodnikiem po](https://github.com/Azure/azure-quickstart-templates/tree/master/1-CONTRIBUTION-GUIDE) opracowywaniu szablonów, które mogą być udostępniane za pośrednictwem tego repozytorium.

Poniższe sekcje zawierają szczegółowe informacje dotyczące definiowania Data Factory zasobów w szablonie Menedżer zasobów.

## <a name="defining-data-factory-resources-in-templates"></a>Definiowanie zasobów Data Factory w szablonach
Szablon najwyższego poziomu służący do definiowania fabryki danych to:

```JSON
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
    { "type": "linkedservices",
        ...
    },
    {"type": "datasets",
        ...
    },
    {"type": "dataPipelines",
        ...
    }
}
```

### <a name="define-data-factory"></a>Definiowanie fabryki danych
Fabrykę danych definiuje się w szablonie usługi Resource Manager jak pokazano w następującym przykładzie:

```JSON
"resources": [
{
    "name": "[variables('<mydataFactoryName>')]",
    "apiVersion": "2015-10-01",
    "type": "Microsoft.DataFactory/datafactories",
    "location": "East US"
}
```
Parametr datafactoryname jest zdefiniowany w "zmiennych" jako:

```JSON
"dataFactoryName": "[concat('<myDataFactoryName>', uniqueString(resourceGroup().id))]",
```

### <a name="define-linked-services"></a>Definiuj połączone usługi

```JSON
"type": "linkedservices",
"name": "[variables('<LinkedServiceName>')]",
"apiVersion": "2015-10-01",
"dependsOn": [ "[variables('<dataFactoryName>')]" ],
"properties": {
    ...
}
```

Aby uzyskać szczegółowe informacje na temat właściwości JSON dla konkretnej połączonej usługi, którą chcesz wdrożyć, zobacz temat [połączona usługa Storage](data-factory-azure-blob-connector.md#azure-storage-linked-service) lub [połączone usługi obliczeniowe](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) . Parametr "dependsOn" określa nazwę odpowiedniej fabryki danych. Przykład definiowania połączonej usługi dla usługi Azure Storage przedstawiono w następującej definicji JSON:

### <a name="define-datasets"></a>Definiowanie zestawów danych

```JSON
"type": "datasets",
"name": "[variables('<myDatasetName>')]",
"dependsOn": [
    "[variables('<dataFactoryName>')]",
    "[variables('<myDatasetLinkedServiceName>')]"
],
"apiVersion": "2015-10-01",
"properties": {
    ...
}
```
Zapoznaj się z [obsługiwanymi magazynami danych](data-factory-data-movement-activities.md#supported-data-stores-and-formats) , aby uzyskać szczegółowe informacje na temat właściwości JSON dla określonego typu zestawu danych, który chcesz wdrożyć. Zwróć uwagę, że parametr "dependsOn" określa nazwę odpowiedniej usługi Data Factory i Storage. Przykład definiowania typu zestawu danych usługi Azure Blob Storage jest przedstawiony w następującej definicji JSON:

```JSON
"type": "datasets",
"name": "[variables('storageDataset')]",
"dependsOn": [
    "[variables('dataFactoryName')]",
    "[variables('storageLinkedServiceName')]"
],
"apiVersion": "2015-10-01",
"properties": {
"type": "AzureBlob",
"linkedServiceName": "[variables('storageLinkedServiceName')]",
"typeProperties": {
    "folderPath": "[concat(parameters('sourceBlobContainer'), '/')]",
    "fileName": "[parameters('sourceBlobName')]",
    "format": {
        "type": "TextFormat"
    }
},
"availability": {
    "frequency": "Hour",
    "interval": 1
}
```

### <a name="define-pipelines"></a>Definiowanie potoków

```JSON
"type": "dataPipelines",
"name": "[variables('<mypipelineName>')]",
"dependsOn": [
    "[variables('<dataFactoryName>')]",
    "[variables('<inputDatasetLinkedServiceName>')]",
    "[variables('<outputDatasetLinkedServiceName>')]",
    "[variables('<inputDataset>')]",
    "[variables('<outputDataset>')]"
],
"apiVersion": "2015-10-01",
"properties": {
    activities: {
        ...
    }
}
```

Zapoznaj się z tematem [Definiowanie potoków](data-factory-create-pipelines.md#pipeline-json) , aby uzyskać szczegółowe informacje na temat właściwości JSON definiujących konkretny potok i działania, które chcesz wdrożyć. Należy zwrócić uwagę, że parametr "dependsOn" określa nazwę fabryki danych i wszystkie odpowiednie połączone usługi lub zestawy. Przykładem potoku, który kopiuje dane z usługi Azure Blob Storage do Azure SQL Database, przedstawiono w poniższym fragmencie kodu JSON:

```JSON
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
    "start": "2016-10-03T00:00:00Z",
    "end": "2016-10-04T00:00:00Z"
}
```
## <a name="parameterizing-data-factory-template"></a>Szablon Data Factory parametryzacja
Najlepsze rozwiązania dotyczące parametryzacja można znaleźć w temacie [najlepsze rozwiązania dotyczące tworzenia szablonów Azure Resource Manager](../../azure-resource-manager/templates/template-best-practices.md). Ogólnie rzecz biorąc, użycie parametrów powinno być zminimalizowane, szczególnie jeśli zamiast tego można użyć zmiennych. Podaj tylko parametry w następujących scenariuszach:

* Ustawienia różnią się w zależności od środowiska (przykład: programowanie, testowanie i produkcja)
* Wpisy tajne (takie jak hasła)

Jeśli musisz pobrać wpisy tajne z [Azure Key Vault](../../key-vault/general/overview.md) podczas wdrażania jednostek Azure Data Factory przy użyciu szablonów, określ **Magazyn kluczy** i **nazwę wpisu tajnego** , jak pokazano w następującym przykładzie:

```JSON
"parameters": {
    "storageAccountKey": {
        "reference": {
            "keyVault": {
                "id":"/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.KeyVault/vaults/<keyVaultName>",
             },
            "secretName": "<secretName>"
           },
       },
       ...
}
```

> [!NOTE]
> Chociaż eksportowanie szablonów istniejących fabryk danych nie jest jeszcze obsługiwane, jest to w trakcie działania.
>
>