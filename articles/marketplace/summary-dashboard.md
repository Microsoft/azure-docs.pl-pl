---
title: Pulpit nawigacyjny podsumowania dla analiz Centrum partnerskiego w komercyjnej witrynie Marketplace
description: Dowiedz się, jak uzyskiwać dostęp do grafów, trendów i wartości zagregowanych danych, które podsumowują aktywność witryny Marketplace z poziomu pulpitu nawigacyjnego podsumowanie w centrum partnerskim.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/09/2020
author: sayantanroy83
ms.author: sroy
ms.openlocfilehash: 6b59da408ae7c88aaf8b9e7423fe955a861edf0f
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/12/2020
ms.locfileid: "94565521"
---
# <a name="summary-dashboard-in-commercial-marketplace-analytics"></a>Pulpit nawigacyjny Podsumowanie w analizie komercyjnej platformy handlowej

Ten artykuł zawiera informacje na temat pulpitu nawigacyjnego podsumowanie w centrum partnerskim. Ten pulpit nawigacyjny wyświetla wykresy, trendy i wartości zagregowanych danych, które podsumowują aktywność witryny Marketplace dla ofert.

Aby uzyskać dostęp do pulpitu nawigacyjnego podsumowanie w centrum partnerskim, w obszarze **Marketing komercyjny** wybierz pozycję **[Analizuj](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)**  >  **Podsumowanie**.

>[!NOTE]
> Aby uzyskać szczegółowe definicje terminologii dotyczącej analizy, zobacz temat [Terminologia i często zadawane pytania dotyczące analizy Marketplace](./partner-center-portal/faq-terminology.md).

## <a name="summary-dashboard"></a>Pulpit nawigacyjny Podsumowanie

Pulpit nawigacyjny podsumowania zawiera omówienie usługi Azure Marketplace i Microsoft AppSource oferuje wydajność biznesową. Pulpit nawigacyjny zawiera szeroki przegląd następujących elementów:

- Zamówienia klientów
- Klienci
- Użycie ofert przez klientów
- Wizyty stron klientów w witrynie Azure Marketplace i AppSource

## <a name="elements-of-the-summary-dashboard"></a>Elementy pulpitu podsumowania

W poniższych sekcjach opisano, jak używać pulpitu nawigacyjnego podsumowanie i jak odczytywać dane.

### <a name="month-range"></a>Zakres miesiąca

Wybór zakresu miesiąca można znaleźć w prawym górnym rogu każdej strony. Dostosuj dane wyjściowe wykresów stron **podsumowujących** , wybierając zakres miesiąca w oparciu o ostatnie 3, 6 lub 12 miesięcy lub wybierając niestandardowy zakres miesiąca o maksymalnym okresie wynoszącym 12 miesięcy. Domyślny zakres miesiąca (okres obliczeń) to sześć miesięcy.

:::image type="content" source="./media/summary-dashboard/summary-dashboard.png" alt-text="Ilustruje opcje zakresu miesięcznego na pulpicie nawigacyjnym podsumowania.":::

> [!NOTE]
> Wszystkie metryki w elementach widget wizualizacji i Eksportowanie raportów uwzględniają okres obliczeń wybrany przez użytkownika.

### <a name="orders-widget"></a>Widżet Orders

Widżet Orders na pulpicie nawigacyjnym podsumowanie * * wyświetla bieżące zamówienia dla wszystkich ofert opartych na języku Transact. Widżet zamówienia przedstawia liczbę i trend wszystkich zakupionych zamówień (z pominięciem zamówień anulowanych) dla wybranego okresu obliczeniowego. **Zlecenia** wartości procentowych przedstawiają stopień wzrostu w wybranym okresie obliczeniowym.

[![Ilustruje widżet Orders na pulpicie nawigacyjnym podsumowania.](./media/summary-dashboard/orders-widget.png)](./media/summary-dashboard/orders-widget.png#lightbox)


Możesz również przejść do raportu zamówienia, wybierając link **pulpitu nawigacyjnego zamówienia** w lewym dolnym rogu widżetu.

### <a name="customers-widget"></a>Widżet klienci

Widżet **klienci** na pulpicie nawigacyjnym **Podsumowanie** wyświetla łączną liczbę klientów, którzy uzyskali oferty w wybranym okresie obliczeniowym. Widżet Customers wyświetla liczbę i trend łącznej liczby aktywnych (w tym nowych i istniejących) klientów (z pominięciem niezmienionych klientów) dla wybranego okresu obliczeń. Wartość procentowa w obszarze **klienci** reprezentuje stopień wzrostu w wybranym okresie obliczeniowym.

[![Ilustruje widżet Customers na pulpicie nawigacyjnym podsumowania.](./media/summary-dashboard/customers-widget.png)](./media/summary-dashboard/customers-widget.png#lightbox)

Możesz również przejść do raportu szczegółowego klientów, wybierając link **pulpitu nawigacyjnego klienci** w lewym dolnym rogu widżetu.

### <a name="usage-widget"></a>Element widget użycia

Widżet **użycie** pulpitu nawigacyjnego **Podsumowanie** przedstawia łączną liczbę zwykłych i nieprzetworzonych godzin użycia dla wszystkich ofert maszyn wirtualnych platformy Azure. Widżet użycie wyświetla liczbę i trend łącznej liczby godzin użycia dla wybranego okresu obliczeniowego.

W tabeli Podsumowanie użycia są wyświetlane godziny użycia klientów dla wszystkich zakupionych ofert.

- Znormalizowane godziny użycia są definiowane jako godziny użycia znormalizowane w celu uwzględnienia liczby rdzeni maszyn wirtualnych ([liczba rdzeni maszyn wirtualnych] x [godz. użycia pierwotnego]). Maszyny wirtualne oznaczone jako "SHAREDCORE" wykorzystują 1/6 (lub 0,1666) jako mnożnik [liczba rdzeni maszyn wirtualnych].
- Godziny użycia pierwotnego są definiowane jako czas działania maszyn wirtualnych w godzinach.

Wartość procentowa poniżej łącznej liczby godzin użytkowania reprezentuje wielkość wzrostu w godzinach użytkowania w wybranym okresie obliczeniowym.

[![Ilustruje widżet użycie na pulpicie nawigacyjnym podsumowanie.](./media/summary-dashboard/usage-widget.png)](./media/summary-dashboard/usage-widget.png#lightbox)

Możesz również przejść do raportu Użycie, wybierając link **pulpitu nawigacyjnego użycie** w lewym dolnym rogu widżetu.

### <a name="marketplace-insights"></a>Szczegółowe informacje o portalu Marketplace

Usługa Marketplace Insights pokazuje liczbę użytkowników, którzy odwiedzili strony ofert w witrynie Azure Marketplace i AppSource. **Liczba odwiedzin stron** zawiera podsumowanie informacji o komercyjnej analizie sieci Web w portalu Marketplace, dzięki której wydawcy mogą mierzyć zaangażowanie klientów na odpowiednie strony z informacjami szczegółowymi dotyczącymi produktów, które znajdują się w sklepach online komercyjnych: Microsoft AppSource i Azure Marketplace. Ten widżet przedstawia liczbę i trend łącznej liczby wizyt stron w wybranym okresie obliczeniowym.

[![Ilustruje widżet liczba odwiedzin stron na pulpicie nawigacyjnym podsumowania.](./media/summary-dashboard/page-visit-count.png)](./media/summary-dashboard/page-visit-count.png#lightbox)

Możesz również przejść do raportu usługi Marketplace Insights, wybierając link **pulpitu nawigacyjnego usługi Marketplace Insights** w lewym dolnym rogu widżetu.

### <a name="geographical-spread"></a>Geograficzne rozmieszczenie

W przypadku wybranego okresu obliczeniowego mapę cieplną wyświetla łączną liczbę klientów, zamówień i znormalizowanych godzin użycia względem wymiaru geograficznego.

:::image type="content" source="./media/summary-dashboard/geo-spread.png" alt-text="Ilustruje widżet kraje do rozmieszczenia na pulpicie nawigacyjnym podsumowania.":::

. Weź pod uwagę następujące kwestie:

- Możesz przenieść mapę, aby wyświetlić dokładną lokalizację.
- Możesz powiększyć do określonej lokalizacji.
- Mapę cieplną zawiera dodatkową siatkę, aby wyświetlić szczegółowe informacje o liczbie klientów, liczbie zamówień oraz znormalizowanych godzinach użycia dla określonej lokalizacji.
- Możesz wyszukać i wybrać kraj/region w siatce, aby powiększyć do lokalizacji na mapie. Przywróć oryginalny widok, wybierając przycisk Home ( **Strona główna** ) na mapie.

> [!TIP]
> Aby pobrać dane, możesz użyć ikony pobierania w prawym górnym rogu dowolnego elementu widget. Możesz przekazać opinię na temat każdego elementu widget, wybierając ikonę "kciuki" lub "kciuk".

## <a name="next-steps"></a>Następne kroki

- Aby zapoznać się z omówieniem raportów analizy dostępnych w portalu komercyjnym, zobacz [dostęp do raportów analitycznych dla komercyjnej witryny Marketplace w centrum partnerskim](./partner-center-portal/analytics.md).
- Aby uzyskać informacje o zamówieniach w formacie graficznym i do pobrania, zobacz temat [Zamówienie pulpitu nawigacyjnego w portalu komercyjnej analizy Marketplace](orders-dashboard.md).
- W przypadku maszyn wirtualnych (VM) są dostępne metryki dotyczące użycia i taryfowego rozliczania, zobacz [pulpit nawigacyjny użycia w portalu komercyjnej analizy Marketplace](usage-dashboard.md).
- Aby uzyskać szczegółowe informacje na temat klientów, w tym trendów wzrostu, zobacz [pulpit nawigacyjny klienta w komercyjnej analizie portalu Marketplace](customer-dashboard.md).
- Aby uzyskać listę żądań pobierania w ciągu ostatnich 30 dni, zobacz [pulpit nawigacyjny w portalu komercyjnej analizy Marketplace](./partner-center-portal/downloads-dashboard.md).
- Aby wyświetlić skonsolidowany widok opinii klientów na temat ofert w witrynie Azure Marketplace i AppSource, zobacz temat [ocena & przeglądy pulpit nawigacyjny analizy w centrum partnerskim](./partner-center-portal/ratings-reviews.md).
- Często zadawane pytania dotyczące analizy komercyjnej portalu Marketplace i kompleksowego słownika warunków dotyczących danych można znaleźć w temacie [Terminologia i często zadawane pytania dotyczące analizy Marketplace](./partner-center-portal/faq-terminology.md).
