---
title: Rozwiązywanie problemów z uruchamianiem maszyn wirtualnych z systemem Linux z powodu błędów systemu plików | Microsoft Docs
description: Dowiedz się, jak rozwiązywać problemy z uruchamianiem maszyn wirtualnych z systemem Linux z powodu błędów systemu plików
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
ms.openlocfilehash: 8f12b88a0ddbc6ae31f40ab31b0126e4fd66b1a5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91325950"
---
# <a name="troubleshoot-linux-vm-starting-issues-due-to-file-system-errors"></a>Rozwiązywanie problemów z uruchamianiem maszyn wirtualnych z systemem Linux z powodu błędów systemu plików

Nie można nawiązać połączenia z maszyną wirtualną platformy Azure z systemem Linux przy użyciu Secure Shell (SSH). Po uruchomieniu funkcji diagnostyki rozruchu w [Azure Portal](https://portal.azure.com/)są wyświetlane wpisy dziennika podobne do poniższych.

## <a name="examples"></a>Przykłady

Poniżej przedstawiono przykłady możliwych błędów.

### <a name="example-1"></a>Przykład 1 

```
Checking all file systems.
[/sbin/fsck.ext4 (1) — /] fsck.ext4 -a /dev/sda1
/dev/sda1 contains a file system with errors, check forced.
/dev/sda1: Inodes that were part of a corrupted orphan linked list found.
/dev/sda1: UNEXPECTED INCONSISTENCY; RUN fsck MANUALLY
```

### <a name="example-2"></a>Przykład 2

```
EXT4-fs (sda1): INFO: recovery required on readonly filesystem
EXT4-fs (sda1): write access will be enabled during recovery
EXT4-fs warning (device sda1): ext4_clear_journal_err:4531: Filesystem error recorded from previous mount: IO failure
EXT4-fs warning (device sda1): ext4_clear_journal_err:4532: Making fs in need of filesystem check.
```

### <a name="example-3"></a>Przykład 3

```
[  14.252404] EXT4-fs (sda1): Couldn’t remount RDWR because of unprocessed orphan inode list.  Please unmount/remount instead
An error occurred while mounting /.
```

### <a name="example-4"></a>Przykład 4 

Ten przykład jest spowodowany przez czystą fsck. W takim przypadku dostępne są również dodatkowe dyski danych dołączone do maszyny wirtualnej (/dev/sdc1 i/dev/SDE1).

```
Checking all file systems. 
[/sbin/fsck.ext4 (1) — /] fsck.ext4 -a /dev/sda1
/dev/sda1: clean, 65405/1905008 files, 732749/7608064 blocks
[/sbin/fsck.ext4 (1) — /tmp] fsck.ext4 -a /dev/sdc1
[/sbin/fsck.ext4 (2) — /backup] fsck.ext4 -a /dev/sde1
/dev/sdc1: clean, 12/1048576 files, 109842/4192957 blocks
/dev/sde1 : clean, 51/67043328 files, 4259482/268173037 blocks
```

Ten problem może wystąpić, jeśli system plików nie został poprawnie zamknięty lub problemy związane z magazynem. Problemy obejmują błędy sprzętu lub oprogramowania, problemy dotyczące sterowników lub programów, Błędy zapisu itp. Zawsze ważne jest, aby utworzyć kopię zapasową najważniejszych danych. Narzędzia, które opisano w tym artykule, mogą ułatwić odzyskanie systemów plików, ale może to spowodować utratę danych.

System Linux ma kilka dostępnych sprawdzań systemu plików. Najczęstsze dla dystrybucji na platformie Azure to: [FSCK](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/6/html/storage_administration_guide/fsck-fs-specific), [E2FSCK](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/storage_administration_guide/fsck-fs-specific)i [Xfs_repair](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/storage_administration_guide/xfsrepair).

## <a name="resolution"></a>Rozwiązanie

Aby rozwiązać ten problem, wykonaj rozruch maszyny wirtualnej w trybie awaryjnym przy użyciu [konsoli szeregowej](./serial-console-linux.md) i napraw system plików za pomocą tego narzędzia. Jeśli konsola szeregowa nie jest włączona na maszynie wirtualnej lub nie działa, zapoznaj się z sekcją [napraw maszynę wirtualną](#repair-the-vm-offline) w tym artykule.

## <a name="use-the-serial-console"></a>Korzystanie z konsoli szeregowej

1. Nawiąż połączenie z konsolą szeregową.

   > [!Note]
   > Aby uzyskać więcej informacji o korzystaniu z konsoli szeregowej dla systemu Linux, zobacz:
   > * [Używanie konsoli szeregowej do uzyskiwania dostępu do GRUB i trybu jednego użytkownika](serial-console-grub-single-user-mode.md)
   > * [Korzystanie z konsoli szeregowej dla wywołań SysRq i NMI](./serial-console-nmi-sysrq.md)

2. Wybierz przycisk ikona potęgi, a następnie wybierz pozycję Uruchom ponownie maszynę wirtualną. (Jeśli konsola szeregowa nie jest włączona lub nie jest prawidłowo połączona, nie zobaczysz przycisku).

   ![OBRAZ](./media/linux-recovery-cannot-ssh-to-linux-vm-due-to-file-system-errors-fsck/restart-vm.png)

3. Wykonaj rozruch maszyny wirtualnej w trybie awaryjnym.

4. Wprowadź hasło konta głównego, aby zalogować się do trybu awaryjnego.

5. Użyj xfs_repair z opcją-n, aby wykryć błędy w systemie plików. W poniższym przykładzie przyjęto założenie, że partycja systemowa jest/dev/sda1. Zastąp ją odpowiednią wartością dla maszyny wirtualnej:

   ```
   xfs_repair -n /dev/sda1
   ```

6. Uruchom następujące polecenie, aby naprawić system plików:

   ```
   xfs_repair /dev/sda1
   ```

7. Jeśli zostanie wyświetlony komunikat o błędzie "błąd: system plików zawiera cenne zmiany metadanych w dzienniku, które muszą być odtwarzane", Utwórz katalog tymczasowy i zainstaluj system plików:

   ```
   mkdir /temp
   mount /dev/sda1 /temp
   ```

8. Jeśli instalacja dysku nie powiedzie się, uruchom polecenie xfs_repair z opcją-L (Wymuś zero):

   ```
   xfs_repair /dev/sda1 -L
   ```

9. Następnie spróbuj zainstalować system plików. Po pomyślnym zainstalowaniu dysku zostaną wyświetlone następujące dane wyjściowe:
 
   ```
   XFS (sda1): Mounting V1 Filesystem
   XFS (sda1): Ending clean mount
   ```

10. Uruchom ponownie maszynę wirtualną, a następnie sprawdź, czy problem został rozwiązany.

    ```
    Reboot -f
    ```

## <a name="repair-the-vm-offline"></a>Naprawianie maszyny wirtualnej w trybie offline

1. Dołącz dysk systemowy maszyny wirtualnej jako dysk danych do maszyny wirtualnej odzyskiwania (dowolna działająca maszyna wirtualna z systemem Linux). W tym celu można użyć [poleceń interfejsu wiersza polecenia](./troubleshoot-recovery-disks-linux.md) lub można zautomatyzować Konfigurowanie maszyny wirtualnej odzyskiwania przy użyciu [poleceń naprawy maszyny wirtualnej](repair-linux-vm-using-azure-virtual-machine-repair-commands.md).

2. Znajdź etykietę stacji dysku systemowego, który został podłączony. W takim przypadku Załóżmy, że etykieta dołączonego dysku systemowego to/dev/sdc1. Zastąp go odpowiednią wartością dla maszyny wirtualnej.

3. Użyj xfs_repair z opcją-n, aby wykryć błędy w systemie plików.

   ```
   xfs_repair -n /dev/sdc1
   ```

4. Uruchom następujące polecenie, aby naprawić system plików:

   ```
   xfs_repair /dev/sdc1
   ```

5. Jeśli zostanie wyświetlony komunikat o błędzie "błąd: system plików zawiera cenne zmiany metadanych w dzienniku, które muszą być odtwarzane", Utwórz katalog tymczasowy i zainstaluj system plików:

   ```
   mkdir /temp

   mount /dev/sdc1 /temp
   ```

   Jeśli instalacja dysku nie powiedzie się, uruchom polecenie xfs_repair z opcją-L (Wymuś zero):

   ```
   xfs_repair /dev/sdc1 -L
   ```

6. Następnie spróbuj zainstalować system plików. Po pomyślnym zainstalowaniu dysku zostaną wyświetlone następujące dane wyjściowe:

   ```
   XFS (sdc1): Mounting V1 Filesystem
   
   XFS (sdc1): Ending clean mount
   ```

7. Odinstaluj i odłącz oryginalny wirtualny dysk twardy, a następnie utwórz maszynę wirtualną na podstawie oryginalnego dysku systemowego. W tym celu można użyć [poleceń interfejsu wiersza](troubleshoot-recovery-disks-linux.md) polecenia lub [poleceń naprawy maszyny wirtualnej](repair-linux-vm-using-azure-virtual-machine-repair-commands.md) , jeśli zostały one użyte do utworzenia maszyny wirtualnej odzyskiwania.

8. Sprawdź, czy problem został rozwiązany.

## <a name="next-steps"></a>Następne kroki

* [Rozwiązywanie problemów z maszyną wirtualną z systemem Linux przez dołączenie dysku systemu operacyjnego do maszyny wirtualnej odzyskiwania przy użyciu interfejsu wiersza 2,0 polecenia platformy Azure](./troubleshoot-recovery-disks-linux.md)
* [Dołączanie dysku danych do maszyny wirtualnej z systemem Linux przy użyciu portalu](../linux/attach-disk-portal.md)
