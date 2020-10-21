---
title: Środowisko chroot na maszynie wirtualnej z systemem Linux.
description: W tym artykule opisano sposób rozwiązywania problemów ze środowiskiem chroot na maszynie wirtualnej w systemie Linux.
mservices: virtual-machines-linux
documentationcenter: ''
author: kailashmsft
manager: dcscontentpm
editor: ''
tags: ''
ms.service: virtual-machines-linux
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.date: 05/05/2020
ms.author: kaib
ms.openlocfilehash: 5d803acc7f2287d0b88791d85fa876f89e4a0955
ms.sourcegitcommit: 03713bf705301e7f567010714beb236e7c8cee6f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/21/2020
ms.locfileid: "92332189"
---
# <a name="chroot-environment-in-a-linux-rescue-vm"></a>Środowisko chroot na maszynie wirtualnej z systemem Linux

W tym artykule opisano sposób rozwiązywania problemów ze środowiskiem chroot na maszynie wirtualnej w systemie Linux.

## <a name="ubuntu-16x--ubuntu-18x"></a>Ubuntu 16. x && Ubuntu 18. x

1. Zatrzymywanie lub cofanie alokacji odnośnej maszyny wirtualnej.
1. Utwórz sprzętową maszynę wirtualną w tej samej wersji systemu operacyjnego, w tej samej grupie zasobów (RSG) i lokalizacji przy użyciu dysku zarządzanego.
1. Użyj Azure Portal, aby utworzyć migawkę dysku systemu operacyjnego, którego dotyczy ta maszyna wirtualna.
1. Utwórz dysk poza migawką dysku systemu operacyjnego i dołącz go do ratowniczej maszyny wirtualnej.
1. Po utworzeniu dysku Rozwiąż problemy ze środowiskiem chroot na maszynie wirtualnej.

   1. Uzyskaj dostęp do maszyny wirtualnej jako użytkownik główny przy użyciu następującego polecenia:

      `#sudo su -`

   1. Znajdź dysk przy użyciu `dmesg` (Metoda używana do odnajdywania nowego dysku może się różnić). Poniższy przykład używa **dmesg** do filtrowania na dyskach **SCSI** :

      `dmesg | grep SCSI`

      Dane wyjściowe będą podobne do poniższego przykładu. W tym przykładzie chcemy, aby **SDC** dysk:

      ```
      [    0.294784] SCSI subsystem initialized
      [    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
      [    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
      [    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
      [ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk
      ```

   1. Użyj następujących poleceń, aby uzyskać dostęp do środowiska chroot:

      ```
      #mkdir /rescue
      #mount /dev/sdc1 /rescue
      #mount /dev/sdc15 /rescue/boot/efi
      #cd /rescue

      #mount -t proc proc proc
      #mount -t sysfs sys sys/
      #mount -o bind /dev dev/
      #mount -o bind /dev/pts dev/pts/
      #mount -o bind /run run/
      #chroot /rescue
      ```

   1. Rozwiązywanie problemów ze środowiskiem chroot.

   1. Użyj następujących poleceń, aby zakończyć środowisko chroot:

      ```
      #exit

      #umount /rescue/proc/
      #umount /rescue/sys/
      #umount /rescue/dev/pts
      #umount /rescue/dev/
      #umount /rescue/run
      #cd /
      #umount /rescue/boot/efi
      #umount /rescue
      ```

      > [!NOTE]
      > Jeśli zostanie wyświetlony komunikat o błędzie `unable to unmount /rescue` , Dodaj-l opcji do polecenia umount.
      >
      > Przykład: `umount -l /rescue`

1. Odłącz dysk od ratowniczej maszyny wirtualnej i przeprowadź wymianę dysku z oryginalną maszyną wirtualną.
1. Uruchom oryginalną maszynę wirtualną i sprawdź jej łączność.

## <a name="rhelcentosoracle-6x--oracle-8x--rhelcentos-7x-with-raw-partitions"></a>RHEL/CentOS/Oracle 6. x && Oracle 8. x && RHEL/CentOS 7. x z nieprzetworzonymi partycjami

1. Zatrzymywanie lub cofanie alokacji odnośnej maszyny wirtualnej.
1. Utwórz sprzętową maszynę wirtualną w tej samej wersji systemu operacyjnego, w tej samej grupie zasobów (RSG) i lokalizacji przy użyciu dysku zarządzanego.
1. Użyj Azure Portal, aby utworzyć migawkę dysku systemu operacyjnego, którego dotyczy ta maszyna wirtualna.
1. Utwórz dysk poza migawką dysku systemu operacyjnego i dołącz go do ratowniczej maszyny wirtualnej.
1. Po utworzeniu dysku Rozwiąż problemy ze środowiskiem chroot na maszynie wirtualnej.

   1. Uzyskaj dostęp do maszyny wirtualnej jako użytkownik główny przy użyciu następującego polecenia:

      `#sudo su -`

   1. Znajdź dysk przy użyciu `dmesg` (Metoda używana do odnajdywania nowego dysku może się różnić). Poniższy przykład używa **dmesg** do filtrowania na dyskach **SCSI** :

      `dmesg | grep SCSI`

      Dane wyjściowe będą podobne do poniższego przykładu. W tym przykładzie chcemy, aby **SDC** dysk:

      ```
      [    0.294784] SCSI subsystem initialized
      [    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
      [    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
      [    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
      [ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk
      ```

   1. Użyj następujących poleceń, aby uzyskać dostęp do środowiska chroot:

      ```
      #mkdir /rescue
      #mount -o nouuid /dev/sdc2 /rescue
      #mount -o nouuid /dev/sdc1 /rescue/boot/
      #cd /rescue

      #mount -t proc proc proc
      #mount -t sysfs sys sys/
      #mount -o bind /dev dev/
      #mount -o bind /dev/pts dev/pts/
      #mount -o bind /run run/
      #chroot /rescue
      ```

   1. Rozwiązywanie problemów ze środowiskiem chroot.

   1. Użyj następujących poleceń, aby zakończyć środowisko chroot:

      ```
      #exit

      #umount /rescue/proc/
      #umount /rescue/sys/
      #umount /rescue/dev/pts
      #umount /rescue/dev/
      #umount /rescue/run
      #cd /
      #umount /rescue/boot/
      #umount /rescue
      ```

      > [!NOTE]
      > Jeśli zostanie wyświetlony komunikat o błędzie `unable to unmount /rescue` , Dodaj-l opcji do polecenia umount.
      >
      > Przykład: `umount -l /rescue`

1. Odłącz dysk od ratowniczej maszyny wirtualnej i przeprowadź wymianę dysku z oryginalną maszyną wirtualną.
1. Uruchom oryginalną maszynę wirtualną i sprawdź jej łączność.

## <a name="rhelcentos-7x-with-lvm"></a>RHEL/CentOS 7. x z LVM

   > [!NOTE]
   > Jeśli oryginalna maszyna wirtualna zawiera Menedżera woluminów logicznych (LVM) na dysku systemu operacyjnego, Utwórz maszynę wirtualną maszyny wirtualnej przy użyciu obrazu z partycjami nieprzetworzonymi na dysku systemu operacyjnego.

1. Zatrzymywanie lub cofanie alokacji odnośnej maszyny wirtualnej.
1. Utwórz sprzętową maszynę wirtualną w tej samej wersji systemu operacyjnego, w tej samej grupie zasobów (RSG) i lokalizacji przy użyciu dysku zarządzanego.
1. Użyj Azure Portal, aby utworzyć migawkę dysku systemu operacyjnego, którego dotyczy ta maszyna wirtualna.
1. Utwórz dysk poza migawką dysku systemu operacyjnego i dołącz go do ratowniczej maszyny wirtualnej.
1. Po utworzeniu dysku Rozwiąż problemy ze środowiskiem chroot na maszynie wirtualnej.

   1. Uzyskaj dostęp do maszyny wirtualnej jako użytkownik główny przy użyciu następującego polecenia:

      `#sudo su -`

   1. Znajdź dysk przy użyciu `dmesg` (Metoda używana do odnajdywania nowego dysku może się różnić). Poniższy przykład używa **dmesg** do filtrowania na dyskach **SCSI** :

      `dmesg | grep SCSI`

      Dane wyjściowe będą podobne do poniższego przykładu. W tym przykładzie chcemy, aby **SDC** dysk:

      ```
      [    0.294784] SCSI subsystem initialized
      [    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
      [    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
      [    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
      [ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk
      ```

   1. Aby aktywować grupę woluminów logicznych, użyj następującego polecenia:

      ```
      #vgscan --mknodes
      #vgchange -ay
      #lvscan
      ```

   1. Użyj `lsblk` polecenia, aby pobrać nazwy LVM:

      ```
      [user@myvm ~]$ lsblk
      NAME              MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
      sda                 8:0    0   64G  0 disk
      ├─sda1              8:1    0  500M  0 part /boot
      ├─sda2              8:2    0   63G  0 part /
      sdb                 8:16   0    4G  0 disk
      └─sdb1              8:17   0    4G  0 part /mnt/resource
      sdc                 8:0    0   64G  0 disk
      ├─sdc1              8:1    0  500M  0 part
      ├─sdc2              8:2    0   63G  0 part
      ├─sdc3              8:3    0    2M  0 part
      ├─sdc4              8:4    0   63G  0 part
        ├─rootvg-tmplv  253:0    0    2G  0 lvm  
        ├─rootvg-usrlv  253:1    0   10G  0 lvm  
        ├─rootvg-optlv  253:2    0    2G  0 lvm  
        ├─rootvg-homelv 253:3    0    1G  0 lvm  
        ├─rootvg-varlv  253:4    0    8G  0 lvm  
        └─rootvg-rootlv 253:5    0    2G  0 lvm
      ```

   1. Użyj następujących poleceń, aby uzyskać dostęp do środowiska chroot:

      ```
      #mkdir /rescue
      #mount /dev/mapper/rootvg-rootlv /rescue
      #mount /dev/mapper/rootvg-varlv /rescue/var
      #mount /dev/mapper/rootvg-homelv /rescue/home
      #mount /dev/mapper/rootvg-usrlv /rescue/usr
      #mount /dev/mapper/rootvg-tmplv /rescue/tmp
      #mount /dev/mapper/rootvg-optlv /rescue/opt
      #mount /dev/sdc2 /rescue/boot/
      #mount /dev/sdc1 /rescue/boot/efi
      #cd /rescue

      #mount -t proc proc proc
      #mount -t sysfs sys sys/
      #mount -o bind /dev dev/
      #mount -o bind /dev/pts dev/pts/
      #mount -o bind /run run/
      #chroot /rescue
      ```

   1. Rozwiązywanie problemów ze środowiskiem chroot.

   1. Użyj następujących poleceń, aby zakończyć środowisko chroot:

      ```
      #exit

      #umount /rescue/proc/
      #umount /rescue/sys/
      #umount /rescue/dev/pts
      #umount /rescue/dev/
      #umount /rescue/run
      #cd /
      #umount /rescue/boot/efi
      #umount /rescue/boot
      #umount /rescue/home
      #umount /rescue/var
      #umount /rescue/usr
      #umount /rescue/tmp
      #umount /rescue/opt
      #umount /rescue
      ```

      > [!NOTE]
      > Jeśli zostanie wyświetlony komunikat o błędzie `unable to unmount /rescue` , Dodaj-l opcji do polecenia umount.
      >
      > Przykład: `umount -l /rescue`

1. Odłącz dysk od ratowniczej maszyny wirtualnej i przeprowadź wymianę dysku z oryginalną maszyną wirtualną.
1. Uruchom oryginalną maszynę wirtualną i sprawdź jej łączność.

## <a name="rhel-8x-with-lvm"></a>RHEL 8. x z LVM

   > [!NOTE]
   > Jeśli oryginalna maszyna wirtualna zawiera Menedżera woluminów logicznych (LVM) na dysku systemu operacyjnego, Utwórz maszynę wirtualną maszyny wirtualnej przy użyciu obrazu z partycjami nieprzetworzonymi na dysku systemu operacyjnego.

1. Zatrzymywanie lub cofanie alokacji odnośnej maszyny wirtualnej.
1. Utwórz sprzętową maszynę wirtualną w tej samej wersji systemu operacyjnego, w tej samej grupie zasobów (RSG) i lokalizacji przy użyciu dysku zarządzanego.
1. Użyj Azure Portal, aby utworzyć migawkę dysku systemu operacyjnego, którego dotyczy ta maszyna wirtualna.
1. Utwórz dysk poza migawką dysku systemu operacyjnego i dołącz go do ratowniczej maszyny wirtualnej.
1. Po utworzeniu dysku Rozwiąż problemy ze środowiskiem chroot na maszynie wirtualnej.

   1. Uzyskaj dostęp do maszyny wirtualnej jako użytkownik główny przy użyciu następującego polecenia:

      `#sudo su -`

   1. Znajdź dysk przy użyciu `dmesg` (Metoda używana do odnajdywania nowego dysku może się różnić). Poniższy przykład używa **dmesg** do filtrowania na dyskach **SCSI** :

      `dmesg | grep SCSI`

      Dane wyjściowe będą podobne do poniższego przykładu. W tym przykładzie chcemy, aby **SDC** dysk:

      ```
      [    0.294784] SCSI subsystem initialized
      [    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
      [    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
      [    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
      [ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk
      ```

   1. Aby aktywować grupę woluminów logicznych, użyj następującego polecenia:

      ```
      #vgscan --mknodes
      #vgchange -ay
      #lvscan
      ```

   1. Użyj `lsblk` polecenia, aby pobrać nazwy LVM:

      ```
      [user@myvm ~]$ lsblk
      NAME              MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
      sda                 8:0    0   64G  0 disk
      ├─sda1              8:1    0  500M  0 part /boot
      ├─sda2              8:2    0   63G  0 part /
      sdb                 8:16   0    4G  0 disk
      └─sdb1              8:17   0    4G  0 part /mnt/resource
      sdc                 8:0    0   64G  0 disk
      ├─sdc1              8:1    0  500M  0 part
      ├─sdc2              8:2    0   63G  0 part
      │ ├─rootvg-tmplv  253:0    0    2G  0 lvm  
      │ ├─rootvg-usrlv  253:1    0   10G  0 lvm  
      │ ├─rootvg-homelv 253:2    0    1G  0 lvm  
      │ ├─rootvg-varlv  253:3    0    8G  0 lvm  
      │ └─rootvg-rootlv 253:4    0    2G  0 lvm  
      ├─sdc14             8:14   0    4M  0 part
      └─sdc15             8:15   0  495M  0 part
      ```

   1. Użyj następujących poleceń, aby uzyskać dostęp do środowiska chroot:

      ```
      #mkdir /rescue
      #mount /dev/mapper/rootvg-rootlv /rescue
      #mount /dev/mapper/rootvg-varlv /rescue/var
      #mount /dev/mapper/rootvg-homelv /rescue/home
      #mount /dev/mapper/rootvg-usrlv /rescue/usr
      #mount /dev/mapper/rootvg-tmplv /rescue/tmp
      #mount /dev/sdc1 /rescue/boot/
      #mount /dev/sdc15 /rescue/boot/efi
      #cd /rescue

      #mount -t proc proc proc
      #mount -t sysfs sys sys/
      #mount -o bind /dev dev/
      #mount -o bind /dev/pts dev/pts/
      #mount -o bind /run run/
      #chroot /rescue
      ```

   1. Rozwiązywanie problemów ze środowiskiem chroot.

   1. Użyj następujących poleceń, aby zakończyć środowisko chroot:

      ```
      #exit

      #umount /rescue/proc/
      #umount /rescue/sys/
      #umount /rescue/dev/pts
      #umount /rescue/dev/
      #umount /rescue/run
      #cd /
      #umount /rescue/boot/efi
      #umount /rescue/boot
      #umount /rescue/home
      #umount /rescue/var
      #umount /rescue/usr
      #umount /rescue/tmp
      #umount /rescue
      ```

      > [!NOTE]
      > Jeśli zostanie wyświetlony komunikat o błędzie `unable to unmount /rescue` , Dodaj-l opcji do polecenia umount.
      >
      > Przykład: `umount -l /rescue`

1. Odłącz dysk od ratowniczej maszyny wirtualnej i przeprowadź wymianę dysku z oryginalną maszyną wirtualną.
1. Uruchom oryginalną maszynę wirtualną i sprawdź jej łączność.

## <a name="oracle-7x"></a>Oracle 7. x

1. Zatrzymywanie lub cofanie alokacji odnośnej maszyny wirtualnej.
1. Utwórz sprzętową maszynę wirtualną w tej samej wersji systemu operacyjnego, w tej samej grupie zasobów (RSG) i lokalizacji przy użyciu dysku zarządzanego.
1. Użyj Azure Portal, aby utworzyć migawkę dysku systemu operacyjnego, którego dotyczy ta maszyna wirtualna.
1. Utwórz dysk poza migawką dysku systemu operacyjnego i dołącz go do ratowniczej maszyny wirtualnej.
1. Po utworzeniu dysku Rozwiąż problemy ze środowiskiem chroot na maszynie wirtualnej.

   1. Uzyskaj dostęp do maszyny wirtualnej jako użytkownik główny przy użyciu następującego polecenia:

      `#sudo su -`

   1. Znajdź dysk przy użyciu `dmesg` (Metoda używana do odnajdywania nowego dysku może się różnić). Poniższy przykład używa **dmesg** do filtrowania na dyskach **SCSI** :

      `dmesg | grep SCSI`

      Dane wyjściowe będą podobne do poniższego przykładu. W tym przykładzie chcemy, aby **SDC** dysk:

      ```
      [    0.294784] SCSI subsystem initialized
      [    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
      [    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
      [    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
      [ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk
      ```

   1. Użyj następujących poleceń, aby uzyskać dostęp do środowiska chroot:

      ```
      #mkdir /rescue
      #mount -o nouuid /dev/sdc2 /rescue
      #mount -o nouuid /dev/sdc1 /rescue/boot/
      #mount /dev/sdc15 /rescue/boot/efi
      #cd /rescue

      #mount -t proc proc proc
      #mount -t sysfs sys sys/
      #mount -o bind /dev dev/
      #mount -o bind /dev/pts dev/pts/
      #mount -o bind /run run/
      ##chroot /rescue
      ```

   1. Rozwiązywanie problemów ze środowiskiem chroot.

   1. Użyj następujących poleceń, aby zakończyć środowisko chroot:

      ```
      #exit

      #umount /rescue/proc/
      #umount /rescue/sys/
      #umount /rescue/dev/pts
      #umount /rescue/dev/
      #umount /rescue/run
      #cd /
      #umount /rescue/boot/efi
      #umount /rescue/boot
      #umount /rescue
      ```

      > [!NOTE]
      > Jeśli zostanie wyświetlony komunikat o błędzie `unable to unmount /rescue` , Dodaj-l opcji do polecenia umount.
      >
      > Przykład: `umount -l /rescue`

1. Odłącz dysk od ratowniczej maszyny wirtualnej i przeprowadź wymianę dysku z oryginalną maszyną wirtualną.
1. Uruchom oryginalną maszynę wirtualną i sprawdź jej łączność.

## <a name="suse-sles-12-sp4-suse-sles-12-sp4-for-sap---suse-sles-15-sp1-suse-sles-15-sp1-for-sap"></a>SUSE-SLES 12 z dodatkiem SP4, SUSE-SLES 12 SP4 for SAP &&  # # SUSE-SLES 15 SP1, SUSE-SLES 15 SP1 dla SAP

1. Zatrzymywanie lub cofanie alokacji odnośnej maszyny wirtualnej.
1. Utwórz sprzętową maszynę wirtualną w tej samej wersji systemu operacyjnego, w tej samej grupie zasobów (RSG) i lokalizacji przy użyciu dysku zarządzanego.
1. Użyj Azure Portal, aby utworzyć migawkę dysku systemu operacyjnego, którego dotyczy ta maszyna wirtualna.
1. Utwórz dysk poza migawką dysku systemu operacyjnego i dołącz go do ratowniczej maszyny wirtualnej.
1. Po utworzeniu dysku Rozwiąż problemy ze środowiskiem chroot na maszynie wirtualnej.

   1. Uzyskaj dostęp do maszyny wirtualnej jako użytkownik główny przy użyciu następującego polecenia:

      `#sudo su -`

   1. Znajdź dysk przy użyciu `dmesg` (Metoda używana do odnajdywania nowego dysku może się różnić). Poniższy przykład używa **dmesg** do filtrowania na dyskach **SCSI** :

      `dmesg | grep SCSI`

      Dane wyjściowe będą podobne do poniższego przykładu. W tym przykładzie chcemy, aby **SDC** dysk:

      ```
      [    0.294784] SCSI subsystem initialized
      [    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
      [    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
      [    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
      [ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk
      ```

   1. Użyj następujących poleceń, aby uzyskać dostęp do środowiska chroot:

      ```
      #mkdir /rescue
      #mount -o nouuid /dev/sdc4 /rescue
      #mount -o nouuid /dev/sdc3 /rescue/boot/
      #mount /dev/sdc2 /rescue/boot/efi
      #cd /rescue

      #mount -t proc proc proc
      #mount -t sysfs sys sys/
      #mount -o bind /dev dev/
      #mount -o bind /dev/pts dev/pts/
      #mount -o bind /run run/
      #chroot /rescue
      ```

   1. Rozwiązywanie problemów ze środowiskiem chroot.

   1. Użyj następujących poleceń, aby zakończyć środowisko chroot:

      ```
      #exit

      #umount /rescue/proc/
      #umount /rescue/sys/
      #umount /rescue/dev/pts
      #umount /rescue/dev/
      #umount /rescue/run
      #cd /
      #umount /rescue/boot/efi
      #umount /rescue/boot
      #umount /rescue
      ```

      > [!NOTE]
      > Jeśli zostanie wyświetlony komunikat o błędzie `unable to unmount /rescue` , Dodaj-l opcji do polecenia umount.
      >
      > Przykład: `umount -l /rescue`

1. Odłącz dysk od ratowniczej maszyny wirtualnej i przeprowadź wymianę dysku z oryginalną maszyną wirtualną.
1. Uruchom oryginalną maszynę wirtualną i sprawdź jej łączność.

## <a name="next-steps"></a>Następne kroki

- [Rozwiązywanie problemów z połączeniem SSH](troubleshoot-ssh-connection.md)
