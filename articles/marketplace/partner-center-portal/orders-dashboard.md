---
title: Pulpit nawigacyjny zamówień Centrum partnerskiego w komercyjnej analizie Marketplace
description: Dowiedz się, jak uzyskać dostęp do raportów analitycznych zamówień oferowanych w portalu Marketplace w formacie graficznym i do pobrania.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/11/2019
ms.openlocfilehash: 3b4159d81ce1d202dd1a302e863e5392f51a9868
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "83747806"
---
# <a name="orders-dashboard-in-commercial-marketplace-analytics"></a>Pulpit nawigacyjny Zamówienia w analizie komercyjnej platformy handlowej

Ten artykuł zawiera informacje na temat **pulpitu nawigacyjnego zamówienia** w centrum partnerskim. Ten pulpit nawigacyjny wyświetla informacje o zamówieniach w formacie graficznym i do pobrania.

Aby uzyskać dostęp do **pulpitu nawigacyjnego Orders** w narzędziach do analiz Centrum partnerskiego, Otwórz **[pulpit nawigacyjny Analizuj](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)** w obszarze komercyjny Portal Marketplace.

>[!NOTE]
> Aby uzyskać szczegółowe definicje terminologii dotyczącej analizy, zobacz [często zadawane pytania i terminologię dotyczącą komercyjnej analizy portalu Marketplace](./faq-terminology.md).

## <a name="orders-dashboard"></a>Pulpit nawigacyjny Zamówienia

**Pulpit nawigacyjny zamówienia** w menu **Analizuj** zawiera bieżące zamówienia dla wszystkich ofert SaaS. Można wyświetlić graficzne reprezentacje następujących elementów:

- [Zamów podsumowanie](#order-summary)
- [Zamówienia według położenia geograficznego](#orders-by-geography)
- [Zamówienia według oferty](#orders-by-offers)
- [Trend zamówień na lokację, a na stanowisko](#orders-trend-per-site-versus-per-seat)
- [Zamówienia według jednostek SKU](#orders-by-skus)
- [Trend zamówień i siedzeń](#orders-and-seats-trend)
- [Tabela szczegółów zamówienia](#order-details-table)

> [!NOTE]
> Istnieją różnice między sposobami wyświetlania raportów analizy w portal Cloud Partner (CPP) i nowym komercyjnym programie Marketplace w centrum partnerskim. Jednym z nich jest to, że informacje dostępne dla **sprzedającego** w programie CPP mają kartę **użycie zamówienia &** , która wyświetla dane dla ofert opartych na użyciu i nieopartych na użyciu. W centrum partnerskim strona **zamówienia** ma osobną kartę dla ofert SaaS.

## <a name="order-dashboard-details"></a>Szczegóły kolejności pulpitu nawigacyjnego

W tej sekcji szczegółowo opisano raporty analityczne.

### <a name="order-summary"></a>Zamów podsumowanie

Sekcja podsumowania zamówienia zawiera liczbę wszystkich zakupionych zamówień (z pominięciem zamówień anulowanych), zamówień anulowanych i siedzeń.

Wartość procentowa obok sumy zamówień reprezentuje stopień wzrostu wybranego zakresu dat.

![Podsumowanie zamówienia Centrum partnerskiego](./media/order-summary.png)

- Zielony trójkąt skierowany w górę wskazuje na pozytywny trend wzrostu.
- Czerwony trójkąt skierowany w dół wskazuje negatywny trend wzrostu względem poprzedniego miesiąca.
- Trendy wzrostu są reprezentowane przez wykresy mikro bar. Możesz wyświetlić wartość dla każdego miesiąca, umieszczając kursor nad kolumnami na wykresie.
- Zamówienia anulowane to liczba zamówień, które zostały wcześniej zakupione, a następnie anulowane w wybranym zakresie dat.
- Stanowiska to liczba siedzeń utworzonych w wybranym zakresie dat.

### <a name="orders-by-geography"></a>Zamówienia według położenia geograficznego

Mapę cieplną **Orders by geography** wyświetla liczbę zamówień na mapie światowej i pokazuje stanowiska zamapowane na podstawie kraju/regionu klienta. Ta mapę cieplną działa tak samo jak **[Klient według położenia geograficznego mapę cieplną](./customer-dashboard.md#customer-by-geography)**.

![Centrum partnerskie — analizowanie zamówień według lokalizacji geograficznej](./media/orders-by-geography.png)

### <a name="orders-by-offers"></a>Zamówienia według oferty

Wykres pierścieniowy **zamówienia według oferty** organizuje zamówienia (w tym anulowane zamówienia) zgodnie z ich nazwami ofert.

- Najważniejsze oferty są wyświetlane na wykresie, a pozostałe oferty są pogrupowane jako "REST All".
- Możesz wybrać konkretne oferty w legendzie, aby wyświetlić tylko te oferty na grafie.
- Umieszczenie kursora na wycinku na wykresie spowoduje wyświetlenie liczby zamówień i procentu oferty w porównaniu do łącznej liczby zamówień w ramach wszystkich ofert.
- **Trend zamówień według oferty** przedstawia trendy dotyczące wzrostu miesiąca. Kolumna month reprezentuje liczbę zamówień według nazwy oferty. Na wykresie liniowym jest wyświetlany trend procentu wzrostu wykreślony na osi z.
- Możesz użyć suwaka w górnej części wykresu, aby przewinąć w prawo i w lewo wzdłuż osi x i skupić się na określonych punktach danych.
- Możesz wyświetlić wykres trendu, wybierając konkretny element w legendzie.
- Możesz również wybrać, aby wyświetlić trendy i dane dla **anulowanych zamówień**. Wykres będzie działać w taki sam sposób, jak Graf **Orders by offers** .

### <a name="orders-trend-per-site-versus-per-seat"></a>Trend zamówień na lokację, a na stanowisko

Wykres pierścieniowy na lokację, a dla każdego **stanowiska na kanapie** reprezentuje podział na SaaSą witryny i zamówienia SaaS zakupione przez klientów (ten wykres zawiera anulowane zamówienia). Wykres kolumnowy reprezentuje trend dla SaaS lokacji i zamówień SaaS zamówionych przez klientów (ten wykres zawiera anulowane zamówienia).

### <a name="orders-by-skus"></a>Zamówienia według jednostek SKU

Wykres **Orders by SKU** reprezentuje trend zamówień na poziomie jednostki składowania (SKU) dla wszystkich ofert (obejmuje to zamówienia anulowane). Wykres pierścieniowy reprezentuje podział pierwszych pięciu zamówień SKU, a wykres kolumnowy reprezentuje trend zamówień dla pięciu głównych jednostek SKU.

### <a name="orders-and-seats-trend"></a>Trend zamówień i siedzeń

Wykres **trendów zamówień i siedzeń** przedstawia najpopularniejsze oferty 50 o najwyższej liczbie zamówień. Są one wyświetlane na tablicy lidera i są posortowane według najwyższej liczby porządkowej i procentowej kolejności.

- **Zamówienia według jednostek SKU**: wybierz ofertę, aby wyświetlić podział liczby zamówień dla pięciu głównych jednostek SKU na wykresie.
- **Stanowiska według jednostek SKU**: miesięczny trend siedzeń dla pięciu pierwszych jednostek SKU. Jeśli wybrana oferta nie jest ofertą na stanowisko, na tym wykresie warstwowym nie będą widoczne żadne dane.

### <a name="canceled-orders-by-offers"></a>Zamówienia anulowane według ofert

Wykres kołowy **anulowania zamówień według oferty** organizuje wszystkie anulowane zamówienia zgodnie z ich nazwami ofert. Najważniejsze oferty są wyświetlane na wykresie, a pozostałe oferty są pogrupowane jako "REST All". Możesz wybrać konkretne oferty w legendzie, aby wyświetlić je na wykresie.

- Umieszczenie kursora na wycinku na wykresie spowoduje wyświetlenie liczby zamówień i procent wybranej oferty w porównaniu do łącznej liczby zamówień we wszystkich ofertach.
- Wykres kolumnowy zawiera trendy dotyczące miesiąca. Kolumny reprezentują liczbę anulowanych zamówień według nazwy oferty. Możesz użyć suwaka na górze wykresu, aby przewinąć w prawo i w lewo wzdłuż osi x i skupić się na określonych punktach danych. Możesz wyświetlić wykres trendu, wybierając konkretny element w legendzie.

### <a name="order-details-table"></a>Tabela szczegółów zamówienia

W tabeli Order Details (szczegóły zamówienia) wyświetlana jest lista numerowana zamówień z 1000 góry posortowanych według daty nabycia.

- Każda kolumna w siatce jest do sortowania.
- Dane można wyodrębnić do pliku TSV, jeśli liczba rekordów jest mniejsza niż 1000.
- Jeśli liczba rekordów przekracza 1000, eksportowane dane będą asynchroniczne umieszczane na stronie pobierania przez następne 30 dni.
- Filtry można zastosować do **tabeli Order Details** , aby wyświetlić tylko te dane, które Cię interesują. Dane można filtrować według kraju/regionu, typu licencji platformy Azure, typu licencji Marketplace, typu oferty, stanu zamówienia, bezpłatnych identyfikatorów, identyfikatora subskrypcji witryny Marketplace, identyfikatora klienta i nazwy firmy.
- Ponieważ oferty SaaS zakupione w witrynie Azure Marketplace lub AppSource nie wymagają subskrypcji platformy Azure, Identyfikator subskrypcji portalu Marketplace zostanie wyświetlony jako 00000000-0000-0000-0000-000000000000 w sekcji **szczegółowe dane zamówień** .

#### <a name="orders-page-filters"></a>Filtry stron zamówień

Te filtry są stosowane na poziomie strony.

Możesz wybrać wiele filtrów, aby renderować wykres dla kryteriów, które chcesz wyświetlić, oraz danych, które mają być wyświetlane w **szczegółowym porządku** siatki/eksportowanie danych. Filtry są stosowane do danych wyodrębnionych dla zakresu danych wybranego w prawym górnym rogu strony Orders (zamówienia).

- Typy ofert i nazwy ofert są wyświetlane tylko dla ofert, dla których masz zamówienia w ramach wybranego zakresu dat. Nazwy ofert na liście są wyświetlane dla ofert o typach wybranych na liście.
- Zastosowane filtry przedstawiają łączną metryki w każdym zaznaczeniu dla każdego wybranego filtru. Zastosowane filtry nie są wyświetlane, gdy wybrano wybór domyślny.
- Jeśli wybrano opcję **wszystkie** dla jednej z list rozwijanych, wszystkie metryki na wybranej stronie zostaną zagregowane. Na przykład: "wszystkie" w opcji filtrowania typów ofert oznacza, że wybrano wszystkie typy ofert. Jest to domyślny wybór dla list rozwijanych. Zastosowane filtry są wyświetlane, gdy **wszystko** jest zaznaczone.
- **Wybór wielokrotny**: wszystkie metryki na stronie zostaną zagregowane dla wszystkich opcji dokonanych na liście rozwijanej. W przypadku wybrania wielu zaznaczeń filtr będzie zawierać liczbę wszystkich dokonanych wyborów. Zapoznaj się z poniższym obrazem.

    ![Centrum partnerskie — Przeanalizuj kolejność z wieloma wartościami zastosowanymi do filtrowania](./media/filters-applied.png)

- **Wybór pojedynczej wartości**: Jeśli wybrano jedną wartość, zastosowany filtr będzie zawierać liczbę wybranych filtrów. Aby uzyskać informacje, zobacz poniżej obraz.

     ![Kolejność analizowania w centrum partnerskim z pojedynczą wartością zastosowaną do filtrowania](./media/filters-applied-single.png)

## <a name="next-steps"></a>Następne kroki

- Aby zapoznać się z omówieniem raportów analitycznych dostępnych na rynku komercyjnym centrum partnerskiego, zobacz [Analiza portalu komercyjnego w centrum partnerskim](./analytics.md).
- W przypadku grafów, trendów i wartości zagregowanych danych, które podsumowują aktywność witryny Marketplace dla oferty, zobacz [pulpit nawigacyjny podsumowania w portalu komercyjnej analizy Marketplace](./summary-dashboard.md).
- W przypadku maszyn wirtualnych (VM) są dostępne metryki dotyczące użycia i taryfowego rozliczania, zobacz [pulpit nawigacyjny użycia w portalu komercyjnej analizy Marketplace](./usage-dashboard.md).
- Aby uzyskać szczegółowe informacje na temat klientów, w tym trendów wzrostu, zobacz [pulpit nawigacyjny klienta w komercyjnej analizie portalu Marketplace](./customer-dashboard.md).
- Aby uzyskać listę żądań pobierania w ciągu ostatnich 30 dni, zobacz [pulpit nawigacyjny w portalu komercyjnej analizy Marketplace](./downloads-dashboard.md).
- Aby wyświetlić skonsolidowany widok opinii klientów na temat ofert w witrynie Azure Marketplace i AppSource, zobacz [pulpit nawigacyjny oceny i przeglądów w portalu komercyjnej analizy Marketplace](./ratings-reviews.md).
- Często zadawane pytania dotyczące analizy komercyjnej portalu Marketplace i kompleksowego słownika warunków dotyczących danych można znaleźć [w temacie często zadawane pytania i terminologia dotyczące komercyjnej analizy w portalu Marketplace](./faq-terminology.md).
