---
title: Włącz przegląd Azure Monitor dla maszyn wirtualnych
description: Dowiedz się, jak wdrażać i konfigurować Azure Monitor dla maszyn wirtualnych. Zapoznaj się z wymaganiami systemowymi.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 12/22/2020
ms.custom: references_regions
ms.openlocfilehash: 18be0f7d1bd8622735f24bf20161d652846112f7
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100373423"
---
# <a name="enable-azure-monitor-for-vms-overview"></a>Włącz przegląd Azure Monitor dla maszyn wirtualnych

Ten artykuł zawiera omówienie opcji dostępnych w celu umożliwienia Azure Monitor dla maszyn wirtualnych monitorowania kondycji i wydajności następujących elementów:

- Maszyny wirtualne platformy Azure 
- Zestawy skalowania maszyn wirtualnych platformy Azure
- Hybrydowe maszyny wirtualne połączone z usługą Azure Arc
- Lokalne maszyny wirtualne
- Maszyny wirtualne hostowane w innym środowisku chmury.  

Aby skonfigurować Azure Monitor dla maszyn wirtualnych:

* Włącz pojedynczą maszynę wirtualną platformy Azure, zestaw skalowania maszyn wirtualnych platformy Azure lub maszynę usługi Azure ARC, wybierając **szczegółowe informacje** z menu w Azure Portal.
* Włącz obsługę wielu maszyn wirtualnych platformy Azure, maszyn wirtualnych platformy Azure lub maszyn usługi Azure ARC przy użyciu Azure Policy. Ta metoda zapewnia, że dla istniejących i nowych maszyn wirtualnych i zestawów skalowania wymagane zależności są zainstalowane i poprawnie skonfigurowane. Zgłoszono niezgodne maszyny wirtualne i zestawy skalowania, dzięki czemu można zdecydować, czy włączyć je i skorygować.
* Włącz obsługę wielu maszyn wirtualnych platformy Azure, maszyn wirtualnych usługi Azure ARC, zestawów skalowania maszyn wirtualnych platformy Azure lub maszyn usługi Azure Arc w ramach określonej subskrypcji lub grupy zasobów przy użyciu programu PowerShell.
* Umożliwia Azure Monitor dla maszyn wirtualnych monitorowania maszyn wirtualnych lub komputerów fizycznych hostowanych w sieci firmowej lub w innym środowisku chmury.

## <a name="supported-machines"></a>Obsługiwane maszyny
Azure Monitor dla maszyn wirtualnych obsługuje następujące maszyny:

- Maszyna wirtualna platformy Azure
- Zestaw skalowania maszyn wirtualnych platformy Azure
- Hybrydowa maszyna wirtualna połączona z usługą Azure Arc


## <a name="supported-azure-arc-machines"></a>Obsługiwane maszyny usługi Azure Arc
Azure Monitor dla maszyn wirtualnych jest dostępna dla serwerów z obsługą usługi Azure Arc w regionach, w których jest dostępna usługa rozszerzenia Arc. Musi być uruchomiona wersja 0,9 lub nowsza agenta Arc.

| Połączone źródło | Obsługiwane | Opis |
|:--|:--|:--|
| Agenci dla systemu Windows | Tak | Wraz z [agentem log Analytics dla systemu Windows](../platform/log-analytics-agent.md)agenci systemu Windows potrzebują agenta zależności. Aby uzyskać więcej informacji, zobacz [obsługiwane systemy operacyjne](../platform/agents-overview.md#supported-operating-systems). |
| Agenci dla systemu Linux | Tak | Wraz z [agentem log Analytics dla systemu Linux](../platform/log-analytics-agent.md)agenci systemu Linux potrzebują agenta zależności. Aby uzyskać więcej informacji, zobacz [obsługiwane systemy operacyjne](#supported-operating-systems). |
| Grupa zarządzania programu System Center Operations Manager | Nie | |

## <a name="supported-operating-systems"></a>Obsługiwane systemy operacyjne

Azure Monitor dla maszyn wirtualnych obsługuje każdy system operacyjny, który obsługuje agenta Log Analytics i agenta zależności. Pełną listę można znaleźć w temacie [Omówienie agentów Azure monitor ](../platform/agents-overview.md#supported-operating-systems) .

> [!IMPORTANT]
> Funkcja kondycji gościa Azure Monitor dla maszyn wirtualnych ma ograniczoną obsługę systemu operacyjnego, gdy jest w publicznej wersji zapoznawczej. Aby uzyskać szczegółową listę, zobacz [włączanie Azure monitor dla maszyn wirtualnych kondycji gościa (wersja zapoznawcza)](vminsights-health-enable.md) .

Zapoznaj się z poniższą listą zagadnień związanych z obsługą agenta zależnego obsługiwanego przez system Linux Azure Monitor dla maszyn wirtualnych:

- Obsługiwane są tylko wersje domyślne i wersje SMP jądra systemu Linux.
- Niestandardowe wersje jądra, takie jak rozszerzenie adresu fizycznego (PAE) i Xen, nie są obsługiwane w przypadku żadnej dystrybucji systemu Linux. Na przykład system z ciągiem wydania *2.6.16.21-0,8-Xen* nie jest obsługiwany.
- Niestandardowe jądra, w tym ponowne kompilacje standardowych jądra, nie są obsługiwane.
- W przypadku Debian dystrybucje innych niż wersja 9,4 funkcja map nie jest obsługiwana, a funkcja wydajność jest dostępna tylko z menu Azure Monitor. Nie jest ona dostępna bezpośrednio z okienka po lewej stronie maszyny wirtualnej platformy Azure.
- CentOSPlus jądro jest obsługiwane.
- Dla luki w zabezpieczeniach Spectre należy zainstalować jądro systemu Linux. Aby uzyskać więcej informacji, skontaktuj się z dostawcą dystrybucji systemu Linux.
## <a name="log-analytics-workspace"></a>Obszar roboczy usługi Log Analytics
Azure Monitor dla maszyn wirtualnych wymaga Log Analytics obszaru roboczego. Aby uzyskać szczegółowe informacje i wymagania tego obszaru roboczego, zobacz [konfigurowanie log Analytics obszaru Azure monitor dla maszyn wirtualnych roboczego](vminsights-configure-workspace.md) .
## <a name="agents"></a>Agenci
Azure Monitor dla maszyn wirtualnych wymaga zainstalowania dwóch agentów na każdej maszynie wirtualnej lub w zestawie skalowania maszyn wirtualnych do monitorowania. Aby dołączyć zasób, zainstaluj tych agentów i połącz je z obszarem roboczym.  

- [Agent log Analytics](../platform/log-analytics-agent.md). Zbiera dane zdarzeń i wydajności z maszyny wirtualnej lub zestawu skalowania maszyn wirtualnych i dostarcza go do obszaru roboczego Log Analytics. Metody wdrażania dla agenta Log Analytics w zasobach platformy Azure używają rozszerzenia maszyny wirtualnej dla [systemów Windows](../../virtual-machines/extensions/oms-windows.md) i [Linux](../../virtual-machines/extensions/oms-linux.md).
- Agent zależności. Zbiera odnalezione dane dotyczące procesów uruchomionych na maszynie wirtualnej i zewnętrznych zależności procesów, które są używane przez [funkcję map w Azure monitor dla maszyn wirtualnych](vminsights-maps.md). Agent zależności korzysta z agenta Log Analytics, aby dostarczyć dane do Azure Monitor. Metody wdrażania dla agenta zależności w zasobach platformy Azure używają rozszerzenia maszyny wirtualnej dla [systemów Windows](../../virtual-machines/extensions/agent-dependency-windows.md) i [Linux](../../virtual-machines/extensions/agent-dependency-linux.md).

> [!NOTE]
> Agent Log Analytics jest tym samym agentem, który jest używany przez System Center Operations Manager. Azure Monitor dla maszyn wirtualnych może monitorować agentów, które są również monitorowane przez Operations Manager, jeśli są połączone bezpośrednio, a na nich jest instalowany Agent zależności. Nie można monitorować agentów podłączonych do Azure Monitor za pomocą [połączenia z grupą zarządzania](../tform/../platform/om-agents.md) przez Azure monitor dla maszyn wirtualnych.

Poniżej przedstawiono wiele metod wdrażania tych agentów. 

| Metoda | Opis |
|:---|:---|
| [Witryna Azure Portal](./vminsights-enable-portal.md) | Zainstaluj obu agentów na jednej maszynie wirtualnej, w zestawie skalowania maszyn wirtualnych lub hybrydowych maszyn wirtualnych połączonych z usługą Azure Arc. |
| [Szablony usługi Resource Manager](vminsights-enable-resource-manager.md) | Zainstaluj obu agentów przy użyciu dowolnej z obsługiwanych metod do wdrożenia szablonu Menedżer zasobów, w tym interfejsu wiersza polecenia i programu PowerShell. |
| [Azure Policy](./vminsights-enable-policy.md) | Przypisz inicjatywę Azure Policy, aby automatycznie zainstalować agentów podczas tworzenia maszyny wirtualnej lub zestawu skalowania maszyn wirtualnych. |
| [Instalacja ręczna](./vminsights-enable-hybrid.md) | Zainstaluj agentów w systemie operacyjnym gościa na komputerach hostowanych poza platformą Azure, w tym w centrum danych lub w innych środowiskach w chmurze. |


## <a name="network-requirements"></a>Wymagania dotyczące sieci

- Zapoznaj się z [wymaganiami sieciowymi](../platform/log-analytics-agent.md#network-requirements) dotyczącymi wymagań sieci dla log Analytics agenta.
- Agent zależności wymaga połączenia z maszyną wirtualną do adresu 169.254.169.254. To jest punkt końcowy usługi Azure Metadata Service. Upewnij się, że ustawienia zapory zezwalają na połączenia z tym punktem końcowym.


## <a name="management-packs"></a>Pakiety administracyjne
W przypadku skonfigurowania Log Analytics obszaru roboczego dla Azure Monitor dla maszyn wirtualnych dwa pakiety administracyjne są przekazywane do wszystkich komputerów z systemem Windows połączonych z tym obszarem roboczym. Pakiety administracyjne mają nazwę *Microsoft. IntelligencePacks. ApplicationDependencyMonitor* i *Microsoft. IntelligencePacks. VMInsights* i są zapisywane w *folderze%ProgramFiles%\Microsoft monitoring Agent\Agent\Health Service State\Management Pack*. 

Źródło danych używane przez pakiet administracyjny *ApplicationDependencyMonitor* to **% Program Files%\Microsoft monitoring Agent\Agent\Health Service State\Resources \<AutoGeneratedID>\Microsoft.EnterpriseManagement.Advisor.ApplicationDependencyMonitorDataSource.dll*. Źródło danych używane przez pakiet administracyjny *VMInsights* to *% Program Files%\Microsoft monitorowania Agent\Agent\Health Service State\Resources \<AutoGeneratedID> \ Microsoft.VirtualMachineMonitoringModule.dll*.

## <a name="diagnostic-and-usage-data"></a>Dane diagnostyczne i użycia

Firma Microsoft automatycznie zbiera dane dotyczące użycia i wydajności za pomocą usługi Azure Monitor. Firma Microsoft używa tych danych w celu poprawienia jakości, bezpieczeństwa i integralności usługi. 

Aby zapewnić dokładne i wydajne możliwości rozwiązywania problemów, funkcja map zawiera dane dotyczące konfiguracji oprogramowania. Dane zawierają informacje takie jak system operacyjny, wersja, adres IP, nazwa DNS i nazwa stacji roboczej. Firma Microsoft nie zbiera nazw, adresów ani innych informacji kontaktowych.

Aby uzyskać więcej informacji na temat zbierania i używania danych, zobacz [zasady zachowania poufności informacji w witrynie Microsoft Online Services](https://go.microsoft.com/fwlink/?LinkId=512132).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak korzystać z funkcji monitorowania wydajności, zobacz [wyświetlanie Azure monitor dla maszyn wirtualnych Performance](vminsights-performance.md). Aby wyświetlić odnalezione zależności aplikacji, zobacz [view Azure monitor dla maszyn wirtualnych map](vminsights-maps.md).
