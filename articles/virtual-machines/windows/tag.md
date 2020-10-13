---
title: Jak oznaczyć maszynę wirtualną przy użyciu programu PowerShell
description: Dowiedz się więcej na temat tagowania maszyny wirtualnej przy użyciu programu PowerShell
author: mmccrory
ms.service: virtual-machines-windows
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 07/05/2016
ms.author: memccror
ms.openlocfilehash: 3aa40ed396e87b342207fc51576cd28170c7d4e9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89322385"
---
# <a name="how-to-tag-a-virtual-machine-in-azure-using-powershell"></a>Jak oznaczyć maszynę wirtualną na platformie Azure przy użyciu programu PowerShell
W tym artykule opisano różne sposoby tagowania maszyny wirtualnej z systemem Windows na platformie Azure za pomocą modelu wdrażania Menedżer zasobów. Tagi to zdefiniowane przez użytkownika pary klucz/wartość, które mogą być umieszczone bezpośrednio w ramach zasobu lub grupy zasobów. Platforma Azure obsługuje obecnie do 50 tagów na zasób i grupę zasobów. Tagi mogą być umieszczane na zasobie w momencie tworzenia lub dodawane do istniejącego zasobu. Należy pamiętać, że Tagi są obsługiwane tylko dla zasobów utworzonych za pośrednictwem modelu wdrażania Menedżer zasobów. Jeśli chcesz oznaczyć maszynę wirtualną za pomocą interfejsu wiersza polecenia platformy Azure, zobacz [jak oznaczyć maszynę wirtualną na platformie Azure przy użyciu interfejsu wiersza polecenia platformy Azure](../linux/tag.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

[!INCLUDE [virtual-machines-common-tag](../../../includes/virtual-machines-common-tag.md)]

## <a name="tagging-with-powershell"></a>Tagowanie przy użyciu programu PowerShell
Aby tworzyć, dodawać i usuwać Tagi za pomocą programu PowerShell, należy najpierw skonfigurować [środowisko programu PowerShell przy użyciu Azure Resource Manager][PowerShell environment with Azure Resource Manager]. Po zakończeniu instalacji możesz umieścić znaczniki w zasobach obliczeniowych, sieci i magazynu podczas tworzenia lub po utworzeniu zasobu za pośrednictwem programu PowerShell. Ten artykuł koncentruje się na wyświetlaniu i edytowaniu tagów umieszczonych w Virtual Machines.

 

Najpierw przejdź do maszyny wirtualnej za pomocą `Get-AzVM` polecenia cmdlet.

```azurepowershell
PS C:\> Get-AzVM -ResourceGroupName "MyResourceGroup" -Name "MyTestVM"
```

Jeśli maszyna wirtualna zawiera już Tagi, zobaczysz wszystkie Tagi w zasobie:

```json
Tags : {
        "Application": "MyApp1",
        "Created By": "MyName",
        "Department": "MyDepartment",
        "Environment": "Production"
        }
```

Jeśli chcesz dodać tagi za pomocą programu PowerShell, możesz użyć `Set-AzResource` polecenia. Uwaga w przypadku aktualizowania tagów za pomocą programu PowerShell Tagi są aktualizowane jako całość. Dlatego jeśli dodajesz jeden tag do zasobu, który ma już Tagi, musisz uwzględnić wszystkie Tagi, które mają zostać umieszczone w zasobie. Poniżej znajduje się przykład dodawania dodatkowych tagów do zasobu za pomocą poleceń cmdlet programu PowerShell.

To pierwsze polecenie cmdlet ustawia wszystkie znaczniki umieszczone w *MyTestVM* do zmiennej *$Tags* przy użyciu `Get-AzResource` `Tags` właściwości i.

```azurepowershell
PS C:\> $tags = (Get-AzResource -ResourceGroupName MyResourceGroup -Name MyTestVM).Tags
```

Drugie polecenie wyświetla Tagi dla danej zmiennej.

```azurepowershell
PS C:\> $tags

Key           Value
----          -----
Department    MyDepartment
Application   MyApp1
Created By    MyName
Environment   Production
```

Trzecie polecenie dodaje dodatkowy tag do zmiennej *$Tags* . Zwróć uwagę na użycie, **+=** Aby dołączyć nową parę klucz/wartość do listy *$Tags* .

```azurepowershell
PS C:\> $tags += @{Location="MyLocation"}
```

Czwarte polecenie ustawia wszystkie Tagi zdefiniowane w zmiennej *$Tags* dla danego zasobu. W tym przypadku jest to MyTestVM.

```azurepowershell
PS C:\> Set-AzResource -ResourceGroupName MyResourceGroup -Name MyTestVM -ResourceType "Microsoft.Compute/VirtualMachines" -Tag $tags
```

Piąte polecenie wyświetla wszystkie Tagi w zasobie. Jak widać, *Lokalizacja* jest teraz definiowana jako tag z elementem *weblocation* jako wartość.

```azurepowershell
PS C:\> (Get-AzResource -ResourceGroupName MyResourceGroup -Name MyTestVM).Tags

Key           Value
----          -----
Department    MyDepartment
Application   MyApp1
Created By    MyName
Environment   Production
Location      MyLocation
```

Aby dowiedzieć się więcej na temat tagowania za pomocą programu PowerShell, zapoznaj się z [poleceniami cmdlet zasobów platformy Azure][Azure Resource Cmdlets].

[!INCLUDE [virtual-machines-common-tag-usage](../../../includes/virtual-machines-common-tag-usage.md)]

## <a name="next-steps"></a>Następne kroki
* Aby dowiedzieć się więcej o znakowaniu zasobów platformy Azure, zobacz [Azure Resource Manager omówienie][Azure Resource Manager Overview] i [Używanie tagów do organizowania zasobów platformy Azure][Using Tags to organize your Azure Resources].
* Aby dowiedzieć się, jak tagi mogą ułatwić zarządzanie użyciem zasobów platformy Azure, zobacz [Opis rachunku na korzystanie z platformy Azure][Understanding your Azure Bill] i [Uzyskiwanie szczegółowych informacji o zużyciu zasobów Microsoft Azure][Gain insights into your Microsoft Azure resource consumption].

[PowerShell environment with Azure Resource Manager]: ../../azure-resource-manager/management/manage-resources-powershell.md
[Azure Resource Cmdlets]: /powershell/module/az.resources/
[Azure Resource Manager Overview]: ../../azure-resource-manager/management/overview.md
[Using Tags to organize your Azure Resources]: ../../azure-resource-manager/management/tag-resources.md
[Understanding your Azure Bill]:../../cost-management-billing/understand/review-individual-bill.md
[Gain insights into your Microsoft Azure resource consumption]:../../cost-management-billing/manage/usage-rate-card-overview.md
