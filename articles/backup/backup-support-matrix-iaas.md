---
title: Tabela obsługi dla maszyn wirtualnych platformy Azure
description: Zawiera podsumowanie ustawień i ograniczeń pomocy technicznej podczas tworzenia kopii zapasowych maszyn wirtualnych platformy Azure przy użyciu usługi Azure Backup.
ms.topic: conceptual
ms.date: 09/13/2019
ms.custom: references_regions
ms.openlocfilehash: 82de73944bd833ec5f921e07a29960ac6d175f8d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102609791"
---
# <a name="support-matrix-for-azure-vm-backup"></a>Tabela obsługi dla maszyn wirtualnych platformy Azure

Za pomocą [usługi Azure Backup](backup-overview.md) można tworzyć kopie zapasowe maszyn i obciążeń lokalnych oraz maszyn wirtualnych platformy Azure. Ten artykuł zawiera podsumowanie ustawień i ograniczeń pomocy technicznej podczas tworzenia kopii zapasowych maszyn wirtualnych platformy Azure przy użyciu Azure Backup.

Inne macierze pomocy technicznej:

- [Macierz obsługi ogólnej](backup-support-matrix.md) dla Azure Backup
- [Macierz obsługi](backup-support-matrix-mabs-dpm.md) dla Azure Backup serwera/programu System Center Data Protection Manager (DPM)
- [Macierz obsługi](backup-support-matrix-mars-agent.md) kopii zapasowej za pomocą agenta Microsoft Azure Recovery Services (MARS)

## <a name="supported-scenarios"></a>Obsługiwane scenariusze

Oto jak można tworzyć kopie zapasowe maszyn wirtualnych platformy Azure i przywracać je za pomocą usługi Azure Backup.

**Scenariusz** | **Tworzenie kopii zapasowych** | **Odczynnik** |**Przywracanie**
--- | --- | --- | ---
Bezpośrednie tworzenie kopii zapasowych maszyn wirtualnych platformy Azure  | Wykonaj kopię zapasową całej maszyny wirtualnej.  | Na maszynie wirtualnej platformy Azure nie jest wymagany żaden dodatkowy Agent. Azure Backup instaluje i używa rozszerzenia dla [agenta maszyny wirtualnej platformy Azure](../virtual-machines/extensions/agent-windows.md) uruchomionego na maszynie wirtualnej. | Przywróć w następujący sposób:<br/><br/> - **Utwórz podstawową maszynę wirtualną**. Jest to przydatne, jeśli maszyna wirtualna nie ma specjalnej konfiguracji, takiej jak wiele adresów IP.<br/><br/> - **Przywróć dysk maszyny wirtualnej**. Przywróć dysk. Następnie Dołącz ją do istniejącej maszyny wirtualnej lub Utwórz nową maszynę wirtualną z dysku przy użyciu programu PowerShell.<br/><br/> - **Zastąp dysk maszyny wirtualnej**. Jeśli maszyna wirtualna istnieje i korzysta z dysków zarządzanych (nieszyfrowanych), można przywrócić dysk i użyć go w celu zastąpienia istniejącego dysku na maszynie wirtualnej.<br/><br/> - **Przywracanie określonych plików/folderów**. Pliki/foldery można przywrócić z maszyny wirtualnej, a nie z całej maszyny wirtualnej.
Bezpośrednie tworzenie kopii zapasowych maszyn wirtualnych platformy Azure (tylko system Windows)  | Utwórz kopię zapasową określonych plików/folderów/woluminów. | Zainstaluj [agenta Recovery Services platformy Azure](backup-azure-file-folder-backup-faq.md).<br/><br/> Agenta MARS można uruchomić wraz z rozszerzeniem kopii zapasowej agenta maszyny wirtualnej platformy Azure, aby utworzyć kopię zapasową maszyny wirtualnej na poziomie pliku/folderu. | Przywracanie określonych folderów/plików.
Tworzenie kopii zapasowej maszyny wirtualnej platformy Azure na serwerze kopii zapasowej  | Tworzenie kopii zapasowej plików/folderów/woluminów; pliki stanu systemu/systemu operacyjnego; dane aplikacji do programu System Center DPM lub do Microsoft Azure Backup Server (serwera usługi MAB).<br/><br/> Program DPM/serwera usługi MAB następnie tworzy kopię zapasową magazynu z usługą Backup. | Zainstaluj agenta ochrony programu DPM/serwera usługi MAB na maszynie wirtualnej. Agent MARS jest instalowany na serwerze DPM/serwera usługi MAB.| Przywracanie plików/folderów/woluminów; pliki stanu systemu/systemu operacyjnego; dane aplikacji.

Dowiedz się więcej o usłudze Backup [przy użyciu serwera zapasowego](backup-architecture.md#architecture-back-up-to-dpmmabs) i o [wymaganiach dotyczących pomocy technicznej](backup-support-matrix-mabs-dpm.md).

## <a name="supported-backup-actions"></a>Obsługiwane akcje tworzenia kopii zapasowych

**Akcja** | **Pomoc techniczna**
--- | ---
Tworzenie kopii zapasowej maszyny wirtualnej, która jest zamykana/maszyną wirtualną w trybie offline | Obsługiwane.<br/><br/> Migawka jest tylko w przypadku awarii, a nie spójna z aplikacją.
Tworzenie kopii zapasowych dysków po migracji na dyski zarządzane | Obsługiwane.<br/><br/> Kopia zapasowa będzie nadal działała. Nie jest wymagana żadna akcja.
Tworzenie kopii zapasowych dysków zarządzanych po włączeniu blokady grupy zasobów | Nieobsługiwane.<br/><br/> Azure Backup nie może usunąć starszych punktów przywracania, a kopie zapasowe rozpoczną się niepowodzeniem po osiągnięciu maksymalnego limitu punktów przywracania.
Modyfikowanie zasad tworzenia kopii zapasowej dla maszyny wirtualnej | Obsługiwane.<br/><br/> Kopia zapasowa maszyny wirtualnej zostanie utworzona przy użyciu ustawień harmonogram i przechowywanie w obszarze nowe zasady. Jeśli ustawienia przechowywania są rozszerzone, istniejące punkty odzyskiwania zostaną oznaczone i zachowane. Jeśli są one zmniejszone, istniejące punkty odzyskiwania zostaną oczyszczone w następnym zadaniu oczyszczania i ostatecznie usunięte.
Anulowanie zadania tworzenia kopii zapasowej| Obsługiwane podczas procesu tworzenia migawek.<br/><br/> Nieobsługiwane w przypadku przesyłania migawki do magazynu.
Tworzenie kopii zapasowej maszyny wirtualnej w innym regionie lub subskrypcji |Nieobsługiwane.<br><br>Aby pomyślnie utworzyć kopię zapasową, maszyny wirtualne muszą znajdować się w tej samej subskrypcji co magazyn kopii zapasowych.
Kopie zapasowe dziennie (za pomocą rozszerzenia maszyny wirtualnej platformy Azure) | Jedna zaplanowana kopia zapasowa dziennie.<br/><br/>Usługa Azure Backup obsługuje maksymalnie trzy kopie zapasowe na żądanie dziennie oraz jedną dodatkową zaplanowaną kopię zapasową.
Kopie zapasowe dziennie (za pośrednictwem agenta MARS) | Trzy zaplanowane kopie zapasowe dziennie.
Kopie zapasowe dziennie (za pośrednictwem programu DPM/serwera usługi MAB) | Dwa zaplanowane kopie zapasowe dziennie.
Kopia zapasowa co miesiąc/co rok| Nieobsługiwane podczas tworzenia kopii zapasowej przy użyciu rozszerzenia maszyny wirtualnej platformy Azure. Obsługiwane są tylko codziennie i co tydzień.<br/><br/> Można skonfigurować zasady, aby zachować codzienne/cotygodniowe kopie zapasowe w okresie przechowywania miesięcznego/rocznego.
Automatyczne dostosowanie zegara | Nieobsługiwane.<br/><br/> Podczas tworzenia kopii zapasowej maszyny wirtualnej Azure Backup nie jest automatycznie dostosowywana do zmian czasu letniego.<br/><br/>  Zmodyfikuj zasady ręcznie w razie konieczności.
[Funkcje zabezpieczeń hybrydowej kopii zapasowej](./backup-azure-security-feature.md) |Wyłączenie funkcji zabezpieczeń nie jest obsługiwane.
Tworzenie kopii zapasowej maszyny wirtualnej, której czas maszyny został zmieniony | Nieobsługiwane.<br/><br/> Jeśli czas komputera zostanie zmieniony na przyszłą datę i godzinę po włączeniu kopii zapasowej dla tej maszyny wirtualnej, jednak nawet jeśli zmiana czasu zostanie wycofana, pomyślne utworzenie kopii zapasowej nie jest gwarantowane.

## <a name="operating-system-support-windows"></a>Obsługa systemu operacyjnego (Windows)

Poniższa tabela zawiera podsumowanie obsługiwanych systemów operacyjnych podczas tworzenia kopii zapasowych maszyn wirtualnych z systemem Windows Azure.

**Scenariusz** | **Obsługa systemu operacyjnego**
--- | ---
Tworzenie kopii zapasowej przy użyciu rozszerzenia agenta maszyny wirtualnej platformy Azure | -Windows 10 Client (tylko 64 bitowy) <br/><br/>-Windows Server 2019 (Datacenter/Datacenter/standard) <br/><br/> -Windows Server 2016 (Datacenter/Datacenter/standard) <br/><br/> — Windows Server 2012 R2 (Datacenter/standard) <br/><br/> — Windows Server 2012 (Datacenter/standard) <br/><br/> -Windows Server 2008 R2 (wersja RTM i SP1 standard)  <br/><br/> — Windows Server 2008 (tylko wersja 64)
Tworzenie kopii zapasowej przy użyciu agenta MARS | [Obsługiwane](backup-support-matrix-mars-agent.md#supported-operating-systems) systemy operacyjne.
Tworzenie kopii zapasowej za pomocą programu DPM/serwera usługi MAB | Obsługiwane systemy operacyjne na potrzeby tworzenia kopii zapasowych za pomocą [serwera usługi MAB](backup-mabs-protection-matrix.md) i [programu DPM](/system-center/dpm/dpm-protection-matrix).

Usługa Azure Backup nie obsługuje 32-bitowych systemów operacyjnych.

## <a name="support-for-linux-backup"></a>Obsługa kopii zapasowych systemu Linux

W przypadku kopii zapasowych maszyn z systemem Linux są obsługiwane poniższe elementy.

**Akcja** | **Pomoc techniczna**
--- | ---
Tworzenie kopii zapasowych maszyn wirtualnych systemu Linux Azure przy użyciu agenta maszyny wirtualnej systemu Linux | Kopia zapasowa spójna z plikiem.<br/><br/> Spójna na poziomie aplikacji kopia zapasowa wykonywana przy użyciu [skryptów niestandardowych](backup-azure-linux-app-consistent.md).<br/><br/> Podczas przywracania można utworzyć nową maszynę wirtualną, przywrócić dysk i użyć jej do utworzenia maszyny wirtualnej lub przywrócić dysk i użyć go do zastąpienia dysku na istniejącej maszynie wirtualnej. Można również przywrócić poszczególne pliki i foldery.
Tworzenie kopii zapasowych maszyn wirtualnych platformy Azure z systemem Linux przy użyciu agenta MARS | Nieobsługiwane.<br/><br/> Agenta usługi MARS można zainstalować tylko na maszynach z systemem Windows.
Tworzenie kopii zapasowych maszyn wirtualnych systemu Linux platformy Azure przy użyciu programu DPM/serwera usługi MAB | Nieobsługiwane.
Tworzenie kopii zapasowych maszyn wirtualnych platformy Azure przy użyciu punktów instalacji Docker | Obecnie Azure Backup nie obsługuje wykluczenia punktów instalacji platformy Docker, ponieważ są one instalowane w różnych ścieżkach za każdym razem.

## <a name="operating-system-support-linux"></a>Obsługa systemu operacyjnego (Linux)

W przypadku kopii zapasowych w systemie Linux dla maszyny wirtualnej platformy Azure Azure Backup obsługuje listę [dystrybucji systemu Linux potwierdzonych przez platformę Azure](../virtual-machines/linux/endorsed-distros.md). . Weź pod uwagę następujące kwestie:

- Azure Backup nie obsługuje podstawowego systemu operacyjnego Linux.
- Usługa Azure Backup nie obsługuje 32-bitowych systemów operacyjnych.
- Inne dystrybucje systemu Linux mogą współdziałać, o ile [Agent maszyny wirtualnej platformy Azure dla systemu Linux](../virtual-machines/extensions/agent-linux.md) jest dostępny na maszynie wirtualnej, o ile Język Python jest obsługiwany.
- Azure Backup nie obsługuje maszyny wirtualnej z systemem Linux skonfigurowanej przez serwer proxy, jeśli nie ma zainstalowanej wersji 2,7 języka Python.
- Azure Backup nie obsługuje tworzenia kopii zapasowych plików systemu plików NFS zainstalowanych z magazynu lub z dowolnego innego serwera NFS do maszyn z systemem Linux lub Windows. Tworzy on tylko kopie zapasowe dysków podłączonych lokalnie do maszyny wirtualnej.

## <a name="backup-frequency-and-retention"></a>Częstotliwość i przechowywanie kopii zapasowych

**Ustawienie** | **Limity**
--- | ---
Maksymalna liczba punktów odzyskiwania na chronione wystąpienie (maszyna/obciążenie) | 9999.
Maksymalny czas wygaśnięcia dla punktu odzyskiwania | Brak limitu (99 lat).
Maksymalna częstotliwość tworzenia kopii zapasowych w magazynie (rozszerzenie maszyny wirtualnej platformy Azure) | Raz dziennie.
Maksymalna częstotliwość tworzenia kopii zapasowych w magazynie (Agent MARS) | Trzy kopie zapasowe dziennie.
Maksymalna częstotliwość wykonywania kopii zapasowych w programie DPM/usłudze MABS | Co 15 minut dla SQL Server.<br/><br/> Raz na godzinę dla innych obciążeń.
Przechowywanie punktów odzyskiwania | Codziennie, co tydzień, co miesiąc i co rok.
Maksymalny okres przechowywania | Zależnie od częstotliwości wykonywania kopii zapasowych.
Punkty odzyskiwania na dysku programu DPM/usługi MABS | 64 dla serwerów plików i 448 dla serwerów aplikacji.<br/><br/> Punkty odzyskiwania na taśmie nie mają ograniczeń w przypadku lokalnego programu DPM.

## <a name="supported-restore-methods"></a>Obsługiwane metody przywracania

**Opcja przywracania** | **Szczegóły**
--- | ---
**Tworzenie nowej maszyny wirtualnej** | Szybko tworzy i pobiera z punktu przywracania skonfigurowaną i uruchomioną podstawową maszynę wirtualną.<br/><br/> Możesz określić nazwę dla maszyny wirtualnej, wybrać grupę zasobów i sieć wirtualną (VNet), w której zostanie umieszczona, a następnie określić konto magazynu dla przywróconej maszyny wirtualnej. Nową maszynę wirtualną należy utworzyć w tym samym regionie co źródłową maszynę wirtualną.
**Przywracanie dysku** | Przywraca dysk maszyny wirtualnej, za pomocą którego można następnie utworzyć nową maszynę wirtualną.<br/><br/> Usługa Azure Backup udostępnia szablon ułatwiający dostosowanie i utworzenie maszyny wirtualnej. <br/><br> Zadanie przywracania generuje szablon, który można pobrać i użyć, aby określić niestandardowe ustawienia maszyny wirtualnej i utworzyć maszynę wirtualną.<br/><br/> Dyski są kopiowane do określonej grupy zasobów.<br/><br/> Alternatywnie możesz dołączyć dysk do istniejącej maszyny wirtualnej lub utworzyć nową maszynę wirtualną przy użyciu programu PowerShell.<br/><br/> Ta opcja jest przydatna, jeśli chcesz dostosować maszynę wirtualną, dodać ustawienia konfiguracji, których nie było w czasie tworzenia kopii zapasowej, lub dodać ustawienia, które należy skonfigurować za pomocą szablonu lub programu PowerShell.
**Zamiana istniejącego** | Można przywrócić dysk i użyć go do zamienienia dysku na istniejącej maszynie wirtualnej.<br/><br/> Wymaga to istnienia bieżącej maszyny wirtualnej. Jeśli została ona usunięta, nie można użyć tej opcji.<br/><br/> Azure Backup tworzy migawkę istniejącej maszyny wirtualnej przed zastąpieniem dysku i zapisuje ją w określonej lokalizacji przemieszczania. Istniejące dyski połączone z maszyną wirtualną są zastępowane wybranym punktem przywracania.<br/><br/> Migawka jest kopiowana do magazynu i zachowywana zgodnie z zasadami przechowywania. <br/><br/> Po zakończeniu operacji Zamień dysk oryginalny dysk jest zachowywany w grupie zasobów. Możesz wybrać opcję ręcznego usuwania dysków oryginalnych, jeśli nie są one potrzebne. <br/><br/>Zastąp istniejące jest obsługiwane dla nieszyfrowanych zarządzanych maszyn wirtualnych oraz dla maszyn wirtualnych [utworzonych przy użyciu obrazów niestandardowych](https://azure.microsoft.com/resources/videos/create-a-custom-virtual-machine-image-in-azure-resource-manager-with-powershell/). Nie jest on obsługiwany w przypadku dysków niezarządzanych i maszyn wirtualnych, klasycznych maszyn wirtualnych i [maszyn wirtualnych uogólnionych](../virtual-machines/windows/capture-image-resource.md).<br/><br/> Jeśli punkt przywracania ma więcej lub mniej dysków niż bieżąca maszyna wirtualna, liczba dysków w punkcie przywracania będzie uwzględniać tylko konfigurację maszyny wirtualnej.<br><br> Polecenie Zamień istniejące jest również obsługiwane w przypadku maszyn wirtualnych z połączonymi zasobami, takich jak [zarządzane tożsamości przypisane przez użytkownika](../active-directory/managed-identities-azure-resources/overview.md) i [Key Vault](../key-vault/general/overview.md).
**Między regionami (region pomocniczy)** | W celu przywrócenia maszyn wirtualnych platformy Azure w regionie pomocniczym, które jest [sparowanym regionem platformy Azure](../best-practices-availability-paired-regions.md#what-are-paired-regions), można użyć funkcji przywracania między regionami.<br><br> Jeśli kopia zapasowa jest wykonywana w regionie pomocniczym, można przywrócić wszystkie maszyny wirtualne platformy Azure dla wybranego punktu odzyskiwania.<br><br> Ta funkcja jest dostępna dla poniższych opcji:<br> <li> [Tworzenie maszyny wirtualnej](./backup-azure-arm-restore-vms.md#create-a-vm) <br> <li> [Przywracanie dysków](./backup-azure-arm-restore-vms.md#restore-disks) <br><br> Nie obsługujemy obecnie opcji [Zamień istniejące dyski](./backup-azure-arm-restore-vms.md#replace-existing-disks) .<br><br> Uprawnienia<br> Operacja przywracania w regionie pomocniczym może być wykonywana przez administratorów kopii zapasowych i administratorów aplikacji.

## <a name="support-for-file-level-restore"></a>Obsługa przywracania na poziomie plików

**Przywracanie** | **Obsługiwane**
--- | ---
Przywracanie plików w różnych systemach operacyjnych | Można przywracać pliki na dowolnym komputerze, który ma ten sam (lub zgodny) system operacyjny, co maszyna wirtualna z kopią zapasową. Zobacz [tabelę zgodnych systemów operacyjnych](backup-azure-restore-files-from-vm.md#step-3-os-requirements-to-successfully-run-the-script).
Przywracanie plików z szyfrowanych maszyn wirtualnych | Nieobsługiwane.
Przywracanie plików z kont magazynu z ograniczeniami w sieci | Nieobsługiwane.
Przywracanie plików na maszynach wirtualnych przy użyciu funkcji miejsca do magazynowania systemu Windows | Przywracanie nie jest obsługiwane na tej samej maszynie wirtualnej.<br/><br/> Zamiast tego należy przywrócić pliki na zgodnej maszynie wirtualnej.
Przywracanie plików na maszynie wirtualnej z systemem Linux przy użyciu tablic LVM/RAID | Przywracanie nie jest obsługiwane na tej samej maszynie wirtualnej.<br/><br/> Przywracanie na zgodnej maszynie wirtualnej.
Przywracanie plików przy użyciu specjalnych ustawień sieci | Przywracanie nie jest obsługiwane na tej samej maszynie wirtualnej. <br/><br/> Przywracanie na zgodnej maszynie wirtualnej.
Przywróć pliki z dysku udostępnionego, dysku tymczasowego, deduplikowanego dysku, Ultra Disk i Disk z włączonym akceleratorem zapisu | Przywracanie nie jest obsługiwane, <br/><br/>Zobacz [Obsługa magazynu maszyn wirtualnych platformy Azure](#vm-storage-support).

## <a name="support-for-vm-management"></a>Obsługa zarządzania maszyną wirtualną

Poniższa tabela zawiera podsumowanie obsługi kopii zapasowych podczas zadań zarządzania maszyną wirtualną, takich jak dodawanie lub zastępowanie dysków maszyn wirtualnych.

**Przywracanie** | **Obsługiwane**
--- | ---
Przywróć w ramach subskrypcji/regionu/strefy. | Nieobsługiwane.
Przywracanie do istniejącej maszyny wirtualnej | Użyj opcji Zamień dysk.
Przywróć dysk z włączonym kontem magazynu dla usługi Azure szyfrowanie usługi Storage (SSE) | Nieobsługiwane.<br/><br/> Przywracanie do konta, które nie ma włączonej funkcji SSE.
Przywracanie do mieszanych kont magazynu |Nieobsługiwane.<br/><br/> Na podstawie typu konta magazynu wszystkie przywrócone dyski będą w warstwie Premium lub standardowa, a nie mieszane.
Przywracanie maszyny wirtualnej bezpośrednio do zestawu dostępności | W przypadku dysków zarządzanych można przywrócić dysk i użyć opcji zestawu dostępności w szablonie.<br/><br/> Nieobsługiwane w przypadku dysków niezarządzanych. W przypadku dysków niezarządzanych Przywróć dysk, a następnie utwórz maszynę wirtualną w zestawie dostępności.
Przywróć kopię zapasową niezarządzanych maszyn wirtualnych po uaktualnieniu do zarządzanej maszyny wirtualnej| Obsługiwane.<br/><br/> Można przywrócić dyski, a następnie utworzyć zarządzaną maszynę wirtualną.
Przywróć maszynę wirtualną do punktu przywracania przed przeprowadzeniem migracji maszyny wirtualnej na dyski zarządzane | Obsługiwane.<br/><br/> Przywracasz do dysków niezarządzanych (domyślnie), przekonwertuj przywrócone dyski na dysk zarządzany i Utwórz maszynę wirtualną z dyskami zarządzanymi.
Przywróć maszynę wirtualną, która została usunięta. | Obsługiwane.<br/><br/> Można przywrócić maszynę wirtualną z punktu odzyskiwania.
Przywracanie maszyny wirtualnej kontrolera domeny  | Obsługiwane. Aby uzyskać szczegółowe informacje, zobacz [Przywracanie maszyn wirtualnych kontrolera domeny](backup-azure-arm-restore-vms.md#restore-domain-controller-vms).
Przywróć MASZYNę wirtualną w innej sieci wirtualnej |Obsługiwane.<br/><br/> Sieć wirtualna musi znajdować się w tej samej subskrypcji i regionie.

## <a name="vm-compute-support"></a>Obsługa obliczeń maszyn wirtualnych

**Środowisko obliczeniowe** | **Pomoc techniczna**
--- | ---
Rozmiar maszyny wirtualnej |Rozmiar maszyny wirtualnej platformy Azure z co najmniej 2 rdzeniami procesora i 1 GB pamięci RAM.<br/><br/> [Dowiedz się więcej.](../virtual-machines/sizes.md)
Tworzenie kopii zapasowych maszyn wirtualnych w [zestawach dostępności](../virtual-machines/availability.md#availability-sets) | Obsługiwane.<br/><br/> Nie można przywrócić maszyny wirtualnej w dostępnym zestawie przy użyciu opcji, aby szybko utworzyć maszynę wirtualną. Zamiast tego, podczas przywracania maszyny wirtualnej Przywróć dysk i użyj go do wdrożenia maszyny wirtualnej lub Przywróć dysk i użyj go, aby zastąpić istniejący dysk.
Tworzenie kopii zapasowych maszyn wirtualnych wdrożonych przy użyciu [korzyści z używania hybrydowego (centrum)](../virtual-machines/windows/hybrid-use-benefit-licensing.md) | Obsługiwane.
Tworzenie kopii zapasowych maszyn wirtualnych wdrażanych z poziomu [portalu Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps?filters=virtual-machine-images)<br/><br/> (Opublikowane przez firmę Microsoft, inna firma) |Obsługiwane.<br/><br/> Na maszynie wirtualnej musi być uruchomiony obsługiwany system operacyjny.<br/><br/> Podczas odzyskiwania plików na maszynie wirtualnej można przywrócić tylko zgodny system operacyjny (nie wcześniejszy niż starszy lub nowszy). Nie przywracamy maszyn wirtualnych portalu Azure Marketplace jako maszyn wirtualnych, ponieważ wymagają one informacji o zakupie. Są one przywracane tylko jako dyski.
Tworzenie kopii zapasowych maszyn wirtualnych wdrożonych z obrazu niestandardowego (innych firm) |Obsługiwane.<br/><br/> Na maszynie wirtualnej musi być uruchomiony obsługiwany system operacyjny.<br/><br/> Podczas odzyskiwania plików na maszynie wirtualnej można przywrócić tylko zgodny system operacyjny (nie wcześniejszy niż starszy lub nowszy).
Tworzenie kopii zapasowych maszyn wirtualnych migrowanych do platformy Azure| Obsługiwane.<br/><br/> Aby można było utworzyć kopię zapasową maszyny wirtualnej, Agent maszyny wirtualnej musi być zainstalowany na migrowanej maszynie.
Tworzenie kopii zapasowej spójności między MASZYNami wirtualnymi | Azure Backup nie zapewnia spójności danych i aplikacji na wielu maszynach wirtualnych.
Tworzenie kopii zapasowej przy użyciu [ustawień diagnostycznych](../azure-monitor/essentials/platform-logs-overview.md)  | Ich. <br/><br/> Jeśli przywracanie maszyny wirtualnej platformy Azure z ustawieniami diagnostycznymi jest wyzwalane przy użyciu opcji [Utwórz nową](backup-azure-arm-restore-vms.md#create-a-vm) , przywracanie nie powiedzie się.
Przywracanie przypiętych do strefy maszyn wirtualnych | Obsługiwane (w przypadku maszyny wirtualnej, której kopia zapasowa jest tworzona po 2019 Jan, a dostępne są [strefy dostępności](https://azure.microsoft.com/global-infrastructure/availability-zones/) ).<br/><br/>Obecnie obsługujemy przywracanie do tej samej strefy, która jest przypięta do maszyn wirtualnych. Jeśli jednak strefa jest niedostępna z powodu awarii, przywracanie zakończy się niepowodzeniem.
Maszyny wirtualne Gen2 | Obsługiwane <br> Azure Backup obsługuje tworzenie kopii zapasowych i przywracanie [maszyn wirtualnych Gen2](https://azure.microsoft.com/updates/generation-2-virtual-machines-in-azure-public-preview/). Po przywróceniu tych maszyn wirtualnych z punktu odzyskiwania są one przywracane jako [maszyny wirtualne Gen2](https://azure.microsoft.com/updates/generation-2-virtual-machines-in-azure-public-preview/).
Tworzenie kopii zapasowych maszyn wirtualnych platformy Azure z blokadami | Nieobsługiwane dla niezarządzanych maszyn wirtualnych. <br><br> Obsługiwane w przypadku zarządzanych maszyn wirtualnych.
[Maszyny wirtualne typu spot](../virtual-machines/spot-vms.md) | Ich. Azure Backup przywraca maszyny wirtualne maszyn wirtualnych jako zwykłe maszyny wirtualne platformy Azure.
[Dedykowany Host platformy Azure](../virtual-machines/dedicated-hosts.md) | Obsługiwane
Konfiguracja funkcji miejsca do magazynowania systemu Windows dla autonomicznych maszyn wirtualnych platformy Azure | Obsługiwane

## <a name="vm-storage-support"></a>Obsługa magazynu maszyn wirtualnych

**Składnik** | **Pomoc techniczna**
--- | ---
Dyski z danymi maszyn wirtualnych platformy Azure | Obsługa tworzenia kopii zapasowych maszyn wirtualnych platformy Azure z maksymalnie 32 dyskami.<br><br> Obsługa kopii zapasowych maszyn wirtualnych platformy Azure z dyskami niezarządzanymi lub klasycznymi maszynami wirtualnymi odbywa się tylko do 16 dysków.
Rozmiar dysku danych | Rozmiar poszczególnych dysków może należeć do 32 TB i maksymalnie 256 TB łączny dla wszystkich dysków w maszynie wirtualnej.
Typ magazynu | HDD w warstwie Standardowa, SSD w warstwie Standardowa, SSD w warstwie Premium.
Dyski zarządzane | Obsługiwane.
Zaszyfrowane dyski | Obsługiwane.<br/><br/> Można utworzyć kopię zapasową maszyn wirtualnych platformy Azure z włączonym Azure Disk Encryption (z aplikacją usługi Azure AD lub bez niej).<br/><br/> Zaszyfrowane maszyny wirtualne nie mogą zostać odzyskane na poziomie plików/folderów. Należy odzyskać całą maszynę wirtualną.<br/><br/> Możesz włączyć szyfrowanie na maszynach wirtualnych, które są już chronione przez Azure Backup.
Dyski z włączonym akcelerator zapisu | Od 23 listopada 2020, obsługiwane tylko w regionach Korea Środkowa (KRC) i Północna Republika Południowej Afryki (SAN) dla ograniczonej liczby subskrypcji. W przypadku tych obsługiwanych subskrypcji Azure Backup będzie tworzyć kopie zapasowe maszyn wirtualnych z włączonym przyspieszeniem zapisu (WA) podczas tworzenia kopii zapasowej.<br><br>W przypadku nieobsługiwanych regionów na maszynie wirtualnej jest wymagane połączenie z Internetem, aby można było wykonać migawki Virtual Machines z włączonym WA.<br><br> **Ważna Uwaga**: w tych nieobsługiwanych regionach maszyny wirtualne z dyskami wa muszą mieć łączność z Internetem dla pomyślnej kopii zapasowej (mimo że te dyski są wykluczone z kopii zapasowej).
Tworzenie kopii zapasowej & przywracanie deduplikowanych maszyn wirtualnych/dysków | Azure Backup nie obsługuje deduplikacji. Aby uzyskać więcej informacji, zobacz ten [artykuł](./backup-support-matrix.md#disk-deduplication-support) <br/> <br/>  -Azure Backup nie deduplikowania między maszynami wirtualnymi w magazynie Recovery Services <br/> <br/>  — Jeśli podczas przywracania istnieją maszyny wirtualne w stanie deduplikacji, nie można przywrócić plików, ponieważ magazyn nie rozumie tego formatu. Można jednak pomyślnie wykonać pełne przywracanie maszyny wirtualnej.
Dodawanie dysku do chronionej maszyny wirtualnej | Obsługiwane.
Zmień rozmiar dysku na chronionej maszynie wirtualnej | Obsługiwane.
Magazyn udostępniony| Tworzenie kopii zapasowych maszyn wirtualnych przy użyciu udostępniony wolumin klastra (CSV) lub Scale-Out serwera plików nie jest obsługiwane. Podczas tworzenia kopii zapasowej mogą wystąpić błędy autorów woluminów. W przypadku przywracania dyski zawierające woluminy woluminów CSV mogą nie zostać dołączone.
[Dyski udostępnione](../virtual-machines/disks-shared-enable.md) | Nieobsługiwane.
SSD w warstwie Ultra dyski | Nieobsługiwane. Aby uzyskać więcej informacji, zobacz te [ograniczenia](selective-disk-backup-restore.md#limitations).
[Dyski tymczasowe](../virtual-machines/managed-disks-overview.md#temporary-disk) | Kopie zapasowe dysków tymczasowych nie są tworzone przez Azure Backup.
Dyski interfejsu NVMe/tymczasowych | Nieobsługiwane.

## <a name="vm-network-support"></a>Obsługa sieci maszyn wirtualnych

**Składnik** | **Pomoc techniczna**
--- | ---
Liczba interfejsów sieciowych (nic) | Maksymalna liczba kart sieciowych obsługiwanych przez określony rozmiar maszyny wirtualnej platformy Azure.<br/><br/> Karty sieciowe są tworzone podczas tworzenia maszyny wirtualnej podczas procesu przywracania.<br/><br/> Liczba kart sieciowych na przywracanej maszynie wirtualnej odzwierciedla liczbę kart sieciowych w maszynie wirtualnej po włączeniu ochrony. Usuwanie kart sieciowych po włączeniu ochrony nie ma wpływu na liczbę.
Zewnętrzny/wewnętrzny moduł równoważenia obciążenia |Obsługiwane. <br/><br/> [Dowiedz się więcej](backup-azure-arm-restore-vms.md#restore-vms-with-special-configurations) o przywracaniu maszyn wirtualnych przy użyciu specjalnych ustawień sieci.
Wiele zarezerwowanych adresów IP |Obsługiwane. <br/><br/> [Dowiedz się więcej](backup-azure-arm-restore-vms.md#restore-vms-with-special-configurations) o przywracaniu maszyn wirtualnych przy użyciu specjalnych ustawień sieci.
Maszyny wirtualne z wieloma kartami sieciowymi| Obsługiwane. <br/><br/> [Dowiedz się więcej](backup-azure-arm-restore-vms.md#restore-vms-with-special-configurations) o przywracaniu maszyn wirtualnych przy użyciu specjalnych ustawień sieci.
Maszyny wirtualne z publicznymi adresami IP| Obsługiwane.<br/><br/> Skojarz istniejący publiczny adres IP z kartą sieciową lub Utwórz adres i skojarz go z kartą sieciową po zakończeniu przywracania.
Sieciowa Grupa zabezpieczeń (sieciowej grupy zabezpieczeń) na karcie sieciowej/podsieci. |Obsługiwane.
Statyczny adres IP | Nieobsługiwane.<br/><br/> Nowa maszyna wirtualna utworzona na podstawie punktu przywracania ma przypisany dynamiczny adres IP.<br/><br/> W przypadku klasycznych maszyn wirtualnych nie można utworzyć kopii zapasowej maszyny wirtualnej z zastrzeżonym adresem IP i bez zdefiniowanego punktu końcowego.
Dynamiczny adres IP |Obsługiwane.<br/><br/> Jeśli karta sieciowa na źródłowej maszynie wirtualnej używa dynamicznego adresowania IP, domyślnie karta sieciowa na przywróconej maszynie wirtualnej będzie używać tego programu.
Azure Traffic Manager| Obsługiwane.<br/><br/>Jeśli kopia zapasowa maszyny wirtualnej znajduje się w Traffic Manager, ręcznie Dodaj przywróconą maszynę wirtualną do tego samego wystąpienia Traffic Manager.
Azure DNS |Obsługiwane.
Niestandardowe DNS |Obsługiwane.
Łączność wychodząca za pośrednictwem serwera proxy HTTP | Obsługiwane.<br/><br/> Uwierzytelniony serwer proxy nie jest obsługiwany.
Punkty końcowe usługi dla sieci wirtualnej| Obsługiwane.<br/><br/> Ustawienia zapory i konta magazynu sieci wirtualnej powinny zezwalać na dostęp ze wszystkich sieci.

## <a name="vm-security-and-encryption-support"></a>Obsługa zabezpieczeń i szyfrowania maszyn wirtualnych

Usługa Azure Backup obsługuje szyfrowanie danych podczas przesyłania i danych magazynowanych:

Ruch sieciowy do platformy Azure:

- Ruch kopii zapasowych z serwerów do magazynu Recovery Services jest szyfrowany przy użyciu Advanced Encryption Standard 256.
- Dane kopii zapasowej są przesyłane za pośrednictwem bezpiecznego połączenia HTTPS.
- W magazynie usługi Recovery Services dane kopii zapasowych są przechowywane w postaci zaszyfrowanej.
- Tylko ty masz klucz szyfrowania do odblokowania tych danych. Firma Microsoft nie może odszyfrować danych kopii zapasowej w żadnym punkcie.

  > [!WARNING]
  > Po skonfigurowaniu magazynu tylko ty masz dostęp do klucza szyfrowania. Firma Microsoft nigdy nie przechowuje kopii i nie ma dostępu do klucza. W przypadku utraty klucza firma Microsoft nie może odzyskać danych kopii zapasowej.

Bezpieczeństwo danych:

- Podczas tworzenia kopii zapasowych maszyn wirtualnych platformy Azure należy skonfigurować szyfrowanie *w ramach* maszyny wirtualnej.
- Azure Backup obsługuje Azure Disk Encryption, który używa funkcji BitLocker na maszynach wirtualnych z systemem Windows oraz zarządzania urządzeniami **Crypt** na komputerach z systemem Linux.
- Na zapleczu usługa Azure Backup używa [szyfrowania usługi Azure Storage](../storage/common/storage-service-encryption.md), które chroni dane magazynowane.

**Maszyna** | **Przesyłanie** | **Magazynowanie**
--- | --- | ---
Lokalne maszyny z systemem Windows bez programu DPM/usługi MABS | ![Tak][green] | ![Tak][green]
Maszyny wirtualne platformy Azure | ![Tak][green] | ![Tak][green]
Lokalne maszyny wirtualne/maszyny wirtualne platformy Azure z programem DPM | ![Tak][green] | ![Tak][green]
Lokalne maszyny wirtualne/maszyny wirtualne platformy Azure z usługą MABS | ![Tak][green] | ![Tak][green]

## <a name="vm-compression-support"></a>Obsługa kompresji maszyny wirtualnej

Funkcja Backup obsługuje kompresję ruchu kopii zapasowej, jak przedstawiono w poniższej tabeli. . Weź pod uwagę następujące kwestie:

- W przypadku maszyn wirtualnych platformy Azure rozszerzenie maszyny wirtualnej odczytuje dane bezpośrednio z konta usługi Azure Storage w sieci magazynowania. Nie jest konieczne kompresowanie tego ruchu.
- Jeśli używasz programu DPM lub serwera usługi MAB, możesz zaoszczędzić przepustowość, kompresując dane przed utworzeniem ich kopii zapasowej w programie DPM/serwera usługi MAB.

**Maszyna** | **Kompresja do usługi MABS/programu DPM (TCP)** | **Kompresuj do magazynu (HTTPS)**
--- | --- | ---
Lokalne maszyny z systemem Windows bez programu DPM/usługi MABS | Nie dotyczy | ![Yes][green]
Maszyny wirtualne platformy Azure | Nie dotyczy | NA
Lokalne maszyny wirtualne/maszyny wirtualne platformy Azure z programem DPM | ![Tak][green] | ![Tak][green]
Lokalne maszyny wirtualne/maszyny wirtualne platformy Azure z usługą MABS | ![Tak][green] | ![Tak][green]

## <a name="next-steps"></a>Następne kroki

- [Tworzenie kopii zapasowych maszyn wirtualnych platformy Azure](backup-azure-arm-vms-prepare.md).
- [Bezpośrednie wykonywanie kopii zapasowych maszyn z systemem Windows](tutorial-backup-windows-server-to-azure.md) bez serwera kopii zapasowych.
- [Konfigurowanie usługi MABS](backup-azure-microsoft-azure-backup.md) na potrzeby kopii zapasowych na platformie Azure, a następnie wykonywanie kopii zapasowych obciążeń w usłudze MABS.
- [Konfigurowanie programu DPM](backup-azure-dpm-introduction.md) na potrzeby kopii zapasowych na platformie Azure, a następnie wykonywanie kopii zapasowych obciążeń w programie DPM.

[green]: ./media/backup-support-matrix/green.png
[yellow]: ./media/backup-support-matrix/yellow.png
[red]: ./media/backup-support-matrix/red.png