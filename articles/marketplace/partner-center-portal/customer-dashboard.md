---
title: Pulpit nawigacyjny klienta w analizie komercyjnej witryny Microsoft Marketplace w centrum partnerskim, Azure Marketplace i Microsoft AppSource
description: Dowiedz się, jak uzyskać dostęp do informacji o klientach, w tym trendów wzrostu, przy użyciu pulpitu nawigacyjnego klienta w komercyjnej analizie portalu Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 07/22/2020
author: shganesh-dev
ms.author: shganesh
ms.openlocfilehash: 44e992bb1445e1e58f42ec150a8e2c0682eea98b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87317590"
---
# <a name="customer-dashboard-in-commercial-marketplace-analytics"></a>Pulpit nawigacyjny Klient w analizie komercyjnej platformy handlowej

Ten artykuł zawiera informacje o **pulpicie nawigacyjnym klienta** w centrum partnerskim. Ten pulpit nawigacyjny wyświetla informacje o klientach, w tym trendy wzrostu, prezentowane w formacie graficznym i do pobrania.

Aby uzyskać dostęp do **pulpitu nawigacyjnego klienta**, Otwórz pulpit nawigacyjny **[Analizuj](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)** w obszarze komercyjne witryny Marketplace.

>[!NOTE]
> Aby uzyskać szczegółowe definicje terminologii dotyczącej analizy, zobacz [często zadawane pytania i terminologię dotyczącą komercyjnej analizy portalu Marketplace](./faq-terminology.md).

## <a name="customer-dashboard"></a>Pulpit nawigacyjny Klient

**Pulpit nawigacyjny klienta** menu **Analizuj** zawiera dane dla klientów, którzy uzyskali oferty. Można wyświetlić graficzne reprezentacje następujących elementów:

- [Podsumowanie klienta](#customer-summary)
- [Klient według położenia geograficznego](#customer-by-geography)
- [Trendy klientów](#customer-trends)
- [Klienci według zamówień i użycia](#customers-by-orders-and-usage)
- [Klienci według planów](#customers-by-plans)
- [Zamówienia i użycie według typu klienta](#orders-and-usage-by-customer-type)
- [Tabela szczegółów klienta](#customer-details-table)
- [Filtry stron klienta](#customer-page-filters)

Maksymalne opóźnienie między pozyskiwaniem klienta i raportowaniem w centrum partnerskim wynosi 48 godzin.

### <a name="customer-summary"></a>Podsumowanie klienta

W sekcji Podsumowanie klienta wyświetlana jest liczba wszystkich klientów, w tym nowych, istniejących i zmienionych, w ramach wybranego zakresu dat.

- Łączni klienci są zdefiniowani jako liczba wszystkich klientów, którzy kupili ofertę i mają co najmniej jedno zamówienie, które nie zostało anulowane.
- Procent wzrostu sprzedaży w porównaniu z poprzednim miesiącem jest wskazywany przez liczbę i wskaźnik w górę w kolorze czerwonym lub w dół na czerwono.
- Trendy wzrostu są reprezentowane przez wykresy słupkowe i wyświetlają wartości dla każdego miesiąca przez umieszczenie kursora nad kolumnami wykresu.

Istnieją trzy **typy klientów**: nowe, istniejące i zmienione.

- Nowy klient uzyskał co najmniej jedną ofertę po raz pierwszy w wybranym miesiącu.
- Istniejący klient uzyskał jedną lub większą liczbę ofert przed wybranym miesiącem.
- Zakupiony klient anulował wszystkie oferty.

### <a name="customer-by-geography"></a>Klient według położenia geograficznego

Wykres **Klient według geografii** pokazuje liczbę wszystkich klientów i klientów nabytych w wybranym zakresie dat, które są mapowane na podstawie kraju/regionu klienta. Jasny do ciemnego koloru mapy reprezentuje niską i wysoką wartość liczby klientów. Wybierz rekord w tabeli, aby powiększyć w kraju/regionie.

Mapa cieplna wyświetla liczbę klientów i procent według kraju/regionu klienta. Możesz przenieść mapę, aby wyświetlić dokładną lokalizację i powiększyć do określonej lokalizacji. Ta mapa zawiera dodatkową siatkę, która umożliwia wyświetlanie% odbiorców według lokalizacji, a także klientów nowo dodanych do tej lokalizacji.

### <a name="customer-trends"></a>Trendy klientów

Na wykresie **trendów dla klientów** jest wyświetlana liczba wszystkich klientów, w tym nowych, istniejących i zmienionych trendów wzrostu miesięcznie.

- Wykres liniowy reprezentuje ogólne wartości procentowe wzrostu klientów.
- Kolumna month reprezentuje liczbę klientów ułożonych przez nowych, istniejących i zmienionych klientów.
- Liczba zmienionych klientów jest wyświetlana w ujemnym kierunku osi Y.
- Można wybrać konkretne elementy legendy, aby wyświetlić bardziej szczegółowe widoki. Na przykład wybierz pozycję nowi klienci w legendzie, aby wyświetlić tylko nowych klientów.
- Możesz użyć suwaka w górnej części wykresu, aby przewinąć w prawo i w lewo na osi x i skupić się na określonych punktach danych, aby wyświetlić bardziej szczegółowe informacje.
- Umieszczenie wskaźnika myszy nad kolumną wykresu spowoduje wyświetlenie szczegółów tylko dla tego miesiąca.

### <a name="customers-by-orders-and-usage"></a>Klienci według zamówień i użycia

Wykres **klienci według zamówień/użycia** ma trzy karty, "zamówienia", "znormalizowane użycie" i "surowe użycie". **Najwyższy percentyl klienta** jest wyświetlany wzdłuż osi x, zgodnie z ich liczbą zamówień. Oś y Wyświetla liczbę zamówień klienta. Pomocnicza oś y (wykres liniowy) zawiera łączny procent łącznej liczby zamówień. Możesz wyświetlić szczegóły, umieszczając kursor nad punktami wzdłuż wykresu liniowego.

Przykładowo zapoznaj się z wykresem poniżej w celu znormalizowania użycia: pierwsze 30 percentylu klientów ma udział w 87% normalnego użycia. 30 percentyl klientów ma udział tylko w 1 570 000 godzinach użytkowania.

### <a name="customers-by-plans"></a>Klienci według planów

Poniżej opisano wykresy **klienci według planów/wykresów użycia** .

1. Tablica lidera przedstawia szczegółowe informacje o pierwszych 50 klientach uporządkowanych według liczby zamówień. Po wybraniu klienta szczegóły klienta są prezentowane w sekcjach 2, 3 i 4 tej tablicy lidera.
2. Szczegóły profilu klienta są wyświetlane w tym miejscu, gdy wydawcy są zalogowani przy użyciu roli właściciela. Jeśli wydawcy są zalogowani przy użyciu roli współautor, szczegóły w tej sekcji nie będą dostępne.
3. Wykres pierścieniowy **order by Plans** zawiera podział zamówień zakupionych dla planów. Wyświetlane są pięć najważniejszych planów o najwyższej liczbie zamówień, podczas gdy pozostałe zamówienia są pogrupowane w obszarze "REST All".
4. Wykres pierścieniowy **według planów** zawiera podział stanowisk zamówionych dla planów. Wyświetlane są pięć najważniejszych planów z najwyższymi stanowiskami, podczas gdy pozostałe zamówienia są pogrupowane w obszarze REST All (wszystkie).

### <a name="orders-and-usage-by-customer-type"></a>Zamówienia i użycie według typu klienta

Na wykresie **Typ zamówienia/użycie wg klienta** wyświetlana jest liczba zamówień, znormalizowanych użycia i nieprzetworzonych godzin użycia rozdzielonych między nowym klientem a istniejącymi klientami. w oparciu o wybrane zamówienia, znormalizowane i pierwotne użycie odpowiednio na wykresie.

- Nowy klient uzyskał co najmniej jedną ofertę lub zgłoszone użycie po raz pierwszy w tym samym miesiącu kalendarzowym (oś y).
- Istniejący klient uzyskał wcześniej ofertę od Ciebie lub zgłoszono użycie przed zaraportowanym miesiącem kalendarzowym (na osi y).

### <a name="customer-details-table"></a>Tabela szczegółów klienta

W tabeli **szczegóły klienta** zostanie wyświetlona lista numerowanych głównych klientów 1000 posortowanych według daty pierwszego nabycia jednej z ofert.

- Informacje osobiste klienta będą dostępne tylko wtedy, gdy klient udostępnił zgodę. Te informacje można wyświetlić tylko wtedy, gdy użytkownik zalogował się przy użyciu poziomu uprawnień właściciela. Dowiedz się więcej o rolach i uprawnieniach użytkowników.
- Każda kolumna w siatce jest do sortowania.
- Dane można wyodrębnić do pliku TSV, jeśli liczba rekordów jest mniejsza niż 1000.
- Jeśli liczba rekordów przekracza 1000, eksportowane dane będą asynchroniczne umieszczane na stronie pobierania przez następne 30 dni.
- Zastosuj filtry do tabeli, aby wyświetlić tylko interesujące Cię dane. Filtrowanie danych według nazwy firmy, identyfikatora klienta, identyfikatora subskrypcji w portalu Marketplace, typu licencji platformy Azure, daty pozyskania, daty utraty, adresu E-mail klienta, kraju/regionu/stanu/miejscowości/pliku zip, języka klienta itd.
- Po zakupieniu oferty przez chronionego klienta informacje w **szczegółowych danych klienta** będą maskowane (* * * * * * * * * * * *).
- Szczegóły wymiaru klienta, takie jak nazwa firmy, nazwa klienta i adres E-mail klienta, znajdują się na poziomie identyfikatora organizacji, a nie w witrynie Azure Marketplace lub na poziomie transakcji AppSource.

### <a name="customer-page-filters"></a>Filtry stron klienta

Filtry **stron klienci** są stosowane na poziomie strony klientów. Można wybrać wiele filtrów, aby renderować wykres dla kryteriów wybranych do wyświetlania, a dane, które mają być widoczne w siatce/eksporcie szczegółowych zamówień danych. Filtry są stosowane do danych wyodrębnionych dla zakresu danych wybranego w prawym górnym rogu strony Orders (zamówienia).

>[!NOTE]
> Szczegółowe definicje poszczególnych pól w siatce klienta, filtrach stron i ich możliwych wyborach znajdują się w artykule często zadawane pytania wymienione poniżej.

## <a name="next-steps"></a>Następne kroki

- Aby zapoznać się z omówieniem raportów analitycznych dostępnych na rynku komercyjnym centrum partnerskiego, zobacz [Analiza portalu komercyjnego w centrum partnerskim](./analytics.md).
- W przypadku grafów, trendów i wartości zagregowanych danych, które podsumowują aktywność witryny Marketplace dla oferty, zobacz [pulpit nawigacyjny podsumowania w portalu komercyjnej analizy Marketplace](./summary-dashboard.md).
- Aby uzyskać informacje o zamówieniach w formacie graficznym i do pobrania, zobacz temat [Zamówienie pulpitu nawigacyjnego w portalu komercyjnej analizy Marketplace](./orders-dashboard.md).
- W przypadku maszyn wirtualnych (VM) są dostępne metryki dotyczące użycia i taryfowego rozliczania, zobacz [pulpit nawigacyjny użycia w portalu komercyjnej analizy Marketplace](./usage-dashboard.md).
- Aby uzyskać listę żądań pobierania w ciągu ostatnich 30 dni, zobacz [pulpit nawigacyjny w portalu komercyjnej analizy Marketplace](./downloads-dashboard.md).
- Aby wyświetlić skonsolidowany widok opinii klientów na temat ofert w witrynie Azure Marketplace i AppSource, zobacz [pulpit nawigacyjny oceny i przeglądów w portalu komercyjnej analizy Marketplace](./ratings-reviews.md).
- Często zadawane pytania dotyczące analizy komercyjnej portalu Marketplace i kompleksowego słownika warunków dotyczących danych można znaleźć [w temacie często zadawane pytania i terminologia dotyczące komercyjnej analizy w portalu Marketplace](./faq-terminology.md).
