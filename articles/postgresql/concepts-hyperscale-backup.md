---
title: Tworzenie i przywracanie kopii zapasowych — Hiperskala (Citus) — Azure Database for PostgreSQL
description: Ochrona danych przed przypadkowym uszkodzeniem lub usunięciem
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 04/28/2020
ms.openlocfilehash: 8dfc82ce79f33553be5220b52a1e415d99c26518
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107483915"
---
# <a name="backup-and-restore-in-azure-database-for-postgresql---hyperscale-citus"></a>Tworzenie i przywracanie kopii zapasowej w Azure Database for PostgreSQL — Hiperskala (Citus)

Azure Database for PostgreSQL — Hiperskala (Citus) automatycznie tworzy kopie zapasowe każdego węzła i zapisuje je w magazynie lokalnie nadmiarowym. Kopie zapasowe mogą służyć do przywracania Hiperskala (Citus) klastra do określonego czasu. Tworzenie kopii zapasowych i przywracanie jest istotną częścią strategii ciągłości biznesowej, ponieważ chronią dane przed przypadkowym uszkodzeniem lub usunięciem.

## <a name="backups"></a>Tworzenie kopii zapasowych

Co najmniej raz dziennie program Azure Database for PostgreSQL kopie zapasowe migawek plików danych i dziennika transakcji bazy danych. Kopie zapasowe umożliwiają przywrócenie serwera do dowolnego punktu w czasie w okresie przechowywania. (Okres przechowywania dla wszystkich klastrów wynosi obecnie 35 dni). Wszystkie kopie zapasowe są szyfrowane przy użyciu 256-bitowego szyfrowania AES.

W regionach świadczenia usługi Azure, które obsługują strefy dostępności, migawki kopii zapasowych są przechowywane w trzech strefach dostępności. Dopóki co najmniej jedna strefa dostępności jest w trybie online, klaster Hiperskala (Citus) można przywrócić.

Nie można eksportować plików kopii zapasowej. Mogą być używane tylko do operacji przywracania w Azure Database for PostgreSQL.

### <a name="backup-storage-cost"></a>Koszt magazynu kopii zapasowych

Aby uzyskać bieżące ceny magazynu kopii zapasowych, zobacz stronę Azure Database for PostgreSQL — Hiperskala (Citus) [cennik.](https://azure.microsoft.com/pricing/details/postgresql/hyperscale-citus/)

## <a name="restore"></a>Przywracanie

W Azure Database for PostgreSQL przywracanie klastra Hiperskala (Citus) tworzy nowy klaster z kopii zapasowych oryginalnych węzłów. 

> [!IMPORTANT]
>Klaster można przywrócić tylko Hiperskala (Citus) w ramach tej samej subskrypcji i grupy zasobów oraz z inną nazwą klastra.


> [!IMPORTANT]
> Usuniętych Hiperskala (Citus) nie można przywrócić. Jeśli usuniesz klaster, wszystkie węzły należące do klastra zostaną usunięte i nie będzie można ich odzyskać. Aby chronić zasoby klastra po wdrożeniu, przed przypadkowym usunięciem lub nieoczekiwanymi zmianami, administratorzy mogą korzystać z [blokad zarządzania](../azure-resource-manager/management/lock-resources.md).

### <a name="point-in-time-restore-pitr"></a>Przywracanie do punktu w czasie (PITR)

Klaster można przywrócić do dowolnego punktu w czasie w ciągu ostatnich 35 dni.
Przywracanie do punktu w czasie jest przydatne w wielu scenariuszach. Gdy na przykład użytkownik przypadkowo usunie dane, porzuci ważną tabelę lub bazę danych bądź gdy aplikacja przypadkowo zastąpi dobre dane za pomocą nieprawidłowych danych.

Proces przywracania tworzy nowy klaster w tym samym regionie, subskrypcji i grupie zasobów platformy Azure co oryginalny. Klaster ma oryginalną konfigurację: taką samą liczbę węzłów, liczbę rdzeni wirtualnych, rozmiar magazynu, role użytkownika, wersję postgreSQL i wersję rozszerzenia Citus.

Ustawienia zapory i parametry serwera PostgreSQL nie są zachowywane z oryginalnej grupy serwerów. Są one resetowane do wartości domyślnych. Zapora uniemożliwi wszystkie połączenia. Po przywróceniu należy ręcznie dostosować te ustawienia. Ogólnie rzecz biorąc, zapoznaj się z naszą listą sugerowanych [zadań po przywróceniu](howto-hyperscale-restore-portal.md#post-restore-tasks).

## <a name="next-steps"></a>Następne kroki

* Zobacz kroki [przywracania grupy serwerów w](howto-hyperscale-restore-portal.md) Azure Portal.
* Dowiedz się więcej [o strefach dostępności platformy Azure.](../availability-zones/az-overview.md)
