---
title: Typy danych w szablonach
description: Opisuje typy danych, które są dostępne w szablonach Azure Resource Manager.
ms.topic: conceptual
ms.author: tomfitz
author: tfitzmac
ms.date: 03/04/2021
ms.openlocfilehash: 7d3f15c8852e6e25c621baad9bc6f20c303ffdb9
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102125142"
---
# <a name="data-types-in-arm-templates"></a>Typy danych w szablonach ARM

W tym artykule opisano typy danych obsługiwane w szablonach Azure Resource Manager (szablony ARM). Obejmuje to zarówno typy danych JSON, jak i Bicep.

## <a name="supported-types"></a>Obsługiwane typy

W ramach szablonu ARM można używać następujących typów danych:

* array
* bool
* int
* object
* secureobject — wskazanie modyfikatora w Bicep
* secureString — wskazywany przez modyfikator w Bicep
* ciąg

## <a name="arrays"></a>Tablice

Tablice zaczynają się od lewego nawiasu kwadratowego ( `[` ) i kończą się znakiem prawego nawiasu kwadratowego ( `]` ).

W formacie JSON tablica może być zadeklarowana w pojedynczym wierszu lub wielu wierszach. Każdy element jest oddzielony przecinkami.

W Bicep Tablica musi być zadeklarowana w wielu wierszach. Nie używaj przecinków między wartościami.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "exampleArray": {
    "type": "array",
    "defaultValue": [
      1,
      2,
      3
    ]
  }
},
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param exampleArray array = [
  1
  2
  3
]
```

---

Elementy tablicy mogą być tego samego typu lub różnych typów.

# <a name="json"></a>[JSON](#tab/json)

```json
"variables": {
  "mixedArray": [
    "[resourceGroup().name]",
    1,
    true,
    "example string"
  ]
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
var mixedArray = [
  resourceGroup().name
  1
  true
  'example string'
]
```

---

## <a name="booleans"></a>Wartości logiczne

Podczas określania wartości logicznych należy użyć `true` lub `false` . Nie otaczaj wartości cudzysłowami.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "exampleBool": {
    "type": "bool",
    "defaultValue": true
  }
},
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param exampleBool bool = true
```

---

## <a name="integers"></a>Liczby całkowite

Podczas określania wartości całkowitych nie używaj cudzysłowów.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "exampleInt": {
    "type": "int",
    "defaultValue": 1
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param exampleInt int = 1
```

---

W przypadku liczb całkowitych przewidzianych jako parametry wbudowane zakres wartości może być ograniczony przez zestaw SDK lub narzędzie wiersza polecenia, które jest używane do wdrożenia. Na przykład w przypadku wdrażania szablonu przy użyciu programu PowerShell typy całkowite mogą przyjmować wartość od-2147483648 do 2147483647. Aby uniknąć tego ograniczenia, określ w [pliku parametrów](parameter-files.md)duże wartości całkowite. Typy zasobów stosują własne limity dla właściwości Integer.

## <a name="objects"></a>Obiekty

Obiekty zaczynają się od lewego nawiasu klamrowego ( `{` ) i kończyć znakiem prawego nawiasu klamrowego ( `}` ). Każda właściwość w obiekcie składa się z klucza i wartości. Klucz i wartość są oddzielone dwukropkiem ( `:` ).

W formacie JSON klucz jest ujęty w cudzysłów. Każda właściwość jest oddzielona przecinkiem.

W Bicep klucz nie jest ujęty w cudzysłów. Nie używaj przecinków do między właściwościami.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "exampleObject": {
    "type": "object",
    "defaultValue": {
      "name": "test name",
      "id": "123-abc",
      "isCurrent": true,
      "tier": 1
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param exampleObject object = {
  name: 'test name'
  id: '123-abc'
  isCurrent: true
  tier: 1
}
```

---

## <a name="strings"></a>Ciągi

W formacie JSON ciągi są oznaczone podwójnymi cudzysłowami. W Bicep ciągi są oznaczane pojedynczymi cudzysłowami.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "exampleString": {
    "type": "string",
    "defaultValue": "test value"
  }
},
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param exampleString string = 'test value'
```
---

## <a name="secure-strings-and-objects"></a>Zabezpieczanie ciągów i obiektów

Bezpieczny ciąg używa tego samego formatu co ciąg, a bezpieczny obiekt używa tego samego formatu co obiekt. Po ustawieniu parametru na bezpieczny ciąg lub zabezpieczony obiekt wartość parametru nie jest zapisywana w historii wdrożenia i nie jest rejestrowana. Jeśli jednak ustawisz tę bezpieczną wartość na właściwość, która nie oczekuje bezpiecznej wartości, wartość nie jest chroniona. Na przykład, jeśli ustawisz bezpieczny ciąg na tag, ta wartość jest przechowywana w postaci zwykłego tekstu. Użyj bezpiecznych ciągów dla haseł i wpisów tajnych.

Za pomocą Bicep, można dodać `@secure()` modyfikator do ciągu lub obiektu.

W poniższym przykładzie przedstawiono dwa bezpieczne parametry:

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "password": {
    "type": "secureString"
  },
  "configValues": {
    "type": "secureObject"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
@secure()
param password string

@secure()
param configValues object
```

---

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o składni szablonów, zobacz [Opis struktury i składni szablonów ARM](template-syntax.md).
