---
title: Rozszerzenia — funkcja do skalowania (Citus) — Azure Database for PostgreSQL
description: Zawiera opis możliwości rozszerzania funkcjonalności bazy danych przy użyciu rozszerzeń w Azure Database for PostgreSQL-Citus.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 07/09/2020
ms.openlocfilehash: 000f8a1457298901dcfc94bc5e0923e94ba35dc7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96620906"
---
# <a name="postgresql-extensions-in-azure-database-for-postgresql--hyperscale-citus"></a>Rozszerzenia PostgreSQL w Azure Database for PostgreSQL — funkcja do skalowania (Citus)

Baza danych PostgreSQL umożliwia rozszerzanie funkcjonalności bazy danych za pomocą rozszerzeń. Rozszerzenia umożliwiają zgrupowanie wielu powiązanych obiektów SQL razem w jednym pakiecie, który można załadować lub usunąć z bazy danych za pomocą jednego polecenia. Po załadowaniu do bazy danych rozszerzenia mogą działać jak funkcje wbudowane. Aby uzyskać więcej informacji na temat rozszerzeń PostgreSQL, zobacz [obiekty powiązane z pakietami w rozszerzeniu](https://www.postgresql.org/docs/current/static/extend-extensions.html).

## <a name="use-postgresql-extensions"></a>Korzystanie z rozszerzeń PostgreSQL

Rozszerzenia PostgreSQL muszą być zainstalowane w bazie danych, zanim będzie można z nich korzystać. Aby zainstalować określone rozszerzenie, uruchom polecenie [Utwórz rozszerzenie](https://www.postgresql.org/docs/current/static/sql-createextension.html) z narzędzia PSQL w celu załadowania spakowanych obiektów do bazy danych.

Azure Database for PostgreSQL-Citus) obecnie obsługuje podzestaw głównych rozszerzeń, jak pokazano tutaj. Rozszerzenia inne niż wymienione na liście nie są obsługiwane. Nie można utworzyć własnego rozszerzenia za pomocą Azure Database for PostgreSQL.

## <a name="extensions-supported-by-azure-database-for-postgresql"></a>Rozszerzenia obsługiwane przez Azure Database for PostgreSQL

W poniższej tabeli wymieniono standardowe rozszerzenia PostgreSQL, które są obecnie obsługiwane przez Azure Database for PostgreSQL. Te informacje są również dostępne po uruchomieniu programu `SELECT * FROM pg_available_extensions;` .

### <a name="data-types-extensions"></a>Rozszerzenia typów danych

> [!div class="mx-tableFixed"]
> | **Wewnętrzny** | **Opis** |
> |---|---|
> | [citext](https://www.postgresql.org/docs/current/static/citext.html) | Zawiera typ ciągu znaków bez uwzględniania wielkości liter. |
> | [modułów](https://www.postgresql.org/docs/current/static/cube.html) | Udostępnia typ danych dla modułów wielowymiarowych. |
> | [hll](https://github.com/citusdata/postgresql-hll) | Udostępnia strukturę danych HyperLogLog. |
> | [hstore](https://www.postgresql.org/docs/current/static/hstore.html) | Dostarcza typ danych do przechowywania zestawów par klucz-wartość. |
> | [jest](https://www.postgresql.org/docs/current/static/isn.html) | Zapewnia typy danych dla międzynarodowych standardów numerowania produktów. |
> | [Lo](https://www.postgresql.org/docs/current/lo.html) | Konserwacja dużego obiektu. |
> | [ltree](https://www.postgresql.org/docs/current/static/ltree.html) | Dostarcza typ danych dla hierarchicznych struktur przypominających drzewa. |
> | [seg](https://www.postgresql.org/docs/current/seg.html) | Typ danych służący do reprezentowania segmentów linii lub interwałów zmiennoprzecinkowych. |
> | [tdigest](https://github.com/tvondra/tdigest) | Typ danych na potrzeby akumulacji w wierszu statystyk opartych na rankingu, takich jak quantiles i przyciętych metod. |
> | [TopN](https://github.com/citusdata/postgresql-topn/) | Wpisz dla elementu Top-n JSONB. |

### <a name="full-text-search-extensions"></a>Rozszerzenia wyszukiwania pełnotekstowego

> [!div class="mx-tableFixed"]
> | **Wewnętrzny** | **Opis** |
> |---|---|
> | [DICT \_ int](https://www.postgresql.org/docs/current/static/dict-int.html) | Udostępnia szablon słownika wyszukiwania tekstu dla liczb całkowitych. |
> | [DICT \_ xsyn](https://www.postgresql.org/docs/current/dict-xsyn.html) | Szablon słownika wyszukiwania tekstu dla rozszerzonego przetwarzania synonimów. |
> | [deakcentowanie](https://www.postgresql.org/docs/current/static/unaccent.html) | Słownik wyszukiwania tekstu, który usuwa akcenty (znaki diakrytyczne) z lexemes. |

### <a name="functions-extensions"></a>Rozszerzenia funkcji

> [!div class="mx-tableFixed"]
> | **Wewnętrzny** | **Opis** |
> |---|---|
> | [autoinc](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.7) | Funkcje do autozwiększania wartości pól. |
> | [earthdistance](https://www.postgresql.org/docs/current/static/earthdistance.html) | Zapewnia środki do obliczania dużej liczby okręgów na powierzchni ziemi. |
> | [fuzzystrmatch](https://www.postgresql.org/docs/current/static/fuzzystrmatch.html) | Udostępnia kilka funkcji, aby określić podobieństwa i odległość między ciągami. |
> | [Wstaw \_ nazwę użytkownika](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.8) | Funkcje śledzenia, kto zmienił tabelę. |
> | [intagg](https://www.postgresql.org/docs/current/intagg.html) | Agregator i moduł wyliczający liczby całkowite (przestarzałe). |
> | [intarray](https://www.postgresql.org/docs/current/static/intarray.html) | Udostępnia funkcje i operatory umożliwiające manipulowanie tablicami całkowitymi bez wartości null. |
> | [moddatetime](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.9) | Funkcje śledzenia czasu ostatniej modyfikacji. |
> | [\_część PG](https://pgxn.org/dist/pg_partman/doc/pg_partman.html) | Zarządza tabelami partycjonowanymi według czasu lub identyfikatora. |
> | [PG \_ trgm](https://www.postgresql.org/docs/current/static/pgtrgm.html) | Oferuje funkcje i operatory umożliwiające określanie podobieństwa tekstu alfanumerycznego na podstawie dopasowywania trigram. |
> | [pgcrypto](https://www.postgresql.org/docs/current/static/pgcrypto.html) | Udostępnia funkcje kryptograficzne. |
> | [refint](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.5) | Funkcje do implementowania integralności referencyjnej (przestarzałe). |
> | \_Analiza sesji | Funkcje do wykonywania zapytań dotyczących tablic HStore. |
> | [tablefunc](https://www.postgresql.org/docs/current/static/tablefunc.html) | Udostępnia funkcje, które manipulują całymi tabelami, w tym krzyżowo. |
> | [tcn](https://www.postgresql.org/docs/current/tcn.html) | Wyzwolone powiadomienia o zmianach. |
> | [timetravel](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.6) | Funkcje do implementowania czasu podróży. |
> | [identyfikator UUID — OSSP](https://www.postgresql.org/docs/current/static/uuid-ossp.html) | Generuje unikatowe identyfikatory uniwersalne (UUID). |

### <a name="hyperscale-citus-extensions"></a>Rozszerzenia funkcji przeskalowania (Citus)

> [!div class="mx-tableFixed"]
> | **Wewnętrzny** | **Opis** |
> |---|---|
> | [citus](https://github.com/citusdata/citus) | Citus rozproszonej bazy danych. |

### <a name="index-types-extensions"></a>Rozszerzenia typów indeksów

> [!div class="mx-tableFixed"]
> | **Wewnętrzny** | **Opis** |
> |---|---|
> | [lotem](https://www.postgresql.org/docs/current/bloom.html) | Metoda dostępu rozwiniętego — indeks oparty na plikach sygnatur. |
> | [Podczas aktywowania RowsetBulk \_ ątek](https://www.postgresql.org/docs/current/static/btree-gin.html) | Dostarcza przykładowe klasy operatorów ĄTEK, które implementują zachowanie typu B-drzewo dla określonych typów danych. |
> | [Podczas aktywowania RowsetBulk \_](https://www.postgresql.org/docs/current/static/btree-gist.html) | Dostarcza klasy operatorów indeksów indeksu, które implementują B-Tree. |

### <a name="language-extensions"></a>Rozszerzenia językowe

> [!div class="mx-tableFixed"]
> | **Wewnętrzny** | **Opis** |
> |---|---|
> | [plpgsql](https://www.postgresql.org/docs/current/static/plpgsql.html) | Język proceduralny z możliwością ładowania PL/pgSQL. |

### <a name="miscellaneous-extensions"></a>Różne rozszerzenia

> [!div class="mx-tableFixed"]
> | **Wewnętrzny** | **Opis** |
> |---|---|
> | [adminpack](https://www.postgresql.org/docs/current/adminpack.html) | Funkcje administracyjne dla PostgreSQL. |
> | [amcheck](https://www.postgresql.org/docs/current/amcheck.html) | Funkcje do sprawdzania integralności relacji. |
> | [dblink](https://www.postgresql.org/docs/current/dblink.html) | Moduł obsługujący połączenia z innymi bazami danych PostgreSQL z poziomu sesji bazy danych. Informacje o tym rozszerzeniu można znaleźć w sekcji "dblink i postgres_fdw". |
> | [\_FDW pliku](https://www.postgresql.org/docs/current/file-fdw.html) | Otoka danych obcych na potrzeby prostego dostępu do plików. |
> | [pageinspect](https://www.postgresql.org/docs/current/pageinspect.html) | Sprawdź zawartość stron bazy danych na niskim poziomie. |
> | [PG \_ buffercache](https://www.postgresql.org/docs/current/static/pgbuffercache.html) | Zapewnia metodę sprawdzania, co dzieje się w pamięci podręcznej buforów udostępnionych w czasie rzeczywistym. |
> | [PG — Strona firmy \_ cronus](https://github.com/citusdata/pg_cron) | Harmonogram zadań dla PostgreSQL. |
> | [PG \_ freespacemap](https://www.postgresql.org/docs/current/pgfreespacemap.html) | Obejrzyj mapę wolnego miejsca (FSM). |
> | [\_przedgrzana Strona](https://www.postgresql.org/docs/current/static/pgprewarm.html) | Zapewnia sposób ładowania danych relacji do pamięci podręcznej buforów. |
> | ["PG" — \_ \_ instrukcje](https://www.postgresql.org/docs/current/static/pgstatstatements.html) | Zapewnia metodę śledzenia statystyk wykonywania wszystkich instrukcji SQL wykonywanych przez serwer. Informacje o tym rozszerzeniu można znaleźć w sekcji "pg_stat_statements". |
> | [\_widoczność PG](https://www.postgresql.org/docs/current/pgvisibility.html) | Obejrzyj mapę widoczności (VM) i informacje o widoczności na poziomie strony. |
> | [pgrowlocks](https://www.postgresql.org/docs/current/static/pgrowlocks.html) | Zapewnia sposób wyświetlania informacji o blokowaniu na poziomie wiersza. |
> | [pgstattuple](https://www.postgresql.org/docs/current/static/pgstattuple.html) | Zapewnia sposób wyświetlania statystyk na poziomie krotki. |
> | [Postgres \_ FDW](https://www.postgresql.org/docs/current/static/postgres-fdw.html) | Otoka danych obcych używana do uzyskiwania dostępu do danych przechowywanych na zewnętrznych serwerach PostgreSQL. Informacje o tym rozszerzeniu można znaleźć w sekcji "dblink i postgres_fdw".|
> | [sslinfo](https://www.postgresql.org/docs/current/sslinfo.html) | Informacje o certyfikatach TLS/SSL. |
> | [TSM \_ \_ wierszy systemu](https://www.postgresql.org/docs/current/tsm-system-rows.html) | Metoda reklasy, która akceptuje liczbę wierszy jako limit. |
> | [\_czas systemowy TSM \_](https://www.postgresql.org/docs/current/tsm-system-time.html) | Metoda potraktować jako limit czas (w milisekundach). |
> | [xml2](https://www.postgresql.org/docs/current/xml2.html) | Zapytania XPath i XSLT. |


### <a name="postgis-extensions"></a>Rozszerzenia PostGIS

> [!div class="mx-tableFixed"]
> | **Wewnętrzny** | **Opis** |
> |---|---|
> | [PostGIS](https://www.postgis.net/), \_ topologia PostGIS, \_ PostGIS \_ geokodera Tiger, PostGIS \_ sfcgal | Obiekty przestrzenne i geograficzne dla PostgreSQL. |
> | \_normalizacja adresów, \_ \_ \_ nawiązanie przez nas danych | Służy do analizowania adresu w elementach składowych. Używane do obsługi kroku normalizacji adresów geokodowania. |
> | PostGIS \_ sfcgal | Funkcje PostGIS SFCGAL. |
> | PostGIS \_ \_ geokodera Tiger | PostGIS geokodny Tiger i odwrotny geokod. |
> | \_topologia PostGIS | Typy i funkcje przestrzenne topologii PostGIS. |


## <a name="pg_stat_statements"></a>pg_stat_statements
[Rozszerzenie "PG \_ stat \_ Statement](https://www.postgresql.org/docs/current/pgstatstatements.html) " jest wstępnie załadowane na każdym serwerze Azure Database for PostgreSQL, aby umożliwić śledzenie statystyk wykonywania instrukcji SQL.

Ustawienie `pg_stat_statements.track` określa, jakie instrukcje są zliczane przez rozszerzenie. Wartość domyślna to `top` , co oznacza, że wszystkie instrukcje wydawane bezpośrednio przez klientów są śledzone. Dwa inne poziomy śledzenia to `none` i `all` . To ustawienie można skonfigurować jako parametr serwera za pomocą [Azure Portal](./howto-configure-server-parameters-using-portal.md) lub [interfejsu wiersza polecenia platformy Azure](./howto-configure-server-parameters-using-cli.md).

Istnieje kompromis między informacjami o wykonywaniu zapytania pg_stat_statements zapewnia i wpływ na wydajność serwera podczas rejestrowania każdej instrukcji SQL. Jeśli nie korzystasz aktywnie z rozszerzenia pg_stat_statements, zalecamy ustawienie wartości `pg_stat_statements.track` `none` . Niektóre usługi monitorowania innych firm mogą polegać na pg_stat_statements w celu dostarczenia szczegółowych informacji o wydajności zapytań, dlatego należy sprawdzić, czy tak się dzieje.

## <a name="dblink-and-postgres_fdw"></a>dblink i postgres_fdw

\_Do łączenia się z jednego serwera PostgreSQL lub innej bazy danych na tym samym serwerze można użyć dblink i Postgres FDW.  Serwer otrzymujący musi zezwalać na połączenia z serwera wysyłającego za pośrednictwem jego zapory.  Aby użyć tych rozszerzeń do nawiązywania połączeń między grupami serwerów Azure Database for PostgreSQL lub Citus), ustaw opcję **Zezwól usługom i zasobom platformy Azure na dostęp do tej grupy serwerów (lub serwera)** do systemu.  To ustawienie należy również włączyć, jeśli chcesz użyć rozszerzeń do zapętlenia z powrotem do tego samego serwera.
Ustawienie **Zezwalaj na dostęp do usług platformy Azure i zasobów do dostępu do tej grupy serwerów** można znaleźć na stronie Azure Portal grupy serwerów Citus) w obszarze **Sieć**.  Obecnie połączenia wychodzące z Azure Database for PostgreSQL pojedynczego serwera i skalowania (Citus) nie są obsługiwane, z wyjątkiem połączeń z innymi grupami serwerów Azure Database for PostgreSQL i Citus.