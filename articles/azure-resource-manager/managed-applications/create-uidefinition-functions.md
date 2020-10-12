---
title: Tworzenie funkcji definicji interfejsu użytkownika
description: Opisuje funkcje, które mają być używane podczas konstruowania definicji interfejsu użytkownika dla Azure Managed Applications
author: tfitzmac
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: tomfitz
ms.openlocfilehash: e4255f0d42e28a72ad55d9b7f81d0dc49b2950cb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87041000"
---
# <a name="createuidefinition-functions"></a>Funkcje CreateUiDefinition

Ten artykuł zawiera omówienie obsługiwanych funkcji programu CreateUiDefinition.

## <a name="function-syntax"></a>Składnia funkcji

Aby użyć funkcji, należy ująć wywołanie z nawiasami kwadratowymi. Na przykład:

```json
"[function()]"
```

Ciągi i inne funkcje mogą być przywoływane jako parametry dla funkcji, ale ciągi muszą być ujęte w apostrofy. Na przykład:

```json
"[fn1(fn2(), 'demo text')]"
```

Jeśli ma to zastosowanie, można odwoływać się do właściwości danych wyjściowych funkcji przy użyciu operatora kropki. Na przykład:

```json
"[func().prop1]"
```

## <a name="collection-functions"></a>Funkcje zbierania

Te funkcje mogą być używane z kolekcjami, takimi jak ciągi JSON, tablice i obiekty.

* [wyświetlana](create-ui-definition-collection-functions.md#contains)
* [puste](create-ui-definition-collection-functions.md#empty)
* [filtru](create-ui-definition-collection-functions.md#filter)
* [pierwszego](create-ui-definition-collection-functions.md#first)
* [ostatniego](create-ui-definition-collection-functions.md#last)
* [length](create-ui-definition-collection-functions.md#length)
* [zmapować](create-ui-definition-collection-functions.md#map)
* [Skocz](create-ui-definition-collection-functions.md#skip)
* [podziału](create-ui-definition-collection-functions.md#split)
* [take (pobierz)](create-ui-definition-collection-functions.md#take)

## <a name="conversion-functions"></a>Funkcje konwersji

Te funkcje mogą służyć do konwertowania wartości między typami danych JSON a kodowaniem.

* [bool](create-ui-definition-conversion-functions.md#bool)
* [decodeBase64](create-ui-definition-conversion-functions.md#decodebase64)
* [decodeUriComponent](create-ui-definition-conversion-functions.md#decodeuricomponent)
* [encodeBase64](create-ui-definition-conversion-functions.md#encodebase64)
* [encodeURIComponent —](create-ui-definition-conversion-functions.md#encodeuricomponent)
* [liczba zmiennoprzecinkowa](create-ui-definition-conversion-functions.md#float)
* [int](create-ui-definition-conversion-functions.md#int)
* [przetwarzania](create-ui-definition-conversion-functions.md#parse)
* [parametry](create-ui-definition-conversion-functions.md#string)

## <a name="date-functions"></a>Funkcje daty

* [addgodz.](create-ui-definition-date-functions.md#addhours)
* [addminut](create-ui-definition-date-functions.md#addminutes)
* [AddSeconds](create-ui-definition-date-functions.md#addseconds)
* [utcNow](create-ui-definition-date-functions.md#utcnow)

## <a name="logical-functions"></a>Funkcje logiczne

Te funkcje mogą być używane w warunkach. Niektóre funkcje mogą nie obsługiwać wszystkich typów danych JSON.

* [and](create-ui-definition-logical-functions.md#and)
* [łączonych](create-ui-definition-logical-functions.md#coalesce)
* [equals](create-ui-definition-logical-functions.md#equals)
* [greater](create-ui-definition-logical-functions.md#greater)
* [greaterOrEquals](create-ui-definition-logical-functions.md#greaterorequals)
* [if](create-ui-definition-logical-functions.md#if)
* [wcześniejsz](create-ui-definition-logical-functions.md#less)
* [lessOrEquals](create-ui-definition-logical-functions.md#lessorequals)
* [niemożliwe](create-ui-definition-logical-functions.md#not)
* [oraz](create-ui-definition-logical-functions.md#or)

## <a name="math-functions"></a>Funkcje matematyczne

* [add](create-ui-definition-math-functions.md#add)
* [CEIL —](create-ui-definition-math-functions.md#ceil)
* [div](create-ui-definition-math-functions.md#div)
* [wykładzin](create-ui-definition-math-functions.md#floor)
* [Maksymalny](create-ui-definition-math-functions.md#max)
* [długości](create-ui-definition-math-functions.md#min)
* [Funkcja](create-ui-definition-math-functions.md#mod)
* [mul](create-ui-definition-math-functions.md#mul)
* [Rand](create-ui-definition-math-functions.md#rand)
* [zakresu](create-ui-definition-math-functions.md#range)
* [Sub](create-ui-definition-math-functions.md#sub)

## <a name="referencing-functions"></a>Funkcje odwołań

* [nazwie](create-ui-definition-referencing-functions.md#basics)
* [przeniesienie](create-ui-definition-referencing-functions.md#location)
* [resourceGroup](create-ui-definition-referencing-functions.md#resourcegroup)
* [czynnooci](create-ui-definition-referencing-functions.md#steps)
* [ramach](create-ui-definition-referencing-functions.md#subscription)

## <a name="string-functions"></a>Funkcje ciągów

* [Concat](create-ui-definition-string-functions.md#concat)
* [endsWith](create-ui-definition-string-functions.md#endswith)
* [guid](create-ui-definition-string-functions.md#guid)
* [indexOf](create-ui-definition-string-functions.md#indexof)
* [lastIndexOf](create-ui-definition-string-functions.md#lastindexof)
* [stępować](create-ui-definition-string-functions.md#replace)
* [startsWith](create-ui-definition-string-functions.md#startswith)
* [podciąg](create-ui-definition-string-functions.md#substring)
* [toLower](create-ui-definition-string-functions.md#tolower)
* [toUpper](create-ui-definition-string-functions.md#toupper)

## <a name="next-steps"></a>Następne kroki

* Aby zapoznać się z wprowadzeniem do Azure Resource Manager, zobacz [Azure Resource Manager omówienie](../management/overview.md).
