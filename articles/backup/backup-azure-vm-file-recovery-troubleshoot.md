---
title: Rozwiązywanie problemów z odzyskiwaniem plików maszyny wirtualnej platformy Azure
description: Rozwiązywanie problemów podczas odzyskiwania plików i folderów z kopii zapasowej maszyny wirtualnej platformy Azure.
ms.topic: troubleshooting
ms.date: 07/12/2020
ms.openlocfilehash: aec69b91ad1dae5864e5e8fba61c53e6d15887f4
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/21/2021
ms.locfileid: "98624510"
---
# <a name="troubleshoot-issues-in-file-recovery-of-an-azure-vm-backup"></a>Rozwiązywanie problemów z odzyskiwaniem plików kopii zapasowej maszyny wirtualnej platformy Azure

W tym artykule opisano kroki rozwiązywania problemów, które mogą pomóc w rozwiązywaniu problemów związanych z odzyskiwaniem plików i folderów z kopii zapasowej maszyny wirtualnej platformy Azure.

## <a name="common-error-messages"></a>Typowe komunikaty o błędach

Ta sekcja zawiera kroki umożliwiające rozwiązywanie problemów z komunikatami o błędach, które mogą zostać wyświetlone.

### <a name="exception-caught-while-connecting-to-target"></a>"Przechwycono wyjątek podczas łączenia z celem"

**Możliwa przyczyna**: skrypt nie może uzyskać dostępu do punktu odzyskiwania.

**Zalecana akcja**: Aby rozwiązać ten problem, wykonaj kroki opisane w [skrypcie, ale połączenie nie powiodło się](#the-script-runs-but-the-connection-to-the-iscsi-target-failed).

### <a name="the-target-has-already-been-logged-in-via-an-iscsi-session"></a>"Element docelowy został już zalogowany za pośrednictwem sesji iSCSI"

**Możliwa przyczyna**: skrypt został już uruchomiony na tym samym komputerze, a dyski zostały podłączone.

**Zalecana akcja**: woluminy punktu odzyskiwania zostały już dołączone. Nie można ich zainstalować z tymi samymi literami dysków oryginalnej maszyny wirtualnej. Przeglądaj dostępne woluminy w Eksploratorze plików.

### <a name="this-script-is-invalid-because-the-disks-have-been-dismounted-via-portalexceeded-the-12-hr-limit-download-a-new-script-from-the-portal"></a>"Ten skrypt jest nieprawidłowy, ponieważ dyski zostały odinstalowane za pośrednictwem portalu/przekroczyły limit 12-godzinny. Pobierz nowy skrypt z portalu "

**Możliwa przyczyna**: dyski zostały odinstalowane z portalu lub Przekroczono limit 12 godzin.

**Zalecana akcja**: 12 godzin po pobraniu skryptu, jego wartość jest nieprawidłowa i nie można jej uruchomić. Przejdź do portalu, a następnie Pobierz nowy skrypt, aby kontynuować odzyskiwanie plików.

### <a name="iscsi_tcp-module-cant-be-loaded-or-iscsi_tcp_module-not-found"></a>nie można załadować modułu iscsi_tcp (lub), nie znaleziono iscsi_tcp_module

**Zalecana akcja**: Aby rozwiązać ten problem, wykonaj kroki opisane w temacie [pobieranie skryptów powiodło się, ale uruchomienie nie](#the-script-downloads-successfully-but-fails-to-run)powiodło się.

## <a name="common-problems"></a>Typowe problemy

Ta sekcja zawiera kroki rozwiązywania typowych problemów, które mogą wystąpić podczas pobierania i wykonywania skryptu w celu odzyskiwania plików.

### <a name="you-cant-download-the-script"></a>Nie można pobrać skryptu

1. Upewnij się, że masz [uprawnienia wymagane do pobrania skryptu](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#select-recovery-point-who-can-generate-script).
1. Sprawdź połączenie z docelowymi adresami IP platformy Azure. Uruchom jedno z następujących poleceń w wierszu polecenia z podwyższonym poziomem uprawnień:

   `nslookup download.microsoft.com`

    lub

    `ping download.microsoft.com`

### <a name="the-script-downloads-successfully-but-fails-to-run"></a>Skrypt zostanie pobrany pomyślnie, ale uruchomienie nie powiedzie się

Po uruchomieniu skryptu języka Python na potrzeby odzyskiwania na poziomie elementu (ILR) w SUSE Linux Enterprise Server 12 SP4 nie powiedzie się z powodu błędu "nie można załadować modułu iscsi_tcp" lub "nie znaleziono iscsi_tcp_module".

**Możliwa przyczyna**: moduł ILR używa **iscsi_tcp** , aby nawiązać połączenie TCP z usługą kopii zapasowej. W ramach programu SLES 12 SP4 wersja SUSE usunięta **iscsi_tcp** z pakietu Open-iSCSI, więc operacja ILR kończy się niepowodzeniem.

**Zalecana akcja**: wykonywanie skryptu odzyskiwania plików nie jest obsługiwane na maszynach wirtualnych SUSE 12 SP4. Spróbuj wykonać operację przywracania w starszej wersji SUSE 12 SP4.

### <a name="the-script-runs-but-the-connection-to-the-iscsi-target-failed"></a>Skrypt jest uruchamiany, ale połączenie z obiektem docelowym iSCSI nie powiodło się

Może zostać wyświetlony komunikat o błędzie "Przechwycono wyjątek podczas łączenia z miejscem docelowym".

1. Upewnij się, że komputer, na którym jest uruchomiony skrypt, spełnia [wymagania dotyczące dostępu](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#step-4-access-requirements-to-successfully-run-the-script).
1. Sprawdź połączenie z docelowymi adresami IP platformy Azure. Uruchom jedno z następujących poleceń w wierszu polecenia z podwyższonym poziomem uprawnień:

   `nslookup download.microsoft.com`

   lub

   `ping download.microsoft.com`
1. Upewnij się, że dostęp do portu wychodzącego iSCSI 3260.
1. Wyszukaj zaporę lub sieciowej grupy zabezpieczeń ruch do usługi Azure Target IP lub adresów URL usług odzyskiwania.
1. Upewnij się, że oprogramowanie antywirusowe nie blokuje wykonywania skryptu.

### <a name="youre-connected-to-the-recovery-point-but-the-disks-werent-attached"></a>Nawiązano połączenie z punktem odzyskiwania, ale dyski nie zostały dołączone

Rozwiąż ten problem, wykonując kroki dla danego systemu operacyjnego.

#### <a name="windows-file-recovery-fails-on-server-with-storage-pools"></a>Odzyskiwanie plików systemu Windows kończy się niepowodzeniem na serwerze z pulami magazynu

Po uruchomieniu skryptu po raz pierwszy w systemie Windows Server 2012 R2 i Windows Server 2016 (z pulami magazynu) Pula magazynów może być dołączona do maszyny wirtualnej w trybie tylko do odczytu.

>[!Tip]
> Upewnij się, że masz odpowiednią [maszynę do uruchomienia skryptu](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#step-2-ensure-the-machine-meets-the-requirements-before-executing-the-script).

Aby rozwiązać ten problem, należy ręcznie przypisać dostęp do odczytu i zapisu do puli magazynów i dołączyć dyski wirtualne:

1. Przejdź do **Menedżer serwera**  >    >    >  **Pule magazynów** usługi plików i magazynowania.

   ![Zrzut ekranu przedstawiający opcje pul magazynów.](./media/backup-azure-restore-files-from-vm/windows-storage-1.png)

1. W oknie **Pula magazynów** kliknij prawym przyciskiem myszy dostępną pulę magazynów, a następnie wybierz pozycję **Ustaw Read-Write dostęp**.

   ![Zrzut ekranu przedstawiający opcje dostępne po kliknięciu prawym przyciskiem myszy dla buforu magazynu.](./media/backup-azure-restore-files-from-vm/windows-storage-read-write-2.png)

1. Po przypisaniu puli magazynów dostęp do odczytu i zapisu kliknij prawym przyciskiem myszy w sekcji **dyski wirtualne** , a następnie wybierz pozycję **Dołącz dysk wirtualny**.

   ![Zrzut ekranu przedstawiający Opcje kliknij prawym przyciskiem myszy dla dysku wirtualnego.](./media/backup-azure-restore-files-from-vm/server-manager-virtual-disk-3.png)

#### <a name="linux-file-recovery-fails-to-auto-mount-because-the-disk-doesnt-contain-volumes"></a>Odzyskiwanie plików z systemu Linux kończy się niepowodzeniem, ponieważ dysk nie zawiera woluminów

Podczas odzyskiwania plików usługa Backup wykrywa woluminy i instaluje je ponownie. Jeśli jednak kopie zapasowe dysków mają surowe partycje, te dyski nie są instalowane w sposób nieinstalowany i nie będzie można zobaczyć dysku danych do odzyskania.

Aby rozwiązać ten problem, przejdź do obszaru [odzyskiwanie plików z kopii zapasowej maszyny wirtualnej platformy Azure](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#lvmraid-arrays-for-linux-vms).

#### <a name="linux-file-recovery-fails-because-the-os-couldnt-identify-the-file-system"></a>Odzyskiwanie plików w systemie Linux kończy się niepowodzeniem, ponieważ system operacyjny nie może zidentyfikować systemu plików

Po uruchomieniu skryptu odzyskiwania plików nie można dołączyć dysku z danymi. Zostanie wyświetlony komunikat "nie można zainstalować następujących partycji, ponieważ system operacyjny nie może zidentyfikować błędu systemu plików".

Aby rozwiązać ten problem, sprawdź, czy wolumin jest szyfrowany przy użyciu aplikacji innej firmy. Jeśli jest zaszyfrowana, dysk lub maszyna wirtualna nie będą wyświetlane w portalu jako zaszyfrowane.

1. Zaloguj się do maszyny wirtualnej z kopią zapasową i uruchom następujące polecenie:

   `lsblk -f`

   ![Zrzut ekranu przedstawiający wyniki polecenia, aby wyświetlić listę zablokowanych urządzeń.](./media/backup-azure-restore-files-from-vm/disk-without-volume-5.png)

1. Sprawdź system plików i szyfrowanie. Jeśli wolumin jest szyfrowany, odzyskiwanie plików nie jest obsługiwane. Dowiedz się więcej na temat [macierzy obsługi kopii zapasowych maszyny wirtualnej platformy Azure](https://docs.microsoft.com/azure/backup/backup-support-matrix-iaas#support-for-file-level-restore).

### <a name="disks-are-attached-but-the-volumes-arent-mounted"></a>Dyski są dołączone, ale woluminy nie są zainstalowane

Rozwiąż ten problem, wykonując kroki dla danego systemu operacyjnego.

#### <a name="windows"></a>Windows

Po uruchomieniu skryptu odzyskiwania plików dla systemu Windows zobaczysz komunikat "0 odzyskiwanych woluminów". Dyski zostaną jednak odnalezione w konsoli Zarządzanie dyskami.

**Możliwa przyczyna**: podczas dołączania woluminów przy użyciu protokołu iSCSI niektóre wykryte woluminy zostały przełączone w tryb offline. Gdy kanał iSCSI komunikuje się między maszyną wirtualną a usługą, wykrywa te woluminy i przenosi je w tryb online, ale nie są one zainstalowane.

   ![Zrzut ekranu przedstawiający 0 podłączonych woluminów odzyskiwania.](./media/backup-azure-restore-files-from-vm/disk-not-attached-6.png)

Aby zidentyfikować i rozwiązać ten problem, wykonaj następujące czynności:

>[!Tip]
>Upewnij się, że masz odpowiednią [maszynę do uruchomienia skryptu](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#step-2-ensure-the-machine-meets-the-requirements-before-executing-the-script).

1. W oknie **cmd** Uruchom polecenie **diskmgmt** , aby otworzyć **przystawkę Zarządzanie dyskami**.
1. Poszukaj dodatkowych dysków. W poniższym przykładzie **dysk 2** jest dodatkowym dyskiem.

   ![Zrzut ekranu okna Zarządzanie dyskami z dodatkowym dyskiem.](./media/backup-azure-restore-files-from-vm/disk-management-7.png)

1. Kliknij prawym przyciskiem myszy pozycję **Nowy wolumin**, a następnie wybierz polecenie **Zmień literę dysku i ścieżki**.

   ![Zrzut ekranu przedstawiający opcje dostępne po kliknięciu prawym przyciskiem myszy na dodatkowym dysku.](./media/backup-azure-restore-files-from-vm/disk-management-8.png)

1. W oknie **Zmień literę dysku lub ścieżkę** wybierz pozycję **Przypisz literę dysku**, przypisz dostępny dysk, a następnie wybierz przycisk **OK**.

   ![Zrzut ekranu przedstawiający okno Zmień literę dysku lub ścieżkę.](./media/backup-azure-restore-files-from-vm/disk-management-9.png)

1. Otwórz Eksploratora plików, aby wyświetlić wybrany dysk i eksplorować pliki.

#### <a name="linux"></a>Linux

>[!Tip]
>Upewnij się, że masz odpowiednią [maszynę do uruchomienia skryptu](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#step-2-ensure-the-machine-meets-the-requirements-before-executing-the-script).

Jeśli chroniona maszyna wirtualna z systemem Linux używa macierzy LVM lub RAID, wykonaj kroki opisane w temacie [odzyskiwanie plików z kopii zapasowej maszyny wirtualnej platformy Azure](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#lvmraid-arrays-for-linux-vms).

### <a name="you-cant-copy-the-files-from-mounted-volumes"></a>Nie można skopiować plików z zainstalowanych woluminów

Kopia może zakończyć się niepowodzeniem z powodu błędu "0x80070780: nie można uzyskać dostępu do pliku przez system". 

Sprawdź, czy na serwerze źródłowym jest włączona Deduplikacja dysku. W takim przypadku upewnij się, że na dysku serwera przywracania włączono również funkcję deduplikacji. Aby nie deduplikowania dysków na serwerze przywracania, można pozostawić nieskonfigurowanej deduplikację.

## <a name="next-steps"></a>Następne kroki

- [Odzyskiwanie plików i folderów z kopii zapasowej maszyny wirtualnej platformy Azure](backup-azure-restore-files-from-vm.md)
