---
title: Konfigurowanie LVM i RAID na zaszyfrowanych urządzeniach — Azure Disk Encryption
description: Ten artykuł zawiera instrukcje dotyczące konfigurowania LVM i RAID na zaszyfrowanych urządzeniach dla maszyn wirtualnych z systemem Linux.
author: jofrance
ms.service: virtual-machines
ms.subservice: disks
ms.collection: linux
ms.topic: how-to
ms.author: jofrance
ms.date: 03/17/2020
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: bdd897e76df941130e3acdf9c30ea8edd41147e9
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104601928"
---
# <a name="configure-lvm-and-raid-on-encrypted-devices"></a>Konfigurowanie LVM i RAID na zaszyfrowanych urządzeniach

W tym artykule przedstawiono krok po kroku proces wykonywania operacji zarządzania woluminami logicznymi (LVM) i RAID na zaszyfrowanych urządzeniach. Ten proces ma zastosowanie do następujących środowisk:

- Dystrybucje systemu Linux
    - RHEL 7.6 +
    - Ubuntu 18.04 +
    - SUSE 12 +
- Azure Disk Encryption rozszerzenie z jednym przebiegiem
- Azure Disk Encryption rozszerzenie o podwójnej przejściu


## <a name="scenarios"></a>Scenariusze

Procedury opisane w tym artykule obsługują następujące scenariusze:  

- Konfigurowanie LVM na urządzeniach szyfrowanych (LVM-on-Crypt)
- Konfigurowanie macierzy RAID na urządzeniach szyfrowanych (RAID-on-Crypt)

Po zaszyfrowaniu urządzenia lub urządzeń źródłowych można utworzyć struktury LVM lub RAID na podstawie tej warstwy zaszyfrowanej. 

Woluminy fizyczne (PVs) są tworzone w warstwie zaszyfrowanej. Woluminy fizyczne są używane do tworzenia grupy woluminów. Tworzysz woluminy i Dodaj wymagane wpisy w katalogu/etc/fstab. 

![Diagram warstw struktur LVM](./media/disk-encryption/lvm-raid-on-crypt/000-lvm-raid-crypt-diagram.png)

W podobny sposób urządzenie RAID jest tworzone na podstawie warstwy zaszyfrowanej na dyskach. System plików jest tworzony na urządzeniu RAID i dodawany do/etc/fstab jako zwykłe urządzenie.

## <a name="considerations"></a>Zagadnienia do rozważenia

Zalecamy użycie LVM-on-Crypt. RAID to opcja, gdy nie można użyć LVM z powodu określonych ograniczeń aplikacji lub środowiska.

Zostanie użyta opcja **EncryptFormatAll** . Aby uzyskać więcej informacji na temat tej opcji, zobacz [Korzystanie z funkcji EncryptFormatAll w przypadku dysków z danymi na maszynach wirtualnych z systemem Linux](./disk-encryption-linux.md#use-encryptformatall-feature-for-data-disks-on-linux-vms).

Mimo że można użyć tej metody w przypadku szyfrowania systemu operacyjnego, obecnie szyfrujemy dyski danych.

W procedurach przyjęto założenie, że wymagania wstępne zostały już sprawdzone w [Azure Disk Encryption scenariuszach dotyczących maszyn wirtualnych z systemem Linux](./disk-encryption-linux.md) i w [ramach szybkiego startu: Tworzenie i szyfrowanie maszyny wirtualnej z systemem Linux przy użyciu interfejsu wiersza polecenia](./disk-encryption-cli-quickstart.md)

Wersja Azure Disk Encryption podwójnego przebiegu znajduje się na ścieżce przestarzałej i nie powinna być już używana w przypadku nowych szyfrowania.

## <a name="general-steps"></a>Ogólne kroki

W przypadku korzystania z konfiguracji "on-Crypt" należy użyć procesu przedstawionego w poniższych procedurach.

>[!NOTE] 
>Stosujemy zmienne w całym artykule. Zastąp odpowiednie wartości.

### <a name="deploy-a-vm"></a>Wdrażanie maszyny wirtualnej 
Następujące polecenia są opcjonalne, ale zalecamy ich zastosowanie na nowo wdrożonej maszynie wirtualnej.

Program PowerShell:

```powershell
New-AzVm -ResourceGroupName ${RGNAME} `
-Name ${VMNAME} `
-Location ${LOCATION} `
-Size ${VMSIZE} `
-Image ${OSIMAGE} `
-Credential ${creds} `
-Verbose
```
Interfejs wiersza polecenia platformy Azure:

```azurecli
az vm create \
-n ${VMNAME} \
-g ${RGNAME} \
--image ${OSIMAGE} \
--admin-username ${username} \
--admin-password ${password} \
-l ${LOCATION} \
--size ${VMSIZE} \
-o table
```
### <a name="attach-disks-to-the-vm"></a>Dołączanie dysków do maszyny wirtualnej
Powtórz następujące polecenia, aby uzyskać `$N` liczbę nowych dysków, które mają zostać dołączone do maszyny wirtualnej.

Program PowerShell:

```powershell
$storageType = 'Standard_LRS'
$dataDiskName = ${VMNAME} + '_datadisk0'
$diskConfig = New-AzDiskConfig -SkuName $storageType -Location $LOCATION -CreateOption Empty -DiskSizeGB 5
$dataDisk1 = New-AzDisk -DiskName $dataDiskName -Disk $diskConfig -ResourceGroupName ${RGNAME}
$vm = Get-AzVM -Name ${VMNAME} -ResourceGroupName ${RGNAME} 
$vm = Add-AzVMDataDisk -VM $vm -Name $dataDiskName -CreateOption Attach -ManagedDiskId $dataDisk1.Id -Lun 0
Update-AzVM -VM ${VM} -ResourceGroupName ${RGNAME}
```

Interfejs wiersza polecenia platformy Azure:

```azurecli
az vm disk attach \
-g ${RGNAME} \
--vm-name ${VMNAME} \
--name ${VMNAME}datadisk1 \
--size-gb 5 \
--new \
-o table
```

### <a name="verify-that-the-disks-are-attached-to-the-vm"></a>Sprawdź, czy dyski są dołączone do maszyny wirtualnej
Program PowerShell:
```powershell
$VM = Get-AzVM -ResourceGroupName ${RGNAME} -Name ${VMNAME}
$VM.StorageProfile.DataDisks | Select-Object Lun,Name,DiskSizeGB
```
![Lista dołączonych dysków w programie PowerShell](./media/disk-encryption/lvm-raid-on-crypt/001-lvm-raid-check-disks-powershell.png)

Interfejs wiersza polecenia platformy Azure:

```azurecli
az vm show -g ${RGNAME} -n ${VMNAME} --query storageProfile.dataDisks -o table
```
![Lista dołączonych dysków w interfejsie wiersza polecenia platformy Azure](./media/disk-encryption/lvm-raid-on-crypt/002-lvm-raid-check-disks-cli.png)

Portal:

![Lista podłączonych dysków w portalu](./media/disk-encryption/lvm-raid-on-crypt/003-lvm-raid-check-disks-portal.png)

System operacyjny:

```bash
lsblk 
```
![Lista dołączonych dysków w systemie operacyjnym](./media/disk-encryption/lvm-raid-on-crypt/004-lvm-raid-check-disks-os.png)

### <a name="configure-the-disks-to-be-encrypted"></a>Skonfiguruj szyfrowanie dysków
Ta konfiguracja odbywa się na poziomie systemu operacyjnego. Odpowiednie dyski są skonfigurowane do tradycyjnego szyfrowania za pomocą Azure Disk Encryption:

- Systemy plików są tworzone na dyskach.
- Tymczasowe punkty instalacji są tworzone w celu zainstalowania systemów plików.
- Systemy plików są konfigurowane w systemie/etc/fstab, aby można je było instalować w czasie rozruchu.

Sprawdź literę urządzenia przypisaną do nowych dysków. W tym przykładzie używane są cztery dyski danych.

```bash
lsblk 
```
![Dyski danych dołączone do systemu operacyjnego](./media/disk-encryption/lvm-raid-on-crypt/004-lvm-raid-check-disks-os.png)

### <a name="create-a-file-system-on-top-of-each-disk"></a>Utwórz system plików na każdym dysku
To polecenie iteruje Tworzenie systemu plików ext4 na każdym dysku zdefiniowanym w części "w" cyklu "for".

```bash
for disk in c d e f; do echo mkfs.ext4 -F /dev/sd${disk}; done |bash
```
![Tworzenie systemu plików ext4](./media/disk-encryption/lvm-raid-on-crypt/005-lvm-raid-create-temp-fs.png)

Znajdź unikatowy identyfikator uniwersalny (UUID) systemów plików, które zostały ostatnio utworzone, Utwórz folder tymczasowy, Dodaj odpowiednie wpisy w/etc/fstab i zainstaluj wszystkie systemy plików.

To polecenie wykonuje także iterację na każdym dysku zdefiniowanym w części "w" cyklu "for":

```bash
for disk in c d e f; do diskuuid="$(blkid -s UUID -o value /dev/sd${disk})"; \
mkdir /tempdata${disk}; \
echo "UUID=${diskuuid} /tempdata${disk} ext4 defaults,nofail 0 0" >> /etc/fstab; \
mount -a; \
done
``` 

### <a name="verify-that-the-disks-are-mounted-properly"></a>Sprawdź, czy dyski są prawidłowo zainstalowane
```bash
lsblk
```
![Lista zainstalowanych systemów plików tymczasowych](./media/disk-encryption/lvm-raid-on-crypt/006-lvm-raid-verify-temp-fs.png)

Sprawdź również, czy dyski są skonfigurowane:

```bash
cat /etc/fstab
```
![Informacje o konfiguracji za pośrednictwem fstab](./media/disk-encryption/lvm-raid-on-crypt/007-lvm-raid-verify-temp-fstab.png)

### <a name="encrypt-the-data-disks"></a>Szyfrowanie dysków danych
Program PowerShell przy użyciu klucza szyfrowania klucza (KEK):

```powershell
$sequenceVersion = [Guid]::NewGuid() 
Set-AzVMDiskEncryptionExtension -ResourceGroupName $RGNAME `
-VMName ${VMNAME} `
-DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl `
-DiskEncryptionKeyVaultId $KeyVaultResourceId `
-KeyEncryptionKeyUrl $keyEncryptionKeyUrl `
-KeyEncryptionKeyVaultId $KeyVaultResourceId `
-VolumeType 'DATA' `
-EncryptFormatAll `
-SequenceVersion $sequenceVersion `
-skipVmBackup;
```

Interfejs wiersza polecenia platformy Azure korzystający z KEK:

```azurecli
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
### <a name="verify-the-encryption-status"></a>Weryfikowanie stanu szyfrowania

Przejdź do następnego kroku tylko wtedy, gdy wszystkie dyski są zaszyfrowane.

Program PowerShell:

```powershell
Get-AzVmDiskEncryptionStatus -ResourceGroupName ${RGNAME} -VMName ${VMNAME}
```
![Stan szyfrowania w programie PowerShell](./media/disk-encryption/lvm-raid-on-crypt/008-lvm-raid-verify-encryption-status-ps.png)

Interfejs wiersza polecenia platformy Azure:

```azurecli
az vm encryption show -n ${VMNAME} -g ${RGNAME} -o table
```
![Stan szyfrowania w interfejsie wiersza polecenia platformy Azure](./media/disk-encryption/lvm-raid-on-crypt/009-lvm-raid-verify-encryption-status-cli.png)

Portal:

![Stan szyfrowania w portalu](./media/disk-encryption/lvm-raid-on-crypt/010-lvm-raid-verify-encryption-status-portal.png)

Poziom systemu operacyjnego:

```bash
lsblk
```
![Stan szyfrowania w systemie operacyjnym](./media/disk-encryption/lvm-raid-on-crypt/011-lvm-raid-verify-encryption-status-os.png)

Rozszerzenie doda systemy plików do/var/lib/azure_disk_encryption_config/azure_crypt_mount (stare szyfrowanie) lub do/etc/crypttab (nowe szyfrowania).

>[!NOTE] 
>Nie należy modyfikować żadnego z tych plików.

Ten plik zajmie się aktywowaniem tych dysków podczas procesu rozruchu, dzięki czemu program LVM lub RAID będzie mógł użyć ich później. 

Nie martw się o punkty instalacji tego pliku. Azure Disk Encryption utraci możliwość pobrania dysków zainstalowanych jako normalny system plików po utworzeniu woluminu fizycznego lub urządzenia RAID na tych zaszyfrowanych urządzeniach. (Spowoduje to usunięcie formatu systemu plików, który został użyty podczas procesu przygotowania).

### <a name="remove-the-temporary-folders-and-temporary-fstab-entries"></a>Usuwanie folderów tymczasowych i tymczasowych wpisów fstab
Należy odinstalować systemy plików na dyskach, które będą używane jako część LVM.

```bash
for disk in c d e f; do umount /tempdata${disk}; done
```
I Usuń wpisy/etc/fstab:

```bash
vi /etc/fstab
```
### <a name="verify-that-the-disks-are-not-mounted-and-that-the-entries-on-etcfstab-were-removed"></a>Sprawdź, czy dyski nie są zainstalowane i czy wpisy w/etc/fstab zostały usunięte

```bash
lsblk
```
![Weryfikowanie, czy tymczasowe systemy plików są odinstalowywane](./media/disk-encryption/lvm-raid-on-crypt/012-lvm-raid-verify-disks-not-mounted.png)

I sprawdź, czy dyski są skonfigurowane:
```bash
cat /etc/fstab
```
![Weryfikacja, czy tymczasowe wpisy fstab są usuwane](./media/disk-encryption/lvm-raid-on-crypt/013-lvm-raid-verify-fstab-temp-removed.png)

## <a name="steps-for-lvm-on-crypt"></a>Kroki dla LVM-on-Crypt
Teraz, gdy dyski bazowe są zaszyfrowane, można utworzyć struktury LVM.

Zamiast używać nazwy urządzenia, należy użyć ścieżek/dev/mapper dla każdego z dysków do utworzenia woluminu fizycznego (na warstwie Crypt na dysku, a nie na dysku).

### <a name="configure-lvm-on-top-of-the-encrypted-layers"></a>Skonfiguruj LVM na podstawie zaszyfrowanych warstw
#### <a name="create-the-physical-volumes"></a>Tworzenie woluminów fizycznych
Zostanie wyświetlone ostrzeżenie z pytaniem, czy można wyczyścić sygnaturę systemu plików. Kontynuuj, wprowadzając wartość **y** lub Użyj **ECHA "y"** , jak pokazano poniżej:

```bash
echo "y" | pvcreate /dev/mapper/c49ff535-1df9-45ad-9dad-f0846509f052
echo "y" | pvcreate /dev/mapper/6712ad6f-65ce-487b-aa52-462f381611a1
echo "y" | pvcreate /dev/mapper/ea607dfd-c396-48d6-bc54-603cf741bc2a
echo "y" | pvcreate /dev/mapper/4159c60a-a546-455b-985f-92865d51158c
```
![Weryfikowanie, czy wolumin fizyczny został utworzony](./media/disk-encryption/lvm-raid-on-crypt/014-lvm-raid-pvcreate.png)

>[!NOTE] 
>Nazwy/dev/mapper/Device należy wymienić na rzeczywiste wartości na podstawie danych wyjściowych **lsblk**.

#### <a name="verify-the-information-for-physical-volumes"></a>Weryfikowanie informacji o woluminach fizycznych
```bash
pvs
```

![Informacje dotyczące woluminów fizycznych](./media/disk-encryption/lvm-raid-on-crypt/015-lvm-raid-pvs.png)

#### <a name="create-the-volume-group"></a>Utwórz grupę woluminów
Utwórz grupę woluminów przy użyciu tych samych urządzeń, które zostały już zainicjowane:

```bash
vgcreate vgdata /dev/mapper/
```

### <a name="check-the-information-for-the-volume-group"></a>Sprawdź informacje o grupie woluminów

```bash
vgdisplay -v vgdata
```
```bash
pvs
```
![Informacje o grupie woluminów](./media/disk-encryption/lvm-raid-on-crypt/016-lvm-raid-pvs-on-vg.png)

#### <a name="create-logical-volumes"></a>Utwórz woluminy logiczne

```bash
lvcreate -L 10G -n lvdata1 vgdata
lvcreate -L 7G -n lvdata2 vgdata
``` 

#### <a name="check-the-created-logical-volumes"></a>Sprawdź utworzone woluminy logiczne

```bash
lvdisplay
lvdisplay vgdata/lvdata1
lvdisplay vgdata/lvdata2
```
![Informacje dotyczące woluminów logicznych](./media/disk-encryption/lvm-raid-on-crypt/017-lvm-raid-lvs.png)

#### <a name="create-file-systems-on-top-of-the-structures-for-logical-volumes"></a>Tworzenie systemów plików na podstawie struktur dla woluminów logicznych

```bash
echo "yes" | mkfs.ext4 /dev/vgdata/lvdata1
echo "yes" | mkfs.ext4 /dev/vgdata/lvdata2
```

#### <a name="create-the-mount-points-for-the-new-file-systems"></a>Utwórz punkty instalacji dla nowych systemów plików

```bash
mkdir /data0
mkdir /data1
```

#### <a name="add-the-new-file-systems-to-etcfstab-and-mount-them"></a>Dodaj nowe systemy plików do/etc/fstab i zainstaluj je

```bash
echo "/dev/mapper/vgdata-lvdata1 /data0 ext4 defaults,nofail 0 0" >>/etc/fstab
echo "/dev/mapper/vgdata-lvdata2 /data1 ext4 defaults,nofail 0 0" >>/etc/fstab
mount -a
```

#### <a name="verify-that-the-new-file-systems-are-mounted"></a>Sprawdź, czy nowe systemy plików są zainstalowane

```bash
lsblk -fs
df -h
```
![Zrzut ekranu przedstawia okno konsoli z systemami plików zainstalowanymi jako data0 i dane1.](./media/disk-encryption/lvm-raid-on-crypt/018-lvm-raid-lsblk-after-lvm.png)

W tej odmianie **lsblk** wymieniamy urządzenia pokazujące zależności w kolejności odwrotnej. Ta opcja pomaga identyfikować urządzenia pogrupowane według woluminu logicznego zamiast oryginalnych nazw urządzeń/dev/SD [Disk].

Ważne jest, aby upewnić się, że opcja **nofail** została dodana do opcji punktu instalacji woluminów LVM utworzonych na podstawie urządzenia zaszyfrowanego za pomocą Azure Disk Encryption. Zapobiega to zawieszeniu systemu operacyjnego podczas procesu rozruchu (lub w trybie konserwacji).

Jeśli nie używasz opcji **nofail** :

- System operacyjny nigdy nie przejdzie do etapu, w którym uruchomiono Azure Disk Encryption, a dyski danych zostaną odblokowane i zainstalowane. 
- Zaszyfrowane dyski zostaną odblokowane po zakończeniu procesu rozruchu. Woluminy LVM i systemy plików zostaną automatycznie zainstalowane do momentu odblokowania ich przez Azure Disk Encryption. 

Można testować ponownie rozruch maszyny wirtualnej i sprawdzać, czy systemy plików są również automatycznie instalowane po rozruchu. Ten proces może potrwać kilka minut, w zależności od liczby i rozmiarów systemów plików.

#### <a name="reboot-the-vm-and-verify-after-reboot"></a>Uruchom ponownie maszynę wirtualną i sprawdź po ponownym uruchomieniu

```bash
shutdown -r now
```
```bash
lsblk
df -h
```
## <a name="steps-for-raid-on-crypt"></a>Kroki dla macierzy RAID-on-Crypt
Teraz, gdy dyski bazowe są zaszyfrowane, można nadal tworzyć struktury RAID. Ten proces jest taki sam jak dla LVM, ale zamiast używać nazwy urządzenia, należy użyć ścieżek/dev/mapper dla każdego dysku.

#### <a name="configure-raid-on-top-of-the-encrypted-layer-of-the-disks"></a>Skonfiguruj platformę RAID na podstawie zaszyfrowanej warstwy dysków
```bash
mdadm --create /dev/md10 \
--level 0 \
--raid-devices=4 \
/dev/mapper/c49ff535-1df9-45ad-9dad-f0846509f052 \
/dev/mapper/6712ad6f-65ce-487b-aa52-462f381611a1 \
/dev/mapper/ea607dfd-c396-48d6-bc54-603cf741bc2a \
/dev/mapper/4159c60a-a546-455b-985f-92865d51158c
```
![Informacje dotyczące skonfigurowanej macierzy RAID za pośrednictwem polecenia mdadm](./media/disk-encryption/lvm-raid-on-crypt/019-lvm-raid-md-creation.png)

>[!NOTE] 
>Nazwy/dev/mapper/Device należy zastąpić wartościami rzeczywistymi na podstawie danych wyjściowych **lsblk**.

### <a name="checkmonitor-raid-creation"></a>Sprawdź/Monitoruj tworzenie macierzy RAID
```bash
watch -n1 cat /proc/mdstat
mdadm --examine /dev/mapper/[]
mdadm --detail /dev/md10
```
![Stan macierzy RAID](./media/disk-encryption/lvm-raid-on-crypt/020-lvm-raid-md-details.png)

### <a name="create-a-file-system-on-top-of-the-new-raid-device"></a>Utwórz system plików na nowym urządzeniu RAID
```bash
mkfs.ext4 /dev/md10
```

Utwórz nowy punkt instalacji dla systemu plików, Dodaj nowy system plików do/etc/fstab i zainstaluj go:

>[!NOTE] 
>Ten cykl iteruje tylko na jednym urządzeniu na potrzeby tego konkretnego przykładu, w razie potrzeby, ma być używany w przypadku wielu urządzeń MD.

```bash
for device in md10; do diskuuid="$(blkid -s UUID -o value /dev/${device})"; \
mkdir /raiddata; \
echo "UUID=${diskuuid} /raiddata ext4 defaults,nofail 0 0" >> /etc/fstab; \
mount -a; \
done
```

Sprawdź, czy nowy system plików jest zainstalowany:

```bash
lsblk -fs
df -h
```
![Zrzut ekranu przedstawia okno konsoli z systemem plików zainstalowanym jako raiddata.](./media/disk-encryption/lvm-raid-on-crypt/021-lvm-raid-lsblk-md-details.png)

Ważne jest, aby upewnić się, że opcja **nofail** została dodana do opcji punktu instalacji woluminów RAID utworzonych na podstawie urządzenia zaszyfrowanego za pomocą Azure Disk Encryption. Zapobiega to zawieszeniu systemu operacyjnego podczas procesu rozruchu (lub w trybie konserwacji).

Jeśli nie używasz opcji **nofail** :

- System operacyjny nigdy nie przejdzie do etapu, w którym uruchomiono Azure Disk Encryption, a dyski danych zostaną odblokowane i zainstalowane.
- Zaszyfrowane dyski zostaną odblokowane po zakończeniu procesu rozruchu. Woluminy RAID i systemy plików zostaną automatycznie zainstalowane do momentu odblokowania ich przez Azure Disk Encryption.

Można testować ponownie rozruch maszyny wirtualnej i sprawdzać, czy systemy plików są również automatycznie instalowane po rozruchu. Ten proces może potrwać kilka minut, w zależności od liczby i rozmiarów systemów plików.

```bash
shutdown -r now
```

I kiedy można się zalogować:

```bash
lsblk
df -h
```
## <a name="next-steps"></a>Następne kroki

- [Zmień rozmiar urządzeń z zarządzaniem woluminami logicznymi zaszyfrowanymi za pomocą Azure Disk Encryption](how-to-resize-encrypted-lvm.md)
- [Rozwiązywanie problemów z usługą Azure Disk Encryption](disk-encryption-troubleshooting.md)
