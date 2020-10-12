---
title: Tworzenie migawki wirtualnego dysku twardego przy użyciu interfejsu wiersza polecenia platformy Azure
description: Dowiedz się, jak utworzyć kopię dysku VHD na platformie Azure jako kopię zapasową lub rozwiązać problemy.
author: roygara
manager: twooley
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 07/11/2018
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 6374108247b9bfb950c42495b13b501ded8a02d2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89317999"
---
# <a name="create-a-snapshot-using-the-portal-or-azure-cli"></a>Tworzenie migawki przy użyciu portalu lub interfejsu wiersza polecenia platformy Azure

Utwórz migawkę dysku systemu operacyjnego lub danych do utworzenia kopii zapasowej lub Rozwiąż problemy z maszyną wirtualną. Migawka to pełna kopia tylko do odczytu dysku VHD. 

## <a name="use-azure-cli"></a>Interfejs wiersza polecenia platformy Azure 

Poniższy przykład wymaga użycia [Cloud Shell](https://shell.azure.com/bash) lub zainstalowania interfejsu wiersza polecenia platformy Azure.

Poniższe kroki pokazują, jak wykonać migawkę przy użyciu polecenia **AZ Snapshot Create** z parametrem **--Source-Disk** . W poniższym przykładzie przyjęto założenie, że istnieje maszyna wirtualna o nazwie *myVM* w grupie zasobów *WebResource* .

Pobierz identyfikator dysku za pomocą polecenia [AZ VM show](/cli/azure/vm#az-vm-show).

```azurecli-interactive
osDiskId=$(az vm show \
   -g myResourceGroup \
   -n myVM \
   --query "storageProfile.osDisk.managedDisk.id" \
   -o tsv)
```

Zrób migawkę o nazwie *osDisk-Backup* przy użyciu polecenia [AZ Snapshot Create](/cli/azure/snapshot#az-snapshot-create).

```azurecli-interactive
az snapshot create \
    -g myResourceGroup \
    --source "$osDiskId" \
    --name osDisk-backup
```

> [!NOTE]
> Jeśli chcesz przechowywać migawkę w magazynie odpornym na strefy, musisz ją utworzyć w regionie, który obsługuje [strefy dostępności](../../availability-zones/az-overview.md) , i uwzględnić parametr **--SKU Standard_ZRS** .

Listę migawek można wyświetlić za pomocą polecenia [AZ Snapshot list](/cli/azure/snapshot#az-snapshot-list).

```azurecli-interactive
az snapshot list \
   -g myResourceGroup \
   - table
```

## <a name="use-azure-portal"></a>Korzystanie z witryny Azure Portal 

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Zaczynając od lewego górnego rogu, kliknij pozycję **Utwórz zasób** i Wyszukaj **migawkę**. Wybierz pozycję **migawka** z wyników wyszukiwania.
3. W bloku **migawka** kliknij pozycję **Utwórz**.
4. Wprowadź **nazwę** migawki.
5. Wybierz istniejącą grupę zasobów lub wpisz nazwę nowej grupy. 
7. W polu **dysk źródłowy**wybierz dysk zarządzany do utworzenia migawki.
8. Wybierz **Typ konta** , który ma być używany do przechowywania migawki. Użyj **HDD w warstwie Standardowa** , chyba że będzie to konieczne w przypadku dysku SSD o wysokiej wydajności.
9. Kliknij przycisk **Utwórz**.


## <a name="next-steps"></a>Następne kroki

 Utwórz maszynę wirtualną na podstawie migawki przez utworzenie dysku zarządzanego na podstawie migawki, a następnie dołączenie nowego dysku zarządzanego jako dysku systemu operacyjnego. Aby uzyskać więcej informacji, zobacz [Tworzenie maszyny wirtualnej na podstawie skryptu migawki](./../scripts/virtual-machines-linux-cli-sample-create-vm-from-snapshot.md?toc=%2fcli%2fmodule%2ftoc.json) .

