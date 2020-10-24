---
title: 'Samouczek: projektowanie wielodostępnej bazy danych (Citus) — Azure Database for PostgreSQL'
description: W tym samouczku pokazano, jak włączyć skalowalną aplikację wielodostępną przy użyciu funkcji Azure Database for PostgreSQL Citus.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.custom: mvc
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 05/14/2019
ms.openlocfilehash: 2939b3b3beac55b537c1dcf844f702a0ad73f06e
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2020
ms.locfileid: "92478613"
---
# <a name="tutorial-design-a-multi-tenant-database-by-using-azure-database-for-postgresql--hyperscale-citus"></a>Samouczek: Projektowanie bazy danych z wieloma dzierżawami przy użyciu Azure Database for PostgreSQL — Citus

W tym samouczku użyjemy Azure Database for PostgreSQL-Citus, aby dowiedzieć się, jak:

> [!div class="checklist"]
> * Tworzenie grupy serwerów hiperskali (Citus)
> * Tworzenie schematu za pomocą narzędzia PSQL
> * Fragmentu tabele między węzłami
> * Pozyskiwanie przykładowych danych
> * Wykonywanie zapytań dotyczących danych dzierżawy
> * Udostępnianie danych między dzierżawcami
> * Dostosuj schemat dla dzierżawy

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [azure-postgresql-hyperscale-create-db](../../includes/azure-postgresql-hyperscale-create-db.md)]

## <a name="use-psql-utility-to-create-a-schema"></a>Tworzenie schematu za pomocą narzędzia PSQL

Po nawiązaniu połączenia z Azure Database for PostgreSQL-Citus z użyciem PSQL można wykonać niektóre podstawowe zadania. Ten samouczek przeprowadzi Cię przez proces tworzenia aplikacji sieci Web, która umożliwia wydziałom śledzenie swoich kampanii.

Wiele firm może korzystać z aplikacji, więc utworzymy tabelę do przechowywania firm i innych dla nich kampanii. W konsoli PSQL Uruchom następujące polecenia:

```sql
CREATE TABLE companies (
  id bigserial PRIMARY KEY,
  name text NOT NULL,
  image_url text,
  created_at timestamp without time zone NOT NULL,
  updated_at timestamp without time zone NOT NULL
);

CREATE TABLE campaigns (
  id bigserial,
  company_id bigint REFERENCES companies (id),
  name text NOT NULL,
  cost_model text NOT NULL,
  state text NOT NULL,
  monthly_budget bigint,
  blacklisted_site_urls text[],
  created_at timestamp without time zone NOT NULL,
  updated_at timestamp without time zone NOT NULL,

  PRIMARY KEY (company_id, id)
);
```

Każda kampania będzie obciążana za uruchamianie reklam. Dodaj tabelę dla reklam, uruchamiając następujący kod w PSQL po powyższym kodzie:

```sql
CREATE TABLE ads (
  id bigserial,
  company_id bigint,
  campaign_id bigint,
  name text NOT NULL,
  image_url text,
  target_url text,
  impressions_count bigint DEFAULT 0,
  clicks_count bigint DEFAULT 0,
  created_at timestamp without time zone NOT NULL,
  updated_at timestamp without time zone NOT NULL,

  PRIMARY KEY (company_id, id),
  FOREIGN KEY (company_id, campaign_id)
    REFERENCES campaigns (company_id, id)
);
```

Na koniec będziemy śledzić statystyki dotyczące kliknięć i wrażenia dla każdej usługi AD:

```sql
CREATE TABLE clicks (
  id bigserial,
  company_id bigint,
  ad_id bigint,
  clicked_at timestamp without time zone NOT NULL,
  site_url text NOT NULL,
  cost_per_click_usd numeric(20,10),
  user_ip inet NOT NULL,
  user_data jsonb NOT NULL,

  PRIMARY KEY (company_id, id),
  FOREIGN KEY (company_id, ad_id)
    REFERENCES ads (company_id, id)
);

CREATE TABLE impressions (
  id bigserial,
  company_id bigint,
  ad_id bigint,
  seen_at timestamp without time zone NOT NULL,
  site_url text NOT NULL,
  cost_per_impression_usd numeric(20,10),
  user_ip inet NOT NULL,
  user_data jsonb NOT NULL,

  PRIMARY KEY (company_id, id),
  FOREIGN KEY (company_id, ad_id)
    REFERENCES ads (company_id, id)
);
```

Nowo utworzone tabele znajdują się na liście tabel teraz w PSQL, uruchamiając:

```postgres
\dt
```

Aplikacje z wieloma dzierżawcami mogą wymuszać unikatowość tylko dla dzierżawców, co oznacza, że wszystkie klucze podstawowe i obce zawierają identyfikator firmy.

## <a name="shard-tables-across-nodes"></a>Fragmentu tabele między węzłami

Wdrożenie w celu przechowania tabeli wierszy w różnych węzłach w oparciu o wartość kolumny wyznaczonej przez użytkownika. Ta "kolumna dystrybucji" oznacza, do której dzierżawy należą wiersze.

Ustawmy, aby kolumna dystrybucji była \_ identyfikatorem firmy, identyfikatorem dzierżawy. W PSQL Uruchom następujące funkcje:

```sql
SELECT create_distributed_table('companies',   'id');
SELECT create_distributed_table('campaigns',   'company_id');
SELECT create_distributed_table('ads',         'company_id');
SELECT create_distributed_table('clicks',      'company_id');
SELECT create_distributed_table('impressions', 'company_id');
```

[!INCLUDE [azure-postgresql-hyperscale-dist-alert](../../includes/azure-postgresql-hyperscale-dist-alert.md)]

## <a name="ingest-sample-data"></a>Pozyskiwanie przykładowych danych

Na zewnątrz PSQL teraz w normalnym wierszu polecenia Pobierz przykładowe zestawy danych:

```bash
for dataset in companies campaigns ads clicks impressions geo_ips; do
  curl -O https://examples.citusdata.com/mt_ref_arch/${dataset}.csv
done
```

Z powrotem w PSQL, zbiorczo Ładuj dane. Upewnij się, że uruchamiasz PSQL w tym samym katalogu, do którego pobrano pliki danych.

```sql
SET CLIENT_ENCODING TO 'utf8';

\copy companies from 'companies.csv' with csv
\copy campaigns from 'campaigns.csv' with csv
\copy ads from 'ads.csv' with csv
\copy clicks from 'clicks.csv' with csv
\copy impressions from 'impressions.csv' with csv
```

Te dane będą teraz rozłożone między węzłami procesu roboczego.

## <a name="query-tenant-data"></a>Wykonywanie zapytań dotyczących danych dzierżawy

Gdy aplikacja żąda danych dla pojedynczej dzierżawy, baza danych może wykonać zapytanie w jednym węźle procesu roboczego. Zapytania o pojedynczej dzierżawie są filtrowane według pojedynczego identyfikatora dzierżawy. Na przykład następujące filtry zapytań `company_id = 5` dla reklam i wrażenia. Spróbuj uruchomić go w PSQL, aby wyświetlić wyniki.

```sql
SELECT a.campaign_id,
       RANK() OVER (
         PARTITION BY a.campaign_id
         ORDER BY a.campaign_id, count(*) desc
       ), count(*) as n_impressions, a.id
  FROM ads as a
  JOIN impressions as i
    ON i.company_id = a.company_id
   AND i.ad_id      = a.id
 WHERE a.company_id = 5
GROUP BY a.campaign_id, a.id
ORDER BY a.campaign_id, n_impressions desc;
```

## <a name="share-data-between-tenants"></a>Udostępnianie danych między dzierżawcami

Dopóki teraz wszystkie tabele nie zostaną rozdystrybuowane przez `company_id` program, ale niektóre dane nie są naturalnie "należące do żadnej dzierżawy i mogą być udostępniane. Na przykład, wszystkie firmy z tej platformy usługi AD mogą chcieć uzyskać informacje geograficzne dla swoich odbiorców na podstawie adresów IP.

Utwórz tabelę, aby przechowywać udostępnione informacje geograficzne. Uruchom następujące polecenia w PSQL:

```sql
CREATE TABLE geo_ips (
  addrs cidr NOT NULL PRIMARY KEY,
  latlon point NOT NULL
    CHECK (-90  <= latlon[0] AND latlon[0] <= 90 AND
           -180 <= latlon[1] AND latlon[1] <= 180)
);
CREATE INDEX ON geo_ips USING gist (addrs inet_ops);
```

Następnie Utwórz `geo_ips` "tabelę referencyjną", aby zapisać kopię tabeli w każdym węźle procesu roboczego.

```sql
SELECT create_reference_table('geo_ips');
```

Załaduj dane przy użyciu przykładowych danych. Należy pamiętać, aby uruchomić to polecenie w PSQL z katalogu, do którego pobrano zestaw danych.

```sql
\copy geo_ips from 'geo_ips.csv' with csv
```

Dołączanie tabeli kliknięć przy użyciu geograficznie \_ adresów IP jest wydajne we wszystkich węzłach.
Oto sprzężenie, aby znaleźć lokalizacje wszystkich osób, które klikną w usłudze AD
290. Spróbuj uruchomić zapytanie w PSQL.

```sql
SELECT c.id, clicked_at, latlon
  FROM geo_ips, clicks c
 WHERE addrs >> c.user_ip
   AND c.company_id = 5
   AND c.ad_id = 290;
```

## <a name="customize-the-schema-per-tenant"></a>Dostosuj schemat dla dzierżawy

Każdy Dzierżawca może wymagać przechowywania specjalnych informacji, które nie są wymagane przez inne osoby. Jednak wszyscy dzierżawcy korzystają ze wspólnej infrastruktury z identycznym schematem bazy danych. Gdzie można znaleźć dodatkowe dane?

Jedną z lew jest użycie typu kolumny Open-zakończony, takiego jak PostgreSQL JSONB.  Nasz schemat zawiera pole JSONB o `clicks` nazwie `user_data` .
Firma (powiedzmy pięć firm) może użyć kolumny, aby sprawdzić, czy użytkownik znajduje się na urządzeniu przenośnym.

Oto zapytanie, aby znaleźć, kto klika pozycję Więcej: mobilne lub tradycyjne osoby odwiedzające.

```sql
SELECT
  user_data->>'is_mobile' AS is_mobile,
  count(*) AS count
FROM clicks
WHERE company_id = 5
GROUP BY user_data->>'is_mobile'
ORDER BY count DESC;
```

Możemy zoptymalizować to zapytanie dla jednej firmy, tworząc [indeks częściowy](https://www.postgresql.org/docs/current/static/indexes-partial.html).

```sql
CREATE INDEX click_user_data_is_mobile
ON clicks ((user_data->>'is_mobile'))
WHERE company_id = 5;
```

Bardziej ogólnie rzecz biorąc, możemy utworzyć [indeks ątek](https://www.postgresql.org/docs/current/static/gin-intro.html) dla każdego klucza i wartości w kolumnie.

```sql
CREATE INDEX click_user_data
ON clicks USING gin (user_data);

-- this speeds up queries like, "which clicks have
-- the is_mobile key present in user_data?"

SELECT id
  FROM clicks
 WHERE user_data ? 'is_mobile'
   AND company_id = 5;
```

## <a name="clean-up-resources"></a>Czyszczenie zasobów

W poprzednich krokach zostały utworzone zasoby platformy Azure w grupie serwerów. Jeśli nie chcesz potrzebować tych zasobów w przyszłości, Usuń grupę serwerów. Naciśnij przycisk *Usuń* na stronie *Przegląd* dla swojej grupy serwerów. Po wyświetleniu monitu na stronie podręcznej Potwierdź nazwę grupy serwerów, a następnie kliknij przycisk *Usuń* końcowego.

## <a name="next-steps"></a>Następne kroki

W tym samouczku pokazano, jak zainicjować obsługę administracyjną grupy serwerów Citus. Nawiązano połączenie z usługą PSQL, utworzono schemat i dane rozproszone. Nauczysz się wysyłać zapytania dotyczące danych zarówno w dzierżawach, jak i między nimi, a następnie dostosować schemat na dzierżawcę.

- Informacje o [typach węzłów](./concepts-hyperscale-nodes.md) grupy serwerów
- Określ najlepszy [początkowy rozmiar](howto-hyperscale-scaling.md#picking-initial-size) grupy serwerów