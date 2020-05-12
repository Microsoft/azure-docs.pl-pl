---
title: Azure Monitor dla usługi Azure cache for Redis (wersja zapoznawcza) | Microsoft Docs
description: W tym artykule opisano Azure Monitor funkcji Redis Cache, która zapewnia redisom pamięci podręcznej platformy Azure w celu szybkiego poznania problemów z wydajnością i wykorzystaniem.
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 05/07/2020
ms.openlocfilehash: af32459ffcb50c9f1cfdc59d2c8d355d3551e230
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/12/2020
ms.locfileid: "83210977"
---
# <a name="explore-azure-monitor-for-azure-cache-for-redis-preview"></a>Eksploruj Azure Monitor dla usługi Azure cache for Redis (wersja zapoznawcza)

Azure Monitor usługi Azure cache for Redis (wersja zapoznawcza) zawiera widok ogólnej wydajności, niepowodzeń, pojemności i kondycji operacyjnej całej pamięci podręcznej platformy Azure dla zasobów Redis w ujednoliconym interaktywnym środowisku. Ten artykuł pomoże Ci zrozumieć korzyści płynące z nowego środowiska monitorowania oraz jak można modyfikować i dostosowywać środowisko, aby odpowiadało unikatowym potrzebom organizacji.

## <a name="introduction"></a>Wprowadzenie

Przed wprowadzeniem do środowiska należy zrozumieć, jak prezentuje i wizualizuje informacje.

Zapewnia:

* **W perspektywie skalowania** pamięci podręcznej platformy Azure dla zasobów Redis w ramach wszystkich subskrypcji w jednej lokalizacji, z możliwością selektywnego określania zakresu tylko tych subskrypcji i zasobów, które chcesz oceniać.

* **Przechodzenie do szczegółów** określonej pamięci podręcznej platformy Azure dla zasobu Redis w celu ułatwienia zdiagnozowania problemów lub wykonywania szczegółowej analizy według kategorii, niepowodzeń, pojemności i operacji. Wybranie jednej z tych opcji zapewnia szczegółowy widok odpowiednich elementów.  

* **Dostosowywalne** — to środowisko zostało utworzone na podstawie Azure monitor szablonów skoroszytów, co umożliwia zmianę sposobu wyświetlania metryk, zmodyfikowanie lub ustawienie progów, które są wyrównane z limitami, a następnie zapisywanie w skoroszycie niestandardowym. Wykresy w skoroszytach można następnie przypinać do pulpitów nawigacyjnych platformy Azure.  

Ta funkcja nie wymaga włączenia ani skonfigurowania niczego, ponieważ ta pamięć podręczna platformy Azure dla Redis jest domyślnie zbierana.

>[!NOTE]
>Dostęp do tej funkcji nie jest naliczany, a opłaty są naliczane tylko za Azure Monitor podstawowe funkcje, które konfigurujesz lub włączasz, zgodnie z opisem na stronie [szczegóły cennika Azure monitor](https://azure.microsoft.com/pricing/details/monitor/) .

## <a name="view-utilization-and-performance-metrics-for-azure-cache-for-redis"></a>Wyświetl metryki użycia i wydajności dla usługi Azure cache for Redis

Aby wyświetlić użycie i wydajność kont magazynu we wszystkich subskrypcjach, wykonaj następujące czynności.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. Wyszukaj ciąg **monitor** i wybierz pozycję **Monitoruj**.

    ![Pole wyszukiwania z słowem "Monitor" oraz listą rozwijaną "Monitor" z obrazem stylu prędkościomierza](./media/cosmosdb-insights-overview/search-monitor.png)

3. Wybierz pozycję **Azure cache for Redis (wersja zapoznawcza)**.

Jeśli ta opcja nie jest obecna, kliknij pozycję **więcej** i wybierz pozycję **pamięć podręczna platformy Azure dla Redis**.

### <a name="overview"></a>Omówienie

W obszarze **Przegląd**w tabeli jest wyświetlana interaktywna pamięć podręczna platformy Azure dla metryk Redis. Wyniki można filtrować na podstawie opcji wybranych z następujących list rozwijanych:

* Są wyświetlane subskrypcje obsługujące tylko **subskrypcje** , które mają pamięć podręczną platformy Azure dla zasobu Redis.  

* **Pamięć podręczna platformy Azure dla Redis** — można wybrać wszystkie, podzbiór lub pojedynczą pamięć podręczną platformy Azure dla zasobu Redis.

* **Zakres czasu** — domyślnie wyświetla ostatnie 4 godziny informacji na podstawie odpowiednich opcji.

Kafelek licznika pod listą rozwijaną zawiera rzutowanie łącznej liczby zasobów usługi Azure cache for Redis w wybranych subskrypcjach. Istnieje warunkowe kodowanie kolorami lub map cieplnych dla kolumn w skoroszycie, które raportują metryki transakcji. Optymalny kolor ma najwyższą wartość, a jaśniejszy kolor jest oparty na najniższych wartościach.

Wybranie strzałki listy rozwijanej obok jednej z pamięci podręcznej platformy Azure dla zasobów Redis spowoduje wyświetlenie podziału metryk wydajności na poszczególnych poziomach zasobów:

![Zrzut ekranu przedstawiający środowisko omówienia](./media/redis-cache-insights-overview/overview.png)

Wybranie nazwy zasobu usługi Azure cache for Redis wyróżnionej na niebiesko spowoduje przejście do domyślnego **omówienia** dla skojarzonego konta. Pokazuje,,,,,, `Used Memory` `Used Memory Percentage` `Server Load` `Server Load Timeline` `CPU` `Connected Clients` `Cache Misses` `Errors (Max)` .

### <a name="operations"></a>Operacje

Wybierz pozycję **operacje** w górnej części strony, a zostanie otwarta część **operacje** szablonu skoroszytu. Pokazuje `Total Operations` , `Total Operations Timeline` ,, `Operations Per Second` `Gets` , `Sets` .

![Zrzut ekranu przedstawiający środowisko omówienia](./media/redis-cache-insights-overview/operations.png)

### <a name="usage"></a>Sposób użycia

Wybierz pozycję **użycie** w górnej części strony, a zostanie otwarta część **użycie** szablonu skoroszytu. Pokazuje `Cache Read` , `Cache Read Timeline` ,, `CacheWrite` `CacheHits` , `Cache Misses` .

![Zrzut ekranu przedstawiający środowisko omówienia](./media/redis-cache-insights-overview/usage.png)

Wybierz pozycję **Błędy** w górnej części strony i zostanie otwarty fragment **błędów** szablonu skoroszytu. Pokazuje,,,,,, `Total Errors` `Failover/Errors` `UnresponsiveClient/Errors` `RDB/Errors` `AOF/Errors` `Export/Errors` `Dataloss/Errors` `Import/Errors` .

![Zrzut ekranu przedstawiający awarie z podziałem według typu żądania HTTP](./media/redis-cache-insights-overview/failures.png)

### <a name="metric-definitions"></a>Definicje metryk

Aby zapoznać się z pełną listą definicji metryk wchodzących w skład tych skoroszytów, zapoznaj się z [artykułem dostępne metryki i interwały raportowania](https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-monitor#available-metrics-and-reporting-intervals).

## <a name="pin-export-and-expand"></a>Przypnij, Eksportuj i rozwiń

Możesz przypiąć każdą z sekcji metryk do [pulpitu nawigacyjnego platformy Azure](https://docs.microsoft.com/azure/azure-portal/azure-portal-dashboards) , wybierając ikonę pinezki w prawym górnym rogu sekcji.

![Przykład numeru PIN sekcji Metryka na pulpit nawigacyjny](./media/cosmosdb-insights-overview/pin.png)

Aby wyeksportować dane do formatu programu Excel, wybierz ikonę strzałki w dół znajdującą się po lewej stronie ikony pinezki.

![Ikona eksportowania skoroszytu](./media/cosmosdb-insights-overview/export.png)

Aby rozwinąć lub zwinąć wszystkie widoki rozwijane w skoroszycie, wybierz ikonę rozwiń z lewej strony ikony eksportu:

![Rozwiń ikonę skoroszytu](./media/cosmosdb-insights-overview/expand.png)

## <a name="customize-azure-monitor-for-azure-cache-for-redis-preview"></a>Dostosowywanie Azure Monitor dla usługi Azure cache for Redis (wersja zapoznawcza)

Ponieważ to środowisko zostało utworzone na podstawie Azure monitor szablonów skoroszytów, można **dostosować**  >  **Edytowanie** i **zapisywać** kopię zmodyfikowanej wersji w skoroszycie niestandardowym. 

![Dostosuj pasek](./media/cosmosdb-insights-overview/customize.png)

Skoroszyty są zapisywane w obrębie grupy zasobów, w sekcji **Moje raporty** prywatnej dla użytkownika lub w sekcji **raporty udostępnione** dostępne dla wszystkich użytkowników mających dostęp do grupy zasobów. Po zapisaniu skoroszytu niestandardowego musisz przejść do galerii skoroszytów, aby go uruchomić.

![Uruchom galerię skoroszytów z poziomu paska poleceń](./media/cosmosdb-insights-overview/gallery.png)

## <a name="next-steps"></a>Następne kroki

* Konfigurowanie [alertów metryk](../platform/alerts-metric.md) i [powiadomień o kondycji usług](../../service-health/alerts-activity-log-service-notifications.md) w celu skonfigurowania zautomatyzowanego alertu w celu uzyskania pomocy w wykrywaniu problemów.

* Dowiedz się, jakie scenariusze skoroszyty są przeznaczone do obsługi, jak tworzyć nowe i dostosowywać istniejące raporty, a inne dzięki przeglądowi [Tworzenie interaktywnych raportów przy użyciu skoroszytów Azure monitor](../app/usage-workbooks.md).
