---
title: Rozwiązywanie problemów z agentami i rozszerzeniami
description: Objawy, przyczyny i rozwiązania błędów Azure Backup związanych z agentami, rozszerzeniem i dyskami.
ms.topic: troubleshooting
ms.date: 07/05/2019
ms.service: backup
ms.openlocfilehash: 0313394ad149460f82c98c63cab95b922b4a3da2
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/09/2021
ms.locfileid: "102519609"
---
# <a name="troubleshoot-azure-backup-failure-issues-with-the-agent-or-extension"></a>Rozwiązywanie problemów z błędem Azure Backup: problemy z agentem lub rozszerzeniem

W tym artykule opisano kroki rozwiązywania problemów, które mogą pomóc w rozwiązywaniu Azure Backup błędów związanych z komunikacją z agentem i rozszerzeniem maszyny wirtualnej.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="step-by-step-guide-to-troubleshoot-backup-failures"></a>Przewodnik krok po kroku dotyczący rozwiązywania problemów z błędami kopii zapasowych

Najczęstsze błędy tworzenia kopii zapasowych można rozwiązać, wykonując następujące czynności dotyczące rozwiązywania problemów:

### <a name="step-1-check-azure-vm-health"></a>Krok 1. Sprawdzanie kondycji maszyny wirtualnej platformy Azure

- **Upewnij się, że stan aprowizacji maszyny wirtualnej platformy Azure to "uruchomiona"**: Jeśli [stan aprowizacji maszyny wirtualnej](../virtual-machines/states-billing.md) znajduje się w stanie **zatrzymania/cofania przydziału/aktualizacji** , będzie zakłócał operację tworzenia kopii zapasowej. Otwórz *Azure Portal > maszynie wirtualnej > przegląd >* i sprawdź stan maszyny wirtualnej, aby upewnić się, że jest **uruchomiona**  , a następnie spróbuj ponownie wykonać operację tworzenia kopii zapasowej.
- **Przejrzyj oczekujące aktualizacje systemu operacyjnego lub Uruchom** ponownie: Upewnij się, że nie ma żadnych oczekujących aktualizacji systemu operacyjnego lub nie ma oczekujących ponownych uruchomień na maszynie wirtualnej.

### <a name="step-2-check-azure-vm-guest-agent-service-health"></a>Krok 2. Sprawdzanie kondycji usługi agenta gościa maszyny wirtualnej platformy Azure

- **Upewnij się, że usługa agenta gościa maszyny wirtualnej platformy Azure jest uruchomiona i aktualna**:
  - Na maszynie wirtualnej z systemem Windows:
    - Przejdź do programu **Services. msc** i upewnij się, że **Usługa agenta gościa maszyny wirtualnej platformy Microsoft Azure** jest uruchomiona. Upewnij się również, że zainstalowano [najnowszą wersję](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409) programu. Aby dowiedzieć się więcej, zobacz [problemy z agentem gościa maszyny wirtualnej systemu Windows](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms).
    - Agent maszyny wirtualnej platformy Azure jest instalowany domyślnie na wszystkich maszynach wirtualnych z systemem Windows wdrożonych z obrazu portalu Azure Marketplace z poziomu witryny Portal, programu PowerShell, interfejsu wiersza polecenia lub szablonu Azure Resource Manager. [Ręczna instalacja agenta](../virtual-machines/extensions/agent-windows.md#manual-installation) może być konieczna podczas tworzenia niestandardowego obrazu maszyny wirtualnej wdrożonego na platformie Azure.
    - Zapoznaj się z matrycą pomocy technicznej, aby sprawdzić, czy maszyna wirtualna jest uruchamiana w [obsługiwanym systemie operacyjnym Windows](backup-support-matrix-iaas.md#operating-system-support-windows).
  - Na maszynie wirtualnej z systemem Linux
    - Upewnij się, że usługa agenta gościa maszyny wirtualnej platformy Azure jest uruchomiona, wykonując polecenie `ps-e` . Upewnij się również, że zainstalowano [najnowszą wersję](../virtual-machines/extensions/update-linux-agent.md) programu. Aby dowiedzieć się więcej, zobacz [problemy z agentem gościa maszyny wirtualnej z systemem Linux](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms).
    - Upewnij się, że [zależności agenta maszyny wirtualnej z systemem Linux w pakietach systemowych](../virtual-machines/extensions/agent-linux.md#requirements) mają obsługiwaną konfigurację. Na przykład: obsługiwana wersja języka Python to 2,6 i nowsze.
    - Zapoznaj się z matrycą pomocy technicznej, aby sprawdzić, czy maszyna wirtualna jest uruchamiana w [obsługiwanym systemie operacyjnym Linux.](backup-support-matrix-iaas.md#operating-system-support-linux)

### <a name="step-3-check-azure-vm-extension-health"></a>Krok 3. Sprawdzanie kondycji rozszerzenia maszyny wirtualnej platformy Azure

- Upewnij się, że **wszystkie rozszerzenia maszyny wirtualnej platformy Azure znajdują się w stanie "Inicjowanie obsługi" zakończyło się pomyślnie**: Jeśli dowolne rozszerzenie jest w stanie niepowodzenia, może zakłócać tworzenie kopii zapasowej.
- *Otwórz Azure Portal > maszynę wirtualną ustawienia > > rozszerzenia >* rozszerzenia i sprawdź, czy wszystkie rozszerzenia zostały **pomyślnie wykonane** .
- Upewnij się, że wszystkie [problemy z rozszerzeniem](../virtual-machines/extensions/overview.md#troubleshoot-extensions) zostały rozwiązane, a następnie ponów operację tworzenia kopii zapasowej.
- **Upewnij się, że aplikacja systemowa modelu COM+** jest uruchomiona. Ponadto **usługa Distributed Transaction Coordinator** powinna działać jako **konto usługi sieciowej**. Wykonaj kroki opisane w tym artykule, aby [rozwiązać problemy związane z modelem COM+ i usługą MSDTC](backup-azure-vms-troubleshoot.md#extensionsnapshotfailedcom--extensioninstallationfailedcom--extensioninstallationfailedmdtc---extension-installationoperation-failed-due-to-a-com-error).

### <a name="step-4-check-azure-backup-vm-extension-health"></a>Krok 4. Sprawdzenie kondycji rozszerzenia maszyny wirtualnej Azure Backup

Azure Backup używa rozszerzenia migawki maszyny wirtualnej w celu utworzenia kopii zapasowej spójnej na poziomie aplikacji maszyny wirtualnej platformy Azure. Azure Backup zainstaluje rozszerzenie w ramach pierwszej zaplanowanej kopii zapasowej wyzwalanej po włączeniu kopii zapasowej.

- **Upewnij się, że rozszerzenie VMSnapshot nie jest w stanie niepowodzenia**: wykonaj kroki opisane w tej [sekcji](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md#usererrorvmprovisioningstatefailed---the-vm-is-in-failed-provisioning-state) , aby sprawdzić i upewnić się, że rozszerzenie Azure Backup jest w dobrej kondycji.

- **Sprawdź, czy program antywirusowy blokuje rozszerzenie**: Niektóre programy antywirusowe mogą uniemożliwiać wykonywanie rozszerzeń.
  
  W momencie niepowodzenia tworzenia kopii zapasowej Sprawdź, czy w programie * znajdują się wpisy dziennika,**Podgląd zdarzeń Dzienniki aplikacji** _ *_Nazwa aplikacji powodującej błąd: IaaSBcdrExtension.exe_* *. Jeśli zobaczysz wpisy, może to oznaczać, że program antywirusowy skonfigurowany na maszynie wirtualnej ograniczy wykonywanie rozszerzenia kopii zapasowej. Przetestuj przez wykluczenie następujących katalogów w konfiguracji programu antywirusowego, a następnie spróbuj ponownie wykonać operację tworzenia kopii zapasowej.
  - `C:\Packages\Plugins\Microsoft.Azure.RecoveryServices.VMSnapshot`
  - `C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.RecoveryServices.VMSnapshot`

- **Sprawdź, czy dostęp do sieci jest wymagany**: pakiety rozszerzeń są pobierane z repozytorium rozszerzeń usługi Azure Storage, a operacje przekazywania stanu rozszerzenia są ogłaszane w usłudze Azure Storage. [Dowiedz się więcej](../virtual-machines/extensions/features-windows.md#network-access).
  - Jeśli korzystasz z nieobsługiwanej wersji agenta, musisz zezwolić na dostęp wychodzący do usługi Azure Storage w tym regionie z maszyny wirtualnej.
  - Jeśli zablokowano dostęp do `168.63.129.16` korzystania z zapory gościa lub z serwerem proxy, rozszerzenia zakończą się niepowodzeniem, niezależnie od powyższego. Porty 80, 443 i 32526 są wymagane, [Dowiedz się więcej](../virtual-machines/extensions/features-windows.md#network-access).

- **Upewnij się, że usługa DHCP jest włączona wewnątrz maszyny wirtualnej gościa**: jest to wymagane, aby uzyskać adres hosta lub sieci szkieletowej z serwera DHCP na potrzeby tworzenia kopii zapasowej maszyny wirtualnej IaaS. Jeśli potrzebujesz statycznego prywatnego adresu IP, należy go skonfigurować za pomocą Azure Portal lub PowerShell i upewnić się, że opcja DHCP wewnątrz maszyny wirtualnej jest włączona, [Dowiedz się więcej](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken).

- **Upewnij się, że usługa składnika zapisywania usługi VSS jest uruchomiona**: wykonaj następujące kroki, aby [rozwiązać problemy z modułem zapisywania usługi VSS](backup-azure-vms-troubleshoot.md#extensionfailedvsswriterinbadstate---snapshot-operation-failed-because-vss-writers-were-in-a-bad-state).
- **Postępuj zgodnie z najlepszymi wskazówkami dotyczącymi tworzenia kopii zapasowych**: Zapoznaj się z [najlepszymi rozwiązaniami, aby włączyć](backup-azure-vms-introduction.md#best-practices)
- Zapoznaj się z **zaleceniami dotyczącymi szyfrowanych dysków**: w przypadku włączenia kopii zapasowej dla maszyn wirtualnych z szyfrowanym dyskiem upewnij się, że zostały podane wszystkie wymagane uprawnienia. Aby dowiedzieć się więcej, zobacz [wykonywanie kopii zapasowej i przywracanie zaszyfrowanej maszyny wirtualnej platformy Azure](backup-azure-vms-encryption.md).

## <a name="usererrorguestagentstatusunavailable---vm-agent-unable-to-communicate-with-azure-backup"></a><a name="UserErrorGuestAgentStatusUnavailable-vm-agent-unable-to-communicate-with-azure-backup"></a>UserErrorGuestAgentStatusUnavailable — agent maszyny wirtualnej nie może komunikować się z usługą Azure Backup

**Kod błędu**: UserErrorGuestAgentStatusUnavailable <br>
**Komunikat o błędzie**: Agent maszyny wirtualnej nie może komunikować się z Azure Backup<br>

Agent maszyny wirtualnej platformy Azure może zostać zatrzymany, nieaktualny, w stanie niespójnym lub nie został zainstalowany. Te Stany uniemożliwiają usłudze Azure Backup wywoływanie migawek.

- **Otwórz Azure Portal > maszynie wirtualnej ustawienia > > właściwości** > upewnij się, że **stan** maszyny wirtualnej jest **uruchomiony** i że **Agent** jest **gotowy**. Jeśli Agent maszyny wirtualnej został zatrzymany lub jest w niespójnym stanie, należy ponownie uruchomić agenta.<br>
  - W przypadku maszyn wirtualnych z systemem Windows wykonaj następujące [kroki](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms) , aby ponownie uruchomić agenta gościa.<br>
  - W przypadku maszyn wirtualnych z systemem Linux wykonaj następujące [kroki](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms) , aby ponownie uruchomić agenta gościa.
- **Otwórz Azure Portal > maszyny wirtualnej > ustawienia > rozszerzenia** > upewnij się, że wszystkie rozszerzenia zostały **pomyślnie wykonane** . Jeśli nie, wykonaj następujące [kroki](#usererrorvmprovisioningstatefailed---the-vm-is-in-failed-provisioning-state) , aby rozwiązać ten problem.

## <a name="guestagentsnapshottaskstatuserror---could-not-communicate-with-the-vm-agent-for-snapshot-status"></a>GuestAgentSnapshotTaskStatusError — nie można nawiązać komunikacji z agentem maszyny wirtualnej w celu uzyskania stanu migawki

**Kod błędu**: GuestAgentSnapshotTaskStatusError<br>
**Komunikat o błędzie**: nie można nawiązać komunikacji z agentem maszyny wirtualnej w celu uzyskania stanu migawki <br>

Po zarejestrowaniu i zaplanowaniu maszyny wirtualnej dla usługi Azure Backup Backup rozpocznie zadanie, komunikując się z rozszerzeniem kopii zapasowej maszyny wirtualnej w celu przeprowadzenia migawki określonego momentu. Jeden z następujących warunków może uniemożliwić wyzwolenie migawki. Jeśli migawka nie zostanie wyzwolona, może wystąpić błąd kopii zapasowej. Wykonaj następujące kroki rozwiązywania problemów w podanej kolejności, a następnie ponów próbę wykonania operacji:  

**Przyczyna 1: [Agent jest zainstalowany na maszynie wirtualnej, ale nie odpowiada (dla maszyn wirtualnych z systemem Windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  

**Przyczyna 2: [Agent zainstalowany na maszynie wirtualnej jest nieaktualny (dla maszyn wirtualnych z systemem Linux)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**

**Przyczyna 3: [nie można pobrać stanu migawki lub nie można wykonać migawki](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**

**Przyczyna 4: [nie ustawiono opcji konfiguracji agenta maszyny wirtualnej (dla maszyn wirtualnych z systemem Linux)](#vm-agent-configuration-options-are-not-set-for-linux-vms)**

**Przyczyna 5: [rozwiązanie kontroli aplikacji blokuje IaaSBcdrExtension.exe](#application-control-solution-is-blocking-iaasbcdrextensionexe)**

## <a name="usererrorvmprovisioningstatefailed---the-vm-is-in-failed-provisioning-state"></a>UserErrorVmProvisioningStateFailed — maszyna wirtualna jest w stanie nieudanego aprowizowania

**Kod błędu**: UserErrorVmProvisioningStateFailed<br>
**Komunikat o błędzie**: stan aprowizacji maszyny wirtualnej to niepowodzenie<br>

Ten błąd występuje, gdy jeden z błędów rozszerzenia przełączy maszynę wirtualną w stan niepowodzenia aprowizacji.<br>**Otwórz Azure Portal > maszynę wirtualną ustawienia > > rozszerzenia >** rozszerzenia i sprawdź, czy wszystkie rozszerzenia mają stan **Pomyślne inicjowanie obsługi** . Aby dowiedzieć się więcej, zobacz [Stany aprowizacji](../virtual-machines/states-billing.md).

- Jeśli dowolne rozszerzenie jest w stanie niepowodzenia, może zakłócać tworzenie kopii zapasowej. Upewnij się, że problemy z rozszerzeniem zostały rozwiązane, i spróbuj ponownie wykonać operację tworzenia kopii zapasowej.
- Jeśli stan aprowizacji maszyn wirtualnych jest w stanie aktualizacji, może to zakłócać tworzenie kopii zapasowej. Upewnij się, że jest w dobrej kondycji i spróbuj ponownie wykonać operację tworzenia kopii zapasowej.

## <a name="usererrorrpcollectionlimitreached---the-restore-point-collection-max-limit-has-reached"></a>UserErrorRpCollectionLimitReached — osiągnięto maksymalny limit kolekcji punktów przywracania

**Kod błędu**: UserErrorRpCollectionLimitReached <br>
**Komunikat o błędzie**: Osiągnięto maksymalny limit kolekcji punktów przywracania. <br>

- Ten problem może wystąpić, jeśli istnieje blokada grupy zasobów punktu odzyskiwania uniemożliwiająca automatyczne czyszczenie punktów odzyskiwania.
- Ten problem może również wystąpić w przypadku wyzwolenia wielu kopii zapasowych dziennie. Obecnie zalecamy tylko jedną kopię zapasową dziennie, ponieważ natychmiastowe punkty przywracania są zachowywane przez 1-5 dni na skonfigurowane przechowywanie migawek i tylko 18 RPS pliku można skojarzyć z maszyną wirtualną w danym momencie. <br>
- Liczba punktów przywracania między kolekcjami punktów przywracania i grupami zasobów dla maszyny wirtualnej nie może przekroczyć 18. Aby utworzyć nowy punkt przywracania, Usuń istniejące punkty przywracania.

Zalecana akcja:<br>
Aby rozwiązać ten problem, Usuń blokadę z grupy zasobów maszyny wirtualnej, a następnie ponów operację, aby wyzwolić czyszczenie.
> [!NOTE]
> Usługa Backup tworzy oddzielną grupę zasobów niż grupa zasobów maszyny wirtualnej do przechowywania kolekcji punktów przywracania. Zalecane jest, aby nie blokować grupy zasobów utworzonej do użycia przez usługę kopii zapasowej. Format nazewnictwa grupy zasobów utworzonej przez usługę kopii zapasowej to: AzureBackupRG_ `<Geo>` _ `<number>` . Na przykład: *AzureBackupRG_northeurope_1*

**Krok 1. [Usuwanie blokady z grupy zasobów punktu przywracania](#remove_lock_from_the_recovery_point_resource_group)** <br>
**Krok 2. [czyszczenie kolekcji punktów przywracania](#clean_up_restore_point_collection)**<br>

## <a name="usererrorkeyvaultpermissionsnotconfigured---backup-doesnt-have-sufficient-permissions-to-the-key-vault-for-backup-of-encrypted-vms"></a>UserErrorKeyvaultPermissionsNotConfigured — kopia zapasowa nie ma wystarczających uprawnień do magazynu kluczy na potrzeby tworzenia kopii zapasowych zaszyfrowanych maszyn wirtualnych

**Kod błędu**: UserErrorKeyvaultPermissionsNotConfigured <br>
**Komunikat o błędzie**: kopia zapasowa nie ma wystarczających uprawnień do magazynu kluczy na potrzeby tworzenia kopii zapasowych zaszyfrowanych maszyn wirtualnych. <br>

Aby operacja tworzenia kopii zapasowej zakończyła się pomyślnie na zaszyfrowanych maszynach wirtualnych, musi mieć uprawnienia dostępu do magazynu kluczy. Uprawnienia można ustawić za pomocą [Azure Portal](./backup-azure-vms-encryption.md) lub za pomocą [programu PowerShell](./backup-azure-vms-automation.md#enable-protection).

## <a name="extensionsnapshotfailednonetwork---snapshot-operation-failed-due-to-no-network-connectivity-on-the-virtual-machine"></a><a name="ExtensionSnapshotFailedNoNetwork-snapshot-operation-failed-due-to-no-network-connectivity-on-the-virtual-machine"></a>ExtensionSnapshotFailedNoNetwork — nie można wykonać operacji migawki z powodu braku łączności z siecią na maszynie wirtualnej

**Kod błędu**: ExtensionSnapshotFailedNoNetwork<br>
**Komunikat o błędzie**: operacja migawki nie powiodła się z powodu braku łączności sieciowej na maszynie wirtualnej<br>

Po zarejestrowaniu i zaplanowaniu maszyny wirtualnej dla usługi Azure Backup Backup rozpocznie zadanie, komunikując się z rozszerzeniem kopii zapasowej maszyny wirtualnej w celu przeprowadzenia migawki określonego momentu. Jeden z następujących warunków może uniemożliwić wyzwolenie migawki. Jeśli migawka nie zostanie wyzwolona, może wystąpić błąd kopii zapasowej. Wykonaj następujący krok rozwiązywania problemów, a następnie spróbuj ponownie wykonać operację:

**[Nie można pobrać stanu migawki lub nie można wykonać migawki](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  

## <a name="extensionoperationfailedformanageddisks---vmsnapshot-extension-operation-failed"></a><a name="ExtensionOperationFailed-vmsnapshot-extension-operation-failed"></a>ExtensionOperationFailedForManagedDisks — nie można wykonać operacji rozszerzenia VMSnapshot

**Kod błędu**: ExtensionOperationFailedForManagedDisks <br>
**Komunikat o błędzie**: nie można wykonać operacji rozszerzenia VMSnapshot<br>

Po zarejestrowaniu i zaplanowaniu maszyny wirtualnej dla usługi Azure Backup Backup rozpocznie zadanie, komunikując się z rozszerzeniem kopii zapasowej maszyny wirtualnej w celu przeprowadzenia migawki określonego momentu. Jeden z następujących warunków może uniemożliwić wyzwolenie migawki. Jeśli migawka nie zostanie wyzwolona, może wystąpić błąd kopii zapasowej. Wykonaj następujące kroki rozwiązywania problemów w podanej kolejności, a następnie ponów próbę wykonania operacji:  
**Przyczyna 1: [nie można pobrać stanu migawki lub nie można wykonać migawki](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  
**Przyczyna 2: [Agent jest zainstalowany na maszynie wirtualnej, ale nie odpowiada (dla maszyn wirtualnych z systemem Windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  
**Przyczyna 3: [Agent zainstalowany na maszynie wirtualnej jest nieaktualny (dla maszyn wirtualnych z systemem Linux)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**

## <a name="backupoperationfailed--backupoperationfailedv2---backup-fails-with-an-internal-error"></a>BackUpOperationFailed/BackUpOperationFailedV2 — nie można utworzyć kopii zapasowej z powodu błędu wewnętrznego

**Kod błędu**: BackUpOperationFailed/BackUpOperationFailedV2 <br>
**Komunikat o błędzie**: wykonywanie kopii zapasowej nie powiodło się z powodu błędu wewnętrznego — ponów próbę wykonania operacji za kilka minut <br>

Po zarejestrowaniu i zaplanowaniu maszyny wirtualnej dla usługi Azure Backup backup inicjuje zadanie, komunikując się z rozszerzeniem kopii zapasowej maszyny wirtualnej w celu przeprowadzenia migawki w danym momencie. Jeden z następujących warunków może uniemożliwić wyzwolenie migawki. Jeśli migawka nie zostanie wyzwolona, może wystąpić błąd kopii zapasowej. Wykonaj następujące kroki rozwiązywania problemów w podanej kolejności, a następnie ponów próbę wykonania operacji:  
**Przyczyna 1: [Agent zainstalowany na maszynie wirtualnej, ale nie odpowiada (dla maszyn wirtualnych z systemem Windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  
**Przyczyna 2: [Agent zainstalowany na maszynie wirtualnej jest nieaktualny (dla maszyn wirtualnych z systemem Linux)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
**Przyczyna 3: [nie można pobrać stanu migawki lub nie można wykonać migawki](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  
**Przyczyna 4: [Usługa kopii zapasowej nie ma uprawnień do usuwania starych punktów przywracania z powodu blokady grupy zasobów](#remove_lock_from_the_recovery_point_resource_group)**<br>

## <a name="usererrorunsupporteddisksize---the-configured-disk-sizes-is-currently-not-supported-by-azure-backup"></a>UserErrorUnsupportedDiskSize — skonfigurowane rozmiary dysków nie są obecnie obsługiwane przez Azure Backup

**Kod błędu**: UserErrorUnsupportedDiskSize <br>
**Komunikat o błędzie**: skonfigurowane rozmiary dysków nie są obecnie obsługiwane przez Azure Backup. <br>

Operacja tworzenia kopii zapasowej może zakończyć się niepowodzeniem podczas tworzenia kopii zapasowej maszyny wirtualnej o rozmiarze dysku większym niż 32 TB. Ponadto kopia zapasowa szyfrowanych dysków o rozmiarze większym niż 4 TB nie jest obecnie obsługiwana. Upewnij się, że rozmiar dysku jest mniejszy lub równy obsługiwanemu limitowi, dzieląc dysk (y).

## <a name="usererrorbackupoperationinprogress---unable-to-initiate-backup-as-another-backup-operation-is-currently-in-progress"></a>UserErrorBackupOperationInProgress — nie można zainicjować kopii zapasowej, ponieważ trwa inna operacja tworzenia kopii zapasowej

**Kod błędu**: UserErrorBackupOperationInProgress <br>
**Komunikat o błędzie**: nie można zainicjować kopii zapasowej, ponieważ trwa inna operacja tworzenia kopii zapasowej<br>

Ostatnie zadanie tworzenia kopii zapasowej nie powiodło się, ponieważ jest w toku istniejące zadanie tworzenia kopii zapasowej. Nie można rozpocząć nowego zadania tworzenia kopii zapasowej, dopóki nie zakończy się bieżące zadanie. Upewnij się, że operacja tworzenia kopii zapasowej jest w toku, przed wyzwoleniem lub zaplanowaniem innej operacji tworzenia kopii zapasowej. Aby sprawdzić stan zadań tworzenia kopii zapasowej, wykonaj następujące czynności:

1. Zaloguj się do Azure Portal, wybierz pozycję **wszystkie usługi**. Wpisz Recovery Services i wybierz pozycję **magazyny Recovery Services**. Zostanie wyświetlona lista magazynów Usług odzyskiwania.
2. Z listy Recovery Services magazynów wybierz magazyn, w którym jest skonfigurowana kopia zapasowa.
3. W menu pulpitu nawigacyjnego magazynu wybierz kolejno pozycje **zadania tworzenia kopii zapasowej** , które wyświetla wszystkie zadania tworzenia kopii zapasowej.
   - Jeśli zadanie tworzenia kopii zapasowej jest w toku, poczekaj na jego zakończenie lub Anuluj zadanie tworzenia kopii zapasowej.
     - Aby anulować zadanie tworzenia kopii zapasowej, kliknij prawym przyciskiem myszy zadanie tworzenia kopii zapasowej i wybierz polecenie **Anuluj** lub Użyj [programu PowerShell](/powershell/module/az.recoveryservices/stop-azrecoveryservicesbackupjob).
   - Jeśli ponownie skonfigurowano kopię zapasową w innym magazynie, upewnij się, że w starym magazynie nie są uruchomione żadne zadania tworzenia kopii zapasowej. Jeśli istnieje, Anuluj zadanie tworzenia kopii zapasowej.
     - Aby anulować zadanie tworzenia kopii zapasowej, kliknij prawym przyciskiem myszy zadanie tworzenia kopii zapasowej i wybierz polecenie **Anuluj** lub Użyj [programu PowerShell](/powershell/module/az.recoveryservices/stop-azrecoveryservicesbackupjob) .
4. Spróbuj ponownie wykonać operację tworzenia kopii zapasowej.

Jeśli operacja zaplanowanej kopii zapasowej trwa dłużej, konflikt z kolejną konfiguracją kopii zapasowej, a następnie zapoznaj się z [najlepszymi rozwiązaniami](backup-azure-vms-introduction.md#best-practices), [wydajnością kopii zapasowej](backup-azure-vms-introduction.md#backup-performance)i [zagadnieniami](backup-azure-vms-introduction.md#backup-and-restore-considerations)dotyczącymi przywracania.

## <a name="usererrorcrpreportedusererror---backup-failed-due-to-an-error-for-details-see-job-error-message-details"></a>UserErrorCrpReportedUserError — tworzenie kopii zapasowej nie powiodło się z powodu błędu. Więcej informacji można znaleźć w szczegółach komunikatu o błędzie zadania

**Kod błędu**: UserErrorCrpReportedUserError <br>
**Komunikat o błędzie**: wykonywanie kopii zapasowej nie powiodło się z powodu błędu. Aby uzyskać szczegółowe informacje, zobacz szczegóły komunikatu o błędzie zadania.

Ten błąd jest zgłaszany z maszyny wirtualnej IaaS. Aby zidentyfikować główną przyczynę problemu, przejdź do ustawień magazynu Recovery Services. W sekcji **monitorowanie** wybierz pozycję **zadania tworzenia kopii zapasowej** , aby przefiltrować i wyświetlić stan. Wybierz pozycję **niepowodzenia** , aby przejrzeć szczegóły komunikatu o błędzie. Wykonaj dalsze działania zgodnie z zaleceniami na stronie Szczegóły błędu.

## <a name="usererrorbcmdatasourcenotpresent---backup-failed-this-virtual-machine-is-not-actively-protected-by-azure-backup"></a>UserErrorBcmDatasourceNotPresent — tworzenie kopii zapasowej nie powiodło się: Ta maszyna wirtualna nie jest chroniona przez Azure Backup

**Kod błędu**: UserErrorBcmDatasourceNotPresent <br>
**Komunikat o błędzie**: wykonywanie kopii zapasowej nie powiodło się: Ta maszyna wirtualna nie jest chroniona przez Azure Backup.

Sprawdź, czy dana maszyna wirtualna jest aktywna (nie w stanie wstrzymania) chronioną przez Azure Backup. Aby rozwiązać ten problem, upewnij się, że maszyna wirtualna jest aktywna, a następnie spróbuj ponownie wykonać operację.

## <a name="causes-and-solutions"></a>Przyczyny i rozwiązania

### <a name="the-agent-is-installed-in-the-vm-but-its-unresponsive-for-windows-vms"></a><a name="the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms"></a>Agent jest zainstalowany na maszynie wirtualnej, ale nie odpowiada (dla maszyn wirtualnych z systemem Windows)

#### <a name="solution-for-this-error"></a>Rozwiązanie dla tego błędu

Agent maszyny wirtualnej mógł zostać uszkodzony lub usługa mogła zostać zatrzymana. Ponowne zainstalowanie agenta maszyny wirtualnej pomoże uzyskać najnowszą wersję. Pomaga również w ponownym uruchomieniu komunikacji z usługą.

1. Ustal, czy usługa agenta gościa systemu Windows Azure jest uruchomiona w usługach VM Services (Services. msc). Spróbuj ponownie uruchomić usługę agenta gościa systemu Windows Azure i zainicjuj wykonywanie kopii zapasowej.
2. Jeśli usługa agenta gościa platformy Microsoft Azure nie jest widoczna w obszarze usługi, w panelu sterowania przejdź do pozycji **programy i funkcje** , aby określić, czy zainstalowano usługę agenta gościa systemu Windows Azure.
3. Jeśli w aplecie **programy i funkcje** zostanie wyświetlony Agent gościa platformy Microsoft Azure, Odinstaluj agenta gościa platformy Microsoft Azure.
4. Pobierz i zainstaluj [najnowszą wersję pliku msi agenta](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Aby ukończyć instalację, musisz mieć uprawnienia administratora.
5. Sprawdź, czy usługi agenta gościa systemu Windows Azure są widoczne w obszarze usługi.
6. Uruchom kopię zapasową na żądanie:
   - W portalu wybierz pozycję **Utwórz kopię zapasową teraz**.

Sprawdź również, czy na maszynie wirtualnej [zainstalowano Microsoft .NET 4,5](/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) . Do komunikacji agenta maszyny wirtualnej z usługą jest wymagany program .NET 4,5.

### <a name="the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms"></a>Agent zainstalowany na maszynie wirtualnej jest nieaktualny (dla maszyn wirtualnych z systemem Linux)

#### <a name="solution"></a>Rozwiązanie

Większość błędów związanych z agentami lub rozszerzeniami dla maszyn wirtualnych z systemem Linux jest spowodowana przez problemy, które mają wpływ na nieaktualny Agent maszyny wirtualnej. Aby rozwiązać ten problem, postępuj zgodnie z następującymi ogólnymi wskazówkami:

1. Postępuj zgodnie z instrukcjami dotyczącymi [aktualizowania agenta maszyny wirtualnej z systemem Linux](../virtual-machines/extensions/update-linux-agent.md).

   > [!NOTE]
   > *Zdecydowanie zalecamy* , aby zaktualizować agenta tylko za pomocą repozytorium dystrybucji. Nie zalecamy pobierania kodu agenta bezpośrednio z usługi GitHub i aktualizowania go. Jeśli najnowszy Agent dystrybucji nie jest dostępny, skontaktuj się z pomocą techniczną, aby uzyskać instrukcje dotyczące sposobu jej instalacji. Aby sprawdzić najnowszego agenta, przejdź do strony [agenta systemu Linux platformy Microsoft Azure](https://github.com/Azure/WALinuxAgent/releases) w repozytorium GitHub.

2. Upewnij się, że na maszynie wirtualnej jest uruchomiony agent platformy Azure, uruchamiając następujące polecenie: `ps -e`

   Jeśli proces nie jest uruchomiony, uruchom go ponownie przy użyciu następujących poleceń:

   - Dla Ubuntu: `service walinuxagent start`
   - W przypadku innych dystrybucji: `service waagent start`

3. [Skonfiguruj agenta autostartu](https://github.com/Azure/WALinuxAgent/wiki/Known-Issues#mitigate_agent_crash).
4. Uruchom nową testową kopię zapasową. Jeśli błąd będzie się utrzymywał, Zbierz następujące dzienniki z maszyny wirtualnej:

   - /var/lib/waagent/*. XML
   - /var/log/waagent.log
   - /var/log/azure/*

Jeśli potrzebujesz pełnego rejestrowania dla programu waagent, wykonaj następujące kroki:

1. W pliku/etc/waagent.conf znajdź następujący wiersz: **Włącz pełne rejestrowanie (t | n)**
2. Zmień wartość **dzienników. verbose** z *n* na *y*.
3. Zapisz zmiany, a następnie uruchom ponownie program waagent, wykonując kroki opisane wcześniej w tej sekcji.

### <a name="vm-agent-configuration-options-are-not-set-for-linux-vms"></a>Nie ustawiono opcji konfiguracji VM-Agent (dla maszyn wirtualnych z systemem Linux)

Plik konfiguracji (/etc/waagent.conf) kontroluje akcje waagent. Rozszerzenia opcji pliku konfiguracji **. wartość Enable** powinna być równa **y** i **aprowizacji.** aby tworzenie kopii zapasowej działało, Agent musi być **ustawiony na wartość** Auto.
Aby uzyskać pełną listę opcji pliku konfiguracji VM-Agent, zobacz <https://github.com/Azure/WALinuxAgent#configuration-file-options>

### <a name="application-control-solution-is-blocking-iaasbcdrextensionexe"></a>Rozwiązanie do sterowania aplikacjami blokuje IaaSBcdrExtension.exe

Jeśli korzystasz z [funkcji AppLocker](/windows/security/threat-protection/windows-defender-application-control/applocker/what-is-applocker) (lub innego rozwiązania do sterowania aplikacjami), a reguły są oparte na programie Publisher lub ścieżce, mogą blokować uruchamianie **IaaSBcdrExtension.exe** pliku wykonywalnego.

#### <a name="solution-to-this-issue"></a>Rozwiązanie tego problemu

Wyklucz `/var/lib` ścieżkę lub **IaaSBcdrExtension.exe** plik wykonywalny z funkcji AppLocker (lub innego oprogramowania do sterowania aplikacjami).

### <a name="the-snapshot-status-cant-be-retrieved-or-a-snapshot-cant-be-taken"></a><a name="the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken"></a>Nie można pobrać stanu migawki lub nie można wykonać migawki

Kopia zapasowa maszyny wirtualnej polega na wystawieniu polecenia migawki na bazowe konto magazynu. Tworzenie kopii zapasowej może zakończyć się niepowodzeniem, ponieważ nie ma dostępu do konta magazynu, lub ponieważ wykonywanie zadania migawki jest opóźnione.

#### <a name="solution-for-this-issue"></a>Rozwiązanie tego problemu

Następujące warunki mogą spowodować niepowodzenie zadania migawki:

| Przyczyna | Rozwiązanie |
| --- | --- |
| Stan maszyny wirtualnej jest raportowany niepoprawnie, ponieważ maszyna wirtualna jest zamknięta w Remote Desktop Protocol (RDP). | Jeśli wyłączysz maszynę wirtualną w protokole RDP, sprawdź Portal, aby określić, czy stan maszyny wirtualnej jest prawidłowy. Jeśli nie jest to poprawne, Zamknij maszynę wirtualną w portalu przy użyciu opcji **zamykania** na pulpicie NAWIGACYJNYM maszyny wirtualnej. |
| Maszyna wirtualna nie może pobrać adresu hosta ani sieci szkieletowej z serwera DHCP. | Aby tworzenie kopii zapasowej maszyny wirtualnej IaaS było możliwe, należy włączyć protokół DHCP wewnątrz gościa. Jeśli maszyna wirtualna nie może pobrać adresu hosta lub sieci szkieletowej z odpowiedzi DHCP 245, nie można pobrać ani uruchomić żadnych rozszerzeń. Jeśli potrzebujesz statycznego prywatnego adresu IP, należy go skonfigurować za pomocą **Azure Portal** lub **PowerShell** i upewnić się, że opcja DHCP wewnątrz maszyny wirtualnej jest włączona. [Dowiedz się więcej](../virtual-network/virtual-networks-static-private-ip-arm-ps.md#change-the-allocation-method-for-a-private-ip-address-assigned-to-a-network-interface) o konfigurowaniu statycznego adresu IP za pomocą programu PowerShell.

### <a name="remove-lock-from-the-recovery-point-resource-group"></a><a name="remove_lock_from_the_recovery_point_resource_group"></a>Usuń blokadę z grupy zasobów punktu odzyskiwania

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2. Przejdź do **opcji wszystkie zasoby**, wybierz grupę zasobów kolekcji punktów przywracania w następującym formacie AzureBackupRG_ `<Geo>` _ `<number>` .
3. W sekcji **Ustawienia** wybierz pozycję **blokady** , aby wyświetlić blokady.
4. Aby usunąć blokadę, wybierz wielokropek i wybierz pozycję **Usuń**.

    ![Usuń blokadę](./media/backup-azure-arm-vms-prepare/delete-lock.png)

### <a name="clean-up-restore-point-collection"></a><a name="clean_up_restore_point_collection"></a> Wyczyść kolekcję punktów przywracania

Po usunięciu blokady punkty przywracania muszą zostać oczyszczone.

Usunięcie grupy zasobów maszyny wirtualnej lub samej maszyny wirtualnej powoduje, że migawki usługi Managed disks pozostają aktywne i wygasną zgodnie z zestawem przechowywania. Aby usunąć migawki przywracania natychmiastowego (jeśli nie są już potrzebne), które są przechowywane w kolekcji punktów przywracania, wyczyść kolekcję punktów przywracania zgodnie z krokami podanymi poniżej.

Aby wyczyścić punkty przywracania, wykonaj dowolną z następujących metod:<br>

- [Czyszczenie kolekcji punktów przywracania przez uruchomienie kopii zapasowej na żądanie](#clean-up-restore-point-collection-by-running-on-demand-backup)<br>
- [Wyczyść kolekcję punktów przywracania z Azure Portal](#clean-up-restore-point-collection-from-azure-portal)<br>

#### <a name="clean-up-restore-point-collection-by-running-on-demand-backup"></a><a name="clean-up-restore-point-collection-by-running-on-demand-backup"></a>Czyszczenie kolekcji punktów przywracania przez uruchomienie kopii zapasowej na żądanie

Po usunięciu blokady wykonaj kopię zapasową na żądanie. Ta akcja zapewni automatyczne wyczyszczenie punktów przywracania. Należy oczekiwać, że ta operacja na żądanie kończy się niepowodzeniem po raz pierwszy. Jednak zapewni to automatyczne czyszczenie zamiast ręcznego usuwania punktów przywracania. Po oczyszczeniu Następna zaplanowana kopia zapasowa powinna zakończyć się powodzeniem.

> [!NOTE]
> Automatyczne czyszczenie zostanie wykonane po kilku godzinach wyzwalania kopii zapasowej na żądanie. Jeśli zaplanowana kopia zapasowa nadal kończy się niepowodzeniem, spróbuj ręcznie usunąć kolekcję punktów przywracania, wykonując kroki opisane [tutaj](#clean-up-restore-point-collection-from-azure-portal).

#### <a name="clean-up-restore-point-collection-from-azure-portal-br"></a><a name="clean-up-restore-point-collection-from-azure-portal"></a>Wyczyść kolekcję punktów przywracania z Azure Portal <br>

Aby ręcznie wyczyścić kolekcję punktów przywracania, która nie została wyczyszczona z powodu blokady w grupie zasobów, spróbuj wykonać następujące czynności:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2. W menu **centrum** wybierz pozycję **wszystkie zasoby**, a następnie wybierz grupę zasobów o następującym formacie AzureBackupRG_ _, w `<Geo>` `<number>` której znajduje się maszyna wirtualna.

    ![Wybierz grupę zasobów](./media/backup-azure-arm-vms-prepare/resource-group.png)

3. Wybierz grupę zasobów, zostanie wyświetlone okienko **Przegląd** .
4. Wybierz opcję **Pokaż ukryte typy** , aby wyświetlić wszystkie ukryte zasoby. Wybierz kolekcje punktów przywracania o następującym formacie AzureBackupRG_ `<VMName>` _ `<number>` .

    ![Wybierz kolekcję punktów przywracania](./media/backup-azure-arm-vms-prepare/restore-point-collection.png)

5. Wybierz pozycję **Usuń** , aby wyczyścić kolekcję punktów przywracania.
6. Spróbuj ponownie wykonać operację tworzenia kopii zapasowej.

> [!NOTE]
 >Jeśli zasób (kolekcja RP) ma dużą liczbę punktów przywracania, usunięcie ich z portalu może przekroczyć limit czasu i zakończyć się niepowodzeniem. Jest to znany problem CRP, w którym wszystkie punkty przywracania nie są usuwane w określonym czasie, a operacja przekracza limit czasu. Jednak operacja usuwania zazwyczaj powiedzie się po dwóch lub trzech ponownych próbach.
