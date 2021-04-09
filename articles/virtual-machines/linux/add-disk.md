---
title: Dodawanie dysku danych do maszyny wirtualnej z systemem Linux przy użyciu interfejsu wiersza polecenia platformy Azure
description: Dowiedz się, jak dodać dysk danych trwałych do maszyny wirtualnej z systemem Linux przy użyciu interfejsu wiersza polecenia platformy Azure
author: cynthn
ms.service: virtual-machines
ms.subservice: disks
ms.collection: linux
ms.topic: how-to
ms.date: 08/20/2020
ms.author: cynthn
ms.openlocfilehash: adf6198cf12011c77fcf3f93d4b595ea433ddefd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104580389"
---
# <a name="add-a-disk-to-a-linux-vm"></a>Dodawanie dysku do maszyny wirtualnej z systemem Linux

W tym artykule opisano sposób dołączania dysku trwałego do maszyny wirtualnej, dzięki czemu można zachować dane, nawet jeśli maszyna wirtualna jest ponownie inicjowana z powodu konserwacji lub zmiany rozmiarów.


## <a name="attach-a-new-disk-to-a-vm"></a>Dołączanie nowego dysku do maszyny wirtualnej

Jeśli chcesz dodać nowy pusty dysk danych do maszyny wirtualnej, użyj polecenia [AZ VM Disk Attach](/cli/azure/vm/disk) z `--new` parametrem. Jeśli maszyna wirtualna znajduje się w strefie dostępności, dysk jest automatycznie tworzony w tej samej strefie co maszyna wirtualna. Aby uzyskać więcej informacji, zobacz [omówienie strefy dostępności](../../availability-zones/az-overview.md). Poniższy przykład tworzy dysk o nazwie *myDataDisk* o rozmiarze 50 GB:

```azurecli
az vm disk attach \
   -g myResourceGroup \
   --vm-name myVM \
   --name myDataDisk \
   --new \
   --size-gb 50
```

## <a name="attach-an-existing-disk"></a>Dołączanie istniejącego dysku

Aby dołączyć istniejący dysk, Znajdź identyfikator dysku i przekaż identyfikator do polecenia [AZ VM Disk Attach](/cli/azure/vm/disk) . W poniższym przykładzie zawarto zapytania dotyczące dysku o nazwie *myDataDisk* w liście *zasobów*, a następnie dołączenie go do maszyny wirtualnej o nazwie *myVM*:

```azurecli
diskId=$(az disk show -g myResourceGroup -n myDataDisk --query 'id' -o tsv)

az vm disk attach -g myResourceGroup --vm-name myVM --name $diskId
```

## <a name="format-and-mount-the-disk"></a>Formatowanie i Instalowanie dysku

Aby podzielić na partycje, sformatować i zainstalować nowy dysk, aby maszyna wirtualna z systemem Linux mogła go używać, Użyj protokołu SSH do maszyny wirtualnej. Aby uzyskać więcej informacji, zobacz temat dotyczący [korzystania z protokołu SSH systemu Linux na platformie Azure](mac-create-ssh-keys.md). Poniższy przykład nawiązuje połączenie z maszyną wirtualną z publicznym adresem IP *10.123.123.25* z nazwą użytkownika *azureuser*:

```bash
ssh azureuser@10.123.123.25
```

### <a name="find-the-disk"></a>Wyszukiwanie dysku

Po nawiązaniu połączenia z maszyną wirtualną należy znaleźć dysk. W tym przykładzie używamy `lsblk` do wyświetlania listy dysków. 

```bash
lsblk -o NAME,HCTL,SIZE,MOUNTPOINT | grep -i "sd"
```

Dane wyjściowe są podobne do poniższego przykładu:

```bash
sda     0:0:0:0      30G
├─sda1             29.9G /
├─sda14               4M
└─sda15             106M /boot/efi
sdb     1:0:1:0      14G
└─sdb1               14G /mnt
sdc     3:0:0:0      50G
```

Oto `sdc` dysk, który chcemy, ponieważ jest 50G. Jeśli nie masz pewności, który dysk jest oparty na samym rozmiarze, możesz przejść do strony maszyny wirtualnej w portalu, wybrać pozycję **dyski** i sprawdzić numer LUN dla dysku w obszarze **dyski danych**. 


### <a name="format-the-disk"></a>Formatowanie dysku

Sformatuj dysk przy użyciu `parted` , jeśli rozmiar dysku wynosi 2 tebibajtów (TIB) lub większy, a następnie użyj partycji GPT, jeśli jest w obszarze 2TiB, a następnie możesz użyć partycji MBR lub GPT. 

> [!NOTE]
> Zalecane jest użycie najnowszej wersji `parted` , która jest dostępna dla Twojego dystrybucji.
> Jeśli rozmiar dysku wynosi 2 tebibajtów (TiB) lub większy, należy użyć partycjonowania GPT. Jeśli rozmiar dysku wynosi 2 TiB, można użyć partycji MBR lub GPT.  


W poniższym przykładzie `parted` jest używany na `/dev/sdc` , czyli gdzie pierwszy dysk z danymi będzie zazwyczaj znajdować się na większości maszyn wirtualnych. Zamień na `sdc` poprawną opcję dla dysku. Formatuje to również przy użyciu systemu plików [XFS](https://xfs.wiki.kernel.org/) .

```bash
sudo parted /dev/sdc --script mklabel gpt mkpart xfspart xfs 0% 100%
sudo mkfs.xfs /dev/sdc1
sudo partprobe /dev/sdc1
```

Użyj [`partprobe`](https://linux.die.net/man/8/partprobe) Narzędzia, aby upewnić się, że jądro zna nową partycję i system plików. Niepowodzenie użycia `partprobe` może spowodować, że polecenia blkid lub lslbk nie zwracają identyfikatora UUID dla nowego systemu plików natychmiast.


### <a name="mount-the-disk"></a>Instalowanie dysku

Teraz Utwórz katalog służący do instalowania systemu plików przy użyciu programu `mkdir` . Poniższy przykład tworzy katalog w `/datadrive` :

```bash
sudo mkdir /datadrive
```

Służy `mount` do instalowania systemu plików. W poniższym przykładzie partycja jest instalowana w `/dev/sdc1` `/datadrive` punkcie instalacji:

```bash
sudo mount /dev/sdc1 /datadrive
```

### <a name="persist-the-mount"></a>Utrwalanie instalacji

Aby upewnić się, że dysk zostanie automatycznie zainstalowany po ponownym uruchomieniu, należy dodać go do pliku */etc/fstab* . Zdecydowanie zaleca się również, aby identyfikator UUID (uniwersalny) był używany w */etc/fstab* , aby można było odwołać się do dysku, a nie tylko nazwy urządzenia (na przykład */dev/sdc1*). Jeśli system operacyjny wykryje błąd dysku podczas rozruchu, w przypadku używania identyfikatora UUID w danej lokalizacji nie zostanie zainstalowany nieprawidłowy dysk. Do pozostałych dysków danych zostałyby w takiej sytuacji przypisane te same identyfikatory urządzeń. Identyfikator UUID nowego dysku możesz znaleźć za pomocą narzędzia `blkid`:

```bash
sudo blkid
```

Dane wyjściowe wyglądają podobnie do poniższego przykładu:

```bash
/dev/sda1: LABEL="cloudimg-rootfs" UUID="11111111-1b1b-1c1c-1d1d-1e1e1e1e1e1e" TYPE="ext4" PARTUUID="1a1b1c1d-11aa-1234-1a1a1a1a1a1a"
/dev/sda15: LABEL="UEFI" UUID="BCD7-96A6" TYPE="vfat" PARTUUID="1e1g1cg1h-11aa-1234-1u1u1a1a1u1u"
/dev/sdb1: UUID="22222222-2b2b-2c2c-2d2d-2e2e2e2e2e2e" TYPE="ext4" TYPE="ext4" PARTUUID="1a2b3c4d-01"
/dev/sda14: PARTUUID="2e2g2cg2h-11aa-1234-1u1u1a1a1u1u"
/dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="xfs" PARTLABEL="xfspart" PARTUUID="c1c2c3c4-1234-cdef-asdf3456ghjk"
```

> [!NOTE]
> Niewłaściwe edytowanie pliku **/etc/fstab** może spowodować, że system nie zostanie rozruchowy. Jeśli nie masz pewności, jak to zrobić, sprawdź informacje na temat prawidłowego edytowania tego pliku w dokumentacji dystrybucji. Przed rozpoczęciem edycji zaleca się również utworzenie kopii zapasowej pliku/etc/fstab.

Następnie otwórz plik */etc/fstab* w edytorze tekstów w następujący sposób:

```bash
sudo nano /etc/fstab
```

W tym przykładzie Użyj wartości UUID dla `/dev/sdc1` urządzenia, które zostało utworzone w poprzednich krokach, i mountpoint `/datadrive` . Dodaj następujący wiersz na końcu `/etc/fstab` pliku:

```bash
UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   xfs   defaults,nofail   1   2
```

W tym przykładzie używamy edytora nano, więc po zakończeniu edytowania pliku Użyj polecenia, `Ctrl+O` Aby zapisać plik i `Ctrl+X` zamknąć Edytor.

> [!NOTE]
> Późniejsze usunięcie dysku z danymi bez edytowania fstab może spowodować niepowodzenie rozruchu maszyny wirtualnej. Większość dystrybucji oferuje opcje *nofail* i/lub *nobootwait* fstab. Te opcje umożliwiają rozruch systemu nawet wtedy, gdy instalacja dysku nie powiedzie się w czasie rozruchu. Aby uzyskać więcej informacji na temat tych parametrów, zapoznaj się z dokumentacją dystrybucji.
>
> Opcja *nofail* gwarantuje, że maszyna wirtualna jest uruchamiana, nawet jeśli system plików jest uszkodzony lub dysk nie istnieje w czasie rozruchu. Bez tej opcji może wystąpić sytuacja, zgodnie z opisem w artykule [nie można przeprowadzić protokołu SSH do maszyny wirtualnej z systemem Linux z powodu błędów fstab](/archive/blogs/linuxonazure/cannot-ssh-to-linux-vm-after-adding-data-disk-to-etcfstab-and-rebooting)
>
> Za pomocą konsoli szeregowej maszyny wirtualnej platformy Azure można uzyskać dostęp do maszyny wirtualnej, jeśli modyfikacja fstab spowodowała awarię rozruchu. Więcej szczegółów można znaleźć w [dokumentacji konsoli szeregowej](/troubleshoot/azure/virtual-machines/serial-console-linux).

### <a name="trimunmap-support-for-linux-in-azure"></a>Obsługa przycinania/mapowania dla systemu Linux na platformie Azure
Niektóre jądra systemu Linux obsługują operacje przycinania/mapowania do odrzucania nieużywanych bloków na dysku. Ta funkcja jest szczególnie przydatna w przypadku magazynu w warstwie Standardowa, aby poinformować platformę Azure, że usunięte strony nie są już prawidłowe i mogą być odrzucone i mogą zaoszczędzić pieniądze w przypadku tworzenia dużych plików, a następnie ich usuwania.

Istnieją dwa sposoby włączania obsługi przycinania na maszynie wirtualnej z systemem Linux. W zwykły sposób zapoznaj się z dystrybucją, aby uzyskać zalecane podejście:

* Użyj `discard` opcji instalacji w */etc/fstab*, na przykład:

    ```bash
    UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   xfs   defaults,discard   1   2
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

## <a name="troubleshooting"></a>Rozwiązywanie problemów

[!INCLUDE [virtual-machines-linux-lunzero](../../../includes/virtual-machines-linux-lunzero.md)]

## <a name="next-steps"></a>Następne kroki

* Aby upewnić się, że maszyna wirtualna z systemem Linux została prawidłowo skonfigurowana, przejrzyj zalecenia dotyczące [wydajności maszyny z systemem Linux](/previous-versions/azure/virtual-machines/linux/optimization) .
* Zwiększ pojemność magazynu, dodając dodatkowe dyski i [Skonfiguruj macierz RAID](/previous-versions/azure/virtual-machines/linux/configure-raid) pod kątem dodatkowej wydajności.