---
title: Sieć globalna firmy Microsoft — Azure
description: Dowiedz się, w jaki sposób firma Microsoft kompiluje i operuje jednym z największych sieci szkieletowych na świecie oraz o tym, dlaczego jest to centralne rozwiązanie w chmurze.
services: networking
documentationcenter: ''
author: KumudD
manager: ''
ms.service: virtual-network
ms.devlang: ''
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/05/2020
ms.author: kumud
ms.reviewer: ypitsch
ms.openlocfilehash: a33c7e7d637b519b249c250481a73d04c661d4d5
ms.sourcegitcommit: 67b44a02af0c8d615b35ec5e57a29d21419d7668
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/06/2021
ms.locfileid: "97915848"
---
# <a name="microsoft-global-network"></a>Globalna sieć firmy Microsoft

Firma Microsoft jest właścicielem i obsługuje jedną z największych sieci szkieletowych na świecie. Ta globalna i rozbudowana architektura obejmująca ponad 165 000 kilometrów łączy nasze centra danych i klientów. 
 
Codziennie klienci na całym świecie łączą i przekazują biliony żądań do Microsoft Azure, Bing, Dynamics 365, Microsoft 365, XBox i wielu innych. Niezależnie od typu klienci oczekują natychmiastowej niezawodności i czasu reakcji z naszych usług. 
 
[Sieć globalna firmy Microsoft](https://azure.microsoft.com/global-infrastructure/global-network/) jest centralną częścią dostarczania doskonałego środowiska w chmurze. Dzięki połączeniu naszych [centrów danych](https://azure.microsoft.com/global-infrastructure/) firmy Microsoft w regionach platformy Azure w wysokości 61 i dużych oczkach, które są dostosowywane strategicznie na całym świecie, Nasza sieć globalna oferuje dostępność, pojemność i elastyczność, aby sprostać wymaganiom.

![Globalna sieć firmy Microsoft](./media/microsoft-global-network/microsoft-global-wan.png)
 
## <a name="get-the-premium-cloud-network"></a>Pobierz sieć w chmurze w warstwie Premium
 
W przypadku korzystania z chmury firmy Microsoft można łatwo wybrać [najlepsze możliwe środowisko](https://www.sdxcentral.com/articles/news/azure-tops-aws-gcp-in-cloud-performance-says-thousandeyes/2018/11/) . Od momentu, gdy ruch klientów przejdzie do naszej sieci globalnej za pośrednictwem strategicznie rozmieszczonych węzłów brzegowych, dane są przesyłane przez zoptymalizowane trasy w najbliższym tempie. Zapewnia to optymalny czas oczekiwania na najlepszą wydajność. Te węzły brzegowe, wszystkie połączone z ponad 4000 unikatowymi partnerami internetowymi (równorzędnymi) za pomocą tysięcy połączeń w więcej niż 175 lokalizacjach, stanowią podstawę naszej strategii połączeń. 
 
Bez względu na to, czy jest nawiązywane połączenie z Warszawy do Tokio, czy z Waszyngton DC do Los Angeles, wydajność sieci jest określana i wpływa na takie elementy, jak opóźnienia, wahania, utrata pakietów i przepływność.  Firma Microsoft zaleca, aby używać bezpośrednich połączeń z przeciwieństwem do przesyłania linków, dzięki czemu ruch związany z odpowiedzią jest symetryczny i pomaga w zachowaniu przeskoków, stron i ścieżek komunikacji równorzędnej jako krótkich i prostych, jak to możliwe. 

Na przykład, jeśli użytkownik w Londynie próbuje uzyskać dostęp do usługi w Tokio, ruch internetowy przechodzi przez firmę Microsoft do Francji, a nasze Trans-Arabia ścieżki między Europą a Indie, a następnie do Japonii, gdzie usługa jest hostowana. Ruch odpowiedzi jest symetryczny. Jest to czasami nazywane [routingiem zimnej ziemniaka](https://en.wikipedia.org/wiki/Hot-potato_and_cold-potato_routing) , co oznacza, że ruch w sieci firmy Microsoft jest dłuższy niż to możliwe.  
  
Czy oznacza to, że to wszystko i cały ruch w przypadku korzystania z usług firmy Microsoft? Tak, cały ruch między centrami danych, w Microsoft Azure lub między usługami firmy Microsoft, takimi jak Virtual Machines, Microsoft 365, XBox, SQL baz danych, Storage i Virtual Networks, jest kierowany w naszej sieci globalnej i nigdy nie przez publiczny Internet, aby zapewnić optymalną wydajność i spójność.  
 
Duże inwestycje w pojemność i różnorodność w sieciach Metro, lądowych i podmorski mają kluczowe znaczenie dla nas, aby zachować spójny i wysoki poziom usług, jednocześnie dostarczając jednocześnie paliwo do najwyższej ilości naszych chmur i Usługi online. Najnowsze dodatki do naszej sieci globalnej to nasz kabel podmorski [Marea](https://www.submarinecablemap.com/#/submarine-cable/marea) , pierwszy otwarty system line (OLS) firmy Subsea, między Bilbao, Hiszpania i Wirginia sekwencje, WIRGINIA, USA, a także [AEC](https://www.submarinecablemap.com/#/submarine-cable/aeconnect-1) między Nowym Jorku, USA i Dublin, Irlandia i [Nowy krzyżyk (NCP)](https://www.submarinecablemap.com/#/submarine-cable/new-cross-pacific-ncp-cable-system) między Tokio, Japonia i Warszawa, Oregon, USA. 
 

## <a name="our-network-is-your-network"></a>Nasza sieć to sieć

Wprowadziliśmy dwa dekady środowiska, a także duże inwestycje w sieć, aby zapewnić optymalną wydajność przez cały czas. Firmy mogą w pełni korzystać z zasobów sieciowych i tworzyć zaawansowane architektury nakładki na górze. 
 
Microsoft Azure oferuje bogatsze portfolio usług i możliwości, dzięki czemu klienci mogą szybko i łatwo tworzyć, rozwijać i zaspokajać wymagania sieciowe w dowolnym miejscu. Nasza rodzina usług łączności obejmuje wirtualne sieci równorzędne między regionami, hybrydową i architekturą typu punkt-lokacja i lokacja-lokacja, a także scenariusze globalne przesyłania adresów IP.  W przypadku przedsiębiorstw, które chcą połączyć własne centrum danych lub sieci z platformą Azure lub klienci z ogromnymi potrzebami pozyskiwania lub tranzytu, [ExpressRoute](../expressroute/expressroute-introduction.md), a [ExpressRoute Direct](../expressroute/expressroute-erdirect-about.md) udostępniają opcje do 100 GB/s, bezpośrednio do sieci globalnej firmy Microsoft w lokalizacjach komunikacji równorzędnej na całym świecie.  
 
[ExpressRoute Global REACH](../expressroute/expressroute-global-reach.md) jest zaprojektowana w celu uzupełnienia implementacji sieci WAN dostawcy usług i łączenia lokacji lokalnych na całym świecie. Jeśli uruchamiasz operację globalną, możesz użyć ExpressRoute Global Reach w połączeniu z preferowanymi i lokalnymi dostawcami usług, aby połączyć wszystkie lokacje globalne przy użyciu sieci globalnej firmy Microsoft. Rozszerzanie nowej sieci w chmurze (WAN) w taki sposób, aby obejmowała dużą liczbę rozgałęzień, można osiągnąć za pośrednictwem wirtualnej sieci WAN platformy Azure, która umożliwia bezproblemowe łączenie gałęzi z siecią globalną firmy Microsoft za pomocą SDWAN & urządzeń sieci VPN (czyli sprzętu lokalnego klienta lub CPE) z wbudowanym łatwośćiem korzystania z funkcji i zautomatyzowanej łączności i zarządzania konfiguracją. 
 
[Globalna komunikacja równorzędna](../virtual-network/virtual-network-peering-overview.md) sieci wirtualnych umożliwia klientom bezproblemowe łączenie się z co najmniej co najmniej z siecią wirtualną platformy Azure. Po nawiązaniu połączenia równorzędnego sieci wirtualne są wyświetlane jako jedna. Ruch między maszynami wirtualnymi w wirtualnych sieciach równorzędnych odbywa się za pomocą infrastruktury sieci szkieletowej firmy Microsoft, podobnie jak ruch jest kierowany między maszynami wirtualnymi w tej samej sieci wirtualnej — tylko za pomocą prywatnych adresów IP. 
 

## <a name="well-managed-using-software-defined-innovation"></a>Dobre zarządzanie przy użyciu innowacji zdefiniowanych programowo

W przypadku korzystania z jednej z wiodących chmur na świecie firma Microsoft zyskała wiele szczegółowych informacji i umożliwia tworzenie infrastruktury globalnej o wysokiej wydajności oraz zarządzanie nią.  
 
Stosujemy się do niezawodnego zestawu zasad operacyjnych: 
 
- Korzystaj z najlepszej jakości sprzętu do przełączania w różnych warstwach sieci.  
- Wdrażaj nowe funkcje z zerowym wpływem na użytkowników końcowych.  
- Aktualizacje są bezpiecznie i niezawodne w całej flotie, tak szybko, jak to możliwe. Godziny zamiast tygodni.  
- Korzystaj ze szczegółowych danych telemetrycznych w skali chmury i w pełni zautomatyzowanego łagodzenia błędów.  
- Używaj ujednoliconej i zdefiniowanej programowo technologii sieci do kontrolowania wszystkich elementów sprzętowych w sieci.  Eliminowanie duplikowanie i zmniejszanie błędów. 
 
Te zasady mają zastosowanie do wszystkich warstw sieci: z interfejsu sieciowego hosta, przełączania platformy, funkcji sieciowych w centrum danych, takich jak moduły równoważenia obciążenia, w całości do sieci WAN przy użyciu naszej platformy inżynierii ruchu i naszych sieci optycznych.  
 
Wykładniczy wzrost liczby platform Azure i jej sieci osiągnęło punkt, w którym ostatecznie zakończył się, że Intuition przez człowieka nie mogą już być używane do zarządzania globalnymi operacjami sieciowymi. Aby spełnić wymagania dotyczące długotrwałych, średnich i krótkoterminowych zmian w sieci, opracowano platformę do dublowania i emulowania naszej sieci produkcyjnej syntetycznie. Możliwość tworzenia dublowanych środowisk i uruchamiania milionów symulacji pozwala na testowanie zmian oprogramowania i sprzętu oraz ich wpływu przed przekazaniem ich do naszej platformy produkcyjnej i sieci. 

## <a name="next-steps"></a>Następne kroki
- [Dowiedz się, jak firma Microsoft zwiększa niezawodność sieci globalnej poprzez inteligentne oprogramowanie](https://azure.microsoft.com/blog/advancing-global-network-reliability-through-intelligent-software-part-1-of-2/)
- [Dowiedz się więcej na temat usług sieciowych oferowanych na platformie Azure](https://azure.microsoft.com/product-categories/networking/)