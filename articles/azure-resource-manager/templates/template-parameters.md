---
title: Parametry w szablonach
description: Opisuje sposób definiowania parametrów w pliku szablonu Azure Resource Manager (szablon ARM) i Bicep.
ms.topic: conceptual
ms.date: 03/03/2021
ms.openlocfilehash: a8608e4733960d7f5ba7e5f548c47f16a2b244bb
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102123398"
---
# <a name="parameters-in-arm-templates"></a>Parametry w szablonach ARM

W tym artykule opisano sposób definiowania i używania parametrów w szablonie Azure Resource Manager (szablon ARM) i pliku Bicep. Dostarczając różne wartości parametrów, można ponownie użyć szablonu dla różnych środowisk.

Menedżer zasobów rozwiązuje wartości parametrów przed rozpoczęciem operacji wdrażania. W każdym przypadku, gdy parametr jest używany w szablonie, Menedżer zasobów zastępuje go rozpoznaną wartością.

Każdy parametr musi być ustawiony na jeden z [typów danych](data-types.md).

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="minimal-declaration"></a>Minimalna deklaracja

Co najmniej każdy parametr musi mieć nazwę i typ. W Bicep parametr nie może mieć takiej samej nazwy jak zmienna, zasób, wyjście lub inny parametr w tym samym zakresie.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "demoString": {
    "type": "string"
  },
  "demoInt": {
    "type": "int"
  },
  "demoBool": {
    "type": "bool"
  },
  "demoObject": {
    "type": "object"
  },
  "demoArray": {
    "type": "array"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param demoString string
param demoInt int
param demoBool bool
param demoObject object
param demoArray array
```

---

## <a name="secure-parameters"></a>Parametry bezpieczne

Parametry ciągu lub obiektu można oznaczyć jako bezpieczne. Wartość bezpiecznego parametru nie jest zapisywana w historii wdrożenia i nie jest zarejestrowana.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "demoPassword": {
    "type": "secureString"
  },
  "demoSecretObject": {
    "type": "secureObject"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
@secure()
param demoPassword string

@secure()
param demoSecretObject object
```

---

## <a name="allowed-values"></a>Dozwolone wartości

Można zdefiniować dozwolone wartości parametru. Podajesz dozwolone wartości w tablicy. Wdrożenie kończy się niepowodzeniem podczas walidacji, jeśli wartość jest przenoszona dla parametru, który nie jest jedną z dozwolonych wartości.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "demoEnum": {
    "type": "string",
    "allowedValues": [
      "one",
      "two"
    ]
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
@allowed([
  'one'
  'two'
])
param demoEnum string
```

---

## <a name="default-value"></a>Wartość domyślna

Możesz określić wartość domyślną dla parametru. Wartość domyślna jest używana, gdy wartość nie zostanie podana podczas wdrażania.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "demoParam": {
    "type": "string",
    "defaultValue": "Contoso"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param demoParam string = 'Contoso'
```

---

Aby określić wartość domyślną wraz z innymi właściwościami parametru, użyj następującej składni.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "demoParam": {
    "type": "string",
    "defaultValue": "Contoso",
    "allowedValues": [
      "Contoso",
      "Fabrikam"
    ]
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
@allowed([
  'Contoso'
  'Fabrikam'
])
param demoParam string = 'Contoso'
```

---

Można używać wyrażeń z wartością domyślną. Nie można użyć funkcji [Reference](template-functions-resource.md#reference) ani żadnej z funkcji [list](template-functions-resource.md#list) w sekcji Parameters. Te funkcje uzyskują stan środowiska uruchomieniowego zasobu i nie można ich wykonać przed wdrożeniem, gdy parametry zostaną rozwiązane.

Wyrażenia nie są dozwolone z innymi właściwościami parametrów.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "location": {
    "type": "string",
    "defaultValue": "[resourceGroup().location]"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param location string = resourceGroup().location
```

---

Możesz użyć innej wartości parametru do skompilowania wartości domyślnej. Poniższy szablon konstruuje nazwę planu hosta z nazwy lokacji.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "siteName": {
    "type": "string",
    "defaultValue": "[concat('site', uniqueString(resourceGroup().id))]"
  },
  "hostingPlanName": {
    "type": "string",
    "defaultValue": "[concat(parameters('siteName'),'-plan')]"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param siteName string = 'site${uniqueString(resourceGroup().id)}'
param hostingPlanName string = '${siteName}-plan'
```

---

## <a name="length-constraints"></a>Ograniczenia długości

Można określić minimalną i maksymalną długość dla parametrów String i Array. Można ustawić jedno lub oba ograniczenia. W przypadku ciągów długość wskazuje liczbę znaków. W przypadku tablic długość wskazuje liczbę elementów w tablicy.

Poniższy przykład deklaruje dwa parametry. Jeden z parametrów jest dla nazwy konta magazynu, która musi zawierać 3-24 znaków. Drugi parametr jest tablicą, która musi zawierać od 1-5 elementów.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "storageAccountName": {
    "type": "string",
    "minLength": 3,
    "maxLength": 24
  },
  "appNames": {
    "type": "array",
    "minLength": 1,
    "maxLength": 5
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
@minLength(3)
@maxLength(24)
param storageAccountName string

@minLength(1)
@maxLength(5)
param appNames array
```

---

## <a name="integer-constraints"></a>Ograniczenia liczby całkowitej

Można ustawić minimalną i maksymalną wartość dla parametrów Integer. Można ustawić jedno lub oba ograniczenia.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "month": {
    "type": "int",
    "minValue": 1,
    "maxValue": 12
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
@minValue(1)
@maxValue(12)
param month int
```

---

## <a name="description"></a>Opis

Możesz dodać opis do parametru, aby ułatwić użytkownikom szablonu zrozumienie wartości, która ma zostać wprowadzona. Podczas wdrażania szablonu za pomocą portalu, tekst w opisie jest automatycznie używany jako Porada dla tego parametru. Dodaj opis tylko wtedy, gdy tekst zawiera więcej informacji niż można wywnioskować na podstawie nazwy parametru.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "virtualMachineSize": {
    "type": "string",
    "metadata": {
      "description": "Must be at least Standard_A3 to support 2 NICs."
    },
    "defaultValue": "Standard_DS1_v2"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
@description('Must be at least Standard_A3 to support 2 NICs.')
param virtualMachineSize string = 'Standard_DS1_v2'
```

---

## <a name="use-parameter"></a>Użyj parametru

W szablonie JSON należy odwołać się do wartości parametru za pomocą funkcji [Parameters](template-functions-deployment.md#parameters) . W Bicep należy użyć nazwy parametru. Poniższy przykład używa wartości parametru dla nazwy Key Vault.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "vaultName": {
      "type": "string",
      "defaultValue": "[format('keyVault{0}', uniqueString(resourceGroup().id))]"
    }
  },
  "resources": [
    {
      "type": "Microsoft.KeyVault/vaults",
      "apiVersion": "2019-09-01",
      "name": "[parameters('vaultName')]",
      ...
    }
  ]
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param vaultName string = 'keyVault${uniqueString(resourceGroup().id)}'

resource keyvault 'Microsoft.KeyVault/vaults@2019-09-01' = {
  name: vaultName
  ...
}
```

---

## <a name="objects-as-parameters"></a>Obiekty jako parametry

Można łatwiej organizować powiązane wartości, przekazując je w postaci obiektu. Takie podejście zmniejsza również liczbę parametrów w szablonie.

Poniższy przykład przedstawia parametr, który jest obiektem. Wartość domyślna zawiera oczekiwane właściwości obiektu. Te właściwości są używane podczas definiowania zasobu do wdrożenia.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "vNetSettings": {
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
  "resources": [
    {
      "type": "Microsoft.Network/virtualNetworks",
      "apiVersion": "2020-06-01",
      "name": "[parameters('vNetSettings').name]",
      "location": "[parameters('vNetSettings').location]",
      "properties": {
        "addressSpace": {
          "addressPrefixes": [
            "[parameters('vNetSettings').addressPrefixes[0].addressPrefix]"
          ]
        },
        "subnets": [
          {
            "name": "[parameters('vNetSettings').subnets[0].name]",
            "properties": {
              "addressPrefix": "[parameters('vNetSettings').subnets[0].addressPrefix]"
            }
          },
          {
            "name": "[parameters('vNetSettings').subnets[1].name]",
            "properties": {
              "addressPrefix": "[parameters('vNetSettings').subnets[1].addressPrefix]"
            }
          }
        ]
      }
    }
  ]
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param vNetSettings object = {
  name: 'VNet1'
  location: 'eastus'
  addressPrefixes: [
    {
      name: 'firstPrefix'
      addressPrefix: '10.0.0.0/22'
    }
  ]
  subnets: [
    {
      name: 'firstSubnet'
      addressPrefix: '10.0.0.0/24'
    }
    {
      name: 'secondSubnet'
      addressPrefix: '10.0.1.0/24'
    }
  ]
}
resource vnet 'Microsoft.Network/virtualNetworks@2020-06-01' = {
  name: vNetSettings.name
  location: vNetSettings.location
  properties: {
    addressSpace: {
      addressPrefixes: [
        vNetSettings.addressPrefixes[0].addressPrefix
      ]
    }
    subnets: [
      {
        name: vNetSettings.subnets[0].name
        properties: {
          addressPrefix: vNetSettings.subnets[0].addressPrefix
        }
      }
      {
        name: vNetSettings.subnets[1].name
        properties: {
          addressPrefix: vNetSettings.subnets[1].addressPrefix
        }
      }
    ]
  }
}
```

---

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
