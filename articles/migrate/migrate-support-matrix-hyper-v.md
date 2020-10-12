---
title: Obsługa oceny funkcji Hyper-V w Azure Migrate
description: Dowiedz się więcej o obsłudze oceny funkcji Hyper-V dzięki ocenie serwera Azure Migrate
ms.topic: conceptual
ms.date: 06/14/2020
ms.openlocfilehash: 16eeb0822a8d598c74ab5118fbd39bda84186db0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91318181"
---
# <a name="support-matrix-for-hyper-v-assessment"></a>Macierz obsługi dla oceny funkcji Hyper-V

W tym artykule przedstawiono podsumowanie wymagań wstępnych i wymagań dotyczących obsługi podczas oceniania maszyn wirtualnych funkcji Hyper-V do migracji na platformę Azure przy użyciu narzędzia do [oceny serwera Azure Migrate:](migrate-services-overview.md#azure-migrate-server-assessment-tool) . Jeśli chcesz przeprowadzić migrację maszyn wirtualnych funkcji Hyper-V do platformy Azure, zapoznaj się z [matrycą obsługi migracji](migrate-support-matrix-hyper-v-migration.md).

Aby skonfigurować ocenę maszyn wirtualnych funkcji Hyper-V, należy utworzyć projekt Azure Migrate i dodać narzędzie do oceny serwera do projektu. Po dodaniu narzędzia należy wdrożyć [urządzenie Azure Migrate](migrate-appliance.md). Urządzenie stale odnajduje maszyny lokalne i wysyła metadane maszyny oraz dane wydajności do platformy Azure. Po zakończeniu odnajdywania można zebrać odnalezione maszyny do grup i uruchomić ocenę dla grupy.


## <a name="limitations"></a>Ograniczenia

**Pomoc techniczna** | **Szczegóły**
--- | ---
**Limity oceny** | Do 35 000 maszyn wirtualnych funkcji Hyper-V można odkrywać i oceniać w jednym [Azure Migrate projekcie](migrate-support-matrix.md#azure-migrate-projects).
**Limity projektu** | Możesz utworzyć wiele projektów w ramach subskrypcji platformy Azure. Oprócz maszyn wirtualnych funkcji Hyper-V projekt może obejmować maszyny wirtualne VMware i serwery fizyczne, a także limity oceny dla każdego z nich.
**Odnajdywanie** | Urządzenie Azure Migrate może odnajdywać maksymalnie 5000 maszyn wirtualnych funkcji Hyper-V.<br/><br/> Urządzenie może połączyć się z maksymalnie 300 hostami funkcji Hyper-V.
**Ocena** | Można dodać do 35 000 maszyn w jednej grupie.<br/><br/> Można ocenić do 35 000 maszyn wirtualnych w ramach pojedynczej oceny dla grupy.

[Dowiedz się więcej](concepts-assessment-calculation.md) na temat ocen.



## <a name="hyper-v-host-requirements"></a>Wymagania dotyczące hosta funkcji Hyper-V

| **Pomoc techniczna**                | **Szczegóły**               
| :-------------------       | :------------------- |
| **Host funkcji Hyper-V**       | Host funkcji Hyper-V może być autonomiczny lub wdrożony w klastrze.<br/><br/> Na hoście funkcji Hyper-V można uruchomić system Windows Server 2019, Windows Server 2016 lub Windows Server 2012 R2. Instalacja Server Core tych systemów operacyjnych również jest obsługiwana. <br/>Nie można ocenić maszyn wirtualnych znajdujących się na hostach funkcji Hyper-V z systemem Windows Server 2012.
| **Uprawnienia**           | Wymagane są uprawnienia administratora na hoście funkcji Hyper-V. <br/> Jeśli nie chcesz przypisywać uprawnień administratora, Utwórz konto użytkownika lokalnego lub domeny, a następnie Dodaj konto użytkownika do tych grup — Użytkownicy zarządzania zdalnego, Administratorzy funkcji Hyper-V i użytkownicy monitora wydajności. |
| **Komunikacja zdalna programu PowerShell**   | [Komunikacja zdalna programu PowerShell](/powershell/module/microsoft.powershell.core/enable-psremoting?view=powershell-7) musi być włączona na każdym hoście funkcji Hyper-V. |
| **Hyper-V Replica**       | W przypadku korzystania z funkcji Hyper-V Replica (lub korzystania z wielu maszyn wirtualnych z tymi samymi identyfikatorami maszyn wirtualnych) i odnajdywania zarówno oryginalnej, jak i zreplikowanej maszyny wirtualnej przy użyciu Azure Migrate, Ocena wygenerowana przez Azure Migrate może być niedokładna. |


## <a name="vm-requirements"></a>Wymagania dotyczące maszyny wirtualnej

| **Pomoc techniczna**                  | **Szczegóły**               
| :----------------------------- | :------------------- |
| **System operacyjny** | Wszystkie systemy operacyjne można ocenić pod kątem migracji.  |
| **Integration Services**       | [Usługi integracji funkcji Hyper-V](/virtualization/hyper-v-on-windows/reference/integration-services) muszą być uruchomione na maszynach wirtualnych, które oceniasz, aby przechwycić informacje o systemie operacyjnym. |
| **Storage** | Dysk lokalny, DAS, JBOD, miejsca do magazynowania, CSV, SMB. Ten magazyn hosta funkcji Hyper-V, na którym są przechowywane pliki VHD/VHDX, jest obsługiwany. <br/> Obsługiwane są kontrolery wirtualne IDE i SCSI| 

## <a name="azure-migrate-appliance-requirements"></a>Wymagania urządzenia usługi Azure Migrate

Azure Migrate używa [urządzenia Azure Migrate](migrate-appliance.md) do odnajdowania i oceny. Urządzenie można wdrożyć przy użyciu skompresowanego wirtualnego dysku twardego funkcji Hyper-V pobranego z portalu lub za pomocą [skryptu programu PowerShell](deploy-appliance-script.md).

- Dowiedz się więcej o [wymaganiach dotyczących urządzeń](migrate-appliance.md#appliance---hyper-v) dla funkcji Hyper-V.
- Uzyskaj informacje o adresach URL, do których urządzenie musi uzyskać dostęp w chmurach [publicznych](migrate-appliance.md#public-cloud-urls) i [administracji rządowej](migrate-appliance.md#government-cloud-urls) .
- W Azure Government należy wdrożyć urządzenie [przy użyciu skryptu](deploy-appliance-script-government.md).

## <a name="port-access"></a>Dostęp do portu

Poniższa tabela zawiera podsumowanie wymagań dotyczących portów dla oceny.

**Urządzenie** | **Połączenie**
--- | ---
**Wprowadzony** | Połączenia przychodzące na porcie TCP 3389, aby zezwolić na połączenia pulpitu zdalnego z urządzeniem.<br/><br/> Połączenia przychodzące na porcie 44368 do zdalnego dostępu do aplikacji do zarządzania urządzeniami przy użyciu adresu URL: ``` https://<appliance-ip-or-name>:44368 ```<br/><br/> Połączenia wychodzące na portach 443 (HTTPS), w celu wysyłania metadanych odnajdywania i wydajności do Azure Migrate.
**Host/klaster funkcji Hyper-V** | Połączenie przychodzące na porcie WinRM 5985 (HTTP) do ściągania metadanych i danych wydajności dla maszyn wirtualnych funkcji Hyper-V przy użyciu sesji model wspólnych informacji (CIM).

## <a name="agent-based-dependency-analysis-requirements"></a>Wymagania dotyczące analizy zależności opartej na agentach

[Analiza zależności](concepts-dependency-visualization.md) pomaga identyfikować zależności między maszynami lokalnymi, które mają zostać poddane ocenie i zmigrować na platformę Azure. W tabeli zestawiono wymagania dotyczące konfigurowania analizy zależności opartej na agentach. Funkcja Hyper-V obecnie obsługuje tylko wizualizację zależności opartą na agentach. 

**Wymaganie** | **Szczegóły** 
--- | --- 
**Przed wdrożeniem** | Należy mieć projekt Azure Migrate przy użyciu narzędzia do oceny serwera dodanego do projektu.<br/><br/>  Wizualizacja zależności jest wdrażana po skonfigurowaniu urządzenia Azure Migrate w celu odnalezienia maszyn lokalnych<br/><br/> [Dowiedz się, jak](create-manage-projects.md) utworzyć projekt po raz pierwszy.<br/> [Dowiedz się, jak](how-to-assess.md) dodać narzędzie do oceny do istniejącego projektu.<br/> Dowiedz się, jak skonfigurować urządzenie Azure Migrate do oceny [maszyn wirtualnych funkcji Hyper-V](how-to-set-up-appliance-hyper-v.md).
**Azure Government** | Wizualizacja zależności nie jest dostępna w Azure Government.
**Log Analytics** | Azure Migrate używa rozwiązania [Service map](../azure-monitor/insights/service-map.md) w [dziennikach Azure monitor](../azure-monitor/log-query/log-query-overview.md) na potrzeby wizualizacji zależności.<br/><br/> Istnieje skojarzenie nowego lub istniejącego obszaru roboczego Log Analytics z projektem Azure Migrate. Obszaru roboczego dla projektu Azure Migrate nie można modyfikować po jego dodaniu. <br/><br/> Obszar roboczy musi znajdować się w tej samej subskrypcji co projekt Azure Migrate.<br/><br/> Obszar roboczy musi znajdować się w regionach Wschodnie stany USA, Azja Południowo-Wschodnia lub Europa Zachodnia. Obszary robocze w innych regionach nie mogą być skojarzone z projektem.<br/><br/> Obszar roboczy musi znajdować się w regionie, w którym [jest obsługiwana Service map](../azure-monitor/insights/vminsights-configure-workspace.md#supported-regions).<br/><br/> W Log Analytics obszar roboczy skojarzony z Azure Migrate jest otagowany przy użyciu klucza projektu migracji oraz nazwy projektu.
**Wymagani agenci** | Na każdej maszynie, którą chcesz analizować, zainstaluj następujących agentów:<br/><br/> [Program Microsoft Monitoring Agent (MMA)](../azure-monitor/platform/agent-windows.md).<br/> [Agent zależności](../azure-monitor/platform/agents-overview.md#dependency-agent).<br/><br/> Jeśli maszyny lokalne nie są połączone z Internetem, należy pobrać i zainstalować na nich bramę Log Analytics.<br/><br/> Dowiedz się więcej na temat instalowania [agenta zależności](how-to-create-group-machine-dependencies.md#install-the-dependency-agent) i [MMA](how-to-create-group-machine-dependencies.md#install-the-mma).
**Log Analytics obszar roboczy** | Obszar roboczy musi znajdować się w tej samej subskrypcji co projekt Azure Migrate.<br/><br/> Azure Migrate obsługuje obszary robocze znajdujące się w regionach Wschodnie stany USA, Azja Południowo-Wschodnia i Europa Zachodnia.<br/><br/>  Obszar roboczy musi znajdować się w regionie, w którym [jest obsługiwana Service map](../azure-monitor/insights/vminsights-configure-workspace.md#supported-regions).<br/><br/> Obszaru roboczego dla projektu Azure Migrate nie można modyfikować po jego dodaniu.
**Koszty** | W rozwiązaniu Service Map nie są naliczane żadne opłaty za pierwsze 180 dni (od dnia skojarzenia obszaru roboczego Log Analytics z projektem Azure Migrate)/<br/><br/> Po upływie 180 dni będą naliczane standardowe opłaty za korzystanie z usługi Log Analytics.<br/><br/> Użycie dowolnego rozwiązania innego niż Service Map w skojarzonym Log Analytics obszarze roboczym spowoduje naliczenie [opłat standardowych](https://azure.microsoft.com/pricing/details/log-analytics/) za log Analytics.<br/><br/> Po usunięciu projektu Azure Migrate obszar roboczy nie zostanie usunięty wraz z nim. Po usunięciu projektu Service Map użycie nie jest bezpłatne, a każdy węzeł będzie obciążany zgodnie z płatną warstwą Log Analytics obszarze roboczym/<br/><br/>Jeśli masz projekty, które zostały utworzone przed Azure Migrate ogólnej dostępności (GA-28 lutego 2018), być może nastąpiły dodatkowe opłaty za Service Map. Aby zapewnić płatność tylko przez 180 dni, zalecamy utworzenie nowego projektu, ponieważ istniejące obszary robocze przed rozliczeniem są nadal płatne.
**Zarządzanie** | Podczas rejestrowania agentów w obszarze roboczym należy używać identyfikatora i klucza dostarczonego przez projekt Azure Migrate.<br/><br/> Możesz użyć obszaru roboczego Log Analytics poza Azure Migrate.<br/><br/> Jeśli usuniesz skojarzony projekt Azure Migrate, obszar roboczy nie zostanie automatycznie usunięty. [Usuń je ręcznie](../azure-monitor/platform/manage-access.md).<br/><br/> Nie usuwaj obszaru roboczego utworzonego przez Azure Migrate, chyba że usuniesz Azure Migrate projekt. Jeśli to zrobisz, funkcja wizualizacji zależności nie będzie działać zgodnie z oczekiwaniami.
**Łączność z Internetem** | Jeśli maszyny nie są połączone z Internetem, należy zainstalować na nich bramę Log Analytics.
**Azure Government** | Analiza zależności oparta na agentach nie jest obsługiwana.

## <a name="next-steps"></a>Następne kroki

[Przygotowanie do oceny maszyn wirtualnych funkcji Hyper-V](tutorial-prepare-hyper-v.md)
