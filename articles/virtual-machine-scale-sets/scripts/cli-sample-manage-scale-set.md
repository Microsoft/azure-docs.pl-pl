---
title: Przykład interfejsu wiersza polecenia platformy Azure do zarządzania zestawem skalowania maszyn wirtualnych
description: Ten przykład pokazuje, jak dodać dyski do zestawu skalowania maszyn wirtualnych. Możesz uaktualnić dyski i dodać maszyny wirtualne do uwierzytelniania w usłudze Azure AD.
author: mimckitt
ms.author: mimckitt
ms.date: 02/04/2021
ms.topic: sample
ms.service: virtual-machine-scale-sets
ms.devlang: azurecli
ms.custom: devx-track-azurecli
ms.openlocfilehash: 1abdf7ae15753d78ac8728f57e9b0cd5dcd9165e
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101672608"
---
# <a name="create-and-manage-virtual-machine-scale-set"></a>Tworzenie zestawu skalowania maszyn wirtualnych i zarządzanie nim

Te przykładowe polecenia służą do prototypowania zestawu skalowania maszyn wirtualnych przy użyciu interfejsu wiersza polecenia platformy Azure.

Te przykładowe polecenia przedstawiają następujące operacje:

* Tworzy zestaw skalowania maszyn wirtualnych.
* Dodaj i Uaktualnij nowe lub istniejące dyski do zestawu skalowania lub do wystąpienia zestawu.
* Dodaj zestaw skalowania do Azure Active Directory (Azure AD) uwierzytelnianie.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

## <a name="sample-commands"></a>Przykładowe polecenia

```azurecli
# Create a resource group
az group create --name MyResourceGroup --location eastus

# Create virtual machine scale set
az vmss create --resource-group MyResourceGroup --name myScaleSet --instance-count 2 \
    --image UbuntuLTS --upgrade-policy-mode automatic --admin-username azureuser \
    --generate-ssh-keys

# Attach a new managed disk to your scale set
az vmss disk attach --resource-group MyResourceGroup --vmss-name myScaleSet --size-gb 50
```

Po dodaniu nowego dysku danych sformatuj i zainstaluj dysk. W przypadku maszyn wirtualnych z systemem Windows, zobacz [dołączanie zarządzanego dysku danych do maszyny wirtualnej z systemem Windows przy użyciu Azure Portal](../../virtual-machines/windows/attach-managed-disk-portal.md). W przypadku maszyn wirtualnych z systemem Linux zobacz [Dodawanie dysku do maszyny wirtualnej z systemem Linux](../../virtual-machines/linux/add-disk.md).

```azurecli
# Attach an existing managed disk to a virtual machine instance in your scale set
az vmss disk attach --resource-group MyResourceGroup --disk myDataDisk \
    --vmss-name myScaleSet --instance-id 0

# See the instances in your virtual machine scale set
az vmss list-instances --resource-group MyResourceGroup --name myScaleSet --output table

# See the disks for your virtual machine
az disk list --resource-group MyResourceGroup \
    --query "[*].{Name:name,Gb:diskSizeGb,Tier:accountType}" --output table

# Deallocate the virtual machine
az vmss deallocate --resource-group MyResourceGroup --name myScaleSet --instance-ids 0 

# Resize the disk
az disk update --resource-group MyResourceGroup --name myDataDisk --size-gb 200

# Restart the disk
az vmss restart --resource-group MyResourceGroup --name myScaleSet --instance-ids 0
```

Aby użyć rozszerzonego dysku, rozwiń partycję podstawową. Aby uzyskać więcej informacji, zobacz [rozszerzanie partycji dyskowej i systemu plików](../../virtual-machines/linux/expand-disks.md#expand-a-disk-partition-and-filesystem).

Ten przykład zmienia rozmiar dysku danych. Ta sama procedura służy do aktualizowania dysku systemu operacyjnego. Aby uzyskać więcej informacji na temat maszyny wirtualnej z systemem Windows, zobacz [jak rozszerzyć dysk systemu operacyjnego maszyny wirtualnej](../../virtual-machines/windows/expand-os-disk.md). Aby uzyskać więcej informacji na temat maszyn wirtualnych z systemem Linux, zobacz [rozszerzanie wirtualnych dysków twardych na maszynę wirtualną z systemem Linux przy użyciu interfejsu wiersza polecenia platformy Azure](../../virtual-machines/linux/expand-disks.md).

```azurecli
# Enable managed service identity on your scale set. This is required to authenticate and interact with other Azure services using bearer tokens.
az vmss identity assign --resource-group MyResourceGroup --name myScaleSet --role Owner \
    --scope /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MyResourceGroup

# Connect to Azure AD authentication
az vmss extension set --resource-group MyResourceGroup --name AADLoginForWindows \
    --publisher Microsoft.Azure.ActiveDirectory --vmss-name myScaleSet

# Upgrade one instance of a scale set virtual machine
az vmss update-instances --resource-group MyResourceGroup --name myScaleSet --instance-ids 0 

# Remove a managed disk from the scale set
az vmss disk detach --resource-group MyResourceGroup --vmss-name myScaleSet --lun 0

# Remove a managed disk from an instance
az vmss disk detach --resource-group MyResourceGroup --vmss-name myScaleSet --instance-id 1 --lun 0

# Delete the pre-existing disk
az disk delete --resource-group MyResourceGroup --disk myDataDisk
```

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Po użyciu tych poleceń Uruchom następujące polecenie, aby usunąć grupę zasobów i wszystkie skojarzone z nią zasoby.

```azurecli
az group delete --name MyResourceGroup
```

## <a name="azure-cli-references-used-in-this-article"></a>Odwołania do interfejsu wiersza polecenia platformy Azure używane w tym artykule

* [AZ Disk Delete](/cli/azure/disk#az_disk_delete)
* [AZ Disk list](/cli/azure/disk#az_disk_list)
* [AZ Disk Update](/cli/azure/disk#az_disk_update)
* [az group create](/cli/azure/group#az_group_create)
* [az vmss create](/cli/azure/vmss#az_vmss_create)
* [AZ Virtual Machine Scale Set unallocate](/cli/azure/vmss#az_vmss_deallocate)
* [az vmss disk attach](/cli/azure/vmss/disk#az_vmss_disk_attach)
* [AZ VMSS Disk odłączenie](/cli/azure/vmss/disk#az_vmss_disk_detach)
* [az vmss extension set](/cli/azure/vmss/extension#az_vmss_extension_set)
* [AZ VMSS Identity Assign](/cli/azure/vmss/identity#az_vmss_identity_assign)
* [AZ VMSS list-Instances](/cli/azure/vmss#az_vmss_list_instances)
* [AZ VMSS restart](/cli/azure/vmss#az_vmss_restart)
* [az vmss update-instances](/cli/azure/vmss#az_vmss_update_instances)