---
title: dołączanie pliku
description: dołączanie pliku
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 09/20/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 1ec3ecdafb8e475f5f13372789528612ccd7b8b9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "66226004"
---
## <a name="using-rbac-to-share-images"></a>Udostępnianie obrazów przy użyciu RBAC

Możesz udostępniać obrazy między subskrypcjami przy użyciu Access Control opartej na rolach (RBAC). Każdy użytkownik, który ma uprawnienia do odczytu wersji obrazu, nawet między subskrypcjami, będzie mógł wdrożyć maszynę wirtualną przy użyciu wersji obrazu.

Aby uzyskać więcej informacji o sposobie udostępniania zasobów przy użyciu funkcji RBAC, zobacz [Zarządzanie dostępem przy użyciu funkcji RBAC i interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli).


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