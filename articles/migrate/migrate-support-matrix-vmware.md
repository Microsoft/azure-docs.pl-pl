---
title: Obsługa oceny VMware w Azure Migrate
description: Informacje o pomocy technicznej dotyczącej oceny serwerów działających w środowisku VMware z Azure Migrate odnajdywania i oceny
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 03/17/2021
ms.openlocfilehash: 943223ee99838b2b6d6de7eecb3c3b8c06d3562c
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/22/2021
ms.locfileid: "104773593"
---
# <a name="support-matrix-for-vmware-assessment"></a>Macierz obsługi dla oceny oprogramowania VMware 

W tym artykule przedstawiono podsumowanie wymagań wstępnych i wymagań dotyczących pomocy technicznej w przypadku odnajdywania i oceniania serwerów działających w środowisku VMware na potrzeby migracji do platformy Azure za pomocą narzędzia [Azure Migrate do odnajdywania i oceny](migrate-services-overview.md#azure-migrate-server-assessment-tool) . Aby ocenić serwery, należy utworzyć projekt, który dodaje do projektu narzędzie do odnajdywania i oceny Azure Migrate:. Po dodaniu narzędzia należy wdrożyć urządzenie Azure Migrate. Urządzenie stale odnajduje serwery lokalne i wysyła metadane konfiguracji i wydajności do platformy Azure. Po zakończeniu odnajdywania można zebrać odnalezione serwery do grup i uruchomić ocenę dla grupy.

Jeśli chcesz migrować serwery VMware na platformę Azure, zapoznaj się z [matrycą obsługi migracji](migrate-support-matrix-vmware-migration.md).



## <a name="limitations"></a>Ograniczenia

**Wymaganie** | **Szczegóły**
--- | ---
**Limity projektu** | Możesz utworzyć wiele projektów w ramach subskrypcji platformy Azure.<br/><br/> Można odkrywać i oceniać maksymalnie 50 000 serwerów ze środowiska VMware w jednym [projekcie](migrate-support-matrix.md#azure-migrate-projects). Projekt może również obejmować serwery fizyczne i serwery ze środowiska Hyper-V, do których obowiązują limity oceny.
**Odnajdywanie** | Urządzenie Azure Migrate może wykryć do 10 000 serwerów na vCenter Server.
**Ocena** | Można dodać maksymalnie 35 000 serwerów w jednej grupie.<br/><br/> W ramach jednej oceny można ocenić do 35 000 serwerów.

[Dowiedz się więcej](concepts-assessment-calculation.md) na temat ocen.


## <a name="vmware-requirements"></a>Wymagania dotyczące oprogramowania VMware

**VMware** | **Szczegóły**
--- | ---
**Program vCenter Server** | Serwery, które mają być odnajdywane i oceniane, muszą być zarządzane przez vCenter Server w wersji 5,5, 6,0, 6,5, 6,7 lub 7,0.<br/><br/> Odnajdywanie serwerów przez udostępnienie szczegółów hosta ESXi w urządzeniu nie jest obecnie obsługiwane.
**Uprawnienia** | Azure Migrate: odnajdywanie i Ocena potrzebują vCenter Server konta tylko do odczytu na potrzeby odnajdywania i oceny.<br/><br/> Jeśli chcesz przeprowadzić wykrywanie spisu oprogramowania i analizy zależności bez wykorzystania agentów, konto wymaga uprawnień włączonych do **Virtual Machines**  >  **operacji gościa**.

## <a name="server-requirements"></a>Wymagania dotyczące serwera
**VMware** | **Szczegóły**
--- | ---
**Obsługiwane systemy operacyjne** | Wszystkie systemy operacyjne Windows i Linux można ocenić pod kątem migracji.
**Storage** | Dyski dołączone do kontrolerów SCSI, IDE i SATA są obsługiwane.


## <a name="azure-migrate-appliance-requirements"></a>Wymagania urządzenia usługi Azure Migrate

Azure Migrate używa [urządzenia Azure Migrate](migrate-appliance.md) do odnajdowania i oceny. Urządzenie można wdrożyć jako serwer w środowisku VMware przy użyciu szablonu komórki jajowe zaimportowanego do vCenter Server lub przy użyciu [skryptu programu PowerShell](deploy-appliance-script.md).

- Dowiedz się więcej o [wymaganiach dotyczących urządzeń](migrate-appliance.md#appliance---vmware) w oprogramowaniu VMware.
- W Azure Government należy wdrożyć urządzenie [przy użyciu skryptu](deploy-appliance-script-government.md).
- Przejrzyj adresy URL, do których urządzenie musi uzyskać dostęp w chmurach [publicznych](migrate-appliance.md#public-cloud-urls) i [rządowych](migrate-appliance.md#government-cloud-urls) .


## <a name="port-access-requirements"></a>Wymagania dotyczące dostępu do portów

**Urządzenie** | **Połączenie**
--- | ---
**Wprowadzony** | Połączenia przychodzące na porcie TCP 3389, aby zezwolić na połączenia pulpitu zdalnego z urządzeniem.<br/><br/> Połączenia przychodzące na porcie 44368 do zdalnego dostępu do aplikacji do zarządzania urządzeniami przy użyciu adresu URL: ```https://<appliance-ip-or-name>:44368``` <br/><br/>Połączenia wychodzące na porcie 443 (HTTPS) do wysyłania metadanych odnajdywania i wydajności do Azure Migrate.
**serwer vCenter** | Połączenia przychodzące na porcie TCP 443 umożliwiające urządzeniu zbieranie metadanych dotyczących konfiguracji i wydajności dla ocen. <br/><br/> Urządzenie domyślnie łączy się z programem vCenter na porcie 443. Jeśli serwer vCenter nasłuchuje na innym porcie, można zmodyfikować port podczas konfigurowania odnajdywania.
**Hosty ESXi** | Jeśli chcesz przeprowadzić [odnajdywanie spisu oprogramowania](how-to-discover-applications.md)lub [analizy zależności bez wykorzystania agentów](concepts-dependency-visualization.md#agentless-analysis), urządzenie łączy się z hostami ESXi na porcie TCP 443 w celu odnajdywania spisu oprogramowania i zależności na serwerach.

## <a name="application-discovery-requirements"></a>Wymagania dotyczące odnajdywania aplikacji

Oprócz odnajdywania serwerów, Azure Migrate: odnajdywanie i ocenianie może wykryć Spis oprogramowania uruchomiony na serwerach. Funkcja odnajdywania aplikacji umożliwia identyfikację i zaplanowanie ścieżki migracji dopasowanej do obciążeń lokalnych.

**Pomoc techniczna** | **Szczegóły**
--- | ---
**Obsługiwane serwery** | Obecnie obsługiwane tylko w przypadku serwerów w środowisku programu VMware. Odnajdywanie aplikacji można przeprowadzić na maksymalnie 10000 serwerach, z każdego urządzenia Azure Migrate.
**Systemy operacyjne** | Obsługiwane są serwery z uruchomionymi wszystkimi wersjami systemów Windows i Linux.
**Wymagania dotyczące maszyny wirtualnej** | Aby przeprowadzić odnajdywanie spisu oprogramowania, narzędzia VMware muszą być zainstalowane i uruchomione na serwerach. <br/><br/> Wersja narzędzi VMware musi być nowsza niż 10.2.0.<br/><br/> Na serwerach z systemem Windows musi być zainstalowany program PowerShell w wersji 2,0 lub nowszej.
**Odnajdywanie** | Odnajdywanie aplikacji na serwerach jest wykonywane z vCenter Server przy użyciu narzędzi VMware zainstalowanych na serwerach. Urządzenie zbiera informacje o spisie oprogramowania z vCenter Server przy użyciu interfejsów API vSphere. Odnajdowanie aplikacji jest bezagentem. Żaden Agent nie jest zainstalowany na serwerze, a urządzenie nie łączy się bezpośrednio z serwerami. Usługi WMI i SSH powinny być włączone i dostępne odpowiednio na serwerach z systemami Windows i Linux.
**konto użytkownika vCenter Server** | VCenter Server konto tylko do odczytu używane do oceny, wymaga uprawnień włączonych do **Virtual Machines**  >  **operacji gościa**, aby można było korzystać z serwerów na potrzeby odnajdywania aplikacji.
**Dostęp do serwera** | Można dodać wiele poświadczeń domen i domen nienależących do domeny (Windows/Linux) do programu Configuration Manager na potrzeby odnajdywania aplikacji.<br/><br/> Musisz mieć konto użytkownika-gościa dla serwerów z systemem Windows, a normalne/normalne konto użytkownika (dostęp sudo) dla wszystkich serwerów z systemem Linux.
**Dostęp do portu** | Urządzenie Azure Migrate musi mieć możliwość nawiązania połączenia z portem TCP 443 na hostach ESXi na serwerach, na których chcesz przeprowadzić odnajdywanie aplikacji. VCenter Server zwraca połączenie hosta ESXi, aby pobrać plik zawierający szczegóły spisu oprogramowania.

## <a name="requirements-for-discovery-of-sql-server-instances-and-databases"></a>Wymagania dotyczące odnajdywania wystąpień SQL Server i baz danych

> [!Note]
> Odnajdywanie i Ocena SQL Server wystąpień i baz danych działających w środowisku VMware jest teraz w wersji zapoznawczej. Aby wypróbować tę funkcję, użyj [**tego linku**](https://aka.ms/AzureMigrate/SQL) w celu utworzenia projektu w regionie **Australia Wschodnia**. Jeśli masz już projekt w regionie Australia Wschodnia i chcesz wypróbować tę funkcję, upewnij się, że zostały spełnione te [**wymagania wstępne**](how-to-discover-sql-existing-project.md) w portalu.

[Funkcja odnajdywania aplikacji](how-to-discover-applications.md) identyfikuje wystąpienia SQL Server. Korzystając z tych informacji, urządzenie próbuje nawiązać połączenie z odpowiednimi wystąpieniami SQL Server za pośrednictwem uwierzytelniania systemu Windows lub poświadczeń uwierzytelniania SQL Server znajdujących się na urządzeniu. Po nawiązaniu połączenia urządzenie zbiera dane dotyczące konfiguracji i wydajności SQL Server wystąpień i baz danych. Dane konfiguracji SQL Server są aktualizowane co 24 godziny, a dane wydajności są przechwytywane co 30 sekund.

**Pomoc techniczna** | **Szczegóły**
--- | ---
**Obsługiwane serwery** | Obecnie obsługiwane tylko w przypadku serwerów SQL w środowisku VMware. Możesz wykryć do 300 wystąpień SQL Server lub 6000 baz danych SQL, w zależności od tego, co jest mniejsze.
**Serwery z systemem Windows** | Obsługiwane są systemy Windows Server 2008 i nowsze.
**Serwery z systemem Linux** | Obecnie nieobsługiwane.
**Mechanizm uwierzytelniania** | Obsługiwane jest uwierzytelnianie systemu Windows i SQL Server. W Menedżerze konfiguracji urządzeń można podać poświadczenia obu typów uwierzytelniania.
**Dostęp do programu SQL Server** | Azure Migrate wymaga konta użytkownika systemu Windows, które jest członkiem roli serwera sysadmin.
**Wersje programu SQL Server** | Obsługiwane są SQL Server 2008 i nowsze.
**Wersje SQL Server** | Obsługiwane są wersje Enterprise, standard, Developer i Express.
**Obsługiwana konfiguracja SQL** | Obsługiwane jest obecnie odnajdowanie tylko autonomicznych wystąpień SQL Server i odpowiednich baz danych.<br/> Identyfikacja klastra trybu failover i zawsze włączona Grupa dostępności nie jest obsługiwana.
**Obsługiwane usługi SQL** | Obsługiwany jest tylko aparat bazy danych SQL Server. <br/> Odnajdywanie SQL Server Reporting Services (SSRS), SQL Server Integration Services (SSIS) i SQL Server Analysis Services (SSAS) nie jest obsługiwane.

> [!Note]
> Azure Migrate zaszyfruje komunikację między urządzeniem Azure Migrate i SQL Server wystąpieniami źródła (z właściwością Szyfruj połączenie ustawioną na wartość TRUE). Te połączenia są szyfrowane za pomocą [**TrustServerCertificate**](https://docs.microsoft.com/dotnet/api/system.data.sqlclient.sqlconnectionstringbuilder.trustservercertificate) (wartość true). Warstwa transportu będzie używać protokołu SSL do szyfrowania kanału i pomijania łańcucha certyfikatów w celu zweryfikowania zaufania. Serwer urządzenia musi być skonfigurowany do [**zaufania głównego urzędu certyfikacji**](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine).<br/>
Jeśli podczas uruchamiania nie zainicjowano obsługi administracyjnej certyfikatu na serwerze, SQL Server generuje certyfikat z podpisem własnym, który jest używany do szyfrowania pakietów logowania. [**Dowiedz się więcej**](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine).

## <a name="dependency-analysis-requirements-agentless"></a>Wymagania dotyczące analizy zależności (bez wykorzystania agentów)

[Analiza zależności](concepts-dependency-visualization.md) pomaga identyfikować zależności między serwerami lokalnymi, które mają być oceniane i migrowane do platformy Azure. W tabeli zestawiono wymagania dotyczące konfigurowania analizy zależności bez agenta.

**Pomoc techniczna** | **Szczegóły**
--- | --- 
**Obsługiwane serwery** | Obecnie obsługiwane tylko w przypadku serwerów w środowisku programu VMware.
**Serwery z systemem Windows** | Windows Server 2016<br/> Windows Server 2012 z dodatkiem R2<br/> Windows Server 2012<br/> Windows Server 2008 R2 (64-bitowy).<br/>Microsoft Windows Server 2008 (32-bitowe).
**Serwery z systemem Linux** | Red Hat Enterprise Linux 7, 6, 5<br/> Ubuntu Linux 14,04, 16,04<br/> Debian 7, 8<br/> Oracle Linux 6, 7<br/> CentOS 5, 6, 7.<br/> SUSE Linux Enterprise Server 11 i nowszych.
**Wymagania dotyczące serwera** | Narzędzia VMware (nowsze niż 10.2.0) muszą być zainstalowane i uruchomione na serwerach, które mają być analizowane.<br/><br/> Na serwerach musi być zainstalowany program PowerShell w wersji 2,0 lub nowszej.
**Metoda odnajdywania** |  Informacje o zależnościach między serwerami są zbierane z vCenter Server przy użyciu narzędzi VMware zainstalowanych na serwerze. Urządzenie zbiera informacje z vCenter Server przy użyciu interfejsów API vSphere. Na serwerze nie zainstalowano żadnego agenta, a urządzenie nie łączy się bezpośrednio z serwerami. Usługi WMI i SSH powinny być włączone i dostępne odpowiednio na serwerach z systemami Windows i Linux.
**konto vCenter** | Konto tylko do odczytu używane przez Azure Migrate do oceny wymaga uprawnień włączonych do **Virtual Machines > operacji gościa**.
**Uprawnienia systemu Windows Server** |  Konto użytkownika (lokalne lub domeny) z uprawnieniami administracyjnymi na serwerach.
**Konto systemu Linux** | Konto użytkownika root lub konto z tymi uprawnieniami dla plików/bin/netstat i/bin/ls: CAP_DAC_READ_SEARCH i CAP_SYS_PTRACE.<br/><br/> Te funkcje należy ustawić przy użyciu następujących poleceń: <br/><br/> sudo setcap CAP_DAC_READ_SEARCH, CAP_SYS_PTRACE = EP/bin/ls<br/><br/> sudo setcap CAP_DAC_READ_SEARCH, CAP_SYS_PTRACE = EP/bin/netstat
**Dostęp do portu** | Urządzenie Azure Migrate musi mieć możliwość nawiązania połączenia z portem TCP 443 na hostach ESXi, na których są uruchomione serwery, których zależności mają być odnajdywane. VCenter Server zwraca połączenie hosta ESXi, aby pobrać plik zawierający dane zależności.


## <a name="dependency-analysis-requirements-agent-based"></a>Wymagania dotyczące analizy zależności (oparte na agentach)

[Analiza zależności](concepts-dependency-visualization.md) pomaga identyfikować zależności między serwerami lokalnymi, które mają być oceniane i migrowane do platformy Azure. W tabeli zestawiono wymagania dotyczące konfigurowania analizy zależności opartej na agentach.

**Wymaganie** | **Szczegóły** 
--- | --- 
**Przed wdrożeniem** | Należy mieć projekt na miejscu z Azure Migrate: odnajdywanie i narzędzie oceny dodane do projektu.<br/><br/>  Wizualizacja zależności jest wdrażana po skonfigurowaniu urządzenia Azure Migrate w celu odnalezienia serwerów lokalnych.<br/><br/> [Dowiedz się, jak](create-manage-projects.md) utworzyć projekt po raz pierwszy.<br/> [Dowiedz się, jak](how-to-assess.md) dodać narzędzie do odnajdywania i oceny do istniejącego projektu.<br/> Dowiedz się, jak skonfigurować urządzenie Azure Migrate na potrzeby oceny serwerów fizycznych ( [Hyper-V](how-to-set-up-appliance-hyper-v.md)) lub [VMware](how-to-set-up-appliance-vmware.md).
**Obsługiwane serwery** | Obsługiwane dla wszystkich serwerów w środowisku lokalnym.
**Log Analytics** | Azure Migrate używa rozwiązania [Service map](../azure-monitor/insights/service-map.md) w [dziennikach Azure monitor](../azure-monitor/log-query/log-query-overview.md) na potrzeby wizualizacji zależności.<br/><br/> Nowy lub istniejący obszar roboczy Log Analytics należy skojarzyć z projektem. Nie można zmodyfikować obszaru roboczego dla projektu po jego dodaniu. <br/><br/> Obszar roboczy musi znajdować się w tej samej subskrypcji co projekt.<br/><br/> Obszar roboczy musi znajdować się w regionach Wschodnie stany USA, Azja Południowo-Wschodnia lub Europa Zachodnia. Obszary robocze w innych regionach nie mogą być skojarzone z projektem.<br/><br/> Obszar roboczy musi znajdować się w regionie, w którym [jest obsługiwana Service map](../azure-monitor/insights/vminsights-configure-workspace.md#supported-regions).<br/><br/> W Log Analytics obszar roboczy skojarzony z Azure Migrate jest otagowany przy użyciu klucza projektu i nazwy projektu.
**Wymagani agenci** | Na każdym serwerze, który chcesz analizować, zainstaluj następujących agentów:<br/><br/> [Program Microsoft Monitoring Agent (MMA)](../azure-monitor/platform/agent-windows.md).<br/> [Agent zależności](../azure-monitor/platform/agents-overview.md#dependency-agent).<br/><br/> Jeśli serwery lokalne nie są połączone z Internetem, należy pobrać i zainstalować na nich bramę Log Analytics.<br/><br/> Dowiedz się więcej na temat instalowania [agenta zależności](how-to-create-group-machine-dependencies.md#install-the-dependency-agent) i [MMA](how-to-create-group-machine-dependencies.md#install-the-mma).
**Log Analytics obszar roboczy** | Obszar roboczy musi znajdować się w tej samej subskrypcji co projekt.<br/><br/> Azure Migrate obsługuje obszary robocze znajdujące się w regionach Wschodnie stany USA, Azja Południowo-Wschodnia i Europa Zachodnia.<br/><br/>  Obszar roboczy musi znajdować się w regionie, w którym [jest obsługiwana Service map](../azure-monitor/insights/vminsights-configure-workspace.md#supported-regions).<br/><br/> Nie można zmodyfikować obszaru roboczego dla projektu po jego dodaniu.
**Koszty** | Rozwiązanie Service Map nie powoduje naliczania opłat za pierwsze 180 dni (od dnia skojarzenia obszaru roboczego Log Analytics z projektem)/<br/><br/> Po upływie 180 dni będą naliczane standardowe opłaty za korzystanie z usługi Log Analytics.<br/><br/> Użycie dowolnego rozwiązania innego niż Service Map w skojarzonym Log Analytics obszarze roboczym spowoduje naliczenie [opłat standardowych](https://azure.microsoft.com/pricing/details/log-analytics/) za log Analytics.<br/><br/> Po usunięciu projektu nie jest on usuwany razem z nim. Po usunięciu projektu Service Map użycie nie jest bezpłatne, a każdy węzeł będzie obciążany zgodnie z płatną warstwą Log Analytics obszarze roboczym/<br/><br/>Jeśli masz projekty, które zostały utworzone przed Azure Migrate ogólnej dostępności (GA-28 lutego 2018), być może nastąpiły dodatkowe opłaty za Service Map. Aby zapewnić płatność tylko przez 180 dni, zalecamy utworzenie nowego projektu, ponieważ istniejące obszary robocze przed rozliczeniem są nadal płatne.
**Zarządzanie** | Po zarejestrowaniu agentów w obszarze roboczym należy użyć identyfikatora i klucza podanego w projekcie.<br/><br/> Możesz użyć obszaru roboczego Log Analytics poza Azure Migrate.<br/><br/> Jeśli usuniesz skojarzony projekt, obszar roboczy nie zostanie automatycznie usunięty. [Usuń je ręcznie](../azure-monitor/platform/manage-access.md).<br/><br/> Nie usuwaj obszaru roboczego utworzonego przez Azure Migrate, chyba że usuniesz ten projekt. Jeśli to zrobisz, funkcja wizualizacji zależności nie będzie działać zgodnie z oczekiwaniami.
**Łączność z Internetem** | Jeśli serwery nie są połączone z Internetem, należy zainstalować na nich bramę Log Analytics.
**Azure Government** | Analiza zależności oparta na agentach nie jest obsługiwana.


## <a name="next-steps"></a>Następne kroki

- [Zapoznaj](best-practices-assessment.md) się z najlepszymi rozwiązaniami dotyczącymi tworzenia ocen.
- [Przygotowanie do](./tutorial-discover-vmware.md) oceny oprogramowania VMware.