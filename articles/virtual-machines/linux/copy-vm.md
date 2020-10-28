---
title: Kopiowanie maszyny wirtualnej z systemem Linux przy użyciu interfejsu wiersza polecenia platformy Azure
description: Dowiedz się, jak utworzyć kopię maszyny wirtualnej z systemem Linux platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure i Managed Disks.
author: cynthn
ms.service: virtual-machines-linux
ms.topic: how-to
ms.date: 10/17/2018
ms.author: cynthn
ms.custom: legacy, devx-track-azurecli
ms.openlocfilehash: f92f286fc9d9438331617cb567272a331834af42
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/27/2020
ms.locfileid: "92735388"
---
# <a name="create-a-copy-of-a-linux-vm-by-using-azure-cli-and-managed-disks"></a>Tworzenie kopii maszyny wirtualnej z systemem Linux przy użyciu interfejsu wiersza polecenia platformy Azure i Managed Disks

W tym artykule opisano sposób tworzenia kopii maszyny wirtualnej platformy Azure z systemem Linux przy użyciu interfejsu wiersza polecenia platformy Azure. Aby skopiować, utworzyć, zapisać i udostępnić obrazy maszyn wirtualnych na dużą skalę, zobacz [udostępnione Galerie obrazów](../shared-images-cli.md).

Możesz również [przekazać i utworzyć maszynę wirtualną z dysku VHD](upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="prerequisites"></a>Wymagania wstępne

-   Zainstaluj [interfejs wiersza polecenia platformy Azure](/cli/azure/install-az-cli2).

-   Zaloguj się do konta platformy Azure przy użyciu [AZ login](/cli/azure/reference-index#az-login).

-   Ma maszynę wirtualną platformy Azure, która będzie używana jako źródło kopii.

## <a name="stop-the-source-vm"></a>Zatrzymaj źródłową maszynę wirtualną

Cofnij przydział źródłowej maszyny wirtualnej przy użyciu polecenia [AZ VM deallocate](/cli/azure/vm#az-vm-deallocate).
Poniższy przykład powoduje cofnięcie przydziału maszyny wirtualnej o nazwie *myVM* *w grupie zasobów zasób:*

```azurecli
az vm deallocate \
    --resource-group myResourceGroup \
    --name myVM
```

## <a name="copy-the-source-vm"></a>Kopiuj źródłową maszynę wirtualną

Aby skopiować maszynę wirtualną, należy utworzyć kopię bazowego wirtualnego dysku twardego. Ten proces umożliwia utworzenie wyspecjalizowanego wirtualnego dysku twardego (VHD) jako dysku zarządzanego, który zawiera tę samą konfigurację i ustawienia co źródłowa maszyna wirtualna.

Aby uzyskać więcej informacji o dyskach funkcji Dyski zarządzane platformy Azure, zobacz [Omówienie funkcji Dyski zarządzane platformy Azure](../managed-disks-overview.md). 

1.  Wyświetl listę każdej maszyny wirtualnej i nazwę jej dysku systemu operacyjnego za pomocą [AZ VM list](/cli/azure/vm#az-vm-list). Poniższy przykład wyświetla listę wszystkich maszyn wirtualnych w grupie zasobów o nazwie Moja *zasobów* :
    
    ```azurecli
    az vm list -g myResourceGroup \
         --query '[].{Name:name,DiskName:storageProfile.osDisk.name}' \
         --output table
    ```

    Dane wyjściowe są podobne do poniższego przykładu:

    ```azurecli
    Name    DiskName
    ------  --------
    myVM    myDisk
    ```

1.  Skopiuj dysk, tworząc nowy dysk zarządzany i używając polecenia [AZ Disk Create](/cli/azure/disk#az-disk-create). Poniższy przykład tworzy dysk o nazwie *myCopiedDisk* z dysku zarządzanego o nazwie Moja *dysk* :

    ```azurecli
    az disk create --resource-group myResourceGroup \
         --name myCopiedDisk --source myDisk
    ``` 

1.  Sprawdź teraz dyski zarządzane w grupie zasobów za pomocą polecenia [AZ Disk list](/cli/azure/disk#az-disk-list). Poniższy przykład zawiera listę dysków zarządzanych w grupie zasobów o nazwie Moja *zasobów* :

    ```azurecli
    az disk list --resource-group myResourceGroup --output table
    ```


## <a name="set-up-a-virtual-network"></a>Konfigurowanie sieci wirtualnej

Następujące kroki opcjonalne tworzą nową sieć wirtualną, podsieć, publiczny adres IP i kartę sieci wirtualnej (NIC).

Jeśli kopiujesz maszynę wirtualną na potrzeby rozwiązywania problemów lub dodatkowych wdrożeń, możesz nie chcieć używać maszyny wirtualnej w istniejącej sieci wirtualnej.

Jeśli chcesz utworzyć infrastrukturę sieci wirtualnej dla skopiowanych maszyn wirtualnych, wykonaj kilka następnych kroków. Jeśli nie chcesz tworzyć sieci wirtualnej, Pomiń, aby [utworzyć maszynę](#create-a-vm)wirtualną.

1.  Utwórz sieć wirtualną za pomocą polecenia [AZ Network VNET Create](/cli/azure/network/vnet#az-network-vnet-create). Poniższy przykład tworzy sieć wirtualną o nazwie *myVnet* oraz podsieć o nazwie Moja *podsieć* :

    ```azurecli
    az network vnet create --resource-group myResourceGroup \
        --location eastus --name myVnet \
        --address-prefix 192.168.0.0/16 \
        --subnet-name mySubnet \
        --subnet-prefix 192.168.1.0/24
    ```

1.  Utwórz publiczny adres IP za pomocą polecenia [AZ Network Public-IP Create](/cli/azure/network/public-ip#az-network-public-ip-create). Poniższy przykład tworzy publiczny adres IP o nazwie *myPublicIP* z nazwą DNS *mypublicdns* . (Ponieważ nazwa DNS musi być unikatowa, podaj unikatową nazwę).

    ```azurecli
    az network public-ip create --resource-group myResourceGroup \
        --location eastus --name myPublicIP --dns-name mypublicdns \
        --allocation-method static --idle-timeout 4
    ```

1.  Utwórz kartę sieciową za pomocą polecenia [AZ Network nic Create](/cli/azure/network/nic#az-network-nic-create).
    Poniższy przykład tworzy kartę sieciową o nazwie *myNic* , która jest dołączona do podsieci z *podsiecią* :

    ```azurecli
    az network nic create --resource-group myResourceGroup \
        --location eastus --name myNic \
        --vnet-name myVnet --subnet mySubnet \
        --public-ip-address myPublicIP
    ```

## <a name="create-a-vm"></a>Tworzenie maszyny wirtualnej

Utwórz maszynę wirtualną za pomocą polecenia [AZ VM Create](/cli/azure/vm#az-vm-create).

Określ skopiowany dysk zarządzany do użycia jako dysk systemu operacyjnego ( `--attach-os-disk` ) w następujący sposób:

```azurecli
az vm create --resource-group myResourceGroup \
    --name myCopiedVM --nics myNic \
    --size Standard_DS1_v2 --os-type Linux \
    --attach-os-disk myCopiedDisk
```

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak używać [galerii obrazów udostępnionych](../shared-images-cli.md) do zarządzania obrazami maszyn wirtualnych.
