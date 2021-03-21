---
title: Funkcje szablonu — wartość liczbowa
description: Opisuje funkcje, które mają być używane w szablonie Azure Resource Manager (szablon ARM) do pracy z liczbami.
ms.topic: conceptual
ms.date: 11/18/2020
ms.openlocfilehash: f3687581d94f80cc923614a0655da1813bd5c97b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97359714"
---
# <a name="numeric-functions-for-arm-templates"></a>Funkcje liczbowe dla szablonów ARM

Menedżer zasobów udostępnia następujące funkcje do pracy z liczbami całkowitymi w szablonie Azure Resource Manager (szablon ARM):

* [add](#add)
* [Funkcji copyindex](#copyindex)
* [div](#div)
* [liczba zmiennoprzecinkowa](#float)
* [int](#int)
* [Maksymalny](#max)
* [min](#min)
* [Funkcja](#mod)
* [mul](#mul)
* [Sub](#sub)

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="add"></a>add

`add(operand1, operand2)`

Zwraca sumę dwóch podanych liczb całkowitych. `add`Funkcja nie jest obsługiwana w Bicep. `+`Zamiast tego użyj operatora.

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
|:--- |:--- |:--- |:--- |
|operand1 |Tak |int |Pierwsza liczba do dodania. |
|Operand2 |Tak |int |Druga liczba do dodania. |

### <a name="return-value"></a>Wartość zwracana

Liczba całkowita, która zawiera sumę parametrów.

### <a name="example"></a>Przykład

Poniższy [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/add.json) dodaje dwa parametry.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "first": {
      "type": "int",
      "defaultValue": 5,
      "metadata": {
        "description": "First integer to add"
      }
    },
    "second": {
      "type": "int",
      "defaultValue": 3,
      "metadata": {
        "description": "Second integer to add"
      }
    }
  },
  "resources": [
  ],
  "outputs": {
    "addResult": {
      "type": "int",
      "value": "[add(parameters('first'), parameters('second'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param first int = 5
param second int = 3

output addResult int = first + second
```

---

Dane wyjściowe z poprzedniego przykładu z wartościami domyślnymi są następujące:

| Nazwa | Typ | Wartość |
| ---- | ---- | ----- |
| AddResult | int | 8 |

## <a name="copyindex"></a>Funkcji copyindex

`copyIndex(loopName, offset)`

Zwraca indeks pętli iteracji.

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
|:--- |:--- |:--- |:--- |
| Pętla | Nie | ciąg | Nazwa pętli do pobrania iteracji. |
| przesunięcie |Nie |int |Liczba, która ma zostać dodana do wartości iteracji opartej na zero. |

### <a name="remarks"></a>Uwagi

Ta funkcja jest zawsze używana z obiektem **copy** . Jeśli nie podano wartości dla **przesunięcia**, zwracana jest bieżąca wartość iteracji. Wartość iteracji zaczyna się od zera.

Właściwość **loopname** pozwala określić, czy funkcji copyindex odwołuje się do iteracji zasobu, czy do iteracji właściwości. Jeśli nie podano wartości dla **loopname**, używana jest bieżąca iteracja typu zasobu. Podaj wartość dla parametru **loopname** podczas iterowania właściwości.

Aby uzyskać więcej informacji o korzystaniu z kopii, zobacz:

* [Iteracja zasobów w szablonach ARM](copy-resources.md)
* [Iteracja właściwości w szablonach ARM](copy-properties.md)
* [Iteracja zmiennej w szablonach ARM](copy-variables.md)
* [Iteracja danych wyjściowych w szablonach ARM](copy-outputs.md)

### <a name="example"></a>Przykład

Poniższy przykład przedstawia pętlę kopiowania i wartość indeksu zawartą w nazwie.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageCount": {
      "type": "int",
      "defaultValue": 2
    }
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-04-01",
      "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": {},
      "copy": {
        "name": "storagecopy",
        "count": "[parameters('storageCount')]"
      }
    }
  ],
  "outputs": {}
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

> [!NOTE]
> Pętle i `copyIndex` nie zostały jeszcze zaimplementowane w Bicep.  Zobacz [pętle](https://github.com/Azure/bicep/blob/main/docs/spec/loops.md).

---

### <a name="return-value"></a>Wartość zwracana

Liczba całkowita reprezentująca bieżący indeks iteracji.

## <a name="div"></a>div

`div(operand1, operand2)`

Zwraca podział liczby całkowitej z dwóch podanych liczb całkowitych. `div`Funkcja nie jest obsługiwana w Bicep. `/`Zamiast tego użyj operatora.

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
|:--- |:--- |:--- |:--- |
| operand1 |Tak |int |Podzielona liczba. |
| Operand2 |Tak |int |Liczba, która jest używana do dzielenia. Nie może mieć wartości 0. |

### <a name="return-value"></a>Wartość zwracana

Liczba całkowita reprezentująca podział.

### <a name="example"></a>Przykład

Poniższy [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/div.json) dzieli jeden parametr przez inny parametr.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "first": {
      "type": "int",
      "defaultValue": 8,
      "metadata": {
        "description": "Integer being divided"
      }
    },
    "second": {
      "type": "int",
      "defaultValue": 3,
      "metadata": {
        "description": "Integer used to divide"
      }
    }
  },
  "resources": [
  ],
  "outputs": {
    "divResult": {
      "type": "int",
      "value": "[div(parameters('first'), parameters('second'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param first int = 8
param second int = 3

output addResult int = first / second
```

---

Dane wyjściowe z poprzedniego przykładu z wartościami domyślnymi są następujące:

| Nazwa | Typ | Wartość |
| ---- | ---- | ----- |
| divResult | int | 2 |

## <a name="float"></a>float

`float(arg1)`

Konwertuje wartość na liczbę zmiennoprzecinkową. Ta funkcja jest używana tylko podczas przekazywania parametrów niestandardowych do aplikacji, na przykład aplikacji logiki. `float`Funkcja nie jest obsługiwana w Bicep.  Zobacz [obsługiwane typy liczbowe inne niż 32-bitowe liczby całkowite](https://github.com/Azure/bicep/issues/486).

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
|:--- |:--- |:--- |:--- |
| arg1 |Tak |ciąg lub int |Wartość do przekonwertowania na liczbę zmiennoprzecinkową. |

### <a name="return-value"></a>Wartość zwracana

Liczba zmiennoprzecinkowa.

### <a name="example"></a>Przykład

Poniższy przykład pokazuje, jak używać metody zmiennoprzecinkowej do przekazywania parametrów do aplikacji logiki:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "type": "Microsoft.Logic/workflows",
  "properties": {
    ...
    "parameters": {
      "custom1": {
        "value": "[float('3.0')]"
      },
      "custom2": {
        "value": "[float(3)]"
      },
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

> [!NOTE]
> `float`Funkcja nie jest obsługiwana w Bicep.  Zobacz [obsługiwane typy liczbowe inne niż 32-bitowe liczby całkowite](https://github.com/Azure/bicep/issues/486).

---

## <a name="int"></a>int

`int(valueToConvert)`

Konwertuje określoną wartość na liczbę całkowitą.

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
|:--- |:--- |:--- |:--- |
| valueToConvert |Tak |ciąg lub int |Wartość do przekonwertowania na liczbę całkowitą. |

### <a name="return-value"></a>Wartość zwracana

Liczba całkowita przekonwertowanej wartości.

### <a name="example"></a>Przykład

Poniższy [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/int.json) konwertuje wartość parametru dostarczoną przez użytkownika na liczbę całkowitą.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "stringToConvert": {
      "type": "string",
      "defaultValue": "4"
    }
  },
  "resources": [
  ],
  "outputs": {
    "intResult": {
      "type": "int",
      "value": "[int(parameters('stringToConvert'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param stringToConvert string = '4'

output inResult int = int(stringToConvert)
```

---

Dane wyjściowe z poprzedniego przykładu z wartościami domyślnymi są następujące:

| Nazwa | Typ | Wartość |
| ---- | ---- | ----- |
| intResult | int | 4 |

## <a name="max"></a>max

`max (arg1)`

Zwraca maksymalną wartość z tablicy liczb całkowitych lub rozdzielaną przecinkami listę liczb całkowitych.

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
|:--- |:--- |:--- |:--- |
| arg1 |Tak |tablica liczb całkowitych lub rozdzielana przecinkami lista liczb całkowitych |Kolekcja, w której ma zostać uzyskana wartość maksymalna. |

### <a name="return-value"></a>Wartość zwracana

Liczba całkowita reprezentująca wartość maksymalną z kolekcji.

### <a name="example"></a>Przykład

Poniższy [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/max.json) pokazuje, jak używać Max z tablicą i listą liczb całkowitych:

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

output arrayOutPut int = max(arrayToTest)
output intOutput int = max(0,3,2,5,4)
```

---

Dane wyjściowe z poprzedniego przykładu z wartościami domyślnymi są następujące:

| Nazwa | Typ | Wartość |
| ---- | ---- | ----- |
| arrayOutput | int | 5 |
| intOutput | int | 5 |

## <a name="min"></a>min

`min (arg1)`

Zwraca minimalną wartość z tablicy liczb całkowitych lub rozdzielaną przecinkami listę liczb całkowitych.

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
|:--- |:--- |:--- |:--- |
| arg1 |Tak |tablica liczb całkowitych lub rozdzielana przecinkami lista liczb całkowitych |Kolekcja, w której ma zostać uzyskana wartość minimalna. |

### <a name="return-value"></a>Wartość zwracana

Liczba całkowita reprezentująca wartość minimalną z kolekcji.

### <a name="example"></a>Przykład

Poniższy [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/min.json) pokazuje, jak używać min z tablicą i listą liczb całkowitych:

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

output arrayOutPut int = min(arrayToTest)
output intOutput int = min(0,3,2,5,4)
```

---

Dane wyjściowe z poprzedniego przykładu z wartościami domyślnymi są następujące:

| Nazwa | Typ | Wartość |
| ---- | ---- | ----- |
| arrayOutput | int | 0 |
| intOutput | int | 0 |

## <a name="mod"></a>Funkcja

`mod(operand1, operand2)`

Zwraca resztę z dzielenia liczb całkowitych przy użyciu dwóch podanych liczb całkowitych. `mod`Funkcja nie jest obsługiwana w Bicep. `%`Zamiast tego użyj operatora.

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
|:--- |:--- |:--- |:--- |
| operand1 |Tak |int |Podzielona liczba. |
| Operand2 |Tak |int |Liczba, która jest używana do dzielenia, nie może wynosić 0. |

### <a name="return-value"></a>Wartość zwracana

Liczba całkowita reprezentująca resztę.

### <a name="example"></a>Przykład

Poniższy [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/mod.json) Zwraca resztę z dzielenia jednego parametru przez inny parametr.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "first": {
      "type": "int",
      "defaultValue": 7,
      "metadata": {
        "description": "Integer being divided"
      }
    },
    "second": {
      "type": "int",
      "defaultValue": 3,
      "metadata": {
        "description": "Integer used to divide"
      }
    }
  },
  "resources": [
  ],
  "outputs": {
    "modResult": {
      "type": "int",
      "value": "[mod(parameters('first'), parameters('second'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param first int = 7
param second int = 3

output modResult int = first % second
```

---

Dane wyjściowe z poprzedniego przykładu z wartościami domyślnymi są następujące:

| Nazwa | Typ | Wartość |
| ---- | ---- | ----- |
| modResult | int | 1 |

## <a name="mul"></a>mul

`mul(operand1, operand2)`

Zwraca iloczyn dwóch podanych liczb całkowitych. `mul`Funkcja nie jest obsługiwana w Bicep. `*`Zamiast tego użyj operatora.

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
|:--- |:--- |:--- |:--- |
| operand1 |Tak |int |Pierwsza liczba do pomnożenia. |
| Operand2 |Tak |int |Druga liczba do pomnożenia. |

### <a name="return-value"></a>Wartość zwracana

Liczba całkowita reprezentująca iloczyn.

### <a name="example"></a>Przykład

Poniższy [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/mul.json) mnoży jeden parametr przez inny parametr.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "first": {
      "type": "int",
      "defaultValue": 5,
      "metadata": {
        "description": "First integer to multiply"
      }
    },
    "second": {
      "type": "int",
      "defaultValue": 3,
      "metadata": {
        "description": "Second integer to multiply"
      }
    }
  },
  "resources": [
  ],
  "outputs": {
    "mulResult": {
      "type": "int",
      "value": "[mul(parameters('first'), parameters('second'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param first int = 5
param second int = 3

output mulResult int = first * second
```

---

Dane wyjściowe z poprzedniego przykładu z wartościami domyślnymi są następujące:

| Nazwa | Typ | Wartość |
| ---- | ---- | ----- |
| mulResult | int | 15 |

## <a name="sub"></a>Sub

`sub(operand1, operand2)`

Zwraca odejmowanie dwóch podanych liczb całkowitych. `sub`Funkcja nie jest obsługiwana w Bicep. `-`Zamiast tego użyj operatora.

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
|:--- |:--- |:--- |:--- |
| operand1 |Tak |int |Liczba odjęta od. |
| Operand2 |Tak |int |Liczba, która jest odejmowana. |

### <a name="return-value"></a>Wartość zwracana

Liczba całkowita reprezentująca odejmowanie.

### <a name="example"></a>Przykład

Poniższy [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/sub.json) odejmuje jeden parametr z innego parametru.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "first": {
      "type": "int",
      "defaultValue": 7,
      "metadata": {
        "description": "Integer subtracted from"
      }
    },
    "second": {
      "type": "int",
      "defaultValue": 3,
      "metadata": {
        "description": "Integer to subtract"
      }
    }
  },
  "resources": [
  ],
  "outputs": {
    "subResult": {
      "type": "int",
      "value": "[sub(parameters('first'), parameters('second'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param first int = 7
param second int = 3

output subResult int = first - second
```

---

Dane wyjściowe z poprzedniego przykładu z wartościami domyślnymi są następujące:

| Nazwa | Typ | Wartość |
| ---- | ---- | ----- |
| Wynik | int | 4 |

## <a name="next-steps"></a>Następne kroki

* Opis sekcji w szablonie ARM można znaleźć [w temacie Omówienie struktury i składni szablonów usługi ARM](template-syntax.md).
* Aby powtórzyć określoną liczbę razy podczas tworzenia typu zasobu, zobacz [iteracja zasobów w szablonach ARM](copy-resources.md).
