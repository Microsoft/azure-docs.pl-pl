---
title: Korzystanie z rozszerzeń PostgreSQL
description: Korzystanie z rozszerzeń PostgreSQL
titleSuffix: Azure Arc enabled data services
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: e247e372237572586e5a4647d24d9ed6067ea823
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104949791"
---
# <a name="use-postgresql-extensions-in-your-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Korzystanie z rozszerzeń PostgreSQL w grupie serwerów PostgreSQL w usłudze Azure Arc

PostgreSQL jest najlepszym rozwiązaniem, gdy jest używany z rozszerzeniami. W rzeczywistości kluczowym elementem naszej funkcjonalności w ramach własnych funkcji w pełni skalowania jest rozszerzenie dostarczone przez firmę Microsoft `citus` , które jest instalowane domyślnie, co umożliwia Postgres przezroczyste fragmentu danych w wielu węzłach.


[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="supported-extensions"></a>Obsługiwane rozszerzenia
Standardowe [`contrib`](https://www.postgresql.org/docs/12/contrib.html) rozszerzenia i następujące rozszerzenia są już wdrożone w kontenerach grupy serwerów PostgreSQL z funkcją Azure ARC:
- [`citus`](https://github.com/citusdata/citus), v: 9,4. Rozszerzenie Citus przez [dane Citus](https://www.citusdata.com/) jest domyślnie ładowane, ponieważ umożliwia przeprowadzenie funkcji skalowania aparatu PostgreSQL. Porzucanie rozszerzenia Citus z grupy serwerów usługi Azure Arc PostgreSQL do skalowania nie jest obsługiwane.
- [`pg_cron`](https://github.com/citusdata/pg_cron), v: 1,2
- [`pgaudit`](https://www.pgaudit.org/), v: 1,4
- plpgsql, v: 1,0
- [`postgis`](https://postgis.net), v: 3.0.2
- [`plv8`](https://plv8.github.io/), v: 2.3.14

Aktualizacje tej listy zostaną ogłoszone w miarę rozwoju z upływem czasu.

> [!IMPORTANT]
> W tej wersji zapoznawczej możesz przystąpić do grupy serwerów z rozszerzeniem innym niż wymienione powyżej, które nie zostanie utrwalone w systemie. Oznacza to, że nie będzie on dostępny po ponownym uruchomieniu systemu i należy go ponownie wprowadzić.

Ten przewodnik zajmie się scenariuszem korzystania z dwóch z następujących rozszerzeń:
- [`PostGIS`](https://postgis.net/)
- [`pg_cron`](https://github.com/citusdata/pg_cron)

## <a name="which-extensions-need-to-be-added-to-the-shared_preload_libraries-and-created"></a>Które rozszerzenia należy dodać do shared_preload_libraries i utworzyć?

|Rozszerzenia   |Wymaga dodania do shared_preload_libraries  |Wymaga utworzenia |
|-------------|--------------------------------------------------|---------------------- |
|`pg_cron`      |Nie       |Tak        |
|`pg_audit`     |Tak       |Tak        |
|`plpgsql`      |Tak       |Tak        |
|`postgis`      |Nie       |Tak        |
|`plv8`      |Nie       |Tak        |

## <a name="add-extensions-to-the-shared_preload_libraries"></a>Dodawanie rozszerzeń do shared_preload_libraries
Aby uzyskać szczegółowe informacje o tym, shared_preload_libraries zapoznaj się [z dokumentacją PostgreSQL](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-SHARED-PRELOAD-LIBRARIES):
- Ten krok nie jest wymagany w przypadku rozszerzeń, które są częścią `contrib`
- Ten krok nie jest wymagany w przypadku rozszerzeń, które nie są wymagane do wstępnego ładowania przez shared_preload_libraries. W przypadku tych rozszerzeń można przejść do następnego akapitu [Tworzenie rozszerzeń](#create-extensions).

### <a name="add-an-extension-at-the-creation-time-of-a-server-group"></a>Dodaj rozszerzenie w czasie tworzenia grupy serwerów
```console
azdata arc postgres server create -n <name of your postgresql server group> --extensions <extension names>
```
### <a name="add-an-extension-to-an-instance-that-already-exists"></a>Dodaj rozszerzenie do wystąpienia, które już istnieje
```console
azdata arc postgres server edit -n <name of your postgresql server group> --extensions <extension names>
```




## <a name="show-the-list-of-extensions-added-to-shared_preload_libraries"></a>Pokaż listę rozszerzeń dodanych do shared_preload_libraries
Uruchom jedno z następujących poleceń.

### <a name="with-an-azdata-cli-command"></a>Za pomocą interfejsu wiersza polecenia azdata
```console
azdata arc postgres server show -n <server group name>
```
Przewiń w dane wyjściowe i zwróć uwagę na sekcje engine\extensions w specyfikacji grupy serwerów. Na przykład:
```console
"engine": {
      "extensions": [
        {
          "name": "citus"
        },
        {
          "name": "pg_cron"
        }
      ]
    },
```
### <a name="with-kubectl"></a>Z polecenia kubectl
```console
kubectl describe postgresql-12s/postgres02
```
Przewiń w dane wyjściowe i zwróć uwagę na sekcje engine\extensions w specyfikacji grupy serwerów. Na przykład:
```console
Engine:
    Extensions:
      Name:  citus
      Name:  pg_cron
```


## <a name="create-extensions"></a>Tworzenie rozszerzeń
Połącz się z grupą serwerów za pomocą wybranego narzędzia klienckiego i uruchom standardowe zapytanie PostgreSQL:
```console
CREATE EXTENSION <extension name>;
```

## <a name="show-the-list-of-extensions-created"></a>Pokaż listę utworzonych rozszerzeń
Połącz się z grupą serwerów za pomocą wybranego narzędzia klienckiego i uruchom standardowe zapytanie PostgreSQL:
```console
select * from pg_extension;
```

## <a name="drop-an-extension"></a>Usuń rozszerzenie
Połącz się z grupą serwerów za pomocą wybranego narzędzia klienckiego i uruchom standardowe zapytanie PostgreSQL:
```console
drop extension <extension name>;
```

## <a name="the-postgis-extension"></a>`PostGIS`Rozszerzenie
Nie musisz dodawać `PostGIS` rozszerzenia do `shared_preload_libraries` .
Pobierz [dane przykładowe](http://duspviz.mit.edu/tutorials/intro-postgis/) z działu z badań miejskich w ramach usługi mit & planowanie. Uruchom `apt-get install unzip` , aby zainstalować rozpakować zgodnie z wymaganiami.

```console
wget http://duspviz.mit.edu/_assets/data/intro-postgis-datasets.zip
unzip intro-postgis-datasets.zip
```

Połączmy się z naszą bazą danych i Utwórz `PostGIS` rozszerzenie:

```console
CREATE EXTENSION postgis;
```

> [!NOTE]
> Jeśli chcesz użyć jednego z rozszerzeń pakietu (na przykład,,...), musisz `postgis` `postgis_raster` `postgis_topology` `postgis_sfcgal` `fuzzystrmatch` najpierw utworzyć rozszerzenie PostGIS, a następnie utworzyć inne rozszerzenie. Na przykład: `CREATE EXTENSION postgis` ; `CREATE EXTENSION postgis_raster`

I Utwórz schemat:

```sql
CREATE TABLE coffee_shops (
  id serial NOT NULL,
  name character varying(50),
  address character varying(50),
  city character varying(50),
  state character varying(50),
  zip character varying(10),
  lat numeric,
  lon numeric,
  geom geometry(POINT,4326)
);
CREATE INDEX coffee_shops_gist ON coffee_shops USING gist (geom);
```

Teraz możemy łączyć `PostGIS` się z funkcją skalowania w poziomie, tworząc tabelę coffee_shops dystrybuowaną:

```sql
SELECT create_distributed_table('coffee_shops', 'id');
```

Załadujmy dane:

```console
\copy coffee_shops(id,name,address,city,state,zip,lat,lon) from cambridge_coffee_shops.csv CSV HEADER;
```

I wypełnij `geom` pole z poprawnie zakodowaną szerokością i długością geograficzną w `PostGIS` `geometry` typie danych:

```sql
UPDATE coffee_shops SET geom = ST_SetSRID(ST_MakePoint(lon,lat),4326);
```

Teraz możemy wyświetlić listę sklepów z systemem kawy najbliżej granic MIT (77 Massachusetts Ave o 42,359055,-71,093500):

```sql
SELECT name, address FROM coffee_shops ORDER BY geom <-> ST_SetSRID(ST_MakePoint(-71.093500,42.359055),4326);
```


## <a name="the-pg_cron-extension"></a>`pg_cron`Rozszerzenie

Teraz włączmy tę `pg_cron` grupę serwerów PostgreSQL, dodając ją do shared_preload_libraries:

```console
azdata postgres server update -n pg2 -ns arc --extensions pg_cron
```

Twoja Grupa serwerów zostanie ponownie uruchomiona, aby zakończyć instalację rozszerzeń. Może to potrwać od 2 do 3 minut.

Teraz można nawiązać połączenie ponownie i utworzyć `pg_cron` rozszerzenie:

```sql
CREATE EXTENSION pg_cron;
```

W celach testowych, program pozwala utworzyć tabelę `the_best_coffee_shop` pobierającą losowo nazwę z poprzedniej `coffee_shops` tabeli i wstawia zawartość tabeli:

```sql
CREATE TABLE the_best_coffee_shop(name text);
```

Możemy użyć `cron.schedule` znaku plus kilka instrukcji SQL, aby uzyskać losową nazwę tabeli (Zauważ użycie tabeli tymczasowej do przechowywania wyników zapytania rozproszonego) i zapisać ją w `the_best_coffee_shop` :

```sql
SELECT cron.schedule('* * * * *', $$
  TRUNCATE the_best_coffee_shop;
  CREATE TEMPORARY TABLE tmp AS SELECT name FROM coffee_shops ORDER BY random() LIMIT 1;
  INSERT INTO the_best_coffee_shop SELECT * FROM tmp;
  DROP TABLE tmp;
$$);
```

Teraz, po minucie, otrzymamy inną nazwę:

```sql
SELECT * FROM the_best_coffee_shop;
```

```console
      name
-----------------
 B & B Snack Bar
(1 row)
```

Aby uzyskać szczegółowe informacje na temat składni, zobacz [plik readme pg_cron](https://github.com/citusdata/pg_cron) .


## <a name="next-steps"></a>Następne kroki
- Przeczytaj dokumentację [`plv8`](https://plv8.github.io/)
- Przeczytaj dokumentację [`PostGIS`](https://postgis.net/)
- Przeczytaj dokumentację [`pg_cron`](https://github.com/citusdata/pg_cron)