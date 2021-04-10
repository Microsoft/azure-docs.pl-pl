---
title: Ochrona serwera plików za pomocą usługi Azure Site Recovery
description: W tym artykule został opisany sposób ochrony serwera plików przy użyciu usługi Azure Site Recovery
author: Sharmistha-Rai
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 07/31/2019
ms.author: sharrai
ms.custom: mvc
ms.openlocfilehash: 9cef163c1b53360222ca32a827552fa361e9dd40
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98874251"
---
# <a name="protect-a-file-server-by-using-azure-site-recovery"></a>Ochrona serwera plików za pomocą usługi Azure Site Recovery 

Usługa [Azure Site Recovery](site-recovery-overview.md) przyczynia się do strategii związanej z ciągłością biznesową i odzyskiwaniem po awarii (BCDR, business continuity and disaster recovery) przez zapewnienie niezawodnego działania aplikacji biznesowych podczas planowanych lub nieplanowanych przestojów. Usługa Site Recovery zarządza odzyskiwaniem po awarii maszyn lokalnych i maszyn wirtualnych platformy Azure, a także organizuje to odzyskiwanie. Odzyskiwanie po awarii obejmuje replikację, tryb failover i odzyskiwanie różnych obciążeń.

Ten artykuł zawiera opis sposobu ochrony serwera plików przy użyciu usługi Site Recovery i podaje inne zalecenia dostosowane do potrzeb różnych środowisk. 

- [Replikowanie maszyn serwera plików w usłudze IaaS platformy Azure](#disaster-recovery-recommendation-for-azure-iaas-virtual-machines)
- [Replikowanie lokalnego serwera plików przy użyciu usługi Site Recovery](#replicate-an-on-premises-file-server-by-using-site-recovery)

## <a name="file-server-architecture"></a>Architektura serwera plików
Celem otwartego rozproszonego systemu udostępniania plików jest zapewnienie środowiska, gdzie grupa geograficznie rozproszonych użytkowników może efektywnie współpracować przy plikach i może zagwarantować, że ich wymagania dotyczące integralności są wymuszane. Typowy lokalny ekosystem serwera plików obsługujący dużą liczbę równoczesnych użytkowników i dużą liczbę elementów zawartości używa replikacji rozproszonego systemu plików (DFSR) do planowania replikacji i ograniczania przepustowości. 

Replikacja DFSR używa algorytmu kompresji znanego jako zdalna kompresja różnicowa (RDC), który może służyć do wydajnej aktualizacji plików przy ograniczonej przepustowości sieci. Wykrywa ona wstawienia, usunięcia i zmiany uporządkowania danych w plikach. Replikacja DFSR jest włączona tylko dla replikacji zmienionych bloków pliku podczas aktualizacji plików. Istnieją również środowiska serwera plików, gdzie codzienne kopie zapasowe są wykonywane w godzinach poza szczytem, co odpowiada potrzebom awarii. Replikacja DFSR nie jest zaimplementowana.

Na poniższym diagramie przedstawiono środowisko serwera plików z wdrożoną replikacją DFSR.
        
![Architektura replikacji DFSR](media/site-recovery-file-server/dfsr-architecture.JPG)

Na poprzednim diagramie wiele serwerów plików, nazywanych członkami, aktywnie uczestniczy w replikacji plików w grupie replikacji. Zawartość w zreplikowanym folderze jest dostępna dla wszystkich klientów, którzy wysyłają żądania do dowolnego członka nawet wtedy, gdy członek przejdzie w tryb offline.

## <a name="disaster-recovery-recommendations-for-file-servers"></a>Zalecenia dotyczące odzyskiwania po awarii dla serwerów plików

* **Replikowanie serwera plików przy użyciu usługi Site Recovery**: serwery plików mogą być replikowane na platformie Azure przy użyciu usługi Site Recovery. Gdy co najmniej jeden lokalny serwer plików jest niedostępny, odzyskiwanie maszyn wirtualnych można przeprowadzić na platformie Azure. Maszyny wirtualne mogą następnie lokalnie obsługiwać żądania od klientów pod warunkiem, że istnieje połączenie sieci VPN typu lokacja-lokacja i usługa Active Directory jest skonfigurowana na platformie Azure. Tej metody możesz użyć w przypadku środowiska skonfigurowanego dla replikacji DFSR lub prostego środowiska serwera plików bez replikacji DFSR. 

* **Rozszerzanie replikacji DFSR na maszynę wirtualną usługi IaaS platformy Azure**: w klastrowanym środowisku serwera plików z wdrożoną replikacją DFSR możesz rozszerzyć lokalną replikację DFSR na platformę Azure. Maszyna wirtualna platformy Azure jest następnie konfigurowana do wykonywania roli serwera plików. 

    * Po obsłużeniu zależności połączenia sieci VPN typu lokacja-lokacja i usługi Active Directory oraz gdy replikacja DFSR jest uruchomiona i co najmniej jeden lokalny serwer plików jest niedostępny, klienci mogą łączyć się z maszyną wirtualną platformy Azure, która obsłuży żądania.

    * Możesz użyć tego podejścia, jeśli Twoje maszyny wirtualne mają konfiguracje, które nie są obsługiwane przez usługę Site Recovery. Przykładem jest udostępniony dysk klastra, który jest czasem powszechnie używany w środowiskach serwerów plików. Replikacja DFSR działa również dobrze w środowiskach o niskiej przepustowości ze średnim współczynnikiem rezygnacji. Musisz wziąć pod uwagę dodatkowy koszt posiadania maszyny wirtualnej platformy Azure działającej przez cały czas. 

* **Użyj Azure File Sync, aby replikować pliki**: Jeśli planujesz używać chmury lub już korzystasz z maszyny wirtualnej platformy Azure, możesz użyć Azure File Sync. Azure File Sync oferuje synchronizację w pełni zarządzanych udziałów plików w chmurze, które są dostępne za pośrednictwem standardowego protokołu [bloku komunikatów serwera](/windows/win32/fileio/microsoft-smb-protocol-and-cifs-protocol-overview) (SMB). Udziały plików platformy Azure można następnie instalować współbieżnie za pośrednictwem chmurowych lub lokalnych wdrożeń systemów Windows, Linux i macOS. 

Poniższy diagram ułatwia określenie, jakiej strategii użyć w swoim środowisku serwera plików.

![Drzewo decyzyjne](media/site-recovery-file-server/decisiontree.png)


### <a name="factors-to-consider-in-your-decisions-about-disaster-recovery-to-azure"></a>Czynniki do rozważenia w ramach decyzji dotyczących odzyskiwania po awarii na platformie Azure

|Środowisko  |Zalecenie  |Kwestie do rozważenia |
|---------|---------|---------|
|Środowisko serwera plików z replikacją DFSR lub bez niej|   [Używanie usługi Site Recovery do replikacji](#replicate-an-on-premises-file-server-by-using-site-recovery)   |    Usługa Site Recovery nie obsługuje klastrów z udostępnianym dyskiem ani urządzeniem magazynującym dołączonym do sieci (NAS). Jeśli środowisko korzysta z tych konfiguracji, użyj dowolnego innego podejścia zgodnie z potrzebami. <br> Usługa Site Recovery nie obsługuje protokołu SMB 3.0. Zreplikowana maszyna wirtualna zawiera zmiany tylko wtedy, gdy zmiany wprowadzone w plikach są aktualizowane w oryginalnej lokalizacji plików.<br>  Site Recovery zapewnia proces replikacji danych niemal synchronicznych, a tym samym w przypadku nieplanowanego scenariusza trybu failover może istnieć potencjalna utrata danych i może to spowodować problemy z niezgodnością numerów USN.
|Środowisko serwera plików z replikacją DFSR     |  [Rozszerzenie replikacji DFSR na maszynie wirtualnej usługi IaaS platformy Azure](#extend-dfsr-to-an-azure-iaas-virtual-machine)  |    Replikacja DFSR działa dobrze w środowiskach z bardzo podzieloną przepustowością. Takie podejście wymaga, aby maszyna wirtualna platformy Azure działała przez cały czas. Należy uwzględnić w planach koszt maszyny wirtualnej.         |
|Maszyna wirtualna usługi IaaS platformy Azure     |     Usługa File Sync    |     Jeśli używasz usługi File Sync w scenariuszu odzyskiwania po awarii, w trybie failover musisz wykonać działania ręcznie, aby upewnić się, że udziały plików są dostępne na komputerze klienckim w sposób przezroczysty. Usługa File Sync wymaga, aby port 445 został otwarty z maszyny klienckiej.     |


### <a name="site-recovery-support"></a>Obsługa usługi Site Recovery
Ponieważ replikacja usługi Site Recovery jest niezależna od aplikacji, te rekomendacje powinny pozostać prawdziwe w następujących scenariuszach.

| Element źródłowy  |Do lokacji dodatkowej  |Na platformę Azure
|---------|---------|---------|
|Azure|  -|Tak|
|Hyper-V|  Tak  |Tak
|VMware  |Tak|  Tak
|Serwer fizyczny|  Tak  |Tak
 

> [!IMPORTANT]
> Przed skorzystaniem z dowolnego z następujących trzech podejść upewnij się, że te zależności zostały uwzględnione.



**Połączenie lokacja-lokacja**: należy ustanowić bezpośrednie połączenie między lokacją lokalną i siecią platformy Azure w celu umożliwienia komunikacji między serwerami. Użyj bezpiecznego połączenia sieci VPN typu lokacja-lokacja z siecią wirtualną platformy Azure, która jest używana jako lokacja odzyskiwania po awarii. Aby uzyskać więcej informacji, zobacz [Establish a site-to-site VPN connection between an on-premises site and an Azure virtual network](../vpn-gateway/tutorial-site-to-site-portal.md) (Nawiązywanie połączenia VPN typu lokacja-lokacja między lokacją lokalną i siecią wirtualną platformy Azure).

**Usługa Active Directory**: replikacja DFSR jest zależna od usługi Active Directory. Oznacza to, że las usługi Active Directory z lokalnymi kontrolerami domeny jest rozszerzony do lokacji odzyskiwania po awarii na platformie Azure. Nawet jeśli nie używasz replikacji DFSR, jeśli odpowiednim użytkownikom należy przyznać dostęp lub sprawdzić pod kątem dostępu, musisz wykonać następujące kroki. Aby uzyskać więcej informacji, zobacz [Extend on-premises Active Directory to Azure](./site-recovery-active-directory.md) (Rozszerzenie lokalnej usługi Active Directory na platformę Azure).

## <a name="disaster-recovery-recommendation-for-azure-iaas-virtual-machines"></a>Zalecenie odzyskiwania po awarii dla maszyn wirtualnych usługi IaaS platformy Azure

Jeśli konfigurujesz odzyskiwanie po awarii serwerów plików hostowanych na maszynach wirtualnych usługi IaaS platformy Azure i zarządzasz nim, możesz wybrać jedną z dwóch opcji w zależności od tego, czy chcesz przenieść się do usługi [Azure Files](../storage/files/storage-files-introduction.md):

* [Używanie usługi File Sync](#use-file-sync-to-replicate-files-hosted-on-an-iaas-virtual-machine)
* [Używanie usługi Site Recovery](#replicate-an-iaas-file-server-virtual-machine-by-using-site-recovery)

## <a name="use-file-sync-to-replicate-files-hosted-on-an-iaas-virtual-machine"></a>Używanie usługi File Sync do replikowania plików hostowanych na maszynie wirtualnej usługi IaaS

Usługi Azure Files można użyć w celu całkowitego zastąpienia lub uzupełnienia tradycyjnych lokalnych serwerów plików lub urządzeń NAS. Udziały plików platformy Azure można również replikować za pomocą usługi Azure File Sync w systemach Windows Server, lokalnie lub w chmurze, w celu zapewnienia wydajności i rozproszonego buforowania danych w miejscu ich używania. W poniższych krokach opisano zalecenie dotyczące odzyskiwania po awarii dla maszyn wirtualnych platformy Azure, które wykonują te same funkcje co tradycyjne serwery plików:
* Chroń maszyny przy użyciu usługi Site Recovery. Postępuj zgodnie z instrukcjami w temacie [Replikowanie maszyny wirtualnej platformy Azure w innym regionie platformy Azure](azure-to-azure-quickstart.md).
* Usługa File Sync służy do replikacji plików z maszyny wirtualnej, która działa jako serwer plików, do chmury.
* Funkcja [plan odzyskiwania](site-recovery-create-recovery-plans.md) usługi Site Recovery służy do dodawania skryptów w celu [zainstalowania udziału plików platformy Azure](../storage/files/storage-how-to-use-files-windows.md) i uzyskiwania dostępu do udziału na Twojej maszynie wirtualnej.

W poniższych krokach skrótowo opisano sposób używania usługi File Sync:

1. [Utwórz konto magazynu na platformie Azure](../storage/common/storage-account-create.md?toc=/azure/storage/files/toc.json). Jeśli dla swoich kont magazynu wybierzesz magazyn geograficznie nadmiarowy z dostępem do odczytu, w razie awarii otrzymasz dostęp do odczytu do swoich danych z regionu pomocniczego. Aby uzyskać więcej informacji, zobacz [odzyskiwanie po awarii i konto magazynu w trybie failover](../storage/common/storage-disaster-recovery-guidance.md?toc=%2fazure%2fstorage%2ffiless%2ftoc.json).
2. [Utwórz udział plików](../storage/files/storage-how-to-create-file-share.md).
3. [Uruchom usługę File Sync](../storage/files/storage-sync-files-deployment-guide.md) na swoim serwerze plików platformy Azure.
4. Utwórz grupę synchronizacji. Punkty końcowe w ramach grupy synchronizacji są synchronizowane ze sobą. Grupa synchronizacji musi zawierać co najmniej jeden punkt końcowy w chmurze, który reprezentuje udział plików platformy Azure. Grupa synchronizacji musi również zawierać jeden punkt końcowy serwera, który reprezentuje ścieżkę na serwerze z systemem Windows.
5. Twoje pliki są teraz synchronizowane z udziałem plików platformy Azure i Twoim serwerem lokalnym.
6. W przypadku awarii w środowisku lokalnym wykonaj przejście w tryb failover przy użyciu [planu odzyskiwania](site-recovery-create-recovery-plans.md). Dodaj skrypt w celu [zainstalowania udziału plików platformy Azure](../storage/files/storage-how-to-use-files-windows.md) i uzyskania dostępu do udziału na swojej maszynie wirtualnej.

### <a name="replicate-an-iaas-file-server-virtual-machine-by-using-site-recovery"></a>Replikowanie maszyny wirtualnej serwera plików usługi IaaS przy użyciu usługi Site Recovery

Jeśli masz klientów lokalnych, którzy uzyskują dostęp do maszyny wirtualnej serwera plików usługi IaaS, wykonaj wszystkie następujące kroki. W przeciwnym razie przejdź do kroku 3.

1. Nawiąż połączenie sieci VPN typu lokacja-lokacja między lokacją lokalną i siecią platformy Azure.
2. Rozszerz lokalną usługę Active Directory.
3. [Skonfiguruj odzyskiwanie po awarii](azure-to-azure-tutorial-enable-replication.md) dla maszyny serwera plików usługi IaaS do regionu pomocniczego.


Aby uzyskać więcej informacji na temat odzyskiwania po awarii do regionu pomocniczego, zobacz [ten artykuł](./azure-to-azure-architecture.md).


## <a name="replicate-an-on-premises-file-server-by-using-site-recovery"></a>Replikowanie lokalnego serwera plików przy użyciu usługi Site Recovery

W poniższych krokach opisano replikację dla maszyny wirtualnej VMware. Kroki replikacji maszyny wirtualnej funkcji Hyper-V można znaleźć w [tym samouczku](./hyper-v-azure-tutorial.md).

1. [Przygotuj zasoby platformy Azure](tutorial-prepare-azure.md) do replikacji maszyn lokalnych.
2. Nawiąż połączenie sieci VPN typu lokacja-lokacja między lokacją lokalną i siecią platformy Azure. 
3. Rozszerz lokalną usługę Active Directory.
4. [Przygotowywanie lokalnych serwerów VMware](./vmware-azure-tutorial-prepare-on-premises.md).
5. [Konfigurowanie odzyskiwania po awarii](./vmware-azure-tutorial.md) na platformie Azure dla lokalnych maszyn wirtualnych.

## <a name="extend-dfsr-to-an-azure-iaas-virtual-machine"></a>Rozszerzenie replikacji DFSR na maszynie wirtualnej usługi IaaS platformy Azure

1. Nawiąż połączenie sieci VPN typu lokacja-lokacja między lokacją lokalną i siecią platformy Azure. 
2. Rozszerz lokalną usługę Active Directory.
3. [Tworzenie i aprowizowanie serwera plików maszyny wirtualnej](../virtual-machines/windows/quick-create-portal.md?toc=%2Fazure%2Fvirtual-machines%2Fwindows%2Ftoc.json) w sieci wirtualnej platformy Azure.
Upewnij się, że maszyna wirtualna zostanie dodana do tej samej sieci wirtualnej platformy Azure, która ma łączność krzyżową ze środowiskiem lokalnym. 
4. Zainstaluj i [skonfiguruj replikację DFSR](https://techcommunity.microsoft.com/t5/storage-at-microsoft/dfs-replication-initial-sync-in-windows-server-2012-r2-attack-of/ba-p/424877) w systemie Windows Server.
5. [Zaimplementuj obszar nazw systemu plików DFS](/windows-server/storage/dfs-namespaces/deploying-dfs-namespaces).
6. Mając zaimplementowany obszar nazw systemu plików DFS, praca w trybie failover dla folderów udostępnionych ze środowiska produkcyjnego do lokacji odzyskiwania po awarii może odbywać się przez aktualizację obiektów docelowych folderów w obszarze nazw systemu plików DFS. Po replikacji tych zmian obszaru nazw systemu plików DFS za pomocą usługi Active Directory użytkownicy są połączeni z odpowiednimi folderami docelowymi w sposób niewidoczny dla użytkownika.

## <a name="use-file-sync-to-replicate-your-on-premises-files"></a>Replikowanie plików lokalnych przy użyciu usługi File Sync
Usługi File Sync można użyć do replikacji plików do chmury. W przypadku awarii i niedostępności Twojego lokalnego serwera plików możesz zainstalować żądane lokalizacje plików z chmury i kontynuować obsługę żądań z komputerów klienckich.
Aby zintegrować usługę File Sync z usługą Site Recovery:

* Chroń maszyny serwera plików przy użyciu usługi Site Recovery. Postępuj zgodnie z instrukcjami podanymi w [tym samouczku](./vmware-azure-tutorial.md).
* Usługa File Sync umożliwia replikację plików z komputera, który służy jako serwer plików, do chmury.
* Funkcja planu odzyskiwania w usłudze Site Recovery pozwala dodać skrypty instalujące udział plików platformy Azure na serwerze plików w trybie failover maszyny wirtualnej na platformie Azure.

Wykonaj następujące kroki, aby użyć usługi File Sync:

1. [Utwórz konto magazynu na platformie Azure](../storage/common/storage-account-create.md?toc=/azure/storage/files/toc.json). Jeśli dla swoich kont magazynu wybierzesz magazyn geograficznie nadmiarowy z dostępem do odczytu (zalecany), w razie awarii masz dostęp do odczytu do swoich danych z regionu pomocniczego. Aby uzyskać więcej informacji, zobacz [odzyskiwanie systemu po awarii i tryb failover dla konta magazynu](../storage/common/storage-disaster-recovery-guidance.md?toc=%2fazure%2fstorage%2ffiless%2ftoc.json).
2. [Utwórz udział plików](../storage/files/storage-how-to-create-file-share.md).
3. [Wdróż usługę File Sync](../storage/files/storage-sync-files-deployment-guide.md) na lokalnym serwerze plików.
4. Utwórz grupę synchronizacji. Punkty końcowe w ramach grupy synchronizacji są synchronizowane ze sobą. Grupa synchronizacji musi zawierać co najmniej jeden punkt końcowy w chmurze, który reprezentuje udział plików platformy Azure. Grupa synchronizacji musi również zawierać jeden punkt końcowy serwera, który reprezentuje ścieżkę na lokalnym serwerze z systemem Windows.
5. Twoje pliki są teraz synchronizowane z udziałem plików platformy Azure i Twoim serwerem lokalnym.
6. W przypadku awarii w środowisku lokalnym wykonaj przejście w tryb failover przy użyciu [planu odzyskiwania](site-recovery-create-recovery-plans.md). Dodaj skrypt w celu zainstalowania udziału plików platformy Azure i uzyskania dostępu do udziału na swojej maszynie wirtualnej.

> [!NOTE]
> Upewnij się, że port 445 jest otwarty. Usługa Azure Files korzysta z protokołu SMB. Protokół SMB komunikuje się za pośrednictwem portu TCP 445. Sprawdź, czy zapora nie blokuje portu TCP 445 z komputera klienckiego.


## <a name="do-a-test-failover"></a>Przeprowadzanie testu trybu failover

1. Przejdź do witryny Azure Portal, a następnie wybierz swój magazyn usługi odzyskiwania.
2. Wybierz plan odzyskiwania utworzony dla środowiska serwera plików.
3. Wybierz pozycję **Test pracy w trybie failover**.
4. Wybierz punkt odzyskiwania i sieć wirtualną platformy Azure, aby rozpocząć proces testowania pracy w trybie failover.
5. Po uruchomieniu dodatkowego środowiska wykonaj swoje operacje weryfikacji.
6. Po zakończeniu operacji weryfikacji wybierz pozycję **Wyczyść test pracy w trybie failover** w planie odzyskiwania, a środowisko testowania pracy w trybie failover zostanie oczyszczone.

Aby uzyskać więcej informacji na temat sposobu przeprowadzania testu pracy w trybie failover, zobacz [Test failover to Site Recovery](site-recovery-test-failover-to-azure.md) (Test pracy w trybie failover dla usługi Site Recovery).

Aby uzyskać wskazówki dotyczące przeprowadzania testu pracy w trybie failover dla usługi Active Directory i DNS, zobacz [Test failover considerations for Active Directory and DNS](site-recovery-active-directory.md) (Zagadnienia testowania pracy w trybie failover dla usługi Active Directory i DNS).

## <a name="do-a-failover"></a>Przejdź w tryb failover

1. Przejdź do witryny Azure Portal i wybierz swój magazyn usługi Recovery Services.
2. Wybierz plan odzyskiwania utworzony dla środowiska serwera plików.
3. Wybierz pozycję **Tryb failover**.
4. Wybierz punkt odzyskiwania, aby rozpocząć proces trybu failover.

Aby uzyskać więcej informacji na temat sposobu pracy w trybie failover, zobacz [Failover in Site Recovery](site-recovery-failover.md) (Tryb failover w usłudze Site Recovery).