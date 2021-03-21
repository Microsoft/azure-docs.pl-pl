---
title: Pulpit nawigacyjny zamówień Centrum partnerskiego w komercyjnej analizie Marketplace | Microsoft AppSource i Azure Marketplace
description: Dowiedz się, w jaki sposób uzyskiwać dostęp do raportów analitycznych zamówień komercyjnych portalu Marketplace w formacie graficznym i do pobrania.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 11/09/2020
author: sayantanroy83
ms.author: sroy
ms.openlocfilehash: ed12e470f3f3d8c1035c1e4e2e0fa7a3b33e2369
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102561385"
---
# <a name="orders-dashboard-in-commercial-marketplace-analytics"></a>Pulpit nawigacyjny Zamówienia w analizie komercyjnej platformy handlowej

Ten artykuł zawiera informacje na temat pulpitu nawigacyjnego zamówienia w centrum partnerskim. Ten pulpit nawigacyjny wyświetla informacje o zamówieniach, w tym trendy wzrostu, prezentowane w formacie graficznym i do pobrania.

Aby uzyskać dostęp do pulpitu nawigacyjnego zamówienia w centrum partnerskim, w obszarze **komercyjne Marketplace** wybierz pozycję **[Analizuj](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)**  >  **zamówienia**.

>[!NOTE]
> Aby uzyskać szczegółowe definicje terminologii dotyczącej analizy, zobacz temat [Terminologia i często zadawane pytania dotyczące analizy Marketplace](./analytics-faq.md).

## <a name="orders-dashboard"></a>Pulpit nawigacyjny Zamówienia

Na pulpicie nawigacyjnym zamówienia są wyświetlane bieżące zamówienia dla wszystkich ofert oprogramowania jako usługi (SaaS). Można wyświetlić graficzne reprezentacje następujących elementów:

- Trend zamówień
- Trend zamówień na stanowisko i lokację
- Zamówienia według ofert i jednostek SKU
- Zamówienia według położenia geograficznego
- Szczegółowa tabela zamówień
- Filtry stron zamówień

> [!NOTE]
> Maksymalne opóźnienie między pozyskiwaniem klienta i raportowaniem w centrum partnerskim wynosi 48 godzin.

## <a name="elements-of-the-orders-dashboard"></a>Elementy pulpitu nawigacyjnego Orders

W poniższych sekcjach opisano sposób używania pulpitu nawigacyjnego zamówienia i sposobu odczytywania danych.

### <a name="month-range"></a>Zakres miesiąca

Wybór zakresu miesiąca można znaleźć w prawym górnym rogu każdej strony. Dostosuj dane wyjściowe wykresów stron **zamówień** , wybierając zakres miesiąca na podstawie ostatnich 6 lub 12 miesięcy lub wybierając niestandardowy zakres miesiąca o maksymalnym okresie wynoszącym 12 miesięcy. Domyślny zakres miesiąca (okres obliczeń) to sześć miesięcy.

:::image type="content" source="./media/orders-dashboard/month-filters.png" alt-text="Ilustruje filtry miesiąca na pulpicie nawigacyjnym zamówień.":::

> [!NOTE]
> Wszystkie metryki w elementach widget wizualizacji i Eksportowanie raportów uwzględniają okres obliczeń wybrany przez użytkownika.

### <a name="orders-trend"></a>Trend zamówień

W tej sekcji znajduje się wykres **Orders** , który pokazuje trend aktywnych i anulowanych zamówień w wybranym okresie obliczeniowym. Tendencje i trendy wzrostu są reprezentowane przez wykres liniowy i wyświetlają wartość dla każdego miesiąca przez umieszczenie kursora nad wierszem na wykresie. Wartość procentowa poniżej metryk zamówień w widżecie reprezentuje wielkość wzrostu lub odrzucania w wybranym okresie obliczeniowym.

Istnieją dwa liczniki zamówień: _aktywne_ i _anulowane_.

- **Aktywny** jest równa liczbie zamówień, które są obecnie używane przez klientów w wybranym zakresie dat.
- **Anulowane** jest równe liczbie zamówień, które zostały wcześniej zakupione, a następnie anulowane w wybranym zakresie dat.

[![Ilustruje widżet Orders na pulpicie nawigacyjnym Orders, który pokazuje trend aktywnych i anulowanych zamówień.](./media/orders-dashboard/orders-trend.png)](./media/orders-dashboard/orders-trend.png#lightbox)

### <a name="orders-by-per-seat-and-site-trend"></a>Zamówienie według trendu według stanowiska i witryny

Wykresy liniowe **według poszczególnych stanowisk i opartych na witrynie** przedstawiają metrykę i trend SaaS zamówień dla poszczególnych witryn i SaaS zamówionych przez klientów (ten wykres zawiera anulowane zamówienia).

[![Ilustruje widżet Orders na pulpicie nawigacyjnym Orders, który pokazuje zamówienia na stanowisko i tendencję lokacji.](./media/orders-dashboard/seats-sites.png)](./media/orders-dashboard/seats-sites.png#lightbox)

Oferty SaaS mogą korzystać z jednego z dwóch modeli cenowych w każdym planie: stawka ryczałtowa (oparta na lokacjach) lub na użytkownika (oparta na miejscu).

- Stała **stawka**: umożliwia dostęp do oferty przy użyciu jednej ceny miesięcznej lub rocznej stawki ryczałtowej. Jest to czasami określane jako Cennik oparty na witrynie.
- **Na użytkownika**: umożliwia dostęp do oferty przy użyciu ceny na podstawie liczby użytkowników, którzy mają dostęp do oferty lub zajmują stanowiska. Korzystając z tego modelu opartego na użyciu, można ustawić minimalną i maksymalną liczbę użytkowników obsługiwaną przez plan. Można utworzyć wiele planów w celu skonfigurowania różnych punktów cenowych na podstawie liczby użytkowników. Te pola są opcjonalne. Jeśli pole pozostanie puste, liczba użytkowników zostanie zinterpretowana jako nie mającej limitu (minimum od 1 do maksimum, tak jak usługa może obsługiwać). Te pola można edytować w ramach aktualizacji planu.
- Opłaty **taryfowe**: według stawki za stawkę ryczałtową. Korzystając z tego modelu cen, można opcjonalnie zdefiniować plany taryfowe używające interfejsu API usługi pomiarowej Marketplace do naliczania opłat klientom za użycie, które nie są objęte stałą stawką.

Aby uzyskać więcej informacji na temat rozliczeń opartych na stanowisku, witrynie i naliczaniu, zobacz artykuł [jak zaplanować ofertę SaaS dla komercyjnej witryny Marketplace](plan-saas-offer.md).

### <a name="orders-by-offers-and-skus"></a>Zamówienia według ofert i jednostek SKU

Oferty z ofertami i wykresem SKU przedstawiają miary i trendy wszystkich ofert:

- Najważniejsze oferty są wyświetlane na wykresie, a pozostałe oferty są pogrupowane jako **Pozostałe**.
- Możesz wybrać konkretne oferty w legendzie, aby wyświetlić tylko te oferty i skojarzone jednostki SKU na wykresie.
- Umieszczenie kursora na wycinku na wykresie przedstawia liczbę zamówień i procent tej oferty w porównaniu do łącznej liczby zamówień w ramach wszystkich ofert.
- **Trend zamówień według oferty** przedstawia trendy dotyczące wzrostu miesiąca. Kolumna month reprezentuje liczbę zamówień według nazwy oferty. Na wykresie liniowym jest wyświetlany trend procentu wzrostu wykreślony na osi z.

:::image type="content" source="./media/orders-dashboard/orders-by-offers.png" alt-text="Ilustruje kolejność według oferty na pulpicie nawigacyjnym zamówień.":::

Możesz wybrać dowolną ofertę i maksymalnie trzy jednostki SKU tej oferty, aby wyświetlić trend miesięczny dla oferty, jednostek SKU i siedzeń.

### <a name="orders-by-geography"></a>Zamówienia według położenia geograficznego

Dla wybranego okresu obliczeniowego mapę cieplną wyświetla łączną liczbę zamówień i procent wzrostu dla nowo dodanych zamówień do lokalizacji geograficznej.  Jasny do ciemnego koloru mapy reprezentuje niską i wysoką wartość liczby klientów. Wybierz rekord w tabeli, aby powiększyć w określonym kraju lub regionie.

[![Przedstawia wykres rozmieszczenia geograficznego na pulpicie nawigacyjnym Orders.](./media/orders-dashboard/geographical-spread.png)](./media/orders-dashboard/geographical-spread.png#lightbox)

. Weź pod uwagę następujące kwestie:

- Możesz przenieść mapę, aby wyświetlić dokładną lokalizację.
- Możesz powiększyć do określonej lokalizacji.
- Mapę cieplną zawiera dodatkową siatkę, aby wyświetlić szczegółowe informacje o liczbie klientów, liczbie zamówień oraz znormalizowanych godzinach użycia dla określonej lokalizacji.
- Możesz wyszukać i wybrać kraj/region w siatce, aby powiększyć do lokalizacji na mapie. Przywróć oryginalny widok, wybierając przycisk Home ( **Strona główna** ) na mapie.

### <a name="orders-details-table"></a>Tabela szczegółów zamówień

W tabeli Order Details (szczegóły zamówienia) wyświetlana jest lista numerowana zamówień z 1 000 góry posortowanych według daty nabycia.

- Każda kolumna w siatce jest do sortowania.
- Dane można wyodrębnić do. Wolumin CSV lub. TSV, jeśli liczba rekordów jest mniejsza niż 1 000.
- Jeśli liczba rekordów przekracza 1 000, eksportowane dane będą asynchroniczne umieszczane na stronie pobierania przez następne 30 dni.
- Zastosuj filtry do tabeli **Order Details** , aby wyświetlić tylko te dane, które Cię interesują. Filtruj według kraju/regionu, typu licencji platformy Azure, typu licencji komercyjnej witryny Marketplace, typu oferty, stanu zamówienia, bezpłatnych identyfikatorów, subskrypcji portalu Marketplace, identyfikatora klienta i nazwy firmy.
- Gdy zamówienie jest kupowane przez chronionego klienta, informacje w obszarze **szczegółowe dane zamówień** są maskowane (* * * * * * * * * * * *).

***Tabela 1: słownik terminów dotyczących danych***

| Nazwa kolumny w<br>interfejs użytkownika | Nazwa atrybutu | Definicja | Nazwa kolumny w programie programistycznym<br>Raporty dostępu |
| ------------ | ------------- | ------------- | ------------- |
| Identyfikator subskrypcji witryny Marketplace | Identyfikator subskrypcji witryny Marketplace | Unikatowy identyfikator skojarzony z subskrypcją platformy Azure używany przez klienta do kupowania komercyjnej oferty portalu Marketplace. W przypadku ofert infrastruktury jest to identyfikator GUID subskrypcji klienta platformy Azure. W przypadku ofert SaaS ta wartość jest wyświetlana jako zero, ponieważ zakupy SaaS nie wymagają subskrypcji platformy Azure. | Identyfikator subskrypcji witryny Marketplace |
| MonthStartDate | Data rozpoczęcia miesiąca | Data rozpoczęcia miesiąca reprezentuje miesiąc zakupu. Format to rrrr-mm-dd. | MonthStartDate |
| Typ oferty | Typ oferty | Typ komercyjnej oferty portalu Marketplace. | OfferType |
| Typ licencji platformy Azure | Typ licencji platformy Azure | Typ umowy licencyjnej używanej przez klientów do kupowania platformy Azure. Znany również jako kanał. Możliwe wartości są następujące:<ul><li>Dostawca rozwiązań w chmurze</li><li>Przedsiębiorstwa</li><li>Przedsiębiorstwo przez odsprzedawcę</li><li>Płatność zgodnie z rzeczywistym użyciem</li></ul> | AzureLicenseType |
| Typ licencji Marketplace | Typ licencji Marketplace | Metoda rozliczeń komercyjnej oferty portalu Marketplace. Różne wartości to:<ul><li>Rozliczanie za pomocą platformy Azure</li><li>Korzystanie z własnej licencji</li><li>Bezpłatna</li><li>Firma Microsoft jako odsprzedawca</li></ul> | MarketplaceLicenseType |
| SKU | SKU | Plan skojarzony z ofertą | SKU |
| Kraj klienta | Kraj/region klienta | Nazwa kraju/regionu podana przez klienta. Kraj/region może być inny niż kraj/region w subskrypcji platformy Azure klienta. | CustomerCountry |
| Jest jednostką SKU wersji zapoznawczej | Jest jednostką SKU wersji zapoznawczej | Wartość będzie informować o tym, czy jest oznaczona jako wersja zapoznawcza. Wartość będzie równa "yes", jeśli jednostka SKU została odpowiednio oznaczona, a tylko subskrypcje platformy Azure autoryzowane przez użytkownika, można wdrożyć i korzystać z tego obrazu. Wartość będzie równa "No", jeśli jednostka SKU nie została zidentyfikowana jako "wersja zapoznawcza". | IsPreviewSKU |
| Identyfikator zamówienia | Identyfikator zamówienia | Unikatowy identyfikator zamówienia klienta dla komercyjnej usługi portalu Marketplace. Oferty oparte na użyciu maszyny wirtualnej nie są skojarzone z kolejnością. | OrderId (Identyfikator zamówienia) |
| Zamówiona ilość | Zamówiona ilość | Liczba zasobów skojarzonych z IDENTYFIKATORem zamówienia dla aktywnych zamówień | OrderQuantity (Zamówiona ilość) |
| Nazwa wystąpienia chmury | Nazwa wystąpienia chmury | Microsoft Cloud, w którym wystąpiło wdrożenie maszyny wirtualnej. | CloudInstanceName |
| Jest nowym klientem | Jest nowym klientem | Wartość określa, czy nowy klient uzyskał co najmniej jedną ofertę po raz pierwszy. Wartość będzie równa "yes", jeśli w ramach tego samego miesiąca kalendarzowego dla "Data pozyskania". Wartość będzie równa "No", jeśli klient kupił dowolne oferty przed zgłoszonym miesiącem kalendarzowym. | IsNewCustomer |
| Stan zamówienia | Stan zamówienia | Stan komercyjnego zamówienia witryny Marketplace w momencie ostatniego odświeżenia danych. | OrderStatus |
| Data anulowania zamówienia | Data anulowania zamówienia | Data anulowania komercyjnego zamówienia witryny Marketplace. | OrderCancelDate |
| Nazwa firmy klienta | Nazwa firmy klienta | Nazwa firmy podana przez klienta. Nazwa może różnić się od miasta w subskrypcji platformy Azure klienta. | CustomerCompanyName |
| Zamówienie daty zakupu | Zamówienie daty zakupu | Data utworzenia komercyjnego zamówienia witryny Marketplace. Format to rrrr-mm-dd. | OrderPurchaseDate |
| Offer Name | Offer Name | Nazwa komercyjnej oferty portalu Marketplace. | OfferName |
| Data zakończenia okresu próbnego | Data zakończenia okresu próbnego | Data zakończenia okresu próbnego dla tego zamówienia lub zostanie ona zakończona. | TrialEndDate |
| Customer ID | Customer ID | Unikatowy identyfikator przypisany do klienta. Klient może mieć zero lub więcej subskrypcji portalu Azure Marketplace. | CustomerId |
| Identyfikator konta rozliczeniowego | Identyfikator konta rozliczeniowego | Identyfikator konta, na którym jest generowane rozliczanie. Mapuj **Identyfikator konta rozliczeniowego** na wartość **CustomerID** , aby połączyć Raport transakcji wypłaty z raportami klientów, zamówień i użycia. | BillingAccountId |
| AssetCount | Liczba zasobów | Liczba zasobów skojarzonych z IDENTYFIKATORem zamówienia. | Przestarzałe |
|||||

### <a name="orders-page-filters"></a>Filtry stron zamówień

Filtry strony **Orders** są stosowane na poziomie strony Orders. Można wybrać jeden lub wiele filtrów, aby renderować wykres dla kryteriów wybranych do wyświetlania, a dane, które mają być widoczne w siatce/eksporcie szczegółowych zamówień danych. Filtry są stosowane do danych wyodrębnionych dla zakresu miesiąca wybranego w prawym górnym rogu strony Orders (zamówienia).

> [!TIP]
> Aby pobrać dane, możesz użyć ikony pobierania w prawym górnym rogu dowolnego elementu widget. Możesz przekazać opinię na temat każdego widżetu, klikając ikonę "kciuki" lub "kciuk".

## <a name="next-steps"></a>Następne kroki

- Aby zapoznać się z omówieniem raportów analizy dostępnych w portalu komercyjnym, zobacz [dostęp do raportów analitycznych dla komercyjnej witryny Marketplace w centrum partnerskim](./partner-center-portal/analytics.md).
- W przypadku grafów, trendów i wartości zagregowanych danych, które podsumowują aktywność witryny Marketplace dla oferty, zobacz [pulpit nawigacyjny podsumowania w portalu komercyjnej analizy Marketplace](./summary-dashboard.md).
- Aby uzyskać informacje o zamówieniach w formacie graficznym i do pobrania, zobacz temat [Zamówienie pulpitu nawigacyjnego w portalu komercyjnej analizy Marketplace](orders-dashboard.md).
- W przypadku maszyn wirtualnych (VM) są dostępne metryki dotyczące użycia i taryfowego rozliczania, zobacz [pulpit nawigacyjny użycia w portalu komercyjnej analizy Marketplace](./usage-dashboard.md).
- Aby uzyskać listę żądań pobierania w ciągu ostatnich 30 dni, zobacz [pulpit nawigacyjny w portalu komercyjnej analizy Marketplace](./partner-center-portal/downloads-dashboard.md).
- Aby wyświetlić skonsolidowany widok opinii klientów na temat ofert w witrynie Azure Marketplace i AppSource, zobacz temat [ocena & przeglądy pulpit nawigacyjny analizy w centrum partnerskim](./partner-center-portal/ratings-reviews.md).
- Często zadawane pytania dotyczące analizy komercyjnej portalu Marketplace i kompleksowego słownika warunków dotyczących danych można znaleźć w temacie [Terminologia i często zadawane pytania dotyczące analizy Marketplace](./analytics-faq.md).