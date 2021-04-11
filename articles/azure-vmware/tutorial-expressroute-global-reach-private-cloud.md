---
title: Samouczek — prywatne środowiska lokalne do chmury prywatnej
description: Dowiedz się, jak utworzyć ExpressRoute Global Reach komunikacji równorzędnej w chmurze prywatnej w rozwiązaniu VMware platformy Azure.
ms.topic: tutorial
ms.date: 03/17/2021
ms.openlocfilehash: 798b822989127ccbb00e971de2cc4147ac234259
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2021
ms.locfileid: "106449566"
---
# <a name="tutorial-peer-on-premises-environments-to-a-private-cloud"></a>Samouczek: środowiska lokalne w chmurze do chmury prywatnej

ExpressRoute Global Reach nawiązuje połączenie środowiska lokalnego z chmurą prywatną rozwiązania Azure VMware. Połączenie z usługą ExpressRoute Global Reach jest nawiązywane między obwodem usługi ExpressRoute w chmurze prywatnej a istniejącym połączeniem ExpressRoute z lokalnymi środowiskami. 

Obwód usługi ExpressRoute używany podczas [konfigurowania sieci dla chmury prywatnej VMware na platformie Azure](tutorial-configure-networking.md) wymaga utworzenia kluczy autoryzacji i korzystania z nich.  Już użyto jednego klucza autoryzacji z obwodu usługi ExpressRoute, a w tym samouczku utworzysz drugi klucz autoryzacji do komunikacji równorzędnej z lokalnym obwodem usługi ExpressRoute.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie drugiego klucza autoryzacji _obwodu 2_— obwodu usługi ExpressRoute w chmurze prywatnej.
> * Użyj Azure Portal lub interfejsu wiersza polecenia platformy Azure w metodzie Cloud Shell w subskrypcji _obwodu 1_ , aby włączyć komunikację równorzędną Global REACH ExpressRoute w chmurze na poziomie lokalnym i prywatnym.


## <a name="before-you-begin"></a>Zanim rozpoczniesz

Przed włączeniem łączności między dwoma obwodami usługi ExpressRoute przy użyciu usługi ExpressRoute Global Reach zapoznaj się z dokumentacją dotyczącą sposobu [włączania łączności w różnych subskrypcjach platformy Azure](../expressroute/expressroute-howto-set-global-reach-cli.md#enable-connectivity-between-expressroute-circuits-in-different-azure-subscriptions).  

## <a name="prerequisites"></a>Wymagania wstępne

- Nawiązano połączenie z i z chmury prywatnej rozwiązań VMware platformy Azure z obwodem usługi ExpressRoute za pomocą komunikacji równorzędnej z bramą ExpressRoute w sieci wirtualnej platformy Azure — jest to obwód 2 z procedur komunikacji równorzędnej.
- Oddzielny, działający obwód ExpressRoute używany do łączenia środowisk lokalnych z platformą Azure — który jest obwodem 1 z perspektywy procedur komunikacji równorzędnej.
- /29 nienakładający się [blok adresów sieciowych](../expressroute/expressroute-routing.md#ip-addresses-used-for-peerings) dla komunikacji równorzędnej ExpressRoute Global REACH.
- Upewnij się, że wszystkie bramy, w tym usługa dostawcy ExpressRoute, obsługują 4-bajtowy numer systemu autonomicznego (ASN). Rozwiązanie VMware platformy Azure używa 4-bajtowego publicznego WPW do anonsowania tras.

>[!IMPORTANT]
>W kontekście tych wymagań wstępnych lokalny obwód usługi ExpressRoute to _obwód 1_, a obwód prywatny usługi ExpressRoute w chmurze znajduje się w innej subskrypcji i z oznaczeniem _obwodu 2_.

## <a name="create-an-expressroute-authorization-key-in-the-private-cloud-expressroute-circuit"></a>Tworzenie klucza autoryzacji ExpressRoute w obwodzie prywatnej chmury ExpressRoute

[!INCLUDE [request-authorization-key](includes/request-authorization-key.md)]
 
## <a name="peer-private-cloud-to-on-premises-with-authorization-key"></a>Usługa Peer Private do lokalnego z kluczem autoryzacji
Teraz, po utworzeniu klucza autoryzacji obwodu usługi ExpressRoute w chmurze prywatnej, można go połączyć z lokalnym obwodem usługi ExpressRoute. Komunikacja równorzędna odbywa się z punktu widzenia lokalnego obwodu usługi ExpressRoute w **Azure Portal** lub przy użyciu **interfejsu wiersza polecenia platformy Azure w Cloud Shell**. W obu metodach należy użyć identyfikatora zasobu i klucza autoryzacji obwodu usługi ExpressRoute w chmurze prywatnej, aby zakończyć komunikację równorzędną.

### <a name="portal"></a>[Portal](#tab/azure-portal)
 
1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu tej samej subskrypcji co lokalny obwód usługi ExpressRoute.

1. W obszarze **zarządzanie chmurą prywatną w obszarze** Zarządzaj wybierz pozycję **łączność**  >  **ExpressRoute Global REACH**  >  **Dodaj**.

    :::image type="content" source="./media/expressroute-global-reach/expressroute-global-reach-tab.png" alt-text="Zrzut ekranu przedstawiający kartę ExpressRoute Global Reach w chmurze prywatnej rozwiązania Azure VMware.":::

1. Utwórz połączenie z chmurą lokalną. Wykonaj jedną z następujących czynności, a następnie wybierz pozycję **Utwórz**:

   - Wybierz **obwód usługi ExpressRoute** z listy lub
   - Jeśli masz Identyfikator obwodu, wklej go w polu i Podaj klucz autoryzacji, który właśnie został utworzony.

   :::image type="content" source="./media/expressroute-global-reach/on-premises-cloud-connections.png" alt-text="Wprowadź identyfikator ExpressRoute i klucz autoryzacji, a następnie wybierz pozycję Utwórz.":::   
   
   Nowe połączenie zostanie wyświetlone na liście połączeń w chmurze lokalnej.

>[!TIP]
>Możesz usunąć lub rozłączyć połączenie z listy, wybierając pozycję **więcej**.  
>
> :::image type="content" source="./media/expressroute-global-reach/on-premises-connection-disconnect.png" alt-text="Odłączanie lub usuwanie połączenia lokalnego":::

### <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Zostały uzupełnione [polecenia interfejsu CLI](../expressroute/expressroute-howto-set-global-reach-cli.md) o szczegółowe informacje i przykłady pomocne w konfigurowaniu komunikacji równorzędnej ExpressRoute Global REACH między środowiskami lokalnymi a chmurą prywatną rozwiązania Azure VMware.

>[!TIP]
>W przypadku zwięzłości w danych wyjściowych poleceń interfejsu wiersza polecenia platformy Azure te instrukcje mogą używać [ `–query` argumentu](/cli/azure/query-azure-cli) , aby wykonać zapytanie JMESPath w celu wyświetlenia tylko wymaganych wyników.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu tej samej subskrypcji co lokalny obwód usługi ExpressRoute. 

1. Otwórz Cloud Shell i pozostaw powłokę jako bash.

   :::image type="content" source="media/expressroute-global-reach/azure-portal-cloud-shell.png" alt-text="Zrzut ekranu przedstawiający Cloud Shell Azure Portal.":::

1. Utwórz komunikację równorzędną z obwodem 1, przekazując w identyfikator zasobu obwodu 2 i klucz autoryzacji. 

   ```azurecli-interactive
   az network express-route peering connection create -g <ResourceGroupName> --circuit-name <Circuit1Name> --peering-name AzurePrivatePeering -n <ConnectionName> --peer-circuit <Circuit2ResourceID> --address-prefix <__.__.__.__/29> --authorization-key <authorizationKey>
   ```

   :::image type="content" source="media/expressroute-global-reach/azure-command-with-results.png" alt-text="Zrzut ekranu przedstawiający polecenie i wyniki pomyślnej komunikacji równorzędnej między obwodem 1 i obwodem 2.":::

Możesz łączyć się ze środowisk lokalnych z chmurą prywatną za pośrednictwem komunikacji równorzędnej ExpressRoute Global Reach.

>[!TIP]
>Komunikację równorzędną można usunąć, postępując zgodnie z instrukcjami [wyłączania łączności między sieciami lokalnymi](../expressroute/expressroute-howto-set-global-reach-cli.md#disable-connectivity-between-your-on-premises-networks) .


---

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono sposób włączania komunikacji równorzędnej w chmurze Global Reach ExpressRoute na poziomie lokalnym i prywatnym. 

Przejdź do następnego samouczka, aby dowiedzieć się, jak wdrożyć i skonfigurować rozwiązanie VMware HCX dla chmury prywatnej rozwiązania Azure VMware.

> [!div class="nextstepaction"]
> [Wdrażanie i konfigurowanie usługi VMware HCX](tutorial-deploy-vmware-hcx.md)


<!-- LINKS - external-->

<!-- LINKS - internal -->
