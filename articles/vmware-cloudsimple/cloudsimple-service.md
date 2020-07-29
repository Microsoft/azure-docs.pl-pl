---
title: Rozwiązanie VMware firmy Azure według CloudSimple — usługa
description: Zawiera omówienie usługi i koncepcji CloudSimple.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: d128a248c2e6e1e2e35e3b633975ba081e77f028
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "77024961"
---
# <a name="cloudsimple-service-overview"></a>Przegląd usługi CloudSimple

Usługa CloudSimple umożliwia korzystanie z rozwiązań VMware platformy Azure przez CloudSimple.  Tworzenie usługi umożliwia kupowanie węzłów, rezerwowanie węzłów i tworzenie chmur prywatnych.  Usługę CloudSimple można utworzyć w każdym regionie świadczenia usługi Azure, w którym jest dostępna usługa CloudSimple. Usługa definiuje sieć brzegową rozwiązania Azure VMware przez CloudSimple. Sieć brzegowa obsługuje usługi, które obejmują sieci VPN, ExpressRoute i łączność internetową z chmurami prywatnymi.

## <a name="gateway-subnet"></a>Podsieć bramy

Podsieć bramy jest wymagana dla usługi CloudSimple i jest unikatowa dla regionu, w którym została utworzona. Podsieć bramy jest używana podczas tworzenia sieci brzegowej i wymaga bloku/28 CIDR.  Przestrzeń adresowa podsieci bramy musi być unikatowa. Nie może pokrywać się z żadną siecią, która komunikuje się ze środowiskiem CloudSimple. Sieci, które komunikują się z usługą CloudSimple, obejmują sieci lokalne i usługę Azure Virtual Network.  Nie można usunąć podsieci bramy po jej utworzeniu.  Podsieć bramy jest usuwana po usunięciu usługi.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak [utworzyć usługę CloudSimple na platformie Azure](quickstart-create-cloudsimple-service.md).
