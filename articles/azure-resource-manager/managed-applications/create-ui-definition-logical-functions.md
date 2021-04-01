---
title: Tworzenie funkcji logicznych definicji interfejsu użytkownika
description: Opisuje funkcje do wykonywania operacji logicznych.
author: tfitzmac
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: tomfitz
ms.openlocfilehash: 00d2f0eeb5d353c8ebd7ad30f6866f890d6cb42e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "87098180"
---
# <a name="createuidefinition-logical-functions"></a>CreateUiDefinition funkcje logiczne

Te funkcje mogą być używane w wyrażeniach warunkowych. Niektóre funkcje mogą nie obsługiwać wszystkich typów danych JSON.

## <a name="and"></a>oraz

Zwraca wartość `true` , jeśli wszystkie parametry mają być szacowane do `true` . Ta funkcja obsługuje co najmniej dwa parametry typu Boolean.

Poniższy przykład zwraca `true` :

```json
"[and(equals(0, 0), equals('web', 'web'), less(1, 2))]"
```

Poniższy przykład zwraca `false` :

```json
"[and(equals(0, 0), greater(1, 2))]"
```

## <a name="coalesce"></a>łączonych

Zwraca wartość pierwszego niezerowego parametru. Ta funkcja obsługuje wszystkie typy danych JSON.

Przyjmij `element1` i nie `element2` są zdefiniowane. Poniższy przykład zwraca `"Contoso"` :

```json
"[coalesce(steps('demoStep').element1, steps('demoStep').element2, 'Contoso')]"
```

Ta funkcja jest szczególnie przydatna w kontekście opcjonalnego wywołania, które następuje z powodu akcji użytkownika po załadowaniu strony. Przykładem jest to, że ograniczenia wprowadzone dla jednego pola w interfejsie użytkownika zależą od aktualnie wybranej wartości innego, **początkowo niewidocznego** pola. W takim przypadku `coalesce()` można użyć, aby zezwolić funkcji na składniowo, w czasie ładowania strony, podczas gdy użytkownik współdziała z polem.

Należy to rozważyć `DropDown` , co umożliwia użytkownikowi wybór spośród kilku różnych typów baz danych:

```
{
    "name": "databaseType",
    "type": "Microsoft.Common.DropDown",
    "label": "Choose database type",
    "toolTip": "Choose database type",
    "defaultValue": "Oracle Database",
    "visible": "[bool(steps('section_database').connectToDatabase)]"
    "constraints": {
        "allowedValues": [
            {
                "label": "Azure Database for PostgreSQL",
                "value": "postgresql"
            },
            {
                "label": "Oracle Database",
                "value": "oracle"
            },
            {
                "label": "Azure SQL",
                "value": "sqlserver"
            }
        ],
        "required": true
    },
```

Aby warunkować akcję innego pola dla aktualnie wybranej wartości tego pola, użyj `coalesce()` , jak pokazano poniżej:

```
"regex": "[concat('^jdbc:', coalesce(steps('section_database').databaseConnectionInfo.databaseType, ''), '.*$')]",
```

Jest to konieczne, ponieważ `databaseType` początkowo nie jest widoczny i w związku z tym nie ma wartości. Powoduje to, że całe wyrażenie nie zostanie prawidłowo obliczone.

## <a name="equals"></a>equals

Zwraca `true` czy oba parametry mają ten sam typ i wartość. Ta funkcja obsługuje wszystkie typy danych JSON.

Poniższy przykład zwraca `true` :

```json
"[equals(0, 0)]"
```

Poniższy przykład zwraca `true` :

```json
"[equals('web', 'web')]"
```

Poniższy przykład zwraca `false` :

```json
"[equals('abc', ['a', 'b', 'c'])]"
```

## <a name="greater"></a>greater

Zwraca wartość `true` , jeśli pierwszy parametr jest ściśle większy niż drugi parametr. Ta funkcja obsługuje parametry tylko typu Number i String.

Poniższy przykład zwraca `false` :

```json
"[greater(1, 2)]"
```

Poniższy przykład zwraca `true` :

```json
"[greater('9', '10')]"
```

## <a name="greaterorequals"></a>greaterOrEquals

Zwraca wartość `true` , jeśli pierwszy parametr jest większy lub równy drugiemu parametrowi. Ta funkcja obsługuje parametry tylko typu Number i String.

Poniższy przykład zwraca `true` :

```json
"[greaterOrEquals(2, 2)]"
```

## <a name="if"></a>if

Zwraca wartość na podstawie tego, czy warunek ma wartość true lub false. Pierwszy parametr jest warunkiem do przetestowania. Drugi parametr jest wartością, która ma zostać zwrócona, jeśli warunek ma wartość true. Trzeci parametr jest wartością, która ma zostać zwrócona, jeśli warunek ma wartość false.

Poniższy przykład zwraca wartość `yes` .

```json
"[if(equals(42, mul(6, 7)), 'yes', 'no')]"
```

## <a name="less"></a>less

Zwraca wartość `true` , jeśli pierwszy parametr jest ściśle mniejszy niż drugi parametr. Ta funkcja obsługuje parametry tylko typu Number i String.

Poniższy przykład zwraca `true` :

```json
"[less(1, 2)]"
```

Poniższy przykład zwraca `false` :

```json
"[less('9', '10')]"
```

## <a name="lessorequals"></a>lessOrEquals

Zwraca wartość `true` , jeśli pierwszy parametr jest mniejszy lub równy drugiemu parametrowi. Ta funkcja obsługuje parametry tylko typu Number i String.

Poniższy przykład zwraca `true` :

```json
"[lessOrEquals(2, 2)]"
```

## <a name="not"></a>not

Zwraca wartość `true` , jeśli parametr ma wartość `false` . Ta funkcja obsługuje tylko parametry typu Boolean.

Poniższy przykład zwraca `true` :

```json
"[not(false)]"
```

Poniższy przykład zwraca `false` :

```json
"[not(equals(0, 0))]"
```

## <a name="or"></a>lub

Zwraca wartość `true` , jeśli co najmniej jeden z parametrów ma wartość `true` . Ta funkcja obsługuje co najmniej dwa parametry typu Boolean.

Poniższy przykład zwraca `true` :

```json
"[or(equals(0, 0), equals('web', 'web'), less(1, 2))]"
```

Poniższy przykład zwraca `true` :

```json
"[or(equals(0, 0), greater(1, 2))]"
```

## <a name="next-steps"></a>Następne kroki

* Aby zapoznać się z wprowadzeniem do Azure Resource Manager, zobacz [Azure Resource Manager omówienie](../management/overview.md).
