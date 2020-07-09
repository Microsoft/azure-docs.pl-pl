---
title: Migrowanie serwerów i maszyn wirtualnych na platformę Azure za pomocą Azure Site Recovery
description: Opisuje sposób migrowania maszyn wirtualnych lokalnych i Azure IaaS na platformę Azure przy użyciu usługi Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: raynew
ms.openlocfilehash: f42942d0e112aad147f78d62b5f5b43587eca9b3
ms.sourcegitcommit: e995f770a0182a93c4e664e60c025e5ba66d6a45
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/08/2020
ms.locfileid: "86135596"
---
# <a name="about-migration"></a>Informacje o migracji

Przeczytaj ten artykuł, aby zapoznać się z krótkim omówieniem, w jaki sposób usługa [Azure Site Recovery](site-recovery-overview.md) ułatwia Migrowanie maszyn. 

> [!TIP]
> Teraz należy używać usługi Azure Migrate do migrowania maszyn wirtualnych i serwerów na platformę Azure, a nie usługi Azure Site Recovery. [Dowiedz się więcej](../migrate/migrate-services-overview.md).


Oto, co można migrować za pomocą Site Recovery:

- **Migrowanie z lokalnego na platformę Azure**: Migrowanie lokalnych maszyn wirtualnych funkcji Hyper-V, maszyn wirtualnych VMware i serwerów fizycznych na platformę Azure. Po zakończeniu migracji obciążenia uruchomione na maszynach lokalnych będą działać na maszynach wirtualnych platformy Azure. 
- **Przeprowadzić migrację na platformie Azure**: przeprowadź migrację maszyn wirtualnych platformy Azure między regionami świadczenia usługi Azure. 
- **Przeprowadzić migrację usługi AWS**: przeprowadź migrację wystąpień usługi AWS dla systemu Windows do maszyn wirtualnych IaaS platformy Azure. 

> [!NOTE]
> Teraz można migrować z lokalizacji lokalnej na platformę Azure przy użyciu usługi Azure Migrate. [Dowiedz się więcej](../migrate/migrate-services-overview.md).

## <a name="what-do-we-mean-by-migration"></a>Co mamy na myśli przez migrację?

Oprócz korzystania z Site Recovery na potrzeby odzyskiwania po awarii lokalnych i maszyn wirtualnych platformy Azure można przeprowadzić migrację za pomocą usługi Site Recovery. Na czym polega różnica?

- W przypadku odzyskiwania po awarii maszyny są replikowane regularnie na platformie Azure. Gdy wystąpi awaria, maszyny przechodzą w tryb failover z lokacji głównej do pomocniczej lokacji platformy Azure i uzyskują do nich dostęp. Gdy lokacja główna będzie znowu dostępna, powrót po awarii z platformy Azure zostanie zakończony pomyślnie.
- W przypadku migracji maszyny lokalne są replikowane na platformę Azure lub maszyny wirtualne platformy Azure do regionu pomocniczego. Następnie można przenieść maszynę wirtualną w tryb failover z lokacji głównej do pomocniczej i ukończyć proces migracji. Nie ma powrotu po awarii.  


## <a name="migration-scenarios"></a>Scenariusze migracji

**Scenariusz** | **Szczegóły**
--- | ---
**Migrowanie z zasobów lokalnych na platformę Azure** | Lokalne maszyny wirtualne programu VMware, maszyny wirtualne funkcji Hyper-V i serwery fizyczne można migrować do platformy Azure. W tym celu należy wykonać niemal te same czynności co w przypadku pełnego odzyskiwania po awarii. Po prostu nie można z powrotem kończyć maszyn z platformy Azure do lokacji lokalnej.
**Migrowanie między regionami platformy Azure** | Maszyny wirtualne platformy Azure można migrować z jednego regionu platformy Azure do innego. Po zakończeniu migracji możesz teraz skonfigurować odzyskiwanie po awarii dla maszyn wirtualnych platformy Azure w regionie pomocniczym, do którego została zmigrowana.
**Migracja usługi AWS na platformę Azure** | Możesz zmigrować wystąpienia usługi AWS na maszyny wirtualne platformy Azure. Site Recovery traktuje wystąpienia AWS jako serwery fizyczne do celów migracji. 

## <a name="next-steps"></a>Następne kroki

- [Migrowanie maszyn lokalnych na platformę Azure](migrate-tutorial-on-premises-azure.md)
- [Migrowanie maszyn wirtualnych między regionami świadczenia usługi Azure](azure-to-azure-tutorial-migrate.md)
- [Migracja usługi AWS na platformę Azure](migrate-tutorial-aws-azure.md)
