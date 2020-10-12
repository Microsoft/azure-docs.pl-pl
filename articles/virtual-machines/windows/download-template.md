---
title: Pobieranie szablonu dla maszyny wirtualnej platformy Azure
description: Pobierz szablon dla maszyny wirtualnej przy użyciu portalu lub programu PowerShell.
author: cynthn
manager: gwallace
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 11/17/2017
ms.author: cynthn
ms.openlocfilehash: 5b7e50ebe6f09de2555af03a47641ef6ca92e92a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87288305"
---
# <a name="download-the-template-for-a-vm"></a>Pobieranie szablonu dla maszyny wirtualnej
Podczas tworzenia maszyny wirtualnej na platformie Azure przy użyciu portalu lub programu PowerShell automatycznie tworzony jest szablon Menedżer zasobów. Możesz użyć tego szablonu, aby szybko zduplikować wdrożenie. Szablon zawiera informacje na temat wszystkich zasobów w grupie zasobów. W przypadku maszyny wirtualnej oznacza to, że szablon zawiera wszystkie elementy, które są tworzone w ramach obsługi maszyny wirtualnej w tej grupie zasobów, w tym zasoby sieciowe.

## <a name="download-the-template-using-the-portal"></a>Pobieranie szablonu przy użyciu portalu
1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com/).
2. Jedno z menu po lewej stronie, wybierz pozycję **Virtual Machines**.
3. Wybierz maszynę wirtualną z listy.
4. Wybierz pozycję **Eksportuj szablon**.
5. Wybierz pozycję **Pobierz** z menu u góry i Zapisz plik zip na komputerze lokalnym.
6. Otwórz plik zip i Wyodrębnij pliki do folderu. Plik. zip zawiera:
   
   * parameters.jsna
   * template.jsna

template.jsw pliku to szablon.

## <a name="download-the-template-using-powershell"></a>Pobieranie szablonu przy użyciu programu PowerShell
Plik szablonu JSON można także pobrać przy użyciu polecenia cmdlet [Export-AzResourceGroup](/powershell/module/az.resources/export-azresourcegroup) . Możesz użyć parametru, `-path` Aby podać nazwę pliku i ścieżkę pliku. JSON. Ten przykład pokazuje, jak pobrać szablon dla grupy zasobów o nazwie Moja **zasobów** do folderu **C:\users\public\downloads** na komputerze lokalnym.

```powershell
    Export-AzResourceGroup -ResourceGroupName "myResourceGroup" -Path "C:\users\public\downloads"
```

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej o wdrażaniu zasobów przy użyciu szablonów, zobacz [Przewodnik po szablonach Menedżer zasobów](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md).
