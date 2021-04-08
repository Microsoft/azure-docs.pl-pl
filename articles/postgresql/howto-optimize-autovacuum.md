---
title: Optymalizacja autopróżni-Azure Database for PostgreSQL-pojedynczego serwera
description: W tym artykule opisano, jak można zoptymalizować funkcję autopróżniową na Azure Database for PostgreSQL-pojedynczym serwerze
author: dianaputnam
ms.author: dianas
ms.service: postgresql
ms.topic: how-to
ms.date: 07/09/2020
ms.openlocfilehash: a94afc1ab970c2cd3f509c86efba4e455d46fd13
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96012565"
---
# <a name="optimize-autovacuum-on-an-azure-database-for-postgresql---single-server"></a>Optymalizowanie autopróżni na Azure Database for PostgreSQL-pojedynczym serwerze

W tym artykule opisano sposób efektywnego optymalizowania autopróżni na serwerze Azure Database for PostgreSQL.

## <a name="overview-of-autovacuum"></a>Omówienie autopróżni

PostgreSQL używa kontroli współbieżności wielowersji (MVCC), aby zapewnić większe współbieżność bazy danych. Każda aktualizacja skutkuje wstawianiem i usuwaniem, a każde usunięcie powoduje, że wiersze są nietrwałe — oznaczone do usunięcia. Oznaczanie nietrwałe identyfikuje nieaktywne krotki, które zostaną później przeczyszczone. Aby wykonać te zadania, PostgreSQL uruchamia zadanie próżniowe.

Zadanie próżniowe może być wyzwalane ręcznie lub automatycznie. Więcej martwych krotek występuje, gdy baza danych działa w dużej liczbie operacji aktualizowania lub usuwania. Gdy baza danych jest bezczynna, istnieje mniej martwych krotek. Konieczne jest przetworzenie próżni częściej, gdy obciążenie bazy danych jest duże, co sprawia, że uruchomione zadania próżniowe są *ręcznie* niewygodne.

Można skonfigurować samopróżnię i korzyści z dostrajania. Wartości domyślne, które PostgreSQL są dostarczane z próbą zapewnienia, że produkt działa na wszystkich rodzajach urządzeń. Urządzenia te obejmują Raspberry PiS. Idealne wartości konfiguracji zależą od:

- Łączna liczba dostępnych zasobów, takich jak jednostka SKU i rozmiar magazynu.
- Użycie zasobów.
- Charakterystyki poszczególnych obiektów.

## <a name="autovacuum-benefits"></a>Korzyści z używania autopróżni

W przypadku braku próżni od czasu do czasu, niedziałające krotki, które gromadzą się mogą spowodować:

- Przeładowanie danych, takie jak większe bazy danych i tabele.
- Większe nieoptymalne indeksy.
- Zwiększona we/wy.

## <a name="monitor-bloat-with-autovacuum-queries"></a>Monitoruj przeładowanie z użyciem zapytań autopróżniowych

Następujące przykładowe zapytanie zostało zaprojektowane, aby określić liczbę utraconych i na żywo krotek w tabeli o nazwie XYZ:

```sql
SELECT relname, n_dead_tup, n_live_tup, (n_dead_tup/ n_live_tup) AS DeadTuplesRatio, last_vacuum, last_autovacuum FROM pg_catalog.pg_stat_all_tables WHERE relname = 'XYZ' order by n_dead_tup DESC;
```

## <a name="autovacuum-configurations"></a>Konfiguracje autopróżniowe

Parametry konfiguracji kontrolujące autopróżniowo są oparte na odpowiedzi na dwa kluczowe pytania:

- Kiedy należy zacząć?
- Ile powinno być czyste po rozpoczęciu?

Poniżej przedstawiono niektóre parametry konfiguracji autopróżniowa, które można zaktualizować w oparciu o poprzednie pytania wraz z pewnymi wskazówkami.

Parametr|Opis|Wartość domyślna
---|---|---
autovacuum_vacuum_threshold|Określa minimalną liczbę zaktualizowanych lub usuniętych krotek wymaganych do wyzwolenia operacji próżniowej w jednej tabeli. Wartość domyślna to 50 krotek. Ten parametr należy ustawić tylko w pliku PostgreSQL. conf lub w wierszu polecenia serwera. Aby zastąpić ustawienia dla poszczególnych tabel, Zmień parametry magazynu tabel.|50
autovacuum_vacuum_scale_factor|Określa ułamek rozmiaru tabeli, który ma zostać dodany do autovacuum_vacuum_threshold podczas decydowania o tym, czy wyzwolić operację próżniową. Wartość domyślna to 0,2, czyli 20 procent rozmiaru tabeli. Ten parametr należy ustawić tylko w pliku PostgreSQL. conf lub w wierszu polecenia serwera. Aby zastąpić ustawienia dla poszczególnych tabel, Zmień parametry magazynu tabel.|0,2
autovacuum_vacuum_cost_limit|Określa wartość limitu kosztu używaną w automatycznych operacjach próżniowych. Jeśli określono wartość-1, która jest wartością domyślną, używana jest zwykła vacuum_cost_limit. Jeśli istnieje więcej niż jeden proces roboczy, wartość jest dystrybuowana proporcjonalnie między uruchomionymi pracownikami autopróżniowymi. Suma limitów dla każdego pracownika nie przekracza wartości tej zmiennej. Ten parametr należy ustawić tylko w pliku PostgreSQL. conf lub w wierszu polecenia serwera. Aby zastąpić ustawienia dla poszczególnych tabel, Zmień parametry magazynu tabel.|-1
autovacuum_vacuum_cost_delay|Określa wartość opóźnienia kosztu używaną w automatycznych operacjach próżniowych. Jeśli określono wartość-1, stosowana jest zwykła vacuum_cost_delay. Wartość domyślna to 20 milisekund. Ten parametr należy ustawić tylko w pliku PostgreSQL. conf lub w wierszu polecenia serwera. Aby zastąpić ustawienia dla poszczególnych tabel, Zmień parametry magazynu tabel.|20 MS
autovacuum_naptime | Określa minimalne opóźnienie między uruchomieniami autopróżniowymi w danej bazie danych. W każdej rundzie demon sprawdza bazę danych i wystawia problemy PRÓŻNIOWe i analizuje polecenia zgodnie z wymaganiami dla tabel w tej bazie danych. Opóźnienie jest mierzone w sekundach. Ten parametr należy ustawić tylko w pliku PostgreSQL. conf lub w wierszu polecenia serwera.| 15 s
autovacuum_max_workers | Określa maksymalną liczbę procesów autopróżniowych, z wyjątkiem uruchamiania autopróżniowego, która może być uruchamiana w dowolnym momencie. Wartość domyślna to trzy. Ten parametr należy ustawić tylko podczas uruchamiania serwera.|3

Aby zastąpić ustawienia poszczególnych tabel, należy zmienić parametry magazynu tabel.

## <a name="autovacuum-cost"></a>Koszt samopróżniowy

Poniżej przedstawiono "koszty" wykonywania operacji próżniowych:

- Strony danych, na których działa próżniowo, są zablokowane.
- Obliczenia i pamięć są używane, gdy zadanie próżniowe jest uruchomione.

W związku z tym nie uruchamiaj zadania próżniowe zbyt często lub zbyt często. Zadanie próżniowe musi dostosowywać się do obciążenia. Przetestuj wszystkie zmiany parametrów autopróżniowych ze względu na kompromisy każdej z nich.

## <a name="autovacuum-start-trigger"></a>Wyzwalacz uruchomienia autopróżniowy

Autopróżnia jest wyzwalana, gdy liczba martwych krotek przekracza autovacuum_vacuum_threshold + autovacuum_vacuum_scale_factor * reltuples. Tutaj reltuples jest stała.

Oczyszczanie z autopróżni musi być zachowywane wraz z obciążeniem bazy danych. W przeciwnym razie możesz wylogować się z magazynu i napotkać ogólne spowolnienie w zapytaniach. Amortyzowany w czasie, szybkość, z jaką operacja próżniowa czyści spójne krotki, powinna być równa szybkości, z jaką są tworzone nieaktywne krotki.

Bazy danych zawierające wiele aktualizacji i usunięć mają więcej martwych krotek i potrzebują większej ilości miejsca. Ogólnie rzecz biorąc, bazy danych zawierające wiele aktualizacji i usunięć mogą korzystać z niskich wartości autovacuum_vacuum_scale_factor i autovacuum_vacuum_threshold. Niskie wartości uniemożliwiają długotrwałe gromadzenie nieaktywnych krotek. Można używać wyższych wartości dla obu parametrów z mniejszymi bazami danych, ponieważ potrzeba obpróżniania jest mniej pilna. Częste Próżnie jest kosztem obliczeniowym i pamięci.

Domyślny współczynnik skalowania wynoszący 20 procent działa prawidłowo w tabelach z niską wartością procentową nieaktywnych krotek. Nie działa dobrze w przypadku tabel, które mają wysoki procent martwych krotek. Na przykład w tabeli 20 GB ten współczynnik skalowania tłumaczy się na 4 GB martwych krotek. W tabeli 1 TB jest 200 GB utraconych krotek.

Za pomocą PostgreSQL można ustawić te parametry na poziomie tabeli lub wystąpieniu. Obecnie te parametry można ustawić na poziomie tabeli tylko w Azure Database for PostgreSQL.

## <a name="estimate-the-cost-of-autovacuum"></a>Oszacowanie kosztów autopróżni

Uruchamianie autopróżni jest kosztowne, a dostępne są parametry służące do kontrolowania środowiska uruchomieniowego operacji próżniowych. Następujące parametry pomagają oszacować koszt uruchomienia próżni:

- vacuum_cost_page_hit = 1
- vacuum_cost_page_miss = 10
- vacuum_cost_page_dirty = 20

Proces próżniowy odczytuje strony fizyczne i sprawdza obecność utraconych krotek. Każda Strona w shared_buffers jest uznawana za koszt 1 (vacuum_cost_page_hit). Wszystkie inne strony są uznawane za 20 (vacuum_cost_page_dirty), jeśli istnieją nieaktywne krotki lub 10 (vacuum_cost_page_miss), jeśli nie istnieją żadne nieaktywne krotki. Operacja próżniowa zostaje zatrzymana, gdy proces przekroczy autovacuum_vacuum_cost_limit.

Po osiągnięciu limitu proces zostanie uśpiony przez czas określony przez parametr autovacuum_vacuum_cost_delay przed ponownym uruchomieniem. Jeśli limit nie zostanie osiągnięty, autopróżnia zaczyna się po wartości określonej przez parametr autovacuum_nap_time.

Podsumowując, parametry autovacuum_vacuum_cost_delay i autovacuum_vacuum_cost_limit kontrolują, jak dużo oczyszczanie danych jest dozwolone na jednostkę czasu. Należy zauważyć, że wartości domyślne są zbyt niskie dla większości warstw cenowych. Optymalne wartości tych parametrów są zależne od warstwy cenowej i powinny być odpowiednio skonfigurowane.

Parametr autovacuum_max_workers określa maksymalną liczbę procesów autopróżniowych, które mogą być uruchamiane jednocześnie.

Za pomocą PostgreSQL można ustawić te parametry na poziomie tabeli lub wystąpieniu. Obecnie te parametry można ustawić na poziomie tabeli tylko w Azure Database for PostgreSQL.

## <a name="optimize-autovacuum-per-table"></a>Optymalizuj autopróżnię na tabelę

Wszystkie poprzednie parametry konfiguracji można skonfigurować dla każdej tabeli. Oto przykład:

```sql
ALTER TABLE t SET (autovacuum_vacuum_threshold = 1000);
ALTER TABLE t SET (autovacuum_vacuum_scale_factor = 0.1);
ALTER TABLE t SET (autovacuum_vacuum_cost_limit = 1000);
ALTER TABLE t SET (autovacuum_vacuum_cost_delay = 10);
```

Autopróżnia jest procesem synchronicznym tabeli. Większy procent martwych krotek, które ma tabela, im większa jest wartość "koszt" do autopróżni. Można podzielić tabele, które mają dużą częstotliwość aktualizacji i usunięć w wielu tabelach. Dzielenie tabel pozwala na zrównoleglanie autopróżnię i zmniejszenie "kosztów" w celu ukończenia autopróżni w jednej tabeli. Możesz również zwiększyć liczbę równoległych procesów roboczych samopróżniowych, aby zapewnić, że pracownicy są zaplanowani w sposób zliberalizowany.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o sposobie używania i dostrajania autopróżni, zobacz następującą dokumentację PostgreSQL:

- [Rozdział 18, konfiguracja serwera](https://www.postgresql.org/docs/9.5/static/runtime-config-autovacuum.html)
- [Rozdział 24, rutynowe zadania konserwacji bazy danych](https://www.postgresql.org/docs/9.6/static/routine-vacuuming.html)
