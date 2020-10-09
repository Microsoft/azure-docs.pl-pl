---
title: plik dołączany
description: plik dołączany
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 07/12/2019
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 012800806aeff81939baa2cee88e78191e4fb6c5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "82195268"
---
### <a name="create-a-user-assigned-identity"></a>Tworzenie tożsamości przypisanej przez użytkownika

Utwórz tożsamość o nazwie *myACRTasksId* w subskrypcji za pomocą polecenia [AZ Identity Create][az-identity-create] . Możesz użyć tej samej grupy zasobów, która została wcześniej użyta do utworzenia rejestru kontenerów lub innej.

```azurecli
az identity create \
  --resource-group myResourceGroup \
  --name myACRTasksId
```

Aby skonfigurować tożsamość przypisaną przez użytkownika w poniższych krokach, użyj polecenia [AZ Identity show][az-identity-show] do przechowywania identyfikatora zasobu tożsamości, identyfikatora podmiotu zabezpieczeń i identyfikatora klienta w zmiennych.

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
[az-identity-create]: /cli/azure/identity#az-identity-create
[az-identity-show]: /cli/azure/identity#az-identity-show