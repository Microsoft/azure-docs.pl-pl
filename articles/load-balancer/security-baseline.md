---
title: Punkt odniesienia zabezpieczeń platformy Azure dla Azure Load Balancer
description: Punkt odniesienia Azure Load Balancer zabezpieczeń zawiera wskazówki proceduralne i zasoby dotyczące wdrażania zaleceń dotyczących zabezpieczeń określonych w te testach porównawczych zabezpieczeń platformy Azure.
author: msmbaldwin
ms.service: load-balancer
ms.topic: conceptual
ms.date: 03/16/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 64e0a8cfcaf00c55038fbe5d1cdc7423b681b690
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2021
ms.locfileid: "107589265"
---
# <a name="azure-security-baseline-for-azure-load-balancer"></a>Punkt odniesienia zabezpieczeń platformy Azure dla Azure Load Balancer

Ten punkt odniesienia zabezpieczeń stosuje wskazówki z testu porównawczego zabezpieczeń platformy [Azure w wersji 1.0](../security/benchmarks/overview-v1.md) do Microsoft Azure Load Balancer. Test porównawczy zabezpieczeń platformy Azure zawiera zalecenia dotyczące sposobu zabezpieczania rozwiązań w chmurze na platformie Azure.
Zawartość jest pogrupowana według **kontrolek zabezpieczeń zdefiniowanych** przez test porównawczy zabezpieczeń platformy Azure i powiązanych wskazówek dotyczących Azure Load Balancer. **Kontrolki** nie dotyczy Azure Load Balancer zostały wykluczone.

 
Aby zobaczyć, Azure Load Balancer mapowanie na test porównawczy zabezpieczeń platformy Azure, zobacz pełny Azure Load Balancer [mapowania punktów odniesienia zabezpieczeń](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Bezpieczeństwo sieci

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: bezpieczeństwo sieci](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1. Ochrona zasobów platformy Azure w sieciach wirtualnych

**Wskazówki:** Użyj wewnętrznych usług Azure Load Balancer, aby zezwolić na ruch do zasobów zaplecza tylko z określonych sieci wirtualnych lub równorzędnych sieci wirtualnych bez narażenia na połączenie z Internetem. Implementowanie zewnętrznego interfejsu Load Balancer z siecią źródłową

Translator adresów (SNAT, Address Translation) do maskuj adresy IP zasobów zaplecza w celu ochrony przed bezpośrednim narażeniem na internet.

Platforma Azure oferuje dwa typy ofert Load Balancer — Standardowa i Podstawowa. Użyj usługa Load Balancer w warstwie Standardowa dla wszystkich obciążeń produkcyjnych. Zaim implementuj sieciowe grupy zabezpieczeń i zezwalaj na dostęp tylko do zaufanych portów i zakresów adresów IP aplikacji. W przypadkach, gdy do podsieci zaplecza lub karty sieciowej maszyn wirtualnych zaplecza nie przypisano żadnej sieciowej grupy zabezpieczeń, ruch z usługi równoważenia obciążenia nie będzie dozwolony dla tych zasobów. W przypadku standardowych usług równoważenia obciążenia podaj reguły ruchu wychodzącego, aby zdefiniować wychodzący nat z sieciową grupą zabezpieczeń. Przejrzyj te reguły ruchu wychodzącego, aby dostosować zachowanie połączeń wychodzących.

Używanie interfejsu usługa Load Balancer w warstwie Standardowa jest zalecane w przypadku obciążeń produkcyjnych, a zwykle podstawowa grupa Load Balancer jest używana tylko do testowania, ponieważ typ podstawowy jest domyślnie otwarty na połączenia z Internetu i nie wymaga sieciowych grup zabezpieczeń do działania.

- [Połączenia wychodzące na platformie Azure](load-balancer-outbound-connections.md)

- [Uaktualnianie usługi Azure Public Load Balancer](upgrade-basic-standard.md)

**Odpowiedzialność**: Klient

**Azure Security Center zabezpieczeń:** Test porównawczy zabezpieczeń platformy [Azure](/azure/governance/policy/samples/azure-security-benchmark) jest domyślną inicjatywą zasad dla usługi Security Center i stanowi podstawę rekomendacji Security Center [firmy .](/azure/security-center/security-center-recommendations) Definicje Azure Policy związane z tą kontrolką są włączane automatycznie przez Security Center. Alerty związane z tą kontrolą mogą [wymagać Azure Defender](/azure/security-center/azure-defender) planu dla powiązanych usług.

**Azure Policy wbudowane definicje — Microsoft.Network:**

[!INCLUDE [Resource Policy for Microsoft.Network 1.1](../../includes/policy/standards/asb/rp-controls/microsoft.network-1-1.md)]

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1.2: Monitorowanie i rejestrowanie konfiguracji i ruchu sieci wirtualnych, podsieci i karty sieciowe

**Wskazówki:** usługa Load Balancer jest usługą pass through, ponieważ opiera się na zasadach sieciowych grup zabezpieczeń stosowanych do zasobów zaplecza i skonfigurowanych reguł ruchu wychodzącego do kontrolowania dostępu do Internetu.

Przejrzyj reguły ruchu wychodzącego skonfigurowane dla usługi usługa Load Balancer w warstwie Standardowa za pomocą bloku Reguły ruchu wychodzącego twojego serwera Load Balancer i bloku reguł równoważenia obciążenia, w którym mogą być włączone niejawne reguły ruchu wychodzącego.

Monitoruj liczbę połączeń wychodzących, aby śledzić, jak często zasoby docierają do Internetu. 

Użyj Security Center i postępuj zgodnie z zaleceniami ochrony sieci, aby ułatwić zabezpieczanie zasobów sieciowych platformy Azure.

Postępuj zgodnie z zaleceniami w zakresie zabezpieczeń zasobów zaplecza, włącz dzienniki przepływu sieciowej grupy zabezpieczeń i wyślij dzienniki na konto usługi Azure Storage w celu inspekcji.

Wyślij również dzienniki przepływu do obszaru roboczego usługi Log Analytics, a następnie użyj Analiza ruchu, aby zapewnić wgląd we wzorce ruchu w chmurze platformy Azure. Zalety usługi Analiza ruchu obejmują możliwość wizualizacji aktywności sieciowej, identyfikowania punktów aktywności i zagrożeń bezpieczeństwa, zrozumienia wzorców przepływu ruchu i identyfikowania błędnych konfiguracji sieci.

- [Jak włączyć dzienniki przepływu sieciowej grupy zabezpieczeń](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Jak włączyć i używać Analiza ruchu](../network-watcher/traffic-analytics.md)

- [Informacje o zabezpieczeniach sieci zapewnianych przez Azure Security Center](../security-center/security-center-network-recommendations.md)

- [Jak mogę sprawdź statystyki połączeń wychodzących](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-diagnostics#how-do-i-check-my-outbound-connection-statistics)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Test [porównawczy zabezpieczeń](/azure/governance/policy/samples/azure-security-benchmark) platformy Azure jest domyślną inicjatywą zasad dla usługi Security Center i stanowi podstawę Security Center [rekomendacji firmy](/azure/security-center/security-center-recommendations). Definicje Azure Policy związane z tą kontrolką są włączane automatycznie przez Security Center. Alerty związane z tą kontrolą mogą [wymagać Azure Defender](/azure/security-center/azure-defender) planu dla powiązanych usług.

**Azure Policy wbudowane definicje — Microsoft.Network**:

[!INCLUDE [Resource Policy for Microsoft.Network 1.2](../../includes/policy/standards/asb/rp-controls/microsoft.network-1-2.md)]

### <a name="13-protect-critical-web-applications"></a>1.3: Ochrona krytycznych aplikacji internetowych

**Wskazówki:** Jawnie zdefiniuj łączność z Internetem i prawidłowe źródłowe ip za pośrednictwem reguł ruchu wychodzącego i sieciowych grup zabezpieczeń wraz z Load Balancer, aby używać analizy zagrożeń firmy Microsoft do ochrony aplikacji internetowych.

- [Integrowanie Azure Firewall](../firewall/integrate-lb.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Odmowa komunikacji ze znanymi złośliwymi adresami IP

**Wskazówki:** włącz standardową ochronę przed atakami DDoS (Distributed Denial of Service) platformy Azure na platformie Azure Virtual Network ochronę przed atakami DDoS. 

Wdrażaj Azure Firewall na poszczególnych granicach sieci organizacji z włączonym filtrowaniem opartym na analizie zagrożeń i skonfigurowanym na "Alert i odmowa" dla złośliwego ruchu sieciowego.

 

Użyj Security Center ochrony przed zagrożeniami, aby wykryć komunikację ze znanymi złośliwymi adresami IP. 

Ta usługa Load Balancer w warstwie Standardowa jest domyślnie bezpieczna i stanowi część prywatnej i izolowanej Virtual Network. Jest ona zamknięta dla przepływów przychodzących, chyba że otwarte przez sieciowe grupy zabezpieczeń jawnie zezwalają na dozwolony ruch i nie zezwalają na znane złośliwe adresy IP. O ile sieciowa grupa zabezpieczeń w podsieci lub karty sieciowej zasobu maszyny wirtualnej nie istnieje za Load Balancer, ruch nie może docierać do tego zasobu. 

Wdrażaj Azure Firewall na poszczególnych granicach sieci organizacji z włączonym filtrowaniem opartym na analizie zagrożeń i skonfigurowanym na "Alert i odmowa" dla złośliwego ruchu sieciowego, aby zapobiec atakom ze złośliwych adresów IP. 

Implementowanie wskazówek w Azure Firewall z Load Balancer.

Używaj Security Center ochrony przed zagrożeniami do wykrywania komunikacji ze znanymi złośliwymi adresami IP. 

Security Center (warstwa Standardowa) zapewnia dostęp just in time do maszyny wirtualnej i konfiguruje dozwolone źródłowe adresy IP tak, aby zezwalały na dostęp tylko z zatwierdzonych adresów/zakresów ADRESÓW IP.
 

Użyj Security Center adaptacyjnego wzmacniania zabezpieczeń sieci firmy Microsoft, aby zalecić konfiguracje sieciowych grup zabezpieczeń, które ograniczają porty i źródłowe ip na podstawie rzeczywistego ruchu i analizy zagrożeń.
 

- [Zarządzanie Azure DDoS Protection Standardowa przy użyciu Azure Portal](../ddos-protection/manage-ddos-protection.md)

- [Azure Firewall filtrowanie na podstawie analizy zagrożeń](../firewall/threat-intel.md)

- [Ochrona przed zagrożeniami w usłudze Azure Security Center](../security-center/azure-defender.md)

- [Zabezpieczanie portów zarządzania przy użyciu dostępu just in time](../security-center/security-center-just-in-time.md)

- [Adaptacyjne wzmacnianie zabezpieczeń sieci w Azure Security Center](../security-center/security-center-adaptive-network-hardening.md)

- [Integrowanie Azure Firewall z Load Balancer](../firewall/overview.md)

**Odpowiedzialność**: Klient

**Azure Security Center:** Test porównawczy zabezpieczeń platformy [Azure](/azure/governance/policy/samples/azure-security-benchmark) to domyślna inicjatywa zasad dla usługi Security Center i stanowi podstawę rekomendacji Security Center [firmy .](/azure/security-center/security-center-recommendations) Definicje Azure Policy związane z tą kontrolką są włączane automatycznie przez Security Center. Alerty związane z tą kontrolą mogą [wymagać Azure Defender](/azure/security-center/azure-defender) planu dla powiązanych usług.

**Azure Policy wbudowane definicje — Microsoft.Network:**

[!INCLUDE [Resource Policy for Microsoft.Network 1.4](../../includes/policy/standards/asb/rp-controls/microsoft.network-1-4.md)]

### <a name="15-record-network-packets"></a>1.5. Rejestrowanie pakietów sieciowych

**Wskazówki:** Włącz Network Watcher przechwytywanie pakietów w celu zbadania anomalii działań.

- [Jak utworzyć wystąpienie Network Watcher danych](../network-watcher/network-watcher-create.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Test [porównawczy zabezpieczeń](/azure/governance/policy/samples/azure-security-benchmark) platformy Azure jest domyślną inicjatywą zasad dla usługi Security Center i stanowi podstawę Security Center [rekomendacji firmy](/azure/security-center/security-center-recommendations). Definicje Azure Policy związane z tą kontrolką są włączane automatycznie przez Security Center. Alerty związane z tą kontrolą mogą [wymagać Azure Defender](/azure/security-center/azure-defender) planu dla powiązanych usług.

**Azure Policy wbudowane definicje — Microsoft.Network**:

[!INCLUDE [Resource Policy for Microsoft.Network 1.5](../../includes/policy/standards/asb/rp-controls/microsoft.network-1-5.md)]

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6. Wdrażanie opartych na sieci systemów wykrywania włamań/zapobiegania włamań (IDS/IPS)

**Wskazówki:** Zaim implementuj ofertę z Azure Marketplace, która obsługuje funkcje IDS/IPS z możliwościami inspekcji ładunku w środowisku Load Balancer. 

Użyj Azure Firewall analizy zagrożeń, jeśli inspekcja ładunku nie jest wymagana. Azure Firewall filtrowania opartego na analizie zagrożeń służy do alertów i/lub blokowania ruchu do i ze znanych złośliwych adresów IP i domen. Adresy IP i domeny pochodzą z kanału informacyjnego analizy zagrożeń firmy Microsoft.

Wdrożenie wybranego rozwiązania zapory na poszczególnych granicach sieci organizacji w celu wykrywania i/lub blokowania złośliwego ruchu.

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

- [Jak wdrożyć Azure Firewall](../firewall/tutorial-firewall-deploy-portal.md)

- [Jak skonfigurować alerty za pomocą Azure Firewall](../firewall/threat-intel.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Zarządzanie ruchem do aplikacji internetowych

**Wskazówki:** Jawnie zdefiniuj łączność z Internetem i prawidłowe źródłowe ip za pośrednictwem reguł ruchu wychodzącego i sieciowych grup zabezpieczeń Load Balancer do używania funkcji analizy zagrożeń firmy Microsoft w celu ochrony aplikacji internetowych.

- [Integrowanie Azure Firewall](../firewall/integrate-lb.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimalizowanie złożoności i obciążeń administracyjnych reguł zabezpieczeń sieci

**Wskazówki:** Podczas tworzenia reguł zabezpieczeń używaj tagów usługi w miejsce określonych adresów IP. Określ nazwę tagu usługi w polu źródłowym lub docelowym reguły, aby zezwolić na ruch dla odpowiedniej usługi lub go odrzucić. 

Firma Microsoft zarządza prefiksami adresów obejmującymi tag usługi i automatycznie aktualizuje tag usługi w przypadku zmiany adresów. 

Domyślnie każda sieciowa grupa zabezpieczeń zawiera tag usługi AzureLoadBalancer, aby zezwolić na ruch sondy kondycji. 

Zapoznaj się z dokumentacją platformy Azure, aby uzyskać informacje o wszystkich tagach usługi dostępnych do użycia w zasadach sieciowej grupy zabezpieczeń.

- [Dostępne tagi usługi](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#available-service-tags)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Obsługa standardowych konfiguracji zabezpieczeń dla urządzeń sieciowych

**Wskazówki:** Definiowanie i implementowanie standardowych konfiguracji zabezpieczeń dla zasobów sieciowych za pomocą Azure Policy.

Użyj Azure Blueprints, aby uprościć wdrożenia platformy Azure na dużą skalę przez pakowanie kluczowych artefaktów środowiska, takich jak szablony usługi Azure Resources Manager, kontrolki RBAC platformy Azure i zasady, w pojedynczej definicji strategii. 

Stosowanie strategii do nowych subskrypcji oraz dostosowywanie kontroli i zarządzania za pomocą wersji.

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy przykłady dotyczące sieci](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#network)

- [Jak utworzyć usługę Azure Blueprint](../governance/blueprints/create-blueprint-portal.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="110-document-traffic-configuration-rules"></a>1.10: Udokumentowanie reguł konfiguracji ruchu

**Wskazówki:** Użyj tagów zasobów dla sieciowych grup zabezpieczeń i innych zasobów związanych z zabezpieczeniami sieci i przepływem ruchu. 

Użyj pola "Opis", aby udokumentować reguły zezwalania na ruch do/z sieci dla poszczególnych reguł sieciowej grupy zabezpieczeń.

Zaim implementuj dowolne wbudowane definicje usługi Azure Policy związane z tagami, takie jak "Wymagaj tagu i jego wartości", co gwarantuje, że wszystkie zasoby są tworzone przy użyciu tagów i powiadamianie o wszelkich istniejących nieotagowanych zasobach.

Użyj Azure PowerShell lub interfejsu wiersza polecenia platformy Azure, aby szukać lub wykonywać akcje na zasobach na podstawie ich tagów.

- [Jak tworzyć tagi i ich używać](../azure-resource-manager/management/tag-resources.md)

- [Jak utworzyć usługę Azure Virtual Network](../virtual-network/quick-create-portal.md)

- [Jak filtrować ruch sieciowy przy użyciu reguł sieciowej grupy zabezpieczeń](../virtual-network/tutorial-filter-network-traffic.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11. Monitorowanie konfiguracji zasobów sieciowych i wykrywanie zmian za pomocą zautomatyzowanych narzędzi

**Wskazówki:** Użyj dziennika aktywności platformy Azure, aby monitorować konfiguracje zasobów i wykrywać zmiany w zasobach platformy Azure. 

Utwórz alerty w Azure Monitor powiadamiania o zmianie krytycznych zasobów.

- [Jak wyświetlać i pobierać zdarzenia dziennika aktywności platformy Azure](https://docs.microsoft.com/azure/azure-monitor/essentials/activity-log#view-the-activity-log)

- [Jak tworzyć alerty w Azure Monitor](../azure-monitor/alerts/alerts-activity-log.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

## <a name="logging-and-monitoring"></a>Rejestrowanie i monitorowanie

*Aby uzyskać więcej informacji, zobacz [Test porównawczy zabezpieczeń platformy Azure: rejestrowanie i monitorowanie.](../security/benchmarks/security-control-logging-monitoring.md)*

### <a name="22-configure-central-security-log-management"></a>2.2. Konfigurowanie centralnego zarządzania dziennikami zabezpieczeń

**Wskazówki:** Przejrzyj zmiany reguł ruchu wychodzącego i sieciowych grup zabezpieczeń dla usług Load Balancer, wyświetlając dziennik aktywności w subskrypcjach. 

Wyświetl dzienniki hosta wewnętrznego, aby upewnić się, że zasoby zaplecza są bezpieczne.

Wyeksportuj te dzienniki do usługi Log Analytics lub innej platformy magazynu. W Azure Monitor obszarów roboczych usługi Log Analytics można wykonywać zapytania i przeprowadzać analizy, a także używać kont usługi Azure Storage do przechowywania długoterminowego i archiwalnej.

Włącz i włącz te dane w celu Azure Sentinel rozwiązania SIEM lub rozwiązania SIEM innej firmy na podstawie wymagań biznesowych organizacji.

- [Jak do dołączać Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Jak zbierać dzienniki platformy i metryki za pomocą Azure Monitor](../azure-monitor/essentials/diagnostic-settings.md)

- [Jak zbierać dzienniki hostów wewnętrznych maszyny wirtualnej platformy Azure za pomocą Azure Monitor](../azure-monitor/vm/quick-collect-azurevm.md)

- [Jak rozpocząć pracę z Azure Monitor integracją rozwiązania SIEM i rozwiązania SIEM innej firmy](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

- [Dzienniki aktywności platformy](../azure-monitor/essentials/activity-log.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3. Włączanie rejestrowania inspekcji dla zasobów platformy Azure

**Wskazówki:** Przejrzyj informacje rejestrowania i inspekcji płaszczyzny sterowania i zarządzania przechwycone za pomocą dzienników aktywności dla podstawowego Load Balancer. Te ustawienia przechwytywania są domyślnie włączone. 

Użyj dzienników aktywności, aby monitorować akcje dotyczące zasobów w celu wyświetlenia wszystkich działań i ich stanu. 

Określ, jakie operacje zostały wykonane na zasobach w subskrypcji, za pomocą dzienników aktywności: 

- kto uruchomił operację
- czas, w którym wystąpiła operacja
- stan operacji

- wartości innych właściwości, które mogą ułatwić badanie operacji

Pobierz informacje z dziennika aktywności za pomocą Azure PowerShell, interfejsu wiersza polecenia platformy Azure, interfejsu API REST platformy Azure lub interfejsu Azure Portal. 

Implementowanie wielowymiarowej diagnostyki dla usługa Load Balancer w warstwie Standardowa konfiguracji za pomocą Azure Monitor.  Obejmują one dostępne metryki zabezpieczeń, w tym informacje o portach i połączeniach translatora adresów sieciowych źródła (SNAT). Dostępne są również dodatkowe metryki dotyczące pakietów SYN (synchronizowanie) i liczników pakietów. 

Pobierz metryki wielowymiarowe programowo za pośrednictwem interfejsów API i zapisz je na koncie magazynu za pomocą opcji "Wszystkie metryki".

Pakiet zawartości Dzienników inspekcji platformy Azure z usługą Microsoft Power BI umożliwia analizowanie danych przy użyciu wstępnie skonfigurowanych pulpitów nawigacyjnych lub dostosowywanie widoków na podstawie wymagań.

Przesyłanie strumieniowe dzienników do centrum zdarzeń lub obszaru roboczego usługi Log Analytics. Można je również wyodrębniać z usługi Azure Blob Storage i wyświetlać w różnych narzędziach, takich jak program Excel i Power BI. 

Włącz i włącz dane w celu Azure Sentinel rozwiązania SIEM lub rozwiązania SIEM innej firmy w zależności od wymagań biznesowych.

- [Zapoznaj się z tym artykułem z instrukcjami krok po kroku dla każdej metody szczegółowo opisanej w artykule Audit operations with Resource Manager (Operacje inspekcji za pomocą Resource Manager](../azure-resource-manager/management/view-activity-logs.md)

- [Dzienniki usługi Azure Monitor dla usługi Load Balancer w warstwie Podstawowa](load-balancer-monitor-log.md)

- [Wyświetlanie dzienników aktywności w celu monitorowania akcji w zasobach](../azure-resource-manager/management/view-activity-logs.md)

- [Programowe pobieranie metryk wielowymiarowych za pośrednictwem interfejsów API](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-diagnostics#retrieve-multi-dimensional-metrics-programmatically-via-apis)

- [Jak rozpocząć pracę z Azure Monitor integracją rozwiązania SIEM i rozwiązania SIEM innej firmy](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="25-configure-security-log-storage-retention"></a>2.5. Konfigurowanie przechowywania magazynu dzienników zabezpieczeń

**Wskazówki:** Dziennik aktywności jest domyślnie włączony i jest zachowywany przez 90 dni w magazynie dzienników zdarzeń platformy Azure. Ustaw okres przechowywania obszaru roboczego usługi Log Analytics zgodnie z przepisami dotyczącymi zgodności obowiązującymi w Azure Monitor. Użyj kont usługi Azure Storage do przechowywania długoterminowego i archiwalnej.

- [Wyświetlanie dzienników aktywności w celu monitorowania akcji w zasobach](../azure-resource-manager/management/view-activity-logs.md)

- [Zmienianie okresu przechowywania danych w u usługi Log Analytics](https://docs.microsoft.com/azure/azure-monitor/logs/manage-cost-storage#change-the-data-retention-period)

- [Jak skonfigurować zasady przechowywania dla dzienników konta usługi Azure Storage](https://docs.microsoft.com/azure/storage/common/manage-storage-analytics-logs#configure-logging)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="26-monitor-and-review-logs"></a>2.6. Monitorowanie i przeglądanie dzienników

**Wskazówki:** Monitorowanie zasobów usługi usługa Load Balancer w warstwie Standardowa oraz zarządzanie nimi przy użyciu strony Load Balancer na stronie Azure Portal oraz strony Resource Health w Azure Monitor. Dostępne metryki zabezpieczeń obejmują informacje o portach i połączeniach translatora adresów sieciowych źródła (SNAT). Dostępne są również metryki dotyczące pakietów SYN (synchronizowanie) i liczników pakietów. 

Użyj Azure Monitor, aby przejrzeć stan sondy kondycji punktu końcowego za pomocą metryk wielowymiarowych dla standardowych, zewnętrznych i wewnętrznych usług równoważenia obciążenia. Zbierz te metryki programowo za pośrednictwem interfejsów API i zapisane na koncie magazynu za pomocą opcji "Wszystkie metryki".

Azure Monitor nie są dostępne dla wewnętrznych podstawowych równoważenia obciążenia. 

Użyj Azure Monitor, aby wyświetlić podsumowanie stanu sondy kondycji na pulę zaplecza dla podstawowego zewnętrznego serwera Load Balancer, na przykład liczbę wystąpień w puli zaplecza, które nie odbierają żądań z puli Load Balancer z powodu błędów sondy kondycji. 

Zaim implementuj rejestrowanie za pomocą usługi Azure Operational Insights, aby dostarczać statystyki dotyczące stanu kondycji usługi równoważenia obciążenia. 

Użyj dzienników aktywności, aby wyświetlić alerty i monitorować akcje dotyczące zasobów i ich stanu w subskrypcjach platformy Azure. Dzienniki aktywności są domyślnie włączone i można je wyświetlać w Azure Portal. 

Użyj usługi Microsoft Power BI z pakietem zawartości dzienników inspekcji platformy Azure i analizuj dane przy użyciu wstępnie skonfigurowanych pulpitów nawigacyjnych. Dostosuj widoki do swoich wymagań w zależności od potrzeb biznesowych. 

Przesyłanie strumieniowe dzienników do centrum zdarzeń lub obszaru roboczego usługi Log Analytics. Można je również wyodrębniać z usługi Azure Blob Storage i wyświetlać w różnych narzędziach, takich jak program Excel i Power BI. Możesz włączyć i dołączyć dane do rozwiązania Azure Sentinel lub rozwiązania SIEM innej firmy.

- [Sondy kondycji usługi Load Balancer](load-balancer-custom-probe-overview.md)

- [Interfejs API REST usługi Azure Monitor](/rest/api/monitor)

- [Jak pobierać metryki za pośrednictwem interfejsu API REST](/rest/api/monitor/metrics/list)

- [usługa Load Balancer w warstwie Standardowa diagnostyki za pomocą metryk, alertów i kondycji zasobów](load-balancer-standard-diagnostics.md)

- [Dzienniki usługi Azure Monitor dla usługi Load Balancer w warstwie Podstawowa](load-balancer-monitor-log.md)

- [Wyświetlanie metryk równoważenia obciążenia w Azure Portal](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-diagnostics#view-your-load-balancer-metrics-in-the-azure-portal)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7. Włącz alerty dla działań anomalii

**Wskazówki:** Monitorowanie Security Center obszaru roboczego usługi Log Analytics i alerty dotyczące anomalii związanych z Load Balancer w dziennikach zabezpieczeń i zdarzeniach.

Włącz i włącz dane do Azure Sentinel lub narzędzia SIEM innej firmy.

- [Jak do dołączać Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Jak zarządzać alertami w Azure Security Center](../security-center/security-center-managing-and-responding-alerts.md)

- [Jak rejestrować alerty dotyczące danych dzienników analizy dzienników](../azure-monitor/alerts/tutorial-response.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

## <a name="identity-and-access-control"></a>Tożsamość i kontrola dostępu

*Aby uzyskać więcej informacji, zobacz Test porównawczy zabezpieczeń platformy [Azure: tożsamość i Access Control](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Utrzymywanie spisu kont administracyjnych

**Wskazówki:** Kontrola dostępu oparta na rolach (RBAC) platformy Azure umożliwia zarządzanie dostępem do zasobów platformy Azure, takich jak Load Balancer za pomocą przypisań ról. Przypisz te role do użytkowników, grupuje jednostki usługi i tożsamości zarządzane.

Wstępnie zdefiniowane i wbudowane role spisu dla niektórych zasobów za pomocą narzędzi, takich jak interfejs wiersza polecenia platformy Azure Azure PowerShell lub Azure Portal.

- [Jak uzyskać rolę katalogu w usłudze Azure Active Directory (Azure AD) przy użyciu programu PowerShell](/powershell/module/azuread/get-azureaddirectoryrole)

- [Jak uzyskać członków roli katalogu w usłudze Azure AD przy użyciu programu PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Używaj uwierzytelniania wieloskładnikowego dla wszystkich Azure Active Directory dostępu opartego na danych

**Wskazówki:** włącz Azure Active Directory wieloskładnikowego (Azure AD) i postępuj zgodnie Security Center z zaleceniami dotyczącymi zarządzania tożsamościami i dostępem.

- [Jak włączyć uwierzytelnianie wieloskładnikowe na platformie Azure](../active-directory/authentication/howto-mfa-getstarted.md) 

- [Jak monitorować tożsamość i dostęp w Azure Security Center](../security-center/security-center-identity-access.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6. Użyj dedykowanych maszyn (stacji roboczych z dostępem uprzywilejowanym) do wykonywania wszystkich zadań administracyjnych

**Wskazówki:** Używanie stacji roboczych z dostępem uprzywilejowanym (PAW) z uwierzytelnianiem wieloskładnikowym skonfigurowanym do zarządzania zasobami sieci platformy Azure i uzyskiwania do nich dostępu. 

- [Dowiedz się więcej o stacjach roboczych z dostępem uprzywilejowanym](/security/compass/privileged-access-devices)

- [Jak włączyć uwierzytelnianie wieloskładnikowe na platformie Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="38-manage-azure-resources-only-from-approved-locations"></a>3.8: Zarządzanie zasobami platformy Azure tylko z zatwierdzonych lokalizacji

**Wskazówki:** Użyj dostępu warunkowego nazwanych lokalizacji, aby zezwolić na dostęp tylko z określonych grup logicznych zakresów adresów IP lub krajów/regionów.

- [Jak skonfigurować nazwane lokalizacje na platformie Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="39-use-azure-active-directory"></a>3.9. Użyj Azure Active Directory

**Wskazówki:** Użyj Azure Active Directory (Azure AD) jako centralnego systemu uwierzytelniania i autoryzacji dla usług. Usługa Azure AD chroni dane przy użyciu silnego szyfrowania danych magazynowanego i podczas ich przesyłania, a także insygnia, skróty i bezpiecznie przechowuje poświadczenia użytkownika.  

- [Jak utworzyć i skonfigurować wystąpienie usługi Azure AD](../active-directory-domain-services/tutorial-create-instance.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Regularne przeglądanie i uzgadnianie dostępu użytkowników

**Wskazówki:** użyj usługi Azure Active Directory (Azure AD), aby udostępnić dzienniki, aby ułatwić odnajdywanie nieaktywnego konta. 

Przeglądy dostępu tożsamości platformy Azure można wykonywać w celu efektywnego zarządzania członkostwem w grupach, dostępem do aplikacji dla przedsiębiorstw i przypisań ról. Dostęp użytkowników powinien być regularnie przeglądany, aby upewnić się, że tylko aktywni użytkownicy mają dalszy dostęp.

- [Opis raportowania w usłudze Azure AD](/azure/active-directory/reports-monitoring/)

- [Jak używać przeglądów dostępu do tożsamości platformy Azure](../active-directory/governance/access-reviews-overview.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Monitorowanie prób uzyskania dostępu do dezaktywowanych poświadczeń

**Wskazówki:** Zintegruj źródła dzienników aktywności logowania, inspekcji i ryzyka związanego z logowaniem do usługi Azure Active Directory (Azure AD) z dowolnym narzędziem SIEM lub monitorowaniem opartym na Twoim dostępie.

Usprawnij ten proces, tworząc ustawienia diagnostyczne dla kont użytkowników usługi Azure AD oraz wysyłając dzienniki inspekcji i dzienniki logowania do obszaru roboczego usługi Log Analytics. Wszystkie żądane alerty można skonfigurować w obszarze roboczym usługi Log Analytics.

- [Jak zintegrować dzienniki aktywności platformy Azure z usługą Azure Monitor](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Alert w przypadku odchylenia zachowania logowania na koncie

**Wskazówki:** korzystanie Azure Active Directory usługi Azure AD (Risk and Identity Protection) w celu konfigurowania automatycznych odpowiedzi na wykryte podejrzane akcje związane z tożsamościami użytkowników. Pozysuj dane do Azure Sentinel do dalszych badań.

- [Jak wyświetlić ryzykowne logowania w usłudze Azure AD](/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

- [Jak skonfigurować i włączyć zasady ryzyka związane z usługą Identity Protection](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Jak do dołączać Azure Sentinel](../sentinel/quickstart-onboard.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

## <a name="data-protection"></a>Ochrona danych

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: ochrona danych](../security/benchmarks/security-control-data-protection.md).*

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4. Szyfrowanie wszystkich informacji poufnych podczas przesyłania

**Wskazówki:** Upewnij się, że każdy klient łączący się z zasobami platformy Azure może negocjować TLS 1.2 lub nowszą.

Postępuj Azure Security Center dotyczących szyfrowania danych w spoczynku i szyfrowania podczas przesyłania, jeśli ma to zastosowanie.

- [Opis szyfrowania podczas przesyłania za pomocą platformy Azure](https://docs.microsoft.com/azure/security/fundamentals/encryption-overview#encryption-of-data-in-transit)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4.6. Zarządzanie dostępem do zasobów przy użyciu kontroli RBAC platformy Azure

**Wskazówki:** Kontrola dostępu na platformie Azure przy użyciu kontroli dostępu na Load Balancer zasobów.

- [Jak skonfigurować RBAC platformy Azure](../role-based-access-control/role-assignments-portal.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7. Wymuszanie kontroli dostępu przy użyciu ochrony przed utratą danych opartej na hoście

**Wskazówki:** Load Balancer to usługa pass through, która nie przechowuje danych klientów. Jest to część podstawowej platformy, która jest zarządzana przez firmę Microsoft. 

Firma Microsoft traktuje całą zawartość klientów jako poufne i bardzo długo chroni przed utratą i utratą danych klientów. 

Aby zapewnić bezpieczeństwo danych klientów na platformie Azure, firma Microsoft wdrożyła i utrzymuje pakiet niezawodnych funkcji i kontroli ochrony danych. 

- [Informacje na temat ochrony danych klientów na platformie Azure](../security/fundamentals/protection-customer-data.md)

**Odpowiedzialność**: Współużytkowane

**Azure Security Center monitorowania:** Brak

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9. Rejestrowanie zmian krytycznych zasobów platformy Azure i alerty dotyczące zmian

**Wskazówki:** użyj Azure Monitor z dziennika aktywności platformy Azure, aby tworzyć alerty w przypadku zmiany krytycznych zasobów platformy Azure, takich jak usługi Load Balancer używane w ważnych obciążeniach produkcyjnych.

- [Jak tworzyć alerty dla zdarzeń dziennika aktywności platformy Azure](../azure-monitor/alerts/alerts-activity-log.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

## <a name="inventory-and-asset-management"></a>Zarządzanie magazynem i zasobami

*Aby uzyskać więcej informacji, zobacz [Test porównawczy zabezpieczeń platformy Azure: spis i zarządzanie zasobami.](../security/benchmarks/security-control-inventory-asset-management.md)*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1. Korzystanie z rozwiązania do automatycznego odnajdywania zasobów

**Wskazówki:** Użyj Azure Resource Graph do wyszukiwania i odnajdywania wszystkich zasobów (takich jak obliczenia, magazyn, sieć, porty, protokoły i tak dalej) w subskrypcjach. Azure Resource Manager do tworzenia i używania bieżących zasobów.

Zapewnij odpowiednie uprawnienia (odczyt) w dzierżawie i wylicz wszystkie subskrypcje i zasoby platformy Azure w swoich subskrypcjach.

- [Jak tworzyć zapytania za pomocą eksploratora usługi Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

- [Jak wyświetlać subskrypcje platformy Azure](/powershell/module/az.accounts/get-azsubscription)

- [Opis kontroli RBAC na platformie Azure](../role-based-access-control/overview.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="62-maintain-asset-metadata"></a>6.2. Obsługa metadanych zasobów

**Wskazówki:** Stosowanie tagów do zasobów platformy Azure z metadanymi w celu logicznego organizowania zgodnie z taksonomią.

- [Jak tworzyć tagi i ich używać](/azure/azure-resource-manager/resource-group-using-tags)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="63-delete-unauthorized-azure-resources"></a>6.3. Usuwanie nieautoryzowanych zasobów platformy Azure

**Wskazówki:** do organizowania i śledzenia zasobów używaj tagowania, grup zarządzania i oddzielnych subskrypcji tam, gdzie jest to konieczne. 

Regularnie uzgadniaj spis i upewnij się, że nieautoryzowane zasoby zostaną usunięte z subskrypcji w terminowy sposób.

- [Jak utworzyć dodatkowe subskrypcje platformy Azure](../cost-management-billing/manage/create-subscription.md)

- [Jak tworzyć grupy zarządzania](../governance/management-groups/create-management-group-portal.md)

- [Jak tworzyć tagi i ich używać](../azure-resource-manager/management/tag-resources.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6.4. Definiowanie i utrzymywanie spisu zatwierdzonych zasobów platformy Azure

**Wskazówki:** utwórz listę zatwierdzonych zasobów platformy Azure zgodnie z potrzebami organizacji, które można wykorzystać jako mechanizm listy zezwalania. Umożliwi to organizacji dołączanie wszelkich nowo dostępnych usług platformy Azure po ich formalnym przejrzeniu i zatwierdzeniu przez typowe procesy oceny zabezpieczeń w organizacji.

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Monitorowanie niezatwierdzonych zasobów platformy Azure

**Wskazówki:** użyj Azure Policy, aby wprowadzić ograniczenia dotyczące typu zasobów, które mogą być tworzone w twoich subskrypcjach.

Wykonywanie zapytań o zasoby i odnajdywanie ich za Azure Resource Graph w posiadanych subskrypcjach. 

Upewnij się, że wszystkie zasoby platformy Azure obecne w środowisku zostały zatwierdzone.

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

- [Jak tworzyć zapytania za pomocą eksploratora usługi Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="69-use-only-approved-azure-services"></a>6.9: Używaj tylko zatwierdzonych usług platformy Azure

**Wskazówki:** użyj Azure Policy, aby wprowadzić ograniczenia dotyczące typu zasobów, które mogą być tworzone w subskrypcjach klientów przy użyciu następujących wbudowanych definicji zasad:
- Niedozwolone typy zasobów
- Dozwolone typy zasobów

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

- [Jak odmówić określonego typu zasobu za pomocą Azure Policy](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

- [Przykładowe wbudowane zasady platformy Azure dla sieci wirtualnej](/azure/virtual-network/policy-samples)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Ograniczanie możliwości interakcji użytkowników z Azure Resource Manager

**Wskazówki:** użyj Azure Active Directory warunkowego usługi (Azure AD), aby ograniczyć użytkownikom możliwość interakcji z usługą Azure Resource Manager, konfigurując opcję "Blokuj dostęp" dla aplikacji "Microsoft Azure Management".

- [Jak skonfigurować dostęp warunkowy, aby zablokować dostęp do usługi Azure Resources Manager](../role-based-access-control/conditional-access-azure-management.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Fizyczne lub logiczne rozdzielenie aplikacji wysokiego ryzyka

**Wskazówki:** Oprogramowanie, które jest wymagane dla operacji biznesowych, ale może ponosić wyższe ryzyko dla organizacji, powinno być izolowane w ramach własnej maszyny wirtualnej i/lub sieci wirtualnej i wystarczająco zabezpieczone za pomocą sieciowej grupy zabezpieczeń Azure Firewall lub sieciowej grupy zabezpieczeń.

- [Jak utworzyć sieć wirtualną](../virtual-network/quick-create-portal.md)

- [Jak utworzyć sieciową grupę zabezpieczeń za pomocą konfiguracji zabezpieczeń](../virtual-network/tutorial-filter-network-traffic.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

## <a name="secure-configuration"></a>Bezpieczna konfiguracja

*Aby uzyskać więcej informacji, zobacz [Test porównawczy zabezpieczeń platformy Azure: bezpieczna konfiguracja.](../security/benchmarks/security-control-secure-configuration.md)*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1. Ustanów bezpieczne konfiguracje dla wszystkich zasobów platformy Azure

**Wskazówki:** użyj Azure Policy, aby utworzyć zasady niestandardowe do inspekcji lub wymuszania konfiguracji zasobów platformy Azure. Użyj wbudowanych definicji Azure Policy danych.

Azure Resource Manager szablon można wyeksportować w pliku JavaScript Object Notation (JSON), który należy przejrzeć, aby upewnić się, że konfiguracje spełniają wymagania dotyczące zabezpieczeń w organizacji.

Wyeksportuj szablony Azure Resource Manager do formatu JavaScript Object Notation (JSON) i okresowo je przeglądaj, aby upewnić się, że konfiguracje spełniają wymagania organizacji dotyczące zabezpieczeń.

Implementowanie zaleceń Security Center jako bezpiecznej konfiguracji odniesienia dla zasobów platformy Azure.

- [Jak wyświetlić dostępne Azure Policy aliasy](/powershell/module/az.resources/get-azpolicyalias)

- [Samouczek: tworzenie zasad i zarządzanie nimi w celu wymuszania zgodności](../governance/policy/tutorials/create-and-manage.md)

- [Eksportowanie pojedynczego i wielu zasobów do szablonu w Azure Portal](../azure-resource-manager/templates/export-template-portal.md)

- [Zalecenia dotyczące zabezpieczeń — przewodnik referencyjny](../security-center/recommendations-reference.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Obsługa bezpiecznych konfiguracji zasobów platformy Azure

**Wskazówki:** użyj Azure Policy [odmowa] i [wd wdrażaj, jeśli nie istnieje], aby wymusić bezpieczne ustawienia w zasobach platformy Azure.  Ponadto możesz użyć szablonów Azure Resource Manager, aby zachować konfigurację zabezpieczeń zasobów platformy Azure wymaganą przez organizację. 

- [Zrozumienie Azure Policy danych](../governance/policy/concepts/effects.md)

- [Tworzenie zasad i zarządzanie nimi w celu wymuszania zgodności](../governance/policy/tutorials/create-and-manage.md)

- [omówienie Azure Resource Manager szablonów](../azure-resource-manager/templates/overview.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Bezpieczne przechowywanie konfiguracji zasobów platformy Azure

**Wskazówki:** użyj Azure DevOps do bezpiecznego przechowywania kodu i zarządzania nimi, na przykład niestandardowych definicji Azure Policy, szablonów Azure Resource Manager i skryptów konfiguracji żądanego stanu.

Przyznaj lub odmów uprawnień określonym użytkownikom, wbudowanym grupom zabezpieczeń lub grupom zdefiniowanym w usłudze Azure Active Directory (Azure AD), jeśli są zintegrowane z usługą Azure DevOps, lub w usłudze Azure AD, jeśli są zintegrowane z programem TFS.

- [Jak przechowywać kod w Azure DevOps](/azure/devops/repos/git/gitworkflow)

- [Informacje o uprawnieniach i grupach w Azure DevOps](/azure/devops/organizations/security/about-permissions)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Wdrażanie narzędzi do zarządzania konfiguracją dla zasobów platformy Azure

**Wskazówki:** Definiowanie i implementowanie standardowych konfiguracji zabezpieczeń dla zasobów platformy Azure przy użyciu Azure Policy.  Użyj Azure Policy aliasów, aby utworzyć zasady niestandardowe do inspekcji lub wymuszania konfiguracji sieci zasobów platformy Azure. Implementowanie wbudowanych definicji zasad związanych z określonymi Azure Load Balancer zasobów.  Ponadto należy użyć Azure Automation, aby wdrożyć zmiany konfiguracji. do zasobów Azure Load Balancer użytkownika.

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

- [Jak używać aliasów](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure#aliases)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Implementowanie zautomatyzowanego monitorowania konfiguracji dla zasobów platformy Azure

**Wskazówki:** użyj Security Center do przeprowadzania skanowania linii bazowej dla zasobów platformy Azure i Azure Policy alertów i inspekcji konfiguracji zasobów.

- [Jak korygować zalecenia w Azure Security Center](../security-center/security-center-remediate-recommendations.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

## <a name="incident-response"></a>Reagowanie na zdarzenia

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: reagowanie na zdarzenia](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1. Tworzenie przewodnika po reagowaniu na zdarzenia

**Wskazówka**: Utwórz przewodnik odpowiedzi na zdarzenia dla swojej organizacji. Upewnij się, że istnieją zarejestrowane plany reakcji na zdarzenia, które definiują wszystkie role pracowników, a także etapy obsługi zdarzeń/zarządzania od wykrywania do oceny po zdarzeniu. 

- [Jak skonfigurować automatyzacje przepływu pracy w Azure Security Center](../security-center/security-center-planning-and-operations-guide.md) 

- [Wskazówki dotyczące tworzenia własnego procesu reagowania na zdarzenia zabezpieczeń](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/) 

- [Centrum zabezpieczeń firmy Microsoft Anatomy of an Incident (Anatomia zdarzenia)](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/) 

- [Klient może również skorzystać z przewodnika obsługi zdarzeń zabezpieczeń komputera NIST, aby pomóc w tworzeniu własnego planu reagowania na zdarzenia](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2. Tworzenie procedury oceniania i ustalania priorytetów zdarzeń

**Wskazówki:** Security Center przypisuje ważność do każdego alertu, aby ułatwić priorytetyzowanie alertów, które należy zbadać w pierwszej kolejności. 

Ważność jest oparta na tym, jak pewna Security Center znajduje się w znajdowaniu lub analizie użytej do wystawienia alertu, a także na poziomie pewności, że za działaniem, które doprowadziło do tego alertu, znajduje się złośliwe intencje.

Oznaczanie subskrypcji za pomocą tagów i tworzenie systemu nazewnictwa w celu identyfikowania i kategoryzowania zasobów platformy Azure, zwłaszcza tych przetwarzających poufne dane.  

Odpowiedzialność za korygowanie alertów w oparciu o krytyczne znaczenie zasobów platformy Azure i środowisko, w którym wystąpiło zdarzenie, leży po stronie użytkownika.

- [Alerty zabezpieczeń w Centrum zabezpieczeń Azure](../security-center/security-center-alerts-overview.md)

- [Organizowanie zasobów platformy Azure przy użyciu tagów](../azure-resource-manager/management/tag-resources.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="103-test-security-response-procedures"></a>10.3: Testowanie procedur reagowania na zabezpieczenia

**Wskazówki:** Przećwiczyj ćwiczenia w celu testowania możliwości reagowania na zdarzenia w systemach w regularnych odstępach czasu, aby ułatwić ochronę zasobów platformy Azure. Zidentyfikuj słabe punkty i luki, a następnie popraw plan reagowania zgodnie z potrzebami. 

- [Publikacja NIST — przewodnik dotyczący programów testowych, szkoleniowych i szkoleniowych dla planów i możliwości IT](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4. Podaj szczegóły kontaktowe zdarzenia zabezpieczeń i skonfiguruj powiadomienia o alertach dotyczących zdarzeń zabezpieczeń

**Wskazówki:** informacje kontaktowe dotyczące zdarzeń naruszenia zabezpieczeń będą używane przez firmę Microsoft do kontaktowania się z tobą, jeśli użytkownik Centrum zabezpieczeń firmy Microsoft (MSRC) odkryje, że dostęp do danych użytkownika uzyskał nieupoważniony lub niedozwolony podmiot. Przejrzyj zdarzenia po fakcie, aby upewnić się, że problemy zostały rozwiązane. 

- [Jak ustawić kontakt zabezpieczeń usługi Azure Security Center](../security-center/security-center-provide-security-contact-details.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5. Dołączanie alertów zabezpieczeń do systemu reagowania na zdarzenia

**Wskazówki:** eksportowanie alertów Security Center i zaleceń przy użyciu funkcji eksportu ciągłego, aby ułatwić identyfikowanie zagrożeń dla zasobów platformy Azure. 

Użyj funkcji eksportu ciągłego w Security Center, która umożliwia eksportowanie alertów i zaleceń ręcznie lub w ciągły sposób. 

Użyj łącznika Security Center danych, aby przesyłać strumieniowo alerty do Azure Sentinel.

- [Jak skonfigurować eksport ciągły](../security-center/continuous-export.md)

- [Jak przesłać strumieniowo alerty do usługi Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatyzowanie odpowiedzi na alerty zabezpieczeń

**Wskazówki:** Użyj funkcji automatyzacji przepływu pracy w Security Center, aby automatycznie wyzwalać odpowiedzi na alerty zabezpieczeń i zalecenia dotyczące ochrony zasobów platformy Azure.

- [Jak skonfigurować automatyzację przepływu pracy w wprowadzeniu zabezpieczeń](../security-center/workflow-automation.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

## <a name="penetration-tests-and-red-team-exercises"></a>Testy penetracyjne i ćwiczenia typu „red team”

*Aby uzyskać więcej informacji, zobacz [test porównawczy zabezpieczeń platformy Azure: testy penetracyjne i ćwiczenia zespołu czerwonego.](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Przeprowadzaj regularne testy penetrowania zasobów platformy Azure i zapewniaj korygowanie wszystkich krytycznych ustaleń zabezpieczeń

**Wskazówki:** postępuj zgodnie z regułami testowania penetracyjne firmy Microsoft, aby upewnić się, że testy penetracyjne nie naruszały zasad firmy Microsoft. Korzystaj ze strategii firmy Microsoft i wykonywania testów typu „red team” i testów na żywo w zarządzanej przez firmę Microsoft infrastrukturze, usługach i aplikacjach w chmurze. 

- [Reguły testów penetracyjnych zaangażowania](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Testy typu „red team” w chmurze firmy Microsoft](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

## <a name="next-steps"></a>Następne kroki

- Zobacz [Omówienie testu porównawczego zabezpieczeń platformy Azure w wersji 2](/azure/security/benchmarks/overview)
- Dowiedz się więcej o [punktach odniesienia zabezpieczeń platformy Azure](/azure/security/benchmarks/security-baselines-overview)
