---
author: cynthn
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 09fa612e7e5c681da16bf19e94c626ae14a3b8a1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "77590732"
---
Podczas dodawania dysków danych do maszyny wirtualnej z systemem Linux mogą wystąpić błędy, jeśli dysk nie istnieje na numerze LUN 0. W przypadku ręcznego dodawania dysku przy użyciu `az vm disk attach -new` polecenia i określania numeru LUN ( `--lun` ) zamiast zezwalania na platformę Azure w celu ustalenia odpowiedniej jednostki LUN należy zadbać o to, że dysk już istnieje/będzie istnieć na numerze LUN 0. 

Rozważmy następujący przykład pokazujący fragment danych wyjściowych z `lsscsi` :

```bash
[5:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sdc 
[5:0:0:1]    disk    Msft     Virtual Disk     1.0   /dev/sdd 
```

Dwa dyski danych istnieją w jednostkach LUN 0 i LUN 1 (pierwsza kolumna w `lsscsi` szczegółach wyjściowych `[host:channel:target:lun]` ). Oba dyski powinny być dostępne z poziomu maszyny wirtualnej. Jeśli ręcznie określono pierwszy dysk, który ma zostać dodany przy użyciu jednostki LUN 1, i drugi dysk o numerze LUN 2, dyski mogą nie być poprawnie widoczne z poziomu maszyny wirtualnej.

> [!NOTE]
> `host`W tych przykładach wartość platformy Azure to 5, ale może się to różnić w zależności od wybranego typu magazynu.
> 
> 

To zachowanie dysku nie jest problemem z platformą Azure, ale sposób, w jaki jądro systemu Linux postępuje zgodnie ze specyfikacją interfejsu SCSI. Gdy jądro systemu Linux skanuje magistralę SCSI dla dołączonych urządzeń, należy znaleźć urządzenie w jednostce LUN 0, aby system kontynuował skanowanie w poszukiwaniu dodatkowych urządzeń. W związku z tym:

* Przejrzyj dane wyjściowe `lsscsi` po dodaniu dysku z danymi, aby sprawdzić, czy masz dysk o numerze LUN 0.
* Jeśli dysk nie jest prawidłowo widoczny w maszynie wirtualnej, sprawdź, czy dysk istnieje na numerze LUN 0.

