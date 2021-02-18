---
title: Azure Monitor dla usługi Azure cache for Redis | Microsoft Docs
description: W tym artykule opisano Azure Monitor funkcji Azure Redis Cache, która zapewnia właścicielom pamięci podręcznej szybkie zrozumienie problemów z wydajnością i wykorzystaniem.
ms.topic: conceptual
author: lgayhardt
ms.author: lagayhar
ms.date: 09/10/2020
ms.openlocfilehash: fee454073c50b9542e140576ef0629a39b8f4294
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100573456"
---
# <a name="explore-azure-monitor-for-azure-cache-for-redis"></a>Eksploruj Azure Monitor dla usługi Azure cache for Redis

Dla całej pamięci podręcznej platformy Azure dla zasobów Redis Azure Monitor dla usługi Azure cache for Redis zapewnia ujednolicony, interaktywny widok:

- Ogólna wydajność
- Błędy
- Pojemność
- Kondycja operacyjna

Ten artykuł ułatwia zapoznanie się z zaletami tego nowego środowiska monitorowania. Przedstawiono w nim również sposób modyfikowania i dostosowywania środowiska w celu dopasowania do unikatowych potrzeb organizacji.

## <a name="introduction"></a>Wprowadzenie

Przed rozpoczęciem korzystania z tego środowiska należy zrozumieć, jak Azure Monitor w usłudze Azure cache for Redis wizualne przedstawiają informacje.

Zapewnia:

- **W perspektywie skalowania** pamięci podręcznej platformy Azure dla zasobów Redis w jednej lokalizacji we wszystkich subskrypcjach. Możesz wybiórczo zakres tylko do subskrypcji i zasobów, które chcesz oszacować.

- **Analiza przechodzenia do szczegółów** określonej pamięci podręcznej platformy Azure dla zasobu Redis. Można zdiagnozować problemy i zapoznać się ze szczegółową analizą użycia, niepowodzeń, pojemności i operacji. Wybierz dowolną z tych kategorii, aby zobaczyć szczegółowy widok istotnych informacji.  

- **Dostosowanie** tego środowiska, które jest skompilowane Azure monitor szablonów skoroszytów korzystającego. Środowisko pozwala zmienić, jakie metryki są wyświetlane, i modyfikować lub ustawiać progi, które są dostosowywane do limitów. Możesz zapisać zmiany w niestandardowym skoroszycie, a następnie przypiąć wykresy skoroszytu do pulpitów nawigacyjnych platformy Azure.

Ta funkcja nie wymaga włączenia ani skonfigurowania żadnych elementów. Pamięć podręczna platformy Azure dla informacji Redis jest domyślnie zbierana.

>[!NOTE]
>Nie ma opłat za dostęp do tej funkcji. Opłata jest naliczana tylko za Azure Monitor podstawowe funkcje, które konfigurujesz lub włączasz, zgodnie z opisem na stronie [szczegóły cennika Azure monitor](https://azure.microsoft.com/pricing/details/monitor/) .

## <a name="view-utilization-and-performance-metrics-for-azure-cache-for-redis"></a>Wyświetl metryki użycia i wydajności dla usługi Azure cache for Redis

Aby wyświetlić użycie i wydajność kont magazynu we wszystkich subskrypcjach, wykonaj następujące czynności:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. Wyszukaj pozycję **monitor** i wybierz pozycję **Monitoruj**.

    ![Pole wyszukiwania z słowem "Monitor" oraz wynikiem wyszukiwania usług, który pokazuje "Monitor" z symbolem prędkościomierza](./media/cosmosdb-insights-overview/search-monitor.png)

1. Wybierz pozycję **pamięć podręczna platformy Azure dla Redis**. Jeśli ta opcja nie jest dostępna, wybierz pozycję **więcej**  >  **pamięci podręcznej platformy Azure dla Redis**.

### <a name="overview"></a>Omówienie

W obszarze **Przegląd** w tabeli jest wyświetlana interaktywna pamięć podręczna platformy Azure dla metryk Redis. Wyniki można filtrować na podstawie opcji wybranych z następujących list rozwijanych:

- **Subskrypcje**: na liście są wyświetlane tylko subskrypcje, które mają pamięć podręczną platformy Azure dla zasobu Redis.  

- **Pamięć podręczna platformy Azure dla Redis**: można wybrać wszystkie, podzbiór lub pojedynczą pamięć podręczną platformy Azure dla zasobu Redis.

- **Zakres czasu**: domyślnie w tabeli są wyświetlane ostatnie cztery godziny informacji na podstawie odpowiednich opcji.

Na listach rozwijanych znajduje się kafelek licznika. Kafelek pokazuje łączną liczbę zasobów usługi Azure cache for Redis w wybranych subskrypcjach. Warunkowe kody kolorów lub mapy cieplne dla kolumn skoroszytów raportu metryki transakcji. Największy kolor reprezentuje najwyższą wartość. Jaśniejsze kolory reprezentują niższe wartości.

Wybranie strzałki listy rozwijanej obok jednej z pamięci podręcznej platformy Azure dla zasobów Redis powoduje oddzielenie metryk wydajności na poszczególnych poziomach zasobów.

![Zrzut ekranu przedstawiający środowisko omówienia](./media/redis-cache-insights-overview/overview.png)

Po wybraniu nazwy zasobu usługi Azure cache for Redis wyróżnionej na niebiesko zostanie wyświetlona domyślna tabela **przeglądu** dla skojarzonego konta. Wyświetla następujące kolumny:

- **Użyta pamięć**
- **Procentowe użycie pamięci**
- **Obciążenie serwera**
- **Oś czasu obciążenia serwera**
- **Procesor CPU**
- **Połączeni klienci**
- **Chybienia w pamięci podręcznej**
- **Błędy (maks.)**

### <a name="operations"></a>Operacje

Po wybraniu **operacji** w górnej części strony zostanie otwarta tabela **operacje** w szablonie skoroszytu. Wyświetla następujące kolumny:

- **Łączna liczba operacji**
- **Oś czasu łącznej liczby operacji**
- **Liczba operacji na sekundę**
- **Pobrania**
- **Zestawy**

![Zrzut ekranu przedstawiający środowisko operacyjne](./media/redis-cache-insights-overview/operations.png)

### <a name="usage"></a>Użycie

Po wybraniu opcji **użycie** w górnej części strony zostanie otwarta tabela **użycie** szablonu skoroszytu. Wyświetla następujące kolumny:

- **Odczyt pamięci podręcznej**
- **Oś czasu odczytu pamięci podręcznej**
- **Zapis w pamięci podręcznej**
- **Trafienia w pamięci podręcznej**
- **Chybienia w pamięci podręcznej**

![Zrzut ekranu przedstawiający środowisko użycia](./media/redis-cache-insights-overview/usage.png)

### <a name="failures"></a>Błędy

Po wybraniu opcji **Błędy** w górnej części strony zostanie otwarta tabela **Błędy** szablonu skoroszytu. Wyświetla następujące kolumny:

- **Całkowita liczba błędów**
- **Tryb failover/błędy**
- **UnresponsiveClient/błędy**
- **RDB/błędy**
- **KOPIA zapasowa AOF/błędy**
- **Eksport/błędy**
- **Datastrata/błędy**
- **Importowanie/błędy**

![Zrzut ekranu przedstawiający błędy z podziałem według typu żądania HTTP](./media/redis-cache-insights-overview/failures.png)

### <a name="metric-definitions"></a>Definicje metryk

Aby zapoznać się z pełną listą definicji metryk, które tworzą te skoroszyty, zapoznaj się z [artykułem na temat dostępnych metryk i interwałów raportowania](../../azure-cache-for-redis/cache-how-to-monitor.md#available-metrics-and-reporting-intervals).

## <a name="view-from-an-azure-cache-for-redis-resource"></a>Wyświetl z pamięci podręcznej platformy Azure dla zasobu Redis

Aby uzyskać dostęp do Azure Monitor dla usługi Azure cache for Redis bezpośrednio z pojedynczego zasobu:

1. W Azure Portal wybierz pozycję Pamięć podręczna platformy Azure dla Redis.

2. Z listy wybierz indywidualną pamięć podręczną platformy Azure dla zasobu Redis. W sekcji monitorowanie wybierz pozycję szczegółowe dane.

    ![Zrzut ekranu przedstawiający opcje menu z wyrazami "Insights" wyróżnionymi w czerwonym polu](./media/redis-cache-insights-overview/insights.png)

Te widoki są również dostępne, wybierając nazwę zasobu pamięci podręcznej platformy Azure dla zasobu Redis z poziomu skoroszytu na poziomie Azure Monitor.

### <a name="resource-level-overview"></a>Przegląd poziomu zasobów

W skoroszycie **przeglądu** Azure Redis Cache przedstawiono kilka metryk wydajności, które dają dostęp do:

- Interaktywne wykresy wydajności pokazujące najważniejsze informacje dotyczące usługi Azure cache na potrzeby wydajności Redis.

- Kafelki metryk i Stanów wyróżniają fragmentu wydajność, łączną liczbę połączonych klientów i ogólne opóźnienia.

![Zrzut ekranu przedstawiający pulpit nawigacyjny przegląd przedstawiający informacje o wydajności procesora CPU, używanej pamięci, połączonych klientach, błędach, wygasłych kluczach i kluczach wykluczonych](./media/redis-cache-insights-overview/resource-overview.png)

Wybranie dowolnej z innych kart dotyczących **wydajności** lub **operacji** powoduje otwarcie odpowiednich skoroszytów.

### <a name="resource-level-performance"></a>Wydajność na poziomie zasobów

![Zrzut ekranu przedstawiający wykresy wydajności zasobów](./media/redis-cache-insights-overview/resource-performance.png)

### <a name="resource-level-operations"></a>Operacje na poziomie zasobów

![Zrzut ekranu przedstawiający wykresy operacji zasobów](./media/redis-cache-insights-overview/resource-operations.png)

## <a name="pin-export-and-expand"></a>Przypnij, Eksportuj i rozwiń

Aby przypiąć każdą sekcję metryki do [pulpitu nawigacyjnego platformy Azure](../../azure-portal/azure-portal-dashboards.md), wybierz symbol pinezki w prawym górnym rogu sekcji.

![Sekcja metryki z wyróżnionym symbolem pinezki](./media/cosmosdb-insights-overview/pin.png)

Aby wyeksportować dane do formatu programu Excel, wybierz symbol strzałki w dół na lewo od symbolu pinezki.

![Wyróżniony symbol eksportu skoroszytu](./media/cosmosdb-insights-overview/export.png)

Aby rozwinąć lub zwinąć wszystkie widoki w skoroszycie, wybierz symbol rozwijania z lewej strony symbolu eksportu.

![Wyróżniony symbol rozwijania skoroszytu](./media/cosmosdb-insights-overview/expand.png)

## <a name="customize-azure-monitor-for-azure-cache-for-redis"></a>Dostosowywanie Azure Monitor dla usługi Azure cache for Redis

Ponieważ to środowisko jest skompilowane Azure monitor szablonów skoroszytów, można wybrać pozycję **Dostosuj**  >  **Edytuj**  >  **Zapisz** , aby zapisać kopię zmodyfikowanej wersji w skoroszycie niestandardowym.

![Pasek poleceń z wyróżnioną pozycją Dostosuj](./media/cosmosdb-insights-overview/customize.png)

Skoroszyty są zapisywane w grupie zasobów w sekcji **Moje raporty** lub w sekcji **raporty udostępnione** . **Moje raporty** jest dostępny tylko dla Ciebie. **Raporty udostępnione** są dostępne dla wszystkich użytkowników mających dostęp do grupy zasobów.

Po zapisaniu skoroszytu niestandardowego przejdź do galerii skoroszytów, aby go otworzyć.

![Pasek poleceń z wyróżnioną galerią](./media/cosmosdb-insights-overview/gallery.png)

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Wskazówki dotyczące rozwiązywania problemów można znaleźć w artykule dotyczącym szczegółowych informacji o [rozwiązywaniu problemów](troubleshoot-workbooks.md)opartych na skoroszycie.

## <a name="next-steps"></a>Następne kroki

* Skonfiguruj [alerty metryk](../alerts/alerts-metric.md) i [powiadomienia dotyczące kondycji usługi](../../service-health/alerts-activity-log-service-notifications-portal.md) , aby skonfigurować automatyczne alerty, które ułatwiają wykrywanie problemów.

* Zapoznaj się z scenariuszami obsługiwanymi przez skoroszyty, sposobami tworzenia lub dostosowywania raportów oraz innymi przez przeglądanie raportów [interaktywnych przy użyciu skoroszytów Azure monitor](../visualize/workbooks-overview.md).
