---
title: Omówienie włączania usługi VM Insights
description: Dowiedz się, jak wdrażać i konfigurować szczegółowe dane dotyczące maszyn wirtualnych. Zapoznaj się z wymaganiami systemowymi.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 12/22/2020
ms.custom: references_regions
ms.openlocfilehash: bb2e12082b80c397eec27409b1177379a92fdd7d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102634162"
---
# <a name="enable-vm-insights-overview"></a>Omówienie włączania usługi VM Insights

Ten artykuł zawiera omówienie opcji dostępnych do włączenia usługi VM Insights w celu monitorowania kondycji i wydajności następujących elementów:

- Maszyny wirtualne platformy Azure 
- Zestawy skalowania maszyn wirtualnych platformy Azure
- Hybrydowe maszyny wirtualne połączone z usługą Azure Arc
- Lokalne maszyny wirtualne
- Maszyny wirtualne hostowane w innym środowisku chmury.  

Aby skonfigurować szczegółowe informacje o maszynie wirtualnej:

* Włącz pojedynczą maszynę wirtualną platformy Azure, zestaw skalowania maszyn wirtualnych platformy Azure lub maszynę usługi Azure ARC, wybierając **szczegółowe informacje** z menu w Azure Portal.
* Włącz obsługę wielu maszyn wirtualnych platformy Azure, maszyn wirtualnych platformy Azure lub maszyn usługi Azure ARC przy użyciu Azure Policy. Ta metoda zapewnia, że dla istniejących i nowych maszyn wirtualnych i zestawów skalowania wymagane zależności są zainstalowane i poprawnie skonfigurowane. Zgłoszono niezgodne maszyny wirtualne i zestawy skalowania, dzięki czemu można zdecydować, czy włączyć je i skorygować.
* Włącz obsługę wielu maszyn wirtualnych platformy Azure, maszyn wirtualnych usługi Azure ARC, zestawów skalowania maszyn wirtualnych platformy Azure lub maszyn usługi Azure Arc w ramach określonej subskrypcji lub grupy zasobów przy użyciu programu PowerShell.
* Włączenie usługi VM Insights w celu monitorowania maszyn wirtualnych lub komputerów fizycznych hostowanych w sieci firmowej lub w innym środowisku chmury.

## <a name="supported-machines"></a>Obsługiwane maszyny
Usługi VM Insights obsługują następujące maszyny:

- Maszyna wirtualna platformy Azure
- Zestaw skalowania maszyn wirtualnych platformy Azure
- Hybrydowa maszyna wirtualna połączona z usługą Azure Arc


## <a name="supported-azure-arc-machines"></a>Obsługiwane maszyny usługi Azure Arc
Usługa VM Insights jest dostępna dla serwerów z obsługą usługi Azure Arc w regionach, w których jest dostępna usługi rozszerzenia Arc. Musi być uruchomiona wersja 0,9 lub nowsza agenta Arc.

| Połączone źródło | Obsługiwane | Opis |
|:--|:--|:--|
| Agenci dla systemu Windows | Tak | Wraz z [agentem log Analytics dla systemu Windows](../agents/log-analytics-agent.md)agenci systemu Windows potrzebują agenta zależności. Aby uzyskać więcej informacji, zobacz [obsługiwane systemy operacyjne](../agents/agents-overview.md#supported-operating-systems). |
| Agenci dla systemu Linux | Tak | Wraz z [agentem log Analytics dla systemu Linux](../agents/log-analytics-agent.md)agenci systemu Linux potrzebują agenta zależności. Aby uzyskać więcej informacji, zobacz [obsługiwane systemy operacyjne](#supported-operating-systems). |
| Grupa zarządzania programu System Center Operations Manager | Nie | |

## <a name="supported-operating-systems"></a>Obsługiwane systemy operacyjne

Usługi VM Insights obsługują wszystkie systemy operacyjne, które obsługują agenta Log Analytics i agenta zależności. Pełną listę można znaleźć w temacie [Omówienie agentów Azure monitor ](../agents/agents-overview.md#supported-operating-systems) .

> [!IMPORTANT]
> Funkcja kondycji gościa usługi VM Insights ma ograniczoną obsługę systemu operacyjnego, gdy jest w publicznej wersji zapoznawczej. Aby uzyskać szczegółową listę, zobacz [Włączanie kondycji gościa usługi VM Insights (wersja zapoznawcza)](../vm/vminsights-health-enable.md) .

### <a name="linux-considerations"></a>Zagadnienia dotyczące systemu Linux
Zapoznaj się z poniższą listą zagadnień związanych z obsługą usługi VM w systemie Linux:

- Obsługiwane są tylko wersje domyślne i wersje SMP jądra systemu Linux.
- Niestandardowe wersje jądra, takie jak rozszerzenie adresu fizycznego (PAE) i Xen, nie są obsługiwane w przypadku żadnej dystrybucji systemu Linux. Na przykład system z ciągiem wydania *2.6.16.21-0,8-Xen* nie jest obsługiwany.
- Niestandardowe jądra, w tym ponowne kompilacje standardowych jądra, nie są obsługiwane.
- W przypadku Debian dystrybucje innych niż wersja 9,4 funkcja map nie jest obsługiwana, a funkcja wydajność jest dostępna tylko z menu Azure Monitor. Nie jest ona dostępna bezpośrednio z okienka po lewej stronie maszyny wirtualnej platformy Azure.
- CentOSPlus jądro jest obsługiwane.

W przypadku luk w zabezpieczeniach Spectre i Meltdown należy zastosować poprawki jądra systemu Linux. Aby uzyskać więcej informacji, skontaktuj się z dostawcą dystrybucji systemu Linux. Uruchom następujące polecenie, aby sprawdzić, czy jest dostępne, jeśli Spectre/Meltdown został skorygowany:

```
$ grep . /sys/devices/system/cpu/vulnerabilities/*
```

Dane wyjściowe tego polecenia będą wyglądać podobnie do poniższego i określić, czy komputer jest narażony na dowolny problem. Jeśli te pliki są niedostępne, komputer jest niepoprawiony.

```
/sys/devices/system/cpu/vulnerabilities/meltdown:Mitigation: PTI
/sys/devices/system/cpu/vulnerabilities/spectre_v1:Vulnerable
/sys/devices/system/cpu/vulnerabilities/spectre_v2:Vulnerable: Minimal generic ASM retpoline
```


## <a name="log-analytics-workspace"></a>Obszar roboczy usługi Log Analytics
Szczegółowe informacje o maszynie wirtualnej wymagają obszaru roboczego Log Analytics. Aby uzyskać szczegółowe informacje i wymagania tego obszaru roboczego, zobacz sekcję [Konfigurowanie obszaru roboczego log Analytics na potrzeby usługi VM Insights](vminsights-configure-workspace.md) .
## <a name="agents"></a>Agenci
Szczegółowe informacje o usłudze VM są wymagane do monitorowania następujących dwóch agentów na każdej maszynie wirtualnej lub w określonym zestawie skalowania maszyn wirtualnych. Aby dołączyć zasób, zainstaluj tych agentów i połącz je z obszarem roboczym.  Zapoznaj się z [wymaganiami sieciowymi](../agents/log-analytics-agent.md#network-requirements) dotyczącymi wymagań sieci dla tych agentów.

- [Agent log Analytics](../agents/log-analytics-agent.md). Zbiera dane zdarzeń i wydajności z maszyny wirtualnej lub zestawu skalowania maszyn wirtualnych i dostarcza go do obszaru roboczego Log Analytics. Metody wdrażania dla agenta Log Analytics w zasobach platformy Azure używają rozszerzenia maszyny wirtualnej dla [systemów Windows](../../virtual-machines/extensions/oms-windows.md) i [Linux](../../virtual-machines/extensions/oms-linux.md).
- Agent zależności. Zbiera odnalezione dane dotyczące procesów uruchomionych na maszynie wirtualnej i zewnętrznych zależności procesów, które są używane przez [funkcję map w usłudze VM Insights](../vm/vminsights-maps.md). Agent zależności korzysta z agenta Log Analytics, aby dostarczyć dane do Azure Monitor. Metody wdrażania dla agenta zależności w zasobach platformy Azure używają rozszerzenia maszyny wirtualnej dla [systemów Windows](../../virtual-machines/extensions/agent-dependency-windows.md) i [Linux](../../virtual-machines/extensions/agent-dependency-linux.md).

> [!NOTE]
> Agent Log Analytics jest tym samym agentem, który jest używany przez System Center Operations Manager. Szczegółowe informacje o maszynie wirtualnej mogą monitorować agentów, które są również monitorowane przez Operations Manager, jeśli są połączone bezpośrednio, a na nich jest instalowany Agent zależności. Nie można monitorować agentów podłączonych do Azure Monitor za pomocą [połączenia z grupą zarządzania](../tform/../agents/om-agents.md) przez szczegółowe informacje o maszynie wirtualnej.

Poniżej przedstawiono wiele metod wdrażania tych agentów. 

| Metoda | Opis |
|:---|:---|
| [Witryna Azure Portal](../vm/vminsights-enable-portal.md) | Zainstaluj obu agentów na jednej maszynie wirtualnej, w zestawie skalowania maszyn wirtualnych lub hybrydowych maszyn wirtualnych połączonych z usługą Azure Arc. |
| [Szablony usługi Resource Manager](../vm/vminsights-enable-resource-manager.md) | Zainstaluj obu agentów przy użyciu dowolnej z obsługiwanych metod do wdrożenia szablonu Menedżer zasobów, w tym interfejsu wiersza polecenia i programu PowerShell. |
| [Azure Policy](../vm/vminsights-enable-policy.md) | Przypisz inicjatywę Azure Policy, aby automatycznie zainstalować agentów podczas tworzenia maszyny wirtualnej lub zestawu skalowania maszyn wirtualnych. |
| [Instalacja ręczna](../vm/vminsights-enable-hybrid.md) | Zainstaluj agentów w systemie operacyjnym gościa na komputerach hostowanych poza platformą Azure, w tym w centrum danych lub w innych środowiskach w chmurze. |


## <a name="network-requirements"></a>Wymagania dotyczące sieci

- Zapoznaj się z [wymaganiami sieciowymi](../agents/log-analytics-agent.md#network-requirements) dotyczącymi wymagań sieci dla log Analytics agenta.
- Agent zależności wymaga połączenia z maszyną wirtualną do adresu 169.254.169.254. To jest punkt końcowy usługi Azure Metadata Service. Upewnij się, że ustawienia zapory zezwalają na połączenia z tym punktem końcowym.


## <a name="management-packs"></a>Pakiety administracyjne
W przypadku skonfigurowania obszaru roboczego Log Analytics dla usługi VM Insights dwa pakiety administracyjne są przekazywane do wszystkich komputerów z systemem Windows połączonych z tym obszarem roboczym. Pakiety administracyjne mają nazwę *Microsoft. IntelligencePacks. ApplicationDependencyMonitor* i *Microsoft. IntelligencePacks. VMInsights* i są zapisywane w *folderze%ProgramFiles%\Microsoft monitoring Agent\Agent\Health Service State\Management Pack*. 

Źródło danych używane przez pakiet administracyjny *ApplicationDependencyMonitor* to **% Program Files%\Microsoft monitoring Agent\Agent\Health Service State\Resources \<AutoGeneratedID>\Microsoft.EnterpriseManagement.Advisor.ApplicationDependencyMonitorDataSource.dll*. Źródło danych używane przez pakiet administracyjny *VMInsights* to *% Program Files%\Microsoft monitorowania Agent\Agent\Health Service State\Resources \<AutoGeneratedID> \ Microsoft.VirtualMachineMonitoringModule.dll*.

## <a name="diagnostic-and-usage-data"></a>Dane diagnostyczne i użycia

Firma Microsoft automatycznie zbiera dane dotyczące użycia i wydajności za pomocą usługi Azure Monitor. Firma Microsoft używa tych danych w celu poprawienia jakości, bezpieczeństwa i integralności usługi. 

Aby zapewnić dokładne i wydajne możliwości rozwiązywania problemów, funkcja map zawiera dane dotyczące konfiguracji oprogramowania. Dane zawierają informacje takie jak system operacyjny, wersja, adres IP, nazwa DNS i nazwa stacji roboczej. Firma Microsoft nie zbiera nazw, adresów ani innych informacji kontaktowych.

Aby uzyskać więcej informacji na temat zbierania i używania danych, zobacz [zasady zachowania poufności informacji w witrynie Microsoft Online Services](https://go.microsoft.com/fwlink/?LinkId=512132).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak korzystać z funkcji monitorowania wydajności, zobacz [Wyświetlanie wydajności usługi VM Insights](../vm/vminsights-performance.md). Aby wyświetlić odnalezione zależności aplikacji, zobacz [Wyświetlanie mapy usługi VM Insights](../vm/vminsights-maps.md).
