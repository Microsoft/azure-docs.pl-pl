---
title: Rozszerzenie sterownika InfiniBand — maszyny wirtualne platformy Azure z systemem Windows
description: Microsoft Azure rozszerzenie do instalowania sterowników InfiniBand na maszynach wirtualnych obliczeniowych serii H i N z systemem Windows.
services: virtual-machines
documentationcenter: ''
author: vermagit
editor: ''
ms.assetid: ''
ms.service: virtual-machines
ms.subservice: hpc
ms.collection: windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 02/01/2021
ms.author: amverma
ms.openlocfilehash: 81720c18ce858cd5667413a9d39afdadc95acb23
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102559702"
---
# <a name="infiniband-driver-extension-for-windows"></a>Rozszerzenie sterownika InfiniBand dla systemu Windows

To rozszerzenie instaluje sterowniki InfiniBand ND (dla usługi non-SR-IOV Enabled) oraz sterowniki OFED (dla maszyn wirtualnych z obsługą wirtualizacji SR- [IOV) (](../sizes-hpc.md) rozmiary "r") i maszyny wirtualne [serii N](../sizes-gpu.md) z systemem Windows. W zależności od rodziny maszyn wirtualnych rozszerzenie instaluje odpowiednie sterowniki dla karty sieciowej Connect-X.

Rozszerzenie jest również dostępne do zainstalowania sterowników InfiniBand dla [maszyn wirtualnych z systemem Linux](hpc-compute-infiniband-linux.md).

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="operating-system"></a>System operacyjny

To rozszerzenie obsługuje następujący dystrybucje systemu operacyjnego, w zależności od obsługi sterowników dla konkretnej wersji systemu operacyjnego. Zanotuj odpowiednią kartę sieciową InfiniBand dla interesujących rozmiarów maszyn wirtualnych z serii H i N.

| Dystrybucja | Sterowniki karty sieciowej InfiniBand |
|---|---|
| Windows 10 | CX5, CX6 |
| Windows Server 2019 | CX5, CX6 |
| Windows Server 2016 | CX3-Pro, CX5, CX6 |
| Windows Server 2012 z dodatkiem R2 | CX3-Pro, CX5, CX6 |
| Windows Server 2012 | CX3-Pro, CX5, CX6 |

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
    "type": "InfiniBandDriverWindows",
    "typeHandlerVersion": "1.2",
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
| typ | InfiniBandDriverWindows | ciąg |
| typeHandlerVersion | 1.2 | int |



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
    "type": "InfiniBandDriverWindows",
    "typeHandlerVersion": "1.2",
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
    -ExtensionName "InfiniBandDriverWindows" `
    -ExtensionType "InfiniBandDriverWindows" `
    -TypeHandlerVersion 1.2 `
    -SettingString '{ `
    }'
```

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name InfiniBandDriverWindows \
  --publisher Microsoft.HpcCompute \
  --version 1.2 
```

### <a name="add-extension-to-a-virtual-machine-scale-set"></a>Dodawanie rozszerzenia do zestawu skalowania maszyn wirtualnych

W poniższym przykładzie jest instalowana Najnowsza wersja 1,2 rozszerzenia InfiniBandDriverWindows na wszystkich maszynach wirtualnych obsługujących funkcję RDMA w istniejącym zestawie skalowania maszyn wirtualnych o nazwie *myVMSS* wdrożonym w grupie zasobów o nazwie Moja *zasobów*:

  ```powershell
  $VMSS = Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS"
  Add-AzVmssExtension -VirtualMachineScaleSet $VMSS -Name "InfiniBandDriverWindows" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverWindows" -TypeHandlerVersion "1.2"
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

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.HpcCompute.InfiniBandDriverWindows\
```

### <a name="exit-codes"></a>Kody zakończenia

W poniższej tabeli opisano znaczenie i zalecaną akcję na podstawie kodów zakończenia procesu instalacji rozszerzenia.

| Kod błędu | Znaczenie | Możliwa akcja |
| :---: | --- | --- |
| 0 | Operacja zakończona pomyślnie |
| 3010 | Operacja powiodła się. Wymagany jest ponowny rozruch. |
| 100 | Operacja nie jest obsługiwana lub nie można jej ukończyć. | Możliwe przyczyny: nieobsługiwana wersja programu PowerShell, rozmiar maszyny wirtualnej nie jest maszyną wirtualną z obsługą InfiniBand, wystąpił błąd podczas pobierania danych. Sprawdź pliki dziennika, aby ustalić przyczynę błędu. |
| 240, 840 | Limit czasu operacji. | Ponów próbę wykonania operacji. |
| -1 | Wystąpił wyjątek. | Sprawdź pliki dziennika, aby ustalić przyczynę wyjątku. |

### <a name="support"></a>Pomoc techniczna

Jeśli potrzebujesz więcej pomocy w dowolnym punkcie tego artykułu, możesz skontaktować się z ekspertami platformy Azure na [forach MSDN i Stack Overflow](https://azure.microsoft.com/support/community/). Możesz też zaplikować zdarzenia pomocy technicznej w [witrynie pomocy technicznej systemu Azure](https://azure.microsoft.com/support/options/). Aby uzyskać informacje o korzystaniu z pomocy technicznej platformy Azure, przeczytaj temat [Microsoft Azure support — często zadawane pytania](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji na temat maszyn wirtualnych z obsługą InfiniBand (rozmiary "r"), zobacz maszyny wirtualne serii [H](../sizes-hpc.md) i [N](../sizes-gpu.md) .

> [!div class="nextstepaction"]
> [Dowiedz się więcej o rozszerzeniach i funkcjach maszyn wirtualnych z systemem Linux](features-linux.md)
