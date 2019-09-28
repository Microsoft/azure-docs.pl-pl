---
title: Rozszerzenie sterownika NVIDIA GPU — maszyny wirtualne platformy Azure z systemem Linux | Microsoft Docs
description: Microsoft Azure rozszerzenia do instalowania sterowników NVIDIA GPU na maszynach wirtualnych obliczeniowych serii N z systemem Linux.
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
ms.openlocfilehash: 83646c0b11bf558f667b29271a27d31e5489c157
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/20/2019
ms.locfileid: "71174007"
---
# <a name="nvidia-gpu-driver-extension-for-linux"></a>Rozszerzenie sterownika GPU NVIDIA dla systemu Linux

## <a name="overview"></a>Przegląd

To rozszerzenie powoduje zainstalowanie sterowników NVIDIA GPU na maszynach wirtualnych serii N z systemem Linux. W zależności od rodziny maszyn wirtualnych rozszerzenie instaluje sterowniki CUDA lub GRID. Jeśli instalujesz sterowniki firmy NVIDIA przy użyciu tego rozszerzenia, akceptujesz i wyrażasz zgodę na warunki [umowy licencyjnej użytkownika końcowego firmy NVIDIA](https://go.microsoft.com/fwlink/?linkid=874330). Podczas instalacji maszyna wirtualna może zostać ponownie przetworzony w celu ukończenia instalacji sterownika.

Instrukcje dotyczące ręcznej instalacji sterowników i aktualnie obsługiwanych wersji są dostępne [tutaj.](
https://docs.microsoft.com/azure/virtual-machines/linux/n-series-driver-setup)
Rozszerzenie jest również dostępne do zainstalowania sterowników NVIDIA GPU na [maszynach wirtualnych z serii N systemu Windows](hpccompute-gpu-windows.md).

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="operating-system"></a>System operacyjny

To rozszerzenie obsługuje następujący dystrybucje systemu operacyjnego, w zależności od obsługi sterowników dla konkretnej wersji systemu operacyjnego.

| Dystrybucja | Version |
|---|---|
| W systemie Linux: Ubuntu | 16.04 LTS, 18.04 LTS |
| W systemie Linux: Red Hat Enterprise Linux | 7.3, 7.4, 7.5, 7.6 |
| W systemie Linux: CentOS | 7.3, 7.4, 7.5, 7.6 |

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
    "typeHandlerVersion": "1.2",
    "autoUpgradeMinorVersion": true,
    "settings": {
    }
  }
}
```

### <a name="properties"></a>properties

| Name | Wartość / przykład | Typ danych |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | date |
| publisher | Microsoft.HpcCompute | string |
| type | NvidiaGpuDriverLinux | string |
| typeHandlerVersion | 1.2 | int |

### <a name="settings"></a>Ustawienia

Wszystkie ustawienia są opcjonalne. Domyślnym zachowaniem jest nieaktualizowanie jądra, jeśli nie jest to wymagane w przypadku instalacji sterownika, zainstaluj najnowszy obsługiwany sterownik i zestaw narzędzi CUDA (zgodnie z potrzebami).

| Name | Opis | Default Value | Prawidłowe wartości | Typ danych |
| ---- | ---- | ---- | ---- | ---- |
| updateOS | Aktualizowanie jądra nawet wtedy, gdy nie jest to wymagane na potrzeby instalacji sterownika | false | true, false | boolean |
| driverVersion | MAGAZYN Wersja sterownika siatki<br> NC/ND: Wersja zestawu narzędzi CUDA. Najnowsze sterowniki dla wybranych CUDA są instalowane automatycznie. | latest | SIATKI "430,30", "418,70", "410,92", "410,71", "390,75", "390,57", "390,42"<br> CUDA "10.0.130", "9.2.88", "9.1.85" | string |
| installCUDA | Zainstaluj zestaw narzędzi CUDA. Dotyczy tylko maszyn wirtualnych z serii NC/ND. | true | true, false | boolean |


## <a name="deployment"></a>Wdrożenie


### <a name="azure-resource-manager-template"></a>Szablon usługi Azure Resource Manager 

Rozszerzenia maszyn wirtualnych platformy Azure można wdrażać przy użyciu szablonów usługi Azure Resource Manager. Szablony są idealne do wdrożenia co najmniej jednej maszyny wirtualnej, która wymaga konfiguracji po wdrożeniu.

Konfiguracji JSON dla rozszerzenia maszyny wirtualnej mogą być zagnieżdżone wewnątrz zasobu maszyny wirtualnej lub umieszczone w katalogu głównego lub najwyższego poziomu szablon JSON usługi Resource Manager. Położenie konfiguracji JSON ma wpływ na wartości nazwy i typu zasobu. Aby uzyskać więcej informacji, zobacz [Ustaw nazwę i typ zasobów podrzędnych](../../azure-resource-manager/resource-manager-template-child-resource.md). 

W poniższym przykładzie przyjęto założenie, że rozszerzenie jest zagnieżdżone w ramach zasobu maszyny wirtualnej. Zagnieżdżanie rozszerzenia zasobu, za pomocą pliku JSON jest umieszczany w `"resources": []` obiektu maszyny wirtualnej.

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

Poniższy przykład odzwierciedla powyższe Azure Resource Manager i przykłady programu PowerShell, a także dodaje ustawienia niestandardowe jako przykład dla instalacji sterowników innych niż domyślne. W konkretnym przypadku aktualizuje jądro systemu operacyjnego i instaluje określony sterownik wersji zestawu narzędzi CUDA.

```azurecli
az vm extension set `
  --resource-group myResourceGroup `
  --vm-name myVM `
  --name NvidiaGpuDriverLinux `
  --publisher Microsoft.HpcCompute `
  --version 1.2 `
  --settings '{ `
    "updateOS": true, `
    "driverVersion": "9.1.85", `
  }'
```

## <a name="troubleshoot-and-support"></a>Rozwiązywanie problemów i pomocy technicznej

### <a name="troubleshoot"></a>Rozwiązywanie problemów

Dane dotyczące stanu wdrożeń rozszerzeń można pobrać z Azure Portal i przy użyciu Azure PowerShell i interfejsu wiersza polecenia platformy Azure. Aby wyświetlić stan wdrożenia dla danej maszyny wirtualnej, uruchom następujące polecenie.

```powershell
Get-AzVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

Dane wyjściowe wykonywania rozszerzenia jest rejestrowany w następującym pliku:

```bash
/var/log/azure/nvidia-vmext-status
```

### <a name="exit-codes"></a>Kody zakończenia

| Kod zakończenia | Znaczenie | Możliwe działania |
| :---: | --- | --- |
| 0 | Operacja zakończona pomyślnie |
| 1 | Nieprawidłowe użycie rozszerzenia | Sprawdź dziennik wyjściowy wykonywania |
| 10 | Usługi integracji z systemem Linux dla funkcji Hyper-V i platformy Azure nie są dostępne ani zainstalowane | Sprawdź dane wyjściowe lspci |
| 11 | Nie znaleziono procesora GPU NVIDIA dla tego rozmiaru maszyny wirtualnej | Korzystanie z [obsługiwanego rozmiaru maszyny wirtualnej i systemu operacyjnego](../linux/n-series-driver-setup.md) |
| 12 | Nieobsługiwana oferta obrazu |
| 13 | Rozmiar maszyny wirtualnej nie jest obsługiwany | Wdrażanie programu przy użyciu maszyny wirtualnej z serii N |
| 14 | Operacja nie powiodła się | Sprawdź dziennik wyjściowy wykonywania |


### <a name="support"></a>Pomoc techniczna

Jeśli potrzebujesz dodatkowej pomocy w dowolnym momencie, w tym artykule, możesz skontaktować się ze ekspertów platformy Azure na [forów platformy Azure z subskrypcją MSDN i Stack Overflow](https://azure.microsoft.com/support/community/). Alternatywnie mogą zgłaszać zdarzenia pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/) i wybierz Uzyskaj pomoc techniczną. Aby uzyskać informacje o korzystaniu z pomocy technicznej platformy Azure, przeczytaj [pomocy technicznej Microsoft Azure — często zadawane pytania](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji na temat rozszerzeń, zobacz [rozszerzenia i funkcje maszyny wirtualnej dla systemu Linux](features-linux.md).

Aby uzyskać więcej informacji o maszynach wirtualnych z serii N, zobacz [rozmiary maszyn wirtualnych zoptymalizowanych pod kątem procesora GPU](../linux/sizes-gpu.md).
