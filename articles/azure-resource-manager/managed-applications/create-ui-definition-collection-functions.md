---
title: Tworzenie funkcji kolekcji definicji interfejsu użytkownika
description: Opisuje funkcje, które mają być używane podczas pracy z kolekcjami, takimi jak tablice i obiekty.
author: tfitzmac
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: tomfitz
ms.openlocfilehash: 2a075c5c99f457681cd49e75014487bf9cca263c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "87098200"
---
# <a name="createuidefinition-collection-functions"></a>Funkcje kolekcji CreateUiDefinition

Te funkcje mogą być używane z kolekcjami, takimi jak ciągi JSON, tablice i obiekty.

## <a name="contains"></a>zawiera

Zwraca `true` Jeśli ciąg zawiera określony podciąg, tablica zawiera określoną wartość lub obiekt zawiera określony klucz.

### <a name="example-string-contains"></a>Przykład: ciąg zawiera

Poniższy przykład zwraca `true` :

```json
"[contains('webapp', 'web')]"
```

### <a name="example-array-contains"></a>Przykład: tablica zawiera

Przyjmij `element1` wartość Returns `[1, 2, 3]` . Poniższy przykład zwraca `false` :

```json
"[contains(steps('demoStep').element1, 4)]"
```

### <a name="example-object-contains"></a>Przykład: obiekt zawiera

Przyjmij `element1` zwrot:

```json
{
  "key1": "Linux",
  "key2": "Windows"
}
```

Poniższy przykład zwraca `true` :

```json
"[contains(steps('demoStep').element1, 'key1')]"
```

## <a name="empty"></a>puste

Zwraca `true` czy ciąg, tablica lub obiekt ma wartość null lub jest pusty.

### <a name="example-string-empty"></a>Przykład: pusty ciąg

Poniższy przykład zwraca `true` :

```json
"[empty('')]"
```

### <a name="example-array-empty"></a>Przykład: tablica pusta

Przyjmij `element1` wartość Returns `[1, 2, 3]` . Poniższy przykład zwraca `false` :

```json
"[empty(steps('demoStep').element1)]"
```

### <a name="example-object-empty"></a>Przykład: obiekt jest pusty

Przyjmij `element1` zwrot:

```json
{
  "key1": "Linux",
  "key2": "Windows"
}
```

Poniższy przykład zwraca `false` :

```json
"[empty(steps('demoStep').element1)]"
```

### <a name="example-null-and-undefined"></a>Przykład: null i undefined

Przyjmij `element1` `null` lub niezdefiniowane. Poniższy przykład zwraca `true` :

```json
"[empty(steps('demoStep').element1)]"
```

## <a name="filter"></a>filter

Zwraca nową tablicę po zastosowaniu logiki filtrowania dostarczonej jako funkcja lambda. Pierwszy parametr jest tablicą, która ma być używana do filtrowania. Drugi parametr jest funkcją lambda, która określa logikę filtrowania.

Poniższy przykład zwraca tablicę `[ { "name": "abc" } ]` .

```json
"[filter(parse('[{\"name\":\"abc\"},{\"name\":\"xyz\"}]'), (item) => contains(item.name, 'abc'))]"
```

## <a name="first"></a>pierwszego

Zwraca pierwszy znak określonego ciągu; Pierwsza wartość określonej tablicy; lub pierwszy klucz i wartość określonego obiektu.

### <a name="example-string-first"></a>Przykład: najpierw ciąg

Poniższy przykład zwraca `"c"` :

```json
"[first('contoso')]"
```

### <a name="example-array-first"></a>Przykład: najpierw tablica

Przyjmij `element1` wartość Returns `[1, 2, 3]` . Poniższy przykład zwraca `1` :

```json
"[first(steps('demoStep').element1)]"
```

#### <a name="example-object-first"></a>Przykład: najpierw obiekt

Przyjmij `element1` zwrot:

```json
{
  "key1": "Linux",
  "key2": "Windows"
}
```

Poniższy przykład zwraca `{"key1": "Linux"}` :

```json
"[first(steps('demoStep').element1)]"
```

## <a name="last"></a>ostatni

Zwraca ostatni znak określonego ciągu, ostatnią wartość określonej tablicy lub ostatni klucz i wartość określonego obiektu.

### <a name="example-string-last"></a>Przykład: ciąg Last

Poniższy przykład zwraca `"o"` :

```json
"[last('contoso')]"
```

### <a name="example-array-last"></a>Przykład: Ostatnia tablica

Przyjmij `element1` wartość Returns `[1, 2, 3]` . Poniższy przykład zwraca `3` :

```json
"[last(steps('demoStep').element1)]"
```

### <a name="example-object-last"></a>Przykład: obiekt Last

Przyjmij `element1` zwrot:

```json
{
  "key1": "Linux",
  "key2": "Windows"
}
```

Poniższy przykład zwraca `{"key2": "Windows"}` :

```json
"[last(steps('demoStep').element1)]"
```

## <a name="length"></a>length

Zwraca liczbę znaków w ciągu, liczbę wartości w tablicy lub liczbę kluczy w obiekcie.

### <a name="example-string-length"></a>Przykład: długość ciągu

Poniższy przykład zwraca `7` :

```json
"[length('Contoso')]"
```

### <a name="example-array-length"></a>Przykład: długość tablicy

Przyjmij `element1` wartość Returns `[1, 2, 3]` . Poniższy przykład zwraca `3` :

```json
"[length(steps('demoStep').element1)]"
```

### <a name="example-object-length"></a>Przykład: Długość obiektu

Przyjmij `element1` zwrot:

```json
{
  "key1": "Linux",
  "key2": "Windows"
}
```

Poniższy przykład zwraca `2` :

```json
"[length(steps('demoStep').element1)]"
```

## <a name="map"></a>map (mapa)

Zwraca nową tablicę po wywołaniu funkcji lambda w podanej tablicy. Pierwszy parametr jest tablicą, która ma być używana przez funkcję lambda. Drugi parametr jest funkcją lambda.

Poniższy przykład zwraca nową tablicę z każdą wartością podwójną. Wynik to `[2, 4, 6]` .

```json
"[map(parse('[1, 2, 3]'), (item) => mul(2, item))]"
```

Poniższy przykład zwraca nową tablicę `["abc", "xyz"]` .

```json
"[map(parse('[{\"name\":\"abc\"},{\"name\":\"xyz\"}]'), (item) => item.name)]"
```

## <a name="skip"></a>Pomiń

Pomija określoną liczbę elementów w kolekcji, a następnie zwraca pozostałe elementy.

### <a name="example-string-skip"></a>Przykład: pomijanie ciągu

Poniższy przykład zwraca `"app"` :

```json
"[skip('webapp', 3)]"
```

### <a name="example-array-skip"></a>Przykład: pomijanie tablicy

Przyjmij `element1` wartość Returns `[1, 2, 3]` . Poniższy przykład zwraca `[3]` :

```json
"[skip(steps('demoStep').element1, 2)]"
```

### <a name="example-object-skip"></a>Przykład: obiekt pomija

Przyjmij `element1` zwrot:

```json
{
  "key1": "Linux",
  "key2": "Windows"
}
```
Poniższy przykład zwraca `{"key2": "Windows"}` :

```json
"[skip(steps('demoStep').element1, 1)]"
```

## <a name="split"></a>split

Zwraca tablicę ciągów zawierającą podciągi danych wejściowych rozdzielanych przez separator.

Poniższy przykład zwraca tablicę `[ "555", "867", "5309" ]` .

```json
"[split('555-867-5309', '-')]"
```

## <a name="take"></a>take (pobierz)

Zwraca określoną liczbę znaków sąsiadujących od początku ciągu, określoną liczbę ciągłych wartości od początku tablicy lub określoną liczbę ciągłych kluczy i wartości z początku obiektu.

### <a name="example-string-take"></a>Przykład: przyjmuje ciąg

Poniższy przykład zwraca `"web"` :

```json
"[take('webapp', 3)]"
```

### <a name="example-array-take"></a>Przykład: wykonaj tablicę

Przyjmij `element1` wartość Returns `[1, 2, 3]` . Poniższy przykład zwraca `[1, 2]` :

```json
"[take(steps('demoStep').element1, 2)]"
```

### <a name="example-object-take"></a>Przykład: obiekt Take

Przyjmij `element1` zwrot:

```json
{
  "key1": "Linux",
  "key2": "Windows"
}
```

Poniższy przykład zwraca `{"key1": "Linux"}` :

```json
"[take(steps('demoStep').element1, 1)]"
```

## <a name="next-steps"></a>Następne kroki

* Aby zapoznać się z wprowadzeniem do Azure Resource Manager, zobacz [Azure Resource Manager omówienie](../management/overview.md).
