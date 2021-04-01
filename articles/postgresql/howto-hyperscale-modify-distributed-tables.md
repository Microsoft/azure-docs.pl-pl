---
title: Modyfikowanie tabel rozproszonych — funkcja do skalowania (Citus) — Azure Database for PostgreSQL
description: Polecenia SQL służące do tworzenia i modyfikowania tabel rozproszonych — funkcja Citus) przy użyciu Azure Portal
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 8/10/2020
ms.openlocfilehash: cf9f9ca5b8690a38c6e5aa6f519378c0a2e3a4f2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "95026441"
---
# <a name="distribute-and-modify-tables"></a>Rozpowszechnianie i modyfikowanie tabel

## <a name="distributing-tables"></a>Dystrybuowanie tabel

Aby utworzyć tabelę rozproszoną, należy najpierw zdefiniować schemat tabeli. W tym celu można zdefiniować tabelę przy użyciu instrukcji [CREATE TABLE](http://www.postgresql.org/docs/current/static/sql-createtable.html) w taki sam sposób, jak w przypadku zwykłej tabeli PostgreSQL.

```sql
CREATE TABLE github_events
(
    event_id bigint,
    event_type text,
    event_public boolean,
    repo_id bigint,
    payload jsonb,
    repo jsonb,
    actor jsonb,
    org jsonb,
    created_at timestamp
);
```

Następnie można użyć \_ funkcji tworzenia rozproszonej \_ tabeli (), aby określić kolumnę dystrybucji tabeli i utworzyć fragmentów procesu roboczego.

```sql
SELECT create_distributed_table('github_events', 'repo_id');
```

Wywołanie funkcji informuje Citus o tym, że \_ tabela zdarzeń GitHub powinna być dystrybuowana w \_ kolumnie Identyfikator repozytorium (przez mieszanie wartości kolumny). Funkcja tworzy również fragmentów na węzłach procesu roboczego przy użyciu \_ \_ \_ wartości konfiguracji współczynnika replikacji Citus. fragmentu i Citus. fragmentu.

Tworzy łączną liczbę Citus. fragmentu liczba \_ fragmentów, gdzie każdy fragmentu jest właścicielem części przestrzeni skrótu i pobiera replikację na podstawie domyślnej \_ \_ wartości konfiguracji współczynnika replikacji Citus. fragmentu. Repliki fragmentu utworzone w procesie roboczym mają ten sam schemat tabeli, indeks i definicje ograniczeń co tabela w koordynatorze. Po utworzeniu replik funkcja zapisuje wszystkie rozproszone metadane w ramach koordynatora.

Każdy utworzony fragmentu ma przypisany unikatowy identyfikator fragmentu, a wszystkie jego repliki mają ten sam identyfikator fragmentu. Fragmentów są reprezentowane w węźle Worker jako zwykłe tabele PostgreSQL o nazwie \' TableName \_ shardid \' , gdzie tablename jest nazwą tabeli rozproszonej, a identyfikator fragmentu jest przypisanym unikatowym identyfikatorem. Można nawiązać połączenie z wystąpieniami Postgres procesu roboczego w celu wyświetlania lub uruchamiania poleceń na poszczególnych fragmentówach.

Teraz można przystąpić do wstawiania danych do rozproszonej tabeli i uruchamiania na niej zapytań. Możesz również dowiedzieć się więcej o formacie UDF używanym w tej sekcji w dokumentacji [języka DDL w tabeli i fragmentu](reference-hyperscale-functions.md#table-and-shard-ddl) .

### <a name="reference-tables"></a>Tabele odwołań

Powyższa metoda dystrybuuje tabele do wielu horyzontalnych fragmentów.  Kolejną możliwością jest dystrybuowanie tabel do jednego fragmentu i replikowanie fragmentu do każdego węzła procesu roboczego. Tabele rozproszone w ten sposób są nazywane *tabelami odwołań.* Są one używane do przechowywania danych, które muszą być często dostępne przez wiele węzłów w klastrze.

Typowymi kandydatami dla tabel referencyjnych są:

-   Mniejsze tabele, które muszą być połączone z większymi rozproszonymi tabelami.
-   Tabele w aplikacjach wielodostępnych, które nie mają kolumny identyfikatora dzierżawy lub które nie znajdują \' t skojarzone z dzierżawcą. (Lub podczas migracji, nawet w przypadku niektórych tabel skojarzonych z dzierżawcą).
-   Tabele, które wymagają unikatowych ograniczeń dla wielu kolumn i są wystarczająco małe.

Na przykład załóżmy, że witryna handlu elektronicznego z wieloma dzierżawcami musi obliczyć podatek sprzedaży dla transakcji w dowolnym z jej magazynów. Informacje podatkowe jest \' t specyficzne dla każdej dzierżawy. Warto umieścić ją w tabeli udostępnionej. Tabela referencyjna zorientowana na stany USA może wyglądać następująco:

```postgresql
-- a reference table

CREATE TABLE states (
  code char(2) PRIMARY KEY,
  full_name text NOT NULL,
  general_sales_tax numeric(4,3)
);

-- distribute it to all workers

SELECT create_reference_table('states');
```

Teraz zapytania, takie jak jeden obliczający podatek dla koszyka, mogą przyłączyć się do `states` tabeli bez obciążenia sieciowego i mogą dodać klucz obcy do kodu stanu w celu lepszego sprawdzenia poprawności.

Oprócz dystrybucji tabeli jako pojedynczej zreplikowanej fragmentu, `create_reference_table` Funkcja UDF oznacza ją jako tabelę referencyjną w tabelach metadanych (Citus). Funkcja Citus) automatycznie wykonuje zatwierdzenia dwufazowe ([2PC](https://en.wikipedia.org/wiki/Two-phase_commit_protocol)) do modyfikacji tabel oznaczonych w ten sposób, co zapewnia gwarancje silnej spójności.

Jeśli masz tabelę rozproszoną z fragmentu liczbą takich elementów, możesz ją uaktualnić do rozpoznanej tabeli referencyjnej, takiej jak:

```postgresql
SELECT upgrade_to_reference_table('table_name');
```

Inny przykład korzystania z tabel referencyjnych można znaleźć w [samouczku bazy danych z wieloma dzierżawcami](tutorial-design-database-hyperscale-multi-tenant.md).

### <a name="distributing-coordinator-data"></a>Dystrybuowanie danych koordynatora

Jeśli istniejąca baza danych PostgreSQL zostanie przekonwertowana na węzeł koordynatora dla klastra ze skalowaniem (Citus), dane w jego tabelach mogą być efektywnie dystrybuowane i z minimalnym przerwaniem działania aplikacji.

`create_distributed_table`Opisana wcześniej funkcja działa w odniesieniu do pustych i niepustych tabel, a dla tej ostatniej automatycznie dystrybuuje wiersze tabeli w całym klastrze. Informacja o tym, czy dane są kopiowane przez obecność komunikatu, jest wyświetlana komunikat \" : kopiowanie danych z tabeli lokalnej \. . \" Na przykład:

```postgresql
CREATE TABLE series AS SELECT i FROM generate_series(1,1000000) i;
SELECT create_distributed_table('series', 'i');
NOTICE:  Copying data from local table...
 create_distributed_table
 --------------------------

 (1 row)
```

Operacje zapisu w tabeli są blokowane podczas migrowania danych, a oczekujące zapisy są obsługiwane jako zapytania rozproszone po zatwierdzeniu funkcji. (Jeśli funkcja zakończy się niepowodzeniem, zapytania stają się ponownie lokalne). Odczyty mogą być kontynuowane jako normalne i staną się zapytaniami rozproszonymi po zatwierdzeniu funkcji.

W przypadku dystrybucji tabel a i B, gdzie A ma klucz obcy do B, należy najpierw rozesłać klucz docelowy tabeli B. Wykonanie tej operacji w niewłaściwej kolejności spowoduje wystąpienie błędu:

```
ERROR:  cannot create foreign key constraint
DETAIL:  Referenced table must be a distributed table or a reference table.
```

Jeśli nie jest możliwe rozpowszechnianie w poprawnej kolejności, należy porzucić klucze obce, rozesłać tabele i ponownie utworzyć klucze obce.

Podczas migrowania danych z zewnętrznej bazy danych, na przykład z usługi Amazon RDS do Citus w chmurze, należy najpierw utworzyć rozproszone tabele (Citus), `create_distributed_table` a następnie skopiować dane do tabeli.
Kopiowanie do tabel rozproszonych zapobiega braku miejsca na węźle koordynatora.

## <a name="colocating-tables"></a>Lokalizowanie tabel

Kolokacja oznacza umieszczenie pokrewnych informacji na tych samych komputerach. Umożliwia wydajne wykonywanie zapytań, wykorzystując jednocześnie skalowalność w poziomie dla całego zestawu danych. Aby uzyskać więcej informacji, zobacz temat [kolokacja](concepts-hyperscale-colocation.md).

Tabele znajdują się w grupach. Aby ręcznie kontrolować przypisanie grupy wspólnej lokalizacji tabeli, należy użyć opcjonalnego `colocate_with` parametru `create_distributed_table` . Jeśli nie masz \' opieki nad \' współlokalizacją tabeli, Pomiń ten parametr. Domyślnie jest to wartość `'default'` , która grupuje tabelę z dowolną inną domyślną tabelą wspólnej lokalizacji z tym samym typem kolumny rozkładu, liczbą fragmentu i czynnikiem replikacji. Jeśli chcesz przerwać lub zaktualizować tę niejawną współlokalizację, możesz użyć `update_distributed_table_colocation()` .

```postgresql
-- these tables are implicitly co-located by using the same
-- distribution column type and shard count with the default
-- co-location group

SELECT create_distributed_table('A', 'some_int_col');
SELECT create_distributed_table('B', 'other_int_col');
```

Gdy nowa tabela nie jest powiązana z innymi w swojej niejawnej grupie wspólnej lokalizacji, określ `colocated_with => 'none'` .

```postgresql
-- not co-located with other tables

SELECT create_distributed_table('A', 'foo', colocate_with => 'none');
```

Podział niepowiązanych tabel na własne grupy wspólnej lokalizacji poprawi [fragmentu](howto-hyperscale-scale-rebalance.md) ponownej wydajności, ponieważ fragmentów w tej samej grupie należy przenieść razem.

Gdy tabele są rzeczywiście powiązane (na przykład gdy zostaną dołączone), może być zrozumiałe, aby jawnie je umieścić. Zyski odpowiedniej wspólnej lokalizacji są ważniejsze niż wszelkie ponowne równoważenie obciążenia.

Aby jawnie rozszukać wiele tabel, Rozpowszechnij je, a następnie umieść inne w swojej grupie wspólnej lokalizacji. Na przykład:

```postgresql
-- distribute stores
SELECT create_distributed_table('stores', 'store_id');

-- add to the same group as stores
SELECT create_distributed_table('orders', 'store_id', colocate_with => 'stores');
SELECT create_distributed_table('products', 'store_id', colocate_with => 'stores');
```

Informacje o grupach współlokalizacyjnych są przechowywane w tabeli [pg_dist_colocation](reference-hyperscale-metadata.md#colocation-group-table) , podczas gdy [pg_dist_partition](reference-hyperscale-metadata.md#partition-table) wykazuje, które tabele są przypisane do których grup.

## <a name="dropping-tables"></a>Usuwanie tabel

Aby usunąć tabele rozproszone, można użyć polecenia Porzuć tabelę Standard PostgreSQL. Podobnie jak w przypadku tabel zwykłych, instrukcja DROP TABLE usuwa wszystkie indeksy, reguły, wyzwalacze i ograniczenia istniejące dla tabeli docelowej. Ponadto porzuca również fragmentów w węzłach procesu roboczego i czyści swoje metadane.

```sql
DROP TABLE github_events;
```

## <a name="modifying-tables"></a>Modyfikowanie tabel

Funkcja przeskalowania (Citus) automatycznie propaguje wiele rodzajów instrukcji języka DDL.
Modyfikacja tabeli rozproszonej w węźle koordynatora spowoduje również aktualizację fragmentów na pracownikach. Inne instrukcje języka DDL wymagają rozproszenia ręcznego, a niektóre inne są zabronione, takie jak dowolne, które spowodują zmodyfikowanie kolumny dystrybucji.
Próba uruchomienia języka DDL, która nie kwalifikuje się do automatycznego propagacji, spowoduje wystąpienie błędu i pozostawienie tabel w węźle koordynatora bez zmian.

Poniżej znajduje się odwołanie do kategorii instrukcji języka DDL, które propagują.
Automatyczne Propagacja można włączyć lub wyłączyć za pomocą [parametru konfiguracji](reference-hyperscale-parameters.md#citusenable_ddl_propagation-boolean) .

### <a name="addingmodifying-columns"></a>Dodawanie/modyfikowanie kolumn

Funkcja Citus) propaguje większość poleceń [ALTER TABLE](https://www.postgresql.org/docs/current/static/ddl-alter.html) automatycznie. Dodawanie kolumn lub zmiana ich wartości domyślnych będzie działały tak, jak w przypadku jednej maszyny PostgreSQL Database:

```postgresql
-- Adding a column

ALTER TABLE products ADD COLUMN description text;

-- Changing default value

ALTER TABLE products ALTER COLUMN price SET DEFAULT 7.77;
```

Znacząca zmiana istniejącej kolumny, taka jak zmiana nazwy lub zmiana jej typu danych, jest zbyt duża. Nie można jednak zmienić typu danych [kolumny dystrybucji](concepts-hyperscale-nodes.md#distribution-column) .
Ta kolumna określa, w jaki sposób dane tabeli są dystrybuowane za pomocą klastra Citus, a modyfikacja jego typu danych wymaga przeniesienia danych.

Próba wykonania tej czynności spowoduje wystąpienie błędu:

```postgres
-- assumining store_id is the distribution column
-- for products, and that it has type integer

ALTER TABLE products
ALTER COLUMN store_id TYPE text;

/*
ERROR:  XX000: cannot execute ALTER TABLE command involving partition column
LOCATION:  ErrorIfUnsupportedAlterTableStmt, multi_utility.c:2150
*/
```

### <a name="addingremoving-constraints"></a>Dodawanie/usuwanie ograniczeń

Funkcja wieloskal (Citus) umożliwia dalsze korzystanie z bezpieczeństwa relacyjnej bazy danych, w tym ograniczeń bazy danych (zobacz [dokumenty](https://www.postgresql.org/docs/current/static/ddl-constraints.html)PostgreSQL).
Ze względu na charakter systemów rozproszonych, funkcja wieloskalowania (Citus) nie będzie ograniczać ograniczeń unikatowości między węzłami procesów roboczych.

Aby skonfigurować klucz obcy między znajdującymi się w nim tabelami rozproszonymi, zawsze Uwzględnij kolumnę dystrybucji w kluczu. Dołączenie kolumny dystrybucji może wiązać się z wprowadzeniem klucza złożonej.

Klucze obce można tworzyć w takich sytuacjach:

-   między dwiema lokalnymi (nierozproszonymi) tabelami
-   między dwiema tabelami referencyjnymi,
-   między dwiema [](concepts-hyperscale-colocation.md) tabelami rozproszonymi, gdy klucz zawiera kolumnę dystrybucji lub
-   jako tabelę rozproszoną odwołującą się do [tabeli referencyjnej](concepts-hyperscale-nodes.md#type-2-reference-tables)

Klucze obce z tabel referencyjnych w tabelach rozproszonych nie są obsługiwane.

> [!NOTE]
>
> Klucze podstawowe i ograniczenia unikatowości muszą zawierać kolumnę dystrybucji. Dodanie ich do kolumny bez dystrybucji spowoduje wygenerowanie błędu

Ten przykład pokazuje, jak utworzyć klucze podstawowe i obce w tabelach rozproszonych:

```postgresql
--
-- Adding a primary key
-- --------------------

-- We'll distribute these tables on the account_id. The ads and clicks
-- tables must use compound keys that include account_id.

ALTER TABLE accounts ADD PRIMARY KEY (id);
ALTER TABLE ads ADD PRIMARY KEY (account_id, id);
ALTER TABLE clicks ADD PRIMARY KEY (account_id, id);

-- Next distribute the tables

SELECT create_distributed_table('accounts', 'id');
SELECT create_distributed_table('ads',      'account_id');
SELECT create_distributed_table('clicks',   'account_id');

--
-- Adding foreign keys
-- -------------------

-- Note that this can happen before or after distribution, as long as
-- there exists a uniqueness constraint on the target column(s) which
-- can only be enforced before distribution.

ALTER TABLE ads ADD CONSTRAINT ads_account_fk
  FOREIGN KEY (account_id) REFERENCES accounts (id);
ALTER TABLE clicks ADD CONSTRAINT clicks_ad_fk
  FOREIGN KEY (account_id, ad_id) REFERENCES ads (account_id, id);
```

Podobnie Uwzględnij kolumnę dystrybucji w ograniczeniach unikatowości:

```postgresql
-- Suppose we want every ad to use a unique image. Notice we can
-- enforce it only per account when we distribute by account id.

ALTER TABLE ads ADD CONSTRAINT ads_unique_image
  UNIQUE (account_id, image_url);
```

Ograniczenia not null mogą być stosowane do dowolnej kolumny (dystrybucja lub nie), ponieważ nie wymagają żadnych odnośników między pracownikami.

```postgresql
ALTER TABLE ads ALTER COLUMN image_url SET NOT NULL;
```

### <a name="using-not-valid-constraints"></a>Używanie nieprawidłowych ograniczeń

W niektórych sytuacjach może być przydatne wymuszanie ograniczeń dla nowych wierszy, jednocześnie pozwalając na pozostawienie istniejących wierszy niezgodnych. Funkcja przeskalowania (Citus) obsługuje tę funkcję dla ograniczeń CHECK i kluczy obcych przy użyciu PostgreSQL \' s nieprawidłowe \" \" oznaczenie ograniczenia.

Rozważmy na przykład aplikację, która przechowuje profile użytkowników w [tabeli referencyjnej](concepts-hyperscale-nodes.md#type-2-reference-tables).

```postgres
-- we're using the "text" column type here, but a real application
-- might use "citext" which is available in a postgres contrib module

CREATE TABLE users ( email text PRIMARY KEY );
SELECT create_reference_table('users');
```

W miarę upływu czasu Załóżmy, że kilka adresów nie jest w tej tabeli.

```postgres
INSERT INTO users VALUES
   ('foo@example.com'), ('hacker12@aol.com'), ('lol');
```

Chcemy sprawdzić poprawność adresów, ale PostgreSQL nie zezwala na dodanie ograniczenia CHECK, które nie powiodło się dla istniejących wierszy. Jednak zezwala *na* ograniczenie oznaczone jako nieprawidłowe:

```postgres
ALTER TABLE users
ADD CONSTRAINT syntactic_email
CHECK (email ~
   '^[a-zA-Z0-9.!#$%&''*+/=?^_`{|}~-]+@[a-zA-Z0-9](?:[a-zA-Z0-9-]{0,61}[a-zA-Z0-9])?(?:\.[a-zA-Z0-9](?:[a-zA-Z0-9-]{0,61}[a-zA-Z0-9])?)*$'
) NOT VALID;
```

Nowe wiersze są teraz chronione.

```postgres
INSERT INTO users VALUES ('fake');

/*
ERROR:  new row for relation "users_102010" violates
        check constraint "syntactic_email_102010"
DETAIL:  Failing row contains (fake).
*/
```

Później w godzinach poza godzinami szczytu administrator bazy danych może podjąć próbę naprawienia nieprawidłowych wierszy i ponownie sprawdzić poprawność.

```postgres
-- later, attempt to validate all rows
ALTER TABLE users
VALIDATE CONSTRAINT syntactic_email;
```

Dokumentacja PostgreSQL zawiera więcej informacji o nieprawidłowym i ZWERYFIKOWANiu ograniczenia w sekcji [ALTER TABLE](https://www.postgresql.org/docs/current/sql-altertable.html) .

### <a name="addingremoving-indices"></a>Dodawanie/usuwanie indeksów

Funkcja Citus) obsługuje dodawanie i usuwanie [indeksów](https://www.postgresql.org/docs/current/static/sql-createindex.html):

```postgresql
-- Adding an index

CREATE INDEX clicked_at_idx ON clicks USING BRIN (clicked_at);

-- Removing an index

DROP INDEX clicked_at_idx;
```

Dodanie indeksu powoduje zablokowanie zapisu, który może być niepożądany w przypadku systemu wielodostępnego \" rekordu. \" Aby zminimalizować przestoje aplikacji, zamiast tego należy utworzyć indeks [jednocześnie](https://www.postgresql.org/docs/current/static/sql-createindex.html#SQL-CREATEINDEX-CONCURRENTLY) . Ta metoda wymaga większej liczby całkowitej pracy niż standardowa kompilacja indeksu i trwa dłużej. Jednak ponieważ umożliwia wykonywanie normalnych operacji podczas kompilowania indeksu, ta metoda jest przydatna do dodawania nowych indeksów w środowisku produkcyjnym.

```postgresql
-- Adding an index without locking table writes

CREATE INDEX CONCURRENTLY clicked_at_idx ON clicks USING BRIN (clicked_at);
```
