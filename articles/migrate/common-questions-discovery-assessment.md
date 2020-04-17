---
title: Pytania dotyczące analizy odnajdywania, oceny i zależności w obszarze Migracja platformy Azure
description: Uzyskaj odpowiedzi na typowe pytania dotyczące odnajdywania, oceny i analizy zależności w usłudze Azure Migrate.
ms.topic: conceptual
ms.date: 04/15/2020
ms.openlocfilehash: 700e5318b66cdf4993a17b1dae85fb43f75ab035
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81529777"
---
# <a name="discovery-assessment-and-dependency-analysis---common-questions"></a>Analiza odnajdowania, oceny i zależności — często zadawane pytania

Ten artykuł zawiera odpowiedzi na typowe pytania dotyczące analizy odnajdywania, oceny i zależności w usłudze Azure Migrate. Jeśli masz inne pytania, sprawdź następujące zasoby:

- [Ogólne pytania](resources-faq.md) dotyczące migracji platformy Azure
- Pytania dotyczące [urządzenia migracji platformy Azure](common-questions-appliance.md)
- Pytania dotyczące [migracji serwera](common-questions-server-migration.md)
- Odpowiedzi na pytania na [forum migracji platformy Azure](https://aka.ms/AzureMigrateForum)


## <a name="what-geographies-are-supported-for-discovery-and-assessment-with-azure-migrate"></a>Jakie lokalizacje geograficzne są obsługiwane do odnajdowania i oceny za pomocą usługi Azure Migrate?

Przejrzyj obsługiwane obszary geograficzne dla chmur [publicznych](migrate-support-matrix.md#supported-geographies-public-cloud) i [rządowych](migrate-support-matrix.md#supported-geographies-azure-government).


## <a name="how-many-vms-can-i-discover-with-an-appliance"></a>Ile maszyn wirtualnych można odkryć za pomocą urządzenia?

Za pomocą jednego urządzenia można wykryć do 10 000 maszyn wirtualnych VMware, do 5000 maszyn wirtualnych funkcji Hyper V i do 250 serwerów fizycznych. Jeśli masz więcej maszyn, przeczytaj o [skalowaniu oceny funkcji Hyper-V,](scale-hyper-v-assessment.md) [skalowaniu oceny VMware](scale-vmware-assessment.md)lub [skalowaniu oceny serwera fizycznego.](scale-physical-assessment.md)


## <a name="the-size-of-my-vm-changed-can-i-run-an-assessment-again"></a>Rozmiar mojej maszyny Wirtualnej uległ zmianie. Czy mogę ponownie przeprowadzić ocenę?

Urządzenie migracji platformy Azure stale zbiera informacje o środowisku lokalnym.  Ocena jest migawką punktu w czasie lokalnych maszyn wirtualnych. Jeśli zmienisz ustawienia na maszynie wirtualnej, którą chcesz ocenić, użyj opcji przeliczanie, aby zaktualizować ocenę o najnowsze zmiany.

## <a name="how-do-i-discover-vms-in-a-multitenant-environment"></a>Jak odnajdyć maszyny wirtualne w środowisku wielodostępnym?

- **VMware:** Jeśli środowisko jest współużytkowane przez dzierżawców i nie chcesz odnajdywać maszyn wirtualnych dzierżawcy w subskrypcji innej dzierżawy, utwórz poświadczenia VMware vCenter Server, które mogą uzyskiwać dostęp tylko do maszyn wirtualnych, które chcesz odkryć. Następnie użyj tych poświadczeń po uruchomieniu odnajdywania w urządzeniu migracji platformy Azure.
- **Funkcja Hyper-V:** Discovery używa poświadczeń hosta funkcji Hyper-V. Jeśli maszyny wirtualne współużytkuje ten sam host funkcji Hyper-V, obecnie nie ma możliwości oddzielenia odnajdywania.  

## <a name="do-i-need-vcenter-server"></a>Czy potrzebuję serwera vCenter?

Tak, usługa Azure Migrate wymaga serwera vCenter Server w środowisku VMware do odnajdywania. Usługa Azure Migrate nie obsługuje odnajdowania hostów ESXi, które nie są zarządzane przez serwer vCenter Server.

## <a name="what-are-the-sizing-options"></a>Jakie są opcje zmiany rozmiaru?

W przypadku zmiany rozmiaru jako lokalnego usługa Azure Migrate nie uwzględnia danych dotyczących wydajności maszyny Wirtualnej do oceny. Usługa Azure Migrate ocenia rozmiary maszyn wirtualnych na podstawie konfiguracji lokalnej. W zależności od rozmiaru opartego na wydajności rozmiary są oparte na danych dotyczących wykorzystania.

Na przykład jeśli lokalna maszyna wirtualna ma cztery rdzenie i 8 GB pamięci przy 50% wykorzystaniu procesora CPU i 50% wykorzystaniu pamięci:
- Rozmiary jako lokalne zalecają jednostkę SKU maszyny Wirtualnej platformy Azure, która ma cztery rdzenie i 8 GB pamięci.
- Rozmiary oparte na wydajności zaleci jednostkę SKU maszyny Wirtualnej, która ma dwa rdzenie i 4 GB pamięci, ponieważ uwzględnia się procent wykorzystania.

Podobnie rozmiar dysku zależy od kryteriów zmiany rozmiaru i typu magazynu:
- Jeśli kryteria zmiany rozmiaru są oparte na wydajności, a typ magazynu jest automatyczny, usługa Azure Migrate uwzględnia wartości kont IOPS i przepływności dysku, gdy identyfikuje typ dysku docelowego (Standardowy lub Premium).
- Jeśli kryteria rozmiaru są oparte na wydajności, a typem magazynu jest Usługa Premium, usługa Azure Migrate zaleca jednostkę SKU dysku premium na podstawie rozmiaru dysku lokalnego. Ta sama logika jest stosowana do rozmiaru dysku, gdy rozmiar jest jako lokalny, a typ magazynu jest standard lub Premium.

## <a name="does-performance-history-and-utilization-affect-sizing"></a>Czy historia wydajności i wykorzystanie wpływa na rozmiary?

Tak, historia wydajności i wykorzystanie wpływają na rozmiary w usłudze Azure Migrate.

### <a name="performance-history"></a>Historia wydajności

Tylko w przypadku zmiany rozmiaru opartego na wydajności usługa Azure Migrate zbiera historię wydajności komputerów lokalnych, a następnie używa jej do zalecania rozmiaru maszyny wirtualnej i typu dysku na platformie Azure:

1. Urządzenie stale profiluje środowisko lokalne w celu zbierania danych wykorzystania w czasie rzeczywistym co 20 sekund.
1. Urządzenie zwija zebrane próbki 20-sekundowe i używa ich do utworzenia pojedynczego punktu danych co 15 minut.
1. Aby utworzyć punkt danych, urządzenie wybiera wartość szczytową ze wszystkich próbek 20-sekundowych.
1. Urządzenie wysyła punkt danych do platformy Azure.

### <a name="utilization"></a>Wykorzystanie

Podczas tworzenia oceny na platformie Azure, w zależności od czasu trwania wydajności i wartości percentyla historii wydajności, która jest ustawiona, usługa Azure Migrate oblicza efektywną wartość wykorzystania, a następnie używa jej do zmiany rozmiaru.

Na przykład jeśli ustawisz czas trwania wydajności na jeden dzień, a wartość percentyla na 95 percentyl, usługa Azure Migrate sortuje 15-minutowe przykładowe punkty wysyłane przez moduł zbierający dla ostatniego dnia w porządku rosnącym. Wybiera 95-ty percentyl wartość jako efektywne wykorzystanie.

Przy użyciu 95-ta wartość percentyla zapewnia, że wartości odstające są ignorowane. Wartość odstania może zostać uwzględniona, jeśli usługa Azure Migrate używa 99 percentyla. Aby wybrać szczytowe użycie dla okresu bez utraty żadnych wartości odstającej, ustaw migrację platformy Azure, aby użyć 99 percentyla.

## <a name="how-are-import-based-assessments-different-from-assessments-with-discovery-source-as-appliance"></a>Czym różnią się oceny oparte na imporcie od ocen ze źródłem odnajdywania jako urządzeniem?

Oceny oparte na imporcie to oceny utworzone za pomocą komputerów importowanych do usługi Azure Migrate przy użyciu pliku CSV. Do importowania są obowiązkowe tylko cztery pola: nazwa serwera, rdzenie, pamięć i system operacyjny. Oto kilka rzeczy, na które należy zwrócić uwagę: 
 - Kryteria gotowości są mniej rygorystyczne w ocenach opartych na imporcie parametru typu rozruchowego. Jeśli typ rozruchu nie jest podany, zakłada się, że urządzenie ma typ rozruchu systemu BIOS, a urządzenie nie jest oznaczone jako **Warunkowo gotowe.** W ocenach ze źródłem odnajdywania jako urządzenie gotowość jest oznaczona jako **Warunkowo gotowy,** jeśli brakuje typu rozruchu. Ta różnica w obliczaniu gotowości jest, ponieważ użytkownicy mogą nie mieć wszystkie informacje na komputerach na wczesnych etapach planowania migracji, gdy są wykonywane oceny oparte na imporcie. 
 - Oceny importu oparte na wydajności używają wartości wykorzystania podanej przez użytkownika do obliczeń rozmiaru prawego. Ponieważ wartość wykorzystania jest dostarczana przez użytkownika, **historia wydajności** i opcje **wykorzystania percentyla** są wyłączone we właściwościach oceny. W ocenach ze źródłem odnajdywania jako urządzenie wybrana wartość percentyla jest pobierana na podstawie danych dotyczących wydajności zebranych przez urządzenie.

## <a name="what-is-dependency-visualization"></a>Co to jest wizualizacja zależności?

Wizualizacja zależności może pomóc w ocenie grup maszyn wirtualnych do migracji z większą pewnością. Wizualizacja zależności sprawdza zależności komputera przed uruchomieniem oceny. Pomaga upewnić się, że nic nie pozostaje w tyle i pomaga uniknąć nieoczekiwanych awarii podczas migracji na platformę Azure. Usługa Azure Migrate używa rozwiązania mapy usług w usłudze Azure Monitor, aby włączyć wizualizację zależności. [Dowiedz się więcej](concepts-dependency-visualization.md).

> [!NOTE]
> Wizualizacja zależności nie jest dostępna w usłudze Azure Dla Instytucji.

## <a name="whats-the-difference-between-agent-based-and-agentless"></a>Jaka jest różnica między agentem a agentem?

Różnice między wizualizacją bez agenta a wizualizacją opartą na agentach są podsumowane w tabeli.

**Wymaganie** | **Bez agenta** | **Oparte na agentach**
--- | --- | ---
Pomoc techniczna | Ta opcja jest obecnie w wersji zapoznawczej i jest dostępna tylko dla maszyn wirtualnych VMware. [Przejrzyj](migrate-support-matrix-vmware.md#agentless-dependency-analysis-requirements) obsługiwane systemy operacyjne. | Ogólnie rzecz biorąc dostępność (GA).
Agent | Nie ma potrzeby instalowania agentów na komputerach, które chcesz sprawdzić krzyżowo. | Agenci, którzy mają być zainstalowani na każdym komputerze lokalnym, który chcesz przeanalizować: [Agent monitorowania firmy Microsoft (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows)i agent [zależności](https://docs.microsoft.com/azure/azure-monitor/platform/agents-overview#dependency-agent). 
Wymagania wstępne | [Przejrzyj](concepts-dependency-visualization.md#agentless-analysis) wymagania wstępne i wymagania dotyczące wdrażania. | [Przejrzyj](concepts-dependency-visualization.md#agent-based-analysis) wymagania wstępne i wymagania dotyczące wdrażania.
Log Analytics | Niewymagane. | Usługa Azure Migrate używa rozwiązania [mapy usług](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-service-map) w [dziennikach usługi Azure Monitor](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) do wizualizacji zależności. [Dowiedz się więcej](concepts-dependency-visualization.md#agent-based-analysis).
Jak to działa | Przechwytuje dane połączenia TCP na komputerach włączonych do wizualizacji zależności. Po odnajdowaniu zbiera dane w odstępach pięciu minut. | Agenci mapy usług zainstalowane na komputerze zbierają dane o procesach TCP i połączeniach przychodzących/wychodzących dla każdego procesu.
Dane | Nazwa serwera komputera źródłowego, proces, nazwa aplikacji.<br/><br/> Nazwa docelowego serwera komputera, proces, nazwa aplikacji i port. | Nazwa serwera komputera źródłowego, proces, nazwa aplikacji.<br/><br/> Nazwa docelowego serwera komputera, proces, nazwa aplikacji i port.<br/><br/> Liczba połączeń, opóźnienia i informacje o transferze danych są zbierane i dostępne dla zapytań usługi Log Analytics. 
Wizualizacja | Mapa zależności pojedynczego serwera może być oglądana przez okres od jednej godziny do 30 dni. | Mapa zależności pojedynczego serwera.<br/><br/> Mapę można oglądać tylko przez godzinę.<br/><br/> Mapa zależności grupy serwerów.<br/><br/> Dodawanie i usuwanie serwerów w grupie z widoku mapy.
Eksport danych | Obecnie nie można pobrać w formacie tabelarycznym. | Dane można wyszukiwać za pomocą usługi Log Analytics.

## <a name="do-i-pay-for-dependency-visualization"></a>Czy płacę za wizualizację zależności?

Nie. Dowiedz się więcej o [cenach migracji platformy Azure](https://azure.microsoft.com/pricing/details/azure-migrate/).

## <a name="what-do-i-install-for-agent-based-dependency-visualization"></a>Co należy zainstalować w wizualizacji zależności opartej na agentach?

Aby użyć wizualizacji zależności opartej na agentach, pobierz i zainstaluj agentów na każdym komputerze lokalnym, który chcesz ocenić:

- [Agent monitorowania firmy Microsoft (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows)
- [Agent zależności](../azure-monitor/platform/agents-overview.md#dependency-agent)
- Jeśli masz komputery, które nie mają połączenia z Internetem, pobierz i zainstaluj na nich bramę usługi Log Analytics.

Te środki są potrzebne tylko wtedy, gdy używasz wizualizacji zależności opartej na agentach.

## <a name="can-i-use-an-existing-workspace"></a>Czy mogę użyć istniejącego obszaru roboczego?

Tak, w przypadku wizualizacji zależności opartej na agentach można dołączyć istniejący obszar roboczy do projektu migracji i użyć go do wizualizacji zależności. 

## <a name="can-i-export-the-dependency-visualization-report"></a>Czy można wyeksportować raport wizualizacji zależności?

Nie, nie można wyeksportować raportu wizualizacji zależności w wizualizacji agenta. Jednak usługa Azure Migrate używa mapy usługi i można użyć [interfejsu API REST mapy usługi,](https://docs.microsoft.com/rest/api/servicemap/machines/listconnections) aby pobrać zależności w formacie JSON.

## <a name="can-i-automate-agent-installation"></a>Czy mogę zautomatyzować instalację agenta?

W przypadku wizualizacji zależności opartych na agentach:

- Użyj [skryptu, aby zainstalować agenta zależności](../azure-monitor/insights/vminsights-enable-hybrid-cloud.md#installation-script-examples).
- W przypadku programu MMA [użyj wiersza polecenia lub automatyzacji](../azure-monitor/platform/log-analytics-agent.md#installation-and-configuration)lub użyj [skryptu](https://gallery.technet.microsoft.com/scriptcenter/Install-OMS-Agent-with-2c9c99ab).
- Oprócz skryptów można użyć narzędzi wdrażania, takich jak Program Microsoft Endpoint Configuration Manager i [Intigua,](https://www.intigua.com/getting-started-intigua-for-azure-migration) aby wdrożyć agentów.

## <a name="what-operating-systems-does-mma-support"></a>Jakie systemy operacyjne obsługuje MMA?

- Wyświetl listę [systemów operacyjnych Windows obsługiwanych przez program MMA](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-windows-operating-systems).
- Zobacz listę [systemów operacyjnych Linux, które obsługuje MMA](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems).

## <a name="can-i-visualize-dependencies-for-more-than-one-hour"></a>Czy mogę wizualizować zależności przez więcej niż godzinę?

W przypadku wizualizacji opartej na agentach można wizualizować zależności przez maksymalnie jedną godzinę. Możesz cofnąć się nawet o miesiąc do określonej daty w historii, ale maksymalny czas wizualizacji wynosi jedną godzinę. Na przykład można użyć czasu trwania w mapie zależności do wyświetlania zależności dla wczoraj, ale można wyświetlić zależności tylko dla okna jednogodzinnego. Jednak można użyć dzienników usługi Azure Monitor do [kwerendy danych zależności](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies) przez dłuższy czas.

W przypadku wizualizacji bez agenta można wyświetlić mapę zależności pojedynczego serwera z okresu od jednej godziny do 30 dni.

## <a name="can-i-visualize-dependencies-for-groups-of-more-than-10-vms"></a>Czy można wizualizować zależności dla grup liczących więcej niż 10 maszyn wirtualnych?

Można [wizualizować zależności](https://docs.microsoft.com/azure/migrate/how-to-create-group-dependencies) dla grup, które mają maksymalnie 10 maszyn wirtualnych. Jeśli masz grupę, która ma więcej niż 10 maszyn wirtualnych, zaleca się podzielenie grupy na mniejsze grupy, a następnie wizualizować zależności.

## <a name="next-steps"></a>Następne kroki

Przeczytaj [omówienie migracji platformy Azure](migrate-services-overview.md).
