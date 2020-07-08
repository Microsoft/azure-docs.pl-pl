---
title: Tworzenie kopii zapasowych i przywracanie-Azure Database for PostgreSQL — pojedynczy serwer
description: Dowiedz się więcej o automatycznych kopiach zapasowych i przywracaniu serwera Azure Database for PostgreSQL — jeden serwer.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 02/25/2020
ms.openlocfilehash: 3e6dfd5882e49ad903e8cff6f0ec7f3d6bd4a8b7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "77619621"
---
# <a name="backup-and-restore-in-azure-database-for-postgresql---single-server"></a>Tworzenie kopii zapasowych i przywracanie w Azure Database for PostgreSQL-pojedynczym serwerze

Azure Database for PostgreSQL automatycznie tworzy kopie zapasowe serwera i przechowuje je w ramach użytkownika skonfigurowanego lokalnie nadmiarowy lub geograficznie nadmiarowy. Kopie zapasowe mogą być używane do przywracania serwera do punktu w czasie. Tworzenie kopii zapasowych i przywracanie jest istotną częścią strategii ciągłości działania, ponieważ chronią dane przed przypadkowym uszkodzeniem lub usunięciem.

## <a name="backups"></a>Tworzenie kopii zapasowych

Azure Database for PostgreSQL wykonuje kopie zapasowe plików danych i dziennika transakcji. W zależności od obsługiwanego maksymalnego rozmiaru magazynu należy wykonać pełne i różnicowe kopie zapasowe (maksymalnie 4 TB serwerów magazynu) lub migawek kopii zapasowych (maksymalnie 16 TB serwerów magazynu). Te kopie zapasowe umożliwiają przywrócenie serwera do dowolnego punktu w czasie w ramach skonfigurowanego okresu przechowywania kopii zapasowych. Domyślny okres przechowywania kopii zapasowych wynosi siedem dni. Opcjonalnie można skonfigurować ją do 35 dni. Wszystkie kopie zapasowe są szyfrowane przy użyciu szyfrowania AES 256-bitowego.

Nie można eksportować tych plików kopii zapasowej. Kopie zapasowe mogą być używane tylko w przypadku operacji przywracania w Azure Database for PostgreSQL. Aby skopiować bazę danych, można użyć [pg_dump](howto-migrate-using-dump-and-restore.md) .

### <a name="backup-frequency"></a>Częstotliwość wykonywania kopii zapasowych

Ogólnie rzecz biorąc, pełne kopie zapasowe są wykonywane co tydzień, różnicowe kopie zapasowe są wykonywane dwa razy dziennie dla serwerów z maksymalną obsługiwaną ilością pamięci 4 TB. Kopie zapasowe migawek są wykonywane co najmniej raz dziennie dla serwerów, które obsługują do 16 TB miejsca do magazynowania. Kopie zapasowe dziennika transakcji w obu przypadkach są wykonywane co pięć minut. Pierwsza migawka pełnej kopii zapasowej jest zaplanowana natychmiast po utworzeniu serwera. Początkowa pełna kopia zapasowa może trwać dłużej na dużym przywracanym serwerze. Najwcześniejszym punktem w czasie, w którym można przywrócić nowy serwer, jest czas, w którym zostanie ukończona początkowa pełna kopia zapasowa. Ponieważ migawki są natychmiastowe, serwery obsługujące do 16 TB pamięci masowej mogą być przywracane z powrotem do czasu utworzenia.

### <a name="backup-redundancy-options"></a>Opcje nadmiarowości kopii zapasowej

Azure Database for PostgreSQL zapewnia elastyczność wyboru między lokalnie nadmiarowym lub geograficznie nadmiarowym magazynem kopii zapasowych w warstwach Ogólnego przeznaczenia i zoptymalizowanych pod kątem pamięci. Gdy kopie zapasowe są przechowywane w magazynie geograficznie nadmiarowym, nie są przechowywane tylko w regionie, w którym znajduje się serwer, ale są również replikowane do [sparowanego centrum danych](https://docs.microsoft.com/azure/best-practices-availability-paired-regions). Zapewnia to lepszą ochronę i możliwość przywracania serwera w innym regionie w przypadku awarii. Warstwa Podstawowa oferuje tylko lokalnie nadmiarowy magazyn kopii zapasowych.

> [!IMPORTANT]
> Konfiguracja lokalnie nadmiarowego lub geograficznie nadmiarowego magazynu dla kopii zapasowej jest dozwolona tylko podczas tworzenia serwera. Po aprowizacji serwera nie można zmienić opcji nadmiarowości magazynu kopii zapasowej.

### <a name="backup-storage-cost"></a>Koszt magazynu kopii zapasowych

Azure Database for PostgreSQL zapewnia do 100% magazynu z zainicjowaną obsługą kopii zapasowych bez dodatkowych kosztów. Zwykle jest to odpowiednie do przechowywania kopii zapasowych przez siedem dni. Każdy dodatkowy magazyn kopii zapasowych jest naliczany w GB miesiąca.

Jeśli na przykład masz zainicjowany serwer z 250 GB, masz 250 GB miejsca w magazynie kopii zapasowych bez dodatkowych opłat. Magazyn o wielkości przekraczającej 250 GB jest naliczany.

## <a name="restore"></a>Przywracanie

W Azure Database for PostgreSQL wykonanie przywracania powoduje utworzenie nowego serwera na podstawie kopii zapasowych oryginalnego serwera.

Dostępne są dwa typy przywracania:

- **Przywracanie do punktu w czasie** jest dostępne z opcją nadmiarowości kopii zapasowych i tworzy nowy serwer w tym samym regionie, w którym znajduje się oryginalny serwer.
- **Przywracanie geograficzne** jest dostępne tylko wtedy, gdy skonfigurowano serwer dla magazynu geograficznie nadmiarowego i umożliwia przywrócenie serwera do innego regionu.

Szacowany czas odzyskiwania zależy od kilku czynników, takich jak rozmiary bazy danych, rozmiar dziennika transakcji, przepustowość sieci i łączna liczba baz danych, które są odzyskiwane w tym samym regionie w tym samym czasie. Czas odzyskiwania jest zwykle krótszy niż 12 godzin.

> [!IMPORTANT]
> **Nie** można przywrócić usuniętych serwerów. Usunięcie serwera spowoduje również usunięcie wszystkich baz danych należących do serwera, których nie można odzyskać. Aby chronić zasoby serwera, po wdrożeniu przed przypadkowym usunięciem lub nieoczekiwanymi zmianami, Administratorzy mogą korzystać z [blokad zarządzania](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources).

### <a name="point-in-time-restore"></a>Przywracanie do określonego momentu

Niezależnie od opcji nadmiarowości kopii zapasowej można wykonać przywracanie do dowolnego punktu w czasie w ramach okresu przechowywania kopii zapasowej. Nowy serwer jest tworzony w tym samym regionie świadczenia usługi Azure, w którym znajduje się oryginalny serwer. Jest on tworzony z konfiguracją pierwotnego serwera dla warstwy cenowej, generowania obliczeń, liczby rdzeni wirtualnych, rozmiaru magazynu, okresu przechowywania kopii zapasowych i opcji nadmiarowości kopii zapasowych.

Przywracanie do punktu w czasie jest przydatne w wielu scenariuszach. Na przykład, gdy użytkownik przypadkowo usunie dane, porzuca istotną tabelę lub bazę danych lub jeśli przypadkowo zastąpi dobre dane przy użyciu nieprawidłowych danych z powodu wady aplikacji.

Może być konieczne poczekanie na wykonanie następnej kopii zapasowej dziennika transakcji, aby można było przywrócić do punktu w czasie w ciągu ostatnich pięciu minut.

### <a name="geo-restore"></a>Przywracanie geograficzne

Serwer można przywrócić w innym regionie świadczenia usługi Azure, w którym usługa jest dostępna, jeśli skonfigurowano serwer pod kątem geograficznie nadmiarowych kopii zapasowych. Serwery obsługujące do 4 TB pamięci masowej można przywrócić do regionu z parą geograficzną lub do dowolnego regionu, który obsługuje maksymalnie 16 TB pamięci masowej. W przypadku serwerów, które obsługują do 16 TB pamięci masowej, można przywrócić kopie zapasowe w dowolnym regionie, w którym są również obsługiwane serwery 16 TB. Przejrzyj [warstwy cenowe usługi Azure Database for PostgeSQL](concepts-pricing-tiers.md) , aby wyświetlić listę obsługiwanych regionów.

Przywracanie geograficzne jest domyślną opcją odzyskiwania, gdy serwer jest niedostępny z powodu zdarzenia w regionie, w którym znajduje się serwer. Jeśli zdarzenie w dużej skali w regionie powoduje niedostępność aplikacji bazy danych, można przywrócić serwer z kopii zapasowych nadmiarowych geograficznie do serwera w dowolnym innym regionie. Istnieje opóźnienie między wykonaniem kopii zapasowej a replikacją do innego regionu. To opóźnienie może wynosić do godziny, więc jeśli wystąpi awaria, może to oznaczać, że istnieje maksymalnie jedna godzina utraty danych.

Podczas przywracania geograficznego konfiguracje serwera, które można zmienić, obejmują generowanie obliczeń, rdzeń wirtualny, okres przechowywania kopii zapasowych i opcje nadmiarowości kopii zapasowych. Zmiana warstwy cenowej (podstawowa, Ogólnego przeznaczenia lub zoptymalizowana pod kątem pamięci) lub rozmiaru magazynu nie jest obsługiwana.

### <a name="perform-post-restore-tasks"></a>Wykonywanie zadań po przywróceniu

Po przywróceniu z dowolnego mechanizmu odzyskiwania należy wykonać następujące zadania, aby uzyskać kopie zapasowe użytkowników i aplikacji:

- Jeśli nowy serwer ma zastąpić oryginalny serwer, przekierować klientów i aplikacje klienckie na nowy serwer
- Upewnij się, że istnieją odpowiednie reguły zapory na poziomie serwera i sieci wirtualnej, aby użytkownicy mogli się łączyć. Te reguły nie są kopiowane z oryginalnego serwera.
- Upewnij się, że istnieją odpowiednie identyfikatory logowania i uprawnienia na poziomie bazy danych
- W razie potrzeby skonfiguruj alerty

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak przywrócić przy użyciu [Azure Portal](howto-restore-server-portal.md).
- Dowiedz się, jak przywrócić za pomocą [interfejsu wiersza polecenia platformy Azure](howto-restore-server-cli.md).
- Aby dowiedzieć się więcej o ciągłości działania, zobacz [Omówienie ciągłości działania firmy](concepts-business-continuity.md).
