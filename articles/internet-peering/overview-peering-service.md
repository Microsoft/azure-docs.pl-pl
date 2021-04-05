---
title: Internetowa komunikacja równorzędna a Peering Service
titleSuffix: Azure
description: Internetowa komunikacja równorzędna a Peering Service
services: internet-peering
author: derekolo
ms.service: internet-peering
ms.topic: overview
ms.date: 5/22/2020
ms.author: derekol
ms.openlocfilehash: 717a8c87c6a1c22d3f75cd4e3054bfdf0c4b5f9a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "95023449"
---
# <a name="internet-peering-vs-peering-service"></a>Internetowa komunikacja równorzędna a Peering Service

Komunikacja równorzędna internetowa dotyczy wszelkich połączeń między siecią globalną firmy Microsoft (AS8075) a przewoźnikami lub usługodawcami sieciowymi. Usługodawca może zostać partnerem usługi komunikacji równorzędnej, implementując wymagania dotyczące partnerstwa usługi komunikacji równorzędnej opisane poniżej w celu zapewnienia niezawodnej i wysoce wydajnej łączności publicznej z optymalnym routingiem od klienta do sieci firmy Microsoft.

## <a name="about-peering-service"></a>Peering Service — informacje
Usługa komunikacji równorzędnej to program partnerski z dostawcami usług kluczowych, który zapewnia najlepszą w swojej klasie publiczną łączność internetową z użytkownikami przedsiębiorstwa. Partnerzy, którzy są częścią programu, będą mieć bezpośrednie, wysoce dostępne, geograficznie nadmiarowe połączenia i zoptymalizowane Routing do firmy Microsoft. Usługa komunikacji równorzędnej jest dodatkiem do portfolio połączeń firmy Microsoft:
*   ExpressRoute dla połączeń prywatnych z zasobami IaaS lub PaaS (obsługa prywatnych przestrzeni adresów IP)
    *   Łączność oparta na partnerze
    *   Bezpośrednia 100G — łączność z firmą Microsoft
*   Protokół IPSEC przez Internet do łączności sieci VPN z chmurą
*   Łączność z platformą Azure w sieci WAN za pośrednictwem wirtualnej sieci WAN

Segment docelowy usługi Komunikacja równorzędna to SaaS łączności, klienci SD-WAN, którzy pragną wykonać internetową zagadnień w oddziale i Klienci mający dwukierunkową strategię MPLS i internetową klasy korporacyjnej.

Głównym celem w przypadku nawiązywania połączenia z Microsoft Cloud powinno być zminimalizowanie opóźnienia przez skrócenie czasu błądzenia (RTT) z witryny użytkownika do sieci globalnej firmy Microsoft, która jest szkieletem sieci publicznej firmy Microsoft, który łączy wszystkie centra danych firmy Microsoft i wiele punktów wejścia aplikacji w chmurze. Zobacz [Uzyskiwanie najlepszej łączności i wydajności w pakiecie Office 365](https://techcommunity.microsoft.com/t5/Office-365-Blog/Getting-the-best-connectivity-and-performance-in-Office-365/ba-p/124694).

> [!div class="mx-imgBorder"]
> ![Obraz dostępu rozproszonego](./media/distributed-access.png)

Na rysunku powyżej każdego biura oddziału globalnego przedsiębiorstwa program nawiązuje połączenie z najbliższą możliwą lokalizacją Microsoft Edge za pomocą sieci partnera.

**Korzyści dla klientów usługi Komunikacja równorzędna:**
* Najlepsza usługa routingu publicznego przez Internet do Microsoft Cloud usług w celu uzyskania optymalnej wydajności i niezawodności.
* Możliwość wybrania preferowanego dostawcy usług w celu nawiązania połączenia z Microsoft Cloud.
* Informacje o ruchu, takie jak raportowanie opóźnień i monitorowanie prefiksów.
* Optymalne przeskoki sieciowe (jako przeskoki) z Microsoft Cloud.
* Kierowanie analiz i statystyk — zdarzenia dla anomalii tras Border Gateway Protocol ([BGP](https://en.wikipedia.org/wiki/Border_Gateway_Protocol)) (Wykrywanie przecieków/przejęcia) i nieoptymalny Routing.

## <a name="peering-service-partnership-requirements"></a>Wymagania dotyczące powiązań usług komunikacji równorzędnej
* Łączność z Microsoft Cloud w lokalizacji najbliższej klientowi. Dostawca usług partnerskich będzie kierować ruchem użytkowników do przeglądarki Microsoft Edge znajdującej się najbliżej użytkownika. Podobnie, w przypadku ruchu skierowanego do użytkownika, firma Microsoft kieruje ruchem (przy użyciu znacznika BGP) do lokalizacji brzegowej znajdującej się najbliżej użytkownika i usługodawcy wyśle ten ruch do użytkownika.
* Partner będzie obsługiwać wysoką dostępność, wysoką przepływność i geograficznie nadmiarową łączność z siecią globalną firmy Microsoft.
* Partner może korzystać z istniejącej komunikacji równorzędnej w celu obsługi komunikacji równorzędnej, jeśli spełnia to wymaganie.

## <a name="faq"></a>Często zadawane pytania
Często zadawane pytania można znaleźć w temacie [Usługa Komunikacja równorzędna — często zadawane](service-faqs.md)pytania.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o korzyściach dla klientów dzięki [usłudze komunikacji równorzędnej](../peering-service/index.yml).
* Informacje o procedurach włączania bezpośredniej komunikacji równorzędnej dla usługi komunikacji równorzędnej w [przewodniku partnera usług komunikacji równorzędnej](walkthrough-peering-service-all.md).