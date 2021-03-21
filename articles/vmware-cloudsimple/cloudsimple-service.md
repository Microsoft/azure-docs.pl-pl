---
title: Rozwiązanie VMware firmy Azure według CloudSimple — usługa
description: Dowiedz się więcej o usłudze CloudSimple z omówieniem. Tworzenie usługi umożliwia kupowanie węzłów, rezerwowanie węzłów i tworzenie chmur prywatnych.
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 8f32197eda4fc7632e883fd21dd6e1caa0dbd24b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97898746"
---
# <a name="cloudsimple-service-overview"></a>Przegląd usługi CloudSimple

Usługa CloudSimple umożliwia korzystanie z rozwiązań VMware platformy Azure przez CloudSimple.  Tworzenie usługi umożliwia kupowanie węzłów, rezerwowanie węzłów i tworzenie chmur prywatnych.  Usługę CloudSimple można utworzyć w każdym regionie świadczenia usługi Azure, w którym jest dostępna usługa CloudSimple. Usługa definiuje sieć brzegową rozwiązania Azure VMware przez CloudSimple. Sieć brzegowa obsługuje usługi, które obejmują sieci VPN, ExpressRoute i łączność internetową z chmurami prywatnymi.

## <a name="gateway-subnet"></a>Podsieć bramy

Podsieć bramy jest wymagana dla usługi CloudSimple i jest unikatowa dla regionu, w którym została utworzona. Podsieć bramy jest używana podczas tworzenia sieci brzegowej i wymaga bloku/28 CIDR.  Przestrzeń adresowa podsieci bramy musi być unikatowa. Nie może pokrywać się z żadną siecią, która komunikuje się ze środowiskiem CloudSimple. Sieci, które komunikują się z usługą CloudSimple, obejmują sieci lokalne i usługę Azure Virtual Network.  Nie można usunąć podsieci bramy po jej utworzeniu.  Podsieć bramy jest usuwana po usunięciu usługi.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak [utworzyć usługę CloudSimple na platformie Azure](quickstart-create-cloudsimple-service.md).
