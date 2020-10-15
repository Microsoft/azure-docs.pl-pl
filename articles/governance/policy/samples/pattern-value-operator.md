---
title: 'Wzorzec: operator wartości w definicji zasad'
description: Ten Azure Policy wzorzec zawiera przykład użycia operatora Value w definicji zasad.
ms.date: 10/14/2020
ms.topic: sample
ms.openlocfilehash: 8392c69ff3d63ff4ecad2a26d5d914b4766147b8
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/15/2020
ms.locfileid: "92072887"
---
# <a name="azure-policy-pattern-the-value-operator"></a>Wzorzec Azure Policy: wartość operator wartości

Operator [Value](../concepts/definition-structure.md#value) oblicza [Parametry](../concepts/definition-structure.md#parameters), [obsługiwane funkcje szablonu](../concepts/definition-structure.md#policy-functions)lub literały do podanej wartości dla danego [warunku](../concepts/definition-structure.md#conditions).

> [!WARNING]
> Jeśli wynik _funkcji szablonu_ jest błąd, Ocena zasad kończy się niepowodzeniem. Niepowodzenie oceny to niejawne **odmowa**. Aby uzyskać więcej informacji, zobacz [unikanie niepowodzeń związanych z szablonami](../concepts/definition-structure.md#avoiding-template-failures).

## <a name="sample-policy-definition"></a>Przykładowa definicja zasad

Ta definicja zasad dodaje lub zastępuje tag określony w parametrze **TagName** (_String_) w obszarze zasoby i dziedziczy wartość **TagName** z grupy zasobów, w której znajduje się zasób. Ta ocena występuje, gdy zasób zostanie utworzony lub zaktualizowany. W efekcie [zmiany](../concepts/effects.md#modify) mogą zostać uruchomione w istniejących zasobach za pośrednictwem [zadania korygowania](../how-to/remediate-resources.md).

:::code language="json" source="~/policy-templates/patterns/pattern-value-operator.json":::

### <a name="explanation"></a>Objaśnienie

:::code language="json" source="~/policy-templates/patterns/pattern-value-operator.json" range="20-30" highlight="7,8":::

Operator **wartości** jest używany w obrębie **Klasa policyrule. if** bloku **Właściwości**. W tym przykładzie [operator logiczny](../concepts/definition-structure.md#logical-operators) **allOf** jest używany w celu stwierdzenia, że oba instrukcje warunkowe muszą mieć wartość true dla efektu, należy **zmodyfikować**, aby wykonać.

**wartość** zwraca wynik funkcji szablonu [resourceing ()](../../../azure-resource-manager/templates/template-functions-resource.md#resourcegroup) do **notEquals** warunku wartości pustej. Jeśli istnieje nazwa tagu podana w nazwie **TagName** w nadrzędnej grupie zasobów, warunek warunkowy ma wartość true.

## <a name="next-steps"></a>Następne kroki

- Przejrzyj inne [wzorce i wbudowane definicje](./index.md).
- Przejrzyj temat [Struktura definicji zasad Azure Policy](../concepts/definition-structure.md).
- Przejrzyj [wyjaśnienie działania zasad](../concepts/effects.md).