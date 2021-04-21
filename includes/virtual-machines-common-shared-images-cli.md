---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 03/24/2020
ms.author: cynthn
ms.custom: include file, devx-track-azurecli
ms.openlocfilehash: fc35f277bfafa80f6239ef807f1a83591646a503
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107799926"
---
## <a name="create-an-image-gallery"></a>Tworzenie galerii obrazów 

Galeria obrazów jest podstawowym zasobem używanym do włączania udostępniania obrazów. 

Dozwolone znaki nazwy galerii to wielkie lub małe litery, cyfry, kropki i kropki. Nazwa galerii nie może zawierać łączników.   Nazwy galerii muszą być unikatowe w ramach subskrypcji. 

Utwórz galerię obrazów za pomocą [narzędzia az sig create](/cli/azure/sig#az_sig_create). Poniższy przykład tworzy grupę zasobów o nazwie galeria o nazwie *myGalleryRG* w regionach *Wschodnie* usa oraz galerię *o nazwie myGallery*.

```azurecli-interactive
az group create --name myGalleryRG --location eastus
az sig create --resource-group myGalleryRG --gallery-name myGallery
```

## <a name="share-the-gallery"></a>Udostępnianie galerii

Obrazy można udostępniać między subskrypcjami przy użyciu Role-Based Access Control (RBAC). Obrazy można udostępniać na poziomie galerii, definicji obrazu lub wersji obrazu. Każdy użytkownik, który ma uprawnienia do odczytu do wersji obrazu, nawet w różnych subskrypcjach, będzie mógł wdrożyć maszynę wirtualną przy użyciu wersji obrazu.

Zalecamy udostępnianie innym użytkownikom na poziomie galerii. Aby uzyskać identyfikator obiektu galerii, użyj [az sig show](/cli/azure/sig#az_sig_show).

```azurecli-interactive
az sig show \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --query id
```

Użyj identyfikatora obiektu jako zakresu, adresu e-mail i az [role assignment create,](/cli/azure/role/assignment#az_role_assignment_create) aby udzielić użytkownikowi dostępu do galerii obrazów udostępnionych. Zastąp `<email-address>` i `<gallery iD>` własnymi informacjami.

```azurecli-interactive
az role assignment create \
   --role "Reader" \
   --assignee <email address> \
   --scope <gallery ID>
```

Aby uzyskać więcej informacji na temat udostępniania zasobów przy użyciu kontroli RBAC, zobacz [Manage access using RBAC and Azure CLI (Zarządzanie](../articles/role-based-access-control/role-assignments-cli.md)dostępem przy użyciu funkcji RBAC i interfejsu wiersza polecenia platformy Azure).