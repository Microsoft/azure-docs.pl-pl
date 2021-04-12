---
title: Udostępnianie obrazów galerii między dzierżawcami
description: Dowiedz się, jak tworzyć zestawy skalowania przy użyciu obrazów, które są współużytkowane przez dzierżawy platformy Azure przy użyciu udostępnionych galerii obrazów.
author: cynthn
ms.author: cynthn
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: shared-image-gallery
ms.date: 04/05/2019
ms.reviewer: akjosh
ms.custom: akjosh, devx-track-azurecli
ms.openlocfilehash: b6b5e48997da03c7f180c62a9142c99b05c95b46
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2021
ms.locfileid: "106444041"
---
# <a name="share-images-across-tenants-with-shared-image-gallery"></a>Udostępnianie obrazów między dzierżawcami za pomocą udostępnionej galerii obrazów

[!INCLUDE [virtual-machines-share-images-across-tenants](../../includes/virtual-machines-share-images-across-tenants.md)]


## <a name="create-a-scale-set-using-azure-cli"></a>Create a scale set using Azure CLI (Tworzenie zestawu skalowania przy użyciu interfejsu wiersza polecenia platformy Azure)

Zaloguj się do jednostki usługi dla dzierżawy 1 przy użyciu identyfikatora appID, klucz aplikacji i identyfikator dzierżawy 1. `az account show --query "tenantId"`W razie potrzeby można użyć programu w celu uzyskania identyfikatorów dzierżawy.

```azurecli-interactive
az account clear
az login --service-principal -u '<app ID>' -p '<Secret>' --tenant '<tenant 1 ID>'
az account get-access-token 
```
 
Zaloguj się do jednostki usługi dla dzierżawy 2 przy użyciu identyfikatora appID, klucz aplikacji i identyfikator dzierżawy 2:

```azurecli-interactive
az login --service-principal -u '<app ID>' -p '<Secret>' --tenant '<tenant 2 ID>'
az account get-access-token
```

Utwórz zestaw skalowania. Zastąp informacje w podanym przykładzie własnym.

```azurecli-interactive
az vmss create \
  -g myResourceGroup \
  -n myScaleSet \
  --image "/subscriptions/<Tenant 1 subscription>/resourceGroups/<Resource group>/providers/Microsoft.Compute/galleries/<Gallery>/images/<Image definition>/versions/<version>" \
  --admin-username azureuser \
  --generate-ssh-keys
```

## <a name="next-steps"></a>Następne kroki

Jeśli napotkasz jakiekolwiek problemy, możesz [rozwiązać problemy z udostępnionymi galeriami obrazów](../virtual-machines/troubleshooting-shared-images.md).
