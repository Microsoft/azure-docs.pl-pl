---
title: Skalowanie w poziomie Azure Database for PostgreSQL grupy serwerów
description: Skalowanie w poziomie Azure Database for PostgreSQL grupy serwerów
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 17bdae658c7095c44a7ae9f30fd85a6c45bf1546
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96779978"
---
# <a name="scale-out-your-azure-arc-enabled-postgresql-hyperscale-server-group-by-adding-more-worker-nodes"></a>Skalowanie w poziomie grupy serwerów PostgreSQL na platformie Azure
W tym dokumencie wyjaśniono, jak skalować w poziomie grupę serwerów PostgreSQL na platformie Azure z włączonym skalowaniem. Robi to, przechodząc przez scenariusz. **Jeśli nie chcesz wykonywać w scenariuszu i chcesz przeczytać o sposobie skalowania w poziomie, przejdź do [skalowania w poziomie](#scale-out)**.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="get-started"></a>Rozpoczęcie pracy
Jeśli znasz już model skalowania usługi Azure ARC z włączonym PostgreSQLm skalowania lub Azure Database for PostgreSQLm skalowania (Citus), możesz pominąć ten akapit. W przeciwnym razie zaleca się rozpoczęcie od odczytu tego modelu skalowania na stronie dokumentacji Azure Database for PostgreSQL Citus. Azure Database for PostgreSQL Citus) to taka sama technologia, która jest hostowana jako usługa na platformie Azure (platforma jako usługa znana również jako PAAS), a nie oferowana jako część usługi Azure ARC z włączonymi Data Services:
- [Węzły i tabele](../../postgresql/concepts-hyperscale-nodes.md)
- [Określanie typu aplikacji](../../postgresql/concepts-hyperscale-app-type.md)
- [Wybieranie kolumny dystrybucji](../../postgresql/concepts-hyperscale-choose-distribution-column.md)
- [Kolokacja tabeli](../../postgresql/concepts-hyperscale-colocation.md)
- [Rozpowszechnianie i modyfikowanie tabel](../../postgresql/howto-hyperscale-modify-distributed-tables.md)
- [Projektowanie bazy danych z wieloma dzierżawcami](../../postgresql/tutorial-design-database-hyperscale-multi-tenant.md)*
- [Projektowanie pulpitu nawigacyjnego analizy w czasie rzeczywistym](../../postgresql/tutorial-design-database-hyperscale-realtime.md)*

> \* W powyższych dokumentach Pomiń sekcje **Zaloguj się do Azure Portal**, & **Utwórz Azure Database for PostgreSQL-Citus**. Zaimplementuj pozostałe kroki w ramach wdrożenia usługi Azure Arc. Te sekcje są specyficzne dla Azure Database for PostgreSQL Citus) oferowanych jako usługa PaaS w chmurze platformy Azure, ale inne części dokumentów są bezpośrednio stosowane do wieloskalowania z włączonym systemem Azure Arc PostgreSQL.

## <a name="scenario"></a>Scenariusz
W tym scenariuszu odwołuje się do grupy serwerów PostgreSQL ze skalą, która została utworzona w ramach przykładu w dokumentacji [Tworzenie grupy serwerów PostgreSQL w systemie Azure Arc](create-postgresql-hyperscale-server-group.md) .

### <a name="load-test-data"></a>Ładowanie danych testowych
W scenariuszu jest wykorzystywane przykładowe publicznie dostępne dane usługi GitHub dostępne w [witrynie sieci Web Citus](https://www.citusdata.com/) (dane Citus są częścią firmy Microsoft).

#### <a name="connect-to-your-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Połącz z grupą serwerów PostgreSQL z funkcją Azure Arc

##### <a name="list-the-connection-information"></a>Wyświetlanie listy informacji o połączeniu
Połącz się z grupą serwerów PostgreSQL z usługą Azure ARC, wybierając najpierw informacje o połączeniu: ogólny format tego polecenia to
```console
azdata arc postgres endpoint list -n <server name>
```
Na przykład:
```console
azdata arc postgres endpoint list -n postgres01
```

Przykładowe dane wyjściowe:

```console
[
  {
    "Description": "PostgreSQL Instance",
    "Endpoint": "postgresql://postgres:<replace with password>@12.345.123.456:1234"
  },
  {
    "Description": "Log Search Dashboard",
    "Endpoint": "https://12.345.123.456:12345/kibana/app/kibana#/discover?_a=(query:(language:kuery,query:'custom_resource_name:\"postgres01\"'))"
  },
  {
    "Description": "Metrics Dashboard",
    "Endpoint": "https://12.345.123.456:12345/grafana/d/postgres-metrics?var-Namespace=arc3&var-Name=postgres01"
  }
]
```

##### <a name="connect-with-the-client-tool-of-your-choice"></a>Połącz się za pomocą wybranego przez siebie narzędzia klienckiego.

Uruchom następujące zapytanie, aby sprawdzić, czy obecnie są dostępne dwa (lub więcej węzłów procesów roboczych), które odpowiadają Kubernetes pod:

```sql
SELECT * FROM pg_dist_node;
```

```console
 nodeid | groupid |                       nodename                        | nodeport | noderack | hasmetadata | isactive | noderole | nodecluster | metadatasynced | shouldhaveshards
--------+---------+-------------------------------------------------------+----------+----------+-------------+----------+----------+-------------+----------------+------------------
      1 |       1 | pg1-1.pg1-svc.default.svc.cluster.local |     5432 | default  | f           | t        | primary  | default     | f              | t
      2 |       2 | pg1-2.pg1-svc.default.svc.cluster.local |     5432 | default  | f           | t        | primary  | default     | f              | t
(2 rows)
```

#### <a name="create-a-sample-schema"></a>Tworzenie przykładowego schematu
Utwórz dwie tabele, uruchamiając następujące zapytanie:

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

JSONB jest typem danych JSON w postaci binarnej w PostgreSQL. Przechowuje elastyczny schemat w jednej kolumnie i z PostgreSQL. Schemat będzie miał indeks ĄTEK do indeksowania każdego klucza i wartości w nim. Indeks ĄTEK jest szybki i łatwy do wykonywania zapytań z różnymi warunkami bezpośrednio w tym ładunku. Teraz będziemy tworzyć kilka indeksów przed załadowaniem danych:

```sql
CREATE INDEX event_type_index ON github_events (event_type);
CREATE INDEX payload_index ON github_events USING GIN (payload jsonb_path_ops);
```

Aby fragmentu tabele standardowe, uruchom zapytanie dla każdej tabeli. Określ tabelę, którą chcemy fragmentu, i klucz, na którym chcemy ją fragmentu. Fragmentu zarówno tabelę zdarzeń, jak i użytkowników w user_id:

```sql
SELECT create_distributed_table('github_events', 'user_id');
SELECT create_distributed_table('github_users', 'user_id');
```

#### <a name="load-sample-data"></a>Ładowanie przykładowych danych
Załaduj dane z KOPIą... Z PROGRAMU:

```sql
COPY github_users FROM PROGRAM 'curl "https://examples.citusdata.com/users.csv"' WITH ( FORMAT CSV );
COPY github_events FROM PROGRAM 'curl "https://examples.citusdata.com/events.csv"' WITH ( FORMAT CSV );
```

#### <a name="query-the-data"></a>Wykonywanie zapytań na danych
Teraz mierzę czas wykonywania prostego zapytania z dwoma węzłami:

```sql
SELECT COUNT(*) FROM github_events;
```
Zanotuj czas wykonywania zapytania.


## <a name="scale-out"></a>Skalowanie w poziomie
Ogólny format polecenia skalowania w poziomie:
```console
azdata arc postgres server edit -n <server group name> -w <target number of worker nodes>
```

> [!CAUTION]
> Wersja zapoznawcza nie obsługuje zmniejszania skali w poziomie. Na przykład nie jest jeszcze możliwe zmniejszenie liczby węzłów roboczych. Jeśli trzeba to zrobić, należy wyodrębnić dane/utworzyć kopię zapasową danych, usunąć grupę serwerów, utworzyć nową grupę serwerów z mniejszą liczbą węzłów roboczych, a następnie zaimportować dane.

W tym przykładzie zwiększamy liczbę węzłów procesu roboczego z 2 do 4, uruchamiając następujące polecenie:

```console
azdata arc postgres server edit -n postgres01 -w 4
```

Po dodaniu węzłów zostanie wyświetlony stan oczekiwania dla grupy serwerów. Na przykład:
```console
azdata arc postgres server list
```

```console
Name        State          Workers
----------  -------------  ---------
postgres01  Pending 4/5    4
```

Po udostępnieniu węzłów moduł przestawcy fragmentu zostanie uruchomiony automatycznie i ponownie dystrybuuje dane do nowych węzłów. Skalowanie w poziomie jest operacją online. Gdy węzły są dodawane, a dane są ponownie dystrybuowane między węzłami, dane pozostają dostępne dla zapytań.

### <a name="verify-the-new-shape-of-the-server-group-optional"></a>Sprawdź nowy kształt grupy serwerów (opcjonalnie)
Użyj jednej z poniższych metod, aby sprawdzić, czy Grupa serwerów używa teraz dodatkowych dodanych węzłów procesu roboczego.

#### <a name="with-azdata"></a>Z azdata:
Uruchom polecenie:
```console
azdata arc postgres server list
```

Zwraca listę grup serwerów utworzonych w przestrzeni nazw i wskazuje liczbę węzłów procesu roboczego. Na przykład:
```console
Name        State    Workers
----------  -------  ---------
postgres01  Ready    4
```

#### <a name="with-kubectl"></a>Z polecenia kubectl:
Uruchom polecenie:
```console
kubectl get postgresql-12
```

Zwraca listę grup serwerów utworzonych w przestrzeni nazw i wskazuje liczbę węzłów procesu roboczego. Na przykład:
```console
NAME         STATE   READY-PODS   EXTERNAL-ENDPOINT   AGE
postgres01   Ready   4/4          10.0.0.4:31066      4d20h
```
> [!NOTE]
> Jeśli utworzono grupę serwerów w wersji 11 PostgreSQL zamiast 12, uruchom następujące polecenie zamiast: _polecenia kubectl Get PostgreSQL-11_

#### <a name="with-a-sql-query"></a>Za pomocą zapytania SQL:
Połącz się z grupą serwerów za pomocą wybranego przez siebie narzędzia klienta i uruchom następujące zapytanie:

```sql
SELECT * FROM pg_dist_node;
```

```console
 nodeid | groupid |                       nodename                        | nodeport | noderack | hasmetadata | isactive | noderole | nodecluster | metadatasynced | shouldhaveshards
--------+---------+-------------------------------------------------------+----------+----------+-------------+----------+----------+-------------+----------------+------------------
      1 |       1 | pg1-1.pg1-svc.default.svc.cluster.local |     5432 | default  | f           | t        | primary  | default     | f              | t
      2 |       2 | pg1-2.pg1-svc.default.svc.cluster.local |     5432 | default  | f           | t        | primary  | default     | f              | t
      3 |       3 | pg1-3.pg1-svc.default.svc.cluster.local |     5432 | default  | f           | t        | primary  | default     | f              | t
      4 |       4 | pg1-4.pg1-svc.default.svc.cluster.local |     5432 | default  | f           | t        | primary  | default     | f              | t
(4 rows)
```

## <a name="return-to-the-scenario"></a>Wróć do scenariusza

Jeśli chcesz porównać czas wykonywania zapytania wyboru liczby z przykładowym zestawem danych, Użyj tej samej kwerendy Count. Może być używany przez cztery węzły procesu roboczego bez żadnych zmian w instrukcji SQL.

```sql
SELECT COUNT(*) FROM github_events;
```
Zanotuj czas wykonywania.


> [!NOTE]
> W zależności od środowiska — na przykład jeśli w ramach maszyny wirtualnej z jednym węzłem wdrożono grupę serwerów testowych, `kubeadm` może wystąpić niewielkie zwiększenie czasu wykonywania. Aby lepiej zrozumieć typ ulepszeń wydajności, które można osiągnąć przy użyciu usługi Azure ARC z włączonym skalowaniem PostgreSQL, Obejrzyj następujące krótkie filmy wideo:
>* [Wysoka wydajność HTAP za pomocą usługi Azure PostgreSQL do skalowania (Citus)](https://www.youtube.com/watch?v=W_3e07nGFxY)
>* [Kompilowanie aplikacji HTAP przy użyciu języka Python & PostgreSQL platformy Azure (Citus)](https://www.youtube.com/watch?v=YDT8_riLLs0)


## <a name="next-steps"></a>Następne kroki

- Przeczytaj [, jak skalować w górę i w dół (pamięć, rdzeni wirtualnych) grupę serwerów PostgreSQL z funkcją Azure Arc](scale-up-down-postgresql-hyperscale-server-group-using-cli.md)
- Przeczytaj informacje na temat sposobu ustawiania parametrów serwera w grupie serwerów PostgreSQL na potrzeby skalowania w usłudze Azure Arc
- Zapoznaj się z pojęciami i przewodnikami dotyczącymi Azure Database for PostgreSQL skalowania w celu rozproszenia danych między wieloma węzłami PostgreSQL. :
    * [Węzły i tabele](../../postgresql/concepts-hyperscale-nodes.md)
    * [Określanie typu aplikacji](../../postgresql/concepts-hyperscale-app-type.md)
    * [Wybieranie kolumny dystrybucji](../../postgresql/concepts-hyperscale-choose-distribution-column.md)
    * [Kolokacja tabeli](../../postgresql/concepts-hyperscale-colocation.md)
    * [Rozpowszechnianie i modyfikowanie tabel](../../postgresql/howto-hyperscale-modify-distributed-tables.md)
    * [Projektowanie bazy danych z wieloma dzierżawcami](../../postgresql/tutorial-design-database-hyperscale-multi-tenant.md)*
    * [Projektowanie pulpitu nawigacyjnego analizy w czasie rzeczywistym](../../postgresql/tutorial-design-database-hyperscale-realtime.md)*

 > \* W powyższych dokumentach Pomiń sekcje **Zaloguj się do Azure Portal**, & **Utwórz Azure Database for PostgreSQL-Citus**. Zaimplementuj pozostałe kroki w ramach wdrożenia usługi Azure Arc. Te sekcje są specyficzne dla Azure Database for PostgreSQL Citus) oferowanych jako usługa PaaS w chmurze platformy Azure, ale inne części dokumentów są bezpośrednio stosowane do wieloskalowania z włączonym systemem Azure Arc PostgreSQL.

- [Konfiguracja magazynu i pojęcia dotyczące magazynu Kubernetes](storage-configuration.md)
- [Model zasobów Kubernetes](https://github.com/kubernetes/community/blob/master/contributors/design-proposals/scheduling/resources.md#resource-quantities)
