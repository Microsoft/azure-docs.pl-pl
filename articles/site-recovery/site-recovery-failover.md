---
title: Uruchamianie trybu failover podczas odzyskiwania po awarii za pomocą Azure Site Recovery
description: Jak przenieść maszyny wirtualne/serwery fizyczne do trybu failover na platformie Azure przy użyciu Azure Site Recovery.
ms.service: site-recovery
ms.topic: article
ms.date: 12/10/2019
ms.openlocfilehash: 481e7c692be24bbebd14584f8158740a5b7043ba
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91317892"
---
# <a name="run-a-failover-from-on-premises-to-azure"></a>Przechodzenie w tryb failover z lokacji lokalnej do platformy Azure

W tym artykule opisano sposób awaryjnego przełączania maszyn lokalnych na platformę Azure w programie [Azure Site Recovery](site-recovery-overview.md)

## <a name="before-you-start"></a>Przed rozpoczęciem

- [Dowiedz się więcej](failover-failback-overview.md) o procesie trybu failover w odzyskiwaniu po awarii.
- Jeśli chcesz przejść do trybu failover wielu maszyn, [Dowiedz się](recovery-plan-overview.md) , jak zbierać maszyny w planie odzyskiwania.
- Przed przeprowadzeniem pełnej pracy w trybie failover należy wykonać [drążenie odzyskiwania po awarii](site-recovery-test-failover-to-azure.md) , aby upewnić się, że wszystko działa zgodnie z oczekiwaniami.

## <a name="prepare-to-connect-after-failover"></a>Przygotuj się do połączenia po przejściu w tryb failover

Aby upewnić się, że możesz nawiązać połączenie z maszynami wirtualnymi platformy Azure utworzonymi po przejściu do trybu failover, poniżej przedstawiono kilka rzeczy, które należy wykonać przed przejściem do trybu failover.


### <a name="prepare-on-premises-to-connect-after-failover"></a>Przygotowywanie lokalnego połączenia po przejściu w tryb failover

Jeśli chcesz nawiązać połączenie z maszynami wirtualnymi platformy Azure przy użyciu protokołu RDP/SSH po przejściu w tryb failover, przed przejściem do trybu failover należy wykonać kilka czynności.

**Po przejściu w tryb failover** | **Lokalizacja** | **Akcje**
--- | --- | ---
**Maszyna wirtualna platformy Azure z systemem Windows** | Maszyna lokalna przed przejściem w tryb failover | Aby uzyskać dostęp do maszyny wirtualnej platformy Azure za pośrednictwem Internetu, Włącz protokół RDP i upewnij się, że reguły TCP i UDP są dodawane do **publicznej**wersji, a dla wszystkich profilów w aplikacjach **zapory systemu Windows**  >  **dozwolony**jest protokół RDP.<br/><br/> Aby uzyskać dostęp do maszyny wirtualnej platformy Azure za pośrednictwem połączenia lokacja-lokacja, Włącz protokół RDP na maszynie i upewnij się, że w **zaporze systemu Windows**  ->  **dozwolone aplikacje i funkcje**są dozwolone w przypadku **domen i sieci prywatnych** .<br/><br/> <br/><br/> Usuń wszystkie statyczne trasy trwałe i serwer proxy WinHTTP. Upewnij się, że zasady sieci SAN systemu operacyjnego są ustawione na **OnlineAll**. [Dowiedz się więcej](https://support.microsoft.com/kb/3031135).<br/><br/> Upewnij się, że nie ma żadnych oczekujących aktualizacji systemu Windows na maszynie wirtualnej podczas wyzwalania trybu failover. System Windows Update może zostać uruchomiony po przełączeniu w tryb failover i nie będzie można zalogować się na maszynie wirtualnej do momentu zakończenia aktualizacji.
**Maszyna wirtualna platformy Azure z systemem Linux** | Maszyna lokalna przed przejściem w tryb failover | Upewnij się, że usługa Secure Shell na maszynie wirtualnej jest uruchamiana automatycznie przy rozruchu systemu.<br/><br/> Sprawdź, czy reguły zapory zezwalają na połączenie SSH.


## <a name="run-a-failover"></a>Uruchamianie trybu failover

W tej procedurze opisano sposób uruchamiania trybu failover dla [planu odzyskiwania](site-recovery-create-recovery-plans.md). Jeśli chcesz uruchomić tryb failover dla pojedynczej maszyny wirtualnej, postępuj zgodnie z instrukcjami dla [maszyny wirtualnej VMware](vmware-azure-tutorial-failover-failback.md), [serwera fizycznego](physical-to-azure-failover-failback.md)lub [maszyny wirtualnej funkcji Hyper-V](hyper-v-azure-failover-failback-tutorial.md).


Uruchom tryb failover planu odzyskiwania w następujący sposób:

1. W magazynie Site Recovery wybierz pozycję **plany odzyskiwania**  >  *recoveryplan_name*.
2. Kliknij pozycję **tryb failover**.

    ![Zrzut ekranu z Azure Site Recovery wyświetlenia okienka ADRP z trybem failover wybranym z menu więcej.](./media/site-recovery-failover/Failover.png)

3. W polu kierunek pracy awaryjnej **trybu**failover  >  **Failover direction**pozostaw wartość domyślną, jeśli wykonujesz replikację do platformy Azure.
4. W obszarze **tryb failover**wybierz **punkt odzyskiwania** , do którego ma zostać przełączona praca awaryjna.

    - **Najnowsze**: Użyj najnowszego punktu. To przetwarza wszystkie dane, które zostały wysłane do usługi Site Recovery Service, i tworzy punkt odzyskiwania dla każdej maszyny. Ta opcja zapewnia najniższy cel punktu odzyskiwania, ponieważ maszyna wirtualna utworzona po przejściu w tryb failover ma wszystkie dane, które zostały zreplikowane do Site Recovery podczas wyzwolenia trybu failover.
    Należy pamiętać, że gdy region źródłowy ulegnie awarii, nie ma więcej możliwości przetwarzania dzienników. W związku z tym należy przełączyć się w tryb failover do ostatniego przetworzonego punktu odzyskiwania. Zobacz następny punkt, aby dowiedzieć się więcej.
   - **Najnowsza przetworzony**: Ta opcja umożliwia przełączenie maszyn wirtualnych w tryb failover do najnowszego punktu odzyskiwania, który został już przetworzony przez Site Recovery. Najnowszy przetworzony punkt odzyskiwania można zobaczyć w **najnowszych punktach odzyskiwania**maszyny wirtualnej. Ta opcja zapewnia niską RTO, ponieważ nie ma czasu poświęcanego na przetwarzanie nieprzetworzonych danych
   - **Najnowsza spójna dla aplikacji**: Użyj tej opcji, aby przełączyć maszyny wirtualne w tryb failover do najnowszego punktu odzyskiwania spójnego z aplikacją, który został przetworzony przez Site Recovery.
   - **Najnowsza przetworzona wiele maszyn wirtualnych**: w przypadku tej opcji maszyny wirtualne będące częścią grupy replikacji przełączenia w tryb failover do najnowszego wspólnego punktu odzyskiwania z WIELOWĄTKOWą maszyną wirtualną. Inne maszyny wirtualne są przełączane w tryb failover do ostatniego przetworzonego punktu odzyskiwania. Ta opcja jest dostępna tylko w przypadku planów odzyskiwania, które mają co najmniej jedną maszynę wirtualną z włączoną spójnością obejmującą wiele maszyn wirtualnych.
   - **Najnowsza spójna z aplikacją obejmującą wiele maszyn wirtualnych**: w przypadku tej opcji maszyny wirtualne, które są częścią grupy replikacji, są w trybie failover do najnowszego wspólnego punktu odzyskiwania spójnego z aplikacją obejmującą wiele maszyn wirtualnych. Inne maszyny wirtualne przełączenia w tryb failover do najnowszego punktu odzyskiwania spójnego na poziomie aplikacji. Tylko w przypadku planów odzyskiwania, które mają co najmniej jedną maszynę wirtualną z włączoną spójnością obejmującą wiele maszyn wirtualnych.
   - **Niestandardowe**: niedostępne w przypadku planów odzyskiwania. Ta opcja służy tylko do przełączania pojedynczych maszyn wirtualnych w tryb failover.

5. Wybierz opcję **Zamknij maszynę przed rozpoczęciem pracy w trybie failover** , jeśli chcesz Site Recovery wyłączyć źródłowe maszyny wirtualne przed rozpoczęciem pracy w trybie failover. Przełączanie do trybu failover będzie kontynuowane, nawet jeśli zamknięcie nie powiedzie się.  

    > [!NOTE]
    > W przypadku przełączenia awaryjnego maszyn wirtualnych funkcji Hyper-V zamknięcie programu próbuje zsynchronizować i zreplikować dane lokalne, które nie zostały jeszcze wysłane do usługi, przed wyzwoleniem trybu failover. 

6. Postępuj zgodnie z postępem pracy w trybie failover na stronie **zadań** . Nawet w przypadku wystąpienia błędów plan odzyskiwania zostanie uruchomiony do momentu jego zakończenia.
7. Po przejściu do trybu failover Zaloguj się do maszyny wirtualnej, aby sprawdzić jej poprawność. 
8. Jeśli chcesz przełączyć się do innego punktu odzyskiwania do użycia w trybie failover, użyj opcji **Zmień punkt odzyskiwania**.
9. Gdy wszystko będzie gotowe, możesz zatwierdzić tryb failover. Akcja **zatwierdzania** usuwa wszystkie punkty odzyskiwania dostępne w usłudze. Opcja **Zmień punkt odzyskiwania** nie będzie już dostępna.

## <a name="run-a-planned-failover-hyper-v"></a>Uruchom planowaną pracę w trybie failover (Hyper-V)

Można uruchomić planowane przejście w tryb failover dla maszyn wirtualnych funkcji Hyper-V.

- Planowana praca w trybie failover to zero opcja trybu failover utraty danych.
- W przypadku wyzwolenia planowanej pracy w trybie failover najpierw zostaną wyłączone źródłowe maszyny wirtualne, a następnie zostanie wyzwolone przejście w tryb failover.
- Planowaną pracę w trybie failover można uruchomić przy użyciu opcji **Planowana praca w trybie failover** . Działa w podobny sposób do zwykłego przejścia w tryb failover.
 
## <a name="track-failovers"></a>Śledzenie pracy w trybie failover

Istnieje wiele zadań skojarzonych z trybem failover.

![Zrzut ekranu przedstawiający stronę zadań z listą zadań z grupą 1: Start (1) rozwiniętą w kolumnie Nazwa. Wiersz zadania programu SQLServer zostanie wyróżniony.](./media/site-recovery-failover/FailoverJob.png)

- **Sprawdzanie wymagań wstępnych**: zapewnia spełnienie wszystkich warunków wymaganych do przejścia w tryb failover.
- **Tryb failover**: przetwarza dane, aby można było z niej utworzyć maszynę wirtualną platformy Azure. W przypadku wybrania **najnowszego** punktu odzyskiwania punkt odzyskiwania jest tworzony na podstawie danych, które zostały wysłane do usługi.
- **Rozpoczęcie**: tworzy maszynę wirtualną platformy Azure przy użyciu danych przetworzonych w poprzednim kroku.

> [!WARNING]
> **Nie Anuluj trybu failover w toku**: przed rozpoczęciem pracy w trybie failover replikacja s została zatrzymana dla maszyny wirtualnej. Jeśli anulujesz zadanie w toku, tryb failover zostanie zatrzymany, ale nie rozpocznie się replikacja maszyny wirtualnej. Nie można ponownie uruchomić replikacji.


### <a name="extra-failover-time"></a>Dodatkowy czas pracy awaryjnej

W niektórych przypadkach tryb failover maszyny wirtualnej wymaga kroku pośredniego, który zwykle trwa około ośmiu do 10 minut. Są to maszyny, na które ma wpływ ten dodatkowy krok/czas:

* Maszyny wirtualne VMware z uruchomioną wersją usługi mobilności starszą niż 9,8.
* Serwery fizyczne i maszyny wirtualne funkcji Hyper-V chronione jako serwery fizyczne.
* Maszyny wirtualne VMware Linux.
* Maszyny wirtualne VMware, na których te sterowniki nie są obecne jako sterowniki rozruchowe:
    * storvsc
    * VMBus
    * storflt
    * Intelide
    * Napęd
* Maszyny wirtualne VMware, które nie mają włączonej usługi DHCP, niezależnie od tego, czy korzystają z protokołu DHCP czy statycznych adresów IP.


## <a name="automate-actions-during-failover"></a>Automatyzowanie akcji podczas pracy w trybie failover

Można zautomatyzować akcje podczas pracy w trybie failover. W tym celu można użyć skryptów lub elementów Runbook usługi Azure Automation w planach odzyskiwania.

- [Dowiedz się więcej](site-recovery-create-recovery-plans.md) na temat tworzenia i dostosowywania planów odzyskiwania, w tym dodawania skryptów.
- [Dowiedz się więcej](site-recovery-runbook-automation.md) o dodawaniu Azure Automation elementów Runbook do planów odzyskiwania.


## <a name="configure-settings-after-failover"></a>Skonfiguruj ustawienia po przejściu w tryb failover

### <a name="retain-drive-letters-after-failover"></a>Zachowaj litery dysku po przejściu w tryb failover

Site Recovery obsługuje przechowywanie liter dysku. Jeśli wykluczasz dysk podczas replikacji maszyny wirtualnej, [zapoznaj się z przykładem](exclude-disks-replication.md#example-1-exclude-the-sql-server-tempdb-disk) tego, jak to działa.

### <a name="prepare-in-azure-to-connect-after-failover"></a>Przygotuj się na platformie Azure, aby nawiązać połączenie

Jeśli chcesz nawiązać połączenie z maszynami wirtualnymi platformy Azure utworzonymi po przejściu do trybu failover przy użyciu protokołu RDP lub SSH, postępuj zgodnie z wymaganiami podsumowanymi w tabeli.

**Tryb failover** | **Lokalizacja** | **Akcje**
--- | --- | ---
**Maszyna wirtualna platformy Azure z systemem Windows** | Maszyna wirtualna platformy Azure po przejściu w tryb failover |  [Dodaj publiczny adres IP](https://aka.ms/addpublicip) dla maszyny wirtualnej.<br/><br/> Reguły sieciowej grupy zabezpieczeń na maszynie wirtualnej w trybie failover (i podsieci platformy Azure, z którą jest połączona) muszą zezwalać na połączenia przychodzące do portu RDP.<br/><br/> Sprawdź **diagnostykę rozruchu** , aby zweryfikować zrzut ekranu maszyny wirtualnej.<br/><br/> Jeśli nie możesz się połączyć, sprawdź, czy maszyna wirtualna jest uruchomiona, i przejrzyj te [wskazówki dotyczące rozwiązywania problemów](https://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).
**Maszyna wirtualna platformy Azure z systemem Linux** | Maszyna wirtualna platformy Azure po przejściu w tryb failover | Reguły sieciowej grupy zabezpieczeń na maszynie wirtualnej w trybie failover (i podsieci platformy Azure, z którą jest połączona) muszą zezwalać na połączenia przychodzące do portu SSH.<br/><br/> [Dodaj publiczny adres IP](https://aka.ms/addpublicip) dla maszyny wirtualnej.<br/><br/> Sprawdź **diagnostykę rozruchu** dla zrzutu ekranu maszyny wirtualnej.<br/><br/>

Wykonaj czynności opisane [tutaj](site-recovery-failover-to-azure-troubleshoot.md), aby rozwiązać wszystkie problemy z łącznością po przejściu do trybu failover.

## <a name="set-up-ip-addressing"></a>Konfigurowanie adresowania IP

- **Wewnętrzne adresy IP**: Aby ustawić wewnętrzny adres IP maszyny wirtualnej platformy Azure po przejściu w tryb failover, masz kilka opcji:
    - Zachowaj ten sam adres IP: możesz użyć tego samego adresu IP na maszynie wirtualnej platformy Azure, który jest przypisany do maszyny lokalnej.
    - Użyj innego adresu IP: możesz użyć innego adresu IP dla maszyny wirtualnej platformy Azure.
    - [Dowiedz się więcej](concepts-on-premises-to-azure-networking.md#assign-an-internal-address) o konfigurowaniu wewnętrznych adresów IP.
- **Zewnętrzne adresy IP**: można zachować publiczne adresy IP w trybie failover. Na maszynach wirtualnych platformy Azure utworzonych w ramach procesu przełączania do trybu failover należy przypisać publiczny adres IP platformy Azure dostępny w regionie świadczenia usługi Azure. Publiczny adres IP można przypisać ręcznie lub przez automatyzację procesu przy użyciu planu odzyskiwania. [Dowiedz się więcej](concepts-public-ip-address-with-site-recovery.md).


## <a name="next-steps"></a>Następne kroki

Po przełączeniu w tryb failover należy ponownie włączyć ochronę, aby rozpocząć replikację maszyn wirtualnych platformy Azure z powrotem do lokacji lokalnej. Gdy replikacja jest uruchomiona, możesz wrócić do trybu failover, gdy wszystko będzie gotowe.

- [Dowiedz się więcej](failover-failback-overview.md#reprotectionfailback) o ochronie i powrotu po awarii.
- [Przygotuj](vmware-azure-reprotect.md) się do reochrony oprogramowania VMware i powrotu po awarii.
- [Powrót po awarii](hyper-v-azure-failback.md) Maszyny wirtualne funkcji Hyper-V.
- [Dowiedz się więcej o](physical-to-azure-failover-failback.md) procesie przełączania do trybu failover i powrotu po awarii dla serwerów fizycznych.

