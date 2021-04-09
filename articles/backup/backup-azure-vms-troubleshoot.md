---
title: Rozwiązywanie problemów z błędami kopii zapasowych przy użyciu maszyn wirtualnych
description: W tym artykule dowiesz się, jak rozwiązywać problemy z tworzeniem kopii zapasowych i przywracaniem maszyn wirtualnych platformy Azure.
ms.reviewer: srinathv
ms.topic: troubleshooting
ms.date: 08/30/2019
ms.openlocfilehash: 2d09081533cdb2de5ee97cb000e9844b41a85ac3
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105559370"
---
# <a name="troubleshooting-backup-failures-on-azure-virtual-machines"></a>Rozwiązywanie problemów dotyczących błędów kopii zapasowych w usłudze Azure Virtual Machines

Możesz rozwiązywać problemy występujące podczas korzystania z Azure Backup z informacjami wymienionymi poniżej:

## <a name="backup"></a>Backup

W tej sekcji omówiono niepowodzenie operacji tworzenia kopii zapasowej maszyny wirtualnej platformy Azure.

### <a name="basic-troubleshooting"></a>Podstawowe rozwiązywanie problemów

* Upewnij się, że Agent maszyny wirtualnej (Agent) jest [najnowszą wersją](./backup-azure-arm-vms-prepare.md#install-the-vm-agent).
* Upewnij się, że wersja systemu operacyjnego Windows lub Linux jest obsługiwana, zapoznaj się z [matrycą obsługi kopii zapasowych IaaS maszyny wirtualnej](./backup-support-matrix-iaas.md).
* Sprawdź, czy inna usługa kopii zapasowej nie jest uruchomiona.
  * Aby upewnić się, że nie występują żadne problemy z rozszerzeniami migawek, [Odinstaluj rozszerzenia w celu wymuszenia ponownego załadowania](./backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md)
* Sprawdź, czy maszyna wirtualna ma łączność z Internetem.
  * Upewnij się, że inna usługa kopii zapasowej nie jest uruchomiona.
* `Services.msc`Upewnij się, że usługa **agenta gościa systemu Windows Azure** jest **uruchomiona**. Jeśli brakuje usługi **agenta gościa platformy Microsoft Azure** , zainstaluj ją z [kopii zapasowych maszyn wirtualnych platformy Azure w magazynie Recovery Services](./backup-azure-arm-vms-prepare.md#install-the-vm-agent).
* **Dziennik zdarzeń** może zawierać błędy kopii zapasowych, które pochodzą z innych produktów kopii zapasowej, na przykład kopia zapasowa systemu Windows Server, a nie z powodu Azure Backup. Wykonaj następujące kroki, aby określić, czy problem dotyczy Azure Backup:
  * Jeśli wystąpił błąd podczas **tworzenia kopii zapasowej** wpisu w źródle lub komunikacie zdarzenia, sprawdź, czy kopie zapasowe usługi Azure IaaS VM zostały wykonane pomyślnie, a także czy punkt przywracania został utworzony z żądanym typem migawki.
  * Jeśli Azure Backup działa, problem jest prawdopodobnie z innym rozwiązaniem tworzenia kopii zapasowej.
  * Oto przykład Podgląd zdarzeń błędu 517, gdzie Azure Backup działał prawidłowo, ale "Kopia zapasowa systemu Windows Server" kończy się niepowodzeniem: ![ kopia zapasowa systemu Windows Server niepowodzenie](media/backup-azure-vms-troubleshoot/windows-server-backup-failing.png)
  * Jeśli Azure Backup zakończy się niepowodzeniem, wyszukaj odpowiedni kod błędu w sekcji typowe błędy kopii zapasowej maszyny wirtualnej w tym artykule.

## <a name="common-issues"></a>Typowe problemy

Poniżej przedstawiono typowe problemy związane z błędami tworzenia kopii zapasowych na maszynach wirtualnych platformy Azure.

### <a name="vmrestorepointinternalerror---antivirus-configured-in-the-vm-is-restricting-the-execution-of-backup-extension"></a>VMRestorePointInternalError — program antywirusowy skonfigurowany na maszynie wirtualnej ogranicza wykonywanie rozszerzenia kopii zapasowej

Kod błędu: VMRestorePointInternalError

Jeśli w chwili tworzenia kopii zapasowej, **Dzienniki aplikacji Podgląd zdarzeń** będą wyświetlały **nazwę aplikacji powodującej błąd: IaaSBcdrExtension.exe** następnie potwierdzenie, że program antywirusowy skonfigurowany na maszynie wirtualnej ogranicza wykonywanie rozszerzenia kopii zapasowej.
Aby rozwiązać ten problem, Wyklucz poniższe katalogi w konfiguracji programu antywirusowego i spróbuj ponownie wykonać operację tworzenia kopii zapasowej.

* `C:\Packages\Plugins\Microsoft.Azure.RecoveryServices.VMSnapshot`
* `C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.RecoveryServices.VMSnapshot`

### <a name="copyingvhdsfrombackupvaulttakinglongtime---copying-backed-up-data-from-vault-timed-out"></a>CopyingVHDsFromBackUpVaultTakingLongTime — Przekroczono limit czasu kopiowania danych kopii zapasowej z magazynu

Kod błędu: CopyingVHDsFromBackUpVaultTakingLongTime <br/>
Komunikat o błędzie: przekroczono limit czasu kopiowania danych kopii zapasowej z magazynu

Może się to zdarzyć z powodu przejściowych błędów magazynu lub niewystarczającej liczby operacji we/wy na koncie magazynu w celu przesłania danych do magazynu w ramach przedziału czasu. Skonfiguruj kopię zapasową maszyny wirtualnej przy użyciu tych [najlepszych](backup-azure-vms-introduction.md#best-practices) rozwiązań i spróbuj ponownie wykonać operację tworzenia kopii zapasowej.

### <a name="usererrorvmnotindesirablestate---vm-is-not-in-a-state-that-allows-backups"></a>UserErrorVmNotInDesirableState — maszyna wirtualna nie jest w stanie umożliwiającym tworzenie kopii zapasowych

Kod błędu: UserErrorVmNotInDesirableState <br/>
Komunikat o błędzie: maszyna wirtualna nie znajduje się w stanie umożliwiającym tworzenie kopii zapasowych.<br/>

Operacja tworzenia kopii zapasowej nie powiodła się, ponieważ maszyna wirtualna jest w stanie niepowodzenia. Aby można było pomyślnie utworzyć kopię zapasową, maszyna wirtualna powinna być w stanie Uruchomiona, Zatrzymana lub Zatrzymana (cofnięcie przydziału).

* Jeśli maszyna wirtualna jest w stanie przejściowym między **działaniem** i **wyłączaniem**, poczekaj na zmianę stanu. Następnie Wyzwól zadanie tworzenia kopii zapasowej.
* Jeśli maszyna wirtualna jest maszyną wirtualną z systemem Linux i używa modułu jądra Security-Enhanced Linux, Wyklucz ścieżkę agenta systemu Linux **/var/lib/waagent** z zasad zabezpieczeń i upewnij się, że rozszerzenie kopii zapasowej jest zainstalowane.

### <a name="usererrorfsfreezefailed---failed-to-freeze-one-or-more-mount-points-of-the-vm-to-take-a-file-system-consistent-snapshot"></a>UserErrorFsFreezeFailed — nie można zablokować co najmniej jednego punktu instalacji maszyny wirtualnej, aby utworzyć migawkę spójną na poziomie systemu plików

Kod błędu: UserErrorFsFreezeFailed <br/>
Komunikat o błędzie: nie można zablokować co najmniej jednego punktu instalacji maszyny wirtualnej, aby można było wykonać migawkę spójną na poziomie systemu plików.

* Odinstaluj urządzenia, dla których stan systemu plików nie został oczyszczony, przy użyciu polecenia **umount** .
* Uruchom sprawdzanie spójności systemu plików na tych urządzeniach przy użyciu polecenia **fsck** .
* Ponownie zainstaluj urządzenia i spróbuj ponownie wykonać operację tworzenia kopii zapasowej.</ol>

Jeśli nie można odinstalować urządzeń, można zaktualizować konfigurację kopii zapasowej maszyny wirtualnej w celu zignorowania niektórych punktów instalacji. Na przykład jeśli punkt instalacji "/mnt/Resource" nie może zostać odinstalowany i spowodowany awariami kopii zapasowych maszyny wirtualnej, można zaktualizować pliki konfiguracji kopii zapasowej maszyny wirtualnej za pomocą ```MountsToSkip``` właściwości w następujący sposób.

```bash
cat /var/lib/waagent/Microsoft.Azure.RecoveryServices.VMSnapshotLinux-1.0.9170.0/main/tempPlugin/vmbackup.conf[SnapshotThread]
fsfreeze: True
MountsToSkip = /mnt/resource
SafeFreezeWaitInSeconds=600
```


### <a name="extensionsnapshotfailedcom--extensioninstallationfailedcom--extensioninstallationfailedmdtc---extension-installationoperation-failed-due-to-a-com-error"></a>ExtensionSnapshotFailedCOM/ExtensionInstallationFailedCOM/ExtensionInstallationFailedMDTC-instalacja/operacja nie powiodła się z powodu błędu modelu COM+

Kod błędu: ExtensionSnapshotFailedCOM <br/>
Komunikat o błędzie: operacja migawki nie powiodła się z powodu błędu modelu COM+

Kod błędu: ExtensionInstallationFailedCOM  <br/>
Komunikat o błędzie: Instalacja/operacja rozszerzenia nie powiodła się z powodu błędu modelu COM+

Kod błędu: ExtensionInstallationFailedMDTC <br/>
Komunikat o błędzie: Instalacja rozszerzenia nie powiodła się z powodu błędu "model COM+ nie może skontaktować się z Distributed Transaction Coordinatorem firmy Microsoft <br/>

Operacja tworzenia kopii zapasowej nie powiodła się z powodu problemu z aplikacją **systemową com+** systemu Windows.  Aby rozwiązać ten problem, wykonaj poniższe czynności:

* Spróbuj uruchomić/ponownie uruchomić **aplikację systemową com+** systemu Windows (z wiersza polecenia z podwyższonym poziomem uprawnień **— net start COMSysApp**).
* Upewnij się, że usługa **Distributed Transaction Coordinator** jest uruchomiona jako konto **usługi sieciowej** . W przeciwnym razie zmień ją na Uruchom jako konto **usługi sieciowej** i uruchom ponownie **aplikację systemową com+**.
* Jeśli nie można uruchomić ponownie usługi, należy ponownie zainstalować usługę **Distributed Transaction Coordinator** , wykonując poniższe kroki:
  * Zatrzymaj usługę MSDTC
  * Otwórz wiersz polecenia (cmd)
  * Uruchom polecenie `msdtc -uninstall`
  * Uruchom polecenie `msdtc -install`
  * Uruchom usługę MSDTC
* Uruchom **aplikację systemową com+** usługi systemu Windows. Po uruchomieniu **aplikacji systemowej com+** Wyzwól zadanie tworzenia kopii zapasowej z Azure Portal.</ol>

### <a name="extensionfailedvsswriterinbadstate---snapshot-operation-failed-because-vss-writers-were-in-a-bad-state"></a>ExtensionFailedVssWriterInBadState — operacja migawki nie powiodła się z powodu nieprawidłowego stanu składników zapisywania usługi VSS

Kod błędu: ExtensionFailedVssWriterInBadState <br/>
Komunikat o błędzie: operacja migawki nie powiodła się z powodu nieprawidłowego stanu składników zapisywania usługi VSS.

Ten błąd występuje z powodu nieprawidłowego stanu składników zapisywania usługi VSS. Rozszerzenia Azure Backup współpracują z składnikami zapisywania usługi VSS w celu wykonania migawek dysków. Aby rozwiązać ten problem, wykonaj poniższe czynności:

Krok 1. Uruchom ponownie składniki zapisywania usługi VSS, które są w złej kondycji.

* W wierszu polecenia z podwyższonym poziomem uprawnień uruchom polecenie ```vssadmin list writers``` .
* Dane wyjściowe zawierają wszystkie składniki zapisywania usługi VSS i ich stan. Dla każdego składnika zapisywania usługi VSS, którego stan jest inny niż **[1] Stable** (Stabilny), uruchom ponownie usługę składnika zapisywania usługi VSS.
* Aby ponownie uruchomić usługę, uruchom następujące polecenia w wierszu polecenia z podwyższonym poziomem uprawnień: 

 ```net stop serviceName``` <br>
 ```net start serviceName```

> [!NOTE]
> Ponowne uruchomienie niektórych usług może mieć wpływ na środowisko produkcyjne. Upewnij się, że proces zatwierdzania jest przestrzegany, a usługa zostanie uruchomiona ponownie po zaplanowanym przestoju.

Krok 2. Jeśli ponowne uruchomienie składnika zapisywania usługi VSS nie rozwiązało problemu, uruchom następujące polecenie w wierszu polecenia z podwyższonym poziomem uprawnień (jako administrator), aby zapobiec tworzeniu wątków dla migawek obiektów BLOB.

```console
REG ADD "HKLM\SOFTWARE\Microsoft\BcdrAgentPersistentKeys" /v SnapshotWithoutThreads /t REG_SZ /d True /f
```

Krok 3. Jeśli kroki 1 i 2 nie rozwiążą problemu, przyczyną błędu może być niepowodzenie zapisywania usługi VSS z powodu ograniczonych operacji we/wy na sekundę.<br>

Aby sprawdzić, przejdź do opcji ***system i Podgląd zdarzeń Dzienniki aplikacji*** i Sprawdź następujący komunikat o błędzie:<br>
*Przekroczono limit czasu dostawcy kopiowania w tle podczas przechowywania zapisu do woluminu skopiowanego w tle. Jest to prawdopodobnie spowodowane nadmiernym działaniem woluminu przez aplikację lub usługę systemową. Spróbuj ponownie później, gdy aktywność woluminu zostanie zmniejszona.*<br>

Rozwiązanie:

* Sprawdź, czy są możliwe rozproszenie obciążenia między dyskami maszyn wirtualnych. Spowoduje to zmniejszenie obciążenia pojedynczych dysków. Ograniczenie liczby operacji we [/wy można sprawdzić przez włączenie metryk diagnostycznych na poziomie magazynu](/troubleshoot/azure/virtual-machines/performance-diagnostics#install-and-run-performance-diagnostics-on-your-vm).
* Zmień zasady tworzenia kopii zapasowych, aby wykonywać kopie zapasowe w godzinach szczytu, gdy obciążenie maszyny wirtualnej jest najniższe.
* Uaktualnij dyski platformy Azure, aby obsługiwać wyższe liczby operacji we/wy. [Dowiedz się więcej tutaj](../virtual-machines/disks-types.md)

### <a name="extensionfailedvssserviceinbadstate---snapshot-operation-failed-due-to-vss-volume-shadow-copy-service-in-bad-state"></a>ExtensionFailedVssServiceInBadState — operacja migawki nie powiodła się z powodu złego stanu usługi VSS (kopiowania woluminów w tle)

Kod błędu: ExtensionFailedVssServiceInBadState <br/>
Komunikat o błędzie: operacja migawki nie powiodła się z powodu nieprawidłowego stanu usługi VSS (kopiowania woluminów w tle).

Ten błąd występuje, ponieważ usługa VSS była w nieprawidłowym stanie. Rozszerzenia Azure Backup współpracują z usługą VSS w celu wykonania migawek dysków. Aby rozwiązać ten problem, wykonaj poniższe czynności:

Uruchom ponownie usługę kopiowania woluminów w tle (VSS).

* Przejdź do narzędzia Services.msc i ponownie uruchom usługę „Usługa kopiowania woluminów w tle”.<br>
(lub)<br>
* W wierszu polecenia z podwyższonym poziomem uprawnień uruchom następujące polecenia:

 ```net stop VSS``` <br>
 ```net start VSS```

Jeśli problem nadal występuje, uruchom ponownie maszynę wirtualną podczas zaplanowanego przestoju.

### <a name="usererrorskunotavailable---vm-creation-failed-as-vm-size-selected-is-not-available"></a>UserErrorSkuNotAvailable — Tworzenie maszyny wirtualnej nie powiodło się, ponieważ wybrany rozmiar maszyny wirtualnej jest niedostępny

Kod błędu: UserErrorSkuNotAvailable komunikat o błędzie: Tworzenie maszyny wirtualnej nie powiodło się, ponieważ wybrany rozmiar maszyny wirtualnej jest niedostępny.

Ten błąd występuje, ponieważ rozmiar maszyny wirtualnej wybrany podczas operacji przywracania jest nieobsługiwany. <br>

Aby rozwiązać ten problem, użyj opcji [Przywróć dyski](./backup-azure-arm-restore-vms.md#restore-disks) podczas operacji przywracania. Użyj tych dysków do utworzenia maszyny wirtualnej z listy [dostępnych rozmiarów maszyn wirtualnych](./backup-support-matrix-iaas.md#vm-compute-support) przy użyciu [poleceń cmdlet programu PowerShell](./backup-azure-vms-automation.md#create-a-vm-from-restored-disks).

### <a name="usererrormarketplacevmnotsupported---vm-creation-failed-due-to-market-place-purchase-request-being-not-present"></a>UserErrorMarketPlaceVMNotSupported — Tworzenie maszyny wirtualnej nie powiodło się z powodu nieobecności żądania zakupu miejsca na rynku

Kod błędu: UserErrorMarketPlaceVMNotSupported komunikat o błędzie: Tworzenie maszyny wirtualnej nie powiodło się z powodu nieobecności żądania zakupu miejsca na rynku.

Azure Backup obsługuje tworzenie kopii zapasowych i przywracanie maszyn wirtualnych, które są dostępne w witrynie Azure Marketplace. Ten błąd występuje, gdy próbujesz przywrócić maszynę wirtualną (z określonym ustawieniem planu/wydawcy), które nie jest już dostępne w witrynie Azure Marketplace, [Dowiedz się więcej tutaj](/legal/marketplace/participation-policy#offering-suspension-and-removal).

* Aby rozwiązać ten problem, należy użyć opcji [Przywróć dyski](./backup-azure-arm-restore-vms.md#restore-disks) podczas operacji przywracania, a następnie użyć poleceń cmdlet [programu PowerShell](./backup-azure-vms-automation.md#create-a-vm-from-restored-disks) lub [interfejsu wiersza polecenia platformy Azure](./tutorial-restore-disk.md) , aby utworzyć maszynę wirtualną przy użyciu najnowszych informacji z witryny Marketplace odpowiadających maszynie wirtualnej.
* Jeśli Wydawca nie zawiera żadnych informacji o witrynie Marketplace, możesz użyć dysków danych do pobrania danych i dołączyć je do istniejącej maszyny wirtualnej.

### <a name="extensionconfigparsingfailure--failure-in-parsing-the-config-for-the-backup-extension"></a>ExtensionConfigParsingFailure — błąd podczas analizowania konfiguracji dla rozszerzenia kopii zapasowej

Kod błędu: ExtensionConfigParsingFailure<br/>
Komunikat o błędzie: Wystąpił błąd podczas analizowania konfiguracji dla rozszerzenia kopii zapasowej.

Ten błąd występuje z powodu zmienionych uprawnień w katalogu **MachineKeys** : **%SYSTEMDRIVE%\programdata\microsoft\crypto\rsa\machinekeys**.
Uruchom następujące polecenie i sprawdź, czy uprawnienia w katalogu **MachineKeys** są domyślne: `icacls %systemdrive%\programdata\microsoft\crypto\rsa\machinekeys` .

Domyślne uprawnienia są następujące:

* Wszyscy: (R, W)
* Builtin\administratorzy: (F)

Jeśli w katalogu **MachineKeys** są wyświetlane uprawnienia inne niż ustawienia domyślne, wykonaj następujące kroki, aby rozwiązać odpowiednie uprawnienia, Usuń certyfikat i Wyzwól kopię zapasową:

1. Naprawianie uprawnień w katalogu **MachineKeys** . Korzystając z opcji właściwości zabezpieczeń Eksploratora i zaawansowane ustawienia zabezpieczeń w katalogu, zresetuj uprawnienia z powrotem do wartości domyślnych. Usuń wszystkie obiekty użytkownika z wyjątkiem ustawień domyślnych z katalogu i upewnij się, że uprawnienia **Wszyscy** mają specjalny dostęp w następujący sposób:

   * Wyświetlanie listy folderów/odczyt danych
   * Atrybuty odczytu
   * Odczyt atrybutów rozszerzonych
   * Tworzenie plików/zapis danych
   * Tworzenie folderów/Dołączanie danych
   * Zapisz atrybuty
   * Zapisz atrybuty rozszerzone
   * Uprawnienia do odczytu
2. Usuń wszystkie certyfikaty, **w których wystawiony** jest klasyczny model wdrażania lub **Generator certyfikatów CRP systemu Windows Azure**:

   * [Otwórz przystawkę Certyfikaty w konsoli komputera lokalnego](/dotnet/framework/wcf/feature-details/how-to-view-certificates-with-the-mmc-snap-in).
   * W obszarze  >  **Certyfikaty** osobiste Usuń wszystkie certyfikaty, **w których wystawiony** jest klasyczny model wdrażania lub **Generator certyfikatów usługi Windows Azure CRP**.
3. Wyzwalanie zadania tworzenia kopii zapasowej maszyny wirtualnej.

### <a name="extensionstuckindeletionstate---extension-state-is-not-supportive-to-backup-operation"></a>ExtensionStuckInDeletionState — stan rozszerzenia nie obsługuje operacji tworzenia kopii zapasowej

Kod błędu: ExtensionStuckInDeletionState <br/>
Komunikat o błędzie: stan rozszerzenia nie obsługuje operacji tworzenia kopii zapasowej

Operacja tworzenia kopii zapasowej nie powiodła się z powodu niespójnego stanu rozszerzenia kopii zapasowej. Aby rozwiązać ten problem, wykonaj poniższe czynności:

* Upewnij się, że agent gościa jest zainstalowany i odpowiada
* W Azure Portal przejdź do pozycji   >  **wszystkie**  >  **rozszerzenia** maszyny wirtualnej.
* Wybierz rozszerzenie kopii zapasowej VmSnapshot lub VmSnapshotLinux i wybierz pozycję **Odinstaluj**.
* Po usunięciu rozszerzenia kopii zapasowej spróbuj ponownie wykonać operację tworzenia kopii zapasowej
* Kolejna operacja tworzenia kopii zapasowej spowoduje zainstalowanie nowego rozszerzenia w odpowiednim stanie

### <a name="extensionfailedsnapshotlimitreachederror---snapshot-operation-failed-as-snapshot-limit-is-exceeded-for-some-of-the-disks-attached"></a>ExtensionFailedSnapshotLimitReachedError — operacja migawki nie powiodła się, ponieważ Przekroczono limit migawek dla niektórych dołączonych dysków

Kod błędu: ExtensionFailedSnapshotLimitReachedError  <br/>
Komunikat o błędzie: operacja migawki nie powiodła się, ponieważ Przekroczono limit migawek dla niektórych dołączonych dysków

Operacja migawki nie powiodła się, ponieważ Przekroczono limit migawek dla niektórych dołączonych dysków. Wykonaj następujące kroki rozwiązywania problemów, a następnie spróbuj ponownie wykonać operację.

* Usuń obiekt BLOB dysku — migawki, które nie są wymagane. Należy zachować ostrożność, aby nie usuwać obiektów BLOB dysków. Należy usunąć tylko obiekty blob migawek.
* Jeśli funkcja usuwania nietrwałego jest włączona na dysku maszyny wirtualnej, należy skonfigurować przechowywanie nietrwałe, aby istniejące migawki były mniejsze niż dopuszczalne maksimum w dowolnym momencie.
* Jeśli Azure Site Recovery jest włączona w kopii zapasowej maszyny wirtualnej, wykonaj następujące kroki:

  * Upewnij się, że wartość **isanysnapshotfailed** jest ustawiona na false w/etc/Azure/vmbackup.conf
  * Zaplanuj Azure Site Recovery w innym czasie, więc nie spowoduje to konfliktu operacji tworzenia kopii zapasowej.

### <a name="extensionfailedtimeoutvmnetworkunresponsive---snapshot-operation-failed-due-to-inadequate-vm-resources"></a>ExtensionFailedTimeoutVMNetworkUnresponsive — operacja migawki nie powiodła się z powodu niewystarczających zasobów maszyny wirtualnej

Kod błędu: ExtensionFailedTimeoutVMNetworkUnresponsive<br/>
Komunikat o błędzie: operacja migawki nie powiodła się z powodu niewystarczających zasobów maszyny wirtualnej.

Operacja tworzenia kopii zapasowej na maszynie wirtualnej nie powiodła się z powodu opóźnienia wywołań sieciowych podczas wykonywania operacji migawki. Aby rozwiązać ten problem, wykonaj krok 1. Jeśli problem będzie się powtarzał, spróbuj wykonać kroki 2 i 3.

**Krok 1**. Tworzenie migawki za poorednictwem hosta

W wierszu polecenia z podwyższonym poziomem uprawnień (Administrator) Uruchom następujące polecenie:

```console
REG ADD "HKLM\SOFTWARE\Microsoft\BcdrAgentPersistentKeys" /v SnapshotMethod /t REG_SZ /d firstHostThenGuest /f
REG ADD "HKLM\SOFTWARE\Microsoft\BcdrAgentPersistentKeys" /v CalculateSnapshotTimeFromHost /t REG_SZ /d True /f
```

Dzięki temu migawki będą wykonywane za pośrednictwem hosta, a nie konta gościa. Ponów próbę wykonania operacji tworzenia kopii zapasowej.

**Krok 2**. Spróbuj zmienić harmonogram tworzenia kopii zapasowych na czas, gdy maszyna wirtualna jest mniej załadowana (na przykład mniej procesorów lub IOPS)

**Krok 3**. próba [zwiększenia rozmiaru maszyny wirtualnej](../virtual-machines/windows/resize-vm.md) i ponowna próba wykonania operacji

### <a name="320001-resourcenotfound---could-not-perform-the-operation-as-vm-no-longer-exists--400094-bcmv2vmnotfound---the-virtual-machine-doesnt-exist--an-azure-virtual-machine-wasnt-found"></a>320001, ResourceNotFound — nie można wykonać operacji, ponieważ maszyna wirtualna już nie istnieje/400094, BCMV2VMNotFound — maszyna wirtualna nie istnieje/nie znaleziono maszyny wirtualnej platformy Azure

Kod błędu: 320001, ResourceNotFound <br/> Komunikat o błędzie: nie można wykonać operacji, ponieważ maszyna wirtualna już nie istnieje. <br/> <br/> Kod błędu: 400094, BCMV2VMNotFound <br/> Komunikat o błędzie: maszyna wirtualna nie istnieje <br/>
Nie znaleziono maszyny wirtualnej platformy Azure.

Ten błąd występuje, gdy podstawowa maszyna wirtualna jest usuwana, ale zasady tworzenia kopii zapasowych nadal wyszukują maszynę wirtualną w celu utworzenia kopii zapasowej. Aby naprawić ten błąd, wykonaj następujące czynności:

* Utwórz ponownie maszynę wirtualną o tej samej nazwie i nazwie grupy zasobów, **nazwie usługi w chmurze**,<br>lub
* Zatrzymaj ochronę maszyny wirtualnej z użyciem lub bez usuwania danych kopii zapasowej. Aby uzyskać więcej informacji, zobacz [Zatrzymywanie ochrony maszyn wirtualnych](backup-azure-manage-vms.md#stop-protecting-a-vm).</li></ol>

### <a name="usererrorbcmpremiumstoragequotaerror---could-not-copy-the-snapshot-of-the-virtual-machine-due-to-insufficient-free-space-in-the-storage-account"></a>UserErrorBCMPremiumStorageQuotaError — nie można skopiować migawki maszyny wirtualnej z powodu niewystarczającej ilości wolnego miejsca na koncie magazynu

Kod błędu: UserErrorBCMPremiumStorageQuotaError<br/> Komunikat o błędzie: nie można skopiować migawki maszyny wirtualnej z powodu niewystarczającej ilości wolnego miejsca na koncie magazynu

 W przypadku maszyn wirtualnych w warstwie Premium w stosie kopii zapasowych maszyny wirtualnej w wersji 1 migawka jest kopiowana do konta magazynu. Ten krok zapewnia, że ruch związany z zarządzaniem kopiami zapasowymi, który działa na migawce, nie ogranicza liczby operacji we/wy dostępnych dla aplikacji korzystających z dysków w warstwie Premium. <br><br>Zalecamy przydzielenie tylko 50%, 17,5 TB, całkowitej przestrzeni kont magazynu. Następnie usługa Azure Backup może skopiować migawkę do konta magazynu i przenieść dane z tej lokalizacji do magazynu w ramach konta magazynu.

### <a name="380008-azurevmoffline---failed-to-install-microsoft-recovery-services-extension-as-virtual-machine--is-not-running"></a>380008, AzureVmOffline — nie można zainstalować rozszerzenia Microsoft Recovery Services, ponieważ maszyna wirtualna nie jest uruchomiona

Kod błędu: 380008, AzureVmOffline <br/> Komunikat o błędzie: nie można zainstalować rozszerzenia Recovery Services Microsoft, ponieważ maszyna wirtualna nie jest uruchomiona

Agent maszyny wirtualnej jest wymaganiem wstępnym dla rozszerzenia Recovery Services platformy Azure. Zainstaluj agenta maszyny wirtualnej platformy Azure i ponownie uruchom operację rejestracji. <br> <ol> <li>Sprawdź, czy Agent maszyny wirtualnej został zainstalowany poprawnie. <li>Upewnij się, że flaga konfiguracji maszyny wirtualnej jest ustawiona poprawnie.</ol> Przeczytaj więcej na temat instalowania agenta maszyny wirtualnej i sprawdzanie poprawności instalacji agenta maszyny wirtualnej.

### <a name="extensionsnapshotbitlockererror---the-snapshot-operation-failed-with-the-volume-shadow-copy-service-vss-operation-error"></a>ExtensionSnapshotBitlockerError — operacja migawki nie powiodła się z powodu błędu operacji Usługa kopiowania woluminów w tle (VSS)

Kod błędu: ExtensionSnapshotBitlockerError <br/> Komunikat o błędzie: operacja migawki nie powiodła się z powodu błędu operacji Usługa kopiowania woluminów w tle (VSS) **ten dysk jest zablokowany przez szyfrowanie dysków funkcją BitLocker. Ten dysk należy odblokować w panelu sterowania.**

Wyłącz funkcję BitLocker dla wszystkich dysków na maszynie wirtualnej i sprawdź, czy problem z usługą VSS został rozwiązany.

### <a name="vmnotindesirablestate---the-vm-isnt-in-a-state-that-allows-backups"></a>VmNotInDesirableState — maszyna wirtualna nie jest w stanie umożliwiającym tworzenie kopii zapasowych

Kod błędu: VmNotInDesirableState <br/> Komunikat o błędzie: maszyna wirtualna nie jest w stanie umożliwiającym tworzenie kopii zapasowych.

* Jeśli maszyna wirtualna jest w stanie przejściowym między **działaniem** i **wyłączaniem**, poczekaj na zmianę stanu. Następnie Wyzwól zadanie tworzenia kopii zapasowej.
* Jeśli maszyna wirtualna jest maszyną wirtualną z systemem Linux i używa modułu jądra Security-Enhanced Linux, Wyklucz ścieżkę agenta systemu Linux **/var/lib/waagent** z zasad zabezpieczeń i upewnij się, że rozszerzenie kopii zapasowej jest zainstalowane.

* Agent maszyny wirtualnej nie znajduje się na komputerze wirtualnym: <br>Zainstaluj wszystkie wymagania wstępne i agenta maszyny wirtualnej. Następnie ponownie uruchom operację. | Przeczytaj więcej na temat [instalacji agenta maszyny wirtualnej i sprawdzanie poprawności instalacji agenta maszyny wirtualnej](#vm-agent).

### <a name="extensionsnapshotfailednosecurenetwork---the-snapshot-operation-failed-because-of-failure-to-create-a-secure-network-communication-channel"></a>ExtensionSnapshotFailedNoSecureNetwork — operacja migawki nie powiodła się z powodu błędu tworzenia bezpiecznego kanału komunikacji sieciowej

Kod błędu: ExtensionSnapshotFailedNoSecureNetwork <br/> Komunikat o błędzie: operacja migawki nie powiodła się z powodu błędu tworzenia bezpiecznego kanału komunikacji sieciowej.

* Otwórz Edytor rejestru, uruchamiając **regedit.exe** w trybie podniesionych uprawnień.
* Zidentyfikuj wszystkie wersje .NET Framework znajdujących się w systemie. Znajdują się one w hierarchii klucza rejestru **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft**.
* Dla każdej .NET Framework znajdującej się w kluczu rejestru Dodaj następujący klucz: <br> **Schusestrongcrypto we "= DWORD: 00000001**. </ol>

### <a name="extensionvcredistinstallationfailure---the-snapshot-operation-failed-because-of-failure-to-install-visual-c-redistributable-for-visual-studio-2012"></a>ExtensionVCRedistInstallationFailure — operacja migawki nie powiodła się z powodu niepowodzenia instalacji Pakiet redystrybucyjny Visual C++ dla Visual Studio 2012

Kod błędu: ExtensionVCRedistInstallationFailure <br/> Komunikat o błędzie: operacja migawki nie powiodła się z powodu niepowodzenia instalacji Pakiet redystrybucyjny Visual C++ dla Visual Studio 2012.

* Przejdź do `C:\Packages\Plugins\Microsoft.Azure.RecoveryServices.VMSnapshot\agentVersion` i zainstaluj vcredist2013_x64.<br/>Upewnij się, że wartość klucza rejestru, która umożliwia instalację usługi, jest ustawiona na poprawną wartość. Oznacza to, że należy ustawić wartość **początkową** w **HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Msiserver** na **3** , a nie **4**. <br><br>Jeśli nadal występują problemy z instalacją, uruchom ponownie usługę instalacji, uruchamiając polecenie **msiexec/unregister** , a następnie pozycję **msiexec/Register** z wiersza polecenia z podwyższonym poziomem uprawnień.
* Sprawdź dziennik zdarzeń, aby sprawdzić, czy są obserwowanie problemy związane z dostępem. Na przykład: *produkt: Microsoft Visual C++ 2013 x64 minimum Runtime-12.0.21005--Error 1401. nie można utworzyć klucza: Software\Classes.  Błąd systemu 5.  Sprawdź, czy masz wystarczające prawa dostępu do tego klucza lub skontaktuj się z pomocą techniczną.* <br><br> Upewnij się, że konto administratora lub użytkownika ma wystarczające uprawnienia, aby zaktualizować klucz rejestru **HKEY_LOCAL_MACHINE\SOFTWARE\Classes**. Podaj odpowiednie uprawnienia i ponownie uruchom agenta gościa platformy Microsoft Azure.<br><br> <li> Jeśli masz produkty antywirusowe, upewnij się, że mają odpowiednie reguły wykluczania, aby zezwolić na instalację.

### <a name="usererrorrequestdisallowedbypolicy---an-invalid-policy-is-configured-on-the-vm-which-is-preventing-snapshot-operation"></a>UserErrorRequestDisallowedByPolicy — na maszynie wirtualnej skonfigurowano nieprawidłowe zasady, które uniemożliwiają wykonanie operacji migawki

Kod błędu: UserErrorRequestDisallowedByPolicy <BR> Komunikat o błędzie: na maszynie wirtualnej skonfigurowano nieprawidłowe zasady, które uniemożliwiają wykonanie operacji migawki.

Jeśli masz Azure Policy, które [regulują Tagi w środowisku](../governance/policy/tutorials/govern-tags.md), Rozważ zmianę zasad z [skutku odmowy](../governance/policy/concepts/effects.md#deny) na [skutek modyfikacji](../governance/policy/concepts/effects.md#modify)lub Utwórz grupę zasobów ręcznie zgodnie ze [schematem nazewnictwa wymaganym przez Azure Backup](./backup-during-vm-creation.md#azure-backup-resource-group-for-virtual-machines).

## <a name="jobs"></a>Stanowiska

| Szczegóły błędu | Obejście |
| --- | --- |
| Anulowanie nie jest obsługiwane dla tego typu zadania: <br>Poczekaj na zakończenie zadania. |Brak |
| Zadanie nie jest w stanie do anulowania: <br>Poczekaj na zakończenie zadania. <br>**lub**<br> Wybrane zadanie nie jest w stanie do anulowania: <br>Poczekaj na zakończenie zadania. |Prawdopodobnie zadanie jest niemal ukończone. Poczekaj na zakończenie zadania.|
| Kopia zapasowa nie może anulować zadania, ponieważ nie jest w toku: <br>Anulowanie jest obsługiwane tylko dla zadań w toku. Spróbuj anulować zadanie w toku. |Ten błąd występuje ze względu na stan przejściowy. Poczekaj chwilę i spróbuj ponownie wykonać operację anulowania. |
| Wykonanie kopii zapasowej nie powiodło się: <br>Poczekaj na zakończenie zadania. |Brak |

## <a name="restore"></a>Przywracanie

### <a name="disks-appear-offline-after-file-restore"></a>Dyski są wyświetlane w trybie offline po przywróceniu plików

Jeśli po przywróceniu, Zauważ, że dyski są w trybie offline, a następnie:

* Sprawdź, czy komputer, na którym skrypt jest wykonywany, spełnia wymagania systemu operacyjnego. [Dowiedz się więcej](./backup-azure-restore-files-from-vm.md#step-3-os-requirements-to-successfully-run-the-script).  
* Upewnij się, że nie są przywracane do tego samego źródła, [Dowiedz się więcej](./backup-azure-restore-files-from-vm.md#step-2-ensure-the-machine-meets-the-requirements-before-executing-the-script).

### <a name="usererrorinstantrpnotfound---restore-failed-because-the-snapshot-of-the-vm-was-not-found"></a>UserErrorInstantRpNotFound — przywracanie nie powiodło się, ponieważ nie znaleziono migawki maszyny wirtualnej

Kod błędu: UserErrorInstantRpNotFound <br>
Komunikat o błędzie: Przywracanie nie powiodło się, ponieważ nie znaleziono migawki maszyny wirtualnej. Migawka mogła zostać usunięta, sprawdź.<br>

Ten błąd występuje, gdy próbujesz przywrócić z punktu odzyskiwania, który nie został przeniesiony do magazynu i został usunięty w fazie migawki. 
<br>
Aby rozwiązać ten problem, spróbuj przywrócić maszynę wirtualną z innego punktu przywracania.<br>

#### <a name="common-errors"></a>Typowe błędy 
| Szczegóły błędu | Obejście |
| --- | --- |
| Przywracanie nie powiodło się z powodu błędu wewnętrznego w chmurze. |<ol><li>Usługa w chmurze, do której próbujesz przywrócić, jest skonfigurowana przy użyciu ustawień DNS. Możesz sprawdzić: <br>**$Deployment = Get-AzureDeployment-ServiceName "ServiceName"-Slot "Product" Get-AzureDns-DnsSettings $Deployment. DnsSettings**.<br>Jeśli **adres** jest skonfigurowany, ustawienia DNS są skonfigurowane.<br> <li>Usługa w chmurze, do której próbujesz przywrócić, jest skonfigurowana za pomocą **zastrzeżonego adresu IP**, a istniejące maszyny wirtualne w usłudze w chmurze mają stan zatrzymany. Możesz sprawdzić, czy usługa w chmurze zarezerwował adres IP przy użyciu następujących poleceń cmdlet programu PowerShell: **$Deployment = Get-AzureDeployment-ServiceName "ServiceName"-Slot "Product" (produkcja) $DEP. ReservedIPName**. <br><li>Próbujesz przywrócić maszynę wirtualną z następującymi specjalnymi konfiguracjami sieci w tej samej usłudze w chmurze: <ul><li>Maszyny wirtualne w obszarze Konfiguracja usługi równoważenia obciążenia, wewnętrzne i zewnętrzne.<li>Maszyny wirtualne z wieloma zarezerwowanymi adresami IP. <li>Maszyny wirtualne z wieloma kartami sieciowymi. </ul><li>Wybierz nową usługę w chmurze w interfejsie użytkownika lub zapoznaj się z tematami dotyczącymi [przywracania](backup-azure-arm-restore-vms.md#restore-vms-with-special-configurations) maszyn wirtualnych mających specjalne konfiguracje sieci.</ol> |
| Wybrana nazwa DNS jest już zajęta: <br>Określ inną nazwę DNS i spróbuj ponownie. |Ta nazwa DNS odwołuje się do nazwy usługi w chmurze, zazwyczaj kończącej się rozszerzeniem **. cloudapp.NET**. Ta nazwa musi być unikatowa. Jeśli zostanie wyświetlony ten błąd, należy wybrać inną nazwę maszyny wirtualnej podczas przywracania. <br><br> Ten błąd jest wyświetlany tylko dla użytkowników Azure Portal. Operacja przywracania za pomocą programu PowerShell kończy się powodzeniem, ponieważ przywraca tylko dyski i nie tworzy maszyny wirtualnej. Ten błąd zostanie zwrócony, gdy maszyna wirtualna zostanie jawnie utworzona przez użytkownika po operacji przywracania dysku. |
| Określona konfiguracja sieci wirtualnej jest nieprawidłowa: <br>Określ inną konfigurację sieci wirtualnej i spróbuj ponownie. |Brak |
| Określona usługa w chmurze używa zastrzeżonego adresu IP, który nie jest zgodny z konfiguracją przywracanej maszyny wirtualnej: <br>Określ inną usługę w chmurze, która nie używa zastrzeżonego adresu IP. Lub wybierz inny punkt odzyskiwania do przywrócenia. |Brak |
| Osiągnięto limit liczby wejściowych punktów końcowych w usłudze w chmurze: <br>Spróbuj ponownie wykonać operację, określając inną usługę w chmurze lub używając istniejącego punktu końcowego. |Brak |
| Magazyn Recovery Services i docelowe konto magazynu znajdują się w dwóch różnych regionach: <br>Upewnij się, że konto magazynu określone w operacji przywracania znajduje się w tym samym regionie platformy Azure co magazyn Recovery Services. |Brak |
| Konto magazynu określone dla operacji przywracania nie jest obsługiwane: <br>Obsługiwane są tylko podstawowe lub standardowe konta magazynu z lokalnymi nadmiarowymi lub geograficznie nadmiarowymi ustawieniami replikacji. Wybierz obsługiwane konto magazynu. |Brak |
| Typ konta magazynu określony dla operacji przywracania nie jest w trybie online: <br>Upewnij się, że konto magazynu określone w operacji przywracania jest w trybie online. |Ten błąd może wystąpić z powodu przejściowego błędu w usłudze Azure Storage lub z powodu przestoju. Wybierz inne konto magazynu. |
| Osiągnięto limit przydziału grupy zasobów: <br>Usuń niektóre grupy zasobów z Azure Portal lub skontaktuj się z pomocą techniczną platformy Azure w celu zwiększenia limitów. |Brak |
| Wybrana podsieć nie istnieje: <br>Wybierz podsieć, która istnieje. |Brak |
| Usługa kopii zapasowej nie ma autoryzacji umożliwiającej dostęp do zasobów w Twojej subskrypcji. |Aby rozwiązać ten problem, należy najpierw przywrócić dyski, wykonując kroki opisane w sekcji [przywracanie kopii zapasowej dysków](backup-azure-arm-restore-vms.md#restore-disks). Następnie użyj kroków programu PowerShell z sekcji [Tworzenie maszyny wirtualnej z przywróconych dysków](backup-azure-vms-automation.md#restore-an-azure-vm). |

## <a name="backup-or-restore-takes-time"></a>Czas wykonywania kopii zapasowej lub przywracania

Jeśli kopia zapasowa trwa dłużej niż 12 godzin lub przywracanie trwa ponad 6 godzin, zapoznaj się z [najlepszymi rozwiązaniami](backup-azure-vms-introduction.md#best-practices)i [zagadnieniami](backup-azure-vms-introduction.md#backup-performance) dotyczącymi wydajności

## <a name="vm-agent"></a>Agent maszyny wirtualnej

### <a name="set-up-the-vm-agent"></a>Konfigurowanie agenta maszyny wirtualnej

Zazwyczaj Agent maszyny wirtualnej znajduje się już w maszynach wirtualnych utworzonych za pomocą galerii platformy Azure. Jednak maszyny wirtualne migrowane z lokalnych centrów danych nie będą miały zainstalowanego agenta maszyny wirtualnej. W przypadku tych maszyn wirtualnych agent maszyny wirtualnej musi zostać zainstalowany jawnie.

#### <a name="windows-vms---set-up-the-agent"></a>Maszyny wirtualne z systemem Windows — Konfigurowanie agenta

* Pobierz i zainstaluj [plik MSI agenta](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Aby zakończyć instalację, musisz mieć uprawnienia administratora.
* W przypadku maszyn wirtualnych utworzonych przy użyciu klasycznego modelu wdrażania należy [zaktualizować Właściwość maszyny wirtualnej](/troubleshoot/azure/virtual-machines/install-vm-agent-offline#use-the-provisionguestagent-property-for-classic-vms) , aby wskazać, że Agent jest zainstalowany. Ten krok nie jest wymagany w przypadku Azure Resource Manager maszyn wirtualnych.

#### <a name="linux-vms---set-up-the-agent"></a>Maszyny wirtualne z systemem Linux — Konfigurowanie agenta

* Zainstaluj najnowszą wersję agenta z repozytorium dystrybucji. Aby uzyskać szczegółowe informacje na temat nazwy pakietu, zobacz [repozytorium agentów systemu Linux](https://github.com/Azure/WALinuxAgent).
* W przypadku maszyn wirtualnych utworzonych przy użyciu klasycznego modelu wdrażania [zaktualizuj właściwość VM](/troubleshoot/azure/virtual-machines/install-vm-agent-offline#use-the-provisionguestagent-property-for-classic-vms) i sprawdź, czy Agent jest zainstalowany. Ten krok nie jest wymagany w przypadku Menedżer zasobów maszyn wirtualnych.

### <a name="update-the-vm-agent"></a>Aktualizowanie agenta maszyny wirtualnej

#### <a name="windows-vms---update-the-agent"></a>Maszyny wirtualne z systemem Windows — Aktualizowanie agenta

* Aby zaktualizować agenta maszyny wirtualnej, zainstaluj ponownie [pliki binarne agenta maszyny wirtualnej](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Przed aktualizacją agenta upewnij się, że w trakcie aktualizacji agenta maszyny wirtualnej nie wystąpią żadne operacje tworzenia kopii zapasowej.

#### <a name="linux-vms---update-the-agent"></a>Maszyny wirtualne z systemem Linux — aktualizowanie agenta

* Aby zaktualizować agenta maszyny wirtualnej z systemem Linux, postępuj zgodnie z instrukcjami zawartymi w artykule [Aktualizowanie agenta maszyny wirtualnej z systemem Linux](../virtual-machines/extensions/update-linux-agent.md?toc=/azure/virtual-machines/linux/toc.json).

    > [!NOTE]
    > Należy zawsze używać repozytorium dystrybucji do aktualizowania agenta.

    Nie pobieraj kodu agenta z usługi GitHub. Jeśli najnowszy Agent nie jest dostępny dla dystrybucji, skontaktuj się z pomocą techniczną, aby uzyskać instrukcje dotyczące uzyskiwania najnowszego agenta. Najnowsze informacje o [agencie systemu Windows Azure Linux](https://github.com/Azure/WALinuxAgent/releases) można także sprawdzić w repozytorium GitHub.

### <a name="validate-vm-agent-installation"></a>Weryfikowanie instalacji agenta maszyny wirtualnej

Sprawdź wersję agenta maszyny wirtualnej na maszynach wirtualnych z systemem Windows:

1. Zaloguj się do maszyny wirtualnej platformy Azure i przejdź do folderu **C:\WindowsAzure\Packages**. Plik **WaAppAgent.exe** powinien zostać znaleziony.
2. Kliknij plik prawym przyciskiem myszy i przejdź do pozycji **Właściwości**. Następnie wybierz kartę **szczegóły** . Pole **wersji produktu** powinno mieć wartość 2.6.1198.718 lub wyższą.

## <a name="troubleshoot-vm-snapshot-issues"></a>Rozwiązywanie problemów z migawką maszyny wirtualnej

Kopia zapasowa maszyny wirtualnej polega na wystawianiu poleceń migawek do magazynu bazowego. Brak dostępu do magazynu lub opóźnień w ramach zadania migawki uruchomienie może spowodować niepowodzenie zadania tworzenia kopii zapasowej. Następujące warunki mogą spowodować niepowodzenie zadania migawki:

* **Maszyny wirtualne z skonfigurowanym SQL Server kopii zapasowej mogą spowodować opóźnienie zadania migawki**. Domyślnie kopia zapasowa maszyny wirtualnej tworzy pełną kopię zapasową VSS na maszynach wirtualnych z systemem Windows. W przypadku maszyn wirtualnych z systemem SQL Server z konfiguracją SQL Server Backup mogą wystąpić opóźnienia migawek. Jeśli w przypadku migawek są opóźniane błędy kopii zapasowych, Ustaw następujący klucz rejestru:

   ```console
   [HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\BCDRAGENT]
   "USEVSSCOPYBACKUP"="TRUE"
   ```

* **Stan maszyny wirtualnej jest raportowany niepoprawnie, ponieważ maszyna wirtualna jest wyłączona w protokole RDP**. Jeśli używasz pulpitu zdalnego do zamykania maszyny wirtualnej, sprawdź, czy stan maszyny wirtualnej w portalu jest prawidłowy. Jeśli stan jest niepoprawny, użyj opcji **zamykania** na pulpicie NAWIGACYJNYM maszyny wirtualnej portalu, aby zamknąć maszynę wirtualną.
* **Jeśli więcej niż cztery maszyny wirtualne współużytkują tę samą usługę w chmurze, należy rozłożyć maszyny wirtualne na wiele zasad tworzenia kopii zapasowych**. Rozłożenie czasu wykonywania kopii zapasowych, więc nie można uruchomić więcej niż czterech kopii zapasowych maszyn wirtualnych. Spróbuj oddzielić godziny rozpoczęcia w zasadach o co najmniej godzinie.
* **Maszyna wirtualna jest uruchamiana z dużym procesorem CPU lub pamięcią**. Jeśli maszyna wirtualna działa z dużą ilością pamięci lub użyciem procesora CPU, więcej niż 90 procent, zadanie migawki jest umieszczane w kolejce i opóźnione. Ostatecznie przeprowadzi limit czasu. Jeśli ten problem wystąpi, wypróbuj kopię zapasową na żądanie.

## <a name="networking"></a>Sieć

Aby tworzenie kopii zapasowej maszyny wirtualnej IaaS było możliwe, należy włączyć protokół DHCP wewnątrz gościa. Jeśli potrzebujesz statycznego prywatnego adresu IP, skonfiguruj go za pomocą Azure Portal lub programu PowerShell. Upewnij się, że opcja DHCP wewnątrz maszyny wirtualnej jest włączona.
Uzyskaj więcej informacji na temat konfigurowania statycznego adresu IP za pomocą programu PowerShell:

* [Jak dodać statyczny wewnętrzny adres IP do istniejącej maszyny wirtualnej](/powershell/module/az.network/set-aznetworkinterfaceipconfig#description)
* [Zmień metodę alokacji dla prywatnego adresu IP przypisanego do interfejsu sieciowego](../virtual-network/virtual-networks-static-private-ip-arm-ps.md#change-the-allocation-method-for-a-private-ip-address-assigned-to-a-network-interface)