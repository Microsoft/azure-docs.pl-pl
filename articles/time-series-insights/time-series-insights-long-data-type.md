---
title: Dodawanie obsługi dla długich typów danych | Microsoft Docs
description: Obsługa długich typów danych
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.topic: conceptual
ms.date: 06/16/2020
ms.custom: dpalled
ms.openlocfilehash: ebb62b67b56134902f2752b43dd25fb0a7c6ccd4
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/07/2020
ms.locfileid: "86045777"
---
# <a name="adding-support-for-long-data-type"></a>Dodawanie obsługi dla długich typów danych

Te zmiany zostaną zastosowane tylko do środowisk wersji zapoznawczej (PAYG). Jeśli masz standardowe środowisko w wersji TSI, możesz zignorować te zmiany.

Wprowadzamy zmiany w sposobie przechowywania i indeksowania danych liczbowych w wersji zapoznawczej Azure Time Series Insights, które mogą mieć wpływ na Ciebie. Jeśli dotyczy to dowolnego z poniższych przypadków, wprowadź niezbędne zmiany tak szybko, jak to możliwe. Twoje dane będą uruchamiane jako długie i podwójne od 29 czerwca do 30 czerwca 2020, w zależności od regionu. Jeśli masz jakieś pytania lub wątpliwości dotyczące tej zmiany, Prześlij bilet pomocy technicznej przez Azure Portal i podaj tę komunikację.

Ta zmiana ma wpływ na następujące sytuacje:

1. Jeśli obecnie używasz zmiennych modelu szeregów czasowych i wysyłasz tylko integralne typy danych w danych telemetrycznych.
1. Jeśli obecnie używasz zmiennych modelu szeregów czasowych i wysyłasz zarówno całkowity, jak i niecałkowity typ danych w danych telemetrycznych.
1. Jeśli używasz zmiennych kategorii do mapowania wartości całkowitych na kategorie.
1. Jeśli używasz zestawu SDK języka JavaScript do tworzenia niestandardowej aplikacji frontonu.
1. Jeśli zbliżasz się do 1 000 — limit nazw właściwości w sklepie ciepłym (WS) i wysyłasz zarówno dane całkowite, jak i niecałkowite, liczba właściwości może być wyświetlana jako Metryka w [Azure Portal](https://portal.azure.com/).

Jeśli którykolwiek z powyższych przypadków dotyczy Ciebie, należy wprowadzić zmiany w modelu w celu uwzględnienia tej zmiany. Zaktualizuj wyrażenie szeregów czasowych w definicji zmiennej w Eksploratorze TSI i w dowolnym kliencie niestandardowym przy użyciu naszych interfejsów API z zalecanymi zmianami. Aby uzyskać szczegółowe informacje, zobacz poniżej.

W zależności od rozwiązania IoT i ograniczeń, użytkownik może nie mieć wglądu w dane wysyłane do środowiska TSI PAYG. Jeśli nie masz pewności, czy dane są tylko integralne, czy zarówno integralne, jak i niecałkowite, możesz korzystać z kilku opcji. Możesz poczekać, aż funkcja zostanie wydana, a następnie poznać zdarzenia pierwotne w interfejsie użytkownika Eksploratora, aby zrozumieć, które właściwości zostały zapisane w dwóch oddzielnych kolumnach. Można zapobiegawczo wprowadzić poniższe zmiany dla wszystkich tagów numerycznych lub tymczasowo przekierować podzestaw zdarzeń do magazynu, aby lepiej zrozumieć i eksplorować schemat. Aby przechowywać zdarzenia, Włącz funkcję [przechwytywania zdarzeń](https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview) dla Event Hubs lub [Roześlij](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c#azure-storage) z IoT Hub do BLOB Storage platformy Azure. Dane można także zaobserwować za pośrednictwem [Eksploratora centrum zdarzeń](https://marketplace.visualstudio.com/items?itemName=Summer.azure-event-hub-explorer)lub za pomocą [hosta procesora zdarzeń](https://docs.microsoft.com/azure/event-hubs/event-hubs-dotnet-standard-getstarted-send#receive-events). Jeśli używasz IoT Hub, zapoznaj się z dokumentacją znajdującą się [tutaj](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-read-builtin) , jak uzyskać dostęp do wbudowanego punktu końcowego.

Należy pamiętać, że jeśli na pewno te zmiany mają wpływ i nie są w stanie wykonać powyższych dat, może wystąpić zakłócenie, w którym zmienne szeregów czasowych, do których uzyskuje się dostęp za pośrednictwem interfejsów API zapytań lub Eksploratora Time Series Insights zwróci *wartość null* (tj. nie pokazywanie danych w Eksploratorze).

## <a name="recommended-changes"></a>Zalecane zmiany

Przypadek 1 & 2: **Używanie zmiennych modelu szeregów czasowych i wysyłanie tylko całkowitych typów danych lub wysyłanie zarówno typów całkowitych, jak i niecałkowitych w danych telemetrycznych.**

Jeśli aktualnie wysyłasz dane telemetryczne typu Integer, dane zostaną podzielone na dwie kolumny: "propertyValue_double" i "propertyValue_long".

Dane całkowite będą zapisywane w "propertyValue_long", gdy zmiany zaczną obowiązywać, a poprzednio pozyskiwane (i przyszłe w przyszłości) dane liczbowe w "propertyValue_double" nie będą kopiowane.

Jeśli chcesz wykonywać zapytania dotyczące danych w tych dwóch kolumnach dla właściwości "propertyValue", musisz użyć funkcji skalarnej *łączenia ()* w TSX. Funkcja akceptuje argumenty tego samego typu danych i zwraca pierwszą wartość różną od null na liście argumentów (więcej informacji na temat użycia można znaleźć [tutaj](https://docs.microsoft.com/rest/api/time-series-insights/preview#other-functions)).

### <a name="variable-definition-in-time-series-explorer---numeric"></a>Definicja zmiennej w Eksploratorze szeregów czasowych — wartość numeryczna

*Definicja poprzedniej zmiennej:*

[![Definicja poprzedniej zmiennej](media/time-series-insights-long-data-type/var-def-previous.png)](media/time-series-insights-long-data-type/var-def-previous.png#lightbox)

*Nowa definicja zmiennej:*

[![Nowa definicja zmiennej](media/time-series-insights-long-data-type/var-def.png)](media/time-series-insights-long-data-type/var-def.png#lightbox)

Można również użyć *"łączenia ($Event. PropertyValue. Double, ToDouble — ($Event. PropertyValue. Long))"* jako niestandardowego [wyrażenia szeregów czasowych.](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax)

### <a name="inline-variable-definition-using-time-series-query-apis---numeric"></a>Wbudowana definicja zmiennej przy użyciu interfejsów API zapytań szeregów czasowych — wartość liczbowa

*Definicja poprzedniej zmiennej:*

    "PropertyValueVariable": {

        "kind": "numeric",

        "value": {

            "tsx": "$event.propertyValue.Double"

        },

        "filter": null,

        "aggregation": {

            "tsx": "avg($value)"

*Nowa definicja zmiennej:*

    "PropertyValueVariable ": {

        "kind": "numeric",

        "value": {

            "tsx": "coalesce($event.propertyValue.Long, toLong($event.propertyValue.Double))"

        },

        "filter": null,

        "aggregation": {

            "tsx": "avg($value)"

Można również użyć *"łączenia ($Event. PropertyValue. Double, ToDouble — ($Event. PropertyValue. Long))"* jako niestandardowego [wyrażenia szeregów czasowych.](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax)

> [!NOTE]
> Zalecamy aktualizowanie tych zmiennych we wszystkich miejscach, w których mogą być używane (model szeregów czasowych, zapisane zapytania, Power BI zapytania łącznika).

Przypadek 3: **Używanie zmiennych kategorii do mapowania wartości całkowitych na kategorie**

Jeśli obecnie używasz zmiennych kategorii, które mapują wartości całkowite na kategorie, możesz użyć funkcji toLong do konwertowania danych z typu Double na typ Long. Podobnie jak w przypadku powyższych przypadków, należy połączyć kolumny Double i Long typu DataType.

### <a name="variable-definition-in-time-series-explorer---categorical"></a>Definicja zmiennej w Eksploratorze szeregów czasowych — kategorii

*Definicja poprzedniej zmiennej:*

[![Definicja poprzedniej zmiennej](media/time-series-insights-long-data-type/var-def-cat-previous.png)](media/time-series-insights-long-data-type/var-def-cat-previous.png#lightbox)

*Nowa definicja zmiennej:*

[![Nowa definicja zmiennej](media/time-series-insights-long-data-type/var-def-cat.png)](media/time-series-insights-long-data-type/var-def-cat.png#lightbox)

Można również użyć *"łączenia ($Event. PropertyValue. Double, ToDouble — ($Event. PropertyValue. Long))"* jako niestandardowego [wyrażenia szeregów czasowych.](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax)

Zmienne kategorii nadal wymagają wartości typu Integer. Typ danych wszystkich argumentów w elemencie łączenia () musi być typu Long w niestandardowym [wyrażeniu szeregów czasowych.](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax)

### <a name="inline-variable-definition-using-time-series-query-apis---categorical"></a>Wbudowana definicja zmiennej przy użyciu interfejsów API zapytań szeregów czasowych — kategorii

*Definicja poprzedniej zmiennej:*

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

*Nowa definicja zmiennej:*

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

Zmienne kategorii nadal wymagają wartości typu Integer. Typ danych wszystkich argumentów w elemencie łączenia () musi być typu Long w niestandardowym [wyrażeniu szeregów czasowych.](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax)

> [!NOTE]
> Zalecamy aktualizowanie tych zmiennych we wszystkich miejscach, w których mogą być używane (model szeregów czasowych, zapisane zapytania, Power BI zapytania łącznika).

Przypadek 4. **Używanie zestawu SDK języka JavaScript do tworzenia niestandardowej aplikacji frontonu**

Jeśli dotyczy to przypadków 1-3 powyżej i kompilowania niestandardowych aplikacji, należy zaktualizować zapytania tak, aby korzystały z funkcji *łączenia ()* , jak pokazano w powyższym przykładzie.

Przypadek 5: **zbliżanie się limitu właściwości 1 000 w sklepie ciepłym**

Jeśli jesteś użytkownikiem sklepu ze zbyt dużą liczbą właściwości i uważasz, że ta zmiana spowodowałaby wypchnięcie środowiska przy użyciu limitu nazw właściwości 1 000 WS, Prześlij bilet pomocy technicznej przez Azure Portal i podaj tę komunikację.

## <a name="next-steps"></a>Następne kroki

* Zapoznaj się z [tym](concepts-supported-data-types.md) artykułem, aby wyświetlić pełną listę obsługiwanych typów danych.
