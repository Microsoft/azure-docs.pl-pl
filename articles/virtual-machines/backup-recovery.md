---
title: Przegląd opcji tworzenia kopii zapasowych dla maszyn wirtualnych
description: Przegląd opcji tworzenia kopii zapasowych dla usługi Azure Virtual Machines.
author: cynthn
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 8/03/2020
ms.author: cynthn
ms.openlocfilehash: 5a093de0a27c8379cb6eff9c2bc3867dfdc20db5
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/26/2021
ms.locfileid: "98787811"
---
# <a name="backup-and-restore-options-for-virtual-machines-in-azure"></a>Opcje tworzenia kopii zapasowych i przywracania dla maszyn wirtualnych na platformie Azure

Możesz chronić swoje dane, tworząc kopie zapasowe w regularnych odstępach czasu. Dostępne są różne opcje tworzenia kopii zapasowych dla maszyn wirtualnych, w zależności od przypadków użycia.

## <a name="azure-backup"></a>Azure Backup

Aby utworzyć kopię zapasową maszyn wirtualnych platformy Azure z uruchomionymi obciążeniami produkcyjnymi, użyj Azure Backup. Azure Backup obsługuje kopie zapasowe spójne z aplikacjami zarówno dla maszyn wirtualnych z systemem Windows, jak i Linux. Usługa Azure Backup tworzy punkty odzyskiwania przechowywane w geograficznie nadmiarowych magazynach odzyskiwania. Z punktu odzyskiwania można przywrócić całą maszynę wirtualną lub tylko poszczególne pliki. 

Aby zapoznać się z prostym, praktycznym wprowadzeniem do Azure Backup dla maszyn wirtualnych platformy Azure, zobacz [Azure Backup przewodnika Szybki Start](../backup/quick-backup-vm-portal.md).

Aby uzyskać więcej informacji na temat działania Azure Backup, zobacz [Planowanie infrastruktury kopii zapasowych maszyn wirtualnych na platformie Azure](../backup/backup-azure-vms-introduction.md)


## <a name="azure-site-recovery"></a>Azure Site Recovery

Azure Site Recovery chroni maszyny wirtualne przed głównym scenariuszem awarii, gdy cały region napotyka awarię ze względu na główne klęski żywiołowe lub rozległe przerwy w działaniu usługi. Można skonfigurować Azure Site Recovery dla maszyn wirtualnych, aby umożliwić odzyskanie aplikacji za pomocą jednego kliknięcia w kilka minut. Możesz przeprowadzić replikację do wybranego regionu platformy Azure, nie jest ograniczona do sparowanych regionów. 

Można uruchomić testy awaryjne odzyskiwania po awarii, korzystając z przechodzenia do trybu failover na żądanie, bez wpływu na obciążenia produkcyjne lub trwającą replikację. Utwórz plany odzyskiwania, aby organizować tryb failover i powrót po awarii całej aplikacji uruchomionej na wielu maszynach wirtualnych. Funkcja planu odzyskiwania jest zintegrowana z elementami Runbook usługi Azure Automation.

Możesz rozpocząć od [replikowania maszyn wirtualnych](../site-recovery/azure-to-azure-quickstart.md). 

## <a name="managed-snapshots"></a>Zarządzane migawki 

W środowiskach deweloperskich i testowych migawki zapewniają szybką i prostą opcję tworzenia kopii zapasowych maszyn wirtualnych używających Managed Disks. Zarządzana migawka to pełna kopia dysku zarządzanego tylko do odczytu. Migawki istnieją niezależnie od dysku źródłowego i mogą być używane do tworzenia nowych dysków zarządzanych na potrzeby odbudowywania maszyny wirtualnej. Opłaty są naliczane na podstawie użytej części dysku. Jeśli na przykład utworzysz migawkę dysku zarządzanego o pojemności 64 GB i rzeczywistą ilość danych wynoszącą 10 GB, będzie ona rozliczana tylko za użyty rozmiar danych wynoszący 10 GB.  

Aby uzyskać więcej informacji na temat tworzenia migawek, zobacz:

* [Tworzenie kopii wirtualnego dysku twardego przechowywanej jako dysk zarządzany przy użyciu migawek w systemie Windows](./windows/snapshot-copy-managed-disk.md)
* [Tworzenie kopii wirtualnego dysku twardego przechowywanej jako dysk zarządzany przy użyciu migawek w systemie Linux](./linux/snapshot-copy-managed-disk.md)



## <a name="next-steps"></a>Następne kroki
Możesz wypróbować Azure Backup, wykonując [Azure Backup przewodnika Szybki Start](../backup/quick-backup-vm-portal.md).