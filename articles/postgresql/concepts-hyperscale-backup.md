---
title: Tworzenie kopii zapasowych i przywracanie — skalowanie (Citus) — Azure Database for PostgreSQL
description: Ochrona danych przed przypadkowym uszkodzeniem lub usunięciem
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 04/28/2020
ms.openlocfilehash: 9656e6ab608665e325ac5f772e06df9ac296c521
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92485379"
---
# <a name="backup-and-restore-in-azure-database-for-postgresql---hyperscale-citus"></a>Tworzenie kopii zapasowych i przywracanie w Azure Database for PostgreSQL-ze skalowaniem (Citus)

Azure Database for PostgreSQL — funkcja Citus) automatycznie tworzy kopie zapasowe każdego węzła i zapisuje je w magazynie lokalnie nadmiarowy. Za pomocą kopii zapasowych można przywrócić klaster z Citus w określonym czasie. Tworzenie kopii zapasowych i przywracanie jest istotną częścią strategii ciągłości biznesowej, ponieważ chronią dane przed przypadkowym uszkodzeniem lub usunięciem.

## <a name="backups"></a>Tworzenie kopii zapasowych

Co najmniej raz dziennie Azure Database for PostgreSQL wykonuje kopie zapasowe migawek plików danych i dziennika transakcji bazy danych. Kopie zapasowe umożliwiają przywrócenie serwera do dowolnego punktu w czasie w okresie przechowywania. (Okres przechowywania jest obecnie 35 dni dla wszystkich klastrów). Wszystkie kopie zapasowe są szyfrowane przy użyciu szyfrowania AES 256-bitowego.

W regionach platformy Azure, które obsługują strefy dostępności, migawki kopii zapasowych są przechowywane w trzech strefach dostępności. Tak długo, jak co najmniej jedna strefa dostępności jest w trybie online, klaster Citus jest dostępnych.

Nie można eksportować plików kopii zapasowej. Mogą być używane tylko w przypadku operacji przywracania w Azure Database for PostgreSQL.

### <a name="backup-storage-cost"></a>Koszt magazynu kopii zapasowych

Aktualną cenę magazynu kopii zapasowych można znaleźć na [stronie z cennikiem](https://azure.microsoft.com/pricing/details/postgresql/hyperscale-citus/)Azure Database for PostgreSQL-Citus.

## <a name="restore"></a>Przywracanie

W Azure Database for PostgreSQL przywracanie klastra Citus (prescaleing) powoduje utworzenie nowego klastra z kopii zapasowych pierwotnych węzłów. 

> [!IMPORTANT]
>Można przywrócić tylko klaster ze skalowaniem (Citus) w ramach tej samej subskrypcji i grupy zasobów oraz inną nazwę klastra.


> [!IMPORTANT]
> Nie można przywrócić usuniętych klastrów Citus. Jeśli usuniesz klaster, wszystkie węzły należące do klastra zostaną usunięte i nie będzie można go odzyskać. Aby chronić zasoby klastra, po ich wdrożeniu przed przypadkowym usunięciem lub nieoczekiwanymi zmianami Administratorzy mogą korzystać z [blokad zarządzania](../azure-resource-manager/management/lock-resources.md).

### <a name="point-in-time-restore-pitr"></a>Przywracanie do punktu w czasie (kopie)

Klaster można przywrócić do dowolnego punktu w czasie w ciągu ostatnich 35 dni.
Przywracanie do punktu w czasie jest przydatne w wielu scenariuszach. Gdy na przykład użytkownik przypadkowo usunie dane, porzuci ważną tabelę lub bazę danych bądź gdy aplikacja przypadkowo zastąpi dobre dane za pomocą nieprawidłowych danych.

Proces przywracania powoduje utworzenie nowego klastra w tym samym regionie, subskrypcji i grupie zasobów platformy Azure co wersja oryginalna. Klaster ma oryginalną konfigurację: tę samą liczbę węzłów, liczbę rdzeni wirtualnych, rozmiar magazynu, role użytkowników, wersję PostgreSQL i wersję rozszerzenia Citus.

Ustawienia zapory i parametry serwera PostgreSQL nie są zachowywane z oryginalnej grupy serwerów, są resetowane do wartości domyślnych. Zapora uniemożliwi wszystkie połączenia. Po przywróceniu trzeba ręcznie dostosować te ustawienia.

> [!IMPORTANT]
> Musisz otworzyć żądanie obsługi, aby przeprowadzić przywracanie do punktu w czasie dla klastra Citus.

### <a name="post-restore-tasks"></a>Post-restore tasks

Po przywróceniu z dowolnego mechanizmu odzyskiwania należy wykonać następujące czynności, aby zapewnić i uruchomić kopie zapasowe użytkowników i aplikacji:

* Jeśli nowy serwer ma zastąpić oryginalny serwer, przekierować klientów i aplikacje klienckie na nowy serwer
* Upewnij się, że istnieją odpowiednie reguły zapory na poziomie serwera i sieci wirtualnej, aby użytkownicy mogli się łączyć. Te reguły nie są kopiowane z oryginalnej grupy serwerów.
* Dostosuj parametry serwera PostgreSQL zgodnie z wymaganiami. Parametry nie są kopiowane z oryginalnej grupy serwerów.
* Upewnij się, że istnieją odpowiednie identyfikatory logowania i uprawnienia na poziomie bazy danych
* W razie potrzeby skonfiguruj alerty

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [strefach dostępności platformy Azure](../availability-zones/az-overview.md).
* Ustawianie [sugerowanych alertów](./howto-hyperscale-alert-on-metric.md#suggested-alerts) w grupach serwerów Citus.