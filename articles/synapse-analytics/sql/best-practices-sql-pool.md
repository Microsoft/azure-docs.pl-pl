---
title: Najważniejsze wskazówki dotyczące puli SQL
description: Zalecenia i najlepsze rozwiązania, które należy znać podczas pracy z pulami SQL.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: b3dc111fe62cbae857f3369165ba29cf40e90342
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81427798"
---
# <a name="best-practices-for-sql-pools-in-azure-synapse-analytics"></a>Najważniejsze wskazówki dotyczące pul SQL w usłudze Azure Synapse Analytics

Ten artykuł zawiera zbiór najlepszych rozwiązań ułatwiające osiągnięcie optymalnej wydajności dla pul SQL w usłudze Azure Synapse Analytics. Poniżej znajdziesz podstawowe wskazówki i ważne obszary, na których należy się skupić podczas tworzenia rozwiązania. Każda sekcja wprowadza do koncepcji, a następnie wskazuje bardziej szczegółowe artykuły, które obejmują koncepcję bardziej szczegółowo.

## <a name="sql-pools-loading"></a>Ładowanie pul SQL

Aby uzyskać wskazówki dotyczące ładowania pul SQL, zobacz [Wskazówki dotyczące ładowania danych](data-loading-best-practices.md).

## <a name="reduce-cost-with-pause-and-scale"></a>Obniżenie kosztów dzięki wstrzymaniu i skalowaniu

Aby uzyskać więcej informacji na temat obniżania kosztów poprzez wstrzymywanie i [skalowanie,](../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)zobacz Zarządzanie obliczeniami .

## <a name="maintain-statistics"></a>Prowadzenie statystyk

Podczas sql server automatycznie wykrywa i tworzy lub aktualizuje statystyki kolumn, pule SQL wymagają ręcznej konserwacji statystyk. Należy zachować statystyki, aby upewnić się, że plany puli SQL są zoptymalizowane.  Rzeczywista wartość planów tworzonych przez optymalizator zależy od jakości dostępnych statystyk.

> [!TIP]
> Utworzenie próbkowanych statystyk dla każdej z kolumn to prosty sposób na rozpoczęcie pracy ze statystykami.  

Równie ważne jest aktualizowanie statystyk w miarę pojawiania się kolejnych znaczących zmian w danych.  Zachowawcze podejście nakazywałoby aktualizowanie statystyk codziennie lub po każdym obciążeniu.  Zawsze istnieje możliwość wypracowania kompromisu pomiędzy wydajnością a kosztami tworzenia i aktualizowania statystyk.

Aby skrócić czas konserwacji statystyk, należy wybiórcze, które kolumny mają statystyki, lub wymagają częstszych aktualizacji. Na przykład można zaktualizować kolumny daty, w których nowe wartości mogą być dodawane codziennie. Skoncentruj się na statystykach kolumn biorących udział w sprzężeniach, kolumnach używanych w klauzuli WHERE i kolumnach znalezionych w polu GROUP BY.

Dodatkowe informacje na temat statystyk można znaleźć w [artykułach Zarządzanie statystykami tabeli](develop-tables-statistics.md), [TWORZENIE STATYSTYK](/sql/t-sql/statements/create-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)i [AKTUALIZOWANIE STATYSTYK.](/sql/t-sql/statements/update-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="group-insert-statements-into-batches"></a>Grupowanie instrukcji INSERT w partie

Jednorazowe obciążenie do małej tabeli z instrukcją INSERT, która `INSERT INTO MyLookup VALUES (1, 'Type 1')`może być najlepszym rozwiązaniem w zależności od potrzeb. Jeśli jednak musisz załadować tysiące lub miliony wierszy w ciągu dnia, jest prawdopodobne, że pojedyncze wstawki nie są optymalne.

Jednym ze sposobów rozwiązania tego problemu jest opracowanie jednego procesu, który zapisuje do pliku, a następnie innego procesu, aby okresowo ładować ten plik. Więcej informacji można znaleźć w artykule [INSERT.](/sql/t-sql/statements/insert-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="use-polybase-to-load-and-export-data-quickly"></a>Korzystanie z funkcji PolyBase do szybkiego ładowania i eksportowania danych

Pula SQL obsługuje ładowanie i eksportowanie danych za pomocą kilku narzędzi, w tym usługi Azure Data Factory, PolyBase i BCP.  W przypadku małych ilości danych, gdy wydajność nie ma decydującego znaczenia, można zastosować dowolne narzędzie.  

> [!NOTE]
> Polybase jest najlepszym wyborem podczas ładowania lub eksportowania dużych ilości danych lub potrzebujesz szybszej wydajności.

Obciążenia funkcji PolyBase można uruchomić za pomocą instrukcji CTAS lub INSERT INTO. CTAS zminimalizuje rejestrowanie transakcji i jest najszybszym sposobem ładowania danych. Usługa Azure Data Factory obsługuje również obciążenia PolyBase i może osiągnąć wydajność podobną do CTAS. PolyBase obsługuje różne formaty plików, w tym pliki Gzip.

Aby zmaksymalizować przepustowość podczas korzystania z plików tekstowych Gzip, podziel pliki na 60 lub więcej plików, aby zmaksymalizować równoległość obciążenia. W celu uzyskania szybszej całkowitej przepływności warto rozważyć równoległe ładowania danych. Dodatkowe informacje dotyczące tematów związanych z tą sekcją znajdują się w następujących artykułach:

- [Ładowanie danych](data-loading-overview.md)
- [Przewodnik po funkcji PolyBase](data-loading-best-practices.md)
- [Wzorce i strategie ładowania puli SQL platformy Azure](https://blogs.msdn.microsoft.com/sqlcat/20../../azure-sql-data-warehouse-loading-patterns-and-strategies/)
- [Ładowanie danych przy użyciu usługi Azure Data Factory](../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Przenoszenie danych za pomocą usługi Azure Data Factory](../../data-factory/transform-data-using-machine-learning.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [Tworzenie tabeli jako wyboru (CTAS)](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

## <a name="load-then-query-external-tables"></a>Ładowanie i przesyłanie zapytań dotyczących tabel zewnętrznych

Polybase nie jest optymalna dla zapytań. Tabele polybase dla pul SQL obecnie obsługują tylko pliki obiektów blob platformy Azure i magazyn usługi Azure Data Lake. Pliki te nie mają żadnych zasobów obliczeniowych, które je wspierają. W rezultacie pule SQL nie można odciążyć tej pracy i musi odczytać cały plik, ładując go do tempdb, dzięki czemu można odczytać dane.

Jeśli masz kilka zapytań do wykonywania zapytań o te dane, lepiej załadować te dane raz i kwerendy użyć tabeli lokalnej. Dalsze wskazówki Polybase znajduje się w [przewodniku do korzystania z PolyBase](data-loading-best-practices.md) artykułu.

## <a name="hash-distribute-large-tables"></a>Dystrybucja dużych tabel z użyciem skrótów

Domyślnym sposobem dystrybucji tabel jest działanie okrężne.   Ta wartość domyślna ułatwia użytkownikom rozpoczęcie tworzenia tabel bez konieczności decydowania o sposobie dystrybucji ich tabel. Okrągłe tabele Robin może działać wystarczająco dla niektórych obciążeń. Ale w większości przypadków kolumna dystrybucji zapewnia lepszą wydajność.  

Najczęstszym przykładem tabeli dystrybuowanej przez kolumnę przewyższającą tabelę okrężnego robina jest połączenie dwóch dużych tabel faktów.  

Na przykład jeśli masz tabelę zamówień dystrybuowanych przez order_id, a tabela transakcji również dystrybuowana przez order_id, po połączeniu tabeli zamówień z tabelą transakcji w order_id, ta kwerenda staje się kwerendą przekazową. Operacje przenoszenia danych są następnie eliminowane. Mniej kroków to szybsze kwerendy. Mniejsza konieczność przenoszenia danych wpływa także na skrócenie czasu działania kwerend.

> [!NOTE]
> Podczas ładowania tabeli rozproszonej nie należy sortować danych przychodzących w kluczu dystrybucji. Spowoduje to spowolnienie obciążeń.

Poniższe linki do artykułu podają dodatkowe informacje na temat poprawy wydajności poprzez wybranie kolumny dystrybucyjnej. Ponadto znajdziesz informacje o tym, jak zdefiniować tabelę rozproszoną w klauzuli WITH instrukcji CREATE TABLE:

- [Omówienie tabeli](develop-tables-overview.md)
- [Dystrybucja tabel](../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Wybór dystrybucji tabel](https://blogs.msdn.microsoft.com/sqlcat/20../../choosing-hash-distributed-table-vs-round-robin-distributed-table-in-azure-sql-dw-service/)
- [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="do-not-over-partition"></a>Unikanie nadmiernego partycjonowania

Podczas partycjonowania danych może być skuteczne dla utrzymania danych za pośrednictwem przełączania partycji lub optymalizacji skanowania za pomocą eliminacji partycji, o zbyt wiele partycji może spowolnić zapytania.  Często strategia partycjonowania o wysokiej szczegółowości, która może działać dobrze na programie SQL Server, może nie działać dobrze w puli SQL.  

Mając zbyt wiele partycji można zmniejszyć skuteczność indeksów klastrowanego magazynu kolumn, jeśli każda partycja ma mniej niż 1 milion wierszy. Pule SQL automatycznie dzieli dane na 60 baz danych. Tak więc, jeśli utworzysz tabelę ze 100 partycjami, wynikiem będzie partycje 6000. Każde obciążenie jest inny, więc najlepszą radą jest eksperymentować z partycjonowania, aby zobaczyć, co działa najlepiej dla obciążenia.  

Jedną z opcji do rozważenia jest przy użyciu szczegółowości, która jest niższa niż zaimplementowano przy użyciu programu SQL Server. Na przykład należy rozważyć użycie partycji tygodniowych lub miesięcznych zamiast partycji dziennych.

Więcej informacji na temat partycjonowania jest szczegółowo w [tabeli partycjonowania](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) artykułu.

## <a name="minimize-transaction-sizes"></a>Minimalizowanie rozmiarów transakcji

Instrukcje INSERT, UPDATE i DELETE są uruchamiane w transakcji. Gdy się nie uda, muszą zostać wycofane. Aby zmniejszyć potencjał długiego wycofywania, należy zminimalizować rozmiary transakcji, gdy tylko jest to możliwe.  Minimalizowanie rozmiarów transakcji można wykonać, dzieląc instrukcje INSERT, UPDATE i DELETE na części. Na przykład, jeśli masz INSERT, które mają potrwać 1 godzinę, można podzielić INSERT na cztery części. Każdy bieg zostanie skrócony do 15 minut.  

> [!TIP]
> Wykorzystaj specjalne przypadki minimalnego rejestrowania, takie jak CTAS, TRUNCATE, DROP TABLE lub INSERT, aby opróżnić tabele, aby zmniejszyć ryzyko wycofywania.  

Innym sposobem na eliminację procesu wycofywania zmian jest korzystanie z operacji z właściwościami Tylko metadane, takich jak przełączanie partycji pod kątem zarządzania danymi.  Na przykład zamiast wykonać instrukcję DELETE, aby usunąć wszystkie wiersze w tabeli, w której order_date był w październiku 2001 r., można podzielić dane co miesiąc. Następnie można przełączyć partycję z danymi dla pustej partycji z innej tabeli (zobacz przykłady TABELI ALTER).  

W przypadku tabel bez partycjonowania należy rozważyć użycie CTAS do zapisania danych, które mają być zachowane w tabeli, a nie przy użyciu delete.  Jeśli CTAS zajmuje taką samą ilość czasu, jest znacznie bezpieczniejsze do uruchomienia, ponieważ ma minimalny rejestrowanie transakcji i może być anulowane szybko w razie potrzeby.

Dalsze informacje na temat treści związanych z tą sekcją znajdują się w poniższych artykułach:

- [Tworzenie tabeli jako wyboru (CTAS)](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Omówienie transakcji](develop-transactions.md)
- [Optymalizacja transakcji](../sql-data-warehouse/sql-data-warehouse-develop-best-practices-transactions.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Partycjonowanie tabeli](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [OBCINANIE TABELI](/sql/t-sql/statements/truncate-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [ALTER TABLE](/sql/t-sql/statements/alter-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="reduce-query-result-sizes"></a>Zmniejszanie rozmiarów wyników kwerendy

Zmniejszenie rozmiarów wyników kwerendy pomaga uniknąć problemów po stronie klienta spowodowanych przez wyniki dużych zapytań.  Kwerendę można edytować, aby zmniejszyć liczbę zwróconych wierszy. Niektóre narzędzia generowania zapytań umożliwiają dodawanie składni "top N" do każdej kwerendy.  Można również CETAS wynik kwerendy do tabeli tymczasowej, a następnie użyć PolyBase eksportu do przetwarzania downlevel.

## <a name="use-the-smallest-possible-column-size"></a>Użycie możliwie najmniejszego rozmiaru kolumny

Podczas definiowania DDL, należy użyć najmniejszego typu danych, który będzie obsługiwać dane w ten sposób poprawi wydajność kwerendy.  To zalecenie jest szczególnie ważne dla kolumn CHAR i VARCHAR.  Jeśli najdłuższa wartość w kolumnie ma 25 znaków, należy zdefiniować typ kolumny jako VARCHAR(25).  Należy unikać domyślnego definiowania wszystkich kolumn znaków jako kolumn długich wartości.  Ponadto zdefiniuj kolumny jako VARCHAR, gdy jest to wszystko, co jest potrzebne, a nie przy użyciu NVARCHAR.

Zobacz [omówienie tabeli](develop-tables-overview.md), [Typy danych tabeli](develop-tables-data-types.md)i [TWORZENIE tabeli](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) artykułów bardziej szczegółowy przegląd podstawowych pojęć istotnych dla powyższych informacji.

## <a name="use-temporary-heap-tables-for-transient-data"></a>Korzystanie z tymczasowych tabel stosów dla danych przejściowych

Gdy tymczasowo trwa to dane w pulach SQL, tabele sterty zazwyczaj przyspieszają ogólny proces.  Jeśli ładujesz dane tylko do etapu przed uruchomieniem więcej przekształceń, ładowanie tabeli do tabeli sterty będzie szybsze niż ładowanie danych do tabeli klastrowanego magazynu kolumn.  

Ładowanie danych do tabeli tymczasowej będzie również ładować znacznie szybciej niż ładowanie tabeli do magazynu trwałego.  Tabele tymczasowe zaczynają się od "#" i są dostępne tylko przez sesję, która go utworzyła. W związku z tym mogą one działać tylko w ograniczonych scenariuszach. Tabele stosu definiuje się przy użyciu klauzuli WITH instrukcji CREATE TABLE.  W przypadku użycia tabeli tymczasowej należy pamiętać o utworzeniu dla niej statystyk.

Dodatkowe wskazówki można znaleźć w [artykułach Tabela tymczasowa](/sql/t-sql/statements/alter-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [TWORZENIE TABELI](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)i [TWORZENIE TABELI JAKO WYBIERZ.](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="optimize-clustered-columnstore-tables"></a>Optymalizowanie tabel klastrowanego magazynu kolumn

Indeksy klastrowanego magazynu kolumn są jednym z najbardziej efektywnych sposobów przechowywania danych w puli SQL.  Domyślnie tabele w puli SQL są tworzone jako klastrowane ColumnStore.  Dla uzyskania najlepszej wydajności kwerend w odniesieniu do tabel magazynu kolumn ważne jest zapewnienie dobrej jakości segmentów.  Jeśli wiersze są zapisywane w tabelach magazynu kolumn przy dużym wykorzystaniu pamięci, może to spowodować obniżenie jakości segmentów w magazynie kolumn.  

Jakość segmentu może być mierzona liczbą wierszy w skompresowanej grupie wierszy. Zobacz [przyczyny niskiej jakości indeksu magazynu kolumn](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#causes-of-poor-columnstore-index-quality) w [tabeli indeksy](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) artykułu instrukcje krok po kroku dotyczące wykrywania i poprawy jakości segmentu dla klastrowanych tabel magazynu kolumn.  

Ponieważ segmenty magazynu kolumn wysokiej jakości są ważne, warto używać identyfikatorów użytkowników, które znajdują się w klasie średnich lub dużych zasobów do ładowania danych. Korzystanie z [niższych jednostek magazynu danych](resource-consumption-models.md) oznacza, że chcesz przypisać większą klasę zasobów do użytkownika ładującego.

Tabele magazynu kolumn zazwyczaj nie wypychają danych do skompresowanego segmentu magazynu kolumn, dopóki nie będzie więcej niż 1 miliona wierszy na tabelę. Każda tabela puli SQL jest podzielony na 60 tabel. W związku z tym tabele magazynu kolumn nie będą korzystać z kwerendy, chyba że tabela ma więcej niż 60 milionów wierszy.  

> [!TIP]
> W przypadku tabel z mniej niż 60 milionami wierszy indeks magazynu kolumn może nie być optymalnym rozwiązaniem.  

Jeśli partycji danych, każda partycja będzie musiał mieć 1 milion wierszy, aby korzystać z indeksu klastrowanego magazynu kolumn.  Dla tabeli ze 100 partycji musi mieć co najmniej 6 miliardów wierszy, aby korzystać z magazynu kolumn klastrowanych (60 dystrybucji *100 partycji* 1 milion wierszy).  

Jeśli tabela nie ma 6 miliardów wierszy, masz dwie główne opcje. Zmniejszyć liczbę partycji lub rozważyć użycie tabeli sterty zamiast.  Warto również eksperymentować, aby sprawdzić, czy można uzyskać lepszą wydajność przy użyciu tabeli sterty z indeksami pomocniczymi, a nie tabelą magazynu kolumn.

Podczas wykonywania zapytania odnoszącego się do tabeli magazynu kolumn kwerendy będą uruchamiane szybciej, jeśli wybrane zostaną tylko niezbędne kolumny.  Więcej informacji na temat indeksów tabel i magazynów kolumn i można znaleźć w [tabeli indeksy](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json), [Kolumna indeksy przewodnik](/sql/relational-databases/indexes/columnstore-indexes-overview?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)i [Odbudowa indeksy magazynu kolumn](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest#rebuilding-indexes-to-improve-segment-quality) artykuły.

## <a name="use-larger-resource-class-to-improve-query-performance"></a>Użycie większej klasy zasobów w celu poprawy wydajność przesyłania zapytań

Pule SQL używają grup zasobów jako sposobu przydzielania pamięci do kwerend. Początkowo wszyscy użytkownicy są przypisywani do małej klasy zasobów, która przyznaje 100 MB pamięci na dystrybucję.  Zawsze jest 60 dystrybucji. Każda dystrybucja otrzymuje co najmniej 100 MB. Całkowita alokacja pamięci w całym systemie wynosi 6000 MB, czyli niecałe 6 GB.  

Stosowanie wybranych kwerend, takich jak duże sprzężenia lub obciążenia odnoszące się do klastrowanych tabel magazynu kolumn, pozwolą korzystać z większych alokacji pamięci.  Niektóre zapytania, takie jak czyste skany, nie będą widoczne żadnych korzyści. Korzystanie z większych klas zasobów wpływa na współbieżność. Warto więc pamiętać o tych faktach przed przeniesieniem wszystkich użytkowników do dużej klasy zasobów.

Aby uzyskać dodatkowe informacje na temat klas zasobów, zapoznaj się [z resource klasy zarządzania obciążeniem](../sql-data-warehouse/resource-classes-for-workload-management.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) artykułu.

## <a name="use-smaller-resource-class-to-increase-concurrency"></a>Użyj mniejszej klasy zasobów, aby zwiększyć współbieżność

Jeśli zauważysz duże opóźnienie w kwerendach użytkowników, użytkownicy mogą być uruchomione w większych klasach zasobów. W tym scenariuszu promuje zużycie gniazd współbieżności, co może spowodować, że inne zapytania do kolejki w kolejce.  Aby ustalić, czy kwerendy użytkowników `SELECT * FROM sys.dm_pdw_waits` są umieszczane w kolejce, uruchom, aby sprawdzić, czy zwracane są wiersze.

[Klasy zasobów do zarządzania obciążeniem](../sql-data-warehouse/resource-classes-for-workload-management.md) i [sys.dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) artykuły zapewni Ci więcej informacji.

## <a name="use-dmvs-to-monitor-and-optimize-your-queries"></a>Korzystanie z widoków DMV do monitorowania i optymalizowania zapytań

Pule SQL mają kilka dmvs, które mogą służyć do monitorowania wykonywania kwerend.  Poniższy artykuł dotyczący monitorowania przedstawia instrukcje krok po kroku dotyczące sposobu wyświetlania szczegółów kwerendy wykonującej.  Aby szybko wyszukać zapytania w tych widokach DMV, z zapytaniami można użyć opcji LABEL. Aby uzyskać dodatkowe szczegółowe informacje, zapoznaj się z artykułami zawartymi na poniższej liście:

- [Monitor your workload using DMVs](../sql-data-warehouse/sql-data-warehouse-manage-monitor.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

- [Etykiety](develop-label.md)
- [OPTION](/sql/t-sql/queries/option-clause-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_exec_sessions](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sessions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_sql_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_dms_workers](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [DBCC PDW_SHOWEXECUTIONPLAN](/sql/t-sql/database-console-commands/dbcc-pdw-showexecutionplan-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="next-steps"></a>Następne kroki

Zobacz też artykuł [Rozwiązywanie problemów, aby](../sql-data-warehouse/sql-data-warehouse-troubleshoot.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) uzyskać typowe problemy i rozwiązania.

Jeśli potrzebujesz informacji, które nie zostały podane w tym artykule, użyj "Wyszukaj dokumenty" po lewej stronie tej strony, aby przeszukać wszystkie dokumenty puli SQL.  [Forum puli SQL](https://social.msdn.microsoft.com/Forums/sqlserver/home?forum=AzureSQLDataWarehouse) to miejsce, w które można zadawać pytania innym użytkownikom i grupie produktów puli SQL.  

Firma Microsoft aktywnie monitoruje to forum, aby mieć pewność, że użytkownicy uzyskują odpowiedzi od innych użytkowników lub pracowników firmy Microsoft.  Jeśli wolisz zadać pytania na przepełnienie stosu, mamy również [platformę Azure SQL pool Stack Overflow Forum](https://stackoverflow.com/questions/tagged/azure-sqldw).

W przypadku żądań funkcji użyj strony [Opinii puli SQL platformy Azure.](https://feedback.azure.com/forums/307516-sql-data-warehouse)  Dodawanie twoich żądań lub inne żądania w górę pomaga nam skupić się na najbardziej poszukiwanych funkcjach.
