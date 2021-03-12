---
title: Rozszerzenie sterownika NVIDIA GPU — maszyny wirtualne platformy Azure z systemem Linux
description: Microsoft Azure rozszerzenia do instalowania sterowników NVIDIA GPU na maszynach wirtualnych obliczeniowych serii N z systemem Linux.
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: virtual-machines
ms.subservice: hpc
ms.collection: linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 01/21/2021
ms.author: amverma
ms.openlocfilehash: fa2b82f3246fd87830010f572ba23aa2df075053
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102566230"
---
# <a name="nvidia-gpu-driver-extension-for-linux"></a>Rozszerzenie sterownika GPU NVIDIA dla systemu Linux

## <a name="overview"></a>Omówienie

To rozszerzenie powoduje zainstalowanie sterowników NVIDIA GPU na maszynach wirtualnych serii N z systemem Linux. W zależności od rodziny maszyn wirtualnych rozszerzenie instaluje sterowniki CUDA lub GRID. Jeśli instalujesz sterowniki firmy NVIDIA przy użyciu tego rozszerzenia, akceptujesz i wyrażasz zgodę na warunki [umowy licencyjnej nvidia End-User](https://go.microsoft.com/fwlink/?linkid=874330). Podczas instalacji maszyna wirtualna może zostać ponownie przetworzony w celu ukończenia instalacji sterownika.

Instrukcje dotyczące ręcznej instalacji sterowników i aktualnie obsługiwanych wersji są dostępne [tutaj](../linux/n-series-driver-setup.md).
Rozszerzenie jest również dostępne do zainstalowania sterowników NVIDIA GPU na [maszynach wirtualnych z serii N systemu Windows](hpccompute-gpu-windows.md).

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="operating-system"></a>System operacyjny

To rozszerzenie obsługuje następujący dystrybucje systemu operacyjnego, w zależności od obsługi sterowników dla konkretnej wersji systemu operacyjnego.

| Dystrybucja | Wersja |
|---|---|
| Linux: Ubuntu | 16,04 LTS, 18,04 LTS |
| Linux: Red Hat Enterprise Linux | 7,3, 7,4, 7,5, 7,6, 7,7, 7,8 |
| Linux: CentOS | 7,3, 7,4, 7,5, 7,6, 7,7, 7,8 |

### <a name="internet-connectivity"></a>Łączność z Internetem

Rozszerzenie Microsoft Azure dla sterowników procesora GPU firmy NVIDIA wymaga, aby docelowa maszyna wirtualna była połączona z Internetem i miała dostęp.

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
    "type": "NvidiaGpuDriverLinux",
    "typeHandlerVersion": "1.3",
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
| typ | NvidiaGpuDriverLinux | ciąg |
| typeHandlerVersion | 1.3 | int |

### <a name="settings"></a>Ustawienia

Wszystkie ustawienia są opcjonalne. Domyślnym zachowaniem jest nieaktualizowanie jądra, jeśli nie jest to wymagane w przypadku instalacji sterownika, zainstaluj najnowszy obsługiwany sterownik i zestaw narzędzi CUDA (zgodnie z potrzebami).

| Nazwa | Opis | Wartość domyślna | Prawidłowe wartości | Typ danych |
| ---- | ---- | ---- | ---- | ---- |
| updateOS | Aktualizowanie jądra nawet wtedy, gdy nie jest to wymagane na potrzeby instalacji sterownika | fałsz | wartość true, false | boolean |
| driverVersion | NV: wersja sterownika siatki<br> NC/ND: wersja zestawu narzędzi CUDA. Najnowsze sterowniki dla wybranych CUDA są instalowane automatycznie. | latest | [Lista](https://github.com/Azure/azhpc-extensions/blob/master/NvidiaGPU/resources.json) obsługiwanych wersji sterowników | ciąg |
| installCUDA | Zainstaluj zestaw narzędzi CUDA. Dotyczy tylko maszyn wirtualnych z serii NC/ND. | true | wartość true, false | boolean |


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
    "type": "NvidiaGpuDriverLinux",
    "typeHandlerVersion": "1.3",
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
    -ExtensionName "NvidiaGpuDriverLinux" `
    -ExtensionType "NvidiaGpuDriverLinux" `
    -TypeHandlerVersion 1.3 `
    -SettingString '{ `
    }'
```

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Poniższy przykład odzwierciedla powyższe Azure Resource Manager i przykłady programu PowerShell.

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name NvidiaGpuDriverLinux \
  --publisher Microsoft.HpcCompute \
  --version 1.3 
```

W poniższym przykładzie dodano również dwa opcjonalne ustawienia niestandardowe jako przykład instalacji sterowników innych niż domyślne. W konkretnym przypadku aktualizuje jądro systemu operacyjnego i instaluje określony sterownik wersji zestawu narzędzi CUDA. Należy pamiętać, że opcja "--Settings" jest opcjonalna i domyślna. Należy pamiętać, że aktualizacja jądra może wydłużyć czasy instalacji rozszerzenia. Wybór konkretnej (starszej) wersji CUDA tolkit może nie zawsze być zgodny z nowszymi jądrami.

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name NvidiaGpuDriverLinux \
  --publisher Microsoft.HpcCompute \
  --version 1.3 \
  --settings '{ \
    "updateOS": true, \
    "driverVersion": "10.0.130" \
  }'
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

Dane wyjściowe wykonania rozszerzenia są rejestrowane w następującym pliku. Zapoznaj się z tym plikiem, aby śledzić stan (wszystkie długotrwałe) instalacji oraz rozwiązywać problemy związane z błędami.

```bash
/var/log/azure/nvidia-vmext-status
```

### <a name="exit-codes"></a>Kody zakończenia

| Kod wyjścia | Znaczenie | Możliwa akcja |
| :---: | --- | --- |
| 0 | Operacja zakończona pomyślnie |
| 1 | Nieprawidłowe użycie rozszerzenia | Sprawdź dziennik wyjściowy wykonywania |
| 10 | Usługi integracji z systemem Linux dla funkcji Hyper-V i platformy Azure nie są dostępne ani zainstalowane | Sprawdź dane wyjściowe lspci |
| 11 | Nie znaleziono procesora GPU NVIDIA dla tego rozmiaru maszyny wirtualnej | Korzystanie z [obsługiwanego rozmiaru maszyny wirtualnej i systemu operacyjnego](../linux/n-series-driver-setup.md) |
| 12 | Nieobsługiwana oferta obrazu |
| 13 | Rozmiar maszyny wirtualnej nie jest obsługiwany | Wdrażanie programu przy użyciu maszyny wirtualnej z serii N |
| 14 | Operacja nie powiodła się | Sprawdź dziennik wyjściowy wykonywania |


### <a name="support"></a>Pomoc techniczna

Jeśli potrzebujesz więcej pomocy w dowolnym punkcie tego artykułu, możesz skontaktować się z ekspertami platformy Azure na [forach MSDN i Stack Overflow](https://azure.microsoft.com/support/community/). Alternatywnie możesz zaplikować zdarzenie pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej systemu Azure](https://azure.microsoft.com/support/options/) i wybierz pozycję Uzyskaj pomoc techniczną. Aby uzyskać informacje o korzystaniu z pomocy technicznej platformy Azure, przeczytaj temat [Microsoft Azure support — często zadawane pytania](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji na temat rozszerzeń, zobacz [rozszerzenia i funkcje maszyny wirtualnej dla systemu Linux](features-linux.md).

Aby uzyskać więcej informacji o maszynach wirtualnych z serii N, zobacz [rozmiary maszyn wirtualnych zoptymalizowanych pod kątem procesora GPU](../sizes-gpu.md).
