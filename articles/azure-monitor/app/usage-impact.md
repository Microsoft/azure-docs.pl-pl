---
title: Wpływ na korzystanie z platformy Azure Application Insights | Dokumentacja firmy Microsoft
description: Analizuj, w jaki sposób różne właściwości mogą mieć wpływ na szybkość konwersji dla części aplikacji.
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 01/08/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 8ce49488124f07f05b8df2d9f4eae41e041aa0aa
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2021
ms.locfileid: "105026193"
---
# <a name="impact-analysis-with-application-insights"></a>Analiza wpływu za pomocą Application Insights

Wpływ analizuje, jak czasy ładowania i inne właściwości wpływają na szybkość konwersji dla różnych części aplikacji. Aby dokładniej wprowadzić ten element, wykrywa on, jak **każdy wymiar** **widoku strony**, **zdarzenia niestandardowe** lub **żądanie** ma wpływ na użycie innego **widoku strony** lub **zdarzenia niestandardowego**. 

![Narzędzie do wpływu](./media/usage-impact/0001-impact.png)

## <a name="still-not-sure-what-impact-does"></a>Nadal nie masz pewności, jaki ma wpływ?

Jednym ze sposobów na zaspokojenie wpływu jest to ostateczne narzędzie do rozliczania argumentów dla kogoś w zespole o tym, jak spowolnienie w niektórych aspektach witryny ma wpływ na to, czy użytkownicy są w niej odkładać. Gdy użytkownicy mogą tolerować określoną ilość wolnego czasu, wpływ na to, jak najlepiej zrównoważyć optymalizację i wydajność w celu zmaksymalizowania konwersji użytkownika.

Jednak analizowanie wydajności jest tylko podzbiorem możliwości. Ze względu na to, że wpływ obsługuje zdarzenia niestandardowe i wymiary, odpowiedzi na pytania, takie jak wybór przeglądarki użytkownika jest skorelowany z różnymi stawkami konwersji, to zaledwie kilka kliknięć.

![Konwersja zrzutu ekranu według przeglądarek](./media/usage-impact/0004-browsers.png)

> [!NOTE]
> Zasób Application Insights musi zawierać widoki stron lub zdarzenia niestandardowe, aby można było użyć narzędzia do wpływu. [Dowiedz się, jak skonfigurować aplikację do automatycznego zbierania wyświetleń stron przy użyciu zestawu SDK języka JavaScript Application Insights](./javascript.md). Należy również pamiętać, że ponieważ analizowanie korelacji, wielkość próbki jest ważna.
>
>

## <a name="is-page-load-time-impacting-how-many-people-convert-on-my-page"></a>Czy czas ładowania strony ma wpływ na liczbę osób konwertowanych na moją stronę?

Aby zacząć odpowiadać na pytania za pomocą narzędzia do obsługi, wybierz początkowy widok strony, zdarzenie niestandardowe lub żądanie.

![Zrzut ekranu pokazujący, gdzie wybrać początkowy widok strony, zdarzenie niestandardowe lub żądanie.](./media/usage-impact/0002-dropdown.png)

1. Wybierz widok strony z listy rozwijanej **dla widoku strony** .
2. Pozostaw listę rozwijaną, w **jaki sposób** zostanie ona rozwinięta, w domyślnym wyborze **czasu trwania** (w tym **czasie trwania** kontekstu jest alias **czasu ładowania strony**).
3. Aby uzyskać **wpływ na użycie** listy rozwijanej, wybierz zdarzenie niestandardowe. To zdarzenie powinno odpowiadać elementowi interfejsu użytkownika w widoku strony wybranym w kroku 1.

![Zrzut ekranu przedstawiający wyniki](./media/usage-impact/0003-results.png)

W tym przypadku, gdy czas ładowania **strony produktu** zwiększa szybkość konwersji **klikniętego produktu** , zostaje wyłączona. Zgodnie z powyższym rozkładem optymalny czas ładowania strony wynoszący 3,5 sekund może być celem osiągnięcia potencjalnego współczynnika konwersji 55%. Dalsze ulepszenia wydajności pozwalające zmniejszyć czas ładowania poniżej 3,5 sekund nie są obecnie skorelowane z dodatkowymi korzyściami z konwersji.

## <a name="what-if-im-tracking-page-views-or-load-times-in-custom-ways"></a>Co zrobić, Jeśli śledzisz widoki stron lub czasy ładowania w sposób niestandardowy?

Wpływ obsługuje zarówno standardowe, jak i niestandardowe właściwości i pomiary. Użyj dowolnej z nich. Zamiast czasu trwania należy użyć filtrów dla zdarzeń podstawowych i pomocniczych w celu uzyskania bardziej szczegółowych informacji.

## <a name="do-users-from-different-countries-or-regions-convert-at-different-rates"></a>Czy użytkownicy z różnych krajów lub regionów konwertują różne stawki?

1. Wybierz widok strony z listy rozwijanej **dla widoku strony** .
2. Wybierz pozycję "kraj lub region" w temacie **Analizowanie sposobu jego** rozwijania
3. Aby uzyskać **wpływ na użycie** listy rozwijanej, wybierz niestandardowe zdarzenie odpowiadające ELEMENTOWI interfejsu użytkownika w widoku strony wybranym w kroku 1.

W takim przypadku wyniki nie pasują do ciągłego modelu osi x, jak w pierwszym przykładzie. Zamiast tego jest prezentowana Wizualizacja podobna do lejka z segmentacją. Sortuj według **użycia** , aby wyświetlić odmianę konwersji do zdarzenia niestandardowego na podstawie kraju/regionu.


## <a name="how-does-the-impact-tool-calculate-these-conversion-rates"></a>Jak narzędzie do obliczania wpływu oblicza te szybkości konwersji?

W obszarze okapu narzędzie do wpływu zależy od [współczynnika korelacji Pearsona](https://en.wikipedia.org/wiki/Pearson_correlation_coefficient). Wyniki są obliczane między-1 i 1 z-1 reprezentujące negatywną korelację liniową i 1 reprezentującą dodatnią korelację liniową.

Podstawowe informacje na temat sposobu działania analizy wpływu są następujące:

Zezwól _na to_ , aby widok strony głównej/zdarzenie niestandardowe/żądanie wybrane w pierwszej liście rozwijanej. (**W przypadku widoku strony**).

Zezwól _B_ = widok strony dodatkowej/wybrane zdarzenie niestandardowe (**wpływ na użycie**).

Wpływ na próbkę wszystkich sesji od użytkowników w wybranym zakresie czasu. Dla każdej sesji szuka każdego wystąpienia _A_.

Sesje są podzielone na dwa różne rodzaje _podsesji_ w oparciu o jeden z dwóch warunków:

- Przekonwertowane podsesja składa się z sesji kończącej się na zdarzeniu _b_ i _obejmuje wszystkie zdarzenia, które_ wystąpiły przed _B_.
- Nieskonwertowane podsesja _występuje, gdy wszystkie wystąpienia_ wystąpią bez terminalu _B_.

Sposób obliczenia wpływu jest różny w zależności od tego, czy analizujemy według metryki, czy przez wymiar. Dla metryk wszystkie _a_ w podsesji są uśredniane. W odniesieniu do wymiarów każda wartość _A_ składa się z _1/N_ do wartości przypisanej do _B_ , gdzie _N_ to _Liczba w_ podsesji.

## <a name="next-steps"></a>Następne kroki

- Aby włączyć środowiska użycia, Rozpocznij wysyłanie [zdarzeń niestandardowych](./api-custom-events-metrics.md#trackevent) lub [wyświetleń stron](./api-custom-events-metrics.md#page-views).
- Jeśli masz już wysłane zdarzenia niestandardowe lub widoki stron, zapoznaj się z narzędziami użycia, aby dowiedzieć się, jak użytkownicy korzystają z usługi.
    - [Lejki](usage-funnels.md)
    - [Noszą](usage-retention.md)
    - [Przepływy użytkownika](usage-flows.md)
    - [Skoroszyty](../visualize/workbooks-overview.md)
    - [Dodawanie kontekstu użytkownika](./usage-overview.md)