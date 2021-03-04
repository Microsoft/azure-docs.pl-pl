---
title: Korzystanie z rozwiązania Service Map na platformie Azure | Microsoft Docs
description: Usługa Service Map jest rozwiązaniem platformy Azure, które automatycznie odnajduje składniki aplikacji w systemach Windows i Linux oraz mapuje komunikację między usługami. Ten artykuł zawiera szczegółowe informacje dotyczące wdrażania Service Map w środowisku użytkownika i korzystania z niego w różnych scenariuszach.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/24/2019
ms.openlocfilehash: ad3f8821189e6e7aabb5653e5f938bb73f57a34b
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102047013"
---
# <a name="using-service-map-solution-in-azure"></a>Korzystanie z rozwiązania Service Map na platformie Azure

Mapa usługi automatycznie odnajduje składniki aplikacji w systemach Windows i Linux oraz mapuje komunikację między usługami. Dzięki usłudze Service Map można wyświetlać serwery w sposób, w jakich się o nich myśli: jako wzajemnie połączone systemy dostarczające usługi. Usługa Service Map pokazuje połączenia między serwerami i procesami, opóźnienie połączeń przychodzących i wychodzących oraz porty dla każdej architektury połączonej za pomocą protokołu TCP. Nie jest wymagana żadna konfiguracja, wystarczy zainstalować agenta.

W tym artykule opisano szczegóły dotyczące dołączania i korzystania z Service Map. Wymagania wstępne rozwiązania są następujące:

* Obszar roboczy Log Analytics w [obsługiwanym regionie](vminsights-configure-workspace.md#supported-regions).

* [Agent log Analytics](vminsights-enable-overview.md#agents) zainstalowany na komputerze z systemem Windows lub Linux podłączonym do tego samego obszaru roboczego, w którym włączono rozwiązanie.

* [Agent zależności](vminsights-enable-overview.md#agents) zainstalowany na komputerze z systemem Windows lub Linux.

>[!NOTE]
>Jeśli wdrożono już Service Map, możesz również wyświetlić mapy w usłudze VM Insights, która zawiera dodatkowe funkcje do monitorowania kondycji i wydajności maszyn wirtualnych. Aby dowiedzieć się więcej, zobacz [Omówienie usługi VM Insights](../vm/vminsights-overview.md). Aby dowiedzieć się więcej o różnicach między rozwiązaniem Service Map i funkcją mapy usługi VM Insights, zobacz poniższe [często zadawane pytania](../faq.md#vm-insights).

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com).

## <a name="enable-service-map"></a>Włącz Service Map

1. Włącz rozwiązanie Service Map z [witryny Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.ServiceMapOMS?tab=Overview) lub przy użyciu procesu opisanego w temacie [Dodawanie rozwiązań monitorowania z Galeria rozwiązań](../insights/solutions.md).
1. [Zainstaluj agenta zależności w systemie Windows](../vm/vminsights-enable-hybrid.md#install-the-dependency-agent-on-windows) lub [Zainstaluj agenta zależności programu](../vm/vminsights-enable-hybrid.md#install-the-dependency-agent-on-linux) dla systemu Linux na każdym komputerze, na którym chcesz pobrać dane. Agent Dependency Agent może monitorować połączenia do najbliższych sąsiadów, więc nie jest konieczny na każdym komputerze.

Dostęp do Service Map w Azure Portal z obszaru roboczego Log Analytics i wybierz **rozwiązania** opcji w okienku po lewej stronie.<br><br> ![Wybierz opcję rozwiązania w obszarze roboczym ](./media/service-map/select-solution-from-workspace.png) .<br> Z listy rozwiązań wybierz pozycję **ServiceMap (WorkspaceName)** i na stronie przegląd rozwiązania Service map kliknij kafelek podsumowanie Service map.<br><br> ![Kafelek podsumowania Service Map ](./media/service-map/service-map-summary-tile.png) .

## <a name="use-cases-make-your-it-processes-dependency-aware"></a>Przypadki użycia: uczyń proces IT Rozpoznaj zależności

### <a name="discovery"></a>Odnajdywanie

Service Map automatycznie kompiluje wspólną mapę referencyjną zależności między serwerami, procesami i usługami innych firm. Odnajduje i mapuje wszystkie zależności TCP, identyfikując niespodziewane połączenia, zdalne systemy innych firm, od których zależą, oraz zależności od tradycyjnych ciemnych obszarów sieci, takich jak Active Directory. Service Map odnajduje nieudane połączenia sieciowe, które mają być podejmowane przez systemy zarządzane, co pomaga identyfikować potencjalną nieprzerwaną konfigurację serwera, awarię usługi i problemy z siecią.

### <a name="incident-management"></a>Zarządzanie zdarzeniami

Service Map pomaga wyeliminować wątpliwości izolacji problemu, pokazując, jak systemy są połączone i wpływają na siebie. Oprócz identyfikowania zakończonych niepowodzeniem połączeń pomaga ona identyfikować nieprawidłowo skonfigurowane usługi równoważenia obciążenia, zaskakujące lub nadmierne obciążenie dla krytycznych usług i nieautoryzowanych klientów, takich jak komputery deweloperskie, które mówią z systemami produkcyjnymi. Korzystając ze zintegrowanych przepływów pracy z Change Tracking, można również sprawdzić, czy zdarzenie zmiany na maszynie lub usłudze zaplecza wyjaśnia główną przyczynę zdarzenia.

### <a name="migration-assurance"></a>Gwarancja migracji

Za pomocą Service Map można efektywnie planować, przyspieszać i weryfikować migracje platformy Azure, co pomaga upewnić się, że nic nie zostanie pozostawione i wystąpiło nieoczekiwane przestoje. Można odkryć wszystkie systemy zależne, które muszą zostać zmigrowane razem, ocenić konfigurację i pojemność systemu oraz sprawdzić, czy uruchomiony system nadal obsługuje użytkowników, czy też jest kandydatem do likwidacji, a nie z migracji. Po zakończeniu przenoszenia można sprawdzić obciążenie i tożsamość klienta, aby sprawdzić, czy systemy testowe i klienci nawiązują połączenie. Jeśli w definicjach planowania podsieci i zapory występują problemy, nieudane połączenia w usłudze Service Map Maps wskażą systemy, które wymagają łączności.

### <a name="business-continuity"></a>Ciągłość działalności biznesowej

Jeśli używasz Azure Site Recovery i potrzebujesz pomocy przy definiowaniu sekwencji odzyskiwania dla środowiska aplikacji, Service Map mogą automatycznie pokazywać, w jaki sposób systemy są zależne od siebie, aby upewnić się, że plan odzyskiwania jest niezawodny. Wybierając serwer lub grupę krytyczną i wyświetlając jej klientów, można zidentyfikować systemy frontonu do odzyskania po przywróceniu i udostępnieniu serwera. Z drugiej strony, przeglądając zależności zaplecza serwerów krytycznych, można zidentyfikować systemy, które mają zostać odzyskane przed przywróceniem systemów fokusu.

### <a name="patch-management"></a>Zarządzanie poprawkami

Service Map ulepsza korzystanie z oceny aktualizacji systemu, pokazując, które inne zespoły i serwery są zależne od Twojej usługi, dzięki czemu możesz powiadomić je z wyprzedzeniem przed wprowadzeniem poprawek w systemie. Service Map również rozszerza zarządzanie poprawkami, wskazując, czy usługi są dostępne i prawidłowo połączone po zastosowaniu poprawek i ponownym uruchomieniu.

## <a name="mapping-overview"></a>Omówienie mapowania

Service Map Agenci zbierają informacje o wszystkich procesach połączonych z protokołem TCP na serwerze, na którym są zainstalowane, i szczegółowe informacje o połączeniach przychodzących i wychodzących poszczególnych procesów.

Z listy w lewym okienku można wybrać maszyny lub grupy, które mają Service Map agenci do wizualizacji ich zależności w określonym przedziale czasu. Mapowanie zależności maszyn koncentruje się na określonej maszynie i pokazują wszystkie maszyny, które są bezpośrednimi klientami lub serwerami TCP tej maszyny.  Mapy grup maszyn pokazują zestawy serwerów i ich zależności.

![Przegląd Service Map](media/service-map/service-map-overview.png)

Maszyny mogą być rozwinięte na mapie, aby pokazać uruchomione grupy procesów i procesy z aktywnymi połączeniami sieciowymi w wybranym zakresie czasu. Gdy komputer zdalny z agentem Service Map jest rozwinięty, aby wyświetlić szczegóły procesu, wyświetlane są tylko te procesy, które komunikują się z maszyną fokusu. Liczba maszyn frontonu bez agentów, które nawiązują połączenie z maszyną fokusową, jest wskazywana po lewej stronie procesów, z którymi się łączą. Jeśli komputer fokusu nawiązać połączenie z maszyną zaplecza, która nie ma agenta, serwer zaplecza zostanie uwzględniony w grupie portów serwera wraz z innymi połączeniami z tym samym numerem portu.

Domyślnie mapowanie Service Map pokazuje ostatnie 30 minut informacji o zależnościach. Korzystając z kontrolek czasowych w lewym górnym rogu, można wykonywać zapytania w usłudze Maps dla historycznych zakresów czasowych (do godziny), aby pokazać, jak zależności były wyszukiwane w przeszłości (na przykład podczas zdarzenia lub przed wystąpieniem zmiany). Service Map dane są przechowywane przez 30 dni w płatnych obszarach roboczych i przez 7 dni w bezpłatnych obszarach roboczych.

## <a name="status-badges-and-border-coloring"></a>Wskaźniki stanu i kolorowanie obramowania

W dolnej części każdego serwera na mapie może być listą znaczków stanu przekazujących informacje o stanie serwera. Wskaźniki wskazują, że istnieją istotne informacje dotyczące serwera z jednej z rozwiązań integracji. Kliknięcie wskaźnika spowoduje przejście bezpośrednio do szczegółów stanu w okienku po prawej stronie. Obecnie dostępne wskaźniki stanu obejmują alerty, personel działu obsługi, zmiany, zabezpieczenia i aktualizacje.

W zależności od ważności znaczków stanu, obramowania węzła maszyny mogą być kolorowe kolor czerwony (krytyczny), żółty (ostrzeżenie) lub niebieski (informacyjny). Kolor reprezentuje najbardziej rygorystyczny stan wszystkich znaczków stanu. Szare obramowanie wskazuje węzeł, który nie ma wskaźników stanu.

![Wskaźniki stanu](media/service-map/status-badges.png)

## <a name="process-groups"></a>Grupy procesów

Grupy procesów łączą procesy, które są skojarzone ze wspólnym produktem lub usługą w grupie procesów.  Po rozwinięciu węzła komputera zostanie wyświetlony proces autonomiczny wraz z grupami procesów.  Jeśli jakiekolwiek połączenia przychodzące i wychodzące do procesu w ramach grupy procesów nie powiodły się, połączenie jest wyświetlane jako nieudane dla całej grupy procesów.

## <a name="machine-groups"></a>Grupy maszyn

Grupy maszyn umożliwiają wyświetlanie map wyśrodkowanych wokół zestawu serwerów, a nie tylko jednego, dlatego można zobaczyć wszystkie elementy członkowskie wielowarstwowej aplikacji lub klastra serwerów na jednej mapie.

Użytkownicy wybierają, które serwery należą do grupy, a następnie wybierają nazwę grupy.  Następnie można wyświetlić grupę ze wszystkimi procesami i połączeniami lub wyświetlić tylko te procesy i połączenia, które bezpośrednio odnoszą się do innych elementów członkowskich grupy.

![Grupa maszyn](media/service-map/machine-group.png)

### <a name="creating-a-machine-group"></a>Tworzenie grupy maszyn

Aby utworzyć grupę, wybierz maszynę lub maszyny z listy komputery, a następnie kliknij przycisk **Dodaj do grupy**.

![Tworzenie grupy](media/service-map/machine-groups-create.png)

W tym miejscu możesz wybrać opcję **Utwórz nowy** i nadać grupie nazwę.

![Grupa nazw](media/service-map/machine-groups-name.png)

>[!NOTE]
>Grupy maszyn są ograniczone do 10 serwerów.

### <a name="viewing-a-group"></a>Wyświetlanie grupy

Po utworzeniu niektórych grup można je wyświetlić, wybierając kartę grupy.

![Karta grupy](media/service-map/machine-groups-tab.png)

Następnie wybierz nazwę grupy, aby wyświetlić mapę dla tej grupy maszyn.
![Grupa maszyn ](media/service-map/machine-group.png) komputery należące do grupy są pokreślone na białej mapie.

Rozszerzanie grupy spowoduje wyświetlenie listy maszyn, które tworzą grupę maszyn.

![Maszyny grupy maszyn](media/service-map/machine-groups-machines.png)

### <a name="filter-by-processes"></a>Filtruj według procesów

Można przełączać widok mapy między pokazywaniem wszystkich procesów i połączeń w grupie i tylko tymi, które bezpośrednio odnoszą się do grupy maszyn.  Widok domyślny to wyświetlanie wszystkich procesów.  Widok można zmienić, klikając ikonę filtru nad mapą.

![Filtruj grupę](media/service-map/machine-groups-filter.png)

Po wybraniu **wszystkich procesów** mapa będzie zawierać wszystkie procesy i połączenia na poszczególnych maszynach w grupie.

![Grupa maszyn — wszystkie procesy](media/service-map/machine-groups-all.png)

Jeśli zmienisz widok tak, aby wyświetlał tylko **procesy połączone z grupą**, mapa zostanie zamapowana na tylko te procesy i połączenia, które są połączone bezpośrednio z innymi komputerami w grupie, tworząc uproszczony widok.

![Filtrowane procesy grupy maszyn](media/service-map/machine-groups-filtered.png)
 
### <a name="adding-machines-to-a-group"></a>Dodawanie maszyn do grupy

Aby dodać maszyny do istniejącej grupy, zaznacz pola obok żądanych maszyn, a następnie kliknij przycisk **Dodaj do grupy**.  Następnie wybierz grupę, do której chcesz dodać maszyny.
 
### <a name="removing-machines-from-a-group"></a>Usuwanie maszyn z grupy

Na liście grupy rozwiń nazwę grupy, aby wyświetlić listę maszyn w grupie maszyn.  Następnie kliknij menu wielokropka obok komputera, który chcesz usunąć, i wybierz polecenie **Usuń**.

![Usuń maszynę z grupy](media/service-map/machine-groups-remove.png)

### <a name="removing-or-renaming-a-group"></a>Usuwanie grupy lub zmiana jej nazwy

Kliknij menu wielokropka obok nazwy grupy na liście grup.

![Menu grupy maszyn](media/service-map/machine-groups-menu.png)


## <a name="role-icons"></a>Ikony ról

Niektóre procesy oferują określone role na maszynach: serwery sieci Web, serwery aplikacji, baza danych i tak dalej. Service Map dodawania adnotacji do pól procesów i maszyn z ikonami ról, aby pomóc w szybkim zidentyfikowaniu roli, jaką odgrywa proces lub serwer.

| Ikona roli | Opis |
|:--|:--|
| ![Serwer sieci Web](media/service-map/role-web-server.png) | Serwer sieci Web |
| ![Serwer aplikacji](media/service-map/role-application-server.png) | Serwer aplikacji |
| ![Serwer bazy danych](media/service-map/role-database.png) | Serwer bazy danych |
| ![Serwer LDAP](media/service-map/role-ldap.png) | Serwer LDAP |
| ![Serwer SMB](media/service-map/role-smb.png) | Serwer SMB |

![Ikony ról](media/service-map/role-icons.png)


## <a name="failed-connections"></a>Nieudane połączenia

Nieudane połączenia są wyświetlane w Service Map Maps dla procesów i komputerów, z przerywaną czerwoną linią wskazującą, że system klienta nie dociera do procesu lub portu. Nieudane połączenia są zgłaszane z dowolnego systemu ze wdrożonym agentem Service Map, jeśli ten system jest podłączany do nieudanego połączenia. Service Map mierzy ten proces, obserwując gniazda TCP, które nie mogą nawiązać połączenia. Ten błąd może być spowodowany przez zaporę, błędną konfigurację na kliencie lub serwerze lub usługa zdalna jest niedostępna.

![Zrzut ekranu jednej części Service Map wyróżnienia kreskowaną czerwoną linią wskazującą, że połączenie między procesem backup.pl i portem 4475 nie powiodło się.](media/service-map/failed-connections.png)

Zrozumienie nieudanych połączeń może pomóc w rozwiązywaniu problemów, weryfikacji migracji, analizie zabezpieczeń i ogólnym zrozumieniu architektury. Nieudane połączenia są czasami nieszkodliwe, ale często wskazują na problem, na przykład nieprzerwane środowisko pracy awaryjnej lub dwie warstwy aplikacji nie mogą komunikować się po migracji do chmury.

## <a name="client-groups"></a>Grupy klientów

Grupy klientów to pola mapy, które reprezentują komputery klienckie bez agentów zależności. Pojedyncza grupa klientów reprezentuje klientów dla danego procesu lub komputera.

![Grupy klientów](media/service-map/client-groups.png)

Aby wyświetlić adresy IP serwerów w grupie klientów, wybierz grupę. Zawartość grupy zostanie wyświetlona w okienku **Właściwości grupy klienta** .

![Właściwości grupy klientów](media/service-map/client-group-properties.png)

## <a name="server-port-groups"></a>Grupy portów serwera

Grupy portów serwera to pola reprezentujące porty serwera na serwerach, na których nie ma agentów zależności. Pole zawiera port serwera oraz liczbę serwerów z połączeniami z tym portem. Rozwiń pole, aby wyświetlić poszczególne serwery i połączenia. Jeśli w polu znajduje się tylko jeden serwer, zostanie wyświetlona nazwa lub adres IP.

![Grupy portów serwera](media/service-map/server-port-groups.png)

## <a name="context-menu"></a>Menu kontekstowe

Kliknięcie wielokropka (...) w prawym górnym rogu dowolnego serwera powoduje wyświetlenie menu kontekstowego dla tego serwera.

![Zrzut ekranu przedstawiający menu kontekstowe otwarte dla serwera w Service Map. Menu zawiera opcje Załaduj mapę serwera i Pokaż własne linki.](media/service-map/context-menu.png)

### <a name="load-server-map"></a>Załaduj mapę serwera

Kliknięcie przycisku **Załaduj mapę serwera** spowoduje przejście do nowej mapy z wybranym serwerem jako nowy komputer fokusu.

### <a name="show-self-links"></a>Pokaż linki własne

Kliknięcie przycisku **Pokaż własne linki** powoduje odświeżenie węzła serwera, łącznie z dowolnymi łączami, które są POŁĄCZENIAmi TCP, które zaczynają się i kończą w ramach procesów na serwerze. Jeśli pokazywane są własne linki, polecenie menu zmieni się w celu **ukrycia samolinków**, aby można je było wyłączyć.

## <a name="computer-summary"></a>Podsumowanie komputera

Okienko **Podsumowanie komputera** zawiera omówienie systemu operacyjnego serwera, liczby zależności i danych z innych rozwiązań. Takie dane obejmują metryki wydajności, bilety usług, śledzenie zmian, zabezpieczenia i aktualizacje.

![Okienko podsumowania komputera](media/service-map/machine-summary.png)

## <a name="computer-and-process-properties"></a>Właściwości komputera i procesu

Po przejściu do mapy Service Map można wybrać maszyny i procesy, aby uzyskać dodatkowy kontekst dotyczący ich właściwości. Maszyny zawierają informacje dotyczące nazw DNS, adresów IPv4, pojemności procesora i pamięci, typu maszyny wirtualnej, systemu operacyjnego i wersji, czasu ostatniego ponownego uruchomienia oraz identyfikatorów ich agentów usługi OMS i Service Map.

![Okienko właściwości maszyny](media/service-map/machine-properties.png)

Można zbierać szczegóły procesu z metadanych systemu operacyjnego dotyczących uruchomionych procesów, w tym nazwę procesu, opis procesu, nazwę użytkownika i domenę (w systemie Windows), nazwę firmy, nazwę produktu, wersję produktu, katalog roboczy, wiersz polecenia i czas rozpoczęcia procesu.

![Okienko właściwości procesu](media/service-map/process-properties.png)

Okienko **Podsumowanie procesu** zawiera dodatkowe informacje na temat łączności procesu, w tym powiązane z nimi porty, połączenia przychodzące i wychodzące oraz połączenia zakończone niepowodzeniem.

![Okienko podsumowania procesu](media/service-map/process-summary.png)

## <a name="alerts-integration"></a>Integracja z alertami

Service Map integruje się z alertami platformy Azure w celu wyświetlenia wyzwolonych alertów dla wybranego serwera w wybranym zakresie czasu. Serwer wyświetla ikonę, jeśli istnieją bieżące alerty, a okienko **alerty komputera** wyświetla alerty.

![Okienko alerty komputera](media/service-map/machine-alerts.png)

Aby włączyć Service Map do wyświetlania odpowiednich alertów, Utwórz regułę alertu, która będzie uruchamiana dla określonego komputera. Aby utworzyć odpowiednie alerty:
- Dołącz klauzulę do grupowania według komputera (na przykład **według interwału komputerowego 1 minuty**).
- Wybierz opcję alertu na podstawie pomiaru metryki.

## <a name="log-events-integration"></a>Integracja zdarzeń dziennika

Service Map integruje się z funkcją wyszukiwania w dziennikach, aby wyświetlić liczbę wszystkich dostępnych zdarzeń dziennika dla wybranego serwera w wybranym zakresie czasu. Możesz kliknąć dowolny wiersz na liście liczników zdarzeń, aby przeskoczyć do wyszukiwania w dzienniku i zobaczyć poszczególne zdarzenia dziennika.

![Okienko zdarzeń dziennika komputera](media/service-map/log-events.png)

## <a name="service-desk-integration"></a>Integracja z obsługą techniczną

Integracja Service Map z łącznik zarządzania usługami IT jest automatyczna, gdy oba rozwiązania zostały włączone i skonfigurowane w obszarze roboczym Log Analytics. Integracja w Service Map ma etykietę "Service Desk". Aby uzyskać więcej informacji, zobacz [centralne zarządzanie narzędzia ITSM elementami roboczymi przy użyciu Łącznik zarządzania usługami IT](../alerts/itsmc-overview.md).

W okienku **usługi Machine Service** jest wyświetlane wszystkie zdarzenia zarządzania usługą IT dla wybranego serwera w wybranym zakresie czasu. Na serwerze zostanie wyświetlona ikona, jeśli istnieją bieżące elementy, a w okienku stanowiska usługi Machine Service znajduje się lista.

![Okienko obsługi maszyn](media/service-map/service-desk.png)

Aby otworzyć element w połączonym rozwiązaniu narzędzia ITSM, kliknij przycisk **Wyświetl element roboczy**.

Aby wyświetlić szczegóły elementu w przeszukiwaniu dzienników, kliknij przycisk **Pokaż w przeszukiwaniu dzienników**.
Metryki połączeń są zapisywane w dwóch nowych tabelach w Log Analytics 

## <a name="change-tracking-integration"></a>Integracja Change Tracking

Integracja Service Map z Change Tracking jest automatyczna, gdy oba rozwiązania zostały włączone i skonfigurowane w obszarze roboczym Log Analytics.

W okienku **Change Tracking komputera** są wyświetlane wszystkie zmiany, z najnowszymi ostatnimi, a także link do przechodzenia do szczegółów w celu przeszukania dziennika w celu uzyskania dodatkowych szczegółów.

![Zrzut ekranu okienka Change Tracking komputera w Service Map.](media/service-map/change-tracking.png)

Poniższy obraz przedstawia szczegółowy widok zdarzenia Zmianakonfiguracji, które może być widoczne po wybraniu opcji **Pokaż w log Analytics**.

![Zdarzenie Zmianakonfiguracji](media/service-map/configuration-change-event-01.png)

## <a name="performance-integration"></a>Integracja wydajności

W okienku **wydajność komputera** są wyświetlane metryki wydajności standardowe dla wybranego serwera. Metryki obejmują użycie procesora CPU, użycie pamięci, wysłane i odebrane bajty sieciowe oraz listę najważniejszych procesów przez wysłane i odebrane bajty sieciowe.

![Okienko wydajności maszyny](media/service-map/machine-performance.png)

Aby wyświetlić dane dotyczące wydajności, może być konieczne [włączenie odpowiednich liczników wydajności log Analytics](../agents/data-sources-performance-counters.md).  Liczniki, które chcesz włączyć:

W systemie Windows:
- Procesor (*) \\ czas procesora (%)
- \\Procent zadeklarowanych bajtów w użyciu
- Liczba bajtów wysłanych z karty sieciowej (*) na \\ sekundę
- Liczba odebranych bajtów karty sieciowej (*) \\ /s

W systemie Linux:
- Procesor (*) \\ czas procesora (%)
- Pamięć (*) \\ % zajętej pamięci
- Liczba bajtów wysłanych z karty sieciowej (*) na \\ sekundę
- Liczba odebranych bajtów karty sieciowej (*) \\ /s

Aby uzyskać dane dotyczące wydajności sieci, należy również włączyć rozwiązanie Wire Data 2.0 w obszarze roboczym.
 
## <a name="security-integration"></a>Integracja zabezpieczeń

Integracja Service Map z Security and Audit jest automatyczna, gdy oba rozwiązania zostały włączone i skonfigurowane w obszarze roboczym Log Analytics.

W okienku **zabezpieczenia komputera** są wyświetlane dane z rozwiązania Security and Audit wybranego serwera. W okienku wyświetlane jest podsumowanie wszystkich problemów związanych z zabezpieczeniami serwera w wybranym zakresie czasu. Kliknięcie dowolnego z problemów z zabezpieczeniami umożliwia przechodzenie do dziennika w celu przeszukania szczegółowych informacji o nich.

![Okienko zabezpieczenia komputera](media/service-map/machine-security.png)

## <a name="updates-integration"></a>Integracja aktualizacji

Integracja Service Map z Update Management jest automatyczna, gdy oba rozwiązania zostały włączone i skonfigurowane w obszarze roboczym Log Analytics.

W okienku **aktualizacje komputera** są wyświetlane dane z rozwiązania Update Management wybranego serwera. Okienko zawiera podsumowanie wszystkich brakujących aktualizacji dla serwera w wybranym zakresie czasu.

![Zrzut ekranu okienka aktualizacje komputera w Service Map.](media/service-map/machine-updates.png)

## <a name="log-analytics-records"></a>Rekordy usługi Log Analytics

Service Map komputer i dane spisu procesu są dostępne do [wyszukiwania](../logs/log-query-overview.md) w log Analytics. Te dane można zastosować do scenariuszy, które obejmują Planowanie migracji, analizę pojemności, odnajdywanie i rozwiązywanie problemów z wydajnością na żądanie.

Jeden rekord jest generowany na godzinę dla każdego unikatowego komputera i procesu, oprócz rekordów generowanych podczas uruchamiania procesu lub komputera w celu Service Map. Te rekordy mają właściwości w poniższych tabelach. Pola i wartości w ServiceMapComputer_CL zdarzenia są mapowane na pola zasobu maszyny w interfejsie API usługi ServiceMap Azure Resource Manager. Pola i wartości w zdarzeniach ServiceMapProcess_CL są mapowane na pola zasobu procesu w interfejsie API usługi ServiceMap Azure Resource Manager. Pole ResourceName_s pasuje do pola Nazwa w odpowiednim zasobie Menedżer zasobów. 

>[!NOTE]
>W miarę rozwoju funkcji Service Map te pola mogą ulec zmianie.

Istnieją wewnętrznie wygenerowane właściwości, których można użyć do identyfikowania unikatowych procesów i komputerów:

- Komputer: Użyj *ResourceID* lub *ResourceName_s* , aby jednoznacznie zidentyfikować komputer w obszarze roboczym log Analytics.
- Proces: Użyj *ResourceID* , aby jednoznacznie zidentyfikować proces w obszarze roboczym log Analytics. *ResourceName_s* jest unikatowy w kontekście maszyny, na której jest uruchomiony proces (MachineResourceName_s) 

Ponieważ wiele rekordów może istnieć dla określonego procesu i komputera w określonym przedziale czasu, kwerendy mogą zwrócić więcej niż jeden rekord dla tego samego komputera lub procesu. Aby dołączyć tylko najnowszy rekord, Dodaj element "| Identyfikator zasobu deduplikacji "do zapytania.

### <a name="connections"></a>Połączenia

Metryki połączeń są zapisywane w nowej tabeli w Log Analytics-VMConnection. Ta tabela zawiera informacje o połączeniach dla komputera (przychodzący i wychodzący). Metryki połączeń są również udostępniane interfejsom API, które udostępniają metodę uzyskiwania określonej metryki w przedziale czasu.  Połączenia TCP wynikające z zaakceptowania w gnieździe nasłuchiwania są przychodzące, podczas gdy te utworzone przez połączenie z danym adresem IP i portem są wychodzące. Kierunek połączenia jest reprezentowany przez Właściwość Direction, która może być ustawiona na wartość **przychodzące** lub **wychodzące**. 

Rekordy w tych tabelach są generowane na podstawie danych zgłaszanych przez agenta zależności. Każdy rekord reprezentuje obserwację w przedziale czasowym co minutę. Właściwość TimeGenerated wskazuje początek interwału czasu. Każdy rekord zawiera informacje służące do identyfikacji odpowiedniej jednostki, czyli połączenia lub portu, a także metryk skojarzonych z tą jednostką. Obecnie zgłaszane jest tylko działanie sieciowe, które odbywa się przy użyciu protokołu TCP przez IPv4.

Aby zarządzać kosztami i złożonością, rekordy połączeń nie reprezentują poszczególnych fizycznych połączeń sieciowych. Wiele połączeń sieci fizycznych jest zgrupowanych w połączenie logiczne, które jest następnie odzwierciedlone w odpowiedniej tabeli.  Oznacza to, że rekordy w tabeli *VMConnection* reprezentują logiczne grupowanie, a nie poszczególne połączenia fizyczne, które są obserwowane. Fizyczne połączenie sieciowe udostępniające tę samą wartość dla następujących atrybutów podczas danego interwału jednominutowego są agregowane w jednym rekordzie logicznym w *VMConnection*. 

| Właściwość | Opis |
|:--|:--|
| `Direction` |Kierunek połączenia, wartość jest *przychodząca* lub *wychodząca* |
| `Machine` |Nazwa FQDN komputera |
| `Process` |Tożsamość procesu lub grup procesów, inicjowanie/zaakceptowanie połączenia |
| `SourceIp` |Adres IP źródła |
| `DestinationIp` |Adres IP miejsca docelowego |
| `DestinationPort` |Numer portu miejsca docelowego |
| `Protocol` |Protokół używany do nawiązywania połączenia.  Wartości to *TCP*. |

W celu uwzględnienia wpływu grupowania informacje o liczbie zgrupowanych połączeń fizycznych są dostępne w następujących właściwościach rekordu:

| Właściwość | Opis |
|:--|:--|
| `LinksEstablished` |Liczba połączeń sieci fizycznych, które zostały nawiązane w przedziale czasu raportowania |
| `LinksTerminated` |Liczba połączeń sieci fizycznych, które zostały przerwane w przedziale czasu raportowania |
| `LinksFailed` |Liczba fizycznych połączeń sieciowych zakończonych niepowodzeniem w przedziale czasu raportowania. Te informacje są obecnie dostępne tylko dla połączeń wychodzących. |
| `LinksLive` |Liczba otwartych połączeń sieciowych w momencie zakończenia przedziału czasu raportowania|

#### <a name="metrics"></a>Metryki

Oprócz metryk licznik połączeń informacje o ilości danych wysłanych i odebranych w danym połączeniu logicznym lub porcie sieciowym są również zawarte w następujących właściwościach rekordu:

| Właściwość | Opis |
|:--|:--|
| `BytesSent` |Łączna liczba bajtów wysłanych w przedziale czasu raportowania |
| `BytesReceived` |Łączna liczba bajtów odebranych w przedziale czasu raportowania |
| `Responses` |Liczba odpowiedzi zaobserwowanych w przedziale czasu raportowania. 
| `ResponseTimeMax` |Największy czas odpowiedzi (w milisekundach) zaobserwowany w przedziale czasu raportowania.  W przypadku braku wartości właściwość jest pusta.|
| `ResponseTimeMin` |Najmniejszy czas odpowiedzi (w milisekundach) zaobserwowany w przedziale czasu raportowania.  W przypadku braku wartości właściwość jest pusta.|
| `ResponseTimeSum` |Suma wszystkich czasów odpowiedzi (w milisekundach) zaobserwowanych w przedziale czasu raportowania.  W przypadku braku wartości właściwość jest pusta|

Zgłaszany trzeci typ danych to czas odpowiedzi — jak długo obiekt wywołujący oczekuje na żądanie wysłane przez połączenie, które ma zostać przetworzone przez zdalny punkt końcowy. Raportowany czas odpowiedzi jest oszacowaniem rzeczywistego czasu odpowiedzi podstawowego protokołu aplikacji. Jest on obliczany przy użyciu algorytmów heurystycznych na podstawie obserwacji przepływu danych między źródłowym i docelowym końcem połączenia sieciowego. Ze pojęciową różnicą jest różnica między czasem, gdy ostatni bajt żądania opuszcza nadawcę, oraz czas, kiedy ostatni bajt odpowiedzi trafia do niego. Te dwa sygnatury czasowe służą do odróżnić zdarzeń żądań i odpowiedzi dla danego połączenia fizycznego. Różnica między nimi reprezentuje czas odpowiedzi pojedynczego żądania. 

W pierwszej wersji tej funkcji nasz algorytm to przybliżenie, które może współdziałać z różnymi poziomami sukcesu w zależności od rzeczywistego protokołu aplikacji używanego dla danego połączenia sieciowego. Na przykład bieżące podejście działa dobrze w przypadku protokołów opartych na odpowiedzi na żądania, takich jak HTTP (S), ale nie działa z protokołami jednokierunkowymi lub opartymi na kolejkach komunikatów.

Oto kilka istotnych kwestii, które należy wziąć pod uwagę:

1. Jeśli proces akceptuje połączenia o tym samym adresie IP, ale przez wiele interfejsów sieciowych, zostanie zgłoszony oddzielny rekord dla każdego interfejsu. 
2. Rekordy z symbolem wieloznacznym adresu IP nie będą miały aktywności. Są one dołączone do reprezentowania faktu, że port na komputerze jest otwarty dla ruchu przychodzącego.
3. Aby zmniejszyć poziom szczegółowości i ilości danych, rekordy z symbolem wieloznacznym adresu IP zostaną pominięte, gdy istnieje pasujący rekord (dla tego samego procesu, portu i protokołu) o określonym adresie IP. W przypadku pominięcia wieloznacznego rekordu adresu IP Właściwość rekordu IsWildcardBind z określonym adresem IP zostanie ustawiona na wartość "true", aby wskazać, że port jest narażony na każdy interfejs maszyny raportowania.
4. Porty, które są powiązane tylko z określonym interfejsem, mają IsWildcardBind ustawiony na wartość "false".

#### <a name="naming-and-classification"></a>Nazewnictwo i Klasyfikacja

Dla wygody adres IP zdalnego końca połączenia jest zawarty we właściwości RemoteIp. W przypadku połączeń przychodzących RemoteIp jest taka sama jak SourceIp, natomiast w przypadku połączeń wychodzących jest taka sama jak DestinationIp. Właściwość RemoteDnsCanonicalNames reprezentuje nazwy kanoniczne DNS zgłoszone przez komputer dla RemoteIp. Właściwości RemoteDnsQuestions i RemoteClassification są zastrzeżone do użytku w przyszłości. 

#### <a name="geolocation"></a>Geolokalizacja

*VMConnection* również zawiera informacje o geolokalizacji dla zdalnego końca każdego rekordu połączenia w następujących właściwościach rekordu: 

| Właściwość | Opis |
|:--|:--|
| `RemoteCountry` |Nazwa kraju/regionu, w którym znajduje się RemoteIp.  Na przykład *Stany Zjednoczone* |
| `RemoteLatitude` |Geolokalizacja geograficzna.  Na przykład *47,68* |
| `RemoteLongitude` |Długość geograficzna geolokalizacji.  Na przykład *-122,12* |

#### <a name="malicious-ip"></a>Złośliwy adres IP

Każda właściwość RemoteIp w tabeli *VMConnection* jest sprawdzana względem zestawu adresów IP ze znaną złośliwym działaniem. Jeśli RemoteIp zostanie zidentyfikowany jako złośliwe, zostaną wypełnione następujące właściwości (są puste, jeśli adres IP nie jest uważany za złośliwy) w następujących właściwościach rekordu:

| Właściwość | Opis |
|:--|:--|
| `MaliciousIp` |Adres RemoteIp |
| `IndicatorThreadType` |Wykryty wskaźnik zagrożeń to jedna z następujących wartości: *botnet*, *C2*, *CryptoMining*, *sieci darknet*, *DDoS*, *MaliciousUrl*, *złośliwe oprogramowanie*, *phishing*, *proxy*, *opierająca*, *listy do obejrzenia*.   |
| `Description` |Opis zaobserwowanego zagrożenia. |
| `TLPLevel` |Poziom protokołu Traffic Light Protocol (TLP) to jedna ze zdefiniowanych wartości: *biały*, *zielony*, *bursztynowy*, *czerwony*. |
| `Confidence` |Wartości to *0 – 100*. |
| `Severity` |Wartości to *0 – 5*, gdzie *5* jest największa i *0* nie jest poważny. Wartość domyślna to *3*.  |
| `FirstReportedDateTime` |Pierwszy raz dostawca zgłosił wskaźnik. |
| `LastReportedDateTime` |Ostatni czas widziany przez przepływanie wskaźnika. |
| `IsActive` |Wskazuje, że wskaźniki są dezaktywowane z wartością *true* lub *false* . |
| `ReportReferenceLink` |Linki do raportów związanych z tym, że jest to zauważalne. |
| `AdditionalInformation` |Zawiera dodatkowe informacje o zaobserwowanym zagrożeniu, jeśli ma to zastosowanie. |

### <a name="servicemapcomputer_cl-records"></a>ServiceMapComputer_CL rekordy

Rekordy z typem *ServiceMapComputer_CL* mają dane spisu dla serwerów z agentami Service map. Te rekordy mają właściwości w poniższej tabeli:

| Właściwość | Opis |
|:--|:--|
| `Type` | *ServiceMapComputer_CL* |
| `SourceSystem` | *OpsManager* |
| `ResourceId` | Unikatowy identyfikator maszyny w obszarze roboczym |
| `ResourceName_s` | Unikatowy identyfikator maszyny w obszarze roboczym |
| `ComputerName_s` | Nazwa FQDN komputera |
| `Ipv4Addresses_s` | Lista adresów IPv4 serwera |
| `Ipv6Addresses_s` | Lista adresów IPv6 serwera |
| `DnsNames_s` | Tablica nazw DNS |
| `OperatingSystemFamily_s` | System Windows lub Linux |
| `OperatingSystemFullName_s` | Pełna nazwa systemu operacyjnego  |
| `Bitness_s` | Liczba bitów maszyny (32-bitowa lub 64-bitowa)  |
| `PhysicalMemory_d` | Pamięć fizyczna w MB |
| `Cpus_d` | Liczba procesorów CPU |
| `CpuSpeed_d` | Szybkość procesora CPU (w MHz)|
| `VirtualizationState_s` | *nieznane*, *fizyczne*, *wirtualne*, *Funkcja hypervisor* |
| `VirtualMachineType_s` | *HyperV*, *VMware* i tak dalej |
| `VirtualMachineNativeMachineId_g` | Identyfikator maszyny wirtualnej przypisany przez funkcję hypervisor |
| `VirtualMachineName_s` | Nazwa maszyny wirtualnej |
| `BootTime_t` | Czas rozruchu |

### <a name="servicemapprocess_cl-type-records"></a>Rekordy typu ServiceMapProcess_CL

Rekordy z typem *ServiceMapProcess_CL* mają dane spisu dla procesów połączonych z protokołem TCP na serwerach z agentami Service map. Te rekordy mają właściwości w poniższej tabeli:

| Właściwość | Opis |
|:--|:--|
| `Type` | *ServiceMapProcess_CL* |
| `SourceSystem` | *OpsManager* |
| `ResourceId` | Unikatowy identyfikator procesu w obszarze roboczym |
| `ResourceName_s` | Unikatowy identyfikator procesu znajdującego się na komputerze, na którym jest uruchomiony|
| `MachineResourceName_s` | Nazwa zasobu maszyny |
| `ExecutableName_s` | Nazwa pliku wykonywalnego procesu |
| `StartTime_t` | Godzina rozpoczęcia puli procesów |
| `FirstPid_d` | Pierwszy Identyfikator PID w puli procesów |
| `Description_s` | Opis procesu |
| `CompanyName_s` | Nazwa firmy |
| `InternalName_s` | Nazwa wewnętrzna |
| `ProductName_s` | Nazwa produktu |
| `ProductVersion_s` | Wersja produktu |
| `FileVersion_s` | Wersja pliku |
| `CommandLine_s` | Wiersz polecenia |
| `ExecutablePath _s` | Ścieżka do pliku wykonywalnego. |
| `WorkingDirectory_s` | Katalog roboczy |
| `UserName` | Konto, na którym proces jest wykonywany |
| `UserDomain` | Domena, w której wykonywany jest proces |

## <a name="sample-log-searches"></a>Przykładowe wyszukiwania dzienników

### <a name="list-all-known-machines"></a>Wyświetl listę wszystkich znanych maszyn

`ServiceMapComputer_CL | summarize arg_max(TimeGenerated, *) by ResourceId`

### <a name="list-the-physical-memory-capacity-of-all-managed-computers"></a>Wyświetl listę pojemności pamięci fizycznej wszystkich zarządzanych komputerów.

`ServiceMapComputer_CL | summarize arg_max(TimeGenerated, *) by ResourceId | project PhysicalMemory_d, ComputerName_s`

### <a name="list-computer-name-dns-ip-and-os"></a>Wyświetl listę nazw komputerów, DNS, IP i systemu operacyjnego.

`ServiceMapComputer_CL | summarize arg_max(TimeGenerated, *) by ResourceId | project ComputerName_s, OperatingSystemFullName_s, DnsNames_s, Ipv4Addresses_s`

### <a name="find-all-processes-with-sql-in-the-command-line"></a>Znajdź wszystkie procesy z "SQL" w wierszu polecenia

`ServiceMapProcess_CL | where CommandLine_s contains_cs "sql" | summarize arg_max(TimeGenerated, *) by ResourceId`

### <a name="find-a-machine-most-recent-record-by-resource-name"></a>Znajdź maszynę (najnowszy rekord) według nazwy zasobu

`search in (ServiceMapComputer_CL) "m-4b9c93f9-bc37-46df-b43c-899ba829e07b" | summarize arg_max(TimeGenerated, *) by ResourceId`

### <a name="find-a-machine-most-recent-record-by-ip-address"></a>Znajdź komputer (najnowszy rekord) według adresu IP

`search in (ServiceMapComputer_CL) "10.229.243.232" | summarize arg_max(TimeGenerated, *) by ResourceId`

### <a name="list-all-known-processes-on-a-specified-machine"></a>Wyświetl listę wszystkich znanych procesów na określonym komputerze

`ServiceMapProcess_CL | where MachineResourceName_s == "m-559dbcd8-3130-454d-8d1d-f624e57961bc" | summarize arg_max(TimeGenerated, *) by ResourceId`

### <a name="list-all-computers-running-sql"></a>Wyświetl listę wszystkich komputerów z uruchomionym programem SQL

`ServiceMapComputer_CL | where ResourceName_s in ((search in (ServiceMapProcess_CL) "\*sql\*" | distinct MachineResourceName_s)) | distinct ComputerName_s`

### <a name="list-all-unique-product-versions-of-curl-in-my-datacenter"></a>Wyświetl listę wszystkich unikatowych wersji programu zwinięcie w moim centrum danych

`ServiceMapProcess_CL | where ExecutableName_s == "curl" | distinct ProductVersion_s`

### <a name="create-a-computer-group-of-all-computers-running-centos"></a>Utwórz grupę komputerów z systemem CentOS

`ServiceMapComputer_CL | where OperatingSystemFullName_s contains_cs "CentOS" | distinct ComputerName_s`

### <a name="summarize-the-outbound-connections-from-a-group-of-machines"></a>Podsumuj połączenia wychodzące z grupy maszyn

```
// the machines of interest
let machines = datatable(m: string) ["m-82412a7a-6a32-45a9-a8d6-538354224a25"];
// map of ip to monitored machine in the environment
let ips=materialize(ServiceMapComputer_CL
| summarize ips=makeset(todynamic(Ipv4Addresses_s)) by MonitoredMachine=ResourceName_s
| mvexpand ips to typeof(string));
// all connections to/from the machines of interest
let out=materialize(VMConnection
| where Machine in (machines)
| summarize arg_max(TimeGenerated, *) by ConnectionId);
// connections to localhost augmented with RemoteMachine
let local=out
| where RemoteIp startswith "127."
| project ConnectionId, Direction, Machine, Process, ProcessName, SourceIp, DestinationIp, DestinationPort, Protocol, RemoteIp, RemoteMachine=Machine;
// connections not to localhost augmented with RemoteMachine
let remote=materialize(out
| where RemoteIp !startswith "127."
| join kind=leftouter (ips) on $left.RemoteIp == $right.ips
| summarize by ConnectionId, Direction, Machine, Process, ProcessName, SourceIp, DestinationIp, DestinationPort, Protocol, RemoteIp, RemoteMachine=MonitoredMachine);
// the remote machines to/from which we have connections
let remoteMachines = remote | summarize by RemoteMachine;
// all augmented connections
(local)
| union (remote)
//Take all outbound records but only inbound records that come from either //unmonitored machines or monitored machines not in the set for which we are computing dependencies.
| where Direction == 'outbound' or (Direction == 'inbound' and RemoteMachine !in (machines))
| summarize by ConnectionId, Direction, Machine, Process, ProcessName, SourceIp, DestinationIp, DestinationPort, Protocol, RemoteIp, RemoteMachine
// identify the remote port
| extend RemotePort=iff(Direction == 'outbound', DestinationPort, 0)
// construct the join key we'll use to find a matching port
| extend JoinKey=strcat_delim(':', RemoteMachine, RemoteIp, RemotePort, Protocol)
// find a matching port
| join kind=leftouter (VMBoundPort 
| where Machine in (remoteMachines) 
| summarize arg_max(TimeGenerated, *) by PortId 
| extend JoinKey=strcat_delim(':', Machine, Ip, Port, Protocol)) on JoinKey
// aggregate the remote information
| summarize Remote=makeset(iff(isempty(RemoteMachine), todynamic('{}'), pack('Machine', RemoteMachine, 'Process', Process1, 'ProcessName', ProcessName1))) by ConnectionId, Direction, Machine, Process, ProcessName, SourceIp, DestinationIp, DestinationPort, Protocol
```

## <a name="rest-api"></a>Interfejs API REST

Wszystkie dane dotyczące serwera, procesu i zależności w Service Map są dostępne za pośrednictwem [interfejsu API REST Service map](/rest/api/servicemap/).

## <a name="diagnostic-and-usage-data"></a>Dane diagnostyczne i użycia

Firma Microsoft automatycznie zbiera dane dotyczące użycia i wydajności za pomocą usługi Service Map. Firma Microsoft używa tych danych w celu zapewnienia i poprawy jakości, bezpieczeństwa i integralności usługi Service Map. Aby zapewnić dokładne i wydajne możliwości rozwiązywania problemów, dane obejmują informacje o konfiguracji oprogramowania, takie jak system operacyjny, wersja, adres IP, nazwa DNS i nazwa stacji roboczej. Firma Microsoft nie zbiera nazw, adresów ani innych informacji kontaktowych.

Aby uzyskać więcej informacji na temat zbierania i używania danych, zobacz [zasady zachowania poufności informacji w witrynie Microsoft Online Services](https://go.microsoft.com/fwlink/?LinkId=512132).

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [przeszukiwaniu dzienników](../logs/log-query-overview.md) w log Analytics, aby pobrać dane zbierane przez Service map.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Jeśli masz problemy z instalowaniem lub uruchamianiem Service Map, ta sekcja może Ci pomóc. Jeśli nadal nie możesz rozwiązać problemu, skontaktuj się z pomocą techniczną firmy Microsoft.

### <a name="dependency-agent-installation-problems"></a>Problemy z instalacją agenta zależności

#### <a name="installer-prompts-for-a-reboot"></a>Instalator monituje o ponowne uruchomienie
Agent zależności *zazwyczaj* nie wymaga ponownego uruchomienia podczas instalacji lub usuwania. Jednak w niektórych rzadkich przypadkach system Windows Server wymaga ponownego uruchomienia w celu kontynuowania instalacji. Zdarza się to, gdy zależność, zazwyczaj Microsoft Visual C++ redystrybucyjna biblioteka wymaga ponownego uruchomienia z powodu zablokowanego pliku.

#### <a name="message-unable-to-install-dependency-agent-visual-studio-runtime-libraries-failed-to-install-code--code_number-appears"></a>Wyświetlany jest komunikat „Nie można zainstalować agenta zależności: Nie można zainstalować bibliotek środowiska uruchomieniowego programu Visual Studio (kod = [code_number])”

Program Microsoft Dependency Agent jest oparty na bibliotekach środowiska uruchomieniowego programu Microsoft Visual Studio. Jeśli podczas instalowania tych bibliotek wystąpi problem, jest wyświetlany komunikat. 

Instalatory biblioteki środowiska uruchomieniowego tworzą dzienniki w folderze %LOCALAPPDATA%\temp. Plik to `dd_vcredist_arch_yyyymmddhhmmss.log` , gdzie *Arch* jest `x86` lub `amd64` i *rrrrmmddggmmss* jest datą i godziną (zegar 24-godzinny) podczas tworzenia dziennika. Dziennik zawiera szczegółowe informacje o problemie, który blokuje instalację.

Pierwszym działaniem, które może pomóc, jest zainstalowanie [najnowszych bibliotek środowiska uruchomieniowego](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads).

W poniższej tabeli przedstawiono numery kodów i sugerowane rozwiązania.

| Kod | Description | Rozwiązanie |
|:--|:--|:--|
| 0x17 | Instalator biblioteki wymaga aktualizacji systemu Windows, która nie została zainstalowana. | Zapoznaj się z najnowszym dziennikiem instalatora biblioteki.<br><br>Jeśli po odwołaniu `Windows8.1-KB2999226-x64.msu` następuje wiersz `Error 0x80240017: Failed to execute MSU package,` nie spełnia wymagań wstępnych dotyczących instalacji KB2999226. Postępuj zgodnie z instrukcjami zamieszczonymi w sekcji wymagań wstępnych w artykule [Uniwersalne środowisko uruchomieniowe języka C w systemie Windows](https://support.microsoft.com/kb/2999226). Aby zainstalować wymagania wstępne, może być konieczne uruchomienie usługi Windows Update i kilkukrotne ponowne uruchomienie komputera.<br><br>Uruchom ponownie Instalatora programu Microsoft Dependency Agent. |

### <a name="post-installation-issues"></a>Problemy po instalacji

#### <a name="server-doesnt-appear-in-service-map"></a>Serwer nie jest wyświetlany w Service Map

Jeśli instalacja agenta zależności zakończyła się pomyślnie, ale nie widzisz maszyny w Service Map rozwiązaniu:
* Czy agent zależności został zainstalowany pomyślnie? Możesz to zweryfikować, sprawdzając, czy usługa jest zainstalowana i uruchomiona.<br><br>
**Windows**: Wyszukaj usługę o nazwie **Microsoft Dependency Agent**.
**Linux**: Wyszukaj uruchomiony proces **Microsoft-Dependency-Agent**.

* Czy jesteś w [log Analytics warstwy Bezpłatna](https://azure.microsoft.com/pricing/details/monitor/)? Plan Bezpłatna pozwala na korzystanie z maksymalnie pięciu unikatowych maszyn w usłudze Service Map. Wszystkie kolejne maszyny nie będą wyświetlane w usłudze Service Map, nawet jeśli poprzednie pięć nie wysyła już danych.

* Czy serwer wysyła dane dziennika i wydajności do dzienników Azure Monitor? Przejdź do katalogu Azure Monitor\Logs i uruchom następujące zapytanie dla komputera: 

    ```kusto
    Usage | where Computer == "admdemo-appsvr" | summarize sum(Quantity), any(QuantityUnit) by DataType
    ```

Czy w wynikach pojawiły się wiele zdarzeń? Czy są to świeże dane? Jeśli tak, Twój agent usługi Log Analytics działa prawidłowo i komunikuje się z obszarem roboczym. Jeśli nie, sprawdź agenta na swojej maszynie: [Rozwiązywanie problemów z agentem usługi Log Analytics dla systemu Windows](../agents/agent-windows-troubleshoot.md) lub [Rozwiązywanie problemów z agentem usługi Log Analytics dla systemu Linux](../agents/agent-linux-troubleshoot.md).

#### <a name="server-appears-in-service-map-but-has-no-processes"></a>Serwer pojawia się w Service Map ale nie ma procesów

Jeśli komputer jest widoczny w Service Map, ale nie ma żadnych procesów ani danych połączenia, oznacza to, że Agent zależności został zainstalowany i uruchomiony, ale nie załadowano sterownika jądra. 

Sprawdź `C:\Program Files\Microsoft Dependency Agent\logs\wrapper.log file` (Windows) lub `/var/opt/microsoft/dependency-agent/log/service.log file` (Linux). W ostatnich wierszach pliku powinna znajdować się odpowiedź, dlaczego nie załadowano jądra. Na przykład jądro może nie być obsługiwane w systemie Linux, jeśli zostało zaktualizowane.

## <a name="suggestions"></a>Sugestie

Czy masz opinię na temat Service Map lub tej dokumentacji?  Odwiedź naszą [stronę głosową użytkownika](https://feedback.azure.com/forums/267889-log-analytics/category/184492-service-map), na której można zasugerować funkcje lub zapoznaj się z istniejącymi sugestiami.