---
title: Samouczek — prywatne środowiska lokalne do chmury prywatnej
description: Dowiedz się, jak utworzyć ExpressRoute Global Reach komunikacji równorzędnej w chmurze prywatnej w rozwiązaniu VMware platformy Azure.
ms.topic: tutorial
ms.date: 09/21/2020
ms.openlocfilehash: cf209071074fd4546ce64336b2b3b91fbcd4cb3b
ms.sourcegitcommit: 5e762a9d26e179d14eb19a28872fb673bf306fa7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/05/2021
ms.locfileid: "97901457"
---
# <a name="tutorial-peer-on-premises-environments-to-a-private-cloud"></a>Samouczek: środowiska lokalne w chmurze do chmury prywatnej

ExpressRoute Global Reach nawiązuje połączenie środowiska lokalnego z chmurą prywatną rozwiązania Azure VMware. Połączenie z usługą ExpressRoute Global Reach jest nawiązywane między obwodem usługi ExpressRoute w chmurze prywatnej a istniejącym połączeniem ExpressRoute z lokalnymi środowiskami. 

Obwód usługi ExpressRoute używany podczas [konfigurowania sieci Azure-to-Private w chmurze](tutorial-configure-networking.md) wymaga utworzenia kluczy autoryzacji i korzystania z nich.  Już użyto jednego klucza autoryzacji z obwodu usługi ExpressRoute, a w tym samouczku utworzysz drugi element do komunikacji równorzędnej z lokalnym obwodem usługi ExpressRoute.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie drugiego klucza autoryzacji _obwodu 2_— obwodu usługi ExpressRoute w chmurze prywatnej
> * Użyj [Azure Portal](#azure-portal-method) lub [interfejsu wiersza polecenia platformy Azure w metodzie Cloud Shell](#azure-cli-in-a-cloud-shell-method) w subskrypcji _obwodu 1_ , aby włączyć komunikację równorzędną w chmurze Global REACH ExpressRoute


## <a name="before-you-begin"></a>Przed rozpoczęciem

Przed włączeniem łączności między dwoma obwodami usługi ExpressRoute przy użyciu usługi ExpressRoute Global Reach zapoznaj się z dokumentacją dotyczącą sposobu [włączania łączności w różnych subskrypcjach platformy Azure](../expressroute/expressroute-howto-set-global-reach-cli.md#enable-connectivity-between-expressroute-circuits-in-different-azure-subscriptions).  


## <a name="prerequisites"></a>Wymagania wstępne

- Nawiązano połączenie z i z chmury prywatnej rozwiązań VMware platformy Azure z obwodem usługi ExpressRoute za pomocą komunikacji równorzędnej z bramą ExpressRoute w sieci wirtualnej platformy Azure — jest to _obwód 2_ z procedur komunikacji równorzędnej.  
- Oddzielny, działający obwód ExpressRoute używany do łączenia środowisk lokalnych z platformą Azure — który jest _obwodem 1_ z perspektywy procedur komunikacji równorzędnej.
- /29 nienakładający się [blok adresów sieciowych](../expressroute/expressroute-routing.md#ip-addresses-used-for-peerings) dla komunikacji równorzędnej ExpressRoute Global REACH.
- Upewnij się, że wszystkie routery, w tym usługa dostawcy ExpressRoute, obsługują 4-bajtowy numer systemu autonomicznego (ASN). Rozwiązanie VMware platformy Azure używa 4-bajtowego publicznego WPW do anonsowania tras.

> [!TIP]
> W kontekście tych wymagań wstępnych lokalny obwód usługi ExpressRoute to _obwód 1_, a obwód prywatny usługi ExpressRoute w chmurze znajduje się w innej subskrypcji i z oznaczeniem _obwodu 2_. 


## <a name="create-an-expressroute-authorization-key-in-the-private-cloud"></a>Tworzenie klucza autoryzacji ExpressRoute w chmurze prywatnej

1. W obszarze **zarządzanie chmurą prywatną w obszarze** Zarządzaj wybierz pozycję **łączność > ExpressRoute > zażądać klucza autoryzacji**.

   :::image type="content" source="media/expressroute-global-reach/start-request-auth-key.png" alt-text="Wybierz pozycję łączność > ExpressRoute > zażądać klucza autoryzacji, aby rozpocząć nowe żądanie.":::

2. Wprowadź nazwę klucza autoryzacji i wybierz pozycję **Utwórz**. 

   :::image type="content" source="media/expressroute-global-reach/create-global-reach-auth-key.png" alt-text="Wybierz pozycję Utwórz, aby utworzyć nowy klucz autoryzacji. ":::

   Po utworzeniu nowy klucz zostanie wyświetlony na liście kluczy autoryzacji dla chmury prywatnej. 

   :::image type="content" source="media/expressroute-global-reach/show-global-reach-auth-key.png" alt-text="Upewnij się, że nowy klucz autoryzacji pojawia się na liście kluczy dla chmury prywatnej. ":::

3. Zanotuj klucz autoryzacji i identyfikator ExpressRoute wraz z blokiem adresów/29. Będziesz ich używać w następnym kroku do kończenia komunikacji równorzędnej. 

## <a name="peer-private-cloud-to-on-premises-using-authorization-key"></a>Równorzędna Chmura prywatna z lokalnym użyciem klucza autoryzacji

Teraz, po utworzeniu klucza autoryzacji obwodu usługi ExpressRoute w chmurze prywatnej, można go połączyć z lokalnym obwodem usługi ExpressRoute.  Komunikacja równorzędna odbywa się z punktu widzenia lokalnego obwodu usługi ExpressRoute w [Azure Portal](#azure-portal-method) lub przy użyciu [interfejsu wiersza polecenia platformy Azure w Cloud Shell](#azure-cli-in-a-cloud-shell-method). W obu metodach należy użyć identyfikatora zasobu i klucza autoryzacji obwodu usługi ExpressRoute w chmurze prywatnej, aby zakończyć komunikację równorzędną.

### <a name="azure-portal-method"></a>Azure Portal, Metoda

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu tej samej subskrypcji co lokalny obwód usługi ExpressRoute.

1. W obszarze **zarządzanie chmurą prywatną w obszarze** Zarządzaj wybierz pozycję **łączność > ExpressRoute Global REACH > Dodaj**.

   :::image type="content" source="./media/expressroute-global-reach/expressroute-global-reach-tab.png" alt-text="Z menu wybierz pozycję łączność, ExpressRoute kartę Global Reach a następnie Dodaj.":::

1. Połączenie z chmurą lokalną można utworzyć, wykonując jedną z następujących czynności:

   - Wybierz obwód usługi ExpressRoute z listy.
   - Jeśli masz Identyfikator obwodu, skopiuj go i wklej.

1. Wybierz pozycję **Połącz**. Nowe połączenie zostanie wyświetlone na liście połączeń w chmurze lokalnej.  

>[!TIP]
>Możesz usunąć lub rozłączyć połączenie z listy, wybierając pozycję **więcej**.  
>
> :::image type="content" source="./media/expressroute-global-reach/on-premises-connection-disconnect.png" alt-text="Odłączanie lub usuwanie połączenia lokalnego":::

### <a name="azure-cli-in-a-cloud-shell-method"></a>Interfejs wiersza polecenia platformy Azure w metodzie Cloud Shell

Zostały uzupełnione [polecenia interfejsu CLI](../expressroute/expressroute-howto-set-global-reach-cli.md) o szczegółowe informacje i przykłady pomocne w konfigurowaniu komunikacji równorzędnej ExpressRoute Global REACH między środowiskami lokalnymi a chmurą prywatną rozwiązania Azure VMware.  

> [!TIP]  
> W przypadku zwięzłości w danych wyjściowych poleceń interfejsu wiersza polecenia platformy Azure te instrukcje mogą używać [ `–query` argumentu, aby wykonać zapytanie JMESPath w celu wyświetlenia tylko wymaganych wyników](/cli/azure/query-azure-cli).


1. Zaloguj się do Azure Portal przy użyciu tej samej subskrypcji co lokalny obwód usługi ExpressRoute i Otwórz Cloud Shell. Pozostaw powłokę jako bash.
 
   :::image type="content" source="media/expressroute-global-reach/open-cloud-shell.png" alt-text="Zaloguj się do Azure Portal i Otwórz Cloud Shell.":::
 
2. Wprowadź polecenie interfejsu wiersza polecenia platformy Azure, aby utworzyć komunikację równorzędną. Użyj określonych informacji i identyfikatora zasobu, klucza autoryzacji i/29 bloku sieciowego CIDR. 

   Obraz pokazuje przykład polecenia, którego będziesz używać, i dane wyjściowe wskazujące na pomyślne przeprowadzenie komunikacji równorzędnej. Przykładowe polecenie jest oparte na poleceniu użytym w [kroku 3 z "Włączanie łączności między obwodami ExpressRoute w różnych subskrypcjach platformy Azure"](../expressroute/expressroute-howto-set-global-reach-cli.md#enable-connectivity-between-expressroute-circuits-in-different-azure-subscriptions).

   :::image type="content" source="media/expressroute-global-reach/azure-command-with-results.png" alt-text="Utwórz ExpressRoute Global Reach komunikacji równorzędnej za pomocą polecenia platformy Azure w Cloud Shell.":::
 
   Możesz łączyć się ze środowisk lokalnych z chmurą prywatną za pośrednictwem komunikacji równorzędnej ExpressRoute Global Reach.

> [!TIP]
> Możesz usunąć komunikację równorzędną, która została właśnie utworzona, wykonując instrukcje [Wyłącz łączność między sieciami lokalnymi](../expressroute/expressroute-howto-set-global-reach-cli.md#disable-connectivity-between-your-on-premises-networks) .


## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono sposób tworzenia drugiego klucza autoryzacji obwodu usługi ExpressRoute w chmurze prywatnej. Zawarto również informacje na temat włączania komunikacji równorzędnej w chmurze Global Reach ExpressRoute na poziomie lokalnym i prywatnym. 

Przejdź do następnego samouczka, aby dowiedzieć się, jak wdrożyć i skonfigurować rozwiązanie VMware HCX dla chmury prywatnej rozwiązania Azure VMware.

> [!div class="nextstepaction"]
> [Wdrażanie i konfigurowanie usługi VMware HCX](tutorial-deploy-vmware-hcx.md)


<!-- LINKS - external-->

<!-- LINKS - internal -->