---
title: Funkcje szablonu — ciąg
description: Opisuje funkcje, które mają być używane w szablonie Azure Resource Manager (szablon ARM) do pracy z ciągami.
ms.topic: conceptual
ms.date: 03/02/2021
ms.openlocfilehash: e823acc07ce0618c064f30e103ec52b7133cea18
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101731123"
---
# <a name="string-functions-for-arm-templates"></a>Funkcje ciągów dla szablonów ARM

Menedżer zasobów udostępnia następujące funkcje do pracy z ciągami w szablonie Azure Resource Manager (szablon ARM):

* [base64](#base64)
* [base64ToJson](#base64tojson)
* [base64ToString](#base64tostring)
* [Concat](#concat)
* [wyświetlana](#contains)
* [dataUri](#datauri)
* [dataUriToString](#datauritostring)
* [puste](#empty)
* [endsWith](#endswith)
* [pierwszego](#first)
* [Formatowanie](#format)
* [guid](#guid)
* [indexOf](#indexof)
* [kodu](#json)
* [ostatniego](#last)
* [lastIndexOf](#lastindexof)
* [length](#length)
* [newGuid](#newguid)
* [padLeft](#padleft)
* [stępować](#replace)
* [Skocz](#skip)
* [podziału](#split)
* [startsWith](#startswith)
* [ciąg](#string)
* [podciąg](#substring)
* [czasochłonn](#take)
* [toLower](#tolower)
* [toUpper](#toupper)
* [Trim](#trim)
* [uniqueString](#uniquestring)
* [adresu](#uri)
* [uriComponent](#uricomponent)
* [uriComponentToString](#uricomponenttostring)

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="base64"></a>base64

`base64(inputString)`

Zwraca reprezentację Base64 ciągu wejściowego.

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
|:--- |:--- |:--- |:--- |
| inputString |Tak |ciąg |Wartość, która ma zostać zwrócona jako reprezentacja Base64. |

### <a name="return-value"></a>Wartość zwracana

Ciąg zawierający reprezentację Base64.

### <a name="examples"></a>Przykłady

Poniższy [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/base64.json) pokazuje, jak używać funkcji Base64.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "stringData": {
      "type": "string",
      "defaultValue": "one, two, three"
    },
    "jsonFormattedData": {
      "type": "string",
      "defaultValue": "{'one': 'a', 'two': 'b'}"
    }
  },
  "variables": {
    "base64String": "[base64(parameters('stringData'))]",
    "base64Object": "[base64(parameters('jsonFormattedData'))]"
  },
  "resources": [
  ],
  "outputs": {
    "base64Output": {
      "type": "string",
      "value": "[variables('base64String')]"
    },
    "toStringOutput": {
      "type": "string",
      "value": "[base64ToString(variables('base64String'))]"
    },
    "toJsonOutput": {
      "type": "object",
      "value": "[base64ToJson(variables('base64Object'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param stringData string = 'one, two, three'
param jsonFormattedData string = '{\'one\': \'a\', \'two\': \'b\'}'

var base64String = base64(stringData)
var base64Object = base64(jsonFormattedData)

output base64Output string = base64String
output toStringOutput string = base64ToString(base64String)
output toJsonOutput object = base64ToJson(base64Object)
```

---

Dane wyjściowe z poprzedniego przykładu z wartościami domyślnymi są następujące:

| Nazwa | Typ | Wartość |
| ---- | ---- | ----- |
| base64Output | Ciąg | b25lLCB0d28sIHRocmVl |
| toStringOutput | Ciąg | Raz dwa trzy |
| toJsonOutput | Obiekt | {"jeden": "a", "dwa": "b"} |

## <a name="base64tojson"></a>base64ToJson

`base64tojson`

Konwertuje reprezentację Base64 na obiekt JSON.

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
|:--- |:--- |:--- |:--- |
| base64Value |Tak |ciąg |Reprezentacja Base64 do przekonwertowania na obiekt JSON. |

### <a name="return-value"></a>Wartość zwracana

Obiekt JSON.

### <a name="examples"></a>Przykłady

Poniższy [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/base64.json) używa funkcji base64ToJson do konwersji wartości Base64:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "stringData": {
      "type": "string",
      "defaultValue": "one, two, three"
    },
    "jsonFormattedData": {
      "type": "string",
      "defaultValue": "{'one': 'a', 'two': 'b'}"
    }
  },
  "variables": {
    "base64String": "[base64(parameters('stringData'))]",
    "base64Object": "[base64(parameters('jsonFormattedData'))]"
  },
  "resources": [
  ],
  "outputs": {
    "base64Output": {
      "type": "string",
      "value": "[variables('base64String')]"
    },
    "toStringOutput": {
      "type": "string",
      "value": "[base64ToString(variables('base64String'))]"
    },
    "toJsonOutput": {
      "type": "object",
      "value": "[base64ToJson(variables('base64Object'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param stringData string = 'one, two, three'
param jsonFormattedData string = '{\'one\': \'a\', \'two\': \'b\'}'

var base64String = base64(stringData)
var base64Object = base64(jsonFormattedData)

output base64Output string = base64String
output toStringOutput string = base64ToString(base64String)
output toJsonOutput object = base64ToJson(base64Object)

```

---

Dane wyjściowe z poprzedniego przykładu z wartościami domyślnymi są następujące:

| Nazwa | Typ | Wartość |
| ---- | ---- | ----- |
| base64Output | Ciąg | b25lLCB0d28sIHRocmVl |
| toStringOutput | Ciąg | Raz dwa trzy |
| toJsonOutput | Obiekt | {"jeden": "a", "dwa": "b"} |

## <a name="base64tostring"></a>base64ToString

`base64ToString(base64Value)`

Konwertuje reprezentację Base64 na ciąg.

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
|:--- |:--- |:--- |:--- |
| base64Value |Tak |ciąg |Reprezentacja Base64 do przekonwertowania na ciąg. |

### <a name="return-value"></a>Wartość zwracana

Ciąg przekonwertowanej wartości Base64.

### <a name="examples"></a>Przykłady

Poniższy [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/base64.json) używa funkcji base64ToString do konwersji wartości Base64:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "stringData": {
      "type": "string",
      "defaultValue": "one, two, three"
    },
    "jsonFormattedData": {
      "type": "string",
      "defaultValue": "{'one': 'a', 'two': 'b'}"
    }
  },
  "variables": {
    "base64String": "[base64(parameters('stringData'))]",
    "base64Object": "[base64(parameters('jsonFormattedData'))]"
  },
  "resources": [
  ],
  "outputs": {
    "base64Output": {
      "type": "string",
      "value": "[variables('base64String')]"
    },
    "toStringOutput": {
      "type": "string",
      "value": "[base64ToString(variables('base64String'))]"
    },
    "toJsonOutput": {
      "type": "object",
      "value": "[base64ToJson(variables('base64Object'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param stringData string = 'one, two, three'
param jsonFormattedData string = '{\'one\': \'a\', \'two\': \'b\'}'

var base64String = base64(stringData)
var base64Object = base64(jsonFormattedData)

output base64Output string = base64String
output toStringOutput string = base64ToString(base64String)
output toJsonOutput object = base64ToJson(base64Object)
```

---

Dane wyjściowe z poprzedniego przykładu z wartościami domyślnymi są następujące:

| Nazwa | Typ | Wartość |
| ---- | ---- | ----- |
| base64Output | Ciąg | b25lLCB0d28sIHRocmVl |
| toStringOutput | Ciąg | Raz dwa trzy |
| toJsonOutput | Obiekt | {"jeden": "a", "dwa": "b"} |

## <a name="concat"></a>concat

`concat (arg1, arg2, arg3, ...)`

Łączy wiele wartości ciągów i zwraca ciąg połączony lub łączy wiele tablic i zwraca tablicę z połączeniem.

Aby uprościć łączenie ciągów, Bicep obsługuje składnię [interpolacji ciągów](https://en.wikipedia.org/wiki/String_interpolation#) .

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
|:--- |:--- |:--- |:--- |
| arg1 |Tak |ciąg lub tablica |Pierwszy ciąg lub tablica do łączenia. |
| dodatkowe argumenty |Nie |ciąg lub tablica |Dodatkowe ciągi lub tablice w kolejności sekwencyjnej dla łączenia. |

Ta funkcja może przyjmować dowolną liczbę argumentów i może akceptować ciągi lub tablice dla parametrów. Nie można jednak dostarczyć obu tablic i ciągów dla parametrów. Ciągi są łączone tylko z innymi ciągami.

### <a name="return-value"></a>Wartość zwracana

Ciąg lub tablica połączonych wartości.

### <a name="examples"></a>Przykłady

Poniższy [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/concat-string.json) pokazuje, jak połączyć dwie wartości ciągu i zwrócić połączony ciąg.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "prefix": {
      "type": "string",
      "defaultValue": "prefix"
    }
  },
  "resources": [],
  "outputs": {
    "concatOutput": {
      "type": "string",
      "value": "[concat(parameters('prefix'), '-', uniqueString(resourceGroup().id))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param prefix string = 'prefix'

output concatOutput string = concat(prefix, '-', uniqueString(resourceGroup().id))
```

lub

```bicep
param prefix string = 'prefix'

output concatOutput string = '${prefix}-${uniqueString(resourceGroup().id)}'
```

---

Dane wyjściowe z poprzedniego przykładu z wartościami domyślnymi są następujące:

| Nazwa | Typ | Wartość |
| ---- | ---- | ----- |
| concatOutput | Ciąg | prefiks — 5yj4yjf5mbg72 |

Poniższy [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/concat-array.json) pokazuje, jak połączyć dwie tablice.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "firstArray": {
      "type": "array",
      "defaultValue": [
        "1-1",
        "1-2",
        "1-3"
      ]
    },
    "secondArray": {
      "type": "array",
      "defaultValue": [
        "2-1",
        "2-2",
        "2-3"
      ]
    }
  },
  "resources": [
  ],
  "outputs": {
    "return": {
      "type": "array",
      "value": "[concat(parameters('firstArray'), parameters('secondArray'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param firstArray array = [
  '1-1'
  '1-2'
  '1-3'
]
param secondArray array = [
  '2-1'
  '2-2'
  '2-3'
]

output return array = concat(firstArray, secondArray)
```

---

Dane wyjściowe z poprzedniego przykładu z wartościami domyślnymi są następujące:

| Nazwa | Typ | Wartość |
| ---- | ---- | ----- |
| return | Tablica | ["1-1", "1-2", "1-3", "2-1", "2-2", "2-3"] |

## <a name="contains"></a>zawiera

`contains (container, itemToFind)`

Sprawdza, czy tablica zawiera wartość, obiekt zawiera klucz, lub ciąg zawiera podciąg. W porównaniu z rozróżnianiem wielkości liter są rozróżniane wielkie litery. Jednak podczas testowania, jeśli obiekt zawiera klucz, w porównaniu nie jest rozróżniana wielkość liter.

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
|:--- |:--- |:--- |:--- |
| kontener |Tak |Tablica, obiekt lub ciąg |Wartość, która zawiera wartość do znalezienia. |
| itemToFind |Tak |ciąg lub int |Wartość do znalezienia. |

### <a name="return-value"></a>Wartość zwracana

**Ma wartość true** , jeśli element zostanie znaleziony. w przeciwnym razie **false**.

### <a name="examples"></a>Przykłady

Poniższy [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/contains.json) pokazuje, jak używać Contains z różnymi typami:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "stringToTest": {
      "type": "string",
      "defaultValue": "OneTwoThree"
    },
    "objectToTest": {
      "type": "object",
      "defaultValue": {
        "one": "a",
        "two": "b",
        "three": "c"
      }
    },
    "arrayToTest": {
      "type": "array",
      "defaultValue": [ "one", "two", "three" ]
    }
  },
  "resources": [
  ],
  "outputs": {
    "stringTrue": {
      "type": "bool",
      "value": "[contains(parameters('stringToTest'), 'e')]"
    },
    "stringFalse": {
      "type": "bool",
      "value": "[contains(parameters('stringToTest'), 'z')]"
    },
    "objectTrue": {
      "type": "bool",
      "value": "[contains(parameters('objectToTest'), 'one')]"
    },
    "objectFalse": {
      "type": "bool",
      "value": "[contains(parameters('objectToTest'), 'a')]"
    },
    "arrayTrue": {
      "type": "bool",
      "value": "[contains(parameters('arrayToTest'), 'three')]"
    },
    "arrayFalse": {
      "type": "bool",
      "value": "[contains(parameters('arrayToTest'), 'four')]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param stringToTest string = 'OneTwoThree'
param objectToTest object = {
  'one': 'a'
  'two': 'b'
  'three': 'c'
}
param arrayToTest array = [
  'one'
  'two'
  'three'
]

output stringTrue bool = contains(stringToTest, 'e')
output stringFalse bool = contains(stringToTest, 'z')
output objectTrue bool = contains(objectToTest, 'one')
output objectFalse bool = contains(objectToTest, 'a')
output arrayTrue bool = contains(arrayToTest, 'three')
output arrayFalse bool = contains(arrayToTest, 'four')
```

---

Dane wyjściowe z poprzedniego przykładu z wartościami domyślnymi są następujące:

| Nazwa | Typ | Wartość |
| ---- | ---- | ----- |
| stringTrue | Wartość logiczna | Prawda |
| stringFalse | Wartość logiczna | Fałsz |
| objectTrue | Wartość logiczna | Prawda |
| objectFalse | Wartość logiczna | Fałsz |
| arrayTrue | Wartość logiczna | Prawda |
| arrayFalse | Wartość logiczna | Fałsz |

## <a name="datauri"></a>dataUri

`dataUri(stringToConvert)`

Konwertuje wartość na identyfikator URI danych.

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
|:--- |:--- |:--- |:--- |
| stringToConvert |Tak |ciąg |Wartość do przekonwertowania na identyfikator URI danych. |

### <a name="return-value"></a>Wartość zwracana

Ciąg sformatowany jako identyfikator URI danych.

### <a name="examples"></a>Przykłady

Poniższy [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/datauri.json) konwertuje wartość na identyfikator URI danych i konwertuje identyfikator URI danych na ciąg:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "stringToTest": {
      "type": "string",
      "defaultValue": "Hello"
    },
    "dataFormattedString": {
      "type": "string",
      "defaultValue": "data:;base64,SGVsbG8sIFdvcmxkIQ=="
    }
  },
  "resources": [],
  "outputs": {
    "dataUriOutput": {
      "value": "[dataUri(parameters('stringToTest'))]",
      "type": "string"
    },
    "toStringOutput": {
      "type": "string",
      "value": "[dataUriToString(parameters('dataFormattedString'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param stringToTest string = 'Hello'
param dataFormattedString string = 'data:;base64,SGVsbG8sIFdvcmxkIQ=='

output dataUriOutput string = dataUri(stringToTest)
output toStringOutput string = dataUriToString(dataFormattedString)
```

---

Dane wyjściowe z poprzedniego przykładu z wartościami domyślnymi są następujące:

| Nazwa | Typ | Wartość |
| ---- | ---- | ----- |
| dataUriOutput | Ciąg | dane: text/Plains; charset = UTF8; Base64, SGVsbG8 = |
| toStringOutput | Ciąg | Hello world! |

## <a name="datauritostring"></a>dataUriToString

`dataUriToString(dataUriToConvert)`

Konwertuje wartość sformatowaną identyfikatora URI danych na ciąg.

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
|:--- |:--- |:--- |:--- |
| dataUriToConvert |Tak |ciąg |Wartość identyfikatora URI danych do przekonwertowania. |

### <a name="return-value"></a>Wartość zwracana

Ciąg zawierający przekonwertowaną wartość.

### <a name="examples"></a>Przykłady

Poniższy [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/datauri.json) konwertuje wartość na identyfikator URI danych i konwertuje identyfikator URI danych na ciąg:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "stringToTest": {
      "type": "string",
      "defaultValue": "Hello"
    },
    "dataFormattedString": {
      "type": "string",
      "defaultValue": "data:;base64,SGVsbG8sIFdvcmxkIQ=="
    }
  },
  "resources": [],
  "outputs": {
    "dataUriOutput": {
      "value": "[dataUri(parameters('stringToTest'))]",
      "type": "string"
    },
    "toStringOutput": {
      "type": "string",
      "value": "[dataUriToString(parameters('dataFormattedString'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param stringToTest string = 'Hello'
param dataFormattedString string = 'data:;base64,SGVsbG8sIFdvcmxkIQ=='

output dataUriOutput string = dataUri(stringToTest)
output toStringOutput string = dataUriToString(dataFormattedString)
```

---

Dane wyjściowe z poprzedniego przykładu z wartościami domyślnymi są następujące:

| Nazwa | Typ | Wartość |
| ---- | ---- | ----- |
| dataUriOutput | Ciąg | dane: text/Plains; charset = UTF8; Base64, SGVsbG8 = |
| toStringOutput | Ciąg | Hello world! |

## <a name="empty"></a>puste

`empty(itemToTest)`

Określa, czy tablica, obiekt lub ciąg jest pusty.

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
|:--- |:--- |:--- |:--- |
| itemToTest |Tak |Tablica, obiekt lub ciąg |Wartość, aby sprawdzić, czy jest pusta. |

### <a name="return-value"></a>Wartość zwracana

Zwraca **wartość true** , jeśli wartość jest pusta. w przeciwnym razie **false**.

### <a name="examples"></a>Przykłady

Poniższy [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/empty.json) sprawdza, czy tablica, obiekt i ciąg są puste.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "testArray": {
      "type": "array",
      "defaultValue": []
    },
    "testObject": {
      "type": "object",
      "defaultValue": {}
    },
    "testString": {
      "type": "string",
      "defaultValue": ""
    }
  },
  "resources": [
  ],
  "outputs": {
    "arrayEmpty": {
      "type": "bool",
      "value": "[empty(parameters('testArray'))]"
    },
    "objectEmpty": {
      "type": "bool",
      "value": "[empty(parameters('testObject'))]"
    },
    "stringEmpty": {
      "type": "bool",
      "value": "[empty(parameters('testString'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param testArray array = []
param testObject object = {}
param testString string = ''

output arrayEmpty bool = empty(testArray)
output objectEmpty bool = empty(testObject)
output stringEmpty bool = empty(testString)
```

---

Dane wyjściowe z poprzedniego przykładu z wartościami domyślnymi są następujące:

| Nazwa | Typ | Wartość |
| ---- | ---- | ----- |
| arrayEmpty | Wartość logiczna | Prawda |
| objectEmpty | Wartość logiczna | Prawda |
| stringEmpty | Wartość logiczna | Prawda |

## <a name="endswith"></a>endsWith

`endsWith(stringToSearch, stringToFind)`

Określa, czy ciąg ma kończyć się wartością. W porównaniu z rozróżnianiem wielkości liter.

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
|:--- |:--- |:--- |:--- |
| stringToSearch |Tak |ciąg |Wartość, która zawiera element do znalezienia. |
| stringToFind |Tak |ciąg |Wartość do znalezienia. |

### <a name="return-value"></a>Wartość zwracana

**Ma wartość true** , jeśli ostatni znak lub znaki ciągu pasują do wartości; w przeciwnym razie **false**.

### <a name="examples"></a>Przykłady

Poniższy [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/startsendswith.json) pokazuje, jak używać funkcji StartsWith i EndsWith:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [],
  "outputs": {
    "startsTrue": {
      "value": "[startsWith('abcdef', 'ab')]",
      "type": "bool"
    },
    "startsCapTrue": {
      "value": "[startsWith('abcdef', 'A')]",
      "type": "bool"
    },
    "startsFalse": {
      "value": "[startsWith('abcdef', 'e')]",
      "type": "bool"
    },
    "endsTrue": {
      "value": "[endsWith('abcdef', 'ef')]",
      "type": "bool"
    },
    "endsCapTrue": {
      "value": "[endsWith('abcdef', 'F')]",
      "type": "bool"
    },
    "endsFalse": {
      "value": "[endsWith('abcdef', 'e')]",
      "type": "bool"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
output startsTrue bool = startsWith('abcdef', 'ab')
output startsCapTrue bool = startsWith('abcdef', 'A')
output startsFalse bool = startsWith('abcdef', 'e')
output endsTrue bool = endsWith('abcdef', 'ef')
output endsCapTrue bool = endsWith('abcdef', 'F')
output endsFalse bool = endsWith('abcdef', 'e')
```

---

Dane wyjściowe z poprzedniego przykładu z wartościami domyślnymi są następujące:

| Nazwa | Typ | Wartość |
| ---- | ---- | ----- |
| startsTrue | Wartość logiczna | Prawda |
| startsCapTrue | Wartość logiczna | Prawda |
| startsFalse | Wartość logiczna | Fałsz |
| endsTrue | Wartość logiczna | Prawda |
| endsCapTrue | Wartość logiczna | Prawda |
| endsFalse | Wartość logiczna | Fałsz |

## <a name="first"></a>pierwszego

`first(arg1)`

Zwraca pierwszy znak ciągu lub pierwszy element tablicy.

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
|:--- |:--- |:--- |:--- |
| arg1 |Tak |Tablica lub ciąg |Wartość do pobrania pierwszego elementu lub znaku. |

### <a name="return-value"></a>Wartość zwracana

Ciąg pierwszego znaku lub typ (ciąg, int, array lub Object) pierwszego elementu w tablicy.

### <a name="examples"></a>Przykłady

Poniższy [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/first.json) pokazuje, jak używać pierwszej funkcji z tablicą i ciągiem.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "arrayToTest": {
      "type": "array",
      "defaultValue": [ "one", "two", "three" ]
    }
  },
  "resources": [
  ],
  "outputs": {
    "arrayOutput": {
      "type": "string",
      "value": "[first(parameters('arrayToTest'))]"
    },
    "stringOutput": {
      "type": "string",
      "value": "[first('One Two Three')]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param arrayToTest array = [
  'one'
  'two'
  'three'
]

output arrayOutput string = first(arrayToTest)
output stringOutput string = first('One Two Three')
```

---

Dane wyjściowe z poprzedniego przykładu z wartościami domyślnymi są następujące:

| Nazwa | Typ | Wartość |
| ---- | ---- | ----- |
| arrayOutput | Ciąg | jeden |
| stringOutput | Ciąg | O |

## <a name="format"></a>format

`format(formatString, arg1, arg2, ...)`

Tworzy sformatowany ciąg z wartości wejściowych.

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
|:--- |:--- |:--- |:--- |
| formatString | Tak | ciąg | Ciąg formatu złożonego. |
| arg1 | Tak | ciąg, liczba całkowita lub wartość logiczna | Wartość, która ma zostać uwzględniona w sformatowanym ciągu. |
| dodatkowe argumenty | Nie | ciąg, liczba całkowita lub wartość logiczna | Dodatkowe wartości do uwzględnienia w sformatowanym ciągu. |

### <a name="remarks"></a>Uwagi

Użyj tej funkcji, aby sformatować ciąg w szablonie. Używa tych samych opcji formatowania co Metoda [System. String. format](/dotnet/api/system.string.format) w programie .NET.

### <a name="examples"></a>Przykłady

Poniższy przykładowy szablon pokazuje, jak używać funkcji format.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "greeting": {
      "type": "string",
      "defaultValue": "Hello"
    },
    "name": {
      "type": "string",
      "defaultValue": "User"
    },
    "numberToFormat": {
      "type": "int",
      "defaultValue": 8175133
    }
  },
  "resources": [
  ],
  "outputs": {
    "formatTest": {
      "type": "string",
      "value": "[format('{0}, {1}. Formatted number: {2:N0}', parameters('greeting'), parameters('name'), parameters('numberToFormat'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param greeting string = 'Hello'
param name string = 'User'
param numberToFormat int = 8175133

output formatTest string = format('{0}, {1}. Formatted number: {2:N0}', greeting, name, numberToFormat)
```

---

Dane wyjściowe z poprzedniego przykładu z wartościami domyślnymi są następujące:

| Nazwa | Typ | Wartość |
| ---- | ---- | ----- |
| formatTest | Ciąg | Witaj, użytkownik. Sformatowana liczba: 8 175 133 |

## <a name="guid"></a>guid

`guid(baseString, ...)`

Tworzy wartość w formacie unikatowego identyfikatora globalnego na podstawie wartości podanych jako parametry.

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
|:--- |:--- |:--- |:--- |
| baseString |Tak |ciąg |Wartość użyta w funkcji skrótu do utworzenia identyfikatora GUID. |
| dodatkowe parametry w razie konieczności |Nie |ciąg |W razie potrzeby można dodać dowolną liczbę ciągów, aby utworzyć wartość określającą poziom unikatowości. |

### <a name="remarks"></a>Uwagi

Ta funkcja jest przydatna, gdy trzeba utworzyć wartość w formacie unikatowego identyfikatora globalnego. Podaj wartości parametrów, które ograniczają zakres unikatowości wyniku. Można określić, czy nazwa jest unikatowa w dół do subskrypcji, grupy zasobów lub wdrożenia.

Zwracana wartość nie jest ciągiem losowym, ale raczej wynikiem funkcji skrótu w parametrach. Zwracana wartość to 36 znaków. Nie jest on globalnie unikatowy. Aby utworzyć nowy identyfikator GUID, który nie jest oparty na tej wartości skrótu parametrów, użyj funkcji [newGuid](#newguid) .

W poniższych przykładach pokazano, jak za pomocą identyfikatora GUID utworzyć unikatową wartość dla często używanych poziomów.

Unikatowy zakres subskrypcji

# <a name="json"></a>[JSON](#tab/json)

```json
"[guid(subscription().subscriptionId)]"
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
guid(subscription().subscriptionId)
```

---

Unikatowy zakres grupy zasobów

# <a name="json"></a>[JSON](#tab/json)

```json
"[guid(resourceGroup().id)]"
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
guid(resourceGroup().id)
```

---

Unikatowy zakres wdrożenia dla grupy zasobów

# <a name="json"></a>[JSON](#tab/json)

```json
"[guid(resourceGroup().id, deployment().name)]"
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
guid(resourceGroup().id, deployment().name)
```

---

### <a name="return-value"></a>Wartość zwracana

Ciąg zawierający 36 znaków w formacie unikatowego identyfikatora globalnego.

### <a name="examples"></a>Przykłady

Następujący [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/guid.json) zwraca wyniki z identyfikatora GUID:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {},
  "resources": [],
  "outputs": {
    "guidPerSubscription": {
      "value": "[guid(subscription().subscriptionId)]",
      "type": "string"
    },
    "guidPerResourceGroup": {
      "value": "[guid(resourceGroup().id)]",
      "type": "string"
    },
    "guidPerDeployment": {
      "value": "[guid(resourceGroup().id, deployment().name)]",
      "type": "string"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
output guidPerSubscription string = guid(subscription().subscriptionId)
output guidPerResourceGroup string = guid(resourceGroup().id)
output guidPerDeployment string = guid(resourceGroup().id, deployment().name)
```

---

## <a name="indexof"></a>indexOf

`indexOf(stringToSearch, stringToFind)`

Zwraca pierwszą pozycję wartości w ciągu. W porównaniu z rozróżnianiem wielkości liter.

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
|:--- |:--- |:--- |:--- |
| stringToSearch |Tak |ciąg |Wartość, która zawiera element do znalezienia. |
| stringToFind |Tak |ciąg |Wartość do znalezienia. |

### <a name="return-value"></a>Wartość zwracana

Liczba całkowita reprezentująca pozycję elementu do znalezienia. Wartość jest zależna od zera. Jeśli element nie zostanie znaleziony, zwracana jest wartość-1.

### <a name="examples"></a>Przykłady

Poniższy [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/indexof.json) pokazuje, jak używać funkcji IndexOf i LastIndexOf:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [],
  "outputs": {
    "firstT": {
      "value": "[indexOf('test', 't')]",
      "type": "int"
    },
    "lastT": {
      "value": "[lastIndexOf('test', 't')]",
      "type": "int"
    },
    "firstString": {
      "value": "[indexOf('abcdef', 'CD')]",
      "type": "int"
    },
    "lastString": {
      "value": "[lastIndexOf('abcdef', 'AB')]",
      "type": "int"
    },
    "notFound": {
      "value": "[indexOf('abcdef', 'z')]",
      "type": "int"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
output firstT int = indexOf('test', 't')
output lastT int = lastIndexOf('test', 't')
output firstString int = indexOf('abcdef', 'CD')
output lastString int = lastIndexOf('abcdef', 'AB')
output notFound int = indexOf('abcdef', 'z')
```

---

Dane wyjściowe z poprzedniego przykładu z wartościami domyślnymi są następujące:

| Nazwa | Typ | Wartość |
| ---- | ---- | ----- |
| pierwsze | int | 0 |
| Ostatnia | int | 3 |
| firstString | int | 2 |
| lastString | int | 0 |
| notFound | int | -1 |

<a id="json"></a>

## <a name="json"></a>json

`json(arg1)`

Konwertuje prawidłowy ciąg JSON na typ danych JSON. Aby uzyskać więcej informacji, zobacz [Funkcja JSON](template-functions-object.md#json).

## <a name="last"></a>ostatni

`last (arg1)`

Zwraca ostatni znak ciągu lub ostatni element tablicy.

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
|:--- |:--- |:--- |:--- |
| arg1 |Tak |Tablica lub ciąg |Wartość do pobrania ostatniego elementu lub znaku. |

### <a name="return-value"></a>Wartość zwracana

Ciąg ostatniego znaku lub typ (ciąg, int, array lub Object) ostatniego elementu w tablicy.

### <a name="examples"></a>Przykłady

Poniższy [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/last.json) pokazuje, jak używać ostatniej funkcji z tablicą i ciągiem.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "arrayToTest": {
      "type": "array",
      "defaultValue": [ "one", "two", "three" ]
    }
  },
  "resources": [
  ],
  "outputs": {
    "arrayOutput": {
      "type": "string",
      "value": "[last(parameters('arrayToTest'))]"
    },
    "stringOutput": {
      "type": "string",
      "value": "[last('One Two Three')]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param arrayToTest array = [
  'one'
  'two'
  'three'
]

output arrayOutput string = last(arrayToTest)
output stringOutput string = last('One Two Three')
```

---

Dane wyjściowe z poprzedniego przykładu z wartościami domyślnymi są następujące:

| Nazwa | Typ | Wartość |
| ---- | ---- | ----- |
| arrayOutput | Ciąg | trzy |
| stringOutput | Ciąg | e |

## <a name="lastindexof"></a>lastIndexOf

`lastIndexOf(stringToSearch, stringToFind)`

Zwraca ostatnią pozycję wartości w ciągu. W porównaniu z rozróżnianiem wielkości liter.

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
|:--- |:--- |:--- |:--- |
| stringToSearch |Tak |ciąg |Wartość, która zawiera element do znalezienia. |
| stringToFind |Tak |ciąg |Wartość do znalezienia. |

### <a name="return-value"></a>Wartość zwracana

Liczba całkowita reprezentująca ostatnią pozycję elementu do znalezienia. Wartość jest zależna od zera. Jeśli element nie zostanie znaleziony, zwracana jest wartość-1.

### <a name="examples"></a>Przykłady

Poniższy [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/indexof.json) pokazuje, jak używać funkcji IndexOf i LastIndexOf:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [],
  "outputs": {
    "firstT": {
      "value": "[indexOf('test', 't')]",
      "type": "int"
    },
    "lastT": {
      "value": "[lastIndexOf('test', 't')]",
      "type": "int"
    },
    "firstString": {
      "value": "[indexOf('abcdef', 'CD')]",
      "type": "int"
    },
    "lastString": {
      "value": "[lastIndexOf('abcdef', 'AB')]",
      "type": "int"
    },
    "notFound": {
      "value": "[indexOf('abcdef', 'z')]",
      "type": "int"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
output firstT int = indexOf('test', 't')
output lastT int = lastIndexOf('test', 't')
output firstString int = indexOf('abcdef', 'CD')
output lastString int = lastIndexOf('abcdef', 'AB')
output notFound int = indexOf('abcdef', 'z')
```

---

Dane wyjściowe z poprzedniego przykładu z wartościami domyślnymi są następujące:

| Nazwa | Typ | Wartość |
| ---- | ---- | ----- |
| pierwsze | int | 0 |
| Ostatnia | int | 3 |
| firstString | int | 2 |
| lastString | int | 0 |
| notFound | int | -1 |

## <a name="length"></a>length

`length(string)`

Zwraca liczbę znaków w ciągu, elementów w tablicy lub we właściwościach poziomu głównego w obiekcie.

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
|:--- |:--- |:--- |:--- |
| arg1 |Tak |Tablica, ciąg lub obiekt |Tablica, która ma być używana do pobierania liczby elementów, ciągu, który ma być używany do pobierania liczby znaków lub obiektu, który ma być używany do pobierania liczby właściwości na poziomie głównym. |

### <a name="return-value"></a>Wartość zwracana

Int.

### <a name="examples"></a>Przykłady

Poniższy [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/length.json) pokazuje, jak używać długości z tablicą i ciągiem:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "arrayToTest": {
      "type": "array",
      "defaultValue": [
        "one",
        "two",
        "three"
      ]
    },
    "stringToTest": {
      "type": "string",
      "defaultValue": "One Two Three"
    },
    "objectToTest": {
      "type": "object",
      "defaultValue": {
        "propA": "one",
        "propB": "two",
        "propC": "three",
        "propD": {
          "propD-1": "sub",
          "propD-2": "sub"
        }
      }
    }
  },
  "resources": [],
  "outputs": {
    "arrayLength": {
      "type": "int",
      "value": "[length(parameters('arrayToTest'))]"
    },
    "stringLength": {
      "type": "int",
      "value": "[length(parameters('stringToTest'))]"
    },
    "objectLength": {
      "type": "int",
      "value": "[length(parameters('objectToTest'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param arrayToTest array = [
  'one'
  'two'
  'three'
]
param stringToTest string = 'One Two Three'
param objectToTest object = {
  'propA': 'one'
  'propB': 'two'
  'propC': 'three'
  'propD': {
    'propD-1': 'sub'
    'propD-2': 'sub'
  }
}

output arrayLength int = length(arrayToTest)
output stringLength int = length(stringToTest)
output objectLength int = length(objectToTest)
```

---

Dane wyjściowe z poprzedniego przykładu z wartościami domyślnymi są następujące:

| Nazwa | Typ | Wartość |
| ---- | ---- | ----- |
| arrayLength | int | 3 |
| stringLength | int | 13 |
| objectLength | int | 4 |

## <a name="newguid"></a>newGuid

`newGuid()`

Zwraca wartość w formacie unikatowego identyfikatora globalnego. **Tej funkcji można użyć tylko w wartości domyślnej dla parametru.**

### <a name="remarks"></a>Uwagi

Tej funkcji można użyć tylko w wyrażeniu dla wartości domyślnej parametru. Użycie tej funkcji w dowolnym miejscu w szablonie powoduje zwrócenie błędu. Funkcja nie jest dozwolona w innych częściach szablonu, ponieważ zwraca inną wartość za każdym razem, gdy jest wywoływana. Wdrożenie tego samego szablonu z tymi samymi parametrami nie da niezawodnego wygenerowania tych samych wyników.

Funkcja newGuid różni się od funkcji [GUID](#guid) , ponieważ nie przyjmuje żadnych parametrów. W przypadku wywołania identyfikatora GUID z tym samym parametrem zwraca ten sam identyfikator za każdym razem. Użyj identyfikatora GUID, gdy zachodzi potrzeba niezawodnego generowania tego samego identyfikatora GUID dla określonego środowiska. Użyj newGuid, gdy w każdym momencie potrzebny jest inny identyfikator, taki jak wdrażanie zasobów w środowisku testowym.

Funkcja newGuid używa [struktury GUID](/dotnet/api/system.guid) w .NET Framework do generowania unikatowego identyfikatora globalnego.

Jeśli zostanie użyta [opcja ponownego wdrożenia wcześniejszego pomyślnego wdrożenia](rollback-on-error.md), a wcześniejsze wdrożenie zawiera parametr, który używa newGuid, parametr nie zostanie ponownie oceniony. Zamiast tego wartość parametru z wcześniejszego wdrożenia zostanie automatycznie ponownie użyta w ramach wdrożenia wycofywania.

W środowisku testowym może być konieczne wielokrotne wdrożenie zasobów, które są tylko na żywo przez krótki czas. Zamiast konstruowania unikatowych nazw, można użyć newGuid z [uniqueString](#uniquestring) do tworzenia unikatowych nazw.

Należy zachować ostrożność wdrażania szablonu, który opiera się na funkcji newGuid dla wartości domyślnej. Po ponownym wdrożeniu i niepodaniu wartości parametru funkcja jest ponownie Szacowana. Jeśli chcesz zaktualizować istniejący zasób zamiast tworzyć nowe, przekaż wartość parametru z wcześniejszego wdrożenia.

### <a name="return-value"></a>Wartość zwracana

Ciąg zawierający 36 znaków w formacie unikatowego identyfikatora globalnego.

### <a name="examples"></a>Przykłady

Poniższy przykładowy szablon przedstawia parametr z nowym identyfikatorem.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "guidValue": {
      "type": "string",
      "defaultValue": "[newGuid()]"
    }
  },
  "resources": [
  ],
  "outputs": {
    "guidOutput": {
      "type": "string",
      "value": "[parameters('guidValue')]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param guidValue string = newGuid()

output guidOutput string = guidValue
```

---

Dane wyjściowe z powyższego przykładu różnią się w zależności od wdrożenia, ale będą podobne do następujących:

| Nazwa | Typ | Wartość |
| ---- | ---- | ----- |
| guidOutput | ciąg | b76a51fc-bd72-4a77-b9a2-3c29e7d2e551 |

Poniższy przykład używa funkcji newGuid, aby utworzyć unikatową nazwę konta magazynu. Ten szablon może posłużyć do środowiska testowego, w którym konto magazynu istnieje przez krótki czas i nie jest wdrażane ponownie.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "guidValue": {
      "type": "string",
      "defaultValue": "[newGuid()]"
    }
  },
  "variables": {
    "storageName": "[concat('storage', uniqueString(parameters('guidValue')))]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('storageName')]",
      "location": "West US",
      "apiVersion": "2018-07-01",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "StorageV2",
      "properties": {}
    }
  ],
  "outputs": {
    "nameOutput": {
      "type": "string",
      "value": "[variables('storageName')]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param guidValue string = newGuid()

var storageName = 'storage${uniqueString(guidValue)}'

resource myStorage 'Microsoft.Storage/storageAccounts@2018-07-01' = {
  name: storageName
  location: 'West US'
  sku: {
    name: 'Standard_LRS'
  }
  kind: 'StorageV2'
  properties: {}
}

output nameOutput string = storageName
```

---

Dane wyjściowe z powyższego przykładu różnią się w zależności od wdrożenia, ale będą podobne do następujących:

| Nazwa | Typ | Wartość |
| ---- | ---- | ----- |
| nameOutput | ciąg | storagenziwvyru7uxie |

## <a name="padleft"></a>padLeft

`padLeft(valueToPad, totalLength, paddingCharacter)`

Zwraca ciąg wyrównany do prawej poprzez dodanie znaków po lewej stronie do osiągnięcia całkowitej określonej długości.

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
|:--- |:--- |:--- |:--- |
| valueToPad |Tak |ciąg lub int |Wartość na wyrównanie do prawej. |
| totalLength |Tak |int |Całkowita liczba znaków w zwracanym ciągu. |
| paddingCharacter |Nie |pojedynczy znak |Znak, który ma zostać użyty do lewej krawędzi do momentu osiągnięcia całkowitej długości. Wartość domyślna to spacja. |

Jeśli oryginalny ciąg jest dłuższy niż liczba znaków do zablokowania, nie są dodawane żadne znaki.

### <a name="return-value"></a>Wartość zwracana

Ciąg z co najmniej liczbą określonych znaków.

### <a name="examples"></a>Przykłady

Poniższy [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/padleft.json) pokazuje, jak uzupełniać wartość parametru dostarczoną przez użytkownika przez dodanie znaku zerowego do momentu osiągnięcia całkowitej liczby znaków.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "testString": {
      "type": "string",
      "defaultValue": "123"
    }
  },
  "resources": [],
  "outputs": {
    "stringOutput": {
      "type": "string",
      "value": "[padLeft(parameters('testString'),10,'0')]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param testString string = '123'

output stringOutput string = padLeft(testString, 10, '0')
```

---

Dane wyjściowe z poprzedniego przykładu z wartościami domyślnymi są następujące:

| Nazwa | Typ | Wartość |
| ---- | ---- | ----- |
| stringOutput | Ciąg | 0000000123 |

## <a name="replace"></a>Zastąp

`replace(originalString, oldString, newString)`

Zwraca nowy ciąg ze wszystkimi wystąpieniami jednego ciągu zamienionego przez inny ciąg.

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
|:--- |:--- |:--- |:--- |
| originalString |Tak |ciąg |Wartość, która ma wszystkie wystąpienia jednego ciągu zamienionego przez inny ciąg. |
| Staryciąg |Tak |ciąg |Ciąg, który ma zostać usunięty z oryginalnego ciągu. |
| Nowyciąg |Tak |ciąg |Ciąg, który ma zostać dodany zamiast usuniętego ciągu. |

### <a name="return-value"></a>Wartość zwracana

Ciąg z zamienionymi znakami.

### <a name="examples"></a>Przykłady

Poniższy [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/replace.json) pokazuje, jak usunąć wszystkie kreski z ciągu dostarczonego przez użytkownika oraz jak zastąpić część ciągu innym ciągiem.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "testString": {
      "type": "string",
      "defaultValue": "123-123-1234"
    }
  },
  "resources": [],
  "outputs": {
    "firstOutput": {
      "type": "string",
      "value": "[replace(parameters('testString'),'-', '')]"
    },
    "secondOutput": {
      "type": "string",
      "value": "[replace(parameters('testString'),'1234', 'xxxx')]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param testString string = '123-123-1234'

output firstOutput string = replace(testString, '-', '')
output secondOutput string = replace(testString, '1234', 'xxxx')
```

---

Dane wyjściowe z poprzedniego przykładu z wartościami domyślnymi są następujące:

| Nazwa | Typ | Wartość |
| ---- | ---- | ----- |
| firstOutput | Ciąg | 1231231234 |
| secondOutput | Ciąg | 123-123 — xxxx |

## <a name="skip"></a>Pomiń

`skip(originalValue, numberToSkip)`

Zwraca ciąg zawierający wszystkie znaki po określonej liczbie znaków lub tablicę ze wszystkimi elementami po określonej liczbie elementów.

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
|:--- |:--- |:--- |:--- |
| originalValue |Tak |Tablica lub ciąg |Tablica lub ciąg, który ma być używany do pomijania. |
| numberToSkip |Tak |int |Liczba elementów lub znaków do pominięcia. Jeśli ta wartość jest równa 0 lub mniejsza, zwracane są wszystkie elementy lub znaki w wartości. Jeśli jest większa niż długość tablicy lub ciągu, zwracana jest pusta tablica lub ciąg. |

### <a name="return-value"></a>Wartość zwracana

Tablica lub ciąg.

### <a name="examples"></a>Przykłady

Poniższy [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/skip.json) pomija określoną liczbę elementów w tablicy i określoną liczbę znaków w ciągu.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "testArray": {
      "type": "array",
      "defaultValue": [
        "one",
        "two",
        "three"
      ]
    },
    "elementsToSkip": {
      "type": "int",
      "defaultValue": 2
    },
    "testString": {
      "type": "string",
      "defaultValue": "one two three"
    },
    "charactersToSkip": {
      "type": "int",
      "defaultValue": 4
    }
  },
  "resources": [],
  "outputs": {
    "arrayOutput": {
      "type": "array",
      "value": "[skip(parameters('testArray'),parameters('elementsToSkip'))]"
    },
    "stringOutput": {
      "type": "string",
      "value": "[skip(parameters('testString'),parameters('charactersToSkip'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param testArray array = [
  'one'
  'two'
  'three'
]
param elementsToSkip int = 2
param testString string = 'one two three'
param charactersToSkip int = 4

output arrayOutput array = skip(testArray, elementsToSkip)
output stringOutput string = skip(testString, charactersToSkip)
```

---

Dane wyjściowe z poprzedniego przykładu z wartościami domyślnymi są następujące:

| Nazwa | Typ | Wartość |
| ---- | ---- | ----- |
| arrayOutput | Tablica | ["trzy"] |
| stringOutput | Ciąg | 2 3 |

## <a name="split"></a>split

`split(inputString, delimiter)`

Zwraca tablicę ciągów, która zawiera podciągi ciągu wejściowego, które są rozdzielane przez określone ograniczniki.

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
|:--- |:--- |:--- |:--- |
| inputString |Tak |ciąg |Ciąg do podzielenia. |
| ogranicznik |Tak |ciąg lub tablica ciągów |Ogranicznik używany do dzielenia ciągu. |

### <a name="return-value"></a>Wartość zwracana

Tablica ciągów.

### <a name="examples"></a>Przykłady

Poniższy [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/split.json) dzieli ciąg wejściowy na przecinek i z przecinkiem lub średnikiem.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "firstString": {
      "type": "string",
      "defaultValue": "one,two,three"
    },
    "secondString": {
      "type": "string",
      "defaultValue": "one;two,three"
    }
  },
  "variables": {
    "delimiters": [ ",", ";" ]
  },
  "resources": [],
  "outputs": {
    "firstOutput": {
      "type": "array",
      "value": "[split(parameters('firstString'),',')]"
    },
    "secondOutput": {
      "type": "array",
      "value": "[split(parameters('secondString'),variables('delimiters'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param firstString string = 'one,two,three'
param secondString string = 'one;two,three'

var delimiters = [
  ','
  ';'
]

output firstOutput array = split(firstString, ',')
output secondOutput array = split(secondString, delimiters)
```

---

Dane wyjściowe z poprzedniego przykładu z wartościami domyślnymi są następujące:

| Nazwa | Typ | Wartość |
| ---- | ---- | ----- |
| firstOutput | Tablica | ["jeden", "dwa", "trzy"] |
| secondOutput | Tablica | ["jeden", "dwa", "trzy"] |

## <a name="startswith"></a>startsWith

`startsWith(stringToSearch, stringToFind)`

Określa, czy ciąg rozpoczyna się od wartości. W porównaniu z rozróżnianiem wielkości liter.

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
|:--- |:--- |:--- |:--- |
| stringToSearch |Tak |ciąg |Wartość, która zawiera element do znalezienia. |
| stringToFind |Tak |ciąg |Wartość do znalezienia. |

### <a name="return-value"></a>Wartość zwracana

**Ma wartość true** , jeśli pierwszy znak lub znaki ciągu pasują do wartości; w przeciwnym razie **false**.

### <a name="examples"></a>Przykłady

Poniższy [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/startsendswith.json) pokazuje, jak używać funkcji StartsWith i EndsWith:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [],
  "outputs": {
    "startsTrue": {
      "value": "[startsWith('abcdef', 'ab')]",
      "type": "bool"
    },
    "startsCapTrue": {
      "value": "[startsWith('abcdef', 'A')]",
      "type": "bool"
    },
    "startsFalse": {
      "value": "[startsWith('abcdef', 'e')]",
      "type": "bool"
    },
    "endsTrue": {
      "value": "[endsWith('abcdef', 'ef')]",
      "type": "bool"
    },
    "endsCapTrue": {
      "value": "[endsWith('abcdef', 'F')]",
      "type": "bool"
    },
    "endsFalse": {
      "value": "[endsWith('abcdef', 'e')]",
      "type": "bool"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
output startsTrue bool = startsWith('abcdef', 'ab')
output startsCapTrue bool = startsWith('abcdef', 'A')
output startsFalse bool = startsWith('abcdef', 'e')
output endsTrue bool = endsWith('abcdef', 'ef')
output endsCapTrue bool = endsWith('abcdef', 'F')
output endsFalse bool = endsWith('abcdef', 'e')
```

---

Dane wyjściowe z poprzedniego przykładu z wartościami domyślnymi są następujące:

| Nazwa | Typ | Wartość |
| ---- | ---- | ----- |
| startsTrue | Wartość logiczna | Prawda |
| startsCapTrue | Wartość logiczna | Prawda |
| startsFalse | Wartość logiczna | Fałsz |
| endsTrue | Wartość logiczna | Prawda |
| endsCapTrue | Wartość logiczna | Prawda |
| endsFalse | Wartość logiczna | False |

## <a name="string"></a>ciąg

`string(valueToConvert)`

Konwertuje określoną wartość na ciąg.

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
|:--- |:--- |:--- |:--- |
| valueToConvert |Tak | Dowolne |Wartość do przekonwertowania na ciąg. Każdy typ wartości może być konwertowany, w tym obiektów i tablic. |

### <a name="return-value"></a>Wartość zwracana

Ciąg przekonwertowanej wartości.

### <a name="examples"></a>Przykłady

Poniższy [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/string.json) pokazuje, jak konwertować różne typy wartości na ciągi:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "testObject": {
      "type": "object",
      "defaultValue": {
        "valueA": 10,
        "valueB": "Example Text"
      }
    },
    "testArray": {
      "type": "array",
      "defaultValue": [
        "a",
        "b",
        "c"
      ]
    },
    "testInt": {
      "type": "int",
      "defaultValue": 5
    }
  },
  "resources": [],
  "outputs": {
    "objectOutput": {
      "type": "string",
      "value": "[string(parameters('testObject'))]"
    },
    "arrayOutput": {
      "type": "string",
      "value": "[string(parameters('testArray'))]"
    },
    "intOutput": {
      "type": "string",
      "value": "[string(parameters('testInt'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param testObject object = {
  'valueA': 10
  'valueB': 'Example Text'
}
param testArray array = [
  'a'
  'b'
  'c'
]
param testInt int = 5

output objectOutput string = string(testObject)
output arrayOutput string = string(testArray)
output intOutput string = string(testInt)
```

---

Dane wyjściowe z poprzedniego przykładu z wartościami domyślnymi są następujące:

| Nazwa | Typ | Wartość |
| ---- | ---- | ----- |
| objectOutput | Ciąg | {"valuea": 10, "Wartośćb": "przykładowy tekst"} |
| arrayOutput | Ciąg | ["a", "b", "c"] |
| intOutput | Ciąg | 5 |

## <a name="substring"></a>podciąg

`substring(stringToParse, startIndex, length)`

Zwraca podciąg, który zaczyna się od określonej pozycji znaku i zawiera określoną liczbę znaków.

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
|:--- |:--- |:--- |:--- |
| stringToParse |Tak |ciąg |Oryginalny ciąg, z którego jest wyodrębniany podciąg. |
| Indeks |Nie |int |Początkowa pozycja znaku (liczony od zera) dla podciągu. |
| length |Nie |int |Liczba znaków podciągu. Musi odwoływać się do lokalizacji w ciągu. Musi być równa zero lub większa. |

### <a name="return-value"></a>Wartość zwracana

Podciąg. Lub pusty ciąg, jeśli długość wynosi zero.

### <a name="remarks"></a>Uwagi

Funkcja kończy się niepowodzeniem, gdy podciąg wykracza poza koniec ciągu lub gdy długość jest mniejsza od zera. Poniższy przykład kończy się niepowodzeniem z błędem "parametry indeksu i długości muszą odwoływać się do lokalizacji w ciągu. Parametr indeksu: "0", parametr długości: "11", długość parametru ciągu: "10". ".

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "inputString": {
    "type": "string",
    "value": "1234567890"
  }
}, "variables": {
  "prefix": "[substring(parameters('inputString'), 0, 11)]"
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param inputString string = '1234567890'

var prefix = substring(inputString, 0, 11)
```

---

### <a name="examples"></a>Przykłady

Poniższy [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/substring.json) wyodrębnia podciąg z parametru.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "testString": {
      "type": "string",
      "defaultValue": "one two three"
    }
  },
  "resources": [],
  "outputs": {
    "substringOutput": {
      "value": "[substring(parameters('testString'), 4, 3)]",
      "type": "string"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param testString string = 'one two three'
output substringOutput string = substring(testString, 4, 3)
```

---

Dane wyjściowe z poprzedniego przykładu z wartościami domyślnymi są następujące:

| Nazwa | Typ | Wartość |
| ---- | ---- | ----- |
| substringOutput | Ciąg | dwa |

## <a name="take"></a>take (pobierz)

`take(originalValue, numberToTake)`

Zwraca ciąg zawierający określoną liczbę znaków od początku ciągu lub tablicę o określonej liczbie elementów od początku tablicy.

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
|:--- |:--- |:--- |:--- |
| originalValue |Tak |Tablica lub ciąg |Tablica lub ciąg, z którego mają zostać przebrane elementy. |
| numberToTake |Tak |int |Liczba elementów lub znaków do wykonania. Jeśli ta wartość jest równa 0 lub mniejsza, zwracana jest pusta tablica lub ciąg. Jeśli jest większa niż długość danej tablicy lub ciągu, zwracane są wszystkie elementy w tablicy lub ciągu. |

### <a name="return-value"></a>Wartość zwracana

Tablica lub ciąg.

### <a name="examples"></a>Przykłady

Poniższy [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/take.json) przyjmuje określoną liczbę elementów z tablicy i znaki z ciągu.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "testArray": {
      "type": "array",
      "defaultValue": [
        "one",
        "two",
        "three"
      ]
    },
    "elementsToTake": {
      "type": "int",
      "defaultValue": 2
    },
    "testString": {
      "type": "string",
      "defaultValue": "one two three"
    },
    "charactersToTake": {
      "type": "int",
      "defaultValue": 2
    }
  },
  "resources": [],
  "outputs": {
    "arrayOutput": {
      "type": "array",
      "value": "[take(parameters('testArray'),parameters('elementsToTake'))]"
    },
    "stringOutput": {
      "type": "string",
      "value": "[take(parameters('testString'),parameters('charactersToTake'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param testArray array = [
  'one'
  'two'
  'three'
]
param elementsToSkip int = 2
param testString string = 'one two three'
param charactersToSkip int = 2

output arrayOutput array = take(testArray, elementsToSkip)
output stringOutput string = take(testString, charactersToSkip)
```

---

Dane wyjściowe z poprzedniego przykładu z wartościami domyślnymi są następujące:

| Nazwa | Typ | Wartość |
| ---- | ---- | ----- |
| arrayOutput | Tablica | ["jeden", "dwa"] |
| stringOutput | Ciąg | on |

## <a name="tolower"></a>toLower

`toLower(stringToChange)`

Konwertuje określony ciąg na małe litery.

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
|:--- |:--- |:--- |:--- |
| stringToChange |Tak |ciąg |Wartość do przekonwertowania na małe litery. |

### <a name="return-value"></a>Wartość zwracana

Ciąg przekonwertowany na małe litery.

### <a name="examples"></a>Przykłady

Poniższy [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/tolower.json) konwertuje wartość parametru na małe litery i wielkie litery.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "testString": {
      "type": "string",
      "defaultValue": "One Two Three"
    }
  },
  "resources": [],
  "outputs": {
    "toLowerOutput": {
      "value": "[toLower(parameters('testString'))]",
      "type": "string"
    },
    "toUpperOutput": {
      "type": "string",
      "value": "[toUpper(parameters('testString'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param testString string = 'One Two Three'

output toLowerOutput string = toLower(testString)
output toUpperOutput string = toUpper(testString)
```

---

Dane wyjściowe z poprzedniego przykładu z wartościami domyślnymi są następujące:

| Nazwa | Typ | Wartość |
| ---- | ---- | ----- |
| toLowerOutput | Ciąg | Raz dwa trzy |
| toUpperOutput | Ciąg | Raz dwa trzy |

## <a name="toupper"></a>toUpper

`toUpper(stringToChange)`

Konwertuje określony ciąg na wielkie litery.

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
|:--- |:--- |:--- |:--- |
| stringToChange |Tak |ciąg |Wartość do przekonwertowania na wielkie litery. |

### <a name="return-value"></a>Wartość zwracana

Ciąg przekonwertowany na wielkie litery.

### <a name="examples"></a>Przykłady

Poniższy [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/tolower.json) konwertuje wartość parametru na małe litery i wielkie litery.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "testString": {
      "type": "string",
      "defaultValue": "One Two Three"
    }
  },
  "resources": [],
  "outputs": {
    "toLowerOutput": {
      "value": "[toLower(parameters('testString'))]",
      "type": "string"
    },
    "toUpperOutput": {
      "type": "string",
      "value": "[toUpper(parameters('testString'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param testString string = 'One Two Three'

output toLowerOutput string = toLower(testString)
output toUpperOutput string = toUpper(testString)
```

---

Dane wyjściowe z poprzedniego przykładu z wartościami domyślnymi są następujące:

| Nazwa | Typ | Wartość |
| ---- | ---- | ----- |
| toLowerOutput | Ciąg | Raz dwa trzy |
| toUpperOutput | Ciąg | Raz dwa trzy |

## <a name="trim"></a>Trim

`trim (stringToTrim)`

Usuwa wszystkie spacje wiodące i końcowe z określonego ciągu.

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
|:--- |:--- |:--- |:--- |
| stringToTrim |Tak |ciąg |Wartość do przycięcia. |

### <a name="return-value"></a>Wartość zwracana

Ciąg bez znaków spacji wiodących i końcowych.

### <a name="examples"></a>Przykłady

Poniższy [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/trim.json) przycina znaki odstępu z parametru.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "testString": {
      "type": "string",
      "defaultValue": "    one two three   "
    }
  },
  "resources": [],
  "outputs": {
    "return": {
      "type": "string",
      "value": "[trim(parameters('testString'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param testString string = '    one two three   '

output return string = trim(testString)
```

---

Dane wyjściowe z poprzedniego przykładu z wartościami domyślnymi są następujące:

| Nazwa | Typ | Wartość |
| ---- | ---- | ----- |
| return | Ciąg | Raz dwa trzy |

## <a name="uniquestring"></a>uniqueString

`uniqueString (baseString, ...)`

Tworzy deterministyczny ciąg skrótu na podstawie wartości podanych jako parametry.

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
|:--- |:--- |:--- |:--- |
| baseString |Tak |ciąg |Wartość użyta w funkcji skrótu do utworzenia unikatowego ciągu. |
| dodatkowe parametry w razie konieczności |Nie |ciąg |W razie potrzeby można dodać dowolną liczbę ciągów, aby utworzyć wartość określającą poziom unikatowości. |

### <a name="remarks"></a>Uwagi

Ta funkcja jest przydatna, gdy konieczne jest utworzenie unikatowej nazwy dla zasobu. Podaj wartości parametrów, które ograniczają zakres unikatowości wyniku. Można określić, czy nazwa jest unikatowa w dół do subskrypcji, grupy zasobów lub wdrożenia.

Zwracana wartość nie jest ciągiem losowym, ale raczej wynikiem funkcji skrótu. Zwrócona wartość to 13 znaków. Nie jest on globalnie unikatowy. Możesz chcieć połączyć wartość z prefiksem z konwencji nazewnictwa, aby utworzyć zrozumiałą nazwę. Poniższy przykład pokazuje format zwracanej wartości. Wartość rzeczywista różni się w zależności od podanych parametrów.

`tcvhiyu5h2o5o`

W poniższych przykładach pokazano, jak za pomocą uniqueString utworzyć unikatową wartość dla często używanych poziomów.

Unikatowy zakres subskrypcji

# <a name="json"></a>[JSON](#tab/json)

```json
"[uniqueString(subscription().subscriptionId)]"
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
uniqueString(subscription().subscriptionId)
```

---

Unikatowy zakres grupy zasobów

# <a name="json"></a>[JSON](#tab/json)

```json
"[uniqueString(resourceGroup().id)]"
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
uniqueString(resourceGroup().id)
```

---

Unikatowy zakres wdrożenia dla grupy zasobów

# <a name="json"></a>[JSON](#tab/json)

```json
"[uniqueString(resourceGroup().id, deployment().name)]"
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
uniqueString(resourceGroup().id, deployment().name)
```

---

Poniższy przykład pokazuje, jak utworzyć unikatową nazwę konta magazynu na podstawie grupy zasobów. W grupie zasobów nazwa nie jest unikatowa, jeśli skonstruowano taki sam sposób.

# <a name="json"></a>[JSON](#tab/json)

```json
"resources": [{
  "name": "[concat('storage', uniqueString(resourceGroup().id))]",
  "type": "Microsoft.Storage/storageAccounts",
  ...
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
resource mystorage 'Microsoft.Storage/storageAccounts@@2018-07-01' = {
  name: 'storage${uniqueString(resourceGroup().id)}'
  ...
}
```

---

Jeśli musisz utworzyć nową unikatową nazwę przy każdym wdrożeniu szablonu i nie planujesz zaktualizować zasobu, możesz użyć funkcji [UtcNow](template-functions-date.md#utcnow) z uniqueString. Tego podejścia można użyć w środowisku testowym. Aby zapoznać się z przykładem, zobacz [UtcNow](template-functions-date.md#utcnow).

### <a name="return-value"></a>Wartość zwracana

Ciąg zawierający 13 znaków.

### <a name="examples"></a>Przykłady

Poniższy [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/uniquestring.json) zwraca wyniki z uniquestring:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [],
  "outputs": {
    "uniqueRG": {
      "value": "[uniqueString(resourceGroup().id)]",
      "type": "string"
    },
    "uniqueDeploy": {
      "value": "[uniqueString(resourceGroup().id, deployment().name)]",
      "type": "string"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
output uniqueRG string = uniqueString(resourceGroup().id)
output uniqueDeploy string = uniqueString(resourceGroup().id, deployment().name)
```

---

## <a name="uri"></a>adresu

`uri (baseUri, relativeUri)`

Tworzy bezwzględny identyfikator URI przez połączenie baseUri i ciągu relativeUri.

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
|:--- |:--- |:--- |:--- |
| baseUri |Tak |ciąg |Podstawowy ciąg identyfikatora URI. Pamiętaj, aby obserwować zachowanie dotyczące obsługi końcowego ukośnika ('/'), zgodnie z opisem poniżej tej tabeli.  |
| relativeUri |Tak |ciąg |Względny ciąg identyfikatora URI, który ma zostać dodany do podstawowego ciągu identyfikatora URI. |

* Jeśli **baseUri** kończy się na końcu ukośnika, wynik jest po prostu **baseUri** , a następnie **relativeUri**.

* Jeśli **baseUri** nie kończy się na końcu ukośnika, występuje jedna z dwóch elementów.

   * Jeśli **baseUri** nie ma ukośników (od "//" blisko przodu), wynik jest po prostu **baseUri** , a następnie **relativeUri**.

   * Jeśli **baseUri** ma kilka ukośników, ale nie kończy się ukośnikiem, wszystko z ostatniego ukośnika jest usuwane z **baseUri** , a wynik jest **baseUri** , a następnie **relativeUri**.

Oto kilka przykładów:

```
uri('http://contoso.org/firstpath', 'myscript.sh') -> http://contoso.org/myscript.sh
uri('http://contoso.org/firstpath/', 'myscript.sh') -> http://contoso.org/firstpath/myscript.sh
uri('http://contoso.org/firstpath/azuredeploy.json', 'myscript.sh') -> http://contoso.org/firstpath/myscript.sh
uri('http://contoso.org/firstpath/azuredeploy.json/', 'myscript.sh') -> http://contoso.org/firstpath/azuredeploy.json/myscript.sh
```
Aby uzyskać szczegółowe informacje, parametry **baseUri** i **relativeUri** są rozpoznawane zgodnie z opisem w [dokumencie RFC 3986, sekcja 5](https://tools.ietf.org/html/rfc3986#section-5).

### <a name="return-value"></a>Wartość zwracana

Ciąg reprezentujący bezwzględny identyfikator URI dla podstawowych i względnych wartości.

### <a name="examples"></a>Przykłady

Poniższy przykład pokazuje, jak utworzyć łącze do zagnieżdżonego szablonu na podstawie wartości szablonu nadrzędnego.

# <a name="json"></a>[JSON](#tab/json)

```json
"templateLink": "[uri(deployment().properties.templateLink.uri, 'nested/azuredeploy.json')]"
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
templateLink: uri(deployment().properties.templateLink.uri, 'nested/azuredeploy.json')
```

---

Poniższy [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/uri.json) pokazuje, jak używać identyfikatorów URI, UriComponent i uriComponentToString:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "variables": {
    "uriFormat": "[uri('http://contoso.com/resources/', 'nested/azuredeploy.json')]",
    "uriEncoded": "[uriComponent(variables('uriFormat'))]"
  },
  "resources": [
  ],
  "outputs": {
    "uriOutput": {
      "type": "string",
      "value": "[variables('uriFormat')]"
    },
    "componentOutput": {
      "type": "string",
      "value": "[variables('uriEncoded')]"
    },
    "toStringOutput": {
      "type": "string",
      "value": "[uriComponentToString(variables('uriEncoded'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
var uriFormat = uri('http://contoso.com/resources/', 'nested/azuredeploy.json')
var uriEncoded = uriComponent(uriFormat)

output uriOutput string = uriFormat
output componentOutput string = uriEncoded
output toStringOutput string = uriComponentToString(uriEncoded)
```

---

Dane wyjściowe z poprzedniego przykładu z wartościami domyślnymi są następujące:

| Nazwa | Typ | Wartość |
| ---- | ---- | ----- |
| uriOutput | Ciąg | `http://contoso.com/resources/nested/azuredeploy.json` |
| componentOutput | Ciąg | `http%3A%2F%2Fcontoso.com%2Fresources%2Fnested%2Fazuredeploy.json` |
| toStringOutput | Ciąg | `http://contoso.com/resources/nested/azuredeploy.json` |

## <a name="uricomponent"></a>uriComponent

`uricomponent(stringToEncode)`

Koduje identyfikator URI.

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
|:--- |:--- |:--- |:--- |
| stringToEncode |Tak |ciąg |Wartość do kodowania. |

### <a name="return-value"></a>Wartość zwracana

Ciąg wartości zakodowanej URI.

### <a name="examples"></a>Przykłady

Poniższy [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/uri.json) pokazuje, jak używać identyfikatorów URI, UriComponent i uriComponentToString:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "variables": {
    "uriFormat": "[uri('http://contoso.com/resources/', 'nested/azuredeploy.json')]",
    "uriEncoded": "[uriComponent(variables('uriFormat'))]"
  },
  "resources": [
  ],
  "outputs": {
    "uriOutput": {
      "type": "string",
      "value": "[variables('uriFormat')]"
    },
    "componentOutput": {
      "type": "string",
      "value": "[variables('uriEncoded')]"
    },
    "toStringOutput": {
      "type": "string",
      "value": "[uriComponentToString(variables('uriEncoded'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
var uriFormat = uri('http://contoso.com/resources/', 'nested/azuredeploy.json')
var uriEncoded = uriComponent(uriFormat)

output uriOutput string = uriFormat
output componentOutput string = uriEncoded
output toStringOutput string = uriComponentToString(uriEncoded)
```

---

Dane wyjściowe z poprzedniego przykładu z wartościami domyślnymi są następujące:

| Nazwa | Typ | Wartość |
| ---- | ---- | ----- |
| uriOutput | Ciąg | `http://contoso.com/resources/nested/azuredeploy.json` |
| componentOutput | Ciąg | `http%3A%2F%2Fcontoso.com%2Fresources%2Fnested%2Fazuredeploy.json` |
| toStringOutput | Ciąg | `http://contoso.com/resources/nested/azuredeploy.json` |

## <a name="uricomponenttostring"></a>uriComponentToString

`uriComponentToString(uriEncodedString)`

Zwraca ciąg wartości zakodowanej URI.

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
|:--- |:--- |:--- |:--- |
| uriEncodedString |Tak |ciąg |Zakodowana wartość identyfikatora URI w celu przekonwertowania na ciąg. |

### <a name="return-value"></a>Wartość zwracana

Zdekodowany ciąg zakodowanej wartości identyfikatora URI.

### <a name="examples"></a>Przykłady

Poniższy [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/uri.json) pokazuje, jak używać identyfikatorów URI, UriComponent i uriComponentToString:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "variables": {
    "uriFormat": "[uri('http://contoso.com/resources/', 'nested/azuredeploy.json')]",
    "uriEncoded": "[uriComponent(variables('uriFormat'))]"
  },
  "resources": [
  ],
  "outputs": {
    "uriOutput": {
      "type": "string",
      "value": "[variables('uriFormat')]"
    },
    "componentOutput": {
      "type": "string",
      "value": "[variables('uriEncoded')]"
    },
    "toStringOutput": {
      "type": "string",
      "value": "[uriComponentToString(variables('uriEncoded'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
var uriFormat = uri('http://contoso.com/resources/', 'nested/azuredeploy.json')
var uriEncoded = uriComponent(uriFormat)

output uriOutput string = uriFormat
output componentOutput string = uriEncoded
output toStringOutput string = uriComponentToString(uriEncoded)
```

---

Dane wyjściowe z poprzedniego przykładu z wartościami domyślnymi są następujące:

| Nazwa | Typ | Wartość |
| ---- | ---- | ----- |
| uriOutput | Ciąg | `http://contoso.com/resources/nested/azuredeploy.json` |
| componentOutput | Ciąg | `http%3A%2F%2Fcontoso.com%2Fresources%2Fnested%2Fazuredeploy.json` |
| toStringOutput | Ciąg | `http://contoso.com/resources/nested/azuredeploy.json` |

## <a name="next-steps"></a>Następne kroki

* Opis sekcji w szablonie ARM można znaleźć [w temacie Omówienie struktury i składni szablonów usługi ARM](template-syntax.md).
* Aby scalić wiele szablonów, zobacz [Używanie połączonych i zagnieżdżonych szablonów podczas wdrażania zasobów platformy Azure](linked-templates.md).
* Aby powtórzyć określoną liczbę razy podczas tworzenia typu zasobu, zobacz [iteracja zasobów w szablonach ARM](copy-resources.md).
* Aby dowiedzieć się, jak wdrożyć utworzony szablon, zobacz [wdrażanie zasobów przy użyciu szablonów ARM i Azure PowerShell](deploy-powershell.md).
