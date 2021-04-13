---
title: Ponowne równoważenie fragmentów-Citus) — Azure Database for PostgreSQL
description: Równomierne dystrybuowanie fragmentów między serwerami w celu uzyskania lepszej wydajności
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 04/09/2021
ms.openlocfilehash: 63322fac4c6ad5b705deedcd8a80466ddd803814
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107305705"
---
# <a name="rebalance-shards-in-hyperscale-citus-server-group"></a>Ponowne równoważenie fragmentów w grupie serwerów Citus

Aby skorzystać z nowo dodanych węzłów, należy ponownie zrównoważyć rozproszoną tabelę [fragmentów](concepts-hyperscale-distributed-data.md#shards), co oznacza przeniesienie niektórych fragmentów z istniejących węzłów do nowych.

## <a name="determine-if-the-server-group-needs-a-rebalance"></a>Ustal, czy Grupa serwerów wymaga ponownego zrównoważenia

Azure Portal można zobaczyć, czy dane są dystrybuowane równomiernie między węzłami procesu roboczego w grupie serwerów. Aby je wyświetlić, przejdź do strony **modułu równoważenia fragmentu** w menu **Zarządzanie grupą serwerów** . Jeśli dane są pochylone między procesami roboczymi, zaleca się, aby ponownie **zrównoważyć** komunikat wraz z listą rozmiaru każdego węzła.

Jeśli dane są już zrównoważone, zobaczysz, że **nie jest to zalecane**.

## <a name="run-the-shard-rebalancer"></a>Uruchamianie modułu równoważenia fragmentu

Aby uruchomić moduł równoważenia fragmentu, należy połączyć się z węzłem koordynatora grupy serwerów i uruchomić funkcję SQL [rebalance_table_shards](reference-hyperscale-functions.md#rebalance_table_shards) w tabelach rozproszonych. Funkcja ponownie równoważy wszystkie tabele w grupie wspólnej [lokalizacji](concepts-hyperscale-colocation.md) tabeli o nazwie w jej argumencie. W ten sposób nie trzeba wywoływać funkcji dla każdej tabeli rozproszonej, po prostu wywołaj ją na reprezentatywnej tabeli z każdej grupy wspólnej lokalizacji.

```sql
SELECT rebalance_table_shards('distributed_table_name');
```

## <a name="monitor-rebalance-progress"></a>Monitorowanie postępu ponownego równoważenia

Aby obserwować moduł równoważenia obciążenia po jego uruchomieniu, Wróć do Azure Portal. Otwórz stronę **modułu równoważenia fragmentu** w temacie **Zarządzanie grupami serwerów**. Spowoduje to wyświetlenie ponownego **równoważenia** komunikatów wraz z dwiema tabelami.

W pierwszej tabeli przedstawiono liczbę fragmentów przenoszonych do lub z węzła, na przykład "6 z 24 przeniesione w". W drugiej tabeli przedstawiono postęp według tabeli bazy danych: nazwa, wartość fragmentu, której dotyczy rozmiar danych i stan ponownego równoważenia.

Wybierz przycisk **Odśwież** , aby zaktualizować stronę. Po zakończeniu ponownego równoważenia ponownie zostanie wyświetlony komunikat ponowne **równoważenie nie jest zalecane**.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [opcjach wydajności](concepts-hyperscale-configuration-options.md)grupy serwerów.
- [Skalowanie grupy serwerów w](howto-hyperscale-scale-grow.md) górę lub w dół
- Zobacz materiały referencyjne [rebalance_table_shards](reference-hyperscale-functions.md#rebalance_table_shards)
