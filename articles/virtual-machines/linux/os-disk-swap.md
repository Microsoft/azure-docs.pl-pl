---
title: Zamiana między dyskami systemu operacyjnego przy użyciu interfejsu wiersza polecenia platformy Azure "
description: Zmień dysk systemu operacyjnego używany przez maszynę wirtualną platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure.
author: cynthn
ms.service: virtual-machines
ms.subservice: disks
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 04/24/2018
ms.author: cynthn
ms.custom: devx-track-azurecli
ms.openlocfilehash: 0c9b0c1948dc4ecef74cd78ec1736803a0c0b4bc
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107497374"
---
# <a name="change-the-os-disk-used-by-an-azure-vm-using-the-azure-cli"></a>Zmienianie dysku systemu operacyjnego używanego przez maszynę wirtualną platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure


Jeśli masz istniejącą maszynę wirtualną, ale chcesz zamienić dysk na dysk kopii zapasowej lub inny dysk systemu operacyjnego, możesz użyć interfejsu wiersza polecenia platformy Azure, aby zamienić dyski systemu operacyjnego. Nie musisz usuwać ani ponownie tworzyć maszyny wirtualnej. Możesz nawet użyć dysku zarządzanego w innej grupie zasobów, o ile nie jest on jeszcze w użyciu.

Maszyna wirtualna musi zostać zatrzymana\cofnie przydział, a następnie identyfikator zasobu dysku zarządzanego można zastąpić identyfikatorem zasobu innego dysku zarządzanego. 

Upewnij się, że rozmiar maszyny wirtualnej i typ magazynu są zgodne z dyskiem, który chcesz dołączyć. Jeśli na przykład dysk, którego chcesz użyć, znajduje się w Premium Storage, maszyna wirtualna musi być w stanie Premium Storage (na przykład rozmiar serii DS).

Ten artykuł wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0.25 lub większej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli). 


Użyj [narzędzia az disk list,](/cli/azure/disk) aby uzyskać listę dysków w grupie zasobów.

```azurecli-interactive
az disk list \
   -g myResourceGroupDisk \
   --query '[*].{diskId:id}' \
   --output table
```


Użyj [az vm stop,](/cli/azure/vm) aby zatrzymać\cofniesz alokację maszyny wirtualnej przed zamianą dysków.

```azurecli-interactive
az vm stop \
   -n myVM \
   -g myResourceGroup
```


Użyj [az vm update](/cli/azure/vm#az-vm-update) z pełnym identyfikatorem zasobu nowego dysku dla `--osdisk` parametru 

```azurecli-interactive 
az vm update \
   -g myResourceGroup \
   -n myVM \
   --os-disk /subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Compute/disks/myDisk 
   ```
   
Uruchom ponownie maszynę wirtualną za [pomocą narzędzia az vm start](/cli/azure/vm).

```azurecli-interactive
az vm start \
   -n myVM \
   -g myResourceGroup
```

   
**Następne kroki**

Aby utworzyć kopię dysku, zobacz [Tworzenie migawki dysku](snapshot-copy-managed-disk.md).
