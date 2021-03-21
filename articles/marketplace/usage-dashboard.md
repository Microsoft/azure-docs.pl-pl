---
title: Pulpit nawigacyjny użycia w komercyjnej analizie Marketplace | Portal Azure Marketplace
description: Dowiedz się, jak uzyskać dostęp do wszystkich metryk użycia i taryfowych rozliczeń dla ofert opublikowanych w portalu Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 11/09/2020
author: sayantanroy83
ms.author: sroy
ms.openlocfilehash: 765f4f358d593cd5fcc021fbf4ec2276c422c4d8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102555129"
---
# <a name="usage-dashboard-in-commercial-marketplace-analytics"></a>Pulpit nawigacyjny Użycie w analizie komercyjnej platformy handlowej

Ten artykuł zawiera informacje na temat pulpitu nawigacyjnego użycie w centrum partnerskim. Ten pulpit nawigacyjny wyświetla wszystkie maszyny wirtualne (VM), które oferuje znormalizowane użycie, surowe użycie i metryki rozliczeń w trzech oddzielnych kartach: wykorzystano maszynę wirtualną, użycie pierwotne maszyny wirtualnej i użycie zliczania opłat.

Aby uzyskać dostęp do pulpitu nawigacyjnego użycie w centrum partnerskim, w obszarze **komercyjne Marketplace** wybierz pozycję **[Analizuj](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)**  >  **użycie**.

>[!NOTE]
> Aby uzyskać szczegółowe definicje terminologii dotyczącej analizy, zobacz temat [Terminologia i często zadawane pytania dotyczące analizy Marketplace](./analytics-faq.md).

## <a name="usage-dashboard"></a>Pulpit nawigacyjny Użycie

Pulpit nawigacyjny **użycie** w menu **Analizuj** zawiera bieżące zamówienia dla wszystkich ofert oprogramowania jako usługi (SaaS). Można wyświetlić graficzne reprezentacje następujących elementów:

- Trend użycia
- Znormalizowane użycie przez oferty
- Znormalizowane użycie według innych wymiarów: rozmiaru maszyny wirtualnej, kanałów sprzedaży i typów ofert
- Użycie według położenia geograficznego
- Szczegółowa tabela użycia
- Filtry stron zamówień

> [!NOTE]
> Maksymalne opóźnienie między generowaniem i raportowaniem zdarzeń użycia w centrum partnerskim wynosi 48 godzin.

## <a name="elements-of-the-usage-dashboard"></a>Elementy pulpitu nawigacyjnego użycia

W poniższych sekcjach opisano, jak korzystać z pulpitu nawigacyjnego użycie i jak odczytywać dane.

### <a name="month-range"></a>Zakres miesiąca

Wybór zakresu miesiąca można znaleźć w prawym górnym rogu każdej strony. Dostosuj dane wyjściowe wykresów stron **użycia** , wybierając zakres miesiąca w oparciu o ostatnie 6 lub 12 miesięcy lub wybierając niestandardowy zakres miesiąca o maksymalnym okresie wynoszącym 12 miesięcy. Domyślny zakres miesiąca (okres obliczeń) to sześć miesięcy.

:::image type="content" source="./media/usage-dashboard/month-filter.png" alt-text="Ilustruje filtry miesiąca na pulpicie nawigacyjnym użycia.":::

### <a name="usage-trend"></a>Trend użycia

W tej sekcji znajdziesz łączną liczbę godzin użycia i trend dla wszystkich ofert, które są używane przez klientów w wybranym okresie obliczeniowym. Metryki i trendy wzrostu są reprezentowane przez wykres liniowy. Pokaż wartość dla każdego miesiąca, umieszczając kursor nad linią na wykresie. Wartość procentowa poniżej metryk użycia w widżecie reprezentuje wielkość wzrostu lub odrzucania w wybranym okresie obliczeniowym.

Istnieją dwie reprezentacje godzin użycia: znormalizowane użycie maszyn wirtualnych i pierwotne użycie maszyn wirtualnych.

- Znormalizowane godziny użycia są definiowane jako godziny użycia znormalizowane w celu uwzględnienia liczby rdzeni maszyn wirtualnych ([liczba rdzeni maszyn wirtualnych] x [godz. użycia pierwotnego]). Maszyny wirtualne oznaczone jako "SHAREDCORE" wykorzystują 1/6 (lub 0,1666) jako mnożnik [liczba rdzeni maszyn wirtualnych].
- Godziny użycia pierwotnego są definiowane jako czas działania maszyn wirtualnych w godzinach.

[![Ilustruje znormalizowane dane użycia i surowego użycia na pulpicie nawigacyjnym użycia.](./media/usage-dashboard/normalized-usage.png)](./media/usage-dashboard/normalized-usage.png#lightbox)

### <a name="normalized-usage-by-offers"></a>Znormalizowane użycie przez oferty

Ta sekcja zawiera informacje o całkowitej liczbie godzin użycia i trendach dla ofert opartych na użyciu w witrynie Azure Marketplace. Wykres znormalizowane użycie przez oferty jest opisany poniżej.

- **Znormalizowane użycie przez program oferuje** skumulowany wykres kolumnowy przedstawia podział znormalizowanych godzin użycia dla pięciu pierwszych ofert zgodnie z wybranym okresem obliczeniowym. Pięć najważniejszych ofert jest wyświetlanych na grafie, a reszta jest zgrupowana w kategorii **rest** .
- Skumulowany wykres kolumnowy przedstawia trend wzrostu miesiąc po miesiącu dla wybranego zakresu dat. Kolumny miesiąc przedstawiają godziny użycia z ofert o największej godzinie użycia dla danego miesiąca. Wykres liniowy przedstawia trend procentu wzrostu wykreślony na pomocniczej osi Y.
- Możesz wybrać konkretne oferty w legendzie, aby wyświetlić tylko te oferty na grafie.

:::image type="content" source="./media/usage-dashboard/normalized-usage-offers.png" alt-text="Ilustruje znormalizowane dane użycia na pulpicie nawigacyjnym użycia.":::

Możesz wybrać dowolną ofertę i maksymalnie trzy jednostki SKU tej oferty, aby wyświetlić trend użycia w ciągu miesiąca dla oferty i wybranych jednostek SKU.

:::image type="content" source="./media/usage-dashboard/normalized-usage-offers-sku.png" alt-text="Ilustruje znormalizowane oferty użycia i dane jednostki SKU na pulpicie nawigacyjnym użycia.":::

### <a name="orders-by-offers-and-skus"></a>Zamówienia według ofert i jednostek SKU

**Oferty z ofertami i** wykresem SKU przedstawiają miary i trendy wszystkich ofert. . Weź pod uwagę następujące kwestie:

- Najważniejsze oferty są wyświetlane na wykresie, a pozostałe oferty są pogrupowane jako **Pozostałe**.
- Możesz wybrać konkretne oferty w legendzie, aby wyświetlić tylko te oferty na grafie.
- Umieszczenie kursora na wycinku na wykresie przedstawia liczbę zamówień i procent tej oferty w porównaniu do łącznej liczby zamówień w ramach wszystkich ofert.
- **Trend zamówień według oferty** przedstawia trendy dotyczące wzrostu miesiąca. Kolumna month reprezentuje liczbę zamówień według nazwy oferty. Na wykresie liniowym jest wyświetlany trend procentu wzrostu wykreślony na osi z.

:::image type="content" source="./media/usage-dashboard/orders-by-offers-and-skus.png" alt-text="Ilustruje kolejność według ofert i wykresu SKU na pulpicie nawigacyjnym użycia.":::

Możesz wybrać dowolną ofertę i maksymalnie trzy jednostki SKU tej oferty, aby wyświetlić trend miesięczny dla oferty, jednostek SKU i siedzeń.

:::image type="content" source="./media/usage-dashboard/orders-by-offers-and-skus-2.png" alt-text="Ilustruje kolejność według ofert i wykresu SKU na pulpicie nawigacyjnym użycia. Jest wyświetlany trend trendu oferty, trendu jednostek SKU i siedzeń.":::

#### <a name="normalized-usage-by-other-dimensions-vm-size-sales-channels-and-offer-type"></a>Znormalizowane użycie według innych wymiarów: rozmiaru maszyny wirtualnej, kanałów sprzedaży i typu oferty

Istnieją trzy karty wymiarów: rozmiar maszyny wirtualnej, kanały sprzedaży i typ oferty. W każdym z tych wymiarów można zobaczyć metryki użycia i trend miesiąc-miesiąc.

:::image type="content" source="./media/usage-dashboard/normalized-usage-other-dimensions.png" alt-text="Ilustruje znormalizowane użycie innego wykresu wymiarów na pulpicie nawigacyjnym użycia.":::

### <a name="usage-by-geography"></a>Użycie według położenia geograficznego

W przypadku wybranego okresu obliczeniowego mapę cieplną wyświetla łączny czas użycia względem wymiaru geograficznego. Jasny do ciemnego koloru mapy reprezentuje niską i wysoką wartość liczby klientów. Wybierz rekord w tabeli, aby powiększyć w kraju/regionie.

:::image type="content" source="./media/usage-dashboard/normalized-usage-country.png" alt-text="Ilustruje znormalizowany wykres kraju użycia na pulpicie nawigacyjnym użycia.":::

. Weź pod uwagę następujące kwestie:

- Możesz przenieść mapę, aby wyświetlić dokładną lokalizację.
- Możesz powiększyć do określonej lokalizacji.
- Mapę cieplną zawiera dodatkową siatkę, aby wyświetlić szczegółowe informacje o liczbie klientów, liczbie zamówień i znormalizowanych godzinach użycia w określonej lokalizacji.
- Możesz wyszukać i wybrać kraj/region w siatce, aby powiększyć do lokalizacji na mapie. Przywróć oryginalny widok, wybierając przycisk Home ( **Strona główna** ) na mapie.

### <a name="usage-details-table"></a>Tabela szczegółów użycia

W tabeli **szczegóły użycia** zostanie wyświetlona lista numerowana pierwszych rekordów użycia 1 000 posortowanych według użycia. . Weź pod uwagę następujące kwestie:

- Każda kolumna w siatce jest do sortowania.
- Dane można wyodrębnić do. TSV lub. Plik CSV, jeśli liczba rekordów jest mniejsza niż 1 000.
- Jeśli liczba rekordów przekracza 1 000, dane eksportu będą asynchroniczne umieszczane na stronie pobierania, które będą dostępne w ciągu następnych 30 dni.
- Zastosuj filtry do **szczegółowych danych użycia** , aby wyświetlić tylko te dane, które Cię interesują. Filtrowanie danych według kraju/regionu, kanału sprzedaży, typu licencji Marketplace, typu użycia, nazwy oferty, typu oferty, bezpłatnej wersji próbnej, identyfikatora subskrypcji witryny Marketplace, identyfikatora klienta i nazwy firmy.

_**Tabela 1: słownik terminów dotyczących danych**_

| Nazwa kolumny w<br>interfejs użytkownika | Nazwa atrybutu | Definicja | Nazwa kolumny w programie programistycznym<br>Raporty dostępu |
| ------------ | ------------- | ------------- | ------------- |
| Identyfikator subskrypcji witryny Marketplace | Identyfikator subskrypcji witryny Marketplace | Unikatowy identyfikator skojarzony z subskrypcją platformy Azure używany przez klienta do kupowania komercyjnej oferty portalu Marketplace. Identyfikator był dawniej identyfikatorem GUID subskrypcji platformy Azure. | MarketplaceSubscriptionId |
| MonthStartDate | Data rozpoczęcia miesiąca | Data rozpoczęcia miesiąca reprezentuje miesiąc zakupu. | MonthStartDate |
| Typ oferty | Typ oferty | Typ komercyjnej oferty portalu Marketplace. | OfferType |
| Typ licencji platformy Azure | Typ licencji platformy Azure | Typ umowy licencyjnej używanej przez klientów do kupowania platformy Azure. Znany również jako kanał. Możliwe wartości są następujące:<ui><li>Dostawca rozwiązań w chmurze</li><li>Przedsiębiorstwa</li><li>Przedsiębiorstwo przez odsprzedawcę</li><li>Płatność zgodnie z rzeczywistym użyciem</li></ul> | AzureLicenseType |
| Typ licencji Marketplace | Typ licencji Marketplace | Metoda rozliczeń komercyjnej oferty portalu Marketplace. Możliwe wartości są następujące:<ul><li>Rozliczanie za pomocą platformy Azure</li><li>Korzystanie z własnej licencji</li><li>Bezpłatna</li><li>Firma Microsoft jako odsprzedawca</li></ul> | MarketplaceLicenseType |
| SKU | SKU | Plan skojarzony z ofertą. | SKU |
| Kraj klienta | Kraj/region klienta | Nazwa kraju/regionu podana przez klienta. Kraj/region może być inny niż kraj/region w subskrypcji platformy Azure klienta. | CustomerCountry |
| Jest jednostką SKU wersji zapoznawczej | Jest jednostką SKU wersji zapoznawczej | Wartość jest wyświetlana, jeśli jednostka SKU została oznaczona jako "wersja zapoznawcza". Wartość będzie równa "yes", jeśli jednostka SKU została odpowiednio oznaczona, a tylko subskrypcje platformy Azure autoryzowane przez użytkownika, można wdrożyć i korzystać z tego obrazu. Wartość będzie równa "No", jeśli jednostka SKU nie została zidentyfikowana jako "wersja zapoznawcza". | IsPreviewSKU |
| Typ rozliczeń jednostki SKU | Typ rozliczeń jednostki SKU | Typ rozliczeń skojarzony z poszczególnymi jednostkami SKU w ofercie. Możliwe wartości są następujące:<ul><li>Bezpłatna</li><li>Płatna</li></ul> | SKUBillingType |
| Właściwości IsInternal ustawionej | Przestarzałe | Przestarzałe | Przestarzałe |
| Rozmiar maszyny wirtualnej | Rozmiar maszyny wirtualnej | W przypadku typów ofert opartych na maszynach wirtualnych ta jednostka oznacza rozmiar maszyny wirtualnej skojarzonej z jednostką SKU oferty. | maszyny wirtualnej (VMSize |
| Nazwa wystąpienia chmury | Nazwa wystąpienia chmury | Microsoft Cloud, w którym wystąpiło wdrożenie maszyny wirtualnej. | CloudInstanceName |
| Serviceplanname | Przestarzałe | Przestarzałe (Definicja jednostki SKU) | Serviceplanname |
| Offer Name | Offer Name | Nazwa komercyjnej oferty portalu Marketplace. | OfferName |
| DeploymentMethod | Przestarzałe | Przestarzałe (definicja typu oferty) | DeploymentMethod |
| Nazwa firmy klienta | Nazwa firmy klienta | Nazwa firmy podana przez klienta. Nazwa może się różnić od nazwy w subskrypcji platformy Azure klienta. | CustomerCompanyName |
| Data wykorzystania | Data wykorzystania | Data generowania zdarzeń użycia dla zasobów opartych na użyciu. | UsageDate |
| IsMultisolution | Jest wielorozwiązań | Oznacza, czy oferta jest typu oferty wielorozwiązań. | IsMultisolution |
| Jest nowym klientem | Przestarzałe | Przestarzałe | IsNewCustomer |
| Rozmiar podstawowy | Rozmiar podstawowy | Liczba rdzeni skojarzonych z ofertą opartą na maszynie wirtualnej. | Zmiana rozmiaru |
| Typ użycia | Typ użycia | Oznacza, czy zdarzenie użycia skojarzone z ofertą jest jednym z następujących:<ul><li>Znormalizowane użycie</li><li>Niesformatowane użycie</li><li>Użycie zmierzone</li></ul> | Parametr UsageType |
| Data zakończenia okresu próbnego | Data zakończenia okresu próbnego | Data zakończenia okresu próbnego dla tego zamówienia lub zostanie ona zakończona. | TrialEndDate |
| Waluta klienta (DW) | Waluta klienta | Waluta używana przez klienta do komercyjnej transakcji portalu Marketplace. | CustomerCurrencyCC |
| Cena (DW) | Cena | Cena jednostkowa jednostki SKU pokazana w walucie klienta. | PriceCC |
| Waluta wypłaty (komputer) | Waluta wypłaty | Wydawca jest płatny za zdarzenia użycia skojarzone z zasobem w walucie skonfigurowanej przez wydawcę. | PayoutCurrencyPC |
| Szacowana cena (komputer) | Szacowana cena | Cena jednostkowa jednostki SKU w walucie skonfigurowanej przez wydawcę. | EstimatedPricePC |
| Informacje o użyciu | Informacje o użyciu | Połączony identyfikator GUID, który służy do łączenia raportu użycia (w komercyjnej analizie Marketplace) z raportem transakcji wypłaty. Odwołanie do użycia jest połączone z identyfikatorami IDZamówienia i LineItemId w raporcie transakcji wypłaty. | UsageReference |
| Jednostka użycia | Jednostka użycia | Jednostka zużycia skojarzona z jednostką SKU. | UsageUnit |
| Customer ID | Customer ID | Unikatowy identyfikator przypisany do klienta. Klient może mieć zero lub więcej subskrypcji portalu Azure Marketplace. | CustomerId |
| Identyfikator konta rozliczeniowego | Identyfikator konta rozliczeniowego | Identyfikator konta, na którym jest generowane rozliczanie. Mapuj **Identyfikator konta rozliczeniowego** na wartość **CustomerID** , aby połączyć Raport transakcji wypłaty z raportami klientów, zamówień i użycia. | BillingAccountId |
| Ilość użycia | Ilość użycia | Łączna liczba jednostek użycia zużywanych przez element zawartości wdrożonych przez klienta.<br>Jest to oparte na elemencie typu użycia. Na przykład, jeśli typ użycia jest znormalizowanym użyciem, ilość użycia jest dla znormalizowanego użycia. | UsageQuantity |
| NormalizedUsage | Znormalizowane użycie | Łączna liczba znormalizowanych jednostek użycia zużywanych przez element zawartości wdrożonych przez klienta.<br>Znormalizowane godziny użycia są definiowane jako godziny użycia znormalizowane w celu uwzględnienia liczby rdzeni maszyn wirtualnych ([liczba rdzeni maszyn wirtualnych] x [godz. użycia pierwotnego]). Maszyny wirtualne oznaczone jako "SHAREDCORE" wykorzystują 1/6 (lub 0,1666) jako mnożnik [liczba rdzeni maszyn wirtualnych]. | NormalizedUsage |
| MeteredUsage | Użycie zmierzone | Łączna liczba jednostek użycia zużywanych przez liczniki, które są skonfigurowane z ofertą wdrożoną przez klienta. | MeteredUsage |
| RawUsage | Niesformatowane użycie | Łączna liczba nieprzetworzonych jednostek użycia, które są używane przez zasób wdrożony przez klienta.<br>Surowe godziny użycia są definiowane jako czas działania maszyn wirtualnych w warunkach użytkowania. | RawUsage |
| Szacowana opłata rozszerzona (DW) | Szacowana rozszerzona opłata w walucie klienta | Oznacza opłaty związane z użyciem. Kolumna jest iloczynem wartości cena (DW) i ilość użycia. | EstimatedExtendedChargeCC |
| Szacowana opłata rozszerzona (komputer) | Szacowana opłata rozszerzona w walucie wypłaty | Oznacza opłaty związane z użyciem. Kolumna jest produktem szacowanej ceny (komputera) i ilości użycia. | EstimatedExtended ChargePC |
|||||

### <a name="usage-page-filters"></a>Filtry stron użycia

Filtry stron **użycia** są stosowane na poziomie strony Orders. Można wybrać jeden lub wiele filtrów, aby renderować wykres dla kryteriów wybranych do wyświetlania, a dane, które mają być widoczne w siatce/eksporcie danych o zamówieniach użycia. Filtry są stosowane w odniesieniu do danych wyodrębnionych dla zakresu miesiąca wybranego w prawym górnym rogu strony użycie.

Widżety i eksport raportu dotyczące użycia surowego maszyny wirtualnej są podobne do znormalizowanych użycia maszyn wirtualnych z następującymi różnicami:

- Znormalizowane godziny użycia są definiowane jako godziny użycia znormalizowane w celu uwzględnienia liczby rdzeni maszyn wirtualnych ([liczba rdzeni maszyn wirtualnych] x [godz. użycia pierwotnego]). Maszyny wirtualne oznaczone jako "SHAREDCORE" wykorzystują 1/6 (lub 0,1666) jako mnożnik [liczba rdzeni maszyn wirtualnych].
- Surowe godziny użycia są definiowane jako czas działania maszyn wirtualnych w warunkach użytkowania.

### <a name="metered-billing-usage"></a>Użycie naliczanych rozliczeń

Karta **użycie taryfowe** zawiera informacje o użyciu typów ofert, w których użycie jest mierzone przez wymiar miernika. Typ nadwyżki oferty SaaS jest obecnie prezentowany. Na karcie przedstawiono graficzną reprezentację trendów nadwyżkowych w przypadku używania rozliczeń SaaS z naliczaniem:

- **Trend nadwyżkowy według wymiaru miernika**: przedstawia miesięczną wartość trendu nadwyżkowego dla wybranego wymiaru licznika oferty. Oś X reprezentuje miesiąc, a oś Y reprezentuje ilość użycia nadwyżkowego. Jednostka pomiaru licznika niestandardowego jest również wyświetlana na osi Y.

    :::image type="content" source="./media/usage-dashboard/metered-usage.png" alt-text="Ilustruje znormalizowany wykres użycia maszyny wirtualnej na pulpicie nawigacyjnym użycia.":::

- **Trend nadwyżkowy według jednostki SKU**: reprezentuje trend ilości użycia wybranego wymiaru licznika według jednostki SKU/planów. Zostaną wyświetlone pięć najważniejszych planów z największą ilością użycia dla wybranej oferty.

- **Trend nadwyżkowy przez klientów**: tablica lidera klienta reprezentuje skumulowaną listę klientów o największej godzinie użycia i jest wyświetlana na _tablicy lidera_, w rankingu według najwyższego użycia miernika niestandardowego. Wybierz klienta na tablicy lidera, aby wyświetlić trend użycia nadwyżkowego dla wybranego wymiaru miernika.

    :::image type="content" source="./media/usage-dashboard/metered-usage-by-customers.png" alt-text="Przedstawia wykres użycia mierzony przez klientów na pulpicie nawigacyjnym użycia.":::

Jeśli masz wiele ofert, które używają niestandardowych mierników, raport Użycie naliczanych opłat zawiera informacje dotyczące użycia dla wszystkich ofert, zgodnie ze swoimi wymiarami mierników niestandardowych.

> [!TIP]
> Aby pobrać dane, możesz użyć ikony pobierania w prawym górnym rogu dowolnego elementu widget. Możesz przekazać opinię na temat każdego widżetu, klikając ikonę "kciuki" lub "kciuk".

## <a name="next-steps"></a>Następne kroki

- Aby zapoznać się z omówieniem raportów analizy dostępnych w portalu komercyjnym, zobacz [dostęp do raportów analitycznych dla komercyjnej witryny Marketplace w centrum partnerskim](./partner-center-portal/analytics.md).
- W przypadku grafów, trendów i wartości zagregowanych danych, które podsumowują aktywność witryny Marketplace dla oferty, zobacz [pulpit nawigacyjny podsumowania w portalu komercyjnej analizy Marketplace](./summary-dashboard.md).
- Aby uzyskać informacje o zamówieniach w formacie graficznym i do pobrania, zobacz temat [pulpit nawigacyjny zamówień w komercyjnej analizie Marketplace](./orders-dashboard.md)
- W przypadku maszyn wirtualnych (VM) są dostępne metryki dotyczące użycia i taryfowego rozliczania, zobacz [pulpit nawigacyjny użycia w portalu komercyjnej analizy Marketplace](usage-dashboard.md).
- Aby uzyskać listę żądań pobierania w ciągu ostatnich 30 dni, zobacz [pulpit nawigacyjny w portalu komercyjnej analizy Marketplace](./partner-center-portal/downloads-dashboard.md).
- Aby wyświetlić skonsolidowany widok opinii klientów na temat ofert w witrynie Azure Marketplace i Microsoft AppSource, zobacz [omówienie & przeglądy pulpit nawigacyjny analizy w centrum partnerskim](./partner-center-portal/ratings-reviews.md).
- Często zadawane pytania dotyczące analizy komercyjnej portalu Marketplace i kompleksowego słownika warunków dotyczących danych można znaleźć w temacie [Terminologia i często zadawane pytania dotyczące analizy Marketplace](./analytics-faq.md).
