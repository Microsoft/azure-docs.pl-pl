---
title: Tworzenie centrum zdarzeń z włączoną funkcją Capture — Azure Event Hubs | Microsoft Docs
description: Tworzenie przestrzeni nazw usługi Azure Event Hubs z jednym centrum zdarzeń i włączanie funkcji przechwytywania przy użyciu szablonu usługi Azure Resource Manager
ms.topic: quickstart
ms.date: 06/23/2020
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 17157e05e4ad123ba2bbdffa199c111df9f8912e
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/18/2021
ms.locfileid: "100653027"
---
# <a name="create-a-namespace-with-event-hub-and-enable-capture-using-a-template"></a>Tworzenie przestrzeni nazw w centrum zdarzeń i włączanie przechwytywania przy użyciu szablonu

W tym artykule przedstawiono sposób używania szablonu usługi Azure Resource Manager umożliwiającego utworzenie przestrzeni nazw usługi [Event Hubs](./event-hubs-about.md) z jednym wystąpieniem centrum zdarzeń oraz włączenie [funkcji przechwytywania](event-hubs-capture-overview.md) w tym centrum zdarzeń. W tym artykule opisano, jak wskazać wdrażane zasoby oraz jak podać parametry realizacji wdrożenia. Można użyć tego szablonu na potrzeby własnych wdrożeń lub dostosować go do konkretnych potrzeb.

W tym artykule pokazano też, jak przechwytywać zdarzenia w obiektach Azure Storage Blob lub w magazynie Azure Data Lake Store zależnie od wybranej lokalizacji docelowej.

Aby uzyskać więcej informacji na temat tworzenia szablonów, zobacz [Tworzenie szablonów usługi Azure Resource Manager][Authoring Azure Resource Manager templates]. Aby zapoznać się z właściwościami i składnią języka JSON, których należy użyć w szablonie, zobacz [Typy zasobów Microsoft.EventHub](/azure/templates/microsoft.eventhub/allversions).

Aby uzyskać więcej informacji na temat praktycznych rozwiązań i wzorców dotyczących konwencji nazewnictwa zasobów platformy Azure, zobacz [Azure Resources Naming Conventions (Konwencje nazewnictwa zasobów platformy Azure)][Azure Resources naming conventions].

Aby uzyskać pełne szablony, kliknij poniższe linki witryny GitHub:

- [Centrum zdarzeń i włączanie funkcji przechwytywania w szablonie magazynu][Event Hub and enable Capture to Storage template] 
- [Centrum zdarzeń i włączanie funkcji przechwytywania w szablonie usługi Azure Data Lake Store][Event Hub and enable Capture to Azure Data Lake Store template]

> [!NOTE]
> Aby sprawdzić najnowsze szablony, odwiedź galerię [Szablony szybkiego startu platformy Azure][Azure Quickstart Templates] i wyszukaj hasło Event Hubs.
> 
> 

## <a name="what-will-you-deploy"></a>Co chcesz wdrożyć?

Ten szablon umożliwia wdrożenie przestrzeni nazw usługi Event Hubs z centrum zdarzeń oraz włączenie [funkcji przechwytywania usługi Event Hubs](event-hubs-capture-overview.md). Funkcja przechwytywania usługi Event Hubs pozwala automatycznie dostarczać strumień danych usługi Event Hubs do usługi Azure Blob Storage lub Azure Data Lake Store w wyznaczonym okresie lub przy określonym interwale rozmiaru. Kliknij poniższy przycisk, aby włączyć funkcję przechwytywania usługi Event Hubs w usłudze Azure Storage:

[![Wdrażanie na platformie Azure](./media/event-hubs-resource-manager-namespace-event-hub/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-eventhubs-create-namespace-and-enable-capture%2Fazuredeploy.json)

Kliknij poniższy przycisk, aby włączyć funkcję przechwytywania usługi Event Hubs w usłudze Azure Data Lake Store:

[![Wdrażanie na platformie Azure](./media/event-hubs-resource-manager-namespace-event-hub/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-eventhubs-create-namespace-and-enable-capture-for-adls%2Fazuredeploy.json)

## <a name="parameters"></a>Parametry

Przy użyciu usługi Azure Resource Manager można zdefiniować parametry dla wartości, które mają zostać uwzględnione podczas wdrażania szablonu. Szablon zawiera sekcję o nazwie `Parameters` obejmującą wszystkie wartości parametrów. Parametr powinien obejmować wartości, które różnią się w zależności od wdrażanego projektu lub środowiska, w którym odbywa się wdrożenie. Nie należy definiować parametrów dla wartości, które pozostają niezmienione. Każda wartość parametru używana w szablonie definiuje wdrażane zasoby.

Szablon zawiera definicje następujących parametrów.

### <a name="eventhubnamespacename"></a>eventHubNamespaceName

Nazwa tworzonej przestrzeni nazw usługi Event Hubs.

```json
"eventHubNamespaceName":{  
     "type":"string",
     "metadata":{  
         "description":"Name of the EventHub namespace"
      }
}
```

### <a name="eventhubname"></a>eventHubName

Nazwa centrum zdarzeń utworzonego w przestrzeni nazw usługi Event Hubs.

```json
"eventHubName":{  
    "type":"string",
    "metadata":{  
        "description":"Name of the event hub"
    }
}
```

### <a name="messageretentionindays"></a>messageRetentionInDays

Liczba dni przechowywania komunikatów w centrum zdarzeń. 

```json
"messageRetentionInDays":{
    "type":"int",
    "defaultValue": 1,
    "minValue":"1",
    "maxValue":"7",
    "metadata":{
       "description":"How long to retain the data in event hub"
     }
 }
```

### <a name="partitioncount"></a>partitionCount

Liczba partycji w utworzonym centrum zdarzeń.

```json
"partitionCount":{
    "type":"int",
    "defaultValue":2,
    "minValue":2,
    "maxValue":32,
    "metadata":{
        "description":"Number of partitions chosen"
    }
 }
```

### <a name="captureenabled"></a>captureEnabled

Włączanie funkcji przechwytywania w centrum zdarzeń.

```json
"captureEnabled":{
    "type":"string",
    "defaultValue":"true",
    "allowedValues": [
    "false",
    "true"],
    "metadata":{
        "description":"Enable or disable the Capture for your event hub"
    }
 }
```
### <a name="captureencodingformat"></a>captureEncodingFormat

Format kodowania na potrzeby serializowania danych zdarzeń.

```json
"captureEncodingFormat":{
    "type":"string",
    "defaultValue":"Avro",
    "allowedValues":[
    "Avro"],
    "metadata":{
        "description":"The encoding format in which Capture serializes the EventData"
    }
}
```

### <a name="capturetime"></a>captureTime

Przedział czasu, w którym funkcja przechwytywania usługi Event Hubs pobiera dane.

```json
"captureTime":{
    "type":"int",
    "defaultValue":300,
    "minValue":60,
    "maxValue":900,
    "metadata":{
         "description":"The time window in seconds for the capture"
    }
}
```

### <a name="capturesize"></a>captureSize
Przedział rozmiaru, w którym funkcja przechwytywania pobiera dane.

```json
"captureSize":{
    "type":"int",
    "defaultValue":314572800,
    "minValue":10485760,
    "maxValue":524288000,
    "metadata":{
        "description":"The size window in bytes for capture"
    }
}
```

### <a name="capturenameformat"></a>captureNameFormat

Format nazwy używany przez funkcję przechwytywania usługi Event Hubs podczas zapisywania plików systemu Avro. Format nazwy funkcji przechwytywania musi zawierać pola `{Namespace}`, `{EventHub}`, `{PartitionId}`, `{Year}`, `{Month}`, `{Day}`, `{Hour}`, `{Minute}` i `{Second}`. Mogą one być uporządkowane w dowolnej kolejności z ogranicznikami lub bez nich.
 
```json
"captureNameFormat": {
      "type": "string",
      "defaultValue": "{Namespace}/{EventHub}/{PartitionId}/{Year}/{Month}/{Day}/{Hour}/{Minute}/{Second}",
      "metadata": {
        "description": "A Capture Name Format must contain {Namespace}, {EventHub}, {PartitionId}, {Year}, {Month}, {Day}, {Hour}, {Minute} and {Second} fields. These can be arranged in any order with or without delimeters. E.g.  Prod_{EventHub}/{Namespace}\\{PartitionId}_{Year}_{Month}/{Day}/{Hour}/{Minute}/{Second}"
      }
    }
  
```

### <a name="apiversion"></a>apiVersion

Wersja interfejsu API szablonu.

```json
 "apiVersion":{  
    "type":"string",
    "defaultValue":"2017-04-01",
    "metadata":{  
        "description":"ApiVersion used by the template"
    }
 }
```

Poniżej przedstawiono parametry, których należy użyć w przypadku wybrania usługi Azure Storage jako lokalizacji docelowej.

### <a name="destinationstorageaccountresourceid"></a>destinationStorageAccountResourceId

Przechwytywanie danych na odpowiednim koncie usługi Storage wymaga identyfikatora zasobu konta usługi Azure Storage.

```json
 "destinationStorageAccountResourceId":{
    "type":"string",
    "metadata":{
        "description":"Your existing Storage account resource ID where you want the blobs be captured"
    }
 }
```

### <a name="blobcontainername"></a>blobContainerName

Kontener obiektów blob, w którym będą przechwytywane dane zdarzeń.

```json
 "blobContainerName":{
    "type":"string",
    "metadata":{
        "description":"Your existing storage container in which you want the blobs captured"
    }
}
```

Jeśli wybierzesz pozycję Azure Data Lake Store Gen 1 jako miejsce docelowe, użyj następujących parametrów. Należy ustawić uprawnienia w ścieżce usługi Data Lake Store, w której ma zostać przechwycone zdarzenie. Aby ustawić uprawnienia, zobacz [przechwytywanie danych do Azure Data Lake Storage generacji 1](event-hubs-capture-enable-through-portal.md#capture-data-to-azure-data-lake-storage-gen-1).

### <a name="subscriptionid"></a>subscriptionId

Identyfikator subskrypcji dla przestrzeni nazw usługi Event Hubs i usługi Azure Data Lake Store. Oba te zasoby muszą korzystać z tego samego identyfikatora subskrypcji.

```json
"subscriptionId": {
    "type": "string",
    "metadata": {
        "description": "Subscription ID of both Azure Data Lake Store and Event Hubs namespace"
     }
 }
```

### <a name="datalakeaccountname"></a>dataLakeAccountName

Nazwa usługi Azure Data Lake Store na potrzeby przechwytywanych zdarzeń.

```json
"dataLakeAccountName": {
    "type": "string",
    "metadata": {
        "description": "Azure Data Lake Store name"
    }
}
```

### <a name="datalakefolderpath"></a>dataLakeFolderPath

Ścieżka folderu docelowego dla przechwytywanych zdarzeń. Jest to folder w usłudze Data Lake Store, do którego wypychane będą zdarzenia podczas operacji przechwytywania. Aby uzyskać informacje dotyczące ustawiania uprawnień dla tego folderu, zobacz [Use Azure Data Lake Store to capture data from Event Hubs](../data-lake-store/data-lake-store-archive-eventhub-capture.md) (Przechwytywanie danych z usługi Event Hubs przy użyciu usługi Azure Data Lake Store).

```json
"dataLakeFolderPath": {
    "type": "string",
    "metadata": {
        "description": "Destination capture folder path"
    }
}
```

## <a name="resources-to-deploy-for-azure-storage-as-destination-to-captured-events"></a>Zasoby do wdrożenia w usłudze Azure Storage jako lokalizacja docelowa przechwytywanych zdarzeń

Tworzy przestrzeń nazw typu **EventHub** z jednym centrum zdarzeń oraz włącza funkcję przechwytywania w usłudze Azure Blob Storage.

```json
"resources":[  
      {  
         "apiVersion":"[variables('ehVersion')]",
         "name":"[parameters('eventHubNamespaceName')]",
         "type":"Microsoft.EventHub/Namespaces",
         "location":"[variables('location')]",
         "sku":{  
            "name":"Standard",
            "tier":"Standard"
         },
         "resources": [
    {
      "apiVersion": "2017-04-01",
      "name": "[parameters('eventHubNamespaceName')]",
      "type": "Microsoft.EventHub/Namespaces",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard"
      },
      "properties": {
        "isAutoInflateEnabled": "true",
        "maximumThroughputUnits": "7"
      },
      "resources": [
        {
          "apiVersion": "2017-04-01",
          "name": "[parameters('eventHubName')]",
          "type": "EventHubs",
          "dependsOn": [
            "[concat('Microsoft.EventHub/namespaces/', parameters('eventHubNamespaceName'))]"
          ],
          "properties": {
            "messageRetentionInDays": "[parameters('messageRetentionInDays')]",
            "partitionCount": "[parameters('partitionCount')]",
            "captureDescription": {
              "enabled": "true",
              "skipEmptyArchives": false,
              "encoding": "[parameters('captureEncodingFormat')]",
              "intervalInSeconds": "[parameters('captureTime')]",
              "sizeLimitInBytes": "[parameters('captureSize')]",
              "destination": {
                "name": "EventHubArchive.AzureBlockBlob",
                "properties": {
                  "storageAccountResourceId": "[parameters('destinationStorageAccountResourceId')]",
                  "blobContainer": "[parameters('blobContainerName')]",
                  "archiveNameFormat": "[parameters('captureNameFormat')]"
                }
              }
            }
          }

        }
      ]
    }
  ]
```

## <a name="resources-to-deploy-for-azure-data-lake-store-as-destination"></a>Zasoby do wdrożenia w usłudze Azure Data Lake Store jako lokalizacja docelowa

Tworzy przestrzeń nazw typu **EventHub** z jednym centrum zdarzeń oraz włącza funkcję przechwytywania w usłudze Azure Data Lake Store.

```json
 "resources": [
        {
            "apiVersion": "2017-04-01",
            "name": "[parameters('namespaceName')]",
            "type": "Microsoft.EventHub/Namespaces",
            "location": "[variables('location')]",
            "sku": {
                "name": "Standard",
                "tier": "Standard"
            },
            "resources": [
                {
                    "apiVersion": "2017-04-01",
                    "name": "[parameters('eventHubName')]",
                    "type": "EventHubs",
                    "dependsOn": [
                        "[concat('Microsoft.EventHub/namespaces/', parameters('namespaceName'))]"
                    ],
                    "properties": {
                        "path": "[parameters('eventHubName')]",
                        "captureDescription": {
                            "enabled": "true",
                            "skipEmptyArchives": false,
                            "encoding": "[parameters('archiveEncodingFormat')]",
                            "intervalInSeconds": "[parameters('captureTime')]",
                            "sizeLimitInBytes": "[parameters('captureSize')]",
                            "destination": {
                                "name": "EventHubArchive.AzureDataLake",
                                "properties": {
                                    "DataLakeSubscriptionId": "[parameters('subscriptionId')]",
                                    "DataLakeAccountName": "[parameters('dataLakeAccountName')]",
                                    "DataLakeFolderPath": "[parameters('dataLakeFolderPath')]",
                                    "ArchiveNameFormat": "[parameters('captureNameFormat')]"
                                }
                            }
                        }
                    }
                }
            ]
        }
    ]
```

> [!NOTE]
> Emitowanie pustych plików przy braku zdarzeń podczas okna przechwytywania można włączyć lub wyłączyć, używając właściwości **skipEmptyArchives**. 

## <a name="commands-to-run-deployment"></a>Polecenia umożliwiające uruchomienie wdrożenia

[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Wdróż szablon, aby włączyć funkcję przechwytywania usługi Event Hubs w usłudze Azure Storage:
 
```powershell
New-AzResourceGroupDeployment -ResourceGroupName \<resource-group-name\> -TemplateFile https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-eventhubs-create-namespace-and-enable-capture/azuredeploy.json
```

Wdróż szablon, aby włączyć funkcję przechwytywania usługi Event Hubs w usłudze Azure Data Lake Store:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName \<resource-group-name\> -TemplateFile https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-eventhubs-create-namespace-and-enable-capture-for-adls/azuredeploy.json
```

## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Usługa Azure Blob Storage jako lokalizacja docelowa:

```azurecli
az deployment group create \<my-resource-group\> \<my-deployment-name\> --template-uri [https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-eventhubs-create-namespace-and-enable-capture/azuredeploy.json][]
```

Usługa Azure Data Lake Store jako lokalizacja docelowa:

```azurecli
az deployment group create \<my-resource-group\> \<my-deployment-name\> --template-uri [https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-eventhubs-create-namespace-and-enable-capture-for-adls/azuredeploy.json][]
```

## <a name="next-steps"></a>Następne kroki

Funkcję przechwytywania usługi Event Hubs można również skonfigurować za pośrednictwem witryny [Azure Portal](https://portal.azure.com). Aby uzyskać więcej informacji, zobacz [Enable Event Hubs Capture using the Azure portal (Włączanie funkcji przechwytywania usługi Event Hubs przy użyciu witryny Azure Portal)](event-hubs-capture-enable-through-portal.md).

Następujące linki pozwalają dowiedzieć się więcej na temat usługi Event Hubs:

* [Przegląd usługi Event Hubs](./event-hubs-about.md)
* [Tworzenie centrum zdarzeń](event-hubs-create.md)
* [Event Hubs — często zadawane pytania](event-hubs-faq.md)

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/templates/template-syntax.md
[Azure Quickstart Templates]:  https://azure.microsoft.com/documentation/templates/?term=event+hubs
[Azure Resources naming conventions]: /azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging
[Event hub and enable Capture to Storage template]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-eventhubs-create-namespace-and-enable-capture
[Event hub and enable Capture to Azure Data Lake Store template]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-eventhubs-create-namespace-and-enable-capture-for-adls
