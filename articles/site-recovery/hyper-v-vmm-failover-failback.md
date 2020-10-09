---
title: Konfigurowanie trybu failover/powrotu po awarii do lokacji pomocniczej funkcji Hyper-V za pomocą Azure Site Recovery
description: Dowiedz się, jak przełączać maszyny wirtualne funkcji Hyper-V do lokacji lokalnej w tryb failover i powrócić po awarii do lokacji głównej, podczas gdy odzyskiwanie awaryjne Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: raynew
ms.openlocfilehash: d31355bcb0ce42874c19988738ba06138c7a0b7c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "74082600"
---
# <a name="fail-over-and-fail-back-hyper-v-vms-replicated-to-your-secondary-on-premises-site"></a>Przełączenie w tryb failover i powrót po awarii maszyn wirtualnych funkcji Hyper-V replikowanych do pomocniczej lokacji lokalnej

Usługa [Azure Site Recovery](site-recovery-overview.md) zarządza i organizuje replikację, pracę w trybie failover oraz powrót po awarii maszyn lokalnych i maszyn wirtualnych platformy Azure.

W tym artykule opisano sposób przełączenia w tryb failover maszyny wirtualnej funkcji Hyper-V zarządzanej w chmurze System Center Virtual Machine Manager (VMM) do pomocniczej lokacji programu VMM. Po wprowadzeniu maszyny w tryb failover można przywrócić ją po awarii do lokacji lokalnej, gdy ta będzie znów dostępna. W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Przełączenie w tryb failover maszyny wirtualnej funkcji Hyper-V z podstawowej chmury programu VMM do pomocniczej chmury programu VMM
> * Ponowne włączanie ochrony z lokacji dodatkowej do podstawowej i powrót po awarii
> * Opcjonalnie Rozpocznij replikację z podstawowego do pomocniczego

## <a name="failover-and-failback"></a>Przechodzenie w tryb failover i powrót po awarii

Przełączenie w tryb failover i powrót po awarii ma trzy etapy:

1. Przełączenie w tryb failover **do lokacji dodatkowej**: przełączenie maszyn w tryb failover z lokacji głównej do pomocniczej.
2. **Powrót po awarii z lokacji dodatkowej**: replikowanie maszyn wirtualnych z elementu pomocniczego do podstawowego i uruchamianie planowanej pracy w trybie failover w celu powrotu po awarii.
3. Po zaplanowanym przejściu w tryb failover opcjonalnie można ponownie rozpocząć replikację z lokacji głównej do pomocniczej bazy danych.


## <a name="prerequisites"></a>Wymagania wstępne

- Upewnij się, że wykonano [drążenie odzyskiwania po awarii](hyper-v-vmm-test-failover.md) , aby sprawdzić, czy wszystko działa zgodnie z oczekiwaniami.
- Aby zakończyć powrót po awarii, upewnij się, że serwery główne i pomocnicze programu VMM są połączone z Site Recovery.



## <a name="run-a-failover-from-primary-to-secondary"></a>Uruchamianie trybu failover z poziomu podstawowego do pomocniczego

Na maszynach wirtualnych funkcji Hyper-V można uruchamiać regularne lub planowane przejścia w tryb failover.

- Używaj zwykłej pracy w trybie failover w nieoczekiwany sposób. Po uruchomieniu tej pracy w trybie failover program Site Recovery tworzy maszynę wirtualną w lokacji dodatkowej i jej uprawnienia. Utrata danych może wystąpić w zależności od oczekujących danych, które nie zostały zsynchronizowane.
- Planowana praca w trybie failover może być używana do konserwacji lub w oczekiwanej awarii. Ta opcja zapewnia zerową utratę danych. Gdy zostanie wyzwolone planowane przejście w tryb failover, źródłowe maszyny wirtualne są zamykane. Niezsynchronizowane dane są synchronizowane, a tryb failover zostanie wyzwolony. 
- 
  W tej procedurze opisano sposób uruchamiania regularnego trybu failover.


1. W obszarze **Ustawienia**  >  **zreplikowane elementy** kliknij maszynę wirtualną > **tryb failover**.
1. Wybierz opcję **Zamknij maszynę przed rozpoczęciem pracy w trybie failover** , jeśli chcesz, aby Site Recovery próbuje wykonać zamknięcie źródłowych maszyn wirtualnych przed wyzwoleniem trybu failover. Site Recovery również spróbuje zsynchronizować dane lokalne, które nie zostały jeszcze wysłane do lokacji dodatkowej przed wyzwoleniem trybu failover. Należy pamiętać, że tryb failover kontynuuje działanie nawet w przypadku niepowodzenia wyłączenia. Postęp pracy w trybie failover można wykonać na stronie **zadań** .
2. Teraz będzie można zobaczyć maszynę wirtualną w pomocniczej chmurze programu VMM.
3. Po sprawdzeniu maszyny wirtualnej **Zatwierdź** tryb failover. To działanie usuwa wszystkie dostępne punkty odzyskiwania.

> [!WARNING]
> **Nie anuluj trybu failover po rozpoczęciu przełączania**: przed uruchomieniem trybu failover replikacja maszyny wirtualnej zostanie zatrzymana. Jeśli anulujesz tryb failover po rozpoczęciu przełączania, zostanie ono zatrzymane, ale maszyna wirtualna nie zostanie ponownie zreplikowana.  


## <a name="reverse-replicate-and-failover"></a>Replikacja odwrotna i tryb failover

Rozpocznij replikację z lokacji dodatkowej do lokacji głównej i wróć do niej powrót po awarii. Po ponownym uruchomieniu maszyn wirtualnych w lokacji głównej można je replikować do lokacji dodatkowej.  

 
1. Kliknij > maszynę wirtualną, a następnie kliknij pozycję **replikacja odwrotna**.
2. Po zakończeniu zadania kliknij >maszynę wirtualną w **trybie failover**, zweryfikuj kierunek trybu failover (z pomocniczej chmury programu VMM) i wybierz lokalizację źródłową i docelową. 
4. Zainicjuj tryb failover. Na karcie **Zadania** można śledzić postęp trybu failover.
5. Sprawdź, czy maszyna wirtualna jest dostępna w głównej chmurze programu VMM.
6. Jeśli chcesz ponownie rozpocząć replikację podstawowej maszyny wirtualnej z powrotem do lokacji dodatkowej, kliknij pozycję **replikacja odwrotna**.

## <a name="next-steps"></a>Następne kroki
[Zapoznaj się z krokami](hyper-v-vmm-disaster-recovery.md) dotyczącymi replikowania maszyn wirtualnych funkcji Hyper-V do lokacji dodatkowej.
