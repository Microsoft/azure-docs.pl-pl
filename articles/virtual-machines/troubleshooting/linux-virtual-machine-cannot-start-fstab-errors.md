---
title: Rozwiązywanie problemów z uruchamianiem maszyny wirtualnej systemu Linux z powodu błędów fstab | Microsoft Docs
description: Wyjaśnia, dlaczego nie można uruchomić maszyny wirtualnej systemu Linux i jak rozwiązać ten problem.
services: virtual-machines-linux
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: ''
ms.service: virtual-machines-linux
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.date: 10/09/2019
ms.author: v-six
ms.openlocfilehash: fd49993e6825c47bbae8f034715c03191e06ab2d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89441667"
---
# <a name="troubleshoot-linux-vm-starting-issues-due-to-fstab-errors"></a>Rozwiązywanie problemów z uruchamianiem maszyny wirtualnej systemu Linux z powodu błędów fstab

Nie można nawiązać połączenia z maszyną wirtualną platformy Azure z systemem Linux przy użyciu połączenia Secure Shell (SSH). Po uruchomieniu funkcji [diagnostyki rozruchu](./boot-diagnostics.md) na [Azure Portal](https://portal.azure.com/)są wyświetlane wpisy dziennika podobne do następujących:

## <a name="examples"></a>Przykłady

Poniżej przedstawiono przykłady możliwych błędów.

### <a name="example-1-a-disk-is-mounted-by-the-scsi-id-instead-of-the-universally-unique-identifier-uuid"></a>Przykład 1: dysk jest instalowany przy użyciu identyfikatora SCSI zamiast uniwersalnego identyfikatora unikatowego (UUID)

```
[K[[1;31m TIME [0m] Timed out waiting for device dev-incorrect.device.
[[1;33mDEPEND[0m] Dependency failed for /data.
[[1;33mDEPEND[0m] Dependency failed for Local File Systems.
…
Welcome to emergency mode! After logging in, type "journalctl -xb" to viewsystem logs, "systemctl reboot" to reboot, "systemctl default" to try again to boot into default mode.
Give root password for maintenance
(or type Control-D to continue)
```

### <a name="example-2-an-unattached-device-is-missing-on-centos"></a>Przykład 2: brak dołączonego urządzenia w CentOS

```
Checking file systems…
fsck from util-linux 2.19.1
Checking all file systems.
/dev/sdc1: nonexistent device ("nofail" fstab option may be used to skip this device)
/dev/sdd1: nonexistent device ("nofail" fstab option may be used to skip this device)
/dev/sde1: nonexistent device ("nofail" fstab option may be used to skip this device)

[/sbin/fsck.ext3 (1) — /CODE] sck.ext3 -a /dev/sdc1
fsck.ext3: No such file or directory while trying to open /dev/sdc1

/dev/sdc1:
The superblock could not be read or does not describe a correct ext2
filesystem. If the device is valid and it really contains an ext2
filesystem (and not swap or ufs or something else), then the superblock
is corrupt, and you might try running e2fsck with an alternate superblock:

e2fsck -b 8193 <device>

[/sbin/fsck.xfs (1) — /GLUSTERDISK] fsck.xfs -a /dev/sdd1
/sbin/fsck.xfs: /dev/sdd1 does not exist
[/sbin/fsck.ext3 (1) — /DATATEMP] fsck.ext3 -a /dev/sde1 fsck.ext3: No such file or directory while trying to open /dev/sde1
```

### <a name="example-3-a-vm-cannot-start-because-of-an-fstab-misconfiguration-or-because-the-disk-is-no-longer-attached"></a>Przykład 3: nie można uruchomić maszyny wirtualnej z powodu nieprawidłowej konfiguracji fstab lub ponieważ dysk nie jest już dołączony

```
The disk drive for /var/lib/mysql is not ready yet or not present.
Continue to wait, or Press S to skip mounting or M for manual recovery
```

### <a name="example-4-a-serial-log-entry-shows-an-incorrect-uuid"></a>Przykład 4: wpis w dzienniku seryjnym pokazuje nieprawidłowy identyfikator UUID

```
Checking filesystems
Checking all file systems.
[/sbin/fsck.ext4 (1) — /] fsck.ext4 -a /dev/sda1
/dev/sda1: clean, 70442/1905008 files, 800094/7608064 blocks
[/sbin/fsck.ext4 (1) — /datadrive] fsck.ext4 -a UUID="<UUID>"
fsck.ext4: Unable to resolve UUID="<UUID>"
[FAILED

*** An error occurred during the file system check.
*** Dropping you to a shell; the system will reboot
*** when you leave the shell.
*** Warning — SELinux is active
*** Disabling security enforcement for system recovery.
*** Run 'setenforce 1' to reenable.
type=1404 audit(1428047455.949:4): enforcing=0 old_enforcing=1 auid=<AUID> ses=4294967295
Give root password for maintenance
(or type Control-D to continue)
```

Ten problem może wystąpić, Jeśli składnia tabeli systemów plików (fstab) jest niepoprawna lub jeśli wymagany dysk danych mapowany do wpisu w pliku "/etc/fstab" nie jest dołączony do maszyny wirtualnej.

## <a name="resolution"></a>Rozwiązanie

Aby rozwiązać ten problem, uruchom maszynę wirtualną w trybie awaryjnym przy użyciu konsoli szeregowej dla platformy Azure Virtual Machines. Następnie napraw system plików za pomocą narzędzia. Jeśli konsola szeregowa nie jest włączona na maszynie wirtualnej, przejdź do sekcji [Naprawa maszyny wirtualnej w trybie offline](#repair-the-vm-offline) .

## <a name="use-the-serial-console"></a>Korzystanie z konsoli szeregowej

### <a name="using-single-user-mode"></a>Korzystanie z trybu pojedynczego użytkownika

1. Nawiąż połączenie z [konsolą szeregową](./serial-console-linux.md).
2. Używanie konsoli szeregowej do przełączanie trybu jednego [użytkownika](serial-console-grub-single-user-mode.md) w tryb jednego użytkownika
3. Po rozruchu maszyny wirtualnej w trybie jednego użytkownika. Użyj swojego ulubionego edytora tekstu, aby otworzyć plik fstab. 

   ```
   # nano /etc/fstab
   ```

4. Przejrzyj wymienione systemy plików. Każdy wiersz w pliku fstab wskazuje system plików, który jest instalowany podczas uruchamiania maszyny wirtualnej. Aby uzyskać więcej informacji na temat składni pliku fstab, uruchom polecenie Man fstab. Aby rozwiązać problem z niepowodzeniem uruchamiania, Przejrzyj każdy wiersz, aby upewnić się, że jest on poprawny w strukturze i zawartości.

   > [!Note]
   > * Pola w każdym wierszu są rozdzielone znakami tabulacji lub spacjami. Puste wiersze są ignorowane. Wiersze, które mają znak numeru (#), jako pierwszy znak to komentarze. Wiersze z komentarzem mogą pozostawać w pliku fstab, ale nie będą przetwarzane. Zaleca się, aby nie usuwać wierszy z fstab wierszy, które nie są już używane.
   > * Aby maszyna wirtualna mogła odzyskiwać i uruchamiać, partycje systemu plików powinny być jedynymi wymaganymi partycjami. Na maszynie wirtualnej mogą wystąpić błędy aplikacji dotyczące dodatkowych partycji z komentarzami. Jednak maszyna wirtualna powinna zostać uruchomiona bez dodatkowych partycji. Możesz później usunąć komentarz z komentarzy do wszystkich wierszy z komentarzem.
   > * Zalecamy zainstalowanie dysków danych na maszynach wirtualnych platformy Azure przy użyciu identyfikatora UUID partycji systemu plików. Na przykład uruchom następujące polecenie: ``/dev/sdc1: LABEL="cloudimg-rootfs" UUID="<UUID>" TYPE="ext4" PARTUUID="<PartUUID>"``
   > * Aby określić identyfikator UUID systemu plików, uruchom polecenie blkid. Aby uzyskać więcej informacji na temat składni, uruchom polecenie Man blkid.
   > * Opcja nofail pomaga upewnić się, że maszyna wirtualna jest uruchomiona, nawet jeśli system plików jest uszkodzony lub system plików nie istnieje podczas uruchamiania. Zalecamy użycie opcji nofail w pliku fstab, aby umożliwić uruchamianie kontynuuje po wystąpieniu błędów w partycjach, które nie są wymagane do uruchomienia maszyny wirtualnej.

5. Zmień lub Dodaj komentarz do nieprawidłowych lub niepotrzebnych wierszy w pliku fstab, aby umożliwić prawidłowe uruchomienie maszyny wirtualnej.

6. Zapisz zmiany w pliku fstab.

7. Uruchom ponownie maszynę wirtualną przy użyciu poniższego polecenia.
   
   ```
   # reboot -f
   ```
> [!Note]
   > Można również użyć polecenia "Ctrl + x", które spowoduje również ponowne uruchomienie maszyny wirtualnej.


8. Jeśli komentarz lub naprawa wpisów zakończyło się pomyślnie, system powinien uzyskać monit bash w portalu. Sprawdź, czy możesz nawiązać połączenie z maszyną wirtualną.

### <a name="using-root-password"></a>Używanie hasła głównego

1. Nawiąż połączenie z [konsolą szeregową](./serial-console-linux.md).
2. Zaloguj się do systemu przy użyciu lokalnego użytkownika i hasła.

   > [!Note]
   > Nie można użyć klucza SSH do logowania się do systemu w konsoli szeregowej.

3. Poszukaj błędu wskazującego, że dysk nie został zainstalowany. W poniższym przykładzie System próbował dołączyć dysk, który nie był już obecny:

   ```
   [DEPEND] Dependency failed for /datadisk1.
   [DEPEND] Dependency failed for Local File Systems.
   [DEPEND] Dependency failed for Relabel all filesystems, if necessary.
   [DEPEND] Dependency failed for Migrate local... structure to the new structure.
   Welcome to emergency mode! After logging in, type "journalctl -xb" to view
   system logs, "systemctl reboot" to reboot, "systemctl default" or ^D to try again to boot into default mode.
   Give root password for maintenance
   (or type Control-D to continue):
   ```

4. Połącz się z maszyną wirtualną przy użyciu hasła głównego (maszyn wirtualnych opartych na Red Hat).

5. Użyj swojego ulubionego edytora tekstu, aby otworzyć plik fstab. Po zainstalowaniu dysku Uruchom następujące polecenie dla systemu nano:

   ```
   $ nano /mnt/troubleshootingdisk/etc/fstab
   ```

6. Przejrzyj wymienione systemy plików. Każdy wiersz w pliku fstab wskazuje system plików, który jest instalowany podczas uruchamiania maszyny wirtualnej. Aby uzyskać więcej informacji na temat składni pliku fstab, uruchom polecenie Man fstab. Aby rozwiązać problem z niepowodzeniem uruchamiania, Przejrzyj każdy wiersz, aby upewnić się, że jest on poprawny w strukturze i zawartości.

   > [!Note]
   > * Pola w każdym wierszu są rozdzielone znakami tabulacji lub spacjami. Puste wiersze są ignorowane. Wiersze, które mają znak numeru (#), jako pierwszy znak to komentarze. Wiersze z komentarzem mogą pozostawać w pliku fstab, ale nie będą przetwarzane. Zaleca się, aby nie usuwać wierszy z fstab wierszy, które nie są już używane.
   > * Aby maszyna wirtualna mogła odzyskiwać i uruchamiać, partycje systemu plików powinny być jedynymi wymaganymi partycjami. Na maszynie wirtualnej mogą wystąpić błędy aplikacji dotyczące dodatkowych partycji z komentarzami. Jednak maszyna wirtualna powinna zostać uruchomiona bez dodatkowych partycji. Możesz później usunąć komentarz z komentarzy do wszystkich wierszy z komentarzem.
   > * Zalecamy zainstalowanie dysków danych na maszynach wirtualnych platformy Azure przy użyciu identyfikatora UUID partycji systemu plików. Na przykład uruchom następujące polecenie: ``/dev/sdc1: LABEL="cloudimg-rootfs" UUID="<UUID>" TYPE="ext4" PARTUUID="<PartUUID>"``
   > * Aby określić identyfikator UUID systemu plików, uruchom polecenie blkid. Aby uzyskać więcej informacji na temat składni, uruchom polecenie Man blkid.
   > * Opcja nofail pomaga upewnić się, że maszyna wirtualna jest uruchomiona, nawet jeśli system plików jest uszkodzony lub system plików nie istnieje podczas uruchamiania. Zalecamy użycie opcji nofail w pliku fstab, aby umożliwić uruchamianie kontynuuje po wystąpieniu błędów w partycjach, które nie są wymagane do uruchomienia maszyny wirtualnej.

7. Zmień lub Dodaj komentarz do nieprawidłowych lub niepotrzebnych wierszy w pliku fstab, aby umożliwić prawidłowe uruchomienie maszyny wirtualnej.

8. Zapisz zmiany w pliku fstab.

9. Uruchom ponownie maszynę wirtualną.

10. Jeśli komentarz lub naprawa wpisów zakończyło się pomyślnie, system powinien uzyskać monit bash w portalu. Sprawdź, czy możesz nawiązać połączenie z maszyną wirtualną.

11. Sprawdź punkty instalacji, gdy przetestujesz wszystkie zmiany fstab, uruchamiając polecenie Mount – a. Jeśli nie ma żadnych błędów, punkty instalacji powinny być dobre.

## <a name="repair-the-vm-offline"></a>Naprawianie maszyny wirtualnej w trybie offline

1. Dołącz dysk systemowy maszyny wirtualnej jako dysk danych do maszyny wirtualnej odzyskiwania (dowolna działająca maszyna wirtualna z systemem Linux). W tym celu można użyć [poleceń interfejsu wiersza polecenia](./troubleshoot-recovery-disks-linux.md) lub można zautomatyzować Konfigurowanie maszyny wirtualnej odzyskiwania przy użyciu [poleceń naprawy maszyny wirtualnej](repair-linux-vm-using-azure-virtual-machine-repair-commands.md).

2. Po zainstalowaniu dysku systemowego jako dysku danych na maszynie wirtualnej odzyskiwania należy wykonać kopię zapasową pliku fstab przed wprowadzeniem zmian, a następnie wykonać kolejne kroki, aby poprawić plik fstab.

3. Poszukaj błędu wskazującego, że dysk nie został zainstalowany. W poniższym przykładzie System próbował dołączyć dysk, który nie był już obecny:

   ```output
   [DEPEND] Dependency failed for /datadisk1.
   [DEPEND] Dependency failed for Local File Systems.
   [DEPEND] Dependency failed for Relabel all filesystems, if necessary.
   [DEPEND] Dependency failed for Migrate local... structure to the new structure.
   Welcome to emergency mode! After logging in, type "journalctl -xb" to view system logs, "systemctl reboot" to reboot, "systemctl default" or ^D to try again to boot into default mode.
   Give root password for maintenance (or type Control-D to continue):
   ```

4. Połącz się z maszyną wirtualną przy użyciu hasła głównego (maszyn wirtualnych opartych na Red Hat).

5. Użyj swojego ulubionego edytora tekstu, aby otworzyć plik fstab. Po zainstalowaniu dysku Uruchom następujące polecenie dla systemu nano. Upewnij się, że pracujesz nad plikiem fstab, który znajduje się na zainstalowanym dysku, a nie w pliku fstab, który znajduje się na maszynie ratowniczej.

   ```
   $ nano /mnt/troubleshootingdisk/etc/fstab
   ```

6. Przejrzyj wymienione systemy plików. Każdy wiersz w pliku fstab wskazuje system plików, który jest instalowany podczas uruchamiania maszyny wirtualnej. Aby uzyskać więcej informacji na temat składni pliku fstab, uruchom polecenie Man fstab. Aby rozwiązać problem z niepowodzeniem uruchamiania, Przejrzyj każdy wiersz, aby upewnić się, że jest on poprawny w strukturze i zawartości.

   > [!Note]
   > * Pola w każdym wierszu są rozdzielone znakami tabulacji lub spacjami. Puste wiersze są ignorowane. Wiersze, które mają znak numeru (#), jako pierwszy znak to komentarze. Wiersze z komentarzem mogą pozostawać w pliku fstab, ale nie będą przetwarzane. Zaleca się, aby nie usuwać wierszy z fstab wierszy, które nie są już używane.
   > * Aby maszyna wirtualna mogła odzyskiwać i uruchamiać, partycje systemu plików powinny być jedynymi wymaganymi partycjami. Na maszynie wirtualnej mogą wystąpić błędy aplikacji dotyczące dodatkowych partycji z komentarzami. Jednak maszyna wirtualna powinna zostać uruchomiona bez dodatkowych partycji. Możesz później usunąć komentarz z komentarzy do wszystkich wierszy z komentarzem.
   > * Zalecamy zainstalowanie dysków danych na maszynach wirtualnych platformy Azure przy użyciu identyfikatora UUID partycji systemu plików. Na przykład uruchom następujące polecenie: ``/dev/sdc1: LABEL="cloudimg-rootfs" UUID="<UUID>" TYPE="ext4" PARTUUID="<PartUUID>"``
   > * Aby określić identyfikator UUID systemu plików, uruchom polecenie blkid. Aby uzyskać więcej informacji na temat składni, uruchom polecenie Man blkid. Należy zauważyć, że dysk, który ma zostać odzyskany, jest teraz instalowany na nowej maszynie wirtualnej. Mimo że identyfikatory UUID powinny być spójne, identyfikator partycji urządzeń (na przykład "/dev/sda1") różnią się w tej maszynie wirtualnej. Partycje systemu plików oryginalnego zakończonego niepowodzeniem maszyny wirtualnej, które znajdują się na dysku VHD niesystemowym, nie są dostępne dla maszyny wirtualnej odzyskiwania [przy użyciu poleceń interfejsu wiersza polecenia](./troubleshoot-recovery-disks-linux.md).
   > * Opcja nofail pomaga upewnić się, że maszyna wirtualna jest uruchomiona, nawet jeśli system plików jest uszkodzony lub system plików nie istnieje podczas uruchamiania. Zalecamy użycie opcji nofail w pliku fstab, aby umożliwić uruchamianie kontynuuje po wystąpieniu błędów w partycjach, które nie są wymagane do uruchomienia maszyny wirtualnej.

7. Zmień lub Dodaj komentarz do nieprawidłowych lub niepotrzebnych wierszy w pliku fstab, aby umożliwić prawidłowe uruchomienie maszyny wirtualnej.

8. Zapisz zmiany w pliku fstab.

9. Uruchom ponownie maszynę wirtualną lub Odbuduj oryginalną MASZYNę wirtualną.

10. Jeśli komentarz lub naprawa wpisów zakończyło się pomyślnie, system powinien uzyskać monit bash w portalu. Sprawdź, czy możesz nawiązać połączenie z maszyną wirtualną.

11. Sprawdź punkty instalacji, gdy przetestujesz wszystkie zmiany fstab, uruchamiając polecenie Mount – a. Jeśli nie ma żadnych błędów, punkty instalacji powinny być dobre.

12. Odinstaluj i odłącz oryginalny wirtualny dysk twardy, a następnie utwórz maszynę wirtualną na podstawie oryginalnego dysku systemowego. W tym celu można użyć [poleceń interfejsu wiersza](troubleshoot-recovery-disks-linux.md) polecenia lub [poleceń naprawy maszyny wirtualnej](repair-linux-vm-using-azure-virtual-machine-repair-commands.md) , jeśli zostały one użyte do utworzenia maszyny wirtualnej odzyskiwania.

13. Po ponownym utworzeniu maszyny wirtualnej i nawiązaniu z nią połączenia za pośrednictwem protokołu SSH wykonaj następujące czynności:
    * Przejrzyj wszystkie wiersze fstab, które zostały zmienione lub oznaczone jako komentarze podczas odzyskiwania.
    * Upewnij się, że używasz identyfikatora UUID i odpowiednio opcję nofail.
    * Przetestuj wszystkie zmiany fstab przed ponownym uruchomieniem maszyny wirtualnej. Aby to zrobić, użyj następującego polecenia: ``$ sudo mount -a``
    * Utwórz dodatkową kopię poprawionego pliku fstab do użycia w przyszłych scenariuszach odzyskiwania.

## <a name="next-steps"></a>Następne kroki

* [Rozwiązywanie problemów z maszyną wirtualną z systemem Linux przez dołączenie dysku systemu operacyjnego do maszyny wirtualnej odzyskiwania przy użyciu interfejsu wiersza 2,0 polecenia platformy Azure](./troubleshoot-recovery-disks-linux.md)
* [Rozwiązywanie problemów z maszyną wirtualną z systemem Linux przez dołączenie dysku systemu operacyjnego do maszyny wirtualnej odzyskiwania przy użyciu Azure Portal](./troubleshoot-recovery-disks-portal-linux.md)
