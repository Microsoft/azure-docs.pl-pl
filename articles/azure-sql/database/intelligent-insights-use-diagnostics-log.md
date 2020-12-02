---
title: Dziennik diagnostyki wydajności Intelligent Insights
description: Intelligent Insights udostępnia dziennik diagnostyki Azure SQL Database i problemów z wydajnością wystąpienia zarządzanego usługi Azure SQL
services: sql-database
ms.service: sql-db-mi
ms.subservice: performance
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: wiassaf, sstein
ms.date: 06/12/2020
ms.openlocfilehash: 6720058ce6e7614284111a75a2ab3a91525df1a3
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96488769"
---
# <a name="use-the-intelligent-insights-performance-diagnostics-log-of-azure-sql-database-and-azure-sql-managed-instance-performance-issues"></a>Korzystanie z dziennika diagnostyki wydajności Intelligent Insights Azure SQL Database i problemów z wydajnością wystąpienia zarządzanego usługi Azure SQL
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Ta strona zawiera informacje dotyczące sposobu korzystania z dziennika diagnostyki wydajności wygenerowanego przez [Intelligent Insights](intelligent-insights-overview.md) Azure SQL Database i problemów z wydajnością wystąpienia zarządzanego usługi Azure SQL, jego formatu i zawartych w nim danych na potrzeby niestandardowych potrzeb deweloperskich. Ten dziennik diagnostyki można wysłać do [Azure monitor dzienników](../../azure-monitor/insights/azure-sql.md), [platformy Azure Event Hubs](../../azure-monitor/platform/resource-logs.md#send-to-azure-event-hubs), [usługi Azure Storage](metrics-diagnostic-telemetry-logging-streaming-export-configure.md#stream-into-azure-storage)lub rozwiązania innej firmy w celu uzyskania niestandardowych funkcji alertów DevOps i raportowania.

> [!NOTE]
> Usługa Intelligent Insights to funkcja w wersji zapoznawczej, która nie jest dostępna w następujących regionach: Europa Zachodnia, Europa Północna, zachodnie stany USA 1 i Wschodnie stany USA 1.

## <a name="log-header"></a>Nagłówek dziennika

Dziennik diagnostyczny używa standardowego formatu JSON do danych wyjściowych Intelligent Insights. Właściwość dokładnej kategorii służąca do uzyskiwania dostępu do dziennika Intelligent Insights jest stałą wartością "SQLInsights".

Nagłówek dziennika jest typowy i składa się z sygnatury czasowej (TimeGenerated), która pokazuje, kiedy wpis został utworzony. Zawiera również identyfikator zasobu (ResourceId) odnoszący się do konkretnej bazy danych, do której odnosi się wpis. Kategoria (kategoria), poziom (poziom) i nazwa operacji (OperationName) są stałymi właściwościami, których wartości nie zmieniają się. Wskazują one, że wpis dziennika jest informacyjny i pochodzi z Intelligent Insights (SQLInsights).

```json
"TimeGenerated" : "2017-9-25 11:00:00", // time stamp of the log entry
"ResourceId" : "database identifier", // value points to a database resource
"Category": "SQLInsights", // fixed property
"Level" : "Informational", // fixed property
"OperationName" : "Insight", // fixed property
```

## <a name="issue-id-and-database-affected"></a>Identyfikator problemu i baza danych, których to dotyczy

Właściwość Identyfikacja problemu (issueId_d) umożliwia unikatowe Śledzenie problemów z wydajnością do momentu rozwiązania problemu. Wiele rekordów zdarzeń w stanie raportowania dziennika tego samego problemu będzie mieć ten sam identyfikator problemu.

Wraz z IDENTYFIKATORem problemu dziennik diagnostyczny zgłasza sygnatury czasowe uruchamiania (intervalStartTime_t) i końca (intervalEndTme_t) określonego zdarzenia związanego z problemem raportowanym w dzienniku diagnostyki.

Właściwość puli elastycznej (elasticPoolName_s) wskazuje, do której puli elastycznej znajduje się baza danych, do której należy problem. Jeśli baza danych nie jest częścią puli elastycznej, ta właściwość nie ma wartości. Baza danych, w której wykryto problem, jest ujawniana w właściwości Nazwa bazy danych (databaseName_s).

```json
"intervalStartTime_t": "2017-9-25 11:00", // start of the issue reported time stamp
"intervalEndTme_t":"2017-9-25 12:00", // end of the issue reported time stamp
"elasticPoolName_s" : "", // resource elastic pool (if applicable)
"databaseName_s" : "db_name", // database name
"issueId_d" : 1525, // unique ID of the issue detected
"status_s" : "Active" // status of the issue – possible values: "Active", "Verifying", and "Complete"
```

## <a name="detected-issues"></a>Wykryte problemy

Następna sekcja dziennika wydajności Intelligent Insights zawiera problemy z wydajnością, które zostały wykryte za pomocą wbudowanej sztucznej analizy. Wykrycia są ujawniane we właściwościach w dzienniku diagnostyki JSON. Te wykrycia obejmują kategorię problemu, wpływ problemu, wpływ na zapytania i metryki. Właściwości wykrywania mogą zawierać wiele wykrytych problemów z wydajnością.

Wykryte problemy z wydajnością są raportowane przy użyciu następującej struktury właściwości wykrywania:

```json
"detections_s" : [{
"impact" : 1 to 3, // impact of the issue detected, possible values 1-3 (1 low, 2 moderate, 3 high impact)
"category" : "Detectable performance pattern", // performance issue detected, see the table
"details": <Details outputted> // details of an issue (see the table)
}]
```

Możliwe do wykrycia wzorce wydajności i szczegółowe informacje, które zostały wprowadzone do dziennika diagnostycznego, znajdują się w poniższej tabeli.

### <a name="detection-category"></a>Kategoria wykrywania

Właściwość Category (kategoria) opisuje kategorię wykrywalnych wzorców wydajności. W poniższej tabeli przedstawiono wszystkie możliwe kategorie wykrywalnych wzorców wydajności. Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów z wydajnością bazy danych przy użyciu Intelligent Insights](intelligent-insights-troubleshoot-performance.md).

W zależności od wykrytego problemu z wydajnością szczegóły, które zostały wygenerowane w pliku dziennika diagnostycznego, różnią się odpowiednio.

| Wykrywalne wzorce wydajności | Szczegóły zostały wydane |
| :------------------- | ------------------- |
| Osiąganie limitów zasobów | <li>Uwzględnione zasoby</li><li>Skróty zapytań</li><li>Procent użycia zasobów</li> |
| Wzrost obciążenia | <li>Liczba zapytań, których wykonywanie zostało zwiększone</li><li>Skróty zapytań o największym udziale do wzrostu obciążenia</li> |
| Wykorzystanie pamięci | <li>Pracownik pamięci</li> |
| Blokowanie | <li>Zmodyfikowane skróty zapytań</li><li>Blokowanie skrótów zapytań</li> |
| Zwiększono MAXDOP | <li>Skróty zapytań</li><li>Czas oczekiwania CXP</li><li>Czas oczekiwania</li> |
| Rywalizacja o PAGELATCH | <li>Skróty zapytań powodujące rywalizację</li> |
| Brakujący indeks | <li>Skróty zapytań</li> |
| Nowe zapytanie | <li>Skrót zapytania dla nowych zapytań</li> |
| Statystyka nietypowych oczekiwań | <li>Nietypowe typy oczekiwania</li><li>Skróty zapytań</li><li>Czasy oczekiwania zapytania</li> |
| Rywalizacja o bazę danych TempDB | <li>Skróty zapytań powodujące rywalizację</li><li>Wysyłanie zapytań do ogólnej bazy danych PAGELATCH czas oczekiwania [%]</li> |
| Niedobory jednostek DTU puli elastycznej | <li>Pula elastyczna</li><li>Najpopularniejsza baza danych zużywająca wartość DTU</li><li>Procent jednostek DTU puli używanych przez najwyższego konsumenta</li> |
| Regresja planu | <li>Skróty zapytań</li><li>Dobre identyfikatory planu</li><li>Złe identyfikatory planu</li> |
| Zmiana wartości konfiguracji Database-Scoped | <li>Zmiany konfiguracji w zakresie bazy danych w porównaniu z wartościami domyślnymi</li> |
| Powolne klienta | <li>Skróty zapytań</li><li>Czas oczekiwania</li> |
| Obniżenie warstwy cenowej | <li>Powiadomienie tekstowe</li> |

### <a name="impact"></a>Wpływ

Właściwość wpływ (wpływ) opisuje, jak wiele wykrytych zachowań jest przyczyną problemu związanego z bazą danych. Ma wpływ na zakres od 1 do 3 z 3 jako najwyższy udział, 2 jako umiarkowany i 1 jako najniższy udział. Wartość wpływu może być używana jako dane wejściowe dla niestandardowej automatyzacji alertów, w zależności od konkretnych potrzeb. Zapytania dotyczące właściwości, których to dotyczy (QueryHashes), zapewniają listę skrótów zapytań, których dotyczyło określone wykrywanie.

### <a name="impacted-queries"></a>Zakierowane zapytania

Następna sekcja dziennika Intelligent Insights zawiera informacje o konkretnych zapytaniach, na które miały wpływ wykryte problemy z wydajnością. Te informacje są ujawniane jako tablica obiektów osadzonych we właściwości impact_s. Właściwość wpływ zawiera jednostki i metryki. Jednostki odwołują się do określonego zapytania (typ: zapytanie). Unikatowy skrót zapytania jest ujawniany w obszarze właściwości wartość (wartość). Ponadto każda z tych zapytań zawiera metrykę i wartość, która wskazuje na wykryty problem z wydajnością.

W poniższym przykładzie dziennika wykryto, że zapytanie z 0x9102EXZ4em skrótu zostało zwiększone przez czas wykonywania (Metric: DurationIncreaseSeconds). Wartość 110 sekund oznacza, że wykonywanie danego zapytania zajęło 110 sekund dłużej. Ponieważ można wykryć wiele zapytań, ta sekcja dziennika może zawierać wiele wpisów zapytań.

```json
"impact" : [{
"entity" : {
"Type" : "Query", // type of entity - query
"Value" : "query hash value", // for example "0x9102EXZ4" query hash value },
"Metric" : "DurationIncreaseSeconds", // measured metric and the measurement unit (in this case seconds)
"Value" : 110 // value of the measured metric (in this case seconds)
}]
```

### <a name="metrics"></a>Metryki

Jednostka miary dla każdej raportowanej metryki jest podana we właściwości Metryka (Metryka) z możliwymi wartościami sekund, liczba i procent. Wartość zmierzonej metryki jest raportowana we właściwości wartość (wartość).

Właściwość DurationIncreaseSeconds dostarcza jednostkę miary w sekundach. Jednostka miary CriticalErrorCount jest liczbą, która reprezentuje liczbę błędów.

```json
"metric" : "DurationIncreaseSeconds", // issue metric type – possible values: DurationIncreaseSeconds, CriticalErrorCount, WaitingSeconds
"value" : 102 // value of the measured metric (in this case seconds)
```

## <a name="root-cause-analysis-and-improvement-recommendations"></a>Zalecenia dotyczące analizy głównej przyczyny i ulepszeń

Ostatnia część dziennika wydajności Intelligent Insights odnosi się do zautomatyzowanej analizy głównej przyczyny problemu z obniżeniem wydajności. Informacje są wyświetlane w otaczające przyjaznym dla ludzi w właściwości analiza przyczyny głównej (rootCauseAnalysis_s). Zalecenia dotyczące ulepszeń są zawarte w dzienniku, jeśli jest to możliwe.

```json
// example of reported root cause analysis of the detected performance issue, in a human-readable format

"rootCauseAnalysis_s" : "High data IO caused performance to degrade. It seems that this database is missing some indexes that could help."
```

Można użyć dziennika wydajności Intelligent Insights z [dziennikami Azure monitor]( https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-sql) lub rozwiązania innych firm do obsługi alertów niestandardowych DevOps i funkcji raportowania.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej na temat pojęć [Intelligent Insights](intelligent-insights-overview.md) .
- Dowiedz się, jak [rozwiązywać problemy z wydajnością za pomocą Intelligent Insights](intelligent-insights-troubleshoot-performance.md).
- Dowiedz się [, jak monitorować problemy z wydajnością za pomocą Azure SQL Analytics](../../azure-monitor/insights/azure-sql.md).
- Dowiedz się [, jak zbierać dane dzienników z zasobów platformy Azure i korzystać z nich](../../azure-monitor/platform/platform-logs-overview.md).