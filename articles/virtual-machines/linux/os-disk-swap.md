---
title: Wymiana między dyskami systemu operacyjnego przy użyciu interfejsu wiersza polecenia "
description: Zmień dysk systemu operacyjnego używany przez maszynę wirtualną platformy Azure przy użyciu interfejsu wiersza polecenia.
author: cynthn
ms.service: virtual-machines
ms.subservice: disks
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 04/24/2018
ms.author: cynthn
ms.openlocfilehash: adcbc5b0ba0056c79576e8d4c8ded2150b206513
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87371876"
---
# <a name="change-the-os-disk-used-by-an-azure-vm-using-the-cli"></a>Zmienianie dysku systemu operacyjnego używanego przez maszynę wirtualną platformy Azure przy użyciu interfejsu wiersza polecenia


Jeśli masz istniejącą maszynę wirtualną, ale chcesz zamienić dysk na dysk kopii zapasowej lub inny dysk systemu operacyjnego, możesz użyć interfejsu wiersza polecenia platformy Azure, aby zamienić dyski systemu operacyjnego. Nie trzeba usuwać ani tworzyć ponownie maszyny wirtualnej. Można nawet użyć dysku zarządzanego w innej grupie zasobów, o ile nie jest on już używany.

Maszyna wirtualna musi być stopped\deallocated, a następnie identyfikator zasobu dysku zarządzanego można zastąpić IDENTYFIKATORem zasobu innego dysku zarządzanego. 

Upewnij się, że rozmiar maszyny wirtualnej i typ magazynu są zgodne z dyskiem, który chcesz dołączyć. Na przykład jeśli dysk, który ma być używany, znajduje się w Premium Storage, maszyna wirtualna musi mieć możliwość Premium Storage (na przykład rozmiaru serii DS).

Ten artykuł wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0.25 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli). 


Użyj [AZ Disk list](/cli/azure/disk) , aby uzyskać listę dysków w grupie zasobów.

```azurecli-interactive
az disk list \
   -g myResourceGroupDisk \
   --query '[*].{diskId:id}' \
   --output table
```


Przed zainstalowaniem dysków Użyj [AZ VM Stop](/cli/azure/vm) to stop\deallocate maszyny wirtualnej.

```azurecli-interactive
az vm stop \
   -n myVM \
   -g myResourceGroup
```


Użyj [AZ VM Update](/cli/azure/vm#az-vm-update) z pełnym identyfikatorem zasobu nowego dysku dla `--osdisk` parametru 

```azurecli-interactive 
az vm update \
   -g myResourceGroup \
   -n myVM \
   --os-disk /subscriptions/<subscription ID>/resourceGroups/swap/providers/Microsoft.Compute/disks/myDisk 
   ```
   
Uruchom ponownie maszynę wirtualną za pomocą polecenia [AZ VM Start](/cli/azure/vm).

```azurecli-interactive
az vm start \
   -n myVM \
   -g myResourceGroup
```

   
**Następne kroki**

Aby utworzyć kopię dysku, zobacz [migawka dysku](snapshot-copy-managed-disk.md).
