---
title: 'Wzorzec: operatory logiczne w definicji zasad'
description: Ten Azure Policy wzorzec zawiera przykłady użycia operatorów logicznych w definicji zasad.
ms.date: 08/17/2020
ms.topic: sample
ms.openlocfilehash: 3f644cdbfc45b06d1ad5db8e7727c0fa69742f00
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88545594"
---
# <a name="azure-policy-pattern-logical-operators"></a>Wzorzec Azure Policy: operatory logiczne

Definicja zasad może zawierać kilka instrukcji warunkowych. Może być konieczne, aby każda instrukcja była prawdziwa lub tylko niektóre z nich były prawdziwe. Aby zapewnić obsługę tych wymagań, język ma [Operatory logiczne](../concepts/definition-structure.md#logical-operators) dla **not**, **allOf**i **anyOf**. Są one opcjonalne i mogą być zagnieżdżane w celu tworzenia złożonych scenariuszy.

## <a name="sample-1-one-logical-operator"></a>Przykład 1: jeden operator logiczny

Ta definicja zasad szacuje konta CosmosDB, aby sprawdzić, czy skonfigurowano automatyczne przełączanie do trybu failover i wiele lokalizacji zapisu. Gdy nie są one, [wyzwalają](../concepts/effects.md#audit) wyzwalacze i tworzą wpis dziennika w przypadku utworzenia lub zaktualizowania niezgodnego zasobu.

:::code language="json" source="~/policy-templates/patterns/pattern-logical-operators-1.json":::

### <a name="sample-1-explanation"></a>Przykład 1: wyjaśnienie

:::code language="json" source="~/policy-templates/patterns/pattern-logical-operators-1.json" range="6-22" highlight="3":::

**Klasa policyrule. if** blokuje użycie jednego **allOf** , aby upewnić się, że wszystkie trzy warunki są spełnione.
Tylko wtedy, gdy wszystkie te warunki mają wartość true, jest wyzwalany przez wyzwalacz efektu **inspekcji** .

## <a name="sample-2-multiple-logical-operators"></a>Przykład 2: wiele operatorów logicznych

Ta definicja zasad służy do obliczania zasobów dla wzorca nazewnictwa. Jeśli zasób nie jest zgodny, zostanie on [odrzucony](../concepts/effects.md#deny).

:::code language="json" source="~/policy-templates/patterns/pattern-logical-operators-2.json":::

### <a name="sample-2-explanation"></a>Przykład 2: wyjaśnienie

:::code language="json" source="~/policy-templates/patterns/pattern-logical-operators-2.json" range="7-21" highlight="2,3,9":::

Ten **Klasa policyrule. Jeśli** blok zawiera również pojedynczy **allOf**, ale każdy warunek jest opakowany operatorem **not** Logical. Warunek wewnątrz operatora **not** logicznego jest obliczany jako pierwszy, a następnie szacuje **, czy** cała klauzula ma wartość true lub false. Jeśli operator logiczny **nie** zwróci wartości true, wyzwalacze efektu zasad.

## <a name="sample-3-combining-logical-operators"></a>Przykład 3: łączenie operatorów logicznych

Ta definicja zasad służy do szacowania kont ze sprężyną języka Java, aby sprawdzić, czy śledzenie nie jest włączone lub czy śledzenie nie jest w stanie pomyślnym.

:::code language="json" source="~/policy-templates/patterns/pattern-logical-operators-3.json":::

### <a name="sample-3-explanation"></a>Przykład 3: wyjaśnienie

:::code language="json" source="~/policy-templates/patterns/pattern-logical-operators-3.json" range="6-28" highlight="3,8":::

Ten **Klasa policyrule. if** Block zawiera operatory logiczne **allOf** i **anyOf** . Operator logiczny **anyOf** oblicza wartość true, tak długo, jak jeden warunek uwzględniony ma wartość true. Ponieważ _Typ_ jest na poziomie rdzenia **allOf**, musi zawsze oszacować wartość true. Jeśli _Typ_ i jeden z warunków w **anyOf** ma wartość true, wyzwalacze efektu zasad.

## <a name="next-steps"></a>Następne kroki

- Przejrzyj inne [wzorce i wbudowane definicje](./index.md).
- Przejrzyj temat [Struktura definicji zasad Azure Policy](../concepts/definition-structure.md).
- Przejrzyj [wyjaśnienie działania zasad](../concepts/effects.md).