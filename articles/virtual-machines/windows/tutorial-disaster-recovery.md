---
title: Samouczek — Konfigurowanie odzyskiwania po awarii dla maszyn wirtualnych z systemem Windows za pomocą Azure Site Recovery
description: Dowiedz się, jak włączyć odzyskiwanie po awarii dla maszyn wirtualnych z systemem Windows w innym regionie platformy Azure przy użyciu usługi Azure Site Recovery.
author: rayne-wiselman
ms.service: virtual-machines
ms.collection: windows
ms.subservice: recovery
ms.topic: tutorial
ms.date: 11/05/2020
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: e9f44ea2af832729a47bf4b719b90f9b14e401b9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102555860"
---
# <a name="tutorial-enable-disaster-recovery-for-windows-vms"></a>Samouczek: Włączanie odzyskiwania po awarii dla maszyn wirtualnych z systemem Windows

W tym samouczku pokazano, jak skonfigurować odzyskiwanie po awarii dla maszyn wirtualnych platformy Azure z systemem Windows. W tym artykule dowiesz się, jak:

> [!div class="checklist"]
> * Włączanie odzyskiwania po awarii dla maszyny wirtualnej z systemem Windows
> * Uruchamianie próbnego odzyskiwania po awarii
> * Zatrzymaj replikację maszyny wirtualnej po przejściu do szczegółów

Po włączeniu replikacji dla maszyny wirtualnej rozszerzenie usługi mobilności Site Recovery zostanie zainstalowane na maszynie wirtualnej i zarejestruje ją z [Azure Site Recovery](../../site-recovery/site-recovery-overview.md). Podczas replikacji zapisy z dysku maszyny wirtualnej są wysyłane do konta magazynu pamięci podręcznej w regionie źródłowym. Dane są wysyłane z lokalizacji do regionu docelowego, a punkty odzyskiwania są generowane na podstawie danych.  Po przełączeniu maszyny wirtualnej w tryb failover podczas odzyskiwania po awarii punkt odzyskiwania jest używany do przywracania maszyny wirtualnej w regionie docelowym.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/).

## <a name="prerequisites"></a>Wymagania wstępne

1. Sprawdź, czy Twoja subskrypcja platformy Azure umożliwia tworzenie maszyn wirtualnych w regionie docelowym. Jeśli właśnie utworzono bezpłatne konto platformy Azure, jesteś administratorem subskrypcji i masz wymagane uprawnienia.
2. Jeśli nie jesteś administratorem subskrypcji, skontaktuj się z administratorem w celu przypisania Cię:
    - Wbudowana rola współautor maszyny wirtualnej lub określone uprawnienia do:
        - Tworzenie maszyny wirtualnej w wybranej sieci wirtualnej.
        - Zapisz na koncie usługi Azure Storage.
        - Zapisz na dysku zarządzanym przez platformę Azure.
    - Wbudowana rola współautora Site Recovery do zarządzania operacjami Site Recovery w magazynie. 
3. Zalecamy korzystanie z maszyny wirtualnej z systemem Windows Server 2012 lub nowszej. Dysk maszyny wirtualnej nie powinien być zaszyfrowany na potrzeby tego samouczka.
4. Jeśli połączenia wychodzące maszyny wirtualnej używają serwera proxy opartego na adresie URL, upewnij się, że mogą uzyskać dostęp do tych adresów URL. Używanie uwierzytelnionego serwera proxy nie jest obsługiwane.

    **Nazwa** | **Chmura publiczna** | **Chmura dla instytucji rządowych** | **Szczegóły**
    --- | --- | --- | ---
    Storage | `*.blob.core.windows.net` | `*.blob.core.usgovcloudapi.net`| Zapisz dane z maszyny wirtualnej na koncie magazynu pamięci podręcznej w regionie źródłowym. 
    Azure AD  | `login.microsoftonline.com` | `login.microsoftonline.us`| Autoryzuj i uwierzytelniaj w celu Site Recovery adresów URL usługi. 
    Replikacja | `*.hypervrecoverymanager.windowsazure.com` | `*.hypervrecoverymanager.windowsazure.com`  |Komunikacja maszyny wirtualnej z usługą Site Recovery. 
    Service Bus | `*.servicebus.windows.net` | `*.servicebus.usgovcloudapi.net` | Zapisywanie maszyny wirtualnej w celu Site Recovery monitorowania i danych diagnostycznych. 

4. Jeśli używasz sieciowych grup zabezpieczeń (sieciowych grup zabezpieczeń) do ograniczania ruchu sieciowego dla maszyn wirtualnych, utwórz reguły sieciowej grupy zabezpieczeń zezwalające na połączenia wychodzące (HTTPS 443) dla maszyny wirtualnej korzystającej z tych tagów usługi (grup adresów IP). Wypróbuj najpierw reguły dla testu sieciowej grupy zabezpieczeń.

    **Tag** | **Zezwalaj** 
    --- | --- 
    Tag magazynu | Umożliwia zapisanie danych z maszyny wirtualnej na koncie magazynu pamięci podręcznej.
    Tag usługi Azure AD | Zezwala na dostęp do wszystkich adresów IP odnoszące się do usługi Azure AD.
    Tag EventsHub | Umożliwia dostęp do monitorowania Site Recovery.
    Tag AzureSiteRecovery | Zezwala na dostęp do usługi Site Recovery w dowolnym regionie.
    GuestAndHybridManagement | Użyj, jeśli chcesz automatycznie uaktualnić agenta mobilności Site Recovery, który jest uruchomiony na maszynach wirtualnych obsługujących replikację.
5.  Na maszynach wirtualnych z systemem Windows zainstaluj najnowsze aktualizacje systemu Windows, aby upewnić się, że maszyny wirtualne mają najnowsze certyfikaty główne.
 
## <a name="enable-disaster-recovery"></a>Włącz odzyskiwanie po awarii

1. W Azure Portal Otwórz stronę właściwości maszyny wirtualnej.
2. W obszarze **Operacja** wybierz pozycję **Odzyskiwanie po awarii**.
3. W   >  **obszarze region docelowy** podstawy wybierz region, do którego chcesz replikować maszynę wirtualną. Regiony źródłowe i docelowe muszą należeć do tej samej Azure Active Directory dzierżawy.
4. Kliknij kolejno pozycje **Recenzja + Uruchom replikację**.

    :::image type="content" source="./media/tutorial-disaster-recovery/disaster-recovery.png" alt-text="Włącz replikację na stronie odzyskiwania po awarii we właściwościach maszyny wirtualnej.":::

5. W obszarze **Przegląd + uruchamianie replikacji** Sprawdź ustawienia:

    - **Ustawienia docelowe**. Domyślnie Site Recovery odzwierciedla ustawienia źródła w celu tworzenia zasobów docelowych.
    - **Ustawienia magazynu — konto magazynu pamięci podręcznej**. Odzyskiwanie używa konta magazynu w regionie źródłowym. Zmiany źródłowej maszyny wirtualnej są buforowane na tym koncie przed replikacją do lokalizacji docelowej.
    - **Ustawienia magazynu — dysk repliki**. Domyślnie program Site Recovery tworzy dyski zarządzane repliki w regionie docelowym, które tworzą duplikaty dysków zarządzanych przez maszynę wirtualną z tym samym typem magazynu (w warstwie Standardowa lub Premium).
    - **Ustawienia replikacji**. Pokazuje szczegóły magazynu i wskazuje, że punkty odzyskiwania utworzone przez Site Recovery są przechowywane przez 24 godziny.
    - **Ustawienia rozszerzenia**. Wskazuje, że Site Recovery zarządza aktualizacjami rozszerzenia usługi mobilności Site Recovery zainstalowanego na replikowanych maszynach wirtualnych. Wskazane konto usługi Azure Automation zarządza procesem aktualizacji.

    :::image type="content" source="./media/tutorial-disaster-recovery/settings-summary.png" alt-text="Zostanie wyświetlona strona podsumowanie ustawień lokalizacji docelowej i replikacji.":::

2. Wybierz pozycję **Rozpocznij replikację**. Rozpocznie się wdrażanie, a Site Recovery zaczyna tworzyć zasoby docelowe. W powiadomieniach można monitorować postęp replikacji.

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

   Ta opcja zapewnia najniższy cel punktu odzyskiwania (RPO) i ogólnie najszybszą maszynę wirtualną. Najpierw przetwarza wszystkie dane, które zostały wysłane do usługi Site Recovery, aby utworzyć punkt odzyskiwania dla każdej maszyny wirtualnej przed przejściem w tryb failover. Ten punkt odzyskiwania ma wszystkie dane zreplikowane do Site Recovery podczas wyzwolenia trybu failover.

3. Wybierz sieć wirtualną, w której zostanie umieszczona maszyna wirtualna po zakończeniu pracy w trybie failover. 

     :::image type="content" source="./media/tutorial-disaster-recovery/test-failover-settings.png" alt-text="Strona umożliwiająca ustawienie opcji testowej pracy w trybie failover.":::

4. Rozpocznie się proces testowego przełączania do trybu failover. Postęp można monitorować w powiadomieniach.

    :::image type="content" source="./media/tutorial-disaster-recovery/test-failover-notification.png" alt-text="Testowe powiadomienia w trybie failover."::: 
    
   Po zakończeniu testowej pracy w trybie failover maszyna wirtualna jest w stanie oczekiwania na przełączenie do *trybu failover testu oczyszczania* na stronie **podstawy** . 



## <a name="clean-up-resources"></a>Czyszczenie zasobów

Maszyna wirtualna jest automatycznie czyszczona przez Site Recovery po przeprowadzeniu przechodzenia do szczegółów.

1. Aby rozpocząć automatyczne czyszczenie, wybierz pozycję **Oczyść test pracy w trybie failover**.

    :::image type="content" source="./media/tutorial-disaster-recovery/start-cleanup.png" alt-text="Rozpocznij czyszczenie na stronie podstawowe informacje."::: 

2. W obszarze **czyszczenie testu pracy w trybie failover** wpisz wszelkie uwagi, które chcesz zarejestrować w trybie failover, a następnie wybierz opcję **testowanie zostało zakończone. Usuń testową maszynę wirtualną w trybie failover**. Następnie wybierz przycisk **OK**.

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

   :::image type="content" source="./media/tutorial-disaster-recovery/uninstall-extension.png" alt-text="Strona do odinstalowania rozszerzenia maszyny wirtualnej Site Recovery.":::



## <a name="next-steps"></a>Następne kroki

W tym samouczku skonfigurowano odzyskiwanie po awarii dla maszyny wirtualnej platformy Azure i uruchomiono przechodzenie do szczegółów odzyskiwania po awarii. Teraz można wykonać pełną pracę w trybie failover dla maszyny wirtualnej.

> [!div class="nextstepaction"]
> [Przełączanie maszyny wirtualnej do pracy w trybie failover w innym regionie](../../site-recovery/azure-to-azure-tutorial-dr-drill.md)
