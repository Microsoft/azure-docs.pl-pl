---
title: Zapora aplikacji sieci Web platformy Azure na Application Gateway — często zadawane pytania
description: Ten artykuł zawiera odpowiedzi na często zadawane pytania dotyczące zapory aplikacji sieci Web w systemie Application Gateway
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: article
ms.date: 05/05/2020
ms.author: victorh
ms.openlocfilehash: 890688dba70a7fa654e97652b3e474b919f9a077
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104585387"
---
# <a name="frequently-asked-questions-for-azure-web-application-firewall-on-application-gateway"></a>Często zadawane pytania dotyczące zapory aplikacji sieci Web platformy Azure w systemie Application Gateway

W tym artykule znajdują się odpowiedzi na często zadawane pytania dotyczące usługi Azure Web Application Firewall (WAF) na Application Gateway funkcje i funkcje. 

## <a name="what-is-azure-waf"></a>Co to jest Azure WAF?

Azure WAF to Zapora aplikacji sieci Web, która pomaga chronić aplikacje sieci Web przed typowymi zagrożeniami, takimi jak iniekcja SQL, skrypty między lokacjami i inne sieci Web. Można zdefiniować zasady WAF, które składają się z kombinacji reguł niestandardowych i zarządzanych w celu kontrolowania dostępu do aplikacji sieci Web.

Zasady usługi Azure WAF można stosować do aplikacji sieci Web hostowanych na Application Gateway lub na drzwiach frontonu platformy Azure.

## <a name="what-features-does-the-waf-sku-support"></a>Jakie funkcje obsługuje jednostka SKU WAF?

Jednostka SKU WAF obsługuje wszystkie funkcje dostępne w ramach standardowej jednostki SKU.

## <a name="how-do-i-monitor-waf"></a>Jak mogę monitorować WAF?

Monitoruj WAF za poorednictwem rejestrowania diagnostycznego. Aby uzyskać więcej informacji, zobacz [rejestrowanie diagnostyczne i metryki dla Application Gateway](../../application-gateway/application-gateway-diagnostics.md).

## <a name="does-detection-mode-block-traffic"></a>Czy tryb wykrywania blokuje ruch?

Nie. Tryb wykrywania rejestruje tylko ruch wyzwalający regułę WAF.

## <a name="can-i-customize-waf-rules"></a>Czy mogę dostosować reguły WAF?

Tak. Aby uzyskać więcej informacji, zobacz [Dostosowywanie grup reguł i reguł WAF](application-gateway-customize-waf-rules-portal.md).

## <a name="what-rules-are-currently-available-for-waf"></a>Jakie reguły są obecnie dostępne dla WAF?

WAF obecnie obsługuje KSR [2.2.9](application-gateway-crs-rulegroups-rules.md#owasp229), [3,0](application-gateway-crs-rulegroups-rules.md#owasp30)i [3,1](application-gateway-crs-rulegroups-rules.md#owasp31). Te reguły zapewniają zabezpieczenia bazowe przed większością luk 10 najważniejszych, które są używane do otwierania projektu zabezpieczeń aplikacji sieci Web (OWASP): 

* Ochrona przed atakami polegającymi na iniekcji SQL
* Ochrona skryptów między lokacjami
* Ochrona przed typowymi atakami sieci Web, takimi jak iniekcja poleceń, przemycie żądań HTTP, dzielenie odpowiedzi HTTP i ataki plików zdalnych
* Ochrona przed naruszeniami protokołu HTTP
* Ochrona przed nieprawidłowościami protokołu HTTP, takimi jak brakujące powiązania agenta i użytkownika hosta oraz akceptowanie nagłówków
* Zapobieganie atakom z użyciem robotów, przeszukiwarek i skanerów
* Wykrywanie typowych niespotykanych konfiguracji aplikacji (to jest Apache, IIS itd.)

Aby uzyskać więcej informacji, zobacz temat [OWASPs Top-10](https://owasp.org/www-project-top-ten/).

## <a name="what-content-types-does-waf-support"></a>Jakie typy zawartości obsługuje WAF?

Application Gateway WAF obsługują następujące typy zawartości dla reguł zarządzanych:

* application/json
* Aplikacja/XML
* aplikacja/x-www-form-urlencoded
* wieloczęściowe/formularz-dane

I dla reguł niestandardowych:

* aplikacja/x-www-form-urlencoded
* wieloczęściowe/formularz-dane

## <a name="does-waf-support-ddos-protection"></a>Czy WAF obsługuje ochronę DDoS?

Tak. Ochronę DDoS można włączyć w sieci wirtualnej, w której wdrożono bramę aplikacji. To ustawienie zapewnia, że usługa Azure DDoS Protection chroni również wirtualny adres IP bramy aplikacji (VIP).

### <a name="does-waf-store-customer-data"></a>Czy WAF przechowywać dane klienta?

Nie, WAF nie przechowuje danych klienta.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [zaporze aplikacji sieci Web platformy Azure](../overview.md).
- Dowiedz się więcej na temat [zewnętrznych drzwi platformy Azure](../../frontdoor/front-door-overview.md).
