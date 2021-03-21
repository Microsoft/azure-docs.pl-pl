---
title: Podstawowa baza zabezpieczeń Azure dla usługi Azure Kubernetes
description: Linia bazowa zabezpieczeń usługi Azure Kubernetes Service zawiera wskazówki i zasoby dotyczące procedur związanych z wdrażaniem zaleceń dotyczących zabezpieczeń określonych w teście zabezpieczeń Azure.
author: msmbaldwin
ms.service: container-service
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: de32de5fc78919154bade3895cab6da82b175a93
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101740639"
---
# <a name="azure-security-baseline-for-azure-kubernetes-service"></a>Podstawowa baza zabezpieczeń Azure dla usługi Azure Kubernetes

Ta linia bazowa zabezpieczeń stosuje wskazówki z [wersji 1,0 usługi Azure Security test](../security/benchmarks/overview-v1.md) do usługi Azure Kubernetes. Test porównawczy zabezpieczeń platformy Azure zawiera zalecenia dotyczące sposobu zabezpieczania rozwiązań w chmurze na platformie Azure.
Zawartość jest pogrupowana według **kontroli zabezpieczeń** zdefiniowanych przez program Azure Security test i powiązane wskazówki dotyczące usługi Azure Kubernetes. **Formanty** nie mają zastosowania do usługi Azure Kubernetes zostały wykluczone.

 
Aby dowiedzieć się, jak usługa Azure Kubernetes jest całkowicie mapowana na test porównawczy zabezpieczeń platformy Azure, zobacz [pełny plik mapowania podstawy zabezpieczeń usługi Azure Kubernetes](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Bezpieczeństwo sieci

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: bezpieczeństwo sieci](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: Ochrona zasobów platformy Azure w ramach sieci wirtualnych

**Wskazówki**: domyślnie sieciowa Grupa zabezpieczeń i tabela tras są tworzone automatycznie za pomocą tworzenia klastra Microsoft Azure Kubernetes Service (AKS). AKS automatycznie modyfikuje sieciowe grupy zabezpieczeń dla odpowiedniego przepływu ruchu, gdy usługi są tworzone za pomocą modułów równoważenia obciążenia, mapowania portów lub tras przychodzących. Sieciowa Grupa zabezpieczeń jest automatycznie skojarzona z wirtualnymi kartami sieciowymi w węzłach klienta i w tabeli tras z podsiecią w sieci wirtualnej. 

Użyj zasad sieciowych AKS, aby ograniczyć ruch sieciowy przez definiowanie reguł ruchu przychodzącego i wychodzącego między nazwami systemu Linux w klastrze na podstawie wyboru obszarów nazw i selektorów etykiet. Użycie zasad sieciowych wymaga wtyczki Azure CNI ze zdefiniowaną siecią wirtualną i podsieciami i można ją włączyć tylko podczas tworzenia klastra. Nie można ich wdrożyć w istniejącym klastrze AKS.

Można zaimplementować prywatny klaster AKS, aby zapewnić ruch sieciowy między serwerem interfejsu API AKS i pulami węzłów pozostały tylko w sieci prywatnej. Płaszczyzna kontroli lub serwer interfejsu API znajduje się w AKSej zarządzanej subskrypcji platformy Azure i używa wewnętrznych adresów IP (RFC1918), natomiast klaster klienta lub Pula węzłów jest w ich własnej subskrypcji. Serwer i klaster lub Pula węzłów komunikują się ze sobą przy użyciu usługi Azure Private link w sieci wirtualnej serwera interfejsu API i prywatnego punktu końcowego, który jest udostępniany w podsieci klastra AKS klienta.  Alternatywnie można użyć publicznego punktu końcowego dla serwera interfejsu API AKS, ale ograniczyć dostęp za pomocą funkcji autoryzowanych zakresów adresów IP serwera interfejsu API AKS. 

- [Pojęcia dotyczące zabezpieczeń aplikacji i klastrów w usłudze Azure Kubernetes Service (AKS)](concepts-security.md)

- [Zabezpieczanie ruchu między różnymi sieciami przy użyciu zasad sieciowych w usłudze Azure Kubernetes Service (AKS)](use-network-policies.md)

- [Tworzenie prywatnego klastra usługi Azure Kubernetes Service](private-clusters.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania**: [wzorzec zabezpieczeń platformy Azure](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) jest domyślną inicjatywy zasad dla Security Center i jest podstawą dla [zaleceń Security Center](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md). Definicje Azure Policy powiązane z tym formantem są włączane automatycznie przez Security Center. Alerty związane z tym formantem mogą wymagać planu [usługi Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) dla powiązanych usług.

**Azure Policy wbudowane definicje — Microsoft. ContainerService**:

[!INCLUDE [Resource Policy for Microsoft.ContainerService 1.1](../../includes/policy/standards/asb/rp-controls/microsoft.containerservice-1-1.md)]

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1,2: Monitoruj i Rejestruj konfigurację oraz ruch sieci wirtualnych, podsieci i kart sieciowych

**Wskazówki**: Użyj Security Center i postępuj zgodnie z zaleceniami dotyczącymi ochrony sieci, aby zabezpieczyć zasoby sieciowe używane przez klastry usługi Azure Kubernetes Service (AKS). 

Włącz dzienniki przepływu sieciowych grup zabezpieczeń i Wyślij dzienniki do konta usługi Azure Storage w celu przeprowadzenia inspekcji. Możesz również wysłać dzienniki przepływów do obszaru roboczego Log Analytics, a następnie użyć Analiza ruchu, aby uzyskać wgląd w wzorce ruchu w chmurze platformy Azure w celu wizualizacji aktywności sieciowej, zidentyfikowania aktywnych plam i zagrożeń bezpieczeństwa, zrozumienia wzorców przepływu ruchu oraz wypróbowania nieprawidłowych konfiguracji sieci.

- [Informacje o zabezpieczeniach sieci zapewnianych przez Azure Security Center](../security-center/security-center-network-recommendations.md)

- [Jak włączyć dzienniki przepływu zabezpieczeń sieci](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Jak włączyć i używać Analiza ruchu](../network-watcher/traffic-analytics.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="13-protect-critical-web-applications"></a>1,3: Ochrona krytycznych aplikacji sieci Web

**Wskazówki**: Użyj usługi Azure Application Gateway firewall aplikacji sieci Web (WAF) przed KLASTRem AKS, aby zapewnić dodatkową warstwę zabezpieczeń przez filtrowanie ruchu przychodzącego do aplikacji sieci Web. Usługa Azure WAF używa zestawu reguł, który jest dostarczany przez program Open Web Application Security Project (OWASP) w celu ataków, takich jak skrypty między lokacjami lub zatrucie plików cookie względem tego ruchu. 

Użyj bramy interfejsu API do uwierzytelniania, autoryzacji, ograniczania, buforowania, transformacji i monitorowania dla interfejsów API używanych w środowisku AKS. Brama interfejsu API służy jako przód do mikrousług, oddziela klientów od mikrousług i zmniejsza złożoność mikrousług, usuwając obciążenie związane z obcinaniem.

- [Poznaj najlepsze rozwiązania dotyczące łączności sieciowej i zabezpieczeń w AKS](operator-best-practices-network.md)

- [Application Gateway kontroler transferu danych przychodzących ](../application-gateway/ingress-controller-overview.md)

- [Korzystanie z usługi Azure API Management z mikrousługami wdrożonymi w usłudze Azure Kubernetes Service](../api-management/api-management-kubernetes.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: odmowa komunikacji ze znanymi złośliwymi adresami IP

**Wskazówki**: Włącz funkcję rozproszonej odmowy usługi firmy Microsoft (DDoS) w sieciach wirtualnych, w których są wdrażane składniki usługi Azure Kubernetes Service (AKS) w celu ochrony przed atakami na DDoS.

Zainstaluj aparat zasad sieciowych i Utwórz zasady sieciowe Kubernetes w celu kontrolowania przepływu ruchu między nazwami w AKS, ponieważ domyślnie cały ruch jest dozwolony między tymi zasobnikami. Zasady sieciowe powinny być używane tylko dla węzłów i zasobników opartych na systemie Linux w AKS. Zdefiniuj reguły ograniczające komunikację pod kątem lepszego zabezpieczenia. 

Wybierz, aby zezwalać na ruch lub odmawiać go na podstawie ustawień, takich jak przypisane etykiety, przestrzeń nazw lub port ruchu. Wymagane zasady sieciowe mogą być automatycznie stosowane w przypadku, gdy w klastrze AKS są tworzone w sposób dynamiczny. 

- [Zabezpieczanie ruchu między różnymi sieciami przy użyciu zasad sieciowych w usłudze Azure Kubernetes Service (AKS)](use-network-policies.md)

- [Jak skonfigurować ochronę DDoS](../ddos-protection/manage-ddos-protection.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="15-record-network-packets"></a>1,5: rejestrowanie pakietów sieciowych

**Wskazówki**: Użyj funkcji przechwytywania pakietów Network Watcher zgodnie z wymaganiami, aby przebadać nietypowe działanie. 

Network Watcher jest włączana automatycznie w regionie sieci wirtualnej podczas tworzenia lub aktualizowania sieci wirtualnej w ramach subskrypcji. Można również tworzyć nowe wystąpienia Network Watcher przy użyciu programu PowerShell, interfejsu wiersza polecenia platformy Azure, API REST lub metody klienta Azure Resource Manager

- [Jak włączyć Network Watcher](../network-watcher/network-watcher-create.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: wdrażanie opartych na sieci systemów zapobiegania wykrywaniem i dostępem intruzów (identyfikatorów/adresów IP)

**Wskazówki**: Zabezpieczanie klastra usługi Azure Kubernetes Service (AKS) przy użyciu usługi Azure Application Gateway włączonej przy użyciu zapory aplikacji sieci Web (WAF). 

Jeśli wykrywanie i/lub Zapobieganie włamaniom nie są wymagane na podstawie inspekcji ładunku lub analizy zachowań, nie jest to konieczne, Application Gateway platformy Azure z usługą WAF można używać i konfigurować w trybie wykrywania w celu rejestrowania alertów i zagrożeń lub "trybu zapobiegania", aby aktywnie blokować wykryte wtargnięcie i ataki.

- [Poznaj najlepsze rozwiązania dotyczące zabezpieczania klastra AKS za pomocą WAF](https://docs.microsoft.com/azure/aks/operator-best-practices-network#secure-traffic-with-a-web-application-firewall-waf)

- [Jak wdrożyć usługę Azure Application Gateway (Azure WAF)](../web-application-firewall/ag/application-gateway-web-application-firewall-portal.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: Minimalizacja złożoności i kosztów administracyjnych reguł zabezpieczeń sieci

**Wskazówki**: Użyj tagów usługi sieci wirtualnej, aby zdefiniować kontrolę dostępu do sieci dla sieciowych grup zabezpieczeń skojarzonych z wystąpieniami usługi Azure Kubernetes Service (AKS). Podczas tworzenia reguł zabezpieczeń można używać tagów usługi zamiast określonych adresów IP, aby zezwolić na ruch dla odpowiedniej usługi lub go odmówić, określając nazwę tagu usługi. 

Firma Microsoft zarządza prefiksami adresów, które obejmują tag usługi, i automatycznie aktualizuje tag usługi jako adresy.

Zastosuj tag platformy Azure do pul węzłów w klastrze AKS. Są one inne niż Tagi usługi sieci wirtualnej i są stosowane do każdego węzła w puli węzłów i zachowują się za pomocą uaktualnień. 

- [Zrozumienie i używanie tagów usługi](../virtual-network/service-tags-overview.md)

- [Zrozumienie sieciowych grup zabezpieczeń dla AKS](support-policies.md)

- [Sterowanie ruchem wychodzącym węzłów klastra w usłudze Azure Kubernetes Service (AKS)](limit-egress-traffic.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: Obsługa standardowych konfiguracji zabezpieczeń dla urządzeń sieciowych

**Wskazówki**: Definiowanie i implementowanie standardowych konfiguracji zabezpieczeń Azure Policy dla zasobów sieciowych skojarzonych z klastrami usługi Azure Kubernetes Service (AKS). 

Użyj aliasów Azure Policy w przestrzeniach nazw "Microsoft. ContainerService" i "Microsoft. Network", aby utworzyć niestandardowe zasady inspekcji lub wymuszania konfiguracji sieciowej klastrów AKS. 

Ponadto Użyj wbudowanych definicji zasad związanych z AKS, takich jak:

- Dozwolone zakresy adresów IP powinny być zdefiniowane w usługach Kubernetes Services

- Wymuszaj ruch przychodzący HTTPS w klastrze Kubernetes

- Upewnij się, że usługi nasłuchują tylko na dozwolonych portach w klastrze Kubernetes

Dodatkowe informacje są dostępne w linkach, do których istnieją odwołania.

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

- [Przykłady Azure Policy dla sieci](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#network)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="110-document-traffic-configuration-rules"></a>1,10: udokumentowanie reguł konfiguracji ruchu

**Wskazówki**: Użyj tagów dla sieciowych grup zabezpieczeń i innych zasobów na potrzeby przepływu ruchu do i z klastrów usługi Azure Kubernetes Service (AKS). Użyj pola "opis" dla poszczególnych reguł grupy zabezpieczeń sieci, aby określić potrzeby biznesowe i/lub czas trwania itd., dla wszystkich reguł zezwalających na ruch do/z sieci.

Użyj dowolnych wbudowanych Azure Policy znaczników, na przykład "Wymagaj tagu i jego wartości", aby upewnić się, że wszystkie zasoby są tworzone za pomocą tagów i aby otrzymywać powiadomienia o istniejących nieoznakowanych zasobach.

Wybierz, aby zezwalać na określone ścieżki sieciowe w ramach klastra lub odrzucać je na podstawie obszarów nazw i selektorów etykiet z zasadami sieci. Użyj tych przestrzeni nazw i etykiet jako deskryptorów dla reguł konfiguracji ruchu. Użyj Azure PowerShell lub interfejsu wiersza polecenia platformy Azure, aby wyszukiwać lub wykonywać akcje na zasobach na podstawie ich tagów.

- [Azure Policy przy użyciu interfejsu wiersza polecenia](/cli/azure/policy)

- [Tworzenie i używanie tagów](../azure-resource-manager/management/tag-resources.md)

- [Jak utworzyć sieciowej grupy zabezpieczeń z konfiguracją zabezpieczeń](../virtual-network/tutorial-filter-network-traffic.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: Użyj zautomatyzowanych narzędzi do monitorowania konfiguracji zasobów sieciowych i wykrywania zmian

**Wskazówki**: Użyj dziennika aktywności platformy Azure do monitorowania konfiguracji zasobów sieciowych i wykrywania zmian zasobów sieciowych związanych z klastrami usługi Azure Kubernetes Service (AKS). 

Tworzenie alertów w ramach Azure Monitor, które będą wyzwalane po wprowadzeniu zmian w krytycznych zasobach sieciowych. Wszystkie wpisy z AzureContainerService użytkownika w dziennikach aktywności są rejestrowane jako akcje platformy. 

Użyj dzienników Azure Monitor, aby włączyć i wysyłać zapytania do dzienników z AKS składników głównych, polecenia-apiserver i polecenia-Controller-Manager. Utwórz węzły, które uruchamiają kubelet z środowiskiem uruchomieniowym kontenera i zarządzaj nimi, i wdrażaj ich aplikacje za pomocą zarządzanego serwera interfejsu API Kubernetes. 

- [Jak wyświetlać i pobierać zdarzenia dziennika aktywności platformy Azure](/azure/azure-monitor/platform/activity-log#view-the-activity-log)

- [Jak utworzyć alerty w Azure Monitor](/azure/azure-monitor/platform/alerts-activity-log)

- [Włączanie i wyświetlanie dzienników węzła master platformy Kubernetes w usłudze Azure Kubernetes Service](/azure/aks/view-master-logs)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

## <a name="logging-and-monitoring"></a>Rejestrowanie i monitorowanie

*Aby uzyskać więcej informacji, zobacz temat [Azure Security test: rejestrowanie i monitorowanie](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2,1: Użyj źródeł synchronizacji zatwierdzonego czasu

**Wskazówki**: węzły usługi Azure Kubernetes Service (AKS) używają NTP.Ubuntu.com do synchronizacji czasu, a także portów UDP 123 i Network Time Protocol (NTP). 

Upewnij się, że serwery NTP są dostępne dla węzłów klastra, jeśli są używane niestandardowe serwery DNS. 

- [Omówienie wymagań dotyczących domen i portów NTP dla węzłów klastra AKS](limit-egress-traffic.md)

**Odpowiedzialność**: Współużytkowane

**Monitorowanie Azure Security Center**: brak

### <a name="22-configure-central-security-log-management"></a>2,2: Skonfiguruj centralne zarządzanie dziennikami zabezpieczeń

**Wskazówki**: Włączanie dzienników inspekcji z poziomu składników Master usługi Azure Kubernetes Services (AKS), polecenia-apiserver i polecenia-Controller-Manager, które są udostępniane jako usługa zarządzana. 

- polecenia-auditaksService: Nazwa wyświetlana w dzienniku inspekcji dla operacji płaszczyzny kontroli (z hcpService) 

- MasterClient: Nazwa wyświetlana w dzienniku inspekcji dla MasterClientCertificate, certyfikat uzyskany z AZ AKS Get-Credentials 

- nodeclient: Nazwa wyświetlana dla ClientCertificate, która jest używana przez węzły agenta

Włącz również inne dzienniki inspekcji, takie jak polecenia-Audit. 

Wyeksportuj te dzienniki do Log Analytics lub innej platformy magazynu. W Azure Monitor używać Log Analytics obszarów roboczych do wykonywania zapytań i przeprowadzania analiz oraz używania kont usługi Azure Storage do przechowywania długoterminowego i archiwizowania.

Włączaj i dołączaj te dane do usługi Azure wskaźnikowej lub SIEM innej firmy na podstawie wymagań firmy obowiązujących w organizacji.

- [Przejrzyj schemat dziennika, w tym tutaj role dzienników](/azure/aks/view-master-logs)

- [Omówienie Azure Monitor kontenerów](/azure/azure-monitor/insights/container-insights-overview)

- [Jak włączyć Azure Monitor dla kontenerów](/azure/azure-monitor/insights/container-insights-onboard)

- [Włączanie i wyświetlanie dzienników węzła master platformy Kubernetes w usłudze Azure Kubernetes Service](/azure/aks/view-master-logs)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: Włączanie rejestrowania inspekcji dla zasobów platformy Azure

**Wskazówki**: używanie dzienników aktywności do monitorowania akcji w zasobach usługi Azure Kubernetes Service (AKS) w celu wyświetlenia wszystkich działań i ich stanu. Określ, jakie operacje zostały wykonane względem zasobów w ramach subskrypcji z dziennikami aktywności: 

- kto uruchomił operację
- gdy wystąpiła operacja
- stan operacji
- wartości innych właściwości, które mogą pomóc w zbadaniu operacji

Pobierz informacje z dziennika aktywności za pośrednictwem Azure PowerShell, interfejsu wiersza polecenia platformy Azure, interfejsu API REST platformy Azure lub Azure Portal. 

Włącz dzienniki inspekcji na głównych składnikach AKS, takich jak: 

- polecenia-auditaksService: Nazwa wyświetlana w dzienniku inspekcji dla operacji płaszczyzny kontroli (z hcpService) 

- MasterClient: Nazwa wyświetlana w dzienniku inspekcji dla MasterClientCertificate, certyfikat uzyskany z AZ AKS Get-Credentials 

- nodeclient: Nazwa wyświetlana dla ClientCertificate, która jest używana przez węzły agenta

Włącz również inne dzienniki inspekcji, takie jak polecenia-Audit. 

- [Jak włączyć i przejrzeć dzienniki węzła głównego Kubernetes w AKS](/azure/aks/view-master-logs)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: Zbierz dzienniki zabezpieczeń z systemów operacyjnych

**Wskazówki**: Włącz automatyczną instalację agentów log Analytics na potrzeby zbierania danych z węzłów klastra AKS. Ponadto automatyczne Inicjowanie obsługi administracyjnej agenta monitorowania Log Analytics platformy Azure z poziomu Azure Security Center, tak jak domyślnie, jest wyłączone. Agenta można również zainstalować ręcznie. W przypadku automatycznej aprowizacji w programie Security Center wdraża agenta Log Analytics na wszystkich obsługiwanych maszynach wirtualnych platformy Azure i utworzonych nowych. Usługa Security Center zbiera dane z usługi Azure Virtual Machines (VM), zestawów skalowania maszyn wirtualnych i kontenerów IaaS, takich jak węzły klastra Kubernetes, do monitorowania luk w zabezpieczeniach i zagrożeń. Dane są zbierane przy użyciu agenta Log Analytics platformy Azure, który odczytuje różne konfiguracje związane z zabezpieczeniami i dzienniki zdarzeń z komputera i kopiuje dane do obszaru roboczego na potrzeby analizy. 

Zbieranie danych jest wymagane w celu zapewnienia wglądu w brakujące aktualizacje, nieprawidłowej konfiguracji ustawień zabezpieczeń systemu operacyjnego, stanu programu Endpoint Protection oraz wykrywania kondycji i zagrożeń.

- [Jak włączyć automatyczną obsługę administracyjną agenta Log Analytics](../security-center/security-center-enable-data-collection.md)

**Odpowiedzialność**: Współużytkowane

**Monitorowanie Azure Security Center**: brak

### <a name="25-configure-security-log-storage-retention"></a>2,5: Konfigurowanie przechowywania magazynu dzienników zabezpieczeń

**Wskazówki**: dołączanie wystąpień usługi Azure Kubernetes Service (AKS) do Azure monitor i Ustawianie odpowiedniego okresu przechowywania obszaru roboczego log Analytics platformy Azure zgodnie z wymaganiami firmy dotyczącymi zgodności. 

- [Jak ustawić parametry przechowywania dzienników dla obszarów roboczych Log Analytics](/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="26-monitor-and-review-logs"></a>2,6: dzienniki monitorowania i przeglądania

**Wskazówki**: dołączanie wystąpień usługi Azure Kubernetes Service (AKS) do Azure monitor i konfigurowania ustawień diagnostycznych dla klastra. 

Użyj Azure Monitor Log Analytics obszaru roboczego do przeglądania dzienników i wykonywania zapytań dotyczących danych dziennika. Dzienniki Azure Monitor są włączane i zarządzane w Azure Portal lub za pośrednictwem interfejsu wiersza polecenia oraz działają zarówno w przypadku Kubernetes kontroli dostępu opartej na rolach (Kubernetes RBAC), usług Azure RBAC i bez kontroli RBAC.

Wyświetlanie dzienników generowanych przez składniki AKS Master (polecenia-apiserver i polecenia-controllermanager) w celu rozwiązywania problemów dotyczących aplikacji i usług. Włączaj i dołączaj dane do usługi Azure wskaźnikowej lub SIEM innych firm w celu scentralizowanego zarządzania dziennikami i monitorowania.

- [Jak włączyć i przejrzeć dzienniki węzła głównego Kubernetes w AKS](/azure/aks/view-master-logs)

- [Jak dołączyć wskaźnik na platformie Azure](../sentinel/quickstart-onboard.md)

- [Jak wykonywać niestandardowe zapytania w Azure Monitor](/azure/azure-monitor/log-query/get-started-queries)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: Włączanie alertów dla nietypowych działań

**Wskazówki**: Użyj usługi Azure Kubernetes Service (AKS) wraz z Security Center, aby uzyskać lepszy wgląd w węzły AKS. 

Przejrzyj Security Center alerty dotyczące zagrożeń i złośliwych działań wykrytych na hoście i na poziomie klastra. Security Center implementuje ciągłej analizy nieprzetworzonych zdarzeń zabezpieczeń występujących w klastrze AKS, takich jak dane sieci, tworzenie procesów i dziennik inspekcji Kubernetes. Ustal, czy to działanie jest oczekiwane, czy też czy aplikacja jest błędna. Użyj metryk i dzienników w Azure Monitor, aby uzasadnić swoje ustalenia. 

- [Omówienie integracji usług Azure Kubernetes Services z usługą Security Center](../security-center/defender-for-kubernetes-introduction.md)

- [Jak włączyć warstwę Standardowa Azure Security Center](../security-center/security-center-get-started.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="28-centralize-anti-malware-logging"></a>2,8: scentralizowanie rejestrowania chroniącego przed złośliwym oprogramowaniem

**Wskazówki**: Zainstaluj i Włącz usługi firmy Microsoft chroniące przed złośliwym oprogramowaniem dla platformy Azure w usłudze Azure Kubernetes Service (AKS) i węzły zestawu skalowania maszyn wirtualnych. Przejrzyj alerty w Security Center na potrzeby korygowania.

- [Oprogramowanie chroniące przed złośliwym oprogramowaniem firmy Microsoft Cloud Services i Virtual Machines](../security/fundamentals/antimalware.md)

- [Przewodnik dotyczący alertów zabezpieczeń](../security-center/alerts-reference.md)

- [Alerty dla kontenerów — klastry usługi Azure Kubernetes](https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-akscluster)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="29-enable-dns-query-logging"></a>2,9: Włączanie rejestrowania zapytań DNS

**Wskazówki**: usługa Azure Kubernetes Service (AKS) używa projektu CoreDNS do zarządzania i rozpoznawania DNS klastra.

Włącz rejestrowanie zapytań DNS, stosując udokumentowaną konfigurację w coredns-Custom ConfigMap. 

- [Dostosowywanie serwera CoreDNS w usłudze Azure Kubernetes Service](coredns-custom.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="210-enable-command-line-audit-logging"></a>2,10: Włączanie rejestrowania inspekcji w wierszu polecenia

**Wskazówki**: Użyj polecenia kubectl, klienta wiersza polecenia w usłudze Azure Kubernetes Service (AKS) w celu zarządzania klastrem Kubernetes i pobrania dzienników z węzła AKS w celu rozwiązywania problemów. Polecenia kubectl jest już zainstalowana, jeśli używasz Azure Cloud Shell. Aby zainstalować polecenia kubectl lokalnie, użyj polecenia cmdlet "Install-AzAksKubectl".

- [Szybki Start — wdrażanie klastra usługi Azure Kubernetes Service przy użyciu programu PowerShell](kubernetes-walkthrough-powershell.md)

- [Pobieranie dzienników agenta kubelet z węzłów klastra usługi Azure Kubernetes Service](kubelet-logs.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

## <a name="identity-and-access-control"></a>Tożsamość i kontrola dostępu

*Aby uzyskać więcej informacji, zobacz informacje o [teście zabezpieczeń Azure: Identity i Access Control](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: obsługa spisu kont administracyjnych

**Wskazówki**: usługa Azure Kubernetes Service (AKS) sama nie oferuje rozwiązania do zarządzania tożsamościami, które przechowuje regularne konta użytkowników i hasła. Dzięki integracji z usługą Azure Active Directory (Azure AD) można udzielić użytkownikom lub grupom dostępu do zasobów Kubernetes w przestrzeni nazw lub w klastrze.

Wykonywanie zapytań ad hoc w celu odnajdywania kont należących do grup administracyjnych AKS za pomocą modułu Azure AD PowerShell

Użyj interfejsu wiersza polecenia platformy Azure w celu wykonywania operacji, takich jak "Uzyskaj poświadczenia dostępu dla zarządzanego klastra Kubernetes", aby pomóc w regularnym uzgadnianiu dostępu. Zaimplementuj ten proces, aby zachować zaktualizowany spis kont usług, który jest innym głównym typem użytkownika w AKS. Wymuś zalecenia dotyczące zarządzania tożsamościami i dostępem Security Center.

- [Jak zintegrować usługę AKS z usługą Azure AD](azure-ad-integration-cli.md)

- [Jak uzyskać członków roli katalogu w usłudze Azure AD przy użyciu programu PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember)

- [Jak monitorować tożsamość i dostęp przy użyciu Azure Security Center](../security-center/security-center-identity-access.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="32-change-default-passwords-where-applicable"></a>3,2: Zmień domyślne hasła, jeśli ma to zastosowanie

**Wskazówki**: usługa Azure Kubernetes Service (AKS) nie ma koncepcji wspólnych haseł domyślnych i nie oferuje rozwiązania do zarządzania tożsamościami, w którym można przechowywać regularne konta użytkowników i hasła. Dzięki integracji z usługą Azure Active Directory (Azure AD) można udzielić dostępu opartego na rolach do zasobów AKS w przestrzeni nazw lub w klastrze. 

Wykonywanie zapytań ad hoc w celu odnajdywania kont należących do grup administracyjnych AKS za pomocą modułu Azure AD PowerShell

- [Informacje na temat opcji dostępu i tożsamości dla AKS](concepts-identity.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: Użyj dedykowanych kont administracyjnych

**Wskazówki**: Integrowanie uwierzytelniania użytkowników dla klastrów usługi Azure Kubernetes Service (AKS) z Azure Active Directory (Azure AD). Zaloguj się do klastra AKS przy użyciu tokenu uwierzytelniania usługi Azure AD. Konfigurowanie kontroli dostępu opartej na rolach Kubernetes (Kubernetes RBAC) w celu uzyskania dostępu administracyjnego do informacji i uprawnień w konfiguracji Kubernetes (kubeconfig), przestrzeni nazw i zasobów klastra. 

Utwórz zasady i procedury dotyczące korzystania z dedykowanych kont administracyjnych. Zaimplementuj zalecenia dotyczące zarządzania tożsamościami i dostępem Security Center.

- [Jak monitorować tożsamość i dostęp przy użyciu Azure Security Center](../security-center/security-center-identity-access.md)

- [Kontrola dostępu do zasobów klastra](azure-ad-rbac.md)

- [Korzystanie z kontroli dostępu opartej na rolach na platformie Azure](control-kubeconfig-access.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3,4: Korzystaj z logowania jednokrotnego (SSO) z usługą Azure Active Directory

**Wskazówki**: Użyj logowania jednokrotnego w usłudze Azure Kubernetes Service (AKS) przy użyciu zintegrowanego uwierzytelniania Azure Active Directory (Azure AD) dla klastra AKS.

- [Jak wyświetlać dzienniki Kubernetes, zdarzenia i metryki pod względem czasu rzeczywistego](/azure/azure-monitor/insights/container-insights-livedata-overview)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Użyj uwierzytelniania wieloskładnikowego, aby uzyskać dostęp oparty na Azure Active Directory

**Wskazówki**: integracja uwierzytelniania dla usługi Azure Kubernetes Service (AKS) z usługą Azure Active Directory (Azure AD). 

Włącz uwierzytelnianie wieloskładnikowe usługi Azure AD i postępuj zgodnie z zaleceniami Security Center zarządzaniem tożsamościami i dostępem.

- [Jak włączyć uwierzytelnianie wieloskładnikowe na platformie Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Jak monitorować tożsamość i dostęp w Azure Security Center](../security-center/security-center-identity-access.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: Używaj dedykowanych maszyn (uprzywilejowany dostęp do stacji roboczych) dla wszystkich zadań administracyjnych

**Wskazówki**: Używanie stacji roboczej z dostępem uprzywilejowanym (dostępem uprzywilejowanym) z usługą Multi-Factor Authentication (MFA) skonfigurowanym do logowania się do określonych klastrów usługi Azure Kubernetes Service (AKS) i powiązanych zasobów.

- [Dowiedz się więcej o stacjach roboczych uprzywilejowanego dostępu](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Jak włączyć Multi-Factor Authentication (MFA) na platformie Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: Rejestruj i Ostrzegaj o podejrzanych działaniach z kont administracyjnych

**Wskazówki**: Użyj raportów zabezpieczeń usługi Azure Active Directory (Azure AD) z uwierzytelnianiem zintegrowanym z usługą Azure AD dla usługi Azure Kubernetes Service (AKS). Alerty mogą być generowane w przypadku działania podejrzanego lub niebezpiecznego w środowisku. Użyj Security Center, aby monitorować działania związane z tożsamościami i dostępem.

- [Identyfikowanie użytkowników usługi Azure AD oflagowanych na skutek ryzykownego działania](../active-directory/identity-protection/overview-identity-protection.md)

- [Jak monitorować działania związane z tożsamościami i dostępem użytkowników w Azure Security Center](../security-center/security-center-identity-access.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="38-manage-azure-resources-only-from-approved-locations"></a>3,8: Zarządzaj zasobami platformy Azure tylko z zatwierdzonych lokalizacji

**Wskazówki**: Użyj dostępu warunkowego o nazwie Locations, aby zezwolić na dostęp do klastrów usługi Azure Kubernetes Service (AKS) tylko do określonych logicznych grup zakresów adresów IP lub krajów/regionów. Wymaga to zintegrowanego uwierzytelniania dla AKS z usługą Azure Active Directory (Azure AD).

Ogranicz dostęp do serwera interfejsu API AKS z ograniczonego zestawu zakresów adresów IP, ponieważ odbiera ona żądania wykonania akcji w klastrze w celu utworzenia zasobów lub skalowania liczby węzłów. 

- [Bezpieczny dostęp do serwera interfejsu API za pomocą zakresów autoryzowanych adresów IP w usłudze Azure Kubernetes Service (AKS)](api-server-authorized-ip-ranges.md)

- [Jak skonfigurować nazwane lokalizacje na platformie Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="39-use-azure-active-directory"></a>3,9: Użyj Azure Active Directory

**Wskazówki**: Użyj Azure Active Directory (Azure AD) jako centralnego systemu uwierzytelniania i autoryzacji dla usługi Azure Kubernetes Service (AKS). Usługa Azure AD chroni dane przy użyciu silnego szyfrowania danych przechowywanych w czasie spoczynku i przesyłania oraz soli, skrótów i bezpiecznego przechowywania poświadczeń użytkownika.

Używanie wbudowanych ról AKS z kontrolą dostępu opartą na rolach (Azure RBAC) — współautor i właściciel zasad zasobów dla operacji przypisywania zasad do klastra Kubernetes

- [Przegląd zasad platformy Azure](../governance/policy/overview.md)

- [Jak zintegrować usługę Azure AD z usługą AKS](azure-ad-integration-cli.md)

- [Integrowanie usługi Azure AD zarządzanej przez usługę AKS](managed-aad.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: regularnie Przeglądaj i Uzgodnij dostęp użytkowników

**Wskazówki**: Użyj raportów zabezpieczeń usługi Azure Active Directory (Azure AD) z uwierzytelnianiem zintegrowanym z usługą Azure AD dla usługi Azure Kubernetes Service (AKS). Wyszukaj w dziennikach usługi Azure AD, aby ułatwić odnajdywanie starych kont. 

Wykonaj przeglądy dostępu do tożsamości platformy Azure, aby efektywnie zarządzać członkostwem w grupach, dostępem do aplikacji dla przedsiębiorstw i przypisaniami ról. Skoryguj zalecenia dotyczące tożsamości i dostępu Security Center.

Należy pamiętać o rolach używanych na potrzeby obsługi lub rozwiązywania problemów. Na przykład wszystkie akcje klastra podejmowane przez pomoc techniczną firmy Microsoft (z zgodą użytkownika) są wykonywane w ramach wbudowanej roli "Edytuj" Kubernetes o nazwie AKS-support-rolebinding. Obsługa AKS jest włączona w tej roli, aby edytować konfigurację i zasoby klastra w celu rozwiązywania problemów z klastrami i ich diagnozowania. Jednak ta rola nie może modyfikować uprawnień ani tworzyć ról ani powiązań ról. Dostęp do tej roli jest włączony tylko w ramach aktywnych biletów pomocy technicznej z dostępem just-in-Time (JIT).
 
- [Opcje dostępu i tożsamości dla usługi Azure Kubernetes Service (AKS)](concepts-identity.md)

- [Jak korzystać z przeglądów dostępu do tożsamości platformy Azure](../active-directory/governance/access-reviews-overview.md)

- [Jak monitorować aktywność użytkownika i dostęp w Azure Security Center](../security-center/security-center-identity-access.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: Monitor próbuje uzyskać dostęp do zdezaktywowanych poświadczeń

**Wskazówki**: Integruj uwierzytelnianie użytkowników w usłudze Azure Kubernetes Service (AKS) z usługą Azure Active Directory (Azure AD). Tworzenie ustawień diagnostycznych dla usługi Azure AD, wysyłanie dzienników inspekcji i logowania do obszaru roboczego usługi Azure Log Analytics. Skonfiguruj żądane alerty (takie jak w przypadku próby zalogowania się przy użyciu konta) w obszarze roboczym usługi Azure Log Analytics.
- [Jak zintegrować dzienniki aktywności platformy Azure z usługą Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Jak tworzyć, wyświetlać i zarządzać alertami dzienników przy użyciu Azure Monitor](/azure/azure-monitor/platform/alerts-log)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: odchylenia zachowania podczas logowania do konta

**Wskazówki**: Integruj uwierzytelnianie użytkowników w usłudze Azure Kubernetes Service (AKS) z usługą Azure Active Directory (Azure AD). Za pomocą funkcji wykrywania ryzyka i ochrony tożsamości usługi Azure AD można skonfigurować automatyczne odpowiedzi na wykryte podejrzane działania związane z tożsamościami użytkowników. Pozyskiwanie danych na platformie Azure — Wskaźnikowanie do dalszych badań w zależności od potrzeb firmy.

- [Jak wyświetlić ryzykowne logowania w usłudze Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

- [Jak skonfigurować i włączyć zasady dotyczące ryzyka związanego z ochroną tożsamości](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Jak dołączyć wskaźnik na platformie Azure](../sentinel/quickstart-onboard.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

## <a name="data-protection"></a>Ochrona danych

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: ochrona danych](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: przechowywanie spisu poufnych informacji

**Wskazówki**: Użyj tagów dotyczących zasobów związanych z wdrożeniami usługi Azure Kubernetes Service (AKS), aby pomóc w śledzeniu zasobów platformy Azure, które przechowują lub przetwarzają informacje poufne.

- [Tworzenie i używanie tagów](../azure-resource-manager/management/tag-resources.md)

- [Aktualizowanie tagów dla zarządzanych klastrów](/rest/api/aks/managedclusters/updatetags)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: Izoluj systemy przechowujące lub przetwarzające informacje poufne

**Wskazówki**: logicznie Izoluj zespoły i obciążenia w tym samym klastrze przy użyciu usługi Azure Kubernetes Service (AKS), aby zapewnić najmniejszą liczbę uprawnień w zakresie zasobów wymaganych przez poszczególne zespoły. 

Użyj przestrzeni nazw w Kubernetes do utworzenia logicznej granicy izolacji. Rozważ zaimplementowanie dodatkowych funkcji Kubernetes na potrzeby izolacji i wielu dzierżawców, takich jak planowanie, Sieć, uwierzytelnianie/autoryzacja i kontenery.

Zaimplementuj osobne subskrypcje i/lub grupy zarządzania dla środowisk programistycznych, testowych i produkcyjnych. Oddziel klastry AKS z sieciami, wdrażając je w różnych sieciach wirtualnych, które są odpowiednio oznakowane.

- [Dowiedz się więcej o najlepszych rozwiązaniach dotyczących izolacji klastra w AKS](operator-best-practices-cluster-isolation.md)

- [Jak utworzyć dodatkowe subskrypcje platformy Azure](../cost-management-billing/manage/create-subscription.md)

- [Poznaj najlepsze rozwiązania dotyczące łączności sieciowej i zabezpieczeń w AKS](operator-best-practices-network.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: Monitoruj i blokuj nieautoryzowany transfer informacji poufnych

**Wskazówki**: Użyj rozwiązania innej firmy z witryny Azure Marketplace w sieci obwodowej, które monitoruje do nieautoryzowanego transferu poufnych informacji i blokuje takie transfery podczas powiadamiania specjalistów ds. bezpieczeństwa informacji.

Firma Microsoft zarządza podstawową platformą i traktuje całą zawartość klienta jako poufną i nadaje im dużą długość, chroniąc przed utratą i narażeniem danych przez klienta. Aby zapewnić bezpieczeństwo danych klienta na platformie Azure, firma Microsoft wdrożyła i utrzymuje pakiet niezawodnych kontroli i możliwości ochrony danych.

- [Lista wymaganych portów, adresów i nazw domen dla funkcji AKS](limit-egress-traffic.md)

- [Jak skonfigurować ustawienia diagnostyczne dla zapory platformy Azure](../firewall/firewall-diagnostics.md)

- [Informacje na temat ochrony danych klientów na platformie Azure](../security/fundamentals/protection-customer-data.md)

**Odpowiedzialność**: Współużytkowane

**Monitorowanie Azure Security Center**: brak

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: Szyfruj wszystkie poufne informacje podczas przesyłania

**Wskazówki**: Tworzenie kontrolera protokołu HTTPS dla ruchu przychodzącego i korzystanie z własnych certyfikatów TLS (lub opcjonalnie szyfrowania) dla wdrożeń usługi Azure Kubernetes Service (AKS). 

Ruch wychodzący Kubernetes jest domyślnie szyfrowany przy użyciu protokołu HTTPS/TLS. Zapoznaj się z potencjalnie nieszyfrowanym ruchem wychodzącym z wystąpień AKS, aby uzyskać dodatkowe monitorowanie. Może to obejmować ruch NTP, ruch DNS, ruch HTTP w celu pobierania aktualizacji w niektórych przypadkach. 

- [Jak utworzyć kontroler protokołu HTTPS w systemie AKS i korzystać z własnych certyfikatów TLS](ingress-own-tls.md)

- [Jak utworzyć kontroler protokołu HTTPS w systemie AKS z szyfrowaniem](ingress-tls.md)

- [Lista potencjalnych portów i protokołów używanych przez AKS](limit-egress-traffic.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: Użyj aktywnego narzędzia do odnajdywania, aby identyfikować poufne dane

**Wskazówki**: funkcje identyfikacji, klasyfikacji i zapobiegania utracie danych nie są jeszcze dostępne dla usługi Azure Storage i zasobów obliczeniowych. Zaimplementuj rozwiązanie innych firm, jeśli jest wymagane na potrzeby zgodności.
Firma Microsoft zarządza podstawową platformą i traktuje całą zawartość klienta jako poufną i nadaje im dużą długość, chroniąc przed utratą i narażeniem danych przez klienta. 

Aby zapewnić bezpieczeństwo danych klienta na platformie Azure, firma Microsoft wdrożyła i utrzymuje pakiet niezawodnych kontroli i możliwości ochrony danych.

- [Informacje na temat ochrony danych klientów na platformie Azure](../security/fundamentals/protection-customer-data.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4,6: Korzystaj z usługi Azure RBAC, aby zarządzać dostępem do zasobów

**Wskazówki**: Użyj systemu autoryzacji kontroli dostępu opartego na rolach (Azure RBAC) wbudowanego w Azure Resource Manager, aby zapewnić precyzyjne zarządzanie dostępem do zasobów platformy Azure.

Skonfiguruj usługę Azure Kubernetes Service (AKS) do korzystania z Azure Active Directory (Azure AD) do uwierzytelniania użytkowników. Zaloguj się do klastra AKS przy użyciu tokenu uwierzytelniania usługi Azure AD przy użyciu tej konfiguracji. 

Używanie wbudowanych ról AKS z usługą Azure RBAC — współautor zasad zasobów i właściciela, w przypadku operacji przypisywania zasad do klastra AKS

- [Jak kontrolować dostęp do zasobów klastra przy użyciu usług Azure RBAC i tożsamości usługi Azure AD w AKS](azure-ad-rbac.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania**: [wzorzec zabezpieczeń platformy Azure](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) jest domyślną inicjatywy zasad dla Security Center i jest podstawą dla [zaleceń Security Center](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md). Definicje Azure Policy powiązane z tym formantem są włączane automatycznie przez Security Center. Alerty związane z tym formantem mogą wymagać planu [usługi Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) dla powiązanych usług.

**Azure Policy wbudowane definicje — Microsoft. ContainerService**:

[!INCLUDE [Resource Policy for Microsoft.ContainerService 4.6](../../includes/policy/standards/asb/rp-controls/microsoft.containerservice-4-6.md)]

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: Wymuś kontrolę dostępu przy użyciu ochrony przed utratą danych opartą na hoście

**Wskazówki**: funkcje identyfikacji, klasyfikacji i zapobiegania utracie danych nie są jeszcze dostępne dla usługi Azure Storage i zasobów obliczeniowych. Zaimplementuj rozwiązanie innych firm, jeśli jest wymagane na potrzeby zgodności.
Firma Microsoft zarządza podstawową platformą i traktuje całą zawartość klienta jako poufną i nadaje im dużą długość, chroniąc przed utratą i narażeniem danych przez klienta. Aby zapewnić bezpieczeństwo danych klienta na platformie Azure, firma Microsoft wdrożyła i utrzymuje pakiet niezawodnych kontroli i możliwości ochrony danych.

- [Informacje na temat ochrony danych klientów na platformie Azure](../security/fundamentals/protection-customer-data.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: Szyfruj poufne informacje w spoczynku

**Wskazówki**: dwa podstawowe typy magazynów udostępnione dla woluminów w usłudze Azure Kubernetes Service (AKS) są obsługiwane przez dyski lub Azure Files platformy Azure. Oba typy magazynów używają usługi Azure szyfrowanie usługi Storage (SSE), która szyfruje dane przechowywane w celu poprawy bezpieczeństwa. Domyślnie dane są szyfrowane przy użyciu kluczy zarządzanych przez firmę Microsoft.

Szyfrowanie — w spoczynku przy użyciu kluczy zarządzanych przez klienta jest dostępny do szyfrowania dysków systemu operacyjnego i danych w klastrach AKS, aby uzyskać dodatkową kontrolę nad kluczami szyfrowania. Klienci są odpowiedzialni za działania związane z zarządzaniem kluczami, takie jak tworzenie kopii zapasowych i rotacja kluczy. Dysków nie można obecnie zaszyfrować przy użyciu Azure Disk Encryption na poziomie węzła AKS.

- [Najlepsze rozwiązania dotyczące magazynu i kopii zapasowych w usłudze Azure Kubernetes Service (AKS)](operator-best-practices-storage.md)

- [Przenoszenie własnych kluczy (BYOK) z dyskami platformy Azure w usłudze Azure Kubernetes Service (AKS)](azure-disk-customer-managed-keys.md)

**Odpowiedzialność**: Współużytkowane

**Monitorowanie Azure Security Center**: brak

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: Rejestruj i Ostrzegaj o zmianach krytycznych zasobów platformy Azure

**Wskazówki**: Azure monitor kontenerów służy do monitorowania wydajności obciążeń kontenera wdrożonych w zarządzanych klastrach Kubernetes hostowanych w usłudze Azure Kubernetes Service (AKS). 

Konfigurowanie alertów dotyczących aktywnego powiadamiania lub tworzenia dziennika, gdy użycie procesora i pamięci w węzłach lub kontenerach przekracza zdefiniowane progi lub w przypadku zmiany stanu kondycji w klastrze na zestawienie kondycji infrastruktury lub węzłów. 

Użyj dziennika aktywności platformy Azure do monitorowania klastrów AKS i powiązanych zasobów na wysokim poziomie. Integracja z usługą Prometheus w celu wyświetlania metryk aplikacji i obciążeń zbieranych z węzłów i Kubernetes przy użyciu zapytań w celu utworzenia niestandardowych alertów, pulpitów nawigacyjnych i szczegółowej szczegółowej analizy.

- [Omówienie Azure Monitor kontenerów](/azure/azure-monitor/insights/container-insights-overview)

- [Jak włączyć Azure Monitor dla kontenerów](/azure/azure-monitor/insights/container-insights-onboard)

- [Jak wyświetlać i pobierać zdarzenia dziennika aktywności platformy Azure](/azure/azure-monitor/platform/activity-log#view-the-activity-log)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

## <a name="vulnerability-management"></a>Zarządzanie lukami w zabezpieczeniach

*Aby uzyskać więcej informacji, zobacz temat [Azure Security test: Zarządzanie lukami w zabezpieczeniach](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: uruchamianie narzędzi do skanowania automatycznych luk w zabezpieczeniach

**Wskazówki**: Użyj Security Center, aby monitorować Azure Container Registry, w tym wystąpienia usługi Azure Kubernetes Service (AKS) dla luk w zabezpieczeniach. Włącz pakiet rejestrów kontenerów w Security Center, aby upewnić się, że Security Center jest gotowy do skanowania obrazów, które są przekazywane do rejestru.

Otrzymuj powiadomienia na pulpicie nawigacyjnym Security Center po znalezieniu problemów po Security Center zeskanowaniu obrazu przy użyciu Qualys. Funkcja zbioru rejestrów kontenerów zapewnia dokładniejszy wgląd w luki w zabezpieczeniach obrazów używanych w rejestrach opartych na Azure Resource Manager. 

Dla każdej luki w zabezpieczeniach należy używać Security Center. Zalecenia te obejmują klasyfikację ważności i wskazówki dotyczące korygowania. 

- [Najlepsze rozwiązania dotyczące zarządzania obrazami kontenerów i zabezpieczeń w usłudze Azure Kubernetes Service (AKS)](../security-center/defender-for-container-registries-introduction.md)

- [Poznaj najlepsze rozwiązania dotyczące zarządzania obrazami kontenerów i zabezpieczeń w programie AKS](operator-best-practices-container-image-management.md)

- [Omówienie integracji rejestru kontenerów z Azure Security Center](../security-center/defender-for-container-registries-introduction.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: Wdróż automatyczne rozwiązanie do zarządzania poprawkami systemu operacyjnego

**Wskazówki**: aktualizacje zabezpieczeń są automatycznie stosowane do węzłów systemu Linux w celu ochrony klastrów usługi Azure Kubernetes Service (AKS) klienta. Te aktualizacje obejmują poprawki zabezpieczeń systemu operacyjnego lub aktualizacje jądra. 

Należy pamiętać, że proces zachowania aktualności węzłów systemu Windows Server różni się od węzłów z systemem Linux, ponieważ węzły systemu Windows Server nie otrzymują codziennych aktualizacji. Zamiast tego klienci muszą przeprowadzić uaktualnienie w pulach węzłów systemu Windows Server w swoich klastrach AKS, które wdrażają nowe węzły z najnowszym obrazem i poprawkami podstawowego serwera okna, przy użyciu panelu sterowania platformy Azure lub interfejsu wiersza polecenia platformy Azure. Te aktualizacje zawierają ulepszenia zabezpieczeń lub funkcji AKS.

- [Dowiedz się, jak aktualizacje są stosowane do węzłów klastra AKS z systemem Linux](node-updates-kured.md)

- [Jak uaktualnić pulę węzłów AKS dla klastrów AKS korzystających z węzłów systemu Windows Server](https://docs.microsoft.com/azure/aks/use-multiple-node-pools#upgrade-a-node-pool)

- [Uaktualnienia obrazu węzła usługi Azure Kubernetes Service (AKS)](node-image-upgrade.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="53-deploy-an-automated-patch-management-solution-for-third-party-software-titles"></a>5,3: Wdróż automatyczne rozwiązanie do zarządzania poprawkami dla tytułów oprogramowania innych firm

**Wskazówki**: Zaimplementuj proces ręczny, aby upewnić się, że aplikacje innych firm węzła klastra usługi Azure Kubernetes Service (AKS) pozostają poprawione na czas istnienia klastra. Może to wymagać włączenia aktualizacji automatycznych, monitorowania węzłów lub wykonywania okresowych ponownych uruchomień.

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania**: [wzorzec zabezpieczeń platformy Azure](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) jest domyślną inicjatywy zasad dla Security Center i jest podstawą dla [zaleceń Security Center](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md). Definicje Azure Policy powiązane z tym formantem są włączane automatycznie przez Security Center. Alerty związane z tym formantem mogą wymagać planu [usługi Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) dla powiązanych usług.

**Azure Policy wbudowane definicje — Microsoft. ContainerService**:

[!INCLUDE [Resource Policy for Microsoft.ContainerService 5.3](../../includes/policy/standards/asb/rp-controls/microsoft.containerservice-5-3.md)]

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: porównanie luk w zabezpieczeniach z tyłu do tyłu

**Wskazówki**: Eksportuj wyniki skanowania Security Center w regularnych odstępach czasu i Porównaj wyniki, aby sprawdzić, czy luki zostały skorygowane.

Użyj polecenia cmdlet "Get-AzSecurityTask" programu PowerShell, aby zautomatyzować pobieranie zadań zabezpieczeń, które Security Center zalecane do wykonania, aby wzmocnić wyniki skanowania stan i rozwiązywania problemów z zabezpieczeniami.

- [Jak używać programu PowerShell do wyświetlania luk w zabezpieczeniach wykrytych przez Azure Security Center](/powershell/module/az.security/get-azsecuritytask)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: Użyj procesu oceny ryzyka, aby określić priorytety korygowania odkrytych luk w zabezpieczeniach

**Wskazówki**: Użyj oceny ważności dostarczonej przez Security Center, aby określić priorytety korygowania luk w zabezpieczeniach. 

Użyj typowego systemu oceny luk w zabezpieczeniach (CVSS) (lub innych systemów oceniania zgodnie z opisem w narzędziu do skanowania), jeśli korzystasz z wbudowanego narzędzia do oceniania luk w zabezpieczeniach (takiego jak Qualys lub Rapid7) oferowanych przez platformę Azure.

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

## <a name="inventory-and-asset-management"></a>Zarządzanie magazynem i zasobami

*Aby uzyskać więcej informacji, zobacz temat [Azure Security test: Inventory and Asset Management](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: Użyj rozwiązania automatycznego odnajdywania zasobów

**Wskazówki**: Użyj grafu zasobów platformy Azure do wykonywania zapytań/odnajdywania wszystkich zasobów (np. obliczeń, magazynu, sieci itp.) w ramach subskrypcji. Upewnij się, że masz odpowiednie uprawnienia (odczyt) w dzierżawie i że można wyliczyć wszystkie subskrypcje platformy Azure oraz zasoby w ramach subskrypcji.

Mimo że klasyczne zasoby platformy Azure mogą zostać odnalezione za pośrednictwem grafu zasobów, zdecydowanie zaleca się tworzenie i używanie zasobów opartych na Azure Resource Managerch.

- [Jak tworzyć zapytania za pomocą usługi Azure Graph](../governance/resource-graph/first-query-portal.md)

- [Jak wyświetlić subskrypcje platformy Azure](/powershell/module/az.accounts/get-azsubscription)

- [Opis kontroli RBAC platformy Azure](../role-based-access-control/overview.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="62-maintain-asset-metadata"></a>6,2: Konserwowanie metadanych zasobów

**Wskazówki**: Zastosuj Tagi do zasobów platformy Azure za pomocą metadanych, aby logicznie zorganizować je w taksonomię.

- [Tworzenie i używanie tagów](../azure-resource-manager/management/tag-resources.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: Usuń nieautoryzowane zasoby platformy Azure

**Wskazówki**: używanie tagowania, grup zarządzania i oddzielnych subskrypcji, gdzie to konieczne, do organizowania i śledzenia zasobów. 

Podczas tworzenia puli węzłów usługi Azure Kubernetes Service (AKS) można stosować do nich, etykiety lub Tagi. Wszystkie węzły w tej puli węzłów również dziedziczą te przyciągi, etykiety lub Tagi.

W celu regularnego uzgadniania spisu, można użyć etykiet lub tagów oraz upewnić się, że nieautoryzowane zasoby są usuwane z subskrypcji w odpowiednim czasie.

- [Jak utworzyć dodatkowe subskrypcje platformy Azure](../cost-management-billing/manage/create-subscription.md)

- [Jak utworzyć Grupy zarządzania](../governance/management-groups/create-management-group-portal.md)

- [Jak tworzyć tagi użytkownika i](../azure-resource-manager/management/tag-resources.md)

- [Klastry zarządzane — aktualizacje tagów](/rest/api/aks/managedclusters/updatetags)

- [Określ przebarwienie, etykietę lub tag dla puli węzłów](https://docs.microsoft.com/azure/aks/use-multiple-node-pools#specify-a-taint-label-or-tag-for-a-node-pool)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6,4: Definiowanie i obsługa spisu zatwierdzonych zasobów platformy Azure

**Wskazówki**: zdefiniuj listę zatwierdzonych zasobów platformy Azure i zatwierdzonego oprogramowania dla zasobów obliczeniowych na podstawie potrzeb firmy.

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: Monitoruj niezatwierdzone zasoby platformy Azure

**Wskazówki**: Użyj Azure Policy, aby umieścić ograniczenia dotyczące typu zasobów, które mogą być tworzone w subskrypcjach klientów, przy użyciu następujących wbudowanych definicji zasad:
-   Niedozwolone typy zasobów 

-   Dozwolone typy zasobów

Użyj grafu zasobów platformy Azure, aby wykonywać zapytania/odnajdywać zasoby w ramach subskrypcji. Upewnij się, że wszystkie zasoby platformy Azure obecne w środowisku są zatwierdzone na podstawie wymagań firmy obowiązujących w organizacji.

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

- [Jak tworzyć zapytania za pomocą usługi Azure Graph](../governance/resource-graph/first-query-portal.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: Monitoruj niezatwierdzone aplikacje oprogramowania w ramach zasobów obliczeniowych

**Wskazówki**: Użyj Azure Automation funkcji Change Tracking i spisu, aby znaleźć oprogramowanie zainstalowane w danym środowisku. 

Zbieraj i wyświetlaj Spis oprogramowania, plików, demonów systemu Linux, usług Windows i kluczy rejestru systemu Windows na komputerach oraz monitoruj niezatwierdzone aplikacje oprogramowania. 

Śledź konfiguracje maszyn, aby pomóc w wykrywaniu problemów operacyjnych w środowisku i lepszym zrozumieniu stanu maszyn.

- [Jak włączyć Spis maszyn wirtualnych platformy Azure](../automation/automation-tutorial-installed-software.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: Usuń niezatwierdzone zasoby platformy Azure i aplikacje oprogramowania

**Wskazówki**: Użyj Azure Automation funkcji Change Tracking i spisu, aby znaleźć oprogramowanie zainstalowane w danym środowisku. 

Zbieraj i wyświetlaj Spis oprogramowania, plików, demonów systemu Linux, usług Windows i kluczy rejestru systemu Windows na komputerach oraz monitoruj niezatwierdzone aplikacje oprogramowania. 

Śledź konfiguracje maszyn, aby pomóc w wykrywaniu problemów operacyjnych w środowisku i lepszym zrozumieniu stanu maszyn.

- [Jak włączyć Spis maszyn wirtualnych platformy Azure](../automation/automation-tutorial-installed-software.md)

- [Jak używać monitorowania integralności plików](../security-center/security-center-file-integrity-monitoring.md)

- [Omówienie usługi Azure Change Tracking](../automation/change-tracking/overview.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="68-use-only-approved-applications"></a>6,8: Używaj tylko zatwierdzonych aplikacji

**Wskazówki**: Użyj Azure Automation funkcji Change Tracking i spisu, aby znaleźć oprogramowanie zainstalowane w danym środowisku. 

Zbieraj i wyświetlaj Spis oprogramowania, plików, demonów systemu Linux, usług Windows i kluczy rejestru systemu Windows na komputerach oraz monitoruj niezatwierdzone aplikacje oprogramowania. 

Śledź konfiguracje maszyn, aby pomóc w wykrywaniu problemów operacyjnych w środowisku i lepszym zrozumieniu stanu maszyn.

Włącz adaptacyjną analizę aplikacji w Security Center dla aplikacji, które istnieją w danym środowisku.

- [Jak włączyć Spis maszyn wirtualnych platformy Azure](../automation/automation-tutorial-installed-software.md)

- [Jak używać Azure Security Center adaptacyjnych kontroli aplikacji](../security-center/security-center-adaptive-application.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="69-use-only-approved-azure-services"></a>6,9: Używaj tylko zatwierdzonych usług platformy Azure

**Wskazówki**: Użyj Azure Policy, aby umieścić ograniczenia dotyczące typu zasobów, które mogą być tworzone w subskrypcjach klientów, przy użyciu następujących wbudowanych definicji zasad:

- Niedozwolone typy zasobów

- Dozwolone typy zasobów

Użyj grafu zasobów platformy Azure, aby wykonywać zapytania/odnajdywać zasoby w ramach subskrypcji. Upewnij się, że wszystkie zasoby platformy Azure obecne w środowisku są zatwierdzone.

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

- [Jak odmówić określonego typu zasobu za pomocą Azure Policy](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6,10: przechowywanie spisu zatwierdzonych tytułów oprogramowania

**Wskazówki**: Użyj Azure Policy, aby umieścić ograniczenia dotyczące typu zasobów, które mogą być tworzone w ramach subskrypcji przy użyciu wbudowanych definicji zasad.

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: Ogranicz możliwość korzystania przez użytkowników z Azure Resource Manager

**Wskazówki**: Użyj dostępu warunkowego platformy Azure, aby ograniczyć możliwość korzystania przez użytkowników z Azure Resource Manager przez skonfigurowanie "blokowania dostępu" dla aplikacji "Microsoft Azure Management".
- [Jak skonfigurować dostęp warunkowy w celu blokowania dostępu do Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="612-limit-users-ability-to-execute-scripts-in-compute-resources"></a>6,12: Ogranicz możliwość wykonywania skryptów w zasobach obliczeniowych przez użytkowników

**Wskazówki**: usługa Azure Kubernetes Service (AKS) sama nie udostępnia rozwiązania do zarządzania tożsamościami, w którym są przechowywane regularne konta użytkowników i hasła. Zamiast tego należy używać Azure Active Directory (Azure AD) jako zintegrowanego rozwiązania do tworzenia tożsamości dla klastrów AKS.

Udziel użytkownikom lub grupom dostępu do zasobów Kubernetes w przestrzeni nazw lub w klastrze za pomocą integracji z usługą Azure AD.

Moduł Azure AD PowerShell służy do wykonywania zapytań ad hoc w celu odnajdywania kont, które są członkami grup administracyjnych AKS i używania go do regularnego uzgadniania dostępu. Użyj interfejsu wiersza polecenia platformy Azure w celu wykonywania operacji, takich jak "Uzyskiwanie poświadczeń dostępu dla zarządzanego klastra Kubernetes. Zaimplementuj zalecenia dotyczące zarządzania tożsamościami i dostępem Security Center.

- [Zarządzanie AKS za pomocą interfejsu wiersza polecenia platformy Azure](/cli/azure/aks)

- [Informacje na temat integracji usług AKS i Azure AD](concepts-identity.md)

- [Jak zintegrować usługę AKS z usługą Azure AD](azure-ad-integration-cli.md)

- [Jak uzyskać rolę katalogu w usłudze Azure AD przy użyciu programu PowerShell](/powershell/module/azuread/get-azureaddirectoryrole)

- [Jak uzyskać członków roli katalogu w usłudze Azure AD przy użyciu programu PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember)

- [Jak monitorować tożsamość i dostęp przy użyciu Azure Security Center](../security-center/security-center-identity-access.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: fizyczne lub logiczne rozdzielenie aplikacji wysokiego ryzyka

**Wskazówki**: korzystanie z funkcji usługi Azure Kubernetes Service (AKS) w celu logicznego izolowania zespołów i obciążeń w tym samym klastrze w celu uzyskania najmniejszej liczby uprawnień w zakresie zasobów wymaganych przez poszczególne zespoły. 

Zaimplementuj przestrzeń nazw w Kubernetes, aby utworzyć logiczną granicę izolacji. Użyj aliasów Azure Policy w przestrzeni nazw "Microsoft. ContainerService", aby utworzyć zasady niestandardowe do inspekcji lub wymuszania konfiguracji wystąpień usługi Azure Kubernetes Service (AKS). 

Przejrzyj i zaimplementuj dodatkowe funkcje Kubernetes oraz zagadnienia dotyczące izolacji i wielu dzierżawców, aby uwzględnić następujące kwestie: planowanie, Sieć, uwierzytelnianie/autoryzacja i kontenery. Używaj również oddzielnych subskrypcji i grup zarządzania na potrzeby tworzenia, testowania i produkcji. Oddziel klastry AKS z sieciami wirtualnymi, podsieciami, które są odpowiednio oznakowane i zabezpieczone za pomocą zapory aplikacji sieci Web (WAF).

- [Dowiedz się więcej o najlepszych rozwiązaniach dotyczących izolacji klastra w AKS](operator-best-practices-cluster-isolation.md)

- [Jak utworzyć dodatkowe subskrypcje platformy Azure](../cost-management-billing/manage/create-subscription.md)

- [Jak utworzyć Grupy zarządzania](../governance/management-groups/create-management-group-portal.md)

- [Poznaj najlepsze rozwiązania dotyczące łączności sieciowej i zabezpieczeń w AKS](operator-best-practices-network.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

## <a name="secure-configuration"></a>Bezpieczna konfiguracja

*Aby uzyskać więcej informacji, zobacz temat [Azure Security test: bezpieczna konfiguracja](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: Ustanów bezpieczne konfiguracje dla wszystkich zasobów platformy Azure

**Wskazówki**: Użyj aliasów Azure Policy w przestrzeni nazw "Microsoft. ContainerService", aby utworzyć zasady niestandardowe do inspekcji lub wymuszania konfiguracji wystąpień usługi Azure Kubernetes Service (AKS). Użyj wbudowanych definicji Azure Policy.

Przykłady wbudowanych definicji zasad dla AKS obejmują:

- Wymuszaj ruch przychodzący HTTPS w klastrze Kubernetes

- Dozwolone zakresy adresów IP powinny być zdefiniowane w usługach Kubernetes Services

- Access Control oparte na rolach (RBAC) powinny być używane w usługach Kubernetes Services

- Zapewnij stosowanie tylko dozwolonych obrazów kontenerów w klastrze Kubernetes

Wyeksportuj szablon konfiguracji AKS w JavaScript Object Notation (JSON) z Azure Resource Manager. Okresowe przeglądy należy przeglądać w celu zapewnienia, że te konfiguracje spełniają wymagania dotyczące zabezpieczeń Twojej organizacji. Użyj zaleceń z Azure Security Center jako bezpiecznej konfiguracji odniesienia dla zasobów platformy Azure. 

- [Jak skonfigurować zasady zabezpieczeń AKS pod kątem i zarządzać nimi](use-pod-security-policies.md)

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: Ustanów bezpieczne konfiguracje systemów operacyjnych

**Wskazówki**: klastry klastrów usługi Azure KUBERNETES (AKS) są wdrażane na maszynach wirtualnych obsługujących system operacyjny w wersji zoptymalizowanej pod kątem zabezpieczeń. System operacyjny hosta zawiera dodatkowe kroki zwiększania bezpieczeństwa w celu zmniejszenia obszaru ataków i pozwala na bezpieczne wdrażanie kontenerów. 

Na platformie Azure są stosowane codzienne poprawki (w tym poprawki zabezpieczeń) do AKS hostów maszyn wirtualnych z niektórymi poprawkami wymagającymi ponownego uruchomienia. Klienci są odpowiedzialni za planowanie ponownych uruchomień AKS hosta maszyn wirtualnych zgodnie z potrzebami. 

- [Ograniczanie zabezpieczeń dla systemu operacyjnego hosta węzła agenta AKS](security-hardened-vm-host-image.md)

- [Informacje o zabezpieczaniu zabezpieczeń w AKS hostach maszyn wirtualnych](security-hardened-vm-host-image.md)

**Odpowiedzialność**: Współużytkowane

**Monitorowanie Azure Security Center**: brak

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: obsługa bezpiecznych konfiguracji zasobów platformy Azure

**Wskazówki**: Zabezpieczanie klastra usługi Azure Kubernetes Service (AKS) przy użyciu zasad zabezpieczeń pod.  Ogranicz, jakie możliwości można zaplanować, aby zwiększyć bezpieczeństwo klastra. 

Nie można uruchomić w klastrze AKS zasobów, które nie są dozwolone. 

Należy również użyć Azure Policy [Odmów] i [Wdróż, jeśli nie istnieje], aby wymusić bezpieczne ustawienia zasobów platformy Azure związane z wdrożeniami AKS (takich jak sieci wirtualne, podsieci, zapory platformy Azure, konta magazynu itp.). 

Utwórz niestandardowe definicje Azure Policy przy użyciu aliasów z następujących przestrzeni nazw: 

- Microsoft. ContainerService

- Microsoft.Network

Dodatkowe informacje są dostępne w linkach, do których istnieją odwołania.

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

- [Zrozumienie efektów Azure Policy](../governance/policy/concepts/effects.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: Zachowaj konfiguracje bezpiecznego systemu operacyjnego

**Wskazówki**: klastry usługi Azure Kubernetes Service (AKS) są wdrażane na maszynach wirtualnych obsługujących zabezpieczenia zoptymalizowane pod kątem zabezpieczeń. System operacyjny hosta zawiera dodatkowe kroki zwiększania bezpieczeństwa w celu zmniejszenia obszaru ataków i pozwala na bezpieczne wdrażanie kontenerów. 

Zapoznaj się z listą kontrolek Centrum zabezpieczeń internetowych (CIS), które są wbudowane w system operacyjny hosta.  

- [Ograniczanie zabezpieczeń dla systemu operacyjnego hosta węzła agenta AKS](security-hardened-vm-host-image.md)

- [Opis konfiguracji stanu klastrów AKS](https://docs.microsoft.com/azure/aks/concepts-clusters-workloads#control-plane)

- [Informacje o zabezpieczaniu zabezpieczeń w AKS hostach maszyn wirtualnych](security-hardened-vm-host-image.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: bezpiecznie przechowuj konfigurację zasobów platformy Azure

**Wskazówki**: Użyj Azure Repos, aby bezpiecznie przechowywać konfiguracje i zarządzać nimi, jeśli są używane definicje Azure Policy niestandardowych. Wyeksportuj szablon konfiguracji usługi Azure Kubernetes Service (AKS) w JavaScript Object Notation (JSON) z Azure Resource Manager. Okresowe przeglądy, aby upewnić się, że konfiguracje spełniają wymagania dotyczące zabezpieczeń Twojej organizacji.

Wdrażaj rozwiązania innych firm, takie jak Terraform, aby utworzyć plik konfiguracji, który deklaruje zasoby dla klastra Kubernetes. Wdrożenie AKS można zabezpieczyć, wdrażając najlepsze rozwiązania w zakresie zabezpieczeń i przechowując konfigurację jako kod w zabezpieczonej lokalizacji.

- [Definiowanie klastra Kubernetes](/azure/developer/terraform/create-k8s-cluster-with-tf-and-aks#define-a-kubernetes-cluster)

- [Ograniczanie zabezpieczeń dla systemu operacyjnego hosta węzła agenta AKS](security-hardened-vm-host-image.md)

- [Jak przechowywać kod w usłudze Azure DevOps](/azure/devops/repos/git/gitworkflow)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: bezpieczne przechowywanie niestandardowych obrazów systemu operacyjnego

**Wskazówki**: nie dotyczy usługi Azure Kubernetes Service (AKS). AKS zapewnia domyślnie zoptymalizowany pod kątem zabezpieczeń system operacyjny hosta (OS). Nie ma bieżącej opcji, aby wybrać alternatywny lub niestandardowy system operacyjny.

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: Wdrażanie narzędzi do zarządzania konfiguracją dla zasobów platformy Azure

**Wskazówki**: Użyj Azure Policy, aby umieścić ograniczenia dotyczące typu zasobów, które mogą być tworzone w subskrypcjach przy użyciu wbudowanych definicji zasad, a także aliasów Azure Policy w przestrzeni nazw "Microsoft. ContainerService". 

Tworzenie zasad niestandardowych na potrzeby inspekcji i wymuszania konfiguracji systemu. Opracowywanie procesu i potoku w celu zarządzania wyjątkami zasad.

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

- [Jak używać aliasów](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure#aliases)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7,8: Wdrażanie narzędzi do zarządzania konfiguracją dla systemów operacyjnych

**Wskazówki**: klastry usługi Azure Kubernetes Service (AKS) są wdrażane na maszynach wirtualnych obsługujących zabezpieczenia zoptymalizowane pod kątem zabezpieczeń. System operacyjny hosta zawiera dodatkowe kroki zwiększania bezpieczeństwa w celu zmniejszenia obszaru ataków i pozwala na bezpieczne wdrażanie kontenerów. 

Zapoznaj się z listą kontrolek Centrum zabezpieczeń internetowych (CIS), które są wbudowane w hosty AKS.  

- [Ograniczanie zabezpieczeń dla systemu operacyjnego hosta węzła agenta AKS](security-hardened-vm-host-image.md)

- [Informacje o zabezpieczaniu zabezpieczeń w AKS hostach maszyn wirtualnych](security-hardened-vm-host-image.md)

- [Opis konfiguracji stanu klastrów AKS](https://docs.microsoft.com/azure/aks/concepts-clusters-workloads#control-plane)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: Zaimplementuj automatyczne monitorowanie konfiguracji dla zasobów platformy Azure

**Wskazówki**: Użyj Security Center, aby przeprowadzić skanowanie linii bazowej dla zasobów związanych z wdrożeniami usługi Azure Kubernetes Service (AKS). Przykłady zasobów obejmują, ale nie są ograniczone do samego klastra AKS, sieci wirtualnej, w której wdrożono klaster AKS, konta usługi Azure Storage używanego do śledzenia stanu Terraform lub Azure Key Vault wystąpieniami używanymi dla kluczy szyfrowania dla systemu operacyjnego i dysków danych klastra AKS.

- [Jak skorygować zalecenia w Azure Security Center](../security-center/security-center-remediate-recommendations.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: Zaimplementuj automatyczne monitorowanie konfiguracji dla systemów operacyjnych

**Wskazówki**: Użyj zaleceń dotyczących kontenera Security Center w sekcji "aplikacje obliczeniowe &amp; ", aby przeprowadzić skanowanie linii bazowej dla klastrów usługi Azure KUBERNETES Service (AKS). 

Otrzymuj powiadomienia na pulpicie nawigacyjnym Security Center, gdy zostaną znalezione problemy z konfiguracją lub luki w zabezpieczeniach. Wymaga to włączenia opcjonalnego zbioru rejestrów kontenerów, który umożliwia Security Center skanowania obrazu.  

- [Omówienie rekomendacji dotyczących kontenera usługi Azure Security Center](../security-center/container-security.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="711-manage-azure-secrets-securely"></a>7,11: bezpieczne zarządzanie wpisami tajnymi platformy Azure

**Wskazówki**: integracja Azure Key Vault z klastrem usługi Azure Kubernetes Service (AKS) przy użyciu dysku FlexVolume. Użyj Azure Key Vault do przechowywania i regularnego rotacji wpisów tajnych, takich jak poświadczenia, klucze konta magazynu lub certyfikaty. Sterownik FlexVolume umożliwia klastrowi AKS natywne pobieranie poświadczeń z Key Vault i bezpieczne udostępnianie ich tylko żądającemu. Użyj tożsamości zarządzanej pod, aby zażądać dostępu do Key Vault i pobrać wymagane poświadczenia za pomocą sterownika FlexVolume. Upewnij się, że Key Vault usuwanie trwałe jest włączone. 

Ogranicz ekspozycję poświadczeń, nie definiując poświadczeń w kodzie aplikacji. 

Unikaj stosowania poświadczeń stałych lub udostępnionych. 

- [Pojęcia dotyczące zabezpieczeń aplikacji i klastrów w usłudze Azure Kubernetes Service (AKS)](concepts-security.md)

- [Jak używać Key Vault z klastrem AKS](https://docs.microsoft.com/azure/aks/developer-best-practices-pod-security#limit-credential-exposure)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: bezpieczne i automatyczne zarządzanie tożsamościami

**Wskazówki**: nie należy definiować poświadczeń w kodzie aplikacji jako najlepsze rozwiązanie w zakresie zabezpieczeń. Użyj zarządzanych tożsamości dla zasobów platformy Azure, aby zezwolić na uwierzytelnianie pod względem dowolnej usługi platformy Azure, która ją obsługuje, w tym Azure Key Vault. W obszarze jest przypisana tożsamość platformy Azure służąca do uwierzytelniania w usłudze Azure Active Directory (Azure AD) i otrzymywania tokenów cyfrowych, które mogą być prezentowane innym usługom platformy Azure, które sprawdzają, czy element jest autoryzowany do uzyskiwania dostępu do usługi i wykonując wymagane akcje.

Należy zauważyć, że tożsamości zarządzane pod nie są przeznaczone do użytku tylko z magazynami systemu Linux i obrazami kontenerów. Udostępnij Azure Key Vault do przechowywania i pobierania kluczy cyfrowych i poświadczeń. Klucze, takie jak te używane do szyfrowania dysków systemu operacyjnego, AKS dane klastra mogą być przechowywane w Azure Key Vault.

Jednostki usługi mogą być również używane w klastrach AKS. Jednak klastry korzystające z jednostek usługi mogą ostatecznie dotrzeć do stanu, w którym należy przeprowadzić odnowienie jednostki usługi w celu zapewnienia działania klastra. Zarządzanie jednostkami usługi zwiększa złożoność, dlatego łatwiej jest używać zarządzanych tożsamości. Te same wymagania dotyczące uprawnień dotyczą zarówno jednostek głównych usługi, jak i zarządzanych tożsamości.

- [Poznaj zarządzane tożsamości i Key Vault za pomocą usługi Azure Kubernetes Service (AKS)](https://docs.microsoft.com/azure/aks/developer-best-practices-pod-security#limit-credential-exposure)

- [Tożsamość usługi Azure AD pod](https://github.com/Azure/aad-pod-identity)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: eliminowanie nieprzewidzianego narażenia na poświadczenia

**Wskazówki**: Implementuj skaner poświadczeń, aby identyfikować poświadczenia w kodzie. Skaner poświadczeń zachęca również do przechodzenia odnalezionych poświadczeń do bardziej bezpiecznych lokalizacji, takich jak Azure Key Vault z zaleceniami.

Ogranicz ekspozycję poświadczeń, nie definiując poświadczeń w kodzie aplikacji. i Unikaj korzystania z poświadczeń udostępnionych. Azure Key Vault należy używać do przechowywania i pobierania kluczy cyfrowych i poświadczeń. Użyj zarządzanych tożsamości dla zasobów platformy Azure, aby umożliwić usłudze na żądanie dostępu do innych zasobów. 

- [Jak skonfigurować skaner poświadczeń](https://secdevtools.azurewebsites.net/helpcredscan.html)

- [Najlepsze rozwiązania dla deweloperów dotyczące zabezpieczeń pod kątem bezpieczeństwa](developer-best-practices-pod-security.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

## <a name="malware-defense"></a>Ochrona przed złośliwym oprogramowaniem

*Aby uzyskać więcej informacji, zobacz temat [Azure Security test: Obrona złośliwego oprogramowania](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-antimalware-software"></a>8,1: Użyj centralnie zarządzanego oprogramowania chroniącego przed złośliwym kodem

**Wskazówki**: AKS zarządza cyklem życia i operacjami węzłów agenta w Twoim imieniu — modyfikowanie zasobów IaaS skojarzonych z węzłami agenta nie jest obsługiwane. Jednak w przypadku węzłów systemu Linux możesz użyć zestawów demonów, aby zainstalować niestandardowe oprogramowanie, takie jak rozwiązanie chroniące przed złośliwym oprogramowaniem.

- [Przewodnik dotyczący alertów zabezpieczeń](../security-center/alerts-reference.md)

- [Alerty dla kontenerów — klastry usługi Azure Kubernetes](https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-akscluster)

- [AKS współdzielona i zestawy demonów](https://docs.microsoft.com/azure/aks/support-policies#shared-responsibility)

**Odpowiedzialność**: Współużytkowane

**Monitorowanie Azure Security Center**: brak

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: przeskanuj pliki przed przekazaniem do zasobów platformy Azure, które nie są obliczeniowe

**Wskazówki**: przeskanuj wstępnie wszystkie pliki przekazywane do zasobów AKS. Użyj wykrywania zagrożeń Security Center dla usług danych w celu wykrywania złośliwego oprogramowania przekazanego do kont magazynu, jeśli używasz konta usługi Azure Storage jako magazynu danych lub śledzenia stanu Terraform dla klastra AKS. 

- [Zrozumienie wykrywania zagrożeń Azure Security Center dla usług danych](../security-center/azure-defender.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="83-ensure-antimalware-software-and-signatures-are-updated"></a>8,3: Upewnij się, że oprogramowanie chroniące przed złośliwym oprogramowaniem i podpisy zostały zaktualizowane

**Wskazówki**: AKS zarządza cyklem życia i operacjami węzłów agenta w Twoim imieniu — modyfikowanie zasobów IaaS skojarzonych z węzłami agenta nie jest obsługiwane. Jednak w przypadku węzłów systemu Linux możesz użyć zestawów demonów, aby zainstalować niestandardowe oprogramowanie, takie jak rozwiązanie chroniące przed złośliwym oprogramowaniem.

- [Przewodnik dotyczący alertów zabezpieczeń](../security-center/alerts-reference.md)

- [Alerty dla kontenerów — klastry usługi Azure Kubernetes](https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-akscluster)

- [AKS współdzielona i zestawy demonów](https://docs.microsoft.com/azure/aks/support-policies#shared-responsibility)

**Odpowiedzialność**: Współużytkowane

**Monitorowanie Azure Security Center**: brak

## <a name="data-recovery"></a>Odzyskiwanie danych

*Aby uzyskać więcej informacji, zobacz [test dotyczący zabezpieczeń platformy Azure: odzyskiwanie danych](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: Zapewnij regularne zautomatyzowane przywracanie awaryjne

**Wskazówki**: Tworzenie kopii zapasowych danych przy użyciu odpowiedniego narzędzia dla typu magazynu, takiego jak Velero, który może tworzyć kopie zapasowe woluminów trwałych wraz z dodatkowymi zasobami i konfiguracjami klastra. Okresowo Sprawdź integralność i bezpieczeństwo tych kopii zapasowych. 

Usuń stan z aplikacji przed utworzeniem kopii zapasowej. W przypadkach, gdy nie można tego zrobić, Utwórz kopię zapasową danych z woluminów trwałych i regularnie Przetestuj operacje przywracania, aby zweryfikować integralność danych i wymagane procesy.

- [Najlepsze rozwiązania dotyczące magazynu i kopii zapasowych w AKS](operator-best-practices-storage.md)

- [Najlepsze rozwiązania dotyczące ciągłości działania i odzyskiwania po awarii w programie AKS](operator-best-practices-multi-region.md)

- [Opis Azure Site Recovery](../site-recovery/site-recovery-overview.md)

- [Jak skonfigurować Velero na platformie Azure](https://github.com/vmware-tanzu/velero-plugin-for-microsoft-azure/blob/master/README.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: wykonaj kompletne kopie zapasowe systemu i Utwórz kopię zapasową wszystkich kluczy zarządzanych przez klienta

**Wskazówki**: Tworzenie kopii zapasowych danych przy użyciu odpowiedniego narzędzia dla typu magazynu, takiego jak Velero, który może tworzyć kopie zapasowe woluminów trwałych wraz z dodatkowymi zasobami i konfiguracjami klastra. 

Wykonywanie zwykłych zautomatyzowanych kopii zapasowych Key Vault certyfikatów, kluczy, zarządzanych kont magazynu i wpisów tajnych za pomocą poleceń programu PowerShell. 

- [Jak utworzyć kopię zapasową certyfikatów Key Vault](/powershell/module/azurerm.keyvault/backup-azurekeyvaultcertificate)

- [Jak utworzyć kopię zapasową kluczy Key Vault](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

- [Jak utworzyć kopię zapasową Key Vault zarządzanych kont magazynu](/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount)

- [Jak utworzyć kopię zapasową Key Vault Secret](/powershell/module/azurerm.keyvault/backup-azurekeyvaultsecret)

- [Jak włączyć Azure Backup](/azure/backup/)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: Weryfikuj wszystkie kopie zapasowe, w tym klucze zarządzane przez klienta

**Wskazówki**: okresowe wykonywanie przywracania danych zawartości w ramach kopii zapasowej Velero. W razie potrzeby przetestuj przywracanie do izolowanej sieci wirtualnej.

Okresowe wykonywanie przywracania danych Key Vault certyfikatów, kluczy, zarządzanych kont magazynu i wpisów tajnych za pomocą poleceń programu PowerShell.

- [Jak przywrócić Key Vault certyfikaty](https://docs.microsoft.com/powershell/module/az.keyvault/restore-azkeyvaultcertificate?view=azps-4.8.0&amp;preserve-view=true)

- [Jak przywrócić klucze Key Vault](https://docs.microsoft.com/powershell/module/az.keyvault/restore-azkeyvaultkey?view=azps-4.8.0&amp;preserve-view=true)

- [Jak przywrócić Key Vault zarządzanych kont magazynu](/powershell/module/az.keyvault/backup-azkeyvaultmanagedstorageaccount)

- [Jak przywrócić Key Vault wpisy tajne](https://docs.microsoft.com/powershell/module/az.keyvault/restore-azkeyvaultsecret?view=azps-4.8.0&amp;preserve-view=true)

- [Jak odzyskać pliki z kopii zapasowej maszyny wirtualnej platformy Azure](/azure/backup/backup-azure-restore-files-from-vm)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: zapewnianie ochrony kopii zapasowych i kluczy zarządzanych przez klienta

**Wskazówki**: Tworzenie kopii zapasowych danych przy użyciu odpowiedniego narzędzia dla typu magazynu, takiego jak Velero, który może tworzyć kopie zapasowe woluminów trwałych wraz z dodatkowymi zasobami i konfiguracjami klastra. 

Włącz Soft-Delete w Key Vault, aby chronić klucze przed przypadkowym lub złośliwym usunięciem, jeśli Azure Key Vault jest używany z wdrożeniami usługi Azure Kubernetes Service (AKS).

- [Omówienie usługi Azure szyfrowanie usługi Storage](../storage/common/storage-service-encryption.md)

- [Jak włączyć Soft-Delete w Key Vault](https://docs.microsoft.com/azure/storage/blobs/soft-delete-blob-overview?tabs=azure-portal)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

## <a name="incident-response"></a>Reagowanie na zdarzenia

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: reagowanie na zdarzenia](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: Tworzenie przewodnika odpowiedzi na zdarzenia

**Wskazówka**: Utwórz przewodnik odpowiedzi na zdarzenia dla swojej organizacji. Upewnij się, że istnieją zarejestrowane plany reakcji na zdarzenia, które definiują wszystkie role pracowników, a także etapy obsługi zdarzeń/zarządzania od wykrywania do oceny po zdarzeniu.

- [Jak skonfigurować automatyzację przepływu pracy w programie Azure Security Center](../security-center/security-center-planning-and-operations-guide.md)

- [Wskazówki dotyczące tworzenia własnego procesu reagowania na zdarzenia zabezpieczeń](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Anatomia incydentu centrum Microsoft Security Response](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Przewodnik obsługi zdarzeń zabezpieczeń komputera NIST](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: Tworzenie oceny incydentu i procedury priorytetyzacji

**Wskazówki**: Określanie priorytetu, które alerty muszą być najpierw badane z przypisaną ważnością Security Center do alertów. Ważność jest oparta na tym, jak dobrze Security Center znajduje się w wyszukiwaniu lub analizach używanych do wystawiania alertu, a także poziom pewności, że istniało złośliwe zamiar w odniesieniu do działania, które doprowadziło do alertu.
Wyraźnie oznaczaj subskrypcje (na przykład produkcyjne, nieprodukcyjne) i Utwórz system nazewnictwa, aby jasno identyfikować i klasyfikować zasoby platformy Azure.

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="103-test-security-response-procedures"></a>10,3: procedury odpowiedzi na zabezpieczenia testowe

**Wskazówki**: przeprowadzanie ćwiczeń do testowania możliwości reagowania na zdarzenia systemu w regularnych erze. Zidentyfikuj słabe punkty i przerwy i popraw plany reagowania na zdarzenia zgodnie z wymaganiami.

- [Przewodnik testowania, uczenia i ćwiczeń programów dla planów i możliwości IT](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: Podaj szczegóły kontaktu dotyczącego zabezpieczeń i Skonfiguruj powiadomienia dotyczące alertów dotyczących zdarzeń związanych z zabezpieczeniami

**Wskazówki**: informacje kontaktowe dotyczące zdarzenia zabezpieczeń będą używane przez firmę Microsoft do skontaktowania się z Tobą, jeśli firma Microsoft Security Response Center (MSRC) wykryje, że dostęp do danych klienta został uzyskany przez nielegalną lub nieautoryzowaną osobę. 

Przejrzyj zdarzenia po fakcie, aby upewnić się, że problemy zostały rozwiązane.

- [Jak ustawić kontakt z zabezpieczeniami Azure Security Center](../security-center/security-center-provide-security-contact-details.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: Uwzględnij alerty zabezpieczeń w systemie odpowiedzi na zdarzenia

**Wskazówki**: eksportowanie alertów Security Center i rekomendacji przy użyciu funkcji eksportu ciągłego. Eksport ciągły umożliwia wyeksportowanie alertów i zaleceń ręcznie lub w stały sposób ciągły. 

Wybierz łącznik danych Security Center, aby przesyłać strumieniowo alerty do platformy Azure, zgodnie z potrzebami i zgodnie z wymaganiami biznesowymi obowiązującymi w organizacji.

- [Jak skonfigurować eksport ciągły](../security-center/continuous-export.md)

- [Jak przesłać strumieniowo alerty do usługi Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: Automatyzowanie odpowiedzi na alerty zabezpieczeń

**Wskazówki**: Użyj funkcji automatyzacji przepływu pracy w programie Security Center, aby automatycznie wyzwalać odpowiedzi za pośrednictwem "Logic Apps" na temat alertów zabezpieczeń i zaleceń.

- [Jak skonfigurować automatyzację przepływu pracy i Logic Apps](../security-center/workflow-automation.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

## <a name="penetration-tests-and-red-team-exercises"></a>Testy penetracyjne i ćwiczenia typu „red team”

*Aby uzyskać więcej informacji, zobacz test [porównawczy zabezpieczeń platformy Azure: testy penetracji i czerwone ćwiczenia zespołu](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: Przeprowadź regularne testowanie penetracji zasobów platformy Azure i zadbaj o skorygowanie wszystkich krytycznych ustaleń dotyczących zabezpieczeń

**Wskazówki**: Postępuj zgodnie z zasadami firmy Microsoft dotyczącymi zaangażowania, aby upewnić się, że testy penetracji nie naruszają zasad firmy Microsoft. Dodatkowe informacje na temat strategii i sposobu wykonywania czerwonych zespołów tworzenia zespołu i testowania aplikacji na żywo w witrynie firmy Microsoft, a w odniesieniu do infrastruktury i usług w chmurze, które znajdują się w odwołaniach.

- [Reguły testów penetracyjnych zaangażowania](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Testy typu „red team” w chmurze firmy Microsoft](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Odpowiedzialność**: Współużytkowane

**Monitorowanie Azure Security Center**: brak

## <a name="next-steps"></a>Następne kroki

- Zobacz [Omówienie testu porównawczego zabezpieczeń platformy Azure w wersji 2](/azure/security/benchmarks/overview)
- Dowiedz się więcej o [punktach odniesienia zabezpieczeń platformy Azure](/azure/security/benchmarks/security-baselines-overview)
