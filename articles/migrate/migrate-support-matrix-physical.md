---
title: Obsługa fizycznej oceny serwera w usłudze Azure Migrate
description: Dowiedz się więcej o obsłudze fizycznej oceny serwera za pomocą oceny serwera migracji usługi Azure
ms.topic: conceptual
ms.date: 04/15/2020
ms.openlocfilehash: ae76a6b570ec58e71a8a1728a2a601728030f58c
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81538158"
---
# <a name="support-matrix-for-physical-server-assessment"></a>Macierz wsparcia dla fizycznej oceny serwera 

W tym artykule podsumowano wymagania wstępne i wymagania dotyczące pomocy technicznej podczas oceny serwerów fizycznych do migracji na platformę Azure przy użyciu narzędzia [Azure Migrate:Server Assessment.](migrate-services-overview.md#azure-migrate-server-assessment-tool) Jeśli chcesz przeprowadzić migrację serwerów fizycznych na platformę Azure, przejrzyj [macierz pomocy technicznej migracji](migrate-support-matrix-physical-migration.md).


Aby ocenić serwery fizyczne, należy utworzyć projekt migracji platformy Azure i dodać narzędzie Oceny serwera do projektu. Po dodaniu narzędzia można wdrożyć [urządzenie migracji platformy Azure](migrate-appliance.md). Urządzenie stale odnajduje maszyny lokalne i wysyła metadane maszyny i dane o wydajności na platformę Azure. Po zakończeniu odnajdywania można zebrać odnalezione maszyny do grup i uruchomić ocenę dla grupy.


## <a name="limitations"></a>Ograniczenia

**Pomoc techniczna** | **Szczegóły**
--- | ---
**Limity oceny** | W jednym [projekcie migracji platformy Azure](migrate-support-matrix.md#azure-migrate-projects)można odkryć i ocenić do 35 000 serwerów fizycznych.
**Limity projektów** | Można utworzyć wiele projektów w ramach subskrypcji platformy Azure. Oprócz serwerów fizycznych projekt może zawierać maszyny wirtualne VMware i maszyny wirtualne funkcji Hyper-V, do limitów oceny dla każdego z nich.
**Odnajdywania** | Urządzenie usługi Azure Migrate może odnajdować do 250 serwerów fizycznych.
**Ocena** | W jednej grupie można dodać do 35 000 maszyn.<br/><br/> W jednej ocenie można ocenić do 35 000 maszyn.

[Dowiedz się więcej](concepts-assessment-calculation.md) o ocenach.

## <a name="physical-server-requirements"></a>Wymagania dotyczące serwera fizycznego

| **Pomoc techniczna**                | **Szczegóły**               
| :-------------------       | :------------------- |
| **Wdrożenie serwera fizycznego**       | Serwer fizyczny może być autonomiczny lub wdrożony w klastrze. |
| **Uprawnienia**           | **Windows:** Potrzebujesz konta użytkownika lokalnego lub domeny na wszystkich serwerach systemu Windows, które chcesz odkryć. Konto użytkownika należy dodać do tych grup: Użytkownicy pulpitu zdalnego, Użytkownicy monitora wydajności i użytkownicy dziennika wydajności. <br/><br/> **Linux:** Potrzebujesz konta głównego na serwerach Linuksa, które chcesz odkryć. |
| **System operacyjny** | Wszystkie systemy operacyjne [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) i [Linux,](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) które są obsługiwane przez platformę Azure, z wyjątkiem systemu Windows Server 2003 i SUSE Linux.|


## <a name="azure-migrate-appliance-requirements"></a>Wymagania urządzenia usługi Azure Migrate

Usługa Azure Migrate używa [urządzenia migracji platformy Azure](migrate-appliance.md) do odnajdowania i oceny. Urządzenie dla serwerów fizycznych może działać na maszynie wirtualnej lub komputerze fizycznym. Urządzenie można skonfigurować przy użyciu skryptu programu PowerShell pobranego z witryny Azure portal.

- Dowiedz się więcej o [wymaganiach dotyczących urządzeń](migrate-appliance.md#appliance---physical) dla serwerów fizycznych.
- Dowiedz się więcej o adresach URL, do których urządzenie musi mieć dostęp w chmurach [publicznych](migrate-appliance.md#public-cloud-urls) i [rządowych.](migrate-appliance.md#government-cloud-urls)

## <a name="port-access"></a>Dostęp do portu

W poniższej tabeli podsumowano wymagania dotyczące portów do oceny.

**Urządzenie** | **Połączenia**
--- | ---
**Urządzenia** | Połączenia przychodzące na porcie TCP 3389, aby umożliwić połączenia pulpitu zdalnego z urządzeniem.<br/><br/> Połączenia przychodzące na porcie 44368, aby zdalnie uzyskać dostęp do aplikacji do zarządzania urządzeniami przy użyciu adresu URL:``` https://<appliance-ip-or-name>:44368 ```<br/><br/> Połączenia wychodzące na portach 443 (HTTPS), aby wysłać metadane odnajdywania i wydajności do usługi Azure Migrate.
**Serwery fizyczne** | **Windows:** Połączenia przychodzące na portach WinRM 5985 (HTTP) i 5986 (HTTPS), aby pobierać metadane konfiguracji i wydajności z serwerów systemu Windows. <br/><br/> **Linux:**  Połączenia przychodzące na porcie 22 (UDP), aby wyciągnąć metadane konfiguracji i wydajności z serwerów systemu Linux. |

## <a name="agent-based-dependency-analysis-requirements"></a>Wymagania analizy zależności oparte na agentach

[Analiza zależności](concepts-dependency-visualization.md) ułatwia identyfikowanie zależności między komputerami lokalnymi, które chcesz ocenić i przeprowadzić migrację na platformę Azure. W tabeli podsumowano wymagania dotyczące konfigurowania analizy zależności opartej na agentach. Obecnie tylko analiza zależności oparta na agentach jest obsługiwana dla serwerów fizycznych.

**Wymaganie** | **Szczegóły** 
--- | --- 
**Przed wdrożeniem** | Powinieneś mieć projekt migracji platformy Azure w miejscu, z narzędzia oceny serwera dodane do projektu.<br/><br/>  Wizualizacja zależności jest wdrażana po skonfigurowaniu urządzenia migracji platformy Azure w celu odnajdowania komputerów lokalnych<br/><br/> [Dowiedz się, jak](create-manage-projects.md) utworzyć projekt po raz pierwszy.<br/> [Dowiedz się, jak](how-to-assess.md) dodać narzędzie do oceny do istniejącego projektu.<br/> Dowiedz się, jak skonfigurować urządzenie migracji platformy Azure do oceny serwerów [hyper-V,](how-to-set-up-appliance-hyper-v.md) [VMware](how-to-set-up-appliance-vmware.md)lub fizycznych.
**Azure Government** | Wizualizacja zależności nie jest dostępna w usłudze Azure Dla Instytucji.
**Log Analytics** | Usługa Azure Migrate używa rozwiązania [mapy usług](../operations-management-suite/operations-management-suite-service-map.md) w [dziennikach usługi Azure Monitor](../log-analytics/log-analytics-overview.md) do wizualizacji zależności.<br/><br/> Nowy lub istniejący obszar roboczy usługi Log Analytics jest skojarzony z projektem migracji platformy Azure. Obszaru roboczego dla projektu migracji platformy Azure nie można zmodyfikować po jego dodaniu. <br/><br/> Obszar roboczy musi być w tej samej subskrypcji co projekt migracji platformy Azure.<br/><br/> Obszar roboczy musi znajdować się w regionach Wschodnich Stanów Zjednoczonych, Azji Południowo-Wschodniej lub Europy Zachodniej. Obszarów roboczych w innych regionach nie można skojarzyć z projektem.<br/><br/> Obszar roboczy musi znajdować się w regionie, w którym [jest obsługiwana mapa usługi](../azure-monitor/insights/vminsights-enable-overview.md#prerequisites).<br/><br/> W usłudze Log Analytics obszar roboczy skojarzony z programem Azure Migrate jest oznaczony kluczem projektu migracji i nazwą projektu.
**Wymaganych agentów** | Na każdym komputerze, który chcesz przeanalizować, zainstaluj następujące środki:<br/><br/> [Agent monitorowania firmy Microsoft (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows).<br/> [Agent zależności](../azure-monitor/platform/agents-overview.md#dependency-agent).<br/><br/> Jeśli komputery lokalne nie są połączone z Internetem, należy pobrać i zainstalować na nich bramę usługi Log Analytics.<br/><br/> Dowiedz się więcej o instalowaniu [agenta zależności](how-to-create-group-machine-dependencies.md#install-the-dependency-agent) i [programu MMA](how-to-create-group-machine-dependencies.md#install-the-mma).
**Obszar roboczy usługi Log Analytics** | Obszar roboczy musi być w tej samej subskrypcji co projekt migracji platformy Azure.<br/><br/> Usługa Azure Migrate obsługuje obszary robocze zamieszkałe w regionach wschodnich stanów USA, Azji Południowo-Wschodniej i Europy Zachodniej.<br/><br/>  Obszar roboczy musi znajdować się w regionie, w którym [jest obsługiwana mapa usługi](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-enable-overview#prerequisites).<br/><br/> Obszaru roboczego dla projektu migracji platformy Azure nie można zmodyfikować po jego dodaniu.
**Koszty** | Rozwiązanie mapy usług nie ponosi żadnych opłat przez pierwsze 180 dni (od dnia skojarzenia obszaru roboczego usługi Log Analytics z projektem migracji platformy Azure)/<br/><br/> Po upływie 180 dni będą naliczane standardowe opłaty za korzystanie z usługi Log Analytics.<br/><br/> Przy użyciu dowolnego rozwiązania innego niż Mapa usługi w skojarzonym obszarze roboczym usługi Log Analytics poniesie [standardowe opłaty](https://azure.microsoft.com/pricing/details/log-analytics/) za usługę Log Analytics.<br/><br/> Po usunięciu projektu migracji platformy Azure obszar roboczy nie jest usuwany wraz z nim. Po usunięciu projektu użycie mapy usługi nie jest bezpłatne, a każdy węzeł zostanie obciążony zgodnie z płatną warstwą obszaru roboczego usługi Log Analytics/<br/><br/>Jeśli masz projekty utworzone przed migracją platformy Azure ogólnej dostępności (GA- 28 lutego 2018), być może ponieśli dodatkowe opłaty mapy usługi. Aby zapewnić płatność tylko po 180 dniach, zaleca się utworzenie nowego projektu, ponieważ istniejące obszary robocze przed ga są nadal obciążalne.
**Zarządzanie** | Podczas rejestrowania agentów w obszarze roboczym, należy użyć identyfikatora i klucza dostarczonego przez projekt migracji platformy Azure.<br/><br/> Można użyć obszaru roboczego usługi Log Analytics poza programem Azure Migrate.<br/><br/> Jeśli usuniesz skojarzony projekt migracji platformy Azure, obszar roboczy nie zostanie automatycznie usunięty. [Usuń go ręcznie](../azure-monitor/platform/manage-access.md).<br/><br/> Nie usuwaj obszaru roboczego utworzonego przez usługę Azure Migrate, chyba że usuniesz projekt migracji platformy Azure. Jeśli to zrobisz, funkcja wizualizacji zależności nie będzie działać zgodnie z oczekiwaniami.
**Łączność z Internetem** | Jeśli komputery nie są połączone z Internetem, należy zainstalować na nich bramę usługi Log Analytics.
**Azure Government** | Analiza zależności oparta na agentach nie jest obsługiwana.

## <a name="next-steps"></a>Następne kroki

[Przygotuj się do oceny serwera fizycznego](tutorial-prepare-physical.md).
