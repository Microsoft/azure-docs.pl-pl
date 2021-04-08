---
title: Jak migrować API Management platformy Azure między regionami
description: Dowiedz się, jak migrować wystąpienie API Management z jednego regionu do innego.
services: api-management
documentationcenter: ''
author: miaojiang
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/26/2019
ms.author: apimpm
ms.openlocfilehash: 0eed2328aca78402c5f4691bb9b3d07d4f36472e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "86250230"
---
# <a name="how-to-migrate-azure-api-management-across-regions"></a>Jak migrować API Management platformy Azure między regionami
Aby migrować wystąpienia API Management z jednego regionu świadczenia usługi Azure do innego, można użyć funkcji [tworzenia kopii zapasowych i przywracania](api-management-howto-disaster-recovery-backup-restore.md) . Należy wybrać tę samą API Management warstwę cenową w regionach źródłowym i docelowym. 

> [!NOTE]
> Tworzenie kopii zapasowych i przywracanie nie będzie funkcjonować podczas migrowania między różnymi typami chmur. W tym celu należy wyeksportować zasób [jako szablon](../azure-resource-manager/management/manage-resource-groups-portal.md#export-resource-groups-to-templates). Następnie dostosuj wyeksportowany szablon dla docelowego regionu platformy Azure i ponownie utwórz zasób. 

## <a name="option-1-use-a-different-api-management-instance-name"></a>Opcja 1. Użyj innej nazwy wystąpienia API Management

1. W regionie docelowym Utwórz nowe wystąpienie API Management z tą samą warstwą cenową co wystąpienie API Management źródłowej. Nowe wystąpienie powinno mieć inną nazwę. 
1. Utwórz kopię zapasową istniejącego wystąpienia API Management na koncie magazynu.
1. Przywróć kopię zapasową utworzoną w kroku 2 do nowego wystąpienia API Management utworzonego w nowym regionie w kroku 1.
1. Jeśli masz domenę niestandardową wskazującą region źródłowy API Management wystąpienia, zaktualizuj domenę niestandardową CNAME, aby wskazywała nowe wystąpienie API Management. 


## <a name="option-2-use-the-same-api-management-instance-name"></a>Opcja 2: Użyj tej samej nazwy wystąpienia API Management

> [!NOTE]
> Ta opcja spowoduje przestoje podczas migracji.

1. Wykonaj kopię zapasową wystąpienia API Management w regionie źródłowym na koncie magazynu.
1. Usuń API Management w regionie źródłowym. 
1. Utwórz nowe wystąpienie API Management w regionie docelowym o tej samej nazwie co w regionie źródłowym.
1. Przywróć kopię zapasową utworzoną w kroku 1 do nowego wystąpienia API Management w regionie docelowym.  


## <a name="next-steps"></a><a name="next-steps"> </a>Następne kroki
* Aby uzyskać więcej informacji na temat funkcji tworzenia kopii zapasowych i przywracania, zobacz [jak zaimplementować odzyskiwanie po awarii](api-management-howto-disaster-recovery-backup-restore.md).
* Aby uzyskać informacje na temat migracji zasobów platformy Azure, zobacz [wskazówki dotyczące migracji między regionami na platformie Azure](https://github.com/Azure/Azure-Migration-Guidance).
* [Zoptymalizuj i oszczędzaj wydatki na chmurę](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
