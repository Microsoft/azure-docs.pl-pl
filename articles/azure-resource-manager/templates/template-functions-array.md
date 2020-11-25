---
title: Funkcje szablonu — tablice
description: Opisuje funkcje, które mają być używane w Azure Resource Manager szablonu do pracy z tablicami.
ms.topic: conceptual
ms.date: 11/18/2020
ms.openlocfilehash: 85f910f6143fd16f996a200172d0efd852897d8c
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/21/2020
ms.locfileid: "96004623"
---
# <a name="array-functions-for-arm-templates"></a>Funkcje tablicy dla szablonów ARM

Menedżer zasobów udostępnia kilka funkcji do pracy z tablicami w szablonie Azure Resource Manager (ARM).

* [array](#array)
* [Concat](#concat)
* [wyświetlana](#contains)
* [przearray](#createarray)
* [puste](#empty)
* [pierwszego](#first)
* [część wspólną](#intersection)
* [ostatniego](#last)
* [length](#length)
* [Maksymalny](#max)
* [min](#min)
* [zakresu](#range)
* [Skocz](#skip)
* [take (pobierz)](#take)
* [Unii](#union)

Aby uzyskać tablicę wartości ciągów rozdzielanych wartością, zobacz [Split](template-functions-string.md#split).

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="array"></a>array

`array(convertToArray)`

Konwertuje wartość na tablicę.

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
|:--- |:--- |:--- |:--- |
| convertToArray |Tak |int, String, array lub Object |Wartość do przekonwertowania na tablicę. |

### <a name="return-value"></a>Wartość zwracana

Tablica.

### <a name="example"></a>Przykład

Poniższy przykład pokazuje, jak używać funkcji Array z różnymi typami.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "intToConvert": {
      "type": "int",
      "defaultValue": 1
    },
    "stringToConvert": {
      "type": "string",
      "defaultValue": "efgh"
    },
    "objectToConvert": {
      "type": "object",
      "defaultValue": {
        "a": "b",
        "c": "d"
      }
    }
  },
  "resources": [
  ],
  "outputs": {
    "intOutput": {
      "type": "array",
      "value": "[array(parameters('intToConvert'))]"
    },
    "stringOutput": {
      "type": "array",
      "value": "[array(parameters('stringToConvert'))]"
    },
    "objectOutput": {
      "type": "array",
      "value": "[array(parameters('objectToConvert'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param intToConvert int = 1
param stringToConvert string = 'efgh'
param objectToConvert object = {
  'a': 'b'
  'c': 'd'
}

output intOutput array = array(intToConvert)
output stringOutput array = array(stringToConvert)
output objectOutput array = array(objectToConvert)
```

---

Dane wyjściowe z poprzedniego przykładu z wartościami domyślnymi są następujące:

| Nazwa | Typ | Wartość |
| ---- | ---- | ----- |
| intOutput | Tablica |  [1] |
| stringOutput | Tablica | ["efgh"] |
| objectOutput | Tablica | [{"a": "b", "c": "d"}] |

## <a name="concat"></a>concat

`concat(arg1, arg2, arg3, ...)`

Łączy wiele tablic i zwraca tablicę połączonej lub łączy wiele wartości ciągów i zwraca ciąg połączony.

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
|:--- |:--- |:--- |:--- |
| arg1 |Tak |Tablica lub ciąg |Pierwsza tablica lub ciąg służący do łączenia. |
| dodatkowe argumenty |Nie |Tablica lub ciąg |Dodatkowe tablice lub ciągi w kolejności sekwencyjnej dla łączenia. |

Ta funkcja może przyjmować dowolną liczbę argumentów i może akceptować ciągi lub tablice dla parametrów. Nie można jednak dostarczyć obu tablic i ciągów dla parametrów. Tablice są łączone tylko z innymi tablicami.

### <a name="return-value"></a>Wartość zwracana

Ciąg lub tablica połączonych wartości.

### <a name="example"></a>Przykład

Poniższy przykład pokazuje, jak połączyć dwie tablice.

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

---

Dane wyjściowe z poprzedniego przykładu z wartościami domyślnymi są następujące:

| Nazwa | Typ | Wartość |
| ---- | ---- | ----- |
| concatOutput | Ciąg | prefiks — 5yj4yjf5mbg72 |

## <a name="contains"></a>zawiera

`contains(container, itemToFind)`

Sprawdza, czy tablica zawiera wartość, obiekt zawiera klucz, lub ciąg zawiera podciąg. W porównaniu z rozróżnianiem wielkości liter są rozróżniane wielkie litery. Jednak podczas testowania, jeśli obiekt zawiera klucz, w porównaniu nie jest rozróżniana wielkość liter.

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
|:--- |:--- |:--- |:--- |
| kontener |Tak |Tablica, obiekt lub ciąg |Wartość, która zawiera wartość do znalezienia. |
| itemToFind |Tak |ciąg lub int |Wartość do znalezienia. |

### <a name="return-value"></a>Wartość zwracana

**Ma wartość true** , jeśli element zostanie znaleziony. w przeciwnym razie **false**.

### <a name="example"></a>Przykład

Poniższy przykład pokazuje, jak używać Contains z różnymi typami:

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

## <a name="createarray"></a>przearray

`createArray (arg1, arg2, arg3, ...)`

Tworzy tablicę z parametrów. `createArray`Funkcja nie jest obsługiwana przez Bicep.  Utwórz literał Array przy użyciu `[]` .

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
|:--- |:--- |:--- |:--- |
| args |Nie |Ciąg, liczba całkowita, tablica lub obiekt |Wartości w tablicy. |

### <a name="return-value"></a>Wartość zwracana

Tablica. Gdy nie podano parametrów, zwraca pustą tablicę.

### <a name="example"></a>Przykład

Poniższy przykład pokazuje, jak używać metody XmlArray z różnymi typami:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
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
    "stringArray": {
      "type": "array",
      "value": "[createArray('a', 'b', 'c')]"
    },
    "intArray": {
      "type": "array",
      "value": "[createArray(1, 2, 3)]"
    },
    "objectArray": {
      "type": "array",
      "value": "[createArray(parameters('objectToTest'))]"
    },
    "arrayArray": {
      "type": "array",
      "value": "[createArray(parameters('arrayToTest'))]"
    },
    "emptyArray": {
      "type": "array",
      "value": "[createArray()]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

> [!NOTE]
> `createArray()` nie jest obsługiwany przez Bicep.  Utwórz literał Array przy użyciu `[]` .

---

Dane wyjściowe z poprzedniego przykładu z wartościami domyślnymi są następujące:

| Nazwa | Typ | Wartość |
| ---- | ---- | ----- |
| stringArray | Tablica | ["a", "b", "c"] |
| intArray | Tablica | [1, 2, 3] |
| objectArray | Tablica | [{"jeden": "a", "dwa": "b", "trzy": "c"}] |
| arrayArray | Tablica | [["jeden", "dwa", "trzy"]] |
| emptyArray | Tablica | [] |

## <a name="empty"></a>puste

`empty(itemToTest)`

Określa, czy tablica, obiekt lub ciąg jest pusty.

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
|:--- |:--- |:--- |:--- |
| itemToTest |Tak |Tablica, obiekt lub ciąg |Wartość, aby sprawdzić, czy jest pusta. |

### <a name="return-value"></a>Wartość zwracana

Zwraca **wartość true** , jeśli wartość jest pusta. w przeciwnym razie **false**.

### <a name="example"></a>Przykład

Poniższy przykład sprawdza, czy tablica, obiekt i ciąg są puste.

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

## <a name="first"></a>pierwszego

`first(arg1)`

Zwraca pierwszy element tablicy lub pierwszy znak ciągu.

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
|:--- |:--- |:--- |:--- |
| arg1 |Tak |Tablica lub ciąg |Wartość do pobrania pierwszego elementu lub znaku. |

### <a name="return-value"></a>Wartość zwracana

Typ (ciąg, int, array lub Object) pierwszego elementu w tablicy lub pierwszy znak ciągu.

### <a name="example"></a>Przykład

Poniższy przykład pokazuje, jak używać pierwszej funkcji z tablicą i ciągiem.

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

## <a name="intersection"></a>część wspólną

`intersection(arg1, arg2, arg3, ...)`

Zwraca pojedynczą tablicę lub obiekt ze wspólnymi elementami z parametrów.

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
|:--- |:--- |:--- |:--- |
| arg1 |Tak |Tablica lub obiekt |Pierwsza wartość, która ma być używana do znajdowania typowych elementów. |
| arg2 |Tak |Tablica lub obiekt |Druga wartość, która ma być używana do znajdowania typowych elementów. |
| dodatkowe argumenty |Nie |Tablica lub obiekt |Dodatkowe wartości używane do znajdowania typowych elementów. |

### <a name="return-value"></a>Wartość zwracana

Tablica lub obiekt ze wspólnymi elementami.

### <a name="example"></a>Przykład

Poniższy przykład pokazuje, jak używać przecięcia z tablicami i obiektami:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "firstObject": {
      "type": "object",
      "defaultValue": {
        "one": "a",
        "two": "b",
        "three": "c"
      }
    },
    "secondObject": {
      "type": "object",
      "defaultValue": {
        "one": "a",
        "two": "z",
        "three": "c"
      }
    },
    "firstArray": {
      "type": "array",
      "defaultValue": [ "one", "two", "three" ]
    },
    "secondArray": {
      "type": "array",
      "defaultValue": [ "two", "three" ]
    }
  },
  "resources": [
  ],
  "outputs": {
    "objectOutput": {
      "type": "object",
      "value": "[intersection(parameters('firstObject'), parameters('secondObject'))]"
    },
    "arrayOutput": {
      "type": "array",
      "value": "[intersection(parameters('firstArray'), parameters('secondArray'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param firstObject object = {
  'one': 'a'
  'two': 'b'
  'three': 'c'
}

param secondObject object = {
  'one': 'a'
  'two': 'z'
  'three': 'c'
}

param firstArray array = [
  'one'
  'two'
  'three'
]

param secondArray array = [
  'two'
  'three'
]

output objectOutput object = intersection(firstObject, secondObject)
output arrayOutput array = intersection(firstArray, secondArray)
```

---

Dane wyjściowe z poprzedniego przykładu z wartościami domyślnymi są następujące:

| Nazwa | Typ | Wartość |
| ---- | ---- | ----- |
| objectOutput | Obiekt | {"jeden": "a", "3": "c"} |
| arrayOutput | Tablica | ["dwa", "trzy"] |

## <a name="last"></a>ostatni

`last (arg1)`

Zwraca ostatni element tablicy lub ostatni znak ciągu.

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
|:--- |:--- |:--- |:--- |
| arg1 |Tak |Tablica lub ciąg |Wartość do pobrania ostatniego elementu lub znaku. |

### <a name="return-value"></a>Wartość zwracana

Typ (ciąg, int, array lub Object) ostatniego elementu w tablicy lub ostatni znak ciągu.

### <a name="example"></a>Przykład

Poniższy przykład pokazuje, jak używać ostatniej funkcji z tablicą i ciągiem.

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
output stringOutput string = last('One Two three')
```

---

Dane wyjściowe z poprzedniego przykładu z wartościami domyślnymi są następujące:

| Nazwa | Typ | Wartość |
| ---- | ---- | ----- |
| arrayOutput | Ciąg | trzy |
| stringOutput | Ciąg | e |

## <a name="length"></a>length

`length(arg1)`

Zwraca liczbę elementów w tablicy, znaków w ciągu lub we właściwościach poziomu głównego w obiekcie.

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
|:--- |:--- |:--- |:--- |
| arg1 |Tak |Tablica, ciąg lub obiekt |Tablica, która ma być używana do pobierania liczby elementów, ciągu, który ma być używany do pobierania liczby znaków lub obiektu, który ma być używany do pobierania liczby właściwości na poziomie głównym. |

### <a name="return-value"></a>Wartość zwracana

Int.

### <a name="example"></a>Przykład

Poniższy przykład pokazuje, jak używać długości z tablicą i ciągiem:

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

Za pomocą tej funkcji z tablicą można określić liczbę iteracji podczas tworzenia zasobów. W poniższym przykładzie parametr **siteNames** będzie odnosił się do tablicy nazw, która będzie używana podczas tworzenia witryn sieci Web.

# <a name="json"></a>[JSON](#tab/json)

```json
"copy": {
  "name": "websitescopy",
  "count": "[length(parameters('siteNames'))]"
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

> [!NOTE]
> Pętle nie są jeszcze zaimplementowane w Bicep.  Zobacz [pętle](https://github.com/Azure/bicep/blob/main/docs/spec/loops.md).

---

Aby uzyskać więcej informacji o używaniu tej funkcji z tablicą, zobacz [Tworzenie wielu wystąpień zasobów w Azure Resource Manager](copy-resources.md).

## <a name="max"></a>max

`max(arg1)`

Zwraca maksymalną wartość z tablicy liczb całkowitych lub rozdzielaną przecinkami listę liczb całkowitych.

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
|:--- |:--- |:--- |:--- |
| arg1 |Tak |tablica liczb całkowitych lub rozdzielana przecinkami lista liczb całkowitych |Kolekcja, w której ma zostać uzyskana wartość maksymalna. |

### <a name="return-value"></a>Wartość zwracana

Int reprezentująca wartość maksymalną.

### <a name="example"></a>Przykład

Poniższy przykład pokazuje, jak używać Max z tablicą i listą liczb całkowitych:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "arrayToTest": {
      "type": "array",
      "defaultValue": [ 0, 3, 2, 5, 4 ]
    }
  },
  "resources": [],
  "outputs": {
    "arrayOutput": {
      "type": "int",
      "value": "[max(parameters('arrayToTest'))]"
    },
    "intOutput": {
      "type": "int",
      "value": "[max(0,3,2,5,4)]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param arrayToTest array = [
  0
  3
  2
  5
  4
]

output arrayOutput int = max(arrayToTest)
output intOutput int = max(0,3,2,5,4)
```

---

Dane wyjściowe z poprzedniego przykładu z wartościami domyślnymi są następujące:

| Nazwa | Typ | Wartość |
| ---- | ---- | ----- |
| arrayOutput | int | 5 |
| intOutput | int | 5 |

## <a name="min"></a>min

`min(arg1)`

Zwraca minimalną wartość z tablicy liczb całkowitych lub rozdzielaną przecinkami listę liczb całkowitych.

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
|:--- |:--- |:--- |:--- |
| arg1 |Tak |tablica liczb całkowitych lub rozdzielana przecinkami lista liczb całkowitych |Kolekcja, w której ma zostać uzyskana wartość minimalna. |

### <a name="return-value"></a>Wartość zwracana

Int reprezentująca wartość minimalną.

### <a name="example"></a>Przykład

Poniższy przykład pokazuje, jak używać min z tablicą i listą liczb całkowitych:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "arrayToTest": {
      "type": "array",
      "defaultValue": [ 0, 3, 2, 5, 4 ]
    }
  },
  "resources": [],
  "outputs": {
    "arrayOutput": {
      "type": "int",
      "value": "[min(parameters('arrayToTest'))]"
    },
    "intOutput": {
      "type": "int",
      "value": "[min(0,3,2,5,4)]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param arrayToTest array = [
  0
  3
  2
  5
  4
]

output arrayOutput int = min(arrayToTest)
output intOutput int = min(0,3,2,5,4)
```

---

Dane wyjściowe z poprzedniego przykładu z wartościami domyślnymi są następujące:

| Nazwa | Typ | Wartość |
| ---- | ---- | ----- |
| arrayOutput | int | 0 |
| intOutput | int | 0 |

## <a name="range"></a>range

`range(startIndex, count)`

Tworzy tablicę liczb całkowitych od początkowej liczby całkowitej i zawierającej liczbę elementów.

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
|:--- |:--- |:--- |:--- |
| Indeks |Tak |int |Pierwsza liczba całkowita w tablicy. Suma wartości startIndex i count nie może być większa niż 2147483647. |
| count |Tak |int |Liczba liczb całkowitych w tablicy. Musi być nieujemną liczbą całkowitą do 10000. |

### <a name="return-value"></a>Wartość zwracana

Tablica liczb całkowitych.

### <a name="example"></a>Przykład

Poniższy przykład pokazuje, jak używać funkcji Range:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "startingInt": {
      "type": "int",
      "defaultValue": 5
    },
    "numberOfElements": {
      "type": "int",
      "defaultValue": 3
    }
  },
  "resources": [],
  "outputs": {
    "rangeOutput": {
      "type": "array",
      "value": "[range(parameters('startingInt'),parameters('numberOfElements'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param startingInt int = 5
param numberOfElements int = 3

output rangeOutput array = range(startingInt, numberOfElements)
```

---

Dane wyjściowe z poprzedniego przykładu z wartościami domyślnymi są następujące:

| Nazwa | Typ | Wartość |
| ---- | ---- | ----- |
| rangeOutput | Tablica | [5, 6, 7] |

## <a name="skip"></a>Pomiń

`skip(originalValue, numberToSkip)`

Zwraca tablicę zawierającą wszystkie elementy po określonej liczbie w tablicy lub zwraca ciąg ze wszystkimi znakami po określonej liczbie w ciągu.

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
|:--- |:--- |:--- |:--- |
| originalValue |Tak |Tablica lub ciąg |Tablica lub ciąg, który ma być używany do pomijania. |
| numberToSkip |Tak |int |Liczba elementów lub znaków do pominięcia. Jeśli ta wartość jest równa 0 lub mniejsza, zwracane są wszystkie elementy lub znaki w wartości. Jeśli jest większa niż długość tablicy lub ciągu, zwracana jest pusta tablica lub ciąg. |

### <a name="return-value"></a>Wartość zwracana

Tablica lub ciąg.

### <a name="example"></a>Przykład

Poniższy przykład pomija określoną liczbę elementów w tablicy i określoną liczbę znaków w ciągu.

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

## <a name="take"></a>take (pobierz)

`take(originalValue, numberToTake)`

Zwraca tablicę o określonej liczbie elementów od początku tablicy lub ciąg z określoną liczbą znaków od początku ciągu.

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
|:--- |:--- |:--- |:--- |
| originalValue |Tak |Tablica lub ciąg |Tablica lub ciąg, z którego mają zostać przebrane elementy. |
| numberToTake |Tak |int |Liczba elementów lub znaków do wykonania. Jeśli ta wartość jest równa 0 lub mniejsza, zwracana jest pusta tablica lub ciąg. Jeśli jest większa niż długość danej tablicy lub ciągu, zwracane są wszystkie elementy w tablicy lub ciągu. |

### <a name="return-value"></a>Wartość zwracana

Tablica lub ciąg.

### <a name="example"></a>Przykład

Poniższy przykład pobiera określoną liczbę elementów z tablicy i znaki z ciągu.

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
param elementsToTake int = 2
param testString string = 'one two three'
param charactersToTake int = 2

output arrayOutput array = take(testArray, elementsToTake)
output stringOutput string = take(testString, charactersToTake)
```

---

Dane wyjściowe z poprzedniego przykładu z wartościami domyślnymi są następujące:

| Nazwa | Typ | Wartość |
| ---- | ---- | ----- |
| arrayOutput | Tablica | ["jeden", "dwa"] |
| stringOutput | Ciąg | on |

## <a name="union"></a>unia

`union(arg1, arg2, arg3, ...)`

Zwraca pojedynczą tablicę lub obiekt ze wszystkimi elementami z parametrów. Zduplikowane wartości lub klucze są uwzględniane tylko raz.

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
|:--- |:--- |:--- |:--- |
| arg1 |Tak |Tablica lub obiekt |Pierwsza wartość, która ma być używana do sprzęgania elementów. |
| arg2 |Tak |Tablica lub obiekt |Druga wartość, która ma być używana do sprzęgania elementów. |
| dodatkowe argumenty |Nie |Tablica lub obiekt |Dodatkowe wartości, które mają być używane na potrzeby sprzęgania elementów. |

### <a name="return-value"></a>Wartość zwracana

Tablica lub obiekt.

### <a name="example"></a>Przykład

Poniższy przykład pokazuje, jak używać Union z tablicami i obiektami:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "firstObject": {
      "type": "object",
      "defaultValue": {
        "one": "a",
        "two": "b",
        "three": "c1"
      }
    },
    "secondObject": {
      "type": "object",
      "defaultValue": {
        "three": "c2",
        "four": "d",
        "five": "e"
      }
    },
    "firstArray": {
      "type": "array",
      "defaultValue": [ "one", "two", "three" ]
    },
    "secondArray": {
      "type": "array",
      "defaultValue": [ "three", "four" ]
    }
  },
  "resources": [
  ],
  "outputs": {
    "objectOutput": {
      "type": "object",
      "value": "[union(parameters('firstObject'), parameters('secondObject'))]"
    },
    "arrayOutput": {
      "type": "array",
      "value": "[union(parameters('firstArray'), parameters('secondArray'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param firstObject object = {
  'one': 'a'
  'two': 'b'
  'three': 'c1'
}

param secondObject object = {
  'three': 'c2'
  'four': 'd'
  'five': 'e'
}

param firstArray array = [
  'one'
  'two'
  'three'
]

param secondArray array = [
  'three'
  'four'
]

output objectOutput object = union(firstObject, secondObject)
output arrayOutput array = union(firstArray, secondArray)
```

---

Dane wyjściowe z poprzedniego przykładu z wartościami domyślnymi są następujące:

| Nazwa | Typ | Wartość |
| ---- | ---- | ----- |
| objectOutput | Obiekt | {"jeden": "a", "dwa": "b", "trzy": "C2", "4": "d", "pięć": "e"} |
| arrayOutput | Tablica | ["jeden", "dwa", "trzy", "cztery"] |

## <a name="next-steps"></a>Następne kroki

* Opis sekcji w szablonie Azure Resource Manager można znaleźć w temacie [Omówienie struktury i składni szablonów usługi ARM](template-syntax.md).
