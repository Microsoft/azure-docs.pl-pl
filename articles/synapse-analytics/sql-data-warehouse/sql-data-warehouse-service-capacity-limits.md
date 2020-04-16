---
title: Limity pojemności — usługa Azure Synapse Analytics (dawniej SQL DW)
description: Maksymalne wartości dozwolone dla różnych składników puli SQL Synapse w usłudze Azure Synapse.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 2/19/2020
ms.author: martinle
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: fbdf0fda51ae35fac4f3f8ae45bfcd788fc406ae
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414002"
---
# <a name="azure-synapse-analytics-formerly-sql-dw-capacity-limits"></a>Limity pojemności usługi Azure Synapse Analytics (dawniej SQL DW)

Maksymalne wartości dozwolone dla różnych składników platformy Azure Synapse.

## <a name="workload-management"></a>Zarządzanie obciążeniem

| Kategoria | Opis | Maksimum |
|:--- |:--- |:--- |
| [Jednostki magazynu danych (DWU)](what-is-a-data-warehouse-unit-dwu-cdwu.md) |Maksymalna dwu dla pojedynczej jednostki puli SQL (magazynu danych) | Gen1: DW6000<br></br>Gen2: DW30000c |
| [Jednostki magazynu danych (DWU)](what-is-a-data-warehouse-unit-dwu-cdwu.md) |Domyślna wartość DTU na serwer |54,000<br></br>Domyślnie każdy serwer SQL (na przykład myserver.database.windows.net) ma przydział jednostek DTU 54 000, co pozwala na dw5000c. Ten limit przydziału jest po prostu limitem bezpieczeństwa. Przydział można zwiększyć, [tworząc bilet pomocy technicznej](sql-data-warehouse-get-started-create-support-ticket.md) i wybierając *przydział* jako typ żądania.  Aby obliczyć potrzeby DTU, należy pomnożyć wartość 7,5 przez całkowitą wymagany dwu lub pomnożyć 9,5 przez całkowitą potrzebną wartość cDWU. Przykład:<br></br>DW6000 x 7,5 = 45 000 jednostek DTU<br></br>DW5000c x 9,5 = 47 500 jednostek DTU.<br></br>Bieżące zużycie danych DTU można wyświetlić z opcji serwera SQL w portalu. Limit przydziału jednostek DTU obejmuje zarówno wstrzymane, jak i niewstrzymane bazy danych. |
| Połączenie z bazą danych |Maksymalna liczba otwartych sesji równoczesnych |1024<br/><br/>Liczba równoczesnych otwartych sesji będzie się różnić w zależności od wybranego DWU. DWU600c i powyżej obsługują maksymalnie 1024 otwarte sesje. DWU500c i poniżej obsługują maksymalny limit sesji otwartej równy 512. Uwaga: istnieją limity liczby zapytań, które można wykonać jednocześnie. Po przekroczeniu limitu współbieżności żądanie przechodzi do kolejki wewnętrznej, w której czeka na przetworzenie. |
| Połączenie z bazą danych |Maksymalna pamięć dla przygotowanych instrukcji |20 MB |
| [Zarządzanie obciążeniem](resource-classes-for-workload-management.md) |Maksymalna liczba równoczesnych zapytań |128<br/><br/>  Zostanie wykonanych maksymalnie 128 równoczesnych zapytań, a pozostałe kwerendy zostaną umieszczone w kolejce.<br/><br/>Liczba równoczesnych zapytań może się zmniejszyć, gdy użytkownicy są przypisani do wyższych klas zasobów lub gdy ustawienie [jednostki magazynu danych](memory-concurrency-limits.md) jest obniżona. Niektóre kwerendy, takie jak kwerendy DMV, są zawsze dozwolone do uruchamiania i nie mają wpływu na limit równoczesnych zapytań. Aby uzyskać więcej informacji na temat równoczesnego wykonywania kwerendy, zobacz [maksymalne współbieżności](memory-concurrency-limits.md) artykułu. |
| [Tempdb](sql-data-warehouse-tables-temporary.md) |Maksymalna gb |399 GB na DW100c. W związku z tym w DWU1000c tempdb jest wielkości do 3,99 TB. |
||||

## <a name="database-objects"></a>Obiekty bazy danych

| Kategoria | Opis | Maksimum |
|:--- |:--- |:--- |
| baza danych |Maksymalny rozmiar | Gen1: 240 TB skompresowane na dysku. Ta przestrzeń jest niezależna od tempdb lub miejsca dziennika, a zatem ta przestrzeń jest przeznaczona dla stałych tabel.  Kompresja klastrowanego magazynu kolumn jest szacowana na 5X.  Ta kompresja umożliwia bazę danych do około 1 PB, gdy wszystkie tabele są klastrowane magazyn kolumn (domyślny typ tabeli). <br/><br/> Gen2: Nieograniczone przechowywanie tabel magazynu kolumn.  Część magazynu wiersza bazy danych jest nadal ograniczona do 240 TB skompresowane na dysku. |
| Tabela |Maksymalny rozmiar |Nieograniczony rozmiar tabel w magazynie kolumn. <br>60 TB dla tabel rowstore skompresowanych na dysku. |
| Tabela |Tabele na bazę danych | 100 000 |
| Tabela |Kolumny na tabelę |1024 kolumny |
| Tabela |Bajty na kolumnę |W zależności od [typu danych](sql-data-warehouse-tables-data-types.md)kolumny . Limit wynosi 8000 dla typów danych char, 4000 dla nvarchar lub 2 GB dla typów danych MAX. |
| Tabela |Bajty w wierszu, zdefiniowany rozmiar |8060 bajtów<br/><br/>Liczba bajtów w wierszu jest obliczana w taki sam sposób, jak w przypadku programu SQL Server z kompresją strony. Podobnie jak SQL Server, magazyn przepełnienia wierszy jest obsługiwany, co umożliwia **odsunięcie kolumn o zmiennej długości** poza wiersz. Gdy wiersze o zmiennej długości są wypychane poza wiersz, tylko 24-bajtowy katalog główny jest przechowywany w rekordzie głównym. Aby uzyskać więcej informacji, zobacz [Dane przepełnienia wierszy przekraczające 8 KB](https://msdn.microsoft.com/library/ms186981.aspx). |
| Tabela |Partycje na tabelę |15 000<br/><br/>Aby uzyskać wysoką wydajność, zalecamy zminimalizowanie liczby partycji, których potrzebujesz, a jednocześnie obsługuje wymagania biznesowe. Wraz ze wzrostem liczby partycji zwiększa się obciążenie dla operacji języka DDL (Data Definition Language) i Data Manipulation Language (DML) i powoduje mniejszą wydajność. |
| Tabela |Znaki na wartość granicy partycji. |4000 |
| Indeks |Indeksy nieklastrowane na tabelę. |50<br/><br/>Dotyczy tylko tabel rowstore. |
| Indeks |Indeksy klastrowane na tabelę. |1<br><br/>Dotyczy zarówno tabel magazynu wierszy, jak i magazynu kolumn. |
| Indeks |Rozmiar klucza indeksu. |900 bajtów.<br/><br/>Dotyczy tylko indeksów magazynu wierszy.<br/><br/>Indeksy na kolumnach varchar o maksymalnym rozmiarze więcej niż 900 bajtów można utworzyć, jeśli istniejące dane w kolumnach nie przekracza 900 bajtów podczas tworzenia indeksu. Jednak później Wstawić lub UPDATE akcje na kolumnach, które powodują całkowity rozmiar przekracza 900 bajtów zakończy się niepowodzeniem. |
| Indeks |Kolumny klucza na indeks. |16<br/><br/>Dotyczy tylko indeksów magazynu wierszy. Indeksy klastrowanego magazynu kolumn obejmują wszystkie kolumny. |
| Statystyki |Rozmiar połączonych wartości kolumn. |900 bajtów. |
| Statystyki |Kolumny na obiekt statystyczny. |32 |
| Statystyki |Statystyki utworzone na kolumnach w tabeli. |30,000 |
| Procedury składowane |Maksymalne poziomy zagnieżdżania. |8 |
| Widok |Kolumny na widok |1,024 |
||||

## <a name="loads"></a>Obciążenia

| Kategoria | Opis | Maksimum |
|:--- |:--- |:--- |
| Obciążenia polybase |MB w wierszu |1<br/><br/>Polybase ładuje wiersze, które są mniejsze niż 1 MB. Ładowanie typów danych LOB do tabel z indeksem klastrowanego magazynu kolumn (CCI) nie jest obsługiwane.<br/><br/> |
||||

## <a name="queries"></a>Zapytania

| Kategoria | Opis | Maksimum |
|:--- |:--- |:--- |
| Zapytanie |Kwerendy w kolejce w tabelach użytkowników. |1000 |
| Zapytanie |Równoczesnych zapytań w widokach systemu. |100 |
| Zapytanie |Kwerendy w kolejce w widokach systemu |1000 |
| Zapytanie |Maksymalne parametry |2098 |
| Batch |Rozmiar maksymalny |65,536*4096 |
| WYNIKI SELECT |Kolumny w wierszu |4096<br/><br/>Nigdy nie można mieć więcej niż 4096 kolumn w wierszu w wyniku SELECT. Nie ma żadnej gwarancji, że zawsze możesz mieć 4096. Jeśli plan kwerend wymaga tabeli tymczasowej, może mieć zastosowanie maksymalnie 1024 kolumn na tabelę. |
| SELECT |Podkemiety zagnieżdżone |32<br/><br/>Nigdy nie można mieć więcej niż 32 zagnieżdżonych podkemietów w instrukcji SELECT. Nie ma gwarancji, że zawsze możesz mieć 32. Na przykład JOIN można wprowadzić podkwerendy do planu kwerend. Liczba podksyłek może być również ograniczona przez dostępną pamięć. |
| SELECT |Kolumny na JOIN |1024 kolumny<br/><br/>Nigdy nie można mieć więcej niż 1024 kolumn w JOIN. Nie ma żadnej gwarancji, że zawsze możesz mieć 1024. Jeśli plan JOIN wymaga tabeli tymczasowej z więcej kolumn niż wynik JOIN, limit 1024 ma zastosowanie do tabeli tymczasowej. |
| SELECT |Bajty na kolumny GRUPY WEDŁUG. |8060<br/><br/>Kolumny w klauzuli GROUP BY mogą mieć maksymalnie 8060 bajtów. |
| SELECT |Bajty na kolumny KOLEJNOŚĆ WEDŁUG |8060 bajtów<br/><br/>Kolumny w klauzuli ORDER BY mogą mieć maksymalnie 8060 bajtów |
| Identyfikatory na instrukcję |Liczba identyfikatorów, do których istnieje odwołanie |65,535<br/><br/> Liczba identyfikatorów, które mogą być zawarte w jednym wyrażeniu kwerendy jest ograniczona. Przekroczenie tej liczby powoduje błąd programu SQL Server 8632. Aby uzyskać więcej informacji, zobacz [Błąd wewnętrzny: Osiągnięto limit usług wyrażeń](https://support.microsoft.com/help/913050/error-message-when-you-run-a-query-in-sql-server-2005-internal-error-a). |
| Literały | Liczba literałów ciągu w instrukcji | 20 000 <br/><br/>Liczba stałych ciągów w jednym wyrażeniu kwerendy jest ograniczona. Przekroczenie tej liczby powoduje błąd programu SQL Server 8632.|
||||

## <a name="metadata"></a>Metadane

| Widok systemu | Maksymalna liczba wierszy |
|:--- |:--- |
| sys.dm_pdw_component_health_alerts |10 000 |
| sys.dm_pdw_dms_cores |100 |
| sys.dm_pdw_dms_workers |Całkowita liczba pracowników DMS dla najnowszych 1000 żądań SQL. |
| sys.dm_pdw_errors |10 000 |
| sys.dm_pdw_exec_requests |10 000 |
| sys.dm_pdw_exec_sessions |10 000 |
| sys.dm_pdw_request_steps |Całkowita liczba kroków dla najnowszych 1000 żądań SQL przechowywanych w pliku sys.dm_pdw_exec_requests. |
| sys.dm_pdw_os_event_logs |10 000 |
| sys.dm_pdw_sql_requests |Najnowsze 1000 żądań SQL, które są przechowywane w pliku sys.dm_pdw_exec_requests. |
|||

## <a name="next-steps"></a>Następne kroki

Aby uzyskać zalecenia dotyczące korzystania z platformy Azure Synapse, zobacz [Ściągawka](cheat-sheet.md).
