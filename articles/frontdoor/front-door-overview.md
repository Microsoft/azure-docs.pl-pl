---
title: Moje drzwi platformy Azure | Microsoft Docs
description: Ten artykuł zawiera omówienie usługi Azure Front Door.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: overview
ms.workload: infrastructure-services
ms.date: 03/09/2021
ms.author: duau
ms.openlocfilehash: 2a5c298c201aa2a1f968c2a96dcfd6963a410cf7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103149226"
---
# <a name="what-is-azure-front-door"></a>Co to jest usługa Azure Front Door?

> [!IMPORTANT]
> Ta dokumentacja dotyczy zewnętrznych drzwi platformy Azure. Szukasz informacji na temat platformy Azure w warstwie Standardowa/Premium (wersja zapoznawcza)? [Tutaj](/standard-premium/overview.md)Wyświetl.

Drzwi frontonu platformy Azure to globalny, skalowalny punkt wejścia, który używa sieci Microsoft Global Edge do tworzenia szybkich, bezpiecznych i skalowalnych aplikacji sieci Web. Za pomocą przednich drzwi możesz przekształcić globalne aplikacje dla użytkowników i przedsiębiorstw na niezawodne, wysoce wydajne, nowoczesne aplikacje zawierające zawartość, która dociera do odbiorców globalnych za pomocą platformy Azure.

<p align="center">
  <img src="./media/front-door-overview/front-door-visual-diagram.png" alt="Front Door architecture" width="600" title="Azure Front Door">
</p>

Drzwiczki z przodu działają w warstwie 7 (warstwie HTTP/HTTPS) przy użyciu protokołu emisji pojedynczej z podziałem sieci globalnej TCP i firmy Microsoft, aby poprawić łączność globalną. Na podstawie metody routingu można upewnić się, że drzwi z przodu będą kierować żądania klientów do najszybszych i najbardziej dostępnych zaplecza aplikacji. Zaplecze aplikacji to dowolna internetowa usługa hostowana wewnątrz platformy Azure lub poza nią. Drzwi frontonu udostępniają szereg [metod routingu ruchu](front-door-routing-methods.md) i [opcji monitorowania kondycji zaplecza](front-door-health-probes.md) , aby odpowiadały różnym potrzebom aplikacji i automatycznym scenariuszom pracy awaryjnej. Podobnie jak w przypadku [Traffic Manager](../traffic-manager/traffic-manager-overview.md), tylne drzwi są odporne na awarie, w tym niepowodzenia w całym regionie świadczenia usługi Azure.

>[!NOTE]
> Platforma Azure udostępnia zestaw w pełni zarządzanych rozwiązań do równoważenia obciążenia dla Twoich scenariuszy. 
> * Jeśli chcesz korzystać z globalnego routingu opartego na systemie DNS i **nie** ma wymagań dotyczących zakończenia protokołu Transport Layer Security (TLS) ("odciążanie protokołu SSL"), żądania dla protokołu HTTP/HTTPS lub przetwarzania warstwy aplikacji, zapoznaj się z [Traffic Manager](../traffic-manager/traffic-manager-overview.md). 
> * Jeśli chcesz zrównoważyć obciążenie między serwerami w regionie w warstwie aplikacji, zapoznaj się z tematem [Application Gateway](../application-gateway/overview.md).
> * Aby przeprowadzić Równoważenie obciążenia warstwy sieciowej, zapoznaj się z tematem [Load Balancer](../load-balancer/load-balancer-overview.md). 
> 
> Kompleksowe scenariusze mogą skorzystać z łączenia tych rozwiązań w razie potrzeby.
> Aby zapoznać się z porównaniem opcji równoważenia obciążenia platformy Azure, zobacz [Omówienie opcji równoważenia obciążenia na platformie Azure](/azure/architecture/guide/technology-choices/load-balancing-overview).

## <a name="why-use-azure-front-door"></a>Dlaczego warto korzystać z usługi Azure Front drzwiczki?

Za pomocą przednich drzwi można kompilować, obsługiwać i skalować dynamiczną aplikację sieci Web oraz zawartość statyczną. Drzwi z przodu umożliwiają zdefiniowanie i monitorowanie globalnego routingu dla ruchu w sieci Web oraz zarządzanie nim przez zoptymalizowanie wydajności i niezawodności najwyższej warstwy dla użytkowników końcowych przez szybką globalną pracę w trybie failover.

Najważniejsze funkcje dołączone do czołowych drzwi:

* Przyspieszona wydajność aplikacji przy użyciu podziału **[protokołu emisji](front-door-routing-architecture.md#anycast)** pojedynczej opartego na **[protokole TCP](front-door-routing-architecture.md#splittcp)**.

* Inteligentne monitorowanie **[sondowania kondycji](front-door-health-probes.md)** zasobów zaplecza.

*  Routing **[oparty na ścieżkach URL](front-door-route-matching.md)** dla żądań.

* Umożliwia hostowanie wielu witryn sieci Web w celu wydajnej infrastruktury aplikacji. 

* **[Koligacja sesji](front-door-routing-methods.md#affinity)** oparta na plikach cookie.

* **[Odciążanie protokołu SSL](front-door-custom-domain-https.md)** i zarządzanie certyfikatami.

* Zdefiniuj własną **[domenę niestandardową](front-door-custom-domain.md)**. 

* Zabezpieczenia aplikacji za pomocą zintegrowanej  **[zapory aplikacji sieci Web (WAF)](../web-application-firewall/overview.md)**.

* Przekierowywanie ruchu HTTP do protokołu HTTPS przy użyciu **[adresu URL](front-door-url-redirect.md)**.

* Niestandardowa ścieżka przesyłania dalej z **[adresem URL](front-door-url-rewrite.md)**.

* Natywna obsługa łączności z kompleksowym protokołem IPv6 i **[protokołu HTTP/2](front-door-http2.md)**.

## <a name="pricing"></a>Ceny

Aby uzyskać informacje o cenach, zobacz [cennik usługi Front Door](https://azure.microsoft.com/pricing/details/frontdoor/). Zobacz umowę [SLA dla drzwi platformy Azure](https://azure.microsoft.com/en-us/support/legal/sla/frontdoor/v1_0/).

## <a name="whats-new"></a>Co nowego

Zasubskrybuj źródło danych RSS i Wyświetl najnowsze aktualizacje funkcji Front-drzwiczk platformy Azure na stronie [aktualizacji platformy Azure](https://azure.microsoft.com/updates/?category=networking&query=Azure%20Front%20Door) .

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [utworzyć usługę Front Door](quickstart-create-front-door.md).
- Dowiedz się, [jak działa usługa Front Door](front-door-routing-architecture.md).
