---
title: Uruchom przechodzenie do szczegółów odzyskiwania po awarii z rozwiązań VMware platformy Azure na platformę Azure za pomocą Azure Site Recovery
description: Dowiedz się, jak uruchomić przechodzenie do szczegółów odzyskiwania po awarii z chmury prywatnej rozwiązania VMware platformy Azure do platformy Azure przy użyciu Azure Site Recovery.
author: Harsha-CS
manager: rochakm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 09/30/2020
ms.author: harshacs
ms.custom: MVC
ms.openlocfilehash: 46d5e1cf773a24b032874ee54021c780e1f361af
ms.sourcegitcommit: 5abc3919a6b99547f8077ce86a168524b2aca350
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/07/2020
ms.locfileid: "91814590"
---
# <a name="run-a-disaster-recovery-drill-from-azure-vmware-solution-to-azure"></a>Uruchom przechodzenie do szczegółów odzyskiwania po awarii z rozwiązań VMware platformy Azure na platformę Azure

W tym artykule opisano sposób uruchamiania przechodzenia do odzyskiwania po awarii dla maszyny wirtualnej rozwiązania VMware platformy Azure na platformie Azure przy użyciu usługi [Azure Site Recovery](site-recovery-overview.md) . Próba pozwala zweryfikować działanie strategii replikacji bez ryzyka utraty danych.


Jest to czwarty samouczek z serii, który pokazuje, jak skonfigurować odzyskiwanie po awarii na platformie Azure dla maszyn wirtualnych platformy Azure.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]

> * Konfigurowanie sieci izolowanej do testowego przełączania w tryb failover
> * Przygotowywanie do połączenia z maszyną wirtualną platformy Azure po przejściu do trybu failover
> * Uruchom test pracy w trybie failover dla jednej maszyny.

> [!NOTE]
> Samouczki pokazują najprostszą ścieżkę wdrożenia dla scenariusza. Jeśli to możliwe używają opcji domyślnych i nie przedstawiają wszystkich możliwych ustawień i ścieżek. Jeśli chcesz dowiedzieć się więcej na temat kroków szczegółowych dotyczących odzyskiwania po awarii, [Zobacz ten artykuł](site-recovery-test-failover-to-azure.md).

## <a name="before-you-start"></a>Przed rozpoczęciem

Wykonaj poprzednie samouczki:

1. Upewnij się, że [skonfigurowano platformę Azure](avs-tutorial-prepare-azure.md) pod kątem odzyskiwania po awarii na platformie Azure.
2. Wykonaj następujące [kroki](avs-tutorial-prepare-avs.md) , aby przygotować wdrożenie rozwiązania Azure VMware do odzyskiwania po awarii na platformie Azure.
3. [Skonfiguruj](avs-tutorial-replication.md) odzyskiwanie po awarii dla maszyn wirtualnych rozwiązań VMware platformy Azure.
 

## <a name="verify-vm-properties"></a>Sprawdzanie właściwości maszyn wirtualnych

Przed uruchomieniem testowego przejścia w tryb failover Sprawdź właściwości maszyny wirtualnej i upewnij się, że  [maszyna wirtualna VMware](vmware-physical-azure-support-matrix.md#replicated-machines) jest zgodna z wymaganiami platformy Azure.

1. W obszarze **Chronione elementy** kliknij kolejno pozycje **Zreplikowane elementy** > i Maszyna wirtualna.
2. Okienko **Zreplikowany element** zawiera podsumowanie informacji na temat maszyny wirtualnej, jej kondycję oraz najnowsze dostępne punkty odzyskiwania. Kliknij przycisk **Właściwości**, aby wyświetlić więcej szczegółów.
3. W obszarze **obliczenia i sieć**można zmodyfikować nazwę platformy Azure, grupę zasobów, rozmiar docelowy, zestaw dostępności i ustawienia dysku zarządzanego.
4. Możesz wyświetlać i modyfikować ustawienia sieciowe, w tym sieć/podsieć, w której zlokalizowana będzie maszyna wirtualna na platformie Azure po wejściu w tryb failover, oraz adres IP, który będzie do niej przypisany.
5. W obszarze **Dyski** można znaleźć informacje dotyczące systemu operacyjnego i dysków danych maszyny wirtualnej.

## <a name="create-a-network-for-test-failover"></a>Tworzenie sieci do testowania pracy w trybie failover

Do testowania pracy w trybie failover zalecamy wybór sieci izolowanej od sieci produkcyjnej lokacji odzyskiwania, określonej w obszarze ustawień **Obliczenia i sieć** poszczególnych maszyn wirtualnych. Tworzone sieci wirtualne platformy Azure są domyślnie izolowane od innych sieci. Sieć testowa powinna odzwierciedlać sieć produkcyjną:

- Sieć testowa powinna mieć taką samą liczbę podsieci jak sieć produkcyjna. Podsieci powinny mieć takie same nazwy.
- Sieć testowa powinna używać tej samej klasy adresów IP i zakresu podsieci.
- Zaktualizuj system DNS sieci testowej, używając adresu IP określonego dla maszyny wirtualnej systemu DNS w obszarze ustawień **Obliczenia i sieć**. Aby uzyskać więcej informacji, zobacz sekcję [Zagadnienia dotyczące testowania trybu failover dla usługi Active Directory](site-recovery-active-directory.md#test-failover-considerations).

## <a name="run-a-test-failover-for-a-single-vm"></a>Uruchamianie testu trybu failover dla pojedynczej maszyny wirtualnej

Po uruchomieniu próby przejścia w tryb failover wykonywane są następujące operacje:

1. Uruchamiane jest sprawdzanie wymagań wstępnych, aby upewnić się, że zostały spełnione wszystkie warunki przejścia w tryb failover.
2. Tryb failover przetwarza dane, aby umożliwić utworzenie maszyny wirtualnej platformy Azure. Jeśli wybierzesz najnowszy punkt odzyskiwania, punkt odzyskiwania zostanie utworzony na podstawie danych.
3. Tworzona jest maszyna wirtualna platformy Azure przy użyciu danych przetworzonych w poprzednim kroku.

Uruchom testowe przełączenie w tryb failover w następujący sposób:

1. W obszarze **Ustawienia**  >  **zreplikowane elementy**kliknij maszynę wirtualną > **i przetestuj tryb failover**.
2. Na potrzeby tego samouczka wybierz punkt odzyskiwania **Najnowszy przetworzony**. Spowoduje to przełączenie maszyny wirtualnej w tryb failover do najnowszego dostępnego punktu w czasie. Wyświetlana jest sygnatura czasowa. Ta opcja zapewnia niską wartość celu czasu odzyskiwania (RTO, Recovery Time Objective), ponieważ nie trzeba poświęcać czasu na przetwarzanie danych.
3. W obszarze **Test pracy w trybie failover** wybierz sieć platformy Azure, z którą maszyny wirtualne platformy Azure zostaną połączone po przejściu w tryb failover.
4. Kliknij przycisk **OK**, aby rozpocząć tryb failover. Możesz śledzić postęp, klikając maszynę wirtualną i otwierając jej właściwości. Możesz też kliknąć zadanie **Testowe przełączenie w tryb failover** po wybraniu pozycji nazwa magazynu > **Ustawienia** > **Zadania** >
   **Zadania usługi Site Recovery**.
5. Po zakończeniu trybu failover w obszarze Azure Portal > **Maszyny wirtualne** będzie widoczna replika maszyny wirtualnej platformy Azure. Upewnij się, że maszyna wirtualna ma prawidłowy rozmiar, jest połączona z odpowiednią siecią i jest uruchomiona.
6. Powinno być teraz możliwe nawiązanie połączenia ze zreplikowaną maszyną wirtualną na platformie Azure.
7. Aby usunąć maszyny wirtualne platformy Azure utworzone podczas testowego przełączania w tryb failover, kliknij pozycję **Wyczyść test pracy w trybie failover** na maszynie wirtualnej. W obszarze **uwagi**Zarejestruj i Zapisz wszelkie obserwacje związane z testem pracy w trybie failover.

W niektórych scenariuszach tryb failover wymaga dodatkowego przetwarzania, którego przeprowadzenie zajmuje około 8–10 minut. Dłuższy czas testowego przełączania w tryb failover może występować w przypadku maszyn VMware z systemem Linux, maszyn wirtualnych VMware, które nie mają włączonej usługi DHCP, oraz maszyn wirtualnych VMware, które nie mają następujących sterowników rozruchowych: storvsc, vmbus, storflt, intelide, atapi.

## <a name="connect-after-failover"></a>Połącz po przejściu w tryb failover

Aby nawiązać połączenie z maszynami wirtualnymi platformy Azure przy użyciu protokołu RDP/SSH po przejściu w tryb failover, [Przygotuj się do połączenia](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover). Jeśli występują problemy z łącznością po przejściu do trybu failover, postępuj zgodnie z przewodnikiem [rozwiązywania problemów](site-recovery-failover-to-azure-troubleshoot.md) .

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Uruchamianie trybu failover](avs-tutorial-failover.md)

