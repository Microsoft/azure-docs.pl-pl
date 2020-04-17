---
title: Obsługa oceny VMware w usłudze Azure Migrate
description: Dowiedz się więcej o obsłudze oceny maszyn wirtualnych VMware za pomocą oceny serwera migracji usługi Azure.
ms.topic: conceptual
ms.date: 04/15/2020
ms.openlocfilehash: 8a09562f14b95256ee9c2b5ba7d9c308cde66397
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81532208"
---
# <a name="support-matrix-for-vmware-assessment"></a>Macierz wsparcia dla oceny VMware 

W tym artykule podsumowano wymagania wstępne i wymagania dotyczące pomocy technicznej podczas oceny maszyn wirtualnych VMware do migracji na platformę Azure przy użyciu narzędzia Azure Migrate:Server Assessment](migrate-services-overview.md#azure-migrate-server-assessment-tool). Jeśli chcesz przeprowadzić migrację maszyn wirtualnych VMware na platformę Azure, przejrzyj [macierz pomocy technicznej migracji](migrate-support-matrix-vmware-migration.md).

Aby ocenić maszyny wirtualne VMware, należy utworzyć projekt migracji platformy Azure, a następnie dodać narzędzie oceny serwera do projektu. Po dodaniu narzędzia można wdrożyć [urządzenie migracji platformy Azure](migrate-appliance.md). Urządzenie stale odnajduje maszyny lokalne i wysyła metadane maszyny i dane o wydajności na platformę Azure. Po zakończeniu odnajdywania można zebrać odnalezione maszyny do grup i uruchomić ocenę dla grupy.

## <a name="limitations"></a>Ograniczenia

**Pomoc techniczna** | **Szczegóły**
--- | ---
**Limity projektów** | Można utworzyć wiele projektów w ramach subskrypcji platformy Azure.<br/><br/> W jednym [projekcie](migrate-support-matrix.md#azure-migrate-projects)można odkryć i ocenić do 35 000 maszyn wirtualnych VMware. Projekt może również zawierać serwery fizyczne i maszyny wirtualne funkcji Hyper-V, do limitów oceny dla każdego z nich.
**Odnajdywania** | Urządzenie migracji platformy Azure może odnajdować do 10 000 maszyn wirtualnych VMware na serwerze vCenter.
**Ocena** | W jednej grupie można dodać do 35 000 maszyn.<br/><br/> W jednej ocenie można ocenić do 35 000 maszyn wirtualnych.

[Dowiedz się więcej](concepts-assessment-calculation.md) o ocenach.


## <a name="application-discovery"></a>Odnajdywanie aplikacji

Oprócz odnajdowania maszyn, Ocena serwera może odnajdywać aplikacje, rolę i funkcje uruchomione na komputerach. Odnajdowanie zasobów reklamowych aplikacji umożliwia identyfikowanie i planowanie ścieżki migracji dostosowanej do obciążeń lokalnych. 

**Pomoc techniczna** | **Szczegóły**
--- | ---
**Obsługiwane maszyny** | Odnajdowanie aplikacji jest obecnie obsługiwane tylko dla maszyn wirtualnych VMware.
**Odnajdywania** | Odnajdowanie aplikacji jest bez agenta. Używa poświadczeń gościa komputera i zdalnie uzyskuje dostęp do maszyn przy użyciu wywołań WMI i SSH.
**Obsługa maszyn wirtualnych** | Odnajdowanie aplikacji jest obsługiwane dla wszystkich wersji systemu Windows i Linux.
**Poświadczenia vCenter** | Odnajdowanie aplikacji wymaga konta serwera vCenter z dostępem tylko do odczytu i uprawnieniami włączonymi dla maszyn wirtualnych > operacji gościa.
**Poświadczenia maszyny Wirtualnej** | Odnajdowanie aplikacji obsługuje obecnie użycie jednego poświadczenia dla wszystkich serwerów systemu Windows i jednego poświadczenia dla wszystkich serwerów systemu Linux.<br/><br/> Utworzysz konto użytkownika gościa dla maszyn wirtualnych systemu Windows i zwykłe konto użytkownika (dostęp nie sudo) dla wszystkich maszyn wirtualnych z systemem Linux.
**Narzędzia VMware** | Narzędzia VMware muszą być zainstalowane i uruchomione na maszynach wirtualnych, które chcesz odkryć. <br/> Wersja narzędzi VMware musi być późniejsza niż 10.2.0.
**PowerShell** | Maszyny wirtualne muszą mieć zainstalowany program PowerShell w wersji 2.0 lub nowszej.
**Dostęp do portu** | Na hostach ESXi z uruchomionymi maszynami wirtualnymi, które chcesz odnajdować, urządzenie migracji platformy Azure musi mieć możliwość nawiązania połączenia z portem TCP 443.
**Limity** | Aby odnajdować aplikacje, można odnajdywać do 10000 maszyn wirtualnych na każdym urządzeniu migracji platformy Azure.



## <a name="vmware-requirements"></a>Wymagania dotyczące VMware

**Vmware** | **Szczegóły**
--- | ---
**Maszyny wirtualne VMware** | Ocena jest obsługiwana dla wszystkich systemów operacyjnych Windows i Linux.
**Program vCenter Server** | Maszyny, które chcesz odnajdować i ocenić, muszą być zarządzane przez serwer vCenter Server w wersji 5.5, 6.0, 6.5 lub 6.7.
**Uprawnienia (ocena)** | konto tylko do odczytu serwera vCenter.
**Uprawnienia (odnajdowanie aplikacji)** | Konto serwera vCenter z dostępem tylko do odczytu i uprawnieniami włączonymi dla **maszyn wirtualnych > operacji gościa**.
**Uprawnienia (wizualizacja zależności)** | Konto serwera center z dostępem tylko do odczytu i uprawnieniami włączonymi dla **operacji gościa maszyn** > **wirtualnych**.


## <a name="azure-migrate-appliance-requirements"></a>Wymagania urządzenia usługi Azure Migrate

Usługa Azure Migrate używa [urządzenia migracji platformy Azure](migrate-appliance.md) do odnajdowania i oceny. Urządzenie można wdrożyć jako maszynę wirtualną VMWare przy użyciu szablonu OVA, zaimportowanego do serwera vCenter Server lub [skryptu programu PowerShell](deploy-appliance-script.md).

- Dowiedz się więcej o [wymaganiach dotyczących urządzeń](migrate-appliance.md#appliance---vmware) dla VMware.
- Dowiedz się więcej o adresach URL, do których urządzenie musi mieć dostęp w chmurach [publicznych](migrate-appliance.md#public-cloud-urls) i [rządowych.](migrate-appliance.md#government-cloud-urls)
- W usłudze Azure Government należy wdrożyć urządzenie przy użyciu skryptu.


## <a name="port-access"></a>Dostęp do portu

**Urządzenie** | **Połączenia**
--- | ---
Urządzenia | Połączenia przychodzące na porcie TCP 3389 umożliwiające zdalne połączenia pulpitu z urządzeniem.<br/><br/> Połączenia przychodzące na porcie 44368 w celu zdalnego dostępu do aplikacji do zarządzania urządzeniami przy użyciu adresu URL:```https://<appliance-ip-or-name>:44368``` <br/><br/>Połączenia wychodzące na porcie 443 (HTTPS), aby wysłać metadane odnajdywania i wydajności do usługi Azure Migrate.
Serwer vCenter | Połączenia przychodzące na porcie TCP 443, aby umożliwić urządzeniu zbieranie metadanych konfiguracji i wydajności dla ocen. <br/><br/> Urządzenie domyślnie łączy się z vCenter na porcie 443. Jeśli serwer vCenter nasłuchuje na innym porcie, można zmodyfikować port podczas konfigurowania odnajdywania.
Hosty ESXi (analiza zależności od odnajdywania aplikacji/bezgentowa) | Jeśli chcesz wykonać [odnajdowanie aplikacji](how-to-discover-applications.md) lub [analizę zależności bez agenta,](concepts-dependency-visualization.md#agentless-analysis)urządzenie łączy się z hostami ESXi na porcie TCP 443, aby odnajdować aplikacje, aby odnajdować aplikacje i uruchomić wizualizację zależności bez agenta na maszynach wirtualnych.

## <a name="agentless-dependency-analysis-requirements"></a>Wymagania dotyczące analizy zależności bez agenta

[Analiza zależności](concepts-dependency-visualization.md) ułatwia identyfikowanie zależności między komputerami lokalnymi, które chcesz ocenić i przeprowadzić migrację na platformę Azure. W tabeli podsumowano wymagania dotyczące konfigurowania analizy zależności bez agenta. 

**Wymaganie** | **Szczegóły**
--- | --- 
**Przed wdrożeniem** | Powinieneś mieć projekt migracji platformy Azure w miejscu, z narzędzia oceny serwera dodane do projektu.<br/><br/>  Wizualizacja zależności jest wdrażana po skonfigurowaniu urządzenia migracji platformy Azure w celu odnajdowania lokalnych komputerów VMWare.<br/><br/> [Dowiedz się, jak](create-manage-projects.md) utworzyć projekt po raz pierwszy.<br/> [Dowiedz się, jak](how-to-assess.md) dodać narzędzie do oceny do istniejącego projektu.<br/> [Dowiedz się, jak](how-to-set-up-appliance-vmware.md) skonfigurować urządzenie migracji platformy Azure do oceny maszyn wirtualnych VMware.
**Obsługa maszyn wirtualnych** | Obecnie obsługiwane tylko dla maszyn wirtualnych VMware.
**Maszyny wirtualne z systemem Windows** | Windows Server 2016<br/> Windows Server 2012 R2<br/> Windows Server 2012<br/> Windows Server 2008 R2 (64-bitowy).
**Konto systemu Windows** |  Do analizy zależności urządzenie migracji platformy Azure potrzebuje konta administratora lokalnego lub domeny, aby uzyskać dostęp do maszyn wirtualnych systemu Windows.
**Maszyny wirtualne z systemem Linux** | Red Hat Enterprise Linux 7, 6, 5<br/> Ubuntu Linux 14.04, 16.04<br/> Debian 7, 8<br/> Oracle Linux 6, 7<br/> CentOS 5, 6, 7.
**Konto Linuksa** | Do analizy zależności na komputerach z systemem Linux urządzenie usługi Azure Migrate musi mieć konto użytkownika z uprawnieniami root.<br/><br/> Alternatywnie konto użytkownika potrzebuje tych uprawnień do plików /bin/netstat i /bin/ls: CAP_DAC_READ_SEARCH i CAP_SYS_PTRACE.
**Wymaganych agentów** | Nie jest wymagany agent na komputerach, które chcesz analizować.
**Narzędzia VMware** | Narzędzia VMware Tools (później niż 10.2) muszą być zainstalowane i uruchomione na każdej maszynie wirtualnej, którą chcesz przeanalizować.
**Poświadczenia serwera vCenter** | Wizualizacja zależności wymaga konta serwera vCenter Server z dostępem tylko do odczytu i uprawnieniami włączonymi dla maszyn wirtualnych > operacji gościa. 
**PowerShell** | Maszyny wirtualne muszą mieć zainstalowany program PowerShell w wersji 2.0 lub wyższej.
**Dostęp do portu** | Na hostach ESXi z uruchomionymi maszynami wirtualnymi, które chcesz przeanalizować, urządzenie migracji platformy Azure musi mieć możliwość nawiązania połączenia z portem TCP 443.


## <a name="agent-based-dependency-analysis-requirements"></a>Wymagania analizy zależności oparte na agentach

[Analiza zależności](concepts-dependency-visualization.md) ułatwia identyfikowanie zależności między komputerami lokalnymi, które chcesz ocenić i przeprowadzić migrację na platformę Azure. W tabeli podsumowano wymagania dotyczące konfigurowania analizy zależności opartej na agentach. 

**Wymaganie** | **Szczegóły** 
--- | --- 
**Przed wdrożeniem** | Powinieneś mieć projekt migracji platformy Azure w miejscu, z narzędzia Azure Migrate: Server Assessment dodane do projektu.<br/><br/>  Wizualizacja zależności jest wdrażana po skonfigurowaniu urządzenia migracji platformy Azure w celu odnajdowania komputerów lokalnych<br/><br/> [Dowiedz się, jak](create-manage-projects.md) utworzyć projekt po raz pierwszy.<br/> [Dowiedz się, jak](how-to-assess.md) dodać narzędzie do oceny do istniejącego projektu.<br/> Dowiedz się, jak skonfigurować urządzenie migracji platformy Azure do oceny serwerów [hyper-V,](how-to-set-up-appliance-hyper-v.md) [VMware](how-to-set-up-appliance-vmware.md)lub fizycznych.
**Azure Government** | Wizualizacja zależności nie jest dostępna w usłudze Azure Dla Instytucji.
**Log Analytics** | Usługa Azure Migrate używa rozwiązania [mapy usług](../operations-management-suite/operations-management-suite-service-map.md) w [dziennikach usługi Azure Monitor](../log-analytics/log-analytics-overview.md) do wizualizacji zależności.<br/><br/> Nowy lub istniejący obszar roboczy usługi Log Analytics jest skojarzony z projektem migracji platformy Azure. Obszaru roboczego dla projektu migracji platformy Azure nie można zmodyfikować po jego dodaniu. <br/><br/> Obszar roboczy musi być w tej samej subskrypcji co projekt migracji platformy Azure.<br/><br/> Obszar roboczy musi znajdować się w regionach Wschodnich Stanów Zjednoczonych, Azji Południowo-Wschodniej lub Europy Zachodniej. Obszarów roboczych w innych regionach nie można skojarzyć z projektem.<br/><br/> Obszar roboczy musi znajdować się w regionie, w którym [jest obsługiwana mapa usługi](../azure-monitor/insights/vminsights-enable-overview.md#prerequisites).<br/><br/> W usłudze Log Analytics obszar roboczy skojarzony z programem Azure Migrate jest oznaczony kluczem projektu migracji i nazwą projektu.
**Wymaganych agentów** | Na każdym komputerze, który chcesz przeanalizować, zainstaluj następujące środki:<br/><br/> [Agent monitorowania firmy Microsoft (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows).<br/> [Agent zależności](../azure-monitor/platform/agents-overview.md#dependency-agent).<br/><br/> Jeśli komputery lokalne nie są połączone z Internetem, należy pobrać i zainstalować na nich bramę usługi Log Analytics.<br/><br/> Dowiedz się więcej o instalowaniu [agenta zależności](how-to-create-group-machine-dependencies.md#install-the-dependency-agent) i [programu MMA](how-to-create-group-machine-dependencies.md#install-the-mma).
**Obszar roboczy usługi Log Analytics** | Obszar roboczy musi być w tej samej subskrypcji co projekt migracji platformy Azure.<br/><br/> Usługa Azure Migrate obsługuje obszary robocze zamieszkałe w regionach wschodnich stanów USA, Azji Południowo-Wschodniej i Europy Zachodniej.<br/><br/>  Obszar roboczy musi znajdować się w regionie, w którym [jest obsługiwana mapa usługi](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-enable-overview#prerequisites).<br/><br/> Obszaru roboczego dla projektu migracji platformy Azure nie można zmodyfikować po jego dodaniu.
**Koszty** | Rozwiązanie mapy usług nie ponosi żadnych opłat przez pierwsze 180 dni (od dnia skojarzenia obszaru roboczego usługi Log Analytics z projektem migracji platformy Azure)/<br/><br/> Po upływie 180 dni będą naliczane standardowe opłaty za korzystanie z usługi Log Analytics.<br/><br/> Przy użyciu dowolnego rozwiązania innego niż Mapa usługi w skojarzonym obszarze roboczym usługi Log Analytics poniesie [standardowe opłaty](https://azure.microsoft.com/pricing/details/log-analytics/) za usługę Log Analytics.<br/><br/> Po usunięciu projektu migracji platformy Azure obszar roboczy nie jest usuwany wraz z nim. Po usunięciu projektu użycie mapy usługi nie jest bezpłatne, a każdy węzeł zostanie obciążony zgodnie z płatną warstwą obszaru roboczego usługi Log Analytics/<br/><br/>Jeśli masz projekty utworzone przed migracją platformy Azure ogólnej dostępności (GA- 28 lutego 2018), być może ponieśli dodatkowe opłaty mapy usługi. Aby zapewnić płatność tylko po 180 dniach, zaleca się utworzenie nowego projektu, ponieważ istniejące obszary robocze przed ga są nadal obciążalne.
**Zarządzanie** | Podczas rejestrowania agentów w obszarze roboczym, należy użyć identyfikatora i klucza dostarczonego przez projekt migracji platformy Azure.<br/><br/> Można użyć obszaru roboczego usługi Log Analytics poza programem Azure Migrate.<br/><br/> Jeśli usuniesz skojarzony projekt migracji platformy Azure, obszar roboczy nie zostanie automatycznie usunięty. [Usuń go ręcznie](../azure-monitor/platform/manage-access.md).<br/><br/> Nie usuwaj obszaru roboczego utworzonego przez usługę Azure Migrate, chyba że usuniesz projekt migracji platformy Azure. Jeśli to zrobisz, funkcja wizualizacji zależności nie będzie działać zgodnie z oczekiwaniami.
**Łączność z Internetem** | Jeśli komputery nie są połączone z Internetem, należy zainstalować na nich bramę usługi Log Analytics.
**Azure Government** | Analiza zależności oparta na agentach nie jest obsługiwana.


## <a name="next-steps"></a>Następne kroki

- [Zapoznaj się z](best-practices-assessment.md) najlepszymi praktykami tworzenia ocen.
- Przygotuj się do oceny [VMware.](tutorial-prepare-vmware.md)
