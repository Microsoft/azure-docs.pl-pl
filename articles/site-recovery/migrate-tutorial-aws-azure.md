---
title: Migrowanie maszyn wirtualnych AWS na platformę Azure za pomocą Azure Migrate
description: W tym artykule opisano opcje migrowania wystąpień AWS na platformę Azure i zalecane Azure Migrate.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 07/27/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 83d8106b9f206da9095c37b6179bdda8001b439c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96009062"
---
# <a name="migrate-amazon-web-services-aws-vms-to-azure"></a>Migrowanie maszyn wirtualnych usług Amazon Web Services (AWS) na platformę Azure

W tym artykule opisano opcje migrowania wystąpień Amazon Web Services (AWS) na platformę Azure.

## <a name="migrate-with-azure-migrate"></a>Migrowanie z Azure Migrate

Zalecamy Migrowanie wystąpień AWS EC2 na platformę Azure przy użyciu usługi [Azure Migrate](../migrate/migrate-services-overview.md) . Azure Migrate jest przeznaczony dla migracji serwera. Azure Migrate udostępnia scentralizowany centrum do odnajdywania, oceniania i migracji maszyn lokalnych na platformę Azure.

[Dowiedz się, jak](../migrate/tutorial-migrate-aws-virtual-machines.md) MIGROWAĆ wystąpienia AWS z Azure Migrate. 


## <a name="migrate-with-site-recovery"></a>Migrowanie z Site Recovery

Site Recovery należy używać tylko w przypadku odzyskiwania po awarii, a nie migracji.

Jeśli używasz już Azure Site Recovery i chcesz nadal używać go do migracji AWS, wykonaj te same czynności, które są używane do konfigurowania [odzyskiwania po awarii maszyn fizycznych](physical-azure-disaster-recovery.md).


> [!NOTE]
> Po uruchomieniu trybu failover na potrzeby odzyskiwania po awarii, ostatnim krokiem jest zatwierdzenie trybu failover. W przypadku migrowania wystąpień AWS opcja **zatwierdzania** nie jest istotna. Zamiast tego należy wybrać opcję **pełna migracja** . 

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> Zapoznaj się z [typowymi pytaniami](../migrate/resources-faq.md) dotyczącymi Azure Migrate.
