---
title: Tworzenie kopii zapasowych i przywracanie w Azure Database for PostgreSQL-elastycznym serwerze
description: Informacje na temat pojęć związanych z tworzeniem kopii zapasowych i przywracaniem z serwera Azure Database for PostgreSQL-elastyczny
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: bed196d1be101ffa75affc389d390ec0fa764b05
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90937053"
---
# <a name="backup-and-restore-in-azure-database-for-postgresql---flexible-server"></a>Tworzenie kopii zapasowych i przywracanie w Azure Database for PostgreSQL-elastycznym serwerze

> [!IMPORTANT]
> Azure Database for PostgreSQL — serwer elastyczny jest w wersji zapoznawczej

Kopie zapasowe stanowią podstawową część wszelkich strategii ciągłości działania. Ułatwiają one ochronę danych przed przypadkowym uszkodzeniem lub usunięciem. Azure Database for PostgreSQL — serwer elastyczny automatycznie tworzy kopię zapasową serwera i zachowuje kopie zapasowe przez okres do 35 dni. Podczas przywracania możesz określić datę i godzinę, do której chcesz przywrócić w okresie przechowywania. Całkowity czas przywracania i odzyskiwania zależy od rozmiaru plików bazy danych i ilości odzyskiwania, które mają zostać wykonane. 

### <a name="backup-process-in-flexible-server"></a>Proces tworzenia kopii zapasowej na serwerze elastycznym
Pierwsza kopia zapasowa migawki jest planowana natychmiast po utworzeniu serwera elastycznego. Następnie wykonywane jest codzienne tworzenie kopii zapasowej plików danych. Kopie zapasowe są przechowywane w strefie nadmiarowego magazynu w obrębie regionu. Dzienniki transakcji (dzienniki zapisu z wyprzedzeniem — WAL) również są archiwizowane w sposób ciągły, dzięki czemu będzie można przywrócić ostatnią zatwierdzoną transakcję. Te kopie zapasowe danych i dzienników umożliwiają przywrócenie serwera do dowolnego punktu w czasie w ramach skonfigurowanego okresu przechowywania kopii zapasowej. Wszystkie kopie zapasowe są szyfrowane za pomocą 256-bitowego szyfrowania AES.

Jeśli baza danych jest skonfigurowana z wysoką dostępnością, codzienne migawki są wykonywane z poziomu podstawowego, a ciągłe kopie zapasowe dziennika są wykonywane z stanu gotowości.

> [!IMPORTANT]
>Kopie zapasowe nie są wykonywane na zatrzymanych serwerach. Kopie zapasowe są jednak wznawiane, gdy baza danych jest uruchamiana automatycznie po upływie 7 dni lub uruchomieniu przez użytkownika.

Kopie zapasowe mogą być używane tylko w przypadku operacji przywracania na serwerze elastycznym. Jeśli chcesz wyeksportować lub zaimportować dane na elastyczny serwer, użyj metodologii [zrzutów i przywracania](https://docs.microsoft.com/azure/postgresql/howto-migrate-using-dump-and-restore)   .


### <a name="backup-retention"></a>Przechowywanie kopii zapasowej

Kopie zapasowe są zachowywane na podstawie ustawienia okresu przechowywania kopii zapasowej serwera. Można wybrać okres przechowywania od 7 do 35 dni. Domyślny okres przechowywania wynosi siedem dni. Okres przechowywania można ustawić podczas tworzenia serwera lub można go zaktualizować w późniejszym czasie. Kopie zapasowe są zachowywane nawet w przypadku zatrzymanych serwerów.

Okres przechowywania kopii zapasowej decyduje o tym, jak daleko wstecz w czasie można pobrać przywracanie do punktu w czasie, ponieważ jest ono \' zależne od dostępnych kopii zapasowych. Okres przechowywania kopii zapasowej może być również traktowany jako okno odzyskiwania z perspektywy przywracania. Wszystkie kopie zapasowe wymagane do przeprowadzenia przywracania do punktu w czasie w ramach okresu przechowywania kopii zapasowych są zachowywane w magazynie kopii zapasowych. Na przykład jeśli okres przechowywania kopii zapasowej jest ustawiony na siedem dni, okno odzyskiwania jest uznawane za ostatnie siedem dni. W tym scenariuszu zachowywane są wszystkie dane i dzienniki wymagane do przywrócenia i odzyskania serwera w ciągu ostatnich siedmiu dni. 


### <a name="backup-storage-cost"></a>Koszt magazynu kopii zapasowych

Elastyczny serwer oferuje do 100% miejsca do magazynowania z magazynem kopii zapasowych bez dodatkowych kosztów. Każdy dodatkowy magazyn kopii zapasowych jest naliczany w GB miesięcznie. Na przykład jeśli masz zainicjowany serwer z 250 GiB magazynu, masz 250 GiB pojemności magazynu kopii zapasowych bez dodatkowych opłat. Jeśli codzienne użycie kopii zapasowej to 25 GiB, możesz mieć do 10 dni wolnego miejsca w magazynie kopii zapasowych. Użycie magazynu kopii zapasowych przekracza 250 GiB jest naliczana zgodnie z [modelem cen](https://azure.microsoft.com/pricing/details/postgresql/).

Metryki [używanej przez magazyn kopii zapasowych](https://docs.microsoft.com/azure/postgresql/concepts-monitoring)można użyć   w Azure Portal do monitorowania magazynu kopii zapasowych zużywanego przez serwer. Metryka Użycie magazynu kopii zapasowych reprezentuje sumę magazynu używanego przez wszystkie przechowywane kopie zapasowe bazy danych i kopie zapasowe dzienników na podstawie okresu przechowywania kopii zapasowych ustawionego dla tego serwera.  Duża liczba transakcji na serwerze może powodować zwiększenie użycia magazynu kopii zapasowych niezależnie od całkowitego rozmiaru bazy danych.

Podstawowym sposobem kontrolowania kosztu magazynowania kopii zapasowych jest ustawienie odpowiedniego okresu przechowywania kopii zapasowych i wybranie odpowiednich opcji nadmiarowości kopii zapasowych w celu spełnienia potrzebnych celów odzyskiwania.

> [!IMPORTANT]
> Kopie zapasowe nadmiarowe nie są obecnie obsługiwane przez elastyczny serwer.

## <a name="point-in-time-restore-overview"></a>Przegląd przywracania do punktu w czasie

Na serwerze elastycznym wykonywanie operacji przywracania do punktu w czasie powoduje utworzenie nowego serwera z elastycznych \' kopii zapasowych serwera w tym samym regionie, w którym znajduje się serwer źródłowy. Jest on tworzony z konfiguracją serwera źródłowego dla warstwy cenowej, generowania obliczeń, liczby rdzeni wirtualnych, rozmiaru magazynu, okresu przechowywania kopii zapasowych i opcji nadmiarowości kopii zapasowych. Również tagi i ustawienia takie jak sieć wirtualna i ustawienia zapory są dziedziczone z serwera źródłowego. 

 > [!IMPORTANT]
> Jeśli przywracasz elastyczny serwer skonfigurowany z nadmiarową wysoką dostępnością strefy, przywrócony serwer zostanie skonfigurowany bez wysokiej dostępności i w tym samym regionie, w którym znajduje się serwer podstawowy. 

 ### <a name="restore-process"></a>Proces przywracania

Pliki fizycznych baz danych są najpierw przywracane z kopii zapasowych migawek do lokalizacji danych serwera. Odpowiednia kopia zapasowa, która została wykonana wcześniej od żądanego punktu w czasie, jest automatycznie wybierana i przywracana. Proces odzyskiwania zostaje następnie zainicjowany przy użyciu plików WAL, aby zapewnić spójny stan bazy danych. 

 Załóżmy na przykład, że założono, że kopie zapasowe są wykonywane codziennie o godzinie 23:00. Jeśli punkt przywracania jest 15 sierpnia 2020 o 10:00 am, zostanie przywrócona dzienna kopia zapasowa 14 sierpnia 2020. Baza danych zostanie odzyskana do momentu od 10:00 25 sierpnia 2020 przy użyciu dzienników transakcji kopia zapasowa między 24 sierpnia, 23:00 do 25 sierpnia od 10:00. 

 Zapoznaj się z [tymi krokami](./how-to-restore-server-portal.md) , aby przywrócić serwer bazy danych.

> [!IMPORTANT]
> Operacje przywracania na serwerze elastycznym tworzą nowy serwer bazy danych i nie zastępują istniejącego serwera baz danych.

Przywracanie do punktu w czasie jest przydatne w wielu scenariuszach. Na przykład, gdy użytkownik przypadkowo usunie dane, porzuca istotną tabelę lub bazę danych lub jeśli przypadkowo zastąpi dobre dane przy użyciu nieprawidłowych danych z powodu wady aplikacji. Będzie można przywrócić ostatnią transakcję ze względu na ciągłą kopię zapasową dzienników transakcji.

Można wybrać między najwcześniejszym punktem przywracania i niestandardowym punktem przywracania.

-   Najwcześniejszy **punkt przywracania**: w zależności od okresu przechowywania będzie to Najwcześniejszy czas, który można przywrócić. Najstarsza godzina tworzenia kopii zapasowej zostanie wybrana i będzie wyświetlana w portalu. Jest to przydatne, jeśli chcesz zbadać lub wykonać niektóre testy od momentu rozpoczęcia tego punktu w czasie.

-   **Niestandardowy punkt przywracania**: Ta opcja umożliwia wybranie dowolnego punktu w czasie w okresie przechowywania zdefiniowanym dla tego elastycznego serwera. Domyślnie Najnowsza godzina w formacie UTC jest zaznaczona i jest przydatna, jeśli chcesz przywrócić ostatnią zatwierdzoną transakcję do celów testowych. Opcjonalnie możesz wybrać inne dni i godziny. 

Szacowany czas odzyskiwania zależy od kilku czynników, takich jak rozmiar bazy danych, ilość dzienników transakcji do przetworzenia, przepustowość sieci i łączna liczba baz danych, które są odzyskiwane w tym samym regionie w tym samym czasie. Ogólny czas odzyskiwania zazwyczaj trwa od kilku minut do kilku godzin.


> [!IMPORTANT]
>  **Nie**można przywrócić usuniętych serwerów   . Usunięcie serwera spowoduje również usunięcie wszystkich baz danych należących do serwera, których nie można odzyskać. Aby chronić zasoby serwera, po wdrożeniu przed przypadkowym usunięciem lub nieoczekiwanymi zmianami, Administratorzy mogą korzystać z [blokad zarządzania](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources).

## <a name="perform-post-restore-tasks"></a>Wykonywanie zadań po przywróceniu

Po przywróceniu bazy danych można wykonać następujące zadania w celu przygotowania i uruchomienia tworzenia kopii zapasowych użytkowników i aplikacji:

-   Jeśli nowy serwer ma zastąpić oryginalny serwer, przekierować klientów i aplikacje klienckie na nowy serwer.

-   Upewnij się, że istnieją odpowiednie reguły zapory na poziomie serwera i sieci wirtualnej, aby użytkownicy mogli się łączyć. Te reguły nie są kopiowane z oryginalnego serwera.
  
-   Obliczenia dla przywróconego serwera można skalować w górę i w dół odpowiednio do wymagań.

-   Upewnij się, że istnieją odpowiednie identyfikatory logowania i uprawnienia na poziomie bazy danych.

-   Skonfiguruj alerty odpowiednio do potrzeb.
  
-  Jeśli przywrócono bazę danych o wysokiej dostępności i jeśli chcesz skonfigurować przywrócony serwer o wysokiej dostępności, możesz wykonać [te kroki](./how-to-manage-high-availability-portal.md).


## <a name="next-steps"></a>Następne kroki

-   Informacje o [ciągłości działania firmy](./concepts-business-continuity.md)
-   Więcej informacji na temat [strefy nadmiarowej wysokiej dostępności](./concepts-high-availability.md)
-   Dowiedz się [, jak przywrócić](./how-to-restore-server-portal.md)

