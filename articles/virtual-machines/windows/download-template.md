---
title: Pobierz szablon maszyny Wirtualnej platformy Azure
description: Pobierz szablon maszyny Wirtualnej przy użyciu portalu lub programu PowerShell.
author: cynthn
manager: gwallace
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 11/17/2017
ms.author: cynthn
ms.openlocfilehash: af6905f0ba62a9053e44134348721312ade6b9d7
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2020
ms.locfileid: "82085386"
---
# <a name="download-the-template-for-a-vm"></a>Pobieranie szablonu dla maszyny wirtualnej
Podczas tworzenia maszyny Wirtualnej na platformie Azure przy użyciu portalu lub programu PowerShell szablon Menedżera zasobów jest tworzony automatycznie dla Ciebie. Za pomocą tego szablonu można szybko powielić wdrożenie. Szablon zawiera informacje o wszystkich zasobach w grupie zasobów. Dla maszyny wirtualnej oznacza to, że szablon zawiera wszystko, co jest tworzone w celu obsługi maszyny Wirtualnej w tej grupie zasobów, w tym zasobów sieciowych.

## <a name="download-the-template-using-the-portal"></a>Pobierz szablon za pomocą portalu
1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com/).
2. Jedno z lewego menu wybierz opcję **Maszyny wirtualne**.
3. Wybierz maszynę wirtualną z listy.
4. Wybierz **pozycję Eksportuj szablon**.
5. Wybierz **polecenie Pobierz** z menu u góry i zapisz plik zip na komputerze lokalnym.
6. Otwórz plik zip i wyodrębnij pliki do folderu. Plik .zip zawiera:
   
   * parameters.json
   * plik template.json

Plik template.json jest szablonem.

## <a name="download-the-template-using-powershell"></a>Pobieranie szablonu przy użyciu programu PowerShell
Plik szablonu .json można również pobrać za pomocą polecenia cmdlet [Export-AzResourceGroup.](https://docs.microsoft.com/powershell/module/az.resources/export-azresourcegroup) Za pomocą `-path` tego parametru można podać nazwę pliku i ścieżkę dla pliku .json. W tym przykładzie pokazano, jak pobrać szablon grupy zasobów o nazwie **myResourceGroup** do folderu **C:\users\public\downloads** na komputerze lokalnym.

```powershell
    Export-AzResourceGroup -ResourceGroupName "myResourceGroup" -Path "C:\users\public\downloads"
```

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej o wdrażaniu zasobów przy użyciu szablonów, zobacz Przewodnik [po szablonie Menedżera zasobów](../../azure-resource-manager/resource-manager-template-walkthrough.md).

