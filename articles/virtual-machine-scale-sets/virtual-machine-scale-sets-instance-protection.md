---
title: Ochrona wystąpienia dla wystąpień zestawu skalowania maszyn wirtualnych platformy Azure
description: Dowiedz się, jak chronić wystąpienia zestawu skalowania maszyn wirtualnych platformy Azure na podstawie operacji skalowania i skalowania.
author: avirishuv
ms.author: avverma
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: availability
ms.date: 02/26/2020
ms.reviewer: jushiman
ms.custom: avverma
ms.openlocfilehash: 8c4944da8ffcaa75e6448483918a29809c32830b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92746790"
---
# <a name="instance-protection-for-azure-virtual-machine-scale-set-instances"></a>Ochrona wystąpienia dla wystąpień zestawu skalowania maszyn wirtualnych platformy Azure

Zestawy skalowania maszyn wirtualnych platformy Azure umożliwiają lepszą elastyczność obciążeń dzięki funkcji [automatycznego skalowania](virtual-machine-scale-sets-autoscale-overview.md), dzięki czemu można skonfigurować czas skalowania infrastruktury i skalowania w poziomie. Zestawy skalowania umożliwiają również centralne zarządzanie, Konfigurowanie i aktualizowanie dużej liczby maszyn wirtualnych za pomocą różnych ustawień [zasad uaktualniania](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model) . Można skonfigurować aktualizację modelu zestawu skalowania, a nowa konfiguracja jest automatycznie stosowana do każdego wystąpienia zestawu skalowania, jeśli zasady uaktualniania są ustawiane na automatyczne lub stopniowe.

Gdy aplikacja przetwarza ruch, mogą wystąpić sytuacje, w których określone wystąpienia mają być traktowane inaczej niż pozostałe wystąpienia zestawu skalowania. Na przykład niektóre wystąpienia w zestawie skalowania mogą wykonywać długotrwałe operacje i nie chcesz, aby te wystąpienia były skalowane do momentu zakończenia operacji. Możesz również mieć wyspecjalizowane kilka wystąpień w zestawie skalowania do wykonywania dodatkowych lub różnych zadań niż inne elementy członkowskie zestawu skalowania. Te maszyny wirtualne "specjalne" są wymagane, aby nie były modyfikowane przy użyciu innych wystąpień w zestawie skalowania. Ochrona wystąpienia udostępnia dodatkowe funkcje kontroli umożliwiające te i inne scenariusze dla aplikacji.

W tym artykule opisano, jak można zastosować różne możliwości ochrony wystąpień z wystąpieniami zestawów skalowania i korzystać z nich.

## <a name="types-of-instance-protection"></a>Typy ochrony wystąpienia
Zestawy skalowania oferują dwa typy możliwości ochrony wystąpienia:

-   **Ochrona przed skalowaniem w poziomie**
    - Włączono za pomocą właściwości **protectFromScaleIn** w wystąpieniu zestawu skalowania
    - Chroni wystąpienie na podstawie zainicjowanej skalowania automatycznego skalowania w poziomie
    - Operacje wystąpienia inicjowane przez użytkownika (w tym usuwanie wystąpienia) **nie są blokowane**
    - Operacje zainicjowane w zestawie skalowania (uaktualnianie, ponowne tworzenie obrazu, cofanie alokacji itp.) **nie są blokowane**

-   **Ochrona przed akcjami zestawu skalowania**
    - Włączono za pomocą właściwości **protectFromScaleSetActions** w wystąpieniu zestawu skalowania
    - Chroni wystąpienie na podstawie zainicjowanej skalowania automatycznego skalowania w poziomie
    - Chroni wystąpienie od operacji zainicjowanych w zestawie skalowania (takich jak uaktualnianie, odtwarzanie obrazu, cofanie alokacji itp.)
    - Operacje wystąpienia inicjowane przez użytkownika (w tym usuwanie wystąpienia) **nie są blokowane**
    - Usuwanie pełnego zestawu skalowania nie jest **blokowane**

## <a name="protect-from-scale-in"></a>Ochrona przed skalowaniem w poziomie
Ochronę wystąpienia można zastosować do wystąpień zestawów skalowania po utworzeniu wystąpień. Ochrona jest stosowana i modyfikowana tylko w [modelu wystąpienia](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-vm-model-view) , a nie w [modelu zestawu skalowania](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-model).

Istnieje wiele sposobów zastosowania ochrony skalowania w Twoich wystąpieniach zestawu skalowania zgodnie z opisem w poniższych przykładach.

### <a name="azure-portal"></a>Azure Portal

Możesz zastosować ochronę skalowania w Azure Portal do wystąpienia w zestawie skalowania. Nie można jednocześnie dopasować więcej niż jednego wystąpienia. Powtórz kroki dla każdego wystąpienia, które chcesz chronić.
 
1. Przejdź do istniejącego zestawu skalowania maszyn wirtualnych.
1. Wybierz pozycję **wystąpienia** z menu po lewej stronie, w obszarze **Ustawienia**.
1. Wybierz nazwę wystąpienia, które chcesz chronić.
1. Wybierz kartę **Zasady ochrony** .
1. W bloku **Zasady ochrony** wybierz opcję **Chroń przed skalowaniem** .
1. Wybierz pozycję **Zapisz**. 

### <a name="rest-api"></a>Interfejs API REST

W poniższym przykładzie zastosowano ochronę skalowania do wystąpienia w zestawie skalowania.

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
>Ochrona wystąpienia jest obsługiwana tylko w przypadku interfejsu API w wersji 2019-03-01 lub nowszej

### <a name="azure-powershell"></a>Azure PowerShell

Użyj polecenia cmdlet [Update-AzVmssVM](/powershell/module/az.compute/update-azvmssvm) , aby zastosować ochronę skalowania do wystąpienia zestawu skalowania.

Poniższy przykład stosuje ochronę skalowania do wystąpienia w zestawie skalowania z IDENTYFIKATORem wystąpienia 0.

```azurepowershell-interactive
Update-AzVmssVM `
  -ResourceGroupName "myResourceGroup" `
  -VMScaleSetName "myVMScaleSet" `
  -InstanceId 0 `
  -ProtectFromScaleIn $true
```

### <a name="azure-cli-20"></a>Interfejs wiersza polecenia platformy Azure 2.0

Użyj [AZ VMSS Update](/cli/azure/vmss#az-vmss-update) , aby zastosować ochronę skalowania do wystąpienia zestawu skalowania.

Poniższy przykład stosuje ochronę skalowania do wystąpienia w zestawie skalowania z IDENTYFIKATORem wystąpienia 0.

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --instance-id 0 \
  --protect-from-scale-in true
```

## <a name="protect-from-scale-set-actions"></a>Ochrona przed akcjami zestawu skalowania
Ochronę wystąpienia można zastosować do wystąpień zestawów skalowania po utworzeniu wystąpień. Ochrona jest stosowana i modyfikowana tylko w [modelu wystąpienia](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-vm-model-view) , a nie w [modelu zestawu skalowania](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-model).

Ochrona wystąpienia z poziomu akcji zestawu skalowania chroni również wystąpienie w ramach skalowania automatycznego skalowania w poziomie.

Istnieje wiele sposobów stosowania ochrony akcji zestawu skalowania w wystąpieniach zestawu skalowania, jak opisano w poniższych przykładach.

### <a name="azure-portal"></a>Azure Portal

Możesz zastosować ochronę przed akcjami zestawu skalowania za pośrednictwem Azure Portal do wystąpienia w zestawie skalowania. Nie można jednocześnie dopasować więcej niż jednego wystąpienia. Powtórz kroki dla każdego wystąpienia, które chcesz chronić.
 
1. Przejdź do istniejącego zestawu skalowania maszyn wirtualnych.
1. Wybierz pozycję **wystąpienia** z menu po lewej stronie, w obszarze **Ustawienia**.
1. Wybierz nazwę wystąpienia, które chcesz chronić.
1. Wybierz kartę **Zasady ochrony** .
1. W bloku **Zasady ochrony** wybierz opcję **Chroń przed zestawem skalowania** .
1. Wybierz pozycję **Zapisz**. 

### <a name="rest-api"></a>Interfejs API REST

Poniższy przykład stosuje ochronę przed akcjami zestawu skalowania do wystąpienia w zestawie skalowania.

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
>Ochrona wystąpienia jest obsługiwana tylko w przypadku interfejsu API w wersji 2019-03-01 lub nowszej.</br>
Ochrona wystąpienia z poziomu akcji zestawu skalowania chroni również wystąpienie w ramach skalowania automatycznego skalowania w poziomie. Nie można określić "protectFromScaleIn": false podczas ustawiania "protectFromScaleSetActions": true

### <a name="azure-powershell"></a>Azure PowerShell

Użyj polecenia cmdlet [Update-AzVmssVM](/powershell/module/az.compute/update-azvmssvm) , aby zastosować ochronę z poziomu akcji zestawu skalowania do wystąpienia zestawu skalowania.

Poniższy przykład stosuje ochronę przed akcjami zestawu skalowania do wystąpienia w zestawie skalowania z IDENTYFIKATORem wystąpienia 0.

```azurepowershell-interactive
Update-AzVmssVM `
  -ResourceGroupName "myResourceGroup" `
  -VMScaleSetName "myVMScaleSet" `
  -InstanceId 0 `
  -ProtectFromScaleIn $true `
  -ProtectFromScaleSetAction $true
```

### <a name="azure-cli-20"></a>Interfejs wiersza polecenia platformy Azure 2.0

Użyj [AZ VMSS Update](/cli/azure/vmss#az-vmss-update) , aby zastosować ochronę przed akcjami zestawu skalowania do wystąpienia zestawu skalowania.

Poniższy przykład stosuje ochronę przed akcjami zestawu skalowania do wystąpienia w zestawie skalowania z IDENTYFIKATORem wystąpienia 0.

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --instance-id 0 \
  --protect-from-scale-in true \
  --protect-from-scale-set-actions true
```

## <a name="troubleshoot"></a>Rozwiązywanie problemów
### <a name="no-protectionpolicy-on-scale-set-model"></a>Brak protectionPolicy na modelu zestawu skalowania
Ochrona wystąpienia ma zastosowanie tylko w wystąpieniach zestawu skalowania, a nie w modelu zestawu skalowania.

### <a name="no-protectionpolicy-on-scale-set-instance-model"></a>Brak protectionPolicy z modelem wystąpienia zestawu skalowania
Domyślnie zasady ochrony nie są stosowane do wystąpienia podczas jego tworzenia.

Po utworzeniu wystąpień można zastosować ochronę wystąpienia do wystąpień zestawów skalowania.

### <a name="not-able-to-apply-instance-protection"></a>Nie można zastosować ochrony wystąpienia
Ochrona wystąpienia jest obsługiwana tylko w przypadku interfejsu API w wersji 2019-03-01 lub nowszej. Sprawdź używaną wersję interfejsu API i Aktualizuj ją zgodnie z wymaganiami. Może być również konieczne zaktualizowanie programu PowerShell lub interfejsu wiersza polecenia do najnowszej wersji.

## <a name="next-steps"></a>Następne kroki
Dowiedz się, jak [wdrożyć aplikację](virtual-machine-scale-sets-deploy-app.md) w zestawach skalowania maszyn wirtualnych.
