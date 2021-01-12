---
title: Najlepsze rozwiązania dotyczące dedykowanej puli SQL (dawniej SQL DW)
description: Zalecenia i najlepsze rozwiązania dotyczące tworzenia rozwiązań dla dedykowanej puli SQL (dawniej SQL DW) w usłudze Azure Synapse Analytics.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: b17e47463ef3fe9a2cc959364825451468266f0e
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/12/2021
ms.locfileid: "98120023"
---
# <a name="best-practices-for-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics"></a>Najlepsze rozwiązania dotyczące dedykowanej puli SQL (dawniej SQL DW) w usłudze Azure Synapse Analytics

Ten artykuł stanowi zbiór najlepszych rozwiązań ułatwiających osiągnięcie optymalnej wydajności z [dedykowanej puli SQL (dawniej SQL DW)](sql-data-warehouse-overview-what-is.md) .  Celem tego artykułu jest udostępnienie podstawowych wskazówek i wyróżnienie ważnych obszarów fokusu.  

## <a name="reduce-cost-with-pause-and-scale"></a>Obniżenie kosztów dzięki wstrzymaniu i skalowaniu

Aby uzyskać więcej informacji dotyczących obniżania kosztów za pomocą wstrzymywania i skalowania, zobacz [Zarządzanie obliczeniami](sql-data-warehouse-manage-compute-overview.md).

## <a name="maintain-statistics"></a>Prowadzenie statystyk

Dedykowana Pula SQL (wcześniej SQL DW) może być skonfigurowana w taki sposób, aby automatycznie wykrywać i tworzyć statystyki dotyczące kolumn.  Plany zapytania utworzone przez optymalizator są tak dobre, jak w przypadku dostępnych statystyk.  

Zalecamy włączenie AUTO_CREATE_STATISTICS dla baz danych i regularne aktualizowanie statystyk codziennie lub po każdym załadowaniu, aby zapewnić, że statystyki dla kolumn używanych w zapytaniach są zawsze aktualne.

Jeśli okaże się, że aktualizacja wszystkich statystyk trwa zbyt długo, możesz spróbować bardziej wybiórczo, w których kolumnach są potrzebne częste aktualizacje statystyk. Można na przykład aktualizować kolumny danych, w których codziennie mogą być dodawane nowe wartości.

> [!TIP]
> W celu uzyskania największej korzyści można uzyskać zaktualizowane statystyki dotyczące kolumn w sprzężeniach, kolumn używanych w klauzuli WHERE i kolumnach, które znajdują się w grupie według.

Zobacz też [Zarządzanie statystykami tabel](sql-data-warehouse-tables-statistics.md), [CREATE STATISTICS](/sql/t-sql/statements/create-statistics-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)i [Update Statistics](/sql/t-sql/statements/update-statistics-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="use-dmvs-to-monitor-and-optimize-your-queries"></a>Korzystanie z widoków DMV do monitorowania i optymalizowania zapytań

Dedykowana Pula SQL (wcześniej SQL DW) ma kilka widoków DMV, których można użyć do monitorowania wykonywania zapytania.  W artykule [monitorowanie obciążenia przy użyciu widoków DMV](sql-data-warehouse-manage-monitor.md) artykułu przedstawiono szczegółowe instrukcje krok po kroku dotyczące sposobu przeglądania szczegółowych informacji o wykonywanej kwerendzie.  

Aby szybko wyszukać zapytania w tych widokach DMV, z zapytaniami można użyć opcji LABEL.

Zobacz też [monitorowanie obciążenia przy użyciu widoków DMV](sql-data-warehouse-manage-monitor.md), [Label](sql-data-warehouse-develop-label.md), [Option](/sql/t-sql/queries/option-clause-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest), [sys.dm_exec_sessions](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sessions-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest), [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest), [sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest), [sys.dm_pdw_sql_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest), [sys.dm_pdw_dms_workers](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest), [DBCC PDW_SHOWEXECUTIONPLAN](/sql/t-sql/database-console-commands/dbcc-pdw-showexecutionplan-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)i [sys.dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="tune-query-performance-with-new-product-enhancements"></a>Dostrajanie wydajności zapytań przy użyciu nowych ulepszeń produktu

- [Strojenie wydajności za pomocą zmaterializowanych widoków](performance-tuning-materialized-views.md)
- [Strojenie wydajności za pomocą uporządkowanego klastrowanego indeksu magazynu kolumn](performance-tuning-ordered-cci.md)
- [Strojenie wydajności za pomocą buforowania zestawu wyników](performance-tuning-result-set-caching.md)

## <a name="group-insert-statements-into-batches"></a>Grupowanie instrukcji INSERT w partie

Jednorazowe ładowanie do małej tabeli za pomocą instrukcji INSERT lub nawet okresowe ponowne ładowanie wyszukiwania może być odpowiednie dla potrzeb przy użyciu instrukcji, takiej jak `INSERT INTO MyLookup VALUES (1, 'Type 1')` .  

Jeśli jednak wymagane jest ładowanie tysięcy lub milionów wierszy na dzień, może okazać się, że same instrukcje INSERT zwyczajnie nie nadążą z działaniem.  Zamiast tego należy opracować procesy w taki sposób, aby zapisywały dane do pliku, po czym będzie następować okresowe uruchamianie innego procesu, który będzie ładować wspomniany plik.

Zobacz również [Wstawianie](/sql/t-sql/statements/insert-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="use-polybase-to-load-and-export-data-quickly"></a>Korzystanie z funkcji PolyBase do szybkiego ładowania i eksportowania danych

Dedykowana Pula SQL (dawniej SQL DW) obsługuje ładowanie i eksportowanie danych za poorednictwem kilku narzędzi, takich jak Azure Data Factory, Base i BCP.  W przypadku małych ilości danych, gdy wydajność nie ma decydującego znaczenia, można zastosować dowolne narzędzie.  Jednak podczas ładowania lub eksportowania dużych ilości danych lub w przypadku, gdy wymagana jest wysoka wydajność, najlepszym wyborem jest funkcja PolyBase.  

Baza danych została zaprojektowana tak, aby korzystała z rozproszonego charakteru systemu i załadowała i eksportuje znacznie szybciej niż inne narzędzia.  Obciążenia funkcji PolyBase można uruchomić za pomocą instrukcji CTAS lub INSERT INTO.   

> [!TIP]
> Użycie instrukcji CTAS minimalizuje rejestrowanie transakcji i stanowi najszybszy sposób ładowania danych.

Azure Data Factory obsługuje również obciążenia wielopodstawowe i mogą osiągać podobną wydajność jako CTAS.  Funkcja PolyBase obsługuje wiele formatów plików, w tym pliki Gzip.  
  
> [!NOTE]
> Aby zmaksymalizować przepływność przy użyciu plików tekstowych gzip, należy podzielić pliki na 60 lub więcej plików, aby zmaksymalizować równoległość obciążenia.  W celu uzyskania szybszej całkowitej przepływności warto rozważyć równoległe ładowania danych.

Zobacz też [ładowanie danych](design-elt-data-loading.md), [Przewodnik po użyciu zestawu Base](guidance-for-loading-data.md), [dedykowane wzorce ładowania puli SQL i strategie](/archive/blogs/sqlcat/), [ładowanie danych za pomocą Azure Data Factory]( ../../data-factory/load-azure-sql-data-warehouse.md), [przenoszenie danych za pomocą Azure Data Factory](../../data-factory/transform-data-using-machine-learning.md), [Tworzenie zewnętrznego formatu pliku](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)i [Tworzenie tabeli jako Select (CTAs)](sql-data-warehouse-develop-ctas.md).

## <a name="load-then-query-external-tables"></a>Ładowanie i przesyłanie zapytań dotyczących tabel zewnętrznych

O ile funkcja Polybase (nazywana też tabelami zewnętrznymi) może być najszybszym sposobem załadowania danych, nie jest to narzędzie zoptymalizowane pod kątem zapytań. Tabele bazowe obecnie obsługują tylko pliki obiektów blob platformy Azure i Magazyn Azure Data Lake. Te pliki nie mają żadnych zasobów obliczeniowych stanowiących ich zaplecze.  

W związku z tym dedykowana Pula SQL nie może odciążać tej pracy i dlatego musi odczytać cały plik przez załadowanie go do bazy danych tempdb, aby można było je odczytać.  W związku z tym mając kilka kwerend, które będą wykonywać zapytania dotyczące tych danych, lepiej jest załadować te dane jeden raz i zapewnić możliwość obejmowania zapytaniem lokalnej tabeli.

Zobacz też [Przewodnik dotyczący korzystania z bazy Base](guidance-for-loading-data.md).

## <a name="hash-distribute-large-tables"></a>Dystrybucja dużych tabel z użyciem skrótów

Domyślnym sposobem dystrybucji tabel jest działanie okrężne.  Dzięki temu użytkownicy mogą z łatwością rozpocząć tworzenie tabel bez konieczności podejmowania decyzji o tym, jak będzie przebiegać dystrybucja tabel.  Tabele wykorzystujące działanie okrężne mogą zapewniać wydajność wystarczającą dla niektórych obciążeń, ale w większości przypadków wybór kolumny dystrybucji sprawdzi się znacznie lepiej.  

Najbardziej typowym przykładem sytuacji, w której zastosowanie dla tabeli dystrybucji według kolumny przyniesie znacznie lepsze wyniki niż zastosowanie tabeli z działaniem okrężnym, jest połączenie dwóch dużych tabel faktów.  

Na przykład w przypadku tabeli z zamówieniami, która jest rozłożona według wartości id_zamowienia, oraz tabeli z transakcjami, która także jest rozłożona według wartości id_zamowienia, po połączeniu tabeli z zamówieniami z tabelą z transakcjami poprzez pola id_zamowienia, to zapytanie staje się kwerendą przekazującą, co oznacza, że zostają wyeliminowane operacje przenoszenia danych.  Mniej kroków to szybsze kwerendy.  Mniejsza konieczność przenoszenia danych wpływa także na skrócenie czasu działania kwerend.  

> [!TIP]
> Podczas ładowania dystrybuowanej tabeli należy upewnić się, że dane przychodzące nie są sortowane według klucza dystrybucji, ponieważ mogłoby to spowolnić ładowanie.  

Zobacz poniższe linki, aby uzyskać szczegółowe informacje o tym, jak wybór kolumny dystrybucji może poprawić wydajność, a także określić sposób definiowania rozproszonej tabeli w klauzuli WITH instrukcji CREATE TABLE.

Zobacz także [Omówienie tabel](sql-data-warehouse-tables-overview.md), [dystrybucja tabel](sql-data-warehouse-tables-distribute.md), [Wybieranie dystrybucji tabel](/archive/blogs/sqlcat/choosing-hash-distributed-table-vs-round-robin-distributed-table-in-azure-sql-dw-service), [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest), [CREATE TABLE jako wybrane](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="do-not-over-partition"></a>Unikanie nadmiernego partycjonowania

Gdy Partycjonowanie danych może być skuteczne do obsługi danych za pomocą przełączania partycji lub optymalizowania skanowania za pomocą eliminacji partycji, zbyt wiele partycji może spowalniać zapytania.  Często wysoce ziarnista strategia partycjonowania, która może być dobrze włączona SQL Server może nie współpracować w dedykowanej puli SQL (dawniej SQL DW).  

Zbyt duża liczba partycji danych może także zmniejszyć skuteczność indeksów klastrowanego magazynu kolumn, jeśli każda partycja ma mniej niż milion wierszy.  Należy pamiętać, że w tle, dedykowana Pula SQL dzieli dane na 60 baz danych, dlatego w przypadku utworzenia tabeli z partycjami 100 w rzeczywistości spowoduje to 6000 partycji.  

Każde obciążenie jest inne, więc warto eksperymentować z podziałem na partycje — w ten sposób można przekonać się, jakie rozwiązanie sprawdzi się najlepiej w przypadku danego obciążenia.  Warto rozważyć stopień szczegółowości niższy od pomyślnie zastosowanego w programie SQL Server.  Można na przykład rozważyć wykorzystanie partycji cotygodniowych lub comiesięcznych zamiast partycji codziennych.

Zobacz też [partycjonowanie tabel](sql-data-warehouse-tables-partition.md).

## <a name="minimize-transaction-sizes"></a>Minimalizowanie rozmiarów transakcji

Instrukcje INSERT, UPDATE i DELETE działają w obrębie transakcji i muszą zostać wycofane, jeśli zakończą się niepowodzeniem.  Aby zminimalizować ryzyko długiego czasu wycofywania, warto minimalizować rozmiary transakcji, gdy tylko jest to możliwe.  Można to zrobić poprzez podział instrukcji INSERT, UPDATE i DELETE na części.  

Na przykład jeśli masz WSTAWIENIE, którego oczekujesz 1 godziny, a jeśli to możliwe, Podziel na cztery części, które będą wykonywane w ciągu 15 minut.  Skorzystaj z specjalnych, minimalnych przypadków rejestrowania, takich jak CTAS, Truncate, DROP TABLE lub INSERT do pustych tabel, aby zmniejszyć ryzyko wycofywania.  

Innym sposobem na eliminację procesu wycofywania zmian jest korzystanie z operacji z właściwościami Tylko metadane, takich jak przełączanie partycji pod kątem zarządzania danymi.  Na przykład zamiast wykonywać instrukcję DELETE, aby usunąć wszystkie wiersze w tabeli, w której order_date była w październiku 2001, można podzielić dane na partycje miesięcznie, a następnie przełączać partycję z danymi dla pustej partycji z innej tabeli (zobacz [ALTER TABLE](/sql/t-sql/statements/alter-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) przykłady).  

W przypadku tabel niepartycjonowanych Rozważ użycie CTAS do zapisania danych, które mają być przechowywane w tabeli, a nie za pomocą polecenia DELETE.  Jeśli CTAS zajmuje ten sam czas, jest to znacznie bezpieczniejsza operacja, ponieważ ma minimalne rejestrowanie transakcji i można ją szybko anulować w razie potrzeby.

Zobacz też [Omówienie transakcji](sql-data-warehouse-develop-transactions.md), [Optymalizowanie transakcji](sql-data-warehouse-develop-best-practices-transactions.md), [partycjonowanie tabel](sql-data-warehouse-tables-partition.md), [TRUNCATE TABLE](/sql/t-sql/statements/truncate-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest), [ALTER TABLE](/sql/t-sql/statements/alter-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)i [CREATE TABLE as Select (CTAs)](sql-data-warehouse-develop-ctas.md).

## <a name="reduce-query-result-sizes"></a>Zmniejsz rozmiary wyników zapytania

Ten krok pozwala uniknąć problemów po stronie klienta spowodowanych przez duży wynik zapytania.  Możesz edytować zapytanie, aby zmniejszyć liczbę zwracanych wierszy. Niektóre narzędzia generowania zapytań umożliwiają dodanie składni "Top N" do każdego zapytania.  Możesz również CETAS wynik zapytania do tabeli tymczasowej, a następnie użyć eksportu bazowego dla przetwarzania niskiego poziomu.

## <a name="use-the-smallest-possible-column-size"></a>Użycie możliwie najmniejszego rozmiaru kolumny

Podczas definiowania kodu DDL przy użyciu najmniejszego typu danych, który będzie obsługiwał dane, poprawi wydajność zapytań.  To podejście jest szczególnie ważne w przypadku kolumn CHAR i VARCHAR.  

Jeśli najdłuższa wartość w kolumnie ma 25 znaków, należy zdefiniować typ kolumny jako VARCHAR(25).  Należy unikać domyślnego definiowania wszystkich kolumn znaków jako kolumn długich wartości.  Ponadto należy unikać stosowania kolumn NVARCHAR, jeśli zastosowanie typu VARCHAR spełni wymagania danego zastosowania.

Zobacz także [Omówienie tabel](sql-data-warehouse-tables-overview.md), [typy danych w tabeli](sql-data-warehouse-tables-data-types.md), [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="use-temporary-heap-tables-for-transient-data"></a>Korzystanie z tymczasowych tabel stosów dla danych przejściowych

Po tymczasowym wykorzystaniu danych, może się okazać, że użycie tabeli sterty zwiększy cały proces.  Jeśli dane są ładowane tylko pod kątem przygotowania do uruchomienia kolejnych przekształceń, załadowanie tabeli do stosu będzie znacznie szybsze niż załadowanie danych do tabeli klastrowanego magazynu kolumn.  

Ponadto załadowanie danych do tabeli tymczasowej będzie także znacznie szybsze niż załadowanie tabeli do pamięci trwałej.  Tabele tymczasowe zaczynają się od "#" i są dostępne tylko dla sesji, która ją utworzyła, więc mogą być tylko w ograniczonych scenariuszach.

Tabele stosu definiuje się przy użyciu klauzuli WITH instrukcji CREATE TABLE.  W przypadku użycia tabeli tymczasowej należy pamiętać o utworzeniu dla niej statystyk.

Zobacz również [tabele tymczasowe](sql-data-warehouse-tables-temporary.md), [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest), [CREATE TABLE jako wybrane](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="optimize-clustered-columnstore-tables"></a>Optymalizowanie tabel klastrowanego magazynu kolumn

Klastrowane indeksy magazynu kolumn to jeden z najbardziej wydajnych sposobów przechowywania danych w dedykowanej puli SQL.  Domyślnie tabele w dedykowanej puli SQL są tworzone jako klastrowane magazynu kolumn.  Dla uzyskania najlepszej wydajności kwerend w odniesieniu do tabel magazynu kolumn ważne jest zapewnienie dobrej jakości segmentów.  

Jeśli wiersze są zapisywane w tabelach magazynu kolumn przy dużym wykorzystaniu pamięci, może to spowodować obniżenie jakości segmentów w magazynie kolumn.  Jakość segmentu określa się na podstawie liczby wierszy w skompresowanej grupie wierszy.  Zobacz [przyczyny niskiej jakości indeksu magazynu kolumn](sql-data-warehouse-tables-index.md#causes-of-poor-columnstore-index-quality) w artykule [indeksy tabel](sql-data-warehouse-tables-index.md) , aby uzyskać instrukcje krok po kroku dotyczące wykrywania i poprawiania jakości segmentu dla klastrowanych tabel magazynu kolumn.  

Ponieważ duże jakości segmenty magazynu kolumn są ważne, dobrym pomysłem jest użycie identyfikatorów użytkowników, które znajdują się w średniej lub dużej klasie zasobów do ładowania danych. W przypadku korzystania z niższych [jednostek magazynu danych](what-is-a-data-warehouse-unit-dwu-cdwu.md) do użytkownika ładującego należy przypisać większą klasę zasobów.

Ponieważ tabele magazynu kolumn zwykle nie przepychają danych do skompresowanego segmentu magazynu kolumn, dopóki nie będzie więcej niż 1 000 000 wierszy na tabelę, a każda dedykowana tabela puli SQL zostanie podzielona na 60 tabel, a tabela magazynu kolumn nie będzie korzystać z kwerendy, chyba że zawiera ona więcej niż 60 000 000 wierszy.  Stosowanie indeksu magazynu kolumn może nie mieć sensu w przypadku tabel z mniej niż 60 milionami wierszy.  Jego użycie nie przyniesie też jednak niekorzystnych skutków.  

Ponadto w przypadku partycjonowania danych warto wziąć pod uwagę, że każda partycja będzie musiała mieć milion wierszy, aby można było odnieść korzyść z zastosowania klastrowanego indeksu magazynu kolumn.  Jeśli tabela ma 100 partycji, będzie musiała mieć co najmniej 6 000 000 000 wierszy do skorzystania z magazynu kolumn klastrowanych (60 distributions *100 partycje* 1 000 000 wiersze).  

Jeśli tabela w tym przykładzie nie ma 6 miliardów wierszy, zmniejsz liczbę partycji lub rozważ zastąpienie jej tabelą stosu.  Ponadto warto poeksperymentować, aby sprawdzić, czy lepszą wydajność można uzyskać, korzystając z tabeli stosu z indeksami pomocniczymi zamiast z tabeli magazynu kolumn.

> [!TIP]
> Podczas wykonywania zapytania odnoszącego się do tabeli magazynu kolumn kwerendy będą uruchamiane szybciej, jeśli wybrane zostaną tylko niezbędne kolumny.  

Zobacz także artykuły [Indeksy tabel](sql-data-warehouse-tables-index.md), [Przewodnik po indeksach magazynu kolumn](/sql/relational-databases/indexes/columnstore-indexes-overview?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) i [Ponowne tworzenie indeksów magazynu kolumn](sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality).

## <a name="use-larger-resource-class-to-improve-query-performance"></a>Użycie większej klasy zasobów w celu poprawy wydajność przesyłania zapytań

Dedykowana Pula SQL używa grup zasobów jako metody przydzielania pamięci do zapytań.  Poza tym wszyscy użytkownicy są przypisani do małej klasy zasobów, która przyznaje 100 MB pamięci na dystrybucję.  Jako że zawsze jest 60 dystrybucji i każda z nich ma przydzielone co najmniej 100 MB, całkowita alokacji pamięci w systemie wynosi 6000 MB lub nieco poniżej 6 GB.  

Stosowanie wybranych kwerend, takich jak duże sprzężenia lub obciążenia odnoszące się do klastrowanych tabel magazynu kolumn, pozwolą korzystać z większych alokacji pamięci.  Niektóre zapytania, takie jak czyste skanowania, nie będą miały żadnych korzyści.  Jednak wykorzystanie większych klas zasobów zmniejsza współbieżność, dlatego warto uwzględnić ten wpływ przed przeniesieniem wszystkich użytkowników do dużej klasy zasobów.

Zobacz też [klasy zasobów do zarządzania obciążeniami](resource-classes-for-workload-management.md).

## <a name="use-smaller-resource-class-to-increase-concurrency"></a>Użycie mniejszej klasy zasobów w celu zwiększenia współbieżności

Jeśli zauważysz, że zapytania użytkownika pozornie są długim opóźnieniem, może się zdarzyć, że użytkownicy pracują w większych klasach zasobów i zużywają wiele miejsc współbieżności, co sprawia, że inne zapytania mogą kolejkować się w górę.  Uruchom polecenie `SELECT * FROM sys.dm_pdw_waits`, aby to sprawdzić i zobaczyć, czy żadne wiersze nie są zwracane.

Zobacz też [klasy zasobów do zarządzania obciążeniami](resource-classes-for-workload-management.md), [sys.dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="other-resources"></a>Inne zasoby

Zobacz artykuł [Rozwiązywanie problemów](sql-data-warehouse-troubleshoot.md), w którym omówiono typowe problemy i ich rozwiązania.

Jeśli nie możesz znaleźć tego, czego szukasz w tym artykule, spróbuj użyć opcji "Wyszukaj dokumenty" w lewej części tej strony, aby przeszukać wszystkie dokumenty usługi Azure Synapse.  [Firma Microsoft&pytań i odpowiedzi na pytania dotyczące usługi Azure Synapse](/answers/topics/azure-synapse-analytics.html) to miejsce, w którym można zadawać pytania innym użytkownikom i grupom produktów platformy Azure Synapse. Firma Microsoft aktywnie monitoruje to forum, aby mieć pewność, że użytkownicy uzyskują odpowiedzi od innych użytkowników lub pracowników firmy Microsoft.  

Jeśli wolisz zadać pytania na Stack Overflow, oferujemy również [Forum usługi Azure Synapse Stack Overflow](https://stackoverflow.com/questions/tagged/azure-sqldw).

Użyj strony [opinii o usłudze Azure Synapse](https://feedback.azure.com/forums/307516-sql-data-warehouse) , aby wykonać żądania dotyczące funkcji.  Dodawanie konkretnych propozycji lub głosowanie na już zgłoszone sugestie bardzo pomaga nam określać priorytety funkcji.