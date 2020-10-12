---
title: Konfigurowanie RAID oprogramowania na maszynie wirtualnej z systemem Linux
description: Dowiedz się, jak skonfigurować macierz RAID w systemie Linux na platformie Azure przy użyciu mdadm.
author: rickstercdn
ms.service: virtual-machines-linux
ms.topic: how-to
ms.date: 02/02/2017
ms.author: rclaus
ms.subservice: disks
ms.openlocfilehash: 3471ccfa0899f73969c511dea283c2d0d7051af8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "84659792"
---
# <a name="configure-software-raid-on-linux"></a>Konfigurowanie RAID oprogramowania w systemie Linux
Typowym scenariuszem jest używanie oprogramowania RAID na maszynach wirtualnych z systemem Linux na platformie Azure, aby przedstawić wiele dołączonych dysków danych jako pojedyncze urządzenie RAID. Zwykle może to służyć do zwiększenia wydajności i zapewnienia lepszej przepływności w porównaniu z użyciem jednego dysku.

## <a name="attaching-data-disks"></a>Dołączanie dysków danych
Do skonfigurowania urządzenia RAID potrzeba co najmniej dwóch pustych dysków danych.  Podstawową przyczyną tworzenia urządzenia RAID jest poprawa wydajności operacji we/wy dysku.  W zależności od potrzeb we/wy można dołączać dyski, które są przechowywane w magazynie w warstwie Standardowa, z maksymalnie 500 operacji we/wy na dysk lub w naszym magazynie w warstwie Premium przy użyciu maksymalnie 5000 operacji we/wy na dysk. W tym artykule opisano sposób aprowizacji i dołączania dysków danych do maszyny wirtualnej z systemem Linux.  Zapoznaj się z artykułem Microsoft Azure. [Dołącz dysk](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) , aby uzyskać szczegółowe instrukcje dotyczące dołączania pustego dysku danych do maszyny wirtualnej z systemem Linux na platformie Azure.

> [!IMPORTANT]
>Nie mieszaj dysków o różnych rozmiarach, dlatego wydajność raidset będzie ograniczona do rozmiaru najwolniejszego dysku. 

## <a name="install-the-mdadm-utility"></a>Instalowanie narzędzia mdadm
* **Ubuntu**
  ```bash
  sudo apt-get update
  sudo apt-get install mdadm
  ```

* **CentOS & Oracle Linux**
  ```bash
  sudo yum install mdadm
  ```

* **SLES i openSUSE**
  ```bash  
  zypper install mdadm
  ```

## <a name="create-the-disk-partitions"></a>Utwórz partycje dysku
W tym przykładzie tworzymy jedną partycję dysku na/dev/SDC. Nowa partycja dysku zostanie wywołana/dev/sdc1.

1. Zacznij `fdisk` tworzyć partycje

    ```bash
    sudo fdisk /dev/sdc
    Device contains neither a valid DOS partition table, nor Sun, SGI or OSF disklabel
    Building a new DOS disklabel with disk identifier 0xa34cb70c.
    Changes will remain in memory only, until you decide to write them.
    After that, of course, the previous content won't be recoverable.

    WARNING: DOS-compatible mode is deprecated. It's strongly recommended to
                    switch off the mode (command 'c') and change display units to
                    sectors (command 'u').
    ```

1. Naciśnij klawisz "n" w wierszu polecenia, aby utworzyć **n**-nową partycję:

    ```bash
    Command (m for help): n
    ```

1. Następnie naciśnij klawisz "p", aby utworzyć partycję **p**rimary:

    ```bash 
    Command action
            e   extended
            p   primary partition (1-4)
    ```

1. Naciśnij klawisz "1", aby wybrać partycję o numerze 1:

    ```bash
    Partition number (1-4): 1
    ```

1. Wybierz punkt początkowy nowej partycji lub naciśnij przycisk, `<enter>` Aby zaakceptować wartość domyślną, aby umieścić partycję na początku wolnego miejsca na dysku:

    ```bash   
    First cylinder (1-1305, default 1):
    Using default value 1
    ```

1. Wybierz rozmiar partycji, na przykład wpisz "+ 10G", aby utworzyć partycję 10 gigabajtów. Lub naciśnij pozycję `<enter>` Utwórz jedną partycję obejmującą cały dysk:

    ```bash   
    Last cylinder, +cylinders or +size{K,M,G} (1-1305, default 1305): 
    Using default value 1305
    ```

1. Następnie zmień identyfikator oraz typ **t**partycji z domyślnego identyfikatora "83" (Linux) na identyfikator "FD" (system Linux RAID):

    ```bash  
    Command (m for help): t
    Selected partition 1
    Hex code (type L to list codes): fd
    ```

1. Na koniec Zapisz tabelę partycji na dysku i wyjdź z narzędzia Fdisk:

    ```bash   
    Command (m for help): w
    The partition table has been altered!
    ```

## <a name="create-the-raid-array"></a>Tworzenie macierzy RAID
1. W poniższym przykładzie przedstawiono "Stripe" (poziom macierzy RAID 0) trzy partycje zlokalizowane na trzech oddzielnych dyskach danych (sdc1, sdd1, SDE1).  Po uruchomieniu tego polecenia zostanie utworzone nowe urządzenie RAID o nazwie **/dev/md127** . Należy również zauważyć, że jeśli te dyski danych były wcześniej częścią innej unieczynnionej macierzy RAID, może być konieczne dodanie `--force` parametru do `mdadm` polecenia:

    ```bash  
    sudo mdadm --create /dev/md127 --level 0 --raid-devices 3 \
        /dev/sdc1 /dev/sdd1 /dev/sde1
    ```

1. Utwórz system plików na nowym urządzeniu RAID
   
    **CentOS, Oracle Linux, SLES 12, openSUSE i Ubuntu**

    ```bash   
    sudo mkfs -t ext4 /dev/md127
    ```
   
    **SLES 11**

    ```bash
    sudo mkfs -t ext3 /dev/md127
    ```
   
    **SLES 11** — Włączanie Boot.MD i tworzenie mdadm. conf

    ```bash
    sudo -i chkconfig --add boot.md
    sudo echo 'DEVICE /dev/sd*[0-9]' >> /etc/mdadm.conf
    ```
   
   > [!NOTE]
   > Po wprowadzeniu tych zmian w systemach SUSE może być wymagane ponowne uruchomienie komputera. Ten krok *nie* jest wymagany w programie SLES 12.
   > 
   

## <a name="add-the-new-file-system-to-etcfstab"></a>Dodaj nowy system plików do/etc/fstab
> [!IMPORTANT]
> Niewłaściwe edytowanie pliku/etc/fstab może spowodować, że system nie zostanie rozruchowy. Jeśli nie masz pewności, jak to zrobić, sprawdź informacje na temat prawidłowego edytowania tego pliku w dokumentacji dystrybucji. Przed rozpoczęciem edycji zaleca się również utworzenie kopii zapasowej pliku/etc/fstab.

1. Utwórz żądany punkt instalacji dla nowego systemu plików, na przykład:

    ```bash
    sudo mkdir /data
    ```
1. Podczas edytowania/etc/fstab **identyfikator UUID** powinien być używany do odwoływania się do systemu plików, a nie nazwy urządzenia.  Użyj `blkid` Narzędzia, aby określić identyfikator UUID dla nowego systemu plików:

    ```bash   
    sudo /sbin/blkid
    ...........
    /dev/md127: UUID="aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee" TYPE="ext4"
    ```

1. Otwórz/etc/fstab w edytorze tekstów i Dodaj wpis dla nowego systemu plików, na przykład:

    ```bash   
    UUID=aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee  /data  ext4  defaults  0  2
    ```
   
    Lub w **SLES 11**:

    ```bash
    /dev/disk/by-uuid/aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee  /data  ext3  defaults  0  2
    ```
   
    Następnie Zapisz i Zamknij katalogu/etc/fstab.

1. Sprawdź, czy wpis/etc/fstab jest poprawny:

    ```bash  
    sudo mount -a
    ```

    Jeśli to polecenie spowoduje komunikat o błędzie, należy sprawdzić składnię w pliku/etc/fstab.
   
    Następnie uruchom `mount` polecenie, aby upewnić się, że system plików jest zainstalowany:

    ```bash   
    mount
    .................
    /dev/md127 on /data type ext4 (rw)
    ```

1. Obowiązkowe Failsafe parametry rozruchu
   
    **Konfiguracja fstab**
   
    Wiele dystrybucji obejmuje `nobootwait` `nofail` parametry instalacji, które mogą zostać dodane do pliku/etc/fstab. Te parametry zezwalają na błędy podczas instalowania określonego systemu plików i umożliwiają dalsze rozruch systemu Linux, nawet jeśli nie można poprawnie zainstalować systemu plików RAID. Aby uzyskać więcej informacji na temat tych parametrów, zapoznaj się z dokumentacją dystrybucji.
   
    Przykład (Ubuntu):

    ```bash  
    UUID=aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee  /data  ext4  defaults,nobootwait  0  2
    ```   

    **Parametry rozruchu systemu Linux**
   
    Oprócz powyższych parametrów, parametr jądra " `bootdegraded=true` " może pozwolić na rozruch systemu nawet wtedy, gdy maszyna wirtualna jest traktowana jako uszkodzona lub obniżona, na przykład jeśli dysk danych został przypadkowo usunięty z maszyny wirtualnej. Domyślnie może to spowodować, że system nie rozruchowy.
   
    Zapoznaj się z dokumentacją dystrybucji dotyczącą sposobu prawidłowego edytowania parametrów jądra. Na przykład w wielu dystrybucjach (CentOS, Oracle Linux, SLES 11) te parametry można dodać ręcznie do `/boot/grub/menu.lst` pliku "".  W Ubuntu ten parametr można dodać do `GRUB_CMDLINE_LINUX_DEFAULT` zmiennej w "/etc/default/grub".


## <a name="trimunmap-support"></a>Obsługa przycinania/mapowania
Niektóre jądra systemu Linux obsługują operacje przycinania/mapowania do odrzucania nieużywanych bloków na dysku. Te operacje są szczególnie przydatne w przypadku magazynu w warstwie Standardowa, aby poinformować platformę Azure, że usunięte strony nie są już prawidłowe i mogą zostać odrzucone. Odrzucanie stron może zaoszczędzić koszt w przypadku tworzenia dużych plików, a następnie ich usuwania.

> [!NOTE]
> RAID nie może wydać poleceń odrzucania, jeśli rozmiar fragmentu tablicy jest ustawiony na wartość mniejszą niż domyślna (512 KB). Wynika to z faktu, że stopień szczegółowości mapowania na hoście również jest 512 KB. W przypadku zmodyfikowania rozmiaru fragmentu tablicy za pośrednictwem `--chunk=` parametru mdadm, żądanie przycinania/mapowania może być ignorowane przez jądro.

Istnieją dwa sposoby włączania obsługi przycinania na maszynie wirtualnej z systemem Linux. W zwykły sposób zapoznaj się z dystrybucją, aby uzyskać zalecane podejście:

- Użyj `discard` opcji instalacji w programie `/etc/fstab` , na przykład:

    ```bash
    UUID=aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee  /data  ext4  defaults,discard  0  2
    ```

- W niektórych przypadkach `discard` opcja może mieć wpływ na wydajność. Alternatywnie można uruchomić `fstrim` polecenie ręcznie z wiersza polecenia lub dodać je do crontab w celu regularnego uruchamiania:

    **Ubuntu**

    ```bash
    # sudo apt-get install util-linux
    # sudo fstrim /data
    ```

    **RHEL/CentOS**
    ```bash
    # sudo yum install util-linux
    # sudo fstrim /data
    ```
