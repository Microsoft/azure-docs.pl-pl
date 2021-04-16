---
title: Jak otagować maszynę wirtualną platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure
description: Dowiedz się więcej o tagowaniu maszyny wirtualnej przy użyciu interfejsu wiersza polecenia platformy Azure.
author: cynthn
ms.service: virtual-machines
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 11/11/2020
ms.author: cynthn
ms.custom: devx-track-azurecli
ms.openlocfilehash: 20bb4ab622a01646bcc61d0f691c514a25a06edc
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107502610"
---
# <a name="how-to-tag-a-vm-using-the-azure-cli"></a>Jak otagować maszynę wirtualną przy użyciu interfejsu wiersza polecenia platformy Azure

W tym artykule opisano sposób tagu maszyny wirtualnej przy użyciu interfejsu wiersza polecenia platformy Azure. Tagi to pary klucz/wartość zdefiniowane przez użytkownika, które można umieścić bezpośrednio w zasobie lub grupie zasobów. Platforma Azure obsługuje obecnie maksymalnie 50 tagów na zasób i grupę zasobów. Tagi mogą być umieszczane w zasobie w czasie tworzenia lub dodawane do istniejącego zasobu. Maszynę wirtualną można również otagować przy użyciu programu Azure [PowerShell.](tag-powershell.md)


Wszystkie właściwości danej maszyny wirtualnej, w tym tagi, można wyświetlić przy `az vm show` użyciu .

```azurecli-interactive
az vm show --resource-group myResourceGroup --name myVM --query tags
```

Aby dodać nowy tag maszyny wirtualnej za pomocą interfejsu wiersza polecenia platformy Azure, możesz użyć `azure vm update` polecenia wraz z parametrem tagu `--set` :

```azurecli-interactive
az vm update \
    --resource-group myResourceGroup \
    --name myVM \
    --set tags.myNewTagName1=myNewTagValue1 tags.myNewTagName2=myNewTagValue2
```

Aby usunąć tagi, możesz użyć `--remove` parametru w `azure vm update` poleceniu .

```azurecli-interactive
az vm update \
   --resource-group myResourceGroup \
   --name myVM \
   --remove tags.myNewTagName1
```

Po zastosowaniu tagów do naszych zasobów interfejsu wiersza polecenia platformy Azure i portalu przyjrzyjmy się szczegółom użycia, aby wyświetlić tagi w portalu rozliczeń.

### <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się więcej na temat tagowania zasobów platformy Azure, zobacz [Azure Resource Manager Overview](../azure-resource-manager/management/overview.md) (Omówienie) i Using Tags to organize your Azure Resources (Używanie tagów [do organizowania zasobów platformy Azure).](../azure-resource-manager/management/tag-resources.md)
- Aby zobaczyć, jak tagi mogą pomóc w zarządzaniu użyciem zasobów platformy Azure, zobacz [Understanding your Azure Bill (Opis rachunku za korzystanie z platformy Azure).](../cost-management-billing/understand/review-individual-bill.md)
