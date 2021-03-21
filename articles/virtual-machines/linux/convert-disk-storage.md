---
title: Konwertowanie magazynu dysków zarządzanych między różnymi typami dysków przy użyciu interfejsu wiersza polecenia platformy Azure
description: Jak przekonwertować dyski zarządzane przez platformę Azure między różnymi typami dysków przy użyciu interfejsu wiersza polecenia platformy Azure.
author: roygara
ms.service: virtual-machines
ms.collection: linux
ms.topic: how-to
ms.date: 02/13/2021
ms.author: albecker
ms.subservice: disks
ms.openlocfilehash: 35ba681ae35e60d7cd275c9e649b2463d2dc30a0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102558461"
---
# <a name="convert-azure-managed-disks-storage-from-standard-to-premium-or-premium-to-standard"></a>Konwertowanie magazynu Azure Managed disks z wersji Standard do Premium lub Premium na Standard

Istnieją cztery typy dysków zarządzanych przez platformę Azure: Azure Ultra disks, dysk SSD Premium, standardowy dysk SSD i standardowy dysk twardy. Możesz przełączać się między dyskiem SSD Premium, standardowym dyskiem SSD i standardowym dyskiem twardym w zależności od potrzeb dotyczących wydajności. Nie można już przełączyć z programu ani na dysk typu Ultra, należy wdrożyć nowy.

Ta funkcja nie jest obsługiwana w przypadku dysków niezarządzanych. Można jednak łatwo [skonwertować dysk niezarządzany na dysk zarządzany](convert-unmanaged-to-managed-disks.md) , aby można było przełączać się między typami dysków.

W tym artykule pokazano, jak konwertować dyski zarządzane z jednego typu dysku na inny przy użyciu interfejsu wiersza polecenia platformy Azure. Aby zainstalować lub uaktualnić narzędzie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

## <a name="before-you-begin"></a>Zanim rozpoczniesz

* Konwersja dysku wymaga ponownego uruchomienia maszyny wirtualnej, dlatego należy zaplanować migrację magazynu dyskowego w ramach istniejącego wcześniej okna obsługi.
* W przypadku dysków niezarządzanych należy najpierw [przeprowadzić konwersję na dyski zarządzane](convert-unmanaged-to-managed-disks.md) , aby można było przełączać się między opcjami magazynu.


## <a name="switch-all-managed-disks-of-a-vm-between-from-one-account-to-another"></a>Przełączanie wszystkich dysków zarządzanych z maszyny wirtualnej między różnymi kontami

Ten przykład pokazuje, jak przekonwertować wszystkie dyski maszyny wirtualnej do magazynu w warstwie Premium. Jednak zmieniając zmienną SKU w tym przykładzie, można skonwertować typ dysków maszyny wirtualnej na standardowy dysk SSD lub standardowy dysk twardy. Należy pamiętać, że aby korzystać z dysków zarządzanych w warstwie Premium, maszyna wirtualna musi używać [rozmiaru maszyny wirtualnej](../sizes.md) obsługującego usługę Premium Storage. Ten przykład umożliwia przełączenie na rozmiar obsługujący usługę Premium Storage.

 ```azurecli

#resource group that contains the virtual machine
rgName='yourResourceGroup'

#Name of the virtual machine
vmName='yourVM'

#Premium capable size 
#Required only if converting from Standard to Premium
size='Standard_DS2_v2'

#Choose between Standard_LRS, StandardSSD_LRS and Premium_LRS based on your scenario
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
## <a name="switch-individual-managed-disks-from-one-disk-type-to-another"></a>Przełączanie pojedynczych dysków zarządzanych z jednego typu dysku na inny

W przypadku obciążeń związanych z tworzeniem i testowaniem warto mieć kombinację dysków w warstwach Standardowa i Premium, aby zmniejszyć koszty. Można uaktualnić tylko te dyski, które potrzebują lepszej wydajności. Ten przykład pokazuje, jak przekonwertować pojedynczy dysk maszyny wirtualnej ze standardu do Premium Storage. Jednak zmieniając zmienną SKU w tym przykładzie, można skonwertować typ dysków maszyny wirtualnej na standardowy dysk SSD lub standardowy dysk twardy. Aby można było korzystać z dysków zarządzanych w warstwie Premium, maszyna wirtualna musi używać [rozmiaru maszyny wirtualnej](../sizes.md) obsługującego usługę Premium Storage. Ten przykład umożliwia przełączenie na rozmiar obsługujący usługę Premium Storage.

 ```azurecli

#resource group that contains the managed disk
rgName='yourResourceGroup'

#Name of your managed disk
diskName='yourManagedDiskName'

#Premium capable size 
#Required only if converting from Standard to Premium
size='Standard_DS2_v2'

#Choose between Standard_LRS, StandardSSD_LRS and Premium_LRS based on your scenario
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

## <a name="switch-managed-disks-from-one-disk-type-to-another"></a>Przełącz dyski zarządzane z jednego typu dysku na inny

Wykonaj następujące kroki:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Wybierz MASZYNę wirtualną z listy **maszyn wirtualnych**.
3. Jeśli maszyna wirtualna nie jest zatrzymana, wybierz pozycję **Zatrzymaj** w górnej części okienka **Przegląd** maszyny wirtualnej i poczekaj na zatrzymanie maszyny wirtualnej.
4. W okienku dla maszyny wirtualnej wybierz pozycję **dyski** z menu.
5. Wybierz dysk, który chcesz skonwertować.
6. Z menu wybierz pozycję **Konfiguracja** .
7. Zmień **Typ konta** z oryginalnego typu dysku na żądany typ dysku.
8. Wybierz pozycję **Zapisz**, a następnie zamknij okienko dysk.

Aktualizacja typu dysku jest chwilowo. Po konwersji można uruchomić ponownie maszynę wirtualną.

## <a name="next-steps"></a>Następne kroki

Utwórz kopię maszyny wirtualnej tylko do odczytu z użyciem [migawek](snapshot-copy-managed-disk.md).