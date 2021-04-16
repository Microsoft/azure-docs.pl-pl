---
title: Tabela obsługi dla maszyn wirtualnych platformy Azure
description: Zawiera podsumowanie ustawień pomocy technicznej i ograniczeń dotyczących kopii zapasowej maszyn wirtualnych platformy Azure za pomocą Azure Backup service.
ms.topic: conceptual
ms.date: 09/13/2019
ms.custom: references_regions
ms.openlocfilehash: 1f63d0c3ad448a8ab9b91764d4c369fefddea25d
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107516726"
---
# <a name="support-matrix-for-azure-vm-backup"></a>Tabela obsługi dla maszyn wirtualnych platformy Azure

Za pomocą usługi [Azure Backup można](backup-overview.md) back up on-premises machines and workloads, and Azure virtual machines (VMs) (Maszyny wirtualne platformy Azure). Ten artykuł zawiera podsumowanie ustawień pomocy technicznej i ograniczeń dotyczących kopii zapasowej maszyn wirtualnych platformy Azure przy użyciu Azure Backup.

Inne macierze obsługi:

- [Ogólna macierz obsługi](backup-support-matrix.md) dla Azure Backup
- [Macierz obsługi](backup-support-matrix-mabs-dpm.md) kopii Azure Backup kopii zapasowej serwera System Center Data Protection Manager (DPM)
- [Macierz obsługi kopii](backup-support-matrix-mars-agent.md) zapasowych za pomocą agenta Microsoft Azure Recovery Services (MARS)

## <a name="supported-scenarios"></a>Obsługiwane scenariusze

Oto jak można tworzyć kopię zapasową i przywracać maszyny wirtualne platformy Azure za pomocą Azure Backup service.

**Scenariusz** | **Tworzenie kopii zapasowych** | **Agenta** |**Przywracanie**
--- | --- | --- | ---
Bezpośrednie tworzenie kopii zapasowych maszyn wirtualnych platformy Azure  | Schowaj kopię zapasową całej maszyny wirtualnej.  | Na maszynie wirtualnej platformy Azure nie jest wymagany żaden dodatkowy agent. Azure Backup instaluje i używa rozszerzenia agenta maszyny wirtualnej platformy [Azure](../virtual-machines/extensions/agent-windows.md) uruchomionego na maszynie wirtualnej. | Przywróć w następujący sposób:<br/><br/> - **Utwórz podstawową maszynę wirtualną.** Jest to przydatne, jeśli maszyna wirtualna nie ma specjalnej konfiguracji, takiej jak wiele adresów IP.<br/><br/> - **Przywróć dysk maszyny wirtualnej.** Przywróć dysk. Następnie dołącz ją do istniejącej maszyny wirtualnej lub utwórz nową maszynę wirtualną na dysku przy użyciu programu PowerShell.<br/><br/> - **Zastąp dysk maszyny wirtualnej**. Jeśli maszyna wirtualna istnieje i używa dysków zarządzanych (niezaszyfrowanych), możesz przywrócić dysk i użyć go do zastąpienia istniejącego dysku na maszynie wirtualnej.<br/><br/> - **Przywróć określone pliki/foldery.** Pliki/foldery można przywrócić z maszyny wirtualnej, a nie z całej maszyny wirtualnej.
Bezpośrednie tworzenie kopii zapasowych maszyn wirtualnych platformy Azure (tylko system Windows)  | Kopii zapasowej określonych plików/folderów/woluminu. | Zainstaluj agenta [usług Azure Recovery Services.](backup-azure-file-folder-backup-faq.yml)<br/><br/> Agenta MARS można uruchomić razem z rozszerzeniem kopii zapasowej dla agenta maszyny wirtualnej platformy Azure, aby utworzyć kopię zapasową maszyny wirtualnej na poziomie plików/folderów. | Przywróć określone foldery/pliki.
Tworzenie kopii zapasowej maszyny wirtualnej platformy Azure na serwerze kopii zapasowych  | Kopii zapasowej plików/folderów/woluminów; pliki stanu systemu/bez systemu operacyjnego; dane aplikacji do System Center DPM lub Microsoft Azure Backup Server (MABS).<br/><br/> Program DPM/mabs następnie tworzyć kopie zapasowe w magazynie kopii zapasowych. | Zainstaluj agenta ochrony DPM/MABS na maszynie wirtualnej. Agent MARS jest instalowany w programie DPM/u użytkownika MABS.| Przywracanie plików/folderów/woluminów; pliki stanu systemu/bez systemu operacyjnego; dane aplikacji.

Dowiedz się więcej o kopii [zapasowej przy użyciu serwera kopii zapasowych](backup-architecture.md#architecture-back-up-to-dpmmabs) i [wymaganiach dotyczących pomocy technicznej.](backup-support-matrix-mabs-dpm.md)

## <a name="supported-backup-actions"></a>Obsługiwane akcje tworzenia kopii zapasowej

**Akcja** | **Pomoc techniczna**
--- | ---
Tworzenia kopii zapasowej maszyny wirtualnej, która jest maszyną wirtualną w trybie offline/zamykania | Obsługiwane.<br/><br/> Migawka jest spójna tylko na awariach, a nie spójna na aplikacji.
Po migracji do dysków zarządzanych należy wrócić do kopii zapasowej dysków | Obsługiwane.<br/><br/> Tworzenie kopii zapasowej będzie nadal działać. Nie jest wymagana żadna akcja.
Po włączeniu blokady grupy zasobów należy wrócić do kopii zapasowej dysków zarządzanych | Nieobsługiwane.<br/><br/> Azure Backup nie można usunąć starszych punktów przywracania, a tworzenie kopii zapasowych rozpocznie się niepowodzeniem po osiągnięciu maksymalnego limitu punktów przywracania.
Modyfikowanie zasad tworzenia kopii zapasowych dla maszyny wirtualnej | Obsługiwane.<br/><br/> Kopię zapasową maszyny wirtualnej można wrócić przy użyciu ustawień harmonogramu i przechowywania w nowych zasadach. Jeśli ustawienia przechowywania zostaną rozszerzone, istniejące punkty odzyskiwania zostaną oznaczone i zachowane. Jeśli zostaną one ograniczone, istniejące punkty odzyskiwania zostaną wyczyszone w następnym zadaniu oczyszczania i ostatecznie usunięte.
Anulowanie zadania tworzenia kopii zapasowej| Obsługiwane podczas procesu tworzenia migawki.<br/><br/> Ta funkcja nie jest obsługiwana, gdy migawka jest przesyłana do magazynu.
Tworzenia kopii zapasowej maszyny wirtualnej w innym regionie lub subskrypcji |Nieobsługiwane.<br><br>Aby pomyślnie utworzyć kopię zapasową, maszyny wirtualne muszą znajdować się w tej samej subskrypcji co magazyn kopii zapasowej.
Kopie zapasowe dziennie (za pośrednictwem rozszerzenia maszyny wirtualnej platformy Azure) | Jedna zaplanowana kopia zapasowa dziennie.<br/><br/>Usługa Azure Backup obsługuje maksymalnie trzy kopie zapasowe na żądanie dziennie i jedną dodatkową zaplanowaną kopię zapasową.
Kopie zapasowe dziennie (za pośrednictwem agenta MARS) | Trzy zaplanowane kopie zapasowe dziennie.
Kopie zapasowe dziennie (za pośrednictwem programu DPM/usługi MABS) | Dwie zaplanowane kopie zapasowe dziennie.
Kopia zapasowa co miesiąc/rok| Nie jest obsługiwane podczas tworzenia kopii zapasowej przy użyciu rozszerzenia maszyny wirtualnej platformy Azure. Obsługiwane są tylko raz dziennie i co tydzień.<br/><br/> Zasady można skonfigurować tak, aby przechowywać codzienne/tygodniowe kopie zapasowe dla miesięcznego/miesięcznego okresu przechowywania.
Automatyczne dostosowanie zegara | Nieobsługiwane.<br/><br/> Azure Backup nie dostosowuje się automatycznie do zmian czasu letniego podczas tworzenia kopii zapasowej maszyny wirtualnej.<br/><br/>  W razie potrzeby ręcznie zmodyfikuj zasady.
[Funkcje zabezpieczeń dla hybrydowej kopii zapasowej](./backup-azure-security-feature.md) |Wyłączanie funkcji zabezpieczeń nie jest obsługiwane.
Kopii zapasowej maszyny wirtualnej, której czas maszyny został zmieniony | Nieobsługiwane.<br/><br/> Jeśli godzina maszyny zostanie zmieniona na przyszłą datę i godzina po włączeniu kopii zapasowej dla tej maszyny wirtualnej, to nawet jeśli zmiana czasu zostanie przywrócona, pomyślna kopia zapasowa nie jest gwarantowana.

## <a name="operating-system-support-windows"></a>Obsługa systemu operacyjnego (Windows)

W poniższej tabeli przedstawiono podsumowanie obsługiwanych systemów operacyjnych podczas kopii zapasowej maszyn wirtualnych platformy Microsoft Azure.

**Scenariusz** | **Obsługa systemu operacyjnego**
--- | ---
Back up with Azure VM agent extension (Kopię zapasową z rozszerzeniem agenta maszyny wirtualnej platformy Azure) | - Windows 10 klienta (tylko 64-bitowy) <br/><br/>— Windows Server 2019 (Datacenter/Datacenter Core/Standard) <br/><br/> — Windows Server 2016 (Datacenter/Datacenter Core/Standard) <br/><br/> - Windows Server 2012 R2 (Datacenter/Standard) <br/><br/> - Windows Server 2012 (Datacenter/Standard) <br/><br/> - Windows Server 2008 R2 (RTM i SP1 Standard)  <br/><br/> - Windows Server 2008 (tylko wersja 64-bitowa)
Back up with MARS agent (Kopię zapasową za pomocą agenta MARS) | [Obsługiwane](backup-support-matrix-mars-agent.md#supported-operating-systems) systemy operacyjne.
Back up with DPM/MABS | Obsługiwane systemy operacyjne do tworzenia kopii zapasowych za pomocą [usługi MABS](backup-mabs-protection-matrix.md) i [programu DPM.](/system-center/dpm/dpm-protection-matrix)

Usługa Azure Backup nie obsługuje 32-bitowych systemów operacyjnych.

## <a name="support-for-linux-backup"></a>Obsługa kopii zapasowej systemu Linux

W przypadku kopii zapasowych maszyn z systemem Linux są obsługiwane poniższe elementy.

**Akcja** | **Pomoc techniczna**
--- | ---
Tworzenia kopii zapasowych maszyn wirtualnych platformy Azure z systemem Linux przy użyciu agenta maszyny wirtualnej platformy Azure z systemem Linux | Kopia zapasowa spójna na plikach.<br/><br/> Spójna na poziomie aplikacji kopia zapasowa wykonywana przy użyciu [skryptów niestandardowych](backup-azure-linux-app-consistent.md).<br/><br/> Podczas przywracania można utworzyć nową maszynę wirtualną, przywrócić dysk i użyć go do utworzenia maszyny wirtualnej albo przywrócić dysk i użyć go do zastąpienia dysku na istniejącej maszynie wirtualnej. Można również przywrócić poszczególne pliki i foldery.
Back up Linux Azure VMs with MARS agent (Back up Linux Azure VMs with MARS agent ( Back up Linux Azure VMs with MARS | Nieobsługiwane.<br/><br/> Agenta usługi MARS można zainstalować tylko na maszynach z systemem Windows.
Back up Linux Azure VMs with DPM/MABS | Nieobsługiwane.
Tworzenie kopii zapasowych maszyn wirtualnych platformy Azure z systemem Linux przy użyciu punktów instalacji platformy Docker | Obecnie Azure Backup nie obsługuje wykluczania punktów instalacji platformy Docker, ponieważ są one instalowane w różnych ścieżkach za każdym razem.

## <a name="operating-system-support-linux"></a>Obsługa systemu operacyjnego (Linux)

W przypadku kopii zapasowych maszyn wirtualnych platformy Azure Azure Backup obsługuje listę dystrybucji systemu Linux zatwierdzonych [przez platformę Azure.](../virtual-machines/linux/endorsed-distros.md) . Weź pod uwagę następujące kwestie:

- Azure Backup nie obsługuje podstawowego systemu operacyjnego Linux.
- Usługa Azure Backup nie obsługuje 32-bitowych systemów operacyjnych.
- Inne własne dystrybucje systemu Linux mogą działać tak długo, jak agent maszyny wirtualnej platformy Azure dla systemu [Linux](../virtual-machines/extensions/agent-linux.md) jest dostępny na maszynie wirtualnej i tak długo, jak jest obsługiwany język Python.
- Azure Backup nie obsługuje maszyny wirtualnej z systemem Linux skonfigurowanej przez serwer proxy, jeśli nie ma zainstalowanego środowiska języka Python w wersji 2.7.
- Azure Backup nie obsługuje kopii zapasowej plików NFS zainstalowanych z magazynu lub z dowolnego innego serwera NFS na maszynach z systemem Linux lub Windows. Jest to tylko kopię zapasową dysków, które są lokalnie dołączone do maszyny wirtualnej.

## <a name="backup-frequency-and-retention"></a>Częstotliwość i przechowywanie kopii zapasowych

**Ustawienie** | **Limity**
--- | ---
Maksymalna liczba punktów odzyskiwania na chronione wystąpienie (maszyna/obciążenie) | 9999.
Maksymalny czas wygaśnięcia punktu odzyskiwania | Brak limitu (99 lat).
Maksymalna częstotliwość tworzenia kopii zapasowych w magazynie (rozszerzenie maszyny wirtualnej platformy Azure) | Raz dziennie.
Maksymalna częstotliwość tworzenia kopii zapasowych w magazynie (agent MARS) | Trzy kopie zapasowe dziennie.
Maksymalna częstotliwość wykonywania kopii zapasowych w programie DPM/usłudze MABS | Co 15 minut dla SQL Server.<br/><br/> Raz na godzinę dla innych obciążeń.
Przechowywanie punktów odzyskiwania | Codziennie, co tydzień, co miesiąc i co rok.
Maksymalny okres przechowywania | Zależnie od częstotliwości wykonywania kopii zapasowych.
Punkty odzyskiwania na dysku programu DPM/usługi MABS | 64 dla serwerów plików i 448 dla serwerów aplikacji.<br/><br/> Punkty odzyskiwania na taśmie nie mają ograniczeń w przypadku lokalnego programu DPM.

## <a name="supported-restore-methods"></a>Obsługiwane metody przywracania

**Opcja przywracania** | **Szczegóły**
--- | ---
**Tworzenie nowej maszyny wirtualnej** | Szybko tworzy i pobiera z punktu przywracania skonfigurowaną i uruchomioną podstawową maszynę wirtualną.<br/><br/> Możesz określić nazwę maszyny wirtualnej, wybrać grupę zasobów i sieć wirtualną, w której zostanie umieszczona, i określić konto magazynu dla przywróconej maszyny wirtualnej. Nową maszynę wirtualną należy utworzyć w tym samym regionie co źródłową maszynę wirtualną.
**Przywracanie dysku** | Przywraca dysk maszyny wirtualnej, za pomocą którego można następnie utworzyć nową maszynę wirtualną.<br/><br/> Usługa Azure Backup udostępnia szablon ułatwiający dostosowanie i utworzenie maszyny wirtualnej. <br/><br> Zadanie przywracania generuje szablon, który można pobrać i użyć do określenia niestandardowych ustawień maszyny wirtualnej oraz utworzenia maszyny wirtualnej.<br/><br/> Dyski są kopiowane do określonej grupy zasobów.<br/><br/> Alternatywnie możesz dołączyć dysk do istniejącej maszyny wirtualnej lub utworzyć nową maszynę wirtualną przy użyciu programu PowerShell.<br/><br/> Ta opcja jest przydatna, jeśli chcesz dostosować maszynę wirtualną, dodać ustawienia konfiguracji, których nie było w czasie tworzenia kopii zapasowej, lub dodać ustawienia, które należy skonfigurować za pomocą szablonu lub programu PowerShell.
**Zamiana istniejącego** | Możesz przywrócić dysk i użyć go do zastąpienia dysku na istniejącej maszynie wirtualnej.<br/><br/> Wymaga to istnienia bieżącej maszyny wirtualnej. Jeśli została ona usunięta, nie można użyć tej opcji.<br/><br/> Azure Backup migawkę istniejącej maszyny wirtualnej przed zastąpieniem dysku i zapisuje ją w określisz lokalizacji przejściowej. Istniejące dyski połączone z maszyną wirtualną są zastępowane wybranym punktem przywracania.<br/><br/> Migawka jest kopiowana do magazynu i zachowywana zgodnie z zasadami przechowywania. <br/><br/> Po operacji wymiany dysku oryginalny dysk jest zachowywany w grupie zasobów. Możesz ręcznie usunąć oryginalne dyski, jeśli nie są potrzebne. <br/><br/>Zastąpienie istniejącego jest obsługiwane w przypadku niezaszyfrowanych zarządzanych maszyn wirtualnych i maszyn wirtualnych [utworzonych przy użyciu obrazów niestandardowych.](https://azure.microsoft.com/resources/videos/create-a-custom-virtual-machine-image-in-azure-resource-manager-with-powershell/) Nie jest ona obsługiwana w przypadku dysków niezaładowanych i maszyn wirtualnych, klasycznych maszyn wirtualnych i [uogólnionych maszyn wirtualnych.](../virtual-machines/windows/capture-image-resource.md)<br/><br/> Jeśli punkt przywracania ma więcej lub mniej dysków niż bieżąca maszyna wirtualna, liczba dysków w punkcie przywracania będzie odzwierciedlać tylko konfigurację maszyny wirtualnej.<br><br> Zastąpienie istniejącej jest również obsługiwane w przypadku maszyn wirtualnych połączonymi zasobami, na przykład [tożsamością zarządzaną](../active-directory/managed-identities-azure-resources/overview.md) przypisaną przez [użytkownika i Key Vault](../key-vault/general/overview.md).
**Między regionami (region pomocniczy)** | Przywracanie między regionami może służyć do przywracania maszyn wirtualnych platformy Azure w regionie pomocniczym, który jest sparowany [z platformą Azure.](../best-practices-availability-paired-regions.md#what-are-paired-regions)<br><br> Jeśli kopia zapasowa zostanie wykonana w regionie pomocniczym, możesz przywrócić wszystkie maszyny wirtualne platformy Azure dla wybranego punktu odzyskiwania.<br><br> Ta funkcja jest dostępna dla poniższych opcji:<br> <li> [Tworzenie maszyny wirtualnej](./backup-azure-arm-restore-vms.md#create-a-vm) <br> <li> [Przywracanie dysków](./backup-azure-arm-restore-vms.md#restore-disks) <br><br> Obecnie nie obsługujemy opcji [Zamień istniejące](./backup-azure-arm-restore-vms.md#replace-existing-disks) dyski.<br><br> Uprawnienia<br> Operację przywracania w regionie pomocniczym mogą wykonywać administratorzy kopii zapasowej i administratorzy aplikacji.

## <a name="support-for-file-level-restore"></a>Obsługa przywracania na poziomie plików

**Przywracanie** | **Obsługiwane**
--- | ---
Przywracanie plików w różnych systemach operacyjnych | Pliki można przywrócić na dowolnej maszynie, która ma ten sam (lub zgodny) system operacyjny co maszyna wirtualna z kopią zapasową. Zobacz [tabelę Compatible OS (Zgodny system operacyjny).](backup-azure-restore-files-from-vm.md#step-3-os-requirements-to-successfully-run-the-script)
Przywracanie plików z zaszyfrowanych maszyn wirtualnych | Nieobsługiwane.
Przywracanie plików z kont magazynu z ograniczeniami sieci | Nieobsługiwane.
Przywracanie plików na maszyn wirtualnych przy użyciu funkcji Miejsca do magazynowania systemu Windows | Przywracanie nie jest obsługiwane na tej samej maszynie wirtualnej.<br/><br/> Zamiast tego należy przywrócić pliki na zgodnej maszynie wirtualnej.
Przywracanie plików na maszynie wirtualnej z systemem Linux przy użyciu macierzy LVM/RAID | Przywracanie nie jest obsługiwane na tej samej maszynie wirtualnej.<br/><br/> Przywracanie na zgodnej maszynie wirtualnej.
Przywracanie plików ze specjalnymi ustawieniami sieci | Przywracanie nie jest obsługiwane na tej samej maszynie wirtualnej. <br/><br/> Przywracanie na zgodnej maszynie wirtualnej.
Przywracanie plików z dysku udostępnionego, dysku tymczasowego, dysku deduplikacji, dysku w ultra i dysku z włączonym akceleratorem zapisu | Przywracanie nie jest obsługiwane, <br/><br/>Zobacz [Obsługa magazynu maszyn wirtualnych platformy Azure.](#vm-storage-support)

## <a name="support-for-vm-management"></a>Obsługa zarządzania maszynami wirtualnymi

W poniższej tabeli podsumowano obsługę tworzenia kopii zapasowych podczas zadań zarządzania maszyną wirtualną, takich jak dodawanie lub zastępowanie dysków maszyn wirtualnych.

**Przywracanie** | **Obsługiwane**
--- | ---
Przywracanie między subskrypcjami/regionami/strefami. | Nieobsługiwane.
Przywracanie do istniejącej maszyny wirtualnej | Użyj opcji zamień dysk.
Przywracanie dysku z włączonym kontem magazynu dla usługi Azure szyfrowanie usługi Storage (SSE) | Nieobsługiwane.<br/><br/> Przywróć na konto, które nie ma włączonej funkcji SSE.
Przywracanie do mieszanych kont magazynu |Nieobsługiwane.<br/><br/> W zależności od typu konta magazynu wszystkie przywrócone dyski będą dyskami w warstwie Premium lub Standardowa i nie będą mieszane.
Przywracanie maszyny wirtualnej bezpośrednio do zestawu dostępności | W przypadku dysków zarządzanych można przywrócić dysk i użyć opcji zestawu dostępności w szablonie.<br/><br/> Nie jest obsługiwane w przypadku dysków niezamaniowych. W przypadku dysków nieza zarządzaniem przywróć dysk, a następnie utwórz maszynę wirtualną w zestawie dostępności.
Przywracanie kopii zapasowej nieza zarządzanych maszyn wirtualnych po uaktualnieniu do zarządzanej maszyny wirtualnej| Obsługiwane.<br/><br/> Możesz przywrócić dyski, a następnie utworzyć zarządzaną maszynę wirtualną.
Przywracanie maszyny wirtualnej do punktu przywracania przed migracją maszyny wirtualnej do dysków zarządzanych | Obsługiwane.<br/><br/> Przywracasz dyski nieza zarządzania (ustawienie domyślne), konwertujesz przywrócone dyski na dysk zarządzany i tworzysz maszynę wirtualną z dyskami zarządzanymi.
Przywróć usuniętą maszynę wirtualną. | Obsługiwane.<br/><br/> Maszynę wirtualną można przywrócić z punktu odzyskiwania.
Przywracanie maszyny wirtualnej kontrolera domeny  | Obsługiwane. Aby uzyskać szczegółowe informacje, [zobacz Przywracanie maszyn wirtualnych kontrolera domeny.](backup-azure-arm-restore-vms.md#restore-domain-controller-vms)
Przywracanie maszyny wirtualnej w innej sieci wirtualnej |Obsługiwane.<br/><br/> Sieć wirtualna musi znajdować się w tej samej subskrypcji i tym samym regionie.

## <a name="vm-compute-support"></a>Obsługa obliczeń na maszynach wirtualnych

**Środowisko obliczeniowe** | **Pomoc techniczna**
--- | ---
Rozmiar maszyny wirtualnej |Dowolny rozmiar maszyny wirtualnej platformy Azure z co najmniej 2 rdzeniami procesora CPU i 1 GB pamięci RAM.<br/><br/> [Dowiedz się więcej.](../virtual-machines/sizes.md)
Back up VMs in availability sets (Kopii zapasowej maszyn wirtualnych w [zestawach dostępności)](../virtual-machines/availability.md#availability-sets) | Obsługiwane.<br/><br/> Nie można przywrócić maszyny wirtualnej w dostępnym zestawie przy użyciu opcji szybkiego tworzenia maszyny wirtualnej. Zamiast tego podczas przywracania maszyny wirtualnej przywróć dysk i użyj go do wdrożenia maszyny wirtualnej lub przywróć dysk i użyj go do zastąpienia istniejącego dysku.
Back up VMs that are deployed with Hybrid Use Benefit (HUB) (Wdrażanie kopii zapasowej maszyn wirtualnych z korzyścią użycia [hybrydowego (HUB)](../virtual-machines/windows/hybrid-use-benefit-licensing.md) | Obsługiwane.
Back up VMs that are deployed from [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps?filters=virtual-machine-images) (Kopii zapasowej maszyn wirtualnych wdrożonych z Azure Marketplace<br/><br/> (Opublikowane przez firmę Microsoft, inne firmy) |Obsługiwane.<br/><br/> Na maszynie wirtualnej musi działać obsługiwany system operacyjny.<br/><br/> Podczas odzyskiwania plików na maszynie wirtualnej można przywrócić tylko do zgodnego systemu operacyjnego (nie starszego ani nowszego). Nie przywracamy maszyn wirtualnych Azure Marketplace jako maszyn wirtualnych, ponieważ wymagają one informacji o zakupie. Są one przywracane tylko jako dyski.
Back up VMs that are deployed from a custom image (third-party) (Tworzyć kopię zapasową maszyn wirtualnych wdrożonych z obrazu niestandardowego (innej firmy) |Obsługiwane.<br/><br/> Na maszynie wirtualnej musi działać obsługiwany system operacyjny.<br/><br/> Podczas odzyskiwania plików na maszynie wirtualnej można przywrócić tylko do zgodnego systemu operacyjnego (nie starszego ani nowszego).
Kopii zapasowej maszyn wirtualnych migrowanych na platformę Azure| Obsługiwane.<br/><br/> Aby można było wykonać kopię zapasową maszyny wirtualnej, na migrowanych maszynach musi być zainstalowany agent maszyny wirtualnej.
Spójność kopii zapasowych wielu maszyn wirtualnych | Azure Backup nie zapewnia spójności danych i aplikacji na wielu maszyn wirtualnych.
Tworzenie kopii zapasowej przy [użyciu ustawień diagnostycznych](../azure-monitor/essentials/platform-logs-overview.md)  | Nieobsługiwane. <br/><br/> Jeśli przywracanie maszyny wirtualnej platformy Azure z [](backup-azure-arm-restore-vms.md#create-a-vm) ustawieniami diagnostycznym zostanie wyzwolone przy użyciu opcji Utwórz nową, przywracanie nie powiedzie się.
Przywracanie maszyn wirtualnych przypiętych do strefy | Obsługiwane (w przypadku maszyny wirtualnej, której kopię zapasową należy wrócić po sty 2019 r. i gdzie są [dostępne](https://azure.microsoft.com/global-infrastructure/availability-zones/) strefy dostępności).<br/><br/>Obecnie obsługujemy przywracanie do tej samej strefy, która jest przypięta na maszyny wirtualne. Jeśli jednak strefa jest niedostępna z powodu awarii, przywracanie zakończy się niepowodzeniem.
Maszyny wirtualne 2. generacji | Obsługiwane <br> Azure Backup obsługuje tworzenie kopii zapasowych i przywracanie maszyn [wirtualnych gen2.](https://azure.microsoft.com/updates/generation-2-virtual-machines-in-azure-public-preview/) Po przywróceniu tych maszyn wirtualnych z punktu odzyskiwania są one przywracane jako maszyny [wirtualne gen2.](https://azure.microsoft.com/updates/generation-2-virtual-machines-in-azure-public-preview/)
Tworzenie kopii zapasowych maszyn wirtualnych platformy Azure z blokadami | Nieobsługiwane w przypadku niezaładowanych maszyn wirtualnych. <br><br> Obsługiwane w przypadku zarządzanych maszyn wirtualnych.
[Maszyny wirtualne typu spot](../virtual-machines/spot-vms.md) | Nieobsługiwane. Azure Backup przywraca maszyny wirtualne usługi Spot jako zwykłe maszyny wirtualne platformy Azure.
[Azure Dedicated Host](../virtual-machines/dedicated-hosts.md) | Obsługiwane
Konfiguracja funkcji Miejsca do magazynowania systemu Windows dla autonomicznych maszyn wirtualnych platformy Azure | Obsługiwane
[Azure VM Scale Sets](../virtual-machine-scale-sets/virtual-machine-scale-sets-orchestration-modes.md#scale-sets-with-flexible-orchestration) | Obsługiwane w przypadku jednolitych i elastycznych modeli aranżacji w celu tworzenia kopii zapasowych i przywracania pojedynczej maszyny wirtualnej platformy Azure.

## <a name="vm-storage-support"></a>Obsługa magazynu maszyn wirtualnych

**Składnik** | **Pomoc techniczna**
--- | ---
Dyski z danymi maszyn wirtualnych platformy Azure | Obsługa tworzenia kopii zapasowych maszyn wirtualnych platformy Azure z maksymalnie 32 dyskami.<br><br> Obsługa tworzenia kopii zapasowych maszyn wirtualnych platformy Azure z dyskami nieza zarządzania lub klasycznymi maszynami wirtualnych wynosi tylko 16 dysków.
Rozmiar dysku danych | Pojedynczy rozmiar dysku może być do 32 TB i maksymalnie 256 TB łącznie dla wszystkich dysków na maszynie wirtualnej.
Typ magazynu | HDD w warstwie Standardowa, SSD w warstwie Standardowa, SSD w warstwie Premium.
Dyski zarządzane | Obsługiwane.
Zaszyfrowane dyski | Obsługiwane.<br/><br/> Maszyny wirtualne platformy Azure z włączoną Azure Disk Encryption mogą być kopii zapasowej (z aplikacją usługi Azure AD lub bez tej aplikacji).<br/><br/> Zaszyfrowanych maszyn wirtualnych nie można odzyskać na poziomie pliku/folderu. Musisz odzyskać całą maszynę wirtualną.<br/><br/> Możesz włączyć szyfrowanie na maszynach wirtualnych, które są już chronione przez Azure Backup.
Dyski z włączoną akcelerator zapisu aktywna | Od 23 listopada 2020 r. obsługiwane tylko w regionach Korea Środkowa (KRC) i Północna Republika Południowej Afryki (SAN) dla ograniczonej liczby subskrypcji. W przypadku tych obsługiwanych subskrypcji program Azure Backup kopie zapasowe maszyn wirtualnych z dyskami z włączoną akcelerą zapisu (WA) podczas tworzenia kopii zapasowej.<br><br>W przypadku nieobsługiwanych regionów wymagana jest łączność z Internetem na maszynie wirtualnej w celu tworzenia migawek Virtual Machines z włączoną usługą WA.<br><br> **Ważna uwaga:** W tych nieobsługiwanych regionach maszyny wirtualne z dyskami WA wymagają łączności z Internetem w celu pomyślnego tworzenia kopii zapasowej (mimo że te dyski są wykluczone z kopii zapasowej).
Back up & Restore deduplicated VMs/disks | Azure Backup nie obsługuje deduplikacji. Aby uzyskać więcej informacji, zobacz ten [artykuł](./backup-support-matrix.md#disk-deduplication-support) <br/> <br/>  — Azure Backup nie deduplikuje maszyn wirtualnych w magazynie usługi Recovery Services <br/> <br/>  — Jeśli podczas przywracania istnieją maszyny wirtualne w stanie deduplikacji, nie można przywrócić plików, ponieważ magazyn nie rozumie formatu. Można jednak pomyślnie wykonać pełne przywracanie maszyny wirtualnej.
Dodawanie dysku do chronionej maszyny wirtualnej | Obsługiwane.
Zmienianie rozmiaru dysku chronionej maszyny wirtualnej | Obsługiwane.
Magazyn udostępniony| Nie jest obsługiwane używanie kopii zapasowej maszyn wirtualnych udostępniony wolumin klastra (CSV) lub Scale-Out Serwera plików. Podczas tworzenia kopii zapasowej prawdopodobnie nie powiedzie się tworzenie woluminów CSV. Po przywróceniu dyski zawierające woluminy CSV mogą nie pojawić się.
[Dyski udostępnione](../virtual-machines/disks-shared-enable.md) | Nieobsługiwane.
SSD w warstwie Ultra dysków | Nieobsługiwane. Aby uzyskać więcej informacji, zobacz te [ograniczenia.](selective-disk-backup-restore.md#limitations)
[Dyski tymczasowe](../virtual-machines/managed-disks-overview.md#temporary-disk) | Dyski tymczasowe nie są kopii zapasowej przez Azure Backup.
Dyski NVMe/efemeracyjne | Nieobsługiwane.

## <a name="vm-network-support"></a>Obsługa sieci maszyn wirtualnych

**Składnik** | **Pomoc techniczna**
--- | ---
Liczba interfejsów sieciowych (NIC) | Maksymalnie maksymalna liczba obsługiwanych karty sieciowe dla określonego rozmiaru maszyny wirtualnej platformy Azure.<br/><br/> Karty sieciowe są tworzone podczas tworzenia maszyny wirtualnej podczas procesu przywracania.<br/><br/> Liczba karty sieciowe na przywróconej maszynie wirtualnej odzwierciedla liczbę karty sieciowe na maszynie wirtualnej po włączeniu ochrony. Usunięcie karty sieciowej po włączeniu ochrony nie ma wpływu na liczbę.
Zewnętrzny/wewnętrzny równoważenie obciążenia |Obsługiwane. <br/><br/> [Dowiedz się więcej](backup-azure-arm-restore-vms.md#restore-vms-with-special-configurations) o przywracaniu maszyn wirtualnych za pomocą specjalnych ustawień sieci.
Wiele zastrzeżonych adresów IP |Obsługiwane. <br/><br/> [Dowiedz się więcej](backup-azure-arm-restore-vms.md#restore-vms-with-special-configurations) o przywracaniu maszyn wirtualnych za pomocą specjalnych ustawień sieci.
Maszyny wirtualne z wieloma kartami sieciowym| Obsługiwane. <br/><br/> [Dowiedz się więcej](backup-azure-arm-restore-vms.md#restore-vms-with-special-configurations) o przywracaniu maszyn wirtualnych za pomocą specjalnych ustawień sieci.
Maszyny wirtualne z publicznymi adresami IP| Obsługiwane.<br/><br/> Po zakończeniu przywracania należy skojarzyć istniejący publiczny adres IP z kartą sieciową lub utworzyć adres i skojarzyć go z kartą sieciową.
Sieciowa grupa zabezpieczeń (NSG) na kartę sieciową/podsieć. |Obsługiwane.
Statyczny adres IP | Nieobsługiwane.<br/><br/> Do nowej maszyny wirtualnej utworzonej na podstawie punktu przywracania jest przypisywany dynamiczny adres IP.<br/><br/> W przypadku klasycznych maszyn wirtualnych nie można utworzyć kopii zapasowej maszyny wirtualnej z zastrzeżonym adresem IP i bez zdefiniowanego punktu końcowego.
Dynamiczny adres IP |Obsługiwane.<br/><br/> Jeśli karta sieciowa na źródłowej maszynie wirtualnej używa dynamicznego adresowania IP, domyślnie będzie ona również używana przez kartę sieciową na przywróconej maszynie wirtualnej.
Azure Traffic Manager| Obsługiwane.<br/><br/>Jeśli maszyna wirtualna z kopią zapasową znajduje się w Traffic Manager, ręcznie dodaj przywróconą maszynę wirtualną do tego samego Traffic Manager wystąpienia.
Azure DNS |Obsługiwane.
Niestandardowe DNS |Obsługiwane.
Łączność wychodząca za pośrednictwem serwera proxy HTTP | Obsługiwane.<br/><br/> Uwierzytelniony serwer proxy nie jest obsługiwany.
Punkty końcowe usługi dla sieci wirtualnej| Obsługiwane.<br/><br/> Ustawienia zapory i konta magazynu sieci wirtualnej powinny zezwalać na dostęp ze wszystkich sieci.

## <a name="vm-security-and-encryption-support"></a>Obsługa zabezpieczeń i szyfrowania maszyn wirtualnych

Usługa Azure Backup obsługuje szyfrowanie danych podczas przesyłania i danych magazynowanych:

Ruch sieciowy do platformy Azure:

- Ruch kopii zapasowej z serwerów do magazynu usługi Recovery Services jest szyfrowany przy użyciu Advanced Encryption Standard 256.
- Dane kopii zapasowej są przesyłane za pośrednictwem bezpiecznego połączenia HTTPS.
- W magazynie usługi Recovery Services dane kopii zapasowych są przechowywane w postaci zaszyfrowanej.
- Tylko Ty masz klucz szyfrowania do odblokowania tych danych. Firma Microsoft nie może odszyfrować danych kopii zapasowej w żadnym punkcie.

  > [!WARNING]
  > Po skonfigurowaniu magazynu tylko Ty masz dostęp do klucza szyfrowania. Firma Microsoft nigdy nie przechowuje kopii i nie ma dostępu do klucza. W przypadku utraty klucza firma Microsoft nie może odzyskać danych kopii zapasowej.

Bezpieczeństwo danych:

- Podczas kopii zapasowej maszyn wirtualnych platformy Azure należy skonfigurować szyfrowanie *na maszynie* wirtualnej.
- Azure Backup obsługuje Azure Disk Encryption, która korzysta z funkcji BitLocker na maszynach wirtualnych z systemem Windows, a program **dm-crypt** na maszynach wirtualnych z systemem Linux.
- Na zapleczu usługa Azure Backup używa [szyfrowania usługi Azure Storage](../storage/common/storage-service-encryption.md), które chroni dane magazynowane.

**Maszyna** | **Przesyłanie** | **Magazynowanie**
--- | --- | ---
Lokalne maszyny z systemem Windows bez programu DPM/usługi MABS | ![Tak][green] | ![Tak][green]
Maszyny wirtualne platformy Azure | ![Tak][green] | ![Tak][green]
Lokalne maszyny wirtualne/maszyny wirtualne platformy Azure z programem DPM | ![Tak][green] | ![Tak][green]
Lokalne maszyny wirtualne/maszyny wirtualne platformy Azure z usługą MABS | ![Tak][green] | ![Tak][green]

## <a name="vm-compression-support"></a>Obsługa kompresji maszyny wirtualnej

Kopia zapasowa obsługuje kompresję ruchu kopii zapasowych, zgodnie z podsumowaniem w poniższej tabeli. . Weź pod uwagę następujące kwestie:

- W przypadku maszyn wirtualnych platformy Azure rozszerzenie maszyny wirtualnej odczytuje dane bezpośrednio z konta usługi Azure Storage za pośrednictwem sieci magazynu. Kompresowanie tego ruchu nie jest konieczne.
- Jeśli używasz programu DPM lub usługi MABS, możesz zaoszczędzić przepustowość, kompresując dane przed ich kopią zapasową w programie DPM/u usługach MABS.

**Maszyna** | **Kompresja do usługi MABS/programu DPM (TCP)** | **Kompresowanie do magazynu (HTTPS)**
--- | --- | ---
Lokalne maszyny z systemem Windows bez programu DPM/usługi MABS | Nie dotyczy | ![Yes][green]
Maszyny wirtualne platformy Azure | Nie dotyczy | NA
Lokalne maszyny wirtualne/maszyny wirtualne platformy Azure z programem DPM | ![Tak][green] | ![Tak][green]
Lokalne maszyny wirtualne/maszyny wirtualne platformy Azure z usługą MABS | ![Tak][green] | ![Tak][green]

## <a name="next-steps"></a>Następne kroki

- [Kopię zapasową maszyn wirtualnych platformy Azure.](backup-azure-arm-vms-prepare.md)
- [Bezpośrednie wykonywanie kopii zapasowych maszyn z systemem Windows](tutorial-backup-windows-server-to-azure.md) bez serwera kopii zapasowych.
- [Konfigurowanie usługi MABS](backup-azure-microsoft-azure-backup.md) na potrzeby kopii zapasowych na platformie Azure, a następnie wykonywanie kopii zapasowych obciążeń w usłudze MABS.
- [Konfigurowanie programu DPM](backup-azure-dpm-introduction.md) na potrzeby kopii zapasowych na platformie Azure, a następnie wykonywanie kopii zapasowych obciążeń w programie DPM.

[green]: ./media/backup-support-matrix/green.png
[yellow]: ./media/backup-support-matrix/yellow.png
[red]: ./media/backup-support-matrix/red.png