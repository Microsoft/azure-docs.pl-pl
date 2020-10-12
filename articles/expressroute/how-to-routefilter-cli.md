---
title: 'ExpressRoute: filtry tras — Komunikacja równorzędna firmy Microsoft: interfejs wiersza polecenia platformy Azure'
description: W tym artykule opisano sposób konfigurowania filtrów tras dla komunikacji równorzędnej firmy Microsoft przy użyciu interfejsu wiersza polecenia platformy Azure
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 12/07/2018
ms.author: duau
ms.custom: devx-track-azurecli
ms.openlocfilehash: 8fbce15b84371b7b7907deff361e2a2e706bec28
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89567711"
---
# <a name="configure-route-filters-for-microsoft-peering-azure-cli"></a>Konfigurowanie filtrów tras dla komunikacji równorzędnej firmy Microsoft: interfejs wiersza polecenia platformy Azure

> [!div class="op_single_selector"]
> * [Azure Portal](how-to-routefilter-portal.md)
> * [Azure PowerShell](how-to-routefilter-powershell.md)
> * [Interfejs wiersza polecenia platformy Azure](how-to-routefilter-cli.md)
> 

Filtry tras to sposób na korzystanie z podzestawu obsługiwanych usług przy użyciu komunikacji równorzędnej firmy Microsoft. Kroki opisane w tym artykule ułatwiają konfigurowanie filtrów tras dla obwodów usługi ExpressRoute i zarządzanie nimi.

Usługi Microsoft 365, takie jak Exchange Online, SharePoint Online i Skype dla firm, są dostępne za pomocą komunikacji równorzędnej firmy Microsoft. W przypadku skonfigurowania komunikacji równorzędnej firmy Microsoft w obwodzie usługi ExpressRoute wszystkie prefiksy związane z tymi usługami są anonsowane za pomocą ustanowionych przez siebie sesji protokołu BGP. Wartość atrybutu Community protokołu BGP jest dołączana do każdego prefiksu w celu zidentyfikowania usługi oferowanej za pośrednictwem prefiksu. Aby zapoznać się z listą wartości społeczności BGP i usług, do których są mapowane, zobacz [społeczności protokołu BGP](expressroute-routing.md#bgp).

Jeśli wymagana jest łączność ze wszystkimi usługami, wiele prefiksów jest anonsowanych za pośrednictwem protokołu BGP. Znacznie zwiększa to rozmiar tabel tras przechowywanych przez routery w sieci. Jeśli planujesz korzystanie tylko z podzestawu usług oferowanych za pomocą komunikacji równorzędnej firmy Microsoft, możesz zmniejszyć rozmiar tabel tras na dwa sposoby. Dostępne możliwości:

* Odfiltruj niechciane prefiksy, stosując filtry tras dla społeczności protokołu BGP. Jest to standardowa metoda sieciowa i jest używana często w wielu sieciach.

* Zdefiniuj filtry tras i zastosuj je do obwodu ExpressRoute. Filtr tras to nowy zasób, który pozwala wybrać listę usług, które mają być używane przez komunikację równorzędną firmy Microsoft. Routery ExpressRoute wysyłają tylko listę prefiksów należących do usług określonych w filtrze tras.

### <a name="about-route-filters"></a><a name="about"></a>Filtry tras — informacje

W przypadku skonfigurowania komunikacji równorzędnej firmy Microsoft w obwodzie usługi ExpressRoute routery Microsoft Edge ustanawiają parę sesji protokołu BGP z routerami brzegowymi (usługodawcą). Żadne trasy nie są ogłaszane w sieci. Aby włączyć ogłaszanie tras w sieci, należy skojarzyć filtr tras.

Filtr tras umożliwia zidentyfikowanie usług, które mają być używane za pośrednictwem komunikacji równorzędnej firmy Microsoft w ramach obwodu usługi ExpressRoute. Zasadniczo jest to lista dozwolonych wszystkich wartości społeczności protokołu BGP. Gdy zasób filtru tras jest zdefiniowany i dołączony do obwodu usługi ExpressRoute, wszystkie prefiksy zamapowane do wartości atrybutu Community protokołu BGP są ogłaszane w sieci.

Aby można było dołączać filtry tras za pomocą usług Microsoft 365, musisz mieć autoryzację do korzystania z usług Microsoft 365 Services za pomocą ExpressRoute. Jeśli nie masz uprawnień do korzystania z usług Microsoft 365 za pomocą ExpressRoute, operacja dołączania filtrów trasy kończy się niepowodzeniem. Aby uzyskać więcej informacji na temat procesu autoryzacji, zobacz [Azure ExpressRoute for Microsoft 365](/microsoft-365/enterprise/azure-expressroute).

> [!IMPORTANT]
> Komunikacja równorzędna firmy Microsoft ze obwodów usługi ExpressRoute, które zostały skonfigurowane przed 1 sierpnia 2017, będzie miała wszystkie prefiksy usług anonsowane za pomocą komunikacji równorzędnej firmy Microsoft, nawet jeśli filtry tras nie są zdefiniowane. Komunikacja równorzędna firmy Microsoft obwodów usługi ExpressRoute, które są skonfigurowane w dniu lub po 1 sierpnia 2017, nie będzie miała żadnych prefiksów anonsowanych do momentu dołączenia do obwodu filtru tras.
> 
> 

### <a name="workflow"></a><a name="workflow"></a>Przepływ pracy

Aby można było pomyślnie połączyć się z usługami za pomocą komunikacji równorzędnej firmy Microsoft, należy wykonać następujące czynności konfiguracyjne:

* Musisz mieć aktywny obwód usługi ExpressRoute z obsługą komunikacji równorzędnej firmy Microsoft. Aby wykonać te zadania, można użyć następujących instrukcji:
  * [Utworzenie obwodu usługi ExpressRoute](howto-circuit-cli.md) i włączenie obwodu przez dostawcę łączności przed kontynuowaniem. Obwód ExpressRoute musi być w stanie aprowizacji i włączony.
  * [Utwórz komunikację równorzędną firmy Microsoft](howto-routing-cli.md) , Jeśli zarządzasz sesją BGP bezpośrednio. Lub mieć dostawcę połączenia, który inicjuje komunikację równorzędną firmy Microsoft dla obwodu.

* Należy utworzyć i skonfigurować filtr tras.
  * Identyfikowanie usług, z których można korzystać za pomocą komunikacji równorzędnej firmy Microsoft
  * Zidentyfikuj listę wartości społeczności BGP skojarzonych z usługami
  * Utwórz regułę zezwalającą na listę prefiksów zgodną z wartościami społeczności BGP

* Należy dołączyć filtr trasy do obwodu ExpressRoute.

## <a name="before-you-begin"></a>Zanim rozpoczniesz

Przed rozpoczęciem zainstaluj najnowszą wersję poleceń interfejsu wiersza polecenia (wersję 2.0 lub nowszą). Aby uzyskać informacje o instalowaniu poleceń interfejsu wiersza polecenia, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) i [Rozpoczynanie pracy z interfejsem wiersza polecenia platformy Azure](/cli/azure/get-started-with-azure-cli).

* Przed rozpoczęciem konfiguracji Przejrzyj [wymagania wstępne](expressroute-prerequisites.md) i [przepływy pracy](expressroute-workflows.md) .

* Musisz mieć aktywny obwód usługi ExpressRoute. Zanim przejdziesz dalej, postępuj zgodnie z instrukcjami, aby [utworzyć obwód usługi ExpressRoute](howto-circuit-cli.md), który powinien zostać włączony przez dostawcę połączenia. Obwód ExpressRoute musi być w stanie aprowizacji i włączony.

* Konieczne jest posiadanie aktywnej komunikacji równorzędnej firmy Microsoft. Postępuj zgodnie z instrukcjami w obszarze [Tworzenie i modyfikowanie konfiguracji komunikacji równorzędnej](howto-routing-cli.md)

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Zaloguj się do konta platformy Azure i wybierz swoją subskrypcję

Aby rozpocząć konfigurację, zaloguj się do konta platformy Azure. W przypadku korzystania z funkcji "Wypróbuj" użytkownik jest zalogowany automatycznie i może pominąć krok logowania. Poniższe przykłady ułatwiają nawiązanie połączenia:

```azurecli
az login
```

Sprawdź subskrypcje dostępne na koncie.

```azurecli-interactive
az account list
```

Wybierz subskrypcję, dla której chcesz utworzyć obwód usługi ExpressRoute.

```azurecli-interactive
az account set --subscription "<subscription ID>"
```

## <a name="step-1-get-a-list-of-prefixes-and-bgp-community-values"></a><a name="prefixes"></a>Krok 1. Pobieranie listy prefiksów i wartości społeczności BGP

### <a name="1-get-a-list-of-bgp-community-values"></a>1. Pobierz listę wartości społeczności BGP

Użyj poniższego polecenia cmdlet, aby uzyskać listę wartości społeczności protokołu BGP skojarzonych z usługami dostępnymi za pomocą komunikacji równorzędnej firmy Microsoft oraz listę skojarzonych z nimi prefiksów:

```azurecli-interactive
az network route-filter rule list-service-communities
```
### <a name="2-make-a-list-of-the-values-that-you-want-to-use"></a>2. Utwórz listę wartości, których chcesz użyć

Utwórz listę wartości społeczności protokołu BGP, które mają być używane w filtrze tras.

## <a name="step-2-create-a-route-filter-and-a-filter-rule"></a><a name="filter"></a>Krok 2. Tworzenie filtru tras i reguły filtrowania

Filtr trasy może mieć tylko jedną regułę, a reguła musi być typu "Zezwalaj". Ta reguła może zawierać listę skojarzonych z nią wartości społeczności BGP.

### <a name="1-create-a-route-filter"></a>1. Tworzenie filtru tras

Najpierw utwórz filtr tras. Polecenie `az network route-filter create` tworzy tylko zasób filtru tras. Po utworzeniu zasobu należy utworzyć regułę i dołączyć ją do obiektu filtr trasy. Uruchom następujące polecenie, aby utworzyć zasób filtru tras:

```azurecli-interactive
az network route-filter create -n MyRouteFilter -g MyResourceGroup
```

### <a name="2-create-a-filter-rule"></a>2. Utwórz regułę filtru

Uruchom następujące polecenie, aby utworzyć nową regułę:
 
```azurecli-interactive
az network route-filter rule create --filter-name MyRouteFilter -n CRM --communities 12076:5040 --access Allow -g MyResourceGroup
```

## <a name="step-3-attach-the-route-filter-to-an-expressroute-circuit"></a><a name="attach"></a>Krok 3. dołączanie filtru tras do obwodu ExpressRoute

Uruchom następujące polecenie, aby dołączyć filtr trasy do obwodu ExpressRoute:

```azurecli-interactive
az network express-route peering update --circuit-name MyCircuit -g ExpressRouteResourceGroupName --name MicrosoftPeering --route-filter MyRouteFilter
```

## <a name="common-tasks"></a><a name="tasks"></a>Typowe zadania

### <a name="to-get-the-properties-of-a-route-filter"></a><a name="getproperties"></a>Aby uzyskać właściwości filtru tras

Aby uzyskać właściwości filtru tras, użyj następującego polecenia:

```azurecli-interactive
az network route-filter show -g ExpressRouteResourceGroupName --name MyRouteFilter 
```

### <a name="to-update-the-properties-of-a-route-filter"></a><a name="updateproperties"></a>Aby zaktualizować właściwości filtru tras

Jeśli filtr trasy jest już dołączony do obwodu, aktualizacje na liście społeczność protokołu BGP automatycznie propagują odpowiednie zmiany anonsu prefiksu za pomocą ustanowionych sesji protokołu BGP. Listę społeczności protokołu BGP filtru tras można zaktualizować za pomocą następującego polecenia:

```azurecli-interactive
az network route-filter rule update --filter-name MyRouteFilter -n CRM -g ExpressRouteResourceGroupName --add communities '12076:5040' --add communities '12076:5010'
```

### <a name="to-detach-a-route-filter-from-an-expressroute-circuit"></a><a name="detach"></a>Aby odłączyć filtr trasy z obwodu usługi ExpressRoute

Gdy filtr trasy zostanie odłączony od obwodu usługi ExpressRoute, żadne prefiksy nie są anonsowane przez sesję protokołu BGP. Filtr tras można odłączyć od obwodu usługi ExpressRoute przy użyciu następującego polecenia:

```azurecli-interactive
az network express-route peering update --circuit-name MyCircuit -g ExpressRouteResourceGroupName --name MicrosoftPeering --remove routeFilter
```

### <a name="to-delete-a-route-filter"></a><a name="delete"></a>Aby usunąć filtr tras

Filtr trasy można usunąć tylko wtedy, gdy nie jest on dołączony do żadnego obwodu. Upewnij się, że filtr trasy nie jest dołączony do żadnego obwodu przed próbą usunięcia go. Filtr tras można usunąć za pomocą następującego polecenia:

```azurecli-interactive
az network route-filter delete -n MyRouteFilter -g MyResourceGroup
```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat ExpressRoute, zobacz [często zadawane pytania](expressroute-faqs.md)dotyczące usługi ExpressRoute.
