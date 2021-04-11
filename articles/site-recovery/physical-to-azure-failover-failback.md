---
title: Konfigurowanie trybu failover i powrotu po awarii dla serwerów fizycznych za pomocą Site Recovery
description: Dowiedz się, jak przełączyć serwery fizyczne do trybu failover na platformie Azure i powrócić po awarii do lokacji lokalnej w celu odzyskiwania po awarii za pomocą Azure Site Recovery
services: site-recovery
ms.service: site-recovery
ms.topic: article
ms.date: 12/17/2019
ms.openlocfilehash: 68427d264b6d2eea1a38e3b8bc5273409221c126
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/07/2021
ms.locfileid: "106579908"
---
# <a name="fail-over-and-fail-back-physical-servers-replicated-to-azure"></a>Przełączenie w tryb failover i powrót po awarii serwerów fizycznych replikowanych do platformy Azure

W tym samouczku opisano sposób przełączenia w tryb failover lokalnych serwerów fizycznych, które są replikowane na platformę Azure za pomocą [Azure Site Recovery](site-recovery-overview.md). Po przełączeniu w tryb failover powrót po awarii z platformy Azure do lokacji lokalnej jest możliwy po jej udostępnieniu.

## <a name="before-you-start"></a>Przed rozpoczęciem

- [Dowiedz się więcej](failover-failback-overview.md) o procesie trybu failover w odzyskiwaniu po awarii.
- Jeśli chcesz przejść do trybu failover wielu maszyn, [Dowiedz się](recovery-plan-overview.md) , jak zbierać maszyny w planie odzyskiwania.
- Przed przeprowadzeniem pełnej pracy w trybie failover należy wykonać [drążenie odzyskiwania po awarii](site-recovery-test-failover-to-azure.md) , aby upewnić się, że wszystko działa zgodnie z oczekiwaniami.
- Wykonaj [te instrukcje](site-recovery-failover.md#prepare-to-connect-after-failover) , aby przygotować się do łączenia się z maszynami wirtualnymi platformy Azure po przejściu



## <a name="run-a-failover"></a>Uruchamianie trybu failover

### <a name="verify-server-properties"></a>Sprawdź właściwości serwera

Sprawdź właściwości serwera i upewnij się, że jest ono zgodne z [wymaganiami platformy Azure](vmware-physical-azure-support-matrix.md#replicated-machines) dla maszyn wirtualnych platformy Azure.

1. W obszarze **chronione elementy** kliknij pozycję **zreplikowane elementy**, a następnie wybierz maszynę.
2. W okienku **replikowany element** znajduje się podsumowanie informacji o maszynie, kondycji i najnowszych dostępnych punktów odzyskiwania. Kliknij przycisk **Właściwości**, aby wyświetlić więcej szczegółów.
3. W obszarze **obliczenia i sieć** można zmodyfikować nazwę platformy Azure, grupę zasobów, rozmiar docelowy, [zestaw dostępności](../virtual-machines/windows/tutorial-availability-sets.md)i ustawienia dysku zarządzanego.
4. Możesz wyświetlać i modyfikować ustawienia sieciowe, w tym sieć/podsieć, w której zlokalizowana będzie maszyna wirtualna na platformie Azure po wejściu w tryb failover, oraz adres IP, który będzie do niej przypisany.
5. W obszarze **dyski** można wyświetlić informacje o systemie operacyjnym komputera i dyskach z danymi.

### <a name="fail-over-to-azure"></a>Przełączenie do trybu failover na platformie Azure

1. W obszarze **Ustawienia**  >  **zreplikowane elementy** kliknij maszynę > **tryb failover**.
2. W obszarze **Tryb failover** wybierz **Punkt odzyskiwania**, którego chcesz użyć do przełączenia do trybu failover. Możesz użyć jednej z następujących opcji:
   - **Najnowszy**: ta opcja najpierw przetwarza wszystkie dane wysyłane do usługi Site Recovery. Zapewnia najniższą wartość celu puntu odzyskiwania, ponieważ maszyna wirtualna platformy Azure utworzona po przejściu do trybu failover zawiera wszystkie dane, które zostały zreplikowane w usłudze Site Recovery do momentu włączenia trybu failover.
   - **Najnowsza przetworzony**: Ta opcja powoduje przełączenie komputera w tryb failover do najnowszego punktu odzyskiwania przetworzonego przez Site Recovery. Ta opcja zapewnia niską wartość celu czasu odzyskiwania, ponieważ nie wymaga przetwarzania nieprzetworzonych danych.
   - **Najnowsza spójna dla aplikacji**: Ta opcja powoduje przełączenie maszyny w tryb failover do najnowszego punktu odzyskiwania spójnego na poziomie aplikacji przetworzonego przez Site Recovery.
   - **Niestandardowy**: umożliwia określenie punktu odzyskiwania.

3. Wybierz opcję **Zamknij maszynę przed rozpoczęciem pracy w trybie failover** , jeśli chcesz, aby Site Recovery próbuje zamknąć maszynę źródłową przed wyzwoleniem trybu failover. Przełączanie do trybu failover będzie kontynuowane, nawet jeśli zamknięcie nie powiedzie się. Postęp pracy w trybie failover można wykonać na stronie **zadań** .
4. Jeśli przeprowadzono przygotowanie do nawiązania połączenia z maszyną wirtualną platformy Azure, należy po przełączeniu w tryb failover nawiązać połączenie w celu weryfikacji.
5. Po weryfikacji należy **Zatwierdzić** tryb failover. To działanie usuwa wszystkie dostępne punkty odzyskiwania.

> [!WARNING]
> Nie Anuluj trybu failover w toku. Przed rozpoczęciem pracy w trybie failover replikacja maszyny zostaje zatrzymana. Jeśli anulujesz tryb failover, zatrzyma się, ale maszyna nie zostanie ponownie zreplikowana.
> W przypadku serwerów fizycznych dodatkowe przetwarzanie trybu failover może potrwać około ośmiu do dziesięciu minut.

## <a name="automate-actions-during-failover"></a>Automatyzowanie akcji podczas pracy w trybie failover

Można zautomatyzować akcje podczas pracy w trybie failover. W tym celu można użyć skryptów lub elementów Runbook usługi Azure Automation w planach odzyskiwania.

- [Dowiedz się więcej](site-recovery-create-recovery-plans.md) na temat tworzenia i dostosowywania planów odzyskiwania, w tym dodawania skryptów.
- [Dowiedz się](site-recovery-runbook-automation.md) , obejmować dodawanie elementów Runbook Azure Automation do planów odzyskiwania.

## <a name="configure-settings-after-failover"></a>Skonfiguruj ustawienia po przejściu w tryb failover

Po przejściu w tryb failover musisz [skonfigurować ustawienia platformy Azure](site-recovery-failover.md#prepare-in-azure-to-connect-after-failover) , aby połączyć się z replikowanymi maszynami wirtualnymi platformy Azure. Ponadto skonfiguruj [wewnętrzne i publiczne](site-recovery-failover.md#set-up-ip-addressing) adresy IP.

## <a name="prepare-for-reprotection-and-failback"></a>Przygotowanie do ochrony i powrotu po awarii

Po przejściu w tryb failover na platformę Azure należy ponownie włączyć ochronę maszyn wirtualnych platformy Azure przez replikowanie ich do lokacji lokalnej. Po przeprowadzeniu replikacji można z powrotem przejść do lokalnego programu, uruchamiając tryb failover z platformy Azure do lokacji lokalnej.

1. Serwery fizyczne replikowane do platformy Azure za pomocą Site Recovery mogą kończyć się niepowodzeniem tylko jako maszyny wirtualne VMware. Do powrotu po awarii potrzebna jest infrastruktura VMware. Wykonaj kroki opisane w [tym artykule](vmware-azure-prepare-failback.md) , aby przygotować się do ponownej ochrony i powrotu po awarii, w tym konfigurowania serwera przetwarzania na platformie Azure oraz lokalnego serwera docelowego i konfigurowania sieci VPN typu lokacja-lokacja, lub ExpressRoute prywatnej komunikacji równorzędnej na potrzeby powrotu po awarii.
2. Upewnij się, że lokalny serwer konfiguracji jest uruchomiony i połączony z platformą Azure. Podczas pracy w trybie failover na platformie Azure lokacja lokalna może być niedostępna, a serwer konfiguracji może być niedostępny lub wyłączony. Podczas powrotu po awarii maszyna wirtualna musi znajdować się w bazie danych serwera konfiguracji. W przeciwnym razie powrót po awarii nie powiedzie się.
3. Usuń wszystkie migawki na lokalnym głównym serwerze docelowym. Ochrona nie będzie zadziałała, jeśli istnieją migawki.  Migawki na maszynie wirtualnej są automatycznie scalane podczas zadania ponownego włączania ochrony.
4. W przypadku ponownego włączania ochrony maszyn wirtualnych zebranych w grupie replikacji w celu zapewnienia spójności wielu maszyn wirtualnych upewnij się, że wszystkie mają ten sam system operacyjny (Windows lub Linux) i upewnij się, że wdrażany główny serwer docelowy ma ten sam typ systemu operacyjnego. Wszystkie maszyny wirtualne w grupie replikacji muszą korzystać z tego samego głównego serwera docelowego.
5. Otwórz [wymagane porty](vmware-azure-prepare-failback.md#ports-for-reprotectionfailback) na potrzeby powrotu po awarii.
6. Upewnij się, że vCenter Server jest połączony przed powrotem po awarii. W przeciwnym razie odłączanie dysków i dołączenie ich z powrotem do maszyny wirtualnej nie powiedzie się.
7. Jeśli serwer vCenter zarządza maszynami wirtualnymi, do których nastąpi powrót po awarii, upewnij się, że masz wymagane uprawnienia. W przypadku przeprowadzania odnajdywania vCenter użytkownika tylko do odczytu i ochrony maszyn wirtualnych ochrona kończy się powodzeniem, a tryb failover działa. Jednak podczas samoochrony tryb failover nie powiedzie się, ponieważ nie można odnaleźć magazynów danych i nie jest on wyświetlany podczas ochrony. Aby rozwiązać ten problem, możesz zaktualizować poświadczenia vCenter przy użyciu [odpowiedniego konta/uprawnień](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery), a następnie ponownie wykonać zadanie. 
8. Jeśli używasz szablonu do tworzenia maszyn wirtualnych, upewnij się, że każda maszyna wirtualna ma własny identyfikator UUID dla dysków. Jeśli lokalny identyfikator UUID maszyny wirtualnej jest niezależny od identyfikatora UUID głównego serwera docelowego, ponieważ obie zostały utworzone na podstawie tego samego szablonu, ponownej ochrony zakończy się niepowodzeniem. Wdróż z innego szablonu.
9. W przypadku powrotu po awarii do alternatywnej vCenter Server upewnij się, że są odnajdywane nowe vCenter Server i główny serwer docelowy. Zazwyczaj jeśli nie są to magazyny danych, są niedostępne lub nie są widoczne w ponownej **ochronie**.
10. Sprawdź następujące scenariusze, w których nie można przeprowadzić powrotu po awarii:
    - Jeśli używasz wersji bezpłatnej ESXi 5,5 lub bezpłatnej funkcji hypervisor vSphere 6. Uaktualnij do innej wersji.
    - Jeśli masz serwer fizyczny z systemem Windows Server 2008 R2 z dodatkiem SP1.
    - Zmigrowane maszyny wirtualne.
    - Maszyna wirtualna, która została przeniesiona do innej grupy zasobów.
    - Replika maszyny wirtualnej platformy Azure, która została usunięta.
    - Replika maszyny wirtualnej platformy Azure, która nie jest chroniona (replikacja do lokacji lokalnej).
10. [Zapoznaj się z typami powrotu po awarii](concepts-types-of-failback.md) , których można użyć — odzyskiwanie oryginalnej lokalizacji i odzyskiwanie lokalizacji alternatywnej.


## <a name="reprotect-azure-vms-to-an-alternate-location"></a>Ponowne włączanie ochrony maszyn wirtualnych platformy Azure do lokalizacji alternatywnej

Ta procedura zakłada, że lokalna maszyna wirtualna jest niedostępna.

1. W obszarze **Ustawienia**> magazynu  >  **zreplikowane elementy** kliknij prawym przyciskiem myszy maszynę, która została przełączona w tryb failover > **ponownie włączyć ochronę**.
2. W obszarze **Włącz ponownie ochronę** sprawdź, czy wybrano opcję **Z platformy Azure do serwera lokalnego**.
3. Określ lokalny główny serwer docelowy oraz serwer przetwarzania.
4. W obszarze **Magazyn danych** wybierz główny docelowy magazyn danych, do którego chcesz odzyskać dyski w środowisku lokalnym.
       - Użyj tej opcji, jeśli lokalna maszyna wirtualna została usunięta lub nie istnieje, i musisz utworzyć nowe dyski.
       - To ustawienie jest ignorowane, jeśli dyski już istnieją, ale trzeba określić wartość.
5. Wybierz główny docelowy dysk przechowywania. Zasady powrotu po awarii są wybierane automatycznie.
6. Kliknij przycisk **OK**, aby rozpocząć ponowne włączanie ochrony. Zadanie rozpoczyna replikację maszyny wirtualnej platformy Azure do lokacji lokalnej. Na karcie **Zadania** można śledzić postęp.

> [!NOTE]
> Jeśli chcesz odzyskać maszynę wirtualną platformy Azure do istniejącej lokalnej maszyny wirtualnej, zainstaluj magazyn danych lokalnej maszyny wirtualnej z dostępem odczytu/zapisu na hoście ESXi głównego serwera docelowego.


## <a name="fail-back-from-azure"></a>Powrót po awarii z platformy Azure

Uruchom tryb failover w następujący sposób:

1. Na stronie **Zreplikowane elementy** kliknij prawym przyciskiem myszy maszynę, a następnie wybierz opcję **Nieplanowana praca w trybie failover**.
2. W obszarze **Potwierdź tryb failover** sprawdź, czy wybrano kierunek trybu failover Z platformy Azure.
3. Wybierz punkt odzyskiwania, którego chcesz użyć do przejścia w tryb failover.
    - Zalecamy korzystanie z **najnowszego** punktu odzyskiwania. Punkt spójności aplikacji jest za ostatnim punktem w czasie i powoduje utratę danych.
    - **Najnowsza** to punkt odzyskiwania spójny na poziomie awarii.
    - Podczas przełączania do trybu failover usługa Site Recovery wyłącza maszyny wirtualne platformy Azure i uruchamia lokalną maszynę wirtualną. Wiąże się to z przestojem, należy więc wybrać odpowiedni moment.
4. Kliknij prawym przyciskiem myszy maszynę, a następnie kliknij przycisk **Zatwierdź**. Spowoduje to wyzwolenie zadania, które usunie maszyny wirtualne platformy Azure.
5. Sprawdź, czy maszyna wirtualna platformy Azure została prawidłowo wyłączona.


## <a name="reprotect-on-premises-machines-to-azure"></a>Ponowne włączanie ochrony lokalnych maszyn na platformie Azure

Dane powinny teraz ponownie znajdować się w lokacji lokalnej, ale nie są replikowane na platformie Azure. Możesz ponownie rozpocząć replikowanie na platformie Azure w następujący sposób:

1. W magazynie w obszarze **Ustawienia** >**Zreplikowane elementy** wybierz maszyny wirtualne, które przywrócono po awarii, i kliknij przycisk **Włącz ponownie ochronę**.
2. Wybierz serwer przetwarzania używany do wysyłania replikowanych danych do platformy Azure, a następnie kliknij przycisk **OK**.


## <a name="next-steps"></a>Następne kroki

Po zakończeniu zadania ponownego włączania ochrony lokalna maszyna wirtualna jest replikowana na platformę Azure. W razie potrzeby można [uruchomić kolejną pracę w trybie failover](site-recovery-failover.md) na platformie Azure.
