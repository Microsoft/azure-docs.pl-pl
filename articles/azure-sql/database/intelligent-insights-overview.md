---
title: Monitorowanie wydajności bazy danych za pomocą Intelligent Insights
description: Intelligent Insights w Azure SQL Database i wystąpienie zarządzane usługi Azure SQL używa wbudowanej analizy do ciągłego monitorowania użycia bazy danych za pomocą sztucznej analizy i wykrywania zdarzeń powodujących zakłócenia, które powodują niską wydajność.
services: sql-database
ms.service: sql-db-mi
ms.subservice: performance
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, sstein
ms.date: 06/12/2020
ms.openlocfilehash: 4f6bbbdbbee6cf83a6bea463cf200be0cf671897
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91317059"
---
# <a name="intelligent-insights-using-ai-to-monitor-and-troubleshoot-database-performance-preview"></a>Intelligent Insights przy użyciu AI do monitorowania i rozwiązywania problemów z wydajnością bazy danych (wersja zapoznawcza)
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Intelligent Insights w Azure SQL Database i wystąpienie zarządzane usługi Azure SQL Server pozwala znać, co dzieje się z wydajnością bazy danych.

Intelligent Insights używa wbudowanej analizy do ciągłego monitorowania użycia bazy danych za pomocą sztucznej analizy i wykrywania zdarzeń powodujących zakłócenia, które powodują niską wydajność. Po wykryciu zostanie utworzona Szczegółowa analiza, która generuje dziennik zasobów Intelligent Insights (o nazwie SQLInsights) z inteligentną oceną problemu. Ta ocena polega na analizie głównej przyczyny problemu z wydajnością bazy danych i, jeśli to możliwe, zalecenia dotyczące ulepszeń wydajności.

## <a name="what-can-intelligent-insights-do-for-you"></a>Co można Intelligent Insights dla Ciebie

Intelligent Insights to unikatowa funkcja analizy wbudowanej platformy Azure, która zapewnia następującą wartość:

- Aktywne monitorowanie
- Dostosowane szczegółowe informacje o wydajności
- Wczesne wykrywanie obniżenia wydajności bazy danych
- Analiza głównych przyczyn problemów wykrytych
- Zalecenia dotyczące poprawy wydajności
- Możliwość skalowania w poziomie na setkach tysięcy baz danych
- Pozytywny wpływ na zasoby DevOps i całkowity koszt posiadania

## <a name="how-does-intelligent-insights-work"></a>Jak działa Intelligent Insights

Intelligent Insights analizuje wydajność bazy danych, porównując obciążenie bazy danych z ostatniej godziny z ostatnim 7-dniowym obciążeniem bazowym. Obciążenie bazy danych składa się z zapytań określonych jako najbardziej znaczące dla wydajności bazy danych, takich jak najczęstsze i największe zapytania. Ponieważ każda baza danych jest unikatowa w oparciu o strukturę, dane, użycie i aplikację, każda wygenerowana linia bazowa obciążenia jest określona i unikatowa dla tego obciążenia. Intelligent Insights niezależnie od linii bazowej obciążenia monitoruje również bezwzględne progi operacyjne oraz wykrywa problemy z nadmiernym czasem oczekiwania, wyjątkami krytycznymi i problemami z parameterizations zapytań, które mogą mieć wpływ na wydajność.

Po wykryciu problemu obniżenia wydajności z wielu obserwowanych metryk przy użyciu sztucznej analizy jest przeprowadzana analiza. Dziennik diagnostyczny jest generowany z inteligentnym wglądem w informacje o tym, co dzieje się z Twoją bazą danych. Intelligent Insights ułatwia śledzenie problemu z wydajnością bazy danych przy użyciu pierwszego wyglądu do rozpoznawania. Każdy wykryty problem jest śledzony przez jego cykl życia przed początkowym wykrywaniem problemu i weryfikacją wydajności po jego zakończeniu.

![Przepływ pracy analizy wydajności bazy danych](./media/intelligent-insights-overview/intelligent-insights-concept.png)

Metryki używane do mierzenia i wykrywania problemów z wydajnością bazy danych są oparte na czasie trwania zapytania, żądaniach limitu czasu, nadmiernym czasie oczekiwania i błędnych żądaniach. Aby uzyskać więcej informacji na temat metryk, zobacz [metryki wykrywania](#detection-metrics).

Określone obniżenie wydajności bazy danych są rejestrowane w dzienniku SQLInsights z inteligentnymi wpisami, które składają się z następujących właściwości:

| Właściwość | Szczegóły |
| :------------------- | ------------------- |
| Informacje o bazie danych | Metadane dotyczące bazy danych, w której wykryto szczegółowe dane, takie jak identyfikator URI zasobu. |
| Zaobserwowany zakres czasu | Godzina rozpoczęcia i zakończenia okresu wykrytego wglądu w szczegółowe dane. |
| Metryki, których dotyczy problem | Metryki, które spowodowały wygenerowanie szczegółowych informacji: <ul><li>Zwiększenie czasu trwania zapytania [s].</li><li>Nadmierne oczekiwanie [s].</li><li>Przekroczono limit czasu żądań [wartość procentowa].</li><li>Żądania z błędami [wartość procentowa].</li></ul>|
| Wartość wpływu | Wartość zmierzonej metryki. |
| Zapytania i kody błędów, których dotyczy problem | Wartość skrótu zapytania lub kod błędu. Mogą one być używane do łatwego skorelowania z zapytaniami, których to dotyczy. Podawane są metryki, które składają się z wzrostu czasu trwania zapytania, czasu oczekiwania, liczby limitów czasu lub kodów błędów. |
| Wykrycia | Wykrywanie wykryte w bazie danych w czasie trwania zdarzenia. Istnieją 15 wzorców wykrywania. Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów z wydajnością bazy danych przy użyciu Intelligent Insights](intelligent-insights-troubleshoot-performance.md). |
| Analiza głównej przyczyny | Analiza głównej przyczyny problemu zidentyfikowanego w formacie do odczytu przez człowieka. Niektóre szczegółowe informacje mogą zawierać zalecenia dotyczące poprawy wydajności, jeśli jest to możliwe. |
|||

Aby dowiedzieć się więcej na temat korzystania z Intelligent Insights z Azure SQL Analytics i dla typowych scenariuszy użycia, zobacz ten film wideo:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Get-Intelligent-Insights-for-Improving-Azure-SQL-Database-Performance/player]
>

Intelligent Insights ułatwia odnajdywanie i rozwiązywanie problemów z wydajnością bazy danych. Aby można było używać Intelligent Insights do rozwiązywania problemów z wydajnością bazy danych, zobacz [Rozwiązywanie problemów z wydajnością w Intelligent Insights](intelligent-insights-troubleshoot-performance.md).

## <a name="intelligent-insights-options"></a>Opcje Intelligent Insights

Dostępne opcje Intelligent Insights są następujące:

| Opcja Intelligent Insights | Obsługa Azure SQL Database | Obsługa wystąpień zarządzanych Azure SQL |
| :----------------------------- | ----- | ----- |
| **Skonfiguruj Intelligent Insights** — Skonfiguruj Intelligent Insights analizę dla baz danych. | Tak | Tak |
| Usługi **Stream Insights do Azure SQL Analytics** — szczegółowe informacje o usłudze Stream Azure SQL Analytics. | Tak | Tak |
| **Usługa Stream Insights w usłudze Azure Event Hubs** — szczegółowe informacje o usłudze Stream Event Hubs w celu uzyskania dalszych integracji niestandardowych. | Tak | Tak |
| **Usługa Stream Insights w usłudze Azure Storage** — szczegółowe informacje o usłudze Stream w usłudze Azure Storage w celu przeprowadzenia dalszej analizy i długoterminowej archiwizacji. | Tak | Tak |

> [!NOTE]
> Usługa Intelligent Insights to funkcja w wersji zapoznawczej, która nie jest dostępna w następujących regionach: Europa Zachodnia, Europa Północna, zachodnie stany USA 1 i Wschodnie stany USA 1.

## <a name="configure-the-export-of-the-intelligent-insights-log"></a>Konfigurowanie eksportu dziennika Intelligent Insights

Dane wyjściowe Intelligent Insights mogą być przesyłane strumieniowo do jednego z kilku miejsc docelowych na potrzeby analizy:

- Dane wyjściowe przesyłane strumieniowo do obszaru roboczego Log Analytics mogą być używane z [Azure SQL Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-sql) do wyświetlania szczegółowych informacji za pomocą interfejsu użytkownika Azure Portal. Jest to zintegrowane rozwiązanie platformy Azure i najbardziej typowy sposób wyświetlania szczegółowych informacji.
- Dane wyjściowe przesyłane strumieniowo do usługi Azure Event Hubs mogą służyć do tworzenia niestandardowych scenariuszy monitorowania i zgłaszania alertów
- Dane wyjściowe przesyłane strumieniowo do usługi Azure Storage mogą służyć do tworzenia niestandardowych aplikacji, takich jak raportowanie niestandardowe, długoterminowe archiwizowanie danych i tak dalej.

Integracja Azure SQL Analytics, platformy Azure Event Hubs, usługi Azure Storage lub produktów innych firm w celu ich użycia odbywa się przy pierwszym włączeniu rejestrowania Intelligent Insights (Dziennik "SQLInsights") w bloku ustawień diagnostycznych bazy danych, a następnie skonfigurować dane dziennika Intelligent Insights do przesyłania strumieniowego do jednego z tych miejsc docelowych.

Aby uzyskać więcej informacji na temat włączania rejestrowania Intelligent Insights i konfigurowania metryk i danych dzienników zasobów do przesyłania strumieniowego do produktu zużywanego, zobacz [metryki i rejestrowanie diagnostyki](metrics-diagnostic-telemetry-logging-streaming-export-configure.md).

### <a name="set-up-with-azure-sql-analytics"></a>Konfiguracja przy użyciu Azure SQL Analytics

Rozwiązanie Azure SQL Analytics zapewnia graficznego interfejsu użytkownika, raportowania i funkcji alertów dotyczących wydajności bazy danych przy użyciu danych dziennika zasobów Intelligent Insights.

Dodaj Azure SQL Analytics do pulpitu nawigacyjnego Azure Portal z portalu Marketplace i aby utworzyć obszar roboczy, zobacz [konfigurowanie Azure SQL Analytics](../../azure-monitor/insights/azure-sql.md#configuration)

Aby użyć Intelligent Insights z Azure SQL Analytics, skonfiguruj dane dziennika Intelligent Insights do przesyłania strumieniowego do obszaru roboczego Azure SQL Analytics utworzonego w poprzednim kroku, zobacz [metryki i rejestrowanie diagnostyki](metrics-diagnostic-telemetry-logging-streaming-export-configure.md).

Poniższy przykład pokazuje Intelligent Insights oglądany przez Azure SQL Analytics:

![Raport Intelligent Insights](./media/intelligent-insights-overview/intelligent-insights-azure-sql-analytics.png)

### <a name="set-up-with-event-hubs"></a>Konfiguracja przy użyciu Event Hubs

Aby użyć Intelligent Insights z Event Hubs, skonfiguruj dane dziennika Intelligent Insights do przesyłania strumieniowego do Event Hubs, zobacz [metryki i rejestrowanie diagnostyki](metrics-diagnostic-telemetry-logging-streaming-export-configure.md) oraz [przesyłanie strumieniowe dzienników diagnostyki platformy Azure do Event Hubs](../../azure-monitor/platform/resource-logs-stream-event-hubs.md).

Aby użyć Event Hubs do konfigurowania niestandardowego monitorowania i generowania alertów, zobacz [co zrobić z metrykami i dziennikami diagnostycznymi w Event Hubs](metrics-diagnostic-telemetry-logging-streaming-export-configure.md#what-to-do-with-metrics-and-resource-logs-in-event-hubs).

### <a name="set-up-with-azure-storage"></a>Konfigurowanie za pomocą usługi Azure Storage

Aby użyć Intelligent Insights z magazynem, skonfiguruj dane dziennika Intelligent Insights do przesyłania strumieniowego do magazynu, zobacz temat [metryki i rejestrowanie diagnostyki](metrics-diagnostic-telemetry-logging-streaming-export-configure.md) oraz [przesyłanie strumieniowe do usługi Azure Storage](metrics-diagnostic-telemetry-logging-streaming-export-configure.md#stream-into-azure-storage).

### <a name="custom-integrations-of-intelligent-insights-log"></a>Niestandardowa integracja dziennika Intelligent Insights

Aby użyć Intelligent Insights z narzędziami innych firm lub dla niestandardowego tworzenia alertów i monitorowania, zobacz [Korzystanie z dziennika diagnostyki wydajności bazy danych Intelligent Insights](intelligent-insights-use-diagnostics-log.md).

## <a name="detection-metrics"></a>Metryki wykrywania

Metryki używane na potrzeby modeli wykrywania generujących Intelligent Insights są oparte na monitorowaniu:

- Czas trwania zapytania
- Żądania limitu czasu
- Nadmierny czas oczekiwania
- Błędne żądania

Czas trwania zapytania i żądania przekroczenia limitu czasu są używane jako modele podstawowe w wykrywaniu problemów z wydajnością obciążenia bazy danych. Są one używane, ponieważ bezpośrednio mierzą, co dzieje się z obciążeniem. Aby wykryć wszystkie możliwe przypadki obniżenia wydajności obciążeń, nadmierny czas oczekiwania i błędy żądania są używane jako dodatkowe modele wskazujące problemy, które mają wpływ na wydajność obciążeń.

System automatycznie rozważa zmiany obciążenia i zmiany liczby żądań zapytań wykonywanych w bazie danych, aby dynamicznie określić normalne i nieaktualne progi wydajności bazy danych.

Wszystkie metryki są brane pod uwagę w różnych relacjach za pomocą ogólnie wykrytych modeli danych. Informacje dostępne za poorednictwem usługi Intelligent Insights obejmują:

- Szczegóły wykrytego problemu z wydajnością.
- Wykryto analizę głównej przyczyny problemu.
- Zalecenia dotyczące poprawy wydajności monitorowanej bazy danych, jeśli jest to możliwe.

## <a name="query-duration"></a>Czas trwania zapytania

Model rozkładu czasu trwania zapytania analizuje poszczególne zapytania i wykrywa wzrost czasu potrzebny do skompilowania i wykonania zapytania w porównaniu z linią bazową wydajności.

Jeśli wbudowana analiza wykryje znaczny wzrost czasu wykonywania zapytania lub tworzenia zapytania, który wpływa na wydajność obciążeń, te zapytania są oflagowane jako problemy z obniżeniem wydajności w czasie wykonywania zapytania.

W dzienniku diagnostyki Intelligent Insights są wyprowadzane wyniki zapytania o nieprawidłowej wydajności. Wartość skrótu zapytania wskazuje, czy spadek wydajności został powiązany z wzrostem czasu kompilacji lub wykonania zapytania, który zwiększył czas trwania zapytania.

## <a name="timeout-requests"></a>Żądania limitu czasu

Model obniżenia poziomu żądań przeanalizuje poszczególne zapytania i wykrywa wzrost limitu czasu na poziomie wykonywania zapytania oraz ogólne limity czasu żądania na poziomie bazy danych w porównaniu z okresem bazowym wydajności.

Niektóre zapytania mogą przekroczyć limit czasu przed osiągnięciem etapu wykonania. Za pomocą środków przerwanych procesów roboczych w porównaniu z żądaniami, wbudowana miara analizy i analizuje wszystkie zapytania, które osiągnęły bazę danych, niezależnie od tego, czy odnoszą się do etapu wykonywania.

Po upływie liczby przekroczeń limitu czasu dla wykonywanych zapytań lub liczby przerwanych procesów roboczych przekracza próg zarządzany przez system, dziennik diagnostyczny jest wypełniany inteligentnym wglądem w szczegółowe dane.

Wygenerowane szczegółowe dane zawierają liczbę żądań, które przekroczyły limit czasu i liczbę zapytań, które przekroczyły limit czasu. Wskazanie obniżenia wydajności jest związane z zwiększeniem limitu czasu na etapie wykonywania lub podano ogólny poziom bazy danych. Gdy wzrost limitu czasu jest uznawany za istotny dla wydajności bazy danych, te zapytania są oflagowane jako problemy z obniżeniem wydajności.

## <a name="excessive-wait-times"></a>Nadmierny czas oczekiwania

Model czasu nadmiarowego oczekiwania monitoruje poszczególne zapytania bazy danych. Wykrywa nietypowo wysokie statystyki oczekiwania na zapytania, które przekroczyły progi bezwzględne zarządzane przez system. Następujące zapytania nadmiernie nadmiarowe metryki czasu oczekiwania są zaobserwowane przy użyciu, [Statystyka oczekiwania magazynu zapytań (sys.query_store_wait_stats)](/sql/relational-databases/system-catalog-views/sys-query-store-wait-stats-transact-sql):

- Osiąganie limitów zasobów
- Osiągnięcie limitów zasobów puli elastycznej
- Nadmierna liczba wątków procesów roboczych lub sesji
- Nadmierne blokowanie bazy danych
- Wykorzystanie pamięci
- Inne statystyki oczekiwania

Osiągnięcie limitów zasobów lub limitów zasobów puli elastycznej oznacza, że zużycie zasobów w ramach subskrypcji lub elastycznej puli przekracza progi bezwzględne. Te statystyki wskazują spadek wydajności obciążeń. Nadmierna liczba wątków procesów roboczych i sesji oznacza warunek, w którym liczba wątków roboczych lub sesji została zainicjowana z przekroczeniem progów bezwzględnych. Te statystyki wskazują spadek wydajności obciążeń.

Nadmierne blokowanie bazy danych oznacza warunek, w którym liczba blokad w bazie danych ma przekroczenie progów bezwzględnych. Ten stan wskazuje na spadek wydajności obciążeń. Wykorzystanie pamięci to warunek, w którym liczba wątków żądających przydzielenia pamięci przekroczy próg absolutny. Ten stan wskazuje na spadek wydajności obciążeń.

Inne wykrywanie statystyk oczekiwania wskazuje warunek, w którym różne metryki mierzone za pomocą magazynu zapytań Statystyka zaczekałą próg bezwzględny. Te statystyki wskazują spadek wydajności obciążeń.

Po wykryciu nadmiernych czasów oczekiwania, w zależności od dostępnych danych, dane wyjściowe w dzienniku diagnostyki Intelligent Insights mogą wynikać z nieprawidłowej wydajności i zamierzonych zapytań, szczegóły metryk, które powodują oczekiwanie na wykonanie zapytań i mierzoną wartość czasu oczekiwania.

## <a name="errored-requests"></a>Błędne żądania

W przypadku błędnych żądań model obniżenia wydajności monitoruje poszczególne zapytania i wykrywa wzrost liczby zapytań, które zostały błędnie porównane z okresem bazowym. Ten model monitoruje również krytyczne wyjątki, które przekroczyły progi bezwzględne zarządzane przez wbudowaną analizę. System automatycznie traktuje liczbę żądań zapytań skierowanych do bazy danych i kont dla wszystkich zmian obciążenia w monitorowanym okresie.

Gdy mierzony wzrost liczby nieudanych żądań względem ogólnej ilości wykonywanych żądań jest uznawany za znaczący dla wydajności obciążenia, uwzględnione zapytania są oflagowane jako błędne żądania spadek wydajności.

W dzienniku Intelligent Insights są wyprowadzane liczba błędnych żądań. Wskazuje, czy spadek wydajności został związany ze wzrostem liczby błędnych żądań lub przekroczeniem monitorowanego progu wyjątku krytycznego i mierzonego czasu obniżenia wydajności.

Jeśli którykolwiek z monitorowanych wyjątków krytycznych przekroczy progi bezwzględne zarządzane przez system, usługa inteligentnego wglądu jest generowana ze szczegółowymi informacjami o wyjątkach.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się [, jak monitorować bazy danych za pomocą usługi SQL Analytics](../../azure-monitor/insights/azure-sql.md).
- Dowiedz się, jak [rozwiązywać problemy z wydajnością za pomocą Intelligent Insights](intelligent-insights-troubleshoot-performance.md).
