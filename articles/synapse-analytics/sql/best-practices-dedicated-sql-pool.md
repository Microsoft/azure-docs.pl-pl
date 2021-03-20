---
title: Najlepsze rozwiązania dotyczące dedykowanych pul SQL
description: Zalecenia i najlepsze rozwiązania, które należy znać podczas pracy z dedykowanymi pulami SQL.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 03/17/2021
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: b11a76be94fc52285482e13dadbc8c7c92af1374
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104609787"
---
# <a name="best-practices-for-dedicated-sql-pools-in-azure-synapse-analytics"></a>Najlepsze rozwiązania dotyczące dedykowanych pul SQL w usłudze Azure Synapse Analytics

Ten artykuł zawiera zbiór najlepszych rozwiązań ułatwiających osiągnięcie optymalnej wydajności dedykowanych pul SQL w usłudze Azure Synapse Analytics. Poniżej znajdziesz podstawowe wskazówki i ważne obszary umożliwiające skoncentrowanie się na tworzeniu rozwiązania. Każda sekcja wprowadza do koncepcji koncepcję, a następnie wskazuje na bardziej szczegółowe artykuły, które obejmują koncepcję bardziej szczegółowo.

## <a name="dedicated-sql-pools-loading"></a>Ładowanie dedykowanych pul SQL

Aby uzyskać wskazówki dotyczące ładowania dedykowanych pul SQL, zobacz [wskazówki dotyczące ładowania danych](data-loading-best-practices.md).

## <a name="reduce-cost-with-pause-and-scale"></a>Obniżenie kosztów dzięki wstrzymaniu i skalowaniu

Aby uzyskać więcej informacji na temat zmniejszania kosztów poprzez Wstrzymywanie i skalowanie, zobacz [Zarządzanie obliczeniami](../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

## <a name="maintain-statistics"></a>Prowadzenie statystyk

Dedykowanej puli SQL można skonfigurować do automatycznego wykrywania i tworzenia statystyk w kolumnach.  Plany zapytania utworzone przez optymalizator są tak dobre, jak w przypadku dostępnych statystyk.  

Zalecamy włączenie AUTO_CREATE_STATISTICS dla baz danych i regularne aktualizowanie statystyk codziennie lub po każdym załadowaniu, aby zapewnić, że statystyki dla kolumn używanych w zapytaniach są zawsze aktualne.

Aby skrócić czas konserwacji statystyk, należy powiedzieć się, które kolumny mają statystykę lub które wymagają najczęściej wykonywanej aktualizacji. Na przykład możesz chcieć zaktualizować kolumny dat, w których nowe wartości mogą być dodawane codziennie. Skup się na statystyce dotyczącej kolumn związanych z sprzężeniami, kolumnami używanymi w klauzuli WHERE i kolumnami, które znajdują się w grupie według.

Dodatkowe informacje na temat statystyk można znaleźć w artykułach [Zarządzaj statystykami tabel](develop-tables-statistics.md), [CREATE STATISTICS](/sql/t-sql/statements/create-statistics-transact-sql?view=azure-sqldw-latest&preserve-view=true)i [Update Statistics](/sql/t-sql/statements/update-statistics-transact-sql?view=azure-sqldw-latest&preserve-view=true) .

## <a name="tune-query-performance-with-new-product-enhancements"></a>Dostrajanie wydajności zapytań przy użyciu nowych ulepszeń produktu

- [Strojenie wydajności za pomocą zmaterializowanych widoków](../sql-data-warehouse/performance-tuning-materialized-views.md)
- [Strojenie wydajności za pomocą uporządkowanego klastrowanego indeksu magazynu kolumn](../sql-data-warehouse/performance-tuning-ordered-cci.md)
- [Strojenie wydajności za pomocą buforowania zestawu wyników](../sql-data-warehouse/performance-tuning-result-set-caching.md)


## <a name="group-insert-statements-into-batches"></a>Grupowanie instrukcji INSERT w partie

Jednorazowe ładowanie do małej tabeli za pomocą instrukcji INSERT, takiej jak, `INSERT INTO MyLookup VALUES (1, 'Type 1')` może być najlepszym rozwiązaniem w zależności od Twoich potrzeb. Jeśli jednak zachodzi potrzeba ładowania tysięcy lub milionów wierszy w ciągu dnia, prawdopodobnie pojedyncze wstawienia nie są optymalne.

Jednym ze sposobów rozwiązania tego problemu jest opracowanie jednego procesu, który zapisuje dane do pliku, a następnie inny proces okresowo ładuje ten plik. Aby uzyskać więcej informacji, zapoznaj się z artykułem dotyczącym [wstawiania](/sql/t-sql/statements/insert-transact-sql?view=azure-sqldw-latest&preserve-view=true) .

## <a name="use-polybase-to-load-and-export-data-quickly"></a>Korzystanie z funkcji PolyBase do szybkiego ładowania i eksportowania danych

Dedykowana Pula SQL obsługuje ładowanie i eksportowanie danych za poorednictwem kilku narzędzi, takich jak Azure Data Factory, Base i BCP.  W przypadku małych ilości danych, gdy wydajność nie ma decydującego znaczenia, można zastosować dowolne narzędzie.  

> [!NOTE]
> Podstawa jest najlepszym wyborem podczas ładowania lub eksportowania dużych ilości danych lub potrzebna jest większa wydajność.

Obciążenia funkcji PolyBase można uruchomić za pomocą instrukcji CTAS lub INSERT INTO. CTAS minimalizuje rejestrowanie transakcji i jest najszybszym sposobem ładowania danych. Azure Data Factory obsługuje również obciążenia bazowe i mogą osiągnąć wydajność podobną do CTAS. Baza Base obsługuje różne formaty plików, w tym pliki gzip.

Aby zmaksymalizować przepływność przy użyciu plików tekstowych gzip, należy podzielić pliki na 60 lub więcej plików, aby zmaksymalizować równoległość obciążenia. W celu uzyskania szybszej całkowitej przepływności warto rozważyć równoległe ładowania danych. Dodatkowe informacje dotyczące tematów odpowiednich dla tej sekcji znajdują się w następujących artykułach:

- [Ładowanie danych](../sql-data-warehouse/design-elt-data-loading.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Przewodnik po funkcji PolyBase](data-loading-best-practices.md)
- [Wzorce i strategie ładowania dedykowanej puli SQL](/archive/blogs/sqlcat/azure-sql-data-warehouse-loading-patterns-and-strategies)
- [Ładowanie danych przy użyciu usługi Azure Data Factory](../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Przenoszenie danych za pomocą usługi Azure Data Factory](../../data-factory/transform-data-using-machine-learning.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql?view=azure-sqldw-latest&preserve-view=true)
- [Utwórz tabelę jako wybraną (CTAS)](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

## <a name="load-then-query-external-tables"></a>Ładowanie i przesyłanie zapytań dotyczących tabel zewnętrznych

Baza nie jest optymalna dla zapytań. Tabele bazowe dla dedykowanych pul SQL obsługują obecnie tylko pliki obiektów blob platformy Azure i Magazyn Azure Data Lake. Te pliki nie mają żadnych zasobów obliczeniowych. W związku z tym dedykowane pule SQL nie mogą odciążać tej pracy i muszą odczytywać cały plik przez załadowanie go do bazy danych tempdb, dzięki czemu może odczytywać dane.

Jeśli masz kilka zapytań na potrzeby wykonywania zapytań dotyczących tych danych, lepiej jest ładować te dane jednokrotnie, a kwerendy używają tabeli lokalnej. Dalsze wskazówki podstawowe są zawarte w  [przewodniku dotyczącym korzystania z artykułu bazowego](data-loading-best-practices.md) .

## <a name="hash-distribute-large-tables"></a>Dystrybucja dużych tabel z użyciem skrótów

Domyślnym sposobem dystrybucji tabel jest działanie okrężne.   To ustawienie domyślne ułatwia użytkownikom Rozpoczynanie tworzenia tabel bez konieczności decydowania o sposobie dystrybuowania ich tabel. Tabele działające w trybie okrężnym mogą być wystarczająco wydajne w przypadku niektórych obciążeń. Jednak w większości przypadków kolumna dystrybucji zapewnia lepszą wydajność.  

Najbardziej typowym przykładem tabeli dystrybuowanej przez kolumnę, która przeprowadzi tabelę działania okrężnego, jest to, że dwa duże tabele faktów są sprzężone.  

Na przykład jeśli masz tabelę zamówień dystrybuowaną przez order_id, a tabela transakcji jest dystrybuowana przez order_id, po dołączeniu tabeli Orders do tabeli transakcji w order_id, to zapytanie zostanie przesłane do zapytania przekazującego. Operacje przenoszenia danych są następnie eliminowane. Mniej kroków to szybsze kwerendy. Mniejsza konieczność przenoszenia danych wpływa także na skrócenie czasu działania kwerend.

> [!TIP]
> Podczas ładowania rozproszonej tabeli dane przychodzące nie powinny być sortowane w kluczu dystrybucji. Wykonanie tej operacji spowoduje spowolnienie ładowania.

Poniższe linki artykułu zawierają dodatkowe informacje o ulepszaniu wydajności poprzez wybranie kolumny dystrybucji. Ponadto znajdziesz informacje na temat sposobu definiowania rozproszonej tabeli w klauzuli WITH instrukcji CREATE TABLE:

- [Przegląd tabeli](develop-tables-overview.md)
- [Dystrybucja tabel](../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Wybór dystrybucji tabel](/archive/blogs/sqlcat/choosing-hash-distributed-table-vs-round-robin-distributed-table-in-azure-sql-dw-service)
- [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?view=azure-sqldw-latest&preserve-view=true)
- [CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?view=azure-sqldw-latest&preserve-view=true)

## <a name="do-not-over-partition"></a>Unikanie nadmiernego partycjonowania

Gdy Partycjonowanie danych może być skuteczne do obsługi danych za pomocą przełączania partycji lub optymalizowania skanowania za pomocą eliminacji partycji, zbyt wiele partycji może spowalniać zapytania.  Często wysoce ziarnista strategia partycjonowania, która może być dobrze włączona SQL Server może nie współpracować z dedykowaną pulą SQL.  

Zbyt wiele partycji może zmniejszyć efektywność klastrowanych indeksów magazynu kolumn, jeśli każda partycja ma mniej niż 1 000 000 wierszy. dedykowane pule SQL automatycznie dzielą dane na 60 baz danych. Dlatego, jeśli utworzysz tabelę z 100 partycji, wynik będzie wynosić 6000 partycji. Każde obciążenie jest inne, więc najlepszą wskazówką jest przeprowadzenie eksperymentu z partycjonowaniem, aby zobaczyć, co najlepiej sprawdza się w obciążeniu.  

Jedną z opcji do rozważenia jest użycie stopnia szczegółowości, który jest niższy niż wdrożony przy użyciu SQL Server. Rozważmy na przykład użycie partycji tygodniowych lub miesięcznych zamiast partycji dziennych.

Więcej informacji na temat partycjonowania znajduje się w artykule [partycjonowanie tabeli](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) .

## <a name="minimize-transaction-sizes"></a>Minimalizowanie rozmiarów transakcji

Instrukcje INSERT, UPDATE i DELETE są uruchamiane w transakcji. Gdy nie powiodą się, muszą zostać wycofane. Aby zmniejszyć prawdopodobieństwo długotrwałego wycofywania, Zminimalizuj rozmiary transakcji, jeśli jest to możliwe.  Aby zminimalizować rozmiary transakcji, można podzielić instrukcje INSERT, UPDATE i DELETE na części. Na przykład jeśli masz WSTAWIENIE, którego oczekujesz na 1 godzinę, możesz przerwać Wstawianie do czterech części. Każdy przebieg zostanie skrócony do 15 minut.  

> [!TIP]
> Skorzystaj z specjalnych, minimalnych przypadków rejestrowania, takich jak CTAS, Truncate, DROP TABLE lub INSERT do pustych tabel, aby zmniejszyć ryzyko wycofywania.  

Innym sposobem na eliminację procesu wycofywania zmian jest korzystanie z operacji z właściwościami Tylko metadane, takich jak przełączanie partycji pod kątem zarządzania danymi.  Na przykład zamiast wykonywać instrukcję DELETE, aby usunąć wszystkie wiersze w tabeli, w której order_date była w październiku 2001, można podzielić dane co miesiąc. Następnie można wyłączyć partycję z danymi dla pustej partycji z innej tabeli (zobacz ALTER TABLE przykłady).  

W przypadku tabel niepartycjonowanych Rozważ użycie CTAS do zapisania danych, które mają być przechowywane w tabeli, a nie za pomocą polecenia DELETE.  Jeśli CTAS zajmuje ten sam czas, jest znacznie bezpieczniejsze do uruchomienia, ponieważ ma minimalne rejestrowanie transakcji i można je szybko anulować w razie potrzeby.

Dalsze informacje dotyczące zawartości powiązanej z tą sekcją znajdują się w poniższych artykułach:

- [Utwórz tabelę jako wybraną (CTAS)](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Omówienie transakcji](develop-transactions.md)
- [Optymalizacja transakcji](../sql-data-warehouse/sql-data-warehouse-develop-best-practices-transactions.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Partycjonowanie tabel](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [TRUNCATE TABLE](/sql/t-sql/statements/truncate-table-transact-sql?view=azure-sqldw-latest&preserve-view=true)
- [ALTER TABLE](/sql/t-sql/statements/alter-table-transact-sql?view=azure-sqldw-latest&preserve-view=true)

## <a name="reduce-query-result-sizes"></a>Zmniejsz rozmiary wyników zapytania

Zmniejszenie rozmiaru wyników zapytania pomaga uniknąć problemów po stronie klienta spowodowanych przez duże wyniki zapytania.  Możesz edytować zapytanie, aby zmniejszyć liczbę zwracanych wierszy. Niektóre narzędzia generowania zapytań umożliwiają dodanie składni "Top N" do każdego zapytania.  Możesz również CETAS wynik zapytania do tabeli tymczasowej, a następnie użyć eksportu bazowego dla przetwarzania niskiego poziomu.

## <a name="use-the-smallest-possible-column-size"></a>Użycie możliwie najmniejszego rozmiaru kolumny

Podczas definiowania kodu DDL należy użyć najmniejszego typu danych, który będzie obsługiwał Twoje dane w taki sposób, że poprawi wydajność zapytań.  To zalecenie jest szczególnie ważne w przypadku kolumn CHAR i VARCHAR.  Jeśli najdłuższa wartość w kolumnie ma 25 znaków, należy zdefiniować typ kolumny jako VARCHAR(25).  Należy unikać domyślnego definiowania wszystkich kolumn znaków jako kolumn długich wartości.  Ponadto należy zdefiniować kolumny jako VARCHAR, gdy jest to wszystko, co jest zbędne zamiast używać NVARCHAR.

Zapoznaj się z [omówieniem tabeli](develop-tables-overview.md), [typami danych tabeli](develop-tables-data-types.md)i artykułami [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?view=azure-sqldw-latest&preserve-view=true) , aby zapoznać się z bardziej szczegółowym omówieniem najważniejszych koncepcji dotyczących powyższych informacji.

## <a name="use-temporary-heap-tables-for-transient-data"></a>Korzystanie z tymczasowych tabel stosów dla danych przejściowych

Po tymczasowym wykorzystaniu danych z dedykowanych pul SQL tabele sterty zwykle przyspieszają cały proces.  Jeśli dane są ładowane tylko w celu przygotowania ich przed uruchomieniem większej przekształceń, załadowanie tabeli do tabeli sterty będzie szybsze niż ładowanie danych do tabeli klastrowanej magazynu kolumn.  

Ładowanie danych do tabeli tymczasowej również ładuje się znacznie szybciej niż w przypadku ładowania tabeli do magazynu trwałego.  Tabele tymczasowe zaczynają się od "#" i są dostępne tylko dla sesji, która ją utworzyła. W związku z tym mogą one być wykonywane tylko w ograniczonych scenariuszach. Tabele stosu definiuje się przy użyciu klauzuli WITH instrukcji CREATE TABLE.  W przypadku użycia tabeli tymczasowej należy pamiętać o utworzeniu dla niej statystyk.

Aby uzyskać dodatkowe wskazówki, zapoznaj się z [tabelami tymczasowymi](/sql/t-sql/statements/alter-table-transact-sql?view=azure-sqldw-latest&preserve-view=true), [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?view=azure-sqldw-latest&preserve-view=true)i [CREATE TABLE jako artykuły SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?view=azure-sqldw-latest&preserve-view=true) .

## <a name="optimize-clustered-columnstore-tables"></a>Optymalizowanie tabel klastrowanego magazynu kolumn

Klastrowane indeksy magazynu kolumn to jeden z najbardziej wydajnych sposobów przechowywania danych w dedykowanej puli SQL.  Domyślnie tabele w dedykowanej puli SQL są tworzone jako klastrowane magazynu kolumn.  Dla uzyskania najlepszej wydajności kwerend w odniesieniu do tabel magazynu kolumn ważne jest zapewnienie dobrej jakości segmentów.  Jeśli wiersze są zapisywane w tabelach magazynu kolumn przy dużym wykorzystaniu pamięci, może to spowodować obniżenie jakości segmentów w magazynie kolumn.  

Jakość segmentu może być mierzona przez liczbę wierszy w skompresowanej grupie wierszy. Zobacz [przyczyny niskiej jakości indeksu magazynu kolumn](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#causes-of-poor-columnstore-index-quality) w artykule [indeksy tabel](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) , aby uzyskać instrukcje krok po kroku dotyczące wykrywania i poprawiania jakości segmentu dla klastrowanych tabel magazynu kolumn.  

Ponieważ duże jakości segmenty magazynu kolumn są ważne, dobrym pomysłem jest użycie identyfikatorów użytkowników, które znajdują się w średniej lub dużej klasie zasobów do ładowania danych. W przypadku korzystania z niższych [jednostek magazynu danych](resource-consumption-models.md) do użytkownika ładującego należy przypisać większą klasę zasobów.

Tabele magazynu kolumn zwykle nie przepychają danych do skompresowanego segmentu magazynu kolumn, dopóki nie będzie więcej niż 1 000 000 wierszy na tabelę. Każda dedykowana tabela puli SQL jest podzielona na 60 tabel. W związku z tym tabele magazynu kolumn nie będą korzystać z zapytania, chyba że tabela ma więcej niż 60 000 000 wierszy.  

> [!TIP]
> W przypadku tabel zawierających mniej niż 60 000 000 wierszy posiadanie indeksu magazynu kolumn może nie być najlepszym rozwiązaniem.  

W przypadku partycjonowania danych każda partycja będzie musiała mieć 1 000 000 wierszy do skorzystania z klastrowanego indeksu magazynu kolumn.  W przypadku tabeli z partycjami 100 należy mieć co najmniej 6 000 000 000 wierszy, które mogą być korzystne z magazynu kolumn klastrowanych (60 dystrybucje *100 partycje* 1 000 000).  

Jeśli tabela nie zawiera 6 000 000 000 wierszy, dostępne są dwie opcje główne. Zmniejsz liczbę partycji lub Rozważ użycie w zamian tabeli sterty.  Może być również warto eksperymentować, aby sprawdzić, czy lepsza wydajność może być uzyskana przy użyciu tabeli sterty z indeksami pomocniczymi, a nie z tabeli magazynu kolumn.

Podczas wykonywania zapytania odnoszącego się do tabeli magazynu kolumn kwerendy będą uruchamiane szybciej, jeśli wybrane zostaną tylko niezbędne kolumny.  Więcej informacji na temat indeksów tabeli i magazynu kolumn i można znaleźć w poniższych artykułach:
- [Indeksy tabel](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Przewodnik po indeksach magazynu kolumn](/sql/relational-databases/indexes/columnstore-indexes-overview?view=azure-sqldw-latest&preserve-view=true)
- [Ponowne tworzenie indeksów magazynu kolumn](../sql-data-warehouse/sql-data-warehouse-tables-index.md?view=azure-sqldw-latest&preserve-view=true#rebuilding-indexes-to-improve-segment-quality) 
- [Strojenie wydajności za pomocą uporządkowanego klastrowanego indeksu magazynu kolumn](../sql-data-warehouse/performance-tuning-ordered-cci.md)

## <a name="use-larger-resource-class-to-improve-query-performance"></a>Użycie większej klasy zasobów w celu poprawy wydajność przesyłania zapytań

Pule SQL używają grup zasobów jako metody przydzielania pamięci do zapytań. Początkowo wszyscy użytkownicy są przypisani do małej klasy zasobów, która przyznaje 100 MB pamięci na dystrybucję.  Zawsze są dystrybuowane 60. Każda dystrybucja ma co najmniej 100 MB. Całkowita alokacja pamięci całego systemu to 6 000 MB lub zaledwie 6 GB.  

Stosowanie wybranych kwerend, takich jak duże sprzężenia lub obciążenia odnoszące się do klastrowanych tabel magazynu kolumn, pozwolą korzystać z większych alokacji pamięci.  Niektóre zapytania, takie jak czyste skanowania, nie będą miały żadnych korzyści. Wykorzystanie większych klas zasobów wpływa na współbieżność. Dlatego przed przeniesieniem wszystkich użytkowników do dużej klasy zasobów warto pamiętać o tych faktach.

Aby uzyskać dodatkowe informacje na temat klas zasobów, zapoznaj się z artykułem [klasy zasobów dla zarządzania obciążeniami](../sql-data-warehouse/resource-classes-for-workload-management.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) .

## <a name="use-smaller-resource-class-to-increase-concurrency"></a>Użyj mniejszej klasy zasobów, aby zwiększyć współbieżność

Jeśli zauważysz długie opóźnienie zapytań użytkowników, użytkownicy mogą pracować w większych klasach zasobów. Ten scenariusz promuje zużycie miejsc współbieżności, co może spowodować Zakolejkowanie innych zapytań.  Aby określić, czy zapytania użytkowników są umieszczane w kolejce, uruchom polecenie, `SELECT * FROM sys.dm_pdw_waits` Aby sprawdzić, czy wiersze są zwracane.

[Klasy zasobów dotyczące zarządzania obciążeniami](../sql-data-warehouse/resource-classes-for-workload-management.md) i artykułów [sys.dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?view=azure-sqldw-latest&preserve-view=true) zawierają więcej informacji.

## <a name="use-dmvs-to-monitor-and-optimize-your-queries"></a>Korzystanie z widoków DMV do monitorowania i optymalizowania zapytań

Dedykowane pule SQL mają kilka widoków DMV, których można użyć do monitorowania wykonywania zapytań.  W artykule dotyczącym monitorowania poniżej przedstawiono instrukcje krok po kroku dotyczące sposobu wyświetlania szczegółowych informacji o wykonywaniu zapytania.  Aby szybko wyszukać zapytania w tych widokach DMV, z zapytaniami można użyć opcji LABEL. Aby uzyskać więcej szczegółowych informacji, zobacz artykuły zawarte na poniższej liście:

- [Monitor your workload using DMVs](../sql-data-warehouse/sql-data-warehouse-manage-monitor.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

- [OZNAKOWAN](develop-label.md)
- [ZAZNACZYĆ](/sql/t-sql/queries/option-clause-transact-sql?view=azure-sqldw-latest&preserve-view=true)
- [sys.dm_exec_sessions](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sessions-transact-sql?view=azure-sqldw-latest&preserve-view=true)
- [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?view=azure-sqldw-latest&preserve-view=true)
- [sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?view=azure-sqldw-latest&preserve-view=true)
- [sys.dm_pdw_sql_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql?view=azure-sqldw-latest&preserve-view=true)
- [sys.dm_pdw_dms_workers](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql?view=azure-sqldw-latest&preserve-view=true)
- [DBCC PDW_SHOWEXECUTIONPLAN](/sql/t-sql/database-console-commands/dbcc-pdw-showexecutionplan-transact-sql?view=azure-sqldw-latest&preserve-view=true)
- [sys.dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?view=azure-sqldw-latest&preserve-view=true)

## <a name="next-steps"></a>Następne kroki

Zobacz również artykuł [Rozwiązywanie problemów](../sql-data-warehouse/sql-data-warehouse-troubleshoot.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) z typowymi problemami i rozwiązaniami.

Jeśli potrzebujesz informacji, które nie zostały podane w tym artykule, przeszukaj [&stronie pytań na temat usługi Azure Synapse](/answers/topics/azure-synapse-analytics.html) , aby zadawać pytania innym użytkownikom i grupie produktów Azure Synapse Analytics.  

Firma Microsoft aktywnie monitoruje to forum, aby mieć pewność, że użytkownicy uzyskują odpowiedzi od innych użytkowników lub pracowników firmy Microsoft.  Jeśli wolisz zadać pytania na Stack Overflow, oferujemy również [Forum usługi Azure Synapse Analytics Stack overflowe](https://stackoverflow.com/questions/tagged/azure-synapse).

W przypadku żądań funkcji Skorzystaj ze strony [opinii o analizie usługi Azure Synapse](https://feedback.azure.com/forums/307516-sql-data-warehouse) .  Dodanie żądań lub innych żądań głosowania pozwala nam skupić się na większości funkcji na żądanie.