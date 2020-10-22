---
title: Konfigurowanie odzyskiwania po awarii oprogramowania SAP NetWeaver z użyciem Azure Site Recovery
description: Dowiedz się, jak skonfigurować odzyskiwanie po awarii dla oprogramowania SAP NetWeaver z Azure Site Recovery.
author: sideeksh
manager: rochakm
ms.topic: how-to
ms.date: 11/27/2018
ms.openlocfilehash: 69f0a20bdcba23d947e3d1b573c1a359da245161
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/22/2020
ms.locfileid: "92369424"
---
# <a name="set-up-disaster-recovery-for-a-multi-tier-sap-netweaver-app-deployment"></a>Konfigurowanie odzyskiwania po awarii dla wielowarstwowego wdrożenia aplikacji SAP NetWeaver

Najnowsze wdrożenia SAP o dużych rozmiarach i średnim rozmiarze używają pewnego rodzaju rozwiązania do odzyskiwania po awarii. Znaczenie niezawodnych i weryfikowalneych rozwiązań odzyskiwania po awarii zostało zwiększone, ponieważ bardziej podstawowe procesy biznesowe są przenoszone do aplikacji takich jak SAP. Azure Site Recovery został przetestowany i zintegrowany z aplikacjami SAP. Site Recovery przekracza możliwości większości lokalnych rozwiązań odzyskiwania po awarii oraz niższy całkowity koszt posiadania niż konkurencyjne rozwiązania.

Za pomocą Site Recovery można:
* Włącz ochronę oprogramowania SAP NetWeaver i innych niż NetWeaver aplikacji produkcyjnych, które są uruchamiane lokalnie przez replikowanie składników do platformy Azure.
* Włącz ochronę oprogramowania SAP NetWeaver i innych niż NetWeaver aplikacji produkcyjnych uruchamianych na platformie Azure przez replikowanie składników do innego centrum danych platformy Azure.
* Skorzystaj z uproszczenia migracji do chmury, używając usługi Site Recovery do migrowania wdrożenia SAP do platformy Azure.
* Upraszczanie uaktualnień projektów SAP, testowanie i Tworzenie prototypów przez utworzenie klonu produkcyjnego na żądanie na potrzeby testowania aplikacji SAP.

Wdrożenia aplikacji SAP NetWeaver można chronić za pomocą [Azure Site Recovery](site-recovery-overview.md). W tym artykule opisano najlepsze rozwiązania dotyczące ochrony wielowarstwowego wdrożenia oprogramowania SAP NetWeaver na platformie Azure podczas replikowania do innego centrum danych platformy Azure przy użyciu Site Recovery. W tym artykule opisano obsługiwane scenariusze i konfiguracje oraz sposób testowania pracy w trybie failover (przechodzenia do odzyskiwania po awarii) i rzeczywistej pracy awaryjnej.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że wiesz, jak wykonać następujące zadania:

* [Replikowanie maszyny wirtualnej na platformę Azure](./azure-to-azure-tutorial-enable-replication.md)
* [Projektowanie sieci odzyskiwania](./azure-to-azure-about-networking.md)
* [Wykonaj test pracy w trybie failover na platformie Azure](./azure-to-azure-tutorial-dr-drill.md)
* [Przełączenie w tryb failover na platformie Azure](site-recovery-failover.md)
* [Replikowanie kontrolera domeny](site-recovery-active-directory.md)
* [Replikowanie wystąpienia SQL Server](site-recovery-sql.md)

## <a name="supported-scenarios"></a>Obsługiwane scenariusze

Za pomocą Site Recovery można zaimplementować rozwiązanie odzyskiwania po awarii w następujących scenariuszach:
* Istnieją systemy SAP działające w jednym centrum danych platformy Azure i są one replikowane do innego centrum danych platformy Azure (odzyskiwania po awarii z platformy Azure do platformy Azure). 
   Aby uzyskać więcej informacji, zobacz [Architektura replikacji z platformy Azure do platformy Azure](./azure-to-azure-architecture.md).
* Istnieją systemy SAP działające na serwerach VMware (lub fizycznych) w środowisku lokalnym. Systemy SAP są również replikowane do lokacji odzyskiwania po awarii w centrum danych platformy Azure (odzyskiwanie po awarii oprogramowania VMware na platformę Azure). 
   W tym scenariuszu wymagane są pewne dodatkowe składniki. Aby uzyskać więcej informacji, zobacz [Architektura replikacji VMware-to-Azure](./vmware-azure-architecture.md).
* Masz systemy SAP działające w środowisku funkcji Hyper-V lokalnie. Systemy SAP są również replikowane do lokacji odzyskiwania po awarii w centrum danych platformy Azure (odzyskiwanie awaryjne funkcji Hyper-V do platformy Azure).
   W tym scenariuszu wymagane są pewne dodatkowe składniki. Aby uzyskać więcej informacji, zobacz [Architektura replikacji funkcji Hyper-V do platformy Azure](./hyper-v-azure-architecture.md).

W tym artykule opisano scenariusz odzyskiwania po awarii **z platformy Azure do platformy Azure** . W tym scenariuszu przedstawiono możliwości odzyskiwania po awarii oprogramowania SAP Site Recovery. Ponieważ replikacja Site Recovery nie jest specyficzna dla aplikacji, proces, który został opisany, ma zastosowanie również do innych scenariuszy.

### <a name="required-foundation-services"></a>Wymagane usługi Foundation
W scenariuszu omawianym w tym artykule wdrożono następujące usługi programu Foundation:
* Azure ExpressRoute lub Azure VPN Gateway
* Co najmniej jeden Azure Active Directory kontroler domeny i serwer DNS działający na platformie Azure

Zalecamy ustanowienie tej infrastruktury przed wdrożeniem Site Recovery.

## <a name="reference-sap-application-deployment"></a>Odwołanie do wdrożenia aplikacji SAP

Ta architektura referencyjna korzysta z oprogramowania SAP NetWeaver w środowisku systemu Windows na platformie Azure o wysokiej dostępności. Ta architektura jest wdrażana z określonymi rozmiarami maszyn wirtualnych, które można zmienić w celu uwzględnienia potrzeb organizacji.

![Diagram typowego wzorca wdrażania SAP](./media/site-recovery-sap/sap-netweaver_latest.png)

## <a name="disaster-recovery-considerations"></a>Zagadnienia dotyczące odzyskiwania po awarii

W przypadku odzyskiwania po awarii należy mieć możliwość przełączenia w tryb failover do regionu pomocniczego. Każda warstwa używa innej strategii w celu zapewnienia ochrony przed odzyskiwaniem po awarii.

#### <a name="vms-running-sap-web-dispatcher-pools"></a>Maszyny wirtualne korzystające z pul dyspozytorów sieci Web SAP

Składnik dyspozytora sieci Web działa jako moduł równoważenia obciążenia dla ruchu SAP między serwerami aplikacji SAP. Aby uzyskać wysoką dostępność składnika dyspozytora sieci Web, Azure Load Balancer implementuje konfigurację równoległego dyspozytora sieci Web. Dyspozytor sieci Web używa konfiguracji okrężnej dystrybucji ruchu HTTP (S) między dostępnymi składnikami w puli modułów równoważenia obciążenia.

#### <a name="vms-running-application-servers-pools"></a>Maszyny wirtualne z uruchomioną pulami serwerów aplikacji
Transakcja SMLG zarządza grupami logowania dla serwerów aplikacji ABAP. Używa funkcji równoważenia obciążenia na serwerze komunikatów usług centralnych do dystrybucji obciążenia między pulami serwerów aplikacji SAP dla ruchu SAPGUIs i RFC. Można replikować zarządzanie przy użyciu Site Recovery.

#### <a name="vms-running-sap-central-services-clusters"></a>Maszyny wirtualne z klastrami usług SAP Central Services
Ta architektura referencyjna uruchamia usługi centralne na maszynach wirtualnych w warstwie aplikacji. Usługi centralne to potencjalna single point of failure w przypadku jednej maszyny wirtualnej. Typowym wdrożeniem i wysoką dostępnością nie są wymagania.

Aby zaimplementować rozwiązanie wysokiej dostępności, można użyć udostępnionego klastra dysku lub klastra udziałów plików. Aby skonfigurować maszyny wirtualne dla udostępnionego klastra dysków, należy użyć klastra trybu failover systemu Windows Server. Zalecamy używanie monitora chmury jako monitora kworum.

 > [!NOTE]
 > Ponieważ Site Recovery nie replikuje monitora chmury, zalecamy wdrożenie monitora chmury w regionie odzyskiwania po awarii.

Aby można było obsługiwać środowisko klastra trybu failover, [oprogramowanie SIOS DataKeeper Cluster](https://azuremarketplace.microsoft.com/marketplace/apps/sios_datakeeper.sios-datakeeper-8) jest funkcją udostępnionego woluminu klastra. W funkcji oprogramowanie SIOS klaster DataKeeper replikuje niezależne dyski należące do węzłów klastra. Ponieważ platforma Azure nie obsługuje natywnie dysków udostępnionych, wymaga rozwiązań zapewnianych przez oprogramowanie SIOS.

Można również obsługiwać klastrowanie, implementując klaster udziałów plików. [SAP](https://blogs.sap.com/2018/03/19/migration-from-a-shared-disk-cluster-to-a-file-share-cluster) ostatnio zmodyfikowano wzorzec wdrażania usług centralnych, aby uzyskać dostęp do katalogów globalnych/sapmnt za pośrednictwem ścieżki UNC. Nadal zalecamy upewnienie się, że udział UNC/sapmnt ma wysoką dostępność. Możesz sprawdzić wystąpienie usług centralnych. Użyj klastra trybu failover systemu Windows Server z serwerem plików skalowalnym w poziomie (SOFS) i funkcją Bezpośrednie miejsca do magazynowania (S2D) w systemie Windows Server 2016.

 > [!NOTE]
 > Site Recovery obecnie obsługuje tylko replikację punktu w poziomie awarii dla maszyn wirtualnych, które używają funkcji bezpośrednie miejsca do magazynowania i pasywnego węzła oprogramowanie SIOS DataKeeper.


## <a name="more-disaster-recovery-considerations"></a>Dodatkowe zagadnienia dotyczące odzyskiwania po awarii

Za pomocą Site Recovery można organizować tryb failover całego wdrożenia SAP w regionach platformy Azure.
Poniżej przedstawiono procedurę konfigurowania odzyskiwania po awarii:

1. Replikowanie maszyn wirtualnych
1. Projektowanie sieci odzyskiwania
1. Replikowanie kontrolera domeny
1. Replikuj warstwę bazową danych
1. Przeprowadzanie testu trybu failover
1. Przejdź w tryb failover

Poniżej znajduje się zalecenie dotyczące odzyskiwania po awarii dla każdej warstwy używanej w tym przykładzie.

 **Warstwy SAP** | **Zalecenie**
 --- | ---
**Pula dyspozytorów sieci Web SAP** |  Replikacja przy użyciu Site Recovery 
**Pula serwerów aplikacji SAP** |  Replikacja przy użyciu Site Recovery 
**Klaster usług SAP Central** |  Replikacja przy użyciu Site Recovery 
**Maszyny wirtualne usługi Active Directory** |  Korzystanie z replikacji usługi Active Directory 
**Serwery usługi SQL Database** |  Użyj zawsze SQL Server replikacji

## <a name="replicate-virtual-machines"></a>Replikowanie maszyn wirtualnych

Aby rozpocząć replikowanie wszystkich maszyn wirtualnych aplikacji SAP do centrum danych odzyskiwania po awarii platformy Azure, postępuj zgodnie ze wskazówkami zawartymi w artykule [replikowanie maszyny wirtualnej na platformę Azure](./azure-to-azure-tutorial-enable-replication.md).

* Aby uzyskać wskazówki dotyczące ochrony Active Directory i systemu DNS, Dowiedz się, [jak chronić Active Directory i system DNS](site-recovery-active-directory.md).

* Aby uzyskać wskazówki dotyczące ochrony warstwy bazy danych działającej na SQL Server, Dowiedz się, [jak chronić SQL Server](site-recovery-sql.md).

## <a name="networking-configuration"></a>Konfiguracja sieci

Jeśli używasz statycznego adresu IP, możesz określić adres IP, który ma być pobierany przez maszynę wirtualną. Aby ustawić adres IP, przejdź do karty **sieciowe ustawienia obliczeń i sieci**  >  **Network interface card**.

![Zrzut ekranu pokazujący sposób ustawiania prywatnego adresu IP w okienku karty sieciowej Site Recovery](./media/site-recovery-sap/sap-static-ip.png)


## <a name="create-a-recovery-plan"></a>Tworzenie planu odzyskiwania

Plan odzyskiwania obsługuje sekwencjonowanie różnych warstw w aplikacji wielowarstwowej podczas pracy w trybie failover. Sekwencjonowanie pomaga zachować spójność aplikacji. Po utworzeniu planu odzyskiwania dla wielowarstwowej aplikacji sieci Web wykonaj kroki opisane w temacie [Tworzenie planu odzyskiwania przy użyciu Site Recovery](site-recovery-create-recovery-plans.md).

### <a name="add-virtual-machines-to-failover-groups"></a>Dodawanie maszyn wirtualnych do grup trybu failover

1. Utwórz plan odzyskiwania, dodając maszyny wirtualne serwerów aplikacji, dyspozytorów sieci Web i usług SAP Central Services.
1. Wybierz pozycję **Dostosuj** , aby zgrupować maszyny wirtualne. Domyślnie wszystkie maszyny wirtualne są częścią grupy 1.

### <a name="add-scripts-to-the-recovery-plan"></a>Dodawanie skryptów do planu odzyskiwania
Aby aplikacje działały prawidłowo, może być konieczne wykonanie niektórych operacji na maszynach wirtualnych platformy Azure. Wykonaj te operacje po przejściu w tryb failover lub w trakcie testu pracy w trybie failover. Możesz również zautomatyzować niektóre operacje wykonywane po przejściu w tryb failover. Można na przykład zaktualizować wpis DNS i zmienić powiązania i połączenia przez dodanie odpowiednich skryptów do planu odzyskiwania.

Najczęściej używane skrypty Site Recovery można wdrożyć na koncie Azure Automation, wybierając pozycję **Wdróż na platformie Azure**. W przypadku korzystania z dowolnego opublikowanego skryptu postępuj zgodnie ze wskazówkami w skrypcie.

[![Wdrażanie na platformie Azure](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)

1. Dodaj skrypt przed akcją do grupy 1 w celu przełączenia w tryb failover grupy dostępności SQL Server. Użyj skryptu ASR-SQL-FailoverAG opublikowanego w przykładowych skryptach. Postępuj zgodnie ze wskazówkami w skrypcie i wprowadź odpowiednie zmiany w skrypcie.
1. Dodaj skrypt po akcji, aby dołączyć moduł równoważenia obciążenia do maszyn wirtualnych w trybie failover w warstwie sieci Web (Grupa 1). Użyj skryptu ASR-AddSingleLoadBalancer opublikowanego w przykładowych skryptach. Postępuj zgodnie ze wskazówkami w skrypcie i wprowadź wymagane zmiany w skrypcie zgodnie z potrzebami.

![Plan odzyskiwania SAP](./media/site-recovery-sap/sap_recovery_plan.png)


## <a name="run-a-test-failover"></a>Wykonywanie próby przejścia w tryb failover

1. W Azure Portal wybierz swój magazyn Recovery Services.
1. Wybierz plan odzyskiwania, który został utworzony dla aplikacji SAP.
1. Wybierz pozycję **Test pracy w trybie failover**.
1. Aby rozpocząć proces testowego przełączania do trybu failover, wybierz punkt odzyskiwania i sieć wirtualną platformy Azure.
1. Gdy środowisko pomocnicze jest w pełni, wykonaj walidację.
1. Po zakończeniu walidacji Wyczyść środowisko trybu failover, wybierając opcję **Oczyść test pracy w trybie failover**.

Aby uzyskać więcej informacji, zobacz [test pracy w trybie failover na platformie Azure w Site Recovery](site-recovery-test-failover-to-azure.md).

## <a name="run-a-failover"></a>Uruchamianie trybu failover

1. W Azure Portal wybierz swój magazyn Recovery Services.
1. Wybierz plan odzyskiwania, który został utworzony dla aplikacji SAP.
1. Wybierz pozycję **Tryb failover**.
1. Aby rozpocząć proces trybu failover, wybierz punkt odzyskiwania.

Aby uzyskać więcej informacji, zobacz [tryb failover w Site Recovery](site-recovery-failover.md).

## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej o tworzeniu rozwiązania do odzyskiwania po awarii dla wdrożeń oprogramowania SAP NetWeaver przy użyciu Site Recovery. Zobacz oficjalny dokument [SAP NetWeaver: kompilowanie rozwiązania do odzyskiwania po awarii przy użyciu Site Recovery](https://aka.ms/asr_sap). Oficjalny dokument omawia zalecenia dotyczące różnych architektur SAP. Możesz zobaczyć obsługiwane aplikacje i typy maszyn wirtualnych dla oprogramowania SAP na platformie Azure. Dostępne są również opcje planu testowania rozwiązania do odzyskiwania po awarii.
* Dowiedz się więcej o [replikacji innych obciążeń](site-recovery-workload.md) przy użyciu Site Recovery.