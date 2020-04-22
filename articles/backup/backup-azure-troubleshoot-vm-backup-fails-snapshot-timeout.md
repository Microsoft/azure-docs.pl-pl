---
title: Rozwiązywanie problemów z agentem i rozszerzeniami
description: Symptomy, przyczyny i rozwiązania błędów usługi Azure Backup związanych z agentem, rozszerzeniem i dyskami.
ms.reviewer: saurse
ms.topic: troubleshooting
ms.date: 07/05/2019
ms.service: backup
ms.openlocfilehash: a3eedb5440711c7a45a13dcd53dd489c490588fc
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81677405"
---
# <a name="troubleshoot-azure-backup-failure-issues-with-the-agent-or-extension"></a>Rozwiązywanie problemów z błędem usługi Azure Backup: problemy z agentem lub rozszerzeniem

Ten artykuł zawiera kroki rozwiązywania problemów, które mogą pomóc w rozwiązaniu błędów usługi Azure Backup związanych z komunikacją z agentem i rozszerzeniem maszyny Wirtualnej.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="usererrorguestagentstatusunavailable---vm-agent-unable-to-communicate-with-azure-backup"></a><a name="UserErrorGuestAgentStatusUnavailable-vm-agent-unable-to-communicate-with-azure-backup"></a>UserErrorGuestAgentStatusUnavailable — agent maszyny wirtualnej nie może komunikować się z usługą Azure Backup

**Kod błędu**: UserErrorGuestAgentStatusNiejdostępny <br>
**Komunikat o błędzie:** Agent maszyny Wirtualnej nie może komunikować się z usługą Azure Backup<br>

Agent maszyny wirtualnej platformy Azure może zostać zatrzymany, przestarzały, w niespójnym stanie lub nie zainstalowany. Te stany uniemożliwiają usługi Azure Backup wyzwalanie migawek.

- **Otwórz witrynę Azure portal > okienka Ustawienia > > > właściwości,** > upewnić się, że stan maszyny **Wirtualnej** jest **uruchomiony,** a **stan agenta** jest **gotowy.** Jeśli agent maszyny Wirtualnej jest zatrzymany lub jest w niespójnym stanie, uruchom ponownie agenta<br>
  - W przypadku maszyn wirtualnych z systemem Windows wykonaj następujące [kroki,](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms) aby ponownie uruchomić agenta gościa.<br>
  - W przypadku maszyn wirtualnych z systemem Linux wykonaj następujące [kroki,](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms) aby ponownie uruchomić agenta gościa.
- **Otwórz witrynę Azure portal > ustawienia > maszyn wirtualnych > rozszerzenia** > Upewnij się, że wszystkie rozszerzenia są w **stanie inicjowania obsługi administracyjnej, który zakończył się pomyślnie.** Jeśli nie, wykonaj następujące [kroki,](https://docs.microsoft.com/azure/backup/backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout#usererrorvmprovisioningstatefailed---the-vm-is-in-failed-provisioning-state) aby rozwiązać problem.

## <a name="guestagentsnapshottaskstatuserror---could-not-communicate-with-the-vm-agent-for-snapshot-status"></a>GuestAgentSnapshotTaskStatusError — nie można nawiązać komunikacji z agentem maszyny wirtualnej w celu uzyskania stanu migawki

**Kod błędu**: GuestAgentSnapshotTaskStatusError<br>
**Komunikat o błędzie:** Nie można komunikować się z agentem maszyny Wirtualnej w celu uzyskania stanu migawki <br>

Po zarejestrowaniu i zaplanowaniu maszyny Wirtualnej dla usługi Azure Backup, kopia zapasowa uruchamia zadanie, komunikując się z rozszerzeniem kopii zapasowej maszyny Wirtualnej, aby wykonać migawkę punktu w czasie. Każdy z następujących warunków może uniemożliwić wyzwolenie migawki. Jeśli migawka nie jest wyzwalana, może wystąpić błąd kopii zapasowej. Wykonaj następujące kroki rozwiązywania problemów w podanej kolejności, a następnie ponów próbę wykonania operacji:  

**Przyczyna 1: [Agent jest zainstalowany na maszynie Wirtualnej, ale nie odpowiada (dla maszyn wirtualnych z systemem Windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  

**Przyczyna 2: [Agent zainstalowany na maszynie wirtualnej jest nieaktualny (dla maszyn wirtualnych z systemem Linux)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**

**Przyczyna 3: [Nie można pobrać stanu migawki lub nie można pobrać migawki](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**

**Przyczyna 4: [Opcje konfiguracji VM-Agent nie są ustawione (dla maszyn wirtualnych z systemem Linux)](#vm-agent-configuration-options-are-not-set-for-linux-vms)**

## <a name="usererrorvmprovisioningstatefailed---the-vm-is-in-failed-provisioning-state"></a>UserErrorVmProvisioningStateFailed — maszyna wirtualna jest w stanie nierozewisania obsługi administracyjnej

**Kod błędu**: UserErrorVmProvisioningStateFailed<br>
**Komunikat o błędzie:** maszyna wirtualna jest w stanie nieuwzwiązania obsługi administracyjnej<br>

Ten błąd występuje, gdy jeden z błędów rozszerzenia stawia maszynę wirtualną w stanie inicjowania obsługi administracyjnej nie powiodło się.<br>**Otwórz witrynę Azure portal > ustawienia > > rozszerzenia > rozszerzenia** i sprawdź, czy wszystkie rozszerzenia są w stanie **inicjowania obsługi administracyjnej powiodło się.** Aby dowiedzieć się więcej, zobacz [Stany inicjowania obsługi administracyjnej](https://docs.microsoft.com/azure/virtual-machines/windows/states-lifecycle#provisioning-states).

- Jeśli rozszerzenie VMSnapshot jest w stanie awarii, kliknij prawym przyciskiem myszy na rozszerzenie nie powiodło się i usuń go. Wyzwalanie kopii zapasowej na żądanie. Ta akcja spowoduje ponowne zainstalowanie rozszerzeń i uruchomienie zadania tworzenia kopii zapasowej.  <br>
- Jeśli inne rozszerzenie jest w stanie awarii, a następnie może zakłócać tworzenie kopii zapasowej. Upewnij się, że te problemy z rozszerzeniem zostały rozwiązane i ponów próbę wykonania kopii zapasowej.
- Jeśli stan inicjowania obsługi administracyjnej maszyny Wirtualnej jest w stanie aktualizacji, może zakłócać tworzenie kopii zapasowej. Upewnij się, że jest w dobrej kondycji i ponów próbę wykonania kopii zapasowej.

## <a name="usererrorrpcollectionlimitreached---the-restore-point-collection-max-limit-has-reached"></a>UserErrorRpCollectionLimitReached — osiągnięto limit maksymalny kolekcji punktów przywracania

**Kod błędu**: UserErrorRpCollectionLimitReached <br>
**Komunikat o błędzie:** Osiągnięto maksymalny limit kolekcji punktów przywracania. <br>

- Ten problem może się zdarzyć, jeśli istnieje blokada w grupie zasobów punktu odzyskiwania uniemożliwia automatyczne oczyszczanie punktów odzyskiwania.
- Ten problem może również się zdarzyć, jeśli wiele kopii zapasowych są wyzwalane dziennie. Obecnie zaleca się tylko jedną kopię zapasową dziennie, ponieważ punkty natychmiastowego przywracania są zachowywane przez 1-5 dni na skonfigurowaną retencję migawki i tylko 18 natychmiastowych punktów dostępowych może być skojarzonych z maszyną wirtualną w danym momencie. <br>
- Liczba punktów przywracania w kolekcjach punktów przywracania i grupach zasobów dla maszyny Wirtualnej nie może przekraczać 18. Aby utworzyć nowy punkt przywracania, usuń istniejące punkty przywracania.

Zalecane działanie:<br>
Aby rozwiązać ten problem, usuń blokadę grupy zasobów maszyny Wirtualnej i ponów próbę uruchomienia oczyszczania.
> [!NOTE]
> Usługa tworzenia kopii zapasowych tworzy oddzielną grupę zasobów niż grupa zasobów maszyny Wirtualnej do przechowywania kolekcji punktów przywracania. Klientom zaleca się, aby nie blokować grupy zasobów utworzonej do użytku przez usługę Kopia zapasowa. Format nazewnictwa grupy zasobów utworzonej przez usługę kopia`<Geo>``<number>` zapasowa to: AzureBackupRG_ _ Np. AzureBackupRG_northeurope_1

**Krok 1: [Usuwanie blokady z grupy zasobów punktu przywracania](#remove_lock_from_the_recovery_point_resource_group)** <br>
**Krok 2: [Oczyszczanie kolekcji punktów przywracania](#clean_up_restore_point_collection)**<br>

## <a name="usererrorkeyvaultpermissionsnotconfigured---backup-doesnt-have-sufficient-permissions-to-the-key-vault-for-backup-of-encrypted-vms"></a>UserErrorKeyvaultPermissionsNotConfigured — kopia zapasowa nie ma wystarczających uprawnień do magazynu kluczy do tworzenia kopii zapasowych zaszyfrowanych maszyn wirtualnych

**Kod błędu**: UserErrorKeyvaultPermissionsNotConfigured <br>
**Komunikat o błędzie:** Kopia zapasowa nie ma wystarczających uprawnień do magazynu kluczy do tworzenia kopii zapasowych zaszyfrowanych maszyn wirtualnych. <br>

Aby operacja tworzenia kopii zapasowej powiodła się na zaszyfrowanych maszynach wirtualnych, musi mieć uprawnienia dostępu do magazynu kluczy. Uprawnienia można ustawić za pośrednictwem [witryny Azure portal](https://docs.microsoft.com/azure/backup/backup-azure-vms-encryption) lub za pośrednictwem programu [PowerShell](https://docs.microsoft.com/azure/backup/backup-azure-vms-automation#enable-protection).

## <a name="extensionsnapshotfailednonetwork---snapshot-operation-failed-due-to-no-network-connectivity-on-the-virtual-machine"></a><a name="ExtensionSnapshotFailedNoNetwork-snapshot-operation-failed-due-to-no-network-connectivity-on-the-virtual-machine"></a>ExtensionSnapshotFailedNoNetwork — nie można wykonać operacji migawki z powodu braku łączności z siecią na maszynie wirtualnej

**Kod błędu**: ExtensionSnapshotFailedNoNetwork<br>
**Komunikat o błędzie:** Operacja migawki nie powiodła się z powodu braku łączności sieciowej na maszynie wirtualnej<br>

Po zarejestrowaniu i zaplanowaniu maszyny Wirtualnej dla usługi Azure Backup, kopia zapasowa uruchamia zadanie, komunikując się z rozszerzeniem kopii zapasowej maszyny Wirtualnej, aby wykonać migawkę punktu w czasie. Każdy z następujących warunków może uniemożliwić wyzwolenie migawki. Jeśli migawka nie jest wyzwalana, może wystąpić błąd kopii zapasowej. Wykonaj następujący krok rozwiązywania problemów, a następnie ponów próbę wykonania operacji:

**[Nie można pobrać stanu migawki lub nie można zrobić migawki](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  

## <a name="extensionoperationfailedformanageddisks---vmsnapshot-extension-operation-failed"></a><a name="ExtensionOperationFailed-vmsnapshot-extension-operation-failed"></a>ExtensionOperationFailedForManagedDisks — operacja rozszerzenia VMSnapshot nie powiodła się

**Kod błędu**: ExtensionOperationFailedForManagedDisks <br>
**Komunikat o błędzie**: Operacja rozszerzenia VMSnapshot nie powiodła się<br>

Po zarejestrowaniu i zaplanowaniu maszyny Wirtualnej dla usługi Azure Backup, kopia zapasowa uruchamia zadanie, komunikując się z rozszerzeniem kopii zapasowej maszyny Wirtualnej, aby wykonać migawkę punktu w czasie. Każdy z następujących warunków może uniemożliwić wyzwolenie migawki. Jeśli migawka nie jest wyzwalana, może wystąpić błąd kopii zapasowej. Wykonaj następujące kroki rozwiązywania problemów w podanej kolejności, a następnie ponów próbę wykonania operacji:  
**Przyczyna 1: [Nie można pobrać stanu migawki lub nie można pobrać migawki](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  
**Przyczyna 2: [Agent jest zainstalowany na maszynie Wirtualnej, ale nie odpowiada (dla maszyn wirtualnych z systemem Windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  
**Przyczyna 3: [Agent zainstalowany na maszynie wirtualnej jest nieaktualny (dla maszyn wirtualnych z systemem Linux)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**

## <a name="backupoperationfailed--backupoperationfailedv2---backup-fails-with-an-internal-error"></a>BackUpOperationFailed/BackUpOperationFailedV2 — nie można utworzyć kopii zapasowej z powodu błędu wewnętrznego

**Kod błędu**: BackUpOperationFailed / BackUpOperationFailedV2 <br>
**Komunikat o błędzie**: Kopia zapasowa nie powiodła się z błędem wewnętrznym - Ponów próbę wykonania operacji w ciągu kilku minut <br>

Po zarejestrowaniu i zaplanowaniu maszyny Wirtualnej dla usługi Azure Backup, kopia zapasowa inicjuje zadanie, komunikując się z rozszerzeniem kopii zapasowej maszyny Wirtualnej, aby wykonać migawkę punktu w czasie. Każdy z następujących warunków może uniemożliwić wyzwolenie migawki. Jeśli migawka nie jest wyzwalana, może wystąpić błąd kopii zapasowej. Wykonaj następujące kroki rozwiązywania problemów w podanej kolejności, a następnie ponów próbę wykonania operacji:  
**Przyczyna 1: [Agent zainstalowany na maszynie wirtualnej, ale nie odpowiada (dla maszyn wirtualnych z systemem Windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  
**Przyczyna 2: [Agent zainstalowany na maszynie wirtualnej jest nieaktualny (dla maszyn wirtualnych z systemem Linux)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
**Przyczyna 3: [Nie można pobrać stanu migawki lub nie można pobrać migawki](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  
**Przyczyna 4: [Usługa tworzenia kopii zapasowych nie ma uprawnień do usuwania starych punktów przywracania z powodu blokady grupy zasobów](#remove_lock_from_the_recovery_point_resource_group)**<br>

## <a name="usererrorunsupporteddisksize---the-configured-disk-sizes-is-currently-not-supported-by-azure-backup"></a>UserErrorUnsupportedDiskSize — skonfigurowany rozmiar dysku nie jest obecnie obsługiwany przez usługę Azure Backup

**Kod błędu**: UserErrorUnsupportedDiskSize <br>
**Komunikat o błędzie:** skonfigurowany rozmiar dysku nie jest obecnie obsługiwany przez usługę Azure Backup. <br>

Operacja tworzenia kopii zapasowej może zakończyć się niepowodzeniem podczas wykonywania kopii zapasowej maszyny Wirtualnej o rozmiarze dysku większym niż 32 TB. Ponadto kopia zapasowa zaszyfrowanych dysków o rozmiarze większym niż 4 TB nie jest obecnie obsługiwana. Upewnij się, że rozmiar(y) dysku jest mniejszy lub równy obsługiwanego limitu, dzieląc dysk(-y).

## <a name="usererrorbackupoperationinprogress---unable-to-initiate-backup-as-another-backup-operation-is-currently-in-progress"></a>UserErrorBackupOperationInProgress — nie można zainicjować kopii zapasowej, ponieważ obecnie trwa kolejna operacja tworzenia kopii zapasowej

**Kod błędu**: UserErrorBackupOperationInProgress <br>
**Komunikat o błędzie**: Nie można zainicjować kopii zapasowej, ponieważ obecnie trwa inna operacja tworzenia kopii zapasowej<br>

Ostatnie zadanie tworzenia kopii zapasowej nie powiodło się, ponieważ trwa istniejące zadanie tworzenia kopii zapasowej. Nie można rozpocząć nowego zadania tworzenia kopii zapasowej, dopóki bieżące zadanie nie zakończy się. Upewnij się, że operacja tworzenia kopii zapasowej jest obecnie w toku, przed wyzwoleniem lub zaplanowaniem innej operacji tworzenia kopii zapasowej. Aby sprawdzić stan zadań tworzenia kopii zapasowej, wykonaj następujące czynności:

1. Zaloguj się do witryny Azure portal, kliknij pozycję **Wszystkie usługi**. Wpisz ciąg Recovery Services i kliknij pozycję **Magazyny usługi Recovery Services**. Zostanie wyświetlona lista magazynów usługi Recovery Services.
2. Z listy magazynów usług odzyskiwania wybierz magazyn, w którym jest skonfigurowana kopia zapasowa.
3. W menu pulpitu nawigacyjnego przechowalni kliknij polecenie **Zadania kopii zapasowej,** w które są wyświetlane wszystkie zadania tworzenia kopii zapasowej.
   - Jeśli zadanie tworzenia kopii zapasowej jest w toku, poczekaj na jego zakończenie lub anuluj zadanie tworzenia kopii zapasowej.
     - Aby anulować zadanie tworzenia kopii zapasowej, kliknij prawym przyciskiem myszy zadanie tworzenia kopii zapasowej i kliknij polecenie **Anuluj** lub użyj programu [PowerShell](https://docs.microsoft.com/powershell/module/az.recoveryservices/stop-azrecoveryservicesbackupjob?view=azps-1.4.0).
   - Jeśli kopia zapasowa została ponownie skonfigurowana w innym magazynie, upewnij się, że w starym magazynie nie są uruchomione żadne zadania tworzenia kopii zapasowej. Jeśli istnieje, anuluj zadanie tworzenia kopii zapasowej.
     - Aby anulować zadanie tworzenia kopii zapasowej, kliknij prawym przyciskiem myszy na zadanie tworzenia kopii zapasowej, a następnie kliknij pozycję **Anuluj** lub użyj programu [PowerShell](https://docs.microsoft.com/powershell/module/az.recoveryservices/stop-azrecoveryservicesbackupjob?view=azps-1.4.0)
4. Ponów próbę wykonania kopii zapasowej.

Jeśli zaplanowana operacja tworzenia kopii zapasowej trwa dłużej, jest to sprzeczne z następną konfiguracją kopii zapasowej, a następnie zapoznaj się z [najlepszymi praktykami](backup-azure-vms-introduction.md#best-practices), [wydajnością kopii zapasowej](backup-azure-vms-introduction.md#backup-performance)i [przywróć uwagę](backup-azure-vms-introduction.md#backup-and-restore-considerations).

## <a name="usererrorcrpreportedusererror---backup-failed-due-to-an-error-for-details-see-job-error-message-details"></a>UserErrorCrpReportedUserError — kopia zapasowa nie powiodła się z powodu błędu. Aby uzyskać szczegółowe informacje, zobacz Szczegóły komunikatu o błędzie zadania

**Kod błędu**: UserErrorCrpReportedUserError <br>
**Komunikat o błędzie**: Kopia zapasowa nie powiodła się z powodu błędu. Aby uzyskać szczegółowe informacje, zobacz Szczegóły komunikatu o błędzie zadania.

Ten błąd jest zgłaszany z maszyny Wirtualnej IaaS. Aby zidentyfikować główną przyczynę problemu, przejdź do ustawień magazynu usług recovery Services. W sekcji **Monitorowanie** wybierz **pozycję Zadania kopii zapasowej,** aby filtrować i wyświetlać stan. Kliknij **błędy,** aby przejrzeć podstawowe szczegóły komunikatu o błędzie. Podejmij dalsze działania zgodnie z zaleceniami na stronie szczegółów błędu.

## <a name="usererrorbcmdatasourcenotpresent---backup-failed-this-virtual-machine-is-not-actively-protected-by-azure-backup"></a>UserErrorBcmDatasourceNotPresent — kopia zapasowa nie powiodła się: Ta maszyna wirtualna nie jest (aktywnie) chroniona przez usługę Azure Backup

**Kod błędu**: UserErrorBcmDatasourceNotPresent <br>
**Komunikat o błędzie:** Kopia zapasowa nie powiodła się: Ta maszyna wirtualna nie jest (aktywnie) chroniona przez usługę Azure Backup.

Sprawdź, czy dana maszyna wirtualna jest aktywnie (nie w stanie wstrzymania) chroniona przez usługę Azure Backup. Aby rozwiązać ten problem, upewnij się, że maszyna wirtualna jest aktywna, a następnie ponów próbę wykonania operacji.

## <a name="causes-and-solutions"></a>Przyczyny i rozwiązania

### <a name="the-agent-is-installed-in-the-vm-but-its-unresponsive-for-windows-vms"></a><a name="the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms"></a>Agent jest zainstalowany na maszynie wirtualnej, ale nie odpowiada (dla maszyn wirtualnych z systemem Windows)

#### <a name="solution"></a>Rozwiązanie

Agent maszyny Wirtualnej mógł zostać uszkodzony lub usługa mogła zostać zatrzymana. Ponowna instalacja agenta maszyny Wirtualnej pomaga uzyskać najnowszą wersję. Pomaga również ponownie uruchomić komunikację z usługą.

1. Określ, czy usługa Agenta gościa systemu Windows Azure jest uruchomiona w usługach maszyny Wirtualnej (services.msc). Spróbuj ponownie uruchomić usługę Agent gościa systemu Windows Azure i zainicjować kopię zapasową.
2. Jeśli usługa Agent gościa systemu Windows Azure nie jest widoczna w usługach, w Panelu sterowania przejdź do **programu i funkcji,** aby ustalić, czy jest zainstalowana usługa Agent gościa systemu Windows Azure.
3. Jeśli agent gościa systemu Windows Azure pojawi się w **programie Programy i funkcje,** odinstaluj agenta gościa systemu Windows Azure.
4. Pobierz i zainstaluj [najnowszą wersję agenta MSI](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Aby ukończyć instalację, musisz mieć uprawnienia administratora.
5. Sprawdź, czy usługi agenta gościa systemu Windows Azure są wyświetlane w usługach.
6. Uruchom kopię zapasową na żądanie:
   - W portalu wybierz pozycję **Kopiuj teraz**.

Ponadto sprawdź, czy [program Microsoft .NET 4.5 jest zainstalowany](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) na maszynie wirtualnej. .NET 4.5 jest wymagane dla agenta maszyny Wirtualnej do komunikowania się z usługą.

### <a name="the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms"></a>Agent zainstalowany na maszynie wirtualnej jest nieaktualny (dla maszyn wirtualnych z systemem Linux)

#### <a name="solution"></a>Rozwiązanie

Większość błędów związanych z agentem lub rozszerzeniem dla maszyn wirtualnych z systemem Linux są spowodowane przez problemy, które wpływają na przestarzałego agenta maszyny Wirtualnej. Aby rozwiązać ten problem, postępuj zgodnie z poniższymi ogólnymi wskazówkami:

1. Postępuj zgodnie z instrukcjami [aktualizacji agenta maszyny Wirtualnej systemu Linux](../virtual-machines/linux/update-agent.md).

   > [!NOTE]
   > *Zdecydowanie zaleca się* aktualizowanie agenta tylko za pośrednictwem repozytorium dystrybucji. Nie zaleca się pobierania kodu agenta bezpośrednio z usługi GitHub i aktualizowania go. Jeśli najnowszy agent dystrybucji nie jest dostępny, skontaktuj się z pomocą techniczną dystrybucji, aby uzyskać instrukcje dotyczące sposobu jej instalowania. Aby sprawdzić, czy najnowszy agent, przejdź do strony [agenta systemu Windows Azure Linux](https://github.com/Azure/WALinuxAgent/releases) w repozytorium GitHub.

2. Upewnij się, że agent platformy Azure jest uruchomiony na maszynie wirtualnej, uruchamiając następujące polecenie:`ps -e`

   Jeśli proces nie jest uruchomiony, uruchom go ponownie za pomocą następujących poleceń:

   - Dla Ubuntu:`service walinuxagent start`
   - W przypadku innych dystrybucji:`service waagent start`

3. [Skonfiguruj agenta automatycznego ponownego uruchamiania](https://github.com/Azure/WALinuxAgent/wiki/Known-Issues#mitigate_agent_crash).
4. Uruchom nową kopię zapasową testu. Jeśli błąd będzie się powtarzał, zbierz następujące dzienniki z maszyny Wirtualnej:

   - /var/lib/waagent/*.xml
   - /var/log/waagent.log
   - /var/log/azure/*

Jeśli wymagane jest pełne rejestrowanie waagent, wykonaj następujące kroki:

1. W pliku /etc/waagent.conf znajdź następujący wiersz: **Włącz pełne rejestrowanie (y|n)**
2. Zmień wartość **Logs.Verbose** z *n* na *y*.
3. Zapisz zmianę, a następnie uruchom ponownie program waagent, wykonując kroki opisane wcześniej w tej sekcji.

### <a name="vm-agent-configuration-options-are-not-set-for-linux-vms"></a>Opcje konfiguracji VM-Agent nie są ustawione (dla maszyn wirtualnych z systemem Linux)

Plik konfiguracyjny (/etc/waagent.conf) steruje działaniami waagenta. Rozszerzenia opcji plików **konfiguracji.Włącz** i **aprowizować.Agent** powinien być ustawiony na **y,** aby kopia zapasowa działała.
Aby uzyskać pełną listę opcji pliku konfiguracyjnego agenta maszyn wirtualnych, zobacz<https://github.com/Azure/WALinuxAgent#configuration-file-options>

### <a name="the-snapshot-status-cant-be-retrieved-or-a-snapshot-cant-be-taken"></a><a name="the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken"></a>Nie można pobrać stanu migawki lub nie można zrobić migawki

Kopia zapasowa maszyny Wirtualnej opiera się na wydawaniu polecenia migawki na podstawowym koncie magazynu. Kopia zapasowa może zakończyć się niepowodzeniem, ponieważ nie ma dostępu do konta magazynu lub ponieważ wykonanie zadania migawki jest opóźnione.

#### <a name="solution"></a>Rozwiązanie

Następujące warunki mogą spowodować niepowodzenie zadania migawki:

| Przyczyna | Rozwiązanie |
| --- | --- |
| Stan maszyny Wirtualnej jest zgłaszany niepoprawnie, ponieważ maszyna wirtualna jest zamykana w protokole RDP (Remote Desktop Protocol). | Jeśli zostanie zamknięta maszyna wirtualna w prow prow, sprawdź w portalu, aby ustalić, czy stan maszyny Wirtualnej jest poprawny. Jeśli nie jest poprawna, zamknij maszynę wirtualną w portalu, używając opcji **Zamykanie** na pulpicie nawigacyjnym maszyny Wirtualnej. |
| Maszyna wirtualna nie może uzyskać adresu hosta lub sieci szkieletowej z usługi DHCP. | Usługa DHCP musi być włączona wewnątrz gościa, aby kopia zapasowa maszyny Wirtualnej usługi IaaS działała. Jeśli maszyna wirtualna nie może uzyskać adresu hosta lub sieci szkieletowej z odpowiedzi DHCP 245, nie może pobrać ani uruchomić żadnych rozszerzeń. Jeśli potrzebujesz statycznego prywatnego adresu IP, należy skonfigurować go za pośrednictwem **portalu Azure** lub **programu PowerShell** i upewnij się, że opcja DHCP wewnątrz maszyny Wirtualnej jest włączona. [Dowiedz się więcej](../virtual-network/virtual-networks-static-private-ip-arm-ps.md#change-the-allocation-method-for-a-private-ip-address-assigned-to-a-network-interface) o konfigurowaniu statycznego adresu IP w programie PowerShell.

### <a name="remove-lock-from-the-recovery-point-resource-group"></a><a name="remove_lock_from_the_recovery_point_resource_group"></a>Usuwanie blokady z grupy zasobów punktu odzyskiwania

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2. Przejdź do **opcji Wszystkie zasoby**, wybierz grupę zasobów kolekcji`<Geo>``<number>`punktów przywracania w następującym formacie AzureBackupRG_ _ .
3. W sekcji **Ustawienia** wybierz pozycję **Blokady,** aby wyświetlić blokady.
4. Aby usunąć blokadę, zaznacz wielokropek i kliknij przycisk **Usuń**.

    ![Usuń blokadę](./media/backup-azure-arm-vms-prepare/delete-lock.png)

### <a name="clean-up-restore-point-collection"></a><a name="clean_up_restore_point_collection"></a>Oczyszczanie kolekcji punktów przywracania

Po usunięciu blokady należy oczyścić punkty przywracania.

Jeśli usuniesz grupę zasobów maszyny Wirtualnej lub samą maszynę wirtualną, migawki natychmiastowego przywracania dysków zarządzanych pozostaną aktywne i wygasną zgodnie z zestawem przechowywania. Aby usunąć migawki natychmiastowego przywracania (jeśli nie są już potrzebne), które są przechowywane w kolekcji punktu przywracania, wyczyść kolekcję punktów przywracania zgodnie z krokami podanymi poniżej.

Aby wyczyścić punkty przywracania, postępuj zgodnie z dowolną z metod:<br>

- [Oczyszczanie zbierania punktów przywracania przez uruchamianie kopii zapasowej na żądanie](#clean-up-restore-point-collection-by-running-on-demand-backup)<br>
- [Oczyszczanie kolekcji punktów przywracania z witryny Azure portal](#clean-up-restore-point-collection-from-azure-portal)<br>

#### <a name="clean-up-restore-point-collection-by-running-on-demand-backup"></a><a name="clean-up-restore-point-collection-by-running-on-demand-backup"></a>Oczyszczanie zbierania punktów przywracania przez uruchamianie kopii zapasowej na żądanie

Po usunięciu blokady wyzwolć kopię zapasową na żądanie. Ta akcja zapewni automatyczne czyszczenie punktów przywracania. Spodziewaj się, że ta operacja na żądanie zakończy się niepowodzeniem po raz pierwszy; jednak zapewni automatyczne oczyszczanie zamiast ręcznego usuwania punktów przywracania. Po oczyszczeniu następna zaplanowana kopia zapasowa powinna zakończyć się pomyślnie.

> [!NOTE]
> Automatyczne oczyszczanie nastąpi po kilku godzinach wyzwalania kopii zapasowej na żądanie. Jeśli zaplanowana kopia zapasowa nadal nie powiedzie się, spróbuj ręcznie usunąć kolekcję punktów przywracania, wykonując kroki wymienione [w tym miejscu](#clean-up-restore-point-collection-from-azure-portal).

#### <a name="clean-up-restore-point-collection-from-azure-portal-br"></a><a name="clean-up-restore-point-collection-from-azure-portal"></a>Oczyszczanie kolekcji punktów przywracania z witryny Azure portal <br>

Aby ręcznie wyczyścić kolekcję punktów przywracania, która nie jest wyczyszczona z powodu blokady w grupie zasobów, spróbuj wykonać następujące czynności:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2. W menu **Centrum** kliknij polecenie **Wszystkie zasoby**, wybierz grupę`<Geo>`Zasobów`<number>` w następującym formacie AzureBackupRG_ _ gdzie znajduje się maszyna wirtualna.

    ![Usuń blokadę](./media/backup-azure-arm-vms-prepare/resource-group.png)

3. Kliknij pozycję Grupa zasobów, zostanie wyświetlone okienko **Przegląd.**
4. Wybierz opcję **Pokaż ukryte typy,** aby wyświetlić wszystkie ukryte zasoby. Wybierz kolekcje punktów przywracania w następującym formacie AzureBackupRG_`<VMName>`_`<number>`.

    ![Usuń blokadę](./media/backup-azure-arm-vms-prepare/restore-point-collection.png)

5. Kliknij **przycisk Usuń,** aby wyczyścić kolekcję punktów przywracania.
6. Ponów próbę wykonania kopii zapasowej ponownie.

> [!NOTE]
 >Jeśli zasób (KOLEKCJA RP) ma dużą liczbę punktów przywracania, a następnie usunięcie ich z portalu może limit czasu i zakończyć się niepowodzeniem. Jest to znany problem CRP, gdzie wszystkie punkty przywracania nie są usuwane w określonym czasie i limit czasu operacji; jednak operacja usuwania zwykle powiedzie się po 2 lub 3 ponownych prób.
