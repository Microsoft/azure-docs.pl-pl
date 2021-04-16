---
title: Dodawanie tagów do usługi Digital Twins
titleSuffix: Azure Digital Twins
description: Zobacz, jak zaimplementować tagi w u usługi Digital Twins
author: baanders
ms.author: baanders
ms.date: 7/22/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 0d45ef8c32e61b5567798b7c42af28badb222601
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107376719"
---
# <a name="add-tags-to-digital-twins"></a>Dodawanie tagów do usługi Digital Twins 

Możesz użyć koncepcji tagów do dalszej identyfikacji i kategoryzowania swoich cyfrowych bliźniaczych reprezentacji. W szczególności użytkownicy mogą chcieć replikować tagi z istniejących systemów, takich jak [Tagi Systemu Windows,](https://project-haystack.org/doc/TagModel)w swoich Azure Digital Twins wystąpień. 

W tym dokumencie opisano wzorce, których można używać do implementowania tagów w bliźniaczych reprezentacji cyfrowych.

Tagi są najpierw dodawane jako właściwości w [modelu, który](concepts-models.md) opisuje cyfrową bliźniacze reprezentacji. Ta właściwość jest następnie ustawiana dla bliźniaczej reprezentacji, gdy jest tworzona na podstawie modelu. Następnie tagi mogą być używane [](concepts-query-language.md) w zapytaniach do identyfikowania i filtrowania bliźniaczych reprezentacji.

## <a name="marker-tags"></a>Tagi znaczników 

Tag **znacznika to** prosty ciąg, który służy do oznaczania lub kategoryzowania bliźniaczej reprezentacji cyfrowej, takiej jak "niebieski" lub "czerwony". Ten ciąg jest nazwą tagu, a tagi znacznika nie mają znaczącej wartości — tag jest znaczący tylko ze względu na jego obecność (lub brak). 

### <a name="add-marker-tags-to-model"></a>Dodawanie tagów znaczników do modelu 

Tagi znaczników są modelowane jako mapa [DTDL](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) z `string` do `boolean` . Wartość logiczna jest ignorowana, ponieważ obecność tagu jest `mapValue` ważna. 

Oto fragment z modelu bliźniaczego implementowania tagu znacznika jako właściwości:

:::code language="json" source="~/digital-twins-docs-samples/models/tags.json" range="2-16":::

### <a name="add-marker-tags-to-digital-twins"></a>Dodawanie tagów znaczników do usługi Digital Twins

Gdy właściwość jest częścią modelu bliźniaczej reprezentacji cyfrowej, możesz ustawić tag znacznika w bliźniaczej reprezentacji cyfrowej, ustawiając `tags` wartość tej właściwości. 

Oto przykład, który wypełnia znacznik `tags` dla trzech bliźniaczych reprezentacji:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_other.cs" id="TagPropertiesMarker":::

Oto przykład kodu na temat sposobu ustawienia znacznika dla bliźniaczej `tags` reprezentacji przy użyciu [zestawu SDK platformy .NET:](/dotnet/api/overview/azure/digitaltwins/client)

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_other.cs" id="TagPropertiesCsharp":::

### <a name="query-with-marker-tags"></a>Wykonywanie zapytań przy użyciu tagów znaczników

Po dodaniu tagów do bliźniaczych reprezentacji cyfrowych można ich użyć do filtrowania bliźniaczych reprezentacji w zapytaniach. 

Oto zapytanie w celu uzyskania wszystkich bliźniaczych reprezentacji oznaczonych jako "red": 

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryMarkerTags1":::

Tagi można również łączyć w przypadku bardziej złożonych zapytań. Oto zapytanie w celu uzyskania wszystkich bliźniaczych reprezentacji, które są okrągłe, a nie czerwone: 

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryMarkerTags2":::

## <a name="value-tags"></a>Tagi wartości 

Tag **wartości to** para klucz-wartość, która służy do nadawać każdemu tagowi wartość, taką jak lub `"color": "blue"` `"color": "red"` . Po utworzeniu tagu wartości można go również użyć jako tagu znacznika, ignorując jego wartość. 

### <a name="add-value-tags-to-model"></a>Dodawanie tagów wartości do modelu 

Tagi wartości są modelowane jako mapa [DTDL](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) z `string` do `string` . Zarówno i `mapKey` `mapValue` są znaczące. 

Oto fragment z modelu bliźniaczego implementowania tagu wartości jako właściwości:

:::code language="json" source="~/digital-twins-docs-samples/models/tags.json" range="17-31":::

### <a name="add-value-tags-to-digital-twins"></a>Dodawanie tagów wartości do usługi Digital Twins

Podobnie jak w przypadku tagów znaczników, możesz ustawić tag wartości w bliźniaczej reprezentacji cyfrowej, ustawiając wartość tej właściwości `tags` z modelu. Aby użyć tagu wartości jako tagu znacznika, możesz ustawić pole na `tagValue` pustą wartość ciągu ( `""` ). 

Oto przykład, który wypełnia wartość dla `tags` trzech bliźniaczych reprezentacji:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_other.cs" id="TagPropertiesValue":::

Zwróć `red` uwagę, że w tym `purple` przykładzie jako tagi znaczników używane są tagi i .

### <a name="query-with-value-tags"></a>Wykonywanie zapytań przy użyciu tagów wartości

Podobnie jak w przypadku tagów znaczników, możesz użyć tagów wartości do filtrowania bliźniaczych reprezentacji w zapytaniach. Można również używać tagów wartości i tagów znaczników jednocześnie.

W powyższym przykładzie `red` jest on używany jako tag znacznika. Należy pamiętać, że jest to zapytanie w celu uzyskania wszystkich bliźniaczych reprezentacji oznaczonych jako "red": 

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryMarkerTags1":::

Oto zapytanie, które ma pobrać wszystkie jednostki, które są małe (tag wartości), a nie czerwone: 

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryMarkerValueTags":::

## <a name="next-steps"></a>Następne kroki

Przeczytaj więcej na temat projektowania modeli bliźniaczych reprezentacji cyfrowych i zarządzania nimi:
* [*Instrukcja: zarządzanie modelami DTDL*](how-to-manage-model.md)

Przeczytaj więcej na temat wykonywania zapytań dotyczących grafu bliźniaczej reprezentacji:
* [*How-to: Query the twin graph (Graf bliźniaczej reprezentacji zapytania)*](how-to-query-graph.md)
