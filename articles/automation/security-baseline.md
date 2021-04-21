---
title: Punkt odniesienia zabezpieczeń platformy Azure dla usługi Automation
description: Punkt odniesienia zabezpieczeń usługi Automation zawiera wskazówki proceduralne i zasoby dotyczące wdrażania zaleceń dotyczących zabezpieczeń określonych w te testach porównawczych zabezpieczeń platformy Azure.
author: msmbaldwin
ms.service: automation
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark, devx-track-azurepowershell
ms.openlocfilehash: 7f87fcac0c1b07e108082c4b4f48bde046dc63c7
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107834502"
---
# <a name="azure-security-baseline-for-automation"></a>Punkt odniesienia zabezpieczeń platformy Azure dla usługi Automation

Ten punkt odniesienia zabezpieczeń stosuje wskazówki z testu porównawczego zabezpieczeń platformy [Azure w wersji 1.0](../security/benchmarks/overview-v1.md) do Azure Automation. Test porównawczy zabezpieczeń platformy Azure zawiera zalecenia dotyczące sposobu zabezpieczania rozwiązań w chmurze na platformie Azure.
Zawartość jest pogrupowana według **kontrolek zabezpieczeń zdefiniowanych** przez test porównawczy zabezpieczeń platformy Azure i powiązanych wskazówek dotyczących Azure Automation. **Kontrolki** nie dotyczy Azure Automation zostały wykluczone.

 
Aby zobaczyć, Azure Automation mapowanie na test porównawczy zabezpieczeń platformy Azure, zobacz pełny Azure Automation [mapowania punktów odniesienia zabezpieczeń](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Bezpieczeństwo sieci

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: bezpieczeństwo sieci](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1. Ochrona zasobów platformy Azure w sieciach wirtualnych

**Wskazówki:** Azure Automation nie obsługuje jeszcze Azure Private Link ograniczania dostępu do usługi za pośrednictwem prywatnych punktów końcowych. Element runbook, które uwierzytelniają się i uruchamiają względem zasobów na platformie Azure, działają w piaskownicy platformy Azure i wykorzystują współdzielone zasoby zaplecza, które firma Microsoft jest odpowiedzialna za izolowanie od siebie nawzajem; Ich sieć jest nieograniczona i może uzyskać dostęp do zasobów publicznych. Azure Automation nie ma obecnie integracji z siecią wirtualną dla sieci prywatnych poza obsługą hybrydowych pracowników runbook. Ta kontrolka nie ma zastosowania, jeśli używasz usługi dostępnej w trybie out-of-box bez hybrydowych pracowników runbook.

Aby uzyskać dalszą izolację dla swoich runbook, możesz użyć hybrydowych procesu roboczego runbook uruchomionych na maszynach wirtualnych platformy Azure. Podczas tworzenia maszyny wirtualnej platformy Azure należy utworzyć sieć wirtualną lub użyć istniejącej sieci wirtualnej i skonfigurować maszyny wirtualne przy użyciu podsieci. Upewnij się, że wszystkie wdrożone podsieci mają sieciową grupę zabezpieczeń zastosowaną z kontrolą dostępu do sieci specyficzną dla zaufanych portów i źródeł aplikacji. Aby zapoznać się z wymaganiami specyficznymi dla usługi, zapoznaj się z zaleceniami zabezpieczeń dla tej określonej usługi. Alternatywnie, jeśli masz określone wymaganie, Azure Firewall można również użyć, aby je spełnić.

- [Sieci wirtualne i maszyny wirtualne na platformie Azure](../virtual-machines/network-overview.md)

- [Jak utworzyć Virtual Network](../virtual-network/quick-create-portal.md)

- [Tworzenie sieciowej grupy zabezpieczeń za pomocą konfiguracji zabezpieczeń](../virtual-network/tutorial-filter-network-traffic.md)

- [Jak wdrożyć i skonfigurować Azure Firewall](../firewall/tutorial-firewall-deploy-portal.md)

- [Środowisko wykonywania runbook](./automation-runbook-execution.md#runbook-execution-environment)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2: Monitorowanie i rejestrowanie konfiguracji i ruchu sieci wirtualnych, podsieci i interfejsów sieciowych

**Wskazówki:** Azure Automation obecnie nie ma integracji z siecią wirtualną dla sieci prywatnych poza obsługą hybrydowych pracowników runbook. Ta kontrolka nie ma zastosowania, jeśli używasz usługi out-of-the-box bez hybrydowych pracowników runbook.

Jeśli używasz hybrydowych pracowników runbook, których kopią zapasową są maszyny wirtualne platformy Azure, upewnij się, że podsieć zawierająca te przepływy pracy jest włączona przy użyciu sieciowej grupy zabezpieczeń i skonfiguruj dzienniki przepływu w celu przekazywania dzienników do konta magazynu w celu inspekcji ruchu. Możesz również przekazać dzienniki przepływu sieciowych grup zabezpieczeń do obszaru roboczego usługi Log Analytics i użyć usługi Analiza ruchu, aby zapewnić wgląd w przepływ ruchu w chmurze platformy Azure. Niektóre zalety usługi Analiza ruchu to możliwość wizualizacji aktywności sieciowej i identyfikowania punktów aktywności, identyfikowania zagrożeń bezpieczeństwa, zrozumienia wzorców przepływu ruchu i identyfikowania błędnych konfiguracji sieci.

Chociaż reguły sieciowej grupy zabezpieczeń i trasy zdefiniowane przez użytkownika nie mają zastosowania do prywatnego punktu końcowego, dzienniki przepływu sieciowej grupy zabezpieczeń i informacje monitorowania połączeń wychodzących są nadal obsługiwane i mogą być używane.

- [How to Enable NSG Flow Logs](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Jak włączyć i używać Analiza ruchu](../network-watcher/traffic-analytics.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Odmowa komunikacji ze znanymi złośliwymi adresami IP

**Wskazówki:** Azure Automation nie ma obecnie integracji z siecią wirtualną dla sieci prywatnych poza obsługą hybrydowych pracowników runbook. Ta kontrolka nie ma zastosowania, jeśli używasz usługi out-of-the-box bez hybrydowych pracowników runbook.

Jeśli używasz hybrydowych pracowników runbook zabezpieczonych maszynami wirtualnymi platformy Azure, włącz standardową ochronę przed atakami DDoS (Distributed Denial of Service) w sieciach wirtualnych hostujących hybrydowe środowiska runbook. Za pomocą Azure Security Center zintegrowanej analizy zagrożeń można uniemożliwić komunikację ze znanymi złośliwymi adresami IP.  Skonfiguruj Azure Firewall w każdym z segmentów Virtual Network, z włączoną analizą zagrożeń, a następnie skonfiguruj alert i odmów dla złośliwego ruchu sieciowego. 

Możesz użyć Azure Security Center typu Just In Time Network, aby ograniczyć narażenie maszyn wirtualnych z systemem Windows na zatwierdzone adresy IP przez ograniczony czas.  Ponadto należy użyć Azure Security Center adaptacyjnego wzmacniania zabezpieczeń sieci dla konfiguracji sieciowej organizacji zabezpieczeń, aby ograniczyć porty i źródłowe ip na podstawie rzeczywistego ruchu i analizy zagrożeń.

- [Jak skonfigurować ochronę przed DDoS](../ddos-protection/manage-ddos-protection.md)

- [Jak wdrożyć Azure Firewall](../firewall/tutorial-firewall-deploy-portal.md)

- [Understand Azure Security Center Integrated Threat Intelligence](../security-center/azure-defender.md)

- [Opis Azure Security Center adaptacyjnego wzmacniania zabezpieczeń sieci](../security-center/security-center-adaptive-network-hardening.md)

- [Understand Azure Security Center Just In Time Network Access Control](../security-center/security-center-just-in-time.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="15-record-network-packets"></a>1.5. Rejestrowanie pakietów sieciowych

**Wskazówki:** usługa Azure Automation obecnie nie ma integracji z siecią wirtualną dla sieci prywatnych poza obsługą hybrydowych pracowników runbook. Ta kontrola nie ma zastosowania, jeśli używasz usługi dostępnej poza usługą bez hybrydowych pracowników.

Jeśli używasz hybrydowych pływów runbook, których kopią są maszyny wirtualne platformy Azure, możesz zarejestrować dzienniki przepływu sieciowej organizacji zabezpieczeń na koncie magazynu w celu wygenerowania rekordów przepływu dla usługi Azure Virtual Machines, które działają jako pracownicy runbook. Podczas badania nietypowych działań można włączyć przechwytywanie pakietów Network Watcher, aby ruch sieciowy mógł być sprawdzany pod uwagę pod uwagę nietypowych i nieoczekiwanych działań.

- [Jak włączyć dzienniki przepływu sieciowej organizacji zabezpieczeń](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Jak włączyć Network Watcher](../network-watcher/network-watcher-create.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6. Wdrażanie sieciowych systemów wykrywania/zapobiegania włamań (IDS/IPS)

**Wskazówki:** Azure Automation nie ma obecnie integracji z siecią wirtualną dla sieci prywatnych poza obsługą hybrydowych pracowników runbook. Ta kontrolka nie ma zastosowania, jeśli używasz usługi out-of-the-box bez hybrydowych pracowników runbook.

Jeśli używasz hybrydowych procesów roboczych runbook hostowanych na maszynach wirtualnych platformy Azure, możesz połączyć przechwytywanie pakietów udostępniane przez narzędzia usług Network Watcher i open source IDS w celu przeprowadzenia wykrywania włamań do sieci dla szerokiego zakresu zagrożeń dla tych maszyn roboczych. Ponadto można wdrażać aplikacje Azure Firewall segmentach Virtual Network odpowiednio, z włączoną analizą zagrożeń i skonfigurowaną na "Alert i odmowa" dla złośliwego ruchu sieciowego.

- [Wykrywanie włamań do sieci przy użyciu Network Watcher i open source internetowych](../network-watcher/network-watcher-intrusion-detection-open-source-tools.md)

- [Jak wdrożyć Azure Firewall](../firewall/tutorial-firewall-deploy-portal.md)

- [Jak skonfigurować alerty za pomocą Azure Firewall](../firewall/threat-intel.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8. Zminimalizuj złożoność i koszty administracyjne związane z regułami zabezpieczeń sieci

**Wskazówki:** Użyj tagów Virtual Network do zdefiniowania kontroli dostępu do sieci w sieciowych grupach zabezpieczeń lub Azure Firewall skonfigurowanych na platformie Azure, które wymagają dostępu do zasobów usługi Automation. Podczas tworzenia reguł zabezpieczeń można użyć tagów usługi zamiast konkretnych adresów IP. Określając nazwę tagu usługi (na przykład GuestAndHybridManagement) w odpowiednim polu źródła lub miejsca docelowego reguły, można zezwolić na ruch dla odpowiedniej usługi lub go odrzucić. Firma Microsoft zarządza prefiksami adresów obejmującymi tag usługi i automatycznie aktualizuje tag usługi w przypadku zmiany adresów.

- [Opis tagów usługi i korzystanie z tych tagów](../virtual-network/service-tags-overview.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Obsługa standardowych konfiguracji zabezpieczeń dla urządzeń sieciowych

**Wskazówki:** Definiowanie i implementowanie standardowych konfiguracji zabezpieczeń dla zasobów sieciowych używanych przez Azure Automation z Azure Policy.

Za pomocą usługi Azure Blueprints można również uprościć wdrożenia platformy Azure na dużą skalę przez pakowanie artefaktów kluczowych środowisk, takich jak szablony usługi Azure Resources Manager, kontrolki RBAC platformy Azure i zasady, w pojedynczej definicji strategii. Możesz zastosować plan do nowych subskrypcji oraz dostosować kontrolę i zarządzanie za pomocą wersji.

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy przykłady dotyczące sieci](../governance/policy/samples/built-in-policies.md#network)

- [Jak utworzyć usługę Azure Blueprint](../governance/blueprints/create-blueprint-portal.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="110-document-traffic-configuration-rules"></a>1.10: Dokumentuj reguły konfiguracji ruchu

**Wskazówki:** użyj tagów dla sieciowych sieci i innych zasobów związanych z zabezpieczeniami sieci i przepływem ruchu. W przypadku poszczególnych reguł sieciowej organizacji sieciowej należy użyć pola "Opis", aby określić potrzebę biznesową i/lub czas trwania (itp.) dla wszystkich reguł, które zezwalają na ruch do/z sieci.

Użyj dowolnej wbudowanej definicji usługi Azure Policy związanej z tagami, takiej jak "Wymagaj tagu i jego wartości", aby upewnić się, że wszystkie zasoby są tworzone za pomocą tagów i powiadamiać o istniejących nieotagowanych zasobach.

Możesz użyć interfejsu Azure PowerShell wiersza polecenia platformy Azure do wyszukiwania lub wykonywania akcji na zasobach na podstawie ich tagów.

- [Jak tworzyć tagi i ich używać](../azure-resource-manager/management/tag-resources.md)

- [Jak utworzyć Virtual Network](../virtual-network/quick-create-portal.md)

- [Jak utworzyć sieciowej grupy zabezpieczeń za pomocą konfiguracji zabezpieczeń](../virtual-network/tutorial-filter-network-traffic.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11. Monitorowanie konfiguracji zasobów sieciowych i wykrywanie zmian za pomocą zautomatyzowanych narzędzi

**Wskazówki:** Użyj dziennika aktywności platformy Azure, aby monitorować konfiguracje zasobów i wykrywać zmiany w zasobach sieciowych. Utwórz alerty w Azure Monitor, które będą wyzwalane w przypadku zmiany krytycznych zasobów.

- [Jak wyświetlać i pobierać zdarzenia dziennika aktywności platformy Azure](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [Jak tworzyć alerty w Azure Monitor](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

## <a name="logging-and-monitoring"></a>Rejestrowanie i monitorowanie

*Aby uzyskać więcej informacji, zobacz [Test porównawczy zabezpieczeń platformy Azure: rejestrowanie i monitorowanie.](../security/benchmarks/security-control-logging-monitoring.md)*

### <a name="22-configure-central-security-log-management"></a>2.2. Konfigurowanie centralnego zarządzania dziennikami zabezpieczeń

**Wskazówki:** Przekazywanie danych dziennika do dzienników Azure Monitor w celu agregowania danych zabezpieczeń generowanych przez Azure Automation zasobów. W Azure Monitor użyj zapytań dzienników do wyszukiwania i przeprowadzania analiz, a następnie użyj kont usługi Azure Storage do przechowywania długoterminowego/archiwalnej. Azure Automation wysyłać stan zadania runbook, strumienie zadań, dane konfiguracji stanu usługi Automation, zarządzanie aktualizacjami oraz dzienniki śledzenia zmian lub spisu do obszaru roboczego usługi Log Analytics. Te informacje są widoczne z interfejsu API Azure Portal, Azure PowerShell i Azure Monitor Logs, który umożliwia wykonywanie prostych badań.

Alternatywnie możesz włączyć i dołączyć dane do rozwiązania Azure Sentinel lub rozwiązania SIEM innej firmy. 

- [Jak do dołączać Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Jak zbierać dzienniki i metryki platformy za pomocą Azure Monitor](../azure-monitor/essentials/diagnostic-settings.md)

- [Jak rozpocząć pracę z Azure Monitor integracją rozwiązania SIEM i rozwiązania SIEM innej firmy](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

- [Przekazywanie danych zadania usługi Azure Automation do dzienników usługi Azure Monitor](automation-manage-send-joblogs-log-analytics.md)

- [Integrowanie konfiguracji DSC z Azure Monitor danych](automation-dsc-diagnostics.md)

- [Obsługiwane regiony połączonego obszaru roboczego usługi Log Analytics](how-to/region-mappings.md)

- [Dzienniki Update Management zapytań](update-management/query-logs.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3. Włączanie rejestrowania inspekcji dla zasobów platformy Azure

**Wskazówki:** włącz Azure Monitor dostępu do dzienników inspekcji i aktywności, w tym źródła zdarzeń, daty, użytkownika, znacznika czasu, adresów źródłowych, adresów docelowych i innych przydatnych elementów. 

- [Jak zbierać dzienniki i metryki platformy za pomocą Azure Monitor](../azure-monitor/essentials/diagnostic-settings.md) 

- [Wyświetlanie i pobieranie zdarzeń dziennika aktywności platformy Azure](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="25-configure-security-log-storage-retention"></a>2.5. Konfigurowanie przechowywania magazynu dzienników zabezpieczeń

**Wskazówki:** w Azure Monitor ustaw okres przechowywania obszaru roboczego usługi Log Analytics zgodnie z przepisami organizacji dotyczącymi zgodności. Użyj kont usługi Azure Storage do przechowywania długoterminowego/archiwalnej.

- [Zmienianie okresu przechowywania danych w u usługi Log Analytics](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

- [Szczegóły przechowywania danych dla kont usługi Automation](./automation-managing-data.md#data-retention)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="26-monitor-and-review-logs"></a>2.6. Monitorowanie i przeglądanie dzienników

**Wskazówki:** analizowanie i monitorowanie dzienników pod celu zachowania anomalii oraz regularne przeglądanie wyników. Użyj Azure Monitor dziennika, aby przeglądać dzienniki i wykonywać zapytania dotyczące danych dziennika.

Alternatywnie można włączyć i dołączyć dane do Azure Sentinel lub rozwiązania SIEM innej firmy. 

- [Jak do dołączać Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Informacje o zapytaniach dzienników w Azure Monitor](../azure-monitor/logs/log-analytics-tutorial.md)

- [Jak wykonywać zapytania niestandardowe w Azure Monitor](../azure-monitor/logs/get-started-queries.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7. Włączanie alertów dla niesłychanych działań

**Wskazówki:** używaj Azure Security Center z Azure Monitor do monitorowania i alertów dotyczących anomalii działań znalezionych w dziennikach zabezpieczeń i zdarzeniach.

Alternatywnie możesz włączyć i włączyć dane, aby Azure Sentinel.

- [Jak do dołączać Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Jak zarządzać alertami w Azure Security Center](../security-center/security-center-managing-and-responding-alerts.md)

- [Jak rejestrować alerty Azure Monitor danych dziennika](../azure-monitor/alerts/tutorial-response.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="29-enable-dns-query-logging"></a>2.9: Włączanie rejestrowania zapytań DNS

**Wskazówki:** Implementowanie rozwiązania innej firmy z Azure Marketplace do rejestrowania DNS zgodnie z potrzebami organizacji.

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

## <a name="identity-and-access-control"></a>Tożsamość i kontrola dostępu

*Aby uzyskać więcej informacji, zobacz Test porównawczy zabezpieczeń platformy [Azure: tożsamość i Access Control](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Utrzymywanie spisu kont administracyjnych

**Wskazówki:** użyj Azure Active Directory wbudowanych ról administratora usługi Azure AD, które można jawnie przypisać i których można używać w zapytaniach. Moduł Azure AD PowerShell umożliwia wykonywanie zapytań ad hoc w celu odnajdywania kont, które są członkami grup administracyjnych. Za każdym razem, gdy używasz kont Uruchom jako konta usługi Automation dla twoich zasobów Runbook, upewnij się, że te jednostki usługi są również śledzone w spisie, ponieważ często mają podwyższony poziom uprawnień. Usuń wszystkie nieużywane konta Uruchom jako, aby zminimalizować narażenie na ataki.

- [Jak uzyskać rolę katalogu w usłudze Azure AD przy użyciu programu PowerShell](/powershell/module/azuread/get-azureaddirectoryrole)

- [Jak uzyskać członków roli katalogu w usłudze Azure AD przy użyciu programu PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember)

- [Usuwanie konta Uruchom jako lub klasycznego konta Uruchom jako](delete-run-as-account.md)

- [Zarządzanie kontem Azure Automation Uruchom jako](manage-runas-account.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Zmień domyślne hasła tam, gdzie ma to zastosowanie

**Wskazówki:** Azure Automation konto nie ma koncepcji haseł domyślnych.  Klienci są odpowiedzialni za aplikacje innych firm i usługi platformy handlowej, które mogą używać domyślnych haseł uruchamianych w oparciu o usługę lub jej hybrydowe środowiska pracy runbook.

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Użyj dedykowanych kont administracyjnych

**Wskazówki:** Tworzenie standardowych procedur operacyjnych dotyczących korzystania z dedykowanych kont administracyjnych. Użyj Azure Security Center zarządzania tożsamościami i dostępem, aby monitorować liczbę kont administracyjnych. Za każdym razem, gdy używasz kont Uruchom jako konta usługi Automation dla twoich zasobów Runbook, upewnij się, że te jednostki usługi są również śledzone w spisie, ponieważ często mają podwyższony poziom uprawnień. Określanie zakresu tych tożsamości przy użyciu najmniej uprzywilejowanych uprawnień, których potrzebują, aby umożliwić wykonywanie zautomatyzowanych procesów przez twoje runbook. Usuń wszystkie nieużywane konta Uruchom jako, aby zminimalizować narażenie na ataki.

Funkcję Just-In-Time/ Just-Enough-Access można również włączyć przy użyciu usługi Azure Active Directory (Azure AD) Privileged Identity Management Privileged Roles for Microsoft Services i Azure Resource Manager.

- [Dowiedz się więcej o Privileged Identity Management](../active-directory/privileged-identity-management/index.yml)

- [Usuwanie konta Uruchom jako lub klasycznego konta Uruchom jako](delete-run-as-account.md)

- [Zarządzanie kontem Azure Automation Uruchom jako](manage-runas-account.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4: Używanie Azure Active Directory jednokrotnego (SSO)

**Wskazówki:** wszędzie tam, gdzie to możliwe, używaj logowania jednokrotnego Azure Active Directory (Azure AD) zamiast konfigurowania pojedynczych poświadczeń autonomicznych dla usługi. Użyj Azure Security Center tożsamości i zarządzania dostępem.

- [Logowanie pojedyncze do aplikacji w usłudze Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

- [Jak monitorować tożsamość i dostęp w Azure Security Center](../security-center/security-center-identity-access.md)

- [Uwierzytelnianie na platformie Azure za pomocą usługi Azure AD](automation-use-azure-ad.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Używaj uwierzytelniania wieloskładnikowego dla Azure Active Directory dostępu opartego na danych

**Wskazówki:** włączanie Azure Active Directory wieloskładnikowego (Azure AD) i postępuj zgodnie z Azure Security Center tożsamości i zarządzania dostępem.

- [Jak włączyć uwierzytelnianie wieloskładnikowe na platformie Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Jak monitorować tożsamość i dostęp w Azure Security Center](../security-center/security-center-identity-access.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6. Użyj dedykowanych maszyn (stacji roboczych z dostępem uprzywilejowanym) do wykonywania wszystkich zadań administracyjnych

**Wskazówki:** Używanie paw z uwierzytelnianiem wieloskładnikowym skonfigurowanym do logowania się i konfigurowania Azure Automation kont w środowiskach produkcyjnych. 

- [Dowiedz się więcej o stacjach roboczych z dostępem uprzywilejowanym](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Jak włączyć uwierzytelnianie wieloskładnikowe na platformie Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: Rejestrowanie i alerty dotyczące podejrzanych działań z kont administracyjnych 

**Wskazówki:** Korzystanie Azure Active Directory (Azure AD) do wyświetlania alertów i raportów dotyczących ryzykownych zachowań użytkowników. Opcjonalnie klient może przesyłać Azure Security Center alerty wykrywania ryzyka w celu Azure Monitor i konfigurowania niestandardowych alertów/powiadomień przy użyciu grup akcji.

- [Opis Azure Security Center wykrywania ryzyka (podejrzane działanie)](../active-directory/identity-protection/overview-identity-protection.md)

- [Jak zintegrować dzienniki aktywności platformy Azure z usługą Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Jak skonfigurować grupy akcji dla niestandardowych alertów i powiadomień](../azure-monitor/alerts/action-groups.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Zarządzanie zasobami platformy Azure tylko z zatwierdzonych lokalizacji 

**Wskazówki:** Zaleca się używanie dostępu warunkowego z nazwami lokalizacji, aby zezwolić na dostęp tylko z określonych grup logicznych zakresów adresów IP lub krajów/regionów. 

- [Jak skonfigurować nazwane lokalizacje na platformie Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="39-use-azure-active-directory"></a>3.9: Użyj Azure Active Directory

**Wskazówki:** Użyj Azure Active Directory (Azure AD) jako centralnego systemu uwierzytelniania i autoryzacji. Usługa Azure AD chroni dane przy użyciu silnego szyfrowania danych magazynowany i podczas przesyłania. Usługa Azure AD dodaje również instyty, skróty i bezpiecznie przechowuje poświadczenia użytkownika. W przypadku korzystania z hybrydowych pracowników runbook można korzystać z tożsamości zarządzanych zamiast kont Uruchom jako, aby umożliwić bardziej bezproblemowe zabezpieczanie uprawnień.

- [Jak utworzyć i skonfigurować wystąpienie usługi Azure AD](../active-directory-domain-services/tutorial-create-instance.md)

- [Używanie uwierzytelniania runbook z tożsamościami zarządzanymi](./automation-hrw-run-runbooks.md#runbook-auth-managed-identities)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Regularne przeglądanie i uzgadnianie dostępu użytkowników

**Wskazówki:** usługa Azure Active Directory (Azure AD) udostępnia dzienniki, które ułatwiają odnajdywanie nieaktywnego konta. Ponadto za pomocą przeglądów dostępu tożsamości platformy Azure można efektywnie zarządzać członkostwem w grupach, dostępem do aplikacji dla przedsiębiorstw i przypisań ról. Dostęp użytkowników można regularnie przeglądać, aby upewnić się, że tylko odpowiedni użytkownicy mają dalszy dostęp. Za każdym razem, gdy używasz kont Uruchom jako konta usługi Automation dla twoich zasobów Runbook, upewnij się, że te jednostki usługi są również śledzone w twoim spisie, ponieważ często mają podniesione uprawnienia. Usuń wszystkie nieużywane konta Uruchom jako, aby zminimalizować narażenie na ataki.

- [Informacje o raportowaniu w usłudze Azure AD](../active-directory/reports-monitoring/index.yml)

- [Jak używać przeglądów dostępu tożsamości platformy Azure](../active-directory/governance/access-reviews-overview.md)

- [Usuwanie konta Uruchom jako lub klasycznego konta Uruchom jako](delete-run-as-account.md)

- [Zarządzanie kontem Azure Automation Uruchom jako](manage-runas-account.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Monitorowanie prób uzyskania dostępu do dezaktywowanych poświadczeń

**Wskazówki:** Masz dostęp do źródeł dzienników aktywności logowania, inspekcji i ryzyka Azure Active Directory (Azure AD), które umożliwiają integrację z dowolnym narzędziem SIEM/monitoring.

Ten proces można usprawnić, tworząc ustawienia diagnostyczne dla kont użytkowników usługi Azure AD oraz wysyłając dzienniki inspekcji i dzienniki logowania do obszaru roboczego usługi Log Analytics. Żądane alerty można skonfigurować w obszarze roboczym usługi Log Analytics.

- [Jak zintegrować dzienniki aktywności platformy Azure z usługą Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12: Odchylenie zachowania alertu podczas logowania na koncie

**Wskazówki:** użyj Azure Active Directory (Azure AD) Risk and Identity Protection, aby skonfigurować automatyczne odpowiedzi na wykryte podejrzane akcje związane z tożsamościami użytkowników dla zasobu sieciowego. Możesz również pozysać dane do Azure Sentinel celu dalszego badania.

- [Jak wyświetlić ryzykowne logowania w usłudze Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

- [Jak skonfigurować i włączyć zasady ryzyka usługi Identity Protection](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Jak do dołączać Azure Sentinel](../sentinel/quickstart-onboard.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Zapewnianie firmie Microsoft dostępu do odpowiednich danych klientów podczas scenariuszy pomocy technicznej

**Wskazówki:** W przypadku kont Azure Automation pomocy technicznej firmy Microsoft można uzyskać dostęp do metadanych zasobów platformy w otwartym przypadku pomocy technicznej bez użycia innego narzędzia.

Jednak w przypadku korzystania z hybrydowych pracowników runbook wspieranych przez maszyny wirtualne platformy Azure i gdy inne firmy muszą uzyskać dostęp do danych klienta (na przykład podczas żądania pomocy technicznej), użyj usługi Skrytka klienta (wersja zapoznawcza) dla maszyn wirtualnych platformy Azure, aby przeglądać i zatwierdzać lub odrzucać żądania dostępu do danych klientów.

- [Opis Skrytka klienta](../security/fundamentals/customer-lockbox-overview.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

## <a name="data-protection"></a>Ochrona danych

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: ochrona danych](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1. Utrzymywanie spisu informacji poufnych

**Wskazówki:** użyj tagów, aby pomóc w śledzeniu Azure Automation zasobów, które przechowują lub przetwarzają poufne informacje. 

- [Jak tworzyć tagi i ich używać](../azure-resource-manager/management/tag-resources.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Izolowanie systemów przechowujące lub przetwarzające informacje poufne

**Wskazówki:** Implementowanie oddzielnych subskrypcji i/lub grup zarządzania na rzecz tworzenia, testowania i produkcji. Izolowanie środowisk przy użyciu oddzielnych zasobów konta usługi Automation. Zasoby, takie jak hybrydowe środowiska pracy runbook, powinny być oddzielone siecią wirtualną/podsiecią, odpowiednio oznakowane i zabezpieczone w sieciowej grupie zabezpieczeń lub Azure Firewall. W przypadku maszyn wirtualnych przechowujące lub przetwarzające poufne dane należy zaimplementować zasady i procedury, aby je wyłączyć, gdy nie są używane.

- [Jak utworzyć dodatkowe subskrypcje platformy Azure](../cost-management-billing/manage/create-subscription.md)

- [Jak utworzyć Grupy zarządzania](../governance/management-groups/create-management-group-portal.md)

- [Jak tworzyć tagi i ich używać](../azure-resource-manager/management/tag-resources.md)

- [Jak utworzyć Virtual Network](../virtual-network/quick-create-portal.md)

- [Jak utworzyć sieciowej grupy zabezpieczeń za pomocą konfiguracji zabezpieczeń](../virtual-network/tutorial-filter-network-traffic.md)

- [Jak wdrożyć Azure Firewall](../firewall/tutorial-firewall-deploy-portal.md)

- [Konfigurowanie alertu lub alertu i odmowy przy użyciu Azure Firewall](../firewall/threat-intel.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Monitorowanie i blokowanie nieautoryzowanego transferu poufnych informacji

**Wskazówki:** W przypadku korzystania z funkcji hybrydowego procesu roboczego elementu Runbook skorzystaj z rozwiązania innej firmy od firmy Azure Marketplace w sieci obwodowej, które monitoruje nieautoryzowany transfer poufnych informacji i blokuje takie transfery podczas powiadamiania specjalistów ds. zabezpieczeń informacji.

W przypadku podstawowej platformy zarządzanej przez firmę Microsoft firma Microsoft traktuje całą zawartość klientów jako poufne i chroni przed utratą i utratą danych klientów. Aby zapewnić bezpieczeństwo danych klientów na platformie Azure, firma Microsoft wdrożyła i utrzymuje pakiet niezawodnych funkcji i kontroli ochrony danych.

- [Informacje na temat ochrony danych klientów na platformie Azure](../security/fundamentals/protection-customer-data.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4. Szyfrowanie wszystkich informacji poufnych podczas przesyłania

**Wskazówki:** Szyfruj wszystkie poufne informacje podczas przesyłania. Upewnij się, że każdy klient łączący się z zasobami platformy Azure w sieciach wirtualnych platformy Azure może negocjować TLS 1.2 lub nowszą. Azure Automation w pełni obsługuje i wymusza warstwę transportową (TLS) 1.2 i wszystkie wywołania klienta lub nowsze wersje dla wszystkich zewnętrznych punktów końcowych SYSTEMU PLIKÓW (za pośrednictwem obiektów webhook, węzłów DSC, hybrydowego procesu roboczego runbook).

Postępuj Azure Security Center dotyczących szyfrowania danych w spoczynku i szyfrowania podczas przesyłania, jeśli ma to zastosowanie.

- [Opis szyfrowania podczas przesyłania za pomocą platformy Azure](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit)

- [Azure Automation wymuszanie TLS 1.2](../active-directory/hybrid/reference-connect-tls-enforcement.md)

**Odpowiedzialność**: Współużytkowane

**Azure Security Center monitorowania:** Brak

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5. Identyfikowanie poufnych danych za pomocą aktywnego narzędzia odnajdywania

**Wskazówki:** Użyj narzędzia do aktywnego odnajdywania innej firmy, aby zidentyfikować wszystkie poufne informacje przechowywane, przetwarzane lub przesyłane przez systemy technologiczne organizacji, w tym te znajdujące się na miejscu lub u zdalnego dostawcy usług, i zaktualizować spis informacji poufnych organizacji.

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4.6: Kontrola dostępu do zasobów przy użyciu kontroli dostępu opartej na rolach

**Wskazówki:** Kontrola dostępu oparta na rolach (RBAC) na platformie Azure umożliwia kontrolowanie dostępu do zasobów usługi Azure Automation przy użyciu wbudowanych definicji ról, a także przypisywanie dostępu użytkownikom, którzy mają dostęp do zasobów automatyzacji, zgodnie z modelem dostępu z najmniej uprzywilejowanymi lub "wystarczającymi uprawnieniami". W przypadku korzystania z hybrydowych pracowników runbook skorzystaj z tożsamości zarządzanych dla tych maszyn wirtualnych, aby uniknąć używania jednostki usługi. W przypadku korzystania zarówno z wielodostępnych, jak i hybrydowych pracowników runbook upewnij się, że do tożsamości pracowników runbook są stosowane uprawnienia RBAC platformy Azure o prawidłowym zakresie.

- [Jak skonfigurować RBAC platformy Azure](../role-based-access-control/role-assignments-portal.md)

- [Uprawnienia runbook dla hybrydowego procesu roboczego runbook](./automation-hybrid-runbook-worker.md#runbook-permissions-for-a-hybrid-runbook-worker)

- [Zarządzanie uprawnieniami ról i zabezpieczeniami](automation-role-based-access-control.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7. Wymuszanie kontroli dostępu za pomocą ochrony przed utratą danych opartej na hoście

**Wskazówki:** Azure Automation obecnie nie uwidacznia bazowych maszyn wirtualnych wielodostępnego procesu roboczego runbook i jest to obsługiwane przez platformę. Ta kontrolka nie ma zastosowania, jeśli używasz usługi out-of-the-box bez hybrydowych pracowników runbook.

Jeśli używasz hybrydowych procesów roboczych elementów Runbook obsługiwanych przez maszyny wirtualne platformy Azure, musisz użyć rozwiązania do zapobiegania utracie danych opartego na hoście innej firmy, aby wymusić kontrolę dostępu do hostowanych maszyn wirtualnych hybrydowego procesu roboczego elementów Runbook.

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8. Szyfrowanie poufnych informacji w spoczynku

**Wskazówki:** Używanie kluczy zarządzanych przez klienta z Azure Automation. Azure Automation obsługuje używanie kluczy zarządzanych przez klienta do szyfrowania wszystkich używanych "bezpiecznych zasobów", takich jak poświadczenia, certyfikaty, połączenia i zaszyfrowane zmienne. Korzystaj z zaszyfrowanych zmiennych w swoich podręcznikach runbook, aby uzyskać wszystkie potrzeby trwałego wyszukiwania zmiennych, aby zapobiec niezamierzonemu narażeniu.

W przypadku korzystania z hybrydowych awarii runbook dyski wirtualne na maszynach wirtualnych są szyfrowane podczas spoczynku przy użyciu szyfrowania po stronie serwera lub usługi Azure Disk Encryption (ADE). Usługa Azure Disk Encryption korzysta z funkcji BitLocker systemu Windows do szyfrowania dysków zarządzanych przy użyciu kluczy zarządzanych przez klienta na maszynie wirtualnej gościa. Szyfrowanie po stronie serwera przy użyciu kluczy zarządzanych przez klienta ulepsza się w usłudze ADE, umożliwiając korzystanie z dowolnego typu systemu operacyjnego i obrazów dla maszyn wirtualnych przez szyfrowanie danych w usłudze Storage.

- [Szyfrowanie dysków zarządzanych platformy Azure po stronie serwera](../virtual-machines/disk-encryption.md)

- [Azure Disk Encryption dla maszyn wirtualnych z systemem Windows](../virtual-machines/windows/disk-encryption-overview.md)

- [Używanie kluczy zarządzanych przez klienta dla konta usługi Automation](./automation-secure-asset-encryption.md#use-of-customer-managed-keys-for-an-automation-account)

- [Zmienne zarządzane w programie Azure Automation](shared-resources/variables.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Test [porównawczy zabezpieczeń](/azure/governance/policy/samples/azure-security-benchmark) platformy Azure jest domyślną inicjatywą zasad dla usługi Security Center i stanowi podstawę Security Center [rekomendacji firmy](/azure/security-center/security-center-recommendations). Definicje Azure Policy związane z tą kontrolką są włączane automatycznie przez Security Center. Alerty związane z tą kontrolą mogą [wymagać Azure Defender](/azure/security-center/azure-defender) planu dla powiązanych usług.

**Azure Policy wbudowane definicje — Microsoft.Automation:**

[!INCLUDE [Resource Policy for Microsoft.Automation 4.8](../../includes/policy/standards/asb/rp-controls/microsoft.automation-4-8.md)]

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9. Rejestrowanie zmian krytycznych zasobów platformy Azure i alerty dotyczące zmian

**Wskazówki:** użyj Azure Monitor z dziennikiem aktywności platformy Azure, aby tworzyć alerty dotyczące zmian krytycznych zasobów platformy Azure, takich jak składniki sieciowe, konta Azure Automation i elementy Runbook. 

- [Rejestrowanie diagnostyczne dla sieciowej grupy zabezpieczeń](../private-link/private-link-overview.md#logging-and-monitoring)

- [Jak tworzyć alerty dla zdarzeń dziennika aktywności platformy Azure](../azure-monitor/alerts/alerts-activity-log.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

## <a name="vulnerability-management"></a>Zarządzanie lukami w zabezpieczeniach

*Aby uzyskać więcej informacji, zobacz [Test porównawczy zabezpieczeń platformy Azure: zarządzanie lukami w zabezpieczeniach.](../security/benchmarks/security-control-vulnerability-management.md)*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1. Uruchamianie zautomatyzowanych narzędzi do skanowania luk w zabezpieczeniach

**Wskazówki:** postępuj zgodnie z zaleceniami Azure Security Center na temat przeprowadzania ocen luk w zabezpieczeniach w zasobach platformy Azure

- [Zalecenia dotyczące zabezpieczeń w Centrum zabezpieczeń Azure](../security-center/security-center-recommendations.md)

- [Security Center rekomendacji](../security-center/recommendations-reference.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4. Porównanie skanów luk w zabezpieczeniach typu "wstecz"

**Wskazówki:** eksportuj wyniki skanowania w spójnych odstępach czasu i porównaj wyniki, aby sprawdzić, czy luki w zabezpieczeniach zostały skorygowane. W przypadku korzystania z rekomendacji dotyczącej zarządzania lukami w zabezpieczeniach sugerowanej przez Azure Security Center, klient może przesunić się do portalu wybranego rozwiązania, aby wyświetlić historyczne dane skanowania.

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5. Ustalanie priorytetów korygowania wykrytych luk w zabezpieczeniach za pomocą procesu oceny ryzyka

**Wskazówki:** Użyj domyślnych klasyfikacji ryzyka (Secure Score) dostarczonych przez Azure Security Center, aby ułatwić priorytetyzowanie korygowania wykrytych luk w zabezpieczeniach.

- [Opis Azure Security Center bezpieczeństwa](../security-center/secure-score-security-controls.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

## <a name="inventory-and-asset-management"></a>Zarządzanie magazynem i zasobami

*Aby uzyskać więcej informacji, zobacz [Test porównawczy zabezpieczeń platformy Azure: spis i zarządzanie zasobami.](../security/benchmarks/security-control-inventory-asset-management.md)*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1. Korzystanie z rozwiązania do automatycznego odnajdywania zasobów

**Wskazówki:** Użyj Azure Resource Graph, aby odpytywać i odnajdywać Azure Automation zasobów w ramach subskrypcji. Upewnij się, że masz odpowiednie uprawnienia do odczytu w dzierżawie i możesz wyliczyć wszystkie subskrypcje platformy Azure, a także zasoby w ramach twoich subskrypcji.

- [Jak tworzyć zapytania za pomocą Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

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

**Wskazówki:** Używaj tagowania, grup zarządzania i oddzielnych subskrypcji, jeśli jest to konieczne, do organizowania i śledzenia Azure Automation zasobów. Regularnie uzgadniaj spis i upewnij się, że nieautoryzowane zasoby są usuwane z subskrypcji w terminowy sposób. Usuń wszystkie nieużywane konta Uruchom jako, aby zminimalizować narażenie na ataki.

- [Jak utworzyć dodatkowe subskrypcje platformy Azure](../cost-management-billing/manage/create-subscription.md)

- [Jak utworzyć Grupy zarządzania](../governance/management-groups/create-management-group-portal.md)

- [Jak tworzyć tagi i ich używać](../azure-resource-manager/management/tag-resources.md)

- [Usuwanie konta Uruchom jako lub klasycznego konta Uruchom jako](delete-run-as-account.md)

- [Zarządzanie kontem Azure Automation Uruchom jako](manage-runas-account.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4. Definiowanie i utrzymywanie spisu zatwierdzonych zasobów platformy Azure

**Wskazówki:** należy utworzyć spis zatwierdzonych zasobów platformy Azure i zatwierdzonego oprogramowania dla zasobów obliczeniowych zgodnie z potrzebami organizacji.

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Monitorowanie niezatwierdzonych zasobów platformy Azure

**Wskazówki:** użyj Azure Policy, aby wprowadzić ograniczenia dotyczące typu zasobów, które mogą być tworzone w subskrypcjach klientów przy użyciu następujących wbudowanych definicji zasad: 

- Niedozwolone typy zasobów 
- Dozwolone typy zasobów 

Ponadto użyj funkcji Azure Resource Graph do wykonywania zapytań/odnajdywania zasobów w ramach subskrypcji. Może to pomóc w środowiskach o wysokim poziomie zabezpieczeń, takich jak środowiska z kontami magazynu. 

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md) 

- [Jak tworzyć zapytania za pomocą Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

- [Azure Policy przykładowe wbudowane dla Azure Automation](policy-reference.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7. Usuwanie niezatwierdzonych zasobów i aplikacji platformy Azure

**Wskazówki:** Klient może uniemożliwić tworzenie lub używanie zasobów Azure Policy zgodnie z wymaganiami wytycznymi firmy klienta. Możesz zaimplementować własny proces usuwania nieautoryzowanych zasobów. W ramach Azure Automation można instalować i usuwać moduły programu PowerShell lub języka Python, do których można uzyskać dostęp za pośrednictwem portalu lub poleceń cmdlet, oraz zarządzać nimi. Niezatwierdzony lub stary moduł powinien zostać usunięty lub zaktualizowany dla tych runbook.

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

- [Zarządzanie modułem w programie Azure Automation](shared-resources/modules.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="69-use-only-approved-azure-services"></a>6.9. Używaj tylko zatwierdzonych usług platformy Azure

**Wskazówki:** Użyj Azure Policy, aby wprowadzić ograniczenia dotyczące typu zasobów, które mogą być tworzone w subskrypcjach klientów przy użyciu następujących wbudowanych definicji zasad:
- Niedozwolone typy zasobów
- Dozwolone typy zasobów

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

- [Jak odmówić określonego typu zasobu za pomocą Azure Policy](../governance/policy/samples/built-in-policies.md#general)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Ograniczanie możliwości interakcji użytkowników z Azure Resource Manager

**Wskazówki:** Użyj zasad dostępu warunkowego platformy Azure, aby ograniczyć użytkownikom możliwość interakcji z usługą Azure Resource Manager, konfigurując opcję "Blokuj dostęp" dla aplikacji "Microsoft Azure Management" z niezabezpieczonych lub niezatwierdzonych lokalizacji lub urządzeń. 

- [Jak skonfigurować dostęp warunkowy, aby zablokować dostęp do Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

## <a name="secure-configuration"></a>Bezpieczna konfiguracja

*Aby uzyskać więcej informacji, zobacz [Test porównawczy zabezpieczeń platformy Azure: bezpieczna konfiguracja.](../security/benchmarks/security-control-secure-configuration.md)*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1. Ustanów bezpieczne konfiguracje dla wszystkich zasobów platformy Azure

**Wskazówki:** użyj Azure Policy aliasów do tworzenia zasad niestandardowych w celu inspekcji lub wymuszania konfiguracji Azure Automation i powiązanych zasobów. Możesz również użyć wbudowanych definicji Azure Policy danych.

Ponadto program Azure Resource Manager możliwość eksportowania szablonu w pliku JavaScript Object Notation (JSON), który należy przejrzeć, aby upewnić się, że konfiguracje spełniają/przekraczają wymagania dotyczące zabezpieczeń w organizacji.

Możesz również użyć zaleceń z witryny Azure Security Center jako bezpiecznej konfiguracji odniesienia dla zasobów platformy Azure.

- [Jak wyświetlić dostępne Azure Policy aliasy](/powershell/module/az.resources/get-azpolicyalias)

- [Samouczek: tworzenie zasad i zarządzanie nimi w celu wymuszania zgodności](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy przykładowe wbudowane dla Azure Automation](policy-reference.md)

- [Eksportowanie pojedynczego i wielu zasobów do szablonu w Azure Portal](../azure-resource-manager/templates/export-template-portal.md)

- [Zalecenia dotyczące zabezpieczeń — przewodnik referencyjny](../security-center/recommendations-reference.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Obsługa bezpiecznych konfiguracji zasobów platformy Azure

**Wskazówki:** użyj Azure Resource Manager szablonów i Azure Policy, aby bezpiecznie skonfigurować zasoby platformy Azure skojarzone z Azure Automation. Azure Resource Manager to pliki oparte na formatach JSON używane do wdrażania zasobów platformy Azure, a wszystkie szablony niestandardowe muszą być przechowywane i przechowywane w bezpiecznym repozytorium kodu. Użyj funkcji integracji kontroli źródła, aby zapewnić, że twoje element runbook na koncie usługi Automation będą na bieżąco ze skryptami w repozytorium kontroli źródła. Użyj Azure Policy [deny] i [deploy if not exist], aby wymusić bezpieczne ustawienia w zasobach platformy Azure.

- [Korzystanie z integracji kontroli źródła](source-control-integration.md)

- [Informacje na temat tworzenia Azure Resource Manager szablonów](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md) 

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md) 

- [Understanding Azure Policy Effects](../governance/policy/concepts/effects.md)

- [Wdrażanie konta usługi Automation przy użyciu Azure Resource Manager szablonu](/azure/automation/quickstart-create-automation-account-template)

- [Azure Policy przykładowe wbudowane dla Azure Automation](policy-reference.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Bezpieczne przechowywanie konfiguracji zasobów platformy Azure

**Wskazówki:** użyj Azure DevOps, aby bezpiecznie przechowywać kod, taki jak niestandardowe zasady platformy Azure, szablony Azure Resource Manager i skrypty Desired State Configuration zarządzania. Aby uzyskać dostęp do zasobów, które zarządzasz w programie Azure DevOps, możesz udzielić lub odmówić uprawnień określonym użytkownikom, wbudowanym grupom zabezpieczeń lub grupom zdefiniowanym w usłudze Azure Active Directory (Azure AD), jeśli są zintegrowane z programem Azure DevOps, lub usługi Active Directory, jeśli są zintegrowane z programem TFS. Użyj funkcji integracji kontroli źródła, aby zapewnić, że twoje element runbook na koncie usługi Automation będą na bieżąco ze skryptami w repozytorium kontroli źródła.

- [Jak przechowywać kod w Azure DevOps](/azure/devops/repos/git/gitworkflow)

- [Informacje o uprawnieniach i grupach w Azure DevOps](/azure/devops/organizations/security/about-permissions)

- [Korzystanie z integracji kontroli źródła](source-control-integration.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7. Wdrażanie narzędzi do zarządzania konfiguracją dla zasobów platformy Azure

**Wskazówki:** Definiowanie i implementowanie standardowych konfiguracji zabezpieczeń dla zasobów platformy Azure przy użyciu Azure Policy. Użyj Azure Policy, aby utworzyć zasady niestandardowe do inspekcji lub wymuszania konfiguracji sieci zasobów platformy Azure. Możesz również korzystać z wbudowanych definicji zasad związanych z określonymi zasobami. 

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

- [Jak używać aliasów](../governance/policy/concepts/definition-structure.md#aliases)

- [Azure Policy przykładowe wbudowane dla Azure Automation](policy-reference.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9. Implementowanie zautomatyzowanego monitorowania konfiguracji dla zasobów platformy Azure

**Wskazówki:** użyj Azure Policy do alertów i inspekcji konfiguracji zasobów platformy Azure. Zasady mogą służyć do wykrywania niektórych zasobów, które nie zostały skonfigurowane przy użyciu prywatnego punktu końcowego.

W przypadku korzystania z funkcji hybrydowego procesu roboczego elementu Runbook skorzystaj z Azure Security Center przeprowadzania skanowania linii bazowych dla maszyn wirtualnych platformy Azure.  Dodatkowe metody automatycznej konfiguracji obejmują: Azure Automation State Configuration.

- [Jak skorygować rekomendacje w Azure Security Center](../security-center/security-center-remediate-recommendations.md)

- [Wprowadzenie do Azure Automation State Configuration](automation-dsc-getting-started.md)

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy przykładowe wbudowane dla Azure Automation](policy-reference.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Wyeliminuj niezamierzone narażenie poświadczeń

**Wskazówki:** implementowanie skanera poświadczeń w celu identyfikowania poświadczeń w kodzie. Skaner poświadczeń ułatwia również przenoszenie odnalezionych poświadczeń do bezpieczniejszych lokalizacji, takich jak usługa Azure Key Vault. 

- [Jak skonfigurować skaner poświadczeń](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

## <a name="data-recovery"></a>Odzyskiwanie danych

*Aby uzyskać więcej informacji, zobacz [Test porównawczy zabezpieczeń platformy Azure: odzyskiwanie danych.](../security/benchmarks/security-control-data-recovery.md)*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Zapewnianie regularnych automatycznych kopii zapasowej

**Wskazówki:** użyj Azure Resource Manager, aby wdrożyć Azure Automation kont i powiązanych zasobów. Azure Resource Manager umożliwia eksportowanie szablonów, których można używać jako kopii zapasowych do przywracania Azure Automation kont i powiązanych zasobów. Użyj Azure Automation, aby regularnie Azure Resource Manager interfejsu API eksportowania szablonu.

Użyj funkcji integracji kontroli źródła, aby zapewnić, że twoje element runbook na koncie usługi Automation będą na bieżąco ze skryptami w repozytorium kontroli źródła.

- [Omówienie usługi Azure Resource Manager](../azure-resource-manager/management/overview.md)

- [Azure Resource Manager szablonów dla Azure Automation zasobów](/azure/templates/microsoft.automation/allversions)

- [Tworzenie konta usługi Automation przy użyciu Azure Resource Manager szablonu](quickstart-create-automation-account-template.md)

- [Eksportowanie pojedynczego i wielu zasobów do szablonu w Azure Portal](../azure-resource-manager/templates/export-template-portal.md)

- [Grupy zasobów — eksportowanie szablonu](/rest/api/resources/resourcegroups/exporttemplate)

- [Wprowadzenie do Azure Automation](automation-intro.md)

- [Jak utworzyć kopię zapasową kluczy magazynu kluczy na platformie Azure](/powershell/module/az.keyvault/backup-azkeyvaultkey)

- [Używanie kluczy zarządzanych przez klienta dla konta usługi Automation](./automation-secure-asset-encryption.md#use-of-customer-managed-keys-for-an-automation-account)

- [Korzystanie z integracji kontroli źródła](source-control-integration.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2. Wykonywanie pełnych kopii zapasowych systemu i tworzenie kopii zapasowych dowolnych kluczy zarządzanych przez klienta

**Wskazówki:** użyj Azure Resource Manager, aby wdrożyć Azure Automation kont i powiązanych zasobów. Azure Resource Manager umożliwia eksportowanie szablonów, których można używać jako kopii zapasowych do przywracania Azure Automation kont i powiązanych zasobów. Użyj Azure Automation, aby regularnie Azure Resource Manager interfejsu API eksportowania szablonu. Kopii zapasowej kluczy zarządzanych przez klienta w Azure Key Vault. Możesz wyeksportować swoje pliki runbook do plików skryptów przy użyciu programu Azure Portal lub PowerShell.

- [Omówienie usługi Azure Resource Manager](../azure-resource-manager/management/overview.md)

- [Azure Resource Manager szablonów dla zasobów Azure Automation zasobów](/azure/templates/microsoft.automation/allversions)

- [Tworzenie konta usługi Automation przy użyciu Azure Resource Manager szablonu](quickstart-create-automation-account-template.md)

- [Eksportowanie pojedynczego zasobu i wielu zasobów do szablonu w Azure Portal](../azure-resource-manager/templates/export-template-portal.md)

- [Grupy zasobów — eksportowanie szablonu](/rest/api/resources/resourcegroups/exporttemplate)

- [Wprowadzenie do Azure Automation](automation-intro.md)

- [Jak utworzyć kopię zapasową kluczy magazynu kluczy na platformie Azure](/powershell/module/az.keyvault/backup-azkeyvaultkey)

- [Używanie kluczy zarządzanych przez klienta dla konta usługi Automation](./automation-secure-asset-encryption.md#use-of-customer-managed-keys-for-an-automation-account)

- [Kopia zapasowa danych platformy Azure dla kont usługi Automation](./automation-managing-data.md#data-backup)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Zweryfikuj wszystkie kopie zapasowe, w tym klucze zarządzane przez klienta

**Wskazówki:** Zapewnij możliwość regularnego wdrażania szablonów Azure Resource Manager w izolowanej subskrypcji, jeśli jest to wymagane. Przetestuj przywracanie kopii zapasowej kluczy zarządzanych przez klienta.

- [Wdrażanie zasobów przy użyciu szablonów usługi ARM i Azure Portal](../azure-resource-manager/templates/deploy-portal.md)

- [Jak przywrócić klucze magazynu kluczy na platformie Azure](/powershell/module/az.keyvault/restore-azkeyvaultkey)

- [Używanie kluczy zarządzanych przez klienta dla konta usługi Automation](./automation-secure-asset-encryption.md#use-of-customer-managed-keys-for-an-automation-account)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4. Zapewnianie ochrony kopii zapasowych i kluczy zarządzanych przez klienta

**Wskazówki:** Użyj Azure DevOps, aby bezpiecznie przechowywać kod i zarządzać jego kodem, na przykład Azure Resource Manager szablonów. Aby chronić zasoby, które zarządzasz w usłudze Azure DevOps, możesz udzielić lub odmówić uprawnień określonym użytkownikom, wbudowanym grupom zabezpieczeń lub grupom zdefiniowanym w usłudze Azure Active Directory (Azure AD), jeśli są zintegrowane z usługą Azure DevOps, lub usługi Active Directory, jeśli są zintegrowane z programem TFS.

Użyj funkcji integracji kontroli źródła, aby zapewnić, że twoje element runbook na koncie usługi Automation będą na bieżąco ze skryptami w repozytorium kontroli źródła.

- [Jak przechowywać kod w Azure DevOps](/azure/devops/repos/git/gitworkflow)

- [Informacje o uprawnieniach i grupach w Azure DevOps](/azure/devops/organizations/security/about-permissions)

- [Korzystanie z integracji kontroli źródła](source-control-integration.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

## <a name="incident-response"></a>Reagowanie na zdarzenia

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: reagowanie na zdarzenia](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1. Tworzenie przewodnika po reagowaniu na zdarzenia

**Wskazówka**: Utwórz przewodnik odpowiedzi na zdarzenia dla swojej organizacji. Upewnij się, że istnieją zarejestrowane plany reakcji na zdarzenia, które definiują wszystkie role pracowników, a także etapy obsługi zdarzeń/zarządzania od wykrywania do oceny po zdarzeniu.

- [Wskazówki dotyczące tworzenia własnego procesu reagowania na zdarzenia zabezpieczeń](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Centrum zabezpieczeń firmy Microsoft Anatomy of an Incident (Anatomia zdarzenia)](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [Klient może również skorzystać z przewodnika obsługi zdarzeń zabezpieczeń komputera NIST, aby pomóc w tworzeniu własnego planu reagowania na zdarzenia](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2. Tworzenie procedury oceniania i ustalania priorytetów zdarzeń

**Wskazówki:** Security Center przypisuje ważność do każdego alertu, aby ułatwić określanie priorytetów, które alerty powinny być badane w pierwszej kolejności. Ważność jest oparta na tym, jak pewna Security Center znajduje się w znajdowaniu lub analizie użytej do wystawienia alertu, a także na poziomie pewności, że za działaniem, które doprowadziło do tego alertu, znajduje się złośliwy zamiar. 

Ponadto wyraźnie oznaczaj subskrypcje (na przykład production, non-prod) przy użyciu tagów i utwórz system nazewnictwa, aby jasno identyfikować i kategoryzować zasoby platformy Azure, zwłaszcza te, które przetwarzają poufne dane.  Odpowiedzialność za korygowanie alertów w oparciu o krytyczne znaczenie zasobów platformy Azure i środowisko, w którym wystąpiło zdarzenie, leży po stronie użytkownika.

- [Alerty zabezpieczeń w Centrum zabezpieczeń Azure](../security-center/security-center-alerts-overview.md)

- [Organizowanie zasobów platformy Azure przy użyciu tagów](../azure-resource-manager/management/tag-resources.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="103-test-security-response-procedures"></a>10.3: Testowanie procedur reagowania na zabezpieczenia

**Wskazówki:** Przećwiczyj ćwiczenia, aby regularnie testować możliwości reagowania na zdarzenia w systemach w celu ochrony zasobów platformy Azure. Zidentyfikuj słabe punkty i przerwy oraz popraw plan zgodnie z wymaganiami.

- [Publikacja NIST — Przewodnik po programach testowych, szkoleniowych i szkoleniowych dla planów i możliwości IT](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4. Podaj szczegóły kontaktowe zdarzenia zabezpieczeń i skonfiguruj powiadomienia o alertach dla zdarzeń zabezpieczeń

**Wskazówki:** informacje kontaktowe dotyczące zdarzeń naruszenia zabezpieczeń będą używane przez firmę Microsoft do kontaktowania się z Tobą, jeśli użytkownik Centrum zabezpieczeń firmy Microsoft (MSRC) odkryje, że do Twoich danych dostęp uzyskał nieupoważniony lub niedozwolony podmiot. Przejrzyj zdarzenia po fakcie, aby upewnić się, że problemy zostały rozwiązane.

- [Jak ustawić kontakt Azure Security Center zabezpieczeń](../security-center/security-center-provide-security-contact-details.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5. Dołączanie alertów zabezpieczeń do systemu reagowania na zdarzenia

**Wskazówki:** eksportuj alerty Azure Security Center i zalecenia przy użyciu funkcji eksportu ciągłego, aby ułatwić identyfikowanie zagrożeń związanych z zasobami platformy Azure. Eksport ciągły umożliwia ręczne lub ciągłe eksportowanie alertów i zaleceń. Możesz użyć łącznika danych Azure Security Center, aby przesyłać strumieniowo alerty do Azure Sentinel.

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

*Aby uzyskać więcej informacji, zobacz [test porównawczy zabezpieczeń platformy Azure: testy penetracyjne i ćwiczenia zespołu red team.](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Przeprowadzaj regularne testy penetrowania zasobów platformy Azure i zapewnij korygowanie wszystkich krytycznych ustaleń zabezpieczeń 

**Wskazówki:** postępuj zgodnie z zasadami firmy Microsoft w zakresie zaangażowania, aby upewnić się, że testy penetracyjne nie naruszały zasad firmy Microsoft. Korzystaj ze strategii firmy Microsoft i wykonywania testów typu „red team” i testów na żywo w zarządzanej przez firmę Microsoft infrastrukturze, usługach i aplikacjach w chmurze.

- [Reguły testów penetracyjnych zaangażowania](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Testy typu „red team” w chmurze firmy Microsoft](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Odpowiedzialność**: Współużytkowane

**Azure Security Center monitorowania:** Brak

## <a name="next-steps"></a>Następne kroki

- Zobacz [Omówienie testu porównawczego zabezpieczeń platformy Azure w wersji 2](../security/benchmarks/overview.md)
- Dowiedz się więcej o [punktach odniesienia zabezpieczeń platformy Azure](../security/benchmarks/security-baselines-overview.md)
