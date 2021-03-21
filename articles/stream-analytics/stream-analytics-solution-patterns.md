---
title: Wzorce rozwiązań usługi Azure Stream Analytics
description: Poznaj typowe wzorce rozwiązań dla Azure Stream Analytics, takie jak pulpity nawigacyjne, obsługa komunikatów zdarzeń, magazyny danych, Wzbogacanie danych referencyjnych i monitorowanie.
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.openlocfilehash: 1bd3c1099344bd266d7e3bc153613daaecfb412a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98020319"
---
# <a name="azure-stream-analytics-solution-patterns"></a>Wzorce rozwiązań usługi Azure Stream Analytics

Podobnie jak w przypadku wielu innych usług na platformie Azure, Stream Analytics jest najlepiej używany z innymi usługami w celu utworzenia większego kompleksowego rozwiązania. W tym artykule omówiono proste rozwiązania Azure Stream Analytics i różne wzorce architektury. Możesz kompilować te wzorce, aby opracowywać bardziej złożone rozwiązania. Wzorce opisane w tym artykule mogą być używane w wielu różnych scenariuszach. Przykłady wzorców specyficznych dla scenariusza są dostępne w [architekturach rozwiązań platformy Azure](https://azure.microsoft.com/solutions/architecture/?product=stream-analytics).

## <a name="create-a-stream-analytics-job-to-power-real-time-dashboarding-experience"></a>Utwórz zadanie Stream Analytics, aby włączyć obsługę pulpitu nawigacyjnego w czasie rzeczywistym

Za pomocą Azure Stream Analytics można szybko skonfigurować pulpity nawigacyjne i alerty w czasie rzeczywistym. Proste rozwiązanie pozyskuje zdarzenia z Event Hubs lub IoT Hub i tworzy [kanał nawigacyjny Power BI z zestawem danych przesyłanych strumieniowo](/power-bi/service-real-time-streaming). Aby uzyskać więcej informacji, Zobacz szczegółowy samouczek [Analizowanie fałszywych danych wywołań przy użyciu Stream Analytics i wizualizacji wyników na pulpicie nawigacyjnym Power BI](stream-analytics-real-time-fraud-detection.md).

![Panel Power BI ASA](media/stream-analytics-solution-patterns/power-bi-dashboard.png)

To rozwiązanie można utworzyć w ciągu zaledwie kilku minut od Azure Portal. Nie ma żadnego rozbudowanego kodu, a język SQL jest używany do wyrażenia logiki biznesowej.

Ten wzorzec rozwiązania zapewnia najniższe opóźnienie ze źródła zdarzenia do pulpitu nawigacyjnego Power BI w przeglądarce. Azure Stream Analytics jest jedyną usługą platformy Azure z wbudowaną możliwością.

## <a name="use-sql-for-dashboard"></a>Korzystanie z SQL dla pulpitu nawigacyjnego

Pulpit nawigacyjny Power BI zapewnia małe opóźnienia, ale nie może być używany do tworzenia raportów pełnych Power BI dopracowane. Typowym wzorcem raportowania jest wyprowadzanie danych w pierwszej kolejności SQL Database. Następnie użyj Power BI łącznika SQL, aby wysłać zapytanie do programu SQL w celu uzyskania najnowszych danych.

![Pulpit nawigacyjny programu ASA SQL](media/stream-analytics-solution-patterns/sql-dashboard.png)

Korzystanie z SQL Database zapewnia większą elastyczność, ale kosztem nieco większego opóźnienia. To rozwiązanie jest optymalne w przypadku zadań z wymaganiami opóźnienia większą niż jedna sekunda. Za pomocą tej metody można zmaksymalizować możliwości Power BI w celu dodatkowego wycinania i utworzenia indeksu danych dla raportów oraz wielu opcji wizualizacji. Zapewniasz również elastyczność korzystania z innych rozwiązań pulpitu nawigacyjnego, takich jak Tableau.

SQL nie jest magazynem danych o dużej przepływności. Maksymalna przepływność do SQL Database z Azure Stream Analytics jest obecnie około 24 MB/s. Jeśli źródła zdarzeń w rozwiązaniu generują dane z większą szybkością, należy użyć logiki przetwarzania w Stream Analytics, aby zmniejszyć szybkość danych wyjściowych do SQL. Można używać technik takich jak filtrowanie, agregacje okien, dopasowanie wzorców do sprzężeń czasowych i funkcje analityczne. Szybkość danych wyjściowych do SQL może być dodatkowo zoptymalizowana przy użyciu technik opisanych w [Azure Stream Analytics danych wyjściowych Azure SQL Database](stream-analytics-sql-output-perf.md).

## <a name="incorporate-real-time-insights-into-your-application-with-event-messaging"></a>Dołączanie szczegółowych informacji w czasie rzeczywistym do aplikacji przy użyciu komunikatów zdarzeń

Drugim najpopularniejszym użyciem Stream Analytics jest generowanie alertów w czasie rzeczywistym. W tym wzorcu rozwiązania logika biznesowa w Stream Analytics może służyć do wykrywania wzorców danych czasowych [i przestrzennych oraz](stream-analytics-geospatial-functions.md) [anomalii](stream-analytics-machine-learning-anomaly-detection.md), a następnie generowania sygnałów alertów. Jednak w przeciwieństwie do rozwiązania pulpitu nawigacyjnego, w którym Stream Analytics używa Power BI jako preferowanego punktu końcowego, można użyć szeregu pośrednich ujść danych. Te ujścia obejmują Event Hubs, Service Bus i Azure Functions. Jako Konstruktor aplikacji należy zdecydować, który ujścia danych najlepiej sprawdza się w danym scenariuszu.

Logika odbiorcy zdarzeń podrzędnych musi być zaimplementowana, aby generować alerty w istniejącym przepływie pracy. Ponieważ można wdrożyć logikę niestandardową w Azure Functions, Azure Functions to najszybszy sposób na wykonanie tej integracji. Samouczek dotyczący używania funkcji platformy Azure jako danych wyjściowych dla zadania Stream Analytics można znaleźć w temacie [Run Azure Functions from Azure Stream Analytics Jobs](stream-analytics-with-azure-functions.md). Azure Functions obsługuje również różne typy powiadomień, w tym tekst i wiadomości e-mail. Aplikacja logiki może być również używana do integracji z Event Hubs między Stream Analytics i aplikacją logiki.

![ASA aplikacji do obsługi komunikatów zdarzeń](media/stream-analytics-solution-patterns/event-messaging-app.png)

Event Hubs, z drugiej strony, oferuje najbardziej elastyczny punkt integracji. Wiele innych usług, takich jak Azure Eksplorator danych i Time Series Insights, może zużywać zdarzenia z Event Hubs. Usługi mogą być połączone bezpośrednio z Event Hubs ujścia z Azure Stream Analytics, aby zakończyć rozwiązanie. Event Hubs to również najwyższa usługa przesyłania komunikatów z przepustowością dostępna na platformie Azure na potrzeby takich scenariuszy integracji.

## <a name="dynamic-applications-and-websites"></a>Dynamiczne aplikacje i witryny sieci Web

Możesz tworzyć niestandardowe wizualizacje w czasie rzeczywistym, takie jak pulpit nawigacyjny lub Wizualizacja mapy, przy użyciu Azure Stream Analytics i usługi Azure Signal Service. Przy użyciu usługi sygnalizującego klienci sieci Web mogą być uaktualniani i wyświetlali zawartość dynamiczną w czasie rzeczywistym.

![Aplikacja dynamiczna ASA](media/stream-analytics-solution-patterns/dynamic-app.png)

## <a name="incorporate-real-time-insights-into-your-application-through-data-stores"></a>Dołączanie szczegółowych informacji w czasie rzeczywistym do aplikacji przy użyciu magazynów danych

Większość usług sieci Web i aplikacji sieci Web dziś używają wzorca żądania/odpowiedzi do obsłużenia warstwy prezentacji. Wzorzec żądania/odpowiedzi jest prosty do kompilowania i można łatwo skalować z niską godziną reakcji przy użyciu frontonu i skalowalnego magazynu, takiego jak Cosmos DB.

Duże ilości danych często tworzą wąskie gardła wydajności w systemie CRUD. [Wzorzec rozwiązania źródła zdarzeń](/azure/architecture/patterns/event-sourcing) służy do rozwiązywania wąskich gardeł wydajności. Wzorce czasowe i szczegółowe informacje są również trudne i niewydajne do wyodrębnienia z tradycyjnego magazynu danych. Nowoczesne aplikacje oparte na danych o wysokiej wielkości woluminów często przyjmują architekturę opartą na przepływu danych. Azure Stream Analytics, ponieważ aparat obliczeniowy dla danych w ruchu to Linchpin w tej architekturze.

![ASA — aplikacja do pozyskiwania zdarzeń](media/stream-analytics-solution-patterns/event-sourcing-app.png)

W tym wzorcu rozwiązania zdarzenia są przetwarzane i agregowane do magazynów danych przez Azure Stream Analytics. Warstwa aplikacji współdziała z magazynami danych przy użyciu tradycyjnego wzorca żądania/odpowiedzi. Ze względu na Stream Analytics "możliwość przetwarzania dużej liczby zdarzeń w czasie rzeczywistym aplikacja jest wysoce skalowalna bez konieczności tworzenia zbiorczej warstwy magazynu danych. Warstwa magazynu danych jest zasadniczo istotnym widokiem w systemie. [Azure Stream Analytics dane wyjściowe do Azure Cosmos DB](stream-analytics-documentdb-output.md) opisują sposób, w jaki Cosmos DB jest używany jako Stream Analytics dane wyjściowe.

W realnych aplikacjach, w których logika przetwarzania jest złożona i istnieje konieczność niezależnego uaktualniania niektórych części logiki, wiele zadań Stream Analytics może być złożonych razem z Event Hubs jako pośrednik zdarzeń pośredniczących.

![Aplikacja do pozyskiwania zdarzeń złożonych](media/stream-analytics-solution-patterns/event-sourcing-app-complex.png)

Ten wzorzec poprawia odporność i możliwości zarządzania w systemie. Jednak mimo że Stream Analytics gwarantuje dokładnie jednokrotne przetwarzanie, istnieje mała szansa, że zduplikowane zdarzenia mogą być pośrednimi Event Hubs. Ważne jest, aby Stream Analytics zadania deduplikowania zdarzeń przy użyciu kluczy logiki w oknie lookback. Aby uzyskać więcej informacji na temat dostarczania zdarzeń, zobacz temat informacje o [gwarancji dostarczania zdarzeń](/stream-analytics-query/event-delivery-guarantees-azure-stream-analytics) .

## <a name="use-reference-data-for-application-customization"></a>Korzystanie z danych referencyjnych na potrzeby dostosowywania aplikacji

Funkcja dane referencyjne Azure Stream Analytics jest zaprojektowana specjalnie dla użytkowników końcowych, takich jak progi alertów, reguły przetwarzania i [geoogrodzenia](geospatial-scenarios.md). Warstwa aplikacji może akceptować zmiany parametrów i przechowywać je w SQL Database. Zadanie Stream Analytics okresowo wysyła zapytania o zmiany z bazy danych i udostępnia parametry dostosowania za pośrednictwem sprzężenia danych referencyjnych. Aby uzyskać więcej informacji na temat sposobu używania danych referencyjnych do dostosowywania aplikacji, zobacz [dane referencyjne SQL](sql-reference-data.md) i [sprzężenie danych referencyjnych](/stream-analytics-query/reference-data-join-azure-stream-analytics).

Ten wzorzec może również służyć do implementowania aparatu reguł, w którym progi reguł są zdefiniowane na podstawie danych referencyjnych. Aby uzyskać więcej informacji na temat reguł, zobacz [konfigurowalne reguły oparte na progach w Azure Stream Analytics](stream-analytics-threshold-based-rules.md).

![ASA — aplikacja danych referencyjnych](media/stream-analytics-solution-patterns/reference-data-app.png)

## <a name="add-machine-learning-to-your-real-time-insights"></a>Dodawanie Machine Learning do wglądu w dane w czasie rzeczywistym

Azure Stream Analytics "wbudowany [model wykrywania anomalii](stream-analytics-machine-learning-anomaly-detection.md) jest wygodnym sposobem wprowadzania Machine Learning do aplikacji w czasie rzeczywistym. Aby uzyskać szerszy zakres Machine Learning, zobacz [Azure Stream Analytics integruje się z usługą oceniania Azure Machine Learning](stream-analytics-machine-learning-integration-tutorial.md).

W przypadku zaawansowanych użytkowników, którzy chcą włączyć szkolenie online i ocenianie w tym samym potoku Stream Analytics, zobacz ten przykład w przypadku [regresji liniowej](stream-analytics-high-frequency-trading.md).

![Aplikacja Machine Learning ASA](media/stream-analytics-solution-patterns/machine-learning-app.png)

## <a name="real-time-data-warehousing"></a>Magazynowanie danych w czasie rzeczywistym

Innym typowym wzorcem jest magazynowanie danych w czasie rzeczywistym, nazywane również magazynem danych przesyłania strumieniowego. Oprócz zdarzeń, które docierają do Event Hubs i IoT Hub z aplikacji, [Azure Stream Analytics działające na IoT Edge](stream-analytics-edge.md) mogą służyć do zaspokojenia czyszczenia danych, zmniejszenia danych i przechowywania danych oraz potrzeby przesyłania dalej. Stream Analytics uruchomione na IoT Edge mogą bezpiecznie obsługiwać ograniczenia przepustowości i problemy z łącznością w systemie. Stream Analytics może obsługiwać przepływność 200 MB na sekundę podczas zapisywania do usługi Azure Synapse Analytics.

![Magazynowanie danych ASA](media/stream-analytics-solution-patterns/data-warehousing.png)


## <a name="archiving-real-time-data-for-analytics"></a>Archiwizowanie danych w czasie rzeczywistym na potrzeby analiz

Większość działań związanych z nauką i analizą danych nadal działa w trybie offline. Dane można archiwizować przez Azure Stream Analytics za pomocą Azure Data Lake Store Gen2 i formatów wyjściowych Parquet. Ta funkcja eliminuje tarcie do strumieniowego źródła danych bezpośrednio do Azure Data Lake Analytics, Azure Databricks i usługi Azure HDInsight. Azure Stream Analytics jest używany jako aparat ETL niemal w czasie rzeczywistym w tym rozwiązaniu. Zarchiwizowane dane można eksplorować w Data Lake przy użyciu różnych aparatów obliczeniowych.

> [!div class="mx-imgBorder"]
> ![Analiza usługi ASA w trybie offline](media/stream-analytics-solution-patterns/offline-analytics.png)

## <a name="use-reference-data-for-enrichment"></a>Użyj danych referencyjnych do wzbogacania

Wzbogacanie danych jest często wymagane dla aparatów ETL. Azure Stream Analytics obsługuje Wzbogacanie danych z [danymi referencyjnymi](stream-analytics-use-reference-data.md) z SQL Database i usługi Azure Blob Storage. Wzbogacanie danych można wykonać w celu załadowania danych zarówno w Azure Data Lake, jak i na platformie Azure Synapse Analytics.


![Analiza usługi ASA w trybie offline z wzbogacaniem danych](media/stream-analytics-solution-patterns/offline-analytics-enriched.png)

## <a name="operationalize-insights-from-archived-data"></a>Operacjonalizować wgląd w dane archiwalne

W przypadku łączenia wzorca analizy offline z wzorcem aplikacji niemal w czasie rzeczywistym można utworzyć pętlę opinii. Pętla opinii umożliwia automatyczne dostosowanie aplikacji do zmian wzorców w danych. Ta pętla zwrotna może być prosta jako zmiana wartości progowej dotyczącej alertów lub jako złożonego Machine Learning modeli. Tę samą architekturę rozwiązania można zastosować do zadań ASA uruchomionych w chmurze i na IoT Edge.

![ASA Insights operacjonalizacji](media/stream-analytics-solution-patterns/insights-operationalization.png)

## <a name="how-to-monitor-asa-jobs"></a>Jak monitorować zadania ASA

Zadanie Azure Stream Analytics można uruchomić 24/7, aby przetwarzać zdarzenia przychodzące w sposób ciągły w czasie rzeczywistym. Gwarancja czasu działania jest istotna dla kondycji ogólnej aplikacji. Chociaż Stream Analytics jest jedyną usługą analizy przesyłania strumieniowego w branży, która oferuje  [gwarancję dostępności na 99,9%](https://azure.microsoft.com/support/legal/sla/stream-analytics/v1_0/), nadal może być ponoszony pewien poziom czasu. W ciągu lat Stream Analytics wprowadził metryki, dzienniki i Stany zadań, aby odzwierciedlały kondycję zadań. Wszystkie z nich są naAzure Monitor usługi i można je wyeksportować do pakietu OMS. Aby uzyskać więcej informacji, zobacz [Omówienie monitorowania zadań Stream Analytics i sposobu monitorowania zapytań](stream-analytics-monitoring.md).

![Monitorowanie ASA](media/stream-analytics-solution-patterns/monitoring.png)

Istnieją dwa kluczowe elementy do monitorowania:

- [Stan niepowodzenia zadania](job-states.md)

    Najpierw musisz się upewnić, że zadanie jest uruchomione. Bez zadania w stanie uruchomionym nie są generowane żadne nowe metryki ani dzienniki. Zadania mogą ulec zmianie do stanu niepowodzenia z różnych powodów, w tym o wysokim poziomie wykorzystania SU (tj. braku zasobów).

- [Metryki opóźnienia znaku wodnego](https://azure.microsoft.com/blog/new-metric-in-azure-stream-analytics-tracks-latency-of-your-streaming-pipeline/)

    Ta Metryka odzwierciedla, jak daleko z potoku przetwarzania jest czas zegara ściany (w sekundach). Niektóre opóźnienia są przypisane do logiki niewłaściwego przetwarzania. W efekcie monitorowanie rosnącego trendu jest znacznie ważniejsze niż monitorowanie wartości bezwzględnej. Stałe opóźnienie stanu powinno być rozkierowane przez projekt aplikacji, a nie przez monitorowanie lub alerty.

W przypadku niepowodzenia dzienniki aktywności i [dzienniki diagnostyki](stream-analytics-job-diagnostic-logs.md) są najlepszym miejscem, aby rozpocząć wyszukiwanie błędów.

## <a name="build-resilient-and-mission-critical-applications"></a>Twórz odporne na błędy i aplikacje o znaczeniu strategicznym

Bez względu na to, czy Azure Stream Analytics "gwarancja SLA i jak ostrożnie uruchamiać kompleksową aplikację, wystąpi awaria. Jeśli aplikacja ma krytyczne znaczenie dla działania, należy przygotować się na awarię, aby zapewnić bezpieczną sprawność.

W przypadku alertów dotyczących aplikacji najważniejszym zadaniem jest wykrycie następnego alertu. Możesz zdecydować się na ponowne uruchomienie zadania od bieżącego czasu podczas odzyskiwania, ignorowanie ostatnich alertów. Semantyka czasu rozpoczęcia zadania jest według pierwszego czasu danych wyjściowych, a nie pierwszego czasu wejścia. Dane wejściowe są rozwiązane wstecz przez odpowiedni czas, aby zagwarantować, że pierwsze dane wyjściowe w określonym czasie są kompletne i poprawne. W wyniku nieoczekiwanie nie zostaną wyświetlone częściowe Agregaty i alerty wyzwalacza.

Możesz również uruchomić dane wyjściowe od pewnego czasu w przeszłości. Zasady przechowywania Event Hubs i IoT Hub przechowują rozsądną ilość danych, aby umożliwić przetwarzanie od przeszłości. Wadą jest to, jak szybko można przechwycić do bieżącego czasu i rozpocząć generowanie nowych alertów. Dane utraciły swoją wartość szybko z upływem czasu, dlatego ważne jest, aby szybko wychwycić do bieżącego czasu. Istnieją dwa sposoby, aby szybko wychwycić:

- Zainicjuj obsługę większej liczby zasobów (SU) podczas przechwytywania.
- Uruchom ponownie od bieżącego czasu.

Ponowne uruchamianie od bieżącego czasu jest proste do wykonania, z kompromisem wychodzącym z przerwy podczas przetwarzania. Ponowne uruchomienie w ten sposób może być prawidłowe w przypadku scenariuszy z alertami, ale może być przyczyną problemów z scenariuszami pulpitu nawigacyjnego i jest niestarterem na potrzeby archiwizowania i scenariuszy magazynowania danych.

Inicjowanie obsługi większej liczby zasobów może przyspieszyć proces, ale efekt przepięcia szybkości przetwarzania jest skomplikowany.

- Przetestuj, czy zadanie jest skalowalne do większej liczby programów SUs. Nie wszystkie zapytania są skalowalne. Musisz się upewnić, że zapytanie jest [równoległe](stream-analytics-parallelization.md).

- Upewnij się, że w Event Hubs nadrzędnym jest wystarczająca liczba partycji lub IoT Hub można dodać więcej jednostek przepływności (TUs) w celu skalowania przepływności wejściowej. Należy pamiętać, że każda Event Hubs jednostek PRZEPŁYWNOŚCI maxes z szybkością wyjściową wynoszącą 2 MB/s.

- Upewnij się, że masz zapewnioną wystarczającą ilość zasobów w ujściach wyjściowych (tj. SQL Database, Cosmos DB), dzięki czemu nie ograniczają one napięcia w danych wyjściowych, co może czasami spowodować zablokowanie systemu.

Najważniejszym zadaniem jest przewidywanie zmiany szybkości przetwarzania, testowanie tych scenariuszy przed przechodzeniem do środowiska produkcyjnego i przygotowanie się do prawidłowego skalowania przetwarzania podczas odzyskiwania po awarii.

W ekstremalnym scenariuszu wszystkie zdarzenia przychodzące są opóźniane, ale po zastosowaniu opóźnionego okna do zadania [wszystkie opóźnione zdarzenia są usuwane](stream-analytics-time-handling.md) . Upuszczenie zdarzeń może wydawać się tajemniczymi zachowaniem na początku; jednak rozważanie Stream Analytics jest aparatem przetwarzania w czasie rzeczywistym, ponieważ oczekuje, że zdarzenia przychodzące są blisko czasu zegara ściany. Należy porzucić zdarzenia, które naruszają te ograniczenia.

### <a name="lambda-architectures-or-backfill-process"></a>Architektury lambda lub proces wypełniania

Na szczęście poprzedni wzorzec archiwizowania danych może być używany do bezproblemowego przetwarzania tych opóźnionych zdarzeń. Pomysłem jest to, że zadanie archiwizowania przetwarza zdarzenia przychodzące w czasie przybycia i archiwizuje zdarzenia w odpowiednim przedziale czasu w obiekcie blob platformy Azure lub Azure Data Lake Store z czasem zdarzenia. Nie ma znaczenia, jak późne jest zdarzenie, nigdy nie zostanie porzucone. Będzie zawsze gruntować w odpowiednim przedziale czasu. Podczas odzyskiwania można ponownie przetwarzać zarchiwizowane zdarzenia i wypełniać wyniki do wybranego magazynu. Jest to podobne do sposobu implementacji wzorców lambda.

![Odpełnienie ASA](media/stream-analytics-solution-patterns/back-fill.png)

Proces wycofywania musi być wykonywany z systemem przetwarzania wsadowego w trybie offline, który prawdopodobnie ma inny model programowania niż Azure Stream Analytics. Oznacza to konieczność ponownego wdrożenia całej logiki przetwarzania.

W przypadku wypełniania nadmiaru jest nadal ważne, aby co najmniej czasowo zarezerwować więcej zasobów do ujścia danych wyjściowych w celu zapewnienia większej przepływności niż wymagania dotyczące ciągłego przetwarzania stanu.

|Scenariusze  |Uruchom ponownie tylko teraz  |Uruchom ponownie od czasu ostatniego zatrzymania |Uruchom ponownie od teraz i wypełnianie z zarchiwizowanymi zdarzeniami|
|---------|---------|---------|---------|
|**Pulpit nawigacyjny**   |Tworzy przerwy    |OK dla krótkiej awarii    |Użyj do długotrwałej przestoju |
|**Alerty**   |Zadowalające |OK dla krótkiej awarii    |Niepotrzebne |
|**Aplikacja do pozyskiwania zdarzeń** |Zadowalające |OK dla krótkiej awarii    |Użyj do długotrwałej przestoju |
|**Magazynowanie danych**   |Utrata danych  |Zadowalające |Niepotrzebne |
|**Analiza w trybie offline**  |Utrata danych  |Zadowalające |Niepotrzebne|

## <a name="putting-it-all-together"></a>Zebranie wszystkich elementów

Nie jest trudno Wyobraź sobie, że wszystkie wymienione powyżej wzorce rozwiązania mogą być połączone ze sobą w złożonym kompleksowym systemie. Połączony system może obejmować pulpity nawigacyjne, alerty, aplikacje do pozyskiwania zdarzeń, magazynowanie danych i możliwości analizy w trybie offline.

Klucz polega na zaprojektowaniu systemu w postaci wzorców, aby można było je niezależnie kompilować, testować, uaktualniać i odzyskiwać.

## <a name="next-steps"></a>Następne kroki

Masz teraz różne wzorce rozwiązań korzystające z Azure Stream Analytics. Teraz możesz utworzyć pierwsze zadanie w usłudze Stream Analytics:

* [Tworzenie zadania usługi Stream Analytics przy użyciu witryny Azure Portal](stream-analytics-quick-create-portal.md).
* [Utwórz zadanie Stream Analytics przy użyciu Azure PowerShell](stream-analytics-quick-create-powershell.md).
* [Tworzenie zadania usługi Stream Analytics przy użyciu programu Visual Studio](stream-analytics-quick-create-vs.md).
