---
title: Rozszerzenie sterownika InfiniBand — maszyny wirtualne platformy Azure z systemem Linux
description: Microsoft Azure rozszerzenia do instalowania sterowników InfiniBand na maszynach wirtualnych obliczeniowych serii H i N z systemem Linux.
services: virtual-machines
documentationcenter: ''
author: vermagit
editor: ''
ms.assetid: ''
ms.service: virtual-machines
ms.subservice: hpc
ms.collection: linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/20/2020
ms.author: amverma
ms.openlocfilehash: 1219f5befb4de81850681a53c10f03dc81249d53
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102559736"
---
# <a name="infiniband-driver-extension-for-linux"></a>Rozszerzenie sterownika InfiniBand dla systemu Linux

To rozszerzenie instaluje sterowniki InfiniBand OFED na serwerze InfiniBand i maszynach wirtualnych z obsługą wirtualizacji SR-IOV (rozmiary "r [") oraz](../sizes-hpc.md) maszyny wirtualne [serii N](../sizes-gpu.md) z systemem Linux. W zależności od rodziny maszyn wirtualnych rozszerzenie instaluje odpowiednie sterowniki dla karty sieciowej Connect-X.

Instrukcje dotyczące ręcznej instalacji sterowników OFED są dostępne [tutaj](../workloads/hpc/enable-infiniband.md#manual-installation).

Rozszerzenie jest również dostępne do zainstalowania sterowników InfiniBand dla [maszyn wirtualnych z systemem Windows](hpc-compute-infiniband-windows.md).

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="operating-system"></a>System operacyjny

To rozszerzenie obsługuje następujący dystrybucje systemu operacyjnego, w zależności od obsługi sterowników dla konkretnej wersji systemu operacyjnego.

| Dystrybucja | Wersja |
|---|---|
| Linux: Ubuntu | 16,04 LTS, 18,04 LTS, 20,04 LTS |
| Linux: CentOS | 7,4, 7,5, 7,6, 7,7, 8,1, 8, 2 |
| Linux: Red Hat Enterprise Linux | 7,4, 7,5, 7,6, 7,7, 8,1, 8, 2 |

### <a name="internet-connectivity"></a>Łączność z Internetem

Rozszerzenie Microsoft Azure dla sterowników InfiniBand wymaga, aby docelowa maszyna wirtualna była podłączona do i ma dostęp do Internetu.

## <a name="extension-schema"></a>Schemat rozszerzenia

Poniższy kod JSON pokazuje schemat rozszerzenia.

```json
{
  "name": "<myExtensionName>",
  "type": "extensions",
  "apiVersion": "2015-06-15",
  "location": "<location>",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', <myVM>)]"
  ],
  "properties": {
    "publisher": "Microsoft.HpcCompute",
    "type": "InfiniBandDriverLinux",
    "typeHandlerVersion": "1.1",
    "autoUpgradeMinorVersion": true,
    "settings": {
    }
  }
}
```

### <a name="properties"></a>Właściwości

| Nazwa | Wartość/przykład | Typ danych |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | data |
| publisher | Microsoft. HpcCompute | ciąg |
| typ | InfiniBandDriverLinux | ciąg |
| typeHandlerVersion | 1.1 | int |



## <a name="deployment"></a>Wdrożenie


### <a name="azure-resource-manager-template"></a>Szablon usługi Azure Resource Manager 

Rozszerzenia maszyny wirtualnej platformy Azure można wdrażać za pomocą szablonów Azure Resource Manager. Szablony są idealne do wdrożenia co najmniej jednej maszyny wirtualnej, która wymaga konfiguracji po wdrożeniu.

Konfiguracja JSON rozszerzenia maszyny wirtualnej może być zagnieżdżona w ramach zasobu maszyny wirtualnej lub umieszczona na głównym lub najwyższego poziomu szablonu JSON Menedżer zasobów. Położenie konfiguracji JSON wpływa na wartość nazwy i typu zasobu. Aby uzyskać więcej informacji, zobacz [Ustawianie nazwy i typu dla zasobów podrzędnych](../../azure-resource-manager/templates/child-resource-name-type.md). 

W poniższym przykładzie przyjęto założenie, że rozszerzenie jest zagnieżdżone w ramach zasobu maszyny wirtualnej. Podczas zagnieżdżania zasobu rozszerzenia kod JSON jest umieszczany w `"resources": []` obiekcie maszyny wirtualnej.

```json
{
  "name": "myExtensionName",
  "type": "extensions",
  "location": "[resourceGroup().location]",
  "apiVersion": "2015-06-15",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', myVM)]"
  ],
  "properties": {
    "publisher": "Microsoft.HpcCompute",
    "type": "InfiniBandDriverLinux",
    "typeHandlerVersion": "1.1",
    "autoUpgradeMinorVersion": true,
    "settings": {
    }
  }
}
```

### <a name="powershell"></a>PowerShell

```powershell
Set-AzVMExtension
    -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" `
    -Location "southcentralus" `
    -Publisher "Microsoft.HpcCompute" `
    -ExtensionName "InfiniBandDriverLinux" `
    -ExtensionType "InfiniBandDriverLinux" `
    -TypeHandlerVersion 1.1 `
    -SettingString '{ `
    }'
```

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name InfiniBandDriverLinux \
  --publisher Microsoft.HpcCompute \
  --version 1.1 
```

### <a name="add-extension-to-a-virtual-machine-scale-set"></a>Dodawanie rozszerzenia do zestawu skalowania maszyn wirtualnych

W poniższym przykładzie jest instalowana Najnowsza wersja 1,1 rozszerzenia InfiniBandDriverLinux na wszystkich maszynach wirtualnych obsługujących funkcję RDMA w istniejącym zestawie skalowania maszyn wirtualnych o nazwie *myVMSS* wdrożonym w grupie zasobów o nazwie Moja *zasobów*:

  ```powershell
  $VMSS = Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS"
  Add-AzVmssExtension -VirtualMachineScaleSet $VMSS -Name "InfiniBandDriverLinux" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverLinux" -TypeHandlerVersion "1.1"
  Update-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "MyVMSS" -VirtualMachineScaleSet $VMSS
  Update-AzVmssInstance -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS" -InstanceId "*"
```


## <a name="troubleshoot-and-support"></a>Rozwiązywanie problemów i pomoc techniczna

### <a name="troubleshoot"></a>Rozwiązywanie problemów

Dane dotyczące stanu wdrożeń rozszerzeń można pobrać z Azure Portal i przy użyciu Azure PowerShell i interfejsu wiersza polecenia platformy Azure. Aby wyświetlić stan wdrożenia dla danej maszyny wirtualnej, uruchom następujące polecenie.

```powershell
Get-AzVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

Dane wyjściowe wykonania rozszerzenia są rejestrowane w następującym pliku. Zapoznaj się z tym plikiem, aby śledzić stan instalacji oraz rozwiązywać problemy.

```bash
/var/log/azure/ib-vmext-status
```

### <a name="exit-codes"></a>Kody zakończenia

W poniższej tabeli opisano znaczenie i zalecaną akcję na podstawie kodów zakończenia procesu instalacji rozszerzenia.

| Kod wyjścia | Znaczenie | Możliwa akcja |
| :---: | --- | --- |
| 0 | Operacja zakończona pomyślnie |
| 1 | Nieprawidłowe użycie rozszerzenia | Sprawdź dziennik wyjściowy wykonywania |
| 10 | Usługi integracji z systemem Linux dla funkcji Hyper-V i platformy Azure nie są dostępne ani zainstalowane | Sprawdź dane wyjściowe lspci |
| 11 | Nie znaleziono Mellanox InfiniBand dla tego rozmiaru maszyny wirtualnej | Korzystanie z [obsługiwanego rozmiaru maszyny wirtualnej i systemu operacyjnego](../sizes-hpc.md) |
| 12 | Nieobsługiwana oferta obrazu |
| 13 | Rozmiar maszyny wirtualnej nie jest obsługiwany | Używanie maszyny wirtualnej z serii [H](../sizes-hpc.md) [i serii n serii n w](../sizes-gpu.md)celu wdrożenia |
| 14 | Operacja nie powiodła się | Sprawdź dziennik wyjściowy wykonywania |


### <a name="support"></a>Pomoc techniczna

Jeśli potrzebujesz więcej pomocy w dowolnym punkcie tego artykułu, możesz skontaktować się z ekspertami platformy Azure na [forach MSDN i Stack Overflow](https://azure.microsoft.com/support/community/). Możesz też zaplikować zdarzenia pomocy technicznej w [witrynie pomocy technicznej systemu Azure](https://azure.microsoft.com/support/options/). Aby uzyskać informacje o korzystaniu z pomocy technicznej platformy Azure, przeczytaj temat [Microsoft Azure support — często zadawane pytania](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji na temat maszyn wirtualnych z obsługą InfiniBand (rozmiary "r"), zobacz maszyny wirtualne serii [H](../sizes-hpc.md) i [N](../sizes-gpu.md) .

> [!div class="nextstepaction"]
> [Dowiedz się więcej o rozszerzeniach i funkcjach maszyn wirtualnych z systemem Linux](features-linux.md)