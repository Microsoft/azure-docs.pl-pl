---
title: Rozszerzenie sterownika procesora graficznego NVIDIA — maszyny wirtualne z systemem Linux platformy Azure
description: Rozszerzenie Microsoft Azure do instalowania sterowników gpu NVIDIA na maszynach wirtualnych z serii N z systemem Linux.
services: virtual-machines-linux
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/11/2019
ms.author: akjosh
ms.openlocfilehash: e7f6653043d46925d6a4c35eedaf81224ea6c36d
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81415785"
---
# <a name="nvidia-gpu-driver-extension-for-linux"></a>Rozszerzenie sterownika GPU NVIDIA dla systemu Linux

## <a name="overview"></a>Omówienie

To rozszerzenie instaluje sterowniki GPU NVIDIA na maszynach wirtualnych z serii N linuksa. W zależności od rodziny maszyn wirtualnych rozszerzenie instaluje sterowniki CUDA lub GRID. Podczas instalowania sterowników NVIDIA przy użyciu tego rozszerzenia użytkownik akceptuje i akceptuje warunki [umowy licencyjnej użytkownika końcowego firmy NVIDIA.](https://go.microsoft.com/fwlink/?linkid=874330) Podczas procesu instalacji maszyna wirtualna może ponownie uruchomić komputer, aby zakończyć konfigurację sterownika.

Instrukcje dotyczące ręcznej instalacji sterowników i bieżących obsługiwanych wersji są dostępne [tutaj.](
https://docs.microsoft.com/azure/virtual-machines/linux/n-series-driver-setup)
Dostępne jest również rozszerzenie do instalowania sterowników GPU NVIDIA na [maszynach wirtualnych z serii Windows N.](hpccompute-gpu-windows.md)

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="operating-system"></a>System operacyjny

To rozszerzenie obsługuje następujące dystrybucje systemu operacyjnego, w zależności od obsługi sterowników dla określonej wersji systemu operacyjnego.

| Dystrybucja | Wersja |
|---|---|
| Linux: Ubuntu | 16.04 LTS, 18.04 LTS |
| Linux: Red Hat Enterprise Linux | 7.3, 7.4, 7.5, 7.6 |
| Linux: CentOS | 7.3, 7.4, 7.5, 7.6 |

### <a name="internet-connectivity"></a>Łączność z Internetem

Rozszerzenie Microsoft Azure dla sterowników gpu NVIDIA wymaga, aby docelowa maszyna wirtualna była podłączona do Internetu i miała dostęp.

## <a name="extension-schema"></a>Schemat rozszerzenia

Poniżej przedstawiono schemat rozszerzenia.

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
    "typeHandlerVersion": "1.2",
    "autoUpgradeMinorVersion": true,
    "settings": {
    }
  }
}
```

### <a name="properties"></a>Właściwości

| Nazwa | Wartość / Przykład | Typ danych |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | date |
| wydawca | Microsoft.HpcCompute | ciąg |
| type | NvidiaGpuDriverLinux | ciąg |
| typHandlerVersion | 1.2 | int |

### <a name="settings"></a>Ustawienia

Wszystkie ustawienia są opcjonalne. Domyślnym zachowaniem jest nie aktualizowanie jądra, jeśli nie jest wymagane do instalacji sterownika, zainstalowanie najnowszego obsługiwanego sterownika i zestawu narzędzi CUDA (w stosownych przypadkach).

| Nazwa | Opis | Wartość domyślna | Prawidłowe wartości | Typ danych |
| ---- | ---- | ---- | ---- | ---- |
| updateOS | Zaktualizuj jądro, nawet jeśli nie jest wymagane do instalacji sterownika | false | wartość true, false | wartość logiczna |
| driverVersion (wersja kierowcy) | NV: Wersja sterownika GRID<br> NC/ND: Wersja zestawu narzędzi CUDA. Najnowsze sterowniki dla wybranego CUDA są instalowane automatycznie. | najnowsza | SIATKA: "430.30", "418.70", "410.92", "410.71", "390.75", "390.57", "390.42"<br> CUDA: "10.0.130", "9.2.88", "9.1.85" | ciąg |
| installCUDA | Zainstaluj zestaw narzędzi CUDA. Dotyczy tylko maszyn wirtualnych serii NC/ND. | true | wartość true, false | wartość logiczna |


## <a name="deployment"></a>Wdrożenie


### <a name="azure-resource-manager-template"></a>Szablon usługi Azure Resource Manager 

Rozszerzenia maszyn wirtualnych platformy Azure można wdrożyć za pomocą szablonów usługi Azure Resource Manager. Szablony są idealne podczas wdrażania co najmniej jednej maszyny wirtualnej, które wymagają konfiguracji po wdrożeniu.

Konfiguracja JSON dla rozszerzenia maszyny wirtualnej może być zagnieżdżona wewnątrz zasobu maszyny wirtualnej lub umieszczona na głównym lub najwyższym poziomie szablonu JSON Menedżera zasobów. Położenie konfiguracji JSON wpływa na wartość nazwy i typu zasobu. Aby uzyskać więcej informacji, zobacz [Ustawianie nazwy i typu zasobów podrzędnych](../../azure-resource-manager/resource-manager-template-child-resource.md). 

W poniższym przykładzie przyjęto założenie, że rozszerzenie jest zagnieżdżone wewnątrz zasobu maszyny wirtualnej. Podczas zagnieżdżania zasobu rozszerzenia `"resources": []` JSON jest umieszczany w obiekcie maszyny wirtualnej.

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
    -ExtensionName "NvidiaGpuDriverLinux" `
    -ExtensionType "NvidiaGpuDriverLinux" `
    -TypeHandlerVersion 1.2 `
    -SettingString '{ `
    }'
```

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Poniższy przykład odzwierciedla powyższe przykłady usługi Azure Resource Manager i Programu PowerShell, a także dodaje ustawienia niestandardowe jako przykład instalacji sterowników innych niż domyślne. W szczególności aktualizuje jądro systemu operacyjnego i instaluje określony sterownik wersji zestawu narzędzi CUDA.

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name NvidiaGpuDriverLinux \
  --publisher Microsoft.HpcCompute \
  --version 1.2 \
  --settings '{ \
    "updateOS": true, \
    "driverVersion": "9.1.85", \
  }'
```

## <a name="troubleshoot-and-support"></a>Rozwiązywanie problemów i pomoc techniczna

### <a name="troubleshoot"></a>Rozwiązywanie problemów

Dane dotyczące stanu wdrożeń rozszerzeń można pobrać z witryny Azure portal i przy użyciu platformy Azure PowerShell i interfejsu wiersza polecenia platformy Azure. Aby wyświetlić stan wdrożenia rozszerzeń dla danej maszyny Wirtualnej, uruchom następujące polecenie.

```powershell
Get-AzVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

Dane wyjściowe wykonania rozszerzenia są rejestrowane w następującym pliku:

```bash
/var/log/azure/nvidia-vmext-status
```

### <a name="exit-codes"></a>Kody zakończenia

| Kod wyjścia | Znaczenie | Możliwe działanie |
| :---: | --- | --- |
| 0 | Operacja udana |
| 1 | Nieprawidłowe użycie rozszerzenia | Sprawdź dziennik wyjścia wykonania |
| 10 | Usługi integracji systemu Linux dla funkcji Hyper-V i platformy Azure są niedostępne lub zainstalowane | Sprawdź moc wyjściową lspci |
| 11 | Nie znaleziono procesora graficznego NVIDIA w tym rozmiarze maszyny Wirtualnej | Używanie [obsługiwanego rozmiaru maszyny Wirtualnej i systemu operacyjnego](../linux/n-series-driver-setup.md) |
| 12 | Oferta obrazów nie jest obsługiwana |
| 13 | Rozmiar maszyny Wirtualnej nie jest obsługiwany | Wdrażanie za pomocą maszyny wirtualnej z serii N |
| 14 | Operacja nie powiodła się | Sprawdź dziennik wyjścia wykonania |


### <a name="support"></a>Pomoc techniczna

Jeśli potrzebujesz więcej pomocy w dowolnym momencie tego artykułu, możesz skontaktować się z ekspertami platformy Azure na [forach MSDN Azure i Stack Overflow](https://azure.microsoft.com/support/community/). Alternatywnie można zgłosić zdarzenie pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/) i wybierz pozycję Uzyskaj pomoc techniczną. Aby uzyskać informacje na temat korzystania z pomocy technicznej platformy Azure, przeczytaj często zadawane [pytania dotyczące pomocy technicznej platformy Microsoft Azure](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji na temat rozszerzeń, zobacz [Rozszerzenia i funkcje maszyn wirtualnych dla systemu Linux](features-linux.md).

Aby uzyskać więcej informacji na temat maszyn wirtualnych z serii N, zobacz [Rozmiary maszyn wirtualnych zoptymalizowane pod kątem procesora GPU](../linux/sizes-gpu.md).
