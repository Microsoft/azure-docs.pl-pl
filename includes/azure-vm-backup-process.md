---
author: v-amallick
ms.service: Azure Backup
ms.topic: include
ms.date: 04/16/2021
ms.author: v-amallick
ms.openlocfilehash: e7af0819a94661a1008d96b7d0738c30a4a80c18
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107716753"
---
1. W przypadku maszyn wirtualnych platformy Azure wybranych do tworzenia kopii Azure Backup zadanie tworzenia kopii zapasowej jest uruchamiane zgodnie z wybranym harmonogramem tworzenia kopii zapasowej.
1. Podczas tworzenia pierwszej kopii zapasowej rozszerzenie kopii zapasowej jest instalowane na maszynie wirtualnej, jeśli maszyna wirtualna jest uruchomiona.
    - W przypadku maszyn wirtualnych z systemem Windows jest instalowane [rozszerzenie VMSnapshot.](../articles/virtual-machines/extensions/vmsnapshot-windows.md)
    - W przypadku maszyn wirtualnych z systemem Linux jest instalowane [rozszerzenie VMSnapshotLinux.](../articles/virtual-machines/extensions/vmsnapshot-linux.md)
1. W przypadku maszyn wirtualnych z systemem Windows, które są uruchomione, usługa Backup koordynuje się z usługą Windows Usługa kopiowania woluminów w tle (VSS), aby utworzyć spójną na aplikacji migawkę maszyny wirtualnej.
    - Domyślnie kopia zapasowa pobiera pełne kopie zapasowe usługi VSS.
    - Jeśli kopia zapasowa nie może wykonać migawki spójnej z aplikacją, jest tworzyć spójną na plikach migawkę bazowego magazynu (ponieważ podczas zatrzymania maszyny wirtualnej nie ma żadnych wystąpień zapisu aplikacji).
1. W przypadku maszyn wirtualnych z systemem Linux kopia zapasowa pobiera kopię zapasową spójną na plikach. W przypadku migawek spójnych na potrzeby aplikacji należy ręcznie dostosować skrypty wykonywane przed i po.
1. Po zakończeniu tworzenia migawki przez kopię zapasową dane są transferowe do magazynu.
    - Operacja tworzenia kopii zapasowej jest zoptymalizowana tak, aby kopie zapasowe poszczególnych dysków maszyny wirtualnej były tworzone równolegle.
    - W przypadku każdego dysku, którego kopia zapasowa jest tworzona, usługa Azure Backup odczytuje bloki danych na dysku, a następnie identyfikuje i przenosi tylko te z nich, które uległy zmianie (przyrost) od czasu utworzenia poprzedniej kopii zapasowej.
    - Dane migawki mogą nie być natychmiast kopiowane do magazynu. W godzinach szczytu może to potrwać kilka godzin. W przypadku zasad codziennego tworzenia kopii zapasowych łączny czas tworzenia kopii zapasowej maszyny wirtualnej jest krótszy niż 24 godziny.
1. Zmiany wprowadzone na maszynie wirtualnej z systemem Windows Azure Backup włączone są:
    - Microsoft Visual C++ 2013 Redistributable(x64) — na maszynie wirtualnej jest zainstalowany program 12.0.40660
    - Typ uruchamiania usługi kopiowania woluminów w tle (VSS) został zmieniony na automatyczny z ręcznego
    - Dodano usługę systemu Windows IaaSVmProvider

1. Po zakończeniu transferu danych migawka jest usuwana i tworzony jest punkt odzyskiwania.

![Architektura kopii zapasowych maszyn wirtualnych platformy Azure](../articles/backup/media/backup-azure-vms-introduction/vmbackup-architecture.png)
