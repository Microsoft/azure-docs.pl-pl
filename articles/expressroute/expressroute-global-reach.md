---
title: 'Azure ExpressRoute: łączenie się z Microsoft Cloud przy użyciu Global Reach'
description: Dowiedz się, jak usługa Azure ExpressRoute Global Reach umożliwia łączenie obwodów ExpressRoute ze sobą, aby utworzyć sieć prywatną między sieciami lokalnymi.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 09/18/2020
ms.author: duau
ms.custom: references_regions
ms.openlocfilehash: e3f9105037c049a53f1b7b99da96dd857070fcc7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90987615"
---
# <a name="expressroute-global-reach"></a>ExpressRoute Global Reach
ExpressRoute to prywatny i odporny na połączenie sieci lokalnych z Microsoft Cloud. Dostęp do wielu usług w chmurze firmy Microsoft, takich jak Azure i Microsoft 365, można uzyskać z prywatnego centrum danych lub sieci firmowej. Na przykład możesz mieć oddział w sieci San Francisco z obwodem usługi ExpressRoute w Dolina krzemu i innym biurze oddziału w Londynie z obwodem usługi ExpressRoute w tym samym mieście. Obie biura oddziałów mają wysoką szybkość łączności z zasobami platformy Azure w zachodnich stanach USA i Południowe Zjednoczone Królestwo. Oddziały nie mogą jednak łączyć i wysyłać danych bezpośrednio ze sobą. Innymi słowy, 10.0.1.0/24 może wysyłać dane do sieci 10.0.3.0/24 i 10.0.4.0/24, ale nie do sieci 10.0.2.0/24.

![Diagram przedstawiający obwody, które nie są połączone ze sobą za pomocą usługi Express Route Global Reach.][1]

Program **ExpressRoute Global REACH**umożliwia łączenie obwodów ExpressRoute ze sobą w celu naprowadzenia prywatnej sieci między sieciami lokalnymi. W powyższym przykładzie, przy dodawaniu ExpressRoute Global Reach, Sieć San Francisco Office (10.0.1.0/24) może bezpośrednio wymieniać dane z biurem w Londynie (10.0.2.0/24) za pośrednictwem istniejących obwodów usługi ExpressRoute i sieci globalnej firmy Microsoft. 

![Diagram przedstawiający obwody połączone ze sobą za pomocą usługi Express Route Global Reach.][2]

## <a name="use-case"></a>Przypadek użycia
ExpressRoute Global Reach zaprojektowano w celu uzupełnienia implementacji sieci WAN dostawcy usług i łączenia biur oddziałów na całym świecie. Na przykład, jeśli dostawca usług działa głównie w Stany Zjednoczone i łączy wszystkie gałęzie w Stanach Zjednoczonych, ale dostawca usług nie działa w Japonii i Hong Kongu, z ExpressRoute Global Reach można pracować z lokalnym dostawcą usług, a firma Microsoft łączy Twoje gałęzie w Stanach Zjednoczonych przy użyciu ExpressRoute i naszej sieci globalnej.

![Diagram przedstawiający przypadek użycia dla Global Reach usługi Express Route.][3]

## <a name="availability"></a>Dostępność 
ExpressRoute Global Reach jest obsługiwane w następujących miejscach. 

> [!NOTE] 
> Aby umożliwić ExpressRoute Global Reach między [różnymi regionami geopolitycznych](expressroute-locations-providers.md#locations), obwody muszą być **jednostkami SKU w warstwie Premium**.

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
* Norwegia
* Singapur
* Szwecja
* Szwajcaria
* Zjednoczone Królestwo
* Stany Zjednoczone

## <a name="next-steps"></a>Następne kroki
- Wyświetl [Global REACH często zadawanych pytań](expressroute-faqs.md#globalreach).
- Dowiedz się, jak [włączyć Global REACH](expressroute-howto-set-global-reach.md).
- Dowiedz się, jak [połączyć obwód ExpressRoute z siecią wirtualną](expressroute-howto-linkvnet-arm.md).

<!--Image References-->
[1]: ./media/expressroute-global-reach/1.png "Diagram bez globalnego zasięgu"
[2]: ./media/expressroute-global-reach/2.png "Diagram z zasięgiem globalnym"
[3]: ./media/expressroute-global-reach/3.png "przypadek użycia globalnego zasięgu"
