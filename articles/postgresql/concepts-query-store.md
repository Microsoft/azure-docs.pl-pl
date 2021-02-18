---
title: Magazyn zapytań — Azure Database for PostgreSQL — pojedynczy serwer
description: W tym artykule opisano funkcję magazynu zapytań w Azure Database for PostgreSQL-pojedynczym serwerze.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: conceptual
ms.date: 07/01/2020
ms.openlocfilehash: 1779df1c5f9baf2aa46ff809ecae9ec5e3cd7adb
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100581562"
---
# <a name="monitor-performance-with-the-query-store"></a>Monitorowanie wydajności za pomocą magazynu zapytań

**Dotyczy:** Azure Database for PostgreSQL — jeden serwer w wersji 9,6 i nowszej

Funkcja magazynu zapytań w Azure Database for PostgreSQL zapewnia sposób śledzenia wydajności zapytań w miarę upływu czasu. Magazyn zapytań upraszcza Rozwiązywanie problemów z wydajnością, pomagając szybko znaleźć najdłuższych uruchomionych i większości zapytań intensywnie korzystających z zasobów. Magazyn zapytań automatycznie przechwytuje historię zapytań i statystyk środowiska uruchomieniowego i zachowuje je do przeglądu. Oddziela ona dane według czasu, dzięki czemu można zobaczyć wzorce użycia bazy danych. Dane dla wszystkich użytkowników, baz danych i zapytań są przechowywane w bazie danych o nazwie **azure_sys** w wystąpieniu Azure Database for PostgreSQL.

> [!IMPORTANT]
> Nie należy modyfikować bazy danych **azure_sys** ani jej schematów. Wykonanie tej czynności uniemożliwi poprawne działanie magazynu zapytań i pokrewnych funkcji wydajności.

## <a name="enabling-query-store"></a>Włączanie magazynu zapytań
Magazyn zapytań jest funkcją wyboru, dlatego nie jest domyślnie aktywna na serwerze. Magazyn jest włączony lub wyłączony globalnie dla wszystkich baz danych na danym serwerze i nie można go włączać ani wyłączać na bazę danych.

### <a name="enable-query-store-using-the-azure-portal"></a>Włącz magazyn zapytań przy użyciu Azure Portal
1. Zaloguj się do Azure Portal i wybierz swój serwer Azure Database for PostgreSQL.
2. Wybierz opcję **parametry serwera** w sekcji **Ustawienia** w menu.
3. Wyszukaj `pg_qs.query_capture_mode` parametr.
4. Ustaw wartość na `TOP` i **Zapisz**.

Aby włączyć statystykę oczekiwania w magazynie zapytań: 
1. Wyszukaj `pgms_wait_sampling.query_capture_mode` parametr.
1. Ustaw wartość na `ALL` i **Zapisz**.


Alternatywnie możesz ustawić te parametry za pomocą interfejsu wiersza polecenia platformy Azure.
```azurecli-interactive
az postgres server configuration set --name pg_qs.query_capture_mode --resource-group myresourcegroup --server mydemoserver --value TOP
az postgres server configuration set --name pgms_wait_sampling.query_capture_mode --resource-group myresourcegroup --server mydemoserver --value ALL
```

Poczekaj do 20 minut na utrwalenie pierwszej partii danych w bazie danych azure_sys.

## <a name="information-in-query-store"></a>Informacje w magazynie zapytań
Magazyn zapytań ma dwa magazyny:
- Magazyn statystyk środowiska uruchomieniowego na potrzeby utrwalania informacji statystycznych wykonywania zapytania.
- Magazyn statystyk oczekiwania na utrwalanie informacji statystycznych oczekiwania.

Typowe scenariusze korzystania z magazynu zapytań obejmują:
- Określanie liczby przypadków wykonania zapytania w danym przedziale czasu
- Porównanie średniego czasu wykonywania zapytania w oknach czasu, aby zobaczyć duże różnice
- Identyfikowanie najdłuższych uruchomionych zapytań w ciągu ostatnich X godzin
- Identyfikowanie pierwszych N zapytań, które oczekują na zasoby
- Zrozumienie charakteru oczekiwania dla konkretnego zapytania

Aby zminimalizować użycie miejsca, statystyki wykonywania środowiska uruchomieniowego w magazynie statystyk środowiska uruchomieniowego są agregowane w ustalonym, konfigurowalnym przedziale czasu. Informacje w tych sklepach są widoczne przez zapytanie dotyczące widoków magazynu zapytań.

## <a name="access-query-store-information"></a>Informacje o magazynie zapytań dostępu

Dane magazynu zapytań są przechowywane w bazie danych azure_sys na serwerze Postgres. 

Następujące zapytanie zwraca informacje o zapytaniach w magazynie zapytań:
```sql
SELECT * FROM query_store.qs_view; 
``` 

Lub to zapytanie dotyczące statystyk oczekiwania:
```sql
SELECT * FROM query_store.pgms_wait_sampling_view;
```

## <a name="finding-wait-queries"></a>Znajdowanie zapytań oczekujących
Typy zdarzeń oczekiwania łączą różne zdarzenia oczekiwania do zasobników według podobieństwa. Magazyn zapytań zawiera typ zdarzenia oczekiwania, konkretną nazwę zdarzenia oczekiwania i zapytanie, którego dotyczy. Aby skorelować te informacje o poczekaniu z statystykami środowiska uruchomieniowego zapytań, można lepiej zrozumieć, co przyczynia się do charakterystyki wydajności zapytań.

Poniżej przedstawiono kilka przykładów, w których można uzyskać więcej szczegółowych informacji na temat obciążenia przy użyciu statystyk oczekiwania w magazynie zapytań:

| **Uchwyceni** | **Akcja** |
|---|---|
|Duża blokada | Sprawdź teksty zapytania dla zaatakowanych zapytań i zidentyfikuj jednostki docelowe. Wyszukaj inne zapytania w magazynie zapytań, modyfikując tę samą jednostkę, która jest wykonywana często i/lub o dużym czasie trwania. Po zidentyfikowaniu tych zapytań Rozważ zmianę logiki aplikacji, aby zwiększyć współbieżność, lub użyj mniej restrykcyjnego poziomu izolacji.|
| Duże oczekiwania we/wy | Znajdź zapytania z dużą liczbą odczytów fizycznych w magazynie zapytań. Jeśli są one zgodne z zapytaniami o wysokim poziomie operacji we/wy, rozważ wprowadzenie indeksu w jednostce źródłowej w celu przeszukania zamiast skanów. Zmniejsza to obciążenie operacji we/wy dla zapytań. Sprawdź **zalecenia dotyczące wydajności** serwera w portalu, aby sprawdzić, czy istnieją zalecenia dotyczące indeksów dla tego serwera, które optymalizują zapytania.|
| Duża ilość pamięci | Znajdź zapytania zużywające najwięcej pamięci w magazynie zapytań. Te zapytania prawdopodobnie opóźnią dalsze postępy zapytań, których to dotyczy. Sprawdź **zalecenia dotyczące wydajności** serwera w portalu, aby sprawdzić, czy istnieją zalecenia dotyczące indeksów, które optymalizują te zapytania.|

## <a name="configuration-options"></a>Opcje konfiguracji
Po włączeniu magazynu zapytań dane są zapisywane w 15-minutowych oknach agregacji, do 500 oddzielnych zapytań dla każdego okna. 

Następujące opcje są dostępne na potrzeby konfigurowania parametrów magazynu zapytań.

| **Parametr** | **Opis** | **Wartooć** | **Zakres**|
|---|---|---|---|
| pg_qs pg_qs.query_capture_mode | Ustawia, które instrukcje są śledzone. | brak | Brak, Góra, wszystkie |
| pg_qs pg_qs.max_query_text_length | Ustawia maksymalną długość zapytania, którą można zapisać. Dłuższe zapytania zostaną obcięte. | 6000 | 100 – 10 tys. |
| pg_qs pg_qs.retention_period_in_days | Ustawia okres przechowywania. | 7 | 1 - 30 |
| pg_qs pg_qs.track_utility | Określa, czy polecenia narzędzi są śledzone | on | włączone, wyłączone |

Poniższe opcje są stosowane w odniesieniu do statystyk oczekiwania.

| **Parametr** | **Opis** | **Wartooć** | **Zakres**|
|---|---|---|---|
| pgms_wait_sampling pgms_wait_sampling.query_capture_mode | Ustawia, które instrukcje są śledzone pod kątem statystyk oczekiwania. | brak | Brak, wszystkie|
| Pgms_wait_sampling Pgms_wait_sampling.history_period | Ustaw częstotliwość próbkowania zdarzeń oczekiwania (w milisekundach). | 100 | 1-600000 |

> [!NOTE] 
> **pg_qs. query_capture_mode** zastępuje **pgms_wait_sampling. query_capture_mode**. Jeśli pg_qs. query_capture_mode ma wartość NONE, ustawienie pgms_wait_sampling. query_capture_mode nie ma żadnego efektu.


Użyj [Azure Portal](howto-configure-server-parameters-using-portal.md) lub [interfejsu wiersza polecenia platformy Azure](howto-configure-server-parameters-using-cli.md) , aby uzyskać lub ustawić inną wartość dla parametru.

## <a name="views-and-functions"></a>Widoki i funkcje
Wyświetlanie magazynu zapytań i zarządzanie nim przy użyciu następujących widoków i funkcji. Każda osoba w publicznej roli PostgreSQL może używać tych widoków do wyświetlania danych w magazynie zapytań. Te widoki są dostępne tylko w bazie danych **azure_sys** .

Zapytania są znormalizowane przez przejrzenie ich struktury po usunięciu literałów i stałych. Jeśli dwa zapytania są identyczne z wyjątkiem wartości literału, będą miały ten sam skrót.

### <a name="query_storeqs_view"></a>query_store query_store.qs_view
Ten widok zwraca wszystkie dane w magazynie zapytań. Dla każdego unikatowego identyfikatora bazy danych, identyfikatora użytkownika i identyfikatora zapytania istnieje jeden wiersz. 

|**Nazwa**   |**Typ** | **Odwołania**  | **Opis**|
|---|---|---|---|
|runtime_stats_entry_id |bigint | | Identyfikator z tabeli runtime_stats_entries|
|user_id    |OID    |pg_authid. OID  |Identyfikator OID użytkownika, który wykonał instrukcję|
|db_id  |OID    |pg_database. OID    |Identyfikator OID bazy danych, w której zostało wykonane wykonywanie instrukcji|
|query_id   |bigint  || Wewnętrzny kod skrótu obliczony na podstawie drzewa analizy instrukcji|
|query_sql_text |Varchar (10000)  || Tekst deklaracji reprezentatywnej. Różne zapytania o tej samej strukturze są klastrowane ze sobą. Ten tekst jest tekstem dla pierwszych zapytań w klastrze.|
|plan_id    |bigint |   |Identyfikator planu odpowiadającego temu zapytaniem, nie jest jeszcze dostępny|
|start_time |sygnatura czasowa  ||  Zapytania są agregowane według przedziałów czasu — domyślnie jest to 15 minut. Jest to godzina rozpoczęcia odpowiadająca przedziale czasu dla tego wpisu.|
|end_time   |sygnatura czasowa  ||  Godzina zakończenia odpowiadająca przedziale czasu dla tego wpisu.|
|Rozmowa  |bigint  || Liczba wykonanych zapytań|
|total_time |Podwójna precyzja   ||  Łączny czas wykonywania zapytania (w milisekundach)|
|min_time   |Podwójna precyzja   ||  Minimalny czas wykonywania zapytania (w milisekundach)|
|max_time   |Podwójna precyzja   ||  Maksymalny czas wykonywania zapytania (w milisekundach)|
|mean_time  |Podwójna precyzja   ||  Średni czas wykonywania zapytania (w milisekundach)|
|stddev_time|   Podwójna precyzja    ||  Odchylenie standardowe czasu wykonywania zapytania (w milisekundach) |
|wierszy   |bigint ||  Łączna liczba wierszy pobranych lub dotkniętych przez instrukcję|
|shared_blks_hit|   bigint  ||  Łączna liczba trafień w pamięci podręcznej bloków udostępnionych przez instrukcję|
|shared_blks_read|  bigint  ||  Łączna liczba bloków udostępnionych odczytywanych przez instrukcję|
|shared_blks_dirtied|   bigint   || Łączna liczba udostępnionych bloków 'dirtied przez instrukcję |
|shared_blks_written|   bigint  ||  Łączna liczba bloków udostępnionych pisanych przez instrukcję|
|local_blks_hit|    bigint ||   Łączna liczba trafień w pamięci podręcznej bloków lokalnych przez instrukcję|
|local_blks_read|   bigint   || Łączna liczba lokalnych bloków odczytywanych przez instrukcję|
|local_blks_dirtied|    bigint  ||  Łączna liczba lokalnych bloków 'dirtied przez instrukcję|
|local_blks_written|    bigint  ||  Łączna liczba bloków lokalnych pisanych przez instrukcję|
|temp_blks_read |bigint  || Łączna liczba bloków tymczasowych odczytywanych przez instrukcję|
|temp_blks_written| bigint   || Łączna liczba bloków tymczasowych pisanych przez instrukcję|
|blk_read_time  |Podwójna precyzja    || Łączny czas trwania instrukcji odczytywania bloków w milisekundach (jeśli track_io_timing jest włączona, w przeciwnym razie zero)|
|blk_write_time |Podwójna precyzja    || Łączny czas oczekiwania instrukcji na zapis bloków w milisekundach (jeśli track_io_timing jest włączona, w przeciwnym razie zero)|
    
### <a name="query_storequery_texts_view"></a>query_store query_store.query_texts_view
Ten widok zwraca dane tekstu zapytania w magazynie zapytań. Dla każdego oddzielnego query_text istnieje jeden wiersz.

| **Nazwa** | **Typ** | **Opis** |
|--|--|--|
| query_text_id | bigint | Identyfikator tabeli query_texts |
| query_sql_text | Varchar (10000) | Tekst deklaracji reprezentatywnej. Różne zapytania o tej samej strukturze są klastrowane ze sobą. Ten tekst jest tekstem dla pierwszych zapytań w klastrze. |

### <a name="query_storepgms_wait_sampling_view"></a>query_store query_store.pgms_wait_sampling_view
Ten widok zwraca dane zdarzeń oczekiwania w magazynie zapytań. Istnieje jeden wiersz dla każdego identyfikatora bazy danych, identyfikatora użytkownika, identyfikatora zapytania i zdarzenia.

| **Nazwa** | **Typ** | **Odwołania** | **Opis** |
|--|--|--|--|
| user_id | OID | pg_authid. OID | Identyfikator OID użytkownika, który wykonał instrukcję |
| db_id | OID | pg_database. OID | Identyfikator OID bazy danych, w której zostało wykonane wykonywanie instrukcji |
| query_id | bigint |  | Wewnętrzny kod skrótu obliczony na podstawie drzewa analizy instrukcji |
| event_type | tekst |  | Typ zdarzenia, dla którego zaplecze oczekuje |
| event | tekst |  | Nazwa zdarzenia oczekiwania, jeśli obecnie trwa oczekiwanie na zaplecze |
| Rozmowa | Liczba całkowita |  | Liczba przechwyconych zdarzeń |

### <a name="functions"></a>Funkcje

Query_store Query_store.qs_reset () zwraca wartość void

`qs_reset` odrzuca wszystkie dane statystyczne zebrane do tej pory przez magazyn zapytań. Tę funkcję można wykonać tylko przez rolę administratora serwera.

Query_store Query_store.staging_data_reset () zwraca wartość void

`staging_data_reset` odrzuca wszystkie dane statystyczne zebrane w pamięci przez magazyn zapytań (czyli dane w pamięci, które nie zostały jeszcze opróżnione do bazy danych). Tę funkcję można wykonać tylko przez rolę administratora serwera.


## <a name="azure-monitor"></a>Azure Monitor
Azure Database for PostgreSQL jest zintegrowany z [Azure monitor ustawień diagnostycznych](../azure-monitor/essentials/diagnostic-settings.md). Ustawienia diagnostyczne umożliwiają wysyłanie dzienników Postgres w formacie JSON do [Azure monitor dzienników](../azure-monitor/logs/log-query-overview.md) na potrzeby analiz i alertów, Event Hubs do przesyłania strumieniowego i usługi Azure Storage w celu archiwizacji.

>[!IMPORTANT]
> Ta funkcja diagnostyczna dla programu jest dostępna tylko w warstwach cenowych Ogólnego przeznaczenia i zoptymalizowanych pod kątem pamięci.

### <a name="configure-diagnostic-settings"></a>Konfigurowanie ustawień diagnostycznych
Ustawienia diagnostyczne dla serwera Postgres można włączyć za pomocą Azure Portal, interfejsu wiersza polecenia i środowiska API REST oraz programu PowerShell. Kategorie dzienników do skonfigurowania to **QueryStoreRuntimeStatistics** i **QueryStoreWaitStatistics**. 

Aby włączyć dzienniki zasobów przy użyciu Azure Portal:

1. W portalu przejdź do pozycji Ustawienia diagnostyczne w menu nawigacji serwera Postgres.
2. Wybierz pozycję Dodaj ustawienie diagnostyczne.
3. Nadaj nazwę temu ustawieniu.
4. Wybierz preferowany punkt końcowy (konto magazynu, centrum zdarzeń, Analiza dzienników).
5. Wybierz typy dzienników **QueryStoreRuntimeStatistics** i **QueryStoreWaitStatistics**.
6. Zapisz ustawienie.

Aby włączyć to ustawienie za pomocą programu PowerShell, interfejsu wiersza polecenia lub API REST, zapoznaj się z [artykułem ustawienia diagnostyczne](../azure-monitor/essentials/diagnostic-settings.md).

### <a name="json-log-format"></a>Format dziennika JSON
W poniższych tabelach opisano pola dla dwóch typów dzienników. W zależności od wybranego wyjściowego punktu końcowego pola zawarte i kolejność ich wyświetlania mogą się różnić.

#### <a name="querystoreruntimestatistics"></a>QueryStoreRuntimeStatistics
|**Pole** | **Opis** |
|---|---|
| TimeGenerated [UTC] | Sygnatura czasowa, gdy dziennik został zarejestrowany w formacie UTC |
| ResourceId | Identyfikator URI zasobu platformy Azure serwera Postgres |
| Kategoria | `QueryStoreRuntimeStatistics` |
| OperationName | `QueryStoreRuntimeStatisticsEvent` |
| LogicalServerName_s | Nazwa serwera Postgres | 
| runtime_stats_entry_id_s | Identyfikator z tabeli runtime_stats_entries |
| user_id_s | Identyfikator OID użytkownika, który wykonał instrukcję |
| db_id_s | Identyfikator OID bazy danych, w której zostało wykonane wykonywanie instrukcji |
| query_id_s | Wewnętrzny kod skrótu obliczony na podstawie drzewa analizy instrukcji |
| end_time_s | Godzina zakończenia odpowiadająca przedziale czasu dla tego wpisu |
| calls_s | Liczba wykonanych zapytań |
| total_time_s | Łączny czas wykonywania zapytania (w milisekundach) |
| min_time_s | Minimalny czas wykonywania zapytania (w milisekundach) |
| max_time_s | Maksymalny czas wykonywania zapytania (w milisekundach) |
| mean_time_s | Średni czas wykonywania zapytania (w milisekundach) |
| ResourceGroup | Grupa zasobów | 
| SubscriptionId | Identyfikator Twojej subskrypcji |
| ResourceProvider | `Microsoft.DBForPostgreSQL` | 
| Zasób | Nazwa serwera Postgres |
| ResourceType | `Servers` | 


#### <a name="querystorewaitstatistics"></a>QueryStoreWaitStatistics
|**Pole** | **Opis** |
|---|---|
| TimeGenerated [UTC] | Sygnatura czasowa, gdy dziennik został zarejestrowany w formacie UTC |
| ResourceId | Identyfikator URI zasobu platformy Azure serwera Postgres |
| Kategoria | `QueryStoreWaitStatistics` |
| OperationName | `QueryStoreWaitEvent` |
| user_id_s | Identyfikator OID użytkownika, który wykonał instrukcję |
| db_id_s | Identyfikator OID bazy danych, w której zostało wykonane wykonywanie instrukcji |
| query_id_s | Wewnętrzny kod skrótu zapytania |
| calls_s | Liczba przechwyconych zdarzeń |
| event_type_s | Typ zdarzenia, dla którego zaplecze oczekuje |
| event_s | Nazwa zdarzenia oczekiwania, jeśli zaplecze aktualnie oczekuje |
| start_time_t | Godzina rozpoczęcia zdarzenia |
| end_time_s | Godzina zakończenia zdarzenia | 
| LogicalServerName_s | Nazwa serwera Postgres | 
| ResourceGroup | Grupa zasobów | 
| SubscriptionId | Identyfikator Twojej subskrypcji |
| ResourceProvider | `Microsoft.DBForPostgreSQL` | 
| Zasób | Nazwa serwera Postgres |
| ResourceType | `Servers` | 

## <a name="limitations-and-known-issues"></a>Ograniczenia i znane problemy
- Jeśli serwer PostgreSQL ma parametr default_transaction_read_only on, magazyn zapytań nie może przechwycić danych.
- Funkcja magazynu zapytań może zostać przerwana, jeśli napotka długie zapytania Unicode (>= 6000 bajtów).
- [Odczyt replik](concepts-read-replicas.md) replikuje dane z magazynu zapytań z serwera podstawowego. Oznacza to, że magazyn zapytań odczytu repliki nie dostarcza statystyk dotyczących zapytań uruchomionych w replice odczytu.


## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej na temat scenariuszy, w [których magazyn zapytań może być szczególnie przydatny](concepts-query-store-scenarios.md).
- Dowiedz się więcej o [najlepszych rozwiązaniach dotyczących korzystania z magazynu zapytań](concepts-query-store-best-practices.md).
