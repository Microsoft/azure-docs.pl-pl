---
title: Co to jest Azure Web Application Firewall na Azure Application Gateway?
titleSuffix: Azure Web Application Firewall
description: Ten artykuł zawiera omówienie usługi Web Application Firewall (WAF) na Application Gateway
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 12/04/2020
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: babd628280ebaee408d44dfacfaf6a5e14f57019
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107481826"
---
# <a name="what-is-azure-web-application-firewall-on-azure-application-gateway"></a>Co to jest Azure Web Application Firewall na Azure Application Gateway?

Azure Web Application Firewall (WAF) na platformie Azure Application Gateway zapewnia scentralizowaną ochronę aplikacji internetowych przed powszechnymi lukami w zabezpieczeniach i lukami w zabezpieczeniach. Aplikacje internetowe są coraz bardziej celem złośliwych ataków, które wykorzystują znane luki w zabezpieczeniach. Najczęściej spotykane ataki to iniekcja SQL i wykonywanie skryptów między witrynami.

WAF on Application Gateway jest oparta na podstawowym zestawie reguł [(CRS)](https://owasp.org/www-project-modsecurity-core-rule-set/) 3.1, 3.0 lub 2.2.9 z Open Web Application Security Project (OWASP). Wafaf automatically updates to include protection against new vulnerabilities, with no additional configuration needed. 

Wszystkie wymienione poniżej funkcje aplikacji sieci WAF istnieją wewnątrz zasad tej aplikacji. Można utworzyć wiele zasad i można je skojarzyć z Application Gateway, z poszczególnymi odbiornikami lub z regułami routingu opartymi na ścieżkach w Application Gateway. W ten sposób można mieć oddzielne zasady dla każdej witryny, za Application Gateway w razie potrzeby. Aby uzyskać więcej informacji na temat zasad WAF, [zobacz Create a WAF Policy (Tworzenie zasad WAF).](create-waf-policy-ag.md)

![Application Gateway diagramu WAF](../media/ag-overview/waf1.png)

Application Gateway działa jako kontroler dostarczania aplikacji (ADC). Oferuje ona protokół Transport Layer Security (TLS), wcześniej znany jako protokół Secure Sockets Layer (SSL), zakończenie, koligacja sesji na podstawie plików cookie, dystrybucję obciążenia działania okrężnego, routing oparty na zawartości, możliwość hostowania wielu witryn internetowych i ulepszenia zabezpieczeń.

Application Gateway zabezpieczeń obejmują zarządzanie zasadami TLS i end-to-end obsługę zabezpieczeń TLS. Zabezpieczenia aplikacji są wzmacniane przez integrację aplikacji sieci Web z Application Gateway. Kombinacja ta chroni aplikacje internetowe przed powszechnymi lukami w zabezpieczeniach. Zapewnia także łatwą do skonfigurowania centralną lokalizację do zarządzania.

## <a name="benefits"></a>Korzyści

W tej sekcji opisano podstawowe korzyści zapewniane przez Application Gateway WAF.

### <a name="protection"></a>Protection

* Chroń aplikacje internetowe przed internetowymi lukami w zabezpieczeniach i atakami bez modyfikacji kodu serwera końcowego.

* Ochrona wielu aplikacji internetowych w tym samym czasie. Wystąpienie usługi Application Gateway może hostować maksymalnie 40 witryn internetowych chronionych przez zaporę aplikacji internetowej.

* Tworzenie niestandardowych zasad aplikacji internetowych dla różnych witryn za tym samym węzłym aplikacji internetowych 

* Ochrona aplikacji internetowych przed złośliwymi botami przy użyciuetu reguł reputacji adresów IP (wersja zapoznawcza)

### <a name="monitoring"></a>Monitorowanie

* Monitoruj ataki na aplikacje internetowe przy użyciu dziennika aplikacji internetowych w czasie rzeczywistym. Dziennik jest zintegrowany z Azure Monitor [w](../../azure-monitor/overview.md) celu śledzenia alertów aplikacji sieci Azure Monitor i łatwego monitorowania trendów.

* Ta Application Gateway WAF jest zintegrowana z Azure Security Center. Security Center zapewnia centralny widok stanu zabezpieczeń wszystkich zasobów platformy Azure.

### <a name="customization"></a>Dostosowywanie

* Dostosuj reguły i grupy reguł aplikacji sieci Web do wymagań aplikacji i wyeliminuj wyniki fałszywie dodatnie.

* Kojarzenie zasad aplikacji internetowej dla każdej witryny za twoją witryną WAF w celu umożliwienia konfiguracji określonej lokacji

* Tworzenie reguł niestandardowych dostosowanych do potrzeb aplikacji

## <a name="features"></a>Funkcje

- Ochrona przed wstrzyknięciem kodu SQL.
- Ochrona skryptów między witrynami.
- Ochrona przed innymi powszechnymi atakami internetowymi, takimi jak iniekcja poleceń, dzielenia żądań HTTP, dzielenie odpowiedzi HTTP i zdalne dołączanie plików.
- Ochrona przed naruszeniami protokołu HTTP.
- Ochrona przed anomaliami protokołu HTTP, takimi jak brak hosta user-agent i akceptowanie nagłówków.
- Ochrona przed przeszukiwarami i skanerami.
- Wykrywanie typowych błędów konfiguracji aplikacji (na przykład Apache i IIS).
- Konfigurowalne limity rozmiaru żądania z dolną i górną granicą.
- Listy wykluczeń pozwalają pominąć niektóre atrybuty żądania w ocenie WAF. Częstym przykładem są tokeny wstawione przez usługę Active Directory, które są używane do uwierzytelniania lub w polach haseł.
- Tworzenie reguł niestandardowych dostosowanych do konkretnych potrzeb aplikacji.
- Filtrowanie geograficzne ruchu w celu umożliwienia lub zablokowania niektórym krajom/regionom dostępu do aplikacji. (wersja zapoznawcza)
- Chroń aplikacje przed botami za pomocą zasad ograniczania ryzyka dla botów. (wersja zapoznawcza)
- Inspekcja danych JSON i XML w treści żądania

## <a name="waf-policy-and-rules"></a>Zasady i reguły WAF

Aby włączyć Web Application Firewall na Application Gateway, należy utworzyć zasady WAF. Te zasady to miejsce, w którym istnieją wszystkie reguły zarządzane, reguły niestandardowe, wykluczenia i inne dostosowania, takie jak limit przekazywania plików.

Możesz skonfigurować zasady aplikacji internetowej i skojarzyć je z co najmniej jedną bramą aplikacji w celu ochrony. Zasady WAF składają się z dwóch typów reguł zabezpieczeń:

- Reguły niestandardowe, które tworzysz

- Zarządzane zestawy reguł, które są kolekcją wstępnie skonfigurowanego zestawu reguł zarządzanego przez platformę Azure

Jeśli obie reguły są obecne, reguły niestandardowe są przetwarzane przed rozpoczęciem przetwarzania reguł w zarządzanym zestawie reguł. Reguła składa się z warunku dopasowania, priorytetu i akcji. Obsługiwane typy akcji to ALLOW, BLOCK i LOG. Można utworzyć w pełni dostosowane zasady, które spełniają określone wymagania dotyczące ochrony aplikacji, łącząc reguły zarządzane i niestandardowe.

Reguły w ramach zasad są przetwarzane w kolejności priorytetów. Priorytet to unikatowa liczba całkowita, która definiuje kolejność reguł do przetwarzania. Mniejsza wartość całkowita oznacza wyższy priorytet, a te reguły są oceniane przed regułami o większej wartości całkowitej. Po dopasowaniu reguły do żądania jest stosowana odpowiednia akcja, która została zdefiniowana w określonej regułie. Po przetworzeniu takiego dopasowania reguły o niższych priorytetach nie są dalej przetwarzane.

Aplikacja internetowa dostarczana przez Application Gateway może mieć skojarzone z nią zasady aplikacji internetowej na poziomie globalnym, na poziomie witryny lub na poziomie uri.

### <a name="core-rule-sets"></a>Podstawowe zestawy reguł

Application Gateway obsługuje trzy zestawy reguł: CRS 3.1, CRS 3.0 i CRS 2.2.9. Te reguły chronią aplikacje internetowe przed złośliwymi działaniami.

Aby uzyskać więcej informacji, zobacz Grupy reguł i reguły CRS zapory [aplikacji internetowej.](application-gateway-crs-rulegroups-rules.md)

### <a name="custom-rules"></a>Reguły niestandardowe

Application Gateway obsługuje również reguły niestandardowe. Za pomocą reguł niestandardowych można tworzyć własne reguły, które są oceniane dla każdego żądania przechodzącego przez regułę WAF. Te reguły mają wyższy priorytet niż pozostałe reguły w zarządzanych zestawach reguł. Jeśli zostanie spełniony zestaw warunków, zostanie podjęta akcja zezwalania lub blokowania. 

Operator geomatch jest teraz dostępny w publicznej wersji zapoznawczej dla reguł niestandardowych. Aby uzyskać więcej informacji, zobacz reguły niestandardowe [geomatchu.](custom-waf-rules-overview.md#geomatch-custom-rules-preview)

> [!NOTE]
> Operator geomatch dla reguł niestandardowych jest obecnie w publicznej wersji zapoznawczej i jest dostarczany z umową poziomu usług w wersji zapoznawczej. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać szczegółowe informacje, zobacz [Dodatkowe warunki użytkowania wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Aby uzyskać więcej informacji na temat reguł niestandardowych, zobacz [Reguły niestandardowe dla Application Gateway.](custom-waf-rules-overview.md)

### <a name="bot-mitigation-preview"></a>Środki zaradcze dla botów (wersja zapoznawcza)

Zarządzany zestaw reguł ochrony przed botami można włączyć dla aplikacji internetowych, aby blokować lub rejestrować żądania ze znanych złośliwych adresów IP wraz z zarządzanym zestawem reguł. Adresy IP pochodzą z kanału informacyjnego analizy zagrożeń firmy Microsoft. System Intelligent Security Graph obsługuje analizę zagrożeń firmy Microsoft i jest używany przez wiele usług, w tym Azure Security Center.

> [!NOTE]
> Zestaw reguł ochrony przed botami jest obecnie w publicznej wersji zapoznawczej i jest dostarczany z umową poziomu usług w wersji zapoznawczej. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać szczegółowe informacje, zobacz [Dodatkowe warunki użytkowania wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Jeśli ochrona przed botami jest włączona, żądania przychodzące zgodne z ip klienta złośliwego bota są rejestrowane w dzienniku zapory, zobacz więcej informacji poniżej. Dostęp do dzienników aplikacji internetowej można uzyskać z konta magazynu, centrum zdarzeń lub analizy dzienników. 

### <a name="waf-modes"></a>Trybyaf (WAF)

Aplikację Application Gateway WAF można skonfigurować do uruchamiania w następujących dwóch trybach:

* **Tryb wykrywania:** monitoruje i rejestruje wszystkie alerty o zagrożeniach. Możesz włączyć diagnostykę rejestrowania dla Application Gateway w **sekcji Diagnostyka.** Należy również upewnić się, że dziennik WAF jest wybrany i włączony. Zapora aplikacji internetowej nie blokuje żądań przychodzących, gdy działa w trybie wykrywania.
* **Tryb zapobiegania:** blokuje włamania i ataki wykrywane przez reguły. Osoba atakująca otrzymuje wyjątek "403 brak autoryzacji dostępu" i połączenie jest zamykane. Tryb zapobiegania rejestruje takie ataki w dziennikach WAF.

> [!NOTE]
> Zaleca się uruchomienie nowo wdrożonej aplikacji sieci WAF w trybie wykrywania przez krótki czas w środowisku produkcyjnym. Dzięki temu można uzyskać dzienniki [zapory i](../../application-gateway/application-gateway-diagnostics.md#firewall-log) zaktualizować wszelkie wyjątki lub [reguły](./custom-waf-rules-overview.md) niestandardowe przed przejściem do trybu zapobiegania. Może to pomóc w skróceniu występowania nieoczekiwanego zablokowanego ruchu.

### <a name="anomaly-scoring-mode"></a>Tryb oceniania anomalii

OWASP ma dwa tryby podejmowania decyzji, czy zablokować ruch: tryb tradycyjny i tryb oceniania anomalii.

W trybie tradycyjnym ruch, który pasuje do dowolnej reguły, jest traktowany niezależnie od innych dopasowania reguł. Ten tryb jest łatwy do zrozumienia. Jednak brak informacji o tym, ile reguł pasuje do określonego żądania, jest ograniczeniem. Wprowadzono więc tryb oceniania anomalii. Jest to ustawienie domyślne dla programu OWASP 3. *x*.

W trybie oceniania anomalii ruch, który pasuje do dowolnej reguły, nie jest natychmiast blokowany, gdy zapora jest w trybie zapobiegania. Reguły mają określoną ważność: *Krytyczne,* *Błąd,* *Ostrzeżenie* lub *Powiadomienie*. Ta ważność ma wpływ na wartość liczbową żądania, która jest nazywana wynikiem anomalii. Na przykład jedno *dopasowanie reguły* ostrzeżenia wpływa na wynik 3. Jedno *dopasowanie* reguły krytycznej przyczynia się do 5.

|Ważność  |Wartość  |
|---------|---------|
|Krytyczne     |5|
|Error        |4|
|Ostrzeżenie      |3|
|Zauważyć       |2|

Istnieje próg 5 dla wyniku anomalii blokującego ruch. Dlatego pojedyncze dopasowanie reguły *krytycznej* jest wystarczające, aby Application Gateway WAF blokowała żądanie, nawet w trybie zapobiegania. Jednak jedno *dopasowanie reguły* ostrzeżenia zwiększa tylko wynik anomalii o 3, co samo w sobie nie wystarczy do zablokowania ruchu.

> [!NOTE]
> Komunikat rejestrowany, gdy reguła WAF pasuje do ruchu, zawiera wartość akcji "Zablokowane". Jednak ruch jest w rzeczywistości blokowany tylko dla wyniku anomalii o wartości 5 lub wyższej. Aby uzyskać więcej informacji, zobacz [Troubleshoot Web Application Firewall (WAF) for Azure Application Gateway](web-application-firewall-troubleshoot.md#understanding-waf-logs)(Rozwiązywanie problemów z Azure Application Gateway ). 

### <a name="waf-monitoring"></a>Monitorowanie aplikacji sieci WAF

Monitorowanie kondycji bramy Application Gateway jest ważne. Monitorowanie kondycji aplikacji internetowych i aplikacji, które chroni, jest obsługiwane przez integrację z Azure Security Center, Azure Monitor i Azure Monitor danych.

![Diagram przedstawiający diagnostykę Application Gateway WAF](../media/ag-overview/diagnostics.png)

#### <a name="azure-monitor"></a>Azure Monitor

Application Gateway są zintegrowane z Azure Monitor [.](../../azure-monitor/overview.md) Dzięki temu można śledzić informacje diagnostyczne, w tym alerty i dzienniki WAF. Dostęp do tej funkcji można uzyskać na karcie **Diagnostyka** w zasobie Application Gateway w portalu lub bezpośrednio za pośrednictwem Azure Monitor. Aby dowiedzieć się więcej na temat włączania dzienników, zobacz [Application Gateway diagnostyki.](../../application-gateway/application-gateway-diagnostics.md)

#### <a name="azure-security-center"></a>Azure Security Center

[Security Center](../../security-center/security-center-introduction.md) pomaga zapobiegać zagrożeniom, wykrywać je i reagować na nie. Zapewnia ona większy wgląd w zabezpieczenia zasobów platformy Azure i kontrolę nad ich zabezpieczeniami. Application Gateway jest [zintegrowany z programem Security Center](../../security-center/security-center-partner-integration.md#integrated-azure-security-solutions). Security Center skanuje środowisko w celu wykrycia niechronionych aplikacji internetowych. Może ona zalecić Application Gateway WAF w celu ochrony tych narażonych zasobów. Zapory tworzy się bezpośrednio z Security Center. Te wystąpienia aplikacji sieci WAF są zintegrowane z Security Center. Wysyłają alerty i informacje o kondycji do Security Center do raportowania.

![Security Center omówienie](../media/ag-overview/figure1.png)

#### <a name="azure-sentinel"></a>Usługa Azure Sentinel

Microsoft Azure Sentinel to skalowalne, natywne dla chmury rozwiązanie do zarządzania zdarzeniami informacji o zabezpieczeniach (SIEM) i zautomatyzowanej odpowiedzi orkiestracji zabezpieczeń (SOAR). Azure Sentinel zapewnia inteligentną analizę zabezpieczeń i analizę zagrożeń w całym przedsiębiorstwie, zapewniając jedno rozwiązanie do wykrywania alertów, widoczności zagrożeń, proaktywnego wykrywania zagrożeń i reagowania na zagrożenia.

Za pomocą wbudowanego skoroszytu zdarzeń zapory zapory aplikacji internetowej platformy Azure można uzyskać przegląd zdarzeń zabezpieczeń w zaporze aplikacji internetowej. Obejmuje to zdarzenia, dopasowane i zablokowane reguły oraz wszystkie inne zdarzenia rejestrowane w dziennikach zapory. Więcej informacji na temat rejestrowania znajduje się poniżej. 


![Skoroszyt zdarzeń zapory zapory zapory aplikacji internetowej platformy Azure](../media/ag-overview/sentinel.png)


#### <a name="azure-monitor-workbook-for-waf"></a>Azure Monitor Workbook for WAF

Ten skoroszyt umożliwia niestandardową wizualizację zdarzeń aplikacji sieci Web istotnych dla bezpieczeństwa w kilku panelach z możliwością filtrowania. Działa ona ze wszystkimi typami aplikacji sieci WAF, w tym Application Gateway, Front Door i CDN, i może być filtrowana na podstawie typu aplikacji sieci WAF lub określonego wystąpienia tej aplikacji. Importowanie za pomocą szablonu usługi ARM lub szablonu galerii. Aby wdrożyć ten skoroszyt, zobacz [Skoroszyt aplikacji sieci Web](https://aka.ms/AzWAFworkbook).

#### <a name="logging"></a>Rejestrowanie

Application Gateway WAF udostępnia szczegółowe raporty dotyczące każdego wykrywanych zagrożeń. Rejestrowanie jest zintegrowane z Diagnostyka Azure dziennikami. Alerty są rejestrowane w formacie JSON. Te dzienniki można zintegrować z Azure Monitor [dziennikami.](../../azure-monitor/insights/azure-networking-analytics.md)

![Application Gateway okien dzienników diagnostycznych](../media/ag-overview/waf2.png)

```json
{
  "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupId}/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/{appGatewayName}",
  "operationName": "ApplicationGatewayFirewall",
  "time": "2017-03-20T15:52:09.1494499Z",
  "category": "ApplicationGatewayFirewallLog",
  "properties": {
    {
      "instanceId": "ApplicationGatewayRole_IN_0",
      "clientIp": "52.161.109.145",
      "clientPort": "0",
      "requestUri": "/",
      "ruleSetType": "OWASP",
      "ruleSetVersion": "3.0",
      "ruleId": "920350",
      "ruleGroup": "920-PROTOCOL-ENFORCEMENT",
      "message": "Host header is a numeric IP address",
      "action": "Matched",
      "site": "Global",
      "details": {
        "message": "Warning. Pattern match \"^[\\\\d.:]+$\" at REQUEST_HEADERS:Host ....",
        "data": "127.0.0.1",
        "file": "rules/REQUEST-920-PROTOCOL-ENFORCEMENT.conf",
        "line": "791"
      },
      "hostname": "127.0.0.1",
      "transactionId": "16861477007022634343"
      "policyId": "/subscriptions/1496a758-b2ff-43ef-b738-8e9eb5161a86/resourceGroups/drewRG/providers/Microsoft.Network/ApplicationGatewayWebApplicationFirewallPolicies/globalWafPolicy",
      "policyScope": "Global",
      "policyScopeName": " Global "
    }
  }
} 

```

## <a name="application-gateway-waf-sku-pricing"></a>Cena jednostki SKU zapory aplikacji sieci Web w usłudze Application Gateway

Modele cenowe są różne dla WAF_v1 i WAF_v2 SKU. Aby dowiedzieć się [więcej, zobacz Application Gateway](https://azure.microsoft.com/pricing/details/application-gateway/) cennik. 

## <a name="whats-new"></a>Co nowego

Aby dowiedzieć się, co nowego w programie Azure Web Application Firewall, zobacz [Aktualizacje platformy Azure.](https://azure.microsoft.com/updates/?category=networking&query=Web%20Application%20Firewall)

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej na [temat reguł zarządzanych przez usługę WAF](application-gateway-crs-rulegroups-rules.md)
- Dowiedz się więcej o [zasadach niestandardowych](custom-waf-rules-overview.md)
- Dowiedz się [więcej Web Application Firewall na Azure Front Door](../afds/afds-overview.md)
