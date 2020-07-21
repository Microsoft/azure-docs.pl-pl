---
title: Informacje o uaktualnianiu przepustowości obwodu | Azure ExpressRoute
description: W tym artykule przedstawiono najlepsze rozwiązania dotyczące uaktualniania przepustowości obwodu usługi ExpressRoute
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 07/07/2020
ms.author: cherylmc
ms.openlocfilehash: a8f5aaa7b2a054aa31198779414387cebf0f0fbd
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/20/2020
ms.locfileid: "86537041"
---
# <a name="about-upgrading-expressroute-circuit-bandwidth"></a>Informacje o uaktualnianiu przepustowości obwodu ExpressRoute

ExpressRoute umożliwia korzystanie z dedykowanych i prywatnych połączeń z siecią globalną firmy Microsoft. Łączność jest zapewniana przez sieć partnera ExpressRoute lub bezpośrednie połączenie z urządzeniami Microsoft Enterprise Edge (MSEE). Po skonfigurowaniu i przetestowaniu łączności fizycznej można włączyć połączenie warstwy 2 i warstwy 3 przez utworzenie obwodu usługi ExpressRoute i skonfigurowanie komunikacji równorzędnej.

## <a name="upgrade-circuit-bandwidth"></a><a name="upgrade"></a>Przepustowość obwodu uaktualnienia

Aby można było uaktualnić przepustowość obwodu, partner ExpressRoute Direct lub ExpressRoute musi mieć [wystarczającą dostępną przepustowość](#considerations) , aby uaktualnienie zakończyło się pomyślnie.

Jeśli pojemność jest dostępna, można uaktualnić obwód przy użyciu następujących metod:

* [Witryna Azure Portal](expressroute-howto-circuit-portal-resource-manager.md#modify)
* [Program PowerShell](expressroute-howto-circuit-arm.md#modify)
* [Interfejs wiersza polecenia platformy Azure](howto-circuit-cli.md#modify)

## <a name="capacity-considerations"></a><a name="considerations"></a>Zagadnienia dotyczące pojemności

### <a name="insufficient-expressroute-partner-bandwidth"></a><a name="bandwidth"></a>Niewystarczająca przepustowość ExpressRoute partnera

Jeśli partner ExpressRoute nie ma wystarczającej wydajności, należy utworzyć nowy obwód skonfigurowany do odpowiedniej przepustowości. W celu zapewnienia łączności nie należy usuwać starego obwodu do momentu, gdy nowo utworzony obwód nie zostanie zainicjowany, Komunikacja równorzędna została skonfigurowana i (w przypadku prywatnej komunikacji równorzędnej) obiekt połączenia z bramą sieci wirtualnej ExpressRoute został zainicjowany.

Jeśli partner ExpressRoute nie ma wystarczającej dostępnej pojemności, musisz zażądać dodatkowej pojemności w odpowiedniej lokalizacji komunikacji równorzędnej. Po udostępnieniu nowej pojemności można wykonać czynności zawarte w artykułach z sekcji wydajność [obwodu uaktualnienia](#upgrade) w celu utworzenia nowego obwodu, skonfigurowania połączenia i usunięcia starego obwodu.


### <a name="insufficient-expressroute-direct-bandwidth"></a><a name="bandwidth"></a>Niewystarczająca przepustowość ExpressRoute Direct

Jeśli usługa ExpressRoute Direct nie ma wystarczającej wydajności, można usunąć obwody skojarzone z zasobem bezpośredniego ExpressRoute, które nie są już potrzebne, lub utworzyć nowy zasób ExpressRoute Direct. Aby uzyskać wskazówki dotyczące zarządzania zasobem ExpressRoute Direct, zobacz [jak skonfigurować ExpressRoute Direct](how-to-expressroute-direct-portal.md).

## <a name="next-steps"></a>Następne kroki

* [Tworzenie i modyfikowanie obwodu](expressroute-howto-circuit-portal-resource-manager.md)
* [Tworzenie i modyfikowanie konfiguracji komunikacji równorzędnej](expressroute-howto-routing-portal-resource-manager.md)
* [Łączenie sieci wirtualnej z obwodem usługi ExpressRoute](expressroute-howto-linkvnet-portal-resource-manager.md)
