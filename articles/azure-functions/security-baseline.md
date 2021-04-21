---
title: Punkt odniesienia zabezpieczeń platformy Azure dla Azure Functions
description: Punkt Azure Functions zabezpieczeń zawiera wskazówki proceduralne i zasoby dotyczące wdrażania zaleceń dotyczących zabezpieczeń określonych w te testach porównawczych zabezpieczeń platformy Azure.
author: msmbaldwin
ms.service: azure-functions
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark, devx-track-azurepowershell
ms.openlocfilehash: 90ae774a4e243b854758c1c7a6cc10a882cd584f
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107831082"
---
# <a name="azure-security-baseline-for-azure-functions"></a>Punkt odniesienia zabezpieczeń platformy Azure dla Azure Functions

Ten punkt odniesienia zabezpieczeń stosuje wskazówki z testu porównawczego zabezpieczeń platformy Azure w wersji [1.0](../security/benchmarks/overview-v1.md) do Azure Functions. Test porównawczy zabezpieczeń platformy Azure zawiera zalecenia dotyczące sposobu zabezpieczania rozwiązań w chmurze na platformie Azure.
Zawartość jest pogrupowana według **kontrolek zabezpieczeń** zdefiniowanych przez test porównawczy zabezpieczeń platformy Azure i powiązanych wskazówek dotyczących Azure Functions. **Kontrolki** nie mają zastosowania Azure Functions zostały wykluczone.

 
Aby zobaczyć, Azure Functions mapuje całkowicie na test porównawczy zabezpieczeń platformy Azure, zobacz pełny Azure Functions [mapowania punktów odniesienia zabezpieczeń.](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)

## <a name="network-security"></a>Bezpieczeństwo sieci

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: bezpieczeństwo sieci](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1. Ochrona zasobów platformy Azure w sieciach wirtualnych

**Wskazówki:** Integrowanie aplikacji Azure Functions z siecią wirtualną platformy Azure. Aplikacje funkcji działające w planie Premium mają takie same możliwości hostingu, jak aplikacje internetowe w Azure App Service, co obejmuje funkcję "integracja z siecią wirtualną".  Sieci wirtualne platformy Azure umożliwiają umieszczanie wielu zasobów platformy Azure, takich jak Azure Functions, w sieci routowalnej bez Internetu.

- [Jak zintegrować usługę Functions z usługą Azure Virtual Network](functions-create-vnet.md)

- [Opis integracji z siecią wirtualną dla Azure Functions i Azure App Service](../app-service/web-sites-integrate-with-vnet.md)

**Odpowiedzialność**: Klient

**Azure Security Center:** Test porównawczy zabezpieczeń platformy [Azure](/azure/governance/policy/samples/azure-security-benchmark) to domyślna inicjatywa zasad dla usługi Security Center i stanowi podstawę rekomendacji Security Center [firmy .](/azure/security-center/security-center-recommendations) Definicje Azure Policy związane z tą kontrolką są włączane automatycznie przez Security Center. Alerty związane z tą kontrolą mogą [wymagać Azure Defender](/azure/security-center/azure-defender) planu dla powiązanych usług.

**Azure Policy wbudowane definicje — Microsoft.Web:**

[!INCLUDE [Resource Policy for Microsoft.Web 1.1](../../includes/policy/standards/asb/rp-controls/microsoft.web-1-1.md)]

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2: Monitorowanie i rejestrowanie konfiguracji i ruchu sieci wirtualnych, podsieci i interfejsów sieciowych

**Wskazówki:** używaj Azure Security Center i postępuj zgodnie z zaleceniami ochrony sieci, aby ułatwić zabezpieczanie zasobów sieciowych i konfiguracji sieci związanych z Azure Functions aplikacji.

Jeśli używasz sieciowych grup zabezpieczeń z implementacją Azure Functions, włącz dzienniki przepływu sieciowych grup zabezpieczeń i wyślij dzienniki na konto usługi Azure Storage w celu przeprowadzania inspekcji ruchu. Możesz również wysyłać dzienniki przepływu sieciowych grup zabezpieczeń do obszaru roboczego usługi Log Analytics i używać Analiza ruchu, aby zapewnić wgląd w przepływ ruchu w chmurze platformy Azure. Niektóre zalety usługi Analiza ruchu to możliwość wizualizacji aktywności sieciowej i identyfikowania punktów aktywności, identyfikowania zagrożeń bezpieczeństwa, zrozumienia wzorców przepływu ruchu i identyfikowania błędnych konfiguracji sieci.

- [Informacje o zabezpieczeniach sieciowych zapewnianych przez Azure Security Center](../security-center/security-center-network-recommendations.md)

- [Jak włączyć dzienniki przepływu sieciowej organizacji zabezpieczeń](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Jak włączyć i używać Analiza ruchu](../network-watcher/traffic-analytics.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="13-protect-critical-web-applications"></a>1.3: Ochrona krytycznych aplikacji internetowych

**Wskazówki:** Aby w pełni zabezpieczyć punkty końcowe Azure Functions w środowisku produkcyjnym, należy rozważyć zaimplementowanie jednej z następujących opcji zabezpieczeń na poziomie aplikacji funkcji:

- Włączanie uwierzytelniania App Service autoryzacji dla aplikacji funkcji

- Używanie usługi Azure API Management (APIM) do uwierzytelniania żądań

- Wdrażanie aplikacji funkcji w Azure App Service Environment.

Ponadto upewnij się, że debugowanie zdalne zostało wyłączone dla aplikacji Azure Functions. Ponadto udostępnianie zasobów między źródłami (CORS) nie powinno zezwalać wszystkim domenom na dostęp do aplikacji funkcji na platformie Azure. Zezwalaj tylko wymaganym domenom na interakcję z aplikacją funkcji.

Rozważ wdrożenie Azure Web Application Firewall (WAF) w ramach konfiguracji sieci w celu dodatkowej inspekcji ruchu przychodzącego. Włącz ustawienie diagnostyczne dla aplikacji internetowej i pozysuj dzienniki do konta magazynu, centrum zdarzeń lub obszaru roboczego usługi Log Analytics. 

- [Jak zabezpieczyć punkty Azure Functions końcowe w środowisku produkcyjnym](./functions-bindings-http-webhook-trigger.md?tabs=csharp#secure-an-http-endpoint-in-production)

- [Jak wdrożyć usługę Azure WAF](../web-application-firewall/ag/create-waf-policy-ag.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Test [porównawczy zabezpieczeń](/azure/governance/policy/samples/azure-security-benchmark) platformy Azure jest domyślną inicjatywą zasad dla usługi Security Center i stanowi podstawę Security Center [rekomendacji firmy](/azure/security-center/security-center-recommendations). Definicje Azure Policy związane z tą kontrolką są włączane automatycznie przez Security Center. Alerty związane z tą kontrolą mogą [wymagać Azure Defender](/azure/security-center/azure-defender) planu dla powiązanych usług.

**Azure Policy wbudowane definicje — Microsoft.Web:**

[!INCLUDE [Resource Policy for Microsoft.Web 1.3](../../includes/policy/standards/asb/rp-controls/microsoft.web-1-3.md)]

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Odmowa komunikacji ze znanymi złośliwymi adresami IP

**Wskazówki:** włącz DDoS Protection Standardowa w sieciach wirtualnych skojarzonych z aplikacjami funkcji, aby chronić przed atakami DDoS. Użyj funkcji analizy zagrożeń w Azure Security Center Zintegrowane, aby uniemożliwić komunikację ze znanymi złośliwymi lub nieużywanymi publicznymi adresami IP.

Ponadto skonfiguruj bramę frontonu, taką jak Azure Web Application Firewall, w celu uwierzytelniania wszystkich żądań przychodzących i filtrowania złośliwego ruchu. Azure Web Application Firewall zabezpieczać aplikację funkcji przez inspekcję przychodzącego ruchu internetowego w celu blokowania wstrzyknięcia kodu SQL, wykonywania skryptów między witrynami, przekazywania złośliwego oprogramowania i ataków DDoS. Wprowadzenie usługi Web Application Firewall wymaga App Service Environment lub korzystania z prywatnych punktów końcowych. Prywatnych punktów końcowych można używać dla funkcji hostowanych w planach Premium i App Service końcowych.

- [Opcje sieciowe usługi Azure Functions](functions-networking-options.md)

- [Jak skonfigurować ochronę przed DDoS](../ddos-protection/manage-ddos-protection.md)

- [Understand Azure Security Center Integrated Threat Intelligence](../security-center/azure-defender.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="15-record-network-packets"></a>1.5. Rejestrowanie pakietów sieciowych

**Wskazówki:** Jeśli używasz sieciowej grupy zabezpieczeń z implementacją Azure Functions, włącz dzienniki przepływu sieciowej grupy zabezpieczeń i wyślij dzienniki do konta magazynu w celu inspekcji ruchu. Możesz również wysyłać dzienniki przepływu do obszaru roboczego usługi Log Analytics i używać Analiza ruchu, aby zapewnić wgląd w przepływ ruchu w chmurze platformy Azure. Niektóre zalety usługi Analiza ruchu to możliwość wizualizacji aktywności sieciowej i identyfikowania punktów aktywności, identyfikowania zagrożeń bezpieczeństwa, zrozumienia wzorców przepływu ruchu i identyfikowania błędnych konfiguracji sieci.

- [How to Enable network security group flow Logs](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Jak włączyć i używać Analiza ruchu](../network-watcher/traffic-analytics.md)

- [Jak włączyć Network Watcher](../network-watcher/network-watcher-create.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6. Wdrażanie sieciowych systemów wykrywania/zapobiegania włamań (IDS/IPS)

**Wskazówki:** Skonfiguruj bramę frontonu, taką jak Azure Web Application Firewall do uwierzytelniania wszystkich żądań przychodzących i filtrowania złośliwego ruchu. Azure Web Application Firewall zabezpieczać aplikacje funkcji przez inspekcję przychodzącego ruchu internetowego w celu blokowania wstrzyknięcia kodu SQL, wykonywania skryptów między witrynami, przekazywania złośliwego oprogramowania i ataków DDoS. Wprowadzenie usługi Web Application Firewall wymaga App Service Environment lub korzystania z prywatnych punktów końcowych. Prywatne punkty końcowe mogą być dostępne dla funkcji hostowanych w planach Premium App Service premium.

Istnieje również wiele opcji platformy handlowej, takich jak Barracuda Web Application Firewall for Azure, które są dostępne na platformie Azure Marketplace które obejmują funkcje wykrywania włamań lub zapobiegania im.

- [Opcje sieciowe usługi Azure Functions](functions-networking-options.md)

- [Azure Functions Premium Plan](functions-premium-plan.md)

- [Wprowadzenie do środowisk App Service Environment](../app-service/environment/intro.md)

- [Networking considerations for an App Service Environment (Zagadnienia dotyczące sieci w środowisku App Service Environment)](../app-service/environment/network-info.md) 

- [Opis Azure Web Application Firewall](../web-application-firewall/overview.md)

- [Używanie prywatnych punktów końcowych dla Azure Functions](../app-service/networking/private-endpoint.md)

- [Understand Barracuda WAF Cloud Service](../app-service/environment/app-service-app-service-environment-web-application-firewall.md#configuring-your-barracuda-waf-cloud-service)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Zarządzanie ruchem do aplikacji internetowych

**Wskazówki:** Skonfiguruj bramę front endu dla sieci, taką jak Azure Web Application Firewall z szyfrowaniem TLS na poziomie end-to-end. Wprowadzenie usługi Web Application Firewall wymaga App Service Environment lub korzystania z prywatnych punktów końcowych. Prywatne punkty końcowe mogą być dostępne dla funkcji hostowanych w planach Premium App Service premium.

- [Opcje sieciowe usługi Azure Functions](functions-networking-options.md)

- [Azure Functions Premium Plan](functions-premium-plan.md)

- [Wprowadzenie do środowisk App Service Environment](../app-service/environment/intro.md)

- [Networking considerations for an App Service Environment (Zagadnienia dotyczące sieci w środowisku App Service Environment)](../app-service/environment/network-info.md) 

- [Opis Azure Web Application Firewall](../web-application-firewall/overview.md)

- [Jak skonfigurować end-to-end TLS przy użyciu Application Gateway z portalem](../application-gateway/end-to-end-ssl-portal.md)

- [Używanie prywatnych punktów końcowych dla Azure Functions](../app-service/networking/private-endpoint.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimalizowanie złożoności i obciążeń administracyjnych reguł zabezpieczeń sieci

**Wskazówki:** Użyj tagów Virtual Network do definiowania kontroli dostępu do sieci w sieciowej grupie zabezpieczeń lub Azure Firewall. Podczas tworzenia reguł zabezpieczeń można użyć tagów usługi zamiast konkretnych adresów IP. Określając nazwę tagu usługi (np. AzureAppService) w odpowiednim polu źródłowym lub docelowym reguły, można zezwolić na ruch dla odpowiedniej usługi lub go odmówić. Firma Microsoft zarządza prefiksami adresów obejmującymi tag usługi i automatycznie aktualizuje tag usługi w przypadku zmiany adresów.

- [Aby uzyskać więcej informacji na temat korzystania z tagów usługi](../virtual-network/service-tags-overview.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Obsługa standardowych konfiguracji zabezpieczeń dla urządzeń sieciowych

**Wskazówki:** Definiowanie i implementowanie standardowych konfiguracji zabezpieczeń dla ustawień sieciowych związanych z Azure Functions. Użyj Azure Policy aliasów w przestrzeniach nazw "Microsoft.Web" i "Microsoft.Network", aby utworzyć niestandardowe zasady inspekcji lub wymuszania konfiguracji sieci Azure Functions. Możesz również korzystać z wbudowanych definicji zasad dla Azure Functions, takich jak:

- Funkcja CORS nie powinna zezwalać każdemu zasobowi na dostęp do aplikacji funkcji

- Aplikacja funkcji powinna być dostępna tylko za pośrednictwem protokołu HTTPS

- W aplikacji funkcji powinna być używana najnowsza wersja TLS

Usługi Azure Blueprints można również użyć do uproszczenia wdrożeń platformy Azure na dużą skalę przez pakowanie kluczowych artefaktów środowiska, takich jak szablony usługi Azure Resource Manager, kontrola dostępu oparta na rolach (RBAC) platformy Azure i zasady w pojedynczej definicji strategii. Można łatwo stosować strategie do nowych subskrypcji, środowisk oraz dostrajać kontrolę i zarządzanie za pomocą wersji.

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

- [Jak utworzyć usługę Azure Blueprint](../governance/blueprints/create-blueprint-portal.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="110-document-traffic-configuration-rules"></a>1.10. Udokumentuj reguły konfiguracji ruchu

**Wskazówki:** Jeśli używasz sieciowej grupy zabezpieczeń z implementacją usługi Azure Functions, użyj tagów dla sieciowych grup zabezpieczeń i innych zasobów związanych z zabezpieczeniami sieci i przepływem ruchu. W przypadku poszczególnych reguł sieciowej grupy zabezpieczeń użyj pola "Opis", aby określić potrzeby biznesowe i/lub czas trwania i tak dalej dla wszystkich reguł, które zezwalają na ruch do/z sieci.

Użyj dowolnej wbudowanej definicji zasad platformy Azure powiązanej z tagami, takiej jak "Wymagaj tagu i jego wartości", aby upewnić się, że wszystkie zasoby są tworzone przy użyciu tagów, i powiadamiać o istniejących nieotagowanych zasobach.

Możesz użyć interfejsu Azure PowerShell wiersza polecenia platformy Azure, aby wyszukiwania lub wykonywania akcji na zasobach na podstawie ich tagów.

- [Jak tworzyć tagi i ich używać](../azure-resource-manager/management/tag-resources.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11. Używanie zautomatyzowanych narzędzi do monitorowania konfiguracji zasobów sieciowych i wykrywania zmian

**Wskazówki:** Użyj dziennika aktywności platformy Azure do monitorowania konfiguracji zasobów sieciowych i wykrywania zmian ustawień sieciowych i zasobów związanych Azure Functions wdrożeniami sieci. Utwórz alerty w Azure Monitor wyzwalane w przypadku zmiany krytycznych ustawień sieciowych lub zasobów sieciowych. 

- [Jak wyświetlać i pobierać zdarzenia dziennika aktywności platformy Azure](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [Jak tworzyć alerty w Azure Monitor](../azure-monitor/alerts/alerts-activity-log.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

## <a name="logging-and-monitoring"></a>Rejestrowanie i monitorowanie

*Aby uzyskać więcej informacji, zobacz [Test porównawczy zabezpieczeń platformy Azure: rejestrowanie i monitorowanie.](../security/benchmarks/security-control-logging-monitoring.md)*

### <a name="22-configure-central-security-log-management"></a>2.2. Konfigurowanie centralnego zarządzania dziennikami zabezpieczeń

**Wskazówki:** W przypadku rejestrowania inspekcji płaszczyzny sterowania włącz ustawienia diagnostyczne dziennika aktywności platformy Azure i wyślij dzienniki do obszaru roboczego usługi Log Analytics, centrum zdarzeń platformy Azure lub konta usługi Azure Storage w celu archiwizacji. Korzystając z danych dziennika aktywności platformy Azure, można określić "co, kto i kiedy" dla wszystkich operacji zapisu (PUT, POST, DELETE) wykonywanych na poziomie płaszczyzny sterowania dla zasobów platformy Azure.

Azure Functions oferuje również wbudowaną integrację z usługą Azure Application Insights do monitorowania funkcji. Application Insights zbiera dane dzienników, wydajności i błędów. Automatycznie wykrywa anomalie wydajności i zawiera zaawansowane narzędzia analityczne, które ułatwiają diagnozowanie problemów i zrozumienie sposobu, w jaki używane są funkcje.

Jeśli masz wbudowane niestandardowe rejestrowanie zabezpieczeń/inspekcji w aplikacji funkcji, włącz ustawienie diagnostyczne "FunctionAppLogs" i wyślij dzienniki do obszaru roboczego usługi Log Analytics, centrum zdarzeń platformy Azure lub konta usługi Azure Storage w celu archiwizacji. 

Opcjonalnie można włączyć i dołączyć dane do Azure Sentinel lub rozwiązania do zarządzania informacjami i zdarzeniami systemu innych firm. 

- [Jak włączyć ustawienia diagnostyczne dla dziennika aktywności platformy Azure](../azure-monitor/essentials/activity-log.md)

- [Jak skonfigurować usługę azure Azure Functions za pomocą usługi Azure Application Insights](functions-monitoring.md)

- [Jak włączyć ustawienia diagnostyczne (dzienniki generowane przez użytkownika) dla Azure Functions](functions-monitor-log-analytics.md)

- [Jak do dołączać Azure Sentinel](../sentinel/quickstart-onboard.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3. Włączanie rejestrowania inspekcji dla zasobów platformy Azure

**Wskazówki:** W przypadku rejestrowania inspekcji płaszczyzny sterowania włącz ustawienia diagnostyczne dziennika aktywności platformy Azure i wyślij dzienniki do obszaru roboczego usługi Log Analytics, centrum zdarzeń platformy Azure lub konta usługi Azure Storage w celu archiwizacji. Korzystając z danych dziennika aktywności platformy Azure, można określić "co, kto i kiedy" dla wszystkich operacji zapisu (PUT, POST, DELETE) wykonywanych na poziomie płaszczyzny sterowania dla zasobów platformy Azure.

Jeśli masz wbudowane niestandardowe rejestrowanie zabezpieczeń/inspekcji w aplikacji funkcji, włącz ustawienie diagnostyczne "FunctionAppLogs" i wyślij dzienniki do obszaru roboczego usługi Log Analytics, centrum zdarzeń platformy Azure lub konta usługi Azure Storage w celu archiwizacji. 

- [Jak włączyć ustawienia diagnostyczne dla dziennika aktywności platformy Azure](../azure-monitor/essentials/activity-log.md)

- [Jak włączyć ustawienia diagnostyczne (dzienniki generowane przez użytkownika) dla Azure Functions](functions-monitor-log-analytics.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Test [porównawczy zabezpieczeń](/azure/governance/policy/samples/azure-security-benchmark) platformy Azure jest domyślną inicjatywą zasad dla usługi Security Center i stanowi podstawę Security Center [rekomendacji firmy](/azure/security-center/security-center-recommendations). Definicje Azure Policy związane z tą kontrolką są włączane automatycznie przez Security Center. Alerty związane z tą kontrolą mogą [wymagać Azure Defender](/azure/security-center/azure-defender) planu dla powiązanych usług.

**Azure Policy wbudowane definicje — Microsoft.Web:**

[!INCLUDE [Resource Policy for Microsoft.Web 2.3](../../includes/policy/standards/asb/rp-controls/microsoft.web-2-3.md)]

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4. Zbieranie dzienników zabezpieczeń z systemów operacyjnych

**Wskazówki:** Nie dotyczy; Ta wytyczynia jest przeznaczona dla zasobów obliczeniowych IaaS.

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="25-configure-security-log-storage-retention"></a>2.5. Konfigurowanie przechowywania magazynu dzienników zabezpieczeń

**Wskazówki:** w Azure Monitor ustaw okres przechowywania dzienników dla obszarów roboczych usługi Log Analytics skojarzonych z aplikacjami funkcji zgodnie z przepisami dotyczącymi zgodności w organizacji.

- [Jak ustawić parametry przechowywania dzienników](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="26-monitor-and-review-logs"></a>2.6. Monitorowanie i przeglądanie dzienników

**Wskazówki:** włącz ustawienia diagnostyczne dziennika aktywności platformy Azure, a także ustawienia diagnostyczne dla aplikacji funkcji i wyślij dzienniki do obszaru roboczego usługi Log Analytics. Wykonywanie zapytań w u usługi Log Analytics w celu wyszukiwania terminów, identyfikowania trendów, analizowania wzorców i zapewnienia wielu innych szczegółowych informacji na podstawie zebranych danych.

Włącz Application Insights, aby aplikacje funkcji zbierały dane dzienników, wydajności i błędów. Możesz wyświetlić dane telemetryczne zebrane przez Application Insights w Azure Portal.

Jeśli masz wbudowane niestandardowe rejestrowanie zabezpieczeń/inspekcji w aplikacji funkcji, włącz ustawienie diagnostyczne "FunctionAppLogs" i wyślij dzienniki do obszaru roboczego usługi Log Analytics, centrum zdarzeń platformy Azure lub konta usługi Azure Storage w celu archiwizacji. 

Opcjonalnie możesz włączyć i dołączyć dane do Azure Sentinel lub rozwiązania do zarządzania informacjami i zdarzeniami systemowymi innej firmy.

- [Jak włączyć ustawienia diagnostyczne dla dziennika aktywności platformy Azure](../azure-monitor/essentials/activity-log.md)

- [Jak włączyć ustawienia diagnostyczne dla Azure Functions](functions-monitor-log-analytics.md)

- [Jak skonfigurować usługę Azure Azure Functions z usługą Azure Application Insights i wyświetlić dane telemetryczne](functions-monitoring.md)

- [Jak do dołączać Azure Sentinel](../sentinel/quickstart-onboard.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7. Włączanie alertów dla niesłychanych działań

**Wskazówki:** włącz ustawienia diagnostyczne dziennika aktywności platformy Azure, a także ustawienia diagnostyczne dla aplikacji funkcji i wyślij dzienniki do obszaru roboczego usługi Log Analytics. Wykonywanie zapytań w u usługi Log Analytics w celu wyszukiwania terminów, identyfikowania trendów, analizowania wzorców i zapewnienia wielu innych szczegółowych informacji na podstawie zebranych danych. Alerty można tworzyć na podstawie zapytań obszaru roboczego usługi Log Analytics.

Włącz Application Insights, aby aplikacje funkcji zbierały dane dzienników, wydajności i błędów. Możesz wyświetlać dane telemetryczne zbierane przez Application Insights i tworzyć alerty w Azure Portal.

Opcjonalnie można włączyć i dołączyć dane do Azure Sentinel lub rozwiązania do zarządzania informacjami i zdarzeniami systemu innych firm.

- [Jak włączyć ustawienia diagnostyczne dla dziennika aktywności platformy Azure](../azure-monitor/essentials/activity-log.md)

- [Jak włączyć ustawienia diagnostyczne dla Azure Functions](functions-monitor-log-analytics.md)

- [Jak włączyć Application Insights dla Azure Functions](./configure-monitoring.md#enable-application-insights-integration)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

## <a name="identity-and-access-control"></a>Tożsamość i kontrola dostępu

*Aby uzyskać więcej informacji, zobacz [Test porównawczy zabezpieczeń platformy Azure: tożsamość i Access Control.](../security/benchmarks/security-control-identity-access-control.md)*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1. Utrzymywanie spisu kont administracyjnych

**Wskazówki:** Azure Active Directory (Azure AD) ma wbudowane role, które muszą być jawnie przypisane i mogą być odpytywalne. Moduł Azure AD PowerShell umożliwia wykonywanie zapytań ad hoc w celu odnajdywania kont, które są członkami grup administracyjnych.

- [Jak uzyskać rolę katalogu w usłudze Azure AD przy użyciu programu PowerShell](/powershell/module/azuread/get-azureaddirectoryrole?preserve-view=true&view=azureadps-2.0)

- [Jak uzyskać członków roli katalogu w usłudze Azure AD przy użyciu programu PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember?preserve-view=true&view=azureadps-2.0)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Zmień domyślne hasła tam, gdzie ma to zastosowanie

**Wskazówki:** Dostęp do płaszczyzny sterowania Azure Functions jest kontrolowany za pośrednictwem Azure Active Directory (Azure AD). Usługa Azure AD nie ma koncepcji haseł domyślnych.

Dostęp do płaszczyzny danych można kontrolować za pomocą kilku sposobów, w tym kluczy autoryzacji, ograniczeń sieci i weryfikacji tożsamości usługi Azure AD. Klucze autoryzacji są używane przez klientów łączących się z Azure Functions punktami końcowymi HTTP i mogą być ponownie wygenerowane w dowolnym momencie. Te klucze są domyślnie generowane dla nowych punktów końcowych HTTP.

Dla aplikacji funkcji dostępnych jest wiele metod wdrażania, z których część może korzystać z zestawu wygenerowanych poświadczeń. Zapoznaj się z metodami wdrażania, które będą używane w aplikacji.

- [Zabezpieczanie punktu końcowego HTTP](./functions-bindings-http-webhook-trigger.md?tabs=csharp#secure-an-http-endpoint-in-production)

- [Jak uzyskać i ponownie wygenerować klucze autoryzacji](./functions-bindings-http-webhook-trigger.md?tabs=csharp#obtaining-keys)

- [Technologie wdrażania w Azure Functions](functions-deployment-technologies.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Użyj dedykowanych kont administracyjnych

**Wskazówki:** Tworzenie standardowych procedur operacyjnych dotyczących korzystania z dedykowanych kont administracyjnych. Użyj Azure Security Center zarządzania tożsamościami i dostępem, aby monitorować liczbę kont administracyjnych.

Ponadto, aby ułatwić śledzenie dedykowanych kont administracyjnych, możesz skorzystać z zaleceń Azure Security Center lub wbudowanych zasad platformy Azure, takich jak:

- Do Subskrypcji powinien być przypisany więcej niż jeden właściciel

- Przestarzałe konta z uprawnieniami właściciela powinny zostać usunięte z subskrypcji

- Konta zewnętrzne z uprawnieniami właściciela powinny zostać usunięte z subskrypcji

Dodatkowe informacje są dostępne pod linkami, do których się odwołują.

- [Jak używać usługi Azure Security Center do monitorowania tożsamości i dostępu ](../security-center/security-center-identity-access.md)

- [Jak używać Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4: Używanie Azure Active Directory jednokrotnego (SSO)

**Wskazówki:** wszędzie tam, gdzie to możliwe, używaj logowania jednokrotnego usługi Azure Active Directory (Azure AD) zamiast konfigurowania pojedynczych poświadczeń autonomicznych w celu uzyskania dostępu do danych do aplikacji funkcji. Użyj Azure Security Center tożsamości i zarządzania dostępem. Zaim implementuj logowanie pojedyncze dla aplikacji funkcji przy użyciu App Service uwierzytelniania/autoryzacji.

- [Opis uwierzytelniania i autoryzacji w Azure Functions](../app-service/overview-authentication-authorization.md#identity-providers)

- [Opis logowania jednokrotnego w usłudze Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Używaj uwierzytelniania wieloskładnikowego dla Azure Active Directory dostępu opartego na danych

**Wskazówki:** włączanie Azure Active Directory wieloskładnikowego (Azure AD) i postępuj zgodnie z Azure Security Center tożsamości i zarządzania dostępem.

- [Jak włączyć uwierzytelnianie wieloskładnikowe na platformie Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Jak monitorować tożsamość i dostęp w Azure Security Center](../security-center/security-center-identity-access.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6. Użyj dedykowanych maszyn (stacji roboczych z dostępem uprzywilejowanym) do wykonywania wszystkich zadań administracyjnych

**Wskazówki:** Używanie stacji roboczych z dostępem uprzywilejowanym (PAW) z uwierzytelnianiem wieloskładnikowym skonfigurowanym do logowania się i konfigurowania zasobów platformy Azure. 

- [Dowiedz się więcej o stacjach roboczych z dostępem uprzywilejowanym](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Jak włączyć uwierzytelnianie wieloskładnikowe na platformie Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: Rejestrowanie i alerty dotyczące podejrzanych działań z kont administracyjnych

**Wskazówki:** Generowanie dzienników i alertów przy użyciu usługi Azure Active Directory (Azure AD) Privileged Identity Management (PIM) w przypadku wystąpienia podejrzanej lub niebezpiecznej aktywności w środowisku.

Ponadto wykrywanie ryzyka w usłudze Azure AD umożliwia wyświetlanie alertów i raportów dotyczących ryzykownych zachowań użytkowników.

- [Jak wdrożyć Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [Opis wykrywania ryzyka w usłudze Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Zarządzanie zasobami platformy Azure tylko z zatwierdzonych lokalizacji

**Wskazówki:** Użyj dostępu warunkowego nazwanych lokalizacji, aby zezwolić na dostęp do usługi Azure Portal tylko z określonych grup logicznych zakresów adresów IP lub krajów/regionów.

- [Jak skonfigurować nazwane lokalizacje na platformie Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="39-use-azure-active-directory"></a>3.9. Użyj Azure Active Directory

**Wskazówki:** Użyj Azure Active Directory (Azure AD) jako centralnego systemu uwierzytelniania i autoryzacji dla aplikacji funkcji. Usługa Azure AD chroni dane przy użyciu silnego szyfrowania danych magazynowych i podczas przesyłania. Usługa Azure AD dodaje również iniekty, skróty i bezpiecznie przechowuje poświadczenia użytkownika.

- [Jak skonfigurować aplikację funkcji do korzystania z logowania usługi Azure AD](../app-service/configure-authentication-provider-aad.md)

- [Jak utworzyć i skonfigurować wystąpienie usługi Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Regularne przeglądanie i uzgadnianie dostępu użytkowników

**Wskazówki:** Azure Active Directory (Azure AD) zawiera dzienniki, które ułatwiają odnajdywanie nieaktywnego konta. Ponadto usługa Azure Identity Access Reviews umożliwia efektywne zarządzanie członkostwem w grupach, dostępem do aplikacji dla przedsiębiorstw i przypisań ról. Dostęp użytkowników można regularnie przeglądać, aby upewnić się, że tylko odpowiedni użytkownicy mają dalszy dostęp.

- [Informacje o raportowaniu w usłudze Azure AD](../active-directory/reports-monitoring/index.yml)

- [Jak używać przeglądów dostępu tożsamości platformy Azure](../active-directory/governance/access-reviews-overview.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Monitorowanie prób uzyskania dostępu do dezaktywowanych poświadczeń

**Wskazówki:** Użyj Azure Active Directory (Azure AD) jako centralnego systemu uwierzytelniania i autoryzacji dla aplikacji funkcji. Usługa Azure AD chroni dane przy użyciu silnego szyfrowania danych magazynowany i podczas przesyłania. Usługa Azure AD dodaje również instyty, skróty i bezpiecznie przechowuje poświadczenia użytkownika.

Masz dostęp do źródeł dzienników zdarzeń inspekcji i inspekcji usługi Azure AD, które umożliwiają integrację z rozwiązaniami Azure Sentinel lub SIEM innej firmy.

Ten proces można usprawnić, tworząc ustawienia diagnostyczne dla kont użytkowników usługi Azure AD oraz wysyłając dzienniki inspekcji i dzienniki logowania do obszaru roboczego usługi Log Analytics. Żądane alerty dzienników można skonfigurować w ramach usługi Log Analytics.

- [Jak skonfigurować aplikację funkcji do korzystania z logowania usługi Azure AD](../app-service/configure-authentication-provider-aad.md)

- [Jak zintegrować dzienniki aktywności platformy Azure z usługą Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Jak Azure Sentinel](../sentinel/quickstart-onboard.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12: Odchylenie zachowania alertu podczas logowania na koncie

**Wskazówki:** Użyj Azure Active Directory (Azure AD) jako centralnego systemu uwierzytelniania i autoryzacji dla aplikacji funkcji. W przypadku odchylenia zachowania logowania do konta na płaszczyźnie sterowania (Azure Portal) użyj funkcji Azure AD Identity Protection i wykrywania ryzyka, aby skonfigurować automatyczne odpowiedzi na wykryte podejrzane akcje związane z tożsamościami użytkowników. Możesz również pozysać dane do Azure Sentinel celu dalszego badania.

- [Jak wyświetlić ryzykowne logowania w usłudze Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

- [Jak skonfigurować i włączyć zasady ryzyka usługi Identity Protection](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Jak do dołączać Azure Sentinel](../sentinel/quickstart-onboard.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

## <a name="data-protection"></a>Ochrona danych

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: ochrona danych](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1. Utrzymywanie spisu informacji poufnych

**Wskazówki:** używanie tagów do śledzenia zasobów platformy Azure, które przechowują lub przetwarzają poufne informacje.

- [Jak tworzyć tagi i ich używać](../azure-resource-manager/management/tag-resources.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Odizoluj systemy przechowujące lub przetwarzające informacje poufne

**Wskazówki:** Zaim implementuj oddzielne subskrypcje i/lub grupy zarządzania na rzecz testowania, testowania i tworzenia aplikacji. Aplikacje funkcji powinny być oddzielone siecią wirtualną (VNet)/podsiecią i odpowiednio oznakowane.

Do przeprowadzenia izolacji sieci można również użyć prywatnych punktów końcowych. Prywatny punkt końcowy platformy Azure to interfejs sieciowy, który łączy Cię prywatnie i bezpiecznie z usługą (na przykład punktem końcowym HTTPs aplikacji funkcji) obsługiwanym przez Azure Private Link. Prywatny punkt końcowy używa prywatnego adresu IP z Twojej sieci wirtualnej, skutecznie przenosząc usługę do sieci wirtualnej. Prywatne punkty końcowe mogą być dostępne dla funkcji hostowanych w planach Premium App Service premium.

- [Jak utworzyć dodatkowe subskrypcje platformy Azure](../cost-management-billing/manage/create-subscription.md)

- [Jak utworzyć Grupy zarządzania](../governance/management-groups/create-management-group-portal.md)

- [Jak tworzyć tagi i ich używać](../azure-resource-manager/management/tag-resources.md)

- [Opcje sieciowe usługi Azure Functions](functions-networking-options.md)

- [Azure Functions Premium Plan](functions-premium-plan.md)

- [Opis prywatnego punktu końcowego](../private-link/private-endpoint-overview.md)

- [Używanie prywatnych punktów końcowych dla Azure Functions](../app-service/networking/private-endpoint.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Szyfruj wszystkie poufne informacje podczas przesyłania

**Wskazówki:** w Azure Portal funkcji w obszarze "Funkcje platformy: sieć: SSL" włącz ustawienie "Tylko protokół HTTPs" i ustaw minimalną wersję protokołu TLS na 1.2.

- [Wymagaj protokołu HTTPS w aplikacjach funkcji](./security-concepts.md#require-https)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Test [porównawczy zabezpieczeń](/azure/governance/policy/samples/azure-security-benchmark) platformy Azure jest domyślną inicjatywą zasad dla usługi Security Center i stanowi podstawę Security Center [rekomendacji firmy](/azure/security-center/security-center-recommendations). Definicje Azure Policy związane z tą kontrolką są włączane automatycznie przez Security Center. Alerty związane z tą kontrolą mogą [wymagać Azure Defender](/azure/security-center/azure-defender) planu dla powiązanych usług.

**Azure Policy wbudowane definicje — Microsoft.Web:**

[!INCLUDE [Resource Policy for Microsoft.Web 4.4](../../includes/policy/standards/asb/rp-controls/microsoft.web-4-4.md)]

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5. Identyfikowanie poufnych danych za pomocą aktywnego narzędzia odnajdywania

**Wskazówki:** obecnie niedostępne; Funkcje identyfikacji, klasyfikacji i zapobiegania utracie danych nie są obecnie dostępne dla Azure Functions. Oznaczanie aplikacji funkcji tagami, które mogą przetwarzać poufne informacje jako takie, i implementowanie rozwiązania innej firmy, jeśli jest to wymagane w celu zapewnienia zgodności.

W przypadku podstawowej platformy zarządzanej przez firmę Microsoft firma Microsoft traktuje całą zawartość klientów jako poufne i bardzo długo chroni przed utratą i utratą danych klientów. Aby zapewnić bezpieczeństwo danych klientów na platformie Azure, firma Microsoft wdrożyła i utrzymuje pakiet niezawodnych funkcji i kontroli ochrony danych.

- [Informacje na temat ochrony danych klientów na platformie Azure](../security/fundamentals/protection-customer-data.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4.6: Kontrola dostępu do zasobów przy użyciu kontroli dostępu opartej na rolach

**Wskazówki:** Użyj kontroli dostępu opartej na rolach (RBAC) platformy Azure, aby zarządzać dostępem do płaszczyzny sterowania aplikacji funkcji (Azure Portal).

- [Jak skonfigurować RBAC platformy Azure](../role-based-access-control/role-assignments-portal.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7. Wymuszanie kontroli dostępu przy użyciu ochrony przed utratą danych opartej na hoście

**Wskazówki:** Nie dotyczy; To zalecenie jest przeznaczone dla zasobów obliczeniowych IaaS.

Firma Microsoft zarządza podstawową infrastrukturą Azure Functions i wdrożyła ścisłe mechanizmy kontroli, aby zapobiec utracie lub narażeniu danych klientów.

- [Informacje na temat ochrony danych klientów na platformie Azure](../security/fundamentals/protection-customer-data.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Szyfrowanie poufnych informacji w spoczynku

**Wskazówki:** Podczas tworzenia aplikacji funkcji należy utworzyć lub połączyć konto usługi Azure Storage ogólnego przeznaczenia, które obsługuje usługi Blob Storage, Queue Storage i Table Storage. Jest to spowodowane tym, że usługa Functions korzysta z usługi Azure Storage w przypadku operacji, takich jak zarządzanie wyzwalaczami i rejestrowanie wykonań funkcji. Usługa Azure Storage szyfruje wszystkie dane na koncie magazynu w spoczynku. Domyślnie dane są szyfrowane przy użyciu kluczy zarządzanych przez firmę Microsoft. Aby uzyskać dodatkową kontrolę nad kluczami szyfrowania, można dostarczyć klucze zarządzane przez klienta na potrzeby szyfrowania danych obiektów blob i plików. Te klucze muszą być obecne w Azure Key Vault, aby aplikacja funkcji mogła uzyskać dostęp do konta magazynu.

- [Opis zagadnienia dotyczące magazynu dla Azure Functions](storage-considerations.md)

- [Understand Azure storage encryption for data at rest (Informacje o szyfrowaniu magazynu platformy Azure dla danych magazynowych)](../storage/common/storage-service-encryption.md)

**Odpowiedzialność**: Współużytkowane

**Azure Security Center monitorowania:** Brak

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9. Rejestrowanie i alerty dotyczące zmian w krytycznych zasobach platformy Azure

**Wskazówki:** użyj Azure Monitor dziennika aktywności platformy Azure, aby tworzyć alerty dotyczące sytuacji, w których w aplikacjach funkcji produkcyjnych zostaną wprowadzone zmiany, a także innych krytycznych lub powiązanych zasobów.

- [Jak tworzyć alerty dla zdarzeń dziennika aktywności platformy Azure](../azure-monitor/alerts/alerts-activity-log.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

## <a name="vulnerability-management"></a>Zarządzanie lukami w zabezpieczeniach

*Aby uzyskać więcej informacji, zobacz [Test porównawczy zabezpieczeń platformy Azure: zarządzanie lukami w zabezpieczeniach.](../security/benchmarks/security-control-vulnerability-management.md)*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1. Uruchamianie zautomatyzowanych narzędzi do skanowania luk w zabezpieczeniach

**Wskazówki:** Zastosuj praktykę DevSecOps, aby zapewnić, że aplikacje funkcji są bezpieczne i pozostaną tak bezpieczne, jak to możliwe przez cały okres ich cyklu życia. Metodyka DevSecOps dołącza zespół ds. zabezpieczeń twojej organizacji i jej możliwości do praktyk metodyki DevOps, co czyni bezpieczeństwo odpowiedzialnością wszystkich członków zespołu.

Ponadto postępuj zgodnie z zaleceniami Azure Security Center, aby ułatwić zabezpieczanie aplikacji funkcji.

- [Jak dodać ciągłą weryfikację zabezpieczeń do potoku ciągłej integracji/ciągłego integracji](/azure/devops/migrate/security-validation-cicd-pipeline?preserve-view=true&view=azure-devops)

- [Jak zaimplementować Azure Security Center oceny luk w zabezpieczeniach](../security-center/deploy-vulnerability-assessment-vm.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5. Określanie priorytetów korygowania wykrytych luk w zabezpieczeniach za pomocą procesu oceny ryzyka

**Wskazówki:** Firma Microsoft wykonuje zarządzanie lukami w zabezpieczeniach w systemach bazowych, które obsługują usługę Azure Functions, jednak możesz użyć ważności zaleceń w ramach programu Azure Security Center, a także bezpiecznego wyniku do mierzenia ryzyka w twoim środowisku. Ocena bezpieczeństwa jest oparta na Security Center rekomendacji, które zostały skorygowane. Aby ustalić priorytety zaleceń, które należy najpierw rozwiązać, należy wziąć pod uwagę ważność każdego z nich.

- [Przewodnik po zaleceniach dotyczących zabezpieczeń](../security-center/recommendations-reference.md)

**Odpowiedzialność**: Współużytkowane

**Azure Security Center monitorowania:** Brak

## <a name="inventory-and-asset-management"></a>Zarządzanie magazynem i zasobami

*Aby uzyskać więcej informacji, zobacz [Test porównawczy zabezpieczeń platformy Azure: spis i zarządzanie zasobami.](../security/benchmarks/security-control-inventory-asset-management.md)*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1. Korzystanie z rozwiązania do automatycznego odnajdywania zasobów

**Wskazówki:** użyj Azure Resource Graph do wykonywania zapytań i odnajdywania wszystkich zasobów (takich jak zasoby obliczeniowe, magazynowe, sieciowe, porty i protokoły itp.) w ramach subskrypcji. Upewnij się, że w dzierżawie są odpowiednie uprawnienia (odczyt) i wyliczenie wszystkich subskrypcji platformy Azure, a także zasobów w ramach twoich subskrypcji.

Mimo że klasyczne zasoby platformy Azure można Resource Graph za pośrednictwem usługi , zdecydowanie zaleca się tworzenie i używanie Azure Resource Manager zasobów w przyszłości.

- [Jak tworzyć zapytania za pomocą Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

- [Jak wyświetlić subskrypcje platformy Azure](/powershell/module/az.accounts/get-azsubscription?preserve-view=true&view=azps-4.8.0)

- [Opis kontroli RBAC na platformie Azure](../role-based-access-control/overview.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="62-maintain-asset-metadata"></a>6.2. Obsługa metadanych zasobów

**Wskazówki:** Stosowanie tagów do zasobów platformy Azure, które dają metadane w celu logicznego zorganizowania ich w taksonomię.

- [Jak tworzyć tagi i ich używać](../azure-resource-manager/management/tag-resources.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="63-delete-unauthorized-azure-resources"></a>6.3. Usuwanie nieautoryzowanych zasobów platformy Azure

**Wskazówki:** W celu organizowania i śledzenia zasobów platformy Azure używaj tagowania, grup zarządzania i oddzielnych subskrypcji, jeśli jest to konieczne. Regularnie uzgadniaj spis i upewnij się, że nieautoryzowane zasoby zostaną usunięte z subskrypcji w terminowy sposób.

Ponadto użyj zasad platformy Azure, aby wprowadzić ograniczenia dotyczące typu zasobów, które można utworzyć w subskrypcjach klientów, korzystając z następujących wbudowanych definicji zasad:

- Niedozwolone typy zasobów

- Dozwolone typy zasobów

Dodatkowe informacje są dostępne pod linkami, do których się odwołują.

- [Jak utworzyć dodatkowe subskrypcje platformy Azure](../cost-management-billing/manage/create-subscription.md)

- [Jak utworzyć Grupy zarządzania](../governance/management-groups/create-management-group-portal.md)

- [Jak tworzyć tagi i ich używać](../azure-resource-manager/management/tag-resources.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4: Definiowanie i utrzymywanie spisu zatwierdzonych zasobów platformy Azure

**Wskazówki:** Zdefiniuj zatwierdzone zasoby platformy Azure i zatwierdzone oprogramowanie dla zasobów obliczeniowych.

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Monitorowanie niezatwierdzonych zasobów platformy Azure

**Wskazówki:** użyj Azure Policy, aby wprowadzić ograniczenia dotyczące typu zasobów, które mogą być tworzone w twoich subskrypcjach. 

Użyj Azure Resource Graph, aby odpytywać/odnajdywać zasoby w ramach swoich subskrypcji.  Upewnij się, że wszystkie zasoby platformy Azure obecne w środowisku zostały zatwierdzone. 

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md) 

- [Jak tworzyć zapytania za pomocą usługi Azure Graph](../governance/resource-graph/first-query-portal.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="69-use-only-approved-azure-services"></a>6.9. Używaj tylko zatwierdzonych usług platformy Azure

**Wskazówki:** użyj Azure Policy, aby wprowadzić ograniczenia dotyczące typu zasobów, które mogą być tworzone w subskrypcjach klientów, korzystając z następujących wbudowanych definicji zasad:

- Niedozwolone typy zasobów

- Dozwolone typy zasobów

Dodatkowe informacje są dostępne pod linkami, do których się odwołujesz.

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

- [Jak odmówić określonego typu zasobu za pomocą Azure Policy](../governance/policy/samples/built-in-policies.md#general)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Ograniczanie możliwości interakcji użytkowników z Azure Resource Manager

**Wskazówki:** Skonfiguruj dostęp warunkowy platformy Azure, aby ograniczyć użytkownikom możliwość interakcji z usługą Azure Resource Manager, konfigurując opcję "Blokuj dostęp" dla aplikacji "Microsoft Azure Management".

- [Jak skonfigurować dostęp warunkowy, aby zablokować dostęp do Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

## <a name="secure-configuration"></a>Bezpieczna konfiguracja

*Aby uzyskać więcej informacji, zobacz [Test porównawczy zabezpieczeń platformy Azure: bezpieczna konfiguracja.](../security/benchmarks/security-control-secure-configuration.md)*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1. Ustanawianie bezpiecznych konfiguracji dla wszystkich zasobów platformy Azure

**Wskazówki:** Definiowanie i implementowanie standardowych konfiguracji zabezpieczeń dla aplikacji funkcji za pomocą Azure Policy. Użyj Azure Policy aliasów w przestrzeni nazw "Microsoft.Web", aby utworzyć niestandardowe zasady inspekcji lub wymuszania konfiguracji aplikacji funkcji. Możesz również korzystać z wbudowanych definicji zasad, takich jak:

- Tożsamość zarządzana powinna być używana w aplikacji funkcji

- Zdalne debugowanie powinno być wyłączone dla aplikacji funkcji

- aplikacja funkcji powinna być dostępna tylko za pośrednictwem protokołu HTTPS

Dodatkowe informacje są dostępne pod linkami, do których się odwołujesz.

- [Jak wyświetlić dostępne Azure Policy aliasy](/powershell/module/az.resources/get-azpolicyalias?preserve-view=true&view=azps-4.8.0)

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Obsługa bezpiecznych konfiguracji zasobów platformy Azure

**Wskazówki:** użyj zasad platformy Azure [odmowa] i [wdaj, jeśli nie istnieje] w celu wymuszenia bezpiecznych ustawień w zasobach platformy Azure.

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

- [Opis Azure Policy danych](../governance/policy/concepts/effects.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Bezpieczne przechowywanie konfiguracji zasobów platformy Azure

**Wskazówki:** bezpieczne przechowywanie szablonów usługi ARM i niestandardowych definicji zasad platformy Azure w kontroli źródła i zarządzanie nimi.

- [Co to jest infrastruktura jako kod](/azure/devops/learn/what-is-infrastructure-as-code)

- [Projektowanie zasad jako przepływów pracy kodu](../governance/policy/concepts/policy-as-code.md)

- [Jak przechowywać kod w Azure DevOps](/azure/devops/repos/git/gitworkflow?preserve-view=true&view=azure-devops)

- [Azure Repos dokumentacji](/azure/devops/repos/?preserve-view=true&view=azure-devops)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7. Wdrażanie narzędzi do zarządzania konfiguracją dla zasobów platformy Azure

**Wskazówki:** Użyj wbudowanych definicji Azure Policy, a także aliasów usługi Azure Policy w przestrzeni nazw "Microsoft.Web", aby utworzyć zasady niestandardowe do alertów, inspekcji i wymuszania konfiguracji systemu. Ponadto opracuj proces i potok do zarządzania wyjątkami zasad.

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9. Implementowanie zautomatyzowanego monitorowania konfiguracji dla zasobów platformy Azure

**Wskazówki:** Użyj wbudowanych definicji Azure Policy, a także aliasów usługi Azure Policy w przestrzeni nazw "Microsoft.Web", aby utworzyć zasady niestandardowe do alertów, inspekcji i wymuszania konfiguracji systemu. Użyj zasad platformy Azure [audit], [deny] i [deploy if not exist], aby automatycznie wymusić konfiguracje zasobów platformy Azure.

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="711-manage-azure-secrets-securely"></a>7.11: Bezpieczne zarządzanie wpisami tajnymi platformy Azure

**Wskazówki:** Używaj tożsamości zarządzanych w połączeniu z Azure Key Vault, aby uprościć i zabezpieczyć zarządzanie kluczami tajnymi dla aplikacji w chmurze. Tożsamości zarządzane umożliwiają aplikacji funkcji uwierzytelnianie w dowolnej usłudze obsługującej uwierzytelnianie usługi Azure Active Directory (Azure AD), w tym Key Vault, bez żadnych poświadczeń w kodzie.

- [Jak utworzyć Key Vault](../key-vault/secrets/quick-create-portal.md)

- [Jak używać tożsamości zarządzanych na App Service i Azure Functions](../app-service/overview-managed-identity.md)

- [Jak uwierzytelnić się w Key Vault](../key-vault/general/authentication.md)

- [Jak przypisać zasady Key Vault dostępu](../key-vault/general/assign-access-policy-portal.md)

- [Użyj Key Vault odwołań dla App Service i Azure Functions](../app-service/app-service-key-vault-references.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Bezpieczne i automatyczne zarządzanie tożsamościami

**Wskazówki:** użyj tożsamości zarządzanych, aby zapewnić aplikacji funkcji automatycznie zarządzaną tożsamość w usłudze Azure Active Directory (Azure AD). Tożsamości zarządzane umożliwiają uwierzytelnianie w dowolnej usłudze, która obsługuje uwierzytelnianie usługi Azure AD, w tym Key Vault, bez żadnych poświadczeń w kodzie.

- [Jak używać tożsamości zarządzanych na App Service i Azure Functions](../app-service/overview-managed-identity.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Test [porównawczy zabezpieczeń](/azure/governance/policy/samples/azure-security-benchmark) platformy Azure jest domyślną inicjatywą zasad dla usługi Security Center i stanowi podstawę Security Center [rekomendacji firmy](/azure/security-center/security-center-recommendations). Definicje Azure Policy związane z tą kontrolką są włączane automatycznie przez Security Center. Alerty związane z tą kontrolą mogą [wymagać Azure Defender](/azure/security-center/azure-defender) planu dla powiązanych usług.

**Azure Policy wbudowane definicje — Microsoft.Web:**

[!INCLUDE [Resource Policy for Microsoft.Web 7.12](../../includes/policy/standards/asb/rp-controls/microsoft.web-7-12.md)]

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Eliminowanie niezamierzonego narażenia na poświadczenia

**Wskazówki:** implementowanie skanera poświadczeń w celu identyfikowania poświadczeń w kodzie. Skaner poświadczeń ułatwia również przenoszenie odnalezionych poświadczeń do bezpieczniejszych lokalizacji, takich jak usługa Azure Key Vault. 

- [Jak skonfigurować skaner poświadczeń](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

## <a name="data-recovery"></a>Odzyskiwanie danych

*Aby uzyskać więcej informacji, zobacz [Test porównawczy zabezpieczeń platformy Azure: odzyskiwanie danych.](../security/benchmarks/security-control-data-recovery.md)*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Zapewnianie regularnych automatycznych kopii zapasowej

**Wskazówki:** użyj funkcji Tworzenia kopii zapasowych i przywracania, aby zaplanować regularne tworzenie kopii zapasowych aplikacji. Aplikacje funkcji działające w planie Premium mają takie same możliwości hostingu, jak aplikacje internetowe w Azure App Service, co obejmuje funkcję tworzenia kopii zapasowych i przywracania.

Korzystaj również z rozwiązania do kontroli źródła, takiego jak Azure Repos i Azure DevOps, aby bezpiecznie przechowywać kod i zarządzać jego kodem. Usługa Azure DevOps Services wykorzystuje wiele funkcji usługi Azure Storage w celu zapewnienia dostępności danych w przypadku awarii sprzętowej, przerw w działaniu usługi lub awarii regionu. Ponadto zespół usługi Azure DevOps postępuje zgodnie z procedurami w celu ochrony danych przed przypadkowym lub złośliwym usunięciem.

- [Tworzenie kopii zapasowej aplikacji na platformie Azure](../app-service/manage-backup.md)

- [Informacje o dostępności danych w Azure DevOps](/azure/devops/organizations/security/data-protection?preserve-view=true&view=azure-devops#data-availability)

- [Jak przechowywać kod w Azure DevOps](/azure/devops/repos/git/gitworkflow?preserve-view=true&view=azure-devops)

- [Azure Repos dokumentacji](/azure/devops/repos/?preserve-view=true&view=azure-devops)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2. Wykonywanie pełnych kopii zapasowych systemu i tworzenie kopii zapasowych dowolnych kluczy zarządzanych przez klienta

**Wskazówki:** użyj funkcji Tworzenia kopii zapasowych i przywracania, aby zaplanować regularne tworzenie kopii zapasowych aplikacji. Aplikacje funkcji działające w planie Premium mają takie same możliwości hostingu, jak aplikacje internetowe w Azure App Service, co obejmuje funkcję tworzenia kopii zapasowych i przywracania. Tworzenie kopii zapasowych kluczy zarządzanych przez klienta w Azure Key Vault.

Korzystaj również z rozwiązania do kontroli źródła, takiego jak Azure Repos i Azure DevOps, aby bezpiecznie przechowywać kod i zarządzać jego kodem. Usługa Azure DevOps Services wykorzystuje wiele funkcji usługi Azure Storage w celu zapewnienia dostępności danych w przypadku awarii sprzętowej, przerw w działaniu usługi lub awarii regionu. Ponadto zespół usługi Azure DevOps postępuje zgodnie z procedurami w celu ochrony danych przed przypadkowym lub złośliwym usunięciem.

- [Tworzenie kopii zapasowej aplikacji na platformie Azure](../app-service/manage-backup.md)

- [Jak utworzyć kopię zapasową kluczy magazynu kluczy na platformie Azure](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

- [Informacje o dostępności danych w Azure DevOps](/azure/devops/organizations/security/data-protection?preserve-view=true&view=azure-devops#data-availability)

- [Jak przechowywać kod w Azure DevOps](/azure/devops/repos/git/gitworkflow?preserve-view=true&view=azure-devops)

- [Azure Repos dokumentacji](/azure/devops/repos/?preserve-view=true&view=azure-devops)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Zweryfikuj wszystkie kopie zapasowe, w tym klucze zarządzane przez klienta

**Wskazówki:** Zapewnianie możliwości okresowego wykonywania przywracania z funkcji tworzenia kopii zapasowych i przywracania. W przypadku tworzenia kopii zapasowej kodu w innej lokalizacji w trybie offline należy okresowo zapewniać możliwość wykonania pełnych przywracania. Przetestuj przywracanie kopii zapasowej kluczy zarządzanych przez klienta.

- [Przywracanie aplikacji na platformie Azure z kopii zapasowej](../app-service/web-sites-restore.md)

- [Przywracanie aplikacji na platformie Azure z migawki](../app-service/app-service-web-restore-snapshots.md)

- [Jak przywrócić klucze magazynu kluczy na platformie Azure](/powershell/module/az.keyvault/restore-azkeyvaultkey?preserve-view=true&view=azps-4.8.0)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4. Zapewnianie ochrony kopii zapasowych i kluczy zarządzanych przez klienta

**Wskazówki:** Kopie zapasowe z funkcji tworzenia kopii zapasowych i przywracania używają konta usługi Azure Storage w ramach subskrypcji. Usługa Azure Storage szyfruje wszystkie dane na koncie magazynu w spoczynku. Domyślnie dane są szyfrowane przy użyciu kluczy zarządzanych przez firmę Microsoft. Aby uzyskać dodatkową kontrolę nad kluczami szyfrowania, możesz podać klucze zarządzane przez klienta na potrzeby szyfrowania danych magazynu.

Jeśli używasz kluczy zarządzanych przez klienta, upewnij się, Soft-Delete w Key Vault jest włączona, aby chronić klucze przed przypadkowym lub złośliwym usunięciem.

- [Szyfrowanie danych magazynowanych w usłudze Azure Storage](../storage/common/storage-service-encryption.md)

- [Jak włączyć Soft-Delete w Key Vault](../storage/blobs/soft-delete-blob-overview.md)

**Odpowiedzialność**: Współużytkowane

**Azure Security Center monitorowania:** Brak

## <a name="incident-response"></a>Reagowanie na zdarzenia

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: reagowanie na zdarzenia](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1. Tworzenie przewodnika po reagowaniu na zdarzenia

**Wskazówka**: Utwórz przewodnik odpowiedzi na zdarzenia dla swojej organizacji. Upewnij się, że istnieją zarejestrowane plany reakcji na zdarzenia, które definiują wszystkie role pracowników, a także etapy obsługi zdarzeń/zarządzania od wykrywania do oceny po zdarzeniu.

- [Jak skonfigurować automatyzacje przepływu pracy w Azure Security Center](../security-center/security-center-planning-and-operations-guide.md)

- [Wskazówki dotyczące tworzenia własnego procesu reagowania na zdarzenia zabezpieczeń](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Centrum zabezpieczeń firmy Microsoft Anatomy of an Incident (Anatomia zdarzenia)](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Przewodnik obsługi zdarzeń zabezpieczeń komputera NIST](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2. Tworzenie procedury oceniania i ustalania priorytetów zdarzeń

**Wskazówki:** Security Center przypisać ważność do każdego alertu, aby ułatwić priorytetyzowanie alertów, które należy zbadać w pierwszej kolejności. Ważność jest oparta na tym, jak pewna jest Security Center w znalezieniu lub analizie użytej do wystawienia alertu, a także na poziomie pewności, że za działaniem, które doprowadziło do tego alertu, znajduje się złośliwe intencje.

Ponadto wyraźnie oznaczaj subskrypcje (na przykład production, non-prod) i utwórz system nazewnictwa, aby jasno identyfikować i kategoryzować zasoby platformy Azure.

**Odpowiedzialność**: Współużytkowane

**Azure Security Center monitorowania:** Brak

### <a name="103-test-security-response-procedures"></a>10.3: Testowanie procedur reagowania na zabezpieczenia

**Wskazówki:** Przećwiczyj ćwiczenia, aby regularnie testować możliwości reagowania na zdarzenia w systemach. Zidentyfikuj słabe punkty i przerwy oraz popraw plan zgodnie z wymaganiami.

- [Zapoznaj się z publikacją NIST: Guide to Test, Training, and Exercise Programs for IT Plans and Capabilities (Przewodnik po programach testowych, szkoleniowych i szkoleniowych dla planów i możliwości IT)](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4. Podaj szczegóły kontaktowe zdarzenia zabezpieczeń i skonfiguruj powiadomienia o alertach dla zdarzeń zabezpieczeń

**Wskazówki:** informacje kontaktowe dotyczące zdarzenia zabezpieczeń będą używane przez firmę Microsoft do kontaktowania się z Tobą, jeśli użytkownik usługi Centrum zabezpieczeń firmy Microsoft (MSRC) odkryje, że dostęp do danych klienta został uzyskany przez nieupoważnioną lub niezgodną z prawem stronę.  Przejrzyj zdarzenia po fakcie, aby upewnić się, że problemy zostały rozwiązane.

- [Jak ustawić kontakt Azure Security Center zabezpieczeń](../security-center/security-center-provide-security-contact-details.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5. Dołączanie alertów zabezpieczeń do systemu reagowania na zdarzenia

**Wskazówki:** eksportowanie alertów Azure Security Center i zaleceń przy użyciu funkcji eksportu ciągłego. Eksport ciągły umożliwia ręczne lub ciągłe eksportowanie alertów i zaleceń. Możesz użyć łącznika danych Azure Security Center, aby przesyłać strumieniowo alerty do Azure Sentinel.

- [Jak skonfigurować eksport ciągły](../security-center/continuous-export.md)

- [Jak przesłać strumieniowo alerty do usługi Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatyzowanie odpowiedzi na alerty zabezpieczeń

**Wskazówki:** Użyj funkcji automatyzacji przepływu pracy w Azure Security Center, aby automatycznie wyzwalać odpowiedzi na alerty zabezpieczeń i zalecenia za pomocą Logic Apps.

- [Jak skonfigurować automatyzację przepływu pracy i Logic Apps](../security-center/workflow-automation.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

## <a name="penetration-tests-and-red-team-exercises"></a>Testy penetracyjne i ćwiczenia typu „red team”

*Aby uzyskać więcej informacji, zobacz [test porównawczy zabezpieczeń platformy Azure: testy penetracyjne i ćwiczenia zespołu czerwonego.](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Przeprowadzaj regularne testy penetrowania zasobów platformy Azure i zapewniaj korygowanie wszystkich krytycznych ustaleń zabezpieczeń

**Wskazówki:** postępuj zgodnie z regułami zaangażowania firmy Microsoft, aby upewnić się, że testy penetracyjne nie naruszały zasad firmy Microsoft. Użyj strategii firmy Microsoft i wykonania czerwonego zespołu oraz testów penetracyjnych na żywo w chmurze, które są zarządzane przez firmę Microsoft w zakresie infrastruktury, usług i aplikacji w chmurze.

- [Reguły testów penetracyjnych zaangażowania](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Testy typu „red team” w chmurze firmy Microsoft](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Odpowiedzialność**: Współużytkowane

**Azure Security Center monitorowania:** Brak

## <a name="next-steps"></a>Następne kroki

- Zobacz [Omówienie testu porównawczego zabezpieczeń platformy Azure w wersji 2](../security/benchmarks/overview.md)
- Dowiedz się więcej o [punktach odniesienia zabezpieczeń platformy Azure](../security/benchmarks/security-baselines-overview.md)
