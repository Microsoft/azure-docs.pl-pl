---
title: Rozszerzenia — funkcja do skalowania (Citus) — Azure Database for PostgreSQL
description: Zawiera opis możliwości rozszerzania funkcjonalności bazy danych przy użyciu rozszerzeń w Azure Database for PostgreSQL-Citus.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 04/07/2021
ms.openlocfilehash: 221d8b1d9fdd40a71bcfdeed57c02451e44052f2
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/07/2021
ms.locfileid: "107012766"
---
# <a name="postgresql-extensions-in-azure-database-for-postgresql--hyperscale-citus"></a>Rozszerzenia PostgreSQL w Azure Database for PostgreSQL — funkcja do skalowania (Citus)

Baza danych PostgreSQL umożliwia rozszerzanie funkcjonalności bazy danych za pomocą rozszerzeń. Rozszerzenia umożliwiają zgrupowanie wielu powiązanych obiektów SQL razem w jednym pakiecie, który można załadować lub usunąć z bazy danych za pomocą jednego polecenia. Po załadowaniu do bazy danych rozszerzenia mogą działać jak funkcje wbudowane. Aby uzyskać więcej informacji na temat rozszerzeń PostgreSQL, zobacz [obiekty powiązane z pakietami w rozszerzeniu](https://www.postgresql.org/docs/current/static/extend-extensions.html).

## <a name="use-postgresql-extensions"></a>Korzystanie z rozszerzeń PostgreSQL

Rozszerzenia PostgreSQL muszą być zainstalowane w bazie danych, zanim będzie można z nich korzystać. Aby zainstalować określone rozszerzenie, uruchom polecenie [Utwórz rozszerzenie](https://www.postgresql.org/docs/current/static/sql-createextension.html) z narzędzia PSQL w celu załadowania spakowanych obiektów do bazy danych.

Azure Database for PostgreSQL-Citus) obecnie obsługuje podzestaw głównych rozszerzeń, jak pokazano tutaj. Rozszerzenia inne niż wymienione na liście nie są obsługiwane. Nie można utworzyć własnego rozszerzenia za pomocą Azure Database for PostgreSQL.

## <a name="extensions-supported-by-azure-database-for-postgresql"></a>Rozszerzenia obsługiwane przez Azure Database for PostgreSQL

W poniższej tabeli wymieniono standardowe rozszerzenia PostgreSQL, które są obecnie obsługiwane przez Azure Database for PostgreSQL. Te informacje są również dostępne po uruchomieniu programu `SELECT * FROM pg_available_extensions;` .

Wersje każdego rozszerzenia zainstalowanych w grupie serwerów czasami różnią się w zależności od wersji PostgreSQL (11, 12 lub 13). W tabeli wymieniono wersje rozszerzeń na wersję bazy danych.

### <a name="citus-extension"></a>Rozszerzenie Citus

> [!div class="mx-tableFixed"]
> | **Wewnętrzny** | **Opis** | **PG 11** | **PG 12** | **PG 13** |
> |---|---|---|---|---|
> | [citus](https://github.com/citusdata/citus) | Citus rozproszonej bazy danych. | 9,5-1 | 9,5-1 | 10.0 — 2 |

### <a name="data-types-extensions"></a>Rozszerzenia typów danych

> [!div class="mx-tableFixed"]
> | **Wewnętrzny** | **Opis** | **PG 11** | **PG 12** | **PG 13** |
> |---|---|---|---|---|
> | [citext](https://www.postgresql.org/docs/current/static/citext.html) | Zawiera typ ciągu znaków bez uwzględniania wielkości liter. | 1.5 | 1.6 | 1.6 |
> | [modułów](https://www.postgresql.org/docs/current/static/cube.html) | Udostępnia typ danych dla modułów wielowymiarowych. | 1.4 | 1.4 | 1.4 |
> | [hll](https://github.com/citusdata/postgresql-hll) | Udostępnia strukturę danych HyperLogLog. | 2,14 | 2.15 | 2.15 |
> | [hstore](https://www.postgresql.org/docs/current/static/hstore.html) | Dostarcza typ danych do przechowywania zestawów par klucz-wartość. | 1.5 | 1.6 | 1.7 |
> | [jest](https://www.postgresql.org/docs/current/static/isn.html) | Zapewnia typy danych dla międzynarodowych standardów numerowania produktów. | 1.2 | 1.2 | 1.2 |
> | [Lo](https://www.postgresql.org/docs/current/lo.html) | Konserwacja dużego obiektu. | 1.1 | 1.1 | 1.1 |
> | [ltree](https://www.postgresql.org/docs/current/static/ltree.html) | Dostarcza typ danych dla hierarchicznych struktur przypominających drzewa. | 1.1 | 1,1 | 1,2 |
> | [seg](https://www.postgresql.org/docs/current/seg.html) | Typ danych służący do reprezentowania segmentów linii lub interwałów zmiennoprzecinkowych. | 1.3 | 1.3 | 1.3 |
> | [tdigest](https://github.com/tvondra/tdigest) | Typ danych na potrzeby akumulacji w wierszu statystyk opartych na rankingu, takich jak quantiles i przyciętych metod. | 1.0 | 1.0 | 1.0 |
> | [TopN](https://github.com/citusdata/postgresql-topn/) | Wpisz dla elementu Top-n JSONB. | 2.2.2 | ppkt | ppkt |

### <a name="full-text-search-extensions"></a>Rozszerzenia wyszukiwania pełnotekstowego

> [!div class="mx-tableFixed"]
> | **Wewnętrzny** | **Opis** | **PG 11** | **PG 12** | **PG 13** |
> |---|---|---|---|---|
> | [DICT \_ int](https://www.postgresql.org/docs/current/static/dict-int.html) | Udostępnia szablon słownika wyszukiwania tekstu dla liczb całkowitych. | 1.0 | 1.0 | 1.0 |
> | [DICT \_ xsyn](https://www.postgresql.org/docs/current/dict-xsyn.html) | Szablon słownika wyszukiwania tekstu dla rozszerzonego przetwarzania synonimów. | 1.0 | 1.0 | 1.0 |
> | [deakcentowanie](https://www.postgresql.org/docs/current/static/unaccent.html) | Słownik wyszukiwania tekstu, który usuwa akcenty (znaki diakrytyczne) z lexemes. | 1.1 | 1.1 | 1.1 |

### <a name="functions-extensions"></a>Rozszerzenia funkcji

> [!div class="mx-tableFixed"]
> | **Wewnętrzny** | **Opis** | **PG 11** | **PG 12** | **PG 13** |
> |---|---|---|---|---|
> | [autoinc](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.7) | Funkcje do autozwiększania wartości pól. | 1.0 | 1.0 | 1.0 |
> | [earthdistance](https://www.postgresql.org/docs/current/static/earthdistance.html) | Zapewnia środki do obliczania dużej liczby okręgów na powierzchni ziemi. | 1.1 | 1.1 | 1.1 |
> | [fuzzystrmatch](https://www.postgresql.org/docs/current/static/fuzzystrmatch.html) | Udostępnia kilka funkcji, aby określić podobieństwa i odległość między ciągami. | 1.1 | 1.1 | 1.1 |
> | [Wstaw \_ nazwę użytkownika](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.8) | Funkcje śledzenia, kto zmienił tabelę. | 1.0 | 1.0 | 1.0 |
> | [intagg](https://www.postgresql.org/docs/current/intagg.html) | Agregator i moduł wyliczający liczby całkowite (przestarzałe). | 1.1 | 1.1 | 1.1 |
> | [intarray](https://www.postgresql.org/docs/current/static/intarray.html) | Udostępnia funkcje i operatory umożliwiające manipulowanie tablicami całkowitymi bez wartości null. | 1.2 | 1.2 | 1.3 |
> | [moddatetime](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.9) | Funkcje śledzenia czasu ostatniej modyfikacji. | 1.0 | 1.0 | 1.0 |
> | [\_część PG](https://pgxn.org/dist/pg_partman/doc/pg_partman.html) | Zarządza tabelami partycjonowanymi według czasu lub identyfikatora. | 4.1 | 4.4.1 | 4.4.1 |
> | [PG \_ trgm](https://www.postgresql.org/docs/current/static/pgtrgm.html) | Oferuje funkcje i operatory umożliwiające określanie podobieństwa tekstu alfanumerycznego na podstawie dopasowywania trigram. | 1.4 | 1.4 | 1.5 |
> | [pgcrypto](https://www.postgresql.org/docs/current/static/pgcrypto.html) | Udostępnia funkcje kryptograficzne. | 1.3 | 1.3 | 1.3 |
> | [refint](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.5) | Funkcje do implementowania integralności referencyjnej (przestarzałe). | 1.0 | 1.0 | 1.0 |
> | \_Analiza sesji | Funkcje do wykonywania zapytań dotyczących tablic HStore. | | | |
> | [tablefunc](https://www.postgresql.org/docs/current/static/tablefunc.html) | Udostępnia funkcje, które manipulują całymi tabelami, w tym krzyżowo. | 1.0 | 1.0 | 1.0 |
> | [tcn](https://www.postgresql.org/docs/current/tcn.html) | Wyzwolone powiadomienia o zmianach. | 1.0 | 1.0 | 1.0 |
> | [timetravel](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.6) | Funkcje do implementowania czasu podróży. | 1.0 | | |
> | [identyfikator UUID — OSSP](https://www.postgresql.org/docs/current/static/uuid-ossp.html) | Generuje unikatowe identyfikatory uniwersalne (UUID). | 1.1 | 1.1 | 1.1 |

### <a name="index-types-extensions"></a>Rozszerzenia typów indeksów

> [!div class="mx-tableFixed"]
> | **Wewnętrzny** | **Opis** | **PG 11** | **PG 12** | **PG 13** |
> |---|---|---|---|---|
> | [lotem](https://www.postgresql.org/docs/current/bloom.html) | Metoda dostępu rozwiniętego — indeks oparty na plikach sygnatur. | 1.0 | 1.0 | 1.0 |
> | [Podczas aktywowania RowsetBulk \_ ątek](https://www.postgresql.org/docs/current/static/btree-gin.html) | Dostarcza przykładowe klasy operatorów ĄTEK, które implementują zachowanie typu B-drzewo dla określonych typów danych. | 1.3 | 1.3 | 1.3 |
> | [Podczas aktywowania RowsetBulk \_](https://www.postgresql.org/docs/current/static/btree-gist.html) | Dostarcza klasy operatorów indeksów indeksu, które implementują B-Tree. | 1.5 | 1.5 | 1.5 |

### <a name="language-extensions"></a>Rozszerzenia językowe

> [!div class="mx-tableFixed"]
> | **Wewnętrzny** | **Opis** | **PG 11** | **PG 12** | **PG 13** |
> |---|---|---|---|---|
> | [plpgsql](https://www.postgresql.org/docs/current/static/plpgsql.html) | Język proceduralny z możliwością ładowania PL/pgSQL. | 1.0 | 1.0 | 1.0 |

### <a name="miscellaneous-extensions"></a>Różne rozszerzenia

> [!div class="mx-tableFixed"]
> | **Wewnętrzny** | **Opis** | **PG 11** | **PG 12** | **PG 13** |
> |---|---|---|---|---|
> | [adminpack](https://www.postgresql.org/docs/current/adminpack.html) | Funkcje administracyjne dla PostgreSQL. | 2.0 | 2.0 | 2.1 |
> | [amcheck](https://www.postgresql.org/docs/current/amcheck.html) | Funkcje do sprawdzania integralności relacji. | 1,1 | 1,2 | 1.2 |
> | [dblink](https://www.postgresql.org/docs/current/dblink.html) | Moduł obsługujący połączenia z innymi bazami danych PostgreSQL z poziomu sesji bazy danych. Informacje o tym rozszerzeniu można znaleźć w sekcji "dblink i postgres_fdw". | 1.2 | 1.2 | 1.2 |
> | [\_FDW pliku](https://www.postgresql.org/docs/current/file-fdw.html) | Otoka danych obcych na potrzeby prostego dostępu do plików. | 1.0 | 1.0 | 1.0 |
> | [pageinspect](https://www.postgresql.org/docs/current/pageinspect.html) | Sprawdź zawartość stron bazy danych na niskim poziomie. | 1.7 | 1.7 | 1.8 |
> | [PG \_ buffercache](https://www.postgresql.org/docs/current/static/pgbuffercache.html) | Zapewnia metodę sprawdzania, co dzieje się w pamięci podręcznej buforów udostępnionych w czasie rzeczywistym. | 1.3 | 1.3 | 1.3 |
> | [PG — Strona firmy \_ cronus](https://github.com/citusdata/pg_cron) | Harmonogram zadań dla PostgreSQL. | 1.1 | 1.3 | 1.3 |
> | [PG \_ freespacemap](https://www.postgresql.org/docs/current/pgfreespacemap.html) | Obejrzyj mapę wolnego miejsca (FSM). | 1.2 | 1.2 | 1.2 |
> | [\_przedgrzana Strona](https://www.postgresql.org/docs/current/static/pgprewarm.html) | Zapewnia sposób ładowania danych relacji do pamięci podręcznej buforów. | 1.2 | 1.2 | 1.2 |
> | ["PG" — \_ \_ instrukcje](https://www.postgresql.org/docs/current/static/pgstatstatements.html) | Zapewnia metodę śledzenia statystyk wykonywania wszystkich instrukcji SQL wykonywanych przez serwer. Informacje o tym rozszerzeniu można znaleźć w sekcji "pg_stat_statements". | 1.6 | 1.7 | 1.8 |
> | [\_widoczność PG](https://www.postgresql.org/docs/current/pgvisibility.html) | Obejrzyj mapę widoczności (VM) i informacje o widoczności na poziomie strony. | 1.2 | 1.2 | 1.2 |
> | [pgrowlocks](https://www.postgresql.org/docs/current/static/pgrowlocks.html) | Zapewnia sposób wyświetlania informacji o blokowaniu na poziomie wiersza. | 1.2 | 1.2 | 1.2 |
> | [pgstattuple](https://www.postgresql.org/docs/current/static/pgstattuple.html) | Zapewnia sposób wyświetlania statystyk na poziomie krotki. | 1.5 | 1.5 | 1.5 |
> | [Postgres \_ FDW](https://www.postgresql.org/docs/current/static/postgres-fdw.html) | Otoka danych obcych używana do uzyskiwania dostępu do danych przechowywanych na zewnętrznych serwerach PostgreSQL. Informacje o tym rozszerzeniu można znaleźć w sekcji "dblink i postgres_fdw".| 1.0 | 1.0 | 1.0 |
> | [sslinfo](https://www.postgresql.org/docs/current/sslinfo.html) | Informacje o certyfikatach TLS/SSL. | 1.2 | 1.2 | 1.2 |
> | [TSM \_ \_ wierszy systemu](https://www.postgresql.org/docs/current/tsm-system-rows.html) | Metoda reklasy, która akceptuje liczbę wierszy jako limit. | 1.0 | 1.0 | 1.0 |
> | [\_czas systemowy TSM \_](https://www.postgresql.org/docs/current/tsm-system-time.html) | Metoda potraktować jako limit czas (w milisekundach). | 1.0 | 1.0 | 1.0 |
> | [xml2](https://www.postgresql.org/docs/current/xml2.html) | Zapytania XPath i XSLT. | 1.1 | 1.1 | 1.1 |


### <a name="postgis-extensions"></a>Rozszerzenia PostGIS

> [!div class="mx-tableFixed"]
> | **Wewnętrzny** | **Opis** | **PG 11** | **PG 12** | **PG 13** |
> |---|---|---|---|---|
> | [PostGIS](https://www.postgis.net/), \_ topologia PostGIS, \_ PostGIS \_ geokodera Tiger, PostGIS \_ sfcgal | Obiekty przestrzenne i geograficzne dla PostgreSQL. | 2.5.1 | 3.0.3 | 3.0.3 |
> | \_normalizacja adresów, \_ \_ \_ nawiązanie przez nas danych | Służy do analizowania adresu w elementach składowych. Używane do obsługi kroku normalizacji adresów geokodowania. | 2.5.1 | 3.0.3 | 3.0.3 |
> | PostGIS \_ sfcgal | Funkcje PostGIS SFCGAL. | 2.5.1 | 3.0.3 | 3.0.3 |
> | PostGIS \_ \_ geokodera Tiger | PostGIS geokodny Tiger i odwrotny geokod. | 2.5.1 | 3.0.3 | 3.0.3 |
> | \_topologia PostGIS | Typy i funkcje przestrzenne topologii PostGIS. | 2.5.1 | 3.0.3 | 3.0.3 |


## <a name="pg_stat_statements"></a>pg_stat_statements
[Rozszerzenie "PG \_ stat \_ Statement](https://www.postgresql.org/docs/current/pgstatstatements.html) " jest wstępnie załadowane na każdym serwerze Azure Database for PostgreSQL, aby umożliwić śledzenie statystyk wykonywania instrukcji SQL.

Ustawienie `pg_stat_statements.track` określa, jakie instrukcje są zliczane przez rozszerzenie. Wartość domyślna to `top` , co oznacza, że wszystkie instrukcje wydawane bezpośrednio przez klientów są śledzone. Dwa inne poziomy śledzenia to `none` i `all` . To ustawienie można skonfigurować jako parametr serwera za pomocą [Azure Portal](./howto-configure-server-parameters-using-portal.md) lub [interfejsu wiersza polecenia platformy Azure](./howto-configure-server-parameters-using-cli.md).

Istnieje kompromis między informacjami o wykonywaniu zapytania pg_stat_statements zapewnia i wpływ na wydajność serwera podczas rejestrowania każdej instrukcji SQL. Jeśli nie korzystasz aktywnie z rozszerzenia pg_stat_statements, zalecamy ustawienie wartości `pg_stat_statements.track` `none` . Niektóre usługi monitorowania innych firm mogą polegać na pg_stat_statements w celu dostarczenia szczegółowych informacji o wydajności zapytań, dlatego należy sprawdzić, czy tak się dzieje.

## <a name="dblink-and-postgres_fdw"></a>dblink i postgres_fdw

\_Do łączenia się z jednego serwera PostgreSQL lub innej bazy danych na tym samym serwerze można użyć dblink i Postgres FDW.  Serwer otrzymujący musi zezwalać na połączenia z serwera wysyłającego za pośrednictwem jego zapory.  Aby użyć tych rozszerzeń do nawiązywania połączeń między grupami serwerów Azure Database for PostgreSQL lub Citus), ustaw opcję **Zezwól usługom i zasobom platformy Azure na dostęp do tej grupy serwerów (lub serwera)** do systemu.  To ustawienie należy również włączyć, jeśli chcesz użyć rozszerzeń do zapętlenia z powrotem do tego samego serwera.
Ustawienie **Zezwalaj na dostęp do usług platformy Azure i zasobów do dostępu do tej grupy serwerów** można znaleźć na stronie Azure Portal grupy serwerów Citus) w obszarze **Sieć**.  Obecnie połączenia wychodzące z Azure Database for PostgreSQL pojedynczego serwera i skalowania (Citus) nie są obsługiwane, z wyjątkiem połączeń z innymi grupami serwerów Azure Database for PostgreSQL i Citus.
