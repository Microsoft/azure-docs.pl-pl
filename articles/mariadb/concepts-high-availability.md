---
title: Wysoka dostępność — Azure Database for MariaDB
description: Ten artykuł zawiera informacje o wysokiej dostępności w Azure Database for MariaDB
author: kummanish
ms.author: manishku
ms.service: mariadb
ms.topic: conceptual
ms.date: 7/7/2020
ms.openlocfilehash: bea32b3b60c9013ea223513c95629092b9ab231b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86203312"
---
# <a name="high-availability-in-azure-database-for-mariadb"></a>Wysoka dostępność w Azure Database for MariaDB
Usługa Azure Database for MariaDB zapewnia gwarantowany wysoki poziom dostępności dzięki finansowej umowie dotyczącej poziomu usług (SLA) wynoszącej [99,99%](https://azure.microsoft.com/support/legal/sla/MariaDB) czasu. Azure Database for MariaDB zapewnia wysoką dostępność podczas planowanych zdarzeń, takich jak operacja obliczeniowa initated przez użytkownika, a także w przypadku nieplanowanych zdarzeń, takich jak podstawowy sprzęt, oprogramowanie lub awarie sieci. Azure Database for MariaDB możliwość szybkiego odzyskania sprawności od najbardziej krytycznych okoliczności, co gwarantuje praktycznie brak aplikacji podczas korzystania z tej usługi.

Azure Database for MariaDB jest odpowiednia do uruchamiania krytycznych baz danych, które wymagają dużego czasu przestoju. Oparta na architekturze platformy Azure, usługa ma nieodłączną dostępność, nadmiarowość i możliwości odporności, aby wyeliminować przestoje bazy danych z planowanych i nieplanowanych awarii, bez konieczności konfigurowania dodatkowych składników. 

## <a name="components-in-azure-database-for-mariadb"></a>Składniki w Azure Database for MariaDB

| **Składnik** | **Opis**|
| ------------ | ----------- |
| <b>Serwer bazy danych MariaDB | Azure Database for MariaDB zapewnia zabezpieczenia, izolację, zabezpieczenia zasobów i funkcję szybkiego ponownego uruchamiania dla serwerów baz danych. Te funkcje ułatwiają wykonywanie operacji, takich jak skalowanie i odzyskiwanie serwera bazy danych, po awarii w kilka sekund. <br/> Modyfikacje danych na serwerze bazy danych zwykle występują w kontekście transakcji bazy danych. Wszystkie zmiany bazy danych są rejestrowane synchronicznie w postaci zapisu z wyprzedzeniem (ib_log) w usłudze Azure Storage, która jest dołączona do serwera bazy danych. Podczas procesu tworzenia [punktu kontrolnego](https://mariadb.com/kb/innodb-redo-log/#checkpoints) bazy danych strony danych z pamięci serwera bazy danych są również opróżniane do magazynu. |
| <b>Magazyn zdalny | Wszystkie MariaDB fizyczne pliki danych i pliki dziennika są przechowywane w usłudze Azure Storage, która jest poddana architekturze w celu przechowywania trzech kopii danych w obrębie regionu w celu zapewnienia nadmiarowości, dostępności i niezawodności danych. Warstwa magazynowania jest również niezależna od serwera bazy danych. Można go odłączyć od nieuszkodzonego serwera bazy danych i ponownie dołączyć do nowego serwera bazy danych w ciągu kilku sekund. Ponadto usługa Azure Storage ciągle monitoruje się pod kątem błędów magazynu. W przypadku wykrycia uszkodzenia bloku zostanie ono automatycznie naprawione przez utworzenie wystąpienia nowej kopii magazynu. |
| <b>Punkt | Brama działa jako serwer proxy bazy danych, przekierowuje wszystkie połączenia klientów do serwera bazy danych. |

## <a name="planned-downtime-mitigation"></a>Planowane ograniczenie przestoju
Azure Database for MariaDB jest zaprojektowany w celu zapewnienia wysokiej dostępności podczas planowanych operacji przestojów. 

![Widok elastycznego skalowania w usłudze Azure MariaDB](./media/concepts-high-availability/elastic-scaling-mariadb-server.png)

Poniżej przedstawiono niektóre planowane scenariusze konserwacji:

| **Scenariusz** | **Opis**|
| ------------ | ----------- |
| <b>Skalowanie w górę/w dół | Gdy użytkownik wykonuje operację skalowania w górę/w dół, nowy serwer bazy danych jest inicjowany przy użyciu skalowanej konfiguracji obliczeniowej. W starym serwerze bazy danych można zakończyć aktywne punkty kontrolne, połączenia klientów są opróżniane, wszystkie niezatwierdzone transakcje są anulowane, a następnie wyłączone. Magazyn zostanie odłączony od starego serwera bazy danych i dołączony do nowego serwera bazy danych. Gdy aplikacja kliencka ponawia próbę połączenia lub próbuje utworzyć nowe połączenie, Brama kieruje żądanie połączenia do nowego serwera bazy danych.|
| <b>Skalowanie w górę magazynu | Skalowanie w górę magazynu jest operacją online i nie przerywa serwera bazy danych.|
| <b>Nowe wdrożenie oprogramowania (Azure) | Nowe funkcje wdrażania lub rozwiązywania błędów są automatycznie wykonywane w ramach planowanej konserwacji usługi. Aby uzyskać więcej informacji, zapoznaj się z [dokumentacją](concepts-monitoring.md#planned-maintenance-notification), a także sprawdź [Portal](https://aka.ms/servicehealthpm).|
| <b>Uaktualnienia wersji pomocniczej | Azure Database for MariaDB automatycznie poprawek serwerów baz danych do wersji pomocniczej ustalonej przez platformę Azure. Odbywa się to w ramach planowanej konserwacji usługi. Może to spowodować skrócenie przestoju w ciągu kilku sekund, a serwer bazy danych zostanie automatycznie uruchomiony ponownie z nową wersją pomocniczą. Aby uzyskać więcej informacji, zapoznaj się z [dokumentacją](concepts-monitoring.md#planned-maintenance-notification), a także sprawdź [Portal](https://aka.ms/servicehealthpm).|


##  <a name="unplanned-downtime-mitigation"></a>Nieplanowane ograniczenie przestoju

Nieplanowany przestój może wystąpić w wyniku nieprzewidzianych awarii, w tym podstawowego błędu sprzętowego, problemów z siecią i błędów oprogramowania. Jeśli serwer bazy danych ulegnie awarii, w ciągu kilku sekund zostanie automatycznie zainicjowany nowy serwer bazy danych. Magazyn zdalny jest automatycznie dołączany do nowego serwera bazy danych. Aparat MariaDB wykonuje operację odzyskiwania przy użyciu plików WAL i Database, a następnie otwiera serwer baz danych, aby umożliwić klientom nawiązywanie połączeń. Niezatwierdzone transakcje są tracone i muszą być ponawiane przez aplikację. Nie można uniknąć nieplanowanych przestojów, Azure Database for MariaDB ogranicza przestoje przez automatyczne wykonywanie operacji odzyskiwania zarówno na serwerze bazy danych, jak i w warstwach magazynu, bez konieczności interwencji człowieka. 


![Widok wysokiej dostępności w usłudze Azure MariaDB](./media/concepts-high-availability/availability-mariadb-server.png)

### <a name="unplanned-downtime-failure-scenarios-and-service-recovery"></a>Nieplanowany przestój: scenariusze awarii i odzyskiwanie usługi
Poniżej przedstawiono niektóre scenariusze awarii i sposób automatycznego odzyskiwania Azure Database for MariaDB:

| **Scenariusz** | **Automatyczne odzyskiwanie** |
| ---------- | ---------- |
| <B>Awaria serwera bazy danych | Jeśli serwer bazy danych nie działa z powodu błędu sprzętowego, aktywne połączenia są porzucane i wszystkie transakcje numerów porządkowych określających są przerywane. Zostanie automatycznie wdrożony nowy serwer bazy danych, a zdalny magazyn danych jest podłączony do nowego serwera bazy danych. Po zakończeniu odzyskiwania bazy danych klienci mogą połączyć się z nowym serwerem bazy danych za pomocą bramy. <br /> <br /> Aplikacje korzystające z baz danych MariaDB muszą być wbudowane w taki sposób, aby wykrywać i ponawiać próby porzucenia połączeń i transakcji zakończonych niepowodzeniem.  Po ponownym ponowieniu próby aplikacji Brama w niewidoczny sposób przekierowuje połączenie do nowo utworzonego serwera bazy danych. |
| <B>Awaria magazynu | Aplikacje nie widzą żadnego wpływu na problemy związane z magazynowaniem, takie jak awaria dysku lub uszkodzenie bloku fizycznego. Ponieważ dane są przechowywane w 3 kopiach, kopia danych jest obsługiwana przez trwające magazynowanie. Uszkodzenia bloków są automatycznie poprawiane. Jeśli kopia danych zostanie utracona, tworzona jest nowa kopia danych. |

Poniżej przedstawiono niektóre scenariusze niepowodzeń, które wymagają wykonania czynności przez użytkownika w celu odzyskania:

| **Scenariusz** | **Plan odzyskiwania** |
| ---------- | ---------- |
| <b> Awaria regionu | Awaria regionu jest zdarzeniem rzadkim. Jeśli jednak potrzebujesz ochrony przed awarią regionu, możesz skonfigurować co najmniej jedną replikę odczytu w innych regionach na potrzeby odzyskiwania po awarii (DR). (Zobacz [ten artykuł](howto-read-replicas-portal.md) dotyczący tworzenia replik odczytu i zarządzania nimi w celu uzyskania szczegółowych informacji). W przypadku awarii poziomu regionu można ręcznie podwyższyć poziom repliki odczytu skonfigurowany w innym regionie jako produkcyjny serwer bazy danych. |
| <b> Błędy logiczne/użytkownika | Odzyskiwanie z błędów użytkowników, takich jak przypadkowo porzucane tabele lub nieprawidłowo aktualizowane dane, polega na przeprowadzeniu [odzyskiwania do punktu w czasie](concepts-backup.md) (kopie) przez przywrócenie i odzyskanie danych do czasu, gdy wystąpi błąd.<br> <br>  Aby przywrócić tylko podzestaw baz danych lub określonych tabel, a nie wszystkich baz danych na serwerze bazy danych, można przywrócić serwer bazy danych w nowym wystąpieniu, wyeksportować tabele za pośrednictwem [mysqldump](howto-migrate-dump-restore.md), a następnie użyć [przywracania](howto-migrate-dump-restore.md#restore-your-mariadb-database) , aby przywrócić te tabele do bazy danych programu. |



## <a name="summary"></a>Podsumowanie

Azure Database for MariaDB zapewnia możliwość szybkiego ponownego uruchomienia serwerów baz danych, magazynu nadmiarowego i wydajnego routingu z bramy. W celu zapewnienia dodatkowej ochrony danych można skonfigurować tworzenie kopii zapasowych jako replikowanych geograficznie, a także wdrożyć co najmniej jedną replikę odczytu w innych regionach. Z nieodłącznymi możliwościami wysokiej dostępności Azure Database for MariaDB chronią bazy danych od najbardziej typowego przestoju i oferują wiodącą w branży, finanse [99,99% umowy SLA dotyczącej czasu pracy](https://azure.microsoft.com/support/legal/sla/MariaDB). Wszystkie te funkcje dostępności i niezawodności umożliwiają platformę Azure to idealna platforma do uruchamiania aplikacji o znaczeniu strategicznym.

## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o [regionach platformy Azure](../availability-zones/az-overview.md)
- Dowiedz się więcej na temat [obsługi błędów łączności przejściowej](concepts-connectivity.md)
- Dowiedz się, jak [replikować dane za pomocą replik odczytu](howto-read-replicas-portal.md)