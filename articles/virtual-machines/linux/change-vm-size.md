---
title: Jak zmienić rozmiar maszyny wirtualnej z systemem Linux przy użyciu interfejsu wiersza polecenia platformy Azure
description: Jak skalować w górę lub w dół maszynę wirtualną z systemem Linux, zmieniając rozmiar maszyny wirtualnej.
author: DavidCBerry13
ms.service: virtual-machines
ms.topic: how-to
ms.date: 02/10/2017
ms.author: daberry
ms.collection: linux
ms.openlocfilehash: 290c42cadd840e5a292201247b1555059b5b4381
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102556642"
---
# <a name="resize-a-linux-virtual-machine-using-azure-cli"></a>Zmienianie rozmiaru maszyny wirtualnej z systemem Linux przy użyciu interfejsu wiersza polecenia platformy Azure 

Po udostępnieniu maszyny wirtualnej można skalować ją w górę lub w dół, zmieniając [rozmiar maszyny][vm-sizes]wirtualnej. W niektórych przypadkach należy najpierw cofnąć alokację maszyny wirtualnej. Należy cofnąć alokację maszyny wirtualnej, jeśli żądany rozmiar nie jest dostępny w klastrze sprzętowym hostującym maszynę wirtualną. W tym artykule szczegółowo opisano sposób zmiany rozmiaru maszyny wirtualnej z systemem Linux przy użyciu interfejsu wiersza polecenia platformy Azure. 

## <a name="resize-a-vm"></a>Zmienianie rozmiaru maszyny wirtualnej
Aby zmienić rozmiar maszyny wirtualnej, należy zainstalować najnowszy [interfejs wiersza polecenia platformy Azure](/cli/azure/install-az-cli2) i zalogować się na konto platformy Azure za pomocą polecenia [AZ login](/cli/azure/reference-index).

1. Wyświetl listę dostępnych rozmiarów maszyn wirtualnych w klastrze sprzętowym, w którym jest hostowana maszyna wirtualna za pomocą [AZ VM list-VM-size-Options](/cli/azure/vm). W poniższym przykładzie wymieniono rozmiary maszyn wirtualnych dla maszyny wirtualnej o nazwie `myVM` w `myResourceGroup` regionie grupy zasobów:
   
    ```azurecli
    az vm list-vm-resize-options --resource-group myResourceGroup --name myVM --output table
    ```

2. Jeśli żądany rozmiar maszyny wirtualnej znajduje się na liście, Zmień rozmiar maszyny wirtualnej za pomocą polecenie [AZ VM](/cli/azure/vm)size. Poniższy przykład zmienia rozmiar maszyny wirtualnej o nazwie `myVM` do `Standard_DS3_v2` rozmiaru:
   
    ```azurecli
    az vm resize --resource-group myResourceGroup --name myVM --size Standard_DS3_v2
    ```
   
    Maszyna wirtualna jest uruchamiana ponownie w trakcie tego procesu. Po ponownym uruchomieniu istniejące dyski systemu operacyjnego i danych są ponownie mapowane. Wszystkie elementy na dysku tymczasowym zostaną utracone.

3. Jeśli żądany rozmiar maszyny wirtualnej nie znajduje się na liście, musisz najpierw cofnąć przydział maszyny wirtualnej za pomocą polecenie [AZ VM deallocate](/cli/azure/vm). Ten proces umożliwia zmianę rozmiaru maszyny wirtualnej w dowolnym rozmiarze dostępnym dla regionu, a następnie rozpoczęcie. Wykonaj następujące kroki, aby cofnąć alokację, zmienić rozmiar, a następnie uruchom maszynę wirtualną o nazwie `myVM` w grupie zasobów o nazwie `myResourceGroup` :
   
    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    az vm resize --resource-group myResourceGroup --name myVM --size Standard_DS3_v2
    az vm start --resource-group myResourceGroup --name myVM
    ```
   
   > [!WARNING]
   > Cofnięcie przydziału maszyny wirtualnej spowoduje również wydanie wszystkich dynamicznych adresów IP przypisanych do maszyny wirtualnej. Nie dotyczy to dysków systemu operacyjnego i danych.

## <a name="next-steps"></a>Następne kroki
Aby uzyskać dodatkową skalowalność, należy uruchomić wiele wystąpień maszyn wirtualnych i skalować je w poziomie. Aby uzyskać więcej informacji, zobacz [Automatyczne skalowanie maszyn z systemem Linux w zestawie skalowania maszyn wirtualnych][scale-set]. 

<!-- links -->
[boot-diagnostics]: https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/
[scale-set]: ../../virtual-machine-scale-sets/tutorial-autoscale-cli.md 
[vm-sizes]:sizes.md
