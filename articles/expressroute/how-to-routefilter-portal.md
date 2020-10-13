---
title: 'ExpressRoute: filtry tras — Komunikacja równorzędna firmy Microsoft: Azure Portal'
description: W tym artykule opisano sposób konfigurowania filtrów tras dla komunikacji równorzędnej firmy Microsoft przy użyciu Azure Portal.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 07/01/2019
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: 468c7a0113a4603f4f47bb529145261ff50d96d4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91650548"
---
# <a name="configure-route-filters-for-microsoft-peering-azure-portal"></a>Konfigurowanie filtrów tras dla komunikacji równorzędnej firmy Microsoft: Azure Portal
> [!div class="op_single_selector"]
> * [Azure Portal](how-to-routefilter-portal.md)
> * [Azure PowerShell](how-to-routefilter-powershell.md)
> * [Interfejs wiersza polecenia platformy Azure](how-to-routefilter-cli.md)
> 

Filtry tras to sposób na korzystanie z podzestawu obsługiwanych usług przy użyciu komunikacji równorzędnej firmy Microsoft. Kroki opisane w tym artykule ułatwiają konfigurowanie filtrów tras dla obwodów usługi ExpressRoute i zarządzanie nimi.

Usługi Microsoft 365, takie jak Exchange Online, SharePoint Online i Skype dla firm, oraz usługi platformy Azure, takie jak Storage i SQL DB, są dostępne za pomocą komunikacji równorzędnej firmy Microsoft. W przypadku skonfigurowania komunikacji równorzędnej firmy Microsoft w obwodzie usługi ExpressRoute wszystkie prefiksy związane z tymi usługami są anonsowane za pomocą ustanowionych przez siebie sesji protokołu BGP. Wartość atrybutu Community protokołu BGP jest dołączana do każdego prefiksu w celu zidentyfikowania usługi oferowanej za pośrednictwem prefiksu. Aby zapoznać się z listą wartości społeczności BGP i usług, do których są mapowane, zobacz [społeczności protokołu BGP](expressroute-routing.md#bgp).

Jeśli wymagana jest łączność ze wszystkimi usługami, wiele prefiksów jest anonsowanych za pośrednictwem protokołu BGP. Znacznie zwiększa to rozmiar tabel tras przechowywanych przez routery w sieci. Jeśli planujesz korzystanie tylko z podzestawu usług oferowanych za pomocą komunikacji równorzędnej firmy Microsoft, możesz zmniejszyć rozmiar tabel tras na dwa sposoby. Dostępne możliwości:

- Odfiltruj niechciane prefiksy, stosując filtry tras dla społeczności protokołu BGP. Jest to standardowa metoda sieciowa i jest używana często w wielu sieciach.

- Zdefiniuj filtry tras i zastosuj je do obwodu ExpressRoute. Filtr tras to nowy zasób, który pozwala wybrać listę usług, które mają być używane przez komunikację równorzędną firmy Microsoft. Routery ExpressRoute wysyłają tylko listę prefiksów należących do usług określonych w filtrze tras.

### <a name="about-route-filters"></a><a name="about"></a>Filtry tras — informacje

W przypadku skonfigurowania komunikacji równorzędnej firmy Microsoft w obwodzie usługi ExpressRoute routery Microsoft Edge ustanawiają parę sesji protokołu BGP z routerami brzegowymi (usługodawcą). Żadne trasy nie są ogłaszane w sieci. Aby włączyć ogłaszanie tras w sieci, należy skojarzyć filtr tras.

Filtr tras umożliwia zidentyfikowanie usług, które mają być używane za pośrednictwem komunikacji równorzędnej firmy Microsoft w ramach obwodu usługi ExpressRoute. Zasadniczo jest to lista wszystkich wartości społeczności protokołu BGP, które mają być dozwolone. Gdy zasób filtru tras jest zdefiniowany i dołączony do obwodu usługi ExpressRoute, wszystkie prefiksy zamapowane do wartości atrybutu Community protokołu BGP są ogłaszane w sieci.

Aby można było dołączać filtry tras za pomocą usług Microsoft 365, musisz mieć autoryzację do korzystania z usług Microsoft 365 Services za pomocą ExpressRoute. Jeśli nie masz uprawnień do korzystania z usług Microsoft 365 za pomocą ExpressRoute, operacja dołączania filtrów trasy kończy się niepowodzeniem. Aby uzyskać więcej informacji na temat procesu autoryzacji, zobacz [Azure ExpressRoute for Microsoft 365](/microsoft-365/enterprise/azure-expressroute).

> [!IMPORTANT]
> Komunikacja równorzędna firmy Microsoft ze obwodów usługi ExpressRoute, które zostały skonfigurowane przed 1 sierpnia 2017, będzie miała wszystkie prefiksy usług anonsowane za pomocą komunikacji równorzędnej firmy Microsoft, nawet jeśli filtry tras nie są zdefiniowane. Komunikacja równorzędna firmy Microsoft obwodów usługi ExpressRoute, które są skonfigurowane w dniu lub po 1 sierpnia 2017, nie będzie miała żadnych prefiksów anonsowanych do momentu dołączenia do obwodu filtru tras.
> 
> 

### <a name="workflow"></a><a name="workflow"></a>Przepływ pracy

Aby można było pomyślnie połączyć się z usługami za pomocą komunikacji równorzędnej firmy Microsoft, należy wykonać następujące czynności konfiguracyjne:

- Musisz mieć aktywny obwód usługi ExpressRoute z obsługą komunikacji równorzędnej firmy Microsoft. Aby wykonać te zadania, można użyć następujących instrukcji:
  - [Utworzenie obwodu usługi ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md) i włączenie obwodu przez dostawcę łączności przed kontynuowaniem. Obwód ExpressRoute musi być w stanie aprowizacji i włączony.
  - [Utwórz komunikację równorzędną firmy Microsoft](expressroute-howto-routing-portal-resource-manager.md) , Jeśli zarządzasz sesją BGP bezpośrednio. Lub mieć dostawcę połączenia, który inicjuje komunikację równorzędną firmy Microsoft dla obwodu.

-  Należy utworzyć i skonfigurować filtr tras.
    - Identyfikowanie usług, z których można korzystać za pomocą komunikacji równorzędnej firmy Microsoft
    - Zidentyfikuj listę wartości społeczności BGP skojarzonych z usługami
    - Utwórz regułę zezwalającą na listę prefiksów zgodną z wartościami społeczności BGP

-  Należy dołączyć filtr trasy do obwodu ExpressRoute.

## <a name="before-you-begin"></a>Zanim rozpoczniesz

Przed rozpoczęciem konfiguracji upewnij się, że spełniasz następujące kryteria:

 - Przed rozpoczęciem konfiguracji Przejrzyj [wymagania wstępne](expressroute-prerequisites.md) i [przepływy pracy](expressroute-workflows.md) .

 - Musisz mieć aktywny obwód usługi ExpressRoute. Zanim przejdziesz dalej, postępuj zgodnie z instrukcjami, aby [utworzyć obwód usługi ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md), który powinien zostać włączony przez dostawcę połączenia. Obwód ExpressRoute musi być w stanie aprowizacji i włączony.

 - Konieczne jest posiadanie aktywnej komunikacji równorzędnej firmy Microsoft. Postępuj zgodnie z instrukcjami w obszarze [Tworzenie i modyfikowanie konfiguracji komunikacji równorzędnej](expressroute-howto-routing-portal-resource-manager.md)


## <a name="step-1-get-a-list-of-prefixes-and-bgp-community-values"></a><a name="prefixes"></a>Krok 1. Pobieranie listy prefiksów i wartości społeczności BGP

### <a name="1-get-a-list-of-bgp-community-values"></a>1. Pobierz listę wartości społeczności BGP

Wartości społeczności protokołu BGP skojarzone z usługami dostępnymi za pomocą komunikacji równorzędnej firmy Microsoft są dostępne na stronie [wymagania dotyczące routingu ExpressRoute](expressroute-routing.md) .

### <a name="2-make-a-list-of-the-values-that-you-want-to-use"></a>2. Utwórz listę wartości, których chcesz użyć

Utwórz listę [wartości społeczności protokołu BGP](expressroute-routing.md#bgp) , które mają być używane w filtrze tras. 

## <a name="step-2-create-a-route-filter-and-a-filter-rule"></a><a name="filter"></a>Krok 2. Tworzenie filtru tras i reguły filtrowania

Filtr trasy może mieć tylko jedną regułę, a reguła musi być typu "Zezwalaj". Ta reguła może zawierać listę skojarzonych z nią wartości społeczności BGP.

### <a name="1-create-a-route-filter"></a>1. Tworzenie filtru tras
Filtr tras można utworzyć, wybierając opcję tworzenia nowego zasobu. Kliknij pozycję **Utwórz zasób zasobów**  >  **Networking**  >  **RouteFilter**, jak pokazano na poniższej ilustracji:

![Zrzut ekranu przedstawiający stronę "filtr tras".](./media/how-to-routefilter-portal/CreateRouteFilter1.png)

Filtr trasy należy umieścić w grupie zasobów. 

![Zrzut ekranu przedstawiający stronę "Tworzenie filtru tras" z wprowadzonymi przykładowymi wartościami.](./media/how-to-routefilter-portal/CreateRouteFilter.png)

### <a name="2-create-a-filter-rule"></a>2. Utwórz regułę filtru

Możesz dodawać i aktualizować reguły, wybierając kartę Zarządzanie regułami dla filtru tras.

![Zrzut ekranu pokazujący stronę "przegląd" z wyróżnioną akcją "Zarządzaj regułą".](./media/how-to-routefilter-portal/ManageRouteFilter.png)


Z listy rozwijanej można wybrać usługi, z którymi chcesz nawiązać połączenie, i zapisać regułę po zakończeniu.

![Zrzut ekranu przedstawiający okno "Zarządzanie regułą" z usługami wybranymi na liście rozwijanej "dozwolone społeczności usług".](./media/how-to-routefilter-portal/AddRouteFilterRule.png)


## <a name="step-3-attach-the-route-filter-to-an-expressroute-circuit"></a><a name="attach"></a>Krok 3. dołączanie filtru tras do obwodu ExpressRoute

Filtr tras można dołączyć do obwodu, wybierając przycisk "Dodaj obwód" i wybierając obwód ExpressRoute z listy rozwijanej.

![Zrzut ekranu pokazujący stronę "przegląd" z wybraną akcją "Dodaj obwód".](./media/how-to-routefilter-portal/AddCktToRouteFilter.png)

Jeśli dostawca łączności konfiguruje komunikację równorzędną dla obwodu usługi ExpressRoute, Odśwież obwód z bloku obwodu ExpressRoute przed wybraniem przycisku "Dodaj obwód".

![Zrzut ekranu pokazujący stronę "przegląd" z wybraną akcją "Odśwież".](./media/how-to-routefilter-portal/RefreshExpressRouteCircuit.png)

## <a name="common-tasks"></a><a name="tasks"></a>Typowe zadania

### <a name="to-get-the-properties-of-a-route-filter"></a><a name="getproperties"></a>Aby uzyskać właściwości filtru tras

Właściwości filtru tras można wyświetlić po otwarciu zasobu w portalu.

![Zrzut ekranu przedstawiający stronę "przegląd".](./media/how-to-routefilter-portal/ViewRouteFilter.png)


### <a name="to-update-the-properties-of-a-route-filter"></a><a name="updateproperties"></a>Aby zaktualizować właściwości filtru tras

Możesz zaktualizować listę wartości społeczności BGP dołączanych do obwodu, wybierając przycisk "Zarządzaj regułą".


![Zrzut ekranu pokazujący stronę "przegląd" z wyróżnioną akcją "Zarządzaj regułą".](./media/how-to-routefilter-portal/ManageRouteFilter.png)

![Zrzut ekranu pokazujący okno "Zarządzanie regułą" z wybranymi usługami.](./media/how-to-routefilter-portal/AddRouteFilterRule.png) 


### <a name="to-detach-a-route-filter-from-an-expressroute-circuit"></a><a name="detach"></a>Aby odłączyć filtr trasy z obwodu usługi ExpressRoute

Aby odłączyć obwód od filtra trasy, kliknij prawym przyciskiem myszy obwód i kliknij pozycję "Usuń skojarzenie".

![Zrzut ekranu pokazujący stronę "przegląd" z wyróżnioną akcją "Usuń skojarzenie".](./media/how-to-routefilter-portal/DetachRouteFilter.png) 


### <a name="to-delete-a-route-filter"></a><a name="delete"></a>Aby usunąć filtr tras

Filtr tras można usunąć, wybierając przycisk Usuń. 

![Tworzenie filtru tras](./media/how-to-routefilter-portal/DeleteRouteFilter.png) 

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać więcej informacji na temat ExpressRoute, zobacz [często zadawane pytania](expressroute-faqs.md)dotyczące usługi ExpressRoute.

* Aby uzyskać informacje na temat przykładów konfiguracji routera, zobacz [przykłady konfiguracji routera w celu skonfigurowania routingu i zarządzania nim](expressroute-config-samples-routing.md). 
