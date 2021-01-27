---
title: Często zadawane pytania dotyczące odzyskiwania po awarii funkcji Hyper-V z Azure Site Recovery
description: W tym artykule podsumowano typowe pytania dotyczące konfigurowania odzyskiwania po awarii dla lokalnych maszyn wirtualnych funkcji Hyper-V na platformie Azure przy użyciu witryny Azure Site Recovery.
ms.date: 11/12/2019
ms.topic: conceptual
ms.openlocfilehash: 649bd69f14cdf8d81fe05d3a5f5cac3389419fc3
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98879448"
---
# <a name="common-questions---hyper-v-to-azure-disaster-recovery"></a>Często zadawane pytania — odzyskiwanie po awarii z funkcji Hyper-V do platformy Azure

Ten artykuł zawiera odpowiedzi na często zadawane pytania, które są wyświetlane podczas replikowania lokalnych maszyn wirtualnych funkcji Hyper-V na platformę Azure. 

## <a name="general"></a>Ogólne

### <a name="how-is-site-recovery-priced"></a>W jaki sposób Site Recovery cena?
Przejrzyj szczegóły [cennika Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/) .

### <a name="how-do-i-pay-for-azure-vms"></a>Jak mogę płacisz za maszyny wirtualne platformy Azure?
Podczas replikacji dane są replikowane do usługi Azure Storage i nie są dokonywane żadne zmiany w maszynie wirtualnej. Po uruchomieniu trybu failover na platformie Azure Site Recovery automatycznie tworzy maszyny wirtualne IaaS platformy Azure. Po naliczeniu opłaty za zasoby obliczeniowe zużywane na platformie Azure.

### <a name="is-there-any-difference-in-cost-when-replicating-to-general-purpose-v2-storage-account"></a>Czy w przypadku replikowania do konta magazynu Ogólnego przeznaczenia v2 występuje różnica w kosztach?

Zwykle zostanie wyświetlony wzrost kosztu transakcji ponoszonych na konta magazynu GPv2, ponieważ Azure Site Recovery to transakcje bardzo duże. [Dowiedz się więcej](../storage/common/storage-account-upgrade.md#pricing-and-billing) , aby oszacować zmianę.

## <a name="azure"></a>Azure

### <a name="what-do-i-need-in-hyper-v-to-orchestrate-replication-with-site-recovery"></a>Co jest potrzebne w funkcji Hyper-V do organizowania replikacji z Site Recovery?

Wymagania serwera hosta funkcji Hyper-V zależą od scenariusza wdrożenia. Zapoznaj się z wymaganiami wstępnymi funkcji Hyper-V:

* [Replicating Hyper-V VMs (without VMM) to Azure (Replikacja maszyn wirtualnych funkcji Hyper-V [bez programu VMM] do platformy Azure)](./hyper-v-azure-tutorial.md)
* [Replicating Hyper-V VMs (with VMM) to Azure (Replikacja maszyn wirtualnych funkcji Hyper-V [z programem VMM] do platformy Azure)](./hyper-v-vmm-disaster-recovery.md)
* [Replicating Hyper-V VMs to a secondary datacenter (Replikacja maszyn wirtualnych funkcji Hyper-V do dodatkowego centrum danych)](./hyper-v-vmm-disaster-recovery.md)
* W przypadku replikowania do dodatkowego centrum danych Przeczytaj informacje o [obsługiwanych systemach operacyjnych gościa dla maszyn wirtualnych funkcji Hyper-V](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/mt126277(v=ws.11)).
* Jeśli przeprowadzasz replikację do platformy Azure, Site Recovery obsługuje wszystkie systemy operacyjne gościa [obsługiwane przez platformę Azure](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc794868(v=ws.10)).

### <a name="can-i-protect-vms-when-hyper-v-is-running-on-a-client-operating-system"></a>Czy mogę chronić maszyny wirtualne, gdy funkcja Hyper-V jest uruchomiona w systemie operacyjnym klienta?
Nie. Maszyny wirtualne muszą znajdować się na serwerze hosta funkcji Hyper-V, który jest uruchomiony na serwerze z obsługiwanym systemem Windows. Jeśli potrzebujesz ochrony komputera klienckiego, możesz go replikować jako maszynę fizyczną do [platformy Azure](./vmware-azure-tutorial.md) lub [dodatkowego centrum](./vmware-physical-secondary-disaster-recovery.md)danych.

### <a name="do-hyper-v-hosts-need-to-be-in-vmm-clouds"></a>Czy hosty funkcji Hyper-V muszą znajdować się w chmurach programu VMM?
Jeśli chcesz przeprowadzić replikację do dodatkowego centrum danych, maszyny wirtualne funkcji Hyper-V muszą znajdować się na serwerach hostów funkcji Hyper-V znajdujących się w chmurze programu VMM. Jeśli chcesz przeprowadzić replikację do platformy Azure, możesz replikować maszyny wirtualne z chmurami programu VMM lub bez nich. [Przeczytaj więcej](./hyper-v-azure-tutorial.md) na temat replikacji funkcji Hyper-V na platformie Azure.


### <a name="can-i-replicate-hyper-v-generation-2-virtual-machines-to-azure"></a>Czy można replikować maszyny wirtualne funkcji Hyper-V drugiej generacji do platformy Azure?
Tak. Site Recovery konwertuje z generacji 2 do generacji 1 podczas pracy w trybie failover. Po awarii maszyna zostanie przekonwertowana z powrotem do generacji 2. [Przeczytaj więcej](https://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/).


### <a name="can-i-deploy-site-recovery-with-vmm-if-i-only-have-one-vmm-server"></a>Czy mogę wdrożyć usługę Site Recovery z programem VMM, jeśli mam tylko jeden serwer VMM?

Tak. Można replikować maszyny wirtualne na serwerach funkcji Hyper-V w chmurze programu VMM do platformy Azure lub replikować między chmurami programu VMM na tym samym serwerze. W przypadku replikacji lokalnej do lokalnej, zalecamy korzystanie z serwera programu VMM zarówno w lokacjach głównych, jak i dodatkowych. 

### <a name="what-do-i-need-in-azure"></a>Co jest potrzebne na platformie Azure?
Potrzebna jest subskrypcja platformy Azure, magazyn Recovery Services, konto magazynu i Sieć wirtualna. Magazyn, konto magazynu i sieć muszą znajdować się w tym samym regionie.

### <a name="what-azure-storage-account-do-i-need"></a>Jakie konto usługi Azure Storage jest potrzebne?
Potrzebujesz konta magazynu LRS lub GRS. Zalecamy użycie konta GRS, dzięki czemu dane będą odporne w przypadku regionalnej awarii lub sytuacji, w której nie można odzyskać regionu podstawowego. Usługa Premium Storage jest obsługiwana.

### <a name="does-my-azure-account-need-permissions-to-create-vms"></a>Czy moje konto platformy Azure wymaga uprawnień do tworzenia maszyn wirtualnych?
Jeśli jesteś administratorem subskrypcji, masz wymagane uprawnienia replikacji. Jeśli nie jesteś, musisz mieć uprawnienia do tworzenia maszyny wirtualnej platformy Azure w grupie zasobów i sieci wirtualnej określonej podczas konfigurowania Site Recovery i uprawnień do zapisu na wybranym koncie magazynu. [Dowiedz się więcej](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines).

### <a name="is-replication-data-sent-to-site-recovery"></a>Czy dane replikacji są wysyłane do Site Recovery?
Nie, Site Recovery nie przechwytuje replikowanych danych i nie zawiera żadnych informacji o działaniach na maszynach wirtualnych. Dane replikacji są wymieniane między hostami funkcji Hyper-V a usługą Azure Storage. Usługa Site Recovery nie ma możliwości przechwycenia tych danych. Do usługi Site Recovery są wysyłane jedynie metadane wymagane do organizowania replikacji i trybu failover.  

Site Recovery to ISO 27001:2013, 27018, HIPAA, DPA certyfikowany i jest w trakcie oceny SOC2 i FedRAMP JAB.

### <a name="can-we-keep-on-premises-metadata-within-a-geographic-region"></a>Czy można zachować lokalne metadane w regionie geograficznym?
Tak. Po utworzeniu magazynu w regionie firma Microsoft gwarantuje, że wszystkie metadane używane przez Site Recovery pozostają w granicach geograficznych tego regionu.

### <a name="does-site-recovery-encrypt-replication"></a>Czy usługa Site Recovery szyfruje replikację?
Tak, obsługiwane są zarówno szyfrowanie podczas przesyłania, jak i [szyfrowanie na platformie Azure](../storage/common/storage-service-encryption.md) .


## <a name="deployment"></a>Wdrożenie

### <a name="what-can-i-do-with-hyper-v-to-azure-replication"></a>Co mogę zrobić z replikacją funkcji Hyper-V do platformy Azure?

- **Odzyskiwanie po awarii**: można skonfigurować pełne odzyskiwanie po awarii. W tym scenariuszu lokalne maszyny wirtualne funkcji Hyper-V są replikowane do usługi Azure Storage:
    - Maszyny wirtualne można replikować na platformę Azure. Jeśli infrastruktura lokalna jest niedostępna, Pracujesz w trybie failover na platformie Azure.
    - Po przełączeniu w tryb failover maszyny wirtualne platformy Azure są tworzone przy użyciu replikowanych danych. Możesz uzyskiwać dostęp do aplikacji i obciążeń na maszynach wirtualnych platformy Azure.
    - Po ponownym udostępnieniu lokalnego centrum danych można wrócić po awarii z platformy Azure do lokacji lokalnej.
- **Migracja**: można użyć Site Recovery do migrowania lokalnych maszyn wirtualnych funkcji Hyper-V do usługi Azure Storage. Następnie Pracujesz w trybie failover z poziomu lokalnego na platformie Azure. Po przejściu w tryb failover aplikacje i obciążenia są dostępne i uruchamiane na maszynach wirtualnych platformy Azure.


### <a name="what-do-i-need-on-premises"></a>Co jest potrzebne w środowisku lokalnym?

Potrzebna jest co najmniej jedna maszyna wirtualna działająca na jednym lub wielu autonomicznych lub klastrowanych hostach funkcji Hyper-V. Możesz również replikować maszyny wirtualne działające na hostach zarządzanych przez System Center Virtual Machine Manager (VMM).
- Jeśli program VMM nie jest uruchomiony podczas wdrażania Site Recovery, hosty i klastry funkcji Hyper-V są zbierane w lokacjach funkcji Hyper-V. Na każdym hoście funkcji Hyper-V są instalowane agenci Site Recovery (dostawca Azure Site Recovery i Agent Recovery Services).
- Jeśli hosty funkcji Hyper-V znajdują się w chmurze programu VMM, można organizować replikację w programie VMM. Należy zainstalować dostawcę Site Recovery na serwerze programu VMM oraz agenta Recovery Services na każdym hoście funkcji Hyper-V. Można mapować między sieciami logicznymi i MASZYNami wirtualnymi programu VMM oraz usługą Azure sieci wirtualnych.
- [Dowiedz się więcej](hyper-v-azure-architecture.md) o architekturze platformy Azure dla funkcji Hyper-V.

### <a name="can-i-replicate-vms-located-on-a-hyper-v-cluster"></a>Czy można replikować maszyny wirtualne znajdujące się w klastrze funkcji Hyper-V?

Tak, Site Recovery obsługuje klastrowane hosty funkcji Hyper-V. Należy pamiętać, że:

- Wszystkie węzły klastra powinny być zarejestrowane w tym samym magazynie.
- Jeśli nie korzystasz z programu VMM, wszystkie hosty funkcji Hyper-V w klastrze powinny zostać dodane do tej samej lokacji funkcji Hyper-V.
- Należy zainstalować dostawcę Azure Site Recovery i agenta Recovery Services na każdym hoście funkcji Hyper-V w klastrze, a następnie dodać każdego hosta do lokacji funkcji Hyper-V.
- W klastrze nie trzeba wykonywać określonych kroków.
- Jeśli uruchomisz narzędzie Planista wdrażania dla funkcji Hyper-V, narzędzie zbiera dane profilu z węzła, który jest uruchomiony, a maszyna wirtualna jest uruchomiona. Narzędzie nie może zbierać danych z węzła, który jest wyłączony, ale będzie śledzić ten węzeł. Gdy węzeł zostanie uruchomiony, narzędzie zacznie zbierać z niego dane profilu maszyny wirtualnej (jeśli maszyna wirtualna jest częścią listy maszyn wirtualnych profilu i jest uruchomiona w węźle).
- Jeśli maszyna wirtualna na hoście funkcji Hyper-V w magazynie Site Recovery jest migrowana do innego hosta funkcji Hyper-V w tym samym klastrze lub do hosta autonomicznego, nie ma to wpływu na replikację maszyny wirtualnej. Host funkcji Hyper-V musi spełniać [wymagania wstępne](hyper-v-azure-support-matrix.md#on-premises-servers)i być skonfigurowany w magazynie Site Recovery. 


### <a name="can-i-protect-vms-when-hyper-v-is-running-on-a-client-operating-system"></a>Czy mogę chronić maszyny wirtualne, gdy funkcja Hyper-V jest uruchomiona w systemie operacyjnym klienta?
Nie. Maszyny wirtualne muszą znajdować się na serwerze hosta funkcji Hyper-V, który jest uruchomiony na serwerze z obsługiwanym systemem Windows. Jeśli potrzebujesz ochrony komputera klienckiego, możesz [go replikować jako maszynę fizyczną](physical-azure-disaster-recovery.md) do platformy Azure.

### <a name="can-i-replicate-hyper-v-generation-2-virtual-machines-to-azure"></a>Czy można replikować maszyny wirtualne funkcji Hyper-V drugiej generacji do platformy Azure?
Tak. Site Recovery konwertuje z generacji 2 do generacji 1 podczas pracy w trybie failover. Po awarii maszyna zostanie przekonwertowana z powrotem do generacji 2.

### <a name="can-i-automate-site-recovery-scenarios-with-an-sdk"></a>Czy można zautomatyzować scenariusze Site Recovery przy użyciu zestawu SDK?
Tak. Przepływy pracy usługi Site Recovery można zautomatyzować przy użyciu interfejsu API REST, programu PowerShell lub zestawu SDK platformy Azure. Obecnie obsługiwane scenariusze replikacji funkcji Hyper-V do platformy Azure przy użyciu programu PowerShell:

- [Replikacja funkcji Hyper-V bez programu VMM przy użyciu programu PowerShell](hyper-v-azure-powershell-resource-manager.md)
- [Replikowanie funkcji Hyper-V za pomocą programu VMM przy użyciu programu PowerShell](hyper-v-vmm-powershell-resource-manager.md)

## <a name="replication"></a>Replikacja

### <a name="where-do-on-premises-vms-replicate-to"></a>Gdzie są replikowane lokalne maszyny wirtualne?
Dane są replikowane do usługi Azure Storage. Po uruchomieniu trybu failover Site Recovery automatycznie tworzy maszyny wirtualne platformy Azure z konta magazynu.

### <a name="what-apps-can-i-replicate"></a>Jakie aplikacje mogę replikować?
Można replikować dowolne aplikacje lub obciążenia z uruchomioną maszyną wirtualną funkcji Hyper-V, która jest zgodna z [wymaganiami dotyczącymi replikacji](hyper-v-azure-support-matrix.md#replicated-vms). Site Recovery zapewnia obsługę replikacji obsługującej aplikacje, dzięki czemu aplikacje mogą przełączać się w tryb failover i powrócić do stanu inteligentnego. Site Recovery integruje się z aplikacjami firmy Microsoft, takimi jak SharePoint, Exchange, Dynamics, SQL Server i Active Directory, i ściśle współpracuje z wiodącymi dostawcami, takimi jak Oracle, SAP, IBM i Red Hat. [Dowiedz się więcej](site-recovery-workload.md) o ochronie obciążeń.

### <a name="whats-the-replication-process"></a>Co to jest proces replikacji?

1. Gdy replikacja początkowa jest wyzwalana, wykonywana jest migawka maszyny wirtualnej funkcji Hyper-V.
2. Wirtualne dyski twarde w maszynie wirtualnej są replikowane pojedynczo, dopóki nie zostaną skopiowane na platformę Azure. Może to chwilę potrwać, w zależności od rozmiaru maszyny wirtualnej i przepustowości sieci. Dowiedz się, jak zwiększyć przepustowość sieci.
3. Jeśli podczas początkowej replikacji wystąpią zmiany dysku, śledzenie replikacji funkcji Hyper-V Replica śledzi zmiany w postaci dzienników replikacji funkcji Hyper-V (. HRL). Te pliki dzienników znajdują się w tym samym folderze co dyski. Każdy dysk ma skojarzony plik. HRL, który jest wysyłany do magazynu pomocniczego. Pliki migawki i dziennika zużywają zasoby dysku w trakcie replikacji początkowej.
4. Po zakończeniu replikacji początkowej migawka maszyny wirtualnej jest usuwana.
5. Wszystkie zmiany dysków w dzienniku zostaną zsynchronizowane i scalone z dyskiem nadrzędnym.
6. Po zakończeniu replikacji początkowej zostanie uruchomione zadanie finalizowanie ochrony na maszynie wirtualnej. Konfiguruje sieć i inne ustawienia po replikacji, aby maszyna wirtualna była chroniona.
7. Na tym etapie można sprawdzić ustawienia maszyny wirtualnej, aby upewnić się, że jest ono gotowe do pracy w trybie failover. Aby sprawdzić, czy maszyna wirtualna jest w trybie failover zgodnie z oczekiwaniami, można uruchomić polecenie drążenia odzyskiwania po awarii.
8. Po replikacji początkowej rozpoczyna się replikacja różnicowa, zgodnie z zasadami replikacji.
9. Zmiany są rejestrowane. HRL. Z każdym dyskiem skonfigurowanym pod kątem replikacji jest skojarzony plik hrl.
10. Dziennik jest wysyłany do konta magazynu klienta. Gdy dziennik jest przesyłany do platformy Azure, zmiany na dysku podstawowym są śledzone w innym pliku dziennika, w tym samym folderze.
11. Podczas replikacji początkowej i różnicowej można monitorować maszynę wirtualną w Azure Portal.

[Dowiedz się więcej](hyper-v-azure-architecture.md#replication-process) o procesie replikacji.

### <a name="can-i-replicate-to-azure-with-a-site-to-site-vpn"></a>Czy można replikować na platformę Azure przy użyciu sieci VPN typu lokacja-lokacja?

Site Recovery replikuje dane ze źródła lokalnego do usługi Azure Storage za pośrednictwem publicznego punktu końcowego lub korzystając z ExpressRoute komunikacji równorzędnej firmy Microsoft. Replikacja za pośrednictwem sieci VPN typu lokacja-lokacja nie jest obsługiwana.

### <a name="can-i-replicate-to-azure-with-expressroute"></a>Czy można replikować na platformę Azure za pomocą ExpressRoute?

Tak, ExpressRoute może służyć do replikowania maszyn wirtualnych na platformę Azure. Site Recovery replikuje dane do konta usługi Azure Storage za pośrednictwem publicznego punktu końcowego i należy skonfigurować [komunikację równorzędną firmy Microsoft](../expressroute/expressroute-circuit-peerings.md#microsoftpeering) na potrzeby replikacji Site Recovery. Gdy maszyny wirtualne zostaną przełączone w tryb failover do sieci wirtualnej platformy Azure, możesz uzyskać do nich dostęp przy użyciu [prywatnej komunikacji równorzędnej](../expressroute/expressroute-circuit-peerings.md#privatepeering).


### <a name="why-cant-i-replicate-over-vpn"></a>Dlaczego nie można replikować za pośrednictwem sieci VPN?

W przypadku replikacji na platformę Azure ruch związany z replikacją dociera do publicznych punktów końcowych konta usługi Azure Storage. W ten sposób można replikować tylko za pośrednictwem publicznego Internetu za pomocą ExpressRoute (Komunikacja równorzędna firmy Microsoft), a sieć VPN nie działa. 

### <a name="what-are-the-replicated-vm-requirements"></a>Jakie są wymagania dotyczące replikowanych maszyn wirtualnych?

W przypadku replikacji na maszynie wirtualnej funkcji Hyper-V musi być uruchomiony obsługiwany system operacyjny. Ponadto maszyna wirtualna musi spełniać wymagania dotyczące maszyn wirtualnych platformy Azure. [Dowiedz się więcej](hyper-v-azure-support-matrix.md#replicated-vms) w macierzy obsługi.

### <a name="why-is-an-additional-standard-storage-account-required-if-i-replicate-my-virtual-machine-disks-to-premium-storage"></a>Dlaczego jest wymagane dodatkowe konto magazynu w przypadku replikowania dysków maszyny wirtualnej do magazynu w warstwie Premium?

W przypadku replikowania lokalnych maszyn wirtualnych i serwerów fizycznych do magazynu w warstwie Premium wszystkie dane znajdujące się na dyskach chronionej maszyny są replikowane do konta magazynu w warstwie Premium. Do przechowywania dzienników replikacji jest wymagane dodatkowe konto magazynu w warstwie Standardowa. Po zakończeniu początkowej fazy replikacji danych dyskowych wszystkie zmiany danych z dysku lokalnego są śledzone w sposób ciągły i przechowywane jako dzienniki replikacji na tym dodatkowym koncie magazynu.

### <a name="how-often-can-i-replicate-to-azure"></a>Jak często mogę przeprowadzić replikację do platformy Azure?

Maszyny wirtualne funkcji Hyper-V mogą być replikowane co 30 sekund (z wyjątkiem magazynu Premium Storage) lub 5 minut.

### <a name="can-azure-site-recovery-and-hyper-v-replica-be-configured-together-on-a-hyper-v-machine"></a>Czy Azure Site Recovery i funkcja Hyper-V Replica można skonfigurować razem na komputerze funkcji Hyper-V?

Tak, zarówno Azure Site Recovery, jak i funkcja Hyper-V Replica można skonfigurować razem dla komputera. Jednak maszyna będzie musiała być chroniona jako maszyna fizyczna i będzie replikowana na platformę Azure przy użyciu serwera konfiguracji/procesu. Więcej informacji o ochronie maszyn fizycznych [znajdziesz tutaj](./physical-azure-architecture.md).

### <a name="can-i-extend-replication"></a>Czy mogę zwiększyć replikację?
Replikacja rozszerzona lub łańcuchowa nie jest obsługiwana. Zażądaj tej funkcji na [forum opinii](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959).

### <a name="can-i-do-an-offline-initial-replication"></a>Czy można wykonać replikację początkową w trybie offline?
Ta funkcja nie jest obsługiwana. Zażądaj tej funkcji na [forum opinii](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from).

### <a name="can-i-exclude-disks"></a>Czy można wykluczać dyski?
Tak, można wykluczać dyski z replikacji. 

### <a name="can-i-replicate-vms-with-dynamic-disks"></a>Czy można replikować maszyny wirtualne z dyskami dynamicznymi?
Dyski dynamiczne mogą być replikowane. Dysk systemu operacyjnego musi być dyskiem podstawowym.



## <a name="security"></a>Zabezpieczenia

### <a name="what-access-does-site-recovery-need-to-hyper-v-hosts"></a>Jakiego dostępu Site Recovery potrzeba na hostach Hyper-V

Site Recovery potrzebuje dostępu do hostów funkcji Hyper-V w celu replikowania wybranych maszyn wirtualnych. Site Recovery instaluje następujące elementy na hostach funkcji Hyper-V:

- Jeśli program VMM nie jest uruchomiony, dostawca Azure Site Recovery i Agent Recovery Services są instalowane na każdym hoście.
- W przypadku korzystania z programu VMM Agent Recovery Services jest instalowany na każdym hoście. Dostawca jest uruchamiany na serwerze programu VMM.


### <a name="what-does-site-recovery-install-on-hyper-v-vms"></a>Co Site Recovery instalować na maszynach wirtualnych funkcji Hyper-V?

Site Recovery nie instaluje jawnie żadnych elementów na maszynach wirtualnych funkcji Hyper-V obsługujących replikację.




## <a name="failover-and-failback"></a>Przechodzenie w tryb failover i powrót po awarii


### <a name="how-do-i-fail-over-to-azure"></a>Jak mogę przełączenia w tryb failover na platformę Azure?

Planowane lub nieplanowane przejście w tryb failover można uruchomić z maszyn wirtualnych funkcji Hyper-V do platformy Azure.

- Jeśli zostanie uruchomione planowane przejście w tryb failover, źródłowe maszyny wirtualne zostaną wyłączone w celu zapewnienia, że nie będzie miała miejsca utrata danych.
- Jeśli lokacja główna jest niedostępna, można uruchomić nieplanowaną pracę w trybie failover.
- W tryb failover można przełączyć pojedynczą maszynę lub można utworzyć plany odzyskiwania, aby zaaranżować tryb failover na wielu maszynach.
- Tryb failover znajduje się w dwóch częściach:
    - Po zakończeniu pierwszego etapu pracy w trybie failover powinno być możliwe wyświetlenie utworzonych maszyn wirtualnych repliki na platformie Azure. Jeśli jest to wymagane, do maszyny wirtualnej można przypisać publiczny adres IP.
    - Następnie możesz zatwierdzić tryb failover, aby rozpocząć uzyskiwanie dostępu do obciążenia z repliki maszyny wirtualnej platformy Azure.
   

### <a name="how-do-i-access-azure-vms-after-failover"></a>Jak mogę dostęp do maszyn wirtualnych platformy Azure po przejściu w tryb failover?
Po przejściu w tryb failover możesz uzyskiwać dostęp do maszyn wirtualnych platformy Azure za pośrednictwem bezpiecznego połączenia internetowego, za pośrednictwem sieci VPN typu lokacja-lokacja lub za pośrednictwem usługi Azure ExpressRoute. Aby nawiązać połączenie, należy przygotować wiele rzeczy. [Dowiedz się więcej](failover-failback-overview.md#connect-to-azure-after-failover).

### <a name="is-failed-over-data-resilient"></a>Czy dane są odporne na awarie?
Platforma Azure została zaprojektowana z myślą o odporności danych. Site Recovery jest zaprojektowana w celu przełączenia w tryb failover do pomocniczego centrum danych platformy Azure zgodnie z umową SLA platformy Azure. W przypadku przejścia w tryb failover upewnijmy się, że Twoje metadane i magazyny pozostają w tym samym regionie geograficznym, który został wybrany dla Twojego magazynu.

### <a name="is-failover-automatic"></a>Czy tryb failover jest automatyczny?
[Tryb failover](site-recovery-failover.md) nie jest automatyczny. Możesz inicjować tryb failover za pomocą pojedynczego kliknięcia w portalu lub można użyć [programu PowerShell](/powershell/module/az.recoveryservices) do wyzwolenia trybu failover.

### <a name="how-do-i-fail-back"></a>Jak mogę powrót po awarii?

Po ponownym uruchomieniu infrastruktury lokalnej można wrócić do trybu powrotu po awarii. Powrót po awarii występuje w trzech etapach:

1. Planowane przejście w tryb failover z platformy Azure do lokacji lokalnej przy użyciu kilku różnych opcji:

    - Minimalizuj czas przestoju: Jeśli używasz tej opcji, Site Recovery synchronizuje dane przed przełączeniem w tryb failover. Sprawdza on zmiany bloków danych i pobiera je do lokacji lokalnej, podczas gdy maszyna wirtualna platformy Azure działa, co minimalizuje przestoje. Gdy ręcznie określisz, że praca w trybie failover powinna zakończyć działanie, maszyna wirtualna platformy Azure zostanie wyłączona, zostaną skopiowane wszystkie końcowe zmiany różnicowe i rozpocznie się przełączanie do trybu failover.
    - Pełne pobieranie: w przypadku tej opcji dane są synchronizowane podczas pracy w trybie failover. Ta opcja powoduje pobranie całego dysku. Jest to szybsze, ponieważ nie są obliczane sumy kontrolne, ale występuje więcej przestojów. Użyj tej opcji, jeśli korzystasz z maszyn wirtualnych platformy Azure z repliką przez jakiś czas lub jeśli lokalna maszyna wirtualna została usunięta.

2. Możesz wybrać opcję powrotu po awarii do tej samej maszyny wirtualnej lub do alternatywnej maszyny wirtualnej. Możesz określić, że Site Recovery powinna utworzyć maszynę wirtualną, jeśli jeszcze nie istnieje.
3. Po zakończeniu synchronizacji początkowej należy wybrać opcję ukończenia pracy w trybie failover. Po zakończeniu możesz zalogować się do lokalnej maszyny wirtualnej, aby sprawdzić, czy wszystko działa zgodnie z oczekiwaniami. W Azure Portal można zobaczyć, że maszyny wirtualne platformy Azure zostały zatrzymane.
4. Możesz zatwierdzić przejście w tryb failover, aby zakończyć dostęp do obciążenia z lokalnej maszyny wirtualnej.
5. Po niepomyślnym zakończeniu obciążeń należy włączyć replikację odwrotną, aby lokalne maszyny wirtualne zostały ponownie zreplikowane na platformę Azure.

### <a name="can-i-fail-back-to-a-different-location"></a>Czy mogę powrócić po awarii do innej lokalizacji?
Tak, po przełączeniu w tryb failover na platformę Azure możesz wrócić do innej lokalizacji, jeśli jest ona niedostępna. [Dowiedz się więcej](hyper-v-azure-failback.md#fail-back-to-an-alternate-location).