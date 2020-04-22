---
title: Rozwiązywanie problemów z błędami kopii zapasowej za pomocą maszyn wirtualnych platformy Azure
description: W tym artykule dowiesz się, jak rozwiązywać problemy z błędami napotkanymi podczas tworzenia kopii zapasowych i przywracania maszyn wirtualnych platformy Azure.
ms.reviewer: srinathv
ms.topic: troubleshooting
ms.date: 08/30/2019
ms.openlocfilehash: 019c27b1f7e8560c86252aaf2ed1fb79df2439fa
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81677338"
---
# <a name="troubleshooting-backup-failures-on-azure-virtual-machines"></a>Rozwiązywanie problemów z błędami tworzenia kopii zapasowych na maszynach wirtualnych platformy Azure

Możesz rozwiązywać problemy z błędami napotkanymi podczas korzystania z usługi Azure Backup, korzystając z informacji wymienionych poniżej:

## <a name="backup"></a>Backup

W tej sekcji opisano niepowodzenie operacji tworzenia kopii zapasowej maszyny wirtualnej platformy Azure.

### <a name="basic-troubleshooting"></a>Podstawowe rozwiązywanie problemów

* Upewnij się, że agent maszyny Wirtualnej (Agent WA) jest [najnowszą wersją](https://docs.microsoft.com/azure/backup/backup-azure-arm-vms-prepare#install-the-vm-agent).
* Upewnij się, że wersja systemu operacyjnego Windows lub Linux VM jest obsługiwana, zapoznaj się z [macierzą obsługi kopii zapasowych maszyn wirtualnych IaaS](https://docs.microsoft.com/azure/backup/backup-support-matrix-iaas).
* Sprawdź, czy inna usługa tworzenia kopii zapasowych nie jest uruchomiona.
  * Aby upewnić się, że nie ma żadnych problemów z rozszerzeniem migawki, [odinstaluj rozszerzenia, aby wymusić ponowne załadowanie, a następnie ponów próbę wykonania kopii zapasowej](https://docs.microsoft.com/azure/backup/backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout).
* Sprawdź, czy maszyna wirtualna ma połączenie z Internetem.
  * Upewnij się, że inna usługa tworzenia kopii zapasowych nie jest uruchomiona.
* Z `Services.msc`programu upewnij się, że usługa **Agent gościa systemu Windows Azure** jest **uruchomiona**. Jeśli brakuje usługi **Agenta gościa systemu Windows Azure,** zainstaluj ją z [kopii zapasowej maszyn wirtualnych platformy Azure w przechowalni usług odzyskiwania](https://docs.microsoft.com/azure/backup/backup-azure-arm-vms-prepare#install-the-vm-agent).
* **Dziennik zdarzeń** może wyświetlać błędy kopii zapasowej, które pochodzą z innych produktów kopii zapasowych, na przykład kopii zapasowej systemu Windows Server i nie są spowodowane tworzeniem kopii zapasowej platformy Azure. Wykonaj następujące kroki, aby ustalić, czy problem dotyczy usługi Azure Backup:
  * Jeśli wystąpił błąd z wpisem **Kopia zapasowa** w źródle zdarzenia lub wiadomości, sprawdź, czy kopie zapasowe maszyn wirtualnych usługi Azure IaaS zakończyły się pomyślnie i czy punkt przywracania został utworzony z żądanym typem migawki.
  * Jeśli usługa Azure Backup działa, problem prawdopodobnie dotyczy innego rozwiązania do tworzenia kopii zapasowych.
  * Oto przykład błędu podglądu zdarzeń 517, w którym kopia zapasowa platformy Azure działała poprawnie, ale "Kopia zapasowa systemu Windows Server" nie powiódł się:<br>
    ![Niepowodzenie kopii zapasowej systemu Windows Server](media/backup-azure-vms-troubleshoot/windows-server-backup-failing.png)
  * Jeśli usługa Azure Backup nie działa, poszukaj odpowiedniego kodu błędu w sekcji Typowe błędy kopii zapasowej maszyny Wirtualnej w tym artykule.

## <a name="common-issues"></a>Typowe problemy

Poniżej przedstawiono typowe problemy z błędami tworzenia kopii zapasowych na maszynach wirtualnych platformy Azure.

## <a name="copyingvhdsfrombackupvaulttakinglongtime---copying-backed-up-data-from-vault-timed-out"></a>CopyingVHDsFromBackUpVaultTakingLongTime — kopiowanie kopii zapasowej danych z przechowalni upłyniętej

Kod błędu: CopyingVHDsFromBackUpVaultTakingLongTime <br/>
Komunikat o błędzie: Kopiowanie kopii zapasowej danych z przechowalni upłyniętej

Może się to zdarzyć z powodu błędów przejściowego magazynu lub niewystarczającej liczby we/wy konta magazynu dla usługi tworzenia kopii zapasowych do przesyłania danych do magazynu w okresie limitu czasu. Skonfiguruj kopię zapasową maszyny Wirtualnej przy użyciu tych [najlepszych rozwiązań](backup-azure-vms-introduction.md#best-practices) i ponów próbę wykonania kopii zapasowej.

## <a name="usererrorvmnotindesirablestate---vm-is-not-in-a-state-that-allows-backups"></a>UserErrorVmNotInDesirableState — maszyna wirtualna nie jest w stanie umożliwiającym tworzenie kopii zapasowych

Kod błędu: UserErrorVmNotInDesirableState <br/>
Komunikat o błędzie: maszyna wirtualna nie jest w stanie, który umożliwia tworzenie kopii zapasowych.<br/>

Operacja tworzenia kopii zapasowej nie powiodła się, ponieważ maszyna wirtualna jest w stanie niepowodzeniem. Aby można było pomyślnie utworzyć kopię zapasową, maszyna wirtualna powinna być w stanie Uruchomiona, Zatrzymana lub Zatrzymana (cofnięcie przydziału).

* Jeśli maszyna wirtualna jest w stanie przejściowym między **uruchamiania** i **zamykania**, poczekaj na zmianę stanu. Następnie wyzwolić zadanie tworzenia kopii zapasowej.
* Jeśli maszyna wirtualna jest maszyną wirtualną z systemem Linux i używa modułu jądra systemu Linux z rozszerzonym o zabezpieczeń, wyklucz ścieżkę agenta systemu Azure Linux **/var/lib/waagent** z zasad zabezpieczeń i upewnij się, że jest zainstalowane rozszerzenie kopii zapasowej.

## <a name="usererrorfsfreezefailed---failed-to-freeze-one-or-more-mount-points-of-the-vm-to-take-a-file-system-consistent-snapshot"></a>UserErrorFsFreezeFailed — nie można zablokować jednego lub więcej punktów instalacji maszyny Wirtualnej w celu przeprowadzenia spójnej migawki systemu plików

Kod błędu: UserErrorFsFreezeFailed <br/>
Komunikat o błędzie: nie można zablokować co najmniej jednego punktu instalacji maszyny Wirtualnej w celu przeprowadzenia spójnej migawki systemu plików.

* Odinstaluj urządzenia, dla których stan systemu plików nie został wyczyszczony, za pomocą polecenia **umount.**
* Uruchom sprawdzanie spójności systemu plików na tych urządzeniach za pomocą polecenia **fsck.**
* Ponownie zamontuj urządzenia i ponów próbę wykonania kopii zapasowej.</ol>

## <a name="extensionsnapshotfailedcom--extensioninstallationfailedcom--extensioninstallationfailedmdtc---extension-installationoperation-failed-due-to-a-com-error"></a>ExtensionSnapshotFailedCOM / ExtensionInstallationFailedCOM / ExtensionInstallationFailedMDTC - Instalacja/operacja rozszerzenia nie powiodła się z powodu błędu COM+

Kod błędu: ExtensionSnapshotFailedCOM <br/>
Komunikat o błędzie: Operacja migawki nie powiodła się z powodu błędu COM+

Kod błędu: ExtensionInstallationFailedCOM  <br/>
Komunikat o błędzie: Instalacja/operacja rozszerzenia nie powiodła się z powodu błędu COM+

Kod błędu: ExtensionInstallationFailedMDTC <br/>
Komunikat o błędzie: Instalacja rozszerzenia nie powiodła się z powodu błędu "COM+ nie może rozmawiać z koordynatorem transakcji rozproszonych firmy Microsoft <br/>

Operacja tworzenia kopii zapasowej nie powiodła się z powodu problemu z aplikacją **systemu COM+ usługi** systemu Windows.  Aby rozwiązać ten problem, wykonaj poniższe czynności:

* Spróbuj uruchomić/ponownie uruchomić usługę systemu Windows **COM+ System Application** (z podwyższonego poziomu wiersza polecenia **- net start COMSysApp**).
* Upewnij się, że usługa **koordynatora transakcji rozproszonych** działa jako konto **usługi sieciowej.** Jeśli nie, zmień go, aby działał jako konto **usługi sieciowej** i uruchom ponownie **aplikację systemową COM+**.
* Jeśli nie można ponownie uruchomić usługi, zainstaluj ponownie usługę **koordynatora transakcji rozproszonych,** wykonując poniższe czynności:
  * Zatrzymaj usługę MSDTC
  * Otwórz wiersz polecenia (cmd)
  * Uruchom polecenie "msdtc -uninstall"
  * Uruchom polecenie "msdtc -install"
  * Uruchom usługę MSDTC
* Uruchom aplikację **systemową COM+** usługi windows . Po uruchomieniu **aplikacji systemowej COM+** wyzwalaj zadanie tworzenia kopii zapasowej z witryny Azure portal.</ol>

## <a name="extensionfailedvsswriterinbadstate---snapshot-operation-failed-because-vss-writers-were-in-a-bad-state"></a>ExtensionFailedVssWriterInBadState — operacja migawki nie powiodła się, ponieważ moduły zapisujące usługi VSS były w złym stanie

Kod błędu: ExtensionFailedVssWriterInBadState <br/>
Komunikat o błędzie: Operacja migawki nie powiodła się, ponieważ moduły zapisu programu VSS były w złym stanie.

Uruchom ponownie moduły zapisu usługi VSS, które są w złym stanie. Z wiersza polecenia z ```vssadmin list writers```podwyższonym poziomem uprawnień uruchom polecenie . Dane wyjściowe zawiera wszystkie moduły zapisu vss i ich stan. Dla każdego modułu zapisującego vss ze stanem, który nie jest **[1] Stabilny**, aby ponownie uruchomić moduł zapisujący vss, uruchom następujące polecenia z wiersza polecenia z podwyższonym poziomem uprawnień:

* ```net stop serviceName```
* ```net start serviceName```

## <a name="extensionconfigparsingfailure--failure-in-parsing-the-config-for-the-backup-extension"></a>ExtensionConfigParsingFailure — niepowodzenie podczas analizowania konfiguracji dla rozszerzenia kopii zapasowej

Kod błędu: ExtensionConfigParsingFailure<br/>
Komunikat o błędzie: Niepowodzenie podczas analizowania konfiguracji dla rozszerzenia kopii zapasowej.

Ten błąd występuje z powodu zmienionych uprawnień w katalogu **MachineKeys:** **%systemdrive%\programdata\microsoft\crypto\rsa\machinekeys**.
Uruchom następujące polecenie i sprawdź, czy uprawnienia do katalogu MachineKeys są uprawnieniami **domyślnymi:** **icacls %systemdrive%\programdata\microsoft\crypto\rsa\machinekeys**.

Uprawnienia domyślne są następujące:

* Wszyscy: (R,W)
* BUILTIN\Administratorzy: (F)

Jeśli w katalogu **MachineKeys** są widoczne uprawnienia inne niż ustawienia domyślne, wykonaj następujące kroki, aby poprawić uprawnienia, usunąć certyfikat i wyzwolić kopię zapasową:

1. Napraw uprawnienia w katalogu **MachineKeys.** Korzystając z właściwości zabezpieczeń Eksploratora i zaawansowanych ustawień zabezpieczeń w katalogu, należy przywrócić uprawnienia do wartości domyślnych. Usuń wszystkie obiekty użytkownika z wyjątkiem ustawień domyślnych z katalogu i upewnij **się,** że uprawnienie Wszyscy ma specjalny dostęp w następujący sposób:

   * Lista folderów/odczytu danych
   * Odczytanie atrybutów
   * Odczyt atrybutów rozszerzonych
   * Tworzenie plików/zapisywanie danych
   * Tworzenie folderów/dołączanie danych
   * Zapis atrybuty
   * Zapisywanie atrybutów rozszerzonych
   * Uprawnienia do odczytu
2. Usuń wszystkie certyfikaty, na których **wystawiono to** klasyczny model wdrażania lub **generator certyfikatów CRP systemu Windows Azure:**

   * [Otwieranie certyfikatów na konsoli komputera lokalnego](https://docs.microsoft.com/dotnet/framework/wcf/feature-details/how-to-view-certificates-with-the-mmc-snap-in).
   * W obszarze**Certyfikaty** **osobiste** > usuń wszystkie certyfikaty, w których **wystawiono do** tego modelu wdrożenia klasycznego lub **generatora certyfikatów CRP systemu Windows Azure**.
3. Wyzwalanie zadania tworzenia kopii zapasowej maszyny Wirtualnej.

## <a name="extensionstuckindeletionstate---extension-state-is-not-supportive-to-backup-operation"></a>ExtensionStuckInDeletionState — stan rozszerzenia nie jest wsparciem dla operacji tworzenia kopii zapasowej

Kod błędu: ExtensionStuckInDeletionState <br/>
Komunikat o błędzie: Stan rozszerzenia nie jest wsparciem dla operacji tworzenia kopii zapasowej

Operacja tworzenia kopii zapasowej nie powiodła się z powodu niespójnego stanu rozszerzenia kopii zapasowej. Aby rozwiązać ten problem, wykonaj poniższe czynności:

* Upewnij się, że agent gościa jest zainstalowany i odpowiada
* W witrynie Azure portal przejdź do sekcji Wszystkie rozszerzenia**ustawień** >  **maszyny wirtualnej** > **Extensions**
* Wybierz rozszerzenie kopii zapasowej VmSnapshot lub VmSnapshotLinux, a następnie kliknij pozycję **Odinstaluj**
* Po usunięciu rozszerzenia kopii zapasowej ponów próbę wykonania kopii zapasowej
* Kolejna operacja tworzenia kopii zapasowej spowoduje zainstalowanie nowego rozszerzenia w odpowiednim stanie

## <a name="extensionfailedsnapshotlimitreachederror---snapshot-operation-failed-as-snapshot-limit-is-exceeded-for-some-of-the-disks-attached"></a>ExtensionFailedSnapshotLimitReachedError — operacja migawki nie powiodła się, ponieważ limit migawek został przekroczony dla niektórych podłączonych dysków

Kod błędu: ExtensionFailedSnapshotLimitReachedError <br/>
Komunikat o błędzie: Operacja migawki nie powiodła się, ponieważ limit migawek został przekroczony dla niektórych podłączonych dysków

Operacja migawki nie powiodła się, ponieważ limit migawki został przekroczony dla niektórych dysków dołączonych. Wykonaj poniższe kroki rozwiązywania problemów, a następnie ponów próbę wykonania operacji.

* Usuń migawki obiektów blob dysku, które nie są wymagane. Należy zachować ostrożność, aby nie usuwać obiektu blob dysku, tylko obiekty blob migawki powinny zostać usunięte.
* Jeśli soft-delete jest włączona na kontach magazynu dysku maszyny Wirtualnej, skonfiguruj przechowywanie usuwania nietrwałego w taki sposób, aby istniejące migawki były mniejsze niż maksymalna dozwolona w dowolnym momencie.
* Jeśli usługa Azure Site Recovery jest włączona na kopii zapasowej maszyny Wirtualnej, wykonaj poniższe czynności:

  * Upewnij się, że wartość **isanysnapshotfailed** jest ustawiona jako false w /etc/azure/vmbackup.conf
  * Zaplanuj usługę Azure Site Recovery w innym czasie, tak aby nie kolidować z operacją tworzenia kopii zapasowej.

## <a name="extensionfailedtimeoutvmnetworkunresponsive---snapshot-operation-failed-due-to-inadequate-vm-resources"></a>ExtensionFailedTimeoutVMNetworkUnresponsive — operacja migawki nie powiodła się z powodu nieodpowiednich zasobów maszyn wirtualnych

Kod błędu: ExtensionFailedTimeoutVMNetworkUnresponsive<br/>
Komunikat o błędzie: Operacja migawki nie powiodła się z powodu niewystarczających zasobów maszyny Wirtualnej.

Operacja tworzenia kopii zapasowej na maszynie wirtualnej nie powiodła się z powodu opóźnienia w wywołaniach sieciowych podczas wykonywania operacji migawki. Aby rozwiązać ten problem, wykonaj krok 1. Jeśli problem będzie się powtarzał, spróbuj wykonać kroki 2 i 3.

**Krok 1:** Tworzenie migawki za pośrednictwem hosta

W wierszu polecenia z podwyższonym poziomem uprawnień (administrator) uruchom poniższe polecenie:

```text
REG ADD "HKLM\SOFTWARE\Microsoft\BcdrAgentPersistentKeys" /v SnapshotMethod /t REG_SZ /d firstHostThenGuest /f
REG ADD "HKLM\SOFTWARE\Microsoft\BcdrAgentPersistentKeys" /v CalculateSnapshotTimeFromHost /t REG_SZ /d True /f
```

Dzięki temu migawki będą wykonywane za pośrednictwem hosta, a nie konta gościa. Ponów próbę wykonania operacji tworzenia kopii zapasowej.

**Krok 2:** Spróbuj zmienić harmonogram tworzenia kopii zapasowych na czas, gdy maszyna wirtualna jest mniej obciążona (mniej procesora/ IOps itp.)

**Krok 3**: Spróbuj [zwiększyć rozmiar maszyny Wirtualnej](https://azure.microsoft.com/blog/resize-virtual-machines/) i ponów próbę wykonania operacji

## <a name="common-vm-backup-errors"></a>Typowe błędy kopii zapasowej maszyny wirtualnej

| Szczegóły błędu | Obejście |
| ------ | --- |
| **Kod błędu**: 320001, ResourceNotFound <br/> **Komunikat o błędzie:** Nie można wykonać operacji, ponieważ maszyna wirtualna już nie istnieje. <br/> <br/> **Kod błędu**: 400094, BCMV2VMNotFound <br/> **Komunikat o błędzie:** Maszyna wirtualna nie istnieje <br/> <br/>  Nie znaleziono maszyny wirtualnej platformy Azure.  |Ten błąd występuje, gdy podstawowa maszyna wirtualna jest usuwana, ale zasady tworzenia kopii zapasowej nadal szuka maszyny Wirtualnej do utworzenia kopii zapasowej. Aby naprawić ten błąd, należy wykonać następujące czynności: <ol><li> Ponownie utwórz maszynę wirtualną o tej samej nazwie i tej samej nazwie grupy zasobów, **nazwie usługi w chmurze**,<br>**Lub**</li><li> Zatrzymaj ochronę maszyny wirtualnej z lub bez usuwania danych kopii zapasowej. Aby uzyskać więcej informacji, zobacz [Zatrzymywać ochronę maszyn wirtualnych](backup-azure-manage-vms.md#stop-protecting-a-vm).</li></ol>|
|**Kod błędu**: UserErrorBCMPremiumStorageQuotaError<br/> **Komunikat o błędzie:** Nie można skopiować migawki maszyny wirtualnej z powodu niewystarczającej ilości wolnego miejsca na koncie magazynu | Dla maszyn wirtualnych w wersji premium na stosie kopii zapasowej maszyny Wirtualnej v1, możemy skopiować migawkę do konta magazynu. Ten krok zapewnia, że ruch zarządzania tworzeniem kopii zapasowych, który działa na migawki, nie ogranicza liczbę we/wy dostępne dla aplikacji przy użyciu dysków w wersji premium. <br><br>Zaleca się przydzielić tylko 50 procent, 17,5 TB całkowitego miejsca na koncie magazynu. Następnie usługa Azure Backup można skopiować migawkę do konta magazynu i przenieść dane z tej skopiowanej lokalizacji na koncie magazynu do magazynu. |
| **Kod błędu**: 380008, AzureVmOffline <br/> **Komunikat o błędzie:** Nie można zainstalować rozszerzenia usług odzyskiwania firmy Microsoft, ponieważ maszyna wirtualna nie jest uruchomiona | Agent maszyny Wirtualnej jest warunkiem wstępnym rozszerzenia usług Azure Recovery Services. Zainstaluj agenta maszyny wirtualnej platformy Azure i uruchom ponownie operację rejestracji. <br> <ol> <li>Sprawdź, czy agent maszyny Wirtualnej jest poprawnie zainstalowany. <li>Upewnij się, że flaga na config maszyny Wirtualnej jest ustawiona poprawnie.</ol> Dowiedz się więcej o instalowaniu agenta maszyny Wirtualnej i sprawdzaniu poprawności instalacji agenta maszyny Wirtualnej. |
| **Kod błędu**: ExtensionSnapshotBitlockerError <br/> **Komunikat o błędzie:** Operacja migawki nie powiodła się z powodu błędu operacji kopiowania woluminów (VSS) **Ten dysk jest zablokowany przez szyfrowanie dysków funkcją BitLocker. Musisz odblokować ten dysk z Panelu sterowania.** |Wyłącz funkcję BitLocker dla wszystkich dysków na maszynie Wirtualnej i sprawdź, czy problem z usługi VSS został rozwiązany. |
| **Kod błędu**: VmNotInDesirableState <br/> **Komunikat o błędzie:** Maszyna wirtualna nie jest w stanie, który umożliwia tworzenie kopii zapasowych. |<ul><li>Jeśli maszyna wirtualna jest w stanie przejściowym między **uruchamiania** i **zamykania**, poczekaj na zmianę stanu. Następnie wyzwolić zadanie tworzenia kopii zapasowej. <li> Jeśli maszyna wirtualna jest maszyną wirtualną z systemem Linux i używa modułu jądra systemu Linux z rozszerzonym o zabezpieczeń, wyklucz ścieżkę agenta systemu Azure Linux **/var/lib/waagent** z zasad zabezpieczeń i upewnij się, że jest zainstalowane rozszerzenie kopii zapasowej.  |
| Agent maszyny Wirtualnej nie jest obecny na maszynie wirtualnej: <br>Zainstaluj wszystkie wymagane i agenta maszyny Wirtualnej. Następnie uruchom ponownie operację. |Dowiedz się więcej o [instalacji agenta maszyn wirtualnych i sprawdzaniu poprawności instalacji agenta maszyn wirtualnych](#vm-agent). |
| **Kod błędu**: ExtensionSnapshotFailedNoSecureNetwork <br/> **Komunikat o błędzie:** Operacja migawki nie powiodła się z powodu niepowodzenia w utworzeniu bezpiecznego kanału komunikacji sieciowej. | <ol><li> Otwórz Edytor rejestru, uruchamiając **plik regedit.exe** w trybie podwyższonego poziomu. <li> Zidentyfikuj wszystkie wersje programu .NET Framework dostępne w systemie. Są one obecne w hierarchii klucza rejestru **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft**. <li> Dla każdego programu .NET Framework obecnego w kluczu rejestru dodaj następujący klucz: <br> **SchUseStrongCrypto"=dword:00000001**. </ol>|
| **Kod błędu**: ExtensionVCRedistInstallationFailure <br/> **Komunikat o błędzie:** Operacja migawki nie powiodła się z powodu niepowodzenia instalacji programu Visual C++ Redystrybucyjne dla programu Visual Studio 2012. | Przejdź do pozycji C:\Packages\Plugins\Microsoft.Azure.RecoveryServices.VMSnapshot\agentVersion i zainstaluj vcredist2013_x64.<br/>Upewnij się, że wartość klucza rejestru, która umożliwia instalację usługi jest ustawiona na poprawną wartość. Oznacza to, że ustaw wartość **Start** w **HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Msiserver** na **3,** a nie **4**. <br><br>Jeśli nadal występują problemy z instalacją, uruchom ponownie usługę instalacyjną, uruchamiając **msiexec /unregister,** a następnie **msiexec /REGISTER** z wiersza polecenia z podwyższonym poziomem uprawnień.  |
| **Kod błędu**: UserErrorRequestDisallowedByPolicy <BR> **Komunikat o błędzie:** nieprawidłowa zasada jest skonfigurowana na maszynie wirtualnej, co uniemożliwia działanie migawki. | Jeśli masz zasadę platformy Azure, która [reguluje tagi w danym środowisku,](https://docs.microsoft.com/azure/governance/policy/tutorials/govern-tags)rozważ zmianę zasad z [efektu Odmów](https://docs.microsoft.com/azure/governance/policy/concepts/effects#deny) na [efekt modyfikuj](https://docs.microsoft.com/azure/governance/policy/concepts/effects#modify)lub utwórz grupę zasobów ręcznie zgodnie ze [schematem nazewnictwa wymaganym przez usługę Azure Backup](https://docs.microsoft.com/azure/backup/backup-during-vm-creation#azure-backup-resource-group-for-virtual-machines).

## <a name="jobs"></a>Stanowiska

| Szczegóły błędu | Obejście |
| --- | --- |
| Anulowanie nie jest obsługiwane dla tego typu zadania: <br>Poczekaj, aż zadanie się zakończy. |Brak |
| Zadanie nie jest w stanie anulowania: <br>Poczekaj, aż zadanie się zakończy. <br>**Lub**<br> Wybrane zadanie nie jest anulowane: <br>Poczekaj na zakończenie zadania. |Jest prawdopodobne, że zadanie jest prawie gotowy. Poczekaj, aż zadanie zostanie zakończone.|
| Kopia zapasowa nie może anulować zadania, ponieważ nie jest ono w toku: <br>Anulowanie jest obsługiwane tylko dla zadań w toku. Spróbuj anulować zadanie w toku. |Ten błąd występuje z powodu stanu przejściowego. Zaczekaj minutę i ponów próbę anulowania operacji. |
| Anulowanie zadania nie powiodło się: <br>Poczekaj, aż zadanie się zakończy. |Brak |

## <a name="restore"></a>Przywracanie

| Szczegóły błędu | Obejście |
| --- | --- |
| Przywracanie nie powiodło się z błędem wewnętrznym chmury. |<ol><li>Usługa w chmurze, którą próbujesz przywrócić, jest skonfigurowana z ustawieniami DNS. Możesz sprawdzić: <br>**$deployment = Get-AzureDeployment -ServiceName "ServiceName" -Slot "Produkcja" Get-AzureDns -DnsSettings $deployment. DnsSettings**.<br>Jeśli **adres** jest skonfigurowany, skonfigurowane są ustawienia DNS.<br> <li>Usługa w chmurze, do której próbujesz przywrócić jest skonfigurowana z **ReservedIP**, a istniejące maszyny wirtualne w usłudze w chmurze są w stanie zatrzymania. Można sprawdzić, czy usługa w chmurze zarezerwowała adres IP przy użyciu następujących poleceń cmdlet programu PowerShell: **$deployment = Get-AzureDeployment -ServiceName "servicename" -Slot "Produkcja" $dep. ReservedIPName**. <br><li>Próbujesz przywrócić maszynę wirtualną z następującymi specjalnymi konfiguracjami sieci do tej samej usługi w chmurze: <ul><li>Maszyny wirtualne w konfiguracji modułu równoważenia obciążenia, wewnętrzne i zewnętrzne.<li>Maszyny wirtualne z wieloma zastrzeżonymi adresami IP. <li>Maszyny wirtualne z wieloma kartami sieciowymi. </ul><li>Wybierz nową usługę w chmurze w interfejsie użytkownika lub zobacz [zagadnienia przywracania](backup-azure-arm-restore-vms.md#restore-vms-with-special-configurations) dla maszyn wirtualnych ze specjalnymi konfiguracjami sieci.</ol> |
| Wybrana nazwa DNS jest już zajęta: <br>Określ inną nazwę DNS i spróbuj ponownie. |Ta nazwa DNS odnosi się do nazwy usługi w chmurze, zwykle kończącej się **na .cloudapp.net**. Ta nazwa musi być unikatowa. Jeśli ten błąd, należy wybrać inną nazwę maszyny Wirtualnej podczas przywracania. <br><br> Ten błąd jest wyświetlany tylko użytkownikom witryny Azure portal. Operacja przywracania za pośrednictwem programu PowerShell powiedzie się, ponieważ przywraca tylko dyski i nie tworzy maszyny Wirtualnej. Błąd będzie napotkany, gdy maszyna wirtualna jest jawnie tworzona przez Ciebie po operacji przywracania dysku. |
| Określona konfiguracja sieci wirtualnej nie jest poprawna: <br>Określ inną konfigurację sieci wirtualnej i spróbuj ponownie. |Brak |
| Określona usługa w chmurze używa zastrzeżonego adresu IP, który nie jest zgodny z konfiguracją przywracanej maszyny wirtualnej: <br>Określ inną usługę w chmurze, która nie używa zastrzeżonego adresu IP. Możesz też wybrać inny punkt odzyskiwania do przywrócenia. |Brak |
| Usługa w chmurze osiągnęła swój limit liczby wejściowych punktów końcowych: <br>Ponów próbę wykonania operacji, określając inną usługę w chmurze lub przy użyciu istniejącego punktu końcowego. |Brak |
| Magazyn usług odzyskiwania i docelowe konto magazynu znajdują się w dwóch różnych regionach: <br>Upewnij się, że konto magazynu określone w operacji przywracania znajduje się w tym samym regionie platformy Azure, co magazyn usług odzyskiwania. |Brak |
| Konto magazynu określone dla operacji przywracania nie jest obsługiwane: <br>Obsługiwane są tylko konta magazynu podstawowego lub standardowego z lokalnie nadmiarowymi lub geograficznie nadmiarowymi ustawieniami replikacji. Wybierz obsługiwane konto magazynu. |Brak |
| Typ konta magazynu określonego dla operacji przywracania nie jest w trybie online: <br>Upewnij się, że konto magazynu określone w operacji przywracania jest w trybie online. |Ten błąd może wystąpić z powodu przejściowego błędu w usłudze Azure Storage lub awarii. Wybierz inne konto magazynu. |
| Osiągnięto przydział grupy zasobów: <br>Usuń niektóre grupy zasobów z witryny Azure portal lub skontaktuj się z pomocą techniczną platformy Azure, aby zwiększyć limity. |Brak |
| Wybrana podsieć nie istnieje: <br>Wybierz istniejącą podsieć. |Brak |
| Usługa kopia zapasowa nie ma autoryzacji dostępu do zasobów w ramach subskrypcji. |Aby rozwiązać ten problem, najpierw przywróć dyski, wykonując czynności opisane w [obszarze Przywracanie kopii zapasowych dysków](backup-azure-arm-restore-vms.md#restore-disks). Następnie użyj kroków programu PowerShell w [programie Utwórz maszynę wirtualną z przywróconych dysków](backup-azure-vms-automation.md#restore-an-azure-vm). |

## <a name="backup-or-restore-takes-time"></a>Tworzenie kopii zapasowych lub przywracanie wymaga czasu

Jeśli kopia zapasowa trwa dłużej niż 12 godzin lub przywracanie trwa dłużej niż 6 godzin, zapoznaj się z [najlepszymi rozwiązaniami](backup-azure-vms-introduction.md#best-practices)i [zagadnieniami wydajności](backup-azure-vms-introduction.md#backup-performance)

## <a name="vm-agent"></a>Agent maszyny Wirtualnej

### <a name="set-up-the-vm-agent"></a>Konfigurowanie agenta maszyny Wirtualnej

Zazwyczaj agent maszyny wirtualnej jest już obecny w maszynach wirtualnych, które są tworzone z galerii platformy Azure. Jednak maszyny wirtualne, które są migrowane z lokalnych centrów danych, nie będą miały zainstalowanego agenta maszyny wirtualnej. Dla tych maszyn wirtualnych agent maszyny wirtualnej musi być zainstalowany jawnie.

#### <a name="windows-vms"></a>Maszyny wirtualne z systemem Windows

* Pobierz i zainstaluj [plik MSI agenta](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Aby zakończyć instalację, potrzebne są uprawnienia administratora.
* W przypadku maszyn wirtualnych utworzonych przy użyciu klasycznego modelu wdrażania [należy zaktualizować właściwość maszyny Wirtualnej,](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/install-vm-agent-offline#use-the-provisionguestagent-property-for-classic-vms) aby wskazać, że agent jest zainstalowany. Ten krok nie jest wymagany dla maszyn wirtualnych usługi Azure Resource Manager.

#### <a name="linux-vms"></a>Maszyny wirtualne z systemem Linux

* Zainstaluj najnowszą wersję agenta z repozytorium dystrybucji. Aby uzyskać szczegółowe informacje na temat nazwy pakietu, zobacz [repozytorium agenta systemu Linux](https://github.com/Azure/WALinuxAgent).
* W przypadku maszyn wirtualnych utworzonych przy użyciu klasycznego modelu wdrażania [należy zaktualizować właściwość maszyny Wirtualnej](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/install-vm-agent-offline#use-the-provisionguestagent-property-for-classic-vms) i sprawdzić, czy agent jest zainstalowany. Ten krok nie jest wymagany dla maszyn wirtualnych Menedżera zasobów.

### <a name="update-the-vm-agent"></a>Aktualizowanie agenta maszyny Wirtualnej

#### <a name="windows-vms"></a>Maszyny wirtualne z systemem Windows

* Aby zaktualizować agenta maszyny Wirtualnej, zainstaluj ponownie [pliki binarne agenta maszyny Wirtualnej](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Przed zaktualizowaniem agenta upewnij się, że podczas aktualizacji agenta maszyny Wirtualnej nie występują żadne operacje tworzenia kopii zapasowej.

#### <a name="linux-vms"></a>Maszyny wirtualne z systemem Linux

* Aby zaktualizować agenta maszyny Wirtualnej systemu Linux, postępuj zgodnie z instrukcjami zawartymi w artykule [Aktualizowanie agenta maszyny wirtualnej z systemem Linux](../virtual-machines/linux/update-agent.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

    > [!NOTE]
    > Zawsze używaj repozytorium dystrybucji, aby zaktualizować agenta.

    Nie pobieraj kodu agenta z usługi GitHub. Jeśli najnowszy agent nie jest dostępny dla twojej dystrybucji, skontaktuj się z pomocą techniczną dystrybucji, aby uzyskać instrukcje dotyczące pozyskania najnowszego agenta. Można również sprawdzić najnowsze informacje [o agencie systemu Windows Azure Linux](https://github.com/Azure/WALinuxAgent/releases) w repozytorium GitHub.

### <a name="validate-vm-agent-installation"></a>Sprawdzanie poprawności instalacji agenta maszyny Wirtualnej

Sprawdź wersję agenta maszyn wirtualnych na maszynach wirtualnych z systemem Windows:

1. Zaloguj się do maszyny wirtualnej platformy Azure i przejdź do folderu **C:\WindowsAzure\Packages**. Powinieneś znaleźć plik **WaAppAgent.exe.**
2. Kliknij prawym przyciskiem myszy plik i przejdź do **pliku Właściwości**. Następnie wybierz kartę **Szczegóły.** Pole **Wersja produktu** powinno mieć 2.6.1198.718 lub wyższą.

## <a name="troubleshoot-vm-snapshot-issues"></a>Rozwiązywanie problemów z migawką maszyny Wirtualnej

Kopia zapasowa maszyny Wirtualnej opiera się na wydawaniu poleceń migawki do magazynu źródłowego. Brak dostępu do magazynu lub opóźnienia w uruchomieniu zadania migawki może spowodować niepowodzenie zadania tworzenia kopii zapasowej. Następujące warunki mogą spowodować niepowodzenie zadania migawki:

* **Maszyny wirtualne ze skonfigurowaniem kopii zapasowej programu SQL Server mogą powodować opóźnienie zadania migawki**. Domyślnie kopia zapasowa maszyny Wirtualnej tworzy pełną kopię zapasową usługi VSS na maszynach wirtualnych z systemem Windows. Maszyny wirtualne, które uruchamiają program SQL Server, ze skonfigurowaną kopią zapasową programu SQL Server, mogą doświadczać opóźnień migawek. Jeśli opóźnienia migawki powodują błędy kopii zapasowej, ustaw następujący klucz rejestru:

   ```text
   [HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\BCDRAGENT]
   "USEVSSCOPYBACKUP"="TRUE"
   ```

* **Stan maszyny Wirtualnej jest zgłaszany niepoprawnie, ponieważ maszyna wirtualna jest wyłączana w prow.** Jeśli pulpit zdalny został zamknięty do zamknięcia maszyny wirtualnej, sprawdź, czy stan maszyny wirtualnej w portalu jest poprawny. Jeśli stan nie jest poprawny, użyj opcji **Zamykanie** w panelu maszyny Wirtualnej portalu, aby zamknąć maszynę wirtualną.
* **Jeśli więcej niż cztery maszyny wirtualne współużytkuje tę samą usługę w chmurze, rozłóż maszyny wirtualne na wiele zasad tworzenia kopii zapasowych.** Rozkładanie czasu wykonywania kopii zapasowej, więc nie więcej niż cztery kopie zapasowe maszyn wirtualnych rozpoczynają się w tym samym czasie. Spróbuj oddzielić godziny rozpoczęcia w zasadach o co najmniej godzinę.
* **Maszyna wirtualna działa z wysokim procesorem LUB pamięcią**. Jeśli maszyna wirtualna działa przy użyciu wysokiej pamięci lub procesora CPU, więcej niż 90 procent, zadanie migawki jest w kolejce i opóźnione. W końcu to czas. Jeśli ten problem się zdarzy, spróbuj wykonać kopię zapasową na żądanie.

## <a name="networking"></a>Networking

Usługa DHCP musi być włączona wewnątrz gościa, aby kopia zapasowa maszyny wirtualnej usługi IaaS działała. Jeśli potrzebujesz statycznego prywatnego adresu IP, skonfiguruj go za pośrednictwem witryny Azure portal lub programu PowerShell. Upewnij się, że opcja DHCP wewnątrz maszyny Wirtualnej jest włączona.
Uzyskaj więcej informacji na temat konfigurowania statycznego adresu IP za pośrednictwem programu PowerShell:

* [Jak dodać statyczny wewnętrzny adres IP do istniejącej maszyny Wirtualnej](https://docs.microsoft.com/powershell/module/az.network/set-aznetworkinterfaceipconfig?view=azps-3.5.0#description)
* [Zmienianie metody alokacji prywatnego adresu IP przypisanego do interfejsu sieciowego](../virtual-network/virtual-networks-static-private-ip-arm-ps.md#change-the-allocation-method-for-a-private-ip-address-assigned-to-a-network-interface)
