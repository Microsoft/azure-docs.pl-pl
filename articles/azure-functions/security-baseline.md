---
title: Podstawa zabezpieczeń platformy Azure dla Azure Functions
description: Podstawa zabezpieczeń platformy Azure dla Azure Functions
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 05/04/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: f22473d4a865e2925ecbfe74d68811c12f7c26d7
ms.sourcegitcommit: 419c8c8061c0ff6dc12c66ad6eda1b266d2f40bd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/18/2020
ms.locfileid: "92168090"
---
# <a name="azure-security-baseline-for-azure-functions"></a>Podstawa zabezpieczeń platformy Azure dla Azure Functions

Podstawą zabezpieczeń platformy Azure dla Azure Functions są zalecenia, które pomogą ulepszyć stan bezpieczeństwa wdrożenia.

Punkt odniesienia dla tej usługi jest rysowany w [wersji 1,0 usługi Azure Security test](../security/benchmarks/overview.md), która zawiera zalecenia dotyczące sposobu zabezpieczania rozwiązań w chmurze na platformie Azure z naszymi najlepszymi wskazówkami.

Aby uzyskać więcej informacji, zobacz [podstawy zabezpieczeń platformy Azure — omówienie](../security/benchmarks/security-baselines-overview.md).

## <a name="network-security"></a>Bezpieczeństwo sieci

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: zabezpieczenia sieci](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1,1: Ochrona zasobów przy użyciu sieciowych grup zabezpieczeń lub zapory platformy Azure na Virtual Network

**Wskazówki**: Integrowanie aplikacji Azure Functions z siecią wirtualną platformy Azure. Aplikacje funkcji działające w planie Premium mają takie same możliwości hostingu jak aplikacje sieci Web w Azure App Service, które obejmują funkcję "Integracja sieci wirtualnej".  Usługa Azure Virtual Networks umożliwia umieszczenie wielu zasobów platformy Azure, takich jak Azure Functions, w sieci bez obsługi Internetu.

- [Jak zintegrować funkcje z Virtual Network platformy Azure](./functions-create-vnet.md)

- [Opis integracji sieci wirtualnej dla Azure Functions i Azure App Service](../app-service/web-sites-integrate-with-vnet.md)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1,2: Monitoruj i Rejestruj konfigurację oraz ruch sieci wirtualnych, podsieci i kart sieciowych

**Wskazówki**: Użyj Azure Security Center i postępuj zgodnie z zaleceniami dotyczącymi ochrony sieci, aby zabezpieczyć zasoby sieciowe i konfiguracje sieci związane z aplikacjami Azure Functions.

Jeśli używasz sieciowych grup zabezpieczeń (sieciowych grup zabezpieczeń) z implementacją Azure Functions, Włącz dzienniki przepływów sieciowej grupy zabezpieczeń i Wyślij dzienniki do konta usługi Azure Storage na potrzeby inspekcji ruchu. Możesz również wysłać dzienniki przepływu sieciowej grupy zabezpieczeń do obszaru roboczego Log Analytics i użyć Analiza ruchu, aby uzyskać wgląd w przepływ ruchu w chmurze platformy Azure. Niektóre zalety Analiza ruchu to możliwość wizualizacji aktywności sieciowej i identyfikowania aktywnych punktów, identyfikowania zagrożeń bezpieczeństwa, zrozumienia wzorców przepływu ruchu i wyznaczania konfiguracji sieci.

- [Informacje o zabezpieczeniach sieci zapewnianych przez Azure Security Center](../security-center/security-center-network-recommendations.md)

- [Jak włączyć dzienniki przepływu sieciowej grupy zabezpieczeń](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Jak włączyć i używać Analiza ruchu](../network-watcher/traffic-analytics.md)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="13-protect-critical-web-applications"></a>1,3: Ochrona krytycznych aplikacji sieci Web

**Wskazówki**: aby w pełni zabezpieczyć punkty końcowe funkcji platformy Azure w środowisku produkcyjnym, należy rozważyć implementację jednej z następujących opcji zabezpieczeń na poziomie aplikacji:
- Włącz App Service uwierzytelnianie/autoryzację dla aplikacji funkcji,
- Użyj usługi Azure API Management (APIM) do uwierzytelniania żądań lub
- Wdróż aplikację funkcji w Azure App Service Environment.

Ponadto upewnij się, że debugowanie zdalne zostało wyłączone dla Azure Functions produkcyjnych. Ponadto udostępnianie zasobów między źródłami (CORS) nie powinno zezwalać wszystkim domenom na dostęp do aplikacji funkcji platformy Azure. Zezwalaj na współdziałanie z aplikacją funkcji platformy Azure tylko wymagane domeny.

Rozważ wdrożenie zapory aplikacji sieci Web platformy Azure (WAF) jako części konfiguracji sieci w celu przeprowadzenia dodatkowej inspekcji ruchu przychodzącego. Włącz ustawienie diagnostyczne dla WAF i pobierania dzienników do konta magazynu, centrum zdarzeń lub Log Analytics obszaru roboczego. 

- [Jak zabezpieczyć punkty końcowe funkcji platformy Azure w środowisku produkcyjnym](./functions-bindings-http-webhook-trigger.md?tabs=csharp#secure-an-http-endpoint-in-production)

- [Jak wdrożyć usługę Azure WAF](../web-application-firewall/ag/create-waf-policy-ag.md)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: odmowa komunikacji ze znanymi złośliwymi adresami IP

**Wskazówki**: Włączanie standardu DDoS Protection w sieciach wirtualnych skojarzonych z aplikacjami funkcji Functions w celu ochrony przed atakami DDoS. Użyj Azure Security Center zintegrowanej analizy zagrożeń, aby odmówić komunikacji ze znanymi złośliwymi lub nieużywanymi publicznymi adresami IP.
Ponadto skonfiguruj bramę frontonu, taką jak Zapora aplikacji sieci Web platformy Azure, aby uwierzytelniać wszystkie żądania przychodzące i odfiltrować złośliwy ruch. Zapora aplikacji sieci Web platformy Azure może pomóc w zabezpieczeniu aplikacji funkcji platformy Azure, sprawdzając ruch przychodzący w sieci Web, aby blokować iniekcje SQL, skrypty między lokacjami, operacje przekazywania złośliwego oprogramowania i ataki DDoS. Wprowadzenie WAF wymaga App Service Environment lub użycia prywatnych punktów końcowych (wersja zapoznawcza). Upewnij się, że prywatne punkty końcowe nie są już w wersji zapoznawczej przed użyciem ich w przypadku obciążeń produkcyjnych.

- [Opcje sieciowe usługi Azure Functions](./functions-networking-options.md)

- [Plan Azure Functions Premium](./functions-scale.md#premium-plan)

- [Wprowadzenie do środowisk App Service Environment](../app-service/environment/intro.md)

- [Networking considerations for an App Service Environment (Zagadnienia dotyczące sieci w środowisku App Service Environment)](../app-service/environment/network-info.md)

- [Jak skonfigurować ochronę DDoS](../virtual-network/manage-ddos-protection.md)

- [Jak wdrożyć zaporę platformy Azure](../firewall/tutorial-firewall-deploy-portal.md)

- [Opis Azure Security Center zintegrowanej analizy zagrożeń](../security-center/azure-defender.md)

- [Informacje o Azure Security Center adaptacyjnej ograniczania przepustowości sieci](../security-center/security-center-adaptive-network-hardening.md)

- [Informacje o Azure Security Center Access Control sieci w czasie](../security-center/security-center-just-in-time.md)

- [Używanie prywatnych punktów końcowych Azure Functions](../app-service/networking/private-endpoint.md)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="15-record-network-packets-and-flow-logs"></a>1,5: rejestrowanie pakietów sieciowych i dzienników przepływów

**Wskazówki**: Jeśli używasz sieciowych grup zabezpieczeń (sieciowych grup zabezpieczeń) z implementacją Azure Functions, Włącz dzienniki przepływu sieciowych grup zabezpieczeń i Wyślij dzienniki do konta magazynu na potrzeby inspekcji ruchu. Możesz również wysyłać dzienniki przepływów do obszaru roboczego Log Analytics i używać Analiza ruchu, aby uzyskać wgląd w przepływ ruchu w chmurze platformy Azure. Niektóre zalety Analiza ruchu to możliwość wizualizacji aktywności sieciowej i identyfikowania aktywnych punktów, identyfikowania zagrożeń bezpieczeństwa, zrozumienia wzorców przepływu ruchu i wyznaczania konfiguracji sieci.

- [Jak włączyć dzienniki przepływu sieciowej grupy zabezpieczeń](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Jak włączyć i używać Analiza ruchu](../network-watcher/traffic-analytics.md)

- [Jak włączyć Network Watcher](../network-watcher/network-watcher-create.md)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: Wdróż systemy zapobiegania wykrywaniu dostępu do sieci/dostępu intruzów (identyfikatory/adresy IP)

**Wskazówki**: Konfigurowanie bramy frontonu, takiej jak Zapora aplikacji sieci Web platformy Azure, aby uwierzytelniać wszystkie żądania przychodzące i odfiltrować złośliwy ruch. Zapora aplikacji sieci Web platformy Azure może pomóc w zabezpieczeniu aplikacji funkcji przez inspekcję przychodzącego ruchu sieciowego w celu blokowania iniekcji SQL, skryptów między lokacjami, przekazywania złośliwego oprogramowania i ataków DDoS. Wprowadzenie WAF wymaga App Service Environment lub użycia prywatnych punktów końcowych (wersja zapoznawcza). Upewnij się, że prywatne punkty końcowe nie są już w wersji zapoznawczej przed użyciem ich w przypadku obciążeń produkcyjnych.

Alternatywnie istnieje wiele opcji portalu Marketplace, takich jak Barracuda WAF for Azure, które są dostępne w witrynie Azure Marketplace, która obejmuje funkcje identyfikatorów/adresów IP.

- [Opcje sieciowe usługi Azure Functions](./functions-networking-options.md)

- [Plan Azure Functions Premium](./functions-scale.md#premium-plan)

- [Wprowadzenie do środowisk App Service Environment](../app-service/environment/intro.md)

- [Networking considerations for an App Service Environment (Zagadnienia dotyczące sieci w środowisku App Service Environment)](../app-service/environment/network-info.md)

- [Omówienie zapory aplikacji sieci Web platformy Azure](../web-application-firewall/index.yml)

- [Używanie prywatnych punktów końcowych Azure Functions](../app-service/networking/private-endpoint.md)

- [Opis usługi Barracuda WAF w chmurze](../app-service/environment/app-service-app-service-environment-web-application-firewall.md#configuring-your-barracuda-waf-cloud-service)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="17-manage-traffic-to-web-applications"></a>1,7: zarządzanie ruchem do aplikacji sieci Web

**Wskazówki**: Skonfiguruj bramę frontonu dla sieci, taką jak Zapora aplikacji sieci Web platformy Azure z kompleksowym szyfrowaniem TLS. Wprowadzenie WAF wymaga App Service Environment lub użycia prywatnych punktów końcowych (wersja zapoznawcza). Upewnij się, że prywatne punkty końcowe nie są już w wersji zapoznawczej przed użyciem ich w przypadku obciążeń produkcyjnych.

- [Opcje sieciowe usługi Azure Functions](./functions-networking-options.md)

- [Plan Azure Functions Premium](./functions-scale.md#premium-plan)

- [Wprowadzenie do środowisk App Service Environment](../app-service/environment/intro.md)

- [Networking considerations for an App Service Environment (Zagadnienia dotyczące sieci w środowisku App Service Environment)](../app-service/environment/network-info.md)

- [Omówienie zapory aplikacji sieci Web platformy Azure](../web-application-firewall/index.yml)

- [Jak skonfigurować kompleksową metodę TLS przy użyciu Application Gateway z portalem](../application-gateway/end-to-end-ssl-portal.md)

- [Używanie prywatnych punktów końcowych Azure Functions](../app-service/networking/private-endpoint.md)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: Minimalizacja złożoności i kosztów administracyjnych reguł zabezpieczeń sieci

**Wskazówki**: Użyj tagów usługi Virtual Network, aby zdefiniować kontrolę dostępu do sieci dla sieciowych grup zabezpieczeń lub zapory platformy Azure. Podczas tworzenia reguł zabezpieczeń można użyć tagów usługi zamiast konkretnych adresów IP. Określając nazwę tagu usługi (np. AzureAppService) w odpowiednim polu źródłowym lub docelowym reguły, można zezwolić na ruch dla odpowiedniej usługi lub go odrzucić. Firma Microsoft zarządza prefiksami adresów, które obejmują tag usługi, i automatycznie aktualizuje tag usługi jako adresy.

- [Aby uzyskać więcej informacji na temat używania tagów usługi](../virtual-network/service-tags-overview.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: Obsługa standardowych konfiguracji zabezpieczeń dla urządzeń sieciowych

**Wskazówki**: Definiowanie i implementowanie standardowych konfiguracji zabezpieczeń dla ustawień sieciowych związanych z Azure Functions. Użyj aliasów Azure Policy w przestrzeniach nazw "Microsoft. Web" i "Microsoft. Network", aby utworzyć niestandardowe zasady inspekcji lub wymuszania konfiguracji sieci Azure Functions. Możesz również używać wbudowanych definicji zasad dla Azure Functions, takich jak:
- Mechanizm CORS nie powinien zezwalać wszystkim zasobom na dostęp do aplikacji funkcji
- aplikacja funkcji powinna być dostępna tylko za pośrednictwem protokołu HTTPS
- Najnowsza wersja protokołu TLS powinna być używana w aplikacja funkcji

Możesz również użyć planów platformy Azure, aby uprościć wdrożenia platformy Azure na dużą skalę przez pakowanie kluczowych artefaktów środowiska, takich jak szablony Azure Resource Manager, kontrola dostępu oparta na rolach (RBAC) na platformie Azure i zasady w ramach jednej definicji planu. Możesz łatwo zastosować plan do nowych subskrypcji, środowisk i dostrajania kontroli i zarządzania przy użyciu wersji.

- [Jak skonfigurować Azure Policy i zarządzać nimi](../governance/policy/tutorials/create-and-manage.md)

- [Jak utworzyć Azure Blueprint](../governance/blueprints/create-blueprint-portal.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="110-document-traffic-configuration-rules"></a>1,10: udokumentowanie reguł konfiguracji ruchu

**Wskazówki**: Jeśli używasz sieciowych grup zabezpieczeń (sieciowych grup zabezpieczeń) z implementacją Azure Functions, użyj tagów dla sieciowych grup zabezpieczeń i innych zasobów związanych z zabezpieczeniami sieci i przepływem ruchu. W przypadku poszczególnych reguł sieciowej grupy zabezpieczeń Użyj pola "Description", aby określić potrzeby biznesowe i/lub czas trwania (itp.) dla reguł zezwalających na ruch do/z sieci.

Użyj dowolnych wbudowanych definicji zasad platformy Azure związanych z tagowaniem, takich jak "Wymagaj tagu i jego wartości", aby upewnić się, że wszystkie zasoby są tworzone przy użyciu tagów i powiadomienia o istniejących nieoznakowanych zasobach.

Możesz użyć Azure PowerShell lub interfejsu wiersza polecenia platformy Azure, aby wyszukiwać lub wykonywać akcje na zasobach na podstawie ich tagów.

- [Tworzenie i używanie tagów](../azure-resource-manager/management/tag-resources.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: Użyj zautomatyzowanych narzędzi do monitorowania konfiguracji zasobów sieciowych i wykrywania zmian

**Wskazówki**: Użyj dziennika aktywności platformy Azure do monitorowania konfiguracji zasobów sieciowych i wykrywania zmian ustawień sieciowych i zasobów związanych z wdrożeniami Azure Functions. Utwórz alerty w Azure Monitor, które będą wyzwalane po wprowadzeniu zmian w krytycznych ustawieniach sieciowych lub zasobach. 

- [Jak wyświetlać i pobierać zdarzenia dziennika aktywności platformy Azure](../azure-monitor/platform/activity-log.md#view-the-activity-log)

- [Jak utworzyć alerty w Azure Monitor](../azure-monitor/platform/alerts-activity-log.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

## <a name="logging-and-monitoring"></a>Rejestrowanie i monitorowanie

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: rejestrowanie i monitorowanie](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2,1: Użyj źródeł synchronizacji zatwierdzonego czasu

**Wskazówki**: Firma Microsoft utrzymuje źródło czasu używane dla zasobów platformy Azure, takie jak Azure Functions sygnatury czasowe w dziennikach.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2,2: Skonfiguruj centralne zarządzanie dziennikami zabezpieczeń

**Wskazówki**: na potrzeby rejestrowania inspekcji płaszczyzny kontroli, Włącz ustawienia diagnostyki dziennika aktywności platformy Azure i Wyślij dzienniki do obszaru roboczego log Analytics, centrum zdarzeń platformy Azure lub konta usługi Azure Storage w celu archiwizacji. Korzystając z danych dziennika aktywności platformy Azure, można określić "co, kto i kiedy" dla operacji zapisu (PUT, POST, DELETE) wykonanych na poziomie płaszczyzny kontroli dla zasobów platformy Azure.

Azure Functions również oferuje wbudowaną integrację z usługą Azure Application Insights do monitorowania funkcji. Application Insights zbiera dane o dzienniku, wydajności i błędach. Automatycznie wykrywa anomalie wydajności i oferuje zaawansowane narzędzia analityczne ułatwiające diagnozowanie problemów oraz zrozumienie sposobu korzystania z funkcji.

Jeśli masz wbudowane niestandardowe rejestrowanie zabezpieczeń/inspekcji w aplikacji funkcji platformy Azure, Włącz ustawienie Diagnostyka "FunctionAppLogs" i Wyślij dzienniki do obszaru roboczego Log Analytics, usługi Azure Event Hub lub konta usługi Azure Storage w celu archiwizacji. 

Opcjonalnie możesz włączyć i dołączyć dane do usługi Azure wskaźnikowej lub SIEM innych firm. 

- [Jak włączyć ustawienia diagnostyczne dla dziennika aktywności platformy Azure](../azure-monitor/platform/activity-log.md)

- [Jak skonfigurować Azure Functions przy użyciu usługi Azure Application Insights](./functions-monitoring.md)

- [Jak włączyć ustawienia diagnostyczne (dzienniki wygenerowane przez użytkownika) dla Azure Functions](./functions-monitor-log-analytics.md)

- [Jak dołączyć wskaźnik na platformie Azure](../sentinel/quickstart-onboard.md)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: Włączanie rejestrowania inspekcji dla zasobów platformy Azure

**Wskazówki**: na potrzeby rejestrowania inspekcji płaszczyzny kontroli, Włącz ustawienia diagnostyki dziennika aktywności platformy Azure i Wyślij dzienniki do obszaru roboczego log Analytics, centrum zdarzeń platformy Azure lub konta usługi Azure Storage w celu archiwizacji. Korzystając z danych dziennika aktywności platformy Azure, można określić "co, kto i kiedy" dla operacji zapisu (PUT, POST, DELETE) wykonanych na poziomie płaszczyzny kontroli dla zasobów platformy Azure.

Jeśli masz wbudowane niestandardowe rejestrowanie zabezpieczeń/inspekcji w aplikacji funkcji platformy Azure, Włącz ustawienie Diagnostyka "FunctionAppLogs" i Wyślij dzienniki do obszaru roboczego Log Analytics, usługi Azure Event Hub lub konta usługi Azure Storage w celu archiwizacji. 

- [Jak włączyć ustawienia diagnostyczne dla dziennika aktywności platformy Azure](../azure-monitor/platform/activity-log.md)

- [Jak włączyć ustawienia diagnostyczne (dzienniki wygenerowane przez użytkownika) dla Azure Functions](./functions-monitor-log-analytics.md)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: Zbierz dzienniki zabezpieczeń z systemów operacyjnych

**Wskazówki**: nie dotyczy; te wytyczne są przeznaczone dla IaaS zasobów obliczeniowych.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="25-configure-security-log-storage-retention"></a>2,5: Konfigurowanie przechowywania magazynu dzienników zabezpieczeń

**Wskazówki**: w Azure monitor ustawić okres przechowywania dziennika dla log Analytics obszarów roboczych skojarzonych z aplikacjami Azure Functions zgodnie z regulacjami zgodności w organizacji.

- [Jak ustawić parametry przechowywania dziennika](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="26-monitor-and-review-logs"></a>2,6: dzienniki monitorowania i przeglądania

**Wskazówki**: Włączanie ustawień diagnostycznych dziennika aktywności platformy Azure oraz ustawień diagnostycznych aplikacji Azure Functions i wysyłanie dzienników do log Analytics obszaru roboczego. Wykonuj zapytania w Log Analytics, aby wyszukiwać terminy, identyfikować trendy, analizować wzorce i udostępniać wiele innych szczegółowych informacji na podstawie zebranych danych.

Włącz Application Insights aplikacji Azure Functions do zbierania danych o dzienniku, wydajności i błędach. Można wyświetlić dane telemetryczne zbierane przez Application Insights w Azure Portal.

Jeśli masz wbudowane niestandardowe rejestrowanie zabezpieczeń/inspekcji w aplikacji funkcji platformy Azure, Włącz ustawienie Diagnostyka "FunctionAppLogs" i Wyślij dzienniki do obszaru roboczego Log Analytics, usługi Azure Event Hub lub konta usługi Azure Storage w celu archiwizacji. 

Opcjonalnie możesz włączyć i dołączyć dane do usługi Azure wskaźnikowej lub SIEM innych firm. 

- [Jak włączyć ustawienia diagnostyczne dla dziennika aktywności platformy Azure](../azure-monitor/platform/activity-log.md)

- [Jak włączyć ustawienia diagnostyczne dla Azure Functions](./functions-monitor-log-analytics.md)

- [Jak skonfigurować Azure Functions za pomocą usługi Azure Application Insights i wyświetlić dane telemetryczne](./functions-monitoring.md)

- [Jak dołączyć wskaźnik na platformie Azure](../sentinel/quickstart-onboard.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="27-enable-alerts-for-anomalous-activity"></a>2,7: Włączanie alertów dla nietypowego działania

**Wskazówki**: Włączanie ustawień diagnostycznych dziennika aktywności platformy Azure oraz ustawień diagnostycznych aplikacji Azure Functions i wysyłanie dzienników do log Analytics obszaru roboczego. Wykonuj zapytania w Log Analytics, aby wyszukiwać terminy, identyfikować trendy, analizować wzorce i udostępniać wiele innych szczegółowych informacji na podstawie zebranych danych. Możesz tworzyć alerty oparte na zapytaniach obszaru roboczego Log Analytics.

Włącz Application Insights aplikacji Azure Functions do zbierania danych o dzienniku, wydajności i błędach. Możesz wyświetlić dane telemetryczne zbierane przez Application Insights i utworzyć alerty w Azure Portal.

Opcjonalnie możesz włączyć i dołączyć dane do usługi Azure wskaźnikowej lub SIEM innych firm. 

- [Jak włączyć ustawienia diagnostyczne dla dziennika aktywności platformy Azure](../azure-monitor/platform/activity-log.md)

- [Jak włączyć ustawienia diagnostyczne dla Azure Functions](./functions-monitor-log-analytics.md)

- [Jak włączyć Application Insights dla Azure Functions](./configure-monitoring.md#enable-application-insights-integration)

- [Jak tworzyć alerty na platformie Azure](../azure-monitor/learn/tutorial-response.md)

- [Jak dołączyć wskaźnik na platformie Azure](../sentinel/quickstart-onboard.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="28-centralize-anti-malware-logging"></a>2,8: scentralizowanie rejestrowania chroniącego przed złośliwym oprogramowaniem

**Wskazówki**: nie dotyczy; Aplikacje Azure Functions nie przetwarzają ani nie generują dzienników związanych z oprogramowaniem chroniącym przed złośliwym kodem.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="29-enable-dns-query-logging"></a>2,9: Włączanie rejestrowania zapytań DNS

**Wskazówki**: nie dotyczy; Aplikacje Azure Functions nie przetwarzają ani nie generują dzienników związanych z usługą DNS.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="210-enable-command-line-audit-logging"></a>2,10: Włączanie rejestrowania inspekcji w wierszu polecenia

**Wskazówki**: nie dotyczy; te wytyczne są przeznaczone dla IaaS zasobów obliczeniowych.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

## <a name="identity-and-access-control"></a>Tożsamość i kontrola dostępu

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: tożsamość i kontrola dostępu](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: obsługa spisu kont administracyjnych

**Wskazówki**: Azure Active Directory (AD) ma wbudowane role, które muszą być jawnie przypisane i są queryable. Za pomocą modułu Azure AD PowerShell można wykonywać zapytania ad hoc w celu odnajdywania kont należących do grup administracyjnych. 

- [Jak uzyskać rolę katalogu w usłudze Azure AD przy użyciu programu PowerShell](/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

- [Jak uzyskać członków roli katalogu w usłudze Azure AD przy użyciu programu PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="32-change-default-passwords-where-applicable"></a>3,2: Zmień domyślne hasła, jeśli ma to zastosowanie

**Wskazówki**: płaszczyzna kontroli dostęp do Azure Functions jest kontrolowany za poorednictwem Azure Active Directory (AD). Usługa Azure AD nie ma koncepcji domyślnych haseł.

Dostęp do płaszczyzny danych można kontrolować za pomocą kilku środków, takich jak klucze autoryzacji, ograniczenia sieci i sprawdzanie poprawności tożsamości usługi Azure AD. Klucze autoryzacji są używane przez klientów nawiązujących połączenie z punktami końcowymi HTTP Azure Functions i mogą być ponownie generowane w dowolnym momencie. Te klucze są domyślnie generowane dla nowych punktów końcowych HTTP.

Wiele metod wdrażania jest dostępnych dla aplikacji funkcji, a niektóre z nich mogą korzystać z zestawu wygenerowanych poświadczeń. Zapoznaj się z metodami wdrażania, które będą używane dla danej aplikacji.

- [Zabezpieczanie punktu końcowego HTTP](./functions-bindings-http-webhook-trigger.md?tabs=csharp#secure-an-http-endpoint-in-production)

- [Jak uzyskać i wygenerować ponownie klucze autoryzacji](./functions-bindings-http-webhook-trigger.md?tabs=csharp#obtaining-keys)

- [Technologie wdrażania w Azure Functions](./functions-deployment-technologies.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: Użyj dedykowanych kont administracyjnych

**Wskazówki**: Tworzenie standardowych procedur operacyjnych dotyczących korzystania z dedykowanych kont administracyjnych. Użyj Azure Security Center Zarządzanie tożsamościami i dostępem, aby monitorować liczbę kont administracyjnych.

Ponadto, aby ułatwić śledzenie dedykowanych kont administracyjnych, można użyć zaleceń z Azure Security Center lub wbudowanych zasad platformy Azure, takich jak: należy mieć więcej niż jednego właściciela przypisanego do przestarzałych kont subskrypcji z uprawnieniami właściciela, które należy usunąć z kont zewnętrznych subskrypcji z uprawnieniami właściciela, należy usunąć z subskrypcji

- [Jak używać Azure Security Center do monitorowania tożsamości i dostępu (wersja zapoznawcza)](../security-center/security-center-identity-access.md)

- [Jak używać Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3,4: Korzystaj z logowania jednokrotnego (SSO) z usługą Azure Active Directory

**Wskazówki**: wszędzie tam, gdzie to możliwe, użyj Azure Active Directory rejestracji jednokrotnej zamiast konfigurować indywidualne poświadczenia autonomiczne na potrzeby dostępu do danych do aplikacji funkcji. Użyj Azure Security Center zalecenia dotyczące zarządzania tożsamościami i dostępem. Zaimplementuj Logowanie jednokrotne dla aplikacji Azure Functions przy użyciu funkcji uwierzytelniania App Service/autoryzacji.

- [Informacje o uwierzytelnianiu i autoryzacji w Azure Functions](../app-service/overview-authentication-authorization.md#identity-providers)

- [Opis logowania jednokrotnego w usłudze Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Użyj uwierzytelniania wieloskładnikowego, aby uzyskać dostęp oparty na Azure Active Directory

**Wskazówki**: Włączanie Azure Active Directory (AD) Multi-Factor Authentication (MFA) i postępuj zgodnie z zaleceniami dotyczącymi zarządzania tożsamościami i dostępem Azure Security Center.

- [Jak włączyć usługę MFA na platformie Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Jak monitorować tożsamość i dostęp w Azure Security Center](../security-center/security-center-identity-access.md)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: Używaj dedykowanych maszyn (uprzywilejowany dostęp do stacji roboczych) dla wszystkich zadań administracyjnych

**Wskazówki**: Użyj stacji roboczych dostępu uprzywilejowanego (dostępem uprzywilejowanym) z usługą Multi-Factor Authentication (MFA) skonfigurowaną w celu logowania się i konfigurowania zasobów platformy Azure.

- [Dowiedz się więcej o stacjach roboczych uprzywilejowanego dostępu](/windows-server/identity/securing-privileged-access/privileged-access-workstations)

- [Jak włączyć usługę MFA na platformie Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3,7: dziennik i alert dotyczący podejrzanego działania z kont administracyjnych

**Wskazówki**: Użyj Azure Active Directory (AD) PRIVILEGED Identity Management (PIM) do generowania dzienników i alertów w przypadku wystąpienia podejrzanych lub niebezpiecznych działań w środowisku.

Ponadto za pomocą funkcji wykrywania ryzyka usługi Azure AD można wyświetlać alerty i raporty na temat ryzykownego zachowania użytkowników.

- [Jak wdrożyć Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [Omówienie wykrywania ryzyka usługi Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: zarządzanie zasobami platformy Azure tylko z zatwierdzonych lokalizacji

**Wskazówki**: Użyj dostępu warunkowego o nazwie Locations, aby zezwolić na dostęp do Azure Portal tylko z określonych logicznych grup zakresów lub krajów/regionów adresów IP.

- [Jak skonfigurować nazwane lokalizacje na platformie Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="39-use-azure-active-directory"></a>3,9: Użyj Azure Active Directory

**Wskazówki**: Użyj Azure Active Directory (AD) jako centralnego systemu uwierzytelniania i autoryzacji dla aplikacji Azure Functions. Usługa Azure AD chroni dane przy użyciu silnego szyfrowania danych przechowywanych i przesyłanych. Usługa Azure AD również Sole, skróty i bezpieczne przechowywanie poświadczeń użytkownika.

- [Jak skonfigurować aplikację Azure Functions do korzystania z logowania za pomocą usługi Azure AD](../app-service/configure-authentication-provider-aad.md)

- [Jak utworzyć i skonfigurować wystąpienie usługi Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: regularnie Przeglądaj i Uzgodnij dostęp użytkowników

**Wskazówki**: Azure Active Directory (AD) zawiera dzienniki ułatwiające odnajdywanie starych kont. Ponadto za pomocą przeglądów dostępu do tożsamości platformy Azure można efektywnie zarządzać członkostwem w grupach, dostępem do aplikacji dla przedsiębiorstw i przypisaniami ról. Dostęp użytkowników może być regularnie przeglądany, aby upewnić się, że tylko Ci użytkownicy mają ciągły dostęp. 

- [Informacje o raportowaniu usługi Azure AD](../active-directory/reports-monitoring/index.yml)

- [Jak korzystać z przeglądów dostępu do tożsamości platformy Azure](../active-directory/governance/access-reviews-overview.md)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3,11: Monitor próbuje uzyskać dostęp do zdezaktywowanych kont

**Wskazówki**: Użyj Azure Active Directory (AD) jako centralnego systemu uwierzytelniania i autoryzacji dla aplikacji funkcji platformy Azure. Usługa Azure AD chroni dane przy użyciu silnego szyfrowania danych przechowywanych i przesyłanych. Usługa Azure AD również Sole, skróty i bezpieczne przechowywanie poświadczeń użytkownika.

Masz dostęp do źródeł działań związanych z logowaniem do usługi Azure AD, inspekcją i ryzykiem dzienników zdarzeń, które umożliwiają integrację z platformą Azure, lub SIEM innych firm.

Proces ten można usprawnić, tworząc ustawienia diagnostyczne dla kont użytkowników usługi Azure AD i wysyłając dzienniki inspekcji i dzienniki logowania do obszaru roboczego Log Analytics. Żądane alerty dzienników można skonfigurować w ramach Log Analytics.

- [Jak skonfigurować aplikację Azure Functions do korzystania z logowania za pomocą usługi Azure AD](../app-service/configure-authentication-provider-aad.md)

- [Jak zintegrować dzienniki aktywności platformy Azure z usługą Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Jak przejść do tablicy wskaźnikowej platformy Azure](../sentinel/quickstart-onboard.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: odchylenia zachowania podczas logowania do konta

**Wskazówki**: Użyj Azure Active Directory (AD) jako centralnego systemu uwierzytelniania i autoryzacji dla aplikacji Azure Functions. W przypadku pominięcia zachowania logowania do konta na płaszczyźnie kontroli (Azure Portal) Użyj funkcji ochrony tożsamości usługi Azure Active Directory (AD) i wykrywania ryzyka, aby skonfigurować automatyczne odpowiedzi na wykryte podejrzane działania związane z tożsamościami użytkowników. Możesz również pozyskać dane do usługi Azure wskaźnikowej na potrzeby dalszej analizy.

- [Jak wyświetlić ryzykowne logowania usługi Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

- [Jak skonfigurować i włączyć zasady dotyczące ryzyka związanego z ochroną tożsamości](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Jak dołączyć wskaźnik na platformie Azure](../sentinel/quickstart-onboard.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: Zapewnij firmie Microsoft dostęp do odpowiednich danych klienta w scenariuszach pomocy technicznej

**Wskazówki**: obecnie niedostępne; Skrytka klienta nie jest obecnie obsługiwana dla Azure Functions.

- [Lista usług obsługiwanych przez Skrytka klienta](../security/fundamentals/customer-lockbox-overview.md#supported-services-and-scenarios-in-general-availability)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

## <a name="data-protection"></a>Ochrona danych

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Ochrona danych](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: przechowywanie spisu poufnych informacji

**Wskazówki**: Użyj tagów, aby pomóc w śledzeniu zasobów platformy Azure, które przechowują lub przetwarzają informacje poufne.

- [Tworzenie i używanie tagów](../azure-resource-manager/management/tag-resources.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: Izoluj systemy przechowujące lub przetwarzające informacje poufne

**Wskazówki**: implementowanie oddzielnych subskrypcji i/lub grup zarządzania na potrzeby tworzenia, testowania i produkcji. Aplikacje funkcji platformy Azure powinny być rozdzielone przez sieć wirtualną (VNet)/Subnet i odpowiednio oznakowane.

Do przeprowadzenia izolacji sieci można także użyć prywatnych punktów końcowych. Prywatny punkt końcowy platformy Azure to interfejs sieciowy, który nawiązuje połączenie prywatnie i bezpiecznie z usługą (na przykład: Azure Functions punkt końcowy HTTPs aplikacji) obsługiwanej przez link prywatny platformy Azure. Prywatny punkt końcowy używa prywatnego adresu IP z Twojej sieci wirtualnej, skutecznie przenosząc usługę do sieci wirtualnej. Prywatne punkty końcowe są w (wersja zapoznawcza) dla aplikacji funkcji działających w ramach planu Premium. Upewnij się, że prywatne punkty końcowe nie są już w wersji zapoznawczej przed użyciem ich w przypadku obciążeń produkcyjnych.

- [Jak utworzyć dodatkowe subskrypcje platformy Azure](../cost-management-billing/manage/create-subscription.md)

- [Jak utworzyć Grupy zarządzania](../governance/management-groups/create-management-group-portal.md)

- [Tworzenie i używanie tagów](../azure-resource-manager/management/tag-resources.md)

- [Opcje sieciowe usługi Azure Functions](./functions-networking-options.md)

- [Plan Azure Functions Premium](./functions-scale.md#premium-plan)

- [Omówienie prywatnego punktu końcowego](../private-link/private-endpoint-overview.md)

- [Używanie prywatnych punktów końcowych Azure Functions](../app-service/networking/private-endpoint.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: Monitoruj i blokuj nieautoryzowany transfer informacji poufnych

**Wskazówki**: wdrażanie zautomatyzowanego narzędzia na obrzeżach sieci, które monitoruje do nieautoryzowanego transferu poufnych informacji i blokuje takie transfery podczas powiadamiania specjalistów ds. bezpieczeństwa informacji. 

Firma Microsoft zarządza podstawową infrastrukturą dla Azure Functions i ma zaimplementowane ścisłe kontrole, aby zapobiec utracie lub narażeniu danych klientów.

- [Zrozumienie ochrony danych klientów na platformie Azure](../security/fundamentals/protection-customer-data.md)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: nie dotyczy

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: Szyfruj wszystkie poufne informacje podczas przesyłania

**Wskazówki**: w Azure Portal aplikacji funkcji platformy Azure w obszarze "funkcje platformy: sieć: SSL" Włącz ustawienie "tylko https" i ustaw minimalną wersję protokołu TLS na 1,2.

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: Użyj aktywnego narzędzia do odnajdywania, aby identyfikować poufne dane

**Wskazówki**: obecnie niedostępne; funkcje identyfikacji, klasyfikacji i zapobiegania utracie danych nie są obecnie dostępne dla Azure Functions. Tagi aplikacje funkcji, które mogą przetwarzać informacje poufne jako takie i wdrażać rozwiązania innych firm, jeśli są wymagane do celów zgodności.

W przypadku podstawowej platformy zarządzanej przez firmę Microsoft Firma Microsoft traktuje całą zawartość klienta jako poufną i nadaje im dużą długość, aby chronić przed utratą i narażeniem danych przez klienta. Aby zapewnić bezpieczeństwo danych klienta na platformie Azure, firma Microsoft wdrożyła i utrzymuje pakiet niezawodnych kontroli i możliwości ochrony danych.

- [Zrozumienie ochrony danych klientów na platformie Azure](../security/fundamentals/protection-customer-data.md)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: korzystanie z usługi Azure RBAC do kontrolowania dostępu do zasobów

**Wskazówki**: Użyj kontroli dostępu opartej na rolach (Azure RBAC) na platformie Azure, aby kontrolować dostęp do płaszczyzny kontroli funkcji platformy azure (Azure Portal). 

- [Jak skonfigurować usługę Azure RBAC](../role-based-access-control/role-assignments-portal.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: Wymuś kontrolę dostępu przy użyciu ochrony przed utratą danych opartą na hoście

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone do IaaS zasobów obliczeniowych.

Firma Microsoft zarządza podstawową infrastrukturą dla Azure Functions i ma zaimplementowane ścisłe kontrole, aby zapobiec utracie lub narażeniu danych klientów.

- [Zrozumienie ochrony danych klientów na platformie Azure](../security/fundamentals/protection-customer-data.md)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: Szyfruj poufne informacje w spoczynku

**Wskazówki**: podczas tworzenia aplikacji funkcji należy utworzyć konto usługi Azure Storage ogólnego przeznaczenia lub połączyć się z nim, które obsługuje magazyn obiektów blob, kolejek i tabel. Wynika to z faktu, że funkcje programu opierają się na usłudze Azure Storage w przypadku operacji takich jak zarządzanie wyzwalaczami i rejestrowanie wykonań funkcji. Usługa Azure Storage szyfruje wszystkie dane na koncie magazynu w stanie spoczynku. Domyślnie dane są szyfrowane przy użyciu kluczy zarządzanych przez firmę Microsoft. Aby uzyskać dodatkową kontrolę nad kluczami szyfrowania, można podać klucze zarządzane przez klienta do szyfrowania obiektów blob i danych plików. Te klucze muszą być obecne w Azure Key Vault, aby aplikacja funkcji mogła uzyskać dostęp do konta magazynu.

- [Poznaj zagadnienia dotyczące magazynu Azure Functions](./storage-considerations.md)

- [Informacje o szyfrowaniu usługi Azure Storage w przypadku przechowywania danych](../storage/common/storage-service-encryption.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: udostępnione

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: Rejestruj i Ostrzegaj o zmianach krytycznych zasobów platformy Azure

**Wskazówki**: Użyj Azure monitor z dziennikiem aktywności platformy Azure, aby utworzyć alerty, kiedy zmiany są wprowadzane do produkcyjnych aplikacji funkcji platformy Azure, a także innych krytycznych lub pokrewnych zasobów.

- [Jak utworzyć alerty dla zdarzeń dziennika aktywności platformy Azure](../azure-monitor/platform/alerts-activity-log.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

## <a name="vulnerability-management"></a>Zarządzanie lukami w zabezpieczeniach

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Zarządzanie lukami w zabezpieczeniach](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: uruchamianie narzędzi do skanowania automatycznych luk w zabezpieczeniach

**Wskazówki**: należy wdrożyć DevSecOpsą, aby zapewnić bezpieczeństwo aplikacji Azure Functions i zachować bezpieczeństwo, jak to możliwe przez cały czas trwania cyklu życia. DevSecOps w organizacji zespół ds. zabezpieczeń i ich możliwości w DevOpsych praktykach, co sprawia, że bezpieczeństwo jest odpowiedzialne za wszystkich członków zespołu.

Ponadto postępuj zgodnie z zaleceniami Azure Security Center, aby pomóc w zabezpieczeniu aplikacji funkcji platformy Azure.

- [Jak dodać ciągłą weryfikację zabezpieczeń do potoku CI/CD](/azure/devops/migrate/security-validation-cicd-pipeline?view=azure-devops)

- [Jak zaimplementować zalecenia dotyczące oceny luk w zabezpieczeniach Azure Security Center](../security-center/deploy-vulnerability-assessment-vm.md)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="52-deploy-an-automated-operating-system-patch-management-solution"></a>5,2: Wdróż automatyczne rozwiązanie do zarządzania poprawkami systemu operacyjnego

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone do IaaS zasobów obliczeniowych.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5,3: Wdróż zautomatyzowane rozwiązanie do zarządzania poprawkami oprogramowania innych firm

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone do IaaS zasobów obliczeniowych.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: porównanie luk w zabezpieczeniach z tyłu do tyłu

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone do IaaS zasobów obliczeniowych.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: Użyj procesu oceny ryzyka, aby określić priorytety korygowania odkrytych luk w zabezpieczeniach

**Wskazówki**: Firma Microsoft przeprowadza zarządzanie lukami w systemach, które obsługują Azure Functions, jednak można użyć ważności zaleceń w ramach Azure Security Center, a także bezpiecznego oceny, aby zmierzyć ryzyko w danym środowisku. Twój bezpieczny wynik jest oparty na liczbie Security Center zaleceń, które zostały przez Ciebie skorygowane. Aby określić priorytety rekomendacji do rozwiązania, należy wziąć pod uwagę ważność każdego z nich.

- [Przewodnik referencyjny dotyczący zaleceń dotyczących zabezpieczeń](../security-center/recommendations-reference.md)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: udostępnione

## <a name="inventory-and-asset-management"></a>Zarządzanie magazynem i zasobami

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Spis i zarządzanie zasobami](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-azure-asset-discovery"></a>6,1: Użyj odnajdywania zasobów platformy Azure

**Wskazówki**: Użyj grafu zasobów platformy Azure do wykonywania zapytań/odnajdywania wszystkich zasobów (takich jak obliczenia, magazyn, Sieć, porty i protokoły itp.) w ramach subskrypcji.  Upewnij się, że masz odpowiednie uprawnienia (odczyt) w dzierżawie i Wylicz wszystkie subskrypcje platformy Azure oraz zasoby w ramach subskrypcji.

Mimo że klasyczne zasoby platformy Azure mogą zostać odnalezione za pośrednictwem grafu zasobów, zdecydowanie zaleca się tworzenie i używanie Azure Resource Manager zasobów do przodu.

- [Jak tworzyć zapytania przy użyciu grafu zasobów platformy Azure](../governance/resource-graph/first-query-portal.md)

- [Jak wyświetlić subskrypcje platformy Azure](/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

- [Opis kontroli RBAC platformy Azure](../role-based-access-control/overview.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="62-maintain-asset-metadata"></a>6,2: Konserwowanie metadanych zasobów

**Wskazówki**: stosowanie tagów do zasobów platformy Azure, dzięki czemu metadane są logicznie zorganizowane w taksonomię.

- [Tworzenie i używanie tagów](../azure-resource-manager/management/tag-resources.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: Usuń nieautoryzowane zasoby platformy Azure

**Wskazówki**: używanie tagowania, grup zarządzania i oddzielnych subskrypcji, gdzie to konieczne, do organizowania i śledzenia zasobów platformy Azure. Regularnie Uzgadniaj spis i zapewnij, że nieautoryzowane zasoby są usuwane z subskrypcji w odpowiednim czasie.

Ponadto Użyj zasad platformy Azure, aby wprowadzić ograniczenia dotyczące typu zasobów, które mogą być tworzone w subskrypcjach klientów, korzystając z następujących wbudowanych definicji zasad: Niedozwolone typy zasobów dozwolone typy zasobów

- [Jak utworzyć dodatkowe subskrypcje platformy Azure](../cost-management-billing/manage/create-subscription.md)

- [Jak utworzyć Grupy zarządzania](../governance/management-groups/create-management-group-portal.md)

- [Tworzenie i używanie tagów](../azure-resource-manager/management/tag-resources.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6,4: przechowywanie spisu zatwierdzonych zasobów platformy Azure i tytułów oprogramowania

**Wskazówki**: Definiowanie zatwierdzonych zasobów platformy Azure i zatwierdzonego oprogramowania dla zasobów obliczeniowych.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: Monitoruj niezatwierdzone zasoby platformy Azure

**Wskazówki**: Użyj Azure Policy, aby umieścić ograniczenia dotyczące typu zasobów, które można utworzyć w ramach subskrypcji. 

Użyj grafu zasobów platformy Azure do wykonywania zapytań/odnajdywania zasobów w ramach subskrypcji.  Upewnij się, że wszystkie zasoby platformy Azure obecne w środowisku są zatwierdzone. 

- [Jak skonfigurować Azure Policy i zarządzać nimi](../governance/policy/tutorials/create-and-manage.md)

- [Jak tworzyć zapytania za pomocą usługi Azure Graph](../governance/resource-graph/first-query-portal.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: Monitoruj niezatwierdzone aplikacje oprogramowania w ramach zasobów obliczeniowych

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone do IaaS zasobów obliczeniowych.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: Usuń niezatwierdzone zasoby platformy Azure i aplikacje oprogramowania

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone do IaaS zasobów obliczeniowych.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="68-use-only-approved-applications"></a>6,8: Używaj tylko zatwierdzonych aplikacji

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone do IaaS zasobów obliczeniowych.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="69-use-only-approved-azure-services"></a>6,9: Używaj tylko zatwierdzonych usług platformy Azure

**Wskazówki**: Użyj Azure Policy, aby wprowadzić ograniczenia dotyczące typu zasobów, które mogą być tworzone w subskrypcjach klientów, przy użyciu następujących wbudowanych definicji zasad: Niedozwolone typy zasobów dozwolone typy zasobów

- [Jak skonfigurować Azure Policy i zarządzać nimi](../governance/policy/tutorials/create-and-manage.md)

- [Jak odmówić określonego typu zasobu za pomocą Azure Policy](../governance/policy/samples/index.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="610-implement-approved-application-list"></a>6,10: Zaimplementuj listę zatwierdzonych aplikacji

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone do IaaS zasobów obliczeniowych.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="611-limit-users-ability-to-interact-with-azure-resources-manager-via-scripts"></a>6,11: Ogranicz możliwość korzystania przez użytkowników z usługi Azure Resources za pośrednictwem skryptów

**Wskazówki**: Skonfiguruj dostęp warunkowy platformy Azure, aby ograniczyć możliwość korzystania przez użytkowników z Azure Resource Manager przez skonfigurowanie "blokowania dostępu" dla aplikacji "Microsoft Azure Management".

- [Jak skonfigurować dostęp warunkowy w celu blokowania dostępu do Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6,12: Ogranicz możliwość wykonywania skryptów w zasobach obliczeniowych przez użytkowników

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone do IaaS zasobów obliczeniowych.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: fizyczne lub logiczne rozdzielenie aplikacji wysokiego ryzyka

**Wskazówki**: dla wrażliwych lub wysoce narażonych aplikacji funkcji platformy Azure, zaimplementuj osobne subskrypcje i/lub grupy zarządzania, aby zapewnić izolację.

Wdrażaj aplikacje funkcji platformy Azure o wysokim ryzyku do własnych Virtual Network (VNet). Zabezpieczenia obwodowe w Azure Functions są realizowane za sieci wirtualnych. Funkcje działające w planie Premium lub App Service Environment (ASE) można zintegrować z sieci wirtualnych. Wybierz najlepszą architekturę przypadku użycia.

- [Opcje sieciowe usługi Azure Functions](./functions-networking-options.md)

- [Plan Azure Functions Premium](./functions-scale.md#premium-plan)

- [Networking considerations for an App Service Environment (Zagadnienia dotyczące sieci w środowisku App Service Environment)](../app-service/environment/network-info.md)

- [Jak utworzyć zewnętrzny środowisko ASE](../app-service/environment/create-external-ase.md)

Jak utworzyć wewnętrzny ASE:

- [https://docs.microsoft.com/azure/app-service/environment/create-ilb-as](../virtual-network/quick-create-portal.md)

- [Jak utworzyć sieciowej grupy zabezpieczeń z konfiguracją zabezpieczeń](../virtual-network/tutorial-filter-network-traffic.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

## <a name="secure-configuration"></a>Bezpieczna konfiguracja

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: bezpieczna konfiguracja](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: Ustanów bezpieczne konfiguracje dla wszystkich zasobów platformy Azure

**Wskazówki**: Definiowanie i implementowanie standardowych konfiguracji zabezpieczeń aplikacji funkcji platformy Azure przy użyciu Azure Policy. Użyj aliasów Azure Policy w przestrzeni nazw "Microsoft. Web", aby utworzyć zasady niestandardowe do inspekcji lub wymuszania konfiguracji aplikacji Azure Functions. Możesz również używać wbudowanych definicji zasad, takich jak:
- Tożsamość zarządzana powinna być używana w aplikacja funkcji
- Zdalne debugowanie powinno zostać wyłączone dla aplikacji funkcji
- aplikacja funkcji powinna być dostępna tylko za pośrednictwem protokołu HTTPS

- [Jak wyświetlić dostępne aliasy Azure Policy](/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

- [Jak skonfigurować Azure Policy i zarządzać nimi](../governance/policy/tutorials/create-and-manage.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: Ustanów bezpieczne konfiguracje systemów operacyjnych

**Wskazówki**: nie dotyczy; te wytyczne są przeznaczone dla IaaS zasobów obliczeniowych.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: obsługa bezpiecznych konfiguracji zasobów platformy Azure

**Wskazówki**: Użyj zasad platformy Azure [Odmów] i [Wdróż, jeśli nie istnieje], aby wymusić bezpieczne ustawienia dla zasobów platformy Azure.

- [Jak skonfigurować Azure Policy i zarządzać nimi](../governance/policy/tutorials/create-and-manage.md)

- [Zrozumienie efektów Azure Policy](../governance/policy/concepts/effects.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: Zachowaj konfiguracje bezpiecznego systemu operacyjnego

**Wskazówki**: nie dotyczy; Chociaż istnieje możliwość wdrożenia funkcji lokalnych, te wytyczne są przeznaczone dla zasobów obliczeniowych IaaS. Podczas wdrażania funkcji lokalnych użytkownik jest odpowiedzialny za bezpieczną konfigurację środowiska.

- [Zrozumienie funkcji lokalnych](./functions-runtime-install.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: bezpiecznie przechowuj konfigurację zasobów platformy Azure

**Wskazówki**: bezpieczne przechowywanie i zarządzanie szablonami ARM oraz niestandardowymi definicjami zasad platformy Azure w kontroli źródła.

- [Co to jest infrastruktura jako kod](/azure/devops/learn/what-is-infrastructure-as-code)

- [Projektowanie zasad jako przepływów pracy kodu](../governance/policy/concepts/policy-as-code.md)

- [Jak przechowywać kod w usłudze Azure DevOps](/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [Dokumentacja Azure Repos](/azure/devops/repos/index?view=azure-devops)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: bezpieczne przechowywanie niestandardowych obrazów systemu operacyjnego

**Wskazówki**: nie dotyczy; te wytyczne są przeznaczone dla IaaS zasobów obliczeniowych.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="77-deploy-system-configuration-management-tools"></a>7,7: Wdrażanie narzędzi do zarządzania konfiguracją systemu

**Wskazówki**: Użyj wbudowanych definicji Azure Policy, a także aliasów Azure Policy w przestrzeni nazw "Microsoft. Web", aby utworzyć zasady niestandardowe na potrzeby alertów, inspekcji i wymuszania konfiguracji systemu. Dodatkowo opracowuj proces i potok na potrzeby zarządzania wyjątkami zasad.

- [Jak skonfigurować Azure Policy i zarządzać nimi](../governance/policy/tutorials/create-and-manage.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7,8: Wdrażanie narzędzi do zarządzania konfiguracją systemu dla systemów operacyjnych

**Wskazówki**: nie dotyczy; te wytyczne są przeznaczone dla IaaS zasobów obliczeniowych.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7,9: Zaimplementuj automatyczne monitorowanie konfiguracji dla usług platformy Azure

**Wskazówki**: Użyj wbudowanych definicji Azure Policy, a także aliasów Azure Policy w przestrzeni nazw "Microsoft. Web", aby utworzyć zasady niestandardowe na potrzeby alertów, inspekcji i wymuszania konfiguracji systemu. Użyj zasad platformy Azure [Audit], [Odmów] i [Wdróż, jeśli nie istnieje], aby automatycznie wymuszać konfiguracje dla zasobów platformy Azure.

- [Jak skonfigurować Azure Policy i zarządzać nimi](../governance/policy/tutorials/create-and-manage.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: Zaimplementuj automatyczne monitorowanie konfiguracji dla systemów operacyjnych

**Wskazówki**: nie dotyczy; te wytyczne są przeznaczone dla IaaS zasobów obliczeniowych.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="711-manage-azure-secrets-securely"></a>7,11: bezpieczne zarządzanie wpisami tajnymi platformy Azure

**Wskazówki**: Użyj zarządzanych tożsamości w połączeniu z Azure Key Vault, aby uprościć i zabezpieczyć poufne Zarządzanie aplikacjami w chmurze. Tożsamości zarządzane umożliwiają aplikacji funkcji uwierzytelnianie w dowolnej usłudze, która obsługuje uwierzytelnianie usługi Azure AD, w tym Key Vault, bez żadnych poświadczeń w kodzie.

- [Jak utworzyć Key Vault](../key-vault/secrets/quick-create-portal.md)

- [Jak używać tożsamości zarządzanych do App Service i Azure Functions](../app-service/overview-managed-identity.md)

* [Jak przeprowadzić uwierzytelnianie w Key Vault](../key-vault/general/authentication.md)

* [Jak przypisać zasady dostępu Key Vault](../key-vault/general/assign-access-policy-portal.md)

- [Użyj Key Vault odwołań dla App Service i Azure Functions](../app-service/app-service-key-vault-references.md)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: bezpieczne i automatyczne zarządzanie tożsamościami

**Wskazówki**: używanie tożsamości zarządzanych do udostępniania aplikacji funkcji platformy Azure z automatyczną tożsamością zarządzaną w usłudze Azure AD. Tożsamości zarządzane umożliwiają uwierzytelnianie w dowolnej usłudze, która obsługuje uwierzytelnianie usługi Azure AD, w tym Key Vault, bez żadnych poświadczeń w kodzie.

- [Jak używać tożsamości zarządzanych do App Service i Azure Functions](../app-service/overview-managed-identity.md)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: eliminowanie nieprzewidzianego narażenia na poświadczenia

**Wskazówki**: Implementuj skaner poświadczeń, aby identyfikować poświadczenia w kodzie. Skaner poświadczeń zachęca również do przemieszczania odnalezionych poświadczeń do bardziej bezpiecznych lokalizacji, takich jak Azure Key Vault. 

- [Jak skonfigurować skaner poświadczeń](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

## <a name="malware-defense"></a>Ochrona przed złośliwym oprogramowaniem

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Obrona złośliwego oprogramowania](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8,1: Użyj centralnie zarządzanego oprogramowania chroniącego przed złośliwym oprogramowaniem

**Wskazówki**: nie dotyczy; te wytyczne są przeznaczone dla IaaS zasobów obliczeniowych.

Oprogramowanie chroniące przed złośliwym oprogramowaniem firmy Microsoft jest włączone na podstawowym hoście, który obsługuje usługi platformy Azure (na przykład Azure Functions), ale nie jest uruchamiane w treści klienta.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: Microsoft

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: przeskanuj pliki przed przekazaniem do zasobów platformy Azure, które nie są obliczeniowe

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone dla zasobów nieobliczeniowych zaprojektowanych do przechowywania danych.


**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8,3: Upewnij się, że oprogramowanie chroniące przed złośliwym oprogramowaniem i podpisy zostały zaktualizowane

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone dla zasobów nieobliczeniowych zaprojektowanych do przechowywania danych.

Oprogramowanie chroniące przed złośliwym oprogramowaniem firmy Microsoft jest włączone na podstawowym hoście, który obsługuje usługi platformy Azure (na przykład Azure Functions), ale nie jest uruchamiane w treści klienta.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

## <a name="data-recovery"></a>Odzyskiwanie danych

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: odzyskiwanie danych](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: Zapewnij regularne zautomatyzowane przywracanie awaryjne

**Wskazówki**: Użyj funkcji tworzenia kopii zapasowych i przywracania, aby zaplanować regularne kopie zapasowe aplikacji. Aplikacje funkcji działające w planie Premium mają takie same możliwości hostingu jak aplikacje sieci Web w Azure App Service, które obejmują funkcję "wykonywanie kopii zapasowych i przywracanie".

Korzystaj również z rozwiązań kontroli źródła, takich jak Azure Repos i Azure DevOps, aby bezpiecznie przechowywać kod i zarządzać nim. Usługa Azure DevOps Services wykorzystuje wiele funkcji usługi Azure Storage w celu zapewnienia dostępności danych w przypadku awarii sprzętowej, przerw w działaniu usługi lub awarii regionu. Ponadto zespół usługi Azure DevOps postępuje zgodnie z procedurami w celu ochrony danych przed przypadkowym lub złośliwym usunięciem.

- [Tworzenie kopii zapasowej aplikacji na platformie Azure](../app-service/manage-backup.md)

- [Zrozumienie dostępności danych w usłudze Azure DevOps](/azure/devops/organizations/security/data-protection?view=azure-devops#data-availability)

- [Jak przechowywać kod w usłudze Azure DevOps](/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [Dokumentacja Azure Repos](/azure/devops/repos/index?view=azure-devops)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: wykonaj kompletne kopie zapasowe systemu i Utwórz kopię zapasową wszystkich kluczy zarządzanych przez klienta

**Wskazówki**: Użyj funkcji tworzenia kopii zapasowych i przywracania, aby zaplanować regularne kopie zapasowe aplikacji. Aplikacje funkcji działające w planie Premium mają takie same możliwości hostingu jak aplikacje sieci Web w Azure App Service, które obejmują funkcję "wykonywanie kopii zapasowych i przywracanie". Utwórz kopię zapasową kluczy zarządzanych przez klienta w Azure Key Vault.

Korzystaj również z rozwiązań kontroli źródła, takich jak Azure Repos i Azure DevOps, aby bezpiecznie przechowywać kod i zarządzać nim. Usługa Azure DevOps Services wykorzystuje wiele funkcji usługi Azure Storage w celu zapewnienia dostępności danych w przypadku awarii sprzętowej, przerw w działaniu usługi lub awarii regionu. Ponadto zespół usługi Azure DevOps postępuje zgodnie z procedurami w celu ochrony danych przed przypadkowym lub złośliwym usunięciem.

- [Tworzenie kopii zapasowej aplikacji na platformie Azure](../app-service/manage-backup.md)

- [Jak utworzyć kopię zapasową kluczy magazynu kluczy na platformie Azure](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

- [Zrozumienie dostępności danych w usłudze Azure DevOps](/azure/devops/organizations/security/data-protection?view=azure-devops#data-availability)

- [Jak przechowywać kod w usłudze Azure DevOps](/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [Dokumentacja Azure Repos](/azure/devops/repos/index?view=azure-devops)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: Weryfikuj wszystkie kopie zapasowe, w tym klucze zarządzane przez klienta

**Wskazówki**: Zapewnij okresowe wykonywanie przywracania za pomocą funkcji tworzenia kopii zapasowych i przywracania. W przypadku używania innej lokalizacji offline do tworzenia kopii zapasowej kodu okresowo zapewniają możliwość wykonywania pełnych operacji przywracania. Testowanie przywracania kopii zapasowych kluczy zarządzanych przez klienta.

- [Przywracanie aplikacji na platformie Azure z kopii zapasowej](../app-service/web-sites-restore.md)

- [Przywracanie aplikacji na platformie Azure z migawki](../app-service/app-service-web-restore-snapshots.md)

- [Jak przywrócić klucze magazynu kluczy na platformie Azure](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: zapewnianie ochrony kopii zapasowych i kluczy zarządzanych przez klienta

**Wskazówki**: kopie zapasowe z funkcji tworzenia kopii zapasowych i przywracania używają konta usługi Azure Storage w ramach subskrypcji. Usługa Azure Storage szyfruje wszystkie dane na koncie magazynu w stanie spoczynku. Domyślnie dane są szyfrowane przy użyciu kluczy zarządzanych przez firmę Microsoft. Aby uzyskać dodatkową kontrolę nad kluczami szyfrowania, można podać klucze zarządzane przez klienta do szyfrowania danych magazynu.

Jeśli używasz kluczy zarządzanych przez klienta, upewnij się, Soft-Delete w Key Vault jest włączona ochrona kluczy przed przypadkowym lub złośliwym usunięciem.

- [Szyfrowanie danych magazynowanych w usłudze Azure Storage](../storage/common/storage-service-encryption.md)

- [Jak włączyć Soft-Delete w Key Vault](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: udostępnione

## <a name="incident-response"></a>Reagowanie na zdarzenia

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: odpowiedź na zdarzenia](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: Tworzenie przewodnika odpowiedzi na zdarzenia

**Wskazówki**: Tworzenie przewodnika odpowiedzi na zdarzenia dla organizacji. Upewnij się, że istnieją zarejestrowane plany reakcji na zdarzenia, które definiują wszystkie role pracowników, a także etapy obsługi zdarzeń/zarządzania z wykrywania do oceny po zdarzeniu.

- [Jak skonfigurować automatyzację przepływu pracy w programie Azure Security Center](../security-center/security-center-planning-and-operations-guide.md)

- [Wskazówki dotyczące tworzenia własnego procesu reagowania na zdarzenia zabezpieczeń](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Anatomia incydentu centrum Microsoft Security Response](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Klient może również korzystać z przewodnika obsługi zdarzeń związanych z bezpieczeństwem programu NIST, aby pomóc w tworzeniu własnego planu reagowania na zdarzenia](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: Tworzenie oceny incydentu i procedury priorytetyzacji

**Wskazówki**: Security Center przypisuje ważność do każdego alertu, aby pomóc w ustaleniu, które alerty należy najpierw zbadać. Ważność jest oparta na tym, jak dobrze Security Center znajduje się w wyszukiwaniu lub analitycznym używanym do wystawiania alertu, a także poziom pewności, że istniało złośliwy wpływ na działanie, które prowadziło do alertu.

Dodatkowo jasno Oznacz subskrypcje (na przykład produkcyjny, nieprodukcyjny) i Utwórz system nazewnictwa, aby jasno identyfikować i klasyfikować zasoby platformy Azure.

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: udostępnione

### <a name="103-test-security-response-procedures"></a>10,3: procedury odpowiedzi na zabezpieczenia testowe

**Wskazówki**: przeprowadzanie ćwiczeń w celu przetestowania możliwości reagowania na zdarzenia systemu w regularnych erze. Zidentyfikuj słabe punkty i przerwy i popraw plan zgodnie z wymaganiami.

- [Zapoznaj się z publikacją NIST: Przewodnik dotyczący testowania, uczenia i ćwiczeń programów dla planów i możliwości IT](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: Podaj szczegóły kontaktu dotyczącego zabezpieczeń i Skonfiguruj powiadomienia dotyczące alertów dotyczących zdarzeń związanych z zabezpieczeniami

**Wskazówki**: informacje kontaktowe dotyczące zdarzenia zabezpieczeń będą używane przez firmę Microsoft do skontaktowania się z Tobą, jeśli firma Microsoft Security Response Center (MSRC) wykryje, że dostęp do danych klienta został uzyskany przez nielegalną lub nieautoryzowaną osobę.  Przejrzyj zdarzenia po fakcie, aby upewnić się, że problemy zostały rozwiązane.

- [Jak ustawić kontakt z zabezpieczeniami Azure Security Center](../security-center/security-center-provide-security-contact-details.md)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: Uwzględnij alerty zabezpieczeń w systemie odpowiedzi na zdarzenia

**Wskazówki**: eksportowanie alertów i zaleceń dotyczących Azure Security Center przy użyciu funkcji eksportu ciągłego. Eksport ciągły umożliwia wyeksportowanie alertów i zaleceń ręcznie lub w stały sposób ciągły. Możesz użyć łącznika danych Azure Security Center do przesyłania strumieniowego alertów do usługi Azure wskaźnikowej.

- [Jak skonfigurować eksport ciągły](../security-center/continuous-export.md)

- [Jak przesłać strumieniowo alerty do usługi Azure wskaźnikowego](../sentinel/connect-azure-security-center.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: Automatyzowanie odpowiedzi na alerty zabezpieczeń

**Wskazówki**: Użyj funkcji automatyzacji przepływu pracy w Azure Security Center, aby automatycznie wyzwalać odpowiedzi na alerty zabezpieczeń i zalecenia z Logic Apps.

- [Jak skonfigurować automatyzację przepływu pracy i Logic Apps](../security-center/workflow-automation.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

## <a name="penetration-tests-and-red-team-exercises"></a>Testy penetracyjne i ćwiczenia typu „red team”

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: testy penetracji i czerwone ćwiczenia zespołu](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: Przeprowadź regularne testowanie penetracji zasobów platformy Azure i zadbaj o skorygowanie wszystkich krytycznych ustaleń dotyczących zabezpieczeń

**Wskazówki**: Postępuj zgodnie z zasadami firmy Microsoft dotyczącymi zaangażowania, aby upewnić się, że testy penetracji nie naruszają zasad firmy Microsoft. Korzystaj z strategii firmy Microsoft i wykonywania testów na żywo z obsługą tworzenia zespołu, usług i aplikacji w chmurze, które są zarządzane przez firmę Microsoft.

- [Reguły testowania penetracji zaangażowania](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Microsoft Cloud Tworzenie zespołu Red.](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: udostępnione

## <a name="next-steps"></a>Następne kroki

- Zobacz [test porównawczy zabezpieczeń platformy Azure](../security/benchmarks/overview.md)
- Dowiedz się więcej o [punktach odniesienia zabezpieczeń platformy Azure](../security/benchmarks/security-baselines-overview.md)