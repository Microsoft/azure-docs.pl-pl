---
title: Uaktualnianie grupy serwerów (Citus) — Azure Database for PostgreSQL
description: W tym artykule opisano, jak uaktualnić PostgreSQL i Citus w Azure Database for PostgreSQL-(Citus).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 4/5/2021
ms.openlocfilehash: 3758e2d458e1a6bd052ac746ac361de033d508e9
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/07/2021
ms.locfileid: "107024025"
---
# <a name="upgrade-hyperscale-citus-server-group"></a>Grupa serwerów Citus (upgrade-Scale)

W tych instrukcjach opisano sposób uaktualniania do nowej wersji głównej programu PostgreSQL we wszystkich węzłach grupy serwerów.

## <a name="test-the-upgrade-first"></a>Najpierw Przetestuj uaktualnienie

Uaktualnienie PostgreSQL powoduje więcej zmian niż można wyobrazić, ponieważ funkcja (Citus) również uaktualnia [rozszerzenia bazy danych](concepts-hyperscale-extensions.md), w tym rozszerzenie Citus.
Zdecydowanie zalecamy przetestowanie aplikacji przy użyciu nowej wersji PostgreSQL i Citus przed uaktualnieniem środowiska produkcyjnego.

Wygodnym sposobem na przetestowanie jest skopiowanie grupy serwerów przy użyciu funkcji [przywracania do punktu w czasie](concepts-hyperscale-backup.md#point-in-time-restore-pitr). Uaktualnij kopię i przetestuj aplikację. Po sprawdzeniu, czy wszystko działa prawidłowo, Uaktualnij oryginalną grupę serwerów.

## <a name="upgrade-a-server-group-in-the-azure-portal"></a>Uaktualnianie grupy serwerów w Azure Portal

1. W sekcji **Przegląd** grupy serwerów Citus (moja skala) wybierz przycisk **Uaktualnij** .
1. Zostanie wyświetlone okno dialogowe z bieżącą wersją PostgreSQL i Citus.
   Wybierz nową wersję PostgreSQL na liście **Uaktualnij do** .
1. Przed uaktualnieniem Sprawdź wartość w **wersji Citus** .
   Ta wartość zmienia się w zależności od wybranej wersji PostgreSQL.
1. Wybierz przycisk **Uaktualnij** , aby kontynuować.

## <a name="next-steps"></a>Następne kroki

* Poznaj [obsługiwane wersje PostgreSQL](concepts-hyperscale-versions.md).
* Zobacz [, które rozszerzenia](concepts-hyperscale-extensions.md) są spakowane dla każdej wersji PostgreSQL w grupie serwerów Citus.
