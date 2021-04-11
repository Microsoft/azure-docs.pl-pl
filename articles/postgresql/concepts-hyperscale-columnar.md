---
title: Kolumnowy magazyn Table Storage — wersja zapoznawcza (Citus) — Azure Database for PostgreSQL
description: Kompresowanie danych przy użyciu magazynu kolumnowego (wersja zapoznawcza)
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 04/07/2021
ms.openlocfilehash: c60c398d49a802dbe051ca37c4aea2092ab5144b
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/07/2021
ms.locfileid: "107024006"
---
# <a name="columnar-table-storage-preview"></a>Magazyn tabel kolumnowy (wersja zapoznawcza)

> [!IMPORTANT]
> Magazyn tabel kolumnowy (Citus) jest obecnie w wersji zapoznawczej. Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
>
> Pełną listę innych nowych funkcji można znaleźć w artykule [dotyczącym funkcji Citus (wersja zapoznawcza)](hyperscale-preview-features.md).

Funkcja Azure Database for PostgreSQL-Citus) obsługuje tylko dołączanie magazynu tabel kolumnowych dla obciążeń analitycznych i magazynów danych. Gdy kolumny (a nie wiersze) są przechowywane w sposób ciągły na dysku, dane staną się coraz bardziej kompresowaćne, a zapytania mogą szybko zażądać podzbioru kolumn.

Aby użyć magazynu kolumnowego, określ `USING columnar` podczas tworzenia tabeli:

```postgresql
CREATE TABLE contestant (
    handle TEXT,
    birthdate DATE,
    rating INT,
    percentile FLOAT,
    country CHAR(3),
    achievements TEXT[]
) USING columnar;
```

Funkcja Citus) konwertuje wiersze do magazynu kolumnowego na "prążki" podczas wstawiania. Każdy pasek zawiera jedną transakcję danych lub 150000 wierszy, w zależności od tego, co jest mniejsze.  (Rozmiar rozłożenia i inne parametry tabeli kolumnowej można zmienić za pomocą funkcji [alter_columnar_table_set](reference-hyperscale-functions.md#alter_columnar_table_set) ).

Na przykład poniższa instrukcja umieszcza wszystkie pięć wierszy w tym samym rozgałęzieniu, ponieważ wszystkie wartości są wstawiane do jednej transakcji:

```postgresql
-- insert these values into a single columnar stripe

INSERT INTO contestant VALUES
  ('a','1990-01-10',2090,97.1,'XA','{a}'),
  ('b','1990-11-01',2203,98.1,'XA','{a,b}'),
  ('c','1988-11-01',2907,99.4,'XB','{w,y}'),
  ('d','1985-05-05',2314,98.3,'XB','{}'),
  ('e','1995-05-05',2236,98.2,'XC','{a}');
```

Najlepiej zrobić duże paski, gdy jest to możliwe, ponieważ funkcja wieloskaling (Citus) kompresuje dane kolumnowy oddzielnie na pasku. Możemy zobaczyć fakty dotyczące naszej tabeli kolumnowej, takich jak szybkość kompresji, liczba taśm oraz średnia liczba wierszy na pas przy użyciu `VACUUM VERBOSE` :

```postgresql
VACUUM VERBOSE contestant;
```
```
INFO:  statistics for "contestant":
storage id: 10000000000
total file size: 24576, total data size: 248
compression rate: 1.31x
total row count: 5, stripe count: 1, average rows per stripe: 5
chunk count: 6, containing data for dropped columns: 0, zstd compressed: 6
```

Dane wyjściowe pokazują, że funkcja preskaling (Citus) użyła algorytmu kompresji zstd w celu uzyskania kompresji danych 1.31 x. Szybkość kompresji polega na porównaniu rozmiaru wstawionych danych w postaci, w jakiej zostały umieszczone w pamięci, w odniesieniu do b) rozmiaru danych skompresowanych w jego momencie.

Ze względu na to, jak jest mierzony, szybkość kompresji może być niezgodna z różnicą rozmiaru między wierszem a kolumną magazynu dla tabeli. Jedynym sposobem na znalezienie różnicy jest konstruowanie tabeli wierszy i kolumn kolumnowy, które zawierają te same dane, i porównanie:

```postgresql
CREATE TABLE contestant_row AS
    SELECT * FROM contestant;

SELECT pg_total_relation_size('contestant_row') as row_size,
       pg_total_relation_size('contestant') as columnar_size;
```
```
 row_size | columnar_size
----------+---------------
    16384 |         24576
```

W przypadku niewielkiej tabeli magazyn kolumnowy w rzeczywistości zużywa więcej miejsca, ale po rozmieszczeniu danych kompresja zostanie wygrana.

## <a name="example"></a>Przykład

Magazyn kolumnowy działa dobrze w przypadku partycjonowania tabel. Aby zapoznać się z przykładem, zobacz dokumentację wspólnotową aparatu Citus, która umożliwia [Archiwizowanie przy użyciu magazynu kolumnowego](https://docs.citusdata.com/en/stable/use_cases/timeseries.html#archiving-with-columnar-storage).

## <a name="gotchas"></a>Pytania dotyczące usługi

* Kolumnowy dyskowy kompresuje na pas. Paski są tworzone na transakcję, więc wstawienie jednego wiersza na transakcję spowoduje umieszczenie pojedynczych wierszy w ich własnych paskach. Kompresja i wydajność pojedynczych pasków wierszy będzie gorsza niż tabela wierszy. Zawsze wstawiaj zbiorczo do tabeli kolumnowej.
* Jeśli columnarizesz wiele niewielkich pasków, będziesz mieć zablokowany.
  Jedyną poprawką jest utworzenie nowej tabeli kolumnowej i skopiowanie danych z oryginału w jednej transakcji:
  ```postgresql
  BEGIN;
  CREATE TABLE foo_compacted (LIKE foo) USING columnar;
  INSERT INTO foo_compacted SELECT * FROM foo;
  DROP TABLE foo;
  ALTER TABLE foo_compacted RENAME TO foo;
  COMMIT;
  ```
* Niekompresowaće dane mogą być problemem, chociaż magazyn kolumnowy jest nadal przydatny podczas wybierania określonych kolumn. Nie trzeba ładować innych kolumn do pamięci.
* W tabeli partycjonowanej z mieszaniem partycji wierszy i kolumn należy dokładnie określić aktualizacje. Odfiltruj je, aby trafiać tylko partycje wierszy.
   * Jeśli operacja jest wskazywana na określoną partycję wiersza (na przykład `UPDATE p2 SET i = i + 1` ), powiedzie się, jeśli jest to wskazane na określonej partycji kolumnowej (na przykład `UPDATE p1 SET i = i + 1` ), zakończy się niepowodzeniem.
   * Jeśli operacja jest przeznaczona dla tabeli partycjonowanej i zawiera klauzulę WHERE, która wyklucza wszystkie partycje kolumnowe (na przykład `UPDATE parent SET i = i + 1 WHERE timestamp = '2020-03-15'` ), powiedzie się.
   * Jeśli operacja jest wskazywana w tabeli partycjonowanej, ale nie filtruje kolumn kluczy partycji, zakończy się niepowodzeniem. Nawet jeśli istnieją klauzule, które pasują do wierszy tylko w przypadku partycji kolumnowych, nie jest to wystarczające — klucz partycji musi być również filtrowany.

## <a name="limitations"></a>Ograniczenia

Ta funkcja nadal ma wiele znaczących ograniczeń. Zobacz [limity i ograniczenia dotyczące skalowania (Citus)](concepts-hyperscale-limits.md#columnar-storage).

## <a name="next-steps"></a>Następne kroki

* Zapoznaj się z przykładem magazynu kolumnowego w [samouczku Citus szeregów czasowych](https://docs.citusdata.com/en/stable/use_cases/timeseries.html#archiving-with-columnar-storage) (Link zewnętrzny).
