---
title: Punkt odniesienia zabezpieczeń platformy Azure dla API Management
description: Punkt API Management zabezpieczeń zawiera wskazówki proceduralne i zasoby dotyczące wdrażania zaleceń dotyczących zabezpieczeń określonych w te testach porównawczych zabezpieczeń platformy Azure.
author: msmbaldwin
ms.service: api-management
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark, devx-track-azurepowershell
ms.openlocfilehash: 488fa4738918849759fbefa86ef1e9730b3b0ed0
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107813492"
---
# <a name="azure-security-baseline-for-api-management"></a>Punkt odniesienia zabezpieczeń platformy Azure dla API Management

Ten punkt odniesienia zabezpieczeń stosuje wskazówki z testu porównawczego zabezpieczeń platformy [Azure w wersji 1.0](../security/benchmarks/overview-v1.md) do API Management. Test porównawczy zabezpieczeń platformy Azure zawiera zalecenia dotyczące sposobu zabezpieczania rozwiązań w chmurze na platformie Azure.
Zawartość jest pogrupowana według **kontrolek zabezpieczeń zdefiniowanych** przez test porównawczy zabezpieczeń platformy Azure i powiązanych wskazówek dotyczących API Management. **Kontrolki** nie dotyczy API Management zostały wykluczone.

 
Aby zobaczyć, API Management mapowanie na test porównawczy zabezpieczeń platformy Azure, zobacz pełny [API Management mapowania punktów odniesienia zabezpieczeń](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Bezpieczeństwo sieci

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: bezpieczeństwo sieci](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1. Ochrona zasobów platformy Azure w sieciach wirtualnych

**Wskazówki:** usługę Azure API Management można wdrożyć w sieci Virtual Network Azure, aby mieć dostęp do usług zaplecza w sieci. Portal deweloperów i brama API Management można skonfigurować tak, aby były dostępne z Internetu (zewnętrzne) lub tylko w sieci wirtualnej (wewnętrznej).

- Zewnętrzne: brama API Management portal dla deweloperów są dostępne z publicznego Internetu za pośrednictwem zewnętrznego usługi równoważenia obciążenia. Brama może uzyskać dostęp do zasobów w sieci wirtualnej.

- Wewnętrzne: brama API Management portal dla deweloperów są dostępne tylko z poziomu sieci wirtualnej za pośrednictwem wewnętrznego równoważenia obciążenia. Brama może uzyskać dostęp do zasobów w sieci wirtualnej.

Ruch przychodzący i wychodzący do podsieci, w której wdrożono API Management, można kontrolować przy użyciu sieciowej grupy zabezpieczeń.

- [Używanie usługi Azure API Management z sieciami wirtualnymi](api-management-using-with-vnet.md)

- [Korzystanie z usługi Azure API Management z wewnętrzną siecią wirtualną](api-management-using-with-internal-vnet.md)

- [Integrowanie usługi API Management w wewnętrznej sieci wirtualnej z usługą Application Gateway](api-management-howto-integrate-internal-vnet-appgateway.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2: Monitorowanie i rejestrowanie konfiguracji i ruchu sieci wirtualnych, podsieci i interfejsów sieciowych

**Wskazówki:** Ruch przychodzący i wychodzący do podsieci, w której wdrożono API Management, można kontrolować przy użyciu sieciowych grup zabezpieczeń. Wd wdrożenie sieciowej sieciowej API Management podsieci sieciowej i włączenie dzienników przepływu sieciowej API Management i wysyłanie dzienników na konto usługi Azure Storage w celu inspekcji ruchu. Możesz również wysyłać dzienniki przepływu sieciowych grup zabezpieczeń do obszaru roboczego usługi Log Analytics i używać usługi Analiza ruchu do zapewnienia wglądu w przepływ ruchu w chmurze platformy Azure. Niektóre zalety usługi Analiza ruchu to możliwość wizualizacji aktywności sieciowej i identyfikowania punktów aktywności, identyfikowania zagrożeń bezpieczeństwa, zrozumienia wzorców przepływu ruchu i identyfikowania błędnych konfiguracji sieci.

Uwaga: Podczas konfigurowania sieciowej API Management podsieci istnieje zestaw portów, które muszą być otwarte. Jeśli którykolwiek z tych portów jest niedostępny, API Management działać nieprawidłowo i mogą stać się niedostępne.

- [Understand NSG configurations for Azure API Management](./api-management-using-with-vnet.md#-common-network-configuration-issues)

- [How to Enable NSG Flow Logs](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Jak włączyć i używać Analiza ruchu](../network-watcher/traffic-analytics.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="13-protect-critical-web-applications"></a>1.3. Ochrona krytycznych aplikacji internetowych

**Wskazówki:** Aby chronić krytyczne interfejsy API sieci Web/protokołu HTTP API Management w sieci Virtual Network (Vnet) w trybie wewnętrznym i skonfigurować Azure Application Gateway. Application Gateway to usługa PaaS. Działa jako zwrotny serwer proxy i zapewnia równoważenie obciążenia L7, routing, zaporę aplikacji internetowej (WAF) i inne usługi.

Połączenie API Management aprowizowanych w wewnętrznej sieci wirtualnej z Application Gateway frontonie umożliwia korzystanie z następujących scenariuszy:
- Użyj pojedynczego zasobu API Management na udostępnianie wszystkich interfejsów API zarówno klientom wewnętrznym, jak i zewnętrznym.
- Użyj pojedynczego zasobu API Management do uwypowienia podzbioru interfejsów API klientom zewnętrznym.
- Zapewnianie sposobu przełączania dostępu do API Management z publicznego Internetu wł. i wył.

Uwaga: ta funkcja jest dostępna w warstwach Premium i Deweloper API Management.

- [Jak zintegrować sieć API Management wewnętrzną siecią wirtualną z Application Gateway](api-management-howto-integrate-internal-vnet-appgateway.md)

- [Opis Azure Application Gateway](../application-gateway/index.yml)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Odmowa komunikacji ze znanymi złośliwymi adresami IP

**Wskazówki:** konfigurowanie API Management w sieci Virtual Network (Vnet) w trybie wewnętrznym i konfigurowanie Azure Application Gateway. Application Gateway to usługa PaaS. Działa jako zwrotny serwer proxy i zapewnia równoważenie obciążenia L7, routing, zaporę aplikacji internetowej (WAF) i inne usługi.

Połączenie API Management aprowizowanych w wewnętrznej sieci wirtualnej z Application Gateway frontonie umożliwia korzystanie z następujących scenariuszy:
- Użyj pojedynczego zasobu API Management do uwypowienia wszystkich interfejsów API zarówno klientom wewnętrznym, jak i klientom zewnętrznym.
- Użyj pojedynczego zasobu API Management do uwsłaniania podzestawu interfejsów API klientom zewnętrznym.
- Zapewnianie sposobu przełączania dostępu do API Management z publicznego Internetu wł. i wył.

Uwaga: ta funkcja jest dostępna w warstwach Premium i Deweloper API Management.

Użyj Azure Security Center zintegrowanej analizy zagrożeń, aby uniemożliwić komunikację ze znanymi złośliwymi lub nieużywanymi internetowymi adresami IP.

- [Jak zintegrować API Management w wewnętrznej sieci wirtualnej z Application Gateway](api-management-howto-integrate-internal-vnet-appgateway.md)

- [Opis Azure Application Gateway](../application-gateway/index.yml)

- [Understand Azure Security Center Integrated Threat Intelligence](../security-center/azure-defender.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="15-record-network-packets"></a>1.5. Rejestrowanie pakietów sieciowych

**Wskazówki:** Ruch przychodzący i wychodzący do podsieci, w której wdrożono API Management, można kontrolować przy użyciu sieciowych grup zabezpieczeń. Wd wdrożenie sieciowej sieciowej API Management podsieci sieciowej, włączenie dzienników przepływu sieciowej API Management wysyłanie dzienników do konta usługi Azure Storage w celu inspekcji ruchu. Możesz również wysyłać dzienniki przepływu sieciowych grup zabezpieczeń do obszaru roboczego usługi Log Analytics i używać Analiza ruchu, aby zapewnić wgląd w przepływ ruchu w chmurze platformy Azure. Niektóre zalety usługi Analiza ruchu to możliwość wizualizacji aktywności sieciowej i identyfikowania punktów aktywności, identyfikowania zagrożeń bezpieczeństwa, zrozumienia wzorców przepływu ruchu i identyfikowania błędnych konfiguracji sieci.

Uwaga: Podczas konfigurowania sieciowej API Management podsieci istnieje zestaw portów, które muszą być otwarte. Jeśli którykolwiek z tych portów jest niedostępny, API Management działać nieprawidłowo i mogą stać się niedostępne.

- [Understand NSG configurations for Azure API Management](./api-management-using-with-vnet.md#-common-network-configuration-issues)

- [How to Enable NSG Flow Logs](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Jak włączyć i używać Analiza ruchu](../network-watcher/traffic-analytics.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6. Wdrażanie sieciowych systemów wykrywania/zapobiegania włamań (IDS/IPS)

**Wskazówki:** Konfigurowanie API Management w sieci Virtual Network (Vnet) w trybie wewnętrznym i konfigurowanie Azure Application Gateway. Application Gateway to usługa PaaS. Działa jako zwrotny serwer proxy i zapewnia równoważenie obciążenia L7, routing, zaporę aplikacji internetowej (WAF) i inne usługi. Application Gateway WAF zapewnia ochronę przed powszechnymi lukami w zabezpieczeniach i lukami w zabezpieczeniach i może działać w następujących dwóch trybach:
- Tryb wykrywania: monitoruje i rejestruje wszystkie alerty o zagrożeniach. Możesz włączyć diagnostykę rejestrowania dla Application Gateway w sekcji Diagnostyka. Należy upewnić się, że dziennik aplikacji sieci Szkieletowej jest wybrany i włączony. Zapora aplikacji internetowej nie blokuje żądań przychodzących, gdy działa w trybie wykrywania.
- Tryb zapobiegania: blokuje włamania i ataki wykrywane przez reguły. Osoba atakująca otrzymuje wyjątek "403 brak autoryzacji dostępu" i połączenie jest zamykane. Tryb zapobiegania rejestruje takie ataki w dziennikach WAF.

Połączenie API Management aprowizowanych w wewnętrznej sieci wirtualnej z Application Gateway frontonie umożliwia korzystanie z następujących scenariuszy:
- Użyj pojedynczego zasobu API Management na udostępnianie wszystkich interfejsów API zarówno klientom wewnętrznym, jak i zewnętrznym.
- Użyj pojedynczego zasobu API Management do uwsłaniania podzestawu interfejsów API klientom zewnętrznym.
- Zapewnianie sposobu przełączania dostępu do API Management z publicznego Internetu wł. i wył.

Uwaga: ta funkcja jest dostępna w warstwach Premium i Deweloper API Management.

- [Jak zintegrować API Management w wewnętrznej sieci wirtualnej z Application Gateway](api-management-howto-integrate-internal-vnet-appgateway.md)

- [Opis Azure Application Gateway WAF](../web-application-firewall/ag/ag-overview.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Zarządzanie ruchem do aplikacji internetowych

**Wskazówki:** Aby zarządzać ruchem przepływanym do interfejsów API sieci Web/HTTP, API Management wdrożyć usługę Virtual Network (Vnet) skojarzoną z App Service Environment w trybie zewnętrznym lub wewnętrznym.

W trybie wewnętrznym skonfiguruj Azure Application Gateway przed API Management. Application Gateway to usługa PaaS. Działa jako zwrotny serwer proxy i zapewnia równoważenie obciążenia L7, routing, zaporę aplikacji internetowej (WAF) i inne usługi. Application Gateway WAF zapewnia ochronę przed powszechnymi lukami w zabezpieczeniach i lukami w zabezpieczeniach.

Połączenie API Management aprowizowanych w wewnętrznej sieci wirtualnej z Application Gateway frontonie umożliwia korzystanie z następujących scenariuszy:
- Użyj pojedynczego zasobu API Management do uwypowienia wszystkich interfejsów API zarówno klientom wewnętrznym, jak i klientom zewnętrznym.
- Użyj pojedynczego zasobu API Management do uwsłaniania podzestawu interfejsów API klientom zewnętrznym.
- Zapewnianie sposobu przełączania dostępu do API Management z publicznego Internetu wł. i wył.

Uwaga: ta funkcja jest dostępna w warstwach Premium i Deweloper API Management.

- [Jak uwidocznić prywatne interfejsy API klientom zewnętrznym](/azure/architecture/example-scenario/apps/publish-internal-apis-externally)

- [Jak używać API Management sieci wirtualnej](api-management-using-with-vnet.md)

- [Azure Web Application Firewall na Azure Application Gateway](../web-application-firewall/ag/ag-overview.md)

- [Opis Azure Application Gateway](../application-gateway/overview.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8. Zminimalizuj złożoność i koszty administracyjne związane z regułami zabezpieczeń sieci

**Wskazówki:** użyj Virtual Network usługi sieci wirtualnej do zdefiniowania kontroli dostępu do sieci w sieciowych grupach zabezpieczeń używanych w podsieciach API Management sieciowych. Podczas tworzenia reguł zabezpieczeń można użyć tagów usługi zamiast konkretnych adresów IP. Określając nazwę tagu usługi (np. ApiManagement) w odpowiednim polu źródła lub miejsca docelowego reguły, można zezwolić na ruch dla odpowiedniej usługi lub go odmówić. Firma Microsoft zarządza prefiksami adresów obejmującymi tag usługi i automatycznie aktualizuje tag usługi w przypadku zmiany adresów.

Uwaga: Podczas konfigurowania sieciowej API Management podsieci istnieje zestaw portów, które muszą być otwarte. Jeśli którykolwiek z tych portów jest niedostępny, API Management działać nieprawidłowo i mogą stać się niedostępne.

- [Opis i używanie tagów usługi](../virtual-network/service-tags-overview.md)

- [Porty wymagane dla API Management](./api-management-using-with-vnet.md#-common-network-configuration-issues)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Obsługa standardowych konfiguracji zabezpieczeń dla urządzeń sieciowych

**Wskazówki:** Definiowanie i implementowanie standardowych konfiguracji zabezpieczeń dla ustawień sieciowych związanych z wdrożeniami usługi Azure API Management platformy Azure. Użyj aliasów usługi Azure Policy w przestrzeniach nazw "Microsoft.ApiManagement" i "Microsoft.Network", aby utworzyć niestandardowe zasady inspekcji lub wymuszania konfiguracji sieci wdrożeń usługi Azure API Management i powiązanych zasobów.

Za pomocą usługi Azure Blueprints można również uprościć wdrożenia platformy Azure na dużą skalę przez pakowanie kluczowych artefaktów środowiska, takich jak szablony usługi Azure Resource Manager, kontrola dostępu na podstawie ról (RBAC) platformy Azure i zasady w pojedynczej definicji strategii. Możesz łatwo zastosować plan do nowych subskrypcji, środowisk oraz dostosować kontrolę i zarządzanie za pomocą zarządzania wersjami.

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

- [Jak utworzyć usługę Azure Blueprint](../governance/blueprints/create-blueprint-portal.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="110-document-traffic-configuration-rules"></a>1.10. Udokumentuj reguły konfiguracji ruchu

**Wskazówki:** użyj tagów dla sieciowych grup zabezpieczeń i innych zasobów związanych z zabezpieczeniami sieci i przepływem ruchu. W przypadku poszczególnych reguł sieciowej organizacji sieciowej można użyć pola "Opis", aby określić potrzebę biznesową i/lub czas trwania (itp.) dla wszystkich reguł, które zezwalają na ruch do/z sieci.

- [Jak tworzyć tagi i ich używać](../azure-resource-manager/management/tag-resources.md)

- [Jak utworzyć Virtual Network](../virtual-network/quick-create-portal.md)

- [Jak utworzyć sieciowej grupy zabezpieczeń za pomocą konfiguracji zabezpieczeń](../virtual-network/tutorial-filter-network-traffic.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11. Monitorowanie konfiguracji zasobów sieciowych i wykrywanie zmian za pomocą zautomatyzowanych narzędzi

**Wskazówki:** Użyj dziennika aktywności platformy Azure, aby monitorować konfiguracje zasobów sieciowych i wykrywać zmiany zasobów sieciowych skojarzonych z wdrożeniami usługi Azure API Management sieci. Utwórz alerty w Azure Monitor, które będą wyzwalane w przypadku zmiany krytycznych zasobów sieciowych.

- [Jak wyświetlać i pobierać zdarzenia dziennika aktywności platformy Azure](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [Jak tworzyć alerty w Azure Monitor](../azure-monitor/alerts/alerts-activity-log.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

## <a name="logging-and-monitoring"></a>Rejestrowanie i monitorowanie

*Aby uzyskać więcej informacji, zobacz [Test porównawczy zabezpieczeń platformy Azure: rejestrowanie i monitorowanie.](../security/benchmarks/security-control-logging-monitoring.md)*

### <a name="22-configure-central-security-log-management"></a>2.2. Konfigurowanie centralnego zarządzania dziennikami zabezpieczeń

**Wskazówki:** w usłudze Azure Monitor użyj obszarów roboczych usługi Log Analytics do wykonywania zapytań i przeprowadzania analiz, wysyłania dzienników do usługi Azure Storage w celu przechowywania długoterminowego/archiwalnej lub analizy offline albo eksportowania dzienników do innego rozwiązania analitycznego na platformie Azure i w innych miejscach przy użyciu Azure Event Hubs. Usługa Azure API Management domyślnie wyprowadza dzienniki i metryki Azure Monitor dane wyjściowe. Szczegółowość rejestrowania można skonfigurować dla całej usługi i dla każdego interfejsu API.

Oprócz Azure Monitor usługę Azure API Management można zintegrować z jedną lub kilkoma usługami Azure Application Insights Services. Ustawienia rejestrowania dla Application Insights można skonfigurować na podstawie usługi lub interfejsu API.

Opcjonalnie możesz włączyć i dołączyć dane do Azure Sentinel lub zarządzania zdarzeniami zabezpieczeń i zdarzeniami innych firm (SIEM).

- [Jak skonfigurować ustawienia diagnostyczne](../azure-monitor/essentials/diagnostic-settings.md#create-in-azure-portal)

- [Jak do dołączać Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Jak rozpocząć pracę z Azure Monitor integracją rozwiązania SIEM i rozwiązania SIEM innej firmy](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

- [Jak utworzyć niestandardowy potok rejestrowania i analizy](api-management-howto-log-event-hubs.md)

- [Jak zintegrować usługę Azure Application Insights](api-management-howto-app-insights.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3. Włączanie rejestrowania inspekcji dla zasobów platformy Azure

**Wskazówki:** W przypadku rejestrowania inspekcji płaszczyzny sterowania włącz ustawienia diagnostyczne dziennika aktywności platformy Azure i wysyłaj dzienniki aktywności do obszaru roboczego usługi Log Analytics w celu raportowania i analizy, do usługi Azure Storage w celu długoterminowego bezpiecznego przechowywania do usługi Azure Event Hubs w celu eksportowania w innych rozwiązaniach analitycznych na platformie Azure i w innych miejscach. Korzystając z danych dziennika aktywności platformy Azure, można określić "co, kto i kiedy" dla wszystkich operacji zapisu (PUT, POST, DELETE) wykonywanych na poziomie płaszczyzny sterowania dla usługi Azure API Management Service.

W przypadku rejestrowania inspekcji płaszczyzny danych dzienniki diagnostyczne zawierają rozbudowane informacje o operacjach i błędach, które są ważne dla inspekcji, a także w celach rozwiązywania problemów. Dzienniki diagnostyczne różnią się od dzienników aktywności. Dzienniki aktywności udostępniają szczegółowe dane operacji wykonywanych w stosunku do zasobów platformy Azure. Dzienniki diagnostyczne udostępniają szczegółowe dane operacji wykonanych przez zasób.

- [Jak włączyć ustawienia diagnostyczne dla dziennika aktywności platformy Azure](../azure-monitor/essentials/activity-log.md)

- [Jak włączyć ustawienia diagnostyczne dla usługi Azure API Management](./api-management-howto-use-azure-monitor.md#resource-logs)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="25-configure-security-log-storage-retention"></a>2.5. Konfigurowanie przechowywania magazynu dzienników zabezpieczeń

**Wskazówki:** w Azure Monitor ustaw okres przechowywania obszaru roboczego usługi Log Analytics zgodnie z przepisami organizacji dotyczącymi zgodności. Użyj kont usługi Azure Storage na potrzeby magazynu długoterminowego/archiwalnej.

- [Jak ustawić parametry przechowywania dzienników dla obszarów roboczych usługi Log Analytics](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

- [Jak archiwizować dzienniki na koncie usługi Azure Storage](../azure-monitor/essentials/resource-logs.md#send-to-azure-storage)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="26-monitor-and-review-logs"></a>2.6. Monitorowanie i przeglądanie dzienników

**Wskazówki:** usługa Azure API Management stale emituje dzienniki i metryki do usługi Azure Monitor, zapewniając wgląd w stan i kondycję interfejsów API niemal w czasie rzeczywistym. Za pomocą Azure Monitor i obszarów roboczych usługi Log Analytics można przeglądać, konfigurować metryki i dzienniki pochodzące z API Management i powiązanych zasobów, a także konfigurować je, a także konfigurować zapytania, wizualizować, rozsyłać, archiwizować i konfigurować alerty. Analizowanie i monitorowanie dzienników pod celu zachowania anomalii oraz regularne przeglądanie wyników.

Opcjonalnie możesz zintegrować API Management z usługą Azure Application Insights i używać jej jako podstawowego lub pomocniczego narzędzia do monitorowania, śledzenia, raportowania i zgłaszania alertów.

- [Jak monitorować i przeglądać dzienniki usługi Azure API Management](./api-management-howto-use-azure-monitor.md)

- [Jak wykonywać zapytania niestandardowe w Azure Monitor](../azure-monitor/logs/get-started-queries.md)

- [Opis obszaru roboczego usługi Log Analytics](../azure-monitor/logs/log-analytics-tutorial.md)

- [Jak zintegrować z usługą Azure Application Insights](api-management-howto-app-insights.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7. Włączanie alertów dla niesłychanych działań

**Wskazówki:** włącz ustawienia diagnostyczne dziennika aktywności platformy Azure, a także ustawienia diagnostyczne dla wystąpień usługi Azure API Management i wyślij dzienniki do obszaru roboczego usługi Log Analytics. Wykonywanie zapytań w u usługi Log Analytics w celu wyszukiwania terminów, identyfikowania trendów, analizowania wzorców i zapewnienia wielu innych szczegółowych informacji na podstawie zebranych danych. Alerty można tworzyć na podstawie zapytań obszaru roboczego usługi Log Analytics.

Utwórz alerty dotyczące metryk, aby wiedzieć, kiedy coś nieoczekiwanego się dzieje. Możesz na przykład otrzymywać powiadomienia, gdy wystąpienie usługi Azure API Management przekroczyło oczekiwaną szczytową pojemność w określonym czasie lub wystąpiła pewna liczba nieautoryzowanych żądań bramy lub błędów we wstępnie zdefiniowanym okresie.

Opcjonalnie możesz zintegrować API Management z usługą Azure Application Insights i używać jej jako podstawowego lub pomocniczego narzędzia do monitorowania, śledzenia, raportowania i zgłaszania alertów.

Opcjonalnie możesz włączyć i dołączyć dane do rozwiązania Azure Sentinel lub rozwiązania SIEM innej firmy.

- [Jak włączyć ustawienia diagnostyczne dla dziennika aktywności platformy Azure](../azure-monitor/essentials/activity-log.md)

- [Jak włączyć ustawienia diagnostyczne dla usługi Azure API Management](./api-management-howto-use-azure-monitor.md#resource-logs)

- [Jak skonfigurować regułę alertu dla usługi Azure API Management](./api-management-howto-use-azure-monitor.md#set-up-an-alert-rule)

- [Jak wyświetlić metryki pojemności wystąpienia usługi Azure API Management](api-management-capacity.md)

- [Jak zintegrować usługę Azure Application Insights](api-management-howto-app-insights.md)

- [Jak do dołączać Azure Sentinel](../sentinel/quickstart-onboard.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

## <a name="identity-and-access-control"></a>Tożsamość i kontrola dostępu

*Aby uzyskać więcej informacji, zobacz Test porównawczy zabezpieczeń platformy [Azure: tożsamość i Access Control](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Utrzymywanie spisu kont administracyjnych

**Wskazówki:** utrzymywanie spisu kont z dostępem administracyjnym do płaszczyzny kontroli usługi Azure API Management (Azure Portal).

Azure Active Directory (Azure AD) ma wbudowane role, które muszą być jawnie przypisane i które mogą być odpytywalne. API Management te role i Role-Based Access Control umożliwiają precyzyjne zarządzanie dostępem dla API Management i jednostek.

Ponadto API Management wbudowanej grupy Administratorzy w systemie API Management użytkownika aplikacji. Grupy w API Management kontrolują widoczność interfejsów API w portalu dla deweloperów, a członkowie grupy Administratorzy mogą zobaczyć wszystkie interfejsy API.

Postępuj zgodnie z zaleceniami Azure Security Center zarządzania kontami administracyjnymi i ich konserwacji.

- [Jak używać kontroli dostępu opartej na rolach w usłudze Azure API Management](api-management-role-based-access-control.md)

- [Jak uzyskać listę użytkowników w wystąpieniu usługi Azure API Management](/powershell/module/az.apimanagement/get-azapimanagementuser)

- [Jak uzyskać listę użytkowników przypisanych do roli katalogu w usłudze Azure AD przy użyciu programu PowerShell](/powershell/module/az.resources/get-azroleassignment)

- [Jak uzyskać definicję roli katalogu w usłudze Azure AD przy użyciu programu PowerShell](/powershell/module/az.resources/get-azroledefinition)

- [Opis zaleceń dotyczących tożsamości i dostępu z Azure Security Center](../security-center/recommendations-reference.md#identityandaccess-recommendations)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Zmień domyślne hasła tam, gdzie ma to zastosowanie

**Wskazówki:** usługa Azure API Management nie ma koncepcji domyślnych haseł/kluczy.

Subskrypcje API Management Azure, które są jednym ze sposobów zabezpieczania dostępu do interfejsów API, są jednak dostępne z parą wygenerowanych kluczy subskrypcji. Klienci mogą ponownie wygenerować te klucze subskrypcji w dowolnym momencie.

- [Understanding Azure API Management Subscriptions](api-management-subscriptions.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Używanie dedykowanych kont administracyjnych

**Wskazówki:** Tworzenie standardowych procedur operacyjnych dotyczących korzystania z dedykowanych kont administracyjnych. Użyj Azure Security Center zarządzania tożsamościami i dostępem, aby monitorować liczbę kont administracyjnych.

Ponadto, aby ułatwić śledzenie dedykowanych kont administracyjnych, możesz skorzystać z zaleceń Azure Security Center lub wbudowanych zasad platformy Azure, takich jak:
- Do subskrypcji powinien być przypisany więcej niż jeden właściciel
- Przestarzałe konta z uprawnieniami właściciela powinny zostać usunięte z subskrypcji
- Konta zewnętrzne z uprawnieniami właściciela powinny zostać usunięte z subskrypcji

- [Jak używać usługi Azure Security Center do monitorowania tożsamości i dostępu (wersja zapoznawcza)](../security-center/security-center-identity-access.md)

- [Jak używać Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4: Używanie Azure Active Directory jednokrotnego (SSO)

**Wskazówki:** Usługę Azure API Management można skonfigurować do korzystania z usługi Azure Active Directory (Azure AD) jako dostawcy tożsamości do uwierzytelniania użytkowników w portalu dla deweloperów w celu skorzystania z możliwości logowania jednokrotnego oferowanych przez usługę Azure AD. Po skonfigurowaniu nowi użytkownicy portalu dla deweloperów mogą zdecydować się na wykonanie procesu rejestracji w trybie out-of-the-box, najpierw uwierzytelnijąc się za pośrednictwem usługi Azure AD, a następnie kończąc proces rejestracji w portalu po uwierzytelnieniu.

- [Autoryzowanie kont deweloperów przy użyciu usługi Azure AD w usłudze Azure API Management](api-management-howto-aad.md)

Alternatywnie proces logowania/rejestracji można dodatkowo dostosować za pomocą delegowania. Delegowanie umożliwia korzystanie z istniejącej witryny internetowej do obsługi logowania/rejestracji i subskrypcji produktów przez deweloperów, w przeciwieństwie do korzystania z wbudowanych funkcji w portalu dla deweloperów. Dzięki temu witryna internetowa może być właścicielem danych użytkownika i wykonywać walidację tych kroków w niestandardowy sposób.

- [Jak delegować rejestrację użytkownika i subskrypcję produktu](api-management-howto-setup-delegation.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Używaj uwierzytelniania wieloskładnikowego dla Azure Active Directory dostępu opartego na Azure Active Directory danych

**Wskazówki:** Włącz uwierzytelnianie wieloskładnikowe usługi Azure Active Directory (Azure AD) i postępuj zgodnie z Azure Security Center dotyczącymi zarządzania tożsamościami i dostępem.

- [Jak włączyć uwierzytelnianie wieloskładnikowe na platformie Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Jak monitorować tożsamość i dostęp w ramach Azure Security Center](../security-center/security-center-identity-access.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6. Użyj dedykowanych maszyn (stacji roboczych z dostępem uprzywilejowanym) do wykonywania wszystkich zadań administracyjnych

**Wskazówki:** Używaj stacji roboczych z dostępem uprzywilejowanym (PAW) z uwierzytelnianiem wieloskładnikowym skonfigurowanym do logowania się i konfigurowania zasobów platformy Azure.

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

**Wskazówki:** Użyj dostępu warunkowego nazwanych lokalizacji, aby zezwolić na dostęp do usługi Azure Portal tylko z określonych logicznych grup zakresów adresów IP lub krajów/regionów.

- [Jak skonfigurować nazwane lokalizacje na platformie Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="39-use-azure-active-directory"></a>3.9: Użyj Azure Active Directory

**Wskazówki:** jeśli to możliwe, należy Azure Active Directory (Azure AD) jako centralnego systemu uwierzytelniania i autoryzacji. Usługa Azure AD chroni dane przy użyciu silnego szyfrowania danych magazynowany i podczas przesyłania. Usługa Azure AD dodaje również instyty, skróty i bezpiecznie przechowuje poświadczenia użytkownika.

Skonfiguruj portal deweloperów usługi Azure API Management do uwierzytelniania kont deweloperów przy użyciu usługi Azure AD.

Skonfiguruj wystąpienie usługi Azure API Management, aby chronić interfejsy API przy użyciu protokołu OAuth 2.0 w usłudze Azure AD.

- [Jak autoryzować konta deweloperów przy użyciu usługi Azure AD w usłudze Azure API Management](api-management-howto-aad.md)

- [Jak chronić interfejs API przy użyciu protokołu OAuth 2.0 z usługą Azure AD i API Management](api-management-howto-protect-backend-with-aad.md)

- [Jak utworzyć i skonfigurować wystąpienie usługi Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Regularne przeglądanie i uzgadnianie dostępu użytkowników

**Wskazówki:** usługa Azure Active Directory (Azure AD) udostępnia dzienniki, które ułatwiają odnajdywanie nieaktywnego konta. Klienci mogą korzystać z przeglądów dostępu do tożsamości platformy Azure, aby efektywnie zarządzać członkostwem w grupach, dostępem do aplikacji dla przedsiębiorstw i przypisań ról. Dostęp użytkowników można regularnie przeglądać, aby upewnić się, że tylko odpowiedni użytkownicy nadal mają odpowiedni dostęp.

Klienci mogą utrzymywać spis kont API Management użytkowników i uzgadniać dostęp zgodnie z potrzebami. W API Management deweloperzy są konsumentami interfejsów API, które są udostępniane za pomocą API Management. Domyślnie nowo utworzone konta deweloperów są aktywne i skojarzone z grupą Deweloperzy. Konta deweloperów, które są w stanie aktywnym, mogą służyć do uzyskiwania dostępu do wszystkich interfejsów API, dla których mają subskrypcje.

Administratorzy mogą tworzyć grupy niestandardowe lub wykorzystywać grupy zewnętrzne w skojarzonych dzierżawach usługi Azure AD. Niestandardowe i zewnętrzne grupy mogą być używane razem z grupami systemowymi, zapewniając deweloperom widoczność produktów interfejsu API i dostęp do nich.

- [Zarządzanie kontami użytkowników w usłudze Azure API Management](api-management-howto-create-or-invite-developers.md)

- [Jak uzyskać listę użytkowników API Management użytkowników](/powershell/module/az.apimanagement/get-azapimanagementuser)

- [Jak tworzyć grupy do zarządzania kontami deweloperów w usłudze Azure API Management i jak z nich korzystać](api-management-howto-create-groups.md)

- [Jak używać przeglądów dostępu do tożsamości platformy Azure](../active-directory/governance/access-reviews-overview.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Monitorowanie prób uzyskania dostępu do dezaktywowanych poświadczeń

**Wskazówki:** Skonfiguruj wystąpienie usługi Azure API Management do uwierzytelniania kont deweloperów przy użyciu usługi Azure Active Directory (Azure AD) jako dostawcy tożsamości w usłudze Azure API Management.

Skonfiguruj wystąpienie usługi Azure API Management, aby chronić interfejsy API przy użyciu protokołu OAuth 2.0 w usłudze Azure AD.

Skonfiguruj zasady weryfikacji JWT dla przychodzących żądań interfejsu API, aby ułatwić wymuszanie istnienia i ważności prawidłowego tokenu.

Utwórz ustawienia diagnostyczne dla kont użytkowników usługi Azure AD i wyślij dzienniki inspekcji i dzienniki logowania do obszaru roboczego usługi Log Analytics. Konfigurowanie żądanych alertów w u usługi Log Analytics. Ponadto możesz do dołączać obszar roboczy usługi Log Analytics do Azure Sentinel rozwiązania SIEM lub rozwiązania SIEM innej firmy.

Skonfiguruj zaawansowane monitorowanie za pomocą API Management użyciu zasad, przechwyć wszelkie dodatkowe informacje o kontekście wymagane do analizy zabezpieczeń i wyślij je do rozwiązania Azure Sentinel lub rozwiązania `log-to-eventhub` SIEM innej firmy.

- [Jak autoryzować konta deweloperów przy użyciu usługi Azure AD w usłudze Azure API Management](api-management-howto-aad.md)

- [Jak chronić interfejs API przy użyciu protokołu OAuth 2.0 z usługą Azure AD i usługą API Management](api-management-howto-protect-backend-with-aad.md)

- [Zasady ograniczeń dostępu usługi API Management](api-management-access-restriction-policies.md)

- [Jak zintegrować dzienniki usługi Azure AD z Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Jak Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Zaawansowane monitorowanie interfejsów API](api-management-log-to-eventhub-sample.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12: Odchylenie zachowania alertu podczas logowania na koncie

**Wskazówki:** w przypadku odchylenia zachowania logowania do konta na płaszczyźnie kontroli (Azure Portal) użyj usługi Azure Active Directory (Azure AD) Identity Protection i funkcji wykrywania ryzyka, aby skonfigurować automatyczne odpowiedzi na wykryte podejrzane akcje związane z tożsamościami użytkowników. Możesz również pozysać dane do Azure Sentinel celu dalszego badania.

- [Jak wyświetlić ryzykowne logowania w usłudze Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

- [Jak skonfigurować i włączyć zasady ryzyka usługi Identity Protection](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Jak do dołączać Azure Sentinel](../sentinel/quickstart-onboard.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Zapewnianie firmie Microsoft dostępu do odpowiednich danych klientów podczas scenariuszy pomocy technicznej

**Wskazówki:** obecnie niedostępne; Skrytka klienta nie jest obecnie obsługiwana w przypadku usługi Azure API Management.

- [Lista usług Skrytka klienta obsługiwanych przez program](../security/fundamentals/customer-lockbox-overview.md#supported-services-and-scenarios-in-general-availability)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

## <a name="data-protection"></a>Ochrona danych

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: ochrona danych](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1. Utrzymywanie spisu informacji poufnych

**Wskazówki:** użyj tagów, aby pomóc w śledzeniu zasobów platformy Azure, które przechowują lub przetwarzają poufne informacje.

- [Jak tworzyć tagi i ich używać](../azure-resource-manager/management/tag-resources.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Izolowanie systemów przechowujące lub przetwarzające informacje poufne

**Wskazówki:** Implementowanie oddzielnych subskrypcji i/lub grup zarządzania na rzecz tworzenia, testowania i produkcji. Wystąpienia API Management Azure powinny być oddzielone siecią wirtualną (VNet)/podsiecią i odpowiednio oznakowane.

- [Jak utworzyć dodatkowe subskrypcje platformy Azure](../cost-management-billing/manage/create-subscription.md)

- [Jak utworzyć Grupy zarządzania](../governance/management-groups/create-management-group-portal.md)

- [Jak tworzyć tagi i ich używać](../azure-resource-manager/management/tag-resources.md)

- [Używanie usługi Azure API Management z sieciami wirtualnymi](api-management-using-with-vnet.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Monitorowanie i blokowanie nieautoryzowanego transferu poufnych informacji

**Wskazówki:** obecnie niedostępne; Funkcje identyfikacji, klasyfikacji i zapobiegania utracie danych nie są obecnie dostępne dla usługi Azure API Management.

Firma Microsoft zarządza podstawową infrastrukturą usługi Azure API Management i wdrożyła ścisłe mechanizmy kontroli, aby zapobiec utracie lub narażeniu danych klientów.

- [Informacje na temat ochrony danych klientów na platformie Azure](../security/fundamentals/protection-customer-data.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Szyfruj wszystkie poufne informacje podczas przesyłania

**Wskazówki:** Wywołania płaszczyzny zarządzania są Azure Resource Manager za pośrednictwem TLS. Wymagany jest prawidłowy token sieci Web JSON (JWT). Wywołania płaszczyzny danych można zabezpieczyć za pomocą protokołu TLS i jednego z obsługiwanych mechanizmów uwierzytelniania (na przykład certyfikatu klienta lub JWT).

- [Opis ochrony danych w usłudze Azure API Management](#data-protection)

- [Zarządzanie ustawieniami TLS w usłudze Azure API Management](api-management-howto-manage-protocols-ciphers.md)

- [Ochrona interfejsów API w usłudze Azure API Management za pomocą Azure Active Directory (Azure AD)](api-management-howto-protect-backend-with-aad.md)

- [Ochrona interfejsów API w usłudze Azure API Management za pomocą Azure AD B2C](howto-protect-backend-frontend-azure-ad-b2c.md)

**Odpowiedzialność**: Współużytkowane

**Azure Security Center monitorowania:** Brak

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5. Identyfikowanie poufnych danych za pomocą aktywnego narzędzia odnajdywania

**Wskazówki:** jeszcze niedostępne; Funkcje identyfikacji, klasyfikacji i zapobiegania utracie danych nie są jeszcze dostępne dla usługi Azure API Management. Oznaczanie usług azure API Management usługami, które mogą przetwarzać poufne informacje jako takie, i implementowanie rozwiązania innych firm, jeśli jest to wymagane do celów zgodności.

W przypadku podstawowej platformy zarządzanej przez firmę Microsoft firma Microsoft traktuje całą zawartość klientów jako poufne i bardzo długo chroni przed utratą i narażeniem danych klientów. Aby zapewnić bezpieczeństwo danych klientów na platformie Azure, firma Microsoft wdrożyła i utrzymuje pakiet niezawodnych funkcji i kontroli ochrony danych.

- [Informacje na temat ochrony danych klientów na platformie Azure](../security/fundamentals/protection-customer-data.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Kontrola dostępu do zasobów przy użyciu kontroli dostępu na platformie Azure 

**Wskazówki:** Kontrola dostępu oparta na rolach (RBAC) platformy Azure umożliwia kontrolowanie dostępu do usługi Azure API Management. Usługa Azure API Management korzysta z kontroli dostępu opartej na rolach platformy Azure, aby umożliwić precyzyjne zarządzanie dostępem dla usług API Management i jednostek (na przykład interfejsów API i zasad).

- [Jak używać kontroli dostępu opartej na rolach platformy Azure w usłudze Azure API Management](api-management-role-based-access-control.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7. Wymuszanie kontroli dostępu przy użyciu ochrony przed utratą danych opartej na hoście

**Wskazówki:** Nie dotyczy; To zalecenie jest przeznaczone dla zasobów obliczeniowych.

Firma Microsoft zarządza podstawową infrastrukturą usługi Azure API Management i wdrożyła ścisłe mechanizmy kontroli, aby zapobiec utracie lub narażeniu danych klientów.

- [Informacje na temat ochrony danych klientów na platformie Azure](../security/fundamentals/protection-customer-data.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9. Rejestrowanie zmian krytycznych zasobów platformy Azure i alerty dotyczące zmian

**Wskazówki:** użyj Azure Monitor z dziennika aktywności platformy Azure, aby tworzyć alerty dotyczące sytuacji, w których zmiany mają miejsce w aplikacjach produkcyjnych Azure Functions, a także innych krytycznych lub powiązanych zasobach.

- [Jak tworzyć alerty dla zdarzeń dziennika aktywności platformy Azure](../azure-monitor/alerts/alerts-activity-log.md)

- [Jak używać dzienników Azure Monitor i dziennika aktywności platformy Azure w usłudze Azure API Management](api-management-howto-use-azure-monitor.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

## <a name="vulnerability-management"></a>Zarządzanie lukami w zabezpieczeniach

*Aby uzyskać więcej informacji, zobacz [Test porównawczy zabezpieczeń platformy Azure: zarządzanie lukami w zabezpieczeniach.](../security/benchmarks/security-control-vulnerability-management.md)*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1. Uruchamianie zautomatyzowanych narzędzi do skanowania luk w zabezpieczeniach

**Wskazówki:** obecnie niedostępne; ocena luk w zabezpieczeniach w Azure Security Center nie jest obecnie dostępna dla usługi Azure API Management.

Podstawowa platforma przeskanowana i poprawiona przez firmę Microsoft. Przejrzyj dostępne mechanizmy kontroli zabezpieczeń, aby ograniczyć luki w zabezpieczeniach związane z konfiguracją usługi.

- [Informacje o mechanizmach kontroli zabezpieczeń dostępnych dla usługi Azure API Management]()

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2. Wdrażanie zautomatyzowanego rozwiązania do zarządzania poprawkami systemu operacyjnego

**Wskazówki:** Nie dotyczy; To zalecenie jest przeznaczone dla zasobów obliczeniowych.

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5.3. Wdrażanie zautomatyzowanego rozwiązania do zarządzania poprawkami dla tytułów oprogramowania innych firm

**Wskazówki:** Nie dotyczy; To zalecenie jest przeznaczone dla zasobów obliczeniowych.

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4. Porównanie skanów luk w zabezpieczeniach typu "wstecz"

**Wskazówki:** Nie dotyczy; To zalecenie jest przeznaczone dla zasobów obliczeniowych.

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5. Ustalanie priorytetów korygowania wykrytych luk w zabezpieczeniach za pomocą procesu oceny ryzyka

**Wskazówki:** obecnie niedostępne; ocena luk w zabezpieczeniach w Azure Security Center nie jest obecnie dostępna dla usługi Azure API Management.

Podstawowa platforma przeskanowana i poprawiona przez firmę Microsoft. Klient może przejrzeć dostępne mechanizmy kontroli zabezpieczeń w celu ograniczenia luk w zabezpieczeniach związanych z konfiguracją usługi.


**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

## <a name="inventory-and-asset-management"></a>Zarządzanie magazynem i zasobami

*Aby uzyskać więcej informacji, zobacz [Test porównawczy zabezpieczeń platformy Azure: spis i zarządzanie zasobami.](../security/benchmarks/security-control-inventory-asset-management.md)*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1. Korzystanie z rozwiązania do automatycznego odnajdywania zasobów

**Wskazówki:** użyj Azure Resource Graph, aby odpytywać/odnajdywać wszystkie zasoby (takie jak zasoby obliczeniowe, magazynowe, sieciowe, porty i protokoły itp.) w ramach subskrypcji. Zapewnij odpowiednie uprawnienia (odczyt) w dzierżawie i wyliczenie wszystkich subskrypcji platformy Azure, a także zasobów w ramach twoich subskrypcji.

Mimo że klasyczne zasoby platformy Azure mogą być Resource Graph pośrednictwem usługi , zdecydowanie zaleca się tworzenie i używanie Azure Resource Manager zasobów w przyszłości.

- [Jak tworzyć zapytania za pomocą Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

- [Jak wyświetlić subskrypcje platformy Azure](/powershell/module/az.accounts/get-azsubscription?preserve-view=true&view=azps-4.8.0)

- [Opis kontroli RBAC na platformie Azure](../role-based-access-control/overview.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="62-maintain-asset-metadata"></a>6.2. Obsługa metadanych zasobów

**Wskazówki:** Stosowanie tagów do zasobów platformy Azure z metadanymi w celu logicznego zorganizowania ich w taksonomię.

- [Jak tworzyć i wykorzystywać tagi](../azure-resource-manager/management/tag-resources.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="63-delete-unauthorized-azure-resources"></a>6.3. Usuwanie nieautoryzowanych zasobów platformy Azure

**Wskazówki:** W celu organizowania i śledzenia zasobów platformy Azure używaj tagowania, grup zarządzania i oddzielnych subskrypcji, jeśli jest to konieczne. Regularnie uzgadniaj spis i upewnij się, że nieautoryzowane zasoby zostaną usunięte z subskrypcji w terminowy sposób.

Ponadto użyj zasad platformy Azure, aby wprowadzić ograniczenia dotyczące typu zasobów, które można utworzyć w subskrypcjach klientów, korzystając z następujących wbudowanych definicji zasad:
- Niedozwolone typy zasobów
- Dozwolone typy zasobów

- [Jak utworzyć dodatkowe subskrypcje platformy Azure](../cost-management-billing/manage/create-subscription.md)

- [Jak utworzyć Grupy zarządzania](../governance/management-groups/create-management-group-portal.md)

- [Jak tworzyć tagi i ich używać](../azure-resource-manager/management/tag-resources.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4: Definiowanie i utrzymywanie spisu zatwierdzonych zasobów platformy Azure

**Wskazówki:** Nie dotyczy; To zalecenie jest przeznaczone dla zasobów obliczeniowych.

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Monitorowanie niezatwierdzonych zasobów platformy Azure

**Wskazówki:** użyj Azure Policy, aby wprowadzić ograniczenia dotyczące typu zasobów, które mogą być tworzone w twoich subskrypcjach, korzystając z następujących wbudowanych definicji zasad:
- Niedozwolone typy zasobów
- Dozwolone typy zasobów

Użyj Azure Resource Graph, aby odpytywać/odnajdywać zasoby w ramach swoich subskrypcji. Upewnij się, że wszystkie zasoby platformy Azure obecne w środowisku zostały zatwierdzone.

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

- [Jak tworzyć zapytania za pomocą usługi Azure Graph](../governance/resource-graph/first-query-portal.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6. Monitorowanie niezatwierdzonych aplikacji w ramach zasobów obliczeniowych

**Wskazówki:** Nie dotyczy; To zalecenie jest przeznaczone dla zasobów obliczeniowych.

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7. Usuwanie niezatwierdzonych zasobów i aplikacji platformy Azure

**Wskazówki:** Nie dotyczy; To zalecenie jest przeznaczone dla zasobów obliczeniowych.

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="68-use-only-approved-applications"></a>6.8: Używaj tylko zatwierdzonych aplikacji

**Wskazówki:** Nie dotyczy; To zalecenie jest przeznaczone dla zasobów obliczeniowych.

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="69-use-only-approved-azure-services"></a>6.9. Używaj tylko zatwierdzonych usług platformy Azure

**Wskazówki:** użyj Azure Policy, aby wprowadzić ograniczenia dotyczące typu zasobów, które mogą być tworzone w subskrypcjach klientów, korzystając z następujących wbudowanych definicji zasad:
- Niedozwolone typy zasobów
- Dozwolone typy zasobów

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

- [Jak odmówić określonego typu zasobu za pomocą Azure Policy](../governance/policy/samples/built-in-policies.md#general)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10: Utrzymywanie spisu zatwierdzonych tytułów oprogramowania

**Wskazówki:** Nie dotyczy; To zalecenie jest przeznaczone dla zasobów obliczeniowych.

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Ograniczanie możliwości interakcji użytkowników z Azure Resource Manager

**Wskazówki:** Skonfiguruj dostęp warunkowy platformy Azure, aby ograniczyć użytkownikom możliwość interakcji z usługą Azure Resource Manager, konfigurując opcję "Blokuj dostęp" dla aplikacji "Microsoft Azure Management".

- [Jak skonfigurować dostęp warunkowy, aby zablokować dostęp do Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

- [Kontrola dostępu oparta na rolach w usłudze Azure API Management](api-management-role-based-access-control.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: Ogranicz użytkownikom możliwość wykonywania skryptów w ramach zasobów obliczeniowych

**Wskazówki:** Nie dotyczy; To zalecenie jest przeznaczone dla zasobów obliczeniowych.

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Fizyczne lub logiczne rozdzielenie aplikacji wysokiego ryzyka

**Wskazówki:** Nie dotyczy; To zalecenie jest przeznaczone dla aplikacji internetowych działających w Azure App Service zasobów obliczeniowych.

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

## <a name="secure-configuration"></a>Bezpieczna konfiguracja

*Aby uzyskać więcej informacji, zobacz [Test porównawczy zabezpieczeń platformy Azure: bezpieczna konfiguracja.](../security/benchmarks/security-control-secure-configuration.md)*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1. Ustanów bezpieczne konfiguracje dla wszystkich zasobów platformy Azure

**Wskazówki:** Definiowanie i implementowanie standardowych konfiguracji zabezpieczeń dla usługi Azure API Management przy użyciu Azure Policy. Użyj Azure Policy aliasów w przestrzeni nazw "Microsoft.ApiManagement", aby utworzyć niestandardowe zasady inspekcji lub wymuszania konfiguracji usług Azure API Management Services.

- [Jak wyświetlić dostępne Azure Policy aliasy](/powershell/module/az.resources/get-azpolicyalias?preserve-view=true&view=azps-4.8.0)

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="72-establish-secure-operating-system-configurations"></a>7.2. Ustanawianie bezpiecznych konfiguracji systemu operacyjnego

**Wskazówki:** Nie dotyczy; To zalecenie jest przeznaczone dla zasobów obliczeniowych.

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Obsługa bezpiecznych konfiguracji zasobów platformy Azure

**Wskazówki:** Definiowanie i implementowanie standardowych konfiguracji zabezpieczeń dla usług Azure API Management za pomocą Azure Policy. Użyj Azure Policy aliasów w przestrzeni nazw "Microsoft.ApiManagement", aby utworzyć niestandardowe zasady inspekcji lub wymuszania konfiguracji usługi Azure API Management wystąpień. Użyj zasad platformy Azure [deny] i [deploy if not exist], aby wymusić bezpieczne ustawienia w zasobach platformy Azure.

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

- [Opis Azure Policy danych](../governance/policy/concepts/effects.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Obsługa bezpiecznych konfiguracji systemu operacyjnego

**Wskazówki:** Nie dotyczy; To zalecenie jest przeznaczone dla zasobów obliczeniowych.

**Odpowiedzialność**: Współużytkowane

**Azure Security Center monitorowania:** Brak

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Bezpieczne przechowywanie konfiguracji zasobów platformy Azure

**Wskazówki:** Jeśli używasz niestandardowych definicji zasad platformy Azure, użyj Azure DevOps lub Azure Repos, aby bezpiecznie przechowywać konfigurację usługi Azure API Management i zarządzać nimi.

- [Jak przechowywać pliki w Azure DevOps](/azure/devops/repos/git/gitworkflow)

- [Azure Repos dokumentacji](/azure/devops/repos/)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Bezpieczne przechowywanie niestandardowych obrazów systemu operacyjnego

**Wskazówki:** Nie dotyczy; To zalecenie jest przeznaczone dla zasobów obliczeniowych.

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7. Wdrażanie narzędzi do zarządzania konfiguracją dla zasobów platformy Azure

**Wskazówki:** Definiowanie i implementowanie standardowych konfiguracji zabezpieczeń dla usług Azure API Management za pomocą Azure Policy. Użyj Azure Policy aliasów w przestrzeni nazw "Microsoft.ApiManagement", aby utworzyć niestandardowe zasady inspekcji lub wymuszania konfiguracji usługi Azure API Management wystąpień. Użyj zasad platformy Azure [deny] i [deploy if not exist], aby wymusić bezpieczne ustawienia w zasobach platformy Azure.

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

- [Opis Azure Policy danych](../governance/policy/concepts/effects.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8. Wdrażanie narzędzi do zarządzania konfiguracją dla systemów operacyjnych

**Wskazówki:** Nie dotyczy; To zalecenie jest przeznaczone dla zasobów obliczeniowych.

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9. Implementowanie zautomatyzowanego monitorowania konfiguracji dla zasobów platformy Azure

**Wskazówki:** użyj zestawu Azure API Management DevOps Resource Kit, aby wykonać zarządzanie konfiguracją dla usługi Azure API Management.

Ponadto zdefiniuj i zaim implementuj standardowe konfiguracje zabezpieczeń dla usług Azure API Management przy użyciu Azure Policy. Użyj Azure Policy aliasów w przestrzeni nazw "Microsoft.ApiManagement", aby utworzyć niestandardowe zasady inspekcji lub wymuszania konfiguracji usługi Azure API Management wystąpień. Użyj zasad platformy Azure [deny] i [deploy if not exist], aby wymusić bezpieczne ustawienia w zasobach platformy Azure.

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

- [Opis Azure Policy danych](../governance/policy/concepts/effects.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Implementowanie zautomatyzowanego monitorowania konfiguracji dla systemów operacyjnych

**Wskazówki:** Nie dotyczy; To zalecenie jest przeznaczone dla zasobów obliczeniowych.

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Bezpieczne i automatyczne zarządzanie tożsamościami

**Wskazówki:** Użyj tożsamości usługi zarządzanej wygenerowanej przez usługę Azure Active Directory (Azure AD), aby umożliwić wystąpieniu usługi API Management łatwy i bezpieczny dostęp do innych zasobów chronionych przez usługę Azure AD, takich jak Azure Key Vault.

- [Jak utworzyć tożsamość zarządzaną dla wystąpienia API Management zarządzanego](api-management-howto-use-managed-service-identity.md)

- [Zasady uwierzytelniania za pomocą tożsamości zarządzanej](./api-management-authentication-policies.md#ManagedIdentity)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Eliminowanie niezamierzonego narażenia na poświadczenia

**Wskazówki:** implementowanie skanera poświadczeń w celu identyfikowania poświadczeń w kodzie. Skaner poświadczeń ułatwia również przenoszenie odnalezionych poświadczeń do bezpieczniejszych lokalizacji, takich jak usługa Azure Key Vault.

- [Jak skonfigurować skaner poświadczeń](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

## <a name="malware-defense"></a>Ochrona przed złośliwym oprogramowaniem

*Aby uzyskać więcej informacji, zobacz [Test porównawczy zabezpieczeń platformy Azure: Malware Defense.](../security/benchmarks/security-control-malware-defense.md)*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1. Korzystanie z centralnie zarządzanego oprogramowania chroniącego przed złośliwym oprogramowaniem

**Wskazówki:** Nie dotyczy; To zalecenie jest przeznaczone dla zasobów obliczeniowych.

Ochrona przed złośliwym oprogramowaniem firmy Microsoft jest włączona na podstawowym hoście, który obsługuje usługi platformy Azure (na przykład Azure API Management), ale nie jest uruchamiane na zawartości klienta.

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Wstępne skanowanie plików do przesłania do zasobów platformy Azure bez obliczeń

**Wskazówki:** Nie dotyczy; To zalecenie jest przeznaczone dla zasobów innych niż obliczeniowe przeznaczone do przechowywania danych.

Ochrona przed złośliwym oprogramowaniem firmy Microsoft jest włączona na podstawowym hoście, który obsługuje usługi platformy Azure (na przykład Azure API Management), ale nie jest uruchamiane na zawartości klienta.

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3. Upewnij się, że oprogramowanie chroniące przed złośliwym oprogramowaniem i podpisy zostały zaktualizowane

**Wskazówki:** Nie dotyczy; To zalecenie jest przeznaczone dla zasobów innych niż obliczeniowe przeznaczone do przechowywania danych.

Ochrona przed złośliwym oprogramowaniem firmy Microsoft jest włączona na podstawowym hoście, który obsługuje usługi platformy Azure (na przykład Azure API Management), ale nie jest uruchamiane na zawartości klienta.

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

## <a name="data-recovery"></a>Odzyskiwanie danych

*Aby uzyskać więcej informacji, zobacz [Test porównawczy zabezpieczeń platformy Azure: odzyskiwanie danych.](../security/benchmarks/security-control-data-recovery.md)*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Zapewnianie regularnych automatycznych kopii zapasowej

**Wskazówki:** Publikując interfejsy API i zarządzając nimi za pośrednictwem usługi Azure API Management, wykorzystujesz odporność na uszkodzenia i możliwości infrastruktury, które w przeciwnym razie chcesz ręcznie zaprojektować, zaimplementować i zarządzać nimi. API Management wdrożenia w wielu regionach, co sprawia, że płaszczyzna danych jest nieprzepuszczana na awarie regionalne bez dodawania jakichkolwiek kosztów operacyjnych.

Funkcje tworzenia i przywracania kopii zapasowych usług API Management bloków konstrukcyjnych niezbędnych do wdrożenia strategii odzyskiwania po awarii. Operacje tworzenia kopii zapasowej i przywracania można wykonywać ręcznie lub automatycznie.

- [Jak wdrożyć API Management danych w wielu regionach](api-management-howto-deploy-multi-region.md)

- [Jak zaimplementować odzyskiwanie po awarii przy użyciu funkcji tworzenia i przywracania kopii zapasowych w usłudze Azure API Management](./api-management-howto-disaster-recovery-backup-restore.md#calling-the-backup-and-restore-operations)

- [Jak wywołać operację tworzenia API Management kopii zapasowej](/rest/api/apimanagement/2019-12-01/apimanagementservice/backup)

- [Jak wywołać operację API Management przywracania](/rest/api/apimanagement/2019-12-01/apimanagementservice/restore)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2. Wykonywanie pełnych kopii zapasowych systemu i tworzenie kopii zapasowych dowolnych kluczy zarządzanych przez klienta

**Wskazówki:** Wykonywanie operacji tworzenia kopii zapasowej i przywracania dostarczanych przez usługę Azure API Management wykonywanie pełnych kopii zapasowych i przywracanie systemu.

Tożsamości zarządzane mogą służyć do uzyskiwania certyfikatów z Azure Key Vault dla API Management nazw domen. Należy utworzyć kopię zapasową wszystkich certyfikatów przechowywanych w Azure Key Vault.

- [Jak zaimplementować odzyskiwanie po awarii przy użyciu funkcji tworzenia i przywracania kopii zapasowych w usłudze Azure API Management](./api-management-howto-disaster-recovery-backup-restore.md#calling-the-backup-and-restore-operations)

- [Jak utworzyć kopię zapasową Azure Key Vault certyfikatów](/powershell/module/az.keyvault/backup-azkeyvaultcertificate?preserve-view=true&view=azps-4.8.0)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Zweryfikuj wszystkie kopie zapasowe, w tym klucze zarządzane przez klienta

**Wskazówki:** Zweryfikuj kopie zapasowe, wykonując testowe przywracanie usługi i certyfikatów z kopii zapasowych.

- [Jak wywołać operację API Management przywracania](/rest/api/apimanagement/2019-12-01/apimanagementservice/restore)

- [Jak przywrócić Azure Key Vault certyfikatów](/powershell/module/az.keyvault/restore-azkeyvaultcertificate)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4. Zapewnianie ochrony kopii zapasowych i kluczy zarządzanych przez klienta

**Wskazówki:** Usługa Azure API Management zapisuje kopie zapasowe na kontach usługi Azure Storage należących do klienta. Postępuj zgodnie z zaleceniami zabezpieczeń usługi Azure Storage, aby chronić kopię zapasową.

- [Jak zaimplementować odzyskiwanie po awarii przy użyciu funkcji tworzenia i przywracania kopii zapasowych w usłudze Azure API Management](./api-management-howto-disaster-recovery-backup-restore.md#calling-the-backup-and-restore-operations)

- [Zalecenie dotyczące zabezpieczeń magazynu obiektów blob](../storage/blobs/security-recommendations.md)

Włącz Soft-Delete w Key Vault, aby chronić klucze przed przypadkowym lub złośliwym usunięciem.

- [Jak włączyć Soft-Delete w Key Vault](../storage/blobs/soft-delete-blob-overview.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

## <a name="incident-response"></a>Reagowanie na zdarzenia

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: reagowanie na zdarzenia](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1. Tworzenie przewodnika po reagowaniu na zdarzenia

**Wskazówka**: Utwórz przewodnik odpowiedzi na zdarzenia dla swojej organizacji. Upewnij się, że istnieją zarejestrowane plany reakcji na zdarzenia, które definiują wszystkie role pracowników, a także etapy obsługi zdarzeń/zarządzania od wykrywania do oceny po zdarzeniu.

- [Wskazówki dotyczące tworzenia własnego procesu reagowania na zdarzenia zabezpieczeń](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Centrum zabezpieczeń firmy Microsoft Anatomy of an Incident (Anatomia zdarzenia)](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [Skorzystaj z przewodnika obsługi zdarzeń zabezpieczeń komputera NIST, aby pomóc w tworzeniu własnego planu reagowania na zdarzenia](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2. Tworzenie procedury oceniania i ustalania priorytetów zdarzeń

**Wskazówki:** Security Center przypisać ważność do każdego alertu, aby ułatwić priorytetyzowanie alertów, które należy zbadać w pierwszej kolejności. Ważność jest oparta na tym, jak pewna Security Center znajduje się w znajdowaniu lub analizie użytej do wystawienia alertu, a także na poziomie ufności, że za działaniem, które doprowadziło do tego alertu, znajduje się złośliwe intencje.

Ponadto wyraźnie oznaczaj subskrypcje (na przykład production, non-prod) przy użyciu tagów i utwórz system nazewnictwa, aby jasno identyfikować i kategoryzować zasoby platformy Azure, zwłaszcza te, które przetwarzają poufne dane. Odpowiedzialność za korygowanie alertów w oparciu o krytyczne znaczenie zasobów platformy Azure i środowisko, w którym wystąpiło zdarzenie, leży po stronie użytkownika.

- [Alerty zabezpieczeń w Centrum zabezpieczeń Azure](../security-center/security-center-alerts-overview.md)

- [Organizowanie zasobów platformy Azure przy użyciu tagów](../azure-resource-manager/management/tag-resources.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="103-test-security-response-procedures"></a>10.3: Testowanie procedur reagowania na zabezpieczenia

**Wskazówki:** Przećwiczyj ćwiczenia w celu testowania możliwości reagowania na zdarzenia w systemach w regularnych odstępach czasu, aby ułatwić ochronę zasobów platformy Azure. Zidentyfikuj słabe punkty i przerwy oraz popraw plan zgodnie z wymaganiami.

- [Publikacja NIST — Guide to Test, Training, and Exercise Programs for IT Plans and Capabilities (Przewodnik dotyczący programów testowych, szkoleniowych i szkoleniowych dla planów i możliwości IT)](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4. Podaj szczegóły kontaktowe zdarzenia zabezpieczeń i skonfiguruj powiadomienia o alertach dla zdarzeń zabezpieczeń

**Wskazówki:** informacje kontaktowe dotyczące zdarzeń naruszenia zabezpieczeń będą używane przez firmę Microsoft do kontaktowania się z tobą, jeśli Centrum zabezpieczeń firmy Microsoft (MSRC) odkryje, że dostęp do danych użytkownika został uzyskany przez niedozwoloną lub niedozwoloną stronę. Przejrzyj zdarzenia po fakcie, aby upewnić się, że problemy zostały rozwiązane.

- [Jak ustawić kontakt Azure Security Center zabezpieczeń](../security-center/security-center-provide-security-contact-details.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5. Dołączanie alertów zabezpieczeń do systemu reagowania na zdarzenia

**Wskazówki:** eksportowanie alertów Azure Security Center i zaleceń przy użyciu funkcji eksportu ciągłego, aby ułatwić identyfikowanie zagrożeń dla zasobów platformy Azure. Eksport ciągły umożliwia eksportowanie alertów i zaleceń ręcznie lub w ciągły sposób. Możesz użyć łącznika danych Azure Security Center, aby przesyłać strumieniowo alerty do Azure Sentinel.

- [Jak skonfigurować eksport ciągły](../security-center/continuous-export.md)

- [Jak przesłać strumieniowo alerty do usługi Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatyzowanie odpowiedzi na alerty zabezpieczeń

**Wskazówki:** Korzystanie z funkcji automatyzacji przepływu pracy w Azure Security Center do automatycznego wyzwalania odpowiedzi za pośrednictwem Logic Apps alertów zabezpieczeń i zaleceń.

- [Jak skonfigurować automatyzację przepływu pracy i Logic Apps](../security-center/workflow-automation.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

## <a name="penetration-tests-and-red-team-exercises"></a>Testy penetracyjne i ćwiczenia typu „red team”

*Aby uzyskać więcej informacji, zobacz [test porównawczy zabezpieczeń platformy Azure: testy penetracyjne i ćwiczenia zespołu czerwonego.](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Przeprowadzaj regularne testy penetrowania zasobów platformy Azure i zapewniaj korygowanie wszystkich krytycznych ustaleń zabezpieczeń

**Wskazówki:** Postępuj zgodnie z zasadami firmy Microsoft w zakresie zaangażowania, aby upewnić się, że testy penetracyjne nie naruszały zasad firmy Microsoft: https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1 .

- [Więcej informacji na temat strategii firmy Microsoft i wykonywania czerwonego zespołu oraz testów penetracyjnych na żywo dotyczących infrastruktury, usług i aplikacji w chmurze zarządzanej przez firmę Microsoft można znaleźć tutaj](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Odpowiedzialność**: Współużytkowane

**Azure Security Center monitorowania:** Brak

## <a name="next-steps"></a>Następne kroki

- Zobacz [Omówienie testu porównawczego zabezpieczeń platformy Azure w wersji 2](../security/benchmarks/overview.md)
- Dowiedz się więcej o [punktach odniesienia zabezpieczeń platformy Azure](../security/benchmarks/security-baselines-overview.md)
