---
title: Łączenie rozwiązania VMware z platformą Azure z środowiskiem lokalnym
description: Dowiedz się, jak połączyć rozwiązanie VMware firmy Azure ze środowiskiem lokalnym.
ms.topic: tutorial
ms.date: 10/02/2020
ms.openlocfilehash: 2a0cb641df00f3e580e87e38aff382d8e8101fc7
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/30/2020
ms.locfileid: "91583340"
---
# <a name="connect-azure-vmware-solution-to-your-on-premises-environment"></a>Łączenie rozwiązania VMware z platformą Azure z środowiskiem lokalnym

W tym artykule będziesz nadal korzystać z [informacji zebranych podczas planowania](production-ready-deployment-steps.md) w celu połączenia rozwiązań VMware platformy Azure z środowiskiem lokalnym.

Przed rozpoczęciem należy wykonać dwa wymagania wstępne dotyczące łączenia rozwiązania Azure VMware z lokalnym środowiskiem:

- Obwód usługi ExpressRoute z środowiska lokalnego na platformę Azure.
- /29 nienakładający się blok adresów sieciowych dla komunikacji równorzędnej ExpressRoute Global Reach, który został zdefiniowany jako część [fazy planowania](production-ready-deployment-steps.md).

>[!NOTE]
> Możesz nawiązać połączenie za pośrednictwem sieci VPN, ale jest to poza zakresem tego dokumentu szybkiego startu.

## <a name="establish-an-expressroute-global-reach-connection"></a>Nawiązywanie połączenia z usługą ExpressRoute Global Reach

Aby nawiązać połączenie lokalne z chmurą prywatną rozwiązania Azure VMware za pomocą ExpressRoute Global Reach, postępuj zgodnie ze [środowiskami lokalnymi, aby](tutorial-expressroute-global-reach-private-cloud.md) skorzystać z samouczka chmury prywatnej.



## <a name="verify-on-premises-network-connectivity"></a>Weryfikowanie łączności z siecią lokalną

Należy teraz zobaczyć **na lokalnym routerze brzegowym** , gdzie ExpressRoute łączy segmenty sieci NSX-T i segmenty zarządzania rozwiązaniami VMware platformy Azure.

>[!NOTE]
>Każdy ma inne środowisko, a niektóre muszą zezwalać na propagację tych tras z powrotem do sieci lokalnej.  

Niektóre środowiska będą mieć zapory chroniące obwód ExpressRoute.  Jeśli nie ma żadnych zapór i nie jest przeprowadzana oczyszczanie trasy, można wysłać polecenie ping do serwera vCenter rozwiązania Azure VMware lub [maszyny wirtualnej](deploy-azure-vmware-solution.md#add-a-vm-on-the-nsx-t-network-segment) w segmencie NSX-T ze środowiska lokalnego.

Ponadto z poziomu maszyny wirtualnej w segmencie NSX-T można uzyskać dostęp do zasobów w środowisku lokalnym.

## <a name="next-steps"></a>Następne kroki

Przejdź do następnej sekcji, aby wdrożyć i skonfigurować oprogramowanie VMware HCX

> [!div class="nextstepaction"]
> [Wdrażanie i Konfigurowanie programu VMware HCX](tutorial-deploy-vmware-hcx.md)