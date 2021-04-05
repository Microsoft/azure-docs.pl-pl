---
title: Parametry serwera — funkcja do skalowania (Citus) — Azure Database for PostgreSQL
description: Parametry w interfejsie API SQL Citus
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: reference
ms.date: 08/10/2020
ms.openlocfilehash: 07f966c7b0be542f848f1a0a4eaf2b5549735b4b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91336245"
---
# <a name="server-parameters"></a>Parametry serwera

Istnieją różne parametry serwera, które mają wpływ na zachowanie funkcji Citus (skala), zarówno ze standardowego PostgreSQL, jak i dla skalowania (Citus).
Te parametry można ustawić w Azure Portal dla grupy serwerów Citus. W kategorii **Ustawienia** wybierz kolejno pozycje **Parametry węzła procesu roboczego** lub **węzeł koordynatora**. Te strony umożliwiają ustawienie parametrów dla wszystkich węzłów procesu roboczego lub tylko dla węzła koordynatora.

## <a name="hyperscale-citus-parameters"></a>Parametry przeskalowania (Citus)

> [!NOTE]
>
> Grupy serwerów ze skalowaniem (Citus) korzystające ze starszych wersji aparatu Citus mogą nie oferować wszystkich parametrów wymienionych poniżej.

### <a name="general-configuration"></a>Konfiguracja ogólna

#### <a name="citususe_secondary_nodes-enum"></a>Citus. Użyj \_ węzłów pomocniczych \_ (enum)

Ustawia zasady, które mają być używane podczas wybierania węzłów dla wybranych zapytań. Jeśli jest ustawiona na wartość "always", planista będzie wysyłać zapytania tylko do węzłów, które są oznaczone jako "pomocniczy" noderole w [pg_dist_node](reference-hyperscale-metadata.md#worker-node-table).

Obsługiwane wartości tego wyliczenia to:

-   **nigdy:** (domyślnie) wszystkie operacje odczytu są wykonywane w węzłach głównych.
-   **zawsze:** Zamiast tego operacje odczytu są uruchamiane z węzłami pomocniczymi, a instrukcje Insert/Update są wyłączone.

#### <a name="cituscluster_name-text"></a>Citus. \_ Nazwa klastra (tekst)

Informuje planistę węzłów koordynatora, który ze koordynuje klaster. Po \_ ustawieniu nazwy klastra planista będzie wykonywać zapytania dotyczące węzłów procesu roboczego w tym samym klastrze.

#### <a name="citusenable_version_checks-boolean"></a>Citus. Włącz \_ \_ Sprawdzanie wersji (wartość logiczna)

Uaktualnienie wersji Citus (prescaleing) wymaga ponownego uruchomienia serwera (w celu wybrania nowej biblioteki udostępnionej), a następnie polecenia ALTER EXTENSION UPDATE.  Niepowodzenie wykonania obu kroków może potencjalnie spowodować błędy lub awarie.
Funkcja Citus) w ten sposób sprawdza poprawność wersji kodu i dopasowania rozszerzenia oraz błędy, jeśli nie \' .

Ta wartość jest równa true i obowiązuje w przypadku koordynatora. W rzadkich przypadkach złożone procesy uaktualniania mogą wymagać ustawienia tego parametru na FAŁSZ, co powoduje wyłączenie sprawdzania.

#### <a name="cituslog_distributed_deadlock_detection-boolean"></a>\_wykrywanie zakleszczenia rozproszonego Citus. log \_ \_ (wartość logiczna)

Czy należy rejestrować w dzienniku serwera rozproszone przetwarzanie związane z wykrywaniem zakleszczenia. Domyślna wartość to false.

#### <a name="citusdistributed_deadlock_detection_factor-floating-point"></a>Citus. rozproszony \_ \_ czynnik wykrywania zakleszczenia \_ (zmiennoprzecinkowa)

Ustawia czas oczekiwania przed sprawdzeniem rozproszonego zakleszczenia. W szczególności czas oczekiwania będzie to wartość pomnożona przez \' ustawienie [ \_ limitu czasu zakleszczenia](https://www.postgresql.org/docs/current/static/runtime-config-locks.html) PostgreSQL s. Wartość domyślna to `2`. Wartość `-1` powoduje wyłączenie wykrywania zakleszczeniów rozproszonych.

#### <a name="citusnode_connection_timeout-integer"></a>\_limit czasu połączenia Citus. Node \_ (liczba całkowita)

`citus.node_connection_timeout`GUC ustawia maksymalny czas trwania (w milisekundach) oczekiwania na ustanowienie połączenia. Funkcja przeskalowania (Citus) zgłasza błąd, jeśli upłynie limit czasu przed nawiązaniem co najmniej jednego połączenia procesu roboczego. Ta GUC ma wpływ na połączenia z koordynatora do pracowników i procesów roboczych.

-   Wartość domyślna: pięć sekund
-   Minimum: 10 ms
-   Maksimum: jedna godzina

```postgresql
-- set to 30 seconds
ALTER DATABASE foo
SET citus.node_connection_timeout = 30000;
```

### <a name="query-statistics"></a>Statystyka zapytań

#### <a name="citusstat_statements_purge_interval-integer"></a>Citus. stat \_ — \_ Przeczyść interwał przeczyszczania \_ (liczba całkowita)

Ustawia częstotliwość, z jaką demon konserwacji usuwa rekordy z [citus_stat_statements](reference-hyperscale-metadata.md#query-statistics-table) , które są niezgodne z `pg_stat_statements` . Ta wartość konfiguracji Ustawia przedział czasu między przeczyszczaniem w sekundach z wartością domyślną 10. Wartość 0 powoduje wyłączenie przeczyszczania.

```psql
SET citus.stat_statements_purge_interval TO 5;
```

Ten parametr jest skuteczny dla koordynatora i można go zmienić w czasie wykonywania.

### <a name="data-loading"></a>Ładowanie danych

#### <a name="citusmulti_shard_commit_protocol-enum"></a>Citus. wiele \_ fragmentu \_ zatwierdzenia \_ protokołu (enum)

Ustawia protokół zatwierdzania, który ma być używany podczas wykonywania kopiowania w rozproszonej tabeli skrótów. W każdym indywidualnym umieszczeniu fragmentu kopia jest wykonywana w bloku transakcji, aby upewnić się, że żadne dane nie zostaną odebrane w przypadku wystąpienia błędu podczas kopiowania. Istnieje jednak szczególna sytuacja, w której kopia powiedzie się we wszystkich miejscach, ale błąd (sprzęt) występuje przed zatwierdzeniem wszystkich transakcji. Ten parametr może służyć do zapobiegania utracie danych w tym przypadku poprzez wybranie następujących protokołów zatwierdzania:

-   **2PC:** (domyślnie) transakcje, w których wykonywana jest kopia w miejscach fragmentu, są najpierw przygotowywane przy użyciu PostgreSQL \' s [zatwierdzania dwufazowego](http://www.postgresql.org/docs/current/static/sql-prepare-transaction.html) , a następnie zatwierdzania. Nieudane zatwierdzenia można ręcznie odzyskać lub przerwać przy użyciu odpowiednio przygotowanego zatwierdzenia lub WYCOFANia.
    W przypadku korzystania z 2PC, [Maksymalna liczba \_ przygotowanych \_ transakcji](http://www.postgresql.org/docs/current/static/runtime-config-resource.html) powinna być zwiększona dla wszystkich procesów roboczych, zwykle do tej samej wartości co maksymalna liczba \_ połączeń.
-   **1PC:** Transakcje, w których kopie są wykonywane w fragmentuach, są zatwierdzane pojedynczo. Dane mogą zostać utracone, jeśli zatwierdzenie zakończy się niepowodzeniem po pomyślnym zakończeniu kopiowania we wszystkich miejscach (rzadkich).

#### <a name="citusshard_replication_factor-integer"></a>Citus. fragmentu \_ — \_ współczynnik replikacji (liczba całkowita)

Ustawia współczynnik replikacji dla fragmentów, czyli liczbę węzłów, w których zostanie umieszczony fragmentów, i wartość domyślną 1. Ten parametr można ustawić w czasie wykonywania i obowiązuje w przypadku koordynatora. Wartość idealny dla tego parametru zależy od rozmiaru klastra i liczby awarii węzła.  Na przykład możesz chcieć zwiększyć ten współczynnik replikacji w przypadku uruchamiania dużych klastrów i częstego obserwowania awarii węzłów.

#### <a name="citusshard_count-integer"></a>Citus. fragmentu \_ (liczba całkowita)

Ustawia liczbę fragmentu dla tabel z podziałem na partycje i wartości domyślne na 32.  Ta wartość jest używana przez [create_distributed_table](reference-hyperscale-functions.md#create_distributed_table) UDF podczas tworzenia tabel z podziałem na partycje. Ten parametr można ustawić w czasie wykonywania i obowiązuje w przypadku koordynatora.

#### <a name="citusshard_max_size-integer"></a>Citus. fragmentu — \_ Maksymalny \_ rozmiar (liczba całkowita)

Ustawia maksymalny rozmiar, do którego zostanie powiększony fragmentu, zanim zostanie on podzielony na wartość 1 GB. Gdy rozmiar pliku źródłowego \' s (który jest używany do przemieszczania) dla jednej fragmentu przekracza tę wartość konfiguracji, baza danych gwarantuje, że zostanie utworzony nowy fragmentu. Ten parametr można ustawić w czasie wykonywania i obowiązuje w przypadku koordynatora.

### <a name="planner-configuration"></a>Konfiguracja planisty

#### <a name="cituslimit_clause_row_fetch_count-integer"></a>\_ \_ Liczba pobrań wierszy Citus. \_ Limit \_ (liczba całkowita)

Ustawia liczbę wierszy do pobrania na zadanie dla optymalizacji klauzuli limitu.
W niektórych przypadkach, wybierz zapytania z klauzulami granicznymi, może być konieczne pobranie wszystkich wierszy z każdego zadania w celu wygenerowania wyników. W tych przypadkach i gdzie przybliżenie da znaczące wyniki, ta wartość konfiguracji ustawia liczbę wierszy do pobrania z każdego fragmentuu. Limity ograniczania są domyślnie wyłączone i ten parametr ma ustawioną wartość-1. Tę wartość można ustawić w czasie wykonywania i obowiązuje w przypadku koordynatora.

#### <a name="cituscount_distinct_error_rate-floating-point"></a>Citus. Count \_ \_ (liczba \_ zmiennoprzecinkowa) — Współczynnik błędów

Funkcja przeskalowania (Citus) może obliczyć liczbę (DISTINCT) przybliżoną przy użyciu rozszerzenia PostgreSQL-HLL. Ten wpis konfiguracji ustawia żądany współczynnik błędów podczas obliczania liczby (DISTINCT). 0,0, co jest ustawieniem domyślnym, powoduje wyłączenie przybliżeń dla liczby (DISTINCT); i 1,0 nie zapewnia żadnych gwarancji dotyczących dokładności wyników. Zalecamy ustawienie tego parametru na 0,005 w celu uzyskania najlepszych wyników. Tę wartość można ustawić w czasie wykonywania i obowiązuje w przypadku koordynatora.

#### <a name="citustask_assignment_policy-enum"></a>\_zasady przypisywania Citus. Task \_ (enum)

> [!NOTE]
> Ta GUC ma zastosowanie tylko wtedy, gdy [shard_replication_factor](reference-hyperscale-parameters.md#citusshard_replication_factor-integer) jest większa niż jeden lub dla zapytań dotyczących [reference_tables](concepts-hyperscale-distributed-data.md#type-2-reference-tables).

Ustawia zasady do użycia podczas przypisywania zadań do pracowników. Koordynator przypisuje zadania do pracowników w oparciu o lokalizacje fragmentu. Ta wartość konfiguracji określa zasady, które mają być używane podczas wykonywania tych przypisań.
Obecnie istnieją trzy możliwe zasady przypisywania zadań, które mogą być używane.

-   **zachłanne:** Zasady zachłanne są domyślne i mają na celu równomierne dystrybuowanie zadań między pracownikami.
-   działające w **trybie okrężnym:** Zasady działania okrężnego przypisują zadania do pracowników w sposób okrężny między różnymi replikami. Te zasady umożliwiają lepsze wykorzystanie klastra, gdy liczba fragmentu w tabeli jest niska w porównaniu z liczbą procesów roboczych.
-   **Pierwsza replika:** Zasady pierwszej repliki przypisują zadania na podstawie kolejności wstawiania (repliks) dla fragmentów. Innymi słowy, zapytanie fragmentu dla fragmentu jest przypisane do procesu roboczego, który ma pierwszą replikę fragmentu.
    Ta metoda pozwala uzyskać mocne gwarancje dotyczące tego, które fragmentów będą używane w węzłach (czyli silniejszych gwarancjach zamieszkania pamięci).

Ten parametr można ustawić w czasie wykonywania i obowiązuje w przypadku koordynatora.

### <a name="intermediate-data-transfer"></a>Transfer danych pośredni

#### <a name="citusbinary_worker_copy_format-boolean"></a>Citus. binarny \_ \_ Format kopiowania procesów roboczych \_ (wartość logiczna)

Użyj formatu kopiowania binarnego do transferowania danych pośrednich między procesami roboczymi.
Podczas sprzęgania dużych tabel funkcja przeskalowania (Citus) może mieć dynamicznie ponownie podzielić na partycje i losowo dane między różnymi pracownikami. Domyślnie dane te są przesyłane w formacie tekstowym. Włączenie tego parametru powoduje, że baza danych używa formatu serializacji binarnej PostgreSQL do transferu tych danych. Ten parametr jest skuteczny dla procesów roboczych i należy go zmienić w pliku PostgreSQL. conf. Po przeprowadzeniu edycji pliku konfiguracji użytkownicy mogą wysłać sygnał SIGHUP lub ponownie uruchomić serwer, aby ta zmiana zaczęła obowiązywać.

#### <a name="citusbinary_master_copy_format-boolean"></a>Citus. binarny \_ \_ Format kopiowania wzorca \_ (wartość logiczna)

Użyj formatu kopiowania binarnego do przesyłania danych między koordynatorem a pracownikami. Podczas wykonywania zapytań rozproszonych pracownicy przenoszą wyniki pośrednie do koordynatora w celu agregacji końcowej. Domyślnie dane te są przesyłane w formacie tekstowym. Włączenie tego parametru powoduje, że baza danych używa formatu serializacji binarnej PostgreSQL do transferu tych danych.
Ten parametr można ustawić w czasie wykonywania i obowiązuje w przypadku koordynatora.

#### <a name="citusmax_intermediate_result_size-integer"></a>Citus. Max \_ — \_ rozmiar pośredni wyniku \_ (liczba całkowita)

Maksymalny rozmiar w KB wyników pośrednich dla cyklicznych, które nie mogą zostać wypchnięte do węzłów procesu roboczego w celu wykonania oraz złożone podzapytania. Wartością domyślną jest 1 GB, a wartość-1 oznacza brak limitu.
Zapytania przekraczające limit zostaną anulowane i zostanie wyświetlony komunikat o błędzie.

### <a name="ddl"></a>JĘZYKA

#### <a name="citusenable_ddl_propagation-boolean"></a>Citus. Enable \_ DDL \_ (wartość logiczna)

Określa, czy automatycznie propagowanie zmian DDL z koordynatora do wszystkich procesów roboczych. Wartością domyślną jest true. Ze względu na to, że niektóre zmiany schematu wymagają dostępu wyłącznego w tabelach, a ponieważ automatyczne Propagacja dotyczy wszystkich procesów roboczych sekwencyjnie, może to spowodować, że klaster z Citusą jest tymczasowo krótszy. Możesz zdecydować się na wyłączenie tego ustawienia i propagowanie zmian ręcznie.

### <a name="executor-configuration"></a>Konfiguracja wykonawcy

#### <a name="general"></a>Ogólne

##### <a name="citusall_modifications_commutative"></a>Citus. wszystkie \_ modyfikacje \_ komutatywna

Funkcja Citus) wymusza reguły przemienności i uzyskuje odpowiednie blokady dla operacji modyfikacji w celu zagwarantowania prawidłowości działania. Na przykład zakłada się, że instrukcja INSERT jest współdana z inną instrukcją INSERT, ale nie z instrukcją UPDATE lub DELETE. Podobnie przyjęto założenie, że instrukcja UPDATE lub DELETE nie będzie współdziałać z inną instrukcją UPDATE lub DELETE. To zabezpieczenie oznacza, że aktualizacje i usunięcia wymagają przeskalowania (Citus) w celu uzyskania silniejszych blokad.

Jeśli masz instrukcje UPDATE, które są komutatywna przy użyciu operacji wstawiania lub innych aktualizacji, możesz złagodzić te założenia przemienności, ustawiając dla tego parametru wartość true. Jeśli ten parametr ma wartość true, wszystkie polecenia są uważane za komutatywna i są traktowane jako blokada udostępniona, co może poprawić ogólną przepływność. Ten parametr można ustawić w czasie wykonywania i obowiązuje w przypadku koordynatora.

##### <a name="citusremote_task_check_interval-integer"></a>\_Interwał sprawdzania zadań zdalnych Citus. \_ \_ (liczba całkowita)

Ustawia częstotliwość, z jaką funkcja Citus) sprawdza stan zadań zarządzanych przez program wykonujący śledzenie zadań. Wartość domyślna to 10 ms. Koordynator przypisuje zadania do pracowników, a następnie regularnie sprawdza je o \' postępie każdego zadania s. Ta wartość konfiguracji Ustawia przedział czasu między dwoma sprawdzeniami. Ten parametr obowiązuje dla koordynatora i może być ustawiony w czasie wykonywania.

##### <a name="citustask_executor_type-enum"></a>Citus. Task \_ — \_ Typ wykonawcy (enum)

Funkcja do skalowania (Citus) ma trzy typy wykonawcze do uruchamiania rozproszonego zapytania SELECT.  Żądany wykonawca można wybrać przez ustawienie tego parametru konfiguracji. Akceptowane wartości tego parametru to:

-   **adaptacyjne:** Wartość domyślna. Jest optymalny dla szybkich odpowiedzi na zapytania, które obejmują agregacje i współzlokalizowane łącza obejmujące wiele fragmentów.
-   **zadanie-śledzenie:** Program wykonujący zadania śledzenia jest dobrze dostosowany do długotrwałych, złożonych zapytań wymagających Shuffling danych między węzłami procesów roboczych i wydajnym zarządzaniem zasobami.
-   **czas rzeczywisty:** (przestarzały) służy do podobnego celu, co w przypadku wykonawcy adaptacyjnego, ale jest mniej elastyczny i może spowodować zwiększenie naciskania połączenia w węzłach procesu roboczego.

Ten parametr można ustawić w czasie wykonywania i obowiązuje w przypadku koordynatora.

##### <a name="citusmulti_task_query_log_level-enum-multi_task_logging"></a>Citus. wiele \_ zapytań o zadania \_ \_ \_ poziomu dziennika (enum) {#multi_task_logging}

Ustawia poziom dziennika dla każdego zapytania, które generuje więcej niż jedno zadanie (to jest, które trafi więcej niż jeden fragmentu). Rejestrowanie jest przydatne podczas migracji aplikacji wielodostępnej, ponieważ można wybrać opcję błędu lub ostrzeżenia dla takich zapytań, znaleźć je i dodać \_ do nich filtr identyfikatora dzierżawy. Ten parametr można ustawić w czasie wykonywania i obowiązuje w przypadku koordynatora. Wartość domyślna tego parametru jest \' wyłączona \' .

Obsługiwane wartości tego wyliczenia to:

-   **wyłączone:** Wyłącz rejestrowanie zapytań, które generują wiele zadań (czyli obejmują wiele fragmentów)
-   **Debuguj:** Rejestruje instrukcję na poziomie ważności debugowania.
-   **Dziennik:** Rejestruje instrukcję na poziomie ważności dziennika. Wiersz dziennika będzie zawierać kwerendę SQL, która została uruchomiona.
-   **Uwaga:** Rejestruje instrukcję na poziomie ważności powiadomienia.
-   **Ostrzeżenie:** Rejestruje instrukcję na poziomie ważności OSTRZEGAWCZej.
-   **błąd:** Instrukcja Logs na poziomie błędu.

Przydatne może być użycie `error` podczas testowania projektowania i niższy poziom dziennika, tak jak `log` w przypadku rzeczywistego wdrożenia produkcyjnego.
Wybranie `log` tej opcji spowoduje, że zapytania składające się z kilku zadań będą wyświetlane w dziennikach bazy danych za pomocą zapytania wyświetlanego po \" instrukcji.\"

```
LOG:  multi-task query about to be executed
HINT:  Queries are split to multiple tasks if they have to be split into several queries on the workers.
STATEMENT:  select * from foo;
```

##### <a name="citusenable_repartition_joins-boolean"></a>Citus. Włącz \_ sprzężenia ponownej partycji \_ (wartość logiczna)

Zwykle próba przełączenia ponownej partycji z wykonawcą adaptacyjnym zakończy się niepowodzeniem z komunikatem o błędzie.  Ustawienie `citus.enable_repartition_joins` wartości true umożliwia przełączenie (Citus) w celu tymczasowego przejścia do programu wykonującego śledzenie zadań w celu wykonania sprzężenia.  Wartość domyślna to false.

#### <a name="task-tracker-executor-configuration"></a>Konfiguracja wykonawcy monitora zadań

##### <a name="citustask_tracker_delay-integer"></a>Citus. \_ opóźnienie śledzenia zadań \_ (liczba całkowita)

Ten parametr ustawia czas uśpienia śledzenia zadań między wartościami zaokrąglania i domyślnie do 200 ms. Proces śledzenia zadań regularnie wznawia działanie, przegląda wszystkie zadania przypisane do niego, a następnie planuje i wykonuje te zadania.  Następnie śledzenie zadań zostanie wstrzymane przez czas przed ponownym przekroczeniem tych zadań.
Ta wartość konfiguracji określa długość tego okresu uśpienia. Ten parametr jest skuteczny dla procesów roboczych i należy go zmienić w pliku PostgreSQL. conf. Po przeprowadzeniu edycji pliku konfiguracji użytkownicy mogą wysłać sygnał SIGHUP lub ponownie uruchomić serwer, aby zmiany zaczęły obowiązywać.

Ten parametr można zmniejszyć, aby przyciąć opóźnienie spowodowane przez program wykonujący śledzenie zadań przez skrócenie przerwy w czasie między zaokrągleniami zarządzania.
Zmniejszenie opóźnienia jest przydatne w przypadkach, gdy zapytania fragmentu są krótkie i w związku z tym regularnie aktualizują stan.

##### <a name="citusmax_assign_task_batch_size-integer"></a>Citus. Max \_ — \_ \_ Rozmiar partii zadań \_ (liczba całkowita)

Program wykonujący śledzenie zadań synchronicznie przypisuje zadania w partiach do demona w ramach procesów roboczych. Ten parametr ustawia maksymalną liczbę zadań do przypisania w ramach pojedynczej partii. Wybranie większego rozmiaru partii umożliwia przyspieszenie przypisywania zadań. Jeśli jednak liczba procesów roboczych jest duża, pobieranie zadań może trwać dłużej.  Ten parametr można ustawić w czasie wykonywania i obowiązuje w przypadku koordynatora.

##### <a name="citusmax_running_tasks_per_node-integer"></a>Citus. Maksymalna liczba \_ uruchomionych \_ zadań \_ na \_ węzeł (liczba całkowita)

Proces śledzenia zadań planuje i wykonuje zadania przypisane do niego zgodnie z potrzebami. Ta wartość konfiguracji ustawia maksymalną liczbę zadań, które mają być wykonywane współbieżnie w jednym węźle w danym momencie, a wartością domyślną jest 8.

Ograniczenie gwarantuje, że \' nie masz wielu zadań jednocześnie, co pozwala uniknąć rywalizacji o operacje we/wy dysku. Jeśli zapytania są obsługiwane z pamięci lub dysków SSD, można zwiększyć maksymalną liczbę \_ uruchomionych \_ zadań \_ na \_ węzeł bez względu na wiele obaw.

##### <a name="cituspartition_buffer_size-integer"></a>Citus. \_ rozmiar buforu partycji \_ (liczba całkowita)

Ustawia rozmiar buforu, który ma być używany dla operacji partycji i domyślnie wynosi 8 MB.
Funkcja CreatePartition (Citus) umożliwia ponowne Partycjonowanie danych tabeli w wielu plikach, gdy są sprzężone dwie duże tabele. Po wypełnieniu tego buforu partycji dane z podziałem na partycje są opróżniane do plików na dysku.  Ten wpis konfiguracji można ustawić w czasie wykonywania i jest skuteczny dla pracowników.

#### <a name="explain-output"></a>Wyjaśnij dane wyjściowe

##### <a name="citusexplain_all_tasks-boolean"></a>Citus. Wyjaśnij \_ wszystkie \_ zadania (wartość logiczna)

Domyślnie funkcja ze skalowaniem (Citus) pokazuje dane wyjściowe pojedynczego, dowolnego zadania, gdy wykonywane jest [wyjaśnienie](http://www.postgresql.org/docs/current/static/sql-explain.html) zapytania rozproszonego. W większości przypadków dane wyjściowe wyjaśnień będą podobne do poszczególnych zadań. Czasami niektóre zadania będą planowane inaczej lub mają znacznie wyższy czas wykonania. W takich przypadkach może być przydatne włączenie tego parametru, po upływie którego dane wyjściowe OBJAŚNIAjące będą obejmować wszystkie zadania. Wyjaśnienie wszystkich zadań może spowodować wydłużenie tego czasu.

## <a name="postgresql-parameters"></a>Parametry PostgreSQL

* [Data](https://www.postgresql.org/docs/current/datatype-datetime.html#DATATYPE-DATETIME-OUTPUT) i godzina — ustawia format wyświetlania wartości daty i godziny
* [Interwał](https://www.postgresql.org/docs/current/datatype-datetime.html#DATATYPE-INTERVAL-OUTPUT) — ustawia format wyświetlania dla wartości interwału
* [Strefa](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-TIMEZONE) czasowa — ustawia strefę czasu na potrzeby wyświetlania i interpretowania sygnatur czasowych
* [APPLICATION_NAME](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-APPLICATION-NAME) — ustawia nazwę aplikacji, która ma być raportowana w statystykach i dziennikach
* [array_nulls](https://www.postgresql.org/docs/current/runtime-config-compatible.html#GUC-ARRAY-NULLS) — włącza wprowadzanie elementów null w tablicach
* [autopróżniowe](https://www.postgresql.org/docs/current/runtime-config-autovacuum.html#GUC-AUTOVACUUM) — uruchamia podproces autopróżniowy
* [autovacuum_analyze_scale_factor](https://www.postgresql.org/docs/current/runtime-config-autovacuum.html#GUC-AUTOVACUUM-ANALYZE-SCALE-FACTOR) — liczba operacji wstawiania, aktualizacji lub usunięcia krotki przed przeanalizem jako ułameku reltuples
* [autovacuum_analyze_threshold](https://www.postgresql.org/docs/current/runtime-config-autovacuum.html#GUC-AUTOVACUUM-ANALYZE-THRESHOLD) — minimalna liczba operacji wstawiania, aktualizacji lub usunięcia krotki przed przeanalizą
* [autovacuum_naptime](https://www.postgresql.org/docs/current/runtime-config-autovacuum.html#GUC-AUTOVACUUM-NAPTIME) — czas uśpienia między uruchomieniami autopróżni
* [autovacuum_vacuum_cost_delaye](https://www.postgresql.org/docs/current/runtime-config-autovacuum.html#GUC-AUTOVACUUM-VACUUM-COST-DELAY) opóźnienie kosztu próżni w milisekundach dla autopróżni
* [autovacuum_vacuum_cost_limit](https://www.postgresql.org/docs/current/runtime-config-autovacuum.html#GUC-AUTOVACUUM-VACUUM-COST-LIMIT) — wartość kosztu podciśnieniowego dostępna przed Napping, dla autopróżni
* [autovacuum_vacuum_scale_factor](https://www.postgresql.org/docs/current/runtime-config-autovacuum.html#GUC-AUTOVACUUM-VACUUM-SCALE-FACTOR) — liczba aktualizacji i usunięć krotek przed próżnią jako ułamek reltuples
* [autovacuum_vacuum_threshold](https://www.postgresql.org/docs/current/runtime-config-autovacuum.html#GUC-AUTOVACUUM-VACUUM-THRESHOLD) — minimalna liczba aktualizacji krotki lub usunięć przed próżnią
* [autovacuum_work_mem](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-AUTOVACUUM-WORK-MEM) — ustawia maksymalną ilość pamięci, która ma być używana przez każdy proces roboczy autopróżniowy
* [backend_flush_after](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-BACKEND-FLUSH-AFTER) — liczba stron, po których wykonane wcześniej operacje zapisu są opróżniane na dysk
* [backslash_quote](https://www.postgresql.org/docs/current/runtime-config-compatible.html#GUC-BACKSLASH-QUOTE) — określa, czy " \' " jest dozwolony w literałach ciągu
* [bgwriter_delay](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-BGWRITER-DELAY) -czas uśpienia modułu zapisywania w tle między zaokrąglami
* [bgwriter_flush_after](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-BGWRITER-FLUSH-AFTER) — liczba stron, po których wykonane wcześniej operacje zapisu są opróżniane na dysk
* [bgwriter_lru_maxpages](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-BGWRITER-LRU-MAXPAGES) — Maksymalna liczba stron LRU do opróżnienia na okrągły
* [bgwriter_lru_multiplier](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-BGWRITER-LRU-MULTIPLIER) — wielokrotność średniego użycia bufora do zwolnienia na wartość zaokrągloną
* [bytea_output](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-BYTEA-OUTPUT) — ustawia format danych wyjściowych dla Byte
* [check_function_bodies](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-CHECK-FUNCTION-BODIES) — sprawdza treść funkcji podczas tworzenia funkcji
* [checkpoint_completion_target](https://www.postgresql.org/docs/current/runtime-config-wal.html#GUC-CHECKPOINT-COMPLETION-TARGET) czasu poświęconego na opróżnianie buforów zanieczyszczonych podczas punktu kontrolnego jako ułamek interwału punktów kontrolnych
* [checkpoint_timeout](https://www.postgresql.org/docs/current/runtime-config-wal.html#GUC-CHECKPOINT-TIMEOUT) — ustawia maksymalny czas między automatycznymi punktami KONTROLnymi Wal
* [checkpoint_warning](https://www.postgresql.org/docs/current/runtime-config-wal.html#GUC-CHECKPOINT-WARNING) — włącza ostrzeżenia, jeśli segmenty punktów kontrolnych są wypełniane częściej niż to
* [client_encoding](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-CLIENT-ENCODING) — ustawia kodowanie zestawu znaków klienta
* [client_min_messages](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-CLIENT-MIN-MESSAGES) — ustawia poziomy komunikatów, które są wysyłane do klienta
* [commit_delay](https://www.postgresql.org/docs/current/runtime-config-wal.html#GUC-COMMIT-DELAY) — ustawia opóźnienie w mikrosekundach między zatwierdzeniem transakcji a opróżnianiem Wal na dysk
* [commit_siblings](https://www.postgresql.org/docs/12/runtime-config-wal.html#GUC-COMMIT-SIBLINGS) — ustawia minimalnych równoczesnych transakcji otwartych przed wykonaniem commit_delay
* [constraint_exclusion](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-CONSTRAINT-EXCLUSION) — umożliwia planistom używanie ograniczeń do optymalizowania zapytań
* [cpu_index_tuple_cost](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-CPU-INDEX-TUPLE-COST) — ustawia oszacowanie kosztu przetwarzania każdego wpisu indeksu podczas skanowania indeksu
* [cpu_operator_cost](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-CPU-OPERATOR-COST) — ustawia oszacowanie kosztu przetwarzania każdego operatora lub wywołania funkcji przez planistę
* [cpu_tuple_cost](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-CPU-TUPLE-COST) — ustawia oszacowanie kosztu przetwarzania poszczególnych krotek (wiersz) przez planistę
* [cursor_tuple_fraction](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-CURSOR-TUPLE-FRACTION) — ustawia oszacowanie części wierszy kursora, które zostaną pobrane
* [deadlock_timeout](https://www.postgresql.org/docs/current/runtime-config-locks.html#GUC-DEADLOCK-TIMEOUT) — ustawia ilość czasu (w milisekundach) oczekiwania na blokadę przed sprawdzeniem zakleszczenia
* [debug_pretty_print](https://www.postgresql.org/docs/current/runtime-config-logging.html#id-1.6.6.11.5.2.3.1.3) — wyświetla wcięcia w drzewie analizowanie i planowanie
* [debug_print_parse](https://www.postgresql.org/docs/current/runtime-config-logging.html#id-1.6.6.11.5.2.2.1.3) — rejestruje drzewo analizy każdego zapytania
* [debug_print_plan](https://www.postgresql.org/docs/current/runtime-config-logging.html#id-1.6.6.11.5.2.2.1.3) — rejestruje plan wykonania każdego zapytania
* [debug_print_rewritten](https://www.postgresql.org/docs/current/runtime-config-logging.html#id-1.6.6.11.5.2.2.1.3) — rejestruje drzewo analizy z ponownym zapisaniem zapytania
* [default_statistics_target](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-DEFAULT-STATISTICS-TARGET) — ustawia domyślny element docelowy statystyk
* [default_tablespace](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-DEFAULT-TABLESPACE) — ustawia domyślny obszar tabel do tworzenia tabel i indeksów w
* [default_text_search_config](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-DEFAULT-TEXT-SEARCH-CONFIG) — ustawia domyślną konfigurację wyszukiwania tekstu
* [default_transaction_deferrable](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-DEFAULT-TRANSACTION-DEFERRABLE) — ustawia domyślny stan odroczony nowych transakcji
* [default_transaction_isolation](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-DEFAULT-TRANSACTION-ISOLATION) — ustawia poziom izolacji transakcji dla każdej nowej transakcji
* [default_transaction_read_only](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-DEFAULT-TRANSACTION-READ-ONLY) — ustawia domyślny stan tylko do odczytu dla nowych transakcji
* default_with_oids — domyślnie tworzy nowe tabele z identyfikatorami OID
* [effective_cache_size](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-EFFECTIVE-CACHE-SIZE) — ustawia założenie planisty o rozmiarze pamięci podręcznej dysku
* [enable_bitmapscan](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-BITMAPSCAN) — umożliwia korzystanie z planów skanowania mapy bitowej przez planistę
* [enable_gathermerge](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-GATHERMERGE) — umożliwia korzystanie z planów zbierania scalonych danych przez planistę
* [enable_hashagg](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-HASHAGG) — umożliwia korzystanie z opartych na skrócie planów agregacji
* [enable_hashjoin](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-HASHJOIN) — umożliwia korzystanie z planów dołączania skrótów przez planistę
* [enable_indexonlyscan](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-INDEXONLYSCAN) — umożliwia korzystanie z planów skanowania tylko do indeksowania przez planistę
* [enable_indexscan](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-INDEXSCAN) — umożliwia korzystanie z planów skanowania indeksu w usłudze Planner
* [enable_material](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-MATERIAL) — umożliwia korzystanie z materializację
* [enable_mergejoin](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-MERGEJOIN) — umożliwia korzystanie z planów sprzężenia scalania w usłudze Planner
* [enable_nestloop](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-NESTLOOP) — umożliwia użycie przez planistę planów sprzężeń zagnieżdżonych pętli
* [enable_seqscan](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-SEQSCAN) — umożliwia korzystanie z planów skanowania sekwencyjnego w programie Planner
* [enable_sort](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-SORT) — umożliwia korzystanie z jawnych kroków sortowania przez planistę
* [enable_tidscan](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-TIDSCAN) — umożliwia korzystanie z planów skanowania identyfikatora TID przez planistę
* [escape_string_warning](https://www.postgresql.org/docs/current/runtime-config-compatible.html#GUC-ESCAPE-STRING-WARNING) — ostrzega o ucieczkach ukośników odwrotnych w zwykłych literałach ciągów
* [exit_on_error](https://www.postgresql.org/docs/current/runtime-config-error-handling.html#GUC-EXIT-ON-ERROR) — kończy sesję na dowolnym błędzie
* [extra_float_digits](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-EXTRA-FLOAT-DIGITS) — ustawia liczbę cyfr wyświetlanych dla wartości zmiennoprzecinkowych
* [force_parallel_mode](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-FORCE-PARALLEL-MODE) -wymusza użycie równoległych obiektów zapytań
* [from_collapse_limit](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-FROM-COLLAPSE-LIMIT) — ustawia rozmiar z listy, powyżej którego podzapytania nie są zwinięte
* [geqo](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-GEQO) — włącza optymalizację kwerendy genetycznej
* [geqo_effort](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-GEQO-EFFORT) -geqo: nakład pracy służy do ustawiania wartości domyślnej dla innych parametrów geqo
* [geqo_generations](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-GEQO-GENERATIONS) -geqo: liczba iteracji algorytmu
* [geqo_pool_size](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-GEQO-POOL-SIZE) -geqo: liczba osób w populacji
* [geqo_seed](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-GEQO-SEED) -geqo: inicjator zaznaczenia ścieżki losowej
* [geqo_selection_bias](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-GEQO-SELECTION-BIAS) -geqo: ciśnienie selektywne w populacji
* [geqo_threshold](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-GEQO-THRESHOLD) — ustawia próg z elementów, po których jest używany geqo
* [gin_fuzzy_search_limit](https://www.postgresql.org/docs/current/runtime-config-client.html#id-1.6.6.14.5.2.2.1.3) — ustawia maksymalny dozwolony wynik dla wyszukiwania dokładnego według ątek
* [gin_pending_list_limit](https://www.postgresql.org/docs/current/runtime-config-client.html#id-1.6.6.14.2.2.23.1.3) — ustawia maksymalny rozmiar listy oczekujących na indeks ątek
* [idle_in_transaction_session_timeout](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-IDLE-IN-TRANSACTION-SESSION-TIMEOUT) — ustawia maksymalny dozwolony czas trwania transakcji z fazy biegu
* [join_collapse_limit](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-JOIN-COLLAPSE-LIMIT) — ustawia rozmiar z listy, poza którym konstrukcje sprzężenia nie są spłaszczone
* [LC_MONETARY](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-LC-MONETARY) — ustawia ustawienia regionalne do formatowania kwot pieniężnych
* [LC_NUMERIC](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-LC-NUMERIC) — ustawia ustawienia regionalne dla numerów formatowania
* [lo_compat_privileges](https://www.postgresql.org/docs/current/runtime-config-compatible.html#GUC-LO-COMPAT-PRIVILEGES) — włącza tryb zgodności z poprzednimi wersjami dla sprawdzania uprawnień dla dużych obiektów
* [LOCK_TIMEOUT](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-LOCK-TIMEOUT) — ustawia maksymalny dozwolony czas trwania (w milisekundach) oczekiwania na blokadę. 0 powoduje wyłączenie
* [log_autovacuum_min_duration](https://www.postgresql.org/docs/current/runtime-config-autovacuum.html#) — ustawia minimalny czas wykonywania, powyżej którego będą rejestrowane akcje autopróżniowe
* [log_checkpoints](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-CHECKPOINTS) — rejestruje każdy punkt kontrolny
* [log_connections](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-CONNECTIONS) — rejestruje każde pomyślne połączenie
* [log_destination](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-DESTINATION) — ustawia miejsce docelowe dla danych wyjściowych dziennika serwera
* [log_disconnections](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-DISCONNECTIONS) -rejestruje koniec sesji, w tym czas trwania
* [log_duration](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-DURATION) — rejestruje czas trwania każdej wykonanej instrukcji SQL
* [log_error_verbosity](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-ERROR-VERBOSITY) — ustawia szczegółowość rejestrowanych komunikatów
* [log_lock_waits](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-LOCK-WAITS) — umożliwia rejestrowanie długich blokad
* [log_min_duration_statement](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-MIN-DURATION-STATEMENT) — ustawia minimalny czas wykonywania (w milisekundach), powyżej którego będą rejestrowane instrukcje. -1 wyłącza czasy trwania instrukcji rejestrowania
* [log_min_error_statement](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-MIN-ERROR-STATEMENT) — powoduje, że wszystkie instrukcje generują błąd na tym poziomie lub powyżej tego poziomu do zarejestrowania
* [log_min_messages](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-MIN-MESSAGES) — ustawia zarejestrowane poziomy komunikatów
* [log_replication_commands](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-REPLICATION-COMMANDS) — rejestruje każde polecenie replikacji
* [log_statement](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-STATEMENT) — ustawia typ zarejestrowanych instrukcji
* [log_statement_stats](https://www.postgresql.org/docs/current/runtime-config-statistics.html#id-1.6.6.12.3.2.1.1.3) — dla każdego zapytania, zapisuje zbiorcze dane statystyczne wydajności w dzienniku serwera
* [log_temp_files](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-TEMP-FILES) — rejestruje użycie plików tymczasowych o rozmiarze większym niż ta liczba kilobajtów
* [maintenance_work_mem](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-MAINTENANCE-WORK-MEM) — ustawia maksymalną ilość pamięci, która będzie używana na potrzeby operacji konserwacyjnych
* [max_parallel_workers](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-MAX-PARALLEL-WORKERS) — ustawia maksymalną liczbę równoległych procesów roboczych, które mogą być jednocześnie aktywne
* [max_parallel_workers_per_gather](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-MAX-PARALLEL-WORKERS-PER-GATHER) — ustawia maksymalną liczbę procesów równoległych na węzeł wykonawczy
* [max_pred_locks_per_page](https://www.postgresql.org/docs/current/runtime-config-locks.html#GUC-MAX-PRED-LOCKS-PER-PAGE) — ustawia maksymalną liczbę niezablokowanych krotek na stronie
* [max_pred_locks_per_relation](https://www.postgresql.org/docs/current/runtime-config-locks.html#GUC-MAX-PRED-LOCKS-PER-RELATION) — ustawia maksymalną liczbę stron i krotek z zablokowanym predykatem na relację
* [max_standby_archive_delay](https://www.postgresql.org/docs/current/runtime-config-replication.html#GUC-MAX-STANDBY-ARCHIVE-DELAY) — ustawia Maksymalne opóźnienie przed anulowaniem zapytań, gdy serwer rezerwy dynamicznej przetwarza zarchiwizowane dane dotyczące pliku Wal
* [max_standby_streaming_delay](https://www.postgresql.org/docs/current/runtime-config-replication.html#GUC-MAX-STANDBY-STREAMING-DELAY) — ustawia Maksymalne opóźnienie przed anulowaniem zapytań, gdy serwer rezerwy dynamicznej przetwarza strumieniowo dane dotyczące pliku Wal
* [max_sync_workers_per_subscription](https://www.postgresql.org/docs/current/runtime-config-replication.html#GUC-MAX-SYNC-WORKERS-PER-SUBSCRIPTION) — Maksymalna liczba procesów roboczych synchronizacji tabeli na subskrypcję
* [max_wal_size](https://www.postgresql.org/docs/current/runtime-config-wal.html#GUC-MAX-WAL-SIZE) — ustawia rozmiar pliku WAL, który wyzwala punkt kontrolny
* [min_parallel_index_scan_size](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-MIN-PARALLEL-INDEX-SCAN-SIZE) — ustawia minimalną ilość danych indeksu dla skanowania równoległego
* [min_wal_size](https://www.postgresql.org/docs/current/runtime-config-wal.html#GUC-MIN-WAL-SIZE) — ustawia minimalny rozmiar do zmniejszenia wartości Wal
* [operator_precedence_warning](https://www.postgresql.org/docs/current/runtime-config-compatible.html#GUC-OPERATOR-PRECEDENCE-WARNING) — emituje Ostrzeżenie dla konstrukcji, które zmieniły znaczenie od czasu PostgreSQL 9,4
* [parallel_setup_cost](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-PARALLEL-SETUP-COST) — ustawia oszacowanie kosztu uruchamiania procesów roboczych dla zapytań równoległych przez planistę
* [parallel_tuple_cost](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-PARALLEL-TUPLE-COST) — ustawia oszacowanie kosztów przekazywania poszczególnych krotek (wierszy) od procesu roboczego do głównego zaplecza planisty
* [pg_stat_statements. Save](https://www.postgresql.org/docs/current/pgstatstatements.html#id-1.11.7.38.8) — zapisuje dane statystyczne pg_stat_statements między zamknięciami serwera
* [pg_stat_statements. Track](https://www.postgresql.org/docs/current/pgstatstatements.html#id-1.11.7.38.8) — wybiera, które instrukcje są śledzone przez pg_stat_statements
* [pg_stat_statements. track_utility](https://www.postgresql.org/docs/current/pgstatstatements.html#id-1.11.7.38.8) — określa, czy polecenia narzędziowe są śledzone przez pg_stat_statements
* [quote_all_identifiers](https://www.postgresql.org/docs/current/runtime-config-compatible.html#GUC-QUOTE-ALL-IDENTIFIERS) — podczas generowania fragmentów SQL, cudzysłowy wszystkich identyfikatorów
* [random_page_cost](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-RANDOM-PAGE-COST) — ustawia oszacowanie kosztu strony dysku niesekwencyjnego pobrania
* [row_security](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-ROW-SECURITY) — włącza zabezpieczenia wierszy
* [search_path](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-SEARCH-PATH) — ustawia kolejność wyszukiwania w schemacie dla nazw, które nie są kwalifikowane do schematu
* [seq_page_cost](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-SEQ-PAGE-COST) — ustawia oszacowanie kosztu strony dysku pobranego sekwencyjnie
* [session_replication_role](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-SESSION-REPLICATION-ROLE) — ustawia zachowanie sesji dla wyzwalaczy i ponownie Napisz reguły
* [standard_conforming_strings](https://www.postgresql.org/docs/current/runtime-config-compatible.html#id-1.6.6.16.2.2.7.1.3) -Przyczyna "..." ciągi do traktowania ukośników odwrotnych
* [statement_timeout](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-STATEMENT-TIMEOUT) — ustawia maksymalny dozwolony czas trwania (w milisekundach) dowolnej instrukcji. 0 powoduje wyłączenie
* [synchronize_seqscans](https://www.postgresql.org/docs/current/runtime-config-compatible.html#id-1.6.6.16.2.2.8.1.3) — umożliwia synchronizowanie skanów sekwencyjnych
* [synchronous_commit](https://www.postgresql.org/docs/current/runtime-config-wal.html#GUC-SYNCHRONOUS-COMMIT) — ustawia poziom synchronizacji bieżącej transakcji
* [tcp_keepalives_count](https://www.postgresql.org/docs/current/runtime-config-connection.html#GUC-TCP-KEEPALIVES-COUNT) — Maksymalna liczba retransmisji w ramach NIEaktywności TCP
* [tcp_keepalives_idle](https://www.postgresql.org/docs/current/runtime-config-connection.html#GUC-TCP-KEEPALIVES-IDLE) czasu między wystawianiem aktywności protokołu TCP
* [tcp_keepalives_interval](https://www.postgresql.org/docs/current/runtime-config-connection.html#GUC-TCP-KEEPALIVES-INTERVAL) czasu między retransmisjami za ruch przychodzący protokołu TCP
* [temp_buffers](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-TEMP-BUFFERS) — ustawia maksymalną liczbę buforów tymczasowych używanych przez każdą sesję bazy danych
* [temp_tablespaces](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-TEMP-TABLESPACES) — ustawia obszary tabel, które mają być używane dla tabel tymczasowych i sortowania plików
* [track_activities](https://www.postgresql.org/docs/current/runtime-config-statistics.html#GUC-TRACK-ACTIVITIES) — zbiera informacje o wykonywaniu poleceń
* [track_counts](https://www.postgresql.org/docs/current/runtime-config-statistics.html#GUC-TRACK-COUNTS) — zbiera dane statystyczne dotyczące aktywności bazy danych
* [track_functions](https://www.postgresql.org/docs/current/runtime-config-statistics.html#GUC-TRACK-FUNCTIONS) — zbiera dane statystyczne na poziomie funkcji dla działania bazy danych
* [track_io_timing](https://www.postgresql.org/docs/current/runtime-config-statistics.html#GUC-TRACK-IO-TIMING) — zbiera statystyki czasu dla działania we/wy bazy danych
* [transform_null_equals](https://www.postgresql.org/docs/current/runtime-config-compatible.html#GUC-TRANSFORM-NULL-EQUALS) -traktuje "wyrażenie = null" jako "wyrażenie IS NULL"
* [vacuum_cost_delay](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-VACUUM-COST-DELAY) -opóźnienie kosztu próżni w milisekundach
* [vacuum_cost_limita](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-VACUUM-COST-LIMIT) kwota kosztu podciśnieniowego dostępna przed Napping
* [vacuum_cost_page_dirty](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-VACUUM-COST-PAGE-DIRTY) -koszt próżniowy dla strony 'dirtied przez próżniowy
* koszt próżniowy [vacuum_cost_page_hit](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-VACUUM-COST-PAGE-HIT) dla strony znalezionej w pamięci podręcznej buforu
* w pamięci podręcznej buforu nie znaleziono kosztu próżni w [vacuum_cost_page_miss](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-VACUUM-COST-PAGE-MISS)
* [vacuum_defer_cleanup_age](https://www.postgresql.org/docs/current/runtime-config-replication.html#GUC-VACUUM-DEFER-CLEANUP-AGE) — liczba transakcji, za pomocą których należy ODROCZYć próżnię i gorącą oczyszczenie (jeśli istnieją)
* [vacuum_freeze_min_age](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-VACUUM-FREEZE-MIN-AGE) — minimalny wiek, w którym próżnia powinna zablokować wiersz tabeli
* [vacuum_freeze_table_age](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-VACUUM-FREEZE-TABLE-AGE) — okres, w którym próżnia powinna skanować całą tabelę w celu zablokowania krotek
* [vacuum_multixact_freeze_min_age](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-VACUUM-MULTIXACT-FREEZE-MIN-AGE) — minimalny wiek, w którym próżnia powinna zamrozić MultiXactId w wierszu tabeli
* [vacuum_multixact_freeze_table_age](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-VACUUM-MULTIXACT-FREEZE-TABLE-AGE) — wiek multixact, w którym próżnia powinna skanować całą tabelę w celu zablokowania krotek
* [wal_receiver_status_interval](https://www.postgresql.org/docs/current/runtime-config-replication.html#GUC-WAL-RECEIVER-STATUS-INTERVAL) — ustawia maksymalny interwał między raportami o stanie odbiorcy Wal a podstawowym
* [wal_writer_delay](https://www.postgresql.org/docs/current/runtime-config-wal.html#GUC-WAL-WRITER-DELAY) czasu między opróżnianiem Wal wykonywanymi w składniku zapisywania Wal
* [wal_writer_flush_after](https://www.postgresql.org/docs/current/runtime-config-wal.html#GUC-WAL-WRITER-FLUSH-AFTER) — ilość pliku Wal zapisywana przez moduł zapisujący WAL, który wyzwala opróżnianie
* [work_mem](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-WORK-MEM) — ustawia ilość pamięci, która ma być używana przez wewnętrzne operacje sortowania i tabele skrótów przed zapisem do plików na dyskach tymczasowych
* [xmlbinary](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-XMLBINARY) — ustawia sposób kodowania wartości binarnych w formacie XML
* [xmloption](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-XMLOPTION) — określa, czy dane XML w niejawnej analizie i serializacji mają być uznawane za dokumenty lub fragmenty zawartości

## <a name="next-steps"></a>Następne kroki

* Inną formą konfiguracji, oprócz parametrów serwera, są [Opcje konfiguracji](concepts-hyperscale-configuration-options.md) zasobów w grupie serwerów Citus.
* Bazowa baza danych PostgreSQL ma także [Parametry konfiguracji](http://www.postgresql.org/docs/current/static/runtime-config.html).
