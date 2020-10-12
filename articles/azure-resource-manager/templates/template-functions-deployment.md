---
title: Funkcje szablonu — wdrożenie
description: Opisuje funkcje, które mają być używane w szablonie Azure Resource Manager do pobierania informacji o wdrożeniu.
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: e8240c05cba82d5563c4b327ecbc65a9c358720f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "84677818"
---
# <a name="deployment-functions-for-arm-templates"></a>Funkcje wdrażania dla szablonów ARM

Menedżer zasobów udostępnia następujące funkcje do uzyskiwania wartości związanych z bieżącym wdrożeniem szablonu Azure Resource Manager (ARM):

* [mieszczeniu](#deployment)
* [środowisko](#environment)
* [wejściowe](#parameters)
* [modyfikacj](#variables)

Aby uzyskać wartości z zasobów, grup zasobów lub subskrypcji, zobacz temat [funkcje zasobów](template-functions-resource.md).

## <a name="deployment"></a>wdrożenie

`deployment()`

Zwraca informacje o bieżącej operacji wdrażania.

### <a name="return-value"></a>Wartość zwracana

Ta funkcja zwraca obiekt, który jest przesyłany podczas wdrażania. Właściwości w zwracanym obiekcie różnią się w zależności od tego, czy są:

* wdrożenie szablonu, który jest plikiem lokalnym lub wdrożenie szablonu, który jest plikiem zdalnym dostępnym za pomocą identyfikatora URI.
* Wdrażanie w grupie zasobów lub wdrażanie jej w jednym z pozostałych zakresów ([subskrypcja platformy Azure](deploy-to-subscription.md), [Grupa zarządzania](deploy-to-management-group.md)lub [dzierżawca](deploy-to-tenant.md)).

Podczas wdrażania szablonu lokalnego w grupie zasobów: funkcja zwraca następujący format:

```json
{
    "name": "",
    "properties": {
        "template": {
            "$schema": "",
            "contentVersion": "",
            "parameters": {},
            "variables": {},
            "resources": [
            ],
            "outputs": {}
        },
        "templateHash": "",
        "parameters": {},
        "mode": "",
        "provisioningState": ""
    }
}
```

Podczas wdrażania szablonu zdalnego w grupie zasobów: funkcja zwraca następujący format:

```json
{
    "name": "",
    "properties": {
        "templateLink": {
            "uri": ""
        },
        "template": {
            "$schema": "",
            "contentVersion": "",
            "parameters": {},
            "variables": {},
            "resources": [],
            "outputs": {}
        },
        "templateHash": "",
        "parameters": {},
        "mode": "",
        "provisioningState": ""
    }
}
```

Po wdrożeniu do subskrypcji platformy Azure, grupy zarządzania lub dzierżawy obiekt zwracany zawiera `location` Właściwość. Właściwość Location jest uwzględniana podczas wdrażania szablonu lokalnego lub zewnętrznego. Format to:

```json
{
    "name": "",
    "location": "",
    "properties": {
        "template": {
            "$schema": "",
            "contentVersion": "",
            "resources": [],
            "outputs": {}
        },
        "templateHash": "",
        "parameters": {},
        "mode": "",
        "provisioningState": ""
    }
}
```

### <a name="remarks"></a>Uwagi

Możesz użyć wdrożenia (), aby połączyć się z innym szablonem na podstawie identyfikatora URI szablonu nadrzędnego.

```json
"variables": {
    "sharedTemplateUrl": "[uri(deployment().properties.templateLink.uri, 'shared-resources.json')]"
}
```

Po ponownym wdrożeniu szablonu z historii wdrożenia w portalu szablon zostanie wdrożony jako plik lokalny. `templateLink`Właściwość nie jest zwracana w funkcji wdrożenia. Jeśli szablon polega na `templateLink` skonstruowaniu linku do innego szablonu, nie należy używać portalu do ponownego wdrożenia. Zamiast tego należy użyć poleceń użytych do pierwotnego wdrożenia szablonu.

### <a name="example"></a>Przykład

Poniższy [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/deployment.json) zwraca obiekt wdrożenia:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "deploymentOutput": {
            "value": "[deployment()]",
            "type" : "object"
        }
    }
}
```

Poprzedni przykład zwraca następujący obiekt:

```json
{
  "name": "deployment",
  "properties": {
    "template": {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "resources": [],
      "outputs": {
        "deploymentOutput": {
          "type": "Object",
          "value": "[deployment()]"
        }
      }
    },
    "templateHash": "13135986259522608210",
    "parameters": {},
    "mode": "Incremental",
    "provisioningState": "Accepted"
  }
}
```

## <a name="environment"></a>środowisko

`environment()`

Zwraca informacje o środowisku platformy Azure używanym do wdrożenia.

### <a name="return-value"></a>Wartość zwracana

Ta funkcja zwraca właściwości dla bieżącego środowiska platformy Azure. W poniższym przykładzie przedstawiono właściwości globalne platformy Azure. Suwerenne chmury mogą zwracać nieco inne właściwości.

```json
{
  "name": "",
  "gallery": "",
  "graph": "",
  "portal": "",
  "graphAudience": "",
  "activeDirectoryDataLake": "",
  "batch": "",
  "media": "",
  "sqlManagement": "",
  "vmImageAliasDoc": "",
  "resourceManager": "",
  "authentication": {
    "loginEndpoint": "",
    "audiences": [
      "",
      ""
    ],
    "tenant": "",
    "identityProvider": ""
  },
  "suffixes": {
    "acrLoginServer": "",
    "azureDatalakeAnalyticsCatalogAndJob": "",
    "azureDatalakeStoreFileSystem": "",
    "azureFrontDoorEndpointSuffix": "",
    "keyvaultDns": "",
    "sqlServerHostname": "",
    "storage": ""
  }
}
```

### <a name="example"></a>Przykład

Poniższy przykładowy szablon zwraca obiekt środowiska.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "environmentOutput": {
            "value": "[environment()]",
            "type" : "object"
        }
    }
}
```

Poprzedni przykład zwraca następujący obiekt po wdrożeniu na globalnym platformie Azure:

```json
{
  "name": "AzureCloud",
  "gallery": "https://gallery.azure.com/",
  "graph": "https://graph.windows.net/",
  "portal": "https://portal.azure.com",
  "graphAudience": "https://graph.windows.net/",
  "activeDirectoryDataLake": "https://datalake.azure.net/",
  "batch": "https://batch.core.windows.net/",
  "media": "https://rest.media.azure.net",
  "sqlManagement": "https://management.core.windows.net:8443/",
  "vmImageAliasDoc": "https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json",
  "resourceManager": "https://management.azure.com/",
  "authentication": {
    "loginEndpoint": "https://login.windows.net/",
    "audiences": [
      "https://management.core.windows.net/",
      "https://management.azure.com/"
    ],
    "tenant": "common",
    "identityProvider": "AAD"
  },
  "suffixes": {
    "acrLoginServer": ".azurecr.io",
    "azureDatalakeAnalyticsCatalogAndJob": "azuredatalakeanalytics.net",
    "azureDatalakeStoreFileSystem": "azuredatalakestore.net",
    "azureFrontDoorEndpointSuffix": "azurefd.net",
    "keyvaultDns": ".vault.azure.net",
    "sqlServerHostname": ".database.windows.net",
    "storage": "core.windows.net"
  }
}
```

## <a name="parameters"></a>parameters

`parameters(parameterName)`

Zwraca wartość parametru. Określona nazwa parametru musi być zdefiniowana w sekcji Parameters szablonu.

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
|:--- |:--- |:--- |:--- |
| parameterName |Tak |ciąg |Nazwa parametru do zwrócenia. |

### <a name="return-value"></a>Wartość zwracana

Wartość określonego parametru.

### <a name="remarks"></a>Uwagi

Zazwyczaj należy używać parametrów do ustawiania wartości zasobów. W poniższym przykładzie ustawiono nazwę witryny sieci Web do wartości parametru przesłanej podczas wdrażania.

```json
"parameters": {
  "siteName": {
      "type": "string"
  }
},
"resources": [
   {
      "apiVersion": "2016-08-01",
      "name": "[parameters('siteName')]",
      "type": "Microsoft.Web/Sites",
      ...
   }
]
```

### <a name="example"></a>Przykład

Poniższy [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/parameters.json) pokazuje uproszczone użycie funkcji Parameters.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "stringParameter": {
            "type" : "string",
            "defaultValue": "option 1"
        },
        "intParameter": {
            "type": "int",
            "defaultValue": 1
        },
        "objectParameter": {
            "type": "object",
            "defaultValue": {"one": "a", "two": "b"}
        },
        "arrayParameter": {
            "type": "array",
            "defaultValue": [1, 2, 3]
        },
        "crossParameter": {
            "type": "string",
            "defaultValue": "[parameters('stringParameter')]"
        }
    },
    "variables": {},
    "resources": [],
    "outputs": {
        "stringOutput": {
            "value": "[parameters('stringParameter')]",
            "type" : "string"
        },
        "intOutput": {
            "value": "[parameters('intParameter')]",
            "type" : "int"
        },
        "objectOutput": {
            "value": "[parameters('objectParameter')]",
            "type" : "object"
        },
        "arrayOutput": {
            "value": "[parameters('arrayParameter')]",
            "type" : "array"
        },
        "crossOutput": {
            "value": "[parameters('crossParameter')]",
            "type" : "string"
        }
    }
}
```

Dane wyjściowe z poprzedniego przykładu z wartościami domyślnymi są następujące:

| Nazwa | Typ | Wartość |
| ---- | ---- | ----- |
| stringOutput | Ciąg | Opcja 1 |
| intOutput | int | 1 |
| objectOutput | Obiekt | {"jeden": "a", "dwa": "b"} |
| arrayOutput | Tablica | [1, 2, 3] |
| crossOutput | Ciąg | Opcja 1 |

Aby uzyskać więcej informacji na temat używania parametrów, zobacz [Parametry w szablonie Azure Resource Manager](template-parameters.md).

## <a name="variables"></a>modyfikacj

`variables(variableName)`

Zwraca wartość zmiennej. Określona nazwa zmiennej musi być zdefiniowana w sekcji zmiennych szablonu.

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
|:--- |:--- |:--- |:--- |
| variableName |Tak |Ciąg |Nazwa zmiennej do zwrócenia. |

### <a name="return-value"></a>Wartość zwracana

Wartość określonej zmiennej.

### <a name="remarks"></a>Uwagi

Zwykle zmienne służą do uproszczenia szablonu przez konstruowanie złożonych wartości tylko raz. Poniższy przykład tworzy unikatową nazwę konta magazynu.

```json
"variables": {
    "storageName": "[concat('storage', uniqueString(resourceGroup().id))]"
},
"resources": [
    {
        "type": "Microsoft.Storage/storageAccounts",
        "name": "[variables('storageName')]",
        ...
    },
    {
        "type": "Microsoft.Compute/virtualMachines",
        "dependsOn": [
            "[variables('storageName')]"
        ],
        ...
    }
],
```

### <a name="example"></a>Przykład

Poniższy [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/variables.json) zwraca różne wartości zmiennych.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "variables": {
        "var1": "myVariable",
        "var2": [ 1,2,3,4 ],
        "var3": "[ variables('var1') ]",
        "var4": {
            "property1": "value1",
            "property2": "value2"
          }
    },
    "resources": [],
    "outputs": {
        "exampleOutput1": {
            "value": "[variables('var1')]",
            "type" : "string"
        },
        "exampleOutput2": {
            "value": "[variables('var2')]",
            "type" : "array"
        },
        "exampleOutput3": {
            "value": "[variables('var3')]",
            "type" : "string"
        },
        "exampleOutput4": {
            "value": "[variables('var4')]",
            "type" : "object"
        }
    }
}
```

Dane wyjściowe z poprzedniego przykładu z wartościami domyślnymi są następujące:

| Nazwa | Typ | Wartość |
| ---- | ---- | ----- |
| exampleOutput1 | Ciąg | NazwaMojejZmiennej |
| exampleOutput2 | Tablica | [1, 2, 3, 4] |
| exampleOutput3 | Ciąg | NazwaMojejZmiennej |
| exampleOutput4 |  Obiekt | {"Property1": "wartość1", "Property2": "wartość2"} |

Aby uzyskać więcej informacji o używaniu zmiennych, zobacz [zmienne w szablonie Azure Resource Manager](template-variables.md).

## <a name="next-steps"></a>Następne kroki

* Opis sekcji w szablonie Azure Resource Manager można znaleźć w temacie [Omówienie struktury i składni szablonów usługi ARM](template-syntax.md).
