---
title: Podstawa zabezpieczeń platformy Azure dla Logic Apps
description: Linia bazowa zabezpieczeń Logic Apps zawiera wskazówki i zasoby dotyczące procedur związanych z wdrażaniem zaleceń dotyczących zabezpieczeń określonych w teście zabezpieczeń platformy Azure.
author: msmbaldwin
ms.service: logic-apps
ms.topic: conceptual
ms.date: 09/01/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: e47dfeab7466faf9a60b161b05d8b3bf39ebdb6a
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98203307"
---
# <a name="azure-security-baseline-for-logic-apps"></a>Podstawa zabezpieczeń platformy Azure dla Logic Apps

Podstawą zabezpieczeń platformy Azure dla Logic Apps są zalecenia, które pomogą ulepszyć stan bezpieczeństwa wdrożenia.

Punkt odniesienia dla tej usługi jest rysowany w [wersji 1,0 usługi Azure Security test](../security/benchmarks/overview.md), która zawiera zalecenia dotyczące sposobu zabezpieczania rozwiązań w chmurze na platformie Azure z naszymi najlepszymi wskazówkami.

Aby uzyskać więcej informacji, zobacz [podstawy zabezpieczeń platformy Azure — omówienie](../security/benchmarks/security-baselines-overview.md).

## <a name="network-security"></a>Bezpieczeństwo sieci

*Aby uzyskać więcej informacji, zobacz [wzorzec zabezpieczeń Azure: zabezpieczenia sieci](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: Ochrona zasobów platformy Azure w ramach sieci wirtualnych

**Wskazówki**: Łączniki działające w ramach usługi Logic Apps z wieloma dzierżawcami są wdrażane i zarządzane przez firmę Microsoft. Te łączniki udostępniają wyzwalacze i akcje umożliwiające dostęp do usług w chmurze, systemów lokalnych lub obu tych programów, w tym pakietów Office 365, Azure Blob Storage, SQL Server, Dynamics, Salesforce, SharePoint i innych. Możesz użyć znacznika usługi AzureConnectors podczas określania reguł w sieciowej grupie zabezpieczeń lub zaporze platformy Azure, aby zezwolić na dostęp do powiązanych zasobów.

W przypadku aplikacji logiki, które wymagają bezpośredniego dostępu do zasobów w sieci wirtualnej platformy Azure, można utworzyć środowisko usługi integracji (ISE), w którym można tworzyć, wdrażać i uruchamiać aplikacje logiki w dedykowanych zasobach. Niektóre sieci wirtualne platformy Azure używają prywatnych punktów końcowych (prywatnego linku platformy Azure) do zapewniania dostępu do usług PaaS platformy Azure, takich jak Azure Storage, Azure Cosmos DB, Azure SQL Database, usług partnerskich lub usług klienta hostowanych na platformie Azure. Jeśli aplikacje logiki wymagają dostępu do sieci wirtualnych, które używają prywatnych punktów końcowych, należy utworzyć, wdrożyć i uruchomić te aplikacje logiki w ramach ISE.

Po utworzeniu ISE można użyć wewnętrznych lub zewnętrznych punktów końcowych dostępu. Wybór określa, czy wyzwalacze żądania lub elementu webhook w usłudze Logic Apps w ISE mogą odbierać wywołania spoza sieci wirtualnej. Wewnętrzne i zewnętrzne punkty końcowe dostępu wpływają również na to, czy można wyświetlić historię uruchamiania aplikacji logiki, w tym dane wejściowe i wyjściowe dla przebiegu, z wewnątrz lub na zewnątrz sieci wirtualnej.

Upewnij się, że wszystkie wdrożenia podsieci sieci wirtualnej powiązane z ISEą mają sieciową grupę zabezpieczeń zastosowanej do kontroli dostępu do sieci, które są specyficzne dla zaufanych portów i źródeł aplikacji. Podczas wdrażania aplikacji logiki w ISE Użyj linku prywatnego. Link prywatny platformy Azure umożliwia dostęp do usług Azure PaaS i hostowanych usług partnerskich i partnerów platformy Azure za pośrednictwem prywatnego punktu końcowego w sieci wirtualnej. Alternatywnie, jeśli masz określony przypadek użycia, możesz spełnić to wymaganie, implementując zaporę platformy Azure. Aby zmniejszyć złożoność podczas konfigurowania reguł zabezpieczeń, użyj tagów usługi reprezentujących grupy prefiksów adresów IP dla określonej usługi platformy Azure.

- [Omówienie łączników dla Logic Apps](../connectors/apis-list.md)

- [Zrozumienie tagów usługi na platformie Azure](../virtual-network/service-tags-overview.md)

- [Informacje o dostępie do zasobów platformy Azure Virtual Network z Azure Logic Apps przy użyciu środowisk usługi integracji (ISEs)](connect-virtual-network-vnet-isolated-environment-overview.md)

- [Informacje o punktach końcowych usługi Virtual Network](../virtual-network/virtual-network-service-endpoints-overview.md)

- [Opis linku prywatnego platformy Azure](../private-link/private-link-overview.md)

- [Informacje o dostępie do punktu końcowego ISE](connect-virtual-network-vnet-isolated-environment-overview.md#ise-endpoint-access)

- [Jak utworzyć Virtual Network](../virtual-network/quick-create-portal.md)

- [Jak utworzyć sieciowej grupy zabezpieczeń z konfiguracją zabezpieczeń](../virtual-network/tutorial-filter-network-traffic.md)

- [Jak wdrożyć i skonfigurować zaporę platformy Azure](../firewall/tutorial-firewall-deploy-portal.md)

- [Jak włączyć dostęp dla ISE](connect-virtual-network-vnet-isolated-environment.md#enable-access-for-ise)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Współużytkowane

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1,2: Monitoruj i Rejestruj konfigurację oraz ruch sieci wirtualnych, podsieci i interfejsów sieciowych

**Wskazówki**: Jeśli uruchamiasz Aplikacje logiki w środowisku usługi integracji (ISE), które używa zewnętrznego punktu dostępu, możesz użyć sieciowej grupy zabezpieczeń (sieciowej grupy zabezpieczeń), aby zmniejszyć ryzyko wystąpienia eksfiltracji danych. Włącz dzienniki przepływu sieciowej grupy zabezpieczeń i Wyślij dzienniki do konta usługi Azure Storage na potrzeby inspekcji ruchu. Dzienniki przepływu sieciowej grupy zabezpieczeń można także wysyłać do obszaru roboczego Log Analytics i używać Analiza ruchu, aby uzyskać wgląd w przepływ ruchu w chmurze platformy Azure. Niektóre zalety Analiza ruchu to możliwość wizualizacji aktywności sieciowej i identyfikowania aktywnych punktów, identyfikowania zagrożeń bezpieczeństwa, zrozumienia wzorców przepływu ruchu i wyznaczania konfiguracji sieci.

- [Informacje o dostępie do punktu końcowego ISE](connect-virtual-network-vnet-isolated-environment-overview.md#ise-endpoint-access)

- [Jak włączyć dzienniki przepływu sieciowej grupy zabezpieczeń](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Jak włączyć i używać Analiza ruchu](../network-watcher/traffic-analytics.md)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="13-protect-critical-web-applications"></a>1,3: Ochrona krytycznych aplikacji sieci Web

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone dla aplikacji sieci Web działających na Azure App Service lub zasobach obliczeniowych.

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: odmowa komunikacji ze znanymi niezłośliwymi adresami IP

**Wskazówki**: Jeśli aplikacja logiki używa wyzwalacza opartego na żądaniach, który odbiera przychodzące wywołania lub żądania, takie jak wyzwalacz żądania lub elementu webhook, można ograniczyć dostęp, tak aby tylko autoryzowani klienci mogli wywołać aplikację logiki.

Jeśli uruchamiasz Aplikacje logiki w środowisku usługi integracji (ISE), Włącz Standard DDoS Protection w sieci wirtualnej skojarzonej z ISE, aby chronić przed atakami DDoS. Użyj Azure Security Center zintegrowanej analizy zagrożeń, aby odmówić komunikacji ze znanymi złośliwymi lub nieużywanymi adresami IP.

Wdróż zaporę platformy Azure w każdej z granic sieci organizacji z włączoną analizą zagrożeń i skonfigurowaną jako "Alert i Odmów" w celu uzyskania złośliwego ruchu sieciowego.

Użyj Azure Security Center dostęp do sieci w czasie w celu skonfigurowania sieciowych grup zabezpieczeń w celu ograniczenia narażenia punktów końcowych na zatwierdzone adresy IP przez ograniczony okres.

Aby zalecać sieciowej grupy zabezpieczeń konfiguracje, które ograniczają porty i źródłowe adresy IP w oparciu o rzeczywisty ruch i analizę zagrożeń, należy użyć Azure Security Center.

- [Jak zabezpieczyć wywołania przychodzące do Logic Apps](logic-apps-securing-a-logic-app.md#secure-inbound-requests)

- [Jak ograniczyć przychodzące adresy IP](logic-apps-securing-a-logic-app.md#restrict-inbound-ip-addresses)

- [Jak skonfigurować ochronę DDoS](../ddos-protection/manage-ddos-protection.md)

- [Jak wdrożyć zaporę platformy Azure](../firewall/tutorial-firewall-deploy-portal.md)

- [Opis Azure Security Center zintegrowanej analizy zagrożeń](../security-center/azure-defender.md)

- [Informacje o Azure Security Center adaptacyjnej ograniczania przepustowości sieci](../security-center/security-center-adaptive-network-hardening.md)

- [Informacje o Azure Security Center Access Control sieci w czasie](../security-center/security-center-just-in-time.md)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="15-record-network-packets"></a>1,5: rejestrowanie pakietów sieciowych

**Wskazówki**: Jeśli uruchamiasz Aplikacje logiki w środowisku usługi integracji (ISE), które używa zewnętrznego punktu dostępu, możesz użyć sieciowej grupy zabezpieczeń (sieciowej grupy zabezpieczeń), aby zmniejszyć ryzyko wystąpienia eksfiltracji danych. Włącz dzienniki przepływu sieciowej grupy zabezpieczeń i Wyślij dzienniki do konta usługi Azure Storage na potrzeby inspekcji ruchu. Dzienniki przepływu sieciowej grupy zabezpieczeń można także wysyłać do obszaru roboczego Log Analytics i używać Analiza ruchu, aby uzyskać wgląd w przepływ ruchu w chmurze platformy Azure. Niektóre zalety Analiza ruchu to możliwość wizualizacji aktywności sieciowej i identyfikowania aktywnych punktów, identyfikowania zagrożeń bezpieczeństwa, zrozumienia wzorców przepływu ruchu i wyznaczania konfiguracji sieci.

Aby zapewnić lepszą ochronę i informacje dotyczące ruchu sieciowego, można odwoływać się do dzienników dostępu, które są generowane tylko wtedy, gdy zostały włączone dla każdego wystąpienia Application Gateway. Ten dziennik służy do wyświetlania wzorców dostępu Application Gateway i analizowania ważnych informacji. Dotyczy to adresu IP wywołującego, żądanego adresu URL, opóźnienia odpowiedzi, kodu powrotu i liczby bajtów.

W przeciwnym razie możesz skorzystać z rozwiązań innych firm z portalu Marketplace, aby spełnić to wymaganie.

- [Informacje o dostępie do punktu końcowego ISE](connect-virtual-network-vnet-isolated-environment-overview.md#ise-endpoint-access)

- [Jak włączyć dzienniki przepływu sieciowej grupy zabezpieczeń](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Jak włączyć i używać Analiza ruchu](../network-watcher/traffic-analytics.md)

- [Jak zintegrować API Management w wewnętrznej sieci wirtualnej z Application Gateway](../api-management/api-management-howto-integrate-internal-vnet-appgateway.md)

- [Jak zrozumieć dzienniki dostępu WAF](../web-application-firewall/ag/web-application-firewall-logs.md#access-log)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: wdrażanie opartych na sieci systemów zapobiegania wykrywaniem i dostępem intruzów (identyfikatorów/adresów IP)

**Wskazówki**: wybierz ofertę z witryny Azure Marketplace, która obsługuje funkcje identyfikatorów/adresów IP z możliwościami inspekcji ładunku.  Jeśli wykrywanie i/lub zapobieganie włamaniu nie są wymagane w oparciu o inspekcję ładunku, można użyć zapory platformy Azure z funkcją analizy zagrożeń. Filtrowanie oparte na analizie zagrożeń na platformie Azure może wysyłać alerty i odrzucać ruch do i ze znanych złośliwych adresów IP i domen. Adresy IP i domeny pochodzą z kanału informacyjnego analizy zagrożeń firmy Microsoft.

Wdrażaj wybrane rozwiązanie zapory w każdej z granic sieci organizacji, aby wykrywać i/lub odrzucać złośliwy ruch.

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

- [Jak wdrożyć zaporę platformy Azure](../firewall/tutorial-firewall-deploy-portal.md)

- [Jak skonfigurować alerty za pomocą zapory platformy Azure](../firewall/threat-intel.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="17-manage-traffic-to-web-applications"></a>1,7: zarządzanie ruchem do aplikacji sieci Web

**Wskazówki**: Jeśli uruchamiasz Aplikacje logiki w środowisku usługi integracji (ISE), wdróż Application Gateway platformy Azure.

- [Jak zintegrować API Management w wewnętrznej sieci wirtualnej z Application Gateway](../api-management/api-management-howto-integrate-internal-vnet-appgateway.md)

- [Jak skonfigurować Application Gateway do korzystania z protokołu HTTPS](../application-gateway/create-ssl-portal.md) 

- [Omówienie równoważenia obciążenia warstwy 7 za pomocą bram aplikacji sieci Web platformy Azure](../application-gateway/overview.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: Minimalizacja złożoności i kosztów administracyjnych reguł zabezpieczeń sieci

**Wskazówki**: dla zasobów, które wymagają dostępu do wystąpień Azure Logic Apps, użyj tagów usługi sieci wirtualnej, aby zdefiniować kontrolę dostępu do sieci dla sieciowych grup zabezpieczeń lub zapory platformy Azure. Podczas tworzenia reguł zabezpieczeń można użyć tagów usługi zamiast konkretnych adresów IP. Określając nazwę tagu usługi (np. LogicApps, LogicAppsManagement) w odpowiednim polu źródłowym lub docelowym reguły, można zezwolić na ruch dla odpowiedniej usługi lub go odrzucić. Firma Microsoft zarządza prefiksami adresów, które obejmują tag usługi, i automatycznie aktualizuje tag usługi jako adresy.

- [Aby uzyskać więcej informacji na temat używania tagów usługi](../virtual-network/service-tags-overview.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: Obsługa standardowych konfiguracji zabezpieczeń dla urządzeń sieciowych

**Wskazówki**: Definiowanie i implementowanie standardowych konfiguracji zabezpieczeń dla zasobów sieciowych związanych z wystąpieniami Azure Logic Apps przy użyciu Azure Policy. Użyj aliasów Azure Policy w przestrzeniach nazw "Microsoft. Logic" i "Microsoft. Network", aby utworzyć zasady niestandardowe do inspekcji lub wymuszania konfiguracji sieci wystąpień usługi Azure Logic Apps. Możesz również używać wbudowanych definicji zasad, takich jak:

- Dzienniki diagnostyczne w Logic Apps powinny być włączone

- Należy włączyć Standard DDoS Protection

Możesz również użyć planów platformy Azure, aby uprościć wdrożenia platformy Azure na dużą skalę przez pakowanie kluczowych artefaktów środowiska, takich jak szablony Azure Resource Manager, kontrola dostępu oparta na rolach na platformie Azure (RBAC) i zasady, w ramach jednej definicji planu. Łatwo stosuj plan do nowych subskrypcji i środowisk, a następnie dostosuj kontrolę i zarządzanie przy użyciu wersji.

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

- [Jak utworzyć Azure Blueprint](../governance/blueprints/create-blueprint-portal.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="110-document-traffic-configuration-rules"></a>1,10: udokumentowanie reguł konfiguracji ruchu

**Wskazówki**: dla poszczególnych reguł sieciowej grupy zabezpieczeń Użyj pola "Description" (opis), aby określić potrzeby biznesowe i/lub czas trwania (itp.) dla reguł zezwalających na ruch do/z sieci.

Użyj dowolnych wbudowanych definicji Azure Policy związanych ze znakiem, takich jak "Wymagaj tagu i jego wartości", aby upewnić się, że wszystkie zasoby są tworzone przy użyciu tagów i powiadomienia o istniejących nieoznakowanych zasobach.

Możesz użyć Azure PowerShell lub interfejsu wiersza polecenia platformy Azure, aby wyszukiwać lub wykonywać akcje na zasobach na podstawie ich tagów.

- [Tworzenie i używanie tagów](../azure-resource-manager/management/tag-resources.md)

- [Jak utworzyć Virtual Network](../virtual-network/quick-create-portal.md)

- [Jak utworzyć sieciowej grupy zabezpieczeń z konfiguracją zabezpieczeń](../virtual-network/tutorial-filter-network-traffic.md)

- [Lista definicji Azure Policy dla Logic Apps](./policy-reference.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: Użyj zautomatyzowanych narzędzi do monitorowania konfiguracji zasobów sieciowych i wykrywania zmian

**Wskazówki**: Użyj dziennika aktywności platformy Azure do monitorowania konfiguracji zasobów sieciowych i wykrywania zmian zasobów sieciowych związanych z wystąpieniami Azure Logic Apps. Tworzenie alertów w ramach Azure Monitor, które będą wyzwalane po wprowadzeniu zmian w krytycznych zasobach sieciowych.

- [Jak wyświetlać i pobierać zdarzenia dziennika aktywności platformy Azure](../azure-monitor/platform/activity-log.md#view-the-activity-log)

- [Jak utworzyć alerty w Azure Monitor](../azure-monitor/platform/alerts-activity-log.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

## <a name="logging-and-monitoring"></a>Rejestrowanie i monitorowanie

*Aby uzyskać więcej informacji, zobacz temat [Azure Security test: rejestrowanie i monitorowanie](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2,1: Użyj źródeł synchronizacji zatwierdzonego czasu

**Wskazówki**: Firma Microsoft utrzymuje źródło czasu używane dla zasobów platformy Azure, takie jak Azure Logic Apps sygnatury czasowe w dziennikach.

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2,2: Skonfiguruj centralne zarządzanie dziennikami zabezpieczeń

**Wskazówki**: Aby uzyskać bogatsze informacje debugowania dotyczące aplikacji logiki w czasie wykonywania, można skonfigurować i użyć dzienników Azure monitor do rejestrowania i przechowywania informacji dotyczących danych i zdarzeń środowiska uruchomieniowego, takich jak zdarzenia wyzwalania, zdarzenia uruchamiania i zdarzenia akcji w obszarze roboczym log Analytics. Usługa Azure Monitor pomaga w monitorowaniu środowisk w chmurze oraz lokalnych w celu łatwiejszego zachowania ich dostępności i wydajności. Korzystając z dzienników Azure Monitor, można tworzyć zapytania dziennika, które pomagają zbierać i przeglądać te informacje. Możesz również używać tych danych diagnostycznych z innymi usługami platformy Azure, takimi jak Azure Storage i Azure Event Hubs.

Alternatywnie możesz włączyć i dołączyć dane do usługi Azure wskaźnikowej lub SIEM innych firm. 

- [Jak włączyć ustawienia diagnostyczne dla dziennika aktywności platformy Azure](../azure-monitor/platform/activity-log.md)

- [Jak skonfigurować Azure Monitor dzienników i zbierać dane diagnostyczne dla Azure Logic Apps](monitor-logic-apps-log-analytics.md)

- [Jak dołączyć wskaźnik na platformie Azure](../sentinel/quickstart-onboard.md) 

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: Włączanie rejestrowania inspekcji dla zasobów platformy Azure

**Wskazówki**: Aby uzyskać bogatsze informacje debugowania dotyczące aplikacji logiki w czasie wykonywania, można skonfigurować i użyć dzienników Azure monitor do rejestrowania i przechowywania informacji dotyczących danych i zdarzeń środowiska uruchomieniowego, takich jak zdarzenia wyzwalania, zdarzenia uruchamiania i zdarzenia akcji w obszarze roboczym log Analytics. Usługa Azure Monitor pomaga w monitorowaniu środowisk w chmurze oraz lokalnych w celu łatwiejszego zachowania ich dostępności i wydajności. Korzystając z dzienników Azure Monitor, można tworzyć zapytania dziennika, które pomagają zbierać i przeglądać te informacje. Możesz również używać tych danych diagnostycznych z innymi usługami platformy Azure, takimi jak Azure Storage i Azure Event Hubs.

Alternatywnie możesz włączyć i dołączyć dane do usługi Azure wskaźnikowej lub SIEM innych firm. 

- [Jak włączyć ustawienia diagnostyczne dla dziennika aktywności platformy Azure](../azure-monitor/platform/activity-log.md)

- [Jak skonfigurować Azure Monitor dzienników i zbierać dane diagnostyczne dla Azure Logic Apps](monitor-logic-apps-log-analytics.md)

- [Jak dołączyć wskaźnik na platformie Azure](../sentinel/quickstart-onboard.md) 

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: Zbierz dzienniki zabezpieczeń z systemów operacyjnych

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone do zasobów obliczeniowych.

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="25-configure-security-log-storage-retention"></a>2,5: Konfigurowanie przechowywania magazynu dzienników zabezpieczeń

**Wskazówki**: po utworzeniu i uruchomieniu aplikacji logiki można sprawdzić stan uruchomienia aplikacji logiki, historię uruchamiania, historię wyzwalania i wydajność. Aby monitorować zdarzenia w czasie rzeczywistym i bogatsze debugowanie, skonfiguruj rejestrowanie diagnostyki dla aplikacji logiki przy użyciu dzienników Azure Monitor. Ta usługa platformy Azure ułatwia monitorowanie środowisk w chmurze i lokalnych, dzięki czemu można łatwiej zachować swoją dostępność i wydajność. Następnie można znaleźć i wyświetlić zdarzenia, takie jak zdarzenia wyzwalania, zdarzenia uruchamiania i zdarzenia akcji. Przechowując te informacje w dziennikach Azure Monitor, można utworzyć zapytania dziennika, które ułatwiają znalezienie i przeanalizowanie tych informacji. Możesz również używać tych danych diagnostycznych z innymi usługami platformy Azure, takimi jak Azure Storage i Azure Event Hubs.

W Azure Monitor Ustaw okres przechowywania dziennika dla dzienników skojarzonych z wystąpieniami Azure Logic Apps zgodnie z regulacjami zgodności w organizacji.

- [Jak monitorować stan uruchomienia, przeglądać historię wyzwalaczy i konfigurować alerty dla Azure Logic Apps](monitor-logic-apps.md)

- [Jak ustawić parametry przechowywania dziennika](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="26-monitor-and-review-logs"></a>2,6: dzienniki monitorowania i przeglądania

**Wskazówki**: Aby skonfigurować rejestrowanie dla aplikacji logiki, możesz włączyć log Analytics podczas tworzenia aplikacji logiki lub można zainstalować rozwiązanie do zarządzania Logic Apps w obszarze roboczym log Analytics dla istniejących aplikacji logiki. To rozwiązanie zawiera zagregowane informacje o uruchomieniach aplikacji logiki i zawiera szczegółowe informacje, takie jak stan, czas wykonywania, stan ponownego przesłania i identyfikatory korelacji. Następnie, aby włączyć rejestrowanie i utworzyć zapytania dotyczące tych informacji, skonfiguruj dzienniki Azure Monitor.

Możesz również włączyć ustawienia diagnostyki dziennika aktywności platformy Azure i wysłać dzienniki do obszaru roboczego Log Analytics. Wykonuj zapytania w Log Analytics, aby wyszukiwać terminy, identyfikować trendy, analizować wzorce i udostępniać wiele innych szczegółowych informacji na podstawie danych dziennika aktywności, które mogły zostać zebrane dla Azure Logic Apps.

Alternatywnie możesz włączyć i dołączyć dane do usługi Azure wskaźnikowej lub SIEM innych firm. 

- [Jak skonfigurować Azure Monitor dzienników i zbierać dane diagnostyczne dla Azure Logic Apps](monitor-logic-apps-log-analytics.md)

- [Jak włączyć ustawienia diagnostyczne dla dziennika aktywności platformy Azure](../azure-monitor/platform/activity-log.md)

- [Jak zbierać i analizować dzienniki aktywności platformy Azure w Log Analytics w Azure Monitor](../azure-monitor/platform/activity-log.md)

- [Jak dołączyć wskaźnik na platformie Azure](../sentinel/quickstart-onboard.md) 

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: Włączanie alertów dla nietypowych działań

**Wskazówki**: Użyj Azure Security Center z log Analytics do monitorowania i wysyłania alertów dotyczących nietypowej aktywności znalezionych w dziennikach i zdarzeniach zabezpieczeń.

Alternatywnie możesz włączyć i dołączyć dane do usługi Azure wskaźnikowej.

- [Jak dołączyć wskaźnik na platformie Azure](../sentinel/quickstart-onboard.md)

- [Jak zarządzać alertami w Azure Security Center](../security-center/security-center-managing-and-responding-alerts.md)

- [Jak ostrzec dane dziennika usługi log Analytics](../azure-monitor/learn/tutorial-response.md)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="28-centralize-anti-malware-logging"></a>2,8: scentralizowanie rejestrowania chroniącego przed złośliwym oprogramowaniem

**Wskazówki**: nie dotyczy; Azure Logic Apps nie przetwarza ani nie tworzy dzienników związanych z oprogramowaniem chroniącym przed złośliwym kodem.

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="29-enable-dns-query-logging"></a>2,9: Włączanie rejestrowania zapytań DNS

**Wskazówki**: nie dotyczy; Azure Logic Apps nie przetwarza ani nie tworzy dzienników związanych z usługą DNS.

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="210-enable-command-line-audit-logging"></a>2,10: Włączanie rejestrowania inspekcji w wierszu polecenia

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone do zasobów obliczeniowych.

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: nie dotyczy

## <a name="identity-and-access-control"></a>Tożsamość i kontrola dostępu

*Aby uzyskać więcej informacji, zobacz [test dotyczący zabezpieczeń platformy Azure: tożsamość i kontrola dostępu](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: obsługa spisu kont administracyjnych

**Wskazówki**: Azure Active Directory (AD) ma wbudowane role, które muszą być jawnie przypisane i są queryable. Za pomocą modułu Azure AD PowerShell można wykonywać zapytania ad hoc w celu odnajdywania kont należących do grup administracyjnych.

Aby łatwo uzyskać dostęp do innych zasobów chronionych przez usługę Azure Active Directory (Azure AD) i uwierzytelnić swoją tożsamość bez logowania, aplikacja logiki może używać tożsamości zarządzanej (dawniej tożsamość usługi zarządzanej lub MSI), a nie poświadczeń lub wpisów tajnych. Platforma Azure zarządza tą tożsamością i pomaga zabezpieczyć poświadczenia, ponieważ dzięki temu nie musisz dostarczać ani rotować wpisów tajnych.

Każdy punkt końcowy żądania w aplikacji logiki ma sygnaturę dostępu współdzielonego (SAS) w adresie URL punktu końcowego. Jeśli adres URL punktu końcowego jest współużytkowany dla wyzwalacza opartego na żądaniach innych stron, można wygenerować adresy URL wywołania zwrotnego, które używają określonych kluczy i mają daty wygaśnięcia. Dzięki temu można bezproblemowo rzutować klucze lub ograniczyć dostęp do wyzwalania aplikacji logiki na podstawie określonego przedziału czasu.

- [Jak uzyskać rolę katalogu w usłudze Azure AD przy użyciu programu PowerShell](/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

- [Uwierzytelnianie dostępu do zasobów platformy Azure przy użyciu tożsamości zarządzanych w programie Azure Logic Apps](create-managed-service-identity.md)

- [Jak uzyskać członków roli katalogu w usłudze Azure AD przy użyciu programu PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

- [Jak zabezpieczyć dostęp i dane w Azure Logic Apps przy użyciu sygnatury dostępu współdzielonego](logic-apps-securing-a-logic-app.md#sas)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="32-change-default-passwords-where-applicable"></a>3,2: Zmień domyślne hasła, jeśli ma to zastosowanie

**Wskazówki**: Azure Active Directory i Azure Logic Apps nie mają koncepcji domyślnych haseł.

W przypadku korzystania z uwierzytelniania podstawowego należy określić nazwę użytkownika i hasło. Podczas tworzenia tych poświadczeń upewnij się, że skonfigurowano silne hasło do uwierzytelniania.

Jeśli używasz infrastruktury jako kodu, unikaj przechowywania haseł w kodzie i zamiast tego użyj Azure Key Vault do przechowywania i pobierania poświadczeń.

- [Jak zabezpieczyć dane w Logic Apps i uzyskiwać do nich dostęp](logic-apps-securing-a-logic-app.md)

- [Jak ustawić i pobrać klucz tajny z Azure Key Vault](../key-vault/general/quick-create-portal.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: Użyj dedykowanych kont administracyjnych

**Wskazówki**: Tworzenie standardowych procedur operacyjnych dotyczących korzystania z dedykowanych kont administracyjnych. Użyj Azure Security Center Zarządzanie tożsamościami i dostępem, aby monitorować liczbę kont administracyjnych.

Ponadto, aby ułatwić śledzenie dedykowanych kont administracyjnych, można użyć zaleceń z Azure Security Center lub wbudowanych zasad platformy Azure, takich jak:

- Do subskrypcji powinien być przypisany więcej niż jeden właściciel
- Przestarzałe konta z uprawnieniami właściciela powinny zostać usunięte z subskrypcji
- Konta zewnętrzne z uprawnieniami właściciela powinny zostać usunięte z subskrypcji

- [Jak używać Azure Security Center do monitorowania tożsamości i dostępu (wersja zapoznawcza)](../security-center/security-center-identity-access.md)

- [Jak używać Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3,4: Użyj Azure Active Directory logowania jednokrotnego (SSO)

**Wskazówki**: Użyj rejestracji aplikacji platformy Azure (nazwy głównej usługi), aby pobrać token, który może służyć do współpracy z magazynami Recovery Services za pośrednictwem wywołań interfejsu API.

Wiele łączników wymaga również, aby najpierw utworzyć połączenie z usługą lub systemem docelowym oraz podać poświadczenia uwierzytelniania lub inne szczegóły konfiguracji, zanim będzie można użyć wyzwalacza lub akcji w aplikacji logiki. Na przykład trzeba autoryzować połączenie z kontem usługi Twitter w celu uzyskania dostępu do danych lub opublikowania w Twoim imieniu.

W przypadku łączników korzystających z Azure Active Directory (Azure AD) OAuth Tworzenie połączenia oznacza zalogowanie się do usługi, takiej jak Office 365, Salesforce lub GitHub, gdzie token dostępu jest szyfrowany i bezpiecznie przechowywany w magazynie kluczy tajnych platformy Azure. Inne łączniki, takie jak FTP i SQL, wymagają połączenia, które ma szczegóły konfiguracji, takie jak adres serwera, nazwa użytkownika i hasło. Te szczegóły konfiguracji połączenia są również szyfrowane i bezpiecznie przechowywane.

- [Jak wywołać interfejsy API REST platformy Azure](/rest/api/azure/#how-to-call-azure-rest-apis-with-postman)

- [Jak zarejestrować aplikację kliencką w usłudze Azure AD](/rest/api/azure/#register-your-client-application-with-azure-ad)

- [Informacje o interfejsie API wyzwalaczy przepływu pracy](/rest/api/logic/workflowtriggers)

- [Opis konfiguracji łącznika](../connectors/apis-list.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Użyj uwierzytelniania wieloskładnikowego dla wszystkich Azure Active Directory dostępu opartego na usłudze

**Wskazówki**: Włączanie Azure Active Directory (AD) Multi-Factor Authentication (MFA) i postępuj zgodnie z zaleceniami dotyczącymi zarządzania tożsamościami i dostępem Azure Security Center.

- [Jak włączyć usługę MFA na platformie Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Jak monitorować tożsamość i dostęp w Azure Security Center](../security-center/security-center-identity-access.md)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3,6: Korzystaj z bezpiecznych stacji roboczych zarządzanych przez platformę Azure na potrzeby zadań administracyjnych

**Wskazówki**: Użyj stacji roboczych dostępu uprzywilejowanego (dostępem uprzywilejowanym) z usługą Multi-Factor Authentication (MFA) skonfigurowaną w celu logowania się i konfigurowania zasobów platformy Azure.

- [Dowiedz się więcej o stacjach roboczych uprzywilejowanego dostępu](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Jak włączyć usługę MFA na platformie Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: Rejestruj i Ostrzegaj o podejrzanych działaniach z kont administracyjnych

**Wskazówki**: Użyj Azure Active Directory (AD) PRIVILEGED Identity Management (PIM) do generowania dzienników i alertów w przypadku wystąpienia podejrzanych lub niebezpiecznych działań w środowisku.

Ponadto za pomocą funkcji wykrywania ryzyka usługi Azure AD można wyświetlać alerty i raporty na temat ryzykownego zachowania użytkowników.

- [Jak wdrożyć Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [Omówienie wykrywania ryzyka usługi Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: zarządzanie zasobami platformy Azure tylko z zatwierdzonych lokalizacji

**Wskazówki**: Użyj dostępu warunkowego o nazwie Locations, aby zezwolić na dostęp do Azure Portal tylko z określonych logicznych grup zakresów lub krajów/regionów adresów IP.

Ponadto każdy punkt końcowy żądania w aplikacji logiki ma sygnaturę dostępu współdzielonego (SAS) w adresie URL punktu końcowego. Możesz ograniczyć aplikację logiki do akceptowania żądań tylko z określonych adresów IP.

- [Jak skonfigurować nazwane lokalizacje na platformie Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

- [Dowiedz się, jak ograniczyć przychodzące adresy IP w Logic Apps](logic-apps-securing-a-logic-app.md#restrict-inbound-ip-addresses)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="39-use-azure-active-directory"></a>3,9: Użyj Azure Active Directory

**Wskazówki**: Użyj Azure Active Directory (AD) jako centralnego systemu uwierzytelniania i autoryzacji dla wystąpień Azure Logic Apps. Usługa Azure AD chroni dane przy użyciu silnego szyfrowania danych przechowywanych i przesyłanych. Usługa Azure AD również Sole, skróty i bezpieczne przechowywanie poświadczeń użytkownika.

W przypadku pomocy technicznej w Logic Apps Użyj tożsamości zarządzanej, aby łatwo uzyskać dostęp do innych zasobów chronionych przez usługę Azure Active Directory (Azure AD) i uwierzytelnić swoją tożsamość bez logowania, a nie poświadczeń lub wpisów tajnych. Platforma Azure zarządza tą tożsamością i pomaga zabezpieczyć poświadczenia, ponieważ dzięki temu nie musisz dostarczać ani rotować wpisów tajnych.

Usługa Azure Logic Apps obsługuje zarówno tożsamości zarządzane przypisane przez system, jak i przez użytkownika. Aplikacja logiki może użyć tożsamości przypisanej przez system lub przez pojedynczego użytkownika, udostępnianej w grupie aplikacji logiki, ale nie obu tych tożsamości. Obecnie tylko określone wbudowane wyzwalacze i akcje obsługują tożsamości zarządzane, a nie zarządzane łączniki lub połączenia, na przykład:

-  HTTP

-  Azure Functions

-  Usługa Azure API Management

-  Azure App Services 

- [Jak utworzyć i skonfigurować wystąpienie usługi Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

- [Uwierzytelnianie dostępu do zasobów platformy Azure przy użyciu tożsamości zarządzanych w programie Azure Logic Apps](create-managed-service-identity.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: regularnie Przeglądaj i Uzgodnij dostęp użytkowników

**Wskazówki**: Azure Active Directory (AD) zawiera dzienniki ułatwiające odnajdywanie starych kont. Ponadto za pomocą przeglądów dostępu do tożsamości platformy Azure można efektywnie zarządzać członkostwem w grupach, dostępem do aplikacji dla przedsiębiorstw i przypisaniami ról. Dostęp użytkowników może być regularnie przeglądany, aby upewnić się, że tylko Ci użytkownicy mają ciągły dostęp. 

- [Informacje o raportowaniu usługi Azure AD](../active-directory/reports-monitoring/index.yml)

- [Jak korzystać z przeglądów dostępu do tożsamości platformy Azure](../active-directory/governance/access-reviews-overview.md)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: Monitor próbuje uzyskać dostęp do zdezaktywowanych poświadczeń

**Wskazówki**: Użyj Azure Active Directory (AD) jako centralnego systemu uwierzytelniania i autoryzacji dla wystąpień Azure Logic Apps. Usługa Azure AD chroni dane przy użyciu silnego szyfrowania danych przechowywanych i przesyłanych. Usługa Azure AD również Sole, skróty i bezpieczne przechowywanie poświadczeń użytkownika.

Masz dostęp do źródeł działań związanych z logowaniem do usługi Azure AD, inspekcją i ryzykiem dzienników zdarzeń, które umożliwiają integrację z platformą Azure, lub SIEM innych firm.

Proces ten można usprawnić, tworząc ustawienia diagnostyczne dla kont użytkowników usługi Azure AD i wysyłając dzienniki inspekcji i dzienniki logowania do obszaru roboczego Log Analytics. Żądane alerty dzienników można skonfigurować w ramach Log Analytics.

- [Jak zintegrować dzienniki aktywności platformy Azure z usługą Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Jak przejść do tablicy wskaźnikowej platformy Azure](../sentinel/quickstart-onboard.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: odchylenia zachowania alertu dotyczącego logowania na koncie

**Wskazówki**: Użyj funkcji ryzyka i ochrony tożsamości usługi Azure AD, aby skonfigurować automatyczne odpowiedzi na wykryte podejrzane działania związane z tożsamościami użytkowników. Możesz również pozyskać dane do usługi Azure wskaźnikowej na potrzeby dalszej analizy. 

- [Jak wyświetlić ryzykowne logowania w usłudze Azure AD](../active-directory/identity-protection/overview-identity-protection.md) 

- [Jak skonfigurować i włączyć zasady dotyczące ryzyka związanego z ochroną tożsamości](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md) 

- [Jak dołączyć wskaźnik na platformie Azure](../sentinel/quickstart-onboard.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: Zapewnij firmie Microsoft dostęp do odpowiednich danych klienta w scenariuszach pomocy technicznej

**Wskazówki**: obecnie niedostępne; Skrytka klienta nie jest jeszcze obsługiwana dla Azure Logic Apps.

- [Lista usług obsługiwanych przez Skrytka klienta](../security/fundamentals/customer-lockbox-overview.md#supported-services-and-scenarios-in-general-availability)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: nie dotyczy

## <a name="data-protection"></a>Ochrona danych

*Aby uzyskać więcej informacji, zobacz [Azure Security test: Data Protection](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: przechowywanie spisu poufnych informacji

**Wskazówki**: Użyj tagów, aby pomóc w śledzeniu zasobów platformy Azure, które przechowują lub przetwarzają informacje poufne.

- [Tworzenie i używanie tagów](../azure-resource-manager/management/tag-resources.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: Izoluj systemy przechowujące lub przetwarzające informacje poufne

**Wskazówki**: Łączniki działające w ramach usługi Logic Apps z wieloma dzierżawcami są wdrażane i zarządzane przez firmę Microsoft. Te łączniki udostępniają wyzwalacze i akcje umożliwiające dostęp do usług w chmurze, systemów lokalnych lub obu tych programów, w tym pakietów Office 365, Azure Blob Storage, SQL Server, Dynamics, Salesforce, SharePoint i innych.

W przypadku aplikacji logiki, które wymagają bezpośredniego dostępu do zasobów w sieci wirtualnej platformy Azure, można utworzyć środowisko usługi integracji (ISE), w którym można tworzyć, wdrażać i uruchamiać aplikacje logiki w dedykowanych zasobach. Niektóre sieci wirtualne platformy Azure używają prywatnych punktów końcowych (prywatnego linku platformy Azure) do zapewniania dostępu do usług PaaS platformy Azure, takich jak Azure Storage, Azure Cosmos DB lub Azure SQL Database, usług partnerskich lub usług klienta hostowanych na platformie Azure. Jeśli aplikacje logiki wymagają dostępu do sieci wirtualnych, które używają prywatnych punktów końcowych, należy utworzyć, wdrożyć i uruchomić te aplikacje logiki w ramach ISE.

Po utworzeniu ISE można użyć wewnętrznych lub zewnętrznych punktów końcowych dostępu. Wybór określa, czy wyzwalacze żądania lub elementu webhook w usłudze Logic Apps w ISE mogą odbierać wywołania spoza sieci wirtualnej.

Ponadto należy wdrożyć izolację przy użyciu osobnych subskrypcji i grup zarządzania dla poszczególnych domen zabezpieczeń, takich jak typ środowiska i poziom czułości danych. Możesz ograniczyć poziom dostępu do zasobów platformy Azure, których wymagają aplikacje i środowiska korporacyjne. Dostęp do zasobów platformy Azure można kontrolować za pośrednictwem kontroli dostępu opartej na rolach (Azure RBAC).

- [Omówienie łączników dla Logic Apps](../connectors/apis-list.md)

- [Dostęp do zasobów platformy Azure Virtual Network z Azure Logic Apps przy użyciu środowisk usługi integracji (ISEs)](connect-virtual-network-vnet-isolated-environment-overview.md)

- [Jak utworzyć dodatkowe subskrypcje platformy Azure](../cost-management-billing/manage/create-subscription.md) 

- [Jak utworzyć Grupy zarządzania](../governance/management-groups/create-management-group-portal.md) 

- [Tworzenie i używanie tagów](../azure-resource-manager/management/tag-resources.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: Monitoruj i blokuj nieautoryzowany transfer informacji poufnych

**Wskazówki**: obecnie niedostępne; funkcje identyfikacji, klasyfikacji i zapobiegania utracie danych nie są jeszcze dostępne dla Azure Logic Apps.

Skorzystaj z rozwiązania innej firmy w witrynie Azure Marketplace w sieci obwodowej, które monitoruje do nieautoryzowanego transferu poufnych informacji i blokuje takie transfery podczas powiadamiania specjalistów ds. bezpieczeństwa informacji. 

Firma Microsoft zarządza podstawową infrastrukturą dla Azure Logic Apps i ma zaimplementowane ścisłe kontrole, aby zapobiec utracie lub narażeniu danych klientów.

- [Informacje na temat ochrony danych klientów na platformie Azure](../security/fundamentals/protection-customer-data.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Współużytkowane

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: Szyfruj wszystkie poufne informacje podczas przesyłania

**Wskazówki**: Szyfruj wszystkie informacje poufne podczas przesyłania. W Azure Logic Apps wszystkie dane podczas uruchamiania aplikacji logiki są szyfrowane podczas przesyłania przy użyciu Transport Layer Security (TLS) i w spoczynku. Podczas wyświetlania historii uruchamiania aplikacji logiki usługa Logic Apps uwierzytelnia dostęp, a następnie dostarcza linki do wejść i wyjść dla żądań i odpowiedzi dla każdego przebiegu. Jednak w przypadku akcji, które obsługują hasła, wpisy tajne, klucze lub inne poufne informacje, chcesz uniemożliwić innym użytkownikom wyświetlanie tych danych i uzyskiwanie do nich dostępu. Na przykład jeśli aplikacja logiki pobiera wpis tajny z Azure Key Vault, który ma być używany podczas uwierzytelniania akcji HTTP, należy ukryć ten klucz tajny z widoku.

Wyzwalacz żądania obsługuje tylko Transport Layer Security (TLS) 1,2 dla żądań przychodzących. Upewnij się, że wszyscy klienci łączący się z zasobami platformy Azure mogą negocjować protokół TLS 1,2 lub nowszy. Wywołania wychodzące przy użyciu łącznika protokołu HTTP Transport Layer Security (TLS) 1,0, 1,1 i 1,2. 

Postępuj zgodnie z zaleceniami Azure Security Center na potrzeby szyfrowania w czasie spoczynku i szyfrowania podczas przesyłania, jeśli ma to zastosowanie.

- [Zabezpieczanie dostępu i danych w ramach wywołań Azure Logic Apps-przychodzących do wyzwalaczy opartych na żądaniach](logic-apps-securing-a-logic-app.md#secure-inbound-requests)

- [Zabezpieczanie dostępu i danych w ramach wywołań Azure Logic Apps wychodzących do innych usług i systemów](logic-apps-securing-a-logic-app.md#secure-outbound-requests)

- [Informacje o szyfrowaniu podczas przesyłania na platformę Azure](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit)

- [Zrozumienie szyfrowania danych na platformie Azure](../security/fundamentals/encryption-atrest.md)

- [Skonfiguruj klucze zarządzane przez klienta, aby szyfrować dane przechowywane w środowiskach usługi Integration Environment (ISEs) w Azure Logic Apps](customer-managed-keys-integration-service-environment.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Współużytkowane

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: Użyj aktywnego narzędzia do odnajdywania, aby identyfikować poufne dane

**Wskazówki**: w Azure Logic Apps wiele wyzwalaczy i akcji ma ustawienia, które można włączyć w celu zabezpieczenia danych wejściowych, wyjściowych lub obu, zasłaniając te dane z historii uruchamiania aplikacji logiki.

Firma Microsoft zarządza podstawową infrastrukturą dla Azure Logic Apps i ma zaimplementowane ścisłe kontrole, aby zapobiec utracie lub narażeniu danych klientów.

- [Bezpieczny dostęp do danych historii uruchamiania](logic-apps-securing-a-logic-app.md#access-to-run-history-data)

- [Informacje na temat ochrony danych klientów na platformie Azure](../security/fundamentals/protection-customer-data.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Współużytkowane

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: korzystanie z usługi Azure RBAC do kontrolowania dostępu do zasobów

**Wskazówki**: można zezwolić tylko określonym użytkownikom lub grupom na uruchamianie określonych zadań, takich jak zarządzanie, edytowanie i wyświetlanie aplikacji logiki. Aby kontrolować ich uprawnienia, użyj funkcji kontroli dostępu opartej na rolach (Azure RBAC) na platformie Azure, aby można było przypisywać niestandardowe lub wbudowane role do członków w ramach subskrypcji platformy Azure:

- Współautor aplikacji logiki: umożliwia zarządzanie aplikacjami logiki, ale nie pozwala na zmianę dostępu do nich.
- Operator aplikacji logiki: umożliwia odczytywanie, Włączanie i wyłączanie aplikacji logiki, ale nie można ich edytować ani aktualizować.

Aby uniemożliwić innym użytkownikom zmianę lub usunięcie aplikacji logiki, możesz użyć blokady zasobów platformy Azure. Ta funkcja uniemożliwia innym osobom zmianę lub usunięcie zasobów produkcyjnych.

- [Bezpieczny dostęp do Azure Logic Apps operacji](logic-apps-securing-a-logic-app.md#access-to-logic-app-operations)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: Wymuś kontrolę dostępu przy użyciu ochrony przed utratą danych opartą na hoście

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone do zasobów obliczeniowych. Firma Microsoft zarządza podstawową infrastrukturą dla Azure Logic Apps i ma zaimplementowane ścisłe kontrole, aby zapobiec utracie lub narażeniu danych klientów.

- [Ochrona danych klienta platformy Azure](../security/fundamentals/protection-customer-data.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Microsoft

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: Szyfruj poufne informacje w spoczynku

**Wskazówki**: Azure Logic Apps opiera się na usłudze Azure Storage do przechowywania i automatycznego szyfrowania danych. To szyfrowanie chroni dane i pomaga sprostać zobowiązaniom dotyczącym bezpieczeństwa i zgodności w organizacji. Domyślnie usługa Azure Storage używa kluczy zarządzanych przez firmę Microsoft do szyfrowania danych.

Podczas tworzenia środowiska usługi integracji (ISE) na potrzeby hostowania aplikacji logiki, a użytkownik chce mieć większą kontrolę nad kluczami szyfrowania używanymi przez usługę Azure Storage, można skonfigurować własny klucz, korzystać z niego i zarządzać nim przy użyciu Azure Key Vault. Ta funkcja jest również znana jako "Bring Your Own Key" (BYOK), a klucz jest nazywany "kluczem zarządzanym przez klienta".

- [Szyfruj dane przechowywane w środowiskach usługi integracji w Azure Logic Apps](customer-managed-keys-integration-service-environment.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: Rejestruj i Ostrzegaj o zmianach krytycznych zasobów platformy Azure

**Wskazówki**: Użyj Azure monitor z dziennikiem aktywności platformy Azure, aby utworzyć alerty dla momentu wprowadzenia zmian w Azure Logic Apps, jak również inne krytyczne lub pokrewne zasoby.

- [Jak utworzyć alerty dla zdarzeń dziennika aktywności platformy Azure](../azure-monitor/platform/alerts-activity-log.md)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

## <a name="vulnerability-management"></a>Zarządzanie lukami w zabezpieczeniach

*Aby uzyskać więcej informacji, zobacz temat [Azure Security test: Zarządzanie lukami w zabezpieczeniach](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: uruchamianie narzędzi do skanowania automatycznych luk w zabezpieczeniach

**Wskazówki**: nie dotyczy; Firma Microsoft przeprowadza zarządzanie lukami w systemach podstawowych, które obsługują Azure Logic Apps.

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: Wdróż automatyczne rozwiązanie do zarządzania poprawkami systemu operacyjnego

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone do zasobów obliczeniowych.

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5,3: Wdróż rozwiązanie zautomatyzowanego zarządzania poprawkami dla tytułów oprogramowania innych firm

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone do zasobów obliczeniowych.

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: porównanie luk w zabezpieczeniach z tyłu do tyłu

**Wskazówki**: nie dotyczy; Firma Microsoft przeprowadza zarządzanie lukami w systemach podstawowych, które obsługują Azure Logic Apps.

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: Użyj procesu oceny ryzyka, aby określić priorytety korygowania odkrytych luk w zabezpieczeniach

**Wskazówki**: nie dotyczy; Firma Microsoft przeprowadza zarządzanie lukami w systemach podstawowych, które obsługują Azure Logic Apps.

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: nie dotyczy

## <a name="inventory-and-asset-management"></a>Zarządzanie magazynem i zasobami

*Aby uzyskać więcej informacji, zobacz temat [Azure Security test: Inventory and Asset Management](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: Użyj rozwiązania automatycznego odnajdywania zasobów

**Wskazówki**: Użyj grafu zasobów platformy Azure do wykonywania zapytań/odnajdywania wszystkich zasobów (takich jak obliczenia, magazyn, Sieć, porty i protokoły itp.) w ramach subskrypcji.  Upewnij się, że masz odpowiednie uprawnienia (odczyt) w dzierżawie i Wylicz wszystkie subskrypcje platformy Azure oraz zasoby w ramach subskrypcji.

Mimo że klasyczne zasoby platformy Azure mogą zostać odnalezione za pośrednictwem grafu zasobów, zdecydowanie zaleca się tworzenie i używanie Azure Resource Manager zasobów do przodu.

- [Jak tworzyć zapytania przy użyciu grafu zasobów platformy Azure](../governance/resource-graph/first-query-portal.md)

- [Jak wyświetlić subskrypcje platformy Azure](/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

- [Opis kontroli RBAC platformy Azure](../role-based-access-control/overview.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="62-maintain-asset-metadata"></a>6,2: Konserwowanie metadanych zasobów

**Wskazówki**: stosowanie tagów do zasobów platformy Azure, dzięki czemu metadane są logicznie zorganizowane w taksonomię.

- [Tworzenie i używanie tagów](../azure-resource-manager/management/tag-resources.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: Usuń nieautoryzowane zasoby platformy Azure

**Wskazówki**: używanie tagowania, grup zarządzania i oddzielnych subskrypcji, gdzie to konieczne, do organizowania i śledzenia zasobów platformy Azure. Regularnie Uzgadniaj spis i zapewnij, że nieautoryzowane zasoby są usuwane z subskrypcji w odpowiednim czasie.

Ponadto należy używać Azure Policy do umieszczania ograniczeń dotyczących typu zasobów, które można utworzyć w subskrypcjach klientów, przy użyciu następujących wbudowanych definicji zasad:

- Niedozwolone typy zasobów
- Dozwolone typy zasobów

- [Jak utworzyć dodatkowe subskrypcje platformy Azure](../cost-management-billing/manage/create-subscription.md)

- [Jak utworzyć Grupy zarządzania](../governance/management-groups/create-management-group-portal.md)

- [Tworzenie i używanie tagów](../azure-resource-manager/management/tag-resources.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6,4: Definiowanie i konserwowanie spisu zatwierdzonych zasobów platformy Azure

**Wskazówki**: Tworzenie spisu zatwierdzonych zasobów platformy Azure (takich jak łączniki) oraz zatwierdzone oprogramowanie do zasobów obliczeniowych zgodnie z potrzebami organizacji.

Uwaga: ze względu na dane i zasady zachowania poufności informacji firmy Google możesz użyć łącznika usługi Gmail tylko z usługami zatwierdzonymi przez firmę Google. Ta sytuacja jest rozwijająca się i może mieć wpływ na inne łączniki Google w przyszłości.

- [Lista wszystkich łączników Logic Apps](/connectors/connector-reference/connector-reference-logicapps-connectors)

- [Informacje o problemach i ograniczeniach dotyczących łączników usługi Gmail](/connectors/gmail/#known-issues-and-limitations)

- [Więcej informacji na temat zasad ochrony prywatności firmy Google](../connectors/connectors-google-data-security-privacy-policy.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: Monitoruj niezatwierdzone zasoby platformy Azure

**Wskazówki**: Użyj Azure Policy, aby umieścić ograniczenia dotyczące typu zasobów, które można utworzyć w ramach subskrypcji. 

Użyj grafu zasobów platformy Azure do wykonywania zapytań/odnajdywania zasobów w ramach subskrypcji.  Upewnij się, że wszystkie zasoby platformy Azure obecne w środowisku są zatwierdzone.

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

- [Jak tworzyć zapytania przy użyciu grafu zasobów platformy Azure](../governance/resource-graph/first-query-portal.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: Monitoruj niezatwierdzone aplikacje oprogramowania w ramach zasobów obliczeniowych

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone do zasobów obliczeniowych.

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: Usuń niezatwierdzone zasoby platformy Azure i aplikacje oprogramowania

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone do zasobów obliczeniowych.

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="68-use-only-approved-applications"></a>6,8: Używaj tylko zatwierdzonych aplikacji

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone do zasobów obliczeniowych.

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="69-use-only-approved-azure-services"></a>6,9: Używaj tylko zatwierdzonych usług platformy Azure

**Wskazówki**: Użyj Azure Policy, aby wprowadzić ograniczenia dotyczące typu zasobów, które można utworzyć w subskrypcjach klientów, przy użyciu następujących wbudowanych definicji zasad:

- Niedozwolone typy zasobów
- Dozwolone typy zasobów

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

- [Jak odmówić określonego typu zasobu za pomocą Azure Policy](../governance/policy/samples/index.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6,10: przechowywanie spisu zatwierdzonych tytułów oprogramowania

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone do zasobów obliczeniowych.

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: Ogranicz możliwość korzystania przez użytkowników z Azure Resource Manager

**Wskazówki**: Skonfiguruj dostęp warunkowy platformy Azure, aby ograniczyć możliwość korzystania przez użytkowników z Azure Resource Manager przez skonfigurowanie "blokowania dostępu" dla aplikacji "Microsoft Azure Management".

- [Jak skonfigurować dostęp warunkowy w celu blokowania dostępu do Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6,12: Ogranicz możliwość wykonywania skryptów w zasobach obliczeniowych przez użytkowników

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone do zasobów obliczeniowych.

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: fizyczne lub logiczne rozdzielenie aplikacji wysokiego ryzyka

**Wskazówki**: zasoby związane z Logic Appsami, które są wymagane do działania biznesowego, ale mogą powodować większe ryzyko dla organizacji, powinny być izolowane w ramach własnej maszyny wirtualnej i/lub sieci wirtualnej i dostatecznie zabezpieczone za pomocą zapory platformy Azure lub sieciowej grupy zabezpieczeń.

Logic Apps, które są wymagane do działania biznesowego, ale mogą powodować większe ryzyko dla organizacji, powinny być izolowane wszędzie tam, gdzie jest to możliwe za pośrednictwem odrębnych grup zasobów z określonymi uprawnieniami i granicami RBAC platformy Azure.

- [Jak utworzyć sieć wirtualną](../virtual-network/quick-create-portal.md) 

- [Jak utworzyć sieciowej grupy zabezpieczeń z konfiguracją zabezpieczeń](../virtual-network/tutorial-filter-network-traffic.md)

- [Jak utworzyć Grupy zarządzania](../governance/management-groups/create-management-group-portal.md) 

- [Jak zabezpieczyć dostęp do Logic Apps za pośrednictwem usługi Azure RBAC](logic-apps-securing-a-logic-app.md#access-to-logic-app-operations)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

## <a name="secure-configuration"></a>Bezpieczna konfiguracja

*Aby uzyskać więcej informacji, zobacz temat [Azure Security test: bezpieczna konfiguracja](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: Ustanów bezpieczne konfiguracje dla wszystkich zasobów platformy Azure

**Wskazówki**: Definiowanie i implementowanie standardowych konfiguracji zabezpieczeń dla wystąpień Azure Logic Apps przy użyciu Azure Policy. Użyj aliasów Azure Policy w przestrzeni nazw "Microsoft. Logic", aby utworzyć zasady niestandardowe do inspekcji lub wymuszania konfiguracji wystąpień Logic Apps. Można na przykład zablokować innym użytkownikom możliwość tworzenia lub używania połączeń z zasobami, w których chcesz ograniczyć dostęp.

Ponadto Azure Resource Manager ma możliwość eksportowania szablonu w JavaScript Object Notation (JSON), który powinien zostać sprawdzony, aby upewnić się, że konfiguracje spełniają/przekroczą wymagania dotyczące zabezpieczeń w organizacji.

Ponadto należy używać zabezpieczonych parametrów do ochrony poufnych danych i wpisów tajnych.

- [Jak wyświetlić dostępne aliasy Azure Policy](/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

- [Blokuj połączenia utworzone przez łączniki w Azure Logic Apps](block-connections-connectors.md)

- [Eksport jednego i kilku zasobów do szablonu w Azure Portal](../azure-resource-manager/templates/export-template-portal.md)

- [Jak wdrożyć szablony Azure Resource Manager dla Azure Logic Apps](logic-apps-deploy-azure-resource-manager-templates.md)

- [Opis bezpiecznych parametrów akcji](logic-apps-securing-a-logic-app.md#secure-action-parameters)

- [Zalecenia dotyczące zabezpieczeń parametrów](../azure-resource-manager/templates/template-best-practices.md#security-recommendations-for-parameters)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: Ustanów bezpieczne konfiguracje systemów operacyjnych

**Wskazówki**: nie dotyczy; te wytyczne są przeznaczone dla zasobów obliczeniowych.

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: obsługa bezpiecznych konfiguracji zasobów platformy Azure

**Wskazówki**: Użyj Azure Policy [Odmów] i [Wdróż, jeśli nie istnieje], aby wymusić bezpieczne ustawienia dla zasobów platformy Azure.

Zdefiniuj i Implementuj standardowe konfiguracje zabezpieczeń dla wystąpień Azure Logic Apps przy użyciu Azure Policy. Użyj aliasów Azure Policy w przestrzeni nazw "Microsoft. Logic", aby utworzyć zasady niestandardowe do inspekcji lub wymuszania konfiguracji wystąpień Logic Apps. Można na przykład zablokować innym użytkownikom możliwość tworzenia lub używania połączeń z zasobami, w których chcesz ograniczyć dostęp.

Ponadto Azure Resource Manager ma możliwość eksportowania szablonu w JavaScript Object Notation (JSON), który powinien zostać sprawdzony, aby upewnić się, że konfiguracje spełniają/przekroczą wymagania dotyczące zabezpieczeń w organizacji.

Ponadto należy się upewnić, że dane w historii uruchamiania są zabezpieczane przy użyciu zaciemniania.

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

- [Zrozumienie efektów Azure Policy](../governance/policy/concepts/effects.md)

- [Blokuj połączenia utworzone przez łączniki w Azure Logic Apps](block-connections-connectors.md)

- [Eksport jednego i kilku zasobów do szablonu w Azure Portal](../azure-resource-manager/templates/export-template-portal.md)

- [Jak wdrożyć szablony Azure Resource Manager dla Azure Logic Apps](logic-apps-deploy-azure-resource-manager-templates.md)

- [Bezpieczny dostęp do danych wejściowych i wyjściowych historii uruchamiania](logic-apps-securing-a-logic-app.md#obfuscate)

- [Bezpieczny dostęp do danych wejściowych parametrów](logic-apps-securing-a-logic-app.md#secure-action-parameters)

- [Zalecenia dotyczące zabezpieczeń parametrów](../azure-resource-manager/templates/template-best-practices.md#security-recommendations-for-parameters)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: Zachowaj konfiguracje bezpiecznego systemu operacyjnego

**Wskazówki**: nie dotyczy; te wytyczne są przeznaczone dla zasobów obliczeniowych.

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Współużytkowane

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: bezpiecznie przechowuj konfigurację zasobów platformy Azure

**Wskazówki**: Jeśli używasz niestandardowych definicji Azure Policy, Użyj usługi Azure DevOps lub Azure Repos, aby bezpiecznie przechowywać kod i zarządzać nim.

Ponadto Azure Resource Manager ma możliwość eksportowania szablonu w JavaScript Object Notation (JSON), który powinien zostać sprawdzony, aby upewnić się, że konfiguracje spełniają/przekroczą wymagania dotyczące zabezpieczeń w organizacji.

- [Jak przechowywać kod w usłudze Azure DevOps](/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [Dokumentacja Azure Repos](/azure/devops/repos/index?view=azure-devops)

- [Eksport jednego i kilku zasobów do szablonu w Azure Portal](../azure-resource-manager/templates/export-template-portal.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: bezpieczne przechowywanie niestandardowych obrazów systemu operacyjnego

**Wskazówki**: nie dotyczy; te wytyczne są przeznaczone dla zasobów obliczeniowych.

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: Wdrażanie narzędzi do zarządzania konfiguracją dla zasobów platformy Azure

**Wskazówki**: Użyj wbudowanych definicji Azure Policy, a także aliasów Azure Policy w przestrzeni nazw "Microsoft. Logic", aby utworzyć zasady niestandardowe na potrzeby alertów, inspekcji i wymuszania konfiguracji systemu. Użyj aliasów Azure Policy, aby utworzyć zasady niestandardowe do inspekcji lub wymuszania konfiguracji sieci zasobów platformy Azure. Dodatkowo opracowuj proces i potok na potrzeby zarządzania wyjątkami zasad.

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7,8: Wdrażanie narzędzi do zarządzania konfiguracją dla systemów operacyjnych

**Wskazówki**: nie dotyczy; te wytyczne są przeznaczone dla zasobów obliczeniowych.

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: Zaimplementuj automatyczne monitorowanie konfiguracji dla zasobów platformy Azure

**Wskazówki**: Użyj wbudowanych definicji Azure Policy, a także aliasów Azure Policy w przestrzeni nazw "Microsoft. Logic", aby utworzyć zasady niestandardowe na potrzeby alertów, inspekcji i wymuszania konfiguracji systemu. Użyj Azure Policy [Audit], [Odmów] i [Wdróż, jeśli nie istnieje], aby automatycznie wymuszać konfiguracje dla zasobów platformy Azure.

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: Zaimplementuj automatyczne monitorowanie konfiguracji dla systemów operacyjnych

**Wskazówki**: nie dotyczy; te wytyczne są przeznaczone dla zasobów obliczeniowych.

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="711-manage-azure-secrets-securely"></a>7,11: bezpieczne zarządzanie wpisami tajnymi platformy Azure

**Wskazówki**: Zabezpiecz dane wejściowe i wyjściowe w historii uruchamiania aplikacji logiki przy użyciu zaciemniania. W przypadku wdrażania w różnych środowiskach należy rozważyć parametryzacja wartości w definicji przepływu pracy aplikacji logiki, która różni się w zależności od tych środowisk. Dzięki temu można uniknąć zakodowanych danych przy użyciu szablonu Azure Resource Manager, aby wdrożyć aplikację logiki, chronić poufne dane przez zdefiniowanie zabezpieczonych parametrów i przekazać te dane jako osobny dane wejściowe za pomocą parametrów szablonu przy użyciu pliku parametrów. Key Vault do przechowywania danych poufnych i używania zabezpieczonych parametrów szablonu, które pobierają te wartości z Key Vault podczas wdrażania. Następnie można odwołać się do magazynu kluczy i wpisów tajnych w pliku parametrów. 

Podczas tworzenia środowiska usługi integracji (ISE) na potrzeby hostowania aplikacji logiki, a użytkownik chce mieć większą kontrolę nad kluczami szyfrowania używanymi przez usługę Azure Storage, można skonfigurować własny klucz, korzystać z niego i zarządzać nim przy użyciu Azure Key Vault. Ta funkcja jest również znana jako "Bring Your Own Key" (BYOK), a klucz jest nazywany "kluczem zarządzanym przez klienta".

- [Zabezpiecz dane wejściowe i wyjściowe w historii uruchamiania w Azure Logic Apps](logic-apps-securing-a-logic-app.md#obfuscate)

- [Zalecenia dotyczące zabezpieczeń parametrów](../azure-resource-manager/templates/template-best-practices.md#security-recommendations-for-parameters)

- [Bezpieczny dostęp do danych wejściowych parametrów w Azure Logic Apps](logic-apps-securing-a-logic-app.md#access-to-parameter-inputs)

- [Przekazuj bezpieczne wartości parametrów podczas wdrażania przy użyciu Azure Key Vault](../azure-resource-manager/templates/key-vault-parameter.md)

- [Skonfiguruj klucze zarządzane przez klienta, aby szyfrować dane przechowywane w środowiskach usługi Integration Environment (ISEs) w Azure Logic Apps](customer-managed-keys-integration-service-environment.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: bezpieczne i automatyczne zarządzanie tożsamościami

**Wskazówki**: aby łatwo uzyskać dostęp do innych zasobów chronionych przez usługę Azure Active Directory (Azure AD) i uwierzytelnić swoją tożsamość bez logowania, aplikacja logiki może używać tożsamości zarządzanej (dawniej tożsamość usługi ZARZĄDZANEJ lub MSI), a nie poświadczeń lub wpisów tajnych. Platforma Azure zarządza tą tożsamością i pomaga zabezpieczyć poświadczenia, ponieważ dzięki temu nie musisz dostarczać ani rotować wpisów tajnych.

Obecnie tylko określone wbudowane wyzwalacze i akcje obsługują tożsamości zarządzane, a nie zarządzane łączniki lub połączenia, na przykład:

- HTTP
- Azure Functions
- Usługa Azure API Management
- Azure App Services

- [Jak uwierzytelniać dostęp do zasobów platformy Azure przy użyciu tożsamości zarządzanych w programie Azure Logic Apps](create-managed-service-identity.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: eliminowanie nieprzewidzianego narażenia na poświadczenia

**Wskazówki**: Zabezpiecz dane wejściowe i wyjściowe w historii uruchamiania aplikacji logiki przy użyciu zaciemniania. W przypadku wdrażania w różnych środowiskach należy rozważyć parametryzacja wartości w definicji przepływu pracy aplikacji logiki, która różni się w zależności od tych środowisk. Dzięki temu można uniknąć zakodowanych danych przy użyciu szablonu Azure Resource Manager, aby wdrożyć aplikację logiki, chronić poufne dane przez zdefiniowanie zabezpieczonych parametrów i przekazać te dane jako osobny dane wejściowe za pomocą parametrów szablonu przy użyciu pliku parametrów. Key Vault do przechowywania danych poufnych i używania zabezpieczonych parametrów szablonu, które pobierają te wartości z Key Vault podczas wdrażania. Następnie można odwołać się do magazynu kluczy i wpisów tajnych w pliku parametrów. 

Możesz również zaimplementować skaner poświadczeń, aby identyfikować poświadczenia w kodzie. Skaner poświadczeń ułatwia również przenoszenie odnalezionych poświadczeń do bezpieczniejszych lokalizacji, takich jak usługa Azure Key Vault. 

- [Zabezpiecz dane wejściowe i wyjściowe w historii uruchamiania w Azure Logic Apps](logic-apps-securing-a-logic-app.md#obfuscate)

- [Zalecenia dotyczące zabezpieczeń parametrów](../azure-resource-manager/templates/template-best-practices.md#security-recommendations-for-parameters)

- [Bezpieczny dostęp do danych wejściowych parametrów w Azure Logic Apps](logic-apps-securing-a-logic-app.md#access-to-parameter-inputs)

- [Przekazuj bezpieczne wartości parametrów podczas wdrażania przy użyciu Azure Key Vault](../azure-resource-manager/templates/key-vault-parameter.md)

- [Jak skonfigurować skaner poświadczeń](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

## <a name="malware-defense"></a>Ochrona przed złośliwym oprogramowaniem

*Aby uzyskać więcej informacji, zobacz temat [Azure Security test: Obrona złośliwego oprogramowania](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8,1: Użyj centralnie zarządzanego oprogramowania chroniącego przed złośliwym oprogramowaniem

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone do zasobów obliczeniowych. Oprogramowanie chroniące przed złośliwym oprogramowaniem firmy Microsoft jest włączone na podstawowym hoście, który obsługuje usługi platformy Azure (na przykład Azure Logic Apps), ale nie jest uruchamiane w treści klienta.

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: przeskanuj pliki przed przekazaniem do zasobów platformy Azure, które nie są obliczeniowe

**Wskazówki**: oprogramowanie chroniące przed złośliwym oprogramowaniem firmy Microsoft jest włączone na podstawowym hoście obsługującym usługi platformy Azure (na przykład Azure Backup), ale nie jest ono uruchamiane w Twojej zawartości. 

Skanuj wstępnie wszystkie pliki przekazywane do zasobów platformy Azure, które nie są obliczeniowe, takie jak App Service, Data Lake Storage, Blob Storage itd. 

Użyj wykrywania zagrożeń Azure Security Center dla usług danych w celu wykrywania złośliwego oprogramowania przekazanego do kont magazynu. 

- [Informacje na temat ochrony przed złośliwym oprogramowaniem firmy Microsoft dla platformy Azure Cloud Services i Virtual Machines](../security/fundamentals/antimalware.md)

- [Zrozumienie wykrywania zagrożeń Azure Security Center dla usług danych](../security-center/azure-defender.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8,3: Upewnij się, że oprogramowanie chroniące przed złośliwym oprogramowaniem i podpisy zostały zaktualizowane

**Wskazówki**: nie dotyczy; te wytyczne są przeznaczone dla zasobów obliczeniowych.

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

## <a name="data-recovery"></a>Odzyskiwanie danych

*Aby uzyskać więcej informacji, zobacz [test dotyczący zabezpieczeń platformy Azure: odzyskiwanie danych](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: zapewnianie regularnych zautomatyzowanych kopii zapasowych

**Wskazówki**: Implementowanie rozwiązania odzyskiwania po awarii (Dr) na miejscu, dzięki któremu można chronić dane, szybko przywracać zasoby, które obsługują krytyczne funkcje biznesowe, i utrzymywać działania, aby zachować ciągłość biznesową (BC).

Ta strategia odzyskiwania po awarii koncentruje się na konfigurowaniu podstawowej aplikacji logiki w celu przełączenia w tryb failover lub w aplikacji logiki tworzenia kopii zapasowej w lokalizacji alternatywnej, w której jest również dostępna Azure Logic Apps. W ten sposób, jeśli podstawowa pogorszy się straty, zakłócenia lub niepowodzenia, pomocnicza może potrwać pracę. Ta strategia wymaga, aby pomocnicza aplikacja logiki i zasoby zależne zostały już wdrożone i przygotowane w lokalizacji alternatywnej.

Ponadto należy rozszerzyć podstawową definicję przepływu pracy aplikacji logiki do szablonu Azure Resource Manager. Ten szablon definiuje infrastrukturę, zasoby, parametry i inne informacje o aprowizacji i wdrażaniu aplikacji logiki.

- [Dowiedz się więcej na temat ciągłości działania i odzyskiwania po awarii dla Azure Logic Apps](business-continuity-disaster-recovery-guidance.md)

- [Jak zautomatyzować wdrażanie Azure Logic Apps przy użyciu szablonów Azure Resource Manager](logic-apps-azure-resource-manager-templates-overview.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: wykonaj kompletne kopie zapasowe systemu i Utwórz kopię zapasową wszystkich kluczy zarządzanych przez klienta

**Wskazówki**: Implementowanie rozwiązania odzyskiwania po awarii (Dr) na miejscu, dzięki któremu można chronić dane, szybko przywracać zasoby, które obsługują krytyczne funkcje biznesowe, i utrzymywać działania, aby zachować ciągłość biznesową (BC).

Ta strategia odzyskiwania po awarii koncentruje się na konfigurowaniu podstawowej aplikacji logiki w celu przełączenia w tryb failover lub w aplikacji logiki tworzenia kopii zapasowej w lokalizacji alternatywnej, w której jest również dostępna Azure Logic Apps. W ten sposób, jeśli podstawowa pogorszy się straty, zakłócenia lub niepowodzenia, pomocnicza może potrwać pracę. Ta strategia wymaga, aby pomocnicza aplikacja logiki i zasoby zależne zostały już wdrożone i przygotowane w lokalizacji alternatywnej.

Ponadto należy rozszerzyć podstawową definicję przepływu pracy aplikacji logiki do szablonu Azure Resource Manager. Ten szablon definiuje infrastrukturę, zasoby, parametry i inne informacje o aprowizacji i wdrażaniu aplikacji logiki.

Każdy punkt końcowy żądania w aplikacji logiki ma sygnaturę dostępu współdzielonego (SAS) w adresie URL punktu końcowego. Jeśli używasz Azure Key Vault do przechowywania wpisów tajnych, Zapewnij regularne automatyczne tworzenie kopii zapasowych kluczy i adresów URL.

- [Dowiedz się więcej na temat ciągłości działania i odzyskiwania po awarii dla Azure Logic Apps](business-continuity-disaster-recovery-guidance.md)

- [Automatyzowanie wdrażania Azure Logic Apps przy użyciu szablonów Azure Resource Manager](logic-apps-azure-resource-manager-templates-overview.md)

- [Bezpieczny dostęp i dane w Azure Logic Apps przy użyciu sygnatury dostępu współdzielonego](logic-apps-securing-a-logic-app.md#sas)

- [Jak utworzyć kopię zapasową kluczy Key Vault](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: Weryfikuj wszystkie kopie zapasowe, w tym klucze zarządzane przez klienta

**Wskazówki**: Strategia odzyskiwania po awarii powinna skupić się na konfigurowaniu podstawowej aplikacji logiki w celu przełączenia w tryb failover do aplikacji logiki w stanie wstrzymania lub tworzenia kopii zapasowej w lokalizacji alternatywnej, w której Azure Logic Apps jest również dostępna. W ten sposób, jeśli podstawowa pogorszy się straty, zakłócenia lub niepowodzenia, pomocnicza może potrwać pracę. Ta strategia wymaga, aby pomocnicza aplikacja logiki i zasoby zależne zostały już wdrożone i przygotowane w lokalizacji alternatywnej.

Testowanie przywracania kopii zapasowych kluczy zarządzanych przez klienta. Należy pamiętać, że dotyczy to Logic Apps działających tylko w środowiskach usługi Integration Environment (ISE).

- [Dowiedz się więcej na temat ciągłości działania i odzyskiwania po awarii dla Azure Logic Apps](business-continuity-disaster-recovery-guidance.md)

- [Skonfiguruj klucze zarządzane przez klienta, aby szyfrować dane przechowywane w środowiskach usługi Integration Environment (ISEs) w Azure Logic Apps](customer-managed-keys-integration-service-environment.md)

- [Jak przywrócić klucze magazynu kluczy na platformie Azure](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: zapewnianie ochrony kopii zapasowych i kluczy zarządzanych przez klienta

**Wskazówki**: Strategia odzyskiwania po awarii powinna skupić się na konfigurowaniu podstawowej aplikacji logiki w celu przełączenia w tryb failover do aplikacji logiki w stanie wstrzymania lub tworzenia kopii zapasowej w lokalizacji alternatywnej, w której Azure Logic Apps jest również dostępna. W ten sposób, jeśli podstawowa pogorszy się straty, zakłócenia lub niepowodzenia, pomocnicza może potrwać pracę. Ta strategia wymaga, aby pomocnicza aplikacja logiki i zasoby zależne zostały już wdrożone i przygotowane w lokalizacji alternatywnej. 

Ochrona kopii zapasowych kluczy zarządzanych przez klienta. Należy pamiętać, że dotyczy to Logic Apps działających tylko w środowiskach usługi Integration Environment (ISE).

Włącz ochronę Soft-Delete i przeczyszczania w Key Vault, aby chronić klucze przed przypadkowym lub złośliwym usunięciem.

- [Dowiedz się więcej na temat ciągłości działania i odzyskiwania po awarii dla Azure Logic Apps](business-continuity-disaster-recovery-guidance.md)

- [Skonfiguruj klucze zarządzane przez klienta, aby szyfrować dane przechowywane w środowiskach usługi Integration Environment (ISEs) w Azure Logic Apps](customer-managed-keys-integration-service-environment.md)

- [Jak włączyć ochronę Soft-Delete i przeczyszczania w programie Key Vault](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

## <a name="incident-response"></a>Reagowanie na zdarzenia

*Aby uzyskać więcej informacji, zobacz temat [Azure Security test: odpowiedź na zdarzenia](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: Tworzenie przewodnika odpowiedzi na zdarzenia

**Wskazówka**: Utwórz przewodnik odpowiedzi na zdarzenia dla swojej organizacji. Upewnij się, że istnieją zarejestrowane plany reakcji na zdarzenia, które definiują wszystkie role pracowników, a także etapy obsługi zdarzeń/zarządzania od wykrywania do oceny po zdarzeniu. 

- [Wskazówki dotyczące tworzenia własnego procesu reagowania na zdarzenia zabezpieczeń](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Anatomia incydentu centrum Microsoft Security Response](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [Skorzystaj z przewodnika obsługi zdarzeń związanych z bezpieczeństwem programu NIST, aby pomóc w tworzeniu własnego planu reagowania na zdarzenia](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: Tworzenie oceny incydentu i procedury priorytetyzacji

**Wskazówki**: Security Center przypisuje ważność do każdego alertu, aby pomóc w ustaleniu, które alerty należy najpierw zbadać. Ważność jest oparta na tym, jak dobrze Security Center znajduje się w wyszukiwaniu lub analitycznym używanym do wystawiania alertu, a także poziom pewności, że istniało złośliwy wpływ na działanie, które prowadziło do alertu. 

Dodatkowo jasno Oznacz subskrypcje (na przykład Produkcja, inne niż prod) przy użyciu tagów i Utwórz system nazewnictwa, aby jasno identyfikować i klasyfikować zasoby platformy Azure, szczególnie te, które przetwarzają dane poufne.  Odpowiedzialność za korygowanie alertów w oparciu o krytyczne znaczenie zasobów platformy Azure i środowisko, w którym wystąpiło zdarzenie, leży po stronie użytkownika.

- [Alerty zabezpieczeń w Centrum zabezpieczeń Azure](../security-center/security-center-alerts-overview.md)

- [Organizowanie zasobów platformy Azure przy użyciu tagów](../azure-resource-manager/management/tag-resources.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="103-test-security-response-procedures"></a>10,3: procedury odpowiedzi na zabezpieczenia testowe

**Wskazówki**: przeprowadzanie ćwiczeń w celu przetestowania możliwości reagowania na zdarzenia systemów w regularnych erzeach w celu ochrony zasobów platformy Azure. Zidentyfikuj słabe punkty i przerwy oraz popraw plan zgodnie z wymaganiami.

- [Przewodnik po publikacji NIST, który umożliwia testowanie, uczenie i wykonywanie programów dla planów i możliwości IT](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: Podaj szczegóły kontaktu dotyczącego zabezpieczeń i Skonfiguruj powiadomienia dotyczące alertów dotyczących zdarzeń związanych z zabezpieczeniami

**Wskazówki**: informacje kontaktowe dotyczące zdarzenia zabezpieczeń będą używane przez firmę Microsoft do skontaktowania się z Tobą, jeśli firma Microsoft Security Response Center (MSRC) wykryje, że dostęp do danych zostały nadane przez nielegalną lub nieautoryzowaną stronę. Przejrzyj zdarzenia po fakcie, aby upewnić się, że problemy zostały rozwiązane.

- [Jak ustawić kontakt z zabezpieczeniami Azure Security Center](../security-center/security-center-provide-security-contact-details.md)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: Uwzględnij alerty zabezpieczeń w systemie odpowiedzi na zdarzenia

**Wskazówki**: eksportowanie alertów i zaleceń dotyczących Azure Security Center przy użyciu funkcji eksportu ciągłego, która pomaga identyfikować zagrożenia dla zasobów platformy Azure. Eksport ciągły umożliwia wyeksportowanie alertów i zaleceń ręcznie lub w stały sposób ciągły. Możesz użyć łącznika danych Azure Security Center do przesyłania strumieniowego alertów do usługi Azure wskaźnikowej.

- [Jak skonfigurować eksport ciągły](../security-center/continuous-export.md)

- [Jak przesłać strumieniowo alerty do usługi Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: Automatyzowanie odpowiedzi na alerty zabezpieczeń

**Wskazówki**: Użyj funkcji automatyzacji przepływu pracy w Azure Security Center, aby automatycznie wyzwalać odpowiedzi poprzez "Logic Apps" dotyczące alertów zabezpieczeń i zaleceń dotyczących ochrony zasobów platformy Azure.

- [Jak skonfigurować automatyzację przepływu pracy i Logic Apps](../security-center/workflow-automation.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

## <a name="penetration-tests-and-red-team-exercises"></a>Testy penetracyjne i ćwiczenia typu „red team”

*Aby uzyskać więcej informacji, zobacz test [porównawczy zabezpieczeń platformy Azure: testy penetracji i czerwone ćwiczenia zespołu](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: Przeprowadź regularne testowanie penetracji zasobów platformy Azure i zadbaj o skorygowanie wszystkich krytycznych ustaleń dotyczących zabezpieczeń

**Wskazówki**: Postępuj zgodnie z zasadami firmy Microsoft dotyczącymi zaangażowania, aby upewnić się, że testy penetracji nie naruszają zasad firmy Microsoft. Korzystaj z strategii firmy Microsoft i wykonywania testów na żywo z obsługą tworzenia zespołu, usług i aplikacji w chmurze, które są zarządzane przez firmę Microsoft.

- [Reguły testów penetracyjnych zaangażowania](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Testy typu „red team” w chmurze firmy Microsoft](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Współużytkowane

## <a name="next-steps"></a>Następne kroki

- Zobacz [test porównawczy zabezpieczeń platformy Azure](../security/benchmarks/overview.md)
- Dowiedz się więcej o [punktach odniesienia zabezpieczeń platformy Azure](../security/benchmarks/security-baselines-overview.md)