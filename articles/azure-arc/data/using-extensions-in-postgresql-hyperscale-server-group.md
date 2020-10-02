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
ms.openlocfilehash: 58386786266c48c6e721094f9f2837709bb684e5
ms.sourcegitcommit: d479ad7ae4b6c2c416049cb0e0221ce15470acf6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/01/2020
ms.locfileid: "91631770"
---
# <a name="use-postgresql-extensions-in-your-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Korzystanie z rozszerzeń PostgreSQL w grupie serwerów PostgreSQL w usłudze Azure Arc

PostgreSQL jest najlepszym rozwiązaniem, gdy jest używany z rozszerzeniami. W rzeczywistości kluczowym elementem naszej funkcjonalności w ramach własnych funkcji w pełni skalowania jest rozszerzenie dostarczone przez firmę Microsoft `citus` , które jest instalowane domyślnie, co umożliwia Postgres przezroczyste fragmentu danych w wielu węzłach.


[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="list-of-extensions"></a>Lista rozszerzeń
Oprócz rozszerzeń w programie [`contrib`](https://www.postgresql.org/docs/12/contrib.html) lista rozszerzeń znajdujących się w kontenerach grupy serwerów PostgreSQL z usługą Azure Arc jest:
- `citus`, v: 9,4
- `pg_cron`, v: 1,2
- `plpgsql`, v: 1,0
- `postgis`, v: 3.0.2
- `plv8`, v: 2.3.14

Ta lista zmienia pracę w nadgodzinach i aktualizacje zostaną opublikowane w tym dokumencie. Nie jest jeszcze możliwe dodawanie rozszerzeń poza tymi wymienionymi powyżej.

Ten przewodnik zajmie się scenariuszem korzystania z dwóch z następujących rozszerzeń:
- [PostGIS](https://postgis.net/)
- [`pg_cron`](https://github.com/citusdata/pg_cron)


## <a name="manage-extensions"></a>Zarządzanie rozszerzeniami

### <a name="enable-extensions"></a>Włącz rozszerzenia
Ten krok nie jest wymagany w przypadku rozszerzeń, które są częścią programu `contrib` .
Ogólny format polecenia umożliwiającego włączenie rozszerzeń:

#### <a name="enable-an-extension-at-the-creation-time-of-a-server-group"></a>Włącz rozszerzenie w czasie tworzenia grupy serwerów:
```console
azdata arc postgres server create -n <name of your postgresql server group> --extensions <extension names>
```
#### <a name="enable-an-extension-on-an-instance-that-already-exists"></a>Włącz rozszerzenie w wystąpieniu, które już istnieje:
```console
azdata arc postgres server edit -n <name of your postgresql server group> --extensions <extension names>
```

#### <a name="get-the-list-of-extensions-enabled"></a>Pobierz listę rozszerzeń włączonych:
Uruchom jedno z następujących poleceń.

##### <a name="with-azdata"></a>Z azdata
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
##### <a name="with-kubectl"></a>Z polecenia kubectl
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


### <a name="create-extensions"></a>Tworzenie rozszerzeń:
Połącz się z grupą serwerów za pomocą wybranego narzędzia klienckiego i uruchom standardowe zapytanie PostgreSQL:
```console
CREATE EXTENSION <extension name>;
```

### <a name="get-the-list-of-extension-created-in-your-server-group"></a>Pobierz listę rozszerzeń utworzonych w grupie serwerów:
Połącz się z grupą serwerów za pomocą wybranego narzędzia klienckiego i uruchom standardowe zapytanie PostgreSQL:
```console
select * from pg_extension;
```

### <a name="drop-an-extension-from-your-server-group"></a>Usuń rozszerzenie z grupy serwerów:
Połącz się z grupą serwerów za pomocą wybranego narzędzia klienckiego i uruchom standardowe zapytanie PostgreSQL:
```console
drop extension <extension name>;
```

## <a name="use-the-postgis-and-the-pg_cron-extensions"></a>Korzystanie z PostGIS i rozszerzeń Pg_cron

### <a name="the-postgis-extension"></a>Rozszerzenie PostGIS

Możemy włączyć rozszerzenie PostGIS w istniejącej grupie serwerów lub utworzyć nową, jeśli rozszerzenie jest już włączone:

**Włączanie rozszerzenia w czasie tworzenia grupy serwerów:**
```console
azdata arc postgres server create -n <name of your postgresql server group> --extensions <extension names>

#Example:
azdata arc postgres server create -n pg2 -w 2 --extensions postgis
```

**Włączanie rozszerzenia na wystąpieniu, które już istnieje:**
```console
azdata arc postgres server edit -n <name of your postgresql server group> --extensions <extension names>

#Example:
azdata arc postgres server edit --extensions postgis -n pg2
```

Aby sprawdzić, jakie rozszerzenia są zainstalowane, użyj poniższego standardowego polecenia PostgreSQL po nawiązaniu połączenia z wystąpieniem przy użyciu ulubionego narzędzia klienta PostgreSQL, takiego jak Azure Data Studio:
```console
select * from pg_extension;
```

Aby uzyskać przykład PostGIS, należy uzyskać [przykładowe dane](http://duspviz.mit.edu/tutorials/intro-postgis/) z Departamentu z badań miejskich w ramach usługi mit & planowanie. Może być konieczne uruchomienie `apt-get install unzip` programu w celu zainstalowania rozpakowania podczas korzystania z maszyny wirtualnej do testowania.

```console
wget http://duspviz.mit.edu/_assets/data/intro-postgis-datasets.zip
unzip intro-postgis-datasets.zip
```

Połączmy się z naszą bazą danych i Utwórz rozszerzenie PostGIS:

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

Teraz możemy łączyć PostGIS z funkcją skalowania w poziomie, tworząc tabelę coffee_shops dystrybuowaną:

```sql
SELECT create_distributed_table('coffee_shops', 'id');
```

Załadujmy dane:

```console
\copy coffee_shops(id,name,address,city,state,zip,lat,lon) from cambridge_coffee_shops.csv CSV HEADER;
```

I wypełnij `geom` pole z poprawnie zakodowaną szerokością i długością geograficzną w PostGIS `geometry` Typ danych:

```sql
UPDATE coffee_shops SET geom = ST_SetSRID(ST_MakePoint(lon,lat),4326);
```

Teraz możemy wyświetlić listę sklepów z systemem kawy najbliżej granic MIT (77 Massachusetts Ave o 42,359055,-71,093500):

```sql
SELECT name, address FROM coffee_shops ORDER BY geom <-> ST_SetSRID(ST_MakePoint(-71.093500,42.359055),4326);
```


### <a name="the-pg_cron-extension"></a>Rozszerzenie pg_cron

Włączmy `pg_cron` do naszej grupy serwerów PostgreSQL, oprócz PostGIS:

```console
azdata postgres server update -n pg2 -ns arc --extensions postgis,pg_cron
```

Należy pamiętać, że spowoduje to ponowne uruchomienie węzłów i zainstalowanie dodatkowych rozszerzeń, co może potrwać 2-3 minut.

Teraz można nawiązać połączenie ponownie i utworzyć `pg_cron` rozszerzenie:

```sql
CREATE EXTENSION pg_cron;
```

W celach testowych program pozwala utworzyć tabelę `the_best_coffee_shop` , która pobiera losowo nazwę z poprzedniej `coffee_shops` tabeli i ustawia zawartość tabeli:

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

>[!NOTE]
>Porzucenie rozszerzenia nie jest obsługiwane `citus` . `citus`Rozszerzenie jest wymagane w celu zapewnienia środowiska ze skalowaniem.

## <a name="next-steps"></a>Następne kroki:
- Przeczytaj dokumentację dotyczącą [PLV8](https://plv8.github.io/)
- Przeczytaj dokumentację dotyczącą [PostGIS](https://postgis.net/)
- Przeczytaj dokumentację [`pg_cron`](https://github.com/citusdata/pg_cron)
