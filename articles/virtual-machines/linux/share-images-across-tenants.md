---
title: Udostępnianie obrazów galerii między dzierżawcami
description: Dowiedz się, jak udostępniać obrazy maszyn wirtualnych w dzierżawach platformy Azure przy użyciu udostępnionych galerii obrazów przy użyciu interfejsu wiersza polecenia platformy Azure.
author: axayjo
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 05/04/2019
ms.author: akjosh
ms.reviewer: cynthn
ms.custom: devx-track-azurecli
ms.openlocfilehash: 2ef788659c871a9bcef6f519664689eacda94daa
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102552885"
---
# <a name="share-gallery-vm-images-across-azure-tenants-using-the-azure-cli"></a>Udostępnianie obrazów maszyn wirtualnych galerii w ramach dzierżawców platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure

Udostępnione Galerie obrazów umożliwiają udostępnianie obrazów przy użyciu funkcji RBAC platformy Azure. Za pomocą funkcji RBAC platformy Azure można udostępniać obrazy w dzierżawie, a nawet użytkownikom spoza dzierżawy. Aby uzyskać więcej informacji na temat tej prostej opcji udostępniania, zobacz [udostępnianie galerii](./shared-images-portal.md#share-the-gallery).

[!INCLUDE [virtual-machines-share-images-across-tenants](../../../includes/virtual-machines-share-images-across-tenants.md)]

> [!IMPORTANT]
> Nie można użyć portalu do wdrożenia maszyny wirtualnej na podstawie obrazu w innej dzierżawie platformy Azure. Aby utworzyć maszynę wirtualną na podstawie obrazu udostępnianego między dzierżawcami, musisz użyć interfejsu wiersza polecenia platformy Azure lub [programu PowerShell](../windows/share-images-across-tenants.md).

## <a name="create-a-vm-using-azure-cli"></a>Tworzenie maszyny wirtualnej przy użyciu interfejsu wiersza polecenia platformy Azure

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

Utwórz maszynę wirtualną. Zastąp informacje w podanym przykładzie własnym.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image "/subscriptions/<Tenant 1 subscription>/resourceGroups/<Resource group>/providers/Microsoft.Compute/galleries/<Gallery>/images/<Image definition>/versions/<version>" \
  --admin-username azureuser \
  --generate-ssh-keys
```

## <a name="next-steps"></a>Następne kroki

Jeśli napotkasz jakiekolwiek problemy, możesz [rozwiązać problemy z udostępnionymi galeriami obrazów](../troubleshooting-shared-images.md).
