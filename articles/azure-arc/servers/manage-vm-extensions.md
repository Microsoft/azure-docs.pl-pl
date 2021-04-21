---
title: Zarządzanie rozszerzeniami maszyny wirtualnej Azure Arc serwerami z włączoną obsługą usługi
description: Azure Arc mogą zarządzać wdrażaniem rozszerzeń maszyn wirtualnych, które zapewniają konfigurację po wdrożeniu i zadania automatyzacji z maszynami wirtualnymi spoza platformy Azure.
ms.date: 04/13/2021
ms.topic: conceptual
ms.openlocfilehash: e28cd7753fc85f2e40385c65392fea73502aa05b
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107832846"
---
# <a name="virtual-machine-extension-management-with-azure-arc-enabled-servers"></a>Zarządzanie rozszerzeniami maszyn wirtualnych na serwerach z obsługą usługi Azure Arc

Rozszerzenia maszyny wirtualnej to małe aplikacje, które zapewniają konfigurację po wdrożeniu i zadania automatyzacji na maszynach wirtualnych platformy Azure. Jeśli na przykład maszyna wirtualna wymaga instalacji oprogramowania, ochrony antywirusowej lub uruchomienia w nim skryptu, można użyć rozszerzenia maszyny wirtualnej.

Azure Arc umożliwiają wdrażanie rozszerzeń maszyn wirtualnych platformy Azure na maszynach wirtualnych z systemami Windows i Linux spoza platformy Azure, co upraszcza zarządzanie maszyną hybrydową w całym ich cyklu życia. Rozszerzeniami maszyn wirtualnych można zarządzać przy użyciu następujących metod na maszynach hybrydowych lub serwerach zarządzanych przez serwery z usługą Arc:

- W [Azure Portal](manage-vm-extensions-portal.md)
- Interfejs [wiersza polecenia platformy Azure](manage-vm-extensions-cli.md)
- [Azure PowerShell](manage-vm-extensions-powershell.md)
- Szablony [Resource Manager Azure](manage-vm-extensions-template.md)

> [!NOTE]
> Azure Arc obsługuje wdrażania rozszerzeń maszyn wirtualnych i zarządzania nimi na maszynach wirtualnych platformy Azure. W przypadku maszyn wirtualnych platformy Azure zobacz następujący artykuł [z omówieniem rozszerzenia maszyny wirtualnej.](../../virtual-machines/extensions/overview.md)

## <a name="key-benefits"></a>Najważniejsze korzyści

Azure Arc obsługi rozszerzeń maszyn wirtualnych z włączonymi serwerami zapewnia następujące kluczowe korzyści:

- Zbieraj dane dzienników do analizy za pomocą dzienników [Azure Monitor,](../../azure-monitor/logs/data-platform-logs.md) włączając rozszerzenie maszyny wirtualnej agenta usługi Log Analytics. Jest to przydatne w przypadku wykonywania złożonej analizy danych z różnych źródeł.

- Dzięki [analizom maszyny](../../azure-monitor/vm/vminsights-overview.md)wirtualnej analizuje ona wydajność maszyn wirtualnych z systemami Windows i Linux oraz monitoruje ich procesy i zależności od innych zasobów i procesów zewnętrznych. Można to osiągnąć, włączając zarówno agenta usługi Log Analytics, jak i rozszerzenia maszyny wirtualnej agenta zależności.

- Pobieranie i wykonywanie skryptów na połączonych maszynach hybrydowych przy użyciu rozszerzenia niestandardowego skryptu. To rozszerzenie jest przydatne w przypadku konfiguracji po wdrożeniu, instalacji oprogramowania lub innych zadań konfiguracji lub zarządzania.

- Automatyczne odświeżanie certyfikatów przechowywanych w [Azure Key Vault](../../key-vault/general/overview.md).

## <a name="availability"></a>Dostępność

Funkcja rozszerzenia maszyny wirtualnej jest dostępna tylko na liście [obsługiwanych regionów.](overview.md#supported-regions) Upewnij się, że dołączasz maszynę w jednym z tych regionów.

## <a name="extensions"></a>Rozszerzenia

W tej wersji obsługujemy następujące rozszerzenia maszyn wirtualnych na maszynach z systemami Windows i Linux.

Aby dowiedzieć się więcej o Azure Connected Machine agenta rozszerzenia i szczegółowe informacje o składniku agenta rozszerzenia, zobacz [Omówienie agenta](agent-overview.md#agent-component-details).

> [!NOTE]
> Niedawno usunięto obsługę rozszerzenia maszyny wirtualnej DSC dla serwerów z usługą Arc. Alternatywnie zalecamy użycie rozszerzenia niestandardowego skryptu do zarządzania konfiguracją po wdrożeniu serwera lub maszyny.

### <a name="windows-extensions"></a>Rozszerzenia systemu Windows

|Wewnętrzny |Publisher |Typ |Dodatkowe informacje |
|----------|----------|-----|-----------------------|
|Azure Defender zintegrowany skaner luk w zabezpieczeniach |Qualys |WindowsAgent.AzureSecurityCenter |[Azure Defender zintegrowane rozwiązanie do oceny luk w zabezpieczeniach dla platformy Azure i maszyn hybrydowych](../../security-center/deploy-vulnerability-assessment-vm.md)|
|Rozszerzenie niestandardowego skryptu |Microsoft.Compute | CustomScriptExtension |[Rozszerzenie niestandardowego skryptu systemu Windows](../../virtual-machines/extensions/custom-script-windows.md)|
|Agent usługi Log Analytics |Microsoft.EnterpriseCloud.Monitoring |MicrosoftMonitoringAgent |[Rozszerzenie maszyny wirtualnej usługi Log Analytics dla systemu Windows](../../virtual-machines/extensions/oms-windows.md)|
|Azure Monitor dla maszyn wirtualnych (szczegółowe informacje) |Microsoft.Azure.Monitoring.DependencyAgent |DependencyAgentWindows | [Rozszerzenie maszyny wirtualnej agenta zależności dla systemu Windows](../../virtual-machines/extensions/agent-dependency-windows.md)|
|Azure Key Vault synchronizacji certyfikatów | Microsoft.Azure.Key.Vault |KeyVaultForWindows | [Key Vault maszyny wirtualnej dla systemu Windows](../../virtual-machines/extensions/key-vault-windows.md) |
|Agent usługi Azure Monitor |Microsoft.Azure.Monitor |AzureMonitorWindowsAgent |[Instalowanie agenta Azure Monitor (wersja zapoznawcza)](../../azure-monitor/agents/azure-monitor-agent-install.md) |

### <a name="linux-extensions"></a>Rozszerzenia systemu Linux

|Wewnętrzny |Publisher |Typ |Dodatkowe informacje |
|----------|----------|-----|-----------------------|
|Azure Defender zintegrowany skaner luk w zabezpieczeniach |Qualys |LinuxAgent.AzureSecurityCenter |[Azure Defender zintegrowane rozwiązanie do oceny luk w zabezpieczeniach dla platformy Azure i maszyn hybrydowych](../../security-center/deploy-vulnerability-assessment-vm.md)|
|Rozszerzenie niestandardowego skryptu |Microsoft.Azure.Extensions |CustomScript |[Rozszerzenie niestandardowego skryptu systemu Linux w wersji 2](../../virtual-machines/extensions/custom-script-linux.md) |
|Agent usługi Log Analytics |Microsoft.EnterpriseCloud.Monitoring |OmsAgentForLinux |[Rozszerzenie maszyny wirtualnej usługi Log Analytics dla systemu Linux](../../virtual-machines/extensions/oms-linux.md) |
|Azure Monitor dla maszyn wirtualnych (szczegółowe informacje) |Microsoft.Azure.Monitoring.DependencyAgent |DependencyAgentLinux |[Rozszerzenie maszyny wirtualnej agenta zależności dla systemu Linux](../../virtual-machines/extensions/agent-dependency-linux.md) |
|Azure Key Vault synchronizacji certyfikatów | Microsoft.Azure.Key.Vault |KeyVaultForLinux | [Key Vault maszyny wirtualnej dla systemu Linux](../../virtual-machines/extensions/key-vault-linux.md) |
|Agent usługi Azure Monitor |Microsoft.Azure.Monitor |AzureMonitorLinuxAgent |[Instalowanie agenta Azure Monitor (wersja zapoznawcza)](../../azure-monitor/agents/azure-monitor-agent-install.md) |

## <a name="prerequisites"></a>Wymagania wstępne

Ta funkcja zależy od następujących dostawców zasobów platformy Azure w subskrypcji:

- **Microsoft.HybridCompute**
- **Microsoft.GuestConfiguration**

Jeśli nie są jeszcze zarejestrowani, postępuj zgodnie z instrukcjami w obszarze [Rejestrowanie dostawców zasobów platformy Azure.](agent-overview.md#register-azure-resource-providers)

Zapoznaj się z dokumentacją każdego rozszerzenia maszyny wirtualnej, do których odwołuje się poprzednia tabela, aby dowiedzieć się, czy ma ono jakiekolwiek wymagania sieciowe lub systemowe. Może to pomóc uniknąć problemów z łącznością z usługą lub funkcją platformy Azure, która opiera się na tym rozszerzeniu maszyny wirtualnej.

### <a name="log-analytics-vm-extension"></a>Rozszerzenie maszyny wirtualnej usługi Log Analytics

Rozszerzenie maszyny wirtualnej agenta usługi Log Analytics dla systemu Linux wymaga zainstalowania języka Python 2.x na maszynie docelowej.

### <a name="azure-key-vault-vm-extension-preview"></a>Azure Key Vault maszyny wirtualnej (wersja zapoznawcza)

Rozszerzenie Key Vault maszyny wirtualnej (wersja zapoznawcza) nie obsługuje następujących systemów operacyjnych Linux:

- CentOS Linux 7 (x64)
- Red Hat Enterprise Linux (RHEL) 7 (x64)
- Amazon Linux 2 (x64)

Wdrażanie rozszerzenia maszyny Key Vault wirtualnej (wersja zapoznawcza) jest obsługiwane tylko przy użyciu:

- Interfejs wiersza polecenia platformy Azure
- The Azure PowerShell
- Szablon usługi Azure Resource Manager

Przed wdrożeniem rozszerzenia należy wykonać następujące czynności:

1. [Utwórz magazyn i certyfikat](../../key-vault/certificates/quick-create-portal.md) (z podpisem własnym lub importu).

2. Przyznaj serwerowi Azure Arc dostęp do tajnego certyfikatu. Jeśli używasz wersji zapoznawczej [kontroli RBAC,](../../key-vault/general/rbac-guide.md)wyszukaj nazwę zasobu Azure Arc i przypisz do niego rolę Key Vault Wpisy tajne **(wersja zapoznawcza).** Jeśli używasz zasad [dostępu](../../key-vault/general/assign-access-policy-portal.md)Key Vault, przypisz uprawnienia **Tajne** uzyskiwanie do tożsamości przypisanej przez system Azure Arc zasobu.

### <a name="connected-machine-agent"></a>Agent połączonej maszyny

Sprawdź, czy maszyna jest [obsługiwana w obsługiwanych wersjach](agent-overview.md#supported-operating-systems) systemu operacyjnego Windows i Linux Azure Connected Machine agenta.

Minimalna wersja agenta Connected Machine obsługiwana przez tę funkcję w systemach Windows i Linux to wersja 1.0.

Aby uaktualnić maszynę do wymaganej wersji agenta, zobacz [Uaktualnianie agenta](manage-agent.md#upgrading-agent).

## <a name="next-steps"></a>Następne kroki

Rozszerzenia maszyn wirtualnych można wdrażać i usuwać oraz zarządzać nimi przy użyciu interfejsu wiersza polecenia platformy [Azure,](manage-vm-extensions-cli.md)interfejsu [Azure PowerShell](manage-vm-extensions-powershell.md), Azure Portal [lub](manage-vm-extensions-portal.md)Azure Resource Manager [szablonów.](manage-vm-extensions-template.md)
