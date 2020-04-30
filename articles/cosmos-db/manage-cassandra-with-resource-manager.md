---
title: Szablony Menedżer zasobów dla Azure Cosmos DB interfejs API Cassandra
description: Utwórz i skonfiguruj Azure Cosmos DB interfejs API Cassandra za pomocą szablonów Azure Resource Manager.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/27/2020
ms.author: mjbrown
ms.openlocfilehash: ff75597bece386635195a84572a9f07b04d9c60f
ms.sourcegitcommit: 67bddb15f90fb7e845ca739d16ad568cbc368c06
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82200817"
---
# <a name="manage-azure-cosmos-db-cassandra-api-resources-using-azure-resource-manager-templates"></a>Zarządzanie zasobami interfejs API Cassandra Azure Cosmos DB przy użyciu szablonów Azure Resource Manager

W tym artykule opisano sposób wykonywania różnych operacji w celu zautomatyzowania zarządzania kontami Azure Cosmos DB, bazami danych i kontenerami przy użyciu szablonów Azure Resource Manager. W tym artykule przedstawiono przykłady tylko dla kont interfejs API Cassandra, aby znaleźć przykłady dla innych kont typu interfejsu API, zobacz: Używanie szablonów Azure Resource Manager z interfejsem API Azure Cosmos DB dla [SQL](manage-sql-with-resource-manager.md), [Gremlin](manage-gremlin-with-resource-manager.md), [MongoDB](manage-mongodb-with-resource-manager.md), artykuły [tabeli](manage-table-with-resource-manager.md) .

## <a name="create-azure-cosmos-account-keyspace-and-table"></a>Tworzenie konta usługi Azure Cosmos, przestrzeni kluczy i tabeli<a id="create-resource"></a>

Tworzenie Azure Cosmos DB zasobów przy użyciu szablonu Azure Resource Manager. Ten szablon spowoduje utworzenie konta usługi Azure Cosmos na potrzeby interfejs API Cassandra z dwiema tabelami, które współużytkują przepływność na poziomie między jednostkami 400 RU/s. Skopiuj szablon i Wdróż, jak pokazano poniżej, lub odwiedź [galerię szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/101-cosmosdb-cassandra/) i Wdróż ją z Azure Portal. Możesz również pobrać szablon na komputer lokalny lub utworzyć nowy szablon i określić ścieżkę lokalną za pomocą `--template-file` parametru.

> [!NOTE]
> Nazwy kont muszą zawierać małe litery i 44 lub mniej znaków.
> Aby zaktualizować RU/s, ponownie prześlij szablon ze zaktualizowanymi wartościami właściwości przepływności.

```json
{
"$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
"contentVersion": "1.0.0.0",
"parameters": {
    "accountName": {
        "type": "string",
        "defaultValue": "",
        "metadata": {
            "description": "Cosmos DB account name, max length 44 characters"
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
    "automaticFailover": {
        "type": "bool",
        "defaultValue": true,
        "allowedValues": [ true, false ],
        "metadata": {
            "description": "Enable automatic failover for regions"
        }
    },
    "keyspaceName": {
        "type": "string",
        "defaultValue": "Keyspace1",
        "metadata": {
            "description": "The name for the Cassandra Keyspace"
        }
    },
    "tableName": {
        "type": "string",
        "defaultValue": "Table1",
        "metadata": {
            "description": "The name for the Cassandra table"
        }
    },
    "throughput": {
        "type": "int",
        "defaultValue": 400,
        "minValue": 400,
        "maxValue": 1000000,
        "metadata": {
            "description": "The throughput for the Cassandra table"
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
        "kind": "GlobalDocumentDB",
        "properties": {
            "capabilities": [{ "name": "EnableCassandra" }],
            "consistencyPolicy": "[variables('consistencyPolicy')[parameters('defaultConsistencyLevel')]]",
            "locations": "[variables('locations')]",
            "databaseAccountOfferType": "Standard",
            "enableAutomaticFailover": "[parameters('automaticFailover')]"
        }
    },
    {
        "type": "Microsoft.DocumentDB/databaseAccounts/cassandraKeyspaces",
        "name": "[concat(variables('accountName'), '/', parameters('keyspaceName'))]",
        "apiVersion": "2020-03-01",
        "dependsOn": [ "[resourceId('Microsoft.DocumentDB/databaseAccounts/', variables('accountName'))]" ],
        "properties":{
            "resource":{
                "id": "[parameters('keyspaceName')]"
            }
        }
    },
    {
        "type": "Microsoft.DocumentDb/databaseAccounts/cassandraKeyspaces/tables",
        "name": "[concat(variables('accountName'), '/', parameters('keyspaceName'), '/', parameters('tableName'))]",
        "apiVersion": "2020-03-01",
        "dependsOn": [ "[resourceId('Microsoft.DocumentDB/databaseAccounts/cassandraKeyspaces', variables('accountName'), parameters('keyspaceName'))]" ],
        "properties":
        {
            "resource":{
                "id":  "[parameters('tableName')]",
                "schema": {
                    "columns": [
                        { "name": "loadid", "type": "uuid" },
                        { "name": "machine", "type": "uuid" },
                        { "name": "cpu", "type": "int" },
                        { "name": "mtime", "type": "int" },
                        { "name": "load", "type": "float" }
                    ],
                    "partitionKeys": [
                        { "name": "machine" },
                        { "name": "cpu" },
                        { "name": "mtime" }
                    ],
                    "clusterKeys": [
                        { "name": "loadid", "orderBy": "asc" }
                    ]
                },
                "options": { "throughput": "[parameters('throughput')]" }
            }
        }
    }
]
}
```

## <a name="deploy-with-the-azure-cli"></a>Wdrażanie przy użyciu interfejsu wiersza polecenia platformy Azure

Aby wdrożyć szablon Azure Resource Manager przy użyciu interfejsu wiersza polecenia platformy Azure, **Skopiuj** skrypt i wybierz opcję **Wypróbuj** , aby otworzyć Azure Cloud Shell. Aby wkleić skrypt, kliknij prawym przyciskiem myszy powłokę, a następnie wybierz polecenie **Wklej**:

```azurecli-interactive

read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the location (i.e. westus2): ' location
read -p 'Enter the account name: ' accountName
read -p 'Enter the primary region (i.e. westus2): ' primaryRegion
read -p 'Enter the secondary region (i.e. eastus2): ' secondaryRegion
read -p 'Enter the keyspace name: ' keyspaceName
read -p 'Enter the table name: ' tableName
read -p 'Enter the throughput: ' throughput

az group create --name $resourceGroupName --location $location
az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-cassandra/azuredeploy.json \
   --parameters accountName=$accountName primaryRegion=$primaryRegion secondaryRegion=$secondaryRegion keyspaceName=$keyspaceName \
   tableName=$tableName throughput=$throughput

az cosmosdb show --resource-group $resourceGroupName --name accountName --output tsv
```

`az cosmosdb show` Polecenie wyświetla nowo utworzone konto usługi Azure Cosmos po jego aprowizacji. Jeśli zdecydujesz się użyć lokalnie zainstalowanej wersji interfejsu wiersza polecenia platformy Azure zamiast korzystania z Cloud Shell, zobacz artykuł [interfejsu wiersza polecenia platformy Azure](/cli/azure/) .

## <a name="next-steps"></a>Następne kroki

Oto kilka dodatkowych zasobów:

- [Dokumentacja Azure Resource Manager](/azure/azure-resource-manager/)
- [Schemat dostawcy zasobów Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions)
- [Azure Cosmos DB Szablony szybkiego startu](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
- [Rozwiązywanie typowych błędów wdrażania Azure Resource Manager](../azure-resource-manager/templates/common-deployment-errors.md)
