---
title: Limity przydziału usługi i dostępność regionów
description: Limity przydziałów, ograniczenia i dostępność regionów dla usługi Azure Container Instances.
ms.topic: article
ms.date: 07/22/2020
ms.openlocfilehash: eaaa8e0b2d72aaea546a1bc351da40932c1deb14
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "87384836"
---
# <a name="quotas-and-limits-for-azure-container-instances"></a>Przydziały i limity dla Azure Container Instances

Wszystkich usług platformy Azure dotyczą określone limity i przydziały dla zasobów i funkcji. W tym artykule opisano domyślne przydziały i limity dla Azure Container Instances.

Dostępność zasobów obliczeniowych, pamięci i magazynu dla Azure Container Instances różni się w zależności od regionu i systemu operacyjnego. Aby uzyskać szczegółowe informacje, zobacz [dostępność zasobów dla Azure Container Instances](container-instances-region-availability.md).

Użyj interfejsu API [użycia listy](/rest/api/container-instances/location/listusage) , aby przejrzeć bieżące użycie przydziału w regionie dla subskrypcji.

## <a name="service-quotas-and-limits"></a>Limity i przydziały dotyczące usługi

[!INCLUDE [container-instances-limits](../../includes/container-instances-limits.md)]

## <a name="next-steps"></a>Następne kroki

Niektóre domyślne limity i przydziały mogą zostać zwiększone. Aby zażądać zwiększenia dotyczącego co najmniej jednego zasobu, dla którego takie zwiększenie jest obsługiwane, prześlij [żądanie pomocy technicznej platformy Azure][azure-support] (wybierz wartość „Limit przydziału” dla pozycji **Typ problemu**).

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
