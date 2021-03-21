---
title: Ponowne równoważenie fragmentów-Citus) — Azure Database for PostgreSQL
description: Równomierne dystrybuowanie fragmentów między serwerami w celu uzyskania lepszej wydajności
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 11/17/2020
ms.openlocfilehash: b69c4fc3ff16cf30181a3529f61af7126b92950b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "95026390"
---
# <a name="rebalance-shards-in-hyperscale-citus-server-group"></a>Ponowne równoważenie fragmentów w grupie serwerów Citus

Aby skorzystać z nowo dodanych węzłów, należy ponownie zrównoważyć rozproszoną tabelę [fragmentów](concepts-hyperscale-distributed-data.md#shards), co oznacza przeniesienie niektórych fragmentów z istniejących węzłów do nowych. Najpierw sprawdź, czy nowi pracownicy pomyślnie ukończyli Inicjowanie obsługi administracyjnej. Następnie należy uruchomić moduł równoważenia fragmentu, łącząc się z węzłem koordynatora klastra z PSQL i uruchamiając następujące polecenie:

```sql
SELECT rebalance_table_shards('distributed_table_name');
```

Funkcja [rebalance_table_shards](reference-hyperscale-functions.md#rebalance_table_shards) ponownie równoważy wszystkie tabele w grupie wspólnej [lokalizacji](concepts-hyperscale-colocation.md) tabeli o nazwie w argumencie. W ten sposób nie trzeba wywoływać funkcji dla każdej tabeli rozproszonej, po prostu wywołaj ją na reprezentatywnej tabeli z każdej grupy wspólnej lokalizacji.

**Następne kroki**


- Dowiedz się więcej o [opcjach wydajności](concepts-hyperscale-configuration-options.md)grupy serwerów.
- [Skalowanie grupy serwerów w](howto-hyperscale-scale-grow.md) górę lub w dół
- Zobacz materiały referencyjne [rebalance_table_shards](reference-hyperscale-functions.md#rebalance_table_shards)
