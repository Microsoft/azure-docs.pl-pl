---
title: Przenoszenie zasobów sieciowych platformy Azure do nowej subskrypcji lub grupy zasobów
description: Użyj Azure Resource Manager, aby przenieść sieci wirtualne i inne zasoby sieciowe do nowej grupy zasobów lub subskrypcji.
ms.topic: conceptual
ms.date: 10/16/2019
ms.openlocfilehash: b7aaf01b696b13136a0f4077f315b137c8917906
ms.sourcegitcommit: 9f4510cb67e566d8dad9a7908fd8b58ade9da3b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2021
ms.locfileid: "106120133"
---
# <a name="move-guidance-for-networking-resources"></a>Wskazówki dotyczące przenoszenia zasobów sieciowych

W tym artykule opisano sposób przenoszenia sieci wirtualnych i innych zasobów sieciowych w określonych scenariuszach.

## <a name="dependent-resources"></a>Zasoby zależne

> [!NOTE]
> Należy pamiętać, że bramy sieci VPN skojarzone z publicznymi adresami IP nie mogą być przenoszone między grupami zasobów lub subskrypcjami.

Podczas przenoszenia zasobu należy również przenieść jego zasoby zależne (np. publiczne adresy IP, bramy sieci wirtualnej, wszystkie powiązane zasoby połączenia). Bramy sieci lokalnej mogą znajdować się w innej grupie zasobów.

Aby przenieść maszynę wirtualną z kartą sieciową do nowej subskrypcji, musisz przenieść wszystkie zasoby zależne. Przenieś sieć wirtualną dla karty interfejsu sieciowego, wszystkie inne karty interfejsu sieciowego dla sieci wirtualnej i bramy sieci VPN.

Aby uzyskać więcej informacji, zobacz [scenariusz przenoszenia między subskrypcjami](../move-resource-group-and-subscription.md#scenario-for-move-across-subscriptions).

## <a name="peered-virtual-network"></a>Równorzędna Sieć wirtualna

Aby przenieść równorzędną sieć wirtualną, należy najpierw wyłączyć komunikację równorzędną sieci wirtualnej. Po wyłączeniu można przenieść sieć wirtualną. Po przeniesieniu ponownie włącz komunikację równorzędną sieci wirtualnej.

## <a name="subnet-links"></a>Linki podsieci

Nie można przenieść sieci wirtualnej do innej subskrypcji, jeśli sieć wirtualna zawiera podsieć z łączami nawigacji zasobów. Na przykład jeśli pamięć podręczna platformy Azure dla zasobu Redis jest wdrożona w podsieci, ta podsieć ma link nawigacji do zasobów.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać polecenia przenoszenia zasobów, zobacz [przenoszenie zasobów do nowej grupy zasobów lub subskrypcji](../move-resource-group-and-subscription.md).
