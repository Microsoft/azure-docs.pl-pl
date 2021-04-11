---
title: 'Wzorzec: parametry w definicji zasad'
description: Ten Azure Policy wzorzec zawiera przykład użycia parametrów ciągów i tablic w definicji zasad oraz sposób Sparametryzuj efektu.
ms.date: 03/31/2021
ms.topic: sample
ms.openlocfilehash: b742aaaf950e2b5670edbaa1f0134da144e675b6
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106092776"
---
# <a name="azure-policy-pattern-parameters"></a>Wzorzec Azure Policy: parametry

Definicję zasad można uczynić dynamicznym, aby zmniejszyć liczbę definicji zasad, które są konieczne przy użyciu [parametrów](../concepts/definition-structure.md#parameters). Parametr jest definiowany podczas przypisywania zasad. Parametry mają zestaw wstępnie zdefiniowanych właściwości opisujących parametr i sposób jego użycia.

## <a name="sample-1-string-parameters"></a>Przykład 1: parametry ciągu

Ta definicja zasad używa dwóch parametrów, **TagName** i **tagValue**, aby określić, co ma być wyszukiwane przez zasady dla zasobów. Ten format umożliwia użycie definicji zasad dla dowolnej liczby kombinacji nazw tagów i wartości tagów, ale tylko do obsługi jednej definicji zasad.

> [!NOTE]
> Aby uzyskać przykładowy tag, który używa **trybu** _All_ i współpracuje z grupą zasobów, zobacz [wzorzec: Tags — przykład #1](./pattern-tags.md#sample-1-parameterize-tags).

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-1.json":::

### <a name="sample-1-explanation"></a>Przykład 1: wyjaśnienie

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-1.json" range="8-13":::

W tej części definicji zasad parametr **TagName** jest zdefiniowany jako _ciąg_ i opis jest przeznaczony do użycia.

Parametr jest następnie używany w bloku **Klasa policyrule. if** , aby zastosować zasady dynamiczne. W tym miejscu służy do definiowania ocenianego pola, które jest tagiem z wartością **TagName**.

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-1.json" range="22-27" highlight="3":::

## <a name="sample-2-array-parameters"></a>Przykład 2: parametry tablicy

Ta definicja zasad używa jednego parametru, **listOfBandwidthinMbps**, aby sprawdzić, czy zasób obwodu trasy Express skonfigurował ustawienie przepustowości do jednej z zatwierdzonych wartości. Jeśli nie jest on zgodny [, nie można](../concepts/effects.md#deny)utworzyć lub zaktualizować zasobu.

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-2.json":::

### <a name="sample-2-explanation"></a>Przykład 2: wyjaśnienie

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-2.json" range="6-12":::

W tej części definicji zasad parametr **listOfBandwidthinMbps** jest zdefiniowany jako _Tablica_ i opis jest przeznaczony do użycia. Jako _Tablica_ ma wiele wartości do dopasowania.

Ten parametr jest następnie używany w bloku **Klasa policyrule. if** . Jako parametr _tablicy_ ,  
 należy użyć lub **notIn** [warunku](../concepts/definition-structure.md#conditions)tablicy. 
W tym miejscu jest używany w odniesieniu do aliasu **. bandwidthInMbps** jako jednej ze zdefiniowanych wartości.

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-2.json" range="21-24" highlight="3":::

## <a name="sample-3-parameterized-effect"></a>Przykład 3: efekt sparametryzowany

Typowym sposobem, aby definicje zasad były wielokrotnego użytku, jest Sparametryzuj samego efektu. W tym przykładzie zastosowano pojedynczy parametr, **efekt**. Parametryzacja efekt umożliwia przypisanie tej samej definicji do różnych zakresów z różnymi efektami.

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-3.json":::

### <a name="sample-3-explanation"></a>Przykład 3: wyjaśnienie

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-3.json" range="11-25":::

W tej części definicji zasad parametr **Effect** jest definiowany jako _ciąg_. Definicja zasad ustawia wartość domyślną dla przypisania do _inspekcji_ i ogranicza inne opcje do _wyłączony_ i _Odmów_.

Parametr jest następnie używany w bloku **Klasa policyrule. then** dla _efektu_.

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-3.json" range="38-40" highlight="2":::

## <a name="next-steps"></a>Następne kroki

- Przejrzyj inne [wzorce i wbudowane definicje](./index.md).
- Przejrzyj temat [Struktura definicji zasad Azure Policy](../concepts/definition-structure.md).
- Przejrzyj [wyjaśnienie działania zasad](../concepts/effects.md).