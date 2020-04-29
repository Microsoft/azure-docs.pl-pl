---
title: 'Wzorzec: parametry w definicji zasad'
description: Ten Azure Policy wzorzec zawiera przykład sposobu używania parametrów w definicji zasad.
ms.date: 01/31/2020
ms.topic: sample
ms.openlocfilehash: 4921bb216ef67b561bc8986cf48239e6448beafc
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "77172807"
---
# <a name="azure-policy-pattern-parameters"></a>Wzorzec Azure Policy: parametry

Definicję zasad można uczynić dynamicznym, aby zmniejszyć liczbę definicji zasad, które są konieczne przy użyciu [parametrów](../concepts/definition-structure.md#parameters). Parametr jest definiowany podczas przypisywania zasad. Parametry mają zestaw wstępnie zdefiniowanych właściwości opisujących parametr i sposób jego użycia.

## <a name="sample-1-string-parameters"></a>Przykład 1: parametry ciągu

Ta definicja zasad używa dwóch parametrów, **TagName** i **tagValue** , aby określić, co ma być wyszukiwane przez zasady dla zasobów. Ten format umożliwia używanie zasad dla dowolnej liczby kombinacji nazwy tagu i wartości tagów, ale tylko do obsługi jednej definicji zasad.

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

W tej części definicji zasad parametr **listOfBandwidthinMbps** jest zdefiniowany jako _Tablica_ i opis jest przeznaczony do użycia. Jako _Tablica_ma wiele wartości do dopasowania.

Ten parametr jest następnie używany w bloku **Klasa policyrule. if** . Jako parametr _tablicy_ , należy **użyć lub** **notIn** [warunku](../concepts/definition-structure.md#conditions) _tablicy_
.
W tym miejscu jest używany w odniesieniu do aliasu **. bandwidthInMbps** jako jednej ze zdefiniowanych wartości.

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-2.json" range="21-24" highlight="3":::

## <a name="next-steps"></a>Następne kroki

- Przejrzyj inne [wzorce i wbudowane definicje](./index.md).
- Przejrzyj temat [Struktura definicji zasad Azure Policy](../concepts/definition-structure.md).
- Przejrzyj [wyjaśnienie działania zasad](../concepts/effects.md).