---
title: Przykład interfejsu wiersza polecenia platformy Azure służący do przeniesienia maszyny wirtualnej platformy Azure w portalu Marketplace do innej subskrypcji
description: Przykład interfejsu wiersza polecenia platformy Azure służący do przeniesienia maszyny wirtualnej portalu Azure Marketplace do innej subskrypcji.
author: cynthn
ms.author: cynthn
manager: ''
ms.date: 01/29/2021
ms.topic: sample
ms.service: virtual-machines
ms.devlang: azurecli
ms.custom: devx-track-azurecli
ms.openlocfilehash: e70284a3a89c30d0f1d1363a15652449a6a1d4f5
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100558091"
---
# <a name="move-a-marketplace-azure-virtual-machine-to-another-subscription"></a>Przenoszenie maszyny wirtualnej platformy Azure w portalu Marketplace do innej subskrypcji

Aby przenieść maszynę wirtualną z portalu Marketplace do innej subskrypcji, musisz przenieść dysk systemu operacyjnego do tej subskrypcji, a następnie ponownie utworzyć maszynę wirtualną.

Ta procedura nie jest potrzebna do przenoszenia dysku danych do nowej subskrypcji. Zamiast tego Utwórz nową maszynę wirtualną w nowej subskrypcji z portalu Marketplace, a następnie przenieś i Dołącz dysk danych.

Ten skrypt demonstruje trzy operacje:

- Utwórz migawkę dysku systemu operacyjnego.
- Przenieś migawkę do innej subskrypcji.
- Utwórz maszynę wirtualną na podstawie tej migawki.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

## <a name="sample-script"></a>Przykładowy skrypt

Aby przenieść maszynę wirtualną z portalu Marketplace do innej subskrypcji, należy utworzyć nową maszynę wirtualną dla tej samej oferty Marketplace z przenoszonego dysku systemu operacyjnego.

> [!NOTE]
> Jeśli plan maszyny wirtualnej nie jest już dostępny w portalu Marketplace, nie można użyć tej procedury.

```azurecli
#!/bin/bash
# Set variable values before proceeding. 

# Variables
sourceResourceGroup= Resource group for the current virtual machine
sourceSubscription= Subscription for the current virtual machine
vmName= Name of the current virtual machine

destinationResourceGroup= Resource group for the new virtual machine, create if necessary
destinationSubscription= Subscription for the new virtual machine

# Set your current subscription for the source virtual machine
az account set --subscription $sourceSubscription

# Load variables about your virtual machine
# osType = windows or linux
osType=$(az vm get-instance-view --resource-group $sourceResourceGroup \
    --name $vmName --subscription $sourceSubscription \
    --query 'storageProfile.osDisk.osType' --output tsv)

# offer = Your offer in Marketplace
offer=$(az vm get-instance-view --resource-group $sourceResourceGroup \
    --name $vmName --query 'storageProfile.imageReference.offer' --output tsv)

# plan = Your plan in Marketplace
plan=$(az vm get-instance-view --resource-group $sourceResourceGroup \
    --name $vmName --query 'plan' --output tsv)

# publisher = Your publisher in Marketplace
publisher=$(az vm get-instance-view --resource-group $sourceResourceGroup \
    --name $vmName --query 'storageProfile.imageReference.publisher' --output tsv)

# Get information to create new virtual machine
planName=$(az vm get-instance-view --resource-group $sourceResourceGroup \
    --subscription $sourceSubscription --query 'plan.name' --name $vmName)
planProduct=$(az vm get-instance-view --resource-group $sourceResourceGroup \
    --subscription $sourceSubscription --query 'plan.product' --name $vmName)
planPublisher=$(az vm get-instance-view --resource-group $sourceResourceGroup \
    --subscription $sourceSubscription --query 'plan.publisher' --name $vmName)

# Get the name of the OS disk
osDiskName=$(az vm show --resource-group $sourceResourceGroup --name $vmName \
    --query 'storageProfile.osDisk.name' --output tsv)

# Verify the terms for your market virtual machine
az vm image terms show --offer $offer --plan '$plan' --publisher $publisher \
    --subscription $sourceSubscription

# Deallocate the virtual machine
az vm deallocate --resource-group $sourceResourceGroup --name $vmName

# Create a snapshot of the OS disk
az snapshot create --resource-group $sourceResourceGroup --name MigrationSnapshot \
    --source "/subscriptions/$sourceSubscription/resourceGroups/$sourceResourceGroup/providers/Microsoft.Compute/disks/$osDiskName"

# Move the snapshot to your destination resource group
az resource move --destination-group $destinationResourceGroup \
    --destination-subscription-id $destinationSubscription \
    --ids "/subscriptions/$sourceSubscription/resourceGroups/$sourceResourceGroup/providers/Microsoft.Compute/snapshots/MigrationSnapshot"

# Set your subscription to the destination value
az account set --subscription $destinationSubscription

# Accept the terms from the Marketplace
az vm image terms accept --offer $offer --plan '$plan' --publisher $publisher \
    --subscription $destinationSubscription

# Create disk from the snapshot 
az disk create --resource-group $destinationResourceGroup --name DestinationDisk \
    --source "/subscriptions/$destinationSubscription/resourceGroups/$destinationResourceGroup/providers/Microsoft.Compute/snapshots/MigrationSnapshot" \
    --os-type $osType

# Create virtual machine from disk
az vm create --resource-group $destinationResourceGroup --name $vmName \
    --plan-name $planName --plan-product $planProduct  --plan-publisher $planPublisher \
    --attach-os-disk "/subscriptions/$destinationSubscription/resourceGroups/$destinationResourceGroup/providers/Microsoft.Compute/disks/DestinationDisk" \
    --os-type $osType
```

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Po uruchomieniu przykładu Użyj następujących poleceń, aby usunąć grupy zasobów i wszystkie skojarzone zasoby:

```azurecli
az group delete --name $sourceResourceGroup --subscription $sourceSubscription
az group delete --name $destinationResourceGroup --subscription $destinationSubscription
```

## <a name="azure-cli-references-used-in-this-article"></a>Odwołania do interfejsu wiersza polecenia platformy Azure używane w tym artykule

- [AZ Account Set](/cli/azure/account#az_account_set)
- [az disk create](/cli/azure/disk#az_disk_create)
- [az group delete](/cli/azure/group#az_group_delete)
- [AZ Resource Move](/cli/azure/resource#az_resource_move)
- [az snapshot create](/cli/azure/snapshot#az_snapshot_create)
- [az vm create](/cli/azure/vm#az_vm_create)
- [AZ VM deallocate](/cli/azure/vm#az_vm_deallocate)
- [az vm delete](/cli/azure/vm#az_vm_delete)
- [AZ VM Get-instance-View](/cli/azure/vm#az_vm_get_instance_view)
- [AZ VM Image Term Accept](/cli/azure/vm/image/terms#az_vm_image_terms_accept)
- [AZ VM Image terms show](/cli/azure/vm/image/terms#az_vm_image_terms_show)
- [az vm show](/cli/azure/vm#az_vm_show)

## <a name="next-steps"></a>Następne kroki

- [Przenoszenie maszyn wirtualnych do innego regionu platformy Azure](../site-recovery/azure-to-azure-tutorial-migrate.md)
- [Przenoszenie maszyny wirtualnej do innej subskrypcji lub grupy zasobów](./linux/move-vm.md)
