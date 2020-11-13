---
title: Zapora aplikacji sieci Web platformy Azure na platformie Azure — często zadawane pytania
description: Ten artykuł zawiera odpowiedzi na często zadawane pytania dotyczące zapory aplikacji sieci Web na platformie Azure
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.devlang: na
ms.topic: article
ms.date: 05/05/2020
ms.author: victorh
ms.openlocfilehash: 5b60082db53b458adc53ac23d98731ad1c97b52b
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/12/2020
ms.locfileid: "94563651"
---
# <a name="frequently-asked-questions-for-azure-web-application-firewall-on-azure-front-door-service"></a>Często zadawane pytania dotyczące zapory aplikacji sieci Web platformy Azure w usłudze Azure Front-drzwiczk

W tym artykule znajdują się odpowiedzi na często zadawane pytania dotyczące usługi Azure Web Application Firewall (WAF) na platformie Azure i jej funkcje. 

## <a name="what-is-azure-waf"></a>Co to jest Azure WAF?

Azure WAF to Zapora aplikacji sieci Web, która pomaga chronić aplikacje sieci Web przed typowymi zagrożeniami, takimi jak iniekcja SQL, skrypty między lokacjami i inne sieci Web. Można zdefiniować zasady WAF, które składają się z kombinacji reguł niestandardowych i zarządzanych w celu kontrolowania dostępu do aplikacji sieci Web.

Zasady usługi Azure WAF można stosować do aplikacji sieci Web hostowanych na Application Gateway lub na drzwiach frontonu platformy Azure.

## <a name="what-is-waf-on-azure-front-door"></a>Co to jest WAF na platformie Azure — drzwiczki? 

Drzwi frontonu platformy Azure to wysoce skalowalna, globalnie dystrybuowana aplikacja i usługa Content Delivery Network. Usługa Azure WAF, która jest zintegrowana z przednim Drzwiem, kończy ataki typu "odmowa usługi" i "ukierunkowane na aplikacje" na granicy sieci platformy Azure, a przed wprowadzeniem sieci wirtualnej zapewnia ochronę bez utraty wydajności.

## <a name="does-azure-waf-support-https"></a>Czy usługa Azure WAF obsługuje protokół HTTPS?

Drzwi przednich oferują odciążanie protokołu TLS. WAF jest natywnie zintegrowana z przednimi drzwiami i może sprawdzić żądanie po jego odszyfrowaniu.

## <a name="does-azure-waf-support-ipv6"></a>Czy usługa Azure WAF obsługuje protokół IPv6?

Tak. Można skonfigurować ograniczenie adresów IP dla protokołów IPv4 i IPv6.

## <a name="how-up-to-date-are-the-managed-rule-sets"></a>Jak aktualne są zarządzane zestawy reguł?

Naszym najlepszym rozwiązaniem jest zmiana krajobrazu zagrożenia. Po zaktualizowaniu nowej reguły zostanie ona dodana do domyślnego zestawu reguł przy użyciu nowego numeru wersji.

## <a name="what-is-the-propagation-time-if-i-make-a-change-to-my-waf-policy"></a>Jaki jest czas propagacji w przypadku wprowadzenia zmiany zasad WAFymi?

Wdrożenie zasad WAF globalnie zwykle trwa około 5 minut i często kończy się wkrótce.

## <a name="can-waf-policies-be-different-for-different-regions"></a>Czy zasady WAF mogą być różne dla różnych regionów?

Po zintegrowaniu z przednim drzwi WAF jest zasobem globalnym. Ta sama konfiguracja ma zastosowanie do wszystkich miejsc z przodu.
 
## <a name="how-do-i-limit-access-to-my-back-end-to-be-from-front-door-only"></a>Jak mogę ograniczyć dostęp do mojego zaplecza tylko z przodu?

Możesz skonfigurować listę Access Control IP w zapleczu, aby zezwolić tylko na wychodzące zakresy adresów IP z przodu i odmówić dostępu z Internetu. Tagi usług są obsługiwane, aby można było używać ich w sieci wirtualnej. Ponadto możesz sprawdzić, czy pole nagłówka HTTP X-forwardd-host jest prawidłowe dla Twojej aplikacji sieci Web.

## <a name="which-azure-waf-options-should-i-choose"></a>Które opcje usługi Azure WAF należy wybrać?

Istnieją dwie opcje stosowania zasad WAFymi na platformie Azure. WAF z platformą Azure front-drzwi to globalne, rozproszone rozwiązanie do zabezpieczeń. WAF with Application Gateway to regionalne, dedykowane rozwiązanie. Zalecamy wybranie rozwiązania na podstawie ogólnych wymagań dotyczących wydajności i zabezpieczeń. Aby uzyskać więcej informacji, zobacz [równoważenie obciążenia przy użyciu pakietu dostarczania aplikacji platformy Azure](../../frontdoor/front-door-lb-with-azure-app-delivery-suite.md).


## <a name="do-you-support-same-waf-features-in-all-integrated-platforms"></a>Czy te same funkcje WAF są obsługiwane we wszystkich zintegrowanych platformach?

Obecnie reguły ModSec KSR 2.2.9, KSR 3,0 i KSR 3,1 są obsługiwane tylko w Application Gateway. Reguły ograniczania, filtrowania geograficznego i zarządzanego zestawu reguł platformy Azure są obsługiwane tylko w przypadku WAF na platformie Azure.

## <a name="is-ddos-protection-integrated-with-front-door"></a>Czy ochrona DDoS jest zintegrowana z przednimi drzwiami? 

Ogólnie dystrybuowane na krawędziach sieci platformy Azure, drzwi frontonu platformy Azure mogą wchłonąć i geograficznie izolować duże ataki. Można utworzyć niestandardowe zasady WAF, aby automatycznie blokować i ograniczać liczbę ataków http (s), które mają znane sygnatury. Więcej informacji można DDoS Protection włączyć w sieci wirtualnej, w której zostaną wdrożone zaplecza. Klienci w Azure DDoS Protection standard otrzymują dodatkowe korzyści, w tym ochronę kosztów, gwarancję SLA i dostęp do ekspertów z zespołu szybkiego reagowania na DDoS w celu uzyskania natychmiastowej pomocy w trakcie ataku. Aby uzyskać więcej informacji, zobacz [DDoS Protection on the front drzwiczks](../../frontdoor/front-door-ddos.md).

## <a name="why-do-additional-requests-above-the-threshold-configured-for-my-rate-limit-rule-get-passed-to-my-backend-server"></a>Dlaczego dodatkowe żądania powyżej progu skonfigurowanego dla reguły limitu szybkości są przenoszone do serwera wewnętrznej bazy danych?

Reguła limitu szybkości może ograniczyć nietypowy ruch z dowolnego adresu IP klienta. Można skonfigurować próg liczby żądań sieci Web dozwolonych przez adres IP klienta w ciągu jednej minuty lub pięciu minut. W przypadku kontroli nad wskaźnikiem szczegółowości można łączyć ograniczenia szybkości z dodatkowymi warunkami dopasowania, takimi jak dopasowanie parametrów HTTP (S). 

Żądania od tego samego klienta często docierają do tego samego serwera front-drzwi. W takim przypadku zobaczysz dodatkowe żądania powyżej progu natychmiastowego zablokowania. 

Jednak jest możliwe, że żądania od tego samego klienta mogą dotrzeć do innego serwera frontonu, który nie odświeżył jeszcze licznika Limit szybkości. Na przykład klient może otworzyć nowe połączenie dla każdego żądania, a wartość progowa jest niska. W takim przypadku pierwsze żądanie do nowego serwera frontonu przejdzie do sprawdzenia limitu szybkości. Próg limitu szybkości jest zwykle ustawiony na wartość wysoki, aby chronić przed atakami typu "odmowa usługi" z dowolnego adresu IP klienta. W przypadku bardzo niskich progów mogą pojawić się dodatkowe żądania powyżej progu.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [zaporze aplikacji sieci Web platformy Azure](../overview.md).
- Dowiedz się więcej na temat [zewnętrznych drzwi platformy Azure](../../frontdoor/front-door-overview.md).