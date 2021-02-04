---
title: Omówienie grup zabezpieczeń aplikacji platformy Azure
titlesuffix: Azure Virtual Network
description: Dowiedz się więcej o korzystaniu z grup zabezpieczeń aplikacji.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/27/2020
ms.author: kumud
ms.reviewer: kumud
ms.openlocfilehash: 4d8ffe8451b2b2a08ab30761eaf3a928b5e117b3
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/04/2021
ms.locfileid: "99537632"
---
# <a name="application-security-groups"></a>Grupy zabezpieczeń aplikacji

Grupy zabezpieczeń aplikacji umożliwiają skonfigurowanie zabezpieczeń sieci jako naturalnego rozszerzenia struktury aplikacji, co pozwala na grupowanie maszyn wirtualnych i definiowanie zasad zabezpieczeń sieci na podstawie tych grup. Możesz ponownie używać zasad zabezpieczeń na dużą skalę bez ręcznej obsługi jawnych adresów IP. Platforma obsługuje złożoność jawnych adresów IP i wiele zestawów reguł, co pozwala skupić się na logice biznesowej. Aby lepiej zrozumieć grupy zabezpieczeń aplikacji, rozważmy następujący przykład:

![Grupy zabezpieczeń aplikacji](./media/security-groups/application-security-groups.png)

Na poprzedniej ilustracji interfejsy sieciowe *NIC1* i *NIC2* są elementami członkowskimi grupy zabezpieczeń aplikacji *AsgWeb*. Interfejs sieciowy *NIC3* jest elementem członkowskim grupy zabezpieczeń aplikacji *AsgLogic*. Interfejs sieciowy *NIC4* jest elementem członkowskim grupy zabezpieczeń aplikacji *AsgDb*. Chociaż każdy interfejs sieciowy w tym przykładzie jest członkiem tylko jednej sieciowej grupy zabezpieczeń, interfejs sieciowy może być członkiem wielu grup zabezpieczeń aplikacji, do których [obowiązują limity platformy Azure](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits). Żaden z interfejsów sieciowych nie ma skojarzonej grupy zabezpieczeń sieci. *NSG1* jest skojarzony z obiema podsieciami i zawiera następujące reguły:

## <a name="allow-http-inbound-internet"></a>Allow-HTTP-Inbound-Internet

Ta reguła jest potrzebna w celu zezwolenia na ruch z Internetu do serwerów internetowych. Ponieważ ruch przychodzący z Internetu jest blokowany przez domyślną regułę zabezpieczeń **DenyAllInbound**, dodatkowa reguła nie jest potrzebna w przypadku grup zabezpieczeń aplikacji *AsgLogic* i *AsgDb*.

|Priorytet|Element źródłowy|Porty źródłowe| Element docelowy | Porty docelowe | Protokół | Access |
|---|---|---|---|---|---|---|
| 100 | Internet | * | AsgWeb | 80 | TCP | Zezwalaj |

## <a name="deny-database-all"></a>Deny-Database-All

Ponieważ domyślna reguła zabezpieczeń **AllowVNetInBound** zezwala na całą komunikację między zasobami w tej samej sieci wirtualnej, ta zasada jest potrzebna w celu blokowania ruchu ze wszystkich zasobów.

|Priorytet|Element źródłowy|Porty źródłowe| Element docelowy | Porty docelowe | Protokół | Access |
|---|---|---|---|---|---|---|
| 120 | * | * | AsgDb | 1433 | Dowolne | Zablokuj |

## <a name="allow-database-businesslogic"></a>Allow-Database-BusinessLogic

Ta reguła zezwala na ruch z grupy zabezpieczeń aplikacji *AsgLogic* do grupy zabezpieczeń aplikacji *AsgDb*. Priorytet tej reguły jest wyższy niż priorytet reguły *Deny-Database-All*. W rezultacie ta reguła jest przetwarzana przed regułą *Deny-Database-All*, a więc ruch z grupy zabezpieczeń aplikacji *AsgLogic* jest dozwolony, natomiast cały pozostały ruch jest blokowany.

|Priorytet|Element źródłowy|Porty źródłowe| Element docelowy | Porty docelowe | Protokół | Access |
|---|---|---|---|---|---|---|
| 110 | AsgLogic | * | AsgDb | 1433 | TCP | Zezwalaj |

Reguły określające grupę zabezpieczeń aplikacji jako źródło lub obiekt docelowy są stosowane tylko do interfejsów sieciowych, które są elementami członkowskimi grupy zabezpieczeń aplikacji. Jeśli interfejs sieciowy nie jest elementem członkowskim grupy zabezpieczeń aplikacji, reguła nie jest stosowana do tego interfejsu sieciowego, mimo że grupa zabezpieczeń sieci jest skojarzona z podsiecią.

Grupy zabezpieczeń aplikacji mają następujące ograniczenia:

-    Istnieją limity liczby grup zabezpieczeń aplikacji, które możesz mieć w ramach subskrypcji, a także inne ograniczenia dotyczące grup zabezpieczeń aplikacji. Aby uzyskać więcej informacji, zobacz [Azure limits (Ograniczenia platformy Azure)](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).
- Jako źródło i obiekt docelowy reguły zabezpieczeń można określić pojedynczą grupę zabezpieczeń aplikacji. Jako źródła lub obiektu docelowego nie można określić kilku grup zabezpieczeń aplikacji.
- Wszystkie interfejsy sieciowe przypisane do grupy zabezpieczeń aplikacji muszą istnieć w tej samej sieci wirtualnej, co pierwszy interfejs sieciowy przypisany do danej grupy zabezpieczeń aplikacji. Na przykład jeśli pierwszy interfejs sieciowy przypisany do grupy zabezpieczeń aplikacji o nazwie *AsgWeb* istnieje w sieci wirtualnej o nazwie *VNet1*, wszystkie kolejne interfejsy sieciowe przypisane do grupy *AsgWeb* muszą istnieć w sieci *VNet1*. Interfejsy sieciowe z różnych sieci wirtualnych nie mogą być dodawane do tej samej grupy zabezpieczeń aplikacji.
- Jeśli określisz grupy zabezpieczeń aplikacji jako źródło i obiekt docelowy w regule zabezpieczeń, interfejsy sieciowe w obu grupach zabezpieczeń aplikacji muszą istnieć w tej samej sieci wirtualnej. Na przykład jeśli grupa *AsgLogic* zawiera interfejsy sieciowe z sieci *VNet1*, a grupa *AsgDb* zawiera interfejsy sieciowe z sieci *VNet2*, nie można przypisać grupy *AsgLogic* jako źródła i grupy *AsgDb* jako obiektu docelowego w regule. Wszystkie interfejsy sieciowe dla źródłowych i docelowych grup zabezpieczeń aplikacji muszą istnieć w tej samej sieci wirtualnej.

> [!TIP]
> Jeśli to możliwe, aby zminimalizować liczbę niezbędnych reguł zabezpieczeń i konieczność zmiany reguł, zaplanuj potrzebne grupy zabezpieczeń aplikacji i utwórz reguły przy użyciu tagów usług lub grup zabezpieczeń aplikacji, a nie pojedynczych adresów IP lub zakresów adresów IP.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak [utworzyć sieciową grupę zabezpieczeń](tutorial-filter-network-traffic.md).
