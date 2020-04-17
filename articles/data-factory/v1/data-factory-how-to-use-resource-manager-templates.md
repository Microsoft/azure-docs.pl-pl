---
title: Używanie szablonów Menedżera zasobów w fabryce danych
description: Dowiedz się, jak tworzyć szablony usługi Azure Resource Manager i używać ich do tworzenia encji usługi Data Factory.
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
ms.openlocfilehash: 73498b3537f4cf9313fc9e2464785f63c2af0d5a
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81460736"
---
# <a name="use-templates-to-create-azure-data-factory-entities"></a>Tworzenie encji usługi Azure Data Factory za pomocą szablonów
> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. 

## <a name="overview"></a>Omówienie
Podczas korzystania z usługi Azure Data Factory dla potrzeb integracji danych, może się okazać, że ponowne użycie tego samego wzorca w różnych środowiskach lub implementowanie tego samego zadania powtarzalnie w ramach tego samego rozwiązania. Szablony ułatwiają wdrażanie tych scenariuszy i zarządzanie nimi w łatwy sposób. Szablony w usłudze Azure Data Factory są idealne dla scenariuszy, które obejmują możliwość ponownego użycia i powtórzenia.

Rozważmy sytuację, w której organizacja ma 10 zakładów produkcyjnych na całym świecie. Dzienniki z każdej instalacji są przechowywane w oddzielnej lokalnej bazie danych programu SQL Server. Firma chce zbudować pojedynczy magazyn danych w chmurze do analizy ad hoc. Chce również mieć tę samą logikę, ale różne konfiguracje dla środowisk deweloperskich, testowych i produkcyjnych.

W takim przypadku zadanie musi być powtórzone w tym samym środowisku, ale z różnymi wartościami w 10 fabrykach danych dla każdego zakładu produkcyjnego. W efekcie **powtarzanie** jest obecne. Formowanie umożliwia abstrakcję tego ogólnego przepływu (czyli potoków mających takie same działania w każdej fabryce danych), ale używa oddzielnego pliku parametrów dla każdego zakładu produkcyjnego.

Ponadto, ponieważ organizacja chce wdrożyć te 10 fabryk danych wiele razy w różnych środowiskach, szablony można użyć tego **ponownego użycia,** wykorzystując oddzielne pliki parametrów do tworzenia, testowania i środowisk produkcyjnych.

## <a name="templating-with-azure-resource-manager"></a>Tworzenie szablonów za pomocą usługi Azure Resource Manager
[Szablony usługi Azure Resource Manager](../../azure-resource-manager/templates/overview.md) to świetny sposób na uzyskanie szablonów w usłudze Azure Data Factory. Szablony Menedżera zasobów definiują infrastrukturę i konfigurację rozwiązania platformy Azure za pomocą pliku JSON. Ponieważ szablony usługi Azure Resource Manager współpracują ze wszystkimi/większością usług platformy Azure, mogą być szeroko używane do łatwego zarządzania wszystkimi zasobami zasobów platformy Azure. Zobacz [Tworzenie szablonów usługi Azure Resource Manager,](../../azure-resource-manager/templates/template-syntax.md) aby dowiedzieć się więcej o szablonach Menedżera zasobów w ogóle.

## <a name="tutorials"></a>Samouczki
Zobacz następujące samouczki, aby uzyskać instrukcje krok po kroku dotyczące tworzenia jednostek usługi Data Factory przy użyciu szablonów Menedżera zasobów:

* [Samouczek: Tworzenie potoku w celu skopiowania danych przy użyciu szablonu usługi Azure Resource Manager](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
* [Samouczek: Tworzenie potoku do przetwarzania danych przy użyciu szablonu usługi Azure Resource Manager](data-factory-build-your-first-pipeline.md)

## <a name="data-factory-templates-on-github"></a>Szablony fabryki danych w usłudze GitHub
Zapoznaj się z następującymi szablonami szybkiego startu platformy Azure w usłudze GitHub:

* [Tworzenie fabryki danych w celu kopiowania danych z usługi Azure Blob Storage do bazy danych SQL usługi Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-blob-to-sql-copy)
* [Tworzenie fabryki danych z aktywnością hive w klastrze usługi Azure HDInsight](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-hive-transformation)
* [Tworzenie fabryki danych w celu kopiowania danych z salesforce do obiektów blob platformy Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-salesforce-to-blob-copy)
* [Tworzenie fabryki danych, która tworzy działania łańcuchowe: kopiuje dane z serwera FTP do obiektów blob platformy Azure, wywołuje skrypt gałęzi w klastrze HDInsight na żądanie, aby przekształcić dane, i kopiuje wynik do bazy danych SQL usługi Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-data-factory-ftp-hive-blob)

Możesz udostępnić szablony usługi Azure Data Factory na [platformie Azure Quick start](https://azure.microsoft.com/documentation/templates/). Zapoznaj się z [przewodnikiem po składkach](https://github.com/Azure/azure-quickstart-templates/tree/master/1-CONTRIBUTION-GUIDE) podczas opracowywania szablonów, które można udostępniać za pośrednictwem tego repozytorium.

W poniższych sekcjach znajdują się szczegółowe informacje na temat definiowania zasobów fabryki danych w szablonie Menedżera zasobów.

## <a name="defining-data-factory-resources-in-templates"></a>Definiowanie zasobów fabryki danych w szablonach
Szablon najwyższego poziomu do definiowania fabryki danych jest:

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
DataFactoryName jest zdefiniowany w "zmiennych" jako:

```JSON
"dataFactoryName": "[concat('<myDataFactoryName>', uniqueString(resourceGroup().id))]",
```

### <a name="define-linked-services"></a>Definiowanie połączonych usług

```JSON
"type": "linkedservices",
"name": "[variables('<LinkedServiceName>')]",
"apiVersion": "2015-10-01",
"dependsOn": [ "[variables('<dataFactoryName>')]" ],
"properties": {
    ...
}
```

Aby uzyskać szczegółowe informacje na temat właściwości JSON dla określonej połączonej usługi, którą chcesz wdrożyć, zobacz [Usługa połączona magazynowania](data-factory-azure-blob-connector.md#azure-storage-linked-service) lub usługi połączone z [danymi obliczeniowymi.](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) Parametr "dependsOn" określa nazwę odpowiedniej fabryki danych. Przykład definiowania połączonej usługi dla usługi Azure Storage jest wyświetlany w następującej definicji JSON:

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
Szczegółowe informacje na temat właściwości JSON dla określonego typu zestawu danych, który chcesz wdrożyć, można znaleźć w [obsługiwanych magazynach danych.](data-factory-data-movement-activities.md#supported-data-stores-and-formats) Należy zauważyć, że parametr "dependsOn" określa nazwę odpowiedniej fabryki danych i usługi połączonej z magazynowaniem. Przykład definiowania typu zestawu danych magazynu obiektów blob platformy Azure jest pokazany w następującej definicji JSON:

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

Zapoznaj się [z definiowaniem potoków,](data-factory-create-pipelines.md#pipeline-json) aby uzyskać szczegółowe informacje na temat właściwości JSON do definiowania określonego potoku i działań, które chcesz wdrożyć. Należy zauważyć, że parametr "dependsOn" określa nazwę fabryki danych oraz wszystkie odpowiednie połączone usługi lub zestawy danych. Przykład potoku, który kopiuje dane z usługi Azure Blob Storage do usługi Azure SQL Database jest wyświetlany w następującym urywek JSON:

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
## <a name="parameterizing-data-factory-template"></a>Szablon usługi data factory parametryczny
Aby uzyskać najlepsze rozwiązania dotyczące parametryzacji, zobacz [Najważniejsze wskazówki dotyczące tworzenia szablonów usługi Azure Resource Manager](../../azure-resource-manager/resource-manager-template-best-practices.md). Ogólnie rzecz biorąc użycie parametrów należy zminimalizować, zwłaszcza jeśli zmienne mogą być używane zamiast. Podaj tylko parametry w następujących scenariuszach:

* Ustawienia różnią się w zależności od środowiska (np. programowania, testowania i produkcji)
* Wpisy tajne (takie jak hasła)

Jeśli chcesz pobierać wpisy tajne z [usługi Azure Key Vault](../../key-vault/general/overview.md) podczas wdrażania jednostek usługi Azure Data Factory przy użyciu szablonów, określ magazyn **kluczy** i **nazwę tajnego,** jak pokazano w poniższym przykładzie:

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
> Podczas eksportowania szablonów dla istniejących fabryk danych nie jest jeszcze obsługiwane, jest w pracach.
>
>
