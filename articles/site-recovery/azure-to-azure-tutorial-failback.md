---
title: Samouczek do powrotu maszyn wirtualnych platformy Azure do regionu podstawowego podczas odzyskiwania po awarii z Azure Site Recovery.
description: Samouczek, aby dowiedzieć się więcej o wycofywaniu maszyn wirtualnych platformy Azure do regionu podstawowego przy użyciu Azure Site Recovery.
ms.topic: tutorial
ms.date: 11/05/2020
ms.custom: mvc
ms.openlocfilehash: 5c127010a7988bf08c77340a4fc10bb32dc76f87
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93393948"
---
# <a name="tutorial-fail-back-azure-vm-to-the-primary-region"></a>Samouczek: powrót po awarii maszyny wirtualnej platformy Azure do regionu podstawowego

Po przejściu w tryb failover maszyny wirtualnej platformy Azure do pomocniczego regionu platformy Azure postępuj zgodnie z tym samouczkiem, aby zakończyć działanie maszyny wirtualnej w podstawowym regionie platformy Azure przy użyciu [Azure Site Recovery](site-recovery-overview.md).  W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> 
> * Zapoznaj się z wymaganiami wstępnymi.
> * Powrót po awarii maszyny wirtualnej w regionie pomocniczym.
> * Ponownie Włącz ochronę podstawowych maszyn wirtualnych z powrotem do regionu pomocniczego.
> 
> [!NOTE]
> W tym samouczku przedstawiono sposób powrotu po awarii z minimalnymi krokami. Jeśli chcesz uruchomić tryb failover z pełnymi ustawieniami, zapoznaj się z tematem [Obsługa sieci](azure-to-azure-about-networking.md)maszyn wirtualnych platformy Azure, [Automatyzacja](azure-to-azure-powershell.md)i [Rozwiązywanie problemów](azure-to-azure-troubleshoot-errors.md).



## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem pracy z tym samouczkiem należy:

1. [Skonfiguruj replikację](azure-to-azure-tutorial-enable-replication.md) dla co najmniej jednej maszyny wirtualnej platformy Azure i wystąpiła próba przeprowadzenia dla niej [szczegółowego odzyskiwania po awarii](azure-to-azure-tutorial-dr-drill.md) .
2. Przełączenie maszyny wirtualnej z regionu podstawowego do regionu pomocniczego [zakończyło się niepowodzeniem](azure-to-azure-tutorial-failover-failback.md) , a następnie ponownie włączyć ochronę, aby replikować z regionu pomocniczego do podstawowego. 
3. Sprawdź, czy region podstawowy jest dostępny i czy masz możliwość tworzenia nowych zasobów i uzyskiwania do nich dostępu.

## <a name="fail-back-to-the-primary-region"></a>Powrót po awarii do regionu podstawowego

Po ponownym włączeniu ochrony maszyn wirtualnych można wrócić do regionu podstawowego w razie potrzeby.

1. W magazynie > **zreplikowane elementy** wybierz maszynę wirtualną.

2. Na stronie Przegląd maszyny wirtualnej Sprawdź, czy maszyna wirtualna jest w dobrej kondycji i czy synchronizacja została ukończona przed uruchomieniem trybu failover. Maszyna wirtualna powinna być w stanie *chronionym* .

    ![Strona przegląd maszyny wirtualnej przedstawiająca maszynę wirtualną w stanie chronionym](./media/azure-to-azure-tutorial-failback/protected-state.png)

3. Na stronie Przegląd wybierz pozycję **tryb failover**. Ponieważ w tej chwili nie wykonujesz testowej pracy w trybie failover, zostanie wyświetlony monit o zweryfikowanie.

    [Na stronie wyrażamy zgodę na uruchomienie trybu failover bez testowego przejścia w tryb failover](./media/azure-to-azure-tutorial-failback/no-test.png)

4. W **trybie failover** Zanotuj kierunek od elementu pomocniczego do podstawowego, a następnie wybierz punkt odzyskiwania. Maszyna wirtualna platformy Azure w miejscu docelowym (region podstawowy) jest tworzona przy użyciu danych z tego punktu.
   - **Ostatnio przetwarzane**: używa najnowszego punktu odzyskiwania przetworzonego przez Site Recovery. Wyświetlana jest sygnatura czasowa. Brak czasu poświęcanego na przetwarzanie danych, więc zapewnia niski cel czasu odzyskiwania (RTO).
   -  **Najnowsze**: przetwarza wszystkie dane wysyłane do Site Recovery, aby utworzyć punkt odzyskiwania dla każdej maszyny wirtualnej przed przełączeniem jej w tryb failover. Zapewnia najniższy cel punktu odzyskiwania (RPO), ponieważ wszystkie dane są replikowane do Site Recovery podczas wyzwolenia trybu failover.
   - **Najnowsza spójna dla aplikacji**: Ta opcja powoduje przełączenie maszyny wirtualnej w tryb failover do najnowszego punktu odzyskiwania spójnego z aplikacją. Wyświetlana jest sygnatura czasowa.
   - **Niestandardowa**: przełączenie w tryb failover do określonego punktu odzyskiwania. Element Custom jest dostępny tylko po przełączeniu w tryb failover pojedynczej maszyny wirtualnej i nie korzysta z planu odzyskiwania.

    > [!NOTE]
    > W przypadku przełączenia w tryb failover maszyny wirtualnej, do której dodano dysk po włączeniu replikacji dla maszyny wirtualnej, punkty replikacji będą zawierać dyski dostępne do odzyskania. Na przykład punkt replikacji, który został utworzony przed dodaniem drugiego dysku, będzie widoczny jako "1 z 2 dysków".

4. Wybierz opcję **Zamknij maszynę przed rozpoczęciem pracy w trybie failover** , jeśli chcesz, aby Site Recovery próbuje zamknąć źródłowe maszyny wirtualne przed rozpoczęciem pracy w trybie failover. Zamknięcie pomaga zapewnić brak utracie danych. Przełączanie do trybu failover będzie kontynuowane, nawet jeśli zamknięcie nie powiedzie się. 

    ![Strona ustawień trybu failover](./media/azure-to-azure-tutorial-failback/failover.png)    

3. Aby rozpocząć pracę w trybie failover, wybierz **przycisk OK**.
4. Monitoruj tryb failover w powiadomieniach.

    ![Powiadomienie o postępie pracy w trybie failover](./media/azure-to-azure-tutorial-failback/notification-progress.png)  
    ![Powiadomienie o pomyślnym przejściu w tryb failover](./media/azure-to-azure-tutorial-failback/notification-success.png)   

## <a name="reprotect-vms"></a>Ponowne włączanie ochrony maszyn wirtualnych

Po zakończeniu powrotu maszyn wirtualnych do regionu podstawowego należy ponownie włączyć ich ochronę, aby umożliwić ich ponowne uruchomienie replikacji do regionu pomocniczego.

1. Na stronie **Przegląd** dla maszyny wirtualnej wybierz pozycję **Włącz ponownie ochronę**.

    ![Przycisk umożliwiający ponowne włączenie ochrony z regionu podstawowego](./media/azure-to-azure-tutorial-failback/reprotect.png)  

2. Sprawdź ustawienia docelowe dla regionu podstawowego. Zasoby oznaczone jako nowe są tworzone przez Site Recovery w ramach operacji ponownego włączania ochrony.
3. Wybierz **przycisk OK** , aby uruchomić proces ponownego włączania ochrony. Proces wysyła dane początkowe do lokalizacji docelowej, a następnie replikuje informacje o różnicach dla maszyn wirtualnych do miejsca docelowego.

     ![Strona przedstawiająca ustawienia replikacji](./media/azure-to-azure-tutorial-failback/replication-settings.png) 

4. Monitoruj postęp ponownego włączania ochrony w powiadomieniach. 

    ![](./media/azure-to-azure-tutorial-failback/notification-reprotect-start.png) [Powiadomienie o](./media/azure-to-azure-tutorial-failback/notification-reprotect-finish.png) postępie ponownej ochrony powiadomień o postępie
    
  

## <a name="clean-up-resources"></a>Czyszczenie zasobów

W przypadku maszyn wirtualnych z dyskami zarządzanymi po zakończeniu powrotu po awarii i ponownej ochrony maszyn wirtualnych na potrzeby replikacji z podstawowego do pomocniczego program Site Recovery automatycznie czyści maszyny w regionie pomocniczego odzyskiwania po awarii. Nie musisz ręcznie usuwać maszyn wirtualnych i kart sieciowych w regionie pomocniczym. Maszyny wirtualne z dyskami niezarządzanymi nie są czyszczone.

W przypadku całkowitego wyłączenia replikacji po powrocie po awarii program Site Recovery czyści maszyny chronione przez nią. W tym przypadku czyści również dyski dla maszyn wirtualnych, które nie korzystają z dysków zarządzanych. 
 
## <a name="next-steps"></a>Następne kroki

W tym samouczku nie powiodło się przywrócenie maszyn wirtualnych z regionu pomocniczego do podstawowego. Jest to ostatni krok w procesie, który obejmuje włączanie replikacji dla maszyny wirtualnej, próba przechodzenia do szczegółów odzyskiwania po awarii, przejście w tryb failover z regionu podstawowego do pomocniczego i zakończenie powrotu po awarii.

> [!div class="nextstepaction"]
> Teraz wypróbuj odzyskiwanie po awarii na platformie Azure dla [lokalnej maszyny wirtualnej](vmware-azure-tutorial-prepare-on-premises.md)

