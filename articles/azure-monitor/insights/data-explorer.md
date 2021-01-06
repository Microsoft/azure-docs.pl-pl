---
title: Azure Monitor dla Eksplorator danych platformy Azure (wersja zapoznawcza) | Microsoft Docs
description: W tym artykule opisano Azure Monitor szczegółowych informacji o klastrach Eksplorator danych platformy Azure.
services: azure-monitor
ms.topic: conceptual
ms.date: 01/05/2021
author: lgayhardt
ms.author: lagayhar
ms.openlocfilehash: 6f25ff02dee16812898d77d0e801f927b354dc78
ms.sourcegitcommit: 67b44a02af0c8d615b35ec5e57a29d21419d7668
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/06/2021
ms.locfileid: "97918332"
---
# <a name="azure-monitor-for-azure-data-explorer-preview"></a>Azure Monitor dla Eksplorator danych platformy Azure (wersja zapoznawcza)

Azure Monitor na platformie Azure Eksplorator danych (wersja zapoznawcza) zapewnia kompleksowe monitorowanie klastrów, zapewniając ujednolicony widok wydajności, operacji, użycia i niepowodzeń klastra.
Ten artykuł pomoże Ci zrozumieć, jak dołączyć Azure Monitor do usługi Azure Eksplorator danych (wersja zapoznawcza) i korzystać z niej.

## <a name="introduction-to-azure-monitor-for-azure-data-explorer-preview"></a>Wprowadzenie do Azure Monitor dla Eksplorator danych platformy Azure (wersja zapoznawcza)

Przed przechodzeniem do środowiska należy zrozumieć, jak prezentuje i wizualizuje informacje.
-    **W perspektywie skalowania** przedstawia widok migawek podstawowych metryk klastrów, aby można było łatwo śledzić wydajność zapytań, pozyskiwania i eksportowania.
-   **Przechodzenie do szczegółów** konkretnego klastra Eksplorator danych platformy Azure w celu ułatwienia szczegółowej analizy.
-    **Dostosowywalne** miejsce, w którym można zmienić metryki, które mają być wyświetlane, zmodyfikować lub ustawić progi, które są wyrównane z limitami, i zapisać własne skoroszyty niestandardowe. Wykresy w skoroszycie można przypinać do pulpitów nawigacyjnych platformy Azure.

## <a name="view-from-azure-monitor-at-scale-perspective"></a>Wyświetl z Azure Monitor (w perspektywie skalowania)

Z Azure Monitor można wyświetlić podstawowe metryki wydajności dla klastra, w tym metryki zapytań, pozyskiwania i eksportowania z wielu klastrów w ramach subskrypcji, a także pomóc w identyfikowaniu problemów z wydajnością.

Aby wyświetlić wydajność klastrów we wszystkich subskrypcjach, wykonaj następujące czynności:

1. Zaloguj się do [Azure Portal](https://portal.azure.com/)

2. Wybierz pozycję **monitor** z okienka po lewej stronie w obszarze Azure Portal i w sekcji centrum Insights **(wersja zapoznawcza) wybierz pozycję Klastry usługi Azure Eksplorator danych**.

![Zrzut ekranu przedstawiający środowisko omówienia z wieloma wykresami](./media/data-explorer/insights-hub.png)

### <a name="overview-tab"></a>Karta przegląd

Na karcie **Przegląd** dla wybranej subskrypcji w tabeli wyświetlane są interaktywne metryki dla klastrów usługi Azure Eksplorator danych pogrupowanych w ramach subskrypcji. Wyniki można filtrować na podstawie opcji wybranych z następujących list rozwijanych:

* Subskrypcje — lista zawiera tylko subskrypcje z klastrami usługi Azure Eksplorator danych.

* Klastry usługi Azure Eksplorator danych — domyślnie tylko do pięciu klastrów są wstępnie wybrane. Jeśli zaznaczysz wszystkie lub wiele klastrów w selektorze zakresu, zostaną zwrócone do 200 klastrów.

* Zakres czasu — domyślnie wyświetla ostatnie 24 godziny informacji na podstawie odpowiednich opcji.

Kafelek licznika, na liście rozwijanej, rzutuje łączną liczbę klastrów Eksplorator danych platformy Azure w wybranych subskrypcjach i odzwierciedla liczbę wybranych usług. Istnieją warunkowe kodowanie kolorów dla kolumn: utrzymywanie aktywności, procesora CPU, wykorzystania pozyskiwania oraz wykorzystania pamięci podręcznej. Komórki kodowane z pomarańczowymi zawierają wartości, które nie są trwałe dla klastra. 

Aby lepiej zrozumieć, co reprezentuje każda Metryka, zalecamy zapoznanie się z dokumentacją dotyczącą [metryk Eksplorator danych platformy Azure](https://docs.microsoft.com/azure/data-explorer/using-metrics#cluster-metrics).

### <a name="query-performance-tab"></a>Karta wydajność zapytań

Ta karta pokazuje czas trwania zapytania, łączną liczbę współbieżnych zapytań i łączną liczbę zapytań z ograniczeniami.

![Zrzut ekranu przedstawiający kartę wydajność zapytań](./media/data-explorer/query-performance.png)

### <a name="ingestion-performance-tab"></a>Karta wydajność pozyskiwania

Na tej karcie są wyświetlane opóźnienia pozyskiwania, pomyślne wyniki pozyskiwania, wyniki pozyskiwania zakończone niepowodzeniem, wolumin pozyskiwania oraz zdarzenia przetworzone dla centrów zdarzeń/IoT.

[![Zrzut ekranu przedstawiający kartę wydajność pozyskiwania](./media/data-explorer/ingestion-performance.png)](./media/data-explorer/ingestion-performance.png#lightbox)

### <a name="streaming-ingest-performance-tab"></a>Karta wydajność pozyskiwania strumieniowego

Na tej karcie znajdują się informacje o średniej szybkości danych, średnim czasie trwania i szybkości żądania.

### <a name="export-performance-tab"></a>Eksportuj kartę wydajności

Na tej karcie znajdują się informacje dotyczące wyeksportowanych rekordów, opóźnień, liczby oczekujących i procent wykorzystania dla operacji eksportu ciągłego.

## <a name="view-from-an-azure-data-explorer-cluster-resource-drill-down-analysis"></a>Wyświetlanie z zasobów klastra usługi Azure Eksplorator danych (analiza przechodzenia do szczegółów)

Aby uzyskać dostęp do Azure Monitor klastrów Eksplorator danych platformy Azure bezpośrednio z klastra Eksplorator danych platformy Azure:

1. W Azure Portal wybierz pozycję **Klastry usługi Azure Eksplorator danych**.

2. Wybierz z listy klaster Eksplorator danych platformy Azure. W sekcji monitorowanie wybierz pozycję **szczegółowe dane (wersja zapoznawcza)**.

Te widoki są również dostępne, wybierając nazwę zasobu klastra Eksplorator danych platformy Azure z poziomu widoku Azure Monitor szczegółowych informacji.

Azure Monitor dla systemu Azure Eksplorator danych łączy dzienniki i metryki, aby zapewnić globalne rozwiązanie do monitorowania. Dołączenie wizualizacji opartych na dziennikach wymaga od użytkowników [włączenia rejestrowania diagnostycznego klastra usługi Azure Eksplorator danych i wysłania ich do log Analytics obszaru roboczego.](https://docs.microsoft.com/azure/data-explorer/using-diagnostic-logs?tabs=commands-and-queries#enable-diagnostic-logs) Dzienniki diagnostyczne, które powinny być włączone, to: **Command**, **Query**, **TableDetails** i **TableUsageStatistics**.

![Zrzut ekranu przedstawiający niebieski przycisk z tekstem "Włączanie dzienników do monitorowania"](./media/data-explorer/enable-logs.png)


 Na karcie Przegląd są wyświetlane następujące **informacje** :

- Kafelki metryk wyróżniają dostępność i ogólny stan klastra, aby szybko ocenić jego kondycję.

- Podsumowanie dotyczące aktywnych [zaleceń usługi Advisor](https://docs.microsoft.com/azure/data-explorer/azure-advisor) i stanu [kondycji zasobów](https://docs.microsoft.com/azure/data-explorer/monitor-with-resource-health) .

- Wykresy przedstawiające najpopularniejszych odbiorców procesora i pamięci oraz liczbę unikatowych użytkowników w czasie.


[![Zrzut ekranu przedstawiający widok z zasobu klastra Eksplorator danych platformy Azure](./media/data-explorer/overview.png)](./media/data-explorer/overview.png#lightbox)

Karta **kluczowe metryki** przedstawia ujednolicony widok niektórych metryk klastra pogrupowanych według: metryki ogólne, powiązane z zapytaniami, powiązane z pozyskiwaniem i przesyłania strumieniowego.

[![Zrzut ekranu przedstawiający widok błędów](./media/data-explorer/key-metrics.png)](./media/data-explorer/key-metrics.png#lightbox)

Karta **użycie** pozwala użytkownikom na głębokie szczegółowe do wydajności poleceń i zapytań w klastrze. Na tej stronie można:
 
 - Sprawdź, którzy użytkownicy i aplikacje wysyłają najwięcej zapytań lub zużywają najwięcej procesora CPU i pamięci (aby zrozumieć, którzy użytkownicy przesyłają największe zapytania do procesu).
 - Zidentyfikuj najważniejszych użytkowników i aplikacje, wykonując nieudane zapytania.
 - Zidentyfikuj ostatnie zmiany w liczbie zapytań w porównaniu do historycznej średniej dziennej (w ciągu ostatnich 16 dni) przez użytkownika i aplikację.
 - Identyfikuj trendy i szczyty w liczbie zapytań, pamięci i użycia procesora CPU przez użytkownika, aplikację i typ polecenia.

[![Zrzut ekranu przedstawiający widok operacji z wykresami pierścieniowymi najpopularniejszych aplikacji według liczby poleceń i zapytań, najważniejszych podmiotów głównych według liczby poleceń i zapytań oraz najważniejszych poleceń według typów poleceń](./media/data-explorer/usage.png)](./media/data-explorer/usage.png#lightbox)

[![Zrzut ekranu przedstawiający widok operacji z wykresami liniowymi według liczby zapytań według aplikacji, całkowitej ilości pamięci według aplikacji i łącznego czasu procesora według aplikacji](./media/data-explorer/usage-2.png)](./media/data-explorer/usage-2.png#lightbox)

Na karcie **tabele** są wyświetlane najnowsze i historyczne właściwości tabel w klastrze. Można zobaczyć, które tabele zużywają najwięcej miejsca, śledzić historię wzrostu według rozmiaru tabeli, gorącą i ilości wierszy w czasie.

Karta **pamięć podręczna** pozwala użytkownikom analizować ich rzeczywiste zapytania, a następnie porównać je ze skonfigurowanymi zasadami pamięci podręcznej (dla każdej tabeli). Można zidentyfikować tabele używane przez większość zapytań i tabel, które nie są w ogóle badane i odpowiednio dostosować zasady pamięci podręcznej. Możesz uzyskać szczegółowe zalecenia dotyczące zasad pamięci podręcznej w określonych tabelach w Azure Advisor (obecnie zalecenia dotyczące pamięci podręcznej są dostępne tylko z [głównego pulpitu nawigacyjnego Azure Advisor](https://docs.microsoft.com/azure/data-explorer/azure-advisor#use-the-azure-advisor-recommendations)), w oparciu o rzeczywiste zapytania, w ciągu ostatnich 30 dni i niezoptymalizowane zasady pamięci podręcznej dla co najmniej 95% zapytań. Zalecenia dotyczące redukcji pamięci podręcznej w Azure Advisor są dostępne dla klastrów, które są "ograniczone przez dane" (co oznacza, że klaster ma niskie użycie procesora CPU i niskie wykorzystanie, ale z powodu dużej pojemności danych klaster nie mógł skalować w dół ani skalować w dół).

[![Zrzut ekranu przedstawiający szczegóły pamięci podręcznej](./media/data-explorer/cache-tab.png)](./media/data-explorer/cache-tab.png#lightbox)

## <a name="pin-to-azure-dashboard"></a>Przypnij do pulpitu nawigacyjnego platformy Azure

Możesz przypiąć dowolną z sekcji metryk (perspektywa "at-Scale") do pulpitu nawigacyjnego platformy Azure, wybierając ikonę pinezki w prawym górnym rogu sekcji.

![Zrzut ekranu przedstawiający ikonę pinezki](./media/data-explorer/pin.png)

## <a name="customize-azure-monitor-for-azure-data-explorer-cluster"></a>Dostosowywanie Azure Monitor dla klastra Eksplorator danych platformy Azure

W tej sekcji przedstawiono typowe scenariusze edytowania skoroszytu w celu dostosowania ich do potrzeb związanych z analizą danych:
* Ustaw zakres skoroszytu w taki sposób, aby zawsze wybierał określoną subskrypcję lub klastry usługi Azure Eksplorator danych
* Zmień metryki w siatce
* Zmiany progów lub renderowania/kodowania kolorów

Możesz rozpocząć Dostosowywanie, włączając tryb edycji, wybierając przycisk **Dostosuj** na górnym pasku narzędzi.

![Zrzut ekranu przedstawiający przycisk Dostosuj](./media/data-explorer/customize.png)

Dostosowania są zapisywane w niestandardowym skoroszycie, aby zapobiec zastąpieniu konfiguracji domyślnej w naszym opublikowanym skoroszycie. Skoroszyty są zapisywane w grupie zasobów, w sekcji Moje raporty, która jest prywatna dla użytkownika lub w sekcji Raporty udostępnione dostępne dla wszystkich użytkowników mających dostęp do grupy zasobów. Po zapisaniu skoroszytu niestandardowego musisz przejść do galerii skoroszytów, aby go uruchomić.

![Zrzut ekranu przedstawiający galerię skoroszytów](./media/data-explorer/gallery.png)

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Ogólne wskazówki dotyczące rozwiązywania problemów można znaleźć w artykule dotyczącym szczegółowych informacji o [rozwiązywaniu problemów](troubleshoot-workbooks.md)opartych na skoroszycie.

Ta sekcja pomoże Ci w diagnozowaniu i rozwiązywaniu problemów z typowymi problemami, które mogą wystąpić podczas korzystania z Azure Monitor dla klastra Eksplorator danych platformy Azure (wersja zapoznawcza). Skorzystaj z poniższej listy, aby znaleźć informacje istotne dla danego problemu.

### <a name="why-dont-i-see-all-my-subscriptions-in-the-subscription-picker"></a>Dlaczego nie widzę wszystkich moich subskrypcji w selektorze subskrypcji?

Wyświetlane są tylko subskrypcje zawierające klastry usługi Azure Eksplorator danych, wybrane z wybranego filtru subskrypcji, które są wybrane w nagłówku Azure Portal.

![Zrzut ekranu filtru subskrypcji](./media/key-vaults-insights-overview/Subscriptions.png)

### <a name="why-do-i-not-see-any-data-for-my-azure-data-explorer-cluster-under-the-usage-tables-or-cache-sections"></a>Dlaczego nie widzę żadnych danych dla klastra usługi Azure Eksplorator danych w sekcji użycie, tabele lub pamięć podręczna?

Aby wyświetlić dane oparte na dziennikach, należy [włączyć dzienniki diagnostyczne](https://docs.microsoft.com/azure/data-explorer/using-diagnostic-logs?tabs=commands-and-queries#enable-diagnostic-logs) dla każdego z klastrów Eksplorator danych platformy Azure, które mają być monitorowane. Można to zrobić w ustawieniach diagnostycznych dla każdego klastra. Konieczne będzie wysłanie danych do obszaru roboczego Log Analytics. Dzienniki diagnostyczne, które powinny być włączone, to: Command, Query, TableDetails i TableUsageStatistics.

### <a name="i-have-already-enabled-logs-for-my-azure-data-explorer-cluster-why-am-i-still-unable-to-see-my-data-under-commands-and-queries"></a>Mam już włączone dzienniki dla mojego klastra usługi Azure Eksplorator danych, dlaczego nadal nie mogę zobaczyć moich danych w ramach poleceń i zapytań?

Obecnie dzienniki diagnostyczne nie działają z mocą wsteczną, więc dane zostaną uruchomione dopiero po wykonaniu akcji dla Eksplorator danych platformy Azure. W związku z tym może upłynąć trochę czasu od godzin do dnia, w zależności od tego, jak aktywny jest klaster usługi Azure Eksplorator danych.


## <a name="next-steps"></a>Następne kroki

Dowiedz się, jakie scenariusze skoroszyty są przeznaczone do obsługi, jak tworzyć nowe i dostosowywać istniejące raporty, a inne dzięki przeglądowi [Tworzenie interaktywnych raportów przy użyciu skoroszytów Azure monitor](../platform/workbooks-overview.md).
