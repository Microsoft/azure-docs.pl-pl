---
title: Zarządzanie rozszerzeniami maszyny wirtualnej za pomocą serwerów z obsługą usługi Azure Arc
description: Serwery z obsługą usługi Azure Arc mogą zarządzać wdrożeniem rozszerzeń maszyn wirtualnych, które zapewniają konfigurację po wdrożeniu i zadania automatyzacji z maszynami wirtualnymi spoza platformy Azure.
ms.date: 11/06/2020
ms.topic: conceptual
ms.openlocfilehash: 7682f6c8631bbaf2310d501d7cee6aecb2311226
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2020
ms.locfileid: "94358035"
---
# <a name="virtual-machine-extension-management-with-azure-arc-enabled-servers"></a>Zarządzanie rozszerzeniami maszyn wirtualnych za pomocą serwerów z obsługą usługi Azure Arc

Rozszerzenia maszyn wirtualnych to małe aplikacje, które zapewniają konfigurację po wdrożeniu i zadania automatyzacji na maszynach wirtualnych platformy Azure. Na przykład jeśli maszyna wirtualna wymaga instalacji oprogramowania, ochrony antywirusowej lub uruchomienia skryptu w swoim środowisku, można użyć rozszerzenia maszyny wirtualnej.

Serwery z obsługą usługi Azure ARC umożliwiają Wdrażanie rozszerzeń maszyn wirtualnych platformy Azure na maszynach wirtualnych z systemami Windows i Linux, które upraszczają zarządzanie maszynami w chmurze w środowisku lokalnym, w granicach i innych środowiskach chmurowych. Rozszerzeniami maszyn wirtualnych można zarządzać przy użyciu następujących metod na maszynach hybrydowych lub serwerach zarządzanych przez serwery z obsługą ARC:

- [Azure Portal](manage-vm-extensions-portal.md)
- [Interfejs wiersza polecenia platformy Azure](manage-vm-extensions-cli.md)
- [Azure PowerShell](manage-vm-extensions-powershell.md)
- [Szablony Menedżer zasobów](manage-vm-extensions-template.md) platformy Azure

## <a name="key-benefits"></a>Najważniejsze korzyści

Obsługa rozszerzenia maszyny wirtualnej z obsługą usługi Azure Arc zapewnia następujące korzyści:

- Użyj [konfiguracji stanu Azure Automation](../../automation/automation-dsc-overview.md) , aby centralnie przechowywać konfiguracje i zachować żądany stan maszyn połączonych hybrydowo włączonych przy użyciu rozszerzenia maszyny wirtualnej DSC.

- Zbieraj dane dziennika do analizy z [dziennikami w Azure monitor](../../azure-monitor/platform/data-platform-logs.md) włączone za pomocą rozszerzenia maszyny wirtualnej log Analytics Agent. Jest to przydatne w przypadku wykonywania złożonej analizy danych z różnych źródeł.

- Dzięki [Azure monitor dla maszyn wirtualnych](../../azure-monitor/insights/vminsights-overview.md)analizuje wydajność maszyn wirtualnych z systemami Windows i Linux oraz monitorują procesy i zależności od innych zasobów i procesów zewnętrznych. Jest to realizowane przez włączenie zarówno rozszerzenia agenta Log Analytics, jak i agenta zależności maszyny wirtualnej.

- Pobieranie i wykonywanie skryptów na maszynach połączonych hybrydowo przy użyciu niestandardowego rozszerzenia skryptu. To rozszerzenie jest przydatne w przypadku konfiguracji po wdrożeniu, instalacji oprogramowania lub innych zadań związanych z konfiguracją lub zarządzaniem.

- Automatyczne odświeżanie certyfikatów przechowywanych w [Azure Key Vault](../../key-vault/general/overview.md).

## <a name="availability"></a>Dostępność

Funkcjonalność rozszerzenia maszyny wirtualnej jest dostępna tylko na liście [obsługiwanych regionów](overview.md#supported-regions). Upewnij się, że Twoja maszyna została dołączona do jednego z tych regionów.

## <a name="extensions"></a>Rozszerzenia

W tej wersji obsługiwane są następujące rozszerzenia maszyn wirtualnych na komputerach z systemem Windows i Linux.

|Rozszerzenie |System operacyjny |Publisher |Dodatkowe informacje |
|----------|---|----------|-----------------------|
|CustomScriptExtension |Windows |Microsoft.Compute |[Rozszerzenie niestandardowego skryptu systemu Windows](../../virtual-machines/extensions/custom-script-windows.md)|
|DSC |Windows |Microsoft. PowerShell|[Rozszerzenie DSC środowiska Windows PowerShell](../../virtual-machines/extensions/dsc-windows.md)|
|Agent usługi Log Analytics |Windows |Microsoft. EnterpriseCloud. Monitoring |[Log Analytics rozszerzenie maszyny wirtualnej dla systemu Windows](../../virtual-machines/extensions/oms-windows.md)|
|Agent zależności firmy Microsoft | Windows |Microsoft.Compute | [Rozszerzenie maszyny wirtualnej agenta zależności dla systemu Windows](../../virtual-machines/extensions/agent-dependency-windows.md)|
|Key Vault | Windows | Microsoft.Compute | [Key Vault rozszerzenie maszyny wirtualnej dla systemu Windows](../../virtual-machines/extensions/key-vault-windows.md) |
|CustomScript|Linux |Microsoft. Azure. Extension |[Rozszerzenie niestandardowego skryptu systemu Linux w wersji 2](../../virtual-machines/extensions/custom-script-linux.md) |
|DSC |Linux |Microsoft. OSTCExtensions |[Rozszerzenie DSC programu PowerShell dla systemu Linux](../../virtual-machines/extensions/dsc-linux.md) |
|Agent usługi Log Analytics |Linux |Microsoft. EnterpriseCloud. Monitoring |[Log Analytics rozszerzenie maszyny wirtualnej dla systemu Linux](../../virtual-machines/extensions/oms-linux.md) |
|Agent zależności firmy Microsoft | Linux |Microsoft.Compute | [Rozszerzenie maszyny wirtualnej agenta zależności dla systemu Linux](../../virtual-machines/extensions/agent-dependency-linux.md) |
|Key Vault | Linux | Microsoft.Compute | [Key Vault rozszerzenie maszyny wirtualnej dla systemu Linux](../../virtual-machines/extensions/key-vault-linux.md) |

Aby dowiedzieć się więcej na temat pakietu agenta połączonego z platformą Azure i szczegółowych informacji o składniku agenta rozszerzeń, zobacz [Omówienie agenta](agent-overview.md#agent-component-details).

## <a name="prerequisites"></a>Wymagania wstępne

Ta funkcja jest zależna od następujących dostawców zasobów platformy Azure w ramach subskrypcji:

- **Microsoft. HybridCompute**
- **Microsoft. GuestConfiguration**

Jeśli nie zostały one jeszcze zarejestrowane, wykonaj kroki opisane w sekcji [Rejestrowanie dostawców zasobów platformy Azure](agent-overview.md#register-azure-resource-providers).

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
