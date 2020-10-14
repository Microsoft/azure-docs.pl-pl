---
title: Utwórz dysk zarządzany na podstawie wersji obrazu
description: Utwórz dysk zarządzany na podstawie wersji obrazu w udostępnionej galerii obrazów.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 10/06/2020
ms.author: cynthn
ms.reviewer: olayemio
ms.openlocfilehash: bf4a1feb91a1ac4b0bca0d6afdbac41a8be3aa4f
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/14/2020
ms.locfileid: "92049877"
---
# <a name="create-a-managed-disk-from-an-image-version"></a>Utwórz dysk zarządzany na podstawie wersji obrazu

Jeśli zachodzi taka potrzeba, można utworzyć dysk zarządzany na podstawie wersji obrazu przechowywanej w galerii obrazów udostępnionych.


## <a name="cli"></a>Interfejs wiersza polecenia

Ustaw `source` zmienną na identyfikator wersji obrazu, a następnie użyj polecenie [AZ Disk Create](/cli/azure/disk.md#az_disk_create) w celu utworzenia dysku zarządzanego. 


Możesz wyświetlić listę wersji obrazu przy użyciu polecenia [AZ SIG Image-Version list](/cli/azure/sig/image-version.md#az_sig_image_version_list). W tym przykładzie szukamy wszystkich wersji obrazu, które są częścią definicji obrazu *myImageDefinition* w galerii obrazów w *galerii* .

```azurecli-interactive
az sig image-version list \
   --resource-group myResourceGroup\
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   -o table
```


W tym przykładzie tworzymy dysk zarządzany o nazwie *myManagedDisk*w regionie *wschodniego* , w grupie zasobów o nazwie Moja *zasobów*. 

```azurecli-interactive
source="/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/galleries/<galleryName>/images/<galleryImageDefinition>/versions/<imageVersion>"

az disk create --resource-group myResourceGroup --location EastUS --name myManagedDisk --gallery-image-reference $source 
```

Jeśli dysk jest dyskiem danych, Dodaj, `--gallery-image-reference-lun` Aby określić jednostkę LUN.

## <a name="powershell"></a>PowerShell

Możesz wyświetlić listę wszystkich wersji obrazu za pomocą polecenia [Get-AzResource](/powershell/module/az.resources/get-azresource). 

```azurepowershell-interactive
Get-AzResource `
   -ResourceType Microsoft.Compute/galleries/images/versions | `
   Format-Table -Property Name,ResourceId,ResourceGroupName
```



Gdy uzyskasz wszystkie potrzebne informacje, możesz użyć polecenie [Get-AzGalleryImageVersion](/powershell/module/az.compute/get-azgalleryimageversion) , aby pobrać wersję obrazu źródłowego, która ma zostać użyta, i przypisać ją do zmiennej. W tym przykładzie pobieramy `1.0.0` wersję obrazu `myImageDefinition` definicji w `myGallery` galerii źródła w `myResourceGroup` grupie zasobów.

```azurepowershell-interactive
$sourceImgVer = Get-AzGalleryImageVersion `
   -GalleryImageDefinitionName myImageDefinition `
   -GalleryName myGallery `
   -ResourceGroupName myResourceGroup `
   -Name 1.0.0
```

Skonfiguruj pewne zmienne informacji o dysku.

```azurepowershell-interactive
$location = "East US"
$resourceGroup = "myResourceGroup"
$diskName = "myDisk"
```

Utwórz konfigurację dysku, a następnie dysk, używając identyfikatora wersji obrazu źródłowego. W przypadku `-GalleryImageReference` jednostki LUN jest wymagana tylko wtedy, gdy źródłem jest dysk danych.

```azurepowershell-interactive
$diskConfig = New-AzDiskConfig `
   -Location $location `
   -CreateOption FromImage `
   -GalleryImageReference @{Id = $sourceImgVer.Id; Lun=1}
```

Utwórz dysk.

```azurepowershell-interactive
New-AzDisk -Disk $diskConfig `
   -ResourceGroupName $resourceGroup `
   -DiskName $diskName
```

## <a name="next-steps"></a>Następne kroki

Możesz również utworzyć wersję obrazu z dysku zarządzanego przy użyciu [interfejsu wiersza polecenia platformy Azure](image-version-managed-image-cli.md) lub [programu PowerShell](image-version-managed-image-powershell.md).


