---
title: Zarządzanie strefowo nadmiarową wysoką dostępnością — Azure Portal — Azure Database for MySQL serwer elastyczny
description: W tym artykule opisano sposób włączania lub wyłączania strefowo nadmiarowej wysokiej dostępności Azure Database for MySQL serwerze elastycznym za pośrednictwem Azure Portal.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 09/21/2020
ms.custom: references_regions
ms.openlocfilehash: e217dcaeafd553803f5c9699ab6d7779ed755b67
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107818294"
---
# <a name="manage-zone-redundant-high-availability-in-azure-database-for-mysql-flexible-server-preview"></a>Zarządzanie strefowo nadmiarową wysoką dostępnością Azure Database for MySQL serwerze elastycznym (wersja zapoznawcza)

W tym artykule opisano sposób włączania lub wyłączania strefowo nadmiarowej konfiguracji wysokiej dostępności na serwerze elastycznym.

Funkcja wysokiej dostępności apowiuje fizycznie oddzieloną replikę podstawową i rezerwową w różnych strefach. Aby uzyskać więcej informacji, zobacz [dokumentację dotyczącą pojęć związanych z wysoką dostępnością.](./concepts/../concepts-high-availability.md) 

> [!IMPORTANT]
> Strefowo nadmiarową wysoką dostępność można włączyć tylko podczas tworzenia serwera elastycznego.

Ta strona zawiera wskazówki dotyczące włączania lub wyłączania wysokiej dostępności. Ta operacja nie zmienia innych ustawień, takich jak konfiguracja sieci wirtualnej, ustawienia zapory i przechowywanie kopii zapasowych. Podobnie wyłączenie wysokiej dostępności jest operacją online i nie ma wpływu na łączność i operacje aplikacji.

> [!IMPORTANT]
> Wysoka dostępność strefowo nadmiarowa jest dostępna w ograniczonym zestawie regionów: Azja Południowo-Wschodnia, Zachodnie stany USA 2, Europa Zachodnia i Wschodnie stany USA.  

## <a name="enable-high-availability-during-server-creation"></a>Włączanie wysokiej dostępności podczas tworzenia serwera

Ta sekcja zawiera szczegółowe informacje dotyczące pól związanych z wysokiej jakości. Możesz wykonać następujące kroki, aby wdrożyć wysoką dostępność podczas tworzenia serwera elastycznego.

1.  Na stronie [Azure Portal](https://portal.azure.com/)pozycję Serwer elastyczny i kliknij pozycję **Utwórz.**  Aby uzyskać szczegółowe informacje na temat wypełniania szczegółów, takich jak **subskrypcja,** grupa **zasobów,** nazwa **serwera,** **region** i inne pola, zobacz dokumentację z niestandardowych informacji na temat tworzenia serwera.

2.  Kliknij pole wyboru Strefowo **nadmiarowa wysoka dostępność** w opcji Dostępność.

3.  Jeśli chcesz zmienić domyślne zasoby obliczeniowe i magazynowe, kliknij **pozycję Konfiguruj serwer.**

4.  Jeśli zaznaczono opcję wysokiej dostępności, warstwa z możliwością serii nie będzie dostępna do wyboru. Możesz wybrać warstwy obliczeniowe **Ogólnego przeznaczenia** lub Zoptymalizowane **pod kątem** pamięci.

    > [!IMPORTANT]
    > Obsługujemy tylko strefowo nadmiarową wysoką dostępność dla * warstwy cenowej Ogólnego **przeznaczenia** _ i _ *_Zoptymalizowane pod kątem_* pamięci*.

5.  Wybierz **rozmiar obliczeniowy** do wyboru z listy rozwijanej.

6.  Wybierz **pozycję Rozmiar magazynu w** GiB  przy użyciu paska suwaka i wybierz okres przechowywania kopii zapasowej z okresu od 7 dni do 35 dni.   

## <a name="disable-high-availability"></a>Wyłączanie wysokiej dostępności

Wykonaj następujące kroki, aby wyłączyć wysoką dostępność serwera elastycznego, który jest już skonfigurowany z nadmiarowością stref.

1.  W [Azure Portal](https://portal.azure.com/)wybierz istniejący serwer Azure Database for MySQL elastyczny.

2.  Na stronie serwera elastycznego kliknij pozycję **Wysoka** dostępność na panelu przednim, aby otworzyć stronę wysokiej dostępności.

3.  Kliknij pole wyboru **strefowo nadmiarowa wysoka dostępność,** aby wyłączyć tę opcję, a następnie kliknij **przycisk Zapisz,** aby zapisać zmianę.

4.  Zostanie wyświetlone okno dialogowe potwierdzenia, w którym można potwierdzić wyłączenie ha.

5.  Kliknij **przycisk Wyłącz wysoką** dostępność, aby wyłączyć wysoką dostępność.

6.  Zostanie wyświetlane powiadomienie o zakończeniu likwidowania wdrożenia o wysokiej dostępności.


## <a name="forced-failover"></a>Wymuszone trybu failover

Wykonaj następujące kroki, aby wymusić tryb failover z podstawowego do rezerwowego serwera elastycznego

1.  W [Azure Portal](https://portal.azure.com/)wybierz istniejący serwer Azure Database for MySQL elastyczny, który ma włączoną funkcję wysokiej dostępności.

2.  Na stronie serwera elastycznego kliknij pozycję **Wysoka** dostępność na panelu przednim, aby otworzyć stronę wysokiej dostępności.

3.  Sprawdź **podstawową strefę dostępności i** **rezerwową strefę dostępności**

4.  Kliknij pozycję **Wymuszone tryb failover,** aby zainicjować procedurę ręcznego trybu failover. Wyskakujące okienko informuje o oczekiwanym czasie pracy w trybu failover w zależności od bieżącego obciążenia w podstawowym i od czasu ostatniego punktu kontrolnego, odczytania komunikatu i kliknięcia przycisku OK.
 
5. Zostanie wyświetlane powiadomienie z informacjami o tym, że trwa trybu failover.

6. Po pomyślnym zakończeniu pracy w trybie failover na serwerze rezerwowym zostanie wyskakujące powiadomienie.

7. Sprawdź nową **podstawową strefę dostępności i** **rezerwową strefę dostępności.**

![Jak wymusić trybu failover](media/how-to-configure-high-availability/how-to-forced-failover.png) 

## <a name="next-steps"></a>Następne kroki

-   Dowiedz się więcej [o ciągłości działania](./concepts-business-continuity.md)
-   Dowiedz się więcej o [strefowo nadmiarowej wysokiej dostępności](./concepts-high-availability.md)
