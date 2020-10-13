---
title: Azure Stream Analytics dane wyjściowe do Azure SQL Database
description: Dowiedz się więcej na temat wyprowadzania danych do platformy SQL Azure z Azure Stream Analytics i osiągnięcia wyższych stawek przepływności zapisu.
author: chetanmsft
ms.author: chetang
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/18/2019
ms.openlocfilehash: b760ad03318b3c31b39b6470251847150dc5a70a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88869426"
---
# <a name="azure-stream-analytics-output-to-azure-sql-database"></a>Azure Stream Analytics dane wyjściowe do Azure SQL Database

W tym artykule omówiono wskazówki pozwalające osiągnąć lepszą wydajność zapisu podczas ładowania danych do Azure SQL Database przy użyciu Azure Stream Analytics.

Dane wyjściowe SQL w Azure Stream Analytics obsługują pisanie równoległe jako opcję. Ta opcja umożliwia korzystanie z w [pełni równoległych](stream-analytics-parallelization.md#embarrassingly-parallel-jobs) topologii zadań, w przypadku których wiele partycji wyjściowych jest jednocześnie zapisywać do tabeli docelowej. Włączenie tej opcji w Azure Stream Analytics może być jednak niewystarczające do osiągnięcia wyższej przepływności, ponieważ zależy ona znacząco od konfiguracji bazy danych i schematu tabeli. Wybór indeksów, klucza klastrowania, współczynnika wypełnienia indeksu i kompresja ma wpływ na czas ładowania tabel. Aby uzyskać więcej informacji na temat optymalizowania bazy danych w celu poprawy wydajności zapytań i obciążeń opartych na wewnętrznych testach, zobacz [SQL Database wskazówki dotyczące wydajności](../azure-sql/database/performance-guidance.md). Porządkowanie operacji zapisu nie jest gwarantowane, gdy piszesz równolegle do SQL Database.

Poniżej przedstawiono konfiguracje w ramach każdej usługi, która może pomóc w zwiększeniu ogólnej przepływności rozwiązania.

## <a name="azure-stream-analytics"></a>Usługa Azure Stream Analytics

- **Dziedzicz partycjonowanie** — ta opcja konfiguracji danych wyjściowych SQL umożliwia dziedziczenie schematu partycjonowania poprzedniego kroku zapytania lub danych wejściowych. Dzięki temu można zapisywać dane w tabeli opartej na dyskach i mieć w [pełni równoległą](stream-analytics-parallelization.md#embarrassingly-parallel-jobs) topologię dla danego zadania, co pozwala na wyświetlanie większej przepływności. Takie partycjonowanie jest już automatycznie wykonywane dla wielu innych danych [wyjściowych](stream-analytics-parallelization.md#partitions-in-inputs-and-outputs). Blokowanie tabeli (TABLOCK) jest również wyłączone dla operacji wstawiania zbiorczego z tą opcją.

> [!NOTE] 
> Jeśli istnieje więcej niż 8 partycji wejściowych, dziedziczenie schematu partycjonowania danych wejściowych może nie być odpowiednią opcją. Ten górny limit został zaobserwowany w tabeli z kolumną o pojedynczej tożsamości i indeksem klastrowanym. W takim przypadku Rozważ użycie [do](https://docs.microsoft.com/stream-analytics-query/into-azure-stream-analytics#into-shard-count) 8 w zapytaniu, aby jawnie określić liczbę składników zapisywania danych wyjściowych. W oparciu o schemat i wybór indeksów Twoje uwagi mogą się różnić.

- **Rozmiar wsadu** — konfiguracja wyjściowa SQL pozwala określić maksymalny rozmiar wsadu w Azure Stream Analytics danych wyjściowych SQL na podstawie charakteru docelowej tabeli lub obciążenia. Rozmiar wsadu to maksymalna liczba rekordów, które zostały wysłane z każdą zbiorczą transakcję wstawiania. W klastrowanych indeksach magazynu kolumn rozmiary usługi Batch dotyczące [100 000](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-data-loading-guidance) umożliwiają większą przetwarzanie równoległe, minimalną rejestrację i optymalizację blokowania. W tabelach opartych na dyskach 10 (domyślnie) lub niższy może być optymalny dla danego rozwiązania, ponieważ wyższe rozmiary partii mogą wyzwalać eskalację blokady podczas operacji wstawiania zbiorczego.

- **Dostrajanie komunikatów wejściowych** — w przypadku optymalizacji przy użyciu dziedziczenia i rozmiaru partii zwiększenie liczby zdarzeń wejściowych na komunikat na partycję ułatwia dalsze wypychanie przepływności zapisu. Dostrajanie komunikatów wejściowych umożliwia rozmiar wsadu w ramach Azure Stream Analytics do określonego rozmiaru partii, a tym samym zwiększenie przepływności. Można to osiągnąć za pomocą [kompresji](stream-analytics-define-inputs.md) lub zwiększając rozmiary komunikatów wejściowych w centrum EventHub lub BLOB.

## <a name="sql-azure"></a>Usługi SQL Azure

- **Partycjonowane tabele i indeksy** — za pomocą [partycjonowanej](https://docs.microsoft.com/sql/relational-databases/partitions/partitioned-tables-and-indexes?view=sql-server-2017) tabeli SQL i indeksów partycjonowanych w tabeli z tą samą kolumną, co klucz partycji (na przykład PartitionID), może znacznie zmniejszyć rywalizację między partycjami podczas operacji zapisu. W przypadku partycjonowanej tabeli należy utworzyć [funkcję partycji](https://docs.microsoft.com/sql/t-sql/statements/create-partition-function-transact-sql?view=sql-server-2017) i [schemat partycji](https://docs.microsoft.com/sql/t-sql/statements/create-partition-scheme-transact-sql?view=sql-server-2017) w podstawowej grupie plików. Spowoduje to również zwiększenie dostępności istniejących danych podczas ładowania nowych danych. Limit operacji we/wy dziennika można uzyskać na podstawie liczby partycji, które można zwiększyć, uaktualniając jednostkę SKU.

- **Unikaj unikatowych naruszeń klucza** — Jeśli w dzienniku aktywności Azure Stream Analytics występuje [wiele komunikatów ostrzegawczych naruszenia klucza](stream-analytics-troubleshoot-output.md#key-violation-warning-with-azure-sql-database-output) , upewnij się, że na zadaniu nie ma wpływu unikatowe naruszenia ograniczenia, które prawdopodobnie wystąpią w przypadku przypadków odzyskiwania. Można to uniknąć przez ustawienie opcji [Ignoruj \_ DUP \_ klucza](stream-analytics-troubleshoot-output.md#key-violation-warning-with-azure-sql-database-output) w indeksach.

## <a name="azure-data-factory-and-in-memory-tables"></a>Tabele Azure Data Factory i In-Memory

- **Tabela w pamięci jako tabela tymczasowa** — [tabele w pamięci](/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization) umożliwiają korzystanie z bardzo dużych ilości danych, ale dane muszą być dopasowane do pamięci. Testy porównawcze pokazują zbiorcze ładowanie z tabeli w pamięci do tabeli opartej na dyskach — około 10 razy szybciej niż bezpośrednio, wstawiając przy użyciu jednego składnika zapisywania do tabeli opartej na dyskach z kolumną tożsamości i indeksem klastrowanym. Aby skorzystać z tej zbiorczej wydajności wstawiania, skonfiguruj [zadanie kopiowania przy użyciu Azure Data Factory](../data-factory/connector-azure-sql-database.md) , które kopiuje dane z tabeli w pamięci do tabeli opartej na dyskach.

## <a name="avoiding-performance-pitfalls"></a>Uniknięcie pułapek wydajności
Zbiorcze Wstawianie danych jest znacznie szybsze niż ładowanie danych przy użyciu pojedynczych operacji wstawiania, ponieważ wielokrotne obciążenie związane z transferem danych, analizowaniem instrukcji INSERT, wykonywaniem instrukcji i wydawanie rekordu transakcji jest możliwe. Zamiast tego do aparatu magazynu jest używana bardziej wydajna ścieżka do przesyłania strumieniowego danych. Koszt instalacji tej ścieżki jest jednak znacznie wyższy niż w przypadku pojedynczej instrukcji INSERT w tabeli opartej na dyskach. Punkt parzystości jest zwykle wokół 100 wierszy, po których ładowanie zbiorcze jest prawie zawsze wydajniejsze. 

Jeśli częstotliwość zdarzeń przychodzących jest niska, można łatwo utworzyć rozmiary partii mniejsze niż 100 wierszy, co sprawia, że zbiorcze Wstawianie jest niewydajne i zużywa zbyt dużo miejsca na dysku. Aby obejść to ograniczenie, można wykonać jedną z następujących czynności:
* Utwórz [wyzwalacz](/sql/t-sql/statements/create-trigger-transact-sql) instead of, aby użyć prostej instrukcji INSERT dla każdego wiersza.
* Użyj tabeli tymczasowej In-Memory, zgodnie z opisem w poprzedniej sekcji.

Innym scenariuszem jest zapisanie w nieklastrowanym indeksie magazynu kolumn (NCCI), gdzie mniejsze operacje wstawiania zbiorczego mogą utworzyć zbyt wiele segmentów, co może spowodować awarię indeksu. W takim przypadku zalecane jest użycie klastrowanego indeksu magazynu kolumn.

## <a name="summary"></a>Podsumowanie

Podsumowując, z funkcją danych wyjściowych partycjonowaną w Azure Stream Analytics dla danych wyjściowych SQL, wyrównany przetwarzanie równoległe zadania z partycjonowaną tabelą w usłudze SQL Azure powinien zapewnić znaczną poprawę przepływności. Wykorzystanie Azure Data Factory do organizowania przenoszenia danych z tabeli In-Memory w tabelach opartych na dyskach może dać w wyniku wzrost wydajności przepływności. Jeśli to możliwe, zwiększenie gęstości komunikatów może być również głównym czynnikiem zwiększającym ogólną przepływność.