---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 07/20/2020
ms.author: msmbaldwin
ms.openlocfilehash: d1a67a131a94bc017eaf2199546ef08f0291cd54
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "102245118"
---
Utwórzmy wpis tajny o nazwie **dbsecret** z wartością **sukcesu!**. Wpis tajny może być hasłem, parametrami połączenia SQL lub innymi informacjami, które są potrzebne do zapewnienia bezpiecznego i dostępnego dla aplikacji. 

Aby dodać wpis tajny do nowo utworzonego magazynu kluczy, użyj interfejsu wiersza polecenia platformy Azure [AZ Key Secret Set](/cli/azure/keyvault/secret#az-keyvault-secret-set) :

```azurecli
az keyvault secret set --vault-name "<your-unique-keyvault-name>" --name "mySecret" --value "Success!"
```