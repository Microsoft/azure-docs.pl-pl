---
title: Planowanie i wdrażanie serwerów z obsługą usługi Azure Arc
description: Dowiedz się, jak włączyć wiele maszyn na serwerach z obsługą usługi Azure ARC, aby uprościć konfigurację najważniejszych funkcji zabezpieczeń, zarządzania i monitorowania na platformie Azure.
ms.date: 03/18/2021
ms.topic: conceptual
ms.openlocfilehash: 401725dcfed85a6675c95434270dd7dbff482b6e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104591184"
---
# <a name="plan-and-deploy-arc-enabled-servers"></a>Planowanie i wdrażanie serwerów z obsługą łuku

Wdrożenie usługi infrastruktury IT lub aplikacji biznesowej jest wyzwaniem dla każdej firmy. Aby zapewnić jego zgodność i uniknąć wszelkich niezwiązanych z nią niedowolnych powitań i nieplanowanych kosztów, należy dokładnie zaplanować, aby upewnić się, że wszystko jest gotowe do użycia. Aby zaplanować wdrożenie serwerów z obsługą usługi Azure Arc w dowolnej skali, należy uwzględnić kryteria projektowania i wdrażania, które muszą zostać spełnione w celu pomyślnego wykonania zadań.

Aby wdrożenie przebiegać bezproblemowo, plan powinien stworzyć jasne zrozumienie:

* Role i obowiązki.
* Spis serwerów fizycznych lub maszyn wirtualnych, aby sprawdzić, czy spełniają one wymagania sieciowe i systemowe.
* Zestaw umiejętności i szkolenia wymagane do zapewnienia pomyślnego wdrożenia i zarządzania nim.
* Kryteria akceptacji i sposób śledzenia sukcesu.
* Narzędzia lub metody służące do automatyzowania wdrożeń.
* Zidentyfikowano zagrożenia i plany zaradcze, aby uniknąć opóźnień, zakłóceń itp.
* Jak uniknąć przerw w działaniu podczas wdrażania.
* Jaka jest ścieżka eskalacji w przypadku wystąpienia znaczącego problemu?

Celem tego artykułu jest upewnienie się, że przygotowano do pomyślnego wdrożenia serwerów z obsługą usługi Azure Arc na wielu serwerach fizycznych lub maszynach wirtualnych w środowisku.

## <a name="prerequisites"></a>Wymagania wstępne

* Na maszynach jest uruchomiony [obsługiwany system operacyjny](agent-overview.md#supported-operating-systems) dla agenta połączonej maszyny.
* Maszyny mają łączność z siecią lokalną lub innym środowiskiem chmury do zasobów platformy Azure, bezpośrednio lub za pośrednictwem serwera proxy.
* Do zainstalowania i skonfigurowania serwera z włączonym Łukem, który jest podłączony do agenta komputera, konto z podwyższonym poziomem uprawnień (czyli administratorem lub jako root) na komputerach.
* Aby dodać maszyny, musisz być członkiem roli **dołączania maszyny połączonej z platformą Azure** .
* Aby odczytywać, modyfikować i usuwać maszyny, musisz być członkiem roli **administratora zasobów maszyny połączonej z platformą Azure** .

## <a name="pilot"></a>Pilotaż

Przed wdrożeniem na wszystkich maszynach produkcyjnych Rozpocznij od oceny tego procesu wdrażania przed jego wdrożeniem w środowisku. W przypadku pilotażu należy zidentyfikować reprezentatywne próbkowanie maszyn, które nie są kluczowe dla firm, które nie mają możliwości prowadzenia działalności. Upewnij się, że masz wystarczającą ilość czasu na uruchomienie pilotażu i ocenę jego wpływu: zalecamy co najmniej 30 dni.

Ustanowienie formalnego planu opisującego zakres i szczegóły pilotażu. Poniżej przedstawiono przykład sposobu, w jaki plan powinien obejmować, aby pomóc Ci rozpocząć pracę.

* Cele — opisuje sterowniki biznesowe i techniczne, które doprowadziły do decyzji, że pilotaż jest niezbędny.
* Kryteria wyboru — określa kryteria używane do wybierania aspektów rozwiązania, które będą prezentowane za pośrednictwem pilotażu.
* Zakres — opisuje zakres pilotażu, który obejmuje, ale nie ogranicza do składników rozwiązania, przewidywanego harmonogramu, czasu trwania pilotażu i liczby maszyn docelowych.
* Kryteria sukcesu i metryki — zdefiniuj kryteria sukcesu pilotażu i określone miary używane do określania poziomu sukcesu.
* Plan szkoleniowy — zawiera opis planu szkolenia inżynierów systemów, administratorów itp., którzy są nowym sposobem na platformę Azure i usługi IT podczas pilotażu.
* Plan przejścia — opis strategii i kryteriów używanych do przejścia od pilotażu do środowiska produkcyjnego.
* Wycofywanie — zawiera opis procedur wycofywania pilotażu do stanu sprzed wdrożenia.
* Ryzyka — lista wszystkich zidentyfikowanych zagrożeń związanych z przeprowadzeniem pilotażu i związana z wdrożeniem produkcyjnym.

## <a name="phase-1-build-a-foundation"></a>Faza 1: Tworzenie podstawy

W tej fazie inżynierowie systemów lub Administratorzy włączają podstawowe funkcje w ramach subskrypcji platformy Azure w organizacji, aby rozpocząć fundament przed włączeniem maszyn do zarządzania przez serwery z włączonym łukiem i innych usług platformy Azure.

|Zadanie |Szczegóły |Czas trwania |
|-----|-------|---------|
| [Tworzenie grupy zasobów](../../azure-resource-manager/management/manage-resource-groups-portal.md#create-resource-groups) | Dedykowana Grupa zasobów obejmująca tylko serwery z obsługą łuku i scentralizowanego zarządzania i monitorowania tych zasobów. | Jedna godzina |
| Zastosuj [Tagi](../../azure-resource-manager/management/tag-resources.md) , aby ułatwić organizowanie maszyn. | Oceń i opracowuj [strategię tagowania](/cloud-adoption-framework/decision-guides/resource-tagging/) dopasowaną przez dział IT, która może pomóc w zmniejszeniu złożoności zarządzania serwerami z włączonym łukiem i uproszczenia podejmowania decyzji dotyczących zarządzania. | Jeden dzień |
| Projektowanie i wdrażanie [dzienników Azure monitor](../../azure-monitor/logs/data-platform-logs.md) | Oceń [zagadnienia dotyczące projektowania i wdrażania](../../azure-monitor/logs/design-logs-deployment.md) , aby określić, czy organizacja ma używać istniejącego lub zaimplementowania innego obszaru roboczego log Analytics do przechowywania zebranych danych dziennika z serwerów i maszyn hybrydowych. <sup>1</sup> | Jeden dzień |
| [Opracowywanie planu Azure Policy](../../governance/policy/overview.md) ładu | Określ, w jaki sposób ma zostać wdrożony nadzór nad serwerami hybrydowymi i maszynami w zakresie subskrypcji lub grupy zasobów przy użyciu Azure Policy. | Jeden dzień |
| Konfigurowanie [kontroli dostępu opartej na rolach](../../role-based-access-control/overview.md) (RBAC) | Opracowywanie planu dostępu w celu kontrolowania, kto ma dostęp do zarządzania serwerami z obsługą łuku i możliwość wyświetlania ich danych z innych usług i rozwiązań platformy Azure. | Jeden dzień |
| Zidentyfikuj maszyny z zainstalowanym agentem Log Analytics | Uruchom następujące zapytanie dziennika w [log Analytics](../../azure-monitor/logs/log-analytics-overview.md) , aby obsłużyć konwersję istniejących wdrożeń agentów log Analytics do agenta zarządzanego przez rozszerzenie:<br> Puls <br> &#124;, gdzie TimeGenerated > temu (30d) <br> &#124;, gdzie ResourceType = = "Machines" i (ComputerEnvironment = = "non-Azure") <br> &#124; Sumuj według komputera, ResourceProvider, ResourceType, ComputerEnvironment | Jedna godzina |

<sup>1</sup> istotną kwestią w ocenie projektu obszaru log Analytics, jest integracja z Azure Automation w celu obsługi Update Management oraz funkcji Change Tracking i spisu, a także Azure Security Center i platformy Azure. Jeśli Twoja organizacja ma już konto usługi Automation i włączyła swoje funkcje zarządzania połączone z obszarem roboczym Log Analytics, Oceń, czy można scentralizować i usprawnić operacje zarządzania, a także zminimalizować koszty, korzystając z tych istniejących zasobów i tworząc zduplikowane konto, obszar roboczy itd.

## <a name="phase-2-deploy-arc-enabled-servers"></a>Faza 2. wdrażanie serwerów z włączonym łukiem

Następnie dodamy do podstawy ustalonej w fazie 1 przez przygotowanie do i wdrożenie agenta połączonego z serwerem z włączonym łukiem.

|Zadanie |Szczegóły |Czas trwania |
|-----|-------|---------|
| Pobierz wstępnie zdefiniowany skrypt instalacyjny | Zapoznaj się z wstępnie zdefiniowanym skryptem instalacji i dostosuj go, aby zapewnić obsługę zautomatyzowanych wymagań dotyczących wdrażania w ramach wdrażania agenta połączonego maszyny.<br><br> Przykład w zasobach dołączania do skali:<br><br> <ul><li> [Skrypt wdrażania w warstwie Podstawowa](onboard-service-principal.md)</ul></li> <ul><li>[W przypadku dołączania VMware vSphere maszyn wirtualnych z systemem Windows Server](https://github.com/microsoft/azure_arc/blob/master/azure_arc_servers_jumpstart/docs/vmware_scaled_powercli_win.md)</ul></li> <ul><li>[W przypadku dołączania VMware vSphere maszyn wirtualnych z systemem Linux](https://github.com/microsoft/azure_arc/blob/master/azure_arc_servers_jumpstart/docs/vmware_scaled_powercli_linux.md)</ul></li> <ul><li>[W przypadku dołączania wystąpień AWS EC2 przy użyciu rozwiązania ansible](https://github.com/microsoft/azure_arc/blob/master/azure_arc_servers_jumpstart/docs/aws_scale_ansible.md)</ul></li> <ul><li>[Wdrażanie w skali przy użyciu komunikacji zdalnej programu PowerShell](https://docs.microsoft.com/azure/azure-arc/servers/onboard-powershell) (tylko system Windows)</ul></li>| Co najmniej jeden dzień, w zależności od wymagań, procesów organizacyjnych (na przykład zmiany i Release Management) i używanej metody automatyzacji. |
| [Tworzenie jednostki usługi](onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale) |Utwórz nazwę główną usługi, aby połączyć maszyny w sposób nieinteraktywny przy użyciu Azure PowerShell lub z portalu.| Jedna godzina |
| Wdróż agenta połączonej maszyny na serwerze docelowym i na maszynach docelowych |Użyj narzędzia Automation, aby wdrożyć skrypty na serwerach i połączyć je z platformą Azure.| Co najmniej jeden dzień w zależności od planu wydania i po wdrożeniu etapowym. |

## <a name="phase-3-manage-and-operate"></a>Faza 3: zarządzanie i obsługa

Faza 3 widzi administratorów lub inżynierów systemów, które umożliwiają automatyzację zadań ręcznych w celu zarządzania i obsługiwania agenta połączonej maszyny oraz komputera w ich cyklu życia.

|Zadanie |Szczegóły |Czas trwania |
|-----|-------|---------|
|Tworzenie alertu Resource Health |Jeśli serwer zatrzyma wysyłanie pulsu do platformy Azure przez dłużej niż 15 minut, może to oznaczać, że jest w trybie offline, połączenie sieciowe zostało zablokowane lub Agent nie jest uruchomiony. Utwórz plan na potrzeby reakcji i zbadaj te incydenty i Użyj [alertów Resource Health](../..//service-health/resource-health-alert-monitor-guide.md) , aby otrzymywać powiadomienia o ich rozpoczęciu.<br><br> Określ następujące podczas konfigurowania alertu:<br> **Typ zasobu**  =  **Serwery z obsługą usługi Azure Arc**<br> **Bieżący stan zasobu**  =  **Niedostępne**<br> **Poprzedni stan zasobu**  =  **Dostępne** | Jedna godzina |
|Tworzenie alertu Azure Advisor | Aby uzyskać najlepsze środowisko i najnowsze poprawki dotyczące zabezpieczeń i błędów, zalecamy utrzymywanie Aktualności agenta serwerów z włączonym usługą Azure Arc. Nieaktualne agenci zostaną zidentyfikowani za pomocą [alertu Azure Advisor](../../advisor/advisor-alerts-portal.md).<br><br> Określ następujące podczas konfigurowania alertu:<br> **Typ zalecenia**  =  **Uaktualnianie do najnowszej wersji agenta połączonej maszyny platformy Azure** | Jedna godzina |
|[Przypisywanie zasad platformy Azure](../../governance/policy/assign-policy-portal.md) do zakresu subskrypcji lub grupy zasobów |Przypisz zasady **włączania Azure monitor dla maszyn wirtualnych** [](../../azure-monitor/vm/vminsights-enable-policy.md) (i inne, które spełniają Twoje potrzeby) do zakresu subskrypcji lub grupy zasobów. Azure Policy umożliwia przypisanie definicji zasad instalujących wymaganych agentów dla Azure Monitor dla maszyn wirtualnych w środowisku.| Różnie |
|[Włącz Update Management dla serwerów z włączonym łukiem](../../automation/update-management/enable-from-automation-account.md) |Skonfiguruj Update Management w Azure Automation, aby zarządzać aktualizacjami systemu operacyjnego dla maszyn wirtualnych z systemami Windows i Linux zarejestrowanych przy użyciu serwerów z włączonym łukiem. | 15 minut |

## <a name="next-steps"></a>Następne kroki

* Informacje dotyczące rozwiązywania problemów można znaleźć w [przewodniku Rozwiązywanie problemów z agentem podłączonych komputerów](troubleshoot-agent-onboard.md).

* Dowiedz się, jak uprościć wdrażanie za pomocą innych usług platformy Azure, takich jak [Konfiguracja stanu](../../automation/automation-dsc-overview.md) Azure Automation i inne obsługiwane [rozszerzenia maszyn wirtualnych platformy Azure](manage-vm-extensions.md).