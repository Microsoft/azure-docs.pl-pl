---
title: Ochrona przed drzwiami platformy Azure — DDoS
description: Ta strona zawiera informacje dotyczące sposobu, w jaki usługa Azure Front Drzwiczks pomaga chronić przed atakami DDoS
services: frontdoor
documentationcenter: ''
author: johndowns
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/28/2020
ms.author: jodowns
ms.openlocfilehash: 24a8a559d72eae4dab0b6e740b34890f1d7ff114
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96350787"
---
# <a name="ddos-protection-on-front-door"></a>DDoS ochronę na wierzchu drzwi

Drzwi frontonu platformy Azure mają kilka funkcji i cech, które mogą pomóc w zapobieganiu atakom typu "odmowa usługi" (DDoS). Te funkcje mogą uniemożliwiać osobom atakującym dotarcie do aplikacji i wpływać na dostępność i wydajność aplikacji.

## <a name="integration-with-azure-ddos-protection-basic"></a>Integracja z usługą Azure DDoS Protection Basic

Drzwi z przodu są chronione przez Azure DDoS Protection podstawowa. Jest ona zintegrowana z platformą front-drzwi domyślnie i bez dodatkowych kosztów. Pełna skala i pojemność sieci wdrożonej globalnie drzwi przednich zapewnia ochronę przed typowymi atakami warstwy sieci za pomocą zawsze włączone monitorowanie ruchu i środki zaradcze w czasie rzeczywistym. Podstawowa ochrona DDoS jest również obroną przed Najczęściej spotykanymi, często powtarzanymi zapytaniami w usłudze DNS warstwy 7 oraz atakami w warstwie 3 i 4, które są przeznaczone dla publicznych punktów końcowych. Ta usługa ma także sprawdzony rekord śledzenia w zakresie ochrony firmowych i korporacyjnych usług firmy Microsoft przed atakami na dużą skalę. Aby uzyskać więcej informacji, zobacz [Azure DDoS Protection](../security/fundamentals/ddos-best-practices.md).

## <a name="protocol-blocking"></a>Blokowanie protokołu

Drzwi z przodu akceptują tylko ruch w protokołach HTTP i HTTPS i będą przetwarzać tylko prawidłowe żądania o znanym `Host` nagłówku. Takie zachowanie pomaga ograniczyć niektóre typowe ataki typu DDoS, w tym ataki pomiarowe, które są rozłożone na wiele protokołów i portów, ataki na wzmocnienie systemu DNS i ataki trujące protokołu TCP.

## <a name="capacity-absorption"></a>Absorpcja pojemności

Drzwi z przodu to wysoce skalowana, globalnie rozproszona usługa. Mamy wielu klientów, w tym własnych produktów w chmurze firmy Microsoft, które odbierają setki tysięcy żądań w każdej sekundzie. Drzwi z przodu znajdują się na granicy sieci platformy Azure, dzięki czemu można wchłonąć i geograficznie izolować duże ataki. Może to uniemożliwić przechodzenie złośliwego ruchu poza granicę sieci platformy Azure.

## <a name="caching"></a>Buforowanie

[Możliwości buforowania drzwi przednich](./front-door-caching.md) mogą służyć do ochrony punktów końcowych w dużych ilościach ruchu wygenerowanych przez atak. Zasoby pamięci podręcznej zostaną zwrócone z węzłów krawędzi z przodu, aby nie były przekazywane do zaplecza. Nawet krótkie czasy wygaśnięcia w pamięci podręcznej (w sekundach lub minutach) odpowiedzi dynamicznych mogą znacznie zmniejszyć obciążenie usług zaplecza. Aby uzyskać więcej informacji o pojęciach dotyczących buforowania i wzorcach, zobacz [zagadnienia dotyczące buforowania](/azure/architecture/best-practices/caching) i [wzorzec z obsługą pamięci podręcznej](/azure/architecture/patterns/cache-aside).

## <a name="web-application-firewall-waf"></a>Zapora aplikacji internetowej

[Zapora aplikacji sieci Web (WAF) frontonu](../web-application-firewall/afds/afds-overview.md) może służyć do ograniczania wielu różnych typów ataków:

* Korzystanie z zestawu reguł zarządzanych zapewnia ochronę przed wielu typowymi atakami.
* Ruch spoza określonego regionu geograficznego lub w określonym regionie można zablokować lub przekierować do statycznej strony sieci Web. Aby uzyskać więcej informacji, zobacz [geograficzne filtrowanie](../web-application-firewall/afds/waf-front-door-geo-filtering.md).
* Adresy IP i zakresy identyfikowane jako złośliwe mogą zostać zablokowane.
* Ograniczanie szybkości można zastosować, aby zapobiec zbyt częstym wywoływaniu usługi adresów IP.
* Można utworzyć [niestandardowe reguły WAF](../web-application-firewall/afds/waf-front-door-custom-rules.md) , aby automatycznie blokować i ograniczać ataki http lub https, które mają znane sygnatury.

## <a name="for-further-protection"></a>Aby zapewnić lepszą ochronę

Jeśli potrzebujesz dalszej ochrony, możesz włączyć opcję [Azure DDoS Protection Standard](../security/fundamentals/ddos-best-practices.md#ddos-protection-standard) w sieci wirtualnej, w której zostaną wdrożone zaplecza. Klienci w DDoS Protection standard otrzymują dodatkowe korzyści, w tym ochronę kosztów, gwarancję SLA i dostęp do ekspertów z zespołu szybkiej odpowiedzi DDoS w celu uzyskania natychmiastowej pomocy w trakcie ataku.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak skonfigurować [profil WAF na wierzchu drzwi](front-door-waf.md). 
- Dowiedz się, jak [utworzyć usługę Front Door](quickstart-create-front-door.md).
- Dowiedz się, [jak działa usługa Front Door](front-door-routing-architecture.md).