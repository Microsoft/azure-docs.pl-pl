---
title: 'Wzorzec: właściwości pola w definicji zasad'
description: Ten Azure Policy wzorzec zawiera przykład użycia właściwości pola w definicji zasad.
ms.date: 06/29/2020
ms.topic: sample
ms.openlocfilehash: 87c550e5fb3ef0efbf219efc738a69071d2e89ba
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85565723"
---
# <a name="azure-policy-pattern-field-properties"></a>Wzorzec Azure Policy: właściwości pola

Operator [Field](../concepts/definition-structure.md#fields) oblicza określoną właściwość lub [alias](../concepts/definition-structure.md#aliases) dla podanej wartości dla danego [warunku](../concepts/definition-structure.md#conditions).

## <a name="sample-policy-definition"></a>Przykładowa definicja zasad

Ta definicja zasad umożliwia zdefiniowanie dozwolonych regionów spełniających wymagania geograficznej lokalizacji w organizacji. Dozwolone zasoby są zdefiniowane w parametrze **listOfAllowedLocations** (_Array_). Zasoby, które pasują do definicji, są [odrzucane](../concepts/effects.md#deny).

:::code language="json" source="~/policy-templates/patterns/pattern-fields.json":::

### <a name="explanation"></a>Objaśnienie

:::code language="json" source="~/policy-templates/patterns/pattern-fields.json" range="18-36" highlight="3,7,11":::

Operator **pola** jest używany trzy razy w obrębie [operatora logicznego](../concepts/definition-structure.md#logical-operators) **allOf**.

- Pierwsze użycie oblicza `location` Właściwość z warunkiem **notIn** do parametru **listOfAllowedLocations** . **notIn** działa, ponieważ oczekuje _tablicy_ , a parametr jest _tablicą_. Jeśli `location` utworzony lub zaktualizowany zasób nie znajduje się na liście zatwierdzonych, ten element ma wartość true.
- Drugie użycie również oblicza `location` Właściwość, ale używa warunku **notEquals** , aby sprawdzić, czy zasób jest _globalny_. Jeśli `location` utworzony lub zaktualizowany zasób nie jest _globalny_, ten element ma wartość true.
- Ostatnie użycie szacuje `type` Właściwość i używa warunku **notEquals** do sprawdzenia, czy typ zasobu nie jest _Microsoft. usługi azureactivedirectory/b2cDirectories_. Jeśli nie, ten element ma wartość true.

Jeśli wszystkie trzy instrukcje warunku w operatorze logicznym **allOf** oceńą wartość true, tworzenie lub aktualizowanie zasobów jest blokowane przez Azure Policy.

## <a name="next-steps"></a>Następne kroki

- Przejrzyj inne [wzorce i wbudowane definicje](./index.md).
- Przejrzyj temat [Struktura definicji zasad Azure Policy](../concepts/definition-structure.md).
- Przejrzyj [wyjaśnienie działania zasad](../concepts/effects.md).