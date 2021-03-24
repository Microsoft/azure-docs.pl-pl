---
title: Pulpit nawigacyjny Analiza platformy handlowej w analizie komercyjnej platformy handlowej
description: Uzyskaj dostęp do podsumowania analizy sieci Web w portalu Marketplace w centrum partnerskim, które pozwala mierzyć zaangażowanie klientów w Microsoft AppSource i Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 03/23/2021
author: sayantanroy83
ms.author: sroy
ms.openlocfilehash: f6ed2029ec333120e3942570c951f5da6e846c7a
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2021
ms.locfileid: "104953276"
---
# <a name="marketplace-insights-dashboard-in-commercial-marketplace-analytics"></a>Pulpit nawigacyjny Analiza platformy handlowej w analizie komercyjnej platformy handlowej

Ten artykuł zawiera informacje na temat pulpitu nawigacyjnego usługi Marketplace Insights w centrum partnerskim. Ten pulpit nawigacyjny zawiera podsumowanie komercyjnej analizy sieci Web w portalu Marketplace, które umożliwia wydawcom zmierzenie zaangażowania klientów na odpowiednie strony szczegółów produktów wymienione w sklepach online komercyjnych: Microsoft AppSource i Azure Marketplace.

Aby uzyskać dostęp do pulpitu nawigacyjnego usługi **Marketplace Insights** w centrum partnerskim, w obszarze komercyjna witryna Marketplace wybierz pozycję **[Analizuj](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)** szczegółowe dane w  >  **portalu Marketplace**.

Aby uzyskać szczegółowe definicje terminologii dotyczącej analizy, zobacz temat [Terminologia i często zadawane pytania dotyczące analizy Marketplace](./analytics-faq.md).

## <a name="marketplace-insights-dashboard"></a>Pulpit nawigacyjny Szczegółowe informacje na temat platformy handlowej

Pulpit nawigacyjny usługi Marketplace Insights zawiera omówienie wydajności firmy w witrynie Azure Marketplace i AppSource. Ten pulpit nawigacyjny zawiera szeroki przegląd następujących elementów:

- Trend odwiedzin stron
- Wywołanie trendu akcji
- Odwiedziny stron i wywoływanie akcji związanych z ofertami, domenami odwołań i identyfikatorami kampanii
- Szczegółowe informacje o portalu Marketplace według lokalizacji geograficznej
- Tabela szczegółów szczegółowych informacji o portalu Marketplace

Pulpit nawigacyjny usługi Marketplace Insights zawiera dane strumienia kliknięć, które nie powinny być skorelowane z potencjalnymi klientami wygenerowanymi w docelowym punkcie końcowym lidera.

> [!NOTE]
> Maksymalne opóźnienie między odwiedzanymi ofertami użytkowników w witrynie Azure Marketplace lub AppSource oraz raportowanie w centrum partnerskim wynosi 48 godzin.

## <a name="elements-of-the-marketplace-insights-dashboard"></a>Elementy pulpitu nawigacyjnego usługi Marketplace Insights

Pulpit nawigacyjny usługi Marketplace Insights zawiera szczegółowe informacje telemetryczne dotyczące sieci Web dla portalu Azure Marketplace i AppSource na dwóch oddzielnych kartach. W poniższych sekcjach opisano, jak korzystać z pulpitu nawigacyjnego usługi Marketplace Insights oraz jak czytać dane.

### <a name="month-range"></a>Zakres miesiąca

Wybór zakresu miesiąca można znaleźć w prawym górnym rogu każdej strony. Dostosuj dane wyjściowe wykresów stron usługi **Marketplace Insights** , wybierając zakres miesiąca w oparciu o ostatnie 6 lub 12 miesięcy lub wybierając niestandardowy zakres miesiąca o maksymalnym okresie wynoszącym 12 miesięcy. Domyślny zakres miesiąca (okres obliczeń) to sześć miesięcy.

:::image type="content" source="./media/insights-dashboard/month-filters.png" alt-text="Ilustruje filtry miesiąca na pulpicie nawigacyjnym usługi Marketplace Insights.":::

> [!NOTE]
> Wszystkie metryki w elementach widget wizualizacji i Eksportowanie raportów uwzględniają okres obliczeń wybrany przez użytkownika.

### <a name="page-visits-trends"></a>Trendy stron

Na wykresie dla **osób odwiedzających** szczegółowe informacje o portalu Marketplace jest wyświetlana liczba _wizyt stron_ i _unikatowych osób odwiedzających_ dla wybranego okresu obliczeń.

**Wizyty stron**: Ta liczba przedstawia liczbę różnych sesji użytkownika na stronie z listą ofert (Strona szczegółów produktu) dla wybranego okresu obliczeniowego. Wskaźniki procentów czerwonych i zielonych przedstawiają procent wzrostu odwiedzin strony. Wykres trendu przedstawia liczbę odwiedzin strony od początku do miesiąca.

**Unikatowi Goście**: Ta liczba reprezentuje liczbę różnych odwiedzających w wybranym okresie obliczeń dla ofert w witrynie Azure Marketplace i AppSource. Osoba odwiedzająca, która odwiedziła co najmniej jedną stronę szczegółów produktu, będzie traktowana jako jeden unikatowy użytkownik.

[![Ilustruje wykres odwiedzających na pulpicie nawigacyjnym usługi Marketplace Insights.](./media/insights-dashboard/visitors.png)](./media/insights-dashboard/visitors.png#lightbox)

### <a name="call-to-actions-trend"></a>Wywołanie trendu akcji

Ta liczba przedstawia liczbę kliknięć przycisku **akcji, które** zostały wykonane na stronie aukcji oferty (Strona szczegółów produktu). _Wywołania akcji_ są zliczane, gdy użytkownicy wybierają przyciski **Pobierz teraz**, **bezpłatna wersja próbna**, **kontakt ze mną** lub **Testuj dysk** .

[![Ilustruje wywołanie wykresu akcji na pulpicie nawigacyjnym usługi Marketplace Insights.](./media/insights-dashboard/call-to-actions-trend.png)](./media/insights-dashboard/call-to-actions-trend.png#lightbox)

### <a name="page-visits-and-call-to-actions-against-offers-referral-domains-and-campaign-ids"></a>Odwiedziny stron i wywoływanie akcji związanych z ofertami, domenami odwołań i identyfikatorami kampanii

**Domeny odwołań**: wybranie konkretnej domeny odwołania powoduje wyświetlenie miesięcznego trendu wizyt stron i wywoływanie akcji na wykresie po prawej stronie.

:::image type="content" source="./media/insights-dashboard/referral-domain.png" alt-text="Ilustruje wykres domeny odwołań na pulpicie nawigacyjnym usługi Marketplace Insights.":::

**Oferty**: Wybierz konkretną ofertę, aby zobaczyć miesięczny trend wizyt stron oraz wywołania akcji na wykresie po prawej stronie.

:::image type="content" source="./media/insights-dashboard/offer-alias.png" alt-text="Ilustruje wykres aliasu oferty na pulpicie nawigacyjnym usługi Marketplace Insights.":::

**Identyfikatory kampanii**: wybierając konkretny identyfikator kampanii, powinno być możliwe zrozumienie sukcesu kampanii. Dla każdej kampanii powinno być możliwe wyświetlenie miesięcznego trendu wizyt stron i wywoływanie akcji na wykresie po prawej stronie.

:::image type="content" source="./media/insights-dashboard/campaign.png" alt-text="Przedstawia wykres kampanii na pulpicie nawigacyjnym usługi Marketplace Insights.":::

### <a name="marketplace-insights-by-geography"></a>Szczegółowe informacje o portalu Marketplace według lokalizacji geograficznej

Dla wybranego okresu obliczeniowego mapę cieplną wyświetla liczbę odwiedzin stron, unikatowych odwiedzających i wywołań akcji (CTA). Jasne do ciemnego koloru mapy reprezentuje niską i wysoką wartość unikatowych osób odwiedzających. Wybierz rekord w tabeli, aby powiększyć w kraju/regionie.

:::image type="content" source="./media/insights-dashboard/geographical-spread.png" alt-text="Przedstawia wykres rozmieszczenia geograficznego na pulpicie nawigacyjnym usługi Marketplace Insights.":::

. Weź pod uwagę następujące kwestie:

- Możesz przenieść mapę, aby wyświetlić dokładną lokalizację.
- Możesz powiększyć do określonej lokalizacji.
- Mapę cieplną zawiera dodatkową siatkę, aby wyświetlić szczegółowe informacje o liczbie klientów, liczbie zamówień i znormalizowanych godzinach użycia w określonej lokalizacji.
- Możesz wyszukać i wybrać kraj/region w siatce, aby powiększyć do lokalizacji na mapie. Przywróć oryginalny widok, wybierając przycisk Home ( **Strona główna** ) na mapie.

### <a name="marketplace-insights-details-table"></a>Tabela szczegółów szczegółowych informacji o portalu Marketplace

Ta tabela zawiera widok listy wizyt stron oraz wywołania akcji wybranych stron, posortowane według daty.

- Dane można wyodrębnić do. TSV lub. Plik CSV, jeśli liczba rekordów jest mniejsza niż 1 000.
- Jeśli liczba rekordów przekracza 1 000, eksportowane dane będą asynchroniczne umieszczane na stronie pobierania przez następne 30 dni.
- Filtrowanie danych według nazw ofert i nazw kampanii, aby wyświetlić dane, które Cię interesują.

> [!TIP]
> Aby pobrać dane, możesz użyć ikony pobierania w prawym górnym rogu dowolnego elementu widget. Możesz przekazać opinię na temat każdego widżetu, klikając ikonę "kciuki" lub "kciuk".

| Nazwa kolumny w<br>interfejs użytkownika | Nazwa atrybutu | Definicja | Nazwa kolumny w programie programistycznym<br>Raporty dostępu |
| ------------ | ------------- | ------------- | ------------- |
| Date (Data) | Data wizyty | Data odwiedzania strony i/lub CTA kliknij pozycję generowanie zdarzeń na stronie oferty w witrynie Azure Marketplace i/lub AppSource. | Date (Data) |
| Offer Name | Offer Name | Nazwa komercyjnej oferty portalu Marketplace. | OfferName |
| Domena odwołania | Domena odwołania | Nazwa domeny referencyjnej, w której wystąpiła odwiedzanie strony. Jeśli nie ma żadnych domen odwołań przechwyconych podczas odwiedzania strony, odpowiadający wpis to "odwołanie do domeny nie istnieje". |  ReferralDomain |
| Nazwa kraju | Nazwa kraju | Nazwa kraju, w którym wystąpiła odwiedzanie strony. | CountryName |
| Wizyty stron | Wizyty stron | Liczba wizyt stron skojarzonych z nazwą oferty dla określonej daty. | PageVisits |
| Pobierz teraz | Pobierz teraz | Liczba kliknięć od początku do "Pobierz teraz" na stronie oferty z określoną datą. | GetItNow |
| Skontaktuj się z nami | Skontaktuj się z nami | Liczba kliknięć od strony "kontakt ze mną" na stronie oferty dla konkretnej daty. | ContactMe |
| Wersja testowa | Wersja testowa | Liczba kliknięć na stronie oferty jako "Test Drive" w określonej dacie. | TestDrive |
| Bezpłatna wersja próbna | Bezpłatna wersja próbna | Liczba kliknięć do "bezpłatnej wersji próbnej" na stronie oferty dla konkretnej daty. | FreeTrial |
| Kampania | Nazwa kampanii | Możliwość zrozumienia informacji telemetrycznych sieci Web (odwiedziny stron i CTA) w odniesieniu do nazwy kampanii. | Kampania |
| n/d | Witryna | Nazwa witryny sieci Web, z której odwiedzasz stronę lub kliknij pozycję CTA. Możliwe wartości są następujące:<br><ul><li>AZUREMARKETPLACE<li>APPSOURCE</ul> | Witryna |
|

## <a name="next-steps"></a>Następne kroki

- Aby zapoznać się z omówieniem raportów analizy dostępnych w portalu komercyjnym, zobacz [dostęp do raportów analitycznych dla komercyjnej witryny Marketplace w centrum partnerskim](./partner-center-portal/analytics.md).
- Aby uzyskać informacje o zamówieniach w formacie graficznym i do pobrania, zobacz temat [Zamówienie pulpitu nawigacyjnego w portalu komercyjnej analizy Marketplace](./orders-dashboard.md).
- W przypadku maszyn wirtualnych (VM) są dostępne metryki dotyczące użycia i taryfowego rozliczania, zobacz [pulpit nawigacyjny użycia w portalu komercyjnej analizy Marketplace](./usage-dashboard.md).
- Aby uzyskać szczegółowe informacje na temat klientów, w tym trendów wzrostu, zobacz [pulpit nawigacyjny klienta w komercyjnej analizie portalu Marketplace](./customer-dashboard.md).
- Aby uzyskać listę żądań pobierania w ciągu ostatnich 30 dni, zobacz [pulpit nawigacyjny w portalu komercyjnej analizy Marketplace](./partner-center-portal/downloads-dashboard.md).
- Aby wyświetlić skonsolidowany widok opinii klientów na temat ofert w witrynie Azure Marketplace i AppSource, zobacz temat [ocena & przeglądy pulpit nawigacyjny analizy w centrum partnerskim](./partner-center-portal/ratings-reviews.md).
- Często zadawane pytania dotyczące analizy komercyjnej portalu Marketplace i kompleksowego słownika warunków dotyczących danych można znaleźć w temacie [Terminologia i często zadawane pytania dotyczące analizy Marketplace](./analytics-faq.md).
