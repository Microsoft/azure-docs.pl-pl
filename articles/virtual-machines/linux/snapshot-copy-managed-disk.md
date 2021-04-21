---
title: Tworzenie migawki dysku VHD przy użyciu interfejsu wiersza polecenia platformy Azure
description: Dowiedz się, jak utworzyć kopię wirtualnego dysku twardego na platformie Azure jako kopię zapasową lub w celu rozwiązywania problemów.
author: roygara
manager: twooley
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 07/11/2018
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: ab19bb1c6cc43334a3d0d427b6aff6ced2d6cc69
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107789667"
---
# <a name="create-a-snapshot-using-the-portal-or-azure-cli"></a>Tworzenie migawki przy użyciu portalu lub interfejsu wiersza polecenia platformy Azure

Zrób migawkę dysku systemu operacyjnego lub danych na potrzeby tworzenia kopii zapasowej lub rozwiązywania problemów z maszyną wirtualną. Migawka jest pełną kopią dysku VHD tylko do odczytu. 

## <a name="use-azure-cli"></a>Interfejs wiersza polecenia platformy Azure 

Poniższy przykład wymaga użycia interfejsu wiersza [Cloud Shell](https://shell.azure.com/bash) lub zainstalowania interfejsu wiersza polecenia platformy Azure.

Poniższe kroki pokazują, jak utworzyć migawkę przy użyciu polecenia **az snapshot create** z **parametrem --source-disk.** W poniższym przykładzie przyjęto założenie, że w grupie zasobów *myResourceGroup* istnieje maszyna wirtualna o nazwie *myVM.*

Pobierz identyfikator dysku za pomocą narzędzia [az vm show](/cli/azure/vm#az_vm_show).

```azurecli-interactive
osDiskId=$(az vm show \
   -g myResourceGroup \
   -n myVM \
   --query "storageProfile.osDisk.managedDisk.id" \
   -o tsv)
```

Utwórz migawkę o nazwie *osDisk-backup* przy użyciu [narzędzia az snapshot create.](/cli/azure/snapshot#az_snapshot_create)

```azurecli-interactive
az snapshot create \
    -g myResourceGroup \
    --source "$osDiskId" \
    --name osDisk-backup
```

> [!NOTE]
> Jeśli chcesz przechowywać migawkę w magazynie odpornym na strefy, musisz utworzyć [](../../availability-zones/az-overview.md) ją w regionie, który obsługuje strefy dostępności i zawiera parametr **--sku Standard_ZRS.**

Listę migawek można wyświetlić za pomocą narzędzia [az snapshot list](/cli/azure/snapshot#az_snapshot_list).

```azurecli-interactive
az snapshot list \
   -g myResourceGroup \
   - table
```

## <a name="use-azure-portal"></a>Korzystanie z witryny Azure Portal 

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. W lewym górnym rogu kliknij pozycję **Utwórz zasób i** wyszukaj **migawkę**. Wybierz **pozycję Migawka** w wynikach wyszukiwania.
3. W bloku **Migawka** kliknij pozycję **Utwórz**.
4. Wprowadź **nazwę** migawki.
5. Wybierz istniejącą grupę zasobów lub wpisz nazwę nowej. 
7. W **przypadku opcji Dysk źródłowy** wybierz dysk zarządzany do utworzenia migawki.
8. Wybierz typ **konta do** przechowywania migawki. Użyj **HDD w warstwie Standardowa,** chyba że jest on przechowywany na dysku SSD o wysokiej wydajności.
9. Kliknij pozycję **Utwórz**.


## <a name="next-steps"></a>Następne kroki

 Utwórz maszynę wirtualną na pomocą migawki, tworząc dysk zarządzany na jej pomocą, a następnie dołączając nowy dysk zarządzany jako dysk systemu operacyjnego. Aby uzyskać więcej informacji, zobacz [Tworzenie maszyny wirtualnej na pomocą skryptu migawki.](/previous-versions/azure/virtual-machines/scripts/virtual-machines-linux-cli-sample-create-vm-from-snapshot?toc=%2fcli%2fmodule%2ftoc.json)