---
title: Deklarowanie zasobów w szablonach
description: Opisuje sposób deklarowania zasobów do wdrożenia w szablonie Azure Resource Manager (szablon ARM).
ms.topic: conceptual
ms.date: 03/02/2021
ms.openlocfilehash: 13f4a08162c40cbb36173627d88a4a8202a4ed26
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101746660"
---
# <a name="resource-declaration-in-arm-templates"></a>Deklaracja zasobów w szablonach ARM

Aby wdrożyć zasób przy użyciu szablonu Azure Resource Manager (szablon ARM), należy dodać deklarację zasobu. Użyj klasy `resources` Array for JSON lub `resource` słowa kluczowego for Bicep.

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="set-resource-type-and-version"></a>Ustaw typ i wersję zasobu

Podczas dodawania zasobu do szablonu, Zacznij od ustawienia Typ zasobu i wersja interfejsu API. Te wartości określają inne właściwości, które są dostępne dla zasobu.

Poniższy przykład pokazuje, jak ustawić typ zasobu i wersję interfejsu API dla konta magazynu. Przykład nie pokazuje pełnej deklaracji zasobu.

# <a name="json"></a>[JSON](#tab/json)

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2019-06-01",
    ...
  }
]
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
resource sa 'Microsoft.Storage/storageAccounts@2019-06-01' = {
  ...
}
```

---

Dla Bicep należy ustawić nazwę symboliczną dla zasobu. W poprzednim przykładzie Nazwa symboliczna to `sa` . Można użyć dowolnej wartości dla nazwy symbolicznej, ale nie może ona być taka sama jak w szablonie innego zasobu, parametru lub zmiennej. Nazwa symboliczna nie jest taka sama jak nazwa zasobu. Nazwa symboliczna jest używana do łatwego odwoływania się do zasobu w innych częściach szablonu.

## <a name="set-resource-name"></a>Ustaw nazwę zasobu

Każdy zasób ma nazwę. Podczas ustawiania nazwy zasobu należy zwrócić uwagę na [reguły i ograniczenia dotyczące nazw zasobów](../management/resource-name-rules.md).

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "storageAccountName": {
    "type": "string",
    "minLength": 3,
    "maxLength": 24
  }
},
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2019-06-01",
    "name": "[parameters('storageAccountName')]",
    ...
  }
]
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
@minLength(3)
@maxLength(24)
param storageAccountName string

resource sa 'Microsoft.Storage/storageAccounts@2019-06-01' = {
  name: storageAccountName
  ...
}
```

---

## <a name="set-location"></a>Ustawianie lokalizacji

Wiele zasobów wymaga lokalizacji. Można określić, czy zasób potrzebuje lokalizacji za pomocą funkcji IntelliSense lub [odwołania do szablonu](/azure/templates/). Poniższy przykład dodaje parametr Location, który jest używany dla konta magazynu.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "storageAccountName": {
    "type": "string",
    "minLength": 3,
    "maxLength": 24
  },
  "location": {
    "type": "string",
    "defaultValue": "[resourceGroup().location]"
  }
},
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2019-06-01",
    "name": "[parameters('storageAccountName')]",
    "location": "[parameters('location')]",
    ...
  }
]
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
@minLength(3)
@maxLength(24)
param storageAccountName string
param location string = resourceGroup().location

resource sa 'Microsoft.Storage/storageAccounts@2019-06-01' = {
  name: storageAccountName
  location: location
  ...
}
```

---

Aby uzyskać więcej informacji, zobacz [Ustawianie lokalizacji zasobów w szablonie ARM](resource-location.md).

## <a name="set-tags"></a>Ustaw Tagi

Możesz zastosować znaczniki do zasobu podczas wdrażania. Tagi pomagają logicznie organizować wdrożone zasoby. Przykłady różnych sposobów określania tagów można znaleźć w temacie [Tagi szablonów ARM](../management/tag-resources.md#arm-templates).

## <a name="set-resource-specific-properties"></a>Ustawianie właściwości specyficznych dla zasobów

Powyższe właściwości są ogólne dla większości typów zasobów. Po ustawieniu tych wartości należy ustawić właściwości specyficzne dla typu zasobu, który jest wdrażany.

Użyj funkcji IntelliSense lub [Dokumentacja szablonu](/azure/templates/) , aby określić, które właściwości są dostępne i które są wymagane. Poniższy przykład ustawia pozostałe właściwości dla konta magazynu.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountName": {
      "type": "string",
      "minLength": 3,
      "maxLength": 24
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]"
    }
  },
  "functions": [],
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-06-01",
      "name": "[parameters('storageAccountName')]",
      "location": "[parameters('location')]",
      "sku": {
        "name": "Standard_LRS",
        "tier": "Standard"
      },
      "kind": "StorageV2",
      "properties": {
        "accessTier": "Hot"
      }
    }
  ]
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
@minLength(3)
@maxLength(24)
param storageAccountName string
param location string = resourceGroup().location

resource sa 'Microsoft.Storage/storageAccounts@2019-06-01' = {
  name: storageAccountName
  location: location
  sku: {
    name: 'Standard_LRS'
    tier: 'Standard'
  }
  kind: 'StorageV2'
  properties: {
    accessTier: 'Hot'
  }
}
```

---

## <a name="next-steps"></a>Następne kroki

* Aby warunkowo wdrożyć zasób, zobacz [wdrażanie warunkowe w szablonach ARM](conditional-resource-deployment.md).
* Aby ustawić zależności zasobów, zobacz [Definiowanie kolejności wdrażania zasobów w usłudze ARM](define-resource-dependency.md).
