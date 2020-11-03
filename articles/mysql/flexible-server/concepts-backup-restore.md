---
title: Tworzenie kopii zapasowych i przywracanie w Azure Database for MySQL elastycznym serwerze
description: Informacje na temat pojęć związanych z tworzeniem kopii zapasowych i przywracaniem Azure Database for MySQL elastycznym serwerze
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: conceptual
ms.date: 09/21/2020
ms.openlocfilehash: 2d69427f9f11a47cedeccb4b1da38b770952f029
ms.sourcegitcommit: 80034a1819072f45c1772940953fef06d92fefc8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/03/2020
ms.locfileid: "93240770"
---
# <a name="backup-and-restore-in-azure-database-for-mysql-flexible-server-preview"></a>Tworzenie kopii zapasowych i przywracanie w Azure Database for MySQL elastycznym serwerze (wersja zapoznawcza)

> [!IMPORTANT] 
> Serwer elastyczny Azure Database for MySQL jest obecnie w publicznej wersji zapoznawczej.

Azure Database for MySQL elastyczny serwer, program automatycznie tworzy kopie zapasowe serwera i bezpiecznie zapisuje je w lokalnym nadmiarowym magazynie w regionie. Kopie zapasowe mogą być używane do przywracania serwera do punktu w czasie. Tworzenie kopii zapasowych i przywracanie jest istotną częścią strategii ciągłości działania, ponieważ chronią dane przed przypadkowym uszkodzeniem lub usunięciem.

## <a name="backup-overview"></a>Omówienie usługi Backup

Elastyczny serwer pobiera kopie zapasowe plików danych i zapisuje je w lokalnym nadmiarowym magazynie. Serwer wykonuje również kopie zapasowe dzienników transakcji i przechowuje je w lokalnym magazynie nadmiarowym. Te kopie zapasowe umożliwiają przywrócenie serwera do dowolnego punktu w czasie w ramach skonfigurowanego okresu przechowywania kopii zapasowych. Domyślny okres przechowywania kopii zapasowych wynosi siedem dni. Opcjonalnie można skonfigurować kopię zapasową bazy danych od 1 do 35 dni. Wszystkie kopie zapasowe są szyfrowane przy użyciu szyfrowania AES 256-bitowego dla danych przechowywanych w spoczynku.

Nie można eksportować tych plików kopii zapasowej. Kopie zapasowe mogą być używane tylko w przypadku operacji przywracania na serwerze elastycznym. Możesz również użyć [mysqldump](../concepts-migrate-dump-restore.md#dump-and-restore-using-mysqldump-utility) z klienta MySQL, aby skopiować bazę danych.

## <a name="backup-frequency"></a>Częstotliwość wykonywania kopii zapasowych

Kopie zapasowe na serwerach elastycznych są oparte na migawce. Pierwsza pełna kopia zapasowa migawki jest planowana natychmiast po utworzeniu serwera. Kopia zapasowa pierwszej pełnej migawki jest zachowywana jako podstawowa kopia zapasowa serwera. Kolejne kopie zapasowe migawki są jedynie różnicowymi kopiami zapasowymi.

Różnicowe kopie zapasowe migawek są tworzone co najmniej raz dziennie. Różnicowe kopie zapasowe migawek nie są tworzone zgodnie z ustalonym harmonogramem. Różnicowe kopie zapasowe migawek są wykonywane co 24 godziny, chyba że dzienniki binarne w programie MySQL przekraczają 50 GB od ostatniej różnicowej kopii zapasowej. W ciągu dnia dozwolonych jest maksymalnie sześć migawek różnicowych. Kopie zapasowe dziennika transakcji są wykonywane co pięć minut.

## <a name="backup-retention"></a>Przechowywanie kopii zapasowej

Kopie zapasowe bazy danych są przechowywane w magazynie lokalnie nadmiarowy (LRS) — który jest przechowywany w trzech kopiach w regionie. Kopie zapasowe są zachowywane na podstawie ustawienia okresu przechowywania kopii zapasowej na serwerze. Okres przechowywania można wybrać z przedziału od 1 do 35 dni, a domyślny okres przechowywania wynosi siedem dni. Okres przechowywania można ustawić podczas tworzenia serwera lub później, aktualizując konfigurację kopii zapasowej za pomocą Azure Portal.

Okres przechowywania kopii zapasowej decyduje o tym, jak daleko w czasie można wykonać operację przywracania do punktu w czasie, ponieważ jest ona oparta na dostępnych kopiach zapasowych. Okres przechowywania kopii zapasowej może być również traktowany jako okno odzyskiwania z perspektywy przywracania. Wszystkie kopie zapasowe wymagane do przeprowadzenia przywracania do punktu w czasie w ramach okresu przechowywania kopii zapasowych są zachowywane w magazynie kopii zapasowych. Na przykład, jeśli okres przechowywania kopii zapasowej jest ustawiony na siedem dni, okno odzyskiwania jest uznawane za ostatnie siedem dni. W tym scenariuszu zachowywane są wszystkie kopie zapasowe wymagane do przywrócenia serwera w ciągu ostatnich siedmiu dni. W przypadku korzystania z okna przechowywania kopii zapasowych siedmiu dni migawki baz danych i kopie zapasowe dziennika transakcji są przechowywane przez ostatnie osiem dni (1 dzień przed oknem).

## <a name="backup-storage-cost"></a>Koszt magazynu kopii zapasowych

Elastyczny serwer oferuje do 100% miejsca do magazynowania z magazynem kopii zapasowych bez dodatkowych kosztów. Każdy dodatkowy magazyn kopii zapasowych jest naliczany w GB miesięcznie. Na przykład jeśli masz zainicjowany serwer z 250 GB miejsca w magazynie, masz 250 GB miejsca do magazynowania dla kopii zapasowych serwera bez dodatkowych opłat. Jeśli codzienne użycie kopii zapasowej to 25 GB, możesz mieć do 10 dni wolnego miejsca w magazynie kopii zapasowych. Magazyn używany do tworzenia kopii zapasowych przekracza 250 GB jest naliczany zgodnie z [modelem cen](https://azure.microsoft.com/pricing/details/mysql/).

Możesz użyć metryki [używany magazyn kopii zapasowych](../concepts-monitoring.md) w Azure monitor dostępnej w Azure Portal do monitorowania magazynu kopii zapasowych zużywanego przez serwer. Metryka używany **Magazyn kopii zapasowych** reprezentuje sumę magazynu zużywanego przez wszystkie kopie zapasowe bazy danych i kopie zapasowe dziennika przechowywane na podstawie okresu przechowywania kopii zapasowej ustawionego dla serwera. Duża liczba transakcji na serwerze może powodować zwiększenie użycia magazynu kopii zapasowych niezależnie od całkowitego rozmiaru bazy danych.

Podstawowym sposobem kontrolowania kosztów magazynu kopii zapasowych jest ustawienie odpowiedniego okresu przechowywania kopii zapasowych. Możesz wybrać okres przechowywania od 1 do 35 dni.

> [!IMPORTANT]
> Kopie zapasowe z serwera bazy danych skonfigurowanego w strefie nadmiarowa Konfiguracja wysokiej dostępności jest wykonywana z serwera podstawowej bazy danych, ponieważ obciążenie jest minimalne z kopiami zapasowymi migawek.

> [!IMPORTANT]
> Kopie zapasowe nadmiarowe nie są obecnie obsługiwane przez elastyczny serwer.

## <a name="point-in-time-restore"></a>Przywracanie do określonego momentu

W Azure Database for MySQL elastycznym serwerze wykonywanie przywracania do punktu w czasie powoduje utworzenie nowego serwera z kopii zapasowych serwera elastycznego w tym samym regionie, w którym znajduje się serwer źródłowy. Jest on tworzony z konfiguracją pierwotnego serwera dla warstwy obliczeń, liczby rdzeni wirtualnych, rozmiaru magazynu, okresu przechowywania kopii zapasowych i opcji nadmiarowości kopii zapasowej. Ponadto Tagi i ustawienia, takie jak sieć wirtualna i Zapora, są dziedziczone z serwera źródłowego. Przywrócone warstwy obliczeniowe i magazynowe serwera przywracane oraz ustawienia konfiguracji i zabezpieczeń można zmienić po zakończeniu przywracania.

> [!NOTE]
> Istnieją dwa parametry serwera, które są resetowane do wartości domyślnych (i nie są kopiowane z serwera podstawowego) po operacji przywracania
> *   time_zone — ta wartość jest ustawiana na domyślny SYSTEM wartości
> *   event_scheduler — event_scheduler jest wyłączona na przywróconym serwerze

Przywracanie do punktu w czasie jest przydatne w wielu scenariuszach. Niektóre typowe przypadki użycia obejmują:
-   Gdy użytkownik przypadkowo usunie dane w bazie danych
-   Użytkownik porzuca ważną tabelę lub bazę danych
-   Aplikacja użytkownika przypadkowo zastępuje dobre dane nieprawidłowymi danymi z powodu wady aplikacji.

Można wybrać między najnowszym punktem przywracania i niestandardowym punktem przywracania za pośrednictwem [Azure Portal](how-to-restore-server-portal.md).

-   **Najnowszy punkt przywracania** : najnowszy punkt przywracania ułatwia przywrócenie serwera do ostatniej kopii zapasowej wykonanej na serwerze źródłowym. Sygnatura czasowa dla przywracania zostanie również wyświetlona w portalu. Ta opcja jest przydatna, aby szybko przywrócić serwer do najbardziej zaktualizowanego stanu.
-   **Niestandardowy punkt przywracania** : umożliwi to wybranie dowolnego punktu w czasie w okresie przechowywania zdefiniowanym dla tego serwera elastycznego. Ta opcja przydaje się do przywrócenia serwera w precyzyjnym punkcie w czasie w celu odzyskania sprawności po błędzie użytkownika.

Szacowany czas odzyskiwania zależy od kilku czynników, takich jak rozmiary baz danych, rozmiar kopii zapasowej dziennika transakcji, rozmiar obliczeń jednostki SKU i czas przywracania. Odzyskiwanie dziennika transakcji jest najbardziej czasochłonną częścią procesu przywracania. Jeśli czas przywracania jest wybierany bliżej harmonogramu tworzenia kopii zapasowej pełnej lub różnicowej, przywracanie jest szybsze, ponieważ aplikacja dziennika transakcji jest minimalna. Aby oszacować dokładny czas odzyskiwania dla serwera, zdecydowanie zalecamy przetestowanie go w środowisku, ponieważ zawiera zbyt wiele zmiennych specyficznych dla środowiska.

> [!IMPORTANT]
> Jeśli przywracasz elastyczny serwer skonfigurowany z nadmiarową wysoką dostępnością strefy, przywrócony serwer zostanie skonfigurowany w tym samym regionie i strefie co serwer podstawowy i wdrożony jako pojedynczy serwer elastyczny w trybie innym niż HA. Zapoznaj się z [strefą nadmiarową wysokiej dostępności](concepts-high-availability.md) dla serwera elastycznego.

> [!IMPORTANT]
> **Nie** można przywrócić usuniętych serwerów. Usunięcie serwera spowoduje również usunięcie wszystkich baz danych należących do serwera, których nie można odzyskać. Aby chronić zasoby serwera, po wdrożeniu przed przypadkowym usunięciem lub nieoczekiwanymi zmianami, Administratorzy mogą korzystać z [blokad zarządzania](../../azure-resource-manager/management/lock-resources.md).

## <a name="perform-post-restore-tasks"></a>Wykonywanie zadań po przywróceniu

Po przywróceniu z poziomu **najnowszego punktu przywracania** lub niestandardowego mechanizmu odzyskiwania **punktu przywracania** należy wykonać następujące zadania, aby zapewnić i uruchamiać kopie zapasowe użytkowników i aplikacji:

-   Jeśli nowy serwer ma zastąpić oryginalny serwer, przekierować klientów i aplikacje klienckie na nowy serwer.
-   Upewnij się, że istnieją odpowiednie reguły zapory na poziomie serwera i sieci wirtualnej, aby użytkownicy mogli się łączyć.
-   Upewnij się, że istnieją odpowiednie identyfikatory logowania i uprawnienia na poziomie bazy danych.
-   Skonfiguruj alerty odpowiednio do potrzeb.

## <a name="next-steps"></a>Następne kroki

-   Informacje o [ciągłości działania firmy](./concepts-business-continuity.md)
-   Więcej informacji na temat [strefy nadmiarowej wysokiej dostępności](./concepts-high-availability.md)
-   Informacje na temat [tworzenia kopii zapasowych i odzyskiwania](./concepts-backup-restore.md)
