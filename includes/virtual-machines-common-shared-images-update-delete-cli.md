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
ms.openlocfilehash: 8d0f9866864ca4b02ca6238be2ac44537a586c2d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "67183243"
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

Zaktualizuj Opis galerii za pomocą polecenia ([AZ SIG Update](https://docs.microsoft.com/cli/azure/sig?view=azure-cli-latest#az-sig-update). 

```azurecli-interactive
az sig update \
   --gallery-name myGallery \
   --resource-group myGalleryRG \
   --set description="My updated description."
```


Zaktualizuj opis definicji obrazu za pomocą polecenia [AZ SIG Image-Definition Update](https://docs.microsoft.com/cli/azure/sig/image-definition?view=azure-cli-latest#az-sig-image-definition-update).

```azurecli-interactive
az sig image-definition update \
   --gallery-name myGallery\
   --resource-group myGalleryRG \
   --gallery-image-definition myImageDefinition \
   --set description="My updated description."
```

Zaktualizuj wersję obrazu, aby dodać region do replikacji przy użyciu polecenia [AZ SIG Image-Version Update](https://docs.microsoft.com/cli/azure/sig/image-definition?view=azure-cli-latest#az-sig-image-definition-update). Ta zmiana potrwa trochę czasu, ponieważ obraz zostanie zreplikowany do nowego regionu.

```azurecli-interactive
az sig image-version update \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --add publishingProfile.targetRegions  name=eastus
```

## <a name="delete-resources"></a>Usuwanie zasobów

Musisz usunąć zasoby w odwrotnej kolejności, usuwając najpierw wersję obrazu. Po usunięciu wszystkich wersji obrazu można usunąć definicję obrazu. Po usunięciu wszystkich definicji obrazu można usunąć galerię. 

Usuń wersję obrazu przy użyciu polecenia [AZ SIG Image-Version Delete](https://docs.microsoft.com/cli/azure/sig/image-version?view=azure-cli-latest#az-sig-image-version-delete).

```azurecli-interactive
az sig image-version delete \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 
```

Usuń definicję obrazu za pomocą polecenia [AZ SIG Image-Definition Delete](https://docs.microsoft.com/cli/azure/sig/image-definition?view=azure-cli-latest#az-sig-image-definition-delete).

```azurecli-interactive
az sig image-definition delete \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition
```


Usuń galerię obrazów przy użyciu polecenia [AZ SIG Delete](https://docs.microsoft.com/cli/azure/sig?view=azure-cli-latest#az-sig-delete).

```azurecli-interactive
az sig delete \
   --resource-group myGalleryRG \
   --gallery-name myGallery
```
