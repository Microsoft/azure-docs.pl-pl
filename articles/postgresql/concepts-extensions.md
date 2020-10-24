---
title: Rozszerzenia — Azure Database for PostgreSQL — pojedynczy serwer
description: Dowiedz się więcej na temat dostępnych rozszerzeń Postgres w Azure Database for PostgreSQL-pojedynczym serwerze
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/14/2020
ms.openlocfilehash: 78395873457f9fe53d45dfbfd94aa9ccdccd614d
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2020
ms.locfileid: "92485464"
---
# <a name="postgresql-extensions-in-azure-database-for-postgresql---single-server"></a>Rozszerzenia PostgreSQL w usłudze Azure Database for PostgreSQL — pojedynczy serwer
PostgreSQL zapewnia możliwość rozszerzenia funkcjonalności bazy danych przy użyciu rozszerzeń. Rozszerzenia zapewniają wiele powiązanych obiektów SQL razem w jednym pakiecie, który można załadować lub usunąć z bazy danych za pomocą jednego polecenia. Po załadowaniu do bazy danych rozszerzenia działają jak wbudowane funkcje.

## <a name="how-to-use-postgresql-extensions"></a>Jak używać rozszerzeń PostgreSQL
Rozszerzenia PostgreSQL muszą być zainstalowane w bazie danych, zanim będzie można z nich korzystać. Aby zainstalować określone rozszerzenie, uruchom polecenie [CREATE EXTENSION](https://www.postgresql.org/docs/current/sql-createextension.html) (Utwórz rozszerzenie) z poziomu narzędzia psql w celu załadowania spakowanych obiektów do bazy danych.

Azure Database for PostgreSQL obsługuje podzestaw rozszerzeń kluczy, jak pokazano poniżej. Te informacje są również dostępne po uruchomieniu programu `SELECT * FROM pg_available_extensions;` . Rozszerzenia poza wymienionymi listami nie są obsługiwane. Nie można utworzyć własnego rozszerzenia w Azure Database for PostgreSQL.

## <a name="postgres-11-extensions"></a>Rozszerzenia Postgres 11

Następujące rozszerzenia są dostępne na serwerach Azure Database for PostgreSQL, które mają Postgres w wersji 11. 

> [!div class="mx-tableFixed"]
> | **Wewnętrzny**| **Wersja rozszerzenia** | **Opis** |
> |---|---|---|
> |[address_standardizer](http://postgis.net/docs/Address_Standardizer.html)         | 2.5.1           | Służy do analizowania adresu w elementach składowych. |
> |[address_standardizer_data_us](http://postgis.net/docs/Address_Standardizer.html) | 2.5.1           | Przykład dotyczący zestawu danych standaryzacji z nami|
> |[btree_gin](https://www.postgresql.org/docs/11/btree-gin.html)                    | 1.3             | Obsługa indeksowania wspólnych typów danych w ĄTEK|
> |[btree_gist](https://www.postgresql.org/docs/11/btree-gist.html)                   | 1.5             | Obsługa indeksowania wspólnych typów danych w rejestrze|
> |[citext](https://www.postgresql.org/docs/11/citext.html)                       | 1.5             | Typ danych dla ciągów znaków bez uwzględniania wielkości liter|
> |[modułów](https://www.postgresql.org/docs/11/cube.html)                         | 1.4             | Typ danych dla modułów wielowymiarowych|
> |[dblink](https://www.postgresql.org/docs/11/dblink.html)                       | 1,2             | Łączenie z innymi bazami danych PostgreSQL z poziomu bazy danych|
> |[dict_int](https://www.postgresql.org/docs/11/dict-int.html)                     | 1.0             | szablon słownika wyszukiwania tekstu dla liczb całkowitych|
> |[earthdistance](https://www.postgresql.org/docs/11/earthdistance.html)                | 1,1             | Oblicz duże odległości na powierzchni ziemi|
> |[fuzzystrmatch](https://www.postgresql.org/docs/11/fuzzystrmatch.html)                | 1,1             | Określ podobieństwa i odległość między ciągami|
> |[hstore](https://www.postgresql.org/docs/11/hstore.html)                       | 1.5             | Typ danych do przechowywania zestawów par (klucz, wartość)|
> |[hypopg](https://hypopg.readthedocs.io/en/latest/)                       | 1.1.2           | Indeksy hipotetyczne dla PostgreSQL|
> |[intarray](https://www.postgresql.org/docs/11/intarray.html)                     | 1,2             | Obsługa funkcji, operatorów i indeksów dla tablic 1-D liczb całkowitych|
> |[jest](https://www.postgresql.org/docs/11/isn.html)                          | 1,2             | typy danych dla międzynarodowych standardów numerowania produktów|
> |[ltree](https://www.postgresql.org/docs/11/ltree.html)                        | 1,1             | Typ danych dla struktur hierarchicznych, takich jak drzewa|
> |[orafce](https://github.com/orafce/orafce)                       | 3.7             | Funkcje i operatory, które emulują podzestaw funkcji i pakietów z RDBMS komercyjnych|
> |[pgaudit](https://www.pgaudit.org/)                     | 1.3.1             | zapewnia funkcje inspekcji|
> |[pgcrypto](https://www.postgresql.org/docs/11/pgcrypto.html)                     | 1.3             | funkcje kryptograficzne|
> |[pgrouting](https://pgrouting.org/)                    | 2.6.2           | Rozszerzenie pgRouting|
> |[pgrowlocks](https://www.postgresql.org/docs/11/pgrowlocks.html)                   | 1,2             | Pokaż informacje o blokowaniu na poziomie wiersza|
> |[pgstattuple](https://www.postgresql.org/docs/11/pgstattuple.html)                  | 1.5             | Pokaż statystyki na poziomie krotki|
> |[pg_buffercache](https://www.postgresql.org/docs/11/pgbuffercache.html)               | 1.3             | Sprawdzanie udostępnionej pamięci podręcznej buforu|
> |[pg_partman](https://github.com/pgpartman/pg_partman)                   | 4.0.0           | Rozszerzenie do zarządzania tabelami partycjonowanymi według czasu lub identyfikatora|
> |[pg_prewarm](https://www.postgresql.org/docs/11/pgprewarm.html)                   | 1,2             | dane relacji przedgrzanej|
> |[pg_stat_statements](https://www.postgresql.org/docs/11/pgstatstatements.html)           | 1.6             | Śledź statystyki wykonywania wszystkich wykonanych instrukcji SQL|
> |[pg_trgm](https://www.postgresql.org/docs/11/pgtrgm.html)                      | 1.4             | Pomiar podobieństwa tekstu i wyszukiwanie indeksu na podstawie trigrams|
> |[plpgsql](https://www.postgresql.org/docs/11/plpgsql.html)                      | 1.0             | Język proceduralny PL/pgSQL|
> |[plv8](https://plv8.github.io/)                         | 2.3.11          | PL/JavaScript (V8) — zaufany Język proceduralny|
> |[postgis](https://www.postgis.net/)                      | 2.5.1           | PostGIS geometrii, Geografia i typy przestrzenne i funkcje rastrowe|
> |[postgis_sfcgal](https://www.postgis.net/)               | 2.5.1           | PostGIS funkcje SFCGAL|
> |[postgis_tiger_geocoder](https://www.postgis.net/)       | 2.5.1           | PostGIS geokodera Tiger i odwrotna geokod|
> |[postgis_topology](https://postgis.net/docs/Topology.html)             | 2.5.1           | Typy i funkcje przestrzenne topologii PostGIS|
> |[postgres_fdw](https://www.postgresql.org/docs/11/postgres-fdw.html)                 | 1.0             | otoka danych obcych dla zdalnych serwerów PostgreSQL|
> |[tablefunc](https://www.postgresql.org/docs/11/tablefunc.html)                    | 1.0             | funkcje, które manipulują całymi tabelami, w tym krzyżowo|
> |[timescaledb](https://docs.timescale.com/latest)                    | 1.3.2             | Włącza skalowalne i złożone zapytania dla danych szeregów czasowych|
> |[deakcentowanie](https://www.postgresql.org/docs/11/unaccent.html)                     | 1,1             | słownik wyszukiwania tekstu, który usuwa akcenty|
> |[identyfikator UUID — OSSP](https://www.postgresql.org/docs/11/uuid-ossp.html)                    | 1,1             | Generuj unikatowe identyfikatory uniwersalne (UUID)|

## <a name="postgres-10-extensions"></a>Rozszerzenia Postgres 10 

Następujące rozszerzenia są dostępne na serwerach Azure Database for PostgreSQL z Postgres wersja 10.

> [!div class="mx-tableFixed"]
> | **Wewnętrzny**| **Wersja rozszerzenia** | **Opis** |
> |---|---|---|
> |[address_standardizer](http://postgis.net/docs/Address_Standardizer.html)         | 2.5.1           | Służy do analizowania adresu w elementach składowych. |
> |[address_standardizer_data_us](http://postgis.net/docs/Address_Standardizer.html) | 2.5.1           | Przykład dotyczący zestawu danych standaryzacji z nami|
> |[btree_gin](https://www.postgresql.org/docs/10/btree-gin.html)                    | 1.3             | Obsługa indeksowania wspólnych typów danych w ĄTEK|
> |[btree_gist](https://www.postgresql.org/docs/10/btree-gist.html)                   | 1.5             | Obsługa indeksowania wspólnych typów danych w rejestrze|
> |[chkpass](https://www.postgresql.org/docs/10/chkpass.html)                       | 1.0             | Typ danych dla haseł szyfrowanych autoszyfrowanie|
> |[citext](https://www.postgresql.org/docs/10/citext.html)                       | 1.4             | Typ danych dla ciągów znaków bez uwzględniania wielkości liter|
> |[modułów](https://www.postgresql.org/docs/10/cube.html)                         | 1,2             | Typ danych dla modułów wielowymiarowych|
> |[dblink](https://www.postgresql.org/docs/10/dblink.html)                       | 1,2             | Łączenie z innymi bazami danych PostgreSQL z poziomu bazy danych|
> |[dict_int](https://www.postgresql.org/docs/10/dict-int.html)                     | 1.0             | szablon słownika wyszukiwania tekstu dla liczb całkowitych|
> |[earthdistance](https://www.postgresql.org/docs/10/earthdistance.html)                | 1,1             | Oblicz duże odległości na powierzchni ziemi|
> |[fuzzystrmatch](https://www.postgresql.org/docs/10/fuzzystrmatch.html)                | 1,1             | Określ podobieństwa i odległość między ciągami|
> |[hstore](https://www.postgresql.org/docs/10/hstore.html)                       | 1.4             | Typ danych do przechowywania zestawów par (klucz, wartość)|
> |[hypopg](https://hypopg.readthedocs.io/en/latest/)                       | 1.1.1           | Indeksy hipotetyczne dla PostgreSQL|
> |[intarray](https://www.postgresql.org/docs/10/intarray.html)                     | 1,2             | Obsługa funkcji, operatorów i indeksów dla tablic 1-D liczb całkowitych|
> |[jest](https://www.postgresql.org/docs/10/isn.html)                          | 1,1             | typy danych dla międzynarodowych standardów numerowania produktów|
> |[ltree](https://www.postgresql.org/docs/10/ltree.html)                        | 1,1             | Typ danych dla struktur hierarchicznych, takich jak drzewa|
> |[orafce](https://github.com/orafce/orafce)                       | 3.7             | Funkcje i operatory, które emulują podzestaw funkcji i pakietów z RDBMS komercyjnych|
> |[pgaudit](https://www.pgaudit.org/)                     | 1,2             | zapewnia funkcje inspekcji|
> |[pgcrypto](https://www.postgresql.org/docs/10/pgcrypto.html)                     | 1.3             | funkcje kryptograficzne|
> |[pgrouting](https://pgrouting.org/)                    | 2.5.2           | Rozszerzenie pgRouting|
> |[pgrowlocks](https://www.postgresql.org/docs/10/pgrowlocks.html)                   | 1,2             | Pokaż informacje o blokowaniu na poziomie wiersza|
> |[pgstattuple](https://www.postgresql.org/docs/10/pgstattuple.html)                  | 1.5             | Pokaż statystyki na poziomie krotki|
> |[pg_buffercache](https://www.postgresql.org/docs/10/pgbuffercache.html)               | 1.3             | Sprawdzanie udostępnionej pamięci podręcznej buforu|
> |[pg_partman](https://github.com/pgpartman/pg_partman)                   | 2.6.3           | Rozszerzenie do zarządzania tabelami partycjonowanymi według czasu lub identyfikatora|
> |[pg_prewarm](https://www.postgresql.org/docs/10/pgprewarm.html)                   | 1,1             | dane relacji przedgrzanej|
> |[pg_stat_statements](https://www.postgresql.org/docs/10/pgstatstatements.html)           | 1.6             | Śledź statystyki wykonywania wszystkich wykonanych instrukcji SQL|
> |[pg_trgm](https://www.postgresql.org/docs/10/pgtrgm.html)                      | 1.3             | Pomiar podobieństwa tekstu i wyszukiwanie indeksu na podstawie trigrams|
> |[plpgsql](https://www.postgresql.org/docs/10/plpgsql.html)                      | 1.0             | Język proceduralny PL/pgSQL|
> |[plv8](https://plv8.github.io/)                         | 2.1.0          | PL/JavaScript (V8) — zaufany Język proceduralny|
> |[postgis](https://www.postgis.net/)                      | zasadniczy           | PostGIS geometrii, Geografia i typy przestrzenne i funkcje rastrowe|
> |[postgis_sfcgal](https://www.postgis.net/)               | zasadniczy           | PostGIS funkcje SFCGAL|
> |[postgis_tiger_geocoder](https://www.postgis.net/)       | zasadniczy           | PostGIS geokodera Tiger i odwrotna geokod|
> |[postgis_topology](https://postgis.net/docs/Topology.html)             | zasadniczy           | Typy i funkcje przestrzenne topologii PostGIS|
> |[postgres_fdw](https://www.postgresql.org/docs/10/postgres-fdw.html)                 | 1.0             | otoka danych obcych dla zdalnych serwerów PostgreSQL|
> |[tablefunc](https://www.postgresql.org/docs/10/tablefunc.html)                    | 1.0             | funkcje, które manipulują całymi tabelami, w tym krzyżowo|
> |[timescaledb](https://docs.timescale.com/latest)                    | 1.1.1             | Włącza skalowalne i złożone zapytania dla danych szeregów czasowych|
> |[deakcentowanie](https://www.postgresql.org/docs/10/unaccent.html)                     | 1,1             | słownik wyszukiwania tekstu, który usuwa akcenty|
> |[identyfikator UUID — OSSP](https://www.postgresql.org/docs/10/uuid-ossp.html)                    | 1,1             | Generuj unikatowe identyfikatory uniwersalne (UUID)|

## <a name="postgres-96-extensions"></a>Rozszerzenia Postgres 9,6 

Następujące rozszerzenia są dostępne na serwerach Azure Database for PostgreSQL, które mają Postgres w wersji 9,6.

> [!div class="mx-tableFixed"]
> | **Wewnętrzny**| **Wersja rozszerzenia** | **Opis** |
> |---|---|---|
> |[address_standardizer](http://postgis.net/docs/Address_Standardizer.html)         | 2.3.2           | Służy do analizowania adresu w elementach składowych. |
> |[address_standardizer_data_us](http://postgis.net/docs/Address_Standardizer.html) | 2.3.2           | Przykład dotyczący zestawu danych standaryzacji z nami|
> |[btree_gin](https://www.postgresql.org/docs/9.6/btree-gin.html)                    | 1.0             | Obsługa indeksowania wspólnych typów danych w ĄTEK|
> |[btree_gist](https://www.postgresql.org/docs/9.6/btree-gist.html)                   | 1,2             | Obsługa indeksowania wspólnych typów danych w rejestrze|
> |[chkpass](https://www.postgresql.org/docs/9.6/chkpass.html)                       | 1.0             | Typ danych dla haseł szyfrowanych autoszyfrowanie|
> |[citext](https://www.postgresql.org/docs/9.6/citext.html)                       | 1.3             | Typ danych dla ciągów znaków bez uwzględniania wielkości liter|
> |[modułów](https://www.postgresql.org/docs/9.6/cube.html)                         | 1,2             | Typ danych dla modułów wielowymiarowych|
> |[dblink](https://www.postgresql.org/docs/9.6/dblink.html)                       | 1,2             | Łączenie z innymi bazami danych PostgreSQL z poziomu bazy danych|
> |[dict_int](https://www.postgresql.org/docs/9.6/dict-int.html)                     | 1.0             | szablon słownika wyszukiwania tekstu dla liczb całkowitych|
> |[earthdistance](https://www.postgresql.org/docs/9.6/earthdistance.html)                | 1,1             | Oblicz duże odległości na powierzchni ziemi|
> |[fuzzystrmatch](https://www.postgresql.org/docs/9.6/fuzzystrmatch.html)                | 1,1             | Określ podobieństwa i odległość między ciągami|
> |[hstore](https://www.postgresql.org/docs/9.6/hstore.html)                       | 1.4             | Typ danych do przechowywania zestawów par (klucz, wartość)|
> |[hypopg](https://hypopg.readthedocs.io/en/latest/)                       | 1.1.1           | Indeksy hipotetyczne dla PostgreSQL|
> |[intarray](https://www.postgresql.org/docs/9.6/intarray.html)                     | 1,2             | Obsługa funkcji, operatorów i indeksów dla tablic 1-D liczb całkowitych|
> |[jest](https://www.postgresql.org/docs/9.6/isn.html)                          | 1,1             | typy danych dla międzynarodowych standardów numerowania produktów|
> |[ltree](https://www.postgresql.org/docs/9.6/ltree.html)                        | 1,1             | Typ danych dla struktur hierarchicznych, takich jak drzewa|
> |[orafce](https://github.com/orafce/orafce)                       | 3.7             | Funkcje i operatory, które emulują podzestaw funkcji i pakietów z RDBMS komercyjnych|
> |[pgaudit](https://www.pgaudit.org/)                     | 1.1.2             | zapewnia funkcje inspekcji|
> |[pgcrypto](https://www.postgresql.org/docs/9.6/pgcrypto.html)                     | 1.3             | funkcje kryptograficzne|
> |[pgrouting](https://pgrouting.org/)                    | 2.3.2           | Rozszerzenie pgRouting|
> |[pgrowlocks](https://www.postgresql.org/docs/9.6/pgrowlocks.html)                   | 1,2             | Pokaż informacje o blokowaniu na poziomie wiersza|
> |[pgstattuple](https://www.postgresql.org/docs/9.6/pgstattuple.html)                  | 1.4             | Pokaż statystyki na poziomie krotki|
> |[pg_buffercache](https://www.postgresql.org/docs/9.6/pgbuffercache.html)               | 1,2             | Sprawdzanie udostępnionej pamięci podręcznej buforu|
> |[pg_partman](https://github.com/pgpartman/pg_partman)                   | 2.6.3           | Rozszerzenie do zarządzania tabelami partycjonowanymi według czasu lub identyfikatora|
> |[pg_prewarm](https://www.postgresql.org/docs/9.6/pgprewarm.html)                   | 1,1             | dane relacji przedgrzanej|
> |[pg_stat_statements](https://www.postgresql.org/docs/9.6/pgstatstatements.html)           | 1.4             | Śledź statystyki wykonywania wszystkich wykonanych instrukcji SQL|
> |[pg_trgm](https://www.postgresql.org/docs/9.6/pgtrgm.html)                      | 1.3             | Pomiar podobieństwa tekstu i wyszukiwanie indeksu na podstawie trigrams|
> |[plpgsql](https://www.postgresql.org/docs/9.6/plpgsql.html)                      | 1.0             | Język proceduralny PL/pgSQL|
> |[plv8](https://plv8.github.io/)                         | 2.1.0          | PL/JavaScript (V8) — zaufany Język proceduralny|
> |[postgis](https://www.postgis.net/)                      | 2.3.2           | PostGIS geometrii, Geografia i typy przestrzenne i funkcje rastrowe|
> |[postgis_sfcgal](https://www.postgis.net/)               | 2.3.2           | PostGIS funkcje SFCGAL|
> |[postgis_tiger_geocoder](https://www.postgis.net/)       | 2.3.2           | PostGIS geokodera Tiger i odwrotna geokod|
> |[postgis_topology](https://postgis.net/docs/Topology.html)             | 2.3.2           | Typy i funkcje przestrzenne topologii PostGIS|
> |[postgres_fdw](https://www.postgresql.org/docs/9.6/postgres-fdw.html)                 | 1.0             | otoka danych obcych dla zdalnych serwerów PostgreSQL|
> |[tablefunc](https://www.postgresql.org/docs/9.6/tablefunc.html)                    | 1.0             | funkcje, które manipulują całymi tabelami, w tym krzyżowo|
> |[timescaledb](https://docs.timescale.com/latest)                    | 1.1.1             | Włącza skalowalne i złożone zapytania dla danych szeregów czasowych|
> |[deakcentowanie](https://www.postgresql.org/docs/9.6/unaccent.html)                     | 1,1             | słownik wyszukiwania tekstu, który usuwa akcenty|
> |[identyfikator UUID — OSSP](https://www.postgresql.org/docs/9.6/uuid-ossp.html)                    | 1,1             | Generuj unikatowe identyfikatory uniwersalne (UUID)|

## <a name="postgres-95-extensions"></a>Rozszerzenia Postgres 9,5 

Następujące rozszerzenia są dostępne na serwerach Azure Database for PostgreSQL, które mają Postgres w wersji 9,5.

> [!div class="mx-tableFixed"]
> | **Wewnętrzny**| **Wersja rozszerzenia** | **Opis** |
> |---|---|---|
> |[address_standardizer](http://postgis.net/docs/Address_Standardizer.html)         | 2.3.0           | Służy do analizowania adresu w elementach składowych. |
> |[address_standardizer_data_us](http://postgis.net/docs/Address_Standardizer.html) | 2.3.0           | Przykład dotyczący zestawu danych standaryzacji z nami|
> |[btree_gin](https://www.postgresql.org/docs/9.5/btree-gin.html)                    | 1.0             | Obsługa indeksowania wspólnych typów danych w ĄTEK|
> |[btree_gist](https://www.postgresql.org/docs/9.5/btree-gist.html)                   | 1,1             | Obsługa indeksowania wspólnych typów danych w rejestrze|
> |[chkpass](https://www.postgresql.org/docs/9.5/chkpass.html)                       | 1.0             | Typ danych dla haseł szyfrowanych autoszyfrowanie|
> |[citext](https://www.postgresql.org/docs/9.5/citext.html)                       | 1,1             | Typ danych dla ciągów znaków bez uwzględniania wielkości liter|
> |[modułów](https://www.postgresql.org/docs/9.5/cube.html)                         | 1.0             | Typ danych dla modułów wielowymiarowych|
> |[dblink](https://www.postgresql.org/docs/9.5/dblink.html)                       | 1,1             | Łączenie z innymi bazami danych PostgreSQL z poziomu bazy danych|
> |[dict_int](https://www.postgresql.org/docs/9.5/dict-int.html)                     | 1.0             | szablon słownika wyszukiwania tekstu dla liczb całkowitych|
> |[earthdistance](https://www.postgresql.org/docs/9.5/earthdistance.html)                | 1.0             | Oblicz duże odległości na powierzchni ziemi|
> |[fuzzystrmatch](https://www.postgresql.org/docs/9.5/fuzzystrmatch.html)                | 1.0             | Określ podobieństwa i odległość między ciągami|
> |[hstore](https://www.postgresql.org/docs/9.5/hstore.html)                       | 1.3             | Typ danych do przechowywania zestawów par (klucz, wartość)|
> |[hypopg](https://hypopg.readthedocs.io/en/latest/)                       | 1.1.1           | Indeksy hipotetyczne dla PostgreSQL|
> |[intarray](https://www.postgresql.org/docs/9.5/intarray.html)                     | 1.0             | Obsługa funkcji, operatorów i indeksów dla tablic 1-D liczb całkowitych|
> |[jest](https://www.postgresql.org/docs/9.5/isn.html)                          | 1.0             | typy danych dla międzynarodowych standardów numerowania produktów|
> |[ltree](https://www.postgresql.org/docs/9.5/ltree.html)                        | 1.0             | Typ danych dla struktur hierarchicznych, takich jak drzewa|
> |[orafce](https://github.com/orafce/orafce)                       | 3.7             | Funkcje i operatory, które emulują podzestaw funkcji i pakietów z RDBMS komercyjnych|
> |[pgaudit](https://www.pgaudit.org/)                     | 1.0.7             | zapewnia funkcje inspekcji|
> |[pgcrypto](https://www.postgresql.org/docs/9.5/pgcrypto.html)                     | 1,2             | funkcje kryptograficzne|
> |[pgrouting](https://pgrouting.org/)                    | 2.3.0           | Rozszerzenie pgRouting|
> |[pgrowlocks](https://www.postgresql.org/docs/9.5/pgrowlocks.html)                   | 1,1             | Pokaż informacje o blokowaniu na poziomie wiersza|
> |[pgstattuple](https://www.postgresql.org/docs/9.5/pgstattuple.html)                  | 1.3             | Pokaż statystyki na poziomie krotki|
> |[pg_buffercache](https://www.postgresql.org/docs/9.5/pgbuffercache.html)               | 1,1             | Sprawdzanie udostępnionej pamięci podręcznej buforu|
> |[pg_partman](https://github.com/pgpartman/pg_partman)                   | 2.6.3           | Rozszerzenie do zarządzania tabelami partycjonowanymi według czasu lub identyfikatora|
> |[pg_prewarm](https://www.postgresql.org/docs/9.5/pgprewarm.html)                   | 1.0             | dane relacji przedgrzanej|
> |[pg_stat_statements](https://www.postgresql.org/docs/9.5/pgstatstatements.html)           | 1.3             | Śledź statystyki wykonywania wszystkich wykonanych instrukcji SQL|
> |[pg_trgm](https://www.postgresql.org/docs/9.5/pgtrgm.html)                      | 1,1             | Pomiar podobieństwa tekstu i wyszukiwanie indeksu na podstawie trigrams|
> |[plpgsql](https://www.postgresql.org/docs/9.5/plpgsql.html)                      | 1.0             | Język proceduralny PL/pgSQL|
> |[postgis](https://www.postgis.net/)                      | 2.3.0           | PostGIS geometrii, Geografia i typy przestrzenne i funkcje rastrowe|
> |[postgis_sfcgal](https://www.postgis.net/)               | 2.3.0           | PostGIS funkcje SFCGAL|
> |[postgis_tiger_geocoder](https://www.postgis.net/)       | 2.3.0           | PostGIS geokodera Tiger i odwrotna geokod|
> |[postgis_topology](https://postgis.net/docs/Topology.html)             | 2.3.0           | Typy i funkcje przestrzenne topologii PostGIS|
> |[postgres_fdw](https://www.postgresql.org/docs/9.5/postgres-fdw.html)                 | 1.0             | otoka danych obcych dla zdalnych serwerów PostgreSQL|
> |[tablefunc](https://www.postgresql.org/docs/9.5/tablefunc.html)                    | 1.0             | funkcje, które manipulują całymi tabelami, w tym krzyżowo|
> |[deakcentowanie](https://www.postgresql.org/docs/9.5/unaccent.html)                     | 1.0             | słownik wyszukiwania tekstu, który usuwa akcenty|
> |[identyfikator UUID — OSSP](https://www.postgresql.org/docs/9.5/uuid-ossp.html)                    | 1.0             | Generuj unikatowe identyfikatory uniwersalne (UUID)|


## <a name="pg_stat_statements"></a>pg_stat_statements
[Pg_stat_statements rozszerzenie](https://www.postgresql.org/docs/current/pgstatstatements.html) jest wstępnie załadowane na każdym serwerze Azure Database for PostgreSQL, aby zapewnić możliwość śledzenia statystyk wykonywania instrukcji SQL.
Ustawienie `pg_stat_statements.track` , które kontroluje, jakie instrukcje są zliczane przez rozszerzenie, domyślnie to `top` , oznacza, że wszystkie instrukcje wydawane bezpośrednio przez klientów są śledzone. Dwa inne poziomy śledzenia to `none` i `all` . To ustawienie można skonfigurować jako parametr serwera za pomocą [Azure Portal](./howto-configure-server-parameters-using-portal.md) lub [interfejsu wiersza polecenia platformy Azure](./howto-configure-server-parameters-using-cli.md).

Istnieje kompromis między informacjami o wykonywaniu zapytania pg_stat_statements zapewnia i wpływ na wydajność serwera podczas rejestrowania każdej instrukcji SQL. Jeśli nie korzystasz aktywnie z rozszerzenia pg_stat_statements, zalecamy ustawienie wartości `pg_stat_statements.track` `none` . Zwróć uwagę na to, że niektóre usługi monitorowania innych firm mogą polegać na pg_stat_statements w celu dostarczenia szczegółowych informacji o wydajności zapytań, więc Potwierdź, czy tak się dzieje.

## <a name="dblink-and-postgres_fdw"></a>dblink i postgres_fdw
[dblink](https://www.postgresql.org/docs/current/contrib-dblink-function.html) i [postgres_fdw](https://www.postgresql.org/docs/current/postgres-fdw.html) umożliwiają łączenie się z jednego serwera PostgreSQL z innym lub z inną bazą danych na tym samym serwerze. Serwer otrzymujący musi zezwalać na połączenia z serwera wysyłającego za pośrednictwem jego zapory. Korzystając z tych rozszerzeń do łączenia się między serwerami Azure Database for PostgreSQL, można to zrobić przez ustawienie opcji "Zezwalaj na dostęp do usług platformy Azure" na wartość włączone. Jest to również wymagane, jeśli chcesz użyć rozszerzeń do zapętlenia z powrotem do tego samego serwera. Ustawienie "Zezwalaj na dostęp do usług platformy Azure" można znaleźć na stronie Azure Portal serwera Postgres, w obszarze zabezpieczenia połączeń. Włączenie opcji "Zezwalaj na dostęp do usług platformy Azure" spowoduje umieszczenie wszystkich adresów IP platformy Azure na liście dozwolonych.

Obecnie połączenia wychodzące z Azure Database for PostgreSQL nie są obsługiwane, z wyjątkiem połączeń z innymi serwerami Azure Database for PostgreSQL w tym samym regionie.

## <a name="uuid"></a>uuid
Jeśli planujesz użyć `uuid_generate_v4()` z [rozszerzenia UUID-OSSP](https://www.postgresql.org/docs/current/uuid-ossp.html), rozważ porównanie z `gen_random_uuid()` [rozszerzeniem pgcrypto](https://www.postgresql.org/docs/current/pgcrypto.html) w celu uzyskania korzyści z wydajności.

## <a name="pgaudit"></a>pgAudit
[Rozszerzenie pgAudit](https://github.com/pgaudit/pgaudit/blob/master/README.md) zapewnia rejestrowanie inspekcji sesji i obiektu. Aby dowiedzieć się, jak używać tego rozszerzenia w Azure Database for PostgreSQL, zapoznaj się z [artykułem pojęcia dotyczące inspekcji](concepts-audit.md). 

## <a name="pg_prewarm"></a>pg_prewarm
Rozszerzenie pg_prewarm ładuje dane relacyjne do pamięci podręcznej. Przedgrzane pamięci podręczne oznacza, że zapytania mają większe czasy reakcji podczas pierwszego uruchomienia po ponownym uruchomieniu. W Postgres 10 i poniżej, przedgrzane jest wykonywane ręcznie przy użyciu [funkcji przedgrzanej](https://www.postgresql.org/docs/10/pgprewarm.html).

W Postgres 11 i nowszych można skonfigurować [Automatyczne](https://www.postgresql.org/docs/current/pgprewarm.html)rozgrzewanie. Musisz dołączyć pg_prewarm na `shared_preload_libraries` liście parametrów i ponownie uruchomić serwer, aby zastosować zmianę. Parametry można ustawić na podstawie szablonu [Azure Portal](howto-configure-server-parameters-using-portal.md), [CLI](howto-configure-server-parameters-using-cli.md), interfejsu API REST lub usługi ARM. 

## <a name="timescaledb"></a>TimescaleDB
TimescaleDB to baza danych szeregów czasowych spakowana jako rozszerzenie dla PostgreSQL. TimescaleDB zapewnia funkcje analityczne zorientowane czasowo, optymalizacje oraz skaluje Postgres dla obciążeń szeregów czasowych.

[Dowiedz się więcej na temat TimescaleDB](https://docs.timescale.com/latest), zarejestrowanego znaku towarowego [, Inc.](https://www.timescale.com/). Azure Database for PostgreSQL udostępnia TimescaleDB [Apache-2](https://www.timescale.com/legal/licenses).

### <a name="installing-timescaledb"></a>Instalowanie TimescaleDB
Aby zainstalować TimescaleDB, należy dołączyć go do udostępnionych bibliotek wstępnego ładowania serwera. Zmiana `shared_preload_libraries` parametru Postgres wymaga **ponownego uruchomienia serwera** . Parametry można zmienić za pomocą [Azure Portal](howto-configure-server-parameters-using-portal.md) lub [interfejsu wiersza polecenia platformy Azure](howto-configure-server-parameters-using-cli.md).

Przy użyciu [Azure Portal](https://portal.azure.com/):

1. Wybierz serwer usługi Azure Database for PostgreSQL.

2. Na pasku bocznym wybierz opcję **parametry serwera**.

3. Wyszukaj `shared_preload_libraries` parametr.

4. Wybierz pozycję **TimescaleDB**.

5. Wybierz pozycję **Zapisz** , aby zachować zmiany. Po zapisaniu zmiany otrzymujesz powiadomienie. 

6. Po powiadomieniu **ponownie uruchom** serwer, aby zastosować te zmiany. Aby dowiedzieć się, jak ponownie uruchomić serwer, zobacz [Ponowne uruchamianie serwera Azure Database for PostgreSQL](howto-restart-server-portal.md).


Teraz można włączyć TimescaleDB w bazie danych Postgres. Nawiąż połączenie z bazą danych i wydaj następujące polecenie:
```sql
CREATE EXTENSION IF NOT EXISTS timescaledb CASCADE;
```
> [!TIP]
> Jeśli zobaczysz błąd, upewnij się, że [serwer został ponownie uruchomiony](howto-restart-server-portal.md) po zapisaniu shared_preload_libraries. 

Teraz można utworzyć tabelę TimescaleDB [od podstaw](https://docs.timescale.com/getting-started/creating-hypertables) lub migrować [istniejące dane szeregów czasowych w PostgreSQL](https://docs.timescale.com/getting-started/migrating-data).

### <a name="restoring-a-timescale-database"></a>Przywracanie bazy danych skali czasu
Aby przywrócić bazę danych skali czasu przy użyciu pg_dump i pg_restore, należy uruchomić dwa procedury pomocnika w docelowej bazie danych: `timescaledb_pre_restore()` i `timescaledb_post restore()` .

Najpierw Przygotuj docelową bazę danych:

```SQL
--create the new database where you'll perform the restore
CREATE DATABASE tutorial;
\c tutorial --connect to the database 
CREATE EXTENSION timescaledb;

SELECT timescaledb_pre_restore();
```

Teraz można uruchomić pg_dump w oryginalnej bazie danych, a następnie pg_restore. Po przywróceniu należy uruchomić następujące polecenie w przywróconej bazie danych:

```SQL
SELECT timescaledb_post_restore();
```


## <a name="next-steps"></a>Następne kroki
Jeśli nie widzisz rozszerzenia, którego chcesz użyć, daj nam znać. Zagłosuj na istniejące żądania lub Utwórz nowe żądania opinii na naszym [forum opinii](https://feedback.azure.com/forums/597976-azure-database-for-postgresql).