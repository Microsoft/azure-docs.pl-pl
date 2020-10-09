---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 07/20/2020
ms.author: msmbaldwin
ms.openlocfilehash: efa380ac243338a91354f948fdc77b2da8dd5406
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87512816"
---
Utwórzmy wpis tajny o nazwie **dbsecret**z wartością **sukcesu!**. Wpis tajny może być hasłem, parametrami połączenia SQL lub innymi informacjami, które są potrzebne do zapewnienia bezpiecznego i dostępnego dla aplikacji. 

Aby dodać wpis tajny do nowo utworzonego magazynu kluczy, użyj interfejsu wiersza polecenia platformy Azure [AZ Key Secret Set](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-set) :

```azurecli
az keyvault secret set --vault-name "<your-unique-keyvault-name>" --name "mySecret" --value "Success!"
```