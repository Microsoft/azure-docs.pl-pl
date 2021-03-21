---
title: Wyklucz maszyny wirtualne funkcji Hyper-V z odzyskiwania po awarii na platformę Azure za pomocą Azure Site Recovery
description: Jak wykluczać dyski maszyny wirtualnej funkcji Hyper-V z replikacji na platformę Azure przy użyciu Azure Site Recovery.
author: mayurigupta13
manager: rochakm
ms.topic: conceptual
ms.author: mayg
ms.date: 11/12/2019
ms.openlocfilehash: 373cffe96119af4a2fc0d74e2090e6cc24dcaf8f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "86131252"
---
# <a name="exclude-disks-from-replication"></a>Wykluczanie dysków z replikacji

W tym artykule opisano sposób wykluczania dysków podczas replikowania maszyn wirtualnych funkcji Hyper-V na platformę Azure. Możesz chcieć wykluczyć dyski z replikacji z kilku powodów:

- Upewnij się, że nieważne dane zmienione na wykluczonym dysku nie są replikowane.
- Zoptymalizuj wykorzystaną przepustowość replikacji lub zasoby po stronie docelowej, wykluczając dyski, które nie są potrzebne do replikacji.
- Zapisz magazyn i zasoby sieciowe, nie usuwając niepotrzebnych danych.

Przed wykluczeniem dysków z replikacji:

- [Dowiedz się więcej](exclude-disks-replication.md) na temat wykluczania dysków.
- Przejrzyj [typowe scenariusze wykluczania](exclude-disks-replication.md#typical-scenarios) i [przykłady](exclude-disks-replication.md#example-1-exclude-the-sql-server-tempdb-disk) , które pokazują, jak wykluczenie dysku wpływa na replikację, pracę w trybie failover i powrót po awarii.

## <a name="before-you-start"></a>Przed rozpoczęciem

Przed rozpoczęciem należy zwrócić uwagę na następujące kwestie:

- **Replikacja**: Domyślnie wszystkie dyski na maszynie są replikowane.
- **Typ dysku**:
    - Dyski podstawowe można wykluczyć z replikacji.
    - Nie możesz wykluczać dysków systemu operacyjnego.
    - Nie zalecamy wykluczania dysków dynamicznych. Site Recovery nie może zidentyfikować wirtualnego dysku twardego w warstwie Podstawowa lub dynamiczna na maszynie wirtualnej gościa.  Jeśli nie wykluczasz wszystkich zależnych dysków woluminu dynamicznego, chroniony dysk dynamiczny będzie uszkodzonym dyskiem maszyny wirtualnej w trybie failover, a dane na tym dysku są niedostępne.
- **Dodawanie/usuwanie/wykluczanie dysków**: po włączeniu replikacji nie można dodawać/usuwać/wykluczać dysków na potrzeby replikacji. Jeśli chcesz dodać/usunąć lub wykluczyć dysk, musisz wyłączyć ochronę maszyny wirtualnej, a następnie włączyć ją ponownie.
- **Tryb failover**: po przejściu do trybu failover aplikacje nie potrzebują dysków wykluczonych, aby można było wykonać te dyski ręcznie. Alternatywnie możesz zintegrować usługę Azure Automation w planie odzyskiwania, aby utworzyć dysk podczas pracy maszyny w trybie failover.
- **Powrót po awarii**: po powrocie po awarii do lokacji lokalnej po zakończeniu pracy w trybie failover dyski utworzone ręcznie na platformie Azure nie będą ponownie działać. Jeśli na przykład przeniesiesz trzy dyski do trybu failover i utworzysz dwa dyski bezpośrednio na maszynie wirtualnej platformy Azure, tylko trzy dyski, które przechodzą w tryb failover, zakończą się niepowodzeniem. Nie można dołączyć dysków utworzonych ręcznie podczas powrotu po awarii ani replikacji odwrotnej maszyn wirtualnych.

## <a name="exclude-disks"></a>Wykluczanie dysków

1. Aby wykluczyć dyski po [włączeniu replikacji](./hyper-v-azure-tutorial.md) dla maszyny wirtualnej funkcji Hyper-V, po wybraniu maszyn wirtualnych, które mają być replikowane, na stronie **Włączanie właściwości replikacji** należy  >    >   przejrzeć kolumny **dyski do replikacji** . Domyślnie do replikacji są wybierane wszystkie dyski.
2. Jeśli nie chcesz replikować określonego dysku, w obszarze **dyski do replikacji** wyczyść zaznaczenie dla wszystkich dysków, które chcesz wykluczyć. 

    ![Wykluczanie dysków z replikacji](./media/hyper-v-exclude-disk/enable-replication6-with-exclude-disk.png)


## <a name="next-steps"></a>Następne kroki
Po skonfigurowaniu i uruchomieniu wdrożenia [dowiedz się więcej](failover-failback-overview.md) o różnych typach trybu failover.
