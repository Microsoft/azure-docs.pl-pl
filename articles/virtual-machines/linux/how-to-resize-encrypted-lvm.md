---
title: Jak zmienić rozmiar zaszyfrowanych dysków zarządzania woluminami logicznymi przy użyciu Azure Disk Encryption
description: Ten artykuł zawiera instrukcje dotyczące zmiany rozmiarów szyfrowanych dysków ADE przy użyciu zarządzania woluminami logicznymi
author: jofrance
ms.service: security
ms.topic: article
ms.author: jofrance
ms.date: 09/21/2020
ms.openlocfilehash: ba652b9424b8d5ce1b6a2c5b7d70b8fd9e999323
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91346315"
---
# <a name="how-to-resize-logical-volume-management-devices-encrypted-with-azure-disk-encryption"></a>Jak zmienić rozmiar urządzeń do zarządzania woluminami logicznymi zaszyfrowanymi za pomocą Azure Disk Encryption

W tym artykule przedstawiono krok po kroku proces zmieniania rozmiaru szyfrowanych dysków danych w systemie LVM przy użyciu funkcji zarządzania woluminami logicznymi systemu Linux, które są stosowane do wielu scenariuszy.

Ten proces ma zastosowanie do następujących środowisk:

- Dystrybucje systemu Linux
    - RHEL 7+
    - Ubuntu 16 +
    - SUSE 12 +
- Azure Disk Encryption rozszerzenie z jednym przebiegiem
- Azure Disk Encryption rozszerzenie o podwójnej przejściu

## <a name="considerations"></a>Zagadnienia do rozważenia

W tym dokumencie przyjęto założenie, że:

1. Istnieje już istniejąca konfiguracja LVM.
   
   Aby uzyskać więcej informacji na temat konfigurowania LVM na maszynie wirtualnej z systemem Linux, zaznacz opcję [Konfiguruj LVM na maszynie wirtualnej z systemem Linux](configure-lvm.md) .

2. Dyski są już zaszyfrowane za pomocą Azure Disk Encryption zaznacz opcję [Konfiguruj LVM on Crypt](how-to-configure-lvm-raid-on-crypt.md) , aby uzyskać informacje na temat konfigurowania LVM-on-Crypt.

3. Aby skorzystać z tych przykładów, masz wymaganą wiedzę dla systemów Linux i LVM.

4. Rozumiesz, że zaleca się używanie dysków danych na platformie Azure, jak wspomniano w [rozwiązywaniu problemów z nazwami urządzeń](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-device-names-problems), jest użycie ścieżek/dev/Disk/scsi1/.

## <a name="scenarios"></a>Scenariusze

Procedury opisane w tym artykule mają zastosowanie do następujących scenariuszy:

### <a name="for-traditional-lvm-and-lvm-on-crypt-configurations"></a>Dla tradycyjnych LVM i LVM-on-Crypt Configurations

- Rozszerzanie woluminu logicznego, gdy jest dostępne miejsce w VG

### <a name="for-traditional-lvm-encryption-the-logical-volumes-are-encrypted-not-the-whole-disk"></a>Dla tradycyjnego szyfrowania LVM (woluminy logiczne są szyfrowane, a nie cały dysk)

- Rozszerzanie tradycyjnego woluminu LVM Dodawanie nowej funkcji PV
- Rozszerzenie tradycyjnego woluminu LVM zmieniającego rozmiar istniejącej funkcji PV

### <a name="for-lvm-on-crypt-recommended-method-the-entire-disk-is-encrypted-not-only-the-logical-volume"></a>Dla LVM-on-Crypt (zalecana metoda, cały dysk jest szyfrowany, a nie tylko wolumin logiczny)

- Rozszerzanie LVM na woluminie Crypt Dodawanie nowej funkcji PV
- Rozszerzanie LVM na woluminie Crypt zmienianie rozmiarów istniejącej funkcji PV

> [!NOTE]
> Mieszanie tradycyjnego szyfrowania LVM i LVM-on-Crypt na tej samej maszynie wirtualnej nie jest zalecane.

> [!NOTE]
> W poniższych przykładach użyto istniejących nazw dysków, woluminów fizycznych, grup woluminów, woluminów logicznych, systemów plików, identyfikatorów UUID i mountpoints, należy zastąpić wartości podane w tych przykładach, aby dopasować je do środowiska.

#### <a name="extending-a-logical-volume-when-theres-available-space-in-the-vg"></a>Rozszerzanie woluminu logicznego, gdy jest dostępne miejsce w VG

Tradycyjna metoda używana do zmiany rozmiaru woluminów logicznych może być stosowana do dysków nieszyfrowanych, tradycyjnych LVM szyfrowanych woluminów oraz konfiguracji LVM-on-Crypt.

1. Sprawdź bieżący rozmiar systemu plików, który chcemy zwiększyć:

    ``` bash
    df -h /mountpoint
    ```

    ![scenariusz-Check-FS1](./media/disk-encryption/resize-lvm/001-resize-lvm-scenarioa-check-fs.png)

2. Sprawdź, czy VG ma wystarczającą ilość miejsca, aby zwiększyć wartość LV

    ``` bash
    vgs
    ```

    ![scenariusz-Check-VG](./media/disk-encryption/resize-lvm/002-resize-lvm-scenarioa-check-vgs.png)

    Można również użyć "vgdisplay"

    ``` bash
    vgdisplay vgname
    ```

    ![scenariusz-Check-vgdisplay](./media/disk-encryption/resize-lvm/002-resize-lvm-scenarioa-check-vgdisplay.png)

3. Określ, którego rozmiaru woluminu logicznego należy zmienić

    ``` bash
    lsblk
    ```

    ![scenariusz-Check-lsblk1](./media/disk-encryption/resize-lvm/002-resize-lvm-scenarioa-check-lsblk1.png)

    W przypadku LVM-on-Crypt różnica dotyczy tych danych wyjściowych, co oznacza, że zaszyfrowana warstwa jest umieszczona na zaszyfrowanej warstwie, obejmującej cały dysk

    ![scenariusz-Check-lsblk2](./media/disk-encryption/resize-lvm/002-resize-lvm-scenarioa-check-lsblk2.png)

4. Sprawdź rozmiar woluminu logicznego

    ``` bash
    lvdisplay lvname
    ```

    ![scenariusz-Check-lvdisplay01](./media/disk-encryption/resize-lvm/002-resize-lvm-scenarioa-check-lvdisplay01.png)

5. Zwiększ rozmiar LV przy użyciu "-r" dla zmiany rozmiaru w trybie online systemu plików

    ``` bash
    lvextend -r -L +2G /dev/vgname/lvname
    ```

    ![scenariusz a — zmiana rozmiaru — LV](./media/disk-encryption/resize-lvm/003-resize-lvm-scenarioa-resize-lv.png)

6. Sprawdź nowe rozmiary dla programu LV i systemu plików

    ``` bash
    df -h /mountpoint
    ```

    ![scenariusz i-check-FS](./media/disk-encryption/resize-lvm/004-resize-lvm-scenarioa-check-fs.png)

    Nowy rozmiar jest odzwierciedlony, co wskazuje na pomyślne zmianę rozmiaru wartości LV i systemu plików

7. Możesz ponownie sprawdzić informacje o LV, aby potwierdzić zmiany na poziomie LV

    ``` bash
    lvdisplay lvname
    ```

    ![scenariusz-Check-lvdisplay2](./media/disk-encryption/resize-lvm/004-resize-lvm-scenarioa-check-lvdisplay2.png)

#### <a name="extending-a-traditional-lvm-volume-adding-a-new-pv"></a>Rozszerzanie tradycyjnego woluminu LVM Dodawanie nowej funkcji PV

Zastosowanie, gdy konieczne jest dodanie nowego dysku w celu zwiększenia rozmiaru grupy woluminów.

1. Sprawdź bieżący rozmiar systemu plików, który chcemy zwiększyć:

    ``` bash
    df -h /mountpoint
    ```

    ![scenariob-Check-FS](./media/disk-encryption/resize-lvm/005-resize-lvm-scenariob-check-fs.png)

2. Sprawdź bieżącą konfigurację woluminu fizycznego

    ``` bash
    pvs
    ```

    ![scenariob-Check-PVS](./media/disk-encryption/resize-lvm/006-resize-lvm-scenariob-check-pvs.png)

3. Sprawdź bieżące informacje VG

    ``` bash
    vgs
    ```

    ![scenariob-Check-VGS](./media/disk-encryption/resize-lvm/007-resize-lvm-scenariob-check-vgs.png)

4. Sprawdź bieżącą listę dysków

    Dyski danych powinny być identyfikowane przez sprawdzenie urządzeń w obszarze/dev/Disk/Azure/scsi1/

    ``` bash
    ls -l /dev/disk/azure/scsi1/
    ```

    ![scenariob-Check-scs1](./media/disk-encryption/resize-lvm/008-resize-lvm-scenariob-check-scs1.png)

5. Sprawdź dane wyjściowe lsblk 

    ``` bash
    lsbk
    ```

    ![scenariob-Check-lsblk](./media/disk-encryption/resize-lvm/008-resize-lvm-scenariob-check-lsblk.png)

6. Dołącz nowy dysk do maszyny wirtualnej

    Postępuj zgodnie z krokami 4 następującego dokumentu

   - [Dołączanie dysku do maszyny wirtualnej](attach-disk-portal.md)

7. Po dołączeniu dysku zapoznaj się z listą dysków, aby zauważyć, że nowy dysk

    ``` bash
    ls -l /dev/disk/azure/scsi1/
    ```

    ![scenariob-Check-scsi12](./media/disk-encryption/resize-lvm/009-resize-lvm-scenariob-check-scsi12.png)

    ``` bash
    lsbk
    ```

    ![scenariob-Check-lsblk12](./media/disk-encryption/resize-lvm/009-resize-lvm-scenariob-check-lsblk1.png)

8. Utwórz nową wartość PV na nowym dysku z danymi

    ``` bash
    pvcreate /dev/newdisk
    ```

    ![scenariob-pvcreate](./media/disk-encryption/resize-lvm/010-resize-lvm-scenariob-pvcreate.png)

    Ta metoda używa całego dysku jako PV bez partycji, opcjonalnie można użyć "fdisk", aby utworzyć partycję, a następnie użyć tej partycji dla "pvcreate".

9. Sprawdź, czy WB została dodana do listy PV.

    ``` bash
    pvs
    ```

    ![scenariob-Check-pvs1](./media/disk-encryption/resize-lvm/011-resize-lvm-scenariob-check-pvs1.png)

10. Zwiększ VG, dodając do niej nowe WB

    ``` bash
    vgextend vgname /dev/newdisk
    ```

    ![scenariob-VG-rozszerzając](./media/disk-encryption/resize-lvm/012-resize-lvm-scenariob-vgextend.png)

11. Sprawdź nowy rozmiar VG

    ``` bash
    vgs
    ```

    ![scenariob-Check-vgs1](./media/disk-encryption/resize-lvm/013-resize-lvm-scenariob-check-vgs1.png)

12. Użyj lsblk, aby określić, które LV należy zmienić rozmiaru

    ``` bash
    lsblk
    ```

    ![scenariob-Check-lsblk1](./media/disk-encryption/resize-lvm/013-resize-lvm-scenariob-check-lsblk1.png)

13. Rozszerzanie rozmiaru LV przy użyciu "-r" w celu zwiększenia możliwości online systemu plików

    ``` bash
    lvextend -r -L +2G /dev/vgname/lvname
    ```

    ![scenariob-lvextend](./media/disk-encryption/resize-lvm/013-resize-lvm-scenariob-lvextend.png) 

14. Weryfikowanie nowych rozmiarów Łotwy i systemu plików

    ``` bash
    df -h /mountpoint
    ```

    ![scenariob-Check-FS1](./media/disk-encryption/resize-lvm/014-resize-lvm-scenariob-check-fs1.png)

    Należy pamiętać, że gdy w tradycyjnych konfiguracjach LVM jest używany program ADE, zaszyfrowana warstwa zostanie utworzona na poziomie LV, a nie na poziomie dysku.

    Na tym etapie zaszyfrowana warstwa zostanie rozszerzona na nowy dysk.
    Rzeczywisty dysk danych nie ma żadnych ustawień szyfrowania na poziomie platformy, dlatego jego stan szyfrowania nie jest aktualizowany.

    >[!NOTE]
    >Oto niektóre z powodów, dla których zaleca się zastosowanie LVM-on-Crypt. 

15. Sprawdź informacje o szyfrowaniu z portalu:

    ![scenariob-Check-portal1](./media/disk-encryption/resize-lvm/014-resize-lvm-scenariob-check-portal1.png)

    Należy dodać nowe LV i włączyć rozszerzenie na maszynie wirtualnej w celu zaktualizowania ustawień szyfrowania na dysku.
    
16. Dodaj nową wartość LV, Utwórz na niej system plików i Dodaj ją do/etc/fstab

17. Ponownie ustaw rozszerzenie szyfrowania w celu sygnatury ustawień szyfrowania na nowym dysku danych na poziomie platformy.

    Przykład:

    Interfejs wiersza polecenia

    ``` bash
    az vm encryption enable -g ${RGNAME} --name ${VMNAME} --disk-encryption-keyvault "<your-unique-keyvault-name>"
    ```

18. Sprawdź informacje o szyfrowaniu z portalu:

    ![scenariob-Check-portal2](./media/disk-encryption/resize-lvm/014-resize-lvm-scenariob-check-portal2.png)

19. Po zaktualizowaniu ustawień szyfrowania można usunąć nowe LV, należy również usunąć wpis z/etc/fstab i/etc/crypttab, które zostały utworzone dla tego programu.

    ![scenariob-Delete-fstab-crypttab](./media/disk-encryption/resize-lvm/014-resize-lvm-scenariob-delete-fstab-crypttab.png)

20. Odinstalowywanie woluminu logicznego

    ``` bash
    umount /mountpoint
    ```

21. Zamknij zaszyfrowaną warstwę woluminu

    ``` bash
    cryptsetup luksClose /dev/vgname/lvname
    ```

22. Usuń wartość LV

    ``` bash
    lvremove /dev/vgname/lvname
    ```

#### <a name="extending-a-traditional-lvm-volume-resizing-an-existing-pv"></a>Rozszerzenie tradycyjnego woluminu LVM zmieniającego rozmiar istniejącej funkcji PV

Niektóre scenariusze lub ograniczenia wymagają zmiany rozmiaru istniejącego dysku.

1. Identyfikowanie zaszyfrowanych dysków

    ``` bash
    ls -l /dev/disk/azure/scsi1/
    ```

    ![scenarioc-Check-scsi1](./media/disk-encryption/resize-lvm/015-resize-lvm-scenarioc-check-scsi1.png)

    ``` bash
    lsblk -fs
    ```

    ![scenarioc-Check-lsblk](./media/disk-encryption/resize-lvm/015-resize-lvm-scenarioc-check-lsblk.png)

2. Sprawdź informacje o usłudze PV

    ``` bash
    pvs
    ```

    ![scenarioc-Check-PVS](./media/disk-encryption/resize-lvm/016-resize-lvm-scenarioc-check-pvs.png)

    Wszystkie miejsca na wszystkich PVs są obecnie używane

3. Sprawdź informacje o VGs

    ``` bash
    vgs
    vgdisplay -v vgname
    ```

    ![scenarioc-Check-VGS](./media/disk-encryption/resize-lvm/017-resize-lvm-scenarioc-check-vgs.png)

4. Sprawdź rozmiary dysków, aby wyświetlić listę rozmiarów dysków, można użyć narzędzia Fdisk lub lsblk

    ``` bash
    for disk in `ls -l /dev/disk/azure/scsi1/* | awk -F/ '{print $NF}'` ; do echo "fdisk -l /dev/${disk} | grep ^Disk "; done | bash

    lsblk -o "NAME,SIZE"
    ```

    ![scenarioc-The-fdisk](./media/disk-encryption/resize-lvm/018-resize-lvm-scenarioc-check-fdisk.png)

    Zidentyfikowano, które PVs są skojarzone z LVs przy użyciu lsblk-FS, można je zidentyfikować również, uruchamiając polecenie "lvdisplay"

    ``` bash
    lvdisplay --maps VG/LV
    lvdisplay --maps datavg/datalv1
    ```

    ![Check-lvdisplay](./media/disk-encryption/resize-lvm/019-resize-lvm-scenarioc-check-lvdisplay.png)

    W tym konkretnym przypadku wszystkie 4 dyski danych są częścią tego samego VG i jednego LV, ale konfiguracja może się różnić od tego przykładu.

5. Sprawdź bieżące użycie systemu plików:

    ``` bash
    df -h /datalvm*
    ```

    ![scenarioc-Check-DF](./media/disk-encryption/resize-lvm/020-resize-lvm-scenarioc-check-df.png)

6. Zmień rozmiar dysków danych:

    Możesz odwoływać się do [dysków rozbudowy systemu Linux](expand-disks.md) (tylko do zmiany rozmiaru dysku), możesz użyć portalu, interfejsu wiersza polecenia lub programu PowerShell, aby wykonać ten krok.

    >[!NOTE]
    >Nie można wykonać operacji zmiany rozmiaru na dyskach wirtualnych z uruchomioną maszyną wirtualną. Musisz cofnąć przydział maszyny wirtualnej dla tego kroku

7. Po zmianie rozmiaru dysków na wymaganą wartość Uruchom maszynę wirtualną i sprawdź nowe rozmiary przy użyciu narzędzia Fdisk

    ``` bash
    for disk in `ls -l /dev/disk/azure/scsi1/* | awk -F/ '{print $NF}'` ; do echo "fdisk -l /dev/${disk} | grep ^Disk "; done | bash

    lsblk -o "NAME,SIZE"
    ```

    ![scenarioc-Check-fdisk1](./media/disk-encryption/resize-lvm/021-resize-lvm-scenarioc-check-fdisk1.png)

    W tym konkretnym przypadku rozmiar/dev/SDD został zmieniony z 5G na 20G

8. Sprawdź bieżący rozmiar PV

    ``` bash
    pvdisplay /dev/resizeddisk
    ```

    ![scenarioc-Check-pvdisplay](./media/disk-encryption/resize-lvm/022-resize-lvm-scenarioc-check-pvdisplay.png)
    
    Nawet w przypadku zmiany rozmiaru dysku funkcja PV nadal ma poprzedni rozmiar.

9. Zmień rozmiar PV

    ``` bash
    pvresize /dev/resizeddisk
    ```

    ![scenarioc-Check-pvresize](./media/disk-encryption/resize-lvm/023-resize-lvm-scenarioc-check-pvresize.png)


10. Sprawdź rozmiar PV

    ``` bash
    pvdisplay /dev/resizeddisk
    ```

    ![scenarioc-Check-pvdisplay1](./media/disk-encryption/resize-lvm/024-resize-lvm-scenarioc-check-pvdisplay1.png)

    Zastosuj tę samą procedurę dla wszystkich dysków, dla których chcesz zmienić rozmiar.

11. Sprawdź informacje o VG

    ``` bash
    vgdisplay vgname
    ```

    ![scenarioc-Check-vgdisplay1](./media/disk-encryption/resize-lvm/025-resize-lvm-scenarioc-check-vgdisplay1.png)

    VG ma teraz miejsce do przydzielenia do LVs

12. Zmień rozmiar LV

    ``` bash
    lvresize -r -L +5G vgname/lvname
    lvresize -r -l +100%FREE /dev/datavg/datalv01
    ```

    ![scenarioc-Check-lvresize1](./media/disk-encryption/resize-lvm/031-resize-lvm-scenarioc-check-lvresize1.png)

13. Sprawdzanie rozmiaru usług FS

    ``` bash
    df -h /datalvm2
    ```

    ![scenarioc-Check-DF3](./media/disk-encryption/resize-lvm/032-resize-lvm-scenarioc-check-df3.png)

#### <a name="extending-an-lvm-on-crypt-volume-adding-a-new-pv"></a>Rozszerzanie woluminu LVM na Crypt Dodawanie nowej funkcji PV

Aby dodać nowy dysk i skonfigurować go w konfiguracji LVM-on-Crypt, należy dokładnie wykonać kroki opisane w sekcji [Konfigurowanie LVM na Crypt](how-to-configure-lvm-raid-on-crypt.md) .

Możesz użyć tej metody, aby dodać miejsce do już istniejącej LV lub zamiast tego można utworzyć nowe VGs lub LVs.

1. Sprawdź bieżący rozmiar VG

    ``` bash
    vgdisplay vgname
    ```

    ![scenariusz — Check-vg01](./media/disk-encryption/resize-lvm/033-resize-lvm-scenarioe-check-vg01.png)

2. Sprawdź rozmiar usług FS i LV, które chcesz zwiększyć

    ``` bash
    lvdisplay /dev/vgname/lvname
    ```

    ![scenariusz — Check-lv01](./media/disk-encryption/resize-lvm/034-resize-lvm-scenarioe-check-lv01.png)

    ``` bash
    df -h mountpoint
    ```

    ![scenariusz — Check-fs01](./media/disk-encryption/resize-lvm/034-resize-lvm-scenarioe-check-fs01.png)

3. Dodaj nowy dysk danych do maszyny wirtualnej i zidentyfikuj go.

    Przed dodaniem dysku Sprawdź dyski

    ``` bash
    fdisk -l | egrep ^"Disk /"
    ```

    ![scenariusz — Check-newdisk01](./media/disk-encryption/resize-lvm/035-resize-lvm-scenarioe-check-newdisk01.png)

    Sprawdź dyski przed dodaniem nowego dysku

    ``` bash
    lsblk
    ```

    ![scenariusz — Check-newdisk002](./media/disk-encryption/resize-lvm/035-resize-lvm-scenarioe-check-newdisk02.png)

    Dodaj nowy dysk przy użyciu programu PowerShell, interfejsu wiersza polecenia platformy Azure lub Azure Portal. Sprawdź, jak [dołączyć dysk,](attach-disk-portal.md) Aby uzyskać informacje na temat dodawania dysków do maszyny wirtualnej.

    Zgodnie ze schematem nazwy jądra dla urządzeń, do nowego dysku zwykle przypisano następną dostępną literę, na tym konkretnym przykładzie nowy dodany dysk jest SDD.

4. Sprawdź dyski po dodaniu nowego dysku

    ``` bash
    fdisk -l | egrep ^"Disk /"
    ```

    ![scenariusz — Check-newdisk02](./media/disk-encryption/resize-lvm/036-resize-lvm-scenarioe-check-newdisk02.png)-

    ``` bash
    lsblk
    ```

    ![scenariusz — Check-newdisk003](./media/disk-encryption/resize-lvm/036-resize-lvm-scenarioe-check-newdisk03.png)

5. Utwórz system plików na początku niedawno dodanego dysku

    Dopasuj ostatnio dodany dysk do połączonych urządzeń w/dev/Disk/Azure/scsi1/

    ``` bash
    ls -la /dev/disk/azure/scsi1/
    ```

    ![scenariusz — Check-newdisk03](./media/disk-encryption/resize-lvm/037-resize-lvm-scenarioe-check-newdisk03.png)

    ``` bash
    mkfs.ext4 /dev/disk/azure/scsi1/${disk}
    ```

    ![scenariusz — mkfs01](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-mkfs01.png)

6. Utwórz nowy mountpoint temp dla nowego dodanego dysku

    ``` bash
    newmount=/data4
    mkdir ${newmount}
    ```

7. Dodaj ostatnio utworzony system plików do/etc/fstab

    ``` bash
    blkid /dev/disk/azure/scsi1/lun4| awk -F\" '{print "UUID="$2" '${newmount}' "$4" defaults,nofail 0 0"}' >> /etc/fstab
    ```

8. Instalowanie nowych utworzonych usług FS przy użyciu instalacji-a

    ``` bash
    mount -a
    ```

9. Sprawdź, czy dodano nowe zainstalowane FS

    ``` bash
    df -h
    ```

    ![Zmiana rozmiaru-LVM-scenariusz-DF](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-df.png)

    ``` bash
    lsblk
    ```

    ![Zmiana rozmiaru-LVM-scenariusz-lsblk](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lsblk.png)

10. Uruchom ponownie szyfrowanie wcześniej uruchomione dla dysków danych

    W przypadku LVM na Crypt zaleca się użycie EncryptFormatAll w przeciwnym razie może wystąpić podwójne szyfrowanie podczas ustawiania dodatkowych dysków.

    Aby uzyskać informacje na temat użycia, zobacz [Konfigurowanie LVM na Crypt](how-to-configure-lvm-raid-on-crypt.md).

    Przykład:

    ``` bash
    az vm encryption enable \
    --resource-group ${RGNAME} \
    --name ${VMNAME} \
    --disk-encryption-keyvault ${KEYVAULTNAME} \
    --key-encryption-key ${KEYNAME} \
    --key-encryption-keyvault ${KEYVAULTNAME} \
    --volume-type "DATA" \
    --encrypt-format-all \
    -o table
    ```

    Po zakończeniu szyfrowania zostanie wyświetlona warstwa Crypt na nowo dodanym dysku

    ``` bash
    lsblk
    ```

    ![scenariusz — lsblk2](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lsblk2.png)

11. Odinstaluj zaszyfrowaną warstwę nowego dysku

    ``` bash
    umount ${newmount}
    ```

12. Sprawdź bieżące informacje PVS

    ``` bash
    pvs
    ```

    ![scenariusz — currentpvs](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-currentpvs.png)

13. Utwórz wa na zaszyfrowanej warstwie dysku

    Pochwyć nazwę urządzenia z poprzedniego polecenia lsblk i Dodaj/dev/mapper przed nazwą urządzenia, aby utworzyć WB

    ``` bash
    pvcreate /dev/mapper/mapperdevicename
    ```

    ![scenariusz — pvcreate](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-pvcreate.png)

    Zostanie wyświetlone ostrzeżenie o czyszczeniu bieżącej sygnatury ext4 FS. jest to oczekiwane na odpowiedź y na to pytanie

14. Sprawdź, czy nowa funkcja PV została dodana do konfiguracji LVM

    ``` bash
    pvs
    ```

    ![scenariusz — newpv](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-newpv.png)

15. Dodaj nową wartość PV do VG, którą chcesz zwiększyć

    ``` bash
    vgextend vgname /dev/mapper/nameofhenewpv
    ```

    ![scenariusz — vgextent](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-vgextent.png)

16. Sprawdź nowy rozmiar i wolne miejsce VG

    ``` bash
    vgdisplay vgname
    ```

    ![scenariusz — vgdisplay](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-vgdisplay.png)

    Zwróć uwagę na zwiększenie łącznej liczby PE i wolnego środowiska PE/rozmiaru

17. Zwiększenie rozmiaru wartości LV i systemu plików przy użyciu opcji-r w systemie lvextend (w tym przykładzie zajmują się całkowitą ilością dostępnego miejsca w VG i dodaniem go do danego woluminu logicznego)

    ``` bash
    lvextend -r -l +100%FREE /dev/vgname/lvname
    ```

    ![scenariusz — lvextend](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lvextend.png)

18. Sprawdzanie rozmiaru wartości LV

    ``` bash
    lvdisplay /dev/vgname/lvname
    ```

    ![scenariusz — lvdisplay](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lvdisplay.png)

19. Sprawdź, czy rozmiar systemu plików został właśnie zmieniony

    ``` bash
    df -h mountpoint
    ```

    ![scenariusz — DF1](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-df1.png)

20. Sprawdź, czy warstwa LVM jest tworzona na warstwie zaszyfrowanej

    ``` bash
    lsblk
    ```

    ![scenariusz — lsblk3](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lsblk3.png)

    Użycie lsblk bez opcji spowoduje wielokrotne wyświetlenie punktów instalacji, ponieważ sortuje według urządzeń i woluminów logicznych, można użyć lsblk-FS,-s odwraca sortowanie, aby mountpoints były wyświetlane jednokrotnie.

    ``` bash
    lsblk -fs
    ```

    ![scenariusz — lsblk4](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lsblk4.png)

#### <a name="extending-an-lvm-on-crypt-volume-resizing-an-existing-pv"></a>Rozszerzanie LVM na woluminie Crypt zmienianie rozmiarów istniejącej funkcji PV

1. Identyfikowanie zaszyfrowanych dysków

    ``` bash
    lsblk
    ```

    ![scenariof-lsblk01](./media/disk-encryption/resize-lvm/039-resize-lvm-scenariof-lsblk01.png)

    ``` bash
    lsblk -s
    ```

    ![scenariof-lsblk012](./media/disk-encryption/resize-lvm/040-resize-lvm-scenariof-lsblk012.png)

2. Sprawdź informacje w usłudze PV

    ``` bash
    pvs
    ```

    ![scenariof-pvs1](./media/disk-encryption/resize-lvm/041-resize-lvm-scenariof-pvs.png)

3. Sprawdź informacje o usłudze VG

    ``` bash
    vgs
    ```

    ![scenariof-vgs](./media/disk-encryption/resize-lvm/042-resize-lvm-scenariof-vgs.png)

4. Sprawdź informacje dotyczące Łotwy

    ``` bash
    lvs
    ```

    ![scenariof-lvs](./media/disk-encryption/resize-lvm/043-resize-lvm-scenariof-lvs.png)

5. Sprawdź użycie systemu plików

    ``` bash
    df -h /mountpoint(s)
    ```

    ![LVM-scenariof-FS](./media/disk-encryption/resize-lvm/044-resize-lvm-scenariof-fs.png)

6. Sprawdź rozmiary dysków

    ``` bash
    fdisk
    fdisk -l | egrep ^"Disk /"
    lsblk
    ```

    ![scenariof-fdisk01](./media/disk-encryption/resize-lvm/045-resize-lvm-scenariof-fdisk01.png)

7. Zmiana rozmiaru dysku danych

    Możesz odwoływać się do [rozbudowy dysków systemu Linux](expand-disks.md) (tylko do zmiany rozmiaru dysku), możesz użyć portalu, interfejsu wiersza polecenia lub programu PowerShell, aby wykonać ten krok.

    >[!NOTE]
    >Nie można wykonać operacji zmiany rozmiaru na dyskach wirtualnych z uruchomioną maszyną wirtualną. Musisz cofnąć przydział maszyny wirtualnej dla tego kroku

8. Sprawdź rozmiary dysków

    ``` bash
    fdisk
    fdisk -l | egrep ^"Disk /"
    lsblk
    ```

    ![scenariof-fdisk02](./media/disk-encryption/resize-lvm/046-resize-lvm-scenariof-fdisk02.png)

    Należy pamiętać, że w tym przypadku oba dyski zostały zmienione o rozmiarze od 2 GB do 4 GB, ale rozmiary FS, LV i PV pozostają takie same.

9. Sprawdź bieżący rozmiar PV

    Należy pamiętać, że w przypadku LVM-on-Crypt, WB jest urządzeniem/dev/mapper/, a nie urządzeniem/dev/SD *

    ``` bash
    pvdisplay /dev/mapper/devicemappername
    ```

    ![scenariof-pvs](./media/disk-encryption/resize-lvm/047-resize-lvm-scenariof-pvs.png)

10. Zmień rozmiar PV

    ``` bash
    pvresize /dev/mapper/devicemappername
    ```

    ![scenariof — Zmień rozmiar-WB](./media/disk-encryption/resize-lvm/048-resize-lvm-scenariof-resize-pv.png)

11. Sprawdź rozmiar PV po zmianie rozmiaru

    ``` bash
    pvdisplay /dev/mapper/devicemappername
    ```

    ![scenariof — WB](./media/disk-encryption/resize-lvm/049-resize-lvm-scenariof-pv.png)

12. Zmień rozmiar zaszyfrowanej warstwy na WB

    ``` bash
    cryptsetup resize /dev/mapper/devicemappername
    ```

    Zastosuj tę samą procedurę dla wszystkich dysków, dla których chcesz zmienić rozmiar.

13. Sprawdź informacje o usłudze VG

    ``` bash
    vgdisplay vgname
    ```

    ![scenariof-vg](./media/disk-encryption/resize-lvm/050-resize-lvm-scenariof-vg.png)

    VG ma teraz miejsce do przydzielenia do LVs

14. Sprawdź informacje o LV

    ``` bash
    lvdisplay vgname/lvname
    ```

    ![scenariof — LV](./media/disk-encryption/resize-lvm/051-resize-lvm-scenariof-lv.png)

15. Sprawdź wykorzystanie usług FS

    ``` bash
    df -h /mountpoint
    ```

    ![scenariof — FS](./media/disk-encryption/resize-lvm/052-resize-lvm-scenariof-fs.png)

16. Zmień rozmiar LV

    ``` bash
    lvresize -r -L +2G /dev/vgname/lvname
    ```

    ![scenariof-lvresize](./media/disk-encryption/resize-lvm/053-resize-lvm-scenariof-lvresize.png)

    Używamy opcji-r, aby przeprowadzić zmianę rozmiaru usług FS

17. Sprawdź informacje o LV

    ``` bash
    lvdisplay vgname/lvname
    ```

    ![scenariof-lvsize](./media/disk-encryption/resize-lvm/054-resize-lvm-scenariof-lvsize.png)

18. Sprawdź użycie systemu plików

    ``` bash
    df -h /mountpoint
    ```

    ![Utwórz system plików](./media/disk-encryption/resize-lvm/055-resize-lvm-scenariof-fs.png)

    Zastosuj tę samą procedurę zmiany wielkości do dowolnych dodatkowych Łotwy, które go wymagają

## <a name="next-steps"></a>Następne kroki

- [Rozwiązywanie problemów z usługą Azure Disk Encryption](disk-encryption-troubleshooting.md)
