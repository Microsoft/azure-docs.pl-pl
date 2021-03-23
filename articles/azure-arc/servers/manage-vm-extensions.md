---
title: Zarządzanie rozszerzeniami maszyny wirtualnej za pomocą serwerów z obsługą usługi Azure Arc
description: Serwery z obsługą usługi Azure Arc mogą zarządzać wdrożeniem rozszerzeń maszyn wirtualnych, które zapewniają konfigurację po wdrożeniu i zadania automatyzacji z maszynami wirtualnymi spoza platformy Azure.
ms.date: 03/22/2021
ms.topic: conceptual
ms.openlocfilehash: 9af2700f036352188e646188485285482ee70c69
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104799585"
---
# <a name="virtual-machine-extension-management-with-azure-arc-enabled-servers"></a>Zarządzanie rozszerzeniami maszyn wirtualnych na serwerach z obsługą usługi Azure Arc

Rozszerzenia maszyn wirtualnych to małe aplikacje, które zapewniają konfigurację po wdrożeniu i zadania automatyzacji na maszynach wirtualnych platformy Azure. Na przykład jeśli maszyna wirtualna wymaga instalacji oprogramowania, ochrony antywirusowej lub uruchamiania w niej skryptu, można użyć rozszerzenia maszyny wirtualnej.

Serwery z obsługą usługi Azure ARC umożliwiają Wdrażanie rozszerzeń maszyn wirtualnych platformy Azure na maszynach wirtualnych z systemami Windows i Linux, które upraszczają zarządzanie komputerem hybrydowym przez ich cykl życia. Rozszerzeniami maszyn wirtualnych można zarządzać przy użyciu następujących metod na maszynach hybrydowych lub serwerach zarządzanych przez serwery z obsługą ARC:

- [Azure Portal](manage-vm-extensions-portal.md)
- [Interfejs wiersza polecenia platformy Azure](manage-vm-extensions-cli.md)
- [Azure PowerShell](manage-vm-extensions-powershell.md)
- [Szablony Menedżer zasobów](manage-vm-extensions-template.md) platformy Azure

## <a name="key-benefits"></a>Najważniejsze korzyści

Obsługa rozszerzenia maszyny wirtualnej z obsługą usługi Azure Arc zapewnia następujące korzyści:

- Zbierz dane dziennika do analizy za pomocą [dzienników w Azure monitor](../../azure-monitor/logs/data-platform-logs.md) , włączając rozszerzenie maszyny wirtualnej log Analytics Agent. Jest to przydatne w przypadku wykonywania złożonej analizy między danymi z różnych rodzajów źródeł.

- Dzięki [Azure monitor dla maszyn wirtualnych](../../azure-monitor/vm/vminsights-overview.md)analizuje wydajność maszyn wirtualnych z systemami Windows i Linux oraz monitorują procesy i zależności od innych zasobów i procesów zewnętrznych. Jest to realizowane przez włączenie zarówno rozszerzenia agenta Log Analytics, jak i agenta zależności maszyny wirtualnej.

- Pobieranie i wykonywanie skryptów na maszynach połączonych hybrydowo przy użyciu niestandardowego rozszerzenia skryptu. To rozszerzenie jest przydatne w przypadku konfiguracji po wdrożeniu, instalacji oprogramowania lub innych zadań związanych z konfiguracją lub zarządzaniem.

- Automatyczne odświeżanie certyfikatów przechowywanych w [Azure Key Vault](../../key-vault/general/overview.md).

## <a name="availability"></a>Dostępność

Funkcjonalność rozszerzenia maszyny wirtualnej jest dostępna tylko na liście [obsługiwanych regionów](overview.md#supported-regions). Upewnij się, że Twoja maszyna została dołączona do jednego z tych regionów.

## <a name="extensions"></a>Rozszerzenia

W tej wersji obsługiwane są następujące rozszerzenia maszyn wirtualnych na komputerach z systemem Windows i Linux.

Aby dowiedzieć się więcej na temat pakietu agenta połączonego z platformą Azure i szczegółowych informacji o składniku agenta rozszerzeń, zobacz [Omówienie agenta](agent-overview.md#agent-component-details).

> [!NOTE]
> Ostatnio obsługiwane rozszerzenie maszyny wirtualnej DSC zostało usunięte dla serwerów z włączonymi łukiem. Alternatywnie zalecamy użycie rozszerzenia niestandardowego skryptu w celu zarządzania konfiguracją po wdrożeniu serwera lub komputera.

### <a name="windows-extensions"></a>Rozszerzenia systemu Windows

|Wewnętrzny |Publisher |Typ |Dodatkowe informacje |
|----------|----------|-----|-----------------------|
|Skaner zintegrowanej luki w zabezpieczeniach usługi Azure Defender |Qualys |WindowsAgent.AzureSecurityCenter |[Rozwiązanie do oceny luk w zabezpieczeniach usługi Azure Defender dla platformy Azure i maszyn hybrydowych](../../security-center/deploy-vulnerability-assessment-vm.md)|
|Rozszerzenie niestandardowego skryptu |Microsoft.Compute | CustomScriptExtension |[Rozszerzenie niestandardowego skryptu systemu Windows](../../virtual-machines/extensions/custom-script-windows.md)|
|Agent usługi Log Analytics |Microsoft. EnterpriseCloud. Monitoring |MicrosoftMonitoringAgent |[Log Analytics rozszerzenie maszyny wirtualnej dla systemu Windows](../../virtual-machines/extensions/oms-windows.md)|
|Azure Monitor dla maszyn wirtualnych (szczegółowe dane) |Microsoft. Azure. Monitoring. DependencyAgent |DependencyAgentWindows | [Rozszerzenie maszyny wirtualnej agenta zależności dla systemu Windows](../../virtual-machines/extensions/agent-dependency-windows.md)|
|Azure Key Vault synchronizację certyfikatów | Microsoft. Azure. Key. magazyn |KeyVaultForWindows | [Key Vault rozszerzenie maszyny wirtualnej dla systemu Windows](../../virtual-machines/extensions/key-vault-windows.md) |
|Agent usługi Azure Monitor |Microsoft. Azure. Monitor |AzureMonitorWindowsAgent |[Zainstaluj agenta Azure Monitor (wersja zapoznawcza)](../../azure-monitor/agents/azure-monitor-agent-install.md) |

### <a name="linux-extensions"></a>Rozszerzenia systemu Linux

|Wewnętrzny |Publisher |Typ |Dodatkowe informacje |
|----------|----------|-----|-----------------------|
|Skaner zintegrowanej luki w zabezpieczeniach usługi Azure Defender |Qualys |LinuxAgent.AzureSecurityCenter |[Rozwiązanie do oceny luk w zabezpieczeniach usługi Azure Defender dla platformy Azure i maszyn hybrydowych](../../security-center/deploy-vulnerability-assessment-vm.md)|
|Rozszerzenie niestandardowego skryptu |Microsoft. Azure. Extensions |CustomScript |[Rozszerzenie niestandardowego skryptu systemu Linux w wersji 2](../../virtual-machines/extensions/custom-script-linux.md) |
|Agent usługi Log Analytics |Microsoft. EnterpriseCloud. Monitoring |OmsAgentForLinux |[Log Analytics rozszerzenie maszyny wirtualnej dla systemu Linux](../../virtual-machines/extensions/oms-linux.md) |
|Azure Monitor dla maszyn wirtualnych (szczegółowe dane) |Microsoft. Azure. Monitoring. DependencyAgent |DependencyAgentLinux |[Rozszerzenie maszyny wirtualnej agenta zależności dla systemu Linux](../../virtual-machines/extensions/agent-dependency-linux.md) |
|Azure Key Vault synchronizację certyfikatów | Microsoft. Azure. Key. magazyn |KeyVaultForLinux | [Key Vault rozszerzenie maszyny wirtualnej dla systemu Linux](../../virtual-machines/extensions/key-vault-linux.md) |
|Agent usługi Azure Monitor |Microsoft. Azure. Monitor |AzureMonitorLinuxAgent |[Zainstaluj agenta Azure Monitor (wersja zapoznawcza)](../../azure-monitor/agents/azure-monitor-agent-install.md) |

## <a name="prerequisites"></a>Wymagania wstępne

Ta funkcja jest zależna od następujących dostawców zasobów platformy Azure w ramach subskrypcji:

- **Microsoft. HybridCompute**
- **Microsoft. GuestConfiguration**

Jeśli nie są one jeszcze zarejestrowane, wykonaj kroki opisane w sekcji [Rejestrowanie dostawców zasobów platformy Azure](agent-overview.md#register-azure-resource-providers).

Zapoznaj się z dokumentacją każdego rozszerzenia maszyny wirtualnej, do którego odwołuje się w poprzedniej tabeli, aby zrozumieć, czy ma on jakieś wymagania dotyczące sieci lub systemu. Może to pomóc uniknąć problemów z łącznością z usługą lub funkcją platformy Azure, która opiera się na rozszerzeniu maszyny wirtualnej.

### <a name="log-analytics-vm-extension"></a>Log Analytics rozszerzenie maszyny wirtualnej

Rozszerzenie maszyny wirtualnej agenta Log Analytics dla systemu Linux wymaga zainstalowania języka Python 2. x na maszynie docelowej.

### <a name="azure-key-vault-vm-extension-preview"></a>Rozszerzenie maszyny wirtualnej Azure Key Vault (wersja zapoznawcza)

Rozszerzenie maszyny wirtualnej Key Vault (wersja zapoznawcza) nie obsługuje następujących systemów operacyjnych Linux:

- CentOS Linux 7 (x64)
- Red Hat Enterprise Linux (RHEL) 7 (x64)
- Amazon Linux 2 (x64)

Wdrażanie rozszerzenia maszyny wirtualnej Key Vault (wersja zapoznawcza) jest obsługiwane tylko przy użyciu:

- Interfejs wiersza polecenia platformy Azure
- Azure PowerShell
- Szablon usługi Azure Resource Manager

Przed wdrożeniem rozszerzenia należy wykonać następujące czynności:

1. [Utwórz magazyn i certyfikat](../../key-vault/certificates/quick-create-portal.md) (z podpisem własnym lub importem).

2. Przyznaj serwerowi usługi Azure Arc dostęp do klucza tajnego certyfikatu. Jeśli korzystasz z [wersji zapoznawczej RBAC](../../key-vault/general/rbac-guide.md), wyszukaj nazwę zasobu usługi Azure Arc i przypisz ją do roli **użytkownika Key Vault Secret (wersja zapoznawcza)** . W przypadku korzystania z [zasad dostępu Key Vault](../../key-vault/general/assign-access-policy-portal.md)należy przypisać uprawnienia do **pobierania** klucza tajnego do tożsamości przypisanej do systemu usługi Azure Arc.

### <a name="connected-machine-agent"></a>Agent połączonej maszyny

Sprawdź, czy maszyna jest zgodna z [obsługiwanymi wersjami](agent-overview.md#supported-operating-systems) systemu operacyjnego Windows i Linux dla agenta połączonego z platformą Azure.

Minimalna wersja agenta połączonej maszyny obsługiwana w przypadku tej funkcji w systemie Windows i Linux to wersja 1,0.

Aby uaktualnić maszynę do wymaganej wersji agenta, zobacz [upgrade Agent](manage-agent.md#upgrading-agent).

## <a name="next-steps"></a>Następne kroki

Można wdrażać i usuwać rozszerzenia maszyn wirtualnych oraz zarządzać nimi przy użyciu [interfejsu wiersza polecenia platformy Azure](manage-vm-extensions-cli.md), [Azure PowerShell](manage-vm-extensions-powershell.md), z [Azure Portal](manage-vm-extensions-portal.md)lub [szablonów Azure Resource Manager](manage-vm-extensions-template.md).
