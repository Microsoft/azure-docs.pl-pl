---
title: Modyfikowanie zestawu skalowania maszyn wirtualnych platformy Azure
description: Dowiedz się, jak modyfikować i aktualizować zestaw skalowania maszyn wirtualnych platformy Azure przy użyciu interfejsów API REST, Azure PowerShell i interfejsu wiersza polecenia platformy Azure
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: management
ms.date: 03/10/2020
ms.reviewer: mimckitt
ms.custom: mimckitt, devx-track-azurecli
ms.openlocfilehash: 7577c8510746d1140c1f8b70081f600d992ae512
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96016679"
---
# <a name="modify-a-virtual-machine-scale-set"></a>Modify a virtual machine scale set (Modyfikowanie zestawu skalowania maszyn wirtualnych)

W całym cyklu życia aplikacji może być konieczne zmodyfikowanie lub zaktualizowanie zestawu skalowania maszyn wirtualnych. Te aktualizacje mogą obejmować sposób aktualizowania konfiguracji zestawu skalowania lub zmiany konfiguracji aplikacji. W tym artykule opisano sposób modyfikowania istniejącego zestawu skalowania przy użyciu interfejsów API REST, Azure PowerShell lub interfejsu wiersza polecenia platformy Azure.

## <a name="fundamental-concepts"></a>Podstawowe pojęcia

### <a name="the-scale-set-model"></a>Model zestawu skalowania
Zestaw skalowania ma "model zestawu skalowania", który przechwytuje *żądany* stan zestawu skalowania jako całości. Aby zbadać model zestawu skalowania, można użyć 

- Interfejs API REST z funkcją [COMPUTE/virtualmachinescalesets/Get](/rest/api/compute/virtualmachinescalesets/get) w następujący sposób:

    ```rest
    GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet?api-version={apiVersion}
    ```

- Azure PowerShell z opcją [Get-AzVmss](/powershell/module/az.compute/get-azvmss):

    ```powershell
    Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
    ```

- Interfejs wiersza polecenia platformy Azure z programem [AZ VMSS show](/cli/azure/vmss):

    ```azurecli
    az vmss show --resource-group myResourceGroup --name myScaleSet
    ```

- Można również użyć [resources.Azure.com](https://resources.azure.com) lub [zestawów SDK platformy Azure](https://azure.microsoft.com/downloads/)dla określonych języków.

Dokładna prezentacja danych wyjściowych zależy od opcji podanych w poleceniu. Poniższy przykład przedstawia skondensowane dane wyjściowe z interfejsu wiersza polecenia platformy Azure:

```azurecli
az vmss show --resource-group myResourceGroup --name myScaleSet
{
  "location": "westus",
  "overprovision": true,
  "plan": null,
  "singlePlacementGroup": true,
  "sku": {
    "additionalProperties": {},
    "capacity": 1,
    "name": "Standard_D2_v2",
    "tier": "Standard"
  },
}
```

Te właściwości mają zastosowanie do zestawu skalowania jako całości.


### <a name="the-scale-set-instance-view"></a>Widok wystąpienia zestawu skalowania
Zestaw skalowania ma również widok wystąpienia zestawu skalowania, który przechwytuje bieżący stan *środowiska uruchomieniowego* zestawu skalowania jako całości. Aby zbadać widok wystąpienia zestawu skalowania, można użyć:

- Interfejs API REST z funkcją [COMPUTE/virtualmachinescalesets/getinstanceview](/rest/api/compute/virtualmachinescalesets/getinstanceview) w następujący sposób:

    ```rest
    GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/instanceView?api-version={apiVersion}
    ```

- Azure PowerShell z opcją [Get-AzVmss](/powershell/module/az.compute/get-azvmss):

    ```powershell
    Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceView
    ```

- Interfejs wiersza polecenia platformy Azure z usługą [AZ VMSS Get-instance-View](/cli/azure/vmss):

    ```azurecli
    az vmss get-instance-view --resource-group myResourceGroup --name myScaleSet
    ```

- Można również użyć [resources.Azure.com](https://resources.azure.com) lub [zestawów Azure SDK](https://azure.microsoft.com/downloads/) specyficznych dla języka

Dokładna prezentacja danych wyjściowych zależy od opcji podanych w poleceniu. Poniższy przykład przedstawia skondensowane dane wyjściowe z interfejsu wiersza polecenia platformy Azure:

```azurecli
$ az vmss get-instance-view --resource-group myResourceGroup --name myScaleSet
{
  "statuses": [
    {
      "additionalProperties": {},
      "code": "ProvisioningState/succeeded",
      "displayStatus": "Provisioning succeeded",
      "level": "Info",
      "message": null,
      "time": "{time}"
    }
  ],
  "virtualMachine": {
    "additionalProperties": {},
    "statusesSummary": [
      {
        "additionalProperties": {},
        "code": "ProvisioningState/succeeded",
        "count": 1
      }
    ]
  }
}
```

Te właściwości zawierają podsumowanie bieżącego stanu środowiska uruchomieniowego maszyn wirtualnych w zestawie skalowania, takie jak stan rozszerzeń zastosowanych do zestawu skalowania.


### <a name="the-scale-set-vm-model-view"></a>Widok modelu maszyny wirtualnej zestawu skalowania
Podobnie jak w przypadku zestawu skalowania ma widok modelu, każde wystąpienie maszyny wirtualnej w zestawie skalowania ma swój własny widok modelu. Aby zbadać widok modelu dla konkretnego wystąpienia maszyny wirtualnej w zestawie skalowania, można użyć:

- Interfejs API REST z funkcją [COMPUTE/virtualmachinescalesetvms/Get](/rest/api/compute/virtualmachinescalesetvms/get) w następujący sposób:

    ```rest
    GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/virtualmachines/instanceId?api-version={apiVersion}
    ```

- Azure PowerShell z opcją [Get-AzVmssVm](/powershell/module/az.compute/get-azvmssvm):

    ```powershell
    Get-AzVmssVm -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId instanceId
    ```

- Interfejs wiersza polecenia platformy Azure z programem [AZ VMSS show](/cli/azure/vmss):

    ```azurecli
    az vmss show --resource-group myResourceGroup --name myScaleSet --instance-id instanceId
    ```

- Możesz również użyć [resources.Azure.com](https://resources.azure.com) lub [zestawów SDK platformy Azure](https://azure.microsoft.com/downloads/).

Dokładna prezentacja danych wyjściowych zależy od opcji podanych w poleceniu. Poniższy przykład przedstawia skondensowane dane wyjściowe z interfejsu wiersza polecenia platformy Azure:

```azurecli
$ az vmss show --resource-group myResourceGroup --name myScaleSet
{
  "location": "westus",
  "name": "{name}",
  "sku": {
    "name": "Standard_D2_v2",
    "tier": "Standard"
  },
}
```

Te właściwości opisują konfigurację wystąpienia maszyny wirtualnej w zestawie skalowania, a nie konfigurację zestawu skalowania jako całości. Na przykład model zestawu skalowania ma `overprovision` jako właściwość, podczas gdy model dla wystąpienia maszyny wirtualnej w zestawie skalowania nie jest. Różnica polega na tym, że nadmierne Inicjowanie obsługi administracyjnej jest właściwością zestawu skalowania jako całości, a nie poszczególnymi wystąpieniami maszyn wirtualnych w zestawie skalowania (Aby uzyskać więcej informacji o nadmiernej aprowizacji, zobacz [zagadnienia dotyczące projektowania zestawów skalowania](virtual-machine-scale-sets-design-overview.md#overprovisioning)).


### <a name="the-scale-set-vm-instance-view"></a>Widok wystąpienia maszyny wirtualnej zestawu skalowania
Podobnie jak w przypadku zestawu skalowania ma widok wystąpienia, każde wystąpienie maszyny wirtualnej w zestawie skalowania ma swój własny widok wystąpienia. Aby zbadać widok wystąpienia określonego wystąpienia maszyny wirtualnej w zestawie skalowania, można użyć:

- Interfejs API REST z funkcją [COMPUTE/virtualmachinescalesetvms/getinstanceview](/rest/api/compute/virtualmachinescalesetvms/getinstanceview) w następujący sposób:

    ```rest
    GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/virtualmachines/instanceId/instanceView?api-version={apiVersion}
    ```

- Azure PowerShell z opcją [Get-AzVmssVm](/powershell/module/az.compute/get-azvmssvm):

    ```powershell
    Get-AzVmssVm -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId instanceId -InstanceView
    ```

- Interfejs wiersza polecenia platformy Azure z usługą [AZ VMSS Get-instance-View](/cli/azure/vmss)

    ```azurecli
    az vmss get-instance-view --resource-group myResourceGroup --name myScaleSet --instance-id instanceId
    ```

- Możesz również użyć [resources.Azure.com](https://resources.azure.com) lub [zestawów Azure SDK](https://azure.microsoft.com/downloads/)

Dokładna prezentacja danych wyjściowych zależy od opcji podanych w poleceniu. Poniższy przykład przedstawia skondensowane dane wyjściowe z interfejsu wiersza polecenia platformy Azure:

```azurecli
$ az vmss get-instance-view --resource-group myResourceGroup --name myScaleSet --instance-id instanceId
{
  "additionalProperties": {
    "osName": "ubuntu",
    "osVersion": "16.04"
  },
  "disks": [
    {
      "name": "{name}",
      "statuses": [
        {
          "additionalProperties": {},
          "code": "ProvisioningState/succeeded",
          "displayStatus": "Provisioning succeeded",
          "time": "{time}"
        }
      ]
    }
  ],
  "statuses": [
    {
      "additionalProperties": {},
      "code": "ProvisioningState/succeeded",
      "displayStatus": "Provisioning succeeded",
      "time": "{time}"
    },
    {
      "additionalProperties": {},
      "code": "PowerState/running",
      "displayStatus": "VM running"
    }
  ],
  "vmAgent": {
    "statuses": [
      {
        "additionalProperties": {},
        "code": "ProvisioningState/succeeded",
        "displayStatus": "Ready",
        "level": "Info",
        "message": "Guest Agent is running",
        "time": "{time}"
      }
    ],
    "vmAgentVersion": "{version}"
  },
}
```

Te właściwości opisują bieżący stan środowiska uruchomieniowego wystąpienia maszyny wirtualnej w zestawie skalowania, który obejmuje wszystkie rozszerzenia zastosowane do zestawu skalowania.


## <a name="how-to-update-global-scale-set-properties"></a>Jak zaktualizować właściwości globalnego zestawu skalowania
Aby zaktualizować Właściwość globalnego zestawu skalowania, należy zaktualizować właściwość w modelu zestawu skalowania. Tę aktualizację można wykonać za pośrednictwem:

- Interfejs API REST z funkcją [COMPUTE/virtualmachinescalesets/metodę createorupdate](/rest/api/compute/virtualmachinescalesets/createorupdate) w następujący sposób:

    ```rest
    PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet?api-version={apiVersion}
    ```

- Aby zaktualizować właściwości globalnego zestawu skalowania, można wdrożyć szablon Menedżer zasobów z właściwościami z interfejsu API REST.

- Azure PowerShell z opcją [Update-AzVmss](/powershell/module/az.compute/update-azvmss):

    ```powershell
    Update-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -VirtualMachineScaleSet {scaleSetConfigPowershellObject}
    ```

- Interfejs wiersza polecenia platformy Azure z usługą [AZ VMSS Update](/cli/azure/vmss):
    - Aby zmodyfikować właściwość:

        ```azurecli
        az vmss update --set {propertyPath}={value}
        ```

    - Aby dodać obiekt do właściwości list w zestawie skalowania: 

        ```azurecli
        az vmss update --add {propertyPath} {JSONObjectToAdd}
        ```

    - Aby usunąć obiekt z właściwości list w zestawie skalowania: 

        ```azurecli
        az vmss update --remove {propertyPath} {indexToRemove}
        ```

    - Jeśli zestaw skalowania został wcześniej wdrożony za pomocą `az vmss create` polecenia, możesz uruchomić `az vmss create` polecenie ponownie, aby zaktualizować zestaw skalowania. Upewnij się, że wszystkie właściwości w `az vmss create` poleceniu są takie same jak poprzednio, z wyjątkiem właściwości, które chcesz zmodyfikować.

- Możesz również użyć [resources.Azure.com](https://resources.azure.com) lub [zestawów SDK platformy Azure](https://azure.microsoft.com/downloads/).

Po zaktualizowaniu modelu zestawu skalowania Nowa konfiguracja ma zastosowanie do wszystkich nowych maszyn wirtualnych utworzonych w zestawie skalowania. Jednak modele istniejących maszyn wirtualnych w zestawie skalowania muszą być nadal aktualne przy użyciu najnowszego ogólnego modelu zestawu skalowania. W modelu dla każdej maszyny wirtualnej jest to właściwość logiczna o nazwie `latestModelApplied` wskazująca, czy maszyna wirtualna jest aktualna przy użyciu najnowszego ogólnego modelu zestawu skalowania ( `true` oznacza to, że maszyna wirtualna jest aktualna przy użyciu najnowszego modelu).


## <a name="how-to-bring-vms-up-to-date-with-the-latest-scale-set-model"></a>Jak zapewnić aktualność maszyn wirtualnych przy użyciu najnowszego modelu zestawu skalowania
Zestawy skalowania mają "zasady uaktualniania", które określają sposób, w jaki maszyny wirtualne są uaktualniane przy użyciu najnowszego modelu zestawu skalowania. Trzy tryby zasad uaktualniania:

- **Automatyczny** — w tym trybie zestaw skalowania nie gwarantuje, że kolejność maszyn wirtualnych jest wyłączona. Zestaw skalowania może jednocześnie przyjmować wszystkie maszyny wirtualne. 
- **Rolling** W tym trybie, zestaw skalowania przedstawia aktualizację w partiach o opcjonalnym czasie wstrzymania między partiami.
- **Ręcznie** — w tym trybie, gdy aktualizujesz model zestawu skalowania, nic się nie dzieje z istniejącymi maszynami wirtualnymi.
 
Aby zaktualizować istniejące maszyny wirtualne, należy wykonać "ręczne uaktualnienie" każdej istniejącej maszyny wirtualnej. To ręczne uaktualnienie można wykonać przy użyciu:

- Interfejs API REST z funkcją [COMPUTE/virtualmachinescalesets/updateinstances](/rest/api/compute/virtualmachinescalesets/updateinstances) w następujący sposób:

    ```rest
    POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/manualupgrade?api-version={apiVersion}
    ```

- Azure PowerShell z opcją [Update-AzVmssInstance](/powershell/module/az.compute/update-azvmssinstance):
    
    ```powershell
    Update-AzVmssInstance -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId instanceId
    ```

- Interfejs wiersza polecenia platformy Azure z [AZ VMSS Update-Instances](/cli/azure/vmss)

    ```azurecli
    az vmss update-instances --resource-group myResourceGroup --name myScaleSet --instance-ids {instanceIds}
    ```

- Można również użyć specyficznych dla języka [zestawów Azure SDK](https://azure.microsoft.com/downloads/).

>[!NOTE]
> Klastry Service Fabric mogą korzystać tylko z trybu *automatycznego* , ale aktualizacja jest obsługiwana inaczej. Aby uzyskać więcej informacji, zobacz [Service Fabric uaktualnień aplikacji](../service-fabric/service-fabric-application-upgrade.md).

Istnieje jeden typ modyfikacji właściwości globalnego zestawu skalowania, które nie są zgodne z zasadami uaktualniania. Zmiany w systemie operacyjnym zestawu skalowania i profilu dysku danych (takie jak nazwa użytkownika i hasło administratora) można zmienić tylko w interfejsie API w wersji *2017-12-01* lub nowszej. Te zmiany stosują się tylko do maszyn wirtualnych utworzonych po zmianie modelu zestawu skalowania. Aby zapewnić aktualność istniejących maszyn wirtualnych, musisz wykonać "reobraz" każdej istniejącej maszyny wirtualnej. Ten obraz można wykonać za pomocą:

- Interfejs API REST z [obliczeniami/virtualmachinescalesets/](/rest/api/compute/virtualmachinescalesets/reimage) odtwarzaniem obrazu w następujący sposób:

    ```rest
    POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/reimage?api-version={apiVersion}
    ```

- Azure PowerShell z opcją [Set-AzVmssVm](/powershell/module/az.compute/set-azvmssvm):

    ```powershell
    Set-AzVmssVM -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId instanceId -Reimage
    ```

- Interfejs wiersza polecenia platformy Azure z programem [AZ VMSS Reimage](/cli/azure/vmss):

    ```azurecli
    az vmss reimage --resource-group myResourceGroup --name myScaleSet --instance-id instanceId
    ```

- Można również użyć specyficznych dla języka [zestawów Azure SDK](https://azure.microsoft.com/downloads/).


## <a name="properties-with-restrictions-on-modification"></a>Właściwości z ograniczeniami dotyczącymi modyfikacji

### <a name="create-time-properties"></a>Właściwości czasu utworzenia
Niektóre właściwości można ustawić tylko podczas tworzenia zestawu skalowania. Te właściwości obejmują:

- Strefy dostępności
- Wydawca odwołania do obrazu
- Oferta odwołania do obrazu
- Typ konta magazynu zarządzanego systemu operacyjnego

### <a name="properties-that-can-only-be-changed-based-on-the-current-value"></a>Właściwości, które można zmienić tylko na podstawie bieżącej wartości
Niektóre właściwości można zmienić z wyjątkami w zależności od bieżącej wartości. Te właściwości obejmują:

- **singlePlacementGroup** — Jeśli singlePlacementGroup ma wartość true, może być modyfikowany na wartość false. Jeśli jednak singlePlacementGroup ma wartość false, **nie** można jej zmodyfikować na wartość true.
- **podsieć** — podsieć zestawu skalowania może zostać zmodyfikowana, o ile oryginalna podsieć i Nowa podsieć znajdują się w tej samej sieci wirtualnej.

### <a name="properties-that-require-deallocation-to-change"></a>Właściwości, które wymagają zmiany alokacji
Niektóre właściwości można zmienić tylko na określone wartości, jeśli nie zostaną cofnięte alokacje maszyn wirtualnych w zestawie skalowania. Te właściwości obejmują:

- **Nazwa jednostki SKU**— Jeśli nowa jednostka SKU maszyny wirtualnej nie jest obsługiwana na urządzeniu, na którym znajduje się zestaw skalowania, musisz cofnąć alokację maszyn wirtualnych w zestawie skalowania Przed zmodyfikowaniem nazwy jednostki SKU. Aby uzyskać więcej informacji, zobacz [jak zmienić rozmiar maszyny wirtualnej platformy Azure](../virtual-machines/windows/resize-vm.md).


## <a name="vm-specific-updates"></a>Aktualizacje specyficzne dla maszyny wirtualnej
Niektóre modyfikacje mogą być stosowane do określonych maszyn wirtualnych zamiast właściwości globalnego zestawu skalowania. Obecnie jedyną obsługiwaną aktualizacją maszyny wirtualnej jest dołączenie/odłączanie dysków danych do/z maszyn wirtualnych w zestawie skalowania. Ta funkcja jest dostępna w wersji zapoznawczej. Aby uzyskać więcej informacji, zobacz [dokumentację w wersji zapoznawczej](https://github.com/Azure/vm-scale-sets/tree/master/preview/disk).


## <a name="scenarios"></a>Scenariusze

### <a name="application-updates"></a>Aktualizacje aplikacji
Jeśli aplikacja jest wdrażana w zestawie skalowania za pomocą rozszerzeń, aktualizacja konfiguracji rozszerzenia powoduje, że aplikacja zostanie zaktualizowana zgodnie z zasadami uaktualniania. Na przykład jeśli masz nową wersję skryptu do uruchomienia w niestandardowym rozszerzeniu skryptu, możesz zaktualizować właściwość *fileUris* , aby wskazywała na nowy skrypt. W niektórych przypadkach można wymusić aktualizację, nawet jeśli konfiguracja rozszerzenia nie zmieniła się (na przykład skrypt został zaktualizowany bez zmiany identyfikatora URI skryptu). W takich przypadkach można zmodyfikować *forceUpdateTag* , aby wymusić aktualizację. Platforma Azure nie interpretuje tej właściwości. W przypadku zmiany wartości nie ma wpływu na sposób uruchamiania rozszerzenia. Zmiana po prostu wymusza ponowne uruchomienie rozszerzenia. Aby uzyskać więcej informacji na temat *forceUpdateTag*, zobacz [dokumentację interfejsu API REST dla rozszerzeń](/rest/api/compute/virtualmachineextensions/createorupdate). Należy pamiętać, że *forceUpdateTag* można używać ze wszystkimi rozszerzeniami, a nie tylko z rozszerzeniem niestandardowego skryptu.

Jest ona również powszechna w przypadku aplikacji wdrażanych za pomocą obrazu niestandardowego. Ten scenariusz został omówiony w poniższej sekcji.

### <a name="os-updates"></a>Aktualizacje systemu operacyjnego
Jeśli używasz obrazów platformy Azure, możesz zaktualizować obraz, modyfikując *elementu imagereference* (więcej informacji, zobacz [dokumentację interfejsu API REST](/rest/api/compute/virtualmachinescalesets/createorupdate)).

>[!NOTE]
> W przypadku obrazów platformy często określono "najnowszą" wersję odwołania do obrazu. Podczas tworzenia, skalowania w poziomie i odtwarzanie obrazu maszyny wirtualne są tworzone z najnowszą dostępną wersją. **Nie oznacza to** jednak, że obraz systemu operacyjnego jest automatycznie aktualizowany w miarę upływu czasu, gdy zostaną wydane nowe wersje obrazu. Oddzielna funkcja jest obecnie dostępna w wersji zapoznawczej, która umożliwia automatyczne uaktualnianie systemu operacyjnego. Aby uzyskać więcej informacji, zobacz [dokumentację automatycznych uaktualnień systemu operacyjnego](virtual-machine-scale-sets-automatic-upgrade.md).

W przypadku używania obrazów niestandardowych można zaktualizować obraz, aktualizując identyfikator *elementu imagereference* (więcej informacji znajduje się w [dokumentacji interfejsu API REST](/rest/api/compute/virtualmachinescalesets/createorupdate)).

## <a name="examples"></a>Przykłady

### <a name="update-the-os-image-for-your-scale-set"></a>Aktualizowanie obrazu systemu operacyjnego dla zestawu skalowania
Może istnieć zestaw skalowania, który uruchamia starą wersję Ubuntu LTS 16,04. Chcesz zaktualizować do nowszej wersji Ubuntu LTS 16,04, takiej jak wersja *16.04.201801090*. Właściwość wersji odwołania do obrazu nie jest częścią listy, dlatego można bezpośrednio modyfikować te właściwości przy użyciu jednego z następujących poleceń:

- Azure PowerShell z opcją [Update-AzVmss](/powershell/module/az.compute/update-azvmss) w następujący sposób:

    ```powershell
    Update-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -ImageReferenceVersion 16.04.201801090
    ```

- Interfejs wiersza polecenia platformy Azure z usługą [AZ VMSS Update](/cli/azure/vmss):

    ```azurecli
    az vmss update --resource-group myResourceGroup --name myScaleSet --set virtualMachineProfile.storageProfile.imageReference.version=16.04.201801090
    ```

Alternatywnie możesz chcieć zmienić obraz używany przez zestaw skalowania. Na przykład możesz chcieć zaktualizować lub zmienić obraz niestandardowy używany przez zestaw skalowania. Możesz zmienić obraz używany przez zestaw skalowania, aktualizując Właściwość Identyfikator odwołania do obrazu. Właściwość identyfikatora odwołania do obrazu nie jest częścią listy, więc możesz bezpośrednio zmodyfikować tę właściwość przy użyciu jednego z następujących poleceń:

- Azure PowerShell z opcją [Update-AzVmss](/powershell/module/az.compute/update-azvmss) w następujący sposób:

    ```powershell
    Update-AzVmss `
        -ResourceGroupName "myResourceGroup" `
        -VMScaleSetName "myScaleSet" `
        -ImageReferenceId /subscriptions/{subscriptionID}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myNewImage
    ```

- Interfejs wiersza polecenia platformy Azure z usługą [AZ VMSS Update](/cli/azure/vmss):

    ```azurecli
    az vmss update \
        --resource-group myResourceGroup \
        --name myScaleSet \
        --set virtualMachineProfile.storageProfile.imageReference.id=/subscriptions/{subscriptionID}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myNewImage
    ```


### <a name="update-the-load-balancer-for-your-scale-set"></a>Aktualizowanie modułu równoważenia obciążenia dla zestawu skalowania
Załóżmy, że masz zestaw skalowania z Azure Load Balancer i chcesz zamienić Azure Load Balancer na Application Gateway platformy Azure. Moduł równoważenia obciążenia i Application Gateway właściwości zestawu skalowania są częścią listy, dzięki czemu można użyć poleceń, aby usunąć lub dodać elementy listy zamiast bezpośrednio modyfikować właściwości:

- Program Azure PowerShell:

    ```powershell
    # Get the current model of the scale set and store it in a local PowerShell object named $vmss
    $vmss=Get-AzVmss -ResourceGroupName "myResourceGroup" -Name "myScaleSet"
    
    # Create a local PowerShell object for the new desired IP configuration, which includes the reference to the application gateway
    $ipconf = New-AzVmssIPConfig "myNic" -ApplicationGatewayBackendAddressPoolsId /subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendAddressPools/{applicationGatewayBackendAddressPoolName} -SubnetId $vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].IpConfigurations[0].Subnet.Id –Name $vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].IpConfigurations[0].Name
    
    # Replace the existing IP configuration in the local PowerShell object (which contains the references to the current Azure Load Balancer) with the new IP configuration
    $vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].IpConfigurations[0] = $ipconf
    
    # Update the model of the scale set with the new configuration in the local PowerShell object
    Update-AzVmss -ResourceGroupName "myResourceGroup" -Name "myScaleSet" -virtualMachineScaleSet $vmss
    ```

- Interfejs wiersza polecenia platformy Azure:

    ```azurecli
    # Remove the load balancer backend pool from the scale set model
    az vmss update --resource-group myResourceGroup --name myScaleSet --remove virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerBackendAddressPools 0
    
    # Remove the load balancer backend pool from the scale set model; only necessary if you have NAT pools configured on the scale set
    az vmss update --resource-group myResourceGroup --name myScaleSet --remove virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerInboundNatPools 0
    
    # Add the application gateway backend pool to the scale set model
    az vmss update --resource-group myResourceGroup --name myScaleSet --add virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].ApplicationGatewayBackendAddressPools '{"id": "/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendAddressPools/{applicationGatewayBackendPoolName}"}'
    ```

>[!NOTE]
> W tych poleceniach przyjęto założenie, że na zestawie skalowania jest tylko jedna konfiguracja IP i moduł równoważenia obciążenia. Jeśli istnieje wiele, może być konieczne użycie indeksu listy innego niż *0*.


## <a name="next-steps"></a>Następne kroki
Można także wykonywać typowe zadania zarządzania w zestawach skalowania przy użyciu [interfejsu wiersza polecenia platformy Azure](virtual-machine-scale-sets-manage-cli.md) lub [Azure PowerShell](virtual-machine-scale-sets-manage-powershell.md).
