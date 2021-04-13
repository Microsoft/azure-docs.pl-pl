---
title: Ciągłość działania — Azure Database for MariaDB
description: Dowiedz się więcej o ciągłości działania (przywracanie do punktu w czasie, awaria centrum danych, przywracanie geograficzne) podczas korzystania z usługi Azure Database for MariaDB.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: conceptual
ms.date: 7/7/2020
ms.openlocfilehash: e4bdbd7dc9a751b6ea332ef760e5f016496c615c
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107306742"
---
# <a name="overview-of-business-continuity-with-azure-database-for-mariadb"></a>Przegląd ciągłości działania z Azure Database for MariaDB

W tym artykule opisano możliwości, które Azure Database for MySQL zapewnia ciągłość działania i odzyskiwanie po awarii. Informacje o opcjach odzyskiwania zdarzeń powodujących zakłócenia, które mogą spowodować utratę danych lub spowodować, że baza danych i aplikacja staną się niedostępne. Dowiedz się, co zrobić, gdy błąd użytkownika lub aplikacji wpływa na integralność danych, region platformy Azure ma awarię lub aplikacja wymaga konserwacji.

## <a name="features-that-you-can-use-to-provide-business-continuity"></a>Funkcje, których można użyć w celu zapewnienia ciągłości biznesowej

Podczas opracowywania planu ciągłości biznesowej należy zrozumieć maksymalny akceptowalny czas, po upływie którego aplikacja zostanie w pełni odzyskana po wystąpieniu zdarzenia zakłócenia — jest to cel czasu odzyskiwania (RTO). Należy również zrozumieć maksymalną ilość najnowszych aktualizacji danych (przedział czasu), jaką aplikacja może tolerować podczas odzyskiwania po wystąpieniu zdarzenia zakłócenia — jest to cel punktu odzyskiwania (RPO).

Azure Database for MariaDB zapewnia funkcje ciągłości działania i odzyskiwania po awarii, które obejmują geograficznie nadmiarowe kopie zapasowe z możliwością inicjowania przywracania geograficznego oraz wdrażania replik odczytu w innym regionie. Każdy z nich ma różne cechy czasu odzyskiwania i potencjalną utratę danych. Dzięki funkcji [przywracania geograficznego](concepts-backup.md) nowy serwer jest tworzony przy użyciu danych kopii zapasowej replikowanych z innego regionu. Całkowity czas przywracania i odzyskiwania zależy od rozmiaru bazy danych i ilości dzienników do odzyskania. Całkowity czas ustanowienia serwera różni się od kilku minut do kilku godzin. W przypadku [replik odczytu](concepts-read-replicas.md)dzienniki transakcji z podstawowego programu są przesyłane strumieniowo do repliki. W przypadku awarii podstawowej bazy danych z powodu błędu poziomu strefy lub poziomu regionu, przełączenie w tryb failover do repliki zapewnia krótszą RTO i zmniejsza utratę danych.

> [!NOTE]
> Opóźnienie między serwerem podstawowym a repliką zależy od opóźnienia między lokacjami, ilości danych do przesłania i najważniejszego obciążenia pracą zapisu na serwerze podstawowym. Duże obciążenia mogą generować duże opóźnienia. 
>
> Z powodu asynchronicznego charakteru replikacji używanej do odczytu replik **nie należy** traktować ich jako rozwiązania wysokiej dostępności, ponieważ wyższa spowolnieniaa może oznaczać wyższy RTO i cel punktu odzyskiwania. Tylko w przypadku obciążeń, w których zwłoka pozostanie mniejsza przez szczytowe i nieszczytowe czasy obciążenia, repliki odczytu mogą działać jako alternatywa HA. W przeciwnym razie repliki odczytu są przeznaczone do prawdziwej skali odczytu w przypadku dużych obciążeń i scenariuszy odzyskiwania po awarii.

W poniższej tabeli porównano RTO i cel punktu odzyskiwania w **typowym** scenariuszu obciążenia:

| **Funkcja** | **Podstawowa** | **Ogólnego przeznaczenia** | **Optymalizacja pod kątem pamięci** |
| :------------: | :-------: | :-----------------: | :------------------: |
| Przywracanie do punktu w czasie z kopii zapasowej | Dowolny punkt przywracania w okresie przechowywania <br/> RTO — różne <br/>Cel punktu odzyskiwania < 15 min| Dowolny punkt przywracania w okresie przechowywania <br/> RTO — różne <br/>Cel punktu odzyskiwania < 15 min | Dowolny punkt przywracania w okresie przechowywania <br/> RTO — różne <br/>Cel punktu odzyskiwania < 15 min |
| Przywracanie geograficzne z kopii zapasowych replikowanych geograficznie | Nieobsługiwane | RTO — różne <br/>Cel punktu odzyskiwania < 1 h | RTO — różne <br/>Cel punktu odzyskiwania < 1 h |
| Repliki do odczytu | RTO — minuty * <br/>Cel punktu odzyskiwania < 5 min * | RTO — minuty * <br/>Cel punktu odzyskiwania < 5 min *| RTO — minuty * <br/>Cel punktu odzyskiwania < 5 min *|

 \* RTO i cel punktu odzyskiwania **mogą być znacznie wyższe** w niektórych przypadkach, w zależności od różnych czynników, takich jak opóźnienie między lokacjami, ilość danych do przesłania i ważna podstawowa obciążenie bazy danych.

## <a name="recover-a-server-after-a-user-or-application-error"></a>Odzyskiwanie serwera po błędzie użytkownika lub aplikacji

Można użyć kopii zapasowych usługi do odzyskania serwera z różnych zdarzeń zakłócających działanie. Użytkownik może przypadkowo usunąć niektóre dane, przypadkowo porzucić ważną tabelę lub nawet porzucić całą bazę danych. Aplikacja może przypadkowo zastąpić dobre dane nieprawidłowymi danymi z powodu wady aplikacji i tak dalej.

Można wykonać przywracanie do punktu w czasie, aby utworzyć kopię serwera do znanego dobrego punktu w czasie. Ten punkt w czasie musi przypadać w okresie przechowywania kopii zapasowej skonfigurowanym dla serwera. Po przywróceniu danych na nowy serwer można zastąpić oryginalny serwer nowym przywróconym serwerem lub skopiować potrzebne dane z przywróconego serwera na oryginalny serwer.

> [!IMPORTANT]
> Usunięte serwery można przywrócić w ciągu **pięciu dni** po usunięciu kopii zapasowych. Kopia zapasowa bazy danych jest dostępna i przywracana tylko w ramach subskrypcji platformy Azure, w której znajduje się serwer. Aby przywrócić usunięty serwer, zapoznaj się z [opisanymi krokami](howto-restore-dropped-server.md). Aby chronić zasoby serwera, po wdrożeniu przed przypadkowym usunięciem lub nieoczekiwanymi zmianami, Administratorzy mogą korzystać z [blokad zarządzania](../azure-resource-manager/management/lock-resources.md).

## <a name="recover-from-an-azure-regional-data-center-outage"></a>Odzyskiwanie po awarii regionalnego centrum danych platformy Azure

Sporadycznie centrum danych platformy Azure może mieć awarię. Gdy wystąpi awaria, powoduje to zakłócenia działania firmy, które może trwać tylko kilka minut, ale może trwać w godzinach.

Jedną z opcji jest poczekanie, aż serwer powróci do trybu online po zakończeniu awarii centrum danych. Działa to w przypadku aplikacji, które mogą zapewnić, że serwer jest w trybie offline przez pewien czas, na przykład w środowisku programistycznym. Gdy centrum danych ma awarie, nie wiesz, jak długo może trwać awaria, więc ta opcja działa tylko wtedy, gdy serwer nie jest potrzebny przez pewien czas.

## <a name="geo-restore"></a>Przywracanie geograficzne

Funkcja przywracania geograficznego przywraca serwer przy użyciu geograficznie nadmiarowych kopii zapasowych. Kopie zapasowe są hostowane w [sparowanym regionie](../best-practices-availability-paired-regions.md)serwera. Te kopie zapasowe są dostępne nawet wtedy, gdy region, w którym znajduje się serwer, jest w trybie offline. Można przywrócić z tych kopii zapasowych do dowolnego innego regionu i przywrócić serwer do trybu online. Dowiedz się więcej o przywracaniu geograficznym z [artykułu dotyczącego tworzenia kopii zapasowych i przywracania](concepts-backup.md).

> [!IMPORTANT]
> Przywracanie geograficzne jest możliwe tylko w przypadku aprowizacji serwera z magazynem kopii zapasowych nadmiarowego. Aby przełączyć się z lokalnie nadmiarowego do geograficznie nadmiarowych kopii zapasowych na istniejącym serwerze, należy wykonać zrzut przy użyciu mysqldump istniejącego serwera i przywrócić go na nowo utworzony serwer skonfigurowany z użyciem geograficznie nadmiarowych kopii zapasowych.

## <a name="cross-region-read-replicas"></a>Repliki odczytu między regionami

Za pomocą replik odczytu między regionami można usprawnić planowanie ciągłości działania i odzyskiwania po awarii. Repliki odczytu są aktualizowane asynchronicznie przy użyciu technologii replikacji binarnych dzienników MySQL. Dowiedz się więcej na temat odczytywania replik, dostępnych regionów i sposobu przełączenia w tryb failover z [artykułu pojęć dotyczących replik](concepts-read-replicas.md). 

## <a name="faq"></a>Często zadawane pytania

### <a name="where-does-azure-database-for-mysql-store-customer-data"></a>Gdzie usługa Azure Database for MySQL przechowywać dane klienta?
Domyślnie Azure Database for MySQL nie przenosi ani nie zapisuje danych klienta poza regionem, w którym jest wdrożony. Jednak klienci mogą opcjonalnie wybrać, aby włączyć [geograficznie nadmiarowe kopie zapasowe](concepts-backup.md#backup-redundancy-options) lub utworzyć [replikę odczytu między regionami](concepts-read-replicas.md#cross-region-replication) do przechowywania danych w innym regionie.


## <a name="next-steps"></a>Następne kroki

- Więcej informacji na temat [zautomatyzowanych kopii zapasowych znajduje się w Azure Database for MariaDB](concepts-backup.md).
- Dowiedz się, jak przywrócić za pomocą [Azure Portal](howto-restore-server-portal.md) lub [interfejsu wiersza polecenia platformy Azure](howto-restore-server-cli.md).
- Dowiedz się więcej [na temat odczytywania replik w Azure Database for MariaDB](concepts-read-replicas.md).
