---
title: Mapowanie wydajności przepływu danych i przewodnik dostrajania
description: Poznaj kluczowe czynniki wpływające na wydajność mapowania przepływów danych w Azure Data Factory.
author: kromerm
ms.topic: conceptual
ms.author: makromer
ms.service: data-factory
ms.custom: seo-lt-2019
ms.date: 01/29/2021
ms.openlocfilehash: 01c448165e6d1f4d6103c61387298f2d9eb40254
ms.sourcegitcommit: 8c8c71a38b6ab2e8622698d4df60cb8a77aa9685
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/01/2021
ms.locfileid: "99222953"
---
# <a name="mapping-data-flows-performance-and-tuning-guide"></a>Przewodnik dotyczący wydajności i dostrajania przepływu danych

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Mapowanie przepływów danych w Azure Data Factory zapewnia interfejs bez kodu do projektowania i uruchamiania przekształceń danych na dużą skalę. Jeśli nie masz doświadczenia w mapowaniu przepływów danych, zobacz [Omówienie przepływu danych mapowania](concepts-data-flow-overview.md). W tym artykule przedstawiono różne sposoby dostrajania i optymalizowania przepływów danych, dzięki czemu są one zgodne z wynikami testów wydajności.

Obejrzyj poniższy film wideo, aby zobaczyć kilka przykładowych chronometraży przekształcania danych za pomocą przepływów danych.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4rNxM]

## <a name="testing-data-flow-logic"></a>Testowanie logiki przepływu danych

W przypadku projektowania i testowania przepływów danych z poziomu środowiska APD ADF tryb debugowania pozwala interaktywnie testować się w przypadku klastra Spark na żywo. Pozwala to na podgląd danych i wykonywanie przepływów danych bez oczekiwania na rozgrzewanie się klastra. Aby uzyskać więcej informacji, zobacz [tryb debugowania](concepts-data-flow-debug-mode.md).

## <a name="monitoring-data-flow-performance"></a>Monitorowanie wydajności przepływu danych

Po zweryfikowaniu logiki transformacji przy użyciu trybu debugowania, Uruchom przepływ danych jako działanie w potoku. Przepływy danych są uruchamiane w potoku za pomocą [działania wykonaj przepływ danych](control-flow-execute-data-flow-activity.md). Działanie przepływu danych ma unikatowe środowisko monitorowania w porównaniu z innymi działaniami Azure Data Factory, które wyświetlają szczegółowy plan wykonania i profil wydajności logiki transformacji. Aby wyświetlić szczegółowe informacje o monitorowaniu przepływu danych, kliknij ikonę okularów w danych wyjściowych uruchomienia działania potoku. Aby uzyskać więcej informacji, zobacz [monitorowanie przepływów danych mapowania](concepts-data-flow-monitoring.md).

![Monitor przepływu danych](media/data-flow/monitoring-details.png "Monitor przepływu danych 2")

W przypadku monitorowania wydajności przepływu danych istnieją cztery możliwe wąskie gardła do wyszukania:

* Czas uruchamiania klastra
* Odczytywanie ze źródła
* Czas transformacji
* Zapisywanie w ujścia 

![Monitorowanie przepływu danych](media/data-flow/monitoring-performance.png "Monitor przepływu danych 3")

Czas uruchamiania klastra to czas, jaki zajmuje Klaster Apache Spark. Ta wartość znajduje się w prawym górnym rogu ekranu monitorowania. Przepływy danych są uruchamiane w modelu just-in-Time, gdzie każde zadanie używa izolowanego klastra. Ten czas uruchamiania zwykle trwa 3-5 minut. W przypadku zadań sekwencyjnych można to zmniejszyć, włączając wartość czasu wygaśnięcia. Aby uzyskać więcej informacji, zobacz [optymalizacja Azure Integration Runtime](#ir).

Przepływy danych wykorzystują Optymalizator Spark, który zmienia kolejność i uruchamia logikę biznesową w fazie "etapy", tak szybko, jak to możliwe. Dla każdego ujścia, do którego przepływ danych zapisuje, do, dane wyjściowe monitorowania wyświetlają czas trwania każdego etapu transformacji oraz czas potrzebny do zapisu danych w zlewie. Czas, który jest największy jest najprawdopodobniej wąskim gardłem przepływu danych. Jeśli etap przekształcenia, który ma największą wartość, zawiera źródło, warto przyjrzeć się dalszemu optymalizacji czasu odczytu. Jeśli przekształcenie zajmuje dużo czasu, może być konieczne ponowne partycjonowanie lub zwiększenie rozmiaru środowiska Integration Runtime. Jeśli czas przetwarzania ujścia jest duży, może być konieczne skalowanie w górę bazy danych lub zweryfikowanie, że nie są one umieszczane w pojedynczym pliku.

Po zidentyfikowaniu wąskiego gardła przepływu danych należy skorzystać z poniższych strategii optymalizacji, aby zwiększyć wydajność.

## <a name="optimize-tab"></a>Karta optymalizacji

Karta **Optymalizacja** zawiera ustawienia umożliwiające skonfigurowanie schematu partycjonowania klastra Spark. Ta karta istnieje w każdej transformacji przepływu danych i określa, czy chcesz ponownie podzielić dane **po** zakończeniu transformacji. Dostosowanie partycjonowania zapewnia kontrolę nad dystrybucją danych między węzłami obliczeniowymi i optymalizacją lokalizacji danych, które mogą mieć pozytywne i negatywne skutki dla ogólnej wydajności przepływu danych.

![Zrzut ekranu przedstawia kartę Optymalizacja, która zawiera opcję partycji, typ partycji i liczbę partycji.](media/data-flow/optimize.png)

Domyślnie należy zaznaczyć opcję *Użyj bieżącego partycjonowania* , która instruuje Azure Data Factory zachować bieżące partycjonowanie danych wyjściowych transformacji. Gdy trwa ponowne Partycjonowanie danych, należy *użyć bieżącego partycjonowania* w większości scenariuszy. Scenariusze, w których może być konieczne ponowne Partycjonowanie danych, to między innymi agregacje i sprzężenia, które znacząco pochylają dane lub w przypadku korzystania z partycjonowania źródła w bazie danych SQL.

Aby zmienić partycjonowanie na dowolnym przekształceniu, wybierz kartę **Optymalizacja** i wybierz przycisk radiowy **Ustaw partycjonowanie** . Zostanie wyświetlona seria opcji partycjonowania. Najlepsza Metoda partycjonowania różni się w zależności od ilości danych, kluczy kandydujących, wartości null i kardynalności. 

> [!IMPORTANT]
> Pojedyncza partycja łączy wszystkie dane rozproszone w jedną partycję. Jest to bardzo niska operacja, która znacznie wpływa na wszystkie przekształcenie i zapis podrzędny. Azure Data Factory zdecydowanie zaleca się korzystanie z tej opcji, chyba że istnieje wyraźny powód firmy.

Następujące opcje partycjonowania są dostępne w każdej transformacji:

### <a name="round-robin"></a>Działania okrężne 

Metoda okrężna dystrybuuje dane równomiernie między partycjami. Użyj działania okrężnego, gdy nie masz dobrych sugestii kluczowych do wdrożenia pełnej, inteligentnej strategii partycjonowania. Można ustawić liczbę partycji fizycznych.

### <a name="hash"></a>Skrót

Azure Data Factory tworzy skrót kolumn do tworzenia jednolitych partycji, takich jak wiersze o podobnych wartościach, mieszczą się w tej samej partycji. W przypadku korzystania z opcji skrótu Sprawdź możliwe pochylenie partycji. Można ustawić liczbę partycji fizycznych.

### <a name="dynamic-range"></a>Zakres dynamiczny

Zakres dynamiczny używa zakresów dynamicznych platformy Spark na podstawie kolumn lub wyrażeń, które podano. Można ustawić liczbę partycji fizycznych. 

### <a name="fixed-range"></a>Stały zakres

Utwórz wyrażenie, które udostępnia stały zakres dla wartości w kolumnach danych partycjonowanych. Aby uniknąć pochylenia partycji, przed użyciem tej opcji należy dobrze zrozumieć swoje dane. Wartości wprowadzane dla wyrażenia są używane jako część funkcji partycji. Można ustawić liczbę partycji fizycznych.

### <a name="key"></a>Klucz

Jeśli masz dobrą wiedzę o kardynalności danych, partycjonowanie kluczy może być dobrą strategią. Partycjonowanie kluczy tworzy partycje dla każdej unikatowej wartości w kolumnie. Nie można ustawić liczby partycji, ponieważ liczba jest oparta na unikatowych wartościach danych.

> [!TIP]
> Ręczne ustawienie schematu partycjonowania powoduje oddzielenie danych i może przesunięte korzyści wynikające z optymalizacji programu Spark. Najlepszym rozwiązaniem jest nie ręczne ustawianie partycjonowania, chyba że jest to konieczne.

## <a name="logging-level"></a>Poziom rejestrowania

Jeśli nie jest wymagane każde wykonanie potoku działań przepływu danych w celu pełnego rejestrowania wszystkich pełnych dzienników telemetrii, możesz opcjonalnie ustawić poziom rejestrowania na "podstawowa" lub "Brak". Podczas wykonywania przepływów danych w trybie "verbose" (ustawienie domyślne), żądanie ADF można w pełni rejestrować na poszczególnych poziomach partycji podczas przekształcania danych. Może to być kosztowna operacja, dzięki czemu można ją włączyć tylko wtedy, gdy Rozwiązywanie problemów może poprawić ogólny przepływ danych i wydajność potoku. Tryb "podstawowy" spowoduje rejestrowanie tylko czasów trwania transformacji, gdy wartość "Brak" spowoduje jedynie podsumowanie czasu trwania.

![Poziom rejestrowania](media/data-flow/logging.png "Ustaw poziom rejestrowania")

## <a name="optimizing-the-azure-integration-runtime"></a><a name="ir"></a> Optymalizacja Azure Integration Runtime

Przepływy danych są uruchamiane w klastrach Spark, które są w czasie wykonywania. Konfiguracja używanego klastra jest definiowana w środowisku Integration Runtime (IR) działania. Podczas definiowania środowiska Integration runtime można utworzyć trzy zagadnienia dotyczące wydajności: typ klastra, rozmiar klastra i czas do wygaśnięcia.

Aby uzyskać więcej informacji na temat tworzenia Integration Runtime, zobacz [Integration Runtime w Azure Data Factory](concepts-integration-runtime.md).

### <a name="cluster-type"></a>Typ klastra

Dostępne są trzy opcje dla typu klastra Spark w wersjach: ogólnego przeznaczenia, zoptymalizowane pod kątem pamięci i zoptymalizowane pod kątem obliczeń.

Klastry **ogólnego przeznaczenia** to wybór domyślny i idealny w przypadku większości obciążeń przepływu danych. Jest to najlepszy balans wydajności i kosztów.

Jeśli przepływ danych ma wiele sprzężeń i wyszukiwań, możesz chcieć użyć klastra **zoptymalizowanego pod kątem pamięci** . Klastry zoptymalizowane pod kątem pamięci mogą przechowywać więcej danych w pamięci i zminimalizować błędy braku pamięci, które mogą zostać wyświetlone. Zoptymalizowane pod kątem pamięci mają najwyższy poziom cen na rdzeń, ale również prowadzi do bardziej pomyślnych potoków. Jeśli podczas wykonywania przepływów danych wystąpią błędy braku pamięci, przejdź do konfiguracji Azure IR zoptymalizowanej pod kątem pamięci. 

**Zoptymalizowane obliczenia** nie są idealnym rozwiązaniem dla przepływów pracy ETL i nie są zalecane przez zespół Azure Data Factory w przypadku większości obciążeń produkcyjnych. W przypadku uproszczonych, niezwiązanych z pamięcią transformacji danych, takich jak filtrowanie danych lub Dodawanie kolumn pochodnych, można używać klastrów zoptymalizowanych pod kątem obliczeń przy tańszej cenie za rdzeń.

### <a name="cluster-size"></a>Rozmiar klastra

Przepływy danych dystrybuują przetwarzanie danych między różnymi węzłami w klastrze Spark w celu równoległego wykonywania operacji. Klaster Spark o większej liczbie rdzeni zwiększa liczbę węzłów w środowisku obliczeniowym. Więcej węzłów zwiększa moc przetwarzania przepływu danych. Zwiększenie rozmiaru klastra jest często prostym sposobem na skrócenie czasu przetwarzania.

Domyślny rozmiar klastra to cztery węzły sterownika i cztery węzły procesu roboczego.  Podczas przetwarzania większej ilości danych zalecane są większe klastry. Poniżej przedstawiono możliwe opcje ustalania wielkości:

| Rdzenie procesu roboczego | Rdzenie sterowników | Całkowita liczba rdzeni | Uwagi |
| ------------ | ------------ | ----------- | ----- |
| 4 | 4 | 8 | Niedostępne w przypadku obliczeń zoptymalizowanych |
| 8 | 8 | 16 | |
| 16 | 16 | 32 | |
| 32 | 16 | 48 | |
| 64 | 16 | 80 | |
| 128 | 16 | 144 | |
| 256 | 16 | 272 | |

Opłaty za przepływy danych są naliczane w rdzeń wirtualny godzinie, co oznacza, że zarówno rozmiar klastra, jak i czas wykonania. W miarę skalowania w górę koszt klastra zostanie zwiększony, ale całkowity czas ulegnie zmniejszeniu.

> [!TIP]
> Istnieje granica rozmiaru klastra wpływającego na wydajność przepływu danych. W zależności od rozmiaru danych istnieje punkt, w którym zwiększenie rozmiaru klastra spowoduje przetrzymywanie poprawy wydajności. Na przykład jeśli masz więcej węzłów niż partycji danych, dodawanie dodatkowych węzłów nie powiedzie się. Najlepszym rozwiązaniem jest rozpoczęcie małych i skalowalnych rozwiązań w celu spełnienia potrzeb związanych z wydajnością. 

### <a name="time-to-live"></a>Czas wygaśnięcia

Domyślnie każde działanie przepływu danych uruchamia nowy klaster na podstawie konfiguracji środowiska IR. Czas uruchamiania klastra trwa kilka minut, a przetwarzanie danych nie będzie możliwe, dopóki nie zostanie ukończone. Jeśli potoki zawierają wiele **sekwencyjnych** przepływów danych, można włączyć wartość czasu wygaśnięcia (TTL). Określenie wartości czasu wygaśnięcia powoduje, że klaster działa przez określony czas po zakończeniu jego wykonywania. Jeśli nowe zadanie zostanie uruchomione przy użyciu środowiska IR w czasie trwania czasu wygaśnięcia, spowoduje to znaczne skrócenie czasu uruchomienia istniejącego klastra. Po zakończeniu drugiego zadania klaster będzie nadal działać przez czas TTL.

Tylko jedno zadanie można uruchomić w pojedynczym klastrze naraz. Jeśli jest dostępny klaster, ale rozpocznie się dwa przepływy danych, tylko jedna z nich będzie korzystać z klastra na żywo. Drugie zadanie spowoduje przejęcie własnego klastra izolowanego.

Jeśli większość przepływów danych jest wykonywanych równolegle, nie zaleca się włączania czasu wygaśnięcia. 

> [!NOTE]
> Czas wygaśnięcia nie jest dostępny w przypadku korzystania z rozwiązania Integration Runtime

## <a name="optimizing-sources"></a>Optymalizowanie źródeł

Dla każdego źródła, z wyjątkiem Azure SQL Database, zalecane jest zachowanie **bieżącego partycjonowania** jako wybranej wartości. Podczas odczytywania ze wszystkich innych systemów źródłowych dane są automatycznie partycjonowane na podstawie rozmiaru danych. Utworzona zostanie Nowa partycja dla każdego 128 MB danych. W miarę wzrostu rozmiaru danych liczba partycji rośnie.

Każde partycjonowanie niestandardowe następuje *po* odczytaniu danych przez program Spark i negatywnie wpłynie na wydajność przepływu danych. Ponieważ dane są równomiernie partycjonowane podczas odczytu, nie jest to zalecane. 

> [!NOTE]
> Szybkości odczytu mogą być ograniczone przez przepływność systemu źródłowego.

### <a name="azure-sql-database-sources"></a>Azure SQL Database źródła

Azure SQL Database ma unikatową opcję partycjonowania o nazwie partycjonowanie "Źródło". Włączenie partycjonowania źródła może zwiększyć czas odczytu z bazy danych Azure SQL DB przez włączenie połączeń równoległych w systemie źródłowym. Określ liczbę partycji i sposób partycjonowania danych. Użyj kolumny partycji o dużej kardynalności. Możesz również wprowadzić zapytanie zgodne ze schematem partycjonowania tabeli źródłowej.

> [!TIP]
> W przypadku partycjonowania źródłowego we/wy SQL Server jest wąskim gardłem. Dodanie zbyt wielu partycji może spowodować nasycenie źródłowej bazy danych. Zazwyczaj cztery lub pięć partycji jest idealnym rozwiązaniem w przypadku korzystania z tej opcji.

![Partycjonowanie źródła](media/data-flow/sourcepart3.png "Partycjonowanie źródła")

#### <a name="isolation-level"></a>Poziom izolacji

Poziom izolacji odczytu w systemie źródłowym Azure SQL ma wpływ na wydajność. Wybranie opcji "Odczytaj niezatwierdzone" zapewni najszybszą wydajność i uniemożliwi wszystkie blokady bazy danych. Aby dowiedzieć się więcej na temat poziomów izolacji SQL, zobacz [Opis poziomów izolacji](/sql/connect/jdbc/understanding-isolation-levels).

#### <a name="read-using-query"></a>Przeczytaj przy użyciu zapytania

Możesz czytać z Azure SQL Database przy użyciu tabeli lub zapytania SQL. Jeśli wykonujesz zapytanie SQL, zapytanie musi zostać zakończone przed rozpoczęciem transformacji. Zapytania SQL mogą być przydatne do wypychania operacji, które mogą być wykonywane szybciej i zmniejszają ilość danych odczytywanych z SQL Server, takich jak instrukcje SELECT, WHERE i JOIN. Podczas wypychania operacji, utracisz możliwość śledzenia elementów i wydajności przekształceń przed wprowadzeniem danych do przepływu danych.

### <a name="azure-synapse-analytics-sources"></a>Źródła analiz usługi Azure Synapse

W przypadku korzystania z usługi Azure Synapse Analytics w opcjach źródła istnieje ustawienie o nazwie **Włącz przemieszczanie** . Pozwala to na odczytywanie ADF z Synapse przy użyciu ```Staging``` , co znacznie zwiększa wydajność odczytu. Włączenie ```Staging``` wymaga określenia Azure Data Lake Storage usługi Azure Blob Storage lub lokalizacji tymczasowej Gen2 w ustawieniach działania przepływu danych.

![Włączanie trybu przejściowego](media/data-flow/enable-staging.png "Włączanie trybu przejściowego")

### <a name="file-based-sources"></a>Źródła oparte na plikach

Podczas gdy przepływy danych obsługują różne typy plików, Azure Data Factory zaleca użycie natywnego formatu Parquet platformy Spark w celu uzyskania optymalnego czasu odczytu i zapisu.

Jeśli używasz tego samego przepływu danych na zestawie plików, zalecamy odczytywanie z folderu przy użyciu ścieżek symboli wieloznacznych lub odczytywanie z listy plików. Pojedynczy przebieg działania przepływu danych może przetwarzać wszystkie pliki w usłudze Batch. Więcej informacji na temat sposobu ustawiania tych ustawień można znaleźć w dokumentacji łącznika, takiej jak [Azure Blob Storage](connector-azure-blob-storage.md#source-transformation).

Jeśli to możliwe, Unikaj używania działania For-Each do uruchamiania przepływów danych przez zestaw plików. Spowoduje to, że każda iteracja dla — każdego będzie uruchamiać własny klaster Spark, który często nie jest konieczny i może być kosztowny. 

## <a name="optimizing-sinks"></a>Optymalizowanie ujścia

Gdy dane są zapisywane w ujściach, wszelkie niestandardowe partycjonowanie będą wykonywane bezpośrednio przed zapisem. Podobnie jak w przypadku źródła, zaleca się, aby w większości przypadków zachować **bieżące partycjonowanie** jako opcję wybranej partycji. Partycjonowane dane będą zapisywane znacznie szybciej niż dane niepartycjonowane, nawet jeśli miejsce docelowe nie zostanie podzielone na partycje. Poniżej przedstawiono poszczególne zagadnienia dotyczące różnych typów ujścia. 

### <a name="azure-sql-database-sinks"></a>Azure SQL Database ujścia

W przypadku Azure SQL Database domyślne partycjonowanie powinno być wykonywane w większości przypadków. Istnieje prawdopodobieństwo, że ujścia może mieć zbyt wiele partycji, aby Twoja baza danych SQL mogła obsłużyć. Jeśli używasz tego programu, zmniejsz liczbę partycji przeszukanych przez SQL Database ujścia.

#### <a name="impact-of-error-row-handling-to-performance"></a>Wpływ obsługi wiersza błędu na wydajność

Po włączeniu obsługi wierszy błędów ("Kontynuuj przy błędzie") w transformacji ujścia funkcja ADF zajmie dodatkowy krok przed zapisaniem zgodnych wierszy w tabeli docelowej. Ten dodatkowy krok będzie miał niewielką spadek wydajności, który może znajdować się w zakresie 5% dodanym dla tego kroku z dodatkowym niewielkim trafieniem wydajności, który zostanie również dodany, jeśli ustawisz opcję, aby również z niezgodnymi wierszami w pliku dziennika.

#### <a name="disabling-indexes-using-a-sql-script"></a>Wyłączanie indeksów przy użyciu skryptu SQL

Wyłączenie indeksów przed obciążeniem w bazie danych SQL może znacznie poprawić wydajność zapisu w tabeli. Uruchom poniższe polecenie przed zapisaniem w usłudze SQL sink.

`ALTER INDEX ALL ON dbo.[Table Name] DISABLE`

Po zakończeniu zapisu Odbuduj indeksy przy użyciu następującego polecenia:

`ALTER INDEX ALL ON dbo.[Table Name] REBUILD`

Mogą one być wykonywane w sposób natywny za pomocą skryptów poprzedzających i podanych po zainicjowaniu w ramach usługi Azure SQL DB lub ujścia Synapse w celu mapowania przepływów danych.

![Wyłącz indeksy](media/data-flow/disable-indexes-sql.png "Wyłącz indeksy")

> [!WARNING]
> Podczas wyłączania indeksów przepływ danych efektywnie pobiera kontrolę nad bazą danych, a w tej chwili nie można pomyślnie wykonać zapytań. W związku z tym wiele zadań ETL jest wyzwalanych w połowie nocy, aby uniknąć tego konfliktu. Aby uzyskać więcej informacji, zapoznaj się z [ograniczeniami wyłączania indeksów](/sql/relational-databases/indexes/disable-indexes-and-constraints) .

#### <a name="scaling-up-your-database"></a>Skalowanie bazy danych w górę

Zaplanuj zmianę rozmiarów źródła i ujścia usługi Azure SQL DB oraz DW przed uruchomieniem potoku, aby zwiększyć przepływność i zminimalizować ograniczenie przepustowości platformy Azure po osiągnięciu limitów jednostek DTU. Po zakończeniu wykonywania potoku Zmień rozmiar baz danych z powrotem na ich normalną szybkość uruchamiania.

### <a name="azure-synapse-analytics-sinks"></a>Ujścia usługi Azure Synapse Analytics

Podczas zapisywania do usługi Azure Synapse Analytics upewnij się, że właściwość **enable Staging** ma wartość true. Dzięki temu APD można napisać przy użyciu [polecenia COPY SQL](/sql/t-sql/statements/copy-into-transact-sql) , które skutecznie ładuje dane. Aby przemieszczać dane podczas korzystania z przemieszczania, należy odwoływać się do Azure Data Lake Storage Gen2 lub konta Blob Storage platformy Azure.

Oprócz przemieszczenia te same najlepsze rozwiązania dotyczą usługi Azure Synapse Analytics jako Azure SQL Database.

### <a name="file-based-sinks"></a>Ujścia oparte na plikach 

Podczas gdy przepływy danych obsługują różne typy plików, Azure Data Factory zaleca użycie natywnego formatu Parquet platformy Spark w celu uzyskania optymalnego czasu odczytu i zapisu.

Jeśli dane są dystrybuowane równomiernie, **Użyj bieżącego partycjonowania** , który będzie najszybszą opcją partycjonowania plików.

#### <a name="file-name-options"></a>Opcje nazwy pliku

Podczas pisania plików można wybrać opcje nazewnictwa, które mają wpływ na wydajność.

![Opcje ujścia](media/data-flow/file-sink-settings.png "Opcje ujścia")

Wybranie opcji **domyślnej** spowoduje zapisanie najszybciej. Każda partycja będzie równa plikowi z domyślną nazwą platformy Spark. Jest to przydatne, jeśli właśnie czytasz z folderu danych.

Ustawienie **wzorca** nazewnictwa zmieni nazwę każdego pliku partycji na bardziej przyjazną nazwę użytkownika. Ta operacja jest wykonywana po zapisie i jest nieco wolniejsza niż wybór domyślny. Na partycję można ręcznie nazwać poszczególne partycje.

Jeśli kolumna odnosi się do sposobu wyprowadzania danych, można wybrać **jako dane w kolumnie**. Spowoduje to ponowne rozłożenie danych i może mieć wpływ na wydajność, jeśli kolumny nie są równomiernie dystrybuowane.

Dane **wyjściowe do pojedynczego pliku** łączą wszystkie dane w jedną partycję. Prowadzi to do długotrwałych czasów zapisu, szczególnie w przypadku dużych zestawów danych. Zespół Azure Data Factory zdecydowanie zaleca **rezygnację** z tej opcji, chyba że istnieje wyraźny powód firmy.

### <a name="cosmosdb-sinks"></a>Ujścia CosmosDB

Podczas zapisywania w CosmosDB, zmiana przepływności i rozmiaru partii podczas wykonywania przepływu danych może zwiększyć wydajność. Te zmiany zaczną obowiązywać tylko w trakcie działania przepływu danych i po zakończeniu zostaną przywrócone do oryginalnych ustawień kolekcji. 

**Rozmiar wsadu:** Zwykle jest to wystarczające od domyślnego rozmiaru partii. Aby dodatkowo dostroić tę wartość, Oblicz rozmiar nieprzebudowanego obiektu danych i upewnij się, że rozmiar obiektu * rozmiar wsadu jest mniejszy niż 2 MB. W takim przypadku można zwiększyć rozmiar wsadu w celu uzyskania lepszej przepływności.

**Przepływność:** W tym miejscu ustaw wyższą wartość ustawienia przepływności, aby zezwolić na szybsze pisanie dokumentów w programie CosmosDB. Należy pamiętać o wyższych kosztach RU na podstawie ustawienia Wysoka przepływność.

**Budżet przepływności zapisu:** Użyj wartości, która jest mniejsza niż łączna liczba jednostek ru na minutę. Jeśli istnieje przepływ danych o dużej liczbie partycji platformy Spark, ustawienie przepływności budżetu pozwoli na zwiększenie równowagi między tymi partycjami.

## <a name="optimizing-transformations"></a>Optymalizowanie transformacji

### <a name="optimizing-joins-exists-and-lookups"></a>Optymalizowanie sprzężeń, EXISTS i Lookups

#### <a name="broadcasting"></a>Emisji

W sprzężeniach, wyszukiwaniu i istniejących przekształceniach, jeśli jeden lub oba strumienie danych są wystarczająco małe, aby mieściły się w pamięci węzła procesu roboczego, można zoptymalizować wydajność, włączając **emisję**. Emisja jest wysyłana w przypadku wysyłania małych ramek danych do wszystkich węzłów w klastrze. Umożliwia to przełączenie aparatu Spark bez reshuffling danych w dużym strumieniu. Domyślnie aparat Spark automatycznie zdecyduje, czy rozgłaszać po jednej stronie sprzężenia. Jeśli znasz dane przychodzące i wiesz, że jeden strumień będzie znacznie mniejszy niż drugi, możesz wybrać opcję **stała** emisja. Naprawiono wymuszanie rozgłaszania wybranego strumienia przez platformę Spark. 

Jeśli rozmiar emitowanych danych jest zbyt duży dla węzła Spark, może wystąpić błąd braku pamięci. Aby uniknąć błędów w pamięci, użyj klastrów **zoptymalizowanych pod kątem pamięci** . Jeśli podczas wykonywania przepływu danych wystąpią limity czasu emisji, można wyłączyć optymalizację emisji. Spowoduje to jednak wolniejsze wykonywanie przepływów danych.

![Optymalizacja transformacji sprzężeń](media/data-flow/joinoptimize.png "Optymalizacja dołączania")

#### <a name="cross-joins"></a>Sprzężenia krzyżowe

Jeśli używasz wartości literału w warunkach sprzężenia lub jeśli masz wiele dopasowań po obu stronach sprzężenia, platforma Spark uruchomi sprzężenie jako sprzężenie krzyżowe. Sprzężenie krzyżowe to pełny kartezjańskiego produkt, który następnie filtruje połączone wartości. Jest to znacznie wolniejsze niż inne typy sprzężeń. Upewnij się, że masz odwołania do kolumn na obu stronach warunków sprzężenia, aby uniknąć wpływu na wydajność.

#### <a name="sorting-before-joins"></a>Sortowanie przed sprzężeniami

W przeciwieństwie do łączenia scalania w narzędziach takich jak SSIS, transformacja sprzężenia nie jest obowiązkową operacją scalania sprzężenia. Klucze sprzężenia nie wymagają sortowania przed przekształceniem. Zespół Azure Data Factory nie zaleca używania transformacji sortowania w mapowaniu przepływów danych.

### <a name="window-transformation-performance"></a>Wydajność transformacji okna

[Transformacja okna](data-flow-window.md) dzieli dane według wartości w kolumnach, które są wybierane jako część ```over()``` klauzuli w ustawieniach transformacji. Istnieje wiele popularnych funkcji agregujących i analitycznych, które są dostępne w transformacji systemu Windows. Jeśli jednak przypadek użycia to Generowanie okna dla całego zestawu danych na potrzeby rankingu ```rank()``` lub numeru wiersza ```rowNumber()``` , zaleca się użycie [transformacji rangi](data-flow-rank.md) i [przekształcenia klucza zastępczego](data-flow-surrogate-key.md). Te przekształcenia będą wykonywały lepsze operacje pełnego zestawu danych przy użyciu tych funkcji.

### <a name="repartitioning-skewed-data"></a>Ponowne partycjonowanie pochylonych danych

Niektóre przekształcenia, takie jak sprzężenia i agregacje, przełączają się na partycje danych i mogą czasami prowadzić do pochylenia danych. Skośne dane oznaczają, że dane nie są równomiernie dystrybuowane między partycjami. Silnie skośne dane mogą prowadzić do wolniejszych transformacji podrzędnych i zapisów ujścia. Skośność danych można sprawdzić w dowolnym momencie przebiegu przepływu danych, klikając transformację na ekranie monitorowanie.

![Skośność i kurtoza](media/data-flow/skewness-kurtosis.png "Skośność i kurtoza")

Na ekranie monitorowanie zostanie wyświetlona, jak dane są dystrybuowane między każdą partycję, oraz z dwiema metrykami, skośnością i kurtoza. **Skośność** to miara asymetrycznego danych i może mieć wartość dodatnią, zero, ujemną lub niezdefiniowaną. Skośność ujemna oznacza, że lewy ogon jest dłuższy od prawej. **Kurtoza** to miara, czy dane są intensywnie rozrzutne czy też z jasne. Wartości z dużą wartością kurtoza nie są pożądane. Idealne zakresy skośności mieszczą się w zakresie od-3 do 3, a zakresy kurtoza są mniejsze niż 10. Prostym sposobem interpretacji tych liczb jest przeszukanie wykresu partycji i zobaczenie, czy 1 słupek jest znacznie większy od reszty.

Jeśli dane nie są równomiernie partycjonowane po przekształceniu, można użyć [karty Optymalizacja](#optimize-tab) do ponownego partycjonowania. Dane reshuffling zajmują czas i mogą nie poprawić wydajności przepływów danych.

> [!TIP]
> W przypadku ponownej partycjonowania danych, ale istnieją przekształcenia podrzędne, które powodują ponowne wygenerowanie danych, użyj podziału skrótu w kolumnie używanej jako klucz sprzężenia.

## <a name="using-data-flows-in-pipelines"></a>Używanie przepływów danych w potokach 

Podczas kompilowania złożonych potoków z wieloma przepływami danych przepływ logiczny może mieć duży wpływ na chronometraż i koszt. Ta sekcja dotyczy wpływu różnych strategii architektury.

### <a name="executing-data-flows-in-parallel"></a>Równoległe wykonywanie przepływów danych

W przypadku równoczesnego wykonywania wielu przepływów danych moduł ADF uruchamia oddzielne klastry Spark dla każdego działania. Pozwala to na odizolowanie i równoległe uruchamianie poszczególnych zadań, ale w tym samym czasie będzie działać wiele klastrów.

Jeśli przepływy danych są wykonywane równolegle, zaleca się, aby nie włączać właściwości czas wygaśnięcia Azure IR, ponieważ będzie to powodować powstanie wielu nieużywanych pul.

> [!TIP]
> Zamiast uruchamiania tego samego przepływu danych wiele razy w przypadku każdego działania, należy przygotować swoje dane w usłudze Data Lake i użyć symboli wieloznacznych do przetworzenia danych w pojedynczym przepływie danych.

### <a name="execute-data-flows-sequentially"></a>Wykonywanie przepływów danych sekwencyjnie

Jeśli wykonujesz działania przepływu danych w sekwencji, zalecamy ustawienie czasu wygaśnięcia w konfiguracji Azure IR. ADF będzie ponownie używał zasobów obliczeniowych w wyniku szybszego uruchomienia klastra. Każde działanie nadal będzie odizolowane dla każdego wykonywania.

Uruchamianie zadań sekwencyjnych będzie prawdopodobnie trwać najdłużej, ale zapewnia czyste rozdzielenie operacji logicznych.

### <a name="overloading-a-single-data-flow"></a>Przeciążanie pojedynczego przepływu danych

Jeśli umieścisz całą logikę wewnątrz pojedynczego przepływu danych, moduł ADF wykona całe zadanie na jednym wystąpieniu platformy Spark. Chociaż może to spowodować obniżenie kosztów, nastąpi łączenie różnych przepływów logicznych i może być trudne do monitorowania i debugowania. Jeśli jeden składnik ulegnie awarii, wszystkie pozostałe części zadania również będą kończyć się niepowodzeniem. Zespół Azure Data Factory zaleca organizowanie przepływów danych przez niezależne przepływy logiki biznesowej. Jeśli przepływ danych jest zbyt duży, dzielenie go na osobne składniki ułatwia monitorowanie i debugowanie. Chociaż nie ma żadnych stałych limitów liczby transformacji w przepływie danych, zbyt wiele spowoduje, że zadanie będzie skomplikowane.

### <a name="execute-sinks-in-parallel"></a>Uruchom ujścia równolegle

Domyślne zachowanie ujścia przepływu danych polega na wykonaniu każdego ujścia sekwencyjnie, w sposób szeregowy i do niepowodzenia przepływu danych w przypadku wystąpienia błędu w ujścia. Ponadto wszystkie ujścia są ustawiane domyślnie do tej samej grupy, o ile nie przejdziesz do właściwości przepływu danych i ustawisz różne priorytety dla ujścia.

Przepływy danych umożliwiają grupowanie zlewów w grupach z karty właściwości przepływu danych w projektancie interfejsu użytkownika. Można ustawić kolejność wykonywania ujścia, a także grupować ujścia przy użyciu tego samego numeru grupy. Aby ułatwić zarządzanie grupami, można zażądać uruchamiania APD w tej samej grupie, aby uruchamiała się równolegle.

W działaniu przepływu danych przebiegu potoku w sekcji "właściwości ujścia" jest opcja włączenia równoległego ładowania obiektów sink. Po włączeniu funkcji "Uruchom równolegle" są narzucane dane przepływów zapisu w połączonych ujściach w tym samym czasie, a nie w sposób sekwencyjny. Aby można było użyć opcji Parallel, ujścia musi być zgrupowane razem i połączone z tym samym strumieniem za pośrednictwem nowej gałęzi lub podziału warunkowego.

## <a name="next-steps"></a>Następne kroki

Zobacz inne artykuły dotyczące przepływu danych związane z wydajnością:

- [Działanie przepływu danych](control-flow-execute-data-flow-activity.md)
- [Monitorowanie wydajności przepływu danych](concepts-data-flow-monitoring.md)