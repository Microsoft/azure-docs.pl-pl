---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 07/12/2019
ms.author: danlep
ms.custom: include file, devx-track-azurecli
ms.openlocfilehash: 70bd79ef944e5918d750a130bd2e2b2c2b656bf4
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781148"
---
### <a name="create-a-user-assigned-identity"></a>Tworzenie tożsamości przypisanej przez użytkownika

Utwórz tożsamość o *nazwie myACRTasksId w* subskrypcji przy użyciu [polecenia az identity create.][az-identity-create] Możesz użyć tej samej grupy zasobów, która została wcześniej użyta do utworzenia rejestru kontenerów, lub innej.

```azurecli
az identity create \
  --resource-group myResourceGroup \
  --name myACRTasksId
```

Aby skonfigurować tożsamość przypisaną przez użytkownika w poniższych krokach, użyj polecenia [az identity show,][az-identity-show] aby zapisać identyfikator zasobu tożsamości, identyfikator podmiotu zabezpieczeń i identyfikator klienta w zmiennych.

```azurecli
# Get resource ID of the user-assigned identity
resourceID=$(az identity show \
  --resource-group myResourceGroup \
  --name myACRTasksId \
  --query id --output tsv)

# Get principal ID of the task's user-assigned identity
principalID=$(az identity show \
  --resource-group myResourceGroup \
  --name myACRTasksId \
  --query principalId --output tsv)

# Get client ID of the user-assigned identity
clientID=$(az identity show \
  --resource-group myResourceGroup \
  --name myACRTasksId \
  --query clientId --output tsv)
```

<!-- LINKS - Internal -->
[az-identity-create]: /cli/azure/identity#az_identity_create
[az-identity-show]: /cli/azure/identity#az_identity_show
