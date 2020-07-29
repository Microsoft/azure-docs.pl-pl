---
title: Migrowanie maszyn wirtualnych usług AWS na platformę Azure za pomocą usługi Azure Site Recovery | Microsoft Docs
description: W tym artykule opisano sposób migrowania maszyn wirtualnych z systemem Windows działających w usługach Amazon Web Services (AWS) na platformę Azure za pomocą usługi Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 07/27/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: dd91e99b45405cca10b9ddc2982674e72ad6bf86
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/28/2020
ms.locfileid: "87281297"
---
# <a name="migrate-amazon-web-services-aws-vms-to-azure"></a>Migrowanie maszyn wirtualnych usług Amazon Web Services (AWS) na platformę Azure

W tym artykule opisano opcje migrowania wystąpień Amazon Web Services (AWS) na platformę Azure.

## <a name="migrate-with-azure-migrate"></a>Migrowanie z Azure Migrate

Zalecamy Migrowanie wystąpień AWS na platformę Azure przy użyciu usługi [Azure Migrate](../migrate/migrate-services-overview.md) . Azure Migrate udostępnia scentralizowany centrum do oceny i migracji maszyn lokalnych na platformę Azure przy użyciu Azure Migrate, innych usług platformy Azure i narzędzi innych firm.

[Dowiedz się, jak](../migrate/tutorial-migrate-aws-virtual-machines.md) MIGROWAĆ wystąpienia AWS z Azure Migrate. 


## <a name="migrate-with-site-recovery"></a>Migrowanie z Site Recovery

Site Recovery należy używać tylko w przypadku odzyskiwania po awarii, a nie migracji.

Jeśli używasz już Azure Site Recovery i chcesz nadal używać go do migracji AWS, wykonaj te same czynności, które są używane do konfigurowania [odzyskiwania po awarii maszyn fizycznych](physical-azure-disaster-recovery.md).


> [!NOTE]
> Po uruchomieniu trybu failover na potrzeby odzyskiwania po awarii, ostatnim krokiem jest zatwierdzenie trybu failover. W przypadku migrowania wystąpień AWS opcja **zatwierdzania** nie jest istotna. Zamiast tego należy wybrać opcję **pełna migracja** . 

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> Zapoznaj się z [typowymi pytaniami](../migrate/resources-faq.md) dotyczącymi Azure Migrate.
