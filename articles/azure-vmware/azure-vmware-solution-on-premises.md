---
title: Łączenie rozwiązania VMware z platformą Azure z środowiskiem lokalnym
description: Dowiedz się, jak połączyć rozwiązanie VMware firmy Azure ze środowiskiem lokalnym.
ms.topic: tutorial
ms.date: 12/28/2020
ms.openlocfilehash: 753835b0206d8bbabe42b057fa40a2d6c4c8c414
ms.sourcegitcommit: 31d242b611a2887e0af1fc501a7d808c933a6bf6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/29/2020
ms.locfileid: "97809687"
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

>[!IMPORTANT]
>Każdy ma inne środowisko, a niektóre muszą zezwalać na propagację tych tras z powrotem do sieci lokalnej.  

Niektóre środowiska mają zapory chroniące obwód ExpressRoute.  W przypadku braku zapór i braku oczyszczania trasy należy wysłać polecenie ping do serwera vCenter rozwiązania Azure VMware lub [maszyny wirtualnej w segmencie NSX-T](deploy-azure-vmware-solution.md#add-a-vm-on-the-nsx-t-network-segment) ze środowiska lokalnego. Ponadto z poziomu maszyny wirtualnej w segmencie NSX-T można uzyskać dostęp do zasobów w środowisku lokalnym.

## <a name="next-steps"></a>Następne kroki

Przejdź do następnej sekcji, aby wdrożyć i skonfigurować oprogramowanie VMware HCX

> [!div class="nextstepaction"]
> [Wdrażanie i konfigurowanie usługi VMware HCX](tutorial-deploy-vmware-hcx.md)