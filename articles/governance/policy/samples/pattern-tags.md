---
title: 'Wzorzec: używanie tagów w definicji zasad'
description: Ten Azure Policy wzorzec zawiera przykłady dodawania znaczników sparametryzowane lub dziedziczenie tagów z grupy zasobów w definicji zasad.
ms.date: 03/31/2021
ms.topic: sample
ms.openlocfilehash: c748eb9b8ea795f9725082ec0aa0b8065ada8c65
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106093388"
---
# <a name="azure-policy-pattern-tags"></a>Wzorzec Azure Policy: Tagi

[Tagi](../../..//azure-resource-manager/management/tag-resources.md) to ważna część zarządzania i organizowania zasobów platformy Azure, a w tym zarządzanie tymi zasobami. Azure Policy umożliwia konfigurowanie tagów w nowych i istniejących zasobach na dużą skalę wraz z efektem [modyfikacji](../concepts/effects.md#modify) i [zadaniami korygowania](../how-to/remediate-resources.md).

## <a name="sample-1-parameterize-tags"></a>Przykład 1: Tagi Sparametryzuj

Ta definicja zasad używa dwóch parametrów, **TagName** i **tagValue** , aby określić, jakie zasady mają być wyszukiwane w grupach zasobów. Ten format umożliwia użycie definicji zasad dla dowolnej liczby kombinacji nazw tagów i wartości tagów, ale tylko do obsługi jednej definicji zasad.

> [!NOTE]
> Chociaż ten wzorzec definicji zasad jest podobny do wzorca w [wzorcu: Parameters #1](./pattern-parameters.md#sample-1-string-parameters), ten przykład używa **trybu** _wszystkie_ i obiekty docelowe grup zasobów.

:::code language="json" source="~/policy-templates/patterns/pattern-tags-1.json":::

### <a name="sample-1-explanation"></a>Przykład 1: wyjaśnienie

:::code language="json" source="~/policy-templates/patterns/pattern-tags-1.json" range="2-8" highlight="3":::

W tym przykładzie **tryb** jest ustawiony na _wszystko_ , ponieważ odwołuje się do grupy zasobów. W większości przypadków **tryb** powinien być ustawiony do _indeksowania_ podczas pracy ze znacznikami. Aby uzyskać więcej informacji, zobacz [tryby](../concepts/definition-structure.md#resource-manager-modes).

:::code language="json" source="~/policy-templates/patterns/pattern-tags-1.json" range="26-36" highlight="7-8":::

W tej części definicji zasad program `concat` łączy sparametryzowane parametry **TagName** i `tags['name']` format do informowania o tym  , aby ocenić ten tag dla parametru **tagValue**.
Jako **notEquals** jest używany, jeśli **Tagi \[ TagName \]** nie są równe **tagValue**, zostanie wyzwolony efekt **modyfikacji** .

:::code language="json" source="~/policy-templates/patterns/pattern-tags-1.json" range="43-47" highlight="3-4":::

W tym samym formacie, który służy do używania wartości tagów sparametryzowanej, jest używany przez operację **addOrReplace** w celu utworzenia lub zaktualizowania znacznika do żądanej wartości w ocenianej grupie zasobów.

## <a name="sample-2-inherit-tag-value-from-resource-group"></a>Przykład 2: dziedziczenie wartości tagu z grupy zasobów

Ta definicja zasad używa parametru **TagName** , aby określić, która wartość tagu dziedziczy z nadrzędnej grupy zasobów.

:::code language="json" source="~/policy-templates/patterns/pattern-tags-2.json":::

### <a name="sample-2-explanation"></a>Przykład 2: wyjaśnienie

:::code language="json" source="~/policy-templates/patterns/pattern-tags-2.json" range="2-8" highlight="3":::

W tym przykładzie **tryb** jest ustawiony na _indeksowanie_ , ponieważ nie jest przeznaczony dla grupy zasobów lub subskrypcji, mimo że pobiera wartość z grupy zasobów. Aby uzyskać więcej informacji, zobacz [tryby](../concepts/definition-structure.md#resource-manager-modes).

:::code language="json" source="~/policy-templates/patterns/pattern-tags-2.json" range="19-29" highlight="3-4,7-8":::

**Klasa policyrule. if** używa `concat` takich jak [przykładowe #1](#sample-1-parameterize-tags) do obliczenia wartości **TagName**, ale używa `resourceGroup()` funkcji, aby porównać ją z wartością tego samego tagu w nadrzędnej grupie zasobów. Druga klauzula w tym miejscu sprawdza, czy tag w grupie zasobów ma wartość i nie ma wartości null.

:::code language="json" source="~/policy-templates/patterns/pattern-tags-2.json" range="36-40" highlight="3-4":::

W tym miejscu wartość przypisana do tagu **TagName** w zasobie również używa funkcji, `resourceGroup()` Aby pobrać wartość z nadrzędnej grupy zasobów. W ten sposób można dziedziczyć Tagi z nadrzędnych grup zasobów. Jeśli zasób został już utworzony, ale nie dodano znacznika, ta sama definicja zasad i [zadanie korygowania](../how-to/remediate-resources.md) mogą aktualizować istniejące zasoby.

## <a name="next-steps"></a>Następne kroki

- Przejrzyj inne [wzorce i wbudowane definicje](./index.md).
- Przejrzyj temat [Struktura definicji zasad Azure Policy](../concepts/definition-structure.md).
- Przejrzyj [wyjaśnienie działania zasad](../concepts/effects.md).