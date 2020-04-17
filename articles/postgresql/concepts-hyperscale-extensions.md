---
title: Rozszerzenia — hiperskala (Citus) — usługa Azure Database for PostgreSQL
description: W tym artykule opisano możliwość rozszerzenia funkcji bazy danych przy użyciu rozszerzeń w usłudze Azure Database for PostgreSQL — Hyperscale (Citus)
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 04/16/2020
ms.openlocfilehash: 5d0798c77135b15e26c9787d9844cd9525cf12c5
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81532021"
---
# <a name="postgresql-extensions-in-azure-database-for-postgresql--hyperscale-citus"></a>PostgreSQL rozszerzenia w usłudze Azure Database dla PostgreSQL — hiperskala (Citus)

PostgreSQL zapewnia możliwość rozszerzenia funkcji bazy danych przy użyciu rozszerzeń. Rozszerzenia umożliwiają łączenie wielu powiązanych obiektów SQL razem w jednym pakiecie, który można załadować lub usunąć z bazy danych za pomocą jednego polecenia. Po załadowaniu do bazy danych rozszerzenia mogą działać jak wbudowane funkcje. Aby uzyskać więcej informacji na temat rozszerzeń PostgreSQL, zobacz [Pakiet powiązanych obiektów do rozszerzenia](https://www.postgresql.org/docs/current/static/extend-extensions.html).

## <a name="use-postgresql-extensions"></a>Korzystanie z rozszerzeń PostgreSQL

Przed użyciem należy zainstalować rozszerzenia PostgreSQL w bazie danych. Aby zainstalować określone rozszerzenie, uruchom polecenie [CREATE EXTENSION](https://www.postgresql.org/docs/current/static/sql-createextension.html) z narzędzia psql, aby załadować spakowane obiekty do bazy danych.

Usługa Azure Database for PostgreSQL — hiperskala (Citus) obsługuje obecnie podzbiór rozszerzeń kluczy wymienionych w tym miejscu. Rozszerzenia inne niż wymienione nie są obsługiwane. Nie można utworzyć własnego rozszerzenia z usługą Azure Database for PostgreSQL.

## <a name="extensions-supported-by-azure-database-for-postgresql"></a>Rozszerzenia obsługiwane przez usługę Azure Database dla postgreSQL

W poniższych tabelach przedstawiono standardowe rozszerzenia PostgreSQL, które są obecnie obsługiwane przez usługę Azure Database for PostgreSQL. Informacje te są również `SELECT * FROM pg_available_extensions;`dostępne po uruchomieniu .

### <a name="data-types-extensions"></a>Rozszerzenia typów danych

> [!div class="mx-tableFixed"]
> | **Wewnętrzny** | **Opis** |
> |---|---|
> | [citext (tekst)](https://www.postgresql.org/docs/current/static/citext.html) | Zawiera typ ciągu znaków bez uwzględniania wielkości liter. |
> | [Moduł](https://www.postgresql.org/docs/current/static/cube.html) | Udostępnia typ danych dla modułów wielowymiarowych. |
> | [sklep z hstore](https://www.postgresql.org/docs/current/static/hstore.html) | Udostępnia typ danych do przechowywania zestawów par klucz-wartość. |
> | [Hll](https://github.com/citusdata/postgresql-hll) | Zapewnia strukturę danych HyperLogLog. |
> | [Isn](https://www.postgresql.org/docs/current/static/isn.html) | Udostępnia typy danych dla międzynarodowych standardów numerowania produktów. |
> | [Lo](https://www.postgresql.org/docs/current/lo.html) | Konserwacja dużych obiektów. |
> | [ltree (drzewo ltree)](https://www.postgresql.org/docs/current/static/ltree.html) | Udostępnia typ danych dla hierarchicznych struktur podobnych do drzewa. |
> | [Seg](https://www.postgresql.org/docs/current/seg.html) | Typ danych do reprezentowania segmentów linii lub interwałów zmiennoprzecinkowych. |
> | [topn](https://github.com/citusdata/postgresql-topn/) | Typ dla top-n JSONB. |

### <a name="full-text-search-extensions"></a>Rozszerzenia wyszukiwania pełnotekstowego

> [!div class="mx-tableFixed"]
> | **Wewnętrzny** | **Opis** |
> |---|---|
> | [dict\_int](https://www.postgresql.org/docs/current/static/dict-int.html) | Zawiera szablon słownika wyszukiwania tekstu dla liczby całkowite. |
> | [dict\_xsyn](https://www.postgresql.org/docs/current/dict-xsyn.html) | Szablon słownika wyszukiwania tekstu dla rozszerzonego przetwarzania synonimów. |
> | [unaccent](https://www.postgresql.org/docs/current/static/unaccent.html) | Słownik wyszukiwania tekstu, który usuwa akcenty (znaki diakrytyczne) z lexemes. |

### <a name="functions-extensions"></a>Rozszerzenia funkcji

> [!div class="mx-tableFixed"]
> | **Wewnętrzny** | **Opis** |
> |---|---|
> | [autoinc](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.7) | Funkcje automatycznego zwiększania pól. |
> | [ziemnych](https://www.postgresql.org/docs/current/static/earthdistance.html) | Umożliwia obliczanie odległości wielkich okręgów na powierzchni Ziemi. |
> | [rozmycie](https://www.postgresql.org/docs/current/static/fuzzystrmatch.html) | Zawiera kilka funkcji w celu określenia podobieństw i odległości między ciągami. |
> | [wstawianie\_nazwy użytkownika](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.8) | Funkcje śledzenia, kto zmienił tabelę. |
> | [intagg ( intagg )](https://www.postgresql.org/docs/current/intagg.html) | Agregator liczby całkowitej i wyliczacz (przestarzały). |
> | [intarray (intarray)](https://www.postgresql.org/docs/current/static/intarray.html) | Udostępnia funkcje i operatory do manipulowania tablicami całkowitymi wolnymi od wartości null. |
> | [moddatetime](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.9) | Funkcje śledzenia czasu ostatniej modyfikacji. |
> | [pgcrypto](https://www.postgresql.org/docs/current/static/pgcrypto.html) | Udostępnia funkcje kryptograficzne. |
> | [pg\_partman](https://pgxn.org/dist/pg_partman/doc/pg_partman.html) | Zarządza tabelami podzielonymi na partycje według czasu lub identyfikatora. |
> | [pg\_trgm](https://www.postgresql.org/docs/current/static/pgtrgm.html) | Udostępnia funkcje i operatory do określania podobieństwa tekstu alfanumerycznego na podstawie dopasowywania trygramów. |
> | [refinansowanie](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.5) | Funkcje implementowania integralności referencyjnej (przestarzałe). |
> | analityka sesji\_ | Funkcje wykonywania zapytań o tablice hstore. |
> | [tablefunc](https://www.postgresql.org/docs/current/static/tablefunc.html) | Udostępnia funkcje, które manipulują całymi tabelami, w tym krzyżykem. |
> | [tcn](https://www.postgresql.org/docs/current/tcn.html) | Wyzwolone powiadomienia o zmianach. |
> | [wymiar czasu](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.6) | Funkcje wdrażania podróży w czasie. |
> | [uuid-ossp](https://www.postgresql.org/docs/current/static/uuid-ossp.html) | Generuje uniwersalne unikatowe identyfikatory (UUID). |

### <a name="hyperscale-extensions"></a>Rozszerzenia na dużą skalę

> [!div class="mx-tableFixed"]
> | **Wewnętrzny** | **Opis** |
> |---|---|
> | [citus (citus)](https://github.com/citusdata/citus) | Bazy danych Citus. |
> | balawancer odłamków\_ | Bezpiecznie równoważą dane w grupie serwerów w przypadku dodania lub usunięcia węzła. |

### <a name="index-types-extensions"></a>Rozszerzenia typów indeksów

> [!div class="mx-tableFixed"]
> | **Wewnętrzny** | **Opis** |
> |---|---|
> | [Bloom](https://www.postgresql.org/docs/current/bloom.html) | Metoda dostępu Bloom - indeks oparty na plikach podpisu. |
> | [btree\_gin](https://www.postgresql.org/docs/current/static/btree-gin.html) | Zawiera przykładowe klasy operatorów GIN, które implementują zachowanie podobne do drzewa B dla niektórych typów danych. |
> | [btree\_gist](https://www.postgresql.org/docs/current/static/btree-gist.html) | Zawiera klasy operatora indeksu GiST, które implementują B-tree. |

### <a name="language-extensions"></a>Rozszerzenia językowe

> [!div class="mx-tableFixed"]
> | **Wewnętrzny** | **Opis** |
> |---|---|
> | [plpgsql ( plpgsql )](https://www.postgresql.org/docs/current/static/plpgsql.html) | PL/pgSQL ładny język proceduralny. |

### <a name="miscellaneous-extensions"></a>Różne rozszerzenia

> [!div class="mx-tableFixed"]
> | **Wewnętrzny** | **Opis** |
> |---|---|
> | [pakiet administracyjny](https://www.postgresql.org/docs/current/adminpack.html) | Funkcje administracyjne dla PostgreSQL. |
> | [amcheck (amcheck)](https://www.postgresql.org/docs/current/amcheck.html) | Funkcje sprawdzania integralności relacji. |
> | [plik\_fdw](https://www.postgresql.org/docs/current/file-fdw.html) | Otoka danych zagranicznych dla płaskiego dostępu do plików. |
> | [pageinspect](https://www.postgresql.org/docs/current/pageinspect.html) | Sprawdź zawartość stron bazy danych na niskim poziomie. |
> | [pg\_bufor](https://www.postgresql.org/docs/current/static/pgbuffercache.html) | Zapewnia środki do badania, co dzieje się w pamięci podręcznej buforu udostępnionego w czasie rzeczywistym. |
> | [pg\_cron](https://github.com/citusdata/pg_cron) | Harmonogram zadań dla PostgreSQL. |
> | [pg\_freespacemap](https://www.postgresql.org/docs/current/pgfreespacemap.html) | Sprawdź mapę wolnego miejsca (FSM). |
> | [pg\_przedogniwem](https://www.postgresql.org/docs/current/static/pgprewarm.html) | Umożliwia załadowanie danych relacji do pamięci podręcznej buforu. |
> | [pg\_\_stat oświadczenia](https://www.postgresql.org/docs/current/static/pgstatstatements.html) | Zapewnia środki do śledzenia statystyk wykonywania wszystkich instrukcji SQL wykonywanych przez serwer. Zobacz sekcję "pg_stat_statements", aby uzyskać informacje na temat tego rozszerzenia. |
> | [pg\_widoczność](https://www.postgresql.org/docs/current/pgvisibility.html) | Sprawdź informacje o widoczności (VM) i widoczności na poziomie strony. |
> | [pgrowlocks](https://www.postgresql.org/docs/current/static/pgrowlocks.html) | Zapewnia sposób wyświetlania informacji o blokowaniu na poziomie wiersza. |
> | [pgstattuple](https://www.postgresql.org/docs/current/static/pgstattuple.html) | Zapewnia środki do wyświetlania statystyk na poziomie krotki. |
> | [postgres\_fdw](https://www.postgresql.org/docs/current/static/postgres-fdw.html) | Otoka danych obcych używana do uzyskiwania dostępu do danych przechowywanych na zewnętrznych serwerach PostgreSQL. Zobacz sekcję "dblink i postgres_fdw", aby uzyskać informacje na temat tego rozszerzenia.|
> | [sslinfo ( sslinfo )](https://www.postgresql.org/docs/current/sslinfo.html) | Informacje o certyfikatach SSL. |
> | [rzędy\_systemu tsm\_](https://www.postgresql.org/docs/current/tsm-system-rows.html) | TABLESAMPLE metoda, która akceptuje liczbę wierszy jako limit. |
> | [czas\_systemowy\_tsm](https://www.postgresql.org/docs/current/tsm-system-time.html) | TABLESAMPLE metoda, która akceptuje czas w milisekundach jako limit. |
> | [hypopg](https://hypopg.readthedocs.io/en/latest/) | Zapewnia możliwość tworzenia hipotetycznych indeksów, które nie kosztują procesora CPU lub dysku. |
> | [dblink (dblink)](https://www.postgresql.org/docs/current/dblink.html) | Moduł, który obsługuje połączenia z innymi bazami danych PostgreSQL z poziomu sesji bazy danych. Zobacz sekcję "dblink i postgres_fdw", aby uzyskać informacje na temat tego rozszerzenia. |
> | [xml2](https://www.postgresql.org/docs/current/xml2.html) | XPath kwerendy i XSLT. |


### <a name="postgis-extensions"></a>Rozszerzenia PostGIS

> [!div class="mx-tableFixed"]
> | **Wewnętrzny** | **Opis** |
> |---|---|
> | [PostGIS](https://www.postgis.net/), topologia\_postgis,\_\_geokoder tygrysa\_postgis, postgis sfcgal | Obiekty przestrzenne i geograficzne dla PostgreSQL. |
> | standardizator adresów,\_\_adres\_\_standardizator danych nas | Służy do analizować adres do elementów składowych. Służy do obsługi kroku normalizacji adresów geokodowania. |
> | postgis\_sfcgal | Funkcje PostGIS SFCGAL. |
> | geokoder\_\_tygrysa postgis | Geokoder tygrysa PostGIS i geokoder odwrotny. |
> | topologia\_postgis | PostGIS topologii typów przestrzennych i funkcji. |


## <a name="pg_stat_statements"></a>pg_stat_statements
[Rozszerzenie\_instrukcji\_pg stat](https://www.postgresql.org/docs/current/pgstatstatements.html) jest wstępnie załadowany na każdym serwerze usługi Azure Database dla postgreSql, aby zapewnić możliwość śledzenia statystyk wykonania instrukcji SQL.

Ustawienie `pg_stat_statements.track` określa, jakie instrukcje są liczone przez rozszerzenie. Domyślnie `top`jest to , co oznacza, że wszystkie instrukcje wystawione bezpośrednio przez klientów są śledzone. Dwa inne poziomy `none` śledzenia `all`są i . To ustawienie można skonfigurować jako parametr serwera za pośrednictwem [witryny Azure portal](https://docs.microsoft.com/azure/postgresql/howto-configure-server-parameters-using-portal) lub [interfejsu wiersza polecenia platformy Azure.](https://docs.microsoft.com/azure/postgresql/howto-configure-server-parameters-using-cli)

Istnieje kompromis między informacjami o wykonaniu kwerendy, pg_stat_statements zapewnia i wpływ na wydajność serwera, jak rejestruje każdą instrukcję SQL. Jeśli nie korzystasz aktywnie z rozszerzenia pg_stat_statements, zalecamy ustawienie `pg_stat_statements.track` `none`. Niektóre usługi monitorowania innych firm mogą polegać na pg_stat_statements do dostarczania szczegółowych informacji o wydajności zapytań, więc potwierdź, czy tak jest w przypadku Ciebie, czy nie.

## <a name="dblink-and-postgres_fdw"></a>dblink i postgres_fdw
Za pomocą łącza dblink i postgres_fdw można połączyć się z jednego serwera PostgreSQL z innym lub z inną bazą danych na tym samym serwerze. Serwer odbiorczy musi zezwolić na połączenia z serwera wysyłającego za pośrednictwem zapory. Aby użyć tych rozszerzeń do łączenia się między usługą Azure Database dla serwerów PostgreSQL, ustaw **zezwalaj na dostęp do usług platformy Azure** na on. Należy również włączyć to ustawienie, jeśli chcesz użyć rozszerzeń, aby zapętlać się z powrotem na tym samym serwerze. Ustawienie **Zezwalaj na dostęp do usług platformy Azure** można znaleźć na stronie portalu Azure dla serwera Postgres w obszarze Zabezpieczenia **połączenia**. Włączanie **Zezwalaj na dostęp do usług platformy Azure** na białej liście wszystkich adresów IP platformy Azure.

Obecnie połączenia wychodzące z usługi Azure Database for PostgreSQL nie są obsługiwane, z wyjątkiem połączeń z innymi serwerami usługi Azure Database dla serwerów PostgreSQL.
