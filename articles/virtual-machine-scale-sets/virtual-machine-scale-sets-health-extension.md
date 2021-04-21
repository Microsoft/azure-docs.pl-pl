---
title: Używanie rozszerzenia kondycji aplikacji z zestawami skalowania maszyn wirtualnych platformy Azure
description: Dowiedz się, jak za pomocą rozszerzenia Application Health monitorować kondycję aplikacji wdrożonych w zestawach skalowania maszyn wirtualnych.
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: extensions
ms.date: 05/06/2020
ms.reviewer: mimckitt
ms.custom: mimckitt
ms.openlocfilehash: 381573ae40f6c31a1c7dbf18bc60be5944fff39e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107762897"
---
# <a name="using-application-health-extension-with-virtual-machine-scale-sets"></a>Używanie rozszerzenia kondycji aplikacji z zestawami skalowania maszyn wirtualnych
Monitorowanie kondycji aplikacji jest ważnym sygnałem do zarządzania wdrożeniem i uaktualniania go. Zestawy skalowania maszyn [](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model) wirtualnych platformy Azure zapewniają obsługę uaktualnień stopniowych, [](virtual-machine-scale-sets-automatic-upgrade.md)w tym automatyczne uaktualnienia obrazu systemu operacyjnego, które polegają na monitorowaniu kondycji poszczególnych wystąpień w celu uaktualnienia wdrożenia. Za pomocą rozszerzenia kondycji można również monitorować kondycję poszczególnych wystąpień w zestawie skalowania i naprawiać wystąpienia przy użyciu [automatycznych napraw wystąpień.](virtual-machine-scale-sets-automatic-instance-repairs.md)

W tym artykule opisano, jak za pomocą rozszerzenia kondycji aplikacji można monitorować kondycję aplikacji wdrożonych w zestawach skalowania maszyn wirtualnych.

## <a name="prerequisites"></a>Wymagania wstępne
W tym artykule założono, że znasz:
-   Rozszerzenia maszyn wirtualnych [platformy Azure](../virtual-machines/extensions/overview.md)
-   [Modyfikowanie zestawów](virtual-machine-scale-sets-upgrade-scale-set.md) skalowania maszyn wirtualnych

## <a name="when-to-use-the-application-health-extension"></a>Kiedy należy używać rozszerzenia kondycji aplikacji
Rozszerzenie kondycji aplikacji jest wdrażane wewnątrz wystąpienia zestawu skalowania maszyn wirtualnych i raportuje o kondycji maszyny wirtualnej z wewnątrz wystąpienia zestawu skalowania. Można skonfigurować rozszerzenie do sondowania w punkcie końcowym aplikacji i aktualizowania stanu aplikacji w tym wystąpieniu. Ten stan wystąpienia jest sprawdzany przez platformę Azure w celu określenia, czy wystąpienie kwalifikuje się do operacji uaktualniania.

Ponieważ rozszerzenie zgłasza kondycję z poziomu maszyny wirtualnej, rozszerzenie może być używane w sytuacjach, w których [](../load-balancer/load-balancer-custom-probe-overview.md)nie można używać sond zewnętrznych, takich jak sondy kondycji aplikacji (które korzystają z niestandardowych sond Azure Load Balancer).

## <a name="extension-schema"></a>Schemat rozszerzenia

Poniższy kod JSON przedstawia schemat rozszerzenia kondycji aplikacji. Rozszerzenie wymaga co najmniej żądania "tcp", "http" lub "https" odpowiednio ze skojarzonym portem lub ścieżką żądania.

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
| apiVersion | `2018-10-01` | data |
| publisher | `Microsoft.ManagedServices` | ciąg |
| typ | `ApplicationHealthLinux` (Linux), `ApplicationHealthWindows` (Windows) | ciąg |
| typeHandlerVersion | `1.0` | int |

### <a name="settings"></a>Ustawienia

| Nazwa | Wartość/przykład | Typ danych
| ---- | ---- | ----
| Protokół | `http`lub `https``tcp` | ciąg |
| port | Opcjonalne, gdy protokół jest `http` `https` lub , obowiązkowy, gdy protokół jest `tcp` | int |
| requestPath | Obowiązkowe, gdy protokół jest `http` lub `https` , niedozwolone, gdy protokół jest `tcp` | ciąg |

## <a name="deploy-the-application-health-extension"></a>Wdrażanie rozszerzenia kondycji aplikacji
Istnieje wiele sposobów wdrażania rozszerzenia kondycji aplikacji w zestawach skalowania zgodnie z poniższymi przykładami.

### <a name="rest-api"></a>Interfejs API REST

W poniższym przykładzie dodano rozszerzenie kondycji aplikacji (o nazwie myHealthExtension) do pliku extensionProfile w modelu zestawu skalowania zestawu skalowania opartego na systemie Windows.

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
Użyj `PATCH` , aby edytować już wdrożone rozszerzenie.

### <a name="azure-powershell"></a>Azure PowerShell

Użyj polecenia cmdlet [Add-AzVmssExtension,](/powershell/module/az.compute/add-azvmssextension) aby dodać rozszerzenie Kondycja aplikacji do definicji modelu zestawu skalowania.

W poniższym przykładzie dodano rozszerzenie Kondycja aplikacji do rozszerzenia w modelu zestawu skalowania zestawu skalowania opartego `extensionProfile` na systemie Windows. W przykładzie użyto nowego modułu Az programu PowerShell.

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

Użyj [rozszerzenia az vmss extension set,](/cli/azure/vmss/extension#az_vmss_extension_set) aby dodać rozszerzenie kondycji aplikacji do definicji modelu zestawu skalowania.

Poniższy przykład dodaje rozszerzenie Kondycja aplikacji do modelu zestawu skalowania zestawu skalowania opartego na systemie Linux.

```azurecli-interactive
az vmss extension set \
  --name ApplicationHealthLinux \
  --publisher Microsoft.ManagedServices \
  --version 1.0 \
  --resource-group <myVMScaleSetResourceGroup> \
  --vmss-name <myVMScaleSet> \
  --settings ./extension.json
```
Informacje extension.jsna temat zawartości pliku.

```json
{
  "protocol": "<protocol>",
  "port": "<port>",
  "requestPath": "</requestPath>"
}
```


## <a name="troubleshoot"></a>Rozwiązywanie problemów
Dane wyjściowe wykonywania rozszerzenia są rejestrowane w plikach w następujących katalogach:

```Windows
C:\WindowsAzure\Logs\Plugins\Microsoft.ManagedServices.ApplicationHealthWindows\<version>\
```

```Linux
/var/lib/waagent/Microsoft.ManagedServices.ApplicationHealthLinux-<extension_version>/status
/var/log/azure/applicationhealth-extension
```

Dzienniki okresowo przechwytują również stan kondycji aplikacji.

## <a name="next-steps"></a>Następne kroki
Dowiedz się, jak [wdrożyć aplikację w zestawach](virtual-machine-scale-sets-deploy-app.md) skalowania maszyn wirtualnych.
