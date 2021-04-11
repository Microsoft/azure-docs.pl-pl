---
title: Operatory logiczne Bicep
description: Opisuje operatory logiczne Bicep, które obliczają warunki.
ms.topic: conceptual
ms.date: 04/07/2021
ms.openlocfilehash: 1eecbe589ecf08e32246dd97d137c60fa7b5078f
ms.sourcegitcommit: c3739cb161a6f39a9c3d1666ba5ee946e62a7ac3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2021
ms.locfileid: "107211338"
---
# <a name="bicep-logical-operators"></a>Operatory logiczne Bicep

Operatory logiczne obliczają wartości logiczne, zwracają wartości inne niż null lub obliczają wyrażenie warunkowe. Aby uruchomić przykłady, użyj interfejsu wiersza polecenia platformy Azure lub Azure PowerShell, aby [wdrożyć plik Bicep](bicep-tutorial-create-first-bicep.md#deploy-bicep-file).

| Operator | Nazwa |
| ---- | ---- |
| `&&` | [Lub](#and-) |
| `||` | [Lub](#or-) |
| `!` | [Niemożliwe](#not-) |
| `??` | [Łączonych](#coalesce-) |
| `?` `:` | [Wyrażenie warunkowe](#conditional-expression--) |

## <a name="and-"></a>I &&

`operand1 && operand2`

Określa, czy obie wartości mają wartość true.

### <a name="operands"></a>Argumenty operacji

| Parametru | Typ | Opis |
| ---- | ---- | ---- |
| `operand1` | boolean | Pierwsza wartość do sprawdzenia, czy wartość jest równa true. |
| `operand2` | boolean | Druga wartość do sprawdzenia, czy wartość jest równa true. |
| Więcej operandów | boolean | Można uwzględnić więcej operandów. |

### <a name="return-value"></a>Wartość zwracana

`True` gdy obie wartości mają wartość true, w przeciwnym razie `false` jest zwracany.

### <a name="example"></a>Przykład

Oblicza zestaw wartości parametrów i zestawu wyrażeń.

```bicep
param operand1 bool = true
param operand2 bool = true

output andResultParm bool = operand1 && operand2
output andResultExp bool = bool(10 >= 10) && bool(5 > 2)
```

Dane wyjściowe z przykładu:

| Nazwa | Typ | Wartość |
| ---- | ---- | ---- |
| `andResultParm` | boolean | true |
| `andResultExp` | boolean | true |

## <a name="or-"></a>Lub | |

`operand1 || operand2`

Określa, czy wartość jest równa true.

### <a name="operands"></a>Argumenty operacji

| Parametru | Typ | Opis |
| ---- | ---- | ---- |
| `operand1` | boolean | Pierwsza wartość do sprawdzenia, czy wartość jest równa true. |
| `operand2` | boolean | Druga wartość do sprawdzenia, czy wartość jest równa true. |
| Więcej operandów | boolean | Można uwzględnić więcej operandów. |

### <a name="return-value"></a>Wartość zwracana

`True` gdy każda wartość jest równa true, w przeciwnym razie `false` jest zwracana.

### <a name="example"></a>Przykład

Oblicza zestaw wartości parametrów i zestawu wyrażeń.

```bicep
param operand1 bool = true
param operand2 bool = false

output orResultParm bool = operand1 || operand2
output orResultExp bool = bool(10 >= 10) || bool(5 < 2)
```

Dane wyjściowe z przykładu:

| Nazwa | Typ | Wartość |
| ---- | ---- | ---- |
| `orResultParm` | boolean | true |
| `orResultExp` | boolean | true |

## <a name="not-"></a>Niemożliwe!

`!boolValue`

Negacja wartości logicznej.

### <a name="operand"></a>Parametru

| Parametru | Typ | Opis |
| ---- | ---- | ---- |
| `boolValue` | boolean | Wartość logiczna, która jest Negacja. |

### <a name="return-value"></a>Wartość zwracana

Wyklucza początkową wartość i zwraca wartość logiczną. Jeśli wartość początkowa to `true` , `false` jest zwracana.

### <a name="example"></a>Przykład

`not`Operator negacji wartości. Wartości mogą być opakowane z nawiasami.

```bicep
param initTrue bool = true
param initFalse bool = false

output startedTrue bool = !(initTrue)
output startedFalse bool = !initFalse
```

Dane wyjściowe z przykładu:

| Nazwa | Typ | Wartość |
| ---- | ---- | ---- |
| `startedTrue` | boolean | fałsz |
| `startedFalse` | boolean | true |

## <a name="coalesce-"></a>Łączenia?

`operand1 ?? operand2`

Zwraca pierwszą wartość różną od zera z operandów.

### <a name="operands"></a>Argumenty operacji

| Parametru | Typ | Opis |
| ---- | ---- | ---- |
| `operand1` | ciąg, liczba całkowita, wartość logiczna, obiekt, tablica | Wartość do przetestowania `null` . |
| `operand2` | ciąg, liczba całkowita, wartość logiczna, obiekt, tablica | Wartość do przetestowania `null` . |
| Więcej operandów | ciąg, liczba całkowita, wartość logiczna, obiekt, tablica | Wartość do przetestowania `null` . |

### <a name="return-value"></a>Wartość zwracana

Zwraca pierwszą wartość o wartości innej niż null. Puste ciągi, puste tablice i puste obiekty nie są `null` i \<empty> zwracana jest wartość.

### <a name="example"></a>Przykład

Wyjściowe instrukcje zwracają wartości inne niż null. Typ wyjściowy musi być zgodny z typem w porównaniu lub generowany jest błąd.

```bicep
param myObject object = {
  'isnull1': null
  'isnull2': null
  'string': 'demoString'
  'emptystr': ''
  'integer': 10
  }

output nonNullStr string = myObject.isnull1 ?? myObject.string ?? myObject.isnull2
output nonNullInt int = myObject.isnull1 ?? myObject.integer ?? myObject.isnull2
output nonNullEmpty string = myObject.isnull1 ?? myObject.emptystr ?? myObject.string ?? myObject.isnull2
```

Dane wyjściowe z przykładu:

| Nazwa | Typ | Wartość |
| ---- | ---- | ---- |
| `nonNullStr` | ciąg | demoString |
| `nonNullInt` | int | 10 |
| `nonNullEmpty` | ciąg | \<empty> |

## <a name="conditional-expression--"></a>Wyrażenie warunkowe? :

`condition ? true-value : false-value`

Oblicza warunek i zwraca wartość, czy warunek ma wartość true lub false.

### <a name="operands"></a>Argumenty operacji

| Parametru | Typ | Opis |
| ---- | ---- | ---- |
| `condition` | boolean | Warunek do obliczenia jako wartość true lub false. |
| `true-value` | ciąg, liczba całkowita, wartość logiczna, obiekt, tablica | Wartość, gdy warunek ma wartość true. |
| `false-value` | ciąg, liczba całkowita, wartość logiczna, obiekt, tablica | Wartość, gdy warunek ma wartość false. |

### <a name="example"></a>Przykład

Ten przykład szacuje początkową parametr i zwraca wartość, czy warunek ma wartość true lub false.

```bicep
param initValue bool = true

output outValue string = initValue ? 'true value' : 'false value'
```

Dane wyjściowe z przykładu:

| Nazwa | Typ | Wartość |
| ---- | ---- | ---- |
| `outValue` | ciąg | wartość prawdziwa |

## <a name="next-steps"></a>Następne kroki

- Aby utworzyć plik Bicep, zobacz [Samouczek: Tworzenie i wdrażanie pierwszego pliku Azure Resource Manager Bicep](bicep-tutorial-create-first-bicep.md).
- Informacje o sposobach rozwiązywania błędów typu Bicep można znaleźć w [dowolnej funkcji dla Bicep](template-functions-any.md).
- Aby porównać składnię dla Bicep i JSON, zobacz [porównanie JSON i Bicep dla szablonów](compare-template-syntax.md).
- Przykłady funkcji szablonu Bicep i ARM można znaleźć w temacie [funkcje szablonu ARM](template-functions.md).
