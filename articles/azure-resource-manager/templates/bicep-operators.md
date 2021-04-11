---
title: Operatory Bicep
description: Opisuje operatory Bicep dostępne dla wdrożeń Azure Resource Manager.
ms.topic: conceptual
ms.date: 04/07/2021
ms.openlocfilehash: 4bf1005a11b1dcfea9f4b28d6bd3fa7c33e3278f
ms.sourcegitcommit: c3739cb161a6f39a9c3d1666ba5ee946e62a7ac3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2021
ms.locfileid: "107211333"
---
# <a name="bicep-operators"></a>Operatory Bicep

W tym artykule opisano operatory Bicep, które są dostępne podczas tworzenia szablonu Bicep i używania Azure Resource Manager do wdrażania zasobów. Operatory są używane do obliczania wartości, porównywania wartości lub obliczania warunków. Istnieją trzy typy operatorów Bicep: [porównanie](#comparison), [logiczne](#logical)i [liczbowe](#numeric).

## <a name="comparison"></a>Porównanie

Operatory porównania porównują wartości i zwracają albo `true` `false` .

| Operator | Nazwa | Opis |
| ---- | ---- | ---- |
| `>=` | [Większe niż lub równe](bicep-operators-comparison.md#greater-than-or-equal-) | Oblicza, czy pierwsza wartość jest większa lub równa drugiej wartości. |
| `>`  | [Większe niż](bicep-operators-comparison.md#greater-than-) | Oblicza, czy pierwsza wartość jest większa od drugiej wartości. |
| `<=` | [Mniejsze niż lub równe](bicep-operators-comparison.md#less-than-or-equal-) | Oblicza, czy pierwsza wartość jest mniejsza lub równa drugiej wartości. |
| `<`  | [Mniejsze niż](bicep-operators-comparison.md#less-than-) | Oblicza, czy pierwsza wartość jest mniejsza od drugiej wartości. |
| `==` | [Równa się](bicep-operators-comparison.md#equals-) | Oblicza, czy dwie wartości są równe. |
| `!=` | [Nie równa się](bicep-operators-comparison.md#not-equal-) | Oblicza, czy dwie wartości **nie** są równe. |
| `=~` | [Bez uwzględniania wielkości liter](bicep-operators-comparison.md#equal-case-insensitive-) | Ignoruje wielkość liter, aby określić, czy dwie wartości są równe. |
| `!~` | [Bez uwzględniania wielkości liter](bicep-operators-comparison.md#not-equal-case-insensitive-) | Ignoruje wielkość liter, aby określić, czy dwie wartości **nie** są równe. |

## <a name="logical"></a>Wartości logiczne

Operatory logiczne obliczają wartości logiczne, zwracają wartości inne niż null lub obliczają wyrażenie warunkowe.

| Operator | Nazwa | Opis |
| ---- | ---- | ---- |
| `&&` | [Lub](bicep-operators-logical.md#and-) | Zwraca wartość, `true` Jeśli wszystkie wartości mają wartość true. |
| `||`| [Lub](bicep-operators-logical.md#or-) | Zwraca `true` czy wartość jest równa true. |
| `!` | [Niemożliwe](bicep-operators-logical.md#not-) | Negacja wartości logicznej. |
| `??` | [Łączonych](bicep-operators-logical.md#coalesce-) | Zwraca pierwszą wartość o wartości innej niż null. |
| `?` `:` | [Wyrażenie warunkowe](bicep-operators-logical.md#conditional-expression--) | Oblicza warunek dla wartości true lub false i zwraca wartość. |

## <a name="numeric"></a>Numeryczny

Operatory liczbowe używają liczb całkowitych do wykonywania obliczeń i zwracania wartości całkowitych.

| Operator | Nazwa | Opis |
| ---- | ---- | ---- |
| `*` | [Mnożenie](bicep-operators-numeric.md#multiply-) | Mnoży dwie liczby całkowite. |
| `/` | [Mieszczon](bicep-operators-numeric.md#divide-) | Dzieli liczbę całkowitą przez liczbę całkowitą. |
| `%` | [Modulo](bicep-operators-numeric.md#modulo-) | Dzieli liczbę całkowitą przez liczbę całkowitą i zwraca resztę. |
| `+` | [Dodaj](bicep-operators-numeric.md#add-) | Dodaje dwie liczby całkowite. |
| `-` | [Odejmowanie](bicep-operators-numeric.md#subtract--) | Odejmuje liczbę całkowitą z liczby całkowitej. |
| `-` | [Przed](bicep-operators-numeric.md#minus--) | Mnoży liczbę całkowitą przez `-1` . |

> [!NOTE]
> Odejmij i minus Użyj tego samego operatora. Działa inaczej, ponieważ funkcja odejmowania używa dwóch operandów, a minus używa jednego operandu.

## <a name="next-steps"></a>Następne kroki

- Aby utworzyć plik Bicep, zobacz [Samouczek: Tworzenie i wdrażanie pierwszego pliku Azure Resource Manager Bicep](bicep-tutorial-create-first-bicep.md).
- Informacje o sposobach rozwiązywania błędów typu Bicep można znaleźć w [dowolnej funkcji dla Bicep](template-functions-any.md).
- Aby porównać składnię dla Bicep i JSON, zobacz [porównanie JSON i Bicep dla szablonów](compare-template-syntax.md).
- Przykłady funkcji szablonu Bicep i ARM można znaleźć w temacie [funkcje szablonu ARM](template-functions.md).
