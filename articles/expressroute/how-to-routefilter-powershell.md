---
title: 'ExpressRoute: filtry tras — Komunikacja równorzędna firmy Microsoft: Azure PowerShell'
description: W tym artykule opisano sposób konfigurowania filtrów tras dla komunikacji równorzędnej firmy Microsoft przy użyciu programu PowerShell
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 02/25/2019
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: c4ca4362f10ea6ed2fa7cc39370fc9b4c764ff3b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89566198"
---
# <a name="configure-route-filters-for-microsoft-peering-powershell"></a>Konfigurowanie filtrów tras dla komunikacji równorzędnej firmy Microsoft: PowerShell
> [!div class="op_single_selector"]
> * [Azure Portal](how-to-routefilter-portal.md)
> * [Azure PowerShell](how-to-routefilter-powershell.md)
> * [Interfejs wiersza polecenia platformy Azure](how-to-routefilter-cli.md)
> 

Filtry tras to sposób na korzystanie z podzestawu obsługiwanych usług przy użyciu komunikacji równorzędnej firmy Microsoft. Kroki opisane w tym artykule ułatwiają konfigurowanie filtrów tras dla obwodów usługi ExpressRoute i zarządzanie nimi.

Usługi Microsoft 365, takie jak Exchange Online, SharePoint Online i Skype dla firm oraz usługi publiczne platformy Azure, takie jak Storage i SQL DB, są dostępne za pomocą komunikacji równorzędnej firmy Microsoft. Usługi publiczne platformy Azure są wybierane dla poszczególnych regionów i nie można ich definiować na usługę publiczną.

W przypadku skonfigurowania komunikacji równorzędnej firmy Microsoft w obwodzie usługi ExpressRoute i dołączeniu filtru tras wszystkie prefiksy wybrane dla tych usług są anonsowane za pomocą ustanowionych przez siebie sesji protokołu BGP. Wartość atrybutu Community protokołu BGP jest dołączana do każdego prefiksu w celu zidentyfikowania usługi oferowanej za pośrednictwem prefiksu. Aby zapoznać się z listą wartości społeczności BGP i usług, do których są mapowane, zobacz [społeczności protokołu BGP](expressroute-routing.md#bgp).

Jeśli wymagana jest łączność ze wszystkimi usługami, wiele prefiksów jest anonsowanych za pośrednictwem protokołu BGP. Znacznie zwiększa to rozmiar tabel tras przechowywanych przez routery w sieci. Jeśli planujesz korzystanie tylko z podzestawu usług oferowanych za pomocą komunikacji równorzędnej firmy Microsoft, możesz zmniejszyć rozmiar tabel tras na dwa sposoby. Dostępne możliwości:

- Odfiltruj niechciane prefiksy, stosując filtry tras dla społeczności protokołu BGP. Jest to standardowa metoda sieciowa i jest używana często w wielu sieciach.

- Zdefiniuj filtry tras i zastosuj je do obwodu ExpressRoute. Filtr tras to nowy zasób, który pozwala wybrać listę usług, które mają być używane przez komunikację równorzędną firmy Microsoft. Routery ExpressRoute wysyłają tylko listę prefiksów należących do usług określonych w filtrze tras.

### <a name="about-route-filters"></a><a name="about"></a>Filtry tras — informacje

W przypadku skonfigurowania komunikacji równorzędnej firmy Microsoft w obwodzie usługi ExpressRoute routery brzegowe sieci firmy Microsoft tworzą parę sesji BGP z routerami brzegowymi (usługodawcą). Żadne trasy nie są ogłaszane w sieci. Aby włączyć ogłaszanie tras w sieci, należy skojarzyć filtr tras.

Filtr tras umożliwia zidentyfikowanie usług, które mają być używane za pośrednictwem komunikacji równorzędnej firmy Microsoft w ramach obwodu usługi ExpressRoute. Zasadniczo jest to lista dozwolonych wszystkich wartości społeczności protokołu BGP. Gdy zasób filtru tras jest zdefiniowany i dołączony do obwodu usługi ExpressRoute, wszystkie prefiksy zamapowane do wartości atrybutu Community protokołu BGP są ogłaszane w sieci.

Aby można było dołączać filtry tras za pomocą usług Microsoft 365, musisz mieć autoryzację do korzystania z usług Microsoft 365 Services za pomocą ExpressRoute. Jeśli nie masz uprawnień do korzystania z usług Microsoft 365 za pomocą ExpressRoute, operacja dołączania filtrów trasy kończy się niepowodzeniem. Aby uzyskać więcej informacji na temat procesu autoryzacji, zobacz [Azure ExpressRoute for Microsoft 365](/microsoft-365/enterprise/azure-expressroute).

> [!IMPORTANT]
> Komunikacja równorzędna firmy Microsoft ze obwodów usługi ExpressRoute, które zostały skonfigurowane przed 1 sierpnia 2017, będzie miała wszystkie prefiksy usług anonsowane za pomocą komunikacji równorzędnej firmy Microsoft, nawet jeśli filtry tras nie są zdefiniowane. Komunikacja równorzędna firmy Microsoft obwodów usługi ExpressRoute, które są skonfigurowane w dniu lub po 1 sierpnia 2017, nie będzie miała żadnych prefiksów anonsowanych do momentu dołączenia do obwodu filtru tras.
> 
> 

### <a name="workflow"></a><a name="workflow"></a>Przepływ pracy

Aby można było pomyślnie połączyć się z usługami za pomocą komunikacji równorzędnej firmy Microsoft, należy wykonać następujące czynności konfiguracyjne:

- Musisz mieć aktywny obwód usługi ExpressRoute z obsługą komunikacji równorzędnej firmy Microsoft. Aby wykonać te zadania, można użyć następujących instrukcji:
  - [Utworzenie obwodu usługi ExpressRoute](expressroute-howto-circuit-arm.md) i włączenie obwodu przez dostawcę łączności przed kontynuowaniem. Obwód ExpressRoute musi być w stanie aprowizacji i włączony.
  - [Utwórz komunikację równorzędną firmy Microsoft](expressroute-circuit-peerings.md) , Jeśli zarządzasz sesją BGP bezpośrednio. Lub mieć dostawcę połączenia, który inicjuje komunikację równorzędną firmy Microsoft dla obwodu.

-  Należy utworzyć i skonfigurować filtr tras.
    - Identyfikowanie usług, z których można korzystać za pomocą komunikacji równorzędnej firmy Microsoft
    - Zidentyfikuj listę wartości społeczności BGP skojarzonych z usługami
    - Utwórz regułę zezwalającą na listę prefiksów zgodną z wartościami społeczności BGP

-  Należy dołączyć filtr trasy do obwodu ExpressRoute.

## <a name="before-you-begin"></a>Zanim rozpoczniesz

Przed rozpoczęciem konfiguracji upewnij się, że spełniasz następujące kryteria:

 - Przed rozpoczęciem konfiguracji Przejrzyj [wymagania wstępne](expressroute-prerequisites.md) i [przepływy pracy](expressroute-workflows.md) .

 - Musisz mieć aktywny obwód usługi ExpressRoute. Zanim przejdziesz dalej, postępuj zgodnie z instrukcjami, aby [utworzyć obwód usługi ExpressRoute](expressroute-howto-circuit-arm.md), który powinien zostać włączony przez dostawcę połączenia. Obwód ExpressRoute musi być w stanie aprowizacji i włączony.

 - Konieczne jest posiadanie aktywnej komunikacji równorzędnej firmy Microsoft. Postępuj zgodnie z instrukcjami w artykule [Tworzenie i modyfikowanie konfiguracji komunikacji równorzędnej](expressroute-circuit-peerings.md) .


### <a name="working-with-azure-powershell"></a>Praca z Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

### <a name="log-in-to-your-azure-account"></a>Zaloguj się do konta platformy Azure

Przed rozpoczęciem tej konfiguracji musisz zalogować się na koncie platformy Azure. Polecenie cmdlet wyświetla monit o podanie poświadczeń logowania dla konta platformy Azure. Po zalogowaniu pobiera ono ustawienia konta, aby były dostępne dla programu Azure PowerShell.

Otwórz konsolę programu PowerShell z podwyższonym poziomem uprawnień i połącz się ze swoim kontem. Skorzystaj z następującego przykładu, aby nawiązać połączenie. Jeśli używasz Azure Cloud Shell, nie musisz uruchamiać tego polecenia cmdlet, ponieważ zostanie ono automatycznie zalogowane.

```azurepowershell
Connect-AzAccount
```

Jeśli masz wiele subskrypcji platformy Azure, wyświetl subskrypcje dla konta.

```azurepowershell-interactive
Get-AzSubscription
```

Wskaż subskrypcję, której chcesz użyć.

```azurepowershell-interactive
Select-AzSubscription -SubscriptionName "Replace_with_your_subscription_name"
```

## <a name="step-1-get-a-list-of-prefixes-and-bgp-community-values"></a><a name="prefixes"></a>Krok 1. Pobieranie listy prefiksów i wartości społeczności BGP

### <a name="1-get-a-list-of-bgp-community-values"></a>1. Pobierz listę wartości społeczności BGP

Użyj poniższego polecenia cmdlet, aby uzyskać listę wartości społeczności protokołu BGP skojarzonych z usługami dostępnymi za pomocą komunikacji równorzędnej firmy Microsoft oraz listę skojarzonych z nimi prefiksów:

```azurepowershell-interactive
Get-AzBgpServiceCommunity
```
### <a name="2-make-a-list-of-the-values-that-you-want-to-use"></a>2. Utwórz listę wartości, których chcesz użyć

Utwórz listę wartości społeczności protokołu BGP, które mają być używane w filtrze tras.

## <a name="step-2-create-a-route-filter-and-a-filter-rule"></a><a name="filter"></a>Krok 2. Tworzenie filtru tras i reguły filtrowania

Filtr trasy może mieć tylko jedną regułę, a reguła musi być typu "Zezwalaj". Ta reguła może zawierać listę skojarzonych z nią wartości społeczności BGP.

### <a name="1-create-a-route-filter"></a>1. Tworzenie filtru tras

Najpierw utwórz filtr tras. Polecenie "New-AzRouteFilter" tworzy tylko zasób filtru tras. Po utworzeniu zasobu należy utworzyć regułę i dołączyć ją do obiektu filtr trasy. Uruchom następujące polecenie, aby utworzyć zasób filtru tras:

```azurepowershell-interactive
New-AzRouteFilter -Name "MyRouteFilter" -ResourceGroupName "MyResourceGroup" -Location "West US"
```

### <a name="2-create-a-filter-rule"></a>2. Utwórz regułę filtru

Zestaw społeczności protokołu BGP można określić jako listę rozdzieloną przecinkami, jak pokazano w przykładzie. Uruchom następujące polecenie, aby utworzyć nową regułę:
 
```azurepowershell-interactive
$rule = New-AzRouteFilterRuleConfig -Name "Allow-EXO-D365" -Access Allow -RouteFilterRuleType Community -CommunityList 12076:5010,12076:5040
```

### <a name="3-add-the-rule-to-the-route-filter"></a>3. Dodaj regułę do filtru tras

Uruchom następujące polecenie, aby dodać regułę filtru do filtru trasy:
 
```azurepowershell-interactive
$routefilter = Get-AzRouteFilter -Name "RouteFilterName" -ResourceGroupName "ExpressRouteResourceGroupName"
$routefilter.Rules.Add($rule)
Set-AzRouteFilter -RouteFilter $routefilter
```

## <a name="step-3-attach-the-route-filter-to-an-expressroute-circuit"></a><a name="attach"></a>Krok 3. dołączanie filtru tras do obwodu ExpressRoute

Uruchom następujące polecenie, aby dołączyć filtr trasy do obwodu ExpressRoute, przy założeniu, że masz tylko komunikację równorzędną firmy Microsoft:

```azurepowershell-interactive
$ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
$ckt.Peerings[0].RouteFilter = $routefilter 
Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

## <a name="common-tasks"></a><a name="tasks"></a>Typowe zadania

### <a name="to-get-the-properties-of-a-route-filter"></a><a name="getproperties"></a>Aby uzyskać właściwości filtru tras

Aby uzyskać właściwości filtru tras, wykonaj następujące czynności:

1. Uruchom następujące polecenie, aby pobrać zasób filtr trasy:

   ```azurepowershell-interactive
   $routefilter = Get-AzRouteFilter -Name "RouteFilterName" -ResourceGroupName "ExpressRouteResourceGroupName"
   ```
2. Pobierz reguły filtra trasy dla zasobu filtru tras, uruchamiając następujące polecenie:

   ```azurepowershell-interactive
   $routefilter = Get-AzRouteFilter -Name "RouteFilterName" -ResourceGroupName "ExpressRouteResourceGroupName"
   $rule = $routefilter.Rules[0]
   ```

### <a name="to-update-the-properties-of-a-route-filter"></a><a name="updateproperties"></a>Aby zaktualizować właściwości filtru tras

Jeśli filtr trasy jest już dołączony do obwodu, aktualizacje na liście społeczność protokołu BGP automatycznie propagują odpowiednie zmiany anonsu prefiksu za pomocą ustanowionych sesji protokołu BGP. Listę społeczności protokołu BGP filtru tras można zaktualizować za pomocą następującego polecenia:

```azurepowershell-interactive
$routefilter = Get-AzRouteFilter -Name "RouteFilterName" -ResourceGroupName "ExpressRouteResourceGroupName"
$routefilter.rules[0].Communities = "12076:5030", "12076:5040"
Set-AzRouteFilter -RouteFilter $routefilter
```

### <a name="to-detach-a-route-filter-from-an-expressroute-circuit"></a><a name="detach"></a>Aby odłączyć filtr trasy z obwodu usługi ExpressRoute

Gdy filtr trasy zostanie odłączony od obwodu usługi ExpressRoute, żadne prefiksy nie są anonsowane przez sesję protokołu BGP. Filtr tras można odłączyć od obwodu usługi ExpressRoute przy użyciu następującego polecenia:
  
```azurepowershell-interactive
$ckt.Peerings[0].RouteFilter = $null
Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="to-delete-a-route-filter"></a><a name="delete"></a>Aby usunąć filtr tras

Filtr trasy można usunąć tylko wtedy, gdy nie jest on dołączony do żadnego obwodu. Upewnij się, że filtr trasy nie jest dołączony do żadnego obwodu przed próbą usunięcia go. Filtr tras można usunąć za pomocą następującego polecenia:

```azurepowershell-interactive
Remove-AzRouteFilter -Name "MyRouteFilter" -ResourceGroupName "MyResourceGroup"
```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat ExpressRoute, zobacz [często zadawane pytania](expressroute-faqs.md)dotyczące usługi ExpressRoute.
