---
title: Łączenie Azure VMware Solution ze środowiskiem lokalnym
description: Dowiedz się, jak połączyć Azure VMware Solution ze środowiskiem lokalnym.
ms.topic: tutorial
ms.date: 04/19/2021
ms.openlocfilehash: 392d82a9aca9b60b394a5d5f4a7e6b0111438e59
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107725623"
---
# <a name="connect-azure-vmware-solution-to-your-on-premises-environment"></a>Łączenie Azure VMware Solution ze środowiskiem lokalnym

W tym artykule będziesz nadal [](production-ready-deployment-steps.md) korzystać z informacji zebranych podczas planowania połączenia Azure VMware Solution ze środowiskiem lokalnym.

Przed rozpoczęciem musisz mieć obwód usługi ExpressRoute ze środowiska lokalnego do platformy Azure.


>[!NOTE]
> Możesz nawiązać połączenie za pośrednictwem sieci VPN, ale jest to poza zakresem tego dokumentu Szybkiego startu.

## <a name="establish-an-expressroute-global-reach-connection"></a>Nawiązywanie połączenia Global Reach ExpressRoute

Aby ustanowić łączność lokalną z chmurą prywatną Azure VMware Solution przy użyciu usługi ExpressRoute Global Reach, postępuj zgodnie z samouczkiem Peer [on-premises environments to a private cloud](tutorial-expressroute-global-reach-private-cloud.md) (Komunikacja równorzędna środowisk lokalnych z chmurą prywatną).

Ten samouczek powoduje połączenie, jak pokazano na diagramie.

:::image type="content" source="media/pre-deployment/azure-vmware-solution-on-premises-diagram.png" alt-text="Diagram Global Reach sieci lokalnej usługi ExpressRoute." lightbox="media/pre-deployment/azure-vmware-solution-on-premises-diagram.png" border="false":::

## <a name="verify-on-premises-network-connectivity"></a>Weryfikowanie łączności z siecią lokalną

Na lokalnym routerze  brzegowym powinno być teraz widać, gdzie expressRoute łączy segmenty sieci NSX-T i segmenty Azure VMware Solution zarządzania.

>[!IMPORTANT]
>Wszyscy mają inne środowisko, a niektóre muszą zezwolić na propagację tych tras z powrotem do sieci lokalnej.  

Niektóre środowiska mają zapory chroniące obwód usługi ExpressRoute.  Jeśli nie ma żadnych zapór i nie występuje żadne przycinanie tras, wyślij polecenie ping do serwera Azure VMware Solution vCenter lub maszyny wirtualnej w [segmencie NSX-T](deploy-azure-vmware-solution.md#add-a-vm-on-the-nsx-t-network-segment) ze środowiska lokalnego. Ponadto z maszyny wirtualnej w segmencie NSX-T można uzyskać zasoby w środowisku lokalnym.

## <a name="next-steps"></a>Następne kroki

Przejdź do następnej sekcji, aby wdrożyć i skonfigurować oprogramowanie VMware HCX

> [!div class="nextstepaction"]
> [Wdrażanie i konfigurowanie usługi VMware HCX](tutorial-deploy-vmware-hcx.md)