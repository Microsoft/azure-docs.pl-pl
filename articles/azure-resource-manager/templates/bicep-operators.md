---
title: Operatory bicep
description: W tym artykule opisano operatory bicep dostępne Azure Resource Manager wdrożeniach.
ms.topic: conceptual
ms.date: 04/15/2021
ms.openlocfilehash: 0838ebf6bc03f4237ef76e07f1eb6f25aa996fc0
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107537832"
---
# <a name="bicep-operators"></a>Operatory bicep

W tym artykule opisano operatory bicep, które są dostępne podczas tworzenia szablonu bicep i używania Azure Resource Manager do wdrażania zasobów. Operatory służą do obliczania wartości, porównywania wartości lub oceniania warunków. Istnieją trzy typy operatorów bicep:

- [Porównanie](#comparison)
- [Logiczne](#logical)
- [Liczbowe](#numeric)

Dołączenie wyrażenia między `(` elementami i `)` umożliwia zastąpienie domyślnego pierwszeństwa operatora bicep. Na przykład wyrażenie x + y /z oblicza najpierw dzielenie, a następnie dodawanie. Jednak wyrażenie (x + y) /z oblicza wartość dodatku jako pierwszą i drugą dzielenie.

## <a name="comparison"></a>Porównanie

Operatory porównania porównują wartości i zwracają wartość `true` lub `false` .

| Operator | Nazwa | Opis |
| ---- | ---- | ---- |
| `>=` | [Większe niż lub równe](bicep-operators-comparison.md#greater-than-or-equal-) | Ocenia, czy pierwsza wartość jest większa niż lub równa drugiej wartości. |
| `>`  | [Większe niż](bicep-operators-comparison.md#greater-than-) | Ocenia, czy pierwsza wartość jest większa niż druga wartość. |
| `<=` | [Mniejsze niż lub równe](bicep-operators-comparison.md#less-than-or-equal-) | Ocenia, czy pierwsza wartość jest mniejsza lub równa drugiej wartości. |
| `<`  | [Mniejsze niż](bicep-operators-comparison.md#less-than-) | Ocenia, czy pierwsza wartość jest mniejsza niż druga wartość. |
| `==` | [Równa się](bicep-operators-comparison.md#equals-) | Ocenia, czy dwie wartości są równe. |
| `!=` | [Nie równa się](bicep-operators-comparison.md#not-equal-) | Ocenia, czy dwie wartości nie **są** równe. |
| `=~` | [Bez uwzględniania równych liter](bicep-operators-comparison.md#equal-case-insensitive-) | Ignoruje przypadek, aby określić, czy dwie wartości są równe. |
| `!~` | [Bez uwzględniania liter](bicep-operators-comparison.md#not-equal-case-insensitive-) | Ignoruje przypadek, aby określić, czy dwie wartości **nie są** równe. |

## <a name="logical"></a>Wartości logiczne

Operatory logiczne oceniają wartości logiczne, zwracają wartości inne niż null lub oceniają wyrażenie warunkowe.

| Operator | Nazwa | Opis |
| ---- | ---- | ---- |
| `&&` | [I](bicep-operators-logical.md#and-) | Zwraca `true` wartość , jeśli wszystkie wartości mają wartość true. |
| `||`| [Lub](bicep-operators-logical.md#or-) | Zwraca `true` wartość , jeśli którakolwiek z wartości ma wartość true. |
| `!` | [Nie](bicep-operators-logical.md#not-) | Neguje wartość logiczną. |
| `??` | [Łączonej](bicep-operators-logical.md#coalesce-) | Zwraca pierwszą wartość niezerową. |
| `?` `:` | [Wyrażenie warunkowe](bicep-operators-logical.md#conditional-expression--) | Oblicza warunek dla wartości true lub false i zwraca wartość. |

## <a name="numeric"></a>Numeryczny

Operatory liczbowe używają liczb całkowitych do wykonywania obliczeń i zwracania wartości całkowitych.

| Operator | Nazwa | Opis |
| ---- | ---- | ---- |
| `*` | [Mnożenie](bicep-operators-numeric.md#multiply-) | Mnoży dwie liczby całkowite. |
| `/` | [Podzielić](bicep-operators-numeric.md#divide-) | Dzieli liczbę całkowitą przez liczbę całkowitą. |
| `%` | [Modulo](bicep-operators-numeric.md#modulo-) | Dzieli liczbę całkowitą przez liczbę całkowitą i zwraca resztę. |
| `+` | [Dodaj](bicep-operators-numeric.md#add-) | Dodaje dwie liczby całkowite. |
| `-` | [Odejmowanie](bicep-operators-numeric.md#subtract--) | Odejmuje liczbę całkowitą od liczby całkowitej. |
| `-` | [Minus](bicep-operators-numeric.md#minus--) | Mnoży liczbę całkowitą przez `-1` . |

> [!NOTE]
> Odejmij i odejmij użyj tego samego operatora. Funkcja jest inna, ponieważ odejmowanie używa dwóch operandów, a minus używa jednego operandu.

## <a name="next-steps"></a>Następne kroki

- Aby utworzyć plik Bicep, zobacz [Samouczek: tworzenie i wdrażanie pierwszego pliku Azure Resource Manager Bicep.](bicep-tutorial-create-first-bicep.md)
- Aby uzyskać informacje na temat sposobu rozwiązywania błędów typu Bicep, zobacz [Dowolna funkcja dla bicep](template-functions-any.md).
- Aby porównać składnię dla danych Bicep i JSON, zobacz Porównanie danych [JSON](compare-template-syntax.md)i Bicep dla szablonów .
- Aby uzyskać przykłady funkcji szablonu Bicep i ARM, zobacz [Arm template functions (Funkcje szablonu usługi ARM).](template-functions.md)
