---
title: Monitoruj Azure Site Recovery | Microsoft Docs
description: Monitorowanie i rozwiązywanie problemów z Azure Site Recovery replikacji i operacji przy użyciu portalu
author: raynew
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: raynew
ms.openlocfilehash: d441284b265ab11dd5ece42ec3737e455d662435
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/26/2020
ms.locfileid: "92545807"
---
# <a name="monitor-site-recovery"></a>Monitorowanie usługi Site Recovery

W tym artykule dowiesz się, jak monitorować usługę Azure [Site Recovery](site-recovery-overview.md)przy użyciu Site Recovery nieskompilowanego monitorowania.  Można monitorować:

- Kondycja i stan maszyn replikowanych przez Site Recovery
- Test stanu trybu failover dla maszyn.
- Problemy i błędy wpływające na konfigurację i replikację.
- Składniki infrastruktury, takie jak serwery lokalne.


## <a name="before-you-start"></a>Przed rozpoczęciem

Przed rozpoczęciem warto zapoznać się z [typowymi pytaniami monitorowania](monitoring-common-questions.md) .

## <a name="monitor-in-the-dashboard"></a>Monitorowanie na pulpicie nawigacyjnym

1. W magazynie kliknij pozycję **Przegląd** . Pulpit nawigacyjny Recovery Services konsoliduje wszystkie informacje monitorowania dla magazynu w jednej lokalizacji. Istnieją strony dla obu Site Recovery i usługi Azure Backup, a także można przełączać się między nimi.

    ![Pulpit nawigacyjny Site Recovery](./media/site-recovery-monitor-and-troubleshoot/dashboard.png)

2. Na pulpicie nawigacyjnym przejdź do szczegółów różnych obszarów. 

    ![Zrzut ekranu pokazujący obszary na pulpicie nawigacyjnym, w którym można przechodzić do szczegółów.](./media/site-recovery-monitor-and-troubleshoot/site-recovery-overview-page.png).

3. W obszarze **zreplikowane elementy** kliknij pozycję **Wyświetl wszystko** , aby wyświetlić wszystkie serwery w magazynie.
4. Kliknij Szczegóły stanu w każdej sekcji, aby przejść do szczegółów.
5. W **widoku infrastruktura** Sortuj informacje o monitorowaniu według typu maszyn, które są replikowane.

## <a name="monitor-replicated-items"></a>Monitoruj zreplikowane elementy

W obszarze **zreplikowane elementy** Monitoruj kondycję wszystkich maszyn w magazynie, dla których włączono replikację.

**Państwu** | **Szczegóły**
--- | ---
Dobra kondycja | Replikacja postępuje normalnie. Nie wykryto błędów ani ostrzeżeń ostrzegawczych.
Ostrzeżenie | Wykryto co najmniej jeden objaw ostrzegawczy, który może mieć wpływ na replikację.
Krytyczne | Wykryto co najmniej jeden krytyczny błąd replikacji.<br/><br/> Te objawy błędów są zwykle wskaźnikami, które zablokowały replikację lub nie postępują tak szybko, jak szybkość zmiany danych.
Nie dotyczy | Serwery, które nie są aktualnie oczekiwane na replikację. Mogą to być maszyny, które zostały przełączone w tryb failover.

## <a name="monitor-test-failovers"></a>Monitoruj test pracy w trybie failover

W przypadku **powodzenia testów trybu failover** monitoruj stan trybu failover dla maszyn w magazynie.

- Zalecamy uruchomienie testowej pracy w trybie failover na replikowanych maszynach co najmniej raz na sześć miesięcy. Jest to sposób, aby sprawdzić, czy tryb failover działa zgodnie z oczekiwaniami, bez zakłócania pracy w środowisku produkcyjnym. 
- Test pracy w trybie failover jest uznawany za pomyślny dopiero po pomyślnym zakończeniu przejścia w tryb failover i po przejściu po awarii.

**Państwu** | **Szczegóły**
--- | ---
Zalecany test | Maszyny, które nie miały testowego przejścia w tryb failover, ponieważ włączono ochronę.
Wykonane pomyślnie | Maszyny z lub więcej pomyślnych testów trybu failover.
Nie dotyczy | Maszyny, które aktualnie nie kwalifikują się do testowej pracy w trybie failover. Na przykład maszyny, które są przełączone w tryb failover, mają w toku replikację początkową/test pracy awaryjnej/trybu failover.

## <a name="monitor-configuration-issues"></a>Monitorowanie problemów z konfiguracją

W obszarze **problemy z konfiguracją** należy monitorować wszelkie problemy, które mogą mieć wpływ na możliwość pomyślnego przełączenia w tryb failover.

- Problemy z konfiguracją (z wyjątkiem dostępności aktualizacji oprogramowania) są wykrywane przez okresową operację sprawdzania poprawności, która domyślnie jest uruchamiana co 12 godzin. Można wymusić natychmiastowe uruchomienie operacji modułu sprawdzania poprawności, klikając ikonę odświeżenia obok nagłówka sekcji **problemy z konfiguracją** .
- Kliknij linki, aby uzyskać więcej szczegółów. W przypadku problemów mających wpływ na określone maszyny kliknij pozycję **wymaga uwagi** w kolumnie **konfiguracje docelowe** . Szczegóły obejmują zalecenia dotyczące korygowania.

**Państwu** | **Szczegóły**
--- | ---
Brakujące konfiguracje | Brak wymaganego ustawienia, takiego jak sieć odzyskiwania lub Grupa zasobów.
Brakujące zasoby | Nie można znaleźć określonego zasobu lub nie jest on dostępny w subskrypcji. Na przykład zasób został usunięty lub poddany migracji. Monitorowane zasoby obejmują docelową grupę zasobów, docelową sieć wirtualną/podsieć, dziennik/docelowe konto magazynu, docelowy zestaw dostępności, docelowy adres IP.
Przydział subskrypcji |  Saldo dostępnego przydziału zasobów subskrypcji jest porównywane z saldem wymaganym do przełączenia wszystkich maszyn w magazynie w tryb failover.<br/><br/> Jeśli nie ma wystarczającej ilości zasobów, zostanie zgłoszony niewystarczający bilans przydziału.<br/><br/> Przydziały są monitorowaniem liczby rdzeni maszyn wirtualnych, liczba rdzeni rodziny maszyn wirtualnych, liczba kart sieciowych (NIC).
Aktualizacje oprogramowania | Dostępność nowych aktualizacji oprogramowania oraz informacje o wygasających wersjach oprogramowania.


## <a name="monitor-errors"></a>Monitoruj błędy

W obszarze **Podsumowanie błędów** Monitoruj obecnie aktywne objawy błędów, które mogą mieć wpływ na replikację serwerów w magazynie, oraz monitoruj liczbę maszyn, których dotyczy problem.

- Błędy mające wpływ na składniki infrastruktury lokalnej są wyświetlane na początku sekcji. Na przykład Nieodebranie pulsu od dostawcy Azure Site Recovery na lokalnym serwerze konfiguracji lub hoście funkcji Hyper-V.
- Następnie są wyświetlane objawy błędu replikacji wpływające na serwery replikowane.
- Pozycje tabeli są sortowane według malejącej kolejności ważności błędów, a następnie przez zmniejszenie kolejności liczby maszyn, na które wpływa.
- Liczba serwerów, których to dotyczy, to przydatny sposób, aby zrozumieć, czy jeden z podstawowych problemów może mieć wpływ na wiele maszyn. Na przykład błąd sieci może mieć wpływ na wszystkie maszyny, które są replikowane do platformy Azure. 
- Na jednym serwerze może wystąpić wiele błędów replikacji. W takim przypadku każdy objaw błędu liczy ten serwer na liście serwerów, których to dotyczy. Po rozwiązaniu problemu należy poprawić parametry replikacji, a na komputerze zostanie wyczyszczony błąd.

## <a name="monitor-the-infrastructure"></a>Monitoruj infrastrukturę.

W **widoku infrastruktura** Monitoruj składniki infrastruktury objęte replikacją i kondycję łączności między serwerami i usługami platformy Azure.

- Zielona linia wskazuje, że połączenie jest w dobrej kondycji.
- Czerwona linia ze zastąpioną ikoną błędu wskazuje istnienie jednego lub kilku objawów błędów, które mają wpływ na łączność.
-  Umieść wskaźnik myszy nad ikoną błędu, aby wyświetlić błąd i liczbę jednostek, których dotyczy problem. Kliknij ikonę filtrowanej listy jednostek, których dotyczy problem.

    ![Widok infrastruktury Site Recovery (magazyn)](./media/site-recovery-monitor-and-troubleshoot/site-recovery-vault-infra-view.png)

### <a name="tips-for-monitoring-the-infrastructure"></a>Wskazówki dotyczące monitorowania infrastruktury

- Upewnij się, że lokalne składniki infrastruktury (serwer konfiguracji, serwery przetwarzania, serwery VMM, hosty funkcji Hyper-V, maszyny VMware) używają najnowszych wersji dostawcy Site Recovery i/lub agentów.
- Aby korzystać ze wszystkich funkcji w widoku infrastruktury, należy uruchomić [pakiet zbiorczy aktualizacji 22](https://support.microsoft.com/help/4072852) dla tych składników.
- Aby skorzystać z widoku infrastruktura, wybierz odpowiedni scenariusz replikacji w danym środowisku. Aby uzyskać więcej informacji, możesz przejść do szczegółów w widoku. W poniższej tabeli przedstawiono, które scenariusze są reprezentowane.

    **Scenariusz** | **Państwu**  | **Wyświetlić dostępne?**
    --- |--- | ---
    **Replikacja między lokacjami lokalnymi** | Wszystkie stany | Nie 
    **Replikacja maszyny wirtualnej platformy Azure między regionami platformy Azure**  | Replikacja włączona/replikacja początkowa w toku | Tak
    **Replikacja maszyny wirtualnej platformy Azure między regionami platformy Azure** | Przełączenie w tryb failover/powrót po awarii | Nie   
    **Replikacja z programu VMware do platformy Azure** | Replikacja włączona/replikacja początkowa w toku | Tak     
    **Replikacja z programu VMware do platformy Azure** | Przełączenie w tryb failover/powrót po awarii | Nie      
    **Replikacja funkcji Hyper-V do platformy Azure** | Przełączenie w tryb failover/powrót po awarii | Nie

- Aby wyświetlić widok infrastruktura dla pojedynczej maszyny replikowanej, w menu magazyn kliknij pozycję **zreplikowane elementy** i wybierz serwer.  




## <a name="monitor-recovery-plans"></a>Monitorowanie planów odzyskiwania

W obszarze **plany odzyskiwania** Monitoruj liczbę planów, twórz nowe plany i Modyfikuj istniejące.  

## <a name="monitor-jobs"></a>Monitorowanie zadań

W obszarze **zadania** monitoruj stan Site Recovery operacji.

- Większość operacji w Azure Site Recovery są wykonywane asynchronicznie, a zadanie śledzenia jest tworzone i używane do śledzenia postępu operacji. 
- Obiekt zadania zawiera wszystkie informacje potrzebne do śledzenia stanu i postęp operacji. 

Monitoruj zadania w następujący sposób:

1. W sekcji **zadania** > pulpitu nawigacyjnego można zobaczyć podsumowanie zadań zakończonych, w toku lub oczekiwanie na dane wejściowe w ciągu ostatnich 24 godzin. Możesz kliknąć dowolny stan, aby uzyskać więcej informacji na temat odpowiednich zadań.
2. Kliknij pozycję **Wyświetl wszystko** , aby wyświetlić wszystkie zadania w ciągu ostatnich 24 godzin.

    > [!NOTE]
    > Możesz również uzyskać dostęp do informacji o zadaniu z menu magazynowego > **zadania Site Recovery** . 

2. Na liście **zadania Site Recovery** zostanie wyświetlona lista zadań. W górnym menu możesz uzyskać szczegółowe informacje o błędach dla konkretnych zadań, filtrować listę zadań na podstawie określonych kryteriów i wyeksportować wybrane szczegóły zadania do programu Excel.
3. Aby przejść do szczegółów zadania, kliknij je. 

## <a name="monitor-virtual-machines"></a>Monitorowanie maszyn wirtualnych

W obszarze **zreplikowane elementy** Pobierz listę replikowanych maszyn. 
    ![Widok listy zreplikowanych elementów Site Recovery](./media/site-recovery-monitor-and-troubleshoot/site-recovery-virtual-machine-list-view.png)

2. Można wyświetlać i filtrować informacje. W menu Akcja u góry można wykonać akcje dla konkretnej maszyny, w tym uruchamianie testowej pracy w trybie failover lub wyświetlanie określonych błędów.
3. Kliknij pozycję **kolumny** , aby wyświetlić dodatkowe kolumny, na przykład aby pokazać cel punktu odzyskiwania, problemy z konfiguracją docelową i błędy replikacji.
4. Kliknij przycisk **Filtruj** , aby wyświetlić informacje na podstawie określonych parametrów, takich jak kondycja replikacji lub określone zasady replikacji.
5. Kliknij prawym przyciskiem myszy maszynę, aby zainicjować operacje, takie jak testowanie pracy w trybie failover lub wyświetlanie określonych szczegółów błędu skojarzonych z nim.
6. Kliknij maszynę, aby przejść do szczegółów. Szczegóły obejmują:
   - **Informacje o replikacji** : bieżący stan i kondycja maszyny.
   - **RPO** (cel punktu odzyskiwania): bieżący punkt odzyskiwania maszyny wirtualnej oraz godzina ostatniego obliczenia punktu odzyskiwania.
   - **Punkty odzyskiwania** : Najnowsze dostępne punkty odzyskiwania dla maszyny.
   - **Gotowość do pracy w trybie failover** : wskazuje, czy dla maszyny uruchomiono test pracy w trybie failover, czy wersja agenta uruchomiona na komputerze (dla maszyn z uruchomioną usługą mobilności) oraz problemy z konfiguracją.
   - **Błędy** : Lista objawów błędów replikacji aktualnie zaobserwowanych na komputerze i możliwych przyczyn/akcji.
   - **Zdarzenia** : chronologiczna lista ostatnich zdarzeń, które mają wpływ na maszynę. Szczegóły błędu przedstawia widoczne objawy błędów, natomiast zdarzenia są historycznym rekordem problemów, które mają wpływ na maszynę.
   - **Widok infrastruktury** : pokazuje stan infrastruktury dla scenariusza, gdy maszyny są replikowane na platformę Azure.

     ![Site Recovery szczegóły zreplikowanego elementu/przegląd](./media/site-recovery-monitor-and-troubleshoot/site-recovery-virtual-machine-details.png)

## <a name="subscribe-to-email-notifications"></a>Subskrybowanie powiadomień e-mail

Możesz subskrybować otrzymywanie powiadomień e-mail dotyczących tych krytycznych zdarzeń:
 
- Stan krytyczny dla zreplikowanej maszyny.
- Brak łączności między składnikami infrastruktury lokalnej a usługą Site Recovery. Wykrywanie połączeń między Site Recovery i lokalnymi serwerami zarejestrowanymi w magazynie jest wykrywane przy użyciu mechanizmu pulsu.
- Awarie trybu failover.

Subskrybuj w następujący sposób:

W sekcji **monitorowanie** > magazynu kliknij pozycję **zdarzenia Site Recovery** .
1. Kliknij pozycję **Powiadomienia e-mail** .
1. W obszarze **powiadomienia e-mail** Włącz opcję powiadomienia i określ, do kogo wysyłać. Możesz wysyłać do wszystkich administratorów subskrypcji, aby otrzymywać powiadomienia i opcjonalnie określone adresy e-mail.

    ![Powiadomienia e-mail](./media/site-recovery-monitor-and-troubleshoot/email.png)

## <a name="next-steps"></a>Następne kroki

[Dowiedz się więcej o](monitor-log-analytics.md) monitorowaniu Site Recovery z Azure monitor.
