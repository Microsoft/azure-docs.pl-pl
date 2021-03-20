---
title: Zarządzanie Virtual Machine Scale Sets przy użyciu Azure PowerShell
description: Typowe polecenia cmdlet Azure PowerShell umożliwiające zarządzanie Virtual Machine Scale Sets, takie jak uruchamianie i zatrzymywanie wystąpienia lub zmienianie pojemności zestawu skalowania.
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: management
ms.date: 05/29/2018
ms.reviewer: mimckitt
ms.custom: mimckitt, devx-track-azurepowershell
ms.openlocfilehash: eee4dd7fae872f6b3ddd01f60aba732edc170766
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91570573"
---
# <a name="manage-a-virtual-machine-scale-set-with-azure-powershell"></a>Zarządzanie zestawem skalowania maszyn wirtualnych za pomocą Azure PowerShell

W całym cyklu życia zestawu skalowania maszyn wirtualnych konieczne może być uruchomienie jednego lub większej liczby zadań zarządzania. Ponadto może pojawić się potrzeba tworzenia skryptów automatyzujących różne zadania cyklu życia. W tym artykule opisano niektóre typowe polecenia cmdlet Azure PowerShell, które umożliwiają wykonywanie tych zadań.

Jeśli musisz utworzyć zestaw skalowania maszyn wirtualnych, możesz [utworzyć zestaw skalowania z Azure PowerShell](quick-create-powershell.md).

[!INCLUDE [updated-for-az.md](../../includes/updated-for-az.md)]

## <a name="view-information-about-a-scale-set"></a>Wyświetlanie informacji o zestawie skalowania
Aby wyświetlić ogólne informacje o zestawie skalowania, użyj polecenie [Get-AzVmss](/powershell/module/az.compute/get-azvmss). Poniższy przykład pobiera informacje o zestawie skalowania o nazwie *myScaleSet* w grupie *zasobów zasobu* . Wprowadź własne nazwy w następujący sposób:

```powershell
Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
```


## <a name="view-vms-in-a-scale-set"></a>Wyświetlanie maszyn wirtualnych w zestawie skalowania
Aby wyświetlić listę wystąpień maszyn wirtualnych w zestawie skalowania, użyj polecenie [Get-AzVmssVM](/powershell/module/az.compute/get-azvmssvm). Poniższy przykład wyświetla listę wszystkich wystąpień maszyn wirtualnych w zestawie skalowania o nazwie *myScaleSet* i w *grupie zasobów zasobu* . Podaj własne wartości dla tych nazw:

```powershell
Get-AzVmssVM -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
```

Aby wyświetlić dodatkowe informacje dotyczące konkretnego wystąpienia maszyny wirtualnej, Dodaj `-InstanceId` parametr do [Get-AzVmssVM](/powershell/module/az.compute/get-azvmssvm) i Określ wystąpienie do wyświetlenia. Poniższy przykład wyświetla informacje o wystąpieniu maszyny wirtualnej *0* w zestawie skalowania o nazwie *myScaleSet* i *grupie zasobów zasobu* . Wprowadź własne nazwy w następujący sposób:

```powershell
Get-AzVmssVM -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "0"
```

Można również uzyskać szczegółowe informacje *instanceView* dla wszystkich wystąpień w jednym wywołaniu interfejsu API, co może pomóc w uniknięciu ograniczenia przepustowości interfejsu API w przypadku dużych instalacji.

```powershell
Get-AzVmssVM -InstanceView -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
```

```rest
GET "https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSSName>/virtualMachines?api-version=2019-03-01&%24expand=instanceView"
```

## <a name="change-the-capacity-of-a-scale-set"></a>Zmienianie pojemności zestawu skalowania
Powyższe polecenia pokazały informacje o zestawie skalowania i wystąpieniach maszyn wirtualnych. Aby zwiększyć lub zmniejszyć liczbę wystąpień w zestawie skalowania, można zmienić pojemność. Zestaw skalowania automatycznie tworzy lub usuwa wymaganą liczbę maszyn wirtualnych, a następnie konfiguruje maszyny wirtualne pod kątem odbierania ruchu aplikacji.

Najpierw utwórz obiekt zestawu skalowania za pomocą polecenia [Get-AzVmss](/powershell/module/az.compute/get-azvmss), a następnie podaj nową wartość parametru `sku.capacity`. Aby zastosować zmiany pojemności, użyj polecenia [Update-AzVmss](/powershell/module/az.compute/update-azvmss). Poniższy przykład aktualizuje *myScaleSet* w *grupie zasobów zasobu zasobów do* pojemności *5* wystąpień. Podaj własne wartości w następujący sposób:

```powershell
# Get current scale set
$vmss = Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"

# Set and update the capacity of your scale set
$vmss.sku.capacity = 5
Update-AzVmss -ResourceGroupName "myResourceGroup" -Name "myScaleSet" -VirtualMachineScaleSet $vmss
```

Aktualizacja pojemności zestawu skalowania trwa kilka minut. Jeśli zmniejszy się pojemność zestawu skalowania, maszyny wirtualne z najwyższymi identyfikatorami wystąpienia zostaną najpierw usunięte.


## <a name="stop-and-start-vms-in-a-scale-set"></a>Zatrzymywanie i uruchamianie maszyn wirtualnych w zestawie skalowania
Aby zatrzymać co najmniej jedną maszynę wirtualną w zestawie skalowania, użyj polecenia [Stop-AzVmss](/powershell/module/az.compute/stop-azvmss). Parametr `-InstanceId` umożliwia wskazanie maszyn wirtualnych, które mają zostać zatrzymane. Jeśli nie podasz identyfikatora wystąpienia, zostaną zatrzymane wszystkie maszyny wirtualne w zestawie skalowania. Aby zatrzymać wiele maszyn wirtualnych, należy oddzielić każdy identyfikator wystąpienia przecinkami.

Poniższy przykład powoduje zatrzymanie wystąpienia *0* w zestawie skalowania o nazwie *myScaleSet* i *grupie zasobów zasobu* . Podaj własne wartości w następujący sposób:

```powershell
Stop-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "0"
```

Domyślnie następuje cofnięcie przydziału zatrzymanych maszyn wirtualnych, co sprawia, że opłaty za operacje obliczeniowe nie są naliczane. Jeśli zatrzymana maszyna wirtualna ma zachować stan Aprowizowano, dodaj parametr `-StayProvisioned` do poprzedniego polecenia. W przypadku zatrzymanych maszyn wirtualnych ze stanem Aprowizowano są naliczane opłaty za operacje obliczeniowe.


### <a name="start-vms-in-a-scale-set"></a>Uruchamianie maszyn wirtualnych w zestawie skalowania
Aby uruchomić co najmniej jedną maszynę wirtualną w zestawie skalowania, użyj polecenia [Start-AzVmss](/powershell/module/az.compute/start-azvmss). Parametr `-InstanceId` umożliwia wskazanie maszyn wirtualnych, które mają zostać uruchomione. Jeśli nie podasz identyfikatora wystąpienia, zostaną uruchomione wszystkie maszyny wirtualne w zestawie skalowania. Aby uruchomić wiele maszyn wirtualnych, należy oddzielić każdy identyfikator wystąpienia przecinkami.

W poniższym przykładzie zostanie uruchomione wystąpienie *0* w zestawie skalowania o nazwie *myScaleSet* i *grupie zasobów zasobu* . Podaj własne wartości w następujący sposób:

```powershell
Start-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "0"
```


## <a name="restart-vms-in-a-scale-set"></a>Ponowne uruchamianie maszyn wirtualnych w zestawie skalowania
Aby ponownie uruchomić co najmniej jedną maszynę wirtualną w zestawie skalowania, użyj opcji [restart-AzVmss](/powershell/module/az.compute/restart-azvmss). Parametr `-InstanceId` umożliwia wskazanie maszyn wirtualnych, które mają zostać uruchomione ponownie. Jeśli nie podasz identyfikatora wystąpienia, wszystkie maszyny wirtualne w zestawie skalowania zostaną uruchomione ponownie. Aby ponownie uruchomić wiele maszyn wirtualnych, każdy identyfikator wystąpienia należy oddzielić przecinkami.

Poniższy przykład powoduje ponowne uruchomienie wystąpienia *0* w zestawie skalowania o nazwie *myScaleSet* i *grupie zasobów zasobu* . Podaj własne wartości w następujący sposób:

```powershell
Restart-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "0"
```


## <a name="remove-vms-from-a-scale-set"></a>Usuwanie maszyn wirtualnych z zestawu skalowania
Aby usunąć co najmniej jedną maszynę wirtualną w zestawie skalowania, użyj polecenie [Remove-AzVmss](/powershell/module/az.compute/remove-azvmss). `-InstanceId`Parametr umożliwia określenie co najmniej jednej maszyny wirtualnej do usunięcia. Jeśli nie określisz identyfikatora wystąpienia, wszystkie maszyny wirtualne w zestawie skalowania zostaną usunięte. Aby usunąć wiele maszyn wirtualnych, każdy identyfikator wystąpienia należy oddzielić przecinkami.

Poniższy przykład usuwa wystąpienie *0* z zestawu skalowania o nazwie *myScaleSet* i grupę *zasobów zasobu* . Podaj własne wartości w następujący sposób:

```powershell
Remove-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "0"
```


## <a name="next-steps"></a>Następne kroki
Inne typowe zadania związane z zestawami skalowania obejmują sposób [wdrażania aplikacji](virtual-machine-scale-sets-deploy-app.md)i [uaktualniania wystąpień maszyn wirtualnych](virtual-machine-scale-sets-upgrade-scale-set.md). Aby [skonfigurować reguły automatycznego skalowania](virtual-machine-scale-sets-autoscale-overview.md), można również użyć Azure PowerShell.
