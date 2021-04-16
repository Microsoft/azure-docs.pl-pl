---
title: Magazyn zapytań — Azure Database for PostgreSQL — Flex Server
description: W tym artykule opisano funkcję magazynu zapytań w Azure Database for PostgreSQL — Flex Server.
author: ssen-msft
ms.author: ssen
ms.service: postgresql
ms.topic: conceptual
ms.date: 04/01/2021
ms.openlocfilehash: 6ab2ea6f6544bcf561e92f00b5afee693393c157
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107559084"
---
# <a name="monitor-performance-with-query-store"></a>Monitorowanie wydajności za pomocą magazynu zapytań
**Dotyczy:** Azure Database for PostgreSQL — Flex Server w wersji 11 lub starszej

Funkcja magazynu zapytań w Azure Database for PostgreSQL umożliwia śledzenie wydajności zapytań w czasie. Magazyn zapytań upraszcza rozwiązywanie problemów z wydajnością, pomagając szybko znaleźć najdłużej działające zapytania intensywnie korzystające z zasobów. Magazyn zapytań automatycznie przechwytuje historię zapytań i statystyk środowiska uruchomieniowego i zachowuje je do przeglądu. Dane są kąsane według czasu, dzięki czemu można zobaczyć wzorce użycia czasowego. Dane wszystkich użytkowników, baz danych i zapytań są przechowywane w bazie danych o nazwie **azure_sys** w Azure Database for PostgreSQL wystąpienia.

> [!IMPORTANT]
> Nie należy modyfikować **azure_sys** ani jej schematu. Uniemożliwi to prawidłowe działanie magazynu zapytań i powiązanych funkcji wydajności.
## <a name="enabling-query-store"></a>Włączanie magazynu zapytań
Magazyn zapytań jest funkcją opt-in, więc nie jest domyślnie włączona na serwerze. Magazyn zapytań jest włączony lub wyłączony globalnie dla wszystkich baz danych na danym serwerze i nie można go włączyć ani wyłączyć dla 1 bazy danych.
### <a name="enable-query-store-using-the-azure-portal"></a>Włączanie magazynu zapytań przy użyciu Azure Portal
1. Zaloguj się do Azure Portal i wybierz Azure Database for PostgreSQL serwera.
2. Wybierz **pozycję Parametry** serwera w **sekcji** Ustawienia menu.
3. Wyszukaj `pg_qs.query_capture_mode` parametr .
4. Ustaw wartość na `TOP` lub `ALL` i zapisz **.**
Poczekaj do 20 minut na utrwalenie pierwszej partii danych w bazie danych azure_sys.
## <a name="information-in-query-store"></a>Informacje w magazynie zapytań
Magazyn zapytań ma jeden magazyn:
- Magazyn statystyk środowiska uruchomieniowego do utrwalania informacji statystyk wykonywania zapytania.

Typowe scenariusze użycia magazynu zapytań obejmują:
- Określanie, ile razy zapytanie zostało wykonane w danym oknie czasowym
- Porównywanie średniego czasu wykonywania zapytania w oknach czasu w celu zobaczenia dużych różnic
- Identyfikowanie najdłużej działających zapytań w ciągu ostatnich kilku godzin Aby zminimalizować użycie miejsca, statystyki wykonywania środowiska uruchomieniowego w magazynie statystyk środowiska uruchomieniowego są agregowane w stałym, konfigurowalnym oknie czasowym. Informacje w tych magazynach mogą być wyszukiwane przy użyciu widoków.
## <a name="access-query-store-information"></a>Uzyskiwanie dostępu do informacji o magazynie zapytań
Dane magazynu zapytań są przechowywane w azure_sys danych na serwerze Postgres. Następujące zapytanie zwraca informacje o zapytaniach w magazynie zapytań:
```sql
SELECT * FROM query_store.qs_view; 
``` 

## <a name="configuration-options"></a>Opcje konfiguracji
Po włączeniu magazynu zapytań zapisuje dane w 15-minutowych oknach agregacji, maksymalnie 500 odrębnych zapytań na okno. Poniżej przedstawiono opcje konfigurowania parametrów magazynu zapytań.

| **Parametr** | **Opis** | **Domyślny** | **Zakres**|
|---|---|---|---|
| pg_qs.query_capture_mode | Określa, które instrukcje są śledzone. | brak | none, top, all |
| pg_qs.max_query_text_length | Ustawia maksymalną długość zapytania, która może zostać zapisana. Dłuższe zapytania zostaną obcięte. | 6000 | 100–10 tys. |
| pg_qs.retention_period_in_days | Ustawia okres przechowywania. | 7 | 1 - 30 |
| pg_qs.track_utility | Określa, czy polecenia narzędzia są śledzone | on | wł., wył. |

Użyj [Azure Portal,](howto-configure-server-parameters-using-portal.md) aby pobrać lub ustawić inną wartość parametru.

## <a name="views-and-functions"></a>Widoki i funkcje
Wyświetlaj magazyn zapytań i zarządzaj nimi przy użyciu następujących widoków i funkcji. Każda osoba w roli publicznej PostgreSQL może użyć tych widoków, aby wyświetlić dane w magazynie zapytań. Te widoki są dostępne tylko w azure_sys **danych.**
Zapytania są normalizowane przez analizę ich struktury po usunięciu literałów i stałych. Jeśli dwa zapytania są identyczne z wyjątkiem wartości literału, będą mieć ten sam queryId.
### <a name="query_storeqs_view"></a>query_store.qs_view
Ten widok zwraca wszystkie dane w magazynie zapytań. Dla każdego oddzielnego identyfikatora bazy danych, identyfikatora użytkownika i identyfikatora zapytania istnieje jeden wiersz. 

|**Nazwa**   |**Typ** | **Odwołania**  | **Opis**|
|---|---|---|---|
|runtime_stats_entry_id |bigint | | Identyfikator z runtime_stats_entries tabeli|
|user_id    |Oid    |pg_authid.oid  |Identyfikator OID użytkownika, który wykonał instrukcje|
|Db_id  |Oid    |pg_database.oid    |OID bazy danych, w której została wykonana instrukcja|
|query_id   |bigint  || Wewnętrzny kod skrótu obliczony na przykład w drzewie analizy instrukcji|
|query_sql_text |Varchar(10000)  || Tekst reprezentatywnej wypowiedzi. Różne zapytania o tej samej strukturze są grupowane razem; ten tekst jest tekstem pierwszego zapytania w klastrze.|
|plan_id    |bigint |   |Identyfikator planu odpowiadający temu zapytaniu, jeszcze niedostępny|
|start_time |sygnatura czasowa  ||  Zapytania są agregowane według przedziałów czasu — przedział czasu zasobnika domyślnie wynosi 15 minut. Jest to godzina rozpoczęcia odpowiadająca przedziałowi czasu dla tego wpisu.|
|End_time   |sygnatura czasowa  ||  Godzina zakończenia odpowiadająca przedziałowi czasu dla tego wpisu.|
|Wywołania  |bigint  || Liczba wykonanych zapytań|
|total_time |podwójna precyzja   ||  Łączny czas wykonywania zapytania, w milisekundach|
|min_time   |podwójna precyzja   ||  Minimalny czas wykonywania zapytania, w milisekundach|
|max_time   |podwójna precyzja   ||  Maksymalny czas wykonywania zapytania, w milisekundach|
|mean_time  |podwójna precyzja   ||  Średni czas wykonywania zapytania, w milisekundach|
|stddev_time|   podwójna precyzja    ||  Odchylenie standardowe czasu wykonywania zapytania, w milisekundach |
|Wierszy   |bigint ||  Łączna liczba wierszy pobranych lub na które ma wpływ instrukcja|
|shared_blks_hit|   bigint  ||  Łączna liczba trafień udostępnionej pamięci podręcznej bloków według instrukcji|
|shared_blks_read|  bigint  ||  Łączna liczba udostępnionych bloków odczytanych przez instrukcje|
|shared_blks_dirtied|   bigint   || Łączna liczba udostępnionych bloków zasypanych przez instrukcje |
|shared_blks_written|   bigint  ||  Łączna liczba udostępnionych bloków napisanych przez instrukcji|
|local_blks_hit|    bigint ||   Łączna liczba trafień lokalnej pamięci podręcznej bloków według instrukcji|
|local_blks_read|   bigint   || Łączna liczba bloków lokalnych odczytanych przez instrukcje|
|local_blks_dirtied|    bigint  ||  Łączna liczba bloków lokalnych chłonianych przez instrukcje|
|local_blks_written|    bigint  ||  Łączna liczba bloków lokalnych zapisywanych przez instrukcji|
|temp_blks_read |bigint  || Łączna liczba bloków tymczasowych odczytanych przez instrukcje|
|temp_blks_written| bigint   || Łączna liczba bloków tymczasowych napisanych przez instrukcje|
|blk_read_time  |podwójna precyzja    || Łączny czas, przez który instrukcja odczytuje bloki, w milisekundach (jeśli track_io_timing jest włączona, w przeciwnym razie zero)|
|blk_write_time |podwójna precyzja    || Łączny czas spędzony na pisaniu bloków w milisekundach (jeśli instrukcja track_io_timing włączona, w przeciwnym razie zero)|
    
### <a name="query_storequery_texts_view"></a>query_store.query_texts_view
Ten widok zwraca dane tekstowe zapytania w magazynie zapytań. Istnieje jeden wiersz dla każdego odrębnego query_text.

| **Nazwa** | **Typ** | **Opis** |
|--|--|--|
| query_text_id | bigint | Identyfikator tabeli query_texts danych |
| query_sql_text | Varchar(10000) | Tekst reprezentatywnej wypowiedzi. Różne zapytania o tej samej strukturze są grupowane razem; ten tekst jest tekstem pierwszego zapytania w klastrze. |

### <a name="functions"></a>Funkcje
`qs_reset` odrzuca wszystkie statystyki zebrane do tej pory przez magazyn zapytań. Ta funkcja może być wykonywana tylko przez rolę administratora serwera.

`staging_data_reset` odrzuca wszystkie statystyki zebrane w pamięci przez magazyn zapytań (czyli dane w pamięci, które nie zostały jeszcze opróżnione do bazy danych). Ta funkcja może być wykonywana tylko przez rolę administratora serwera.

## <a name="limitations-and-known-issues"></a>Ograniczenia i znane problemy
- Jeśli serwer PostgreSQL ma default_transaction_read_only, magazyn zapytań nie przechwyci żadnych danych.
## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o [scenariuszach, w których magazyn zapytań może być szczególnie przydatny.](concepts-query-store-scenarios.md)
- Dowiedz się więcej o [najlepszych rozwiązaniach dotyczących używania magazynu zapytań.](concepts-query-store-best-practices.md)
