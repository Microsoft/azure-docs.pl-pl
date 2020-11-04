---
title: Zmiana rozmiaru dysku systemu operacyjnego z partycją GPT | Microsoft Docs
description: Ten artykuł zawiera instrukcje dotyczące zmiany rozmiarów dysku systemu operacyjnego z partycją GPT.
services: virtual-machines-linux
documentationcenter: ''
author: kailashmsft
manager: dcscontentpm
editor: ''
tags: ''
ms.service: security
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.devlang: azurecli
ms.date: 05/03/2020
ms.author: kaib
ms.custom: seodec18
ms.openlocfilehash: baa260e911673ea99b292ab5dc9895840d0098ef
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2020
ms.locfileid: "93340338"
---
# <a name="resize-an-os-disk-that-has-a-gpt-partition"></a>Zmiana rozmiaru dysku systemu operacyjnego z partycją GPT

> [!NOTE]
> Ten scenariusz dotyczy tylko dysków systemu operacyjnego z partycją z tabelą partycji GUID (GPT).

W tym artykule opisano sposób zwiększenia rozmiaru dysku systemu operacyjnego z partycją GPT w systemie Linux. 

## <a name="identify-whether-the-os-disk-has-an-mbr-or-gpt-partition"></a>Określanie, czy dysk systemu operacyjnego ma partycję MBR lub GPT

Użyj `parted` polecenia, aby określić, czy partycja dysku została utworzona za pomocą partycji głównego rekordu rozruchowego (MBR) czy partycji GPT.

### <a name="mbr-partition"></a>Partycja MBR

W poniższych danych wyjściowych **tabela partycji** zawiera wartość **MSDOS**. Ta wartość identyfikuje partycję MBR.

```
[user@myvm ~]# parted -l /dev/sda
Model: Msft Virtual Disk (scsi)
Disk /dev/sda: 107GB
Sector size (logical/physical): 512B/512B
Partition Table: msdos
Number  Start   End     Size    Type     File system  Flags
1       1049kB  525MB   524MB   primary  ext4         boot
2       525MB   34.4GB  33.8GB  primary  ext4
[user@myvm ~]#
```

### <a name="gpt-partition"></a>Partycja GPT

W poniższych danych wyjściowych **tabela partycji** zawiera wartość **GPT**. Ta wartość identyfikuje partycję GPT.

```
[user@myvm ~]# parted -l /dev/sda
Model: Msft Virtual Disk (scsi)
Disk /dev/sda: 68.7GB
Sector size (logical/physical): 512B/512B
Partition Table: gpt
Disk Flags:

Number  Start   End     Size    File system  Name                  Flags
1       1049kB  525MB   524MB   fat16        EFI System Partition  boot
2       525MB   1050MB  524MB   xfs
3       1050MB  1052MB  2097kB                                     bios_grub
4       1052MB  68.7GB  67.7GB                                     lvm
```

Jeśli maszyna wirtualna (VM) zawiera partycję GPT na dysku systemu operacyjnego, Zwiększ rozmiar dysku systemu operacyjnego.

## <a name="increase-the-size-of-the-os-disk"></a>Zwiększenie rozmiaru dysku systemu operacyjnego

Poniższe instrukcje dotyczą dystrybucji z systemem Linux.

> [!NOTE]
> Przed kontynuowaniem Utwórz kopię zapasową maszyny wirtualnej lub wykonaj migawkę dysku systemu operacyjnego.

### <a name="ubuntu"></a>Ubuntu

Aby zwiększyć rozmiar dysku systemu operacyjnego w Ubuntu 16. x i 18. x:

1. Zatrzymaj maszynę wirtualną.
1. Zwiększ rozmiar dysku systemu operacyjnego z portalu.
1. Uruchom ponownie maszynę wirtualną, a następnie zaloguj się do maszyny wirtualnej jako użytkownik **główny** .
1. Sprawdź, czy na dysku systemu operacyjnego jest teraz wyświetlany zwiększony rozmiar systemu plików.

Jak pokazano w poniższym przykładzie, rozmiar dysku systemu operacyjnego został zmieniony z portalu do 100 GB. System plików **/dev/sda1** zainstalowany na **/** teraz wyświetla 97 GB.

```
user@myvm:~# df -Th
Filesystem     Type      Size  Used Avail Use% Mounted on
udev           devtmpfs  314M     0  314M   0% /dev
tmpfs          tmpfs      65M  2.3M   63M   4% /run
/dev/sda1      ext4       97G  1.8G   95G   2% /
tmpfs          tmpfs     324M     0  324M   0% /dev/shm
tmpfs          tmpfs     5.0M     0  5.0M   0% /run/lock
tmpfs          tmpfs     324M     0  324M   0% /sys/fs/cgroup
/dev/sda15     vfat      105M  3.6M  101M   4% /boot/efi
/dev/sdb1      ext4       20G   44M   19G   1% /mnt
tmpfs          tmpfs      65M     0   65M   0% /run/user/1000
user@myvm:~#
```

### <a name="suse"></a>SUSE

Aby zwiększyć rozmiar dysku systemu operacyjnego w wersji SUSE 12 SP4, SUSE SLES 12 dla oprogramowania SAP, SUSE SLES 15 i SUSE SLES 15 dla oprogramowania SAP:

1. Zatrzymaj maszynę wirtualną.
1. Zwiększ rozmiar dysku systemu operacyjnego z portalu.
1. Uruchom ponownie maszynę wirtualną.

Po ponownym uruchomieniu maszyny wirtualnej wykonaj następujące czynności:

1. Uzyskaj dostęp do maszyny wirtualnej jako użytkownik **główny** przy użyciu następującego polecenia:

   ```
   # sudo -i
   ```

1. Użyj poniższego polecenia, aby zainstalować pakiet **growpart** , który będzie używany do zmiany rozmiaru partycji:

   ```
   # zypper install growpart
   ```

1. Użyj `lsblk` polecenia, aby znaleźć partycję zainstalowaną w katalogu głównym systemu plików ("/"). W takim przypadku widzimy, że partycja 4 urządzenia SDA jest zainstalowana na/:

   ```
   # lsblk
   NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
   sda      8:0    0   48G  0 disk
   ├─sda1   8:1    0    2M  0 part
   ├─sda2   8:2    0  512M  0 part /boot/efi
   ├─sda3   8:3    0    1G  0 part /boot
   └─sda4   8:4    0 28.5G  0 part /
   sdb      8:16   0    4G  0 disk
   └─sdb1   8:17   0    4G  0 part /mnt/resource
   ```

1. Zmień rozmiar wymaganej partycji za pomocą `growpart` polecenia, używając numeru partycji znalezionego w poprzednim kroku.

   ```
   # growpart /dev/sda 4
   CHANGED: partition=4 start=3151872 old: size=59762655 end=62914527 new: size=97511391 end=100663263
   ```

1. Uruchom `lsblk` polecenie ponownie, aby sprawdzić, czy partycja została zwiększona.

   Następujące dane wyjściowe pokazują, że rozmiar partycji **/dev/sda4** został zmieniony na 46,5 GB:
   
   ```
   linux:~ # lsblk
   NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
   sda      8:0    0   48G  0 disk
   ├─sda1   8:1    0    2M  0 part
   ├─sda2   8:2    0  512M  0 part /boot/efi
   ├─sda3   8:3    0    1G  0 part /boot
   └─sda4   8:4    0 46.5G  0 part /
   sdb      8:16   0    4G  0 disk
   └─sdb1   8:17   0    4G  0 part /mnt/resource
   ```

1. Określ typ systemu plików na dysku systemu operacyjnego za pomocą `lsblk` polecenia z `-f` flagą:

   ```
   linux:~ # lsblk -f
   NAME   FSTYPE LABEL UUID                                 MOUNTPOINT
   sda
   ├─sda1
   ├─sda2 vfat   EFI   AC67-D22D                            /boot/efi
   ├─sda3 xfs    BOOT  5731a128-db36-4899-b3d2-eb5ae8126188 /boot
   └─sda4 xfs    ROOT  70f83359-c7f2-4409-bba5-37b07534af96 /
   sdb
   └─sdb1 ext4         8c4ca904-cd93-4939-b240-fb45401e2ec6 /mnt/resource
   ```

1. W oparciu o typ systemu plików użyj odpowiednich poleceń, aby zmienić rozmiar systemu plików.
   
   W przypadku **XFS** Użyj następującego polecenia:
   
   ```
   #xfs_growfs /
   ```
   
   Przykładowe dane wyjściowe:
   
   ```
   linux:~ # xfs_growfs /
   meta-data=/dev/sda4              isize=512    agcount=4, agsize=1867583 blks
            =                       sectsz=512   attr=2, projid32bit=1
            =                       crc=1        finobt=0 spinodes=0 rmapbt=0
            =                       reflink=0
   data     =                       bsize=4096   blocks=7470331, imaxpct=25
            =                       sunit=0      swidth=0 blks
   naming   =version 2              bsize=4096   ascii-ci=0 ftype=1
   log      =internal               bsize=4096   blocks=3647, version=2
            =                       sectsz=512   sunit=0 blks, lazy-count=1
   realtime =none                   extsz=4096   blocks=0, rtextents=0
   data blocks changed from 7470331 to 12188923
   ```
   
   W przypadku **ext4** Użyj następującego polecenia:
   
   ```
   #resize2fs /dev/sda4
   ```
   
1. Sprawdź zwiększony rozmiar systemu plików dla **DF-ty** przy użyciu następującego polecenia:
   
   ```
   #df -Thl
   ```
   
   Przykładowe dane wyjściowe:
   
   ```
   linux:~ # df -Thl
   Filesystem     Type      Size  Used Avail Use% Mounted on
   devtmpfs       devtmpfs  445M  4.0K  445M   1% /dev
   tmpfs          tmpfs     458M     0  458M   0% /dev/shm
   tmpfs          tmpfs     458M   14M  445M   3% /run
   tmpfs          tmpfs     458M     0  458M   0% /sys/fs/cgroup
   /dev/sda4      xfs        47G  2.2G   45G   5% /
   /dev/sda3      xfs      1014M   86M  929M   9% /boot
   /dev/sda2      vfat      512M  1.1M  511M   1% /boot/efi
   /dev/sdb1      ext4      3.9G   16M  3.7G   1% /mnt/resource
   tmpfs          tmpfs      92M     0   92M   0% /run/user/1000
   tmpfs          tmpfs      92M     0   92M   0% /run/user/490
   ```
   
   W poprzednim przykładzie widzimy, że zwiększono rozmiar systemu plików dla dysku systemu operacyjnego.

### <a name="rhel-lvm"></a>RHEL LVM

Aby zwiększyć rozmiar dysku systemu operacyjnego w RHEL 7. x z LVM:

1. Zatrzymaj maszynę wirtualną.
1. Zwiększ rozmiar dysku systemu operacyjnego z portalu.
1. Uruchom maszynę wirtualną.

Po ponownym uruchomieniu maszyny wirtualnej wykonaj następujące czynności:

1. Uzyskaj dostęp do maszyny wirtualnej jako użytkownik **główny** przy użyciu następującego polecenia:
 
   ```
   #sudo su
   ```

1. Zainstaluj pakiet **gptfdisk** , który jest wymagany do zwiększenia rozmiaru dysku systemu operacyjnego.

   ```
   #yum install gdisk -y
   ```

1. Aby zobaczyć największy sektor dostępny na dysku, uruchom następujące polecenie:

   ```
   #sgdisk -e /dev/sda
   ```

1. Zmień rozmiar partycji bez usuwania przy użyciu następującego polecenia. **Część** polecenia ma opcję o nazwie **resizepart** , aby zmienić rozmiar partycji bez jej usuwania. Liczba 4 po **resizepart** wskazuje zmianę rozmiarów czwartej partycji.

   ```
   #parted -s /dev/sda "resizepart 4 -1" quit
   ```
    
1. Uruchom następujące polecenie, aby sprawdzić, czy partycja została zwiększona:

   ```
   #lsblk
   ```

   Poniższe dane wyjściowe pokazują, że rozmiar partycji **/dev/sda4** został zmieniony na 99 GB.

   ```
   [user@myvm ~]# lsblk
   NAME              MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
   fd0                 2:0    1    4K  0 disk
   sda                 8:0    0  100G  0 disk
   ├─sda1              8:1    0  500M  0 part /boot/efi
   ├─sda2              8:2    0  500M  0 part /boot
   ├─sda3              8:3    0    2M  0 part
   └─sda4              8:4    0   99G  0 part
   ├─rootvg-tmplv    253:0    0    2G  0 lvm  /tmp
   ├─rootvg-usrlv    253:1    0   10G  0 lvm  /usr
   ├─rootvg-optlv    253:2    0    2G  0 lvm  /opt
   ├─rootvg-homelv   253:3    0    1G  0 lvm  /home
   ├─rootvg-varlv    253:4    0    8G  0 lvm  /var
   └─rootvg-rootlv   253:5    0    2G  0 lvm  /
   sdb                 8:16   0   50G  0 disk
   └─sdb1              8:17   0   50G  0 part /mnt/resource
   ```

1. Użyj następującego polecenia, aby zmienić rozmiar woluminu fizycznego (PV):

   ```
   #pvresize /dev/sda4
   ```

   Poniższe dane wyjściowe pokazują, że rozmiar PV został zmieniony na 99,02 GB.

   ```
   [user@myvm ~]# pvresize /dev/sda4
   Physical volume "/dev/sda4" changed
   1 physical volume(s) resized or updated / 0 physical volume(s) not resized

   [user@myvm ~]# pvs
   PV         VG     Fmt  Attr PSize   PFree
   /dev/sda4  rootvg lvm2 a--  <99.02g <74.02g
   ```

1. W poniższym przykładzie rozmiar **/dev/mapper/rootvg-rootlv** jest zmieniany z 2 GB do 12 GB (wzrost 10 GB) za pomocą poniższego polecenia. To polecenie spowoduje również zmianę rozmiaru systemu plików.

   ```
   #lvresize -r -L +10G /dev/mapper/rootvg-rootlv
   ```

   Przykładowe dane wyjściowe:

   ```
   [user@myvm ~]# lvresize -r -L +10G /dev/mapper/rootvg-rootlv
   Size of logical volume rootvg/rootlv changed from 2.00 GiB (512 extents) to 12.00 GiB (3072 extents).
   Logical volume rootvg/rootlv successfully resized.
   meta-data=/dev/mapper/rootvg-rootlv isize=512    agcount=4, agsize=131072 blks
            =                       sectsz=4096  attr=2, projid32bit=1
            =                       crc=1        finobt=0 spinodes=0
   data     =                       bsize=4096   blocks=524288, imaxpct=25
            =                       sunit=0      swidth=0 blks
   naming   =version 2              bsize=4096   ascii-ci=0 ftype=1
   log      =internal               bsize=4096   blocks=2560, version=2
            =                       sectsz=4096  sunit=1 blks, lazy-count=1
   realtime =none                   extsz=4096   blocks=0, rtextents=0
   data blocks changed from 524288 to 3145728
   ```
         
1. Sprawdź, czy **/dev/mapper/rootvg-rootlv** ma zwiększony rozmiar systemu plików przy użyciu następującego polecenia:

   ```
   #df -Th /
   ```

   Przykładowe dane wyjściowe:

   ```
   [user@myvm ~]# df -Th /
   Filesystem                Type  Size  Used Avail Use% Mounted on
   /dev/mapper/rootvg-rootlv xfs    12G   71M   12G   1% /
   [user@myvm ~]#
   ```

> [!NOTE]
> Aby użyć tej samej procedury, aby zmienić rozmiar dowolnego innego woluminu logicznego, Zmień nazwę **LV** w kroku 7.

### <a name="rhel-raw"></a>RHEL RAW
>[!NOTE]
>Zawsze należy utworzyć migawkę maszyny wirtualnej przed zwiększeniem rozmiaru dysku systemu operacyjnego.

Aby zwiększyć rozmiar dysku systemu operacyjnego w RHEL z nieprzetworzoną partycją:

Zatrzymaj maszynę wirtualną.
Zwiększ rozmiar dysku systemu operacyjnego z portalu.
Uruchom maszynę wirtualną.
Po ponownym uruchomieniu maszyny wirtualnej wykonaj następujące czynności:

1. Uzyskaj dostęp do maszyny wirtualnej jako użytkownik **główny** przy użyciu następującego polecenia:
 
   ```
   sudo su
   ```

1. Zainstaluj pakiet **gptfdisk** , który jest wymagany do zwiększenia rozmiaru dysku systemu operacyjnego.

   ```
   yum install gdisk -y
   ```

1.  Aby wyświetlić wszystkie sektory dostępne na dysku, uruchom następujące polecenie:
    ```
    gdisk -l /dev/sda
    ```

1. Zobaczysz szczegóły dotyczące typu partycji. Upewnij się, że jest to tabela GPT. Zidentyfikuj partycję główną. Nie zmieniaj ani nie usuwaj partycji rozruchowej (partycja rozruchowa systemu BIOS) i partycji systemowej ("partycja systemowa EFI")

1. Użyj poniższego polecenia, aby rozpocząć partycjonowanie po raz pierwszy. 
    ```
    gdisk /dev/sda
    ```

1. Zobaczysz komunikat z prośbą o następne polecenie ("polecenie:? w celu uzyskania pomocy "). 

   ```
   w
   ```

1. Zostanie wyświetlone ostrzeżenie informujące o "ostrzeżeniu! Nagłówek pomocniczy został zbyt wcześnie umieszczony na dysku! Czy chcesz rozwiązać ten problem? (T/N): ". Musisz nacisnąć przycisk "t"

   ```
   Y
   ```

1. Powinien zostać wyświetlony komunikat z informacją, że sprawdzanie końcowe zostało ukończone i zażądaj potwierdzenia. Naciśnij klawisz "Y"

   ```
   Y
   ```

1. Sprawdź, czy wszystko zostało wykonane prawidłowo przy użyciu polecenia partprobe

   ```
   partprobe
   ```

1. Powyższe kroki zapewniają, że pomocniczy nagłówek GPT jest umieszczony na końcu. Następnym krokiem jest rozpoczęcie procesu zmiany rozmiarów przy użyciu narzędzia gdisk. Użyj poniższego polecenia.

   ```
   gdisk /dev/sda
   ```
1. Aby wyświetlić listę partycji, w menu poleceń naciśnij klawisz "p". Zidentyfikuj partycję główną (w krokach sda2 jest traktowana jako partycja główna), a partycja rozruchowa (w krokach, sda3 jest traktowana jako partycja rozruchowa) 

   ```
   p
   ```
    ![Partycja główna i partycja rozruchowa](./media/resize-os-disk-rhelraw/resize-os-disk-rhelraw1.png)

1. Naciśnij przycisk 'd ', aby usunąć partycję, a następnie wybierz numer partycji przypisanej do rozruchu (w tym przykładzie jest to "3").
   ```
   d
   3
   ```
1. Naciśnij przycisk 'd ', aby usunąć partycję, a następnie wybierz numer partycji przypisanej do rozruchu (w tym przykładzie jest to "2").
   ```
   d
   2
   ```
    ![Usuwanie partycji głównej i partycji rozruchowej](./media/resize-os-disk-rhelraw/resize-os-disk-rhelraw2.png)

1. Aby ponownie utworzyć partycję główną o większym rozmiarze, naciśnij przycisk "n", wprowadź numer partycji, który został usunięty wcześniej dla katalogu głównego ("2" w tym przykładzie) i wybierz pierwszy sektor jako "wartość domyślna", ostatni sektor jako "wartość ostatniego sektora — sektor rozmiaru rozruchu" ("4096 w tym przypadku" odpowiada na rozruch z 2 MB) i kod szesnastkowy "8300"
   ```
   n
   2
   (Enter default)
   (Calculateed value of Last sector value - 4096)
   8300
   ```
1. Aby ponownie utworzyć partycję rozruchową, należy nacisnąć klawisz "n", wprowadzić numer partycji, który został wcześniej usunięty dla rozruchu ("3" w tym przykładzie) i wybrać pierwszy sektor jako wartość domyślną ", ostatni sektor jako" wartość domyślna "i kod szesnastkowy jako" EF02 "
   ```
   n
   3
   (Enter default)
   (Enter default)
   EF02
   ```

1. Zapisz zmiany za pomocą polecenia "w" i naciśnij klawisz "Y", aby potwierdzić
   ```
   w
   Y
   ```
1. Uruchom polecenie "partprobe", aby sprawdzić stabilność dysku
   ```
   partprobe
   ```
1. Przeprowadź ponowny rozruch maszyny wirtualnej, a rozmiar partycji głównej zostałby zwiększony
   ```
   reboot
   ```

   ![Nowa partycja główna i partycja rozruchowa](./media/resize-os-disk-rhelraw/resize-os-disk-rhelraw3.png)

1. Uruchom polecenie xfs_growfs na partycji, aby zmienić ich rozmiar
   ```
   xfs_growfs /dev/sda2
   ```

   ![XFS](./media/resize-os-disk-rhelraw/resize-os-disk-rhelraw4.png)

## <a name="next-steps"></a>Następne kroki

- [Zmień rozmiar dysku](expand-disks.md)
