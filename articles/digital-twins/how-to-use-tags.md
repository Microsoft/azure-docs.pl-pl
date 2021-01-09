---
title: Dodawanie tagów do cyfrowego bliźniaczych reprezentacji
titleSuffix: Azure Digital Twins
description: Zobacz, jak zaimplementować Tagi w Digital bliźniaczych reprezentacji
author: baanders
ms.author: baanders
ms.date: 7/22/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 9a1a55bdf21b74116450ca32f66d891f1aa206d3
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/08/2021
ms.locfileid: "98045414"
---
# <a name="add-tags-to-digital-twins"></a>Dodawanie tagów do cyfrowego bliźniaczych reprezentacji 

Można użyć koncepcji tagów do dalszej identyfikacji i kategoryzacji cyfrowego bliźniaczych reprezentacji. W szczególności użytkownicy mogą chcieć replikować Tagi z istniejących systemów, takich jak [Tagi Haystack](https://project-haystack.org/doc/TagModel), w ramach swoich wystąpień bliźniaczych reprezentacji cyfrowych platformy Azure. 

W tym dokumencie opisano wzorce, których można użyć w celu zaimplementowania tagów w Digital bliźniaczych reprezentacji.

Tagi są najpierw dodawane jako właściwości w [modelu](concepts-models.md) , który opisuje wieloosiową cyfrę. Ta właściwość jest następnie ustawiana na sznurze, gdy zostanie utworzony na podstawie modelu. Po tym znaczniki mogą być używane w [zapytaniach](concepts-query-language.md) w celu identyfikowania i filtrowania bliźniaczych reprezentacji.

## <a name="marker-tags"></a>Znaczniki znaczników 

Znacznik **znacznika** jest prostym ciągiem, który jest używany do oznaczania lub kategoryzowania dwucyfrowego sznurka, takiego jak "Blue" lub "Red". Ten ciąg jest nazwą tagu, a znaczniki znacznika nie mają znaczącej wartości — tag jest istotny tylko przez jego obecność (lub nieobecność). 

### <a name="add-marker-tags-to-model"></a>Dodaj znaczniki znacznika do modelu 

Znaczniki znacznika są modelowane jako mapa [DTDL](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) z elementu `string` do `boolean` . Wartość logiczna `mapValue` jest ignorowana, ponieważ obecność znacznika jest istotna. 

Poniżej znajduje się fragment modelu dwuosiowego implementujący tag znacznika jako właściwość:

:::code language="json" source="~/digital-twins-docs-samples/models/tags.json" range="2-16":::

### <a name="add-marker-tags-to-digital-twins"></a>Dodawanie tagów znacznika do cyfrowego bliźniaczych reprezentacji

Gdy `tags` Właściwość jest częścią modelu przędzy cyfrowej, można ustawić tag znacznika w dwucyfrowej formie, ustawiając wartość tej właściwości. 

Oto przykład, który wypełnia znacznik `tags` dla trzech bliźniaczych reprezentacji:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_other.cs" id="TagPropertiesMarker":::

### <a name="query-with-marker-tags"></a>Zapytanie ze znacznikami znaczników

Po dodaniu tagów do cyfrowego bliźniaczych reprezentacji Tagi mogą służyć do filtrowania bliźniaczych reprezentacji w zapytaniach. 

Oto zapytanie, aby pobrać wszystkie bliźniaczych reprezentacji oznaczone jako "Red": 

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryMarkerTags1":::

Możesz również łączyć Tagi, aby uzyskać bardziej skomplikowane zapytania. Oto zapytanie, aby pobrać wszystkie bliźniaczych reprezentacji, które są okrągłe, a nie czerwony: 

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryMarkerTags2":::

## <a name="value-tags"></a>Tagi wartości 

**Tag wartości** jest parą klucz-wartość, która jest używana do nadawania każdemu tagowi wartości, takiej jak `"color": "blue"` lub `"color": "red"` . Po utworzeniu znacznika wartości można go również użyć jako znacznika znacznika, ignorując wartość znacznika. 

### <a name="add-value-tags-to-model"></a>Dodawanie tagów wartości do modelu 

Tagi wartości są modelowane jako mapa [DTDL](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) z elementu `string` do `string` . Zarówno, `mapKey` jak i `mapValue` są istotne. 

Poniżej znajduje się fragment modelu dwuosiowego implementujący tag wartości jako właściwość:

:::code language="json" source="~/digital-twins-docs-samples/models/tags.json" range="17-31":::

### <a name="add-value-tags-to-digital-twins"></a>Dodawanie tagów wartości do Digital bliźniaczych reprezentacji

Podobnie jak w przypadku znaczników znacznika, można ustawić tag wartości w formie dwucyfrowej, ustawiając wartość tej `tags` właściwości z modelu. Aby użyć tagu wartości jako znacznika znacznika, można ustawić `tagValue` pole na wartość pustego ciągu ( `""` ). 

Oto przykład, który wypełnia wartość `tags` dla trzech bliźniaczych reprezentacji:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_other.cs" id="TagPropertiesValue":::

Należy zauważyć, że `red` i `purple` są używane jako znaczniki znaczników w tym przykładzie.

### <a name="query-with-value-tags"></a>Zapytanie z tagami wartości

Podobnie jak w przypadku tagów znacznika, można użyć tagów wartości do filtrowania bliźniaczych reprezentacji w zapytaniach. Możesz również używać tagów wartości i znaczników znaczników jednocześnie.

Z powyższego przykładu `red` jest używany jako tag znacznika. Należy pamiętać, że jest to zapytanie, aby pobrać wszystkie bliźniaczych reprezentacji, które zostały oznaczone jako "Red": 

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryMarkerTags1":::

Oto zapytanie umożliwiające pobieranie wszystkich jednostek, które są małe (tag wartości), a nie czerwonych: 

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryMarkerValueTags":::

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat projektowania modeli cyfrowych bliźniaczych i zarządzania nimi:
* [*Instrukcje: Zarządzanie modelami niestandardowymi*](how-to-manage-model.md)

Przeczytaj więcej na temat wykonywania zapytań na grafie bliźniaczym:
* [*Instrukcje: zapytanie o wykres bliźniaczy*](how-to-query-graph.md)