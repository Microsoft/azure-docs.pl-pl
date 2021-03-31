---
title: 'Azure Front drzwiczki: Ochrona DDoS'
description: Ta strona zawiera informacje na temat sposobu, w jaki platforma Azure Front-Standard/Premium pomaga chronić przed atakami DDoS
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.topic: conceptual
ms.date: 02/18/2021
ms.author: jodowns
ms.openlocfilehash: 9c67944717888439c0d6bd84e1615f51dd91fcac
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101100148"
---
# <a name="ddos-protection-on-azure-front-door-standardpremium-preview"></a>Ochrona DDoS na platformie Azure — Standard/Premium (wersja zapoznawcza)

> [!Note]
> Ta dokumentacja dotyczy platformy Azure — Standard/Premium (wersja zapoznawcza). Szukasz informacji na temat zewnętrznych drzwi platformy Azure? [Tutaj](../front-door-overview.md)Wyświetl.

Drzwi frontonu platformy Azure mają kilka funkcji i cech, które mogą pomóc w zapobieganiu atakom typu "odmowa usługi" (DDoS). Te funkcje mogą uniemożliwiać osobom atakującym dotarcie do aplikacji i wpływać na dostępność i wydajność aplikacji.

> [!IMPORTANT]
> Platforma Azure Front-Standard/Premium (wersja zapoznawcza) jest obecnie dostępna w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="integration-with-azure-ddos-protection-basic"></a>Integracja z usługą Azure DDoS Protection Basic

Drzwi z przodu są chronione przez Azure DDoS Protection podstawowa. Ta funkcja jest domyślnie zintegrowana z platformą front-drzwi i bez dodatkowych kosztów. Pełna skala i pojemność sieci wdrożonej globalnie drzwi przednich zapewnia ochronę przed typowymi atakami warstwy sieci za pomocą zawsze włączone monitorowanie ruchu i środki zaradcze w czasie rzeczywistym. Podstawowa ochrona DDoS jest również obroną przed Najczęściej spotykanymi, często powtarzanymi zapytaniami w usłudze DNS warstwy 7 oraz atakami w warstwie 3 i 4, które są przeznaczone dla publicznych punktów końcowych. Ta usługa ma także sprawdzony rekord śledzenia w zakresie ochrony firmowych i korporacyjnych usług firmy Microsoft przed atakami na dużą skalę. Aby uzyskać więcej informacji, zobacz [Azure DDoS Protection](../../security/fundamentals/ddos-best-practices.md).

## <a name="protocol-blocking"></a>Blokowanie protokołu

Drzwi z przodu akceptują tylko ruch w protokołach HTTP i HTTPS i będą przetwarzać tylko prawidłowe żądania o znanym `Host` nagłówku. Takie zachowanie pomaga ograniczyć niektóre typowe ataki typu DDoS, w tym ataki pomiarowe, które rozpoznają się z zakresem protokołów i portów, atakami na wzmocnienie systemu DNS i atakami polegającymi na zatruciu TCP.

## <a name="capacity-absorption"></a>Absorpcja pojemności

Drzwi z przodu to wysoce skalowana, globalnie rozproszona usługa. Mamy wielu klientów, w tym własnych produktów w chmurze firmy Microsoft, które odbierają setki tysięcy żądań w każdej sekundzie. Drzwi z przodu znajdują się na brzegu sieci platformy Azure, pochłaniają i geograficznie izolują ataki dużych ilości. Może to uniemożliwić przechodzenie złośliwego ruchu poza granicę sieci platformy Azure.

## <a name="caching"></a>Buforowanie

[Możliwości buforowania drzwi przednich](concept-caching.md) mogą służyć do ochrony punktów końcowych w dużych ilościach ruchu wygenerowanych przez atak. Zasoby pamięci podręcznej zostaną zwrócone z węzłów krawędzi z przodu, aby nie były przekazywane do zaplecza. Nawet krótkie czasy wygaśnięcia w pamięci podręcznej (w sekundach lub minutach) odpowiedzi dynamicznych mogą znacznie zmniejszyć obciążenie usług zaplecza. Aby uzyskać więcej informacji o pojęciach dotyczących buforowania i wzorcach, zobacz [zagadnienia dotyczące buforowania](/azure/architecture/best-practices/caching) i [wzorzec z obsługą pamięci podręcznej](/azure/architecture/patterns/cache-aside).

## <a name="web-application-firewall-waf"></a>Zapora aplikacji internetowej

[Zapora aplikacji sieci Web (WAF) frontonu](../../web-application-firewall/afds/afds-overview.md) może służyć do ograniczania wielu różnych typów ataków:

* Używanie zestawu reguł zarządzanych zapewnia ochronę przed wieloma typowymi atakami.
* Ruch spoza określonego regionu geograficznego lub w określonym regionie można zablokować lub przekierować do statycznej strony sieci Web. Aby uzyskać więcej informacji, zobacz [geograficzne filtrowanie](../../web-application-firewall/afds/waf-front-door-geo-filtering.md).
* Adresy IP i zakresy identyfikowane jako złośliwe mogą zostać zablokowane.
* Ograniczanie szybkości można zastosować, aby zapobiec zbyt częstym wywoływaniu usługi adresów IP.
* Można utworzyć [niestandardowe reguły WAF](../../web-application-firewall/afds/waf-front-door-custom-rules.md) , aby automatycznie blokować i ograniczać ataki http lub https, które mają znane sygnatury.

## <a name="for-further-protection"></a>Aby zapewnić lepszą ochronę

Jeśli potrzebujesz dalszej ochrony, możesz włączyć opcję [Azure DDoS Protection Standard](../../security/fundamentals/ddos-best-practices.md#ddos-protection-standard) w sieci wirtualnej, w której zostaną wdrożone zaplecza. Klienci w DDoS Protection standard otrzymują więcej korzyści, takich jak:

* Ochrona kosztów
* Gwarancja SLA
* Dostęp do ekspertów z zespołu szybkiego reagowania DDoS w celu uzyskania natychmiastowej pomocy w trakcie ataku.

## <a name="next-steps"></a>Następne kroki

Dowiedz się [, jak utworzyć warstwę przednią/standardową](create-front-door-portal.md).
