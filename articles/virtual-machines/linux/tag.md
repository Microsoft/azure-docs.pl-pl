---
title: Jak oznaczyć maszynę wirtualną systemu Azure Linux
description: Dowiedz się więcej o oznaczaniu maszyny wirtualnej systemu Azure Linux utworzonej na platformie Azure przy użyciu modelu wdrażania usługi Resource Manager.
services: virtual-machines-linux
documentationcenter: ''
author: mmccrory
manager: gwallace
tags: azure-resource-manager
ms.assetid: ca0e17e5-d78e-42e6-9dad-c1e8f1c58027
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/28/2017
ms.author: memccror
ms.openlocfilehash: fd4a93f4c0b2f052fe5c9890bee01e5da0dcead2
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81460957"
---
# <a name="how-to-tag-a-linux-virtual-machine-in-azure"></a>Jak oznaczyć maszynę wirtualną systemu Linux na platformie Azure
W tym artykule opisano różne sposoby tagowania maszyny wirtualnej systemu Linux na platformie Azure za pośrednictwem modelu wdrażania usługi Resource Manager. Tagi są parami kluczy/wartości zdefiniowanych przez użytkownika, które można umieścić bezpośrednio w zasobie lub grupie zasobów. Platforma Azure obsługuje obecnie do 50 tagów na grupę zasobów i zasobów. Tagi mogą być umieszczane w zasobie w momencie tworzenia lub dodawane do istniejącego zasobu. Należy pamiętać, że tagi są obsługiwane dla zasobów utworzonych tylko za pośrednictwem modelu wdrażania Menedżera zasobów.

[!INCLUDE [virtual-machines-common-tag](../../../includes/virtual-machines-common-tag.md)]

## <a name="tagging-with-azure-cli"></a>Oznaczanie za pomocą interfejsu wiersza polecenia platformy Azure

Aby rozpocząć, potrzebujesz najnowszego interfejsu [wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) zainstalowanego i zalogowanego do konta platformy Azure przy użyciu [logowania az.](/cli/azure/reference-index#az-login)

Za pomocą tego polecenia można wyświetlić wszystkie właściwości danej maszyny wirtualnej, w tym znaczniki:

```azurecli
az vm show --resource-group MyResourceGroup --name MyTestVM
```

Aby dodać nowy tag maszyny Wirtualnej za pośrednictwem `azure vm update` interfejsu wiersza polecenia platformy Azure, można użyć polecenia wraz z parametrem tag **--set:**

```azurecli
az vm update \
    --resource-group MyResourceGroup \
    --name MyTestVM \
    --set tags.myNewTagName1=myNewTagValue1 tags.myNewTagName2=myNewTagValue2
```

Aby usunąć znaczniki, można użyć parametru `azure vm update` **--remove** w poleceniu.

```azurecli
az vm update --resource-group MyResourceGroup --name MyTestVM --remove tags.myNewTagName1
```

Teraz, gdy zastosowaliśmy tagi do naszych zasobów interfejsu wiersza polecenia platformy Azure i portalu, przyjrzyjmy się szczegółom użycia, aby wyświetlić tagi w portalu rozliczeniowym.

[!INCLUDE [virtual-machines-common-tag-usage](../../../includes/virtual-machines-common-tag-usage.md)]

## <a name="next-steps"></a>Następne kroki
* Aby dowiedzieć się więcej o oznaczaniu zasobów platformy Azure, zobacz [Omówienie usługi Azure Resource Manager][Azure Resource Manager Overview] i [organizowanie zasobów platformy Azure przy użyciu tagów.][Using Tags to organize your Azure Resources]
* Aby zobaczyć, jak tagi mogą pomóc w zarządzaniu korzystaniem z zasobów platformy Azure, zobacz [Opis rachunku za korzystanie][Understanding your Azure Bill] z platformy Azure i uzyskaj wgląd w zużycie zasobów platformy Microsoft [Azure.][Gain insights into your Microsoft Azure resource consumption]

[Azure CLI environment]: ../../azure-resource-manager/xplat-cli-azure-resource-manager.md
[Azure Resource Manager Overview]: ../../azure-resource-manager/management/overview.md
[Using Tags to organize your Azure Resources]: ../../azure-resource-manager/management/tag-resources.md
[Understanding your Azure Bill]:../../cost-management-billing/understand/review-individual-bill.md
[Gain insights into your Microsoft Azure resource consumption]:../../cost-management-billing/manage/usage-rate-card-overview.md
