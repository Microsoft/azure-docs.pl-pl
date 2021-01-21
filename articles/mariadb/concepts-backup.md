---
title: Tworzenie kopii zapasowych i przywracanie Azure Database for MariaDB
description: Dowiedz się więcej o automatycznych kopiach zapasowych i przywracaniu serwera Azure Database for MariaDB.
author: savjani
ms.author: pariks
ms.service: jroth
ms.topic: conceptual
ms.date: 8/13/2020
ms.openlocfilehash: 1d5fc158d2e37223e048c4106206ddba4602fabb
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/21/2021
ms.locfileid: "98659941"
---
# <a name="backup-and-restore-in-azure-database-for-mariadb"></a>Tworzenie kopii zapasowych i przywracanie w Azure Database for MariaDB

Azure Database for MariaDB automatycznie tworzy kopie zapasowe serwera i przechowuje je w ramach użytkownika skonfigurowanego lokalnie nadmiarowy lub geograficznie nadmiarowy. Kopie zapasowe mogą być używane do przywracania serwera do punktu w czasie. Tworzenie kopii zapasowych i przywracanie jest istotną częścią strategii ciągłości biznesowej, ponieważ chronią dane przed przypadkowym uszkodzeniem lub usunięciem.

## <a name="backups"></a>Tworzenie kopii zapasowych

Azure Database for MariaDB pobiera pełne, różnicowe i transakcyjne kopie zapasowe dziennika. Te kopie zapasowe umożliwiają przywrócenie serwera do dowolnego punktu w czasie w ramach skonfigurowanego okresu przechowywania kopii zapasowych. Domyślny okres przechowywania kopii zapasowych wynosi siedem dni. Opcjonalnie można skonfigurować ją do 35 dni. Wszystkie kopie zapasowe są szyfrowane za pomocą 256-bitowego szyfrowania AES.

Te pliki kopii zapasowej nie są uwidaczniane przez użytkownika i nie można ich eksportować. Te kopie zapasowe mogą być używane tylko w przypadku operacji przywracania w Azure Database for MariaDB. Możesz użyć [mysqldump](howto-migrate-dump-restore.md) , aby skopiować bazę danych.

### <a name="backup-frequency"></a>Częstotliwość wykonywania kopii zapasowych

#### <a name="servers-with-up-to-4-tb-storage"></a>Serwery z do 4 TB magazynu

W przypadku serwerów, które obsługują maksymalnie 4 TB magazynu, pełne kopie zapasowe są wykonywane co tydzień. Różnicowe kopie zapasowe są wykonywane dwa razy dziennie. Kopie zapasowe dziennika transakcji są wykonywane co pięć minut.

#### <a name="servers-with-up-to-16-tb-storage"></a>Serwery z maksymalnie 16 TBm pamięci masowej
W podzestawie [regionów świadczenia usługi Azure](concepts-pricing-tiers.md#storage)wszystkie nowo Obsługiwane serwery mogą obsługiwać do 16 TB pamięci masowej. Kopie zapasowe na tych dużych serwerach magazynu są oparte na migawce. Pierwsza pełna kopia zapasowa migawki jest planowana natychmiast po utworzeniu serwera. Kopia zapasowa pierwszej pełnej migawki jest zachowywana jako podstawowa kopia zapasowa serwera. Kolejne kopie zapasowe migawki są jedynie różnicowymi kopiami zapasowymi. 

Różnicowe kopie zapasowe migawek są tworzone co najmniej raz dziennie. Różnicowe kopie zapasowe migawek nie są tworzone zgodnie z ustalonym harmonogramem. Kopie zapasowe migawek różnicowych są wykonywane co 24 godziny, chyba że dziennik transakcji (binlog w MariaDB) przekracza 50 GB od czasu ostatniej różnicowej kopii zapasowej. W ciągu dnia dozwolonych jest maksymalnie sześć migawek różnicowych. 

Kopie zapasowe dziennika transakcji są wykonywane co pięć minut. 

### <a name="backup-retention"></a>Przechowywanie kopii zapasowej

Kopie zapasowe są zachowywane na podstawie ustawienia okresu przechowywania kopii zapasowej na serwerze. Można wybrać okres przechowywania wynoszący od 7 do 35 dni. Domyślny okres przechowywania wynosi 7 dni. Okres przechowywania można ustawić podczas tworzenia serwera lub później, aktualizując konfigurację kopii zapasowej za pomocą [Azure Portal](howto-restore-server-portal.md#set-backup-configuration) lub [interfejsu wiersza polecenia platformy Azure](howto-restore-server-cli.md#set-backup-configuration). 

Okres przechowywania kopii zapasowej decyduje o tym, jak daleko w czasie można pobrać przywracanie do punktu w czasie, ponieważ jest ono oparte na dostępnych kopiach zapasowych. Okres przechowywania kopii zapasowej może być również traktowany jako okno odzyskiwania z perspektywy przywracania. Wszystkie kopie zapasowe wymagane do przeprowadzenia przywracania do punktu w czasie w ramach okresu przechowywania kopii zapasowych są zachowywane w magazynie kopii zapasowych. Na przykład jeśli okres przechowywania kopii zapasowej jest ustawiony na 7 dni, okno odzyskiwania jest uznawane za ostatnie 7 dni. W tym scenariuszu zachowywane są wszystkie kopie zapasowe wymagane do przywrócenia serwera w ciągu ostatnich 7 dni. Z oknem przechowywania kopii zapasowej siedmiu dni:
- Serwery z magazynem o pojemności do 4 TB przechowują do 2 kopii zapasowych pełnych baz danych, wszystkie różnicowe kopie zapasowe i kopie zapasowe dziennika transakcji wykonane od najwcześniejszej pełnej kopii zapasowej bazy danych.
-   Serwery z magazynem do 16 TB będą zachować pełną migawkę bazy danych, wszystkie migawki różnicowe i kopie zapasowe dziennika transakcji w ciągu ostatnich 8 dni.

#### <a name="long-term-retention-of-backups"></a>Długoterminowe przechowywanie kopii zapasowych
Długoterminowe przechowywanie kopii zapasowych poza 35 dni nie jest obecnie obsługiwane w sposób natywny przez usługę. Możesz użyć mysqldump, aby tworzyć kopie zapasowe i przechowywać je do długoterminowego przechowywania. Nasz zespół pomocy technicznej Blogged [krok po kroku,](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/automate-backups-of-your-azure-database-for-mysql-server-to/ba-p/1791157) aby udostępnić, jak można to osiągnąć. 

### <a name="backup-redundancy-options"></a>Opcje nadmiarowości kopii zapasowej

Azure Database for MariaDB zapewnia elastyczność wyboru między lokalnie nadmiarowym lub geograficznie nadmiarowym magazynem kopii zapasowych w warstwach Ogólnego przeznaczenia i zoptymalizowanych pod kątem pamięci. Gdy kopie zapasowe są przechowywane w magazynie geograficznie nadmiarowym, nie są przechowywane tylko w regionie, w którym znajduje się serwer, ale są również replikowane do [sparowanego centrum danych](../best-practices-availability-paired-regions.md). Zapewnia to lepszą ochronę i możliwość przywracania serwera w innym regionie w przypadku awarii. Warstwa Podstawowa oferuje tylko lokalnie nadmiarowy magazyn kopii zapasowych.

#### <a name="moving-from-locally-redundant-to-geo-redundant-backup-storage"></a>Przechodzenie z lokalnie nadmiarowego do magazynu kopii zapasowej nadmiarowej geograficznie
Konfigurowanie lokalnie nadmiarowego lub geograficznie nadmiarowego magazynu dla kopii zapasowych jest dozwolone tylko podczas tworzenia serwera. Po aprowizacji serwera nie można zmienić opcji nadmiarowości magazynu kopii zapasowych. W celu przeniesienia magazynu kopii zapasowych z magazynu lokalnie nadmiarowego do magazynu geograficznie nadmiarowego, utworzenie nowego serwera i Migrowanie danych przy użyciu [zrzutów i przywracania](howto-migrate-dump-restore.md) jest jedyną obsługiwaną opcją.

### <a name="backup-storage-cost"></a>Koszt magazynu kopii zapasowych

Azure Database for MariaDB zapewnia do 100% magazynu z zainicjowaną obsługą kopii zapasowych bez dodatkowych kosztów. Każdy dodatkowy magazyn kopii zapasowych jest naliczany w GB miesięcznie. Na przykład jeśli Zainicjowano obsługę serwera z 250 GB miejsca w magazynie, masz 250 GB dodatkowego magazynu dla kopii zapasowych serwera bez dodatkowych opłat. Magazyn używany do tworzenia kopii zapasowych przekracza 250 GB jest naliczany zgodnie z [modelem cen](https://azure.microsoft.com/pricing/details/mariadb/). 

Można użyć metryki [używany magazyn kopii zapasowych](concepts-monitoring.md) w Azure monitor dostępnej za pośrednictwem Azure Portal do monitorowania magazynu kopii zapasowych zużywanego przez serwer. Metryka używany magazyn kopii zapasowych reprezentuje sumę magazynu zużywanego przez wszystkie pełne kopie zapasowe bazy danych, różnicowe kopie zapasowe i kopie zapasowe dzienników przechowywane na podstawie okresu przechowywania kopii zapasowej ustawionego dla serwera. Częstotliwość wykonywania kopii zapasowych to zarządzane i wyjaśnione wcześniej usługi. Duża liczba transakcji na serwerze może powodować zwiększenie użycia magazynu kopii zapasowych niezależnie od całkowitego rozmiaru bazy danych. W przypadku magazynu geograficznie nadmiarowego użycie magazynu kopii zapasowych jest dwa razy większe niż magazyn lokalnie nadmiarowy. 

Podstawowym sposobem kontrolowania kosztu magazynowania kopii zapasowych jest ustawienie odpowiedniego okresu przechowywania kopii zapasowych i wybranie odpowiednich opcji nadmiarowości kopii zapasowych w celu spełnienia potrzebnych celów odzyskiwania. Możesz wybrać okres przechowywania z zakresu od 7 do 35 dni. Serwery Ogólnego przeznaczenia i zoptymalizowane pod kątem pamięci mogą wybrać magazyn Geograficznie nadmiarowy dla kopii zapasowych.

## <a name="restore"></a>Przywracanie

W Azure Database for MariaDB wykonanie przywracania powoduje utworzenie nowego serwera z kopii zapasowych oryginalnego serwera i przywrócenie wszystkich baz danych znajdujących się na serwerze.

Dostępne są dwa typy przywracania:

- **Przywracanie do punktu w czasie** jest dostępne z opcją nadmiarowości kopii zapasowych i tworzy nowy serwer w tym samym regionie, w którym znajduje się oryginalny serwer korzystający z kombinacji kopii zapasowych dziennika pełnego i transakcji.
- **Przywracanie geograficzne** jest dostępne tylko wtedy, gdy skonfigurowano serwer dla magazynu geograficznie nadmiarowego i umożliwia przywrócenie serwera do innego regionu przy użyciu najnowszej kopii zapasowej.

Szacowany czas odzyskiwania zależy od kilku czynników, takich jak rozmiary bazy danych, rozmiar dziennika transakcji, przepustowość sieci i łączna liczba baz danych, które są odzyskiwane w tym samym regionie w tym samym czasie. Czas odzyskiwania jest zwykle krótszy niż 12 godzin.

> [!IMPORTANT]
> **Nie** można przywrócić usuniętych serwerów. Usunięcie serwera spowoduje również usunięcie wszystkich baz danych należących do serwera, których nie można odzyskać. Aby chronić zasoby serwera, po wdrożeniu przed przypadkowym usunięciem lub nieoczekiwanymi zmianami, Administratorzy mogą korzystać z [blokad zarządzania](../azure-resource-manager/management/lock-resources.md).

### <a name="point-in-time-restore"></a>Przywracanie do określonego momentu

Niezależnie od opcji nadmiarowości kopii zapasowej można wykonać przywracanie do dowolnego punktu w czasie w ramach okresu przechowywania kopii zapasowej. Nowy serwer jest tworzony w tym samym regionie świadczenia usługi Azure, w którym znajduje się oryginalny serwer. Jest on tworzony z konfiguracją pierwotnego serwera dla warstwy cenowej, generowania obliczeń, liczby rdzeni wirtualnych, rozmiaru magazynu, okresu przechowywania kopii zapasowych i opcji nadmiarowości kopii zapasowych.

Przywracanie do punktu w czasie jest przydatne w wielu scenariuszach. Na przykład, gdy użytkownik przypadkowo usunie dane, porzuca istotną tabelę lub bazę danych lub jeśli przypadkowo zastąpi dobre dane przy użyciu nieprawidłowych danych z powodu wady aplikacji.

Może być konieczne poczekanie na wykonanie następnej kopii zapasowej dziennika transakcji, aby można było przywrócić do punktu w czasie w ciągu ostatnich pięciu minut.

### <a name="geo-restore"></a>Przywracanie geograficzne

Serwer można przywrócić w innym regionie świadczenia usługi Azure, w którym usługa jest dostępna, jeśli skonfigurowano serwer pod kątem geograficznie nadmiarowych kopii zapasowych. Serwery obsługujące do 4 TB pamięci masowej można przywrócić do regionu z parą geograficzną lub do dowolnego regionu, który obsługuje maksymalnie 16 TB pamięci masowej. W przypadku serwerów, które obsługują do 16 TB pamięci masowej, można przywrócić kopie zapasowe w dowolnym regionie, w którym są również obsługiwane serwery 16 TB. Przejrzyj [Azure Database for MariaDB warstwy cenowe](concepts-pricing-tiers.md) , aby wyświetlić listę obsługiwanych regionów.

Przywracanie geograficzne jest domyślną opcją odzyskiwania, gdy serwer jest niedostępny z powodu zdarzenia w regionie, w którym znajduje się serwer. Jeśli zdarzenie w dużej skali w regionie powoduje niedostępność aplikacji bazy danych, można przywrócić serwer z kopii zapasowych nadmiarowych geograficznie do serwera w dowolnym innym regionie. Przywracanie geograficzne wykorzystuje najnowszą kopię zapasową serwera. Istnieje opóźnienie między wykonaniem kopii zapasowej a replikacją do innego regionu. To opóźnienie może wynosić do godziny, więc jeśli wystąpi awaria, może to oznaczać, że istnieje maksymalnie jedna godzina utraty danych.

> [!IMPORTANT]
>W przypadku wykonywania przywracania geograficznego dla nowo utworzonego serwera synchronizacja początkowej kopii zapasowej może trwać dłużej niż 24 godziny, w zależności od rozmiaru danych, ponieważ czas wstępnej kopii zapasowej pełnej migawki jest znacznie wyższy. Kolejne kopie zapasowe migawek są kopiami przyrostowymi, dlatego przywracanie jest szybsze po 24 godzinach tworzenia serwera. Jeśli oceniasz, czy przywracasz lokalizację geograficzną, aby zdefiniować RTO, zalecamy odczekanie i ocenę operacji przywracania geograficznego **tylko po 24 godzinach** tworzenia serwera w celu uzyskania lepszych oszacowań.

Podczas przywracania geograficznego konfiguracje serwera, które można zmienić, obejmują generowanie obliczeń, rdzeń wirtualny, okres przechowywania kopii zapasowych i opcje nadmiarowości kopii zapasowych. Zmiana warstwy cenowej (podstawowa, Ogólnego przeznaczenia lub zoptymalizowana pod kątem pamięci) lub rozmiaru magazynu podczas przywracania geograficznego nie jest obsługiwana.

Szacowany czas odzyskiwania zależy od kilku czynników, takich jak rozmiary bazy danych, rozmiar dziennika transakcji, przepustowość sieci i łączna liczba baz danych, które są odzyskiwane w tym samym regionie w tym samym czasie. Czas odzyskiwania jest zwykle krótszy niż 12 godzin.

### <a name="perform-post-restore-tasks"></a>Wykonywanie zadań po przywróceniu

Po przywróceniu z dowolnego mechanizmu odzyskiwania należy wykonać następujące zadania, aby uzyskać kopie zapasowe użytkowników i aplikacji:

- Jeśli nowy serwer ma zastąpić oryginalny serwer, przekierować klientów i aplikacje klienckie na nowy serwer
- Upewnij się, że istnieją odpowiednie reguły sieci wirtualnej, aby użytkownicy mogli się łączyć. Te reguły nie są kopiowane z oryginalnego serwera.
- Upewnij się, że istnieją odpowiednie identyfikatory logowania i uprawnienia na poziomie bazy danych
- W razie potrzeby skonfiguruj alerty

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się więcej o ciągłości działania, zobacz [Omówienie ciągłości działania firmy](concepts-business-continuity.md).
- Aby przywrócić do punktu w czasie za pomocą Azure Portal, zobacz [przywracanie serwera do punktu w czasie przy użyciu Azure Portal](howto-restore-server-portal.md).
- Aby przywrócić do punktu w czasie za pomocą interfejsu wiersza polecenia platformy Azure, zobacz [przywracanie serwera do punktu w czasie za pomocą interfejsu wiersza polecenia](howto-restore-server-cli.md).
