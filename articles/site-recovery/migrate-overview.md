---
title: Porównanie Azure Migrate i Site Recovery migracji na platformę Azure
description: Podsumowuje zalety używania Azure Migrate do migracji, a nie Site Recovery.
services: site-recovery
ms.service: site-recovery
ms.topic: conceptual
ms.date: 08/06/2020
ms.openlocfilehash: c4d30b8b21bf3a0ea27467d2120b04a107f5a73f
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/07/2021
ms.locfileid: "106581415"
---
# <a name="migrating-to-azure"></a>Migracja na platformę Azure

W przypadku migracji zalecamy użycie usługi Azure Migrate do migrowania maszyn wirtualnych i serwerów na platformę Azure, a nie usługi Azure Site Recovery. [Dowiedz się więcej](../migrate/migrate-services-overview.md) o Azure Migrate.


## <a name="why-use-azure-migrate"></a>Dlaczego warto używać usługi Azure Migrate?

Używanie Azure Migrate do migracji zapewnia wiele zalet:
 
 
- Azure Migrate udostępnia scentralizowany centrum do odnajdywania, oceny i migracji do platformy Azure.
- Używanie Azure Migrate zapewnia współdziałanie i przyszłą rozszerzalność przy użyciu narzędzi Azure Migrate, innych usług platformy Azure i narzędzi innych firm.
- Azure Migrate: Narzędzie migracji serwera jest przeznaczone do migracji serwera na platformę Azure. Jest zoptymalizowany pod kątem migracji. Nie musisz uczyć się o pojęciach i scenariuszach, które nie są bezpośrednio związane z migracją. 
- Nie są naliczane opłaty za użycie narzędzi do migracji przez 180 dni od momentu uruchomienia replikacji dla maszyny wirtualnej. Zapewnia to czas na ukończenie migracji. Płacisz tylko za zasoby magazynowe i sieciowe używane podczas replikacji oraz opłaty za obliczenia używane podczas migracji testowej.
- Azure Migrate obsługuje wszystkie scenariusze migracji obsługiwane przez Site Recovery. Ponadto w przypadku maszyn wirtualnych VMware Azure Migrate zapewnia opcję migracji bez wykorzystania agentów.
- Są dostępne priorytety nowych funkcji migracji dla Azure Migrate: tylko narzędzie migracji serwera. Te funkcje nie są przeznaczone do Site Recovery.

## <a name="when-to-use-site-recovery"></a>Kiedy używać Site Recovery?

Należy używać Site Recovery:

- Odzyskiwanie po awarii maszyn lokalnych na platformę Azure.
- Odzyskiwanie po awarii maszyn wirtualnych platformy Azure między regionami platformy Azure.

Chociaż zalecamy używanie Azure Migrate do migrowania serwerów lokalnych na platformę Azure, jeśli rozpoczęto już przebieg migracji przy użyciu Site Recovery, można kontynuować korzystanie z niej w celu przeprowadzenia migracji.  

## <a name="next-steps"></a>Następne kroki

> Zapoznaj się z [typowymi pytaniami](../migrate/resources-faq.md) dotyczącymi Azure Migrate.
