---
title: Migrowanie maszyn lokalnych przy użyciu Azure Migrate
description: W tym artykule opisano sposób migrowania maszyn lokalnych na platformę Azure i zaleca się Azure Migrate.
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 07/27/2020
ms.author: raynew
ms.openlocfilehash: e0e60ee346d20113b2ec7970390d9874522cc770
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87847316"
---
# <a name="migrate-on-premises-machines-to-azure"></a>Migrowanie maszyn lokalnych do platformy Azure

W tym artykule opisano opcje migrowania maszyn lokalnych na platformę Azure. 

## <a name="migrate-with-azure-migrate"></a>Migrowanie z Azure Migrate

Zalecamy Migrowanie maszyn na platformę Azure przy użyciu usługi [Azure Migrate](../migrate/migrate-services-overview.md) . Azure Migrate jest przeznaczony dla migracji serwera. Azure Migrate udostępnia scentralizowany centrum do odnajdywania, oceniania i migracji maszyn lokalnych na platformę Azure.

Wykonaj następujące linki, aby przeprowadzić migrację za pomocą Azure Migrate:

- [Dowiedz się więcej o](../migrate/server-migrate-overview.md) opcjach migracji maszyn wirtualnych VMware, a następnie Migruj maszyny wirtualne na platformę Azure z migracją [bez agenta](../migrate/tutorial-migrate-vmware.md) lub z [użyciem agentów](../migrate/tutorial-migrate-vmware-agent.md) .
- [Migrowanie maszyn wirtualnych funkcji Hyper-V](../migrate/tutorial-migrate-hyper-v.md) do platformy Azure.
- Migrowanie [serwerów fizycznych lub innych maszyn wirtualnych](../migrate/tutorial-migrate-physical-virtual-machines.md), w tym [wystąpień AWS](../migrate/tutorial-migrate-aws-virtual-machines.md) do platformy Azure.

## <a name="migrate-with-site-recovery"></a>Migrowanie z Site Recovery
Site Recovery należy używać tylko w przypadku odzyskiwania po awarii, a nie migracji.

Jeśli używasz już Azure Site Recovery i chcesz nadal używać go do migracji, wykonaj te same czynności, które są używane na potrzeby odzyskiwania po awarii.

- Maszyny wirtualne VMware: [Przygotuj platformę Azure](tutorial-prepare-azure.md) i [oprogramowanie VMware](vmware-azure-tutorial-prepare-on-premises.md), uruchom [replikację maszyn](vmware-azure-tutorial.md), [Sprawdź](tutorial-dr-drill-azure.md) , czy wszystko działa, i [Uruchom tryb failover](vmware-azure-tutorial-failover-failback.md).
- Maszyny wirtualne funkcji Hyper-V: [Przygotuj platformę Azure](tutorial-prepare-azure-for-hyperv.md) i [funkcję Hyper-v](hyper-v-prepare-on-premises-tutorial.md), uruchom [replikację maszyn](hyper-v-azure-tutorial.md), [Sprawdź](tutorial-dr-drill-azure.md) , czy wszystko działa, i [Uruchom tryb failover](hyper-v-azure-failover-failback-tutorial.md).
- Serwery fizyczne: [postępuj zgodnie z przewodnikiem](physical-azure-disaster-recovery.md) , aby przygotować platformę Azure, przygotować maszyny do odzyskiwania po awarii i skonfigurować replikację.

> [!NOTE]
> Po uruchomieniu trybu failover na potrzeby odzyskiwania po awarii, ostatnim krokiem jest zatwierdzenie trybu failover. W przypadku migrowania maszyn lokalnych opcja **zatwierdzania** nie ma znaczenia. Zamiast tego należy wybrać opcję **pełna migracja** . 

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> Zapoznaj się z [typowymi pytaniami](../migrate/resources-faq.md) dotyczącymi Azure Migrate.

  
