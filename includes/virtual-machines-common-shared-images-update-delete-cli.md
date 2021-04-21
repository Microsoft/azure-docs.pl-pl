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
ms.openlocfilehash: b966f68e19794aadebff76e3e9b29ed79a32eebe
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107800133"
---
## <a name="update-resources"></a>Aktualizowanie zasobów

Istnieją pewne ograniczenia dotyczące tego, co można zaktualizować. Można zaktualizować następujące elementy: 

Galeria obrazów udostępnionych:
- Opis

Definicja obrazu:
- Zalecane procesory wirtualne
- Zalecana pamięć
- Opis
- Data zakończenia cyklu życia

Wersja obrazu:
- Liczba replik regionalnych
- Regiony docelowe
- Wykluczenie z najnowszej wersji
- Data zakończenia cyklu życia

Jeśli planujesz dodawanie regionów repliki, nie usuwaj źródłowego obrazu zarządzanego. Źródłowy obraz zarządzany jest wymagany do replikowania wersji obrazu do dodatkowych regionów. 

Zaktualizuj opis galerii przy użyciu funkcji ([az sig update](/cli/azure/sig#az_sig_update). 

```azurecli-interactive
az sig update \
   --gallery-name myGallery \
   --resource-group myGalleryRG \
   --set description="My updated description."
```


Zaktualizuj opis definicji obrazu za pomocą [az sig image-definition update](/cli/azure/sig/image-definition#az_sig_image_definition_update).

```azurecli-interactive
az sig image-definition update \
   --gallery-name myGallery\
   --resource-group myGalleryRG \
   --gallery-image-definition myImageDefinition \
   --set description="My updated description."
```

Zaktualizuj wersję obrazu, aby dodać region do replikacji do przy użyciu [az sig image-version update](/cli/azure/sig/image-definition#az_sig_image_definition_update). Ta zmiana potrwa trochę czasu, ponieważ obraz zostanie zreplikowany do nowego regionu.

```azurecli-interactive
az sig image-version update \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --add publishingProfile.targetRegions  name=eastus
```

W tym przykładzie pokazano, jak za pomocą [funkcji az sig image-version update](/cli/azure/sig/image-definition#az_sig_image_definition_update) wykluczyć tę wersję obrazu z użycia jako *najnowszego obrazu.*

```azurecli-interactive
az sig image-version update \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --set publishingProfile.excludeFromLatest=true
```

W tym przykładzie pokazano, jak za pomocą [funkcji az sig image-version update](/cli/azure/sig/image-definition#az_sig_image_definition_update) uwzględnić tę wersję obrazu w rozważaniu *najnowszego obrazu.*

```azurecli-interactive
az sig image-version update \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --set publishingProfile.excludeFromLatest=false
```

## <a name="delete-resources"></a>Usuwanie zasobów

Musisz usunąć zasoby w odwrotnej kolejności, najpierw usuwając wersję obrazu. Po usunięciu wszystkich wersji obrazu można usunąć definicję obrazu. Po usunięciu wszystkich definicji obrazów możesz usunąć galerię. 

Usuń wersję obrazu za pomocą [narzędzia az sig image-version delete](/cli/azure/sig/image-version#az_sig_image_version_delete).

```azurecli-interactive
az sig image-version delete \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 
```

Usuń definicję obrazu za pomocą [narzędzia az sig image-definition delete](/cli/azure/sig/image-definition#az_sig_image_definition_delete).

```azurecli-interactive
az sig image-definition delete \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition
```


Usuń galerię obrazów za pomocą [narzędzia az sig delete](/cli/azure/sig#az_sig_delete).

```azurecli-interactive
az sig delete \
   --resource-group myGalleryRG \
   --gallery-name myGallery
```