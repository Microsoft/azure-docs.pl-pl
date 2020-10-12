---
title: Używanie sekwencjonowania rozszerzeń z zestawami skalowania maszyn wirtualnych platformy Azure
description: Dowiedz się, jak sekwencjonować Inicjowanie obsługi rozszerzeń podczas wdrażania wielu rozszerzeń w zestawach skalowania maszyn wirtualnych.
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: extensions
ms.date: 01/30/2019
ms.reviewer: mimckitt
ms.custom: mimckitt
ms.openlocfilehash: 3271041b9f4db100cd05588129c7d714d4478f10
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "83121035"
---
# <a name="sequence-extension-provisioning-in-virtual-machine-scale-sets"></a>Inicjowanie obsługi rozszerzenia sekwencji w zestawach skalowania maszyn wirtualnych
Rozszerzenia maszyny wirtualnej platformy Azure udostępniają takie funkcje, jak konfiguracja po wdrożeniu oraz zarządzanie, monitorowanie, zabezpieczenia i inne. Wdrożenia produkcyjne zwykle używają kombinacji wielu rozszerzeń skonfigurowanych dla wystąpień maszyn wirtualnych w celu uzyskania pożądanych wyników.

W przypadku korzystania z wielu rozszerzeń na maszynie wirtualnej ważne jest, aby upewnić się, że rozszerzenia wymagające tego samego zasobu systemu operacyjnego nie próbują uzyskać tych zasobów w tym samym czasie. Niektóre rozszerzenia są również zależne od innych rozszerzeń w celu zapewnienia wymaganych konfiguracji, takich jak ustawienia środowiska i wpisy tajne. Bez poprawnego uporządkowania i sekwencjonowania, zależne wdrożenia rozszerzeń mogą zakończyć się niepowodzeniem.

W tym artykule opisano, jak można skalować rozszerzenia, które mają być skonfigurowane dla wystąpień maszyn wirtualnych w zestawach skalowania maszyn wirtualnych.

## <a name="prerequisites"></a>Wymagania wstępne
W tym artykule założono, że znasz:
-   [Rozszerzenia](../virtual-machines/extensions/overview.md) maszyny wirtualnej platformy Azure
-   [Modyfikowanie](virtual-machine-scale-sets-upgrade-scale-set.md) zestawów skalowania maszyn wirtualnych

## <a name="when-to-use-extension-sequencing"></a>Kiedy używać sekwencjonowania rozszerzeń
Sekwencjonowanie rozszerzeń w nie jest obowiązkowe dla zestawów skalowania, a jeśli nie zostanie określony, rozszerzenia mogą być obsługiwane w wystąpieniu zestawu skalowania w dowolnej kolejności.

Na przykład jeśli model zestawu skalowania ma dwa rozszerzenia — Extension i ExtensionB — określone w modelu, mogą wystąpić następujące sekwencje aprowizacji:
-   Extension-> ExtensionB
-   ExtensionB > — rozszerzenie

Jeśli aplikacja wymaga rozszerzenia A, aby zawsze była obsługiwana przed rozszerzeniem B, należy użyć sekwencjonowania rozszerzeń zgodnie z opisem w tym artykule. W przypadku sekwencjonowania rozszerzeń zostanie wykonana tylko jedna sekwencja:
-   Extension-> ExtensionB

Wszystkie rozszerzenia, które nie są określone w zdefiniowanej sekwencji aprowizacji, mogą być obsługiwane w dowolnym momencie, w tym przed, po lub w określonej sekwencji. Sekwencjonowanie rozszerzeń określa tylko, że określone rozszerzenie będzie obsługiwane po innym określonym rozszerzeniu. Nie ma to wpływu na inicjowanie obsługi innych rozszerzeń zdefiniowanych w modelu.

Na przykład jeśli model zestawu skalowania ma trzy rozszerzenia — rozszerzenie A, rozszerzenie B i rozszerzenie C — określone w modelu, a rozszerzenie C jest ustawione jako obsługiwane po rozszerzeniu A, wówczas mogą wystąpić następujące sekwencje aprowizacji:
-   Extension-> ExtensionC-> ExtensionB
-   ExtensionB-> Extension-> ExtensionC
-   Extension-> ExtensionB-> ExtensionC

Jeśli zachodzi konieczność zapewnienia, że żadne inne rozszerzenie nie jest obsługiwane podczas wykonywania zdefiniowanej sekwencji rozszerzenia, zalecamy sekwencjonowanie wszystkich rozszerzeń w modelu zestawu skalowania. W powyższym przykładzie można ustawić, aby można było zainicjować zainicjowanie rozszerzenia B po rozszerzeniu C, aby można było wykonać tylko jedną sekwencję:
-   Extension-> ExtensionC-> ExtensionB


## <a name="how-to-use-extension-sequencing"></a>Jak używać sekwencjonowania rozszerzeń
Aby włączyć inicjowanie rozszerzenia, należy zaktualizować definicję rozszerzenia w modelu zestawu skalowania, aby uwzględnić Właściwość "provisionAfterExtensions", która akceptuje tablicę nazw rozszerzeń. Rozszerzenia wymienione w wartości tablicy właściwości muszą być w pełni zdefiniowane w modelu zestawu skalowania.

### <a name="template-deployment"></a>Wdrożenie szablonu
W poniższym przykładzie zdefiniowano szablon, w którym zestaw skalowania ma trzy rozszerzenia — Extension, ExtensionB i ExtensionC — w taki sposób, że rozszerzenia są udostępniane w kolejności:
-   Extension-> ExtensionB-> ExtensionC

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

Ponieważ właściwość "provisionAfterExtensions" akceptuje tablicę nazw rozszerzeń, powyższy przykład może być modyfikowany w taki sposób, że ExtensionC jest inicjowana po rozszerzeniu i ExtensionB, ale nie jest wymagane porządkowanie między rozszerzeniem i ExtensionB. Następujący szablon może służyć do osiągnięcia tego scenariusza:

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
Poniższy przykład dodaje nowe rozszerzenie o nazwie ExtensionC do modelu zestawu skalowania. ExtensionC ma zależności od rozszerzeń i ExtensionB, które zostały już zdefiniowane w modelu zestawu skalowania.

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

Jeśli ExtensionC został wcześniej zdefiniowany w modelu zestawu skalowania i teraz chcesz dodać jego zależności, można wykonać operację, `PATCH` Aby edytować już wdrożone właściwości rozszerzenia.

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
Zmiany w istniejących wystąpieniach zestawu skalowania są stosowane podczas następnego [uaktualnienia](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model).

### <a name="azure-powershell"></a>Azure PowerShell
Użyj polecenia cmdlet [Add-AzVmssExtension](/powershell/module/az.compute/add-azvmssextension) , aby dodać rozszerzenie kondycja aplikacji do definicji modelu zestawu skalowania. Sekwencjonowanie rozszerzeń wymaga użycia polecenia AZ PowerShell 1.2.0 lub nowszego.

Poniższy przykład dodaje [rozszerzenie kondycji aplikacji](virtual-machine-scale-sets-health-extension.md) do `extensionProfile` modelu zestawu skalowania opartego na systemie Windows. Rozszerzenie kondycji aplikacji zostanie zainicjowane po zainicjowaniu obsługi [niestandardowego rozszerzenia skryptu](../virtual-machines/extensions/custom-script-windows.md), które jest już zdefiniowane w zestawie skalowania.

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
Użyj [AZ VMSS Extension Set](/cli/azure/vmss/extension#az-vmss-extension-set) , aby dodać rozszerzenie kondycji aplikacji do definicji modelu zestawu skalowania. Sekwencjonowanie rozszerzeń wymaga użycia interfejsu wiersza polecenia platformy Azure 2.0.55 lub nowszego.

Poniższy przykład dodaje [rozszerzenie kondycji aplikacji](virtual-machine-scale-sets-health-extension.md) do modelu zestawu skalowania z zestawu skalowania na podstawie systemu Windows. Rozszerzenie kondycji aplikacji zostanie zainicjowane po zainicjowaniu obsługi [niestandardowego rozszerzenia skryptu](../virtual-machines/extensions/custom-script-windows.md), które jest już zdefiniowane w zestawie skalowania.

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

### <a name="not-able-to-add-extension-with-dependencies"></a>Nie można dodać rozszerzenia z zależnościami?
1. Upewnij się, że rozszerzenia określone w provisionAfterExtensions są zdefiniowane w modelu zestawu skalowania.
2. Upewnij się, że nie są wprowadzane żadne zależności cykliczne. Na przykład następująca sekwencja nie jest dozwolona: Extension-> ExtensionB-> ExtensionC-> Extension
3. Upewnij się, że wszystkie rozszerzenia, w których są wykonywane zależności, mają właściwość "Settings" w obszarze rozszerzenie "Properties" (właściwości). Na przykład jeśli ExtentionB musi zostać zainicjowana po rozszerzeniu, a następnie Extension musi mieć pole "Settings" w obszarze "właściwości". Możesz określić pustą właściwość "Settings" (Ustawienia), jeśli rozszerzenie nie wymaga żadnych wymaganych ustawień.

### <a name="not-able-to-remove-extensions"></a>Nie można usunąć rozszerzeń?
Upewnij się, że usunięte rozszerzenia nie są wymienione w obszarze provisionAfterExtensions dla innych rozszerzeń.

## <a name="next-steps"></a>Następne kroki
Dowiedz się, jak [wdrożyć aplikację](virtual-machine-scale-sets-deploy-app.md) w zestawach skalowania maszyn wirtualnych.
