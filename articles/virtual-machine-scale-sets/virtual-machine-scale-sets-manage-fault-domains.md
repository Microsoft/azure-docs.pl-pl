---
title: Zarządzanie domenami błędów w zestawach skalowania maszyn wirtualnych platformy Azure
description: Dowiedz się, jak wybrać odpowiednią liczbę identyfikatorów FD podczas tworzenia zestawu skalowania maszyn wirtualnych.
author: mimckitt
ms.author: mimckitt
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: availability
ms.date: 12/18/2018
ms.reviewer: jushiman
ms.custom: mimckitt, devx-track-azurecli
ms.openlocfilehash: 10d45662f84a354ee4b261c2e7255a57aa81ad0f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107774489"
---
# <a name="choosing-the-right-number-of-fault-domains-for-virtual-machine-scale-set"></a>Choosing the right number of fault domains for virtual machine scale set (Wybieranie odpowiedniej liczby domen błędów dla zestawu skalowania maszyn wirtualnych)
Zestawy skalowania maszyn wirtualnych są domyślnie tworzone z pięcioma domenami błędów w regionach świadczenia usługi Azure bez stref. W regionach, które obsługują strefowe wdrażanie zestawów skalowania maszyn wirtualnych i ta opcja jest zaznaczona, domyślna wartość liczby domen błędów wynosi 1 dla każdej strefy. FD=1 w tym przypadku oznacza, że wystąpienia maszyn wirtualnych należące do zestawu skalowania będą rozłożyć się na wiele stojaków przy najlepszym narzucie pracy.

Można również rozważyć dopasowanie liczby domen błędów zestawu skalowania do liczby domen błędów Dyski zarządzane błędów. Takie wyrównanie może pomóc zapobiec utracie kworum, jeśli cała Dyski zarządzane domeny błędów nie działa. Liczbę błędów FD można ustawić na mniejszą lub równą liczbie domen błędów Dyski zarządzane dostępnych w każdym z regionów. Zapoznaj się z [tym dokumentem,](../virtual-machines/availability.md) aby dowiedzieć się więcej o liczbie Dyski zarządzane błędów według regionów.

## <a name="rest-api"></a>Interfejs API REST
Właściwość można ustawić na `properties.platformFaultDomainCount` 1, 2 lub 3 (wartość domyślna to 3, jeśli nie zostanie określona). Zapoznaj się z dokumentacją interfejsu API REST [tutaj.](/rest/api/compute/virtualmachinescalesets/createorupdate)

## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure
Możesz ustawić parametr na `--platform-fault-domain-count` 1, 2 lub 3 (wartość domyślna to 3, jeśli nie zostanie określona). Zapoznaj się z dokumentacją interfejsu wiersza polecenia platformy Azure [tutaj.](/cli/azure/vmss#az_vmss_create)

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
- Dowiedz się więcej o [funkcjach dostępności i nadmiarowości dla](../virtual-machines/availability.md) środowisk platformy Azure.
