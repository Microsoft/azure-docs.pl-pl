---
title: Rozszerzanie wirtualnych dysków twardych na maszynie wirtualnej z systemem Linux
description: Dowiedz się, jak rozszerzyć wirtualne dyski twarde na maszynie wirtualnej z systemem Linux przy użyciu interfejsu wiersza polecenia platformy Azure.
author: roygara
ms.service: virtual-machines
ms.collection: linux
ms.topic: how-to
ms.date: 10/15/2018
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: c27b042b78931fd58e43e4bbb06699abe510f385
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107762555"
---
# <a name="expand-virtual-hard-disks-on-a-linux-vm-with-the-azure-cli"></a>Rozszerzanie wirtualnych dysków twardych na maszynie wirtualnej z systemem Linux przy użyciu interfejsu wiersza polecenia platformy Azure

W tym artykule opisano sposób rozszerzania dysków zarządzanych dla maszyny wirtualnej z systemem Linux przy użyciu interfejsu wiersza polecenia platformy Azure. Możesz dodać [dyski danych, aby](add-disk.md) zapewnić dodatkowe miejsce do magazynowania, a także rozszerzyć istniejący dysk danych. Domyślny rozmiar wirtualnego dysku twardego dla systemu operacyjnego to zwykle 30 GB na maszynie wirtualnej z systemem Linux na platformie Azure. 

> [!WARNING]
> Zawsze upewnij się, że system plików jest w dobrej kondycji, typ tabeli partycji dysku będzie obsługiwać nowy rozmiar i przed wykonaniem operacji zmiany rozmiaru dysku upewnij się, że dane są wykonywane w kopii zapasowej. Aby uzyskać więcej informacji, zobacz [Azure Backup Szybki start.](../../backup/quick-backup-vm-portal.md) 

## <a name="expand-an-azure-managed-disk"></a>Rozszerzanie dysku zarządzanego platformy Azure
Upewnij się, że masz zainstalowany najnowszy interfejs wiersza polecenia platformy [Azure](/cli/azure/install-az-cli2) i że zalogowano się do konta platformy Azure przy użyciu polecenia [az login](/cli/azure/reference-index#az_login).

Ten artykuł wymaga istniejącej maszyny wirtualnej na platformie Azure z dołączonym i przygotowanym co najmniej jednym dyskiem danych. Jeśli nie masz jeszcze maszyny wirtualnej, z których możesz korzystać, zobacz Create and prepare a VM with data disks (Tworzenie i przygotowywanie [maszyny wirtualnej z dyskami danych).](tutorial-manage-disks.md#create-and-attach-disks)

W poniższych przykładach zastąp przykładowe nazwy parametrów, takie jak *myResourceGroup* *i myVM* własnymi wartościami.

1. Nie można wykonywać operacji na wirtualnych dyskach twardych, gdy maszyna wirtualna jest uruchomiona. Cofniesz alokację maszyny wirtualnej za pomocą [az vm deallocate](/cli/azure/vm#az_vm_deallocate). Poniższy przykład cofnie przydział maszyny wirtualnej o *nazwie myVM* w grupie zasobów *o nazwie myResourceGroup:*

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

    > [!NOTE]
    > Aby rozszerzyć wirtualny dysk twardy, należy cofnieć przydział maszyny wirtualnej. Zatrzymanie maszyny wirtualnej `az vm stop` za pomocą elementu nie zwalnia zasobów obliczeniowych. Aby zwolnić zasoby obliczeniowe, użyj elementu `az vm deallocate` .

1. Wyświetl listę dysków zarządzanych w grupie zasobów za pomocą [narzędzia az disk list.](/cli/azure/disk#az_disk_list) Poniższy przykład przedstawia listę dysków zarządzanych w grupie zasobów o nazwie *myResourceGroup*:

    ```azurecli
    az disk list \
        --resource-group myResourceGroup \
        --query '[*].{Name:name,Gb:diskSizeGb,Tier:accountType}' \
        --output table
    ```

    Rozwiń wymagany dysk za pomocą [narzędzia az disk update](/cli/azure/disk#az_disk_update). Poniższy przykład rozszerza dysk zarządzany o nazwie *myDataDisk* do *200* GB:

    ```azurecli
    az disk update \
        --resource-group myResourceGroup \
        --name myDataDisk \
        --size-gb 200
    ```

    > [!NOTE]
    > Po rozwinięciu dysku zarządzanego zaktualizowany rozmiar jest zaokrąglany w górę do najbliższego rozmiaru dysku zarządzanego. Aby uzyskać tabelę dostępnych rozmiarów i warstw dysków zarządzanych, zobacz Omówienie usługi [Azure Dyski zarządzane — cennik i rozliczenia.](../managed-disks-overview.md)

1. Uruchom maszynę wirtualną za pomocą [az vm start](/cli/azure/vm#az_vm_start). Poniższy przykład uruchamia maszynę wirtualną *o nazwie myVM* w grupie zasobów *o nazwie myResourceGroup:*

    ```azurecli
    az vm start --resource-group myResourceGroup --name myVM
    ```


## <a name="expand-a-disk-partition-and-filesystem"></a>Rozszerzanie partycji dysku i systemu plików
Aby użyć rozwiniętego dysku, rozwiń podstawową partycję i system plików.

1. Na maszynie wirtualnej ssh z odpowiednimi poświadczeniami. Publiczny adres IP maszyny wirtualnej można wyświetlić za pomocą az [vm show](/cli/azure/vm#az_vm_show):

    ```azurecli
    az vm show --resource-group myResourceGroup --name myVM -d --query [publicIps] --output tsv
    ```

1. Rozwiń podstawową partycję i system plików.

    a. Jeśli dysk jest już zainstalowany, odinstaluj go:

    ```bash
    sudo umount /dev/sdc1
    ```

    b. Użyj `parted` , aby wyświetlić informacje o dysku i zmienić rozmiar partycji:

    ```bash
    sudo parted /dev/sdc
    ```

    Wyświetl informacje o istniejącym układzie partycji za `print` pomocą . Dane wyjściowe są podobne do następującego przykładu, który pokazuje dysk podstawowy jest 215 GB:

    ```bash
    GNU Parted 3.2
    Using /dev/sdc1
    Welcome to GNU Parted! Type 'help' to view a list of commands.
    (parted) print
    Model: Unknown Msft Virtual Disk (scsi)
    Disk /dev/sdc1: 215GB
    Sector size (logical/physical): 512B/4096B
    Partition Table: loop
    Disk Flags:
    
    Number  Start  End    Size   File system  Flags
        1      0.00B  107GB  107GB  ext4
    ```

    c. Rozwiń partycję za pomocą . `resizepart` Wprowadź numer partycji, *1* i rozmiar nowej partycji:

    ```bash
    (parted) resizepart
    Partition number? 1
    End?  [107GB]? 215GB
    ```

    d. Aby zakończyć pracę, wprowadź `quit` .

1. Po zmianie rozmiaru partycji sprawdź spójność partycji za pomocą `e2fsck` :

    ```bash
    sudo e2fsck -f /dev/sdc1
    ```

1. Zmień rozmiar systemu plików za pomocą `resize2fs` :

    ```bash
    sudo resize2fs /dev/sdc1
    ```

1. Zainstaluj partycję w żądanej lokalizacji, takiej jak `/datadrive` :

    ```bash
    sudo mount /dev/sdc1 /datadrive
    ```

1. Aby sprawdzić, czy rozmiar dysku danych został zmieniony, `df -h` użyj . Następujące przykładowe dane wyjściowe pokazują, że dysk *danych /dev/sdc1* ma teraz 200 GB:

    ```bash
    Filesystem      Size   Used  Avail Use% Mounted on
    /dev/sdc1        197G   60M   187G   1% /datadrive
    ```

## <a name="next-steps"></a>Następne kroki
* Jeśli potrzebujesz dodatkowego magazynu, możesz również dodać dyski danych do [maszyny wirtualnej z systemem Linux.](add-disk.md) 
* Aby uzyskać więcej informacji na temat szyfrowania dysków, [zobacz Azure Disk Encryption dla maszyn wirtualnych z systemem Linux.](disk-encryption-overview.md)
