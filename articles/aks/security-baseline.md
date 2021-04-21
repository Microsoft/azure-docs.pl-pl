---
title: Punkt odniesienia zabezpieczeń platformy Azure dla Azure Kubernetes Service
description: Punkt Azure Kubernetes Service zabezpieczeń zawiera wskazówki proceduralne i zasoby dotyczące wdrażania zaleceń dotyczących zabezpieczeń określonych w te testach porównawczych zabezpieczeń platformy Azure.
author: msmbaldwin
ms.service: container-service
ms.topic: conceptual
ms.date: 03/30/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark, devx-track-azurepowershell
ms.openlocfilehash: 0564f1f39ac9d492dfffdf0e7adacdde08db0874
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107769593"
---
# <a name="azure-security-baseline-for-azure-kubernetes-service"></a>Punkt odniesienia zabezpieczeń platformy Azure dla Azure Kubernetes Service

Ten punkt odniesienia zabezpieczeń stosuje wskazówki z testu porównawczego zabezpieczeń platformy [Azure w wersji 1.0 do](../security/benchmarks/overview-v1.md) usługi Azure Kubernetes. Test porównawczy zabezpieczeń platformy Azure zawiera zalecenia dotyczące sposobu zabezpieczania rozwiązań w chmurze na platformie Azure. Zawartość jest pogrupowana według **kontrolek zabezpieczeń zdefiniowanych** przez test porównawczy zabezpieczeń platformy Azure i powiązanych wskazówek dotyczących usługi Azure Kubernetes. **Wykluczono** mechanizmy kontroli, które nie mają zastosowania do usługi Azure Kubernetes lub której odpowiedzialnością jest firma Microsoft.

Aby zobaczyć, jak usługa Azure Kubernetes całkowicie mapuje się na test porównawczy zabezpieczeń platformy Azure, zobacz pełny plik mapowania punktów odniesienia zabezpieczeń usługi [Azure Kubernetes.](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)

## <a name="network-security"></a>Bezpieczeństwo sieci

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: bezpieczeństwo sieci](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1. Ochrona zasobów platformy Azure w sieciach wirtualnych

**Wskazówki:** domyślnie sieciowa grupa zabezpieczeń i tabela tras są tworzone automatycznie po utworzeniu klastra Microsoft Azure Kubernetes Service (AKS). Podczas tworzenia usług za pomocą usług równoważenia obciążenia, mapowań portów lub tras ruchu wychodzącego usługi AKS automatycznie modyfikują sieciowe grupy zabezpieczeń dla odpowiedniego przepływu ruchu. Sieciowa grupa zabezpieczeń jest automatycznie skojarzona z wirtualnymi kartami sieciowym w węzłach klienta i tabelą tras z podsiecią w sieci wirtualnej. 

Użyj zasad sieciowych usługi AKS, aby ograniczyć ruch sieciowy, definiując reguły ruchu przychodzących i wychodzącego między zasobnikami systemu Linux w klastrze na podstawie wybranych przestrzeni nazw i selektorów etykiet. Użycie zasad sieciowych wymaga wtyczki Azure CNI ze zdefiniowaną siecią wirtualną i podsieciami i można ją włączyć tylko podczas tworzenia klastra. Nie można ich wdrożyć w istniejącym klastrze usługi AKS.

Można zaimplementować prywatny klaster AKS, aby zapewnić, że ruch sieciowy między serwerem interfejsu API usługi AKS i pulami węzłów pozostanie tylko w sieci prywatnej. Płaszczyzna sterowania lub serwer interfejsu API znajdują się w subskrypcji platformy Azure zarządzanej przez usługę AKS i wykorzystują wewnętrzne adresy IP (RFC1918), podczas gdy pula węzłów lub klaster klienta znajduje się we własnej subskrypcji. Serwer i pula klastrów lub węzłów komunikują się ze sobą przy użyciu usługi Azure Private Link w sieci wirtualnej serwera interfejsu API i prywatnego punktu końcowego, który jest dostępny w podsieci klastra AKS klienta.  Możesz też użyć publicznego punktu końcowego dla serwera interfejsu API usługi AKS, ale ograniczyć dostęp za pomocą funkcji Autoryzowane zakresy adresów IP serwera interfejsu API usługi AKS. 

- [Pojęcia dotyczące zabezpieczeń aplikacji i klastrów w usłudze Azure Kubernetes Service (AKS)](concepts-security.md)

- [Zabezpieczanie ruchu między zasobnikami przy użyciu zasad sieciowych Azure Kubernetes Service (AKS)](use-network-policies.md)

- [Tworzenie prywatnego klastra Azure Kubernetes Service klastra](private-clusters.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Test [porównawczy zabezpieczeń](/azure/governance/policy/samples/azure-security-benchmark) platformy Azure jest domyślną inicjatywą zasad dla usługi Security Center i stanowi podstawę Security Center [rekomendacji firmy](/azure/security-center/security-center-recommendations). Definicje Azure Policy związane z tą kontrolką są włączane automatycznie przez Security Center. Alerty związane z tą kontrolą mogą [wymagać Azure Defender](/azure/security-center/azure-defender) planu dla powiązanych usług.

**Azure Policy wbudowane definicje — Microsoft.ContainerService:**

[!INCLUDE [Resource Policy for Microsoft.ContainerService 1.1](../../includes/policy/standards/asb/rp-controls/microsoft.containerservice-1-1.md)]

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1.2: Monitorowanie i rejestrowanie konfiguracji i ruchu sieci wirtualnych, podsieci i karty sieciowe

**Wskazówki:** użyj Security Center i postępuj zgodnie z zaleceniami w zakresie ochrony sieci, aby zabezpieczyć zasoby sieciowe używane przez klastry Azure Kubernetes Service (AKS). 

Włącz dzienniki przepływu sieciowej grupy zabezpieczeń i wyślij dzienniki do konta usługi Azure Storage w celu inspekcji. Możesz również wysłać dzienniki przepływu do obszaru roboczego usługi Log Analytics, a następnie użyć usługi Analiza ruchu do zapewnienia wglądu we wzorce ruchu w chmurze platformy Azure w celu wizualizacji aktywności sieciowej, identyfikowania punktów aktywności i zagrożeń bezpieczeństwa, zrozumienia wzorców przepływu ruchu i określenia błędów konfiguracji sieci.

- [Informacje o zabezpieczeniach sieciowych zapewnianych przez Azure Security Center](../security-center/security-center-network-recommendations.md)

- [Jak włączyć dzienniki przepływu zabezpieczeń sieci](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Jak włączyć i używać Analiza ruchu](../network-watcher/traffic-analytics.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="13-protect-critical-web-applications"></a>1.3. Ochrona krytycznych aplikacji internetowych

**Wskazówki:** Użyj usługi Azure Application Gateway Web Application Firewall (WAF) przed klastrem usługi AKS, aby zapewnić dodatkową warstwę zabezpieczeń przez filtrowanie ruchu przychodzącego do aplikacji internetowych. Usługa Azure WAF używa zestawu reguł dostarczonych przez firmę Open Web Application Security Project (OWASP) dotyczących ataków, takich jak zatrucie plików cookie lub skryptów między witrynami. 

Brama interfejsu API umożliwia uwierzytelnianie, autoryzację, ograniczanie, buforowanie, przekształcanie i monitorowanie interfejsów API używanych w środowisku usługi AKS. Brama interfejsu API służy jako drzwi wejściowe do mikrousług, oddzieli klientów od mikrousług i zmniejsza złożoność mikrousług przez usunięcie obciążenia związanego z obsługą problemów krzyżowych.

- [Understand best practices for network connectivity and security in AKS](operator-best-practices-network.md)

- [Application Gateway przychodzących ](../application-gateway/ingress-controller-overview.md)

- [Używanie usługi Azure API Management z mikrousługami wdrożonych w usłudze Azure Kubernetes Service](../api-management/api-management-kubernetes.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Odmowa komunikacji ze znanymi złośliwymi adresami IP

**Wskazówki:** włącz standardową ochronę przed atakami DDoS (Distributed Denial-of-Service) firmy Microsoft w sieciach wirtualnych, w których wdrożono składniki usługi Azure Kubernetes Service (AKS).

Zainstaluj aparat zasad sieciowych i utwórz zasady sieciowe kubernetes w celu kontrolowania przepływu ruchu między zasobnikami w UKS, ponieważ domyślnie cały ruch między tymi zasobnikami jest dozwolony. Zasady sieci powinny być używane tylko w przypadku węzłów i zasobników opartych na systemie Linux w UKS. Definiowanie reguł, które ograniczają komunikację zasobników w celu poprawy zabezpieczeń. 

Wybierz zezwalanie na ruch lub odmawianie go na podstawie ustawień, takich jak przypisane etykiety, przestrzeń nazw lub port ruchu. Wymagane zasady sieciowe mogą być stosowane automatycznie podczas dynamicznego tworzenia zasobników w klastrze usługi AKS. 

- [Zabezpieczanie ruchu między zasobnikami przy użyciu zasad sieciowych Azure Kubernetes Service (AKS)](use-network-policies.md)

- [Jak skonfigurować ochronę przed DDoS](../ddos-protection/manage-ddos-protection.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="15-record-network-packets"></a>1.5. Rejestrowanie pakietów sieciowych

**Wskazówki:** użyj Network Watcher przechwytywania pakietów zgodnie z wymaganiami do badania anomalii działań. 

Network Watcher jest automatycznie włączana w regionie sieci wirtualnej podczas tworzenia lub aktualizowania sieci wirtualnej w ramach subskrypcji. Możesz również tworzyć nowe wystąpienia usługi Network Watcher użyciu programu PowerShell, interfejsu wiersza polecenia platformy Azure, interfejsu API REST lub metody Azure Resource Manager Client

- [Jak włączyć Network Watcher](../network-watcher/network-watcher-create.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6. Wdrażanie opartych na sieci systemów wykrywania włamań/zapobiegania włamań (IDS/IPS)

**Wskazówki:** Zabezpieczanie klastra Azure Kubernetes Service (AKS) przy użyciu Azure Application Gateway z włączoną Web Application Firewall (WAF). 

Jeśli wykrywanie włamań i/lub zapobieganie na podstawie inspekcji ładunku lub analizy zachowania nie jest wymagane, można użyć serwera Azure Application Gateway z włamań i skonfigurować go w "trybie wykrywania", aby rejestrować alerty i zagrożenia, lub "tryb zapobiegania" w celu aktywnego blokowania wykrytych włamań i ataków.

- [Poznaj najlepsze rozwiązania dotyczące zabezpieczania klastra usługi AKS przy użyciu aplikacji sieci Szkieletowej aplikacji sieci Szkieletowej](https://docs.microsoft.com/azure/aks/operator-best-practices-network#secure-traffic-with-a-web-application-firewall-waf)

- [Jak wdrożyć aplikację Azure Application Gateway (Azure WAF)](../web-application-firewall/ag/application-gateway-web-application-firewall-portal.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimalizowanie złożoności i obciążeń administracyjnych reguł zabezpieczeń sieci

**Wskazówki:** użyj tagów usługi dla sieci wirtualnej, aby zdefiniować kontrolę dostępu do sieci w sieciowych grupach zabezpieczeń skojarzonych Azure Kubernetes Service wystąpieniami usługi (AKS). Tagi usługi mogą być używane w miejsce określonych adresów IP podczas tworzenia reguł zabezpieczeń w celu zezwalania na ruch dla odpowiedniej usługi lub blokowania go przez określenie nazwy tagu usługi. 

Firma Microsoft zarządza prefiksami adresów obejmującymi tag usługi i automatycznie aktualizuje tag usługi w przypadku zmiany adresów.

Stosowanie tagu platformy Azure do pul węzłów w klastrze usługi AKS. Różnią się one od tagów usługi dla sieci wirtualnej i są stosowane do każdego węzła w puli węzłów i utrwalane za pośrednictwem uaktualnień. 

- [Opis tagów usługi i korzystanie z tych tagów](../virtual-network/service-tags-overview.md)

- [Understand NSGs for AKS](support-policies.md)

- [Kontrolowanie ruchu wychodzącego dla węzłów klastra w Azure Kubernetes Service (AKS)](limit-egress-traffic.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Obsługa standardowych konfiguracji zabezpieczeń dla urządzeń sieciowych

**Wskazówki:** Definiowanie i implementowanie standardowych konfiguracji zabezpieczeń Azure Policy dla zasobów sieciowych skojarzonych z klastrami Azure Kubernetes Service (AKS). 

Użyj Azure Policy aliasów w przestrzeniach nazw "Microsoft.ContainerService" i "Microsoft.Network", aby utworzyć niestandardowe zasady inspekcji lub wymuszania konfiguracji sieci klastrów usługi AKS. 

Ponadto należy użyć wbudowanych definicji zasad związanych z usługą AKS, takich jak:

- W usługach Kubernetes należy zdefiniować autoryzowane zakresy adresów IP

- Wymuszaj ruch przychodzący HTTPS w klastrze Kubernetes

- Upewnij się, że usługi nasłuchują tylko na dozwolonych portach w klastrze Kubernetes

Dodatkowe informacje są dostępne pod linkami, do których się odwołują.

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy przykłady dotyczące sieci](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#network)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="110-document-traffic-configuration-rules"></a>1.10. Udokumentuj reguły konfiguracji ruchu

**Wskazówki:** Użyj tagów dla sieciowych grup zabezpieczeń i innych zasobów na użytek przepływu ruchu do i Azure Kubernetes Service (AKS). Użyj pola "Opis" dla poszczególnych reguł sieciowej grupy zabezpieczeń, aby określić potrzebę biznesową i/lub czas trwania i tak dalej dla wszystkich reguł, które zezwalają na ruch do/z sieci.

Użyj dowolnej wbudowanej definicji Azure Policy związanej z tagami, na przykład "Wymagaj tagu i jego wartości", aby upewnić się, że wszystkie zasoby są tworzone przy użyciu tagów i otrzymywać powiadomienia dotyczące istniejących nieotagowanych zasobów.

Wybierz zezwalanie lub odrzucanie określonych ścieżek sieciowych w klastrze na podstawie przestrzeni nazw i selektorów etykiet z zasadami sieciowymi. Użyj tych przestrzeni nazw i etykiet jako deskryptorów dla reguł konfiguracji ruchu. Użyj Azure PowerShell lub interfejsu wiersza polecenia platformy Azure, aby szukać zasobów lub wykonywać na nich akcje na podstawie ich tagów.

- [Azure Policy interfejsu wiersza polecenia](/cli/azure/policy)

- [Jak tworzyć tagi i ich używać](../azure-resource-manager/management/tag-resources.md)

- [Jak utworzyć sieciowej grupy zabezpieczeń za pomocą konfiguracji zabezpieczeń](../virtual-network/tutorial-filter-network-traffic.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11. Monitorowanie konfiguracji zasobów sieciowych i wykrywanie zmian za pomocą zautomatyzowanych narzędzi

**Wskazówki:** Użyj dziennika aktywności platformy Azure do monitorowania konfiguracji zasobów sieciowych i wykrywania zmian zasobów sieciowych związanych Azure Kubernetes Service klastrami usługi (AKS). 

Utwórz alerty w Azure Monitor, które będą wyzwalane w przypadku zmiany krytycznych zasobów sieciowych. Wszystkie wpisy użytkownika AzureContainerService w dziennikach aktywności są rejestrowane jako akcje platformy. 

Użyj Azure Monitor, aby włączyć i odpytować dzienniki z usługi AKS dla składników wzorcowych, kube-apiserver i kube-controller-manager. Tworzenie węzłów, które uruchamiają kubelet ze środowiskiem uruchomieniowym kontenera, i zarządzanie nimi oraz wdrażanie aplikacji za pośrednictwem zarządzanego serwera interfejsu API Kubernetes. 

- [Jak wyświetlać i pobierać zdarzenia dziennika aktywności platformy Azure](/azure/azure-monitor/platform/activity-log#view-the-activity-log)

- [Jak tworzyć alerty w Azure Monitor](/azure/azure-monitor/platform/alerts-activity-log)

- [Włączanie i wyświetlanie dzienników węzła master platformy Kubernetes w usłudze Azure Kubernetes Service](/azure/aks/view-master-logs)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

## <a name="logging-and-monitoring"></a>Rejestrowanie i monitorowanie

*Aby uzyskać więcej informacji, zobacz [Test porównawczy zabezpieczeń platformy Azure: rejestrowanie i monitorowanie.](../security/benchmarks/security-control-logging-monitoring.md)*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1. Używanie zatwierdzonych źródeł synchronizacji czasu

**Wskazówki:** Azure Kubernetes Service (AKS) używają usługi ntp.ubuntu.com do synchronizacji czasu, wraz z portem UDP 123 i protokołem NTP (Network Time Protocol). 

Upewnij się, że serwery NTP są dostępne dla węzłów klastra w przypadku korzystania z niestandardowych serwerów DNS. 

- [Understand NTP domain and port requirements for AKS cluster nodes (Opis wymagań dotyczących domeny i portu NTP dla węzłów klastra usługi AKS)](limit-egress-traffic.md)

**Odpowiedzialność**: Współużytkowane

**Azure Security Center monitorowania:** Brak

### <a name="22-configure-central-security-log-management"></a>2.2. Konfigurowanie centralnego zarządzania dziennikami zabezpieczeń

**Wskazówki:** Włączanie dzienników inspekcji ze składników wzorcowych usług Azure Kubernetes Services (AKS), kube-apiserver i kube-controller-manager, które są dostarczane jako usługa zarządzana. 

- kube-auditaksService: nazwa wyświetlana w dzienniku inspekcji dla operacji płaszczyzny sterowania (z hcpService) 

- masterclient: nazwa wyświetlana w dzienniku inspekcji dla certyfikatu MasterClientCertificate, który można pobrać z az aks get-credentials 

- nodeclient: nazwa wyświetlana certyfikatu ClientCertificate, która jest używana przez węzły agenta

Włącz również inne dzienniki inspekcji, takie jak kube-audit. 

Wyeksportuj te dzienniki do usługi Log Analytics lub innej platformy magazynu. W Azure Monitor obszarów roboczych usługi Log Analytics można wykonywać zapytania i przeprowadzać analizy, a także używać kont usługi Azure Storage do przechowywania długoterminowego i archiwalnej.

Włącz i włącz te dane w celu Azure Sentinel rozwiązania SIEM lub rozwiązania SIEM innej firmy w zależności od wymagań biznesowych organizacji.

- [Przejrzyj schemat dziennika, w tym role dziennika tutaj](/azure/aks/view-master-logs)

- [Understand Azure Monitor for Containers](/azure/azure-monitor/insights/container-insights-overview)

- [Jak włączyć usługę Azure Monitor for Containers](/azure/azure-monitor/insights/container-insights-onboard)

- [Włączanie i wyświetlanie dzienników węzła master platformy Kubernetes w usłudze Azure Kubernetes Service](/azure/aks/view-master-logs)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3. Włączanie rejestrowania inspekcji dla zasobów platformy Azure

**Wskazówki:** Używanie dzienników aktywności do monitorowania akcji Azure Kubernetes Service zasobów usługi (AKS) w celu wyświetlenia wszystkich działań i ich stanu. Określ, jakie operacje zostały wykonane na zasobach w subskrypcji, za pomocą dzienników aktywności: 

- kto uruchomił operację
- czas, w którym wystąpiła operacja
- stan operacji
- wartości innych właściwości, które mogą pomóc w badaniach operacji

Pobieranie informacji z dziennika aktywności za pośrednictwem Azure PowerShell, interfejsu wiersza polecenia platformy Azure, interfejsu API REST platformy Azure lub interfejsu Azure Portal. 

Włącz dzienniki inspekcji składników wzorcowych AKS, takie jak: 

- kube-auditaksService: nazwa wyświetlana w dzienniku inspekcji dla operacji płaszczyzny sterowania (z usługi hcpService) 

- masterclient: nazwa wyświetlana w dzienniku inspekcji dla certyfikatu MasterClientCertificate, który można uzyskać z az aks get-credentials 

- nodeclient: nazwa wyświetlana certyfikatu ClientCertificate, która jest używana przez węzły agenta

Włącz również inne dzienniki inspekcji, takie jak kube-audit. 

- [Jak włączyć i przejrzeć dzienniki węzła głównego kubernetes w u usługach AKS](/azure/aks/view-master-logs)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4. Zbieranie dzienników zabezpieczeń z systemów operacyjnych

**Wskazówki:** Włącz automatyczną instalację agentów usługi Log Analytics w celu zbierania danych z węzłów klastra usługi AKS. Ponadto włącz automatyczną aprowizowanie agenta monitorowania usługi Azure Log Analytics z Azure Security Center, ponieważ domyślnie automatyczna aprownia jest wyłączona. Agenta można również zainstalować ręcznie. Po wł. automatycznej aprow Security Center agenta usługi Log Analytics na wszystkich obsługiwanych maszyn wirtualnych platformy Azure i wszystkich nowo utworzonych. Usługa Security Center zbiera dane z usługi Azure Virtual Machines (VM), zestawów skalowania maszyn wirtualnych i kontenerów IaaS, takich jak węzły klastra Kubernetes, w celu monitorowania luk w zabezpieczeniach i zagrożeń. Dane są zbierane przy użyciu agenta usługi Azure Log Analytics, który odczytuje różne konfiguracje związane z zabezpieczeniami i dzienniki zdarzeń z maszyny i kopiuje dane do obszaru roboczego w celu analizy. 

Zbieranie danych jest wymagane w celu zapewnienia wglądu w brakujące aktualizacje, błędnie skonfigurowane ustawienia zabezpieczeń systemu operacyjnego, stan ochrony punktu końcowego oraz wykrywanie kondycji i zagrożeń.

- [Jak włączyć automatyczną aprowizowanie agenta usługi Log Analytics](../security-center/security-center-enable-data-collection.md)

**Odpowiedzialność**: Współużytkowane

**Azure Security Center monitorowania:** Brak

### <a name="25-configure-security-log-storage-retention"></a>2.5. Konfigurowanie przechowywania magazynu dzienników zabezpieczeń

**Wskazówki:** Dołączanie wystąpień usługi Azure Kubernetes Service (AKS) do usługi Azure Monitor i ustawianie odpowiedniego okresu przechowywania obszaru roboczego usługi Azure Log Analytics zgodnie z wymaganiami zgodności w organizacji. 

- [Jak ustawić parametry przechowywania dzienników dla obszarów roboczych usługi Log Analytics](/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="26-monitor-and-review-logs"></a>2.6. Monitorowanie i przeglądanie dzienników

**Wskazówki:** Dołączanie wystąpień Azure Kubernetes Service (AKS) w celu Azure Monitor i konfigurowania ustawień diagnostycznych klastra. 

Użyj Azure Monitor usługi Log Analytics, aby przeglądać dzienniki i wykonywać zapytania dotyczące danych dziennika. Azure Monitor są włączone i zarządzane w usłudze Azure Portal lub za pośrednictwem interfejsu wiersza polecenia i działają zarówno z klastrami AKS opartymi na rolach (RBAC, Kubernetes RBAC) platformy Azure, jak i klastrami AKS bez funkcji RBAC.

Wyświetl dzienniki wygenerowane przez składniki główne usługi AKS (kube-apiserver i kube-controllermanager) na potrzeby rozwiązywania problemów z aplikacją i usługami. Włącz i na pokładzie danych do Azure Sentinel lub SIEM innej firmy do scentralizowanego zarządzania dziennikami i monitorowania.

- [Jak włączyć i przejrzeć dzienniki węzła głównego kubernetes w ucieku AKS](/azure/aks/view-master-logs)

- [Jak do dołączać Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Jak wykonywać zapytania niestandardowe w Azure Monitor](/azure/azure-monitor/log-query/get-started-queries)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7. Włącz alerty dla działań anomalii

**Wskazówki:** użyj Azure Kubernetes Service (AKS) razem z Security Center, aby uzyskać bardziej wgląd w węzły usługi AKS. 

Przejrzyj Security Center dotyczące zagrożeń i złośliwych działań wykrytych na hoście i na poziomie klastra. Security Center implementuje ciągłą analizę nieprzetworzonych zdarzeń zabezpieczeń występujących w klastrze usługi AKS, takich jak dane sieciowe, tworzenie procesów i dziennik inspekcji kubernetes. Ustal, czy to działanie jest oczekiwane, czy też aplikacja działa nieprawidłowo. Użyj metryk i dzienników w Azure Monitor, aby zrównowazić swoje wyniki. 

- [Opis integracji usług Azure Kubernetes Services z Security Center](../security-center/defender-for-kubernetes-introduction.md)

- [Jak włączyć usługę Azure Security Center Standardowa](../security-center/security-center-get-started.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="28-centralize-anti-malware-logging"></a>2.8: Scentralizowanie rejestrowania ochrony przed złośliwym oprogramowaniem

**Wskazówki:** Instalowanie i włączanie ochrony przed złośliwym oprogramowaniem firmy Microsoft dla platformy Azure Azure Kubernetes Service maszyn wirtualnych (AKS) i węzłów zestawu skalowania maszyn wirtualnych. Przejrzyj alerty w Security Center do skorygowania.

- [Microsoft Antimalware dla Azure Cloud Services i Virtual Machines](../security/fundamentals/antimalware.md)

- [Przewodnik informacyjny dotyczący alertów zabezpieczeń](../security-center/alerts-reference.md)

- [Alerty dla kontenerów — Azure Kubernetes Service klastry](https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-akscluster)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="29-enable-dns-query-logging"></a>2.9: Włączanie rejestrowania zapytań DNS

**Wskazówki:** Azure Kubernetes Service (AKS) używa projektu CoreDNS do zarządzania systemem DNS klastra i rozpoznawania go.

Włącz rejestrowanie zapytań DNS, stosując udokumentowaną konfigurację w coredns-custom ConfigMap. 

- [Dostosowywanie serwera CoreDNS w usłudze Azure Kubernetes Service](coredns-custom.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="210-enable-command-line-audit-logging"></a>2.10: Włączanie rejestrowania inspekcji w wierszu polecenia

**Wskazówki:** Użyj narzędzia kubectl, klienta wiersza polecenia w układzie Azure Kubernetes Service (AKS) do zarządzania klastrem Kubernetes i uzyskania dzienników z węzła usługi AKS na potrzeby rozwiązywania problemów. Program Kubectl jest już zainstalowany, jeśli używasz Azure Cloud Shell. Aby zainstalować kubectl lokalnie, użyj polecenia cmdlet "Install-AzAksKubectl".

- [Szybki start — wdrażanie klastra Azure Kubernetes Service przy użyciu programu PowerShell](kubernetes-walkthrough-powershell.md)

- [Pobieranie dzienników agenta kubelet z węzłów klastra usługi Azure Kubernetes Service](kubelet-logs.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

## <a name="identity-and-access-control"></a>Tożsamość i kontrola dostępu

*Aby uzyskać więcej informacji, zobacz [Test porównawczy zabezpieczeń platformy Azure: tożsamość i Access Control.](../security/benchmarks/security-control-identity-access-control.md)*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1. Utrzymywanie spisu kont administracyjnych

**Wskazówki:** Azure Kubernetes Service (AKS) nie zapewnia rozwiązania do zarządzania tożsamościami, które przechowuje zwykłe konta użytkowników i hasła. Dzięki Azure Active Directory (Azure AD) można udzielić użytkownikom lub grupom dostępu do zasobów platformy Kubernetes w przestrzeni nazw lub w klastrze.

Wykonywanie zapytań ad hoc w celu odnajdywania kont, które są członkami grup administracyjnych usługi AKS, za pomocą modułu programu Azure AD PowerShell

Interfejs wiersza polecenia platformy Azure umożliwia regularne uzgadnianie dostępu przy użyciu operacji takich jak "Uzyskiwanie poświadczeń dostępu dla zarządzanego klastra Kubernetes". Zaim implementuj ten proces, aby zachować zaktualizowany spis kont usług, które są innym podstawowym typem użytkownika w usłudze AKS. Wymuszanie Security Center zasad zarządzania tożsamościami i dostępem.

- [Jak zintegrować usługę AKS z usługą Azure AD](azure-ad-integration-cli.md)

- [Jak uzyskać członków roli katalogu w usłudze Azure AD przy użyciu programu PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember)

- [Jak monitorować tożsamość i dostęp za pomocą Azure Security Center](../security-center/security-center-identity-access.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Zmień domyślne hasła tam, gdzie ma to zastosowanie

**Wskazówki:** Azure Kubernetes Service (AKS) nie ma koncepcji typowych haseł domyślnych i nie zapewnia rozwiązania do zarządzania tożsamościami, w którym można przechowywać zwykłe konta użytkowników i hasła. Dzięki Azure Active Directory (Azure AD) można udzielić dostępu opartego na rolach do zasobów usługi AKS w przestrzeni nazw lub w klastrze. 

Wykonywanie zapytań ad hoc w celu odnajdywania kont, które są członkami grup administracyjnych usługi AKS, za pomocą modułu programu Azure AD PowerShell

- [Opis opcji dostępu i tożsamości dla usług AKS](concepts-identity.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Używanie dedykowanych kont administracyjnych

**Wskazówki:** Integrowanie uwierzytelniania użytkowników dla klastrów Azure Kubernetes Service (AKS) z usługą Azure Active Directory (Azure AD). Zaloguj się do klastra usługi AKS przy użyciu tokenu uwierzytelniania usługi Azure AD. Skonfiguruj kontrolę dostępu opartą na rolach (RBAC) usługi Kubernetes, aby uzyskać dostęp administracyjny do informacji i uprawnień konfiguracji kubenetes (kubeconfig), przestrzeni nazw i zasobów klastra. 

Tworzenie zasad i procedur dotyczących korzystania z dedykowanych kont administracyjnych. Implementowanie Security Center zarządzania tożsamościami i dostępem.

- [Jak monitorować tożsamość i dostęp za pomocą Azure Security Center](../security-center/security-center-identity-access.md)

- [Kontrola dostępu do zasobów klastra](azure-ad-rbac.md)

- [Korzystanie z kontroli dostępu opartej na rolach na platformie Azure](control-kubeconfig-access.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Korzystanie z logowania jednokrotnego (SSO) z Azure Active Directory

**Wskazówki:** Używanie logowania pojedynczego na Azure Kubernetes Service (AKS) z uwierzytelnianiem zintegrowanym usługi Azure Active Directory (Azure AD) dla klastra usługi AKS.

- [Jak wyświetlać dzienniki, zdarzenia i metryki zasobników kubernetes w czasie rzeczywistym](/azure/azure-monitor/insights/container-insights-livedata-overview)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Użyj uwierzytelniania wieloskładnikowego dla wszystkich Azure Active Directory dostępu opartego na systemie

**Wskazówki:** integrowanie uwierzytelniania Azure Kubernetes Service (AKS) z Azure Active Directory (Azure AD). 

Włącz uwierzytelnianie wieloskładnikowe usługi Azure AD Security Center zgodnie z zaleceniami dotyczącymi zarządzania tożsamościami i dostępem.

- [Jak włączyć uwierzytelnianie wieloskładnikowe na platformie Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Jak monitorować tożsamość i dostęp w ramach Azure Security Center](../security-center/security-center-identity-access.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6. Użyj dedykowanych maszyn (stacji roboczych z dostępem uprzywilejowanym) do wykonywania wszystkich zadań administracyjnych

**Wskazówki:** używanie stacji roboczej z dostępem uprzywilejowanym (PAW) z uwierzytelnianiem wieloskładnikowym (MFA) skonfigurowanej do logowania się do określonych klastrów Azure Kubernetes Service (AKS) i powiązanych zasobów.

- [Dowiedz się więcej o stacjach roboczych z dostępem uprzywilejowanym](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Jak włączyć usługę Multi-Factor Authentication (MFA) na platformie Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7. Rejestrowanie i alerty dotyczące podejrzanych działań z kont administracyjnych

**Wskazówki:** korzystanie Azure Active Directory (Azure AD) z uwierzytelnianiem zintegrowanym z usługą Azure AD na Azure Kubernetes Service (AKS). Alerty mogą być generowane, gdy w środowisku wystąpi podejrzane lub niebezpieczne działanie. Użyj Security Center, aby monitorować działania tożsamości i dostępu.

- [Identyfikowanie użytkowników usługi Azure AD oflagowanych na skutek ryzykownego działania](../active-directory/identity-protection/overview-identity-protection.md)

- [Jak monitorować aktywność użytkowników w zakresie tożsamości i dostępu w Azure Security Center](../security-center/security-center-identity-access.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="38-manage-azure-resources-only-from-approved-locations"></a>3.8: Zarządzanie zasobami platformy Azure tylko z zatwierdzonych lokalizacji

**Wskazówki:** Użyj dostępu warunkowego nazwanych lokalizacji, aby zezwolić na dostęp do klastrów usługi Azure Kubernetes Service (AKS) tylko z określonych grup logicznych zakresów adresów IP lub krajów/regionów. Wymaga to zintegrowanego uwierzytelniania dla usługi AKS z Azure Active Directory (Azure AD).

Ogranicz dostęp do serwera interfejsu API usługi AKS z ograniczonego zestawu zakresów adresów IP, ponieważ odbiera żądania wykonania akcji w klastrze w celu utworzenia zasobów lub skalowania liczby węzłów. 

- [Bezpieczny dostęp do serwera interfejsu API przy użyciu autoryzowanych zakresów adresów IP w Azure Kubernetes Service (AKS)](api-server-authorized-ip-ranges.md)

- [Jak skonfigurować nazwane lokalizacje na platformie Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="39-use-azure-active-directory"></a>3.9: Użyj Azure Active Directory

**Wskazówki:** Użyj usługi Azure Active Directory (Azure AD) jako centralnego systemu uwierzytelniania i autoryzacji dla usługi Azure Kubernetes Service (AKS). Usługa Azure AD chroni dane przy użyciu silnego szyfrowania danych magazynowane i podczas przesyłania, a także dodaje do danych skrótów skróty i bezpiecznie przechowuje poświadczenia użytkownika.

Używanie wbudowanych ról usługi AKS z kontrolą dostępu na podstawie ról (RBAC) platformy Azure — współautor i właściciel zasad zasobów na użytek operacji przypisywania zasad do klastra Kubernetes

- [Przegląd zasad platformy Azure](../governance/policy/overview.md)

- [Jak zintegrować usługę Azure AD z usługą AKS](azure-ad-integration-cli.md)

- [Integrowanie usługi Azure AD zarządzanej przez usługę AKS](managed-aad.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Regularne przeglądanie i uzgadnianie dostępu użytkowników

**Wskazówki:** używaj Azure Active Directory zabezpieczeń usługi Azure AD z uwierzytelnianiem zintegrowanym z usługą Azure AD na Azure Kubernetes Service (AKS). Przeszukaj dzienniki usługi Azure AD, aby ułatwić odnajdywanie nieaktywnego konta. 

Wykonywanie przeglądów dostępu do tożsamości platformy Azure w celu efektywnego zarządzania członkostwem w grupach, dostępem do aplikacji dla przedsiębiorstw i przypisań ról. Zalecenia dotyczące korygowania tożsamości i dostępu z Security Center.

Należy pamiętać o rolach używanych do celów pomocy technicznej lub rozwiązywania problemów. Na przykład wszystkie akcje klastra podejmowane przez pomoc techniczną firmy Microsoft (za zgodą użytkownika) są podejmowane w ramach wbudowanej roli "edytuj" platformy Kubernetes o nazwie aks-support-rolebinding. Ta rola obsługuje usługę AKS w celu edytowania konfiguracji klastra i zasobów w celu rozwiązywania i diagnozowania problemów z klastrem. Jednak ta rola nie może modyfikować uprawnień ani tworzyć ról ani powiązań ról. Dostęp do tej roli jest włączany tylko w ramach aktywnych biletów pomocy technicznej z dostępem just in time (JIT).
 
- [Opcje dostępu i tożsamości dla usługi Azure Kubernetes Service (AKS)](concepts-identity.md)

- [Jak używać przeglądów dostępu do tożsamości platformy Azure](../active-directory/governance/access-reviews-overview.md)

- [Jak monitorować tożsamość użytkownika i działania dostępu w Azure Security Center](../security-center/security-center-identity-access.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Monitorowanie prób uzyskania dostępu do dezaktywowanych poświadczeń

**Wskazówki:** Integrowanie uwierzytelniania użytkowników na Azure Kubernetes Service (AKS) z Azure Active Directory (Azure AD). Utwórz ustawienia diagnostyczne dla usługi Azure AD, wysyłając dzienniki inspekcji i logowania do obszaru roboczego usługi Azure Log Analytics. Skonfiguruj żądane alerty (np. gdy dezaktywowane konto spróbuje się zalogować) w obszarze roboczym usługi Azure Log Analytics.
- [Jak zintegrować dzienniki aktywności platformy Azure z usługą Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Jak tworzyć i wyświetlać alerty dzienników oraz zarządzać nimi przy użyciu Azure Monitor](/azure/azure-monitor/platform/alerts-log)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Odchylenie zachowania alertu przy logowaniu do konta

**Wskazówki:** Integrowanie uwierzytelniania użytkowników na Azure Kubernetes Service (AKS) z Azure Active Directory (Azure AD). Użyj funkcji wykrywania ryzyka i ochrony tożsamości usługi Azure AD, aby skonfigurować automatyczne odpowiedzi na wykryte podejrzane akcje związane z tożsamościami użytkowników. Pozysuj dane do Azure Sentinel do dalszych badań w oparciu o potrzeby biznesowe.

- [Jak wyświetlić ryzykowne logowania w usłudze Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

- [Jak skonfigurować i włączyć zasady ryzyka usługi Identity Protection](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Jak do dołączać Azure Sentinel](../sentinel/quickstart-onboard.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

## <a name="data-protection"></a>Ochrona danych

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: ochrona danych](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1. Utrzymywanie spisu poufnych informacji

**Wskazówki:** użyj tagów dla zasobów związanych z Azure Kubernetes Service (AKS), aby pomóc w śledzeniu zasobów platformy Azure, które przechowują lub przetwarzają poufne informacje.

- [Jak tworzyć tagi i ich używać](../azure-resource-manager/management/tag-resources.md)

- [Aktualizowanie tagów dla klastrów zarządzanych](/rest/api/aks/managedclusters/updatetags)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Izolowanie systemów przechowujące lub przetwarzające informacje poufne

**Wskazówki:** Logicznie odizoluj zespoły i obciążenia w tym samym klastrze za pomocą usługi Azure Kubernetes Service (AKS), aby zapewnić najmniejszą liczbę uprawnień w zakresie zasobów wymaganych przez każdy zespół. 

Użyj przestrzeni nazw na platformie Kubernetes, aby utworzyć granicę izolacji logicznej. Rozważ zaimplementowanie dodatkowych funkcji rozwiązania Kubernetes w celu izolacji i wieloadyscyjności, takich jak planowanie, sieć, uwierzytelnianie/autoryzacja i kontenery.

Zaim implementuj oddzielne subskrypcje i/lub grupy zarządzania dla środowisk deweloperskich, testowych i produkcyjnych. Oddziel klastry usługi AKS od sieci, wdrażając je w odrębnych sieciach wirtualnych, które są odpowiednio oznaczone.

- [Dowiedz się więcej o najlepszych rozwiązaniach dotyczących izolacji klastra w u usługach AKS](operator-best-practices-cluster-isolation.md)

- [Jak utworzyć dodatkowe subskrypcje platformy Azure](../cost-management-billing/manage/create-subscription.md)

- [Understand best practices for network connectivity and security in AKS](operator-best-practices-network.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Monitorowanie i blokowanie nieautoryzowanego transferu poufnych informacji

**Wskazówki:** Skorzystaj z rozwiązania innej firmy od firmy Azure Marketplace w sieci obwodowej, które monitoruje nieautoryzowany transfer poufnych informacji i blokuje takie transfery podczas powiadamiania specjalistów ds. zabezpieczeń informacji.

Firma Microsoft zarządza podstawową platformą i traktuje całą zawartość klientów jako poufne oraz przechodzi do bardzo długich okresów, aby chronić się przed utratą i narażeniem danych klientów. Aby zapewnić bezpieczeństwo danych klientów na platformie Azure, firma Microsoft wdrożyła i utrzymuje pakiet niezawodnych funkcji i kontroli ochrony danych.

- [Lista wymaganych portów, adresów i nazw domen dla funkcji usługi AKS](limit-egress-traffic.md)

- [Jak skonfigurować ustawienia diagnostyczne dla Azure Firewall](../firewall/firewall-diagnostics.md)

- [Informacje na temat ochrony danych klientów na platformie Azure](../security/fundamentals/protection-customer-data.md)

**Odpowiedzialność**: Współużytkowane

**Azure Security Center monitorowania:** Brak

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Szyfruj wszystkie poufne informacje podczas przesyłania

**Wskazówki:** Utwórz kontroler ruchu przychodzących HTTPS i użyj własnych certyfikatów TLS (lub opcjonalnie let's encrypt) dla wdrożeń usługi Azure Kubernetes Service (AKS). 

Ruch wychodzący kubernetes jest domyślnie szyfrowany za pośrednictwem protokołu HTTPS/TLS. Przejrzyj potencjalnie niezaszyfrowany ruch wychodzący z wystąpień usługi AKS w celu dodatkowego monitorowania. W niektórych przypadkach może to obejmować ruch NTP, ruch DNS, ruch HTTP do pobierania aktualizacji. 

- [Jak utworzyć kontroler ruchu przychodzących HTTPS w u usługi AKS i używać własnych certyfikatów TLS](ingress-own-tls.md)

- [How to create an HTTPS ingress controller on AKS with Let's Encrypt](ingress-tls.md)

- [Lista potencjalnych portów i protokołów wychodzącego używanych przez platformę AKS](limit-egress-traffic.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5. Identyfikowanie poufnych danych za pomocą aktywnego narzędzia odnajdywania

**Wskazówki:** Funkcje identyfikacji, klasyfikacji i zapobiegania utracie danych nie są jeszcze dostępne dla usługi Azure Storage ani zasobów obliczeniowych. Implementowanie rozwiązania innej firmy, jeśli jest to wymagane do celów zgodności.
Firma Microsoft zarządza podstawową platformą i traktuje całą zawartość klientów jako poufne i przechodzi przez długi czas w celu ochrony przed utratą i utratą danych klientów. 

Aby zapewnić bezpieczeństwo danych klientów na platformie Azure, firma Microsoft wdrożyła i utrzymuje pakiet niezawodnych funkcji i kontroli ochrony danych.

- [Informacje na temat ochrony danych klientów na platformie Azure](../security/fundamentals/protection-customer-data.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4.6. Zarządzanie dostępem do zasobów przy użyciu kontroli RBAC platformy Azure

**Wskazówki:** Użyj systemu autoryzacji kontroli dostępu opartej na rolach (RBAC) platformy Azure opartego na Azure Resource Manager, aby zapewnić szczegółowe zarządzanie dostępem do zasobów platformy Azure.

Skonfiguruj Azure Kubernetes Service (AKS) do używania usługi Azure Active Directory (Azure AD) do uwierzytelniania użytkowników. Zaloguj się do klastra usługi AKS przy użyciu tokenu uwierzytelniania usługi Azure AD przy użyciu tej konfiguracji. 

Używanie wbudowanych ról usługi AKS z usługą Azure RBAC — współautor i właściciel zasad zasobów na użytek operacji przypisywania zasad do klastra usługi AKS

- [Jak kontrolować dostęp do zasobów klastra przy użyciu kontroli dostępu na podstawie ról platformy Azure i tożsamości usługi Azure AD w usłudze AKS](azure-ad-rbac.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Test [porównawczy zabezpieczeń](/azure/governance/policy/samples/azure-security-benchmark) platformy Azure jest domyślną inicjatywą zasad dla usługi Security Center i stanowi podstawę Security Center [rekomendacji firmy](/azure/security-center/security-center-recommendations). Definicje Azure Policy związane z tą kontrolką są włączane automatycznie przez Security Center. Alerty związane z tą kontrolą mogą [wymagać Azure Defender](/azure/security-center/azure-defender) planu dla powiązanych usług.

**Azure Policy wbudowane definicje — Microsoft.ContainerService:**

[!INCLUDE [Resource Policy for Microsoft.ContainerService 4.6](../../includes/policy/standards/asb/rp-controls/microsoft.containerservice-4-6.md)]

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7. Wymuszanie kontroli dostępu przy użyciu ochrony przed utratą danych opartej na hoście

**Wskazówki:** Funkcje identyfikacji, klasyfikacji i zapobiegania utracie danych nie są jeszcze dostępne dla usługi Azure Storage ani zasobów obliczeniowych. Implementowanie rozwiązania innej firmy, jeśli jest to wymagane do celów zgodności.
Firma Microsoft zarządza podstawową platformą i traktuje całą zawartość klientów jako poufne oraz przechodzi do bardzo długich okresów, aby chronić się przed utratą i narażeniem danych klientów. Aby zapewnić bezpieczeństwo danych klientów na platformie Azure, firma Microsoft wdrożyła i utrzymuje pakiet niezawodnych funkcji i kontroli ochrony danych.

- [Informacje na temat ochrony danych klientów na platformie Azure](../security/fundamentals/protection-customer-data.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8. Szyfrowanie poufnych informacji w spoczynku

**Wskazówki:** Dwa podstawowe typy magazynu dostępne dla woluminów w usłudze Azure Kubernetes Service (AKS) są wsadowe w usłudze Azure Disks lub Azure Files. Oba typy magazynów używają usługi Azure szyfrowanie usługi Storage (SSE), która szyfruje dane magazynowe w celu zwiększenia bezpieczeństwa. Domyślnie dane są szyfrowane przy użyciu kluczy zarządzanych przez firmę Microsoft.

Szyfrowanie danych w spoczynku przy użyciu kluczy zarządzanych przez klienta jest dostępne do szyfrowania dysków systemu operacyjnego i danych w klastrach usługi AKS w celu dodatkowej kontroli nad kluczami szyfrowania. Klienci są właścicielami działań zarządzania kluczami, takich jak tworzenie kopii zapasowej i rotacja kluczy. Dysków nie można obecnie zaszyfrować przy Azure Disk Encryption na poziomie węzła usługi AKS.

- [Najlepsze rozwiązania dotyczące magazynu i kopii zapasowych w Azure Kubernetes Service (AKS)](operator-best-practices-storage.md)

- [Korzystanie z własnych kluczy (BYOK) z dyskami platformy Azure w usłudze Azure Kubernetes Service (AKS)](azure-disk-customer-managed-keys.md)

**Odpowiedzialność**: Współużytkowane

**Azure Security Center monitorowania:** Brak

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9. Rejestrowanie i alerty dotyczące zmian w krytycznych zasobach platformy Azure

**Wskazówki:** użyj Azure Monitor dla kontenerów, aby monitorować wydajność obciążeń kontenerów wdrożonych w zarządzanych klastrach Kubernetes hostowanych w Azure Kubernetes Service (AKS). 

Skonfiguruj alerty proaktywnego tworzenia powiadomień lub dzienników, gdy użycie procesora CPU i pamięci w węzłach lub kontenerach przekracza zdefiniowane progi lub gdy w klastrze wystąpi zmiana stanu kondycji w pniu zbiorczym kondycji infrastruktury lub węzłów. 

Użyj dziennika aktywności platformy Azure, aby monitorować klastry usługi AKS i powiązane zasoby na wysokim poziomie. Integracja z usługą Prometheus w celu wyświetlania metryk aplikacji i obciążeń zbieranych z węzłów i rozwiązania Kubernetes przy użyciu zapytań w celu tworzenia niestandardowych alertów, pulpitów nawigacyjnych i szczegółowej analizy.

- [Understand Azure Monitor for Containers](/azure/azure-monitor/insights/container-insights-overview)

- [Jak włączyć obsługę Azure Monitor kontenerów](/azure/azure-monitor/insights/container-insights-onboard)

- [Jak wyświetlać i pobierać zdarzenia dziennika aktywności platformy Azure](/azure/azure-monitor/platform/activity-log#view-the-activity-log)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

## <a name="vulnerability-management"></a>Zarządzanie lukami w zabezpieczeniach

*Aby uzyskać więcej informacji, zobacz [Test porównawczy zabezpieczeń platformy Azure: zarządzanie lukami w zabezpieczeniach.](../security/benchmarks/security-control-vulnerability-management.md)*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1. Uruchamianie zautomatyzowanych narzędzi do skanowania luk w zabezpieczeniach

**Wskazówki:** użyj Security Center, aby monitorować Azure Container Registry, w tym Azure Kubernetes Service (AKS) pod względu na luki w zabezpieczeniach. Włącz pakiet Container Registries w Security Center, aby upewnić się, Security Center jest gotowy do skanowania obrazów, które są wypychane do rejestru.

Otrzymuj powiadomienia na pulpicie nawigacyjnym Security Center, gdy problemy zostaną znalezione po Security Center skanowania obrazu przy użyciu rozwiązania Qualys. Funkcja pakietu Container Registries zapewnia bardziej szczegółowe informacje o lukach w zabezpieczeniach obrazów używanych w Azure Resource Manager rejestrach. 

Użyj Security Center, aby uzyskać zalecenia z akcjami dla każdej luki w zabezpieczeniach. Zalecenia te obejmują klasyfikację ważności i wskazówki dotyczące korygowania. 

- [Najlepsze rozwiązania dotyczące zarządzania obrazami kontenerów i zabezpieczeń w Azure Kubernetes Service (AKS)](../security-center/defender-for-container-registries-introduction.md)

- [Understand best practices for container image management and security in AKS](operator-best-practices-container-image-management.md)

- [Informacje o integracji rejestru kontenerów z Azure Security Center](../security-center/defender-for-container-registries-introduction.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2. Wdrażanie zautomatyzowanego rozwiązania do zarządzania poprawkami systemu operacyjnego

**Wskazówki:** Aktualizacje zabezpieczeń są automatycznie stosowane do węzłów systemu Linux w celu ochrony klastrów Azure Kubernetes Service klienta (AKS). Te aktualizacje obejmują poprawki zabezpieczeń systemu operacyjnego lub aktualizacje jądra. 

Należy pamiętać, że proces aktualizacji węzłów systemu Windows Server różni się od węzłów z systemem Linux, ponieważ węzły systemu Windows Server nie otrzymują codziennych aktualizacji. Zamiast tego klienci muszą wykonać uaktualnienie pul węzłów systemu Windows Server w klastrach usługi AKS, które wdrażają nowe węzły przy użyciu najnowszego podstawowego obrazu i poprawek serwera Windows Server przy użyciu panelu sterowania platformy Azure lub interfejsu wiersza polecenia platformy Azure. Te aktualizacje zawierają ulepszenia zabezpieczeń lub funkcjonalności w u usługach AKS.

- [Opis sposobu stosowania aktualizacji do węzłów klastra usługi AKS z systemem Linux](node-updates-kured.md)

- [Jak uaktualnić pulę węzłów usługi AKS dla klastrów usługi AKS, które korzystają z węzłów systemu Windows Server](https://docs.microsoft.com/azure/aks/use-multiple-node-pools#upgrade-a-node-pool)

- [Azure Kubernetes Service obrazów węzła (AKS)](node-image-upgrade.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="53-deploy-an-automated-patch-management-solution-for-third-party-software-titles"></a>5.3. Wdrażanie zautomatyzowanego rozwiązania do zarządzania poprawkami dla tytułów oprogramowania innych firm

**Wskazówki:** Implementowanie ręcznego procesu w celu Azure Kubernetes Service aplikacji innych firm w węźle klastra (AKS) pozostaje poprawione przez cały okres istnienia klastra. Może to wymagać włączenia aktualizacji automatycznych, monitorowania węzłów lub przeprowadzania okresowych ponownych uruchomień.

**Odpowiedzialność**: Klient

**Azure Security Center:** Test porównawczy zabezpieczeń platformy [Azure](/azure/governance/policy/samples/azure-security-benchmark) to domyślna inicjatywa zasad dla usługi Security Center i stanowi podstawę rekomendacji Security Center [firmy .](/azure/security-center/security-center-recommendations) Definicje Azure Policy związane z tą kontrolką są włączane automatycznie przez Security Center. Alerty związane z tą kontrolą mogą [wymagać Azure Defender](/azure/security-center/azure-defender) planu dla powiązanych usług.

**Azure Policy wbudowane definicje — Microsoft.ContainerService:**

[!INCLUDE [Resource Policy for Microsoft.ContainerService 5.3](../../includes/policy/standards/asb/rp-controls/microsoft.containerservice-5-3.md)]

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4. Porównanie skanów luk w zabezpieczeniach typu "wstecz"

**Wskazówki:** eksportuj Security Center skanowania w spójnych odstępach czasu i porównaj wyniki, aby sprawdzić, czy luki w zabezpieczeniach zostały skorygowane.

Użyj polecenia cmdlet programu PowerShell "Get-AzSecurityTask", aby zautomatyzować pobieranie zadań zabezpieczeń, które program Security Center zaleca, aby zwiększyć poziom zabezpieczeń i skorygować wyniki skanowania luk w zabezpieczeniach.

- [Jak używać programu PowerShell do wyświetlania luk w zabezpieczeniach wykrytych przez Azure Security Center](/powershell/module/az.security/get-azsecuritytask)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5. Określanie priorytetów korygowania wykrytych luk w zabezpieczeniach za pomocą procesu oceny ryzyka

**Wskazówki:** Użyj klasyfikacji ważności podanej przez Security Center, aby ustalić priorytety korygowania luk w zabezpieczeniach. 

Użyj wspólnego systemu oceniania luk w zabezpieczeniach (CVSS) (lub innego systemu oceniania dostarczonego przez narzędzie do skanowania), jeśli używasz wbudowanego narzędzia do oceny luk w zabezpieczeniach (takiego jak Qualys lub Rapid7, oferowanego przez platformę Azure).

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

## <a name="inventory-and-asset-management"></a>Zarządzanie magazynem i zasobami

*Aby uzyskać więcej informacji, zobacz [Test porównawczy zabezpieczeń platformy Azure: spis i zarządzanie zasobami.](../security/benchmarks/security-control-inventory-asset-management.md)*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1. Korzystanie z rozwiązania do automatycznego odnajdywania zasobów

**Wskazówki:** użyj Azure Resource Graph do wykonywania zapytań i odnajdywania wszystkich zasobów (takich jak obliczenia, magazyn, sieć itp.) w ramach subskrypcji. Upewnij się, że masz odpowiednie uprawnienia (odczyt) w dzierżawie i możesz wyliczyć wszystkie subskrypcje platformy Azure, a także zasoby w ramach twoich subskrypcji.

Mimo że klasyczne zasoby platformy Azure można Resource Graph za pośrednictwem Resource Graph, zdecydowanie zaleca się tworzenie i używanie Azure Resource Manager opartych na zasobach w przyszłości.

- [Jak tworzyć zapytania za pomocą usługi Azure Graph](../governance/resource-graph/first-query-portal.md)

- [Jak wyświetlić subskrypcje platformy Azure](/powershell/module/az.accounts/get-azsubscription)

- [Opis kontroli RBAC na platformie Azure](../role-based-access-control/overview.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="62-maintain-asset-metadata"></a>6.2. Obsługa metadanych zasobów

**Wskazówki:** Stosowanie tagów do zasobów platformy Azure z metadanymi w celu logicznego zorganizowania ich w taksonomię.

- [Jak tworzyć tagi i ich używać](../azure-resource-manager/management/tag-resources.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Usuwanie nieautoryzowanych zasobów platformy Azure

**Wskazówki:** W celu organizowania i śledzenia zasobów używaj tagowania, grup zarządzania i oddzielnych subskrypcji, jeśli jest to konieczne. 

Stosowanie etykiet, etykiet lub tagów podczas tworzenia puli Azure Kubernetes Service (AKS). Wszystkie węzły w tej puli węzłów będą również dziedziczyć ten znacznik, etykietę lub etykietę.

Etykiety, etykiety lub tagi mogą być używane do regularnego uzgadniania spisu i zapewnienia, że nieautoryzowane zasoby zostaną usunięte z subskrypcji w terminowy sposób.

- [Jak utworzyć dodatkowe subskrypcje platformy Azure](../cost-management-billing/manage/create-subscription.md)

- [Jak utworzyć Grupy zarządzania](../governance/management-groups/create-management-group-portal.md)

- [Jak tworzyć tagi użytkowników i je tworzyć](../azure-resource-manager/management/tag-resources.md)

- [Klastry zarządzane — aktualizowanie tagów](/rest/api/aks/managedclusters/updatetags)

- [Określanie wartości, etykiety lub tagu dla puli węzłów](https://docs.microsoft.com/azure/aks/use-multiple-node-pools#specify-a-taint-label-or-tag-for-a-node-pool)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6.4. Definiowanie i utrzymywanie spisu zatwierdzonych zasobów platformy Azure

**Wskazówki:** zdefiniuj listę zatwierdzonych zasobów platformy Azure i zatwierdzone oprogramowanie dla zasobów obliczeniowych na podstawie potrzeb biznesowych organizacji.

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Monitorowanie niezatwierdzonych zasobów platformy Azure

**Wskazówki:** Użyj Azure Policy, aby wprowadzić ograniczenia dotyczące typu zasobów, które mogą być tworzone w subskrypcjach klientów przy użyciu następujących wbudowanych definicji zasad:
-   Niedozwolone typy zasobów 

-   Dozwolone typy zasobów

Użyj Azure Resource Graph, aby odpytywać/odnajdywać zasoby w ramach subskrypcji. Upewnij się, że wszystkie zasoby platformy Azure obecne w środowisku zostały zatwierdzone na podstawie wymagań biznesowych organizacji.

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

- [Jak tworzyć zapytania za pomocą usługi Azure Graph](../governance/resource-graph/first-query-portal.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6. Monitorowanie niezatwierdzonych aplikacji w ramach zasobów obliczeniowych

**Wskazówki:** użyj Azure Automation Śledzenie zmian i spis, aby dowiedzieć się, które oprogramowanie jest zainstalowane w twoim środowisku. 

Zbieranie i wyświetlanie spisu oprogramowania, plików, demonów systemu Linux, usług systemu Windows i kluczy rejestru systemu Windows na komputerach oraz monitorowanie niezatwierdzonych aplikacji. 

Śledź konfiguracje maszyn, aby pomóc w kwestiach operacyjnych w całym środowisku i lepiej zrozumieć stan maszyn.

- [Jak włączyć spis maszyn wirtualnych platformy Azure](../automation/automation-tutorial-installed-software.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7. Usuwanie niezatwierdzonych zasobów i aplikacji platformy Azure

**Wskazówki:** użyj Azure Automation Śledzenie zmian i spis, aby dowiedzieć się, które oprogramowanie jest zainstalowane w twoim środowisku. 

Zbieranie i wyświetlanie spisu oprogramowania, plików, demonów systemu Linux, usług systemu Windows i kluczy rejestru systemu Windows na komputerach oraz monitorowanie niezatwierdzonych aplikacji. 

Śledź konfiguracje maszyn, aby pomóc w wskazaniu problemów operacyjnych w całym środowisku i lepszym zrozumieniu stanu maszyn.

- [Jak włączyć spis maszyn wirtualnych platformy Azure](../automation/automation-tutorial-installed-software.md)

- [Jak używać monitorowania integralności plików](../security-center/security-center-file-integrity-monitoring.md)

- [Opis usługi Azure Change Tracking](../automation/change-tracking/overview.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="68-use-only-approved-applications"></a>6.8: Używaj tylko zatwierdzonych aplikacji

**Wskazówki:** użyj Azure Automation Śledzenie zmian i spis, aby dowiedzieć się, które oprogramowanie jest zainstalowane w twoim środowisku. 

Zbieranie i wyświetlanie spisu oprogramowania, plików, demonów systemu Linux, usług systemu Windows i kluczy rejestru systemu Windows na komputerach oraz monitorowanie niezatwierdzonych aplikacji. 

Śledź konfiguracje maszyn, aby pomóc w wskazaniu problemów operacyjnych w całym środowisku i lepszym zrozumieniu stanu maszyn.

Włącz adaptacyjną analizę aplikacji w Security Center dla aplikacji, które istnieją w Twoim środowisku.

- [Jak włączyć spis maszyn wirtualnych platformy Azure](../automation/automation-tutorial-installed-software.md)

- [Jak używać funkcji adaptacyjnego sterowania Azure Security Center aplikacji](../security-center/security-center-adaptive-application.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="69-use-only-approved-azure-services"></a>6.9. Używaj tylko zatwierdzonych usług platformy Azure

**Wskazówki:** Użyj Azure Policy, aby wprowadzić ograniczenia dotyczące typu zasobów, które mogą być tworzone w subskrypcjach klientów przy użyciu następujących wbudowanych definicji zasad:

- Niedozwolone typy zasobów

- Dozwolone typy zasobów

Użyj Azure Resource Graph, aby odpytywać/odnajdywać zasoby w ramach subskrypcji. Upewnij się, że wszystkie zasoby platformy Azure obecne w środowisku zostały zatwierdzone.

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

- [Jak odmówić określonego typu zasobu za pomocą Azure Policy](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10: Utrzymywanie spisu zatwierdzonych tytułów oprogramowania

**Wskazówki:** użyj Azure Policy, aby wprowadzić ograniczenia dotyczące typu zasobów, które mogą być tworzone w subskrypcjach przy użyciu wbudowanych definicji zasad.

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Ograniczanie możliwości interakcji użytkowników z Azure Resource Manager

**Wskazówki:** Użyj dostępu warunkowego platformy Azure, aby ograniczyć użytkownikom możliwość interakcji z usługą Azure Resource Manager przez skonfigurowanie "Blokuj dostęp" dla aplikacji "Microsoft Azure Management".
- [Jak skonfigurować dostęp warunkowy, aby zablokować dostęp do Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="612-limit-users-ability-to-execute-scripts-in-compute-resources"></a>6.12: Ograniczanie możliwości wykonywania skryptów w zasobach obliczeniowych przez użytkowników

**Wskazówki:** Azure Kubernetes Service (AKS) nie zapewnia rozwiązania do zarządzania tożsamościami, w którym są przechowywane zwykłe konta użytkowników i hasła. Zamiast tego użyj Azure Active Directory (Azure AD) jako zintegrowanego rozwiązania do zarządzania tożsamościami dla klastrów usługi AKS.

Udzielanie użytkownikom lub grupom dostępu do zasobów platformy Kubernetes w przestrzeni nazw lub w klastrze przy użyciu integracji z usługą Azure AD.

Moduł Azure AD PowerShell umożliwia wykonywanie zapytań ad hoc w celu odnajdywania kont, które są członkami grup administracyjnych usługi AKS, i używania go do regularnego uzgadniania dostępu. Interfejs wiersza polecenia platformy Azure umożliwia korzystanie z operacji, takich jak "Uzyskiwanie poświadczeń dostępu dla zarządzanego klastra Kubernetes. Implementowanie Security Center zarządzania tożsamościami i dostępem.

- [Zarządzanie usługą AKS przy użyciu interfejsu wiersza polecenia platformy Azure](/cli/azure/aks)

- [Opis integracji usług AKS i Azure AD](concepts-identity.md)

- [Jak zintegrować usługę AKS z usługą Azure AD](azure-ad-integration-cli.md)

- [Jak uzyskać rolę katalogu w usłudze Azure AD przy użyciu programu PowerShell](/powershell/module/azuread/get-azureaddirectoryrole)

- [Jak uzyskać członków roli katalogu w usłudze Azure AD przy użyciu programu PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember)

- [Jak monitorować tożsamość i dostęp za pomocą Azure Security Center](../security-center/security-center-identity-access.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Fizyczne lub logiczne rozdzielenie aplikacji wysokiego ryzyka

**Wskazówki:** Użyj funkcji Azure Kubernetes Service (AKS), aby logicznie odizolować zespoły i obciążenia w tym samym klastrze dla najmniejszej liczby uprawnień ograniczonych do zasobów wymaganych przez każdy zespół. 

Zaim implementuj przestrzeń nazw na platformie Kubernetes, aby utworzyć granicę izolacji logicznej. Użyj Azure Policy aliasów w przestrzeni nazw "Microsoft.ContainerService", aby utworzyć niestandardowe zasady inspekcji lub wymuszania konfiguracji wystąpień usługi Azure Kubernetes Service (AKS). 

Przejrzyj i zaim implementuj dodatkowe funkcje i zagadnienia dotyczące izolacji i wieloskładnikowego rozwiązania Kubernetes, aby uwzględnić następujące zagadnienia: planowanie, sieć, uwierzytelnianie/autoryzacja i kontenery. Należy również używać oddzielnych subskrypcji i grup zarządzania na użytek tworzenia, testowania i produkcji. Oddziel klastry usługi AKS z sieciami wirtualnymi, podsieciami, które są odpowiednio otagowane i zabezpieczone za pomocą Web Application Firewall (WAF).

- [Dowiedz się więcej o najlepszych rozwiązaniach dotyczących izolacji klastra w u usługach AKS](operator-best-practices-cluster-isolation.md)

- [Jak utworzyć dodatkowe subskrypcje platformy Azure](../cost-management-billing/manage/create-subscription.md)

- [Jak utworzyć Grupy zarządzania](../governance/management-groups/create-management-group-portal.md)

- [Understand best practices for network connectivity and security in AKS](operator-best-practices-network.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

## <a name="secure-configuration"></a>Bezpieczna konfiguracja

*Aby uzyskać więcej informacji, zobacz [Test porównawczy zabezpieczeń platformy Azure: bezpieczna konfiguracja.](../security/benchmarks/security-control-secure-configuration.md)*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1. Ustanów bezpieczne konfiguracje dla wszystkich zasobów platformy Azure

**Wskazówki:** użyj Azure Policy aliasów w przestrzeni nazw "Microsoft.ContainerService", aby utworzyć niestandardowe zasady inspekcji lub wymuszania konfiguracji wystąpień usługi Azure Kubernetes Service (AKS). Używanie wbudowanych definicji Azure Policy danych.

Przykłady wbudowanych definicji zasad dla usług AKS obejmują:

- Wymuszaj ruch przychodzący HTTPS w klastrze Kubernetes

- W usługach Kubernetes należy zdefiniować autoryzowane zakresy adresów IP

- W usługach Kubernetes Access Control na podstawie ról (RBAC)

- Zapewnij stosowanie tylko dozwolonych obrazów kontenerów w klastrze Kubernetes

Wyeksportuj szablon konfiguracji usługi AKS w pliku JavaScript Object Notation (JSON) przy użyciu Azure Resource Manager. Okresowo sprawdzaj je, aby upewnić się, że te konfiguracje spełniają wymagania dotyczące zabezpieczeń w organizacji. Użyj zaleceń z witryny Azure Security Center jako bezpiecznej konfiguracji odniesienia dla zasobów platformy Azure. 

- [Jak skonfigurować zasady zabezpieczeń zasobnika usługi AKS i zarządzać nimi](use-pod-security-policies.md)

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="72-establish-secure-operating-system-configurations"></a>7.2. Ustanawianie bezpiecznych konfiguracji systemu operacyjnego

**Wskazówki:** Klastry Azure Kubernetes Clusters (AKS) są wdrażane na maszynach wirtualnych hostów z zoptymalizowanym pod kątem zabezpieczeń systemem operacyjnym. System operacyjny hosta ma włączone dodatkowe kroki wzmacniania zabezpieczeń w celu zmniejszenia obszaru ataków i umożliwia bezpieczne wdrażanie kontenerów. 

Platforma Azure stosuje codzienne poprawki (w tym poprawki zabezpieczeń) do hostów maszyn wirtualnych usługi AKS z niektórymi poprawkami wymagającymi ponownego uruchomienia. Klienci są odpowiedzialni za planowanie ponownego uruchamiania hosta maszyny wirtualnej usługi AKS zgodnie z potrzebami. 

- [Wzmacnianie zabezpieczeń systemu operacyjnego hosta węzła agenta usługi AKS](security-hardened-vm-host-image.md)

- [Opis wzmacniania zabezpieczeń na hostach maszyn wirtualnych usługi AKS](security-hardened-vm-host-image.md)

**Odpowiedzialność**: Współużytkowane

**Azure Security Center monitorowania:** Brak

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Utrzymywanie bezpiecznych konfiguracji zasobów platformy Azure

**Wskazówki:** Zabezpieczanie klastra Azure Kubernetes Service (AKS) przy użyciu zasad zabezpieczeń zasobników.  Ogranicz zasobniki, które można zaplanować, aby zwiększyć bezpieczeństwo klastra. 

Zasobniki, które żądają zasobów, które są niedozwolone, nie mogą być uruchamiane w klastrze usługi AKS. 

Użyj również efektów Azure Policy [deny] i [deploy if not exist], aby wymusić bezpieczne ustawienia dla zasobów platformy Azure związanych z wdrożeniami usługi AKS (takich jak sieci wirtualne, podsieci, zapory platformy Azure, konta magazynu itd.). 

Tworzenie niestandardowych Azure Policy przy użyciu aliasów z następujących przestrzeni nazw: 

- Microsoft.ContainerService

- Microsoft.Network

Dodatkowe informacje są dostępne pod linkami, do których się odwołujesz.

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

- [Understand Azure Policy Effects](../governance/policy/concepts/effects.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Obsługa bezpiecznych konfiguracji systemu operacyjnego

**Wskazówki:** klastry Azure Kubernetes Service (AKS) są wdrażane na maszynach wirtualnych hosta z zoptymalizowanym pod kątem zabezpieczeń systemu operacyjnego. System operacyjny hosta ma wbudowane dodatkowe kroki wzmacniania zabezpieczeń w celu zmniejszenia obszaru ataków i umożliwia bezpieczne wdrażanie kontenerów. 

Zapoznaj się z listą kontrolek usługi Center for Internet Security (CIS), które są wbudowane w system operacyjny hosta.  

- [Wzmacnianie zabezpieczeń systemu operacyjnego hosta węzła agenta usługi AKS](security-hardened-vm-host-image.md)

- [Informacje o konfiguracji stanu klastrów usługi AKS](https://docs.microsoft.com/azure/aks/concepts-clusters-workloads#control-plane)

- [Opis wzmacniania zabezpieczeń na hostach maszyn wirtualnych usługi AKS](security-hardened-vm-host-image.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Bezpieczne przechowywanie konfiguracji zasobów platformy Azure

**Wskazówki:** Użyj Azure Repos, aby bezpiecznie przechowywać konfiguracje i zarządzać nimi, jeśli używasz niestandardowych Azure Policy konfiguracji. Wyeksportuj szablon konfiguracji Azure Kubernetes Service (AKS) w pliku JavaScript Object Notation (JSON) przy użyciu Azure Resource Manager. Okresowo je sprawdzaj, aby upewnić się, że konfiguracje spełniają wymagania dotyczące zabezpieczeń w organizacji.

Zaim implementuj rozwiązania innych firm, takie jak Terraform, aby utworzyć plik konfiguracji, który deklaruje zasoby dla klastra Kubernetes. Wdrożenie usługi AKS można zabezpieczyć, wdrażając najlepsze rozwiązania w zakresie zabezpieczeń i przechowując konfigurację jako kod w zabezpieczonej lokalizacji.

- [Definiowanie klastra Kubernetes](/azure/developer/terraform/create-k8s-cluster-with-tf-and-aks#define-a-kubernetes-cluster)

- [Wzmacnianie zabezpieczeń systemu operacyjnego hosta węzła agenta usługi AKS](security-hardened-vm-host-image.md)

- [Jak przechowywać kod w Azure DevOps](/azure/devops/repos/git/gitworkflow)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Bezpieczne przechowywanie niestandardowych obrazów systemu operacyjnego

**Wskazówki:** Nie dotyczy Azure Kubernetes Service (AKS). Domyślnie usługi AKS zapewnia zoptymalizowany pod kątem zabezpieczeń system operacyjny hosta. Nie ma bieżącej opcji wyboru alternatywnego lub niestandardowego systemu operacyjnego.

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7. Wdrażanie narzędzi do zarządzania konfiguracją dla zasobów platformy Azure

**Wskazówki:** Użyj usługi Azure Policy, aby wprowadzić ograniczenia dotyczące typu zasobów, które mogą być tworzone w subskrypcjach przy użyciu wbudowanych definicji zasad, a także aliasów Azure Policy w przestrzeni nazw "Microsoft.ContainerService". 

Tworzenie zasad niestandardowych w celu inspekcji i wymuszania konfiguracji systemu. Opracowywanie procesu i potoku do zarządzania wyjątkami zasad.

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

- [Jak używać aliasów](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure#aliases)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8. Wdrażanie narzędzi do zarządzania konfiguracją dla systemów operacyjnych

**Wskazówki:** Azure Kubernetes Service (AKS) są wdrażane na maszynach wirtualnych hosta z zoptymalizowanym pod kątem zabezpieczeń systemem operacyjnym. System operacyjny hosta ma wbudowane dodatkowe kroki wzmacniania zabezpieczeń w celu zmniejszenia obszaru ataków i umożliwia bezpieczne wdrażanie kontenerów. 

Zapoznaj się z listą kontrolek usługi Center for Internet Security (CIS), które są wbudowane w hosty usługi AKS.  

- [Wzmacnianie zabezpieczeń systemu operacyjnego hosta węzła agenta usługi AKS](security-hardened-vm-host-image.md)

- [Opis wzmacniania zabezpieczeń na hostach maszyn wirtualnych usługi AKS](security-hardened-vm-host-image.md)

- [Informacje o konfiguracji stanu klastrów usługi AKS](https://docs.microsoft.com/azure/aks/concepts-clusters-workloads#control-plane)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Implementowanie zautomatyzowanego monitorowania konfiguracji dla zasobów platformy Azure

**Wskazówki:** użyj Security Center, aby przeprowadzić skanowanie bazowe zasobów związanych z wdrożeniami usługi Azure Kubernetes Service (AKS). Przykładowe zasoby to między innymi sam klaster usługi AKS, sieć wirtualna, w której wdrożono klaster usługi AKS, konto usługi Azure Storage używane do śledzenia stanu narzędzia Terraform lub wystąpienia programu Azure Key Vault używane do kluczy szyfrowania dla dysków danych i systemu operacyjnego klastra AKS.

- [Jak korygować zalecenia w Azure Security Center](../security-center/security-center-remediate-recommendations.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Implementowanie zautomatyzowanego monitorowania konfiguracji dla systemów operacyjnych

**Wskazówki:** użyj Security Center kontenera w sekcji "Aplikacje obliczeniowe", aby przeprowadzić skanowanie bazowe klastrów Azure Kubernetes Service &amp; (AKS). 

Otrzymuj powiadomienia na pulpicie nawigacyjnym Security Center, gdy zostaną znalezione problemy z konfiguracją lub luki w zabezpieczeniach. Wymaga to włączenia opcjonalnego pakietu container registries, co Security Center skanowanie obrazu.  

- [Omówienie rekomendacji dotyczących kontenera usługi Azure Security Center](../security-center/container-security.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="711-manage-azure-secrets-securely"></a>7.11: Bezpieczne zarządzanie wpisami tajnymi platformy Azure

**Wskazówki:** Integrowanie Azure Key Vault z klastrem Azure Kubernetes Service (AKS) przy użyciu dysku FlexVolume. Użyj Azure Key Vault do przechowywania i regularnego obracania wpisów tajnych, takich jak poświadczenia, klucze konta magazynu lub certyfikaty. Sterownik FlexVolume umożliwia klastrowi AKS natywne pobieranie poświadczeń z usługi Key Vault i bezpieczne zapewnianie ich tylko dla zasobnika, który żąda. Tożsamość zarządzana zasobnika umożliwia zażądanie dostępu do Key Vault i pobranie wymaganych poświadczeń za pośrednictwem sterownika FlexVolume. Upewnij się Key Vault że włączono usuwanie nie softowe. 

Ogranicz narażenie poświadczeń, nie definiując poświadczeń w kodzie aplikacji. 

Unikaj używania poświadczeń stałych lub udostępnionych. 

- [Pojęcia dotyczące zabezpieczeń aplikacji i klastrów w usłudze Azure Kubernetes Service (AKS)](concepts-security.md)

- [Jak używać usługi Key Vault z klastrem AKS](https://docs.microsoft.com/azure/aks/developer-best-practices-pod-security#limit-credential-exposure)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Bezpieczne i automatyczne zarządzanie tożsamościami

**Wskazówki:** nie należy definiować poświadczeń w kodzie aplikacji jako najlepsze rozwiązanie w zakresie zabezpieczeń. Użyj tożsamości zarządzanych dla zasobów platformy Azure, aby pozwolić zasobnikowi na uwierzytelnianie się względem dowolnej usługi na platformie Azure, która go obsługuje, w tym Azure Key Vault. Zasobnik ma przypisaną tożsamość platformy Azure na potrzeby uwierzytelniania w usłudze Azure Active Directory (Azure AD) i odbierania tokenu cyfrowego, który można prezentować innym usługom platformy Azure, które sprawdzają, czy zasobnik jest autoryzowany do uzyskiwania dostępu do usługi i wykonywania wymaganych akcji.

Należy pamiętać, że tożsamości zarządzane zasobników są przeznaczone tylko do użycia z zasobnikami systemu Linux i obrazami kontenerów. Aprowizować Azure Key Vault do przechowywania i pobierania kluczy cyfrowych i poświadczeń. Klucze, takie jak te używane do szyfrowania dysków systemu operacyjnego, dane klastra usługi AKS mogą być przechowywane w Azure Key Vault.

Jednostki usługi mogą być również używane w klastrach usługi AKS. Jednak klastry korzystające z jednostki usługi mogą ostatecznie osiągnąć stan, w którym należy odnowić jednostkę usługi, aby zapewnić działanie klastra. Zarządzanie jednostkami usługi zwiększa złożoność, dlatego łatwiej jest używać tożsamości zarządzanych. Te same wymagania dotyczące uprawnień dotyczą zarówno jednostki usługi, jak i tożsamości zarządzanych.

- [Informacje o tożsamościach zarządzanych i Key Vault użyciu Azure Kubernetes Service (AKS)](https://docs.microsoft.com/azure/aks/developer-best-practices-pod-security#limit-credential-exposure)

- [Tożsamość zasobnika usługi Azure AD](https://github.com/Azure/aad-pod-identity)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Eliminowanie niezamierzonego narażenia na poświadczenia

**Wskazówki:** implementowanie skanera poświadczeń w celu identyfikowania poświadczeń w kodzie. Skaner poświadczeń zachęca również do przenoszenia odnalezionych poświadczeń do bezpieczniejszych lokalizacji, takich Azure Key Vault z zaleceniami.

Ogranicz narażenie poświadczeń, nie definiując poświadczeń w kodzie aplikacji. i unikaj używania poświadczeń udostępnionych. Azure Key Vault do przechowywania i pobierania kluczy cyfrowych i poświadczeń. Użyj tożsamości zarządzanych dla zasobów platformy Azure, aby pozwolić zasobnikowi na żądanie dostępu do innych zasobów. 

- [Jak skonfigurować skaner poświadczeń](https://secdevtools.azurewebsites.net/helpcredscan.html)

- [Najlepsze rozwiązania dla deweloperów dotyczące zabezpieczeń zasobników](developer-best-practices-pod-security.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

## <a name="malware-defense"></a>Ochrona przed złośliwym oprogramowaniem

*Aby uzyskać więcej informacji, zobacz [Test porównawczy zabezpieczeń platformy Azure: Malware Defense.](../security/benchmarks/security-control-malware-defense.md)*

### <a name="81-use-centrally-managed-antimalware-software"></a>8.1: Korzystanie z centralnie zarządzanego oprogramowania do ochrony przed złośliwym oprogramowaniem

**Wskazówki:** AKS zarządza cyklem życia i operacjami węzłów agentów w Twoim imieniu — modyfikowanie zasobów IaaS skojarzonych z węzłami agentów nie jest obsługiwane. Jednak w przypadku węzłów systemu Linux można użyć zestawów demonów do zainstalowania niestandardowego oprogramowania, takiego jak rozwiązanie chroniące przed złośliwym oprogramowaniem.

- [Przewodnik informacyjny dotyczący alertów zabezpieczeń](../security-center/alerts-reference.md)

- [Alerty dla kontenerów — Azure Kubernetes Service klastry](https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-akscluster)

- [Wspólna odpowiedzialność za aks i zestawy demonów](https://docs.microsoft.com/azure/aks/support-policies#shared-responsibility)

**Odpowiedzialność**: Współużytkowane

**Azure Security Center monitorowania:** Brak

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Pliki przed skanowaniem, które mają zostać przekazane do zasobów platformy Azure bez obliczeń

**Wskazówki:** wstępne skanowanie wszystkich plików przekazywanych do zasobów AKS. Wykrywanie zagrożeń Security Center usług danych za pomocą usługi Security Center wykrywanie złośliwego oprogramowania przekazanego na konta magazynu w przypadku używania konta usługi Azure Storage jako magazynu danych lub śledzenia stanu narzędzia Terraform dla klastra usługi AKS. 

- [Understand Azure Security Center's Threat detection for data services (Opis wykrywania zagrożeń dla usług danych)](../security-center/azure-defender.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="83-ensure-antimalware-software-and-signatures-are-updated"></a>8.3: Upewnij się, że oprogramowanie do ochrony przed złośliwym oprogramowaniem i podpisy zostały zaktualizowane

**Wskazówki:** AKS zarządza cyklem życia i operacjami węzłów agenta w Twoim imieniu — modyfikowanie zasobów IaaS skojarzonych z węzłami agenta nie jest obsługiwane. Jednak w przypadku węzłów systemu Linux można użyć zestawów demonów do zainstalowania niestandardowego oprogramowania, takiego jak rozwiązanie chroniące przed złośliwym oprogramowaniem.

- [Przewodnik informacyjny dotyczący alertów zabezpieczeń](../security-center/alerts-reference.md)

- [Alerty dla kontenerów — Azure Kubernetes Service klastry](https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-akscluster)

- [Wspólna odpowiedzialność i zestawy demonów WKS](https://docs.microsoft.com/azure/aks/support-policies#shared-responsibility)

**Odpowiedzialność**: Współużytkowane

**Azure Security Center monitorowania:** Brak

## <a name="data-recovery"></a>Odzyskiwanie danych

*Aby uzyskać więcej informacji, zobacz [Test porównawczy zabezpieczeń platformy Azure: odzyskiwanie danych.](../security/benchmarks/security-control-data-recovery.md)*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Zapewnianie regularnych automatycznych kopii zapasowej

**Wskazówki:** W tym celu należy utworzyć kopię zapasową danych przy użyciu odpowiedniego narzędzia dla typu magazynu, takiego jak Velero, które może ować kopię zapasową trwałych woluminów wraz z dodatkowymi zasobami i konfiguracjami klastra. Okresowo sprawdzaj integralność i zabezpieczenia tych kopii zapasowych. 

Usuń stan z aplikacji przed tworzeniem kopii zapasowej. W przypadkach, gdy nie można tego zrobić, należy wykonać kopię zapasową danych z trwałych woluminów i regularnie testować operacje przywracania w celu sprawdzenia integralności danych i wymaganych procesów.

- [Najlepsze rozwiązania dotyczące magazynu i kopii zapasowych w u usługi AKS](operator-best-practices-storage.md)

- [Najlepsze rozwiązania dotyczące ciągłości działania i odzyskiwania po awarii w u usługi AKS](operator-best-practices-multi-region.md)

- [Opis Azure Site Recovery](../site-recovery/site-recovery-overview.md)

- [Jak skonfigurować narzędzie Velero na platformie Azure](https://github.com/vmware-tanzu/velero-plugin-for-microsoft-azure/blob/master/README.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2. Wykonywanie pełnych kopii zapasowych systemu i tworzenie kopii zapasowych dowolnych kluczy zarządzanych przez klienta

**Wskazówki:** należy utworzyć kopię zapasową danych przy użyciu odpowiedniego narzędzia dla typu magazynu, takiego jak Velero, które umożliwia kopię zapasową trwałych woluminów wraz z dodatkowymi zasobami i konfiguracjami klastra. 

Regularne wykonywanie automatycznych kopii zapasowych certyfikatów Key Vault, kluczy, kont magazynu zarządzanego i wpisów tajnych za pomocą poleceń programu PowerShell. 

- [Jak utworzyć kopię zapasową Key Vault certyfikatów](/powershell/module/azurerm.keyvault/backup-azurekeyvaultcertificate)

- [Jak utworzyć kopię zapasową Key Vault kluczy](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

- [Jak utworzyć kopię zapasową Key Vault magazynu zarządzanego](/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount)

- [Jak utworzyć kopię zapasową Key Vault tajnych](/powershell/module/azurerm.keyvault/backup-azurekeyvaultsecret)

- [Jak włączyć Azure Backup](/azure/backup/)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Zweryfikuj wszystkie kopie zapasowe, w tym klucze zarządzane przez klienta

**Wskazówki:** okresowe przywracanie danych zawartości w ramach kopii zapasowej Velero. W razie potrzeby przetestuj przywracanie do izolowanej sieci wirtualnej.

Okresowo przeprowadzaj przywracanie danych Key Vault certyfikatów, kluczy, zarządzanych kont magazynu i wpisów tajnych za pomocą poleceń programu PowerShell.

- [Jak przywrócić Key Vault certyfikatów](/powershell/module/az.keyvault/restore-azkeyvaultcertificate)

- [Jak przywrócić Key Vault kluczy](/powershell/module/az.keyvault/restore-azkeyvaultkey)

- [Jak przywrócić Key Vault magazynu zarządzanego](/powershell/module/az.keyvault/backup-azkeyvaultmanagedstorageaccount)

- [Jak przywrócić Key Vault tajne](/powershell/module/az.keyvault/restore-azkeyvaultsecret)

- [Jak odzyskać pliki z kopii zapasowej maszyny wirtualnej platformy Azure](/azure/backup/backup-azure-restore-files-from-vm)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4. Zapewnianie ochrony kopii zapasowych i kluczy zarządzanych przez klienta

**Wskazówki:** W tym celu należy utworzyć kopię zapasową danych przy użyciu odpowiedniego narzędzia dla typu magazynu, takiego jak Velero, które może ować kopię zapasową trwałych woluminów wraz z dodatkowymi zasobami i konfiguracjami klastra. 

Włącz Soft-Delete w Key Vault, aby chronić klucze przed przypadkowym lub złośliwym usunięciem, jeśli program Azure Key Vault jest używany z programem na Azure Kubernetes Service (AKS).

- [Opis usługi Azure szyfrowanie usługi Storage](../storage/common/storage-service-encryption.md)

- [Jak włączyć Soft-Delete w Key Vault](https://docs.microsoft.com/azure/storage/blobs/soft-delete-blob-overview?tabs=azure-portal)

**Odpowiedzialność**: Klient

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

**Wskazówki:** najpierw należy ustalić, które alerty należy zbadać, Security Center przypisaną do alertów ważność. Ważność jest oparta na tym, jak pewna Security Center znajduje się w znajdowaniu lub analizie użytej do wystawienia alertu, a także na poziomie ufności, że za działaniem, które doprowadziło do tego alertu, znajduje się złośliwe intencje.
Wyraźnie oznaczaj subskrypcje (na przykład produkcyjne lub nieprodukcyjne) i utwórz system nazewnictwa, aby jasno identyfikować i kategoryzować zasoby platformy Azure.

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="103-test-security-response-procedures"></a>10.3: Testowanie procedur reagowania na zabezpieczenia

**Wskazówki:** Przećwiczyj ćwiczenia, aby regularnie testować możliwości reagowania na zdarzenia w systemach. Identyfikowanie słabych punktów i luk oraz poprawianie planów reagowania na zdarzenia zgodnie z potrzebami.

- [Przewodnik po programach testowych, szkoleniowych i szkoleniowych dla planów i możliwości IT](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4. Podaj szczegóły kontaktowe zdarzenia zabezpieczeń i skonfiguruj powiadomienia o alertach dla zdarzeń zabezpieczeń

**Wskazówki:** informacje kontaktowe dotyczące zdarzenia zabezpieczeń będą używane przez firmę Microsoft do kontaktowania się z Tobą, jeśli Centrum zabezpieczeń firmy Microsoft (MSRC) odkryje, że dostęp do danych klienta został uzyskany przez niedozwoloną lub niedozwoloną stronę. 

Przejrzyj zdarzenia po fakcie, aby upewnić się, że problemy zostały rozwiązane.

- [Jak ustawić kontakt Azure Security Center zabezpieczeń](../security-center/security-center-provide-security-contact-details.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5. Dołączanie alertów zabezpieczeń do systemu reagowania na zdarzenia

**Wskazówki:** eksportowanie Security Center i zaleceń przy użyciu funkcji eksportu ciągłego. Eksport ciągły umożliwia eksportowanie alertów i zaleceń ręcznie lub w ciągły sposób. 

Wybierz łącznik Security Center danych, aby przesyłać strumieniowo alerty do Azure Sentinel zgodnie z potrzebami i na podstawie wymagań biznesowych organizacji.

- [Jak skonfigurować eksport ciągły](../security-center/continuous-export.md)

- [Jak przesłać strumieniowo alerty do usługi Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatyzowanie odpowiedzi na alerty zabezpieczeń

**Wskazówki:** Użyj funkcji automatyzacji przepływu pracy w Security Center, aby automatycznie wyzwalać odpowiedzi za pośrednictwem "Logic Apps" na temat alertów zabezpieczeń i zaleceń.

- [Jak skonfigurować automatyzację przepływu pracy i Logic Apps](../security-center/workflow-automation.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

## <a name="penetration-tests-and-red-team-exercises"></a>Testy penetracyjne i ćwiczenia typu „red team”

*Aby uzyskać więcej informacji, zobacz [test porównawczy zabezpieczeń platformy Azure: testy penetracyjne i ćwiczenia zespołu czerwonego.](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Przeprowadzaj regularne testy penetrowania zasobów platformy Azure i zapewnij korygowanie wszystkich krytycznych ustaleń zabezpieczeń

**Wskazówki:** postępuj zgodnie z zasadami firmy Microsoft w zakresie zaangażowania, aby upewnić się, że testy penetracyjne nie naruszały zasad firmy Microsoft. Dodatkowe informacje na temat strategii firmy Microsoft i wykonywania czerwonego zespołu i testów penetracyjnych na żywo w odniesieniu do infrastruktury, usług i aplikacji w chmurze zarządzanej przez firmę Microsoft, za pomocą przywołynych linków.

- [Reguły testów penetracyjnych zaangażowania](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Testy typu „red team” w chmurze firmy Microsoft](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Odpowiedzialność**: Współużytkowane

**Azure Security Center monitorowania:** Brak

## <a name="next-steps"></a>Następne kroki

- Zobacz [Omówienie testu porównawczego zabezpieczeń platformy Azure w wersji 2](/azure/security/benchmarks/overview)
- Dowiedz się więcej o [punktach odniesienia zabezpieczeń platformy Azure](/azure/security/benchmarks/security-baselines-overview)
