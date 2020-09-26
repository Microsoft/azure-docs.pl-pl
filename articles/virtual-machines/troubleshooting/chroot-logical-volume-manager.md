---
title: Odzyskiwanie maszyn wirtualnych z systemem Linux przy użyciu programu chroot, gdzie jest używany program LVM (Menedżer woluminów logicznych) — maszyny wirtualne platformy Azure
description: Odzyskiwanie maszyn wirtualnych z systemem Linux za pomocą LVMs.
services: virtual-machines-linux
documentationcenter: ''
author: vilibert
manager: spogge
editor: ''
tags: Linux chroot LVM
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 11/24/2019
ms.author: vilibert
ms.openlocfilehash: 98514bad6a04e0c3058faf3133fc44333039ce53
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91361470"
---
# <a name="troubleshooting-a-linux-vm-when-there-is-no-access-to-the-azure-serial-console-and-the-disk-layout-is-using-lvm-logical-volume-manager"></a>Rozwiązywanie problemów z maszyną wirtualną z systemem Linux w przypadku braku dostępu do konsoli szeregowej platformy Azure, a układ dysku używa LVM (Menedżer woluminów logicznych)

Ten przewodnik rozwiązywania problemów jest pomocny w przypadku scenariuszy, w których maszyna wirtualna z systemem Linux nie jest uruchamiana, protokół SSH nie jest możliwy i dla podstawowego układu systemu plików skonfigurowano LVM (Menedżer woluminów logicznych).

## <a name="take-snapshot-of-the-failing-vm"></a>Wykonaj migawkę maszyny wirtualnej, która kończy się niepowodzeniem

Utwórz migawkę powiązanej maszyny wirtualnej. 

Migawka zostanie następnie dołączona do **ratowniczej** maszyny wirtualnej. Postępuj zgodnie z instrukcjami w [tym miejscu](../linux/snapshot-copy-managed-disk.md#use-azure-portal) , aby zrobić **migawkę**.

## <a name="create-a-rescue-vm"></a>Tworzenie ratowniczej maszyny wirtualnej
Zwykle zalecana jest ratownicza maszyna wirtualna o tej samej lub podobnej wersji systemu operacyjnego. Użyj tego samego **regionu** i **grupy zasobów** maszyny wirtualnej, której to dotyczy

## <a name="connect-to-the-rescue-vm"></a>Nawiązywanie połączenia z maszyną wirtualną
Połącz się za pomocą protokołu SSH **z maszyną** wirtualną. Podnieś poziom uprawnień i Zostań administratorem

`sudo su -`

## <a name="attach-the-disk"></a>Dołącz dysk
Dołącz dysk do **ratowniczej** maszyny wirtualnej wykonanej wcześniej z migawki.

Azure Portal — > wybierz pozycję **łódź** VM-> **disks** 

![Utwórz dysk](./media/chroot-logical-volume-manager/create-disk-from-snap.png)

Wypełnij pola. Przypisz nazwę do nowego dysku, wybierz tę samą grupę zasobów co migawka, objętą maszyną wirtualną i ratowniczą maszynę wirtualną.

**Typ źródła** to **migawka** .
**Migawka źródłowa** to nazwa utworzonej wcześniej **migawki** .

![Utwórz dysk 2](./media/chroot-logical-volume-manager/create-disk-from-snap-2.png)

Utwórz punkt instalacji dołączonego dysku.

`mkdir /rescue`

Uruchom polecenie **fdisk-l** , aby sprawdzić, czy dysk migawki został dołączony i wyświetlić listę wszystkich dostępnych urządzeń i partycji

`fdisk -l`

W większości scenariuszy dołączony dysk migawki będzie widoczny jako **/dev/SDC** wyświetlający dwie partycje **/dev/sdc1** i **/dev/sdc2**

![Narzędzia](./media/chroot-logical-volume-manager/fdisk-output-sdc.png)

**\*** Wskazuje partycję rozruchową, obie partycje mają być zainstalowane.

Uruchom polecenie **lsblk** , aby wyświetlić LVMs maszyny wirtualnej, której to dotyczy

`lsblk`

![Uruchom lsblk](./media/chroot-logical-volume-manager/lsblk-output-mounted.png)


Sprawdź, czy są wyświetlane LVMs z danej maszyny wirtualnej.
Jeśli nie, Użyj poniższych poleceń, aby je włączyć i ponownie uruchomić **lsblk**.
Przed kontynuowaniem upewnij się, że LVMs z dołączonego dysku jest widoczny.

```
vgscan --mknodes
vgchange -ay
lvscan
mount –a
lsblk
```

Znajdź ścieżkę do zainstalowania woluminu logicznego, który zawiera partycję/(root). Zawiera pliki konfiguracji, takie jak/etc/default/grub

W tym przykładzie pobieranie danych wyjściowych z poprzedniego polecenia **lsblk**  **rootvg-rootlv** jest poprawnym **głównym** LV do zainstalowania i może być używane w następnym poleceniu.

W danych wyjściowych następnego polecenia zostanie wyświetlona ścieżka do instalacji dla **głównego** LV

`pvdisplay -m | grep -i rootlv`

![Rootlv](./media/chroot-logical-volume-manager/locate-rootlv.png)

Zainstaluj to urządzenie w katalogu/Rescue

`mount /dev/rootvg/rootlv /rescue`

Zainstaluj partycję, która ma ustawioną **flagę rozruchu** w/Rescue/Boot

`
mount /dev/sdc1 /rescue/boot
`

Sprawdź, czy systemy plików dołączonego dysku są teraz prawidłowo zainstalowane przy użyciu polecenia **lsblk**

![Uruchom lsblk](./media/chroot-logical-volume-manager/lsblk-output-1.png)

lub **DF-ty** polecenie

![DF](./media/chroot-logical-volume-manager/df-output.png)

## <a name="gaining-chroot-access"></a>Uzyskiwanie dostępu chroot

Uzyskaj dostęp **chroot** , który umożliwi wykonywanie różnych poprawek, istnieją niewielkie różnice dla każdej dystrybucji systemu Linux.

```
 cd /rescue
 mount -t proc proc proc
 mount -t sysfs sys sys/
 mount -o bind /dev dev/
 mount -o bind /dev/pts dev/pts/
 chroot /rescue
```

Jeśli wystąpi błąd, taki jak:

**chroot: nie można uruchomić polecenia "/bin/bash": Brak pliku lub katalogu**

Próba zainstalowania woluminu logicznego **usr**

`
mount  /dev/mapper/rootvg-usrlv /rescue/usr
`

> [!TIP]
> Podczas wykonywania poleceń w środowisku **chroot** należy zauważyć, że są one uruchamiane na dołączonym dysku systemu operacyjnego, a nie **na lokalnej maszynie** wirtualnej. 

Polecenia mogą służyć do instalowania, usuwania i aktualizacji oprogramowania. Rozwiązywanie problemów z maszynami wirtualnymi w celu naprawienia błędów.


Wykonaj polecenie lsblk, a/Rescue jest teraz/,/Rescue/Boot to/boot ![ zrzut ekranu przedstawia okno konsoli z poleceniem l s BLK i jego drzewem wyjściowym.](./media/chroot-logical-volume-manager/chrooted.png)

## <a name="perform-fixes"></a>Wykonaj poprawki

### <a name="example-1---configure-the-vm-to-boot-from-a-different-kernel"></a>Przykład 1 — Konfigurowanie maszyny wirtualnej do rozruchu z innego jądra

Typowym scenariuszem jest wymuszenie rozruchu maszyny wirtualnej z poprzedniego jądra, ponieważ bieżące zainstalowane jądro mogło ulec uszkodzeniu lub uaktualnienie nie zostało prawidłowo ukończone.


```
cd /boot/grub2

grep -i linux grub.cfg

grub2-editenv list

grub2-set-default "CentOS Linux (3.10.0-1062.1.1.el7.x86_64) 7 (Core)"

grub2-editenv list

grub2-mkconfig -o /boot/grub2/grub.cfg
```

*Instruktaż*

Polecenie **grep** wyświetla listę jądra, które są świadome **grub. cfg** .
![Zrzut ekranu przedstawia okno konsoli z wynikami wyszukiwania GREP dla jądra.](./media/chroot-logical-volume-manager/kernels.png)

**grub2-editenv wyświetla listę** , które jądro zostanie załadowane przy następnym rozruchu ![ jądra domyślnego](./media/chroot-logical-volume-manager/kernel-default.png)

**grub2-set-default** służy do zmiany do innego ![ zestawu grub2 jądra](./media/chroot-logical-volume-manager/grub2-set-default.png)

**grub2-editenv** wyświetla listę, które jądro zostanie załadowane przy następnym rozruchowym ![ nowym jądrem](./media/chroot-logical-volume-manager/kernel-new.png)

**grub2-mkconfig** ponownie kompiluje grub. cfg przy użyciu wersji wymaganych ![ grub2 mkconfig](./media/chroot-logical-volume-manager/grub2-mkconfig.png)



### <a name="example-2---upgrade-packages"></a>Przykład 2 — pakiety uaktualniające

Niepowodzenie uaktualnienia jądra może spowodować, że maszyna wirtualna nie jest rozruchowa.
Zainstaluj wszystkie woluminy logiczne, aby zezwolić na usunięcie lub ponowne zainstalowanie pakietów

Uruchom polecenie **LVS** , aby sprawdzić, które **LVS** są dostępne do zainstalowania, każda maszyna wirtualna, która została zmigrowana lub pochodzi od innego dostawcy chmury, różni się w zależności od konfiguracji.

Wyjdź z środowiska **chroot** Zainstaluj wymagane **LV**

![Zrzut ekranu przedstawia okno konsoli z poleceniem l-s, a następnie zainstalowanie L V.](./media/chroot-logical-volume-manager/advanced.png)

Teraz ponownie Uzyskuj dostęp do środowiska **chroot** , uruchamiając

`chroot /rescue`

Wszystkie LVs powinny być widoczne jako zainstalowane partycje

![Zaawansowany](./media/chroot-logical-volume-manager/chroot-all-mounts.png)

Wykonywanie zapytania dotyczącego zainstalowanego **jądra**

![Zaawansowany](./media/chroot-logical-volume-manager/rpm-kernel.png)

W razie konieczności Usuń lub Uaktualnij **jądro** 
 ![ Zaawansowane](./media/chroot-logical-volume-manager/rpm-remove-kernel.png)


### <a name="example-3---enable-serial-console"></a>Przykład 3 — Włączanie konsoli szeregowej
Jeśli nie można uzyskać dostępu do konsoli szeregowej platformy Azure, sprawdź parametry konfiguracji GRUB dla maszyny wirtualnej z systemem Linux i popraw je. Szczegółowe informacje można znaleźć [w tym dokumencie](./serial-console-grub-proactive-configuration.md)

### <a name="example-4---kernel-loading-with-problematic-lvm-swap-volume"></a>Przykład 4 — ładowanie jądra ze problematycznym woluminem wymiany LVM

Maszyna wirtualna może się nie powieść w pełni rozruch i zostanie wyświetlony monit **Dracut** .
Więcej szczegółów dotyczących awarii można znaleźć w konsoli szeregowej platformy Azure lub przejdź do Azure Portal-> Boot Diagnostics-> log


Może wystąpić błąd podobny do tego:

```
[  188.000765] dracut-initqueue[324]: Warning: /dev/VG/SwapVol does not exist
         Starting Dracut Emergency Shell...
Warning: /dev/VG/SwapVol does not exist
```

W tym przykładzie skonfigurowano grub. cfg, aby załadować wartość LV o nazwie **Rd. LVM. lv = VG/SwapVol** , a maszyna wirtualna nie może jej zlokalizować. Ten wiersz przedstawia sposób ładowania jądra odwołującego się do SwapVol LV

```
[    0.000000] Command line: BOOT_IMAGE=/vmlinuz-3.10.0-1062.4.1.el7.x86_64 root=/dev/mapper/VG-OSVol ro console=tty0 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0 biosdevname=0 crashkernel=256M rd.lvm.lv=VG/OSVol rd.lvm.lv=VG/SwapVol nodmraid rhgb quiet
[    0.000000] e820: BIOS-provided physical RAM map:
```

 Usuń problemy z problemami z konfiguracją/etc/default/grub i Skompiluj ponownie grub2. cfg


## <a name="exit-chroot-and-swap-the-os-disk"></a>Wyjdź z chroot i Zamień dysk systemu operacyjnego

Po naprawieniu problemu Kontynuuj Odinstalowywanie i odłączaj dysk od ratowniczej maszyny wirtualnej, co pozwoli na zamianę na dysk systemu operacyjnego maszyny wirtualnej.

```
exit
cd /
umount /rescue/proc/
umount /rescue/sys/
umount /rescue/dev/pts
umount /rescue/dev/
umount /rescue/boot
umount /rescue
```

Odłącz dysk od ratowniczej maszyny wirtualnej i przeprowadź wymianę dysków.

Wybierz maszynę wirtualną z **dysków** portalu i wybierz pozycję **Odłącz** 
 ![ Odłączanie dysku](./media/chroot-logical-volume-manager/detach-disk.png) 

Zapisz zmiany, a następnie ![ Zapisz](./media/chroot-logical-volume-manager/save-detach.png) 

Dysk będzie teraz dostępny, umożliwiając jego zamianę na oryginalny dysk systemu operacyjnego maszyny wirtualnej, której to dotyczy.

Przejdź do Azure Portal do maszyny wirtualnej, która kończy się niepowodzeniem, a następnie wybierz pozycję **dyski**  ->  **Zamień dysk wymiany dysku systemu operacyjnego** 
 ![](./media/chroot-logical-volume-manager/swap-disk.png) 

Wypełnij pola ten dysk **jest odłączony od razu** w poprzednim kroku. Nazwa maszyny wirtualnej, której dotyczy ta maszyna wirtualna, jest również wymagana, a następnie wybierz przycisk **OK** .

![Nowy dysk systemu operacyjnego](./media/chroot-logical-volume-manager/new-osdisk.png) 

Jeśli maszyna wirtualna jest uruchomiona, wymiana dysków zostanie wyłączona, należy ponownie uruchomić maszynę wirtualną po zakończeniu operacji wymiany dysku.


## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej o

 [Konsola szeregowa platformy Azure]( ./serial-console-linux.md)

[Tryb pojedynczego użytkownika](./serial-console-grub-single-user-mode.md)