---
title: 'Wyświetlanie efektywnych tras koncentratora wirtualnego: Azure Virtual WAN | Microsoft Docs'
description: Jak wyświetlić efektywne trasy dla koncentratora wirtualnego w wirtualnej sieci WAN platformy Azure
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: cherylmc
ms.openlocfilehash: ad487aa7492abcf02988c78ccfa2ba7cd4798249
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "90983643"
---
# <a name="view-virtual-hub-effective-routes"></a>Wyświetlanie obowiązujących tras koncentratora wirtualnego

Wszystkie trasy wirtualnego koncentratora sieci WAN można wyświetlić w Azure Portal. W tym artykule przedstawiono kroki umożliwiające wyświetlenie obowiązujących tras. Aby uzyskać więcej informacji na temat routingu koncentratorów wirtualnych, zobacz [Informacje o routingu koncentratora wirtualnego](about-virtual-hub-routing.md).

## <a name="select-connections-or-route-tables"></a><a name="routing"></a>Wybieranie połączeń lub tabel tras

1. Przejdź do centrum wirtualnego, a następnie wybierz pozycję **Routing**. Na stronie Routing wybierz pozycję **efektywne trasy**.
1. Z listy rozwijanej można wybrać opcję **tabela tras**. Jeśli nie widzisz opcji tabeli tras, oznacza to, że nie masz skonfigurowanej tabeli tras niestandardowych lub domyślnych w tym koncentratorze wirtualnym.

## <a name="view-output"></a><a name="output"></a>Wyświetl dane wyjściowe

Na stronie dane wyjściowe są wyświetlane następujące pola:

* **Prefiks**: prefiks adresu znany bieżącemu obiektowi (informacje z routera koncentratora wirtualnego)
* **Typ następnego przeskoku**: może być Virtual Network połączenie, VPN_S2S_Gateway, ExpressRouteGateway, koncentrator zdalny lub Zapora platformy Azure.
* **Następny przeskok**: to jest link do identyfikatora zasobu następnego przeskoku lub po prostu zawiera link do bieżącego centrum.
* **Źródło**: Identyfikator zasobu źródła routingu.
* **Ścieżka as**: ATRYBUT BGP AS (system autonomiczny) wyświetla listę wszystkich numerów AS, które muszą zostać przesunięte w celu uzyskania dostępu do lokalizacji, do której jest dołączona ścieżka, jest anonsowana z.

### <a name="example"></a><a name="example"></a>Przyklad

Wartości w poniższej tabeli przykładowej wykazują, że połączenie koncentratora wirtualnego lub tabela tras poznania trasy 10.2.0.0/24 (prefiks gałęzi). Poznasz trasę z powodu **typu następnego przeskoku sieci VPN** VPN_S2S_Gateway z **następnym przeskokiem** VPN Gateway identyfikator zasobu. **Kierowanie punktów początkowych** do identyfikatora zasobu w tabeli/połączeniu z BRAMą VPN/trasą. **Ścieżka as** wskazuje ścieżkę as dla gałęzi.

Użyj paska przewijania u dołu tabeli, aby wyświetlić ścieżkę "AS".

| **Prefiks** |  **Typ następnego przeskoku** | **Narzędzie Następny przeskok** |  **Źródło trasy** |**Ścieżka AS** |
| ---        | ---                | ---          | ---               | ---         |
| 10.2.0.0/24| VPN_S2S_Gateway |/subscriptions/ `<sub id>` /ResourceGroups/ `<resource group name>` /providers/Microsoft.Network/vpnGateways/vpngw|/subscriptions/ `<sub id>` /ResourceGroups/ `<resource group name>` /providers/Microsoft.Network/vpnGateways/vpngw| 20000|

**Zagadnienia dotyczące**

* Jeśli widzisz wartość 0.0.0.0/0 w danych wyjściowych **uzyskiwania efektywnej trasy** , oznacza to, że trasa istnieje w jednej z tabel tras. Jeśli jednak ta trasa została skonfigurowana dla Internetu, w połączeniu wymagana jest dodatkowa flaga **"enableInternetSecurity": true** . W skutecznej trasie na karcie sieciowej maszyny wirtualnej nie będzie wyświetlana trasa, jeśli flaga "enableInternetSecurity" połączenia ma wartość "false".

* W przypadku edycji połączenia sieci wirtualnej, połączenia sieci VPN lub połączenia ExpressRoute pojawia się pole **Propaguj trasę domyślną** . To pole wskazuje flagę **enableInternetSecurity** , która jest zawsze domyślnie "false" dla połączeń EXPRESSROUTE i VPN, ale "true" dla połączeń sieci wirtualnej.

* Przy przeglądaniu efektywnych tras na karcie sieciowej maszyny wirtualnej, jeśli następnym przeskokiem jest "Virtual Network Gateway", który implikuje router koncentratora wirtualnego, gdy maszyna wirtualna jest połączona z koncentratorem wirtualnej sieci WAN.

* Rzeczywiste trasy dla tabeli tras koncentratora wirtualnego są wypełniane tylko wtedy, gdy koncentrator wirtualny ma podłączoną co najmniej jeden typ połączenia (VPN/ER/Sieć wirtualna).

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać więcej informacji na temat usługi Virtual WAN, zobacz [Omówienie usługi Virtual WAN](virtual-wan-about.md).
* Aby uzyskać więcej informacji na temat routingu koncentratorów wirtualnych, zobacz [Informacje o routingu koncentratora wirtualnego](about-virtual-hub-routing.md).
