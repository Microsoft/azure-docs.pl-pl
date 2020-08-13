---
title: Parametry serwera — funkcja do skalowania (Citus) — Azure Database for PostgreSQL
description: Parametry w interfejsie API SQL Citus
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: reference
ms.date: 08/10/2020
ms.openlocfilehash: 57258540f3cd7b4c47b662b0885453cedd188e5b
ms.sourcegitcommit: 1aef4235aec3fd326ded18df7fdb750883809ae8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/12/2020
ms.locfileid: "88136839"
---
# <a name="server-parameters"></a>Parametry serwera

Istnieją różne parametry serwera, które mają wpływ na zachowanie funkcji Citus), oba parametry ze standardowego PostgreSQL i parametry specyficzne dla. Więcej informacji o parametrach konfiguracji PostgreSQL można znaleźć w sekcji [Konfiguracja czasu wykonywania](http://www.postgresql.org/docs/current/static/runtime-config.html) w dokumentacji PostgreSQL.

Pozostała część tego odwołania ma na celu przedyskutowanie specyficznych dla Citus parametrów konfiguracji. Te parametry można ustawić w Azure Portal w obszarze **Parametry węzła procesu roboczego** w obszarze **Ustawienia** dla grupy serwerów Citus.

> [!NOTE]
>
> Grupy serwerów ze skalowaniem ze starszymi wersjami aparatu Citus mogą nie oferować wszystkich parametrów wymienionych poniżej.

## <a name="general-configuration"></a>Konfiguracja ogólna

### <a name="citususe_secondary_nodes-enum"></a>Citus. Użyj \_ węzłów pomocniczych \_ (enum)

Ustawia zasady, które mają być używane podczas wybierania węzłów dla wybranych zapytań. Jeśli jest ustawiona na wartość "always", planista będzie wysyłać zapytania tylko do węzłów, które są oznaczone jako "pomocniczy" noderole w [pg_dist_node](reference-hyperscale-metadata.md#worker-node-table).

Obsługiwane wartości tego wyliczenia to:

-   **nigdy:** (domyślnie) wszystkie operacje odczytu są wykonywane w węzłach głównych.
-   **zawsze:** Zamiast tego operacje odczytu są uruchamiane z węzłami pomocniczymi, a instrukcje Insert/Update są wyłączone.

### <a name="cituscluster_name-text"></a>Citus. \_ Nazwa klastra (tekst)

Informuje planistę węzłów koordynatora, który ze koordynuje klaster. Po \_ ustawieniu nazwy klastra planista będzie wykonywać zapytania dotyczące węzłów procesu roboczego w tym samym klastrze.

### <a name="citusenable_version_checks-boolean"></a>Citus. Włącz \_ \_ Sprawdzanie wersji (wartość logiczna)

Uaktualnienie wersji Citus (prescaleing) wymaga ponownego uruchomienia serwera (w celu wybrania nowej biblioteki udostępnionej), a następnie polecenia ALTER EXTENSION UPDATE.  Niepowodzenie wykonania obu kroków może potencjalnie spowodować błędy lub awarie.
Funkcja Citus) w ten sposób sprawdza poprawność wersji kodu i dopasowania rozszerzenia oraz błędy, jeśli nie \' .

Ta wartość jest równa true i obowiązuje w przypadku koordynatora. W rzadkich przypadkach złożone procesy uaktualniania mogą wymagać ustawienia tego parametru na FAŁSZ, co powoduje wyłączenie sprawdzania.

### <a name="cituslog_distributed_deadlock_detection-boolean"></a>\_wykrywanie zakleszczenia rozproszonego Citus. log \_ \_ (wartość logiczna)

Czy należy rejestrować w dzienniku serwera rozproszone przetwarzanie związane z wykrywaniem zakleszczenia. Domyślna wartość to false.

### <a name="citusdistributed_deadlock_detection_factor-floating-point"></a>Citus. rozproszony \_ \_ czynnik wykrywania zakleszczenia \_ (zmiennoprzecinkowa)

Ustawia czas oczekiwania przed sprawdzeniem rozproszonego zakleszczenia. W szczególności czas oczekiwania będzie to wartość pomnożona przez \' ustawienie [ \_ limitu czasu zakleszczenia](https://www.postgresql.org/docs/current/static/runtime-config-locks.html) PostgreSQL s. Wartość domyślna to `2`. Wartość `-1` powoduje wyłączenie wykrywania zakleszczeniów rozproszonych.

### <a name="citusnode_connection_timeout-integer"></a>\_limit czasu połączenia Citus. Node \_ (liczba całkowita)

`citus.node_connection_timeout`GUC ustawia maksymalny czas trwania (w milisekundach) oczekiwania na ustanowienie połączenia. Funkcja przeskalowania (Citus) zgłasza błąd, jeśli upłynie limit czasu przed nawiązaniem co najmniej jednego połączenia procesu roboczego. Ta GUC ma wpływ na połączenia z koordynatora do pracowników i procesów roboczych.

-   Wartość domyślna: pięć sekund
-   Minimum: 10 ms
-   Maksimum: jedna godzina

```postgresql
-- set to 30 seconds
ALTER DATABASE foo
SET citus.node_connection_timeout = 30000;
```

## <a name="query-statistics"></a>Statystyka zapytań

### <a name="citusstat_statements_purge_interval-integer"></a>Citus. stat \_ — \_ Przeczyść interwał przeczyszczania \_ (liczba całkowita)

Ustawia częstotliwość, z jaką demon konserwacji usuwa rekordy z [citus_stat_statements](reference-hyperscale-metadata.md#query-statistics-table) , które są niezgodne z `pg_stat_statements` . Ta wartość konfiguracji Ustawia przedział czasu między przeczyszczaniem w sekundach z wartością domyślną 10. Wartość 0 powoduje wyłączenie przeczyszczania.

```psql
SET citus.stat_statements_purge_interval TO 5;
```

Ten parametr jest skuteczny dla koordynatora i można go zmienić w czasie wykonywania.

## <a name="data-loading"></a>Ładowanie danych

### <a name="citusmulti_shard_commit_protocol-enum"></a>Citus. wiele \_ fragmentu \_ zatwierdzenia \_ protokołu (enum)

Ustawia protokół zatwierdzania, który ma być używany podczas wykonywania kopiowania w rozproszonej tabeli skrótów. W każdym indywidualnym umieszczeniu fragmentu kopia jest wykonywana w bloku transakcji, aby upewnić się, że żadne dane nie zostaną odebrane w przypadku wystąpienia błędu podczas kopiowania. Istnieje jednak szczególna sytuacja, w której kopia powiedzie się we wszystkich miejscach, ale błąd (sprzęt) występuje przed zatwierdzeniem wszystkich transakcji. Ten parametr może służyć do zapobiegania utracie danych w tym przypadku poprzez wybranie następujących protokołów zatwierdzania:

-   **2PC:** (domyślnie) transakcje, w których wykonywana jest kopia w miejscach fragmentu, są najpierw przygotowywane przy użyciu PostgreSQL \' s [zatwierdzania dwufazowego](http://www.postgresql.org/docs/current/static/sql-prepare-transaction.html) , a następnie zatwierdzania. Nieudane zatwierdzenia można ręcznie odzyskać lub przerwać przy użyciu odpowiednio przygotowanego zatwierdzenia lub WYCOFANia.
    W przypadku korzystania z 2PC, [Maksymalna liczba \_ przygotowanych \_ transakcji](http://www.postgresql.org/docs/current/static/runtime-config-resource.html) powinna być zwiększona dla wszystkich procesów roboczych, zwykle do tej samej wartości co maksymalna liczba \_ połączeń.
-   **1PC:** Transakcje, w których kopie są wykonywane w fragmentuach, są zatwierdzane pojedynczo. Dane mogą zostać utracone, jeśli zatwierdzenie zakończy się niepowodzeniem po pomyślnym zakończeniu kopiowania we wszystkich miejscach (rzadkich).

### <a name="citusshard_replication_factor-integer"></a>Citus. fragmentu \_ — \_ współczynnik replikacji (liczba całkowita)

Ustawia współczynnik replikacji dla fragmentów, czyli liczbę węzłów, w których zostanie umieszczony fragmentów, i wartość domyślną 1. Ten parametr można ustawić w czasie wykonywania i obowiązuje w przypadku koordynatora. Wartość idealny dla tego parametru zależy od rozmiaru klastra i liczby awarii węzła.  Na przykład możesz chcieć zwiększyć ten współczynnik replikacji w przypadku uruchamiania dużych klastrów i częstego obserwowania awarii węzłów.

### <a name="citusshard_count-integer"></a>Citus. fragmentu \_ (liczba całkowita)

Ustawia liczbę fragmentu dla tabel z podziałem na partycje i wartości domyślne na 32.  Ta wartość jest używana przez [create_distributed_table](reference-hyperscale-functions.md#create_distributed_table) UDF podczas tworzenia tabel z podziałem na partycje. Ten parametr można ustawić w czasie wykonywania i obowiązuje w przypadku koordynatora.

### <a name="citusshard_max_size-integer"></a>Citus. fragmentu — \_ Maksymalny \_ rozmiar (liczba całkowita)

Ustawia maksymalny rozmiar, do którego zostanie powiększony fragmentu, zanim zostanie on podzielony na wartość 1 GB. Gdy rozmiar pliku źródłowego \' s (który jest używany do przemieszczania) dla jednej fragmentu przekracza tę wartość konfiguracji, baza danych gwarantuje, że zostanie utworzony nowy fragmentu. Ten parametr można ustawić w czasie wykonywania i obowiązuje w przypadku koordynatora.

## <a name="planner-configuration"></a>Konfiguracja planisty

### <a name="cituslimit_clause_row_fetch_count-integer"></a>\_ \_ Liczba pobrań wierszy Citus. \_ Limit \_ (liczba całkowita)

Ustawia liczbę wierszy do pobrania na zadanie dla optymalizacji klauzuli limitu.
W niektórych przypadkach, wybierz zapytania z klauzulami granicznymi, może być konieczne pobranie wszystkich wierszy z każdego zadania w celu wygenerowania wyników. W tych przypadkach i gdzie przybliżenie da znaczące wyniki, ta wartość konfiguracji ustawia liczbę wierszy do pobrania z każdego fragmentuu. Limity ograniczania są domyślnie wyłączone i ten parametr ma ustawioną wartość-1. Tę wartość można ustawić w czasie wykonywania i obowiązuje w przypadku koordynatora.

### <a name="cituscount_distinct_error_rate-floating-point"></a>Citus. Count \_ \_ (liczba \_ zmiennoprzecinkowa) — Współczynnik błędów

Funkcja przeskalowania (Citus) może obliczyć liczbę (DISTINCT) przybliżoną przy użyciu rozszerzenia PostgreSQL-HLL. Ten wpis konfiguracji ustawia żądany współczynnik błędów podczas obliczania liczby (DISTINCT). 0,0, co jest ustawieniem domyślnym, powoduje wyłączenie przybliżeń dla liczby (DISTINCT); i 1,0 nie zapewnia żadnych gwarancji dotyczących dokładności wyników. Zalecamy ustawienie tego parametru na 0,005 w celu uzyskania najlepszych wyników. Tę wartość można ustawić w czasie wykonywania i obowiązuje w przypadku koordynatora.

### <a name="citustask_assignment_policy-enum"></a>\_zasady przypisywania Citus. Task \_ (enum)

> [!NOTE]
> Ta GUC ma zastosowanie tylko wtedy, gdy [shard_replication_factor](reference-hyperscale-parameters.md#citusshard_replication_factor-integer) jest większa niż jeden lub dla zapytań dotyczących [reference_tables](concepts-hyperscale-distributed-data.md#type-2-reference-tables).

Ustawia zasady do użycia podczas przypisywania zadań do pracowników. Koordynator przypisuje zadania do pracowników w oparciu o lokalizacje fragmentu. Ta wartość konfiguracji określa zasady, które mają być używane podczas wykonywania tych przypisań.
Obecnie istnieją trzy możliwe zasady przypisywania zadań, które mogą być używane.

-   **zachłanne:** Zasady zachłanne są domyślne i mają na celu równomierne dystrybuowanie zadań między pracownikami.
-   działające w **trybie okrężnym:** Zasady działania okrężnego przypisują zadania do pracowników w sposób okrężny między różnymi replikami. Te zasady umożliwiają lepsze wykorzystanie klastra, gdy liczba fragmentu w tabeli jest niska w porównaniu z liczbą procesów roboczych.
-   **Pierwsza replika:** Zasady pierwszej repliki przypisują zadania na podstawie kolejności wstawiania (repliks) dla fragmentów. Innymi słowy, zapytanie fragmentu dla fragmentu jest przypisane do procesu roboczego, który ma pierwszą replikę fragmentu.
    Ta metoda pozwala uzyskać mocne gwarancje dotyczące tego, które fragmentów będą używane w węzłach (czyli silniejszych gwarancjach zamieszkania pamięci).

Ten parametr można ustawić w czasie wykonywania i obowiązuje w przypadku koordynatora.

## <a name="intermediate-data-transfer"></a>Transfer danych pośredni

### <a name="citusbinary_worker_copy_format-boolean"></a>Citus. binarny \_ \_ Format kopiowania procesów roboczych \_ (wartość logiczna)

Użyj formatu kopiowania binarnego do transferowania danych pośrednich między procesami roboczymi.
Podczas sprzęgania dużych tabel funkcja przeskalowania (Citus) może mieć dynamicznie ponownie podzielić na partycje i losowo dane między różnymi pracownikami. Domyślnie dane te są przesyłane w formacie tekstowym. Włączenie tego parametru powoduje, że baza danych używa formatu serializacji binarnej PostgreSQL do transferu tych danych. Ten parametr jest skuteczny dla procesów roboczych i należy go zmienić w pliku PostgreSQL. conf. Po przeprowadzeniu edycji pliku konfiguracji użytkownicy mogą wysłać sygnał SIGHUP lub ponownie uruchomić serwer, aby ta zmiana zaczęła obowiązywać.

### <a name="citusbinary_master_copy_format-boolean"></a>Citus. binarny \_ \_ Format kopiowania wzorca \_ (wartość logiczna)

Użyj formatu kopiowania binarnego do przesyłania danych między koordynatorem a pracownikami. Podczas wykonywania zapytań rozproszonych pracownicy przenoszą wyniki pośrednie do koordynatora w celu agregacji końcowej. Domyślnie dane te są przesyłane w formacie tekstowym. Włączenie tego parametru powoduje, że baza danych używa formatu serializacji binarnej PostgreSQL do transferu tych danych.
Ten parametr można ustawić w czasie wykonywania i obowiązuje w przypadku koordynatora.

### <a name="citusmax_intermediate_result_size-integer"></a>Citus. Max \_ — \_ rozmiar pośredni wyniku \_ (liczba całkowita)

Maksymalny rozmiar w KB wyników pośrednich dla cyklicznych, które nie mogą zostać wypchnięte do węzłów procesu roboczego w celu wykonania oraz złożone podzapytania. Wartością domyślną jest 1 GB, a wartość-1 oznacza brak limitu.
Zapytania przekraczające limit zostaną anulowane i zostanie wyświetlony komunikat o błędzie.

## <a name="ddl"></a>JĘZYKA

### <a name="citusenable_ddl_propagation-boolean"></a>Citus. Enable \_ DDL \_ (wartość logiczna)

Określa, czy automatycznie propagowanie zmian DDL z koordynatora do wszystkich procesów roboczych. Wartością domyślną jest true. Ze względu na to, że niektóre zmiany schematu wymagają dostępu wyłącznego w tabelach, a ponieważ automatyczne Propagacja dotyczy wszystkich procesów roboczych sekwencyjnie, może to spowodować, że klaster z Citusą jest tymczasowo krótszy. Możesz zdecydować się na wyłączenie tego ustawienia i propagowanie zmian ręcznie.

## <a name="executor-configuration"></a>Konfiguracja wykonawcy

### <a name="general"></a>Ogólne

#### <a name="citusall_modifications_commutative"></a>Citus. wszystkie \_ modyfikacje \_ komutatywna

Funkcja Citus) wymusza reguły przemienności i uzyskuje odpowiednie blokady dla operacji modyfikacji w celu zagwarantowania prawidłowości działania. Na przykład zakłada się, że instrukcja INSERT jest współdana z inną instrukcją INSERT, ale nie z instrukcją UPDATE lub DELETE. Podobnie przyjęto założenie, że instrukcja UPDATE lub DELETE nie będzie współdziałać z inną instrukcją UPDATE lub DELETE. To zabezpieczenie oznacza, że aktualizacje i usunięcia wymagają przeskalowania (Citus) w celu uzyskania silniejszych blokad.

Jeśli masz instrukcje UPDATE, które są komutatywna przy użyciu operacji wstawiania lub innych aktualizacji, możesz złagodzić te założenia przemienności, ustawiając dla tego parametru wartość true. Jeśli ten parametr ma wartość true, wszystkie polecenia są uważane za komutatywna i są traktowane jako blokada udostępniona, co może poprawić ogólną przepływność. Ten parametr można ustawić w czasie wykonywania i obowiązuje w przypadku koordynatora.

#### <a name="citusremote_task_check_interval-integer"></a>\_Interwał sprawdzania zadań zdalnych Citus. \_ \_ (liczba całkowita)

Ustawia częstotliwość, z jaką funkcja Citus) sprawdza stan zadań zarządzanych przez program wykonujący śledzenie zadań. Wartość domyślna to 10 ms. Koordynator przypisuje zadania do pracowników, a następnie regularnie sprawdza je o \' postępie każdego zadania s. Ta wartość konfiguracji Ustawia przedział czasu między dwoma sprawdzeniami. Ten parametr obowiązuje dla koordynatora i może być ustawiony w czasie wykonywania.

#### <a name="citustask_executor_type-enum"></a>Citus. Task \_ — \_ Typ wykonawcy (enum)

Funkcja do skalowania (Citus) ma trzy typy wykonawcze do uruchamiania rozproszonego zapytania SELECT.  Żądany wykonawca można wybrać przez ustawienie tego parametru konfiguracji. Akceptowane wartości tego parametru to:

-   **adaptacyjne:** Wartość domyślna. Jest optymalny dla szybkich odpowiedzi na zapytania, które obejmują agregacje i współzlokalizowane łącza obejmujące wiele fragmentów.
-   **zadanie-śledzenie:** Program wykonujący zadania śledzenia jest dobrze dostosowany do długotrwałych, złożonych zapytań wymagających Shuffling danych między węzłami procesów roboczych i wydajnym zarządzaniem zasobami.
-   **czas rzeczywisty:** (przestarzały) służy do podobnego celu, co w przypadku wykonawcy adaptacyjnego, ale jest mniej elastyczny i może spowodować zwiększenie naciskania połączenia w węzłach procesu roboczego.

Ten parametr można ustawić w czasie wykonywania i obowiązuje w przypadku koordynatora.

#### <a name="citusmulti_task_query_log_level-enum-multi_task_logging"></a>Citus. wiele \_ zapytań o zadania \_ \_ \_ poziomu dziennika (enum) {#multi_task_logging}

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

#### <a name="citusenable_repartition_joins-boolean"></a>Citus. Włącz \_ sprzężenia ponownej partycji \_ (wartość logiczna)

Zwykle próba przełączenia ponownej partycji z wykonawcą adaptacyjnym zakończy się niepowodzeniem z komunikatem o błędzie.  Ustawienie `citus.enable_repartition_joins` wartości true umożliwia przełączenie (Citus) w celu tymczasowego przejścia do programu wykonującego śledzenie zadań w celu wykonania sprzężenia.  Wartość domyślna to false.

### <a name="task-tracker-executor-configuration"></a>Konfiguracja wykonawcy monitora zadań

#### <a name="citustask_tracker_delay-integer"></a>Citus. \_ opóźnienie śledzenia zadań \_ (liczba całkowita)

Ten parametr ustawia czas uśpienia śledzenia zadań między wartościami zaokrąglania i domyślnie do 200 ms. Proces śledzenia zadań regularnie wznawia działanie, przegląda wszystkie zadania przypisane do niego, a następnie planuje i wykonuje te zadania.  Następnie śledzenie zadań zostanie wstrzymane przez czas przed ponownym przekroczeniem tych zadań.
Ta wartość konfiguracji określa długość tego okresu uśpienia. Ten parametr jest skuteczny dla procesów roboczych i należy go zmienić w pliku PostgreSQL. conf. Po przeprowadzeniu edycji pliku konfiguracji użytkownicy mogą wysłać sygnał SIGHUP lub ponownie uruchomić serwer, aby zmiany zaczęły obowiązywać.

Ten parametr można zmniejszyć, aby przyciąć opóźnienie spowodowane przez program wykonujący śledzenie zadań przez skrócenie przerwy w czasie między zaokrągleniami zarządzania.
Zmniejszenie opóźnienia jest przydatne w przypadkach, gdy zapytania fragmentu są krótkie i w związku z tym regularnie aktualizują stan.

#### <a name="citusmax_assign_task_batch_size-integer"></a>Citus. Max \_ — \_ \_ Rozmiar partii zadań \_ (liczba całkowita)

Program wykonujący śledzenie zadań synchronicznie przypisuje zadania w partiach do demona w ramach procesów roboczych. Ten parametr ustawia maksymalną liczbę zadań do przypisania w ramach pojedynczej partii. Wybranie większego rozmiaru partii umożliwia przyspieszenie przypisywania zadań. Jeśli jednak liczba procesów roboczych jest duża, pobieranie zadań może trwać dłużej.  Ten parametr można ustawić w czasie wykonywania i obowiązuje w przypadku koordynatora.

#### <a name="citusmax_running_tasks_per_node-integer"></a>Citus. Maksymalna liczba \_ uruchomionych \_ zadań \_ na \_ węzeł (liczba całkowita)

Proces śledzenia zadań planuje i wykonuje zadania przypisane do niego zgodnie z potrzebami. Ta wartość konfiguracji ustawia maksymalną liczbę zadań, które mają być wykonywane współbieżnie w jednym węźle w danym momencie, a wartością domyślną jest 8.

Ograniczenie gwarantuje, że \' nie masz wielu zadań jednocześnie, co pozwala uniknąć rywalizacji o operacje we/wy dysku. Jeśli zapytania są obsługiwane z pamięci lub dysków SSD, można zwiększyć maksymalną liczbę \_ uruchomionych \_ zadań \_ na \_ węzeł bez względu na wiele obaw.

#### <a name="cituspartition_buffer_size-integer"></a>Citus. \_ rozmiar buforu partycji \_ (liczba całkowita)

Ustawia rozmiar buforu, który ma być używany dla operacji partycji i domyślnie wynosi 8 MB.
Funkcja CreatePartition (Citus) umożliwia ponowne Partycjonowanie danych tabeli w wielu plikach, gdy są sprzężone dwie duże tabele. Po wypełnieniu tego buforu partycji dane z podziałem na partycje są opróżniane do plików na dysku.  Ten wpis konfiguracji można ustawić w czasie wykonywania i jest skuteczny dla pracowników.

### <a name="explain-output"></a>Wyjaśnij dane wyjściowe

#### <a name="citusexplain_all_tasks-boolean"></a>Citus. Wyjaśnij \_ wszystkie \_ zadania (wartość logiczna)

Domyślnie funkcja ze skalowaniem (Citus) pokazuje dane wyjściowe pojedynczego, dowolnego zadania, gdy wykonywane jest [wyjaśnienie](http://www.postgresql.org/docs/current/static/sql-explain.html) zapytania rozproszonego. W większości przypadków dane wyjściowe wyjaśnień będą podobne do poszczególnych zadań. Czasami niektóre zadania będą planowane inaczej lub mają znacznie wyższy czas wykonania. W takich przypadkach może być przydatne włączenie tego parametru, po upływie którego dane wyjściowe OBJAŚNIAjące będą obejmować wszystkie zadania. Wyjaśnienie wszystkich zadań może spowodować wydłużenie tego czasu.

## <a name="next-steps"></a>Następne kroki

* Inną formą konfiguracji, oprócz parametrów serwera, są [Opcje konfiguracji](concepts-hyperscale-configuration-options.md) zasobów w grupie serwerów Citus.
* Bazowa baza danych PostgreSQL ma także [Parametry konfiguracji](http://www.postgresql.org/docs/current/static/runtime-config.html).
