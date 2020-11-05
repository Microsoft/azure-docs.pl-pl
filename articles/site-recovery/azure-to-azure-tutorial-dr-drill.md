---
title: Samouczek przedstawiający przechodzenie do szczegółów odzyskiwania po awarii maszyny wirtualnej platformy Azure przy użyciu Azure Site Recovery
description: W tym samouczku Uruchom funkcję odzyskiwania po awarii maszyny wirtualnej platformy Azure w innym regionie przy użyciu Site Recovery.
services: site-recovery
ms.topic: tutorial
ms.date: 11/05/2020
ms.custom: mvc
ms.openlocfilehash: c7cd1898f27f3b7255009efb40f6bcc8938dbf9e
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/05/2020
ms.locfileid: "93395654"
---
# <a name="tutorial-run-a-disaster-recovery-drill-for-azure-vms"></a>Samouczek: uruchamianie przechodzenia do odzyskiwania po awarii dla maszyn wirtualnych platformy Azure

Dowiedz się, jak przeprowadzić drążenie awaryjne w innym regionie świadczenia usługi Azure, w przypadku maszyn wirtualnych platformy Azure replikowanych za pomocą [Azure Site Recovery](site-recovery-overview.md). W tym artykule opisano następujące zagadnienia:

> [!div class="checklist"]
> * Weryfikacja wymagań wstępnych
> * Sprawdź ustawienia maszyny wirtualnej przed przejściem do szczegółów
> * Wykonywanie próby przejścia w tryb failover
> * Wyczyść po przejściu do szczegółów


> [!NOTE]
> Ten samouczek zawiera minimalne kroki uruchamiania funkcji drążenia odzyskiwania po awarii. Jeśli chcesz wykonać drążenie z pełnym testowaniem infrastruktury, Dowiedz się więcej o [sieci](azure-to-azure-about-networking.md)maszyn wirtualnych platformy Azure, [automatyzacji](azure-to-azure-powershell.md)i [rozwiązywaniu problemów](azure-to-azure-troubleshoot-errors.md).

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem tego samouczka musisz włączyć odzyskiwanie awaryjne dla co najmniej jednej maszyny wirtualnej platformy Azure. Aby to zrobić, [Wykonaj pierwszy samouczek](azure-to-azure-tutorial-enable-replication.md) z tej serii.

## <a name="verify-vm-settings"></a>Sprawdź ustawienia maszyny wirtualnej

1. W magazynie > **zreplikowane elementy** wybierz maszynę wirtualną.

    ![Opcja otwierania strony odzyskiwania po awarii we właściwościach maszyny wirtualnej](./media/azure-to-azure-tutorial-dr-drill/vm-settings.png)

2. Na stronie **Przegląd** Sprawdź, czy maszyna wirtualna jest chroniona i w dobrej kondycji.
3. Po uruchomieniu testowego przejścia w tryb failover wybierz sieć wirtualną platformy Azure w regionie docelowym. Maszyna wirtualna platformy Azure utworzona po przejściu do trybu failover zostanie umieszczona w tej sieci. 

    - W tym samouczku wybieramy istniejącą sieć podczas uruchamiania testowej pracy w trybie failover.
    - Zalecamy wybranie sieci nieprodukcyjnej do przechodzenia do szczegółów, dzięki czemu adresy IP i składniki sieciowe pozostają dostępne w sieciach produkcyjnych.
   - Możesz również wstępnie skonfigurować ustawienia sieciowe do użycia na potrzeby testowego przełączania do trybu failover. Szczegółowe ustawienia, które można przypisać dla każdej karty sieciowej, obejmują podsieć, prywatny adres IP, publiczny adres IP, moduł równoważenia obciążenia i sieciową grupę zabezpieczeń. Ta metoda nie jest używana w tym miejscu, ale możesz [zapoznać się z tym artykułem](azure-to-azure-customize-networking.md#customize-failover-and-test-failover-networking-configurations) , aby dowiedzieć się więcej.


## <a name="run-a-test-failover"></a>Wykonywanie próby przejścia w tryb failover


1. Na stronie **Przegląd** wybierz pozycję **test pracy w trybie failover**.

    
    ![Przycisk Test pracy w trybie failover dla zreplikowanego elementu](./media/azure-to-azure-tutorial-dr-drill/test-failover-button.png)

2. W obszarze **test pracy w trybie failover** wybierz punkt odzyskiwania. Maszyna wirtualna platformy Azure w regionie docelowym jest tworzona przy użyciu danych z tego punktu odzyskiwania.
  
   - **Ostatnio przetwarzane** : używa najnowszego punktu odzyskiwania przetworzonego przez Site Recovery. Wyświetlana jest sygnatura czasowa. Brak czasu poświęcanego na przetwarzanie danych, więc zapewnia niski cel czasu odzyskiwania (RTO).
   -  **Najnowsze** : przetwarza wszystkie dane wysyłane do Site Recovery, aby utworzyć punkt odzyskiwania dla każdej maszyny wirtualnej przed przełączeniem jej w tryb failover. Zapewnia najniższy cel punktu odzyskiwania (RPO), ponieważ wszystkie dane są replikowane do Site Recovery podczas wyzwolenia trybu failover.
   - **Najnowsza spójna dla aplikacji** : Ta opcja powoduje przełączenie maszyny wirtualnej w tryb failover do najnowszego punktu odzyskiwania spójnego z aplikacją. Wyświetlana jest sygnatura czasowa.
   - **Niestandardowa** : przełączenie w tryb failover do określonego punktu odzyskiwania. Element Custom jest dostępny tylko po przełączeniu w tryb failover pojedynczej maszyny wirtualnej i nie korzysta z planu odzyskiwania.

3. W obszarze **Azure Virtual Network** wybierz sieć docelową, w której chcesz umieścić maszyny wirtualne platformy Azure utworzone po przejściu do trybu failover. Wybierz sieć nieprodukcyjną, jeśli jest to możliwe, a nie sieci, która została utworzona podczas włączania replikacji.

    ![Strona ustawień testu pracy w trybie failover](./media/azure-to-azure-tutorial-dr-drill/test-failover-settings.png)    

4. Aby rozpocząć pracę w trybie failover, wybierz **przycisk OK**.
5. Monitoruj test pracy w trybie failover w powiadomieniach.

    ![Powiadomienie o ](./media/azure-to-azure-tutorial-dr-drill/notification-start-test-failover.png) powodzeniu powiadomienia o postępie ![](./media/azure-to-azure-tutorial-dr-drill/notification-finish-test-failover.png)     


5. Po zakończeniu pracy w trybie failover maszyna wirtualna platformy Azure utworzona w regionie docelowym zostanie wyświetlona w **Virtual Machines** Azure Portal. Upewnij się, że maszyna wirtualna jest uruchomiona o odpowiednim rozmiarze i połączona z wybraną siecią.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

1. Na stronie **podstawowe** wybierz pozycję **Oczyść test pracy w trybie failover**.

    ![Przycisk umożliwiający rozpoczęcie procesu oczyszczania](./media/azure-to-azure-tutorial-dr-drill/select-cleanup.png)

2. W obszarze notatki dotyczące **oczyszczania w trybie failover**  >  **Notes** należy zarejestrować i zapisać wszelkie obserwacje związane z testem pracy w trybie failover. 
3. Wybierz opcję **testowanie zostało zakończone** , aby usunąć maszyny wirtualne utworzone podczas testu pracy w trybie failover.

    ![Strona z opcjami czyszczenia](./media/azure-to-azure-tutorial-dr-drill/cleanup-failover.png)

4. Monitoruj postęp oczyszczania w powiadomieniach.

    ![](./media/azure-to-azure-tutorial-dr-drill/notification-start-cleanup.png) ![ Powiadomienie o powodzeniu oczyszczania powiadomienia o postępie oczyszczania](./media/azure-to-azure-tutorial-dr-drill/notification-finish-cleanup.png)

## <a name="next-steps"></a>Następne kroki

W tym samouczku wykonano przechodzenie do szczegółów odzyskiwania po awarii w celu sprawdzenia, czy tryb failover działa zgodnie z oczekiwaniami. Teraz można wypróbować pełną pracę w trybie failover.

> [!div class="nextstepaction"]
> [Uruchamianie produkcyjnego trybu failover](azure-to-azure-tutorial-failover-failback.md)
