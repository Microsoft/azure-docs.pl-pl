---
title: Obsługa oceny funkcji Hyper-V w Azure Migrate
description: Dowiedz się więcej o obsłudze oceny funkcji Hyper-V dzięki Azure Migrate odnajdywania i oceny
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 03/18/2021
ms.openlocfilehash: 8531d0b2252e6ddff75509046b5a4576b99d339f
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104870793"
---
# <a name="support-matrix-for-hyper-v-assessment"></a>Macierz obsługi dla oceny funkcji Hyper-V

W tym artykule przedstawiono podsumowanie wymagań wstępnych i wymagań dotyczących obsługi w przypadku odnajdywania i oceniania serwerów lokalnych działających w środowisku funkcji Hyper-V na potrzeby migracji na platformę Azure przy użyciu narzędzia do [odnajdywania i oceny Azure Migrate:](migrate-services-overview.md#azure-migrate-discovery-and-assessment-tool) . Aby przeprowadzić migrację serwerów z funkcją Hyper-V na platformę Azure, zapoznaj się z [matrycą obsługi migracji](migrate-support-matrix-hyper-v-migration.md).

Aby skonfigurować odnajdywanie i ocenianie serwerów z funkcją Hyper-V, należy utworzyć projekt i dodać Azure Migrate: narzędzie odnajdywania i oceny do projektu. Po dodaniu narzędzia należy wdrożyć [urządzenie Azure Migrate](migrate-appliance.md). Urządzenie stale odnajduje serwery lokalne i wysyła metadane serwera oraz dane wydajności do platformy Azure. Po zakończeniu odnajdywania można zebrać odnalezione serwery do grup i uruchomić ocenę dla grupy.

## <a name="limitations"></a>Ograniczenia

**Pomoc techniczna** | **Szczegóły**
--- | ---
**Limity oceny** | Można odkrywać i oceniać maksymalnie 35 000 serwerów w jednym [projekcie](migrate-support-matrix.md#project).
**Limity projektu** | Możesz utworzyć wiele projektów w ramach subskrypcji platformy Azure. Oprócz serwerów w funkcji Hyper-V projekt może obejmować serwery w oprogramowaniu VMware i serwerach fizycznych, a także limity oceny dla każdego z nich.
**Odnajdywanie** | Urządzenie Azure Migrate może wykryć do 5000 serwerów z uruchomioną funkcją Hyper-V.<br/><br/> Urządzenie może połączyć się z maksymalnie 300 hostami funkcji Hyper-V.
**Ocena** | Można dodać maksymalnie 35 000 serwerów w jednej grupie.<br/><br/> Można ocenić do 35 000 serwerów w pojedynczej ocenie dla grupy.

[Dowiedz się więcej](concepts-assessment-calculation.md) na temat ocen.

## <a name="hyper-v-host-requirements"></a>Wymagania dotyczące hosta funkcji Hyper-V

| **Pomoc techniczna**                | **Szczegóły**
| :-------------------       | :------------------- |
| **Host funkcji Hyper-V**       | Host funkcji Hyper-V może być autonomiczny lub wdrożony w klastrze.<br/><br/> Na hoście funkcji Hyper-V można uruchomić system Windows Server 2019, Windows Server 2016 lub Windows Server 2012 R2. Te systemy operacyjne są również obsługiwane w instalacjach Server Core. <br/>Nie można ocenić serwerów znajdujących się na hostach funkcji Hyper-V z systemem Windows Server 2012.
| **Uprawnienia**           | Wymagane są uprawnienia administratora na hoście funkcji Hyper-V. <br/> Jeśli nie chcesz przypisywać uprawnień administratora, Utwórz konto użytkownika lokalnego lub domeny, a następnie Dodaj konto użytkownika do tych grup — Użytkownicy zarządzania zdalnego, Administratorzy funkcji Hyper-V i użytkownicy monitora wydajności. |
| **Komunikacja zdalna programu PowerShell**   | [Komunikacja zdalna programu PowerShell](/powershell/module/microsoft.powershell.core/enable-psremoting) musi być włączona na każdym hoście funkcji Hyper-V. |
| **Hyper-V Replica**       | Jeśli korzystasz z funkcji Hyper-V Replica (lub masz wiele serwerów z tymi samymi identyfikatorami serwera) i odnajdziesz zarówno oryginalne, jak i zreplikowane serwery przy użyciu Azure Migrate, Ocena wygenerowanego przez Azure Migrate może być niedokładna. |

## <a name="server-requirements"></a>Wymagania dotyczące serwera

| **Pomoc techniczna**                  | **Szczegóły**
| :----------------------------- | :------------------- |
| **System operacyjny** | Wszystkie systemy operacyjne można ocenić pod kątem migracji.  |
| **Integration Services**       | [Usługi integracji funkcji Hyper-V](/virtualization/hyper-v-on-windows/reference/integration-services) muszą być uruchomione na serwerach, które oceniasz, aby przechwycić informacje o systemie operacyjnym. |
| **Storage** | Dysk lokalny, DAS, JBOD, miejsca do magazynowania, CSV, SMB. Te magazyny hostów funkcji Hyper-V, na których są przechowywane pliki VHD/VHDX, są obsługiwane. <br/> Obsługiwane są kontrolery wirtualne IDE i SCSI|

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
**Host/klaster funkcji Hyper-V** | Połączenie przychodzące na porcie WinRM 5985 (HTTP) lub 5986 (HTTPS) do ściągania metadanych i danych wydajności dla serwerów w funkcji Hyper-V przy użyciu sesji model wspólnych informacji (CIM).

## <a name="agent-based-dependency-analysis-requirements"></a>Wymagania dotyczące analizy zależności opartej na agentach

[Analiza zależności](concepts-dependency-visualization.md) pomaga identyfikować zależności między serwerami lokalnymi, które mają być oceniane i migrowane do platformy Azure. W tabeli zestawiono wymagania dotyczące konfigurowania analizy zależności opartej na agentach. Funkcja Hyper-V obecnie obsługuje tylko wizualizację zależności opartą na agentach.

**Wymaganie** | **Szczegóły**
--- | --- 
**Przed wdrożeniem** | Należy mieć projekt na miejscu z Azure Migrate: odnajdywanie i narzędzie oceny dodane do projektu.<br/><br/>  Wizualizacja zależności jest wdrażana po skonfigurowaniu urządzenia Azure Migrate w celu odnalezienia serwerów lokalnych.<br/><br/> [Dowiedz się, jak](create-manage-projects.md) utworzyć projekt po raz pierwszy.<br/> [Dowiedz się, jak](how-to-assess.md) dodać Azure Migrate: Narzędzie do odnajdywania i oceny do istniejącego projektu.<br/> Dowiedz się, jak skonfigurować urządzenie na potrzeby odnajdywania i oceny [serwerów w funkcji Hyper-V](how-to-set-up-appliance-hyper-v.md).
**Azure Government** | Wizualizacja zależności nie jest dostępna w Azure Government.
**Log Analytics** | Azure Migrate używa rozwiązania [Service map](../azure-monitor/vm/service-map.md) w [dziennikach Azure monitor](../azure-monitor/logs/log-query-overview.md) na potrzeby wizualizacji zależności.<br/><br/> Nowy lub istniejący obszar roboczy Log Analytics należy skojarzyć z projektem. Nie można zmodyfikować obszaru roboczego dla projektu po jego dodaniu. <br/><br/> Obszar roboczy musi znajdować się w tej samej subskrypcji co projekt.<br/><br/> Obszar roboczy musi znajdować się w regionach Wschodnie stany USA, Azja Południowo-Wschodnia lub Europa Zachodnia. Obszary robocze w innych regionach nie mogą być skojarzone z projektem.<br/><br/> Obszar roboczy musi znajdować się w regionie, w którym [jest obsługiwana Service map](../azure-monitor/vm/vminsights-configure-workspace.md#supported-regions).<br/><br/> W Log Analytics obszar roboczy skojarzony z Azure Migrate jest otagowany przy użyciu klucza projektu migracji oraz nazwy projektu.
**Wymagani agenci** | Na każdym serwerze, który chcesz analizować, zainstaluj następujących agentów:<br/><br/> [Program Microsoft Monitoring Agent (MMA)](../azure-monitor/agents/agent-windows.md).<br/> [Agent zależności](../azure-monitor/agents/agents-overview.md#dependency-agent).<br/><br/> Jeśli serwery lokalne nie są połączone z Internetem, należy pobrać i zainstalować na nich bramę Log Analytics.<br/><br/> Dowiedz się więcej na temat instalowania [agenta zależności](how-to-create-group-machine-dependencies.md#install-the-dependency-agent) i [MMA](how-to-create-group-machine-dependencies.md#install-the-mma).
**Log Analytics obszar roboczy** | Obszar roboczy musi znajdować się w tej samej subskrypcji co projekt.<br/><br/> Azure Migrate obsługuje obszary robocze znajdujące się w regionach Wschodnie stany USA, Azja Południowo-Wschodnia i Europa Zachodnia.<br/><br/>  Obszar roboczy musi znajdować się w regionie, w którym [jest obsługiwana Service map](../azure-monitor/vm/vminsights-configure-workspace.md#supported-regions).<br/><br/> Nie można zmodyfikować obszaru roboczego dla projektu po jego dodaniu.
**Koszty** | Rozwiązanie Service Map nie powoduje naliczania opłat za pierwsze 180 dni (od dnia skojarzenia obszaru roboczego Log Analytics z projektem)/<br/><br/> Po upływie 180 dni będą naliczane standardowe opłaty za korzystanie z usługi Log Analytics.<br/><br/> Użycie dowolnego rozwiązania innego niż Service Map w skojarzonym Log Analytics obszarze roboczym spowoduje naliczenie [opłat standardowych](https://azure.microsoft.com/pricing/details/log-analytics/) za log Analytics.<br/><br/> Po usunięciu projektu nie jest on usuwany razem z nim. Po usunięciu projektu Service Map użycie nie jest bezpłatne, a każdy węzeł będzie obciążany zgodnie z płatną warstwą Log Analytics obszarze roboczym/<br/><br/>Jeśli masz projekty, które zostały utworzone przed Azure Migrate ogólnej dostępności (GA-28 lutego 2018), być może nastąpiły dodatkowe opłaty za Service Map. Aby zapewnić płatność tylko przez 180 dni, zalecamy utworzenie nowego projektu, ponieważ istniejące obszary robocze przed rozliczeniem są nadal płatne.
**Zarządzanie** | Po zarejestrowaniu agentów w obszarze roboczym należy użyć identyfikatora i klucza podanego w projekcie.<br/><br/> Możesz użyć obszaru roboczego Log Analytics poza Azure Migrate.<br/><br/> Jeśli usuniesz skojarzony projekt, obszar roboczy nie zostanie automatycznie usunięty. [Usuń je ręcznie](../azure-monitor/logs/manage-access.md).<br/><br/> Nie usuwaj obszaru roboczego utworzonego przez Azure Migrate, chyba że usuniesz ten projekt. Jeśli to zrobisz, funkcja wizualizacji zależności nie będzie działać zgodnie z oczekiwaniami.
**Łączność z Internetem** | Jeśli serwery nie są połączone z Internetem, należy zainstalować na nich bramę Log Analytics.
**Azure Government** | Analiza zależności oparta na agentach nie jest obsługiwana.

## <a name="next-steps"></a>Następne kroki

[Przygotuj się do oceny serwerów z uruchomioną funkcją Hyper-V](./tutorial-discover-hyper-v.md).