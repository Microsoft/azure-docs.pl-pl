---
title: 'Samouczek: Konfigurowanie filtrów tras dla komunikacji równorzędnej firmy Microsoft — interfejs wiersza polecenia platformy Azure'
description: W tym samouczku opisano sposób konfigurowania filtrów tras dla komunikacji równorzędnej firmy Microsoft przy użyciu interfejsu wiersza polecenia platformy Azure.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: tutorial
ms.date: 10/08/2020
ms.author: duau
ms.custom: devx-track-azurecli
ms.openlocfilehash: ac7fc5af21f11699331d41a074e88ae757170664
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91975998"
---
# <a name="tutorial-configure-route-filters-for-microsoft-peering-azure-cli"></a>Samouczek: Konfigurowanie filtrów tras dla komunikacji równorzędnej firmy Microsoft: interfejs wiersza polecenia platformy Azure

> [!div class="op_single_selector"]
> * [Azure Portal](how-to-routefilter-portal.md)
> * [Azure PowerShell](how-to-routefilter-powershell.md)
> * [Interfejs wiersza polecenia platformy Azure](how-to-routefilter-cli.md)
> 

Filtry tras to sposób na korzystanie z podzestawu obsługiwanych usług przy użyciu komunikacji równorzędnej firmy Microsoft. Kroki opisane w tym artykule ułatwiają konfigurowanie filtrów tras dla obwodów usługi ExpressRoute i zarządzanie nimi.

Usługi Microsoft 365, takie jak Exchange Online, SharePoint Online i Skype dla firm, są dostępne za pomocą komunikacji równorzędnej firmy Microsoft. Po skonfigurowaniu komunikacji równorzędnej firmy Microsoft w obwodzie usługi ExpressRoute wszystkie prefiksy związane z tymi usługami są anonsowane za pomocą ustanowionych przez siebie sesji protokołu BGP. Wartość atrybutu Community protokołu BGP jest dołączana do każdego prefiksu w celu zidentyfikowania usługi oferowanej za pośrednictwem prefiksu. Aby zapoznać się z listą wartości społeczności BGP i usług, do których są mapowane, zobacz [społeczności protokołu BGP](expressroute-routing.md#bgp).

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

Aby pomyślnie nawiązać połączenie z usługami za pomocą komunikacji równorzędnej firmy Microsoft, należy wykonać następujące czynności konfiguracyjne:

* Musisz mieć aktywny obwód usługi ExpressRoute z obsługą komunikacji równorzędnej firmy Microsoft. Aby wykonać te zadania, można użyć następujących instrukcji:
  * Przed kontynuowaniem [Utwórz obwód usługi ExpressRoute](howto-circuit-cli.md) i włącz go przez dostawcę połączenia. Obwód ExpressRoute musi być w stanie aprowizacji i włączony.
  * [Utwórz komunikację równorzędną firmy Microsoft](howto-routing-cli.md) , Jeśli zarządzasz sesją BGP bezpośrednio. Lub mieć dostawcę połączenia, który inicjuje komunikację równorzędną firmy Microsoft dla obwodu.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten przewodnik Szybki Start będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.0.28 lub nowszej. Aby dowiedzieć się, jaka wersja jest używana, uruchom polecenie `az --version`. Jeśli konieczna będzie instalacja lub uaktualnienie interfejsu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli).

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Zaloguj się do konta platformy Azure i wybierz swoją subskrypcję

Aby rozpocząć konfigurację, zaloguj się do konta platformy Azure. Jeśli używasz "try", nastąpi automatyczne logowanie i można pominąć krok logowania. Poniższe przykłady ułatwiają nawiązanie połączenia:

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

## <a name="get-a-list-of-prefixes-and-bgp-community-values"></a><a name="prefixes"></a>Pobierz listę prefiksów i wartości społeczności BGP

1. Użyj następującego polecenia cmdlet, aby uzyskać listę wartości społeczności protokołu BGP i prefiksów skojarzonych z usługami dostępnymi za pomocą komunikacji równorzędnej firmy Microsoft:

    ```azurecli-interactive
    az network route-filter rule list-service-communities
    ```

1. Utwórz listę wartości społeczności protokołu BGP, które mają być używane w filtrze tras.

## <a name="create-a-route-filter-and-a-filter-rule"></a><a name="filter"></a>Tworzenie filtru tras i reguły filtrowania

Filtr trasy może mieć tylko jedną regułę, a reguła musi być typu "Zezwalaj". Ta reguła może zawierać listę skojarzonych z nią wartości społeczności BGP. Polecenie `az network route-filter create` tworzy tylko zasób filtru tras. Po utworzeniu zasobu należy utworzyć regułę i dołączyć ją do obiektu filtr trasy.

1. Aby utworzyć zasób filtru tras, uruchom następujące polecenie:

    ```azurecli-interactive
    az network route-filter create -n MyRouteFilter -g MyResourceGroup
    ```

1. Aby utworzyć regułę filtru tras, uruchom następujące polecenie:
 
    ```azurecli-interactive
    az network route-filter rule create --filter-name MyRouteFilter -n CRM --communities 12076:5040 --access Allow -g MyResourceGroup
    ```

## <a name="attach-the-route-filter-to-an-expressroute-circuit"></a><a name="attach"></a>Dołącz filtr trasy do obwodu ExpressRoute

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

Jeśli filtr trasy jest już dołączony do obwodu, aktualizacje na liście społeczność protokołu BGP automatycznie propagują zmiany anonsów prefiksu w ramach ustanowionej sesji protokołu BGP. Listę społeczności protokołu BGP filtru tras można zaktualizować za pomocą następującego polecenia:

```azurecli-interactive
az network route-filter rule update --filter-name MyRouteFilter -n CRM -g ExpressRouteResourceGroupName --add communities '12076:5040' --add communities '12076:5010'
```

### <a name="to-detach-a-route-filter-from-an-expressroute-circuit"></a><a name="detach"></a>Aby odłączyć filtr trasy z obwodu usługi ExpressRoute

Gdy filtr trasy zostanie odłączony od obwodu usługi ExpressRoute, żadne prefiksy nie są anonsowane przez sesję protokołu BGP. Filtr tras można odłączyć od obwodu usługi ExpressRoute przy użyciu następującego polecenia:

```azurecli-interactive
az network express-route peering update --circuit-name MyCircuit -g ExpressRouteResourceGroupName --name MicrosoftPeering --remove routeFilter
```

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Filtr trasy można usunąć tylko wtedy, gdy nie jest on dołączony do żadnego obwodu. Przed próbą usunięcia filtru tras upewnij się, że nie jest on dołączony do żadnego obwodu. Filtr tras można usunąć za pomocą następującego polecenia:

```azurecli-interactive
az network route-filter delete -n MyRouteFilter -g MyResourceGroup
```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać informacje na temat przykładów konfiguracji routera, zobacz:

> [!div class="nextstepaction"]
> [Przykłady konfiguracji routera umożliwiające skonfigurowanie routingu i zarządzanie nim](expressroute-config-samples-routing.md)
