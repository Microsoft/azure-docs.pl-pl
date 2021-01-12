---
title: Najlepsze rozwiązania dotyczące programowania
description: Zalecenia i najlepsze rozwiązania, które należy znać podczas opracowywania rozwiązań dla puli SQL Synapse.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 09/04/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: e8826dff6d347f78625272a5620a1f168b8804db
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/12/2021
ms.locfileid: "98120040"
---
# <a name="development-best-practices-for-synapse-sql-pool"></a>Najlepsze rozwiązania dotyczące programowania dla puli SQL Synapse

W tym artykule opisano wskazówki i najlepsze praktyki związane z opracowywaniem rozwiązania puli SQL.

## <a name="tune-query-performance-with-new-product-enhancements"></a>Dostrajanie wydajności zapytań przy użyciu nowych ulepszeń produktu

- [Strojenie wydajności za pomocą zmaterializowanych widoków](performance-tuning-materialized-views.md)
- [Strojenie wydajności za pomocą uporządkowanego klastrowanego indeksu magazynu kolumn](performance-tuning-ordered-cci.md)
- [Strojenie wydajności za pomocą buforowania zestawu wyników](performance-tuning-result-set-caching.md)

## <a name="reduce-cost-with-pause-and-scale"></a>Obniżenie kosztów dzięki wstrzymaniu i skalowaniu

Aby uzyskać więcej informacji na temat zmniejszania kosztów za pomocą wstrzymywania i skalowania, zobacz artykuł dotyczący [zarządzania obliczeniem](sql-data-warehouse-manage-compute-overview.md) .

## <a name="maintain-statistics"></a>Prowadzenie statystyk

Pulę SQL można skonfigurować do automatycznego wykrywania i tworzenia statystyk w kolumnach.  Plany zapytania utworzone przez optymalizator są tak dobre, jak w przypadku dostępnych statystyk.  

Zalecamy włączenie AUTO_CREATE_STATISTICS dla baz danych i regularne aktualizowanie statystyk codziennie lub po każdym załadowaniu, aby zapewnić, że statystyki dla kolumn używanych w zapytaniach są zawsze aktualne.

Jeśli okaże się, że aktualizacja wszystkich statystyk trwa zbyt długo, możesz spróbować bardziej wybiórczo, w których kolumnach są potrzebne częste aktualizacje statystyk. Można na przykład aktualizować kolumny danych, w których codziennie mogą być dodawane nowe wartości.

> [!TIP]
> Dzięki temu można uzyskać dostęp do wszystkich danych statystycznych dotyczących kolumn związanych z sprzężeniami, kolumn używanych w klauzuli WHERE i kolumn znalezionych w grupie Grupuj według.

Zobacz też [Zarządzanie statystykami tabel](sql-data-warehouse-tables-statistics.md), [CREATE STATISTICS](sql-data-warehouse-tables-statistics.md)i [Update Statistics](sql-data-warehouse-tables-statistics.md#update-statistics).

## <a name="hash-distribute-large-tables"></a>Dystrybucja dużych tabel z użyciem skrótów

Domyślnym sposobem dystrybucji tabel jest działanie okrężne.  Ten projekt ułatwia użytkownikom rozpoczęcie tworzenia tabel bez konieczności decydowania o sposobie dystrybuowania ich tabel.  

Tabele wykorzystujące działanie okrężne mogą zapewniać wydajność wystarczającą dla niektórych obciążeń, ale w większości przypadków wybór kolumny dystrybucji sprawdzi się znacznie lepiej.  Najbardziej typowym przykładem sytuacji, w której zastosowanie dla tabeli dystrybucji według kolumny przyniesie znacznie lepsze wyniki niż zastosowanie tabeli z działaniem okrężnym, jest połączenie dwóch dużych tabel faktów.  

Na przykład w przypadku tabeli z zamówieniami, która jest rozłożona według wartości id_zamowienia, oraz tabeli z transakcjami, która także jest rozłożona według wartości id_zamowienia, po połączeniu tabeli z zamówieniami z tabelą z transakcjami poprzez pola id_zamowienia, to zapytanie staje się kwerendą przekazującą, co oznacza, że zostają wyeliminowane operacje przenoszenia danych.  Mniej kroków to szybsze kwerendy.  Mniejsza konieczność przenoszenia danych wpływa także na skrócenie czasu działania kwerend.  

Podczas ładowania dystrybuowanej tabeli należy upewnić się, że dane przychodzące nie są sortowane według klucza dystrybucji, ponieważ mogłoby to spowolnić ładowanie.  Poniższe artykuły zawierają szczegółowe informacje na temat zwiększania wydajności, wybierając kolumnę dystrybucji i sposób definiowania rozproszonej tabeli w klauzuli WITH instrukcji CREATE TABLES.

Zobacz również [Omówienie tabeli](sql-data-warehouse-tables-overview.md), [dystrybucji tabel](sql-data-warehouse-tables-distribute.md), [wybierania dystrybucji tabel](/archive/blogs/sqlcat/choosing-hash-distributed-table-vs-round-robin-distributed-table-in-azure-sql-dw-service), [CREATE TABLE](sql-data-warehouse-tables-overview.md)i [CREATE TABLE jako wybrane](sql-data-warehouse-develop-ctas.md)

## <a name="do-not-over-partition"></a>Unikanie nadmiernego partycjonowania

Gdy Partycjonowanie danych może być skuteczne do obsługi danych za pomocą przełączania partycji lub optymalizowania skanowania za pomocą eliminacji partycji, zbyt wiele partycji może spowalniać zapytania.  

Często strategia partycjonowania o wysokiej szczegółowości, która może być dobrze włączona SQL Server może nie współpracować z pulą SQL.  Zbyt duża liczba partycji danych może także zmniejszyć skuteczność indeksów klastrowanego magazynu kolumn, jeśli każda partycja ma mniej niż milion wierszy.  

Należy pamiętać, że w tle Pula SQL dzieli Twoje dane na 60 baz danych, dlatego w przypadku utworzenia tabeli z partycjami 100 w rzeczywistości spowoduje to 6000 partycji.  Każde obciążenie jest inne, więc warto eksperymentować z podziałem na partycje — w ten sposób można przekonać się, jakie rozwiązanie sprawdzi się najlepiej w przypadku danego obciążenia.  

> [!TIP]
> Rozważ użycie mniejszego stopnia szczegółowości niż to, co mogło być w SQL Server.  Można na przykład rozważyć wykorzystanie partycji cotygodniowych lub comiesięcznych zamiast partycji codziennych.

Zobacz też [partycjonowanie tabel](sql-data-warehouse-tables-partition.md).

## <a name="minimize-transaction-sizes"></a>Minimalizowanie rozmiarów transakcji

Instrukcje INSERT, UPDATE i DELETE działają w obrębie transakcji i muszą zostać wycofane, jeśli zakończą się niepowodzeniem.  Aby zminimalizować ryzyko długiego czasu wycofywania, warto minimalizować rozmiary transakcji, gdy tylko jest to możliwe.  Można to zrobić poprzez podział instrukcji INSERT, UPDATE i DELETE na części.  

Na przykład jeśli masz WSTAWkę, której oczekujesz 1 godzinę, a jeśli to możliwe, Podziel na cztery części, które będą wykonywane w ciągu 15 minut.  Skorzystaj z specjalnych, minimalnych przypadków rejestrowania, takich jak CTAS, Truncate, DROP TABLE lub INSERT do pustych tabel, aby zmniejszyć ryzyko wycofywania.  

Innym sposobem na eliminację procesu wycofywania zmian jest korzystanie z operacji z właściwościami Tylko metadane, takich jak przełączanie partycji pod kątem zarządzania danymi.  Na przykład zamiast wykonywać instrukcję DELETE w celu usunięcia z tabeli wszystkich wierszy, dla których wartość data_zamowienia to październik 2001, można rozdzielić dane na miesiące, aby następnie przełączyć wybraną partycję na pustą partycję z innej tabeli (zobacz przykłady instrukcji ALTER TABLE).  

W przypadku tabel niepartycjonowanych Rozważ użycie CTAS do zapisania danych, które mają być przechowywane w tabeli, a nie za pomocą polecenia DELETE.  Jeśli CTAS zajmuje ten sam czas, jest to znacznie bezpieczniejsza operacja, ponieważ ma minimalne rejestrowanie transakcji i można ją szybko anulować w razie potrzeby.

Zobacz też [Omówienie transakcji](sql-data-warehouse-develop-transactions.md), [Optymalizowanie transakcji](sql-data-warehouse-develop-best-practices-transactions.md), [partycjonowanie tabel](sql-data-warehouse-tables-partition.md), [TRUNCATE TABLE](/sql/t-sql/statements/truncate-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest), [ALTER TABLE](/sql/t-sql/statements/alter-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)i [CREATE TABLE as Select (CTAs)](sql-data-warehouse-develop-ctas.md).

## <a name="use-the-smallest-possible-column-size"></a>Użycie możliwie najmniejszego rozmiaru kolumny

Podczas definiowania kodu DDL przy użyciu najmniejszego typu danych, który będzie obsługiwał dane, poprawi wydajność zapytań.  To podejście jest szczególnie ważne w przypadku kolumn CHAR i VARCHAR.  

Jeśli najdłuższa wartość w kolumnie ma 25 znaków, należy zdefiniować typ kolumny jako VARCHAR(25).  Należy unikać domyślnego definiowania wszystkich kolumn znaków jako kolumn długich wartości.  Ponadto należy unikać stosowania kolumn NVARCHAR, jeśli zastosowanie typu VARCHAR spełni wymagania danego zastosowania.

Zobacz także [Omówienie tabel](sql-data-warehouse-tables-overview.md), [typy danych tabeli](sql-data-warehouse-tables-data-types.md)i [CREATE TABLE](sql-data-warehouse-tables-overview.md).

## <a name="optimize-clustered-columnstore-tables"></a>Optymalizowanie tabel klastrowanego magazynu kolumn

Klastrowane indeksy magazynu kolumn to jeden z najbardziej wydajnych sposobów przechowywania danych w puli SQL.  Domyślnie tabele w puli SQL są tworzone jako klastrowane magazynu kolumn.  

> [!NOTE]
> Aby osiągnąć optymalną wydajność dla zapytań w tabelach magazynu kolumn, należy pamiętać o dobrej jakości segmentów.  

Jeśli wiersze są zapisywane w tabelach magazynu kolumn przy dużym wykorzystaniu pamięci, może to spowodować obniżenie jakości segmentów w magazynie kolumn.  Jakość segmentu określa się na podstawie liczby wierszy w skompresowanej grupie wierszy.  

Zobacz [przyczyny niskiej jakości indeksu magazynu kolumn](sql-data-warehouse-tables-index.md#causes-of-poor-columnstore-index-quality) w artykule [indeksy tabel](sql-data-warehouse-tables-index.md) , aby uzyskać instrukcje krok po kroku dotyczące wykrywania i poprawiania jakości segmentu dla klastrowanych tabel magazynu kolumn.  

Ponieważ duże jakości segmenty magazynu kolumn są ważne, dobrym pomysłem jest użycie identyfikatorów użytkowników, które znajdują się w średniej lub dużej klasie zasobów do ładowania danych. W przypadku korzystania z niższych [jednostek magazynu danych](what-is-a-data-warehouse-unit-dwu-cdwu.md) do użytkownika ładującego należy przypisać większą klasę zasobów.

Ponieważ tabele magazynu kolumn zwykle nie przepychają danych do skompresowanego segmentu magazynu kolumn, dopóki nie będzie więcej niż 1 000 000 wierszy na tabelę, a każda tabela puli SQL jest podzielona na 60 tabel, zazwyczaj tabele magazynu kolumn nie będą korzystać z zapytań, chyba że tabela ma więcej niż 60 000 000 wierszy.  

W przypadku tabeli zawierającej mniej niż 60 000 000 wierszy może nie mieć sensu, aby mieć indeks magazynu kolumn.  Jego użycie nie przyniesie też jednak niekorzystnych skutków.  

Ponadto w przypadku partycjonowania danych warto wziąć pod uwagę, że każda partycja będzie musiała mieć milion wierszy, aby można było odnieść korzyść z zastosowania klastrowanego indeksu magazynu kolumn.  Jeśli tabela ma 100 partycji, będzie musiała mieć co najmniej 6 000 000 000 wierszy do skorzystania z magazynu kolumn klastrowanych (60 distributions *100 partycje* 1 000 000 wiersze).  

Jeśli tabela w tym przykładzie nie ma 6 miliardów wierszy, zmniejsz liczbę partycji lub rozważ zastąpienie jej tabelą stosu.  Ponadto warto poeksperymentować, aby sprawdzić, czy lepszą wydajność można uzyskać, korzystając z tabeli stosu z indeksami pomocniczymi zamiast z tabeli magazynu kolumn.

> [!TIP]
> Podczas wykonywania zapytania odnoszącego się do tabeli magazynu kolumn kwerendy będą uruchamiane szybciej, jeśli wybrane zostaną tylko niezbędne kolumny.  

Zobacz również [indeksy tabel](sql-data-warehouse-tables-index.md), [Przewodnik po indeksach magazynu kolumn](/sql/relational-databases/indexes/columnstore-indexes-overview?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)i ponowne [Kompilowanie indeksów magazynu kolumn](sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality).

## <a name="next-steps"></a>Następne kroki

Jeśli nie możesz znaleźć tego, czego szukasz w tym artykule, spróbuj użyć opcji "Wyszukaj dokumenty" w lewej części tej strony, aby przeszukać wszystkie dokumenty usługi Azure Synapse.  

[Firma Microsoft&pytań i odpowiedzi na pytania dotyczące usługi Azure Synapse](/answers/topics/azure-synapse-analytics.html) to miejsce, w którym można zadawać pytania innym użytkownikom i grupom produktów platformy Azure Synapse.  Firma Microsoft aktywnie monitoruje to forum, aby mieć pewność, że użytkownicy uzyskują odpowiedzi od innych użytkowników lub pracowników firmy Microsoft.  

Jeśli wolisz zadać pytania na Stack Overflow, oferujemy również [Forum usługi Azure Synapse Analytics Stack overflowe](https://stackoverflow.com/questions/tagged/azure-sqldw).

Użyj strony [opinii o usłudze Azure Synapse](https://feedback.azure.com/forums/307516-sql-data-warehouse) , aby wykonać żądania dotyczące funkcji.  Dodawanie konkretnych propozycji lub głosowanie na już zgłoszone sugestie bardzo pomaga nam określać priorytety funkcji.