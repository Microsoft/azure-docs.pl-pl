---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: include
ms.date: 08/13/2020
ms.author: danlep
ms.custom: include file, devx-track-azurecli
ms.openlocfilehash: 173c9156f253e43111299b53287e97ab7b2c0aa5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92746932"
---
## <a name="create-azure-container-registry"></a>Tworzenie rejestru kontenerów platformy Azure

Przed utworzeniem rejestru kontenerów należy zapewnić *grupę zasobów*, w której zostanie wdrożony. Grupa zasobów to kolekcja logiczna przeznaczona do wdrażania wszystkich zasobów platformy Azure i zarządzania nimi.

Utwórz grupę zasobów za pomocą polecenia [az group create][az-group-create]. W poniższym przykładzie grupa zasobów o nazwie *myResourceGroup* zostanie utworzona w regionie *eastus*:

```azurecli
az group create --name myResourceGroup --location eastus
```

Po utworzeniu grupy zasobów utwórz rejestr kontenerów platformy Azure za pomocą polecenia [az acr create][az-acr-create]. Nazwa rejestru kontenerów musi być unikatowa w obrębie platformy Azure i może zawierać od 5 do 50 znaków alfanumerycznych. Zamień wartość `<acrName>` na unikatową nazwę rejestru:

```azurecli
az acr create --resource-group myResourceGroup --name <acrName> --sku Basic
```

Oto częściowe dane wyjściowe dla nowego rejestru kontenerów platformy Azure o nazwie *mycontainerregistry082*:

```output
{
  "creationDate": "2020-07-16T21:54:47.297875+00:00",
  "id": "/subscriptions/<Subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/mycontainerregistry082",
  "location": "eastus",
  "loginServer": "mycontainerregistry082.azurecr.io",
  "name": "mycontainerregistry082",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "sku": {
    "name": "Basic",
    "tier": "Basic"
  },
  "status": null,
  "storageAccount": null,
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

Dalsza część tego samouczka odnosi się do wartości `<acrName>` jako symbolu zastępczego dla nazwy rejestru kontenerów, która została wybrana w tym kroku.

## <a name="log-in-to-container-registry"></a>Logowanie do rejestru kontenerów

Należy zalogować się do wystąpienia usługi Azure Container Registry przed wypchnięciem do niego obrazów. Aby wykonać tę operację, użyj polecenia [az acr login][az-acr-login]. Należy wprowadzić unikatową nazwę wybraną dla rejestru kontenerów podczas jego tworzenia.

```azurecli
az acr login --name <acrName>
```

Na przykład:

```azurecli
az acr login --name mycontainerregistry082
```

Po ukończeniu polecenie zwraca ciąg `Login Succeeded`:

```output
Login Succeeded
```

<!-- LINKS - Internal -->
[az-acr-create]: /cli/azure/acr#az-acr-create
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-group-create]: /cli/azure/group#az-group-create
