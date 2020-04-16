---
title: Najlepsze rozwiązania dotyczące programowania synapse SQL
description: Zalecenia i najlepsze rozwiązania, które należy znać podczas opracowywania dla Synapse SQL.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 3356817fbaef955e05358f9c92619f3c04f747d7
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81429618"
---
# <a name="development-best-practices-for-synapse-sql"></a>Najlepsze rozwiązania dotyczące programowania synapse SQL
W tym artykule opisano wskazówki i najlepsze rozwiązania podczas opracowywania rozwiązania magazynu danych. 

## <a name="development-best-practices-for-synapse-sql"></a>Najlepsze rozwiązania dotyczące programowania synapse SQL

### <a name="reduce-cost-with-pause-and-scale"></a>Obniżenie kosztów dzięki wstrzymaniu i skalowaniu

Aby uzyskać więcej informacji dotyczących obniżania kosztów za pomocą wstrzymywania i skalowania, zobacz [Zarządzanie obliczeniami](../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

### <a name="maintain-statistics"></a>Prowadzenie statystyk

Upewnij się, że aktualizujesz statystyki codziennie lub po każdym obciążeniu.  Zawsze istnieje możliwość wypracowania kompromisu pomiędzy wydajnością a kosztami tworzenia i aktualizowania statystyk. Jeśli okaże się, że utrzymanie wszystkich statystyk trwa zbyt długo, bądź bardziej selektywny, jeśli kolumny mają statystyki lub które kolumny wymagają częstego aktualizowania.  

Na przykład można zaktualizować kolumny daty, w których nowe wartości mogą być dodawane codziennie. **Najwięcej korzyści można uzyskać dzięki statystykom kolumn zaangażowanych w sprzężenia, kolumnom używanym w klauzuli WHERE i kolumnach znalezionych w polu GROUP BY.**

Zobacz też [Zarządzanie statystykami tabeli](develop-tables-statistics.md), [TWORZENIE STATYSTYK](develop-tables-statistics.md), [AKTUALIZOWANIE STATYSTYK][UPDATE STATISTICS].

### <a name="hash-distribute-large-tables"></a>Dystrybucja dużych tabel z użyciem skrótów

Domyślnym sposobem dystrybucji tabel jest działanie okrężne.  Ułatwia to użytkownikom rozpoczęcie tworzenia tabel bez konieczności decydowania o sposobie dystrybucji ich tabel.  Okrągłe tabele Robin może działać wystarczająco dla niektórych obciążeń. Ale w większości przypadków wybranie kolumny dystrybucji będzie działać znacznie lepiej.  

Najbardziej typowym przykładem sytuacji, w której zastosowanie dla tabeli dystrybucji według kolumny przyniesie znacznie lepsze wyniki niż zastosowanie tabeli z działaniem okrężnym, jest połączenie dwóch dużych tabel faktów.  Na przykład jeśli masz tabelę zamówień, która jest dystrybuowana przez order_id, i tabelę transakcji, która jest również dystrybuowana przez order_id, po połączeniu tabeli zamówień z tabelą transakcji w order_id, ta kwerenda staje się kwerendą przekazową. Oznacza to, że eliminujemy operacje przenoszenia danych.  Mniej kroków to szybsze kwerendy.  Mniejsza konieczność przenoszenia danych wpływa także na skrócenie czasu działania kwerend.

Podczas ładowania dystrybuowanej tabeli należy upewnić się, że dane przychodzące nie są sortowane według klucza dystrybucji, ponieważ mogłoby to spowolnić ładowanie.  Zobacz poniższe łącza, aby uzyskać dodatkowe informacje na temat sposobu wyboru kolumny dystrybucyjnej może poprawić wydajność, a także jak zdefiniować tabelę rozproszoną w with klauzuli z create tabel instrukcji.

Zobacz też [omówienie tabeli][Table overview], [Rozkład tabeli][Table distribution], [Wybieranie rozkładu tabeli,][Selecting table distribution] [TWORZENIE TABELI][CREATE TABLE], [TWORZENIE TABELI JAKO WYBIERZ][CREATE TABLE AS SELECT].

### <a name="do-not-over-partition"></a>Unikanie nadmiernego partycjonowania
Podczas partycjonowania danych może być skuteczne dla utrzymania danych za pośrednictwem przełączania partycji lub optymalizacji skanowania za pomocą eliminacji partycji, o zbyt wiele partycji może spowolnić zapytania.  Często strategia partycjonowania o wysokiej szczegółowości, która może działać dobrze na programie SQL Server, może nie działać dobrze w puli SQL.  

Zbyt duża liczba partycji danych może także zmniejszyć skuteczność indeksów klastrowanego magazynu kolumn, jeśli każda partycja ma mniej niż milion wierszy. Pula SQL dzieli dane na 60 baz danych. Tak więc, jeśli utworzysz tabelę ze 100 partycjami, wynikiem będzie partycje 6000.  Każde obciążenie jest inne, więc warto eksperymentować z podziałem na partycje — w ten sposób można przekonać się, jakie rozwiązanie sprawdzi się najlepiej w przypadku danego obciążenia.  

Jedną z opcji do rozważenia jest przy użyciu ziarnistość, która jest niższa niż to, co może pracował dla Ciebie w programie SQL Server.  Można na przykład rozważyć wykorzystanie partycji cotygodniowych lub comiesięcznych zamiast partycji codziennych.

Zobacz też [Partycjonowanie tabeli][Table partitioning].

### <a name="minimize-transaction-sizes"></a>Minimalizowanie rozmiarów transakcji

Instrukcje INSERT, UPDATE i DELETE działają w obrębie transakcji i muszą zostać wycofane, jeśli zakończą się niepowodzeniem.  Aby zminimalizować ryzyko długiego czasu wycofywania, warto minimalizować rozmiary transakcji, gdy tylko jest to możliwe.  Można to zrobić poprzez podział instrukcji INSERT, UPDATE i DELETE na części.  Na przykład, jeśli masz INSERT, które mają potrwać 1 godzinę, można podzielić INSERT na cztery części, a tym samym skrócenie każdego uruchomienia do 15 minut.

Stosuj szczególne przypadki minimalnego rejestrowania, takie jak CTAS, TRUNCATE, DROP TABLE lub INSERT, do opróżniania tabel, aby zmniejszyć ryzyko wycofywania.  Innym sposobem na eliminację procesu wycofywania zmian jest korzystanie z operacji z właściwościami Tylko metadane, takich jak przełączanie partycji pod kątem zarządzania danymi.  Na przykład zamiast wykonywać instrukcję DELETE w celu usunięcia z tabeli wszystkich wierszy, dla których wartość data_zamowienia to październik 2001, można rozdzielić dane na miesiące, aby następnie przełączyć wybraną partycję na pustą partycję z innej tabeli (zobacz przykłady instrukcji ALTER TABLE).  

W przypadku tabel niepartycjonowanych zamiast korzystać z instrukcji DELETE, należy rozważyć użycie instrukcji CTAS do zapisu danych, które mają zostać zachowane w tabeli.  Jeśli wykonanie instrukcji CTAS trwa tyle samo czasu, to na jej korzyść nadal przemawia znacznie większe bezpieczeństwo. Jej uruchomienie wiąże się z minimalnym rejestrowaniem, przez co operacja może w razie potrzeby zostać szybko anulowana.

Zobacz też [Opis transakcji][Understanding transactions], [Optymalizacja transakcji][Optimizing transactions], [Partycjonowanie tabeli,][Table partitioning] [OBCINANIE TABELI,][TRUNCATE TABLE] [ZMIEŃ TABELĘ][ALTER TABLE], [Tworzenie tabeli jako wybierz (CTAS)][Create table as select (CTAS)].

### <a name="use-the-smallest-possible-column-size"></a>Użycie możliwie najmniejszego rozmiaru kolumny

Użycie podczas definiowania kwerendy DDL najmniejszego typu danych, który umożliwi obsługę danych, zwiększy wydajność kwerendy.  Jest to szczególnie ważne w przypadku kolumn CHAR i VARCHAR.  Jeśli najdłuższa wartość w kolumnie ma 25 znaków, należy zdefiniować typ kolumny jako VARCHAR(25).  Należy unikać domyślnego definiowania wszystkich kolumn znaków jako kolumn długich wartości.  Ponadto należy unikać stosowania kolumn NVARCHAR, jeśli zastosowanie typu VARCHAR spełni wymagania danego zastosowania.

Zobacz także artykuły [Omówienie tabel][Table overview], [Typy danych w tabelach](develop-tables-data-types.md) i [CREATE TABLE][CREATE TABLE].

### <a name="optimize-clustered-columnstore-tables"></a>Optymalizowanie tabel klastrowanego magazynu kolumn

Indeksy klastrowanego magazynu kolumn są jednym z najbardziej efektywnych sposobów przechowywania danych w puli SQL.  Domyślnie tabele w puli SQL są tworzone jako klastrowane ColumnStore.  Dla uzyskania najlepszej wydajności kwerend w odniesieniu do tabel magazynu kolumn ważne jest zapewnienie dobrej jakości segmentów.  Jeśli wiersze są zapisywane w tabelach magazynu kolumn przy dużym wykorzystaniu pamięci, może to spowodować obniżenie jakości segmentów w magazynie kolumn.  

Jakość segmentu określa się na podstawie liczby wierszy w skompresowanej grupie wierszy.  Zobacz sekcję [Przyczyny niskiej jakości indeksu magazynu kolumn][Causes of poor columnstore index quality] w artykule [Indeksy tabel][Table indexes], aby uzyskać instrukcje krok po kroku dotyczące sprawdzania i poprawiania jakości segmentu tabel klastrowanego magazynu kolumn.  Ponieważ segmenty magazynu kolumn wysokiej jakości są ważne, warto używać identyfikatorów użytkowników, które znajdują się w klasie średnich lub dużych zasobów do ładowania danych. Korzystanie z [niższych jednostek magazynu danych](azure -synapse-resource-consumption-models.md) oznacza, że chcesz przypisać większą klasę zasobów do użytkownika ładującego.

Ponieważ tabele magazynu kolumn zazwyczaj nie wypchnie danych do skompresowanego segmentu magazynu kolumn, dopóki nie będzie więcej niż 1 milion wierszy na tabelę, a każda tabela puli SQL jest podzielona na 60 tabel, tabele magazynu kolumn nie będą korzystać z kwerendy, chyba że tabela ma więcej niż 60 milionów wierszy.  W przypadku tabel z mniej niż 60 milionami wierszy indeks columstore może nie być optymalnym rozwiązaniem.  

Ponadto w przypadku partycjonowania danych warto wziąć pod uwagę, że każda partycja będzie musiała mieć milion wierszy, aby można było odnieść korzyść z zastosowania klastrowanego indeksu magazynu kolumn.  Jeśli tabela ma 100 partycji, to będzie musiał mieć co najmniej 6 miliardów wierszy, aby korzystać z magazynu kolumn klastrowanych (60 dystrybucji *100 partycji* 1 milion wierszy).  

Jeśli tabela nie ma 6 miliardów wierszy, zmniejsz liczbę partycji lub rozważ użycie tabeli sterty.  Warto również eksperymentować, aby sprawdzić, czy można uzyskać lepszą wydajność przy użyciu tabeli sterty z indeksami pomocniczymi, a nie tabelą magazynu kolumn.

Podczas wykonywania zapytania odnoszącego się do tabeli magazynu kolumn kwerendy będą uruchamiane szybciej, jeśli wybrane zostaną tylko niezbędne kolumny.  

Zobacz też [Indeksy tabeli][Table indexes], [Przewodnik po indeksach magazynu kolumn][Columnstore indexes guide], Przebudowa [indeksów magazynu kolumn][Rebuilding columnstore indexes].

### <a name="next-steps"></a>Następne kroki

Jeśli nie znalazłeś tego, czego szukasz w tym artykule, spróbuj użyć "Wyszukaj dokumenty" po lewej stronie tej strony, aby przeszukać wszystkie dokumenty puli SQL platformy Azure.  [Forum puli SQL platformy Azure][Azure SQL pool MSDN Forum] to miejsce, w które można zadawać pytania innym użytkownikom i grupie produktów puli SQL.  

Firma Microsoft aktywnie monitoruje to forum, aby mieć pewność, że użytkownicy uzyskują odpowiedzi od innych użytkowników lub pracowników firmy Microsoft.  Jeśli wolisz zadać pytania na przepełnienie stosu, mamy również [platformę Azure SQL pool Stack Overflow Forum][Azure SQL pool Stack Overflow Forum].

Strona [Opinie puli SQL platformy Azure][Azure SQL pool Feedback] służy do tworzenia żądań funkcji.  Dodawanie konkretnych propozycji lub głosowanie na już zgłoszone sugestie bardzo pomaga nam określać priorytety funkcji.

<!--Image references-->

<!--Article references-->
[Create a support ticket]: ../sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json
[Create table as select (CTAS)]: ../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json
[Table overview]:develop-tables-overview.md
[Table distribution]: ../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json
[Table indexes]: ../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json
[Causes of poor columnstore index quality]: ../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#causes-of-poor-columnstore-index-quality
[Rebuilding columnstore indexes]: ../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#rebuilding-indexes-to-improve-segment-quality
[Table partitioning]: ../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json
[Temporary tables]:develop-tables-temporary.md
[Guide for using PolyBase]:../sql-analytics/data-loading-best-practices.md
[Load data]: ../sql-data-warehouse/esign-elt-data-loading.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json
[Move data with Azure Data Factory]: ../../data-factory/transform-data-using-machine-learning.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json
[Load data with Azure Data Factory]: ../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json
[Load data with bcp]: /sql/tools/bcp-utility
[Load data with PolyBase]: ../sql-data-warehouse/load-data-wideworldimportersdw.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json
[Monitor your workload using DMVs]: ../sql-data-warehouse/sql-data-warehouse-manage-monitor.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json
[Pause compute resources]: ../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md#pause-compute-bk?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json
[Resume compute resources]: ../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md#resume-compute-bk?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json
[Scale compute resources]: ../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md#scale-compute?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json
[Understanding transactions]:develop-transactions.md
[Optimizing transactions]: ../sql-data-warehouse/sql-data-warehouse-develop-best-practices-transactions.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json
[Troubleshooting]: ../sql-data-warehouse/sql-data-warehouse-troubleshoot.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json
[LABEL]:develop-label.md

<!--MSDN references-->
[ALTER TABLE]: /sql/t-sql/statements/alter-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest
[CREATE EXTERNAL FILE FORMAT]: /sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest
[CREATE STATISTICS]: /sql/t-sql/statements/create-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest
[CREATE TABLE]: /sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest
[CREATE TABLE AS SELECT]: /sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest
[DBCC PDW_SHOWEXECUTIONPLAN]: /sql/t-sql/database-console-commands/dbcc-pdw-showexecutionplan-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest
[INSERT]: /sql/t-sql/statements/insert-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest
[OPTION]: /sql/t-sql/queries/option-clause-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest
[TRUNCATE TABLE]: /sql/t-sql/statements/truncate-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest
[UPDATE STATISTICS]: /sql/t-sql/statements/update-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest
[sys.dm_exec_sessions]: /sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sessions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest
[sys.dm_pdw_exec_requests]: /sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest
[sys.dm_pdw_request_steps]: /sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest
[sys.dm_pdw_sql_requests]: /sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest
[sys.dm_pdw_dms_workers]: /sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest
[sys.dm_pdw_waits]: /sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest
[Columnstore indexes guide]: /sql/relational-databases/indexes/columnstore-indexes-overview?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest

<!--Other Web references-->
[Selecting table distribution]: https://blogs.msdn.microsoft.com/sqlcat/20../../choosing-hash-distributed-table-vs-round-robin-distributed-table-in-azure-sql-dw-service/
[Azure SQL pool Feedback]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Azure SQL pool MSDN Forum]: https://social.msdn.microsoft.com/Forums/sqlserver/home?forum=AzureSQLDataWarehouse
[Azure SQL pool Stack Overflow Forum]:  https://stackoverflow.com/questions/tagged/azure-sqldw
[Azure SQL pool loading patterns and strategies]: https://blogs.msdn.microsoft.com/sqlcat/20../../azure-sql-data-warehouse-loading-patterns-and-strategies/

## <a name="development-best-practices-for-sql-on-demand-preview"></a>Najlepsze rozwiązania dotyczące programowania języka SQL na żądanie (wersja zapoznawcza)

### <a name="general-considerations"></a>Zagadnienia ogólne

SQL na żądanie umożliwia wykonywanie zapytań o pliki na kontach magazynu platformy Azure. Nie ma możliwości magazynu lokalnego lub pozyskiwania, co oznacza, że wszystkie pliki, które są docelowe kwerendy są zewnętrzne do SQL na żądanie. W związku z tym wszystko związane z odczytywaniem plików z magazynu może mieć wpływ na wydajność kwerendy.

### <a name="colocate-azure-storage-account-and-sql-on-demand"></a>Kolokowanie konta usługi Azure Storage i języka SQL na żądanie

 
Aby zminimalizować opóźnienia, współlokuj swoje konto magazynu platformy Azure i punkt końcowy SQL na żądanie. Konta magazynu i punkty końcowe aprowizowane podczas tworzenia obszaru roboczego znajdują się w tym samym regionie. 
 
Aby uzyskać optymalną wydajność, jeśli uzyskujesz dostęp do innych kont magazynu z SQL na żądanie, upewnij się, że znajdują się w tym samym regionie. W przeciwnym razie będzie zwiększone opóźnienie transferu sieciowego danych z regionu zdalnego do regionu punktu końcowego.

### <a name="azure-storage-throttling"></a>Ograniczanie przepustowości usługi Azure Storage

Wiele aplikacji i usług może uzyskać dostęp do konta magazynu. Gdy połączone we/wy usługi We/Wy lub przepływność generowane przez aplikacje, usługi i sql na żądanie obciążenia przekracza limity konta magazynu, występuje ograniczanie magazynu. Gdy występuje ograniczanie magazynu, istnieje znaczny negatywny wpływ na wydajność kwerendy. 
 
Po wykryciu ograniczania przepustowości sql na żądanie ma wbudowaną obsługę tego scenariusza. SQL na żądanie będzie żądania do magazynu w wolniejszym tempie, dopóki ograniczanie przepustowości zostanie rozwiązany. Jednak w celu optymalnego wykonywania kwerendy zaleca się, aby nie naprężać konta magazynu z innymi obciążeniami podczas wykonywania kwerendy.

### <a name="prepare-files-for-querying"></a>Przygotowywanie plików do wykonywania zapytań

Jeśli to możliwe, można przygotować pliki do lepszej wydajności:

- Konwertuj CSV na parkiet – Parkiet jest formatem kolumnowym. Ponieważ jest skompresowany, ma mniejsze rozmiary plików niż pliki CSV z tymi samymi danymi, a SQL na żądanie będzie potrzebował mniej czasu i żądań magazynu, aby go odczytać.
- Jeśli kwerenda jest przeznaczona dla pojedynczego dużego pliku, skorzystasz z podziału go na wiele mniejszych plików.
- Spróbuj zachować rozmiar pliku CSV poniżej 10 GB.
- Zaleca się, aby mieć pliki o jednakowym rozmiarze dla pojedynczej ścieżki OPENROWSET lub zewnętrznej lokalizacji tabeli.
- Partycjonowanie danych przez przechowywanie partycji do różnych folderów lub nazw plików - sprawdź [użyć funkcji nazwy pliku i ścieżki pliku, aby kierować określone partycje](#use-fileinfo-and-filepath-functions-to-target-specific-partitions).

### <a name="use-fileinfo-and-filepath-functions-to-target-specific-partitions"></a>Użyj funkcji fileinfo i filepath do kierowania określonych partycji

Dane są często zorganizowane w partycje. Można poinstruować sql na żądanie do kwerendy poszczególnych folderów i plików. Zmniejszy to liczbę plików i ilość danych, które kwerenda musi odczytać i przetworzyć. W związku z tym, można osiągnąć lepszą wydajność. Aby uzyskać więcej informacji, sprawdź funkcje [nazwy pliku](develop-storage-files-overview.md#filename-function) i [ścieżki plików](develop-storage-files-overview.md#filepath-function) oraz przykłady dotyczące [wykonywania zapytań](query-specific-files.md)o określone pliki .

Jeśli dane w magazynie nie jest podzielony na partycje, należy wziąć pod uwagę partycjonowanie go, dzięki czemu można użyć tych funkcji do optymalizacji kwerend docelowych tych plików.

Podczas [wykonywania zapytań o partycjonowane tabele platformy Spark](develop-storage-files-spark-tables.md) z języka SQL na żądanie, kwerenda będzie automatycznie kierować tylko pliki potrzebne.

### <a name="use-cetas-to-enhance-query-performance-and-joins"></a>Stosowanie cetasu w celu zwiększenia wydajności zapytań i sprzężenia

[CETAS](develop-tables-cetas.md) jest jedną z najważniejszych funkcji dostępnych w sql na żądanie. CETAS to operacja równoległa, która tworzy metadane tabeli zewnętrznej i eksportuje wynik kwerendy SELECT do zestawu plików na koncie magazynu.

Za pomocą programu CETAS można przechowywać często używane części zapytań, takie jak przyłączone tabele odwołań, w nowym zestawie plików. Później można dołączyć do tej pojedynczej tabeli zewnętrznej zamiast powtarzać wspólne sprzężenia w wielu kwerendach. Gdy CETAS generuje pliki parkietu, statystyki będą tworzone automatycznie, gdy pierwsze zapytanie będzie skierowane do tej zewnętrznej tabeli, a uzyskasz lepszą wydajność.
