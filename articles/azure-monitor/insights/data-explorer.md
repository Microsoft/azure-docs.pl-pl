---
title: Azure Monitor for Azure Data Explorer (wersja zapoznawcza)| Microsoft Docs
description: W tym artykule opisano Azure Monitor Insights dla klastrów Azure Data Explorer klastrach.
services: azure-monitor
ms.topic: conceptual
ms.date: 01/05/2021
author: lgayhardt
ms.author: lagayhar
ms.openlocfilehash: ac147df90d3fa0c27bb4299c57ec79d9a9031710
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107737602"
---
# <a name="azure-monitor-for-azure-data-explorer-preview"></a>Azure Monitor for Azure Data Explorer (wersja zapoznawcza)

Azure Monitor for Azure Data Explorer (wersja zapoznawcza) zapewnia kompleksowe monitorowanie klastrów, zapewniając ujednolicony widok wydajności, operacji, użycia i awarii klastra.
Ten artykuł pomoże Ci zrozumieć, jak dołączać i używać Azure Monitor na Azure Data Explorer (wersja zapoznawcza).

## <a name="introduction-to-azure-monitor-for-azure-data-explorer-preview"></a>Wprowadzenie do Azure Monitor for Azure Data Explorer (wersja zapoznawcza)

Przed rozpoczęciem pracy należy zrozumieć, jak prezentuje i wizualizuje informacje.
-    **W perspektywie skalowania** przedstawiający widok migawki podstawowych metryk klastrów, aby łatwo śledzić wydajność zapytań, pozyskiwania i operacji eksportowania.
-   **Przechodzenie do szczegółów** określonego klastra Azure Data Explorer, co pomaga w przeprowadzaniu szczegółowej analizy.
-    **Dostosowywalne** miejsce, w którym można zmieniać metryki, które mają być dostępne, modyfikować lub ustawiać progi zgodne z limitami oraz zapisywać własne skoroszyty niestandardowe. Wykresy w skoroszycie można przypiąć do pulpitów nawigacyjnych platformy Azure.

## <a name="view-from-azure-monitor-at-scale-perspective"></a>Wyświetl z Azure Monitor (z perspektywy skali)

Z Azure Monitor można wyświetlić główne metryki wydajności klastra, w tym metryki dotyczące zapytań, pozyskiwania i operacji eksportowania z wielu klastrów w ramach subskrypcji, a także ułatwić identyfikowanie problemów z wydajnością.

Aby wyświetlić wydajność klastrów we wszystkich subskrypcjach, wykonaj następujące kroki:

1. Zaloguj się do [Azure Portal](https://portal.azure.com/)

2. Wybierz **pozycję Monitoruj** w okienku po lewej stronie w Azure Portal, a następnie w sekcji Centrum szczegółowych informacji wybierz pozycję **Azure Data Explorer Klastry (wersja zapoznawcza)**.

![Zrzut ekranu przedstawiający przegląd funkcji z wieloma grafami](./media/data-explorer/insights-hub.png)

### <a name="overview-tab"></a>Karta Przegląd

Na karcie **Przegląd** dla wybranej subskrypcji w tabeli są wyświetlane interaktywne metryki Azure Data Explorer klastrów pogrupowanych w ramach subskrypcji. Wyniki można filtrować na podstawie opcji wybranych z następujących list rozwijanych:

* Subskrypcje — wyświetlane są tylko subskrypcje, Azure Data Explorer klastry.

* Azure Data Explorer klastrach — domyślnie jest wstępnie wybranych tylko pięć klastrów. W przypadku wybrania wszystkich lub wielu klastrów w selektorze zakresu zostanie zwróconych maksymalnie 200 klastrów.

* Zakres czasu — domyślnie wyświetla informacje z ostatnich 24 godzin na podstawie wybranych opcji.

Kafelek licznika na liście rozwijanej zawiera łączną liczbę klastrów Azure Data Explorer w wybranych subskrypcjach i odzwierciedla liczbę wybranych klastrów. Kolumny mają warunkowe kodowanie kolorami: Podtrzymanie aktywności, Użycie procesora CPU, Wykorzystanie pozyskiwania i Wykorzystanie pamięci podręcznej. Komórki kodowane pomarańczowo mają wartości, które nie są zrównoważone dla klastra. 

Aby lepiej zrozumieć, co reprezentują poszczególne metryki, zalecamy przeczytanie dokumentacji dotyczącej Azure Data Explorer [metryk.](/azure/data-explorer/using-metrics#cluster-metrics)

### <a name="query-performance-tab"></a>Karta Wydajność zapytań

Na tej karcie przedstawiono czas trwania zapytania, łączną liczbę równoczesnych zapytań i łączną liczbę zapytań z ograniczeniami.

![Zrzut ekranu przedstawiający kartę wydajności zapytań](./media/data-explorer/query-performance.png)

### <a name="ingestion-performance-tab"></a>Karta Wydajność pozyskiwania

Na tej karcie przedstawiono opóźnienie pozyskiwania, wyniki pozyskiwania, wyniki nieudanego pozyskiwania, wolumin pozyskiwania i zdarzenia przetworzone dla usługi Event/IoT Hubs.

[![Zrzut ekranu przedstawiający kartę wydajności pozyskiwania](./media/data-explorer/ingestion-performance.png)](./media/data-explorer/ingestion-performance.png#lightbox)

### <a name="streaming-ingest-performance-tab"></a>Karta Wydajność pozysłania przesyłania strumieniowego

Ta karta zawiera informacje na temat średniej szybkości danych, średniego czasu trwania i szybkości żądania.

### <a name="export-performance-tab"></a>Karta Eksportowanie wydajności

Ta karta zawiera informacje dotyczące wyeksportowanych rekordów, opóźnień, liczby oczekujących i procentu użycia dla operacji eksportu ciągłego.

## <a name="view-from-an-azure-data-explorer-cluster-resource-drill-down-analysis"></a>Wyświetlanie z zasobu Azure Data Explorer klastra (analiza przechodzenia do szczegółów)

Aby uzyskać Azure Monitor klastrów Azure Data Explorer klastra bezpośrednio z klastra Azure Data Explorer klastra:

1. W Azure Portal wybierz pozycję **Azure Data Explorer Klastry.**

2. Z listy wybierz klaster Azure Data Explorer klastra. W sekcji monitorowanie wybierz pozycję **Szczegółowe informacje (wersja zapoznawcza).**

Te widoki są również dostępne po wybraniu nazwy zasobu klastra Azure Data Explorer w widoku Azure Monitor szczegółowych informacji.

Azure Monitor dla Azure Data Explorer łączy zarówno dzienniki, jak i metryki w celu zapewnienia globalnego rozwiązania do monitorowania. Włączenie wizualizacji opartych na dziennikach wymaga od użytkowników włączenia rejestrowania diagnostycznego klastra Azure Data Explorer i wysłania ich do [obszaru roboczego usługi Log Analytics.](/azure/data-explorer/using-diagnostic-logs?tabs=commands-and-queries#enable-diagnostic-logs). Dzienniki diagnostyczne, które powinny być włączone, to: **Command**, **Query**, **TableDetails** i **TableUsageStatistics.**

![Zrzut ekranu przedstawiający niebieski przycisk z tekstem "Włącz dzienniki do monitorowania"](./media/data-explorer/enable-logs.png)


 Karta **Przegląd** zawiera:

- Kafelki metryk z wyróżnieniami dostępności i ogólnym stanem klastra, aby szybko ocenić jego kondycję.

- Podsumowanie aktywnych rekomendacji [usługi Advisor i](/azure/data-explorer/azure-advisor) stanu [kondycji](/azure/data-explorer/monitor-with-resource-health) zasobu.

- Wykresy przedstawiające użytkowników procesora CPU i pamięci oraz liczbę unikatowych użytkowników w czasie.


[![Zrzut ekranu przedstawiający widok z zasobu Azure Data Explorer klastra](./media/data-explorer/overview.png)](./media/data-explorer/overview.png#lightbox)

Karta **Kluczowe metryki** zawiera ujednolicony widok niektórych metryk klastra pogrupowanych według: metryk ogólnych, powiązanych z zapytaniami, związanych z pozyskiwaniem i pozyskiwaniem strumieniowym.

[![Zrzut ekranu przedstawiający widok błędów](./media/data-explorer/key-metrics.png)](./media/data-explorer/key-metrics.png#lightbox)

Karta **Użycie** pozwala użytkownikom zagłębić się w wydajność poleceń i zapytań klastra. Na tej stronie można:
 
 - Sprawdź, które grupy obciążeń, użytkownicy i aplikacje wysyłają najwięcej zapytań lub zużywają najwięcej zasobów procesora i pamięci (aby zrozumieć, które obciążenia wysyłają najcięższe zapytania do przetwarzania przez klaster).
 - Identyfikowanie grup obciążeń, użytkowników i aplikacji według zapytań, które zakończyły się niepowodzeniem.
 - Zidentyfikuj ostatnie zmiany liczby zapytań w porównaniu do historycznej średniej dziennej (w ciągu ostatnich 16 dni) według grupy obciążeń, użytkowników i aplikacji.
 - Zidentyfikuj trendy i wartości szczytowe liczby zapytań, pamięci i użycia procesora CPU według grupy obciążenia, użytkownika, aplikacji i typu polecenia.

[![Zrzut ekranu przedstawiający widok operacji z wykresami pierścieniowymi przedstawiającymi najważniejsze aplikacje według liczby poleceń i zapytań, najważniejsze podmioty zabezpieczeń według liczby poleceń i zapytań oraz najważniejsze polecenia według typów poleceń](./media/data-explorer/usage.png)](./media/data-explorer/usage.png#lightbox)

[![Zrzut ekranu przedstawiający widok operacji z wykresami liniowymi liczby zapytań według aplikacji, całkowitej ilości pamięci według aplikacji i całkowitego użycia procesora CPU według aplikacji](./media/data-explorer/usage-2.png)](./media/data-explorer/usage-2.png#lightbox)

Karta **tabele** zawiera najnowsze i historyczne właściwości tabel w klastrze. Możesz zobaczyć, które tabele zużywają najwięcej miejsca, śledzić historię wzrostu według rozmiaru tabeli, gorących danych i liczby wierszy w czasie.

Karta **pamięci podręcznej** umożliwia użytkownikom analizowanie wzorców wyszukiwania w rzeczywistych zapytaniach i porównywanie ich ze skonfigurowanymi zasadami pamięci podręcznej (dla każdej tabeli). Możesz zidentyfikować tabele używane przez większość zapytań i tabel, których w ogóle nie dotyczy zapytanie, i odpowiednio dostosować zasady pamięci podręcznej. Konkretne rekomendacje dotyczące zasad pamięci podręcznej można uzyskać dla określonych tabel w programie Azure Advisor (obecnie zalecenia dotyczące pamięci podręcznej są dostępne tylko z głównego pulpitu nawigacyjnego usługi [Azure Advisor](/azure/data-explorer/azure-advisor#use-the-azure-advisor-recommendations)), na podstawie rzeczywistego wyglądu zapytań z ostatnich 30 dni oraz niezoptymalizowanych zasad pamięci podręcznej dla co najmniej 95% zapytań. Zalecenia dotyczące redukcji pamięci podręcznej w programie Azure Advisor są dostępne dla klastrów, które są "ograniczone przez dane" (co oznacza, że klaster ma niskie użycie procesora CPU i niskie wykorzystanie pozyskiwania, ale ze względu na wysoką pojemność danych klaster nie może skalować w poziomie ani skalować w dół).

[![Zrzut ekranu przedstawiający szczegóły pamięci podręcznej](./media/data-explorer/cache-tab.png)](./media/data-explorer/cache-tab.png#lightbox)

Na **karcie Granice** klastra są wyświetlane granice klastra na podstawie użycia. Na tej karcie można sprawdzić użycie procesora CPU, pozyskiwania i pamięci podręcznej. Te metryki są oznaczane jako "niskie", "średnie" lub "wysokie". Te metryki i wyniki są ważne podczas podejmowania decyzji o optymalnej wartości SKU i liczby wystąpień dla klastra. Są one również brane pod uwagę Azure Advisor rekomendacji dotyczącej rozmiaru/sku. Na tej karcie możesz wybrać kafelek metryki i poznać jej trend oraz sposób decydowania o jego wyniku. Możesz również wyświetlić rekomendację Azure Advisor SKU/rozmiaru dla klastra. Na przykład na poniższej ilustracji widać, że wszystkie metryki są oznaczane jako "Niska", dzięki czemu klaster otrzymuje rekomendację kosztów umożliwiającą skalowanie w dół i zmniejszanie kosztów.

> [!div class="mx-imgBorder"]
> [![Zrzut ekranu przedstawiający granice klastra.](./media/data-explorer/cluster-boundaries.png)](./media/data-explorer/cluster-boundaries.png#lightbox)

## <a name="pin-to-azure-dashboard&quot;></a>Przypinanie do pulpitu nawigacyjnego platformy Azure

Możesz przypiąć dowolną sekcję metryki (z perspektywy &quot;na dużą skalę") do pulpitu nawigacyjnego platformy Azure, wybierając ikonę pinezki w prawym górnym rogu sekcji.

![Zrzut ekranu przedstawiający wybraną ikonę pinezki](./media/data-explorer/pin.png)

## <a name="customize-azure-monitor-for-azure-data-explorer-cluster"></a>Dostosowywanie Azure Monitor klastra Azure Data Explorer klastra

W tej sekcji wyróżnione są typowe scenariusze edytowania skoroszytu w celu dostosowania go do potrzeb analizy danych:
* Określ zakres skoroszytu, aby zawsze wybierać określoną subskrypcję lub Azure Data Explorer klastry
* Zmienianie metryk w siatce
* Zmienianie progów lub renderowanie/kodowanie kolorów

Dostosowania można rozpocząć, włączając tryb edycji, wybierając przycisk **Dostosuj** na górnym pasku narzędzi.

![Zrzut ekranu przedstawiający przycisk dostosowywania](./media/data-explorer/customize.png)

Dostosowania są zapisywane w skoroszycie niestandardowym, aby zapobiec nadpisyniu domyślnej konfiguracji w opublikowanym skoroszycie. Skoroszyty są zapisywane w grupie zasobów w sekcji Moje raporty, która jest dla Ciebie prywatna, lub w sekcji Udostępnione raporty dostępnej dla wszystkich użytkowników z dostępem do grupy zasobów. Po zapisaniu skoroszytu niestandardowego musisz przejść do galerii skoroszytów, aby go uruchomić.

![Zrzut ekranu przedstawiający galerię skoroszytów](./media/data-explorer/gallery.png)

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Ogólne wskazówki dotyczące rozwiązywania problemów można znaleźć w artykule na temat rozwiązywania problemów z analizą opartą na [dedykowanym skoroszycie.](troubleshoot-workbooks.md)

Ta sekcja pomoże Ci w diagnozowaniu i rozwiązywaniu niektórych typowych problemów, które mogą wystąpić podczas korzystania z usługi Azure Monitor do Azure Data Explorer klastra (wersja zapoznawcza). Skorzystaj z poniższej listy, aby znaleźć informacje dotyczące konkretnego problemu.

### <a name="why-dont-i-see-all-my-subscriptions-in-the-subscription-picker"></a>Dlaczego nie widzę wszystkich moich subskrypcji w storze subskrypcji?

Wyświetlane są tylko subskrypcje zawierające klastry Azure Data Explorer wybrane z wybranego filtru subskrypcji, które są wybrane w obszarze "Katalog i subskrypcja" w nagłówku Azure Portal subskrypcji.

![Zrzut ekranu przedstawiający filtr subskrypcji](./media/key-vaults-insights-overview/Subscriptions.png)

### <a name="why-do-i-not-see-any-data-for-my-azure-data-explorer-cluster-under-the-usage-tables-or-cache-sections"></a>Dlaczego nie widzę żadnych danych dla klastra Azure Data Explorer w sekcjach Użycie, Tabele lub Pamięć podręczna?

Aby wyświetlić dane oparte na dziennikach, należy włączyć dzienniki diagnostyczne dla każdego Azure Data Explorer klastra, które chcesz monitorować. [](/azure/data-explorer/using-diagnostic-logs?tabs=commands-and-queries#enable-diagnostic-logs) Można to zrobić w obszarze ustawień diagnostycznych dla każdego klastra. Musisz wysłać dane do obszaru roboczego usługi Log Analytics. Dzienniki diagnostyczne, które powinny być włączone, to: Command, Query, TableDetails i TableUsageStatistics.

### <a name="i-have-already-enabled-logs-for-my-azure-data-explorer-cluster-why-am-i-still-unable-to-see-my-data-under-commands-and-queries"></a>Dzienniki klastra klastra usługi Azure Data Explorer zostały już włączone. Dlaczego nadal nie mogę wyświetlić moich danych w obszarze Polecenia i zapytania?

Obecnie dzienniki diagnostyczne nie działają wstecznie, więc dane zaczną pojawiać się dopiero po rozpoczęciu działania Azure Data Explorer. W związku z tym może to zająć trochę czasu , od godzin do dnia, w zależności od tego, jak aktywny jest Azure Data Explorer klastra.


## <a name="next-steps"></a>Następne kroki

Zapoznaj się ze scenariuszami, w których skoroszyty są przeznaczone do obsługi, jak tworzyć nowe i dostosowywać istniejące raporty i nie tylko, przeglądając temat Tworzenie interaktywnych raportów za pomocą Azure Monitor [skoroszytów.](../visualize/workbooks-overview.md)
