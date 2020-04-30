---
title: 'Azure ExpressRoute: łączenie się z Microsoft Cloud przy użyciu Global Reach'
description: W tym artykule opisano Global Reach ExpressRoute.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 04/24/2020
ms.author: cherylmc
ms.openlocfilehash: 4c326a556530fd778c1178f11b79d2aba613b4c1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82136587"
---
# <a name="expressroute-global-reach"></a>ExpressRoute Global Reach
ExpressRoute to prywatny i odporny na podłączenie sieci lokalnych do Microsoft Cloud. Możesz uzyskać dostęp do wielu usług w chmurze firmy Microsoft, takich jak Azure, i pakietu Office 365 z prywatnego centrum danych lub sieci firmowej. Na przykład możesz mieć oddział w sieci San Francisco z obwodem usługi ExpressRoute w Dolina krzemu i innym biurze oddziału w Londynie z obwodem usługi ExpressRoute w tym samym mieście. Obie biura oddziałów mogą mieć dużą szybkość łączności z zasobami platformy Azure w regionie zachodnie stany USA i Południowe Zjednoczone Królestwo. Oddziały nie mogą jednak wymieniać danych bezpośrednio. Innymi słowy, 10.0.1.0/24 może wysyłać dane do 10.0.3.0/24 i 10.0.4.0/24, ale nie do 10.0.2.0/24.

![konieczności][1]

Program **ExpressRoute Global REACH**umożliwia łączenie obwodów ExpressRoute ze sobą w celu naprowadzenia prywatnej sieci między sieciami lokalnymi. W powyższym przykładzie, przy dodawaniu ExpressRoute Global Reach, Sieć San Francisco Office (10.0.1.0/24) może bezpośrednio wymieniać dane z biurem w Londynie (10.0.2.0/24) za pośrednictwem istniejących obwodów usługi ExpressRoute i sieci globalnej firmy Microsoft. 

![się][2]

## <a name="use-case"></a>Przypadek użycia
ExpressRoute Global Reach zaprojektowano w celu uzupełnienia implementacji sieci WAN dostawcy usług i łączenia biur oddziałów na całym świecie. Na przykład, jeśli dostawca usług działa głównie w Stany Zjednoczone i łączy wszystkie gałęzie w Stanach Zjednoczonych, ale dostawca usług nie działa w Japonii i Hong Kongu, z ExpressRoute Global Reach można pracować z lokalnym dostawcą usług, a firma Microsoft łączy Twoje gałęzie w Stanach Zjednoczonych przy użyciu ExpressRoute i naszej sieci globalnej.

![przypadek użycia][3]

## <a name="availability"></a>Dostępność 
ExpressRoute Global Reach obecnie jest obsługiwana w następujących miejscach.

* Australia
* Kanada
* Francja
* Niemcy
* SRA Hongkong
* Irlandia
* Japonia
* Korea
* Holandia
* Nowa Zelandia
* Singapur
* Szwajcaria
* Wielka Brytania
* Stany Zjednoczone

Obwody usługi ExpressRoute muszą zostać utworzone w [lokalizacjach komunikacji równorzędnej ExpressRoute](expressroute-locations.md) w powyższych krajach lub regionach. Aby umożliwić ExpressRoute Global Reach między [różnymi regionami geopolitycznych](expressroute-locations.md), obwody muszą być jednostkami SKU w warstwie Premium.

## <a name="next-steps"></a>Następne kroki
1. [Wyświetlanie Global Reach często zadawanych pytań](expressroute-faqs.md#globalreach)
2. [Dowiedz się, jak włączyć Global Reach](expressroute-howto-set-global-reach.md)
3. [Dowiedz się, jak połączyć obwód ExpressRoute z siecią wirtualną](expressroute-howto-linkvnet-arm.md)


<!--Image References-->
[1]: ./media/expressroute-global-reach/1.png "Diagram bez globalnego zasięgu"
[2]: ./media/expressroute-global-reach/2.png "Diagram z zasięgiem globalnym"
[3]: ./media/expressroute-global-reach/3.png "przypadek użycia globalnego zasięgu"
