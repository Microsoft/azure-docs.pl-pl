---
title: Tworzenie maszyny wirtualnej z wyspecjalizowanej wersji obrazu przy użyciu interfejsu wiersza polecenia platformy Azure
description: Utwórz maszynę wirtualną przy użyciu wyspecjalizowanej wersji obrazu w Shared Image Gallery użyciu interfejsu wiersza polecenia platformy Azure.
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 04/23/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.custom: devx-track-azurecli
ms.openlocfilehash: b3498037f3d2088459784ab066b8e94ba344a275
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107792187"
---
# <a name="create-a-vm-using-a-specialized-image-version-with-the-azure-cli"></a>Tworzenie maszyny wirtualnej przy użyciu wyspecjalizowanej wersji obrazu za pomocą interfejsu wiersza polecenia platformy Azure

Utwórz maszynę wirtualną na [pomocą wyspecjalizowanej wersji obrazu](./shared-image-galleries.md#generalized-and-specialized-images) przechowywanej w Shared Image Gallery. Jeśli chcesz utworzyć maszynę wirtualną przy użyciu uogólnionych wersji obrazu, zobacz Create a VM from a generalized image version (Tworzenie maszyny wirtualnej na pomocą [uogólnionych wersji obrazu).](vm-generalized-image-version-cli.md)

W tym przykładzie zastąp nazwy zasobów zgodnie z potrzebami. 

Wyświetl listę definicji obrazów w galerii przy użyciu [narzędzia az sig image-definition list,](/cli/azure/sig/image-definition#az_sig_image_definition_list) aby wyświetlić nazwę i identyfikator definicji.

```azurecli-interactive 
resourceGroup=myGalleryRG
gallery=myGallery
az sig image-definition list \
   --resource-group $resourceGroup \
   --gallery-name $gallery \
   --query "[].[name, id]" \
   --output tsv
```

Utwórz maszynę wirtualną za pomocą narzędzia [az vm create](/cli/azure/vm#az_vm_create) przy użyciu parametru --specialized, aby wskazać, że obraz jest wyspecjalizowanym obrazem. 

Użyj identyfikatora definicji obrazu dla , `--image` aby utworzyć maszynę wirtualną z najnowszej dostępnej wersji obrazu. Możesz również utworzyć maszynę wirtualną z określonej wersji, podając identyfikator wersji obrazu dla `--image` . 

W tym przykładzie tworzymy maszynę wirtualną z najnowszej wersji obrazu *myImageDefinition.*

```azurecli
az group create --name myResourceGroup --location eastus
az vm create --resource-group myResourceGroup \
    --name myVM \
    --image "/subscriptions/<Subscription ID>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition" \
    --specialized
```


## <a name="next-steps"></a>Następne kroki
[Usługa Azure Image Builder (wersja zapoznawcza)](./image-builder-overview.md) może pomóc w automatyzacji tworzenia wersji obrazu. Można jej nawet użyć do zaktualizowania i utworzenia nowej wersji obrazu z [istniejącej wersji obrazu.](./linux/image-builder-gallery-update-image-version.md) 

Możesz również utworzyć zasób Shared Image Gallery przy użyciu szablonów. Dostępnych jest kilka szablonów szybkiego startu platformy Azure: 

- [Tworzenie galerii obrazów udostępnionych](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Tworzenie definicji obrazu w usłudze Shared Image Gallery](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Tworzenie wersji obrazu w usłudze Shared Image Gallery](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Tworzenie maszyny wirtualnej na podstawie wersji obrazu](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)