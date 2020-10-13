---
title: Przekształć magazyn Managed disks między warstwami SSD Standard i Premium
description: Jak skonwertować Magazyn Azure Managed disks ze standardu do Premium lub Premium na Standard przy użyciu interfejsu wiersza polecenia platformy Azure.
author: roygara
ms.service: virtual-machines-linux
ms.topic: how-to
ms.date: 07/12/2018
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 10674d15a7515b01b0df6cf37bce89f153cb9b0b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88870689"
---
# <a name="convert-azure-managed-disks-storage-from-standard-to-premium-or-premium-to-standard"></a>Konwertowanie magazynu Azure Managed disks z wersji Standard do Premium lub Premium na Standard

Istnieją cztery typy dysków zarządzanych przez platformę Azure: Azure Ultra disks, dysk SSD Premium, standardowy dysk SSD i standardowy dysk twardy. W zależności od potrzeb związanych z wydajnością można przełączać się między trzema dyskami z rozpowszechnymi typami dysków (dyski SSD Premium, standardowy dysk SSD i standardowy dysk twardy). Nie można już przełączyć z programu ani na dysk typu Ultra, należy wdrożyć nowy.

Ta funkcja nie jest obsługiwana w przypadku dysków niezarządzanych. Można jednak łatwo [skonwertować dysk niezarządzany na dysk zarządzany](convert-unmanaged-to-managed-disks.md) , aby można było przełączać się między typami dysków.

W tym artykule pokazano, jak konwertować dyski zarządzane z warstwy Standardowa do Premium lub Premium na Standard przy użyciu interfejsu wiersza polecenia platformy Azure. Aby zainstalować lub uaktualnić narzędzie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

## <a name="before-you-begin"></a>Zanim rozpoczniesz

* Konwersja dysku wymaga ponownego uruchomienia maszyny wirtualnej, dlatego należy zaplanować migrację magazynu dyskowego w ramach istniejącego wcześniej okna obsługi.
* W przypadku dysków niezarządzanych należy najpierw [przeprowadzić konwersję na dyski zarządzane](convert-unmanaged-to-managed-disks.md) , aby można było przełączać się między opcjami magazynu.


## <a name="switch-all-managed-disks-of-a-vm-between-premium-and-standard"></a>Przełączanie wszystkich dysków zarządzanych maszyny wirtualnej między warstwami Premium i Standard

Ten przykład pokazuje, jak przekonwertować wszystkie dyski maszyny wirtualnej ze standardu do magazynu w warstwie Premium lub Premium do magazynu w warstwie Standardowa. Aby można było korzystać z dysków zarządzanych w warstwie Premium, maszyna wirtualna musi używać [rozmiaru maszyny wirtualnej](../sizes.md) obsługującego usługę Premium Storage. Ten przykład umożliwia przełączenie na rozmiar obsługujący usługę Premium Storage.

 ```azurecli

#resource group that contains the virtual machine
rgName='yourResourceGroup'

#Name of the virtual machine
vmName='yourVM'

#Premium capable size 
#Required only if converting from Standard to Premium
size='Standard_DS2_v2'

#Choose between Standard_LRS and Premium_LRS based on your scenario
sku='Premium_LRS'

#Deallocate the VM before changing the size of the VM
az vm deallocate --name $vmName --resource-group $rgName

#Change the VM size to a size that supports Premium storage 
#Skip this step if converting storage from Premium to Standard
az vm resize --resource-group $rgName --name $vmName --size $size

#Update the SKU of all the data disks 
az vm show -n $vmName -g $rgName --query storageProfile.dataDisks[*].managedDisk -o tsv \
 | awk -v sku=$sku '{system("az disk update --sku "sku" --ids "$1)}'

#Update the SKU of the OS disk
az vm show -n $vmName -g $rgName --query storageProfile.osDisk.managedDisk -o tsv \
| awk -v sku=$sku '{system("az disk update --sku "sku" --ids "$1)}'

az vm start --name $vmName --resource-group $rgName

```
## <a name="switch-individual-managed-disks-between-standard-and-premium"></a>Przełączanie poszczególnych dysków zarządzanych między warstwami standardowa i Premium

W przypadku obciążeń związanych z tworzeniem i testowaniem warto mieć kombinację dysków w warstwach Standardowa i Premium, aby zmniejszyć koszty. Można uaktualnić tylko te dyski, które potrzebują lepszej wydajności. Ten przykład pokazuje, jak przekonwertować pojedynczy dysk maszyny wirtualnej ze standardu do magazynu w warstwie Premium lub Premium do magazynu w warstwie Standardowa. Aby można było korzystać z dysków zarządzanych w warstwie Premium, maszyna wirtualna musi używać [rozmiaru maszyny wirtualnej](../sizes.md) obsługującego usługę Premium Storage. Ten przykład umożliwia przełączenie na rozmiar obsługujący usługę Premium Storage.

 ```azurecli

#resource group that contains the managed disk
rgName='yourResourceGroup'

#Name of your managed disk
diskName='yourManagedDiskName'

#Premium capable size 
#Required only if converting from Standard to Premium
size='Standard_DS2_v2'

#Choose between Standard_LRS and Premium_LRS based on your scenario
sku='Premium_LRS'

#Get the parent VM Id 
vmId=$(az disk show --name $diskName --resource-group $rgName --query managedBy --output tsv)

#Deallocate the VM before changing the size of the VM
az vm deallocate --ids $vmId 

#Change the VM size to a size that supports Premium storage 
#Skip this step if converting storage from Premium to Standard
az vm resize --ids $vmId --size $size

# Update the SKU
az disk update --sku $sku --name $diskName --resource-group $rgName 

az vm start --ids $vmId 
```

## <a name="switch-managed-disks-between-standard-hdd-and-standard-ssd"></a>Przełącz dyski zarządzane między HDD w warstwie Standardowa i SSD w warstwie Standardowa

Ten przykład pokazuje, jak przekonwertować pojedynczy dysk maszyny wirtualnej z HDD w warstwie Standardowa na SSD w warstwie Standardowa lub z SSD w warstwie Standardowa na HDD w warstwie Standardowa.

 ```azurecli

#resource group that contains the managed disk
rgName='yourResourceGroup'

#Name of your managed disk
diskName='yourManagedDiskName'

#Choose between Standard_LRS and StandardSSD_LRS based on your scenario
sku='StandardSSD_LRS'

#Get the parent VM ID 
vmId=$(az disk show --name $diskName --resource-group $rgName --query managedBy --output tsv)

#Deallocate the VM before changing the disk type
az vm deallocate --ids $vmId 

# Update the SKU
az disk update --sku $sku --name $diskName --resource-group $rgName 

az vm start --ids $vmId 
```

## <a name="switch-managed-disks-between-standard-and-premium-in-azure-portal"></a>Przełącz dyski zarządzane w warstwach Standardowa i Premium w Azure Portal

Wykonaj następujące kroki:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Wybierz MASZYNę wirtualną z listy **maszyn wirtualnych**.
3. Jeśli maszyna wirtualna nie jest zatrzymana, wybierz pozycję **Zatrzymaj** w górnej części okienka **Przegląd** maszyny wirtualnej i poczekaj na zatrzymanie maszyny wirtualnej.
4. W okienku dla maszyny wirtualnej wybierz pozycję **dyski** z menu.
5. Wybierz dysk, który chcesz skonwertować.
6. Z menu wybierz pozycję **Konfiguracja** .
7. Zmień **Typ konta** z **HDD w warstwie Standardowa** na **SSD w warstwie Premium** lub z **SSD w warstwie Premium** na **HDD w warstwie Standardowa**.
8. Wybierz pozycję **Zapisz**, a następnie zamknij okienko dysk.

Aktualizacja typu dysku jest chwilowo. Po konwersji można uruchomić ponownie maszynę wirtualną.

## <a name="next-steps"></a>Następne kroki

Utwórz kopię maszyny wirtualnej tylko do odczytu z użyciem [migawek](snapshot-copy-managed-disk.md).