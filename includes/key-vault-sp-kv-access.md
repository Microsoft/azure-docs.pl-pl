---
author: msmbaldwin
ms.service: key-vault
ms.subservice: B2C
ms.topic: include
ms.date: 07/20/2020
ms.author: msmbaldwin
ms.openlocfilehash: 826d5c2183ecc70908192695927890dcf06137b1
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "87013146"
---
Utwórz zasady dostępu dla magazynu kluczy, który przyznaje uprawnienia do nazwy głównej usługi przez przekazanie `clientId` do polecenia [AZ Key magazynu Set-Policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) . Nadaj jednostce usługi uprawnienia Get, list i Set dla kluczy i wpisów tajnych.

```azurecli
az keyvault set-policy -n <your-unique-keyvault-name> --spn <clientId-of-your-service-principal> --secret-permissions delete get list set --key-permissions create decrypt delete encrypt get list unwrapKey wrapKey
```
