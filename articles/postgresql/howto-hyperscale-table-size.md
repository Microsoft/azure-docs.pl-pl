---
title: Określanie rozmiaru tabeli — Citus) — Azure Database for PostgreSQL
description: Jak znaleźć prawdziwy rozmiar tabel rozproszonych w grupie serwerów Citus
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 1/5/2021
ms.openlocfilehash: 6ebdbe250862ccd462259900ba56d007f002a52f
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/06/2021
ms.locfileid: "97937681"
---
# <a name="determine-table-and-relation-size"></a>Określanie rozmiaru tabeli i relacji

Typowy sposób znajdowania rozmiarów tabeli w PostgreSQL, `pg_total_relation_size` znacząco w obszarze — raporty rozmiaru tabel rozproszonych w ramach skalowania (Citus).
Cała ta funkcja działa w grupie serwerów z Citus, aby odsłonić rozmiar tabel w węźle koordynatora.  W rzeczywistości dane w tabelach rozproszonych znajdują się w węzłach procesu roboczego (w fragmentów), a nie na koordynatorze. Rzeczywista miara rozmiaru tabeli rozproszonej jest uzyskiwana jako suma rozmiarów fragmentu. Funkcja Citus) oferuje funkcje pomocnika do wykonywania zapytań dotyczących tych informacji.

<table>
<colgroup>
<col style="width: 40%" />
<col style="width: 59%" />
</colgroup>
<thead>
<tr class="header">
<th>Funkcja</th>
<th>Zwraca</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td>citus_relation_size (relation_name)</td>
<td><ul>
<li>Rozmiar rzeczywistych danych w tabeli ("<a href="https://www.postgresql.org/docs/current/static/storage-file-layout.html">rozwidlenie główne</a>").</li>
<li>Relacją może być nazwa tabeli lub indeksu.</li>
</ul></td>
</tr>
<tr class="even">
<td>citus_table_size (relation_name)</td>
<td><ul>
<li><p>citus_relation_size plus:</p>
<blockquote>
<ul>
<li>rozmiar <a href="https://www.postgresql.org/docs/current/static/storage-fsm.html">mapy wolnego miejsca</a></li>
<li>rozmiar <a href="https://www.postgresql.org/docs/current/static/storage-vm.html">mapy widoczności</a></li>
</ul>
</blockquote></li>
</ul></td>
</tr>
<tr class="odd">
<td>citus_total_relation_size (relation_name)</td>
<td><ul>
<li><p>citus_table_size plus:</p>
<blockquote>
<ul>
<li>rozmiar indeksów</li>
</ul>
</blockquote></li>
</ul></td>
</tr>
</tbody>
</table>

Te funkcje są analogiczne do trzech standardowych [funkcji rozmiaru obiektów](https://www.postgresql.org/docs/current/static/functions-admin.html#FUNCTIONS-ADMIN-DBSIZE)PostgreSQL, z wyjątkiem sytuacji, gdy nie mogą połączyć się z węzłem.

## <a name="example"></a>Przykład

Oto jak wyświetlić listę rozmiarów wszystkich tabel rozproszonych:

``` postgresql
SELECT logicalrelid AS name,
       pg_size_pretty(citus_table_size(logicalrelid)) AS size
  FROM pg_dist_partition;
```

Dane wyjściowe:

```
┌───────────────┬───────┐
│     name      │ size  │
├───────────────┼───────┤
│ github_users  │ 39 MB │
│ github_events │ 37 MB │
└───────────────┴───────┘
```

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak [skalować grupę serwerów](howto-hyperscale-scale-grow.md) , aby przechowywać więcej danych.
* Rozróżniaj [Typy tabel](concepts-hyperscale-nodes.md) w grupie serwerów Citus.
