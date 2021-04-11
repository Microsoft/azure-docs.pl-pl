---
title: Operatory porównania Bicep
description: Opisuje operatory porównania Bicep, które porównują wartości.
ms.topic: conceptual
ms.date: 04/07/2021
ms.openlocfilehash: c382ef355131d271d9f4fa4a978a807b9aac1e4f
ms.sourcegitcommit: c3739cb161a6f39a9c3d1666ba5ee946e62a7ac3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2021
ms.locfileid: "107211322"
---
# <a name="bicep-comparison-operators"></a>Operatory porównania Bicep

Operatory porównania porównują wartości i zwracają albo `true` `false` . Aby uruchomić przykłady, użyj interfejsu wiersza polecenia platformy Azure lub Azure PowerShell, aby [wdrożyć plik Bicep](bicep-tutorial-create-first-bicep.md#deploy-bicep-file).

| Operator | Nazwa |
| ---- | ---- |
| `>=` | [Większe niż lub równe](#greater-than-or-equal-) |
| `>`  | [Większe niż](#greater-than-) |
| `<=` | [Mniejsze niż lub równe](#less-than-or-equal-) |
| `<`  | [Mniejsze niż](#less-than-) |
| `==` | [Równa się](#equals-) |
| `!=` | [Nie równa się](#not-equal-) |
| `=~` | [Bez uwzględniania wielkości liter](#equal-case-insensitive-) |
| `!~` | [Bez uwzględniania wielkości liter](#not-equal-case-insensitive-) |

## <a name="greater-than-or-equal-"></a>Większe niż lub równe >=

`operand1 >= operand2`

Oblicza, czy pierwsza wartość jest większa lub równa drugiej wartości.

### <a name="operands"></a>Argumenty operacji

| Parametru | Typ | Opis |
| ---- | ---- | ---- |
| `operand1` | Liczba całkowita, ciąg | Pierwsza wartość w porównaniu. |
| `operand2` | Liczba całkowita, ciąg | Druga wartość w porównaniu. |

### <a name="return-value"></a>Wartość zwracana

Jeśli pierwsza wartość jest większa lub równa drugiej wartości, `true` jest zwracana. W przeciwnym razie `false` jest zwracany.

### <a name="example"></a>Przykład

Porównywana jest para liczb całkowitych i par ciągów.

```bicep
param firstInt int = 10
param secondInt int = 5

param firstString string = 'A'
param secondString string = 'A'

output intGtE bool = firstInt >= secondInt
output stringGtE bool = firstString >= secondString
```

Dane wyjściowe z przykładu:

| Nazwa | Typ | Wartość |
| ---- | ---- | ---- |
| `intGtE` | boolean | true |
| `stringGtE` | boolean | true |

## <a name="greater-than-"></a>Większe niż >

`operand1 > operand2`

Oblicza, czy pierwsza wartość jest większa od drugiej wartości.

### <a name="operands"></a>Argumenty operacji

| Parametru | Typ | Opis |
| ---- | ---- | ---- |
| `operand1` | Liczba całkowita, ciąg | Pierwsza wartość w porównaniu. |
| `operand2` | Liczba całkowita, ciąg | Druga wartość w porównaniu. |

### <a name="return-value"></a>Wartość zwracana

Jeśli pierwsza wartość jest większa niż druga wartość, `true` jest zwracana. W przeciwnym razie `false` jest zwracany.

### <a name="example"></a>Przykład

Porównywana jest para liczb całkowitych i par ciągów.

```bicep
param firstInt int = 10
param secondInt int = 5

param firstString string = 'bend'
param secondString string = 'band'

output intGt bool = firstInt > secondInt
output stringGt bool = firstString > secondString
```

Dane wyjściowe z przykładu:

Wartość **e** na **zgięciu** sprawia, że pierwszy ciąg jest większy.

| Nazwa | Typ | Wartość |
| ---- | ---- | ---- |
| `intGt` | boolean | true |
| `stringGt` | boolean | true |

## <a name="less-than-or-equal-"></a>Mniejsze niż lub równe <=

`operand1 <= operand2`

Oblicza, czy pierwsza wartość jest mniejsza lub równa drugiej wartości.

### <a name="operands"></a>Argumenty operacji

| Parametru | Typ | Opis |
| ---- | ---- | ---- |
| `operand1` | Liczba całkowita, ciąg | Pierwsza wartość w porównaniu. |
| `operand2` | Liczba całkowita, ciąg | Druga wartość w porównaniu. |

### <a name="return-value"></a>Wartość zwracana

Jeśli pierwsza wartość jest mniejsza lub równa drugiej wartości, `true` jest zwracana. W przeciwnym razie `false` jest zwracany.

### <a name="example"></a>Przykład

Porównywana jest para liczb całkowitych i par ciągów.

```bicep
param firstInt int = 5
param secondInt int = 10

param firstString string = 'demo'
param secondString string = 'demo'

output intLtE bool = firstInt <= secondInt
output stringLtE bool = firstString <= secondString
```

Dane wyjściowe z przykładu:

| Nazwa | Typ | Wartość |
| ---- | ---- | ---- |
| `intLtE` | boolean | true |
| `stringLtE` | boolean | true |

## <a name="less-than-"></a>Mniejsze niż <

`operand1 < operand2`

Oblicza, czy pierwsza wartość jest mniejsza od drugiej wartości.

### <a name="operands"></a>Argumenty operacji

| Parametru | Typ | Opis |
| ---- | ---- | ---- |
| `operand1` | Liczba całkowita, ciąg | Pierwsza wartość w porównaniu. |
| `operand2` | Liczba całkowita, ciąg | Druga wartość w porównaniu. |

### <a name="return-value"></a>Wartość zwracana

Jeśli pierwsza wartość jest mniejsza od drugiej wartości, `true` jest zwracana. W przeciwnym razie `false` jest zwracany.

### <a name="example"></a>Przykład

Porównywana jest para liczb całkowitych i par ciągów.

```bicep
param firstInt int = 5
param secondInt int = 10

param firstString string = 'demo'
param secondString string = 'Demo'

output intLt bool = firstInt < secondInt
output stringLt bool = firstString < secondString
```

Dane wyjściowe z przykładu:

Ten ciąg jest `true` przyczyną, że małe litery są mniejsze niż wielkie litery.

| Nazwa | Typ | Wartość |
| ---- | ---- | ---- |
| `intLt` | boolean | true |
| `stringLt` | boolean | true |

## <a name="equals-"></a>Równa się = =

`operand1 == operand2`

Oblicza, czy wartości są równe.

### <a name="operands"></a>Argumenty operacji

| Parametru | Typ | Opis |
| ---- | ---- | ---- |
| `operand1` | ciąg, liczba całkowita, wartość logiczna, tablica, obiekt | Pierwsza wartość w porównaniu. |
| `operand2` | ciąg, liczba całkowita, wartość logiczna, tablica, obiekt | Druga wartość w porównaniu. |

### <a name="return-value"></a>Wartość zwracana

Jeśli operandy są równe, `true` jest zwracana. Jeśli operandy są inne, `false` są zwracane.

### <a name="example"></a>Przykład

Pary liczb całkowitych, ciągów i wartości logicznych są porównywane.

```bicep
param firstInt int = 5
param secondInt int = 5

param firstString string = 'demo'
param secondString string = 'demo'

param firstBool bool = true
param secondBool bool = true

output intEqual bool = firstInt == secondInt
output stringEqual bool = firstString == secondString
output boolEqual bool = firstBool == secondBool
```

Dane wyjściowe z przykładu:

| Nazwa | Typ | Wartość |
| ---- | ---- | ---- |
| `intEqual` | boolean | true |
| `stringEqual` | boolean | true |
| `boolEqual` | boolean | true |

## <a name="not-equal-"></a>Nie równa się! =

`operand1 != operand2`

Oblicza, czy dwie wartości **nie** są równe.

### <a name="operands"></a>Argumenty operacji

| Parametru | Typ | Opis |
| ---- | ---- | ---- |
| `operand1` | ciąg, liczba całkowita, wartość logiczna, tablica, obiekt | Pierwsza wartość w porównaniu. |
| `operand2` | ciąg, liczba całkowita, wartość logiczna, tablica, obiekt | Druga wartość w porównaniu. |

### <a name="return-value"></a>Wartość zwracana

Jeśli operandy **nie** są równe, `true` jest zwracana. Jeśli operandy są równe, `false` jest zwracana.

### <a name="example"></a>Przykład

Pary liczb całkowitych, ciągów i wartości logicznych są porównywane.

```bicep
param firstInt int = 10
param secondInt int = 5

param firstString string = 'demo'
param secondString string = 'test'

param firstBool bool = false
param secondBool bool = true

output intNotEqual bool = firstInt != secondInt
output stringNotEqual bool = firstString != secondString
output boolNotEqual bool = firstBool != secondBool
```

Dane wyjściowe z przykładu:

| Nazwa | Typ | Wartość |
| ---- | ---- | ---- |
| `intNotEqual` | boolean | true |
| `stringNotEqual` | boolean | true |
| `boolNotEqual` | boolean | true |

## <a name="equal-case-insensitive-"></a>Bez uwzględniania wielkości liter = ~

`operand1 =~ operand2`

Ignoruje wielkość liter, aby określić, czy dwie wartości są równe.

### <a name="operands"></a>Argumenty operacji

| Parametru | Typ | Opis |
| ---- | ---- | ---- |
| `operand1`  | ciąg | Pierwszy ciąg w porównaniu. |
| `operand2`  | ciąg | Drugi ciąg w porównaniu. |

### <a name="return-value"></a>Wartość zwracana

Jeśli ciągi są równe, `true` jest zwracana. W przeciwnym razie `false` jest zwracany.

### <a name="example"></a>Przykład

Porównuje ciągi, które używają wielkich liter.

```bicep
param firstString string = 'demo'
param secondString string = 'DEMO'

param thirdString string = 'demo'
param fourthString string = 'TEST'

output strEqual1 bool = firstString =~ secondString
output strEqual2 bool = thirdString =~ fourthString
```

Dane wyjściowe z przykładu:

| Nazwa | Typ | Wartość |
| ---- | ---- | ---- |
| `strEqual1` | boolean | true |
| `strEqual2` | boolean | fałsz |

## <a name="not-equal-case-insensitive-"></a>Bez uwzględniania wielkości liter! ~

`operand1 !~ operand2`

Ignoruje wielkość liter, aby określić, czy dwie wartości **nie** są równe.

### <a name="operands"></a>Argumenty operacji

| Parametru | Typ | Opis |
| ---- | ---- | ---- |
| `operand1` | ciąg | Pierwszy ciąg w porównaniu. |
| `operand2` | ciąg | Drugi ciąg w porównaniu. |

### <a name="return-value"></a>Wartość zwracana

Jeśli ciągi **nie** są równe, `true` jest zwracana. W przeciwnym razie `false` jest zwracany.

### <a name="example"></a>Przykład

Porównuje ciągi, które używają wielkich liter.

```bicep
param firstString string = 'demo'
param secondString string = 'TEST'

param thirdString string = 'demo'
param fourthString string = 'DeMo'

output strNotEqual1 bool = firstString !~ secondString
output strEqual2 bool = thirdString !~ fourthString
```

Dane wyjściowe z przykładu:

| Nazwa | Typ | Wartość |
| ---- | ---- | ---- |
| `strNotEqual1` | boolean | true |
| `strNotEqual2` | boolean | fałsz |

## <a name="next-steps"></a>Następne kroki

- Aby utworzyć plik Bicep, zobacz [Samouczek: Tworzenie i wdrażanie pierwszego pliku Azure Resource Manager Bicep](bicep-tutorial-create-first-bicep.md).
- Informacje o sposobach rozwiązywania błędów typu Bicep można znaleźć w [dowolnej funkcji dla Bicep](template-functions-any.md).
- Aby porównać składnię dla Bicep i JSON, zobacz [porównanie JSON i Bicep dla szablonów](compare-template-syntax.md).
- Przykłady funkcji szablonu Bicep i ARM można znaleźć w temacie [funkcje szablonu ARM](template-functions.md).
