---
title: Rozproszone dane — Citus — Azure Database for PostgreSQL
description: Informacje o tabelach rozproszonych, tabelach referencyjnych, tabelach lokalnych i fragmentów w Azure Database for PostgreSQL.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: 7757fdb4953640597a805c3d74a9e1ef08ef2c07
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "86114503"
---
# <a name="distributed-data-in-azure-database-for-postgresql--hyperscale-citus"></a>Dane rozproszone w Azure Database for PostgreSQL — skalowanie (Citus)

W tym artykule opisano trzy typy tabel w Azure Database for PostgreSQL — funkcja Citus).
Pokazuje, jak tabele rozproszone są przechowywane jako fragmentów, i sposób, w jaki fragmentów są umieszczane w węzłach.

## <a name="table-types"></a>Typy table

Istnieją trzy typy tabel w grupie serwerów ze skalą (Citus), z których każdy jest używany do różnych celów.

### <a name="type-1-distributed-tables"></a>Typ 1: tabele rozproszone

Pierwszy typ, a najpopularniejsze, są tabelami rozproszonymi. Wyglądają one jako normalne tabele do instrukcji SQL, ale są one podzielone na partycje w węzłach procesu roboczego. Oznacza to, że wiersze tabeli są przechowywane w różnych węzłach w tabelach fragmentów o nazwie fragmentów.

Funkcja przeskalowania (Citus) jest uruchamiana nie tylko SQL, ale instrukcje języka DDL w całym klastrze.
Zmiana schematu rozproszonej tabeli kaskadowej w celu zaktualizowania wszystkich fragmentów tabeli między pracownikami.

#### <a name="distribution-column"></a>Kolumna dystrybucji

Funkcja przeskaling (Citus) używa algorytmu fragmentowania do przypisywania wierszy do fragmentów. Przypisanie jest wykonywane w sposób deterministyczny na podstawie wartości kolumny tabeli zwanej kolumną dystrybucji. Administrator klastra musi wyznaczyć tę kolumnę podczas dystrybucji tabeli.
Wybór właściwego wyboru jest istotny dla wydajności i funkcjonalności.

### <a name="type-2-reference-tables"></a>Typ 2: tabele odwołań

Tabela referencyjna jest typem rozproszonej tabeli, której cała zawartość jest skoncentrowana na jednym fragmentu. Fragmentu jest replikowana na każdym procesie roboczym. Zapytania dotyczące dowolnego pracownika mogą uzyskać dostęp do informacji referencyjnych lokalnie, bez narzutu sieciowego żądającego wierszy z innego węzła. Tabele referencyjne nie mają kolumny dystrybucji, ponieważ nie ma potrzeby odróżniania oddzielnych fragmentów na wiersz.

Tabele odwołań są zwykle małe i są używane do przechowywania danych, które są istotne dla zapytań uruchomionych w dowolnym węźle procesu roboczego. Przykładem są wartości wyliczane, takie jak Stany zamówień lub kategorie produktów.

### <a name="type-3-local-tables"></a>Typ 3: tabele lokalne

W przypadku używania funkcji Citus (preskalowanie) węzeł koordynatora, z którym nawiązujesz połączenie, jest zwykłą bazą danych PostgreSQL. Można utworzyć zwykłe tabele na koordynatorze i zrezygnować z ich fragmentu.

Dobrym kandydatem do tabel lokalnych byłyby małe tabele administracyjne, które nie uczestniczą w zapytaniach sprzężenia. Przykładem jest tabela użytkownicy do logowania i uwierzytelniania aplikacji.

## <a name="shards"></a>Fragmentów

W poprzedniej sekcji opisano, jak tabele rozproszone są przechowywane jako fragmentów w węzłach procesu roboczego. W tej sekcji omówiono więcej szczegółów technicznych.

`pg_dist_shard`Tabela metadanych w koordynatorze zawiera wiersz dla każdej fragmentu tabeli rozproszonej w systemie. Wiersz jest zgodny z IDENTYFIKATORem fragmentu z zakresem liczb całkowitych w przestrzeni skrótów (shardminvalue, shardmaxvalue).

```sql
SELECT * from pg_dist_shard;
 logicalrelid  | shardid | shardstorage | shardminvalue | shardmaxvalue
---------------+---------+--------------+---------------+---------------
 github_events |  102026 | t            | 268435456     | 402653183
 github_events |  102027 | t            | 402653184     | 536870911
 github_events |  102028 | t            | 536870912     | 671088639
 github_events |  102029 | t            | 671088640     | 805306367
 (4 rows)
```

Jeśli węzeł koordynatora chce określić, które fragmentu przechowuje wiersz `github_events` , miesza wartość kolumny dystrybucji w wierszu. Następnie węzeł sprawdzi, który \' zakres fragmentu zawiera wartość zmieszaną. Zakresy są zdefiniowane, aby obraz funkcji skrótu był ich rozłącznym złożeniem.

### <a name="shard-placements"></a>Fragmentu

Załóżmy, że fragmentu 102027 jest skojarzony z danym wierszem. Wiersz jest odczytywany lub zapisywana w tabeli o nazwie `github_events_102027` w jednym z procesów roboczych. Który proces roboczy? Jest to określane całkowicie przez tabele metadanych. Mapowanie elementu fragmentu na proces roboczy jest nazywane rozmieszczeniem fragmentu.

Węzeł koordynator ponownie zapisuje zapytania do fragmentów odwołujących się do określonych tabel, takich jak `github_events_102027` i uruchamia te fragmenty na odpowiednich procesach roboczych. Oto przykład zapytania uruchamianego w tle, aby znaleźć węzeł przechowujący fragmentu o IDENTYFIKATORze 102027.

```sql
SELECT
    shardid,
    node.nodename,
    node.nodeport
FROM pg_dist_placement placement
JOIN pg_dist_node node
  ON placement.groupid = node.groupid
 AND node.noderole = 'primary'::noderole
WHERE shardid = 102027;
```

```output
┌─────────┬───────────┬──────────┐
│ shardid │ nodename  │ nodeport │
├─────────┼───────────┼──────────┤
│  102027 │ localhost │     5433 │
└─────────┴───────────┴──────────┘
```

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [wybrać kolumnę dystrybucji](concepts-hyperscale-choose-distribution-column.md) dla tabel rozproszonych.
