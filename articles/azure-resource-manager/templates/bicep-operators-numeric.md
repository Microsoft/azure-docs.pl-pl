---
title: Bicep Operatory liczbowe
description: Opisuje Bicep Operatory liczbowe, które obliczają wartości.
ms.topic: conceptual
ms.date: 04/07/2021
ms.openlocfilehash: f2c7f722a3f13648c94b69039d31e5095a3256f7
ms.sourcegitcommit: c3739cb161a6f39a9c3d1666ba5ee946e62a7ac3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2021
ms.locfileid: "107211317"
---
# <a name="bicep-numeric-operators"></a>Bicep Operatory liczbowe

Operatory liczbowe używają liczb całkowitych do wykonywania obliczeń i zwracania wartości całkowitych. Aby uruchomić przykłady, użyj interfejsu wiersza polecenia platformy Azure lub Azure PowerShell, aby [wdrożyć plik Bicep](bicep-tutorial-create-first-bicep.md#deploy-bicep-file).

| Operator | Nazwa |
| ---- | ---- |
| `*` | [Mnożenie](#multiply-) |
| `/` | [Mieszczon](#divide-) |
| `%` | [Modulo](#modulo-) |
| `+` | [Dodaj](#add-) |
| `-` | [Odejmowanie](#subtract--) |
| `-` | [Przed](#minus--) |

> [!NOTE]
> Odejmij i minus Użyj tego samego operatora. Działa inaczej, ponieważ funkcja odejmowania używa dwóch operandów, a minus używa jednego operandu.

## <a name="multiply-"></a>Mnożenia

`operand1 * operand2`

Mnoży dwie liczby całkowite.

### <a name="operands"></a>Argumenty operacji

| Parametru | Typ | Opis |
| ---- | ---- | ---- |
| `operand1`  | liczba całkowita | Liczba do pomnożenia. |
| `operand2`  | liczba całkowita  | Mnożnik liczby. |

### <a name="return-value"></a>Wartość zwracana

Mnożenie zwraca produkt jako liczbę całkowitą.

### <a name="example"></a>Przykład

Dwie liczby całkowite są mnożone i zwracają produkt.

```bicep
param firstInt int = 5
param secondInt int = 2

output product int = firstInt * secondInt
```

Dane wyjściowe z przykładu:

| Nazwa | Typ | Wartość |
| ---- | ---- | ---- |
| `product` | liczba całkowita | 10 |

## <a name="divide-"></a>Mieszczon

`operand1 / operand2`

Dzieli liczbę całkowitą przez liczbę całkowitą.

### <a name="operands"></a>Argumenty operacji

| Parametru | Typ | Opis |
| ---- | ---- | ---- |
| `operand1` | liczba całkowita | Podzielona liczba całkowita. |
| `operand2` | liczba całkowita | Liczba całkowita, która jest używana na potrzeby dzielenia. Nie może być równa zero. |

### <a name="return-value"></a>Wartość zwracana

Podział zwraca iloraz jako liczbę całkowitą.

### <a name="example"></a>Przykład

Dwie liczby całkowite są podzielone i zwracają iloraz.

```bicep
param firstInt int = 10
param secondInt int = 2

output quotient int = firstInt / secondInt
```

Dane wyjściowe z przykładu:

| Nazwa | Typ | Wartość |
| ---- | ---- | ---- |
| `quotient` | liczba całkowita | 5 |

## <a name="modulo-"></a>Operator

`operand1 % operand2`

Dzieli liczbę całkowitą przez liczbę całkowitą i zwraca resztę.

### <a name="operands"></a>Argumenty operacji

| Parametru | Typ | Opis |
| ---- | ---- | ---- |
| `operand1`  | liczba całkowita  | Podzielona liczba całkowita. |
| `operand2`  | liczba całkowita  | Liczba całkowita, która jest używana na potrzeby dzielenia. Nie może mieć wartości 0. |

### <a name="return-value"></a>Wartość zwracana

Reszta jest zwracana jako liczba całkowita. Jeśli podział nie produkuje reszty, zwracana jest wartość 0.

### <a name="example"></a>Przykład

Dwie pary liczb całkowitych są podzielone i zwracają reszty.

```bicep
param firstInt int = 10
param secondInt int = 3

param thirdInt int = 8
param fourthInt int = 4

output remainder int = firstInt % secondInt
output zeroRemainder int = thirdInt % fourthInt
```

Dane wyjściowe z przykładu:

| Nazwa | Typ | Wartość |
| ---- | ---- | ---- |
| `remainder` | liczba całkowita | 1 |
| `zeroRemainder` | liczba całkowita | 0 |

## <a name="add-"></a>Dodaj +

`operand1 + operand2`

Dodaje dwie liczby całkowite.

### <a name="operands"></a>Argumenty operacji

| Parametru | Typ | Opis |
| ---- | ---- | ---- |
| `operand1` | liczba całkowita | Liczba do dodania. |
| `operand2` | liczba całkowita | Liczba, która jest dodawana do liczby. |

### <a name="return-value"></a>Wartość zwracana

Dodanie zwraca sumę jako liczbę całkowitą.

### <a name="example"></a>Przykład

Dwie liczby całkowite są dodawane i zwracają sumę.

```bicep
param firstInt int = 10
param secondInt int = 2

output sum int = firstInt + secondInt
```

Dane wyjściowe z przykładu:

| Nazwa | Typ | Wartość |
| ---- | ---- | ---- |
| `sum` | liczba całkowita | 12 |

## <a name="subtract--"></a>Odjęt

`operand1 - operand2`

Odejmuje liczbę całkowitą z liczby całkowitej.

### <a name="operands"></a>Argumenty operacji

| Parametru | Typ | Opis |
| ---- | ---- | ---- |
| `operand1` | liczba całkowita | Większa liczba odejmowana od. |
| `operand2` | liczba całkowita | Liczba odjęta od większej liczby. |

### <a name="return-value"></a>Wartość zwracana

Odejmowanie zwraca różnicę jako liczbę całkowitą.

### <a name="example"></a>Przykład

Liczba całkowita jest odejmowana i zwraca różnicę.

```bicep
param firstInt int = 10
param secondInt int = 4

output difference int = firstInt - secondInt
```

Dane wyjściowe z przykładu:

| Nazwa | Typ | Wartość |
| ---- | ---- | ---- |
| `difference` | liczba całkowita | 6 |

## <a name="minus--"></a>Przed

`-integerValue`

Mnoży liczbę całkowitą przez `-1` .

### <a name="operand"></a>Parametru

| Parametru | Typ | Opis |
| ---- | ---- | ---- |
| `integerValue` | liczba całkowita | Liczba całkowita pomnożona przez `-1` . |

### <a name="return-value"></a>Wartość zwracana

Liczba całkowita jest mnożona przez `-1` . Dodatnia liczba całkowita zwraca ujemną liczbę całkowitą, a ujemna liczba całkowita zwraca dodatnią liczbę całkowitą. Wartości mogą być opakowane z nawiasami.

### <a name="example"></a>Przykład

```bicep
param posInt int = 10
param negInt int = -20

output startedPositive int = -posInt
output startedNegative int = -(negInt)
```

Dane wyjściowe z przykładu:

| Nazwa | Typ | Wartość |
| ---- | ---- | ---- |
| `startedPositive` | liczba całkowita | -10 |
| `startedNegative` | liczba całkowita | 20 |

## <a name="next-steps"></a>Następne kroki

- Aby utworzyć plik Bicep, zobacz [Samouczek: Tworzenie i wdrażanie pierwszego pliku Azure Resource Manager Bicep](bicep-tutorial-create-first-bicep.md).
- Informacje o sposobach rozwiązywania błędów typu Bicep można znaleźć w [dowolnej funkcji dla Bicep](template-functions-any.md).
- Aby porównać składnię dla Bicep i JSON, zobacz [porównanie JSON i Bicep dla szablonów](compare-template-syntax.md).
- Przykłady funkcji szablonu Bicep i ARM można znaleźć w temacie [funkcje szablonu ARM](template-functions.md).
