---
title: 'Samouczek: fragmentu danych na węzłach procesu roboczego — Citus) — Azure Database for PostgreSQL'
description: W tym samouczku przedstawiono sposób tworzenia tabel rozproszonych i wizualizacji ich dystrybucji danych przy użyciu funkcji Azure Database for PostgreSQL Citus.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.custom: mvc
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 12/16/2020
ms.openlocfilehash: 7d93002af866aa653972182a13ea37d37e912ce8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97630313"
---
# <a name="tutorial-shard-data-on-worker-nodes-in-azure-database-for-postgresql--hyperscale-citus"></a>Samouczek: fragmentu danych na węzłach procesu roboczego w Azure Database for PostgreSQL — funkcja do skalowania (Citus)

W tym samouczku użyjemy Azure Database for PostgreSQL-Citus, aby dowiedzieć się, jak:

> [!div class="checklist"]
> * Tworzenie fragmentów rozproszonego
> * Zobacz, gdzie są umieszczone fragmentów tabeli
> * Identyfikuj rozkład skośny
> * Tworzenie ograniczeń w tabelach rozproszonych
> * Uruchamianie zapytań dotyczących danych rozproszonych

## <a name="prerequisites"></a>Wymagania wstępne

Ten samouczek wymaga grupy serwerów z uruchomioną Citusą z dwoma węzłami procesu roboczego. Jeśli nie masz działającej grupy serwerów, postępuj zgodnie z samouczkiem [Tworzenie grupy serwerów](tutorial-hyperscale-server-group.md) , a następnie wróć do tego.

## <a name="hash-distributed-data"></a>Dane rozproszone z mieszaniem

Dystrybuowanie wierszy tabeli na wielu serwerach PostgreSQL jest kluczową techniką dla skalowalnych zapytań w ramach skalowania (Citus). Jednocześnie wiele węzłów może przechowywać więcej danych niż tradycyjna baza danych, a w wielu przypadkach można korzystać z procesorów roboczych równolegle w celu wykonywania zapytań.

W sekcji wymagania wstępne została utworzona grupa serwerów ze skalowaniem (Citus) z dwoma węzłami procesu roboczego.

![Koordynator i dwaj pracownicy](tutorial-hyperscale-shard/nodes.png)

Tabele metadanych węzła koordynatora śledzą pracowników i rozproszone dane. W tabeli [pg_dist_node](reference-hyperscale-metadata.md#worker-node-table) można sprawdzić aktywnych procesów roboczych.

```sql
select nodeid from pg_dist_node where isactive;
```
```
 nodeid | nodename
--------+-----------
      1 | 10.0.0.21
      2 | 10.0.0.23
```

> [!NOTE]
> Nodenames na Citus) są wewnętrznymi adresami IP w sieci wirtualnej, a rzeczywiste adresy mogą się różnić.

### <a name="rows-shards-and-placements"></a>Wiersze, fragmentów i miejsca

Aby korzystać z zasobów procesora i magazynu dla węzłów procesu roboczego, należy rozpowszechnić dane tabeli w całej grupie serwerów.  Dystrybuowanie tabeli przypisuje każdy wiersz do grupy logicznej o nazwie *fragmentu.* Utwórzmy tabelę i rozpowszechnimy ją:

```sql
-- create a table on the coordinator
create table users ( email text primary key, bday date not null );

-- distribute it into shards on workers
select create_distributed_table('users', 'email');
```

Funkcja Citus) przypisuje każdy wiersz do fragmentu na podstawie wartości *kolumny dystrybucji*, która w naszym przypadku została określona jako `email` . Każdy wiersz będzie w dokładnie jednym fragmentu, a każdy fragmentu może zawierać wiele wierszy.

![tabela użytkownicy z wierszami wskazującymi fragmentów](tutorial-hyperscale-shard/table.png)

Domyślnie `create_distributed_table()` wprowadza 32 fragmentów, jak widać przez zliczanie w tabeli metadanych [pg_dist_shard](reference-hyperscale-metadata.md#shard-table):

```sql
select logicalrelid, count(shardid)
  from pg_dist_shard
 group by logicalrelid;
```
```
 logicalrelid | count
--------------+-------
 users        |    32
```

Funkcja do skalowania (Citus) używa `pg_dist_shard` tabeli do przypisywania wierszy do fragmentów na podstawie wartości skrótu wartości w kolumnie dystrybucja. Szczegóły mieszania są nieważne dla tego samouczka. Co oznacza, że możemy wykonywać zapytania, aby zobaczyć, które wartości są mapowane dla fragmentu identyfikatorów:

```sql
-- Where would a row containing hi@test.com be stored?
-- (The value doesn't have to actually be present in users, the mapping
-- is a mathematical operation consulting pg_dist_shard.)
select get_shard_id_for_distribution_column('users', 'hi@test.com');
```
```
 get_shard_id_for_distribution_column
--------------------------------------
                               102008
```

Mapowanie wierszy do fragmentów jest czysto logiczne. Fragmentów musi być przypisany do określonych węzłów procesu roboczego dla magazynu, w którym funkcja przeskalowania (Citus) wywołuje *fragmentu umieszczanie*.

![fragmentów przypisany do pracowników](tutorial-hyperscale-shard/shard-placement.png)

Możemy przyjrzeć się fragmentuom w [pg_dist_placement](reference-hyperscale-metadata.md#shard-placement-table).
Dołączenie go do innych obserwowanych tabel metadanych pokazuje, gdzie przebywa każdy fragmentu.

```sql
-- limit the output to the first five placements

select
    shard.logicalrelid as table,
    placement.shardid as shard,
    node.nodename as host
from
    pg_dist_placement placement,
    pg_dist_node node,
    pg_dist_shard shard
where placement.groupid = node.groupid
  and shard.shardid = placement.shardid
order by shard
limit 5;
```
```
 table | shard  |    host
-------+--------+------------
 users | 102008 | 10.0.0.21
 users | 102009 | 10.0.0.23
 users | 102010 | 10.0.0.21
 users | 102011 | 10.0.0.23
 users | 102012 | 10.0.0.21
```

### <a name="data-skew"></a>Pochylenie danych

Grupa serwerów działa najbardziej wydajnie, gdy dane są umieszczane równo w węzłach procesu roboczego, a dane są umieszczane razem w tych samych pracownikach. W tej sekcji skupmy się na pierwszej części, jednorodności umieszczania.

Aby zademonstrować, Utwórzmy przykładowe dane dla naszej `users` tabeli:

```sql
-- load sample data
insert into users
select
    md5(random()::text) || '@test.com',
    date_trunc('day', now() - random()*'100 years'::interval)
from generate_series(1, 1000);
```

Aby wyświetlić rozmiary fragmentu, można uruchomić [funkcje rozmiaru tabeli](https://www.postgresql.org/docs/current/functions-admin.html#FUNCTIONS-ADMIN-DBSIZE) na fragmentów.

```sql
-- sizes of the first five shards
select *
from
    run_command_on_shards('users', $cmd$
      select pg_size_pretty(pg_table_size('%1$s'));
    $cmd$)
order by shardid
limit 5;
```
```
 shardid | success | result
---------+---------+--------
  102008 | t       | 16 kB
  102009 | t       | 16 kB
  102010 | t       | 16 kB
  102011 | t       | 16 kB
  102012 | t       | 16 kB
```

Możemy zobaczyć, że fragmentów są równej wielkości. Znaleźliśmy już, że rozmieszczenia są równomiernie dystrybuowane między procesami roboczymi, więc możemy wnioskować, że węzły procesu roboczego przechowują w przybliżeniu równe liczby wierszy.

Wiersze w naszym `users` przykładzie są dystrybuowane równomiernie, ponieważ właściwości kolumny dystrybucji `email` .

1. Liczba adresów e-mail była większa lub równa liczbie fragmentów.
2. Liczba wierszy na adres e-mail była podobna (w naszym przypadku dokładnie jeden wiersz na adres, ponieważ deklarujemy pocztą e-mail klucz).

Dowolna opcja tabeli i kolumny dystrybucji, w której każda właściwość nie powiedzie się, będzie kończyć się nierównym rozmiarem danych w przypadku pracowników, czyli *pochylenie danych*.

### <a name="add-constraints-to-distributed-data"></a>Dodawanie ograniczeń do danych rozproszonych

Funkcja Citus) umożliwia dalsze korzystanie z bezpieczeństwa relacyjnej bazy danych, w tym [ograniczeń bazy danych](https://www.postgresql.org/docs/current/ddl-constraints.html).
Istnieje jednak ograniczenie. Ze względu na charakter systemów rozproszonych funkcja wieloskalowania (Citus) nie umożliwia ograniczenia unikatowości między odwołaniami ani integralności referencyjnej między węzłami procesu roboczego.

Rozważmy nasz `users` przykład tabeli w powiązanej tabeli.

```sql
-- books that users own
create table books (
    owner_email text references users (email),
    isbn text not null,
    title text not null
);

-- distribute it
select create_distributed_table('books', 'owner_email');
```

W celu zapewnienia efektywności rozpowszechniamy `books` ten sam sposób, co `users` : według adresu e-mail właściciela. Dystrybucja według podobnych wartości kolumn jest nazywana [współlokalizacją](concepts-hyperscale-colocation.md).

Nie wystąpił problem z dystrybucją książek z kluczem obcym dla użytkowników, ponieważ klucz znajduje się w kolumnie dystrybucji. Jednak wystąpił problem podczas tworzenia `isbn` klucza:

```sql
-- will not work
alter table books add constraint books_isbn unique (isbn);
```
```
ERROR:  cannot create constraint on "books"
DETAIL: Distributed relations cannot have UNIQUE, EXCLUDE, or
        PRIMARY KEY constraints that do not include the partition column
        (with an equality operator if EXCLUDE).
```

W tabeli rozproszonej najlepszym rozwiązaniem jest to, że kolumny są unikatowe modulo w kolumnie dystrybucja:

```sql
-- a weaker constraint is allowed
alter table books add constraint books_isbn unique (owner_email, isbn);
```

Powyższe ograniczenie jedynie sprawia, że unikatowy dla każdego użytkownika ma wartość ISBN. Innym rozwiązaniem jest utworzenie książek jako [tabeli referencyjnej](howto-hyperscale-modify-distributed-tables.md#reference-tables) , a nie rozproszonej tabeli, a następnie utworzenie oddzielnej tabeli rozproszonej, która kojarzy książki z użytkownikami.

## <a name="query-distributed-tables"></a>Wykonywanie zapytań w tabelach rozproszonych

W poprzednich sekcjach pokazano, jak rozproszone wiersze tabeli są umieszczane w fragmentów w węzłach procesu roboczego. Większość czasu nie musisz wiedzieć, jak i gdzie są przechowywane dane w grupie serwerów. Funkcja wieloskalowania (Citus) ma rozproszonego modułu wykonującego zapytania, który automatycznie dzieli regularne zapytania SQL. Uruchamia je równolegle na węzłach procesu roboczego blisko danych.

Na przykład można uruchomić zapytanie w celu znalezienia średniego wieku użytkowników, traktując tabelę rozproszoną, `users` taką jak zwykła tabela na koordynatorze.

```sql
select avg(current_date - bday) as avg_days_old from users;
```
```
    avg_days_old
--------------------
 17926.348000000000
```

![Kwerenda przechodzenia do fragmentów przez koordynatora](tutorial-hyperscale-shard/query-fragments.png)

W tle program wykonujący skalowanie (Citus) tworzy oddzielne zapytanie dla każdego fragmentuu, uruchamia je w procesach roboczych i łączy wynik. Jeśli używasz polecenia WYJAŚNIj PostgreSQL, możesz go zobaczyć:

```sql
explain select avg(current_date - bday) from users;
```
```
                                  QUERY PLAN
----------------------------------------------------------------------------------
 Aggregate  (cost=500.00..500.02 rows=1 width=32)
   ->  Custom Scan (Citus Adaptive)  (cost=0.00..0.00 rows=100000 width=16)
     Task Count: 32
     Tasks Shown: One of 32
     ->  Task
       Node: host=10.0.0.21 port=5432 dbname=citus
       ->  Aggregate  (cost=41.75..41.76 rows=1 width=16)
         ->  Seq Scan on users_102040 users  (cost=0.00..22.70 rows=1270 width=4)
```

W danych wyjściowych przedstawiono przykładowy plan wykonania dla *fragmentu zapytania* działającego w systemie fragmentu 102040 (tabela `users_102040` w obszarze roboczym 10.0.0.21). Inne fragmenty nie są wyświetlane, ponieważ są podobne. Widzimy, że węzeł procesu roboczego skanuje tabele fragmentu i stosuje agregację. Węzeł koordynatora łączy zagregowane wyniki.

## <a name="next-steps"></a>Następne kroki

W tym samouczku utworzyliśmy tabelę rozproszoną i uczymy się jej fragmentów i miejsc. Napotkaliśmy wyzwanie przy użyciu ograniczenia unikatowości i ograniczeń klucza obcego, a wreszcie zawarto, jak zapytania rozproszone działają na wysokim poziomie.

* Dowiedz się więcej na temat [typów tabel](concepts-hyperscale-nodes.md) ze skalą (Citus)
* Uzyskaj więcej porad na temat [wybierania kolumny dystrybucji](concepts-hyperscale-choose-distribution-column.md)
* Poznaj zalety wspólnej [lokalizacji tabeli](concepts-hyperscale-colocation.md)
