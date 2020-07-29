---
title: Wycofywanie klasycznych maszyn wirtualnych platformy Azure w dniu 1 marca 2023
description: Artykuł zawiera ogólne omówienie klasycznej emerytury maszyny wirtualnej
author: tanmaygore
manager: vashan
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 02/10/2020
ms.author: tagore
ms.openlocfilehash: 6ddc9299f72e8a0b25e8b1f85968933bd6868af8
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/28/2020
ms.locfileid: "87288543"
---
# <a name="migrate-your-iaas-resources-to-azure-resource-manager-by-march-1-2023"></a>Migrowanie zasobów IaaS do Azure Resource Manager 1 marca 2023 

W 2014 uruchomiono IaaS na Azure Resource Manager i dodaliśmy możliwości kiedykolwiek od. Ponieważ [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/) ma teraz pełnych możliwości IaaS i innych zaliczeń, firma Microsoft zakończyła zarządzanie maszynami wirtualnymi IaaS za pośrednictwem platformy Azure Service Manager 28 lutego 2020. Ta funkcja zostanie całkowicie wycofana 1 marca 2023. 

Obecnie około 90% maszyn wirtualnych IaaS korzysta z Azure Resource Manager. Jeśli używasz zasobów IaaS za pomocą usługi Azure Service Manager (ASM), Rozpocznij Planowanie migracji teraz i uzupełnij ją do 1 marca 2023, aby skorzystać z [Azure Resource Manager](../azure-resource-manager/management/index.yml).

Klasyczne maszyny wirtualne będą miały następujące [nowoczesne zasady cyklu życia](https://support.microsoft.com/help/30881/modern-lifecycle-policy) dla wycofania.

## <a name="how-does-this-affect-me"></a>Jak to wpłynie na mnie? 

1) Od 28 lutego 2020 Klienci, którzy nie korzystali z maszyn wirtualnych IaaS za pośrednictwem platformy Azure Service Manager (ASM) w miesiącu lutego 2020, nie będą już mogli tworzyć klasycznych maszyn wirtualnych. 
2) 1 marca 2023 klienci nie będą już mogli uruchamiać IaaS maszyn wirtualnych przy użyciu usługi Azure Service Manager, a wszystkie te, które nadal działają lub są przydzieleni, zostaną zatrzymane i cofnięte alokacje. 
2) 1 marca 2023, subskrypcje, które nie zostały zmigrowane do Azure Resource Manager, zostaną poinformowane o osiach czasu do usuwania pozostałych klasycznych maszyn wirtualnych.  

Ta wycofanie **nie** wpłynie na następujące usługi i funkcje platformy Azure: 
- Cloud Services 
- **Konta magazynu** nieużywane przez klasyczne maszyny wirtualne 
- Sieci wirtualne (sieci wirtualnych) **nie** są używane przez klasyczne maszyny wirtualne. 
- Inne zasoby klasyczne

## <a name="what-actions-should-i-take"></a>Jakie akcje należy wykonać? 

- Rozpocznij Planowanie migracji do Azure Resource Manager, dzisiaj. 

- [Dowiedz się więcej](./windows/migration-classic-resource-manager-overview.md) na temat migrowania klasycznych maszyn wirtualnych z systemem [Linux](./linux/migration-classic-resource-manager-plan.md) i [Windows](./windows/migration-classic-resource-manager-plan.md) do Azure Resource Manager.

- Aby uzyskać więcej informacji, zapoznaj się z [często zadawanymi pytaniami dotyczącymi migracji klasycznej do Azure Resource Manager](./windows/migration-classic-resource-manager-faq.md) .

- Aby uzyskać pytania techniczne, problemy i Dodawanie subskrypcji do listy dozwolonych, [skontaktuj się z pomocą techniczną](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

- Aby uzyskać inne pytania, które nie są częścią często zadawanych pytań i opinii, Skomentuj poniżej.
