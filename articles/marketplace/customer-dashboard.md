---
title: Pulpit nawigacyjny klientów w analizie komercyjnej witryny Microsoft Marketplace w centrum partnerskim, Azure Marketplace i Microsoft AppSource
description: Dowiedz się, jak uzyskać dostęp do informacji o klientach, w tym trendów wzrostu, przy użyciu pulpitu nawigacyjnego klienci w komercyjnej analizie portalu Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 11/09/2020
author: sayantanroy83
ms.author: sroy
ms.openlocfilehash: c98b47507a7543002f00aae82370f5ea0043510d
ms.sourcegitcommit: 19ffdad48bc4caca8f93c3b067d1cf29234fef47
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/06/2021
ms.locfileid: "97954281"
---
# <a name="customers-dashboard-in-commercial-marketplace-analytics"></a>Pulpit nawigacyjny klientów w komercyjnej analizie Marketplace

Ten artykuł zawiera informacje na temat pulpitu nawigacyjnego klienci w centrum partnerskim. Ten pulpit nawigacyjny wyświetla informacje o klientach, w tym trendy wzrostu, prezentowane w formacie graficznym i do pobrania.

Aby uzyskać dostęp do pulpitu nawigacyjnego klienci w centrum partnerskim, w obszarze **komercyjne Marketplace** wybierz pozycję **[Analizuj](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)**  >  **klientów**.

>[!NOTE]
> Aby uzyskać szczegółowe definicje terminologii dotyczącej analizy, zobacz temat [Terminologia i często zadawane pytania dotyczące analizy Marketplace](./analytics-faq.md).

## <a name="customers-dashboard"></a>Pulpit nawigacyjny Klienci

Na pulpicie nawigacyjnym klienci są wyświetlane dane dla klientów, którzy uzyskali oferty. Można wyświetlić graficzne reprezentacje następujących elementów:

- Trend aktywnych i zmienionych klientów
- Trend wzrostu klienta obejmujący istniejących, nowych i zmienionych klientów
- Klienci według zamówień i użycia
- Percentyl klienci 
- Typ klienta według zamówień i użycia
- Klienci według lokalizacji geograficznej
- Tabela szczegółów klientów
- Filtry stron klientów

> [!NOTE]
> Maksymalne opóźnienie między pozyskiwaniem klienta i raportowaniem w centrum partnerskim wynosi 48 godzin.

## <a name="elements-of-the-customers-dashboard"></a>Elementy pulpitu nawigacyjnego klienci

W poniższych sekcjach opisano, jak korzystać z pulpitu nawigacyjnego klienci i jak odczytywać dane.

### <a name="month-range"></a>Zakres miesiąca

Wybór zakresu miesiąca można znaleźć w prawym górnym rogu każdej strony. Dostosuj dane wyjściowe wykresów stron **klientów** , wybierając zakres miesiąca na podstawie ostatnich 6 lub 12 miesięcy lub wybierając niestandardowy zakres miesiąca o maksymalnym okresie wynoszącym 12 miesięcy. Domyślny zakres miesiąca (okres obliczeń) to sześć miesięcy.

:::image type="content" source="./media/customer-dashboard/month-range-filters.png" alt-text="Ilustruje filtry miesiąca na stronie Customers.":::

> [!NOTE]
> Wszystkie metryki w elementach widget wizualizacji i Eksportowanie raportów uwzględniają okres obliczeń wybrany przez użytkownika.

### <a name="active-and-churned-customers-trend"></a>Trend aktywnych i zmienionych klientów

W tej sekcji znajdziesz trend wzrostu dla klientów w wybranym okresie obliczeniowym. Tendencje i trendy wzrostu są reprezentowane przez wykres liniowy i wyświetlają wartości dla każdego miesiąca przez umieszczenie kursora nad wierszem na wykresie. Wartość procentowa poniżej **aktywnych klientów** w widżecie reprezentuje stopień wzrostu w wybranym okresie obliczeniowym. Na przykład poniższy zrzut ekranu przedstawia wzrost liczby 0,92% dla wybranego okresu obliczeń.

[![Ilustruje widżet Customers na stronie klienci.](./media/customer-dashboard/customers-widget.png)](./media/customer-dashboard/customers-widget.png#lightbox)

Istnieją trzy _typy klientów_: nowe, istniejące i zmienione.

- Nowy klient uzyskał co najmniej jedną ofertę po raz pierwszy w wybranym miesiącu.
- Istniejący klient uzyskał jedną lub większą liczbę ofert przed wybranym miesiącem.
- Zakupiony klient anulował wszystkie oferty. Niezmienione klienci są reprezentowani na osi negatywnej w widżecie trend.

### <a name="customer-growth-trend-including-existing-new-and-churned-customers"></a>Trend wzrostu klienta obejmujący istniejących, nowych i zmienionych klientów

Ta sekcja zawiera informacje o trendach i liczbie wszystkich klientów, w tym nowych, istniejących i zmienionych, z trendem wzrostu miesięcznie.

- Wykres liniowy reprezentuje ogólne wartości procentowe wzrostu klientów.
- Kolumna month reprezentuje liczbę klientów ułożonych przez nowych, istniejących i zmienionych klientów.
- Liczba zmienionych klientów jest wyświetlana w ujemnym kierunku osi X.
- Można wybrać konkretne elementy legendy, aby wyświetlić bardziej szczegółowe widoki. Na przykład wybierz pozycję nowi klienci w legendzie, aby wyświetlić tylko nowych klientów.
- Umieszczenie wskaźnika myszy nad kolumną na wykresie powoduje wyświetlenie szczegółów tylko dla tego miesiąca.

[![Ilustracja przedstawiająca element widget trendów dla klientów na stronie Customers.](./media/customer-dashboard/customers-trend.png)](./media/customer-dashboard/customers-trend.png#lightbox)

### <a name="customers-by-ordersusage"></a>Klienci według zamówień/użycia

Wykres **klienci według zamówień/użycia** ma trzy karty: zamówienia, znormalizowane użycie i pierwotne użycie. Wybierz kartę **zamówienia** , aby wyświetlić szczegóły zamówienia.

:::image type="content" source="./media/customer-dashboard/customers-by-orders-usage.png" alt-text="Przedstawia kartę Orders (zamówienia) w obszarze klienci według zamówień i użycia na stronie klienci.":::

. Weź pod uwagę następujące kwestie:

- Tablica lidera przedstawia szczegółowe informacje o klientach uporządkowane według liczby zamówień. Po wybraniu klienta szczegóły klienta są wyświetlane w sekcjach sąsiadujące "Szczegóły", "zamówienia według jednostek SKU" i "jednostki SKU według siedzeń".
- Szczegóły profilu klienta są wyświetlane w tym miejscu, gdy wydawcy są zalogowani przy użyciu roli właściciela. Jeśli wydawcy są zalogowani przy użyciu roli współautor, szczegóły w tej sekcji nie będą dostępne.
- Wykres pierścieniowy **Orders by SKU** zawiera podział zamówień zakupionych dla planów. Wyświetlane są pięć najważniejszych planów o najwyższej liczbie zamówień, podczas gdy pozostałe zamówienia są pogrupowane w obszarze **rest All (wszystkie**).
- Wykres pierścieniowy **jednostki SKU według siedzeń** przedstawia podział stanowisk zamówionych dla planów. Wyświetlane są pięć najważniejszych planów z najwyższymi stanowiskami, podczas gdy pozostałe zamówienia są pogrupowane w obszarze **rest All (wszystkie**).

Możesz również wybrać kartę **znormalizowane** użycie lub **surowe użycie** , aby wyświetlić szczegóły użycia.

- Tablica lidera przedstawia szczegółowe informacje o klientach, które są klasyfikowane według liczby godzin użycia. Po wybraniu klienta szczegóły dotyczące klienta są prezentowane w sekcji "Szczegóły", "znormalizowane użycie przez oferty" i "znormalizowane użycie według rozmiaru maszyny wirtualnej".
- Szczegóły profilu klienta są wyświetlane w tym miejscu, gdy wydawcy są zalogowani przy użyciu roli właściciela. Jeśli wydawcy są zalogowani przy użyciu roli współautor, szczegóły w tej sekcji nie będą dostępne.
- Wykres pierścieniowy z **znormalizowanym użyciem przez oferty** zawiera podział użycia wykorzystanego przez oferty. Wyświetlane są pięć najważniejszych planów o najwyższej liczbie użycia, podczas gdy pozostałe oferty są pogrupowane w obszarze **rest All (wszystkie**).
- Wykres pierścieniowy z **znormalizowanym użyciem według rozmiaru maszyny wirtualnej** przedstawia podział użycia zużyty przez różne rozmiary maszyn wirtualnych. Wyświetlane są pięć pierwszych rozmiarów maszyn wirtualnych z najwyższym znormalizowanym użyciem, podczas gdy reszta użycia jest zgrupowana w obszarze **rest All (wszystkie**).

### <a name="top-customers-percentile"></a>U największych klientów percentyl

Wykres **percentylu najważniejszych klientów** ma trzy karty, "zamówienia", "znormalizowane użycie" i "surowe użycie". _Najwyższy percentyl klienta_ jest wyświetlany wzdłuż osi x, zgodnie z określoną liczbą zamówień. Oś y Wyświetla liczbę zamówień klienta. Pomocnicza oś y (wykres liniowy) zawiera łączny procent łącznej liczby zamówień. Możesz wyświetlić szczegóły, umieszczając kursor nad punktami wzdłuż wykresu liniowego.

[![Ilustruje kartę zamówienia w widżecie najwyższego poziomu percentylu klienta na stronie klienci.](./media/customer-dashboard/top-customer-percentile.png)](./media/customer-dashboard/top-customer-percentile.png#lightbox)

### <a name="customer-type-by-orders-and-usage"></a>Typ klienta według zamówień i użycia

Na wykresie **Typ zamówienia/użycie wg klienta** wyświetlana jest liczba zamówień, znormalizowanych użycia i nieprzetworzonych godzin użycia rozdzielonych między nowym klientem a istniejącymi klientami. w oparciu o wybrane zamówienia, znormalizowane i pierwotne użycie odpowiednio na wykresie.

Na tym wykresie przedstawiono następujące elementy:

- Nowy klient uzyskał co najmniej jedną ofertę lub zgłoszone użycie po raz pierwszy w tym samym miesiącu kalendarzowym (oś y).
- Istniejący klient uzyskał wcześniej ofertę od Ciebie lub zgłoszono użycie przed zaraportowanym miesiącem kalendarzowym (na osi y).

[![Przedstawia kartę Orders widżetu order by Customer na stronie klienci.](./media/customer-dashboard/orders-by-customer-type.png)](./media/customer-dashboard/orders-by-customer-type.png#lightbox)

### <a name="customers-by-geography"></a>Klienci według lokalizacji geograficznej

W przypadku wybranego okresu obliczeniowego mapę cieplną wyświetla łączną liczbę klientów i procent nowo dodanych klientów względem wymiaru geograficznego. Jasny do ciemnego koloru mapy reprezentuje niską i wysoką wartość liczby klientów. Wybierz rekord w tabeli, aby powiększyć w danym kraju lub regionie.

[![Ilustruje kartę zamówienia w widżecie order by geography na stronie klienci.](./media/customer-dashboard/customers-by-geography.png)](./media/customer-dashboard/customers-by-geography.png#lightbox)

. Weź pod uwagę następujące kwestie:

- Możesz przenieść mapę, aby wyświetlić dokładną lokalizację.
- Możesz powiększyć do określonej lokalizacji.
- Mapę cieplną ma dodatkową siatkę, aby wyświetlić szczegółowe informacje o liczbie klientów, liczbie zamówień, znormalizowanych godzinach użycia w określonej lokalizacji.
- Możesz wyszukać i wybrać kraj/region w siatce, aby powiększyć do lokalizacji na mapie. Przywróć oryginalny widok, wybierając przycisk Home ( **Strona główna** ) na mapie.

### <a name="customer-details-table"></a>Tabela szczegółów klienta

W tabeli **szczegóły klienta** zostanie wyświetlona lista numerowanych głównych klientów 1 000 posortowanych według daty pierwszego nabycia jednej z ofert. Można rozwinąć sekcję, wybierając ikonę rozwinięcia na Wstążce szczegóły.

. Weź pod uwagę następujące kwestie:

- Informacje osobiste klienta będą dostępne tylko wtedy, gdy klient udostępnił zgodę. Te informacje można wyświetlić tylko wtedy, gdy użytkownik zalogował się przy użyciu poziomu uprawnień właściciela.
- Każda kolumna w siatce jest do sortowania.
- Dane można wyodrębnić do. Wolumin CSV lub. TSV, jeśli liczba rekordów jest mniejsza niż 1 000.
- Jeśli liczba rekordów przekracza 1 000, eksportowane dane będą asynchroniczne umieszczane na stronie pobierania przez następne 30 dni.
- Zastosuj filtry do tabeli, aby wyświetlić tylko interesujące Cię dane. Filtrowanie danych według nazwy firmy, identyfikatora klienta, identyfikatora subskrypcji w portalu Marketplace, typu licencji platformy Azure, daty pozyskania, daty utraty, adresu E-mail klienta, kraju/regionu/stanu/miejscowości/pliku zip, języka klienta itd.
- Po zakupieniu oferty przez chronionego klienta informacje w **szczegółowych danych klienta** będą maskowane (* * * * * * * * * * * *).
- Szczegóły wymiaru klienta, takie jak nazwa firmy, nazwa klienta i adres E-mail klienta, znajdują się na poziomie identyfikatora organizacji, a nie w witrynie Azure Marketplace lub na poziomie transakcji Microsoft AppSource.

_**Tabela 1: słownik terminów dotyczących danych**_

| Nazwa kolumny | Nazwa atrybutu | Definicja |
| ------------ | ------------- | ------------- |
| Identyfikator subskrypcji witryny Marketplace | Identyfikator subskrypcji witryny Marketplace | Unikatowy identyfikator skojarzony z subskrypcją platformy Azure używany przez klienta do kupowania komercyjnej oferty portalu Marketplace. W przypadku ofert infrastruktury jest to identyfikator GUID subskrypcji klienta platformy Azure. W przypadku ofert SaaS ta wartość jest wyświetlana jako zero, ponieważ zakupy SaaS nie wymagają subskrypcji platformy Azure. |
| DateAcquired | Data pozyskania | Pierwszy dzień zakupu opublikowanej oferty przez klienta. |
| DateLost | Data utraty | Ostatnia data anulowania ostatniego zakupionego oferty przez klienta. |
| Nazwa dostawcy | Nazwa dostawcy | Nazwa dostawcy występującego w relacji między firmą Microsoft a klientem. Jeśli klient jest przedsiębiorstwem do odsprzedawcy, będzie to odsprzedawca. Jeśli jest używany dostawca rozwiązań w chmurze (CSP), będzie to dostawca usług kryptograficznych. |
| Adres E-mail dostawcy | Adres E-mail dostawcy | Adres e-mail dostawcy, który ma związek między firmą Microsoft a klientem. Jeśli klient jest przedsiębiorstwem do odsprzedawcy, będzie to odsprzedawca. Jeśli jest używany dostawca rozwiązań w chmurze (CSP), będzie to dostawca usług kryptograficznych. |
| FirstName (Imię) | Imię klienta | Imię i nazwisko dostarczone przez klienta. Nazwa może różnić się od nazwy podanej w subskrypcji platformy Azure klienta. |
| LastName (Nazwisko) | Nazwisko klienta | Nazwisko dostarczone przez klienta. Nazwa może różnić się od nazwy podanej w subskrypcji platformy Azure klienta. |
| E-mail | Adres E-mail klienta | Adres e-mail dostarczony przez klienta końcowego. Adres e-mail może różnić się od adresu e-mail w subskrypcji platformy Azure klienta. |
| Nazwa firmy klienta | Nazwa firmy klienta | Nazwa firmy podana przez klienta. Nazwa może różnić się od miasta w subskrypcji platformy Azure klienta. |
| CustomerCity | Miasto klienta | Nazwa miasta podana przez klienta. Miasto może być inne niż miasto w subskrypcji platformy Azure klienta. |
| Kod pocztowy klienta | Kod pocztowy klienta | Kod pocztowy dostarczony przez klienta. Kod może być inny niż kod pocztowy podany w subskrypcji platformy Azure klienta. |
| CustomerCommunicationCulture | Język komunikacji klienta | Język preferowany przez klienta do komunikacji. |
| CustomerCountryRegion | Kraj/region klienta | Nazwa kraju/regionu podana przez klienta. Kraj/region może być inny niż kraj/region w subskrypcji platformy Azure klienta. |
| AzureLicenseType | Typ licencji platformy Azure | Typ umowy licencyjnej używanej przez klientów do kupowania platformy Azure. Znany również jako _kanał_. Możliwe wartości są następujące:<ul><li>Dostawca rozwiązań w chmurze</li><li>Przedsiębiorstwa</li><li>Przedsiębiorstwo przez odsprzedawcę</li><li>Płatność zgodnie z rzeczywistym użyciem</li></ul> |
| PromotionalCustomers | Czy kontakt promocyjny jest wybierany | Ta wartość informuje o tym, czy klient aktywnie zabrał w celu skontaktowania się z wydawcami. W tej chwili nie prezentujemy opcji klientom, dlatego w całej tablicy podano wartość "No". Po wdrożeniu tej funkcji rozpocznie się aktualizowanie. |
| CustomerState | Stan klienta | Stan miejsca zamieszkania dostarczonego przez klienta. Stan może być inny niż stan podany w subskrypcji platformy Azure klienta. |
| CommerceRootCustomer | Główny klient handlowy | Jeden identyfikator konta rozliczeniowego może być skojarzony z wieloma identyfikatorami klientów.<br>Jedna kombinacja identyfikatora konta rozliczeniowego i identyfikatora klienta może być skojarzona z wieloma komercyjnymi subskrypcjami portalu Marketplace.<br>Główny klient handlowy oznacza nazwę klienta subskrypcji. |
| Identyfikator klienta | Customer ID | Unikatowy identyfikator przypisany do klienta. Klient może mieć zero lub więcej subskrypcji portalu Azure Marketplace. |
| Identyfikator konta rozliczeniowego | Identyfikator konta rozliczeniowego | Identyfikator konta, na którym jest generowane rozliczanie. Mapuj **Identyfikator konta rozliczeniowego** na wartość **CustomerID** , aby połączyć Raport transakcji wypłaty z raportami klientów, zamówień i użycia. |
||||

### <a name="customers-page-filters"></a>Filtry stron klientów

Filtry stron klienci są stosowane na poziomie strony klientów. Można wybrać wiele filtrów, aby renderować wykres dla kryteriów wybranych do wyświetlenia, a dane, które mają być widoczne, w siatce/eksporcie "szczegółowe zamówienia danych". Filtry są stosowane w odniesieniu do danych wyodrębnionych dla zakresu miesiąca wybranego w prawym górnym rogu strony Customers.

> [!TIP]
> Aby pobrać dane, możesz użyć ikony pobierania w prawym górnym rogu dowolnego elementu widget. Możesz przekazać opinię na temat każdego widżetu, klikając ikonę "kciuki" lub "kciuk".

## <a name="next-steps"></a>Następne kroki

- Aby zapoznać się z omówieniem raportów analizy dostępnych w portalu komercyjnym, zobacz [dostęp do raportów analitycznych dla komercyjnej witryny Marketplace w centrum partnerskim](./partner-center-portal/analytics.md).
- W przypadku grafów, trendów i wartości zagregowanych danych, które podsumowują aktywność witryny Marketplace dla oferty, zobacz [pulpit nawigacyjny podsumowania w portalu komercyjnej analizy Marketplace](./summary-dashboard.md).
- Aby uzyskać informacje o zamówieniach w formacie graficznym i do pobrania, zobacz temat [Zamówienie pulpitu nawigacyjnego w portalu komercyjnej analizy Marketplace](./orders-dashboard.md).
- W przypadku maszyn wirtualnych (VM) są dostępne metryki dotyczące użycia i taryfowego rozliczania, zobacz [pulpit nawigacyjny użycia w portalu komercyjnej analizy Marketplace](./usage-dashboard.md).
- Aby uzyskać listę żądań pobierania w ciągu ostatnich 30 dni, zobacz [pulpit nawigacyjny w portalu komercyjnej analizy Marketplace](./partner-center-portal/downloads-dashboard.md).
- Aby wyświetlić skonsolidowany widok opinii klientów na temat ofert w witrynie Azure Marketplace i Microsoft AppSource, zobacz [omówienie & przeglądy pulpit nawigacyjny analizy w centrum partnerskim](./partner-center-portal/ratings-reviews.md).
- Często zadawane pytania dotyczące analizy komercyjnej portalu Marketplace i kompleksowego słownika warunków dotyczących danych można znaleźć w temacie [Terminologia i często zadawane pytania dotyczące analizy Marketplace](./analytics-faq.md).