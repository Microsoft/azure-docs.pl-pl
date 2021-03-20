---
title: Wykluczanie dysków maszyny wirtualnej VMware z odzyskiwania po awarii na platformę Azure za pomocą Azure Site Recovery
description: Jak wykluczać dyski maszyny wirtualnej VMware z replikacji na platformę Azure przy użyciu Azure Site Recovery.
author: mayurigupta13
manager: rochakm
ms.date: 12/10/2019
ms.author: mayg
ms.topic: conceptual
ms.openlocfilehash: c4842172ff181b5cdbe7f6fecf69da8755ae43fa
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "86129873"
---
# <a name="exclude-disks-from-vmware-vm-replication-to-azure"></a>Wykluczanie dysków z replikacji maszyny wirtualnej VMware do platformy Azure

W tym artykule opisano sposób wykluczania dysków podczas replikowania maszyn wirtualnych VMware na platformę Azure w celu odzyskiwania po awarii. Możesz chcieć wykluczyć dyski z replikacji z kilku powodów:

- Upewnij się, że nieważne dane zmienione na wykluczonym dysku nie są replikowane.
- Zoptymalizuj wykorzystaną przepustowość replikacji lub zasoby po stronie docelowej, wykluczając dyski, które nie są potrzebne do replikacji.
- Zapisz magazyn i zasoby sieciowe, nie usuwając niepotrzebnych danych.

Przed wykluczeniem dysków z replikacji:

- [Dowiedz się więcej](exclude-disks-replication.md) na temat wykluczania dysków.
- Przejrzyj [typowe scenariusze wykluczania](exclude-disks-replication.md#typical-scenarios) i [przykłady](exclude-disks-replication.md#example-1-exclude-the-sql-server-tempdb-disk) , które pokazują, jak wykluczenie dysku wpływa na replikację, pracę w trybie failover i powrót po awarii.

## <a name="before-you-start"></a>Przed rozpoczęciem

 Przed rozpoczęciem należy zwrócić uwagę na następujące kwestie:

- **Replikacja**: Domyślnie wszystkie dyski na maszynie są replikowane.
- **Typ dysku**: tylko dyski podstawowe można wyłączyć z replikacji. Nie możesz wykluczać dysków systemu operacyjnego ani dysków dynamicznych.
- **Usługa mobilności**: aby wykluczyć dysk z replikacji, należy ręcznie zainstalować usługę mobilności na maszynie przed włączeniem replikacji. Nie można użyć instalacji wypychanej, ponieważ ta metoda instaluje usługę mobilności na maszynie wirtualnej dopiero po włączeniu replikacji.  
- **Dodawanie/usuwanie/wykluczanie dysków**: po włączeniu replikacji nie można dodawać/usuwać/wykluczać dysków na potrzeby replikacji. Jeśli chcesz dodać/usunąć lub wykluczyć dyski, musisz wyłączyć ochronę maszyny, a następnie włączyć ją ponownie.
- **Tryb failover**: po przejściu do trybu failover aplikacje muszą mieć wykluczone dyski, aby można było wykonać te dyski ręcznie. Alternatywnie możesz zintegrować usługę Azure Automation w planie odzyskiwania, aby utworzyć dysk podczas pracy maszyny w trybie failover.
- **Powrót po awarii — system Windows**: po powrocie po awarii do lokacji lokalnej po zakończeniu pracy w trybie failover dyski systemu Windows tworzone ręcznie na platformie Azure nie będą ponownie działać. Jeśli na przykład przejdziesz do trybu failover trzy dyski i utworzysz dwa dyski bezpośrednio na maszynach wirtualnych platformy Azure, tylko trzy dyski, które zostały przełączone w tryb failover, zakończą się niepowodzeniem.
- **Powrót po awarii — Linux**: w przypadku powrotu po awarii maszyn z systemem Linux dyski tworzone ręcznie na platformie Azure nie powiodły się. Jeśli na przykład przejdziesz do trybu failover trzy dyski i utworzysz dwa dyski bezpośrednio na maszynach wirtualnych platformy Azure, wszystkie pięć zakończą się niepowodzeniem. Nie można wykluczyć dysków, które zostały utworzone ręcznie podczas powrotu po awarii lub w przypadku ochrony maszyn wirtualnych.



## <a name="exclude-disks-from-replication"></a>Wykluczanie dysków z replikacji

1. Po [włączeniu replikacji](./hyper-v-azure-tutorial.md) dla maszyny wirtualnej VMware, po wybraniu maszyn wirtualnych, które mają być replikowane, na stronie **Włączanie właściwości replikacji** należy  >    >   przejrzeć kolumny **dyski do replikacji** . Domyślnie do replikacji są wybierane wszystkie dyski.
2. Jeśli nie chcesz replikować określonego dysku, w obszarze **dyski do replikacji** wyczyść zaznaczenie dla wszystkich dysków, które chcesz wykluczyć. 

    ![Wykluczanie dysków z replikacji](./media/vmware-azure-exclude-disk/enable-replication-exclude-disk1.png)



## <a name="next-steps"></a>Następne kroki
Po skonfigurowaniu i uruchomieniu wdrożenia [dowiedz się więcej](failover-failback-overview.md) o różnych typach trybu failover.
