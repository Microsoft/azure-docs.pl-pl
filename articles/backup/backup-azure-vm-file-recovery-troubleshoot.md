---
title: Rozwiązywanie problemów z odzyskiwaniem plików maszyny wirtualnej platformy Azure
description: Rozwiązywanie problemów związanych z odzyskiwaniem plików i folderów z kopii zapasowej maszyny wirtualnej platformy Azure.
ms.reviewer: srinathv
ms.topic: troubleshooting
ms.date: 07/12/2020
ms.openlocfilehash: 96876415405cc5099b4d9ca209c36086b793d6e0
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/15/2020
ms.locfileid: "97513409"
---
# <a name="troubleshooting-issues-in-file-recovery-of-azure-vm-backup"></a>Rozwiązywanie problemów z odzyskiwaniem plików z kopii zapasowej maszyny wirtualnej platformy Azure

W tym artykule opisano kroki rozwiązywania problemów, które mogą pomóc w rozwiązywaniu Azure Backup błędów związanych z problemami związanymi z odzyskiwaniem plików i folderów z kopii zapasowej maszyny wirtualnej platformy Azure. 

## <a name="exception-caught-while-connecting-to-target"></a>Przechwycono wyjątek podczas łączenia z miejscem docelowym

Możliwa przyczyna: skrypt nie może uzyskać dostępu do zalecanej akcji punktu odzyskiwania: Sprawdź, czy maszyna spełnia wszystkie [wymagania dotyczące dostępu](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#step-4-access-requirements-to-successfully-run-the-script).

## <a name="the-target-has-already-been-logged-in-via-an-iscsi-session"></a>Element docelowy został już zalogowany za pośrednictwem sesji iSCSI

Możliwa przyczyna: skrypt został już wykonany na tym samym komputerze, a dyski zostały podłączone.
Zalecana akcja: woluminy punktu odzyskiwania zostały już dołączone. Mogą nie być zainstalowane z tymi samymi literami dysków oryginalnej maszyny wirtualnej. Przeglądaj wszystkie dostępne woluminy w Eksploratorze plików.

## <a name="this-script-is-invalid-because-the-disks-have-been-dismounted-via-portalexceeded-the-12-hr-limit-download-a-new-script-from-the-portal"></a>Ten skrypt jest nieprawidłowy, ponieważ dyski zostały odinstalowane za pośrednictwem portalu/przekroczyły limit 12-godzinny. Pobieranie nowego skryptu z portalu

Możliwa przyczyna: dyski zostały odinstalowane z portalu lub Przekroczono limit 12 godzin.
Zalecana akcja: skrypt jest nieprawidłowy po 12 godzinach od momentu pobrania i nie można go wykonać. Odwiedź portal i Pobierz nowy skrypt, aby kontynuować odzyskiwanie plików.

## <a name="troubleshooting-common-issues"></a>Rozwiązywanie typowych problemów

Ta sekcja zawiera kroki rozwiązywania problemów występujących podczas pobierania i wykonywania skryptu na potrzeby odzyskiwania plików.

## <a name="cannot-download-the-script"></a>Nie można pobrać skryptu

Zalecana akcja:

1. Upewnij się, że masz wszystkie [wymagane uprawnienia do pobrania skryptu](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#select-recovery-point-who-can-generate-script).
2. Upewnij się, że nawiązano połączenie z docelowym adresem IP platformy Azure.
Aby zweryfikować połączenie, uruchom następujące polecenie w wierszu polecenia z podwyższonym poziomem uprawnień. 
    - *nslookup Download.Microsoft.com* lub
    - *polecenie ping download.microsoft.com*

## <a name="the-script-downloads-successfully-but-fails-to-run"></a>Skrypt zostanie pobrany pomyślnie, ale uruchomienie nie powiedzie się

### <a name="for-sles-12-sp4-os-linux"></a>Dla systemu operacyjnego SLES 12 z dodatkiem SP4 (Linux)

Błąd: nie znaleziono iscsi_tcp_module

Możliwa przyczyna: podczas uruchamiania skryptu języka Python na potrzeby odzyskiwania na poziomie elementu w systemie SUSE Linux w wersji 12sp4, kończy się niepowodzeniem z powodu błędu ***nie można załadować modułu iscsi_tcp**. Moduł ILR używa "iscsi_tcp", aby nawiązać połączenie TCP z usługą kopii zapasowej, SUSE w ramach wydania 12SP4 usuniętego iscsi_tcp z pakietu Open-iSCSI, w związku z czym operacja ILR nie powiedzie się.

Zalecana akcja: wykonywanie skryptu odzyskiwania plików nie jest obsługiwane na maszynach wirtualnych SUSE 12SP4, spróbuj wykonać operację przywracania w starszej wersji SUSE 12SP4.

## <a name="the-script-runs-but-connection-to-iscsi-target-failed"></a>Skrypt jest uruchamiany, ale nie można nawiązać połączenia z obiektem docelowym iSCSI

Błąd: Przechwycono wyjątek podczas łączenia z miejscem docelowym

1. Upewnij się, że komputer, na którym jest uruchomiony skrypt, spełnia wszystkie [wymagania dotyczące dostępu](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#step-4-access-requirements-to-successfully-run-the-script).
2. Sprawdź łączność z docelowymi adresami IP platformy Azure.
Aby zweryfikować połączenie, uruchom następujące polecenie w wierszu polecenia z podwyższonym poziomem uprawnień. 
    - _nslookup download.microsoft.com * lub<br>
    - *polecenie ping download.microsoft.com*
3. Upewnij się, że istnieje dostęp do portu wychodzącego iSCSI 3260.
4. Upewnij się, że nie ma żadnej zapory/sieciowej grupy zabezpieczeń, która blokuje ruch do docelowych adresów IP lub usług odzyskiwania platformy Azure.
5. Sprawdź, czy program antywirusowy blokuje wykonywanie skryptu.

## <a name="connected-to-recovery-point-but-disks-did-not-get-attached"></a>Połączono z punktem odzyskiwania, ale nie połączono dysków

Upewnij się, że masz odpowiednią [maszynę do uruchomienia skryptu](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#step-2-ensure-the-machine-meets-the-requirements-before-executing-the-script)

### <a name="on-windows-vm"></a>Na maszynie wirtualnej z systemem Windows

**Pula magazynów na maszynie wirtualnej jest dołączona w trybie tylko do odczytu** W przypadku systemów Windows 2012 R2 i Windows 2016 (z pulą magazynów) podczas pierwszego uruchamiania skryptu Pula magazynów dołączona do maszyny wirtualnej może przejść w stan tylko do odczytu.

Aby rozwiązać ten problem, należy ręcznie ustawić Read-Write dostępu do puli magazynu po raz pierwszy i dołączyć dyski wirtualne, wykonując poniższe kroki:

1. Ustaw dostęp Read-Write.
Przejdź do Menedżer serwera > usługi plików i magazynowania > woluminy > Pule magazynów.

   ![Magazyn systemu Windows](./media/backup-azure-restore-files-from-vm/windows-storage-1.png)

2. W oknie **Pula magazynów** kliknij prawym przyciskiem myszy dostępną pulę magazynów, a następnie wybierz opcję **Ustaw Read-Write dostęp** .

   ![Zapis odczytu z magazynu systemu Windows](./media/backup-azure-restore-files-from-vm/windows-storage-read-write-2.png)

3. Po przypisaniu puli magazynów z dostępem do odczytu/zapisu musimy dołączyć dysk wirtualny.
Przejdź do **Menedżer serwera interfejsu użytkownika**, w sekcji dyski wirtualne > kliknij prawym przyciskiem myszy, aby wybrać opcję **Dołącz dysk wirtualny** .

   ![Dysk wirtualny Menedżera serwera](./media/backup-azure-restore-files-from-vm/server-manager-virtual-disk-3.png)

### <a name="on-linux-vm"></a>Na maszynie wirtualnej z systemem Linux

#### <a name="file-recovery-fails-to-auto-mount-because-disk-does-not-contain-volumes"></a>Nie można przeprowadzić automatycznej instalacji odzyskiwania plików, ponieważ dysk nie zawiera woluminów

Podczas odzyskiwania plików usługa Backup wykrywa woluminy i instaluje je. Jeśli jednak kopie zapasowe dysków mają surowe partycje, te dyski nie są instalowane w sposób nieinstalowany i nie można zobaczyć dysku danych na potrzeby odzyskiwania.

Aby rozwiązać ten problem, wykonaj kroki opisane w tym [artykule](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#lvmraid-arrays-for-linux-vms).
 
#### <a name="os-could-not-identify-the-filesystem-causing-linux-file-recovery-to-fail-while-mountings-disks"></a>System operacyjny nie może zidentyfikować systemu plików powodującego niepowodzenie odzyskiwania plików z systemem Linux podczas instalacji dysków

Podczas uruchamiania dysku danych skryptu odzyskiwania plików nie można dołączyć z powodu następującego błędu: *nie można zainstalować następujących partycji, ponieważ system operacyjny nie może zidentyfikować systemu plików*

- Aby rozwiązać ten problem, sprawdź, czy wolumin jest szyfrowany przy użyciu aplikacji innej firmy. Jeśli zaszyfrowane, dysk lub maszyna wirtualna nie będą wyświetlane w portalu jako zaszyfrowane.
1. Zaloguj się do kopii zapasowej maszyny wirtualnej i uruchom polecenie: *lsblk-f*<br>

   ![Dysk bez woluminu](./media/backup-azure-restore-files-from-vm/disk-without-volume-5.png)

2. Sprawdź system plików i szyfrowanie.
3. Jeśli wolumin jest szyfrowany, odzyskiwanie plików nie jest obsługiwane. [Dowiedz się więcej](https://docs.microsoft.com/azure/backup/backup-support-matrix-iaas#support-for-file-level-restore).

## <a name="disks-are-attached-but-unable-to-mount-volumes"></a>Dyski są dołączone, ale nie można zainstalować woluminów

- Upewnij się, że masz odpowiednią [maszynę do uruchomienia skryptu](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#step-2-ensure-the-machine-meets-the-requirements-before-executing-the-script).

### <a name="on-windows-vm"></a>Na maszynie wirtualnej z systemem Windows

Podczas uruchamiania skryptu odzyskiwania plików dla systemu Windows istnieje komunikat, że ***0 woluminy są dołączone** _, ale dyski są odnajdywane w konsoli Zarządzanie dyskami. Podczas dołączania woluminów za poorednictwem protokołu iSCSI niektóre woluminy, które wykryto, przechodzą w stan offline. Gdy kanał iSCSI komunikuje się między maszyną wirtualną a usługą, wykrywa te woluminy i przełączają je do trybu online, ale nie są zainstalowane.

   ![Dysk nie został dołączony](./media/backup-azure-restore-files-from-vm/disk-not-attached-6.png)

Aby zidentyfikować i rozwiązać ten problem, wykonaj następujące czynności:

1. Przejdź do *Przystawka Zarządzanie dyskami**, uruchamiając polecenie **diskmgmt** w oknie cmd.
2. Sprawdź, czy są wyświetlane jakieś dodatkowe dyski. W poniższym przykładzie dysk 2 jest dodatkowym dyskiem.

   ![Management0 dysku](./media/backup-azure-restore-files-from-vm/disk-management-7.png)

3. Kliknij prawym przyciskiem myszy **wolumin** i wybierz polecenie **Zmień literę dysku i ścieżki**.

   ![Management1 dysku](./media/backup-azure-restore-files-from-vm/disk-management-8.png)

4. W oknie **Dodawanie litery dysku lub ścieżki** wybierz pozycję **Przypisz literę dysku** i przypisz dostępny dysk i kliknij przycisk **OK**. 

   ![Management2 dysku](./media/backup-azure-restore-files-from-vm/disk-management-9.png)

5. W Eksploratorze plików można wyświetlić dysk.

   ![Management3 dysku](./media/backup-azure-restore-files-from-vm/disk-management-10.png)

6. Nowe woluminy powinny zostać dołączone.  

   ![Dysk nie jest instalowany](./media/backup-azure-restore-files-from-vm/disk-not-mounting-11.png)

7. W Eksploratorze plików nowe woluminy będą widoczne po odinstalowaniu skryptu.

   ![Nie mounting1 dysku](./media/backup-azure-restore-files-from-vm/disk-not-mounting-12.png)

   ![Nie mounting2 dysku](./media/backup-azure-restore-files-from-vm/disk-not-mounting-13.png)

#### <a name="on-linux-vms"></a>Na maszynach wirtualnych z systemem Linux 

- Upewnij się, że masz odpowiednią [maszynę do uruchomienia skryptu](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#step-2-ensure-the-machine-meets-the-requirements-before-executing-the-script).
- Jeśli chroniona maszyna wirtualna z systemem Linux używa tablic LVM i/lub RAID, wykonaj kroki opisane w tym [artykule](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#lvmraid-arrays-for-linux-vms).

## <a name="cannot-copy-the-files-from-mounted-volumes"></a>Nie można skopiować plików z zainstalowanych woluminów

Kopia może zakończyć się niepowodzeniem z powodu błędu 0x80070780: nie można uzyskać dostępu do pliku przez system. W takim przypadku należy sprawdzić, czy na serwerze źródłowym jest włączona Deduplikacja dysku. Upewnij się, że na dyskach na serwerze przywracania jest włączona funkcja deduplikacji. Można pozostawić nieskonfigurowaną rolę deduplikacji, aby nie deduplikowania dysków na serwerze przywracania.
