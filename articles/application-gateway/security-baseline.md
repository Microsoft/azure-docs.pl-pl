---
title: Podstawa zabezpieczeń Azure dla usługi Azure Application Gateway
description: Podstawa zabezpieczeń Azure dla usługi Azure Application Gateway
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 06/05/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 4f28665998dcac9f641d4142a0dea60707fb02e9
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/07/2021
ms.locfileid: "99805364"
---
# <a name="azure-security-baseline-for-azure-application-gateway"></a>Podstawa zabezpieczeń Azure dla usługi Azure Application Gateway

Podstawowa usługa Azure Security Application Gateway zawiera zalecenia, które pomogą ulepszyć stan bezpieczeństwa wdrożenia.

Punkt odniesienia dla tej usługi jest rysowany w [wersji 1,0 usługi Azure Security test](../security/benchmarks/overview.md), która zawiera zalecenia dotyczące sposobu zabezpieczania rozwiązań w chmurze na platformie Azure z naszymi najlepszymi wskazówkami.

Aby uzyskać więcej informacji, zobacz [podstawy zabezpieczeń platformy Azure — omówienie](../security/benchmarks/security-baselines-overview.md).

## <a name="network-security"></a>Bezpieczeństwo sieci

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: zabezpieczenia sieci](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: Ochrona zasobów platformy Azure w ramach sieci wirtualnych

**Wskazówki**: Upewnij się, że wszystkie wdrożenia podsieci Virtual Network Azure Application Gateway mają sieciową grupę zabezpieczeń (sieciowej grupy zabezpieczeń) stosowaną w ramach kontroli dostępu do sieci, które są specyficzne dla zaufanych portów i źródeł aplikacji. Chociaż sieciowe grupy zabezpieczeń są obsługiwane na platformie Azure Application Gateway, istnieją pewne ograniczenia i wymagania, które muszą być zgodne, aby sieciowej grupy zabezpieczeń i Application Gateway platformy Azure działały zgodnie z oczekiwaniami.

* [Poznaj ograniczenia i wymagania dotyczące używania sieciowych grup zabezpieczeń z platformą Azure Application Gateway](./configuration-overview.md)

* [Jak utworzyć sieciowej grupy zabezpieczeń z konfiguracją zabezpieczeń](../virtual-network/tutorial-filter-network-traffic.md)

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1,2: Monitoruj i Rejestruj konfigurację oraz ruch sieci wirtualnych, podsieci i kart sieciowych

**Wskazówki**: dla sieciowych grup zabezpieczeń (sieciowych grup zabezpieczeń) skojarzonych z podsieciami platformy Azure Application Gateway Włącz dzienniki przepływów sieciowej grupy zabezpieczeń i Wyślij dzienniki do konta magazynu na potrzeby inspekcji ruchu. Możesz również wysłać dzienniki przepływu sieciowej grupy zabezpieczeń do obszaru roboczego Log Analytics i użyć Analiza ruchu, aby uzyskać wgląd w przepływ ruchu w chmurze platformy Azure. Niektóre zalety Analiza ruchu to możliwość wizualizacji aktywności sieciowej i identyfikowania aktywnych punktów, identyfikowania zagrożeń bezpieczeństwa, zrozumienia wzorców przepływu ruchu i wyznaczania konfiguracji sieci.

Uwaga: istnieją sytuacje, w których dzienniki przepływu sieciowej grupy zabezpieczeń skojarzone z podsieciami usługi Azure Application Gateway nie będą wyświetlać dozwolonych danych. Jeśli konfiguracja pasuje do poniższego scenariusza, w dziennikach przepływu sieciowej grupy zabezpieczeń nie będzie widoczny dozwolony ruch:
- Wdrożono Application Gateway v2
- Masz sieciowej grupy zabezpieczeń w podsieci bramy aplikacji
- Włączono dzienniki przepływu sieciowej grupy zabezpieczeń na tym sieciowej grupy zabezpieczeń

* [Jak włączyć dzienniki przepływu sieciowej grupy zabezpieczeń](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

* [Jak włączyć i używać Analiza ruchu](../network-watcher/traffic-analytics.md)

* [Informacje o zabezpieczeniach sieci zapewnianych przez Azure Security Center](../security-center/security-center-network-recommendations.md)

* [Często zadawane pytania dotyczące diagnostyki i rejestrowania w usłudze Azure Application Gateway](./application-gateway-faq.yml#what-types-of-logs-does-application-gateway-provide)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="13-protect-critical-web-applications"></a>1,3: Ochrona krytycznych aplikacji sieci Web

**Wskazówki**: Wdróż zaporę aplikacji sieci Web platformy Azure (WAF) przed krytycznymi aplikacjami sieci Web, aby uzyskać dodatkową inspekcję ruchu przychodzącego. Zapora aplikacji sieci Web (WAF) to usługa (funkcja platformy Azure Application Gateway), która zapewnia scentralizowaną ochronę aplikacji sieci Web przed typowymi atakami i lukami w zabezpieczeniach. Usługa Azure WAF może pomóc w zabezpieczeniu Azure App Service aplikacji sieci Web przez inspekcję ruchu przychodzącego sieci Web w celu blokowania ataków, takich jak iniekcje SQL, skrypty między lokacjami, operacje przekazywania złośliwego oprogramowania i ataki DDoS. WAF opiera się na regułach z OWASP (Open Web Application Security Project) podstawowych zestawów reguł 3,1 (tylko WAF_v2), 3,0 i 2.2.9.

* [Omówienie funkcji usługi Azure Application Gateway](./features.md)

* [Opis usługi Azure WAF](../web-application-firewall/ag/ag-overview.md)

* [Jak wdrożyć usługę Azure WAF](../web-application-firewall/ag/create-waf-policy-ag.md)

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: odmowa komunikacji ze znanymi złośliwymi adresami IP

**Wskazówki**: Włącz ochronę standardową DDoS w sieciach wirtualnych platformy Azure skojarzonych z wystąpieniami produkcyjnymi usługi Azure Application Gateway, aby chronić przed atakami DDoS. Użyj Azure Security Center zintegrowanej analizy zagrożeń, aby odmówić komunikacji ze znanymi złośliwymi adresami IP.

* [Jak skonfigurować ochronę DDoS](../ddos-protection/manage-ddos-protection.md)

* [Opis Azure Security Center zintegrowanej analizy zagrożeń](../security-center/azure-defender.md)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="15-record-network-packets"></a>1,5: rejestrowanie pakietów sieciowych

**Wskazówki**: dla sieciowych grup zabezpieczeń (sieciowych grup zabezpieczeń) skojarzonych z podsieciami platformy Azure Application Gateway Włącz dzienniki przepływów sieciowej grupy zabezpieczeń i Wyślij dzienniki do konta magazynu na potrzeby inspekcji ruchu. Możesz również wysłać dzienniki przepływu sieciowej grupy zabezpieczeń do obszaru roboczego Log Analytics i użyć Analiza ruchu, aby uzyskać wgląd w przepływ ruchu w chmurze platformy Azure. Niektóre zalety Analiza ruchu to możliwość wizualizacji aktywności sieciowej i identyfikowania aktywnych punktów, identyfikowania zagrożeń bezpieczeństwa, zrozumienia wzorców przepływu ruchu i wyznaczania konfiguracji sieci.

Uwaga: istnieją sytuacje, w których dzienniki przepływu sieciowej grupy zabezpieczeń skojarzone z podsieciami usługi Azure Application Gateway nie będą wyświetlać dozwolonych danych. Jeśli konfiguracja pasuje do poniższego scenariusza, w dziennikach przepływu sieciowej grupy zabezpieczeń nie będzie widoczny dozwolony ruch:
- Wdrożono Application Gateway v2
- Masz sieciowej grupy zabezpieczeń w podsieci bramy aplikacji
- Włączono dzienniki przepływu sieciowej grupy zabezpieczeń na tym sieciowej grupy zabezpieczeń

* [Jak włączyć dzienniki przepływu sieciowej grupy zabezpieczeń](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

* [Jak włączyć i używać Analiza ruchu](../network-watcher/traffic-analytics.md)

* [Informacje o zabezpieczeniach sieci zapewnianych przez Azure Security Center](../security-center/security-center-network-recommendations.md)

* [Często zadawane pytania dotyczące diagnostyki i rejestrowania w usłudze Azure Application Gateway](./application-gateway-faq.yml#what-types-of-logs-does-application-gateway-provide)

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: Wdróż systemy zapobiegania wykrywaniu dostępu do sieci/dostępu intruzów (identyfikatory/adresy IP)

**Wskazówki**: Wdróż zaporę aplikacji sieci Web platformy Azure (WAF) przed krytycznymi aplikacjami sieci Web, aby uzyskać dodatkową inspekcję ruchu przychodzącego. Zapora aplikacji sieci Web (WAF) to usługa (funkcja platformy Azure Application Gateway), która zapewnia scentralizowaną ochronę aplikacji sieci Web przed typowymi atakami i lukami w zabezpieczeniach. Usługa Azure WAF może pomóc w zabezpieczeniu Azure App Service aplikacji sieci Web przez inspekcję ruchu przychodzącego sieci Web w celu blokowania ataków, takich jak iniekcje SQL, skrypty między lokacjami, operacje przekazywania złośliwego oprogramowania i ataki DDoS. WAF opiera się na regułach z OWASP (Open Web Application Security Project) podstawowych zestawów reguł 3,1 (tylko WAF_v2), 3,0 i 2.2.9.

Alternatywnie istnieje wiele opcji portalu Marketplace, takich jak Barracuda WAF for Azure, które są dostępne w witrynie Azure Marketplace, która obejmuje funkcje identyfikatorów/adresów IP.

* [Omówienie funkcji usługi Azure Application Gateway](./features.md)

* [Opis usługi Azure WAF](../web-application-firewall/ag/ag-overview.md)

* [Jak wdrożyć usługę Azure WAF](../web-application-firewall/ag/create-waf-policy-ag.md)

* [Opis usługi Barracuda WAF w chmurze](../app-service/environment/app-service-app-service-environment-web-application-firewall.md#configuring-your-barracuda-waf-cloud-service)

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="17-manage-traffic-to-web-applications"></a>1,7: zarządzanie ruchem do aplikacji sieci Web

**Wskazówki**: wdrażanie Application Gateway platformy Azure dla aplikacji sieci Web z WŁĄCZONYm protokołem HTTPS/SSL dla zaufanych certyfikatów.

* [Jak wdrożyć Application Gateway](./quick-create-portal.md)

* [Jak skonfigurować Application Gateway do korzystania z protokołu HTTPS](./create-ssl-portal.md)

* [Omówienie równoważenia obciążenia warstwy 7 za pomocą bram aplikacji sieci Web platformy Azure](./overview.md)

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: Minimalizacja złożoności i kosztów administracyjnych reguł zabezpieczeń sieci

**Wskazówki**: Użyj tagów usługi Virtual Network, aby zdefiniować kontrolę dostępu do sieci dla sieciowych grup zabezpieczeń lub zapory platformy Azure. Podczas tworzenia reguł zabezpieczeń można użyć tagów usługi zamiast konkretnych adresów IP. Określając nazwę tagu usługi (np. Gatewaymanager) w odpowiednim polu źródłowym lub docelowym reguły, można zezwolić na ruch dla odpowiedniej usługi lub go odrzucić. Firma Microsoft zarządza prefiksami adresów, które obejmują tag usługi, i automatycznie aktualizuje tag usługi jako adresy.

Dla sieciowych grup zabezpieczeń (sieciowych grup zabezpieczeń) skojarzonych z podsieciami platformy Azure Application Gateway należy zezwolić na ruch przychodzący z Internetu na portach TCP 65503-65534 dla jednostki SKU Application Gateway V1 oraz portów TCP 65200-65535 dla jednostki SKU v2 z podsiecią docelową jako dowolny i źródłowy jako tag usługi bramy. Ten zakres portów jest wymagany w przypadku komunikacji infrastruktury platformy Azure. Te porty są chronione (zablokowane) przez certyfikaty platformy Azure. Jednostki zewnętrzne, w tym klienci tych bram, nie mogą komunikować się z tymi punktami końcowymi.

* [Zrozumienie i używanie tagów usługi](../virtual-network/service-tags-overview.md)

* [Omówienie konfiguracji Application Gateway platformy Azure](./configuration-overview.md)

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: Obsługa standardowych konfiguracji zabezpieczeń dla urządzeń sieciowych

**Wskazówki**: Definiowanie i implementowanie standardowych konfiguracji zabezpieczeń dla ustawień sieciowych związanych z wdrożeniami Application Gateway platformy Azure. Użyj aliasów Azure Policy w przestrzeni nazw "Microsoft. Network", aby utworzyć zasady niestandardowe do inspekcji lub wymuszania konfiguracji sieci bram aplikacji platformy Azure, sieci wirtualnych platformy Azure i sieciowych grup zabezpieczeń. Może być również używana wbudowana definicja zasad.

Możesz również użyć planów platformy Azure, aby uprościć wdrożenia platformy Azure na dużą skalę przez pakowanie kluczowych artefaktów środowiska, takich jak szablony Azure Resource Manager, kontrola dostępu oparta na rolach (RBAC) na platformie Azure i zasady w ramach jednej definicji planu. Możesz łatwo zastosować plan do nowych subskrypcji, środowisk i dostrajania kontroli i zarządzania przy użyciu wersji.

* [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

* [Jak utworzyć Azure Blueprint](../governance/blueprints/create-blueprint-portal.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="110-document-traffic-configuration-rules"></a>1,10: udokumentowanie reguł konfiguracji ruchu

**Wskazówki**: Użyj tagów dla sieciowych grup zabezpieczeń (sieciowych grup zabezpieczeń) skojarzonych z podsiecią platformy Azure Application Gateway oraz innych zasobów związanych z zabezpieczeniami sieci i przepływem ruchu. W przypadku poszczególnych reguł sieciowej grupy zabezpieczeń Użyj pola "Description", aby określić potrzeby biznesowe i/lub czas trwania (itp.) dla reguł zezwalających na ruch do/z sieci.

Użyj dowolnych wbudowanych definicji zasad platformy Azure związanych z tagowaniem, takich jak "Wymagaj tagu i jego wartości", aby upewnić się, że wszystkie zasoby są tworzone przy użyciu tagów i powiadomienia o istniejących nieoznakowanych zasobach.

Możesz użyć Azure PowerShell lub interfejsu wiersza polecenia platformy Azure, aby wyszukiwać lub wykonywać akcje na zasobach na podstawie ich tagów.

* [Tworzenie i używanie tagów](../azure-resource-manager/management/tag-resources.md)

* [Jak utworzyć Virtual Network](../virtual-network/quick-create-portal.md)

* [Jak utworzyć sieciowej grupy zabezpieczeń z konfiguracją zabezpieczeń](../virtual-network/tutorial-filter-network-traffic.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: Użyj zautomatyzowanych narzędzi do monitorowania konfiguracji zasobów sieciowych i wykrywania zmian

**Wskazówki**: Użyj dziennika aktywności platformy Azure do monitorowania konfiguracji zasobów sieciowych i wykrywania zmian ustawień sieciowych i zasobów związanych z wdrożeniami Application Gateway platformy Azure. Utwórz alerty w Azure Monitor, które będą wyzwalane po wprowadzeniu zmian w krytycznych ustawieniach sieciowych lub zasobach.

* [Jak wyświetlać i pobierać zdarzenia dziennika aktywności platformy Azure](../azure-monitor/platform/activity-log.md#view-the-activity-log)

* [Jak utworzyć alerty w Azure Monitor](../azure-monitor/platform/alerts-activity-log.md)

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

## <a name="logging-and-monitoring"></a>Rejestrowanie i monitorowanie

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: rejestrowanie i monitorowanie](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2,1: Użyj źródeł synchronizacji zatwierdzonego czasu

**Wskazówki**: Firma Microsoft utrzymuje źródło czasu używane dla zasobów platformy Azure, takich jak Azure App Service.

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2,2: Skonfiguruj centralne zarządzanie dziennikami zabezpieczeń

**Wskazówki**: na potrzeby rejestrowania inspekcji płaszczyzny kontroli, Włącz ustawienia diagnostyki dziennika aktywności platformy Azure i Wyślij dzienniki do obszaru roboczego log Analytics, centrum zdarzeń platformy Azure lub konta usługi Azure Storage. Korzystając z danych dziennika aktywności platformy Azure, można określić "co, kto i kiedy" w przypadku operacji zapisu (PUT, POST, DELETE) wykonanych na poziomie płaszczyzny kontroli dla Application Gateway platformy Azure i powiązanych zasobów, takich jak sieciowe grupy zabezpieczeń (sieciowych grup zabezpieczeń), używane do ochrony podsieci Application Gateway platformy Azure.

Oprócz dzienników aktywności można skonfigurować ustawienia diagnostyczne dla wdrożeń usługi Azure Application Gateway. Ustawienia diagnostyczne służą do konfigurowania eksportu strumieniowego dzienników platformy i metryk dla zasobu do wybranego miejsca docelowego (konta magazynu, Event Hubs i Log Analytics).

Usługa Azure Application Gateway oferuje również wbudowaną integrację z usługą Azure Application Insights. Application Insights zbiera dane o dzienniku, wydajności i błędach. Application Insights automatycznie wykrywa anomalie wydajności i oferuje zaawansowane narzędzia analityczne ułatwiające diagnozowanie problemów oraz zrozumienie sposobu używania aplikacji sieci Web. Możesz włączyć eksport ciągły, aby eksportować dane telemetryczne z Application Insights do scentralizowanej lokalizacji, aby zapewnić czas dłuższy niż standardowy okres przechowywania.

* [Jak włączyć ustawienia diagnostyczne dla dziennika aktywności platformy Azure](../azure-monitor/platform/activity-log.md)

* [Jak włączyć ustawienia diagnostyczne dla Application Gateway platformy Azure](./application-gateway-diagnostics.md)

* [Jak włączyć Application Insights](../azure-monitor/app/app-insights-overview.md)

* [Jak skonfigurować eksport ciągły](../azure-monitor/app/export-telemetry.md)

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: Włączanie rejestrowania inspekcji dla zasobów platformy Azure

**Wskazówki**: na potrzeby rejestrowania inspekcji płaszczyzny kontroli, Włącz ustawienia diagnostyki dziennika aktywności platformy Azure i Wyślij dzienniki do obszaru roboczego log Analytics, centrum zdarzeń platformy Azure lub konta usługi Azure Storage. Korzystając z danych dziennika aktywności platformy Azure, można określić "co, kto i kiedy" w przypadku operacji zapisu (PUT, POST, DELETE) wykonanych na poziomie płaszczyzny kontroli dla Application Gateway platformy Azure i powiązanych zasobów, takich jak sieciowe grupy zabezpieczeń (sieciowych grup zabezpieczeń), używane do ochrony podsieci Application Gateway platformy Azure.

Oprócz dzienników aktywności można skonfigurować ustawienia diagnostyczne dla wdrożeń usługi Azure Application Gateway. Ustawienia diagnostyczne służą do konfigurowania eksportu strumieniowego dzienników platformy i metryk dla zasobu do wybranego miejsca docelowego (konta magazynu, Event Hubs i Log Analytics).

Usługa Azure Application Gateway oferuje również wbudowaną integrację z usługą Azure Application Insights. Application Insights zbiera dane o dzienniku, wydajności i błędach. Application Insights automatycznie wykrywa anomalie wydajności i oferuje zaawansowane narzędzia analityczne ułatwiające diagnozowanie problemów oraz zrozumienie sposobu używania aplikacji sieci Web. Możesz włączyć eksport ciągły, aby eksportować dane telemetryczne z Application Insights do scentralizowanej lokalizacji, aby zapewnić czas dłuższy niż standardowy okres przechowywania.

* [Jak włączyć ustawienia diagnostyczne dla dziennika aktywności platformy Azure](../azure-monitor/platform/activity-log.md)

* [Jak włączyć ustawienia diagnostyczne dla Application Gateway platformy Azure](./application-gateway-diagnostics.md)

* [Jak włączyć Application Insights](../azure-monitor/app/app-insights-overview.md)

* [Jak skonfigurować eksport ciągły](../azure-monitor/app/export-telemetry.md)

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: Zbierz dzienniki zabezpieczeń z systemów operacyjnych

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone do IaaS zasobów obliczeniowych.

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="25-configure-security-log-storage-retention"></a>2,5: Konfigurowanie przechowywania magazynu dzienników zabezpieczeń

**Wskazówki**: w obszarze Azure monitor Ustaw okres przechowywania log Analytics obszaru roboczego zgodnie z regulacjami zgodności w organizacji. Używaj kont usługi Azure Storage do przechowywania długoterminowego/archiwizowania.

* [Jak ustawić parametry przechowywania dzienników dla obszarów roboczych Log Analytics](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="26-monitor-and-review-logs"></a>2,6: dzienniki monitorowania i przeglądania

**Wskazówki**: Włączanie ustawień diagnostycznych dziennika aktywności platformy Azure oraz ustawień diagnostycznych dla Application Gateway platformy Azure i wysyłanie dzienników do obszaru roboczego log Analytics. Wykonuj zapytania w Log Analytics, aby wyszukiwać terminy, identyfikować trendy, analizować wzorce i udostępniać wiele innych szczegółowych informacji na podstawie zebranych danych.

Użyj Azure Monitor dla sieci, aby uzyskać Kompleksowy wgląd w kondycję i metryki dla wszystkich wdrożonych zasobów sieciowych, w tym bram aplikacji platformy Azure.

Opcjonalnie możesz włączyć i dołączyć dane do usługi Azure wskaźnikowej lub SIEM innych firm.

* [Jak włączyć ustawienia diagnostyczne dla dziennika aktywności platformy Azure](../azure-monitor/platform/activity-log.md)

* [Jak włączyć ustawienia diagnostyczne dla Application Gateway platformy Azure](./application-gateway-diagnostics.md)

* [Jak używać Azure Monitor dla sieci](../azure-monitor/insights/network-insights-overview.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: Włączanie alertów dla nietypowych działań

**Wskazówki**: Wdróż jednostkę SKU usługi Azure Web Application Firewall (WAF) w wersji 2 przed krytycznymi aplikacjami sieci Web, aby uzyskać dodatkową inspekcję ruchu przychodzącego. Zapora aplikacji sieci Web (WAF) to usługa (funkcja platformy Azure Application Gateway), która zapewnia scentralizowaną ochronę aplikacji sieci Web przed typowymi atakami i lukami w zabezpieczeniach. Usługa Azure WAF może pomóc w zabezpieczeniu Azure App Service aplikacji sieci Web przez inspekcję ruchu przychodzącego sieci Web w celu blokowania ataków, takich jak iniekcje SQL, skrypty między lokacjami, operacje przekazywania złośliwego oprogramowania i ataki DDoS. WAF opiera się na regułach z OWASP (Open Web Application Security Project) podstawowych zestawów reguł 3,1 (tylko WAF_v2), 3,0 i 2.2.9.

Włącz ustawienia diagnostyczne dziennika aktywności platformy Azure oraz ustawienia diagnostyczne usługi Azure WAF i Wyślij dzienniki do obszaru roboczego Log Analytics. Wykonuj zapytania w Log Analytics, aby wyszukiwać terminy, identyfikować trendy, analizować wzorce i udostępniać wiele innych szczegółowych informacji na podstawie zebranych danych. Możesz tworzyć alerty oparte na zapytaniach obszaru roboczego Log Analytics.

Użyj Azure Monitor dla sieci, aby uzyskać Kompleksowy wgląd w kondycję i metryki dla wszystkich wdrożonych zasobów sieciowych, w tym bram aplikacji platformy Azure. W konsoli Azure Monitor dla sieci można wyświetlać i tworzyć alerty dla Application Gateway platformy Azure.

* [Jak wdrożyć usługę Azure WAF](../web-application-firewall/ag/create-waf-policy-ag.md)

* [Jak włączyć ustawienia diagnostyczne dla dziennika aktywności platformy Azure](../azure-monitor/platform/activity-log.md)

* [Jak włączyć ustawienia diagnostyczne dla Application Gateway platformy Azure](./application-gateway-diagnostics.md)

* [Jak używać Azure Monitor dla sieci](../azure-monitor/insights/network-insights-overview.md)

* [Jak tworzyć alerty na platformie Azure](../azure-monitor/learn/tutorial-response.md)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="28-centralize-anti-malware-logging"></a>2,8: scentralizowanie rejestrowania chroniącego przed złośliwym oprogramowaniem

**Wskazówki**: wdrażanie zapory aplikacji sieci Web platformy Azure (WAF) v2 przed krytycznymi aplikacjami sieci Web w celu uzyskania dodatkowej inspekcji ruchu przychodzącego. Zapora aplikacji sieci Web (WAF) to usługa (funkcja platformy Azure Application Gateway), która zapewnia scentralizowaną ochronę aplikacji sieci Web przed typowymi atakami i lukami w zabezpieczeniach. Usługa Azure WAF może pomóc w zabezpieczeniu Azure App Service aplikacji sieci Web przez inspekcję ruchu przychodzącego sieci Web w celu blokowania ataków, takich jak iniekcje SQL, skrypty między lokacjami, operacje przekazywania złośliwego oprogramowania i ataki DDoS.

Skonfiguruj ustawienia diagnostyczne dla wdrożeń usługi Azure Application Gateway. Ustawienia diagnostyczne służą do konfigurowania eksportu strumieniowego dzienników platformy i metryk dla zasobu do wybranego miejsca docelowego (konta magazynu, Event Hubs i Log Analytics).

* [Jak wdrożyć usługę Azure WAF](../web-application-firewall/ag/create-waf-policy-ag.md)

* [Jak skonfigurować ustawienia diagnostyczne dla usługi Azure WAF](./application-gateway-diagnostics.md)

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="29-enable-dns-query-logging"></a>2,9: Włączanie rejestrowania zapytań DNS

**Wskazówki**: nie dotyczy; Usługa Azure Application Gateway nie przetwarza ani nie tworzy dzienników związanych z usługą DNS.

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="210-enable-command-line-audit-logging"></a>2,10: Włączanie rejestrowania inspekcji w wierszu polecenia

**Wskazówki**: nie dotyczy; te wytyczne są przeznaczone dla IaaS zasobów obliczeniowych.

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: nie dotyczy

## <a name="identity-and-access-control"></a>Tożsamość i kontrola dostępu

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: tożsamość i kontrola dostępu](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: obsługa spisu kont administracyjnych

**Wskazówki**: Azure Active Directory (AD) ma wbudowane role, które muszą być jawnie przypisane i są queryable. Za pomocą modułu Azure AD PowerShell można wykonywać zapytania ad hoc w celu odnajdywania kont należących do grup administracyjnych.

* [Jak uzyskać rolę katalogu w usłudze Azure AD przy użyciu programu PowerShell](/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

* [Jak uzyskać członków roli katalogu w usłudze Azure AD przy użyciu programu PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="32-change-default-passwords-where-applicable"></a>3,2: Zmień domyślne hasła, jeśli ma to zastosowanie

**Wskazówki**: kontrola płaszczyzny kontroli dostępu do Application Gateway platformy Azure odbywa się za pomocą Azure Active Directory (AD). Usługa Azure AD nie ma koncepcji domyślnych haseł.

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: Użyj dedykowanych kont administracyjnych

**Wskazówki**: Tworzenie standardowych procedur operacyjnych dotyczących korzystania z dedykowanych kont administracyjnych. Użyj Azure Security Center Zarządzanie tożsamościami i dostępem, aby monitorować liczbę kont administracyjnych.

Ponadto, aby ułatwić śledzenie dedykowanych kont administracyjnych, można użyć zaleceń z Azure Security Center lub wbudowanych zasad platformy Azure, takich jak:
- Do subskrypcji powinien być przypisany więcej niż jeden właściciel
- Przestarzałe konta z uprawnieniami właściciela powinny zostać usunięte z subskrypcji
- Konta zewnętrzne z uprawnieniami właściciela powinny zostać usunięte z subskrypcji

* [Jak używać Azure Security Center do monitorowania tożsamości i dostępu (wersja zapoznawcza)](../security-center/security-center-identity-access.md)

* [Jak używać Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3,4: Korzystaj z logowania jednokrotnego (SSO) z usługą Azure Active Directory

**Wskazówki**: Użyj rejestracji aplikacji platformy Azure (nazwy głównej usługi), aby pobrać token, który może służyć do współpracy z bramami aplikacji platformy Azure za pośrednictwem wywołań interfejsu API.

* [Jak wywołać interfejsy API REST platformy Azure](/rest/api/azure/#how-to-call-azure-rest-apis-with-postman)

* [Jak zarejestrować aplikację kliencką (nazwę główną usługi) w usłudze Azure AD](/rest/api/azure/#register-your-client-application-with-azure-ad)

* [Informacje o interfejsie API usługi Azure Recovery Services](/rest/api/recoveryservices/)

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Użyj uwierzytelniania wieloskładnikowego, aby uzyskać dostęp oparty na Azure Active Directory

**Wskazówki**: Włącz usługę Azure AD MFA i postępuj zgodnie z zaleceniami Azure Security Center zarządzaniem tożsamościami i dostępem.

* [Jak włączyć usługę MFA na platformie Azure](../active-directory/authentication/howto-mfa-getstarted.md)

* [Jak monitorować tożsamość i dostęp w Azure Security Center](../security-center/security-center-identity-access.md)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: Używaj dedykowanych maszyn (uprzywilejowany dostęp do stacji roboczych) dla wszystkich zadań administracyjnych

**Wskazówki**: Użyj dostępem uprzywilejowanym (uprzywilejowanych stacji roboczych dostępu) za pomocą usługi MFA skonfigurowanej do logowania się i konfigurowania zasobów platformy Azure.

* [Dowiedz się więcej o stacjach roboczych uprzywilejowanego dostępu](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

* [Jak włączyć usługę MFA na platformie Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: Rejestruj i Ostrzegaj o podejrzanych działaniach z kont administracyjnych

**Wskazówki**: używanie raportów zabezpieczeń Azure Active Directory do generowania dzienników i alertów w przypadku wystąpienia podejrzanych lub niebezpiecznych działań w środowisku. Użyj Azure Security Center, aby monitorować działania związane z tożsamościami i dostępem.

* [Identyfikowanie użytkowników usługi Azure AD oflagowanych na skutek ryzykownego działania](../active-directory/identity-protection/overview-identity-protection.md)

* [Jak monitorować działania użytkowników związane z tożsamościami i dostępem w usłudze Azure Security Center](../security-center/security-center-identity-access.md)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: zarządzanie zasobami platformy Azure tylko z zatwierdzonych lokalizacji

**Wskazówki**: Użyj dostępu warunkowego o nazwie Locations, aby zezwolić na dostęp tylko do określonych logicznych grup zakresów adresów IP lub krajów/regionów.

* [Jak skonfigurować nazwane lokalizacje na platformie Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="39-use-azure-active-directory"></a>3,9: Użyj Azure Active Directory

**Wskazówki**: Użyj Azure Active Directory (AAD) jako centralnego systemu uwierzytelniania i autoryzacji. W usłudze AAD dane są chronione przy użyciu silnego szyfrowania danych przechowywanych i przesyłanych. W usłudze AAD są również Sole, skróty i bezpieczne przechowywanie poświadczeń użytkownika.

* [Jak utworzyć i skonfigurować wystąpienie usługi AAD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: regularnie Przeglądaj i Uzgodnij dostęp użytkowników

**Wskazówki**: usługa Azure AD udostępnia dzienniki, które ułatwiają odnajdywanie starych kont. Ponadto za pomocą przeglądów dostępu do tożsamości platformy Azure można efektywnie zarządzać członkostwem w grupach, dostępem do aplikacji dla przedsiębiorstw i przypisaniami ról. Dostęp użytkowników może być regularnie przeglądany, aby upewnić się, że tylko Ci użytkownicy mają ciągły dostęp.

* [Informacje o raportowaniu usługi Azure AD](../active-directory/reports-monitoring/index.yml)

* [Jak korzystać z przeglądów dostępu do tożsamości platformy Azure](../active-directory/governance/access-reviews-overview.md)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: Monitor próbuje uzyskać dostęp do zdezaktywowanych poświadczeń

**Wskazówki**: masz dostęp do źródeł działań związanych z logowaniem do usługi Azure AD, inspekcją i ryzykiem dzienników zdarzeń, które umożliwiają integrację z dowolnym narzędziem Siem/monitorowania.

Proces ten można usprawnić, tworząc ustawienia diagnostyczne dla Azure Active Directory kont użytkowników i wysyłając dzienniki inspekcji i dzienniki logowania do obszaru roboczego Log Analytics. Żądane alerty można skonfigurować w obszarze roboczym Log Analytics.

* [Jak zintegrować dzienniki aktywności platformy Azure z usługą Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: odchylenia zachowania podczas logowania do konta

**Wskazówki**: użyj funkcji Azure AD Identity Protection i wykrywania ryzyka, aby skonfigurować automatyczne odpowiedzi na wykryte podejrzane działania związane z tożsamościami użytkowników. Możesz również pozyskać dane do usługi Azure wskaźnikowej na potrzeby dalszej analizy.

* [Jak wyświetlić ryzykowne logowania w usłudze Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

* [Jak skonfigurować i włączyć zasady dotyczące ryzyka związanego z ochroną tożsamości](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

* [Jak dołączyć wskaźnik na platformie Azure](../sentinel/quickstart-onboard.md)

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: Zapewnij firmie Microsoft dostęp do odpowiednich danych klienta w scenariuszach pomocy technicznej

**Wskazówki**: nie dotyczy; Skrytka klienta nie ma zastosowania do usługi Azure Application Gateway.

* [Lista usług obsługiwanych przez Skrytka klienta](../security/fundamentals/customer-lockbox-overview.md#supported-services-and-scenarios-in-general-availability)

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: nie dotyczy

## <a name="data-protection"></a>Ochrona danych

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Ochrona danych](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: przechowywanie spisu poufnych informacji

**Wskazówki**: Użyj tagów, aby pomóc w śledzeniu zasobów platformy Azure, które przechowują lub przetwarzają informacje poufne.

* [Tworzenie i używanie tagów](../azure-resource-manager/management/tag-resources.md)

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: Izoluj systemy przechowujące lub przetwarzające informacje poufne

**Wskazówki**: implementowanie oddzielnych subskrypcji i/lub grup zarządzania na potrzeby tworzenia, testowania i produkcji. Upewnij się, że wszystkie wdrożenia podsieci usługi Virtual Network Azure Application Gateway mają sieciową grupę zabezpieczeń (sieciowej grupy zabezpieczeń) stosowaną z kontrolkami dostępu do sieci określonymi dla zaufanych portów i źródeł aplikacji. Chociaż sieciowe grupy zabezpieczeń są obsługiwane na platformie Azure Application Gateway, istnieją pewne ograniczenia i wymagania, które muszą być zgodne, aby sieciowej grupy zabezpieczeń i Application Gateway platformy Azure działały zgodnie z oczekiwaniami.

* [Jak utworzyć dodatkowe subskrypcje platformy Azure](../cost-management-billing/manage/create-subscription.md)

* [Jak utworzyć Grupy zarządzania](../governance/management-groups/create-management-group-portal.md)

* [Tworzenie i używanie tagów](../azure-resource-manager/management/tag-resources.md)

* [Poznaj ograniczenia i wymagania dotyczące używania sieciowych grup zabezpieczeń z platformą Azure Application Gateway](./configuration-overview.md)

* [Jak utworzyć sieciowej grupy zabezpieczeń z konfiguracją zabezpieczeń](../virtual-network/tutorial-filter-network-traffic.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: Monitoruj i blokuj nieautoryzowany transfer informacji poufnych

**Wskazówki**: Upewnij się, że wszystkie wdrożenia podsieci Virtual Network Azure Application Gateway mają sieciową grupę zabezpieczeń (sieciowej grupy zabezpieczeń) stosowaną w ramach kontroli dostępu do sieci, które są specyficzne dla zaufanych portów i źródeł aplikacji. Ogranicz ruch wychodzący tylko do zaufanych lokalizacji, aby pomóc w ograniczeniu zagrożeń eksfiltracji danych. Chociaż sieciowe grupy zabezpieczeń są obsługiwane na platformie Azure Application Gateway, istnieją pewne ograniczenia i wymagania, które muszą być zgodne, aby sieciowej grupy zabezpieczeń i Application Gateway platformy Azure działały zgodnie z oczekiwaniami.

* [Poznaj ograniczenia i wymagania dotyczące używania sieciowych grup zabezpieczeń z platformą Azure Application Gateway](./configuration-overview.md)

* [Jak utworzyć sieciowej grupy zabezpieczeń z konfiguracją zabezpieczeń](../virtual-network/tutorial-filter-network-traffic.md)

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Współużytkowane

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: Szyfruj wszystkie poufne informacje podczas przesyłania

**Wskazówki**: Konfigurowanie kompleksowego szyfrowania przy użyciu protokołu TLS dla bram aplikacji platformy Azure.

* [Jak skonfigurować kompleksową metodę TLS przy użyciu usługi Azure Application Gateway](./end-to-end-ssl-portal.md)

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Współużytkowane

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: Użyj aktywnego narzędzia do odnajdywania, aby identyfikować poufne dane

**Wskazówki**: nie dotyczy, usługa Azure Application Gateway nie przechowuje danych klienta w stanie spoczynku.

Firma Microsoft zarządza podstawową infrastrukturą dla systemu Azure Application Gateway i ma zaimplementowane ścisłe kontrole, aby zapobiec utracie lub narażeniu danych klientów.

* [Informacje na temat ochrony danych klientów na platformie Azure](../security/fundamentals/protection-customer-data.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: korzystanie z usługi Azure RBAC do kontrolowania dostępu do zasobów

**Wskazówki**: Użyj kontroli dostępu opartej na rolach (Azure RBAC) na platformie Azure, aby kontrolować dostęp do płaszczyzny kontroli Application Gateway platformy azure (Azure Portal).

* [Jak skonfigurować usługę Azure RBAC](../role-based-access-control/role-assignments-portal.md)

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: Wymuś kontrolę dostępu przy użyciu ochrony przed utratą danych opartą na hoście

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone do IaaS zasobów obliczeniowych.

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: Szyfruj poufne informacje w spoczynku

**Wskazówki**: nie dotyczy; Usługa Azure Application Gateway nie przechowuje danych klienta.

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: Rejestruj i Ostrzegaj o zmianach krytycznych zasobów platformy Azure

**Wskazówki**: Użyj Azure monitor z dziennikiem aktywności platformy Azure, aby utworzyć alerty, kiedy zmiany są wprowadzane do produkcyjnych wystąpień Application Gateway platformy Azure, a także innych krytycznych lub powiązanych zasobów.

* [Jak utworzyć alerty dla zdarzeń dziennika aktywności platformy Azure](../azure-monitor/platform/alerts-activity-log.md)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

## <a name="vulnerability-management"></a>Zarządzanie lukami w zabezpieczeniach

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Zarządzanie lukami w zabezpieczeniach](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: uruchamianie narzędzi do skanowania automatycznych luk w zabezpieczeniach

**Wskazówki**: obecnie niedostępne; Ocena luk w zabezpieczeniach w Azure Security Center nie jest jeszcze dostępna dla Application Gateway platformy Azure.

Platforma bazowa przeskanowana i poprawiona przez firmę Microsoft. Przejrzyj kontrolki zabezpieczeń dostępne dla usługi Azure Application Gateway, aby zmniejszyć liczbę luk w zabezpieczeniach związanych z konfiguracją usług.

* [Pokrycie funkcji (w tym Ocena luk w zabezpieczeniach) dla usług Azure PaaS Services](../security-center/features-paas.md)

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: Wdróż automatyczne rozwiązanie do zarządzania poprawkami systemu operacyjnego

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone do IaaS zasobów obliczeniowych.

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5,3: Wdróż rozwiązanie zautomatyzowanego zarządzania poprawkami dla tytułów oprogramowania innych firm

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone do IaaS zasobów obliczeniowych.

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: porównanie luk w zabezpieczeniach z tyłu do tyłu

**Wskazówki**: nie są jeszcze dostępne; Ocena luk w zabezpieczeniach w Azure Security Center nie jest jeszcze dostępna dla Application Gateway platformy Azure.

Platforma bazowa przeskanowana i poprawiona przez firmę Microsoft. Przejrzyj kontrolki zabezpieczeń dostępne dla usługi Azure Application Gateway, aby zmniejszyć liczbę luk w zabezpieczeniach związanych z konfiguracją usług.

* [Pokrycie funkcji (w tym Ocena luk w zabezpieczeniach) dla usług Azure PaaS Services](../security-center/features-paas.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: Użyj procesu oceny ryzyka, aby określić priorytety korygowania odkrytych luk w zabezpieczeniach

**Wskazówki**: nie są jeszcze dostępne; Ocena luk w zabezpieczeniach w Azure Security Center nie jest jeszcze dostępna dla Application Gateway platformy Azure.

Platforma bazowa przeskanowana i poprawiona przez firmę Microsoft. Przejrzyj kontrolki zabezpieczeń dostępne dla usługi Azure Application Gateway, aby zmniejszyć liczbę luk w zabezpieczeniach związanych z konfiguracją usług.

* [Pokrycie funkcji (w tym Ocena luk w zabezpieczeniach) dla usług Azure PaaS Services](../security-center/features-paas.md)

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

## <a name="inventory-and-asset-management"></a>Zarządzanie magazynem i zasobami

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Spis i zarządzanie zasobami](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: Użyj rozwiązania automatycznego odnajdywania zasobów

**Wskazówki**: Użyj grafu zasobów platformy Azure do wykonywania zapytań/odnajdywania wszystkich zasobów (takich jak obliczenia, magazyn, Sieć, porty i protokoły itp.) w ramach subskrypcji. Upewnij się, że masz odpowiednie uprawnienia (odczyt) w dzierżawie i Wylicz wszystkie subskrypcje platformy Azure oraz zasoby w ramach subskrypcji.

Mimo że klasyczne zasoby platformy Azure mogą zostać odnalezione za pośrednictwem grafu zasobów, zdecydowanie zaleca się tworzenie i używanie Azure Resource Manager zasobów do przodu.

* [Jak tworzyć zapytania przy użyciu grafu zasobów platformy Azure](../governance/resource-graph/first-query-portal.md)

* [Jak wyświetlić subskrypcje platformy Azure](/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

* [Opis kontroli RBAC platformy Azure](../role-based-access-control/overview.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="62-maintain-asset-metadata"></a>6,2: Konserwowanie metadanych zasobów

**Wskazówki**: stosowanie tagów do zasobów platformy Azure, dzięki czemu metadane są logicznie zorganizowane w taksonomię.

* [Tworzenie i używanie tagów](../azure-resource-manager/management/tag-resources.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: Usuń nieautoryzowane zasoby platformy Azure

**Wskazówki**: używanie tagowania, grup zarządzania i oddzielnych subskrypcji, gdzie to konieczne, do organizowania i śledzenia zasobów platformy Azure. Regularnie Uzgadniaj spis i zapewnij, że nieautoryzowane zasoby są usuwane z subskrypcji w odpowiednim czasie.

Ponadto Użyj zasad platformy Azure, aby wprowadzić ograniczenia dotyczące typu zasobów, które można utworzyć w subskrypcjach klientów, korzystając z następujących wbudowanych definicji zasad:
- Niedozwolone typy zasobów
- Dozwolone typy zasobów

* [Jak utworzyć dodatkowe subskrypcje platformy Azure](../cost-management-billing/manage/create-subscription.md)

* [Jak utworzyć Grupy zarządzania](../governance/management-groups/create-management-group-portal.md)

* [Tworzenie i używanie tagów](../azure-resource-manager/management/tag-resources.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6,4: Definiowanie i obsługa spisu zatwierdzonych zasobów platformy Azure

**Wskazówki**: Definiowanie zatwierdzonych zasobów platformy Azure.

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: Monitoruj niezatwierdzone zasoby platformy Azure

**Wskazówki**: Użyj Azure Policy, aby umieścić ograniczenia dotyczące typu zasobów, które można utworzyć w ramach subskrypcji.

Użyj grafu zasobów platformy Azure do wykonywania zapytań/odnajdywania zasobów w ramach subskrypcji. Upewnij się, że wszystkie zasoby platformy Azure obecne w środowisku są zatwierdzone.

* [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

* [Jak tworzyć zapytania za pomocą usługi Azure Graph](../governance/resource-graph/first-query-portal.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: Monitoruj niezatwierdzone aplikacje oprogramowania w ramach zasobów obliczeniowych

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone do IaaS zasobów obliczeniowych.

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: Usuń niezatwierdzone zasoby platformy Azure i aplikacje oprogramowania

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone do IaaS zasobów obliczeniowych.

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="68-use-only-approved-applications"></a>6,8: Używaj tylko zatwierdzonych aplikacji

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone do IaaS zasobów obliczeniowych.

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="69-use-only-approved-azure-services"></a>6,9: Używaj tylko zatwierdzonych usług platformy Azure

**Wskazówki**: Użyj Azure Policy, aby wprowadzić ograniczenia dotyczące typu zasobów, które można utworzyć w subskrypcjach klientów, przy użyciu następujących wbudowanych definicji zasad:
- Niedozwolone typy zasobów
- Dozwolone typy zasobów

* [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

* [Jak odmówić określonego typu zasobu za pomocą Azure Policy](../governance/policy/samples/index.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6,10: przechowywanie spisu zatwierdzonych tytułów oprogramowania

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone do IaaS zasobów obliczeniowych.

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: Ogranicz możliwość korzystania przez użytkowników z Azure Resource Manager

**Wskazówki**: Skonfiguruj dostęp warunkowy platformy Azure, aby ograniczyć możliwość korzystania przez użytkowników z Azure Resource Manager przez skonfigurowanie "blokowania dostępu" dla aplikacji "Microsoft Azure Management".

* [Jak skonfigurować dostęp warunkowy w celu blokowania dostępu do Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6,12: Ogranicz możliwość wykonywania skryptów w zasobach obliczeniowych przez użytkowników

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone do IaaS zasobów obliczeniowych.

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: fizyczne lub logiczne rozdzielenie aplikacji wysokiego ryzyka

**Wskazówki**: implementowanie oddzielnych subskrypcji i/lub grup zarządzania na potrzeby tworzenia, testowania i produkcji. Upewnij się, że wszystkie wdrożenia podsieci usługi Virtual Network Azure Application Gateway mają sieciową grupę zabezpieczeń (sieciowej grupy zabezpieczeń) stosowaną z kontrolkami dostępu do sieci określonymi dla zaufanych portów i źródeł aplikacji. Chociaż sieciowe grupy zabezpieczeń są obsługiwane na platformie Azure Application Gateway, istnieją pewne ograniczenia i wymagania, które muszą być zgodne, aby sieciowej grupy zabezpieczeń i Application Gateway platformy Azure działały zgodnie z oczekiwaniami.

* [Jak utworzyć dodatkowe subskrypcje platformy Azure](../cost-management-billing/manage/create-subscription.md)

* [Jak utworzyć Grupy zarządzania](../governance/management-groups/create-management-group-portal.md)

* [Tworzenie i używanie tagów](../azure-resource-manager/management/tag-resources.md)

* [Poznaj ograniczenia i wymagania dotyczące używania sieciowych grup zabezpieczeń z platformą Azure Application Gateway](./configuration-overview.md)

* [Jak utworzyć sieciowej grupy zabezpieczeń z konfiguracją zabezpieczeń](../virtual-network/tutorial-filter-network-traffic.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

## <a name="secure-configuration"></a>Bezpieczna konfiguracja

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: bezpieczna konfiguracja](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: Ustanów bezpieczne konfiguracje dla wszystkich zasobów platformy Azure

**Wskazówki**: Definiowanie i implementowanie standardowych konfiguracji zabezpieczeń dla ustawień sieciowych związanych z wdrożeniami Application Gateway platformy Azure. Użyj aliasów Azure Policy w przestrzeni nazw "Microsoft. Network", aby utworzyć zasady niestandardowe do inspekcji lub wymuszania konfiguracji sieci bram aplikacji platformy Azure, sieci wirtualnych platformy Azure i sieciowych grup zabezpieczeń. Może być również używana wbudowana definicja zasad.

* [Jak wyświetlić dostępne aliasy Azure Policy](/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

* [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: Ustanów bezpieczne konfiguracje systemów operacyjnych

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone do IaaS zasobów obliczeniowych.

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: obsługa bezpiecznych konfiguracji zasobów platformy Azure

**Wskazówki**: Użyj zasad platformy Azure [Odmów] i [Wdróż, jeśli nie istnieje], aby wymusić bezpieczne ustawienia dla zasobów platformy Azure.

* [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

* [Zrozumienie efektów Azure Policy](../governance/policy/concepts/effects.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: Zachowaj konfiguracje bezpiecznego systemu operacyjnego

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone do IaaS zasobów obliczeniowych.

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: bezpiecznie przechowuj konfigurację zasobów platformy Azure

**Wskazówki**: Jeśli używasz niestandardowych definicji zasad platformy Azure, Użyj usługi Azure DevOps lub Azure Repos, aby bezpiecznie przechowywać kod i zarządzać nim.

* [Jak przechowywać kod w usłudze Azure DevOps](/azure/devops/repos/git/gitworkflow?view=azure-devops)

* [Dokumentacja Azure Repos](/azure/devops/repos/index?view=azure-devops)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: bezpieczne przechowywanie niestandardowych obrazów systemu operacyjnego

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone do IaaS zasobów obliczeniowych.

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: Wdrażanie narzędzi do zarządzania konfiguracją dla zasobów platformy Azure

**Wskazówki**: Użyj wbudowanych definicji Azure Policy, a także aliasów Azure Policy w przestrzeni nazw "Microsoft. Network", aby utworzyć zasady niestandardowe na potrzeby alertów, inspekcji i wymuszania konfiguracji systemu. Dodatkowo opracowuj proces i potok na potrzeby zarządzania wyjątkami zasad.

* [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7,8: Wdrażanie narzędzi do zarządzania konfiguracją dla systemów operacyjnych

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone do IaaS zasobów obliczeniowych.

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: Zaimplementuj automatyczne monitorowanie konfiguracji dla zasobów platformy Azure

**Wskazówki**: Użyj wbudowanych definicji Azure Policy, a także aliasów Azure Policy w przestrzeni nazw "Microsoft. Network", aby utworzyć zasady niestandardowe na potrzeby alertów, inspekcji i wymuszania konfiguracji systemu. Użyj zasad platformy Azure [Audit], [Odmów] i [Wdróż, jeśli nie istnieje], aby automatycznie wymuszać konfiguracje dla zasobów platformy Azure.

* [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: Zaimplementuj automatyczne monitorowanie konfiguracji dla systemów operacyjnych

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone do IaaS zasobów obliczeniowych.

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="711-manage-azure-secrets-securely"></a>7,11: bezpieczne zarządzanie wpisami tajnymi platformy Azure

**Wskazówki**: Użyj tożsamości zarządzanych, aby zapewnić Application Gateway platformy Azure z automatyczną tożsamością zarządzaną w usłudze Azure Active Directory (AD). Tożsamości zarządzane umożliwiają uwierzytelnianie w dowolnej usłudze, która obsługuje uwierzytelnianie usługi Azure AD, w tym Key Vault, bez żadnych poświadczeń w kodzie.

Użyj Azure Key Vault, aby bezpiecznie przechowywać certyfikaty. Azure Key Vault to magazyn tajny zarządzany przez platformę, którego można użyć do zabezpieczenia kluczy tajnych, klucze i certyfikatów SSL. Usługa Azure Application Gateway obsługuje integrację z Key Vault dla certyfikatów serwera, które są dołączone do odbiorników z włączonym protokołem HTTPS. Ta obsługa jest ograniczona do jednostki SKU Application Gateway v2.

* [Jak skonfigurować kończenie połączeń SSL z certyfikatami Key Vault przy użyciu Azure PowerShell](./configure-keyvault-ps.md)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: bezpieczne i automatyczne zarządzanie tożsamościami

**Wskazówki**: Użyj tożsamości zarządzanych, aby zapewnić Application Gateway platformy Azure z automatyczną tożsamością zarządzaną w usłudze Azure Active Directory (AD). Tożsamości zarządzane umożliwiają uwierzytelnianie w dowolnej usłudze, która obsługuje uwierzytelnianie usługi Azure AD, w tym Key Vault, bez żadnych poświadczeń w kodzie.

Użyj Azure Key Vault, aby bezpiecznie przechowywać certyfikaty. Azure Key Vault to magazyn tajny zarządzany przez platformę, którego można użyć do zabezpieczenia kluczy tajnych, klucze i certyfikatów SSL. Usługa Azure Application Gateway obsługuje integrację z Key Vault dla certyfikatów serwera, które są dołączone do odbiorników z włączonym protokołem HTTPS. Ta obsługa jest ograniczona do jednostki SKU Application Gateway v2.

* [Jak skonfigurować kończenie połączeń SSL z certyfikatami Key Vault przy użyciu Azure PowerShell](./configure-keyvault-ps.md)

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: eliminowanie nieprzewidzianego narażenia na poświadczenia

**Wskazówki**: Implementuj skaner poświadczeń, aby identyfikować poświadczenia w kodzie. Skaner poświadczeń ułatwia również przenoszenie odnalezionych poświadczeń do bezpieczniejszych lokalizacji, takich jak usługa Azure Key Vault.

* [Jak skonfigurować skaner poświadczeń](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

## <a name="malware-defense"></a>Ochrona przed złośliwym oprogramowaniem

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Obrona złośliwego oprogramowania](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8,1: Użyj centralnie zarządzanego oprogramowania chroniącego przed złośliwym oprogramowaniem

**Wskazówki**: wdrażanie zapory aplikacji sieci Web platformy Azure (WAF) v2 przed krytycznymi aplikacjami sieci Web w celu uzyskania dodatkowej inspekcji ruchu przychodzącego. Zapora aplikacji sieci Web (WAF) to usługa (funkcja platformy Azure Application Gateway), która zapewnia scentralizowaną ochronę aplikacji sieci Web przed typowymi atakami i lukami w zabezpieczeniach. Usługa Azure WAF może pomóc w zabezpieczeniu Azure App Service aplikacji sieci Web przez inspekcję ruchu przychodzącego sieci Web w celu blokowania ataków, takich jak iniekcje SQL, skrypty między lokacjami, operacje przekazywania złośliwego oprogramowania i ataki DDoS.

Skonfiguruj ustawienia diagnostyczne dla wdrożeń usługi Azure Application Gateway. Ustawienia diagnostyczne służą do konfigurowania eksportu strumieniowego dzienników platformy i metryk dla zasobu do wybranego miejsca docelowego (konta magazynu, Event Hubs i Log Analytics).

* [Jak wdrożyć usługę Azure WAF](../web-application-firewall/ag/create-waf-policy-ag.md)

* [Jak skonfigurować ustawienia diagnostyczne dla usługi Azure WAF](./application-gateway-diagnostics.md)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: przeskanuj pliki przed przekazaniem do zasobów platformy Azure, które nie są obliczeniowe

**Wskazówki**: nie dotyczy; Usługa Azure Application Gateway nie przechowuje danych klienta.

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8,3: Upewnij się, że oprogramowanie chroniące przed złośliwym oprogramowaniem i podpisy zostały zaktualizowane

**Wskazówki**: w przypadku korzystania z zapory aplikacji sieci Web (WAF) platformy Azure można skonfigurować zasady WAFymi. Zasady WAF obejmują dwa typy reguł zabezpieczeń: niestandardowe reguły, które są tworzone przez klienta i zarządzane zestawy reguł, które są zbiorem wstępnie skonfigurowanego zestawu reguł zarządzanej przez platformę Azure. Zestawy reguł zarządzane przez platformę Azure zapewniają łatwy sposób wdrażania ochrony przed wspólnym zbiorem zagrożeń bezpieczeństwa. Ponieważ takie zestaw reguł jest zarządzany przez platformę Azure, reguły są aktualizowane w razie potrzeby w celu ochrony przed nowymi sygnaturami ataków.

* [Opis zestawów reguł WAF zarządzanych przez platformę Azure](../web-application-firewall/ag/ag-overview.md#waf-policy-and-rules)

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Współużytkowane

## <a name="data-recovery"></a>Odzyskiwanie danych

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: odzyskiwanie danych](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: Zapewnij regularne zautomatyzowane przywracanie awaryjne

**Wskazówki**: usługa Azure Application Gateway nie przechowuje danych klienta. Jeśli jednak korzystasz z niestandardowych definicji zasad platformy Azure, Użyj usługi Azure DevOps lub Azure Repos, aby bezpiecznie przechowywać kod i zarządzać nim.

Usługa Azure DevOps Services wykorzystuje wiele funkcji usługi Azure Storage w celu zapewnienia dostępności danych w przypadku awarii sprzętowej, przerw w działaniu usługi lub awarii regionu. Ponadto zespół usługi Azure DevOps postępuje zgodnie z procedurami w celu ochrony danych przed przypadkowym lub złośliwym usunięciem.

* [Zrozumienie dostępności danych w usłudze Azure DevOps](/azure/devops/organizations/security/data-protection?view=azure-devops#data-availability)

* [Jak przechowywać kod w usłudze Azure DevOps](/azure/devops/repos/git/gitworkflow?view=azure-devops)

* [Dokumentacja Azure Repos](/azure/devops/repos/index?view=azure-devops)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: wykonaj kompletne kopie zapasowe systemu i Utwórz kopię zapasową wszystkich kluczy zarządzanych przez klienta

**Wskazówki**: Tworzenie kopii zapasowych certyfikatów zarządzanych przez klienta w ramach Azure Key Vault.

* [Jak utworzyć kopię zapasową certyfikatów magazynu kluczy na platformie Azure](/powershell/module/azurerm.keyvault/backup-azurekeyvaultcertificate)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: Weryfikuj wszystkie kopie zapasowe, w tym klucze zarządzane przez klienta

**Wskazówki**: testowanie przywracania kopii zapasowych certyfikatów zarządzanych przez klienta.

* [Jak przywrócić certyfikaty magazynu kluczy](/powershell/module/azurerm.keyvault/restore-azurekeyvaultcertificate)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: zapewnianie ochrony kopii zapasowych i kluczy zarządzanych przez klienta

**Wskazówki**: Upewnij się, że dla Azure Key Vault jest włączona funkcja usuwania nietrwałego. Usuwanie nietrwałe umożliwia Odzyskiwanie usuniętych magazynów kluczy i obiektów magazynu, takich jak klucze, wpisy tajne i certyfikaty.

* [Jak używać usuwania nietrwałego Azure Key Vault](../key-vault/general/key-vault-recovery.md)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

## <a name="incident-response"></a>Reagowanie na zdarzenia

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: odpowiedź na zdarzenia](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: Tworzenie przewodnika odpowiedzi na zdarzenia

**Wskazówka**: Utwórz przewodnik odpowiedzi na zdarzenia dla swojej organizacji. Upewnij się, że istnieją zarejestrowane plany reakcji na zdarzenia, które definiują wszystkie role pracowników, a także etapy obsługi zdarzeń/zarządzania od wykrywania do oceny po zdarzeniu.

* [Jak skonfigurować automatyzację przepływu pracy w programie Azure Security Center](../security-center/security-center-planning-and-operations-guide.md)

* [Wskazówki dotyczące tworzenia własnego procesu reagowania na zdarzenia zabezpieczeń](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [Anatomia incydentu centrum Microsoft Security Response](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [Klient może również korzystać z przewodnika obsługi zdarzeń związanych z bezpieczeństwem programu NIST, aby pomóc w tworzeniu własnego planu reagowania na zdarzenia](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: Tworzenie oceny incydentu i procedury priorytetyzacji

**Wskazówki**: Security Center przypisuje ważność do każdego alertu, aby pomóc w ustaleniu, które alerty należy najpierw zbadać. Ważność jest oparta na tym, jak dobrze Security Center znajduje się w wyszukiwaniu lub analitycznym używanym do wystawiania alertu, a także poziom pewności, że istniało złośliwy wpływ na działanie, które prowadziło do alertu.

Dodatkowo jasno Oznacz subskrypcje (na przykład produkcyjny, nieprodukcyjny) i Utwórz system nazewnictwa, aby jasno identyfikować i klasyfikować zasoby platformy Azure.

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="103-test-security-response-procedures"></a>10,3: procedury odpowiedzi na zabezpieczenia testowe

**Wskazówki**: przeprowadzanie ćwiczeń w celu przetestowania możliwości reagowania na zdarzenia systemu w regularnych erze. Zidentyfikuj słabe punkty i przerwy oraz popraw plan zgodnie z wymaganiami.

* [Zapoznaj się z publikacją NIST: Przewodnik dotyczący testowania, uczenia i ćwiczeń programów dla planów i możliwości IT](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: Podaj szczegóły kontaktu dotyczącego zabezpieczeń i Skonfiguruj powiadomienia dotyczące alertów dotyczących zdarzeń związanych z zabezpieczeniami

**Wskazówki**: informacje kontaktowe dotyczące zdarzenia zabezpieczeń będą używane przez firmę Microsoft do skontaktowania się z Tobą, jeśli firma Microsoft Security Response Center (MSRC) wykryje, że dostęp do danych klienta został uzyskany przez nielegalną lub nieautoryzowaną osobę. Przejrzyj zdarzenia po fakcie, aby upewnić się, że problemy zostały rozwiązane.

* [Jak ustawić kontakt z zabezpieczeniami Azure Security Center](../security-center/security-center-provide-security-contact-details.md)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: Uwzględnij alerty zabezpieczeń w systemie odpowiedzi na zdarzenia

**Wskazówki**: eksportowanie alertów i zaleceń dotyczących Azure Security Center przy użyciu funkcji eksportu ciągłego. Eksport ciągły umożliwia wyeksportowanie alertów i zaleceń ręcznie lub w stały sposób ciągły. Możesz użyć łącznika danych Azure Security Center do przesyłania strumieniowego alertów do usługi Azure wskaźnikowej.

* [Jak skonfigurować eksport ciągły](../security-center/continuous-export.md)

* [Jak przesłać strumieniowo alerty do usługi Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: Automatyzowanie odpowiedzi na alerty zabezpieczeń

**Wskazówki**: Użyj funkcji automatyzacji przepływu pracy w programie Azure Security Center, aby automatycznie wyzwalać odpowiedzi za pośrednictwem "Logic Apps" na temat alertów zabezpieczeń i zaleceń.

* [Jak skonfigurować automatyzację przepływu pracy i Logic Apps](../security-center/workflow-automation.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

## <a name="penetration-tests-and-red-team-exercises"></a>Testy penetracyjne i ćwiczenia typu „red team”

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: testy penetracji i czerwone ćwiczenia zespołu](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: Przeprowadź regularne testowanie penetracji zasobów platformy Azure i zadbaj o skorygowanie wszystkich krytycznych ustaleń dotyczących zabezpieczeń

**Wskazówki**: 

* [Postępuj zgodnie z zasadami firmy Microsoft dotyczącymi zaangażowania, aby upewnić się, że testy penetracji nie naruszają zasad firmy Microsoft](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

* [W tym miejscu znajdziesz więcej informacji na temat strategii i wykonywania trójwymiarowych operacji tworzenia zespołu i testowania aplikacji na żywo w witrynie Microsoft.](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Współużytkowane

## <a name="next-steps"></a>Następne kroki

- Zobacz [test porównawczy zabezpieczeń platformy Azure](../security/benchmarks/overview.md)
- Dowiedz się więcej o [punktach odniesienia zabezpieczeń platformy Azure](../security/benchmarks/security-baselines-overview.md)