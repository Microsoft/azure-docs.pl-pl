---
title: Korzystanie z maszyny wirtualnej Rozwiązywanie problemów z systemem Linux przy użyciu interfejsu wiersza polecenia platformy Azure | Microsoft Docs
description: Dowiedz się, jak rozwiązywać problemy z maszyną wirtualną z systemem Linux, łącząc dysk systemu operacyjnego z maszyną wirtualną odzyskiwania przy użyciu interfejsu wiersza polecenia
services: virtual-machines-linux
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/16/2017
ms.author: genli
ms.openlocfilehash: 70855f7956e42d815e0b8135ccfe6e374853d8aa
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98197527"
---
# <a name="troubleshoot-a-linux-vm-by-attaching-the-os-disk-to-a-recovery-vm-with-the-azure-cli"></a>Rozwiązywanie problemów z maszyną wirtualną z systemem Linux przez dołączenie dysku systemu operacyjnego do maszyny wirtualnej odzyskiwania przy użyciu interfejsu wiersza polecenia Azure
Jeśli maszyna wirtualna z systemem Linux napotyka błąd rozruchu lub dysku, może być konieczne wykonanie kroków rozwiązywania problemów na wirtualnym dysku twardym. Typowym przykładem może być nieprawidłowy wpis w programie `/etc/fstab` , który uniemożliwia pomyślne uruchomienie maszyny wirtualnej. W tym artykule szczegółowo opisano sposób łączenia wirtualnego dysku twardego z inną maszyną wirtualną z systemem Linux przy użyciu interfejsu wiersza polecenia platformy Azure w celu usunięcia błędów, a następnie ponownego utworzenia oryginalnej maszyny wirtualnej. 

## <a name="recovery-process-overview"></a>Omówienie procesu odzyskiwania
Proces rozwiązywania problemów jest następujący:

1. Zatrzymaj zaatakowaną maszynę wirtualną.
1. Utwórz migawkę z dysku systemu operacyjnego maszyny wirtualnej.
1. Utwórz dysk na podstawie migawki dysku systemu operacyjnego.
1. Dołącz i zainstaluj nowy dysk systemu operacyjnego na innej maszynie wirtualnej z systemem Linux w celu rozwiązywania problemów.
1. Nawiąż połączenie z maszyną wirtualną rozwiązywania problemów. Edytuj pliki lub Uruchom dowolne narzędzia, aby rozwiązać problemy na nowym dysku systemu operacyjnego.
1. Odinstaluj i odłącz nowy dysk systemu operacyjnego z maszyny wirtualnej rozwiązywania problemów.
1. Zmień dysk systemu operacyjnego dla maszyny wirtualnej, której to dotyczy.

Aby wykonać te kroki rozwiązywania problemów, należy zainstalować najnowszy [interfejs wiersza polecenia platformy Azure](/cli/azure/install-az-cli2) i zalogować się do konta platformy Azure przy użyciu polecenia [AZ login](/cli/azure/reference-index).

Za pomocą poleceń naprawy maszyny wirtualnej można zautomatyzować kroki 1, 2, 3, 4, 6 i 7. Aby uzyskać więcej dokumentacji i instrukcji, zobacz temat [Naprawa maszyny wirtualnej z systemem Linux przy użyciu poleceń naprawy maszyny wirtualnej platformy Azure](repair-linux-vm-using-azure-virtual-machine-repair-commands.md).

> [!Important]
> Skrypty w tym artykule mają zastosowanie tylko do maszyn wirtualnych, które korzystają z [dysku zarządzanego](../managed-disks-overview.md). 

W poniższych przykładach Zastąp nazwy parametrów własnymi wartościami, takimi jak `myResourceGroup` i `myVM` .

## <a name="determine-boot-issues"></a>Określanie problemów z rozruchem
Sprawdź dane wyjściowe seryjnej, aby określić, dlaczego nie można prawidłowo uruchomić maszyny wirtualnej. Typowym przykładem jest nieprawidłowy wpis w `/etc/fstab` lub źródłowy wirtualny dysk twardy, który jest usuwany lub przenoszony.

Pobierz dzienniki rozruchowe za pomocą [AZ VM Boot-Diagnostics Get-Boot-Log](/cli/azure/vm/boot-diagnostics). Poniższy przykład pobiera dane wyjściowe seryjne z maszyny wirtualnej o nazwie `myVM` w grupie zasobów o nazwie `myResourceGroup` :

```azurecli
az vm boot-diagnostics get-boot-log --resource-group myResourceGroup --name myVM
```

Przejrzyj dane wyjściowe seryjnej, aby określić dlaczego rozruch maszyny wirtualnej kończy się niepowodzeniem. Jeśli dane wyjściowe seryjnej nie zapewniają żadnych wskazań, może być konieczne przejrzenie plików dziennika w programie `/var/log` po powiązaniu wirtualnego dysku twardego z maszyną wirtualną rozwiązywania problemów.

## <a name="stop-the-vm"></a>Zatrzymywanie maszyny wirtualnej

Poniższy przykład powoduje zatrzymanie maszyny wirtualnej o nazwie `myVM` z grupy zasobów o nazwie `myResourceGroup` :

```azurecli
az vm stop --resource-group MyResourceGroup --name MyVm
```
## <a name="take-a-snapshot-from-the-os-disk-of-the-affected-vm"></a>Utwórz migawkę z dysku systemu operacyjnego z zaatakowaną maszyną wirtualną

Migawka to pełna kopia tylko do odczytu dysku VHD. Nie można go dołączyć do maszyny wirtualnej. W następnym kroku utworzymy dysk na podstawie tej migawki. Poniższy przykład tworzy migawkę o nazwie `mySnapshot` z dysku systemu operacyjnego maszyny wirtualnej o nazwie "myVM". 

```azurecli
#Get the OS disk Id 
$osdiskid=(az vm show -g myResourceGroup -n myVM --query "storageProfile.osDisk.managedDisk.id" -o tsv)

#creates a snapshot of the disk
az snapshot create --resource-group myResourceGroupDisk --source "$osdiskid" --name mySnapshot
```
## <a name="create-a-disk-from-the-snapshot"></a>Utwórz dysk na podstawie migawki

Ten skrypt tworzy dysk zarządzany o nazwie `myOSDisk` z migawki o nazwie `mySnapshot` .  

```azurecli
#Provide the name of your resource group
$resourceGroup="myResourceGroup"

#Provide the name of the snapshot that will be used to create Managed Disks
$snapshot="mySnapshot"

#Provide the name of the Managed Disk
$osDisk="myNewOSDisk"

#Provide the size of the disks in GB. It should be greater than the VHD file size.
$diskSize=128

#Provide the storage type for Managed Disk. Premium_LRS or Standard_LRS.
$storageType="Premium_LRS"

#Provide the OS type
$osType="linux"

#Get the snapshot Id 
$snapshotId=(az snapshot show --name $snapshot --resource-group $resourceGroup --query id -o tsv)

# Create a new Managed Disks using the snapshot Id.

az disk create --resource-group $resourceGroup --name $osDisk --sku $storageType --size-gb $diskSize --source $snapshotId

```

Jeśli grupa zasobów i migawka źródłowa nie znajdują się w tym samym regionie, podczas uruchamiania zostanie wyświetlony komunikat o błędzie "nie można odnaleźć zasobu" `az disk create` . W takim przypadku należy określić, `--location <region>` Aby utworzyć dysk w tym samym regionie co migawka źródłowa.

Teraz masz kopię oryginalnego dysku systemu operacyjnego. Nowy dysk można zainstalować na innej maszynie wirtualnej z systemem Windows w celu rozwiązywania problemów.

## <a name="attach-the-new-virtual-hard-disk-to-another-vm"></a>Dołącz nowy wirtualny dysk twardy do innej maszyny wirtualnej
W następnych kilku krokach do rozwiązywania problemów służy inna maszyna wirtualna. Podłącz dysk do tej maszyny wirtualnej rozwiązywania problemów, aby przeglądać i edytować zawartość tego dysku. Ten proces umożliwia poprawienie wszystkich błędów konfiguracji lub przejrzenie dodatkowych plików dziennika aplikacji lub systemu.

Ten skrypt dołącza dysk `myNewOSDisk` do maszyny wirtualnej `MyTroubleshootVM` :

```azurecli
# Get ID of the OS disk that you just created.
$myNewOSDiskid=(az disk show -g $resourceGroup -n $osDisk --query id -o tsv)

# Attach the disk to the troubleshooting VM
az vm disk attach --disk $myNewOSDiskid --resource-group $resourceGroup --size-gb $diskSize --sku $storageType --vm-name MyTroubleshootVM
```
## <a name="mount-the-attached-data-disk"></a>Instalowanie dołączonego dysku z danymi

> [!NOTE]
> W poniższych przykładach szczegółowo opisano kroki wymagane na maszynie wirtualnej Ubuntu. Jeśli używasz innego dystrybucji systemu Linux, takiego jak Red Hat Enterprise Linux lub SUSE, lokalizacje i polecenia w pliku dziennika `mount` mogą być nieco inne. Zapoznaj się z dokumentacją dotyczącą określonych dystrybucji, aby uzyskać odpowiednie zmiany w poleceniach.

1. Użyj odpowiednich poświadczeń protokołu SSH do rozwiązywania problemów z maszyną wirtualną. Jeśli ten dysk jest pierwszym dyskiem danych dołączonym do maszyny wirtualnej rozwiązywania problemów, prawdopodobnie dysk jest podłączony do programu `/dev/sdc` . Użyj `dmesg` , aby wyświetlić dołączone dyski:

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

    W poprzednim przykładzie dysk systemu operacyjnego jest na dysku, `/dev/sda` a dysk tymczasowy podany dla każdej maszyny wirtualnej ma wartość `/dev/sdb` . Jeśli masz wiele dysków danych, powinny one znajdować się w `/dev/sdd` , `/dev/sde` i tak dalej.

2. Utwórz katalog, aby zainstalować istniejący wirtualny dysk twardy. Poniższy przykład tworzy katalog o nazwie `troubleshootingdisk` :

    ```bash
    sudo mkdir /mnt/troubleshootingdisk
    ```

3. Jeśli masz wiele partycji na istniejącym wirtualnym dysku twardym, zainstaluj wymaganą partycję. W poniższym przykładzie jest instalowana Pierwsza partycja podstawowa w `/dev/sdc1` :

    ```bash
    sudo mount /dev/sdc1 /mnt/troubleshootingdisk
    ```

    > [!NOTE]
    > Najlepszym rozwiązaniem jest zainstalowanie dysków z danymi na maszynach wirtualnych na platformie Azure przy użyciu uniwersalnego identyfikatora UUID dla wirtualnego dysku twardego. W przypadku tego krótkiego scenariusza rozwiązywania problemów zainstalowanie wirtualnego dysku twardego przy użyciu identyfikatora UUID nie jest konieczne. Jednak w normalnych warunkach Edycja `/etc/fstab` w celu zainstalowania wirtualnych dysków twardych przy użyciu nazwy urządzenia zamiast identyfikatora UUID może spowodować niepowodzenie rozruchu maszyny wirtualnej.


## <a name="fix-issues-on-the-new-os-disk"></a>Rozwiązywanie problemów z nowym dyskiem systemu operacyjnego
Po zainstalowaniu istniejącego wirtualnego dysku twardego można wykonać wszelkie czynności konserwacyjne i rozwiązywania problemów zgodnie z wymaganiami. Po usunięciu problemów wykonaj następujące kroki.


## <a name="unmount-and-detach-the-new-os-disk"></a>Odinstalowywanie i odłączanie nowego dysku systemu operacyjnego
Po rozwiązaniu problemów należy odinstalować i odłączyć istniejący wirtualny dysk twardy z maszyny wirtualnej rozwiązywania problemów. Nie można użyć wirtualnego dysku twardego z żadną inną maszyną wirtualną, dopóki dzierżawa nie dołączy wirtualnego dysku twardego do maszyny wirtualnej rozwiązywania problemów.

1. Odinstaluj istniejący wirtualny dysk twardy z sesji SSH do rozwiązywania problemów z maszyną wirtualną. Najpierw Zmień katalog nadrzędny dla punktu instalacji:

    ```bash
    cd /
    ```

    Odinstaluj już istniejący wirtualny dysk twardy. Poniższy przykład Odinstalowuje urządzenie w `/dev/sdc1` :

    ```bash
    sudo umount /dev/sdc1
    ```

2. Teraz Odłącz wirtualny dysk twardy od maszyny wirtualnej. Zakończ sesję SSH z maszyną wirtualną rozwiązywania problemów:

    ```azurecli
    az vm disk detach -g MyResourceGroup --vm-name MyTroubleShootVm --name myNewOSDisk
    ```

## <a name="change-the-os-disk-for-the-affected-vm"></a>Zmiana dysku systemu operacyjnego dla maszyny wirtualnej, której to dotyczy

Do wymiany dysków systemu operacyjnego można użyć interfejsu wiersza polecenia platformy Azure. Nie trzeba usuwać ani tworzyć ponownie maszyny wirtualnej.

Ten przykład powoduje zatrzymanie maszyny wirtualnej o nazwie `myVM` i przypisanie dysku o nazwie `myNewOSDisk` jako nowego dysku systemu operacyjnego.

```azurecli
# Stop the affected VM
az vm stop -n myVM -g myResourceGroup

# Get ID of the OS disk that is repaired.
$myNewOSDiskid=(az vm show -g $resourceGroup -n $osDisk --query id -o tsv)

# Change the OS disk of the affected VM to "myNewOSDisk"
az vm update -g myResourceGroup -n myVM --os-disk $myNewOSDiskid

# Start the VM
az vm start -n myVM -g myResourceGroup
```

## <a name="next-steps"></a>Następne kroki
Jeśli masz problemy z nawiązywaniem połączenia z maszyną wirtualną, zobacz [Rozwiązywanie problemów z połączeniami SSH z maszyną wirtualną platformy Azure](troubleshoot-ssh-connection.md). Problemy z uzyskiwaniem dostępu do aplikacji uruchomionych na maszynie wirtualnej można znaleźć [w temacie Rozwiązywanie problemów z łącznością aplikacji na maszynie wirtualnej z systemem Linux](troubleshoot-app-connection.md).

