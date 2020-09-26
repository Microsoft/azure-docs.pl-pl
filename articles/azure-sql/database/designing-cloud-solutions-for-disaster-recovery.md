---
title: Projektowanie usług dostępnych globalnie
description: Zapoznaj się z tematem projektowanie aplikacji dla usług o wysokiej dostępności przy użyciu Azure SQL Database.
keywords: odzyskiwanie po awarii w chmurze, rozwiązania odzyskiwania po awarii, tworzenie kopii zapasowych danych aplikacji, replikacja geograficzna, Planowanie ciągłości biznesowej
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: sstein
ms.date: 07/28/2020
ms.openlocfilehash: be632ba06edc858e7eadcd6e57a4f7769f69f2cb
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91321683"
---
# <a name="designing-globally-available-services-using-azure-sql-database"></a>Projektowanie usług dostępnych globalnie przy użyciu Azure SQL Database
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Podczas kompilowania i wdrażania usług w chmurze przy użyciu Azure SQL Database można użyć [aktywnej replikacji geograficznej](active-geo-replication-overview.md) lub [grup autotrybu failover](auto-failover-group-overview.md) w celu zapewnienia odporności na awarie regionalne i błędy krytyczne. Ta sama funkcja umożliwia tworzenie globalnie rozproszonych aplikacji zoptymalizowanych pod kątem lokalnego dostępu do danych. W tym artykule omówiono typowe wzorce aplikacji, w tym zalety i wady poszczególnych opcji.

> [!NOTE]
> Jeśli używasz baz danych Premium lub Krytyczne dla działania firmy i pul elastycznych, możesz je odporne na awarie regionalne, konwertując je na konfigurację nadmiarowego wdrożenia strefy. Zobacz [bazy danych strefowo nadmiarowe](high-availability-sla.md).  

## <a name="scenario-1-using-two-azure-regions-for-business-continuity-with-minimal-downtime"></a>Scenariusz 1. Korzystanie z dwóch regionów świadczenia usługi Azure w celu zapewnienia ciągłości biznesowej z minimalnym czasem przestoju

W tym scenariuszu aplikacje mają następującą charakterystykę:

* Aplikacja jest aktywna w jednym regionie platformy Azure
* Wszystkie sesje bazy danych wymagają dostępu do odczytu i zapisu (RW) do danych
* Warstwa sieci Web i warstwa danych muszą być rozmieszczone w celu ograniczenia opóźnienia i kosztu ruchu
* W zasadniczy sposób przestój jest wyższym ryzykiem biznesowym w przypadku tych aplikacji niż utrata danych

W takim przypadku topologia wdrażania aplikacji jest zoptymalizowana pod kątem obsługi awarii regionalnych, gdy wszystkie składniki aplikacji muszą przełączać się w tryb failover. Na poniższym diagramie przedstawiono topologię. W przypadku nadmiarowości geograficznej zasoby aplikacji są wdrażane w regionie A i B. Jednak zasoby w regionie B nie są wykorzystywane do momentu awarii regionu A. Grupa trybu failover jest konfigurowana między dwoma regionami w celu zarządzania łącznością z bazą danych, replikacją i trybem failover. Usługa sieci Web w obu regionach jest skonfigurowana do uzyskiwania dostępu do bazy danych za pośrednictwem trybu failover odbiornika do odczytu i zapisu ** &lt; -Group-name &gt; . Database.Windows.NET** (1). Usługa Azure Traffic Manager jest skonfigurowana do używania [metody routingu priorytetowego](../../traffic-manager/traffic-manager-configure-priority-routing-method.md) (2).  

> [!NOTE]
> [Usługa Azure Traffic Manager](../../traffic-manager/traffic-manager-overview.md) jest używana w tym artykule wyłącznie na potrzeby ilustracji. Można użyć dowolnego rozwiązania równoważenia obciążenia, które obsługuje metodę routingu priorytetowego.

Na poniższym diagramie przedstawiono tę konfigurację przed awarią:

![Scenariusz 1. Konfiguracja przed awarią.](./media/designing-cloud-solutions-for-disaster-recovery/scenario1-a.png)

Po awarii w regionie podstawowym SQL Database wykryje, że podstawowa baza danych nie jest dostępna i wyzwala tryb failover w regionie pomocniczym na podstawie parametrów zasad automatycznych trybu failover (1). W zależności od umowy SLA aplikacji można skonfigurować okres prolongaty, który kontroluje czas między wykryciem przestoju i pracą w trybie failover. Istnieje możliwość, że usługa Azure Traffic Manager inicjuje przejście w tryb failover punktu końcowego przed rozpoczęciem pracy w trybie failover bazy danych przez grupę trybu failover. W takim przypadku aplikacja sieci Web nie może natychmiast ponownie nawiązać połączenia z bazą danych. Jednak ponowne połączenia będą automatycznie kończyć się powodzeniem zaraz po zakończeniu pracy w trybie failover bazy danych. Po przywróceniu i ponownym przełączeniu regionu zakończonego niepowodzeniem stary podstawowy automatycznie ponownie nawiąże połączenie jako nowe pomocnicze. Poniższy diagram ilustruje konfigurację po przejściu do trybu failover.

> [!NOTE]
> Wszystkie transakcje przekazane po przejściu do trybu failover zostaną utracone podczas ponownego nawiązywania połączenia. Po zakończeniu pracy w trybie failover aplikacja w regionie B będzie mogła ponownie nawiązać połączenie i ponownie przetworzyć żądania użytkownika. Zarówno aplikacja sieci Web, jak i podstawowa baza danych znajdują się teraz w regionie B i pozostają w tym samym miejscu.

![Scenariusz 1. Konfiguracja po przejściu do trybu failover](./media/designing-cloud-solutions-for-disaster-recovery/scenario1-b.png)

Jeśli wystąpi awaria w regionie B, proces replikacji między podstawową i pomocniczą bazą danych zostanie zawieszony, ale połączenie między nimi pozostaje nienaruszone (1). Traffic Manager wykrywa, że łączność z regionem B jest uszkodzona i oznacza aplikację sieci Web punktu końcowego 2 jako obniżoną (2). W tym przypadku nie ma to wpływu na wydajność aplikacji, ale baza danych zostanie ujawniona i w związku z tym w większym stopniu ryzyko utraty danych w regionie przypadku kończy się niepowodzeniem.

> [!NOTE]
> W przypadku odzyskiwania po awarii zalecamy konfigurację z wdrożeniem aplikacji ograniczoną do dwóch regionów. Wynika to z faktu, że większość lokalizacje geograficzne platformy Azure ma tylko dwa regiony. Ta konfiguracja nie chroni aplikacji przed równoczesnym uszkodzeniem obu regionów. W mało prawdopodobnym wystąpieniu tego błędu można odzyskać bazy danych w trzecim regionie przy użyciu [operacji przywracania geograficznego](disaster-recovery-guidance.md#recover-using-geo-restore).
>

 Gdy awaria zostanie wyeliminowana, pomocnicza baza danych automatycznie ponownie zsynchronizuje się z serwerem podstawowym. W trakcie synchronizacji można mieć wpływ na wydajność podstawowej. Konkretny wpływ zależy od ilości danych uzyskanych przez nowy podstawowy od przejścia w tryb failover. 

> [!NOTE]
> Po ograniczeniu przestojów Traffic Manager rozpocznie routing połączeń z aplikacją w regionie A jako punkt końcowy o wyższym priorytecie. Jeśli zamierzasz zachować podstawową w regionie B przez pewien czas, należy odpowiednio zmienić tabelę priorytetów w profilu możliwe Manager. 
>
 
 Na poniższym diagramie przedstawiono awarię w regionie pomocniczym:

![Scenariusz 1. Konfiguracja po awarii w regionie pomocniczym.](./media/designing-cloud-solutions-for-disaster-recovery/scenario1-c.png)

Kluczowe **zalety** tego wzorca projektowego są następujące:

* Ta sama aplikacja sieci Web jest wdrażana w obu regionach bez żadnej konfiguracji specyficznej dla regionu i nie wymaga dodatkowej logiki do zarządzania pracą w trybie failover.
* Tryb failover nie ma wpływu na wydajność aplikacji, ponieważ aplikacja sieci Web i baza danych są zawsze zlokalizowane.

Głównym **kompromisem** jest to, że zasoby aplikacji w regionie B są bezterminowo wykorzystywane.

## <a name="scenario-2-azure-regions-for-business-continuity-with-maximum-data-preservation"></a>Scenariusz 2. regiony platformy Azure na potrzeby ciągłości działania z maksymalnym zachowaniem danych

Ta opcja jest najbardziej przydatna w przypadku aplikacji o następujących cechach:

* Wszelkie utraty danych to duże ryzyko biznesowe. Trybu failover bazy danych można używać tylko jako ostatniej, jeśli awaria jest spowodowana przez Katastrofalny błąd.
* Aplikacja obsługuje tryby operacji tylko do odczytu i odczytu i zapisu oraz może działać w trybie tylko do odczytu w danym okresie czasu.

W tym wzorcu aplikacja przełącza się do trybu tylko do odczytu, gdy połączenia do odczytu i zapisu zaczynają otrzymywać błędy limitu czasu. Aplikacja sieci Web jest wdrażana w obu regionach i zawiera połączenie z punktem końcowym odbiornika odczytu i zapisu oraz innym połączeniem z punktem końcowym odbiornika tylko do odczytu (1). Profil Traffic Manager powinien korzystać z [priorytetowego routingu](../../traffic-manager/traffic-manager-configure-priority-routing-method.md). [Monitorowanie punktu końcowego](../../traffic-manager/traffic-manager-monitoring.md) powinno być włączone dla punktu końcowego aplikacji w każdym regionie (2).

Na poniższym diagramie przedstawiono tę konfigurację przed awarią:

![Scenariusz 2. Konfiguracja przed awarią.](./media/designing-cloud-solutions-for-disaster-recovery/scenario2-a.png)

Gdy Traffic Manager wykryje Niepowodzenie połączenia z regionem A, automatycznie przełączy ruch użytkownika do wystąpienia aplikacji w regionie B. W tym wzorcu ważne jest, aby ustawić okres prolongaty z utratą danych na wystarczająco wysoką wartość, na przykład 24 godziny. Gwarantuje to, że utrata danych jest niedostępna, jeśli w tym czasie czas przestoju zostanie skorygowany. Po aktywowaniu przez aplikację sieci Web w regionie B operacje odczytu i zapisu kończą się niepowodzeniem. W tym momencie należy przełączyć się do trybu tylko do odczytu (1). W tym trybie żądania są automatycznie kierowane do pomocniczej bazy danych. Jeśli awaria jest spowodowana przez Katastrofalny błąd, prawdopodobnie nie można rozwiązać tego problemu w okresie prolongaty. Po jego wygaśnięciu Grupa trybu failover wyzwala tryb failover. Gdy odbiornik do odczytu i zapisu stanie się dostępny, a połączenia z nim zakończą się niepowodzeniem (2). Na poniższym diagramie przedstawiono dwa etapy procesu odzyskiwania.

> [!NOTE]
> Jeśli awaria w regionie podstawowym zostaną skorygowane w okresie prolongaty, Traffic Manager wykryje przywrócenie łączności w regionie podstawowym i przełączy ruch użytkownika z powrotem do wystąpienia aplikacji w regionie A. To wystąpienie aplikacji wznawia działanie i działa w trybie odczytu i zapisu przy użyciu podstawowej bazy danych w regionie A, jak pokazano na poprzednim diagramie.

![Scenariusz 2. Etapy odzyskiwania po awarii.](./media/designing-cloud-solutions-for-disaster-recovery/scenario2-b.png)

Jeśli wystąpi awaria w regionie B, Traffic Manager wykryje awarię sieci Web punktu końcowego w regionie B i oznaczy ją jako obniżoną (1). W międzyczasie grupa trybu failover przełącza odbiornik tylko do odczytu do regionu A (2). Ta awaria nie ma wpływu na środowisko użytkownika końcowego, ale podstawowa baza danych jest narażona na awarię. Na poniższym diagramie przedstawiono awarię w regionie pomocniczym:

![Scenariusz 2. Awaria regionu pomocniczego.](./media/designing-cloud-solutions-for-disaster-recovery/scenario2-c.png)

Po ograniczeniu przestojów pomocnicza baza danych jest natychmiast synchronizowana z serwerem podstawowym, a odbiornik tylko do odczytu jest przełączany do pomocniczej bazy danych w regionie B. Podczas synchronizacji, w zależności od ilości danych, które muszą zostać zsynchronizowane, może być nieco nieznacznie wpływać na jego wydajność.

Ten Wzorzec projektowy ma kilka **zalet**:

* Pozwala to uniknąć utraty danych w trakcie tymczasowego okresu przestoju.
* Przestoje są zależne od tego, jak szybko Traffic Manager wykrywa błąd łączności, który można skonfigurować.

**Kompromis** polega na tym, że aplikacja musi być w stanie pracować w trybie tylko do odczytu.

## <a name="scenario-3-application-relocation-to-a-different-geography-without-data-loss-and-near-zero-downtime"></a>Scenariusz 3: przemieszczenie aplikacji do innej lokalizacji geograficznej bez utraty danych i bliskiego przestoju

W tym scenariuszu aplikacja ma następujące cechy:

* Użytkownicy końcowi uzyskują dostęp do aplikacji z różnych lokalizacje geograficzne
* Aplikacja zawiera obciążenia tylko do odczytu, które nie zależą od pełnej synchronizacji z najnowszymi aktualizacjami
* Dostęp do zapisu do danych powinien być obsługiwany w tej samej lokalizacji geograficznej dla większości użytkowników
* Opóźnienie odczytu ma kluczowe znaczenie dla środowiska użytkownika końcowego

Aby spełnić te wymagania, należy zazagwarantować, że urządzenie użytkownika **zawsze** łączy się z aplikacją wdrożoną w tej samej lokalizacji geograficznej w przypadku operacji tylko do odczytu, takich jak przeglądanie danych, analiza itp. Podczas gdy operacje OLTP są przetwarzane w tym samym obszarze geograficznym **większości czasu**. Na przykład w czasie trwania operacji OLTP w trakcie dnia są przetwarzane w tej samej lokalizacji geograficznej, ale w godzinach, w których mogą być przetwarzane w innej lokalizacji geograficznej. Jeśli działanie użytkownika końcowego przede wszystkim odbywa się w godzinach pracy, można zagwarantować optymalną wydajność większości użytkowników w większości czasu. Na poniższym diagramie przedstawiono tę topologię.

Zasoby aplikacji należy wdrożyć w każdej lokalizacji geograficznej, w której masz duże zapotrzebowanie na użycie. Na przykład jeśli aplikacja jest aktywnie używana w Stany Zjednoczone, Unii Europejskiej i Południowej Azja Wschodnia, aplikacja powinna zostać wdrożona we wszystkich tych lokalizacje geograficzneach. Podstawowa baza danych powinna zostać przełączona dynamicznie z jednej lokalizacji geograficznej na następną na końcu godzin pracy. Ta metoda jest wywoływana "po". Obciążenie OLTP zawsze nawiązuje połączenie z bazą danych za pośrednictwem trybu failover odbiornika do odczytu i zapisu ** &lt; — Grupa-name &gt; . Database.Windows.NET** (1). Obciążenie tylko do odczytu nawiązuje połączenie z lokalną bazą danych bezpośrednio przy użyciu bazy danych serwera punktu końcowego ** &lt; serwera — name &gt; . Database.Windows.NET** (2). Traffic Manager jest skonfigurowany przy użyciu [metody routingu wydajności](../../traffic-manager/traffic-manager-configure-performance-routing-method.md). Gwarantuje to, że urządzenie użytkownika końcowego jest połączone z usługą sieci Web w najbliższym regionie. Traffic Manager powinna być skonfigurowana z włączonym monitorowaniem punktu końcowego dla każdego punktu końcowego usługi sieci Web (3).

> [!NOTE]
> Konfiguracja grupy trybu failover określa, który region jest używany do pracy w trybie failover. Ponieważ nowy element podstawowy znajduje się w innej lokalizacji geograficznej, przełączenie w tryb failover skutkuje dłuższym opóźnieniem dla obciążeń w ramach OLTP i tylko do odczytu, dopóki zagrożony region nie zostanie przywrócony do trybu online.

![Scenariusz 3. Konfiguracja z podstawową w regionie Wschodnie stany USA.](./media/designing-cloud-solutions-for-disaster-recovery/scenario3-a.png)

Na przykład po upływie dnia 11 czasu lokalnego aktywne bazy danych powinny zostać przełączone do następnego regionu (Europa Północna). To zadanie można całkowicie zautomatyzować przy użyciu [Azure Logic Apps](../../logic-apps/logic-apps-overview.md). Zadanie obejmuje następujące kroki:

* Przełącz serwer podstawowy w grupie trybu failover do Europy Północnej przy użyciu przyjaznego trybu failover (1)
* Usuń grupę trybu failover między regionami Wschodnie stany USA i Europa Północna
* Utwórz nową grupę trybu failover o tej samej nazwie, ale między Europą Północna a Azja Wschodnia (2).
* Dodaj podstawową w Europie Północnej i pomocniczą Azja Wschodnia do tej grupy trybu failover (3).

Na poniższym diagramie przedstawiono nową konfigurację po planowanej pracy w trybie failover:

![Scenariusz 3. Przejście podstawowego do Europy Północnej.](./media/designing-cloud-solutions-for-disaster-recovery/scenario3-b.png)

W przypadku awarii w Europie Północnej na przykład automatyczne przejście do trybu failover bazy danych jest inicjowane przez grupę trybu failover, co efektywnie skutkuje przeniesieniem aplikacji do następnego regionu przed harmonogramem (1).  W takim przypadku Wschodnie stany USA są jedynym pozostałym regionem pomocniczym, dopóki Europa Północna nie będzie w trybie online. Pozostałe dwa regiony umożliwiają klientom wszystkie trzy lokalizacje geograficzne przez przełączanie ról. Azure Logic Apps należy odpowiednio dostosować. Ponieważ pozostałe regiony uzyskują dodatkowy ruch użytkownika z Europy, wpływ na wydajność aplikacji jest taki sam jak w przypadku dodatkowych opóźnień, ale również przez większą liczbę połączeń użytkowników końcowych. Po zmniejszeniu przestojów w Europie Północnej, pomocnicza baza danych jest natychmiast synchronizowana z bieżącym serwerem podstawowym. Na poniższym diagramie przedstawiono awarię w Europie Północnej:

![Scenariusz 3. Awaria w Europie Północnej.](./media/designing-cloud-solutions-for-disaster-recovery/scenario3-c.png)

> [!NOTE]
> Można skrócić czas, w którym środowisko użytkownika końcowego w Europie jest obniżone o długi czas oczekiwania. Aby to zrobić, należy wdrożyć kopię aplikacji i utworzyć pomocnicze bazy danych w innym regionie lokalnym (Europa Zachodnia) jako zastąpienie wystąpienia aplikacji offline w Europie Północnej. Kiedy Ostatnia wraca do trybu online, możesz zdecydować, czy kontynuować korzystanie z Europa Zachodnia, czy też usunąć kopię aplikacji, a następnie przełączyć się z powrotem do korzystania z Europy Północnej.

Najważniejsze **zalety** tego projektu to:

* Obciążenie aplikacji tylko do odczytu uzyskuje dostęp do danych w regionie w każdym momencie.
* Obciążenie aplikacji do odczytu i zapisu uzyskuje dostęp do danych w najbliższym regionie w okresie największej aktywności w każdej lokalizacji geograficznej.
* Ze względu na to, że aplikacja jest wdrażana w wielu regionach, może przeżyły utratę jednego z regionów bez jakiegokolwiek znaczącego przestoju.

Istnieją jednak pewne **kompromisy**:

* Regionalna awaria powoduje, że w lokalizacji geograficznej ma wpływ dłuższy czas oczekiwania. Zarówno do odczytu, jak i do odczytu obciążenia są obsługiwane przez aplikację w innej lokalizacji geograficznej.
* Obciążenia tylko do odczytu muszą łączyć się z innym punktem końcowym w każdym regionie.

## <a name="business-continuity-planning-choose-an-application-design-for-cloud-disaster-recovery"></a>Planowanie ciągłości działania: wybierz projekt aplikacji na potrzeby odzyskiwania po awarii w chmurze

Konkretna Strategia odzyskiwania po awarii w chmurze może łączyć lub zwiększać te wzorce projektowe, aby najlepiej spełniały potrzeby aplikacji.  Jak wspomniano wcześniej, wybrana strategia jest oparta na umowie SLA, która ma być oferowana klientom i topologii wdrażania aplikacji. Aby ułatwić podjęcie decyzji, Poniższa tabela zawiera porównanie opcji na podstawie celu punktu odzyskiwania (RPO) i szacowanego czasu odzyskiwania (ERT).

| Wzorce | Cel punktu odzyskiwania | ERT |
|:--- |:--- |:--- |
| Wdrożenie Active-pasywne na potrzeby odzyskiwania po awarii z dostępem do udostępnionej bazy danych |Dostęp do odczytu i zapisu < 5 sek. |Czas wykrywania niepowodzeń + wartość czasu wygaśnięcia systemu DNS |
| Wdrażanie aktywne-aktywne na potrzeby równoważenia obciążenia aplikacji |Dostęp do odczytu i zapisu < 5 sek. |Czas wykrywania niepowodzeń + wartość czasu wygaśnięcia systemu DNS |
| Wdrożenie aktywne-pasywne na potrzeby zachowywania danych |Dostęp tylko do odczytu < 5 sek | Dostęp tylko do odczytu = 0 |
||Dostęp do odczytu i zapisu = zero | Dostęp do odczytu i zapisu = czas wykrycia błędu + okres prolongaty z utratą danych |
|||

## <a name="next-steps"></a>Następne kroki

* Aby zapoznać się z omówieniem i scenariuszami ciągłości działania, zobacz temat [ciągłość działania — Omówienie](business-continuity-high-availability-disaster-recover-hadr-overview.md)
* Aby dowiedzieć się więcej o aktywnej replikacji geograficznej, zobacz [aktywną replikację geograficzną](active-geo-replication-overview.md).
* Aby dowiedzieć się więcej na temat grup autotrybu failover, zobacz [grupy autopraca awaryjna](auto-failover-group-overview.md).
* Aby uzyskać informacje o aktywnej replikacji geograficznej z pulami elastycznych, zobacz [strategie dotyczące odzyskiwania po awarii puli elastycznej](disaster-recovery-strategies-for-applications-with-elastic-pool.md).
