---
title: Jak zmienić rozmiar zaszyfrowanych dysków zarządzania woluminami logicznymi przy użyciu Azure Disk Encryption
description: Ten artykuł zawiera instrukcje dotyczące zmiany rozmiarów szyfrowanych dysków ADE przy użyciu funkcji zarządzania woluminami logicznymi.
author: jofrance
ms.service: security
ms.topic: article
ms.author: jofrance
ms.date: 09/21/2020
ms.openlocfilehash: 3a3e9b7406e11261aff12d77d9fbeed5debbe938
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91744274"
---
# <a name="how-to-resize-logical-volume-management-devices-that-use-azure-disk-encryption"></a>Jak zmienić rozmiar urządzeń do zarządzania woluminami logicznymi, które używają Azure Disk Encryption

W tym artykule dowiesz się, jak zmienić rozmiar dysków z danymi, które używają Azure Disk Encryption. Aby zmienić rozmiar dysków, należy użyć zarządzania woluminami logicznymi (LVM) w systemie Linux. Kroki dotyczą wielu scenariuszy.

Tego procesu zmiany rozmiarów można użyć w następujących środowiskach:

- Dystrybucje systemu Linux:
    - Red Hat Enterprise Linux (RHEL) 7 lub nowszy
    - Ubuntu 16 lub nowszy
    - SUSE 12 lub nowszy
- Azure Disk Encryption wersje: 
    - Rozszerzenie z jednym przebiegiem
    - Rozszerzenie o podwójnej przejściu

## <a name="prerequisites"></a>Wymagania wstępne

W tym artykule założono, że masz:

- Istniejąca konfiguracja LVM. Aby uzyskać więcej informacji, zobacz [Konfigurowanie LVM na maszynie wirtualnej z systemem Linux](configure-lvm.md).

- Dyski, które są już zaszyfrowane przez Azure Disk Encryption. Aby uzyskać więcej informacji, zobacz [Konfigurowanie LVM i RAID na zaszyfrowanych urządzeniach](how-to-configure-lvm-raid-on-crypt.md).

- Środowisko pracy przy użyciu systemu Linux i LVM.

- Środowisko pracy przy użyciu ścieżek */dev/Disk/scsi1/* dla dysków danych na platformie Azure. Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów z nazwami urządzeń maszyny wirtualnej z systemem Linux](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-device-names-problems). 

## <a name="scenarios"></a>Scenariusze

Procedury opisane w tym artykule mają zastosowanie do następujących scenariuszy:

- Tradycyjne konfiguracje LVM i LVM-on-Crypt
- Tradycyjne szyfrowanie LVM 
- LVM-on-Crypt 

### <a name="traditional-lvm-and-lvm-on-crypt-configurations"></a>Tradycyjne konfiguracje LVM i LVM-on-Crypt

Tradycyjne konfiguracje LVM i LVM-on-Crypt, rozszerzając wolumin logiczny (LV), gdy grupa woluminów (VG) ma dostępne miejsce.

### <a name="traditional-lvm-encryption"></a>Tradycyjne szyfrowanie LVM 

W tradycyjnym szyfrowaniu LVM LVs są szyfrowane. Cały dysk nie jest szyfrowany.

Za pomocą tradycyjnego szyfrowania LVM można:

- Po dodaniu nowego woluminu fizycznego (PV) można zwiększyć wartość LV.
- Zwiększ wartość LV w przypadku zmiany rozmiaru istniejącej funkcji PV.

### <a name="lvm-on-crypt"></a>LVM-on-Crypt 

Zalecaną metodą szyfrowania dysków jest LVM-on-Encryption. Ta metoda szyfruje cały dysk, a nie tylko wartość LV.

Za pomocą LVM-on-Crypt można: 

- Po dodaniu nowej funkcji PV Zwiększ wartość LV.
- Zwiększ wartość LV w przypadku zmiany rozmiaru istniejącej funkcji PV.

> [!NOTE]
> Nie zalecamy mieszania tradycyjnego szyfrowania LVM i LVM-on-Crypt na tej samej maszynie wirtualnej.

Poniższe sekcje zawierają przykłady korzystania z LVM i LVM-on-Crypt. W przykładach użyto istniejących wartości dysków, PVs, VGs, LVs, systemów plików, unikatowych identyfikatorów uniwersalnych (UUID) i punktów instalacji. Zamień te wartości na własne wartości, aby dopasować je do środowiska.

#### <a name="extend-an-lv-when-the-vg-has-available-space"></a>Zwiększ wartość LV, gdy VG ma dostępne miejsce

Tradycyjnym sposobem zmiany rozmiaru LVs jest zwiększenie zakresu LV, gdy VG ma dostępne miejsce. Tej metody można użyć w przypadku dysków nieszyfrowanych, tradycyjnych woluminów szyfrowanych LVM i konfiguracji LVM-on-Crypt.

1. Sprawdź bieżący rozmiar systemu plików, który chcesz zwiększyć:

    ``` bash
    df -h /mountpoint
    ```

    ![Zrzut ekranu pokazujący kod, który sprawdza rozmiar systemu plików. Polecenie i wynik są wyróżnione.](./media/disk-encryption/resize-lvm/001-resize-lvm-scenarioa-check-fs.png)

2. Sprawdź, czy VG ma wystarczającą ilość miejsca, aby zwiększyć wartość LV:

    ``` bash
    vgs
    ```

    ![Zrzut ekranu przedstawiający kod, który sprawdza ilość miejsca w VG. Polecenie i wynik są wyróżnione.](./media/disk-encryption/resize-lvm/002-resize-lvm-scenarioa-check-vgs.png)

    Można również użyć `vgdisplay` :

    ``` bash
    vgdisplay vgname
    ```

    ![Zrzut ekranu pokazujący kod wyświetlania V G, który sprawdza ilość miejsca w VG. Polecenie i wynik są wyróżnione.](./media/disk-encryption/resize-lvm/002-resize-lvm-scenarioa-check-vgdisplay.png)

3. Określ, które LV należy zmienić rozmiaru:

    ``` bash
    lsblk
    ```

    ![Zrzut ekranu przedstawiający wynik polecenia l-b l k. Polecenie i wynik są wyróżnione.](./media/disk-encryption/resize-lvm/002-resize-lvm-scenarioa-check-lsblk1.png)

    Różnica polega na tym, że w danych wyjściowych na poziomie dysku zaszyfrowana warstwa jest równa LVM-on-Crypt.

    ![Zrzut ekranu przedstawiający wynik polecenia l-b l k. Dane wyjściowe są wyróżnione. Pokazuje zaszyfrowaną warstwę.](./media/disk-encryption/resize-lvm/002-resize-lvm-scenarioa-check-lsblk2.png)

4. Sprawdź rozmiar LV:

    ``` bash
    lvdisplay lvname
    ```

    ![Zrzut ekranu przedstawiający kod, który sprawdza rozmiar woluminu logicznego. Polecenie i wynik są wyróżnione.](./media/disk-encryption/resize-lvm/002-resize-lvm-scenarioa-check-lvdisplay01.png)

5. Zwiększ rozmiar LV przy użyciu, `-r` Aby zmienić rozmiar systemu plików w trybie online:

    ``` bash
    lvextend -r -L +2G /dev/vgname/lvname
    ```

    ![Zrzut ekranu przedstawiający kod, który zwiększa rozmiar woluminu logicznego. Polecenie i wyniki są wyróżnione.](./media/disk-encryption/resize-lvm/003-resize-lvm-scenarioa-resize-lv.png)

6. Sprawdź nowe rozmiary programu LV i systemu plików:

    ``` bash
    df -h /mountpoint
    ```

    ![Zrzut ekranu przedstawiający kod, który weryfikuje rozmiar LV i system plików. Polecenie i wynik są wyróżnione.](./media/disk-encryption/resize-lvm/004-resize-lvm-scenarioa-check-fs.png)

    Rozmiar wyjściowy wskazuje, że rozmiar LV i systemu plików został pomyślnie zmieniony.

Informacje o LV można sprawdzić ponownie, aby potwierdzić zmiany na poziomie LV:

``` bash
lvdisplay lvname
```

![Zrzut ekranu przedstawiający kod, który potwierdza nowe rozmiary. Rozmiary są wyróżnione.](./media/disk-encryption/resize-lvm/004-resize-lvm-scenarioa-check-lvdisplay2.png)

#### <a name="extend-a-traditional-lvm-volume-by-adding-a-new-pv"></a>Rozszerzając tradycyjny wolumin LVM, dodając nowe WB

Gdy musisz dodać nowy dysk, aby zwiększyć rozmiar VG, Rozszerz tradycyjny wolumin LVM przez dodanie nowej funkcji PV.

1. Sprawdź bieżący rozmiar systemu plików, który chcesz zwiększyć:

    ``` bash
    df -h /mountpoint
    ```

    ![Zrzut ekranu przedstawiający kod, który sprawdza bieżący rozmiar systemu plików. Polecenie i wynik są wyróżnione.](./media/disk-encryption/resize-lvm/005-resize-lvm-scenariob-check-fs.png)

2. Sprawdź bieżącą konfigurację PV:

    ``` bash
    pvs
    ```

    ![Zrzut ekranu przedstawiający kod, który sprawdza bieżącą konfigurację PV. Polecenie i wynik są wyróżnione.](./media/disk-encryption/resize-lvm/006-resize-lvm-scenariob-check-pvs.png)

3. Sprawdź bieżące informacje VG:

    ``` bash
    vgs
    ```

    ![Zrzut ekranu przedstawiający kod, który sprawdza bieżące informacje o grupie woluminów. Polecenie i wynik są wyróżnione.](./media/disk-encryption/resize-lvm/007-resize-lvm-scenariob-check-vgs.png)

4. Sprawdź bieżącą listę dysków. Zidentyfikuj dyski danych, sprawdzając urządzenia w */dev/Disk/Azure/scsi1/*.

    ``` bash
    ls -l /dev/disk/azure/scsi1/
    ```

    ![Zrzut ekranu przedstawiający kod, który sprawdza bieżącą listę dysków. Polecenie i wyniki są wyróżnione.](./media/disk-encryption/resize-lvm/008-resize-lvm-scenariob-check-scs1.png)

5. Sprawdź dane wyjściowe `lsblk` : 

    ``` bash
    lsbk
    ```

    ![Zrzut ekranu przedstawiający kod, który sprawdza dane wyjściowe l s b l k. Polecenie i wyniki są wyróżnione.](./media/disk-encryption/resize-lvm/008-resize-lvm-scenariob-check-lsblk.png)

6. Dołącz nowy dysk do maszyny wirtualnej, postępując zgodnie z instrukcjami zawartymi w temacie [dołączanie dysku danych do maszyny wirtualnej z systemem Linux](attach-disk-portal.md).

7. Sprawdź listę dysków i zwróć uwagę na nowy dysk.

    ``` bash
    ls -l /dev/disk/azure/scsi1/
    ```

    ![Zrzut ekranu przedstawiający kod, który sprawdza listę dysków. Wyniki są wyróżnione.](./media/disk-encryption/resize-lvm/009-resize-lvm-scenariob-check-scsi12.png)

    ``` bash
    lsbk
    ```

    ![Zrzut ekranu przedstawiający kod, który sprawdza listę dysków przy użyciu l s b l k. Polecenie i wynik są wyróżnione.](./media/disk-encryption/resize-lvm/009-resize-lvm-scenariob-check-lsblk1.png)

8. Utwórz nową wartość PV na początku nowego dysku danych:

    ``` bash
    pvcreate /dev/newdisk
    ```

    ![Zrzut ekranu przedstawiający kod, który tworzy nową wartość PV. Zostanie wyróżniony wynik.](./media/disk-encryption/resize-lvm/010-resize-lvm-scenariob-pvcreate.png)

    Ta metoda używa całego dysku jako PV bez partycji. Alternatywnie można użyć programu, `fdisk` Aby utworzyć partycję, a następnie użyć tej partycji dla programu `pvcreate` .

9. Sprawdź, czy WB została dodana do listy PV:

    ``` bash
    pvs
    ```

    ![Zrzut ekranu przedstawiający kod pokazujący listę woluminów fizycznych. Zostanie wyróżniony wynik.](./media/disk-encryption/resize-lvm/011-resize-lvm-scenariob-check-pvs1.png)

10. Zwiększ wartość VG, dodając do niej nową wartość PV:

    ``` bash
    vgextend vgname /dev/newdisk
    ```

    ![Zrzut ekranu przedstawiający kod, który rozszerza grupę woluminów. Zostanie wyróżniony wynik.](./media/disk-encryption/resize-lvm/012-resize-lvm-scenariob-vgextend.png)

11. Sprawdź nowy rozmiar VG:

    ``` bash
    vgs
    ```

    ![Zrzut ekranu przedstawiający kod, który sprawdza rozmiar grupy woluminów. Wyniki są wyróżnione.](./media/disk-encryption/resize-lvm/013-resize-lvm-scenariob-check-vgs1.png)

12. Użyj, `lsblk` Aby określić wartość PARAMETRU LV, dla którego należy zmienić rozmiar:

    ``` bash
    lsblk
    ```

    ![Zrzut ekranu przedstawiający kod, który identyfikuje wolumin lokalny, dla którego należy zmienić rozmiar. Wyniki są wyróżnione.](./media/disk-encryption/resize-lvm/013-resize-lvm-scenariob-check-lsblk1.png)

13. Zwiększ rozmiar LV przy użyciu, `-r` Aby zwiększyć system plików w trybie online:

    ``` bash
    lvextend -r -L +2G /dev/vgname/lvname
    ```

    ![Zrzut ekranu przedstawiający kod, który zwiększa rozmiar systemu plików w trybie online. Wyniki są wyróżnione.](./media/disk-encryption/resize-lvm/013-resize-lvm-scenariob-lvextend.png) 

14. Sprawdź nowe rozmiary wartości LV i system plików:

    ``` bash
    df -h /mountpoint
    ```

    ![Zrzut ekranu przedstawiający kod, który sprawdza rozmiary woluminu lokalnego i systemu plików. Polecenie i wynik są wyróżnione.](./media/disk-encryption/resize-lvm/014-resize-lvm-scenariob-check-fs1.png)

    >[!IMPORTANT]
    >Gdy szyfrowanie danych platformy Azure jest używane w tradycyjnych konfiguracjach LVM, zaszyfrowana warstwa jest tworzona na poziomie LV, a nie na poziomie dysku.
    >
    >Na tym etapie zaszyfrowana warstwa zostanie rozszerzona na nowy dysk. Rzeczywisty dysk danych nie ma ustawień szyfrowania na poziomie platformy, więc jego stan szyfrowania nie jest aktualizowany.
    >
    >Oto niektóre z powodów, dla których zaleca się zastosowanie LVM-on-Crypt. 

15. Sprawdź informacje o szyfrowaniu z portalu:

    ![Zrzut ekranu przedstawiający informacje o szyfrowaniu w portalu. Nazwa dysku i szyfrowanie są wyróżnione.](./media/disk-encryption/resize-lvm/014-resize-lvm-scenariob-check-portal1.png)

    Aby zaktualizować ustawienia szyfrowania na dysku, Dodaj nowe LV i Włącz rozszerzenie na maszynie wirtualnej.
    
16. Dodaj nowe LV, Utwórz na nim system plików i dodaj go do programu `/etc/fstab` .

17. Ponownie ustaw rozszerzenie szyfrowania. Ta godzina spowoduje oznaczenie ustawień szyfrowania na nowym dysku danych na poziomie platformy. Oto przykład interfejsu wiersza polecenia:

    ``` bash
    az vm encryption enable -g ${RGNAME} --name ${VMNAME} --disk-encryption-keyvault "<your-unique-keyvault-name>"
    ```

18. Sprawdź informacje o szyfrowaniu z portalu:

    ![Zrzut ekranu przedstawiający informacje o szyfrowaniu w portalu. Nazwa dysku i informacje o szyfrowaniu są wyróżnione.](./media/disk-encryption/resize-lvm/014-resize-lvm-scenariob-check-portal2.png)

Po zaktualizowaniu ustawień szyfrowania można usunąć nowe LV. Usuń także wpis z `/etc/fstab` i `/etc/crypttab` , który został utworzony.

![Zrzut ekranu przedstawiający kod, który usuwa nowy wolumin logiczny. Usunięta karta F S i karta Crypt są wyróżnione.](./media/disk-encryption/resize-lvm/014-resize-lvm-scenariob-delete-fstab-crypttab.png)

Wykonaj następujące kroki, aby zakończyć czyszczenie:

1. Odinstalowanie programu LV:

    ``` bash
    umount /mountpoint
    ```

1. Zamknij zaszyfrowaną warstwę woluminu:

    ``` bash
    cryptsetup luksClose /dev/vgname/lvname
    ```

1. Usuń wartość LV:

    ``` bash
    lvremove /dev/vgname/lvname
    ```

#### <a name="extend-a-traditional-lvm-volume-by-resizing-an-existing-pv"></a>Rozszerzając tradycyjny wolumin LVM, zmieniając rozmiar istniejącej funkcji PV

W niektórych scenariuszach Twoje ograniczenia mogą wymagać zmiany rozmiaru istniejącego dysku. Oto kroki tej procedury:

1. Zidentyfikuj zaszyfrowane dyski:

    ``` bash
    ls -l /dev/disk/azure/scsi1/
    ```

    ![Zrzut ekranu przedstawiający kod, który identyfikuje szyfrowane dyski. Wyniki są wyróżnione.](./media/disk-encryption/resize-lvm/015-resize-lvm-scenarioc-check-scsi1.png)

    ``` bash
    lsblk -fs
    ```

    ![Zrzut ekranu przedstawiający kod alternatywny, który identyfikuje szyfrowane dyski. Wyniki są wyróżnione.](./media/disk-encryption/resize-lvm/015-resize-lvm-scenarioc-check-lsblk.png)

2. Sprawdź informacje o usłudze PV:

    ``` bash
    pvs
    ```

    ![Zrzut ekranu przedstawiający kod, który sprawdza informacje o woluminie fizycznym. Wyniki są wyróżnione.](./media/disk-encryption/resize-lvm/016-resize-lvm-scenarioc-check-pvs.png)

    Wyniki w obrazie pokazują, że wszystkie miejsca na wszystkich PVs są obecnie używane.

3. Sprawdź informacje VG:

    ``` bash
    vgs
    vgdisplay -v vgname
    ```

    ![Zrzut ekranu przedstawiający kod, który sprawdza informacje o grupie woluminów. Wyniki są wyróżnione.](./media/disk-encryption/resize-lvm/017-resize-lvm-scenarioc-check-vgs.png)

4. Sprawdź rozmiary dysków. Możesz użyć `fdisk` lub `lsblk` , aby wyświetlić listę rozmiarów dysków.

    ``` bash
    for disk in `ls -l /dev/disk/azure/scsi1/* | awk -F/ '{print $NF}'` ; do echo "fdisk -l /dev/${disk} | grep ^Disk "; done | bash

    lsblk -o "NAME,SIZE"
    ```

    ![Zrzut ekranu przedstawiający kod, który sprawdza rozmiary dysków. Wyniki są wyróżnione.](./media/disk-encryption/resize-lvm/018-resize-lvm-scenarioc-check-fdisk.png)

    Tutaj określono, które PVs są skojarzone z LVs przy użyciu `lsblk -fs` . Skojarzenia można zidentyfikować, uruchamiając `lvdisplay` .

    ``` bash
    lvdisplay --maps VG/LV
    lvdisplay --maps datavg/datalv1
    ```

    ![Zrzut ekranu przedstawiający alternatywny sposób identyfikowania skojarzeń woluminów fizycznych z woluminami lokalnymi. Wyniki są wyróżnione.](./media/disk-encryption/resize-lvm/019-resize-lvm-scenarioc-check-lvdisplay.png)

    W takim przypadku wszystkie cztery dyski danych są częścią tego samego VG i jednego typu LV. Konfiguracja może się różnić.

5. Sprawdź bieżące użycie systemu plików:

    ``` bash
    df -h /datalvm*
    ```

    ![Zrzut ekranu przedstawiający kod, który sprawdza użycie systemu plików. Polecenie i wyniki są wyróżnione.](./media/disk-encryption/resize-lvm/020-resize-lvm-scenarioc-check-df.png)

6. Zmień rozmiar dysków danych, postępując zgodnie z instrukcjami w temacie [rozszerzanie dysku zarządzanego platformy Azure](expand-disks.md#expand-an-azure-managed-disk). Możesz użyć portalu, interfejsu wiersza polecenia lub programu PowerShell.

    >[!IMPORTANT]
    >Nie można zmienić rozmiaru dysków wirtualnych, gdy maszyna wirtualna jest uruchomiona. Cofnij przydział maszyny wirtualnej na potrzeby tego kroku.

7. Uruchom maszynę wirtualną i sprawdź nowe rozmiary przy użyciu polecenia `fdisk` .

    ``` bash
    for disk in `ls -l /dev/disk/azure/scsi1/* | awk -F/ '{print $NF}'` ; do echo "fdisk -l /dev/${disk} | grep ^Disk "; done | bash

    lsblk -o "NAME,SIZE"
    ```

    ![Zrzut ekranu przedstawiający kod umożliwiający sprawdzenie rozmiaru dysku. Zostanie wyróżniony wynik.](./media/disk-encryption/resize-lvm/021-resize-lvm-scenarioc-check-fdisk1.png)

    W tym przypadku `/dev/sdd` rozmiar został zmieniony z 5 g do 20 g.

8. Sprawdź bieżący rozmiar PV:

    ``` bash
    pvdisplay /dev/resizeddisk
    ```

    ![Zrzut ekranu przedstawiający kod, który sprawdza rozmiar P V. Zostanie wyróżniony wynik.](./media/disk-encryption/resize-lvm/022-resize-lvm-scenarioc-check-pvdisplay.png)
    
    Mimo że zmieniono rozmiar dysku, funkcja PV nadal ma poprzedni rozmiar.

9. Zmień rozmiar PV:

    ``` bash
    pvresize /dev/resizeddisk
    ```

    ![Zrzut ekranu przedstawiający kod, który zmienia rozmiar woluminu fizycznego. Zostanie wyróżniony wynik.](./media/disk-encryption/resize-lvm/023-resize-lvm-scenarioc-check-pvresize.png)


10. Sprawdź rozmiar PV:

    ``` bash
    pvdisplay /dev/resizeddisk
    ```

    ![Zrzut ekranu przedstawiający kod, który sprawdza rozmiar woluminu fizycznego. Zostanie wyróżniony wynik.](./media/disk-encryption/resize-lvm/024-resize-lvm-scenarioc-check-pvdisplay1.png)

    Zastosuj tę samą procedurę dla wszystkich dysków, których rozmiar chcesz zmienić.

11. Sprawdź informacje VG.

    ``` bash
    vgdisplay vgname
    ```

    ![Zrzut ekranu przedstawiający kod, który sprawdza informacje dla grupy woluminów. Zostanie wyróżniony wynik.](./media/disk-encryption/resize-lvm/025-resize-lvm-scenarioc-check-vgdisplay1.png)

    VG ma teraz wystarczającą ilość miejsca do przydzielenia do LVs.

12. Zmień rozmiar LV:

    ``` bash
    lvresize -r -L +5G vgname/lvname
    lvresize -r -l +100%FREE /dev/datavg/datalv01
    ```

    ![Zrzut ekranu przedstawiający kod, który zmienia rozmiar L V. Wyniki są wyróżnione.](./media/disk-encryption/resize-lvm/031-resize-lvm-scenarioc-check-lvresize1.png)

13. Sprawdź rozmiar systemu plików:

    ``` bash
    df -h /datalvm2
    ```

    ![Zrzut ekranu przedstawiający kod, który sprawdza rozmiar systemu plików. Zostanie wyróżniony wynik.](./media/disk-encryption/resize-lvm/032-resize-lvm-scenarioc-check-df3.png)

#### <a name="extend-an-lvm-on-crypt-volume-by-adding-a-new-pv"></a>Zwiększ rozmiar woluminu LVM na Crypt przez dodanie nowej funkcji PV

Możesz również zwiększyć wolumin LVM-on-Crypt, dodając nowe PV. Ta metoda jest ściśle zgodna z krokami [konfiguracji LVM i RAID na zaszyfrowanych urządzeniach](how-to-configure-lvm-raid-on-crypt.md#general-steps). Zapoznaj się z sekcją, w której wyjaśniono, jak dodać nowy dysk i skonfigurować go w konfiguracji LVM-on-Crypt.

Za pomocą tej metody można dodać miejsce do istniejącej LV. Można też utworzyć nowy VGs lub LVs.

1. Sprawdź bieżący rozmiar VG:

    ``` bash
    vgdisplay vgname
    ```

    ![Zrzut ekranu przedstawiający kod, który sprawdza rozmiar grupy woluminów. Wyniki są wyróżnione.](./media/disk-encryption/resize-lvm/033-resize-lvm-scenarioe-check-vg01.png)

2. Sprawdź rozmiar systemu plików i wartości LV, które chcesz rozwinąć:

    ``` bash
    lvdisplay /dev/vgname/lvname
    ```

    ![Zrzut ekranu przedstawiający kod, który sprawdza rozmiar woluminu lokalnego. Wyniki są wyróżnione.](./media/disk-encryption/resize-lvm/034-resize-lvm-scenarioe-check-lv01.png)

    ``` bash
    df -h mountpoint
    ```

    ![Zrzut ekranu przedstawiający kod, który sprawdza rozmiar systemu plików. Zostanie wyróżniony wynik.](./media/disk-encryption/resize-lvm/034-resize-lvm-scenarioe-check-fs01.png)

3. Dodaj nowy dysk danych do maszyny wirtualnej i zidentyfikuj go.

    Przed dodaniem nowego dysku Sprawdź dyski:

    ``` bash
    fdisk -l | egrep ^"Disk /"
    ```

    ![Zrzut ekranu przedstawiający kod, który sprawdza rozmiar dysków. Zostanie wyróżniony wynik.](./media/disk-encryption/resize-lvm/035-resize-lvm-scenarioe-check-newdisk01.png)

    Oto inny sposób na sprawdzenie dysków przed dodaniem nowego dysku:

    ``` bash
    lsblk
    ```

    ![Zrzut ekranu przedstawiający alternatywny kod, który sprawdza rozmiar dysków. Wyniki są wyróżnione.](./media/disk-encryption/resize-lvm/035-resize-lvm-scenarioe-check-newdisk02.png)

    Aby dodać nowy dysk, można użyć programu PowerShell, interfejsu wiersza polecenia platformy Azure lub Azure Portal. Aby uzyskać więcej informacji, zobacz [dołączanie dysku danych do maszyny wirtualnej z systemem Linux](attach-disk-portal.md).

    Schemat nazw jądra ma zastosowanie do nowo dodanego urządzenia. Nowy dysk ma zwykle przypisaną następną dostępną literę. W takim przypadku dodany dysk jest `sdd` .

4. Sprawdź dyski, aby upewnić się, że nowy dysk został dodany:

    ``` bash
    fdisk -l | egrep ^"Disk /"
    ```

    ![Zrzut ekranu przedstawiający kod, który wyświetla listę dysków. Wyniki są wyróżnione.](./media/disk-encryption/resize-lvm/036-resize-lvm-scenarioe-check-newdisk02.png)

    ``` bash
    lsblk
    ```

    ![Zrzut ekranu przedstawiający nowo dodany dysk w danych wyjściowych.](./media/disk-encryption/resize-lvm/036-resize-lvm-scenarioe-check-newdisk03.png)

5. Utwórz system plików na początku niedawno dodanego dysku. Dopasowuje dysk do połączonych urządzeń `/dev/disk/azure/scsi1/` .

    ``` bash
    ls -la /dev/disk/azure/scsi1/
    ```

    ![Zrzut ekranu przedstawiający kod, który tworzy system plików. Wyniki są wyróżnione.](./media/disk-encryption/resize-lvm/037-resize-lvm-scenarioe-check-newdisk03.png)

    ``` bash
    mkfs.ext4 /dev/disk/azure/scsi1/${disk}
    ```

    ![Zrzut ekranu przedstawiający dodatkowy kod, który tworzy system plików i dopasowuje dysk do połączonych urządzeń. Wyniki są wyróżnione.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-mkfs01.png)

6. Utwórz tymczasowy punkt instalacji dla nowego dodanego dysku:

    ``` bash
    newmount=/data4
    mkdir ${newmount}
    ```

7. Dodaj ostatnio utworzony system plików do programu `/etc/fstab` .

    ``` bash
    blkid /dev/disk/azure/scsi1/lun4| awk -F\" '{print "UUID="$2" '${newmount}' "$4" defaults,nofail 0 0"}' >> /etc/fstab
    ```

8. Zainstaluj nowo utworzony system plików:

    ``` bash
    mount -a
    ```

9. Sprawdź, czy nowy system plików jest zainstalowany:

    ``` bash
    df -h
    ```

    ![Zrzut ekranu przedstawiający kod, który weryfikuje, czy system plików jest zainstalowany. Zostanie wyróżniony wynik.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-df.png)

    ``` bash
    lsblk
    ```

    ![Zrzut ekranu przedstawiający dodatkowy kod, który weryfikuje, czy system plików jest zainstalowany. Zostanie wyróżniony wynik.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lsblk.png)

10. Uruchom ponownie szyfrowanie, które zostało wcześniej uruchomione dla dysków danych.

    >[!TIP]
    >W przypadku LVM-on-Crypt zalecamy korzystanie z programu `EncryptFormatAll` . W przeciwnym razie podczas ustawiania dodatkowych dysków może być widoczne podwójne szyfrowanie.
    >
    >Aby uzyskać więcej informacji, zobacz [Konfigurowanie LVM i RAID na zaszyfrowanych urządzeniach](how-to-configure-lvm-raid-on-crypt.md).

    Oto przykład:

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

    Po zakończeniu szyfrowania na nowo dodanym dysku zobaczysz warstwę Crypt:

    ``` bash
    lsblk
    ```

    ![Zrzut ekranu przedstawiający kod, który sprawdza warstwę Crypt. Zostanie wyróżniony wynik.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lsblk2.png)

11. Odinstaluj zaszyfrowaną warstwę nowego dysku:

    ``` bash
    umount ${newmount}
    ```

12. Sprawdź bieżące informacje PV:

    ``` bash
    pvs
    ```

    ![Zrzut ekranu przedstawiający kod, który sprawdza informacje o woluminie fizycznym. Zostanie wyróżniony wynik.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-currentpvs.png)

13. Utwórz wa na zaszyfrowanej warstwie dysku. Z poprzedniego polecenia Weź nazwę urządzenia `lsblk` . Dodaj `/dev/` Mapowanie przed nazwą urządzenia, aby utworzyć WB:

    ``` bash
    pvcreate /dev/mapper/mapperdevicename
    ```

    ![Zrzut ekranu przedstawiający kod, który tworzy wolumin fizyczny w zaszyfrowanej warstwie. Wyniki są wyróżnione.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-pvcreate.png)

    Zobaczysz ostrzeżenie o czyszczeniu bieżącej `ext4 fs` sygnatury. To ostrzeżenie jest oczekiwane. Odpowiedz na to pytanie przy użyciu `y` .

14. Sprawdź, czy nowa funkcja PV została dodana do konfiguracji LVM:

    ``` bash
    pvs
    ```

    ![Zrzut ekranu przedstawiający kod, który weryfikuje, czy wolumin fizyczny został dodany do konfiguracji LVM. Zostanie wyróżniony wynik.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-newpv.png)

15. Dodaj nową wartość PV do VG, którą chcesz zwiększyć.

    ``` bash
    vgextend vgname /dev/mapper/nameofhenewpv
    ```

    ![Zrzut ekranu przedstawiający kod, który dodaje wolumin fizyczny do grupy woluminów. Wyniki są wyróżnione.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-vgextent.png)

16. Sprawdź nowy rozmiar i wolne miejsce VG:

    ``` bash
    vgdisplay vgname
    ```

    ![Zrzut ekranu przedstawiający kod, który weryfikuje rozmiar i wolne miejsce w grupie woluminów. Wyniki są wyróżnione.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-vgdisplay.png)

    Zwróć uwagę na wzrost `Total PE` liczby i `Free PE / Size` .

17. Zwiększ rozmiar wartości LV i systemu plików. Użyj `-r` opcji na `lvextend` . W tym przykładzie dodamy łączną ilość dostępnego miejsca w VG do danego LV.

    ``` bash
    lvextend -r -l +100%FREE /dev/vgname/lvname
    ```

    ![Zrzut ekranu przedstawiający kod, który zwiększa rozmiar woluminu lokalnego i systemu plików. Wyniki są wyróżnione.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lvextend.png)

Wykonaj kolejne kroki, aby zweryfikować zmiany.

1. Sprawdź rozmiar wartości LV:

    ``` bash
    lvdisplay /dev/vgname/lvname
    ```

    ![Zrzut ekranu przedstawiający kod, który weryfikuje nowy rozmiar woluminu lokalnego. Wyniki są wyróżnione.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lvdisplay.png)

1. Sprawdź, czy nowy rozmiar systemu plików:

    ``` bash
    df -h mountpoint
    ```

    ![Zrzut ekranu przedstawiający kod, który weryfikuje nowy rozmiar systemu plików. Zostanie wyróżniony wynik.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-df1.png)

1. Sprawdź, czy warstwa LVM znajduje się na warstwie zaszyfrowanej:

    ``` bash
    lsblk
    ```

    ![Zrzut ekranu przedstawiający kod, który weryfikuje, czy warstwa LVM znajduje się na warstwie zaszyfrowanej. Zostanie wyróżniony wynik.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lsblk3.png)

    Jeśli używasz `lsblk` bez opcji, punkty instalacji są widoczne wiele razy. Polecenie sortuje według urządzenia i LVs. 

    Możesz chcieć użyć `lsblk -fs` . W tym poleceniu `-fs` odwraca porządek sortowania, aby punkty instalacji były wyświetlane jednokrotnie. Dyski są wyświetlane wiele razy.

    ``` bash
    lsblk -fs
    ```

    ![Zrzut ekranu przedstawiający kod alternatywny, który weryfikuje, czy warstwa LVM znajduje się u góry warstwy zaszyfrowanej. Zostanie wyróżniony wynik.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lsblk4.png)

#### <a name="extend-an-lvm-on-a-crypt-volume-by-resizing-an-existing-pv"></a>Zwiększ wartość LVM na woluminie Crypt, zmieniając rozmiar istniejącej funkcji PV

1. Zidentyfikuj zaszyfrowane dyski:

    ``` bash
    lsblk
    ```

    ![Zrzut ekranu przedstawiający kod, który identyfikuje zaszyfrowane dyski. Wyniki są wyróżnione.](./media/disk-encryption/resize-lvm/039-resize-lvm-scenariof-lsblk01.png)

    ``` bash
    lsblk -s
    ```

    ![Zrzut ekranu przedstawiający kod alternatywny, który identyfikuje zaszyfrowane dyski. Wyniki są wyróżnione.](./media/disk-encryption/resize-lvm/040-resize-lvm-scenariof-lsblk012.png)

2. Sprawdź informacje o usłudze PV:

    ``` bash
    pvs
    ```

    ![Zrzut ekranu przedstawiający kod, który sprawdza informacje dotyczące woluminów fizycznych. Wyniki są wyróżnione.](./media/disk-encryption/resize-lvm/041-resize-lvm-scenariof-pvs.png)

3. Sprawdź informacje o usłudze VG:

    ``` bash
    vgs
    ```

    ![Zrzut ekranu przedstawiający kod, który sprawdza informacje o grupach woluminów. Wyniki są wyróżnione.](./media/disk-encryption/resize-lvm/042-resize-lvm-scenariof-vgs.png)

4. Sprawdź informacje dotyczące Łotwy:

    ``` bash
    lvs
    ```

    ![Zrzut ekranu przedstawiający kod, który sprawdza informacje dotyczące woluminu lokalnego. Zostanie wyróżniony wynik.](./media/disk-encryption/resize-lvm/043-resize-lvm-scenariof-lvs.png)

5. Sprawdź użycie systemu plików:

    ``` bash
    df -h /mountpoint(s)
    ```

    ![Zrzut ekranu przedstawiający kod, który sprawdza, jaka część systemu plików jest używana. Wyniki są wyróżnione.](./media/disk-encryption/resize-lvm/044-resize-lvm-scenariof-fs.png)

6. Sprawdź rozmiary dysków:

    ``` bash
    fdisk
    fdisk -l | egrep ^"Disk /"
    lsblk
    ```

    ![Zrzut ekranu przedstawiający kod, który sprawdza rozmiar dysków. Wyniki są wyróżnione.](./media/disk-encryption/resize-lvm/045-resize-lvm-scenariof-fdisk01.png)

7. Zmień rozmiar dysku z danymi. Możesz użyć portalu, interfejsu wiersza polecenia lub programu PowerShell. Aby uzyskać więcej informacji, zobacz sekcję zmiana rozmiaru dysku w temacie [rozszerzanie wirtualnych dysków twardych na maszynę wirtualną z systemem Linux](expand-disks.md#expand-an-azure-managed-disk). 

    >[!IMPORTANT]
    >Nie można zmienić rozmiaru dysków wirtualnych, gdy maszyna wirtualna jest uruchomiona. Cofnij przydział maszyny wirtualnej na potrzeby tego kroku.

8. Sprawdź rozmiary dysków:

    ``` bash
    fdisk
    fdisk -l | egrep ^"Disk /"
    lsblk
    ```

    ![Zrzut ekranu przedstawiający kod, który sprawdza rozmiary dysków. Wyniki są wyróżnione.](./media/disk-encryption/resize-lvm/046-resize-lvm-scenariof-fdisk02.png)

    W takim przypadku rozmiar obu dysków został zmieniony z 2 GB do 4 GB. Ale rozmiar systemu plików, LV i PV pozostaje taki sam.

9. Sprawdź bieżący rozmiar PV. Pamiętaj, że w przypadku LVM-on-Crypt, WA to `/dev/mapper/` urządzenie, a nie `/dev/sd*` urządzenie.

    ``` bash
    pvdisplay /dev/mapper/devicemappername
    ```

    ![Zrzut ekranu przedstawiający kod, który sprawdza rozmiar bieżącego woluminu fizycznego. Wyniki są wyróżnione.](./media/disk-encryption/resize-lvm/047-resize-lvm-scenariof-pvs.png)

10. Zmień rozmiar PV:

    ``` bash
    pvresize /dev/mapper/devicemappername
    ```

    ![Zrzut ekranu przedstawiający kod, który zmienia rozmiar woluminu fizycznego. Wyniki są wyróżnione.](./media/disk-encryption/resize-lvm/048-resize-lvm-scenariof-resize-pv.png)

11. Sprawdź nowy rozmiar PV:

    ``` bash
    pvdisplay /dev/mapper/devicemappername
    ```

    ![Zrzut ekranu przedstawiający kod, który sprawdza rozmiar woluminu fizycznego. Wyniki są wyróżnione.](./media/disk-encryption/resize-lvm/049-resize-lvm-scenariof-pv.png)

12. Zmień rozmiar zaszyfrowanej warstwy na wa:

    ``` bash
    cryptsetup resize /dev/mapper/devicemappername
    ```

    Zastosuj tę samą procedurę dla wszystkich dysków, których rozmiar chcesz zmienić.

13. Sprawdź informacje o usłudze VG:

    ``` bash
    vgdisplay vgname
    ```

    ![Zrzut ekranu przedstawiający kod, który sprawdza informacje dla grupy woluminów. Wyniki są wyróżnione.](./media/disk-encryption/resize-lvm/050-resize-lvm-scenariof-vg.png)

    VG ma teraz wystarczającą ilość miejsca do przydzielenia do LVs.

14. Sprawdź informacje o LV:

    ``` bash
    lvdisplay vgname/lvname
    ```

    ![Zrzut ekranu przedstawiający kod, który sprawdza informacje dotyczące woluminu lokalnego. Wyniki są wyróżnione.](./media/disk-encryption/resize-lvm/051-resize-lvm-scenariof-lv.png)

15. Sprawdź użycie systemu plików:

    ``` bash
    df -h /mountpoint
    ```

    ![Zrzut ekranu przedstawiający kod sprawdzający użycie systemu plików. Wyniki są wyróżnione.](./media/disk-encryption/resize-lvm/052-resize-lvm-scenariof-fs.png)

16. Zmień rozmiar LV:

    ``` bash
    lvresize -r -L +2G /dev/vgname/lvname
    ```

    ![Zrzut ekranu przedstawiający kod, który zmienia rozmiar woluminu lokalnego. Wyniki są wyróżnione.](./media/disk-encryption/resize-lvm/053-resize-lvm-scenariof-lvresize.png)

    W tym miejscu użyto `-r` również opcji zmiany rozmiaru systemu plików.

17. Sprawdź informacje o LV:

    ``` bash
    lvdisplay vgname/lvname
    ```

    ![Zrzut ekranu przedstawiający kod, który pobiera informacje o woluminie lokalnym. Wyniki są wyróżnione.](./media/disk-encryption/resize-lvm/054-resize-lvm-scenariof-lvsize.png)

18. Sprawdź użycie systemu plików:

    ``` bash
    df -h /mountpoint
    ```

    ![Zrzut ekranu przedstawiający kod, który sprawdza użycie systemu plików. Wyniki są wyróżnione.](./media/disk-encryption/resize-lvm/055-resize-lvm-scenariof-fs.png)

Zastosuj tę samą procedurę zmiany wielkości do innych Łotwy, które go wymagają.

## <a name="next-steps"></a>Następne kroki

[Rozwiązywanie problemów Azure Disk Encryption](disk-encryption-troubleshooting.md)
