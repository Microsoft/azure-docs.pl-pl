---
title: Omówienie strefowo nadmiarowej wysokiej dostępności z Azure Database for MySQL elastycznym
description: Poznaj pojęcia związane z strefowo nadmiarową wysoką dostępnością z Azure Database for MySQL elastycznym
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 01/29/2021
ms.openlocfilehash: 5b5e1491d7f76cd4cff76d0c9a1af4daa49fa483
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107813006"
---
# <a name="high-availability-concepts-in-azure-database-for-mysql-flexible-server-preview"></a>Pojęcia dotyczące wysokiej dostępności w Azure Database for MySQL elastycznym (wersja zapoznawcza)

> [!IMPORTANT] 
> Azure Database for MySQL — serwer elastyczny jest obecnie w publicznej wersji zapoznawczej.

Azure Database for MySQL elastyczny serwer (wersja zapoznawcza) umożliwia konfigurowanie wysokiej dostępności z automatycznym trybem failover przy użyciu opcji **strefowo nadmiarowej** wysokiej dostępności. W przypadku wdrożenia w konfiguracji strefowo nadmiarowej serwer elastyczny automatycznie aprowizuje replikę rezerwową i zarządza nią w innej strefie dostępności. Przy użyciu replikacji na poziomie magazynu dane są **replikowane synchronicznie** na serwer rezerwowy w strefie pomocniczej, aby umożliwić zerowy spadek danych po pracy w trybie failover. Trybu failover jest w pełni niewidoczny dla aplikacji klienckiej i nie wymaga żadnych akcji użytkownika. Serwer rezerwowy nie jest dostępny dla żadnych operacji odczytu lub zapisu, ale jest rezerwą pasywną w celu włączenia szybkiego trybu failover. Czasy pracy w przypadku trybu failover zazwyczaj wahają się od 60 do 120 sekund.

Konfiguracja strefowo nadmiarowej wysokiej dostępności umożliwia automatyczne pracę w trybie failover podczas planowanych zdarzeń, takich jak zainicjowane przez użytkownika operacje obliczeniowe na dużą skalę, oraz nieplanowane zdarzenia, takie jak podstawowe błędy sprzętu i oprogramowania, awarie sieci, a nawet awarie stref dostępności.

:::image type="content" source="./media/concepts-high-availability/1-flexible-server-overview-zone-redundant-ha.png" alt-text="widok strefowo nadmiarowej wysokiej dostępności":::

## <a name="zone-redundancy-architecture"></a>Architektura nadmiarowości strefy

Serwer podstawowy jest wdrażany w regionie i określonej strefie dostępności. W przypadku wyboru wysokiej dostępności automatycznie wdrażany jest serwer repliki rezerwowej z taką samą konfiguracją jak serwer podstawowy, w tym warstwa obliczeniowa, rozmiar obliczeniowy, rozmiar magazynu i konfiguracja sieci. Dane dziennika są replikowane synchronicznie do repliki rezerwowej w celu zapewnienia zerowej utraty danych w przypadku awarii. Automatyczne kopie zapasowe, zarówno migawki, jak i kopie zapasowe dzienników, są wykonywane z podstawowego serwera bazy danych. 

Kondycja ha ha jest stale monitorowana i raportowany na stronie przeglądu.

Poniżej wymieniono różne stany replikacji:

| **Stan** | **Opis** |
| :----- | :------ |
| <b>NotEnabled | Strefowo nadmiarowa ha nie jest włączona |
| <b>CreatingStandby | W procesie tworzenia nowego stanu wstrzymania |
| <b>ReplicatingData | Po utworzeniu rezerwy nadrobić zaległości z serwerem podstawowym. |
| <b>Pracy awaryjnej | Serwer bazy danych jest w trakcie pracy w trybie pracy w trybie pracy w trybie online z serwera podstawowego do rezerwowego. |
| <b>Dobra kondycja | Strefowo nadmiarowa ha jest w stanie stabilnym i w dobrej kondycji. |
| <b>RemovingStandby | W oparciu o akcję użytkownika replika rezerwowa jest w trakcie usuwania.| 

## <a name="advantages"></a>Zalety

Oto niektóre zalety korzystania z funkcji ha ha nadmiarowości strefy: 

- Replika rezerwowa jest wdrażana w dokładnie takiej samej konfiguracji maszyny wirtualnej jak podstawowa, takiej jak rdzenie wirtualne, magazyn, ustawienia sieciowe (sieć wirtualna, zapora) itp.
- Możliwość usunięcia repliki rezerwowej przez wyłączenie wysokiej dostępności.
- Automatyczne kopie zapasowe są oparte na migawkach, wykonywane z podstawowego serwera bazy danych i przechowywane w magazynie strefowo nadmiarowym.
- W przypadku pracy w trybie failover Azure Database for MySQL serwer elastyczny automatycznie przechodzi w tryb failover do repliki rezerwowej, jeśli jest włączona wysoka dostępność. Konfiguracja wysokiej dostępności monitoruje serwer podstawowy i powraca do trybu online.
- Klienci zawsze łączą się z podstawowym serwerem bazy danych.
- W przypadku awarii bazy danych lub awarii węzła próba ponownego uruchomienia jest podejmowana najpierw w tym samym węźle. Jeśli to się nie powiedzie, zostanie wyzwolony automatyczny tryb failover.
- Możliwość ponownego uruchomienia serwera w celu pobrania wszelkich zmian parametrów serwera statycznego.

## <a name="steady-state-operations"></a>Operacje w stanie stabilnym

Aplikacje są połączone z serwerem podstawowym przy użyciu nazwy serwera bazy danych. Informacje o repliki rezerwowej nie są udostępniane w celu uzyskania bezpośredniego dostępu. Zatwierdzenia i zapis są potwierdzane w aplikacji tylko wtedy, gdy pliki dziennika są utrwalane zarówno na dysku serwera podstawowego, jak i w replice rezerwowej w sposób synchroniczny. Ze względu na to dodatkowe wymaganie dotyczące rundy aplikacje mogą oczekiwać podwyższonego opóźnienia operacji zapisu i zatwierdzeń. Kondycję wysokiej dostępności można monitorować w portalu.

## <a name="failover-process"></a>Proces trybu failover 
Aby zapewnić ciągłość działalności biznesowej, musisz mieć proces trybu failover dla planowanych i nieplanowanych zdarzeń. 

### <a name="planned-events"></a>Zdarzenia planowane

Zdarzenia planowanych przestojów obejmują działania zaplanowane przez platformę Azure, takie jak okresowe aktualizacje oprogramowania, uaktualnienia wersji pomocniczej lub inicjowane przez klientów, takie jak skalowanie operacji obliczeniowych i skalowania magazynu. Wszystkie te zmiany są najpierw stosowane do repliki rezerwowej. W tym czasie aplikacje nadal mają dostęp do serwera podstawowego. Po zaktualizowaniu repliki rezerwowej połączenia serwera podstawowego są opróżniane, wyzwalany jest tryb failover, który aktywuje replikę rezerwową jako replikę podstawową o tej samej nazwie serwera bazy danych przez zaktualizowanie rekordu DNS. Połączenia klientów są rozłączone i muszą ponownie nawiązać połączenie i mogą wznowić swoje operacje. Nowy serwer rezerwowy jest ustalany w tej samej strefie co stary serwer podstawowy. Łączny czas pracy w przypadku trybu failover powinien wynosić 60–120 s. 

>[!NOTE]
> W przypadku operacji skalowania obliczeń skalujemy pomocniczy serwer repliki, po którym następuje serwer podstawowy. Nie jest zaangażowane żadne trybu failover.

### <a name="failover-process---unplanned-events"></a>Proces trybu failover — zdarzenia nieplanowane
Nieplanowane przestoje usługi obejmują błędy oprogramowania, które lub błędy infrastruktury, takie jak obliczenia, sieć, awarie magazynu lub awarie zasilania, mają wpływ na dostępność bazy danych. W przypadku niedostępności bazy danych replikacja do repliki rezerwowej zostanie zerowana, a replika rezerwowa zostanie aktywowana jako podstawowa baza danych. System DNS jest aktualizowany, a klienci ponownie nawiązają połączenie z serwerem bazy danych i wznawiają swoje operacje. Łączny czas pracy w przypadku trybu failover powinien potrwać 60–120 s. Jednak w zależności od aktywności na podstawowym serwerze bazy danych w czasie pracy w trybie failover, takiej jak duże transakcje i czas odzyskiwania, tryb failover może trwać dłużej.

### <a name="forced-failover"></a>Wymuszone trybu failover
Azure Database for MySQL wymuszonego trybu failover umożliwia ręczne wymuś trybu failover, co umożliwia przetestowanie funkcji ze scenariuszami aplikacji i pomaga przygotować się na wypadek awarii. Wymuszone przełączenie w tryb failover powoduje przełączenie serwera rezerwowego na serwer podstawowy przez wyzwolenie trybu failover, które aktywuje rezerwową replikę, aby stała się serwerem podstawowym o tej samej nazwie serwera bazy danych przez zaktualizowanie rekordu DNS. Oryginalny serwer podstawowy zostanie ponownie uruchomiony i przełączony do repliki rezerwowej. Połączenia klienta są rozłączone i muszą zostać ponownie nawiązać połączenie, aby wznowić swoje operacje. W zależności od bieżącego obciążenia i ostatniego punktu kontrolnego będzie mierzony całkowity czas pracy w awarii. Ogólnie rzecz biorąc, oczekiwana jest od 60 do 120 s.

## <a name="schedule-maintenance-window"></a>Okno konserwacji harmonogramu 

Serwery elastyczne oferują możliwość planowania konserwacji, w ramach której można wybrać 30-minutowe okno w jeden dzień zgodnie z preferencjami, podczas którego będzie odbywać się konserwacja platformy Azure, taka jak stosowanie poprawek lub uaktualnienia wersji pomocniczej. W przypadku serwerów elastycznych skonfigurowanych z wysoką dostępnością te działania konserwacyjne są najpierw wykonywane na replice rezerwowej. 

## <a name="point-in-time-restore"></a>Przywracanie do określonego momentu 
Ponieważ serwer elastyczny jest skonfigurowany w wysokiej dostępności synchronicznie replikuje dane, serwer rezerwowy jest aktualny z serwerem podstawowym. Wszelkie błędy użytkownika w podstawowym — takie jak przypadkowe usunięcie tabeli lub nieprawidłowe aktualizacje są replikowane do stanu wstrzymania. W związku z tym nie można użyć rezerwy, aby odzyskać dane po takich błędach logicznych. Aby odzyskać dane po tych błędach logicznych, należy wykonać przywracanie do punktu w czasie bezpośrednio przed jego wystąpieniam. Korzystając z funkcji przywracania do punktu w czasie serwera elastycznego, podczas przywracania bazy danych skonfigurowanej z wysoką dostępnością nowy serwer bazy danych jest przywracany jako nowy serwer elastyczny o nazwie podanej przez użytkownika. Następnie możesz wyeksportować obiekt z serwera bazy danych i zaimportować go na serwer produkcyjnej bazy danych. Podobnie, jeśli chcesz sklonować serwer bazy danych w celach testowych i programistów lub chcesz przywrócić do innych celów, możesz wybrać najnowszy punkt przywracania lub niestandardowy punkt przywracania. Operacja przywracania tworzy serwer elastyczny z jedną strefą.

## <a name="mitigate-downtime"></a>Ograniczanie przestoju 
Jeśli nie używasz funkcji nadmiarowości strefy HA, nadal musisz mieć możliwość ograniczenia przestoju aplikacji. W ramach zaplanowanych okien obsługi można zaplanować przestoje usługi, takie jak zaplanowane poprawki, uaktualnienia wersji pomocniczej lub operacje inicjowane przez użytkownika. Planowane przestoje usługi, takie jak zaplanowane poprawki, uaktualnienia wersji pomocniczej lub operacje inicjowane przez użytkownika, takie jak skalowanie zasobów obliczeniowych, są przestojem. Aby ograniczyć wpływ aplikacji na zadania konserwacji inicjowane przez platformę Azure, możesz zaplanować je w ciągu dnia tygodnia i o godzinie, co najmniej wpływa na aplikację. 

W przypadku nieplanowanych zdarzeń przestojów, takich jak awaria bazy danych lub awaria serwera, na którym ma to wpływ, serwer jest ponownie uruchamiany w tej samej strefie. Mimo że występuje to rzadko, jeśli ma to wpływ na całą strefę, możesz przywrócić bazę danych w innej strefie w regionie. 

## <a name="things-to-know-with-zone-redundancy"></a>Co należy wiedzieć z nadmiarowością strefy 

Poniżej podano kilka kwestii, które należy wziąć pod uwagę w przypadku korzystania z wysokiej dostępności nadmiarowości strefy: 

- Wysoką dostępność można **ustawić** tylko podczas elastycznego czasu tworzenia serwera.
- Wysoka dostępność nie jest obsługiwana w warstwie obliczeniowej z możliwością serii.
- Z powodu replikacji synchronicznej do innej strefy dostępności podstawowy serwer bazy danych może mieć podwyższony poziom opóźnienia zapisu i zatwierdzania.
- Repliki rezerwowej nie można używać w przypadku zapytań tylko do odczytu.
- W zależności od aktywności na serwerze podstawowym w czasie pracy w awarii ukończenie trybu failover może potrwać do 60–120 sekund lub dłużej.
- Ponowne uruchomienie podstawowego serwera bazy danych w celu pobrania zmian parametrów statycznych powoduje również ponowne uruchomienie repliki rezerwowej.
- Konfigurowanie replik do odczytu dla strefowo nadmiarowych serwerów wysokiej dostępności nie jest obsługiwane.
- Konfigurowanie zadań zarządzania zainicjowanych przez klienta nie może być zautomatyzowane podczas zarządzanego okna obsługi.
- Planowane zdarzenia, takie jak skalowanie obliczeń i uaktualnienia wersji pomocniczej, są w tym samym czasie zarówno w trybie podstawowym, jak i rezerwy. 


## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej [o ciągłości działania](./concepts-business-continuity.md)
- Dowiedz się więcej o [strefowo nadmiarowej wysokiej dostępności](./concepts-high-availability.md)
- Dowiedz się więcej o [kopii zapasowej i odzyskiwaniu](./concepts-backup-restore.md)
