---
title: Rozszerzanie wirtualnych dysków twardych na maszynę wirtualną z systemem Linux
description: Dowiedz się, jak rozszerzyć wirtualne dyski twarde na maszynę wirtualną z systemem Linux przy użyciu interfejsu wiersza polecenia platformy Azure.
author: roygara
ms.service: virtual-machines
ms.collection: linux
ms.topic: how-to
ms.date: 10/15/2018
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 72778c431c561f5345dde3d6803e814d6fdebfba
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102549128"
---
# <a name="expand-virtual-hard-disks-on-a-linux-vm-with-the-azure-cli"></a>Rozszerzanie wirtualnych dysków twardych na maszynę wirtualną z systemem Linux przy użyciu interfejsu wiersza polecenia platformy Azure

W tym artykule opisano sposób rozszerzania dysków zarządzanych dla maszyny wirtualnej z systemem Linux przy użyciu interfejsu wiersza polecenia platformy Azure. Można [dodać dyski danych](add-disk.md) , aby zapewnić dodatkowe miejsce do magazynowania, i można również rozszerzyć istniejący dysk danych. Domyślny rozmiar wirtualnego dysku twardego dla systemu operacyjnego (OS) jest zwykle 30 GB na maszynie wirtualnej z systemem Linux na platformie Azure. 

> [!WARNING]
> Zawsze upewnij się, że system plików jest w dobrej kondycji, typ tabeli partycji dysku będzie obsługiwał nowy rozmiar i upewnij się, że kopie zapasowe danych zostały wykonane przed wykonaniem operacji zmiany rozmiaru dysku. Aby uzyskać więcej informacji, zobacz [Azure Backup przewodnika Szybki Start](../../backup/quick-backup-vm-portal.md). 

## <a name="expand-an-azure-managed-disk"></a>Rozszerzanie dysku zarządzanego platformy Azure
Upewnij się, że zainstalowano najnowszy [interfejs wiersza polecenia platformy Azure](/cli/azure/install-az-cli2) i są one zalogowane do konta platformy Azure przy użyciu polecenia [AZ login](/cli/azure/reference-index#az-login).

Ten artykuł wymaga istniejącej maszyny wirtualnej na platformie Azure z co najmniej jednym dyskiem danych dołączonym i przygotowanym. Jeśli nie masz jeszcze maszyny wirtualnej, której możesz użyć, zobacz [Tworzenie i przygotowywanie maszyny wirtualnej z dyskami danych](tutorial-manage-disks.md#create-and-attach-disks).

W poniższych przykładach Zastąp przykładowe nazwy parametrów, takie jak *myVM* *i, z* własnymi wartościami.

1. Nie można wykonać operacji na wirtualnych dyskach twardych z uruchomioną maszyną wirtualną. Cofnij przydział maszyny wirtualnej za pomocą [AZ VM deallocate](/cli/azure/vm#az-vm-deallocate). Poniższy przykład powoduje cofnięcie przydziału maszyny wirtualnej o nazwie *myVM* w grupie zasobów o nazwie Moja *zasobów*:

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

    > [!NOTE]
    > Aby zwiększyć rozmiar wirtualnego dysku twardego, należy cofnąć przydział maszyny wirtualnej. Zatrzymywanie maszyny wirtualnej przy użyciu programu nie `az vm stop` powoduje zwolnienia zasobów obliczeniowych. Aby zwolnić zasoby obliczeniowe, użyj programu `az vm deallocate` .

1. Wyświetl listę dysków zarządzanych w grupie zasobów za pomocą [AZ Disk list](/cli/azure/disk#az-disk-list). Poniższy przykład przedstawia listę dysków zarządzanych w grupie zasobów o nazwie Moja *zasobów*:

    ```azurecli
    az disk list \
        --resource-group myResourceGroup \
        --query '[*].{Name:name,Gb:diskSizeGb,Tier:accountType}' \
        --output table
    ```

    Rozwiń żądany dysk za pomocą polecenie [AZ Disk Update](/cli/azure/disk#az-disk-update). Poniższy przykład rozszerza dysk zarządzany o nazwie *myDataDisk* do *200* GB:

    ```azurecli
    az disk update \
        --resource-group myResourceGroup \
        --name myDataDisk \
        --size-gb 200
    ```

    > [!NOTE]
    > Po rozwinięciu dysku zarządzanego zaktualizowany rozmiar jest zaokrąglany do najbliższego rozmiaru dysku zarządzanego. Tabelę dostępnych rozmiarów i warstw dysków zarządzanych można znaleźć w temacie [Omówienie usługi Azure Managed disks — Cennik i rozliczenia](../managed-disks-overview.md).

1. Uruchom maszynę wirtualną za pomocą [AZ VM Start](/cli/azure/vm#az-vm-start). Poniższy przykład uruchamia maszynę wirtualną o nazwie *myVM* w grupie zasobów o nazwie Moja *zasobów*:

    ```azurecli
    az vm start --resource-group myResourceGroup --name myVM
    ```


## <a name="expand-a-disk-partition-and-filesystem"></a>Rozszerzanie partycji dyskowej i systemu plików
Aby użyć rozszerzonego dysku, rozwiń podstawową partycję i system plików.

1. Użyj protokołu SSH do maszyny wirtualnej z odpowiednimi poświadczeniami. Publiczny adres IP maszyny wirtualnej można wyświetlić za pomocą [AZ VM show](/cli/azure/vm#az-vm-show):

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

    Wyświetl informacje o istniejącym układzie partycji za pomocą programu `print` . Dane wyjściowe są podobne do poniższego przykładu, który pokazuje, że dysk podstawowy to 215 GB:

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

    c. Rozwiń partycję za pomocą `resizepart` . Wprowadź numer partycji, *1* i rozmiar nowej partycji:

    ```bash
    (parted) resizepart
    Partition number? 1
    End?  [107GB]? 215GB
    ```

    d. Aby wyjść, wprowadź `quit` .

1. Po zmianie rozmiaru partycji Sprawdź spójność partycji z `e2fsck` :

    ```bash
    sudo e2fsck -f /dev/sdc1
    ```

1. Zmień rozmiar systemu plików przy użyciu `resize2fs` :

    ```bash
    sudo resize2fs /dev/sdc1
    ```

1. Zainstaluj partycję w odpowiedniej lokalizacji, na przykład `/datadrive` :

    ```bash
    sudo mount /dev/sdc1 /datadrive
    ```

1. Aby sprawdzić, czy rozmiar dysku danych został zmieniony, użyj `df -h` . Następujące przykładowe dane wyjściowe przedstawiają teraz 200  GB:

    ```bash
    Filesystem      Size   Used  Avail Use% Mounted on
    /dev/sdc1        197G   60M   187G   1% /datadrive
    ```

## <a name="next-steps"></a>Następne kroki
* Jeśli potrzebujesz dodatkowego magazynu, możesz również [dodać dyski danych do maszyny wirtualnej z systemem Linux](add-disk.md). 
* Aby uzyskać więcej informacji na temat szyfrowania dysków, zobacz [Azure Disk Encryption dla maszyn wirtualnych z systemem Linux](disk-encryption-overview.md).
