---
title: Dołączanie dysku danych do maszyny wirtualnej z systemem Linux
description: Użyj portalu, aby dołączyć nowy lub istniejący dysk danych do maszyny wirtualnej z systemem Linux.
author: cynthn
ms.service: virtual-machines-linux
ms.topic: how-to
ms.date: 07/12/2018
ms.author: cynthn
ms.subservice: disks
ms.openlocfilehash: c0129ba35841a457e73ef1061d156d3246cb65f5
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84660215"
---
# <a name="use-the-portal-to-attach-a-data-disk-to-a-linux-vm"></a>Dołączanie dysku danych do maszyny wirtualnej z systemem Linux przy użyciu portalu 
W tym artykule opisano sposób dołączania nowych i istniejących dysków do maszyny wirtualnej z systemem Linux za pomocą Azure Portal. Możesz również [dołączyć dysk danych do maszyny wirtualnej z systemem Windows w Azure Portal](../windows/attach-managed-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

Przed dołączeniem dysków do maszyny wirtualnej zapoznaj się z następującymi wskazówkami:

* Rozmiar maszyny wirtualnej kontroluje liczbę dysków z danymi, które można dołączyć. Aby uzyskać szczegółowe informacje, zobacz [rozmiary maszyn wirtualnych](sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Dyski dołączone do maszyn wirtualnych są w rzeczywistości plikami VHD przechowywanymi na platformie Azure. Aby uzyskać szczegółowe informacje, zobacz [wprowadzenie do usługi Managed disks](managed-disks-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Po dołączeniu dysku musisz [nawiązać połączenie z maszyną wirtualną z systemem Linux, aby zainstalować nowy dysk](#connect-to-the-linux-vm-to-mount-the-new-disk).


## <a name="find-the-virtual-machine"></a>Znajdź maszynę wirtualną
1. Przejdź do [Azure Portal](https://portal.azure.com/) , aby znaleźć maszynę wirtualną. Wyszukaj i wybierz pozycję **maszyny wirtualne**.
2. Z listy wybierz maszynę wirtualną.
3. Na pasku bocznym strony **maszyny wirtualne** w obszarze **Ustawienia**wybierz pozycję **dyski**.
   
    ![Otwórz ustawienia dysku](./media/attach-disk-portal/find-disk-settings.png)


## <a name="attach-a-new-disk"></a>Dołącz nowy dysk

1. W okienku **dyski** kliknij pozycję **+ Dodaj dysk danych**.
2. Kliknij menu rozwijane **Nazwa** i wybierz pozycję **Utwórz dysk**:

    ![Tworzenie dysku zarządzanego platformy Azure](./media/attach-disk-portal/create-new-md.png)

3. Wprowadź nazwę dla dysku zarządzanego. Sprawdź ustawienia domyślne, zaktualizuj je w razie potrzeby, a następnie kliknij przycisk **Utwórz**.
   
   ![Przejrzyj ustawienia dysku](./media/attach-disk-portal/create-new-md-settings.png)

4. Kliknij przycisk **Zapisz** , aby utworzyć dysk zarządzany i zaktualizować konfigurację maszyny wirtualnej:

   ![Zapisz nowy dysk zarządzany przez platformę Azure](./media/attach-disk-portal/confirm-create-new-md.png)

5. Po utworzeniu dysku przez platformę Azure i dołączeniu go do maszyny wirtualnej nowy dysk zostanie wyświetlony na liście ustawień dyskowych maszyny wirtualnej w obszarze **dyski danych**. Ponieważ dyski zarządzane są zasobami najwyższego poziomu, dysk jest wyświetlany w katalogu głównym grupy zasobów:

   ![Dysk zarządzany przez platformę Azure w grupie zasobów](./media/attach-disk-portal/view-md-resource-group.png)

## <a name="attach-an-existing-disk"></a>Dołączanie istniejącego dysku
1. W okienku **dyski** kliknij pozycję **+ Dodaj dysk danych**.
2. Kliknij menu rozwijane **, aby wyświetlić** listę istniejących dysków zarządzanych dostępnych dla Twojej subskrypcji platformy Azure. Wybierz dysk zarządzany do dołączenia:

   ![Dołącz istniejący dysk zarządzany platformy Azure](./media/attach-disk-portal/select-existing-md.png)

3. Kliknij przycisk **Zapisz** , aby dołączyć istniejący dysk zarządzany i zaktualizować konfigurację maszyny wirtualnej:
   
   ![Zapisz aktualizacje dysku zarządzanego na platformie Azure](./media/attach-disk-portal/confirm-attach-existing-md.png)

4. Po dołączeniu dysku do maszyny wirtualnej na platformie Azure jest on wyświetlany w ustawieniach dysku maszyny wirtualnej w obszarze **dyski danych**.

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

W tym miejscu *SDC* jest dyskiem, który chcemy. 

### <a name="partition-a-new-disk"></a>Partycjonowanie nowego dysku
Jeśli używasz istniejącego dysku, który zawiera dane, Pomiń Instalowanie dysku. W przypadku dołączania nowego dysku należy podzielić dysk na partycje.

> [!NOTE]
> Zalecane jest korzystanie z najnowszych wersji programu fdisk lub częściowo dostępnych dla Twojego dystrybucji.

Podziel dysk na partycje za pomocą polecenia `fdisk`. Jeśli rozmiar dysku wynosi 2 tebibajtów (TiB) lub większy, należy użyć partycjonowania GPT, za pomocą którego można `parted` wykonać partycjonowanie GPT. Jeśli rozmiar dysku jest w obszarze 2TiB, można użyć partycji MBR lub GPT. Ustaw go jako dysk podstawowy na partycji 1 i zaakceptuj inne ustawienia domyślne. Poniższy przykład uruchamia `fdisk` proces w */dev/SDC*:

```bash
sudo fdisk /dev/sdc
```

Nową partycję możesz dodać za pomocą polecenia `n`. W tym przykładzie wybieramy również `p` partycję podstawową i zaakceptujemy resztę wartości domyślnych. Dane wyjściowe będą mieć postać podobną do następującej:

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

Wydrukuj tabelę partycji przez wpisanie `p` , a następnie użyj polecenia, `w` Aby zapisać tabelę na dysku i zakończyć. Dane wyjściowe powinny wyglądać podobnie do poniższego przykładu:

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

#### <a name="alternate-method-using-parted"></a>Alternatywna metoda korzystająca z części
Narzędzie Fdisk wymaga interaktywnego wprowadzania danych, dlatego nie jest idealnym rozwiązaniem do użycia w skryptach automatyzacji. Jednak narzędzie z [częściową](https://www.gnu.org/software/parted/) sytuacją może być przetwarzane przy użyciu skryptów, a tym samym lepsze w scenariuszach automatyzacji. Narzędzie częściowo służy do partycjonowania i formatowania dysku danych. W poniższym przewodniku będziemy używać nowego dysku danych/dev/SDC i sformatować go przy użyciu systemu plików [XFS](https://xfs.wiki.kernel.org/) .
```bash
sudo parted /dev/sdc --script mklabel gpt mkpart xfspart xfs 0% 100%
sudo mkfs.xfs /dev/sdc1
partprobe /dev/sdc1
```
Jak wspomniano powyżej, użyjemy narzędzia [partprobe](https://linux.die.net/man/8/partprobe) , aby upewnić się, że jądro natychmiast zna nową partycję i system plików. Niepowodzenie użycia partprobe może spowodować, że polecenia blkid lub lslbk nie zwracają identyfikatora UUID dla nowego systemu plików natychmiast.

### <a name="mount-the-disk"></a>Instalowanie dysku
Utwórz katalog służący do instalowania systemu plików przy użyciu programu `mkdir` . W poniższym przykładzie jest tworzony katalog o godzinie */datadrive*:

```bash
sudo mkdir /datadrive
```

Służy `mount` do instalowania systemu plików. Poniższy przykład powoduje zainstalowanie partycji */dev/sdc1* w punkcie instalacji */datadrive* :

```bash
sudo mount /dev/sdc1 /datadrive
```

Aby upewnić się, że dysk zostanie automatycznie zainstalowany po ponownym uruchomieniu, należy dodać go do pliku */etc/fstab* . Zdecydowanie zaleca się również, aby identyfikator UUID (uniwersalny) był używany w */etc/fstab* , aby można było odwołać się do dysku, a nie tylko nazwy urządzenia (na przykład */dev/sdc1*). Jeśli system operacyjny wykryje błąd dysku podczas rozruchu, w przypadku używania identyfikatora UUID w danej lokalizacji nie zostanie zainstalowany nieprawidłowy dysk. Do pozostałych dysków danych zostałyby w takiej sytuacji przypisane te same identyfikatory urządzeń. Identyfikator UUID nowego dysku możesz znaleźć za pomocą narzędzia `blkid`:

```bash
sudo -i blkid
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
Po zakończeniu Zapisz plik */etc/fstab* i uruchom ponownie system.
> [!NOTE]
> Późniejsze usunięcie dysku z danymi bez edytowania fstab może spowodować niepowodzenie rozruchu maszyny wirtualnej. Większość dystrybucji oferuje opcje *nofail* i/lub *nobootwait* fstab. Te opcje umożliwiają rozruch systemu nawet wtedy, gdy instalacja dysku nie powiedzie się w czasie rozruchu. Aby uzyskać więcej informacji na temat tych parametrów, zapoznaj się z dokumentacją dystrybucji.
> 
> Opcja *nofail* gwarantuje, że maszyna wirtualna jest uruchamiana, nawet jeśli system plików jest uszkodzony lub dysk nie istnieje w czasie rozruchu. Bez tej opcji może wystąpić sytuacja, zgodnie z opisem w artykule [nie można przeprowadzić protokołu SSH do maszyny wirtualnej z systemem Linux z powodu błędów fstab](https://blogs.msdn.microsoft.com/linuxonazure/2016/07/21/cannot-ssh-to-linux-vm-after-adding-data-disk-to-etcfstab-and-rebooting/)

### <a name="trimunmap-support-for-linux-in-azure"></a>Obsługa przycinania/mapowania dla systemu Linux na platformie Azure
Niektóre jądra systemu Linux obsługują operacje przycinania/mapowania do odrzucania nieużywanych bloków na dysku. Ta funkcja jest szczególnie przydatna w przypadku magazynu w warstwie Standardowa, aby poinformować platformę Azure, że usunięte strony nie są już prawidłowe i mogą być odrzucone i mogą zaoszczędzić pieniądze w przypadku tworzenia dużych plików, a następnie ich usuwania.

Istnieją dwa sposoby włączania obsługi przycinania na maszynie wirtualnej z systemem Linux. W zwykły sposób zapoznaj się z dystrybucją, aby uzyskać zalecane podejście:

* Użyj `discard` opcji instalacji w */etc/fstab*, na przykład:

    ```bash
    UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults,discard   1   2
    ```
* W niektórych przypadkach `discard` opcja może mieć wpływ na wydajność. Alternatywnie można uruchomić `fstrim` polecenie ręcznie z wiersza polecenia lub dodać je do crontab w celu regularnego uruchamiania:
  
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

## <a name="next-steps"></a>Następne kroki
[Dysk danych](add-disk.md) można również dołączyć przy użyciu interfejsu wiersza polecenia platformy Azure.
