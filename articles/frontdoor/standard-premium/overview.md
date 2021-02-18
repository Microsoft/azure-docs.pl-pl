---
title: Warstwa czołowa platformy Azure — warstwa standardowa/Premium | Microsoft Docs
description: Ten artykuł zawiera omówienie standardu Front/Premium platformy Azure.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: overview
ms.workload: infrastructure-services
ms.date: 02/18/2021
ms.author: duau
ms.openlocfilehash: 32654f743301f9f2f6c010947d73d957c96dceac
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/18/2021
ms.locfileid: "101100890"
---
# <a name="what-is-azure-front-door-standardpremium-preview"></a>Co to jest wersja Standard/Premium systemu Azure (wersja zapoznawcza)?

> [!IMPORTANT]
> Ta dokumentacja dotyczy platformy Azure — Standard/Premium (wersja zapoznawcza). Szukasz informacji na temat zewnętrznych drzwi platformy Azure? Wyświetl [dokumenty z przodu platformy Azure](../front-door-overview.md).

Platforma Azure Front-Standard/Premium jest szybką, niezawodną i bezpieczną nowoczesnej sieci CDN w chmurze, która korzysta z usługi Microsoft Global Edge Network i integruje się z funkcją inteligentnej ochrony przed zagrożeniami. Łączy ona możliwości programu Azure CDN Standard od firmy Microsoft, platformy Azure — Zapora aplikacji sieci Web (WAF) na jedną bezpieczną platformę sieci CDN w chmurze.

Korzystając z platformy Azure w warstwie Standardowa/Premium, możesz przekształcić globalne aplikacje dla konsumentów i przedsiębiorstw w bezpieczne i wysoce wydajne, nowoczesne aplikacje, które uzyskują dostęp do globalnej grupy odbiorców z małymi opóźnieniami.

   :::image type="content" source="../media/overview/front-door-overview.png" alt-text="Architektura platformy Azure z przodu Standard/Premium" lightbox="../media/overview/front-door-overview-expanded.png":::

Platforma Azure Front-at Standard/Premium działa w warstwie 7 (warstwie protokołu HTTP/HTTPS) przy użyciu protokołu emisji pojedynczej z podziałem sieci globalnej TCP i Microsoft, aby zwiększyć łączność globalną. Na podstawie metody routingu możesz zapewnić, że drzwi platformy Azure będą kierować żądania klientów do najszybszych i najbardziej dostępnych źródeł. Zaplecze aplikacji to dowolna internetowa usługa hostowana wewnątrz platformy Azure lub poza nią. AzureFront drzwi standardowa/Premium udostępniają szereg metod routingu ruchu i opcji monitorowania kondycji pochodzenia, aby odpowiadały różnym potrzebom aplikacji i automatycznym scenariuszom pracy awaryjnej. Podobnie jak w przypadku Traffic Manager, tylne drzwi są odporne na awarie, w tym niepowodzenia w całym regionie świadczenia usługi Azure.

Drzwi frontonu platformy Azure chronią również aplikację na krawędziach za pomocą zapory aplikacji sieci Web, ochrony bot oraz wbudowanych funkcji 3/warstwy 4 DDoS Protection. Zabezpiecza on także zaplecza prywatne z usługą link prywatny. Drzwi frontonu platformy Azure zapewniają najlepsze w firmie Microsoft zabezpieczenia na skalę globalną.  

>[!NOTE]
> Platforma Azure udostępnia zestaw w pełni zarządzanych rozwiązań do równoważenia obciążenia dla Twoich scenariuszy.
>
> * Jeśli chcesz korzystać z globalnego routingu opartego na systemie DNS i **nie** ma wymagań dotyczących zakończenia protokołu Transport Layer Security (TLS) ("odciążanie protokołu SSL"), żądania dla protokołu HTTP/HTTPS lub przetwarzania warstwy aplikacji, zapoznaj się z [Traffic Manager](../../traffic-manager/traffic-manager-overview.md).
> * Jeśli chcesz zrównoważyć obciążenie między serwerami w regionie w warstwie aplikacji, przejrzyj [Application Gateway](../../application-gateway/overview.md)
> * Aby przeprowadzić Równoważenie obciążenia warstwy sieciowej, zapoznaj się z tematem [Load Balancer](../../load-balancer/load-balancer-overview.md).
>
> Kompleksowe scenariusze mogą skorzystać z łączenia tych rozwiązań w razie potrzeby.
> Aby zapoznać się z porównaniem opcji równoważenia obciążenia platformy Azure, zobacz [Omówienie opcji równoważenia obciążenia na platformie Azure](/azure/architecture/guide/technology-choices/load-balancing-overview).

> [!IMPORTANT]
> Usługa Azure Front-Standard/Premium jest obecnie dostępna w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="why-use-azure-front-door-standardpremium-preview"></a>Dlaczego warto korzystać z platformy Azure w warstwie Standardowa/Premium (wersja zapoznawcza)?

Platforma Azure Front-Standard/Premium udostępnia pojedynczą ujednoliconą platformę dla zawartości statycznej i przyspieszania aplikacji dynamicznych z zaawansowanymi możliwościami zabezpieczeń. Drzwi z przodu umożliwiają również definiowanie i monitorowanie globalnego routingu aplikacji oraz zarządzanie nim.

Najważniejsze funkcje dostępne w przypadku platformy Azure Front-Standard/Premium (wersja zapoznawcza):

- Przyspieszona wydajność aplikacji przy użyciu podziału protokołu emisji pojedynczej opartego na protokole TCP.

- Inteligentne monitorowanie **[sondy kondycji](concept-health-probes.md)** i równoważenie obciążenia między **[źródłami](concept-origin.md)**.

- Zdefiniuj własną domenę niestandardową przy użyciu elastycznej weryfikacji domeny.

- Zabezpieczenia aplikacji za pomocą zintegrowanej [zapory aplikacji sieci Web (WAF)](../../web-application-firewall/afds/afds-overview.md).

- Odciążanie protokołu SSL i zintegrowane zarządzanie certyfikatami.

- Zabezpiecz swoje źródła za pomocą **[linku prywatnego](concept-private-link.md)**.  

- Dostosowywalne Routing ruchu i optymalizacje za pośrednictwem **[zestawu reguł](concept-rule-set.md)**.

- **[Raporty wbudowane](how-to-reports.md)** ze wszystkimi pulpitami nawigacyjnymi dla wszystkich drzwi i wzorców.

- Monitorowanie i alerty w **[czasie rzeczywistym](how-to-monitor-metrics.md)** , które integrują się z usługą Azure Monitoring.

- **[Rejestrowanie](how-to-logs.md)** dla każdego żądania Front-drzwiczk i sondy kondycji zakończone niepowodzeniem.

- Natywna obsługa łączności z kompleksowym protokołem IPv6 i protokołu HTTP/2.

## <a name="pricing"></a>Cennik

Platforma Azure Front-Standard/Premium ma dwie jednostki SKU, standard i Premium. Zobacz [porównanie warstw](tier-comparison.md). Aby uzyskać informacje o cenach, zobacz [cennik usługi Front Door](https://azure.microsoft.com/pricing/details/frontdoor/). 

## <a name="whats-new"></a>Co nowego

Zasubskrybuj źródło danych RSS i Wyświetl najnowsze aktualizacje funkcji Front-drzwiczk platformy Azure na stronie [aktualizacji platformy Azure](https://azure.microsoft.com/updates/?category=networking&query=Azure%20Front%20Door) .

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak [utworzyć usługę Front Door](create-front-door-portal.md).
