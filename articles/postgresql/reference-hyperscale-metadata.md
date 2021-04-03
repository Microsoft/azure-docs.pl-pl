---
title: Tabele systemowe — funkcja do skalowania (Citus) — Azure Database for PostgreSQL
description: Metadane rozproszonego wykonywania zapytań
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: reference
ms.date: 08/10/2020
ms.openlocfilehash: 74403365fe48584fa5d1db0e349c9dfc3772d874
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97652860"
---
# <a name="system-tables-and-views"></a>Tabele i widoki systemowe

Funkcja Citus) tworzy i utrzymuje specjalne tabele zawierające informacje o rozproszonych danych w grupie serwerów. Węzeł koordynator bada te tabele podczas planowania sposobu uruchamiania zapytań w węzłach procesu roboczego.

## <a name="coordinator-metadata"></a>Metadane koordynatora

Funkcja przeskaling (Citus) dzieli każdą tabelę rozproszoną na wiele fragmentówów logicznych na podstawie kolumny dystrybucja. Koordynator zachowuje tabele metadanych, aby śledzić statystyki i informacje o kondycji i lokalizacji tych fragmentów.

W tej sekcji opisano każdą z tych tabel metadanych i ich schemat.
Możesz przeglądać te tabele i wysyłać do nich zapytania przy użyciu programu SQL po zalogowaniu się do węzła koordynatora.

> [!NOTE]
>
> Grupy serwerów ze skalowaniem (Citus) korzystające ze starszych wersji aparatu Citus mogą nie oferować wszystkich tabel wymienionych poniżej.

### <a name="partition-table"></a>Tabela partycji

W \_ tabeli partycji rozkłowej PG \_ są przechowywane metadane dotyczące tego, które tabele w bazie danych są dystrybuowane. Dla każdej tabeli rozproszonej zawiera ona również informacje na temat metody dystrybucji oraz szczegółowe informacje o kolumnie dystrybucji.

| Nazwa         | Typ     | Opis                                                                                                                                                                                                                                           |
|--------------|----------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| logicalrelid | regclass | Rozproszona tabela, do której odnosi się ten wiersz. Ta wartość odwołuje się do kolumny relfilenode w tabeli wykazu systemu pg_class.                                                                                                                   |
| partmethod   | char     | Metoda używana do partycjonowania/dystrybucji. Wartości tej kolumny odpowiadające różnym metodom dystrybucji są dołączane: "a", hash: "h", tabela odwołania: "n"                                                                          |
| partkey      | tekst     | Szczegółowe informacje na temat kolumny dystrybucji, w tym numeru kolumny, typu i innych istotnych informacji.                                                                                                                                      |
| colocationid | liczba całkowita  | Grupa wspólnej lokalizacji, do której należy ta tabela. Tabele w tej samej grupie umożliwiają współznajdujące się w nich sprzężenia i rozproszone pakiety zbiorcze między innymi optymalizacjami. Ta wartość odwołuje się do kolumny colocationid w tabeli pg_dist_colocation.                      |
| repmodel     | char     | Metoda używana do replikacji danych. Wartości tej kolumny odpowiadające różnym metodom replikacji są: replikacja oparta na instrukcji Citus: "c", PostgreSQL Streaming Replication: ", dwufazowe zatwierdzanie (dla tabel referencyjnych): 't" |

```
SELECT * from pg_dist_partition;
 logicalrelid  | partmethod |                                                        partkey                                                         | colocationid | repmodel 
---------------+------------+------------------------------------------------------------------------------------------------------------------------+--------------+----------
 github_events | h          | {VAR :varno 1 :varattno 4 :vartype 20 :vartypmod -1 :varcollid 0 :varlevelsup 0 :varnoold 1 :varoattno 4 :location -1} |            2 | c
 (1 row)
```

### <a name="shard-table"></a>Tabela fragmentu

W tabeli fragmentu w języku fragmentów \_ \_ są przechowywane metadane dotyczące poszczególnych tabeli. Pg_dist_shard zawiera informacje o tym, do której tabeli rozproszonej fragmentów należy, i statystyk dotyczących kolumny dystrybucji dla fragmentów.
W przypadku dołączania tabel rozproszonych te statystyki odpowiadają minimalnym/maksymalnym wartościom kolumny dystrybucji. W przypadku tabel rozproszonych skrótami są zakresy tokenów skrótu przypisane do tego fragmentu. Te statystyki są używane do oczyszczania niepowiązanych fragmentów podczas wybierania zapytań.

| Nazwa          | Typ     | Opis                                                                                                                                                                                  |
|---------------|----------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| logicalrelid  | regclass | Rozproszona tabela, do której odnosi się ten wiersz. Ta wartość odwołuje się do kolumny relfilenode w tabeli wykazu systemu pg_class.                                                          |
| shardid       | bigint   | Unikatowy identyfikator globalny przypisany do tego fragmentu.                                                                                                                                           |
| shardstorage  | char     | Typ magazynu używany dla tego fragmentu. W poniższej tabeli omówiono różne typy magazynów.                                                                                               |
| shardminvalue | tekst     | W przypadku dołączania tabel rozproszonych minimalna wartość kolumny rozkład w tym fragmentu (włącznie). Dla tabel dystrybuowanych przez skrót minimalna wartość tokenu skrótu przypisana do tego fragmentu (włącznie). |
| shardmaxvalue | tekst     | W przypadku dołączania tabel rozproszonych maksymalna wartość kolumny rozkład w tym fragmentu (włącznie). W przypadku tabel rozproszonych z mieszaniem maksymalna wartość tokenu skrótu przypisana do tego fragmentu (włącznie). |

```
SELECT * from pg_dist_shard;
 logicalrelid  | shardid | shardstorage | shardminvalue | shardmaxvalue 
---------------+---------+--------------+---------------+---------------
 github_events |  102026 | t            | 268435456     | 402653183
 github_events |  102027 | t            | 402653184     | 536870911
 github_events |  102028 | t            | 536870912     | 671088639
 github_events |  102029 | t            | 671088640     | 805306367
 (4 rows)
```

#### <a name="shard-storage-types"></a>Fragmentu typy magazynów

Kolumna shardstorage w \_ fragmentuach w programie rozkłu PG \_ wskazuje typ magazynu używanego dla fragmentu. Poniżej znajduje się krótkie omówienie różnych typów magazynu fragmentu i ich reprezentacji.

| Typ magazynu | Shardstorage wartość | Opis                                                                        |
|--------------|--------------------|------------------------------------------------------------------------------------|
| TABELE        | 'C                | Wskazuje, że fragmentu przechowuje dane należące do zwykłej tabeli rozproszonej.         |
| KOLUMN     | s                | Wskazuje, że fragmentu przechowuje dane kolumnowy. (Używane w tabelach cstore_fdw rozproszonych) |
| ZAGRANICZNA      | n                | Wskazuje, że fragmentu przechowuje dane obce. (Używane w tabelach file_fdw rozproszonych)    |

### <a name="shard-placement-table"></a>Tabela rozmieszczenia fragmentu

\_Tabela rozmieszczenia w procesie rozkłowym PG \_ śledzi lokalizację replik fragmentu w węzłach procesu roboczego. Każda replika fragmentu przypisana do określonego węzła jest nazywana rozmieszczeniem fragmentu. W tej tabeli przechowywane są informacje o kondycji i lokalizacji każdego rozmieszczenia fragmentu.

| Nazwa        | Typ   | Opis                                                                                                                               |
|-------------|--------|-------------------------------------------------------------------------------------------------------------------------------------------|
| shardid     | bigint | Identyfikator fragmentu skojarzony z tym umieszczaniem. Ta wartość odwołuje się do kolumny shardid w tabeli wykazu pg_dist_shard.             |
| shardstate  | int    | Opisuje stan tego umieszczania. Różne stany fragmentu zostały omówione w sekcji poniżej.                                         |
| shardlength | bigint | W przypadku dołączania tabel rozproszonych rozmiar fragmentu umieszczany w węźle procesu roboczego w bajtach. W przypadku tabel rozproszonych z mieszaniem wartość zero.            |
| placementid | bigint | Unikatowy wygenerowany automatycznie identyfikator dla każdego indywidualnego położenia.                                                                           |
| GroupID     | int    | Oznacza grupę jednego serwera podstawowego i zero lub więcej serwerów pomocniczych, gdy używany jest model replikacji strumieniowej. |

```
SELECT * from pg_dist_placement;
  shardid | shardstate | shardlength | placementid | groupid
 ---------+------------+-------------+-------------+---------
   102008 |          1 |           0 |           1 |       1
   102008 |          1 |           0 |           2 |       2
   102009 |          1 |           0 |           3 |       2
   102009 |          1 |           0 |           4 |       3
   102010 |          1 |           0 |           5 |       3
   102010 |          1 |           0 |           6 |       4
   102011 |          1 |           0 |           7 |       4
```

#### <a name="shard-placement-states"></a>Stany umieszczenia fragmentu

Funkcja do skalowania (Citus) zarządza kondycją fragmentu dla poszczególnych rozmieszczenia. Jeśli umieszczenie systemu w stanie niespójnym, Citus automatycznie oznaczy go jako niedostępny. Stan umieszczania jest rejestrowany w tabeli pg_dist_shard_placement w kolumnie shardstate. Oto krótkie omówienie różnych stanów umieszczenia fragmentu:

| Nazwa stanu | Shardstate wartość | Opis                                                                                                                                                                                                                                                                                                                                                                                                                         |
|------------|------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| SFINALIZOWANY  | 1                | Nowy fragmentów stanu jest tworzony w. Fragmentu umieszczane w tym stanie są uznawane za aktualne i są używane w planowaniu i wykonywaniu zapytań.                                                                                                                                                                                                                                                                                 |
| NIEAKTYWNE   | 3                | Fragmentuy w tym stanie są uznawane za nieaktywne ze względu na brak synchronizacji z innymi replikami tego samego fragmentu. Stan może wystąpić, gdy operacja dołączania, modyfikacji (INSERT, UPDATE, DELETE) lub DDL kończy się niepowodzeniem dla tego położenia. Planista zapytań zignoruje przemieszczenie w tym stanie podczas planowania i wykonywania. Użytkownicy mogą synchronizować dane w tych fragmentów z ostateczną repliką jako działaniem w tle. |
| TO_DELETE  | 4                | Jeśli Citus próbuje porzucić rozmieszczenie fragmentu w odpowiedzi na wywołanie master_apply_delete_command i zakończy się niepowodzeniem, umieszczanie jest przenoszone do tego stanu. Następnie użytkownicy mogą usunąć te fragmentów jako kolejne działania w tle.                                                                                                                                                                                                              |

### <a name="worker-node-table"></a>Tabela węzłów procesu roboczego

\_Tabela węzła rozkłowego PG \_ zawiera informacje o węzłach procesu roboczego w klastrze.

| Nazwa             | Typ    | Opis                                                                                                                                                                                |
|------------------|---------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| NodeId           | int     | Wygenerowany automatycznie identyfikator dla danego węzła.                                                                                                                                          |
| GroupID          | int     | Identyfikator używany do oznaczania grupy jeden serwer podstawowy i zero lub więcej serwerów pomocniczych, gdy używany jest model replikacji strumieniowej. Domyślnie jest to taka sama jak NodeId.         |
| nodename         | tekst    | Nazwa hosta lub adres IP węzła procesu roboczego PostgreSQL.                                                                                                                                     |
| nodeport         | int     | Numer portu, na którym nasłuchuje węzeł procesu roboczego PostgreSQL.                                                                                                                              |
| noderack         | tekst    | Obowiązkowe Informacje o rozłożeniu stojaka dla węzła procesu roboczego.                                                                                                                                 |
| hasmetadata      | boolean | Zarezerwowane do użytku wewnętrznego.                                                                                                                                                                 |
| IsActive         | boolean | Czy węzeł akceptuje fragmentu umieszczania.                                                                                                                                     |
| noderole         | tekst    | Czy węzeł jest podstawowym, czy pomocniczym                                                                                                                                                 |
| nodecluster      | tekst    | Nazwa klastra zawierającego ten węzeł                                                                                                                                               |
| shouldhaveshards | boolean | Jeśli wartość jest równa false, fragmentów zostanie przesunięty do węzła (opróżniany) w przypadku ponownego zrównoważenia, a fragmentów z nowych tabel rozproszonych nie będą umieszczane w węźle, chyba że znajdują się one w fragmentów już tam |

```
SELECT * from pg_dist_node;
 nodeid | groupid | nodename  | nodeport | noderack | hasmetadata | isactive | noderole | nodecluster | shouldhaveshards
--------+---------+-----------+----------+----------+-------------+----------+----------+-------------+------------------
      1 |       1 | localhost |    12345 | default  | f           | t        | primary  | default     | t
      2 |       2 | localhost |    12346 | default  | f           | t        | primary  | default     | t
      3 |       3 | localhost |    12347 | default  | f           | t        | primary  | default     | t
(3 rows)
```

### <a name="distributed-object-table"></a>Tabela obiektów rozproszonych

\_Tabela obiektów rozkłu Citus.pg \_ zawiera listę obiektów, takich jak typy i funkcje, które zostały utworzone w węźle koordynatora i rozpropagowane do węzłów procesu roboczego. Gdy administrator dodaje nowe węzły procesu roboczego do klastra, funkcja wieloskalowania (Citus) automatycznie tworzy kopie obiektów rozproszonych w nowych węzłach (w odpowiedniej kolejności, w której są spełnione zależności obiektów).

| Nazwa                        | Typ    | Opis                                          |
|-----------------------------|---------|------------------------------------------------------|
| Identyfikator ClassID                     | OID     | Klasa obiektu rozproszonego                      |
| objid                       | OID     | Identyfikator obiektu obiektu rozproszonego                  |
| objsubid                    | liczba całkowita | Identyfikator podrzędny obiektu obiektu rozproszonego, na przykład attnum |
| typ                        | tekst    | Część stabilnego adresu używanego podczas uaktualnień PG   |
| object_names                | tekst []  | Część stabilnego adresu używanego podczas uaktualnień PG   |
| object_args                 | tekst []  | Część stabilnego adresu używanego podczas uaktualnień PG   |
| distribution_argument_index | liczba całkowita | Prawidłowy tylko w przypadku funkcji/procedur rozproszonych      |
| colocationid                | liczba całkowita | Prawidłowy tylko w przypadku funkcji/procedur rozproszonych      |

\"Stabilne adresy \" jednoznacznie identyfikują obiekty niezależnie od określonego serwera. Funkcja do skalowania (Citus) śledzi obiekty podczas uaktualnienia PostgreSQL przy użyciu stabilnych adresów utworzonych za pomocą funkcji [ \_ Identyfikuj \_ obiekt \_ jako \_ adres ()](https://www.postgresql.org/docs/current/functions-info.html#FUNCTIONS-INFO-OBJECT-TABLE) .

Oto \' przykład sposobu `create_distributed_function()` dodawania wpisów do `citus.pg_dist_object` tabeli:

```psql
CREATE TYPE stoplight AS enum ('green', 'yellow', 'red');

CREATE OR REPLACE FUNCTION intersection()
RETURNS stoplight AS $$
DECLARE
        color stoplight;
BEGIN
        SELECT *
          FROM unnest(enum_range(NULL::stoplight)) INTO color
         ORDER BY random() LIMIT 1;
        RETURN color;
END;
$$ LANGUAGE plpgsql VOLATILE;

SELECT create_distributed_function('intersection()');

-- will have two rows, one for the TYPE and one for the FUNCTION
TABLE citus.pg_dist_object;
```

```
-[ RECORD 1 ]---------------+------
classid                     | 1247
objid                       | 16780
objsubid                    | 0
type                        |
object_names                |
object_args                 |
distribution_argument_index |
colocationid                |
-[ RECORD 2 ]---------------+------
classid                     | 1255
objid                       | 16788
objsubid                    | 0
type                        |
object_names                |
object_args                 |
distribution_argument_index |
colocationid                |
```

### <a name="colocation-group-table"></a>Tabela grup wspólnej lokalizacji

Tabela wspólnej lokalizacji w ramach funkcji ROZKŁ-PG \_ \_ zawiera informacje o tym, które tabele \' fragmentów należy [](concepts-hyperscale-colocation.md)umieścić razem, lub wspólnie.
Gdy dwie tabele znajdują się w tej samej grupie wspólnej lokalizacji, funkcja przeskalowania (Citus) gwarantuje, że fragmentów z tymi samymi wartościami partycji będą umieszczane w tych samych węzłach procesu roboczego.
W ramach wspólnej lokalizacji można dołączać optymalizacje, niektóre rozproszone pakiety zbiorcze i obsłudze kluczy obcych. Fragmentu współlokalizacja jest wnioskowana, gdy liczba fragmentu, współczynniki replikacji i typ kolumny partycji są zgodne między dwiema tabelami; Jednak w razie potrzeby można określić niestandardową grupę wspólnej lokalizacji podczas tworzenia tabeli rozproszonej.

| Nazwa                   | Typ | Opis                                                                   |
|------------------------|------|-------------------------------------------------------------------------------|
| colocationid           | int  | Unikatowy identyfikator grupy wspólnej lokalizacji, do której odnosi się ten wiersz.          |
| shardcount             | int  | Liczba fragmentu dla wszystkich tabel w tej grupie wspólnej lokalizacji                          |
| replicationfactor      | int  | Współczynnik replikacji dla wszystkich tabel w tej grupie wspólnej lokalizacji.                  |
| distributioncolumntype | OID  | Typ kolumny dystrybucji dla wszystkich tabel w tej grupie wspólnej lokalizacji. |

```
SELECT * from pg_dist_colocation;
  colocationid | shardcount | replicationfactor | distributioncolumntype 
 --------------+------------+-------------------+------------------------
             2 |         32 |                 2 |                     20
  (1 row)
```

### <a name="rebalancer-strategy-table"></a>Tabela strategii modułu równoważenia obciążenia

W tej tabeli opisano strategie, których [rebalance_table_shards](reference-hyperscale-functions.md#rebalance_table_shards) może użyć do określenia, gdzie przenieść fragmentów.

| Nazwa                           | Typ    | Opis                                                                                                                                       |
|--------------------------------|---------|---------------------------------------------------------------------------------------------------------------------------------------------------|
| default_strategy               | boolean | Czy rebalance_table_shards powinien wybrać tę strategię domyślnie. Użyj citus_set_default_rebalance_strategy, aby zaktualizować tę kolumnę             |
| shard_cost_function            | regproc | Identyfikator funkcji kosztu, która musi przyjmować shardid jako bigint i zwracać swoje koncepcje o koszt, jako wartość rzeczywista                                |
| node_capacity_function         | regproc | Identyfikator funkcji pojemności, która musi przyjmować NodeId jako int, i zwracać jej koncepcję pojemności węzła jako rzeczywistego typu                          |
| shard_allowed_on_node_function | regproc | Identyfikator funkcji, która podaje shardid bigint i nodeidarg int, zwraca wartość Boolean dla tego, czy Citus może przechowywać fragmentu w węźle |
| default_threshold              | float4  | Próg uznawany za pełny lub zbyt pusty węzła, który określa, kiedy rebalance_table_shards powinna próbować przenieść fragmentów                    |
| minimum_threshold              | float4  | Zabezpieczenie uniemożliwiające zbyt niską wartość argumentu progu rebalance_table_shards ()                                                  |

Instalacja ze skalowaniem (Citus) jest dostarczana z tymi strategiami w tabeli:

```postgresql
SELECT * FROM pg_dist_rebalance_strategy;
```

```
-[ RECORD 1 ]-------------------+-----------------------------------
Name                            | by_shard_count
default_strategy                | true
shard_cost_function             | citus_shard_cost_1
node_capacity_function          | citus_node_capacity_1
shard_allowed_on_node_function  | citus_shard_allowed_on_node_true
default_threshold               | 0
minimum_threshold               | 0
-[ RECORD 2 ]-------------------+-----------------------------------
Name                            | by_disk_size
default_strategy                | false
shard_cost_function             | citus_shard_cost_by_disk_size
node_capacity_function          | citus_node_capacity_1
shard_allowed_on_node_function  | citus_shard_allowed_on_node_true
default_threshold               | 0.1
minimum_threshold               | 0.01
```

Domyślna strategia, `by_shard_count` przypisuje każdemu fragmentu tego samego kosztu. Jego efektem jest wyrównywanie liczby fragmentu w różnych węzłach. W ramach innej wstępnie zdefiniowanej strategii `by_disk_size` program przypisuje koszt do każdego fragmentuu pasującego do jego rozmiaru dysku w bajtach oraz z fragmentów, które znajdują się na nim. Rozmiar dysku jest obliczany przy użyciu `pg_total_relation_size` , dlatego zawiera indeksy. Ta strategia próbuje osiągnąć to samo miejsce na dysku w każdym węźle. Należy zauważyć próg 0,1--zapobiega niepotrzebnemu przeniesieniu fragmentu spowodowanym niewielkimi różnicami w miejscu na dysku.

#### <a name="creating-custom-rebalancer-strategies"></a>Tworzenie niestandardowych strategii modułu równoważenia obciążenia

Poniżej przedstawiono przykłady funkcji, które mogą być używane w ramach nowych strategii modułu równoważenia fragmentu oraz zarejestrowane w [pg_dist_rebalance_strategy](reference-hyperscale-metadata.md?#rebalancer-strategy-table) za pomocą funkcji [citus_add_rebalance_strategy](reference-hyperscale-functions.md#citus_add_rebalance_strategy) .

-   Ustawianie wyjątku pojemności węzła według wzorca nazwy hosta:

    ```postgresql
    CREATE FUNCTION v2_node_double_capacity(nodeidarg int)
        RETURNS boolean AS $$
        SELECT
            (CASE WHEN nodename LIKE '%.v2.worker.citusdata.com' THEN 2 ELSE 1 END)
        FROM pg_dist_node where nodeid = nodeidarg
        $$ LANGUAGE sql;
    ```

-   Ponowne równoważenie według liczby zapytań, które przechodzą do fragmentu, zgodnie z oczekiwaniami [citus_stat_statements](reference-hyperscale-metadata.md#query-statistics-table):

    ```postgresql
    -- example of shard_cost_function

    CREATE FUNCTION cost_of_shard_by_number_of_queries(shardid bigint)
        RETURNS real AS $$
        SELECT coalesce(sum(calls)::real, 0.001) as shard_total_queries
        FROM citus_stat_statements
        WHERE partition_key is not null
            AND get_shard_id_for_distribution_column('tab', partition_key) = shardid;
    $$ LANGUAGE sql;
    ```

-   Izolowanie określonego fragmentu (10000) w węźle (adres \' 10.0.0.1 \' ):

    ```postgresql
    -- example of shard_allowed_on_node_function

    CREATE FUNCTION isolate_shard_10000_on_10_0_0_1(shardid bigint, nodeidarg int)
        RETURNS boolean AS $$
        SELECT
            (CASE WHEN nodename = '10.0.0.1' THEN shardid = 10000 ELSE shardid != 10000 END)
        FROM pg_dist_node where nodeid = nodeidarg
        $$ LANGUAGE sql;

    -- The next two definitions are recommended in combination with the above function.
    -- This way the average utilization of nodes is not impacted by the isolated shard.
    CREATE FUNCTION no_capacity_for_10_0_0_1(nodeidarg int)
        RETURNS real AS $$
        SELECT
            (CASE WHEN nodename = '10.0.0.1' THEN 0 ELSE 1 END)::real
        FROM pg_dist_node where nodeid = nodeidarg
        $$ LANGUAGE sql;
    CREATE FUNCTION no_cost_for_10000(shardid bigint)
        RETURNS real AS $$
        SELECT
            (CASE WHEN shardid = 10000 THEN 0 ELSE 1 END)::real
        $$ LANGUAGE sql;
    ```

### <a name="query-statistics-table"></a>Tabela statystyk zapytania

Funkcja przeskalowanie (Citus) zapewnia `citus_stat_statements` statystykę dotyczącą wykonywania zapytań i dla kogo. \'Analogicznie do (i może być sprzężone) widok " [PG \_ stat \_ Statement](https://www.postgresql.org/docs/current/static/pgstatstatements.html) " w PostgreSQL, który śledzi dane statystyczne dotyczące szybkości zapytań.

Ten widok umożliwia śledzenie zapytań dotyczących dzierżawców w aplikacji z wieloma dzierżawami, co ułatwia podejmowanie decyzji o tym, kiedy należy przeprowadzić izolację dzierżawy.

| Nazwa          | Typ   | Opis                                                                      |
|---------------|--------|----------------------------------------------------------------------------------|
| QueryId       | bigint | Identyfikator (dobry dla pg_stat_statements sprzężeń)                                   |
| userid        | OID    | Użytkownik, który uruchomił zapytanie                                                           |
| DBID          | OID    | wystąpienie bazy danych koordynatora                                                 |
| query         | tekst   | ciąg zapytania anonimowe                                                          |
| przetwarzania      | tekst   | Citus używany moduł wykonujący: adaptacyjny, w czasie rzeczywistym, śledzenie zadań, router lub wstawianie — wybór |
| partition_key | tekst   | wartość kolumny dystrybucji w zapytaniach wykonanych przez router, w przeciwnym razie wartość NULL               |
| Rozmowa         | bigint | liczba uruchomień zapytania                                                |

```sql
-- create and populate distributed table
create table foo ( id int );
select create_distributed_table('foo', 'id');
insert into foo select generate_series(1,100);

-- enable stats
-- pg_stat_statements must be in shared_preload libraries
create extension pg_stat_statements;

select count(*) from foo;
select * from foo where id = 42;

select * from citus_stat_statements;
```

Wyniki:

```
-[ RECORD 1 ]-+----------------------------------------------
queryid       | -909556869173432820
userid        | 10
dbid          | 13340
query         | insert into foo select generate_series($1,$2)
executor      | insert-select
partition_key |
calls         | 1
-[ RECORD 2 ]-+----------------------------------------------
queryid       | 3919808845681956665
userid        | 10
dbid          | 13340
query         | select count(*) from foo;
executor      | adaptive
partition_key |
calls         | 1
-[ RECORD 3 ]-+----------------------------------------------
queryid       | 5351346905785208738
userid        | 10
dbid          | 13340
query         | select * from foo where id = $1
executor      | adaptive
partition_key | 42
calls         | 1
```

Zastrzeżenia

-   Dane statystyczne nie są replikowane i są wygrane \' awarie bazy danych lub przełączenia w tryb failover
-   Śledzi ograniczoną liczbę zapytań, ustawianych przez `pg_stat_statements.max` GUC (domyślnie 5000)
-   Aby OBCIĄĆ tabelę, użyj `citus_stat_statements_reset()` funkcji

### <a name="distributed-query-activity"></a>Działanie zapytania rozproszonego

Funkcja Citus) oferuje specjalne widoki do oglądania zapytań i blokad w całym klastrze, w tym zapytań specyficznych dla fragmentu używanych wewnętrznie do kompilowania wyników dla zapytań rozproszonych.

-   **\_ \_ \_ działanie stat Citus w czasie** wykonywania: pokazuje zapytania rozproszone, które są wykonywane na wszystkich węzłach. Nadzbiór `pg_stat_activity` , można użyć wszędzie tam, gdzie jest to ostatnie.
-   **\_ \_ \_ działanie stat Citus procesu roboczego**: pokazuje zapytania dla pracowników, w tym zapytania dotyczące fragmentów względem poszczególnych fragmentów.
-   **Citus \_ Lock \_ czeka**: zablokowane zapytania w klastrze.

Pierwsze dwa widoki obejmują wszystkie kolumny [ \_ \_ działania stat (PG](https://www.postgresql.org/docs/current/static/monitoring-stats.html#PG-STAT-ACTIVITY-VIEW) ) oraz hosta hosta/portu procesu roboczego, który zainicjował zapytanie i hosta/portu węzła koordynatora w klastrze.

Rozważmy na przykład liczenie wierszy w rozproszonej tabeli:

```postgresql
-- run from worker on localhost:9701

SELECT count(*) FROM users_table;
```

Zobaczysz zapytanie w `citus_dist_stat_activity` :

```postgresql
SELECT * FROM citus_dist_stat_activity;

-[ RECORD 1 ]----------+----------------------------------
query_hostname         | localhost
query_hostport         | 9701
master_query_host_name | localhost
master_query_host_port | 9701
transaction_number     | 1
transaction_stamp      | 2018-10-05 13:27:20.691907+03
datid                  | 12630
datname                | postgres
pid                    | 23723
usesysid               | 10
usename                | citus
application\_name      | psql
client\_addr           | 
client\_hostname       | 
client\_port           | -1
backend\_start         | 2018-10-05 13:27:14.419905+03
xact\_start            | 2018-10-05 13:27:16.362887+03
query\_start           | 2018-10-05 13:27:20.682452+03
state\_change          | 2018-10-05 13:27:20.896546+03
wait\_event_type       | Client
wait\_event            | ClientRead
state                  | idle in transaction
backend\_xid           | 
backend\_xmin          | 
query                  | SELECT count(*) FROM users_table;
backend\_type          | client backend
```

To zapytanie wymaga informacji ze wszystkich fragmentów. Niektóre informacje znajdują się w fragmentu `users_table_102038` , co ma być przechowywane w `localhost:9700` . Zobaczysz zapytanie uzyskujący dostęp do fragmentu, przeglądając `citus_worker_stat_activity` Widok:

```postgresql
SELECT * FROM citus_worker_stat_activity;

-[ RECORD 1 ]----------+-----------------------------------------------------------------------------------------
query_hostname         | localhost
query_hostport         | 9700
master_query_host_name | localhost
master_query_host_port | 9701
transaction_number     | 1
transaction_stamp      | 2018-10-05 13:27:20.691907+03
datid                  | 12630
datname                | postgres
pid                    | 23781
usesysid               | 10
usename                | citus
application\_name      | citus
client\_addr           | ::1
client\_hostname       | 
client\_port           | 51773
backend\_start         | 2018-10-05 13:27:20.75839+03
xact\_start            | 2018-10-05 13:27:20.84112+03
query\_start           | 2018-10-05 13:27:20.867446+03
state\_change          | 2018-10-05 13:27:20.869889+03
wait\_event_type       | Client
wait\_event            | ClientRead
state                  | idle in transaction
backend\_xid           | 
backend\_xmin          | 
query                  | COPY (SELECT count(*) AS count FROM users_table_102038 users_table WHERE true) TO STDOUT
backend\_type          | client backend
```

W `query` polu są wyświetlane dane, które są kopiowane z fragmentu do zliczenia.

> [!NOTE]
> Jeśli zapytanie routera (np. Single-dzierżawca w aplikacji z wieloma dzierżawcami, "SELECT"
> * Z tabeli, w której tenant_id = X ") jest wykonywane bez bloku transakcji, \_ \_ Nazwa hosta zapytania głównego \_ i \_ \_ kolumny portów hosta zapytania głównego \_ będą mieć wartość null w \_ \_ działaniu stat Citus procesu roboczego \_ .

Aby zobaczyć `citus_lock_waits` , jak działa, możemy ręcznie wygenerować sytuację blokowania. Najpierw \' skonfigurujemy tabelę testową od koordynatora:

```postgresql
CREATE TABLE numbers AS
  SELECT i, 0 AS j FROM generate_series(1,10) AS i;
SELECT create_distributed_table('numbers', 'i');
```

Następnie można uruchomić następującą sekwencję instrukcji przy użyciu dwóch sesji w ramach koordynatora:

```postgresql
-- session 1                           -- session 2
-------------------------------------  -------------------------------------
BEGIN;
UPDATE numbers SET j = 2 WHERE i = 1;
                                       BEGIN;
                                       UPDATE numbers SET j = 3 WHERE i = 1;
                                       -- (this blocks)
```

`citus_lock_waits`Widok przedstawia sytuację.

```postgresql
SELECT * FROM citus_lock_waits;

-[ RECORD 1 ]-------------------------+----------------------------------------
waiting_pid                           | 88624
blocking_pid                          | 88615
blocked_statement                     | UPDATE numbers SET j = 3 WHERE i = 1;
current_statement_in_blocking_process | UPDATE numbers SET j = 2 WHERE i = 1;
waiting_node_id                       | 0
blocking_node_id                      | 0
waiting_node_name                     | coordinator_host
blocking_node_name                    | coordinator_host
waiting_node_port                     | 5432
blocking_node_port                    | 5432
```

W tym przykładzie zapytania pochodzą z koordynatora, ale widok może również wyświetlać blokady między zapytaniami pochodzącymi z procesów roboczych (wykonywanych za pomocą funkcji Citus) MX dla wystąpienia).

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak niektóre [funkcje funkcji Citus (deskaling)](reference-hyperscale-functions.md) zmieniają tabele systemowe
* Przegląd pojęć związanych z [węzłami i tabelami](concepts-hyperscale-nodes.md)
