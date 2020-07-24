---
author: msmbaldwin
ms.service: key-vault
ms.subservice: B2C
ms.topic: include
ms.date: 07/20/2020
ms.author: msmbaldwin
ms.openlocfilehash: 826d5c2183ecc70908192695927890dcf06137b1
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87013146"
---
Utwórz zasady dostępu dla magazynu kluczy, który przyznaje uprawnienia do nazwy głównej usługi przez przekazanie `clientId` do polecenia [AZ Key magazynu Set-Policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) . Nadaj jednostce usługi uprawnienia Get, list i Set dla kluczy i wpisów tajnych.

```azurecli
az keyvault set-policy -n <your-unique-keyvault-name> --spn <clientId-of-your-service-principal> --secret-permissions delete get list set --key-permissions create decrypt delete encrypt get list unwrapKey wrapKey
```
