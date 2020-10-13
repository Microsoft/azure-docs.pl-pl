---
title: Podstawa zabezpieczeń platformy Azure dla Azure Automation
description: Podstawa zabezpieczeń platformy Azure dla usługi Automation
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 06/22/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: a57473a26d5fe809274f14c8767356914e0d4962
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89400521"
---
# <a name="azure-security-baseline-for-automation"></a>Podstawa zabezpieczeń platformy Azure dla usługi Automation

Podstawą zabezpieczeń platformy Azure dla usługi Automation są zalecenia, które pomogą ulepszyć stan bezpieczeństwa wdrożenia.

Punkt odniesienia dla tej usługi jest rysowany w [wersji 1,0 usługi Azure Security test](../security/benchmarks/overview.md), która zawiera zalecenia dotyczące sposobu zabezpieczania rozwiązań w chmurze na platformie Azure z naszymi najlepszymi wskazówkami.

Aby uzyskać więcej informacji, zobacz [podstawy zabezpieczeń platformy Azure — omówienie](../security/benchmarks/security-baselines-overview.md).

## <a name="network-security"></a>Bezpieczeństwo sieci

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: zabezpieczenia sieci](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: Ochrona zasobów platformy Azure w ramach sieci wirtualnych

**Wskazówki**: konto Azure Automation nie obsługuje jeszcze prywatnego linku platformy Azure do ograniczania dostępu do usługi za pomocą prywatnych punktów końcowych. Elementy Runbook, które uwierzytelniają i uruchamiają się w odniesieniu do zasobów platformy Azure, działają w piaskownicy platformy Azure oraz wykorzystują udostępnione zasoby zaplecza, które firma Microsoft jest odpowiedzialna za izolowanie od siebie. ich sieci są nieograniczone i mogą uzyskiwać dostęp do zasobów publicznych. Azure Automation obecnie nie ma integracji sieci wirtualnej dla sieci prywatnej poza obsługą hybrydowych procesów roboczych elementów Runbook. Ta kontrolka nie ma zastosowania, jeśli używasz wbudowanej usługi Box bez hybrydowych procesów roboczych elementów Runbook.

Aby zapewnić lepszą izolację elementów Runbook, można używać hybrydowych procesów roboczych elementów Runbook uruchomionych na maszynach wirtualnych platformy Azure. Podczas tworzenia maszyny wirtualnej platformy Azure należy utworzyć sieć wirtualną lub użyć istniejącej sieci wirtualnej i skonfigurować maszyny wirtualne z podsiecią. Upewnij się, że we wszystkich wdrożonych podsieciach została zastosowana sieciowa Grupa zabezpieczeń z kontrolkami dostępu do sieci, które są specyficzne dla zaufanych portów i źródeł aplikacji. Aby uzyskać wymagania dotyczące usługi, zapoznaj się z zaleceniami dotyczącymi zabezpieczeń tej konkretnej usługi.

Alternatywnie, jeśli masz określone wymaganie, można również użyć zapory platformy Azure do jej spełnienia.

* [Sieci wirtualne i maszyny wirtualne na platformie Azure](../virtual-machines/network-overview.md)

* [Jak utworzyć Virtual Network](../virtual-network/quick-create-portal.md)

* [Jak utworzyć sieciowej grupy zabezpieczeń z konfiguracją zabezpieczeń](../virtual-network/tutorial-filter-network-traffic.md)

* [Jak wdrożyć i skonfigurować zaporę platformy Azure](../firewall/tutorial-firewall-deploy-portal.md)

* [Środowisko wykonywania elementu Runbook](./automation-runbook-execution.md#runbook-execution-environment)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1,2: Monitoruj i Rejestruj konfigurację oraz ruch sieci wirtualnych, podsieci i kart sieciowych

**Wskazówki**: obecnie Azure Automation nie ma integracji sieci wirtualnej dla sieci prywatnej poza obsługą hybrydowych procesów roboczych elementów Runbook. Ta kontrolka nie ma zastosowania, jeśli używasz wbudowanej usługi Box bez hybrydowych procesów roboczych elementów Runbook.

Jeśli używasz hybrydowych procesów roboczych elementów Runbook, które są obsługiwane przez usługi Azure Virtual Machines, upewnij się, że podsieć zawierająca tych pracowników jest włączona z sieciową grupą zabezpieczeń (sieciowej grupy zabezpieczeń), a następnie skonfiguruj dzienniki przepływu, aby przekazywać dzienniki do konta magazynu na potrzeby inspekcji ruchu. Możesz również przesłać dalej dzienniki przepływu sieciowej grupy zabezpieczeń do obszaru roboczego Log Analytics i użyć Analiza ruchu, aby uzyskać wgląd w przepływ ruchu w chmurze platformy Azure. Niektóre zalety Analiza ruchu to możliwość wizualizacji aktywności sieciowej i identyfikowania aktywnych punktów, identyfikowania zagrożeń bezpieczeństwa, zrozumienia wzorców przepływu ruchu i wyznaczania konfiguracji sieci.

Mimo że reguły sieciowej grupy zabezpieczeń i trasy zdefiniowane przez użytkownika nie mają zastosowania do prywatnego punktu końcowego, dzienniki przepływów sieciowej grupy zabezpieczeń i informacje monitorowania dla połączeń wychodzących są nadal obsługiwane i mogą być używane.

* [Jak włączyć dzienniki przepływu sieciowej grupy zabezpieczeń](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

* [Jak włączyć i używać Analiza ruchu](../network-watcher/traffic-analytics.md)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="13-protect-critical-web-applications"></a>1,3: Ochrona krytycznych aplikacji sieci Web

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone dla aplikacji sieci Web działających na Azure App Service lub zasobach obliczeniowych.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: odmowa komunikacji ze znanymi złośliwymi adresami IP

**Wskazówki**: Azure Automation obecnie nie ma integracji z siecią wirtualną dla sieci prywatnej poza wsparciem dla hybrydowych procesów roboczych elementów Runbook. Ta kontrolka nie ma zastosowania, jeśli używasz wbudowanej usługi Box bez hybrydowych procesów roboczych elementów Runbook.

Jeśli używasz hybrydowych procesów roboczych elementów Runbook chronionych przez usługi Azure Virtual Machines, Włącz rozproszoną ochronę "odmowa usługi" (DDoS) w sieciach wirtualnych obsługujących hybrydowe procesy robocze elementów Runbook, aby chronić przed atakami DDoS. Korzystając z Azure Security Center zintegrowanej analizy zagrożeń, można odmówić komunikacji ze znanymi złośliwymi adresami IP. Skonfiguruj zaporę platformy Azure w każdym z segmentów Virtual Network, z włączoną analizą zagrożeń i skonfiguruj **alert i Odmów** dla złośliwego ruchu sieciowego.

Możesz użyć Azure Security Center dostęp do sieci w czasie, aby ograniczyć narażenie maszyn wirtualnych z systemem Windows na zatwierdzone adresy IP przez ograniczony czas. Aby ograniczyć liczbę portów i źródłowych adresów IP w oparciu o rzeczywisty ruch i analizę zagrożeń, należy również użyć Azure Security Center zalecenia dotyczące ograniczania przepustowości sieci.

* [Jak skonfigurować ochronę DDoS](../virtual-network/manage-ddos-protection.md)

* [Jak wdrożyć zaporę platformy Azure](../firewall/tutorial-firewall-deploy-portal.md)

* [Opis Azure Security Center zintegrowanej analizy zagrożeń](../security-center/threat-protection.md)

* [Informacje o Azure Security Center adaptacyjnej ograniczania przepustowości sieci](../security-center/security-center-adaptive-network-hardening.md)

* [Informacje o Azure Security Center Access Control sieci w czasie](../security-center/security-center-just-in-time.md)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="15-record-network-packets"></a>1,5: rejestrowanie pakietów sieciowych

**Wskazówki**: Azure Automation obecnie nie ma integracji sieci wirtualnej dla sieci prywatnej poza obsługą hybrydowych procesów roboczych elementów Runbook, ten formant nie ma zastosowania, jeśli używasz wbudowanej usługi Box bez hybrydowych procesów roboczych.

Jeśli używasz hybrydowych procesów roboczych elementów Runbook, które są obsługiwane przez usługę Azure Virtual Machines, możesz zarejestrować dzienniki przepływu sieciowej grupy zabezpieczeń na koncie magazynu w celu wygenerowania rekordów przepływu dla Virtual Machines platformy Azure, które działają jako procesy robocze elementów Runbook. Podczas badania nietypowej aktywności można włączyć funkcję przechwytywania pakietów Network Watcher, aby można było przejrzeć ruch sieciowy pod kątem nietypowych i nieoczekiwanych działań.

* [Jak włączyć dzienniki przepływu sieciowej grupy zabezpieczeń](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

* [Jak włączyć Network Watcher](../network-watcher/network-watcher-create.md)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: wdrażanie opartych na sieci systemów zapobiegania wykrywaniem i dostępem intruzów (identyfikatorów/adresów IP)

**Wskazówki**: Azure Automation obecnie nie ma integracji z siecią wirtualną dla sieci prywatnej poza wsparciem dla hybrydowych procesów roboczych elementów Runbook. Ta kontrolka nie ma zastosowania, jeśli używasz wbudowanej usługi Box bez hybrydowych procesów roboczych elementów Runbook.

Jeśli używasz hybrydowych procesów roboczych elementów Runbook hostowanych na maszynach wirtualnych platformy Azure, możesz połączyć przechwycenia pakietów udostępniane przez narzędzia Network Watcher i identyfikatory typu open source, aby przeprowadzić wykrywanie wtargnięcia w sieci dla szerokiego zakresu zagrożeń dla tych maszyn roboczych. Ponadto w razie potrzeby można wdrożyć zaporę platformy Azure do segmentów Virtual Network, z włączoną analizą zagrożeń i skonfigurowaną jako "Alert i Odmów" w celu uzyskania złośliwego ruchu sieciowego.

* [Wykrywanie intruzów w sieci za pomocą Network Watcher i narzędzi open source](../network-watcher/network-watcher-intrusion-detection-open-source-tools.md)

* [Jak wdrożyć zaporę platformy Azure](../firewall/tutorial-firewall-deploy-portal.md)

* [Jak skonfigurować alerty za pomocą zapory platformy Azure](../firewall/threat-intel.md)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="17-manage-traffic-to-web-applications"></a>1,7: zarządzanie ruchem do aplikacji sieci Web

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone dla aplikacji sieci Web działających na Azure App Service lub zasobach obliczeniowych.

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: Minimalizacja złożoności i kosztów administracyjnych reguł zabezpieczeń sieci

**Wskazówki**: Użyj tagów usługi Virtual Network, aby zdefiniować kontrolę dostępu do sieci dla sieciowych grup zabezpieczeń lub zapory platformy Azure skonfigurowanych na platformie Azure, które wymagają dostępu do zasobów usługi Automation. Podczas tworzenia reguł zabezpieczeń można użyć tagów usługi zamiast konkretnych adresów IP. Określając nazwę tagu usługi (na przykład GuestAndHybridManagement) w odpowiednim polu źródłowym lub docelowym reguły, można zezwolić na ruch dla odpowiedniej usługi lub go odrzucić. Firma Microsoft zarządza prefiksami adresów, które obejmują tag usługi, i automatycznie aktualizuje tag usługi jako adresy.

* [Zrozumienie i używanie tagów usługi](../virtual-network/service-tags-overview.md)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: Obsługa standardowych konfiguracji zabezpieczeń dla urządzeń sieciowych

**Wskazówki**: Definiowanie i implementowanie standardowych konfiguracji zabezpieczeń dla zasobów sieciowych używanych przez Azure Automation z Azure Policy.

Możesz również użyć planów platformy Azure, aby uprościć wdrożenia platformy Azure na dużą skalę przez pakowanie kluczowych artefaktów, takich jak szablony usługi Azure Resources, formanty RBAC platformy Azure i zasady, w ramach jednej definicji planu. Możesz zastosować plan do nowych subskrypcji i dostosować kontrolę i zarządzanie przy użyciu wersji.

* [Jak skonfigurować Azure Policy i zarządzać nimi](../governance/policy/tutorials/create-and-manage.md)

* [Przykłady Azure Policy dla sieci](/azure/governance/policy/samples/#network)

* [Jak utworzyć Azure Blueprint](../governance/blueprints/create-blueprint-portal.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="110-document-traffic-configuration-rules"></a>1,10: udokumentowanie reguł konfiguracji ruchu

**Wskazówki**: używanie tagów dla sieciowych grup zabezpieczeń i innych zasobów związanych z zabezpieczeniami sieci i przepływem ruchu. W przypadku poszczególnych reguł sieciowej grupy zabezpieczeń Użyj pola "Description", aby określić potrzeby biznesowe i/lub czas trwania (itp.) dla reguł zezwalających na ruch do/z sieci.

Użyj dowolnych wbudowanych definicji Azure Policy związanych ze znakiem, takich jak "Wymagaj tagu i jego wartości", aby upewnić się, że wszystkie zasoby są tworzone przy użyciu tagów i powiadomienia o istniejących nieoznakowanych zasobach.

Możesz użyć Azure PowerShell lub interfejsu wiersza polecenia platformy Azure, aby wyszukiwać lub wykonywać akcje na zasobach na podstawie ich tagów.

* [Tworzenie i używanie tagów](../azure-resource-manager/management/tag-resources.md)

* [Jak utworzyć Virtual Network](../virtual-network/quick-create-portal.md)

* [Jak utworzyć sieciowej grupy zabezpieczeń z konfiguracją zabezpieczeń](../virtual-network/tutorial-filter-network-traffic.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: Użyj zautomatyzowanych narzędzi do monitorowania konfiguracji zasobów sieciowych i wykrywania zmian

**Wskazówki**: Użyj dziennika aktywności platformy Azure do monitorowania konfiguracji zasobów i wykrywania zmian zasobów sieciowych. Utwórz alerty w Azure Monitor, które będą wyzwalane po wprowadzeniu zmian w zasobach krytycznych.

* [Jak wyświetlać i pobierać zdarzenia dziennika aktywności platformy Azure](../azure-monitor/platform/activity-log.md#view-the-activity-log)

* [Jak utworzyć alerty w Azure Monitor](../azure-monitor/platform/alerts-activity-log.md)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

## <a name="logging-and-monitoring"></a>Rejestrowanie i monitorowanie

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: rejestrowanie i monitorowanie](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2,1: Użyj źródeł synchronizacji zatwierdzonego czasu

**Wskazówki**: Firma Microsoft przechowuje źródła czasu dla zasobów platformy Azure. Istnieje jednak możliwość zarządzania ustawieniami synchronizacji czasu dla wszystkich hybrydowych procesów roboczych elementów Runbook uruchomionych na maszynach wirtualnych z systemem Windows.

* [Jak skonfigurować synchronizację czasu dla zasobów obliczeniowych platformy Azure](../virtual-machines/windows/time-sync.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2,2: Skonfiguruj centralne zarządzanie dziennikami zabezpieczeń

**Wskazówki**: Przekaż dane dziennika do dzienników Azure monitor, aby agregować dane zabezpieczeń wygenerowane przez Azure Automation zasoby. W ramach Azure Monitor należy używać zapytań dzienników do wyszukiwania i przeprowadzania analiz oraz używania kont usługi Azure Storage do przechowywania długoterminowego/archiwizowania. Azure Automation może wysyłać stan zadania elementu Runbook, strumienie zadań, dane konfiguracji stanu usługi Automation, zarządzanie aktualizacjami oraz dzienniki zmian lub spisu do Log Analytics obszaru roboczego. Te informacje są widoczne w interfejsie API dzienników Azure Portal, Azure PowerShell i Azure Monitor, który umożliwia wykonywanie prostych badań.

Alternatywnie możesz włączyć i dołączyć dane do usługi Azure wskaźnikowej lub SIEM innych firm.

* [Jak dołączyć wskaźnik na platformie Azure](../sentinel/quickstart-onboard.md)

* [Jak zbierać dzienniki platformy i metryki za pomocą Azure Monitor](../azure-monitor/platform/diagnostic-settings.md)

* [Jak rozpocząć pracę z usługą Azure Monitor i integracją SIEM innej firmy](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

* [Przekazywanie danych zadania usługi Azure Automation do dzienników usługi Azure Monitor](./automation-manage-send-joblogs-log-analytics.md)

* [Integrowanie DSC z dziennikami Azure Monitor](./automation-dsc-diagnostics.md)

* [Obsługiwane regiony połączonego obszaru roboczego usługi Log Analytics](./how-to/region-mappings.md)

* [Dzienniki Update Management zapytań](./update-management/update-mgmt-query-logs.md)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: Włączanie rejestrowania inspekcji dla zasobów platformy Azure

**Wskazówki**: umożliwia Azure monitor dostępu do dzienników inspekcji i aktywności, w tym źródła zdarzeń, daty, użytkownika, sygnatury czasowej, adresów źródłowych, adresów docelowych i innych przydatnych elementów.

* [Jak zbierać dzienniki platformy i metryki za pomocą Azure Monitor](../azure-monitor/platform/diagnostic-settings.md)

* [Wyświetlanie i pobieranie zdarzeń dziennika aktywności platformy Azure](../azure-monitor/platform/activity-log.md#view-the-activity-log)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: Zbierz dzienniki zabezpieczeń z systemów operacyjnych

**Wskazówki**: w przypadku korzystania z Azure Automation z wielodostępnymi procesami roboczymi elementów Runbook ten formant nie ma zastosowania, a Platforma obsługuje bazowe maszyny wirtualne.

W przypadku korzystania z funkcji hybrydowego procesu roboczego elementu Runbook Azure Security Center zapewnia monitorowanie dzienników zdarzeń zabezpieczeń dla maszyn wirtualnych z systemem Windows. Jeśli organizacja ma zachować dane dziennika zdarzeń zabezpieczeń, może być przechowywana w ramach warstwy zbierania danych. w tym momencie można wykonać zapytanie w Log Analytics. Istnieją różne warstwy: minimalne, wspólne i wszystkie, które opisano szczegółowo w poniższym łączu.

* [Skonfiguruj warstwę zbierania danych w Azure Security Center](../security-center/security-center-enable-data-collection.md#data-collection-tier)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="25-configure-security-log-storage-retention"></a>2,5: Konfigurowanie przechowywania magazynu dzienników zabezpieczeń

**Wskazówki**: w obszarze Azure monitor Ustaw okres przechowywania log Analytics obszaru roboczego zgodnie z regulacjami zgodności w organizacji. Używaj kont usługi Azure Storage do przechowywania długoterminowego/archiwizowania.

* [Zmień okres przechowywania danych w Log Analytics](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

* [Szczegóły przechowywania danych dla kont usługi Automation](./automation-managing-data.md#data-retention)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="26-monitor-and-review-logs"></a>2,6: dzienniki monitorowania i przeglądania

**Wskazówki**: analizowanie i monitorowanie dzienników pod kątem nietypowego zachowania i regularnego przeglądania wyników. Użyj zapytań dzienników Azure Monitor, aby przejrzeć dzienniki i wykonywać zapytania dotyczące danych dziennika.

Alternatywnie możesz włączyć i dołączyć dane do usługi Azure wskaźnikowej lub SIEM innych firm.

* [Jak dołączyć wskaźnik na platformie Azure](../sentinel/quickstart-onboard.md)

* [Omówienie zapytań dzienników w Azure Monitor](../azure-monitor/log-query/get-started-portal.md)

* [Jak wykonywać niestandardowe zapytania w Azure Monitor](../azure-monitor/log-query/get-started-queries.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: Włączanie alertów dla nietypowych działań

**Wskazówki**: Użyj Azure Security Center z Azure monitor do monitorowania i wysyłania alertów dotyczących nietypowej aktywności znalezionych w dziennikach i zdarzeniach zabezpieczeń.

Alternatywnie możesz włączyć i dołączyć dane do usługi Azure wskaźnikowej.

* [Jak dołączyć wskaźnik na platformie Azure](../sentinel/quickstart-onboard.md)

* [Jak zarządzać alertami w Azure Security Center](../security-center/security-center-managing-and-responding-alerts.md)

* [Jak ostrzec o Azure Monitor danych dziennika](../azure-monitor/learn/tutorial-response.md)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="28-centralize-anti-malware-logging"></a>2,8: scentralizowanie rejestrowania chroniącego przed złośliwym oprogramowaniem

**Wskazówki**: w przypadku korzystania z Azure Automation z wielodostępnymi procesami roboczymi elementów Runbook ten formant nie ma zastosowania, a Platforma obsługuje bazowe maszyny wirtualne.

Jeśli jednak korzystasz z funkcji hybrydowego procesu roboczego elementu Runbook, możesz użyć oprogramowania antywirusowego firmy Microsoft dla platformy Azure Cloud Services i maszyn wirtualnych. Skonfiguruj swoje maszyny wirtualne do rejestrowania zdarzeń na koncie usługi Azure Storage. Skonfiguruj obszar roboczy Log Analytics, aby pozyskiwać zdarzenia z kont magazynu, a następnie utwórz alerty, jeśli są odpowiednie. Postępuj zgodnie z zaleceniami w Azure Security Center: "COMPUTE &amp; Apps".

* [Jak skonfigurować oprogramowanie antywirusowe firmy Microsoft dla Cloud Services i Virtual Machines](../security/fundamentals/antimalware.md)

* [Jak włączyć monitorowanie na poziomie gościa dla maszyn wirtualnych](../cost-management-billing/cloudyn/azure-vm-extended-metrics.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="29-enable-dns-query-logging"></a>2,9: Włączanie rejestrowania zapytań DNS

**Wskazówki**: Implementowanie rozwiązania innej firmy z witryny Azure Marketplace na potrzeby rejestrowania w systemie DNS zgodnie z potrzebami organizacji.

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="210-enable-command-line-audit-logging"></a>2,10: Włączanie rejestrowania inspekcji w wierszu polecenia

**Wskazówki**: w przypadku korzystania z Azure Automation z wielodostępnymi procesami roboczymi elementów Runbook ten formant nie ma zastosowania, a Platforma obsługuje bazowe maszyny wirtualne.

Jednak w przypadku korzystania z funkcji hybrydowego procesu roboczego elementu Runbook Azure Security Center zapewnia monitorowanie dzienników zdarzeń zabezpieczeń dla maszyn wirtualnych platformy Azure. Security Center inicjuje agenta Log Analytics na wszystkich obsługiwanych maszynach wirtualnych platformy Azure i nowych, które są tworzone w przypadku włączenia automatycznej aprowizacji. Lub ręcznie zainstalować agenta. Agent włącza zdarzenie tworzenia procesu 4688 i pole wiersza polecenia wewnątrz zdarzenia 4688. Nowe procesy utworzone na maszynie wirtualnej są rejestrowane przez dziennik zdarzeń i monitorowane przez usługi wykrywania Security Center.

* [Zbieranie danych w usłudze Azure Security Center](../security-center/security-center-enable-data-collection.md#data-collection-tier)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

## <a name="identity-and-access-control"></a>Tożsamość i kontrola dostępu

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: tożsamość i kontrola dostępu](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: obsługa spisu kont administracyjnych

**Wskazówki**: Użyj Azure Active Directory wbudowanych ról administratora, które mogą być jawnie przypisane i mogą być wysyłane zapytania. Za pomocą modułu Azure AD PowerShell można wykonywać zapytania ad hoc w celu odnajdywania kont należących do grup administracyjnych. Za każdym razem, gdy używane są konta Uruchom jako konta usługi Automation dla elementów Runbook, upewnij się, że te jednostki usługi są również śledzone w spisie, ponieważ często mają podwyższony poziom uprawnień. Usuń wszystkie nieużywane konta Uruchom jako, aby zminimalizować narażoną powierzchnię ataku.

* [Jak uzyskać rolę katalogu w usłudze Azure AD przy użyciu programu PowerShell](/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

* [Jak uzyskać członków roli katalogu w usłudze Azure AD przy użyciu programu PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

* [Usuwanie konta Uruchom jako lub klasycznego konta Uruchom jako](./manage-runas-account.md#delete-a-run-as-or-classic-run-as-account)

* [Zarządzanie Azure Automation kontem Uruchom jako](./manage-runas-account.md)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="32-change-default-passwords-where-applicable"></a>3,2: Zmień domyślne hasła, jeśli ma to zastosowanie

**Wskazówki**: konto Azure Automation nie ma koncepcji domyślnych haseł. Klienci są odpowiedzialni za aplikacje innych firm i usługi w portalu Marketplace, które mogą korzystać z domyślnych haseł, które są uruchamiane na podstawie usługi lub hybrydowych procesów roboczych elementów Runbook.

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: Użyj dedykowanych kont administracyjnych

**Wskazówki**: Tworzenie standardowych procedur operacyjnych dotyczących korzystania z dedykowanych kont administracyjnych. Użyj Azure Security Center Zarządzanie tożsamościami i dostępem, aby monitorować liczbę kont administracyjnych. Za każdym razem, gdy używane są konta Uruchom jako konta usługi Automation dla elementów Runbook, upewnij się, że te jednostki usługi są również śledzone w spisie, ponieważ często mają podwyższony poziom uprawnień. Zakres tych tożsamości z najmniej uprzywilejowanymi uprawnieniami, które są potrzebne, aby elementy Runbook mogły pomyślnie wykonać swój zautomatyzowany proces. Usuń wszystkie nieużywane konta Uruchom jako, aby zminimalizować narażoną powierzchnię ataku.

Możesz również włączyć dostęp do usługi Microsoft just-in-Time/tylko do odczytu, korzystając z ról uprzywilejowanych Azure AD Privileged Identity Management dla usług firmowych i Azure Resource Manager.

* [Dowiedz się więcej o Privileged Identity Management](../active-directory/privileged-identity-management/index.yml)

* [Usuwanie konta Uruchom jako lub klasycznego konta Uruchom jako](./manage-runas-account.md#delete-a-run-as-or-classic-run-as-account)

* [Zarządzanie Azure Automation kontem Uruchom jako](./manage-runas-account.md)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3,4: Korzystaj z logowania jednokrotnego (SSO) z usługą Azure Active Directory

**Wskazówki**: wszędzie tam, gdzie to możliwe, należy używać logowania jednokrotnego z Azure Active Directory zamiast konfigurować indywidualne poświadczenia autonomiczne dla poszczególnych usług. Użyj Azure Security Center zalecenia dotyczące zarządzania tożsamościami i dostępem.

* [Single sign-on to applications in Azure Active Directory (Logowanie jednokrotne do aplikacji w usłudze Azure Active Directory)](../active-directory/manage-apps/what-is-single-sign-on.md)

* [Jak monitorować tożsamość i dostęp w Azure Security Center](../security-center/security-center-identity-access.md)

* [Uwierzytelnianie na platformie Azure za pomocą usługi Azure AD](./automation-use-azure-ad.md)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Użyj uwierzytelniania wieloskładnikowego, aby uzyskać dostęp oparty na Azure Active Directory

**Wskazówki**: Włączanie uwierzytelniania wieloskładnikowego usługi Azure AD (MFA) i postępowanie Azure Security Center zalecenia dotyczące zarządzania tożsamościami i dostępem.

* [Jak włączyć usługę MFA na platformie Azure](../active-directory/authentication/howto-mfa-getstarted.md)

* [Jak monitorować tożsamość i dostęp w Azure Security Center](../security-center/security-center-identity-access.md)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="36-use-dedicated-machines-for-all-administrative-tasks"></a>3,6: Używaj dedykowanych maszyn do wszystkich zadań administracyjnych

**Wskazówki**: Użyj dostępem uprzywilejowanym z uwierzytelnianiem wieloskładnikowym skonfigurowanym do logowania się i konfigurowania zasobów konta Azure Automation w środowiskach produkcyjnych.

* [Dowiedz się więcej o stacjach roboczych uprzywilejowanego dostępu](/windows-server/identity/securing-privileged-access/privileged-access-workstations)

* [Jak włączyć usługę MFA na platformie Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: Rejestruj i Ostrzegaj o podejrzanych działaniach z kont administracyjnych

**Wskazówki**: korzystanie z funkcji wykrywania ryzyka usługi Azure AD do wyświetlania alertów i raportów na temat ryzykownego zachowania użytkowników. Opcjonalnie klient może dalej Azure Security Center alerty wykrywania ryzyka, aby Azure Monitor i skonfigurować niestandardowe alerty/powiadomienia przy użyciu grup akcji.

* [Zrozumienie Azure Security Center wykrywania ryzyka (podejrzane działanie)](../active-directory/identity-protection/overview-identity-protection.md)

* [Jak zintegrować dzienniki aktywności platformy Azure z usługą Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

* [Jak skonfigurować grupy akcji dla niestandardowych alertów i powiadomień](../azure-monitor/platform/action-groups.md)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: zarządzanie zasobami platformy Azure tylko z zatwierdzonych lokalizacji

**Wskazówki**: zaleca się używanie nazwanych lokalizacji dostępu warunkowego, aby zezwolić na dostęp tylko do określonych logicznych grup zakresów adresów IP lub krajów/regionów.

* [Jak skonfigurować nazwane lokalizacje na platformie Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="39-use-azure-active-directory"></a>3,9: Użyj Azure Active Directory

**Wskazówki**: Użyj usługi Azure AD jako centralnego systemu uwierzytelniania i autoryzacji. Usługa Azure AD chroni dane przy użyciu silnego szyfrowania danych przechowywanych i przesyłanych. Usługa Azure AD również Sole, skróty i bezpieczne przechowywanie poświadczeń użytkownika. W przypadku korzystania z hybrydowych procesów roboczych elementów Runbook można korzystać z tożsamości zarządzanych zamiast kont Uruchom jako, aby zapewnić bardziej bezproblemowe zabezpieczenia.

* [Jak utworzyć i skonfigurować wystąpienie usługi Azure AD](../active-directory-domain-services/tutorial-create-instance.md)

* [Używanie uwierzytelniania elementu Runbook z tożsamościami zarządzanymi](./automation-hrw-run-runbooks.md#runbook-auth-managed-identities)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: regularnie Przeglądaj i Uzgodnij dostęp użytkowników

**Wskazówki**: usługa Azure AD udostępnia dzienniki, które ułatwiają odnajdywanie starych kont. Ponadto za pomocą przeglądów dostępu do tożsamości platformy Azure można efektywnie zarządzać członkostwem w grupach, dostępem do aplikacji dla przedsiębiorstw i przypisaniami ról. Dostęp użytkowników może być regularnie przeglądany, aby upewnić się, że tylko Ci użytkownicy mają ciągły dostęp. Za każdym razem, gdy używane są konta Uruchom jako konta usługi Automation dla elementów Runbook, upewnij się, że te jednostki usługi są również śledzone w spisie, ponieważ często mają podwyższony poziom uprawnień. Usuń wszystkie nieużywane konta Uruchom jako, aby zminimalizować narażoną powierzchnię ataku.

* [Informacje o raportowaniu usługi Azure AD](../active-directory/reports-monitoring/index.yml)

* [Jak korzystać z przeglądów dostępu do tożsamości platformy Azure](../active-directory/governance/access-reviews-overview.md)

* [Usuwanie konta Uruchom jako lub klasycznego konta Uruchom jako](./manage-runas-account.md#delete-a-run-as-or-classic-run-as-account)

* [Zarządzanie Azure Automation kontem Uruchom jako](./manage-runas-account.md)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: Monitor próbuje uzyskać dostęp do zdezaktywowanych poświadczeń

**Wskazówki**: masz dostęp do źródeł działań związanych z logowaniem do usługi Azure AD, inspekcją i ryzykiem dzienników zdarzeń, które umożliwiają integrację z dowolnym narzędziem Siem/monitorowania.

Proces ten można usprawnić, tworząc ustawienia diagnostyczne dla Azure Active Directory kont użytkowników i wysyłając dzienniki inspekcji i dzienniki logowania do obszaru roboczego Log Analytics. Żądane alerty można skonfigurować w obszarze roboczym Log Analytics.

* [Jak zintegrować dzienniki aktywności platformy Azure z usługą Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: odchylenia zachowania podczas logowania do konta

**Wskazówki**: Użyj funkcji ryzyka i ochrony tożsamości usługi Azure AD, aby skonfigurować automatyczne odpowiedzi na wykryte podejrzane działania związane z tożsamościami użytkowników dla zasobu sieciowego. Możesz również pozyskać dane do usługi Azure wskaźnikowej na potrzeby dalszej analizy.

* [Jak wyświetlić ryzykowne logowania usługi Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

* [Jak skonfigurować i włączyć zasady dotyczące ryzyka związanego z ochroną tożsamości](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

* [Jak dołączyć wskaźnik na platformie Azure](../sentinel/quickstart-onboard.md)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: Zapewnij firmie Microsoft dostęp do odpowiednich danych klienta w scenariuszach pomocy technicznej

**Wskazówki**: w przypadku kont Azure Automation pomoc techniczna firmy Microsoft może uzyskać dostęp do metadanych zasobów platformy w otwartym przypadku pomocy technicznej bez użycia innego narzędzia.

Jednak w przypadku używania hybrydowych procesów roboczych elementów Runbook przez usługi Azure Virtual Machines i innych firm do uzyskiwania dostępu do danych klienta (na przykład w trakcie żądania obsługi) należy użyć Skrytka klienta (wersja zapoznawcza) dla usługi Azure Virtual Machines, aby przejrzeć i zatwierdzić lub odrzucić żądania dostępu do danych klienta.

* [Informacje o Skrytka klienta](../security/fundamentals/customer-lockbox-overview.md)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

## <a name="data-protection"></a>Ochrona danych

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Ochrona danych](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: przechowywanie spisu poufnych informacji

**Wskazówki**: Użyj tagów, aby pomóc w śledzeniu zasobów Azure Automation, które przechowują lub przetwarzają informacje poufne.

* [Tworzenie i używanie tagów](../azure-resource-manager/management/tag-resources.md)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: Izoluj systemy przechowujące lub przetwarzające informacje poufne

**Wskazówki**: implementowanie oddzielnych subskrypcji i/lub grup zarządzania na potrzeby tworzenia, testowania i produkcji. Izoluj środowiska przy użyciu oddzielnych zasobów konta usługi Automation. Zasoby, takie jak hybrydowe procesy robocze elementów Runbook, powinny być oddzielone przez sieć wirtualną/podsieć, odpowiednio otagowane i zabezpieczone w ramach sieciowej grupy zabezpieczeń (sieciowej grupy zabezpieczeń) lub zapory platformy Azure. W przypadku maszyn wirtualnych przechowujących lub przetwarzających dane poufne należy zaimplementować zasady i procedury, aby je wyłączyć, gdy nie są używane.

* [Jak utworzyć dodatkowe subskrypcje platformy Azure](../cost-management-billing/manage/create-subscription.md)

* [Jak utworzyć Grupy zarządzania](../governance/management-groups/create.md)

* [Tworzenie i używanie tagów](../azure-resource-manager/management/tag-resources.md)

* [Jak utworzyć Virtual Network](../virtual-network/quick-create-portal.md)

* [Jak utworzyć sieciowej grupy zabezpieczeń z konfiguracją zabezpieczeń](../virtual-network/tutorial-filter-network-traffic.md)

* [Jak wdrożyć zaporę platformy Azure](../firewall/tutorial-firewall-deploy-portal.md)

* [Jak skonfigurować alert lub alert i odmówić za pomocą zapory platformy Azure](../firewall/threat-intel.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: Monitoruj i blokuj nieautoryzowany transfer informacji poufnych

**Wskazówki**: w przypadku korzystania z funkcji hybrydowego procesu roboczego elementu Runbook Skorzystaj z rozwiązania innej firmy z witryny Azure Marketplace w sieci obwodowej, która monitoruje do nieautoryzowanego transferu poufnych informacji i blokuje takie transfery podczas powiadamiania specjalistów ds. bezpieczeństwa informacji.

W przypadku podstawowej platformy zarządzanej przez firmę Microsoft Firma Microsoft traktuje całą zawartość klienta jako poufną i chroniącą przed utratą i narażeniem danych przez klienta. Aby zapewnić bezpieczeństwo danych klienta na platformie Azure, firma Microsoft wdrożyła i utrzymuje pakiet niezawodnych kontroli i możliwości ochrony danych.

* [Zrozumienie ochrony danych klientów na platformie Azure](../security/fundamentals/protection-customer-data.md)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: Szyfruj wszystkie poufne informacje podczas przesyłania

**Wskazówki**: Szyfruj wszystkie informacje poufne podczas przesyłania. Upewnij się, że wszyscy klienci łączący się z zasobami platformy Azure w usłudze Azure Virtual Networks mogą negocjować protokół TLS 1,2 lub wyższy. Azure Automation w pełni obsługuje i wymusza Transport Layer (TLS) 1,2 oraz wszystkie wywołania klienta lub nowsze wersje dla wszystkich zewnętrznych punktów końcowych HTPS (za poorednictwem elementów webhook, węzłów DSC, hybrydowego procesu roboczego elementu Runbook).

Postępuj zgodnie z zaleceniami Azure Security Center na potrzeby szyfrowania w czasie spoczynku i szyfrowania podczas przesyłania, jeśli ma to zastosowanie.

* [Informacje o szyfrowaniu podczas przesyłania na platformę Azure](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit)

* [Azure Automation protokołu TLS 1,2](https://azure.microsoft.com/updates/azure-automation-tls12-enforcement/)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: udostępnione

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: Użyj aktywnego narzędzia do odnajdywania, aby identyfikować poufne dane

**Wskazówki**: Użyj narzędzia do odnajdywania aktywnego innej firmy, aby zidentyfikować wszystkie informacje poufne przechowywane, przetwarzane lub przekazywane przez systemy technologiczne organizacji, w tym zlokalizowane na miejscu lub u dostawcy usług zdalnych, a także aktualizować spis informacji poufnych w organizacji.

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: korzystanie z usługi Azure RBAC do kontrolowania dostępu do zasobów

**Wskazówki**: Użyj kontroli dostępu opartej na rolach (Azure RBAC) na platformie Azure, aby kontrolować dostęp do zasobów Azure Automation przy użyciu wbudowanych definicji ról, przypisując użytkownikom dostęp do zasobów usługi Automation po najniższym poziomie uprzywilejowanym lub modelu dostępu "tylko do odczytu". W przypadku korzystania z hybrydowych procesów roboczych elementów Runbook należy wykorzystać zarządzane tożsamości dla tych maszyn wirtualnych, aby uniknąć korzystania z jednostek usługi, w przypadku korzystania z wielu dzierżawców lub hybrydowych procesów roboczych elementów Runbook upewnij się, że w zakresie tożsamości procesów roboczych elementów Runbook zastosowano odpowiednie uprawnienia kontroli dostępu platformy Azure.

* [Jak skonfigurować usługę Azure RBAC](../role-based-access-control/role-assignments-portal.md)

* [Uprawnienia elementu Runbook dla hybrydowego procesu roboczego elementu Runbook](./automation-hybrid-runbook-worker.md#runbook-permissions-for-a-hybrid-runbook-worker)

* [Zarządzanie uprawnieniami ról i zabezpieczeniami](./automation-role-based-access-control.md)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: Wymuś kontrolę dostępu przy użyciu ochrony przed utratą danych opartą na hoście

**Wskazówki**: Azure Automation obecnie nie uwidaczniają maszyn wirtualnych wielodostępnego elementu Runbook z wieloma dzierżawcami i są obsługiwane przez platformę. Ta kontrolka nie ma zastosowania, jeśli używasz wbudowanej usługi Box bez hybrydowych procesów roboczych elementów Runbook.

Jeśli używasz hybrydowych procesów roboczych elementów Runbook, które są obsługiwane przez usługi Azure Virtual Machines, musisz użyć rozwiązania do zapobiegania utracie danych opartego na hoście innej firmy, aby wymusić kontrolę dostępu do hostowanych maszyn wirtualnych hybrydowych procesów roboczych elementu Runbook.

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: Szyfruj poufne informacje w spoczynku

**Wskazówki**: Używanie kluczy zarządzanych przez klienta w programie Azure Automation. Azure Automation obsługuje używanie kluczy zarządzanych przez klienta do szyfrowania wszystkich użytych bezpiecznych zasobów, takich jak poświadczenia, certyfikaty, połączenia i zmienne zaszyfrowane. Korzystaj z szyfrowanych zmiennych z elementami Runbook dla wszystkich trwałych wyszukiwań zmiennych, aby zapobiec niezamierzonemu narażeniu.

W przypadku korzystania z hybrydowych procesów roboczych elementów Runbook dyski wirtualne na maszynach wirtualnych są szyfrowane w stanie spoczynku przy użyciu szyfrowania po stronie serwera lub usługi Azure Disk Encryption (ADE). Usługa Azure Disk Encryption wykorzystuje funkcję BitLocker systemu Windows do szyfrowania dysków zarządzanych z kluczami zarządzanymi przez klienta w ramach maszyny wirtualnej gościa. Szyfrowanie po stronie serwera z kluczami zarządzanymi przez klienta usprawnia w systemie ADE, umożliwiając korzystanie z dowolnych typów systemów operacyjnych i obrazów dla maszyn wirtualnych przez szyfrowanie danych w usłudze Storage.

* [Szyfrowanie po stronie serwera dla usługi Azure Managed disks](../virtual-machines/windows/disk-encryption.md)

* [Azure Disk Encryption dla maszyn wirtualnych z systemem Windows](../virtual-machines/windows/disk-encryption-overview.md)

* [Korzystanie z kluczy zarządzanych przez klienta dla konta usługi Automation](./automation-secure-asset-encryption.md#use-of-customer-managed-keys-for-an-automation-account)

* [Zmienne zarządzane w Azure Automation](./shared-resources/variables.md)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: Rejestruj i Ostrzegaj o zmianach krytycznych zasobów platformy Azure

**Wskazówki**: Użyj Azure monitor z dziennikiem aktywności platformy Azure, aby utworzyć alerty dotyczące sytuacji, w których zmiany dotyczą najważniejszych zasobów platformy Azure, takich jak składniki sieci, konta Azure Automation i elementy Runbook.

* [Rejestrowanie diagnostyczne dla sieciowej grupy zabezpieczeń](../private-link/private-link-overview.md#logging-and-monitoring)

* [Jak utworzyć alerty dla zdarzeń dziennika aktywności platformy Azure](../azure-monitor/platform/alerts-activity-log.md)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

## <a name="vulnerability-management"></a>Zarządzanie lukami w zabezpieczeniach

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Zarządzanie lukami w zabezpieczeniach](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: uruchamianie narzędzi do skanowania automatycznych luk w zabezpieczeniach

**Wskazówki**: Postępuj zgodnie z zaleceniami Azure Security Center na temat przeprowadzania ocen luk w zabezpieczeniach zasobów platformy Azure

* [Zalecenia dotyczące zabezpieczeń w Centrum zabezpieczeń Azure](../security-center/security-center-recommendations.md)

* [Security Center informacje dotyczące rekomendacji](../security-center/recommendations-reference.md)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: Wdróż automatyczne rozwiązanie do zarządzania poprawkami systemu operacyjnego

**Wskazówki**: Azure Automation obecnie nie uwidaczniają maszyn wirtualnych wielodostępnego elementu Runbook z wieloma dzierżawcami i są obsługiwane przez platformę. Ta kontrolka nie ma zastosowania, jeśli używasz wbudowanej usługi Box bez hybrydowych procesów roboczych elementów Runbook.

Jeśli używasz hybrydowych procesów roboczych elementów Runbook, które są obsługiwane przez usługi Azure Virtual Machines, Użyj usługi Azure Update Management do zarządzania aktualizacjami i poprawkami dla maszyn wirtualnych. Update Management opiera się na lokalnie skonfigurowanym repozytorium aktualizacji w celu zastosowania poprawek obsługiwanych systemów Windows. Narzędzia, takie jak System Center Updates Publisher (aktualizacje wydawcy), umożliwiają publikowanie aktualizacji niestandardowych w programie Windows Server Update Services (WSUS). Ten scenariusz umożliwia Update Management poprawek maszyn, które używają Configuration Manager jako repozytorium aktualizacji z oprogramowaniem innych firm.

* [Update Management na platformie Azure](./update-management/update-mgmt-overview.md)

* [Zarządzanie aktualizacjami i poprawkami dla maszyn wirtualnych](./update-management/update-mgmt-manage-updates-for-vm.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5,3: Wdróż rozwiązanie zautomatyzowanego zarządzania poprawkami dla tytułów oprogramowania innych firm

**Wskazówki**: Azure Automation obecnie nie uwidaczniają maszyn wirtualnych wielodostępnego procesu roboczego elementu Runbook z wieloma dzierżawcami i są obsługiwane przez platformę. Ta kontrolka nie ma zastosowania, jeśli używasz wbudowanej usługi Box bez hybrydowych procesów roboczych elementów Runbook.

Jeśli używasz hybrydowych procesów roboczych elementów Runbook, które są obsługiwane przez usługi Azure Virtual Machines, możesz użyć usługi Azure Update Management do zarządzania aktualizacjami i poprawkami dla maszyn wirtualnych. Update Management opiera się na lokalnie skonfigurowanym repozytorium aktualizacji w celu zastosowania poprawek obsługiwanych systemów Windows. Narzędzia, takie jak System Center Updates Publisher (program Updates Publisher), umożliwiają publikowanie aktualizacji niestandardowych w programie Windows Server Update Services (WSUS). Ten scenariusz umożliwia Update Management poprawek maszyn, które używają Configuration Manager jako repozytorium aktualizacji z oprogramowaniem innych firm.

* [Update Management rozwiązanie na platformie Azure](./update-management/update-mgmt-overview.md)

* [Zarządzanie aktualizacjami i poprawkami dla maszyn wirtualnych platformy Azure](./update-management/update-mgmt-manage-updates-for-vm.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: porównanie luk w zabezpieczeniach z tyłu do tyłu

**Wskazówki**: Eksportuj wyniki skanowania w regularnych odstępach czasu i Porównaj wyniki, aby sprawdzić, czy luki zostały skorygowane. W przypadku korzystania z rekomendacji dotyczących zarządzania lukami w zabezpieczeniach sugerowanej przez Azure Security Center klient może przestawić w portalu wybranego rozwiązania, aby wyświetlić historyczne dane skanowania.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: Użyj procesu oceny ryzyka, aby określić priorytety korygowania odkrytych luk w zabezpieczeniach

**Wskazówki**: Użyj domyślnych ocen ryzyka ("Secure Score") dostarczonych przez Azure Security Center, aby pomóc w ustalaniu priorytetów w odniesieniu do odkrytych luk w zabezpieczeniach.

* [Informacje na temat Azure Security Center zabezpieczeń](../security-center/secure-score-security-controls.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

## <a name="inventory-and-asset-management"></a>Zarządzanie magazynem i zasobami

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Spis i zarządzanie zasobami](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: Użyj rozwiązania automatycznego odnajdywania zasobów

**Wskazówki**: Użyj grafu zasobów platformy Azure do wykonywania zapytań i odnajdywania wszystkich Azure Automation zasobów w ramach subskrypcji. Upewnij się, że masz odpowiednie uprawnienia (odczyt) w dzierżawie i że można wyliczyć wszystkie subskrypcje platformy Azure oraz zasoby w ramach subskrypcji.

* [Jak tworzyć zapytania przy użyciu grafu zasobów platformy Azure](../governance/resource-graph/first-query-portal.md)

* [Jak wyświetlić subskrypcje platformy Azure](/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

* [Opis kontroli RBAC platformy Azure](../role-based-access-control/overview.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="62-maintain-asset-metadata"></a>6,2: Konserwowanie metadanych zasobów

**Wskazówki**: stosowanie tagów do zasobów platformy Azure, dzięki czemu metadane są logicznie zorganizowane w taksonomię.

* [Tworzenie i używanie tagów](../azure-resource-manager/management/tag-resources.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: Usuń nieautoryzowane zasoby platformy Azure

**Wskazówki**: używanie tagowania, grup zarządzania i oddzielnych subskrypcji, gdzie to konieczne, do organizowania i śledzenia zasobów Azure Automation. Regularnie Uzgadniaj spis i zapewnij, że nieautoryzowane zasoby są usuwane z subskrypcji w odpowiednim czasie. Usuń wszystkie nieużywane konta Uruchom jako, aby zminimalizować narażoną powierzchnię ataku.

* [Jak utworzyć dodatkowe subskrypcje platformy Azure](../cost-management-billing/manage/create-subscription.md)

* [Jak utworzyć Grupy zarządzania](../governance/management-groups/create.md)

* [Tworzenie i używanie tagów](../azure-resource-manager/management/tag-resources.md)

* [Usuwanie konta Uruchom jako lub klasycznego konta Uruchom jako](./manage-runas-account.md#delete-a-run-as-or-classic-run-as-account)

* [Zarządzanie Azure Automation kontem Uruchom jako](./manage-runas-account.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6,4: Definiowanie i obsługa spisu zatwierdzonych zasobów platformy Azure

**Wskazówki**: należy utworzyć spis zatwierdzonych zasobów platformy Azure i zatwierdzonego oprogramowania dla zasobów obliczeniowych zgodnie z potrzebami organizacji.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: Monitoruj niezatwierdzone zasoby platformy Azure

**Wskazówki**: Użyj Azure Policy, aby umieścić ograniczenia dotyczące typu zasobów, które mogą być tworzone w subskrypcjach klientów, przy użyciu następujących wbudowanych definicji zasad:
- Niedozwolone typy zasobów
- Dozwolone typy zasobów

Ponadto za pomocą grafu zasobów platformy Azure można wysyłać zapytania/odnajdywać zasoby w ramach subskrypcji. Może to pomóc w środowiskach opartych na wysokich zabezpieczeniach, takich jak te z kontami magazynu.

* [Jak skonfigurować Azure Policy i zarządzać nimi](../governance/policy/tutorials/create-and-manage.md)

* [Jak tworzyć zapytania przy użyciu grafu zasobów platformy Azure](../governance/resource-graph/first-query-portal.md)

* [Przykładowe Azure Policy wbudowane dla Azure Automation](./policy-samples.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: Monitoruj niezatwierdzone aplikacje oprogramowania w ramach zasobów obliczeniowych

**Wskazówki**: Oferta Azure Automation nie uwidacznia obecnie podstawowych maszyn wirtualnych procesu roboczego z wieloma dzierżawcami i jest obsługiwana przez platformę. Ta kontrolka nie ma zastosowania, jeśli używasz wbudowanej usługi Box bez hybrydowych procesów roboczych. Istnieje jednak możliwość zainstalowania, usunięcia i zarządzania modułami programu PowerShell lub modułów języka Python, do których elementy Runbook mogą uzyskać dostęp za pośrednictwem portalu lub poleceń cmdlet. Nie można usunąć lub zaktualizować niezatwierdzonego lub starego modułu dla elementów Runbook.

Jeśli używasz hybrydowych procesów roboczych elementów Runbook, które są obsługiwane przez usługę Azure Virtual Machines, Azure Automation zapewnia pełną kontrolę podczas wdrażania, działania i likwidowania obciążeń i zasobów. Skorzystaj ze spisu maszyn wirtualnych platformy Azure, aby zautomatyzować zbieranie informacji o całym oprogramowaniu na Virtual Machines. W Azure Portal są dostępne nazwy, wersje, Wydawca i czas odświeżania oprogramowania. Aby uzyskać dostęp do daty instalacji i innych informacji, klient musi włączyć diagnostykę na poziomie gościa i przenieść dzienniki zdarzeń systemu Windows do obszaru roboczego Log Analytics.

* [Wprowadzenie do usługi Azure Automation](./automation-intro.md)

* [Jak włączyć Spis maszyn wirtualnych platformy Azure](./automation-tutorial-installed-software.md)

* [Zarządzanie modułem w Azure Automation](./shared-resources/modules.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: Usuń niezatwierdzone zasoby platformy Azure i aplikacje oprogramowania

**Wskazówki**: Klient może uniemożliwić tworzenie zasobów lub użycie z Azure Policy zgodnie z wymaganiami firmy klienta. Możesz zaimplementować własny proces usuwania nieautoryzowanych zasobów. W ramach Azure Automation można zainstalować i usunąć program PowerShell lub moduły języka Python, do których elementy Runbook mogą uzyskać dostęp za pośrednictwem portalu lub poleceń cmdlet. Nie można usunąć lub zaktualizować niezatwierdzonego lub starego modułu dla elementów Runbook.

* [Jak skonfigurować Azure Policy i zarządzać nimi](../governance/policy/tutorials/create-and-manage.md)

* [Zarządzanie modułem w Azure Automation](./shared-resources/modules.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="68-use-only-approved-applications"></a>6,8: Używaj tylko zatwierdzonych aplikacji

**Wskazówki**: w przypadku korzystania z funkcji hybrydowego procesu roboczego elementu Runbook można użyć Azure Security Center adaptacyjnych kontroli aplikacji, aby upewnić się, że tylko autoryzowane oprogramowanie i wszystkie nieautoryzowane oprogramowanie zostało zablokowane na platformie Azure Virtual Machines.

* [Jak używać Azure Security Center adaptacyjnych kontroli aplikacji](../security-center/security-center-adaptive-application.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="69-use-only-approved-azure-services"></a>6,9: Używaj tylko zatwierdzonych usług platformy Azure

**Wskazówki**: Użyj Azure Policy, aby umieścić ograniczenia dotyczące typu zasobów, które mogą być tworzone w subskrypcjach klientów, przy użyciu następujących wbudowanych definicji zasad:
- Niedozwolone typy zasobów
- Dozwolone typy zasobów

* [Jak skonfigurować Azure Policy i zarządzać nimi](../governance/policy/tutorials/create-and-manage.md)

* [Jak odmówić określonego typu zasobu za pomocą Azure Policy](../governance/policy/samples/index.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6,10: przechowywanie spisu zatwierdzonych tytułów oprogramowania

**Wskazówki**: w przypadku korzystania z funkcji hybrydowego procesu roboczego elementu Runbook można użyć funkcji adaptacyjne kontrolki aplikacji Azure Security Center z maszynami wirtualnymi hybrydowych procesów roboczych.

Adaptacyjna kontrola aplikacji to inteligentne, zautomatyzowane i kompleksowe rozwiązanie od Azure Security Center, które pomaga kontrolować, które aplikacje mogą być uruchamiane na maszynach z platformą Azure i poza platformą Azure (Windows i Linux). Zaimplementuj rozwiązanie innych firm, jeśli nie spełni wymagania organizacji.

* [Jak używać Azure Security Center adaptacyjnych kontroli aplikacji](../security-center/security-center-adaptive-application.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: Ogranicz możliwość korzystania przez użytkowników z Azure Resource Manager

**Wskazówki**: Użyj zasad dostępu warunkowego platformy Azure, aby ograniczyć możliwość współdziałania z Azure Resource Manager przez skonfigurowanie "blokowania dostępu" dla aplikacji "Microsoft Azure Management" z niezabezpieczonych lub niezatwierdzonych lokalizacji lub urządzeń.

* [Jak skonfigurować dostęp warunkowy w celu blokowania dostępu do Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6,12: Ogranicz możliwość wykonywania skryptów w zasobach obliczeniowych przez użytkowników

**Wskazówki**: w przypadku korzystania z funkcji hybrydowego procesu roboczego elementu Runbook, w zależności od typu skryptów, można użyć określonych konfiguracji systemu operacyjnego lub zasobów innych firm, aby ograniczyć możliwość wykonywania skryptów w ramach zasobów obliczeniowych platformy Azure. Możesz również wykorzystać Azure Security Center adaptacyjnych kontroli aplikacji, aby upewnić się, że tylko autoryzowane oprogramowanie i wszystkie nieautoryzowane oprogramowanie zostało zablokowane na platformie Azure Virtual Machines.

* [Jak kontrolować wykonywanie skryptów programu PowerShell w środowiskach systemu Windows](/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6)

* [Jak używać Azure Security Center adaptacyjnych kontroli aplikacji](../security-center/security-center-adaptive-application.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: fizyczne lub logiczne rozdzielenie aplikacji wysokiego ryzyka

**Wskazówki**: aplikacje o wysokim ryzyku wdrożone w środowisku platformy Azure mogą być izolowane przy użyciu oddzielnych kontenerów sieci i zasobów przy użyciu konstrukcji takich jak sieci wirtualne, podsieci, subskrypcje i grupy zarządzania, które mogą być wystarczająco zabezpieczone za pomocą zapory platformy Azure, zapory aplikacji sieci Web (WAF) lub sieciowej grupy zabezpieczeń (sieciowej grupy zabezpieczeń).

* [Sieci wirtualne i maszyny wirtualne na platformie Azure](../virtual-machines/network-overview.md)

* [Omówienie Zapory platformy Azure](../firewall/overview.md)

* [Omówienie zapory aplikacji sieci Web platformy Azure](../web-application-firewall/overview.md)

* [Sieciowe grupy zabezpieczeń](../virtual-network/security-overview.md)

* [Omówienie usługi Azure Virtual Network](../virtual-network/virtual-networks-overview.md)

* [Organizowanie zasobów przy użyciu grup zarządzania platformy Azure](../governance/management-groups/overview.md)

* [Przewodnik po decyzjach związanych z subskrypcjami](/azure/cloud-adoption-framework/decision-guides/subscriptions/)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

## <a name="secure-configuration"></a>Bezpieczna konfiguracja

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: bezpieczna konfiguracja](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: Ustanów bezpieczne konfiguracje dla wszystkich zasobów platformy Azure

**Wskazówki**: Użyj aliasów Azure Policy do tworzenia zasad niestandardowych w celu inspekcji lub wymuszania konfiguracji Azure Automation i powiązanych zasobów. Możesz również użyć wbudowanych definicji Azure Policy.

Ponadto Azure Resource Manager ma możliwość eksportowania szablonu w JavaScript Object Notation (JSON), który powinien zostać sprawdzony, aby upewnić się, że konfiguracje spełniają/przekroczą wymagania dotyczące zabezpieczeń organizacji.

Możesz również użyć zaleceń z Azure Security Center jako bezpiecznej linii bazowej konfiguracji dla zasobów platformy Azure.

* [Jak wyświetlić dostępne aliasy Azure Policy](/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

* [Samouczek: Tworzenie zasad i zarządzanie nimi w celu wymuszenia zgodności](../governance/policy/tutorials/create-and-manage.md)

* [Przykładowe Azure Policy wbudowane dla Azure Automation](./policy-samples.md)

* [Eksport jednego i kilku zasobów do szablonu w Azure Portal](../azure-resource-manager/templates/export-template-portal.md)

* [Zalecenia dotyczące zabezpieczeń — Przewodnik referencyjny](../security-center/recommendations-reference.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: Ustanów bezpieczne konfiguracje systemów operacyjnych

**Wskazówki**: Azure Automation obecnie nie uwidaczniają maszyn wirtualnych lub systemu operacyjnego wielodostępnego elementu Runbook z wieloma dzierżawcami. Jest to obsługiwane przez platformę. Ta kontrolka nie ma zastosowania, jeśli używasz wbudowanej usługi Box bez hybrydowych procesów roboczych elementów Runbook.

W przypadku korzystania z funkcji hybrydowego procesu roboczego elementu Runbook Użyj zalecenia Azure Security Center [Koryguj luki w zabezpieczeniach w konfiguracjach zabezpieczeń na Virtual Machines], aby zachować konfiguracje zabezpieczeń na maszynach wirtualnych.

* [Jak monitorować zalecenia dotyczące Azure Security Center](../security-center/security-center-recommendations.md)

* [Jak skorygować Azure Security Center zalecenia](../security-center/security-center-remediate-recommendations.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: obsługa bezpiecznych konfiguracji zasobów platformy Azure

**Wskazówki**: użyj szablonów Azure Resource Manager i Azure Policy, aby bezpiecznie skonfigurować zasoby platformy Azure skojarzone z programem Azure Automation. Szablony Azure Resource Manager są plikami opartymi na notacji JSON służącymi do wdrażania zasobów platformy Azure, a wszystkie szablony niestandardowe muszą być przechowywane i konserwowane bezpiecznie w repozytorium kodu. Użyj funkcji integracji kontroli źródła, aby zapewnić aktualność elementów Runbook na koncie usługi Automation przy użyciu skryptów w repozytorium kontroli źródła. Aby wymusić bezpieczne ustawienia w ramach zasobów platformy Azure, użyj Azure Policy [Odmów] i [Wdróż, jeśli nie istnieje].

* [Korzystanie z integracji kontroli źródła](./source-control-integration.md)

* [Informacje na temat tworzenia szablonów Azure Resource Manager](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)

* [Jak skonfigurować Azure Policy i zarządzać nimi](../governance/policy/tutorials/create-and-manage.md)

* [Zrozumienie Azure Policy efektów](../governance/policy/concepts/effects.md)

* [Wdróż konto usługi Automation przy użyciu szablonu Azure Resource Manager](./quickstart-create-automation-account-template.md#deploy-the-template)

* [Przykładowe Azure Policy wbudowane dla Azure Automation](./policy-samples.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: Zachowaj konfiguracje bezpiecznego systemu operacyjnego

**Wskazówki**: Azure Automation obecnie nie uwidaczniają maszyn wirtualnych lub systemu operacyjnego wielodostępnego elementu Runbook z wieloma dzierżawcami, a ta usługa jest obsługiwana przez platformę. Ta kontrolka nie ma zastosowania, jeśli używasz wbudowanej usługi Box bez hybrydowych procesów roboczych elementów Runbook.

W przypadku korzystania z funkcji hybrydowego procesu roboczego elementu Runbook istnieje kilka opcji utrzymywania bezpiecznej konfiguracji dla usługi Azure Virtual Machines na potrzeby wdrożenia:

- Szablony Azure Resource Manager: są to pliki w formacie JSON używane do wdrożenia maszyny wirtualnej z Azure Portal, a szablon niestandardowy musi być zachowany. Firma Microsoft przeprowadza konserwację szablonów podstawowych.
- Niestandardowy wirtualny dysk twardy (VHD): w niektórych przypadkach może być wymagane posiadanie niestandardowych plików VHD, takich jak w przypadku złożonych środowisk, które nie mogą być zarządzane za pomocą innych metod.
- Azure Automation konfiguracja stanu: po wdrożeniu podstawowego systemu operacyjnego może to służyć do bardziej szczegółowego kontrolowania ustawień i wymuszania przez strukturę automatyzacji.

W większości scenariuszy szablony maszyn wirtualnych firmy Microsoft połączone z konfiguracją stanu Azure Automation mogą pomóc w spotkaniu i utrzymaniu wymagań w zakresie zabezpieczeń.

* [Informacje na temat pobierania szablonu maszyny wirtualnej](../virtual-machines/windows/download-template.md)

* [Informacje na temat tworzenia szablonów ARM](../virtual-machines/windows/ps-template.md)

* [Jak przekazać niestandardowy wirtualny dysk twardy maszyny wirtualnej na platformę Azure](/azure-stack/operator/azure-stack-add-vm-image?view=azs-1910)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: bezpiecznie przechowuj konfigurację zasobów platformy Azure

**Wskazówki**: za pomocą usługi Azure DevOps bezpiecznie przechowuj swój kod, taki jak niestandardowe zasady platformy Azure, szablony Azure Resource Manager i skrypty konfiguracji żądanego stanu, i zarządzaj nimi. Aby uzyskać dostęp do zasobów zarządzanych w usłudze Azure DevOps, można udzielić lub odmówić uprawnień do określonych użytkowników, wbudowanych grup zabezpieczeń lub grup zdefiniowanych w Azure Active Directory, jeśli są zintegrowane z usługą Azure DevOps lub Active Directory w przypadku integracji z programem TFS. Użyj funkcji integracji kontroli źródła, aby zapewnić aktualność elementów Runbook na koncie usługi Automation przy użyciu skryptów w repozytorium kontroli źródła.

* [Jak przechowywać kod w usłudze Azure DevOps](/azure/devops/repos/git/gitworkflow?view=azure-devops)

* [Informacje o uprawnieniach i grupach na platformie Azure DevOps](/azure/devops/organizations/security/about-permissions)

* [Korzystanie z integracji kontroli źródła](./source-control-integration.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: bezpieczne przechowywanie niestandardowych obrazów systemu operacyjnego

**Wskazówki**: Azure Automation obecnie nie uwidaczniają maszyn wirtualnych lub systemu operacyjnego wielodostępnego elementu Runbook z wieloma dzierżawcami, a ta usługa jest obsługiwana przez platformę. Ta kontrolka nie ma zastosowania, jeśli używasz wbudowanej usługi Box bez hybrydowych procesów roboczych elementów Runbook.

W przypadku korzystania z funkcji hybrydowego procesu roboczego elementu Runbook upewnij się, że masz prawidłowo ograniczenie dostępu do obrazu niestandardowego systemu operacyjnego znajdującego się na koncie magazynu, tak aby tylko autoryzowani użytkownicy mieli dostęp do tego obrazu.

* [Opis kontroli RBAC platformy Azure](../role-based-access-control/rbac-and-directory-admin-roles.md)

* [Jak skonfigurować usługę Azure RBAC](../role-based-access-control/quickstart-assign-role-user-portal.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: Wdrażanie narzędzi do zarządzania konfiguracją dla zasobów platformy Azure

**Wskazówki**: Definiowanie i implementowanie standardowych konfiguracji zabezpieczeń dla zasobów platformy Azure przy użyciu Azure Policy. Użyj aliasów Azure Policy, aby utworzyć zasady niestandardowe do inspekcji lub wymuszania konfiguracji sieci zasobów platformy Azure. Mogą również korzystać z wbudowanych definicji zasad związanych z konkretnymi zasobami.

* [Jak skonfigurować Azure Policy i zarządzać nimi](../governance/policy/tutorials/create-and-manage.md)

* [Jak używać aliasów](../governance/policy/concepts/definition-structure.md#aliases)

* [Przykładowe Azure Policy wbudowane dla Azure Automation](./policy-samples.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7,8: Wdrażanie narzędzi do zarządzania konfiguracją dla systemów operacyjnych

**Wskazówki**: Azure Automation obecnie nie uwidaczniają maszyn wirtualnych lub systemu operacyjnego wielodostępnego elementu Runbook z wieloma dzierżawcami, a ta usługa jest obsługiwana przez platformę. Ta kontrolka nie ma zastosowania, jeśli używasz wbudowanej usługi Box bez hybrydowych procesów roboczych elementów Runbook.

W przypadku korzystania z funkcji hybrydowego procesu roboczego elementu Runbook Użyj konfiguracji stanu Azure Automation w ramach procesów roboczych elementów Runbook, która jest usługą zarządzania konfiguracją dla węzłów w chmurze lub lokalnego centrum danych. Umożliwia szybkie i łatwe skalowanie na tysiącach maszyn z poziomu centralnej, bezpiecznej lokalizacji. Można łatwo dołączać maszyny, przypisywać do nich konfiguracje deklaracyjne i wyświetlać raporty pokazujące zgodność poszczególnych maszyn z wybranym określonym stanem.

* [Dołączanie maszyn w celu zarządzania przez Azure Automation konfigurację stanu](./automation-dsc-onboarding.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: Zaimplementuj automatyczne monitorowanie konfiguracji dla zasobów platformy Azure

**Wskazówki**: Użyj Azure Policy do alertów i inspekcji konfiguracji zasobów platformy Azure, zasady mogą służyć do wykrywania określonego zasobu, który nie jest skonfigurowany za pomocą prywatnego punktu końcowego.

Korzystając z funkcji hybrydowego procesu roboczego elementu Runbook, Skorzystaj z Azure Security Center, aby przeprowadzić skanowanie linii bazowej dla maszyn wirtualnych platformy Azure. Dodatkowe metody automatycznej konfiguracji obejmują konfigurację stanu Azure Automation.

* [Jak skorygować zalecenia w Azure Security Center](../security-center/security-center-remediate-recommendations.md)

* [Wprowadzenie do konfiguracji stanu Azure Automation](./automation-dsc-getting-started.md)

* [Jak skonfigurować Azure Policy i zarządzać nimi](../governance/policy/tutorials/create-and-manage.md)

* [Przykładowe Azure Policy wbudowane dla Azure Automation](./policy-samples.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: Zaimplementuj automatyczne monitorowanie konfiguracji dla systemów operacyjnych

**Wskazówki**: Oferta Azure Automation nie uwidacznia obecnie podstawowych maszyn wirtualnych lub systemu operacyjnego z wieloma dzierżawcami, a ta usługa jest obsługiwana przez platformę. Ta kontrolka nie ma zastosowania, jeśli używasz wbudowanej usługi Box bez hybrydowych procesów roboczych.

W przypadku korzystania z funkcji hybrydowego procesu roboczego elementu Runbook Użyj konfiguracji stanu Azure Automation dla procesów roboczych elementów Runbook, która jest usługą zarządzania konfiguracją dla węzłów konfiguracji żądanego stanu (DSC) w dowolnym lokalnym centrum danych. Umożliwia szybkie i łatwe skalowanie na tysiącach maszyn z poziomu centralnej, bezpiecznej lokalizacji. Można łatwo dołączać maszyny, przypisywać do nich konfiguracje deklaracyjne i wyświetlać raporty pokazujące zgodność poszczególnych maszyn z wybranym określonym stanem.

* [Dołączanie maszyn w celu zarządzania przez Azure Automation konfigurację stanu](./automation-dsc-onboarding.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="711-manage-azure-secrets-securely"></a>7,11: bezpieczne zarządzanie wpisami tajnymi platformy Azure

**Wskazówki**: Użyj tożsamość usługi zarządzanej w połączeniu z Azure Key Vault, aby uprościć i zabezpieczyć poufne Zarządzanie aplikacjami w chmurze.

* [Korzystanie z kluczy zarządzanych przez klienta dla konta usługi Automation](./automation-secure-asset-encryption.md#use-of-customer-managed-keys-for-an-automation-account)

* [Używanie uwierzytelniania elementu Runbook z tożsamościami zarządzanymi](./automation-hrw-run-runbooks.md#runbook-auth-managed-identities)

* [Jak utworzyć Key Vault](../key-vault/secrets/quick-create-portal.md)

* [Jak przeprowadzić uwierzytelnianie w Key Vault](../key-vault/general/authentication.md)

* [Jak przypisać zasady dostępu Key Vault](../key-vault/general/assign-access-policy-portal.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: bezpieczne i automatyczne zarządzanie tożsamościami

**Wskazówki**: używanie tożsamości zarządzanych do świadczenia usług platformy Azure z automatyczną tożsamością zarządzaną w usłudze Azure AD. Tożsamości zarządzane umożliwiają uwierzytelnianie w dowolnej usłudze, która obsługuje uwierzytelnianie usługi Azure AD, w tym Key Vault, bez żadnych poświadczeń w kodzie.

* [Jak skonfigurować tożsamości zarządzane](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

* [Korzystanie z kluczy zarządzanych przez klienta dla konta usługi Automation](./automation-secure-asset-encryption.md#use-of-customer-managed-keys-for-an-automation-account)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: eliminowanie nieprzewidzianego narażenia na poświadczenia

**Wskazówki**: Implementuj skaner poświadczeń, aby identyfikować poświadczenia w kodzie. Skaner poświadczeń zachęca również do przemieszczania odnalezionych poświadczeń do bardziej bezpiecznych lokalizacji, takich jak Azure Key Vault.

* [Jak skonfigurować skaner poświadczeń](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

## <a name="malware-defense"></a>Ochrona przed złośliwym oprogramowaniem

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Obrona złośliwego oprogramowania](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8,1: Użyj centralnie zarządzanego oprogramowania chroniącego przed złośliwym oprogramowaniem

**Wskazówki**: Oferta Azure Automation nie uwidacznia obecnie podstawowych maszyn wirtualnych lub systemu operacyjnego z wieloma dzierżawcami, a ta usługa jest obsługiwana przez platformę. Ta kontrolka nie ma zastosowania, jeśli używasz wbudowanej usługi Box bez hybrydowych procesów roboczych elementów Runbook.

W przypadku korzystania z funkcji hybrydowego procesu roboczego elementu Runbook usługa Microsoft Anti-Malware dla maszyn wirtualnych z systemem Windows jest używana do ciągłego monitorowania i obrony zasobów programu Runbook Worker.

* [Jak skonfigurować oprogramowanie antywirusowe firmy Microsoft dla Cloud Services i Virtual Machines](../security/fundamentals/antimalware.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: przeskanuj pliki przed przekazaniem do zasobów platformy Azure, które nie są obliczeniowe

**Wskazówki**: nie dotyczy; Azure Automation jako usługa nie przechowuje plików. Oprogramowanie chroniące przed złośliwym oprogramowaniem firmy Microsoft jest włączone na podstawowym hoście obsługującym usługi platformy Azure (na przykład Azure Automation), ale nie jest ono uruchamiane w Twojej zawartości.

* [Informacje na temat ochrony przed złośliwym oprogramowaniem firmy Microsoft Cloud Services i Virtual Machines](../security/fundamentals/antimalware.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8,3: Upewnij się, że oprogramowanie chroniące przed złośliwym oprogramowaniem i podpisy zostały zaktualizowane

**Wskazówki**: Azure Automation obecnie nie uwidaczniają maszyn wirtualnych lub systemu operacyjnego wielodostępnego elementu Runbook z wieloma dzierżawcami, a ta usługa jest obsługiwana przez platformę. Ta kontrolka nie ma zastosowania, jeśli używasz wbudowanej usługi Box bez hybrydowych procesów roboczych elementów Runbook.

W przypadku korzystania z funkcji hybrydowego procesu roboczego elementu Runbook Użyj usługi Microsoft chroniącej przed złośliwym kodem dla platformy Azure, aby automatycznie zainstalować najnowszą aktualizację sygnatury, platformy i aparatu domyślnie w programie Runbook Worker. Postępuj zgodnie z zaleceniami w Azure Security Center: "COMPUTE &amp; Apps", aby upewnić się, że wszystkie punkty końcowe są aktualne przy użyciu najnowszych sygnatur. System operacyjny Windows może być dodatkowo chroniony przy użyciu dodatkowych zabezpieczeń w celu ograniczenia ryzyka ataków na ataki z wirusami lub złośliwym oprogramowaniem z usługą Microsoft Defender Advanced Threat Protection, która integruje się z Azure Security Center.

* [Jak wdrożyć program Microsoft chroniący przed złośliwym kodem dla platformy Azure Cloud Services i Virtual Machines](../security/fundamentals/antimalware.md)

* [Zaawansowana ochrona przed zagrożeniami w usłudze Microsoft Defender](/windows/security/threat-protection/microsoft-defender-atp/onboard-configure)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

## <a name="data-recovery"></a>Odzyskiwanie danych

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: odzyskiwanie danych](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: Zapewnij regularne zautomatyzowane przywracanie awaryjne

**Wskazówki**: Użyj Azure Resource Manager, aby wdrożyć konta Azure Automation i powiązane zasoby. Azure Resource Manager zapewnia możliwość eksportowania szablonów, które mogą być używane jako kopie zapasowe do przywracania kont Azure Automation i powiązanych zasobów. Użyj Azure Automation, aby regularnie wywoływać interfejs API eksportowania szablonu Azure Resource Manager.

Użyj funkcji integracji kontroli źródła, aby zapewnić aktualność elementów Runbook na koncie usługi Automation przy użyciu skryptów w repozytorium kontroli źródła.

* [Omówienie usługi Azure Resource Manager](../azure-resource-manager/management/overview.md)

* [Azure Resource Manager dokumentacja szablonu dla Azure Automation zasobów](/azure/templates/microsoft.automation/allversions)

* [Tworzenie konta usługi Automation przy użyciu szablonu Azure Resource Manager](./quickstart-create-automation-account-template.md)

* [Eksport jednego i kilku zasobów do szablonu w Azure Portal](../azure-resource-manager/templates/export-template-portal.md)

* [Grupy zasobów — Eksportowanie szablonu](/rest/api/resources/resourcegroups/exporttemplate)

* [Wprowadzenie do Azure Automation](./automation-intro.md)

* [Jak utworzyć kopię zapasową kluczy magazynu kluczy na platformie Azure](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0)

* [Korzystanie z kluczy zarządzanych przez klienta dla konta usługi Automation](./automation-secure-asset-encryption.md#use-of-customer-managed-keys-for-an-automation-account)

* [Korzystanie z integracji kontroli źródła](./source-control-integration.md)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: wykonaj kompletne kopie zapasowe systemu i Utwórz kopię zapasową wszystkich kluczy zarządzanych przez klienta

**Wskazówki**: Użyj Azure Resource Manager, aby wdrożyć konta Azure Automation i powiązane zasoby. Azure Resource Manager zapewnia możliwość eksportowania szablonów, które mogą być używane jako kopie zapasowe do przywracania kont Azure Automation i powiązanych zasobów. Użyj Azure Automation, aby regularnie wywoływać interfejs API eksportowania szablonu Azure Resource Manager. Utwórz kopię zapasową kluczy zarządzanych przez klienta w ramach Azure Key Vault. Elementy Runbook można eksportować do plików skryptów przy użyciu programu Azure Portal lub PowerShell.

* [Omówienie usługi Azure Resource Manager](../azure-resource-manager/management/overview.md)

* [Azure Resource Manager dokumentacja szablonu dla Azure Automation zasobów](/azure/templates/microsoft.automation/allversions)

* [Tworzenie konta usługi Automation przy użyciu szablonu Azure Resource Manager](./quickstart-create-automation-account-template.md)

* [Eksport jednego i kilku zasobów do szablonu w Azure Portal](../azure-resource-manager/templates/export-template-portal.md)

* [Grupy zasobów — Eksportowanie szablonu](/rest/api/resources/resourcegroups/exporttemplate)

* [Wprowadzenie do Azure Automation](./automation-intro.md)

* [Jak utworzyć kopię zapasową kluczy magazynu kluczy na platformie Azure](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0)

* [Korzystanie z kluczy zarządzanych przez klienta dla konta usługi Automation](./automation-secure-asset-encryption.md#use-of-customer-managed-keys-for-an-automation-account)

* [Kopia zapasowa danych platformy Azure dla kont usługi Automation](./automation-managing-data.md#data-backup)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: Weryfikuj wszystkie kopie zapasowe, w tym klucze zarządzane przez klienta

**Wskazówki**: Upewnij się, że w razie potrzeby okresowo Wdrażaj regularnie wdrożenia szablonów Azure Resource Manager. Testowanie przywracania kopii zapasowych kluczy zarządzanych przez klienta.

* [Wdrażanie zasobów za pomocą szablonów ARM i Azure Portal](../azure-resource-manager/templates/deploy-portal.md)

* [Jak przywrócić klucze magazynu kluczy na platformie Azure](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

* [Korzystanie z kluczy zarządzanych przez klienta dla konta usługi Automation](./automation-secure-asset-encryption.md#use-of-customer-managed-keys-for-an-automation-account)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: zapewnianie ochrony kopii zapasowych i kluczy zarządzanych przez klienta

**Wskazówki**: Użyj usługi Azure DevOps, aby bezpiecznie przechowywać kod, taki jak szablony Azure Resource Manager i zarządzać nimi. Aby chronić zasoby zarządzane w usłudze Azure DevOps, można udzielić lub odmówić uprawnień do określonych użytkowników, wbudowanych grup zabezpieczeń lub grup zdefiniowanych w Azure Active Directory, jeśli są zintegrowane z usługą Azure DevOps lub Active Directory w przypadku integracji z programem TFS.

Użyj funkcji integracji kontroli źródła, aby zapewnić aktualność elementów Runbook na koncie usługi Automation przy użyciu skryptów w repozytorium kontroli źródła.

* [Jak przechowywać kod w usłudze Azure DevOps](/azure/devops/repos/git/gitworkflow?view=azure-devops)

* [Informacje o uprawnieniach i grupach na platformie Azure DevOps](/azure/devops/organizations/security/about-permissions)

* [Korzystanie z integracji kontroli źródła](./source-control-integration.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

## <a name="incident-response"></a>Reagowanie na zdarzenia

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: odpowiedź na zdarzenia](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: Tworzenie przewodnika odpowiedzi na zdarzenia

**Wskazówki**: Tworzenie przewodnika odpowiedzi na zdarzenia dla organizacji. Upewnij się, że istnieją zarejestrowane plany reakcji na zdarzenia, które definiują wszystkie role pracowników, a także etapy obsługi zdarzeń/zarządzania z wykrywania do oceny po zdarzeniu.

* [Wskazówki dotyczące tworzenia własnego procesu reagowania na zdarzenia zabezpieczeń](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [Anatomia incydentu centrum Microsoft Security Response](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

* [Klient może również korzystać z przewodnika obsługi zdarzeń związanych z bezpieczeństwem programu NIST, aby pomóc w tworzeniu własnego planu reagowania na zdarzenia](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: Tworzenie oceny incydentu i procedury priorytetyzacji

**Wskazówki**: Security Center przypisuje ważność do każdego alertu, aby pomóc w ustaleniu, które alerty należy najpierw zbadać. Ważność jest oparta na tym, jak dobrze Security Center znajduje się w wyszukiwaniu lub analitycznym używanym do wystawiania alertu, a także poziom pewności, że istniało złośliwy wpływ na działanie, które prowadziło do alertu.

Dodatkowo jasno Oznacz subskrypcje (na przykład Produkcja, inne niż prod) przy użyciu tagów i Utwórz system nazewnictwa, aby jasno identyfikować i klasyfikować zasoby platformy Azure, szczególnie te, które przetwarzają dane poufne. Odpowiedzialność za korygowanie alertów zależy od zagrożenia dla zasobów platformy Azure i środowiska, w którym wystąpiło zdarzenie.

* [Alerty zabezpieczeń w Centrum zabezpieczeń Azure](../security-center/security-center-alerts-overview.md)

* [Organizowanie zasobów platformy Azure przy użyciu tagów](../azure-resource-manager/management/tag-resources.md)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="103-test-security-response-procedures"></a>10,3: procedury odpowiedzi na zabezpieczenia testowe

**Wskazówki**: przeprowadzanie ćwiczeń w celu przetestowania możliwości reagowania na zdarzenia systemów w regularnych erzeach w celu ochrony zasobów platformy Azure. Zidentyfikuj słabe punkty i przerwy i popraw plan zgodnie z wymaganiami.

* [Przewodnik po publikacji NIST, który umożliwia testowanie, uczenie i wykonywanie programów dla planów i możliwości IT](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: Podaj szczegóły kontaktu dotyczącego zabezpieczeń i Skonfiguruj powiadomienia dotyczące alertów dotyczących zdarzeń związanych z zabezpieczeniami

**Wskazówki**: informacje kontaktowe dotyczące zdarzenia zabezpieczeń będą używane przez firmę Microsoft do skontaktowania się z Tobą, jeśli firma Microsoft Security Response Center (MSRC) wykryje, że dostęp do danych zostały nadane przez nielegalną lub nieautoryzowaną stronę. Przejrzyj zdarzenia po fakcie, aby upewnić się, że problemy zostały rozwiązane.

* [Jak ustawić kontakt z zabezpieczeniami Azure Security Center](../security-center/security-center-provide-security-contact-details.md)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: Uwzględnij alerty zabezpieczeń w systemie odpowiedzi na zdarzenia

**Wskazówki**: eksportowanie alertów i zaleceń dotyczących Azure Security Center przy użyciu funkcji eksportu ciągłego, która pomaga identyfikować zagrożenia dla zasobów platformy Azure. Eksport ciągły umożliwia wyeksportowanie alertów i zaleceń ręcznie lub w stały sposób ciągły. Możesz użyć łącznika danych Azure Security Center do przesyłania strumieniowego alertów do usługi Azure wskaźnikowej.

* [Jak skonfigurować eksport ciągły](../security-center/continuous-export.md)

* [Jak przesłać strumieniowo alerty do usługi Azure wskaźnikowego](../sentinel/connect-azure-security-center.md)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: Automatyzowanie odpowiedzi na alerty zabezpieczeń

**Wskazówki**: Użyj funkcji automatyzacji przepływu pracy w Azure Security Center, aby automatycznie wyzwalać odpowiedzi poprzez "Logic Apps" dotyczące alertów zabezpieczeń i zaleceń dotyczących ochrony zasobów platformy Azure.

* [Jak skonfigurować automatyzację przepływu pracy i Logic Apps](../security-center/workflow-automation.md)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

## <a name="penetration-tests-and-red-team-exercises"></a>Testy penetracyjne i ćwiczenia typu „red team”

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: testy penetracji i czerwone ćwiczenia zespołu](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: Przeprowadź regularne testowanie penetracji zasobów platformy Azure i zadbaj o skorygowanie wszystkich krytycznych ustaleń dotyczących zabezpieczeń

**Wskazówki**: Postępuj zgodnie z zasadami firmy Microsoft dotyczącymi zaangażowania, aby upewnić się, że testy penetracji nie naruszają zasad firmy Microsoft. Korzystaj z strategii firmy Microsoft i wykonywania testów na żywo z obsługą tworzenia zespołu, usług i aplikacji w chmurze, które są zarządzane przez firmę Microsoft.

* [Reguły testowania penetracji zaangażowania](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

* [Microsoft Cloud Tworzenie zespołu Red.](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: udostępnione

## <a name="next-steps"></a>Następne kroki

- Zobacz [test porównawczy zabezpieczeń platformy Azure](../security/benchmarks/overview.md)
- Dowiedz się więcej o [punktach odniesienia zabezpieczeń platformy Azure](../security/benchmarks/security-baselines-overview.md)
