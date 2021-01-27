---
title: Scenariusze automatyzacji dotyczące rozliczeń platformy Azure i zarządzania kosztami
description: Dowiedz się, jak typowe scenariusze rozliczeń i zarządzania kosztami są mapowane na różne interfejsy API.
author: bandersmsft
ms.reviewer: adwise
tags: billing
ms.service: cost-management-billing
ms.subservice: common
ms.topic: reference
ms.date: 01/26/2021
ms.author: banders
ms.openlocfilehash: 40eb6bbb952596e015be7c1ed29dbefb3fb6016d
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98897717"
---
# <a name="automation-scenarios-for-billing-and-cost-management"></a>Scenariusze automatyzacji dotyczące rozliczeń i zarządzania kosztami

W tym artykule wymieniono typowe scenariusze dotyczące rozliczeń i zarządzania kosztami na platformie Azure. Scenariusze te są mapowane na dostępne interfejsy API. W ramach każdego mapowania scenariusza na interfejs API można znaleźć podsumowanie interfejsów API i udostępnianych przez nich funkcji.

## <a name="common-scenarios"></a>Typowe scenariusze

Interfejsów API rozliczeń i zarządzania kosztami można używać w kilku scenariuszach, aby uzyskać odpowiedzi na pytania dotyczące kosztów i użycia. Oto zarys typowych scenariuszy:

- **Uzgadnianie faktur**: Czy opłata naliczona przez firmę Microsoft jest właściwa?  Jaki jest mój rachunek i czy mogę go obliczyć samodzielnie?

- **Opłaty łączone**: Znam już wysokość opłaty. Kto w mojej organizacji powinien zapłacić?

- **Optymalizacja kosztów**: Znam wysokość naliczonej opłaty. Jak zoptymalizować wydatki na platformę Azure?

- **Śledzenie kosztów**: Chcę sprawdzić, ile wydaję na platformę Azure i jak wygląda jej użycie w czasie. Jakie są trendy? Jak lepiej korzystać z zasobów?

- **Wydatki na platformę Azure w miesiącu**: Jaka jest aktualna liczba wydatków bieżącego miesiąca? Czy należy wprowadzić jakieś zmiany dotyczące wydatków i/lub używania platformy Azure? W jakie dni miesiąca użycie platformy Azure jest największe?

- **Alerty**: Jak skonfigurować użycie oparte na zasobach lub alerty pieniężne?

## <a name="scenario-to-api-mapping"></a>Mapowanie scenariuszy na interfejsy API

|         Interfejs API        | Uzgadnianie faktur    | Opłaty łączone    | Optymalizacja kosztów    | Śledzenie kosztów    | Wydatki w środku miesiąca    | Alerty    |
|:---------------------------:|:-------------------------:|:----------------:|:--------------------:|:----------------:|:------------------:|:---------:|
| Budżety                     |                           |                  |           X          |                  |                    |     X     |
| Opłaty za korzystanie z witryny Marketplace                |             X             |         X        |           X          |         X        |          X         |     X     |
| Arkusz cen                 |             X             |         X        |           X          |         X        |          X         |           |
| Rekomendacje dotyczące rezerwacji |                           |                  |           X          |                  |                    |           |
| Szczegóły rezerwacji         |                           |                  |           X          |         X        |                    |           |
| Podsumowania rezerwacji       |                           |                  |           X          |         X        |                    |           |
| Szczegóły użycia               |             X             |         X        |           X          |         X        |          X         |     X     |
| Okresy rozliczeniowe             |             X             |         X        |           X          |         X        |                    |           |
| Faktury                    |             X             |         X        |           X          |         X        |                    |           |
| RateCard                    |             X             |                  |           X          |         X        |          X         |           |
| Nieklasyfikowane użycie               |             X             |                  |           X          |                  |          X         |           |

> [!NOTE]
> Mapowanie scenariuszy na interfejsy API nie obejmuje interfejsów API użycia w przypadku subskrypcji Enterprise. Jeśli to możliwe, w nowych scenariuszach programistycznych używaj ogólnych interfejsów API użycia.

## <a name="api-summaries"></a>Podsumowania interfejsów API

### <a name="consumption"></a>Zużycie
Klienci korzystający z subskrypcji Web Direct i Enterprise mogą używać następujących interfejsów API, o ile nie zaznaczono inaczej:

-    [Interfejs API budżetów](/rest/api/consumption/budgets) (*tylko klienci korporacyjni*): umożliwia tworzenie budżetów kosztów lub budżetów użycia dla zasobów, grup zasobów bądź mierników rozliczeń. Po utworzeniu budżetów można skonfigurować alerty umożliwiające powiadamianie o przekroczeniu zdefiniowanych progów budżetów. Można również skonfigurować akcje, które mają być wykonywane po osiągnięciu kwoty budżetu.

-    [Interfejs API opłat za korzystanie z witryny Marketplace](/rest/api/consumption/marketplaces): umożliwia uzyskanie danych opłat i danych użycia dla wszystkich zasobów witryny Azure Marketplace (oferty partnerów platformy Azure). Danych tych można użyć do sumowania kosztów wszystkich zasobów witryny Marketplace lub analizowania kosztów/użycia określonych zasobów.

-    [Interfejs API arkusza cen](/rest/api/consumption/pricesheet) (*tylko klienci korporacyjni*): umożliwia pobieranie niestandardowego cennika dla wszystkich mierników. Przedsiębiorstwa mogą łączyć te dane z danymi użycia oraz informacjami o użyciu witryny Marketplace w celu obliczania kosztów.

-    [Interfejs API zaleceń dotyczących rezerwacji](/rest/api/consumption/reservationrecommendations): umożliwia uzyskanie zaleceń dotyczących zakupu wystąpień usługi Reserved VM Instances. Zalecenia ułatwiają analizowanie przewidywanych oszczędności i kwot zakupów. Aby uzyskać więcej informacji, zobacz [Interfejsy API na potrzeby automatyzacji rezerwacji platformy Azure](../reservations/reservation-apis.md).

-    [Interfejs API szczegółów rezerwacji](/rest/api/consumption/reservationsdetails): umożliwia uzyskanie informacji dotyczących zakupionych rezerwacji maszyn wirtualnych, takich jak bieżące zużycie na tle zarezerwowanego zużycia. Dane można wyświetlić na poziomie poszczególnych maszyn wirtualnych. Aby uzyskać więcej informacji, zobacz [Interfejsy API na potrzeby automatyzacji rezerwacji platformy Azure](../reservations/reservation-apis.md).

-    [Interfejs API podsumowań rezerwacji](/rest/api/consumption/reservationssummaries): umożliwia uzyskanie zagregowanych informacji dotyczących rezerwacji maszyn wirtualnych zakupionych przez organizację, takich jak bieżące zużycie na tle zarezerwowanego zużycia w agregacji. Aby uzyskać więcej informacji, zobacz [Interfejsy API na potrzeby automatyzacji rezerwacji platformy Azure](../reservations/reservation-apis.md).

-    [Interfejs API szczegółów użycia](/rest/api/consumption/usagedetails): umożliwia uzyskanie informacji o opłatach i użyciu dla wszystkich zasobów platformy Azure udostępnianych przez firmę Microsoft. Informacje te mają postać szczegółowych rekordów i aktualnie są udostępniane raz dziennie dla jednego miernika. Informacji tych można użyć do sumowania kosztów wszystkich zasobów lub analizowania kosztów/użycia określonych zasobów.

-    [Interfejs API usługi RateCard](/previous-versions/azure/reference/mt219005(v=azure.100)): klienci korzystający z subskrypcji Web Direct mogą uzyskiwać stawki mierników. Zwrócone informacje można połączyć z informacjami o użyciu zasobów w celu ręcznego obliczenia kwoty rachunku.

-    [Interfejs API nieklasyfikowanego użycia](/previous-versions/azure/reference/mt219003(v=azure.100)): umożliwia pobieranie nieprzetworzonych informacji o użyciu przed rozpoczęciem pomiarów/naliczeniem opłat przez platformę Azure.

### <a name="billing"></a>Rozliczenia
-    [Interfejs API okresów rozliczeniowych](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods): umożliwia określanie okresu rozliczeniowego do analizy i uzyskanie identyfikatorów faktur dla tego okresu. Identyfikatorów faktur można używać razem z interfejsem API faktur.

-    [Interfejs API faktur](/rest/api/billing/2019-10-01-preview/invoices): umożliwia uzyskanie adresu URL, za pomocą którego można pobrać fakturę w postaci pliku PDF za dany okres rozliczeniowy.

### <a name="enterprise-consumption"></a>Użycie w ramach subskrypcji Enterprise
Następujące interfejsy API są dostępne tylko w przypadku korzystania z subskrypcji Enterprise:

-    [Interfejs API podsumowania salda](/rest/api/billing/enterprise/billing-enterprise-api-balance-summary): umożliwia uzyskanie comiesięcznego podsumowania informacji dotyczących sald, nowych zakupów, opłat za usługę Azure Marketplace, korekt i opłat za użycie nadwyżkowe. Informacje te mogą dotyczyć bieżącego okresu rozliczeniowego lub dowolnego okresu w przeszłości. Przedsiębiorstwa mogą porównać te dane z ręcznie obliczonymi podsumowaniami opłat. Ten interfejs API nie udostępnia informacji dotyczących określonych zasobów ani zagregowanego widoku kosztów.

-    [Interfejs API szczegółów użycia](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail): umożliwia uzyskanie informacji o użyciu platformy Azure (dla ofert firmy Microsoft) w bieżącym miesiącu, wskazanym okresie rozliczeniowym lub okresie niestandardowym. Za pomocą tych danych przedsiębiorstwa mogą ręcznie obliczyć rachunki na podstawie stawki i zużycia. Przedsiębiorstwa mogą również użyć informacji dla poszczególnych działów/organizacji w celu podzielenia kosztów na organizacje. Dane te zapewniają wgląd w użycie/koszty z podziałem na zasoby.

-    [Interfejs API opłat za sklep Marketplace](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge): umożliwia uzyskanie informacji o użyciu platformy Azure (dla ofert partnerów) w bieżącym miesiącu, wskazanym okresie rozliczeniowym lub okresie niestandardowym. Za pomocą tych danych przedsiębiorstwa mogą ręcznie obliczyć rachunki na podstawie stawki i zużycia. Przedsiębiorstwa mogą również użyć informacji dla poszczególnych działów/organizacji w celu podzielenia kosztów na organizacje. Ten interfejs API zapewnia wgląd w użycie/koszty z podziałem na zasoby.

-    [Interfejs API arkusza cen](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet): umożliwia uzyskanie odpowiedniej stawki za każdy miernik dla danej rejestracji i okresu rozliczeniowego. Informacje o stawce można łączyć z danymi użycia oraz informacjami o użyciu witryny Marketplace w celu ręcznego obliczenia kwoty rachunku.

-    [Interfejs API okresów rozliczeniowych](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods): umożliwia uzyskanie listy okresów rozliczeniowych. Ten interfejs API udostępnia również właściwość wskazującą trasę interfejsu API dla czterech zestawów danych interfejsu API Enterprise dotyczących okresu rozliczeniowego: BalanceSummary, UsageDetails, Marketplace Charges i PriceSheet.

-    [Interfejs API zaleceń dotyczących wystąpień zarezerwowanych](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation): udostępnia informacje o użyciu maszyn wirtualnych w okresie 7, 30 lub 60 dni i uzyskanie zaleceń dotyczących pojedynczych i współdzielonych zakupów. Za pomocą tego interfejsu API można analizować przewidywane oszczędności i zalecane kwoty zakupów. Aby uzyskać więcej informacji, zobacz [Interfejsy API na potrzeby automatyzacji rezerwacji platformy Azure](../reservations/reservation-apis.md).

## <a name="frequently-asked-questions"></a>Często zadawane pytania

### <a name="whats-the-difference-between-the-enterprise-reporting-apis-and-the-consumption-apis-when-should-i-use-each"></a>Czym różnią się interfejsy API raportowania korporacyjnego od interfejsów API użycia? Kiedy używa się tych interfejsów?
Te interfejsy API mają podobny zestaw funkcji i umożliwiają odpowiadanie na wiele tych samych pytań w obszarze rozliczeń i zarządzania kosztami. Są one jednak przeznaczone dla różnych użytkowników:

- Interfejsy API raportowania korporacyjnego są dostępne dla klientów, którzy podpisali umowę Enterprise Agreement z firmą Microsoft i mogą korzystać z negocjowanych opłat z góry za platformę Azure (wcześniej nazywanych zobowiązaniem pieniężnym) i niestandardowych cen. Interfejsy API wymagają klucza, który można uzyskać w witrynie [Enterprise Portal](https://ea.azure.com). Opis tych interfejsów API można znaleźć w temacie [Przegląd interfejsów API raportowania dla klientów korporacyjnych](enterprise-api.md).

- Interfejsy API użycia są dostępne dla wszystkich klientów z kilkoma wyjątkami. Aby uzyskać więcej informacji, zobacz [Przegląd interfejsu API użycia platformy Azure](consumption-api-overview.md) i [Dokumentacja interfejsu API użycia platformy Azure](/rest/api/consumption/). W najnowszych scenariuszach programistycznych zalecamy używanie udostępnionych interfejsów API.

### <a name="whats-the-difference-between-the-usage-details-api-and-the-usage-api"></a>Czym różni się interfejs API szczegółów użycia od interfejsu API użycia?
Te interfejsy API udostępniają zasadniczo różne dane:

- [Interfejs API szczegółów użycia](/rest/api/consumption/usagedetails) udostępnia informacje o użyciu i kosztach platformy Azure dotyczące wystąpienia miernika. Udostępnione dane zostały już przetworzone przez system zliczania kosztów na platformie Azure, a koszty — wraz z ewentualnymi innymi zmianami — zostały zastosowane:

   - Zmiany na koncie dotyczące korzystania z opłat z góry za platformę Azure
   - Zmiany na koncie dotyczące rozbieżności użycia wykrytych przez platformę Azure

- [Interfejs API użycia](/previous-versions/azure/reference/mt219003(v=azure.100)) udostępnia nieprzetworzone informacje o użyciu platformy Azure przed ich przekazaniem do systemu zliczania kosztów na platformie Azure. Dane te nie muszą być skorelowane z danymi użycia lub kwotą opłaty, które są generowane przez system pomiaru opłat platformy Azure.

### <a name="whats-the-difference-between-the-invoice-api-and-the-usage-details-api"></a>Czym różni się interfejs API faktur od interfejsu API szczegółów użycia?
Te interfejsy API udostępniają różne widoki tych samych danych:

- [Interfejs API faktur](/rest/api/billing/2019-10-01-preview/invoices) jest przeznaczony tylko dla klientów z subskrypcją Web Direct. Udostępnia on comiesięczne zestawienie na rachunku oparte na zagregowanych opłatach dla każdego typu miernika.

- [Interfejs API szczegółów użycia](/rest/api/consumption/usagedetails) udostępnia szczegółowy widok rejestru użycia/kosztów dla każdego dnia. Mogą z niego korzystać zarówno klienci z subskrypcją Enterprise, jak i subskrypcją Web Direct.

### <a name="whats-the-difference-between-the-price-sheet-api-and-the-ratecard-api"></a>Czym różni się interfejs API arkusza cen od interfejsu API usługi RateCard?
Te interfejsy API udostępniają podobne zestawy danych, ale są przeznaczone dla różnych użytkowników:

- [Interfejs API arkusza cen](/rest/api/consumption/pricesheet) udostępnia niestandardowy cennik, wynegocjowany dla klienta z subskrypcją Enterprise.

- [Interfejs API usługi RateCard](/previous-versions/azure/reference/mt219005(v=azure.100)) udostępnia publiczny cennik, przeznaczony dla klientów z subskrypcją Web Direct.

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać informacje o korzystaniu z interfejsów API REST pobieranie cen dla wszystkich usług platformy Azure, zobacz [ceny detaliczne platformy Azure — omówienie](/rest/api/cost-management/retail-prices/azure-retail-prices).

- Aby porównać fakturę z zawartością pliku szczegółowego dziennego użycia i raportami zarządzania kosztami w witrynie Azure Portal, zobacz [Opis zawartości rachunku za korzystanie z platformy Microsoft Azure](../understand/review-individual-bill.md).

- Jeśli masz pytania lub potrzebujesz pomocy, [utwórz wniosek o pomoc techniczną](https://go.microsoft.com/fwlink/?linkid=2083458).