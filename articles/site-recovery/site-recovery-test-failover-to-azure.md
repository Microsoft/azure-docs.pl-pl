---
title: Uruchamianie testu pracy w trybie failover (do testowania odzyskiwania po awarii) na platformie Azure w Azure Site Recovery
description: Dowiedz się więcej o uruchamianiu testowej pracy w trybie failover z poziomu lokalnego na platformę Azure przy użyciu usługi Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: raynew
ms.openlocfilehash: 815a35c167bbcd3ac03dfdaaf6d699e58a791f33
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/22/2020
ms.locfileid: "92369390"
---
# <a name="run-a-test-failover-disaster-recovery-drill-to-azure"></a>Uruchamianie testu pracy w trybie failover (do testowania odzyskiwania po awarii) na platformie Azure 


W tym artykule opisano sposób przeprowadzania przechodzenia do trybu failover odzyskiwania po awarii na platformie Azure przy użyciu Site Recovery testowego przełączenia.  

Przeprowadzasz test pracy w trybie failover, aby zweryfikować strategię replikacji i odzyskiwania po awarii bez utraty danych ani przestojów. Test pracy w trybie failover nie ma wpływu na bieżącą replikację lub środowisko produkcyjne. Test pracy w trybie failover można uruchomić na określonej maszynie wirtualnej lub w [planie odzyskiwania](site-recovery-create-recovery-plans.md) zawierającym wiele maszyn wirtualnych.


## <a name="run-a-test-failover"></a>Wykonywanie próby przejścia w tryb failover
W tej procedurze opisano, jak uruchomić test pracy w trybie failover dla planu odzyskiwania. Jeśli chcesz uruchomić test pracy w trybie failover dla pojedynczej maszyny wirtualnej, wykonaj kroki opisane [tutaj](tutorial-dr-drill-azure.md#run-a-test-failover-for-a-single-vm) .

![Zrzut ekranu strony testowej pracy w trybie failover w Azure Portal.](./media/site-recovery-test-failover-to-azure/TestFailover.png)


1. W Site Recovery w Azure Portal kliknij kolejno pozycje **plany odzyskiwania**  >  *recoveryplan_name*  >  **test pracy w trybie failover**.
2. Wybierz **punkt odzyskiwania** , do którego ma zostać przełączona praca awaryjna. Możesz użyć jednej z następujących opcji:
    - **Najnowsza przetworzony**: Ta opcja powoduje przełączenie w tryb failover wszystkich maszyn wirtualnych w planie do najnowszego punktu odzyskiwania przetworzonego przez Site Recovery. Aby wyświetlić najnowszy punkt odzyskiwania dla określonej maszyny wirtualnej, sprawdź **najnowsze punkty odzyskiwania** w ustawieniach maszyny wirtualnej. Ta opcja zapewnia niską wartość celu czasu odzyskiwania, ponieważ nie wymaga przetwarzania nieprzetworzonych danych.
    - **Najnowsza spójna dla aplikacji**: Ta opcja powoduje przełączenie w tryb failover wszystkich maszyn wirtualnych w planie do ostatniego punktu odzyskiwania spójnego na poziomie aplikacji przetworzonego przez Site Recovery. Aby wyświetlić najnowszy punkt odzyskiwania dla określonej maszyny wirtualnej, sprawdź **najnowsze punkty odzyskiwania** w ustawieniach maszyny wirtualnej.
    - **Najnowsze**: Ta opcja najpierw przetwarza wszystkie dane, które zostały wysłane do usługi Site Recovery, aby utworzyć punkt odzyskiwania dla każdej maszyny wirtualnej przed przejściem w tryb failover. Ta opcja zapewnia najniższy cel punktu odzyskiwania, ponieważ maszyna wirtualna utworzona po zakończeniu pracy w trybie failover będzie miała wszystkie dane zreplikowane do Site Recovery podczas wyzwolenia trybu failover.
    - **Ostatnio przetworzone wiele maszyn wirtualnych**: Ta opcja jest dostępna dla planów odzyskiwania z co najmniej jedną maszyną wirtualną, na której włączono spójność wielu maszyn wirtualnych. Maszyny wirtualne z włączonym ustawieniem przechodzą w tryb failover do najnowszego wspólnego punktu odzyskiwania z wielowątkową maszyną wirtualną. Inne maszyny wirtualne są przełączane w tryb failover do ostatniego przetworzonego punktu odzyskiwania.  
    - **Najnowsza aplikacja obejmująca wiele maszyn wirtualnych — spójna**: Ta opcja jest dostępna w przypadku planów odzyskiwania z co najmniej jedną maszyną wirtualną, na której włączono spójność wielu maszyn wirtualnych. Maszyny wirtualne, które są częścią grupy replikacji, są przełączane w tryb failover do najnowszego wspólnego punktu odzyskiwania spójnego z aplikacjami wielomaszynowo. Inne maszyny wirtualne są przełączane w tryb failover do najnowszego punktu odzyskiwania spójnego z aplikacją.
    - **Niestandardowe**: Użyj tej opcji, aby przełączyć KONKRETNą maszynę wirtualną do określonego punktu odzyskiwania.
3. Wybierz sieć wirtualną platformy Azure, w której zostaną utworzone testowe maszyny wirtualne.

    - Site Recovery próbuje utworzyć testowe maszyny wirtualne w podsieci o tej samej nazwie i tym samym adresie IP, co podane w ustawieniach **obliczeniowych i sieciowych** maszyny wirtualnej.
    - Jeśli podsieć o tej samej nazwie nie jest dostępna w sieci wirtualnej platformy Azure używanej na potrzeby testowego przełączania do trybu failover, testowa maszyna wirtualna jest tworzona w pierwszej podsieci alfabetycznie.
    - Jeśli ten sam adres IP nie jest dostępny w podsieci, maszyna wirtualna odbiera kolejny dostępny adres IP w podsieci. [Dowiedz się więcej](#create-a-network-for-test-failover).
4. W przypadku przełączenia w tryb failover na platformie Azure, gdy szyfrowanie danych jest włączone, w **kluczu szyfrowania**wybierz certyfikat, który został wystawiony po włączeniu szyfrowania podczas instalacji dostawcy. Ten krok można zignorować, jeśli szyfrowanie nie jest włączone.
5. Śledź postęp pracy w trybie failover na karcie **zadania** . Należy mieć możliwość wyświetlenia testowej maszyny repliki w Azure Portal.
6. Aby zainicjować połączenie RDP z maszyną wirtualną platformy Azure, należy [dodać publiczny adres IP](/archive/blogs/srinathv/how-to-add-a-public-ip-address-to-azure-vm-for-vm-failed-over-using-asr) do interfejsu sieciowego maszyny wirtualnej przełączonej w tryb failover.
7. Gdy wszystko działa zgodnie z oczekiwaniami, kliknij przycisk **Oczyść test pracy w trybie failover**. Spowoduje to usunięcie maszyn wirtualnych, które zostały utworzone podczas testu pracy w trybie failover.
8. W obszarze **uwagi**Zarejestruj i Zapisz wszelkie obserwacje związane z testem pracy w trybie failover.


![Zrzut ekranu przedstawiający kartę zadania testowania pracy w trybie failover.](./media/site-recovery-test-failover-to-azure/TestFailoverJob.png)

Po wyzwoleniu testowej pracy w trybie failover następuje:

1. **Wymagania wstępne**: Sprawdzanie wymagań wstępnych jest przeprowadzane w celu upewnienia się, że spełnione są wszystkie warunki wymagane do przejścia w tryb failover.
2. **Tryb failover**: przełączenie w tryb failover i przygotowanie danych, dzięki czemu można z niej utworzyć maszynę wirtualną platformy Azure.
3. **Najnowsze**: w przypadku wybrania najnowszego punktu odzyskiwania punkt odzyskiwania jest tworzony na podstawie danych, które zostały wysłane do usługi.
4. **Rozpoczęcie**: ten krok polega na utworzeniu maszyny wirtualnej platformy Azure przy użyciu danych przetworzonych w poprzednim kroku.

### <a name="failover-timing"></a>Czas pracy w trybie failover

W następujących scenariuszach tryb failover wymaga dodatkowego etapu pośredniego, który przeważnie trwa około 8 do 10 minut:

* Maszyny wirtualne VMware z uruchomioną wersją usługi mobilności starszej niż 9,8
* Serwery fizyczne
* Maszyny wirtualne VMware Linux
* Maszyna wirtualna funkcji Hyper-V chroniona jako serwery fizyczne
* Maszyna wirtualna VMware, w której następujące sterowniki nie są sterownikiem rozruchowym:
    * storvsc
    * VMBus
    * storflt
    * Intelide
    * Napęd
* Maszyna wirtualna VMware, która nie ma włączonego protokołu DHCP, niezależnie od tego, czy korzystają z protokołu DHCP czy statycznych adresów IP.

We wszystkich innych przypadkach żaden krok pośredni nie jest wymagany, a tryb failover trwa znacznie mniej czasu.


## <a name="create-a-network-for-test-failover"></a>Tworzenie sieci do testowania pracy w trybie failover

Do testowania pracy w trybie failover zalecamy wybór sieci izolowanej od sieci produkcyjnej lokacji odzyskiwania, określonej w obszarze ustawień **Obliczenia i sieć** poszczególnych maszyn wirtualnych. Tworzone sieci wirtualne platformy Azure są domyślnie izolowane od innych sieci. Sieć testowa powinna odzwierciedlać sieć produkcyjną:

- Sieć testowa powinna mieć taką samą liczbę podsieci jak sieć produkcyjna. Podsieci powinny mieć takie same nazwy.
- W sieci testowej powinien być używany taki sam zakres adresów IP.
- Zaktualizuj system DNS sieci testowej, używając adresu IP określonego dla maszyny wirtualnej systemu DNS w obszarze ustawień **Obliczenia i sieć**. Aby uzyskać więcej informacji, zobacz sekcję [Zagadnienia dotyczące testowania trybu failover dla usługi Active Directory](site-recovery-active-directory.md#test-failover-considerations).


## <a name="test-failover-to-a-production-network-in-the-recovery-site"></a>Testowanie pracy w trybie failover w sieci produkcyjnej w lokacji odzyskiwania

Chociaż zalecamy korzystanie z sieci testowej niezależnej od sieci produkcyjnej, jeśli chcesz przetestować przechodzenie awaryjne w sieci produkcyjnej, należy pamiętać o następujących kwestiach:

- Upewnij się, że podstawowa maszyna wirtualna jest zamknięta po uruchomieniu testowej pracy w trybie failover. W przeciwnym razie będą dwie maszyny wirtualne o tej samej tożsamości uruchomione w tej samej sieci w tym samym czasie. Może to prowadzić do nieoczekiwanych konsekwencji.
- Wszystkie zmiany maszyn wirtualnych utworzonych na potrzeby testowej pracy w trybie failover zostaną utracone po oczyszczeniu do trybu failover. Te zmiany nie są replikowane z powrotem do podstawowej maszyny wirtualnej.
- Testowanie w środowisku produkcyjnym prowadzi do przestoju aplikacji produkcyjnej. Użytkownicy nie powinni korzystać z aplikacji uruchomionych na maszynach wirtualnych, gdy test pracy w trybie failover jest w toku.  



## <a name="prepare-active-directory-and-dns"></a>Przygotowanie Active Directory i systemu DNS

Aby uruchomić test pracy w trybie failover na potrzeby testowania aplikacji, w środowisku testowym potrzebna jest kopia środowiska produkcyjnego Active Directory. Przeczytaj [zagadnienia dotyczące testowania pracy w trybie failover Active Directory](site-recovery-active-directory.md#test-failover-considerations) , aby dowiedzieć się więcej.

## <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Przygotowanie do połączenia z maszynami wirtualnymi Azure po przejściu do trybu failover

Jeśli chcesz nawiązać połączenie z maszynami wirtualnymi platformy Azure przy użyciu protokołu RDP/SSH po przejściu w tryb failover, postępuj zgodnie z wymaganiami podanymi w tabeli.

**Tryb failover** | **Lokalizacja** | **Akcje**
--- | --- | ---
**Maszyna wirtualna platformy Azure z systemem Windows** | Maszyna lokalna przed przejściem w tryb failover | Aby uzyskać dostęp do maszyny wirtualnej platformy Azure za pośrednictwem Internetu, Włącz protokół RDP i upewnij się, że reguły TCP i UDP są dodawane do **publicznej**wersji, a dla wszystkich profilów w aplikacjach **zapory systemu Windows**  >  **dozwolony**jest protokół RDP.<br/><br/> Aby uzyskać dostęp do maszyny wirtualnej platformy Azure za pośrednictwem połączenia lokacja-lokacja, Włącz protokół RDP na maszynie i upewnij się, że w **zaporze systemu Windows**  ->  **dozwolone aplikacje i funkcje**są dozwolone w przypadku **domen i sieci prywatnych** .<br/><br/>  Upewnij się, że zasady sieci SAN systemu operacyjnego są ustawione na **OnlineAll**. [Dowiedz się więcej](https://support.microsoft.com/kb/3031135).<br/><br/> Upewnij się, że nie ma żadnych oczekujących aktualizacji systemu Windows na maszynie wirtualnej podczas wyzwalania trybu failover. System Windows Update może zostać uruchomiony po przełączeniu w tryb failover i nie będzie można zalogować się na maszynie wirtualnej do momentu zakończenia aktualizacji.
**Maszyna wirtualna platformy Azure z systemem Windows** | Maszyna wirtualna platformy Azure po przejściu w tryb failover |  [Dodaj publiczny adres IP](/archive/blogs/srinathv/how-to-add-a-public-ip-address-to-azure-vm-for-vm-failed-over-using-asr) dla maszyny wirtualnej.<br/><br/> Reguły sieciowej grupy zabezpieczeń na maszynie wirtualnej w trybie failover (i podsieci platformy Azure, z którą jest połączona) muszą zezwalać na połączenia przychodzące do portu RDP.<br/><br/> Sprawdź **diagnostykę rozruchu** , aby zweryfikować zrzut ekranu maszyny wirtualnej.<br/><br/> Jeśli nie możesz się połączyć, sprawdź, czy maszyna wirtualna jest uruchomiona, i przejrzyj te [wskazówki dotyczące rozwiązywania problemów](https://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).
**Maszyna wirtualna platformy Azure z systemem Linux** | Maszyna lokalna przed przejściem w tryb failover | Upewnij się, że usługa Secure Shell na maszynie wirtualnej jest uruchamiana automatycznie przy rozruchu systemu.<br/><br/> Sprawdź, czy reguły zapory zezwalają na połączenie SSH.
**Maszyna wirtualna platformy Azure z systemem Linux** | Maszyna wirtualna platformy Azure po przejściu w tryb failover | Reguły sieciowej grupy zabezpieczeń na maszynie wirtualnej w trybie failover (i podsieci platformy Azure, z którą jest połączona) muszą zezwalać na połączenia przychodzące do portu SSH.<br/><br/> [Dodaj publiczny adres IP](/archive/blogs/srinathv/how-to-add-a-public-ip-address-to-azure-vm-for-vm-failed-over-using-asr) dla maszyny wirtualnej.<br/><br/> Sprawdź **diagnostykę rozruchu** dla zrzutu ekranu maszyny wirtualnej.<br/><br/>

Wykonaj czynności opisane [tutaj](site-recovery-failover-to-azure-troubleshoot.md), aby rozwiązać wszystkie problemy z łącznością po przejściu do trybu failover.

## <a name="next-steps"></a>Następne kroki
Po zakończeniu drążenia odzyskiwania po awarii Dowiedz się więcej o innych typach [trybu failover](site-recovery-failover.md).