---
title: Limity pojemności dla dedykowanej puli SQL
description: Maksymalne wartości dozwolone dla różnych składników dedykowanej puli SQL w usłudze Azure Synapse Analytics.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 2/19/2020
ms.author: martinle
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: d778844fee8cad9359532ffa23e177bf7b13c4b8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98117694"
---
# <a name="capacity-limits-for-dedicated-sql-pool-in-azure-synapse-analytics"></a>Limity pojemności dla dedykowanej puli SQL w usłudze Azure Synapse Analytics

Maksymalne wartości dozwolone dla różnych składników dedykowanej puli SQL w usłudze Azure Synapse Analytics.

## <a name="workload-management"></a>Zarządzanie obciążeniem

| Kategoria | Opis | Maksimum |
|:--- |:--- |:--- |
| [Jednostki magazynu danych (jednostek dwu)](what-is-a-data-warehouse-unit-dwu-cdwu.md) |Maksymalna jednostek dwu dla pojedynczej dedykowanej puli SQL  | Gen1: DW6000<br></br>Gen2: DW30000c |
| [Jednostki magazynu danych (jednostek dwu)](what-is-a-data-warehouse-unit-dwu-cdwu.md) |Domyślna wartość DTU na serwer |54 000<br></br>Domyślnie każdy serwer SQL (na przykład myserver.database.windows.net) ma limit przydziału jednostek DTU wynoszący 54 000, który umożliwia DW6000c. Ten limit przydziału jest po prostu limitem bezpieczeństwa. Możesz zwiększyć limit przydziału, [tworząc bilet pomocy technicznej](sql-data-warehouse-get-started-create-support-ticket.md) i wybierając pozycję *przydział* jako typ żądania.  Aby obliczyć potrzeby jednostek DTU, pomnóż 7,5 przez łączną potrzebną jednostek dwu lub pomnożenie 9 przez łączną cDWU potrzebną. Na przykład:<br></br>DW6000 x 7,5 = 45 000 DTU<br></br>DW7500c x 9 = 67 500 DTU.<br></br>Bieżące użycie jednostek DTU można wyświetlić z poziomu opcji programu SQL Server w portalu. Limit przydziału jednostek DTU obejmuje zarówno wstrzymane, jak i niewstrzymane bazy danych. |
| Połączenie z bazą danych |Maksymalna liczba otwartych sesji współbieżnych |1024<br/><br/>Liczba równoczesnych otwartych sesji różni się w zależności od wybranej jednostek dwu. DWU600c i nowsze obsługują maksymalnie 1024 otwartych sesji. DWU500c i poniżej, obsługują maksymalny limit współbieżnych sesji otwierania wynoszący 512. Uwaga Istnieją limity liczby zapytań, które mogą być wykonywane współbieżnie. Po przekroczeniu limitu współbieżności żądanie przechodzi do kolejki wewnętrznej, w której czeka na przetworzenie. |
| Połączenie z bazą danych |Maksymalna ilość pamięci dla przygotowanych instrukcji |20 MB |
| [Zarządzanie obciążeniem](resource-classes-for-workload-management.md) |Maksymalna liczba współbieżnych zapytań |128<br/><br/>  Zostanie wykonane maksymalnie 128 współbieżnych zapytań, a pozostałe zapytania zostaną dodane do kolejki.<br/><br/>Liczba współbieżnych zapytań może się zmniejszyć, gdy użytkownicy są przypisani do wyższych klas zasobów lub gdy ustawienie [jednostki magazynu danych](memory-concurrency-limits.md) jest mniejsze. Niektóre zapytania, takie jak zapytania DMV, są zawsze dozwolone do uruchomienia i nie wpływają na współbieżny limit zapytań. Aby uzyskać więcej informacji na temat współbieżnego wykonywania zapytań, zobacz artykuł [maksymalne wartości współbieżności](memory-concurrency-limits.md) . |
| [tempdb](sql-data-warehouse-tables-temporary.md) |Maksymalna ilość GB |399 GB na DW100c. W DWU1000c baza danych tempdb ma rozmiar 3,99 TB. |
||||

## <a name="database-objects"></a>Obiekty bazy danych

| Kategoria | Opis | Maksimum |
|:--- |:--- |:--- |
| baza danych |Maksymalny rozmiar | Gen1:240 TB skompresowane na dysku. Ta przestrzeń jest niezależna od bazy danych tempdb lub miejsca w dzienniku, dlatego to miejsce jest przeznaczone do trwałych tabel.  Szacuje się, że kompresja magazynu kolumn jest w pięciokrotną.  Ta kompresja pozwala na zwiększenie rozmiaru bazy danych do około 1 PB, gdy wszystkie tabele są klastrowane z magazynu kolumn (domyślny typ tabeli). <br/><br/> Gen2: nieograniczony magazyn dla tabel magazynu kolumn.  Część magazynu wierszy bazy danych jest nadal ograniczona do 240 TB skompresowanej na dysku. |
| Tabela |Maksymalny rozmiar |Nieograniczony rozmiar dla tabel magazynu kolumn. <br>60 TB dla tabel magazynu wierszy skompresowanych na dysku. |
| Tabela |Tabele na bazę danych | 100 000 |
| Tabela |Kolumny na tabelę |1024 kolumn |
| Tabela |Bajtów na kolumnę |Zależne od [typu danych](sql-data-warehouse-tables-data-types.md)kolumny. Limit wynosi 8000 dla typów danych char, 4000 dla nvarchar lub 2 GB w przypadku MAKSYMALNYch typów danych. |
| Tabela |Bajty na wiersz, zdefiniowany rozmiar |8060 bajtów<br/><br/>Liczba bajtów na wiersz jest obliczana w taki sam sposób, jak w przypadku SQL Server z kompresją strony. Podobnie jak SQL Server, magazyn przepełnienia wierszy jest obsługiwany, co umożliwia wypchnięcie **kolumn o zmiennej długości** poza wierszem. Gdy wiersze o zmiennej długości są wypychane poza wierszem, tylko 24-bajtowy katalog główny jest przechowywany w rekordzie głównym. Aby uzyskać więcej informacji, zobacz [dane przepełnienia wierszy przekraczające 8 KB](/previous-versions/sql/sql-server-2008-r2/ms186981(v=sql.105)). |
| Tabela |Partycje na tabelę |15 000<br/><br/>W celu zapewnienia wysokiej wydajności zalecamy zminimalizowanie liczby potrzebnych partycji przy zachowaniu wymagań firmy. Wraz ze wzrostem liczby partycji, obciążenie dla operacji języka definicji danych (DDL) i języka manipulowania danymi (DML) zwiększa się i powoduje wolniejszą wydajność. |
| Tabela |Liczba znaków na wartość graniczną partycji. |4000 |
| Indeks |Indeksy nieklastrowane na tabelę. |50<br/><br/>Dotyczy tylko tabel magazynu wierszy. |
| Indeks |Indeksy klastrowane na tabelę. |1<br><br/>Dotyczy zarówno magazynu wierszy, jak i tabel magazynu kolumn. |
| Indeks |Rozmiar klucza indeksu. |900 bajtów.<br/><br/>Dotyczy tylko indeksów magazynu wierszy.<br/><br/>Można utworzyć indeksy kolumn varchar o maksymalnym rozmiarze większym niż 900 bajtów, jeśli podczas tworzenia indeksu istniejące dane w kolumnach nie przekraczają 900 bajtów. Jednak późniejsze operacje wstawiania lub aktualizowania w kolumnach, które powodują, że całkowity rozmiar przekracza 900 bajtów będzie kończyć się niepowodzeniem. |
| Indeks |Kolumny kluczy na indeks. |16<br/><br/>Dotyczy tylko indeksów magazynu wierszy. Klastrowane indeksy magazynu kolumn obejmują wszystkie kolumny. |
| Statystyki |Rozmiar połączonych wartości kolumn. |900 bajtów. |
| Statystyki |Kolumny na obiekt statystyk. |32 |
| Statystyki |Statystyka utworzona dla kolumn na tabelę. |30 000 |
| Procedury składowane |Maksymalne poziomy zagnieżdżenia. |8 |
| Widok |Kolumny na widok |1 024 |
||||

## <a name="loads"></a>Powoduje

| Kategoria | Opis | Maksimum |
|:--- |:--- |:--- |
| Obciążenia wielopodstawowe |MB na wiersz |1<br/><br/>Liczba wierszy ładowania bazy jest mniejsza niż 1 MB. Ładowanie typów danych obiektów LOB do tabel z klastrowanym indeksem magazynu kolumn (WIK) nie jest obsługiwane.<br/> |
|Obciążenia wielopodstawowe|Łączna liczba plików|1 000 000<br/><br/>Obciążenia wielopodstawowe nie mogą przekroczyć więcej niż 1M plików. Może wystąpić następujący błąd: **operacja nie powiodła się, ponieważ liczba podziałów przekracza górną granicę 1000000**.|

## <a name="queries"></a>Zapytania

| Kategoria | Opis | Maksimum |
|:--- |:--- |:--- |
| Zapytanie |Zakolejkowane zapytania w tabelach użytkownika. |1000 |
| Zapytanie |Współbieżne zapytania w widokach systemu. |100 |
| Zapytanie |Zakolejkowane zapytania w widokach systemu |1000 |
| Zapytanie |Parametry maksymalne |2098 |
| Batch |Rozmiar maksymalny |65536 * 4096 |
| Wybierz wyniki |Kolumny na wiersz |4096<br/><br/>Nie można nigdy mieć więcej niż 4096 kolumn na wiersz w wyniku SELECT. Nie ma gwarancji, że zawsze możesz mieć 4096. Jeśli plan zapytania wymaga tabeli tymczasowej, może zostać zastosowana Maksymalna liczba kolumn 1024 na tabelę. |
| SELECT |Zagnieżdżone podzapytania |32<br/><br/>W instrukcji SELECT nigdy nie można korzystać z więcej niż 32 zagnieżdżonych podzapytań. Nie ma gwarancji, że zawsze możesz mieć 32. Na przykład SPRZĘŻENIe może wprowadzić podzapytanie do planu zapytania. Liczbę podzapytań można również ograniczyć przez dostępną pamięć. |
| SELECT |Kolumny na SPRZĘŻENIe |1024 kolumn<br/><br/>W SPRZĘŻENIu nigdy nie można mieć więcej niż 1024 kolumn. Nie ma gwarancji, że zawsze możesz mieć 1024. Jeśli plan SPRZĘŻENIa wymaga tabeli tymczasowej zawierającej więcej kolumn niż wynik SPRZĘŻENIa, limit 1024 dotyczy tabeli tymczasowej. |
| SELECT |Bajtów na grupę według kolumn. |8060<br/><br/>Kolumny w klauzuli GROUP BY mogą mieć maksymalnie 8060 bajtów. |
| SELECT |Liczba bajtów na kolejność według kolumn |8060 bajtów<br/><br/>Kolumny w klauzuli ORDER BY mogą mieć maksymalnie 8060 bajtów. |
| Identyfikatory na instrukcję |Liczba przywoływanych identyfikatorów |65 535<br/><br/> Liczba identyfikatorów, które mogą być zawarte w pojedynczym wyrażeniu zapytania, jest ograniczona. Przekroczenie tej liczby spowoduje SQL Server błędu 8632. Aby uzyskać więcej informacji, zobacz [błąd wewnętrzny: osiągnięto limit usług Expression Services](https://support.microsoft.com/help/913050/error-message-when-you-run-a-query-in-sql-server-2005-internal-error-a). |
| Literały ciągu | Liczba literałów ciągu w instrukcji | 20 000 <br/><br/>Liczba stałych ciągów w pojedynczym wyrażeniu zapytania jest ograniczona. Przekroczenie tej liczby spowoduje SQL Server błędu 8632.|
||||

## <a name="metadata"></a>Metadane

| Widok systemu | Maksymalna liczba wierszy |
|:--- |:--- |
| sys.dm_pdw_component_health_alerts |10 000 |
| sys.dm_pdw_dms_cores |100 |
| sys.dm_pdw_dms_workers |Łączna liczba procesów roboczych usługi DMS dla najnowszych żądań SQL 1000. |
| sys.dm_pdw_errors |10 000 |
| sys.dm_pdw_exec_requests |10 000 |
| sys.dm_pdw_exec_sessions |10 000 |
| sys.dm_pdw_request_steps |Łączna liczba kroków dla najnowszych żądań SQL 1000 przechowywanych w sys.dm_pdw_exec_requests. |
| sys.dm_pdw_os_event_logs |10 000 |
| sys.dm_pdw_sql_requests |Najnowsze 1000 żądań SQL, które są przechowywane w sys.dm_pdw_exec_requests. |
|||

## <a name="next-steps"></a>Następne kroki

Aby zapoznać się z zaleceniami dotyczącymi korzystania z usługi Azure Synapse, zobacz [Arkusz Ściągawka](cheat-sheet.md).