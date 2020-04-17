---
title: Jak oznaczyć zasób maszyny Wirtualnej systemu Windows na platformie Azure
description: Dowiedz się więcej o oznaczaniu maszyny wirtualnej systemu Windows utworzonej na platformie Azure przy użyciu modelu wdrażania usługi Resource Manager
services: virtual-machines-windows
documentationcenter: ''
author: mmccrory
manager: gwallace
tags: azure-resource-manager
ms.assetid: 56d17f45-e4a7-4d84-8022-b40334ae49d2
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 07/05/2016
ms.author: memccror
ms.openlocfilehash: 8f95c11f93ca2075eb2472ad5bb7360df7d69234
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81456451"
---
# <a name="how-to-tag-a-windows-virtual-machine-in-azure"></a>Jak oznaczyć maszynę wirtualną systemu Windows na platformie Azure
W tym artykule opisano różne sposoby oznaczania maszyny wirtualnej systemu Windows na platformie Azure za pośrednictwem modelu wdrażania Usługi Resource Manager. Tagi są parami kluczy/wartości zdefiniowanych przez użytkownika, które można umieścić bezpośrednio w zasobie lub grupie zasobów. Platforma Azure obsługuje obecnie do 50 tagów na grupę zasobów i zasobów. Tagi mogą być umieszczane w zasobie w momencie tworzenia lub dodawane do istniejącego zasobu. Należy pamiętać, że tagi są obsługiwane dla zasobów utworzonych tylko za pośrednictwem modelu wdrażania Menedżera zasobów. Jeśli chcesz oznaczyć maszynę wirtualną systemu Linux, zobacz [Jak oznaczyć maszynę wirtualną systemu Linux na platformie Azure](../linux/tag.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

[!INCLUDE [virtual-machines-common-tag](../../../includes/virtual-machines-common-tag.md)]

## <a name="tagging-with-powershell"></a>Oznaczanie za pomocą programu PowerShell
Aby tworzyć, dodawać i usuwać znaczniki za pośrednictwem programu PowerShell, należy najpierw skonfigurować [środowisko programu PowerShell][PowerShell environment with Azure Resource Manager]za pomocą usługi Azure Resource Manager . Po zakończeniu instalacji można umieszczać znaczniki na zasobach obliczeniowych, sieciowych i magazynowych podczas tworzenia lub po utworzeniu zasobu za pośrednictwem programu PowerShell. W tym artykule skupimy się na wyświetlaniu/edytowaniu tagów umieszczonych na maszynach wirtualnych.

 

Najpierw przejdź do maszyny wirtualnej za pośrednictwem polecenia `Get-AzVM` cmdlet.

        PS C:\> Get-AzVM -ResourceGroupName "MyResourceGroup" -Name "MyTestVM"

Jeśli maszyna wirtualna zawiera już tagi, zobaczysz wszystkie znaczniki na zasobie:

        Tags : {
                "Application": "MyApp1",
                "Created By": "MyName",
                "Department": "MyDepartment",
                "Environment": "Production"
               }

Jeśli chcesz dodać znaczniki za pośrednictwem programu PowerShell, możesz użyć tego `Set-AzResource` polecenia. Uwaga Podczas aktualizowania tagów za pośrednictwem programu PowerShell znaczniki są aktualizowane jako całość. Jeśli więc dodajesz jeden znacznik do zasobu, który ma już znaczniki, musisz dołączyć wszystkie znaczniki, które mają zostać umieszczone w zasobie. Poniżej znajduje się przykład jak dodać dodatkowe tagi do zasobu za pośrednictwem poleceń cmdlet programu PowerShell.

To pierwsze polecenie cmdlet ustawia wszystkie tagi umieszczone na *MyTestVM* `Get-AzResource` do `Tags` *zmiennej $tags,* przy użyciu i właściwości.

        PS C:\> $tags = (Get-AzResource -ResourceGroupName MyResourceGroup -Name MyTestVM).Tags

Drugie polecenie wyświetla znaczniki dla danej zmiennej.

```
    PS C:\> $tags
    
    Key           Value
    ----          -----
    Department    MyDepartment
    Application   MyApp1
    Created By    MyName
    Environment   Production
```

Trzecie polecenie dodaje dodatkowy znacznik do *zmiennej $tags.* Zanotuj **+=** użycie do dokłowania nowej pary klucz/wartość do listy *$tags.*

        PS C:\> $tags += @{Location="MyLocation"}

Czwarte polecenie ustawia wszystkie znaczniki zdefiniowane w *zmiennej $tags* do danego zasobu. W tym przypadku jest to MyTestVM.

        PS C:\> Set-AzResource -ResourceGroupName MyResourceGroup -Name MyTestVM -ResourceType "Microsoft.Compute/VirtualMachines" -Tag $tags

Piąte polecenie wyświetla wszystkie znaczniki na zasobie. Jak widać, *Lokalizacja* jest teraz zdefiniowana jako tag z *MyLocation* jako wartość.

```
    PS C:\> (Get-AzResource -ResourceGroupName MyResourceGroup -Name MyTestVM).Tags

    Key           Value
    ----          -----
    Department    MyDepartment
    Application   MyApp1
    Created By    MyName
    Environment   Production
    Location      MyLocation
```

Aby dowiedzieć się więcej o tagowaniu za pośrednictwem programu PowerShell, zapoznaj się z [poleceniami cmdlet zasobów platformy Azure][Azure Resource Cmdlets].

[!INCLUDE [virtual-machines-common-tag-usage](../../../includes/virtual-machines-common-tag-usage.md)]

## <a name="next-steps"></a>Następne kroki
* Aby dowiedzieć się więcej o oznaczaniu zasobów platformy Azure, zobacz [Omówienie usługi Azure Resource Manager][Azure Resource Manager Overview] i [organizowanie zasobów platformy Azure przy użyciu tagów.][Using Tags to organize your Azure Resources]
* Aby zobaczyć, jak tagi mogą pomóc w zarządzaniu korzystaniem z zasobów platformy Azure, zobacz [Opis rachunku za korzystanie][Understanding your Azure Bill] z platformy Azure i uzyskaj wgląd w zużycie zasobów platformy Microsoft [Azure.][Gain insights into your Microsoft Azure resource consumption]

[PowerShell environment with Azure Resource Manager]: ../../azure-resource-manager/management/manage-resources-powershell.md
[Azure Resource Cmdlets]: https://docs.microsoft.com/powershell/module/az.resources/
[Azure Resource Manager Overview]: ../../azure-resource-manager/management/overview.md
[Using Tags to organize your Azure Resources]: ../../azure-resource-manager/management/tag-resources.md
[Understanding your Azure Bill]:../../cost-management-billing/understand/review-individual-bill.md
[Gain insights into your Microsoft Azure resource consumption]:../../cost-management-billing/manage/usage-rate-card-overview.md
