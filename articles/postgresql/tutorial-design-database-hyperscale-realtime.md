---
title: 'Samouczek: projektowanie pulpitu nawigacyjnego w czasie rzeczywistym (Citus) — Azure Database for PostgreSQL'
description: Azure Database for PostgreSQL w tym samouczku pokazano, jak zrównoleglanie zapytania pulpitu nawigacyjnego w czasie rzeczywistym przy użyciu funkcji wieloskalowania (Citus).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.custom: mvc
ms.topic: tutorial
ms.date: 05/14/2019
ms.openlocfilehash: 480af87519f8e11c14f009058b0f518bdfc34a1b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "95026271"
---
# <a name="tutorial-design-a-real-time-analytics-dashboard-by-using-azure-database-for-postgresql--hyperscale-citus"></a>Samouczek: projektowanie pulpitu nawigacyjnego analizy w czasie rzeczywistym za pomocą Azure Database for PostgreSQL — Citus

W tym samouczku użyjemy Azure Database for PostgreSQL-Citus, aby dowiedzieć się, jak:

> [!div class="checklist"]
> * Tworzenie grupy serwerów hiperskali (Citus)
> * Tworzenie schematu za pomocą narzędzia PSQL
> * Fragmentu tabele między węzłami
> * Generowanie danych przykładowych
> * Wykonaj pakiety zbiorcze
> * Wykonywanie zapytań dotyczących danych nieprzetworzonych i agregowanych
> * Dane wygasające

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [azure-postgresql-hyperscale-create-db](../../includes/azure-postgresql-hyperscale-create-db.md)]

## <a name="use-psql-utility-to-create-a-schema"></a>Tworzenie schematu za pomocą narzędzia PSQL

Po nawiązaniu połączenia z Azure Database for PostgreSQL-Citus z użyciem PSQL można wykonać niektóre podstawowe zadania. Ten samouczek przeprowadzi Cię przez proces pozyskiwania danych o ruchu z analizy sieci Web, a następnie zestawia dane w celu zapewnienia pulpitów nawigacyjnych w czasie rzeczywistym na podstawie tych danych.

Utwórzmy tabelę, która będzie używać wszystkich naszych nieprzetworzonych danych o ruchu w sieci Web. Uruchom następujące polecenia w terminalu PSQL:

```sql
CREATE TABLE http_request (
  site_id INT,
  ingest_time TIMESTAMPTZ DEFAULT now(),

  url TEXT,
  request_country TEXT,
  ip_address TEXT,

  status_code INT,
  response_time_msec INT
);
```

Utworzymy również tabelę, w której będą przechowywane nasze zagregowane wartości, oraz tabelę, która utrzymuje pozycję naszego ostatniego zestawienia. Uruchom następujące polecenia w PSQL również:

```sql
CREATE TABLE http_request_1min (
  site_id INT,
  ingest_time TIMESTAMPTZ, -- which minute this row represents

  error_count INT,
  success_count INT,
  request_count INT,
  average_response_time_msec INT,
  CHECK (request_count = error_count + success_count),
  CHECK (ingest_time = date_trunc('minute', ingest_time))
);

CREATE INDEX http_request_1min_idx ON http_request_1min (site_id, ingest_time);

CREATE TABLE latest_rollup (
  minute timestamptz PRIMARY KEY,

  CHECK (minute = date_trunc('minute', minute))
);
```

Nowo utworzone tabele znajdują się na liście tabel teraz za pomocą tego polecenia PSQL:

```postgres
\dt
```

## <a name="shard-tables-across-nodes"></a>Fragmentu tabele między węzłami

Wdrożenie w celu przechowania tabeli wierszy w różnych węzłach w oparciu o wartość kolumny wyznaczonej przez użytkownika. Ta "kolumna dystrybucji" oznacza sposób, w jaki dane są podzielonej na fragmenty między węzłami.

Ustawmy, aby kolumna dystrybucji była \_ identyfikatorem witryny, fragmentu kluczem. W PSQL Uruchom następujące funkcje:

  ```sql
SELECT create_distributed_table('http_request',      'site_id');
SELECT create_distributed_table('http_request_1min', 'site_id');
```

[!INCLUDE [azure-postgresql-hyperscale-dist-alert](../../includes/azure-postgresql-hyperscale-dist-alert.md)]

## <a name="generate-sample-data"></a>Generowanie danych przykładowych

Teraz nasza grupa serwerów powinna być gotowa do pozyskiwania danych. Aby ciągle wstawiać dane, firma Microsoft może wykonać następujące czynności lokalnie `psql` .

```sql
DO $$
  BEGIN LOOP
    INSERT INTO http_request (
      site_id, ingest_time, url, request_country,
      ip_address, status_code, response_time_msec
    ) VALUES (
      trunc(random()*32), clock_timestamp(),
      concat('http://example.com/', md5(random()::text)),
      ('{China,India,USA,Indonesia}'::text[])[ceil(random()*4)],
      concat(
        trunc(random()*250 + 2), '.',
        trunc(random()*250 + 2), '.',
        trunc(random()*250 + 2), '.',
        trunc(random()*250 + 2)
      )::inet,
      ('{200,404}'::int[])[ceil(random()*2)],
      5+trunc(random()*150)
    );
    COMMIT;
    PERFORM pg_sleep(random() * 0.25);
  END LOOP;
END $$;
```

Zapytanie wstawia około osiem wierszy co sekundę. Wiersze są przechowywane w różnych węzłach procesu roboczego jako kierowane do kolumny dystrybucji `site_id` .

   > [!NOTE]
   > Pozostaw uruchomioną kwerendę generowania danych i Otwórz drugie połączenie PSQL dla pozostałych poleceń w tym samouczku.
   >

## <a name="query"></a>Zapytanie

Opcja hostingu w ramach skalowania umożliwia wielu węzłom równoległe przetwarzanie zapytań w celu przyspieszenia. Na przykład baza danych oblicza zagregowane wartości, takie jak SUM i COUNT w węzłach procesu roboczego, a następnie łączy wyniki w odpowiedzi końcowej.

Oto zapytanie pozwalające obliczyć liczbę żądań sieci Web na minutę wraz z kilkoma statystykami.
Spróbuj uruchomić go w PSQL i obserwuj wyniki.

```sql
SELECT
  site_id,
  date_trunc('minute', ingest_time) as minute,
  COUNT(1) AS request_count,
  SUM(CASE WHEN (status_code between 200 and 299) THEN 1 ELSE 0 END) as success_count,
  SUM(CASE WHEN (status_code between 200 and 299) THEN 0 ELSE 1 END) as error_count,
  SUM(response_time_msec) / COUNT(1) AS average_response_time_msec
FROM http_request
WHERE date_trunc('minute', ingest_time) > now() - '5 minutes'::interval
GROUP BY site_id, minute
ORDER BY minute ASC;
```

## <a name="rolling-up-data"></a>Wycofywanie danych

Poprzednie zapytanie działa prawidłowo w wczesnych etapach, ale jego wydajność obniży się w miarę skalowania danych. Nawet w przypadku przetwarzania rozproszonego szybciej można wstępnie obliczyć dane, niż w przypadku wielokrotnego obliczania.

Możemy upewnić się, że pulpit nawigacyjny pozostanie szybko, regularnie pobierając pierwotne dane do tabeli agregacji. Można eksperymentować z czasem trwania agregacji. Użyto tabeli agregacji na minutę, ale zamiast tego można przerwać dane w ciągu 5, 15 lub 60 minut.

Aby łatwiej uruchomić ten pakiet zbiorczy, należy umieścić go w funkcji plpgsql. Uruchom te polecenia w PSQL, aby utworzyć `rollup_http_request` funkcję.

```sql
-- initialize to a time long ago
INSERT INTO latest_rollup VALUES ('10-10-1901');

-- function to do the rollup
CREATE OR REPLACE FUNCTION rollup_http_request() RETURNS void AS $$
DECLARE
  curr_rollup_time timestamptz := date_trunc('minute', now());
  last_rollup_time timestamptz := minute from latest_rollup;
BEGIN
  INSERT INTO http_request_1min (
    site_id, ingest_time, request_count,
    success_count, error_count, average_response_time_msec
  ) SELECT
    site_id,
    date_trunc('minute', ingest_time),
    COUNT(1) as request_count,
    SUM(CASE WHEN (status_code between 200 and 299) THEN 1 ELSE 0 END) as success_count,
    SUM(CASE WHEN (status_code between 200 and 299) THEN 0 ELSE 1 END) as error_count,
    SUM(response_time_msec) / COUNT(1) AS average_response_time_msec
  FROM http_request
  -- roll up only data new since last_rollup_time
  WHERE date_trunc('minute', ingest_time) <@
          tstzrange(last_rollup_time, curr_rollup_time, '(]')
  GROUP BY 1, 2;

  -- update the value in latest_rollup so that next time we run the
  -- rollup it will operate on data newer than curr_rollup_time
  UPDATE latest_rollup SET minute = curr_rollup_time;
END;
$$ LANGUAGE plpgsql;
```

Korzystając z naszej funkcji, wykonaj ją w celu zestawienia danych:

```sql
SELECT rollup_http_request();
```

Wraz z naszymi danymi w formie wstępnie zagregowanej możemy zbadać tabelę zestawień, aby uzyskać ten sam raport zgodnie z wcześniejszym użyciem. Uruchom poniższe zapytanie:

```sql
SELECT site_id, ingest_time as minute, request_count,
       success_count, error_count, average_response_time_msec
  FROM http_request_1min
 WHERE ingest_time > date_trunc('minute', now()) - '5 minutes'::interval;
 ```

## <a name="expiring-old-data"></a>Wygaśnięcie starych danych

Pakiety zbiorcze umożliwiają szybsze wykonywanie zapytań, ale nadal musimy wygasnąć stare dane, aby uniknąć niezwiązanych kosztów magazynowania. Zdecyduj, jak długo chcesz przechowywać dane dla każdego stopnia szczegółowości, i użyj standardowych zapytań, aby usunąć wygasłe dane. W poniższym przykładzie postanowiono zachować dane pierwotne przez jeden dzień i agregacje na minutę przez jeden miesiąc:

```sql
DELETE FROM http_request WHERE ingest_time < now() - interval '1 day';
DELETE FROM http_request_1min WHERE ingest_time < now() - interval '1 month';
```

W środowisku produkcyjnym można zawijać te zapytania w funkcji i wywoływać je co minutę w zadaniu firmy cronus.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

W poprzednich krokach zostały utworzone zasoby platformy Azure w grupie serwerów. Jeśli nie chcesz potrzebować tych zasobów w przyszłości, Usuń grupę serwerów. Naciśnij przycisk *Usuń* na stronie *Przegląd* dla swojej grupy serwerów. Po wyświetleniu monitu na stronie podręcznej Potwierdź nazwę grupy serwerów, a następnie kliknij przycisk *Usuń* końcowego.

## <a name="next-steps"></a>Następne kroki

W tym samouczku pokazano, jak zainicjować obsługę administracyjną grupy serwerów Citus. Nawiązano połączenie z usługą PSQL, utworzono schemat i dane rozproszone. Wiesz już, jak wykonywać zapytania dotyczące danych w postaci pierwotnej, regularnie agregować te dane, wysyłać zapytania do zagregowanych tabel i wygasać stare dane.

- Informacje o [typach węzłów](./concepts-hyperscale-nodes.md) grupy serwerów
- Określ najlepszy [początkowy rozmiar](howto-hyperscale-scale-initial.md) grupy serwerów
