---
title: Konfigurowanie trybu failover maszyn wirtualnych funkcji Hyper-V na platformie Azure w Azure Site Recovery
description: Dowiedz się, jak przechodzić w tryb failover maszyn wirtualnych funkcji Hyper-V na platformie Azure przy użyciu Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 12/16/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: b46a2ea12a697afde8223cc3595365c1286512c7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "86132454"
---
# <a name="fail-over-hyper-v-vms-to-azure"></a>Przełączanie maszyn wirtualnych funkcji Hyper-V do trybu failover na platformie Azure

W tym samouczku opisano sposób awaryjnego przełączania maszyn wirtualnych funkcji Hyper-V na platformę Azure przy użyciu [Azure Site Recovery](site-recovery-overview.md). Po wprowadzeniu maszyny w tryb failover można przywrócić ją po awarii do lokacji lokalnej, gdy ta będzie znów dostępna. Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Sprawdź właściwości maszyny wirtualnej funkcji Hyper-V, aby sprawdzić zgodność z wymaganiami platformy Azure.
> * Przełączenie określonych maszyn wirtualnych do trybu failover na platformie Azure.


Jest to piąty samouczek z tej serii. Założono w nim, że zostały już wykonane zadania z poprzednich samouczków.    

1. [Przygotowywanie platformy Azure](tutorial-prepare-azure.md)
2. [Przygotowywanie lokalnej funkcji Hyper-V](./hyper-v-prepare-on-premises-tutorial.md)
3. Konfigurowanie odzyskiwania po awarii dla [maszyn wirtualnych funkcji Hyper-V](./hyper-v-azure-tutorial.md) lub dla [maszyn wirtualnych funkcji Hyper-V zarządzanych w chmurach programu System Center VMM](./hyper-v-vmm-azure-tutorial.md)
4. [Uruchamianie próbnego odzyskiwania po awarii](tutorial-dr-drill-azure.md)

[Dowiedz się więcej o](failover-failback-overview.md#types-of-failover) różnych typach trybu failover. Jeśli chcesz przejść do trybu failover wielu maszyn wirtualnych w planie odzyskiwania, zapoznaj się z [tym artykułem](site-recovery-failover.md).

## <a name="prepare-for-failover"></a>Przygotowanie do przejścia do trybu failover 
Upewnij się, że na maszynie wirtualnej nie znajdują się żadne migawki oraz że podczas powrotu po awarii lokalna maszyna wirtualna jest wyłączona. Pomaga to zapewnić spójność danych podczas replikacji. Nie należy włączać lokalnej maszyny wirtualnej podczas powrotu po awarii. 

Przełączanie do trybu failover i powrót po awarii odbywa się w trzech etapach:

1. **Przejście do trybu failover na platformie Azure**: przełączenie maszyn wirtualnych funkcji Hyper-V w tryb failover, z lokacji lokalnej na platformę Azure.
2. **Powrót po awarii do środowiska lokalnego**: przełączenie maszyn wirtualnych platformy Azure w tryb failover do lokacji lokalnej, gdy będzie ona już dostępna. Na tym etapie rozpoczyna się synchronizacja danych z platformy Azure do środowiska lokalnego, a po jej zakończeniu uruchamiane są lokalne maszyny wirtualne.  
3. **Odwrotna replikacja lokalnych maszyn wirtualnych**: po powrocie po awarii do środowiska lokalnego następuje odwrotna replikacja lokalnych maszyn wirtualnych w celu ponownego rozpoczęcia replikacji na platformie Azure.

## <a name="verify-vm-properties"></a>Sprawdzanie właściwości maszyn wirtualnych

Przed przełączeniem do trybu failover sprawdź właściwości maszyny wirtualnej i upewnij się, że maszyna wirtualna spełnia [wymagania platformy Azure](hyper-v-azure-support-matrix.md#replicated-vms).

W obszarze **Chronione elementy** kliknij kolejno pozycje **Zreplikowane elementy** > Maszyna wirtualna.

1. Okienko **Zreplikowany element** zawiera podsumowanie informacji na temat maszyny wirtualnej, jej kondycję oraz najnowsze dostępne punkty odzyskiwania. Kliknij przycisk **Właściwości**, aby wyświetlić więcej szczegółów.

1. W obszarze **Obliczenia i sieć** można zmodyfikować nazwę platformy Azure, grupę zasobów, rozmiar docelowy, [zestaw dostępności](../virtual-machines/windows/tutorial-availability-sets.md) i ustawienia dysku zarządzanego.

1. Możesz wyświetlać i modyfikować ustawienia sieciowe, w tym sieć/podsieć, w której zlokalizowana będzie maszyna wirtualna na platformie Azure po wejściu w tryb failover, oraz adres IP, który będzie do niej przypisany.

1. W obszarze **Dyski** można znaleźć informacje dotyczące systemu operacyjnego i dysków danych maszyny wirtualnej.

## <a name="fail-over-to-azure"></a>Przełączenie do trybu failover na platformie Azure

1. W obszarze **Ustawienia**  >  **zreplikowane elementy** kliknij maszynę wirtualną > **tryb failover**.
2. W obszarze **Tryb failover** wybierz **Najnowszy** punkt odzyskiwania. 
3. Wybierz pozycję **Zamknij maszynę przed rozpoczęciem pracy w trybie failover**. Usługa Site Recovery próbuje zamknąć źródłowe maszyny wirtualne przed wyzwoleniem trybu failover. Przełączanie do trybu failover będzie kontynuowane, nawet jeśli zamknięcie nie powiedzie się. Postęp pracy w trybie failover można wykonać na stronie **zadań** .
4. Po weryfikacji przełączenia do trybu failover kliknij przycisk **Zatwierdź**. To działanie usuwa wszystkie dostępne punkty odzyskiwania.

> [!WARNING]
> **Nie należy anulować trybu failover po rozpoczęciu przełączania**: w takim przypadku przełączanie zostanie zatrzymane, ale maszyna wirtualna nie zostanie ponownie zreplikowana.

## <a name="connect-to-failed-over-vm"></a>Nawiązywanie połączenia z maszyną wirtualną w trybie failover

1. Jeśli chcesz nawiązać połączenie z maszynami wirtualnymi platformy Azure po przejściu w tryb failover przy użyciu usług Remote Desktop Protocol (RDP) i Secure Shell (SSH), [Sprawdź, czy zostały spełnione wymagania](failover-failback-overview.md#connect-to-azure-after-failover).
2. Po przejściu w tryb failover przejdź do maszyny wirtualnej i sprawdź poprawność, [łącząc](../virtual-machines/windows/connect-logon.md) się z nią.
3. Jeśli po przejściu w tryb failover ma zostać użyty inny punkt odzyskiwania, użyj **Zmień punkt odzyskiwania** . Po zatwierdzeniu przejścia w tryb failover w następnym kroku ta opcja nie będzie już dostępna.
4. Po sprawdzeniu poprawności wybierz pozycję **Zatwierdź** , aby zakończyć punkt odzyskiwania maszyny wirtualnej po przejściu do trybu failover.
5. Po zatwierdzeniu wszystkie pozostałe dostępne punkty odzyskiwania zostaną usunięte. Ten krok powoduje zakończenie pracy w trybie failover.

>[!TIP]
> Jeśli występują problemy z łącznością po przejściu do trybu failover, postępuj zgodnie z [przewodnikiem rozwiązywania problemów](site-recovery-failover-to-azure-troubleshoot.md).


## <a name="next-steps"></a>Następne kroki

Po przejściu w tryb failover ponownie Włącz ochronę maszyn wirtualnych platformy Azure, aby umożliwić ich replikację z platformy Azure do lokacji lokalnej. Następnie po ponownym włączeniu ochrony maszyn wirtualnych i przeprowadzeniu replikacji do lokacji lokalnej powrót po awarii z platformy Azure zakończy się niepowodzeniem.
