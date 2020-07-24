---
title: Równorzędne środowiska lokalne z chmurą prywatną
description: W tym samouczku rozwiązania Azure VMware (Automatyczna synchronizacja) można utworzyć ExpressRoute Global Reach komunikację równorzędną z chmurą prywatną w systemie.
ms.topic: tutorial
ms.date: 07/16/2020
ms.openlocfilehash: a9a002eab3219a0db74062570d31595bfcc0d6a3
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87101679"
---
# <a name="tutorial-peer-on-premises-environments-to-a-private-cloud"></a>Samouczek: środowiska lokalne w chmurze do chmury prywatnej

ExpressRoute Global Reach nawiązuje połączenie środowiska lokalnego z chmurami prywatnymi. Połączenie ExpressRoute Global Reach jest ustanawiane między obwodem prywatnej chmury ExpressRoute a istniejącym połączeniem ExpressRoute ze środowiskami lokalnymi.  Istnieją instrukcje dotyczące konfigurowania ExpressRoute Global Reach przy użyciu interfejsu wiersza polecenia platformy Azure i programu PowerShell, a ponadto zostały rozszerzone [polecenia CLI](../expressroute/expressroute-howto-set-global-reach-cli.md) z określonymi szczegółami i przykładami, które ułatwiają konfigurowanie ExpressRoute Global REACH komunikacji równorzędnej między środowiskami lokalnymi a chmurą prywatną rozwiązania Azure VMware (Automatyczna synchronizacja).   

Przed włączeniem łączności między dwoma obwodami usługi ExpressRoute przy użyciu usługi ExpressRoute Global Reach zapoznaj się z dokumentacją dotyczącą sposobu [włączania łączności w różnych subskrypcjach platformy Azure](../expressroute/expressroute-howto-set-global-reach-cli.md#enable-connectivity-between-expressroute-circuits-in-different-azure-subscriptions).  Obwód usługi ExpressRoute, który jest używany podczas [konfigurowania sieci Azure-to-Private w chmurze](tutorial-configure-networking.md) , wymaga tworzenia i używania kluczy autoryzacji podczas komunikacji równorzędnej z bramami ExpressRoute lub z innymi obwodami ExpressRoute przy użyciu Global REACH. Już użyto jednego klucza autoryzacji z obwodu usługi ExpressRoute i utworzysz drugi element do komunikacji równorzędnej z lokalnym obwodem usługi ExpressRoute.

> [!TIP]
> W kontekście tych instrukcji lokalny obwód usługi ExpressRoute to _obwód 1_, a obwód prywatny usługi ExpressRoute w chmurze znajduje się w innej subskrypcji i ma etykietę _obwód 2_. 

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Skorzystaj z istniejących instrukcji związanych z zarządzaniem obwodów usługi ExpressRoute i komunikacji równorzędnej ExpressRoute Global Reach
> * Tworzenie klucza autoryzacji _obwodu 2_— obwodu usługi ExpressRoute w chmurze prywatnej
> * Korzystanie z interfejsu wiersza polecenia platformy Azure w Cloud Shell subskrypcji _obwodu 1_ w celu włączenia komunikacji równorzędnej w chmurze Global REACH ExpressRoute

## <a name="prerequisites"></a>Wymagania wstępne

Wymagania wstępne dotyczące tego samouczka:
- Chmurę prywatną z obwodem usługi ExpressRoute za pomocą komunikacji równorzędnej z bramą ExpressRoute w usłudze Azure Virtual Network (VNet) — jest to _obwód 2_ z perspektywy procedur komunikacji równorzędnej.
- Oddzielny, działający obwód ExpressRoute używany do łączenia środowisk lokalnych z platformą Azure — jest to _obwód 1_ z perspektywy procedur komunikacji równorzędnej.
- /29 nienakładający się [blok adresów sieciowych](../expressroute/expressroute-routing.md#ip-addresses-used-for-peerings) dla komunikacji równorzędnej ExpressRoute Global REACH.

## <a name="create-an-expressroute-authorization-key-in-the-avs-private-cloud"></a>Utwórz klucz autoryzacji ExpressRoute w chmurze prywatnej automatycznej synchronizacji

1. W obszarze **zarządzanie chmurą prywatną w obszarze**Zarządzaj wybierz pozycję **łączność > ExpressRoute > zażądać klucza autoryzacji**.

   :::image type="content" source="media/expressroute-global-reach/start-request-auth-key.png" alt-text="Wybierz pozycję łączność > ExpressRoute > zażądać klucza autoryzacji, aby rozpocząć nowe żądanie":::

2. Wprowadź nazwę klucza autoryzacji i wybierz pozycję **Utwórz**. 

   :::image type="content" source="media/expressroute-global-reach/create-global-reach-auth-key.png" alt-text="Kliknij przycisk Utwórz, aby utworzyć nowy klucz autoryzacji.":::

   Po utworzeniu nowy klucz zostanie wyświetlony na liście kluczy autoryzacji dla chmury prywatnej. 

   :::image type="content" source="media/expressroute-global-reach/show-global-reach-auth-key.png" alt-text="Upewnij się, że nowy klucz autoryzacji pojawia się na liście kluczy dla chmury prywatnej.":::

3. Zanotuj klucz autoryzacji i identyfikator ExpressRoute wraz z blokiem adresów/29. Będziesz ich używać w następnym kroku do kończenia komunikacji równorzędnej. 

## <a name="peer-private-cloud-to-on-premises-using-authorization-key"></a>Równorzędna Chmura prywatna z lokalnym użyciem klucza autoryzacji

Teraz, po utworzeniu klucza autoryzacji obwodu usługi ExpressRoute w chmurze prywatnej, można go połączyć z lokalnym obwodem usługi ExpressRoute.  Komunikacja równorzędna odbywa się z punktu widzenia lokalnego obwodu usługi ExpressRoute przy użyciu interfejsu wiersza polecenia platformy Azure w ramach Cloud Shell i identyfikatora zasobu oraz klucza autoryzacji obwodu prywatnej chmury ExpressRoute, który został utworzony w poprzednich krokach.

> [!TIP]  
> W przypadku zwięzłości w danych wyjściowych poleceń interfejsu wiersza polecenia platformy Azure te instrukcje mogą używać [ `–query` argumentu, aby wykonać zapytanie JMESPath w celu wyświetlenia tylko wymaganych wyników](https://docs.microsoft.com/cli/azure/query-azure-cli?view=azure-cli-latest).


1. Zaloguj się do Azure Portal przy użyciu tej samej subskrypcji co lokalny obwód usługi ExpressRoute i Otwórz Cloud Shell. Pozostaw powłokę jako bash.
 
   :::image type="content" source="media/expressroute-global-reach/open-cloud-shell.png" alt-text="Zaloguj się do Azure Portal i Otwórz Cloud Shell.":::
 
2. W wierszu polecenia, wprowadź polecenie interfejsu wiersza polecenia platformy Azure, aby utworzyć komunikację równorzędną przy użyciu określonych informacji i identyfikatora zasobu, klucza autoryzacji i/29 bloku sieciowego CIDR. 

   Poniżej znajduje się przykład polecenia, którego będziesz używać, i dane wyjściowe wskazujące pomyślne przeprowadzenie komunikacji równorzędnej. Przykładowe polecenie jest oparte na poleceniu użytym w [kroku 3 z "Włączanie łączności między obwodami ExpressRoute w różnych subskrypcjach platformy Azure"](../expressroute/expressroute-howto-set-global-reach-cli.md#enable-connectivity-between-expressroute-circuits-in-different-azure-subscriptions).

   :::image type="content" source="media/expressroute-global-reach/azure-command-with-results.png" alt-text="Utwórz ExpressRoute Global Reach komunikacji równorzędnej za pomocą polecenia platformy Azure w Cloud Shell.":::
 
   Teraz powinno być możliwe nawiązanie połączenia między środowiskami lokalnymi a chmurą prywatną za pośrednictwem komunikacji równorzędnej usługi ExpressRoute Global Reach.

> [!TIP]
> Możesz usunąć komunikację równorzędną, która została właśnie utworzona, wykonując instrukcje [Wyłącz łączność między sieciami lokalnymi](../expressroute/expressroute-howto-set-global-reach-cli.md#disable-connectivity-between-your-on-premises-networks) .


## <a name="next-steps"></a>Następne kroki

Jeśli planujesz używanie rozszerzenia chmury hybrydowej (HCX) do migrowania obciążeń maszyn wirtualnych do chmury prywatnej, użyj procedury [rozwiązania Install HCX for Azure VMware](hybrid-cloud-extension-installation.md) .


<!-- LINKS - external-->

<!-- LINKS - internal -->
