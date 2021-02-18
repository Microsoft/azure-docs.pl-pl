---
title: Obsługa oceny VMware w Azure Migrate
description: Dowiedz się więcej o obsłudze oceny maszyn wirtualnych VMware za pomocą oceny serwera Azure Migrate.
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 11/10/2020
ms.openlocfilehash: cea13d0037a8b8074a78177794c553668d3cd31b
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100590596"
---
# <a name="support-matrix-for-vmware-assessment"></a>Macierz obsługi dla oceny oprogramowania VMware 

W tym artykule przedstawiono podsumowanie wymagań wstępnych i wymagań dotyczących obsługi w przypadku odnajdywania i oceniania maszyn wirtualnych VMware na potrzeby migracji na platformę Azure przy użyciu narzędzia do [oceny serwera Azure Migrate](migrate-services-overview.md#azure-migrate-server-assessment-tool) . Aby ocenić maszyny wirtualne VMware, należy utworzyć projekt Azure Migrate i dodać narzędzie do oceny serwera do projektu. Po dodaniu narzędzia należy wdrożyć urządzenie Azure Migrate. Urządzenie stale odnajduje maszyny lokalne i wysyła metadane maszyny oraz dane wydajności do platformy Azure. Po zakończeniu odnajdywania można zebrać odnalezione maszyny do grup i uruchomić ocenę dla grupy. 

Jeśli chcesz przeprowadzić migrację maszyn wirtualnych VMware na platformę Azure, zapoznaj się z [matrycą obsługi migracji](migrate-support-matrix-vmware-migration.md).



## <a name="limitations"></a>Ograniczenia

**Wymaganie** | **Szczegóły**
--- | ---
**Limity projektu** | Możesz utworzyć wiele projektów w ramach subskrypcji platformy Azure.<br/><br/> Można odkrywać i oceniać do 35 000 maszyn wirtualnych VMware w jednym [projekcie](migrate-support-matrix.md#azure-migrate-projects). Projekt może również obejmować serwery fizyczne i maszyny wirtualne funkcji Hyper-V, a także limity oceny dla każdego z nich.
**Odnajdywanie** | Urządzenie Azure Migrate może wykryć do 10 000 maszyn wirtualnych VMware na vCenter Server.
**Ocena** | Można dodać do 35 000 maszyn w jednej grupie.<br/><br/> W ramach jednej oceny można ocenić do 35 000 maszyn wirtualnych.

[Dowiedz się więcej](concepts-assessment-calculation.md) na temat ocen.


## <a name="vmware-requirements"></a>Wymagania dotyczące oprogramowania VMware

**VMware** | **Szczegóły**
--- | ---
**Program vCenter Server** | Komputery, które mają być odnajdywane i oceniane, muszą być zarządzane przez vCenter Server w wersji 5,5, 6,0, 6,5, 6,7 lub 7,0.<br/><br/> Odnajdywanie maszyn wirtualnych VMware przez udostępnienie szczegółów hosta ESXi w urządzeniu nie jest obecnie obsługiwane.
**Uprawnienia** | Ocena serwera wymaga vCenter Server konta tylko do odczytu na potrzeby odnajdywania i oceny.<br/><br/> Jeśli chcesz przeprowadzić odnajdywanie aplikacji lub wizualizację zależności, konto wymaga uprawnień włączonych do **Virtual Machines**  >  **operacji gościa**.

## <a name="vm-requirements"></a>Wymagania dotyczące maszyny wirtualnej
**VMware** | **Szczegóły**
--- | ---
**Maszyny wirtualne VMware** | Wszystkie systemy operacyjne można ocenić pod kątem migracji. 
**Storage** | Dyski dołączone do kontrolerów SCSI, IDE i SATA są obsługiwane.


## <a name="azure-migrate-appliance-requirements"></a>Wymagania urządzenia usługi Azure Migrate

Azure Migrate używa [urządzenia Azure Migrate](migrate-appliance.md) do odnajdowania i oceny. Urządzenie można wdrożyć jako maszynę wirtualną VMware przy użyciu szablonu komórki jajowe zaimportowanego do vCenter Server lub przy użyciu [skryptu programu PowerShell](deploy-appliance-script.md).

- Dowiedz się więcej o [wymaganiach dotyczących urządzeń](migrate-appliance.md#appliance---vmware) w oprogramowaniu VMware.
- W Azure Government należy wdrożyć urządzenie [przy użyciu skryptu](deploy-appliance-script-government.md).
- Przejrzyj adresy URL, do których urządzenie musi uzyskać dostęp w chmurach [publicznych](migrate-appliance.md#public-cloud-urls) i [rządowych](migrate-appliance.md#government-cloud-urls) .


## <a name="port-access-requirements"></a>Wymagania dotyczące dostępu do portów

**Urządzenie** | **Połączenie**
--- | ---
**Wprowadzony** | Połączenia przychodzące na porcie TCP 3389, aby zezwolić na połączenia pulpitu zdalnego z urządzeniem.<br/><br/> Połączenia przychodzące na porcie 44368 do zdalnego dostępu do aplikacji do zarządzania urządzeniami przy użyciu adresu URL: ```https://<appliance-ip-or-name>:44368``` <br/><br/>Połączenia wychodzące na porcie 443 (HTTPS) do wysyłania metadanych odnajdywania i wydajności do Azure Migrate.
**serwer vCenter** | Połączenia przychodzące na porcie TCP 443 umożliwiające urządzeniu zbieranie metadanych dotyczących konfiguracji i wydajności dla ocen. <br/><br/> Urządzenie domyślnie łączy się z programem vCenter na porcie 443. Jeśli serwer vCenter nasłuchuje na innym porcie, można zmodyfikować port podczas konfigurowania odnajdywania.
**Hosty ESXi** | Jeśli chcesz przeprowadzić [odnajdywanie aplikacji](how-to-discover-applications.md)lub [analizę zależności bez wykorzystania agentów](concepts-dependency-visualization.md#agentless-analysis), urządzenie łączy się z hostami ESXi na porcie TCP 443, w celu odnajdywania aplikacji i uruchamiania wizualizacji zależności bez agenta na maszynach wirtualnych.

## <a name="application-discovery-requirements"></a>Wymagania dotyczące odnajdywania aplikacji

Oprócz odnajdywania maszyn oceny serwera mogą wykrywać aplikacje, role i funkcje uruchomione na maszynach. Odnajdywanie spisu aplikacji umożliwia zidentyfikowanie i zaplanowanie ścieżki migracji dopasowanej do obciążeń lokalnych. 

**Pomoc techniczna** | **Szczegóły**
--- | ---
**Obsługiwane maszyny** | Obecnie obsługiwane tylko w przypadku maszyn wirtualnych VMware. Na każdym urządzeniu Azure Migrate można odnajdywać aplikacje zainstalowane na maksymalnie 10000 maszynach wirtualnych VMware.
**Systemy operacyjne** | Obsługa maszyn wirtualnych z systemami Windows i Linux.
**Wymagania dotyczące maszyny wirtualnej** | Narzędzia VMware muszą być zainstalowane i uruchomione na maszynach wirtualnych, na których mają być odnajdywane aplikacje. <br/><br/> Wersja narzędzi VMware musi być nowsza niż 10.2.0.<br/><br/> Na maszynach wirtualnych musi być zainstalowany program PowerShell w wersji 2,0 lub nowszej.
**Odnajdywanie** | Informacje o aplikacjach zainstalowanych na maszynie wirtualnej są zbierane z vCenter Server przy użyciu narzędzi VMware zainstalowanych na maszynie wirtualnej. Urządzenie zbiera informacje o aplikacji z vCenter Server przy użyciu interfejsów API vSphere. Odnajdowanie aplikacji jest bezagentem. Nic nie jest zainstalowane na maszynach wirtualnych, a urządzenie nie łączy się bezpośrednio z maszynami wirtualnymi. Usługa WMI/SSH powinna być włączona i dostępna na maszynach wirtualnych.
**vCenter** | VCenter Server konto tylko do odczytu używane do oceny, wymaga uprawnień włączonych do **Virtual Machines**  >  **operacji gościa**, aby można było korzystać z maszyny wirtualnej w celu odnajdywania aplikacji.
**Dostęp do maszyny wirtualnej** | Funkcja odnajdywania aplikacji musi mieć konto użytkownika lokalnego na maszynie wirtualnej w celu odnajdywania aplikacji.<br/><br/> Azure Migrate obecnie obsługuje użycie jednego poświadczenia dla wszystkich serwerów z systemem Windows i jedno poświadczenie dla wszystkich serwerów z systemem Linux.<br/><br/> Tworzysz konto użytkownika-gościa dla maszyn wirtualnych z systemem Windows oraz normalne/normalne konto użytkownika (dostęp sudo) dla wszystkich maszyn wirtualnych z systemem Linux.
**Dostęp do portu** | Urządzenie Azure Migrate musi mieć możliwość nawiązania połączenia z portem TCP 443 na hostach ESXi z uruchomionymi maszynami wirtualnymi, na których mają zostać odnajdywane aplikacje. VCenter Server zwraca połączenie hosta ESXI, aby pobrać plik zawierający informacje o aplikacji.



## <a name="dependency-analysis-requirements-agentless"></a>Wymagania dotyczące analizy zależności (bez wykorzystania agentów)

[Analiza zależności](concepts-dependency-visualization.md) pomaga identyfikować zależności między maszynami lokalnymi, które mają zostać poddane ocenie i zmigrować na platformę Azure. W tabeli zestawiono wymagania dotyczące konfigurowania analizy zależności bez agenta. 

**Pomoc techniczna** | **Szczegóły**
--- | --- 
**Obsługiwane maszyny** | Obecnie obsługiwane tylko w przypadku maszyn wirtualnych VMware.
**Maszyny wirtualne z systemem Windows** | Windows Server 2016<br/> Windows Server 2012 z dodatkiem R2<br/> Windows Server 2012<br/> Windows Server 2008 R2 (64-bitowy).<br/>Microsoft Windows Server 2008 (32-bitowe). 
**Maszyny wirtualne z systemem Linux** | Red Hat Enterprise Linux 7, 6, 5<br/> Ubuntu Linux 14,04, 16,04<br/> Debian 7, 8<br/> Oracle Linux 6, 7<br/> CentOS 5, 6, 7.<br/> SUSE Linux Enterprise Server 11 i nowszych.
**Wymagania dotyczące maszyny wirtualnej** | Narzędzia VMware (nowsze niż 10.2.0) muszą być zainstalowane i uruchomione na maszynach wirtualnych, które mają być analizowane.<br/><br/> Na maszynach wirtualnych musi być zainstalowany program PowerShell w wersji 2,0 lub nowszej.
**Metoda odnajdywania** |  Informacje o zależnościach między maszynami wirtualnymi są zbierane z vCenter Server przy użyciu narzędzi VMware zainstalowanych na maszynie wirtualnej. Urządzenie zbiera informacje z vCenter Server przy użyciu interfejsów API vSphere. Odnajdywanie jest bez agentów. Nic nie jest zainstalowane na maszynie wirtualnej, a urządzenie nie łączy się bezpośrednio z maszynami wirtualnymi. Usługa WMI/SSH powinna być włączona i dostępna na maszynach wirtualnych.
**konto vCenter** | Konto tylko do odczytu używane przez Azure Migrate do oceny wymaga uprawnień włączonych do **Virtual Machines > operacji gościa**.
**Uprawnienia maszyny wirtualnej z systemem Windows** |  Konto (administrator lokalny lub domena) z uprawnieniami administratora lokalnego na maszynach wirtualnych.
**Konto systemu Linux** | Konto użytkownika root lub konto z tymi uprawnieniami dla plików/bin/netstat i/bin/ls: CAP_DAC_READ_SEARCH i CAP_SYS_PTRACE.<br/><br/> Te funkcje należy ustawić przy użyciu następujących poleceń: <br/><br/> sudo setcap CAP_DAC_READ_SEARCH, CAP_SYS_PTRACE = EP/bin/ls<br/><br/> sudo setcap CAP_DAC_READ_SEARCH, CAP_SYS_PTRACE = EP/bin/netstat
**Dostęp do portu** | Urządzenie Azure Migrate musi mieć możliwość nawiązania połączenia z portem TCP 443 na hostach ESXI, na których są uruchomione maszyny wirtualne, których zależności mają być odnajdywane. VCenter Server zwraca połączenie hosta ESXI, aby pobrać plik zawierający informacje o zależnościach.


## <a name="dependency-analysis-requirements-agent-based"></a>Wymagania dotyczące analizy zależności (oparte na agentach)

[Analiza zależności](concepts-dependency-visualization.md) pomaga identyfikować zależności między maszynami lokalnymi, które mają zostać poddane ocenie i zmigrować na platformę Azure. W tabeli zestawiono wymagania dotyczące konfigurowania analizy zależności opartej na agentach. 

**Wymaganie** | **Szczegóły** 
--- | --- 
**Przed wdrożeniem** | Należy mieć projekt Azure Migrate na miejscu z Azure Migrate: Narzędzie do oceny serwera dodane do projektu.<br/><br/>  Wizualizacja zależności jest wdrażana po skonfigurowaniu urządzenia Azure Migrate w celu odnalezienia maszyn lokalnych<br/><br/> [Dowiedz się, jak](create-manage-projects.md) utworzyć projekt po raz pierwszy.<br/> [Dowiedz się, jak](how-to-assess.md) dodać narzędzie do oceny do istniejącego projektu.<br/> Dowiedz się, jak skonfigurować urządzenie Azure Migrate na potrzeby oceny serwerów fizycznych ( [Hyper-V](how-to-set-up-appliance-hyper-v.md)) lub [VMware](how-to-set-up-appliance-vmware.md).
**Obsługiwane maszyny** | Obsługiwane przez wszystkie maszyny.
**Azure Government** | Wizualizacja zależności nie jest dostępna w Azure Government.
**Log Analytics** | Azure Migrate używa rozwiązania [Service map](../azure-monitor/vm/service-map.md) w [dziennikach Azure monitor](../azure-monitor/logs/log-query-overview.md) na potrzeby wizualizacji zależności.<br/><br/> Istnieje skojarzenie nowego lub istniejącego obszaru roboczego Log Analytics z projektem Azure Migrate. Obszaru roboczego dla projektu Azure Migrate nie można modyfikować po jego dodaniu. <br/><br/> Obszar roboczy musi znajdować się w tej samej subskrypcji co projekt Azure Migrate.<br/><br/> Obszar roboczy musi znajdować się w regionach Wschodnie stany USA, Azja Południowo-Wschodnia lub Europa Zachodnia. Obszary robocze w innych regionach nie mogą być skojarzone z projektem.<br/><br/> Obszar roboczy musi znajdować się w regionie, w którym [jest obsługiwana Service map](../azure-monitor/vm/vminsights-configure-workspace.md#supported-regions).<br/><br/> W Log Analytics obszar roboczy skojarzony z Azure Migrate jest otagowany przy użyciu klucza projektu migracji oraz nazwy projektu.
**Wymagani agenci** | Na każdej maszynie, którą chcesz analizować, zainstaluj następujących agentów:<br/><br/> [Program Microsoft Monitoring Agent (MMA)](../azure-monitor/agents/agent-windows.md).<br/> [Agent zależności](../azure-monitor/agents/agents-overview.md#dependency-agent).<br/><br/> Jeśli maszyny lokalne nie są połączone z Internetem, należy pobrać i zainstalować na nich bramę Log Analytics.<br/><br/> Dowiedz się więcej na temat instalowania [agenta zależności](how-to-create-group-machine-dependencies.md#install-the-dependency-agent) i [MMA](how-to-create-group-machine-dependencies.md#install-the-mma).
**Log Analytics obszar roboczy** | Obszar roboczy musi znajdować się w tej samej subskrypcji co projekt Azure Migrate.<br/><br/> Azure Migrate obsługuje obszary robocze znajdujące się w regionach Wschodnie stany USA, Azja Południowo-Wschodnia i Europa Zachodnia.<br/><br/>  Obszar roboczy musi znajdować się w regionie, w którym [jest obsługiwana Service map](../azure-monitor/vm/vminsights-configure-workspace.md#supported-regions).<br/><br/> Obszaru roboczego dla projektu Azure Migrate nie można modyfikować po jego dodaniu.
**Koszty** | W rozwiązaniu Service Map nie są naliczane żadne opłaty za pierwsze 180 dni (od dnia skojarzenia obszaru roboczego Log Analytics z projektem Azure Migrate)/<br/><br/> Po upływie 180 dni będą naliczane standardowe opłaty za korzystanie z usługi Log Analytics.<br/><br/> Użycie dowolnego rozwiązania innego niż Service Map w skojarzonym Log Analytics obszarze roboczym spowoduje naliczenie [opłat standardowych](https://azure.microsoft.com/pricing/details/log-analytics/) za log Analytics.<br/><br/> Po usunięciu projektu Azure Migrate obszar roboczy nie zostanie usunięty wraz z nim. Po usunięciu projektu Service Map użycie nie jest bezpłatne, a każdy węzeł będzie obciążany zgodnie z płatną warstwą Log Analytics obszarze roboczym/<br/><br/>Jeśli masz projekty, które zostały utworzone przed Azure Migrate ogólnej dostępności (GA-28 lutego 2018), być może nastąpiły dodatkowe opłaty za Service Map. Aby zapewnić płatność tylko przez 180 dni, zalecamy utworzenie nowego projektu, ponieważ istniejące obszary robocze przed rozliczeniem są nadal płatne.
**Zarządzanie** | Podczas rejestrowania agentów w obszarze roboczym należy używać identyfikatora i klucza dostarczonego przez projekt Azure Migrate.<br/><br/> Możesz użyć obszaru roboczego Log Analytics poza Azure Migrate.<br/><br/> Jeśli usuniesz skojarzony projekt Azure Migrate, obszar roboczy nie zostanie automatycznie usunięty. [Usuń je ręcznie](../azure-monitor/logs/manage-access.md).<br/><br/> Nie usuwaj obszaru roboczego utworzonego przez Azure Migrate, chyba że usuniesz Azure Migrate projekt. Jeśli to zrobisz, funkcja wizualizacji zależności nie będzie działać zgodnie z oczekiwaniami.
**Łączność z Internetem** | Jeśli maszyny nie są połączone z Internetem, należy zainstalować na nich bramę Log Analytics.
**Azure Government** | Analiza zależności oparta na agentach nie jest obsługiwana.


## <a name="next-steps"></a>Następne kroki

- [Zapoznaj](best-practices-assessment.md) się z najlepszymi rozwiązaniami dotyczącymi tworzenia ocen.
- [Przygotowanie do](./tutorial-discover-vmware.md) oceny oprogramowania VMware.