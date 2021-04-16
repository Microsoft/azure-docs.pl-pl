---
title: Tworzenie maszyny wirtualnej na użyciu uogólninego obrazu przy użyciu interfejsu wiersza polecenia platformy Azure
description: Utwórz maszynę wirtualną z uogólnionych wersji obrazu przy użyciu interfejsu wiersza polecenia platformy Azure.
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.topic: how-to
ms.workload: infrastructure
ms.date: 05/04/2020
ms.author: cynthn
ms.custom: devx-track-azurecli
ms.openlocfilehash: 41c6995f16b836231142520362f9aace7d91ffe0
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107500315"
---
# <a name="create-a-vm-from-a-generalized-image-version-using-the-azure-cli"></a>Tworzenie maszyny wirtualnej na użyciu uogólnionych wersji obrazu przy użyciu interfejsu wiersza polecenia platformy Azure

Utwórz maszynę wirtualną na stronie [uogólnionych wersji obrazu](./shared-image-galleries.md#generalized-and-specialized-images) przechowywanych w Shared Image Gallery. Jeśli chcesz utworzyć maszynę wirtualną przy użyciu wyspecjalizowanego obrazu, zobacz [Tworzenie maszyny wirtualnej na pomocą wyspecjalizowanego obrazu.](vm-specialized-image-version-powershell.md) 


## <a name="get-the-image-id"></a>Uzyskiwanie identyfikatora obrazu

Wyświetl listę definicji obrazów w galerii przy użyciu [narzędzia az sig image-definition list,](/cli/azure/sig/image-definition#az-sig-image-definition-list) aby wyświetlić nazwę i identyfikator definicji.

```azurecli-interactive 
resourceGroup=myGalleryRG
gallery=myGallery
az sig image-definition list --resource-group $resourceGroup --gallery-name $gallery --query "[].[name, id]" --output tsv
```

## <a name="create-the-vm"></a>Tworzenie maszyny wirtualnej

Utwórz maszynę wirtualną przy użyciu polecenia [az vm create](/cli/azure/vm#az-vm-create). Aby użyć najnowszej wersji obrazu, ustaw identyfikator `--image` definicji obrazu. 

W razie potrzeby w tym przykładzie zastąp nazwy zasobów. 

```azurecli-interactive 
imgDef="/subscriptions/<subscription ID where the gallery is located>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition"
vmResourceGroup=myResourceGroup
location=eastus
vmName=myVM
adminUsername=azureuser


az group create --name $vmResourceGroup --location $location

az vm create\
   --resource-group $vmResourceGroup \
   --name $vmName \
   --image $imgDef \
   --admin-username $adminUsername \
   --generate-ssh-keys
```

Możesz również użyć określonej wersji, używając identyfikatora wersji obrazu dla `--image` parametru . Aby na przykład użyć obrazu w *wersji 1.0.0,* wpisz: `--image "/subscriptions/<subscription ID where the gallery is located>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition/versions/1.0.0"` .

## <a name="next-steps"></a>Następne kroki

[Usługa Azure Image Builder (wersja zapoznawcza)](./image-builder-overview.md) może pomóc zautomatyzować tworzenie wersji obrazu. Można jej nawet użyć do zaktualizowania i utworzenia nowej wersji obrazu z [istniejącej wersji obrazu.](./linux/image-builder-gallery-update-image-version.md)