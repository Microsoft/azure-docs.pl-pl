---
title: Rozszerzenia-Azure Database for PostgreSQL-elastyczny serwer
description: Dowiedz się więcej na temat dostępnych rozszerzeń Postgres na serwerze elastycznym Azure Database for PostgreSQL
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: 1126c218f8e80b7d89183746890a3fae1357d29d
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/22/2020
ms.locfileid: "90938521"
---
# <a name="postgresql-extensions-in-azure-database-for-postgresql---flexible-server"></a>Rozszerzenia PostgreSQL na serwerze elastycznym Azure Database for PostgreSQL

> [!IMPORTANT]
> Azure Database for PostgreSQL — serwer elastyczny jest w wersji zapoznawczej

PostgreSQL zapewnia możliwość rozszerzenia funkcjonalności bazy danych przy użyciu rozszerzeń. Rozszerzenia rozszerzają wiele powiązanych obiektów SQL razem w jednym pakiecie, który można załadować lub usunąć z bazy danych za pomocą polecenia. Po załadowaniu do bazy danych rozszerzenia działają jak wbudowane funkcje.

## <a name="how-to-use-postgresql-extensions"></a>Jak używać rozszerzeń PostgreSQL
Rozszerzenia PostgreSQL muszą być zainstalowane w bazie danych, zanim będzie można z nich korzystać. Aby zainstalować określone rozszerzenie, uruchom polecenie [Create Extension](https://www.postgresql.org/docs/current/sql-createextension.html) . To polecenie ładuje spakowane obiekty do bazy danych programu.

Azure Database for PostgreSQL obsługuje podzestaw rozszerzeń kluczy, jak pokazano poniżej. Te informacje są również dostępne po uruchomieniu programu `SHOW azure.extensions;` . Rozszerzenia niewymienione w tym dokumencie nie są obsługiwane na serwerze Azure Database for PostgreSQL-elastycznym. Nie można utworzyć ani załadować własnego rozszerzenia w Azure Database for PostgreSQL.


## <a name="postgres-12-extensions"></a>Rozszerzenia Postgres 12

Następujące rozszerzenia są dostępne na serwerach Azure Database for PostgreSQL-elastycznych, które mają Postgres w wersji 12. 

> [!div class="mx-tableFixed"]
> | **Wewnętrzny**| **Wersja rozszerzenia** | **Opis** |
> |---|---|---|
> |[amcheck](https://www.postgresql.org/docs/12/amcheck.html)                    | 1,2             | funkcje sprawdzania integralności relacji|
> |[lotem](https://www.postgresql.org/docs/12/bloom.html)                    | 1,0             | Metoda dostępu rozwiniętego — indeks oparty na pliku sygnatury|
> |[btree_gin](https://www.postgresql.org/docs/12/btree-gin.html)                    | 1.3             | Obsługa indeksowania wspólnych typów danych w ĄTEK|
> |[btree_gist](https://www.postgresql.org/docs/12/btree-gist.html)                   | 1.5             | Obsługa indeksowania wspólnych typów danych w rejestrze|
> |[citext](https://www.postgresql.org/docs/12/citext.html)                       | 1.6             | Typ danych dla ciągów znaków bez uwzględniania wielkości liter|
> |[modułów](https://www.postgresql.org/docs/12/cube.html)                         | 1.4             | Typ danych dla modułów wielowymiarowych|
> |[dblink](https://www.postgresql.org/docs/12/dblink.html)                       | 1,2             | Łączenie z innymi bazami danych PostgreSQL z poziomu bazy danych|
> |[dict_int](https://www.postgresql.org/docs/12/dict-int.html)                     | 1,0             | szablon słownika wyszukiwania tekstu dla liczb całkowitych|
> |[dict_xsyn](https://www.postgresql.org/docs/12/dict-xsyn.html)                     | 1,0             | szablon słownika wyszukiwania tekstu dla rozszerzonego przetwarzania synonimów|
> |[earthdistance](https://www.postgresql.org/docs/12/earthdistance.html)                | 1,1             | Oblicz duże odległości na powierzchni ziemi|
> |[fuzzystrmatch](https://www.postgresql.org/docs/12/fuzzystrmatch.html)                | 1,1             | Określ podobieństwa i odległość między ciągami|
> |[hstore](https://www.postgresql.org/docs/12/hstore.html)                       | 1.6             | Typ danych do przechowywania zestawów par (klucz, wartość)|
> |[intagg](https://www.postgresql.org/docs/12/intagg.html)                     | 1,1             | agregator liczb całkowitych i moduł wyliczający. Zbędn|
> |[intarray](https://www.postgresql.org/docs/12/intarray.html)                     | 1,2             | Obsługa funkcji, operatorów i indeksów dla tablic 1-D liczb całkowitych|
> |[jest](https://www.postgresql.org/docs/12/isn.html)                          | 1,2             | typy danych dla międzynarodowych standardów numerowania produktów|
> |[ltree](https://www.postgresql.org/docs/12/ltree.html)                        | 1,1             | Typ danych dla struktur hierarchicznych, takich jak drzewa|
> |[pageinspect](https://www.postgresql.org/docs/12/pageinspect.html)                        | 1,7             | Sprawdzanie zawartości stron bazy danych na niskim poziomie|
> |[pg_buffercache](https://www.postgresql.org/docs/12/pgbuffercache.html)               | 1.3             | Sprawdzanie udostępnionej pamięci podręcznej buforu|
> |[pg_freespacemap](https://www.postgresql.org/docs/12/pgfreespacemap.html)               | 1,2             | Obejrzyj mapę wolnego miejsca (FSM)|
> |[pg_prewarm](https://www.postgresql.org/docs/12/pgprewarm.html)                   | 1,2             | dane relacji przedgrzanej|
> |[pg_stat_statements](https://www.postgresql.org/docs/12/pgstatstatements.html)           | 1,7             | Śledź statystyki wykonywania wszystkich wykonanych instrukcji SQL|
> |[pg_trgm](https://www.postgresql.org/docs/12/pgtrgm.html)                      | 1.4             | Pomiar podobieństwa tekstu i wyszukiwanie indeksu na podstawie trigrams|
> |[pg_visibility](https://www.postgresql.org/docs/12/pgvisibility.html)                      | 1,2             | Obejrzyj mapę widoczności (VM) i informacje o widoczności na poziomie strony|
> |[pgaudit](https://www.pgaudit.org/)                     | 1.4             | zapewnia funkcje inspekcji|
> |[pgcrypto](https://www.postgresql.org/docs/12/pgcrypto.html)                     | 1.3             | funkcje kryptograficzne|
> |[pgrowlocks](https://www.postgresql.org/docs/12/pgrowlocks.html)                   | 1,2             | Pokaż informacje o blokowaniu na poziomie wiersza|
> |[pgstattuple](https://www.postgresql.org/docs/12/pgstattuple.html)                  | 1.5             | Pokaż statystyki na poziomie krotki|
> |[plpgsql](https://www.postgresql.org/docs/12/plpgsql.html)                      | 1,0             | Język proceduralny PL/pgSQL|
> |[postgis](https://www.postgis.net/)                      | 3.0.0           | PostGIS geometrii, Geografia i typy przestrzenne i funkcje rastrowe|
> |[postgres_fdw](https://www.postgresql.org/docs/12/postgres-fdw.html)                 | 1,0             | otoka danych obcych dla zdalnych serwerów PostgreSQL|
> |[sslinfo](https://www.postgresql.org/docs/12/sslinfo.html)                    | 1,2             | Informacje o certyfikatach SSL|
> |[tsm_system_rows](https://www.postgresql.org/docs/12/tsm-system-rows.html)                    | 1,0             |  Metoda reklasy, która akceptuje liczbę wierszy jako limit|
> |[tsm_system_time](https://www.postgresql.org/docs/12/tsm-system-time.html)                    | 1,0             |  Metoda potraktować jako limit czas w milisekundach|
> |[deakcentowanie](https://www.postgresql.org/docs/12/unaccent.html)                     | 1,1             | słownik wyszukiwania tekstu, który usuwa akcenty|
> |[identyfikator UUID — OSSP](https://www.postgresql.org/docs/12/uuid-ossp.html)                    | 1,1             | Generuj unikatowe identyfikatory uniwersalne (UUID)|

## <a name="postgres-11-extensions"></a>Rozszerzenia Postgres 11

Następujące rozszerzenia są dostępne na serwerach Azure Database for PostgreSQL-elastycznych, które mają Postgres w wersji 11. 

> [!div class="mx-tableFixed"]
> | **Wewnętrzny**| **Wersja rozszerzenia** | **Opis** |
> |---|---|---|
> |[amcheck](https://www.postgresql.org/docs/11/amcheck.html)                    | 1,1             | funkcje sprawdzania integralności relacji|
> |[lotem](https://www.postgresql.org/docs/11/bloom.html)                    | 1,0             | Metoda dostępu rozwiniętego — indeks oparty na pliku sygnatury|
> |[btree_gin](https://www.postgresql.org/docs/11/btree-gin.html)                    | 1.3             | Obsługa indeksowania wspólnych typów danych w ĄTEK|
> |[btree_gist](https://www.postgresql.org/docs/11/btree-gist.html)                   | 1.5             | Obsługa indeksowania wspólnych typów danych w rejestrze|
> |[citext](https://www.postgresql.org/docs/11/citext.html)                       | 1.5             | Typ danych dla ciągów znaków bez uwzględniania wielkości liter|
> |[modułów](https://www.postgresql.org/docs/11/cube.html)                         | 1.4             | Typ danych dla modułów wielowymiarowych|
> |[dblink](https://www.postgresql.org/docs/11/dblink.html)                       | 1,2             | Łączenie z innymi bazami danych PostgreSQL z poziomu bazy danych|
> |[dict_int](https://www.postgresql.org/docs/11/dict-int.html)                     | 1,0             | szablon słownika wyszukiwania tekstu dla liczb całkowitych|
> |[dict_xsyn](https://www.postgresql.org/docs/11/dict-xsyn.html)                     | 1,0             | szablon słownika wyszukiwania tekstu dla rozszerzonego przetwarzania synonimów|
> |[earthdistance](https://www.postgresql.org/docs/11/earthdistance.html)                | 1,1             | Oblicz duże odległości na powierzchni ziemi|
> |[fuzzystrmatch](https://www.postgresql.org/docs/11/fuzzystrmatch.html)                | 1,1             | Określ podobieństwa i odległość między ciągami|
> |[hstore](https://www.postgresql.org/docs/11/hstore.html)                       | 1.5             | Typ danych do przechowywania zestawów par (klucz, wartość)|
> |[intagg](https://www.postgresql.org/docs/11/intagg.html)                     | 1,1             | agregator liczb całkowitych i moduł wyliczający. Zbędn|
> |[intarray](https://www.postgresql.org/docs/11/intarray.html)                     | 1,2             | Obsługa funkcji, operatorów i indeksów dla tablic 1-D liczb całkowitych|
> |[jest](https://www.postgresql.org/docs/11/isn.html)                          | 1,2             | typy danych dla międzynarodowych standardów numerowania produktów|
> |[ltree](https://www.postgresql.org/docs/11/ltree.html)                        | 1,1             | Typ danych dla struktur hierarchicznych, takich jak drzewa|
> |[pageinspect](https://www.postgresql.org/docs/11/pageinspect.html)                        | 1,7             | Sprawdzanie zawartości stron bazy danych na niskim poziomie|
> |[pg_buffercache](https://www.postgresql.org/docs/11/pgbuffercache.html)               | 1.3             | Sprawdzanie udostępnionej pamięci podręcznej buforu|
> |[pg_freespacemap](https://www.postgresql.org/docs/11/pgfreespacemap.html)               | 1,2             | Obejrzyj mapę wolnego miejsca (FSM)|
> |[pg_prewarm](https://www.postgresql.org/docs/11/pgprewarm.html)                   | 1,2             | dane relacji przedgrzanej|
> |[pg_stat_statements](https://www.postgresql.org/docs/11/pgstatstatements.html)           | 1.6             | Śledź statystyki wykonywania wszystkich wykonanych instrukcji SQL|
> |[pg_trgm](https://www.postgresql.org/docs/11/pgtrgm.html)                      | 1.4             | Pomiar podobieństwa tekstu i wyszukiwanie indeksu na podstawie trigrams|
> |[pg_visibility](https://www.postgresql.org/docs/11/pgvisibility.html)                      | 1,2             | Obejrzyj mapę widoczności (VM) i informacje o widoczności na poziomie strony|
> |[pgaudit](https://www.pgaudit.org/)                     | 1.3.1             | zapewnia funkcje inspekcji|
> |[pgcrypto](https://www.postgresql.org/docs/11/pgcrypto.html)                     | 1.3             | funkcje kryptograficzne|
> |[pgrowlocks](https://www.postgresql.org/docs/11/pgrowlocks.html)                   | 1,2             | Pokaż informacje o blokowaniu na poziomie wiersza|
> |[pgstattuple](https://www.postgresql.org/docs/11/pgstattuple.html)                  | 1.5             | Pokaż statystyki na poziomie krotki|
> |[plpgsql](https://www.postgresql.org/docs/11/plpgsql.html)                      | 1,0             | Język proceduralny PL/pgSQL|
> |[postgis](https://www.postgis.net/)                      | 2.5.1           | PostGIS geometrii, Geografia i typy przestrzenne i funkcje rastrowe|
> |[postgres_fdw](https://www.postgresql.org/docs/11/postgres-fdw.html)                 | 1,0             | otoka danych obcych dla zdalnych serwerów PostgreSQL|
> |[sslinfo](https://www.postgresql.org/docs/11/sslinfo.html)                    | 1,2             | Informacje o certyfikatach SSL|
> |[tablefunc](https://www.postgresql.org/docs/11/tablefunc.html)                    | 1,0             | funkcje, które manipulują całymi tabelami, w tym krzyżowo|
> |[tsm_system_rows](https://www.postgresql.org/docs/11/tsm-system-rows.html)                    | 1,0             |  Metoda reklasy, która akceptuje liczbę wierszy jako limit|
> |[tsm_system_time](https://www.postgresql.org/docs/11/tsm-system-time.html)                    | 1,0             |  Metoda potraktować jako limit czas w milisekundach|
> |[deakcentowanie](https://www.postgresql.org/docs/11/unaccent.html)                     | 1,1             | słownik wyszukiwania tekstu, który usuwa akcenty|
> |[identyfikator UUID — OSSP](https://www.postgresql.org/docs/11/uuid-ossp.html)                    | 1,1             | Generuj unikatowe identyfikatory uniwersalne (UUID)|


## <a name="pg_prewarm"></a>pg_prewarm

Rozszerzenie pg_prewarm ładuje dane relacyjne do pamięci podręcznej. Przedgrzane pamięci podręczne oznacza, że zapytania mają większe czasy reakcji podczas pierwszego uruchomienia po ponownym uruchomieniu. Funkcja autouzupełniania nie jest obecnie dostępna w Azure Database for PostgreSQL-elastycznym serwerze.


## <a name="next-steps"></a>Następne kroki

Jeśli nie widzisz rozszerzenia, którego chcesz użyć, daj nam znać. Zagłosuj na istniejące żądania lub Utwórz nowe żądania opinii na naszym [forum opinii](https://feedback.azure.com/forums/597976-azure-database-for-postgresql).