---
title: Przegląd ciągłości działania z serwerem elastycznym Azure Database for PostgreSQL
description: Poznaj koncepcje ciągłości działania z serwerem elastycznym Azure Database for PostgreSQL
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: 341d3c6f0a2d84c2b7b725959db9f7e5dd80d3ff
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92542968"
---
# <a name="overview-of-business-continuity-with-azure-database-for-postgresql---flexible-server"></a>Przegląd ciągłości działania z serwerem elastycznym Azure Database for PostgreSQL

> [!IMPORTANT]
> Azure Database for PostgreSQL — serwer elastyczny jest w wersji zapoznawczej

**Ciągłość** działania w Azure Database for PostgreSQL-elastycznym serwerze odnosi się do mechanizmów, zasad i procedur, które umożliwiają firmie kontynuowanie pracy w obliczu zakłóceń, szczególnie w przypadku infrastruktury obliczeniowej. W większości przypadków elastyczny serwer będzie obsługiwał zdarzenia zakłócające, które mogą wystąpić w środowisku chmury i zachować działania aplikacji i procesów roboczych. Istnieją jednak pewne zdarzenia, których nie można automatycznie obsłużyć, takich jak:

- Użytkownik przypadkowo usunął lub zaktualizuje wiersz w tabeli.
- Trzęsienia ziemi powoduje awarię i tymczasowe wyłączenie centrum danych lub strefy dostępności.
- Poprawka bazy danych wymagana do usunięcia usterki lub problemu z zabezpieczeniami.

Elastyczny serwer udostępnia funkcje chroniące dane i ograniczające czas przestoju dla najważniejszych baz danych w przypadku planowanych i nieplanowanych przestojów. W oparciu o infrastrukturę platformy Azure, która oferuje już niezawodne odporności i dostępność, elastyczny serwer ma funkcje zapewniania ciągłości biznesowej, które zapewniają dodatkową ochronę przed awariami, wymagania dotyczące czasu odzyskiwania i zmniejszają ryzyko utraty danych. Podczas tworzenia architektury aplikacji należy wziąć pod uwagę tolerancję przestoju, czyli cel czasu odzyskiwania (RTO) i ryzyko utraty danych, czyli cel punktu odzyskiwania (RPO). Na przykład baza danych o krytycznym znaczeniu dla firmy wymaga znacznie bardziej rygorystycznych wymagań dotyczących czasu pracy w porównaniu z testową bazą danych.  

> [!IMPORTANT]
> W trakcie okresu zapoznawczego nie jest oferowana umowa dotycząca poziomu usług (SLA)%. 

W poniższej tabeli przedstawiono funkcje, które oferuje elastyczny serwer.


| **Funkcja** | **Opis** | **Zagadnienia do rozważenia** |
| ---------- | ----------- | ------------ |
| **Automatyczne kopie zapasowe** | Elastyczny serwer automatycznie wykonuje codzienne kopie zapasowe plików bazy danych i ciągle tworzy kopie zapasowe dzienników transakcji. Kopie zapasowe mogą być przechowywane z 7 dni do 35 dni. Serwer bazy danych będzie można przywrócić do dowolnego punktu w czasie w ramach okresu przechowywania kopii zapasowej. RTO zależy od rozmiaru danych do przywrócenia i czasu na odzyskanie dziennika. Może to potrwać od kilku minut do 12 godzin. Aby uzyskać więcej informacji, zobacz temat [koncepcje — tworzenie kopii zapasowych i przywracanie](./concepts-backup-restore.md). |Dane kopii zapasowej pozostają w regionie. |
| **Strefa nadmiarowa wysokiej dostępności** | Elastyczny serwer można wdrożyć za pomocą konfiguracji nadmiarowej wysokiej dostępności (HA) strefy, w której serwery podstawowe i zapasowe są wdrażane w dwóch różnych strefach dostępności w obrębie regionu. Ta konfiguracja HA chroni bazy danych przed awariami na poziomie strefy, a także ułatwia skrócenie przestojów aplikacji podczas planowanych i nieplanowanych przestojów. Dane z serwera podstawowego są replikowane do repliki gotowości w trybie synchronicznym. W przypadku awarii serwera podstawowego serwer jest automatycznie przełączona w tryb failover do repliki w stanie wstrzymania. RTO w większości przypadków powinien znajdować się w zakresie 60s-120s. Oczekiwany cel punktu odzyskiwania to zero (brak utraty danych). Aby uzyskać więcej informacji, zobacz [pojęcia — wysoka dostępność](./concepts-high-availability.md). | Obsługiwane w warstwach obliczeniowych ogólnego przeznaczenia i zoptymalizowanych pod kątem pamięci. Dostępne tylko w regionach, w których jest dostępnych wiele stref. |
| **Dyski zarządzane w warstwie Premium** | Pliki bazy danych są przechowywane w wysoce trwałym i niezawodnym magazynie zarządzanym w warstwie Premium. Zapewnia to nadmiarowość danych z trzema kopiami replik przechowywanych w strefie dostępności z automatycznym odzyskiwaniem danych. Aby uzyskać więcej informacji, zobacz [Dokumentacja usługi Managed disks](../../virtual-machines/managed-disks-overview.md). | Dane przechowywane w strefie dostępności. |
| **Strefowo nadmiarowa kopia zapasowa** | Elastyczne kopie zapasowe serwera są automatycznie i bezpiecznie przechowywane w strefie nadmiarowego magazynu w regionie. W trakcie awarii na poziomie strefy, w której jest inicjowany serwer, a jeśli serwer nie jest skonfigurowany z nadmiarowością strefy, można nadal przywrócić bazę danych przy użyciu najnowszego punktu przywracania w innej strefie. Aby uzyskać więcej informacji, zobacz temat [koncepcje — tworzenie kopii zapasowych i przywracanie](./concepts-backup-restore.md).| Dotyczy tylko w regionach, w których jest dostępnych wiele stref.|


## <a name="planned-downtime-events"></a>Zdarzenia planowanego przestoju
Poniżej przedstawiono niektóre zaplanowane scenariusze konserwacji. Te zdarzenia zwykle ponoszą do kilku minut przestoju i bez utraty danych.

| **Scenariusz** | **Proces**|
| ------------------- | ----------- | 
| <b>Skalowanie obliczeniowe (zainicjowane przez użytkownika)| Podczas operacji skalowania obliczeń można wykonać aktywne punkty kontrolne, połączenia klientów są opróżniane, wszystkie niezatwierdzone transakcje są anulowane, magazyn zostanie odłączony, a następnie wyłączony. Nowy, elastyczny serwer o tej samej nazwie serwera bazy danych jest inicjowany z użyciem skalowanej konfiguracji obliczeniowej. Magazyn jest następnie dołączany do nowego serwera, a baza danych zostanie uruchomiona, w razie potrzeby, w razie konieczności, przed zaakceptowaniem połączeń z klientami. |
| <b>Skalowanie magazynu (zainicjowane przez użytkownika) | Po zainicjowaniu operacji skalowania w górę można wykonać aktywne punkty kontrolne, połączenia klientów są opróżniane, wszystkie niezatwierdzone transakcje są anulowane, a następnie wyłączone. Magazyn jest skalowany do żądanego rozmiaru, a następnie dołączany do nowego serwera. Odzyskiwanie jest wykonywane w razie konieczności przed zaakceptowaniem połączeń z klientami. Należy zauważyć, że skalowanie w dół rozmiaru magazynu nie jest obsługiwane. |
| <b>Nowe wdrożenie oprogramowania (zainicjowane przez platformę Azure) | Nowe funkcje wdrażania lub rozwiązywania błędów są automatycznie wykonywane w ramach planowanej konserwacji usługi i można zaplanować, kiedy te działania mają być wykonywane. Aby uzyskać więcej informacji, sprawdź [Portal](https://aka.ms/servicehealthpm). | 
| <b>Uaktualnienia wersji pomocniczej (zainicjowane przez platformę Azure) | Azure Database for PostgreSQL automatycznie poprawek serwerów baz danych do wersji pomocniczej ustalonej przez platformę Azure. Odbywa się to w ramach planowanej konserwacji usługi. Serwer bazy danych jest automatycznie uruchamiany ponownie przy użyciu nowej wersji pomocniczej. Aby uzyskać więcej informacji, zobacz [dokumentację](../concepts-monitoring.md#planned-maintenance-notification). Możesz również sprawdzić [Portal](https://aka.ms/servicehealthpm).| 

 Gdy elastyczny serwer jest skonfigurowany z **nadmiarową wysoką dostępnością strefy**, elastyczny serwer przeprowadzi najpierw skalowanie i operacje konserwacji na serwerze rezerwy. Aby uzyskać więcej informacji, zobacz [pojęcia — wysoka dostępność](./concepts-high-availability.md).

##  <a name="unplanned-downtime-mitigation"></a>Nieplanowane ograniczenie przestoju

Nieplanowane przestoje mogą wystąpić w wyniku nieprzewidzianych zakłóceń, takich jak awaria sprzętu, problemy z siecią i błędy oprogramowania. Jeśli serwer bazy danych skonfigurowany pod kątem wysokiej dostępności zostanie nieoczekiwanie wyłączony, replika rezerwy zostanie aktywowana, a klienci mogą wznowić wykonywanie operacji. Jeśli nie skonfigurowano wysokiej dostępności (HA), jeśli próba ponownego uruchomienia nie powiedzie się, zostanie automatycznie zainicjowany nowy serwer bazy danych. Chociaż nie można uniknąć nieplanowanych przestojów, elastyczny serwer pomaga ograniczyć przestoje przez automatyczne wykonywanie operacji odzyskiwania bez konieczności interwencji człowieka. 
   
### <a name="unplanned-downtime-failure-scenarios-and-service-recovery"></a>Nieplanowany przestój: scenariusze awarii i odzyskiwanie usługi
Poniżej przedstawiono niektóre nieplanowane scenariusze błędów i proces odzyskiwania. 

| **Scenariusz** | **Proces odzyskiwania [nie HA]** | **Proces odzyskiwania [HA]** |
| ---------- | ---------- | ------- |
| <B>Awaria serwera bazy danych | Jeśli serwer bazy danych nie działa, platforma Azure podejmie próbę ponownego uruchomienia serwera bazy danych. Jeśli to się nie powiedzie, serwer bazy danych zostanie uruchomiony ponownie w innym węźle fizycznym.  <br /> <br /> Czas odzyskiwania (RTO) zależy od różnych czynników, w tym działania w momencie wystąpienia błędu, takie jak duże transakcje i wolumin odzyskiwania, które mają być wykonywane podczas uruchamiania serwera bazy danych. <br /> <br /> Aplikacje korzystające z baz danych PostgreSQL muszą być wbudowane w taki sposób, aby wykrywać i ponawiać próby porzucenia połączeń i transakcji zakończonych niepowodzeniem. | Awaria serwera bazy danych powoduje przełączenie serwera w tryb failover na serwer rezerwy, co zmniejsza przestoje. Aby uzyskać więcej informacji, zobacz [stronę pojęcia o wysokiej dostępności](./concepts-high-availability.md). RTO powinien mieć wartość 60-120s, bez utraty danych. |
| <B>Awaria magazynu | Aplikacje nie widzą żadnego wpływu na problemy związane z magazynowaniem, takie jak awaria dysku lub uszkodzenie bloku fizycznego. Ponieważ dane są przechowywane w trzech kopiach, kopia danych jest obsługiwana przez trwające magazynowanie. Uszkodzony blok danych zostanie automatycznie naprawiony, a nowa kopia danych zostanie utworzona automatycznie. | W przypadku wszelkich rzadkich i nieodwracalnych błędów, takich jak cały magazyn jest niedostępny, elastyczny serwer jest przełączona w tryb failover do repliki gotowości, aby zmniejszyć przestoje. Aby uzyskać więcej informacji, zobacz [stronę pojęcia o wysokiej dostępności](./concepts-high-availability.md). |
| <b> Błędy logiczne/użytkownika | W celu odzyskania błędów użytkowników, takich jak przypadkowe porzucanie tabel lub niewłaściwie zaktualizowanych danych, należy wykonać [odzyskiwanie do punktu w czasie](../concepts-backup.md) (kopie). Podczas wykonywania operacji przywracania należy określić niestandardowy punkt przywracania, który jest odpowiednim czasem przed wystąpieniem błędu.<br> <br>  Aby przywrócić tylko podzestaw baz danych lub określonych tabel, a nie wszystkich baz danych na serwerze bazy danych, można przywrócić serwer bazy danych w nowym wystąpieniu, wyeksportować tabele za pośrednictwem [pg_dump](https://www.postgresql.org/docs/11/app-pgdump.html), a następnie użyć [pg_restore](https://www.postgresql.org/docs/11/app-pgrestore.html) do przywrócenia tych tabel do bazy danych programu. | Te błędy użytkownika nie są chronione o wysokiej dostępności, ponieważ wszystkie zmiany są replikowane synchronicznie do repliki w stanie wstrzymania. Należy wykonać przywracanie do punktu w czasie, aby odzyskać te błędy. |
| <b> Awaria strefy dostępności | Aby odzyskać sprawność po awarii poziomu strefy, można wykonać przywracanie do punktu w czasie za pomocą kopii zapasowej i wybrać niestandardowy punkt przywracania z najnowszym terminem przywracania najnowszych danych. Nowy, elastyczny serwer zostanie wdrożony w innej strefie nieobjętej wpływem. Czas trwania przywracania zależy od poprzedniej kopii zapasowej i ilości dzienników transakcji do odzyskania. | Elastyczny serwer jest automatycznie przełączona w tryb failover na serwer rezerwy w ciągu 60-120s z zerową utratą danych. Aby uzyskać więcej informacji, zobacz [stronę pojęcia o wysokiej dostępności](./concepts-high-availability.md). | 
| <b> Awaria regionu | Replika odczytu między regionami i Funkcja przywracania geograficznego funkcji kopii zapasowych nie są jeszcze obsługiwane w wersji zapoznawczej. | |


> [!IMPORTANT]
> **Nie** można przywrócić usuniętych serwerów. Usunięcie serwera spowoduje również usunięcie wszystkich baz danych należących do serwera, których nie można odzyskać. Użyj [blokady zasobów platformy Azure](../../azure-resource-manager/management/lock-resources.md) , aby zapobiec przypadkowemu usunięciu serwera.


## <a name="next-steps"></a>Następne kroki

-   Więcej informacji na temat [strefy nadmiarowej wysokiej dostępności](./concepts-high-availability.md)
-   Informacje na temat [tworzenia kopii zapasowych i odzyskiwania](./concepts-backup-restore.md)