---
title: Podstawa zabezpieczeń platformy Azure dla Virtual Machine Scale Sets
description: Linia bazowa zabezpieczeń Virtual Machine Scale Sets zawiera wskazówki i zasoby dotyczące procedur związanych z wdrażaniem zaleceń dotyczących zabezpieczeń określonych w teście zabezpieczeń platformy Azure.
author: msmbaldwin
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 07/22/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: d55e53ba455a4b91b4f57ea08b250320a5467c2b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89398448"
---
# <a name="azure-security-baseline-for-virtual-machine-scale-sets"></a>Podstawa zabezpieczeń platformy Azure dla Virtual Machine Scale Sets

Podstawą zabezpieczeń platformy Azure dla Virtual Machine Scale Sets są zalecenia, które pomogą ulepszyć stan bezpieczeństwa wdrożenia.

Punkt odniesienia dla tej usługi jest rysowany w [wersji 1,0 usługi Azure Security test](https://docs.microsoft.com/azure/security/benchmarks/overview), która zawiera zalecenia dotyczące sposobu zabezpieczania rozwiązań w chmurze na platformie Azure z naszymi najlepszymi wskazówkami.

Aby uzyskać więcej informacji, zobacz [podstawy zabezpieczeń platformy Azure — omówienie](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview).

## <a name="network-security"></a>Bezpieczeństwo sieci

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: zabezpieczenia sieci](/azure/security/benchmarks/security-control-network-security).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: Ochrona zasobów platformy Azure w ramach sieci wirtualnych

**Wskazówki**: podczas tworzenia maszyny wirtualnej platformy Azure należy utworzyć sieć wirtualną lub użyć istniejącej sieci wirtualnej i skonfigurować maszynę wirtualną z podsiecią. Upewnij się, że we wszystkich wdrożonych podsieciach została zastosowana sieciowa Grupa zabezpieczeń z kontrolkami dostępu do sieci, które są specyficzne dla zaufanych portów i źródeł aplikacji.

Alternatywnie, jeśli masz określony przypadek użycia dla centralnej zapory, można również użyć zapory platformy Azure do spełnienia wymagań.

* [Obsługa sieci w kontekście zestawów skalowania maszyn wirtualnych platformy Azure](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-networking)

* [Jak utworzyć Virtual Network](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

* [Jak utworzyć sieciowej grupy zabezpieczeń z konfiguracją zabezpieczeń](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)

* [Jak wdrożyć i skonfigurować zaporę platformy Azure](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1,2: Monitoruj i Rejestruj konfigurację oraz ruch sieci wirtualnych, podsieci i interfejsów sieciowych

**Wskazówki**: Użyj Azure Security Center, aby zidentyfikować i przestrzegać zaleceń dotyczących ochrony sieci, aby pomóc w zabezpieczeniu zasobów maszyn wirtualnych platformy Azure na platformie Azure. Włącz dzienniki przepływu sieciowej grupy zabezpieczeń i Wyślij dzienniki do konta magazynu w celu przeprowadzenia inspekcji ruchu dla maszyn wirtualnych w przypadku nietypowej aktywności.

* [Jak włączyć dzienniki przepływu sieciowej grupy zabezpieczeń](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)

* [Informacje o zabezpieczeniach sieci zapewnianych przez Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-network-recommendations)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="13-protect-critical-web-applications"></a>1,3: Ochrona krytycznych aplikacji sieci Web

**Wskazówki**: Jeśli korzystasz z zestawu skalowania maszyn wirtualnych (VMSS) do hostowania aplikacji sieci Web, użyj sieciowej grupy zabezpieczeń (sieciowej grupy zabezpieczeń) w podsieci VMSS, aby ograniczyć ruch sieciowy, porty i protokoły, które mogą się komunikować. Postępuj zgodnie z najniższym podejściem do sieci podczas konfigurowania sieciowych grup zabezpieczeń, aby zezwalać na wymagany ruch do aplikacji.

Możesz również wdrożyć zaporę aplikacji sieci Web platformy Azure (WAF) przed krytycznymi aplikacjami sieci Web, aby uzyskać dodatkową inspekcję ruchu przychodzącego. Włącz ustawienie diagnostyczne dla WAF i pobierania dzienników do konta magazynu, centrum zdarzeń lub Log Analytics obszaru roboczego.

* [Obsługa sieci w kontekście zestawów skalowania maszyn wirtualnych platformy Azure](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-networking)

* [Tworzenie bramy aplikacji za pomocą zapory aplikacji sieci Web przy użyciu Azure Portal](https://docs.microsoft.com/azure/web-application-firewall/ag/application-gateway-web-application-firewall-portal)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: odmowa komunikacji ze znanymi niezłośliwymi adresami IP

**Wskazówki**: Włącz rozproszonej ochrony typu "odmowa usługi" (DDoS) w sieciach wirtualnych, aby chronić przed atakami DDoS. Za pomocą Azure Security Center zintegrowanej analizy zagrożeń można monitorować komunikację ze znanymi złośliwymi adresami IP. Skonfiguruj zaporę platformy Azure na każdym z segmentów Virtual Network, w których analiza zagrożeń jest włączona i skonfigurowana jako "Alert i Odmów" w przypadku złośliwego ruchu sieciowego.

Możesz użyć Azure Security Center dostęp do sieci w czasie, aby ograniczyć narażenie Windows Virtual Machines na zatwierdzone adresy IP przez ograniczony okres. Należy również użyć opcji Azure Security Center adaptacyjnej ochrony sieci, aby zalecać konfiguracje sieciowej grupy zabezpieczeń, które ograniczają porty i źródłowe adresy IP na podstawie rzeczywistego ruchu i analizy zagrożeń.

* [Jak skonfigurować ochronę DDoS](https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection)

* [Jak wdrożyć zaporę platformy Azure](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal)

* [Opis Azure Security Center zintegrowanej analizy zagrożeń](https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer)

* [Informacje o Azure Security Center adaptacyjnej ograniczania przepustowości sieci](https://docs.microsoft.com/azure/security-center/security-center-adaptive-network-hardening)

* [Informacje o Azure Security Center Access Control sieci w czasie](https://docs.microsoft.com/azure/security-center/security-center-just-in-time)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="15-record-network-packets"></a>1,5: rejestrowanie pakietów sieciowych

**Wskazówki**: można zarejestrować dzienniki przepływu sieciowej grupy zabezpieczeń na koncie magazynu w celu wygenerowania rekordów przepływu dla Virtual Machines platformy Azure. Podczas badania nietypowej aktywności można włączyć funkcję przechwytywania pakietów Network Watcher, aby można było przejrzeć ruch sieciowy pod kątem nietypowych i nieoczekiwanych działań.

* [Jak włączyć dzienniki przepływu sieciowej grupy zabezpieczeń](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)

* [Jak włączyć Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-create)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: wdrażanie opartych na sieci systemów zapobiegania wykrywaniem i dostępem intruzów (identyfikatorów/adresów IP)

**Wskazówki**: łącząc przechwycenia pakietów udostępniane przez Network Watcher i narzędzie identyfikatorów typu "open source", można przeprowadzić wykrywanie wtargnięcia w sieci dla szerokiego zakresu zagrożeń. Ponadto w razie potrzeby można wdrożyć zaporę platformy Azure na segmentach Virtual Network, z włączoną analizą zagrożeń i konfiguracją "Alert i Odmów" w przypadku złośliwego ruchu sieciowego.

* [Wykrywanie intruzów w sieci przy użyciu narzędzi Network Watcher i Open Source](https://docs.microsoft.com/azure/network-watcher/network-watcher-intrusion-detection-open-source-tools)

* [Jak wdrożyć zaporę platformy Azure](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal)

* [Jak skonfigurować alerty za pomocą zapory platformy Azure](https://docs.microsoft.com/azure/firewall/threat-intel)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="17-manage-traffic-to-web-applications"></a>1,7: zarządzanie ruchem do aplikacji sieci Web

**Wskazówki**: Jeśli korzystasz z zestawu skalowania maszyn wirtualnych (VMSS) do hostowania aplikacji sieci Web, możesz wdrożyć usługę Azure Application Gateway dla aplikacji sieci Web z WŁĄCZONYm protokołem HTTPS/SSL dla zaufanych certyfikatów. Dzięki usłudze Azure Application Gateway kierowanie ruchu sieci Web aplikacji do określonych zasobów przez przypisanie odbiorników do portów, tworzenie reguł i Dodawanie zasobów do puli zaplecza, takiej jak VMSS itp.

* [Jak wdrożyć Application Gateway](https://docs.microsoft.com/azure/application-gateway/quick-create-portal)

* [Jak skonfigurować Application Gateway do korzystania z protokołu HTTPS](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal)

* [Tworzenie zestawu skalowania, który odwołuje się do usługi Application Gateway](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-networking#create-a-scale-set-that-references-an-application-gateway)

* [Omówienie równoważenia obciążenia warstwy 7 za pomocą bram aplikacji sieci Web platformy Azure](https://docs.microsoft.com/azure/application-gateway/overview)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: Minimalizacja złożoności i kosztów administracyjnych reguł zabezpieczeń sieci

**Wskazówki**: Użyj tagów usługi Virtual Network, aby zdefiniować kontrolę dostępu do sieci dla sieciowych grup zabezpieczeń lub zapory platformy Azure skonfigurowanej dla maszyn wirtualnych platformy Azure. Podczas tworzenia reguł zabezpieczeń można użyć tagów usługi zamiast konkretnych adresów IP. Określając nazwę tagu usługi (np. ApiManagement) w odpowiednim polu źródłowym lub docelowym reguły, można zezwolić na ruch dla odpowiedniej usługi lub go odrzucić. Firma Microsoft zarządza prefiksami adresów, które obejmują tag usługi, i automatycznie aktualizuje tag usługi jako adresy.

* [Zrozumienie i używanie tagów usługi](https://docs.microsoft.com/azure/virtual-network/service-tags-overview)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: Obsługa standardowych konfiguracji zabezpieczeń dla urządzeń sieciowych

**Wskazówki**: Definiowanie i implementowanie standardowych konfiguracji zabezpieczeń dla platformy Azure Virtual Machine Scale Sets przy użyciu Azure Policy. Możesz również użyć planów platformy Azure, aby uprościć wdrażanie maszyn wirtualnych platformy Azure na dużą skalę przez pakowanie kluczowych artefaktów środowiska, takich jak szablony Azure Resource Manager, przypisania ról i przypisania Azure Policy, w ramach jednej definicji planu. Można zastosować plan do subskrypcji i włączyć zarządzanie zasobami za pomocą wersji strategii.

* [Jak skonfigurować Azure Policy i zarządzać nimi](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Informacje o szablonach zestawu skalowania maszyn wirtualnych](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-mvss-start)

* [Przykłady Azure Policy dla sieci](https://docs.microsoft.com/azure/governance/policy/samples/#network)

* [Jak utworzyć Azure Blueprint](https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="110-document-traffic-configuration-rules"></a>1,10: udokumentowanie reguł konfiguracji ruchu

**Wskazówki**: można używać tagów dla sieciowych grup zabezpieczeń (sieciowej grupy zabezpieczeń) i innych zasobów związanych z zabezpieczeniami sieci i przepływem ruchu skonfigurowanym dla maszyn wirtualnych z systemem Windows. W przypadku poszczególnych reguł sieciowej grupy zabezpieczeń Użyj pola "Description", aby określić potrzeby biznesowe i/lub czas trwania dla reguł zezwalających na ruch do/z sieci.

* [Tworzenie i używanie tagów](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

* [Jak utworzyć Virtual Network](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

* [Jak utworzyć sieciowej grupy zabezpieczeń z konfiguracją zabezpieczeń](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: Użyj zautomatyzowanych narzędzi do monitorowania konfiguracji zasobów sieciowych i wykrywania zmian

**Wskazówki**: Użyj dziennika aktywności platformy Azure do monitorowania zmian w konfiguracjach zasobów sieciowych związanych z zestawem skalowania maszyn wirtualnych platformy Azure. Utwórz alerty w Azure Monitor, które będą wyzwalane po wprowadzeniu zmian w krytycznych ustawieniach sieciowych lub zasobach.

Użyj Azure Policy do sprawdzania poprawności (i/lub korygowania) konfiguracji zasobów sieciowych związanych z zestawem skalowania maszyn wirtualnych.

* [Jak wyświetlać i pobierać zdarzenia dziennika aktywności platformy Azure](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view)

* [Jak utworzyć alerty w Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

* [Jak skonfigurować Azure Policy i zarządzać nimi](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Przykłady Azure Policy dla sieci](https://docs.microsoft.com/azure/governance/policy/samples/#network)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

## <a name="logging-and-monitoring"></a>Rejestrowanie i monitorowanie

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: rejestrowanie i monitorowanie](/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="21-use-approved-time-synchronization-sources"></a>2,1: Użyj źródeł synchronizacji zatwierdzonego czasu

**Wskazówki**: Firma Microsoft przechowuje źródła czasu dla zasobów platformy Azure, ale istnieje możliwość zarządzania ustawieniami synchronizacji czasu dla Virtual Machines.

* [Jak skonfigurować synchronizację czasu dla zasobów obliczeniowych systemu Windows Azure](https://docs.microsoft.com/azure/virtual-machines/windows/time-sync)

* [Jak skonfigurować synchronizację czasu dla zasobów obliczeniowych systemu Azure Linux](https://docs.microsoft.com/azure/virtual-machines/linux/time-sync)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: udostępnione

### <a name="22-configure-central-security-log-management"></a>2,2: Skonfiguruj centralne zarządzanie dziennikami zabezpieczeń

**Wskazówki**: dzienniki aktywności mogą służyć do inspekcji operacji i akcji wykonywanych na zasobach zestawu skalowania maszyn wirtualnych. Dziennik aktywności zawiera wszystkie operacje zapisu (PUT, POST, DELETE) dla zasobów, z wyjątkiem operacji odczytu (GET). Dzienników aktywności można użyć do znalezienia błędu podczas rozwiązywania problemów lub do monitorowania sposobu, w jaki użytkownik w organizacji zmodyfikował zasób.

Można włączać i dołączać dane dzienników z dzienników aktywności platformy Azure lub zasobów maszyn wirtualnych do centrum danych platformy Azure lub SIEM innych firm na potrzeby centralnego zarządzania dziennikami zabezpieczeń.

Użyj Azure Security Center, aby zapewnić monitorowanie dzienników zdarzeń zabezpieczeń dla usługi Azure Virtual Machines. Dane dotyczące ilości danych generowanych przez dziennik zdarzeń zabezpieczeń nie są domyślnie przechowywane.

Jeśli organizacja chce zachować dane dziennika zdarzeń zabezpieczeń z maszyny wirtualnej, może być ona przechowywana w obszarze roboczym Log Analytics w ramach żądanej warstwy zbierania danych skonfigurowanej w Azure Security Center.

* [Jak zbierać dzienniki platformy i metryki za pomocą Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings)

* [Jak dołączyć wskaźnik na platformie Azure](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

* [Jak rozpocząć pracę z usługą Azure Monitor i integracją SIEM innej firmy](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools)

* [Zbieranie danych w usłudze Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection#data-collection-tier)

* [Jak monitorować maszyny wirtualne na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/windows/monitor)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: Włączanie rejestrowania inspekcji dla zasobów platformy Azure

**Wskazówki**: dzienniki aktywności mogą służyć do inspekcji operacji i akcji wykonywanych na zasobach zestawu skalowania maszyn wirtualnych. Dziennik aktywności zawiera wszystkie operacje zapisu (PUT, POST, DELETE) dla zasobów, z wyjątkiem operacji odczytu (GET). Dzienników aktywności można użyć do znalezienia błędu podczas rozwiązywania problemów lub do monitorowania sposobu, w jaki użytkownik w organizacji zmodyfikował zasób.

Włącz zbieranie danych diagnostycznych systemu operacyjnego gościa, wdrażając rozszerzenie diagnostyki na Virtual Machines (VM). Można użyć rozszerzenia diagnostyki do zbierania danych diagnostycznych, takich jak Dzienniki aplikacji lub liczniki wydajności, z maszyny wirtualnej platformy Azure.

Aby uzyskać zaawansowaną widoczność aplikacji i usług obsługiwanych przez zestaw skalowania maszyn wirtualnych platformy Azure, możesz włączyć zarówno Azure Monitor dla maszyn wirtualnych, jak i Application Insights. Za pomocą Application Insights można monitorować aplikację i przechwytywać dane telemetryczne, takie jak żądania HTTP, wyjątki itp., aby można było skorelować problemy między maszynami wirtualnymi i aplikacją.

* [Jak zbierać dzienniki platformy i metryki za pomocą Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings)

* [Wyświetlanie i pobieranie zdarzeń dziennika aktywności platformy Azure](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view)

* [Jak monitorować maszyny wirtualne na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/windows/monitor)

* [Omówienie usługi Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: Zbierz dzienniki zabezpieczeń z systemów operacyjnych

**Wskazówki**: Użyj Azure Security Center, aby zapewnić monitorowanie dzienników zdarzeń zabezpieczeń dla usługi Azure Virtual Machines. Dane dotyczące ilości danych generowanych przez dziennik zdarzeń zabezpieczeń nie są domyślnie przechowywane.

Jeśli organizacja chce zachować dane dziennika zdarzeń zabezpieczeń z maszyny wirtualnej, może być ona przechowywana w obszarze roboczym Log Analytics w ramach żądanej warstwy zbierania danych skonfigurowanej w Azure Security Center.

* [Zbieranie danych w usłudze Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection#data-collection-tier)

* [Jak monitorować maszyny wirtualne na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/windows/monitor)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="25-configure-security-log-storage-retention"></a>2,5: Konfigurowanie przechowywania magazynu dzienników zabezpieczeń

**Wskazówki**: Upewnij się, że wszystkie konta magazynów lub log Analytics obszary robocze używane do przechowywania dzienników maszyn wirtualnych mają ustawiony okres przechowywania dziennika zgodnie z przepisami obowiązującymi w organizacji.

* [Jak monitorować maszyny wirtualne na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/windows/monitor)

* [Jak skonfigurować okres przechowywania obszaru roboczego Log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="26-monitor-and-review-logs"></a>2,6: dzienniki monitorowania i przeglądania

**Wskazówki**: analizowanie i monitorowanie dzienników pod kątem nietypowego zachowania i regularnego przeglądania wyników. Użyj Azure Monitor do przeglądania dzienników i wykonywania zapytań dotyczących danych dziennika.

Alternatywnie możesz włączyć i przejrzeć dane na platformie Azure, aby monitorować i przeglądać dzienniki.

* [Jak dołączyć wskaźnik na platformie Azure](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

* [Informacje o obszarze roboczym Log Analytics](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal)

* [Jak wykonywać niestandardowe zapytania w Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: Włączanie alertów dla nietypowych działań

**Wskazówki**: Użyj Azure Security Center skonfigurowany za pomocą log Analytics obszaru roboczego do monitorowania i wysyłania alertów dotyczących nietypowej aktywności znalezionych w dziennikach zabezpieczeń i zdarzeniach dla Virtual Machines platformy Azure.

Alternatywnie możesz włączyć i dołączyć dane do usługi Azure wskaźnikowej lub SIEM innych firm, aby skonfigurować alerty dla nietypowej aktywności.

* [Jak dołączyć wskaźnik na platformie Azure](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

* [Jak zarządzać alertami w Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts)

* [Jak ostrzec dane dziennika usługi log Analytics](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="28-centralize-anti-malware-logging"></a>2,8: scentralizowanie rejestrowania chroniącego przed złośliwym oprogramowaniem

**Wskazówki**: możesz użyć programu Microsoft chroniącego przed złośliwym oprogramowaniem dla platformy Azure Cloud Services i Virtual Machines i skonfigurować maszyny wirtualne z systemem Windows do rejestrowania zdarzeń na koncie usługi Azure Storage. Skonfiguruj obszar roboczy Log Analytics, aby pozyskiwać zdarzenia z kont magazynu, a następnie utwórz alerty, jeśli są odpowiednie. Postępuj zgodnie z zaleceniami w Azure Security Center: "COMPUTE &amp; Apps". W przypadku maszyn wirtualnych z systemem Linux do wykrywania luk w zabezpieczeniach programu jest potrzebne narzędzie innej firmy.

* [Jak skonfigurować oprogramowanie antywirusowe firmy Microsoft dla Cloud Services i Virtual Machines](https://docs.microsoft.com/azure/security/fundamentals/antimalware)

* [Jak włączyć monitorowanie na poziomie gościa dla Virtual Machines](https://docs.microsoft.com/azure/cost-management/azure-vm-extended-metrics)

* [Instrukcje dotyczące dołączania serwerów z systemem Linux do usługi Azure Security Center](https://docs.microsoft.com/azure/security-center/quick-onboard-linux-computer)

* [Poniższy link zapewnia wskazówki dotyczące zabezpieczeń zalecanych przez firmę Microsoft, które mogą stanowić listę kryteriów dla wybranego oprogramowania.](https://docs.microsoft.com/azure/virtual-machines/linux/security-recommendations)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="29-enable-dns-query-logging"></a>2,9: Włączanie rejestrowania zapytań DNS

**Wskazówki**: Implementowanie rozwiązania innej firmy z witryny Azure Marketplace na potrzeby rejestrowania w systemie DNS zgodnie z potrzebami organizacji.

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="210-enable-command-line-audit-logging"></a>2,10: Włączanie rejestrowania inspekcji w wierszu polecenia

**Wskazówki**: Azure Security Center zawiera monitorowanie dziennika zdarzeń zabezpieczeń dla usługi Azure Virtual Machines (VM). Security Center udostępnia Microsoft Monitoring Agent na wszystkich obsługiwanych maszynach wirtualnych platformy Azure i nowych, które są tworzone w przypadku włączenia automatycznej aprowizacji lub można zainstalować agenta ręcznie. Agent włącza zdarzenie tworzenia procesu 4688 i pole wiersza polecenia wewnątrz zdarzenia 4688. Nowe procesy utworzone na maszynie wirtualnej są rejestrowane przez dziennik zdarzeń i monitorowane przez usługi wykrywania Security Center.

W przypadku maszyn wirtualnych z systemem Linux można ręcznie skonfigurować rejestrowanie konsoli na poszczególnych węzłach i użyć dziennika systemowego do przechowywania danych. Należy również użyć obszaru roboczego Log Analytics Azure Monitor do przeglądania dzienników i wykonywania zapytań dotyczących danych dziennika systemowego z maszyn wirtualnych platformy Azure.

* [Zbieranie danych w usłudze Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection#data-collection-tier)

* [Jak wykonywać niestandardowe zapytania w Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries)

* [Syslog data sources in Azure Monitor (Źródła danych usługi Syslog w usłudze Azure Monitor)](https://docs.microsoft.com/azure/azure-monitor/platform/data-sources-syslog)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

## <a name="identity-and-access-control"></a>Tożsamość i kontrola dostępu

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: tożsamość i kontrola dostępu](/azure/security/benchmarks/security-control-identity-access-control).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: obsługa spisu kont administracyjnych

**Wskazówki**: gdy Azure Active Directory jest zalecaną metodą administrowania dostępem użytkowników, usługa Azure Virtual Machines może mieć konta lokalne. Zarówno konta lokalne, jak i domeny powinny być przeglądane i zarządzane, zwykle z minimalnym wpływem. Ponadto w celu uzyskania dostępu do zasobów maszyn wirtualnych Skorzystaj z usługi Azure Privileged Identity Management.

* [Informacje dotyczące kont lokalnych są dostępne pod adresem](https://docs.microsoft.com/azure/active-directory/devices/assign-local-admin#manage-the-device-administrator-role)

* [Informacje o programie Privileged Identity Manager](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="32-change-default-passwords-where-applicable"></a>3,2: Zmień domyślne hasła, jeśli ma to zastosowanie

**Wskazówki**: zestaw skalowania maszyn wirtualnych platformy Azure i Azure Active Directory nie mają koncepcji domyślnych haseł. Klient odpowiedzialny za aplikacje innych firm i usługi w portalu Marketplace, które mogą korzystać z domyślnych haseł.

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: Użyj dedykowanych kont administracyjnych

**Wskazówki**: Tworzenie standardowych procedur operacyjnych dotyczących korzystania z dedykowanych kont administracyjnych, które mają dostęp do maszyn wirtualnych. Użyj Azure Security Center Zarządzanie tożsamościami i dostępem, aby monitorować liczbę kont administracyjnych. Wszystkie konta administratorów służące do uzyskiwania dostępu do zasobów maszyn wirtualnych platformy Azure mogą być również zarządzane przez usługę Azure Privileged Identity Management (PIM). Usługa Azure Privileged Identity Management udostępnia kilka opcji, takich jak Justowanie w czasie, wymagające Multi-Factor Authentication przed założeniem roli, a także opcje delegowania, dzięki czemu uprawnienia są dostępne tylko dla określonych ram czasowych i wymagają osoby zatwierdzającej.

* [Informacje o tożsamości i dostępie Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

* [Informacje o programie Privileged Identity Manager](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3,4: Użyj Azure Active Directory logowania jednokrotnego (SSO)

**Wskazówki**: wszędzie tam, gdzie to możliwe, należy używać logowania jednokrotnego z Azure Active Directory zamiast konfigurować indywidualne poświadczenia autonomiczne dla poszczególnych usług. Użyj Azure Security Center zalecenia dotyczące zarządzania tożsamościami i dostępem.

* [Single sign-on to applications in Azure Active Directory (Logowanie jednokrotne do aplikacji w usłudze Azure Active Directory)](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

* [Jak monitorować tożsamość i dostęp w Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Użyj uwierzytelniania wieloskładnikowego dla wszystkich Azure Active Directory dostępu opartego na usłudze

**Wskazówki**: Włącz usługę Azure AD MFA i postępuj zgodnie z zaleceniami Azure Security Center zarządzaniem tożsamościami i dostępem.

* [Jak włączyć usługę MFA na platformie Azure](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

* [Jak monitorować tożsamość i dostęp w Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3,6: Korzystaj z bezpiecznych stacji roboczych zarządzanych przez platformę Azure na potrzeby zadań administracyjnych

**Wskazówki**: Użyj dostępem uprzywilejowanym (uprzywilejowanych stacji roboczych dostępu) za pomocą usługi MFA skonfigurowanej do logowania się i konfigurowania zasobów platformy Azure.

* [Dowiedz się więcej o stacjach roboczych uprzywilejowanego dostępu](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations)

* [Jak włączyć usługę MFA na platformie Azure](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: Rejestruj i Ostrzegaj o podejrzanych działaniach z kont administracyjnych

**Wskazówki**: Użyj Azure AD PRIVILEGED Identity Management (PIM) do generowania dzienników i alertów w przypadku wystąpienia podejrzanych lub niebezpiecznych działań w środowisku. Użyj funkcji wykrywania ryzyka usługi Azure AD, aby wyświetlać alerty i raporty na temat ryzykownego zachowania użytkowników. Opcjonalnie klient może pozyskiwać Azure Security Center alerty wykrywania ryzyka w Azure Monitor i skonfigurować niestandardowe alerty/powiadomienia przy użyciu grup akcji.

* [Jak wdrożyć Privileged Identity Management (PIM)](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan)

* [Zrozumienie Azure Security Center wykrywania ryzyka (podejrzane działanie)](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events)

* [Jak zintegrować dzienniki aktywności platformy Azure z usługą Azure Monitor](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

* [Jak skonfigurować grupy akcji dla niestandardowych alertów i powiadomień](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: zarządzanie zasobami platformy Azure tylko z zatwierdzonych lokalizacji

**Wskazówki**: Użyj zasad dostępu warunkowego Azure Active Directory i nazwanych lokalizacji, aby zezwolić na dostęp tylko z określonych logicznych grup zakresów lub krajów/regionów adresów IP.

* [Jak skonfigurować nazwane lokalizacje na platformie Azure](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="39-use-azure-active-directory"></a>3,9: Użyj Azure Active Directory

**Wskazówki**: Użyj Azure Active Directory (Azure AD) jako centralnego systemu uwierzytelniania i autoryzacji. Usługa Azure AD chroni dane przy użyciu silnego szyfrowania danych przechowywanych i przesyłanych. Usługa Azure AD również Sole, skróty i bezpieczne przechowywanie poświadczeń użytkownika. Przy użyciu tożsamości zarządzanych można uwierzytelniać się w dowolnej usłudze, która obsługuje uwierzytelnianie usługi Azure AD, w tym Key Vault, bez żadnych poświadczeń w kodzie. Kod, który jest uruchomiony na maszynie wirtualnej, może używać swojej tożsamości zarządzanej do żądania tokenów dostępu dla usług, które obsługują uwierzytelnianie w usłudze Azure AD.

* [Jak utworzyć i skonfigurować wystąpienie usługi Azure AD](https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-create-instance)

* [Zarządzanie tożsamościami dla zasobów platformy Azure — omówienie](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: regularnie Przeglądaj i Uzgodnij dostęp użytkowników

**Wskazówki**: usługa Azure AD udostępnia dzienniki, które ułatwiają odnajdywanie starych kont. Ponadto Azure Active Directory przeglądy dostępu do tożsamości w celu wydajnego zarządzania członkostwem w grupach, dostępem do aplikacji dla przedsiębiorstw i przypisaniami ról. Dostęp użytkownika może być regularnie przeglądany, aby upewnić się, że tylko Ci użytkownicy mają stały dostęp. W przypadku korzystania z usługi Azure Virtual Machines należy przejrzeć lokalne grupy zabezpieczeń i użytkowników, aby upewnić się, że nie ma żadnych nieoczekiwanych kont, które mogłyby naruszyć bezpieczeństwo systemu.

* [Jak korzystać z przeglądów dostępu do tożsamości platformy Azure](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: Monitor próbuje uzyskać dostęp do zdezaktywowanych poświadczeń

**Wskazówki**: Konfigurowanie ustawień diagnostycznych dla Azure Active Directory w celu wysyłania dzienników inspekcji i dzienników logowania do log Analytics obszaru roboczego. Należy również użyć Azure Monitor do przeglądania dzienników i wykonywania zapytań dotyczących danych dziennika z maszyn wirtualnych platformy Azure.

* [Informacje o obszarze roboczym Log Analytics](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal)

* [Jak zintegrować dzienniki aktywności platformy Azure z usługą Azure Monitor](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

* [Jak wykonywać niestandardowe zapytania w Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries)

* [Jak monitorować maszyny wirtualne na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/windows/monitor)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: odchylenia zachowania alertu dotyczącego logowania na koncie

**Wskazówki**: Użyj funkcji ochrony przed ryzykiem i tożsamością Azure Active Directory, aby skonfigurować automatyczne odpowiedzi na wykryte podejrzane działania dotyczące zasobów konta magazynu. Aby zaimplementować odpowiedzi na zabezpieczenia organizacji, należy włączyć automatyczne odpowiedzi za pomocą wskaźnikowego platformy Azure.

* [Jak wyświetlić ryzykowne logowania usługi Azure AD](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

* [Jak skonfigurować i włączyć zasady dotyczące ryzyka związanego z ochroną tożsamości](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies)

* [Jak dołączyć wskaźnik na platformie Azure](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: Zapewnij firmie Microsoft dostęp do odpowiednich danych klienta w scenariuszach pomocy technicznej

**Wskazówki**: w scenariuszach pomocy technicznej, w których firma Microsoft potrzebuje dostępu do danych klienta (na przykład w trakcie żądania obsługi), należy używać skrytka klienta dla maszyn wirtualnych platformy Azure do przeglądania i zatwierdzania lub odrzucania żądań dostępu do danych klienta.

* [Informacje o Skrytka klienta](https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

## <a name="data-protection"></a>Ochrona danych

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Ochrona danych](/azure/security/benchmarks/security-control-data-protection).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: przechowywanie spisu poufnych informacji

**Wskazówki**: Użyj tagów, aby pomóc w śledzeniu maszyn wirtualnych platformy Azure, które przechowują lub przetwarzają informacje poufne.

* [Tworzenie i używanie tagów](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: Izoluj systemy przechowujące lub przetwarzające informacje poufne

**Wskazówki**: implementowanie oddzielnych subskrypcji i/lub grup zarządzania na potrzeby tworzenia, testowania i produkcji. Zasoby powinny być oddzielone przez sieć wirtualną/podsieć, odpowiednio otagowane i zabezpieczone w sieciowej grupie zabezpieczeń (sieciowej grupy zabezpieczeń) lub przez zaporę platformy Azure. W przypadku Virtual Machines przechowywania lub przetwarzania danych poufnych należy zaimplementować zasady i procedury, aby je wyłączyć, gdy nie są używane.

* [Jak utworzyć dodatkowe subskrypcje platformy Azure](https://docs.microsoft.com/azure/billing/billing-create-subscription)

* [Jak utworzyć Grupy zarządzania](https://docs.microsoft.com/azure/governance/management-groups/create)

* [Tworzenie i używanie tagów](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

* [Jak utworzyć Virtual Network](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

* [Jak utworzyć sieciowej grupy zabezpieczeń z konfiguracją zabezpieczeń](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)

* [Jak wdrożyć zaporę platformy Azure](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal)

* [Jak skonfigurować alert lub alert i odmówić za pomocą zapory platformy Azure](https://docs.microsoft.com/azure/firewall/threat-intel)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: Monitoruj i blokuj nieautoryzowany transfer informacji poufnych

**Wskazówki**: Zaimplementuj rozwiązanie innych firm w sieci obwodowej, które monitoruje do nieautoryzowanego transferu poufnych informacji i blokuje takie transfery podczas powiadamiania specjalistów ds. bezpieczeństwa informacji.

W przypadku podstawowej platformy zarządzanej przez firmę Microsoft Firma Microsoft traktuje całą zawartość klienta jako poufną, aby chronić przed utratą i narażeniem danych przez klienta. Aby zapewnić bezpieczeństwo danych klienta na platformie Azure, firma Microsoft wdrożyła i utrzymuje pakiet niezawodnych kontroli i możliwości ochrony danych.

* [Zrozumienie ochrony danych klientów na platformie Azure](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: Szyfruj wszystkie poufne informacje podczas przesyłania

**Wskazówki**: dane przesyłane do, z i między Virtual Machines (VM) z systemem Windows są szyfrowane na wiele sposobów, w zależności od rodzaju połączenia, takiego jak podczas łączenia się z maszyną wirtualną w sesji protokołu RDP lub SSH.

Firma Microsoft korzysta z protokołu Transport Layer Security (TLS) w celu ochrony danych w przypadku podróży między usługami w chmurze i klientami.

* [Szyfrowanie w trakcie przesyłania na maszynach wirtualnych](https://docs.microsoft.com/azure/security/fundamentals/encryption-overview#in-transit-encryption-in-vms)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: udostępnione

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: Użyj aktywnego narzędzia do odnajdywania, aby identyfikować poufne dane

**Wskazówki**: Użyj narzędzia do odnajdywania aktywnego innej firmy, aby zidentyfikować wszystkie informacje poufne przechowywane, przetwarzane lub przekazywane przez systemy technologiczne organizacji, w tym zlokalizowane na miejscu lub u dostawcy usług zdalnych, a także aktualizować spis informacji poufnych w organizacji.

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: korzystanie z usługi Azure RBAC do kontrolowania dostępu do zasobów

**Wskazówki**: korzystanie z funkcji kontroli dostępu opartej na rolach (Azure RBAC) na platformie Azure umożliwia rozdzielenie obowiązków w zespole i udzielanie użytkownikom tylko dostępu do tych maszyn wirtualnych, których potrzebują do wykonywania swoich zadań. Zamiast udzielać każdemu nieograniczonyemu uprawnienia do maszyny wirtualnej, można zezwolić tylko na niektóre akcje. Można skonfigurować kontrolę dostępu dla maszyny wirtualnej w Azure Portal przy użyciu interfejsu wiersza polecenia platformy Azure lub Azure PowerShell.

* [Kontrola dostępu na podstawie ról platformy Azure](https://docs.microsoft.com/azure/role-based-access-control/overview)

* [Role wbudowane platformy Azure](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#virtual-machine-contributor)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: Wymuś kontrolę dostępu przy użyciu ochrony przed utratą danych opartą na hoście

**Wskazówki**: implementowanie narzędzia innej firmy, takiego jak automatyczne rozwiązanie do ochrony przed utratą danych opartych na hoście, aby wymusić kontrolę dostępu w celu ograniczenia ryzyka naruszeń danych.

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: Szyfruj poufne informacje w spoczynku

**Wskazówki**: dyski wirtualne w Virtual Machines (VM) są szyfrowane w spoczynku przy użyciu szyfrowania po stronie serwera lub usługi Azure Disk Encryption (ADE). Azure Disk Encryption wykorzystuje DM-Crypt funkcji systemu Linux do szyfrowania dysków zarządzanych z kluczami zarządzanymi przez klienta w ramach maszyny wirtualnej gościa. Szyfrowanie po stronie serwera z kluczami zarządzanymi przez klienta usprawnia w systemie ADE, umożliwiając korzystanie z dowolnych typów systemów operacyjnych i obrazów dla maszyn wirtualnych przez szyfrowanie danych w usłudze Storage.

* [Azure Disk Encryption Virtual Machine Scale Sets](https://docs.microsoft.com/azure/virtual-machine-scale-sets/disk-encryption-overview)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: Rejestruj i Ostrzegaj o zmianach krytycznych zasobów platformy Azure

**Wskazówki**: Użyj Azure monitor z dziennikiem aktywności platformy Azure, aby utworzyć alerty dotyczące zmian w ramach zestawów skalowania maszyn wirtualnych i powiązanych zasobów.

* [Jak utworzyć alerty dla zdarzeń dziennika aktywności platformy Azure](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

* [Rejestrowanie analityki magazynu platformy Azure](https://docs.microsoft.com/azure/storage/common/storage-analytics-logging)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

## <a name="vulnerability-management"></a>Zarządzanie lukami w zabezpieczeniach

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Zarządzanie lukami w zabezpieczeniach](/azure/security/benchmarks/security-control-vulnerability-management).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: uruchamianie narzędzi do skanowania automatycznych luk w zabezpieczeniach

**Wskazówki**: Postępuj zgodnie z zaleceniami Azure Security Center na temat przeprowadzania ocen luk w zabezpieczeniach na platformie Azure Virtual Machines. Skorzystaj z rozwiązań zabezpieczeń platformy Azure lub rozwiązania innych firm, aby przeprowadzać oceny luk w zabezpieczeniach dla maszyn wirtualnych.

* [Jak zaimplementować zalecenia dotyczące oceny luk w zabezpieczeniach Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: Wdróż automatyczne rozwiązanie do zarządzania poprawkami systemu operacyjnego

**Wskazówki**: Włączanie automatycznych uaktualnień systemu operacyjnego dla obsługiwanych wersji systemu operacyjnego lub dla obrazów niestandardowych przechowywanych w galerii obrazów udostępnionych.

* [Automatyczne uaktualnienia systemu operacyjnego dla zestawów skalowania maszyn wirtualnych na platformie Azure](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5,3: Wdróż rozwiązanie zautomatyzowanego zarządzania poprawkami dla tytułów oprogramowania innych firm

**Wskazówki**: usługa Azure Virtual Machine Scale Sets (VMSS) może korzystać z automatycznych uaktualnień obrazu systemu operacyjnego. Możesz użyć rozszerzenia konfiguracji żądanego stanu (DSC) platformy Azure dla bazowych maszyn wirtualnych w VMSS. Konfiguracja DSC służy do konfigurowania maszyn wirtualnych, gdy są one w trybie online, tak aby działały żądane oprogramowanie.

* [Używanie Virtual Machine Scale Sets z rozszerzeniem DSC platformy Azure](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-dsc)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: porównanie luk w zabezpieczeniach z tyłu do tyłu

**Wskazówki**: Eksportuj wyniki skanowania w regularnych odstępach czasu i Porównaj wyniki, aby sprawdzić, czy luki zostały skorygowane. W przypadku korzystania z rekomendacji dotyczących zarządzania lukami w zabezpieczeniach sugerowanej przez Azure Security Center klient może przestawić w portalu wybranego rozwiązania, aby wyświetlić historyczne dane skanowania.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: Użyj procesu oceny ryzyka, aby określić priorytety korygowania odkrytych luk w zabezpieczeniach

**Wskazówki**: Użyj domyślnych ocen ryzyka ("Secure Score") dostarczonych przez Azure Security Center.

* [Informacje na temat Azure Security Center zabezpieczeń](https://docs.microsoft.com/azure/security-center/security-center-secure-score)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

## <a name="inventory-and-asset-management"></a>Zarządzanie magazynem i zasobami

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Spis i zarządzanie zasobami](/azure/security/benchmarks/security-control-inventory-asset-management).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: Użyj rozwiązania automatycznego odnajdywania zasobów

**Wskazówki**: Użyj grafu zasobów platformy Azure do wykonywania zapytań i odnajdywania wszystkich zasobów (w tym maszyn wirtualnych) w ramach subskrypcji. Upewnij się, że masz odpowiednie uprawnienia (odczyt) w dzierżawie i że można wyliczyć wszystkie subskrypcje platformy Azure oraz zasoby w ramach subskrypcji.

* [Jak tworzyć zapytania za pomocą usługi Azure Graph](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

* [Jak wyświetlić subskrypcje platformy Azure](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

* [Opis kontroli RBAC platformy Azure](https://docs.microsoft.com/azure/role-based-access-control/overview)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="62-maintain-asset-metadata"></a>6,2: Konserwowanie metadanych zasobów

**Wskazówki**: stosowanie tagów do zasobów platformy Azure, dzięki czemu metadane są logicznie zorganizowane zgodnie z taksonomią.

* [Tworzenie i używanie tagów](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: Usuń nieautoryzowane zasoby platformy Azure

**Wskazówki**: używanie tagowania, grup zarządzania i oddzielnych subskrypcji, gdzie to konieczne, do organizowania i śledzenia Virtual Machinesych zestawów skalowania i powiązanych zasobów. Regularnie Uzgadniaj spis i zapewnij, że nieautoryzowane zasoby są usuwane z subskrypcji w odpowiednim czasie.

* [Jak utworzyć dodatkowe subskrypcje platformy Azure](https://docs.microsoft.com/azure/billing/billing-create-subscription)

* [Jak utworzyć Grupy zarządzania](https://docs.microsoft.com/azure/governance/management-groups/create)

* [Tworzenie i używanie tagów](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6,4: Definiowanie i konserwowanie spisu zatwierdzonych zasobów platformy Azure

**Wskazówki**: Tworzenie spisu zatwierdzonych zasobów platformy Azure oraz zatwierdzonego oprogramowania do zasobów obliczeniowych zgodnie z naszymi potrzebami organizacji.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: Monitoruj niezatwierdzone zasoby platformy Azure

**Wskazówki**: Użyj usługi Azure Policy, aby wprowadzić ograniczenia dotyczące typu zasobów, które można utworzyć w subskrypcjach klientów, korzystając z następujących wbudowanych definicji zasad:
- Niedozwolone typy zasobów
- Dozwolone typy zasobów

Ponadto Użyj grafu zasobów platformy Azure do wykonywania zapytań/odnajdywania zasobów w ramach subskrypcji. Może to pomóc w środowiskach opartych na wysokich zabezpieczeniach, takich jak te z kontami magazynu.

* [Jak skonfigurować Azure Policy i zarządzać nimi](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Jak tworzyć zapytania za pomocą usługi Azure Graph](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: Monitoruj niezatwierdzone aplikacje oprogramowania w ramach zasobów obliczeniowych

**Wskazówki**: Azure Automation zapewnia pełną kontrolę podczas wdrażania, działania i likwidowania obciążeń i zasobów. Skorzystaj ze spisu maszyn wirtualnych platformy Azure, aby zautomatyzować zbieranie informacji o całym oprogramowaniu na Virtual Machines. Uwaga: nazwa oprogramowania, wersja, Wydawca i czas odświeżania są dostępne w Azure Portal. Aby uzyskać dostęp do daty instalacji i innych informacji, klient musi włączyć diagnostykę na poziomie gościa i przenieść dzienniki zdarzeń systemu Windows do obszaru roboczego Log Analytics.

Obecnie adaptacyjne kontrolki aplikacji nie są dostępne dla Virtual Machine Scale Sets.

* [Wprowadzenie do usługi Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro)

* [Jak włączyć Spis maszyn wirtualnych platformy Azure](https://docs.microsoft.com/azure/automation/automation-tutorial-installed-software)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: Usuń niezatwierdzone zasoby platformy Azure i aplikacje oprogramowania

**Wskazówki**: Azure Automation zapewnia pełną kontrolę podczas wdrażania, działania i likwidowania obciążeń i zasobów. Change Tracking można użyć do zidentyfikowania wszystkich programów zainstalowanych w programie Virtual Machines. Aby usunąć nieautoryzowane oprogramowanie, można zaimplementować własny proces lub użyć konfiguracji stanu Azure Automation.

* [Wprowadzenie do usługi Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro)

* [Śledź zmiany w środowisku przy użyciu rozwiązania Change Tracking](https://docs.microsoft.com/azure/automation/change-tracking)

* [Przegląd konfiguracji stanu Azure Automation](https://docs.microsoft.com/azure/automation/automation-dsc-overview)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="68-use-only-approved-applications"></a>6,8: Używaj tylko zatwierdzonych aplikacji

**Wskazówki**: obecnie adaptacyjne kontrolki aplikacji nie są dostępne dla Virtual Machine Scale Sets. Używanie oprogramowania innej firmy w celu kontrolowania użycia tylko do zatwierdzonych aplikacji.

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="69-use-only-approved-azure-services"></a>6,9: Używaj tylko zatwierdzonych usług platformy Azure

**Wskazówki**: Użyj usługi Azure Policy, aby wprowadzić ograniczenia dotyczące typu zasobów, które można utworzyć w subskrypcjach klientów, korzystając z następujących wbudowanych definicji zasad:
- Niedozwolone typy zasobów
- Dozwolone typy zasobów

* [Jak skonfigurować Azure Policy i zarządzać nimi](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Jak odmówić określonego typu zasobu za pomocą Azure Policy](https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6,10: przechowywanie spisu zatwierdzonych tytułów oprogramowania

**Wskazówki**: obecnie adaptacyjne kontrolki aplikacji nie są dostępne dla Virtual Machine Scale Sets. Zaimplementuj rozwiązanie innych firm, jeśli nie spełni wymagania organizacji.

* [Jak używać Azure Security Center adaptacyjnych kontroli aplikacji](https://docs.microsoft.com/azure/security-center/security-center-adaptive-application)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: Ogranicz możliwość korzystania przez użytkowników z Azure Resource Manager

**Wskazówki**: Użyj dostępu warunkowego platformy Azure, aby ograniczyć możliwość korzystania przez użytkowników z Azure Resource Manager przez skonfigurowanie "blokowania dostępu" dla aplikacji "Microsoft Azure Management".

* [Jak skonfigurować dostęp warunkowy w celu blokowania dostępu do Azure Resource Manager](https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6,12: Ogranicz możliwość wykonywania skryptów w zasobach obliczeniowych przez użytkowników

**Wskazówki**: w zależności od typu skryptów, można użyć określonych konfiguracji systemu operacyjnego lub zasobów innych firm, aby ograniczyć możliwość wykonywania skryptów w ramach zasobów obliczeniowych platformy Azure.

* [Jak kontrolować wykonywanie skryptów programu PowerShell w środowiskach systemu Windows](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: fizyczne lub logiczne rozdzielenie aplikacji wysokiego ryzyka

**Wskazówki**: aplikacje o wysokim ryzyku wdrożone w środowisku platformy Azure mogą być izolowane przy użyciu sieci wirtualnej, podsieci, subskrypcji, grup zarządzania itp. i dostatecznie zabezpieczone za pomocą zapory platformy Azure, zapory aplikacji sieci Web (WAF) lub sieciowej grupy zabezpieczeń (sieciowej grupy zabezpieczeń).

* [Sieci wirtualne i maszyny wirtualne na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/windows/network-overview)

* [Omówienie Zapory platformy Azure](https://docs.microsoft.com/azure/firewall/overview)

* [Omówienie zapory aplikacji sieci Web](https://docs.microsoft.com/azure/web-application-firewall/overview)

* [Omówienie zabezpieczeń sieci](https://docs.microsoft.com/azure/virtual-network/security-overview)

* [Omówienie usługi Azure Virtual Network](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)

* [Organizowanie zasobów przy użyciu grup zarządzania platformy Azure](https://docs.microsoft.com/azure/governance/management-groups/overview)

* [Przewodnik po decyzjach związanych z subskrypcjami](https://docs.microsoft.com/azure/cloud-adoption-framework/decision-guides/subscriptions/)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

## <a name="secure-configuration"></a>Bezpieczna konfiguracja

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: bezpieczna konfiguracja](/azure/security/benchmarks/security-control-secure-configuration).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: Ustanów bezpieczne konfiguracje dla wszystkich zasobów platformy Azure

**Wskazówki**: Użyj Azure Policy lub Azure Security Center, aby zachować konfiguracje zabezpieczeń dla wszystkich zasobów platformy Azure. Ponadto Azure Resource Manager ma możliwość eksportowania szablonu w JavaScript Object Notation (JSON), który powinien zostać sprawdzony, aby upewnić się, że konfiguracje spełniają/przekroczą wymagania dotyczące zabezpieczeń firmy.

* [Jak skonfigurować Azure Policy i zarządzać nimi](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Informacje na temat pobierania szablonu maszyny wirtualnej](https://docs.microsoft.com/azure/virtual-machines/windows/download-template)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: Ustanów bezpieczne konfiguracje systemów operacyjnych

**Wskazówki**: Użyj Azure Security Center zalecenia [Koryguj luki w zabezpieczeniach w konfiguracjach zabezpieczeń na Virtual Machines], aby zachować konfiguracje zabezpieczeń we wszystkich zasobach obliczeniowych.

* [Jak monitorować zalecenia dotyczące Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-recommendations)

* [Jak skorygować Azure Security Center zalecenia](https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: obsługa bezpiecznych konfiguracji zasobów platformy Azure

**Wskazówki**: użyj szablonów Azure Resource Manager i zasad platformy Azure, aby bezpiecznie skonfigurować zasoby platformy Azure skojarzone z zestawami skalowania Virtual Machines. Szablony Azure Resource Manager są plikami opartymi na notacji JSON służącymi do wdrażania maszyn wirtualnych wraz z zasobami platformy Azure i szablonem niestandardowym. Firma Microsoft przeprowadza konserwację szablonów podstawowych. Użyj zasad platformy Azure [Odmów] i [Wdróż, jeśli nie istnieje], aby wymusić bezpieczne ustawienia dla zasobów platformy Azure.

* [Informacje na temat tworzenia szablonów Azure Resource Manager](https://docs.microsoft.com/azure/virtual-machines/windows/ps-template)

* [Jak skonfigurować Azure Policy i zarządzać nimi](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Zrozumienie Azure Policy efektów](https://docs.microsoft.com/azure/governance/policy/concepts/effects)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: Zachowaj konfiguracje bezpiecznego systemu operacyjnego

**Wskazówki**: istnieje kilka opcji utrzymywania bezpiecznej konfiguracji usługi Azure Virtual Machines (VM) na potrzeby wdrożenia:

1. Szablony Azure Resource Manager: są to pliki w formacie JSON używane do wdrożenia maszyny wirtualnej z Azure Portal, a szablon niestandardowy musi być obsługiwany. Firma Microsoft przeprowadza konserwację szablonów podstawowych.

2. Niestandardowy wirtualny dysk twardy (VHD): w niektórych przypadkach może być wymagane posiadanie niestandardowych plików VHD, takich jak w przypadku złożonych środowisk, które nie mogą być zarządzane za pomocą innych metod.

3. Azure Automation konfiguracja stanu: po wdrożeniu podstawowego systemu operacyjnego może to służyć do bardziej szczegółowego kontrolowania ustawień i wymuszania przez strukturę automatyzacji.

W przypadku większości scenariuszy szablony maszyn wirtualnych firmy Microsoft połączone z konfiguracją żądanego stanu Azure Automation mogą pomóc w spotkaniu i utrzymaniu wymagań w zakresie zabezpieczeń.

* [Informacje na temat pobierania szablonu maszyny wirtualnej](https://docs.microsoft.com/azure/virtual-machines/windows/download-template)

* [Informacje na temat tworzenia szablonów ARM](https://docs.microsoft.com/azure/virtual-machines/windows/ps-template)

* [Jak przekazać niestandardowy wirtualny dysk twardy maszyny wirtualnej na platformę Azure](https://docs.microsoft.com/azure-stack/operator/azure-stack-add-vm-image?view=azs-1910)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: udostępnione

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: bezpiecznie przechowuj konfigurację zasobów platformy Azure

**Wskazówki**: Użyj usługi Azure DevOps, aby bezpiecznie przechowywać kod, taki jak niestandardowe zasady platformy Azure, szablony Azure Resource Manager, skrypty konfiguracji żądanego stanu itp.  Aby uzyskać dostęp do zasobów zarządzanych w usłudze Azure DevOps, takich jak kod, kompilacje i śledzenie pracy, musisz mieć uprawnienia do tych konkretnych zasobów. Większość uprawnień uzyskuje się za poorednictwem wbudowanych grup zabezpieczeń, zgodnie z opisem w temacie uprawnienia i dostęp. Można udzielić lub odmówić uprawnień określonym użytkownikom, wbudowanym grupom zabezpieczeń lub grupom zdefiniowanym w Azure Active Directory (Azure AD) w przypadku integracji z usługą Azure DevOps lub Active Directory w przypadku integracji z programem TFS.

* [Jak przechowywać kod w usłudze Azure DevOps](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

* [Informacje o uprawnieniach i grupach na platformie Azure DevOps](https://docs.microsoft.com/azure/devops/organizations/security/about-permissions)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: bezpieczne przechowywanie niestandardowych obrazów systemu operacyjnego

**Wskazówki**: Jeśli używasz obrazów niestandardowych (np. wirtualnego dysku twardego), użyj kontroli dostępu opartej na rolach (Azure RBAC) na platformie Azure, aby zapewnić dostęp do obrazów tylko autoryzowanym użytkownikom.

* [Opis kontroli RBAC platformy Azure](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles)

* [Jak skonfigurować usługę Azure RBAC](https://docs.microsoft.com/azure/role-based-access-control/quickstart-assign-role-user-portal)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: Wdrażanie narzędzi do zarządzania konfiguracją dla zasobów platformy Azure

**Wskazówki**: wykorzystanie Azure Policy do alertów, inspekcji i wymuszania konfiguracji systemu dla maszyn wirtualnych. Dodatkowo opracowuj proces i potok na potrzeby zarządzania wyjątkami zasad.

* [Jak skonfigurować Azure Policy i zarządzać nimi](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7,8: Wdrażanie narzędzi do zarządzania konfiguracją dla systemów operacyjnych

**Wskazówki**: Azure Automation konfiguracja stanu to usługa zarządzania konfiguracją dla węzłów konfiguracji żądanego stanu (DSC) w dowolnym chmurze lub lokalnym centrum danych. Umożliwia szybkie i łatwe skalowanie na tysiącach maszyn z poziomu centralnej, bezpiecznej lokalizacji. Można łatwo dołączać maszyny, przypisywać do nich konfiguracje deklaracyjne i wyświetlać raporty pokazujące zgodność poszczególnych maszyn z wybranym określonym stanem.

* [Dołączanie maszyn w celu zarządzania przez Azure Automation konfigurację stanu](https://docs.microsoft.com/azure/automation/automation-dsc-onboarding)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: Zaimplementuj automatyczne monitorowanie konfiguracji dla zasobów platformy Azure

**Wskazówki**: skorzystaj z Azure Security Center, aby przeprowadzić skanowanie linii bazowej dla maszyn wirtualnych platformy Azure. Dodatkowe metody konfiguracji automatycznej obejmują używanie Azure Automation konfiguracji stanu.

* [Jak skorygować zalecenia w Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations)

* [Wprowadzenie do konfiguracji stanu Azure Automation](https://docs.microsoft.com/azure/automation/automation-dsc-getting-started)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: Zaimplementuj automatyczne monitorowanie konfiguracji dla systemów operacyjnych

**Wskazówki**: Azure Automation konfiguracja stanu to usługa zarządzania konfiguracją dla węzłów konfiguracji żądanego stanu (DSC) w dowolnym chmurze lub lokalnym centrum danych. Umożliwia szybkie i łatwe skalowanie na tysiącach maszyn z poziomu centralnej, bezpiecznej lokalizacji. Można łatwo dołączać maszyny, przypisywać do nich konfiguracje deklaracyjne i wyświetlać raporty pokazujące zgodność poszczególnych maszyn z wybranym określonym stanem.

* [Dołączanie maszyn w celu zarządzania przez Azure Automation konfigurację stanu](https://docs.microsoft.com/azure/automation/automation-dsc-onboarding)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="711-manage-azure-secrets-securely"></a>7,11: bezpieczne zarządzanie wpisami tajnymi platformy Azure

**Wskazówki**: Użyj tożsamość usługi zarządzanej w połączeniu z Azure Key Vault, aby uprościć i zabezpieczyć poufne Zarządzanie aplikacjami w chmurze.

* [Jak przeprowadzić integrację z tożsamościami zarządzanymi przez platformę Azure](https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity)

* [Jak utworzyć Key Vault](https://docs.microsoft.com/azure/key-vault/quick-create-portal)

* [Jak przeprowadzić uwierzytelnianie w Key Vault](../key-vault/general/authentication.md)

* [Jak przypisać zasady dostępu Key Vault](../key-vault/general/assign-access-policy-portal.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: bezpieczne i automatyczne zarządzanie tożsamościami

**Wskazówki**: używanie tożsamości zarządzanych do świadczenia usług platformy Azure z automatyczną tożsamością zarządzaną w usłudze Azure AD. Tożsamości zarządzane umożliwiają uwierzytelnianie w dowolnej usłudze, która obsługuje uwierzytelnianie usługi Azure AD, w tym Key Vault, bez żadnych poświadczeń w kodzie.

* [Jak skonfigurować tożsamości zarządzane](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: eliminowanie nieprzewidzianego narażenia na poświadczenia

**Wskazówki**: Implementuj skaner poświadczeń, aby identyfikować poświadczenia w kodzie. Skaner poświadczeń zachęca również do przemieszczania odnalezionych poświadczeń do bardziej bezpiecznych lokalizacji, takich jak Azure Key Vault.

* [Jak skonfigurować skaner poświadczeń](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

## <a name="malware-defense"></a>Ochrona przed złośliwym oprogramowaniem

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Obrona złośliwego oprogramowania](/azure/security/benchmarks/security-control-malware-defense).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8,1: Użyj centralnie zarządzanego oprogramowania chroniącego przed złośliwym oprogramowaniem

**Wskazówki**: Usługa Microsoft chroniąca przed złośliwym kodem dla maszyn wirtualnych z systemem Windows Azure może stale monitorować i chronić zasoby. Do ochrony przed złośliwym oprogramowaniem na maszynie wirtualnej platformy Azure z systemem Linux będzie potrzebne narzędzie innej firmy.

* [Jak skonfigurować oprogramowanie chroniące przed złośliwym oprogramowaniem firmy Microsoft dla Cloud Services i Virtual Machines](https://docs.microsoft.com/azure/security/fundamentals/antimalware)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: przeskanuj pliki przed przekazaniem do zasobów platformy Azure, które nie są obliczeniowe

**Wskazówki**: nie dotyczy maszyn wirtualnych platformy Azure, ponieważ jest to zasób obliczeniowy.

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: nie dotyczy

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8,3: Upewnij się, że oprogramowanie chroniące przed złośliwym oprogramowaniem i podpisy zostały zaktualizowane

**Wskazówki**: po wdrożeniu dla maszyn wirtualnych z systemem Windows program Microsoft chroniący przed złośliwym kodem dla platformy Azure automatycznie zainstaluje najnowszą aktualizację sygnatury, platformy i aparatu. Postępuj zgodnie z zaleceniami w Azure Security Center: "COMPUTE &amp; Apps", aby upewnić się, że wszystkie punkty końcowe są aktualne przy użyciu najnowszych sygnatur. System operacyjny Windows może być dodatkowo chroniony przy użyciu dodatkowych zabezpieczeń w celu ograniczenia ryzyka ataków na ataki przez wirusy lub złośliwe oprogramowanie w usłudze Microsoft Defender Advanced Threat Protection, która integruje się z Azure Security Center.

Do ochrony przed złośliwym oprogramowaniem na maszynie wirtualnej platformy Azure z systemem Linux będzie potrzebne narzędzie innej firmy.

* [Jak wdrożyć program Microsoft chroniący przed złośliwym kodem dla platformy Azure Cloud Services i Virtual Machines](https://docs.microsoft.com/azure/security/fundamentals/antimalware)

* [Zaawansowana ochrona przed zagrożeniami w usłudze Microsoft Defender](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/onboard-configure)

* [Jak skonfigurować oprogramowanie chroniące przed złośliwym oprogramowaniem firmy Microsoft dla Cloud Services i Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/linux/security-recommendations)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

## <a name="data-recovery"></a>Odzyskiwanie danych

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: odzyskiwanie danych](/azure/security/benchmarks/security-control-data-recovery).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: zapewnianie regularnych zautomatyzowanych kopii zapasowych

**Wskazówki**: Tworzenie migawki wystąpienia zestawu skalowania maszyn wirtualnych platformy Azure lub dysku zarządzanego dołączonego do wystąpienia przy użyciu programu PowerShell lub interfejsów API REST. Za pomocą Azure Automation można również wykonywać skrypty kopii zapasowych w regularnych odstępach czasu.

* [Jak wykonać migawkę wystąpienia zestawu skalowania maszyn wirtualnych i dysku zarządzanego](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-faq#how-do-i-take-a-snapshot-of-a-virtual-machine-scale-set-instance)

* [Wprowadzenie do Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: wykonaj kompletne kopie zapasowe systemu i Utwórz kopię zapasową wszystkich kluczy zarządzanych przez klienta

**Wskazówki**: tworzenie migawek maszyn wirtualnych platformy Azure lub dysków zarządzanych dołączonych do tych wystąpień przy użyciu programu PowerShell lub interfejsów API REST. Wykonaj kopię zapasową wszystkich kluczy zarządzanych przez klienta w ramach Azure Key Vault.

Włącz Azure Backup i docelowy Virtual Machines platformy Azure, a także żądaną częstotliwość i okres przechowywania. Obejmuje to kompletną kopię zapasową stanu systemu. Jeśli używasz usługi Azure Disk Encryption, kopia zapasowa maszyny wirtualnej platformy Azure automatycznie obsługuje tworzenie kopii zapasowych kluczy zarządzanych przez klienta.

* [Tworzenie kopii zapasowych na maszynach wirtualnych platformy Azure korzystających z szyfrowania](https://docs.microsoft.com/azure/backup/backup-azure-vms-encryption)

* [Omówienie kopii zapasowej maszyny wirtualnej platformy Azure](https://docs.microsoft.com/azure/backup/backup-azure-vms-introduction)

* [Jak wykonać migawkę wystąpienia zestawu skalowania maszyn wirtualnych i dysku zarządzanego](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-faq#how-do-i-take-a-snapshot-of-a-virtual-machine-scale-set-instance)

* [Jak utworzyć kopię zapasową kluczy magazynu kluczy na platformie Azure](https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: Weryfikuj wszystkie kopie zapasowe, w tym klucze zarządzane przez klienta

**Wskazówki**: Zapewnij okresowe wykonywanie przywracania danych z dysku zarządzanego w Azure Backup. W razie potrzeby przetestuj przywracanie zawartości do izolowanej sieci wirtualnej lub subskrypcji. Klient do testowania przywracania kopii zapasowych kluczy zarządzanych przez klienta.

W przypadku korzystania z usługi Azure Disk Encryption można przywrócić zestawy skalowania maszyn wirtualnych przy użyciu kluczy szyfrowania dysku. W przypadku korzystania z szyfrowania dysków można przywrócić maszynę wirtualną platformy Azure z kluczami szyfrowania dysku.

* [Tworzenie kopii zapasowych na maszynach wirtualnych platformy Azure korzystających z szyfrowania](https://docs.microsoft.com/azure/backup/backup-azure-vms-encryption)

* [Przywracanie dysku i tworzenie odzyskanej maszyny wirtualnej na platformie Azure](https://docs.microsoft.com/azure/backup/tutorial-restore-disk)

* [Jak przywrócić klucze magazynu kluczy na platformie Azure](https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

* [Jak włączyć szyfrowanie dysków dla usługi Azure Virtual Machine Scale Sets](https://docs.microsoft.com/azure/virtual-machine-scale-sets/disk-encryption-overview)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: zapewnianie ochrony kopii zapasowych i kluczy zarządzanych przez klienta

**Wskazówki**: Włącz ochronę na dysku zarządzanym przy użyciu blokad. Włącz ochronę Soft-Delete i przeczyszczania w Key Vault, aby chronić klucze przed przypadkowym lub złośliwym usunięciem.

* [Blokowanie zasobów w celu uniemożliwienia nieoczekiwanych zmian](https://docs.microsoft.com/azure/azure-resource-manager/management/lock-resources)

* [Azure Key Vault informacje o ochronie usuwania nietrwałego i przeczyszczania](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

## <a name="incident-response"></a>Reagowanie na zdarzenia

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: odpowiedź na zdarzenia](/azure/security/benchmarks/security-control-incident-response).*

### <a name="101-create-an-incident-response-guide"></a>10,1: Tworzenie przewodnika odpowiedzi na zdarzenia

**Wskazówki**: Tworzenie przewodnika odpowiedzi na zdarzenia dla organizacji. Upewnij się, że istnieją zarejestrowane plany reakcji na zdarzenia, które definiują wszystkie role pracowników, a także etapy obsługi zdarzeń/zarządzania z wykrywania do oceny po zdarzeniu.

* [Wskazówki dotyczące tworzenia własnego procesu reagowania na zdarzenia zabezpieczeń](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [Anatomia incydentu centrum Microsoft Security Response](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

* [Skorzystaj z przewodnika obsługi zdarzeń związanych z bezpieczeństwem programu NIST, aby pomóc w tworzeniu własnego planu reagowania na zdarzenia](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: Tworzenie oceny incydentu i procedury priorytetyzacji

**Wskazówki**: Security Center przypisuje ważność do każdego alertu, aby pomóc w ustaleniu, które alerty należy najpierw zbadać. Ważność jest oparta na tym, jak dobrze Security Center znajduje się w wyszukiwaniu lub analitycznym używanym do wystawiania alertu, a także poziom pewności, że istniało złośliwy wpływ na działanie, które prowadziło do alertu.

Dodatkowo jasno Oznacz subskrypcje (na przykład Produkcja, inne niż prod) przy użyciu tagów i Utwórz system nazewnictwa, aby jasno identyfikować i klasyfikować zasoby platformy Azure, szczególnie te, które przetwarzają dane poufne. Odpowiedzialność za korygowanie alertów zależy od zagrożenia dla zasobów platformy Azure i środowiska, w którym wystąpiło zdarzenie.

* [Alerty zabezpieczeń w Centrum zabezpieczeń Azure](https://docs.microsoft.com/azure/security-center/security-center-alerts-overview)

* [Organizowanie zasobów platformy Azure przy użyciu tagów](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="103-test-security-response-procedures"></a>10,3: procedury odpowiedzi na zabezpieczenia testowe

**Wskazówki**: przeprowadzanie ćwiczeń w celu przetestowania możliwości reagowania na zdarzenia systemów w regularnych erzeach w celu ochrony zasobów platformy Azure. Zidentyfikuj słabe punkty i przerwy i popraw plan zgodnie z wymaganiami.

* [Przewodnik po publikacji NIST, który umożliwia testowanie, uczenie i wykonywanie programów dla planów i możliwości IT](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: Podaj szczegóły kontaktu dotyczącego zabezpieczeń i Skonfiguruj powiadomienia dotyczące alertów dotyczących zdarzeń związanych z zabezpieczeniami

**Wskazówki**: informacje kontaktowe dotyczące zdarzenia zabezpieczeń będą używane przez firmę Microsoft do skontaktowania się z Tobą, jeśli firma Microsoft Security Response Center (MSRC) wykryje, że dostęp do danych zostały nadane przez nielegalną lub nieautoryzowaną stronę. Przejrzyj zdarzenia po fakcie, aby upewnić się, że problemy zostały rozwiązane.

* [Jak ustawić kontakt z zabezpieczeniami Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: Uwzględnij alerty zabezpieczeń w systemie odpowiedzi na zdarzenia

**Wskazówki**: eksportowanie alertów i zaleceń dotyczących Azure Security Center przy użyciu funkcji eksportu ciągłego, która pomaga identyfikować zagrożenia dla zasobów platformy Azure. Eksport ciągły umożliwia wyeksportowanie alertów i zaleceń ręcznie lub w stały sposób ciągły. Możesz użyć łącznika danych Azure Security Center do przesyłania strumieniowego alertów do usługi Azure wskaźnikowej.

* [Jak skonfigurować eksport ciągły](https://docs.microsoft.com/azure/security-center/continuous-export)

* [Jak przesłać strumieniowo alerty do usługi Azure wskaźnikowego](https://docs.microsoft.com/azure/sentinel/connect-azure-security-center)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: Automatyzowanie odpowiedzi na alerty zabezpieczeń

**Wskazówki**: Użyj funkcji automatyzacji przepływu pracy w Azure Security Center, aby automatycznie wyzwalać odpowiedzi poprzez "Logic Apps" dotyczące alertów zabezpieczeń i zaleceń dotyczących ochrony zasobów platformy Azure.

* [Jak skonfigurować automatyzację przepływu pracy i Logic Apps](https://docs.microsoft.com/azure/security-center/workflow-automation)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

## <a name="penetration-tests-and-red-team-exercises"></a>Testy penetracyjne i ćwiczenia typu „red team”

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: testy penetracji i czerwone ćwiczenia zespołu](/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: Przeprowadź regularne testowanie penetracji zasobów platformy Azure i zadbaj o skorygowanie wszystkich krytycznych ustaleń dotyczących zabezpieczeń

**Wskazówki**: Postępuj zgodnie z zasadami firmy Microsoft dotyczącymi zaangażowania, aby upewnić się, że testy penetracji nie naruszają zasad firmy Microsoft. Korzystaj z strategii firmy Microsoft i wykonywania testów na żywo z obsługą tworzenia zespołu, usług i aplikacji w chmurze, które są zarządzane przez firmę Microsoft.

* [Reguły testowania penetracji zaangażowania](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

* [Microsoft Cloud Tworzenie zespołu Red.](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: udostępnione

## <a name="next-steps"></a>Następne kroki

- Zobacz [test porównawczy zabezpieczeń platformy Azure](/azure/security/benchmarks/overview)
- Dowiedz się więcej o [punktach odniesienia zabezpieczeń platformy Azure](/azure/security/benchmarks/security-baselines-overview)
