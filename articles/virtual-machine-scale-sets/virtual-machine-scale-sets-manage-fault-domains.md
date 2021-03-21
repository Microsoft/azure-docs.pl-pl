---
title: Zarządzanie domenami błędów w zestawach skalowania maszyn wirtualnych platformy Azure
description: Dowiedz się, jak wybrać odpowiednią liczbę domenami błędów podczas tworzenia zestawu skalowania maszyn wirtualnych.
author: mimckitt
ms.author: mimckitt
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: availability
ms.date: 12/18/2018
ms.reviewer: jushiman
ms.custom: mimckitt, devx-track-azurecli
ms.openlocfilehash: 8c114d6260cf81bcc4fb256fc8a09947ab9ce1d8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102502488"
---
# <a name="choosing-the-right-number-of-fault-domains-for-virtual-machine-scale-set"></a>Choosing the right number of fault domains for virtual machine scale set (Wybieranie odpowiedniej liczby domen błędów dla zestawu skalowania maszyn wirtualnych)
Zestawy skalowania maszyn wirtualnych są tworzone z pięcioma domenami błędów domyślnie w regionach platformy Azure bez stref. W przypadku regionów obsługujących wdrożenie strefowe zestawów skalowania maszyn wirtualnych i wybrania tej opcji wartość domyślna liczby domen błędów wynosi 1 dla każdej z tych stref. Demon = 1 w tym przypadku oznacza, że wystąpienia maszyn wirtualnych należące do zestawu skalowania będą rozłożone w wielu stojakach na podstawie najlepszego wysiłku.

Można również rozważyć wyrównanie liczby domen błędów zestawu skalowania z liczbą domen błędów Managed Disks. To wyrównanie może pomóc w zapobieganiu utracie kworum, jeśli cała domena błędów Managed Disks ulegnie awarii. Licznik FD może być ustawiony na wartość mniejszą lub równą liczbie Managed Disks domen błędów dostępnych w poszczególnych regionach. Zapoznaj się z tym [dokumentem](../virtual-machines/availability.md) , aby dowiedzieć się więcej o liczbie Managed disks domen błędów według regionów.

## <a name="rest-api"></a>Interfejs API REST
Właściwość można ustawić na wartość `properties.platformFaultDomainCount` 1, 2 lub 3 (Domyślnie wartość 3, jeśli nie zostanie określona). Zapoznaj się z dokumentacją interfejsu API REST [tutaj](/rest/api/compute/virtualmachinescalesets/createorupdate).

## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure
Parametr można ustawić na wartość `--platform-fault-domain-count` 1, 2 lub 3 (Domyślnie wartość 3, jeśli nie zostanie określona). W [tym miejscu](/cli/azure/vmss#az-vmss-create)zapoznaj się z dokumentacją interfejsu wiersza polecenia platformy Azure.

```azurecli-interactive
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image UbuntuLTS \
  --upgrade-policy-mode automatic \
  --admin-username azureuser \
  --platform-fault-domain-count 3\
  --generate-ssh-keys
```

Utworzenie i skonfigurowanie wszystkich zasobów zestawu skalowania i maszyn wirtualnych trwa kilka minut.

## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o [funkcjach dostępności i nadmiarowości](../virtual-machines/availability.md) dla środowisk platformy Azure.
