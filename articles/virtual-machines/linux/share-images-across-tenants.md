---
title: Udostępnianie obrazów galerii między dzierżawcami na platformie Azure
description: Dowiedz się, jak udostępniać obrazy maszyn wirtualnych w dzierżawach platformy Azure przy użyciu udostępnionych galerii obrazów.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 05/04/2019
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: 62f136eb1c2684bfd307a05ccbb4836739da6f3a
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/05/2020
ms.locfileid: "82789457"
---
# <a name="share-gallery-vm-images-across-azure-tenants"></a>Udostępnianie obrazów maszyn wirtualnych z galerii w ramach dzierżawców platformy Azure

Udostępnione Galerie obrazów umożliwiają udostępnianie obrazów przy użyciu RBAC. RBAC można używać do udostępniania obrazów w dzierżawie, a nawet do osób poza dzierżawcą. Aby uzyskać więcej informacji na temat tej prostej opcji udostępniania, zobacz [udostępnianie galerii](/azure/virtual-machines/linux/shared-images-portal#share-the-gallery).

[!INCLUDE [virtual-machines-share-images-across-tenants](../../../includes/virtual-machines-share-images-across-tenants.md)]

> [!IMPORTANT]
> Nie można użyć portalu do wdrożenia maszyny wirtualnej na podstawie obrazu w innej dzierżawie platformy Azure. Aby utworzyć maszynę wirtualną na podstawie obrazu udostępnianego między dzierżawcami, musisz użyć interfejsu wiersza polecenia platformy Azure lub [programu PowerShell](../windows/share-images-across-tenants.md).

## <a name="create-a-vm-using-azure-cli"></a>Tworzenie maszyny wirtualnej przy użyciu interfejsu wiersza polecenia platformy Azure

Zaloguj się do jednostki usługi dla dzierżawy 1 przy użyciu identyfikatora appID, klucz aplikacji i identyfikator dzierżawy 1. W razie potrzeby `az account show --query "tenantId"` można użyć programu w celu uzyskania identyfikatorów dzierżawy.

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

Jeśli napotkasz jakiekolwiek problemy, możesz [rozwiązać problemy z udostępnionymi galeriami obrazów](troubleshooting-shared-images.md).