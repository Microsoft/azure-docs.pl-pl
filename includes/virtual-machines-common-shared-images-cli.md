---
title: plik dołączany
description: plik dołączany
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 03/24/2020
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: b49dc6ef2bfee311bc3ca524a5ccb0a4e4b5ca9c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "84793682"
---
## <a name="create-an-image-gallery"></a>Tworzenie galerii obrazów 

Galeria obrazów jest podstawowym zasobem używanym do włączania udostępniania obrazu. 

Dozwolone znaki w nazwie galerii to wielkie lub małe litery, cyfry, kropki i kropki. Nazwa galerii nie może zawierać kresek.   Nazwy galerii muszą być unikatowe w ramach subskrypcji. 

Utwórz galerię obrazów przy użyciu polecenia [AZ SIG Create](/cli/azure/sig#az-sig-create). Poniższy przykład tworzy grupę zasobów o nazwie Galeria o nazwie *myGalleryRG* w *regionie Wschodnie stany USA*i galerię o nazwie Moja *Gallery*.

```azurecli-interactive
az group create --name myGalleryRG --location eastus
az sig create --resource-group myGalleryRG --gallery-name myGallery
```

## <a name="share-the-gallery"></a>Udostępnianie galerii

Możesz udostępniać obrazy między subskrypcjami przy użyciu Role-Based Access Control (RBAC). Obrazy można udostępniać w galerii, definicji obrazu lub na poziomie wersji obrazu. Każdy użytkownik, który ma uprawnienia do odczytu wersji obrazu, nawet między subskrypcjami, będzie mógł wdrożyć maszynę wirtualną przy użyciu wersji obrazu.

Zalecamy udostępnianie innym użytkownikom na poziomie galerii. Aby uzyskać identyfikator obiektu galerii, użyj [AZ SIG show](/cli/azure/sig#az-sig-show).

```azurecli-interactive
az sig show \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --query id
```

Użyj identyfikatora obiektu jako zakresu wraz z adresem e-mail i [AZ role przypisanie Create](/cli/azure/role/assignment#az-role-assignment-create) , aby dać użytkownikowi dostęp do galerii obrazów udostępnionych. Zastąp `<email-address>` i `<gallery iD>` własnymi informacjami.

```azurecli-interactive
az role assignment create \
   --role "Reader" \
   --assignee <email address> \
   --scope <gallery ID>
```

Aby uzyskać więcej informacji o sposobie udostępniania zasobów przy użyciu funkcji RBAC, zobacz [Zarządzanie dostępem przy użyciu funkcji RBAC i interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli).
