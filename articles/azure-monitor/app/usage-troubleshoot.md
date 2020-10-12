---
title: Rozwiązywanie problemów z narzędziami do analizy użytkowników — Application Insights platformy Azure
description: Przewodnik rozwiązywania problemów — analizowanie użycia witryny i aplikacji przy użyciu Application Insights.
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 07/11/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 02d1bd9d204d88ba90218b1254c66ac0da80be85
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87323506"
---
# <a name="troubleshoot-user-behavior-analytics-tools-in-application-insights"></a>Rozwiązywanie problemów z narzędziami analizy zachowania użytkownika w Application Insights
Masz pytania dotyczące [narzędzi analitycznych zachowania użytkownika w Application Insights](usage-overview.md): [Użytkownicy, sesje, zdarzenia](usage-segmentation.md), [Lejki](usage-funnels.md), [przepływy użytkownika](usage-flows.md), [przechowywanie](usage-retention.md)lub kohorty? Oto kilka odpowiedzi.

## <a name="counting-users"></a>Zliczanie użytkowników
**Narzędzia analizy zachowania użytkownika pokazują, że moja aplikacja ma jeden użytkownik/sesja, ale wiem, że moja aplikacja ma wiele użytkowników/sesji. Jak można naprawić te nieprawidłowe liczniki?**

Wszystkie zdarzenia telemetrii w Application Insights mają [Identyfikator użytkownika anonimowego](./data-model-context.md) i [Identyfikator sesji](./data-model-context.md) jako dwie ich standardowe właściwości. Domyślnie wszystkie narzędzia analizy użycia zliczają użytkowników i sesje na podstawie tych identyfikatorów. Jeśli te właściwości standardowe nie są wypełniane unikatowymi identyfikatorami dla każdego użytkownika i sesji aplikacji, zobaczysz nieprawidłową liczbę użytkowników i sesji w narzędziach analizy użycia.

W przypadku monitorowania aplikacji sieci Web najprostszym rozwiązaniem jest dodanie [Application Insights zestawu SDK języka JavaScript](./javascript.md) do aplikacji i upewnienie się, że fragment skryptu jest ładowany na każdej stronie, która ma być monitorowana. Zestaw JavaScript SDK automatycznie generuje anonimowe identyfikatory użytkowników i sesji, a następnie wypełnia zdarzenia telemetrii tymi identyfikatorami, gdy są wysyłane z aplikacji.

Jeśli monitorowana jest usługa sieci Web (bez interfejsu użytkownika), należy [utworzyć inicjatora telemetrii, który wypełnia właściwości identyfikator użytkownika anonimowego i identyfikator sesji](usage-send-user-context.md) zgodnie z pojęciem unikatowych użytkowników i sesji.

Jeśli aplikacja wysyła [uwierzytelnione identyfikatory użytkowników](./api-custom-events-metrics.md#authenticated-users), możesz policzyć na podstawie identyfikatorów użytkowników uwierzytelnionych w narzędziu użytkownicy. Na liście rozwijanej "Pokaż" Wybierz pozycję "Użytkownicy uwierzytelnieni".

Narzędzia analizy zachowania użytkownika nie obsługują obecnie zliczania użytkowników lub sesji na podstawie właściwości innych niż anonimowy identyfikator użytkownika, identyfikator uwierzytelnionego użytkownika lub identyfikator sesji.

## <a name="naming-events"></a>Nazywanie zdarzeń
**Moja aplikacja ma tysiące różnych widoków stron i niestandardowych nazw zdarzeń. Trudno jest rozróżnić między nimi, a narzędzia do analizy zachowań użytkowników często przestają odpowiadać. Jak można rozwiązać te problemy związane z nazewnictwem?**

Widok strony i niestandardowe nazwy zdarzeń są używane w całym narzędziu do analizy zachowań użytkownika. Zdarzenia nazewnictwa są niezbędne do uzyskania wartości z tych narzędzi. Celem jest równowaga między za pomocą zbyt kilku nazw ogólnych ("kliknięty przycisk") i mających zbyt wiele nazw ("przycisk edycji kliknięty na http: \/ /www.contoso.com/index").

Aby wprowadzić zmiany w widoku strony i niestandardowych nazwach zdarzeń wysyłanych przez aplikację, należy zmienić kod źródłowy i ponownie wdrożyć aplikację. **Wszystkie dane telemetryczne w Application Insights są przechowywane przez 90 dni i nie można ich usunąć**, dlatego zmiany wprowadzane do nazw zdarzeń pozostaną 90 dni do pełnego manifestu. W ciągu 90 dni od wprowadzenia zmian nazw nazwy starych i nowych zdarzeń będą wyświetlane w telemetrii, dlatego należy odpowiednio dostosować zapytania i komunikować się w zespołach.

Jeśli aplikacja wysyła zbyt wiele nazw widoku strony, sprawdź, czy te nazwy widoków stron są określone ręcznie w kodzie czy są wysyłane automatycznie przez zestaw Application Insights JavaScript SDK:

* Jeśli nazwy widoku strony są ręcznie określone w kodzie przy użyciu [ `trackPageView` interfejsu API](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md), Zmień nazwę na mniej specyficzną. Unikaj częstych pomyłek, takich jak umieszczenie adresu URL w nazwie widoku strony. Zamiast tego należy użyć parametru URL `trackPageView` interfejsu API. Przenieś inne szczegóły z nazwy widoku strony do właściwości niestandardowych.

* Jeśli Application Insights zestaw SDK języka JavaScript automatycznie wysyła nazwy widoku strony, można zmienić tytuły stron lub przełączyć się na ręczne wysyłanie nazw wyświetlanych stron. Zestaw SDK domyślnie wysyła [tytuł](https://developer.mozilla.org/docs/Web/HTML/Element/title) każdej strony jako nazwę widoku strony. Możesz zmienić tytuły, aby były bardziej ogólne, ale będzie to wiedzieć o optymalizacji i innych wpływach tej zmiany. Ręczne Określanie nazw wyświetlanych na stronie przy użyciu `trackPageView` interfejsu API zastępuje automatycznie zbierane nazwy, aby można było wysyłać więcej ogólnych nazw w telemetrii bez zmiany tytułów stron.   

Jeśli aplikacja wysyła zbyt wiele nazw zdarzeń niestandardowych, należy zmienić nazwę w kodzie na mniej specyficzną. Ponownie, Unikaj umieszczania adresów URL i innych informacji na stronie lub dynamicznych bezpośrednio w niestandardowych nazwach zdarzeń. Zamiast tego należy przenieść te szczegóły do właściwości niestandardowych zdarzenia niestandardowego z `trackEvent` interfejsem API. Na przykład zamiast nie `appInsights.trackEvent("Edit button clicked on http://www.contoso.com/index")` sugerujemy `appInsights.trackEvent("Edit button clicked", { "Source URL": "http://www.contoso.com/index" })` .

## <a name="next-steps"></a>Następne kroki

* [Narzędzia analizy zachowań użytkownika — omówienie](usage-overview.md)

## <a name="get-help"></a>Uzyskaj pomoc
* [Stack Overflow](https://stackoverflow.com/questions/tagged/ms-application-insights)

