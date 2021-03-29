---
title: Zbieranie danych dziennika
titleSuffix: Azure Cognitive Search
description: Zbieranie i analizowanie danych dziennika przez włączenie ustawienia diagnostycznego, a następnie użycie języka zapytań Kusto do eksplorowania wyników.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: 3c8dd5cd9da2fd1e741635a6471c0662066d147e
ms.sourcegitcommit: dae6b628a8d57540263a1f2f1cdb10721ed1470d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "105709943"
---
# <a name="collect-and-analyze-log-data-for-azure-cognitive-search"></a>Zbieranie i analizowanie danych dziennika dla usługi Azure Wyszukiwanie poznawcze

Dzienniki diagnostyczne lub operacyjne zapewniają wgląd w szczegółowe operacje na platformie Azure Wyszukiwanie poznawcze i są przydatne do monitorowania procesów usług i obciążeń. Wewnętrznie firma Microsoft zachowuje informacje o systemie w zasobie przez krótki czas (około 30 dni), co jest wystarczające do badania i analizy w przypadku utworzenia biletu pomocy technicznej. Jeśli jednak chcesz mieć własność przez dane operacyjne, należy skonfigurować ustawienie diagnostyczne, aby określić, gdzie zbierane są informacje o rejestrowaniu.

Rejestrowanie diagnostyczne jest włączane w ramach integracji z [Azure monitor](../azure-monitor/index.yml). 

Podczas konfigurowania rejestrowania diagnostycznego zostanie wyświetlony monit o określenie mechanizmu magazynu. W poniższej tabeli przedstawiono opcje zbierania i utrwalania danych.

| Zasób | Sposób użycia |
|----------|----------|
| [Wysyłanie do obszaru roboczego usługi Log Analytics](../azure-monitor/essentials/tutorial-resource-logs.md) | Zdarzenia i metryki są wysyłane do obszaru roboczego Log Analytics, w którym można wykonywać zapytania w portalu w celu zwrócenia szczegółowych informacji. Aby zapoznać się z wprowadzeniem, zobacz Rozpoczynanie [pracy z dziennikami Azure monitor](../azure-monitor/logs/log-analytics-tutorial.md) |
| [Archiwizowanie przy użyciu magazynu obiektów BLOB](../storage/blobs/storage-blobs-overview.md) | Zdarzenia i metryki są archiwizowane w kontenerze obiektów blob i przechowywane w plikach JSON. Dzienniki mogą być bardzo szczegółowe (przez godzinę/minutę), przydatne do przeszukiwania konkretnego zdarzenia, ale nie dla badania otwartego. Użyj edytora JSON, aby wyświetlić Nieprzetworzony plik dziennika lub Power BI do agregowania i wizualizacji danych dziennika.|
| [Przesyłanie strumieniowe do centrum zdarzeń](../event-hubs/index.yml) | Zdarzenia i metryki są przesyłane strumieniowo do usługi Event Hubs platformy Azure. Wybierz tę opcję jako alternatywną usługę zbierania danych dla bardzo dużych dzienników. |

## <a name="prerequisites"></a>Wymagania wstępne

Utwórz zasoby z wyprzedzeniem, aby można było wybrać co najmniej jeden podczas konfigurowania rejestrowania diagnostycznego.

+ [Tworzenie obszaru roboczego usługi log Analytics](../azure-monitor/logs/quick-create-workspace.md)

+ [Tworzenie konta magazynu](../storage/common/storage-account-create.md)

+ [Tworzenie centrum zdarzeń](../event-hubs/event-hubs-create.md)

## <a name="enable-data-collection"></a>Włączanie zbierania danych

Ustawienia diagnostyczne określają, jak są zbierane zdarzenia i metryki.

1. W obszarze **Monitorowanie** wybierz pozycję **Ustawienia diagnostyczne**.

   ![Ustawienia diagnostyczne](./media/search-monitor-usage/diagnostic-settings.png "Ustawienia diagnostyczne")

1. Wybierz **+ Dodaj ustawienie diagnostyczne**

1. Zaznacz **log Analytics**, wybierz obszar roboczy, a następnie wybierz pozycję **OperationLogs** i **AllMetrics**.

   ![Konfigurowanie zbierania danych](./media/search-monitor-usage/configure-storage.png "Konfigurowanie zbierania danych")

1. Zapisz ustawienie.

1. Po włączeniu rejestrowania Użyj usługi wyszukiwania, aby rozpocząć generowanie dzienników i metryk. To zajmie czas przed udostępnieniem zarejestrowanych zdarzeń i metryk.

W przypadku Log Analytics będzie to kilka minut, zanim dane będą dostępne, a następnie można uruchomić zapytania Kusto w celu zwrócenia danych. Aby uzyskać więcej informacji, zobacz [monitorowanie żądań zapytań](search-monitor-logs.md).

W przypadku usługi BLOB Storage trwa jedna godzina, zanim kontenery pojawią się w usłudze BLOB Storage. Dla każdego kontenera istnieje jeden obiekt BLOB o godzinie. Kontenery są tworzone tylko w przypadku działania do rejestrowania lub mierzenia. Po skopiowaniu danych na konto magazynu dane są formatowane jako kod JSON i umieszczane w dwóch kontenerach:

+ Insights-Logs-operationlogs: w przypadku dzienników ruchu wyszukiwania
+ Insights-Metrics-pt1m: for Metrics

## <a name="query-log-information"></a>Informacje dziennika zapytania

Dwie tabele zawierają dzienniki i metryki dla usługi Azure Wyszukiwanie poznawcze: **AzureDiagnostics** i **AzureMetrics**.

1. W obszarze **monitorowanie** wybierz pozycję **dzienniki**.

1. W oknie zapytania wprowadź **AzureMetrics** . Uruchom to proste zapytanie, aby zapoznać się z danymi zebranymi w tej tabeli. Przewiń tabelę, aby wyświetlić metryki i wartości. Zwróć uwagę na liczbę rekordów u góry, a jeśli usługa zbiera metryki przez pewien czas, możesz chcieć dostosować przedział czasu, aby uzyskać dostęp do możliwego do zarządzania zestawu danych.

   ![Tabela AzureMetrics](./media/search-monitor-usage/azuremetrics-table.png "Tabela AzureMetrics")

1. Wprowadź następujące zapytanie, aby zwrócić tabelaryczny zestaw wyników.

   ```kusto
   AzureMetrics
   | project MetricName, Total, Count, Maximum, Minimum, Average
   ```

1. Powtórz poprzednie kroki, rozpoczynając od **AzureDiagnostics** , aby zwrócić wszystkie kolumny do celów informacyjnych, a następnie bardziej selektywne zapytanie, które wyodrębnia bardziej interesujące informacje.

   ```kusto
   AzureDiagnostics
   | project OperationName, resultSignature_d, DurationMs, Query_s, Documents_d, IndexName_s
   | where OperationName == "Query.Search" 
   ```

   ![Tabela AzureDiagnostics](./media/search-monitor-usage/azurediagnostics-table.png "Tabela AzureDiagnostics")

## <a name="kusto-query-examples"></a>Przykłady zapytań Kusto

Jeśli włączono rejestrowanie diagnostyczne, można wysłać zapytanie do **AzureDiagnostics** , aby uzyskać listę operacji uruchomionych w usłudze i kiedy. Możesz również skorelować działanie, aby zbadać zmiany wydajności.

#### <a name="example-list-operations"></a>Przykład: operacje na liście 

Zwróć listę operacji i liczbę każdej z nich.

```kusto
AzureDiagnostics
| summarize count() by OperationName
```

#### <a name="example-correlate-operations"></a>Przykład: skorelowanie operacji

Skorelować żądanie zapytania z operacjami indeksowania i Renderuj punkty danych na wykresie czasu, aby zobaczyć operacje, które się pokrywają.

```kusto
AzureDiagnostics
| summarize OperationName, Count=count()
| where OperationName in ('Query.Search', 'Indexing.Index')
| summarize Count=count(), AvgLatency=avg(DurationMs) by bin(TimeGenerated, 1h), OperationName
| render timechart
```

## <a name="logged-operations"></a>Zarejestrowane operacje

Zarejestrowane zdarzenia przechwycone przez Azure Monitor obejmują te związane z indeksowaniem i zapytaniami. Tabela **AzureDiagnostics** w log Analytics zbiera dane operacyjne powiązane z zapytaniami i indeksowanie.

| OperationName | Opis |
|---------------|-------------|
| Servicestatystyka | Ta operacja to rutynowe wywołanie [pobierania statystyk usługi](/rest/api/searchservice/get-service-statistics), wywoływane bezpośrednio lub niejawnie, aby wypełnić stronę omówienia portalu po jej załadowaniu lub odświeżeniu. |
| Zapytanie. Search |  Zażądaj zapytań względem indeksu, aby uzyskać informacje na temat zarejestrowanych zapytań, zobacz temat [monitorowanie zapytań](search-monitor-queries.md) .|
| Indeksowanie. index  | Ta operacja jest wywołaniem do [dodawania, aktualizowania lub usuwania dokumentów](/rest/api/searchservice/addupdate-or-delete-documents). |
| zwiększa. Prototyp | Jest to indeks utworzony przez Kreatora importu danych. |
| Indeksatory. Utwórz | Utwórz indeksator jawnie lub niejawnie za pomocą Kreatora importu danych. |
| Indeksatory. Get | Zwraca nazwę indeksatora za każdym razem, gdy indeksator jest uruchomiony. |
| Indeksatory. stan | Zwraca stan indeksatora za każdym razem, gdy indeksator jest uruchomiony. |
| Źródła danych. Pobierz | Zwraca nazwę źródła danych przy każdym uruchomieniu indeksatora.|
| Indeksy. Get | Zwraca nazwę indeksu za każdym razem, gdy indeksator jest uruchamiany. |

## <a name="log-schema"></a>Schemat dziennika

W przypadku tworzenia niestandardowych raportów struktury danych zawierające dane dziennika usługi Azure Wyszukiwanie poznawcze są zgodne ze schematem poniżej. W przypadku usługi BLOB Storage każdy obiekt BLOB ma jeden element główny o nazwie **Records** zawierający tablicę obiektów dziennika. Każdy obiekt BLOB zawiera rekordy dla wszystkich operacji, które miały miejsce w danej godzinie.

Poniższa tabela jest częściową listą pól wspólnych dla rejestrowania zasobów.

| Nazwa | Typ | Przykład | Uwagi |
| --- | --- | --- | --- |
| timeGenerated |datetime |"2018 R-12-07T00:00:43.6872559 Z" |Sygnatura czasowa operacji |
| resourceId |ciąg |"/SUBSCRIPTIONS/11111111-1111-1111-1111-111111111111/<br/>RESOURCEGROUPS/DOMYŚLNE/DOSTAWCY/<br/> Programu. SEARCH/SEARCHSERVICES/SEARCHSERVICE " |Identyfikator zasobu |
| operationName |ciąg |"Query. Search" |Nazwa operacji |
| operationVersion |ciąg |"2020-06-30" |Używana wersja interfejsu API |
| category |ciąg |"OperationLogs" | — stała |
| resultType |ciąg |Prawnego |Możliwe wartości: sukces lub niepowodzenie |
| resultSignature |int |200 |Kod wyniku HTTP |
| Milisekundach) |int |50 |Czas trwania operacji w milisekundach |
| properties |object |Zapoznaj się z poniższą tabelą. |Obiekt zawierający dane specyficzne dla operacji |

### <a name="properties-schema"></a>Schemat właściwości

Poniższe właściwości są specyficzne dla Wyszukiwanie poznawcze platformy Azure.

| Nazwa | Typ | Przykład | Uwagi |
| --- | --- | --- | --- |
| Description_s |ciąg |"GET/Indexes (" Content ")/docs" |Punkt końcowy operacji |
| Documents_d |int |42 |Liczba przetworzonych dokumentów |
| IndexName_s |ciąg |"test-index" |Nazwa indeksu skojarzonego z operacją |
| Query_s |ciąg |"? Search = AzureSearch&$count = true&API-Version = 2020-06-30" |Parametry zapytania |

## <a name="metrics-schema"></a>Schemat metryk

Metryki są przechwytywane dla żądań zapytań i mierzone w ciągu jednej minuty. Każda Metryka uwidacznia wartości minimalne, maksymalne i średnie na minutę. Aby uzyskać więcej informacji, zobacz [monitorowanie żądań zapytań](search-monitor-queries.md).

| Nazwa | Typ | Przykład | Uwagi |
| --- | --- | --- | --- |
| resourceId |ciąg |"/SUBSCRIPTIONS/11111111-1111-1111-1111-111111111111/<br/>RESOURCEGROUPS/DOMYŚLNE/DOSTAWCY/<br/>Programu. SEARCH/SEARCHSERVICES/SEARCHSERVICE " |Identyfikator zasobu |
| metricName |ciąg |Opóźnienie |Nazwa metryki |
| time |datetime |"2018 R-12-07T00:00:43.6872559 Z" |Sygnatura czasowa operacji |
| średnia |int |64 |Średnia wartość próbek pierwotnych w interwale czasu metryki, jednostki w sekundach lub procent, w zależności od metryki. |
| minimum |int |37 |Minimalna wartość próbek pierwotnych w interwale czasu metryki (w sekundach). |
| maksimum |int |78 |Maksymalna wartość próbek pierwotnych w interwale czasu metryki (w sekundach).  |
| suma |int |258 |Całkowita wartość próbek pierwotnych w interwale czasu metryki (w sekundach).  |
| count |int |4 |Liczba metryk emitowanych z węzła do dziennika w interwale jednominutowym.  |
| timegrain |ciąg |"PT1M" |Ziarno czasu metryki w ISO 8601. |

Jest to typowe dla zapytań wykonywanych w milisekundach, dlatego w metrykach, takich jak zapytań, będą wyświetlane tylko zapytania, które mierzą jako sekundy.

W przypadku metryk **kwerendy wyszukiwania na sekundę** minimalna wartość to najmniejsza liczba zapytań wyszukiwania na sekundę, które zostały zarejestrowane w tej minucie. Ta sama zasada dotyczy wartości maksymalnej. Średnia, to wartość zagregowana w całej minucie. Na przykład w ciągu jednej minuty może istnieć wzorzec podobny do tego: jedna sekunda wysokiego obciążenia, która jest maksymalną wartością dla SearchQueriesPerSecond, a następnie 58 sekund średniego obciążenia, a wreszcie jedna sekunda z tylko jednym zapytaniem, co jest minimalne.

W przypadku **kwerend wyszukiwania z ograniczeniami wartość procentowa**, minimum, maksimum, średnia i suma — wszystkie mają tę samą wartość: procent zapytań wyszukiwania, które zostały ograniczone, od łącznej liczby zapytań wyszukiwania w ciągu jednej minuty.

## <a name="view-raw-log-files"></a>Wyświetlanie nieprzetworzonych plików dziennika

Magazyn obiektów BLOB jest używany do archiwizowania plików dziennika. Aby wyświetlić plik dziennika, można użyć dowolnego edytora JSON. Jeśli go nie masz, zalecamy [Visual Studio Code](https://code.visualstudio.com/download).

1. W Azure Portal Otwórz konto magazynu. 

2. W okienku nawigacji po lewej stronie kliknij pozycję **obiekty blob**. Powinny być widoczne informacje **Insights-Logs-operationlogs** i **Insights-Metrics-pt1m**. Te kontenery są tworzone przez usługę Azure Wyszukiwanie poznawcze podczas eksportowania danych dziennika do magazynu obiektów BLOB.

3. Kliknij kolejno pozycje hierarchia folderów, aby uzyskać dostęp do pliku. JSON.  Użyj menu kontekstowego, aby pobrać plik.

Po pobraniu pliku Otwórz go w edytorze JSON, aby wyświetlić jego zawartość.

## <a name="next-steps"></a>Następne kroki

Jeśli jeszcze tego nie zrobiono, zapoznaj się z tematem monitorowanie usługi wyszukiwania, aby dowiedzieć się więcej na temat pełnego zakresu możliwości nadzoru.

> [!div class="nextstepaction"]
> [Monitoruj operacje i działania na platformie Azure Wyszukiwanie poznawcze](search-monitor-usage.md)