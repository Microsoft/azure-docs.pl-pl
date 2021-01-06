---
title: Parametry w szablonach
description: Opisuje sposób definiowania parametrów w szablonie Azure Resource Manager (szablon ARM).
ms.topic: conceptual
ms.date: 11/24/2020
ms.openlocfilehash: 929d871bc0087d4fda585773b349dee4e0945c7c
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/06/2021
ms.locfileid: "97934717"
---
# <a name="parameters-in-arm-templates"></a>Parametry w szablonach ARM

W tym artykule opisano sposób definiowania i używania parametrów w szablonie Azure Resource Manager (szablon ARM). Dostarczając różne wartości parametrów, można ponownie użyć szablonu dla różnych środowisk.

Menedżer zasobów rozwiązuje wartości parametrów przed rozpoczęciem operacji wdrażania. W każdym przypadku, gdy parametr jest używany w szablonie, Menedżer zasobów zastępuje go rozpoznaną wartością.

Każdy parametr musi być ustawiony na jeden z [typów danych](template-syntax.md#data-types).

## <a name="define-parameter"></a>Zdefiniuj parametr

Poniższy przykład pokazuje prostą definicję parametru. Definiuje parametr o nazwie `storageSKU` . Parametr jest wartością ciągu i akceptuje tylko wartości, które są prawidłowe dla zamierzonego użycia. Parametr używa wartości domyślnej, jeśli podczas wdrażania nie podano żadnej wartości.

```json
"parameters": {
  "storageSKU": {
    "type": "string",
    "allowedValues": [
      "Standard_LRS",
      "Standard_ZRS",
      "Standard_GRS",
      "Standard_RAGRS",
      "Premium_LRS"
    ],
    "defaultValue": "Standard_LRS",
    "metadata": {
      "description": "The type of replication to use for the storage account."
    }
  }
}
```

## <a name="use-parameter"></a>Użyj parametru

W szablonie odwołujesz się do wartości parametru za pomocą funkcji [Parameters](template-functions-deployment.md#parameters) . W poniższym przykładzie wartość parametru służy do ustawiania jednostki SKU dla konta magazynu.

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "sku": {
      "name": "[parameters('storageSKU')]"
    },
    ...
  }
]
```

## <a name="template-functions"></a>Funkcje szablonów

Podczas określania wartości domyślnej dla parametru, można użyć większości funkcji szablonu. Możesz użyć innej wartości parametru do skompilowania wartości domyślnej. Poniższy szablon demonstruje użycie funkcji w wartości domyślnej. Jeśli nie podano nazwy dla lokacji, tworzona jest unikatowa wartość ciągu i dołącza ją do **lokacji**. Jeśli nie podano nazwy dla planu hosta, przyjmuje ona wartość dla lokacji i dołącza do **-Plan**.

```json
"parameters": {
  "siteName": {
    "type": "string",
    "defaultValue": "[concat('site', uniqueString(resourceGroup().id))]",
    "metadata": {
      "description": "The site name. To use the default value, do not specify a new value."
    }
  },
  "hostingPlanName": {
    "type": "string",
    "defaultValue": "[concat(parameters('siteName'),'-plan')]",
    "metadata": {
      "description": "The host name. To use the default value, do not specify a new value."
    }
  }
}
```

Nie można użyć funkcji [Reference](template-functions-resource.md#reference) ani żadnej z funkcji [list](template-functions-resource.md#list) w sekcji Parameters. Te funkcje uzyskują stan środowiska uruchomieniowego zasobu i nie można ich wykonać przed wdrożeniem, gdy parametry zostaną rozwiązane.

## <a name="objects-as-parameters"></a>Obiekty jako parametry

Można łatwiej organizować powiązane wartości, przekazując je w postaci obiektu. Takie podejście zmniejsza również liczbę parametrów w szablonie.

Poniższy przykład przedstawia parametr, który jest obiektem. Wartość domyślna zawiera oczekiwane właściwości obiektu.

```json
"parameters": {
  "VNetSettings": {
    "type": "object",
    "defaultValue": {
      "name": "VNet1",
      "location": "eastus",
      "addressPrefixes": [
        {
          "name": "firstPrefix",
          "addressPrefix": "10.0.0.0/22"
        }
      ],
      "subnets": [
        {
          "name": "firstSubnet",
          "addressPrefix": "10.0.0.0/24"
        },
        {
          "name": "secondSubnet",
          "addressPrefix": "10.0.1.0/24"
        }
      ]
    }
  }
},
```

Odwoływanie się do właściwości obiektu za pomocą operatora kropki.

```json
"resources": [
  {
    "type": "Microsoft.Network/virtualNetworks",
    "apiVersion": "2015-06-15",
    "name": "[parameters('VNetSettings').name]",
    "location": "[parameters('VNetSettings').location]",
    "properties": {
      "addressSpace":{
        "addressPrefixes": [
          "[parameters('VNetSettings').addressPrefixes[0].addressPrefix]"
        ]
      },
      "subnets":[
        {
          "name":"[parameters('VNetSettings').subnets[0].name]",
          "properties": {
            "addressPrefix": "[parameters('VNetSettings').subnets[0].addressPrefix]"
          }
        },
        {
          "name":"[parameters('VNetSettings').subnets[1].name]",
          "properties": {
            "addressPrefix": "[parameters('VNetSettings').subnets[1].addressPrefix]"
          }
        }
      ]
    }
  }
]
```

## <a name="example-templates"></a>Przykładowe szablony

W poniższych przykładach przedstawiono scenariusze używania parametrów.

|Template  |Opis  |
|---------|---------|
|[parametry z funkcjami dla wartości domyślnych](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/parameterswithfunctions.json) | Pokazuje, jak używać funkcji szablonu podczas definiowania wartości domyślnych dla parametrów. Szablon nie wdraża żadnych zasobów. Konstruuje wartości parametrów i zwraca te wartości. |
|[Parameter — obiekt](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/parameterobject.json) | Demonstruje użycie obiektu dla parametru. Szablon nie wdraża żadnych zasobów. Konstruuje wartości parametrów i zwraca te wartości. |

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej o dostępnych właściwościach parametrów, zobacz [Opis struktury i składni szablonów usługi ARM](template-syntax.md).
* Aby dowiedzieć się więcej o przekazywaniu wartości parametrów jako pliku, zobacz [Tworzenie pliku parametrów Menedżer zasobów](parameter-files.md).
* Aby zapoznać się z zaleceniami dotyczącymi tworzenia parametrów, zobacz [Best Practices-Parameters](template-best-practices.md#parameters).
