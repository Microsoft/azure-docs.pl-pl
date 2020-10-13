---
title: Jak oznaczyć maszynę wirtualną platformy Azure przy użyciu interfejsu wiersza polecenia
description: Dowiedz się więcej na temat tagowania maszyny wirtualnej przy użyciu interfejsu wiersza polecenia platformy Azure.
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 02/28/2017
ms.author: cynthn
ms.custom: devx-track-azurecli
ms.openlocfilehash: 1a417e7cff4c7afb601861ddfe09eec171f0cf15
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89320617"
---
# <a name="how-to-tag-a-linux-virtual-machine-in-azure"></a>Jak oznaczyć maszynę wirtualną z systemem Linux na platformie Azure
W tym artykule opisano różne sposoby tagowania maszyny wirtualnej z systemem Linux na platformie Azure za pomocą modelu wdrażania Menedżer zasobów. Tagi to zdefiniowane przez użytkownika pary klucz/wartość, które mogą być umieszczone bezpośrednio w ramach zasobu lub grupy zasobów. Platforma Azure obsługuje obecnie do 50 tagów na zasób i grupę zasobów. Tagi mogą być umieszczane na zasobie w momencie tworzenia lub dodawane do istniejącego zasobu. Należy pamiętać, że Tagi są obsługiwane tylko dla zasobów utworzonych za pośrednictwem modelu wdrażania Menedżer zasobów.

[!INCLUDE [virtual-machines-common-tag](../../../includes/virtual-machines-common-tag.md)]

## <a name="tagging-with-azure-cli"></a>Tagowanie przy użyciu interfejsu wiersza polecenia platformy Azure

Aby rozpocząć, musisz zainstalować najnowszy [interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) i zalogować się na konto platformy Azure za pomocą polecenia [AZ login](/cli/azure/reference-index#az-login).

Można wyświetlić wszystkie właściwości danej maszyny wirtualnej, w tym Tagi, za pomocą tego polecenia:

```azurecli
az vm show --resource-group MyResourceGroup --name MyTestVM
```

Aby dodać nowy tag maszyny wirtualnej za pomocą interfejsu wiersza polecenia platformy Azure, można użyć `azure vm update` polecenia wraz z parametrem tag **--Set**:

```azurecli
az vm update \
    --resource-group MyResourceGroup \
    --name MyTestVM \
    --set tags.myNewTagName1=myNewTagValue1 tags.myNewTagName2=myNewTagValue2
```

Aby usunąć Tagi, można użyć parametru **--Remove** w `azure vm update` poleceniu.

```azurecli
az vm update --resource-group MyResourceGroup --name MyTestVM --remove tags.myNewTagName1
```

Teraz, po zastosowaniu tagów do naszych zasobów interfejsu wiersza polecenia platformy Azure i portalu, zapoznaj się z informacjami na temat użycia, aby zobaczyć Tagi w portalu rozliczeń.

[!INCLUDE [virtual-machines-common-tag-usage](../../../includes/virtual-machines-common-tag-usage.md)]

## <a name="next-steps"></a>Następne kroki
* Aby dowiedzieć się więcej o znakowaniu zasobów platformy Azure, zobacz [Azure Resource Manager omówienie][Azure Resource Manager Overview] i [Używanie tagów do organizowania zasobów platformy Azure][Using Tags to organize your Azure Resources].
* Aby dowiedzieć się, jak tagi mogą ułatwić zarządzanie użyciem zasobów platformy Azure, zobacz [Opis rachunku na korzystanie z platformy Azure][Understanding your Azure Bill] i [Uzyskiwanie szczegółowych informacji o zużyciu zasobów Microsoft Azure][Gain insights into your Microsoft Azure resource consumption].

[Azure CLI environment]: ../../azure-resource-manager/management/manage-resources-cli.md
[Azure Resource Manager Overview]: ../../azure-resource-manager/management/overview.md
[Using Tags to organize your Azure Resources]: ../../azure-resource-manager/management/tag-resources.md
[Understanding your Azure Bill]: ../../cost-management-billing/understand/review-individual-bill.md
[Gain insights into your Microsoft Azure resource consumption]: ../../cost-management-billing/manage/usage-rate-card-overview.md
