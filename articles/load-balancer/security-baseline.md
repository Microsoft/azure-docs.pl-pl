---
title: Podstawa zabezpieczeń platformy Azure dla Azure Load Balancer
description: Linia bazowa zabezpieczeń Azure Load Balancer zawiera wskazówki i zasoby dotyczące procedur związanych z wdrażaniem zaleceń dotyczących zabezpieczeń określonych w teście zabezpieczeń platformy Azure.
author: msmbaldwin
ms.service: load-balancer
ms.topic: conceptual
ms.date: 09/28/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 6d03f0057e657933f4b0f38c48f2a2cdbe1ba2a4
ms.sourcegitcommit: 541bb46e38ce21829a056da880c1619954678586
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2020
ms.locfileid: "91940331"
---
# <a name="azure-security-baseline-for-azure-load-balancer"></a>Podstawa zabezpieczeń platformy Azure dla Azure Load Balancer

Podstawowa baza danych zabezpieczeń Azure dla Microsoft Azure Load Balancer zawiera zalecenia, które pomogą ulepszyć stan bezpieczeństwa wdrożenia. Punkt odniesienia dla tej usługi jest rysowany w [wersji 1,0 usługi Azure Security test](https://docs.microsoft.com/azure/security/benchmarks/overview), która zawiera zalecenia dotyczące sposobu zabezpieczania rozwiązań w chmurze na platformie Azure z naszymi najlepszymi wskazówkami. Aby uzyskać więcej informacji, zobacz [podstawy zabezpieczeń platformy Azure — omówienie](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview).

## <a name="network-security"></a>Bezpieczeństwo sieci

*Aby uzyskać więcej informacji, zobacz [wzorzec zabezpieczeń Azure: zabezpieczenia sieci](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: Ochrona zasobów platformy Azure w ramach sieci wirtualnych

**Wskazówki**: Użyj wewnętrznych modułów równoważenia obciążenia platformy Azure, aby zezwalać tylko na ruch z zasobów zaplecza z określonych sieci wirtualnych lub równorzędnych sieci wirtualnych bez narażania się na Internet. Zaimplementuj zewnętrzny Load Balancer z translatorem adresów sieciowych (NAT), aby zamaskowane adresy IP zasobów zaplecza na potrzeby ochrony przed bezpośrednim narażeniem internetowym.

Platforma Azure oferuje dwa typy ofert Load Balancer, w warstwach Standardowa i podstawowa. Użyj usługa Load Balancer w warstwie Standardowa dla wszystkich obciążeń produkcyjnych. Implementowanie sieciowych grup zabezpieczeń i Zezwalanie na dostęp tylko do zaufanych portów i zakresów adresów IP aplikacji. W przypadkach, gdy nie istnieje sieciowa Grupa zabezpieczeń przypisana do podsieci zaplecza lub karty sieciowej zaplecza maszyn wirtualnych, ruch nie będzie dozwolony dla tych zasobów z modułu równoważenia obciążenia. W przypadku standardowych modułów równoważenia obciążenia Podaj reguły ruchu wychodzącego, aby zdefiniować wychodzące NAT z sieciową grupą zabezpieczeń. Przejrzyj te reguły ruchu wychodzącego, aby dostosować zachowanie połączeń wychodzących. 

Użycie usługa Load Balancer w warstwie Standardowa jest zalecane w przypadku obciążeń produkcyjnych, a zazwyczaj podstawowa Load Balancer jest używana tylko do testowania, ponieważ typ podstawowy jest domyślnie otwarty dla połączeń z Internetu i nie wymaga grup zabezpieczeń sieci do działania. 

- [Połączenia wychodzące na platformie Azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#outboundrule)

- [Uaktualnij Load Balancer publicznej platformy Azure](https://docs.microsoft.com/azure/load-balancer/upgrade-basic-standard)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1,2: Monitoruj i Rejestruj konfigurację oraz ruch sieci wirtualnych, podsieci i kart sieciowych

**Wskazówki**: Load Balancer jest usługą przekazującą, ponieważ jest ona zależna od zasad sieciowych grup zabezpieczeń stosowanych do zasobów zaplecza i skonfigurowanych reguł ruchu wychodzącego w celu kontrolowania dostępu do Internetu.

Sprawdź reguły ruchu wychodzącego skonfigurowane dla usługa Load Balancer w warstwie Standardowa za pomocą bloku reguły ruchu wychodzącego Load Balancer i bloku reguły równoważenia obciążenia, w którym można włączyć reguły wychodzące.

Monitoruj liczbę połączeń wychodzących, aby śledzić, jak często zasoby docierają do Internetu. 

Użyj Security Center i postępuj zgodnie z zaleceniami dotyczącymi ochrony sieci, aby pomóc w zabezpieczeniu zasobów sieciowych platformy Azure.

Postępuj zgodnie z zaleceniami dotyczącymi zabezpieczeń zasobów zaplecza i Włącz dzienniki przepływu sieciowych grup zabezpieczeń i Wyślij dzienniki do konta usługi Azure Storage w celu przeprowadzenia inspekcji.

Wyślij również dzienniki przepływów do obszaru roboczego Log Analytics, a następnie użyj Analiza ruchu, aby uzyskać wgląd w wzorce ruchu w chmurze platformy Azure. Zalety Analiza ruchu obejmują możliwość wizualizacji aktywności sieciowej, identyfikowania aktywnych plam i zagrożeń bezpieczeństwa, zrozumienia wzorców przepływu ruchu i wyznaczania konfiguracji sieci.

- [Jak włączyć dzienniki przepływu sieciowych grup zabezpieczeń](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)

- [Jak włączyć i używać Analiza ruchu](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)

- [Informacje o zabezpieczeniach sieci zapewnianych przez Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-network-recommendations)

- [Jak mogę sprawdzić statystykę połączenia wychodzącego](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-diagnostics#how-do-i-check-my-outbound-connection-statistics)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="13-protect-critical-web-applications"></a>1,3: Ochrona krytycznych aplikacji sieci Web

**Wskazówki**: jawnie Zdefiniuj połączenie internetowe i prawidłowe źródłowe adresy IP za pośrednictwem reguł ruchu wychodzącego i sieciowych grup zabezpieczeń z Load Balancer, aby użyć analizy zagrożeń firmy Microsoft w celu ochrony aplikacji sieci Web.

- [Integrowanie zapory platformy Azure](https://docs.microsoft.com/azure/firewall/integrate-lb)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: odmowa komunikacji ze znanymi złośliwymi adresami IP

**Wskazówki**: Włącz usługę Azure Distributed odmowa usługi (DDoS) na platformie Virtual Network Azure w celu ochrony przed atakami DDoS. 

Wdróż zaporę platformy Azure w każdej z granic sieci organizacji przy włączonej filtrowaniu opartym na analizie zagrożeń i skonfigurowanym jako "Alert i Odmów" w przypadku złośliwego ruchu sieciowego.

 

Użyj Security Center ochrony przed zagrożeniami, aby wykrywać komunikację ze znanymi złośliwymi adresami IP. 

Usługa Load Balancer w warstwie Standardowa jest zaprojektowana tak, aby była zabezpieczona domyślnie i częścią prywatnych i izolowanych Virtual Network. Jest on zamknięty w przypadku przepływów przychodzących, chyba że są otwierane przez sieciowe grupy zabezpieczeń w celu jawnego zezwolenia na dozwolony ruch i nie zezwalają na znane złośliwe adresy IP. Jeśli sieciowa Grupa zabezpieczeń w podsieci lub karcie sieciowej zasobu maszyny wirtualnej nie istnieje za Load Balancer, ruch nie może nawiązać połączenia z tym zasobem. 

Wdróż zaporę platformy Azure w każdej z granic sieci organizacji z włączonym filtrowaniem opartym na analizie zagrożeń i skonfigurowanym jako "Alert i Odmów" dla złośliwego ruchu sieciowego, aby zapobiec atakom ze złośliwych adresów IP. 

Zaimplementuj wskazówki dotyczące integracji zapory platformy Azure z Load Balancer.

Użyj funkcji Security Center ochrony przed zagrożeniami, aby wykryć komunikację ze znanymi złośliwymi adresami IP. 

Security Center (warstwa standardowa) zapewnia dostęp do maszyny wirtualnej just-in-Time i konfiguruje dozwolone źródłowe adresy IP w celu zezwalania na dostęp tylko z zatwierdzonych adresów IP/zakresów.
 

Aby zalecać konfiguracje grup zabezpieczeń sieci, które ograniczają porty i źródłowe adresy IP na podstawie rzeczywistego ruchu i analizy zagrożeń, należy użyć funkcji adaptacyjnej ochrony sieci Security Center.
 

- [Zarządzanie standardem Azure DDoS Protection przy użyciu Azure Portal](https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection)

- [Filtrowanie na podstawie analizy zagrożeń zapory platformy Azure](https://docs.microsoft.com/azure/firewall/threat-intel)

- [Ochrona przed zagrożeniami w usłudze Azure Security Center](https://docs.microsoft.com/azure/security-center/threat-protection)

- [Zabezpieczanie portów zarządzania przy użyciu dostępu just in Time](https://docs.microsoft.com/azure/security-center/security-center-just-in-time)

- [Adaptacyjne Zabezpieczanie sieci w Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-adaptive-network-hardening)

- [Integracja zapory platformy Azure z Load Balancer](https://docs.microsoft.com/azure/firewall/overview)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="15-record-network-packets"></a>1,5: rejestrowanie pakietów sieciowych

**Wskazówki**: Włączanie funkcji przechwytywania pakietów Network Watcher w celu zbadania nietypowych działań.

- [Jak utworzyć wystąpienie Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-create)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: wdrażanie opartych na sieci systemów zapobiegania wykrywaniem i dostępem intruzów (identyfikatorów/adresów IP)

**Wskazówki**: implementowanie oferty z poziomu portalu Azure Marketplace, która obsługuje funkcje identyfikatorów/adresów IP z możliwościami inspekcji ładunków w środowisku Load Balancer. 

Użyj analizy zagrożeń zapory platformy Azure, jeśli Inspekcja ładunku nie jest wymagana. Filtrowanie oparte na analizie zagrożeń dla zapory platformy Azure służy do wyzwalania alertów dotyczących i/lub blokowania ruchu do i ze znanych złośliwych adresów IP i domen. Adresy IP i domeny pochodzą z kanału informacyjnego analizy zagrożeń firmy Microsoft.

W celu wykrycia i/lub zablokowania złośliwego ruchu należy wdrożyć wybrane rozwiązanie zapory na wszystkich granicach sieci w organizacji.

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

- [Jak wdrożyć zaporę platformy Azure](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal)

- [Jak skonfigurować alerty za pomocą zapory platformy Azure](https://docs.microsoft.com/azure/firewall/threat-intel)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="17-manage-traffic-to-web-applications"></a>1,7: zarządzanie ruchem do aplikacji sieci Web

**Wskazówki**: jawnie Zdefiniuj połączenie internetowe i prawidłowe źródłowe adresy IP za pośrednictwem reguł ruchu wychodzącego i sieciowych grup zabezpieczeń z Load Balancer, aby chronić aplikacje sieci Web za pomocą funkcji analizy zagrożeń firmy Microsoft.

- [Integrowanie zapory platformy Azure](https://docs.microsoft.com/azure/firewall/integrate-lb)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: Minimalizacja złożoności i kosztów administracyjnych reguł zabezpieczeń sieci

**Wskazówki**: Użyj tagów usługi zamiast określonych adresów IP podczas tworzenia reguł zabezpieczeń. Określ nazwę tagu usługi w polu źródłowym lub docelowym reguły, aby zezwolić na ruch dla odpowiedniej usługi lub go odrzucić. 

Firma Microsoft zarządza prefiksami adresów, które obejmują tag usługi, i automatycznie aktualizuje tag usługi jako adresy. 

Domyślnie każda sieciowa Grupa zabezpieczeń zawiera tag usługi AzureLoadBalancer, aby zezwolić na ruch sondy kondycji. 

Zapoznaj się z dokumentacją platformy Azure, aby uzyskać wszystkie Tagi usługi dostępne do użycia w regułach sieciowej grupy zabezpieczeń.

- [Dostępne Tagi usług](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#available-service-tags)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: Obsługa standardowych konfiguracji zabezpieczeń dla urządzeń sieciowych

**Wskazówki**: Definiowanie i implementowanie standardowych konfiguracji zabezpieczeń dla zasobów sieciowych za pomocą Azure Policy.

Za pomocą planów platformy Azure można uprościć wdrożenia platformy Azure na dużą skalę przez pakowanie kluczowych artefaktów, takich jak szablony usługi Azure Resources, formanty RBAC platformy Azure i zasady, w ramach jednej definicji planu. 

Zastosuj plan do nowych subskrypcji i Dostosuj kontrolę i zarządzanie przy użyciu wersji.

- [Jak skonfigurować Azure Policy i zarządzać nimi](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Przykłady Azure Policy dla sieci](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#network)

- [Jak utworzyć Azure Blueprint](https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="110-document-traffic-configuration-rules"></a>1,10: udokumentowanie reguł konfiguracji ruchu

**Wskazówki**: Użyj tagów zasobów dla sieciowych grup zabezpieczeń i innych zasobów związanych z zabezpieczeniami sieci i przepływem ruchu. 

Za pomocą pola "opis" można udokumentować reguły zezwalające na ruch do/z sieci dla poszczególnych reguł grupy zabezpieczeń sieci.

Zaimplementuj dowolne wbudowane definicje Azure Policy związane ze znakiem, takie jak "Wymagaj tagu i jego wartości", co gwarantuje, że wszystkie zasoby są tworzone przy użyciu tagów i powiadomienia o wszelkich istniejących nieoznakowanych zasobach.

Użyj Azure PowerShell lub interfejsu wiersza polecenia platformy Azure, aby wyszukiwać lub wykonywać akcje na zasobach na podstawie ich tagów.

- [Tworzenie i używanie tagów](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

- [Jak utworzyć Virtual Network platformy Azure](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

- [Jak filtrować ruch sieciowy przy użyciu reguł sieciowej grupy zabezpieczeń](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: Użyj zautomatyzowanych narzędzi do monitorowania konfiguracji zasobów sieciowych i wykrywania zmian

**Wskazówki**: Użyj dziennika aktywności platformy Azure do monitorowania konfiguracji zasobów i wykrywania zmian w zasobach platformy Azure. 

Utwórz alerty w Azure Monitor, aby powiadomić Cię o zmianie krytycznych zasobów.

- [Jak wyświetlać i pobierać zdarzenia dziennika aktywności platformy Azure](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view)

- [Jak utworzyć alerty w Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

## <a name="logging-and-monitoring"></a>Rejestrowanie i monitorowanie

*Aby uzyskać więcej informacji, zobacz temat [Azure Security test: rejestrowanie i monitorowanie](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="22-configure-central-security-log-management"></a>2,2: Skonfiguruj centralne zarządzanie dziennikami zabezpieczeń

**Wskazówki**: Przejrzyj zmiany reguł ruchu wychodzącego i sieciowych grup zabezpieczeń dla modułów równoważenia obciążenia, wyświetlając dziennik aktywności w swoich subskrypcjach. 

Zapoznaj się z wewnętrznymi dziennikami hostów, aby upewnić się, że zasoby zaplecza są bezpieczne.

Wyeksportuj te dzienniki do Log Analytics lub innej platformy magazynu. W Azure Monitor używać Log Analytics obszarów roboczych do wykonywania zapytań i przeprowadzania analiz oraz używania kont usługi Azure Storage do przechowywania długoterminowego i archiwizowania.

Włączaj i dołączaj te dane do usługi Azure wskaźnikowej lub SIEM innej firmy na podstawie wymagań firmy obowiązujących w organizacji.

- [Jak dołączyć wskaźnik na platformie Azure](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

- [Jak zbierać dzienniki platformy i metryki za pomocą Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings)

- [Jak zbierać dzienniki wewnętrznego hosta maszyny wirtualnej platformy Azure z Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/learn/quick-collect-azurevm)

- [Jak rozpocząć pracę z usługą Azure Monitor i integracją SIEM innej firmy](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

- [Dzienniki aktywności platformy](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: Włączanie rejestrowania inspekcji dla zasobów platformy Azure

**Wskazówki**: Przejrzyj informacje o rejestrowaniu i inspekcji płaszczyzny zarządzania i przechwyceniu z dziennikami aktywności dla podstawowego Load Balancer. Te ustawienia przechwytywania są domyślnie włączone. 

Za pomocą dzienników aktywności można monitorować akcje na zasobach w celu wyświetlenia wszystkich działań i ich stanu. 

Określ, jakie operacje zostały wykonane względem zasobów w ramach subskrypcji z dziennikami aktywności: 

- kto uruchomił operację
- gdy wystąpiła operacja
- stan operacji

- wartości innych właściwości, które mogą pomóc w zbadaniu operacji

Pobierz informacje z dziennika aktywności za pośrednictwem Azure PowerShell, interfejsu wiersza polecenia platformy Azure, interfejsu API REST platformy Azure lub Azure Portal. 

Zaimplementuj wielowymiarową diagnostykę dla możliwości konfiguracji usługa Load Balancer w warstwie Standardowa z Azure Monitor.  Obejmują one dostępne metryki dotyczące zabezpieczeń, które zawierają informacje o połączeniach z translatorem adresów sieciowych (NAT), porty. Dostępne są również dodatkowe metryki dotyczące pakietów SYN (synch) i liczników pakietów. 

Pobierz metryki wielowymiarowe programowo za pośrednictwem interfejsów API i Zapisz je na koncie magazynu za pomocą opcji "wszystkie metryki".

Pakiet zawartości dzienników inspekcji platformy Azure w usłudze Microsoft Power BI umożliwia przeanalizowanie danych przy użyciu wstępnie skonfigurowanych pulpitów nawigacyjnych lub dostosowanie widoków zgodnie z wymaganiami.

Przesyłanie strumieniowe dzienników do centrum zdarzeń lub obszaru roboczego Log Analytics. Można je również wyodrębnić z usługi Azure Blob Storage i przeglądać w różnych narzędziach, takich jak Excel i Power BI. 

Włączaj i dołączaj dane do produktów wskaźnikowych platformy Azure lub SIEM innych firm zgodnie z wymaganiami biznesowymi.

- [Zapoznaj się z tym artykułem, wprowadzając instrukcje krok po kroku dla każdej metody szczegółowej w operacjach inspekcji z Menedżer zasobów](https://docs.microsoft.com/azure/azure-resource-manager/management/view-activity-logs)

- [Dzienniki usługi Azure Monitor dla usługi Load Balancer w warstwie Podstawowa](https://docs.microsoft.com/azure/load-balancer/load-balancer-monitor-log)

- [Wyświetlanie dzienników aktywności w celu monitorowania akcji dotyczących zasobów](https://docs.microsoft.com/azure/azure-resource-manager/management/view-activity-logs)

- [Programowe pobieranie metryk wielowymiarowych za pośrednictwem interfejsów API](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-diagnostics#retrieve-multi-dimensional-metrics-programmatically-via-apis)

- [Jak rozpocząć pracę z usługą Azure Monitor i integracją SIEM innej firmy](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="25-configure-security-log-storage-retention"></a>2,5: Konfigurowanie przechowywania magazynu dzienników zabezpieczeń

**Wskazówki**: Dziennik aktywności jest domyślnie włączony i jest zachowywany przez 90 dni w magazynie dzienników zdarzeń platformy Azure. Ustaw okres przechowywania obszaru roboczego Log Analytics zgodnie z regulacjami zgodności organizacji w programie Azure Monitor. Używaj kont usługi Azure Storage do przechowywania długoterminowego i archiwizowania.

- [Wyświetlanie dzienników aktywności w celu monitorowania akcji w artykule dotyczącym zasobów](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit)

- [Zmień okres przechowywania danych w Log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

- [Jak skonfigurować zasady przechowywania dla dzienników kont usługi Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-monitor-storage-account#configure-logging)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="26-monitor-and-review-logs"></a>2,6: dzienniki monitorowania i przeglądania

**Wskazówki**: monitorowanie i rozwiązywanie problemów usługa Load Balancer w warstwie Standardowa zasobów oraz zarządzanie nimi za pomocą strony Load Balancer Azure Portal oraz strony Resource Health w obszarze Azure monitor. Dostępne metryki dotyczące zabezpieczeń zawierają informacje o połączeniach z translatorem adresów sieciowych (NAT) źródła. Dodatkowo dostępne są również metryki dotyczące pakietów SYN (Synchronize) i liczników pakietów. 

Użyj Azure Monitor, aby sprawdzić stan sondy kondycji punktu końcowego za pomocą wielowymiarowych metryk dla usług w warstwie Standardowa, zewnętrzna i wewnętrzna. Można programowo zbierać te metryki za pośrednictwem interfejsów API i zapisywać je na koncie magazynu za pomocą opcji "wszystkie metryki".

Dzienniki Azure Monitor nie są dostępne dla wewnętrznych podstawowych modułów równoważenia obciążenia. 

Użyj Azure Monitor, aby zobaczyć stan sondy kondycji podsumowany dla puli zaplecza dla podstawowych Load Balancer zewnętrznych, takich jak liczba wystąpień w puli zaplecza, które nie otrzymują żądań z Load Balancer z powodu błędów sondy kondycji. 

Zaimplementuj rejestrowanie w usłudze Azure Operational Insights, aby zapewnić statystykę stanu kondycji modułu równoważenia obciążenia. 

Korzystając z dzienników aktywności, można wyświetlać alerty i monitorować akcje dotyczące zasobów oraz ich stan w subskrypcjach platformy Azure. Dzienniki aktywności są domyślnie włączone i można je wyświetlić w Azure Portal. 

Użyj programu Microsoft Power BI z pakietem zawartości dzienników inspekcji platformy Azure i analizuj dane przy użyciu wstępnie skonfigurowanych pulpitów nawigacyjnych. Dostosuj widoki zgodnie z potrzebami biznesowymi. 

Przesyłanie strumieniowe dzienników do centrum zdarzeń lub obszaru roboczego Log Analytics. Można je również wyodrębnić z usługi Azure Blob Storage i przeglądać w różnych narzędziach, takich jak Excel i Power BI. Dane można włączać i dołączać do produktów wskaźnikowych platformy Azure lub SIEM innych firm.

- [Sondy kondycji usługi Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview)

- [Interfejs API REST usługi Azure Monitor](https://docs.microsoft.com/rest/api/monitor)

- [Jak pobrać metryki za pośrednictwem interfejsu API REST](https://docs.microsoft.com/rest/api/monitor/metrics/list)

- [Diagnostyka usługa Load Balancer w warstwie Standardowa przy użyciu metryk, alertów i kondycji zasobów](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-diagnostics)

- [Dzienniki usługi Azure Monitor dla usługi Load Balancer w warstwie Podstawowa](https://docs.microsoft.com/azure/load-balancer/load-balancer-monitor-log)

- [Wyświetl metryki modułu równoważenia obciążenia w Azure Portal](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-diagnostics#view-your-load-balancer-metrics-in-the-azure-portal)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: Włączanie alertów dla nietypowych działań

**Wskazówki**: Użyj Security Center z log Analytics obszarem roboczym do monitorowania i generowania alertów dotyczących nietypowej aktywności związanej z Load Balancer w dziennikach i zdarzeniach zabezpieczeń.

Włącz i Dołącz dane do usługi Azure wskaźnikowej lub narzędzia SIEM innej firmy.

- [Jak dołączyć wskaźnik na platformie Azure](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

- [Jak zarządzać alertami w Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts)

- [Jak ostrzec dane dziennika usługi log Analytics](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="28-centralize-anti-malware-logging"></a>2,8: scentralizowanie rejestrowania chroniącego przed złośliwym oprogramowaniem

**Wskazówki**: nie mają zastosowania do Azure Load Balancer. To zalecenie jest przeznaczone do zasobów obliczeniowych.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="29-enable-dns-query-logging"></a>2,9: Włączanie rejestrowania zapytań DNS

**Wskazówki**: nie dotyczy Azure Load Balancer jest podstawową usługą sieciową, która nie wykonuje zapytań DNS.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="210-enable-command-line-audit-logging"></a>2,10: Włączanie rejestrowania inspekcji w wierszu polecenia

**Wskazówki**: nie mają zastosowania do Azure Load Balancer, ponieważ to zalecenie dotyczy zasobów obliczeniowych.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

## <a name="identity-and-access-control"></a>Tożsamość i kontrola dostępu

*Aby uzyskać więcej informacji, zobacz [test dotyczący zabezpieczeń platformy Azure: tożsamość i kontrola dostępu](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: obsługa spisu kont administracyjnych

**Wskazówki**: Kontrola dostępu oparta na rolach (Azure RBAC) umożliwia zarządzanie dostępem do zasobów platformy Azure, takich jak Load Balancer, za pomocą przypisań ról. Przypisz te role do użytkowników, grup podmiotów usługi i tożsamości zarządzanych. 

Wstępnie zdefiniowane i wbudowane role dla niektórych zasobów przy użyciu narzędzi, takich jak interfejs wiersza polecenia platformy Azure, Azure PowerShell lub Azure Portal.

- [Jak uzyskać rolę katalogu w usłudze Azure AD przy użyciu programu PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

- [Jak uzyskać członków roli katalogu w usłudze Azure AD przy użyciu programu PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

## <a name="data-protection"></a>Ochrona danych

*Aby uzyskać więcej informacji, zobacz [Azure Security test: Data Protection](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection).*

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4,6: Korzystaj z usługi Azure RBAC, aby zarządzać dostępem do zasobów

**Wskazówki**: Użyj usługi Azure RBAC, aby kontrolować dostęp do zasobów Load Balancer.

- [Jak skonfigurować RBAC na platformie Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: Wymuś kontrolę dostępu przy użyciu ochrony przed utratą danych opartą na hoście

**Wskazówki**: Load Balancer to usługa przekazująca, która nie przechowuje danych klienta. Jest to część podstawowej platformy zarządzanej przez firmę Microsoft. 

Firma Microsoft traktuje całą zawartość klienta jako poufną i nadaje im dużą długość, aby zapewnić ochronę przed utratą i narażeniem danych przez klienta. 

Aby zapewnić bezpieczeństwo danych klienta na platformie Azure, firma Microsoft wdrożyła i utrzymuje pakiet niezawodnych kontroli i możliwości ochrony danych. 

- [Zrozumienie ochrony danych klientów na platformie Azure](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: udostępnione

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: Rejestruj i Ostrzegaj o zmianach krytycznych zasobów platformy Azure

**Wskazówki**: Użyj Azure monitor w dzienniku aktywności platformy Azure, aby utworzyć alerty w przypadku wprowadzenia zmian do krytycznych zasobów platformy Azure, takich jak usługi równoważenia obciążenia używane do ważnych obciążeń produkcyjnych.

- [Jak utworzyć alerty dla zdarzeń dziennika aktywności platformy Azure](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

## <a name="inventory-and-asset-management"></a>Zarządzanie magazynem i zasobami

*Aby uzyskać więcej informacji, zobacz temat [Azure Security test: Inventory and Asset Management](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: Użyj rozwiązania automatycznego odnajdywania zasobów

**Wskazówki**: usługa Azure Resource Graph umożliwia wykonywanie zapytań dotyczących wszystkich zasobów (np. obliczeń, magazynu, sieci, portów, protokołów itd.) w Twoich subskrypcjach i odnajdywanie ich. Do tworzenia i używania bieżących zasobów zaleca się Azure Resource Manager. 

Upewnij się, że masz odpowiednie uprawnienia (odczyt) w dzierżawie i Wylicz wszystkie subskrypcje i zasoby platformy Azure w swoich subskrypcjach.

- [Jak tworzyć zapytania za pomocą Eksploratora Azure Resource Graph](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

- [Jak wyświetlić subskrypcje platformy Azure](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

- [Opis kontroli RBAC platformy Azure](https://docs.microsoft.com/azure/role-based-access-control/overview)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="62-maintain-asset-metadata"></a>6,2: Konserwowanie metadanych zasobów

**Wskazówki**: stosowanie tagów do zasobów platformy Azure przy użyciu metadanych w celu logicznego organizowania według taksonomii.

- [Tworzenie i używanie tagów](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: Usuń nieautoryzowane zasoby platformy Azure

**Wskazówki**: używanie tagowania, grup zarządzania i oddzielnych subskrypcji w miarę potrzeb w celu organizowania i śledzenia zasobów. 

Regularnie Uzgadniaj spis i zapewnij, że nieautoryzowane zasoby są usuwane z subskrypcji w odpowiednim czasie.

- [Jak utworzyć dodatkowe subskrypcje platformy Azure](https://docs.microsoft.com/azure/billing/billing-create-subscription)

- [Tworzenie grup zarządzania](https://docs.microsoft.com/azure/governance/management-groups/create)

- [Tworzenie i używanie tagów](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6,4: Definiowanie i obsługa spisu zatwierdzonych zasobów platformy Azure

**Wskazówki**: Utwórz listę zatwierdzonych zasobów platformy Azure zgodnie z potrzebami organizacji, których możesz użyć jako mechanizmu listy dozwolonych. Dzięki temu Twoja organizacja będzie mogła dołączyć wszelkie nowo dostępne usługi platformy Azure po ich formalnej ocenie i zaakceptowaniu przez typowe procesy oceny zabezpieczeń w organizacji.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: Monitoruj niezatwierdzone zasoby platformy Azure

**Wskazówki**: Użyj Azure Policy, aby wprowadzić ograniczenia dotyczące typu zasobów, które mogą być tworzone w ramach subskrypcji.

Wysyłaj zapytania o zasoby i odnajduj je za pomocą usługi Azure Resource Graph w ramach subskrypcji będących własnością. 

Upewnij się, że wszystkie zasoby platformy Azure obecne w środowisku zostały zatwierdzone.

- [Jak skonfigurować Azure Policy i zarządzać nimi](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Jak tworzyć zapytania za pomocą Eksploratora Azure Resource Graph](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: Ogranicz możliwość korzystania przez użytkowników z Azure Resource Manager

**Wskazówki**: korzystanie z dostępu warunkowego usługi Azure AD w celu ograniczenia możliwości współpracy użytkowników z Azure Resource Manager przez skonfigurowanie "blokowania dostępu" dla aplikacji "Microsoft Azure Management".

- [Jak skonfigurować dostęp warunkowy, aby blokować dostęp do usługi Azure Resources](https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: fizyczne lub logiczne rozdzielenie aplikacji wysokiego ryzyka

**Wskazówki**: oprogramowanie, które jest wymagane do działania biznesowego, ale może powodować większe ryzyko dla organizacji, powinno być izolowane w ramach własnej maszyny wirtualnej i/lub sieci wirtualnej i dostatecznie zabezpieczone za pomocą zapory platformy Azure lub sieciowej grupy zabezpieczeń.

- [Jak utworzyć sieć wirtualną](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

- [Jak utworzyć sieciową grupę zabezpieczeń z konfiguracją zabezpieczeń](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

## <a name="secure-configuration"></a>Bezpieczna konfiguracja

*Aby uzyskać więcej informacji, zobacz temat [Azure Security test: bezpieczna konfiguracja](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: Ustanów bezpieczne konfiguracje dla wszystkich zasobów platformy Azure

**Wskazówki**: Użyj aliasów Azure Policy do tworzenia zasad niestandardowych w celu inspekcji lub wymuszania konfiguracji zasobów platformy Azure. Użyj wbudowanych definicji Azure Policy.

Azure Resource Manager ma możliwość eksportowania szablonu w JavaScript Object Notation (JSON), który powinien zostać sprawdzony w celu upewnienia się, że konfiguracje spełniają wymagania dotyczące zabezpieczeń Twojej organizacji.

Eksportowanie szablonów Azure Resource Manager do formatów JavaScript Object Notation (JSON) i okresowe przeglądanie ich w celu upewnienia się, że konfiguracje spełniają wymagania dotyczące zabezpieczeń organizacji. 

Zaimplementuj zalecenia z Security Center jako bezpieczną konfigurację odniesienia dla zasobów platformy Azure. 

- [Jak wyświetlić dostępne aliasy Azure Policy](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

- [Samouczek: Tworzenie zasad i zarządzanie nimi w celu wymuszenia zgodności](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Eksport jednego i kilku zasobów do szablonu w Azure Portal](https://docs.microsoft.com/azure/azure-resource-manager/templates/export-template-portal)

- [Zalecenia dotyczące zabezpieczeń — Przewodnik referencyjny](https://docs.microsoft.com/azure/security-center/recommendations-reference)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: obsługa bezpiecznych konfiguracji zasobów platformy Azure

**Wskazówki**: Użyj Azure Policy [Odmów] i [Wdróż, jeśli nie istnieje], aby wymusić bezpieczne ustawienia dla zasobów platformy Azure.  Ponadto za pomocą szablonów Azure Resource Manager można zachować konfigurację zabezpieczeń zasobów platformy Azure wymaganą przez organizację. 

- [Zrozumienie efektów Azure Policy](https://docs.microsoft.com/azure/governance/policy/concepts/effects)

- [Tworzenie zasad i zarządzanie nimi w celu wymuszania zgodności](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Przegląd szablonów Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/templates/overview)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: bezpiecznie przechowuj konfigurację zasobów platformy Azure

**Wskazówki**: Użyj usługi Azure DevOps, aby bezpiecznie przechowywać kod, taki jak niestandardowe definicje Azure Policy, szablony Azure Resource Manager i skrypty konfiguracji żądanego stanu, oraz zarządzać nimi. 

Udziel lub Odmów uprawnień określonym użytkownikom, wbudowanym grupom zabezpieczeń lub grupom zdefiniowanym w Azure Active Directory (Azure AD), jeśli jest zintegrowany z usługą Azure DevOps lub w Active Directory, jeśli jest zintegrowany z programem TFS.

- [Jak przechowywać kod w usłudze Azure DevOps](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [Informacje o uprawnieniach i grupach na platformie Azure DevOps](https://docs.microsoft.com/azure/devops/organizations/security/about-permissions)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: Wdrażanie narzędzi do zarządzania konfiguracją dla zasobów platformy Azure

**Wskazówki**: Definiowanie i implementowanie standardowych konfiguracji zabezpieczeń dla zasobów platformy Azure przy użyciu Azure Policy.  Użyj aliasów Azure Policy, aby utworzyć zasady niestandardowe do inspekcji lub wymuszania konfiguracji sieci zasobów platformy Azure. Zaimplementuj wbudowane definicje zasad powiązane z konkretnymi zasobami Azure Load Balancer.  Należy również użyć Azure Automation, aby wdrożyć zmiany w konfiguracji. do zasobów Azure Load Balancer.

- [Jak skonfigurować Azure Policy i zarządzać nimi](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Jak używać aliasów](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure#aliases)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: Zaimplementuj automatyczne monitorowanie konfiguracji dla zasobów platformy Azure

**Wskazówki**: Użyj Security Center, aby przeprowadzić skanowanie linii bazowej dla zasobów platformy Azure i Azure Policy do alertów i inspekcji konfiguracji zasobów.

- [Jak skorygować zalecenia w Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

## <a name="incident-response"></a>Reagowanie na zdarzenia

*Aby uzyskać więcej informacji, zobacz temat [Azure Security test: odpowiedź na zdarzenia](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response).*

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: Tworzenie oceny incydentu i procedury priorytetyzacji

**Wskazówki**: Security Center przypisuje ważność do każdego alertu, aby pomóc w ustaleniu, które alerty należy najpierw zbadać. 

Ważność jest oparta na tym, jak dobrze Security Center znajduje się w wyszukiwaniu lub analizach używanych do wystawiania alertu, a także poziom pewności, że istniało złośliwe zamiar w odniesieniu do działania, które doprowadziło do alertu.

Oznacz subskrypcje przy użyciu tagów i Utwórz system nazewnictwa, aby identyfikować i klasyfikować zasoby platformy Azure, szczególnie te, które przetwarzają poufne dane.  

Odpowiedzialność za korygowanie alertów zależy od zagrożenia dla zasobów platformy Azure i środowiska, w którym wystąpiło zdarzenie.

- [Alerty zabezpieczeń w Centrum zabezpieczeń Azure](https://docs.microsoft.com/azure/security-center/security-center-alerts-overview)

- [Organizowanie zasobów platformy Azure przy użyciu tagów](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: Uwzględnij alerty zabezpieczeń w systemie odpowiedzi na zdarzenia

**Wskazówki**: eksportowanie alertów i zaleceń dotyczących Security Center przy użyciu funkcji eksportu ciągłego, która pomaga identyfikować zagrożenia dla zasobów platformy Azure. 

Używanie funkcji eksportu ciągłego w Security Center, która umożliwia eksportowanie alertów i zaleceń ręcznie lub w stały sposób ciągły. 

Skorzystaj z łącznika danych Security Center, aby przesłać strumieniowo alerty do usługi Azure wskaźnikowej.

- [Jak skonfigurować eksport ciągły](https://docs.microsoft.com/azure/security-center/continuous-export)

- [Jak przesłać strumieniowo alerty do usługi Azure wskaźnikowego](https://docs.microsoft.com/azure/sentinel/connect-azure-security-center)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: Automatyzowanie odpowiedzi na alerty zabezpieczeń

**Wskazówki**: Użyj funkcji automatyzacji przepływu pracy w Security Center, aby automatycznie wyzwalać odpowiedzi na alerty zabezpieczeń i zalecenia dotyczące ochrony zasobów platformy Azure.

- [Jak skonfigurować automatyzację przepływu pracy w usłudze wprowadzania zabezpieczeń](https://docs.microsoft.com/azure/security-center/workflow-automation)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

## <a name="penetration-tests-and-red-team-exercises"></a>Testy penetracyjne i ćwiczenia typu „red team”

*Aby uzyskać więcej informacji, zobacz test [porównawczy zabezpieczeń platformy Azure: testy penetracji i czerwone ćwiczenia zespołu](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: Przeprowadź regularne testowanie penetracji zasobów platformy Azure i zadbaj o skorygowanie wszystkich krytycznych ustaleń dotyczących zabezpieczeń

**Wskazówki**: Postępuj zgodnie z regułami testowania penetracji Microsoft Cloud, aby upewnić się, że testy penetracji nie naruszają zasad firmy Microsoft. Korzystaj z strategii firmy Microsoft i wykonywania testów na żywo z obsługą tworzenia zespołu, usług i aplikacji w chmurze, które są zarządzane przez firmę Microsoft. 

- [Reguły testowania penetracji zaangażowania](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft Cloud Tworzenie zespołu Red.](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: udostępnione

## <a name="next-steps"></a>Następne kroki

- Zobacz [test porównawczy zabezpieczeń platformy Azure](/azure/security/benchmarks/overview)
- Dowiedz się więcej o [punktach odniesienia zabezpieczeń platformy Azure](/azure/security/benchmarks/security-baselines-overview)
