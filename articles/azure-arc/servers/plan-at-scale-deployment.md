---
title: Jak planować i wdrażać Azure Arc serwerów z włączoną obsługą
description: Dowiedz się, jak włączyć obsługę wielu maszyn na Azure Arc, aby uprościć konfigurację podstawowych funkcji zabezpieczeń, zarządzania i monitorowania na platformie Azure.
ms.date: 04/21/2021
ms.topic: conceptual
ms.openlocfilehash: e3f8fe410da56f627ceab5f17c980f2daa1a262c
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107831982"
---
# <a name="plan-and-deploy-arc-enabled-servers"></a>Planowanie i wdrażanie serwerów z usługą Arc

Wdrożenie usługi infrastruktury IT lub aplikacji biznesowej jest wyzwaniem dla każdej firmy. Aby można było je dobrze wykonać i uniknąć niechłannych niespodzianek i nieplanowanych kosztów, należy dokładnie zaplanować go, aby upewnić się, że wszystko jest tak gotowe, jak to możliwe. Aby zaplanować wdrażanie Azure Arc serwerów w dowolnej skali, powinna obejmować kryteria projektowania i wdrażania, które muszą zostać spełnione w celu pomyślnego ukończenia zadań.

Aby wdrożenie przebiegało bezproblemowo, plan powinien jasno zrozumieć:

* Role i obowiązki.
* Spis serwerów fizycznych lub maszyn wirtualnych w celu sprawdzenia, czy spełniają one wymagania sieciowe i systemowe.
* Zestaw umiejętności i szkolenie wymagane do umożliwienia pomyślnego wdrożenia i zarządzania w tym zakresie.
* Kryteria akceptacji i sposób śledzenia sukcesu.
* Narzędzia lub metody służące do automatyzowania wdrożeń.
* Identyfikowanie zagrożeń i planów ograniczania ryzyka w celu uniknięcia opóźnień, zakłóceń itp.
* Jak uniknąć zakłóceń podczas wdrażania.
* Jaka jest ścieżka eskalacji w przypadku wystąpienia istotnego problemu?

Celem tego artykułu jest zapewnienie gotowości do pomyślnego wdrożenia serwerów z obsługą Azure Arc na wielu produkcyjnych serwerach fizycznych lub maszynach wirtualnych w środowisku.

## <a name="prerequisites"></a>Wymagania wstępne

* Na maszynach działa [obsługiwany system operacyjny](agent-overview.md#supported-operating-systems) agenta Connected Machine.
* Maszyny mają łączność z sieci lokalnej lub innego środowiska chmury do zasobów na platformie Azure bezpośrednio lub za pośrednictwem serwera proxy.
* Aby zainstalować i skonfigurować agenta Connected Machine serwerów z obsługą usługi Arc, konto z podwyższonym poziomem uprawnień (czyli administratorem lub użytkownikiem głównym) na maszynach.
* Aby dołączać maszyny, należysz do Azure Connected Machine **dołączania.**
* Aby odczytywać, modyfikować i usuwać maszynę, należysz do Azure Connected Machine **administratora** zasobów.

## <a name="pilot"></a>Pilotaż

Przed wdrożeniem na wszystkich maszynach produkcyjnych należy rozpocząć od oceny tego procesu wdrażania przed wdrożeniem go w szerokim środowisku. W przypadku pilotażu zidentyfikuj reprezentatywne próbkowanie maszyn, które nie mają krytycznego znaczenie dla możliwości prowadzenia działalności firmy. Należy zapewnić wystarczająco dużo czasu na uruchomienie pilotażu i ocenę jego wpływu: zalecamy co najmniej 30 dni.

Ustanów formalny plan opisujący zakres i szczegóły pilotażu. Poniżej przedstawiono przykład tego, co powinien zawierać plan, aby ułatwić ci pracę.

* Cele — opisuje czynniki biznesowe i techniczne, które doprowadziły do podjęcia decyzji o konieczności pilotażu.
* Kryteria wyboru — określa kryteria używane do wybierania aspektów rozwiązania, które zostaną zademonstrowane za pośrednictwem pilotażu.
* Zakres — opisuje zakres pilotażu, który obejmuje między innymi składniki rozwiązania, przewidywany harmonogram, czas trwania pilotażu i liczbę maszyn docelowych.
* Kryteria i metryki sukcesu — zdefiniuj kryteria sukcesu pilotażu i konkretne miary używane do określenia poziomu sukcesu.
* Plan szkolenia — opisuje plan szkoleń dla inżynierów systemów, administratorów itp., którzy są nowymi osobami na platformie Azure i usługach it podczas pilotażu.
* Plan przejścia — zawiera opis strategii i kryteriów, które służą jako przewodnik przejścia z pilotażu do produkcji.
* Wycofywanie — opisuje procedury wycofywania pilotażu do stanu przed wdrożeniem.
* Ryzyka — lista wszystkich zidentyfikowanych czynników ryzyka związanych z przeprowadzeniem pilotażu i powiązanych z wdrożeniem produkcyjnym.

## <a name="phase-1-build-a-foundation"></a>Faza 1: Tworzenie podstaw

W tej fazie inżynierowie systemu lub administratorzy włączają podstawowe funkcje w subskrypcji platformy Azure organizacji, aby rozpocząć podstawy przed włączeniem maszyn do zarządzania przez serwery z usługą Arc i inne usługi platformy Azure.

|Zadanie |Szczegóły |Czas trwania |
|-----|-------|---------|
| [Tworzenie grupy zasobów](../../azure-resource-manager/management/manage-resource-groups-portal.md#create-resource-groups) | Dedykowana grupa zasobów, która obejmuje tylko serwery z usługą Arc oraz scentralizowane zarządzanie tymi zasobami i ich monitorowanie. | Jedna godzina |
| Stosowanie [tagów w](../../azure-resource-manager/management/tag-resources.md) celu organizowania maszyn. | Oceń i opracuj strategię tagowania dostosowaną do potrzeb IT, która może pomóc zmniejszyć złożoność zarządzania serwerami z usługą Arc i uprościć podejmowanie decyzji dotyczących zarządzania. [](/azure/cloud-adoption-framework/decision-guides/resource-tagging/) | Jeden dzień |
| Projektowanie i wdrażanie [dzienników Azure Monitor danych](../../azure-monitor/logs/data-platform-logs.md) | Oceń [zagadnienia dotyczące](../../azure-monitor/logs/design-logs-deployment.md) projektowania i wdrażania, aby określić, czy organizacja powinna używać istniejącego, czy implementować inny obszar roboczy usługi Log Analytics do przechowywania zebranych danych dzienników z hybrydowych serwerów i maszyn. <sup>1</sup> | Jeden dzień |
| [Opracowywanie Azure Policy](../../governance/policy/overview.md) ładu | Określ sposób implementowania ładu hybrydowych serwerów i maszyn w zakresie subskrypcji lub grupy zasobów za pomocą Azure Policy. | Jeden dzień |
| Konfigurowanie [kontroli dostępu opartej na rolach](../../role-based-access-control/overview.md) (RBAC) | Opracuj plan dostępu, aby kontrolować, kto ma dostęp do zarządzania serwerami z usługą Arc, oraz możliwość wyświetlania ich danych z innych usług i rozwiązań platformy Azure. | Jeden dzień |
| Identyfikowanie maszyn z już zainstalowanym agentem usługi Log Analytics | Uruchom następujące zapytanie dziennika w u [usługi Log Analytics,](../../azure-monitor/logs/log-analytics-overview.md) aby obsługiwać konwersję istniejących wdrożeń agentów usługi Log Analytics na agenta zarządzanego przez rozszerzenie:<br> Puls <br> &#124; where TimeGenerated > ago(30d) <br> &#124; where ResourceType == "machines" and (ComputerEnvironment == "Non-Azure") <br> &#124; według Computer, ResourceProvider, ResourceType, ComputerEnvironment | Jedna godzina |

<sup>1</sup> Ważnym elementem oceny projektu obszaru roboczego usługi Log Analytics jest integracja z usługą Azure Automation w celu obsługi jej funkcji Update Management i Śledzenie zmian i spis, a także Azure Security Center i Azure Sentinel. Jeśli Twoja organizacja ma już konto usługi Automation i włączyła funkcje zarządzania połączone z obszarem roboczym usługi Log Analytics, oceń, czy można scentralizować i usprawnić operacje zarządzania, a także zminimalizować koszty przy użyciu istniejących zasobów w porównaniu z tworzeniem zduplikowanego konta, obszaru roboczego itp.

## <a name="phase-2-deploy-arc-enabled-servers"></a>Faza 2: wdrażanie serwerów z usługą Arc

Następnie dodajemy do podstaw układ w fazie 1, przygotowując się do i wdrażając agenta Connected Machine serwerów z obsługą usługi Arc.

|Zadanie |Szczegóły |Czas trwania |
|-----|-------|---------|
| Pobieranie wstępnie zdefiniowanego skryptu instalacji | Przejrzyj i dostosuj wstępnie zdefiniowany skrypt instalacji na potrzeby wdrożenia na dużą skalę agenta connected machine w celu obsługi wymagań dotyczących wdrażania zautomatyzowanego.<br><br> Przykładowe zasoby dołączania na dużą skalę:<br><br> <ul><li> [Podstawowy skrypt wdrażania na dużą skalę](onboard-service-principal.md)</ul></li> <ul><li>[Maszyny wirtualne z systemem Windows Server VMware vSphere dołączanie na dużą skalę](https://github.com/microsoft/azure_arc/blob/main/docs/azure_arc_jumpstart/azure_arc_servers/scaled_deployment/vmware_scaled_powercli_win/_index.md)</ul></li> <ul><li>[Dołączanie na dużą skalę na VMware vSphere wirtualnych z systemem Linux](https://github.com/microsoft/azure_arc/blob/main/docs/azure_arc_jumpstart/azure_arc_servers/scaled_deployment/vmware_scaled_powercli_linux/_index.md)</ul></li> <ul><li>[Dołączanie wystąpień usługi EC2 usług AWS na dużą skalę przy użyciu urządzenia Ansible](https://github.com/microsoft/azure_arc/blob/main/docs/azure_arc_jumpstart/azure_arc_servers/scaled_deployment/aws_scaled_ansible/_index.md)</ul></li> <ul><li>[Wdrażanie na dużą skalę przy użyciu komunikacji zdalnej programu PowerShell](./onboard-powershell.md) (tylko system Windows)</ul></li>| Co najmniej jeden dzień w zależności od wymagań, procesów organizacyjnych (na przykład Change i Release Management) oraz używanej metody automatyzacji. |
| [Tworzenie jednostki usługi](onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale) |Utwórz jednostkę usługi w celu łączenia maszyn w sposób nieinterakcyjny przy Azure PowerShell lub z portalu.| Jedna godzina |
| Wdrażanie agenta Connected Machine na serwerach docelowych i na maszynach docelowych |Użyj narzędzia automatyzacji, aby wdrożyć skrypty na serwerach i połączyć je z platformą Azure.| Co najmniej jeden dzień w zależności od planu wydania i w przypadku etapowego wycofywania. |

## <a name="phase-3-manage-and-operate"></a>Faza 3: Zarządzanie i obsługa

Faza 3 widzi, jak administratorzy lub inżynierowie systemu umożliwiają automatyzację ręcznych zadań zarządzania i obsługi agenta connected machine i maszyny w trakcie ich cyklu życia.

|Zadanie |Szczegóły |Czas trwania |
|-----|-------|---------|
|Tworzenie alertu Resource Health alertu |Jeśli serwer przestanie wysyłać pulsy na platformę Azure dłużej niż 15 minut, może to oznaczać, że serwer jest w trybie offline, połączenie sieciowe zostało zablokowane lub agent nie jest uruchomiony. Opracuj plan reagowania na te zdarzenia [](../..//service-health/resource-health-alert-monitor-guide.md) i ich badania, a następnie użyj alertów Resource Health, aby uzyskać powiadomienia o ich uruchomieniu.<br><br> Podczas konfigurowania alertu określ następujące elementy:<br> **Typ zasobu**  =  **Azure Arc serwerów z włączoną obsługą**<br> **Bieżący stan zasobu**  =  **Niedostępne**<br> **Poprzedni stan zasobu**  =  **Dostępne** | Jedna godzina |
|Tworzenie alertu Azure Advisor alertu | Aby uzyskać najlepsze środowisko oraz najnowsze poprawki zabezpieczeń i błędów, zalecamy, aby agent serwerów z włączonymi Azure Arc był aktualny. W przypadku aktualnych agentów zostanie zidentyfikowany alert [Azure Advisor alertu](../../advisor/advisor-alerts-portal.md).<br><br> Podczas konfigurowania alertu określ następujące elementy:<br> **Typ zalecenia**  =  **Uaktualnianie do najnowszej wersji Azure Connected Machine Agent** | Jedna godzina |
|[Przypisywanie zasad platformy Azure](../../governance/policy/assign-policy-portal.md) do zakresu subskrypcji lub grupy zasobów |Przypisz **zasady Azure Monitor dla maszyn wirtualnych** [zasobów](../../azure-monitor/vm/vminsights-enable-policy.md) (i inne, które spełniają Twoje potrzeby) do zakresu subskrypcji lub grupy zasobów. Azure Policy umożliwia przypisywanie definicji zasad, które instalują wymaganych agentów na potrzeby szczegółowych informacji o maszynach wirtualnych w środowisku.| Różnie |
|[Włączanie Update Management dla serwerów z usługą Arc](../../automation/update-management/enable-from-automation-account.md) |Skonfiguruj Update Management w Azure Automation do zarządzania aktualizacjami systemu operacyjnego dla maszyn wirtualnych z systemami Windows i Linux zarejestrowanych na serwerach z usługą Arc. | 15 minut |

## <a name="next-steps"></a>Następne kroki

* Informacje dotyczące rozwiązywania problemów można znaleźć w [przewodniku Rozwiązywanie problemów z agentem połączonej maszyny.](troubleshoot-agent-onboard.md)

* Dowiedz się, jak uprościć wdrażanie za pomocą innych usług platformy Azure, takich jak Azure Automation [State Configuration](../../automation/automation-dsc-overview.md) i inne obsługiwane rozszerzenia [maszyn wirtualnych platformy Azure.](manage-vm-extensions.md)