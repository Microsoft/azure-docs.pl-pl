---
title: Węzły — funkcja do skalowania (Citus) — Azure Database for PostgreSQL
description: Dowiedz się więcej na temat typów węzłów i tabel w grupie serwerów w Azure Database for PostgreSQL.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 07/28/2019
ms.openlocfilehash: b3eda2c8de8319552f32938f20ff98af0e0a49fc
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91314831"
---
# <a name="nodes-and-tables-in-azure-database-for-postgresql--hyperscale-citus"></a>Węzły i tabele w Azure Database for PostgreSQL — funkcja do skalowania (Citus)

## <a name="nodes"></a>Węzły

Typ hostujący (Citus) Azure Database for PostgreSQL umożliwia współrzędnie serwerów (nazywanych węzłami) w architekturze "udostępnione Nothing". Węzły w grupie serwerów wspólnie przechowują więcej danych i wykorzystują więcej rdzeni procesora CPU niż jest to możliwe na jednym serwerze. Architektura umożliwia również skalowanie bazy danych przez dodanie większej liczby węzłów do grupy serwerów.

### <a name="coordinator-and-workers"></a>Koordynator i procesy robocze

Każda grupa serwerów ma węzeł koordynatora i wielu procesów roboczych. Aplikacje wysyłają swoje zapytania do węzła koordynatora, który przekazuje go do odpowiednich pracowników i gromadzi wyniki. Aplikacje nie mogą łączyć się bezpośrednio z pracownikami.

Funkcja Citus) umożliwia administratorowi bazy danych *dystrybuowanie* tabel, przechowując różne wiersze w różnych węzłach procesu roboczego. Tabele rozproszone to wydajność klucza do przedskalowania (Citus). Niepowodzenie dystrybucji tabel pozostawia je w całości w węźle koordynatora i nie może korzystać z równoległości między maszynami.

Dla każdego zapytania w tabelach rozproszonych koordynator kieruje go do jednego węzła procesu roboczego lub parallelizes go w kilka w zależności od tego, czy wymagane dane znajdują się na jednym lub wielu węzłach. Koordynator decyduje o tym, co należy zrobić, aby poznać tabele metadanych. Te tabele śledzą nazwy DNS i kondycję węzłów procesu roboczego oraz dystrybucję danych między węzłami.

## <a name="table-types"></a>Typy table

Istnieją trzy typy tabel w grupie serwerów z Citusą, które są przechowywane inaczej w węzłach i używane do różnych celów.

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

- [Określ typ aplikacji](concepts-hyperscale-app-type.md) , aby przygotować się do modelowania danych
