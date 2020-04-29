---
title: Dodawanie dysku danych do maszyny wirtualnej z systemem Linux przy użyciu interfejsu wiersza polecenia platformy Azure
description: Dowiedz się, jak dodać dysk danych trwałych do maszyny wirtualnej z systemem Linux przy użyciu interfejsu wiersza polecenia platformy Azure
author: roygara
manager: twooley
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 06/13/2018
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: a80a1fe21ba0b40aebf9e426e3d49f499c2d2a21
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79250415"
---
# <a name="add-a-disk-to-a-linux-vm"></a>Dodawanie dysku do maszyny wirtualnej z systemem Linux
W tym artykule opisano sposób dołączania dysku trwałego do maszyny wirtualnej, dzięki czemu można zachować dane, nawet jeśli maszyna wirtualna jest ponownie inicjowana z powodu konserwacji lub zmiany rozmiarów.


## <a name="attach-a-new-disk-to-a-vm"></a>Dołączanie nowego dysku do maszyny wirtualnej

Jeśli chcesz dodać nowy pusty dysk danych do maszyny wirtualnej, użyj polecenia [AZ VM Disk Attach](/cli/azure/vm/disk?view=azure-cli-latest) z `--new` parametrem. Jeśli maszyna wirtualna znajduje się w strefie dostępności, dysk jest automatycznie tworzony w tej samej strefie co maszyna wirtualna. Aby uzyskać więcej informacji, zobacz [omówienie strefy dostępności](../../availability-zones/az-overview.md). Poniższy przykład tworzy dysk o nazwie *myDataDisk* o rozmiarze 50 GB:

```azurecli
az vm disk attach \
   -g myResourceGroup \
   --vm-name myVM \
   --name myDataDisk \
   --new \
   --size-gb 50
```

## <a name="attach-an-existing-disk"></a>Dołączanie istniejącego dysku

Aby dołączyć istniejący dysk, Znajdź identyfikator dysku i przekaż identyfikator do polecenia [AZ VM Disk Attach](/cli/azure/vm/disk?view=azure-cli-latest) . W poniższym przykładzie zawarto zapytania dotyczące dysku o nazwie *myDataDisk* w liście *zasobów*, a następnie dołączenie go do maszyny wirtualnej o nazwie *myVM*:

```azurecli
diskId=$(az disk show -g myResourceGroup -n myDataDisk --query 'id' -o tsv)

az vm disk attach -g myResourceGroup --vm-name myVM --name $diskId
```

## <a name="connect-to-the-linux-vm-to-mount-the-new-disk"></a>Nawiązywanie połączenia z maszyną wirtualną z systemem Linux w celu zainstalowania nowego dysku

Aby podzielić na partycje, sformatować i zainstalować nowy dysk, aby maszyna wirtualna z systemem Linux mogła go używać, Użyj protokołu SSH do maszyny wirtualnej. Aby uzyskać więcej informacji, zobacz temat dotyczący [korzystania z protokołu SSH systemu Linux na platformie Azure](mac-create-ssh-keys.md). Poniższy przykład nawiązuje połączenie z maszyną wirtualną za pomocą publicznego wpisu DNS *mypublicdns.westus.cloudapp.Azure.com* z nazwą użytkownika *azureuser*:

```bash
ssh azureuser@mypublicdns.westus.cloudapp.azure.com
```

Po nawiązaniu połączenia z maszyną wirtualną możesz dołączyć dysk. Najpierw Znajdź dysk przy użyciu `dmesg` (Metoda używana do odnajdywania nowego dysku może się różnić). Poniższy przykład używa dmesg do filtrowania na dyskach *SCSI* :

```bash
dmesg | grep SCSI
```

Dane wyjściowe są podobne do poniższego przykładu:

```bash
[    0.294784] SCSI subsystem initialized
[    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
[    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
[    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
[ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk
```

> [!NOTE]
> Zalecane jest korzystanie z najnowszych wersji programu fdisk lub częściowo dostępnych dla Twojego dystrybucji.

W tym miejscu *SDC* jest dyskiem, który chcemy. Podziel dysk na partycje `parted`, jeśli rozmiar dysku wynosi 2 Tebibajtów (TIB) lub większy, a następnie użyj partycji GPT, jeśli jest w obszarze 2TiB, a następnie możesz użyć partycji MBR lub GPT. Jeśli używasz partycjonowania MBR, możesz użyć polecenia `fdisk`. Ustaw go jako dysk podstawowy na partycji 1 i zaakceptuj inne ustawienia domyślne. Poniższy przykład uruchamia `fdisk` proces w */dev/SDC*:

```bash
sudo fdisk /dev/sdc
```

Nową partycję możesz dodać za pomocą polecenia `n`. W tym przykładzie wybieramy `p` również partycję podstawową i zaakceptujemy resztę wartości domyślnych. Dane wyjściowe będą mieć postać podobną do następującej:

```bash
Device contains neither a valid DOS partition table, nor Sun, SGI or OSF disklabel
Building a new DOS disklabel with disk identifier 0x2a59b123.
Changes will remain in memory only, until you decide to write them.
After that, of course, the previous content won't be recoverable.

Warning: invalid flag 0x0000 of partition table 4 will be corrected by w(rite)

Command (m for help): n
Partition type:
   p   primary (0 primary, 0 extended, 4 free)
   e   extended
Select (default p): p
Partition number (1-4, default 1): 1
First sector (2048-10485759, default 2048):
Using default value 2048
Last sector, +sectors or +size{K,M,G} (2048-10485759, default 10485759):
Using default value 10485759
```

Wydrukuj tabelę partycji przez wpisanie `p` , a następnie użyj `w` polecenia, aby zapisać tabelę na dysku i zakończyć. Dane wyjściowe powinny wyglądać podobnie do poniższego przykładu:

```bash
Command (m for help): p

Disk /dev/sdc: 5368 MB, 5368709120 bytes
255 heads, 63 sectors/track, 652 cylinders, total 10485760 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk identifier: 0x2a59b123

   Device Boot      Start         End      Blocks   Id  System
/dev/sdc1            2048    10485759     5241856   83  Linux

Command (m for help): w
The partition table has been altered!

Calling ioctl() to re-read partition table.
Syncing disks.
```
Użyj poniższego polecenia, aby zaktualizować jądro:
```
partprobe 
```

Teraz napisz system plików do partycji za pomocą `mkfs` polecenia. Określ typ systemu plików i nazwę urządzenia. Poniższy przykład tworzy system plików *ext4* na partycji */dev/sdc1* , który został utworzony w poprzednich krokach:

```bash
sudo mkfs -t ext4 /dev/sdc1
```

Dane wyjściowe są podobne do poniższego przykładu:

```bash
mke2fs 1.42.9 (4-Feb-2014)
Discarding device blocks: done
Filesystem label=
OS type: Linux
Block size=4096 (log=2)
Fragment size=4096 (log=2)
Stride=0 blocks, Stripe width=0 blocks
327680 inodes, 1310464 blocks
65523 blocks (5.00%) reserved for the super user
First data block=0
Maximum filesystem blocks=1342177280
40 block groups
32768 blocks per group, 32768 fragments per group
8192 inodes per group
Superblock backups stored on blocks:
    32768, 98304, 163840, 229376, 294912, 819200, 884736
Allocating group tables: done
Writing inode tables: done
Creating journal (32768 blocks): done
Writing superblocks and filesystem accounting information: done
```

Teraz Utwórz katalog służący do instalowania systemu plików przy użyciu `mkdir`programu. W poniższym przykładzie jest tworzony katalog o godzinie */datadrive*:

```bash
sudo mkdir /datadrive
```

Służy `mount` do instalowania systemu plików. Poniższy przykład powoduje zainstalowanie partycji */dev/sdc1* w punkcie instalacji */datadrive* :

```bash
sudo mount /dev/sdc1 /datadrive
```

Aby upewnić się, że dysk zostanie automatycznie zainstalowany po ponownym uruchomieniu, należy dodać go do pliku */etc/fstab* . Zdecydowanie zaleca się również, aby identyfikator UUID (uniwersalny) był używany w */etc/fstab* , aby można było odwołać się do dysku, a nie tylko nazwy urządzenia (na przykład */dev/sdc1*). Jeśli system operacyjny wykryje błąd dysku podczas rozruchu, w przypadku używania identyfikatora UUID w danej lokalizacji nie zostanie zainstalowany nieprawidłowy dysk. Do pozostałych dysków danych zostałyby w takiej sytuacji przypisane te same identyfikatory urządzeń. Identyfikator UUID nowego dysku możesz znaleźć za pomocą narzędzia `blkid`:

```bash
sudo blkid
```

Dane wyjściowe wyglądają podobnie do poniższego przykładu:

```bash
/dev/sda1: UUID="11111111-1b1b-1c1c-1d1d-1e1e1e1e1e1e" TYPE="ext4"
/dev/sdb1: UUID="22222222-2b2b-2c2c-2d2d-2e2e2e2e2e2e" TYPE="ext4"
/dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="ext4"
```

> [!NOTE]
> Niewłaściwe edytowanie pliku **/etc/fstab** może spowodować, że system nie zostanie rozruchowy. Jeśli nie masz pewności, jak to zrobić, sprawdź informacje na temat prawidłowego edytowania tego pliku w dokumentacji dystrybucji. Przed rozpoczęciem edycji zaleca się również utworzenie kopii zapasowej pliku/etc/fstab.

Następnie otwórz plik */etc/fstab* w edytorze tekstów w następujący sposób:

```bash
sudo vi /etc/fstab
```

W tym przykładzie Użyj wartości UUID dla urządzenia */dev/sdc1* , które zostało utworzone w poprzednich krokach, i mountpoint */datadrive*. Dodaj następujący wiersz na końcu pliku */etc/fstab* :

```bash
UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults,nofail   1   2
```

> [!NOTE]
> Późniejsze usunięcie dysku z danymi bez edytowania fstab może spowodować niepowodzenie rozruchu maszyny wirtualnej. Większość dystrybucji oferuje opcje *nofail* i/lub *nobootwait* fstab. Te opcje umożliwiają rozruch systemu nawet wtedy, gdy instalacja dysku nie powiedzie się w czasie rozruchu. Aby uzyskać więcej informacji na temat tych parametrów, zapoznaj się z dokumentacją dystrybucji.
>
> Opcja *nofail* gwarantuje, że maszyna wirtualna jest uruchamiana, nawet jeśli system plików jest uszkodzony lub dysk nie istnieje w czasie rozruchu. Bez tej opcji może wystąpić sytuacja, zgodnie z opisem w artykule [nie można przeprowadzić protokołu SSH do maszyny wirtualnej z systemem Linux z powodu błędów fstab](https://blogs.msdn.microsoft.com/linuxonazure/2016/07/21/cannot-ssh-to-linux-vm-after-adding-data-disk-to-etcfstab-and-rebooting/)
>
> Za pomocą konsoli szeregowej maszyny wirtualnej platformy Azure można uzyskać dostęp do maszyny wirtualnej, jeśli modyfikacja fstab spowodowała awarię rozruchu. Więcej szczegółów można znaleźć w [dokumentacji konsoli szeregowej](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-linux).

### <a name="trimunmap-support-for-linux-in-azure"></a>Obsługa przycinania/mapowania dla systemu Linux na platformie Azure
Niektóre jądra systemu Linux obsługują operacje przycinania/mapowania do odrzucania nieużywanych bloków na dysku. Ta funkcja jest szczególnie przydatna w przypadku magazynu w warstwie Standardowa, aby poinformować platformę Azure, że usunięte strony nie są już prawidłowe i mogą być odrzucone i mogą zaoszczędzić pieniądze w przypadku tworzenia dużych plików, a następnie ich usuwania.

Istnieją dwa sposoby włączania obsługi przycinania na maszynie wirtualnej z systemem Linux. W zwykły sposób zapoznaj się z dystrybucją, aby uzyskać zalecane podejście:

* Użyj opcji `discard` instalacji w */etc/fstab*, na przykład:

    ```bash
    UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults,discard   1   2
    ```
* W niektórych przypadkach opcja może `discard` mieć wpływ na wydajność. Alternatywnie można uruchomić `fstrim` polecenie ręcznie z wiersza polecenia lub dodać je do crontab w celu regularnego uruchamiania:

    **Ubuntu**

    ```bash
    sudo apt-get install util-linux
    sudo fstrim /datadrive
    ```

    **RHEL/CentOS**

    ```bash
    sudo yum install util-linux
    sudo fstrim /datadrive
    ```

## <a name="troubleshooting"></a>Rozwiązywanie problemów

[!INCLUDE [virtual-machines-linux-lunzero](../../../includes/virtual-machines-linux-lunzero.md)]

## <a name="next-steps"></a>Następne kroki

* Aby upewnić się, że maszyna wirtualna z systemem Linux została prawidłowo skonfigurowana, przejrzyj zalecenia dotyczące [wydajności maszyny z systemem Linux](optimization.md) .
* Zwiększ pojemność magazynu, dodając dodatkowe dyski i [Skonfiguruj macierz RAID](configure-raid.md) pod kątem dodatkowej wydajności.
