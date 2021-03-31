---
title: Tworzenie funkcji konwersji definicji interfejsu użytkownika
description: Opisuje funkcje, które mają być używane podczas konwertowania wartości między typami danych i kodowaniem.
author: tfitzmac
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: tomfitz
ms.openlocfilehash: b69cd35b27b343da08727b4c4ee9b4fd025e1df7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "87098195"
---
# <a name="createuidefinition-conversion-functions"></a>Funkcje konwersji CreateUiDefinition

Te funkcje mogą służyć do konwertowania wartości między typami danych JSON a kodowaniem.

## <a name="bool"></a>bool

Konwertuje parametr na wartość logiczną. Ta funkcja obsługuje parametry typu Number, String i Boolean. Podobne do wartości logicznych w języku JavaScript, dowolnej wartości z wyjątkiem `0` lub `'false'` zwraca `true` .

Poniższy przykład zwraca `true` :

```json
"[bool(1)]"
```

Poniższy przykład zwraca `false` :

```json
"[bool(0)]"
```

Poniższy przykład zwraca `true` :

```json
"[bool(true)]"
```

Poniższy przykład zwraca `true` :

```json
"[bool('true')]"
```

## <a name="decodebase64"></a>decodeBase64

Dekoduje parametr z zakodowanego ciągu Base-64. Ta funkcja obsługuje tylko parametry typu String.

Poniższy przykład zwraca `"Contoso"` :

```json
"[decodeBase64('Q29udG9zbw==')]"
```

## <a name="decodeuricomponent"></a>decodeUriComponent

Dekoduje parametr z ciągu zakodowanego w adresie URL. Ta funkcja obsługuje tylko parametry typu String.

Poniższy przykład zwraca `"https://portal.azure.com/"` :

```json
"[decodeUriComponent('https%3A%2F%2Fportal.azure.com%2F')]"
```

## <a name="encodebase64"></a>encodeBase64

Koduje parametr do zakodowanego ciągu Base-64. Ta funkcja obsługuje tylko parametry typu String.

Poniższy przykład zwraca `"Q29udG9zbw=="` :

```json
"[encodeBase64('Contoso')]"
```

## <a name="encodeuricomponent"></a>encodeURIComponent —

Koduje parametr na ciąg zakodowany w adresie URL. Ta funkcja obsługuje tylko parametry typu String.

Poniższy przykład zwraca `"https%3A%2F%2Fportal.azure.com%2F"` :

```json
"[encodeUriComponent('https://portal.azure.com/')]"
```

## <a name="float"></a>float

Konwertuje parametr na zmiennoprzecinkową. Ta funkcja obsługuje parametry typu Number i String.

Poniższy przykład zwraca `1.0` :

```json
"[float('1.0')]"
```

Poniższy przykład zwraca `2.9` :

```json
"[float(2.9)]"
```

## <a name="int"></a>int

Konwertuje parametr na liczbę całkowitą. Ta funkcja obsługuje parametry typu Number i String.

Poniższy przykład zwraca `1` :

```json
"[int('1')]"
```

Poniższy przykład zwraca `2` :

```json
"[int(2.9)]"
```

## <a name="parse"></a>parse

Konwertuje parametr na typ natywny. Innymi słowy, ta funkcja jest odwrotnością `string()` . Ta funkcja obsługuje tylko parametry typu String.

Poniższy przykład zwraca `1` :

```json
"[parse('1')]"
```

Poniższy przykład zwraca `true` :

```json
"[parse('true')]"
```

Poniższy przykład zwraca `[1,2,3]` :

```json
"[parse('[1,2,3]')]"
```

Poniższy przykład zwraca `{"type":"webapp"}` :

```json
"[parse('{\"type\":\"webapp\"}')]"
```

## <a name="string"></a>ciąg

Konwertuje parametr na ciąg. Ta funkcja obsługuje parametry wszystkich typów danych JSON.

Poniższy przykład zwraca `"1"` :

```json
"[string(1)]"
```

Poniższy przykład zwraca `"2.9"` :

```json
"[string(2.9)]"
```

Poniższy przykład zwraca `"[1,2,3]"` :

```json
"[string([1,2,3])]"
```

Poniższy przykład zwraca `"{"type":"webapp"}"` :

```json
"[string({\"type\":\"webapp\"})]"
```

## <a name="next-steps"></a>Następne kroki

* Aby zapoznać się z wprowadzeniem do Azure Resource Manager, zobacz [Azure Resource Manager omówienie](../management/overview.md).
