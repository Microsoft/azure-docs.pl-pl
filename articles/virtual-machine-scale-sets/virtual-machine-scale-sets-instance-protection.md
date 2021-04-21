---
title: Ochrona wystąpień dla wystąpień zestawu skalowania maszyn wirtualnych platformy Azure
description: Dowiedz się, jak chronić wystąpienia zestawu skalowania maszyn wirtualnych platformy Azure przed operacjami skalowania w systemie i zestawu skalowania.
author: avirishuv
ms.author: avverma
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: instance-protection
ms.date: 02/26/2020
ms.reviewer: jushiman
ms.custom: avverma
ms.openlocfilehash: 292abce3361c000eeeef2c399d5ffa2d2c4852e1
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107762861"
---
# <a name="instance-protection-for-azure-virtual-machine-scale-set-instances"></a>Ochrona wystąpień dla wystąpień zestawu skalowania maszyn wirtualnych platformy Azure

Zestawy skalowania maszyn wirtualnych platformy Azure [](virtual-machine-scale-sets-autoscale-overview.md)umożliwiają większą elastyczność obciążeń dzięki funkcji automatycznego skalowania, dzięki czemu można skonfigurować skalowanie infrastruktury w celu zwiększenia skali w zewnątrz i skali w zewnątrz. Zestawy skalowania umożliwiają również centralne konfigurowanie i aktualizowanie dużej liczby maszyn wirtualnych oraz zarządzanie nimi za pomocą różnych [ustawień zasad uaktualniania.](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model) Możesz skonfigurować aktualizację w modelu zestawu skalowania, a nowa konfiguracja zostanie automatycznie zastosowana do każdego wystąpienia zestawu skalowania, jeśli ustawiono zasady uaktualniania automatyczne lub stopniowe.

Gdy aplikacja przetwarza ruch, mogą być sytuacje, w których określone wystąpienia mają być traktowane inaczej niż pozostałe wystąpienia zestawu skalowania. Na przykład niektóre wystąpienia w zestawie skalowania mogą wykonywać długotrwałe operacje i nie chcesz, aby te wystąpienia były skalowane do momentu ukończenia operacji. Być może masz również wyspecjalizowanych kilka wystąpień w zestawie skalowania, aby wykonywać dodatkowe lub inne zadania niż inne elementy członkowskie zestawu skalowania. Te "specjalne" maszyny wirtualne nie powinny być modyfikowane przy użyciu innych wystąpień w zestawie skalowania. Ochrona wystąpień zapewnia dodatkowe kontrolki umożliwiające włączenie tych i innych scenariuszy dla aplikacji.

W tym artykule opisano sposób stosowania różnych możliwości ochrony wystąpień i korzystania z nich za pomocą wystąpień zestawu skalowania.

## <a name="types-of-instance-protection"></a>Typy ochrony wystąpień
Zestawy skalowania zapewniają dwa typy możliwości ochrony wystąpień:

-   **Ochrona przed skalą w skali w**
    - Włączane **za pomocą właściwości protectFromScaleIn** w wystąpieniu zestawu skalowania
    - Chroni wystąpienie przed skalowaniem inicjowane przez autoskalowanie
    - Operacje wystąpienia inicjowane przez użytkownika (w tym usuwanie wystąpienia) **nie są blokowane**
    - Operacje inicjowane w zestawie skalowania (uaktualnianie, odtwarzanie z obrazu, cofanie alokacji itp.) **nie są blokowane**

-   **Ochrona przed akcjami zestawu skalowania**
    - Włączane **za pomocą właściwości protectFromScaleSetActions** w wystąpieniu zestawu skalowania
    - Chroni wystąpienie przed skalowaniem inicjowaym przez autoskalowanie
    - Chroni wystąpienie przed operacjami zainicjowane w zestawie skalowania (takimi jak uaktualnianie, odtwarzanie z obrazu, cofanie alokacji itp.)
    - Operacje wystąpienia inicjowane przez użytkownika (w tym usuwanie wystąpienia) **nie są blokowane**
    - Usuwanie pełnego zestawu skalowania **nie jest blokowane**

## <a name="protect-from-scale-in"></a>Ochrona przed skalowaniem w
Ochronę wystąpień można zastosować do wystąpień zestawu skalowania po ich utworzeniu. Ochrona jest stosowana i modyfikowana tylko w [modelu wystąpienia,](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-vm-model-view) a nie w modelu [zestawu skalowania](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-model).

Istnieje wiele sposobów stosowania ochrony skalowania w skali w wystąpieniach zestawu skalowania, jak o ile o tym omówienie o tym w poniższych przykładach.

### <a name="azure-portal"></a>Azure Portal

Ochronę skalowania w skali można zastosować za pośrednictwem Azure Portal wystąpienia w zestawie skalowania. Jednocześnie nie można dostosować więcej niż jednego wystąpienia. Powtórz kroki dla każdego wystąpienia, które chcesz chronić.
 
1. Przejdź do istniejącego zestawu skalowania maszyn wirtualnych.
1. Wybierz **pozycję Wystąpienia** z menu po lewej stronie w obszarze **Ustawienia**.
1. Wybierz nazwę wystąpienia, które chcesz chronić.
1. Wybierz **kartę Zasady** ochrony.
1. W bloku **Zasady ochrony** wybierz opcję Chroń **przed skalowaniem w** skali.
1. Wybierz pozycję **Zapisz**. 

### <a name="rest-api"></a>Interfejs API REST

Poniższy przykład dotyczy ochrony skalowanej w skali w przypadku wystąpienia w zestawie skalowania.

```
PUT on `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/virtualMachines/{instance-id}?api-version=2019-03-01`
```

```json
{
  "properties": {
    "protectionPolicy": {
      "protectFromScaleIn": true
    }
  }        
}

```

> [!NOTE]
>Ochrona wystąpień jest obsługiwana tylko w interfejsie API w wersji 2019-03-01 lub nowszą

### <a name="azure-powershell"></a>Azure PowerShell

Użyj polecenia cmdlet [Update-AzVmssVM,](/powershell/module/az.compute/update-azvmssvm) aby zastosować ochronę skalowania w skali w skali w wystąpieniu zestawu skalowania.

W poniższym przykładzie ochrona skalowana w skali w skali jest stosowana do wystąpienia w zestawie skalowania o identyfikatorze wystąpienia 0.

```azurepowershell-interactive
Update-AzVmssVM `
  -ResourceGroupName "myResourceGroup" `
  -VMScaleSetName "myVMScaleSet" `
  -InstanceId 0 `
  -ProtectFromScaleIn $true
```

### <a name="azure-cli-20"></a>Interfejs wiersza polecenia platformy Azure 2.0

Użyj [az vmss update,](/cli/azure/vmss#az_vmss_update) aby zastosować ochronę skalowania w skali w wystąpieniu zestawu skalowania.

W poniższym przykładzie ochrona skalowana w skali w skali jest stosowana do wystąpienia w zestawie skalowania o identyfikatorze wystąpienia 0.

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --instance-id 0 \
  --protect-from-scale-in true
```

## <a name="protect-from-scale-set-actions"></a>Ochrona przed akcjami zestawu skalowania
Ochronę wystąpień można zastosować do wystąpień zestawu skalowania po ich utworzeniu. Ochrona jest stosowana i modyfikowana tylko w [modelu wystąpienia,](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-vm-model-view) a nie w modelu [zestawu skalowania](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-model).

Ochrona wystąpienia przed akcjami zestawu skalowania chroni również wystąpienie przed skalowaniem do inicjatora skalowania automatycznego.

Istnieje wiele sposobów stosowania ochrony akcji zestawu skalowania w wystąpieniach zestawu skalowania zgodnie z poniższymi przykładami.

### <a name="azure-portal"></a>Azure Portal

Ochronę można zastosować z poziomu akcji zestawu skalowania za pośrednictwem Azure Portal do wystąpienia w zestawie skalowania. Jednocześnie nie można dostosować więcej niż jednego wystąpienia. Powtórz kroki dla każdego wystąpienia, które chcesz chronić.
 
1. Przejdź do istniejącego zestawu skalowania maszyn wirtualnych.
1. Wybierz **pozycję Wystąpienia** z menu po lewej stronie w obszarze **Ustawienia**.
1. Wybierz nazwę wystąpienia, które chcesz chronić.
1. Wybierz **kartę Zasady** ochrony.
1. W bloku **Zasady ochrony** wybierz opcję Akcje Chroń **przed zestawem skalowania.**
1. Wybierz pozycję **Zapisz**. 

### <a name="rest-api"></a>Interfejs API REST

W poniższym przykładzie ochrona przed akcjami zestawu skalowania jest stosowana do wystąpienia w zestawie skalowania.

```
PUT on `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vMScaleSetName}/virtualMachines/{instance-id}?api-version=2019-03-01`
```

```json
{
  "properties": {
    "protectionPolicy": {
      "protectFromScaleIn": true,
      "protectFromScaleSetActions": true
    }
  }        
}

```

> [!NOTE]
>Ochrona wystąpień jest obsługiwana tylko w interfejsie API w wersji 2019-03-01 lub nowszej.</br>
Ochrona wystąpienia przed akcjami zestawu skalowania chroni również wystąpienie przed skalowaniem do inicjatora skalowania automatycznego. Nie można określić wartości "protectFromScaleIn": false podczas ustawiania opcji "protectFromScaleSetActions": true

### <a name="azure-powershell"></a>Azure PowerShell

Użyj polecenia cmdlet [Update-AzVmssVM,](/powershell/module/az.compute/update-azvmssvm) aby zastosować ochronę przed akcjami zestawu skalowania do wystąpienia zestawu skalowania.

W poniższym przykładzie ochrona przed akcjami zestawu skalowania jest stosowana do wystąpienia w zestawie skalowania o identyfikatorze wystąpienia 0.

```azurepowershell-interactive
Update-AzVmssVM `
  -ResourceGroupName "myResourceGroup" `
  -VMScaleSetName "myVMScaleSet" `
  -InstanceId 0 `
  -ProtectFromScaleIn $true `
  -ProtectFromScaleSetAction $true
```

### <a name="azure-cli-20"></a>Interfejs wiersza polecenia platformy Azure 2.0

Użyj [az vmss update,](/cli/azure/vmss#az_vmss_update) aby zastosować ochronę z akcji zestawu skalowania do wystąpienia zestawu skalowania.

W poniższym przykładzie ochrona przed akcjami zestawu skalowania jest stosowana do wystąpienia w zestawie skalowania o identyfikatorze wystąpienia 0.

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --instance-id 0 \
  --protect-from-scale-in true \
  --protect-from-scale-set-actions true
```

## <a name="troubleshoot"></a>Rozwiązywanie problemów
### <a name="no-protectionpolicy-on-scale-set-model"></a>Brak ochronyPolicy w modelu zestawu skalowania
Ochrona wystąpień ma zastosowanie tylko w wystąpieniach zestawu skalowania, a nie w modelu zestawu skalowania.

### <a name="no-protectionpolicy-on-scale-set-instance-model"></a>Brak ochronyPolicy w modelu wystąpienia zestawu skalowania
Domyślnie zasady ochrony nie są stosowane do wystąpienia podczas jego tworzenia.

Ochronę wystąpień można zastosować do wystąpień zestawu skalowania po utworzeniu wystąpień.

### <a name="not-able-to-apply-instance-protection"></a>Nie można zastosować ochrony wystąpienia
Ochrona wystąpień jest obsługiwana tylko w interfejsie API w wersji 2019-03-01 lub nowszą. Sprawdź używaną wersję interfejsu API i zaktualizuj go zgodnie z wymaganiami. Może być również konieczne zaktualizowanie programu PowerShell lub interfejsu wiersza polecenia do najnowszej wersji.

## <a name="next-steps"></a>Następne kroki
Dowiedz się, jak [wdrożyć aplikację w zestawach](virtual-machine-scale-sets-deploy-app.md) skalowania maszyn wirtualnych.
