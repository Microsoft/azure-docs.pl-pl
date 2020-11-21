---
title: Obsługa długich typów danych w Azure Time Series Insights Gen2 | Microsoft Docs
description: Obsługa długich typów danych w Azure Time Series Insights Gen2.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.topic: conceptual
ms.date: 10/01/2020
ms.custom: dpalled
ms.openlocfilehash: 3460cd8a88733ede041f6c0635ba40797675ed03
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/21/2020
ms.locfileid: "95025331"
---
# <a name="adding-support-for-long-data-type-in-azure-time-series-insights-gen2"></a>Dodawanie obsługi długich typów danych w Azure Time Series Insights Gen2

Dodanie obsługi dla długich typów danych ma wpływ na sposób przechowywania i indeksowania danych liczbowych w Azure Time Series Insights tylko środowiskach Gen2. Jeśli masz środowisko Gen1, możesz zignorować te zmiany.

Od 29 czerwca lub 30 czerwca 2020, w zależności od regionu, Twoje dane będą indeksowane jako **Long** i **Double**.  Jeśli masz jakieś pytania lub wątpliwości dotyczące tej zmiany, Prześlij bilet pomocy technicznej przez Azure Portal i podaj tę komunikację.

Jeśli masz wpływ na następujące przypadki, wprowadź zalecane zmiany:

- **Przypadek 1**: obecnie są używane zmienne modelu szeregów czasowych i wysyłane są tylko integralne typy danych w danych telemetrycznych.
- **Przypadek 2**: obecnie korzystasz ze zmiennych modelu szeregów czasowych i wysyłaj zarówno całkowite, jak i niecałkowite typy danych w danych telemetrycznych.
- **Przypadek 3**: użycie zmiennych kategorii do mapowania wartości całkowitych na kategorie.
- **Przypadek 4**: używasz zestawu SDK języka JavaScript, aby utworzyć niestandardową aplikację frontonu.
- **Przypadek 5**: zbliża się limit nazw właściwości 1 000 w sklepie ciepłym i wysyła zarówno dane całkowite, jak i niecałkowite. Liczba właściwości może być wyświetlana jako Metryka w [Azure Portal](https://portal.azure.com/).

Jeśli którykolwiek z tych przypadków dotyczy Ciebie, wprowadź zmiany w modelu. Zaktualizuj wyrażenie szeregów czasowych (TSX) w definicji zmiennej przy użyciu zalecanych zmian. Aktualizuj oba:

- Eksplorator Azure Time Series Insights
- Dowolny klient niestandardowy korzystający z naszych interfejsów API

W zależności od rozwiązania IoT i ograniczeń można nie mieć wglądu w dane wysyłane do środowiska Azure Time Series Insights Gen2. Jeśli nie masz pewności, czy dane są tylko integralne, czy zarówno integralna, jak i niecałkowita, masz kilka opcji:

- Możesz poczekać, aż funkcja zostanie wydana. Następnie Zbadaj zdarzenia pierwotne w interfejsie użytkownika Eksploratora, aby zrozumieć, które właściwości są zapisywane w dwóch oddzielnych kolumnach.
- Można zapobiegawczo wprowadzić zalecane zmiany dla wszystkich tagów numerycznych.
- Możesz tymczasowo kierować podzestaw zdarzeń do magazynu, aby lepiej zrozumieć i eksplorować schemat.

Aby przechowywać zdarzenia, Włącz funkcję [przechwytywania zdarzeń](../event-hubs/event-hubs-capture-overview.md) dla platformy Azure Event Hubs lub [roześlij](../iot-hub/iot-hub-devguide-messages-d2c.md#azure-storage-as-a-routing-endpoint) ją z IoT Hub do usługi Azure Blob Storage.

Dane można także zaobserwować za pośrednictwem [Eksploratora centrum zdarzeń](https://marketplace.visualstudio.com/items?itemName=Summer.azure-event-hub-explorer)lub za pomocą [hosta procesora zdarzeń](../event-hubs/event-hubs-dotnet-standard-getstarted-send.md#receive-events).

Jeśli używasz IoT Hub, przejdź do pozycji [odczytywanie komunikatów z urządzenia do chmury z wbudowanego punktu końcowego,](../iot-hub/iot-hub-devguide-messages-read-builtin.md) Aby uzyskać dostęp do wbudowanego punktu końcowego.

> [!NOTE]
> Jeśli nie wprowadzisz zalecanych zmian, może wystąpić zakłócenie. Na przykład zmienne Time Series Insights, do których uzyskuje się dostęp za pośrednictwem interfejsów API zapytań lub Eksplorator Time Series Insights zwróci **wartość null** (oznacza to, że w Eksploratorze nie są wyświetlane żadne dane).

## <a name="recommended-changes"></a>Zalecane zmiany

### <a name="case-1-using-time-series-model-variables-and-sending-only-integral-data-types-in-telemetry-data"></a>Przypadek 1: Używanie zmiennych modelu szeregów czasowych i wysyłanie tylko całkowitych typów danych w danych telemetrycznych

Zalecane zmiany w przypadku 1 są takie same, jak w przypadku 2. Postępuj zgodnie z instrukcjami w sekcji w przypadku 2.

### <a name="case-2-using-time-series-model-variables-and-sending-both-integral-and-nonintegral-types-in-telemetry-data"></a>Przypadek 2: Używanie zmiennych modelu szeregów czasowych i wysyłanie zarówno całkowitych, jak i niecałkowitych typów danych telemetrycznych

Jeśli aktualnie wysyłasz dane telemetryczne typu Integer, Twoje dane zostaną podzielone na dwie kolumny:

- **propertyValue_double**
- **propertyValue_long**

Dane liczb całkowitych są zapisywane do **propertyValue_long**. Poprzednio pozyskiwane (i przyszłe pozyskiwane) dane liczbowe w **propertyValue_double** nie są kopiowane.

Jeśli chcesz wykonać zapytanie o dane w tych dwóch kolumnach dla właściwości **PropertyValue** , musisz użyć funkcji skalarnej **łączenia ()** w TSX. Funkcja akceptuje argumenty tego samego **typu danych** i zwraca pierwszą wartość różną od null na liście argumentów. Aby uzyskać więcej informacji, zobacz [Azure Time Series Insights Gen2 Data Access](/rest/api/time-series-insights/reference-time-series-expression-syntax#other-functions).

#### <a name="variable-definition-in-tsx---numeric"></a>Definicja zmiennej w TSX — wartość liczbowa

*Definicja poprzedniej zmiennej:*

[![Zrzut ekranu przedstawia okno dialogowe Dodawanie nowej zmiennej dla zmiennej PropertyValue, numeryczne.](media/time-series-insights-long-data-type/var-def-previous.png)](media/time-series-insights-long-data-type/var-def-previous.png#lightbox)

*Nowa definicja zmiennej:*

[![Zrzut ekranu przedstawia okno dialogowe Dodawanie nowej zmiennej dla zmiennej PropertyValue z wartością niestandardową, numeryczną.](media/time-series-insights-long-data-type/var-def.png)](media/time-series-insights-long-data-type/var-def.png#lightbox)

Można również użyć **łączenia ($Event. PropertyValue. Double, ToDouble — ($Event. PropertyValue. Long))** jako [wyrażenia niestandardowego szeregu czasowego](/rest/api/time-series-insights/reference-time-series-expression-syntax).

#### <a name="inline-variable-definition-using-tsx-query-apis---numeric"></a>Wbudowana definicja zmiennej przy użyciu interfejsów API zapytań TSX — wartość liczbowa

*Definicja poprzedniej zmiennej:*

```JSON
"PropertyValueVariable": {

    "kind": "numeric",

    "value": {

        "tsx": "$event.propertyValue.Double"

    },

    "filter": null,

    "aggregation": {

        "tsx": "avg($value)"
    }
}
```

*Nowa definicja zmiennej:*

```JSON
"PropertyValueVariable ": {

    "kind": "numeric",

    "value": {

        "tsx": "coalesce($event.propertyValue.Long, toLong($event.propertyValue.Double))"

    },

    "filter": null,

    "aggregation": {

        "tsx": "avg($value)"
    }
}
```

Można również użyć **łączenia ($Event. PropertyValue. Double, ToDouble — ($Event. PropertyValue. Long))** jako [wyrażenia niestandardowego szeregu czasowego](/rest/api/time-series-insights/reference-time-series-expression-syntax).

> [!NOTE]
> Zalecamy, aby zaktualizować te zmienne we wszystkich miejscach, w których mogą być używane. Te miejsca obejmują model szeregów czasowych, zapisane zapytania i zapytania łączników Power BI.

### <a name="case-3-using-categorical-variables-to-map-integer-values-to-categories"></a>Przypadek 3: Używanie zmiennych kategorii do mapowania wartości całkowitych na kategorie

Jeśli obecnie używasz zmiennych kategorii, które mapują wartości całkowite na kategorie, możesz użyć funkcji **toLong** do konwertowania danych z typu **Double** na typ **Long** . Podobnie jak w przypadku przypadków 1 i 2, należy połączyć kolumny **typu** **Double** i **Long** .

#### <a name="variable-definition-in-time-series-explorer---categorical"></a>Definicja zmiennej w Eksploratorze szeregów czasowych — kategorii

*Definicja poprzedniej zmiennej:*

[![Zrzut ekranu przedstawia okno dialogowe Dodawanie nowej zmiennej dla zmiennej PropertyValue, kategorii.](media/time-series-insights-long-data-type/var-def-cat-previous.png)](media/time-series-insights-long-data-type/var-def-cat-previous.png#lightbox)

*Nowa definicja zmiennej:*

[![Zrzut ekranu przedstawia okno dialogowe Dodawanie nowej zmiennej dla zmiennej PropertyValue z wartością niestandardową kategorii.](media/time-series-insights-long-data-type/var-def-cat.png)](media/time-series-insights-long-data-type/var-def-cat.png#lightbox)

Można również użyć **łączenia ($Event. PropertyValue. Double, ToDouble — ($Event. PropertyValue. Long))** jako [wyrażenia niestandardowego szeregu czasowego](/rest/api/time-series-insights/preview#time-series-expression-and-syntax).

Zmienne kategorii nadal wymagają wartości typu Integer. Typ **danych** wszystkich argumentów w elemencie **łączenia ()** musi być typu **Long** w niestandardowym [wyrażeniu szeregów czasowych.](/rest/api/time-series-insights/reference-time-series-expression-syntax)

#### <a name="inline-variable-definition-using-tsx-query-apis---categorical"></a>Wbudowana definicja zmiennej przy użyciu interfejsów API zapytań TSX — kategorii

*Definicja poprzedniej zmiennej:*

```JSON
"PropertyValueVariable_Long": {

    "kind": "categorical",

    "value": {

        "tsx": "tolong($event.propertyValue.Double)"

    },

    "categories": [

    {
        "label": "Good",

        "values": [0, 1, 2 ]

    },

    {

        "label": "Bad",

        "values": [ 3, 4 ]

    } ],

    "defaultCategory": {

        "label": "Unknown"

    }
}
```

*Nowa definicja zmiennej:*

```JSON
"PropertyValueVariable_Long": {

    "kind": "categorical",

    "value": {

        "tsx": "coalesce($event.propertyValue.Long, tolong($event.propertyValue.Double))"

    },

    "categories": [

    {
        "label": "Good",

        "values": [0, 1, 2 ]

    },

    {

        "label": "Bad",

        "values": [ 3, 4 ]

    } ],

    "defaultCategory": {

        "label": "Unknown"

    }
}
```

Zmienne kategorii nadal wymagają wartości typu Integer. Typ **danych** wszystkich argumentów w elemencie **łączenia ()** musi być typu **Long** w niestandardowym [wyrażeniu szeregów czasowych](/rest/api/time-series-insights/reference-time-series-expression-syntax).

> [!NOTE]
> Zalecamy, aby zaktualizować te zmienne we wszystkich miejscach, w których mogą być używane. Te miejsca obejmują model szeregów czasowych, zapisane zapytania i zapytania łączników Power BI.

### <a name="case-4-using-the-javascript-sdk-to-build-a-custom-front-end-application"></a>Przypadek 4. Używanie zestawu SDK języka JavaScript do tworzenia niestandardowej aplikacji frontonu

Jeśli masz wpływ na przypadki od 1 do 3 i kompilacji aplikacji niestandardowych, musisz zaktualizować zapytania, aby użyć funkcji **łączenia ()** , jak pokazano w poprzednich przykładach.

### <a name="case-5-nearing-warm-store-1000-property-limit"></a>Przypadek 5: zbliżanie się limitu właściwości 1 000 w sklepie ciepłym

Jeśli jesteś użytkownikiem sklepu ze zbyt dużą liczbą właściwości i uważasz, że ta zmiana spowodowałaby wypchnięcie środowiska w ramach limitu nazw właściwości magazynu ciepłego 1 000, Prześlij bilet pomocy technicznej przez Azure Portal i podaj tę komunikację.

## <a name="next-steps"></a>Następne kroki

- Zapoznaj się z pełną listą [obsługiwanych typów danych](concepts-supported-data-types.md).