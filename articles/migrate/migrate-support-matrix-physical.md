---
title: Obsługa oceny serwera fizycznego w Azure Migrate
description: Dowiedz się więcej o obsłudze oceny serwera fizycznego za pomocą oceny serwera Azure Migrate
ms.topic: conceptual
ms.date: 04/15/2020
ms.openlocfilehash: 31fd676a339a6c82cec84e0f355ac875f68a653c
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2020
ms.locfileid: "82983673"
---
# <a name="support-matrix-for-physical-server-assessment"></a>Macierz obsługi dla oceny serwera fizycznego 

W tym artykule przedstawiono podsumowanie wymagań wstępnych i wymagań dotyczących obsługi podczas oceniania serwerów fizycznych do migracji na platformę Azure za pomocą narzędzia do [oceny serwera Azure Migrate:](migrate-services-overview.md#azure-migrate-server-assessment-tool) . Jeśli chcesz migrować serwery fizyczne na platformę Azure, zapoznaj się z [matrycą obsługi migracji](migrate-support-matrix-physical-migration.md).


Aby ocenić serwery fizyczne, należy utworzyć projekt Azure Migrate i dodać narzędzie do oceny serwera do projektu. Po dodaniu narzędzia należy wdrożyć [urządzenie Azure Migrate](migrate-appliance.md). Urządzenie stale odnajduje maszyny lokalne i wysyła metadane maszyny oraz dane wydajności do platformy Azure. Po zakończeniu odnajdywania można zebrać odnalezione maszyny do grup i uruchomić ocenę dla grupy.


## <a name="limitations"></a>Ograniczenia

**Pomoc techniczna** | **Szczegóły**
--- | ---
**Limity oceny** | W jednym [Azure Migrate projekcie](migrate-support-matrix.md#azure-migrate-projects)można odnajdywać i oceniać maksymalnie 35 000 serwerów fizycznych.
**Limity projektu** | Możesz utworzyć wiele projektów w ramach subskrypcji platformy Azure. Oprócz serwerów fizycznych projekt może obejmować maszyny wirtualne VMware i maszyny wirtualne funkcji Hyper-V, a także limity oceny dla każdego z nich.
**Odnajdowa** | Urządzenie Azure Migrate może odnajdywać maksymalnie 250 serwerów fizycznych.
**Ocena** | Można dodać do 35 000 maszyn w jednej grupie.<br/><br/> W ramach jednej oceny można ocenić do 35 000 maszyn.

[Dowiedz się więcej](concepts-assessment-calculation.md) na temat ocen.

## <a name="physical-server-requirements"></a>Wymagania dotyczące serwera fizycznego

| **Pomoc techniczna**                | **Szczegóły**               
| :-------------------       | :------------------- |
| **Wdrożenie serwera fizycznego**       | Serwer fizyczny może być autonomiczny lub wdrożony w klastrze. |
| **Uprawnienia**           | **System Windows:** Musisz być administratorem domeny lub administratorem lokalnym na wszystkich serwerach z systemem Windows, które mają zostać odnajdywane. Konto użytkownika należy dodać do tych grup: Użytkownicy zarządzania zdalnego, użytkownicy monitora wydajności i Użytkownicy dzienników wydajności. <br/><br/> System **Linux:** Na serwerach z systemem Linux, które mają zostać odnajdywane, jest potrzebne konto główne. |
| **System operacyjny** | Wszystkie systemy operacyjne [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) i [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) , które są obsługiwane przez platformę Azure, z wyjątkiem systemu Windows Server 2003 i SUSE Linux.<br/><br/> Klienckie systemy operacyjne Windows 10 i Windows 8. |


## <a name="azure-migrate-appliance-requirements"></a>Wymagania urządzenia usługi Azure Migrate

Azure Migrate używa [urządzenia Azure Migrate](migrate-appliance.md) do odnajdowania i oceny. Urządzenie dla serwerów fizycznych można uruchomić na maszynie wirtualnej lub na komputerze fizycznym. 

- Dowiedz się więcej o [wymaganiach dotyczących urządzeń](migrate-appliance.md#appliance---physical) dla serwerów fizycznych.
- Uzyskaj informacje o adresach URL, do których urządzenie musi uzyskać dostęp w chmurach [publicznych](migrate-appliance.md#public-cloud-urls) i [administracji rządowej](migrate-appliance.md#government-cloud-urls) .
- Urządzenie można skonfigurować przy użyciu [skryptu programu PowerShell](how-to-set-up-appliance-physical.md) pobranego z Azure Portal.
W Azure Government Wdróż urządzenie [przy użyciu tego skryptu](deploy-appliance-script-government.md).

## <a name="port-access"></a>Dostęp do portu

Poniższa tabela zawiera podsumowanie wymagań dotyczących portów dla oceny.

**Urządzenie** | **Połączenia**
--- | ---
**Wprowadzony** | Połączenia przychodzące na porcie TCP 3389, aby zezwolić na połączenia pulpitu zdalnego z urządzeniem.<br/><br/> Połączenia przychodzące na porcie 44368, aby zdalnie uzyskiwać dostęp do aplikacji do zarządzania urządzeniami przy użyciu adresu URL:``` https://<appliance-ip-or-name>:44368 ```<br/><br/> Połączenia wychodzące na portach 443 (HTTPS), w celu wysyłania metadanych odnajdywania i wydajności do Azure Migrate.
**Serwery fizyczne** | **System Windows:** Połączenia przychodzące na portach usługi WinRM 5985 (HTTP) i 5986 (HTTPS), aby ściągnąć konfigurację i metadane wydajności z serwerów z systemem Windows. <br/><br/> System **Linux:**  Połączenia przychodzące na porcie 22 (UDP) do ściągania metadanych konfiguracji i wydajności z serwerów z systemem Linux. |

## <a name="agent-based-dependency-analysis-requirements"></a>Wymagania dotyczące analizy zależności opartej na agentach

[Analiza zależności](concepts-dependency-visualization.md) pomaga identyfikować zależności między maszynami lokalnymi, które mają zostać poddane ocenie i zmigrować na platformę Azure. W tabeli zestawiono wymagania dotyczące konfigurowania analizy zależności opartej na agentach. W przypadku serwerów fizycznych obsługiwana jest obecnie tylko analiza zależności oparta na agentach.

**Wymaganie** | **Szczegóły** 
--- | --- 
**Przed wdrożeniem** | Należy mieć projekt Azure Migrate przy użyciu narzędzia do oceny serwera dodanego do projektu.<br/><br/>  Wizualizacja zależności jest wdrażana po skonfigurowaniu urządzenia Azure Migrate w celu odnalezienia maszyn lokalnych<br/><br/> [Dowiedz się, jak](create-manage-projects.md) utworzyć projekt po raz pierwszy.<br/> [Dowiedz się, jak](how-to-assess.md) dodać narzędzie do oceny do istniejącego projektu.<br/> Dowiedz się, jak skonfigurować urządzenie Azure Migrate na potrzeby oceny serwerów fizycznych ( [Hyper-V](how-to-set-up-appliance-hyper-v.md)) lub [VMware](how-to-set-up-appliance-vmware.md).
**Azure Government** | Wizualizacja zależności nie jest dostępna w Azure Government.
**Log Analytics** | Azure Migrate używa rozwiązania [Service map](../operations-management-suite/operations-management-suite-service-map.md) w [dziennikach Azure monitor](../log-analytics/log-analytics-overview.md) na potrzeby wizualizacji zależności.<br/><br/> Istnieje skojarzenie nowego lub istniejącego obszaru roboczego Log Analytics z projektem Azure Migrate. Obszaru roboczego dla projektu Azure Migrate nie można modyfikować po jego dodaniu. <br/><br/> Obszar roboczy musi znajdować się w tej samej subskrypcji co projekt Azure Migrate.<br/><br/> Obszar roboczy musi znajdować się w regionach Wschodnie stany USA, Azja Południowo-Wschodnia lub Europa Zachodnia. Obszary robocze w innych regionach nie mogą być skojarzone z projektem.<br/><br/> Obszar roboczy musi znajdować się w regionie, w którym [jest obsługiwana Service map](../azure-monitor/insights/vminsights-enable-overview.md#prerequisites).<br/><br/> W Log Analytics obszar roboczy skojarzony z Azure Migrate jest otagowany przy użyciu klucza projektu migracji oraz nazwy projektu.
**Wymagani agenci** | Na każdej maszynie, którą chcesz analizować, zainstaluj następujących agentów:<br/><br/> [Program Microsoft Monitoring Agent (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows).<br/> [Agent zależności](../azure-monitor/platform/agents-overview.md#dependency-agent).<br/><br/> Jeśli maszyny lokalne nie są połączone z Internetem, należy pobrać i zainstalować na nich bramę Log Analytics.<br/><br/> Dowiedz się więcej na temat instalowania [agenta zależności](how-to-create-group-machine-dependencies.md#install-the-dependency-agent) i [MMA](how-to-create-group-machine-dependencies.md#install-the-mma).
**Obszar roboczy usługi Log Analytics** | Obszar roboczy musi znajdować się w tej samej subskrypcji co projekt Azure Migrate.<br/><br/> Azure Migrate obsługuje obszary robocze znajdujące się w regionach Wschodnie stany USA, Azja Południowo-Wschodnia i Europa Zachodnia.<br/><br/>  Obszar roboczy musi znajdować się w regionie, w którym [jest obsługiwana Service map](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-enable-overview#prerequisites).<br/><br/> Obszaru roboczego dla projektu Azure Migrate nie można modyfikować po jego dodaniu.
**Koszty** | W rozwiązaniu Service Map nie są naliczane żadne opłaty za pierwsze 180 dni (od dnia skojarzenia obszaru roboczego Log Analytics z projektem Azure Migrate)/<br/><br/> Po upływie 180 dni będą naliczane standardowe opłaty za korzystanie z usługi Log Analytics.<br/><br/> Użycie dowolnego rozwiązania innego niż Service Map w skojarzonym Log Analytics obszarze roboczym spowoduje naliczenie [opłat standardowych](https://azure.microsoft.com/pricing/details/log-analytics/) za log Analytics.<br/><br/> Po usunięciu projektu Azure Migrate obszar roboczy nie zostanie usunięty wraz z nim. Po usunięciu projektu Service Map użycie nie jest bezpłatne, a każdy węzeł będzie obciążany zgodnie z płatną warstwą Log Analytics obszarze roboczym/<br/><br/>Jeśli masz projekty, które zostały utworzone przed Azure Migrate ogólnej dostępności (GA-28 lutego 2018), być może nastąpiły dodatkowe opłaty za Service Map. Aby zapewnić płatność tylko przez 180 dni, zalecamy utworzenie nowego projektu, ponieważ istniejące obszary robocze przed rozliczeniem są nadal płatne.
**Zarządzanie** | Podczas rejestrowania agentów w obszarze roboczym należy używać identyfikatora i klucza dostarczonego przez projekt Azure Migrate.<br/><br/> Możesz użyć obszaru roboczego Log Analytics poza Azure Migrate.<br/><br/> Jeśli usuniesz skojarzony projekt Azure Migrate, obszar roboczy nie zostanie automatycznie usunięty. [Usuń je ręcznie](../azure-monitor/platform/manage-access.md).<br/><br/> Nie usuwaj obszaru roboczego utworzonego przez Azure Migrate, chyba że usuniesz Azure Migrate projekt. Jeśli to zrobisz, funkcja wizualizacji zależności nie będzie działać zgodnie z oczekiwaniami.
**Łączność z Internetem** | Jeśli maszyny nie są połączone z Internetem, należy zainstalować na nich bramę Log Analytics.
**Azure Government** | Analiza zależności oparta na agentach nie jest obsługiwana.

## <a name="next-steps"></a>Następne kroki

[Przygotuj się do oceny serwera fizycznego](tutorial-prepare-physical.md).
