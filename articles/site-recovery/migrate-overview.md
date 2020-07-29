---
title: Migrowanie serwerów i maszyn wirtualnych na platformę Azure za pomocą Azure Site Recovery
description: Opisuje sposób migrowania maszyn wirtualnych lokalnych i Azure IaaS na platformę Azure przy użyciu usługi Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 07/27/2020
ms.author: raynew
ms.openlocfilehash: aaf01dcd63c21e4741456f4f7fccaf22b8fbfffc
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/28/2020
ms.locfileid: "87281314"
---
# <a name="about-migration"></a>Informacje o migracji

Usługa Azure Migrate służy do migrowania maszyn wirtualnych i serwerów na platformę Azure, a nie usługi Azure Site Recovery. [Dowiedz się więcej](../migrate/migrate-services-overview.md) o Azure Migrate.


## <a name="why-use-azure-migrate"></a>Dlaczego warto używać usługi Azure Migrate?

Używanie Azure Migrate do migracji zapewnia wiele zalet:
 
 
- Azure Migrate udostępnia scentralizowany centrum do odnajdywania, oceny i migracji do platformy Azure.
- Używanie Azure Migrate zapewnia współdziałanie i przyszłą rozszerzalność przy użyciu narzędzi Azure Migrate, innych usług platformy Azure i narzędzi innych firm.
- Azure Migrate: Narzędzie migracji serwera jest przeznaczone do migracji serwera na platformę Azure. Jest zoptymalizowany pod kątem migracji. Nie musisz uczyć się o pojęciach i scenariuszach, które nie są bezpośrednio związane z migracją. 
- Nie są naliczane opłaty za użycie narzędzi do migracji przez 180 dni od momentu uruchomienia replikacji dla maszyny wirtualnej. Zapewnia to czas na ukończenie migracji. Płacisz tylko za zasoby magazynowe i sieciowe używane podczas replikacji oraz opłaty za obliczenia używane podczas migracji testowej.
- W przypadku maszyn wirtualnych VMware Azure Migrate zapewnia migrację bez wykorzystania agentów, a także migrację opartą na agentach.
- Są dostępne priorytety nowych funkcji migracji dla Azure Migrate: tylko narzędzie migracji serwera.

## <a name="when-to-use-site-recovery"></a>Kiedy używać Site Recovery?

Należy używać Site Recovery:

- Odzyskiwanie po awarii maszyn lokalnych na platformę Azure.
- Odzyskiwanie po awarii maszyn wirtualnych platformy Azure między regionami platformy Azure.

Chociaż zalecamy używanie Azure Migrate do migrowania serwerów lokalnych na platformę Azure, jeśli rozpoczęto już przebieg migracji przy użyciu Site Recovery, można kontynuować korzystanie z niej w celu przeprowadzenia migracji.  

## <a name="next-steps"></a>Następne kroki

> Zapoznaj się z [typowymi pytaniami](../migrate/resources-faq.md) dotyczącymi Azure Migrate.
