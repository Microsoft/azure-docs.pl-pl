---
title: Szablony Menedżer zasobów dla Azure Cosmos DB interfejsu API dla MongoDB
description: Użyj szablonów Azure Resource Manager, aby utworzyć i skonfigurować interfejs API Azure Cosmos DB dla MongoDB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/27/2020
ms.author: mjbrown
ms.openlocfilehash: e312b5d8b6052dafa4855afa035429ef06608f1b
ms.sourcegitcommit: 67bddb15f90fb7e845ca739d16ad568cbc368c06
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82200789"
---
# <a name="manage-azure-cosmos-db-mongodb-api-resources-using-azure-resource-manager-templates"></a>Zarządzanie zasobami interfejsu API usługi Azure Cosmos DB MongoDB przy użyciu szablonów Azure Resource Manager

W tym artykule opisano sposób wykonywania różnych operacji w celu zautomatyzowania zarządzania kontami Azure Cosmos DB, bazami danych i kontenerami przy użyciu szablonów Azure Resource Manager. W tym artykule przedstawiono przykłady Azure Cosmos DB API for MongoDB, aby znaleźć przykłady dla innych kont typu interfejsu API, zobacz: Używanie szablonów Azure Resource Manager z interfejsem API Azure Cosmos DB dla [Cassandra](manage-cassandra-with-resource-manager.md), [Gremlin](manage-gremlin-with-resource-manager.md), [SQL](manage-sql-with-resource-manager.md), artykułów [tabeli](manage-table-with-resource-manager.md) .

## <a name="create-azure-cosmos-db-api-for-mongodb-account-database-and-collection"></a>Tworzenie Azure Cosmos DB interfejsu API dla konta MongoDB, bazy danych i kolekcji<a id="create-resource"></a>

Tworzenie Azure Cosmos DB zasobów przy użyciu szablonu Azure Resource Manager. Ten szablon spowoduje utworzenie konta usługi Azure Cosmos dla interfejsu API MongoDB z dwiema kolekcjami, które współdzielą przepływność na 400 RU/s na poziomie bazy danych. Skopiuj szablon i Wdróż, jak pokazano poniżej, lub odwiedź [galerię szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/101-cosmosdb-mongodb/) i Wdróż ją z Azure Portal. Możesz również pobrać szablon na komputer lokalny lub utworzyć nowy szablon i określić ścieżkę lokalną za pomocą `--template-file` parametru.

> [!NOTE]
> Nazwy kont muszą zawierać małe litery i 44 lub mniej znaków.
> Aby zaktualizować RU/s, ponownie Wdróż szablon ze zaktualizowanymi wartościami właściwości przepływności.

```json
{
"$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
"contentVersion": "1.0.0.0",
"parameters": {
   "accountName": {
      "type": "string",
      "defaultValue": "",
      "metadata": {
         "description": "Cosmos DB account name"
      }
   },
   "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
         "description": "Location for the Cosmos DB account."
      }
   },
   "primaryRegion":{
      "type":"string",
      "metadata": {
         "description": "The primary replica region for the Cosmos DB account."
      }
   },
   "secondaryRegion":{
      "type":"string",
      "metadata": {
        "description": "The secondary replica region for the Cosmos DB account."
     }
   },
   "defaultConsistencyLevel": {
      "type": "string",
      "defaultValue": "Session",
      "allowedValues": [ "Eventual", "ConsistentPrefix", "Session", "BoundedStaleness", "Strong" ],
      "metadata": {
         "description": "The default consistency level of the Cosmos DB account."
      }
   },
   "serverVersion": {
      "defaultValue": "3.6",
      "allowedValues": [
         "3.2",
         "3.6"
      ],
      "type": "String",
      "metadata": {
         "description": "Specifies the MongoDB server version to use."
      }
   },
   "maxStalenessPrefix": {
      "type": "int",
      "defaultValue": 100000,
      "minValue": 10,
      "maxValue": 1000000,
      "metadata": {
         "description": "Max stale requests. Required for BoundedStaleness. Valid ranges, Single Region: 10 to 1000000. Multi Region: 100000 to 1000000."
      }
   },
   "maxIntervalInSeconds": {
      "type": "int",
      "defaultValue": 300,
      "minValue": 5,
      "maxValue": 86400,
      "metadata": {
         "description": "Max lag time (seconds). Required for BoundedStaleness. Valid ranges, Single Region: 5 to 84600. Multi Region: 300 to 86400."
      }
   },
   "databaseName": {
      "type": "string",
      "defaultValue": "Database1",
      "metadata": {
         "description": "The name for the Mongo DB database"
      }
   },
   "throughput": {
      "type": "int",
      "defaultValue": 400,
      "minValue": 400,
      "maxValue": 1000000,
      "metadata": {
         "description": "The shared throughput for the Mongo DB database"
      }
   },
   "collection1Name": {
      "type": "string",
      "defaultValue": "Collection1",
      "metadata": {
         "description": "The name for the first Mongo DB collection"
      }
   },
   "collection2Name": {
      "type": "string",
      "defaultValue": "Collection2",
      "metadata": {
         "description": "The name for the second Mongo DB collection"
      }
   }
},
"variables": {
   "accountName": "[toLower(parameters('accountName'))]",
   "consistencyPolicy": {
      "Eventual": {
         "defaultConsistencyLevel": "Eventual"
      },
      "ConsistentPrefix": {
         "defaultConsistencyLevel": "ConsistentPrefix"
      },
      "Session": {
         "defaultConsistencyLevel": "Session"
      },
      "BoundedStaleness": {
         "defaultConsistencyLevel": "BoundedStaleness",
         "maxStalenessPrefix": "[parameters('maxStalenessPrefix')]",
         "maxIntervalInSeconds": "[parameters('maxIntervalInSeconds')]"
      },
      "Strong": {
         "defaultConsistencyLevel": "Strong"
      }
   },
   "locations":
   [
      {
         "locationName": "[parameters('primaryRegion')]",
         "failoverPriority": 0,
         "isZoneRedundant": false
      },
      {
         "locationName": "[parameters('secondaryRegion')]",
         "failoverPriority": 1,
         "isZoneRedundant": false
      }
   ]
},
"resources":
[
   {
      "type": "Microsoft.DocumentDB/databaseAccounts",
      "name": "[variables('accountName')]",
      "apiVersion": "2020-03-01",
      "location": "[parameters('location')]",
      "kind": "MongoDB",
      "properties": {
         "consistencyPolicy": "[variables('consistencyPolicy')[parameters('defaultConsistencyLevel')]]",
         "locations": "[variables('locations')]",
         "databaseAccountOfferType": "Standard",
         "apiProperties": {
            "serverVersion": "[parameters('serverVersion')]"
         }
      }
   },
   {
      "type": "Microsoft.DocumentDB/databaseAccounts/mongodbDatabases",
      "name": "[concat(variables('accountName'), '/', parameters('databaseName'))]",
      "apiVersion": "2020-03-01",
      "dependsOn": [ "[resourceId('Microsoft.DocumentDB/databaseAccounts/', variables('accountName'))]" ],
      "properties":{
         "resource":{
            "id": "[parameters('databaseName')]"
         },
         "options": { "throughput": "[parameters('throughput')]" }
      }
   },
   {
      "type": "Microsoft.DocumentDb/databaseAccounts/mongodbDatabases/collections",
      "name": "[concat(variables('accountName'), '/', parameters('databaseName'), '/', parameters('collection1Name'))]",
      "apiVersion": "2020-03-01",
      "dependsOn": [ "[resourceId('Microsoft.DocumentDB/databaseAccounts/mongodbDatabases', variables('accountName'), parameters('databaseName'))]" ],
      "properties":
      {
         "resource":{
            "id":  "[parameters('collection1Name')]",
            "shardKey": { "user_id": "Hash" },
            "indexes": [
               {
                  "key": { "keys":["user_id", "user_address"] },
                  "options": { "unique": "true" }
               },
               {
                  "key": { "keys":["_ts"] },
                  "options": { "expireAfterSeconds": "2629746" }
               }
            ],
            "options": {
               "If-Match": "<ETag>"
            }
         }
      }
   },
   {
      "type": "Microsoft.DocumentDb/databaseAccounts/mongodbDatabases/collections",
      "name": "[concat(variables('accountName'), '/', parameters('databaseName'), '/', parameters('collection2Name'))]",
      "apiVersion": "2020-03-01",
      "dependsOn": [ "[resourceId('Microsoft.DocumentDB/databaseAccounts/mongodbDatabases', variables('accountName'),  parameters('databaseName'))]" ],
      "properties":
      {
         "resource":{
            "id":  "[parameters('collection2Name')]",
            "shardKey": { "company_id": "Hash" },
            "indexes": [
               {
                  "key": { "keys":["company_id", "company_address"] },
                  "options": { "unique": "true" }
               },
               {
                  "key": { "keys":["_ts"] },
                  "options": { "expireAfterSeconds": "2629746" }
               }
            ],
            "options": {
               "If-Match": "<ETag>"
            }
         }
      }
   }
]
}
```

### <a name="deploy-via-the-azure-cli"></a>Wdrażanie za pomocą interfejsu wiersza polecenia platformy Azure

Aby wdrożyć szablon Azure Resource Manager przy użyciu interfejsu wiersza polecenia platformy Azure, **Skopiuj** skrypt i wybierz opcję **Wypróbuj** , aby otworzyć Azure Cloud Shell. Aby wkleić skrypt, kliknij prawym przyciskiem myszy powłokę, a następnie wybierz polecenie **Wklej**:

```azurecli-interactive

read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the location (i.e. westus2): ' location
read -p 'Enter the account name: ' accountName
read -p 'Enter the primary region (i.e. westus2): ' primaryRegion
read -p 'Enter the secondary region (i.e. eastus2): ' secondaryRegion
read -p 'Enter the database name: ' databaseName
read -p 'Enter the database throughput: ' throughput
read -p 'Enter the first collection name: ' collection1Name
read -p 'Enter the second collection name: ' collection2Name

az group create --name $resourceGroupName --location $location
az group deployment create --resource-group $resourceGroupName \
  --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-mongodb/azuredeploy.json \
  --parameters accountName=$accountName primaryRegion=$primaryRegion secondaryRegion=$secondaryRegion \
  databaseName=$databaseName throughput=$throughput collection1Name=$collection1Name collection2Name=$collection2Name

az cosmosdb show --resource-group $resourceGroupName --name accountName --output tsv
```

`az cosmosdb show` Polecenie wyświetla nowo utworzone konto usługi Azure Cosmos po jego aprowizacji. Jeśli zdecydujesz się użyć lokalnie zainstalowanej wersji interfejsu wiersza polecenia platformy Azure zamiast korzystania z Cloud Shell, zobacz artykuł [interfejsu wiersza polecenia platformy Azure](/cli/azure/) .

## <a name="next-steps"></a>Następne kroki

Oto kilka dodatkowych zasobów:

- [Dokumentacja Azure Resource Manager](/azure/azure-resource-manager/)
- [Schemat dostawcy zasobów Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions)
- [Azure Cosmos DB Szablony szybkiego startu](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
- [Rozwiązywanie typowych błędów wdrażania Azure Resource Manager](../azure-resource-manager/templates/common-deployment-errors.md)
