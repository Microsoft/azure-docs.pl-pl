---
title: Punkt odniesienia zabezpieczeń platformy Azure dla Virtual Machine Scale Sets
description: Punkt Virtual Machine Scale Sets zabezpieczeń zawiera wskazówki proceduralne i zasoby dotyczące wdrażania zaleceń dotyczących zabezpieczeń określonych w te testach porównawczych zabezpieczeń platformy Azure.
author: msmbaldwin
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 04/08/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 60b1de12f031a55388960a6e3c4e7df00c43e3c8
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107867816"
---
# <a name="azure-security-baseline-for-virtual-machine-scale-sets"></a>Punkt odniesienia zabezpieczeń platformy Azure dla Virtual Machine Scale Sets

Ten punkt odniesienia zabezpieczeń stosuje wskazówki z testu porównawczego zabezpieczeń platformy [Azure w wersji 1.0](../security/benchmarks/overview-v1.md) do Virtual Machine Scale Sets. Test porównawczy zabezpieczeń platformy Azure zawiera zalecenia dotyczące zabezpieczania rozwiązań w  chmurze na platformie Azure. Zawartość jest pogrupowana według kontrolek zabezpieczeń zdefiniowanych przez test porównawczy zabezpieczeń platformy Azure i powiązanych wskazówek dotyczących Virtual Machine Scale Sets.

> [!NOTE]
> **Wykluczono** mechanizmy kontroli, Virtual Machine Scale Sets których odpowiedzialnością jest firma Microsoft. Aby zobaczyć, Virtual Machine Scale Sets mapuje całkowicie na test porównawczy zabezpieczeń platformy Azure, zobacz pełny **[Virtual Machine Scale Sets mapowania punktów odniesienia zabezpieczeń](https://github.com/MicrosoftDocs/SecurityBenchmarks/raw/master/Azure%20Offer%20Security%20Baselines/1.1/virtual-machine-scale-sets-security-baseline-v1.1.xlsx)**.

## <a name="network-security"></a>Bezpieczeństwo sieci

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: bezpieczeństwo sieci](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1. Ochrona zasobów platformy Azure w sieciach wirtualnych

**Wskazówki:** Podczas tworzenia maszyny wirtualnej platformy Azure należy utworzyć sieć wirtualną lub użyć istniejącej sieci wirtualnej i skonfigurować maszynę wirtualną z podsiecią. Upewnij się, że wszystkie wdrożone podsieci mają sieciową grupę zabezpieczeń zastosowaną za pomocą kontroli dostępu do sieci specyficznej dla zaufanych portów i źródeł aplikacji.

Alternatywnie, jeśli masz konkretny przypadek użycia dla scentralizowanej zapory, Azure Firewall można również użyć do spełnienia tych wymagań.

- [Obsługa sieci w kontekście zestawów skalowania maszyn wirtualnych platformy Azure](virtual-machine-scale-sets-networking.md)

- [Jak utworzyć Virtual Network](../virtual-network/quick-create-portal.md)

- [Tworzenie sieciowej grupy zabezpieczeń za pomocą konfiguracji zabezpieczeń](../virtual-network/tutorial-filter-network-traffic.md)

- [Jak wdrożyć i skonfigurować Azure Firewall](../firewall/tutorial-firewall-deploy-portal.md)

**Odpowiedzialność**: Klient

**Azure Security Center:** Test porównawczy zabezpieczeń platformy [Azure](/azure/governance/policy/samples/azure-security-benchmark) to domyślna inicjatywa zasad dla usługi Security Center i stanowi podstawę rekomendacji Security Center [firmy .](/azure/security-center/security-center-recommendations) Definicje Azure Policy związane z tą kontrolką są włączane automatycznie przez Security Center. Alerty związane z tą kontrolą mogą [wymagać Azure Defender](/azure/security-center/azure-defender) planu dla powiązanych usług.

**Azure Policy wbudowane definicje — Microsoft.Compute:**

[!INCLUDE [Resource Policy for Microsoft.Compute 1.1](../../includes/policy/standards/asb/rp-controls/microsoft.compute-1-1.md)]

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2: Monitorowanie i rejestrowanie konfiguracji i ruchu sieci wirtualnych, podsieci i interfejsów sieciowych

**Wskazówki:** skorzystaj z Azure Security Center, aby zidentyfikować i postępować zgodnie z zaleceniami ochrony sieci w celu zabezpieczenia zasobów maszyny wirtualnej platformy Azure na platformie Azure. Włącz dzienniki przepływu sieciowej organizacji zabezpieczeń i wysyłaj dzienniki na konto magazynu w celu inspekcji ruchu dla maszyn wirtualnych pod uwagę nietypowych działań.

- [Jak włączyć dzienniki przepływu sieciowej organizacji zabezpieczeń](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Informacje o zabezpieczeniach sieciowych zapewnianych przez Azure Security Center](../security-center/security-center-network-recommendations.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="13-protect-critical-web-applications"></a>1.3: Ochrona krytycznych aplikacji internetowych

**Wskazówki:** Jeśli używasz zestawu skalowania maszyn wirtualnych do hostowania aplikacji internetowych, użyj sieciowej grupy zabezpieczeń w podsieci zestawu skalowania maszyn wirtualnych, aby ograniczyć dozwolony ruch sieciowy, porty i protokoły. Podczas konfigurowania sieciowych urządzeń sieciowych należy stosować podejście z najmniej uprzywilejowanymi uprawnieniami, aby zezwalać tylko na wymagany ruch do aplikacji.

Możesz również wdrożyć Azure Web Application Firewall (WAF) przed krytycznymi aplikacjami internetowymi w celu dodatkowej inspekcji ruchu przychodzącego. Włącz ustawienie diagnostyczne dla aplikacji internetowej i pozysuj dzienniki do konta magazynu, centrum zdarzeń lub obszaru roboczego usługi Log Analytics.

- [Obsługa sieci w kontekście zestawów skalowania maszyn wirtualnych platformy Azure](virtual-machine-scale-sets-networking.md)

- [Tworzenie bramy aplikacji przy użyciu Web Application Firewall przy użyciu Azure Portal](../web-application-firewall/ag/application-gateway-web-application-firewall-portal.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Odmowa komunikacji ze znanymi złośliwymi adresami IP

**Wskazówki:** włącz standardową ochronę przed rozproszoną odmową usługi (DDoS) w sieciach wirtualnych, aby chronić przed atakami DDoS. Za Azure Security Center zintegrowanej analizy zagrożeń można monitorować komunikację ze znanymi złośliwymi adresami IP.  Skonfiguruj Azure Firewall w każdym z segmentów Virtual Network, z włączoną analizą zagrożeń i skonfigurowaną na "Alert i odmowa" dla złośliwego ruchu sieciowego.

Aby Azure Security Center ograniczyć narażenie Windows Virtual Machines na zatwierdzone adresy IP przez ograniczony czas, możesz użyć dostępu just in time do sieci firmy.  Ponadto użyj funkcji adaptacyjnego Azure Security Center sieciowego, aby zalecić konfiguracje sieciowej sieciowej sieci, które ograniczają porty i źródłowe ip na podstawie rzeczywistego ruchu i analizy zagrożeń.

- [Jak skonfigurować ochronę przed DDoS](../ddos-protection/manage-ddos-protection.md)

- [Jak wdrożyć Azure Firewall](../firewall/tutorial-firewall-deploy-portal.md)

- [Understand Azure Security Center Integrated Threat Intelligence](../security-center/azure-defender.md)

- [Opis Azure Security Center adaptacyjnego wzmacniania zabezpieczeń sieci](../security-center/security-center-adaptive-network-hardening.md)

- [Opis Azure Security Center sieci just in time Access Control](../security-center/security-center-just-in-time.md)

**Odpowiedzialność**: Klient

**Azure Security Center zabezpieczeń:** Test porównawczy zabezpieczeń platformy [Azure](/azure/governance/policy/samples/azure-security-benchmark) jest domyślną inicjatywą zasad dla usługi Security Center i stanowi podstawę rekomendacji Security Center [firmy .](/azure/security-center/security-center-recommendations) Definicje Azure Policy związane z tą kontrolką są włączane automatycznie przez Security Center. Alerty związane z tą kontrolą mogą [wymagać Azure Defender](/azure/security-center/azure-defender) planu dla powiązanych usług.

**Azure Policy wbudowane definicje — Microsoft.Compute:**

[!INCLUDE [Resource Policy for Microsoft.Compute 1.4](../../includes/policy/standards/asb/rp-controls/microsoft.compute-1-4.md)]

### <a name="15-record-network-packets"></a>1.5. Rejestrowanie pakietów sieciowych

**Wskazówki:** Dzienniki przepływu sieciowej organizacji zabezpieczeń można rejestrować na koncie magazynu w celu generowania rekordów przepływu dla usługi Azure Virtual Machines. Podczas badania nietypowych działań można włączyć przechwytywanie pakietów Network Watcher, aby umożliwić przeglądanie ruchu sieciowego pod uwagę pod uwagę nietypowych i nieoczekiwanych działań.

- [How to Enable NSG Flow Logs](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Jak włączyć Network Watcher](../network-watcher/network-watcher-create.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6. Wdrażanie sieciowych systemów wykrywania/zapobiegania włamań (IDS/IPS)

**Wskazówki:** łącząc przechwytywanie pakietów zapewniane przez usługę Network Watcher i narzędzie identyfikatorów IDS typu open source, można wykrywać włamanie do sieci w celu wykrycia szerokiego zakresu zagrożeń. Ponadto możesz wdrażać aplikacje Azure Firewall segmentach usługi Virtual Network zgodnie z potrzebami, z włączoną analizą zagrożeń i skonfigurowaną na "Alert i odmowa" dla złośliwego ruchu sieciowego.

- [Wykrywanie włamań do sieci za Network Watcher i narzędzi typu open source](../network-watcher/network-watcher-intrusion-detection-open-source-tools.md)

- [Jak wdrożyć Azure Firewall](../firewall/tutorial-firewall-deploy-portal.md)

- [Jak skonfigurować alerty za pomocą Azure Firewall](../firewall/threat-intel.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Zarządzanie ruchem do aplikacji internetowych

**Wskazówki:** Jeśli używasz zestawu skalowania maszyn wirtualnych do hostowania aplikacji internetowych, możesz wdrożyć usługę Azure Application Gateway dla aplikacji internetowych z włączonym protokołem HTTPS/SSL dla zaufanych certyfikatów. Dzięki Azure Application Gateway można kierować ruch internetowy aplikacji do określonych zasobów, przypisując odbiorniki do portów, tworząc reguły i dodając zasoby do puli zaplecza, na przykład zestaw skalowania maszyn wirtualnych itp.

- [Jak wdrożyć Application Gateway](../application-gateway/quick-create-portal.md)

- [Jak skonfigurować Application Gateway do korzystania z protokołu HTTPS](../application-gateway/create-ssl-portal.md)

- [Tworzenie zestawu skalowania, który odwołuje się do usługi Application Gateway](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-networking#create-a-scale-set-that-references-an-application-gateway)

- [Opis równoważenia obciążenia warstwy 7 za pomocą bram aplikacji internetowych platformy Azure](../application-gateway/overview.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimalizowanie złożoności i obciążeń administracyjnych reguł zabezpieczeń sieci

**Wskazówki:** Użyj tagów Virtual Network service do definiowania kontroli dostępu do sieci w sieciowych grupach zabezpieczeń lub Azure Firewall skonfigurowanych dla maszyn wirtualnych platformy Azure. Podczas tworzenia reguł zabezpieczeń można użyć tagów usługi zamiast konkretnych adresów IP. Określając nazwę tagu usługi (np. ApiManagement) w odpowiednim polu źródła lub miejsca docelowego reguły, można zezwolić na ruch dla odpowiedniej usługi lub go odmówić. Firma Microsoft zarządza prefiksami adresów obejmującymi tag usługi i automatycznie aktualizuje tag usługi w przypadku zmiany adresów.

- [Opis tagów usługi i korzystanie z tych tagów](../virtual-network/service-tags-overview.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Obsługa standardowych konfiguracji zabezpieczeń dla urządzeń sieciowych

**Wskazówki:** Definiowanie i implementowanie standardowych konfiguracji zabezpieczeń dla usługi Azure Virtual Machine Scale Sets przy użyciu Azure Policy. Usługi Azure Blueprints można również użyć do uproszczenia wdrożeń maszyn wirtualnych platformy Azure na dużą skalę przez pakowanie kluczowych artefaktów środowiska, takich jak szablony Azure Resource Manager, przypisania ról i przypisania Azure Policy, w pojedynczej definicji strategii. Możesz zastosować tę strategia do subskrypcji i włączyć zarządzanie zasobami za pomocą obsługi wersji strategii.

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

- [Dowiedz się więcej o szablonach zestawu skalowania maszyn wirtualnych](virtual-machine-scale-sets-mvss-start.md) 

- [Azure Policy przykłady dotyczące sieci](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#network)

- [Jak utworzyć usługę Azure Blueprint](../governance/blueprints/create-blueprint-portal.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="110-document-traffic-configuration-rules"></a>1.10. Udokumentuj reguły konfiguracji ruchu

**Wskazówki:** tagów można używać dla sieciowych grup zabezpieczeń i innych zasobów związanych z zabezpieczeniami sieci i przepływem ruchu skonfigurowanymi dla maszyn wirtualnych z systemem Windows. W przypadku poszczególnych reguł sieciowych należy użyć pola "Opis", aby określić potrzeby biznesowe i/lub czas trwania dla dowolnych reguł, które zezwalają na ruch do/z sieci.

- [Jak tworzyć tagi i ich używać](../azure-resource-manager/management/tag-resources.md)

- [Jak utworzyć Virtual Network](../virtual-network/quick-create-portal.md)

- [Tworzenie sieciowej grupy zabezpieczeń za pomocą konfiguracji zabezpieczeń](../virtual-network/tutorial-filter-network-traffic.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11. Używanie zautomatyzowanych narzędzi do monitorowania konfiguracji zasobów sieciowych i wykrywania zmian

**Wskazówki:** Użyj dziennika aktywności platformy Azure, aby monitorować zmiany konfiguracji zasobów sieciowych związanych z zestawem skalowania maszyn wirtualnych platformy Azure. Utwórz alerty w Azure Monitor, które będą wyzwalane w przypadku zmiany kluczowych ustawień sieciowych lub zasobów sieciowych.

Użyj Azure Policy, aby zweryfikować (i/lub skorygować) konfiguracje zasobów sieciowych związanych z zestawem skalowania maszyn wirtualnych.

- [Jak wyświetlać i pobierać zdarzenia dziennika aktywności platformy Azure](https://docs.microsoft.com/azure/azure-monitor/essentials/activity-log#view-the-activity-log)

- [Jak tworzyć alerty w Azure Monitor](../azure-monitor/alerts/alerts-activity-log.md)

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy przykłady dotyczące sieci](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#network)

**Odpowiedzialność**: Klient

**Azure Security Center:** Test porównawczy zabezpieczeń platformy [Azure](/azure/governance/policy/samples/azure-security-benchmark) to domyślna inicjatywa zasad dla usługi Security Center i stanowi podstawę rekomendacji Security Center [firmy .](/azure/security-center/security-center-recommendations) Definicje Azure Policy związane z tą kontrolką są włączane automatycznie przez Security Center. Alerty związane z tą kontrolą mogą [wymagać Azure Defender](/azure/security-center/azure-defender) planu dla powiązanych usług.

**Azure Policy wbudowane definicje — Microsoft.Compute:**

[!INCLUDE [Resource Policy for Microsoft.Compute 1.11](../../includes/policy/standards/asb/rp-controls/microsoft.compute-1-11.md)]

## <a name="logging-and-monitoring"></a>Rejestrowanie i monitorowanie

*Aby uzyskać więcej informacji, zobacz [Test porównawczy zabezpieczeń platformy Azure: rejestrowanie i monitorowanie.](../security/benchmarks/security-control-logging-monitoring.md)*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1. Używanie zatwierdzonych źródeł synchronizacji czasu

**Wskazówki:** Firma Microsoft utrzymuje źródła czasu dla zasobów platformy Azure, jednak istnieje możliwość zarządzania ustawieniami synchronizacji czasu dla Virtual Machines.

- [Jak skonfigurować synchronizację czasu dla zasobów obliczeniowych systemu Windows na platformie Azure](../virtual-machines/windows/time-sync.md)

- [Jak skonfigurować synchronizację czasu dla zasobów obliczeniowych systemu Linux na platformie Azure](../virtual-machines/linux/time-sync.md)

**Odpowiedzialność**: Współużytkowane

**Azure Security Center monitorowania:** Brak

### <a name="22-configure-central-security-log-management"></a>2.2. Konfigurowanie centralnego zarządzania dziennikami zabezpieczeń

**Wskazówki:** Dzienniki aktywności mogą służyć do inspekcji operacji i akcji wykonywanych na zasobach zestawu skalowania maszyn wirtualnych. Dziennik aktywności zawiera wszystkie operacje zapisu (PUT, POST, DELETE) dla zasobów z wyjątkiem operacji odczytu (GET). Dzienniki aktywności mogą służyć do znalezienia błędu podczas rozwiązywania problemów lub monitorowania sposobu, w jaki użytkownik w organizacji zmodyfikował zasób.

W celu centralnego zarządzania dziennikami zabezpieczeń można włączyć i włączyć dane dzienników wytwarzanych na podstawie dzienników aktywności platformy Azure lub zasobów maszyny wirtualnej Azure Sentinel lub rozwiązania SIEM innej firmy.

Użyj Azure Security Center, aby zapewnić monitorowanie dziennika zdarzeń zabezpieczeń dla usługi Azure Virtual Machines. Biorąc pod uwagę ilość danych generowanych przez dziennik zdarzeń zabezpieczeń, nie jest ona domyślnie przechowywana. 

Jeśli Organizacja chce zachować dane dziennika zdarzeń zabezpieczeń z maszyny wirtualnej, mogą być przechowywane w obszarze roboczym usługi Log Analytics w żądanej warstwie zbierania danych skonfigurowanej w ramach Azure Security Center.

- [Jak zbierać dzienniki platformy i metryki za pomocą Azure Monitor ](../azure-monitor/essentials/diagnostic-settings.md)

- [Jak do dołączać Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Jak rozpocząć pracę z Azure Monitor integracją rozwiązania SIEM i rozwiązania SIEM innej firmy](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools)

- [Zbieranie danych w usłudze Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection#data-collection-tier) 

- [Jak monitorować maszyny wirtualne na platformie Azure](../azure-monitor/vm/monitor-vm-azure.md)

**Odpowiedzialność**: Klient

**Azure Security Center:** Test porównawczy zabezpieczeń platformy [Azure](/azure/governance/policy/samples/azure-security-benchmark) to domyślna inicjatywa zasad dla usługi Security Center i stanowi podstawę rekomendacji Security Center [firmy .](/azure/security-center/security-center-recommendations) Definicje Azure Policy związane z tą kontrolką są włączane automatycznie przez Security Center. Alerty związane z tą kontrolą mogą [wymagać Azure Defender](/azure/security-center/azure-defender) planu dla powiązanych usług.

**Azure Policy wbudowane definicje — Microsoft.Compute:**

[!INCLUDE [Resource Policy for Microsoft.Compute 2.2](../../includes/policy/standards/asb/rp-controls/microsoft.compute-2-2.md)]

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3. Włączanie rejestrowania inspekcji dla zasobów platformy Azure

**Wskazówki:** Dzienniki aktywności mogą służyć do inspekcji operacji i akcji wykonywanych na zasobach zestawu skalowania maszyn wirtualnych. Dziennik aktywności zawiera wszystkie operacje zapisu (PUT, POST, DELETE) dla zasobów z wyjątkiem operacji odczytu (GET). Dzienniki aktywności mogą służyć do znalezienia błędu podczas rozwiązywania problemów lub monitorowania sposobu, w jaki użytkownik w organizacji zmodyfikował zasób.

Włącz zbieranie danych diagnostycznych systemu operacyjnego gościa, wdrażając rozszerzenie diagnostyczne na maszynie Virtual Machines wirtualnej. Za pomocą rozszerzenia diagnostyki można zbierać dane diagnostyczne, takie jak dzienniki aplikacji lub liczniki wydajności, z maszyny wirtualnej platformy Azure.

Aby uzyskać zaawansowany wgląd w aplikacje i usługi obsługiwane przez zestaw skalowania maszyn wirtualnych platformy Azure, możesz włączyć usługę Azure Monitor dla maszyn wirtualnych Application Insights. Dzięki Application Insights możesz monitorować aplikację i przechwytywać dane telemetryczne, takie jak żądania HTTP, wyjątki itp., aby skorelować problemy między maszynami wirtualnych i aplikacją.

- [Jak zbierać dzienniki i metryki platformy za pomocą Azure Monitor](../azure-monitor/essentials/diagnostic-settings.md)

- [Wyświetlanie i pobieranie zdarzeń dziennika aktywności platformy Azure](https://docs.microsoft.com/azure/azure-monitor/essentials/activity-log#view-the-activity-log)

- [Jak monitorować maszyny wirtualne na platformie Azure](../azure-monitor/vm/monitor-vm-azure.md)

- [Omówienie usługi Application Insights](../azure-monitor/app/app-insights-overview.md)

**Odpowiedzialność**: Klient

**Azure Security Center:** Test porównawczy zabezpieczeń platformy [Azure](/azure/governance/policy/samples/azure-security-benchmark) to domyślna inicjatywa zasad dla usługi Security Center i stanowi podstawę rekomendacji Security Center [firmy .](/azure/security-center/security-center-recommendations) Definicje Azure Policy związane z tą kontrolką są włączane automatycznie przez Security Center. Alerty związane z tą kontrolą mogą [wymagać Azure Defender](/azure/security-center/azure-defender) planu dla powiązanych usług.

**Azure Policy wbudowane definicje — Microsoft.Compute:**

[!INCLUDE [Resource Policy for Microsoft.Compute 2.3](../../includes/policy/standards/asb/rp-controls/microsoft.compute-2-3.md)]

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4. Zbieranie dzienników zabezpieczeń z systemów operacyjnych

**Wskazówki:** użyj Azure Security Center, aby zapewnić monitorowanie dziennika zdarzeń zabezpieczeń dla usługi Azure Virtual Machines. Biorąc pod uwagę ilość danych generowanych przez dziennik zdarzeń zabezpieczeń, nie jest ona domyślnie przechowywana. 

Jeśli Organizacja chce zachować dane dziennika zdarzeń zabezpieczeń z maszyny wirtualnej, mogą być przechowywane w obszarze roboczym usługi Log Analytics w żądanej warstwie zbierania danych skonfigurowanej w ramach Azure Security Center.

- [Zbieranie danych w usłudze Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection#data-collection-tier) 

- [Jak monitorować maszyny wirtualne na platformie Azure](../azure-monitor/vm/monitor-vm-azure.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Test [porównawczy zabezpieczeń](/azure/governance/policy/samples/azure-security-benchmark) platformy Azure jest domyślną inicjatywą zasad dla usługi Security Center i stanowi podstawę Security Center [rekomendacji firmy](/azure/security-center/security-center-recommendations). Definicje Azure Policy związane z tą kontrolką są włączane automatycznie przez Security Center. Alerty związane z tą kontrolą mogą [wymagać Azure Defender](/azure/security-center/azure-defender) planu dla powiązanych usług.

**Azure Policy wbudowane definicje — Microsoft.Compute:**

[!INCLUDE [Resource Policy for Microsoft.Compute 2.4](../../includes/policy/standards/asb/rp-controls/microsoft.compute-2-4.md)]

### <a name="25-configure-security-log-storage-retention"></a>2.5. Konfigurowanie przechowywania magazynu dzienników zabezpieczeń

**Wskazówki:** Upewnij się, że wszystkie konta magazynu lub obszary robocze usługi Log Analytics używane do przechowywania dzienników maszyn wirtualnych mają ustawiony okres przechowywania dzienników zgodnie z przepisami dotyczącymi zgodności w organizacji.

- [Jak monitorować maszyny wirtualne na platformie Azure](../azure-monitor/vm/monitor-vm-azure.md)

- [Jak skonfigurować okres przechowywania obszaru roboczego usługi Log Analytics](../azure-monitor/logs/manage-cost-storage.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="26-monitor-and-review-logs"></a>2.6. Monitorowanie i przeglądanie dzienników

**Wskazówki:** analizowanie i monitorowanie dzienników pod celu zachowania anomalii oraz regularne przeglądanie wyników. Użyj Azure Monitor, aby przeglądać dzienniki i wykonywać zapytania dotyczące danych dziennika.

Alternatywnie możesz włączyć i dołączyć dane do rozwiązania Azure Sentinel lub rozwiązania SIEM innej firmy w celu monitorowania i przeglądania dzienników.

- [Jak do dołączać Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Opis obszaru roboczego usługi Log Analytics](/azure/azure-monitor/logs/get-started-queries)

- [Jak wykonywać zapytania niestandardowe w Azure Monitor](../azure-monitor/logs/log-analytics-tutorial.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7. Włącz alerty dla działań anomalii

**Wskazówki:** użyj Azure Security Center z obszarem roboczym usługi Log Analytics do monitorowania i alertów dotyczących anomalii działań znalezionych w dziennikach zabezpieczeń i zdarzeniach dla usługi Azure Virtual Machines.  

Alternatywnie można włączyć i dołączyć dane do rozwiązania Azure Sentinel lub rozwiązania SIEM innej firmy w celu skonfigurowania alertów dotyczących anomalii.

- [Jak do dołączać Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Jak zarządzać alertami w Azure Security Center](../security-center/security-center-managing-and-responding-alerts.md)

- [Jak rejestrować alerty dotyczące danych dzienników analizy dzienników](../azure-monitor/alerts/tutorial-response.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="28-centralize-anti-malware-logging"></a>2.8. Scentralizowanie rejestrowania w poszukiwaniu złośliwego oprogramowania

**Wskazówki:** Możesz używać ochrony przed złośliwym oprogramowaniem firmy Microsoft na Azure Cloud Services i Virtual Machines i konfigurować maszyny wirtualne z systemem Windows do logowania zdarzeń na koncie usługi Azure Storage. Skonfiguruj obszar roboczy usługi Log Analytics, aby pozyscyć zdarzenia z kont magazynu i tworzyć alerty tam, gdzie jest to konieczne. Postępuj zgodnie z zaleceniami Azure Security Center: "Aplikacje &amp; obliczeniowe".  W przypadku maszyn wirtualnych z systemem Linux potrzebne jest narzędzie innej firmy do wykrywania luk w zabezpieczeniach przed złośliwym oprogramowaniem. 

- [Jak skonfigurować program Microsoft Anti-malware dla Cloud Services i Virtual Machines](../security/fundamentals/antimalware.md)

- [Jak włączyć monitorowanie na poziomie gościa dla Virtual Machines](../cost-management-billing/cloudyn/azure-vm-extended-metrics.md)

- [Instrukcje dotyczące dołączania serwerów z systemem Linux do usługi Azure Security Center](../security-center/quickstart-onboard-machines.md) 

- [Poniższy link zawiera zalecane przez firmę Microsoft wytyczne dotyczące zabezpieczeń, które mogą służyć jako lista kryteriów dla wybranego oprogramowania luki w zabezpieczeniach](../virtual-machines/security-recommendations.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Test [porównawczy zabezpieczeń](/azure/governance/policy/samples/azure-security-benchmark) platformy Azure jest domyślną inicjatywą zasad dla usługi Security Center i stanowi podstawę Security Center [rekomendacji firmy](/azure/security-center/security-center-recommendations). Definicje Azure Policy związane z tą kontrolką są włączane automatycznie przez Security Center. Alerty związane z tą kontrolą mogą [wymagać Azure Defender](/azure/security-center/azure-defender) planu dla powiązanych usług.

**Azure Policy wbudowane definicje — Microsoft.Compute:**

[!INCLUDE [Resource Policy for Microsoft.Compute 2.8](../../includes/policy/standards/asb/rp-controls/microsoft.compute-2-8.md)]

### <a name="29-enable-dns-query-logging"></a>2.9: Włączanie rejestrowania zapytań DNS

**Wskazówki:** implementowanie rozwiązania innej firmy z Azure Marketplace do rejestrowania DNS zgodnie z potrzebami organizacji.

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="210-enable-command-line-audit-logging"></a>2.10: Włączanie rejestrowania inspekcji w wierszu polecenia

**Wskazówki:** ten Azure Security Center monitorowania dziennika zdarzeń zabezpieczeń dla usługi Azure Virtual Machines (VM). Security Center aprowizuje Microsoft Monitoring Agent wszystkich obsługiwanych maszyn wirtualnych platformy Azure i wszystkich nowych, które są tworzone, jeśli włączono automatyczną aprowizowanie, LUB można zainstalować agenta ręcznie.  Agent włącza zdarzenie tworzenia procesu 4688 i pole CommandLine wewnątrz zdarzenia 4688. Nowe procesy utworzone na maszynie wirtualnej są rejestrowane przez usługę EventLog i monitorowane Security Center usług wykrywania danych.

W przypadku maszyn wirtualnych z systemem Linux można ręcznie skonfigurować rejestrowanie konsoli dla węzłów i przechowywać dane przy użyciu plików syslog.  Ponadto użyj obszaru Azure Monitor usługi Log Analytics, aby przeglądać dzienniki i wykonywać zapytania dotyczące danych dziennika systemowego z maszyn wirtualnych platformy Azure.

- [Zbieranie danych w usłudze Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection#data-collection-tier)

- [Jak wykonywać zapytania niestandardowe w Azure Monitor](../azure-monitor/logs/get-started-queries.md)

- [Syslog data sources in Azure Monitor (Źródła danych usługi Syslog w usłudze Azure Monitor)](../azure-monitor/agents/data-sources-syslog.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

## <a name="identity-and-access-control"></a>Tożsamość i kontrola dostępu

*Aby uzyskać więcej informacji, zobacz [Test porównawczy zabezpieczeń platformy Azure: tożsamość i Access Control.](../security/benchmarks/security-control-identity-access-control.md)*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1. Utrzymywanie spisu kont administracyjnych

**Wskazówki:** chociaż Azure Active Directory (Azure AD) to zalecana metoda administrowania dostępem użytkowników, usługa Azure Virtual Machines może mieć konta lokalne. Zarówno konta lokalne, jak i konta domeny powinny być przeglądane i zarządzane, zwykle z minimalnym rozmiarem. Ponadto skorzystaj z usługi Azure Privileged Identity Management dla kont administracyjnych używanych do uzyskiwania dostępu do zasobów maszyn wirtualnych.

- [Informacje dotyczące kont lokalnych](https://docs.microsoft.com/azure/active-directory/devices/assign-local-admin#manage-the-device-administrator-role)

- [Informacje na temat programu Privileged Identity Manager](../active-directory/privileged-identity-management/pim-deployment-plan.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Zmień domyślne hasła tam, gdzie ma to zastosowanie

**Wskazówki:** Zestaw skalowania maszyn wirtualnych platformy Azure Azure Active Directory (Azure AD) nie ma koncepcji haseł domyślnych. Klient odpowiedzialny za aplikacje innych firm i usługi platformy handlowej, które mogą używać haseł domyślnych.

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Użyj dedykowanych kont administracyjnych

**Wskazówki:** Tworzenie standardowych procedur operacyjnych dotyczących używania dedykowanych kont administracyjnych, które mają dostęp do maszyn wirtualnych. Użyj Azure Security Center zarządzania tożsamościami i dostępem, aby monitorować liczbę kont administracyjnych. Wszystkie konta administratorów używane do uzyskiwania dostępu do zasobów maszyny wirtualnej platformy Azure mogą być również zarządzane przez usługę Azure Privileged Identity Management (PIM). Usługa Azure Privileged Identity Management udostępnia kilka opcji, takich jak podniesienie uprawnień Just in Time, wymaganie uwierzytelniania wieloskładnikowego przed założeniem roli i opcje delegowania, dzięki czemu uprawnienia są dostępne tylko dla określonych ram czasowych i wymagają osoby zatwierdzającej.

- [Opis Azure Security Center tożsamości i dostępu](../security-center/security-center-identity-access.md)

- [Informacje na temat programu Privileged Identity Manager](../active-directory/privileged-identity-management/pim-deployment-plan.md)

**Odpowiedzialność**: Klient

**Azure Security Center:** Test porównawczy zabezpieczeń platformy [Azure](/azure/governance/policy/samples/azure-security-benchmark) to domyślna inicjatywa zasad dla usługi Security Center i stanowi podstawę rekomendacji Security Center [firmy .](/azure/security-center/security-center-recommendations) Definicje Azure Policy związane z tą kontrolką są włączane automatycznie przez Security Center. Alerty związane z tą kontrolą mogą [wymagać Azure Defender](/azure/security-center/azure-defender) planu dla powiązanych usług.

**Azure Policy wbudowane definicje — Microsoft.Compute:**

[!INCLUDE [Resource Policy for Microsoft.Compute 3.3](../../includes/policy/standards/asb/rp-controls/microsoft.compute-3-3.md)]

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4: Używanie Azure Active Directory jednokrotnego (SSO)

**Wskazówki:** wszędzie tam, gdzie to możliwe, używaj logowania jednokrotnego Azure Active Directory (Azure AD) zamiast konfigurowania pojedynczych poświadczeń autonomicznych dla usługi. Użyj Azure Security Center tożsamości i zarządzania dostępem.

- [Logowanie pojedyncze do aplikacji w usłudze Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

- [Jak monitorować tożsamość i dostęp w Azure Security Center](../security-center/security-center-identity-access.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Używaj uwierzytelniania wieloskładnikowego dla Azure Active Directory dostępu opartego na danych

**Wskazówki:** włączanie Azure Active Directory wieloskładnikowego (Azure AD) i postępuj zgodnie z Azure Security Center tożsamości i zarządzania dostępem.

- [Jak włączyć uwierzytelnianie wieloskładnikowe na platformie Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Jak monitorować tożsamość i dostęp w Azure Security Center](../security-center/security-center-identity-access.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3.6. Używanie bezpiecznych stacji roboczych zarządzanych przez platformę Azure do wykonywania zadań administracyjnych

**Wskazówki:** Używanie stacji roboczych z dostępem uprzywilejowanym z uwierzytelnianiem wieloskładnikowym skonfigurowanym do logowania się do zasobów platformy Azure i konfigurowania ich.

- [Dowiedz się więcej o stacjach roboczych z dostępem uprzywilejowanym](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Jak włączyć uwierzytelnianie wieloskładnikowe na platformie Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: Rejestrowanie i alerty dotyczące podejrzanych działań z kont administracyjnych

**Wskazówki:** generowanie dzienników i alertów przy użyciu usługi Azure Active Directory (Azure AD) Privileged Identity Management (PIM) w przypadku wystąpienia podejrzanej lub niebezpiecznej aktywności w środowisku. Wykrywanie ryzyka w usłudze Azure AD umożliwia wyświetlanie alertów i raportów dotyczących ryzykownych zachowań użytkowników. Opcjonalnie klient może pozyscywać alerty Azure Security Center wykrywania ryzyka do usługi Azure Monitor skonfigurować niestandardowe alerty/powiadomienia przy użyciu grup akcji.

- [Jak wdrożyć Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [Understanding Azure Security Center risk detections (suspicious activity) (Informacje o wykrywaniu ryzyka związanego z ryzykiem (podejrzane działanie)](../active-directory/identity-protection/overview-identity-protection.md)

- [Jak zintegrować dzienniki aktywności platformy Azure z usługą Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Jak skonfigurować grupy akcji dla niestandardowych alertów i powiadomień](../azure-monitor/alerts/action-groups.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Zarządzanie zasobami platformy Azure tylko z zatwierdzonych lokalizacji

**Wskazówki:** Użyj zasad dostępu warunkowego usługi Azure Active Directory (Azure AD) i nazwanych lokalizacji, aby zezwolić na dostęp tylko z określonych grup logicznych zakresów adresów IP lub krajów/regionów.

- [Jak skonfigurować nazwane lokalizacje na platformie Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="39-use-azure-active-directory"></a>3.9. Użyj Azure Active Directory

**Wskazówki:** Używanie Azure Active Directory (Azure AD) jako centralnego systemu uwierzytelniania i autoryzacji. Usługa Azure AD chroni dane przy użyciu silnego szyfrowania danych magazynowych i podczas przesyłania. Usługa Azure AD dodaje również iniekty, skróty i bezpiecznie przechowuje poświadczenia użytkownika.  Tożsamości zarządzanych można używać do uwierzytelniania w dowolnej usłudze obsługującej uwierzytelnianie usługi Azure AD, w tym Key Vault, bez żadnych poświadczeń w kodzie. Kod uruchomiony na maszynie wirtualnej może używać swojej tożsamości zarządzanej do żądania tokenów dostępu dla usług, które obsługują uwierzytelnianie usługi Azure AD.

- [Jak utworzyć i skonfigurować wystąpienie usługi Azure AD](../active-directory-domain-services/tutorial-create-instance.md)

- [Omówienie tożsamości zarządzanych dla zasobów platformy Azure](../active-directory/managed-identities-azure-resources/overview.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Regularne przeglądanie i uzgadnianie dostępu użytkowników

**Wskazówki:** Azure Active Directory (Azure AD) zawiera dzienniki, które ułatwiają odnajdywanie nieaktualnych kont. Ponadto za pomocą przeglądów dostępu tożsamości usługi Azure AD można efektywnie zarządzać członkostwem w grupach, dostępem do aplikacji dla przedsiębiorstw i przypisań ról. Dostęp użytkownika można regularnie przeglądać, aby upewnić się, że tylko odpowiedni użytkownicy mają dalszy dostęp. W przypadku korzystania z maszyn wirtualnych platformy Azure należy przejrzeć lokalne grupy zabezpieczeń i użytkowników, aby upewnić się, że nie ma nieoczekiwanych kont, które mogłyby naruszyć bezpieczeństwo systemu.

- [Jak używać przeglądów dostępu tożsamości platformy Azure](../active-directory/governance/access-reviews-overview.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Monitorowanie prób uzyskania dostępu do dezaktywowanych poświadczeń

**Wskazówki:** konfigurowanie ustawień diagnostycznych dla usługi Azure Active Directory (Azure AD) w celu wysyłania dzienników inspekcji i dzienników logowania do obszaru roboczego usługi Log Analytics. Ponadto użyj Azure Monitor, aby przeglądać dzienniki i wykonywać zapytania dotyczące danych dzienników z maszyn wirtualnych platformy Azure.

- [Opis obszaru roboczego usługi Log Analytics](../azure-monitor/logs/log-analytics-tutorial.md)

- [Jak zintegrować dzienniki aktywności platformy Azure z usługą Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Jak wykonywać zapytania niestandardowe w Azure Monitor](../azure-monitor/logs/get-started-queries.md)

- [Jak monitorować maszyny wirtualne na platformie Azure](/azure/azure-monitor/vm/monitor-vm-azure)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12: Odchylenie zachowania alertu podczas logowania na koncie

**Wskazówki:** użyj Azure Active Directory usługi Azure AD (Risk and Identity Protection) w celu skonfigurowania automatycznych odpowiedzi na wykryte podejrzane akcje związane z zasobami konta magazynu. Należy włączyć automatyczne odpowiedzi za pośrednictwem Azure Sentinel, aby zaimplementować odpowiedzi zabezpieczeń organizacji.

- [Jak wyświetlić ryzykowne logowania w usłudze Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

- [Jak skonfigurować i włączyć zasady ryzyka usługi Identity Protection](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Jak do dołączać Azure Sentinel](../sentinel/quickstart-onboard.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Zapewnianie firmie Microsoft dostępu do odpowiednich danych klientów podczas scenariuszy pomocy technicznej

**Wskazówki:** W scenariuszach pomocy technicznej, w których firma Microsoft potrzebuje dostępu do danych klientów (na przykład podczas żądania pomocy technicznej), użyj usługi Skrytka klienta dla maszyn wirtualnych platformy Azure, aby przeglądać i zatwierdzać lub odrzucać żądania dostępu do danych klientów.

- [Opis Skrytka klienta](../security/fundamentals/customer-lockbox-overview.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

## <a name="data-protection"></a>Ochrona danych

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: ochrona danych](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1. Utrzymywanie spisu informacji poufnych

**Wskazówki:** używanie tagów do śledzenia maszyn wirtualnych platformy Azure, które przechowują lub przetwarzają informacje poufne.

- [Jak tworzyć tagi i ich używać](../azure-resource-manager/management/tag-resources.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Odizoluj systemy przechowujące lub przetwarzające informacje poufne

**Wskazówki:** Zaim implementuj oddzielne subskrypcje i/lub grupy zarządzania na przykład w środowisku dewelopera, testowania i produkcji. Zasoby powinny być oddzielone siecią wirtualną/podsiecią, odpowiednio oznakowane i zabezpieczone w sieciowej grupie zabezpieczeń lub za pomocą Azure Firewall. Aby Virtual Machines lub przetwarzać poufne dane, należy zaimplementować zasady i procedury, aby je wyłączyć, gdy nie są w użyciu.

- [Jak utworzyć dodatkowe subskrypcje platformy Azure](../cost-management-billing/manage/create-subscription.md)

- [Jak utworzyć Grupy zarządzania](../governance/management-groups/create-management-group-portal.md)

- [Jak tworzyć tagi i ich używać](../azure-resource-manager/management/tag-resources.md)

- [Jak utworzyć Virtual Network](../virtual-network/quick-create-portal.md)

- [Tworzenie sieciowej grupy zabezpieczeń za pomocą konfiguracji zabezpieczeń](../virtual-network/tutorial-filter-network-traffic.md)

- [Jak wdrożyć Azure Firewall](../firewall/tutorial-firewall-deploy-portal.md)

- [Jak skonfigurować alert lub alert i odmówić przy użyciu Azure Firewall](../firewall/threat-intel.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Monitorowanie i blokowanie nieautoryzowanego transferu poufnych informacji

**Wskazówki:** Wdrożenie rozwiązania innych firm w obwodach sieci, które monitoruje nieautoryzowany transfer poufnych informacji i blokuje takie transfery podczas powiadamiania specjalistów ds. zabezpieczeń informacji.

W przypadku podstawowej platformy zarządzanej przez firmę Microsoft firma Microsoft traktuje całą zawartość klienta jako wrażliwą na utratę i narażenie danych klientów. Aby zapewnić bezpieczeństwo danych klientów na platformie Azure, firma Microsoft wdrożyła i utrzymuje pakiet niezawodnych funkcji i kontroli ochrony danych.

- [Informacje na temat ochrony danych klientów na platformie Azure](../security/fundamentals/protection-customer-data.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4. Szyfrowanie wszystkich informacji poufnych podczas przesyłania

**Wskazówki:** Dane podczas przesyłania do Virtual Machines, z i między maszyną wirtualną z systemem Windows są szyfrowane na wiele sposobów, w zależności od charakteru połączenia, na przykład podczas nawiązywania połączenia z maszyną wirtualną w sesji protokołu RDP lub SSH.

Firma Microsoft używa protokołu Transport Layer Security (TLS) do ochrony danych podczas podróży między usługami w chmurze i klientami.

- [Szyfrowanie podczas przesyłania na maszyny wirtualne](https://docs.microsoft.com/azure/security/fundamentals/encryption-overview#in-transit-encryption-in-vms)

**Odpowiedzialność**: Współużytkowane

**Azure Security Center monitorowania:** Brak

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5. Identyfikowanie poufnych danych za pomocą aktywnego narzędzia odnajdywania

**Wskazówki:** Użyj narzędzia do aktywnego odnajdywania innej firmy, aby zidentyfikować wszystkie poufne informacje przechowywane, przetwarzane lub przesyłane przez systemy technologiczne organizacji, w tym te znajdujące się na miejscu lub u zdalnego dostawcy usług, i zaktualizować spis informacji poufnych organizacji.

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6. Kontrola dostępu do zasobów przy użyciu kontroli dostępu na platformie Azure 

**Wskazówki:** Korzystając z kontroli dostępu opartej na rolach (RBAC) na platformie Azure, możesz segregować obowiązki w ramach zespołu i udzielać użytkownikom na Twojej maszynie wirtualnej tylko dostępu potrzebnego do wykonywania zadań. Zamiast nadawać wszystkim użytkownikom nieograniczone uprawnienia na maszynie wirtualnej, możesz zezwolić tylko na niektóre akcje. Kontrolę dostępu dla maszyny wirtualnej można skonfigurować w Azure Portal, przy użyciu interfejsu wiersza polecenia platformy Azure lub Azure PowerShell.

- [Kontrola dostępu na podstawie ról platformy Azure](../role-based-access-control/overview.md)

- [Wbudowane role platformy Azure](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#virtual-machine-contributor)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7. Wymuszanie kontroli dostępu przy użyciu ochrony przed utratą danych opartej na hoście

**Wskazówki:** Wdrożenie narzędzia innej firmy, takiego jak zautomatyzowane oparte na hoście rozwiązanie do ochrony przed utratą danych, w celu wymuszenia kontroli dostępu w celu ograniczenia ryzyka naruszenia zabezpieczeń danych.

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8. Szyfrowanie poufnych informacji w spoczynku

**Wskazówki:** Dyski wirtualne na Virtual Machines (VM) są szyfrowane podczas spoczynku przy użyciu szyfrowania po stronie serwera lub usługi Azure Disk Encryption (ADE). Azure Disk Encryption korzysta z DM-Crypt systemu Linux do szyfrowania dysków zarządzanych przy użyciu kluczy zarządzanych przez klienta na maszynie wirtualnej gościa. Szyfrowanie po stronie serwera za pomocą kluczy zarządzanych przez klienta ulepsza usługę ADE, umożliwiając korzystanie z dowolnego typu systemu operacyjnego i obrazów dla maszyn wirtualnych przez szyfrowanie danych w usłudze Storage.

- [Azure Disk Encryption dla Virtual Machine Scale Sets](disk-encryption-overview.md)

**Odpowiedzialność**: Klient

**Azure Security Center zabezpieczeń:** Test porównawczy zabezpieczeń platformy [Azure](/azure/governance/policy/samples/azure-security-benchmark) jest domyślną inicjatywą zasad dla usługi Security Center i stanowi podstawę rekomendacji Security Center [firmy .](/azure/security-center/security-center-recommendations) Definicje Azure Policy związane z tą kontrolką są włączane automatycznie przez Security Center. Alerty związane z tą kontrolą mogą [wymagać Azure Defender](/azure/security-center/azure-defender) planu dla powiązanych usług.

**Azure Policy wbudowane definicje — Microsoft.Compute:**

[!INCLUDE [Resource Policy for Microsoft.Compute 4.8](../../includes/policy/standards/asb/rp-controls/microsoft.compute-4-8.md)]

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9. Rejestrowanie i alerty dotyczące zmian w krytycznych zasobach platformy Azure

**Wskazówki:** użyj Azure Monitor z dziennika aktywności platformy Azure, aby tworzyć alerty dotyczące zmian w zestawach skalowania maszyn wirtualnych i powiązanych zasobach.  

- [Jak tworzyć alerty dla zdarzeń dziennika aktywności platformy Azure](../azure-monitor/alerts/alerts-activity-log.md)

- [Rejestrowanie usługi Azure Storage Analytics](../storage/common/storage-analytics-logging.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

## <a name="vulnerability-management"></a>Zarządzanie lukami w zabezpieczeniach

*Aby uzyskać więcej informacji, zobacz [Test porównawczy zabezpieczeń platformy Azure: zarządzanie lukami w zabezpieczeniach.](../security/benchmarks/security-control-vulnerability-management.md)*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1. Uruchamianie zautomatyzowanych narzędzi do skanowania luk w zabezpieczeniach

**Wskazówki:** postępuj zgodnie z zaleceniami Azure Security Center na temat przeprowadzania ocen luk w zabezpieczeniach na platformie Azure Virtual Machines.  Użyj zalecanych zabezpieczeń platformy Azure lub rozwiązania innej firmy do przeprowadzania ocen luk w zabezpieczeniach dla maszyn wirtualnych.

- [Jak zaimplementować Azure Security Center oceny luk w zabezpieczeniach](../security-center/deploy-vulnerability-assessment-vm.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Test [porównawczy zabezpieczeń](/azure/governance/policy/samples/azure-security-benchmark) platformy Azure jest domyślną inicjatywą zasad dla usługi Security Center i stanowi podstawę Security Center [rekomendacji firmy](/azure/security-center/security-center-recommendations). Definicje Azure Policy związane z tą kontrolką są włączane automatycznie przez Security Center. Alerty związane z tą kontrolą mogą [wymagać Azure Defender](/azure/security-center/azure-defender) planu dla powiązanych usług.

**Azure Policy wbudowane definicje — Microsoft.Compute:**

[!INCLUDE [Resource Policy for Microsoft.Compute 5.1](../../includes/policy/standards/asb/rp-controls/microsoft.compute-5-1.md)]

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2. Wdrażanie zautomatyzowanego rozwiązania do zarządzania poprawkami systemu operacyjnego

**Wskazówki:** włącz automatyczne uaktualnienia systemu operacyjnego dla obsługiwanych wersji systemu operacyjnego lub obrazów niestandardowych przechowywanych w Shared Image Gallery.

- [Automatyczne uaktualnienia systemu operacyjnego dla zestawów skalowania maszyn wirtualnych na platformie Azure](virtual-machine-scale-sets-automatic-upgrade.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Test [porównawczy zabezpieczeń](/azure/governance/policy/samples/azure-security-benchmark) platformy Azure jest domyślną inicjatywą zasad dla usługi Security Center i stanowi podstawę Security Center [rekomendacji firmy](/azure/security-center/security-center-recommendations). Definicje Azure Policy związane z tą kontrolką są włączane automatycznie przez Security Center. Alerty związane z tą kontrolą mogą [wymagać Azure Defender](/azure/security-center/azure-defender) planu dla powiązanych usług.

**Azure Policy wbudowane definicje — Microsoft.Compute:**

[!INCLUDE [Resource Policy for Microsoft.Compute 5.2](../../includes/policy/standards/asb/rp-controls/microsoft.compute-5-2.md)]

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5.3. Wdrażanie zautomatyzowanego rozwiązania do zarządzania poprawkami dla tytułów oprogramowania innych firm

**Wskazówki:** usługa Azure Virtual Machine Scale Sets może używać automatycznych uaktualnień obrazów systemu operacyjnego. Możesz użyć rozszerzenia Azure Desired State Configuration (DSC) dla bazowych maszyn wirtualnych w zestawie skalowania maszyn wirtualnych. DsC służy do konfigurowania maszyn wirtualnych, gdy są one dostępne w trybie online, tak aby uruchamiały żądane oprogramowanie.

- [Używanie Virtual Machine Scale Sets z rozszerzeniem Azure DSC](virtual-machine-scale-sets-dsc.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: Porównanie skanów luk w zabezpieczeniach typu wstecz

**Wskazówki:** Eksportuj wyniki skanowania w spójnych odstępach czasu i porównaj wyniki, aby sprawdzić, czy luki w zabezpieczeniach zostały skorygowane. W przypadku korzystania z rekomendacji dotyczącej zarządzania lukami w zabezpieczeniach sugerowanej przez Azure Security Center, klient może przesunić się do portalu wybranego rozwiązania, aby wyświetlić historyczne dane skanowania.

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5. Ustalanie priorytetów korygowania wykrytych luk w zabezpieczeniach za pomocą procesu oceny ryzyka

**Wskazówki:** Użyj domyślnych klasyfikacji ryzyka (Secure Score) dostarczonych przez Azure Security Center.

- [Opis Azure Security Center bezpieczeństwa](../security-center/secure-score-security-controls.md)

**Odpowiedzialność**: Klient

**Azure Security Center:** Test porównawczy zabezpieczeń platformy [Azure](/azure/governance/policy/samples/azure-security-benchmark) to domyślna inicjatywa zasad dla usługi Security Center i stanowi podstawę rekomendacji Security Center [firmy .](/azure/security-center/security-center-recommendations) Definicje Azure Policy związane z tą kontrolką są włączane automatycznie przez Security Center. Alerty związane z tą kontrolą mogą [wymagać Azure Defender](/azure/security-center/azure-defender) planu dla powiązanych usług.

**Azure Policy wbudowane definicje — Microsoft.Compute:**

[!INCLUDE [Resource Policy for Microsoft.Compute 5.5](../../includes/policy/standards/asb/rp-controls/microsoft.compute-5-5.md)]

## <a name="inventory-and-asset-management"></a>Zarządzanie magazynem i zasobami

*Aby uzyskać więcej informacji, zobacz [Test porównawczy zabezpieczeń platformy Azure: spis i zarządzanie zasobami.](../security/benchmarks/security-control-inventory-asset-management.md)*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1. Korzystanie z rozwiązania do automatycznego odnajdywania zasobów

**Wskazówki:** Użyj Azure Resource Graph do wykonywania zapytań i odnajdywania wszystkich zasobów (w tym maszyn wirtualnych) w ramach subskrypcji. Upewnij się, że masz odpowiednie uprawnienia do odczytu w dzierżawie i możesz wyliczyć wszystkie subskrypcje platformy Azure, a także zasoby w ramach twoich subskrypcji.

- [Jak tworzyć zapytania za pomocą usługi Azure Graph](../governance/resource-graph/first-query-portal.md)

- [Jak wyświetlić subskrypcje platformy Azure](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-4.8.0&amp;preserve-view=true)

- [Opis kontroli RBAC na platformie Azure](../role-based-access-control/overview.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="62-maintain-asset-metadata"></a>6.2. Obsługa metadanych zasobów

**Wskazówki:** Stosowanie tagów do zasobów platformy Azure z metadanymi w celu logicznego organizowania ich zgodnie z taksonomią.

- [Jak tworzyć tagi i ich używać](../azure-resource-manager/management/tag-resources.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Usuwanie nieautoryzowanych zasobów platformy Azure

**Wskazówki:** używaj tagowania, grup zarządzania i oddzielnych subskrypcji, jeśli jest to konieczne, do organizowania i śledzenia Virtual Machines skalowania i powiązanych zasobów. Regularnie uzgadniaj spis i upewnij się, że nieautoryzowane zasoby są usuwane z subskrypcji w terminowy sposób.

- [Jak utworzyć dodatkowe subskrypcje platformy Azure](../cost-management-billing/manage/create-subscription.md)

- [Jak utworzyć Grupy zarządzania](../governance/management-groups/create-management-group-portal.md)

- [Jak tworzyć tagi i ich używać](../azure-resource-manager/management/tag-resources.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4. Definiowanie i utrzymywanie spisu zatwierdzonych zasobów platformy Azure

**Wskazówki:** Utwórz spis zatwierdzonych zasobów platformy Azure i zatwierdzonego oprogramowania dla zasobów obliczeniowych zgodnie z potrzebami organizacji.

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Monitorowanie niezatwierdzonych zasobów platformy Azure

**Wskazówki:** Użyj zasad platformy Azure, aby wprowadzić ograniczenia dotyczące typu zasobów, które mogą być tworzone w subskrypcjach klientów przy użyciu następujących wbudowanych definicji zasad:

- Niedozwolone typy zasobów

- Dozwolone typy zasobów

Ponadto użyj tego Azure Resource Graph do wykonywania zapytań/odnajdywania zasobów w ramach subskrypcji. Może to pomóc w środowiskach o wysokim poziomie zabezpieczeń, takich jak środowiska z kontami magazynu.

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

- [Jak tworzyć zapytania za pomocą usługi Azure Graph](../governance/resource-graph/first-query-portal.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: Monitorowanie niezatwierdzonych aplikacji w ramach zasobów obliczeniowych

**Wskazówki:** Azure Automation zapewnia pełną kontrolę podczas wdrażania, obsługi i likwidowania obciążeń i zasobów.  Skorzystaj ze spisu maszyn wirtualnych platformy Azure, aby zautomatyzować zbieranie informacji o całym oprogramowaniu na Virtual Machines. Uwaga: nazwa oprogramowania, wersja, wydawca i czas odświeżania są dostępne w Azure Portal. Aby uzyskać dostęp do daty instalacji i innych informacji, klient musi włączyć diagnostykę na poziomie gościa i wprowadzić dzienniki zdarzeń systemu Windows do obszaru roboczego usługi Log Analytics.

Obecnie funkcje adaptacyjnego sterowania aplikacją nie są dostępne dla Virtual Machine Scale Sets.

- [Wprowadzenie do usługi Azure Automation](../automation/automation-intro.md)

- [Jak włączyć spis maszyn wirtualnych platformy Azure](../automation/automation-tutorial-installed-software.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7. Usuwanie niezatwierdzonych zasobów i aplikacji platformy Azure

**Wskazówki:** Azure Automation zapewnia pełną kontrolę podczas wdrażania, obsługi i likwidowania obciążeń i zasobów.  Użytkownik może używać Change Tracking do identyfikowania całego oprogramowania zainstalowanego na Virtual Machines. Możesz zaimplementować własny proces lub użyć Azure Automation State Configuration do usunięcia nieautoryzowanego oprogramowania.

- [Wprowadzenie do usługi Azure Automation](../automation/automation-intro.md)

- [Śledzenie zmian w środowisku za pomocą Change Tracking rozwiązania](../automation/change-tracking/overview.md)

- [Azure Automation State Configuration omówienie](../automation/automation-dsc-overview.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="68-use-only-approved-applications"></a>6.8: Używaj tylko zatwierdzonych aplikacji

**Wskazówki:** Obecnie funkcje adaptacyjnego sterowania aplikacją nie są dostępne dla Virtual Machine Scale Sets. Używanie oprogramowania innych firm do kontrolowania użycia tylko zatwierdzonych aplikacji.

**Odpowiedzialność**: Klient

**Azure Security Center:** Test porównawczy zabezpieczeń platformy [Azure](/azure/governance/policy/samples/azure-security-benchmark) to domyślna inicjatywa zasad dla usługi Security Center i stanowi podstawę rekomendacji Security Center [firmy .](/azure/security-center/security-center-recommendations) Definicje Azure Policy związane z tą kontrolką są włączane automatycznie przez Security Center. Alerty związane z tą kontrolą mogą [wymagać Azure Defender](/azure/security-center/azure-defender) planu dla powiązanych usług.

**Azure Policy wbudowane definicje — Microsoft.Compute:**

[!INCLUDE [Resource Policy for Microsoft.Compute 6.8](../../includes/policy/standards/asb/rp-controls/microsoft.compute-6-8.md)]

### <a name="69-use-only-approved-azure-services"></a>6.9. Używaj tylko zatwierdzonych usług platformy Azure

**Wskazówki:** Użyj zasad platformy Azure, aby wprowadzić ograniczenia dotyczące typu zasobów, które mogą być tworzone w subskrypcjach klientów przy użyciu następujących wbudowanych definicji zasad:
- Niedozwolone typy zasobów
- Dozwolone typy zasobów

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

- [Jak odmówić określonego typu zasobu za pomocą Azure Policy](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Test [porównawczy zabezpieczeń](/azure/governance/policy/samples/azure-security-benchmark) platformy Azure jest domyślną inicjatywą zasad dla usługi Security Center i stanowi podstawę Security Center [rekomendacji firmy](/azure/security-center/security-center-recommendations). Definicje Azure Policy związane z tą kontrolką są włączane automatycznie przez Security Center. Alerty związane z tą kontrolą mogą [wymagać Azure Defender](/azure/security-center/azure-defender) planu dla powiązanych usług.

**Azure Policy wbudowane definicje — Microsoft.Compute:**

[!INCLUDE [Resource Policy for Microsoft.Compute 6.9](../../includes/policy/standards/asb/rp-controls/microsoft.compute-6-9.md)]

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10. Utrzymywanie spisu zatwierdzonych tytułów oprogramowania

**Wskazówki:** Obecnie funkcje adaptacyjnego sterowania aplikacją nie są dostępne dla Virtual Machine Scale Sets. Zaim implementuj rozwiązanie innej firmy, jeśli nie spełnia to wymagań organizacji.

- [Jak używać funkcji adaptacyjnego sterowania Azure Security Center](../security-center/security-center-adaptive-application.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Test [porównawczy zabezpieczeń](/azure/governance/policy/samples/azure-security-benchmark) platformy Azure jest domyślną inicjatywą zasad dla usługi Security Center i stanowi podstawę Security Center [rekomendacji firmy](/azure/security-center/security-center-recommendations). Definicje Azure Policy związane z tą kontrolką są włączane automatycznie przez Security Center. Alerty związane z tą kontrolą mogą [wymagać Azure Defender](/azure/security-center/azure-defender) planu dla powiązanych usług.

**Azure Policy wbudowane definicje — Microsoft.Compute:**

[!INCLUDE [Resource Policy for Microsoft.Compute 6.10](../../includes/policy/standards/asb/rp-controls/microsoft.compute-6-10.md)]

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Ograniczanie możliwości interakcji użytkowników z Azure Resource Manager

**Wskazówki:** Użyj dostępu warunkowego platformy Azure, aby ograniczyć użytkownikom możliwość interakcji z usługą Azure Resource Manager, konfigurując opcję "Blokuj dostęp" dla aplikacji "Microsoft Azure Management".

- [Jak skonfigurować dostęp warunkowy, aby zablokować dostęp do Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: Ogranicz użytkownikom możliwość wykonywania skryptów w ramach zasobów obliczeniowych

**Wskazówki:** W zależności od typu skryptów można użyć konfiguracji specyficznych dla systemu operacyjnego lub zasobów innych firm, aby ograniczyć użytkownikom możliwość wykonywania skryptów w ramach zasobów obliczeniowych platformy Azure.

- [Jak kontrolować wykonywanie skryptów programu PowerShell w środowiskach windows](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-7&amp;preserve-view=true)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Fizyczne lub logiczne rozdzielenie aplikacji wysokiego ryzyka

**Wskazówki:** Aplikacje o wysokim ryzyku wdrożone w środowisku platformy Azure mogą być izolowane przy użyciu sieci wirtualnej, podsieci, subskrypcji, grup zarządzania itp. i wystarczająco zabezpieczone za pomocą sieciowej grupy zabezpieczeń (Azure Firewall, Web Application Firewall) lub sieciowej grupy zabezpieczeń. 

- [Sieci wirtualne i maszyny wirtualne na platformie Azure](/azure/virtual-machines/windows/network-overview)

- [Azure Firewall omówienie](../firewall/overview.md)

- [Web Application Firewall omówienie](../web-application-firewall/overview.md)

- [Omówienie zabezpieczeń sieci](../virtual-network/network-security-groups-overview.md)

- [Omówienie usługi Azure Virtual Network](../virtual-network/virtual-networks-overview.md)

- [Organizowanie zasobów przy użyciu grup zarządzania platformy Azure](../governance/management-groups/overview.md)

- [Przewodnik po decyzjach związanych z subskrypcjami](/azure/cloud-adoption-framework/decision-guides/subscriptions/)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

## <a name="secure-configuration"></a>Bezpieczna konfiguracja

*Aby uzyskać więcej informacji, zobacz [Test porównawczy zabezpieczeń platformy Azure: bezpieczna konfiguracja.](../security/benchmarks/security-control-secure-configuration.md)*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1. Ustanów bezpieczne konfiguracje dla wszystkich zasobów platformy Azure

**Wskazówki:** Użyj Azure Policy lub Azure Security Center, aby zachować konfiguracje zabezpieczeń dla wszystkich zasobów platformy Azure. Ponadto Azure Resource Manager wyeksportować szablon w formie JavaScript Object Notation (JSON), który należy przejrzeć, aby upewnić się, że konfiguracje spełniają/przekraczają wymagania dotyczące zabezpieczeń firmy.

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

- [Informacje na temat pobierania szablonu maszyny wirtualnej](/previous-versions/azure/virtual-machines/windows/download-template)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="72-establish-secure-operating-system-configurations"></a>7.2. Ustanawianie bezpiecznych konfiguracji systemu operacyjnego

**Wskazówki:** Aby zachować konfiguracje zabezpieczeń Azure Security Center wszystkich zasobów obliczeniowych, użyj rekomendacji Virtual Machines Korygowanie luk w zabezpieczeniach w konfiguracjach zabezpieczeń na komputerze.

- [Jak monitorować Azure Security Center zalecenia](../security-center/security-center-recommendations.md)

- [Jak skorygować Azure Security Center zalecenia](../security-center/security-center-remediate-recommendations.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Obsługa bezpiecznych konfiguracji zasobów platformy Azure

**Wskazówki:** Użyj szablonów Azure Resource Manager usługi Azure Policies, aby bezpiecznie skonfigurować zasoby platformy Azure skojarzone z zestawami Virtual Machines Scale Sets.  Azure Resource Manager są plikami opartymi na danych JSON używanymi do wdrażania maszyny wirtualnej wraz z zasobami platformy Azure, a szablon niestandardowy musi być zachowany.  Firma Microsoft przeprowadza konserwację szablonów podstawowych.  Użyj zasad platformy Azure [odmowa] i [wdaj, jeśli nie istnieje] w celu wymuszenia bezpiecznych ustawień w zasobach platformy Azure.

- [Informacje na temat tworzenia Azure Resource Manager szablonów](../virtual-machines/windows/ps-template.md)

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

- [Understanding Azure Policy Effects](../governance/policy/concepts/effects.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Obsługa bezpiecznych konfiguracji systemu operacyjnego

**Wskazówki:** Istnieje kilka opcji utrzymania bezpiecznej konfiguracji usługi Azure Virtual Machines (VM) dla wdrożenia:

1. Azure Resource Manager szablonów: są to pliki oparte na danych JSON używane do wdrażania maszyny wirtualnej z Azure Portal, a szablon niestandardowy musi zostać zachowany. Firma Microsoft przeprowadza konserwację szablonów podstawowych.

2. Niestandardowy wirtualny dysk twardy (VHD): w pewnych okolicznościach może być wymagane, aby niestandardowe pliki VHD były używane, na przykład podczas pracy ze złożonymi środowiskami, które nie mogą być zarządzane za pomocą innych środków.

3. Azure Automation State Configuration: Po wdrożeniu podstawowego systemu operacyjnego można go użyć w celu bardziej szczegółowego kontrolowania ustawień i wymuszania za pomocą struktury automatyzacji.

W przypadku większości scenariuszy podstawowe szablony maszyn wirtualnych firmy Microsoft w połączeniu z Azure Automation Desired State Configuration mogą pomóc w spełnianiu i spełnianiu wymagań dotyczących zabezpieczeń.

- [Informacje na temat pobierania szablonu maszyny wirtualnej](/previous-versions/azure/virtual-machines/windows/download-template)

- [Informacje na temat tworzenia szablonów arm](../virtual-machines/windows/ps-template.md)

- [Jak przekazać niestandardowy wirtualny dysk twardy maszyny wirtualnej na platformę Azure](../virtual-machines/windows/upload-generalized-managed.md)

**Odpowiedzialność**: Współużytkowane

**Azure Security Center:** Test porównawczy zabezpieczeń platformy [Azure](/azure/governance/policy/samples/azure-security-benchmark) to domyślna inicjatywa zasad dla usługi Security Center i stanowi podstawę rekomendacji Security Center [firmy .](/azure/security-center/security-center-recommendations) Definicje Azure Policy związane z tą kontrolką są włączane automatycznie przez Security Center. Alerty związane z tą kontrolą mogą [wymagać Azure Defender](/azure/security-center/azure-defender) planu dla powiązanych usług.

**Azure Policy wbudowane definicje — Microsoft.Compute:**

[!INCLUDE [Resource Policy for Microsoft.Compute 7.4](../../includes/policy/standards/asb/rp-controls/microsoft.compute-7-4.md)]

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Bezpieczne przechowywanie konfiguracji zasobów platformy Azure

**Wskazówki:** Użyj Azure DevOps, aby bezpiecznie przechowywać kod i zarządzać jego kodem, na przykład niestandardowymi zasadami platformy Azure, Azure Resource Manager szablonami, skryptami Desired State Configuration itp.  Aby uzyskać dostęp do zasobów, Azure DevOps, takich jak kod, kompilacje i śledzenie pracy, musisz mieć uprawnienia do tych określonych zasobów. Większość uprawnień jest udzielana za pośrednictwem wbudowanych grup zabezpieczeń, zgodnie z opisem w tece Uprawnienia i dostęp. Możesz udzielić lub odmówić uprawnień określonym użytkownikom, wbudowanym grupom zabezpieczeń lub grupom zdefiniowanym w usłudze Azure Active Directory (Azure AD), jeśli są zintegrowane z usługą Azure DevOps, lub usłudze Active Directory, jeśli są zintegrowane z programem TFS.

- [Jak przechowywać kod w Azure DevOps](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops&amp;preserve-view=true)

- [Informacje o uprawnieniach i grupach w Azure DevOps](/azure/devops/organizations/security/about-permissions)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Bezpieczne przechowywanie niestandardowych obrazów systemu operacyjnego

**Wskazówki:** W przypadku korzystania z obrazów niestandardowych (np. wirtualnego dysku twardego) użyj kontroli dostępu opartej na rolach platformy Azure, aby upewnić się, że tylko autoryzowani użytkownicy mogą uzyskać dostęp do obrazów.  

- [Opis kontroli RBAC na platformie Azure](../role-based-access-control/rbac-and-directory-admin-roles.md)

- [Jak skonfigurować kontrolę RBAC na platformie Azure](../role-based-access-control/quickstart-assign-role-user-portal.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7. Wdrażanie narzędzi do zarządzania konfiguracją dla zasobów platformy Azure

**Wskazówki:** Wykorzystaj Azure Policy do alertów, inspekcji i wymuszania konfiguracji systemu dla maszyn wirtualnych. Ponadto opracuj proces i potok do zarządzania wyjątkami zasad.

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8. Wdrażanie narzędzi do zarządzania konfiguracją dla systemów operacyjnych

**Wskazówki:** Azure Automation State Configuration to usługa zarządzania konfiguracją dla węzłów Desired State Configuration (DSC) w dowolnym chmurze lub lokalnym centrum danych. Umożliwia ona skalowalność na tysiącach maszyn szybko i łatwo z centralnej, bezpiecznej lokalizacji. Maszyny można łatwo dołączać, przypisywać do nich konfiguracje deklaratywne i wyświetlać raporty przedstawiające zgodność poszczególnych maszyn z określonym żądanym stanem. 

- [Dołączanie maszyn do zarządzania przez Azure Automation State Configuration](../automation/automation-dsc-onboarding.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Implementowanie zautomatyzowanego monitorowania konfiguracji dla zasobów platformy Azure

**Wskazówki:** korzystanie Azure Security Center do przeprowadzania skanowania linii bazowej dla maszyn wirtualnych platformy Azure.  Dodatkowe metody automatycznej konfiguracji obejmują używanie Azure Automation State Configuration.

- [Jak korygować zalecenia w Azure Security Center](../security-center/security-center-remediate-recommendations.md)

- [Wprowadzenie do Azure Automation State Configuration](../automation/automation-dsc-getting-started.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Implementowanie zautomatyzowanego monitorowania konfiguracji dla systemów operacyjnych

**Wskazówki:** Azure Automation State Configuration to usługa zarządzania konfiguracją dla węzłów Desired State Configuration (DSC) w dowolnym chmurze lub lokalnym centrum danych. Umożliwia ona skalowalność na tysiącach maszyn szybko i łatwo z centralnej, bezpiecznej lokalizacji. Maszyny można łatwo dołączać, przypisywać do nich konfiguracje deklaratywne i wyświetlać raporty przedstawiające zgodność poszczególnych maszyn z określonym żądanym stanem.

- [Dołączanie maszyn do zarządzania przez Azure Automation State Configuration](../automation/automation-dsc-onboarding.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Test [porównawczy zabezpieczeń](/azure/governance/policy/samples/azure-security-benchmark) platformy Azure jest domyślną inicjatywą zasad dla usługi Security Center i stanowi podstawę Security Center [rekomendacji firmy](/azure/security-center/security-center-recommendations). Definicje Azure Policy związane z tą kontrolką są włączane automatycznie przez Security Center. Alerty związane z tą kontrolą mogą [wymagać Azure Defender](/azure/security-center/azure-defender) planu dla powiązanych usług.

**Azure Policy wbudowane definicje — Microsoft.Compute:**

[!INCLUDE [Resource Policy for Microsoft.Compute 7.10](../../includes/policy/standards/asb/rp-controls/microsoft.compute-7-10.md)]

### <a name="711-manage-azure-secrets-securely"></a>7.11: Bezpieczne zarządzanie wpisami tajnymi platformy Azure

**Wskazówki:** używanie tożsamości usługi zarządzanej w połączeniu z Azure Key Vault, aby uprościć i zabezpieczyć zarządzanie kluczami tajnymi dla aplikacji w chmurze.

- [Jak zintegrować usługę z tożsamościami zarządzanymi platformy Azure](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

- [Jak utworzyć Key Vault](../key-vault/general/quick-create-portal.md)

- [Jak uwierzytelnić się w Key Vault](../key-vault/general/authentication.md)

- [Jak przypisać zasady Key Vault dostępu](../key-vault/general/assign-access-policy-portal.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Bezpieczne i automatyczne zarządzanie tożsamościami

**Wskazówki:** korzystanie z tożsamości zarządzanych w celu zapewnienia usługom platformy Azure automatycznie zarządzanej tożsamości w usłudze Azure Active Directory (Azure AD). Tożsamości zarządzane umożliwiają uwierzytelnianie w dowolnej usłudze obsługującej uwierzytelnianie usługi Azure AD, w tym Key Vault, bez żadnych poświadczeń w kodzie.

- [Jak skonfigurować tożsamości zarządzane](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Wyeliminuj niezamierzone narażenie poświadczeń

**Wskazówki:** implementowanie skanera poświadczeń w celu identyfikowania poświadczeń w kodzie. Skaner poświadczeń ułatwia również przenoszenie odnalezionych poświadczeń do bezpieczniejszych lokalizacji, takich jak usługa Azure Key Vault.

- [Jak skonfigurować skaner poświadczeń](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

## <a name="malware-defense"></a>Ochrona przed złośliwym oprogramowaniem

*Aby uzyskać więcej informacji, zobacz [Test porównawczy zabezpieczeń platformy Azure: Malware Defense.](../security/benchmarks/security-control-malware-defense.md)*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1. Używanie centralnie zarządzanego oprogramowania chroniącego przed złośliwym kodem

**Wskazówki:** używaj Microsoft Antimalware maszyn wirtualnych platformy Azure z systemem Windows, aby stale monitorować i chronić zasoby.  Potrzebne będzie narzędzie innej firmy do ochrony przed złośliwym oprogramowaniem na maszynie wirtualnej z systemem Linux na platformie Azure. 

- [Jak skonfigurować Microsoft Antimalware dla Cloud Services i Virtual Machines](../security/fundamentals/antimalware.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Test [porównawczy zabezpieczeń](/azure/governance/policy/samples/azure-security-benchmark) platformy Azure jest domyślną inicjatywą zasad dla usługi Security Center i stanowi podstawę Security Center [rekomendacji firmy](/azure/security-center/security-center-recommendations). Definicje Azure Policy związane z tą kontrolką są włączane automatycznie przez Security Center. Alerty związane z tą kontrolą mogą [wymagać Azure Defender](/azure/security-center/azure-defender) planu dla powiązanych usług.

**Azure Policy wbudowane definicje — Microsoft.Compute:**

[!INCLUDE [Resource Policy for Microsoft.Compute 8.1](../../includes/policy/standards/asb/rp-controls/microsoft.compute-8-1.md)]

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3. Upewnij się, że oprogramowanie chroniące przed złośliwym oprogramowaniem i podpisy zostały zaktualizowane

**Wskazówki:** po wdrożeniu dla maszyn wirtualnych z systemem Windows usługa Microsoft Antimalware dla platformy Azure domyślnie automatycznie zainstaluje najnowsze aktualizacje podpisu, platformy i aparatu. Postępuj zgodnie z zaleceniami Azure Security Center: "Compute Apps" (Aplikacje obliczeniowe), aby upewnić się, że wszystkie punkty końcowe są aktualne przy użyciu &amp; najnowszych sygnatur. System operacyjny Windows można dodatkowo chronić za pomocą dodatkowych zabezpieczeń, aby ograniczyć ryzyko ataków opartych na oprogramowaniu antywirusowym lub złośliwym oprogramowaniu za pomocą usługi Zaawansowana ochrona przed zagrożeniami w usłudze Microsoft Defender, która integruje się z Azure Security Center.

Potrzebne będzie narzędzie innej firmy do ochrony przed złośliwym oprogramowaniem na maszynie wirtualnej z systemem Linux na platformie Azure. 

- [Jak wdrożyć Microsoft Antimalware dla Azure Cloud Services i Virtual Machines](../security/fundamentals/antimalware.md)

- [Zaawansowana ochrona przed zagrożeniami w usłudze Microsoft Defender](/windows/security/threat-protection/microsoft-defender-atp/onboard-configure) 

- [Jak skonfigurować Microsoft Antimalware dla Cloud Services i Virtual Machines](../virtual-machines/security-recommendations.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Test [porównawczy zabezpieczeń](/azure/governance/policy/samples/azure-security-benchmark) platformy Azure jest domyślną inicjatywą zasad dla usługi Security Center i stanowi podstawę Security Center [rekomendacji firmy](/azure/security-center/security-center-recommendations). Definicje Azure Policy związane z tą kontrolką są włączane automatycznie przez Security Center. Alerty związane z tą kontrolą mogą [wymagać Azure Defender](/azure/security-center/azure-defender) planu dla powiązanych usług.

**Azure Policy wbudowane definicje — Microsoft.Compute:**

[!INCLUDE [Resource Policy for Microsoft.Compute 8.3](../../includes/policy/standards/asb/rp-controls/microsoft.compute-8-3.md)]

## <a name="data-recovery"></a>Odzyskiwanie danych

*Aby uzyskać więcej informacji, zobacz [Test porównawczy zabezpieczeń platformy Azure: odzyskiwanie danych.](../security/benchmarks/security-control-data-recovery.md)*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Zapewnianie regularnych automatycznych kopii zapasowej

**Wskazówki:** tworzenie migawki wystąpienia zestawu skalowania maszyn wirtualnych platformy Azure lub dysku zarządzanego dołączonego do wystąpienia przy użyciu programu PowerShell lub interfejsów API REST.  Skryptów kopii Azure Automation można również używać do wykonywania skryptów kopii zapasowych w regularnych odstępach czasu.

- [Jak utworzyć migawkę wystąpienia zestawu skalowania maszyn wirtualnych i dysku zarządzanego](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-faq#how-do-i-take-a-snapshot-of-a-virtual-machine-scale-set-instance) 

- [Wprowadzenie do Azure Automation](../automation/automation-intro.md)

**Odpowiedzialność**: Klient

**Azure Security Center:** Test porównawczy zabezpieczeń platformy [Azure](/azure/governance/policy/samples/azure-security-benchmark) to domyślna inicjatywa zasad dla usługi Security Center i stanowi podstawę rekomendacji Security Center [firmy .](/azure/security-center/security-center-recommendations) Definicje Azure Policy związane z tą kontrolką są włączane automatycznie przez Security Center. Alerty związane z tą kontrolą mogą [wymagać Azure Defender](/azure/security-center/azure-defender) planu dla powiązanych usług.

**Azure Policy wbudowane definicje — Microsoft.Compute:**

[!INCLUDE [Resource Policy for Microsoft.Compute 9.1](../../includes/policy/standards/asb/rp-controls/microsoft.compute-9-1.md)]

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2. Wykonywanie pełnych kopii zapasowych systemu i tworzenie kopii zapasowych dowolnych kluczy zarządzanych przez klienta

**Wskazówki:** Tworzenie migawek maszyn wirtualnych platformy Azure lub dysków zarządzanych dołączonych do tych wystąpień przy użyciu programu PowerShell lub interfejsów API REST. Kopii zapasowej wszystkich kluczy zarządzanych przez klienta w Azure Key Vault.

Włącz Azure Backup i docelowe maszyny Virtual Machines Azure, a także żądaną częstotliwość i okresy przechowywania. Obejmuje to pełną kopię zapasową stanu systemu. Jeśli używasz usługi Azure Disk Encryption, kopia zapasowa maszyny wirtualnej platformy Azure automatycznie obsługuje tworzenie kopii zapasowych kluczy zarządzanych przez klienta.

- [Tworzenie kopii zapasowej na maszynach wirtualnych platformy Azure, które używają szyfrowania](../backup/backup-azure-vms-encryption.md)

- [Omówienie tworzenia kopii zapasowej maszyny wirtualnej platformy Azure](../backup/backup-azure-vms-introduction.md)

- [Jak utworzyć migawkę wystąpienia zestawu skalowania maszyn wirtualnych i dysku zarządzanego](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-faq#how-do-i-take-a-snapshot-of-a-virtual-machine-scale-set-instance)

- [Jak utworzyć kopię zapasową kluczy magazynu kluczy na platformie Azure](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey?view=azps-4.8.0&amp;preserve-view=true)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Test [porównawczy zabezpieczeń](/azure/governance/policy/samples/azure-security-benchmark) platformy Azure jest domyślną inicjatywą zasad dla usługi Security Center i stanowi podstawę Security Center [rekomendacji firmy](/azure/security-center/security-center-recommendations). Definicje Azure Policy związane z tą kontrolką są włączane automatycznie przez Security Center. Alerty związane z tą kontrolą mogą [wymagać Azure Defender](/azure/security-center/azure-defender) planu dla powiązanych usług.

**Azure Policy wbudowane definicje — Microsoft.Compute:**

[!INCLUDE [Resource Policy for Microsoft.Compute 9.2](../../includes/policy/standards/asb/rp-controls/microsoft.compute-9-2.md)]

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Zweryfikuj wszystkie kopie zapasowe, w tym klucze zarządzane przez klienta

**Wskazówki:** Zapewnianie możliwości okresowego przywracania danych dysku zarządzanego w Azure Backup. W razie potrzeby przetestuj przywracanie zawartości do izolowanej sieci wirtualnej lub subskrypcji. Klient może przetestować przywracanie kopii zapasowej kluczy zarządzanych przez klienta.

Jeśli używasz usługi Azure Disk Encryption, możesz przywrócić zestawy skalowania maszyn wirtualnych przy użyciu kluczy szyfrowania dysków. Korzystając z szyfrowania dysków, możesz przywrócić maszynę wirtualną platformy Azure przy użyciu kluczy szyfrowania dysków.

- [Tworzenie kopii zapasowych na maszynach wirtualnych platformy Azure, które używają szyfrowania](../backup/backup-azure-vms-encryption.md)

- [Przywracanie dysku i tworzenie odzyskanej maszyny wirtualnej na platformie Azure](../backup/tutorial-restore-disk.md)

- [Jak przywrócić klucze magazynu kluczy na platformie Azure](https://docs.microsoft.com/powershell/module/az.keyvault/restore-azkeyvaultkey?view=azps-4.8.0&amp;preserve-view=true)

- [Jak włączyć szyfrowanie dysków dla usługi Azure Virtual Machine Scale Sets](disk-encryption-overview.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4. Zapewnianie ochrony kopii zapasowych i kluczy zarządzanych przez klienta

**Wskazówki:** Włączanie ochrony przed usuwaniem dysku zarządzanego przy użyciu blokad. Włącz Soft-Delete i przeczyszczanie w programie Key Vault, aby chronić klucze przed przypadkowym lub złośliwym usunięciem.  

- [Blokowanie zasobów w celu uniemożliwienia nieoczekiwanych zmian](../azure-resource-manager/management/lock-resources.md)

- [Azure Key Vault omówienie usuwania nie soft-delete i ochrony przed przeczyszczaniem](../key-vault/general/soft-delete-overview.md)

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

**Wskazówki:** Security Center przypisać ważność do każdego alertu, aby ułatwić priorytetyzowanie alertów, które należy zbadać w pierwszej kolejności. Ważność jest oparta na tym, jak pewna Security Center znajduje się w znajdowaniu lub metryki użytej do wystawienia alertu, a także na poziomie ufności, że za działaniem, które doprowadziło do tego alertu, istnieje złośliwe intencje. 

Ponadto wyraźnie oznaczaj subskrypcje (na przykład production, non-prod) przy użyciu tagów i utwórz system nazewnictwa, aby jasno identyfikować i kategoryzować zasoby platformy Azure, zwłaszcza te, które przetwarzają poufne dane.  Odpowiedzialność za korygowanie alertów w oparciu o krytyczne znaczenie zasobów platformy Azure i środowisko, w którym wystąpiło zdarzenie, leży po stronie użytkownika.

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

**Wskazówki:** informacje kontaktowe dotyczące zdarzeń naruszenia zabezpieczeń będą używane przez firmę Microsoft do kontaktowania się z Tobą, jeśli użytkownik Centrum zabezpieczeń firmy Microsoft (MSRC) odkryje, że do Twoich danych dostęp uzyskał nieupoważniony lub niedozwolony podmiot. Przejrzyj zdarzenia po fakcie, aby upewnić się, że problemy zostały rozwiązane.

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

**Wskazówki:** Użyj funkcji automatyzacji przepływu pracy w usłudze Azure Security Center, aby automatycznie wyzwalać odpowiedzi za pośrednictwem Logic Apps alertów zabezpieczeń i zaleceń dotyczących ochrony zasobów platformy Azure. 

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

- Zobacz [Omówienie testu porównawczego zabezpieczeń platformy Azure w wersji 2](/azure/security/benchmarks/overview)
- Dowiedz się więcej o [punktach odniesienia zabezpieczeń platformy Azure](/azure/security/benchmarks/security-baselines-overview)
