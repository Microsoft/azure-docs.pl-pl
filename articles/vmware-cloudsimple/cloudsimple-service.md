---
title: Rozwiązanie VMware firmy Azure według CloudSimple — usługa
description: Dowiedz się więcej o usłudze CloudSimple z omówieniem. Tworzenie usługi umożliwia kupowanie węzłów, rezerwowanie węzłów i tworzenie chmur prywatnych.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: ec77209c0995bf5aec0957c8d4d2269f27d743bc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88142132"
---
# <a name="cloudsimple-service-overview"></a>Przegląd usługi CloudSimple

Usługa CloudSimple umożliwia korzystanie z rozwiązań VMware platformy Azure przez CloudSimple.  Tworzenie usługi umożliwia kupowanie węzłów, rezerwowanie węzłów i tworzenie chmur prywatnych.  Usługę CloudSimple można utworzyć w każdym regionie świadczenia usługi Azure, w którym jest dostępna usługa CloudSimple. Usługa definiuje sieć brzegową rozwiązania Azure VMware przez CloudSimple. Sieć brzegowa obsługuje usługi, które obejmują sieci VPN, ExpressRoute i łączność internetową z chmurami prywatnymi.

## <a name="gateway-subnet"></a>Podsieć bramy

Podsieć bramy jest wymagana dla usługi CloudSimple i jest unikatowa dla regionu, w którym została utworzona. Podsieć bramy jest używana podczas tworzenia sieci brzegowej i wymaga bloku/28 CIDR.  Przestrzeń adresowa podsieci bramy musi być unikatowa. Nie może pokrywać się z żadną siecią, która komunikuje się ze środowiskiem CloudSimple. Sieci, które komunikują się z usługą CloudSimple, obejmują sieci lokalne i usługę Azure Virtual Network.  Nie można usunąć podsieci bramy po jej utworzeniu.  Podsieć bramy jest usuwana po usunięciu usługi.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak [utworzyć usługę CloudSimple na platformie Azure](quickstart-create-cloudsimple-service.md).
