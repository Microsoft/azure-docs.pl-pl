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
ms.openlocfilehash: 16ca4ad1204301542ffaa0177d88b2c2800f13b4
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/06/2020
ms.locfileid: "87836279"
---
# <a name="migrate-your-iaas-resources-to-azure-resource-manager-by-march-1-2023"></a>Migrowanie zasobów IaaS do Azure Resource Manager 1 marca 2023 

W 2014 uruchomiono IaaS na Azure Resource Manager i dodaliśmy możliwości kiedykolwiek od. Ponieważ [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/) ma teraz pełnych możliwości IaaS i innych zaliczeń, firma Microsoft zakończyła zarządzanie maszynami wirtualnymi IaaS za pośrednictwem [platformy Azure Service Manager](https://docs.microsoft.com/azure/virtual-machines/windows/migration-classic-resource-manager-faq#what-is-azure-service-manager-and-what-does-it-mean-by-classic) 28 lutego 2020. Ta funkcja zostanie całkowicie wycofana 1 marca 2023. 

Obecnie około 90% maszyn wirtualnych IaaS korzysta z Azure Resource Manager. Jeśli używasz zasobów IaaS za pomocą usługi Azure Service Manager (ASM), Rozpocznij Planowanie migracji teraz i uzupełnij ją do 1 marca 2023, aby skorzystać z [Azure Resource Manager](../azure-resource-manager/management/index.yml).

Klasyczne maszyny wirtualne będą miały następujące [nowoczesne zasady cyklu życia](https://support.microsoft.com/help/30881/modern-lifecycle-policy) dla wycofania.

## <a name="how-does-this-affect-me"></a>Jak to wpłynie na mnie? 

- Od 28 lutego 2020 Klienci, którzy nie korzystali z maszyn wirtualnych IaaS za pośrednictwem platformy Azure Service Manager (ASM) w miesiącu lutego 2020, nie będą już mogli tworzyć klasycznych maszyn wirtualnych. 
- 1 marca 2023 klienci nie będą już mogli uruchamiać IaaS maszyn wirtualnych przy użyciu usługi Azure Service Manager, a wszystkie te, które nadal działają lub są przydzieleni, zostaną zatrzymane i cofnięte alokacje. 
- 1 marca 2023, subskrypcje, które nie zostały zmigrowane do Azure Resource Manager, będą otrzymywać informacje dotyczące osi czasu na potrzeby usuwania pozostałych klasycznych maszyn wirtualnych.  

Ta wycofanie **nie** wpłynie na następujące usługi i funkcje platformy Azure: 
- Cloud Services 
- **Konta magazynu** nieużywane przez klasyczne maszyny wirtualne 
- Sieci wirtualne (sieci wirtualnych) **nie** są używane przez klasyczne maszyny wirtualne. 
- Inne zasoby klasyczne

## <a name="what-actions-should-i-take"></a>Jakie akcje należy wykonać? 

- Rozpocznij Planowanie migracji do Azure Resource Manager, dzisiaj. 

- [Dowiedz się więcej](./windows/migration-classic-resource-manager-overview.md) na temat migrowania klasycznych maszyn wirtualnych z systemem [Linux](./linux/migration-classic-resource-manager-plan.md) i [Windows](./windows/migration-classic-resource-manager-plan.md) do Azure Resource Manager.

- Aby uzyskać więcej informacji, zapoznaj się z [często zadawanymi pytaniami dotyczącymi migracji klasycznej do Azure Resource Manager](./windows/migration-classic-resource-manager-faq.md) .

- Aby uzyskać pytania techniczne, problemy i Dodawanie subskrypcji do listy dozwolonych, [skontaktuj się z pomocą techniczną](https://ms.portal.azure.com/#create/Microsoft.Support/Parameters/{"pesId":"6f16735c-b0ae-b275-ad3a-03479cfa1396","supportTopicId":"8a82f77d-c3ab-7b08-d915-776b4ff64ff4"}).

- Aby uzyskać inne pytania, które nie są częścią często zadawanych pytań i opinii, Skomentuj poniżej.

- Dokończ migrację najszybciej, jak to możliwe, aby zapobiec wpływowi na działalność biznesową i zwiększyć wydajność, & nowe funkcje zapewniane przez Azure Resource Manager. 

## <a name="what-resources-are-provided-to-me-for-this-migration"></a>Jakie zasoby są udostępniane dla tej migracji?

- [Microsoft Q&A](https://docs.microsoft.com/answers/topics/azure-virtual-machines-migration.html): Pomoc techniczna firmy Microsoft & Community na potrzeby migracji

- [Obsługa migracji platformy Azure](https://ms.portal.azure.com/#create/Microsoft.Support/Parameters/{"pesId":"6f16735c-b0ae-b275-ad3a-03479cfa1396","supportTopicId":"1135e3d0-20e2-aec5-4ef0-55fd3dae2d58"}): dedykowany zespół pomocy technicznej w zakresie pomocy technicznej podczas migracji

- [Microsoft Fast](https://www.microsoft.com/fasttrack)Track: zespół Microsoft Fast Track może zapewnić pomoc techniczną podczas migracji do kwalifikujących się klientów. 

- Jeśli Twoja firma/organizacja współpracuje z firmą Microsoft i/lub pracują z przedstawicielem firmy Microsoft, takim jak partner rozwiązań w chmurze (CSA), menedżerowie ds. kont technicznych (TAMs)), Pracuj z nimi w celu uzyskania dodatkowych zasobów na potrzeby migracji. 

