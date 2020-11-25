---
title: Funkcje szablonu — porównanie
description: Opisuje funkcje, które mają być używane w szablonie Azure Resource Manager do porównywania wartości.
ms.topic: conceptual
ms.date: 11/18/2020
ms.openlocfilehash: c5ffcfe7688935da6ea5602cdb2c66a8b86a8d88
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/21/2020
ms.locfileid: "96004606"
---
# <a name="comparison-functions-for-arm-templates"></a>Funkcje porównania dla szablonów ARM

Menedżer zasobów udostępnia kilka funkcji służących do dokonywania porównań w szablonach Azure Resource Manager (ARM).

* [łączonych](#coalesce)
* [equals](#equals)
* [greater](#greater)
* [greaterOrEquals](#greaterorequals)
* [wcześniejsz](#less)
* [lessOrEquals](#lessorequals)

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="coalesce"></a>łączonych

`coalesce(arg1, arg2, arg3, ...)`

Zwraca pierwszą wartość różną od zera z parametrów. Puste ciągi, puste tablice i puste obiekty nie mają wartości null.

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
|:--- |:--- |:--- |:--- |
| arg1 |Tak |int, String, array lub Object |Pierwsza wartość do przetestowania dla wartości null. |
| dodatkowe argumenty |Nie |int, String, array lub Object |Dodatkowe wartości do przetestowania na wartość null. |

### <a name="return-value"></a>Wartość zwracana

Wartość pierwszych parametrów innych niż null, które mogą być ciągami, int, tablicami lub obiektami. Wartość null, jeśli wszystkie parametry mają wartość null.

### <a name="example"></a>Przykład

Poniższy [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/coalesce.json) przedstawia dane wyjściowe z różnych sposobów łączenia.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "objectToTest": {
      "type": "object",
      "defaultValue": {
        "null1": null,
        "null2": null,
        "string": "default",
        "int": 1,
        "object": { "first": "default" },
        "array": [ 1 ]
      }
    }
  },
  "resources": [
  ],
  "outputs": {
    "stringOutput": {
      "type": "string",
      "value": "[coalesce(parameters('objectToTest').null1, parameters('objectToTest').null2, parameters('objectToTest').string)]"
    },
    "intOutput": {
      "type": "int",
      "value": "[coalesce(parameters('objectToTest').null1, parameters('objectToTest').null2, parameters('objectToTest').int)]"
    },
    "objectOutput": {
      "type": "object",
      "value": "[coalesce(parameters('objectToTest').null1, parameters('objectToTest').null2, parameters('objectToTest').object)]"
    },
    "arrayOutput": {
      "type": "array",
      "value": "[coalesce(parameters('objectToTest').null1, parameters('objectToTest').null2, parameters('objectToTest').array)]"
    },
    "emptyOutput": {
      "type": "bool",
      "value": "[empty(coalesce(parameters('objectToTest').null1, parameters('objectToTest').null2))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param objectToTest object = {
  'null1': null
  'null2': null
  'string': 'default'
  'int': 1
  'object': {
    'first': 'default'
  }
  'array': [
    1
  ]
}

output stringOutput string = coalesce(objectToTest.null1, objectToTest.null2, objectToTest.string)
output intOutput int = coalesce(objectToTest.null1, objectToTest.null2, objectToTest.int)
output objectOutput object = coalesce(objectToTest.null1, objectToTest.null2, objectToTest.object)
output arrayOutput array = coalesce(objectToTest.null1, objectToTest.null2, objectToTest.array)
output emptyOutput bool =empty(coalesce(objectToTest.null1, objectToTest.null2))
```

---

Dane wyjściowe z poprzedniego przykładu z wartościami domyślnymi są następujące:

| Nazwa | Typ | Wartość |
| ---- | ---- | ----- |
| stringOutput | Ciąg | default |
| intOutput | int | 1 |
| objectOutput | Obiekt | {"First": "default"} |
| arrayOutput | Tablica |  [1] |
| emptyOutput | Wartość logiczna | Prawda |

## <a name="equals"></a>equals

`equals(arg1, arg2)`

Sprawdza, czy dwie wartości są równe siebie. `equals`Funkcja nie jest obsługiwana w Bicep. `==`Zamiast tego użyj operatora.

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
|:--- |:--- |:--- |:--- |
| arg1 |Tak |int, String, array lub Object |Pierwsza wartość do sprawdzenia równości. |
| arg2 |Tak |int, String, array lub Object |Druga wartość do sprawdzenia równości. |

### <a name="return-value"></a>Wartość zwracana

Zwraca **wartość true** , jeśli wartości są równe. w przeciwnym razie **false**.

### <a name="remarks"></a>Uwagi

Funkcja Equals jest często używana z elementem, `condition` Aby sprawdzić, czy zasób został wdrożony.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "condition": "[equals(parameters('newOrExisting'),'new')]",
  "type": "Microsoft.Storage/storageAccounts",
  "name": "[variables('storageAccountName')]",
  "apiVersion": "2017-06-01",
  "location": "[resourceGroup().location]",
  "sku": {
    "name": "[variables('storageAccountType')]"
  },
  "kind": "Storage",
  "properties": {}
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

> [!NOTE]
> `Conditions` nie zostały jeszcze zaimplementowane w Bicep. Zobacz [warunki](https://github.com/Azure/bicep/issues/186).

---

### <a name="example"></a>Przykład

Poniższy [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/equals.json) sprawdza różne typy wartości dla równości. Wszystkie wartości domyślne zwracają wartość true.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "firstInt": {
      "type": "int",
      "defaultValue": 1
    },
    "secondInt": {
      "type": "int",
      "defaultValue": 1
    },
    "firstString": {
      "type": "string",
      "defaultValue": "a"
    },
    "secondString": {
      "type": "string",
      "defaultValue": "a"
    },
    "firstArray": {
      "type": "array",
      "defaultValue": [ "a", "b" ]
    },
    "secondArray": {
      "type": "array",
      "defaultValue": [ "a", "b" ]
    },
    "firstObject": {
      "type": "object",
      "defaultValue": { "a": "b" }
    },
    "secondObject": {
      "type": "object",
      "defaultValue": { "a": "b" }
    }
  },
  "resources": [
  ],
  "outputs": {
    "checkInts": {
      "type": "bool",
      "value": "[equals(parameters('firstInt'), parameters('secondInt') )]"
    },
    "checkStrings": {
      "type": "bool",
      "value": "[equals(parameters('firstString'), parameters('secondString'))]"
    },
    "checkArrays": {
      "type": "bool",
      "value": "[equals(parameters('firstArray'), parameters('secondArray'))]"
    },
    "checkObjects": {
      "type": "bool",
      "value": "[equals(parameters('firstObject'), parameters('secondObject'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param firstInt int = 1
param secondInt int = 1
param firstString string = 'a'
param secondString string = 'a'
param firstArray array = [
  'a'
  'b'
]
param secondArray array = [
  'a'
  'b'
]
param firstObject object = {
  'a': 'b'
}
param secondObject object = {
  'a': 'b'
}

output checInts bool = firstInt == secondInt
output checkStrings bool = firstString == secondString
output checkArrays bool = firstArray == secondArray
output checkObjects bool = firstObject == secondObject
```

---

Dane wyjściowe z poprzedniego przykładu z wartościami domyślnymi są następujące:

| Nazwa | Typ | Wartość |
| ---- | ---- | ----- |
| checkInts | Wartość logiczna | Prawda |
| checkStrings | Wartość logiczna | Prawda |
| checkArrays | Wartość logiczna | Prawda |
| checkObjects | Wartość logiczna | Prawda |

Poniższy [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/not-equals.json) używa [nie](template-functions-logical.md#not) z **równą**.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [
  ],
  "outputs": {
    "checkNotEquals": {
      "type": "bool",
      "value": "[not(equals(1, 2))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
output checkNotEquals bool = ! (1 == 2)
```

---

Dane wyjściowe z poprzedniego przykładu to:

| Nazwa | Typ | Wartość |
| ---- | ---- | ----- |
| checkNotEquals | Wartość logiczna | Prawda |

## <a name="greater"></a>greater

`greater(arg1, arg2)`

Sprawdza, czy pierwsza wartość jest większa od drugiej wartości. `greater`Funkcja nie jest obsługiwana w Bicep. `>`Zamiast tego użyj operatora.

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
|:--- |:--- |:--- |:--- |
| arg1 |Tak |int lub String |Pierwsza wartość dla większego porównania. |
| arg2 |Tak |int lub String |Druga wartość dla większego porównania. |

### <a name="return-value"></a>Wartość zwracana

Zwraca **wartość true** , jeśli pierwsza wartość jest większa niż druga wartość; w przeciwnym razie **false**.

### <a name="example"></a>Przykład

Poniższy [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/greater.json) sprawdza, czy wartość jest większa od drugiej.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "firstInt": {
      "type": "int",
      "defaultValue": 1
    },
    "secondInt": {
      "type": "int",
      "defaultValue": 2
    },
    "firstString": {
      "type": "string",
      "defaultValue": "A"
    },
    "secondString": {
      "type": "string",
      "defaultValue": "a"
    }
  },
  "resources": [
  ],
  "outputs": {
    "checkInts": {
      "type": "bool",
      "value": "[greater(parameters('firstInt'), parameters('secondInt') )]"
    },
    "checkStrings": {
      "type": "bool",
      "value": "[greater(parameters('firstString'), parameters('secondString'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param firstInt int = 1
param secondInt int = 2
param firstString string = 'A'
param secondString string = 'a'

output checkInts bool = firstInt > secondInt
output checkStrings bool = firstString > secondString
```

---

Dane wyjściowe z poprzedniego przykładu z wartościami domyślnymi są następujące:

| Nazwa | Typ | Wartość |
| ---- | ---- | ----- |
| checkInts | Wartość logiczna | Fałsz |
| checkStrings | Wartość logiczna | Prawda |

## <a name="greaterorequals"></a>greaterOrEquals

`greaterOrEquals(arg1, arg2)`

Sprawdza, czy pierwsza wartość jest większa lub równa drugiej wartości. `greaterOrEquals`Funkcja nie jest obsługiwana w Bicep. `>=`Zamiast tego użyj operatora.

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
|:--- |:--- |:--- |:--- |
| arg1 |Tak |int lub String |Pierwsza wartość dla porównania wyższego lub równego. |
| arg2 |Tak |int lub String |Druga wartość dla porównania wyższego lub równego. |

### <a name="return-value"></a>Wartość zwracana

Zwraca **wartość true** , jeśli pierwsza wartość jest większa lub równa drugiej wartości; w przeciwnym razie **false**.

### <a name="example"></a>Przykład

Poniższy [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/greaterorequals.json) sprawdza, czy jedna wartość jest większa lub równa drugiej.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "firstInt": {
      "type": "int",
      "defaultValue": 1
    },
    "secondInt": {
      "type": "int",
      "defaultValue": 2
    },
    "firstString": {
      "type": "string",
      "defaultValue": "A"
    },
    "secondString": {
      "type": "string",
      "defaultValue": "a"
    }
  },
  "resources": [
  ],
  "outputs": {
    "checkInts": {
      "type": "bool",
      "value": "[greaterOrEquals(parameters('firstInt'), parameters('secondInt') )]"
    },
    "checkStrings": {
      "type": "bool",
      "value": "[greaterOrEquals(parameters('firstString'), parameters('secondString'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param firstInt int = 1
param secondInt int = 2
param firstString string = 'A'
param secondString string = 'a'

output checkInts bool = firstInt >= secondInt
output checkStrings bool = firstString >= secondString
```

---

Dane wyjściowe z poprzedniego przykładu z wartościami domyślnymi są następujące:

| Nazwa | Typ | Wartość |
| ---- | ---- | ----- |
| checkInts | Wartość logiczna | Fałsz |
| checkStrings | Wartość logiczna | Prawda |

## <a name="less"></a>less

`less(arg1, arg2)`

Sprawdza, czy pierwsza wartość jest mniejsza od drugiej wartości. `less`Funkcja nie jest obsługiwana w Bicep. `<`Zamiast tego użyj operatora.

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
|:--- |:--- |:--- |:--- |
| arg1 |Tak |int lub String |Pierwsza wartość dla mniejszego porównania. |
| arg2 |Tak |int lub String |Druga wartość dla mniejszego porównania. |

### <a name="return-value"></a>Wartość zwracana

Zwraca **wartość true** , jeśli pierwsza wartość jest mniejsza od drugiej wartości; w przeciwnym razie **false**.

### <a name="example"></a>Przykład

Poniższy [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/less.json) sprawdza, czy jedna wartość jest mniejsza od drugiej.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "firstInt": {
      "type": "int",
      "defaultValue": 1
    },
    "secondInt": {
      "type": "int",
      "defaultValue": 2
    },
    "firstString": {
      "type": "string",
      "defaultValue": "A"
    },
    "secondString": {
      "type": "string",
      "defaultValue": "a"
    }
  },
  "resources": [
  ],
  "outputs": {
    "checkInts": {
      "type": "bool",
      "value": "[less(parameters('firstInt'), parameters('secondInt') )]"
    },
    "checkStrings": {
      "type": "bool",
      "value": "[less(parameters('firstString'), parameters('secondString'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param firstInt int = 1
param secondInt int = 2
param firstString string = 'A'
param secondString string = 'a'

output checkInts bool = firstInt < secondInt
output checkStrings bool = firstString < secondString
```

---

Dane wyjściowe z poprzedniego przykładu z wartościami domyślnymi są następujące:

| Nazwa | Typ | Wartość |
| ---- | ---- | ----- |
| checkInts | Wartość logiczna | Prawda |
| checkStrings | Wartość logiczna | Fałsz |

## <a name="lessorequals"></a>lessOrEquals

`lessOrEquals(arg1, arg2)`

Sprawdza, czy pierwsza wartość jest mniejsza lub równa drugiej wartości. `lessOrEquals`Funkcja nie jest obsługiwana w Bicep. `<=`Zamiast tego użyj operatora.

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
|:--- |:--- |:--- |:--- |
| arg1 |Tak |int lub String |Pierwsza wartość dla porównania less lub równa się. |
| arg2 |Tak |int lub String |Druga wartość dla porównania less lub równa się. |

### <a name="return-value"></a>Wartość zwracana

Zwraca **wartość true** , jeśli pierwsza wartość jest mniejsza lub równa drugiej wartości; w przeciwnym razie **false**.

### <a name="example"></a>Przykład

Poniższy [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/lessorequals.json) sprawdza, czy jedna wartość jest mniejsza lub równa drugiej.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "firstInt": {
      "type": "int",
      "defaultValue": 1
    },
    "secondInt": {
      "type": "int",
      "defaultValue": 2
    },
    "firstString": {
      "type": "string",
      "defaultValue": "A"
    },
    "secondString": {
      "type": "string",
      "defaultValue": "a"
    }
  },
  "resources": [
  ],
  "outputs": {
    "checkInts": {
      "type": "bool",
      "value": "[lessOrEquals(parameters('firstInt'), parameters('secondInt') )]"
    },
    "checkStrings": {
      "type": "bool",
      "value": "[lessOrEquals(parameters('firstString'), parameters('secondString'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param firstInt int = 1
param secondInt int = 2
param firstString string = 'A'
param secondString string = 'a'

output checkInts bool = firstInt <= secondInt
output checkStrings bool = firstString <= secondString
```

---

Dane wyjściowe z poprzedniego przykładu z wartościami domyślnymi są następujące:

| Nazwa | Typ | Wartość |
| ---- | ---- | ----- |
| checkInts | Wartość logiczna | Prawda |
| checkStrings | Wartość logiczna | Fałsz |

## <a name="next-steps"></a>Następne kroki

* Opis sekcji w szablonie Azure Resource Manager można znaleźć w temacie [Omówienie struktury i składni szablonów usługi ARM](template-syntax.md).
