---
title: Zmiana rozmiaru dysku systemu operacyjnego z partycją GPT
description: Ten artykuł zawiera instrukcje dotyczące zmiany rozmiaru dysku systemu operacyjnego, który ma partycję z tabelą partycji GUID (GPT) w systemie Linux.
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
ms.openlocfilehash: 76aa18c9724d85b1dd3fb8de3d7d033d40ff95ce
ms.sourcegitcommit: cc13f3fc9b8d309986409276b48ffb77953f4458
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/14/2020
ms.locfileid: "97400237"
---
# <a name="resize-an-os-disk-that-has-a-gpt-partition"></a>Zmiana rozmiaru dysku systemu operacyjnego z partycją GPT

> [!NOTE]
> Ten artykuł dotyczy tylko dysków systemu operacyjnego z partycją z tabelą partycji GUID (GPT).

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

Aby zwiększyć rozmiar dysku systemu operacyjnego w Ubuntu 16. *x* i 18. *x*:

1. Zatrzymaj maszynę wirtualną.
1. Zwiększ rozmiar dysku systemu operacyjnego z portalu.
1. Uruchom ponownie maszynę wirtualną, a następnie zaloguj się do maszyny wirtualnej jako użytkownik **główny** .
1. Sprawdź, czy na dysku systemu operacyjnego jest teraz wyświetlany zwiększony rozmiar systemu plików.

W poniższym przykładzie zmieniono rozmiar dysku systemu operacyjnego z portalu do 100 GB. System plików **/dev/sda1** zainstalowany na **/** teraz wyświetla 97 GB.

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

1. Uzyskaj dostęp do maszyny wirtualnej jako użytkownik **główny** przy użyciu tego polecenia:

   ```
   # sudo -i
   ```

1. Użyj poniższego polecenia, aby zainstalować pakiet **growpart** , który będzie używany do zmiany rozmiaru partycji:

   ```
   # zypper install growpart
   ```

1. Użyj `lsblk` polecenia, aby znaleźć partycję zainstalowaną w folderze głównym systemu plików ( **/** ). W takim przypadku zobaczymy, że partycja 4 urządzenia **SDA** jest zainstalowana na **/** :

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

1. Zmień rozmiar wymaganej partycji za pomocą `growpart` polecenia i numeru partycji określonego w poprzednim kroku:

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
   
   W przypadku **XFS** Użyj tego polecenia:
   
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
   
   W przypadku **ext4** Użyj tego polecenia:
   
   ```
   #resize2fs /dev/sda4
   ```
   
1. Sprawdź zwiększony rozmiar systemu plików dla **DF-ty** przy użyciu tego polecenia:
   
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

### <a name="rhel-with-lvm"></a>RHEL z LVM

1. Uzyskaj dostęp do maszyny wirtualnej jako użytkownik **główny** przy użyciu tego polecenia:

   ```bash
   [root@dd-rhel7vm ~]# sudo -i
   ```

1. Użyj `lsblk` polecenia, aby określić, który wolumin logiczny (LV) jest instalowany w katalogu głównym systemu plików ( **/** ). W takim przypadku zobaczymy, że **rootvg-rootlv** jest zainstalowany w systemie **/** . Jeśli chcesz użyć innego systemu plików, Zastąp punkt LV i punktu instalacji w tym artykule.

   ```shell
   [root@dd-rhel7vm ~]# lsblk -f
   NAME                  FSTYPE      LABEL   UUID                                   MOUNTPOINT
   fd0
   sda
   ├─sda1                vfat                C13D-C339                              /boot/efi
   ├─sda2                xfs                 8cc4c23c-fa7b-4a4d-bba8-4108b7ac0135   /boot
   ├─sda3
   └─sda4                LVM2_member         zx0Lio-2YsN-ukmz-BvAY-LCKb-kRU0-ReRBzh
      ├─rootvg-tmplv      xfs                 174c3c3a-9e65-409a-af59-5204a5c00550   /tmp
      ├─rootvg-usrlv      xfs                 a48dbaac-75d4-4cf6-a5e6-dcd3ffed9af1   /usr
      ├─rootvg-optlv      xfs                 85fe8660-9acb-48b8-98aa-bf16f14b9587   /opt
      ├─rootvg-homelv     xfs                 b22432b1-c905-492b-a27f-199c1a6497e7   /home
      ├─rootvg-varlv      xfs                 24ad0b4e-1b6b-45e7-9605-8aca02d20d22   /var
      └─rootvg-rootlv     xfs                 4f3e6f40-61bf-4866-a7ae-5c6a94675193   /
   ```

1. Sprawdź, czy w grupie woluminów LVM (VG), która zawiera partycję główną, znajduje się wolne miejsce. Jeśli jest wolne miejsce, przejdź do kroku 12.

   ```bash
   [root@dd-rhel7vm ~]# vgdisplay rootvg
   --- Volume group ---
   VG Name               rootvg
   System ID
   Format                lvm2
   Metadata Areas        1
   Metadata Sequence No  7
   VG Access             read/write
   VG Status             resizable
   MAX LV                0
   Cur LV                6
   Open LV               6
   Max PV                0
   Cur PV                1
   Act PV                1
   VG Size               <63.02 GiB
   PE Size               4.00 MiB
   Total PE              16132
   Alloc PE / Size       6400 / 25.00 GiB
   Free  PE / Size       9732 / <38.02 GiB
   VG UUID               lPUfnV-3aYT-zDJJ-JaPX-L2d7-n8sL-A9AgJb
   ```

   W tym przykładzie, bezpłatna wersja **środowiska PE/size** pokazuje, że w grupie woluminów istnieje 38,02 GB wolnego miejsca. Nie musisz zmieniać rozmiaru dysku przed dodaniem miejsca do grupy woluminów.

1. Aby zwiększyć rozmiar dysku systemu operacyjnego w RHEL 7. *x* z LVM:

   1. Zatrzymaj maszynę wirtualną.
   1. Zwiększ rozmiar dysku systemu operacyjnego z portalu.
   1. Uruchom maszynę wirtualną.

1. Po ponownym uruchomieniu maszyny wirtualnej wykonaj następujące czynności:

   - Zainstaluj pakiet **Cloud-Handlers-growpart** , aby udostępnić polecenie **growpart** , które jest wymagane do zwiększenia rozmiaru dysku systemu operacyjnego i procedury obsługi GDISK dla układów dysków GPT. Te pakiety są wstępnie instalowane na większości obrazów witryny Marketplace.

   ```bash
   [root@dd-rhel7vm ~]# yum install cloud-utils-growpart gdisk
   ```

1. Określ, który dysk i partycja przechowuje wolumin fizyczny LVM lub woluminy (PV) w grupie woluminów o nazwie **rootvg** przy użyciu `pvscan` polecenia. Zanotuj rozmiar i wolne miejsce na liście w nawiasach kwadratowych (**[** i **]**).

   ```bash
   [root@dd-rhel7vm ~]# pvscan
     PV /dev/sda4   VG rootvg          lvm2 [<63.02 GiB / <38.02 GiB free]
   ```

1. Sprawdź rozmiar partycji za pomocą polecenia `lsblk` . 

   ```bash
   [root@dd-rhel7vm ~]# lsblk /dev/sda4
   NAME            MAJ:MIN RM SIZE RO TYPE MOUNTPOINT
   sda4              8:4    0  63G  0 part
   ├─rootvg-tmplv  253:1    0   2G  0 lvm  /tmp
   ├─rootvg-usrlv  253:2    0  10G  0 lvm  /usr
   ├─rootvg-optlv  253:3    0   2G  0 lvm  /opt
   ├─rootvg-homelv 253:4    0   1G  0 lvm  /home
   ├─rootvg-varlv  253:5    0   8G  0 lvm  /var
   └─rootvg-rootlv 253:6    0   2G  0 lvm  /
   ```

1. Rozwiń partycję zawierającą to WB przy użyciu `growpart` , nazwę urządzenia i numer partycji. Wykonanie tej czynności spowoduje rozwinięcie określonej partycji w celu użycia całego wolnego miejsca na urządzeniu.

   ```bash
   [root@dd-rhel7vm ~]# growpart /dev/sda 4
   CHANGED: partition=4 start=2054144 old: size=132161536 end=134215680 new: size=199272414 end=201326558
   ```

1. Sprawdź, czy rozmiar partycji został zmieniony na rozmiar oczekiwany przy użyciu `lsblk` polecenia ponownie. Zwróć uwagę, że w przykładzie **sda4** zmienił się z 63 gb na 95 GB.

   ```bash
   [root@dd-rhel7vm ~]# lsblk /dev/sda4
   NAME            MAJ:MIN RM SIZE RO TYPE MOUNTPOINT
   sda4              8:4    0  95G  0 part
   ├─rootvg-tmplv  253:1    0   2G  0 lvm  /tmp
   ├─rootvg-usrlv  253:2    0  10G  0 lvm  /usr
   ├─rootvg-optlv  253:3    0   2G  0 lvm  /opt
   ├─rootvg-homelv 253:4    0   1G  0 lvm  /home
   ├─rootvg-varlv  253:5    0   8G  0 lvm  /var
   └─rootvg-rootlv 253:6    0   2G  0 lvm  /
   ```

1. Rozwiń WB, aby użyć reszty nowo rozwiniętej partycji:

   ```bash
   [root@dd-rhel7vm ~]# pvresize /dev/sda4
   Physical volume "/dev/sda4" changed
   1 physical volume(s) resized or updated / 0 physical volume(s) not resized
   ```

1. Sprawdź, czy nowy rozmiar PV jest oczekiwanym rozmiarem, porównując go z oryginalnymi wartościami **[size/Free]** :

   ```bash
   [root@dd-rhel7vm ~]# pvscan
   PV /dev/sda4   VG rootvg          lvm2 [<95.02 GiB / <70.02 GiB free]
   ```

1. Rozwiń żądany wolumin logiczny (LV) o żądaną kwotę. Ta kwota nie musi być całością wolnego miejsca w grupie woluminów. W poniższym przykładzie rozmiar **/dev/mapper/rootvg-rootlv** jest zmieniany z 2 GB do 12 GB (wzrost 10 GB). To polecenie spowoduje również zmianę rozmiaru systemu plików.

   ```bash
   [root@dd-rhel7vm ~]# lvresize -r -L +10G /dev/mapper/rootvg-rootlv
   ```

   Przykładowe dane wyjściowe:

   ```bash
   [root@dd-rhel7vm ~]# lvresize -r -L +10G /dev/mapper/rootvg-rootlv
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

1. `lvresize`Polecenie automatycznie wywołuje odpowiednie polecenie zmiany rozmiaru dla systemu plików w LV. Sprawdź, czy **/dev/mapper/rootvg-rootlv**, który jest zainstalowany w systemie **/** , ma zwiększony rozmiar systemu plików przy użyciu tego polecenia:

   ```shell
   [root@dd-rhel7vm ~]# df -Th /
   ```

   Przykładowe dane wyjściowe:

   ```shell
   [root@dd-rhel7vm ~]# df -Th /
   Filesystem                Type  Size  Used Avail Use% Mounted on
   /dev/mapper/rootvg-rootlv xfs    12G   71M   12G   1% /
   [root@dd-rhel7vm ~]#
   ```

> [!NOTE]
> Aby użyć tej samej procedury w celu zmiany rozmiaru dowolnego innego woluminu logicznego, należy zmienić nazwę LV w kroku 12.

### <a name="rhel-raw"></a>RHEL RAW

Aby zwiększyć rozmiar dysku systemu operacyjnego w partycji RHEL RAW:

1. Zatrzymaj maszynę wirtualną.
1. Zwiększ rozmiar dysku systemu operacyjnego z portalu.
1. Uruchom maszynę wirtualną.

Po ponownym uruchomieniu maszyny wirtualnej wykonaj następujące czynności:

1. Uzyskaj dostęp do maszyny wirtualnej jako użytkownik **główny** przy użyciu następującego polecenia:

   ```bash
   [root@dd-rhel7vm ~]# sudo -i
   ```

1. Po ponownym uruchomieniu maszyny wirtualnej wykonaj następujące czynności:

   - Zainstaluj pakiet **Cloud-Handlers-growpart** , aby udostępnić polecenie **growpart** , które jest wymagane do zwiększenia rozmiaru dysku systemu operacyjnego i procedury obsługi GDISK dla układów dysków GPT. Ten pakiet jest preinstalowany na większości obrazów witryny Marketplace.

   ```bash
   [root@dd-rhel7vm ~]# yum install cloud-utils-growpart gdisk
   ```

1. Użyj polecenia **lsblk-f** , aby sprawdzić, czy partycja i typ systemu plików posiadają **/** partycję główną ():

   ```bash
   [root@vm-dd-cent7 ~]# lsblk -f
   NAME    FSTYPE LABEL UUID                                 MOUNTPOINT
   sda
   ├─sda1  xfs          2a7bb59d-6a71-4841-a3c6-cba23413a5d2 /boot
   ├─sda2  xfs          148be922-e3ec-43b5-8705-69786b522b05 /
   ├─sda14
   └─sda15 vfat         788D-DC65                            /boot/efi
   sdb
   └─sdb1  ext4         923f51ff-acbd-4b91-b01b-c56140920098 /mnt/resource
   ```

1. Aby sprawdzić, Zacznij od wymienienia tabeli partycji dysku SDA z **gdisk**. W tym przykładzie zobaczymy dysk 48-GB z partycją 2 w 29,0 GiB. Dysk został rozwinięty z 30 GB do 48 GB w Azure Portal.

   ```bash
   [root@vm-dd-cent7 ~]# gdisk -l /dev/sda
   GPT fdisk (gdisk) version 0.8.10

   Partition table scan:
   MBR: protective
   BSD: not present
   APM: not present
   GPT: present

   Found valid GPT with protective MBR; using GPT.
   Disk /dev/sda: 100663296 sectors, 48.0 GiB
   Logical sector size: 512 bytes
   Disk identifier (GUID): 78CDF84D-9C8E-4B9F-8978-8C496A1BEC83
   Partition table holds up to 128 entries
   First usable sector is 34, last usable sector is 62914526
   Partitions will be aligned on 2048-sector boundaries
   Total free space is 6076 sectors (3.0 MiB)

   Number  Start (sector)    End (sector)  Size       Code  Name
      1         1026048         2050047   500.0 MiB   0700
      2         2050048        62912511   29.0 GiB    0700
   14            2048           10239   4.0 MiB     EF02
   15           10240         1024000   495.0 MiB   EF00  EFI System Partition
   ```

1. Rozwiń partycję dla katalogu głównego, w tym przypadku sda2 za pomocą polecenia **growpart** . Użycie tego polecenia rozwija partycję, aby użyć całego ciągłego miejsca na dysku.

   ```bash
   [root@vm-dd-cent7 ~]# growpart /dev/sda 2
   CHANGED: partition=2 start=2050048 old: size=60862464 end=62912512 new: size=98613214 end=100663262
   ```

1. Teraz Wydrukuj ponownie nową tabelę partycji z **gdisk** .  Zauważ, że partycja 2 została rozwinięta do 47,0 GiB:

   ```bash
   [root@vm-dd-cent7 ~]# gdisk -l /dev/sda
   GPT fdisk (gdisk) version 0.8.10

   Partition table scan:
   MBR: protective
   BSD: not present
   APM: not present
   GPT: present

   Found valid GPT with protective MBR; using GPT.
   Disk /dev/sda: 100663296 sectors, 48.0 GiB
   Logical sector size: 512 bytes
   Disk identifier (GUID): 78CDF84D-9C8E-4B9F-8978-8C496A1BEC83
   Partition table holds up to 128 entries
   First usable sector is 34, last usable sector is 100663262
   Partitions will be aligned on 2048-sector boundaries
   Total free space is 4062 sectors (2.0 MiB)

   Number  Start (sector)    End (sector)  Size       Code  Name
      1         1026048         2050047   500.0 MiB   0700
      2         2050048       100663261   47.0 GiB    0700
   14            2048           10239   4.0 MiB     EF02
   15           10240         1024000   495.0 MiB   EF00  EFI System Partition
   ```

1. Rozwiń system plików na partycji z **xfs_growfs**, co jest odpowiednie dla standardowego wygenerowanego w witrynie Marketplace systemu RedHat:

   ```bash
   [root@vm-dd-cent7 ~]# xfs_growfs /
   meta-data=/dev/sda2              isize=512    agcount=4, agsize=1901952 blks
            =                       sectsz=4096  attr=2, projid32bit=1
            =                       crc=1        finobt=0 spinodes=0
   data     =                       bsize=4096   blocks=7607808, imaxpct=25
            =                       sunit=0      swidth=0 blks
   naming   =version 2              bsize=4096   ascii-ci=0 ftype=1
   log      =internal               bsize=4096   blocks=3714, version=2
            =                       sectsz=4096  sunit=1 blks, lazy-count=1
   realtime =none                   extsz=4096   blocks=0, rtextents=0
   data blocks changed from 7607808 to 12326651
   ```

1. Sprawdź, czy nowy rozmiar jest odzwierciedlony przy użyciu polecenia **DF** :

   ```bash
   [root@vm-dd-cent7 ~]# df -hl
   Filesystem      Size  Used Avail Use% Mounted on
   devtmpfs        452M     0  452M   0% /dev
   tmpfs           464M     0  464M   0% /dev/shm
   tmpfs           464M  6.8M  457M   2% /run
   tmpfs           464M     0  464M   0% /sys/fs/cgroup
   /dev/sda2        48G  2.1G   46G   5% /
   /dev/sda1       494M   65M  430M  13% /boot
   /dev/sda15      495M   12M  484M   3% /boot/efi
   /dev/sdb1       3.9G   16M  3.7G   1% /mnt/resource
   tmpfs            93M     0   93M   0% /run/user/1000
   ```
