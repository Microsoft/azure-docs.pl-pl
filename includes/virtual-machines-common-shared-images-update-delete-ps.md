---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 04/25/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: d2a85f3947e9993e5d1853e45c6d03586a074cf6
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/26/2020
ms.locfileid: "67183194"
---
## <a name="update-resources"></a>Aktualizowanie zasobów

Istnieją pewne ograniczenia dotyczące tego, co można zaktualizować. Następujące elementy można zaktualizować: 

Galeria obrazów udostępnionych:
- Opis

Definicja obrazu:
- Zalecane procesorów wirtualnych vCPU
- Zalecana pamięć
- Opis
- Data zakończenia okresu istnienia

Wersja obrazu:
- Liczba replik regionalnych
- Regiony docelowe
- Wykluczanie z najnowszych
- Data zakończenia okresu istnienia

Jeśli planujesz dodanie regionów replik, nie usuwaj źródłowego obrazu zarządzanego. Źródłowy obraz zarządzany jest wymagany do replikowania wersji obrazu do dodatkowych regionów. 

Aby zaktualizować opis galerii, użyj polecenie [Update-AzGallery](https://docs.microsoft.com/powershell/module/az.compute/update-azgallery).

```azurepowershell-interactive
Update-AzGallery `
   -Name $gallery.Name ` 
   -ResourceGroupName $resourceGroup.Name
```

W tym przykładzie pokazano, jak za pomocą [Update-AzGalleryImageDefinition](https://docs.microsoft.com/powershell/module/az.compute/update-azgalleryimagedefinition) zaktualizować datę końca okresu ważności dla definicji obrazu.

```azurepowershell-interactive
Update-AzGalleryImageDefinition `
   -GalleryName $gallery.Name `
   -Name $galleryImage.Name `
   -ResourceGroupName $resourceGroup.Name `
   -EndOfLifeDate 01/01/2030
```

Ten przykład pokazuje, jak używać [Update-AzGalleryImageVersion](https://docs.microsoft.com/powershell/module/az.compute/update-azgalleryimageversion) do wykluczania tej wersji obrazu z używania jako *najnowszego* obrazu.

```azurepowershell-interactive
Update-AzGalleryImageVersion `
   -GalleryImageDefinitionName $galleryImage.Name `
   -GalleryName $gallery.Name `
   -Name $galleryVersion.Name `
   -ResourceGroupName $resourceGroup.Name `
   -PublishingProfileExcludeFromLatest
```


## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Podczas usuwania zasobów należy zacząć od ostatniego elementu w zagnieżdżonych zasobach — wersji obrazu. Po usunięciu wersji można usunąć definicję obrazu. Nie można usunąć galerii, dopóki nie zostaną usunięte wszystkie znajdujące się w niej zasoby.

```azurepowershell-interactive
$resourceGroup = "myResourceGroup"
$gallery = "myGallery"
$imageDefinition = "myImageDefinition"
$imageVersion = "myImageVersion"

Remove-AzGalleryImageVersion `
   -GalleryImageDefinitionName $imageDefinition `
   -GalleryName $gallery `
   -Name $imageVersion `
   -ResourceGroupName $resourceGroup

Remove-AzGalleryImageDefinition `
   -ResourceGroupName $resourceGroup `
   -GalleryName $gallery `
   -GalleryImageDefinitionName $imageDefinition

Remove-AzGallery `
   -Name $gallery `
   -ResourceGroupName $resourceGroup

Remove-AzResourceGroup -Name $resourceGroup
```

