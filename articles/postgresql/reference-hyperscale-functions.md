---
title: Funkcje SQL — skalowanie (Citus) — Azure Database for PostgreSQL
description: Funkcje w interfejsie API SQL Citus
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: reference
ms.date: 08/10/2020
ms.openlocfilehash: f324ef44d002f50bf27c08072e904c1d92b5512f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "95026237"
---
# <a name="functions-in-the-hyperscale-citus-sql-api"></a>Funkcje w interfejsie API SQL Citus

Ta sekcja zawiera informacje referencyjne dotyczące funkcji zdefiniowanych przez użytkownika, które są udostępniane przez funkcję Citus. Funkcje te pomagają w dostarczaniu dodatkowych funkcji do skalowania (Citus) innych niż standardowe polecenia SQL.

> [!NOTE]
>
> Grupy serwerów ze skalowaniem (Citus) korzystające ze starszych wersji aparatu Citus mogą nie oferować wszystkich funkcji wymienionych poniżej.

## <a name="table-and-shard-ddl"></a>Tabela i fragmentu DDL

### <a name="create_distributed_table"></a>Tworzenie \_ rozproszonej \_ tabeli

Funkcja tworzenia \_ rozproszonej \_ tabeli () służy do definiowania rozproszonej tabeli i tworzenia jej fragmentów, jeśli jest to tabela dystrybuowana z użyciem skrótów. Ta funkcja przyjmuje nazwę tabeli, kolumnę dystrybucji i opcjonalną metodę dystrybucji i wstawia odpowiednie metadane, aby oznaczyć tabelę jako rozproszoną. Jeśli nie określono metody dystrybucji, funkcja domyślnie przyjmuje wartość "hash". Jeśli tabela jest dystrybuowana przez funkcję tworzenia skrótów, funkcja tworzy również fragmentów procesu roboczego na podstawie wartości konfiguracyjnych fragmentu Count i fragmentu. Jeśli tabela zawiera jakiekolwiek wiersze, są one automatycznie dystrybuowane do węzłów procesu roboczego.

Ta funkcja zastępuje użycie metody Master \_ create \_ Distributed \_ Table (), a następnie Master \_ create \_ Worker \_ fragmentów ().

#### <a name="arguments"></a>Argumenty

**\_ Nazwa tabeli:** nazwa tabeli, która musi być dystrybuowana.

**\_ kolumna dystrybucji:** kolumna, w której ma zostać dystrybuowana tabela.

**\_ Typ dystrybucji:** (opcjonalnie) Metoda, zgodnie z którą tabela ma być dystrybuowana. Dozwolone wartości to dołączenie lub wartość skrótu z wartością domyślną "hash".

**Znajdź \_ przy użyciu:** (opcjonalnie) Uwzględnij bieżącą tabelę w grupie wspólnej lokalizacji innej tabeli. Tabele domyślne są umieszczane w, gdy są dystrybuowane według kolumn tego samego typu, mają tę samą liczbę fragmentu i mają ten sam współczynnik replikacji. Możliwe wartości dla programu `colocate_with` to `default` , `none` Aby rozpocząć nową grupę wspólnej lokalizacji, lub nazwę innej tabeli, która ma być rozszukiwana z tą tabelą.  (Zobacz [kolokacja tabeli](concepts-hyperscale-colocation.md)).

Należy pamiętać, że wartość domyślna to `colocate_with` niejawna współlokalizacja. [Współlokalizacja](concepts-hyperscale-colocation.md) może być doskonałym rozwiązaniem w przypadku, gdy tabele są powiązane lub zostaną dołączone.  Jeśli jednak dwie tabele są niepowiązane, ale mają korzystać z tego samego typu danych dla ich kolumn dystrybucji, przypadkowe współlokalizowanie ich może obniżyć wydajność podczas ponownego [równoważenia fragmentu](howto-hyperscale-scale-rebalance.md).  Tabela fragmentów będzie niepotrzebnie przenoszona razem w \" kaskadowo.\"

Jeśli nowa tabela rozproszona nie jest powiązana z innymi tabelami, najlepiej ją określić `colocate_with => 'none'` .

#### <a name="return-value"></a>Wartość zwracana

Nie dotyczy

#### <a name="example"></a>Przykład

Ten przykład informuje bazę danych, że \_ tabela zdarzeń GitHub powinna być dystrybuowana przez skrót w \_ kolumnie Identyfikator repozytorium.

```postgresql
SELECT create_distributed_table('github_events', 'repo_id');

-- alternatively, to be more explicit:
SELECT create_distributed_table('github_events', 'repo_id',
                                colocate_with => 'github_repo');
```

### <a name="create_reference_table"></a>Tworzenie \_ tabeli referencyjnej \_

Funkcja Create \_ Reference \_ Table () służy do definiowania małych odwołań lub tabel wymiarów. Ta funkcja przyjmuje nazwę tabeli i tworzy rozproszoną tabelę z tylko jednym fragmentu, replikowanym do każdego węzła procesu roboczego.

#### <a name="arguments"></a>Argumenty

**\_ Nazwa tabeli:** nazwa małego wymiaru lub tabeli referencyjnej, która musi być dystrybuowana.

#### <a name="return-value"></a>Wartość zwracana

Nie dotyczy

#### <a name="example"></a>Przykład

Ten przykład informuje bazę danych, że tabela krajowa powinna być zdefiniowana jako tabela referencyjna

```postgresql
SELECT create_reference_table('nation');
```

### <a name="upgrade_to_reference_table"></a>Uaktualnij \_ do \_ tabeli referencyjnej \_

Funkcja uaktualniania \_ do \_ odwołania \_ tabeli () przyjmuje istniejącą tabelę rozproszoną, która ma liczbę fragmentu o jednej i uaktualnia ją do rozpoznanej tabeli referencyjnej. Po wywołaniu tej funkcji tabela będzie tak, jakby była utworzona za pomocą [create_reference_table](#create_reference_table).

#### <a name="arguments"></a>Argumenty

**\_ Nazwa tabeli:** nazwa tabeli rozproszonej (o liczbie fragmentu = 1), która zostanie dystrybuowana jako tabela referencyjna.

#### <a name="return-value"></a>Wartość zwracana

Nie dotyczy

#### <a name="example"></a>Przykład

Ten przykład informuje bazę danych, że tabela krajowa powinna być zdefiniowana jako tabela referencyjna

```postgresql
SELECT upgrade_to_reference_table('nation');
```

### <a name="mark_tables_colocated"></a>Oznacz \_ tabele \_ wspólnie

Funkcja Oznacz \_ tabele, która \_ współdzielona () przyjmuje tabelę rozproszoną (Źródło) i listę innych (obiektów docelowych), i umieszcza elementy docelowe w tej samej grupie wspólnej lokalizacji jako źródło. Jeśli źródło jeszcze nie należy do grupy, ta funkcja tworzy jedną i przypisuje źródłową i docelową.

Tabele, które należy wykonać, powinny być wykonywane w czasie dystrybucji tabeli za pośrednictwem `colocate_with` parametru [create_distributed_table](#create_distributed_table), ale `mark_tables_colocated` mogą zadbać o to później, w razie potrzeby.

#### <a name="arguments"></a>Argumenty

**\_ \_ Nazwa tabeli źródłowej:** nazwa tabeli rozproszonej, której Grupa współdzielona zostanie przypisana do dopasowania.

**\_ nazwy tabel docelowych \_ :** Tablica nazw rozproszonej tabeli docelowej nie może być pusta. Te tabele rozproszone muszą być zgodne z tabelą źródłową w programie:

> -   Metoda dystrybucji
> -   Typ kolumny dystrybucji
> -   Typ replikacji
> -   Liczba fragmentu

Jeśli żaden z powyższych opcji nie stosuj, funkcja Przeskaluj (Citus) zgłosi błąd. Na przykład próba przeszukiwania tabel `apples` i, `oranges` których typy kolumn dystrybucji różnią się w wyniku:

```
ERROR:  XX000: cannot colocate tables apples and oranges
DETAIL:  Distribution column types don't match for apples and oranges.
```

#### <a name="return-value"></a>Wartość zwracana

Nie dotyczy

#### <a name="example"></a>Przykład

Ten przykład umieszcza `products` i `line_items` znajduje się w tej samej grupie wspólnej lokalizacji co `stores` . W przykładzie przyjęto założenie, że te tabele są dystrybuowane w kolumnie o zgodnym typie, najprawdopodobniej z \" identyfikatorem magazynu.\"

```postgresql
SELECT mark_tables_colocated('stores', ARRAY['products', 'line_items']);
```

### <a name="create_distributed_function"></a>Tworzenie \_ funkcji rozproszonej \_

Propaguje funkcję z węzła koordynatora do pracowników i oznacza ją do wykonania rozproszonego. Po wywołaniu funkcji rozproszonej w ramach koordynatora funkcja przeskalowania (Citus) używa wartości \" argumentu dystrybucji, \" Aby wybrać węzeł procesu roboczego, który ma uruchamiać funkcję. Wykonanie funkcji na procesach roboczych zwiększa równoległości i może doprowadzić kod bliżej danych w fragmentów na potrzeby małych opóźnień.

Ścieżka wyszukiwania Postgres nie jest przekazywana przez koordynatora do pracowników w trakcie wykonywania funkcji rozproszonej, więc kod funkcji rozproszonej powinien w pełni kwalifikować nazwy obiektów bazy danych. Powiadomienia wysyłane przez funkcje nie będą wyświetlane użytkownikowi.

#### <a name="arguments"></a>Argumenty

**\_ nazwa funkcji:** nazwa funkcji do dystrybucji. Nazwa musi zawierać typy parametrów funkcji w nawiasach, ponieważ wiele funkcji może mieć taką samą nazwę w PostgreSQL. Na przykład `'foo(int)'` różni się od `'foo(int, text)'` .

**\_ Nazwa ARG \_ :** (opcjonalnie) nazwa argumentu do dystrybucji. Dla wygody (lub jeśli argumenty funkcji nie mają nazw), symbol zastępczy pozycyjny jest dozwolony, taki jak `'$1'` . Jeśli ten parametr nie jest określony, funkcja o nazwie przez `function_name` jest tworzona wyłącznie w ramach procesów roboczych. Jeśli węzły procesu roboczego są dodawane w przyszłości, funkcja zostanie automatycznie utworzona.

**Znajdź \_ przy użyciu:** (opcjonalnie) gdy funkcja dystrybuowana odczytuje lub zapisuje dane w rozproszonej tabeli (lub, bardziej ogólnie, w grupie wspólnej lokalizacji), pamiętaj o nazwie tej tabeli przy użyciu `colocate_with` parametru. Następnie każde wywołanie funkcji zostanie uruchomione w węźle procesu roboczego zawierającym odpowiednie fragmentów.

#### <a name="return-value"></a>Wartość zwracana

Nie dotyczy

#### <a name="example"></a>Przykład

```postgresql
-- an example function which updates a hypothetical
-- event_responses table which itself is distributed by event_id
CREATE OR REPLACE FUNCTION
  register_for_event(p_event_id int, p_user_id int)
RETURNS void LANGUAGE plpgsql AS $fn$
BEGIN
  INSERT INTO event_responses VALUES ($1, $2, 'yes')
  ON CONFLICT (event_id, user_id)
  DO UPDATE SET response = EXCLUDED.response;
END;
$fn$;

-- distribute the function to workers, using the p_event_id argument
-- to determine which shard each invocation affects, and explicitly
-- colocating with event_responses which the function updates
SELECT create_distributed_function(
  'register_for_event(int, int)', 'p_event_id',
  colocate_with := 'event_responses'
);
```

## <a name="metadata--configuration-information"></a>Informacje o metadanych/konfiguracji

### <a name="master_get_table_metadata"></a>Główne \_ pobieranie \_ \_ metadanych tabeli

Główna \_ Funkcja pobierania \_ \_ metadanych tabeli () może służyć do zwracania metadanych związanych z dystrybucją dla rozproszonej tabeli. Metadane obejmują identyfikator relacji, typ magazynu, metodę dystrybucji, kolumnę dystrybucji, liczbę replikacji, maksymalny rozmiar fragmentu oraz zasady umieszczania fragmentu dla tabeli. W tle ta funkcja wysyła zapytania do tabel metadanych Citus), aby uzyskać wymagane informacje i łączy je z krotką przed zwróceniem do użytkownika.

#### <a name="arguments"></a>Argumenty

**\_ Nazwa tabeli:** nazwa tabeli rozproszonej, dla której mają zostać pobrane metadane.

#### <a name="return-value"></a>Wartość zwracana

Spójna kolekcja zawierająca następujące informacje:

**logiczne \_ relid:** identyfikator OID tabeli rozproszonej. Odwołuje się do kolumny relfilenode w \_ tabeli wykazu systemu klasy PG.

**\_ Typ magazynu części \_ :** typ magazynu używany dla tabeli. Może to być "t" (tabela standardowa), "f" (Tabela obca) lub "c" (tabela kolumnowy).

**część \_ — Metoda:** metoda dystrybucji użyta dla tabeli. Może to być "a" (append) lub "h" (hash).

**\_ klucz części:** kolumna dystrybucji dla tabeli.

**\_ Liczba replik części \_ :** bieżąca liczba replikacji fragmentu.

**\_ Maksymalny \_ rozmiar części:** bieżący maksymalny rozmiar fragmentu w bajtach.

**\_ zasady umieszczania części \_ :** fragmentu zasady umieszczania używane do umieszczania fragmentów tabeli. Może być 1 (lokalny-węzeł-pierwszy) lub 2 (działające w trybie okrężnym).

#### <a name="example"></a>Przykład

Poniższy przykład pobiera i wyświetla metadane tabeli dla \_ tabeli zdarzeń serwisu GitHub.

```postgresql
SELECT * from master_get_table_metadata('github_events');
 logical_relid | part_storage_type | part_method | part_key | part_replica_count | part_max_size | part_placement_policy 
---------------+-------------------+-------------+----------+--------------------+---------------+-----------------------
         24180 | t                 | h           | repo_id  |                  2 |    1073741824 |                     2
(1 row)
```

### <a name="get_shard_id_for_distribution_column"></a>Pobierz \_ \_ identyfikator fragmentu \_ dla \_ \_ kolumny dystrybucji

Funkcja Citus) przypisuje każdy wiersz tabeli rozproszonej do fragmentu na podstawie wartości kolumny dystrybucji wiersza i metody dystrybucji tabeli. W większości przypadków precyzyjne mapowanie to szczegóły niskiego poziomu, które administrator bazy danych może zignorować. Jednak może być przydatne, aby określić fragmentu wiersza w przypadku ręcznych zadań konserwacji bazy danych lub po prostu zaspokojenie Curiosity. `get_shard_id_for_distribution_column`Ta funkcja udostępnia te informacje dla tabel rozproszonych i mieszanych, a także tabel referencyjnych. Nie działa w przypadku dystrybucji dołączania.

#### <a name="arguments"></a>Argumenty

**\_ Nazwa tabeli:** rozproszonej tabeli.

**\_ wartość dystrybucji:** wartość kolumny dystrybucji.

#### <a name="return-value"></a>Wartość zwracana

Fragmentu ID (Citus) kojarzy z wartością kolumny dystrybucji dla danej tabeli.

#### <a name="example"></a>Przykład

```postgresql
SELECT get_shard_id_for_distribution_column('my_table', 4);

 get_shard_id_for_distribution_column
--------------------------------------
                               540007
(1 row)
```

### <a name="column_to_column_name"></a>\_Nazwa kolumny do kolumny \_ \_

Tłumaczy `partkey` kolumnę `pg_dist_partition` na nazwę kolumny tekstowej. Tłumaczenie jest przydatne do określenia kolumny dystrybucji tabeli rozproszonej.

Aby zapoznać się z bardziej szczegółowym omówieniem, zobacz [Wybieranie kolumny dystrybucji](concepts-hyperscale-choose-distribution-column.md).

#### <a name="arguments"></a>Argumenty

**\_ Nazwa tabeli:** rozproszonej tabeli.

**\_ tekst var kolumny \_ :** wartość `partkey` w `pg_dist_partition` tabeli.

#### <a name="return-value"></a>Wartość zwracana

Nazwa `table_name` kolumny dystrybucji.

#### <a name="example"></a>Przykład

```postgresql
-- get distribution column name for products table

SELECT column_to_column_name(logicalrelid, partkey) AS dist_col_name
  FROM pg_dist_partition
 WHERE logicalrelid='products'::regclass;
```

Dane wyjściowe:

```
┌───────────────┐
│ dist_col_name │
├───────────────┤
│ company_id    │
└───────────────┘
```

### <a name="citus_relation_size"></a>\_rozmiar relacji \_ Citus

Pobierz miejsce na dysku używane przez wszystkie fragmentów tabeli rozproszonej.
Miejsce na dysku zawiera rozmiar \" głównego rozwidlenia, \" ale wyklucza mapę widoczności i mapę wolnego miejsca dla fragmentów.

#### <a name="arguments"></a>Argumenty

**logicalrelid:** nazwa tabeli rozproszonej.

#### <a name="return-value"></a>Wartość zwracana

Rozmiar w bajtach jako bigint.

#### <a name="example"></a>Przykład

```postgresql
SELECT pg_size_pretty(citus_relation_size('github_events'));
```

```
pg_size_pretty
--------------
23 MB
```

### <a name="citus_table_size"></a>\_rozmiar tabeli \_ Citus

Pobierz miejsce na dysku używane przez wszystkie fragmentów tabeli rozproszonej, z wyłączeniem indeksów (z uwzględnieniem wyskakujących map wolnego miejsca i mapy widoczności).

#### <a name="arguments"></a>Argumenty

**logicalrelid:** nazwa tabeli rozproszonej.

#### <a name="return-value"></a>Wartość zwracana

Rozmiar w bajtach jako bigint.

#### <a name="example"></a>Przykład

```postgresql
SELECT pg_size_pretty(citus_table_size('github_events'));
```

```
pg_size_pretty
--------------
37 MB
```

### <a name="citus_total_relation_size"></a>\_łączny \_ rozmiar relacji Citus \_

Pobierz całkowite miejsce na dysku używane przez wszystkie fragmentów w określonej tabeli rozproszonej, w tym wszystkie indeksy i wyskakujące dane.

#### <a name="arguments"></a>Argumenty

**logicalrelid:** nazwa tabeli rozproszonej.

#### <a name="return-value"></a>Wartość zwracana

Rozmiar w bajtach jako bigint.

#### <a name="example"></a>Przykład

```postgresql
SELECT pg_size_pretty(citus_total_relation_size('github_events'));
```

```
pg_size_pretty
--------------
73 MB
```

### <a name="citus_stat_statements_reset"></a>\_ \_ \_ Resetowanie instrukcji Citus

Usuwa wszystkie wiersze z [citus_stat_statements](reference-hyperscale-metadata.md#query-statistics-table).
Ta funkcja działa niezależnie od programu `pg_stat_statements_reset()` . Aby zresetować wszystkie statystyki, wywołaj obie funkcje.

#### <a name="arguments"></a>Argumenty

Nie dotyczy

#### <a name="return-value"></a>Wartość zwracana

Brak

## <a name="server-group-management-and-repair"></a>Zarządzanie grupą serwerów i ich naprawa

### <a name="master_copy_shard_placement"></a>\_fragmentu — \_ \_ umieszczenie kopii wzorca

Jeśli nie można zaktualizować umieszczania fragmentu podczas wykonywania polecenia modyfikacji lub operacji DDL, zostanie ona oznaczona jako nieaktywna. \_ \_ \_ Funkcja umieszczania fragmentu kopii wzorca może następnie zostać wywołana w celu naprawy nieaktywnego rozmieszczenia fragmentu przy użyciu danych z zdrowego umieszczania.

Aby naprawić fragmentu, funkcja najpierw odrzuca rozmieszczenie fragmentu w złej kondycji i tworzy je ponownie przy użyciu schematu w ramach koordynatora. Po utworzeniu położenia fragmentu funkcja kopiuje dane z położenia w dobrej kondycji i aktualizuje metadane, aby oznaczyć nowe położenie fragmentu jako zdrowe. Ta funkcja zapewnia, że fragmentu będzie chroniona przed jednoczesnymi modyfikacjami podczas naprawy.

#### <a name="arguments"></a>Argumenty

**\_ identyfikator fragmentu:** identyfikator fragmentu, który ma zostać naprawiony.

**\_ \_ nazwa węzła źródłowego:** nazwa DNS węzła, na którym znajduje się fragmentu w dobrej kondycji ( \" węzeł źródłowy \" ).

**\_ port węzła źródłowego \_ :** port w źródłowym węźle procesu roboczego, na którym nasłuchuje serwer bazy danych.

**\_ \_ nazwa węzła docelowego:** nazwa DNS węzła, na którym znajduje się nieprawidłowe rozmieszczenie fragmentu ( \" węzeł docelowy \" ).

**\_ port węzła docelowego \_ :** port w docelowym węźle procesu roboczego, na którym nasłuchuje serwer bazy danych.

#### <a name="return-value"></a>Wartość zwracana

Nie dotyczy

#### <a name="example"></a>Przykład

W poniższym przykładzie nastąpi Naprawa nieaktywnego rozmieszczenia fragmentu fragmentu 12345, który znajduje się na serwerze bazy danych działającym na "nieprawidłowym \_ hoście" na porcie 5432. Aby go naprawić, użyje danych z zdrowego rozmieszczenia fragmentu na serwerze działającym na "dobrym \_ hoście" na porcie
5432.

```postgresql
SELECT master_copy_shard_placement(12345, 'good_host', 5432, 'bad_host', 5432);
```

### <a name="master_move_shard_placement"></a>\_fragmentu — \_ \_ położenie wzorca przenoszenia

Ta funkcja przenosi daną fragmentu (i znajdującą się w niej fragmentów) z jednego węzła do drugiego. Zwykle jest on używany pośrednio podczas ponownego równoważenia fragmentu, a nie jest wywoływany bezpośrednio przez administratora bazy danych.

Istnieją dwa sposoby przenoszenia danych: Blokowanie lub odblokowywanie. Metoda blokowania oznacza, że podczas przenoszenia wszystkich modyfikacji do fragmentu są wstrzymane.
Drugi sposób, który pozwala uniknąć blokowania fragmentu zapisu, opiera się na replikacji logicznej Postgres 10.

Po pomyślnym wykonaniu operacji przenoszenia fragmentów w węźle źródłowym zostaną usunięte. Jeśli przeniesienie nie powiedzie się w dowolnym momencie, ta funkcja zgłasza błąd i pozostawia węzły źródłowe i docelowe bez zmian.

#### <a name="arguments"></a>Argumenty

**\_ identyfikator fragmentu:** identyfikator fragmentu, który ma zostać przeniesiony.

**\_ \_ nazwa węzła źródłowego:** nazwa DNS węzła, na którym znajduje się fragmentu w dobrej kondycji ( \" węzeł źródłowy \" ).

**\_ port węzła źródłowego \_ :** port w źródłowym węźle procesu roboczego, na którym nasłuchuje serwer bazy danych.

**\_ \_ nazwa węzła docelowego:** nazwa DNS węzła, na którym znajduje się nieprawidłowe rozmieszczenie fragmentu ( \" węzeł docelowy \" ).

**\_ port węzła docelowego \_ :** port w docelowym węźle procesu roboczego, na którym nasłuchuje serwer bazy danych.

**\_ Tryb transferu fragmentu \_ :** (opcjonalnie) Określ metodę replikacji, czy ma być używana replikacja logiczna PostgreSQL lub polecenie kopiowania między procesami roboczymi. Możliwe wartości są następujące:

> -   `auto`: Wymagaj tożsamości repliki, jeśli istnieje replikacja logiczna, w przeciwnym razie użyj starszego zachowania (np. do naprawy fragmentu, PostgreSQL 9,6). Jest to wartość domyślna.
> -   `force_logical`: Użyj replikacji logicznej, nawet jeśli tabela nie ma tożsamości repliki. Wszystkie współbieżne instrukcje Update/Delete do tabeli będą kończyć się niepowodzeniem podczas replikacji.
> -   `block_writes`: Użyj kopiowania (blokowanie zapisu) dla tabel, które nie mają klucza podstawowego lub tożsamości repliki.

#### <a name="return-value"></a>Wartość zwracana

Nie dotyczy

#### <a name="example"></a>Przykład

```postgresql
SELECT master_move_shard_placement(12345, 'from_host', 5432, 'to_host', 5432);
```

### <a name="rebalance_table_shards"></a>ponowne równoważenie \_ tabeli \_ fragmentów

\_Funkcja Zrównoważ ponownie tabelę \_ fragmentów () przenosi fragmentów danej tabeli, aby równomiernie rozłożyć je między pracowników. Funkcja najpierw oblicza listę ruchów, które muszą zostać wprowadzone w celu zapewnienia, że Grupa serwerów jest zrównoważona w ramach danego progu. Następnie przenosi fragmentu umieszcza jeden według jednego z węzłów źródłowych do węzła docelowego i aktualizuje odpowiednie metadane fragmentu w celu odzwierciedlenia przeniesienia.

Każdy fragmentu ma przypisany koszt podczas ustalania, czy fragmentów są \" równomiernie dystrybuowane. \" Domyślnie każdy fragmentu ma ten sam koszt (wartość 1), więc dystrybucja w celu wyrównywania kosztów między pracownikami jest taka sama jak wyrównanie liczby fragmentów na każdym z nich. Strategia stałego kosztu jest wywoływana \" przez \_ fragmentu \_ Count \" i jest domyślną strategią ponownego równoważenia.

Domyślna strategia jest odpowiednia w następujących okolicznościach:

*  Fragmentów są w przybliżeniu tego samego rozmiaru
*  Fragmentów uzyskuje około tego samego natężenia ruchu
*  Węzły procesu roboczego mają ten sam rozmiar/typ
*  Fragmentów nie został przypięty do określonych procesów roboczych

Jeśli którekolwiek z tych założeń nie zostało wstrzymane, domyślne ponowne zrównoważenie może spowodować powstanie nieprawidłowego planu. W takim przypadku można dostosować strategię przy użyciu `rebalance_strategy` parametru.

Zaleca się Wywołaj [get_rebalance_table_shards_plan](#get_rebalance_table_shards_plan) przed uruchomieniem ponownego zrównoważenia \_ tabeli \_ fragmentów, aby zobaczyć i zweryfikować akcje do wykonania.

#### <a name="arguments"></a>Argumenty

**\_ Nazwa tabeli:** (opcjonalnie) nazwa tabeli, której fragmentów należy ponownie zrównoważyć. Jeśli wartość jest równa NULL, należy ponownie zrównoważyć wszystkie istniejące grupy wspólnej lokalizacji.

**próg:** (opcjonalnie) liczba zmiennoprzecinkowa z zakresu od 0,0 do 1,0, która wskazuje maksymalną różnicę współczynnika wykorzystania węzła z średniego użycia. Na przykład określenie 0,1 spowoduje, że moduł równoważenia fragmentu podejmie próbę zrównoważenia wszystkich węzłów o tej samej liczbie fragmentów ± 10%.
W każdym przypadku moduł równoważenia fragmentu podejmie próbę uzgodnienia wykorzystania wszystkich węzłów procesu roboczego z średnim wykorzystaniem (1-próg \* ) \_ \. . (1
+ próg) \* średniego \_ zakresu wykorzystania.

**Maksymalna \_ fragmentu \_ przenoszenia:** (opcjonalnie) Maksymalna liczba fragmentów do przeniesienia.

**wykluczona \_ \_ Lista fragmentu:** (opcjonalnie) identyfikatory fragmentów, które nie powinny zostać przeniesione podczas operacji ponownego równoważenia.

**\_ Tryb transferu fragmentu \_ :** (opcjonalnie) Określ metodę replikacji, czy ma być używana replikacja logiczna PostgreSQL lub polecenie kopiowania między procesami roboczymi. Możliwe wartości są następujące:

> -   `auto`: Wymagaj tożsamości repliki, jeśli istnieje replikacja logiczna, w przeciwnym razie użyj starszego zachowania (np. do naprawy fragmentu, PostgreSQL 9,6). Jest to wartość domyślna.
> -   `force_logical`: Użyj replikacji logicznej, nawet jeśli tabela nie ma tożsamości repliki. Wszystkie współbieżne instrukcje Update/Delete do tabeli będą kończyć się niepowodzeniem podczas replikacji.
> -   `block_writes`: Użyj kopiowania (blokowanie zapisu) dla tabel, które nie mają klucza podstawowego lub tożsamości repliki.

**tylko opróżnianie \_ :** (opcjonalnie) w przypadku wartości true Przenieś fragmentów poza węzły procesu roboczego, `shouldhaveshards` dla których ustawiono wartość false w [pg_dist_node](reference-hyperscale-metadata.md#worker-node-table); Przenieś nie inne fragmentów.

**strategia ponownego zrównoważenia \_ :** (opcjonalnie) nazwa strategii w [pg_dist_rebalance_strategy](reference-hyperscale-metadata.md#rebalancer-strategy-table).
Jeśli ten argument zostanie pominięty, funkcja wybierze strategię domyślną, jak wskazano w tabeli.

#### <a name="return-value"></a>Wartość zwracana

Nie dotyczy

#### <a name="example"></a>Przykład

W poniższym przykładzie zostanie podjęta próba ponownego zrównoważenia fragmentów \_ tabeli zdarzeń serwisu GitHub w ramach progu domyślnego.

```postgresql
SELECT rebalance_table_shards('github_events');
```

W tym przykładowym wykorzystaniu zostanie podjęta próba ponownego zrównoważenia \_ tabeli zdarzeń serwisu GitHub bez przechodzenia fragmentów z identyfikatorem 1 i 2.

```postgresql
SELECT rebalance_table_shards('github_events', excluded_shard_list:='{1,2}');
```

### <a name="get_rebalance_table_shards_plan"></a>Pobierz \_ \_ \_ Plan fragmentów tabeli ponownego zrównoważenia \_

Wyprowadzaj planowane ruchy fragmentu [rebalance_table_shards](#rebalance_table_shards) bez ich wykonywania.
Chociaż jest to mało prawdopodobne, pobieranie z \_ \_ \_ planu fragmentów tabeli \_ może wynikać nieco inaczej, niż to, w jaki sposób \_ fragmentów wywołanie tabeli ponownego zrównoważenia \_ z tymi samymi argumentami. Nie są one wykonywane w tym samym czasie, więc fakty dotyczące grupy serwerów \- — na przykład miejsce na dysku \- — mogą się różnić w zależności od wywołań.

#### <a name="arguments"></a>Argumenty

Te same argumenty jak ponowne równoważenie \_ tabeli \_ fragmentów: relacja, próg, maksymalna \_ fragmentu \_ przenoszone, wykluczone \_ \_ listy fragmentu i tylko opróżniania \_ . Zapoznaj się z dokumentacją tej funkcji dla argumentów znaczenie.

#### <a name="return-value"></a>Wartość zwracana

Krotki zawierające następujące kolumny:

-   **\_ Nazwa tabeli**: tabela, której fragmentów zostałaby przeniesiona
-   **shardid**: fragmentu
-   **\_ rozmiar fragmentu**: rozmiar w bajtach
-   **SourceName**: Nazwa hosta węzła źródłowego
-   **sourceport**: Port węzła źródłowego
-   **TargetName**: Nazwa hosta węzła docelowego
-   **targetport**: Port węzła docelowego

### <a name="get_rebalance_progress"></a>Pobieranie \_ postępu ponownego równoważenia \_

Po rozpoczęciu ponownej równoważenia fragmentu `get_rebalance_progress()` funkcja wyświetla postęp każdego fragmentuu. Monitoruje przeniesienie planowane i wykonane przez `rebalance_table_shards()` .

#### <a name="arguments"></a>Argumenty

Nie dotyczy

#### <a name="return-value"></a>Wartość zwracana

Krotki zawierające następujące kolumny:

-   Identyfikator **sesji**: Postgres PID monitora równoważenia obciążenia
-   **\_ Nazwa tabeli**: tabela, której przenoszona jest fragmentów
-   **shardid**: fragmentu
-   **\_ rozmiar fragmentu**: rozmiar w bajtach
-   **SourceName**: Nazwa hosta węzła źródłowego
-   **sourceport**: Port węzła źródłowego
-   **TargetName**: Nazwa hosta węzła docelowego
-   **targetport**: Port węzła docelowego
-   **postęp**: 0 = oczekiwanie na przeniesienie; 1 = przeniesienie; 2 = kompletne

#### <a name="example"></a>Przykład

```sql
SELECT * FROM get_rebalance_progress();
```

```
┌───────────┬────────────┬─────────┬────────────┬───────────────┬────────────┬───────────────┬────────────┬──────────┐
│ sessionid │ table_name │ shardid │ shard_size │  sourcename   │ sourceport │  targetname   │ targetport │ progress │
├───────────┼────────────┼─────────┼────────────┼───────────────┼────────────┼───────────────┼────────────┼──────────┤
│      7083 │ foo        │  102008 │    1204224 │ n1.foobar.com │       5432 │ n4.foobar.com │       5432 │        0 │
│      7083 │ foo        │  102009 │    1802240 │ n1.foobar.com │       5432 │ n4.foobar.com │       5432 │        0 │
│      7083 │ foo        │  102018 │     614400 │ n2.foobar.com │       5432 │ n4.foobar.com │       5432 │        1 │
│      7083 │ foo        │  102019 │       8192 │ n3.foobar.com │       5432 │ n4.foobar.com │       5432 │        2 │
└───────────┴────────────┴─────────┴────────────┴───────────────┴────────────┴───────────────┴────────────┴──────────┘
```

### <a name="citus_add_rebalance_strategy"></a>Citus \_ Dodawanie \_ strategii ponownego równoważenia \_

Dołącz wiersz do [pg_dist_rebalance_strategy](reference-hyperscale-metadata.md?#rebalancer-strategy-table) .

#### <a name="arguments"></a>Argumenty

Aby uzyskać więcej informacji na temat tych argumentów, zobacz odpowiednie wartości kolumn w temacie `pg_dist_rebalance_strategy` .

**Nazwa:** identyfikator nowej strategii

**\_ Funkcja kosztu fragmentu \_ :** identyfikuje funkcję służącą do określenia \" kosztu \" każdego fragmentu

**\_ Funkcja pojemności węzła \_ :** określa funkcję do mierzenia pojemności węzła

**fragmentu \_ dozwolony \_ w \_ \_ funkcji Node:** identyfikuje funkcję, która określa, które fragmentów mogą być umieszczone w węzłach

**domyślny \_ próg:** wartość progowa liczby zmiennoprzecinkowej, która dostosowuje dokładnie ten skumulowany koszt fragmentu między węzłami

**\_ próg minimalny:** (opcjonalnie) kolumna zabezpieczeń, która posiada minimalną wartość dozwoloną dla argumentu progowego modułu równoważenia obciążenia \_ tabeli \_ fragmentów (). Wartość domyślna to 0

#### <a name="return-value"></a>Wartość zwracana

Nie dotyczy

### <a name="citus_set_default_rebalance_strategy"></a>Citus \_ Ustaw \_ domyślną \_ strategię ponownego bilansu \_

Zaktualizuj tabelę [pg_dist_rebalance_strategy](reference-hyperscale-metadata.md#rebalancer-strategy-table) , zmieniając strategię nazwaną przez jej argument na wartość domyślną wybraną podczas ponownego równoważenia fragmentów.

#### <a name="arguments"></a>Argumenty

**Nazwa:** nazwa strategii w \_ \_ strategii ponownego zrównoważenia \_

#### <a name="return-value"></a>Wartość zwracana

Nie dotyczy

#### <a name="example"></a>Przykład

```postgresql
SELECT citus_set_default_rebalance_strategy('by_disk_size');
```

### <a name="citus_remote_connection_stats"></a>\_ \_ Statystyka połączenia zdalnego Citus \_

\_ \_ \_ Funkcja Statystyka połączenia zdalnego Citus () pokazuje liczbę aktywnych połączeń z każdym węzłem zdalnym.

#### <a name="arguments"></a>Argumenty

Nie dotyczy

#### <a name="example"></a>Przykład

```postgresql
SELECT * from citus_remote_connection_stats();
```

```
    hostname    | port | database_name | connection_count_to_node
----------------+------+---------------+--------------------------
 citus_worker_1 | 5432 | postgres      |                        3
(1 row)
```

### <a name="master_drain_node"></a>główny \_ węzeł opróżniania \_

Funkcja głównego \_ węzła opróżniania \_ () przenosi fragmentów poza wyznaczony węzeł i do innych węzłów, `shouldhaveshards` dla których ustawiono wartość true w [pg_dist_node](reference-hyperscale-metadata.md#worker-node-table). Wywołaj funkcję przed usunięciem węzła z grupy serwerów i wyłączając serwer fizyczny węzła.

#### <a name="arguments"></a>Argumenty

**węzełname:** Nazwa hosta węzła do opróżniania.

**NodePort:** Numer portu węzła, który ma zostać opróżniony.

**\_ Tryb transferu fragmentu \_ :** (opcjonalnie) Określ metodę replikacji, czy ma być używana replikacja logiczna PostgreSQL lub polecenie kopiowania między procesami roboczymi. Możliwe wartości są następujące:

> -   `auto`: Wymagaj tożsamości repliki, jeśli istnieje replikacja logiczna, w przeciwnym razie użyj starszego zachowania (np. do naprawy fragmentu, PostgreSQL 9,6). Jest to wartość domyślna.
> -   `force_logical`: Użyj replikacji logicznej, nawet jeśli tabela nie ma tożsamości repliki. Wszystkie współbieżne instrukcje Update/Delete do tabeli będą kończyć się niepowodzeniem podczas replikacji.
> -   `block_writes`: Użyj kopiowania (blokowanie zapisu) dla tabel, które nie mają klucza podstawowego lub tożsamości repliki.

**strategia ponownego zrównoważenia \_ :** (opcjonalnie) nazwa strategii w [pg_dist_rebalance_strategy](reference-hyperscale-metadata.md#rebalancer-strategy-table).
Jeśli ten argument zostanie pominięty, funkcja wybierze strategię domyślną, jak wskazano w tabeli.

#### <a name="return-value"></a>Wartość zwracana

Nie dotyczy

#### <a name="example"></a>Przykład

Poniżej przedstawiono typowe kroki usuwania pojedynczego węzła (na przykład "10.0.0.1" na standardowym porcie PostgreSQL):

1.  Opróżnianie węzła.

    ```postgresql
    SELECT * from master_drain_node('10.0.0.1', 5432);
    ```

2.  Poczekaj na zakończenie polecenia

3.  Usuń węzeł

Podczas opróżniania wielu węzłów zaleca się używanie [rebalance_table_shards](#rebalance_table_shards) zamiast tego. Dzięki temu funkcja Citus (fragmentów) może zaplanować Planowanie z wyprzedzeniem i przesunąć się o niedozwoloną liczbę razy.

1.  Uruchom dla każdego węzła, który chcesz usunąć:

    ```postgresql
    SELECT * FROM master_set_node_property(node_hostname, node_port, 'shouldhaveshards', false);
    ```

2.  Opróżniaj je wszystkie jednocześnie przy użyciu [rebalance_table_shards](#rebalance_table_shards)

    ```postgresql
    SELECT * FROM rebalance_table_shards(drain_only := true);
    ```

3.  Poczekaj na zakończenie ponownego równoważenia opróżniania

4.  Usuń węzły

### <a name="replicate_table_shards"></a>Replikowanie \_ tabeli \_ fragmentów

Funkcja replikowania \_ tabeli \_ fragmentów () replikuje fragmentów w podanej tabeli. Funkcja najpierw oblicza listę replikowanych fragmentów i lokalizacji, z których mogą być pobierane do replikacji. Funkcja następnie kopiuje te fragmentów i aktualizuje odpowiednie metadane fragmentu, aby odzwierciedlały kopię.

#### <a name="arguments"></a>Argumenty

**\_ Nazwa tabeli:** nazwa tabeli, której fragmentów musi być replikowana.

**fragmentu \_ \_ :** (opcjonalnie) żądany czynnik replikacji do osiągnięcia dla każdego fragmentuu.

**Maksymalna liczba \_ fragmentuch \_ kopii:** (opcjonalnie) maksymalną liczbę fragmentów do skopiowania, aby osiągnąć żądany czynnik replikacji.

**wykluczona \_ \_ Lista fragmentu:** (opcjonalnie) identyfikatory fragmentów, które nie powinny zostać skopiowane podczas operacji replikacji.

#### <a name="return-value"></a>Wartość zwracana

Nie dotyczy

#### <a name="examples"></a>Przykłady

Poniższy przykład podejmie próbę replikowania fragmentów \_ tabeli zdarzeń serwisu GitHub do \_ \_ współczynnika replikacji fragmentu.

```postgresql
SELECT replicate_table_shards('github_events');
```

W tym przykładzie zostanie podjęta próba przeprowadzenia fragmentów \_ tabeli zdarzeń serwisu GitHub do żądanego czynnika replikacji z maksymalnie 10 kopiami fragmentu. Moduł przestawer skopiuje maksymalnie 10 fragmentów w trakcie próby osiągnięcia żądanego współczynnika replikacji.

```postgresql
SELECT replicate_table_shards('github_events', max_shard_copies:=10);
```

### <a name="isolate_tenant_to_new_shard"></a>Izolowanie \_ dzierżawy \_ do \_ nowego \_ fragmentu

Ta funkcja tworzy nowy fragmentu do przechowywania wierszy z określoną pojedynczą wartością w kolumnie dystrybucja. Jest to szczególnie przydatne w przypadku przypadków użycia wielodostępnego w ramach wielodostępności (Citus), w którym duża dzierżawa może być umieszczona samodzielnie na własnym fragmentu i ostatecznie w jej własnym węźle fizycznym.

#### <a name="arguments"></a>Argumenty

**\_ Nazwa tabeli:** nazwa tabeli, aby uzyskać nowy fragmentu.

**Identyfikator dzierżawy \_ :** wartość kolumny dystrybucji, która zostanie przypisana do nowego fragmentu.

**opcja kaskadowa \_ :** (opcjonalnie), gdy jest ustawiona na \" KASKADowo \" izoluje także fragmentu ze wszystkich tabel w [grupie wspólnej lokalizacji](concepts-hyperscale-colocation.md)bieżącej tabeli.

#### <a name="return-value"></a>Wartość zwracana

**\_ identyfikator fragmentu:** funkcja zwraca unikatowy identyfikator przypisany do nowo utworzonego fragmentu.

#### <a name="examples"></a>Przykłady

Utwórz nowy fragmentu do przechowywania LineItems dla dzierżawcy 135:

```postgresql
SELECT isolate_tenant_to_new_shard('lineitem', 135);
```

```
┌─────────────────────────────┐
│ isolate_tenant_to_new_shard │
├─────────────────────────────┤
│                      102240 │
└─────────────────────────────┘
```

## <a name="next-steps"></a>Następne kroki

* Wiele funkcji w tym artykule modyfikowanie systemowych [tabel metadanych](reference-hyperscale-metadata.md)
* [Parametry serwera](reference-hyperscale-parameters.md) dostosowują zachowanie niektórych funkcji
