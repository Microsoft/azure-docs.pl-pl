---
title: plik dołączany
description: plik dołączany
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 03/18/2020
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 10c2b447a3f174afe93f56084827756d24d982cc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "82792194"
---
## <a name="create-an-image-gallery"></a>Tworzenie galerii obrazów 

Galeria obrazów jest podstawowym zasobem używanym do włączania udostępniania obrazu. Dozwolone znaki w nazwie galerii to wielkie lub małe litery, cyfry, kropki i kropki. Nazwa galerii nie może zawierać kresek. Nazwy galerii muszą być unikatowe w ramach subskrypcji. 

Utwórz galerię obrazów przy użyciu polecenia [New-AzGallery](https://docs.microsoft.com/powershell/module/az.compute/new-azgallery). Poniższy przykład tworzy galerię o nazwie Moja *Gallery* w grupie zasobów *myGalleryRG* .

```azurepowershell-interactive
$resourceGroup = New-AzResourceGroup `
   -Name 'myGalleryRG' `
   -Location 'West Central US'  
$gallery = New-AzGallery `
   -GalleryName 'myGallery' `
   -ResourceGroupName $resourceGroup.ResourceGroupName `
   -Location $resourceGroup.Location `
   -Description 'Shared Image Gallery for my organization'  
```
   

## <a name="share-the-gallery"></a>Udostępnianie galerii

Zalecamy Udostępnianie dostępu na poziomie galerii obrazów. Użyj adresu e-mail i polecenia cmdlet [Get-AzADUser](/powershell/module/az.resources/get-azaduser) , aby uzyskać identyfikator obiektu dla użytkownika, a następnie użyj polecenie [New-AzRoleAssignment](/powershell/module/Az.Resources/New-AzRoleAssignment) w celu uzyskania dostępu do galerii. Zastąp przykładową wiadomość e-mail, alinne_montes@contoso.com w tym przykładzie, własnymi informacjami.

```azurepowershell-interactive
# Get the object ID for the user
$user = Get-AzADUser -StartsWith alinne_montes@contoso.com
# Grant access to the user for our gallery
New-AzRoleAssignment `
   -ObjectId $user.Id `
   -RoleDefinitionName Reader `
   -ResourceName $gallery.Name `
   -ResourceType Microsoft.Compute/galleries `
   -ResourceGroupName $resourceGroup.ResourceGroupName

```

