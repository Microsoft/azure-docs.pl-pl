---
title: Samouczek — prywatne środowiska lokalne do chmury prywatnej
description: Dowiedz się, jak utworzyć ExpressRoute Global Reach komunikacji równorzędnej w chmurze prywatnej w rozwiązaniu VMware platformy Azure.
ms.topic: tutorial
ms.date: 01/27/2021
ms.openlocfilehash: 04be94849f1cd78ff915ca271845b283c8cfc64c
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100558814"
---
# <a name="tutorial-peer-on-premises-environments-to-a-private-cloud"></a>Samouczek: środowiska lokalne w chmurze do chmury prywatnej

ExpressRoute Global Reach nawiązuje połączenie środowiska lokalnego z chmurą prywatną rozwiązania Azure VMware. Połączenie z usługą ExpressRoute Global Reach jest nawiązywane między obwodem usługi ExpressRoute w chmurze prywatnej a istniejącym połączeniem ExpressRoute z lokalnymi środowiskami. 

Z tego samouczka dowiesz się, jak wykonywać następujące czynności:

> [!div class="checklist"]
> * Użyj Azure Portal, aby włączyć komunikację równorzędną z chmurą w chmurze ExpressRoute Global Reach.


## <a name="before-you-begin"></a>Zanim rozpoczniesz

Przed włączeniem łączności między dwoma obwodami usługi ExpressRoute przy użyciu usługi ExpressRoute Global Reach zapoznaj się z dokumentacją dotyczącą sposobu [włączania łączności w różnych subskrypcjach platformy Azure](../expressroute/expressroute-howto-set-global-reach-cli.md#enable-connectivity-between-expressroute-circuits-in-different-azure-subscriptions).  


## <a name="prerequisites"></a>Wymagania wstępne

- Oddzielny, działający obwód ExpressRoute używany do łączenia środowisk lokalnych z platformą Azure.
- Upewnij się, że wszystkie bramy, w tym usługa dostawcy ExpressRoute, obsługują 4-bajtowy numer systemu autonomicznego (ASN). Rozwiązanie VMware platformy Azure używa 4-bajtowego publicznego WPW do anonsowania tras.


## <a name="create-an-expressroute-authorization-key-in-the-on-premises-expressroute-circuit"></a>Tworzenie klucza autoryzacji ExpressRoute w lokalnym obwodzie ExpressRoute

1. W bloku **obwody usługi ExpressRoute** w obszarze Ustawienia wybierz pozycję **autoryzacje**.

2. Wprowadź nazwę klucza autoryzacji i wybierz pozycję **Zapisz**.

    :::image type="content" source="media/expressroute-global-reach/start-request-auth-key-on-premises-expressroute.png" alt-text="Wybierz pozycję autoryzacje i wprowadź nazwę klucza autoryzacji.":::
  
     Po utworzeniu nowy klucz zostanie wyświetlony na liście kluczy autoryzacji obwodu.
 
 4. Zanotuj klucz autoryzacji i identyfikator ExpressRoute. Będziesz ich używać w następnym kroku do kończenia komunikacji równorzędnej.
 
 
 ## <a name="peer-private-cloud-to-on-premises"></a>Równorzędna Chmura prywatna do lokalnego

1. W obszarze **zarządzanie chmurą prywatną w obszarze** Zarządzaj wybierz pozycję **łączność > ExpressRoute Global REACH > Dodaj**.

    :::image type="content" source="./media/expressroute-global-reach/expressroute-global-reach-tab.png" alt-text="Z menu wybierz pozycję łączność, ExpressRoute kartę Global Reach a następnie Dodaj.":::

2. Wprowadź identyfikator ExpressRoute i klucz autoryzacji utworzony w poprzedniej sekcji.

    :::image type="content" source="./media/expressroute-global-reach/on-premises-cloud-connections.png" alt-text="Wprowadź identyfikator ExpressRoute i klucz autoryzacji, a następnie wybierz pozycję Utwórz.":::

3. Wybierz przycisk **Utwórz**. Nowe połączenie zostanie wyświetlone na liście połączeń w chmurze lokalnej.  

>[!TIP]
>Możesz usunąć lub rozłączyć połączenie z listy, wybierając pozycję **więcej**.  
>
> :::image type="content" source="./media/expressroute-global-reach/on-premises-connection-disconnect.png" alt-text="Odłączanie lub usuwanie połączenia lokalnego":::


## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono sposób włączania komunikacji równorzędnej w chmurze Global Reach ExpressRoute na poziomie lokalnym i prywatnym. 

Przejdź do następnego samouczka, aby dowiedzieć się, jak wdrożyć i skonfigurować rozwiązanie VMware HCX dla chmury prywatnej rozwiązania Azure VMware.

> [!div class="nextstepaction"]
> [Wdrażanie i konfigurowanie usługi VMware HCX](tutorial-deploy-vmware-hcx.md)


<!-- LINKS - external-->

<!-- LINKS - internal -->
