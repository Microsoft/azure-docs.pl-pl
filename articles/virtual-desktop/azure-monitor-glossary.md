---
title: Monitorowanie słownika wirtualnego systemu Windows — Azure
description: Słownik terminów i koncepcji związanych z Azure Monitor dla pulpitu wirtualnego systemu Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/29/2021
ms.author: helohr
manager: lizross
ms.openlocfilehash: 38f34324a0cda518645f8b9231e08bafbd75cca0
ms.sourcegitcommit: dae6b628a8d57540263a1f2f1cdb10721ed1470d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "105709535"
---
# <a name="azure-monitor-for-windows-virtual-desktop-glossary"></a>Azure Monitor słownika wirtualnego systemu Windows

W tym artykule wymieniono i krótko opisano najważniejsze pojęcia i koncepcje dotyczące Azure Monitor dla pulpitu wirtualnego systemu Windows (wersja zapoznawcza).

## <a name="alerts"></a>Alerty

Wszystkie aktywne alerty Azure Monitor skonfigurowane w ramach subskrypcji i sklasyfikowane jako [ważność 0](#severity-0-alerts) zostaną wyświetlone na stronie Przegląd. Aby dowiedzieć się, jak skonfigurować alerty, zobacz [reagowanie na zdarzenia przy użyciu alertów Azure monitor](../azure-monitor/alerts/tutorial-response.md).

## <a name="available-sessions"></a>Dostępne sesje

Dostępne sesje przedstawiają liczbę dostępnych sesji w puli hostów. Usługa oblicza tę liczbę przez pomnożenie liczby maszyn wirtualnych (VM) przez maksymalną dozwoloną liczbę sesji na maszynę wirtualną, a następnie odjęcie całkowitej liczby sesji.

## <a name="connection-success"></a>Pomyślne połączenie

Ten element pokazuje kondycję połączenia. "Powodzenie połączenia" oznacza, że połączenie może dotrzeć do hosta, jak zostało potwierdzone przez stos na tej maszynie wirtualnej. Niepowodzenie połączenia oznacza, że połączenie nie mogło nawiązać połączenia z hostem.

## <a name="daily-active-users-dau"></a>Aktywni użytkownicy codziennie (DAU)

Całkowita liczba użytkowników, którzy uruchomili sesję w ciągu ostatnich 24 godzin.

## <a name="daily-alerts"></a>Alerty na dzień

Całkowita liczba alertów wyzwalanych każdego dnia.

## <a name="daily-connections-and-reconnections"></a>Połączenia i ponowne połączenia na dzień

Całkowita liczba połączeń i repołączeń rozpoczętych lub ukończonych w ciągu ostatnich 24 godzin.

## <a name="daily-connected-hours"></a>Godziny połączenia na dzień

Całkowita liczba godzin spędzonych w powiązaniu między użytkownikami w ciągu ostatnich 24 godzin.

## <a name="diagnostics-and-errors"></a>Diagnostyka i błędy

Po pojawieniu się błędu lub alertu w Azure Monitor dla pulpitu wirtualnego systemu Windows jest on kategoryzowany przez trzy rzeczy:

- Typ działania: Ta kategoria zawiera informacje o tym, jak ten błąd jest kategoryzowany przez diagnostykę pulpitu wirtualnego systemu Windows. Kategorie to działania związane z zarządzaniem, kanały informacyjne, połączenia, rejestracje hostów, błędy i punkty kontrolne. Więcej informacji na temat tych kategorii [znajduje się w log Analytics do funkcji diagnostyki](diagnostics-log-analytics.md).

- Rodzaj: Ta kategoria pokazuje lokalizację błędu. 

     - W usłudze pulpitu wirtualnego systemu Windows wystąpią błędy oznaczone jako "usługa" lub "serviceerror = TRUE".
     - Błędy oznaczone jako "Deployment" lub "serviceerror = FALSE" wystąpiły poza usługą pulpitu wirtualnego systemu Windows.
     - Aby dowiedzieć się więcej o tagu serviceerror, zobacz [typowe scenariusze błędów](diagnostics-role-service.md#common-error-scenarios).

- Źródło: Ta kategoria zawiera bardziej szczegółowy opis miejsca wystąpienia błędu.

     - Diagnostyka: rola usługi odpowiedzialna za monitorowanie i raportowanie aktywności usługi, aby umożliwić użytkownikom obserwowanie i zdiagnozowanie problemów z wdrażaniem.

     - RDBroker: rola usługi odpowiedzialna za organizowanie działań związanych z wdrażaniem, obsługa stanu obiektów, sprawdzanie poprawności uwierzytelniania itd.

     - RDGateway: rola usługi odpowiedzialna za obsługę łączności sieciowej między użytkownikami końcowymi i maszynami wirtualnymi.

     - RDStack: składnik oprogramowania zainstalowany na maszynach wirtualnych, aby umożliwić im komunikację z usługą pulpitu wirtualnego systemu Windows.

     - Klient: oprogramowanie uruchomione na komputerze użytkownika końcowego, który udostępnia interfejs usługi pulpitu wirtualnego systemu Windows. Wyświetla listę opublikowanych zasobów i hostuje Pulpit zdalny połączenie po dokonaniu wyboru.

Każdy problem z diagnostyką lub błąd zawiera komunikat wyjaśniający, co poszło źle. Aby dowiedzieć się więcej o rozwiązywaniu problemów, zobacz [Identyfikowanie i diagnozowanie problemów z pulpitem wirtualnym systemu Windows](diagnostics-role-service.md).

## <a name="input-delay"></a>Opóźnienie wejściowe

"Opóźnienie wejścia" w Azure Monitor dla pulpitu wirtualnego systemu Windows oznacza, że opóźnienie wejściowe dla każdego z sesji. Na stronie wydajność hosta w [aka.MS/azmonwvdi](https://portal.azure.com/#blade/Microsoft_Azure_WVD/WvdManagerMenuBlade/workbooks), ten licznik wydajności jest skonfigurowany do wysyłania raportu do usługi co 30 sekund. Te 30-sekundowe interwały są nazywane "przykładami" i raportem najgorszego przypadku w tym oknie. Wartości Media i p95 odzwierciedlają medianę i używany 95. percentyl we wszystkich próbkach.

W obszarze **opóźnienie wejściowe według hosta** możesz wybrać wiersz hosta sesji, aby przefiltrować wszystkie inne wizualizacje na stronie do tego hosta. Możesz również wybrać nazwę procesu, aby odfiltrować Średnie opóźnienie wejścia na wykresie czasu.

Wprowadzamy opóźnienia w następujących kategoriach:

- Dobre: poniżej 150 milisekund.
- Akceptowalne: 150-500 milisekund.
- Niska: 500 – 2000 milisekund (poniżej 2 sekund).
- Złe: ponad 2 000 milisekund (2 sekundy i więcej).

Aby dowiedzieć się więcej o tym, jak działa licznik opóźnień wejściowych, zobacz [liczniki wydajności opóźnienia danych wejściowych użytkownika](/windows-server/remote/remote-desktop-services/rds-rdsh-performance-counters/).

##  <a name="monthly-active-users-mau"></a>Aktywni użytkownicy miesięcznie (MAU, monthly active user)

Całkowita liczba użytkowników, którzy uruchomili sesję w ciągu ostatnich 28 dni. Jeśli dane są przechowywane przez 30 dni lub mniej, podczas okresów, w których dostępne są mniej niż 28 dni, mogą być widoczne wartości MAU i połączenia.

## <a name="performance-counters"></a>Liczniki wydajności

Liczniki wydajności pokazują wydajność składników sprzętowych, systemów operacyjnych i aplikacji.

Poniższa tabela zawiera listę zalecanych liczników wydajności i przedziałów czasu, których Azure Monitor używa dla pulpitu wirtualnego systemu Windows:

|Nazwa licznika wydajności|Przedział czasu|
|---|---|
|Dysk logiczny (C:) \\ średnia długość kolejki dysku|30 sekund|
|Dysk logiczny (C:) \\ Średni czas dysku w s/transfer|60 sekund|
|Dysk logiczny (C:) \\ Bieżąca długość kolejki dysku|30 sekund|
|Pamięć ( \* ) \\ dostępne MB|30 sekund|
|Pamięć ( \* ) \\ błędy stron/s|30 sekund|
|Pamięć ( \* ) \\ strony/s|30 sekund|
|Pamięć ( \* ) \\ % zadeklarowanych bajtów w użyciu|30 sekund|
|DyskFizyczny ( \* ) \\ średnia długość kolejki dysku|30 sekund|
|DyskFizyczny ( \* ) \\ Średni czas dysku w s/odczyt|30 sekund|
|DyskFizyczny ( \* ) \\ Średni czas dysku w s/transfer|30 sekund|
|DyskFizyczny ( \* ) \\ Średni czas dysku w s/zapis|30 sekund|
|Informacje o procesorze (_Total) \\ % czasu procesora|30 sekund|
|\*Aktywne sesje usług terminalowych \\|60 sekund|
|\*Nieaktywne sesje usług \\ terminalowych|60 sekund|
|\* \\ Łączna liczba sesji usług terminalowych|60 sekund|
|\*Opóźnienie wejściowe użytkownika na proces ( \* ) \\ maksymalne opóźnienie wejścia|30 sekund|
|\*Opóźnienie wejściowe użytkownika na sesję ( \* ) \\ maksymalne opóźnienie wejściowe|30 sekund|
|Sieć funkcji RemoteFX ( \* ) \\ bieżący RTT TCP|30 sekund|
|Sieć funkcji RemoteFX ( \* ) \\ Bieżąca przepustowość UDP|30 sekund|

Aby dowiedzieć się więcej o sposobie odczytywania liczników wydajności, zobacz [Konfigurowanie liczników wydajności](../azure-monitor/agents/data-sources-performance-counters.md).

Aby dowiedzieć się więcej o licznikach wydajności opóźnienia wejścia, zobacz [liczniki wydajności opóźnienia danych wejściowych użytkownika](/windows-server/remote/remote-desktop-services/rds-rdsh-performance-counters/).

## <a name="potential-connectivity-issues"></a>Potencjalne problemy z łącznością

Potencjalne problemy z łącznością przedstawiają hosty, użytkowników, opublikowane zasoby i klientów z szybkością niepowodzeń połączenia. Po wybraniu filtru "Raport według" możesz oszacować ważność problemu, sprawdzając wartości w następujących kolumnach:

- Próby (liczba prób połączenia)
- Zasoby (liczba opublikowanych aplikacji lub pulpitów)
- Hosty (liczba maszyn wirtualnych)
- Klienci

Na przykład w przypadku wybrania filtru **według użytkownika** można sprawdzić, czy w kolumnie **próby** są wyświetlane próby połączenia poszczególnych użytkowników.

Jeśli zauważysz, że problem z połączeniem obejmuje wiele hostów, użytkowników, zasobów lub klientów, prawdopodobnie problem ma wpływ na cały system. Jeśli tak nie jest, jest to mniejszy problem o niższym priorytecie.

Możesz również wybrać pozycje, aby wyświetlić dodatkowe informacje. Można wyświetlić, które hosty, zasoby i wersje klientów były związane z problemem. Na ekranie zostaną również wyświetlone wszystkie błędy raportowane podczas próby połączenia.

## <a name="round-trip-time-rtt"></a>Czas błądzenia (RTT)

Czas błądzenia (RTT) to oszacowanie czasu błądzenia połączenia między lokalizacją użytkownika końcowego a regionem świadczenia usługi Azure. Aby zobaczyć, które lokalizacje mają najlepsze opóźnienia, Wyszukaj żądaną lokalizację w [narzędziu szacowania środowisko pulpitu wirtualnego systemu Windows](https://azure.microsoft.com/services/virtual-desktop/assessment/).

## <a name="session-history"></a>Historia sesji

Element **Sessions** pokazuje stan wszystkich sesji, połączonych i rozłączonych. **Sesje bezczynne** są wyświetlane tylko w odłączonych sesjach.

## <a name="severity-0-alerts"></a>Alerty o ważności 0

Najbardziej pilne elementy, które należy wziąć pod uwagę od razu. Jeśli te problemy nie są rozwiązywane, może to spowodować, że wdrożenie pulpitu wirtualnego systemu Windows przestanie działać.

## <a name="time-to-connect"></a>Czas nawiązywania połączenia

Czas do połączenia to czas między rozpoczęciem sesji przez użytkownika a zaliczeniem ich jako zalogowanym do usługi. Ustanowienie nowych połączeń będzie trwać dłużej niż Ponowne ustanawianie istniejących połączeń.

## <a name="user-report"></a>Raport użytkownika

Na stronie raport użytkownika można wyświetlić historię połączeń określonego użytkownika i informacje diagnostyczne. Każdy raport użytkownika przedstawia wzorce użycia, opinie użytkowników i wszelkie błędy napotkane przez użytkowników podczas sesji. Większość mniejszych problemów można rozwiązać z opiniami użytkowników. Jeśli trzeba dig się bardziej szczegółowo, można również filtrować informacje o określonym IDENTYFIKATORze lub okresie.

## <a name="users-per-core"></a>Użytkownicy na rdzeń

Jest to liczba użytkowników w poszczególnych rdzeńch maszyn wirtualnych. Śledzenie maksymalnej liczby użytkowników na rdzeń w miarę upływu czasu może pomóc w ustaleniu, czy środowisko spójnie działa na dużą, niskiej lub fluktuacji liczby użytkowników na rdzeń. Znajomość liczby aktywnych użytkowników pomoże Ci efektywnie zasoby i skalować środowisko.

## <a name="windows-event-logs"></a>Dzienniki zdarzeń systemu Windows

Dzienniki zdarzeń systemu Windows to źródła danych zbierane przez Log Analytics agentów na maszynach wirtualnych z systemem Windows. Można zbierać zdarzenia z dzienników standardowych, takich jak system i aplikacja, a także niestandardowych dzienników tworzonych przez aplikacje, które muszą być monitorowane.

Poniższa tabela zawiera listę wymaganych dzienników zdarzeń systemu Windows dla Azure Monitor dla pulpitu wirtualnego systemu Windows:

|Nazwa zdarzenia|Typ zdarzenia|
|---|---|
|Aplikacja|Błąd i ostrzeżenie|
|Microsoft-Windows-TerminalServices-RemoteConnectionManager/administrator|Błąd, ostrzeżenie i informacje|
|Microsoft-Windows-TerminalServices-LocalSessionManager/operacyjny|Błąd, ostrzeżenie i informacje|
|System|Błąd i ostrzeżenie|
| Microsoft-FSLogix-Apps/Operational|Błąd, ostrzeżenie i informacje|
|Microsoft-FSLogix-Apps/administrator|Błąd, ostrzeżenie i informacje|

Aby dowiedzieć się więcej na temat dzienników zdarzeń systemu Windows, zobacz [Właściwości rekordów zdarzeń systemu Windows](../azure-monitor/agents/data-sources-windows-events.md#configuring-windows-event-logs).

## <a name="next-steps"></a>Następne kroki

Aby rozpocząć pracę z usługą Azure Monitor dla pulpitu wirtualnego systemu Windows, zapoznaj się z następującymi artykułami:

- [Monitorowanie wdrożenia przy użyciu Azure Monitor dla pulpitu wirtualnego systemu Windows](azure-monitor.md)
- [Azure Monitor rozwiązywania problemów z pulpitem wirtualnym systemu Windows](troubleshoot-azure-monitor.md)

Możesz również skonfigurować Azure Advisor, aby pomóc w rozwiązaniu problemu lub uniknąć typowych problemów. Dowiedz się więcej o [użyciu Azure Advisor z pulpitem wirtualnym systemu Windows](azure-advisor.md).

Jeśli potrzebujesz pomocy lub masz pytania, zapoznaj się z naszymi zasobami społeczności:

- Zadawaj pytania lub udzielaj sugestii społeczności w [TechCommunity pulpitu wirtualnego systemu Windows](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop).
   
- Aby dowiedzieć się, jak opuścić opinię, zobacz [Omówienie rozwiązywania problemów, opinie i pomoc techniczną dla pulpitu wirtualnego systemu Windows](troubleshoot-set-up-overview.md#report-issues).

- Możesz również wystawić opinię na temat pulpitu wirtualnego systemu Windows w [centrum opinii pulpitu wirtualnego systemu Windows](https://support.microsoft.com/help/4021566/windows-10-send-feedback-to-microsoft-with-feedback-hub-app)
