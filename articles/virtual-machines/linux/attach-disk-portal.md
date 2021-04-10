---
title: Dołączanie dysku danych do maszyny wirtualnej z systemem Linux
description: Użyj portalu, aby dołączyć nowy lub istniejący dysk danych do maszyny wirtualnej z systemem Linux.
author: cynthn
ms.service: virtual-machines
ms.topic: how-to
ms.date: 08/28/2020
ms.author: cynthn
ms.subservice: disks
ms.collection: linux
ms.openlocfilehash: 0fe584ea8559c285ee7e25caca958ff56aa9454d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104601847"
---
# <a name="use-the-portal-to-attach-a-data-disk-to-a-linux-vm"></a>Dołączanie dysku danych do maszyny wirtualnej z systemem Linux przy użyciu portalu 
W tym artykule opisano sposób dołączania nowych i istniejących dysków do maszyny wirtualnej z systemem Linux za pomocą Azure Portal. Możesz również [dołączyć dysk danych do maszyny wirtualnej z systemem Windows w Azure Portal](../windows/attach-managed-disk-portal.md). 

Przed dołączeniem dysków do maszyny wirtualnej zapoznaj się z następującymi wskazówkami:

* Rozmiar maszyny wirtualnej kontroluje liczbę dysków z danymi, które można dołączyć. Aby uzyskać szczegółowe informacje, zobacz [rozmiary maszyn wirtualnych](../sizes.md).
* Dyski dołączone do maszyn wirtualnych są w rzeczywistości plikami VHD przechowywanymi na platformie Azure. Aby uzyskać szczegółowe informacje, zobacz [wprowadzenie do usługi Managed disks](../managed-disks-overview.md).
* Po dołączeniu dysku musisz [nawiązać połączenie z maszyną wirtualną z systemem Linux, aby zainstalować nowy dysk](#connect-to-the-linux-vm-to-mount-the-new-disk).


## <a name="find-the-virtual-machine"></a>Znajdź maszynę wirtualną
1. Przejdź do [Azure Portal](https://portal.azure.com/) , aby znaleźć maszynę wirtualną. Wyszukaj i wybierz pozycję **maszyny wirtualne**.
2. Z listy wybierz maszynę wirtualną.
3. Na stronie **maszyny wirtualne** w obszarze **Ustawienia** wybierz pozycję **dyski**.


## <a name="attach-a-new-disk"></a>Dołącz nowy dysk

1. W okienku **dyski** w obszarze **dyski danych** wybierz pozycję **Utwórz i Dołącz nowy dysk**.

1. Wprowadź nazwę dla dysku zarządzanego. Sprawdź ustawienia domyślne i w razie potrzeby zaktualizuj **Typ magazynu**, **rozmiar (GIB)**, **szyfrowanie** i **buforowanie hosta** .
   
   :::image type="content" source="./media/attach-disk-portal/create-new-md.png" alt-text="Przejrzyj ustawienia dysku.":::


1. Gdy skończysz, wybierz pozycję **Zapisz** w górnej części strony, aby utworzyć dysk zarządzany i zaktualizować konfigurację maszyny wirtualnej.


## <a name="attach-an-existing-disk"></a>Dołączanie istniejącego dysku
1. W okienku **dyski** w obszarze **dyski danych** wybierz pozycję  **Dołącz istniejące dyski**.
1. Kliknij menu rozwijane dla **nazwy dysku** i wybierz dysk z listy dostępnych dysków zarządzanych. 

1. Kliknij przycisk **Zapisz** , aby dołączyć istniejący dysk zarządzany i zaktualizować konfigurację maszyny wirtualnej:
   

## <a name="connect-to-the-linux-vm-to-mount-the-new-disk"></a>Nawiązywanie połączenia z maszyną wirtualną z systemem Linux w celu zainstalowania nowego dysku
Aby podzielić na partycje, sformatować i zainstalować nowy dysk, aby maszyna wirtualna z systemem Linux mogła go używać, Użyj protokołu SSH do maszyny wirtualnej. Aby uzyskać więcej informacji, zobacz temat dotyczący [korzystania z protokołu SSH systemu Linux na platformie Azure](mac-create-ssh-keys.md). Poniższy przykład nawiązuje połączenie z maszyną wirtualną z publicznym adresem IP *10.123.123.25* z nazwą użytkownika *azureuser*: 

```bash
ssh azureuser@10.123.123.25
```

## <a name="find-the-disk"></a>Wyszukiwanie dysku

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
sdc     3:0:0:0       4G
```

W tym przykładzie dodany dysk jest `sdc` . Jest to numer LUN 0 i wynosi 4 GB.

Bardziej złożonego przykładu jest to, co wygląda na wiele dysków z danymi w portalu:

:::image type="content" source="./media/attach-disk-portal/find-disk.png" alt-text="Zrzut ekranu przedstawiający wiele dysków wyświetlanych w portalu.":::

Na obrazie można zobaczyć, że istnieją 3 dyski danych: 4 GB w jednostkach LUN 0, 16GB o numerze LUN 1 i 32G o numerze LUN 2.

Oto, co może wyglądać w następujący sposób `lsblk` :

```bash
sda     0:0:0:0      30G
├─sda1             29.9G /
├─sda14               4M
└─sda15             106M /boot/efi
sdb     1:0:1:0      14G
└─sdb1               14G /mnt
sdc     3:0:0:0       4G
sdd     3:0:0:1      16G
sde     3:0:0:2      32G
```

Na podstawie danych wyjściowych `lsblk` można zobaczyć, że dysk 4 GB o numerze LUN 0 to `sdc` dysk 16GB o numerze LUN 1 `sdd` , a dysk 32G w jednostce LUN 2 to `sde` .

### <a name="partition-a-new-disk"></a>Partycjonowanie nowego dysku

Jeśli używasz istniejącego dysku, który zawiera dane, Pomiń Instalowanie dysku. W przypadku dołączania nowego dysku należy podzielić dysk na partycje.

`parted`Narzędzie może służyć do partycjonowania i formatowania dysku danych.

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

Utwórz katalog służący do instalowania systemu plików przy użyciu programu `mkdir` . Poniższy przykład tworzy katalog w `/datadrive` :

```bash
sudo mkdir /datadrive
```

Służy `mount` do instalowania systemu plików. Poniższy przykład powoduje zainstalowanie partycji */dev/sdc1* w `/datadrive` punkcie instalacji:

```bash
sudo mount /dev/sdc1 /datadrive
```

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

Używamy edytora nano, więc po zakończeniu edytowania pliku Użyj polecenia, `Ctrl+O` Aby zapisać plik i `Ctrl+X` zamknąć Edytor.

> [!NOTE]
> Późniejsze usunięcie dysku z danymi bez edytowania fstab może spowodować niepowodzenie rozruchu maszyny wirtualnej. Większość dystrybucji oferuje opcje *nofail* i/lub *nobootwait* fstab. Te opcje umożliwiają rozruch systemu nawet wtedy, gdy instalacja dysku nie powiedzie się w czasie rozruchu. Aby uzyskać więcej informacji na temat tych parametrów, zapoznaj się z dokumentacją dystrybucji.
> 
> Opcja *nofail* gwarantuje, że maszyna wirtualna jest uruchamiana, nawet jeśli system plików jest uszkodzony lub dysk nie istnieje w czasie rozruchu. Bez tej opcji może wystąpić sytuacja, zgodnie z opisem w artykule [nie można przeprowadzić protokołu SSH do maszyny wirtualnej z systemem Linux z powodu błędów fstab](/archive/blogs/linuxonazure/cannot-ssh-to-linux-vm-after-adding-data-disk-to-etcfstab-and-rebooting)


## <a name="verify-the-disk"></a>Weryfikowanie dysku

Możesz teraz ponownie użyć, `lsblk` Aby zobaczyć dysk i mountpoint.

```bash
lsblk -o NAME,HCTL,SIZE,MOUNTPOINT | grep -i "sd"
```

Dane wyjściowe będą przypominać następujące:

```bash
sda     0:0:0:0      30G
├─sda1             29.9G /
├─sda14               4M
└─sda15             106M /boot/efi
sdb     1:0:1:0      14G
└─sdb1               14G /mnt
sdc     3:0:0:0       4G
└─sdc1                4G /datadrive
```

Zobaczysz, że `sdc` jest teraz zainstalowany w `/datadrive` .

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

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji i pomóc w rozwiązywaniu problemów z dyskiem, zobacz temat [Rozwiązywanie problemów z nazwami urządzeń maszyny wirtualnej z systemem Linux](/troubleshoot/azure/virtual-machines/troubleshoot-device-names-problems).

[Dysk danych](add-disk.md) można również dołączyć przy użyciu interfejsu wiersza polecenia platformy Azure.
