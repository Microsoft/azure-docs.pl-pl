---
title: Samouczek — prywatne środowiska lokalne do chmury prywatnej
description: Dowiedz się, jak utworzyć ExpressRoute Global Reach komunikacji równorzędnej w chmurze prywatnej w rozwiązaniu VMware platformy Azure.
ms.topic: tutorial
ms.date: 09/21/2020
ms.openlocfilehash: 9de6cbe177ac8d2ca4957e80c7ca1072a0d7985e
ms.sourcegitcommit: a2d8acc1b0bf4fba90bfed9241b299dc35753ee6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/12/2020
ms.locfileid: "91948310"
---
# <a name="tutorial-peer-on-premises-environments-to-a-private-cloud"></a>Samouczek: środowiska lokalne w chmurze do chmury prywatnej

ExpressRoute Global Reach nawiązuje połączenie środowiska lokalnego z chmurą prywatną rozwiązania Azure VMware. Połączenie z usługą ExpressRoute Global Reach jest nawiązywane między obwodem usługi ExpressRoute w chmurze prywatnej a istniejącym połączeniem ExpressRoute z lokalnymi środowiskami. 

Obwód usługi ExpressRoute, który jest używany podczas [konfigurowania sieci Azure-to-Private w chmurze](tutorial-configure-networking.md) , wymaga tworzenia i używania kluczy autoryzacji podczas komunikacji równorzędnej z bramami ExpressRoute lub z innymi obwodami ExpressRoute przy użyciu Global REACH. Jeden klucz autoryzacji został już użyty z obwodu usługi ExpressRoute, a w tym samouczku utworzysz drugi element do komunikacji równorzędnej z lokalnym obwodem usługi ExpressRoute.

Z tego samouczka dowiesz się, jak wykonywać następujące czynności:

> [!div class="checklist"]
> * Tworzenie drugiego klucza autoryzacji _obwodu 2_— obwodu usługi ExpressRoute w chmurze prywatnej
> * Użyj [Azure Portal](#azure-portal-method) lub [interfejsu wiersza polecenia platformy Azure w metodzie Cloud Shell](#azure-cli-in-a-cloud-shell-method) w subskrypcji _obwodu 1_ , aby włączyć komunikację równorzędną w chmurze Global REACH ExpressRoute


## <a name="before-you-begin"></a>Przed rozpoczęciem

Przed włączeniem łączności między dwoma obwodami usługi ExpressRoute przy użyciu usługi ExpressRoute Global Reach zapoznaj się z dokumentacją dotyczącą sposobu [włączania łączności w różnych subskrypcjach platformy Azure](../expressroute/expressroute-howto-set-global-reach-cli.md#enable-connectivity-between-expressroute-circuits-in-different-azure-subscriptions).  


## <a name="prerequisites"></a>Wymagania wstępne

1. Nawiązano połączenie z i z chmury prywatnej rozwiązań VMware platformy Azure z obwodem usługi ExpressRoute za pomocą komunikacji równorzędnej z bramą ExpressRoute w sieci wirtualnej platformy Azure — jest to _obwód 2_ z procedur komunikacji równorzędnej.  
1. Oddzielny, działający obwód ExpressRoute używany do łączenia środowisk lokalnych z platformą Azure — który jest _obwodem 1_ z perspektywy procedur komunikacji równorzędnej.
1. /29 nienakładający się [blok adresów sieciowych](../expressroute/expressroute-routing.md#ip-addresses-used-for-peerings) dla komunikacji równorzędnej ExpressRoute Global REACH.

> [!TIP]
> W kontekście tych wymagań wstępnych lokalny obwód usługi ExpressRoute to _obwód 1_, a obwód prywatny usługi ExpressRoute w chmurze znajduje się w innej subskrypcji i z oznaczeniem _obwodu 2_. 


## <a name="create-an-expressroute-authorization-key-in-the-private-cloud"></a>Tworzenie klucza autoryzacji ExpressRoute w chmurze prywatnej

1. W obszarze **zarządzanie chmurą prywatną w obszarze**Zarządzaj wybierz pozycję **łączność > ExpressRoute > zażądać klucza autoryzacji**.

   :::image type="content" source="media/expressroute-global-reach/start-request-auth-key.png" alt-text="Wybierz pozycję łączność > ExpressRoute > zażądać klucza autoryzacji, aby rozpocząć nowe żądanie.":::

2. Wprowadź nazwę klucza autoryzacji i wybierz pozycję **Utwórz**. 

   :::image type="content" source="media/expressroute-global-reach/create-global-reach-auth-key.png" alt-text="Wybierz pozycję łączność > ExpressRoute > zażądać klucza autoryzacji, aby rozpocząć nowe żądanie.":::

   Po utworzeniu nowy klucz zostanie wyświetlony na liście kluczy autoryzacji dla chmury prywatnej. 

   :::image type="content" source="media/expressroute-global-reach/show-global-reach-auth-key.png" alt-text="Wybierz pozycję łączność > ExpressRoute > zażądać klucza autoryzacji, aby rozpocząć nowe żądanie.":::

3. Zanotuj klucz autoryzacji i identyfikator ExpressRoute wraz z blokiem adresów/29. Będziesz ich używać w następnym kroku do kończenia komunikacji równorzędnej. 

## <a name="peer-private-cloud-to-on-premises-using-authorization-key"></a>Równorzędna Chmura prywatna z lokalnym użyciem klucza autoryzacji

Teraz, po utworzeniu klucza autoryzacji obwodu usługi ExpressRoute w chmurze prywatnej, można go połączyć z lokalnym obwodem usługi ExpressRoute.  Komunikacja równorzędna odbywa się z punktu widzenia lokalnego obwodu usługi ExpressRoute w [Azure Portal](#azure-portal-method) lub przy użyciu [interfejsu wiersza polecenia platformy Azure w Cloud Shell](#azure-cli-in-a-cloud-shell-method). Z każdą z tych metod będziesz używać identyfikatora zasobu i klucza autoryzacji obwodu usługi ExpressRoute w chmurze prywatnej, który został utworzony w poprzednich krokach, aby zakończyć komunikację równorzędną.

### <a name="azure-portal-method"></a>Azure Portal, Metoda

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu tej samej subskrypcji co lokalny obwód usługi ExpressRoute.

1. W obszarze **zarządzanie chmurą prywatną w obszarze**Zarządzaj wybierz pozycję **łączność > ExpressRoute Global REACH > Dodaj**.

   :::image type="content" source="./media/expressroute-global-reach/expressroute-global-reach-tab.png" alt-text="Wybierz pozycję łączność > ExpressRoute > zażądać klucza autoryzacji, aby rozpocząć nowe żądanie.":::

1. Połączenie z chmurą lokalną można utworzyć, wykonując jedną z następujących czynności:

   - Wybierz obwód usługi ExpressRoute z listy.
   - Jeśli masz Identyfikator obwodu, skopiuj go i wklej.

1. Wybierz pozycję **Połącz**. Nowe połączenie zostanie wyświetlone na liście połączeń w chmurze lokalnej.  

>[!TIP]
>Możesz usunąć lub rozłączyć połączenie z listy, wybierając pozycję **więcej**.  
>
> :::image type="content" source="./media/expressroute-global-reach/on-premises-connection-disconnect.png" alt-text="Wybierz pozycję łączność > ExpressRoute > zażądać klucza autoryzacji, aby rozpocząć nowe żądanie.":::

### <a name="azure-cli-in-a-cloud-shell-method"></a>Interfejs wiersza polecenia platformy Azure w metodzie Cloud Shell

Zostały uzupełnione [polecenia interfejsu CLI](../expressroute/expressroute-howto-set-global-reach-cli.md) o szczegółowe informacje i przykłady pomocne w konfigurowaniu komunikacji równorzędnej ExpressRoute Global REACH między środowiskami lokalnymi a chmurą prywatną rozwiązania Azure VMware.  

> [!TIP]  
> W przypadku zwięzłości w danych wyjściowych poleceń interfejsu wiersza polecenia platformy Azure te instrukcje mogą używać [ `–query` argumentu, aby wykonać zapytanie JMESPath w celu wyświetlenia tylko wymaganych wyników](/cli/azure/query-azure-cli).


1. Zaloguj się do Azure Portal przy użyciu tej samej subskrypcji co lokalny obwód usługi ExpressRoute i Otwórz Cloud Shell. Pozostaw powłokę jako bash.
 
   :::image type="content" source="media/expressroute-global-reach/open-cloud-shell.png" alt-text="Wybierz pozycję łączność > ExpressRoute > zażądać klucza autoryzacji, aby rozpocząć nowe żądanie.":::
 
2. W wierszu polecenia, wprowadź polecenie interfejsu wiersza polecenia platformy Azure, aby utworzyć komunikację równorzędną przy użyciu określonych informacji i identyfikatora zasobu, klucza autoryzacji i/29 bloku sieciowego CIDR. 

   Poniżej znajduje się przykład polecenia, którego będziesz używać, i dane wyjściowe wskazujące pomyślne przeprowadzenie komunikacji równorzędnej. Przykładowe polecenie jest oparte na poleceniu użytym w [kroku 3 z "Włączanie łączności między obwodami ExpressRoute w różnych subskrypcjach platformy Azure"](../expressroute/expressroute-howto-set-global-reach-cli.md#enable-connectivity-between-expressroute-circuits-in-different-azure-subscriptions).

   :::image type="content" source="media/expressroute-global-reach/azure-command-with-results.png" alt-text="Wybierz pozycję łączność > ExpressRoute > zażądać klucza autoryzacji, aby rozpocząć nowe żądanie.":::
 
   Teraz powinno być możliwe nawiązanie połączenia między środowiskami lokalnymi a chmurą prywatną za pośrednictwem komunikacji równorzędnej usługi ExpressRoute Global Reach.

> [!TIP]
> Możesz usunąć komunikację równorzędną, która została właśnie utworzona, wykonując instrukcje [Wyłącz łączność między sieciami lokalnymi](../expressroute/expressroute-howto-set-global-reach-cli.md#disable-connectivity-between-your-on-premises-networks) .


## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono sposób tworzenia drugiego klucza autoryzacji dla obwodu usługi ExpressRoute w chmurze prywatnej i włączenia komunikacji równorzędnej w chmurze ExpressRoute Global Reach. 

Przejdź do następnego samouczka, aby dowiedzieć się, jak wdrożyć i skonfigurować rozwiązanie VMware HCX dla chmury prywatnej rozwiązania Azure VMware.

> [!div class="nextstepaction"]
> [Wdrażanie i konfigurowanie usługi VMware HCX](tutorial-deploy-vmware-hcx.md)


<!-- LINKS - external-->

<!-- LINKS - internal -->