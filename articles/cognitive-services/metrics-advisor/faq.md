---
title: Często zadawane pytania dotyczące klasyfikatora metryk
titleSuffix: Azure Cognitive Services
description: Często zadawane pytania dotyczące usługi Advisor metryk.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 09/10/2020
ms.author: aahi
ms.openlocfilehash: 0fde9a0f46073a2f3a24962ea58431581455f474
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/22/2020
ms.locfileid: "90939046"
---
# <a name="metrics-advisor-frequently-asked-questions"></a>Często zadawane pytania dotyczące klasyfikatora metryk

### <a name="what-is-the-cost-of-my-instance"></a>Jaki jest koszt mojego wystąpienia?

Nie ma obecnie kosztu na korzystanie z Twojego wystąpienia w wersji zapoznawczej.

### <a name="why-is-the-demo-website-readonly"></a>Dlaczego demonstracyjna witryna sieci Web jest tylko do odczytu?

[Demonstracyjna witryna sieci Web](https://anomaly-detector.azurewebsites.net/) jest publicznie dostępna. To wystąpienie jest tylko do odczytu, aby zapobiec przypadkowemu przekazywaniu jakichkolwiek danych.

### <a name="why-cant-i-create-the-resource-the-pricing-tier-is-unavailable-and-it-says-you-have-already-created-1-s0-for-this-subscription"></a>Dlaczego nie mogę utworzyć zasobu? "Warstwa cenowa" jest niedostępna i mówi "utworzono już 1 S0 dla tej subskrypcji"?

:::image type="content" source="media/pricing.png" alt-text="Komunikat, gdy istnieje już zasób F0":::

W publicznej wersji zapoznawczej można utworzyć tylko jedno wystąpienie klasyfikatora metryk w ramach subskrypcji w jednym regionie.

Jeśli masz już utworzone wystąpienie w tym samym regionie, w którym jest używana ta sama subskrypcja, możesz wypróbować inny region lub inną subskrypcję, aby utworzyć nowe wystąpienie. Możesz również usunąć istniejące wystąpienie, aby utworzyć nowe.

Jeśli istniejące wystąpienie zostało już usunięte, ale nadal widzisz błąd, przed utworzeniem nowego wystąpienia Zaczekaj około 20 minut od usunięcia zasobu.

## <a name="basic-concepts"></a>Podstawowe pojęcia

### <a name="what-is-multi-dimensional-time-series-data"></a>Co to jest wielowymiarowe dane szeregów czasowych?

Zobacz [wielowymiarową definicję metryki](glossary.md#multi-dimensional-metric)  w słowniku.

### <a name="how-much-data-is-needed-for-metrics-advisor-to-start-anomaly-detection"></a>Ile danych jest potrzebnych do uruchomienia usługi klasyfikator metryk w celu rozpoczęcia wykrywania anomalii?

Co najmniej jeden punkt danych może wyzwolić wykrywanie anomalii. Nie przynosi to jednak najlepszej dokładności. Usługa przyjmie okno poprzednich punktów danych przy użyciu wartości określonej jako reguła "Fill-Gap" podczas tworzenia źródła danych.

Zalecamy posiadanie pewnych danych przed upływem sygnatury czasowej, która ma zostać wykrycie.
Na podstawie stopnia szczegółowości danych zalecana ilość danych różni się w zależności od tego, co podano poniżej.

| Poziom szczegółowości | Zalecana ilość danych do wykrycia |
| ----------- | ------------------------------------- |
| Mniej niż 5 minut | 4 dni danych |
| 5 minut do 1 dnia | 28 dni danych |
| Więcej niż 1 dzień, do 31 dni | 4 lata na dane |
| Więcej niż 31 dni | 48 lat danych |

### <a name="why-metrics-advisor-doesnt-detect-anomalies-from-historical-data"></a>Dlaczego klasyfikator metryk nie wykrywa anomalii z danych historycznych?

Usługa Advisor Metrics została zaprojektowana do wykrywania danych przesyłanych strumieniowo na żywo. Istnieje ograniczenie maksymalnej długości danych historycznych, które usługa będzie wyszukiwać z powrotem i uruchamiania wykrywania anomalii. Oznacza to, że tylko punkty danych po pewnym najwcześniejszym znaczniku czasu będą miały wyniki wykrywania anomalii. Najwcześniejsza sygnatura czasowa zależy od stopnia szczegółowości danych.

Na podstawie stopnia szczegółowości danych dane historyczne, które mają wynik wykrywania anomalii, są następujące.

| Poziom szczegółowości | Maksymalna długość danych historycznych na potrzeby wykrywania anomalii |
| ----------- | ------------------------------------- |
| Mniej niż 5 minut | Czas dołączania — 13 godzin |
| 5 minut do mniej niż 1 godzina | Czas dołączania — 4 dni  |
| 1 godzina do mniej niż 1 dzień | Czas dołączania — 14 dni  |
| 1 dzień | Czas dołączania — 28 dni  |
| Więcej niż 1 dzień, mniej niż 31 dni | Czas dołączania — 2 lata  |
| Więcej niż 31 dni | Czas dołączania — 24 lata   |

### <a name="more-concepts-and-technical-terms"></a>Więcej pojęć i warunków technicznych

Przejdź do [słownika](glossary.md) , aby dowiedzieć się więcej.

## <a name="how-do-i-detect-such-kinds-of-anomalies"></a>Jak mogę wykryć takie rodzaje anomalii? 

### <a name="how-do-i-detect-spikes--dips-as-anomalies"></a>Jak mogę wykryć skoki & spadnie jako anomalie?

Jeśli masz wstępnie zdefiniowane progi, możesz w rzeczywistości ręcznie ustawić "próg twardy" w [konfiguracjach wykrywania anomalii](how-tos/configure-metrics.md#anomaly-detection-methods).
Jeśli nie ma żadnych progów, można użyć "Inteligentne wykrywanie", które jest obsługiwane przez AI. Aby uzyskać szczegółowe informacje, zapoznaj się z tematem [Dostosowywanie konfiguracji wykrywania](how-tos/configure-metrics.md#tune-the-detecting-configuration) .

### <a name="how-do-i-detect-inconformity-with-regular-seasonal-patterns-as-anomalies"></a>Jak mogę wykryć niezgodność ze zwykłymi (sezonowymi) wzorcami jako anomalie?

"Inteligentne wykrywanie" jest w stanie poznać wzorzec danych, w tym wzorce sezonowe. Następnie wykrywa te punkty danych, które nie są zgodne ze zwykłymi wzorcami jako anomalie. Aby uzyskać szczegółowe informacje, zapoznaj się z tematem [Dostosowywanie konfiguracji wykrywania](how-tos/configure-metrics.md#tune-the-detecting-configuration) .

### <a name="how-do-i-detect-flat-lines-as-anomalies"></a>Jak mogę wykryć płaskie linie jako anomalie?

Jeśli dane są zwykle dość niestabilne i wymagają dużej ilości danych, a użytkownik chce otrzymywać alerty, gdy zostanie on zbyt stabilny lub nawet stanie się linią płaską, "próg zmiany" można skonfigurować do wykrywania takich punktów danych, gdy zmiana jest zbyt mała.
Szczegóły można znaleźć w [konfiguracjach wykrywania anomalii](how-tos/configure-metrics.md#anomaly-detection-methods) .

## <a name="next-steps"></a>Następne kroki
- [Omówienie klasyfikatora metryk](overview.md)
- [Wypróbuj witrynę demonstracyjną](quickstarts/explore-demo.md)
- [Używanie portalu internetowego](quickstarts/web-portal.md)