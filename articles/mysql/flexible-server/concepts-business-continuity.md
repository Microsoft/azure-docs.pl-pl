---
title: Przegląd ciągłości działania — Azure Database for MySQL elastyczny serwer
description: Dowiedz się więcej na temat pojęć związanych z ciągłością działania w Azure Database for MySQL elastycznym serwerze
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: conceptual
ms.date: 09/21/2020
ms.openlocfilehash: 4f9cc8321d5d1d19dbcb8294ad6205b01337ee72
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101715058"
---
# <a name="overview-of-business-continuity-with-azure-database-for-mysql---flexible-server-preview"></a>Przegląd ciągłości działania z serwerem elastycznym Azure Database for MySQL (wersja zapoznawcza)

> [!IMPORTANT]
> Serwer elastyczny Azure Database for MySQL jest obecnie w publicznej wersji zapoznawczej.

Azure Database for MySQL elastyczny serwer umożliwia funkcje ciągłości działania chroniące bazy danych w przypadku planowanej i nieplanowanej awarii. Funkcje, takie jak zautomatyzowane kopie zapasowe i wysoka dostępność, są różne dla różnych poziomów ochrony przed awarią oraz różnych zagrożeń związanych z czasem odzyskiwania i utratą danych. Podczas tworzenia architektury aplikacji w celu ochrony przed awariami należy wziąć pod uwagę cel czasu odzyskiwania (RTO) i cel punktu odzyskiwania dla każdej aplikacji. RTO to tolerancja przestoju i cel punktu odzyskiwania to tolerancja utraty danych po przerwie w działaniu usługi bazy danych.

W poniższej tabeli przedstawiono funkcje, które oferuje elastyczny serwer.

| **Funkcja** | **Opis** | **Ograniczenia** |
| ---------- | ----------- | ------------ |
| **Odzyskiwanie & kopii zapasowej** | Elastyczny serwer automatycznie wykonuje codzienne kopie zapasowe plików bazy danych i ciągle tworzy kopie zapasowe dzienników transakcji. Kopie zapasowe mogą być przechowywane przez dowolny okres od 1 do 35 dni. Serwer bazy danych będzie można przywrócić do dowolnego punktu w czasie w ramach okresu przechowywania kopii zapasowej. Czas odzyskiwania zależy od rozmiaru danych do przywrócenia i czasu na odzyskanie dziennika. Aby uzyskać więcej informacji, zobacz temat [koncepcje — tworzenie kopii zapasowych i przywracanie](./concepts-backup-restore.md) . |Dane kopii zapasowej pozostają w regionie |
| **Lokalna nadmiarowa kopia zapasowa** | Elastyczne kopie zapasowe serwera są automatycznie i bezpiecznie przechowywane w lokalnym nadmiarowym magazynie w regionie i w tej samej strefie dostępności. Lokalnie nadmiarowe kopie zapasowe replikuje pliki danych kopii zapasowej serwera trzy razy w jednej lokalizacji fizycznej w regionie podstawowym. Magazyn lokalnie nadmiarowy zapewnia co najmniej 99,999999999% (11 dziewięciu danych) trwałości obiektów w danym roku. Aby uzyskać więcej informacji, zobacz temat [koncepcje — tworzenie kopii zapasowych i przywracanie](./concepts-backup-restore.md) .| Dotyczy we wszystkich regionach |
| **Strefa nadmiarowa wysokiej dostępności** | Elastyczny serwer można wdrożyć w trybie wysokiej dostępności, który wdraża serwery podstawowe i rezerwowe w dwóch różnych strefach dostępności w obrębie regionu. Zapewnia to ochronę przed awariami na poziomie strefy, a także zmniejsza przestoje aplikacji podczas planowanych i nieplanowanych przestojów. Dane z serwera podstawowego są synchronicznie replikowane do repliki w stanie wstrzymania. Podczas każdego zdarzenia przestoju serwer bazy danych jest automatycznie przełączona w tryb failover do repliki w stanie wstrzymania. Zapoznaj się z [pojęciami — wysoka dostępność](./concepts-high-availability.md) , aby uzyskać więcej szczegółów. | Obsługiwane w warstwach obliczeniowych ogólnego przeznaczenia i zoptymalizowanych pod kątem pamięci. Dostępne tylko w regionach, w których jest dostępnych wiele stref.|
| **Udziały plików w warstwie Premium** | Pliki bazy danych są przechowywane w wysoce trwałych i niezawodnych udziałach plików platformy Azure w warstwie Premium, które zapewniają nadmiarowość danych z trzema kopiami replik przechowywanych w strefie dostępności z automatycznym odzyskiwaniem danych. Aby uzyskać więcej informacji, zapoznaj się z [udziałami plików w warstwie Premium](../../storage/files/storage-how-to-create-file-share.md) . | Dane przechowywane w strefie dostępności |

> [!IMPORTANT]
> W okresie zapoznawczym są oferowane bezpłatne warunki umowy SLA RTO i punktu odzyskiwania. Szczegóły podane na tej stronie są przeznaczone tylko do celów związanych z informacjami i planowaniem.

## <a name="planned-downtime-mitigation"></a>Planowane ograniczenie przestoju

Poniżej przedstawiono niektóre planowane scenariusze konserwacji, które ponoszą przestoje:

| **Scenariusz** | **Proces**|
| :------------ | :----------- |
| **Skalowanie obliczeniowe (użytkownik)**| Podczas wykonywania operacji skalowania obliczeń nowy elastyczny serwer jest inicjowany przy użyciu skalowalnej konfiguracji obliczeniowej. W istniejącym serwerze bazy danych można wykonać aktywne punkty kontrolne, połączenia klientów są opróżniane, wszystkie niezatwierdzone transakcje są anulowane, a następnie wyłączone. Magazyn jest następnie dołączany do nowego serwera, a baza danych zostanie uruchomiona, w razie potrzeby, w razie konieczności, przed zaakceptowaniem połączeń z klientami. |
| **Nowe wdrożenie oprogramowania (Azure)** | Nowe funkcje wdrażania lub rozwiązywania błędów są automatycznie wykonywane w ramach planowanej konserwacji usługi i można zaplanować, kiedy te działania mają być wykonywane. Aby uzyskać więcej informacji, zobacz [dokumentację](https://aka.ms/servicehealthpm), a także sprawdź [Portal](https://aka.ms/servicehealthpm) |
| **Uaktualnienia wersji pomocniczej (Azure)** | Azure Database for MySQL automatycznie poprawek serwerów baz danych do wersji pomocniczej ustalonej przez platformę Azure. Odbywa się to w ramach planowanej konserwacji usługi. Może to spowodować skrócenie przestoju w ciągu kilku sekund, a serwer bazy danych zostanie automatycznie uruchomiony ponownie z nową wersją pomocniczą. Aby uzyskać więcej informacji, zobacz [dokumentację](../concepts-monitoring.md#planned-maintenance-notification), a także sprawdź [Portal](https://aka.ms/servicehealthpm).|

Gdy elastyczny serwer jest skonfigurowany z **nadmiarową wysoką dostępnością strefy**, elastyczny serwer wykonuje operacje na serwerze rezerwy najpierw, a następnie na serwerze podstawowym bez trybu failover. Zapoznaj się z [pojęciami — wysoka dostępność](./concepts-high-availability.md) , aby uzyskać więcej szczegółów.

## <a name="unplanned-downtime-mitigation"></a>Nieplanowane ograniczenie przestoju

Nieplanowane przestoje mogą wystąpić w wyniku nieprzewidzianych awarii, w tym podstawowego błędu sprzętowego, problemów z siecią i błędów oprogramowania. Jeśli serwer bazy danych ulegnie awarii, jeśli zostanie skonfigurowany z wysoką dostępnością [HA], zostanie uaktywniona replika gotowości. Jeśli nie, zostanie automatycznie zainicjowany nowy serwer bazy danych. Chociaż nie można uniknąć nieplanowanych przestojów, elastyczny serwer zmniejsza czas przestoju przez automatyczne wykonywanie operacji odzyskiwania zarówno na serwerze bazy danych, jak i w warstwach magazynu, bez konieczności interwencji człowieka.

### <a name="unplanned-downtime-failure-scenarios-and-service-recovery"></a>Nieplanowany przestój: scenariusze awarii i odzyskiwanie usługi

Poniżej przedstawiono niektóre nieplanowane scenariusze błędów i proces odzyskiwania:

| **Scenariusz** | **Proces odzyskiwania [nie HA]** | **Proces odzyskiwania [HA]** |
| :---------- | ---------- | ------- |
| **Awaria serwera bazy danych** | Jeśli serwer bazy danych nie działa z powodu błędu sprzętowego, aktywne połączenia są porzucane i wszystkie transakcje numerów porządkowych określających są przerywane. Platforma Azure podejmie próbę ponownego uruchomienia serwera bazy danych. Jeśli to się powiedzie, zostanie wykonane odzyskiwanie bazy danych. Jeśli ponowne uruchomienie nie powiedzie się, zostanie podjęta próba ponownego uruchomienia serwera bazy danych w innym węźle fizycznym.  <br /> <br /> Czas odzyskiwania (RTO) zależy od różnych czynników, w tym działania w momencie wystąpienia błędu, takie jak duża transakcja i ilość odzyskiwania do wykonania podczas uruchamiania serwera bazy danych. <br /> <br /> Aplikacje korzystające z baz danych MySQL muszą zostać skompilowane w taki sposób, aby wykrywać i ponawiać próby porzucenia połączeń i transakcji zakończonych niepowodzeniem.  Po ponownym próbie aplikacji połączenia są kierowane do nowo utworzonego serwera bazy danych. | Jeśli wykryje awarię serwera bazy danych, zostanie uaktywniony serwer bazy danych w stanie wstrzymania, co zmniejsza przestoje. Więcej informacji można znaleźć na [stronie pojęć o wysokiej dostępności](concepts-high-availability.md) . RTO powinien być 60-120 s z elementem RPO = 0 |
| **Awaria magazynu** | Aplikacje nie widzą żadnego wpływu na problemy związane z magazynowaniem, takie jak awaria dysku lub uszkodzenie bloku fizycznego. Ponieważ dane są przechowywane w 3 kopiach, kopia danych jest obsługiwana przez trwające magazynowanie. Uszkodzenia bloków są automatycznie poprawiane. Jeśli kopia danych zostanie utracona, tworzona jest nowa kopia danych. | W przypadku błędów nieodwracalnych serwer elastyczny jest przełączona w tryb failover do repliki gotowości, aby zmniejszyć przestoje. Więcej informacji można znaleźć na [stronie pojęć o wysokiej dostępności](./concepts-high-availability.md) . |
| **Błędy logiczne/użytkownika** | Odzyskiwanie z błędów użytkowników, takich jak przypadkowo porzucane tabele lub nieprawidłowo aktualizowane dane, polega na przeprowadzeniu [odzyskiwania do punktu w czasie](concepts-backup-restore.md) (kopie) przez przywrócenie i odzyskanie danych do czasu, gdy wystąpi błąd.<br> <br>  Aby przywrócić tylko podzestaw baz danych lub określonych tabel, a nie wszystkich baz danych na serwerze bazy danych, można przywrócić serwer bazy danych w nowym wystąpieniu, wyeksportować tabele za pośrednictwem [pg_dump](https://www.postgresql.org/docs/current/app-pgdump.html), a następnie użyć [pg_restore](https://www.postgresql.org/docs/current/app-pgrestore.html) do przywrócenia tych tabel do bazy danych programu. | Te błędy użytkownika nie są chronione z wysoką dostępnością ze względu na fakt, że wszystkie operacje użytkownika są replikowane do stanu wstrzymania. |
| **Awaria strefy dostępności** | Chociaż jest to zdarzenie rzadkie, jeśli chcesz odzyskać sprawność po awarii poziomu strefy, możesz wykonać odzyskiwanie do punktu w czasie za pomocą kopii zapasowej i wybrać niestandardowy punkt przywracania, aby uzyskać dostęp do najnowszych danych. Nowy, elastyczny serwer zostanie wdrożony w innej strefie. Czas trwania przywracania zależy od poprzedniej kopii zapasowej i liczby dzienników transakcji do odzyskania. | Elastyczny serwer przeprowadza automatyczne przejście w tryb failover do lokacji w stanie wstrzymania. Więcej informacji można znaleźć na [stronie pojęć o wysokiej dostępności](./concepts-high-availability.md) . |
| **Awaria regionu** | Repliki obejmujące wiele regionów i funkcje przywracania geograficznego nie są jeszcze obsługiwane w wersji zapoznawczej. | |

> [!IMPORTANT]
> **Nie** można przywrócić usuniętych serwerów. Usunięcie serwera spowoduje również usunięcie wszystkich baz danych należących do serwera, których nie można odzyskać. Użyj [blokady zasobów platformy Azure](../../azure-resource-manager/management/lock-resources.md) , aby zapobiec przypadkowemu usunięciu serwera.

## <a name="next-steps"></a>Następne kroki

- Więcej informacji na temat [strefy nadmiarowej wysokiej dostępności](./concepts-high-availability.md)
- Informacje na temat [tworzenia kopii zapasowych i odzyskiwania](./concepts-backup-restore.md)