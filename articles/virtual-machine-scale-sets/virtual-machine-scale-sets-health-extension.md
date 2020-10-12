---
title: Używanie rozszerzenia kondycji aplikacji z zestawami skalowania maszyn wirtualnych platformy Azure
description: Dowiedz się, jak używać rozszerzenia kondycja aplikacji do monitorowania kondycji aplikacji wdrożonych w zestawach skalowania maszyn wirtualnych.
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: extensions
ms.date: 05/06/2020
ms.reviewer: mimckitt
ms.custom: mimckitt
ms.openlocfilehash: a38a715b45ab4d0810862ef4d016e4187ea507ab
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "84783048"
---
# <a name="using-application-health-extension-with-virtual-machine-scale-sets"></a>Używanie rozszerzenia kondycji aplikacji z zestawami skalowania maszyn wirtualnych
Monitorowanie kondycji aplikacji jest ważnym sygnałem do zarządzania wdrożeniem i uaktualniania go. Zestawy skalowania maszyn wirtualnych platformy Azure zapewniają obsługę [uaktualnień stopniowych](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model) , takich jak [automatyczne uaktualnienia obrazu systemu operacyjnego](virtual-machine-scale-sets-automatic-upgrade.md), które polegają na monitorowaniu kondycji poszczególnych wystąpień w celu uaktualnienia wdrożenia. Można również użyć rozszerzenia kondycji do monitorowania kondycji aplikacji każdego wystąpienia w zestawie skalowania i wykonywania napraw wystąpień przy użyciu [automatycznych napraw wystąpień](virtual-machine-scale-sets-automatic-instance-repairs.md).

W tym artykule opisano, jak można użyć rozszerzenia kondycja aplikacji do monitorowania kondycji aplikacji wdrożonych w ramach zestawów skalowania maszyn wirtualnych.

## <a name="prerequisites"></a>Wymagania wstępne
W tym artykule założono, że znasz:
-   [Rozszerzenia](../virtual-machines/extensions/overview.md) maszyny wirtualnej platformy Azure
-   [Modyfikowanie](virtual-machine-scale-sets-upgrade-scale-set.md) zestawów skalowania maszyn wirtualnych

## <a name="when-to-use-the-application-health-extension"></a>Kiedy używać rozszerzenia kondycji aplikacji
Rozszerzenie kondycji aplikacji jest wdrażane wewnątrz wystąpienia zestawu skalowania maszyn wirtualnych i raportuje kondycję maszyny wirtualnej z wewnątrz wystąpienia zestawu skalowania. Można skonfigurować rozszerzenie do sondowania w punkcie końcowym aplikacji i zaktualizować stan aplikacji w tym wystąpieniu. To wystąpienie jest sprawdzane przez platformę Azure w celu ustalenia, czy wystąpienie kwalifikuje się do operacji uaktualniania.

Ponieważ rozszerzenie raportuje kondycję z maszyny wirtualnej, rozszerzenie może być używane w sytuacjach, w których nie można używać sond zewnętrznych, takich jak sondy kondycji aplikacji (które korzystają z [sond](../load-balancer/load-balancer-custom-probe-overview.md)Azure Load Balancer niestandardowych).

## <a name="extension-schema"></a>Schemat rozszerzenia

Poniższy kod JSON przedstawia schemat rozszerzenia kondycji aplikacji. Rozszerzenie wymaga co najmniej jednego żądania "TCP", "http" lub "https" ze skojarzonym portem lub ścieżką żądania.

```json
{
  "type": "extensions",
  "name": "HealthExtension",
  "apiVersion": "2018-10-01",
  "location": "<location>",  
  "properties": {
    "publisher": "Microsoft.ManagedServices",
    "type": "< ApplicationHealthLinux or ApplicationHealthWindows>",
    "autoUpgradeMinorVersion": true,
    "typeHandlerVersion": "1.0",
    "settings": {
      "protocol": "<protocol>",
      "port": "<port>",
      "requestPath": "</requestPath>"
    }
  }
}  
```

### <a name="property-values"></a>Wartości właściwości

| Nazwa | Wartość/przykład | Typ danych
| ---- | ---- | ---- 
| apiVersion | `2018-10-01` | date |
| publisher | `Microsoft.ManagedServices` | ciąg |
| typ | `ApplicationHealthLinux` (Linux), `ApplicationHealthWindows` (system Windows) | ciąg |
| typeHandlerVersion | `1.0` | int |

### <a name="settings"></a>Ustawienia

| Nazwa | Wartość/przykład | Typ danych
| ---- | ---- | ----
| protokol | `http` lub `https` lub `tcp` | ciąg |
| port | Opcjonalne, gdy protokół jest `http` lub `https` , obowiązkowy, gdy protokół jest `tcp` | int |
| requestPath | Obowiązkowe, gdy protokół jest `http` lub `https` , nie jest dozwolony, gdy protokół jest `tcp` | ciąg |

## <a name="deploy-the-application-health-extension"></a>Wdróż rozszerzenie kondycji aplikacji
Istnieje wiele sposobów wdrażania rozszerzenia kondycji aplikacji w zestawach skalowania, jak opisano w poniższych przykładach.

### <a name="rest-api"></a>Interfejs API REST

Poniższy przykład dodaje rozszerzenie kondycji aplikacji (o nazwie myHealthExtension) do extensionProfile w modelu zestawu skalowania zestawu skalowania na podstawie systemu Windows.

```
PUT on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/extensions/myHealthExtension?api-version=2018-10-01`
```

```json
{
  "name": "myHealthExtension",
  "properties": {
    "publisher": " Microsoft.ManagedServices",
    "type": "< ApplicationHealthWindows>",
    "autoUpgradeMinorVersion": true,
    "typeHandlerVersion": "1.0",
    "settings": {
      "protocol": "<protocol>",
      "port": "<port>",
      "requestPath": "</requestPath>"
    }
  }
}
```
Służy `PATCH` do edytowania już wdrożonego rozszerzenia.

### <a name="azure-powershell"></a>Azure PowerShell

Użyj polecenia cmdlet [Add-AzVmssExtension](/powershell/module/az.compute/add-azvmssextension) , aby dodać rozszerzenie kondycja aplikacji do definicji modelu zestawu skalowania.

Poniższy przykład dodaje rozszerzenie kondycji aplikacji do `extensionProfile` modelu zestawu skalowania na podstawie zestawu skalowania systemu Windows. W przykładzie zastosowano nowy AZ PowerShell module.

```azurepowershell-interactive
# Define the scale set variables
$vmScaleSetName = "myVMScaleSet"
$vmScaleSetResourceGroup = "myVMScaleSetResourceGroup"

# Define the Application Health extension properties
$publicConfig = @{"protocol" = "http"; "port" = 80; "requestPath" = "/healthEndpoint"};
$extensionName = "myHealthExtension"
$extensionType = "ApplicationHealthWindows"
$publisher = "Microsoft.ManagedServices"

# Get the scale set object
$vmScaleSet = Get-AzVmss `
  -ResourceGroupName $vmScaleSetResourceGroup `
  -VMScaleSetName $vmScaleSetName

# Add the Application Health extension to the scale set model
Add-AzVmssExtension -VirtualMachineScaleSet $vmScaleSet `
  -Name $extensionName `
  -Publisher $publisher `
  -Setting $publicConfig `
  -Type $extensionType `
  -TypeHandlerVersion "1.0" `
  -AutoUpgradeMinorVersion $True

# Update the scale set
Update-AzVmss -ResourceGroupName $vmScaleSetResourceGroup `
  -Name $vmScaleSetName `
  -VirtualMachineScaleSet $vmScaleSet
```


### <a name="azure-cli-20"></a>Interfejs wiersza polecenia platformy Azure 2.0

Użyj [AZ VMSS Extension Set](/cli/azure/vmss/extension#az-vmss-extension-set) , aby dodać rozszerzenie kondycji aplikacji do definicji modelu zestawu skalowania.

Poniższy przykład dodaje rozszerzenie kondycja aplikacji do modelu zestawu skalowania opartego na systemie Linux.

```azurecli-interactive
az vmss extension set \
  --name ApplicationHealthLinux \
  --publisher Microsoft.ManagedServices \
  --version 1.0 \
  --resource-group <myVMScaleSetResourceGroup> \
  --vmss-name <myVMScaleSet> \
  --settings ./extension.json
```
extension.jszawartości pliku.

```json
{
  "protocol": "<protocol>",
  "port": "<port>",
  "requestPath": "</requestPath>"
}
```


## <a name="troubleshoot"></a>Rozwiązywanie problemów
Dane wyjściowe wykonania rozszerzenia są rejestrowane w plikach znalezionych w następujących katalogach:

```Windows
C:\WindowsAzure\Logs\Plugins\Microsoft.ManagedServices.ApplicationHealthWindows\<version>\
```

```Linux
/var/lib/waagent/Microsoft.ManagedServices.ApplicationHealthLinux-<extension_version>/status
/var/log/azure/applicationhealth-extension
```

Dzienniki również okresowo przechwytują stan kondycji aplikacji.

## <a name="next-steps"></a>Następne kroki
Dowiedz się, jak [wdrożyć aplikację](virtual-machine-scale-sets-deploy-app.md) w zestawach skalowania maszyn wirtualnych.
