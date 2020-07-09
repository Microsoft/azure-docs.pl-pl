---
title: 'Wyświetlanie efektywnych tras koncentratora wirtualnego: Azure Virtual WAN | Microsoft Docs'
description: Jak wyświetlić efektywne trasy dla koncentratora wirtualnego w wirtualnej sieci WAN platformy Azure
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 06/29/2020
ms.author: cherylmc
ms.openlocfilehash: 20cdc55b474034480392f9dfb05b20ad25df6939
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/07/2020
ms.locfileid: "86037770"
---
# <a name="view-virtual-hub-effective-routes"></a>Wyświetlanie obowiązujących tras koncentratora wirtualnego

Wszystkie trasy wirtualnego koncentratora sieci WAN można wyświetlić w Azure Portal. W tym artykule przedstawiono kroki umożliwiające wyświetlenie obowiązujących tras. Aby uzyskać więcej informacji na temat routingu koncentratorów wirtualnych, zobacz [Informacje o routingu koncentratora wirtualnego](about-virtual-hub-routing.md).

> [!NOTE]
> Na Azure Portal niektóre z tych funkcji mogą być nadal wycofywane i niedostępne do tygodnia od 3 sierpnia. 
>

## <a name="select-connections-or-route-tables"></a><a name="routing"></a>Wybieranie połączeń lub tabel tras

1. Przejdź do centrum wirtualnego, a następnie wybierz pozycję **Routing**. Na stronie Routing wybierz pozycję **efektywne trasy**.
1. Z listy rozwijanej można wybrać **Typ połączenia** lub **tabelę tras**. Jeśli nie widzisz opcji tabeli tras, oznacza to, że nie masz skonfigurowanej tabeli tras niestandardowych lub domyślnych w tym koncentratorze wirtualnym.
1. Z listy rozwijanej dla **tabel połączeń/tras**można wybrać jedną z następujących pozycji:

   * Połączenie Virtual Network
   * Połączenie z witryną sieci VPN
   * Połączenie ExpressRoute
   * Połączenie punkt-lokacja
   * Tabela tras

   :::image type="content" source="./media/effective-routes-virtual-hub/routing.png" alt-text="Routing":::

## <a name="view-output"></a><a name="output"></a>Wyświetl dane wyjściowe

Na stronie dane wyjściowe są wyświetlane następujące pola:

* **Prefiks**: prefiks adresu znany bieżącemu obiektowi.
* **Typ następnego przeskoku**: może być Virtual Network połączenie, VPN_S2S_Gateway, ExpressRouteGateway, koncentrator zdalny lub Zapora platformy Azure.
* **Następny przeskok**: jest to adres IP lub po prostu wyświetla on-link, aby implikuje bieżące centrum.
* **Źródło**: Identyfikator zasobu źródła routingu.
* **Ścieżka as**: ATRYBUT BGP AS (system autonomiczny) wyświetla listę wszystkich numerów AS, które muszą zostać przesunięte w celu uzyskania dostępu do lokalizacji, do której jest dołączona ścieżka, jest anonsowana z.

### <a name="example"></a><a name="example"></a>Przyklad

Wartości w poniższej tabeli przykładowej wykazują, że połączenie koncentratora wirtualnego lub tabela tras poznania trasy 10.2.0.0/24 (prefiks gałęzi). Poznasz trasę z powodu **typu następnego przeskoku sieci VPN** VPN_S2S_Gateway z **następnym przeskokiem** VPN Gateway identyfikator zasobu. **Kierowanie punktów początkowych** do identyfikatora zasobu w tabeli/połączeniu z BRAMą VPN/trasą. **Ścieżka as** wskazuje ścieżkę as dla gałęzi.

Użyj paska przewijania u dołu tabeli, aby wyświetlić ścieżkę "AS".

| **Prefiks** |  **Typ następnego skoku** | **Następny przeskok** |  **Źródło trasy** |**Ścieżka AS** |
| ---        | ---                | ---          | ---               | ---         |
| 10.2.0.0/24| VPN_S2S_Gateway |10.1.0.6, 10.1.0.7|/subscriptions/ `<sub id>` /ResourceGroups/ `<resource group name>` /providers/Microsoft.Network/vpnGateways/vpngw| 20000|

**Zagadnienia dotyczące**

* Jeśli widzisz wartość 0.0.0.0/0 w danych wyjściowych **uzyskiwania efektywnej trasy** , oznacza to, że trasa istnieje w jednej z tabel tras. Jeśli jednak ta trasa została skonfigurowana dla Internetu, w połączeniu wymagana jest dodatkowa flaga **"enableInternetSecurity": true** . W skutecznej trasie na karcie sieciowej maszyny wirtualnej nie będzie wyświetlana trasa, jeśli flaga "enableInternetSecurity" połączenia ma wartość "false".

* W przypadku edycji połączenia sieci wirtualnej, połączenia sieci VPN lub połączenia ExpressRoute pojawia się pole **Propaguj trasę domyślną** . To pole wskazuje flagę **enableInternetSecurity** , która jest zawsze domyślnie "false" dla połączeń EXPRESSROUTE i VPN, ale "true" dla połączeń sieci wirtualnej.

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać więcej informacji na temat usługi Virtual WAN, zobacz [Omówienie usługi Virtual WAN](virtual-wan-about.md).
* Aby uzyskać więcej informacji na temat routingu koncentratorów wirtualnych, zobacz [Informacje o routingu koncentratora wirtualnego](about-virtual-hub-routing.md).
