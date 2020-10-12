---
title: Pulpit nawigacyjny podsumowania dla analiz Centrum partnerskiego w komercyjnej witrynie Marketplace
description: Dowiedz się, jak uzyskiwać dostęp do grafów, trendów i wartości zagregowanych danych, które podsumowują aktywność witryny Marketplace z poziomu pulpitu nawigacyjnego podsumowanie w centrum partnerskim.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/11/2019
author: mingshen-ms
ms.author: mingshen
ms.openlocfilehash: 8e58de7975bc7f5de1fe2ad71f97c02c901c0c21
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87304126"
---
# <a name="summary-dashboard-in-commercial-marketplace-analytics"></a>Pulpit nawigacyjny Podsumowanie w analizie komercyjnej platformy handlowej

Ten artykuł zawiera informacje na temat pulpitu nawigacyjnego podsumowanie w centrum partnerskim. Ten pulpit nawigacyjny wyświetla wykresy, trendy i wartości zagregowanych danych, które podsumowują aktywność witryny Marketplace dla ofert.

Aby uzyskać dostęp do pulpitu nawigacyjnego podsumowanie, Otwórz **[pulpit nawigacyjny Analizuj](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)** w obszarze komercyjny Portal Marketplace.

>[!NOTE]
> Aby uzyskać szczegółowe definicje terminologii dotyczącej analizy, zobacz [często zadawane pytania i terminologię dotyczącą komercyjnej analizy portalu Marketplace](./faq-terminology.md).

## <a name="summary-dashboard"></a>Pulpit nawigacyjny Podsumowanie

Pulpit nawigacyjny **podsumowania** przedstawia przegląd w zależności od typu oferty. **Szczegółowe** informacje są wyświetlane w skrócie i zapewniają szeroką gamę działań sprzedaży oferowanych przez oferty. Możesz wizualizować te raporty przy użyciu pulpitu nawigacyjnego **Podsumowanie** . Ten artykuł zawiera więcej informacji na temat każdego z następujących elementów:

- [Zakres dat](#date-range)
- [Sekcja podsumowania](#summary-section)
- [Klienci według lokalizacji geograficznej](#customers-by-geography)
- [Wykresy trendu wzrostu](#growth-trend-charts)
- [Rankingów klienta](#customer-leaderboard)
- [Trend liczby stanowisk](#seat-count-trend)
- [Trend zamówień SaaS bezpłatnych wersji próbnych](#free-trials-saas-orders-trend)

## <a name="elements-of-the-summary-dashboard"></a>Elementy pulpitu podsumowania

W poniższych sekcjach opisano, jak używać pulpitu nawigacyjnego podsumowanie i jak odczytywać dane.

### <a name="date-range"></a>Zakres dat

Wybór zakresu dat można znaleźć w prawym górnym rogu każdej strony. Dostosuj dane wyjściowe wykresów stron **podsumowujących** , wybierając zakres dat na podstawie ostatnich 3, 6 lub 12 miesięcy lub wybierając niestandardowy zakres dat o maksymalnym okresie wynoszącym 12 miesięcy. Domyślny zakres dat to sześć miesięcy.

![Pulpit nawigacyjny analizy Centrum partnerskiego](./media/analyze-dashboard.png)

### <a name="summary-section"></a>Sekcja podsumowania

Sekcja podsumowania zawiera liczbę utworzonych zamówień, nabytych klientów i użycie w ramach wybranego zakresu dat. Częściowo bieżący miesiąc zostanie wykluczony z obliczeń tych metryk. Na przykład: Jeśli wybrano przedział czasu 6 min, godziny użycia są obliczane przez sześć miesięcy przed bieżącym miesiącem. W przypadku wybrania niestandardowego zakresu dat wartość częściowa od bieżącego miesiąca zostanie wykluczona z obliczenia.

![Trendy wzrostu na pulpicie nawigacyjnym podsumowania](./media/summary-summary-section.png)

#### <a name="reading-the-summary-section"></a>Odczytywanie sekcji podsumowania

- **Orders**: liczba zakupionych zamówień (wyklucza anulowane zamówienia) dla ofert, które zostały już opublikowane.
- **Klienci**: liczba wszystkich klientów, którzy kupili oferty i mają co najmniej jedno nieanulowane zamówienie.
- **Znormalizowane godziny użycia**: zdefiniowane jako godziny użycia znormalizowane dla konta dla liczby rdzeni maszyn wirtualnych ([liczba rdzeni maszyn wirtualnych] x [godz. użycia pierwotnego]). Maszyny wirtualne oznaczone jako "SHAREDCORE" wykorzystują 1/6 (lub 0,1666) jako mnożnik [liczba rdzeni maszyn wirtualnych].
- **Godziny użycia pierwotnego**: czas, przez który maszyny wirtualne były uruchamiane w godzinach. Wartość procentowa obok **łącznej liczby zamówień**, **łącznej liczby klientów**, **znormalizowanych godzin użycia**, **godzin użycia nieprzetworzonego**, **odwiedzin stron**oraz **wywołania akcji** przedstawiają stopień wzrostu użycia dla wybranego zakresu dat ([ostatnie użycie miesiąca – pierwsze użycie miesiąca])/użycie pierwszego miesiąca). Zgodnie z powyższym opisem, częściowa kwota bieżącego miesiąca zostanie wykluczona z tej metryki.
- **Trendy wzrostu**: po umieszczeniu wskaźnika myszy na kolumnach wykresu wykresy słupkowe wyświetlają wartość dla każdego miesiąca.
- **Zielony trójkąt skierowany w górę**: wskazuje pozytywny trend wzrostu.
- **Czerwony trójkąt skierowany w dół**: wskazuje trend ujemnego wzrostu względem poprzedniego miesiąca.

### <a name="customers-by-geography"></a>Klienci według lokalizacji geograficznej

Mapę cieplną **klienci według lokalizacji geograficznej** wyświetlają liczbę klientów na mapie światowej.

![Pulpit nawigacyjny podsumowania klientów według lokalizacji geograficznej](./media/summary-customers-by-geography.png)

- Możesz przenieść mapę, aby wyświetlić dokładną lokalizację.
- Możesz powiększyć do określonej lokalizacji.
- Mapę cieplną ma dodatkową siatkę, aby wyświetlić szczegółowe informacje o liczbie klientów, liczbie zamówień, znormalizowanych godzinach użycia w określonej lokalizacji.
- Możesz wyszukać i wybrać kraj/region w siatce, aby powiększyć do lokalizacji na mapie. Przywróć widok oryginalny, naciskając przycisk Home ( **Strona główna** ) na mapie.
- **Nowy** klient zakupił jedną z ofert po raz pierwszy w miesiącu w ramach wybranego zakresu dat.

### <a name="growth-trend-charts"></a>Wykresy trendu wzrostu

Trendy można wyświetlać w oparciu o wzrost liczby **zakupionych zamówień** (w tym zamówień anulowanych), **klientów nabytych** (w tym utraconych klientów) i zgłoszonych danych **użycia** , które są wyświetlane w miesiącu według miesiąca zgodnie z wybranym zakresem dat. Można przeanalizować te trendy, wybierając linki poniżej wykresu, co umożliwia przejście do odpowiednich stron **zamówienia**, **użycia**, **klienta**lub **szczegółowego wglądu w dane witryny Marketplace** .

Strony oferty w witrynie Marketplace **odwiedzane i wywołujące** wykresy trendów związanych z akcją są wyświetlane w witrynie Azure Marketplace i AppSource na dwóch kartach.

![Odwiedziny stron i wywoływanie akcji wykresy trendów na pulpicie nawigacyjnym podsumowania](./media/summary-page-visits-and-cta.png)

Wykres **zamówienia według oferty** organizuje zamówienia zgodnie z nazwą oferty.

Wykres kołowy **zamówienia według kanału sprzedaży** organizuje zamówienia (w tym zamówienia, które zostały anulowane przez klientów) w wybranym zakresie dat według kanału sprzedaży. Kanał sprzedaży to typ umowy licencyjnej używanej przez klientów do kupowania platformy Azure, czyli dostawcy rozwiązań w chmurze (CSP), przedsiębiorstwa, przedsiębiorstwa przez odsprzedawcę, GTM i płatność zgodnie z rzeczywistym użyciem.

**Użycie przez oferty** i **użycie przez** wykresy kołowe kanałów sprzedaży przedstawia podział użycia odpowiednio do najlepszych ofert i kanałów sprzedaży. Wewnętrzny wykres kołowy reprezentuje pierwotne użycie i zewnętrzny wykres kołowy reprezentuje znormalizowane użycie.

Na wykresach kołowych typ licencji w **witrynie Marketplace** i **użycie według typów licencji witryny Marketplace** są wyświetlane podział zamówień i użycia według ich typu licencji. Typy licencji obejmują:

- **Rozliczanie za pomocą platformy Azure**: Firma Microsoft rozlicza klientów w Twoim imieniu w przypadku wybrania sprzedaży oferty przez firmę Microsoft przy użyciu tego typu licencji. Typy płatności obejmują płatność zgodnie z rzeczywistym użyciem za pośrednictwem karty kredytowej lub fakturowania w przedsiębiorstwie.
- **Bring Your Own License**: Firma Microsoft nie rozlicza klientów za korzystanie z tego typu oferty portalu Marketplace. To użycie jest wymienione jako **Pobierz teraz (bezpłatnie)** w portalu Marketplace.
- **Bezpłatna**: Firma Microsoft nie nalicza opłat za użycie tego typu oferty portalu Marketplace. To użycie jest wymienione jako **bezpłatna wersja próbna** w portalu Marketplace.
- **Firma Microsoft jako odsprzedawca**: reprezentuje oferty sprzedane przez odsprzedawców firmy Microsoft jako część **programu dostawcy rozwiązań w chmurze (CSP)**.

### <a name="customer-leaderboard"></a>Rankingów klienta

Najlepsi 50 klientów z największą liczbą zamówień są wyświetlane na *tablicy lidera*, posortowane według najwyższej liczby porządkowej i procentowej kolejności.

- Wybierz klienta, aby wyświetlić jego szczegóły profilu, zamówienia uporządkowane według oferty lub zamówienia uporządkowane według typu licencji platformy Azure i kanału cenowego.
- Wykres pierścieniowy **oferty wg zamówień** przedstawia cztery pierwsze oferty (według liczby porządkowej), a pozostałe oferty pogrupowane jako "REST All".
- Wykres pierścieniowy z **znormalizowanym użyciem według oferty** przedstawia pięć najważniejszych ofert według użycia.

> [!NOTE]
> Dane osobowe klienta będą prezentowane tylko wtedy, gdy klient udostępnił zgodę. Te informacje można wyświetlić, jeśli zalogowano się na poziomie uprawnień roli **właściciela** . Użytkownicy z rolą **współautor** nie będą mogli wyświetlić tych informacji. [Dowiedz się więcej o rolach i uprawnieniach użytkowników](./manage-account.md#define-user-roles-and-permissions).

### <a name="seat-count-trend"></a>Trend liczby stanowisk

Wykres **order by-stanowisko/na lokacja** przedstawia podział wszystkich zamówień zakupionych zgodnie z modelem cen. Wykres **trendu liczby stanowisk** przedstawia stanowiska i zamówienia zakupione dla wszystkich ofert dla oprogramowania na stanowisko jako usługa (SaaS).

### <a name="free-trials-saas-orders-trend"></a>Trend zamówień SaaS bezpłatnych wersji próbnych

Wykres **trendu bezpłatnej wersji próbnej SaaS** przedstawia trend zamówień na potrzeby bezpłatnych testów SaaS oferowanych przez 30-dniowy okres próbny.

## <a name="next-steps"></a>Następne kroki

- Aby zapoznać się z omówieniem raportów analitycznych dostępnych na rynku komercyjnym centrum partnerskiego, zobacz [Analiza portalu komercyjnego w centrum partnerskim](./analytics.md).
- Aby uzyskać informacje o zamówieniach w formacie graficznym i do pobrania, zobacz temat [Zamówienie pulpitu nawigacyjnego w portalu komercyjnej analizy Marketplace](./orders-dashboard.md).
- W przypadku maszyn wirtualnych (VM) są dostępne metryki dotyczące użycia i taryfowego rozliczania, zobacz [pulpit nawigacyjny użycia w portalu komercyjnej analizy Marketplace](./usage-dashboard.md).
- Aby uzyskać szczegółowe informacje na temat klientów, w tym trendów wzrostu, zobacz [pulpit nawigacyjny klienta w komercyjnej analizie portalu Marketplace](./customer-dashboard.md).
- Aby uzyskać listę żądań pobierania w ciągu ostatnich 30 dni, zobacz [pulpit nawigacyjny w portalu komercyjnej analizy Marketplace](./downloads-dashboard.md).
- Aby wyświetlić skonsolidowany widok opinii klientów na temat ofert w witrynie Azure Marketplace i AppSource, zobacz [pulpit nawigacyjny oceny i przeglądów w portalu komercyjnej analizy Marketplace](./ratings-reviews.md).
- Często zadawane pytania dotyczące analizy komercyjnej portalu Marketplace i kompleksowego słownika warunków dotyczących danych można znaleźć [w temacie często zadawane pytania i terminologia dotyczące komercyjnej analizy w portalu Marketplace](./faq-terminology.md).
