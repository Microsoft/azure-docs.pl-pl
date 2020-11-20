---
title: Migrowanie SAP HANA na platformie Azure (duże wystąpienia) do usługi Azure Virtual Machines | Microsoft Docs
description: Jak migrować SAP HANA na platformie Azure (duże wystąpienia) do usługi Azure Virtual Machines
services: virtual-machines-linux
documentationcenter: ''
author: bentrin
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/11/2020
ms.author: bentrin
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 25eae9f9ba0e28a5aa069972c8c7d5eb2877545f
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94967690"
---
# <a name="sap-hana-on-azure-large-instance-migration-to-azure-virtual-machines"></a>SAP HANA migracji dużych wystąpień platformy Azure do platformy Azure Virtual Machines
W tym artykule opisano możliwe scenariusze wdrażania dużych wystąpień platformy Azure i zawarto podejście do planowania i migracji z zminimalizowanym przestojem przejścia

## <a name="overview"></a>Omówienie
Ponieważ anonsowanie dużych wystąpień platformy Azure dla SAP HANA (HLI) we wrześniu 2016, wielu klientów przyjmuje ten sprzęt jako ofertę usługi dla swojej platformy obliczeniowej w pamięci.  W ostatnich latach rozszerzenie rozmiaru maszyny wirtualnej platformy Azure w połączeniu z obsługą wdrażania w skali HANA przekroczyło największe zapotrzebowanie na wydajność bazy danych ERP klientów korporacyjnych.  Zaczynamy widzieć klientów, którzy wyrażają zainteresowanie tym, aby migrować SAP HANA obciążenie z serwerów fizycznych do maszyn wirtualnych platformy Azure.
Ten przewodnik nie jest dokumentem konfiguracji krok po kroku. Opisuje ona typowe modele wdrażania i oferuje zalecenia dotyczące planowania i migracji.  Celem jest wypróbowanie koniecznych zagadnień związanych z przygotowaniem w celu zminimalizowania przestojów przejścia.

## <a name="assumptions"></a>Założenia
W tym artykule opisano następujące założenia:
- Jedyne rozważane zainteresowania to niejednorodna Migracja usługi obliczeniowej bazy danych HANA z dużego wystąpienia Hana (HLI) do maszyny wirtualnej platformy Azure bez znaczących uaktualnień lub poprawek oprogramowania. Te drobne aktualizacje obejmują używanie nowszej wersji systemu operacyjnego lub wersji HANA jawnie ustalonej jako obsługiwane przez odpowiednie uwagi SAP. 
- Wszystkie działania Updates/Upgrades należy wykonać przed migracją lub po niej.  Na przykład SAP HANA MCOS konwertowanie do wdrożenia MDC. 
- Podejście do migracji, które będzie oferować najmniejszy przestój, jest SAP HANA replikacji systemu. Inne metody migracji nie są częścią zakresu tego dokumentu.
- Te wskazówki dotyczą zarówno jednostek SKU rev3, jak i Rev4 z elementu HLI.
- Architektura wdrażania platformy HANA pozostaje przede wszystkim niezmieniona podczas migracji.  Oznacza to, że system z pojedynczym wystąpieniem odzyskiwania po awarii pozostaje taki sam w miejscu docelowym.
- Klienci przejrzały i rozumieli Umowa dotycząca poziomu usług (SLA) dla architektury docelowej (do). 
- Warunki komercyjne między HLIs i maszynami wirtualnymi są różne. Klienci powinni monitorować korzystanie z maszyn wirtualnych w celu zarządzania kosztami.
- Klienci wiedzą, że wartość HLI jest dedykowaną platformą obliczeniową, podczas gdy maszyny wirtualne działają w infrastrukturze, która została jeszcze udostępniona.
- Klienci mają zweryfikowane, że docelowe maszyny wirtualne obsługują zamierzoną architekturę. Aby wyświetlić wszystkie obsługiwane jednostki SKU maszyn wirtualnych z certyfikatem wdrożenia SAP HANA, zobacz [Katalog sprzętu SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure).
- Klienci sprawdziły plan projektu i migracji.
- Zaplanuj maszynę wirtualną odzyskiwania po awarii wraz z lokacją główną.  Klienci nie mogą używać elementu HLI jako węzła DR dla lokacji głównej działającej na maszynach wirtualnych po migracji.
- Klienci skopiowali wymagane pliki kopii zapasowej do docelowych maszyn wirtualnych w oparciu o wymagania dotyczące możliwości odzyskiwania i zgodności firmy. Za pomocą kopii zapasowych dostępnych maszyn wirtualnych umożliwia odzyskiwanie do punktu w czasie w okresie przejściowym.
- W przypadku HSR HA klienci muszą skonfigurować i skonfigurować urządzenie STONITH na SAP HANA przewodniki o wysokiej dostępności dla [SLES](./high-availability-guide-suse-pacemaker.md) i [RHEL](./high-availability-guide-rhel-pacemaker.md).  Nie jest to wstępnie skonfigurowane, jak w przypadku programu.
- To podejście migracji nie obejmuje jednostek SKU Optane z konfiguracją.

## <a name="deployment-scenarios"></a>Scenariusze wdrażania
W poniższej tabeli zestawiono typowe modele wdrażania z klientami.  Możliwa jest migracja do maszyn wirtualnych platformy Azure w przypadku wszystkich scenariuszy.  Aby skorzystać z dodatkowych dostępnych usług platformy Azure, mogą być wymagane niewielkie zmiany architektury.

| Identyfikator scenariusza | HLI — scenariusz | Przeprowadzić migrację do maszyny wirtualnej Verbatim? | Dyskusji |
| --- | --- | --- | --- |
| 1 | [Pojedynczy węzeł z jednym identyfikatorem SID](./hana-supported-scenario.md#single-node-with-one-sid) | Tak | - |
| 2 | [Pojedynczy węzeł z MCOS](./hana-supported-scenario.md#single-node-mcos) | Tak | - |
| 3 | [Pojedynczy węzeł z odzyskiwaniem po awarii przy użyciu replikacji magazynu](./hana-supported-scenario.md#single-node-with-dr-using-storage-replication) | Nie | Replikacja magazynu nie jest dostępna w przypadku platformy wirtualnej platformy Azure, Zmień bieżące rozwiązanie DR na HSR lub Backup/Restore |
| 4 | [Pojedynczy węzeł z DR (Multipurpose) przy użyciu replikacji magazynu](./hana-supported-scenario.md#single-node-with-dr-multipurpose-using-storage-replication) | Nie | Replikacja magazynu nie jest dostępna w przypadku platformy wirtualnej platformy Azure, Zmień bieżące rozwiązanie DR na HSR lub Backup/Restore |
| 5 | [HSR z STONITHem w celu zapewnienia wysokiej dostępności](./hana-supported-scenario.md#hsr-with-stonith-for-high-availability) | Tak | Brak wstępnie skonfigurowanych SBD dla docelowych maszyn wirtualnych.  Wybierz i Wdróż rozwiązanie STONITH.  Możliwe opcje: Agent ogrodzenia platformy Azure (obsługiwany zarówno w [RHEL](./high-availability-guide-rhel-pacemaker.md), [SLES](./high-availability-guide-suse-pacemaker.md)), SBD |
| 6 | [HA z HSR, DR z replikacją magazynu](./hana-supported-scenario.md#high-availability-with-hsr-and-dr-with-storage-replication) | Nie | Zastąp replikację magazynu dla potrzeb odzyskiwania po awarii przy użyciu usługi HSR lub tworzenia kopii zapasowej/przywracania |
| 7 | [Przełączenie w tryb failover hosta (1 + 1)](./hana-supported-scenario.md#host-auto-failover-11) | Tak | Korzystanie z usługi ANF dla magazynu udostępnionego z maszynami wirtualnymi platformy Azure |
| 8 | [Skalowanie w poziomie przy użyciu rezerwy](./hana-supported-scenario.md#scale-out-with-standby) | Tak | 4HANA M128s, M416s, M416ms VM przy użyciu ANF tylko dla magazynu |
| 9 | [Skalowanie w poziomie bez rezerwy](./hana-supported-scenario.md#scale-out-without-standby) | Tak | BW/4HANA z M128s, M416s, M416ms VM (z lub bez użycia ANF for Storage) |
| 10 | [Skalowanie za pomocą odzyskiwania po awarii przy użyciu replikacji magazynu](./hana-supported-scenario.md#scale-out-with-dr-using-storage-replication) | Nie | Zastąp replikację magazynu dla potrzeb odzyskiwania po awarii przy użyciu usługi HSR lub tworzenia kopii zapasowej/przywracania |
| 11 | [Pojedynczy węzeł z odzyskiwaniem po awarii przy użyciu HSR](./hana-supported-scenario.md#single-node-with-dr-using-hsr) | Tak | - |
| 12 | [HSR z jednym węzłem do odzyskiwania po awarii (koszt zoptymalizowany)](./hana-supported-scenario.md#single-node-hsr-to-dr-cost-optimized) | Tak | - |
| 13 | [HA i DR z HSR](./hana-supported-scenario.md#high-availability-and-disaster-recovery-with-hsr) | Tak | - |
| 14 | [HA i DR z HSR (zoptymalizowane pod kątem kosztów)](./hana-supported-scenario.md#high-availability-and-disaster-recovery-with-hsr-cost-optimized) | Tak | - |
| 15 | [Skalowanie w poziomie za pomocą narzędzia DR using HSR](./hana-supported-scenario.md#scale-out-with-dr-using-hsr) | Tak | BW/4HANA z M128s. M416s, M416ms maszyny wirtualne (z użyciem programu ANF for Storage lub bez niego) |


## <a name="source-hli-planning"></a>Planowanie źródła (HLI)
Podczas dołączania serwera z systemem, zarówno do zarządzania usługami firmy Microsoft, jak i klientów odbywa się zgodnie z planowaniem ustawień dotyczących obliczeń, sieci, magazynu i systemu operacyjnego na potrzeby uruchamiania bazy danych SAP HANA.  Podobne planowanie należy wykonać, aby przeprowadzić migrację na maszynę wirtualną platformy Azure.

### <a name="sap-hana-housekeeping"></a>SAP HANA dla gospodarstw domowych 
Dobrym sposobem działania jest uporządkowanego zawartości bazy danych, tak aby niechciane, nieaktualne dane lub stare dzienniki nie były migrowane do nowej bazy danych.  Dla gospodarstw domowych zazwyczaj polega na usunięciu lub archiwizowaniu starych, wygasłych lub nieaktywnych danych.  Te akcje dotyczące "higieny danych" należy przetestować w systemach nieprodukcyjnych w celu sprawdzenia poprawności przycinania danych przed użyciem produkcji.

### <a name="allow-network-connectivity-for-new-vms-and-or-virtual-network"></a>Zezwalaj na łączność sieciową dla nowych maszyn wirtualnych i sieci wirtualnych 
W przypadku wdrażania klienta, Sieć została skonfigurowana w oparciu o informacje opisane w artykule [SAP HANA (duże wystąpienia) Architektura sieci](./hana-network-architecture.md). Ponadto Routing ruchu sieciowego odbywa się w sposób opisany w sekcji "Routing na platformie Azure".
- W przypadku konfigurowania nowej maszyny wirtualnej jako lokalizacji docelowej migracji, jeśli znajduje się ona w istniejącej sieci wirtualnej z zakresami adresów IP, które są już dozwolone do łączenia z elementem HLI, nie jest wymagana żadna dodatkowa aktualizacja łączności.
- Jeśli nowa maszyna wirtualna platformy Azure zostanie umieszczona w nowym Microsoft Azure Virtual Network, może znajdować się w innym regionie i nawiązać połączenie równorzędne z istniejącą siecią wirtualną, klucz usługi ExpressRoute i identyfikator zasobu z oryginalnej metody aprowizacji jest używany do zezwalania na dostęp dla tego nowego zakresu adresów IP sieci wirtualnej.  Koordynuj za pomocą usługi Microsoft Service Management, aby umożliwić łączność z siecią wirtualną.  Uwaga: aby zminimalizować opóźnienie sieci między warstwami aplikacji i bazy danych, zarówno warstwy aplikacji, jak i bazy danych muszą znajdować się w tej samej sieci wirtualnej.  

### <a name="existing-app-layer-availability-set-availability-zones-and-proximity-placement-group-ppg"></a>Istniejący zestaw dostępności warstwy aplikacji, Strefy dostępności i Grupa umieszczania sąsiedztwa (PPG)
Bieżący model wdrażania jest gotowy do spełnienia określonych celów poziomu usług.  W tym celu upewnij się, że infrastruktura docelowa spełni lub przekroczy określone cele.  
Serwery aplikacji SAP są coraz większe niż nie, a klienci są umieszczani w zestawie dostępności.  Jeśli bieżący poziom usług wdrożenia jest zadowalający i 
- Jeśli docelowa maszyna wirtualna przyjmuje nazwę hosta nazwy, aktualizując adres usługi nazw domen (DNS) wskazujący, że IP maszyny wirtualnej będzie działał bez aktualizowania żadnych profilów SAP
- Jeśli nie korzystasz z programu PPG, pamiętaj, aby umieścić wszystkie serwery aplikacji i bazy danych w tej samej strefie, aby zminimalizować opóźnienie sieci.
- Jeśli używasz programu PPG, zapoznaj się z sekcją tego dokumentu: "Planowanie docelowe, zestaw dostępności, Strefy dostępności i Grupa umieszczania sąsiedztwa (PPG)".

### <a name="storage-replication-discontinuance-process-if-used"></a>Proces zaprzestania replikacji magazynu (jeśli jest używany)
Jeśli replikacja magazynu jest używana jako rozwiązanie odzyskiwania po awarii, należy ją przerwać (niezaplanowane) po wyłączeniu aplikacji SAP.  Ponadto ostatni SAP HANA wykaz, plik dziennika i kopie zapasowe danych zostały zreplikowane na zdalnych woluminach magazynu DR.  Należy to zrobić jako środek ostrożności w przypadku awarii serwera fizycznego na przejście do maszyny wirtualnej platformy Azure.

### <a name="data-backups-preservation-consideration"></a>Zagadnienie dotyczące zachowywania kopii zapasowych danych
Po przecięciu do SAP HANA na maszynie wirtualnej platformy Azure, wszystkie dane oparte na migawce lub kopie zapasowe dzienników na serwerze HLI nie są łatwo dostępne lub dostępnych maszyny wirtualnej w razie potrzeby. Przed rozpoczęciem tworzenia kopii zapasowej opartej na platformie Azure w okresie wczesnego przejścia w celu spełnienia wymagań związanych z odzyskiwaniem do określonych momentów zalecamy utworzenie kopii zapasowych na poziomie plików oprócz migawek, dni lub tygodni przed przecięciem.  Te kopie zapasowe są kopiowane do konta usługi Azure Storage dostępnego dla nowej maszyny wirtualnej SAP HANA.
Oprócz tworzenia kopii zapasowej zawartości klucza, rozsądne jest posiadanie pełnych kopii zapasowych oprogramowania SAP, które są łatwo dostępne w przypadku, gdy konieczne jest wycofanie.

### <a name="adjusting-system-monitoring"></a>Dostosowywanie monitorowania systemu 
Klienci używają wielu różnych narzędzi do monitorowania i wysyłania powiadomień o alertach dla systemów w ramach ich poziomej platformy SAP.  Ten element jest tylko wywołaniem odpowiedniej akcji, aby uwzględnić zmiany dotyczące monitorowania i aktualizowania odbiorców powiadomień o alertach w razie potrzeby.

### <a name="microsoft-operations-team-involvement"></a>Uczestnictwo zespołu operacyjnego firmy Microsoft 
Otwórz bilet z Azure Portal na podstawie istniejącego wystąpienia elementu.  Po utworzeniu biletu pomocy technicznej skontaktuje się z Tobą za pośrednictwem poczty e-mail.  

### <a name="engage-microsoft-account-team"></a>Zaangażowanie konto Microsoft zespołu
Zaplanuj migrację blisko rocznicowego czasu odnowienia kontraktu HLI, aby zminimalizować niepotrzebnie za pośrednictwem kosztów zasobów obliczeniowych. Aby zlikwidować blok HLI, wymagany jest koordynowanie zakończenia kontraktu i rzeczywiste zamknięcie jednostki.

## <a name="destination-planning"></a>Planowanie docelowe
Zaplanuj nową infrastrukturę, aby zadbać o to, aby to zrobić, zapewnią konieczność zagwarantowania, że nowe dodanie będzie pasować do dużego schematu rzeczy.  Poniżej przedstawiono niektóre kluczowe punkty dla Contemplation.

### <a name="resource-availability-in-the-target-region"></a>Dostępność zasobów w regionie docelowym 
Bieżący region wdrażania serwerów aplikacji SAP zwykle znajduje się w bliskiej bliskości ze skojarzonym HLIs.  HLIs są jednak oferowane w mniejszej liczbie lokalizacji niż dostępne regiony platformy Azure.  Podczas migrowania fizycznego elementu HLI do maszyny wirtualnej platformy Azure jest to również dobry czas na "precyzyjne dostosowanie" odległości wszystkich powiązanych usług na potrzeby optymalizacji wydajności.  Podczas tego należy wziąć pod uwagę, że w wybranym regionie są wszystkie wymagane zasoby.  Na przykład dostępność określonej rodziny maszyn wirtualnych lub oferowanie stref platformy Azure w celu skonfigurowania wysokiej dostępności.

### <a name="virtual-network"></a>Sieć wirtualna 
Klienci muszą zdecydować, czy uruchomić nową bazę danych HANA w istniejącej sieci wirtualnej, czy utworzyć nową.  Podstawowym czynnikiem decydującym jest bieżący układ sieci dla oprogramowania SAP.  Ponadto, gdy infrastruktura przechodzi z jednej strefy do wdrożenia z dwoma strefami i używa PPG, nakłada zmiany architektury. Aby uzyskać więcej informacji, zobacz artykuł [Azure PPG w celu uzyskania optymalnego opóźnienia sieci przy użyciu aplikacji SAP](./sap-proximity-placement-scenarios.md).   

### <a name="security"></a>Zabezpieczenia
Czy nowa usługa SAP HANA VM wyląduje w nowej lub istniejącej sieci wirtualnej/podsieci, reprezentuje nową, krytyczną usługę biznesową, która wymaga ochrony.  Kontrola dostępu zgodna z zasadami zabezpieczeń informacji o firmie powinna zostać oceniona i wdrożona dla tej nowej klasy usług.

### <a name="vm-sizing-recommendation"></a>Zalecenie dotyczące zmiany wielkości maszyny wirtualnej
Ta migracja jest również okazja do odpowiedniego rozmiaru aparatu obliczeniowego platformy HANA.  Jeden z nich może korzystać z [widoków systemu](https://help.sap.com/viewer/7c78579ce9b14a669c1f3295b0d8ca16/Cloud/3859e48180bb4cf8a207e15cf25a7e57.html) Hana w połączeniu z platformą Hana Studio, aby zrozumieć użycie zasobów systemowych, co pozwala na prawidłowe ustalanie wielkości wydatków.

### <a name="storage"></a>Magazyn 
Wydajność magazynu to jeden z czynników wpływających na środowisko użytkownika aplikacji SAP.  Na podstawie danej jednostki SKU maszyny wirtualnej jest publikowany minimalny układ magazynu [SAP HANA konfigurację magazynu maszyn wirtualnych platformy Azure](./hana-vm-operations-storage.md). Zalecamy przejrzenie tych minimalnych wartości i porównanie z istniejącymi statystykami systemu HLI w celu zapewnienia odpowiedniej pojemności i wydajności operacji we/wy dla nowej maszyny wirtualnej platformy HANA.

Jeśli skonfigurujesz PPG dla nowej maszyny wirtualnej HANA i skojarzonych z nią serwerów, Prześlij bilet pomocy technicznej w celu sprawdzenia i sprawdzenia wspólnej lokalizacji magazynu i maszyny wirtualnej. Ponieważ może zajść konieczność zmiany rozwiązania do tworzenia kopii zapasowych, należy również oglądany koszt magazynu, aby uniknąć niespodziewanych wydatków operacyjnych.

### <a name="storage-replication-for-disaster-recovery"></a>Replikacja magazynu na potrzeby odzyskiwania po awarii
Przy użyciu opcji HLI replikacja magazynu została udostępniona jako opcja domyślna dla odzyskiwania po awarii. Ta funkcja nie jest opcją domyślną dla SAP HANA na maszynie wirtualnej platformy Azure. Weź pod uwagę HSR, tworzenie kopii zapasowej/przywracanie lub inne obsługiwane rozwiązania spełniające Twoje wymagania biznesowe.

### <a name="availability-sets-availability-zones-and-proximity-placement-groups"></a>Zestawy dostępności, Strefy dostępności i grupy umieszczania zbliżeniowe 
Aby skrócić odległość między warstwą aplikacji i SAP HANA, aby zachować co najmniej opóźnienie sieci, nową maszynę wirtualną bazy danych i bieżące serwery aplikacji SAP należy umieścić w PPG. Zapoznaj się z [grupą umieszczania w sąsiedztwie](./sap-proximity-placement-scenarios.md) , aby dowiedzieć się, jak zestaw dostępności platformy Azure i strefy dostępności pracy z PPG dla wdrożeń SAP
Jeśli elementy docelowe systemu HANA są wdrażane w więcej niż jednej strefie platformy Azure, klienci powinni mieć jasny widok profilu opóźnienia wybranych stref. Rozmieszczenie składników systemu SAP jest optymalne w odniesieniu do proximal odległości między aplikacją SAP a bazą danych.  [Narzędzie Test opóźnienia strefy dostępności](https://github.com/Azure/SAP-on-Azure-Scripts-and-Utilities/tree/master/AvZone-Latency-Test) domeny publicznej pomaga ułatwić pomiar.  


### <a name="backup-strategy"></a>Strategia tworzenia kopii zapasowych
Wielu klientów korzysta już z rozwiązań do tworzenia kopii zapasowych innych firm, SAP HANA w dniu HLI.  W takim przypadku należy skonfigurować tylko dodatkową chronioną bazę danych maszyny wirtualnej i platformy HANA.  Bieżące zadania tworzenia kopii zapasowej w postaci nieplanowanej są teraz niezaplanowane, jeśli komputer jest likwidowany po migracji.
Azure Backup SAP HANA na maszynie wirtualnej jest teraz ogólnie dostępna.  Zobacz te linki, aby uzyskać szczegółowe informacje na temat [tworzenia kopii](../../../backup/backup-azure-sap-hana-database.md)zapasowych, [przywracania](../../../backup/sap-hana-db-restore.md)i [zarządzania](../../../backup/sap-hana-db-manage.md) SAP HANA kopii zapasowych na maszynach wirtualnych platformy Azure.

### <a name="dr-strategy"></a>Strategia odzyskiwania po awarii
Jeśli cele poziomu usług uwzględniają dłuższy czas odzyskiwania, prosta kopia zapasowa do magazynu obiektów blob i przywracania w miejscu lub przywrócenie nowej maszyny wirtualnej jest najprostszą i tańszą strategią odzyskiwania po awarii.  
Podobnie jak platforma dużej instancji, w której program HANA DR zwykle odbywa się z HSR; Na maszynie wirtualnej platformy Azure HSR jest to również najbardziej naturalne i natywne rozwiązanie SAP HANA DR.  Niezależnie od tego, czy wdrożenie źródłowe ma pojedyncze wystąpienie, czy jako klastrowane, replika infrastruktury źródłowej jest wymagana w regionie odzyskiwania po awarii.
Ta replika DR zostanie skonfigurowana po zakończeniu migracji podstawowego programu HLI do maszyny wirtualnej.  Baza danych DR HANA zostanie zarejestrowana w podstawowym SAP HANA w wystąpieniu maszyny wirtualnej jako lokacji dodatkowej replikacji.  

### <a name="sap-application-server-connectivity-destination-change"></a>Zmiana miejsca docelowego łączności serwera aplikacji SAP
Migracja HSR skutkuje nowym hostem usługi HANA DB, a więc nową nazwą hosta bazy danych dla warstwy aplikacji, profile SAP należy zmodyfikować, aby odzwierciedlały nową nazwę hosta.  Jeśli przełączenie odbywa się przy użyciu rozpoznawania nazw, które zachowuje nazwę hosta, nie jest wymagana żadna zmiana profilu.

### <a name="operating-system"></a>System operacyjny
Obrazy systemu operacyjnego dla elementu HLI i maszyny wirtualnej, mimo że nie są na tym samym poziomie wydania, SLES 12 SP4 na przykład nie są identyczne. Aby zainstalować te same pakiety na serwerze docelowym, klienci muszą sprawdzić poprawność wymaganych pakietów, poprawek, poprawek, jądra i poprawek zabezpieczeń.  Jest ona obsługiwana, aby używać HSR do replikowania ze starszej wersji systemu operacyjnego do maszyny wirtualnej z nowszą wersją systemu operacyjnego.  Sprawdź określone obsługiwane wersje, przeglądając [uwagi SAP note 2763388](https://launchpad.support.sap.com/#/notes/2763388).

### <a name="new-sap-license-request"></a>Nowe żądanie licencji SAP
Proste żądanie żądania nowej licencji SAP dla nowego systemu HANA teraz, gdy zostanie ono zmigrowane do maszyn wirtualnych.

### <a name="service-level-agreement-sla-differences"></a>Różnice w umowie dotyczącej poziomu usług (SLA)
Autorzy lubią, aby wywoływać różnice między umowami SLA dostępności między elementami.  Na przykład klastrowane pary HLIs HA oferują dostępność na 99,99%. Aby uzyskać tę samą umowę SLA, należy wdrożyć maszyny wirtualne w strefach dostępności. W tym [artykule](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_9/) opisano dostępność ze skojarzonymi architekturami wdrażania, aby klienci mogli odpowiednio zaplanować infrastrukturę docelową.


## <a name="migration-strategy"></a>Strategia migracji
W tym dokumencie omówiono tylko podejście do replikacji systemu HANA w przypadku migracji z usługi HLI do maszyny wirtualnej platformy Azure.  Zależy od wdrożonego rozwiązania magazynu docelowego, proces różni się nieznacznie. Kroki wysokiego poziomu opisano poniżej.

### <a name="vm-with-premiumultra-disks-for-data"></a>Maszyna wirtualna z opcją Premium/Ultra-disks dla danych
W przypadku maszyn wirtualnych, które są wdrażane z opcją Premium lub Ultra-disks, Standardowa konfiguracja replikacji systemu SAP HANA ma zastosowanie do konfigurowania HSR.  [Artykuł pomocy SAP](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.04/099caa1959ce4b3fa1144562fa09e163.html) zawiera przegląd kroków związanych z konfigurowaniem replikacji systemu, przejęciem przez system pomocniczy, powrót po awarii do podstawowego i wyłączanie replikacji systemu.  Na potrzeby migracji będzie potrzebna tylko instalacja, przejęcie i wyłączenie kroków replikacji.  

### <a name="vm-with-anf-for-data-and-log-volumes"></a>Maszyna wirtualna z ANF dla woluminów danych i dzienników
Na wysokim poziomie większość migawek magazynów pełnych danych i dzienników należy skopiować do usługi Azure Storage, gdzie są one dostępne i odzyskiwane przez docelową maszynę wirtualną HANA.  Proces kopiowania można wykonać przy użyciu wszystkich natywnych narzędzi do kopiowania systemu Linux.  

> [!IMPORTANT]
> Kopiowanie i transfer danych może zająć kilka godzin, zależnie od rozmiaru bazy danych HANA i przepustowości sieci.  Większość procesu kopiowania należy wykonać z wyprzedzeniem pierwszego przestoju bazy danych HANA.

### <a name="mcos-to-mdc-conversion"></a>Konwersja MCOS na MDC
Wiele składników w jednym modelu wdrożenia systemu (MCOS) użyła niektórych z naszych klientów.  Motywacja była obejście ograniczenia migawki magazynu wielu baz danych (MDC) wcześniejszych wersji SAP HANA.  W modelu MCOS kilka niezależnych wystąpień SAP HANA jest ułożone w jeden blok.  Użycie HSR do migracji będzie działało prawidłowo, ale w przypadku wielu maszyn wirtualnych HANA z jedną bazą danych dzierżawy każdy.  Ten stan końcowy jest przeznaczony dla busier krajobrazu niż w przypadku, gdy klient mógł zostać przyzwyczajony do.  W SAP HANA przypadku MDC 2,0 wdrożenie domyślne jest zdolne do przeprowadzenia [przeniesienia dzierżawy Hana](https://launchpad.support.sap.com/#/notes/2472478) po migracji HSR.  Ten proces "konsoliduje" te niezależne bazy danych HANA do współdzierżawców w jednym kontenerze HANA.  

### <a name="application-layer-consideration"></a>Rozważenia warstwy aplikacji
Serwer bazy danych jest wyświetlany jako środek systemu SAP.  Wszystkie serwery aplikacji powinny znajdować się w sąsiedztwie SAP HANA DB.  W niektórych przypadkach, gdy wymagane jest nowe zastosowanie PPG, przeniesienie istniejących serwerów aplikacji na PPG, gdzie może być wymagana maszyna wirtualna HANA.  Tworzenie nowych serwerów aplikacji może być uznawane za łatwiejsze, jeśli masz już szablony wdrażania.  
Jeśli istniejące serwery aplikacji i Nowa maszyna wirtualna HANA są optymalnie zlokalizowane, nie trzeba kompilować nowych serwerów aplikacji, o ile nie jest wymagana dodatkowa pojemność.  
W przypadku skompilowania nowej infrastruktury w celu zwiększenia dostępności usługi istniejące serwery aplikacji mogą stać się niepotrzebne i powinny być wyłączone i usunięte.
Jeśli docelowa nazwa hosta maszyny wirtualnej uległa zmianie i różni się od nazwy hosta protokołu HLI, profile serwera aplikacji SAP należy dostosować, aby wskazywały na nowy host.  Jeśli tylko adres IP bazy danych HANA zostanie zmieniony, do realizacji połączeń przychodzących z nową maszyną wirtualną HANA jest wymagana aktualizacja rekordu DNS.

### <a name="acceptance-test"></a>Test akceptacji
Mimo że migracja z programu HLI do maszyny wirtualnej nie wprowadza żadnych istotnych zmian w zawartości bazy danych w porównaniu do migracji heterogenicznej, nadal zalecamy sprawdzenie poprawności kluczowych funkcji i wydajności nowego Instalatora.  

### <a name="cutover-plan"></a>Plan uruchomienie produkcyjne
Mimo że migracja jest prosta w przód, obejmuje jednak likwidację istniejącej bazy danych.  Należy uważnie zaplanować zachowanie systemu źródłowego przy użyciu skojarzonej zawartości, a obrazy kopii zapasowych mają kluczowe znaczenie w przypadku konieczności powrotu do wielkości liter.  Dobrym planowaniem jest zaoferowanie odwrócenia speedier.   


## <a name="post-migration"></a>Zadania po migracji
Zadanie migracji nie zostanie wykonane, dopóki nie zostanie bezpiecznie oddzielona żadna usługa lub łączność zależna od usługi HLI w celu zapewnienia zachowania integralności danych.  Ponadto Zamknij niepotrzebne usługi.  Ta sekcja wywołuje kilka najważniejszych elementów.

### <a name="decommissioning-the-hli"></a>Likwidowanie
Po pomyślnej migracji bazy danych HANA do maszyny wirtualnej platformy Azure upewnij się, że żadne produktywne transakcje biznesowe nie są uruchamiane w bazie danych.  Niemniej jednak przechowywanie wartości w danym okresie jest równe w lokalnym oknie przechowywania kopii zapasowej, jeśli jest to konieczne, zapewniając bezpieczne rozwiązanie do odzyskiwania speedier.  Tylko wtedy powinien zostać zlikwidowany blok HLI.  Klienci powinni w umowie zawrzeć swoje zobowiązania z firmą Microsoft, kontaktując się z przedstawicielami firmy Microsoft.

### <a name="remove-any-proxy-ex-iptables-bigip-configured-for-hli"></a>Usuń wszystkie serwery proxy (np. dołączenie iptables, BIGIP) skonfigurowane dla elementu HLI 
Jeśli usługa serwera proxy, taka jak dołączenie iptables, jest używana do kierowania ruchu lokalnego do i z elementu HLI, nie jest już wymagana po pomyślnej migracji do maszyny wirtualnej.  Jednak ta usługa łączności powinna być przechowywana przez cały czas, w którym blok.  Zamknij usługę tylko po całkowitym zlikwidowaniu bloku HLI.

### <a name="remove-global-reach-for-hli"></a>Usuń Global Reach dla elementu HLI 
Global Reach służy do łączenia bramy ExpressRoutei klientów z bramą ExpressRoute.  Pozwala on na ruch lokalny klientów, aby uzyskać dostęp do dzierżawy usługi HLI bezpośrednio bez korzystania z usługi proxy.  To połączenie nie jest już potrzebne w przypadku braku jednostki HLI po migracji.  Podobnie jak w przypadku usługi dołączenie iptables proxy, GlobalReach powinien być również utrzymany do momentu całkowitego zlikwidowania bloku.

### <a name="operating-system-subscription--movereuse"></a>Subskrypcja systemu operacyjnego — przenoszenie/ponowne używanie
Ponieważ serwery maszyn wirtualnych są postawiliśmy i są likwidowane, subskrypcje systemu operacyjnego mogą zostać zastąpione lub użyte ponownie w celu uniknięcia podwójnego płacenia licencji systemu operacyjnego.



## <a name="next-steps"></a>Następne kroki
Zobacz następujące artykuły:
- [SAP HANA konfiguracje i operacje infrastruktury na platformie Azure](./hana-vm-operations.md).
- [Obciążenia SAP na platformie Azure: planowanie i wdrażanie listy kontrolnej](./sap-deployment-checklist.md).
