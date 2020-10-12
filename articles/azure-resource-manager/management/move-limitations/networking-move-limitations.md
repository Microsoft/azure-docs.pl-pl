---
title: Przenoszenie zasobów sieciowych platformy Azure do nowej subskrypcji lub grupy zasobów
description: Użyj Azure Resource Manager, aby przenieść sieci wirtualne i inne zasoby sieciowe do nowej grupy zasobów lub subskrypcji.
ms.topic: conceptual
ms.date: 10/16/2019
ms.openlocfilehash: 0cd6887d3489f2ffede0f5e3d63533a33a6ccc04
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "75485236"
---
# <a name="move-guidance-for-networking-resources"></a>Wskazówki dotyczące przenoszenia zasobów sieciowych

W tym artykule opisano sposób przenoszenia sieci wirtualnych i innych zasobów sieciowych w określonych scenariuszach.

## <a name="dependent-resources"></a>Zasoby zależne

Podczas przenoszenia sieci wirtualnej należy również przenieść jej zasoby zależne. W przypadku bram sieci VPN należy przenieść adresy IP, bramy sieci wirtualnej i wszystkie powiązane zasoby połączenia. Bramy sieci lokalnej mogą znajdować się w innej grupie zasobów.

Aby przenieść maszynę wirtualną z kartą sieciową do nowej subskrypcji, musisz przenieść wszystkie zasoby zależne. Przenieś sieć wirtualną dla karty interfejsu sieciowego, wszystkie inne karty interfejsu sieciowego dla sieci wirtualnej i bramy sieci VPN.

Aby uzyskać więcej informacji, zobacz [scenariusz przenoszenia między subskrypcjami](../move-resource-group-and-subscription.md#scenario-for-move-across-subscriptions).

## <a name="peered-virtual-network"></a>Równorzędna Sieć wirtualna

Aby przenieść równorzędną sieć wirtualną, należy najpierw wyłączyć komunikację równorzędną sieci wirtualnej. Po wyłączeniu można przenieść sieć wirtualną. Po przeniesieniu ponownie włącz komunikację równorzędną sieci wirtualnej.

## <a name="subnet-links"></a>Linki podsieci

Nie można przenieść sieci wirtualnej do innej subskrypcji, jeśli sieć wirtualna zawiera podsieć z łączami nawigacji zasobów. Na przykład jeśli pamięć podręczna platformy Azure dla zasobu Redis jest wdrożona w podsieci, ta podsieć ma link nawigacji do zasobów.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać polecenia przenoszenia zasobów, zobacz [przenoszenie zasobów do nowej grupy zasobów lub subskrypcji](../move-resource-group-and-subscription.md).
