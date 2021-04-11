---
title: Odzyskiwanie po awarii programu VMware z Azure Site Recovery
description: Ten artykuł zawiera omówienie odzyskiwania po awarii maszyn wirtualnych VMware na platformie Azure przy użyciu usługi Azure Site Recovery.
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/12/2019
ms.openlocfilehash: 8e72d66bcf8398946b8901ef86666aa9aba34105
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/07/2021
ms.locfileid: "106579082"
---
# <a name="about-disaster-recovery-of-vmware-vms-to-azure"></a>Informacje na temat odzyskiwania po awarii maszyn wirtualnych VMware na platformie Azure

Ten artykuł zawiera omówienie odzyskiwania po awarii dla lokalnych maszyn wirtualnych VMware na platformie Azure przy użyciu usługi [Azure Site Recovery](site-recovery-overview.md) .

## <a name="what-is-bcdr"></a>Co to jest BCDR?

Strategia ciągłości działania i odzyskiwania po awarii (BCDR) pomaga zapewnić, że Twoja firma działa. Podczas planowanych przestojów i nieoczekiwanych awarii BCDR zapewnia bezpieczeństwo i dostępność danych oraz gwarantuje, że aplikacje będą działać. Oprócz funkcji BCDR platformy, takich jak parowanie regionalne i magazyn o wysokiej dostępności, platforma Azure udostępnia Recovery Services jako integralną część rozwiązania BCDR. Usługi odzyskiwania obejmują: 

- [Azure Backup](../backup/backup-overview.md) tworzyć kopie zapasowe danych lokalnych i maszyn wirtualnych platformy Azure. Można utworzyć kopię zapasową plików i folderów, określonych obciążeń lub całej maszyny wirtualnej. 
- [Azure Site Recovery](site-recovery-overview.md) zapewnia odporność i odzyskiwanie po awarii dla aplikacji i obciążeń uruchomionych na maszynach lokalnych lub maszynach wirtualnych Azure IaaS. Site Recovery organizuje replikację i obsługuje pracę w trybie failover na platformie Azure, gdy wystąpi awaria. Obsługuje ona również odzyskiwanie z platformy Azure do lokacji głównej. 

> [!NOTE]
> Site Recovery nie przenosi ani nie zapisuje danych klienta poza regionem docelowym, w którym Konfiguracja odzyskiwania po awarii została skonfigurowana dla maszyn źródłowych. Klienci mogą wybrać magazyn Recovery Services z innego regionu, jeśli tak się wybierzesz. Magazyn Recovery Services zawiera metadane, ale nie dane rzeczywiste klienta.

## <a name="how-does-site-recovery-do-disaster-recovery"></a>Jak Site Recovery przeprowadzić odzyskiwanie po awarii?

1. Po przygotowaniu platformy Azure i lokacji lokalnej należy skonfigurować i włączyć replikację na maszynach lokalnych.
2. Site Recovery organizuje replikację początkową maszyny zgodnie z ustawieniami zasad.
3. Po replikacji początkowej Site Recovery replikuje zmiany różnicowe na platformie Azure. 
4. W przypadku replikacji wszystkich elementów zgodnie z oczekiwaniami należy wykonać drążenie odzyskiwania po awarii.
    - Funkcja drążenia pomaga zapewnić, że praca w trybie failover będzie działała zgodnie z oczekiwaniami, gdy wystąpi rzeczywista konieczność.
    - Funkcja drążenia wykonuje test pracy w trybie failover bez wpływu na środowisko produkcyjne.
5. Jeśli wystąpi awaria, należy uruchomić pełną pracę w trybie failover na platformie Azure. Możesz przejść do trybu failover na jednej maszynie lub utworzyć plan odzyskiwania, który przejdzie w tryb failover wielu maszyn w tym samym czasie.
6. W przypadku przejścia w tryb failover maszyny wirtualne platformy Azure są tworzone na podstawie danych maszyny wirtualnej na dyskach zarządzanych lub na kontach magazynu. Użytkownicy mogą nadal uzyskiwać dostęp do aplikacji i obciążeń z maszyny wirtualnej platformy Azure
7. Po ponownym udostępnieniu lokacji lokalnej nie można wrócić z powrotem z platformy Azure.
8. Po powrocie po awarii i zakończeniu pracy z poziomu lokacji głównej należy ponownie rozpocząć replikację lokalnych maszyn wirtualnych na platformę Azure.


## <a name="how-do-i-know-if-my-environment-is-suitable-for-disaster-recovery-to-azure"></a>Jak mogę sprawdzić, czy środowisko jest odpowiednie do odzyskiwania po awarii na platformie Azure?

Site Recovery może replikować dowolne obciążenia uruchomione na obsługiwanej maszynie wirtualnej VMware lub serwerze fizycznym. Poniżej przedstawiono elementy, które należy zaewidencjonować w Twoim środowisku:

- Jeśli replikujesz maszyny wirtualne VMware, czy korzystasz z odpowiednich wersji serwerów wirtualizacji VMware? [Sprawdź tutaj](vmware-physical-azure-support-matrix.md#on-premises-virtualization-servers).
- Czy maszyny, które mają być replikowane, działają w obsługiwanym systemie operacyjnym? [Sprawdź tutaj](vmware-physical-azure-support-matrix.md#replicated-machines).
- W przypadku odzyskiwania po awarii systemu Linux komputery z systemem plików i magazynem gościa są obsługiwane? [Sprawdź tutaj](vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage)
- Czy maszyny, które chcesz replikować, są zgodne z wymaganiami platformy Azure? [Sprawdź tutaj](vmware-physical-azure-support-matrix.md#azure-vm-requirements).
- Czy konfiguracja sieci jest obsługiwana? [Sprawdź tutaj](vmware-physical-azure-support-matrix.md#network).
- Czy konfiguracja magazynu jest obsługiwana? [Sprawdź tutaj](vmware-physical-azure-support-matrix.md#storage).


## <a name="what-do-i-need-to-set-up-in-azure-before-i-start"></a>Co muszę skonfigurować na platformie Azure przed rozpoczęciem?

Na platformie Azure należy przygotować następujące elementy:

1. Sprawdź, czy Twoje konto platformy Azure ma uprawnienia do tworzenia maszyn wirtualnych na platformie Azure.
2. Utwórz sieć platformy Azure, która będzie dołączana do maszyn wirtualnych platformy Azure po utworzeniu ich z poziomu kont magazynu lub dysków zarządzanych po przejściu do trybu failover.
3. Skonfiguruj magazyn usługi Azure Recovery Services dla Site Recovery. Magazyn znajduje się w Azure Portal i służy do wdrażania, konfigurowania, organizowania, monitorowania i rozwiązywania problemów z wdrożeniem Site Recovery.

*Potrzebujesz dodatkowej pomocy?*

Dowiedz się, jak skonfigurować platformę Azure, [weryfikując swoje konto](tutorial-prepare-azure.md#verify-account-permissions), tworząc [Sieć](tutorial-prepare-azure.md#set-up-an-azure-network)i [konfigurując magazyn](tutorial-prepare-azure.md#create-a-recovery-services-vault).



## <a name="what-do-i-need-to-set-up-on-premises-before-i-start"></a>Co muszę skonfigurować lokalnie przed rozpoczęciem?

W tym miejscu należy wykonać następujące czynności:

1. Należy skonfigurować kilka kont:

    - W przypadku replikowania maszyn wirtualnych VMware do Site Recovery dostępu vCenter Server lub vSphere hostów ESXi do automatycznego odnajdywania maszyn wirtualnych jest potrzebne konto.
    - Do zainstalowania agenta usługi mobilności Site Recovery na każdym komputerze fizycznym lub maszynie wirtualnej, która ma zostać zreplikowana, jest wymagane konto.

2. Jeśli jeszcze tego nie zrobisz, musisz sprawdzić zgodność infrastruktury programu VMware.
3. Upewnij się, że możesz połączyć się z maszynami wirtualnymi platformy Azure po przejściu do trybu failover. Można skonfigurować protokół RDP na lokalnych maszynach z systemem Windows lub na maszynach SSH na komputerze z systemem Linux.

*Potrzebujesz dodatkowej pomocy?*
- Przygotuj konta do [automatycznego odnajdywania](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery) i [instalacji usługi mobilności](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-mobility-service-installation).
- [Sprawdź](vmware-azure-tutorial-prepare-on-premises.md#check-vmware-requirements) , czy ustawienia programu VMware są zgodne.
- [Przygotuj](vmware-azure-tutorial-prepare-on-premises.md#prepare-to-connect-to-azure-vms-after-failover) się, aby nawiązać połączenie na platformie Azure po przejściu do trybu failover.
- Jeśli potrzebujesz więcej szczegółowych informacji na temat konfigurowania adresowania IP dla maszyn wirtualnych platformy Azure po przejściu do trybu failover, [Przeczytaj ten artykuł](concepts-on-premises-to-azure-networking.md).

## <a name="how-do-i-set-up-disaster-recovery"></a>Jak mogę skonfigurować odzyskiwanie po awarii?

Po włączeniu platformy Azure i infrastruktury lokalnej można skonfigurować odzyskiwanie po awarii.

1. Aby zrozumieć składniki, które należy wdrożyć, przejrzyj [architekturę programu VMware i platformy Azure](vmware-azure-architecture.md)oraz [architekturę fizyczną z platformą Azure](physical-azure-architecture.md). Istnieje kilka składników, dlatego ważne jest, aby zrozumieć, jak wszystkie te elementy pasują do siebie.
2. **Środowisko źródłowe**: pierwszy krok wdrożenia to skonfigurowanie środowiska źródłowego replikacji. Określ, co chcesz replikować, i miejsce, w którym chcesz przeprowadzić replikację.
3. **Serwer konfiguracji**: należy skonfigurować serwer konfiguracji w lokalnym środowisku źródłowym:
    - Serwer konfiguracji jest pojedynczą maszyną lokalną. W przypadku odzyskiwania po awarii programu VMware zalecamy wdrożenie go jako maszyny wirtualnej VMware, którą można wdrożyć przy użyciu szablonu OVF do pobrania.
    - Serwer konfiguracji koordynuje komunikację między środowiskiem lokalnym i platformą Azure
    - Kilka innych składników działa na komputerze serwera konfiguracji.
        - Serwer przetwarzania odbiera, optymalizuje i wysyła dane replikacji do konta magazynu pamięci podręcznej na platformie Azure. Obsługuje ona również automatyczną instalację usługi mobilności na maszynach, które mają być replikowane, i przeprowadza automatyczne odnajdowanie maszyn wirtualnych na serwerach VMware.
        - Główny serwer docelowy służy do obsługi replikacji danych podczas powrotu po awarii z platformy Azure.
    - Konfiguracja obejmuje rejestrowanie serwera konfiguracji w magazynie, pobieranie serwera MySQL i programu VMware PowerCLI oraz określanie kont utworzonych na potrzeby instalacji automatycznego odnajdywania i usługi mobilności.
4. **Środowisko docelowe**: skonfigurujesz docelowe środowisko platformy Azure, określając subskrypcję platformy Azure i ustawienia sieci.
5. **Zasady replikacji**: Określ sposób replikacji. Ustawienia obejmują między innymi tworzenie i przechowywanie punktów odzyskiwania oraz możliwość tworzenia migawek spójnych na poziomie aplikacji.
6. **Włącz replikację**. Należy włączyć replikację dla maszyn lokalnych. Jeśli utworzono konto w celu zainstalowania usługi mobilności, zostanie ona zainstalowana po włączeniu replikacji dla maszyny. 

*Potrzebujesz dodatkowej pomocy?*

- Aby zapoznać się z krótkim przewodnikiem, możesz skorzystać z [samouczka](vmware-azure-tutorial.md)dotyczącego programu VMware i [serwera fizycznego](physical-azure-disaster-recovery.md).
- [Dowiedz się więcej](vmware-azure-set-up-source.md) o konfigurowaniu środowiska źródłowego.
- [Informacje o](vmware-azure-deploy-configuration-server.md) wymaganiach dotyczących serwera konfiguracji i konfigurowaniu serwera konfiguracji przy użyciu szablonu OVF na potrzeby replikacji oprogramowania VMware. Jeśli z jakiegoś powodu nie można użyć szablonu lub są replikowane serwery fizyczne, [Użyj tych instrukcji](physical-azure-set-up-source.md#set-up-the-source-environment).
- [Dowiedz się więcej](vmware-azure-set-up-target.md) o ustawieniach docelowych.
- [Uzyskaj więcej informacji](vmware-azure-set-up-replication.md) na temat konfigurowania zasad replikacji.
- [Dowiedz się](vmware-azure-enable-replication.md) , jak włączyć replikację i [wykluczyć](vmware-azure-exclude-disk.md) dyski z replikacji.


## <a name="something-went-wrong-how-do-i-troubleshoot"></a>Coś poszło źle, jak rozwiązywać problemy?

- Najpierw należy przeprowadzić [monitorowanie wdrożenia](site-recovery-monitor-and-troubleshoot.md) w celu sprawdzenia stanu zreplikowanych elementów, zadań i problemów z infrastrukturą oraz zidentyfikowania błędów.
- Jeśli nie można ukończyć replikacji początkowej lub bieżąca replikacja nie działa zgodnie z oczekiwaniami, [zapoznaj się z tym artykułem](vmware-azure-troubleshoot-replication.md) dotyczącym typowych błędów i porad dotyczących rozwiązywania problemów.
- Jeśli masz problemy z automatyczną instalacją usługi mobilności na maszynach, które mają być replikowane, przejrzyj typowe błędy w [tym artykule](vmware-azure-troubleshoot-push-install.md).
- Jeśli tryb failover nie działa zgodnie z oczekiwaniami, Sprawdź typowe błędy w [tym artykule](site-recovery-failover-to-azure-troubleshoot.md).
- Jeśli powrót po awarii nie działa, sprawdź, czy Twój problem występuje w [tym artykule](vmware-azure-troubleshoot-failback-reprotect.md).



## <a name="next-steps"></a>Następne kroki

W przypadku replikacji na miejscu należy [przeprowadzić przechodzenie do odzyskiwania po awarii](tutorial-dr-drill-azure.md) , aby upewnić się, że tryb failover działa zgodnie z oczekiwaniami. 
