---
title: Magazyn zapytań — Azure Database for MariaDB
description: Dowiedz się więcej o funkcji magazynu zapytań w Azure Database for MariaDB, aby ułatwić śledzenie wydajności w miarę upływu czasu.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: conceptual
ms.date: 01/15/2021
ms.openlocfilehash: 164285b1fea3dce18161066e643aa165e47cc496
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98664201"
---
# <a name="monitor-azure-database-for-mariadb-performance-with-query-store"></a>Monitorowanie wydajności Azure Database for MariaDB przy użyciu magazynu zapytań

**Dotyczy:** Azure Database for MariaDB 10,2

Funkcja magazynu zapytań w usłudze Azure Database for MariaDB umożliwia śledzenie wydajności zapytań w miarę upływu czasu. Magazyn zapytań upraszcza Rozwiązywanie problemów z wydajnością, pomagając szybko znaleźć najdłuższych uruchomionych i większości zapytań intensywnie korzystających z zasobów. Magazyn zapytań automatycznie przechwytuje historię zapytań i statystyk środowiska uruchomieniowego i zachowuje je do przeglądu. Oddziela ona dane według czasu, dzięki czemu można zobaczyć wzorce użycia bazy danych. Dane dla wszystkich użytkowników, baz danych i zapytań są przechowywane w bazie danych schematu **MySQL** w wystąpieniu Azure Database for MariaDB.

## <a name="common-scenarios-for-using-query-store"></a>Typowe scenariusze korzystania z magazynu zapytań

Magazynu zapytań można używać w wielu scenariuszach, takich jak następujące:

- Wykrywanie zapytań uległa pogorszeniu
- Określanie liczby przypadków wykonania zapytania w danym przedziale czasu
- Porównanie średniego czasu wykonywania zapytania w oknach czasu, aby zobaczyć duże różnice

## <a name="enabling-query-store"></a>Włączanie magazynu zapytań

Magazyn zapytań jest funkcją wyboru, dlatego nie jest domyślnie aktywna na serwerze. Magazyn zapytań jest włączony lub wyłączony globalnie dla wszystkich baz danych na danym serwerze i nie można go włączyć ani wyłączyć na bazę danych.

### <a name="enable-query-store-using-the-azure-portal"></a>Włącz magazyn zapytań przy użyciu Azure Portal

1. Zaloguj się do Azure Portal i wybierz swój serwer Azure Database for MariaDB.
2. Wybierz opcję **parametry serwera** w sekcji **Ustawienia** w menu.
3. Wyszukaj parametr query_store_capture_mode.
4. Ustaw wartość wszystkie i **Zapisz**.

Aby włączyć statystykę oczekiwania w magazynie zapytań:

1. Wyszukaj parametr query_store_wait_sampling_capture_mode.
2. Ustaw wartość wszystkie i **Zapisz**.

Zaczekaj do 20 minut, aż pierwsza partia danych ma pozostać w bazie danych MySQL.

## <a name="information-in-query-store"></a>Informacje w magazynie zapytań

Magazyn zapytań ma dwa magazyny:

- Magazyn statystyk środowiska uruchomieniowego na potrzeby utrwalania informacji statystycznych wykonywania zapytania.
- Magazyn statystyk oczekiwania dla utrwalania informacji o statystyce oczekiwania.

Aby zminimalizować użycie miejsca, statystyki wykonywania środowiska uruchomieniowego w magazynie statystyk środowiska uruchomieniowego są agregowane w ustalonym, konfigurowalnym przedziale czasu. Informacje w tych sklepach są widoczne przez zapytanie dotyczące widoków magazynu zapytań.

Następujące zapytanie zwraca informacje o zapytaniach w magazynie zapytań:

```sql
SELECT * FROM mysql.query_store;
```

Lub to zapytanie dotyczące statystyk oczekiwania:

```sql
SELECT * FROM mysql.query_store_wait_stats;
```

## <a name="finding-wait-queries"></a>Znajdowanie zapytań oczekujących

> [!NOTE]
> Statystykę oczekiwania nie należy włączać w godzinach szczytowego obciążenia lub być wyłączone w nieskończoność w przypadku cennych obciążeń. <br>W przypadku obciążeń z wysokim użyciem procesora CPU lub na serwerach skonfigurowanych przy użyciu mniejszej rdzeni wirtualnych należy zachować ostrożność podczas włączania statystyk oczekiwania. Nie powinna być włączona w nieskończoność. 

Typy zdarzeń oczekiwania łączą różne zdarzenia oczekiwania do zasobników według podobieństwa. Magazyn zapytań zawiera typ zdarzenia oczekiwania, konkretną nazwę zdarzenia oczekiwania i zapytanie, którego dotyczy. Aby skorelować te informacje o poczekaniu z statystykami środowiska uruchomieniowego zapytań, można lepiej zrozumieć, co przyczynia się do charakterystyki wydajności zapytań.

Poniżej przedstawiono kilka przykładów, w których można uzyskać więcej szczegółowych informacji na temat obciążenia przy użyciu statystyk oczekiwania w magazynie zapytań:

| **Uchwyceni** | **Akcja** |
|---|---|
|Duża blokada | Sprawdź teksty zapytania dla zaatakowanych zapytań i zidentyfikuj jednostki docelowe. Wyszukaj inne zapytania w magazynie zapytań, modyfikując tę samą jednostkę, która jest wykonywana często i/lub o dużym czasie trwania. Po zidentyfikowaniu tych zapytań Rozważ zmianę logiki aplikacji, aby zwiększyć współbieżność, lub użyj mniej restrykcyjnego poziomu izolacji. |
|Duże oczekiwania we/wy | Znajdź zapytania z dużą liczbą odczytów fizycznych w magazynie zapytań. Jeśli są one zgodne z kwerendami o wysokim poziomie operacji we/wy, rozważ wprowadzenie indeksu w jednostce źródłowej, aby przeszukiwać zamiast skanów. Zmniejsza to obciążenie operacji we/wy dla zapytań. Sprawdź **zalecenia dotyczące wydajności** serwera w portalu, aby sprawdzić, czy istnieją zalecenia dotyczące indeksów dla tego serwera, które optymalizują zapytania. |
|Duża ilość pamięci | Znajdź zapytania zużywające najwięcej pamięci w magazynie zapytań. Te zapytania prawdopodobnie opóźnią dalsze postępy zapytań, których to dotyczy. Sprawdź **zalecenia dotyczące wydajności** serwera w portalu, aby sprawdzić, czy istnieją zalecenia dotyczące indeksów, które optymalizują te zapytania.|

## <a name="configuration-options"></a>Opcje konfiguracji

Po włączeniu magazynu zapytań dane są zapisywane w 15-minutowych oknach agregacji, do 500 oddzielnych zapytań dla każdego okna.

Następujące opcje są dostępne na potrzeby konfigurowania parametrów magazynu zapytań.

| **Parametr** | **Opis** | **Wartooć** | **Zakres** |
|---|---|---|---|
| query_store_capture_mode | Włącz/Wyłącz funkcję magazynu zapytań na podstawie wartości. Uwaga: Jeśli performance_schema jest wyłączona, włączenie query_store_capture_mode spowoduje włączenie performance_schema i podzestawu narzędzi schematu wydajności wymaganych dla tej funkcji. | ALL | BRAK, WSZYSTKIE |
| query_store_capture_interval | Interwał przechwytywania magazynu zapytań (w minutach). Umożliwia określenie interwału, w którym metryki zapytania są agregowane | 15 | 5 - 60 |
| query_store_capture_utility_queries | Włączenie lub wyłączenie w celu przechwycenia wszystkich zapytań narzędziowych wykonywanych w systemie. | NO | TAK, NIE |
| query_store_retention_period_in_days | Przedział czasu w dniach, aby zachować dane w magazynie zapytań. | 7 | 1 - 30 |

Poniższe opcje są stosowane w odniesieniu do statystyk oczekiwania.

| **Parametr** | **Opis** | **Wartooć** | **Zakres** |
|---|---|---|---|
| query_store_wait_sampling_capture_mode | Umożliwia włączenie/wyłączenie statystyk oczekiwania. | DAWAJ | BRAK, WSZYSTKIE |
| query_store_wait_sampling_frequency | Zmienia częstotliwość próbkowania oczekiwania w sekundach. od 5 do 300 sekund. | 30 | 5-300 |

> [!NOTE]
> Obecnie **query_store_capture_mode** zastępuje tę konfigurację, co oznacza, że oba **query_store_capture_mode** i **QUERY_STORE_WAIT_SAMPLING_CAPTURE_MODE** muszą być włączone dla wszystkich, aby statystyki oczekiwania mogły działać. Jeśli **query_store_capture_mode** jest wyłączone, a następnie zaczekaj, statystyki są również wyłączone, ponieważ statystyki oczekiwania wykorzystują performance_schema włączone i query_text przechwycone przez magazyn zapytań.

Użyj [Azure Portal](howto-server-parameters.md) , aby uzyskać lub ustawić inną wartość dla parametru.

## <a name="views-and-functions"></a>Widoki i funkcje

Wyświetlanie magazynu zapytań i zarządzanie nim przy użyciu następujących widoków i funkcji. Każda osoba w [roli publicznej Wybieranie uprawnienia](howto-create-users.md#create-more-admin-users) może używać tych widoków do wyświetlania danych w magazynie zapytań. Te widoki są dostępne tylko w bazie danych **MySQL** .

Zapytania są znormalizowane przez przejrzenie ich struktury po usunięciu literałów i stałych. Jeśli dwa zapytania są identyczne z wyjątkiem wartości literału, będą miały ten sam skrót.

### <a name="mysqlquery_store"></a>mysql.query_store

Ten widok zwraca wszystkie dane w magazynie zapytań. Dla każdego unikatowego identyfikatora bazy danych, identyfikatora użytkownika i identyfikatora zapytania istnieje jeden wiersz.

| **Nazwa** | **Typ danych** | **IS_NULLABLE** | **Opis** |
|---|---|---|---|
| `schema_name`| varchar (64) | NO | Nazwa schematu |
| `query_id`| bigint (20) | NO| Unikatowy identyfikator wygenerowany dla konkretnego zapytania, jeśli to samo zapytanie jest wykonywane w innym schemacie, zostanie wygenerowany nowy identyfikator |
| `timestamp_id` | sygnatura czasowa| NO| Znacznik czasu, w którym jest wykonywane zapytanie. Jest to oparte na konfiguracji query_store_interval|
| `query_digest_text`| longtext| NO| Znormalizowany tekst zapytania po usunięciu wszystkich literałów|
| `query_sample_text` | longtext| NO| Pierwszy wygląd rzeczywistego zapytania z literałami|
| `query_digest_truncated` | bit| TAK| Czy tekst zapytania został obcięty. Wartość będzie równa tak, jeśli zapytanie jest dłuższe niż 1 KB|
| `execution_count` | bigint (20)| NO| Liczba wykonań zapytania dla tego identyfikatora znacznika czasu/w skonfigurowanym okresie interwału|
| `warning_count` | bigint (20)| NO| Liczba ostrzeżeń wygenerowanych przez to zapytanie w czasie wewnętrznym|
| `error_count` | bigint (20)| NO| Liczba błędów wygenerowanych przez to zapytanie w interwale|
| `sum_timer_wait` | double| TAK| Łączny czas wykonywania tego zapytania w interwale|
| `avg_timer_wait` | double| TAK| Średni czas wykonywania dla tego zapytania w interwale|
| `min_timer_wait` | double| TAK| Minimalny czas wykonywania dla tego zapytania|
| `max_timer_wait` | double| TAK| Maksymalny czas wykonywania|
| `sum_lock_time` | bigint (20)| NO| Łączny czas spędzony dla wszystkich blokad dla tego wykonania zapytania w tym przedziale czasu|
| `sum_rows_affected` | bigint (20)| NO| Liczba zaatakowanych wierszy|
| `sum_rows_sent` | bigint (20)| NO| Liczba wierszy wysłanych do klienta|
| `sum_rows_examined` | bigint (20)| NO| Liczba badanych wierszy|
| `sum_select_full_join` | bigint (20)| NO| Liczba pełnych sprzężeń|
| `sum_select_scan` | bigint (20)| NO| Liczba wybranych skanów |
| `sum_sort_rows` | bigint (20)| NO| Liczba posortowanych wierszy|
| `sum_no_index_used` | bigint (20)| NO| Liczba przypadków, gdy zapytanie nie używało żadnych indeksów|
| `sum_no_good_index_used` | bigint (20)| NO| Liczba przypadków, gdy aparat wykonywania zapytania nie korzystał z prawidłowych indeksów|
| `sum_created_tmp_tables` | bigint (20)| NO| Łączna liczba utworzonych tabel tymczasowych|
| `sum_created_tmp_disk_tables` | bigint (20)| NO| Łączna liczba tabel tymczasowych utworzonych w dysku (generuje we/wy)|
| `first_seen` | sygnatura czasowa| NO| Pierwsze wystąpienie (UTC) zapytania w oknie agregacji|
| `last_seen` | sygnatura czasowa| NO| Ostatnie wystąpienie (UTC) zapytania w tym oknie agregacji|

### <a name="mysqlquery_store_wait_stats"></a>mysql.query_store_wait_stats

Ten widok zwraca dane zdarzeń oczekiwania w magazynie zapytań. Istnieje jeden wiersz dla każdego identyfikatora bazy danych, identyfikatora użytkownika, identyfikatora zapytania i zdarzenia.

| **Nazwa**| **Typ danych** | **IS_NULLABLE** | **Opis** |
|---|---|---|---|
| `interval_start` | sygnatura czasowa | NO| Początek interwału (15-minutowy przyrost)|
| `interval_end` | sygnatura czasowa | NO| Koniec interwału (co 15 minut)|
| `query_id` | bigint (20) | NO| Wygenerowano unikatowy identyfikator w znormalizowanej kwerendzie (z magazynu zapytań)|
| `query_digest_id` | varchar (32) | NO| Znormalizowany tekst zapytania po usunięciu wszystkich literałów (z magazynu zapytań) |
| `query_digest_text` | longtext | NO| Pierwszy wygląd rzeczywistego zapytania z literałami (z magazynu zapytań) |
| `event_type` | varchar (32) | NO| Kategoria zdarzenia oczekiwania |
| `event_name` | varchar (128) | NO| Nazwa zdarzenia oczekiwania |
| `count_star` | bigint (20) | NO| Liczba zdarzeń oczekiwania próbkowanych w przedziale czasu dla zapytania |
| `sum_timer_wait_ms` | double | NO| Łączny czas oczekiwania (w milisekundach) tego zapytania w interwale |

### <a name="functions"></a>Funkcje

| **Nazwa**| **Opis** |
|---|---|
| `mysql.az_purge_querystore_data(TIMESTAMP)` | Przeczyszcza wszystkie dane magazynu zapytań przed daną sygnaturą czasową |
| `mysql.az_procedure_purge_querystore_event(TIMESTAMP)` | Przeczyszcza wszystkie dane zdarzeń oczekiwania przed daną sygnaturą czasową |
| `mysql.az_procedure_purge_recommendation(TIMESTAMP)` | Przeczyszcza zalecenia, których wygaśnięcie jest przed daną sygnaturą czasową |

## <a name="limitations-and-known-issues"></a>Ograniczenia i znane problemy

- Jeśli serwer MariaDB ma parametr `default_transaction_read_only` na, magazyn zapytań nie może przechwycić danych.
- Funkcja magazynu zapytań może zostać przerwana, jeśli napotka długie zapytania Unicode ( \> = 6000 bajtów).
- Okres przechowywania statystyk oczekiwania wynosi 24 godziny.
- Statystyki oczekiwania wykorzystują przykładowe przechwycenie zdarzenia. Częstotliwość można zmodyfikować przy użyciu parametru `query_store_wait_sampling_frequency` .

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o usłudze [Query Performance Insights](concepts-query-performance-insight.md)
