---
title: Włącz maszyny wirtualne VMware na potrzeby odzyskiwania po awarii przy użyciu Azure Site Recovery
description: W tym artykule opisano sposób włączania replikacji maszyn wirtualnych VMware na potrzeby odzyskiwania po awarii przy użyciu usługi Azure Site Recovery
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.date: 12/07/2020
ms.topic: conceptual
ms.author: ramamill
ms.openlocfilehash: 19a98b5786f35839d84e1e969c29e45e2b5e8dea
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98573398"
---
# <a name="enable-replication-to-azure-for-vmware-vms"></a>Włącz replikację do platformy Azure dla maszyn wirtualnych VMware

W tym artykule opisano sposób włączania replikacji lokalnych maszyn wirtualnych programu VMware do platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne

W tym artykule przyjęto założenie, że system spełnia następujące kryteria:

- [Skonfiguruj lokalne środowisko źródłowe](vmware-azure-set-up-source.md).
- [Skonfiguruj środowisko docelowe na platformie Azure](vmware-azure-set-up-target.md).
- Przed rozpoczęciem [Sprawdź, czy zostały spełnione wymagania i wymagania wstępne](vmware-physical-azure-support-matrix.md) . Ważne rzeczy do zanotowania:
  - [Obsługiwane systemy operacyjne](vmware-physical-azure-support-matrix.md#replicated-machines) dla replikowanych maszyn.
  - Obsługa [magazynu i dysku](vmware-physical-azure-support-matrix.md#storage) .
  - [Wymagania dotyczące platformy Azure](vmware-physical-azure-support-matrix.md#azure-vm-requirements) , które powinny być zgodne z maszynami lokalnymi.

### <a name="resolve-common-issues"></a>Rozwiązywanie typowych problemów

- Każdy dysk powinien być mniejszy niż 4 TB.
- Dysk systemu operacyjnego powinien być dyskiem podstawowym, a nie dyskiem dynamicznym.
- W przypadku maszyn wirtualnych z obsługą interfejsu UEFI 2, Rodzina systemów operacyjnych powinna być systemem Windows, a dysk rozruchowy powinien być mniejszy niż 300 GB.

## <a name="before-you-start"></a>Przed rozpoczęciem

Podczas replikowania maszyn wirtualnych programu VMware należy pamiętać o następujących kwestiach:

- Twoje konto użytkownika platformy Azure musi mieć określone [uprawnienia](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) , aby umożliwić replikację nowej maszyny wirtualnej na platformie Azure.
- Maszyny wirtualne VMware są odnajdywane co 15 minut. Po przeprowadzeniu odnajdywania maszyny wirtualne mogą pojawić się w Azure Portal po 15 minutach lub dłużej. Po dodaniu nowego serwera vCenter lub hosta vSphere odnajdywanie może potrwać 15 minut lub dłużej.
- Zaktualizowanie środowiska na maszynie wirtualnej w portalu może potrwać 15 minut lub dłużej. Na przykład instalacja narzędzi VMware.
- Możesz sprawdzić czas ostatniego wykrycia maszyn wirtualnych VMware: zobacz **ostatni kontakt w** polu na stronie **serwery konfiguracji** dla hosta vCenter Server/vSphere.
- Aby dodać maszyny wirtualne do replikacji bez oczekiwania na zaplanowane odnajdywanie, wyróżnij serwer konfiguracji (ale nie klikaj go), a następnie wybierz pozycję **Odśwież**.
- Po włączeniu replikacji, jeśli maszyna wirtualna jest przygotowana, serwer przetwarzania automatycznie zainstaluje usługę mobilności Azure Site Recovery na maszynie wirtualnej.

## <a name="enable-replication"></a>Włączanie replikacji

Przed wykonaniem kroków opisanych w tej sekcji zapoznaj się z następującymi informacjami:

- Azure Site Recovery teraz replikuje dane bezpośrednio do dysków zarządzanych w przypadku wszystkich nowych replikacji. Serwer przetwarzania zapisuje dzienniki replikacji na koncie magazynu pamięci podręcznej w regionie docelowym. Te dzienniki są używane do tworzenia punktów odzyskiwania na dyskach zarządzanych repliki z konwencją nazewnictwa `asrseeddisk` .
- Obsługa replikacji z dyskami zarządzanymi odbywa się przy użyciu polecenia [AZ. RecoveryServices w wersji 2.0.0](https://www.powershellgallery.com/packages/Az.RecoveryServices/2.0.0-preview)
- W momencie przejścia w tryb failover wybrany punkt odzyskiwania jest używany do tworzenia dysku zarządzanego przez użytkownika.
- Nie ma to wpływu na maszyny wirtualne, które zostały wcześniej skonfigurowane do replikowania na docelowe konta magazynu.
- Replikacja do kont magazynu dla nowej maszyny wirtualnej jest dostępna tylko za pośrednictwem interfejsu API REST (Representational State Transfer) i programu PowerShell. Do replikacji na konta magazynu używaj interfejsu API REST platformy Azure w wersji 2016-08-10 lub 2018-01-10.

Aby włączyć replikację, wykonaj następujące kroki:

1. Przejdź do **kroku 2: Replikowanie**  >  **źródła** aplikacji. Po włączeniu replikacji po raz pierwszy wybierz pozycję **+ Replikuj** w magazynie, aby włączyć replikację dla dodatkowych maszyn wirtualnych.
1. Na stronie **źródłowej** > **Źródło** wybierz serwer konfiguracji.
1. W obszarze **Typ maszyny** wybierz pozycję **Virtual Machines** lub **maszyny fizyczne**.
1. W obszarze **vCenter/vSphere Hypervisor** wybierz serwer vCenter zarządzający hostem vSphere lub wybierz tego hosta. To ustawienie nie ma zastosowania, jeśli są replikowane komputery fizyczne.
1. Wybierz serwer przetwarzania. Jeśli nie utworzono żadnych dodatkowych serwerów przetwarzania, w menu rozwijanym będzie dostępny serwer przetwarzania z wbudowaną konfiguracją. Stan kondycji każdego serwera przetwarzania jest wskazywany zgodnie z zalecanymi limitami i innymi parametrami. Wybierz serwer przetwarzania w dobrej kondycji. Nie można wybrać serwera przetwarzania [krytycznego](vmware-physical-azure-monitor-process-server.md#process-server-alerts) . Możesz [rozwiązywać problemy i](vmware-physical-azure-troubleshoot-process-server.md) rozwiązywać te błędy **albo** skonfigurować [serwer przetwarzania skalowalny](vmware-azure-set-up-process-server-scale.md)w poziomie.

   :::image type="content" source="./media/vmware-azure-enable-replication/ps-selection.png" alt-text="Włącz okno źródła replikacji":::

   > [!NOTE]
   > Począwszy od [wersji 9,24](site-recovery-whats-new.md), wprowadzono dodatkowe alerty w celu udoskonalenia alertów kondycji serwera przetwarzania. Uaktualnij składniki Site Recovery do wersji 9,24 lub nowszej w celu wygenerowania wszystkich alertów.

1. W obszarze **cel** wybierz subskrypcję i grupę zasobów, w której chcesz utworzyć maszyny wirtualne w trybie failover. Wybierz model wdrażania, którego chcesz użyć na platformie Azure dla maszyn wirtualnych w trybie failover.
1. Wybierz sieć i podsieć platformy Azure, z którą maszyny wirtualne platformy Azure będą łączyć się po przejściu do trybu failover. Sieć musi znajdować się w tym samym regionie co magazyn usługi Site Recovery.

   Wybierz pozycję **Konfiguruj teraz dla wybranych maszyn** , aby zastosować ustawienia sieci do wszystkich maszyn wirtualnych wybranych do ochrony. Wybierz pozycję **Konfiguruj później** , aby wybrać sieć platformy Azure na maszynę wirtualną. Jeśli nie masz sieci, musisz ją utworzyć. Aby utworzyć sieć przy użyciu Azure Resource Manager, wybierz pozycję **Utwórz nowy**. Wybierz podsieć, jeśli ma zastosowanie, a następnie wybierz przycisk **OK**.

   :::image type="content" source="./media/vmware-azure-enable-replication/enable-rep3.png" alt-text="Włącz okno celu replikacji":::

1. W obszarze **maszyny wirtualne**  >  **Wybierz pozycję Maszyny** wirtualne, a następnie wybierz każdą maszynę wirtualną, którą chcesz replikować. Można wybrać tylko maszyny wirtualne, dla których można włączyć replikację. Następnie wybierz przycisk **OK**. Jeśli nie możesz wyświetlić lub wybrać żadnej konkretnej maszyny wirtualnej, zobacz [maszyna źródłowa nie znajduje się na liście w Azure Portal](vmware-azure-troubleshoot-replication.md#step-3-troubleshoot-source-machines-that-arent-available-for-replication) , aby rozwiązać ten problem.

   :::image type="content" source="./media/vmware-azure-enable-replication/enable-replication5.png" alt-text="Okno włączania replikacji Wybieranie maszyn wirtualnych":::

1. Aby   >  **skonfigurować** właściwości, wybierz konto, za pomocą którego serwer przetwarzania automatycznie zainstaluje Site Recovery usługę mobilności na maszynie wirtualnej. Ponadto wybierz typ docelowego dysku zarządzanego do użycia na potrzeby replikacji na podstawie wzorców zmian danych.
1. Domyślnie replikowane są wszystkie dyski źródłowej maszyny wirtualnej. Aby wykluczyć dyski z replikacji, usuń zaznaczenie pola wyboru **Dołącz** dla wszystkich dysków, które nie mają być replikowane. Następnie wybierz przycisk **OK**. Później możesz skonfigurować dodatkowe właściwości. [Dowiedz się więcej](vmware-azure-exclude-disk.md) na temat wykluczania dysków.

   :::image type="content" source="./media/vmware-azure-enable-replication/enable-replication6.png" alt-text="Okno włączania konfiguracji replikacji":::

1. W obszarze **Ustawienia replikacji**  >  **Skonfiguruj ustawienia replikacji** Sprawdź, czy wybrano odpowiednie zasady replikacji. Ustawienia zasad replikacji można modyfikować w obszarze **Ustawienia** zasady  >  **replikacji**  >  _Nazwa zasad_  >  **Edytuj ustawienia**. Zmiany zastosowane do zasad dotyczą również replikowania i nowych maszyn wirtualnych.
1. Jeśli chcesz zebrać maszyny wirtualne do grupy replikacji, Włącz **spójność z obsługą wiele maszyn** wirtualnych. Określ nazwę grupy, a następnie wybierz przycisk **OK**.

   > [!NOTE]
   > - Maszyny wirtualne w grupie replikacji są replikowane razem i udostępniają punkty odzyskiwania spójne pod kątem awarii i spójne z aplikacjami po przełączeniu w tryb failover.
   > - Zbierz maszyny wirtualne i serwery fizyczne w taki sposób, aby odzwierciedlały obciążenia. Włączenie spójności między MASZYNami wirtualnymi może wpłynąć na wydajność obciążeń. Zrób to tylko wtedy, gdy maszyny wirtualne działają w tym samym obciążeniu i potrzebujesz spójności.

   :::image type="content" source="./media/vmware-azure-enable-replication/enable-replication7.png" alt-text="Włącz okno replikacji":::

1. Wybierz pozycję **Włącz replikację**. Postęp zadania **Włącz ochronę** można śledzić w obszarze **Ustawienia**  >  **zadania**  >  **Site Recovery zadania**. Po uruchomieniu zadania **finalizowania ochrony** maszyna wirtualna jest gotowa do pracy w trybie failover.

## <a name="monitor-initial-replication"></a>Monitoruj replikację początkową

Po ukończeniu "Włącz replikację" chronionego elementu Azure Site Recovery inicjuje replikację (równoznaczną z synchronizacją) danych z maszyny źródłowej do regionu docelowego. W tym okresie zostanie utworzona replika dysków źródłowych. Dopiero po zakończeniu kopiowania dysków oryginalnych zmiany różnicowe są kopiowane do regionu docelowego. Czas potrzebny do skopiowania oryginalnych dysków zależy od wielu parametrów, takich jak:

- rozmiar dysków maszyny źródłowej
- przepustowość dostępna do transferu danych na platformę Azure (możesz użyć planisty wdrożenia, aby określić optymalną przepustowość)
- Przetwarzaj zasoby serwera, takie jak pamięć, wolne miejsce na dysku, dostępne dla pamięci podręcznej & przetwarzania danych z chronionych elementów (Upewnij się, że serwer przetwarzania jest w [dobrej kondycji](vmware-physical-azure-monitor-process-server.md#monitor-proactively))

Aby śledzić postęp replikacji początkowej, przejdź do magazynu usługi Recovery Services w obszarze Azure Portal-> zreplikowane elementy > — wartość kolumny "status" dla zreplikowanego elementu. Stan pokazuje procent ukończenia replikacji początkowej. Po umieszczeniu wskaźnika myszy na stanie będzie dostępne "całkowita ilość przesłanych danych". Po kliknięciu pozycji stan zostanie otwarta strona kontekstowa zawierająca następujące parametry:

- Ostatnio odświeżono — wskazuje ostatnią godzinę, o której informacje o replikacji całego komputera zostały odświeżone przez usługę.
- Procent ukończenia — wskazuje procent początkowej replikacji dla maszyny wirtualnej
- Całkowita liczba przesłanych danych — ilość danych transferowanych z maszyny wirtualnej na platformę Azure

    :::image type="content" source="media/vmware-azure-enable-replication/initial-replication-state.png" alt-text="stan replikacji" lightbox="media/vmware-azure-enable-replication/initial-replication-state.png":::

- Postęp synchronizacji (aby śledzić szczegóły na poziomie dysku)
    - Stan replikacji
      - Jeśli replikacja nie jest jeszcze uruchomiona, stan jest aktualizowany jako "w kolejce". Podczas replikacji początkowej tylko trzy dyski są replikowane jednocześnie. Ten mechanizm ma na celu uniknięcie ograniczenia przepustowości na serwerze przetwarzania.
      - Po uruchomieniu replikacji stan jest aktualizowany jako "w toku".
      - Po zakończeniu replikacji początkowej stan jest oznaczony jako "kompletna".        
   - Site Recovery odczytuje przez oryginalny dysk, przesyła dane na platformę Azure i przechwytuje postępy na poziomie dysku. Należy pamiętać, że Site Recovery pomija replikację niezajętego rozmiaru dysku i dodaje ją do ukończonych danych. W związku z tym suma danych transferowanych na wszystkich dyskach może nie zostać dodana do "całkowitej ilości przesłanych danych" na poziomie maszyny wirtualnej.
   - Po kliknięciu dymka informacji dotyczącego dysku możesz uzyskać szczegółowe informacje o tym, kiedy replikacja (równoznaczna z synchronizacją) została wyzwolona dla dysku, dane przesłane do platformy Azure w ciągu ostatnich 15 minut, po których następuje godzina ostatniego odświeżenia. Ta sygnatura czasowa wskazuje najnowszy czas, w którym informacje zostały odebrane przez usługę platformy Azure z maszyny źródłowej :::image type="content" source="media/vmware-azure-enable-replication/initial-replication-info-balloon.png" alt-text="początkowa-replikacja-informacje-dymek-szczegóły" lightbox="media/vmware-azure-enable-replication/initial-replication-info-balloon.png":::
   - Zostanie wyświetlona kondycja każdego dysku
      - Jeśli replikacja jest wolniejsza niż oczekiwana, stan dysku zmieni się na ostrzeżenie
      - Jeśli replikacja nie postępuje, stan dysku zmieni się na krytyczny

Jeśli kondycja jest w stanie krytycznym/ostrzeżeniem, upewnij się, że kondycja replikacji maszyny i [serwera przetwarzania](vmware-physical-azure-monitor-process-server.md) jest w dobrej kondycji. 

Po ukończeniu zadania włączania replikacji postęp replikacji będzie równy 0%, a całkowita liczba przesłanych danych będzie równa ". Po kliknięciu dane dla każdego z zidentyfikowanych dysków będą mieć wartość "Brak". Oznacza to, że replikacja jest jeszcze uruchomiona, a Azure Site Recovery jeszcze nie otrzymuje najnowszych statystyk. Postęp jest odświeżany z interwałem wynoszącym 30 minut.

> [!NOTE]
> Upewnij się, że serwery konfiguracji, skalowalne w poziomie serwery przetwarzania i agenci mobilności są aktualizowane w wersji 9,36 lub nowszej, aby zapewnić, że dokładny postęp jest przechwytywany i wysyłany do usług Site Recovery.


## <a name="view-and-manage-vm-properties"></a>Wyświetlanie właściwości maszyny wirtualnej i zarządzanie nimi

Następnie sprawdź właściwości źródłowej maszyny wirtualnej. Należy pamiętać, że nazwa maszyny wirtualnej platformy Azure musi być zgodna z [wymaganiami dotyczącymi maszyn wirtualnych platformy Azure](vmware-physical-azure-support-matrix.md#replicated-machines).

1. Przejdź do   >  **pozycji Ustawienia zreplikowane elementy**, a następnie wybierz maszynę wirtualną. Na stronie **podstawowe** informacje o ustawieniach i stanie maszyny wirtualnej.
1. W obszarze **Właściwości** można wyświetlić informacje dotyczące replikacji i trybu failover dla maszyny wirtualnej.
1. We właściwościach obliczeń **obliczeniowych i sieciowych**  >  można zmienić wiele właściwości maszyny wirtualnej.

   :::image type="content" source="./media/vmware-azure-enable-replication/vmproperties.png" alt-text="Okno właściwości obliczania i sieci":::

   - **Nazwa maszyny wirtualnej platformy Azure**: zmodyfikuj nazwę, aby spełniała wymagania platformy Azure, w razie potrzeby.
   - **Docelowy rozmiar maszyny wirtualnej lub typ maszyny wirtualnej**: domyślny rozmiar maszyny wirtualnej jest wybierany na podstawie parametrów obejmujących liczbę dysków, liczbę kart sieciowych, liczbę rdzeni procesora CPU, pamięć i dostępne rozmiary ról maszyny wirtualnej w docelowym regionie platformy Azure. Azure Site Recovery wybiera pierwszy dostępny rozmiar maszyny wirtualnej spełniający wszystkie kryteria. Przed przejściem w tryb failover możesz wybrać inny rozmiar maszyny wirtualnej w zależności od potrzeb. Rozmiar dysku maszyny wirtualnej jest również oparty na rozmiarze dysku źródłowego i można go zmienić tylko po przejściu do trybu failover. Dowiedz się więcej na temat rozmiarów dysków i szybkości operacji we/wy na [potrzeby skalowalności i wydajności dla dysków maszyn wirtualnych](../virtual-machines/disks-scalability-targets.md).
   - **Grupa zasobów**: można wybrać [grupę zasobów](../azure-resource-manager/management/overview.md#resource-groups), z której maszyna wirtualna stanie się częścią przełączenia w tryb failover. To ustawienie można zmienić w dowolnym momencie przed przełączeniem w tryb failover. Po przejściu w tryb failover w przypadku migrowania maszyny wirtualnej do innej grupy zasobów ustawienia ochrony dla tej maszyny wirtualnej zostaną przerwane.
   - **Zestaw dostępności**: można wybrać [zestaw dostępności](../virtual-machines/windows/tutorial-availability-sets.md) , jeśli maszyna wirtualna musi być częścią wpisu do trybu failover. Po wybraniu zestawu dostępności należy pamiętać o następujących kwestiach:
     - Wyświetlane są tylko zestawy dostępności należące do określonej grupy zasobów.
     - Maszyny wirtualne, które znajdują się w różnych sieciach wirtualnych, nie mogą być częścią tego samego zestawu dostępności.
     - Tylko maszyny wirtualne o tym samym rozmiarze mogą być częścią zestawu dostępności.

1. Możesz również dodać informacje dotyczące sieci docelowej, podsieci i adresu IP przypisanego do maszyny wirtualnej platformy Azure.
1. Na **dyskach** można zobaczyć system operacyjny i dyski danych na maszynie wirtualnej, która zostanie zreplikowana.

### <a name="configure-networks-and-ip-addresses"></a>Konfigurowanie sieci i adresów IP

Można ustawić docelowy adres IP:

- Jeśli nie podano adresu, maszyna wirtualna w trybie failover używa protokołu DHCP.
- Jeśli ustawisz adres, który nie jest dostępny w trybie failover, praca w trybie failover nie działa.
- Jeśli adres jest dostępny w sieci testowej pracy w trybie failover, można użyć tego samego docelowego adresu IP dla testowej pracy w trybie failover.

Liczba kart sieciowych zależy od rozmiaru określonego dla docelowej maszyny wirtualnej w następujący sposób:

- Jeśli liczba kart sieciowych w źródłowej maszynie wirtualnej jest mniejsza lub równa liczbie kart, które są dozwolone dla rozmiaru docelowej maszyny wirtualnej, obiekt docelowy ma taką samą liczbę kart sieciowych jak źródło.
- Jeśli liczba kart sieciowych dla źródłowej maszyny wirtualnej przekracza liczbę dozwoloną dla rozmiaru docelowej maszyny wirtualnej, używany jest maksymalny rozmiar docelowy. Na przykład jeśli źródłowa maszyna wirtualna ma dwie karty sieciowe, a rozmiar docelowej maszyny wirtualnej obsługuje cztery, docelowa maszyna wirtualna ma dwie karty. Jeśli źródłowa maszyna wirtualna ma dwie karty sieciowe, ale rozmiar docelowy obsługuje tylko jeden z nich, docelowa maszyna wirtualna ma tylko jedną kartę sieciową.
- Jeśli maszyna wirtualna ma kilka kart sieciowych, wszystkie będą łączyć się z tą samą siecią. Ponadto pierwsza karta wyświetlana na liście będzie domyślną kartą sieciową na maszynie wirtualnej platformy Azure.

### <a name="azure-hybrid-benefit"></a>Korzyść użycia hybrydowego platformy Azure

Klienci programu Microsoft Software Assurance mogą korzystać z Korzyść użycia hybrydowego platformy Azure, aby zaoszczędzić na kosztach licencjonowania na komputerach z systemem Windows Server migrowanych do platformy Azure. Korzyść dotyczy również odzyskiwania po awarii platformy Azure. Jeśli masz uprawnienia, możesz przypisać korzyść do maszyny wirtualnej, która Site Recovery utworzona w przypadku przejścia w tryb failover.

1. Przejdź do **właściwości komputera i sieci** zreplikowanej maszyny wirtualnej.
1. Odpowiedź na pytanie, czy masz licencję systemu Windows Server, która uprawnia do Korzyść użycia hybrydowego platformy Azure.
1. Upewnij się, że masz uprawniającą licencję systemu Windows Server z programem Software Assurance, której możesz użyć do zastosowania korzyści dla maszyny wirtualnej, która zostanie utworzona w trybie failover.
1. Zapisz ustawienia dla zreplikowanej maszyny wirtualnej.

[Dowiedz się więcej](https://azure.microsoft.com/pricing/hybrid-benefit/) o korzyść użycia hybrydowego platformy Azure.

## <a name="next-steps"></a>Następne kroki

Gdy maszyna wirtualna osiągnie stan chroniony, spróbuj przełączyć się do [trybu failover](site-recovery-failover.md) , aby sprawdzić, czy aplikacja jest wyświetlana na platformie Azure.

- [Dowiedz się więcej](site-recovery-manage-registration-and-protection.md) o tym, jak wyczyścić ustawienia rejestracji i ochrony, aby wyłączyć replikację.
- [Dowiedz się więcej](vmware-azure-disaster-recovery-powershell.md) o tym, jak zautomatyzować replikację maszyn wirtualnych przy użyciu programu PowerShell.
