---
title: Używanie sekwencjonowania rozszerzeń z zestawami skalowania maszyn wirtualnych platformy Azure
description: Dowiedz się, jak sekwencji rozszerzenia inicjowania obsługi administracyjnej podczas wdrażania wielu rozszerzeń w zestawach skalowania maszyn wirtualnych.
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: extensions
ms.date: 01/30/2019
ms.reviewer: mimckitt
ms.custom: mimckitt
ms.openlocfilehash: 1b5aea1f0f0101231408dc9ad7b57a30f2c86256
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107788161"
---
# <a name="sequence-extension-provisioning-in-virtual-machine-scale-sets"></a>Aprowizowanie rozszerzenia sekwencji w zestawach skalowania maszyn wirtualnych
Rozszerzenia maszyn wirtualnych platformy Azure zapewniają możliwości, takie jak konfiguracja i zarządzanie po wdrożeniu, monitorowanie, zabezpieczenia i nie tylko. Wdrożenia produkcyjne zwykle używają kombinacji wielu rozszerzeń skonfigurowanych dla wystąpień maszyn wirtualnych w celu osiągnięcia żądanych wyników.

W przypadku korzystania z wielu rozszerzeń na maszynie wirtualnej należy upewnić się, że rozszerzenia wymagające tych samych zasobów systemu operacyjnego nie próbują uzyskać tych zasobów w tym samym czasie. Niektóre rozszerzenia zależą również od innych rozszerzeń w celu zapewnienia wymaganych konfiguracji, takich jak ustawienia środowiska i wpisy tajne. Bez prawidłowej kolejności i sekwencjonowania wdrożenia rozszerzeń zależnych mogą się nie powieść.

W tym artykule szczegółowo opisano sposób sekwencji rozszerzeń do skonfigurowania dla wystąpień maszyn wirtualnych w zestawach skalowania maszyn wirtualnych.

## <a name="prerequisites"></a>Wymagania wstępne
W tym artykule założono, że znasz:
-   Rozszerzenia maszyn wirtualnych [platformy](../virtual-machines/extensions/overview.md) Azure
-   [Modyfikowanie zestawów](virtual-machine-scale-sets-upgrade-scale-set.md) skalowania maszyn wirtualnych

## <a name="when-to-use-extension-sequencing"></a>Kiedy używać sekwencjonowania rozszerzeń
Rozszerzenia sekwencjonowania nie są obowiązkowe dla zestawów skalowania i o ile nie zostaną określone, rozszerzenia mogą być aprowowane w wystąpieniu zestawu skalowania w dowolnej kolejności.

Jeśli na przykład model zestawu skalowania ma dwa rozszerzenia — ExtensionA i ExtensionB — określone w modelu, może wystąpić każda z następujących sekwencji aprowacji:
-   ExtensionA -> ExtensionB
-   ExtensionB -> ExtensionA

Jeśli aplikacja wymaga, aby rozszerzenie A zawsze było aprowizowane przed rozszerzeniem B, należy użyć sekwencjonowania rozszerzeń zgodnie z opisem w tym artykule. Sekwencjonowanie rozszerzeń spowoduje teraz tylko jedną sekwencję:
-   ExtensionA — > ExtensionB

Wszystkie rozszerzenia, które nie zostały określone w zdefiniowanej sekwencji aprowacji, mogą być aprowowane w dowolnym momencie, w tym przed, po lub w ramach zdefiniowanej sekwencji. Sekwencjonowanie rozszerzeń określa tylko, że określone rozszerzenie zostanie aprowowane po innym konkretnym rozszerzeniu. Nie ma to wpływu na aprowizowanie jakichkolwiek innych rozszerzeń zdefiniowanych w modelu.

Jeśli na przykład model zestawu skalowania ma trzy rozszerzenia — rozszerzenie A, rozszerzenie B i rozszerzenie C — określone w modelu, a rozszerzenie C jest ustawione na aprowizowanie po rozszerzeniu A, może wystąpić każda z następujących sekwencji aprowacji:
-   ExtensionA -> ExtensionC -> ExtensionB
-   ExtensionB -> ExtensionA -> ExtensionC
-   ExtensionA -> ExtensionB -> ExtensionC

Jeśli musisz upewnić się, że żadne inne rozszerzenie nie jest aprowowane podczas wykonywania zdefiniowanej sekwencji rozszerzeń, zalecamy sekwencjonowanie wszystkich rozszerzeń w modelu zestawu skalowania. W powyższym przykładzie dla rozszerzenia B można ustawić aprowizować po rozszerzeniu C w taki sposób, że może wystąpić tylko jedna sekwencja:
-   ExtensionA -> ExtensionC -> ExtensionB


## <a name="how-to-use-extension-sequencing"></a>Jak używać sekwencjonowania rozszerzeń
Aby aprowizować rozszerzenia sekwencji, należy zaktualizować definicję rozszerzenia w modelu zestawu skalowania tak, aby zawierała właściwość "provisionAfterExtensions", która akceptuje tablicę nazw rozszerzeń. Rozszerzenia wymienione w wartości tablicy właściwości muszą być w pełni zdefiniowane w modelu zestawu skalowania.

### <a name="template-deployment"></a>Wdrażanie szablonu
W poniższym przykładzie zdefiniowano szablon, w którym zestaw skalowania ma trzy rozszerzenia — ExtensionA, ExtensionB i ExtensionC — tak, aby rozszerzenia zostały aprowowane w następującej kolejności:
-   ExtensionA -> ExtensionB -> ExtensionC

```json
"virtualMachineProfile": {
  "extensionProfile": {
    "extensions": [
      {
        "name": "ExtensionA",
        "properties": {
          "publisher": "ExtensionA.Publisher",
          "settings": {},
          "typeHandlerVersion": "1.0",
          "autoUpgradeMinorVersion": true,
          "type": "ExtensionA"
        }
      },
      {
        "name": "ExtensionB",
        "properties": {
          "provisionAfterExtensions": [
            "ExtensionA"
          ],
          "publisher": "ExtensionB.Publisher",
          "settings": {},
          "typeHandlerVersion": "2.0",
          "autoUpgradeMinorVersion": true,
          "type": "ExtensionB"
        }
      }, 
      {
        "name": "ExtensionC",
        "properties": {
          "provisionAfterExtensions": [
            "ExtensionB"
          ],
          "publisher": "ExtensionC.Publisher",
          "settings": {},
          "typeHandlerVersion": "3.0",
          "autoUpgradeMinorVersion": true,
          "type": "ExtensionC"                   
        }
      }
    ]
  }
}
```

Ponieważ właściwość "provisionAfterExtensions" akceptuje tablicę nazw rozszerzeń, powyższy przykład można zmodyfikować w taki sposób, aby rozszerzenie ExtensionC było aprowowane po rozszerzeniach ExtensionA i ExtensionB, ale nie jest wymagana kolejność między rozszerzeniami ExtensionA i ExtensionB. W celu osiągnięcia tego scenariusza można użyć następującego szablonu:

```json
"virtualMachineProfile": {
  "extensionProfile": {
    "extensions": [
      {
        "name": "ExtensionA",
        "properties": {
          "publisher": "ExtensionA.Publisher",
          "settings": {},
          "typeHandlerVersion": "1.0",
          "autoUpgradeMinorVersion": true,
          "type": "ExtensionA"
        }
      },
      {
        "name": "ExtensionB",
        "properties": {
          "publisher": "ExtensionB.Publisher",
          "settings": {},
          "typeHandlerVersion": "2.0",
          "autoUpgradeMinorVersion": true,
          "type": "ExtensionB"
        }
      }, 
      {
        "name": "ExtensionC",
        "properties": {
          "provisionAfterExtensions": [
            "ExtensionA","ExtensionB"
          ],
          "publisher": "ExtensionC.Publisher",
          "settings": {},
          "typeHandlerVersion": "3.0",
          "autoUpgradeMinorVersion": true,
          "type": "ExtensionC"                   
        }
      }
    ]
  }
}
```

### <a name="rest-api"></a>Interfejs API REST
Poniższy przykład dodaje nowe rozszerzenie o nazwie ExtensionC do modelu zestawu skalowania. Rozszerzenie ExtensionC ma zależności od extensionA i ExtensionB, które zostały już zdefiniowane w modelu zestawu skalowania.

```
PUT on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/extensions/ExtensionC?api-version=2018-10-01`
```

```json
{ 
  "name": "ExtensionC",
  "properties": {
    "provisionAfterExtensions": [
      "ExtensionA","ExtensionB"
    ],
    "publisher": "ExtensionC.Publisher",
    "settings": {},
    "typeHandlerVersion": "3.0",
    "autoUpgradeMinorVersion": true,
    "type": "ExtensionC" 
  }                  
}
```

Jeśli rozszerzenie ExtensionC zostało zdefiniowane wcześniej w modelu zestawu skalowania, a teraz chcesz dodać jego zależności, możesz wykonać polecenie , aby edytować już wdrożone `PATCH` właściwości rozszerzenia.

```
PATCH on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/extensions/ExtensionC?api-version=2018-10-01`
```

```json
{ 
  "properties": {
    "provisionAfterExtensions": [
      "ExtensionA","ExtensionB"
    ]
  }                  
}
```
Zmiany istniejących wystąpień zestawu skalowania są stosowane przy następnym [uaktualnieniu.](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model)

### <a name="azure-powershell"></a>Azure PowerShell
Użyj polecenia cmdlet [Add-AzVmssExtension,](/powershell/module/az.compute/add-azvmssextension) aby dodać rozszerzenie Kondycja aplikacji do definicji modelu zestawu skalowania. Sekwencjonowanie rozszerzeń wymaga użycia polecenia Az PowerShell 1.2.0 lub jego więcej.

W poniższym przykładzie dodano rozszerzenie [Kondycja aplikacji](virtual-machine-scale-sets-health-extension.md) do zestawu skalowania `extensionProfile` opartego na systemie Windows. Rozszerzenie kondycji aplikacji zostanie aprowowane po aprowizowania rozszerzenia [niestandardowego skryptu](../virtual-machines/extensions/custom-script-windows.md), które zostało już zdefiniowane w zestawie skalowania.

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
  -ProvisionAfterExtension "CustomScriptExtension" `
  -AutoUpgradeMinorVersion $True

# Update the scale set
Update-AzVmss -ResourceGroupName $vmScaleSetResourceGroup `
  -Name $vmScaleSetName `
  -VirtualMachineScaleSet $vmScaleSet
```

### <a name="azure-cli-20"></a>Interfejs wiersza polecenia platformy Azure 2.0
Użyj [rozszerzenia az vmss extension set,](/cli/azure/vmss/extension#az_vmss_extension_set) aby dodać rozszerzenie kondycji aplikacji do definicji modelu zestawu skalowania. Sekwencjonowanie rozszerzeń wymaga użycia interfejsu wiersza polecenia platformy Azure w wersji 2.0.55 lub wersji powyżej.

Poniższy przykład dodaje rozszerzenie [Kondycja aplikacji](virtual-machine-scale-sets-health-extension.md) do modelu zestawu skalowania zestawu skalowania opartego na systemie Windows. Rozszerzenie kondycji aplikacji zostanie aprowowane po aprowizowania rozszerzenia [niestandardowego skryptu](../virtual-machines/extensions/custom-script-windows.md), które zostało już zdefiniowane w zestawie skalowania.

```azurecli-interactive
az vmss extension set \
  --name ApplicationHealthWindows \
  --publisher Microsoft.ManagedServices \
  --version 1.0 \
  --resource-group <myVMScaleSetResourceGroup> \
  --vmss-name <myVMScaleSet> \
  --provision-after-extensions CustomScriptExtension \
  --settings ./extension.json
```


## <a name="troubleshoot"></a>Rozwiązywanie problemów

### <a name="not-able-to-add-extension-with-dependencies"></a>Nie możesz dodać rozszerzenia z zależnościami?
1. Upewnij się, że rozszerzenia określone w provisionAfterExtensions są zdefiniowane w modelu zestawu skalowania.
2. Upewnij się, że nie są wprowadzane żadne zależności cykliczne. Na przykład następująca sekwencja nie jest dozwolona: ExtensionA -> ExtensionB -> ExtensionC -> ExtensionA
3. Upewnij się, że wszystkie rozszerzenia, od których są zależne, mają właściwość "settings" w obszarze rozszerzenia "properties". Jeśli na przykład rozszerzenie ExtentionB musi zostać aprowowane po rozszerzeniu ExtensionA, rozszerzenie ExtensionA musi mieć pole "settings" w obszarze "właściwości rozszerzenia A". Możesz określić pustą właściwość "settings", jeśli rozszerzenie nie wymaga wymaganych ustawień.

### <a name="not-able-to-remove-extensions"></a>Nie możesz usunąć rozszerzeń?
Upewnij się, że usuwane rozszerzenia nie są wymienione w obszarze provisionAfterExtensions dla innych rozszerzeń.

## <a name="next-steps"></a>Następne kroki
Dowiedz się, jak [wdrożyć aplikację w zestawach](virtual-machine-scale-sets-deploy-app.md) skalowania maszyn wirtualnych.
