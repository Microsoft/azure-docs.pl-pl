---
title: Podstawa zabezpieczeń platformy Azure dla Azure Load Balancer
description: Linia bazowa zabezpieczeń Azure Load Balancer zawiera wskazówki i zasoby dotyczące procedur związanych z wdrażaniem zaleceń dotyczących zabezpieczeń określonych w teście zabezpieczeń platformy Azure.
author: msmbaldwin
ms.service: load-balancer
ms.topic: conceptual
ms.date: 09/28/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 07dd369ceeefee85f4d9180a0b9b33a0d4c8bbdb
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94698210"
---
# <a name="azure-security-baseline-for-azure-load-balancer"></a>Podstawa zabezpieczeń platformy Azure dla Azure Load Balancer

Podstawowa baza danych zabezpieczeń Azure dla Microsoft Azure Load Balancer zawiera zalecenia, które pomogą ulepszyć stan bezpieczeństwa wdrożenia. Punkt odniesienia dla tej usługi jest rysowany w [wersji 1,0 usługi Azure Security test](../security/benchmarks/overview.md), która zawiera zalecenia dotyczące sposobu zabezpieczania rozwiązań w chmurze na platformie Azure z naszymi najlepszymi wskazówkami. Aby uzyskać więcej informacji, zobacz [podstawy zabezpieczeń platformy Azure — omówienie](../security/benchmarks/security-baselines-overview.md).

## <a name="network-security"></a>Bezpieczeństwo sieci

*Aby uzyskać więcej informacji, zobacz [wzorzec zabezpieczeń Azure: zabezpieczenia sieci](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: Ochrona zasobów platformy Azure w ramach sieci wirtualnych

**Wskazówki**: Użyj wewnętrznych modułów równoważenia obciążenia platformy Azure, aby zezwalać tylko na ruch z zasobów zaplecza z określonych sieci wirtualnych lub równorzędnych sieci wirtualnych bez narażania się na Internet. Zaimplementuj zewnętrzny Load Balancer z translatorem adresów sieciowych (NAT), aby zamaskowane adresy IP zasobów zaplecza na potrzeby ochrony przed bezpośrednim narażeniem internetowym.

Platforma Azure oferuje dwa typy ofert Load Balancer, w warstwach Standardowa i podstawowa. Użyj usługa Load Balancer w warstwie Standardowa dla wszystkich obciążeń produkcyjnych. Implementowanie sieciowych grup zabezpieczeń i Zezwalanie na dostęp tylko do zaufanych portów i zakresów adresów IP aplikacji. W przypadkach, gdy nie istnieje sieciowa Grupa zabezpieczeń przypisana do podsieci zaplecza lub karty sieciowej zaplecza maszyn wirtualnych, ruch nie będzie dozwolony dla tych zasobów z modułu równoważenia obciążenia. W przypadku standardowych modułów równoważenia obciążenia Podaj reguły ruchu wychodzącego, aby zdefiniować wychodzące NAT z sieciową grupą zabezpieczeń. Przejrzyj te reguły ruchu wychodzącego, aby dostosować zachowanie połączeń wychodzących. 

Użycie usługa Load Balancer w warstwie Standardowa jest zalecane w przypadku obciążeń produkcyjnych, a zazwyczaj podstawowa Load Balancer jest używana tylko do testowania, ponieważ typ podstawowy jest domyślnie otwarty dla połączeń z Internetu i nie wymaga grup zabezpieczeń sieci do działania. 

- [Połączenia wychodzące na platformie Azure](load-balancer-outbound-connections.md)

- [Uaktualnij Load Balancer publicznej platformy Azure](./upgrade-basic-standard.md)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1,2: Monitoruj i Rejestruj konfigurację oraz ruch sieci wirtualnych, podsieci i kart sieciowych

**Wskazówki**: Load Balancer jest usługą przekazującą, ponieważ jest ona zależna od zasad sieciowych grup zabezpieczeń stosowanych do zasobów zaplecza i skonfigurowanych reguł ruchu wychodzącego w celu kontrolowania dostępu do Internetu.

Sprawdź reguły ruchu wychodzącego skonfigurowane dla usługa Load Balancer w warstwie Standardowa za pomocą bloku reguły ruchu wychodzącego Load Balancer i bloku reguły równoważenia obciążenia, w którym można włączyć reguły wychodzące.

Monitoruj liczbę połączeń wychodzących, aby śledzić, jak często zasoby docierają do Internetu. 

Użyj Security Center i postępuj zgodnie z zaleceniami dotyczącymi ochrony sieci, aby pomóc w zabezpieczeniu zasobów sieciowych platformy Azure.

Postępuj zgodnie z zaleceniami dotyczącymi zabezpieczeń zasobów zaplecza i Włącz dzienniki przepływu sieciowych grup zabezpieczeń i Wyślij dzienniki do konta usługi Azure Storage w celu przeprowadzenia inspekcji.

Wyślij również dzienniki przepływów do obszaru roboczego Log Analytics, a następnie użyj Analiza ruchu, aby uzyskać wgląd w wzorce ruchu w chmurze platformy Azure. Zalety Analiza ruchu obejmują możliwość wizualizacji aktywności sieciowej, identyfikowania aktywnych plam i zagrożeń bezpieczeństwa, zrozumienia wzorców przepływu ruchu i wyznaczania konfiguracji sieci.

- [Jak włączyć dzienniki przepływu sieciowych grup zabezpieczeń](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Jak włączyć i używać Analiza ruchu](../network-watcher/traffic-analytics.md)

- [Informacje o zabezpieczeniach sieci zapewnianych przez Azure Security Center](../security-center/security-center-network-recommendations.md)

- [Jak mogę sprawdzić statystykę połączenia wychodzącego](./load-balancer-standard-diagnostics.md#how-do-i-check-my-outbound-connection-statistics)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="13-protect-critical-web-applications"></a>1,3: Ochrona krytycznych aplikacji sieci Web

**Wskazówki**: jawnie Zdefiniuj połączenie internetowe i prawidłowe źródłowe adresy IP za pośrednictwem reguł ruchu wychodzącego i sieciowych grup zabezpieczeń z Load Balancer, aby użyć analizy zagrożeń firmy Microsoft w celu ochrony aplikacji sieci Web.

- [Integrowanie zapory platformy Azure](../firewall/integrate-lb.md)

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
 

- [Zarządzanie standardem Azure DDoS Protection przy użyciu Azure Portal](../ddos-protection/manage-ddos-protection.md)

- [Filtrowanie na podstawie analizy zagrożeń zapory platformy Azure](../firewall/threat-intel.md)

- [Ochrona przed zagrożeniami w usłudze Azure Security Center](../security-center/azure-defender.md)

- [Zabezpieczanie portów zarządzania przy użyciu dostępu just in time](../security-center/security-center-just-in-time.md)

- [Adaptacyjne Zabezpieczanie sieci w Azure Security Center](../security-center/security-center-adaptive-network-hardening.md)

- [Integracja zapory platformy Azure z Load Balancer](../firewall/overview.md)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="15-record-network-packets"></a>1,5: rejestrowanie pakietów sieciowych

**Wskazówki**: Włączanie funkcji przechwytywania pakietów Network Watcher w celu zbadania nietypowych działań.

- [Jak utworzyć wystąpienie Network Watcher](../network-watcher/network-watcher-create.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: wdrażanie opartych na sieci systemów zapobiegania wykrywaniem i dostępem intruzów (identyfikatorów/adresów IP)

**Wskazówki**: implementowanie oferty z poziomu portalu Azure Marketplace, która obsługuje funkcje identyfikatorów/adresów IP z możliwościami inspekcji ładunków w środowisku Load Balancer. 

Użyj analizy zagrożeń zapory platformy Azure, jeśli Inspekcja ładunku nie jest wymagana. Filtrowanie oparte na analizie zagrożeń dla zapory platformy Azure służy do wyzwalania alertów dotyczących i/lub blokowania ruchu do i ze znanych złośliwych adresów IP i domen. Adresy IP i domeny pochodzą z kanału informacyjnego analizy zagrożeń firmy Microsoft.

W celu wykrycia i/lub zablokowania złośliwego ruchu należy wdrożyć wybrane rozwiązanie zapory na wszystkich granicach sieci w organizacji.

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

- [Jak wdrożyć zaporę platformy Azure](../firewall/tutorial-firewall-deploy-portal.md)

- [Jak skonfigurować alerty za pomocą zapory platformy Azure](../firewall/threat-intel.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="17-manage-traffic-to-web-applications"></a>1,7: zarządzanie ruchem do aplikacji sieci Web

**Wskazówki**: jawnie Zdefiniuj połączenie internetowe i prawidłowe źródłowe adresy IP za pośrednictwem reguł ruchu wychodzącego i sieciowych grup zabezpieczeń z Load Balancer, aby chronić aplikacje sieci Web za pomocą funkcji analizy zagrożeń firmy Microsoft.

- [Integrowanie zapory platformy Azure](../firewall/integrate-lb.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: Minimalizacja złożoności i kosztów administracyjnych reguł zabezpieczeń sieci

**Wskazówki**: Użyj tagów usługi zamiast określonych adresów IP podczas tworzenia reguł zabezpieczeń. Określ nazwę tagu usługi w polu źródłowym lub docelowym reguły, aby zezwolić na ruch dla odpowiedniej usługi lub go odrzucić. 

Firma Microsoft zarządza prefiksami adresów, które obejmują tag usługi, i automatycznie aktualizuje tag usługi jako adresy. 

Domyślnie każda sieciowa Grupa zabezpieczeń zawiera tag usługi AzureLoadBalancer, aby zezwolić na ruch sondy kondycji. 

Zapoznaj się z dokumentacją platformy Azure, aby uzyskać wszystkie Tagi usługi dostępne do użycia w regułach sieciowej grupy zabezpieczeń.

- [Dostępne Tagi usług](../virtual-network/service-tags-overview.md#available-service-tags)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: Obsługa standardowych konfiguracji zabezpieczeń dla urządzeń sieciowych

**Wskazówki**: Definiowanie i implementowanie standardowych konfiguracji zabezpieczeń dla zasobów sieciowych za pomocą Azure Policy.

Za pomocą planów platformy Azure można uprościć wdrożenia platformy Azure na dużą skalę przez pakowanie kluczowych artefaktów, takich jak szablony usługi Azure Resources, formanty RBAC platformy Azure i zasady, w ramach jednej definicji planu. 

Zastosuj plan do nowych subskrypcji i Dostosuj kontrolę i zarządzanie przy użyciu wersji.

- [Jak skonfigurować Azure Policy i zarządzać nimi](../governance/policy/tutorials/create-and-manage.md)

- [Przykłady Azure Policy dla sieci](../governance/policy/samples/built-in-policies.md#network)

- [Jak utworzyć Azure Blueprint](../governance/blueprints/create-blueprint-portal.md)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="110-document-traffic-configuration-rules"></a>1,10: udokumentowanie reguł konfiguracji ruchu

**Wskazówki**: Użyj tagów zasobów dla sieciowych grup zabezpieczeń i innych zasobów związanych z zabezpieczeniami sieci i przepływem ruchu. 

Za pomocą pola "opis" można udokumentować reguły zezwalające na ruch do/z sieci dla poszczególnych reguł grupy zabezpieczeń sieci.

Zaimplementuj dowolne wbudowane definicje Azure Policy związane ze znakiem, takie jak "Wymagaj tagu i jego wartości", co gwarantuje, że wszystkie zasoby są tworzone przy użyciu tagów i powiadomienia o wszelkich istniejących nieoznakowanych zasobach.

Użyj Azure PowerShell lub interfejsu wiersza polecenia platformy Azure, aby wyszukiwać lub wykonywać akcje na zasobach na podstawie ich tagów.

- [Tworzenie i używanie tagów](../azure-resource-manager/management/tag-resources.md)

- [Jak utworzyć Virtual Network platformy Azure](../virtual-network/quick-create-portal.md)

- [Jak filtrować ruch sieciowy przy użyciu reguł sieciowej grupy zabezpieczeń](../virtual-network/tutorial-filter-network-traffic.md)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: Użyj zautomatyzowanych narzędzi do monitorowania konfiguracji zasobów sieciowych i wykrywania zmian

**Wskazówki**: Użyj dziennika aktywności platformy Azure do monitorowania konfiguracji zasobów i wykrywania zmian w zasobach platformy Azure. 

Utwórz alerty w Azure Monitor, aby powiadomić Cię o zmianie krytycznych zasobów.

- [Jak wyświetlać i pobierać zdarzenia dziennika aktywności platformy Azure](../azure-monitor/platform/activity-log.md#view-the-activity-log)

- [Jak utworzyć alerty w Azure Monitor](../azure-monitor/platform/alerts-activity-log.md)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

## <a name="logging-and-monitoring"></a>Rejestrowanie i monitorowanie

*Aby uzyskać więcej informacji, zobacz temat [Azure Security test: rejestrowanie i monitorowanie](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2,2: Skonfiguruj centralne zarządzanie dziennikami zabezpieczeń

**Wskazówki**: Przejrzyj zmiany reguł ruchu wychodzącego i sieciowych grup zabezpieczeń dla modułów równoważenia obciążenia, wyświetlając dziennik aktywności w swoich subskrypcjach. 

Zapoznaj się z wewnętrznymi dziennikami hostów, aby upewnić się, że zasoby zaplecza są bezpieczne.

Wyeksportuj te dzienniki do Log Analytics lub innej platformy magazynu. W Azure Monitor używać Log Analytics obszarów roboczych do wykonywania zapytań i przeprowadzania analiz oraz używania kont usługi Azure Storage do przechowywania długoterminowego i archiwizowania.

Włączaj i dołączaj te dane do usługi Azure wskaźnikowej lub SIEM innej firmy na podstawie wymagań firmy obowiązujących w organizacji.

- [Jak dołączyć wskaźnik na platformie Azure](../sentinel/quickstart-onboard.md)

- [Jak zbierać dzienniki platformy i metryki za pomocą Azure Monitor](../azure-monitor/platform/diagnostic-settings.md)

- [Jak zbierać dzienniki wewnętrznego hosta maszyny wirtualnej platformy Azure z Azure Monitor](../azure-monitor/learn/quick-collect-azurevm.md)

- [Jak rozpocząć pracę z usługą Azure Monitor i integracją SIEM innej firmy](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

- [Dzienniki aktywności platformy](../azure-monitor/platform/activity-log.md)

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

- [Zapoznaj się z tym artykułem, wprowadzając instrukcje krok po kroku dla każdej metody szczegółowej w operacjach inspekcji z Menedżer zasobów](../azure-resource-manager/management/view-activity-logs.md)

- [Dzienniki usługi Azure Monitor dla usługi Load Balancer w warstwie Podstawowa](./load-balancer-monitor-log.md)

- [Wyświetlanie dzienników aktywności w celu monitorowania akcji dotyczących zasobów](../azure-resource-manager/management/view-activity-logs.md)

- [Programowe pobieranie metryk wielowymiarowych za pośrednictwem interfejsów API](./load-balancer-standard-diagnostics.md#retrieve-multi-dimensional-metrics-programmatically-via-apis)

- [Jak rozpocząć pracę z usługą Azure Monitor i integracją SIEM innej firmy](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="25-configure-security-log-storage-retention"></a>2,5: Konfigurowanie przechowywania magazynu dzienników zabezpieczeń

**Wskazówki**: Dziennik aktywności jest domyślnie włączony i jest zachowywany przez 90 dni w magazynie dzienników zdarzeń platformy Azure. Ustaw okres przechowywania obszaru roboczego Log Analytics zgodnie z regulacjami zgodności organizacji w programie Azure Monitor. Używaj kont usługi Azure Storage do przechowywania długoterminowego i archiwizowania.

- [Wyświetlanie dzienników aktywności w celu monitorowania akcji w artykule dotyczącym zasobów](../azure-resource-manager/management/view-activity-logs.md)

- [Zmień okres przechowywania danych w Log Analytics](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

- [Jak skonfigurować zasady przechowywania dla dzienników kont usługi Azure Storage](../storage/common/storage-monitor-storage-account.md#configure-logging)

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

- [Sondy kondycji usługi Load Balancer](./load-balancer-custom-probe-overview.md)

- [Interfejs API REST usługi Azure Monitor](/rest/api/monitor)

- [Jak pobrać metryki za pośrednictwem interfejsu API REST](/rest/api/monitor/metrics/list)

- [Diagnostyka usługa Load Balancer w warstwie Standardowa przy użyciu metryk, alertów i kondycji zasobów](./load-balancer-standard-diagnostics.md)

- [Dzienniki usługi Azure Monitor dla usługi Load Balancer w warstwie Podstawowa](./load-balancer-monitor-log.md)

- [Wyświetl metryki modułu równoważenia obciążenia w Azure Portal](./load-balancer-standard-diagnostics.md#view-your-load-balancer-metrics-in-the-azure-portal)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: Włączanie alertów dla nietypowych działań

**Wskazówki**: Użyj Security Center z log Analytics obszarem roboczym do monitorowania i generowania alertów dotyczących nietypowej aktywności związanej z Load Balancer w dziennikach i zdarzeniach zabezpieczeń.

Włącz i Dołącz dane do usługi Azure wskaźnikowej lub narzędzia SIEM innej firmy.

- [Jak dołączyć wskaźnik na platformie Azure](../sentinel/quickstart-onboard.md)

- [Jak zarządzać alertami w Azure Security Center](../security-center/security-center-managing-and-responding-alerts.md)

- [Jak ostrzec dane dziennika usługi log Analytics](../azure-monitor/learn/tutorial-response.md)

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

*Aby uzyskać więcej informacji, zobacz [test dotyczący zabezpieczeń platformy Azure: tożsamość i kontrola dostępu](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: obsługa spisu kont administracyjnych

**Wskazówki**: Kontrola dostępu oparta na rolach (Azure RBAC) umożliwia zarządzanie dostępem do zasobów platformy Azure, takich jak Load Balancer, za pomocą przypisań ról. Przypisz te role do użytkowników, grup podmiotów usługi i tożsamości zarządzanych. 

Wstępnie zdefiniowane i wbudowane role dla niektórych zasobów przy użyciu narzędzi, takich jak interfejs wiersza polecenia platformy Azure, Azure PowerShell lub Azure Portal.

- [Jak uzyskać rolę katalogu w usłudze Azure AD przy użyciu programu PowerShell](/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

- [Jak uzyskać członków roli katalogu w usłudze Azure AD przy użyciu programu PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

## <a name="data-protection"></a>Ochrona danych

*Aby uzyskać więcej informacji, zobacz [Azure Security test: Data Protection](../security/benchmarks/security-control-data-protection.md).*

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4,6: Korzystaj z usługi Azure RBAC, aby zarządzać dostępem do zasobów

**Wskazówki**: Użyj usługi Azure RBAC, aby kontrolować dostęp do zasobów Load Balancer.

- [Jak skonfigurować usługę Azure RBAC](../role-based-access-control/role-assignments-portal.md)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: Wymuś kontrolę dostępu przy użyciu ochrony przed utratą danych opartą na hoście

**Wskazówki**: Load Balancer to usługa przekazująca, która nie przechowuje danych klienta. Jest to część podstawowej platformy zarządzanej przez firmę Microsoft. 

Firma Microsoft traktuje całą zawartość klienta jako poufną i nadaje im dużą długość, aby zapewnić ochronę przed utratą i narażeniem danych przez klienta. 

Aby zapewnić bezpieczeństwo danych klienta na platformie Azure, firma Microsoft wdrożyła i utrzymuje pakiet niezawodnych kontroli i możliwości ochrony danych. 

- [Zrozumienie ochrony danych klientów na platformie Azure](../security/fundamentals/protection-customer-data.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: udostępnione

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: Rejestruj i Ostrzegaj o zmianach krytycznych zasobów platformy Azure

**Wskazówki**: Użyj Azure monitor w dzienniku aktywności platformy Azure, aby utworzyć alerty w przypadku wprowadzenia zmian do krytycznych zasobów platformy Azure, takich jak usługi równoważenia obciążenia używane do ważnych obciążeń produkcyjnych.

- [Jak utworzyć alerty dla zdarzeń dziennika aktywności platformy Azure](../azure-monitor/platform/alerts-activity-log.md)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

## <a name="inventory-and-asset-management"></a>Zarządzanie magazynem i zasobami

*Aby uzyskać więcej informacji, zobacz temat [Azure Security test: Inventory and Asset Management](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: Użyj rozwiązania automatycznego odnajdywania zasobów

**Wskazówki**: usługa Azure Resource Graph umożliwia wykonywanie zapytań dotyczących wszystkich zasobów (np. obliczeń, magazynu, sieci, portów, protokołów itd.) w Twoich subskrypcjach i odnajdywanie ich. Do tworzenia i używania bieżących zasobów zaleca się Azure Resource Manager. 

Upewnij się, że masz odpowiednie uprawnienia (odczyt) w dzierżawie i Wylicz wszystkie subskrypcje i zasoby platformy Azure w swoich subskrypcjach.

- [Jak tworzyć zapytania za pomocą Eksploratora Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

- [Jak wyświetlić subskrypcje platformy Azure](/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

- [Opis kontroli RBAC platformy Azure](../role-based-access-control/overview.md)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="62-maintain-asset-metadata"></a>6,2: Konserwowanie metadanych zasobów

**Wskazówki**: stosowanie tagów do zasobów platformy Azure przy użyciu metadanych w celu logicznego organizowania według taksonomii.

- [Tworzenie i używanie tagów](../azure-resource-manager/management/tag-resources.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: Usuń nieautoryzowane zasoby platformy Azure

**Wskazówki**: używanie tagowania, grup zarządzania i oddzielnych subskrypcji w miarę potrzeb w celu organizowania i śledzenia zasobów. 

Regularnie Uzgadniaj spis i zapewnij, że nieautoryzowane zasoby są usuwane z subskrypcji w odpowiednim czasie.

- [Jak utworzyć dodatkowe subskrypcje platformy Azure](../cost-management-billing/manage/create-subscription.md)

- [Tworzenie grup zarządzania](../governance/management-groups/create-management-group-portal.md)

- [Tworzenie i używanie tagów](../azure-resource-manager/management/tag-resources.md)

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

- [Jak skonfigurować Azure Policy i zarządzać nimi](../governance/policy/tutorials/create-and-manage.md)

- [Jak tworzyć zapytania za pomocą Eksploratora Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: Ogranicz możliwość korzystania przez użytkowników z Azure Resource Manager

**Wskazówki**: korzystanie z dostępu warunkowego usługi Azure AD w celu ograniczenia możliwości współpracy użytkowników z Azure Resource Manager przez skonfigurowanie "blokowania dostępu" dla aplikacji "Microsoft Azure Management".

- [Jak skonfigurować dostęp warunkowy, aby blokować dostęp do usługi Azure Resources](../role-based-access-control/conditional-access-azure-management.md)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: fizyczne lub logiczne rozdzielenie aplikacji wysokiego ryzyka

**Wskazówki**: oprogramowanie, które jest wymagane do działania biznesowego, ale może powodować większe ryzyko dla organizacji, powinno być izolowane w ramach własnej maszyny wirtualnej i/lub sieci wirtualnej i dostatecznie zabezpieczone za pomocą zapory platformy Azure lub sieciowej grupy zabezpieczeń.

- [Jak utworzyć sieć wirtualną](../virtual-network/quick-create-portal.md)

- [Jak utworzyć sieciową grupę zabezpieczeń z konfiguracją zabezpieczeń](../virtual-network/tutorial-filter-network-traffic.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

## <a name="secure-configuration"></a>Bezpieczna konfiguracja

*Aby uzyskać więcej informacji, zobacz temat [Azure Security test: bezpieczna konfiguracja](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: Ustanów bezpieczne konfiguracje dla wszystkich zasobów platformy Azure

**Wskazówki**: Użyj aliasów Azure Policy do tworzenia zasad niestandardowych w celu inspekcji lub wymuszania konfiguracji zasobów platformy Azure. Użyj wbudowanych definicji Azure Policy.

Azure Resource Manager ma możliwość eksportowania szablonu w JavaScript Object Notation (JSON), który powinien zostać sprawdzony w celu upewnienia się, że konfiguracje spełniają wymagania dotyczące zabezpieczeń Twojej organizacji.

Eksportowanie szablonów Azure Resource Manager do formatów JavaScript Object Notation (JSON) i okresowe przeglądanie ich w celu upewnienia się, że konfiguracje spełniają wymagania dotyczące zabezpieczeń organizacji. 

Zaimplementuj zalecenia z Security Center jako bezpieczną konfigurację odniesienia dla zasobów platformy Azure. 

- [Jak wyświetlić dostępne aliasy Azure Policy](/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

- [Samouczek: Tworzenie zasad i zarządzanie nimi w celu wymuszenia zgodności](../governance/policy/tutorials/create-and-manage.md)

- [Eksport jednego i kilku zasobów do szablonu w Azure Portal](../azure-resource-manager/templates/export-template-portal.md)

- [Zalecenia dotyczące zabezpieczeń — przewodnik referencyjny](../security-center/recommendations-reference.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: obsługa bezpiecznych konfiguracji zasobów platformy Azure

**Wskazówki**: Użyj Azure Policy [Odmów] i [Wdróż, jeśli nie istnieje], aby wymusić bezpieczne ustawienia dla zasobów platformy Azure.  Ponadto za pomocą szablonów Azure Resource Manager można zachować konfigurację zabezpieczeń zasobów platformy Azure wymaganą przez organizację. 

- [Zrozumienie efektów Azure Policy](../governance/policy/concepts/effects.md)

- [Tworzenie zasad i zarządzanie nimi w celu wymuszania zgodności](../governance/policy/tutorials/create-and-manage.md)

- [Przegląd szablonów Azure Resource Manager](../azure-resource-manager/templates/overview.md)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: bezpiecznie przechowuj konfigurację zasobów platformy Azure

**Wskazówki**: Użyj usługi Azure DevOps, aby bezpiecznie przechowywać kod, taki jak niestandardowe definicje Azure Policy, szablony Azure Resource Manager i skrypty konfiguracji żądanego stanu, oraz zarządzać nimi. 

Udziel lub Odmów uprawnień określonym użytkownikom, wbudowanym grupom zabezpieczeń lub grupom zdefiniowanym w Azure Active Directory (Azure AD), jeśli jest zintegrowany z usługą Azure DevOps lub w Active Directory, jeśli jest zintegrowany z programem TFS.

- [Jak przechowywać kod w usłudze Azure DevOps](/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [Informacje o uprawnieniach i grupach na platformie Azure DevOps](/azure/devops/organizations/security/about-permissions)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: Wdrażanie narzędzi do zarządzania konfiguracją dla zasobów platformy Azure

**Wskazówki**: Definiowanie i implementowanie standardowych konfiguracji zabezpieczeń dla zasobów platformy Azure przy użyciu Azure Policy.  Użyj aliasów Azure Policy, aby utworzyć zasady niestandardowe do inspekcji lub wymuszania konfiguracji sieci zasobów platformy Azure. Zaimplementuj wbudowane definicje zasad powiązane z konkretnymi zasobami Azure Load Balancer.  Należy również użyć Azure Automation, aby wdrożyć zmiany w konfiguracji. do zasobów Azure Load Balancer.

- [Jak skonfigurować Azure Policy i zarządzać nimi](../governance/policy/tutorials/create-and-manage.md)

- [Jak używać aliasów](../governance/policy/concepts/definition-structure.md#aliases)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: Zaimplementuj automatyczne monitorowanie konfiguracji dla zasobów platformy Azure

**Wskazówki**: Użyj Security Center, aby przeprowadzić skanowanie linii bazowej dla zasobów platformy Azure i Azure Policy do alertów i inspekcji konfiguracji zasobów.

- [Jak skorygować zalecenia w Azure Security Center](../security-center/security-center-remediate-recommendations.md)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

## <a name="incident-response"></a>Reagowanie na zdarzenia

*Aby uzyskać więcej informacji, zobacz temat [Azure Security test: odpowiedź na zdarzenia](../security/benchmarks/security-control-incident-response.md).*

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: Tworzenie oceny incydentu i procedury priorytetyzacji

**Wskazówki**: Security Center przypisuje ważność do każdego alertu, aby pomóc w ustaleniu, które alerty należy najpierw zbadać. 

Ważność jest oparta na tym, jak dobrze Security Center znajduje się w wyszukiwaniu lub analizach używanych do wystawiania alertu, a także poziom pewności, że istniało złośliwe zamiar w odniesieniu do działania, które doprowadziło do alertu.

Oznacz subskrypcje przy użyciu tagów i Utwórz system nazewnictwa, aby identyfikować i klasyfikować zasoby platformy Azure, szczególnie te, które przetwarzają poufne dane.  

Odpowiedzialność za korygowanie alertów zależy od zagrożenia dla zasobów platformy Azure i środowiska, w którym wystąpiło zdarzenie.

- [Alerty zabezpieczeń w Centrum zabezpieczeń Azure](../security-center/security-center-alerts-overview.md)

- [Organizowanie zasobów platformy Azure przy użyciu tagów](../azure-resource-manager/management/tag-resources.md)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: Uwzględnij alerty zabezpieczeń w systemie odpowiedzi na zdarzenia

**Wskazówki**: eksportowanie alertów i zaleceń dotyczących Security Center przy użyciu funkcji eksportu ciągłego, która pomaga identyfikować zagrożenia dla zasobów platformy Azure. 

Używanie funkcji eksportu ciągłego w Security Center, która umożliwia eksportowanie alertów i zaleceń ręcznie lub w stały sposób ciągły. 

Skorzystaj z łącznika danych Security Center, aby przesłać strumieniowo alerty do usługi Azure wskaźnikowej.

- [Jak skonfigurować eksport ciągły](../security-center/continuous-export.md)

- [Jak przesłać strumieniowo alerty do usługi Azure wskaźnikowego](../sentinel/connect-azure-security-center.md)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: Automatyzowanie odpowiedzi na alerty zabezpieczeń

**Wskazówki**: Użyj funkcji automatyzacji przepływu pracy w Security Center, aby automatycznie wyzwalać odpowiedzi na alerty zabezpieczeń i zalecenia dotyczące ochrony zasobów platformy Azure.

- [Jak skonfigurować automatyzację przepływu pracy w usłudze wprowadzania zabezpieczeń](../security-center/workflow-automation.md)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

## <a name="penetration-tests-and-red-team-exercises"></a>Testy penetracyjne i ćwiczenia typu „red team”

*Aby uzyskać więcej informacji, zobacz test [porównawczy zabezpieczeń platformy Azure: testy penetracji i czerwone ćwiczenia zespołu](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: Przeprowadź regularne testowanie penetracji zasobów platformy Azure i zadbaj o skorygowanie wszystkich krytycznych ustaleń dotyczących zabezpieczeń

**Wskazówki**: Postępuj zgodnie z regułami testowania penetracji Microsoft Cloud, aby upewnić się, że testy penetracji nie naruszają zasad firmy Microsoft. Korzystaj z strategii firmy Microsoft i wykonywania testów na żywo z obsługą tworzenia zespołu, usług i aplikacji w chmurze, które są zarządzane przez firmę Microsoft. 

- [Reguły testowania penetracji zaangażowania](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft Cloud Tworzenie zespołu Red.](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: udostępnione

## <a name="next-steps"></a>Następne kroki

- Zobacz [test porównawczy zabezpieczeń platformy Azure](../security/benchmarks/overview.md)
- Dowiedz się więcej o [punktach odniesienia zabezpieczeń platformy Azure](../security/benchmarks/security-baselines-overview.md)