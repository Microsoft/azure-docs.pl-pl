---
title: Arkusz Ściągawka dla usługi Azure Synapse Analytics (dawniej SQL DW)
description: Znajdź linki i najlepsze rozwiązania umożliwiające szybkie tworzenie rozwiązań Azure Synapse Analytics (dawniej SQL DW).
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql-dw
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: 648f06ef1af5d6dce9fa3583c6358d3bd173f209
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2020
ms.locfileid: "93319674"
---
# <a name="cheat-sheet-for-azure-synapse-analytics-formerly-sql-dw"></a>Arkusz Ściągawka dla usługi Azure Synapse Analytics (dawniej SQL DW)

Ten arkusz Ściągawka zawiera pomocne wskazówki i najlepsze rozwiązania dotyczące tworzenia rozwiązań Synapse platformy Azure.

Na poniższym rysunku przedstawiono proces projektowania magazynu danych:

![Szkic](./media/cheat-sheet/picture-flow.png)

## <a name="queries-and-operations-across-tables"></a>Zapytania i operacje między tabelami

Jeśli wcześniej wiadomo, które operacje i zapytania będą uruchamiane w magazynie danych, można przydzielać priorytety architekturze magazynu danych dla tych operacji. Te zapytania i operacje mogą być następujące:

* Łączenie jednej lub dwóch tabel faktów z tabelami wymiarów, filtrowanie połączonej tabeli, a następnie łączenie wyników w składnicy danych.
* Wprowadzanie dużych lub małych aktualizacji do sprzedaży faktów.
* Dołączanie tylko danych do tabel.

Wcześniejsza znajomość typów operacji pomaga zoptymalizować projekt tabel.

## <a name="data-migration"></a>Migracja danych

Najpierw Załaduj dane do [Azure Data Lake Storage](../../data-factory/connector-azure-data-lake-store.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) lub BLOB Storage platformy Azure. Następnie użyj [instrukcji Copy](/sql/t-sql/statements/copy-into-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) (wersja zapoznawcza), aby załadować dane do tabel przemieszczania. Użyj następującej konfiguracji:

| Projekt | Zalecenie |
|:--- |:--- |
| Dystrybucja | Działanie okrężne |
| Indeksowanie | Sterta |
| Partycjonowanie | Brak |
| Klasa zasobów | largerc lub xlargerc |

Dowiedz się więcej o [migracji danych](https://blogs.msdn.microsoft.com/sqlcat/20../../migrating-data-to-azure-sql-data-warehouse-in-practice/), [ładowaniu danych](design-elt-data-loading.md) i [proces wyodrębniania, przekształcania i ładowania (ELT)](design-elt-data-loading.md).

## <a name="distributed-or-replicated-tables"></a>Tabele rozproszone lub replikowane

Użyj następujących strategii, w zależności od właściwości tabeli:

| Typ | Doskonałe rozwiązanie dla...| Należy uważać, jeśli...|
|:--- |:--- |:--- |
| Replikowane | * Małe tabele wymiarów w schemacie gwiazdy z mniej niż 2 GB miejsca w magazynie po kompresji (~ pięciokrotną kompresji) |* Wiele transakcji zapisu znajduje się w tabeli (na przykład INSERT, Upsert, DELETE, Update)<br></br>* Często zmienia się inicjowanie obsługi jednostek magazynu danych (jednostek dwu)<br></br>* Można używać tylko 2-3 kolumn, ale tabela zawiera wiele kolumn<br></br>* Indeksuj zreplikowaną tabelę |
| Działanie okrężne (ustawienie domyślne) | * Tabela tymczasowa/przejściowa<br></br> * Brak oczywistego sprzęgania klucza lub dobrej kolumny kandydata |* Wydajność jest niska z powodu przenoszenia danych |
| Skrót | * Tabele faktów<br></br>* Duże tabele wymiarów |* Nie można zaktualizować klucza dystrybucji |

**Pomoc**

* Rozpocznij od działania okrężnego, ale staraj się zastosować strategię dystrybucji skrótów, aby skorzystać z zalet architektury z wysokim wskaźnikiem przetwarzania równoległego.
* Upewnij się, że typowe klucze skrótów mają ten sam format danych.
* Nie Dystrybuuj w formacie varchar.
* Tabele wymiarów ze wspólnym kluczem skrótu do tabeli faktów z częstymi operacjami sprzężenia mogą być rozproszonymi tabelami skrótów.
* Użyj elementu *[sys.dm_pdw_nodes_db_partition_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-partition-stats-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)* , aby analizować skośność danych.
* Użyj *[sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)* , aby analizować przepływy danych za zapytania, monitorować emisję czasu i wykonywać operacje losowe. To pomaga w ocenie stosowanej strategii dystrybucji.

Dowiedz się więcej o [tabelach replikowanych](design-guidance-for-replicated-tables.md) i [tabelach rozproszonych](sql-data-warehouse-tables-distribute.md).

## <a name="index-your-table"></a>Indeksowanie tabeli

Indeksowanie ułatwia szybkie odczytywanie tabel. W zależności od potrzeb można używać unikatowego zestawu technologii:

| Typ | Doskonałe rozwiązanie dla... | Należy uważać, jeśli...|
|:--- |:--- |:--- |
| Sterta | * Tymczasowa/tymczasowa tabela<br></br>* Małe tabele z małymi wyszukiwaniami |* Każde wyszukiwanie skanuje pełną tabelę |
| Indeks klastrowany | * Tabele zawierające do 100 000 000 wierszy<br></br>* Duże tabele (więcej niż 100 000 000 wierszy) z intensywnym użyciem kolumn: 1-2 |* Używane w zreplikowanej tabeli<br></br>* Istnieją złożone zapytania obejmujące wiele operacji przyłączania i grupowania<br></br>* Wprowadzane są aktualizacje dla indeksowanych kolumn: zajmują pamięć |
| Klastrowany indeks magazynu kolumn (CCI) (ustawienie domyślne) | * Duże tabele (więcej niż 100 000 000 wierszy) | * Używane w zreplikowanej tabeli<br></br>* W tabeli wprowadzono duże operacje aktualizacji<br></br>* Przełożenie tabeli na partycje: grupy wierszy nie rozciągają się między różnymi węzłami i partycjami dystrybucji |

**Pomoc**

* Oprócz indeksu klastrowanego można do kolumny intensywnie używanej do filtrowania dodać indeks nieklastrowany.
* Zachowaj ostrożność przy zarządzaniu pamięcią tabeli przy użyciu indeksu CCI. Gdy ładujesz dane, chcesz, aby użytkownik (lub zapytanie) korzystał z zalet dużej klasy zasobów. Pamiętaj, aby unikać przycinania i tworzenia wielu małych skompresowanych grup wierszy.
* W przypadku drugiej generacji tabele indeksu CCI są buforowane lokalnie w węzłach obliczeniowych w celu zmaksymalizowania wydajności.
* W przypadku interfejsu CCI przyczyną niskiej wydajności może być słaba kompresja grup wierszy. W takim przypadku ponownie skompiluj lub zorganizuj indeks CCI. Chcesz, aby każda skompresowana grupa wierszy zawierała co najmniej 100 000 wierszy. Idealna liczba to 1 mln wierszy w grupie.
* Na podstawie rozmiaru i częstotliwości ładowania przyrostowego chcesz zautomatyzować reorganizację lub ponowną kompilację indeksów. Gruntowne porządki będą zawsze pomocne.
* Jeśli chcesz przyciąć grupę wierszy, podejdź do tego strategicznie. Jak duże są otwarte grupy wierszy? Ile danych planujesz załadować w ciągu nadchodzących dni?

Dowiedz się więcej o [indeksach](sql-data-warehouse-tables-index.md).

## <a name="partitioning"></a>Partycjonowanie

Tabelę możesz partycjonować, gdy jest to duża tabela faktów (większa niż 1 miliard wierszy). W 99% przypadków klucz partycji powinien opierać się na dacie. Uważaj, aby nie partycjonować nadmiernie, szczególnie gdy korzystasz z klastrowanego indeksu magazynu kolumn.

Partycjonowanie może przynieść korzyści w przypadku tabel przejściowych, które wymagają procesu ELT. Ułatwia ono zarządzanie cyklem życia danych.
Uważaj, aby nadmiernie nie partycjonować danych, szczególnie w przypadku klastrowanego indeksu magazynu kolumn.

Dowiedz się więcej o [partycjach](sql-data-warehouse-tables-partition.md).

## <a name="incremental-load"></a>Ładowanie przyrostowe

Jeśli planujesz ładować dane przyrostowo, najpierw upewnij się, że przydzielasz większe klasy zasobów na potrzeby ładowania danych.  Jest to szczególnie ważne podczas ładowania do tabel z klastrowanymi indeksami magazynu kolumn.  Zobacz [klasy zasobów](resource-classes-for-workload-management.md) , aby uzyskać więcej szczegółów.  

Zalecamy korzystanie z aplikacji wielobase i ADF v2 w celu automatyzacji potoków usługi ELT w magazynie danych.

W przypadku dużej partii aktualizacji danych historycznych Rozważ użycie [CTAs](sql-data-warehouse-develop-ctas.md) , aby napisać dane, które mają być przechowywane w tabeli, a nie za pomocą instrukcji INSERT, Update i DELETE.

## <a name="maintain-statistics"></a>Prowadzenie statystyk

Ważne jest aktualizowanie statystyk w miarę pojawiania się kolejnych *znaczących* zmian danych. Zobacz [aktualizacja statystyk](sql-data-warehouse-tables-statistics.md#update-statistics) , aby ustalić, czy wprowadzono *znaczące* zmiany. Zaktualizowane statystyki optymalizują plany zapytań. Jeśli okaże się, że obsługa wszystkich statystyk trwa zbyt długo, należy przemyśleć dokładnie wybór kolumn ze statystykami.

Można również zdefiniować częstotliwość aktualizacji. Można na przykład codziennie aktualizować kolumny danych, w których mogą być dodawane nowe wartości. Największe korzyści można osiągnąć, prowadząc statystyki dla kolumn uczestniczących w sprzężeniach, kolumn używanych w ramach klauzuli WHERE i kolumn z klauzuli GROUP BY.

Dowiedz się więcej o [statystykach](sql-data-warehouse-tables-statistics.md).

## <a name="resource-class"></a>Klasa zasobów

Grupy zasobów są używane jako sposób przydzielania pamięci do zapytań. Jeśli potrzeba większej ilości pamięci w celu zwiększenia szybkości zapytań lub ładowania, należy przydzielić wyższe klasy zasobów. Z drugiej strony użycie większych klas zasobów wpływa na współbieżność. Należy o tym pamiętać przed przeniesieniem wszystkich użytkowników do dużej klasy zasobów.

Jeśli zauważysz, że wykonywanie zapytań trwa zbyt długo, sprawdź, czy użytkownicy nie stosują uruchamiania w dużych klasach zasobów. Duże klasy zasobów używają wielu miejsc współbieżności. Może to powodować powstanie kolejki innych zasobów.

Na koniec przy użyciu Gen2 [puli SQL](sql-data-warehouse-overview-what-is.md#dedicated-sql-pool-in-azure-synapse)każda klasa zasobów otrzymuje 2,5 razy więcej pamięci niż Gen1.

Dowiedz się więcej, jak pracować z [klasami zasobów i współbieżnością](resource-classes-for-workload-management.md).

## <a name="lower-your-cost"></a>Obniżanie kosztów

Kluczową cechą usługi Azure Synapse jest możliwość [zarządzania zasobami obliczeniowymi](sql-data-warehouse-manage-compute-overview.md). Pulę SQL można wstrzymać, gdy nie jest używana, co powoduje zatrzymanie rozliczeń zasobów obliczeniowych. Zasoby można skalować zgodnie ze swoimi wymaganiami dotyczącymi wydajności. W celu wstrzymania użyj witryny [Azure Portal](pause-and-resume-compute-portal.md) lub programu [PowerShell](pause-and-resume-compute-powershell.md). W celu skalowania użyj witryny [Azure Portal](quickstart-scale-compute-portal.md), programu [PowerShell](quickstart-scale-compute-powershell.md), języka [T-SQL](quickstart-scale-compute-tsql.md) lub [interfejsu API REST](sql-data-warehouse-manage-compute-rest-api.md#scale-compute).

Teraz możesz używać automatycznego skalowania w dowolnym momencie dzięki funkcji Azure Functions:

[![Obraz przedstawiający przycisk "wdróż na platformie Azure".](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://ms.portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FMicrosoft%2Fsql-data-warehouse-samples%2Fmaster%2Farm-templates%2FsqlDwTimerScaler%2Fazuredeploy.json)

## <a name="optimize-your-architecture-for-performance"></a>Optymalizacja architektury pod kątem wydajności

Zalecamy rozważenie użycia bazy danych SQL Database i usługi Azure Analysis Services w architekturze gwiazdy. To rozwiązanie może spowodować rozdzielenie obciążenia między różnymi grupami użytkowników przy równoczesnym korzystaniu z zaawansowanych funkcji zabezpieczeń bazy danych SQL Database i usługi Azure Analysis Services. Jest to również sposób na zapewnienie użytkownikom nieograniczonej współbieżności.

Dowiedz się więcej [na temat typowych architektur, które wykorzystują usługę Azure Synapse](https://blogs.msdn.microsoft.com/sqlcat/20../../common-isv-application-patterns-using-azure-sql-data-warehouse/).

Wdróż aplikację w jednym kliknięciem szprych w bazach danych SQL z puli SQL:

[![Obraz przedstawiający przycisk "wdróż na platformie Azure".](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://ms.portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FMicrosoft%2Fsql-data-warehouse-samples%2Fmaster%2Farm-templates%2FsqlDwSpokeDbTemplate%2Fazuredeploy.json)
