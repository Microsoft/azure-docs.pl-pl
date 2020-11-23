---
title: Zainstaluj agenta Azure Monitor
description: Opcje instalowania agenta Azure Monitor (AMA) na maszynach wirtualnych platformy Azure i serwerach z obsługą usługi Azure Arc.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/17/2020
ms.openlocfilehash: 4c6252b31b4be05ea3c0bcf160a28bf335239b23
ms.sourcegitcommit: 5ae2f32951474ae9e46c0d46f104eda95f7c5a06
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/23/2020
ms.locfileid: "95324860"
---
# <a name="install-the-azure-monitor-agent-preview"></a>Zainstaluj agenta Azure Monitor (wersja zapoznawcza)
W tym artykule opisano różne opcje, które są obecnie dostępne do zainstalowania [agenta Azure monitor](azure-monitor-agent-overview.md) na maszynach wirtualnych platformy Azure i serwerach z obsługą usługi Azure ARC, a także opcje tworzenia [skojarzeń z regułami zbierania danych](data-collection-rule-azure-monitor-agent.md) , które określają, które dane mają być zbierane przez agenta.

## <a name="prerequisites"></a>Wymagania wstępne
Przed zainstalowaniem agenta Azure Monitor wymagane są następujące wymagania wstępne.

- [Tożsamość systemu zarządzanego](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md) musi być włączona na maszynach wirtualnych platformy Azure. Nie jest to wymagane w przypadku serwerów z obsługą usługi Azure Arc. Tożsamość systemowa zostanie włączona automatycznie, jeśli agent zostanie zainstalowany w ramach procesu [tworzenia i przypisywania reguły zbierania danych przy użyciu Azure Portal](#install-with-azure-portal).
- W sieci wirtualnej dla maszyny wirtualnej musi być włączony [tag usługi AzureResourceManager](../../virtual-network/service-tags-overview.md) .

## <a name="virtual-machine-extension-details"></a>Szczegóły rozszerzenia maszyny wirtualnej
Agent Azure Monitor jest zaimplementowany jako [rozszerzenie maszyny wirtualnej platformy Azure](../../virtual-machines/extensions/overview.md) ze szczegółami podanymi w poniższej tabeli. Można go zainstalować przy użyciu dowolnej metody instalacji rozszerzeń maszyn wirtualnych, w tym tych opisanych w tym artykule.

| Właściwość | Windows | Linux |
|:---|:---|:---|
| Publisher | Microsoft. Azure. Monitor  | Microsoft. Azure. Monitor |
| Typ      | AzureMonitorWindowsAgent | AzureMonitorLinuxAgent  |
| TypeHandlerVersion  | 1,0 | 1.5 |


## <a name="install-with-azure-portal"></a>Zainstaluj przy użyciu Azure Portal
Aby zainstalować agenta Azure Monitor przy użyciu Azure Portal, postępuj zgodnie z procesem [tworzenia reguły zbierania danych](data-collection-rule-azure-monitor-agent.md#create-rule-and-association-in-azure-portal) w Azure Portal. Umożliwia to skojarzenie reguły zbierania danych z co najmniej jedną maszyną wirtualną platformy Azure lub serwerami z obsługą usługi Azure Arc. Agent zostanie zainstalowany na dowolnej z tych maszyn wirtualnych, które go jeszcze nie posiadają.


## <a name="install-with-resource-manager-template"></a>Instalowanie przy użyciu szablonu Menedżer zasobów
Za pomocą szablonów Menedżer zasobów można zainstalować agenta Azure Monitor na maszynach wirtualnych platformy Azure oraz na serwerach z obsługą usługi Azure Arc i utworzyć skojarzenie z regułami zbierania danych. Przed utworzeniem skojarzenia należy utworzyć dowolną regułę zbierania danych.

Pobierz przykładowe szablony na potrzeby instalacji agenta i utwórz skojarzenie z następujących: 

- [Szablon do zainstalowania agenta Azure Monitor (Azure i Azure ARC)](../samples/resource-manager-agent.md#azure-monitor-agent-preview) 
- [Szablon służący do tworzenia skojarzenia z regułą zbierania danych](../samples/resource-manager-data-collection-rules.md)

Zainstaluj szablony przy użyciu [dowolnej metody wdrażania dla Menedżer zasobów szablonów](../../azure-resource-manager/templates/deploy-powershell.md) , takich jak następujące polecenia.

# <a name="powershell"></a>[Program PowerShell](#tab/ARMAgentPowerShell)
```powershell
New-AzResourceGroupDeployment -ResourceGroupName "<resource-group-name>" -TemplateFile "<template-filename.json>" -TemplateParameterFile "<parameter-filename.json>"
```
# <a name="cli"></a>[Interfejs wiersza polecenia](#tab/ARMAgentCLI)
```powershell
New-AzResourceGroupDeployment -ResourceGroupName "<resource-group-name>" -TemplateFile "<template-filename.json>" -TemplateParameterFile "<parameter-filename.json>"
```
---

## <a name="install-with-powershell"></a>Instalowanie przy użyciu programu PowerShell
Agenta Azure Monitor można zainstalować na maszynach wirtualnych platformy Azure oraz na serwerach z obsługą usługi Azure ARC przy użyciu polecenia programu PowerShell służącego do dodawania rozszerzenia maszyny wirtualnej. 

### <a name="azure-virtual-machines"></a>Maszyny wirtualne platformy Azure
Użyj następujących poleceń programu PowerShell, aby zainstalować agenta Azure Monitor na maszynach wirtualnych platformy Azure.
# <a name="windows"></a>[Windows](#tab/PowerShellWindows)
```powershell
Set-AzVMExtension -Name AMAWindows -ExtensionType AzureMonitorWindowsAgent -Publisher Microsoft.Azure.Monitor -ResourceGroupName <resource-group-name> -VMName <virtual-machine-name> -Location <location>
```
# <a name="linux"></a>[Linux](#tab/PowerShellLinux)
```powershell
Set-AzVMExtension -Name AMALinux -ExtensionType AzureMonitorLinuxAgent -Publisher Microsoft.Azure.Monitor -ResourceGroupName <resource-group-name> -VMName <virtual-machine-name> -Location <location>
```
---

### <a name="azure-arc-enabled-servers"></a>Serwery z usługą Azure Arc
Użyj następujących poleceń programu PowerShell, aby zainstalować serwer z włączoną funkcją Azure Monitor Agent onazure.
# <a name="windows"></a>[Windows](#tab/PowerShellWindowsArc)
```powershell
New-AzConnectedMachineExtension -Name AMAWindows -ExtensionType AzureMonitorWindowsAgent -Publisher Microsoft.Azure.Monitor -ResourceGroupName <resource-group-name> -VMName <virtual-machine-name> -Location <location>
```
# <a name="linux"></a>[Linux](#tab/PowerShellLinuxArc)
```powershell
New-AzConnectedMachineExtension -Name AMALinux -ExtensionType AzureMonitorLinuxAgent -Publisher Microsoft.Azure.Monitor -ResourceGroupName <resource-group-name> -VMName <virtual-machine-name> -Location <location>
```
---
## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure
Agenta Azure Monitor można zainstalować na maszynach wirtualnych platformy Azure i na serwerach z obsługą usługi Azure ARC przy użyciu interfejsu wiersza polecenia platformy Azure w celu dodania rozszerzenia maszyny wirtualnej. 

### <a name="azure-virtual-machines"></a>Maszyny wirtualne platformy Azure
Użyj następujących poleceń interfejsu wiersza polecenia, aby zainstalować agenta Azure Monitor na maszynach wirtualnych platformy Azure.
# <a name="windows"></a>[Windows](#tab/CLIWindows)
```azurecli
az vm extension set --name AzureMonitorWindowsAgent --publisher Microsoft.Azure.Monitor --ids <vm-resource-id>
```
# <a name="linux"></a>[Linux](#tab/CLILinux)
```azurecli
az vm extension set --name AzureMonitorLinuxAgent --publisher Microsoft.Azure.Monitor --ids <vm-resource-id>
```
---
### <a name="azure-arc-enabled-servers"></a>Serwery z usługą Azure Arc
Użyj następujących poleceń interfejsu wiersza polecenia w celu zainstalowania serwerów z obsługą usługi Azure Arc Azure Monitor Agent.

# <a name="windows"></a>[Windows](#tab/CLIWindowsArc)
```azurecli
az connectedmachine machine-extension create --name AzureMonitorWindowsAgent --publisher Microsoft.Azure.Monitor --ids <vm-resource-id>
```
# <a name="linux"></a>[Linux](#tab/CLILinuxArc)
```azurecli
az connectedmachine machine-extension create --name AzureMonitorLinuxAgent --publisher Microsoft.Azure.Monitor --ids <vm-resource-id>
```
---


## <a name="next-steps"></a>Następne kroki

- [Utwórz regułę zbierania danych](data-collection-rule-azure-monitor-agent.md) , aby zbierać dane z agenta i wysyłać je do Azure monitor.
