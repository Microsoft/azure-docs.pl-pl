---
title: 'Samouczek: Konfigurowanie filtrów tras dla komunikacji równorzędnej firmy Microsoft — Azure Portal'
description: W tym samouczku opisano sposób konfigurowania filtrów tras dla komunikacji równorzędnej firmy Microsoft przy użyciu Azure Portal.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: tutorial
ms.date: 10/15/2020
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: d142028d197f9e279b5f1e05757946dc40d2c153
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/16/2020
ms.locfileid: "92109140"
---
# <a name="tutorial-configure-route-filters-for-microsoft-peering-using-the-azure-portal"></a>Samouczek: Konfigurowanie filtrów tras dla komunikacji równorzędnej firmy Microsoft przy użyciu Azure Portal

> [!div class="op_single_selector"]
> * [Azure Portal](how-to-routefilter-portal.md)
> * [Azure PowerShell](how-to-routefilter-powershell.md)
> * [Interfejs wiersza polecenia platformy Azure](how-to-routefilter-cli.md)
> 

Filtry tras to sposób na korzystanie z podzestawu obsługiwanych usług przy użyciu komunikacji równorzędnej firmy Microsoft. Kroki opisane w tym artykule ułatwiają konfigurowanie filtrów tras dla obwodów usługi ExpressRoute i zarządzanie nimi.

Usługi Microsoft 365, takie jak Exchange Online, SharePoint Online i Skype dla firm, są dostępne za pomocą komunikacji równorzędnej firmy Microsoft. Po skonfigurowaniu komunikacji równorzędnej firmy Microsoft w obwodzie usługi ExpressRoute wszystkie prefiksy związane z tymi usługami są anonsowane za pomocą ustanowionych przez siebie sesji protokołu BGP. Wartość atrybutu Community protokołu BGP jest dołączana do każdego prefiksu w celu zidentyfikowania usługi oferowanej za pośrednictwem prefiksu. Aby zapoznać się z listą wartości społeczności BGP i usług, do których są mapowane, zobacz [społeczności protokołu BGP](expressroute-routing.md#bgp).

Łączność z wszystkimi usługami platformy Azure i Microsoft 365 powoduje, że wiele prefiksów jest anonsowanych za pośrednictwem protokołu BGP. Duża liczba prefiksów znacząco zwiększa rozmiar tabel tras przechowywanych przez routery w sieci. Jeśli planujesz korzystanie tylko z podzestawu usług oferowanych za pomocą komunikacji równorzędnej firmy Microsoft, możesz zmniejszyć rozmiar tabel tras na dwa sposoby. Można:

* Odfiltruj niechciane prefiksy, stosując filtry tras dla społeczności protokołu BGP. Filtrowanie tras jest standardową metodą sieciową i jest używane często w wielu sieciach.

* Zdefiniuj filtry tras i zastosuj je do obwodu ExpressRoute. Filtr tras to nowy zasób, który pozwala wybrać listę usług, które mają być używane przez komunikację równorzędną firmy Microsoft. Routery ExpressRoute wysyłają tylko listę prefiksów należących do usług określonych w filtrze tras.

Z tego samouczka dowiesz się, jak wykonywać następujące czynności:
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
  - Przed kontynuowaniem [Utwórz obwód usługi ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md) i włącz go przez dostawcę połączenia. Obwód ExpressRoute musi być w stanie aprowizacji i włączony.
  - [Utwórz komunikację równorzędną firmy Microsoft](expressroute-howto-routing-portal-resource-manager.md) , Jeśli zarządzasz sesją BGP bezpośrednio. Lub mieć dostawcę połączenia, który inicjuje komunikację równorzędną firmy Microsoft dla obwodu.

## <a name="get-a-list-of-prefixes-and-bgp-community-values"></a><a name="prefixes"></a>Pobierz listę prefiksów i wartości społeczności BGP

### <a name="get-a-list-of-bgp-community-values"></a>Pobierz listę wartości społeczności BGP

Wartości społeczności protokołu BGP skojarzone z usługami dostępnymi za pomocą komunikacji równorzędnej firmy Microsoft są dostępne na stronie [wymagania dotyczące routingu ExpressRoute](expressroute-routing.md) .

### <a name="make-a-list-of-the-values-that-you-want-to-use"></a>Utwórz listę wartości, które mają być używane

Utwórz listę [wartości społeczności protokołu BGP](expressroute-routing.md#bgp) , które mają być używane w filtrze tras. 

## <a name="create-a-route-filter-and-a-filter-rule"></a><a name="filter"></a>Tworzenie filtru tras i reguły filtrowania

Filtr trasy może mieć tylko jedną regułę, a reguła musi być typu "Zezwalaj". Ta reguła może zawierać listę skojarzonych z nią wartości społeczności BGP.

1. Wybierz pozycję **Utwórz zasób** , a następnie wyszukaj *Filtr trasy* , jak pokazano na poniższej ilustracji:

    :::image type="content" source="./media/how-to-routefilter-portal/create-route-filter.png" alt-text="Zrzut ekranu przedstawiający stronę filtru trasy":::

1. Umieść filtr trasy w grupie zasobów. Upewnij się, że lokalizacja jest taka sama jak obwód ExpressRoute. Wybierz kolejno pozycje **Przegląd + Utwórz** i **Utwórz**.

    :::image type="content" source="./media/how-to-routefilter-portal/create-route-filter-basic.png" alt-text="Zrzut ekranu przedstawiający stronę filtru trasy":::

### <a name="create-a-filter-rule"></a>Utwórz regułę filtru

1. Aby dodać i zaktualizować reguły, wybierz kartę Zarządzanie regułami dla filtru tras.

    :::image type="content" source="./media/how-to-routefilter-portal/manage-route-filter.png" alt-text="Zrzut ekranu przedstawiający stronę filtru trasy":::

1. Wybierz z listy rozwijanej usługi, z którymi chcesz nawiązać połączenie i Zapisz regułę po zakończeniu.

    :::image type="content" source="./media/how-to-routefilter-portal/add-route-filter-rule.png" alt-text="Zrzut ekranu przedstawiający stronę filtru trasy":::

## <a name="attach-the-route-filter-to-an-expressroute-circuit"></a><a name="attach"></a>Dołącz filtr trasy do obwodu ExpressRoute

Dołącz filtr trasy do obwodu, wybierając przycisk **+ Dodaj obwód** i wybierając obwód ExpressRoute z listy rozwijanej.

:::image type="content" source="./media/how-to-routefilter-portal/add-circuit-to-route-filter.png" alt-text="Zrzut ekranu przedstawiający stronę filtru trasy":::

Jeśli dostawca łączności konfiguruje komunikację równorzędną dla obwodu usługi ExpressRoute, Odśwież obwód ze strony obwodu ExpressRoute przed wybraniem przycisku **+ Dodaj obwód** .

:::image type="content" source="./media/how-to-routefilter-portal/refresh-express-route-circuit.png" alt-text="Zrzut ekranu przedstawiający stronę filtru trasy":::

## <a name="common-tasks"></a><a name="tasks"></a>Typowe zadania

### <a name="to-get-the-properties-of-a-route-filter"></a><a name="getproperties"></a>Aby uzyskać właściwości filtru tras

Właściwości filtru tras można wyświetlić po otwarciu zasobu w portalu.

:::image type="content" source="./media/how-to-routefilter-portal/view-route-filter.png" alt-text="Zrzut ekranu przedstawiający stronę filtru trasy":::

### <a name="to-update-the-properties-of-a-route-filter"></a><a name="updateproperties"></a>Aby zaktualizować właściwości filtru tras

1. Możesz zaktualizować listę wartości społeczności BGP dołączanych do obwodu, wybierając przycisk **Zarządzaj regułą** .

    :::image type="content" source="./media/how-to-routefilter-portal/update-route-filter.png" alt-text="Zrzut ekranu przedstawiający stronę filtru trasy":::

1. Wybierz odpowiednie społeczności usługi, a następnie wybierz pozycję **Zapisz**.

    :::image type="content" source="./media/how-to-routefilter-portal/add-route-filter-rule.png" alt-text="Zrzut ekranu przedstawiający stronę filtru trasy":::

### <a name="to-detach-a-route-filter-from-an-expressroute-circuit"></a><a name="detach"></a>Aby odłączyć filtr trasy z obwodu usługi ExpressRoute

Aby odłączyć obwód od filtra trasy, kliknij prawym przyciskiem myszy obwód i wybierz polecenie **Usuń skojarzenie**.

:::image type="content" source="./media/how-to-routefilter-portal/detach-route-filter.png" alt-text="Zrzut ekranu przedstawiający stronę filtru trasy":::


## <a name="clean-up-resources"></a>Czyszczenie zasobów

Filtr tras można usunąć, wybierając przycisk **Usuń** . Upewnij się, że filtr trasy nie jest skojarzony z żadnym obwodem przed wykonaniem tej operacji.

:::image type="content" source="./media/how-to-routefilter-portal/delete-route-filter.png" alt-text="Zrzut ekranu przedstawiający stronę filtru trasy":::

## <a name="next-steps"></a>Następne kroki

Aby uzyskać informacje na temat przykładów konfiguracji routera, zobacz:

> [!div class="nextstepaction"]
> [Przykłady konfiguracji routera umożliwiające skonfigurowanie routingu i zarządzanie nim](expressroute-config-samples-routing.md)
