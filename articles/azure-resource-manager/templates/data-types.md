---
title: Typy danych w szablonach
description: Opisuje typy danych, które są dostępne w Azure Resource Manager szablonów.
ms.topic: conceptual
ms.author: tomfitz
author: tfitzmac
ms.date: 03/04/2021
ms.openlocfilehash: 4d6c8306b3dbdfe895055dc008d81cc0d85d8d6c
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107538066"
---
# <a name="data-types-in-arm-templates"></a>Typy danych w szablonach arm

W tym artykule opisano typy danych obsługiwane w Azure Resource Manager szablonów (szablonów ARM). Obejmuje zarówno typy danych JSON, jak i Bicep.

## <a name="supported-types"></a>Obsługiwane typy

W szablonie usługi ARM można używać następujących typów danych:

* array
* bool
* int
* object
* secureObject — wskazywany przez modyfikator w bicepsie
* secureString — wskazywane przez modyfikator w bicepsie
* ciąg

## <a name="arrays"></a>Tablice

Tablice zaczynają się lewym nawiasem kwadratowym ( `[` ), a kończą się prawym nawiasem kwadratowym ( `]` ).

W danych JSON tablicę można zadeklarować w jednym wierszu lub w wielu wierszach. Każdy element jest oddzielony przecinkiem.

W bicepie tablica musi być zadeklarowana w wielu wierszach. Nie używaj przecinków między wartościami.

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

Podczas określania wartości logicznych użyj wartości `true` lub `false` . Nie otaczaj wartości cudzysłowami.

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

Podczas określania wartości całkowitych nie używaj znaków cudzysłowu.

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

W przypadku liczb całkowitych przekazanych jako parametry wbudowane zakres wartości może być ograniczony przez zestaw SDK lub narzędzie wiersza polecenia służące do wdrażania. Na przykład w przypadku wdrażania szablonu przy użyciu programu PowerShell typy całkowite mogą mieć zakres od -2147483648 do 2147483647. Aby uniknąć tego ograniczenia, określ duże wartości całkowite w pliku [parametrów](parameter-files.md). Typy zasobów stosują własne limity dla właściwości liczb całkowitych.

## <a name="objects"></a>Obiekty

Obiekty rozpoczynają się od lewego nawiasu klamrowego `{` () i kończą się prawym nawiasem klamrowy ( `}` ). Każda właściwość w obiekcie składa się z klucza i wartości. Klucz i wartość są oddzielone dwukropkiem ( `:` ).

# <a name="json"></a>[JSON](#tab/json)

W danych JSON klucz jest ujęty w cudzysłów. Każda właściwość jest oddzielona przecinkiem.

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

W bicepie klucz nie jest ujęty w cudzysłów. Nie używaj przecinków do między właściwościami.

```bicep
param exampleObject object = {
  name: 'test name'
  id: '123-abc'
  isCurrent: true
  tier: 1
}
```

Dostęp do właściwości jest używany do uzyskiwania dostępu do właściwości obiektu. Są one konstruowane przy użyciu `.` operatora . Na przykład:

```bicep
var x = {
  y: {
    z: 'Hello`
    a: true
  }
  q: 42
}
```

Biorąc pod uwagę poprzednią deklarację, wyrażenie x.y.z oblicza ciąg literału "Hello". Podobnie wyrażenie x.q oblicza literał liczby całkowitej 42.

Dostępy do właściwości mogą być używane z dowolnym obiektem. Obejmuje to parametry i zmienne typów obiektów i literałów obiektów. Użycie dostępu do właściwości w wyrażeniu typu innego niż obiekt jest błędem.

---

## <a name="strings"></a>Ciągi

W danych JSON ciągi są oznaczone podwójnymi cudzysłowami. W bicepie ciągi są oznaczone pojedynczymi cudzysłowami.

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

Bezpieczny ciąg używa tego samego formatu co ciąg, a bezpieczny obiekt używa tego samego formatu co obiekt . W przypadku ustawienia parametru na bezpieczny ciąg lub bezpieczny obiekt wartość parametru nie jest zapisywana w historii wdrażania i nie jest rejestrowana. Jeśli jednak ustawisz bezpieczną wartość na właściwość, która nie oczekuje bezpiecznej wartości, ta wartość nie będzie chroniona. Jeśli na przykład ustawisz bezpieczny ciąg na tag, ta wartość będzie przechowywana jako zwykły tekst. Używanie bezpiecznych ciągów haseł i wpisów tajnych.

Za pomocą bicep można dodać `@secure()` modyfikator do ciągu lub obiektu.

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

Aby dowiedzieć się więcej o składni szablonu, zobacz [Understand the structure and syntax of ARM templates (Opis struktury i składni szablonów usługi ARM).](template-syntax.md)
