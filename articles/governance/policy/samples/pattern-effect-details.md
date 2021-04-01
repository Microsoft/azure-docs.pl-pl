---
title: 'Wzorzec: efekty definicji zasad'
description: Ten Azure Policy wzorzec zawiera przykład zastosowania różnych efektów definicji zasad.
ms.date: 10/14/2020
ms.topic: sample
ms.openlocfilehash: f1da9bd153707db35c07ed3c176542797a694d7a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92073040"
---
# <a name="azure-policy-pattern-effects"></a>Wzorzec Azure Policy: efekty

Azure Policy ma wiele [efektów](../concepts/effects.md) , które określają sposób, w jaki usługa reaguje na niezgodne zasoby. Niektóre efekty są proste i nie wymagają żadnych dodatkowych właściwości w definicji zasad, a inne wymagają kilku właściwości.

## <a name="sample-1-simple-effect"></a>Przykład 1: prosty efekt

Ta definicja zasad sprawdza, czy tag zdefiniowany w parametrze **TagName** istnieje dla szacowanego zasobu. Jeśli tag jeszcze nie istnieje, efekt [modyfikacji](../concepts/effects.md#modify) jest wyzwalany w celu dodania znacznika z wartością w parametrze **tagValue**.

:::code language="json" source="~/policy-templates/patterns/pattern-effect-details-1.json":::

### <a name="sample-1-explanation"></a>Przykład 1: wyjaśnienie

:::code language="json" source="~/policy-templates/patterns/pattern-effect-details-1.json" range="40-50":::

Efekt **modyfikacji** wymaga bloku **Klasa policyrule. then. Details** , który definiuje **roleDefinitionIds** i **operacje**. Te parametry informują Azure Policy, jakie role są potrzebne do dodania znacznika i skorygowania zasobu oraz operacji **modyfikacji** do użycia. W tym przykładzie **operacja** _dodawania_ i parametry są używane do ustawiania znacznika i jego wartości.

## <a name="sample-2-complex-effect"></a>Przykład 2: efekt złożony

Ta definicja zasad przeprowadza inspekcję każdej maszyny wirtualnej w przypadku, gdy rozszerzenie zdefiniowane w parametrach **Publisher** i **Type** nie istnieje. Używa [auditIfNotExists](../concepts/effects.md#auditifnotexists) do sprawdzenia zasobu związanego z maszyną wirtualną, aby sprawdzić, czy istnieje wystąpienie zgodne ze zdefiniowanymi parametrami. Ten przykład sprawdza typ **rozszerzeń** .

:::code language="json" source="~/policy-templates/patterns/pattern-effect-details-2.json":::

### <a name="sample-2-explanation"></a>Przykład 2: wyjaśnienie

:::code language="json" source="~/policy-templates/patterns/pattern-effect-details-2.json" range="45-58":::

Efekt **auditIfNotExists** wymaga bloku **Klasa policyrule. then. Details** , aby zdefiniować zarówno **Typ** , jak i **existenceCondition** do wyszukania. **ExistenceCondition** używa elementów języka zasad, takich jak [Operatory logiczne](../concepts/definition-structure.md#logical-operators), w celu określenia, czy istnieje odpowiedni powiązany zasób. W tym przykładzie wartości sprawdzane względem każdego [aliasu](../concepts/definition-structure.md#aliases) są zdefiniowane w parametrach.

## <a name="next-steps"></a>Następne kroki

- Przejrzyj inne [wzorce i wbudowane definicje](./index.md).
- Przejrzyj temat [Struktura definicji zasad Azure Policy](../concepts/definition-structure.md).
- Przejrzyj [wyjaśnienie działania zasad](../concepts/effects.md).