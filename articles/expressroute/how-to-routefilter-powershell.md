---
title: 'Samouczek: Konfigurowanie filtrów tras dla komunikacji równorzędnej firmy Microsoft — Azure PowerShell'
description: W tym samouczku opisano sposób konfigurowania filtrów tras dla komunikacji równorzędnej firmy Microsoft przy użyciu programu PowerShell.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: tutorial
ms.date: 10/08/2020
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: 90d4def5a1c08e305b9315f299e83e2187b6be2c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91969946"
---
# <a name="tutorial-configure-route-filters-for-microsoft-peering-using-powershell"></a>Samouczek: Konfigurowanie filtrów tras dla komunikacji równorzędnej firmy Microsoft przy użyciu programu PowerShell

> [!div class="op_single_selector"]
> * [Azure Portal](how-to-routefilter-portal.md)
> * [Azure PowerShell](how-to-routefilter-powershell.md)
> * [Interfejs wiersza polecenia platformy Azure](how-to-routefilter-cli.md)
> 

Filtry tras to sposób na korzystanie z podzestawu obsługiwanych usług przy użyciu komunikacji równorzędnej firmy Microsoft. Kroki opisane w tym artykule ułatwiają konfigurowanie filtrów tras dla obwodów usługi ExpressRoute i zarządzanie nimi.

Usługi Microsoft 365, takie jak Exchange Online, SharePoint Online i Skype dla firm oraz usługi publiczne platformy Azure, takie jak Storage i SQL DB, są dostępne za pomocą komunikacji równorzędnej firmy Microsoft. Usługi publiczne platformy Azure są wybierane dla poszczególnych regionów i nie można ich definiować na usługę publiczną.

Po skonfigurowaniu komunikacji równorzędnej firmy Microsoft w obwodzie usługi ExpressRoute wszystkie prefiksy związane z tymi usługami są anonsowane za pomocą ustanowionych przez siebie sesji protokołu BGP. Wartość atrybutu Community protokołu BGP jest dołączana do każdego prefiksu w celu zidentyfikowania usługi oferowanej za pośrednictwem prefiksu. Aby zapoznać się z listą wartości społeczności BGP i usług, do których są mapowane, zobacz [społeczności protokołu BGP](expressroute-routing.md#bgp).

Łączność z wszystkimi usługami platformy Azure i Microsoft 365 powoduje, że wiele prefiksów jest anonsowanych za pośrednictwem protokołu BGP. Duża liczba prefiksów znacząco zwiększa rozmiar tabel tras przechowywanych przez routery w sieci. Jeśli planujesz korzystanie tylko z podzestawu usług oferowanych za pomocą komunikacji równorzędnej firmy Microsoft, możesz zmniejszyć rozmiar tabel tras na dwa sposoby. Oto co możesz zrobić:

* Odfiltruj niechciane prefiksy, stosując filtry tras dla społeczności protokołu BGP. Filtrowanie tras jest standardową metodą sieciową i jest używane często w wielu sieciach.

* Zdefiniuj filtry tras i zastosuj je do obwodu ExpressRoute. Filtr tras to nowy zasób, który pozwala wybrać listę usług, które mają być używane przez komunikację równorzędną firmy Microsoft. Routery ExpressRoute wysyłają tylko listę prefiksów należących do usług określonych w filtrze tras.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> - Pobierz wartości społeczności BGP.
> - Utwórz filtr tras i regułę filtrowania.
> - Skojarz filtr trasy z obwodem ExpressRoute.

### <a name="about-route-filters"></a><a name="about"></a>Filtry tras — informacje

Po skonfigurowaniu komunikacji równorzędnej firmy Microsoft w obwodzie usługi ExpressRoute routery Microsoft Edge ustanawiają parę sesji protokołu BGP z routerami brzegowymi za pośrednictwem dostawcy łączności. Żadne trasy nie są ogłaszane w sieci. Aby włączyć ogłaszanie tras w sieci, należy skojarzyć filtr tras.

Filtr tras umożliwia zidentyfikowanie usług, które mają być używane za pośrednictwem komunikacji równorzędnej firmy Microsoft w ramach obwodu usługi ExpressRoute. Zasadniczo jest to lista dozwolonych wszystkich wartości społeczności protokołu BGP. Po zdefiniowaniu i dołączeniu zasobu filtru trasy do obwodu ExpressRoute wszystkie prefiksy mapowane na wartości społeczności BGP są anonsowane w sieci.

Aby dołączyć filtry tras za pomocą usług Microsoft 365, musisz mieć autoryzację do korzystania z usług Microsoft 365 Services za pomocą ExpressRoute. Jeśli nie masz uprawnień do korzystania z usług Microsoft 365 Services za pomocą ExpressRoute, operacja dołączania filtrów trasy kończy się niepowodzeniem. Aby uzyskać więcej informacji na temat procesu autoryzacji, zobacz [Azure ExpressRoute for Microsoft 365](/microsoft-365/enterprise/azure-expressroute).

> [!IMPORTANT]
> Komunikacja równorzędna firmy Microsoft ze obwodów usługi ExpressRoute, które zostały skonfigurowane przed 1 sierpnia 2017, będzie miała wszystkie prefiksy usług anonsowane za pomocą komunikacji równorzędnej firmy Microsoft, nawet jeśli filtry tras nie są zdefiniowane. Komunikacja równorzędna firmy Microsoft obwodów usługi ExpressRoute, które są skonfigurowane w dniu lub po 1 sierpnia 2017, nie będzie miała żadnych prefiksów anonsowanych do momentu dołączenia do obwodu filtru tras.
> 
## <a name="prerequisites"></a>Wymagania wstępne

- Przed rozpoczęciem konfiguracji Przejrzyj [wymagania wstępne](expressroute-prerequisites.md) i [przepływy pracy](expressroute-workflows.md) .

- Musisz mieć aktywny obwód usługi ExpressRoute z obsługą komunikacji równorzędnej firmy Microsoft. Aby wykonać te zadania, można użyć następujących instrukcji:
  - Przed kontynuowaniem [Utwórz obwód usługi ExpressRoute](expressroute-howto-circuit-arm.md) i włącz go przez dostawcę połączenia. Obwód ExpressRoute musi być w stanie aprowizacji i włączony.
  - [Utwórz komunikację równorzędną firmy Microsoft](expressroute-circuit-peerings.md) , Jeśli zarządzasz sesją BGP bezpośrednio. Lub mieć dostawcę połączenia, który inicjuje komunikację równorzędną firmy Microsoft dla obwodu.
  
[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Zaloguj się do konta platformy Azure i wybierz swoją subskrypcję

[!INCLUDE [sign in](../../includes/expressroute-cloud-shell-connect.md)]

## <a name="get-a-list-of-prefixes-and-bgp-community-values"></a><a name="prefixes"></a> Pobierz listę prefiksów i wartości społeczności BGP

1. Użyj następującego polecenia cmdlet, aby uzyskać listę wartości społeczności protokołu BGP i prefiksów skojarzonych z usługami dostępnymi za pomocą komunikacji równorzędnej firmy Microsoft:

    ```azurepowershell-interactive
    Get-AzBgpServiceCommunity
    ```

1. Utwórz listę wartości społeczności protokołu BGP, które mają być używane w filtrze tras.

## <a name="create-a-route-filter-and-a-filter-rule"></a><a name="filter"></a>Tworzenie filtru tras i reguły filtrowania

Filtr trasy może mieć tylko jedną regułę, a reguła musi być typu "Zezwalaj". Ta reguła może zawierać listę skojarzonych z nią wartości społeczności BGP. Polecenie `az network route-filter create` tworzy tylko zasób filtru tras. Po utworzeniu zasobu należy utworzyć regułę i dołączyć ją do obiektu filtr trasy.

1. Aby utworzyć zasób filtru tras, uruchom następujące polecenie:

    ```azurepowershell-interactive
    New-AzRouteFilter -Name "MyRouteFilter" -ResourceGroupName "MyResourceGroup" -Location "West US"
    ```

1. Aby utworzyć regułę filtru tras, uruchom następujące polecenie:
 
    ```azurepowershell-interactive
    $rule = New-AzRouteFilterRuleConfig -Name "Allow-EXO-D365" -Access Allow -RouteFilterRuleType Community -CommunityList 12076:5010,12076:5040
    ```

1. Uruchom następujące polecenie, aby dodać regułę filtru do filtru trasy:
 
    ```azurepowershell-interactive
    $routefilter = Get-AzRouteFilter -Name "RouteFilterName" -ResourceGroupName "ExpressRouteResourceGroupName"
    $routefilter.Rules.Add($rule)
    Set-AzRouteFilter -RouteFilter $routefilter
    ```

## <a name="attach-the-route-filter-to-an-expressroute-circuit"></a><a name="attach"></a>Dołącz filtr trasy do obwodu ExpressRoute

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

Jeśli filtr trasy jest już dołączony do obwodu, aktualizacje na liście społeczność protokołu BGP automatycznie propagują zmiany anonsów prefiksu w ramach ustanowionej sesji protokołu BGP. Listę społeczności protokołu BGP filtru tras można zaktualizować za pomocą następującego polecenia:

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

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Filtr trasy można usunąć tylko wtedy, gdy nie jest on dołączony do żadnego obwodu. Przed próbą usunięcia filtru tras upewnij się, że nie jest on dołączony do żadnego obwodu. Filtr tras można usunąć za pomocą następującego polecenia:

```azurepowershell-interactive
Remove-AzRouteFilter -Name "MyRouteFilter" -ResourceGroupName "MyResourceGroup"
```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać informacje na temat przykładów konfiguracji routera, zobacz:

> [!div class="nextstepaction"]
> [Przykłady konfiguracji routera umożliwiające skonfigurowanie routingu i zarządzanie nim](expressroute-config-samples-routing.md)
