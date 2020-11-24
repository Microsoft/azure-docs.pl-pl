---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 04/25/2019
ms.author: cynthn
ms.custom: include file, devx-track-azurecli
ms.openlocfilehash: fe37b0c9dbc16520a0dcb0993236db2797da6b68
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/24/2020
ms.locfileid: "95553942"
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

Zaktualizuj Opis galerii za pomocą polecenia ([AZ SIG Update](/cli/azure/sig?view=azure-cli-latest#az-sig-update). 

```azurecli-interactive
az sig update \
   --gallery-name myGallery \
   --resource-group myGalleryRG \
   --set description="My updated description."
```


Zaktualizuj opis definicji obrazu za pomocą polecenia [AZ SIG Image-Definition Update](/cli/azure/sig/image-definition?view=azure-cli-latest#az-sig-image-definition-update).

```azurecli-interactive
az sig image-definition update \
   --gallery-name myGallery\
   --resource-group myGalleryRG \
   --gallery-image-definition myImageDefinition \
   --set description="My updated description."
```

Zaktualizuj wersję obrazu, aby dodać region do replikacji przy użyciu polecenia [AZ SIG Image-Version Update](/cli/azure/sig/image-definition?view=azure-cli-latest#az-sig-image-definition-update). Ta zmiana potrwa trochę czasu, ponieważ obraz zostanie zreplikowany do nowego regionu.

```azurecli-interactive
az sig image-version update \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --add publishingProfile.targetRegions  name=eastus
```

Ten przykład pokazuje, jak używać [aktualizacji AZ SIG Image-Version](/cli/azure/sig/image-definition?view=azure-cli-latest#az-sig-image-definition-update) do wykluczania tej wersji obrazu z używania jako *najnowszego* obrazu.

```azurecli-interactive
az sig image-version update \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --set publishingProfile.excludeFromLatest=true
```

Ten przykład pokazuje, jak używać [aktualizacji AZ SIG Image-Version](/cli/azure/sig/image-definition?view=azure-cli-latest#az-sig-image-definition-update) w celu uwzględnienia tej wersji obrazu w przypadku *najnowszego* obrazu.

```azurecli-interactive
az sig image-version update \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --set publishingProfile.excludeFromLatest=false
```

## <a name="delete-resources"></a>Usuwanie zasobów

Musisz usunąć zasoby w odwrotnej kolejności, usuwając najpierw wersję obrazu. Po usunięciu wszystkich wersji obrazu można usunąć definicję obrazu. Po usunięciu wszystkich definicji obrazu można usunąć galerię. 

Usuń wersję obrazu przy użyciu polecenia [AZ SIG Image-Version Delete](/cli/azure/sig/image-version?view=azure-cli-latest#az-sig-image-version-delete).

```azurecli-interactive
az sig image-version delete \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 
```

Usuń definicję obrazu za pomocą polecenia [AZ SIG Image-Definition Delete](/cli/azure/sig/image-definition?view=azure-cli-latest#az-sig-image-definition-delete).

```azurecli-interactive
az sig image-definition delete \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition
```


Usuń galerię obrazów przy użyciu polecenia [AZ SIG Delete](/cli/azure/sig?view=azure-cli-latest#az-sig-delete).

```azurecli-interactive
az sig delete \
   --resource-group myGalleryRG \
   --gallery-name myGallery
```