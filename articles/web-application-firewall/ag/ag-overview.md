---
title: Co to jest Zapora aplikacji sieci Web platformy Azure w usłudze Azure Application Gateway?
titleSuffix: Azure Web Application Firewall
description: Ten artykuł zawiera omówienie zapory aplikacji sieci Web (WAF) na Application Gateway
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 09/16/2020
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: 050252718e4796ff20d57be3fdeac98f0cf04fdf
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/28/2020
ms.locfileid: "92785225"
---
# <a name="what-is-azure-web-application-firewall-on-azure-application-gateway"></a>Co to jest Zapora aplikacji sieci Web platformy Azure w usłudze Azure Application Gateway?

Zapora aplikacji sieci Web platformy Azure (WAF) na platformie Azure Application Gateway zapewnia scentralizowaną ochronę aplikacji sieci Web przed typowymi atakami i lukami w zabezpieczeniach. Aplikacje sieci Web są coraz bardziej przeznaczone dla złośliwych ataków wykorzystujących często znane luki w zabezpieczeniach. Iniekcja kodu SQL i skryptów między lokacjami są między Najczęściej spotykanymi atakami.

WAF na Application Gateway opiera się na [podstawowym zestawie reguł (KSR)](https://owasp.org/www-project-modsecurity-core-rule-set/) 3,1, 3,0 lub 2.2.9 z projektu Open Web Application Security (OWASP). WAF automatycznie aktualizuje, aby uwzględnić ochronę przed nowymi lukami w zabezpieczeniach, bez konieczności dodatkowej konfiguracji. 

Wszystkie funkcje WAF wymienione poniżej istnieją w zasadach WAF. Można utworzyć wiele zasad i można je skojarzyć z Application Gateway, do poszczególnych odbiorników lub regułami routingu opartymi na ścieżce na Application Gateway. W ten sposób można mieć osobne zasady dla każdej witryny za Application Gateway w razie potrzeby. Aby uzyskać więcej informacji na temat zasad WAFymi, zobacz [Tworzenie zasad WAF](create-waf-policy-ag.md).

   > [!NOTE]
   > Zasady WAF dla identyfikatorów URI są dostępne w publicznej wersji zapoznawczej. Oznacza to, że ta funkcja podlega dodatkowym warunkom użytkowania firmy Microsoft. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

![Application Gateway diagram WAF](../media/ag-overview/waf1.png)

Application Gateway działa jako kontroler dostarczania aplikacji (ADC). Oferuje on Transport Layer Security (TLS), wcześniej znany jako SSL (SSL), zakończenie, koligację sesji na podstawie plików cookie, dystrybucję obciążeń z uwzględnieniem zawartości, możliwość hostowania wielu witryn sieci Web i ulepszeń zabezpieczeń.

Ulepszenia zabezpieczeń Application Gateway obejmują zarządzanie zasadami protokołu TLS i kompleksową obsługę protokołu TLS. Zabezpieczenia aplikacji są wzmacniane przez integrację WAF w Application Gateway. Połączenie chroni aplikacje sieci Web przed typowymi lukami w zabezpieczeniach. Udostępnia ona łatwą do skonfigurowania centralną lokalizację do zarządzania.

## <a name="benefits"></a>Korzyści

W tej sekcji opisano podstawowe korzyści, które WAF na Application Gateway.

### <a name="protection"></a>Protection

* Ochrona aplikacji sieci Web przed lukami w zabezpieczeniach i atakami w sieci Web bez konieczności modyfikowania kodu zaplecza.

* Ochrona wielu aplikacji sieci Web w tym samym czasie. Wystąpienie Application Gateway może obsługiwać do 40 witryn sieci Web, które są chronione przez zaporę aplikacji internetowej.

* Tworzenie niestandardowych zasad WAF dla różnych lokacji w tym samym WAF 

* Ochrona aplikacji sieci Web przed złośliwym botów z zestawem reguł reputacji adresów IP (wersja zapoznawcza)

### <a name="monitoring"></a>Monitorowanie

* Monitoruj ataki do aplikacji sieci Web przy użyciu dziennika WAF w czasie rzeczywistym. Dziennik jest zintegrowany z [Azure monitor](../../azure-monitor/overview.md) do śledzenia alertów WAF i łatwego monitorowania trendów.

* Application Gateway WAF jest zintegrowany z Azure Security Center. Security Center stanowi centralny widok stanu zabezpieczeń wszystkich zasobów platformy Azure.

### <a name="customization"></a>Dostosowywanie

* Dostosuj reguły WAF i grupy reguł, aby spełniały wymagania aplikacji i wyeliminować fałszywe pozytywne.

* Skojarz zasady WAF z lokacjami za WAF, aby umożliwić konfigurację specyficzną dla lokacji

* Tworzenie reguł niestandardowych w celu dostosowania ich do potrzeb aplikacji

## <a name="features"></a>Funkcje

- Ochrona iniekcji SQL.
- Ochrona skryptów między lokacjami.
- Ochrona przed innymi typowymi atakami sieci Web, takimi jak iniekcja poleceń, mycie żądań HTTP, dzielenie odpowiedzi HTTP i zdalne Dołączanie plików.
- Ochrona przed naruszeniami protokołu HTTP.
- Ochrona przed anomaliami protokołu HTTP, takimi jak brak agenta użytkownika hosta i akceptowanie nagłówków.
- Ochrona przed przeszukiwarkami i skanerami.
- Wykrywanie typowych niezwiązanych konfiguracji aplikacji (np. Apache i IIS).
- Konfigurowalne limity rozmiaru żądań z dolną i górną granicą.
- Listy wykluczeń pozwalają pominąć niektóre atrybuty żądania z oceny WAF. Typowym przykładem są tokeny wstawione Active Directory, które są używane dla pól uwierzytelniania lub hasła.
- Utwórz reguły niestandardowe, aby odpowiadały określonym potrzebom aplikacji.
- Ruch w filtrach geograficznych, aby zezwalać na dostęp do aplikacji lub blokować je określonym krajom/regionom. (wersja zapoznawcza)
- Chroń aplikacje przed botów za pomocą zestawu reguł ograniczenia bot. (wersja zapoznawcza)
- Sprawdzanie danych JSON i XML w treści żądania

## <a name="waf-policy-and-rules"></a>Zasady i reguły WAF

Aby włączyć zaporę aplikacji sieci Web na Application Gateway, należy utworzyć zasady WAFymi. Ta zasada polega na tym, że istnieją wszystkie reguły zarządzane, reguły niestandardowe, wykluczenia i inne dostosowania, takie jak limit przekazywania plików.

Można skonfigurować zasady WAF i skojarzyć te zasady z co najmniej jedną bramą aplikacji do ochrony. Zasady WAF obejmują dwa typy reguł zabezpieczeń:

- Niestandardowe reguły, które tworzysz

- Zarządzane zestawy reguł, które są zbiorem wstępnie skonfigurowanego zestawu reguł zarządzanego przez platformę Azure

Gdy obie są obecne, reguły niestandardowe są przetwarzane przed przetworzeniem reguł w zarządzanym zestawie reguł. Reguła zawiera warunek dopasowania, priorytet i akcję. Obsługiwane typy akcji to: Zezwalaj, Blokuj i Rejestruj. Można utworzyć w pełni dostosowane zasady spełniające określone wymagania dotyczące ochrony aplikacji przez połączenie reguł zarządzanych i niestandardowych.

Reguły w ramach zasad są przetwarzane w kolejności priorytetów. Priorytet jest unikatową liczbą całkowitą, która definiuje kolejność reguł do przetworzenia. Mniejsza wartość całkowita oznacza wyższy priorytet, a reguły są oceniane przed regułami o wyższej wartości całkowitej. Po dopasowaniu reguły odpowiednia akcja zdefiniowana w regule zostanie zastosowana do żądania. Gdy takie dopasowanie zostanie przetworzone, reguły o niższych priorytetach nie są przetwarzane więcej.

W aplikacji sieci Web dostarczonej przez Application Gateway mogą być skojarzone zasady WAF na poziomie globalnym, na poziomie poszczególnych lokacji lub na poziomie poszczególnych identyfikatorów URI.

### <a name="core-rule-sets"></a>Podstawowe zestawy reguł

Application Gateway obsługuje trzy zestawy reguł: KSR 3,1, KSR 3,0 i 2.2.9 KSR. Te reguły chronią aplikacje sieci Web przed złośliwymi działaniami.

Aby uzyskać więcej informacji, zobacz reguły [i reguły dotyczące reguł KSR aplikacji sieci Web](application-gateway-crs-rulegroups-rules.md).

### <a name="custom-rules"></a>Reguły niestandardowe

Application Gateway obsługuje także reguły niestandardowe. Za pomocą reguł niestandardowych można utworzyć własne reguły, które są oceniane dla każdego żądania, które przechodzi przez WAF. Reguły te mają wyższy priorytet niż pozostałe reguły w zarządzanych zestawach reguł. W przypadku spełnienia zestawu warunków akcja jest podejmowana do zezwalania lub blokowania. 

Operator geodopasowania jest teraz dostępny w publicznej wersji zapoznawczej dla reguł niestandardowych. Aby uzyskać więcej informacji, zobacz temat [reguły niestandardowe geodopasowania](custom-waf-rules-overview.md#geomatch-custom-rules-preview) .

> [!NOTE]
> Operator geodopasowania dla reguł niestandardowych jest obecnie w publicznej wersji zapoznawczej i jest dostarczany z umową dotyczącą poziomu usług w wersji zapoznawczej. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać szczegółowe informacje, zobacz [Dodatkowe warunki użytkowania wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Aby uzyskać więcej informacji na temat reguł niestandardowych, zobacz [reguły niestandardowe dla Application Gateway.](custom-waf-rules-overview.md)

### <a name="bot-mitigation-preview"></a>Bot (wersja zapoznawcza)

Zarządzany zestaw reguł ochrony bot można włączyć dla WAF, aby blokować lub rejestrować żądania ze znanych złośliwych adresów IP obok zarządzanego zestawu reguł. Adresy IP pochodzą z kanału informacyjnego analizy zagrożeń firmy Microsoft. System Intelligent Security Graph obsługuje analizę zagrożeń firmy Microsoft i jest używany przez wiele usług, w tym Azure Security Center.

> [!NOTE]
> Zestaw reguł ochrony bot jest obecnie w publicznej wersji zapoznawczej i jest dostarczany z umową dotyczącą poziomu usług w wersji zapoznawczej. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać szczegółowe informacje, zobacz [Dodatkowe warunki użytkowania wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Jeśli ochrona bot jest włączona, przychodzące żądania zgodne ze złośliwymi adresami IP klientów bot są rejestrowane w dzienniku zapory, Zobacz więcej informacji poniżej. Użytkownik może uzyskać dostęp do dzienników WAF z konta magazynu, centrum zdarzeń lub usługi log Analytics. 

### <a name="waf-modes"></a>Tryby WAF

WAF Application Gateway można skonfigurować do uruchamiania w następujących dwóch trybach:

* **Tryb wykrywania** : służy do monitorowania i rejestrowania wszystkich alertów dotyczących zagrożeń. Należy włączyć diagnostykę rejestrowania dla Application Gateway w sekcji **Diagnostyka** . Należy również upewnić się, że dziennik WAF został wybrany i włączony. Zapora aplikacji sieci Web nie blokuje żądań przychodzących, gdy działa w trybie wykrywania.
* **Tryb zapobiegania** : blokuje wtargnięcie i ataki wykrywane przez zasady. Osoba atakująca otrzymuje wyjątek "403 nieautoryzowany dostęp", a połączenie jest zamknięte. Tryb zapobiegania rejestruje takie ataki w dziennikach WAF.

> [!NOTE]
> Zaleca się uruchamianie nowo wdrożonych WAF w trybie wykrywania przez krótki czas w środowisku produkcyjnym. Zapewnia to możliwość uzyskiwania [dzienników zapory](../../application-gateway/application-gateway-diagnostics.md#firewall-log) i aktualizowania wyjątków lub [reguł niestandardowych](./custom-waf-rules-overview.md) przed przejściem do trybu zapobiegania. Może to pomóc w zmniejszeniu wystąpienia nieoczekiwanego zablokowanego ruchu.

### <a name="anomaly-scoring-mode"></a>Tryb oceniania anomalii

OWASP ma dwa tryby decydowania, czy ruch ma być blokowany: tryb tradycyjny i tryb oceniania anomalii.

W trybie tradycyjnym ruch pasujący do dowolnej reguły jest traktowany niezależnie od innych dopasowań reguł. Ten tryb jest łatwy do zrozumienia. Jednak brak informacji o liczbie reguł zgodnych z określonym żądaniem jest ograniczeniem. Dlatego wprowadzono tryb oceniania anomalii. Jest to wartość domyślna dla OWASP 3. *x* .

W trybie oceniania anomalii ruch pasujący do dowolnej reguły nie jest natychmiast blokowany, gdy Zapora działa w trybie zapobiegania. Reguły mają określoną wagę: *krytyczny* , *błąd* , *Ostrzeżenie* lub *powiadomienie* . Ważność ta ma wpływ na wartość liczbową żądania, która jest nazywana wynikiem anomalii. Na przykład jedna reguła *ostrzegawcza* współtworzy wynik 3. Jedna zgodność z regułą *krytyczną* współtworzy 5.

|Ważność  |Wartość  |
|---------|---------|
|Krytyczne     |5|
|Error        |4|
|Ostrzeżenie      |3|
|Wzory       |2|

Istnieje próg 5 dla wyniku anomalii, aby blokować ruch. Tak więc pojedyncze dopasowanie reguły *krytycznej* jest wystarczające dla Application Gateway WAF do zablokowania żądania, nawet w trybie zapobiegania. Ale jedna reguła *ostrzegawcza* dopasowuje tylko wynik anomalii o wartości 3, która nie wystarcza do blokowania ruchu.

> [!NOTE]
> Komunikat rejestrowany, gdy reguła WAF jest zgodna z ruchem, zawiera wartość akcji "zablokowana". Jednak ruch jest blokowany tylko dla wyniku anomalii o wartości 5 lub wyższej.  

### <a name="waf-monitoring"></a>Monitorowanie WAF

Monitorowanie kondycji bramy Application Gateway jest ważne. Monitorowanie kondycji WAF i aplikacji, które chroni, jest obsługiwane przez integrację z dziennikami Azure Security Center, Azure Monitor i Azure Monitor.

![Diagram diagnostyki Application Gateway WAF](../media/ag-overview/diagnostics.png)

#### <a name="azure-monitor"></a>Azure Monitor

Dzienniki Application Gateway są zintegrowane z [Azure monitor](../../azure-monitor/overview.md). Pozwala to na śledzenie informacji diagnostycznych, w tym alertów WAF i dzienników. Możesz uzyskać dostęp do tej funkcji na karcie **Diagnostyka** w zasobie Application Gateway w portalu lub bezpośrednio za pomocą Azure monitor. Aby dowiedzieć się więcej na temat włączania dzienników, zobacz [Application Gateway Diagnostics](../../application-gateway/application-gateway-diagnostics.md).

#### <a name="azure-security-center"></a>Azure Security Center

[Security Center](../../security-center/security-center-intro.md) pomaga zapobiegać zagrożeniom, wykrywać je i reagować na nie. Zapewnia ona lepszy wgląd w zabezpieczenia zasobów platformy Azure i kontrolę nad nimi. Application Gateway jest [zintegrowana z Security Center](../../application-gateway/application-gateway-integration-security-center.md). Security Center skanuje środowisko w celu wykrywania niechronionych aplikacji sieci Web. Może być zalecane Application Gateway WAF do ochrony tych zasobów. Zapory są tworzone bezpośrednio z Security Center. Te wystąpienia WAF są zintegrowane z Security Center. Wysyłają one alerty i informacje o kondycji w celu Security Center na potrzeby raportowania.

![Okno przeglądu Security Center](../media/ag-overview/figure1.png)

#### <a name="azure-sentinel"></a>Usługa Azure Sentinel

Microsoft Azure wskaźnikiem wydajności jest skalowalne i natywne rozwiązanie do zarządzania zdarzeniami zabezpieczeń (SIEM) i o (Security Orchestration). Platforma Azure — wskaźnik przedstawia inteligentne rozwiązania w zakresie zabezpieczeń i analizy zagrożeń w całym przedsiębiorstwie, zapewniając jedno rozwiązanie do wykrywania alertów, widoczności zagrożeń, aktywnego polowania i reagowania na zagrożenia.

Korzystając z wbudowanego skoroszytu WAF zdarzeń zapory platformy Azure, możesz zapoznać się z omówieniem zdarzeń zabezpieczeń w WAF. Obejmuje to zdarzenia, dopasowane i zablokowane reguły oraz wszystkie inne, które są rejestrowane w dziennikach zapory. Zobacz więcej na temat rejestrowania poniżej. 


![Skoroszyt zdarzeń zapory platformy Azure WAF](../media/ag-overview/sentinel.png)


#### <a name="azure-monitor-workbook-for-waf"></a>Azure Monitor skoroszyt dla WAF

Ten skoroszyt umożliwia niestandardowe wizualizacje zdarzeń WAF związanych z zabezpieczeniami w kilku panelach z możliwością filtrowania. Działa ze wszystkimi typami WAF, w tym Application Gateway, z przodu i sieci CDN, i można je filtrować na podstawie typu WAF lub określonego wystąpienia WAF. Importowanie za pomocą szablonu ARM lub szablonu galerii. Aby wdrożyć ten skoroszyt, zobacz [skoroszyt WAF](https://github.com/Azure/Azure-Network-Security/tree/master/Azure%20WAF/Azure%20Monitor%20Workbook).

#### <a name="logging"></a>Rejestrowanie

Application Gateway WAF udostępnia szczegółowe raporty dotyczące każdego wykrytego zagrożenia. Rejestrowanie jest zintegrowane z dziennikami Diagnostyka Azure. Alerty są rejestrowane w formacie JSON. Te dzienniki można zintegrować z [dziennikami Azure monitor](../../azure-monitor/insights/azure-networking-analytics.md).

![Okna dzienników diagnostyki Application Gateway](../media/ag-overview/waf2.png)

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

Modele cen są różne dla WAF_v1 i WAF_v2 jednostek SKU. Aby dowiedzieć się więcej, zobacz stronę z [cennikiem Application Gateway](https://azure.microsoft.com/pricing/details/application-gateway/) . 

## <a name="whats-new"></a>Co nowego?

Aby dowiedzieć się, co nowego w zaporze aplikacji sieci Web platformy Azure, zobacz [aktualizacje platformy Azure](https://azure.microsoft.com/updates/?category=networking&query=Web%20Application%20Firewall).

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [regułach zarządzanych przez WAF](application-gateway-crs-rulegroups-rules.md)
- Dowiedz się więcej na temat [reguł niestandardowych](custom-waf-rules-overview.md)
- Dowiedz się więcej o [zaporze aplikacji sieci Web na platformie Azure — drzwi](../afds/afds-overview.md)
