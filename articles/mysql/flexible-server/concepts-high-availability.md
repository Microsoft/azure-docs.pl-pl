---
title: Omówienie nadmiarowej wysokiej dostępności strefy przy użyciu Azure Database for MySQL elastyczny serwer
description: Dowiedz się więcej o pojęciach nadmiarowej wysokiej dostępności strefy z Azure Database for MySQL elastyczny serwer
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: conceptual
ms.date: 01/29/2021
ms.openlocfilehash: f01a0869f7786ee6197835610456f4bb1cbd6b03
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99097121"
---
# <a name="high-availability-concepts-in-azure-database-for-mysql-flexible-server-preview"></a>Pojęcia dotyczące wysokiej dostępności w Azure Database for MySQL elastycznym serwerze (wersja zapoznawcza)

> [!IMPORTANT] 
> Serwer elastyczny Azure Database for MySQL jest obecnie w publicznej wersji zapoznawczej.

Azure Database for MySQL elastyczny serwer (wersja zapoznawcza) umożliwia konfigurowanie wysokiej dostępności z automatycznym trybem failover przy użyciu opcji **nadmiarowej** wysokiej dostępności strefy. W przypadku wdrożenia w konfiguracji strefowo nadmiarowej serwer elastyczny automatycznie aprowizuje replikę rezerwową i zarządza nią w innej strefie dostępności. Przy użyciu replikacji na poziomie magazynu dane są **synchronicznie replikowane** do serwera rezerw w strefie pomocniczej w celu włączenia zerowej utraty danych po przejściu do trybu failover. Tryb failover jest w pełni niewidoczny dla aplikacji klienckiej i nie wymaga żadnych akcji użytkownika. Serwer rezerwy nie jest dostępny dla żadnej operacji odczytu lub zapisu, ale jest to pasywne wstrzymywanie, aby włączyć szybką pracę w trybie failover. Czasy pracy w trybie failover zazwyczaj należą do zakresu od 60-120 sekund.

Konfiguracja nadmiarowej strefy o wysokiej dostępności umożliwia automatyczne przełączanie w tryb failover podczas planowanych zdarzeń, takich jak operacje obliczeniowe skalowania przez użytkownika, a także niezaplanowanych zdarzeń, takich jak błędy sprzętu i oprogramowania, awarie sieci, a nawet niepowodzenia strefy dostępności.

:::image type="content" source="./media/concepts-high-availability/1-flexible-server-overview-zone-redundant-ha.png" alt-text="Widok strefy nadmiarowej wysokiej dostępności":::

## <a name="zone-redundancy-architecture"></a>Architektura nadmiarowości strefy

Serwer podstawowy jest wdrażany w regionie i określonej strefie dostępności. Po wybraniu wysokiej dostępności serwer repliki zapasowej z taką samą konfiguracją jak serwer podstawowy jest automatycznie wdrażany, w tym z warstwą obliczeniową, rozmiarem obliczeń, rozmiarem magazynu i konfiguracją sieci. Dane dziennika są synchronicznie replikowane do repliki gotowości w celu zapewnienia zerowej utraty danych w przypadku awarii. Automatyczne kopie zapasowe, migawki i kopie zapasowe dzienników są wykonywane z serwera podstawowej bazy danych. 

Kondycja HA jest stale monitorowana i raportowana na stronie Przegląd.

Poniżej wymieniono różne stany replikacji:

| **Stan** | **Opis** |
| :----- | :------ |
| <b>NotEnabled | Strefa o nadmiarowej dostępności nie jest włączona |
| <b>CreatingStandby | W procesie tworzenia nowego stanu wstrzymania |
| <b>ReplicatingData | Po utworzeniu w stanie gotowości zostanie wystawiony serwer podstawowy. |
| <b>FailingOver | Serwer bazy danych jest w trakcie przełączenia w tryb failover z serwera podstawowego do stanu wstrzymania. |
| <b>Dobra kondycja | Strefowo nadmiarowy HA jest w stałym stanie i w dobrej kondycji. |
| <b>RemovingStandby | W oparciu o akcję użytkownika replika rezerwy jest w trakcie usuwania.| 

## <a name="advantages"></a>Zalety

Poniżej przedstawiono niektóre zalety korzystania z funkcji HA o nadmiarowości strefy: 

-   Replika wstrzymania zostanie wdrożona w ramach dokładnej konfiguracji maszyny wirtualnej, takiej jak rdzeni wirtualnych, Storage, ustawienia sieci (Sieć wirtualna, Zapora) itp.
-   Możliwość usunięcia repliki rezerwy poprzez wyłączenie wysokiej dostępności.
-   Automatyczne kopie zapasowe są oparte na migawkach, wykonywane z podstawowego serwera bazy danych i przechowywane w strefie nadmiarowej magazynu.
-   W przypadku przejścia w tryb failover Azure Database for MySQL elastyczny serwer automatycznie przejdzie w tryb failover w replice gotowości, jeśli jest włączona wysoka dostępność. Konfiguracja wysokiej dostępności spowoduje monitorowanie serwera podstawowego i przywrócenie jego do trybu online.
-   Klienci zawsze łączą się z serwerem podstawowej bazy danych.
-   W przypadku awarii bazy danych lub awarii węzła ponowne uruchomienie zostanie podjęte najpierw w tym samym węźle. Jeśli to się nie powiedzie, zostanie wyzwolona automatyczna praca awaryjna.
-   Możliwość ponownego uruchomienia serwera w celu pobrania wszelkich zmian parametrów serwera statycznego.

## <a name="steady-state-operations"></a>Operacje o stałym stanie

Aplikacje są połączone z serwerem podstawowym przy użyciu nazwy serwera bazy danych. Informacje o replice gotowości nie są ujawniane w celu uzyskania dostępu bezpośredniego. Zatwierdzenia i zapisy są potwierdzane aplikacji dopiero po utrwaleniu plików dziennika zarówno na dysku serwera podstawowego, jak i w replice rezerwowej w sposób synchroniczny. Ze względu na to, że to dodatkowe wymaganie dotyczące rundy, aplikacje mogą oczekiwać na podwyższony czas oczekiwania na zapisy i zatwierdzenia. Możesz monitorować kondycję wysokiej dostępności w portalu.

## <a name="failover-process"></a>Proces trybu failover 
W celu zapewnienia ciągłości biznesowej należy przeprowadzić proces pracy awaryjnej dla planowanych i nieplanowanych zdarzeń. 

### <a name="planned-events"></a>Zdarzenia planowane

Zdarzenia planowanych przestojów obejmują działania zaplanowane przez platformę Azure, takie jak okresowe aktualizacje oprogramowania, uaktualnienia wersji pomocniczej lub inicjowane przez klientów, takie jak skalowanie operacji obliczeniowych i skalowania. Wszystkie te zmiany są najpierw stosowane do repliki w stanie wstrzymania. W tym czasie aplikacje będą nadal uzyskiwać dostęp do serwera podstawowego. Po zaktualizowaniu repliki w celu przełączenia na serwer podstawowy są opróżniane, zostanie wyzwolone przełączenie w tryb failover, który aktywuje replikę rezerwy jako podstawową z tą samą nazwą serwera bazy danych przez zaktualizowanie rekordu DNS. Połączenia klienckie są rozłączone i będą musiały ponownie nawiązać połączenie i mogą wznowić swoje operacje. Nowy serwer rezerwy zostanie ustanowiony w tej samej strefie co stary element podstawowy. Należy oczekiwać, że ogólny czas pracy awaryjnej to 60-120 s. 

>[!NOTE]
> W przypadku operacji skalowania obliczeń skaluje pomocniczy serwer repliki, a następnie serwer podstawowy. Nie ma żadnych przełączeń do trybu failover.

### <a name="failover-process---unplanned-events"></a>Proces trybu failover — niezaplanowanych zdarzeń
Nieplanowane przestoje usługi obejmują błędy oprogramowania, które dotyczą błędów infrastruktury, takich jak obliczenia, Sieć, awarie magazynu, lub wpływ na dostępność bazy danych. W przypadku niedostępności bazy danych replikacja do repliki w stanie wstrzymania jest poważna, a replika rezerwy jest aktywowana jako podstawowa baza danych. System DNS jest aktualizowany, a następnie klienci ponownie nawiązują połączenie z serwerem bazy danych i wznawiają operacje. Ogólny czas przejścia w tryb failover powinien trwać 60-120 s. Jednak w zależności od działania w podstawowym serwerze baz danych w czasie pracy w trybie failover, takim jak duże transakcje i czas odzyskiwania, praca awaryjna może trwać dłużej.

## <a name="schedule-maintenance-window"></a>Zaplanuj okno obsługi 

Elastyczne serwery oferują możliwość planowania konserwacji, w którym można wybrać 30-minutowy okno w ciągu dnia preferencji, w którym działa konserwacja platformy Azure, np. poprawki lub uaktualnienia wersji pomocniczej. W przypadku elastycznych serwerów skonfigurowanych pod kątem wysokiej dostępności te działania konserwacyjne są wykonywane najpierw w odniesieniu do repliki w stanie wstrzymania. 

## <a name="point-in-time-restore"></a>Przywracanie do określonego momentu 
Ponieważ serwer elastyczny jest skonfigurowany w ramach wysokiej dostępności synchronicznie replikuje dane, serwer rezerwy będzie aktualny z serwerem podstawowym. Wszelkie błędy użytkownika na podstawowym — takie jak przypadkowe upuszczenie tabeli lub nieprawidłowe aktualizacje są wiernie replikowane do stanu wstrzymania. W związku z tym nie można użyć funkcji wstrzymywania do odzyskania z takich błędów logicznych. Aby odzyskać z tych błędów logicznych, należy wykonać przywracanie do punktu w czasie w prawo, zanim wystąpi błąd. Podczas przywracania bazy danych skonfigurowanej z wysoką dostępnością przy użyciu elastycznej funkcji przywracania do punktu w czasie na serwerze nowy serwer bazy danych zostanie przywrócony jako nowy serwer elastyczny z nazwą dostarczoną przez użytkownika. Następnie można wyeksportować obiekt z serwera bazy danych i zaimportować go do produkcyjnego serwera bazy danych. Podobnie, jeśli chcesz sklonować serwer bazy danych na potrzeby testowania i programowania, lub chcesz przywrócić do innych celów, możesz wybrać najnowszy punkt przywracania lub niestandardowy punkt przywracania. Operacja przywracania spowoduje utworzenie elastycznego serwera pojedynczej strefy.

## <a name="mitigate-downtime"></a>Eliminowanie przestojów 
Jeśli nie korzystasz z funkcji HA o nadmiarowości strefy, musisz nadal mieć możliwość ograniczenia przestoju aplikacji. Planowanie przestojów usługi, takich jak zaplanowane poprawki, wersje pomocnicze lub operacje zainicjowane przez użytkownika, można wykonać w ramach zaplanowanych okien obsługi. Planowane przestoje usługi, takie jak zaplanowane poprawki, uaktualnienia wersji pomocniczej lub operacje zainicjowane przez użytkownika, takie jak skalowanie obliczeniowe, wiążą się z przestojem. Aby zmniejszyć wpływ aplikacji na zainicjowane przez platformę Azure zadania konserwacji, można zaplanować ich zaplanowanie w ciągu dnia tygodnia i czas, który najmniej ma wpływ na aplikację. 

W przypadku nieplanowanych przestojów, takich jak awaria bazy danych lub awaria serwera, zmieniony serwer zostanie uruchomiony ponownie w ramach tej samej strefy. Chociaż rzadko ma to wpływ na całą strefę, możesz przywrócić bazę danych w innej strefie w regionie. 

## <a name="things-to-know-with-zone-redundancy"></a>Elementy, które należy znać przy użyciu nadmiarowości stref 

Poniżej przedstawiono kilka zagadnień, które należy wziąć pod uwagę w przypadku korzystania z wysokiej dostępności nadmiarowości strefy: 

-   Wysoką dostępność można ustawić **tylko** w czasie elastycznego tworzenia serwera.
-   Wysoka dostępność nie jest obsługiwana w warstwie obliczeniowej z możliwością naliczania.
-   Ze względu na replikację synchroniczną do innej strefy dostępności serwer podstawowej bazy danych może mieć podwyższony czas oczekiwania na zapis i zatwierdzenie.
-   Nie można użyć repliki rezerwowej dla zapytań tylko do odczytu.
-   W zależności od działania na serwerze podstawowym w momencie przejścia w tryb failover może upłynąć nawet 60-120 sekund lub dłużej do ukończenia pracy w trybie failover.
-   Ponowne uruchomienie podstawowego serwera bazy danych w celu pobrania zmian parametrów statycznych spowoduje również ponowne uruchomienie repliki gotowości.
-   Konfigurowanie replik odczytu dla serwerów nadmiarowych o wysokiej dostępności nie jest obsługiwane.
-   Konfigurowanie zadań zarządzania inicjowanych przez klienta nie można zautomatyzować podczas zarządzanego okna obsługi.
-   Planowane zdarzenia, takie jak skalowanie obliczeniowe i wersje pomocnicze, odbywają się jednocześnie zarówno na poziomie podstawowym, jak i w stanie wstrzymania. 


## <a name="next-steps"></a>Następne kroki

-   Informacje o [ciągłości działania firmy](./concepts-business-continuity.md)
-   Więcej informacji na temat [strefy nadmiarowej wysokiej dostępności](./concepts-high-availability.md)
-   Informacje na temat [tworzenia kopii zapasowych i odzyskiwania](./concepts-backup-restore.md)
