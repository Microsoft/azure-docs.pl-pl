---
title: Nadchodzące zmiany w regułach pozyskiwania i spłaszczania w Azure Time Series Insights Gen2 | Microsoft Docs
description: Zmiany reguły pozyskiwania
ms.service: time-series-insights
services: time-series-insights
author: lyrana
ms.author: lyhughes
manager: dpalled
ms.workload: big-data
ms.topic: conceptual
ms.date: 10/02/2020
ms.custom: lyhughes
ms.openlocfilehash: 56a1d5aab2f665f9c5bd8f6fa322f35e55483c7b
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/21/2020
ms.locfileid: "95016721"
---
# <a name="upcoming-changes-to-json-flattening-and-escaping-rules-for-new-environments"></a>Nadchodzące zmiany w regułach spłaszczania i ucieczki JSON dla nowych środowisk

> [!IMPORTANT]
> Te zmiany zostaną zastosowane do *nowo utworzonych* Microsoft Azure Time Series Insights środowiska Gen2. Zmiany nie dotyczą środowisk Gen1.

Środowisko Azure Time Series Insights Gen2 tworzy dynamicznie kolumny magazynu zgodnie z określonym zestawem konwencji nazewnictwa. Po pozyskaniu zdarzenia Time Series Insights stosuje zestaw reguł do ładunku JSON i nazw właściwości. Zmiany sposobu spłaszczania i przechowywania danych JSON w celu uwzględnienia nowych środowisk Azure Time Series Insights Gen2 w lipcu 2020. Ta zmiana ma wpływ na następujące sytuacje:

* Ładunek JSON zawiera obiekty zagnieżdżone.
* Ładunek JSON zawiera tablice.
* W nazwie właściwości JSON należy użyć jednego z następujących czterech znaków specjalnych: `[` `\` `.``'`
* Co najmniej jedna z właściwości identyfikatora szeregów czasowych (TS) znajduje się w obiekcie zagnieżdżonym.

Jeśli tworzysz nowe środowisko, a co najmniej jeden z tych przypadków dotyczy ładunku zdarzenia, Twoje dane będą spłaszczone i przechowywane inaczej. Poniższa tabela zawiera podsumowanie zmian:

| Bieżąca reguła | Nowa reguła | Przykładowy kod JSON | Nazwa poprzedniej kolumny | Nazwa nowej kolumny
|---|---| ---| ---|  ---|
| Zagnieżdżony kod JSON jest spłaszczony przy użyciu znaku podkreślenia jako delineator. |Zagnieżdżony kod JSON jest spłaszczony przy użyciu okresu jako delineator.  | ``{"series" : { "value" : 19.338 }}`` | `series_value_double` |`series.value_double` |
| Znaki specjalne nie są wyprowadzane. | Nazwy właściwości JSON, które zawierają znaki specjalne `.` `[`   `\` i `'` są wyprowadzane przy użyciu `['` i `']` . W ramach `['` i `']` , istnieją dodatkowe ucieczki pojedynczych cudzysłowów i ukośników odwrotnych. Pojedynczy cytat zostanie zapisany jako `\'` i ukośnik odwrotny `\\` .  | ```"Foo's Law Value": "17.139999389648"``` | `Foo's Law Value_double` | `['Foo\'s Law Value']_double` |
| Tablice elementów podstawowych są przechowywane jako ciąg. | Tablice typów pierwotnych są przechowywane jako typ dynamiczny.  | `"values": [154, 149, 147]` | `values_string`  | `values_dynamic` |
Tablice obiektów są zawsze spłaszczone i wytwarzają wiele zdarzeń. | Jeśli obiekty w tablicy nie mają właściwości identyfikatora TS lub timestamp, tablica obiektów jest przechowywana całościowo jako typ dynamiczny. | `"values": [{"foo" : 140}, {"bar" : 149}]` | `values_foo_long | values_bar_long` | `values_dynamic` |

## <a name="recommended-changes-for-new-environments"></a>Zalecane zmiany dla nowych środowisk

### <a name="if-your-ts-id-andor-timestamp-property-is-nested-within-an-object"></a>Jeśli identyfikator TS i/lub właściwość sygnatury czasowej są zagnieżdżone w obiekcie

Wszystkie nowe wdrożenia muszą być zgodne z nowymi regułami pozyskiwania. Na przykład, jeśli identyfikator TS to `telemetry_tagId` , należy zaktualizować wszystkie szablony Azure Resource Manager lub zautomatyzowanego wdrażania skryptów, aby skonfigurować `telemetry.tagId` je jako środowisko TS ID. Konieczna jest również ta zmiana dla sygnatur czasowych źródła zdarzeń w zagnieżdżonym formacie JSON.

### <a name="if-your-payload-contains-nested-json-or-special-characters-and-you-automate-authoring-time-series-model-variable-expressions"></a>Jeśli ładunek zawiera zagnieżdżony kod JSON lub znaki specjalne i automatyzuje tworzenie wyrażeń zmiennych [modelu szeregów czasowych](./concepts-model-overview.md)

Zaktualizuj kod klienta, który wykonuje [TypesBatchPut](/rest/api/time-series-insights/dataaccessgen2/timeseriestypes/executebatch#typesbatchput) , aby pasował do nowych reguł pozyskiwania. Na przykład należy zaktualizować poprzednie [wyrażenie szeregów czasowych](/rest/api/time-series-insights/reference-time-series-expression-syntax) `"value": {"tsx": "$event.series_value.Double"}` do jednej z następujących opcji:

* `"value": {"tsx": "$event.series.value.Double"}`
* `"value": {"tsx": "$event['series']['value'].Double"}`

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej na temat [Azure Time Series Insights Gen2 Storage i](./concepts-ingestion-overview.md)transferów przychodzących.

* Dowiedz się, jak wykonywać zapytania dotyczące danych, korzystając z [interfejsów API zapytań szeregów czasowych](./concepts-query-overview.md).

* Przeczytaj więcej na temat [nowej składni wyrażeń szeregów czasowych](/rest/api/time-series-insights/reference-time-series-expression-syntax).