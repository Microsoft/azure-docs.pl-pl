---
title: 'Szybki Start: Tworzenie grupy serwerów — Citus) — Azure Database for PostgreSQL'
description: Przewodnik Szybki Start dotyczący tworzenia i wykonywania zapytań dotyczących tabel rozproszonych w Azure Database for PostgreSQL funkcji Citus.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.custom: mvc
ms.topic: quickstart
ms.date: 08/17/2020
ms.openlocfilehash: 03a6e927a074067e85f1a3adca38cae386d1af38
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/21/2020
ms.locfileid: "95026220"
---
# <a name="quickstart-create-a-hyperscale-citus-server-group-in-the-azure-portal"></a>Szybki Start: Tworzenie grupy serwerów ze skalą (Citus) w Azure Portal

Azure Database for PostgreSQL to usługa zarządzana, która służy do uruchamiania i skalowania w chmurze baz danych PostgreSQL o wysokiej dostępności, a także zarządzania nimi. W tym przewodniku szybki start przedstawiono sposób tworzenia grupy serwerów Azure Database for PostgreSQL-Citus) przy użyciu Azure Portal. Poznasz dane rozproszone: tabele fragmentowania w węzłach, pozyskiwanie przykładowych danych i wykonywanie zapytań wykonywanych na wielu węzłach.

[!INCLUDE [azure-postgresql-hyperscale-create-db](../../includes/azure-postgresql-hyperscale-create-db.md)]

## <a name="create-and-distribute-tables"></a>Tworzenie i dystrybuowanie tabel

Po nawiązaniu połączenia z węzłem koordynatora ze skalowaniem za pomocą PSQL można wykonać niektóre podstawowe zadania.

Na serwerach z Citus) Istnieją trzy typy tabel:

- Rozproszone lub podzielonej na fragmenty tabele (rozproszenie w celu ułatwienia skalowania pod kątem wydajności i przetwarzanie równoległe)
- Tabele odwołań (obsługiwane są wiele kopii)
- Tabele lokalne (często używane dla wewnętrznych tabel administracyjnych)

W tym przewodniku szybki start będziemy głównie skupić się na tabelach rozproszonych i zapoznać się z nimi.

Model danych, z którym będziemy współpracować, jest prosty: dane dotyczące użytkowników i zdarzeń z usługi GitHub. Zdarzenia obejmują tworzenie rozwidlenia, zatwierdzenia git powiązane z organizacją i nie tylko.

Po nawiązaniu połączenia za pośrednictwem PSQL Utwórzmy nasze tabele. W konsoli PSQL Uruchom następujące:

```sql
CREATE TABLE github_events
(
    event_id bigint,
    event_type text,
    event_public boolean,
    repo_id bigint,
    payload jsonb,
    repo jsonb,
    user_id bigint,
    org jsonb,
    created_at timestamp
);

CREATE TABLE github_users
(
    user_id bigint,
    url text,
    login text,
    avatar_url text,
    gravatar_id text,
    display_login text
);
```

`payload`Pole elementu `github_events` zawiera JSONB typu danych. JSONB jest typem danych JSON w postaci binarnej w Postgres. Typ danych ułatwia przechowywanie elastycznego schematu w jednej kolumnie.

Postgres może utworzyć `GIN` indeks tego typu, który będzie indeksować każdy klucz i jego wartość. Indeks umożliwia szybkie i łatwe wykonywanie zapytań dotyczących ładunku z różnych warunków. Przed załadowaniem danych przejdźmy do siebie i utworzysz kilka indeksów. W PSQL:

```sql
CREATE INDEX event_type_index ON github_events (event_type);
CREATE INDEX payload_index ON github_events USING GIN (payload jsonb_path_ops);
```

Następnie zajmiemy się tymi tabelami Postgres na węźle koordynatora i przekażemy funkcję Citus, aby fragmentu je przez pracowników. W tym celu uruchomimy zapytanie dla każdej tabeli, określając klucz, na który fragmentu. W bieżącym przykładzie będziemy fragmentu zarówno tabelę zdarzeń, jak i użytkowników `user_id` :

```sql
SELECT create_distributed_table('github_events', 'user_id');
SELECT create_distributed_table('github_users', 'user_id');
```

[!INCLUDE [azure-postgresql-hyperscale-dist-alert](../../includes/azure-postgresql-hyperscale-dist-alert.md)]

Jesteśmy gotowi do ładowania danych. W programie PSQL nadal poprowadzisz pobieranie plików z powłoki:

```sql
\! curl -O https://examples.citusdata.com/users.csv
\! curl -O https://examples.citusdata.com/events.csv
```

Następnie Załaduj dane z plików do tabel rozproszonych:

```sql
SET CLIENT_ENCODING TO 'utf8';

\copy github_events from 'events.csv' WITH CSV
\copy github_users from 'users.csv' WITH CSV
```

## <a name="run-queries"></a>Uruchamianie zapytań

Teraz czas dla części zabawnej, w rzeczywistości uruchamia kilka zapytań. Zacznijmy od prostego `count (*)` , aby zobaczyć, ile danych załadowałeś:

```sql
SELECT count(*) from github_events;
```

To działały dobrze. Powrócimy do tego sortowania agregacji w bitach, ale dla teraz przyjrzyjmy się kilku innym zapytaniom. W kolumnie JSONB `payload` istnieje dobry bit danych, ale jest ona różna w zależności od typu zdarzenia. `PushEvent` zdarzenia zawierają rozmiar, który obejmuje liczbę odrębnych zatwierdzeń dla wypychania. Możemy użyć jej do znalezienia łącznej liczby zatwierdzeń na godzinę:

```sql
SELECT date_trunc('hour', created_at) AS hour,
       sum((payload->>'distinct_size')::int) AS num_commits
FROM github_events
WHERE event_type = 'PushEvent'
GROUP BY hour
ORDER BY hour;
```

Dotychczas zapytania dotyczyły wyłącznie wydarzeń usługi GitHub \_ , ale możemy połączyć te informacje z użytkownikami usługi GitHub \_ . Ze względu na to, że podzielonej na fragmenty zarówno użytkowników, jak i zdarzenia w tym samym identyfikatorze ( `user_id` ), wiersze obu tabel ze zgodnymi identyfikatorami użytkowników będą [współzlokalizowane](concepts-hyperscale-colocation.md) w tych samych węzłach bazy danych i można je łatwo dołączyć.

Jeśli dołączymy `user_id` się, funkcja Citus może wypchnąć wykonywanie operacji JOIN do fragmentów na potrzeby wykonywania równolegle w węzłach procesu roboczego. Załóżmy na przykład, że użytkownicy, którzy utworzyli największą liczbę repozytoriów:

```sql
SELECT gu.login, count(*)
  FROM github_events ge
  JOIN github_users gu
    ON ge.user_id = gu.user_id
 WHERE ge.event_type = 'CreateEvent'
   AND ge.payload @> '{"ref_type": "repository"}'
 GROUP BY gu.login
 ORDER BY count(*) DESC;
```

## <a name="clean-up-resources"></a>Czyszczenie zasobów

W poprzednich krokach zostały utworzone zasoby platformy Azure w grupie serwerów. Jeśli nie chcesz potrzebować tych zasobów w przyszłości, Usuń grupę serwerów. Naciśnij przycisk **Usuń** na stronie **Przegląd** dla swojej grupy serwerów. Po wyświetleniu monitu na stronie podręcznej Potwierdź nazwę grupy serwerów, a następnie kliknij przycisk **Usuń** końcowego.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start zawarto informacje na temat aprowizacji grupy serwerów z Citus. Nawiązano połączenie z usługą PSQL, utworzono schemat i dane rozproszone.

- Postępuj zgodnie z samouczkiem, aby [utworzyć skalowalne aplikacje z wieloma dzierżawcami](./tutorial-design-database-hyperscale-multi-tenant.md)
- Określ najlepszy [początkowy rozmiar](howto-hyperscale-scale-initial.md) grupy serwerów
