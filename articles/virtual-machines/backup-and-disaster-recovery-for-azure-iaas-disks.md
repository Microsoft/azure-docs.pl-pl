---
title: Tworzenie kopii zapasowych i odzyskiwanie po awarii dla dysków IaaS na maszynach wirtualnych platformy Azure
description: W tym artykule opisano sposób planowania tworzenia kopii zapasowych i odzyskiwania po awarii maszyn wirtualnych IaaS i dysków na platformie Azure. Ten dokument dotyczy zarówno dysków zarządzanych, jak i niezarządzanych.
author: roygara
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 07/19/2017
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: e5ae08c23748e55a8c3b75eb8fb9c112684f022e
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102507910"
---
# <a name="backup-and-disaster-recovery-for-azure-iaas-disks"></a>Tworzenie kopii zapasowych i odzyskiwanie po awarii dla dysków usługi Azure IaaS

W tym artykule wyjaśniono, jak planować tworzenie kopii zapasowych i odzyskiwanie po awarii (DR) maszyn wirtualnych IaaS i dysków na platformie Azure. Ten dokument dotyczy zarówno dysków zarządzanych, jak i niezarządzanych.

Najpierw omówiono wbudowane możliwości odporności na uszkodzenia na platformie Azure, które pomagają chronić przed awariami lokalnymi. Następnie omawiamy scenariusze awaryjne, które nie są w pełni objęte wbudowanymi funkcjami. Przedstawiono również kilka przykładów scenariuszy obciążeń, w których można zastosować różne zagadnienia dotyczące kopii zapasowych i odzyskiwania po awarii. Następnie analizujemy możliwe rozwiązania dla dysków odzyskiwania po awarii IaaS.

## <a name="introduction"></a>Wprowadzenie

Platforma Azure korzysta z różnych metod nadmiarowości i odporności na uszkodzenia, aby pomóc w ochronie klientów przed zlokalizowanymi awariami sprzętowymi. Awarie lokalne mogą obejmować problemy z maszyną serwera usługi Azure Storage, która przechowuje część danych dla dysku wirtualnego lub uszkodzenie dysków SSD lub dysk twardy na tym serwerze. Awarie takich izolowanych składników sprzętowych mogą wystąpić podczas normalnych operacji.

Platforma Azure została zaprojektowana tak, aby była odporna na awarie. Główne awarie mogą skutkować niepowodzeniem lub niedostępnością wielu serwerów magazynu, a nawet całego centrum danych. Mimo że maszyny wirtualne i dyski są zwykle chronione przed zlokalizowanymi awariami, konieczne jest wykonanie dodatkowych kroków w celu ochrony obciążenia przed uszkodzeniem całego regionu, na przykład w przypadku poważnych awarii, które mogą mieć wpływ na maszynę wirtualną i dyski.

Oprócz możliwości awarii platformy mogą wystąpić problemy z aplikacją klienta lub danymi. Na przykład Nowa wersja aplikacji może przypadkowo wprowadzić zmiany w danych, które powodują jego przerwanie. W takim przypadku możesz chcieć przywrócić aplikację i dane do wcześniejszej wersji, która zawiera ostatni znany dobry stan. Wymaga to regularnego wykonywania kopii zapasowych.

W przypadku regionalnego odzyskiwania po awarii należy utworzyć kopię zapasową dysków maszyny wirtualnej IaaS w innym regionie.

Zanim zaczniemy korzystać z opcji tworzenia kopii zapasowych i odzyskiwania po awarii, podsumowanie kilka metod obsługi zlokalizowanych błędów.

### <a name="azure-iaas-resiliency"></a>Odporność na usługę Azure IaaS

*Odporność* to tolerancja dla normalnych błędów występujących w składnikach sprzętowych. Odporność to zdolność do odzyskiwania po awarii i kontynuowania działania. Nie dotyczy to unikania awarii, ale odpowiadania na awarie w taki sposób, który pozwala uniknąć przestoju lub utraty danych. Celem odporności jest przywrócenie aplikacji do stanu pełnej funkcjonalności po wystąpieniu awarii. Usługi Azure Virtual Machines i disks zostały zaprojektowane z myślą o odporności na typowe błędy sprzętu. Przyjrzyjmy się, jak platforma Azure IaaS zapewnia tę odporność.

Maszyna wirtualna składa się głównie z dwóch części: serwera obliczeniowego i dysków trwałych. Oba mają wpływ na odporność na uszkodzenia maszyny wirtualnej.

Jeśli serwer hosta obliczeniowego platformy Azure, na którym znajduje się maszyna wirtualna, napotyka awarię sprzętową, co jest rzadkie, platforma Azure została zaprojektowana w celu automatycznego przywrócenia maszyny wirtualnej na innym serwerze. Jeśli ten scenariusz, komputer zostanie ponownie uruchomiony, a maszyna wirtualna zostanie połączona za jakiś czas. Platforma Azure automatycznie wykrywa takie awarie sprzętowe i wykonuje odzyskiwanie w celu zapewnienia, że maszyna wirtualna klienta jest dostępna tak szybko, jak to możliwe.

W odniesieniu do dysków IaaS trwałość danych ma kluczowe znaczenie dla trwałej platformy magazynu. Klienci platformy Azure mają ważne aplikacje biznesowe działające w IaaS i zależą od trwałości danych. Ochrona projektów platformy Azure dla tych dysków IaaS z trzema nadmiarowymi kopiami danych, które są przechowywane lokalnie. Te kopie zapewniają wysoką trwałość przed awariami lokalnymi. Jeśli jeden ze składników sprzętowych, w którym znajduje się dysk, ulegnie awarii, nie ma to żadnego oddziaływania, ponieważ do obsługi żądań dysku są dostępne dwie dodatkowe kopie. Działa prawidłowo, nawet jeśli dwa różne składniki sprzętowe obsługujące dysk nie powiodą się w tym samym czasie (co jest rzadki). 

Aby zapewnić, że zawsze są zachowywane trzy repliki, usługa Azure Storage automatycznie duplikuje nową kopię danych w tle, jeśli jedna z trzech kopii będzie niedostępna. W związku z tym nie powinno być konieczne używanie macierzy RAID z dyskami platformy Azure w celu odporności na uszkodzenia. Prosta konfiguracja RAID 0 powinna być wystarczająca do rozłożenia dysków, w razie potrzeby, do tworzenia większych woluminów.

Ze względu na tę architekturę platforma Azure stale dostarcza trwałość klasy korporacyjnej dla dysków IaaS z wiodącym w branży [stawką niepowodzeń](https://en.wikipedia.org/wiki/Annualized_failure_rate)o zerowej skali.

Zlokalizowane błędy sprzętu na hoście obliczeniowym lub na platformie magazynu mogą czasami spowodować tymczasową niedostępność maszyny wirtualnej, która jest objęta umową [SLA platformy Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/) na potrzeby dostępności maszyny wirtualnej. Platforma Azure oferuje również wiodącą w branży umowę SLA dotyczącą pojedynczych wystąpień maszyn wirtualnych, które korzystają z usługi Azure Premium dysków SSD.

Aby chronić obciążenia aplikacji przed przestojem z powodu tymczasowej niedostępności dysku lub maszyny wirtualnej, klienci mogą korzystać z [zestawów dostępności](./availability.md). Co najmniej dwie maszyny wirtualne w zestawie dostępności zapewniają nadmiarowość aplikacji. Następnie platforma Azure tworzy te maszyny wirtualne i dyski w osobnych domenach błędów z różnymi składnikami usług, sieci i serwerów.

Ze względu na te oddzielne domeny błędów zlokalizowane awarie sprzętu zazwyczaj nie mają wpływu na wiele maszyn wirtualnych w zestawie w tym samym czasie. Posiadanie oddzielnych domen błędów zapewnia wysoką dostępność aplikacji. Jest to dobre rozwiązanie, aby korzystać z zestawów dostępności, gdy wymagana jest wysoka dostępność. W następnej sekcji omówiono aspekt odzyskiwania po awarii.

### <a name="backup-and-disaster-recovery"></a>Tworzenie kopii zapasowych i odzyskiwanie po awarii

Odzyskiwanie po awarii to możliwość odzyskania danych z rzadkich, ale głównych, zdarzeń. Zdarzenia te obejmują nieprzejściowe, szerokie błędy skalowania, takie jak zakłócenia usługi, które mają wpływ na cały region. Odzyskiwanie awaryjne obejmuje tworzenie kopii zapasowych i archiwizowanie danych oraz może obejmować interwencję ręczną, taką jak przywracanie bazy danych z kopii zapasowej.

Wbudowana funkcja ochrony platformy Azure dla zlokalizowanych awarii może nie w pełni chronić maszyn wirtualnych/dysków, jeśli główna awaria powoduje wystąpienie przestoju na dużą skalę. Takie krótkoterminowe przestoje obejmują zdarzenia katastrofalne, na przykład, jeśli centrum danych jest osiągane przez huragan, trzęsienie ziemi, ogień lub w przypadku awarii jednostki sprzętowej o dużej skali. Ponadto mogą wystąpić błędy spowodowane problemami z aplikacją lub danymi.

Aby pomóc w ochronie obciążeń IaaS od awarii, należy zaplanować nadmiarowość i utworzyć kopie zapasowe w celu umożliwienia odzyskiwania. W przypadku odzyskiwania po awarii należy utworzyć kopię zapasową w innej lokalizacji geograficznej w lokacji głównej. Takie podejście pomaga zapewnić, że kopia zapasowa nie ma wpływ na to samo zdarzenie, które pierwotnie wpłynęło na maszynę wirtualną lub dyski. Aby uzyskać więcej informacji, zobacz [odzyskiwanie po awarii dla aplikacji platformy Azure](/azure/architecture/resiliency/disaster-recovery-azure-applications).

Zagadnienia dotyczące odzyskiwania po awarii mogą obejmować następujące aspekty:

- Wysoka dostępność: zdolność aplikacji do kontynuowania działania w dobrej kondycji bez znaczących przestojów. W *dobrej kondycji* ten stan oznacza, że aplikacja jest w stanie reakcji, a użytkownicy mogą łączyć się z aplikacją i korzystać z niej. Niektóre aplikacje i bazy danych o kluczowym znaczeniu mogą być zawsze dostępne, nawet w przypadku awarii na platformie. W przypadku tych obciążeń może być konieczne zaplanowanie nadmiarowości aplikacji, a także danych.

- Trwałość danych: w niektórych przypadkach głównym zagadnieniem jest upewnienie się, że dane są zachowywane w przypadku awarii. W związku z tym może być wymagana kopia zapasowa danych w innej lokacji. W przypadku takich obciążeń może nie być potrzebna pełna nadmiarowość dla aplikacji, ale tylko zwykła kopia zapasowa dysków.

## <a name="backup-and-dr-scenarios"></a>Scenariusze tworzenia kopii zapasowych i odzyskiwania po awarii

Zapoznaj się z kilkoma Typowymi przykładami scenariuszy obciążeń aplikacji i zagadnieniami dotyczącymi planowania odzyskiwania po awarii.

### <a name="scenario-1-major-database-solutions"></a>Scenariusz 1: główne rozwiązania baz danych

Należy wziąć pod uwagę produkcyjny serwer bazy danych, taki jak SQL Server lub Oracle, który może obsługiwać wysoką dostępność. Najważniejsze aplikacje produkcyjne i użytkownicy zależą od tej bazy danych. Plan odzyskiwania po awarii dla tego systemu może wymagać obsługi następujących wymagań:

- Dane muszą być chronione i możliwe do odzyskania.
- Serwer musi być dostępny do użycia.

Plan odzyskiwania po awarii może wymagać utrzymania repliki bazy danych w innym regionie jako kopia zapasowa. W zależności od wymagań dotyczących dostępności serwera i odzyskiwania danych rozwiązanie może być przedziałem od aktywnej aktywnej lub aktywnej pasywnej lokacji repliki do okresowego wykonywania kopii zapasowych w trybie offline danych. Relacyjne bazy danych, takie jak SQL Server i Oracle, zapewniają różne opcje replikacji. Aby uzyskać SQL Server, użyj [SQL Server zawsze włączone grupy dostępności](/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server) w celu zapewnienia wysokiej dostępności.

Bazy danych NoSQL, takie jak MongoDB, obsługują również [repliki](https://docs.mongodb.com/manual/replication/) w celu zapewnienia nadmiarowości. Używane są repliki w celu zapewnienia wysokiej dostępności.

### <a name="scenario-2-a-cluster-of-redundant-vms"></a>Scenariusz 2: klaster nadmiarowych maszyn wirtualnych

Weź pod uwagę obciążenie obsługiwane przez klaster maszyn wirtualnych, które zapewniają nadmiarowość i równoważenie obciążenia. Przykładem jest klaster Cassandra wdrożony w regionie. Ten typ architektury już zapewnia wysoki poziom nadmiarowości w tym regionie. Aby jednak chronić obciążenia przed awarią regionalną, należy rozważyć rozproszenie klastra między dwoma regionami lub regularne tworzenie kopii zapasowych w innym regionie.

### <a name="scenario-3-iaas-application-workload"></a>Scenariusz 3: IaaS obciążenia aplikacji

Przyjrzyjmy się obciążeniu aplikacji IaaS. Na przykład ta aplikacja może być typowym obciążeniem produkcyjnym działającym na maszynie wirtualnej platformy Azure. Może to być serwer sieci Web lub serwer plików przechowujący zawartość i inne zasoby witryny. Może to być również niestandardowa aplikacja biznesowa działająca na maszynie wirtualnej, która przechowuje dane, zasoby i stan aplikacji na dyskach maszyn wirtualnych. W takim przypadku ważne jest, aby upewnić się, że kopie zapasowe są wykonywane regularnie. Częstotliwość tworzenia kopii zapasowych powinna być oparta na charakterze obciążenia maszyny wirtualnej. Jeśli na przykład aplikacja jest uruchamiana codziennie i modyfikuje dane, kopia zapasowa powinna być wykonywana co godzinę.

Innym przykładem jest serwer raportowania, który pobiera dane z innych źródeł i generuje zagregowane raporty. Utrata tej maszyny wirtualnej lub dysków może prowadzić do utraty raportów. Jednak może być możliwe ponowne uruchomienie procesu raportowania i ponowne wygenerowanie danych wyjściowych. W takim przypadku nie masz naprawdę utraty danych, nawet jeśli serwer raportowania został trafiony na awarię. W związku z tym może mieć wyższy poziom tolerancji dla utraty części danych na serwerze raportowania. W takim przypadku rzadziej występujące kopie zapasowe są opcją obniżenia kosztów.

### <a name="scenario-4-iaas-application-data-issues"></a>Scenariusz 4: IaaS problemy z danymi aplikacji

Problemy z danymi aplikacji IaaS są kolejną możliwością. Rozważmy aplikację, która oblicza, utrzymuje i obsługuje krytyczne dane handlowe, takie jak informacje o cenach. Nowa wersja aplikacji ma usterkę oprogramowania, która nieprawidłowo przeliczyła Cennik i zarejestrował istniejące dane handlowe obsługiwane przez platformę. W tym miejscu najlepszym sposobem działania jest przywrócenie wcześniejszej wersji aplikacji i danych. Aby to umożliwić, należy okresowo tworzyć kopie zapasowe systemu.

## <a name="disaster-recovery-solution-azure-backup"></a>Rozwiązanie odzyskiwania po awarii: Azure Backup 

[Azure Backup](https://azure.microsoft.com/services/backup/) jest używany do tworzenia kopii zapasowych i odzyskiwania po awarii oraz dysków [zarządzanych](managed-disks-overview.md) . Można utworzyć zadanie tworzenia kopii zapasowej z kopiami zapasowymi opartymi na czasie, łatwym przywracaniem maszyn wirtualnych i zasadami przechowywania kopii zapasowych.

Jeśli używasz [dysków SSD Premium](disks-types.md), [Managed disks](managed-disks-overview.md)lub innych typów dysków z opcją [Magazyn lokalnie nadmiarowy](../storage/common/storage-redundancy.md#locally-redundant-storage) , warto szczególnie pamiętać, aby okresowo wykonywać kopie zapasowe Dr. Azure Backup przechowuje dane w magazynie usługi Recovery Services w celu długoterminowego przechowywania. Wybierz opcję [magazynu geograficznie nadmiarowego](../storage/common/storage-redundancy.md#geo-redundant-storage) dla magazynu usługi Backup Recovery Services. Ta opcja zapewnia, że kopie zapasowe są replikowane do innego regionu platformy Azure w celu ochrony przed awariami regionalnymi.

W przypadku dysków niezarządzanych można użyć lokalnie nadmiarowego typu magazynu dla dysków IaaS, ale upewnij się, że Azure Backup jest włączona z opcją magazynu geograficznie nadmiarowego dla magazynu usługi Recovery Services.

> [!NOTE]
> W przypadku korzystania z [magazynu geograficznie nadmiarowego](../storage/common/storage-redundancy.md#geo-redundant-storage) lub [magazynu geograficznie nadmiarowego do odczytu](../storage/common/storage-redundancy.md#read-access-to-data-in-the-secondary-region)  dla dysków niezarządzanych nadal potrzebne są spójne migawki na potrzeby tworzenia kopii zapasowych i odzyskiwania po awarii. Użyj zarówno [Azure Backup](https://azure.microsoft.com/services/backup/) , jak i [spójnych migawek](#alternative-solution-consistent-snapshots).

 Poniższa tabela zawiera podsumowanie rozwiązań dostępnych na potrzeby odzyskiwania po awarii.

| Scenariusz | Automatyczna replikacja | Rozwiązanie DR |
| --- | --- | --- |
| Dyski SSD w warstwie Premium | Lokalna ([Magazyn lokalnie nadmiarowy](../storage/common/storage-redundancy.md#locally-redundant-storage)) | [Azure Backup](https://azure.microsoft.com/services/backup/) |
| Dyski zarządzane | Lokalna ([Magazyn lokalnie nadmiarowy](../storage/common/storage-redundancy.md#locally-redundant-storage)) | [Azure Backup](https://azure.microsoft.com/services/backup/) |
| Niezarządzane lokalnie nadmiarowe dyski magazynu | Lokalna ([Magazyn lokalnie nadmiarowy](../storage/common/storage-redundancy.md#locally-redundant-storage)) | [Azure Backup](https://azure.microsoft.com/services/backup/) |
| Niezarządzane geograficznie nadmiarowe dyski magazynu | Między regionami ([Magazyn Geograficznie nadmiarowy](../storage/common/storage-redundancy.md#geo-redundant-storage)) | [Azure Backup](https://azure.microsoft.com/services/backup/)<br/>[Spójne migawki](#alternative-solution-consistent-snapshots) |
| Niezarządzane dyski magazynu geograficznie nadmiarowego do odczytu | Między regionami ([Magazyn Geograficznie nadmiarowy do odczytu](../storage/common/storage-redundancy.md#read-access-to-data-in-the-secondary-region)) | [Azure Backup](https://azure.microsoft.com/services/backup/)<br/>[Spójne migawki](#alternative-solution-consistent-snapshots) |

Wysoka dostępność jest Najlepsza w przypadku korzystania z dysków zarządzanych w zestawie dostępności wraz z Azure Backup. W przypadku korzystania z dysków niezarządzanych można nadal używać Azure Backup do odzyskiwania po awarii. Jeśli nie możesz użyć Azure Backup, a następnie pobierając [spójne migawki](#alternative-solution-consistent-snapshots), zgodnie z opisem w dalszej części, to alternatywne rozwiązanie do tworzenia kopii zapasowych i odzyskiwania po awarii.

Opcje wysokiej dostępności, tworzenia kopii zapasowych i odzyskiwania po awarii na poziomie aplikacji lub infrastruktury mogą być reprezentowane w następujący sposób:

| Poziom |   Wysoka dostępność   | Kopia zapasowa lub odzyskiwanie po awarii |
| --- | --- | --- |
| Aplikacja | SQL Server AlwaysOn | Azure Backup |
| Infrastruktura    | Zestaw dostępności  | Magazyn Geograficznie nadmiarowy ze spójnymi migawkami |

### <a name="using-azure-backup"></a>Korzystanie z usługi Azure Backup 

[Azure Backup](../backup/backup-azure-vms-introduction.md) może tworzyć kopie zapasowe maszyn wirtualnych z systemem Windows lub Linux do magazynu usługi Azure Recovery Services. Tworzenie kopii zapasowych i przywracanie danych o kluczowym znaczeniu dla firmy jest skomplikowane przez fakt, że należy utworzyć kopię zapasową danych o krytycznym znaczeniu dla firmy, gdy aplikacje generujące dane są uruchomione. 

Aby rozwiązać ten problem, Azure Backup zapewnia spójne z aplikacjami kopie zapasowe dla obciążeń firmy Microsoft. Używa usługi woluminów w tle, aby upewnić się, że dane są poprawnie zapisywane do magazynu. W przypadku maszyn wirtualnych z systemem Linux domyślnym trybem spójności kopii zapasowej są kopie zapasowe spójne z plikami, ponieważ system Linux nie ma funkcji równorzędnej usługi woluminu w tle, tak jak w przypadku systemu Windows. W przypadku maszyn z systemem Linux zapoznaj się z tematem [Tworzenie kopii zapasowych maszyn wirtualnych systemu Linux na poziomie aplikacji](../backup/backup-azure-linux-app-consistent.md)

![Przepływ Azure Backup][1]

Gdy Azure Backup inicjuje zadanie tworzenia kopii zapasowej w zaplanowanym czasie, wyzwala rozszerzenie kopii zapasowej zainstalowane na maszynie wirtualnej, aby wykonać migawkę do punktu w czasie. Migawka jest podejmowana w ramach koordynacji z usługą woluminów w tle w celu uzyskania spójnej migawki dysków w maszynie wirtualnej bez konieczności jej zamykania. Rozszerzenie kopii zapasowej na maszynie wirtualnej opróżnia wszystkie zapisy przed podjęciem spójnej migawki wszystkich dysków. Po wykonaniu migawki dane są przesyłane przez Azure Backup do magazynu kopii zapasowych. Aby zapewnić wydajniejszy proces tworzenia kopii zapasowej, usługa zidentyfikuje i przesyła tylko te bloki danych, które uległy zmianie po utworzeniu ostatniej kopii zapasowej.

Aby przywrócić, można wyświetlić dostępne kopie zapasowe za pomocą Azure Backup a następnie zainicjować przywracanie. Kopie zapasowe platformy Azure można tworzyć i przywracać za pośrednictwem [Azure Portal](https://portal.azure.com/)przy [użyciu programu PowerShell](../backup/backup-azure-vms-automation.md)lub [interfejsu wiersza polecenia platformy Azure](/cli/azure/).

### <a name="steps-to-enable-a-backup"></a>Procedura włączania kopii zapasowej

Wykonaj następujące kroki, aby włączyć tworzenie kopii zapasowych maszyn wirtualnych przy użyciu [Azure Portal](https://portal.azure.com/). Istnieje kilka odmian w zależności od konkretnego scenariusza. Szczegółowe informacje znajdują się w dokumentacji [Azure Backup](../backup/backup-azure-vms-introduction.md) . Azure Backup [obsługuje również maszyny wirtualne z dyskami zarządzanymi](https://azure.microsoft.com/blog/azure-managed-disk-backup/).

1.  Utwórz magazyn usługi Recovery Services dla maszyny wirtualnej:

    a. W [Azure Portal](https://portal.azure.com/)Przeglądaj **wszystkie zasoby** i Znajdź **Recovery Services magazyny**.

    b. W menu **magazyny Recovery Services** kliknij pozycję **Dodaj** i postępuj zgodnie z instrukcjami, aby utworzyć nowy magazyn w tym samym regionie co maszyna wirtualna. Na przykład jeśli maszyna wirtualna znajduje się w regionie zachodnie stany USA, wybierz pozycję zachodnie stany USA dla magazynu.

1.  Sprawdź replikację magazynu dla nowo utworzonego magazynu. Uzyskaj dostęp do magazynu w obszarze **magazyny Recovery Services** i przejdź do **Właściwości**  >  **kopia zapasowa**  >  **Aktualizacja** konfiguracji. Upewnij się, że opcja **Magazyn Geograficznie nadmiarowy** jest zaznaczona domyślnie. Ta opcja zapewnia, że magazyn zostanie automatycznie zreplikowany do dodatkowego centrum danych. Na przykład magazyn w regionie zachodnie stany USA jest automatycznie replikowany do regionu Wschodnie stany USA.

1.  Skonfiguruj zasady tworzenia kopii zapasowych i wybierz maszynę wirtualną z tego samego interfejsu użytkownika.

1.  Upewnij się, że na maszynie wirtualnej jest zainstalowany agent kopii zapasowych. Jeśli maszyna wirtualna jest tworzona przy użyciu obrazu galerii platformy Azure, Agent kopii zapasowej jest już zainstalowany. W przeciwnym razie (w przypadku korzystania z obrazu niestandardowego) Użyj instrukcji, aby [zainstalować agenta maszyny wirtualnej na maszynę wirtualną](../backup/backup-azure-arm-vms-prepare.md#install-the-vm-agent).

1.  Po wykonaniu poprzednich kroków kopia zapasowa jest uruchamiana w regularnych odstępach czasu zgodnie z zasadami tworzenia kopii zapasowych. W razie potrzeby można wyzwolić pierwszą kopię zapasową ręcznie z poziomu pulpitu nawigacyjnego magazynu na Azure Portal.

Aby zautomatyzować Azure Backup za pomocą skryptów, zapoznaj się z [poleceniami cmdlet programu PowerShell dotyczącymi tworzenia kopii zapasowych maszyn wirtualnych](../backup/backup-azure-vms-automation.md).

### <a name="steps-for-recovery"></a>Kroki odzyskiwania

Jeśli musisz naprawić lub ponownie skompilować maszynę wirtualną, możesz przywrócić maszynę wirtualną z dowolnego punktu odzyskiwania kopii zapasowej w magazynie. Istnieje kilka różnych opcji wykonywania odzyskiwania:

-   Nową maszynę wirtualną można utworzyć jako reprezentację punktu w czasie kopii zapasowej maszyny wirtualnej.

-   Można przywrócić dyski, a następnie użyć szablonu dla maszyny wirtualnej w celu dostosowania i odbudowania przywróconej maszyny wirtualnej.

Aby uzyskać więcej informacji, zobacz instrukcje dotyczące [przywracania maszyn wirtualnych przy użyciu Azure Portal](../backup/backup-azure-arm-restore-vms.md). W tym dokumencie wyjaśniono również określone kroki przywracania kopii zapasowych maszyn wirtualnych do sparowanego centrum danych przy użyciu geograficznie nadmiarowego magazynu kopii zapasowych w przypadku awarii w podstawowym centrum danych. W takim przypadku Azure Backup używa usługi obliczeniowej z regionu pomocniczego do utworzenia przywróconej maszyny wirtualnej.

Można również użyć programu PowerShell do [tworzenia nowej maszyny wirtualnej z przywróconych dysków](../backup/backup-azure-vms-automation.md#create-a-vm-from-restored-disks).

## <a name="alternative-solution-consistent-snapshots"></a>Rozwiązanie alternatywne: spójne migawki

Jeśli nie można użyć Azure Backup, można zaimplementować własny mechanizm tworzenia kopii zapasowych za pomocą migawek. Tworzenie spójnych migawek dla wszystkich dysków używanych przez maszynę wirtualną, a następnie replikowanie tych migawek do innego regionu jest skomplikowane. Z tego powodu platforma Azure rozważa użycie usługi Backup jako lepszej opcji niż tworzenie niestandardowego rozwiązania.

W przypadku korzystania z magazynu geograficznie nadmiarowego dostępnego do odczytu/magazynu geograficznie nadmiarowego dla dysków migawki są automatycznie replikowane do dodatkowego centrum danych. W przypadku używania lokalnie nadmiarowego magazynu dla dysków należy samodzielnie zreplikować dane. Aby uzyskać więcej informacji, zobacz [Tworzenie kopii zapasowych dysków maszyn wirtualnych niezarządzanych przez platformę Azure przy użyciu migawek przyrostowych](windows/incremental-snapshots.md).

Migawka to reprezentacja obiektu w określonym punkcie w czasie. Migawka wiąże się z rozliczeniem przyrostowego rozmiaru danych, które przechowuje. Aby uzyskać więcej informacji, zobacz [Tworzenie migawki obiektu BLOB](../storage/blobs/snapshots-overview.md).

### <a name="create-snapshots-while-the-vm-is-running"></a>Tworzenie migawek podczas działania maszyny wirtualnej

Chociaż można wykonać migawkę w dowolnym momencie, jeśli maszyna wirtualna jest uruchomiona, nadal dane są przesyłane strumieniowo do dysków. Migawki mogą zawierać operacje częściowe, które były w locie. Ponadto, jeśli istnieje kilka dysków, migawki różnych dysków mogą wystąpić w różnych godzinach. Te scenariusze mogą spowodować niekoordynowanie migawek. Brak koordynacji jest szczególnie problematyczny dla woluminów rozłożonych, których pliki mogą być uszkodzone, jeśli podczas tworzenia kopii zapasowej zostały wprowadzone zmiany.

Aby uniknąć tej sytuacji, proces tworzenia kopii zapasowej musi implementować następujące czynności:

1.  Zablokuj wszystkie dyski.

1.  Opróżnij wszystkie oczekujące zapisy.

1.  [Utwórz migawkę obiektu BLOB](../storage/blobs/snapshots-manage-dotnet.md) dla wszystkich dysków.

Niektóre aplikacje systemu Windows, takie jak SQL Server, zapewniają skoordynowany mechanizm tworzenia kopii zapasowych za pośrednictwem usługi woluminów w tle do tworzenia kopii zapasowych spójnych na poziomie aplikacji. W systemie Linux można użyć narzędzia, takiego jak *fsfreeze* , aby koordynować dyski. To narzędzie zapewnia kopie zapasowe spójne z plikami, ale nie migawki spójne z aplikacjami. Ten proces jest skomplikowany, dlatego należy rozważyć użycie [Azure Backup](../backup/backup-azure-vms-introduction.md) lub rozwiązania do tworzenia kopii zapasowych innych firm, które implementuje już tę procedurę.

Poprzedni proces skutkuje kolekcją skoordynowanych migawek dla wszystkich dysków maszyn wirtualnych reprezentujących konkretny widok punktu w czasie dla maszyny wirtualnej. To jest punkt przywracania kopii zapasowej maszyny wirtualnej. Możesz powtórzyć ten proces w zaplanowanych odstępach czasu, aby utworzyć okresowe kopie zapasowe. Aby zapoznać się z procedurą kopiowania migawek do innego regionu na potrzeby odzyskiwania po awarii, zobacz [Kopiuj kopie zapasowe do innego regionu](#copy-the-snapshots-to-another-region) .

### <a name="create-snapshots-while-the-vm-is-offline"></a>Tworzenie migawek, gdy maszyna wirtualna jest w trybie offline

Kolejną opcją tworzenia spójnych kopii zapasowych jest zamknięcie maszyny wirtualnej i przejęcie migawek obiektów BLOB każdego dysku. Tworzenie migawek obiektów BLOB jest łatwiejsze niż koordynowanie migawek uruchomionej maszyny wirtualnej, ale wymaga to kilku minut przestoju.

1. Zamknij maszynę wirtualną.

1. Utwórz migawkę każdego obiektu BLOB wirtualnego dysku twardego, który zajmuje zaledwie kilka sekund.

    Aby utworzyć migawkę, można użyć [programu PowerShell](/powershell/module/az.storage), [interfejsu API REST usługi Azure Storage](/rest/api/storageservices/Snapshot-Blob)lub jednej z bibliotek klienta usługi Azure Storage, takich jak [Biblioteka klienta magazynu dla platformy .NET](/rest/api/storageservices/Creating-a-Snapshot-of-a-Blob). [](/cli/azure/)

1. Uruchom maszynę wirtualną, która skończy przestoje. Zwykle cały proces kończy się w ciągu kilku minut.

Ten proces daje kolekcję spójnych migawek dla wszystkich dysków, dostarczając punkt przywracania kopii zapasowej dla maszyny wirtualnej.

### <a name="copy-the-snapshots-to-another-region"></a>Kopiowanie migawek do innego regionu

Tworzenie migawek może być niewystarczające w przypadku odzyskiwania po awarii. Kopie zapasowe migawek należy również replikować do innego regionu.

Jeśli używasz magazynu geograficznie nadmiarowego lub magazynu geograficznie nadmiarowego do odczytu dla dysków, migawki są replikowane automatycznie do regionu pomocniczego. Przed replikacją może istnieć kilka minut opóźnienia. Jeśli główne centrum danych przestanie istnieć przed rozpoczęciem replikacji migawek, nie można uzyskać dostępu do migawek z dodatkowego centrum danych. Prawdopodobieństwo, że jest to małe.

> [!NOTE]
> Tylko dyski w magazynie geograficznie nadmiarowym lub z geograficznie nadmiarowym dostępem do odczytu nie chronią maszyny wirtualnej przed awariami. Należy również utworzyć skoordynowane migawki lub użyć Azure Backup. Jest to wymagane do odzyskania spójnego stanu maszyny wirtualnej.

W przypadku korzystania z magazynu lokalnie nadmiarowego należy skopiować migawki na inne konto magazynu natychmiast po utworzeniu migawki. Celem kopiowania może być konto magazynu lokalnie nadmiarowego w innym regionie, co spowoduje, że kopia znajduje się w regionie zdalnym. Możesz również skopiować migawkę do konta magazynu geograficznie nadmiarowego do odczytu w tym samym regionie. W takim przypadku migawka jest opóźnieniem replikowana do zdalnego regionu pomocniczego. Kopia zapasowa jest chroniona przed awariami w lokacji głównej po zakończeniu kopiowania i replikacji.

Aby efektywnie kopiować przyrostowe migawki na potrzeby odzyskiwania po awarii, zapoznaj się z instrukcjami zawartymi w temacie [Tworzenie kopii zapasowych dysków maszyny wirtualnej niezarządzanych przez platformę Azure](windows/incremental-snapshots.md)

![Tworzenie kopii zapasowych niezarządzanych dysków maszyny wirtualnej platformy Azure przy użyciu migawek przyrostowych][2]

### <a name="recovery-from-snapshots"></a>Odzyskiwanie z migawek

Aby pobrać migawkę, skopiuj ją w celu utworzenia nowego obiektu BLOB. W przypadku kopiowania migawki z konta podstawowego można skopiować migawkę do podstawowego obiektu BLOB migawki. Ten proces powoduje przywrócenie dysku do migawki. Ten proces jest znany jako podwyższanie poziomu migawki. W przypadku kopiowania kopii zapasowej migawki z konta dodatkowego, w przypadku konta magazynu geograficznie nadmiarowego do odczytu, należy skopiować je na konto podstawowe. Migawkę można skopiować za [pomocą programu PowerShell](/powershell/module/az.storage) lub narzędzia AzCopy. Aby uzyskać więcej informacji, zobacz [transfer danych za pomocą narzędzia wiersza polecenia AzCopy](../storage/common/storage-use-azcopy-v10.md).

W przypadku maszyn wirtualnych z wieloma dyskami należy skopiować wszystkie migawki, które są częścią tego samego skoordynowanego punktu przywracania. Po skopiowaniu migawek do zapisywalnych obiektów BLOB wirtualnego dysku twardego można użyć obiektów BLOB do ponownego utworzenia maszyny wirtualnej przy użyciu szablonu dla maszyny wirtualnej.

## <a name="other-options"></a>Inne opcje

### <a name="sql-server"></a>SQL Server

SQL Server uruchomiony na maszynie wirtualnej ma wbudowane funkcje tworzenia kopii zapasowej bazy danych SQL Server w usłudze Azure Blob Storage lub w udziale plików. Jeśli konto magazynu jest magazynem geograficznie nadmiarowym lub magazyn Geograficznie nadmiarowy z dostępem do odczytu, możesz uzyskać dostęp do tych kopii zapasowych w dodatkowym centrum danych konta magazynu w przypadku awarii, z tymi samymi ograniczeniami, które zostały opisane wcześniej. Aby uzyskać więcej informacji, zobacz [wykonywanie kopii zapasowych i przywracanie SQL Server w usłudze Azure Virtual Machines](../azure-sql/virtual-machines/windows/azure-storage-sql-server-backup-restore-use.md). Oprócz tworzenia kopii zapasowych i przywracania, [SQL Server zawsze włączonych grup dostępności](../azure-sql/virtual-machines/windows/business-continuity-high-availability-disaster-recovery-hadr-overview.md) mogą obsługiwać pomocnicze repliki baz danych. Ta możliwość znacznie skraca czas odzyskiwania po awarii.

## <a name="other-considerations"></a>Inne zagadnienia

W tym artykule omówiono sposób tworzenia kopii zapasowych maszyn wirtualnych i ich dysków w celu zapewnienia obsługi odzyskiwania po awarii oraz używania tych kopii zapasowych i migawek w celu odzyskania danych. Dzięki modelowi Azure Resource Manager wiele osób używa szablonów do tworzenia maszyn wirtualnych i innych infrastruktury na platformie Azure. Możesz użyć szablonu, aby utworzyć maszynę wirtualną o tej samej konfiguracji za każdym razem. Jeśli używasz niestandardowych obrazów do tworzenia maszyn wirtualnych, musisz również upewnić się, że obrazy są chronione za pomocą konta magazynu geograficznie nadmiarowego do odczytu, aby je zapisać.

W związku z tym proces tworzenia kopii zapasowej może być kombinacją dwóch rzeczy:

- Wykonaj kopię zapasową danych (dysków).
- Wykonaj kopię zapasową konfiguracji (szablony i obrazy niestandardowe).

W zależności od wybranej opcji tworzenia kopii zapasowej może być konieczne obsługę kopii zapasowych zarówno danych, jak i konfiguracji. Usługa Backup może obsłużyć wszystkie te dane.

## <a name="appendix-understanding-the-impact-of-data-redundancy"></a>Dodatek: zrozumienie wpływu nadmiarowości danych

W przypadku kont magazynu na platformie Azure istnieją trzy typy nadmiarowości danych, które należy wziąć pod uwagę w przypadku odzyskiwania po awarii: lokalnie nadmiarowy, Geograficznie nadmiarowy lub geograficznie nadmiarowy z dostępem do odczytu. 

Magazyn lokalnie nadmiarowy przechowuje trzy kopie danych w tym samym centrum. Podczas zapisywania danych przez maszynę wirtualną wszystkie trzy kopie są aktualizowane przed zwróceniem do obiektu wywołującego, więc wiadomo, że są identyczne. Dysk jest chroniony przed awariami lokalnymi, ponieważ jest mało prawdopodobne, że wszystkie trzy kopie mają ten sam czas. W przypadku magazynu lokalnie nadmiarowego nie ma nadmiarowości geograficznej, więc dysk nie jest chroniony przed błędami, które mogą wpływać na całe centrum danych lub jednostkę magazynową.

Dzięki magazynowi geograficznie nadmiarowym i magazyn Geograficznie nadmiarowy z dostępem do odczytu trzy kopie danych są przechowywane w regionie podstawowym wybranym przez użytkownika. Trzy kopie danych są przechowywane w odpowiednim regionie pomocniczym ustawionym przez platformę Azure. Jeśli na przykład dane są przechowywane w zachodnich Stanach Zjednoczonych, dane są replikowane do regionu Wschodnie stany USA. Przechowywanie kopii jest wykonywane asynchronicznie i istnieje niewielkie opóźnienie między aktualizacjami lokacji głównej i dodatkowej. Repliki dysków w lokacji dodatkowej są spójne dla poszczególnych dysków (z opóźnieniem), ale repliki wielu aktywnych dysków mogą nie być zsynchronizowane ze sobą. Aby zapewnić spójne repliki na wielu dyskach, są konieczne spójne migawki.

Główną różnicą między magazynem geograficznie nadmiarowym i magazynem geograficznie nadmiarowym z dostępem do odczytu jest magazyn Geograficznie nadmiarowy z dostępem do odczytu, który można odczytać w dowolnym momencie. Jeśli wystąpi problem, który renderuje dane w regionie podstawowym niedostępnym, zespół platformy Azure podejmuje wszelkie wysiłki w celu przywrócenia dostępu. Gdy podstawowy program nie działa, jeśli jest włączony magazyn Geograficznie nadmiarowy z dostępem do odczytu, można uzyskać dostęp do danych w dodatkowym centrum. W związku z tym, jeśli planujesz odczytywać z repliki, gdy podstawowy jest niedostępny, należy rozważyć magazyn Geograficznie nadmiarowy dostępny do odczytu.

Jeśli okaże się to znaczna awaria, zespół platformy Azure może wyzwolić geograficzną pracę w trybie failover i zmienić podstawowe wpisy DNS, aby wskazywały magazyn pomocniczy. W tym momencie, jeśli masz magazyn Geograficznie nadmiarowy lub magazyn Geograficznie nadmiarowy dostępny do odczytu, możesz uzyskać dostęp do danych w regionie, który był używany jako pomocniczy. Innymi słowy, jeśli konto magazynu jest magazynem geograficznie nadmiarowym i występuje problem, można uzyskać dostęp do magazynu pomocniczego tylko wtedy, gdy istnieje geograficzna tryb failover.

Aby uzyskać więcej informacji, zobacz [Co zrobić po wystąpieniu awarii usługi Azure Storage](../storage/common/storage-disaster-recovery-guidance.md).

## <a name="next-steps"></a>Następne kroki

Zobacz [Tworzenie kopii zapasowych dysków maszyny wirtualnej niezarządzanych przez platformę Azure przy użyciu migawek przyrostowych](linux/incremental-snapshots.md).

[1]: ./media/virtual-machines-common-backup-and-disaster-recovery-for-azure-iaas-disks/backup-and-disaster-recovery-for-azure-iaas-disks-1.png
[2]: ./media/virtual-machines-common-backup-and-disaster-recovery-for-azure-iaas-disks/backup-and-disaster-recovery-for-azure-iaas-disks-2.png