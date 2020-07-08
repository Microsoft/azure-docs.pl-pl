---
title: dołączanie pliku
description: dołączanie pliku
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 03/09/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: be71b269e618d13a126e4005754b307e9c6517d7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "67183225"
---
## <a name="azure-backup"></a>Azure Backup

Aby utworzyć kopię zapasową maszyn wirtualnych platformy Azure z uruchomionymi obciążeniami produkcyjnymi, użyj Azure Backup. Azure Backup obsługuje kopie zapasowe spójne z aplikacjami zarówno dla maszyn wirtualnych z systemem Windows, jak i Linux. Usługa Azure Backup tworzy punkty odzyskiwania przechowywane w geograficznie nadmiarowych magazynach odzyskiwania. Z punktu odzyskiwania można przywrócić całą maszynę wirtualną lub tylko poszczególne pliki. 

Aby uzyskać proste, praktyczne wprowadzenie do Azure Backup dla maszyn wirtualnych platformy Azure, zobacz samouczek "Tworzenie kopii zapasowych maszyn wirtualnych platformy Azure" dla [systemu](../articles/virtual-machines/windows/tutorial-backup-vms.md) [Linux](../articles/virtual-machines/linux/tutorial-backup-vms.md) lub Windows.

Aby uzyskać więcej informacji na temat działania Azure Backup, zobacz [Planowanie infrastruktury kopii zapasowych maszyn wirtualnych na platformie Azure](../articles/backup/backup-azure-vms-introduction.md)


## <a name="azure-site-recovery"></a>Azure Site Recovery

Azure Site Recovery chroni maszyny wirtualne przed głównym scenariuszem awarii, gdy cały region napotyka awarię ze względu na główne klęski żywiołowe lub rozległe przerwy w działaniu usługi. Można skonfigurować Azure Site Recovery dla maszyn wirtualnych, aby umożliwić odzyskanie aplikacji za pomocą jednego kliknięcia w kilka minut. Możesz przeprowadzić replikację do wybranego regionu platformy Azure, nie jest ograniczona do sparowanych regionów. 

Można uruchomić testy awaryjne odzyskiwania po awarii, korzystając z przechodzenia do trybu failover na żądanie, bez wpływu na obciążenia produkcyjne lub trwającą replikację. Utwórz plany odzyskiwania, aby organizować tryb failover i powrót po awarii całej aplikacji uruchomionej na wielu maszynach wirtualnych. Funkcja planu odzyskiwania jest zintegrowana z elementami Runbook usługi Azure Automation.

Możesz rozpocząć od [replikowania maszyn wirtualnych](https://aka.ms/a2a-getting-started). 

## <a name="managed-snapshots"></a>Zarządzane migawki 

W środowiskach deweloperskich i testowych migawki zapewniają szybką i prostą opcję tworzenia kopii zapasowych maszyn wirtualnych używających Managed Disks. Zarządzana migawka to pełna kopia dysku zarządzanego tylko do odczytu. Migawki istnieją niezależnie od dysku źródłowego i mogą być używane do tworzenia nowych dysków zarządzanych na potrzeby odbudowywania maszyny wirtualnej. Opłaty są naliczane na podstawie użytej części dysku. Jeśli na przykład utworzysz migawkę dysku zarządzanego o pojemności 64 GB i rzeczywistą ilość danych wynoszącą 10 GB, będzie ona rozliczana tylko za użyty rozmiar danych wynoszący 10 GB.  

Aby uzyskać więcej informacji na temat tworzenia migawek, zobacz:

* [Tworzenie kopii wirtualnego dysku twardego przechowywanej jako dysk zarządzany przy użyciu migawek w systemie Windows](../articles/virtual-machines/windows/snapshot-copy-managed-disk.md)
* [Tworzenie kopii wirtualnego dysku twardego przechowywanej jako dysk zarządzany przy użyciu migawek w systemie Linux](../articles/virtual-machines/linux/snapshot-copy-managed-disk.md)



## <a name="next-steps"></a>Następne kroki
Azure Backup można wypróbować, postępując zgodnie z samouczkiem "Tworzenie kopii zapasowej maszyn wirtualnych systemu Windows" w systemie [Linux](../articles/virtual-machines/linux/tutorial-backup-vms.md) lub [Windows](../articles/virtual-machines/windows/tutorial-backup-vms.md).
