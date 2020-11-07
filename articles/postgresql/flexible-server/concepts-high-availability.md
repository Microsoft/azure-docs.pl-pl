---
title: Omówienie nadmiarowej wysokiej dostępności strefy przy użyciu serwera Azure Database for PostgreSQL-elastycznie (wersja zapoznawcza)
description: Dowiedz się więcej o pojęciach nadmiarowej wysokiej dostępności strefy przy użyciu Azure Database for PostgreSQL-elastyczny serwer
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: b23c95ef0005c8246feb8dc32e4a07a0ae19b72f
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2020
ms.locfileid: "94359548"
---
# <a name="high-availability-concepts-in-azure-database-for-postgresql---flexible-server"></a>Koncepcje wysokiej dostępności na serwerze elastycznym Azure Database for PostgreSQL

> [!IMPORTANT]
> Azure Database for PostgreSQL — serwer elastyczny jest w wersji zapoznawczej

Serwer elastyczny Azure Database for PostgreSQL oferuje konfigurację wysokiej dostępności z funkcją automatycznej pracy awaryjnej przy użyciu **nadmiarowego** wdrażania serwera. W przypadku wdrożenia w konfiguracji strefowo nadmiarowej serwer elastyczny automatycznie aprowizuje replikę rezerwową i zarządza nią w innej strefie dostępności. Przy użyciu replikacji Streaming PostgreSQL dane są replikowane do serwera repliki gotowości w trybie **synchronicznym** . 

Konfiguracja nadmiarowa stref umożliwia automatyczne przejście w tryb failover bez utraty danych podczas planowanych zdarzeń, takich jak operacja obliczeniowa skalowania przez użytkownika, a także podczas nieplanowanych zdarzeń, takich jak błędy sprzętu i oprogramowania, awarie sieci i niepowodzenia strefy dostępności. 

:::image type="content" source="./media/business-continuity/concepts-zone-redundant-high-availability-architecture.png" alt-text="Strefa nadmiarowa wysokiej dostępności"::: 

## <a name="zone-redundant-high-availability-architecture"></a>Architektura nadmiarowej wysokiej dostępności strefy

Możesz wybrać region i strefę dostępności, aby wdrożyć podstawowy serwer bazy danych. Serwer repliki rezerwowej jest aprowizowany w innej strefie dostępności z taką samą konfiguracją, jak serwer podstawowy, z uwzględnieniem warstwy obliczeniowej, rozmiaru zasobów obliczeniowych, rozmiaru magazynu i konfiguracji sieci. Dzienniki transakcji są replikowane w trybie synchronicznym do repliki gotowości przy użyciu replikacji PostgreSQL Streaming. Automatyczne kopie zapasowe są wykonywane okresowo z podstawowego serwera bazy danych, podczas gdy dzienniki transakcji są ciągle archiwizowane w magazynie kopii zapasowych z repliki gotowości. 

Kondycja konfiguracji wysokiej dostępności jest stale monitorowana i raportowana w portalu. Poniżej wymieniono Stany nadmiarowej wysokiej dostępności strefy:

| **Stan** | **Opis** |
| ------- | ------ |
| <b> Inicjacj | W procesie tworzenia nowego serwera rezerwy |
| <b> Replikowanie danych | Po utworzeniu stanu gotowości jest on rzutowany na podstawowy. |
| <b> Dobrej kondycji | Replikacja jest w stałym stanie i w dobrej kondycji. |
| <b> Przechodzenie w tryb failover | Serwer bazy danych jest w trakcie przełączenia w tryb failover. |
| <b> Usuwanie wstrzymania | W procesie usuwania serwera rezerwy. | 
| <b> Niewłączony | Strefa nadmiarowa wysokiej dostępności nie jest włączona.  |

## <a name="steady-state-operations"></a>Operacje o stałym stanie

PostgreSQL aplikacje klienckie są połączone z serwerem podstawowym przy użyciu nazwy serwera bazy danych. Odczyty aplikacji są obsługiwane bezpośrednio z serwera podstawowego, podczas gdy zatwierdzenia i zapisy są potwierdzane do aplikacji dopiero po utrwaleniu danych zarówno na serwerze podstawowym, jak i w replice gotowości. Ze względu na to, że to dodatkowe wymaganie dotyczące rundy, aplikacje mogą oczekiwać na podwyższony czas oczekiwania na zapisy i zatwierdzenia. Możesz monitorować kondycję wysokiej dostępności w portalu.

:::image type="content" source="./media/business-continuity/concepts-high-availability-steady-state.png" alt-text="Strefa nadmiarowa wysokiej dostępności — stan stały"::: 

1. Klienci łączą się z serwerem elastycznym i wykonują operacje zapisu.
2. Zmiany są replikowane do lokacji w stanie wstrzymania.
3. Podstawowe potwierdzenie odbioru.
4. Zapisy/zatwierdzenia są potwierdzane.

## <a name="failover-process---planned-downtimes"></a>Proces trybu failover — planowane przestoje

Zdarzenia planowanych przestojów obejmują zaplanowane okresowe aktualizacje oprogramowania platformy Azure oraz uaktualnienia wersji pomocniczej. W przypadku skonfigurowania wysokiej dostępności te operacje są najpierw stosowane do repliki gotowości, podczas gdy aplikacje nadal uzyskują dostęp do serwera podstawowego. Po zaktualizowaniu repliki w celu przełączenia na serwer podstawowy są opróżniane i zostanie wyzwolone przejście w tryb failover, która aktywuje replikę rezerwy jako podstawową z tą samą nazwą serwera bazy danych. Aplikacje klienckie będą musiały ponownie nawiązać połączenie z tą samą nazwą serwera bazy danych na nowym serwerze podstawowym i mogą wznowić ich operacje. Nowy serwer rezerwy zostanie ustanowiony w tej samej strefie co stary element podstawowy. 

W przypadku innych operacji zainicjowanych przez użytkownika, takich jak skalowanie w poziomie lub magazyn skalowania, zmiany są stosowane najpierw w stanie wstrzymania, a następnie podstawowym. Obecnie połączenia nie przełączają się do trybu failover i w związku z tym przerywają czas przestoju, gdy operacja jest przeprowadzana na serwerze podstawowym.

### <a name="reducing-planned-downtime-with-managed-maintenance-window"></a>Zmniejszanie planowanego przestoju przy użyciu zarządzanego okna obsługi

 Możesz zaplanować działania konserwacyjne zainicjowane przez platformę Azure, wybierając 30-minutowy okno w ciągu dnia preferencji, w którym oczekuje się, że działania w bazach danych będą niskie. Zadania konserwacji platformy Azure, takie jak poprawki lub wersje pomocnicze, zostaną wykonane w tym oknie.  W przypadku elastycznych serwerów skonfigurowanych pod kątem wysokiej dostępności te działania konserwacyjne są wykonywane najpierw w replice gotowości, a następnie aktywowane. Aplikacje następnie ponownie nawiązują połączenie z nowym serwerem podstawowym i wznawiają ich operacje podczas aprowizacji nowego stanu wstrzymania.

## <a name="failover-process---unplanned-downtimes"></a>Proces trybu failover — nieplanowane przestoje

Nieplanowane przestoje obejmują błędy oprogramowania lub składniki infrastruktury mające wpływ na dostępność bazy danych. W przypadku wykrycia niedostępności serwera w systemie monitorowania replikacja do repliki w stanie wstrzymania jest poważna, a replika rezerwy jest aktywowana jako podstawowy serwer bazy danych. Klienci mogą ponownie połączyć się z serwerem bazy danych przy użyciu tych samych parametrów połączenia i wznowić ich operacje. Oczekiwany czas pracy w trybie failover wynosi 60 120s. Jednak w zależności od działania w podstawowym serwerze baz danych w czasie pracy w trybie failover, takim jak duże transakcje i czas odzyskiwania, praca awaryjna może trwać dłużej.

:::image type="content" source="./media/business-continuity/concepts-high-availability-failover-state.png" alt-text="Strefa nadmiarowa wysokiej dostępności — tryb failover"::: 

1. Serwer podstawowej bazy danych nie działa, a klienci utracą łączność z bazą danych. 
2. Serwer rezerwy został aktywowany, aby stał się nowym serwerem podstawowym. Klient nawiązuje połączenie z nowym serwerem podstawowym przy użyciu tych samych parametrów połączenia. Posiadanie aplikacji klienckiej w tej samej strefie co serwer podstawowej bazy danych zmniejsza opóźnienia i zwiększa wydajność.
3. Serwer rezerwy jest ustanowiony w tej samej strefie co stary serwer podstawowy i replikacja przesyłania strumieniowego jest inicjowana. 
4. Po ustanowieniu replikacji o stałym stanie aplikacja kliencka zatwierdzeń i zapisów zostanie potwierdzone po utrwaleniu danych w lokacjach.

## <a name="point-in-time-restore"></a>Przywracanie do określonego momentu 

Elastyczne serwery z konfiguracją wysokiej dostępności umożliwiają replikację danych w czasie rzeczywistym do serwera rezerwowego, aby zapewnić aktualność. Wszelkie błędy użytkownika na serwerze podstawowym, takie jak przypadkowe usunięcie tabeli lub nieprawidłowe aktualizacje danych, są wiernie replikowane do repliki w stanie wstrzymania. W związku z tym nie można użyć funkcji wstrzymywania do odzyskania z takich błędów logicznych. Aby odzyskać te błędy, należy wykonać przywracanie do punktu w czasie z kopii zapasowych.  Korzystając z funkcji przywracania do punktu w czasie, można przywrócić do czasu przed wystąpieniem błędu. W przypadku baz danych skonfigurowanych pod kątem wysokiej dostępności nowy serwer bazy danych zostanie przywrócony jako serwer elastyczny o pojedynczej strefie z nazwą dostarczoną przez użytkownika. Następnie można wyeksportować obiekt z serwera bazy danych i zaimportować go do produkcyjnego serwera bazy danych. Podobnie, jeśli chcesz sklonować serwer bazy danych na potrzeby testowania i programowania, lub chcesz przywrócić do innych celów, możesz wykonać przywracanie do punktu w czasie.

## <a name="zone-redundant-high-availability---features"></a>Strefa nadmiarowa wysokiej dostępności — funkcje

-   Replika wstrzymywania zostanie wdrożona w dokładnej konfiguracji maszyny wirtualnej, takiej jak serwer podstawowy, w tym rdzeni wirtualnych, Storage, ustawienia sieci (Sieć wirtualna, Zapora) itp.

-   Możliwość dodania wysokiej dostępności dla istniejącego serwera baz danych.

-   Możliwość usunięcia repliki rezerwy poprzez wyłączenie wysokiej dostępności.

-   Możliwość wybrania strefy dostępności dla podstawowego serwera bazy danych.

-   Możliwość zatrzymywania, uruchamiania i ponownego uruchamiania podstawowych i rezerwowych serwerów baz danych.

-   Automatyczne kopie zapasowe są wykonywane z serwera podstawowej bazy danych i przechowywane w magazynie strefowo nadmiarowy.

-   Klienci zawsze łączą się z serwerem podstawowej bazy danych.

-   Możliwość ponownego uruchomienia serwera w celu pobrania wszelkich zmian parametrów serwera statycznego.
  
-   Okresowe działania konserwacyjne, takie jak uaktualnienia wersji pomocniczej, są najpierw wykonywane w stanie wstrzymania, a usługa została przełączona w tryb failover, aby zmniejszyć przestoje  

## <a name="zone-redundant-high-availability---limitations"></a>Strefa nadmiarowa wysokiej dostępności — ograniczenia

-   Wysoka dostępność nie jest obsługiwana w warstwie obliczeniowej z możliwością naliczania.
-   Wysoka dostępność jest obsługiwana tylko w regionach, w których jest dostępnych wiele stref.
-   Ze względu na replikację synchroniczną do innej strefy dostępności aplikacje mogą mieć podwyższony czas oczekiwania na zapis i zatwierdzenie.

-   Nie można użyć repliki rezerwowej dla zapytań tylko do odczytu.

-   W zależności od działania na serwerze podstawowym w momencie przejścia w tryb failover może upłynąć nawet dwie minuty lub dłużej, aby można było ukończyć pracę w trybie failover.

-   Ponowne uruchomienie podstawowego serwera bazy danych w celu pobrania zmian parametrów statycznych spowoduje również ponowne uruchomienie repliki gotowości.

-   Konfigurowanie dodatkowych replik odczytu nie jest obsługiwane.

-   Konfigurowanie zadań zarządzania inicjowanych przez klienta nie można zaplanować podczas zarządzanego okna obsługi.

-   Zdarzenia planowane, takie jak skalowanie zasobów obliczeniowych i skalowanie magazynu, najpierw odbywają się na serwerze rezerwowym, a następnie na serwerze podstawowym. Usługa nie jest przełączana w tryb failover. 

-  W przypadku skonfigurowania logicznego dekodowania lub replikacji logicznej przy użyciu serwera elastycznego z konfiguracją HA w przypadku przejścia w tryb failover do serwera rezerwowego gniazda replikacji logicznej nie są kopiowane do serwera rezerwowego.  

## <a name="next-steps"></a>Następne kroki

-   Informacje o [ciągłości działania firmy](./concepts-business-continuity.md)
-   Dowiedz się, jak [zarządzać wysoką dostępnością](./how-to-manage-high-availability-portal.md)
-   Informacje na temat [tworzenia kopii zapasowych i odzyskiwania](./concepts-backup-restore.md)