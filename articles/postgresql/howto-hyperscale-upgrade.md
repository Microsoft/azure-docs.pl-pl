---
title: Uaktualnianie grupy serwerów — Hiperskala (Citus) — Azure Database for PostgreSQL
description: W tym artykule opisano, jak uaktualnić usługi PostgreSQL i Citus Azure Database for PostgreSQL — Hiperskala (Citus).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 4/5/2021
ms.openlocfilehash: 161204bf02ac36c1f5a3969cf57c61e98560c9b5
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107518902"
---
# <a name="upgrade-hyperscale-citus-server-group"></a>Uaktualnianie Hiperskala (Citus) grupy serwerów

W tych instrukcjach opisano sposób uaktualniania do nowej wersji głównych programu PostgreSQL we wszystkich węzłach grupy serwerów.

## <a name="test-the-upgrade-first"></a>Najpierw przetestuj uaktualnienie

Uaktualnienie bazy danych PostgreSQL powoduje więcej zmian niż można sobie wyobrazić, ponieważ program Hiperskala (Citus) także uaktualni rozszerzenia bazy danych [,](concepts-hyperscale-extensions.md)w tym rozszerzenie Citus.
Zdecydowanie zalecamy przetestowanie aplikacji przy użyciu nowej wersji postgreSQL i Citus przed uaktualnieniem środowiska produkcyjnego.

Wygodnym sposobem testowania jest skopiowanie grupy serwerów przy użyciu [przywracania do punktu w czasie.](concepts-hyperscale-backup.md#restore) Uaktualnij kopię i przetestuj pod jej względem aplikację. Po sprawdzeniu, czy wszystko działa prawidłowo, uaktualnij oryginalną grupę serwerów.

## <a name="upgrade-a-server-group-in-the-azure-portal"></a>Uaktualnianie grupy serwerów w Azure Portal

1. W **sekcji Przegląd** grupy serwerów Hiperskala (Citus) wybierz **przycisk Uaktualnij.**
1. Zostanie wyświetlone okno dialogowe z bieżącą wersją programu PostgreSQL i citus.
   Wybierz nową wersję postgreSQL z **listy Uaktualnij do.**
1. Sprawdź, czy wartość w wersji **Citus po uaktualnieniu** jest zgodnie z oczekiwaniami.
   Ta wartość zmienia się w zależności od wybranej wersji postgreSQL.
1. Wybierz przycisk **Uaktualnij,** aby kontynuować.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej [o obsługiwanych wersjach programu PostgreSQL.](concepts-hyperscale-versions.md)
* Sprawdź, [które rozszerzenia są](concepts-hyperscale-extensions.md) spakowane z każdą wersją postgreSQL w Hiperskala (Citus) grupy serwerów.
