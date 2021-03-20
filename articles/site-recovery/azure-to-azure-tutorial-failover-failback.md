---
title: Samouczek dotyczący przełączania maszyn wirtualnych platformy Azure w tryb failover w regionie pomocniczym odzyskiwania po awarii z Azure Site Recovery.
description: Samouczek, aby dowiedzieć się, jak przełączyć się w tryb failover i ponownie chronić maszyny wirtualne platformy Azure zreplikowane do pomocniczego regionu platformy Azure na potrzeby odzyskiwania po awarii, z usługą Azure Site Recovery.
ms.topic: tutorial
ms.date: 11/05/2020
ms.custom: mvc
ms.openlocfilehash: 99263c83d25542073d63c1cba394a147bd5b2170
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93392782"
---
# <a name="tutorial-fail-over-azure-vms-to-a-secondary-region"></a>Samouczek: praca awaryjna maszyn wirtualnych platformy Azure w regionie pomocniczym

Dowiedz się, jak przełączać maszyny wirtualne platformy Azure, które są włączone na potrzeby odzyskiwania po awarii w usłudze [Azure Site Recovery](site-recovery-overview.md), do pomocniczego regionu platformy Azure. Po przejściu w tryb failover należy ponownie włączyć ochronę maszyn wirtualnych w regionie docelowym, aby były one replikowane z powrotem do regionu podstawowego. W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Sprawdzanie wymagań wstępnych
> * Sprawdź ustawienia maszyny wirtualnej
> * Uruchamianie trybu failover w regionie pomocniczym
> * Rozpocznij replikację maszyny wirtualnej z powrotem do regionu podstawowego.


> [!NOTE]
> W tym samouczku pokazano, jak przełączać maszyny wirtualne w tryb failover z minimalnymi krokami. Jeśli chcesz uruchomić tryb failover z pełnymi ustawieniami, zapoznaj się z tematem [Obsługa sieci](azure-to-azure-about-networking.md)maszyn wirtualnych platformy Azure, [Automatyzacja](azure-to-azure-powershell.md)i [Rozwiązywanie problemów](azure-to-azure-troubleshoot-errors.md).



## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem pracy z tym samouczkiem należy:

1. Skonfiguruj replikację dla co najmniej jednej maszyny wirtualnej platformy Azure. Jeśli jeszcze tego nie zrobiono, [Wykonaj pierwszy samouczek](azure-to-azure-tutorial-enable-replication.md) w tej serii, aby to zrobić.
2. Zalecamy [wykonanie drążenia odzyskiwania po awarii](azure-to-azure-tutorial-dr-drill.md) dla zreplikowanych maszyn wirtualnych. Uruchomienie przechodzenia do szczegółów przed uruchomieniem pełnej pracy w trybie failover pozwala upewnić się, że wszystko działa zgodnie z oczekiwaniami, bez wpływu na środowisko produkcyjne. 


## <a name="verify-the-vm-settings"></a>Weryfikowanie ustawień maszyny wirtualnej

1. W magazynie > **zreplikowane elementy** wybierz maszynę wirtualną.

    ![Opcja otwierania właściwości maszyny wirtualnej na stronie Przegląd](./media/azure-to-azure-tutorial-failover-failback/vm-settings.png)

2. Przed uruchomieniem trybu failover na stronie **Przegląd** maszyny wirtualnej Sprawdź, czy maszyna wirtualna jest chroniona i w dobrej kondycji.
    ![Strona, aby zweryfikować właściwości i stan maszyny wirtualnej](./media/azure-to-azure-tutorial-failover-failback/vm-state.png)

3. Przed przejściem w tryb failover Sprawdź, czy:
    - Na maszynie wirtualnej działa obsługiwany system operacyjny [Windows](azure-to-azure-support-matrix.md#windows) lub [Linux](azure-to-azure-support-matrix.md#replicated-machines---linux-file-systemguest-storage) .
    - Maszyna wirtualna jest zgodna z wymaganiami dotyczącymi [obliczeń](azure-to-azure-support-matrix.md#replicated-machines---compute-settings), [magazynu](azure-to-azure-support-matrix.md#replicated-machines---storage)i [sieci](azure-to-azure-support-matrix.md#replicated-machines---networking) .

## <a name="run-a-failover"></a>Uruchamianie trybu failover


1. Na stronie **Przegląd** maszyny wirtualnej wybierz pozycję **tryb failover**.

    ![Przycisk trybu failover dla zreplikowanego elementu](./media/azure-to-azure-tutorial-failover-failback/failover-button.png)

3. W obszarze **tryb failover** wybierz punkt odzyskiwania. Maszyna wirtualna platformy Azure w regionie docelowym jest tworzona przy użyciu danych z tego punktu odzyskiwania.
  
   - **Ostatnio przetwarzane**: używa najnowszego punktu odzyskiwania przetworzonego przez Site Recovery. Wyświetlana jest sygnatura czasowa. Brak czasu poświęcanego na przetwarzanie danych, więc zapewnia niski cel czasu odzyskiwania (RTO).
   -  **Najnowsze**: przetwarza wszystkie dane wysyłane do Site Recovery, aby utworzyć punkt odzyskiwania dla każdej maszyny wirtualnej przed przełączeniem jej w tryb failover. Zapewnia najniższy cel punktu odzyskiwania (RPO), ponieważ wszystkie dane są replikowane do Site Recovery podczas wyzwolenia trybu failover.
   - **Najnowsza spójna dla aplikacji**: Ta opcja powoduje przełączenie maszyny wirtualnej w tryb failover do najnowszego punktu odzyskiwania spójnego z aplikacją. Wyświetlana jest sygnatura czasowa.
   - **Niestandardowa**: przełączenie w tryb failover do określonego punktu odzyskiwania. Element Custom jest dostępny tylko po przełączeniu w tryb failover pojedynczej maszyny wirtualnej i nie korzysta z planu odzyskiwania.

    > [!NOTE]
    > W przypadku dodania dysku do maszyny wirtualnej po włączeniu replikacji punkty replikacji przedstawiają dyski dostępne do odzyskania. Na przykład punkt replikacji utworzony przed dodaniem drugiego dysku będzie widoczny jako "1 z 2 dysków".

4. Wybierz opcję **Zamknij maszynę przed rozpoczęciem pracy w trybie failover** , jeśli chcesz, aby Site Recovery próby zamknięcia źródłowych maszyn wirtualnych przed rozpoczęciem pracy w trybie failover. Zamknięcie pomaga zapewnić brak utracie danych. Przełączanie do trybu failover będzie kontynuowane, nawet jeśli zamknięcie nie powiedzie się. 

    ![Strona ustawień trybu failover](./media/azure-to-azure-tutorial-failover-failback/failover-settings.png)    

3. Aby rozpocząć pracę w trybie failover, wybierz **przycisk OK**.
4. Monitoruj tryb failover w powiadomieniach.

    ![Powiadomienie o ](./media/azure-to-azure-tutorial-failover-failback/notification-failover-start.png) powodzeniu powiadomienia o postępie ![](./media/azure-to-azure-tutorial-failover-failback/notification-failover-finish.png)     

5. Po przejściu w tryb failover maszyna wirtualna platformy Azure utworzona w regionie docelowym zostanie wyświetlona w **Virtual Machines**. Upewnij się, że maszyna wirtualna jest uruchomiona i ma odpowiednie rozmiary. Jeśli chcesz użyć innego punktu odzyskiwania dla maszyny wirtualnej, wybierz pozycję **Zmień punkt odzyskiwania** na stronie **podstawy** .
6. Po zakończeniu pracy awaryjnej maszyny wirtualnej wybierz pozycję **Zatwierdź** na stronie Przegląd, aby zakończyć pracę w trybie failover.

    ![Przycisk Zatwierdź](./media/azure-to-azure-tutorial-failover-failback/commit-button.png) 

7. W obszarze **zatwierdzenie** wybierz pozycję **OK** , aby potwierdzić. Zatwierdzenie usuwa wszystkie dostępne punkty odzyskiwania dla maszyny wirtualnej w Site Recovery i nie będzie można zmienić punktu odzyskiwania.

8. Monitoruj postęp zatwierdzania w powiadomieniach.

    ![](./media/azure-to-azure-tutorial-failover-failback/notification-commit-start.png)Powiadomienie o powodzeniu zatwierdzenia powiadomienia o postępie zatwierdzeń ![](./media/azure-to-azure-tutorial-failover-failback/notification-commit-finish.png)    

9. Site Recovery nie czyści źródłowej maszyny wirtualnej po przejściu w tryb failover. Należy to zrobić ręcznie.


## <a name="reprotect-the-vm"></a>Ponowne włączanie ochrony maszyny wirtualnej

Po przejściu w tryb failover należy ponownie chronić maszynę wirtualną w regionie pomocniczym, tak aby była replikowana z powrotem do regionu podstawowego. 

1. Przed rozpoczęciem upewnij się, że **stan** maszyny wirtualnej to przełączenie w *tryb failover* .
2. Sprawdź, czy masz dostęp do regionu podstawowego, a także czy masz uprawnienia do tworzenia maszyn wirtualnych.
3. Na stronie **Przegląd** maszyny wirtualnej wybierz pozycję **Włącz ponownie ochronę**.

   ![Przycisk umożliwiający ponowne włączenie ochrony maszyny wirtualnej dla maszyny wirtualnej.](./media/azure-to-azure-tutorial-failover-failback/reprotect-button.png)

4. W obszarze **ponowna ochrona** sprawdź kierunek replikacji (pomocniczy do regionu podstawowego) i sprawdź ustawienia docelowe dla regionu podstawowego. Zasoby oznaczone jako nowe są tworzone przez Site Recovery w ramach operacji ponownego włączania ochrony.

     ![Strona ustawień ochrony](./media/azure-to-azure-tutorial-failover-failback/reprotect.png)

6. Wybierz **przycisk OK** , aby uruchomić proces ponownego włączania ochrony. Proces wysyła dane początkowe do lokalizacji docelowej, a następnie replikuje informacje o różnicach dla maszyn wirtualnych do miejsca docelowego.
7. Monitoruj postęp ponownego włączania ochrony w powiadomieniach. 

    ![Powiadomienia o postępie ponownego włączania ochrony powiadomień o ](./media/azure-to-azure-tutorial-failover-failback/notification-reprotect-start.png) ![ zasobie](./media/azure-to-azure-tutorial-failover-failback/notification-reprotect-finish.png)
    

## <a name="next-steps"></a>Następne kroki

W tym samouczku przełączeno w tryb failover z regionu podstawowego do pomocniczego i rozpoczęte replikację maszyn wirtualnych z powrotem do regionu podstawowego. Teraz można wrócić z regionu pomocniczego do podstawowego.

> [!div class="nextstepaction"]
> [Powrót po awarii do regionu podstawowego](azure-to-azure-tutorial-failback.md)
