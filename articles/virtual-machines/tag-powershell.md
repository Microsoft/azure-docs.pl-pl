---
title: Jak oznaczyć maszynę wirtualną przy użyciu programu PowerShell
description: Dowiedz się więcej na temat tagowania maszyny wirtualnej przy użyciu programu PowerShell
author: cynthn
ms.service: virtual-machines
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 11/11/2020
ms.author: cynthn
ms.openlocfilehash: bf13d5c0caeb0bf31a383cd23155a6856c81c53b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98897395"
---
# <a name="how-to-tag-a-virtual-machine-in-azure-using-powershell"></a>Jak oznaczyć maszynę wirtualną na platformie Azure przy użyciu programu PowerShell

W tym artykule opisano, jak oznaczyć maszynę wirtualną na platformie Azure przy użyciu programu PowerShell. Tagi to zdefiniowane przez użytkownika pary klucz/wartość, które mogą być umieszczone bezpośrednio w ramach zasobu lub grupy zasobów. Platforma Azure obsługuje obecnie do 50 tagów na zasób i grupę zasobów. Tagi mogą być umieszczane na zasobie w momencie tworzenia lub dodawane do istniejącego zasobu. Jeśli chcesz oznaczyć maszynę wirtualną za pomocą interfejsu wiersza polecenia platformy Azure, zobacz [jak oznaczyć maszynę wirtualną na platformie Azure przy użyciu interfejsu wiersza polecenia platformy Azure](tag-cli.md).

Użyj `Get-AzVM` polecenia cmdlet, aby wyświetlić bieżącą listę tagów dla maszyny wirtualnej.

```azurepowershell-interactive
Get-AzVM -ResourceGroupName "myResourceGroup" -Name "myVM" | Format-List -Property Tags
```

Jeśli maszyna wirtualna już zawiera Tagi, zobaczysz wszystkie Tagi w formacie listy.

Aby dodać tagi, użyj `Set-AzResource` polecenia. W przypadku aktualizowania tagów za pomocą programu PowerShell Tagi są aktualizowane jako całość. Jeśli dodajesz jeden tag do zasobu, który ma już Tagi, musisz uwzględnić wszystkie Tagi, które mają zostać umieszczone w zasobie. Poniżej znajduje się przykład dodawania dodatkowych tagów do zasobu za pomocą poleceń cmdlet programu PowerShell.

Przypisz wszystkie bieżące Tagi dla maszyny wirtualnej do `$tags` zmiennej przy użyciu `Get-AzResource` `Tags` właściwości i.

```azurepowershell-interactive
$tags = (Get-AzResource -ResourceGroupName myResourceGroup -Name myVM).Tags
```

Aby wyświetlić bieżące Tagi, wpisz zmienną.

```azurepowershell-interactive
$tags
```

Oto dane wyjściowe, które mogą wyglądać następująco:

```output
Key           Value
----          -----
Department    MyDepartment
Application   MyApp1
Created By    MyName
Environment   Production
```

W poniższym przykładzie dodamy tag o nazwie `Location` z wartością `myLocation` . Służy `+=` do dołączania nowej pary klucz/wartość do `$tags` listy.

```azurepowershell-interactive
$tags += @{Location="myLocation"}
```

Użyj `Set-AzResource` , aby ustawić wszystkie Tagi zdefiniowane w zmiennej *$Tags* na maszynie wirtualnej.

```azurepowershell-interactive
Set-AzResource -ResourceGroupName myResourceGroup -Name myVM -ResourceType "Microsoft.Compute/VirtualMachines" -Tag $tags
```

Służy `Get-AzResource` do wyświetlania wszystkich tagów w zasobie.

```azurepowershell-interactive
(Get-AzResource -ResourceGroupName myResourceGroup -Name myVM).Tags

```

Dane wyjściowe powinny wyglądać podobnie do poniższego, który zawiera teraz nowy tag:

```output

Key           Value
----          -----
Department    MyDepartment
Application   MyApp1
Created By    MyName
Environment   Production
Location      MyLocation
```

### <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się więcej o znakowaniu zasobów platformy Azure, zobacz [Azure Resource Manager omówienie](../azure-resource-manager/management/overview.md) i [Używanie tagów do organizowania zasobów platformy Azure](../azure-resource-manager/management/tag-resources.md).
- Aby dowiedzieć się, jak tagi mogą ułatwić zarządzanie użyciem zasobów platformy Azure, zobacz [Opis rachunku na korzystanie z platformy Azure](../cost-management-billing/understand/review-individual-bill.md).
