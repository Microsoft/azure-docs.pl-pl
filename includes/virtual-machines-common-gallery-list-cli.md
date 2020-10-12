---
title: plik dołączany
description: plik dołączany
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 01/28/2020
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 0e8972b1b2bfaac12baee1ea823429749ed70461
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "82792755"
---
## <a name="list-information"></a>Informacje o liście

Pobierz lokalizację, stan i inne informacje o dostępnych galeriach obrazów przy użyciu polecenia [AZ SIG list](/cli/azure/sig#az-sig-list).

```azurecli-interactive 
az sig list -o table
```

Wyświetl listę definicji obrazów w galerii, w tym informacje o typie i stanie systemu operacyjnego za pomocą polecenia [AZ SIG Image-Definition list](/cli/azure/sig/image-definition#az-sig-image-definition-list).

```azurecli-interactive 
az sig image-definition list --resource-group myGalleryRG --gallery-name myGallery -o table
```

Wyświetl listę wersji obrazu udostępnionego w galerii przy użyciu polecenia [AZ SIG Image-Version list](/cli/azure/sig/image-version#az-sig-image-version-list).

```azurecli-interactive
az sig image-version list --resource-group myGalleryRG --gallery-name myGallery --gallery-image-definition myImageDefinition -o table
```

Pobierz identyfikator wersji obrazu przy użyciu polecenia [AZ SIG Image-Version show](/cli/azure/sig/image-version#az-sig-image-version-show).

```azurecli-interactive
az sig image-version show \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --query "id"
```