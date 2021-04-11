---
title: Samouczek — Konfigurowanie odzyskiwania po awarii dla maszyn wirtualnych z systemem Linux za pomocą Azure Site Recovery
description: Dowiedz się, jak skonfigurować odzyskiwanie po awarii dla maszyn wirtualnych z systemem Linux w innym regionie platformy Azure przy użyciu usługi Azure Site Recovery.
author: rayne-wiselman
ms.service: virtual-machines
ms.collection: linux
ms.subservice: recovery
ms.topic: tutorial
ms.date: 11/05/2020
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: b5e83f883b5e1e35842ab128e4732e993fb937a0
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2021
ms.locfileid: "106383697"
---
# <a name="tutorial-set-up-disaster-recovery-for-linux-virtual-machines"></a>Samouczek: Konfigurowanie odzyskiwania po awarii dla maszyn wirtualnych z systemem Linux

W tym samouczku pokazano, jak skonfigurować odzyskiwanie po awarii dla maszyn wirtualnych platformy Azure z systemem Linux. W tym artykule dowiesz się, jak:

> [!div class="checklist"]
> * Włączanie odzyskiwania po awarii dla maszyny wirtualnej z systemem Linux
> * Uruchom drążenie odzyskiwania po awarii, aby sprawdzić, czy działa zgodnie z oczekiwaniami
> * Zatrzymaj replikację maszyny wirtualnej po przejściu do szczegółów

Po włączeniu replikacji dla maszyny wirtualnej rozszerzenie usługi mobilności Site Recovery zostanie zainstalowane na maszynie wirtualnej i zarejestruje ją z [Azure Site Recovery](../../site-recovery/site-recovery-overview.md). Podczas replikacji operacje zapisu na dyskach maszyn wirtualnych są wysyłane do konta magazynu pamięci podręcznej w regionie źródłowej maszyny wirtualnej. Dane są wysyłane z lokalizacji do regionu docelowego, a punkty odzyskiwania są generowane na podstawie danych.  Po awarii maszyny wirtualnej w tryb failover do innego regionu podczas odzyskiwania po awarii punkt odzyskiwania jest używany do tworzenia maszyny wirtualnej w regionie docelowym.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/).

## <a name="prerequisites"></a>Wymagania wstępne

1. Sprawdź, czy Twoja subskrypcja platformy Azure umożliwia tworzenie maszyn wirtualnych w regionie docelowym. Jeśli właśnie utworzono bezpłatne konto platformy Azure, jesteś administratorem subskrypcji i masz wymagane uprawnienia.
2. Jeśli nie jesteś administratorem subskrypcji, skontaktuj się z administratorem w celu przypisania Cię:
    - Wbudowana rola współautor maszyny wirtualnej lub określone uprawnienia do:
        - Tworzenie maszyny wirtualnej w wybranej sieci wirtualnej.
        - Zapisz na koncie usługi Azure Storage.
        - Zapisz na dysku zarządzanym platformy Azure.
     - Wbudowana rola współautora Site Recovery do zarządzania operacjami Site Recovery w magazynie. 
3. Sprawdź, czy na maszynie wirtualnej z systemem Linux jest uruchomiony [obsługiwany system operacyjny](../../site-recovery/azure-to-azure-support-matrix.md#linux).
4. Jeśli połączenia wychodzące maszyny wirtualnej używają serwera proxy opartego na adresie URL, upewnij się, że mogą uzyskać dostęp do tych adresów URL. Używanie uwierzytelnionego serwera proxy nie jest obsługiwane.

    **Nazwa** | **Chmura publiczna** | **Chmura dla instytucji rządowych** | **Szczegóły**
    --- | --- | --- | ---
    Storage | `*.blob.core.windows.net` | `*.blob.core.usgovcloudapi.net`| Zapisz dane z maszyny wirtualnej na koncie magazynu pamięci podręcznej w regionie źródłowym. 
    Azure AD  | `login.microsoftonline.com` | `login.microsoftonline.us`| Autoryzuj i uwierzytelniaj w celu Site Recovery adresów URL usługi. 
    Replikacja | `*.hypervrecoverymanager.windowsazure.com` | `*.hypervrecoverymanager.windowsazure.com`  |Komunikacja maszyny wirtualnej z usługą Site Recovery. 
    Service Bus | `*.servicebus.windows.net` | `*.servicebus.usgovcloudapi.net` | Zapisywanie maszyny wirtualnej w celu Site Recovery na potrzeby monitorowania i danych diagnostycznych. 

4. Jeśli używasz sieciowych grup zabezpieczeń (sieciowych grup zabezpieczeń) do ograniczania ruchu sieciowego dla maszyn wirtualnych, utwórz reguły sieciowej grupy zabezpieczeń zezwalające na połączenia wychodzące (HTTPS 443) dla maszyny wirtualnej korzystającej z tych tagów usługi (grup adresów IP). Wypróbuj najpierw reguły dla testu sieciowej grupy zabezpieczeń.

    **Tag** | **Zezwalaj** 
    --- | --- 
    Tag magazynu | Umożliwia zapisanie danych z maszyny wirtualnej na koncie magazynu pamięci podręcznej.
    Tag usługi Azure AD | Zezwala na dostęp do wszystkich adresów IP odnoszące się do usługi Azure AD.
    Tag EventsHub | Umożliwia dostęp do monitorowania Site Recovery.
    Tag AzureSiteRecovery | Zezwala na dostęp do usługi Site Recovery w dowolnym regionie.
    GuestAndHybridManagement | Użyj, jeśli chcesz automatycznie uaktualnić agenta mobilności Site Recovery, który jest uruchomiony na maszynach wirtualnych obsługujących replikację.
5. Upewnij się, że maszyny wirtualne mają najnowsze certyfikaty główne. Na maszynach wirtualnych z systemem Linux postępuj zgodnie ze wskazówkami dostarczonymi przez dystrybutora systemu Linux, aby uzyskać najnowsze zaufane certyfikaty główne i listę odwołania certyfikatów na maszynie wirtualnej.

## <a name="create-a-vm-and-enable-disaster-recovery"></a>Tworzenie maszyny wirtualnej i włączanie odzyskiwania po awarii

Opcjonalnie można włączyć odzyskiwanie po awarii podczas tworzenia maszyny wirtualnej.

1. [Tworzenie maszyny wirtualnej z systemem Linux](quick-create-portal.md).
2. Na karcie **Zarządzanie** w obszarze **Site Recovery** wybierz opcję **Włącz odzyskiwanie po awarii**.
3. W **obszarze region pomocniczy** wybierz region docelowy, do którego chcesz replikować maszynę wirtualną w celu odzyskania po awarii.
4. W obszarze **subskrypcja pomocnicza** wybierz subskrypcję docelową, w której zostanie utworzona docelowa maszyna wirtualna. Docelowa maszyna wirtualna jest tworzona po przełączeniu w tryb failover źródłowej maszyny wirtualnej z regionu źródłowego do regionu docelowego.
5. W obszarze **magazyn Recovery Services** wybierz magazyn, który ma być używany na potrzeby replikacji. Jeśli nie masz magazynu, wybierz pozycję **Utwórz nowy**. Wybierz grupę zasobów, w której chcesz umieścić magazyn, oraz nazwę magazynu.
6. W obszarze **zasady Site Recovery** pozostaw zasady domyślne lub wybierz pozycję **Utwórz nową** , aby ustawić wartości niestandardowe.

    - Punkty odzyskiwania są tworzone na podstawie migawek dysków maszyny wirtualnej wykonanych w określonym punkcie w czasie. Po przełączeniu maszyny wirtualnej w tryb failover należy użyć punktu odzyskiwania, aby przywrócić maszynę wirtualną w regionie docelowym. 
    - Punkt odzyskiwania spójny na poziomie awarii jest tworzony co pięć minut. Nie można zmodyfikować tego ustawienia. Migawka spójna pod kątem awarii przechwytuje dane znajdujące się na dysku podczas tworzenia migawki. Nie zawiera żadnych elementów w pamięci. 
    - Domyślnie Site Recovery zachowuje punkty odzyskiwania spójne z awarią przez 24 godziny. Można ustawić wartość niestandardową z zakresu od 0 do 72 godzin.
    - Migawka spójna na poziomie aplikacji jest wykonywana co 4 godziny.
    - Domyślnie Site Recovery są przechowywane punkty odzyskiwania przez 24 godziny.

7. W obszarze **Opcje dostępności** Określ, czy maszyna wirtualna jest wdrażana jako autonomiczna, w strefie dostępności, czy w zestawie dostępności.

    :::image type="content" source="./media/tutorial-disaster-recovery/create-vm.png" alt-text="Włącz replikację na stronie właściwości zarządzania maszyną wirtualną.":::

8. Zakończ tworzenie maszyny wirtualnej.

## <a name="enable-disaster-recovery-for-an-existing-vm"></a>Włączanie odzyskiwania po awarii dla istniejącej maszyny wirtualnej

Jeśli chcesz włączyć odzyskiwanie po awarii na istniejącej maszynie wirtualnej, Użyj tej procedury.

1. W Azure Portal Otwórz stronę właściwości maszyny wirtualnej.
2. W obszarze **Operacja** wybierz pozycję **Odzyskiwanie po awarii**.

    :::image type="content" source="./media/tutorial-disaster-recovery/existing-vm.png" alt-text="Otwórz Opcje odzyskiwania po awarii dla istniejącej maszyny wirtualnej.":::

3. Jeśli maszyna wirtualna jest wdrożona w strefie **dostępności, można** wybrać opcję odzyskiwanie awaryjne między strefami dostępności.
4. W **obszarze region docelowy** wybierz region, do którego chcesz replikować maszynę wirtualną. Regiony źródłowe i docelowe muszą należeć do tej samej Azure Active Directory dzierżawy.

    :::image type="content" source="./media/tutorial-disaster-recovery/basics.png" alt-text="Ustaw podstawowe opcje odzyskiwania po awarii dla maszyny wirtualnej.":::

5. Wybierz pozycję **Dalej: Ustawienia zaawansowane**.
6. W obszarze **Ustawienia zaawansowane** można przeglądać ustawienia i modyfikować wartości w ustawieniach niestandardowych. Domyślnie Site Recovery odzwierciedla ustawienia źródła w celu tworzenia zasobów docelowych.

    - **Subskrypcja docelowa**. Subskrypcja, w ramach której tworzona jest docelowa maszyna wirtualna po zakończeniu pracy w trybie failover.
    - **Docelowa Grupa zasobów maszyny wirtualnej**. Grupa zasobów, w której tworzona jest docelowa maszyna wirtualna po zakończeniu pracy w trybie failover.
    - **Docelowa sieć wirtualna**. Sieć wirtualna platformy Azure, w której znajduje się docelowa maszyna wirtualna, gdy zostanie utworzona po przejściu w tryb failover.
    - **Docelowa dostępność**. Gdy docelowa maszyna wirtualna jest tworzona jako pojedyncze wystąpienie, w zestawie dostępności lub strefie dostępności.
    - **Położenie sąsiedztwa**. W razie potrzeby wybierz grupę umieszczania bliskości, w której znajduje się docelowa maszyna wirtualna po zakończeniu pracy w trybie failover.
    - **Ustawienia magazynu — konto magazynu pamięci podręcznej**. Odzyskiwanie używa konta magazynu w regionie źródłowym jako tymczasowego magazynu danych. Zmiany źródłowej maszyny wirtualnej są buforowane na tym koncie przed replikacją do lokalizacji docelowej.
        - Domyślnie jedno konto magazynu pamięci podręcznej jest tworzone na magazyn i ponownie używane.
        - Jeśli chcesz dostosować konto pamięci podręcznej dla maszyny wirtualnej, możesz wybrać inne konto magazynu.
    - **Ustawienia magazynu — dysk zarządzany repliki**. Domyślnie program Site Recovery tworzy dyski zarządzane repliki w regionie docelowym.
        -  Domyślnie docelowy dysk zarządzany jest dublowany dla źródłowych dysków zarządzanych maszyn wirtualnych, przy użyciu tego samego typu magazynu (standardowy dysk twardy/SSD lub dysk SSD Premium).
        - Typ magazynu można dostosować zgodnie z wymaganiami.
    - **Ustawienia replikacji**. Pokazuje magazyn, w którym znajduje się maszyna wirtualna, oraz zasady replikacji używane dla maszyny wirtualnej. Domyślnie punkty odzyskiwania utworzone przez Site Recovery dla maszyny wirtualnej są przechowywane przez 24 godziny.
    - **Ustawienia rozszerzenia**. Wskazuje, że Site Recovery zarządza aktualizacjami rozszerzenia usługi mobilności Site Recovery zainstalowanego na replikowanych maszynach wirtualnych.
        - Wskazane konto usługi Azure Automation zarządza procesem aktualizacji.
        - Można dostosować konto usługi Automation.

    :::image type="content" source="./media/tutorial-disaster-recovery/settings-summary.png" alt-text="Zostanie wyświetlona strona podsumowanie ustawień lokalizacji docelowej i replikacji.":::

6. Wybierz kolejno pozycje **Recenzja + Uruchom replikację**.

7. Wybierz pozycję **Rozpocznij replikację**. Rozpocznie się wdrażanie, a Site Recovery zaczyna tworzyć zasoby docelowe. W powiadomieniach można monitorować postęp replikacji.

    :::image type="content" source="./media/tutorial-disaster-recovery/notifications.png" alt-text="Powiadomienie o postępie replikacji.":::

## <a name="check-vm-status"></a>Sprawdź stan maszyny wirtualnej

Po zakończeniu zadania replikacji można sprawdzić stan replikacji maszyny wirtualnej.

1. Otwórz stronę właściwości maszyny wirtualnej.
2. W obszarze **Operacja** wybierz pozycję **Odzyskiwanie po awarii**.
3. Rozwiń sekcję **podstawowe** , aby przejrzeć ustawienia domyślne dotyczące magazynu, zasad replikacji i ustawień docelowych.
4. W obszarze **kondycja i stan** Pobierz informacje o stanie replikacji maszyny wirtualnej, wersji agenta, gotowości do przejścia w tryb failover i najnowszych punktach odzyskiwania. 

    :::image type="content" source="./media/tutorial-disaster-recovery/essentials.png" alt-text="Widok podstawy odzyskiwania po awarii maszyny wirtualnej.":::

5. W **widoku infrastruktura** zapoznaj się z wizualną wizualizacją źródłową i docelową maszyn wirtualnych, dysków zarządzanych oraz konta magazynu pamięci podręcznej.

    :::image type="content" source="./media/tutorial-disaster-recovery/infrastructure.png" alt-text="Mapa wizualizacji infrastruktury na potrzeby odzyskiwania po awarii maszyny wirtualnej.":::

## <a name="run-a-drill"></a>Uruchom przechodzenie do szczegółów

Uruchom przechodzenie do szczegółów, aby upewnić się, że odzyskiwanie awaryjne działa zgodnie z oczekiwaniami. Po uruchomieniu testowego trybu failover program tworzy kopię maszyny wirtualnej bez wpływu na bieżącą replikację lub w środowisku produkcyjnym.

1. Na stronie odzyskiwania po awarii maszyny wirtualnej wybierz pozycję **test pracy w trybie failover**.
2. W obszarze **test pracy w trybie failover** Pozostaw domyślne ustawienie **ostatniego przetworzonego (niskiego RPO)** dla punktu odzyskiwania.

   Ta opcja zapewnia najniższy cel punktu odzyskiwania (RPO) i ogólnie obraca maszynę wirtualną w regionie docelowym. Najpierw przetwarza wszystkie dane, które zostały wysłane do usługi Site Recovery, aby utworzyć punkt odzyskiwania dla każdej maszyny wirtualnej przed przejściem w tryb failover. Ten punkt odzyskiwania ma wszystkie dane zreplikowane do Site Recovery podczas wyzwolenia trybu failover.

3. Wybierz sieć wirtualną, w której zostanie umieszczona maszyna wirtualna po zakończeniu pracy w trybie failover. 

     :::image type="content" source="./media/tutorial-disaster-recovery/test-failover-settings.png" alt-text="Strona umożliwiająca ustawienie opcji testowej pracy w trybie failover.":::

4. Rozpocznie się proces testowego przełączania do trybu failover. Postęp można monitorować w powiadomieniach.

    :::image type="content" source="./media/tutorial-disaster-recovery/test-failover-notification.png" alt-text="Testowe powiadomienia w trybie failover."::: 
    
   Po zakończeniu testowej pracy w trybie failover maszyna wirtualna jest w stanie oczekiwania na przełączenie do *trybu failover testu oczyszczania* na stronie **podstawy** . 
  
## <a name="clean-up-resources"></a>Czyszczenie zasobów

Maszyna wirtualna jest automatycznie czyszczona przez Site Recovery po przeprowadzeniu przechodzenia do szczegółów. 
 
1. Aby rozpocząć automatyczne czyszczenie, wybierz pozycję **Oczyść test pracy w trybie failover**.

    :::image type="content" source="./media/tutorial-disaster-recovery/start-cleanup.png" alt-text="Rozpocznij czyszczenie na stronie podstawowe informacje."::: 

6. W obszarze **czyszczenie testu pracy w trybie failover** wpisz wszelkie uwagi, które chcesz zarejestrować w trybie failover, a następnie wybierz opcję **testowanie zostało zakończone. Usuń testową maszynę wirtualną w trybie failover**. Następnie wybierz przycisk **OK**.

    :::image type="content" source="./media/tutorial-disaster-recovery/delete-test.png" alt-text="Strona do rejestrowania notatek i usuwania testowej maszyny wirtualnej."::: 

7. Rozpocznie się proces usuwania. Możesz monitorować postęp w powiadomieniach.

    :::image type="content" source="./media/tutorial-disaster-recovery/delete-test-notification.png" alt-text="Powiadomienia do monitorowania testowej maszyny wirtualnej."::: 


### <a name="stop-replicating-the-vm"></a>Zatrzymaj replikację maszyny wirtualnej

Po zakończeniu przechodzenia do szczegółów odzyskiwania po awarii zalecamy dalsze wypróbowanie pełnego trybu failover. Jeśli nie chcesz wykonać pełnego trybu failover, możesz wyłączyć replikację. Spowoduje to wykonanie następujących czynności:

- Usuwa maszynę wirtualną z listy Site Recovery replikowanych maszyn.
- Powoduje zatrzymanie Site Recovery rozliczeń dla maszyny wirtualnej.
- Automatycznie czyści ustawienia replikacji źródłowej.

Aby zatrzymać replikację:

1. Na stronie odzyskiwania po awarii maszyny wirtualnej wybierz pozycję **Wyłącz replikację**.
2. W obszarze **Wyłącz replikację** wybierz przyczyny wyłączenia replikacji. Następnie wybierz przycisk **OK**.

    :::image type="content" source="./media/tutorial-disaster-recovery/disable-replication.png" alt-text="Na stronie aby wyłączyć replikację i podać przyczynę."::: 


Rozszerzenie Site Recovery zainstalowane na maszynie wirtualnej podczas replikacji nie zostanie automatycznie usunięte. Jeśli wyłączysz replikację maszyny wirtualnej i nie chcesz jej ponownie zreplikować, możesz usunąć rozszerzenie Site Recovery ręcznie, wykonując następujące czynności: 

1. Przejdź do rozszerzeń **ustawień**> maszyny wirtualnej  >  .
2. Na stronie **rozszerzenia** zaznacz każdy wpis *Microsoft. Azure. RecoveryServices* dla systemu Linux.
3. Na stronie właściwości rozszerzenia wybierz pozycję **Odinstaluj**.


## <a name="next-steps"></a>Następne kroki

W tym samouczku skonfigurowano odzyskiwanie po awarii dla maszyny wirtualnej platformy Azure i uruchomiono przechodzenie do szczegółów odzyskiwania po awarii. Teraz można wykonać pełną pracę w trybie failover dla maszyny wirtualnej.

> [!div class="nextstepaction"]
> [Przełączanie maszyny wirtualnej do pracy w trybie failover w innym regionie](../../site-recovery/azure-to-azure-tutorial-dr-drill.md)
