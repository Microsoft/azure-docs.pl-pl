---
title: Podstawa zabezpieczeń platformy Azure dla Azure DevTest Labs
description: Linia bazowa zabezpieczeń Azure DevTest Labs zawiera wskazówki i zasoby dotyczące procedur związanych z wdrażaniem zaleceń dotyczących zabezpieczeń określonych w teście zabezpieczeń platformy Azure.
author: msmbaldwin
ms.service: devtest-lab
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 13585173b54c79158161da54f968ba66eb018f6a
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101705742"
---
# <a name="azure-security-baseline-for-azure-devtest-labs"></a>Podstawa zabezpieczeń platformy Azure dla Azure DevTest Labs

Ta linia bazowa zabezpieczeń stosuje wskazówki z programu [Azure Security test w wersji 1,0](../security/benchmarks/overview-v1.md) do Azure DevTest Labs. Test porównawczy zabezpieczeń platformy Azure zawiera zalecenia dotyczące sposobu zabezpieczania rozwiązań w chmurze na platformie Azure. Zawartość jest pogrupowana według **kontroli zabezpieczeń** zdefiniowanych przez program Azure Security test i powiązane wskazówki dotyczące Azure DevTest Labs. **Kontrolki** nie mają zastosowania do Azure DevTest Labs zostały wykluczone.

Aby dowiedzieć się, jak Azure DevTest Labs całkowicie mapować do testu porównawczego zabezpieczeń platformy Azure, zobacz [pełny Azure DevTest Labs pliku mapowania linii bazowej zabezpieczeń](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Bezpieczeństwo sieci

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: bezpieczeństwo sieci](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: Ochrona zasobów platformy Azure w ramach sieci wirtualnych

**Wskazówki**: podczas wdrażania zasobów Azure DevTest Labs należy utworzyć lub użyć istniejącej sieci wirtualnej. Upewnij się, że wybrana Sieć wirtualna ma zastosowana sieciową grupę zabezpieczeń do jej podsieci i kontroli dostępu do sieci skonfigurowanych specyficznie dla zaufanych portów i źródeł aplikacji. Gdy zasoby laboratorium są skonfigurowane za pomocą sieci wirtualnej, nie są one publicznie adresowane i można uzyskać do nich dostęp tylko z poziomu sieci wirtualnej. Istnieje również możliwość utworzenia laboratorium izolowanego od sieci, gdzie wraz ze środowiskami laboratoryjnymi zasoby laboratoryjne, takie jak konto magazynu i magazyny kluczy, również będą całkowicie izolowane i dostępne tylko za pomocą określonych punktów końcowych. 

W zależności od potrzeb organizacji należy używać usługi Zapora platformy Azure do centralnego tworzenia, wymuszania i rejestrowania zasad łączności aplikacji i sieci w ramach subskrypcji i sieci wirtualnych.

- [Jak skonfigurować sieć wirtualną dla Azure DevTest Labs](devtest-lab-configure-vnet.md)

- [Jak utworzyć izolowane dla sieci wystąpienie DevTest Labs](network-isolation.md)

- [Jak utworzyć sieciową grupę zabezpieczeń z regułami zabezpieczeń](../virtual-network/tutorial-filter-network-traffic.md)

- [Jak wdrożyć i skonfigurować zaporę platformy Azure](../firewall/tutorial-firewall-deploy-portal.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1,2: Monitoruj i Rejestruj konfigurację oraz ruch sieci wirtualnych, podsieci i kart sieciowych

**Wskazówki**: Wdróż sieciową grupę zabezpieczeń w sieci, do której są wdrażane zasoby Azure DevTest Labs. Włącz dzienniki przepływu sieciowych grup zabezpieczeń w sieciowych grupach zabezpieczeń na potrzeby inspekcji ruchu.

Możesz również wysłać dzienniki przepływu sieciowej grupy zabezpieczeń do obszaru roboczego Log Analytics i użyć Analiza ruchu, aby uzyskać wgląd w przepływ ruchu w chmurze platformy Azure. Niektóre zalety Analiza ruchu to możliwość wizualizacji aktywności sieciowej i identyfikowania aktywnych punktów, identyfikowania zagrożeń bezpieczeństwa, zrozumienia wzorców przepływu ruchu i wyznaczania konfiguracji sieci.

- [Jak włączyć dzienniki przepływu sieciowej grupy zabezpieczeń](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Jak włączyć i używać Analiza ruchu](../network-watcher/traffic-analytics.md)

- [Informacje o zabezpieczeniach sieci zapewnianych przez Azure Security Center](../security-center/security-center-network-recommendations.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="13-protect-critical-web-applications"></a>1,3: Ochrona krytycznych aplikacji sieci Web

**Wskazówki**: wdrażanie zapory aplikacji sieci Web platformy Azure (WAF) przed krytycznymi aplikacjami sieci Web wdrożonymi przy użyciu szablonów Azure Resource Manager, aby uzyskać dodatkową inspekcję ruchu przychodzącego. Włącz ustawienie diagnostyczne dla WAF i pobierania dzienników do konta magazynu, centrum zdarzeń lub Log Analytics obszaru roboczego.

- [Jak wdrożyć usługę Azure WAF](../web-application-firewall/ag/create-waf-policy-ag.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: odmowa komunikacji ze znanymi złośliwymi adresami IP

**Wskazówki**: wdrażanie Virtual Network platformy Azure (VNET) dla laboratorium usprawnia zabezpieczenia i izolację laboratorium. Podsieci, zasady kontroli dostępu i inne funkcje ułatwiają również ograniczanie dostępu. W przypadku wdrożenia w sieci wirtualnej Azure DevTest Labs nie jest publicznie adresowane i można uzyskać do nich dostęp tylko z maszyn wirtualnych i aplikacji w sieci wirtualnej.

Włącz ochronę standardową DDoS w sieciach wirtualnych platformy Azure, aby chronić przed atakami DDoS. Użyj Azure Security Center zintegrowanej analizy zagrożeń, aby odmówić komunikacji ze znanymi złośliwymi adresami IP.

Wdróż zaporę platformy Azure w każdej z granic sieci organizacji z włączoną analizą zagrożeń i skonfigurowaną jako "Alert i Odmów" w celu uzyskania złośliwego ruchu sieciowego. Użyj Azure Security Center dostęp do sieci w czasie w celu skonfigurowania sieciowych grup zabezpieczeń w celu ograniczenia narażenia punktów końcowych na zatwierdzone adresy IP przez ograniczony okres. Aby zalecać sieciowej grupy zabezpieczeń konfiguracje, które ograniczają porty i źródłowe adresy IP w oparciu o rzeczywisty ruch i analizę zagrożeń, należy użyć Azure Security Center.

- [Jak skonfigurować sieć wirtualną dla Azure DevTest Labs](devtest-lab-configure-vnet.md)

- [Jak skonfigurować ochronę DDoS](/azure/virtual-network/manage-ddos-protection)

- [Jak wdrożyć zaporę platformy Azure](../firewall/tutorial-firewall-deploy-portal.md)

- [Opis Azure Security Center zintegrowanej analizy zagrożeń](/azure/security-center/threat-protection)

- [Informacje o Azure Security Center adaptacyjnej ograniczania przepustowości sieci](../security-center/security-center-adaptive-network-hardening.md)

- [Informacje o Azure Security Center Access Control sieci w czasie](../security-center/security-center-just-in-time.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="15-record-network-packets"></a>1,5: rejestrowanie pakietów sieciowych

**Wskazówki**: Włączanie funkcji przechwytywania pakietów Network Watcher w sieci wirtualnej laboratorium w celu zbadania nietypowych działań. 

- [Jak włączyć dzienniki przepływu sieciowej grupy zabezpieczeń](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Jak włączyć i używać Analiza ruchu](../network-watcher/traffic-analytics.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: wdrażanie opartych na sieci systemów zapobiegania wykrywaniem i dostępem intruzów (identyfikatorów/adresów IP)

**Wskazówki**: Użyj zapory platformy Azure wdrożonej w sieci wirtualnej z włączoną funkcją analizy zagrożeń. Filtrowanie oparte na analizie zagrożeń na platformie Azure może wysyłać alerty i odrzucać ruch do i ze znanych złośliwych adresów IP i domen. Adresy IP i domeny pochodzą z kanału informacyjnego analizy zagrożeń firmy Microsoft. Jeśli Twoja organizacja wymaga dodatkowych funkcji na platformie Azure, wybierz odpowiednią ofertę z witryny Azure Marketplace, która obsługuje funkcje identyfikatorów/adresów IP z możliwościami inspekcji ładunku.

Wdrażaj wybrane rozwiązanie zapory w każdej z granic sieci organizacji, aby wykrywać i/lub odrzucać złośliwy ruch.

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)
 

- [Jak wdrożyć zaporę platformy Azure](../firewall/tutorial-firewall-deploy-portal.md)
 

- [Jak skonfigurować alerty za pomocą zapory platformy Azure](../firewall/threat-intel.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="17-manage-traffic-to-web-applications"></a>1,7: zarządzanie ruchem do aplikacji sieci Web

**Wskazówki**: podczas pracy z DevTest Labs Azure Resource Manager środowiska, które obejmują aplikacje sieci Web, wdróż Application Gateway platformy Azure z WŁĄCZONYm protokołem HTTPS/TLS dla zaufanych certyfikatów.

- [Jak wdrożyć Application Gateway](../application-gateway/quick-create-portal.md)

- [Jak skonfigurować Application Gateway do korzystania z protokołu HTTPS](../application-gateway/create-ssl-portal.md)

- [Omówienie równoważenia obciążenia warstwy 7 za pomocą bram aplikacji sieci Web platformy Azure](../application-gateway/overview.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: Minimalizacja złożoności i kosztów administracyjnych reguł zabezpieczeń sieci

**Wskazówki**: Użyj tagów usługi Virtual Network, aby zdefiniować kontrolę dostępu do sieci dla sieciowych grup zabezpieczeń lub zapory platformy Azure skonfigurowanej dla zasobów DevTest Labs. Podczas tworzenia reguł zabezpieczeń można użyć tagów usługi zamiast konkretnych adresów IP. Określając nazwę tagu usługi (np. ApiManagement) w odpowiednim polu źródłowym lub docelowym reguły, można zezwolić na ruch dla odpowiedniej usługi lub go odrzucić. Firma Microsoft zarządza prefiksami adresów, które obejmują tag usługi, i automatycznie aktualizuje tag usługi jako adresy.

Aby uprościć konfigurację zabezpieczeń, można również użyć grup zabezpieczeń aplikacji. Grupy zabezpieczeń aplikacji umożliwiają skonfigurowanie zabezpieczeń sieci jako naturalnego rozszerzenia struktury aplikacji, co pozwala na grupowanie maszyn wirtualnych i definiowanie zasad zabezpieczeń sieci na podstawie tych grup.

- [Zrozumienie i używanie tagów usługi](../virtual-network/service-tags-overview.md)

- [Zrozumienie i używanie grup zabezpieczeń aplikacji](/azure/virtual-network/security-overview#application-security-groups)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: Obsługa standardowych konfiguracji zabezpieczeń dla urządzeń sieciowych

**Wskazówki**: Definiowanie i implementowanie standardowych konfiguracji zabezpieczeń dla zasobów sieciowych za pomocą Azure Policy.

Możesz również użyć planów platformy Azure, aby uprościć duże wdrożenia platformy Azure przez spakowanie kluczowych artefaktów środowiska, takich jak szablony Menedżera zasobów platformy Azure, kontrolki RBAC i zasady, w ramach jednej definicji planu. Możesz zastosować plan do nowych subskrypcji i dostosować kontrolę i zarządzanie przy użyciu wersji.

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

- [Przykłady Azure Policy dla sieci](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#network)

- [Jak utworzyć Azure Blueprint](../governance/blueprints/create-blueprint-portal.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="110-document-traffic-configuration-rules"></a>1,10: udokumentowanie reguł konfiguracji ruchu

**Wskazówki**: Użyj tagów dla zasobów sieciowych skojarzonych z wdrożeniem Azure DevTest Labs, aby logicznie zorganizować je w taksonomię. W przypadku poszczególnych reguł sieciowej grupy zabezpieczeń Użyj pola "Description", aby określić potrzeby biznesowe i/lub czas trwania (itp.) dla reguł zezwalających na ruch do/z sieci.

Użyj dowolnych wbudowanych definicji Azure Policy związanych ze znakiem, takich jak "Wymagaj tagu i jego wartości", aby upewnić się, że wszystkie zasoby są tworzone przy użyciu tagów i powiadomienia o istniejących nieoznakowanych zasobach.

Możesz użyć Azure PowerShell lub interfejsu wiersza polecenia platformy Azure, aby wyszukiwać lub wykonywać akcje na zasobach na podstawie ich tagów.

- [Tworzenie i używanie tagów](../azure-resource-manager/management/tag-resources.md)

- [Jak utworzyć Virtual Network](../virtual-network/quick-create-portal.md)

- [Jak utworzyć sieciowej grupy zabezpieczeń z konfiguracją zabezpieczeń](../virtual-network/tutorial-filter-network-traffic.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: Użyj zautomatyzowanych narzędzi do monitorowania konfiguracji zasobów sieciowych i wykrywania zmian

**Wskazówki**: Użyj dziennika aktywności platformy Azure do monitorowania konfiguracji zasobów i wykrywania zmian w zasobach platformy Azure. Utwórz alerty w Azure Monitor, które będą wyzwalane po wprowadzeniu zmian w zasobach krytycznych.

- [Jak wyświetlać i pobierać zdarzenia dziennika aktywności platformy Azure](/azure/azure-monitor/platform/activity-log#view-the-activity-log)

- [Jak utworzyć alerty w Azure Monitor](/azure/azure-monitor/platform/alerts-activity-log)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

## <a name="logging-and-monitoring"></a>Rejestrowanie i monitorowanie

*Aby uzyskać więcej informacji, zobacz temat [Azure Security test: rejestrowanie i monitorowanie](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2,1: Użyj źródeł synchronizacji zatwierdzonego czasu

**Wskazówki**: Firma Microsoft przechowuje źródła czasu dla zasobów platformy Azure. Można jednak zarządzać ustawieniami synchronizacji czasu dla zasobów obliczeniowych.

- [Synchronizacja czasu dla maszyn wirtualnych z systemem Windows na platformie Azure](../virtual-machines/windows/time-sync.md)

**Odpowiedzialność**: Współużytkowane

**Monitorowanie Azure Security Center**: brak

### <a name="22-configure-central-security-log-management"></a>2,2: Skonfiguruj centralne zarządzanie dziennikami zabezpieczeń

**Wskazówki**: Włączanie ustawień diagnostycznych dziennika aktywności platformy Azure i wysyłanie dzienników do obszaru roboczego log Analytics, usługi Azure Event Hub lub konta usługi Azure Storage w celu archiwizacji. Dzienniki aktywności zapewniają wgląd w operacje, które zostały wykonane na Azure DevTest Labs wystąpieniach na poziomie płaszczyzny zarządzania. Korzystając z danych dziennika aktywności platformy Azure, można określić "co, kto i kiedy" w przypadku operacji zapisu (PUT, POST, DELETE) wykonanych na poziomie płaszczyzny zarządzania dla wystąpień usługi DevTest Labs.

- [Tworzenie ustawień diagnostycznych w celu wysyłania metryk i dzienników platformy do różnych miejsc docelowych](/azure/azure-monitor/platform/diagnostic-settings)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: Włączanie rejestrowania inspekcji dla zasobów platformy Azure

**Wskazówki**: Włączanie ustawień diagnostycznych dziennika aktywności platformy Azure i wysyłanie dzienników do obszaru roboczego log Analytics, usługi Azure Event Hub lub konta usługi Azure Storage w celu archiwizacji. Dzienniki aktywności zapewniają wgląd w operacje, które zostały wykonane na Azure DevTest Labs wystąpieniach na poziomie płaszczyzny zarządzania. Korzystając z danych dziennika aktywności platformy Azure, można określić "co, kto i kiedy" dla operacji zapisu (PUT, POST, DELETE) wykonanych na poziomie płaszczyzny zarządzania dla wystąpień usługi DevTest Labs.

- [Tworzenie ustawień diagnostycznych w celu wysyłania metryk i dzienników platformy do różnych miejsc docelowych](/azure/azure-monitor/platform/diagnostic-settings)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: Zbierz dzienniki zabezpieczeń z systemów operacyjnych

**Wskazówki**: Azure DevTest Labs maszyny wirtualne są tworzone i własnością klienta. W związku z tym organizacja jest odpowiedzialna za monitorowanie. Aby monitorować system operacyjny obliczeń, można użyć Azure Security Center. Dane zbierane przez Security Center z systemu operacyjnego obejmują typ i wersję systemu operacyjnego, system operacyjny (dzienniki zdarzeń systemu Windows), uruchomione procesy, nazwę komputera, adresy IP i zalogowanego użytkownika. Agent Log Analytics również zbiera pliki zrzutu awaryjnego.

Aby uzyskać więcej informacji, zobacz następujące artykuły:

- [Jak zbierać dzienniki wewnętrznego hosta maszyny wirtualnej platformy Azure z Azure Monitor](/azure/azure-monitor/learn/quick-collect-azurevm)

- [Omówienie zbierania danych Azure Security Center](../security-center/security-center-enable-data-collection.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="25-configure-security-log-storage-retention"></a>2,5: Konfigurowanie przechowywania magazynu dzienników zabezpieczeń

**Wskazówki**: w Azure monitor ustawić okres przechowywania dziennika dla log Analytics obszarów roboczych skojarzonych z wystąpieniami Azure DevTest Labs zgodnie z regulacjami zgodności w organizacji.

- [Aby uzyskać więcej informacji, zobacz następujący artykuł](/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="26-monitor-and-review-logs"></a>2,6: dzienniki monitorowania i przeglądania

**Wskazówki**: Włączanie ustawień diagnostycznych dziennika aktywności platformy Azure i wysyłanie dzienników do obszaru roboczego log Analytics. Uruchom zapytania w Log Analytics, aby wyszukiwać terminy, identyfikować trendy, analizować wzorce i udostępniać wiele innych szczegółowych informacji na podstawie danych dziennika aktywności, które mogły zostać zebrane dla Azure DevTest Labs.

Aby uzyskać więcej informacji, zobacz następujące artykuły:

- [Jak włączyć ustawienia diagnostyczne dla dziennika aktywności platformy Azure](/azure/azure-monitor/platform/diagnostic-settings)

- [Jak zbierać i analizować dzienniki aktywności platformy Azure w obszarze roboczym Log Analytics w Azure Monitor](/azure/azure-monitor/platform/activity-log)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: Włączanie alertów dla nietypowych działań

**Wskazówki**: używanie obszaru roboczego usługi Azure log Analytics do monitorowania i generowania alertów dotyczących nietypowych działań w dziennikach zabezpieczeń i zdarzeniach związanych z Azure DevTest Labs.

- [Jak ostrzec dane dziennika usługi log Analytics](/azure/azure-monitor/learn/tutorial-response)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

## <a name="identity-and-access-control"></a>Tożsamość i kontrola dostępu

*Aby uzyskać więcej informacji, zobacz informacje o [teście zabezpieczeń Azure: Identity i Access Control](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: obsługa spisu kont administracyjnych

**Wskazówki**: Azure Active Directory (Azure AD) ma wbudowane role, które muszą być jawnie przypisane i są queryable. Przy użyciu modułu Azure AD PowerShell można uruchamiać zapytania ad hoc w celu odnajdywania kont należących do grup administracyjnych.

- [Jak uzyskać rolę katalogu w usłudze Azure AD przy użyciu programu PowerShell](/powershell/module/azuread/get-azureaddirectoryrole)

- [Jak uzyskać członków roli katalogu w usłudze Azure AD przy użyciu programu PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember)

- [Role Azure DevTest Labs](devtest-lab-add-devtest-user.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="32-change-default-passwords-where-applicable"></a>3,2: Zmień domyślne hasła, jeśli ma to zastosowanie

**Wskazówki**: Azure Active Directory (Azure AD) nie ma koncepcji domyślnych haseł. Inne zasoby platformy Azure wymagające hasła wymuszają utworzenie hasła przy użyciu wymagań dotyczących złożoności i minimalnej długości hasła, które różnią się w zależności od usługi. Użytkownik jest odpowiedzialny za aplikacje innych firm i usługi Marketplace, które mogą korzystać z domyślnych haseł.

DevTest Labs nie ma koncepcji domyślnych haseł.

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: Użyj dedykowanych kont administracyjnych

**Wskazówki**: Tworzenie standardowych procedur operacyjnych dotyczących korzystania z dedykowanych kont administracyjnych. Użyj Azure Security Center Zarządzanie tożsamościami i dostępem, aby monitorować liczbę kont administracyjnych.

Ponadto, aby ułatwić śledzenie dedykowanych kont administracyjnych, można użyć zaleceń z Azure Security Center lub wbudowanych zasad platformy Azure, takich jak:

- Do subskrypcji powinien być przypisany więcej niż jeden właściciel

- Przestarzałe konta z uprawnieniami właściciela powinny zostać usunięte z subskrypcji
- Konta zewnętrzne z uprawnieniami właściciela powinny zostać usunięte z subskrypcji

- [Jak używać Azure Security Center do monitorowania tożsamości i dostępu (wersja zapoznawcza)](../security-center/security-center-identity-access.md)

- [Jak używać Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Role Azure DevTest Labs](devtest-lab-add-devtest-user.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3,4: Korzystaj z logowania jednokrotnego (SSO) z usługą Azure Active Directory

**Wskazówki**: DevTest Labs używa usługi Azure Active Directory (Azure AD) do zarządzania tożsamościami. Te dwa kluczowe aspekty należy wziąć pod uwagę w przypadku udzielenia użytkownikom dostępu do środowiska w oparciu o DevTest Labs:
- Zarządzanie zasobami: zapewnia dostęp do Azure Portal do zarządzania zasobami (tworzenia maszyn wirtualnych, tworzenia środowisk, uruchamiania, zatrzymywania, ponownego uruchamiania, usuwania i stosowania artefaktów itd.). Zarządzanie zasobami odbywa się na platformie Azure przy użyciu kontroli dostępu opartej na rolach (Azure RBAC). Przypisujesz role do użytkowników i ustawisz uprawnienia na poziomie zasobów i dostępu.
- Maszyny wirtualne (na poziomie sieci): w konfiguracji domyślnej maszyny wirtualne używają konta administratora lokalnego. Jeśli istnieje dostępna domena (Azure Active Directory Domain Services (Azure AD DS), domena lokalna lub domena oparta na chmurze), komputery można przyłączyć do domeny. W celu nawiązania połączenia z maszynami użytkownicy mogą używać ich tożsamości opartych na domenie.

- [Architektura referencyjna dla DevTest Labs](https://docs.microsoft.com/azure/devtest-labs/devtest-lab-reference-architecture#architecture)

- [Opis logowania jednokrotnego w usłudze Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Użyj uwierzytelniania wieloskładnikowego, aby uzyskać dostęp oparty na Azure Active Directory

**Wskazówki**: Włączanie uwierzytelniania wieloskładnikowego Azure Active Directory (Azure AD) i przestrzeganie Azure Security Center zalecenia dotyczące zarządzania tożsamościami i dostępem.

- [Jak włączyć uwierzytelnianie wieloskładnikowe na platformie Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Jak monitorować tożsamość i dostęp w Azure Security Center](../security-center/security-center-identity-access.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: Używaj dedykowanych maszyn (uprzywilejowany dostęp do stacji roboczych) dla wszystkich zadań administracyjnych

**Wskazówki**: Użyj stacji roboczych dostępu uprzywilejowanego (dostępem uprzywilejowanym) z uwierzytelnianiem wieloskładnikowym skonfigurowanym do logowania się i konfigurowania zasobów platformy Azure. 

- [Dowiedz się więcej o stacjach roboczych uprzywilejowanego dostępu](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)
 
- [Jak włączyć uwierzytelnianie wieloskładnikowe na platformie Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: Rejestruj i Ostrzegaj o podejrzanych działaniach z kont administracyjnych

**Wskazówki**: Użyj raportów zabezpieczeń usługi Azure Active Directory (Azure AD) na potrzeby generowania dzienników i alertów w przypadku wystąpienia podejrzanych lub niebezpiecznych działań w środowisku. Użyj Azure Security Center, aby monitorować działania związane z tożsamościami i dostępem.

- [Identyfikowanie użytkowników usługi Azure AD oflagowanych na skutek ryzykownego działania](../active-directory/identity-protection/overview-identity-protection.md)

- [Jak monitorować działania użytkowników związane z tożsamościami i dostępem w usłudze Azure Security Center](../security-center/security-center-identity-access.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="38-manage-azure-resources-only-from-approved-locations"></a>3,8: Zarządzaj zasobami platformy Azure tylko z zatwierdzonych lokalizacji

**Wskazówki**: Użyj dostępu warunkowego o nazwie Locations, aby zezwolić na dostęp tylko do określonych logicznych grup zakresów adresów IP lub krajów/regionów.

- [Jak skonfigurować nazwane lokalizacje na platformie Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="39-use-azure-active-directory"></a>3,9: Użyj Azure Active Directory

**Wskazówki**: Użyj Azure Active Directory (Azure AD) jako centralnego systemu uwierzytelniania i autoryzacji. Usługa Azure AD chroni dane przy użyciu silnego szyfrowania danych przechowywanych i przesyłanych. Usługa Azure AD również Sole, skróty i bezpieczne przechowywanie poświadczeń użytkownika.

- [Jak utworzyć i skonfigurować wystąpienie usługi Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: regularnie Przeglądaj i Uzgodnij dostęp użytkowników

**Wskazówki**: Azure Active Directory (Azure AD) zawiera dzienniki ułatwiające wykrywanie starych kont. Ponadto za pomocą przeglądów dostępu do tożsamości platformy Azure można efektywnie zarządzać członkostwem w grupach, dostępem do aplikacji dla przedsiębiorstw i przypisaniami ról. Dostęp użytkowników może być regularnie przeglądany, aby upewnić się, że tylko Ci użytkownicy mają ciągły dostęp.

- [Informacje o raportowaniu usługi Azure AD](../active-directory/reports-monitoring/overview-reports.md)

- [Jak korzystać z przeglądów dostępu do tożsamości platformy Azure](../active-directory/governance/access-reviews-overview.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: Monitor próbuje uzyskać dostęp do zdezaktywowanych poświadczeń

**Wskazówki**: masz dostęp do źródeł danych dotyczących logowania w usłudze Azure Active Directory (Azure AD), inspekcji i zdarzeń związanych z ryzykiem, które umożliwiają integrację z dowolnymi narzędziami do zarządzania informacjami i zdarzeniami zabezpieczeń (Siem).

Proces ten można usprawnić, tworząc ustawienia diagnostyczne dla kont użytkowników usługi Azure AD i wysyłając dzienniki inspekcji i dzienniki logowania do obszaru roboczego Log Analytics. Można skonfigurować alerty w obszarze roboczym Log Analytics.

- [Jak zintegrować dzienniki aktywności platformy Azure z usługą Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: odchylenia zachowania podczas logowania do konta

**Wskazówki**: Użyj funkcji ryzyka i ochrony tożsamości Azure Active Directory (Azure AD), aby skonfigurować automatyczne odpowiedzi na wykryte podejrzane działania związane z tożsamościami użytkowników.

- [Jak wyświetlić ryzykowne logowania w usłudze Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

- [Jak skonfigurować i włączyć zasady dotyczące ryzyka związanego z ochroną tożsamości](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

## <a name="data-protection"></a>Ochrona danych

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: ochrona danych](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: przechowywanie spisu poufnych informacji

**Wskazówki**: Użyj tagów, aby pomóc w śledzeniu zasobów platformy Azure, które przechowują lub przetwarzają informacje poufne.

- [Tworzenie i używanie tagów](../azure-resource-manager/management/tag-resources.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4,6: Korzystaj z usługi Azure RBAC, aby zarządzać dostępem do zasobów

**Wskazówki**: Użyj kontroli dostępu opartej na rolach (Azure RBAC) na platformie Azure, aby kontrolować dostęp do laboratoriów w Azure DevTest Labs.

- [Jak skonfigurować usługę Azure RBAC](../role-based-access-control/role-assignments-portal.md)

- [Informacje o rolach w DevTest Labs](devtest-lab-add-devtest-user.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: Rejestruj i Ostrzegaj o zmianach krytycznych zasobów platformy Azure

**Wskazówki**: Użyj Azure monitor z dziennikiem aktywności platformy Azure, aby utworzyć alerty dla sytuacji, gdy zmiany są wprowadzane do wystąpień DevTest Labs i innych krytycznych lub powiązanych zasobów.

- [Jak utworzyć alerty dla zdarzeń dziennika aktywności platformy Azure](/azure/azure-monitor/platform/alerts-activity-log)

- [Jak utworzyć alerty dla zdarzeń dziennika aktywności DevTest Labs](create-alerts.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

## <a name="inventory-and-asset-management"></a>Zarządzanie magazynem i zasobami

*Aby uzyskać więcej informacji, zobacz temat [Azure Security test: Inventory and Asset Management](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: Użyj rozwiązania automatycznego odnajdywania zasobów

**Wskazówki**: Użyj grafu zasobów platformy Azure do wykonywania zapytań i odnajdywania wszystkich zasobów (w tym zasobów DevTest Labs) w ramach subskrypcji. Upewnij się, że masz odpowiednie uprawnienia (odczyt) w dzierżawie i możesz wyliczyć wszystkie subskrypcje i zasoby platformy Azure w ramach subskrypcji.

- [Jak tworzyć zapytania za pomocą usługi Azure Graph](../governance/resource-graph/first-query-portal.md)

- [Jak wyświetlić subskrypcje platformy Azure](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-4.8.0&amp;preserve-view=true)

- [Opis kontroli RBAC platformy Azure](../role-based-access-control/overview.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="62-maintain-asset-metadata"></a>6,2: Konserwowanie metadanych zasobów

**Wskazówki**: stosowanie tagów do zasobów platformy Azure, dzięki czemu metadane są logicznie zorganizowane zgodnie z taksonomią.

- [Tworzenie i używanie tagów](../azure-resource-manager/management/tag-resources.md)

- [Konfigurowanie tagów dla DevTest Labs](devtest-lab-add-tag.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: Usuń nieautoryzowane zasoby platformy Azure

**Wskazówki**: korzystanie z tagowania, grup zarządzania i oddzielnych subskrypcji oraz oddzielnych laboratoriów, gdzie to konieczne, do organizowania i śledzenia zasobów Lab i związanych z laboratorium. Regularnie Uzgadniaj spis i zapewnij szybkie usuwanie nieautoryzowanych zasobów z subskrypcji.

- [Jak utworzyć dodatkowe subskrypcje platformy Azure](../cost-management-billing/manage/create-subscription.md)

- [Jak utworzyć Grupy zarządzania](../governance/management-groups/create-management-group-portal.md)

- [Jak utworzyć laboratorium przy użyciu DevTest Labs](devtest-lab-create-lab.md)

- [Tworzenie i używanie tagów](devtest-lab-add-tag.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6,4: Definiowanie i obsługa spisu zatwierdzonych zasobów platformy Azure

**Wskazówki**: Tworzenie spisu zatwierdzonych zasobów platformy Azure oraz zatwierdzonego oprogramowania dla zasobów obliczeniowych zgodnie z potrzebami organizacji. Jako administrator subskrypcji można także użyć funkcji adaptacyjnego sterowania aplikacjami, która Azure Security Center ułatwia zdefiniowanie zestawu aplikacji, które mogą być uruchamiane w skonfigurowanych grupach maszyn laboratorium. Ta funkcja jest dostępna zarówno na platformie Azure, jak i w systemie Windows (wszystkie wersje, klasyczne lub Azure Resource Manager) i na maszynach z systemem Linux.

- [Jak włączyć adaptacyjną kontrolę aplikacji](../security-center/security-center-adaptive-application.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: Monitoruj niezatwierdzone zasoby platformy Azure

**Wskazówki**: Użyj usługi Azure Policy, aby wprowadzić ograniczenia dotyczące typu zasobów, które mogą być tworzone w subskrypcjach klientów, korzystając z następujących wbudowanych definicji zasad:

- Niedozwolone typy zasobów

- Dozwolone typy zasobów

Należy również użyć grafu zasobów platformy Azure do wykonywania zapytań i odnajdywania zasobów w ramach subskrypcji. Może pomóc w wysokich środowiskach opartych na zabezpieczeniach, takich jak te z kontami magazynu.

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

- [Jak tworzyć zapytania za pomocą usługi Azure Graph](../governance/resource-graph/first-query-portal.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: Monitoruj niezatwierdzone aplikacje oprogramowania w ramach zasobów obliczeniowych

**Wskazówki**: Azure Automation zapewnia pełną kontrolę podczas wdrażania, działania i likwidowania obciążeń i zasobów. Jako administrator subskrypcji możesz użyć spisu maszyn wirtualnych platformy Azure, aby zautomatyzować zbieranie informacji o całym oprogramowaniu na maszynach wirtualnych DevTest Labs w ramach subskrypcji. Właściwości nazwa oprogramowania, wersja, Wydawca i czas odświeżania są dostępne w Azure Portal. Aby uzyskać dostęp do daty instalacji i innych informacji, klient musi włączyć diagnostykę na poziomie gościa i przenieść dzienniki zdarzeń systemu Windows do obszaru roboczego Log Analytics.

Oprócz używania Change Tracking do monitorowania aplikacji programowych, adaptacyjnych kontroli aplikacji w Azure Security Center korzystania z uczenia maszynowego do analizowania aplikacji uruchomionych na maszynach i tworzenia listy dozwolonych na podstawie tej analizy. Ta funkcja znacznie upraszcza proces konfigurowania i konserwowania zasad listy dozwolonych aplikacji, co pozwala uniknąć niechcianego oprogramowania, które ma być używane w danym środowisku. Można skonfigurować tryb inspekcji lub Tryb wymuszania. Tryb inspekcji tylko przeprowadza inspekcję działania na chronionych maszynach wirtualnych. Tryb wymuszania wymusza reguły i sprawdza, czy aplikacje, które nie są dozwolone do uruchomienia, są zablokowane. 

- [Wprowadzenie do usługi Azure Automation](../automation/automation-intro.md)

- [Jak włączyć Spis maszyn wirtualnych platformy Azure](../automation/automation-tutorial-installed-software.md)

- [Zrozumienie adaptacyjnych kontrolek aplikacji](../security-center/security-center-adaptive-application.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: Usuń niezatwierdzone zasoby platformy Azure i aplikacje oprogramowania

**Wskazówki**: Azure Automation zapewnia pełną kontrolę podczas wdrażania, działania i likwidowania obciążeń i zasobów. Administrator subskrypcji może używać Change Tracking do identyfikowania wszystkich programów zainstalowanych na maszynach wirtualnych hostowanych w DevTest Labs. Aby usunąć nieautoryzowane oprogramowanie, można zaimplementować własny proces lub użyć konfiguracji stanu Azure Automation.

- [Wprowadzenie do usługi Azure Automation](../automation/automation-intro.md)

- [Śledź zmiany w środowisku przy użyciu rozwiązania Change Tracking](../automation/change-tracking/overview.md)

- [Przegląd konfiguracji stanu Azure Automation](../automation/automation-dsc-overview.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="68-use-only-approved-applications"></a>6,8: Używaj tylko zatwierdzonych aplikacji

**Wskazówki**: jako administrator subskrypcji można użyć Azure Security Center adaptacyjnych kontroli aplikacji, aby upewnić się, że wykonywane jest tylko autoryzowane oprogramowanie i wszystkie nieautoryzowane oprogramowanie jest blokowane na maszynach wirtualnych platformy Azure hostowanych w laboratoriach DevTest Labs.

- [Jak używać Azure Security Center adaptacyjnych kontroli aplikacji](../security-center/security-center-adaptive-application.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="69-use-only-approved-azure-services"></a>6,9: Używaj tylko zatwierdzonych usług platformy Azure

**Wskazówki**: Użyj usługi Azure Policy, aby wprowadzić ograniczenia dotyczące typu zasobów, które mogą być tworzone w subskrypcjach klientów, korzystając z następujących wbudowanych definicji zasad:
- Niedozwolone typy zasobów
- Dozwolone typy zasobów

Materiał referencyjny:

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

- [Jak odmówić określonego typu zasobu za pomocą Azure Policy](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6,10: przechowywanie spisu zatwierdzonych tytułów oprogramowania

**Wskazówki**: adaptacyjne Sterowanie aplikacjami to inteligentne, zautomatyzowane i kompleksowe rozwiązanie od Azure Security Center, które pomaga kontrolować, które aplikacje mogą być uruchamiane na maszynach z platformą Azure i poza platformą Azure (Windows i Linux) hostowane w DevTest Labs. Pamiętaj, że musisz być administratorem subskrypcji, aby skonfigurować to ustawienie dla bazowych zasobów obliczeniowych hostowanych w DevTest Labs. Zaimplementuj rozwiązanie innych firm, jeśli to ustawienie nie spełnia wymagań organizacji.

- [Jak używać Azure Security Center adaptacyjnych kontroli aplikacji](../security-center/security-center-adaptive-application.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: Ogranicz możliwość korzystania przez użytkowników z Azure Resource Manager

**Wskazówki**: korzystanie z dostępu warunkowego platformy Azure w celu ograniczenia możliwości korzystania przez użytkowników z Azure Resource Manager przez skonfigurowanie **dostępu blokowania** dla **Microsoft Azure aplikacji zarządzania** .

- [Jak skonfigurować dostęp warunkowy w celu blokowania dostępu do Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="612-limit-users-ability-to-execute-scripts-in-compute-resources"></a>6,12: Ogranicz możliwość wykonywania skryptów w zasobach obliczeniowych przez użytkowników

**Wskazówki**: w zależności od typu skryptów, można użyć konfiguracji specyficznych dla systemu operacyjnego lub zasobów innych firm, aby ograniczyć możliwość wykonywania skryptów w ramach maszyn wirtualnych hostowanych w DevTest Labs. Możesz również użyć Azure Security Center adaptacyjnych kontroli aplikacji, aby upewnić się, że tylko autoryzowane oprogramowanie i wszystkie nieautoryzowane oprogramowanie zostało zablokowane na podstawowych maszynach wirtualnych platformy Azure.

- [Jak kontrolować wykonywanie skryptów programu PowerShell w środowiskach systemu Windows](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-7&amp;preserve-view=true)

- [Jak używać Azure Security Center adaptacyjnych kontroli aplikacji](../security-center/security-center-adaptive-application.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: fizyczne lub logiczne rozdzielenie aplikacji wysokiego ryzyka

**Wskazówki**: aplikacje o wysokim ryzyku wdrożone w środowisku platformy Azure mogą być izolowane przy użyciu sieci wirtualnej, podsieci, subskrypcji, grup zarządzania itd. i dostatecznie zabezpieczone przez zaporę platformy Azure, zaporę aplikacji sieci Web (WAF) lub sieciową grupę zabezpieczeń (sieciowej grupy zabezpieczeń).

- [Konfigurowanie sieci wirtualnej dla usługi DevTest Labs](devtest-lab-configure-vnet.md)

- [Omówienie Zapory platformy Azure](../web-application-firewall/overview.md)

- [Omówienie zapory aplikacji sieci Web](../virtual-network/network-security-groups-overview.md)

- [Omówienie zabezpieczeń sieci](security-baseline.md)

- [Organizowanie zasobów przy użyciu grup zarządzania platformy Azure](../governance/management-groups/overview.md)

- [Przewodnik po decyzjach związanych z subskrypcjami](/azure/cloud-adoption-framework/decision-guides/subscriptions/)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

## <a name="secure-configuration"></a>Bezpieczna konfiguracja

*Aby uzyskać więcej informacji, zobacz temat [Azure Security test: bezpieczna konfiguracja](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: Ustanów bezpieczne konfiguracje dla wszystkich zasobów platformy Azure

**Wskazówki**: Użyj aliasów Azure Policy do tworzenia zasad niestandardowych w celu inspekcji lub wymuszania konfiguracji zasobów platformy Azure utworzonych w ramach DevTest Labs. Możesz również użyć wbudowanych definicji Azure Policy.

Ponadto Azure Resource Manager ma możliwość eksportowania szablonu w JavaScript Object Notation (JSON), który powinien zostać sprawdzony, aby upewnić się, że konfiguracje spełniają/przekroczą wymagania dotyczące zabezpieczeń organizacji.

Możesz również użyć zaleceń z Azure Security Center jako bezpiecznej linii bazowej konfiguracji dla zasobów platformy Azure.

- [Jak wyświetlić dostępne aliasy Azure Policy](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-4.8.0&amp;preserve-view=true)

- [Samouczek: Tworzenie zasad i zarządzanie nimi w celu wymuszenia zgodności](../governance/policy/tutorials/create-and-manage.md)

- [Eksport jednego i kilku zasobów do szablonu w Azure Portal](../azure-resource-manager/templates/export-template-portal.md)

- [Zalecenia dotyczące zabezpieczeń — przewodnik referencyjny](../security-center/recommendations-reference.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: Ustanów bezpieczne konfiguracje systemów operacyjnych

**Wskazówki**: Użyj zaleceń Azure Security Center, aby zachować konfiguracje zabezpieczeń dla wszystkich podstawowych zasobów obliczeniowych utworzonych w ramach DevTest Labs. Ponadto możesz użyć niestandardowych obrazów systemu operacyjnego lub Azure Automation konfiguracji stanu lub artefaktów DevTest Labs, aby określić konfigurację zabezpieczeń systemu operacyjnego wymaganego przez organizację.

- [Jak monitorować zalecenia dotyczące Azure Security Center](../security-center/security-center-recommendations.md)

- [Zalecenia dotyczące zabezpieczeń — przewodnik referencyjny](../security-center/recommendations-reference.md)

- [Przegląd konfiguracji stanu Azure Automation](../automation/automation-dsc-overview.md)

- [Przekazywanie wirtualnego dysku twardego i używanie go do tworzenia nowych maszyn wirtualnych z systemem Windows na platformie Azure](../virtual-machines/windows/upload-generalized-managed.md)

- [Tworzenie maszyny wirtualnej z systemem Linux na podstawie dysku niestandardowego przy użyciu interfejsu wiersza polecenia platformy Azure](../virtual-machines/linux/upload-vhd.md)

- [Tworzenie i dystrybuowanie obrazów niestandardowych do wielu DevTest Labs](image-factory-save-distribute-custom-images.md)

**Odpowiedzialność**: Współużytkowane

**Monitorowanie Azure Security Center**: brak

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: obsługa bezpiecznych konfiguracji zasobów platformy Azure

**Wskazówki**: Użyj Azure Policy **Odmów** i **Wdróż, jeśli nie istnieją** reguły, aby wymusić bezpieczne ustawienia dla zasobów platformy Azure utworzonych w ramach usługi DevTest Labs. Ponadto, możesz użyć szablonów Azure Resource Manager, aby zachować konfigurację zabezpieczeń zasobów platformy Azure wymaganych przez organizację.

- [Zrozumienie efektów Azure Policy](../governance/policy/concepts/effects.md)

- [Tworzenie zasad i zarządzanie nimi w celu wymuszania zgodności](../governance/policy/tutorials/create-and-manage.md)

- [Przegląd szablonów Azure Resource Manager](../azure-resource-manager/templates/overview.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: Zachowaj konfiguracje bezpiecznego systemu operacyjnego

**Wskazówki**: Postępuj zgodnie z zaleceniami Azure Security Center na temat przeprowadzania ocen luk w zabezpieczeniach na podstawie własnych zasobów obliczeniowych platformy Azure utworzonych w ramach laboratorium. Ponadto można użyć szablonów Azure Resource Manager, niestandardowych obrazów systemu operacyjnego lub konfiguracji stanu Azure Automation, aby zachować konfigurację zabezpieczeń systemu operacyjnego wymaganego przez organizację. Możesz również użyć rozwiązania fabryki obrazów, które jest rozwiązaniem typu "Konfiguracja jako kod", które automatycznie kompiluje i dystrybuuje obrazy na bieżąco ze wszystkimi wymaganymi konfiguracjami.

Ponadto w firmie Microsoft są zarządzane i utrzymywane w portalu Azure Marketplace obrazy maszyn wirtualnych opublikowane przez firmę Microsoft.

- [Jak zaimplementować zalecenia dotyczące oceny luk w zabezpieczeniach Azure Security Center](../security-center/deploy-vulnerability-assessment-vm.md)

- [Przegląd konfiguracji stanu Azure Automation](../automation/automation-dsc-overview.md)

- [Przykładowy skrypt do przekazania wirtualnego dysku twardego na platformę Azure i utworzenia nowej maszyny wirtualnej](/azure/virtual-machines/scripts/virtual-machines-windows-powershell-upload-generalized-script)

- [Jak utworzyć fabrykę obrazów w DevTest Labs](image-factory-create.md)

**Odpowiedzialność**: Współużytkowane

**Monitorowanie Azure Security Center**: brak

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: bezpiecznie przechowuj konfigurację zasobów platformy Azure

**Wskazówki**: Użyj usługi Azure DevOps, aby bezpiecznie przechowywać kod, taki jak niestandardowe zasady platformy Azure, szablony Azure Resource Manager i skrypty konfiguracji żądanego stanu oraz zarządzać nimi. Aby uzyskać dostęp do zasobów zarządzanych w usłudze Azure DevOps, można udzielić lub odmówić uprawnień określonym użytkownikom, wbudowanym grupom zabezpieczeń lub grupom zdefiniowanym w Azure Active Directory (Azure AD) w przypadku integracji z usługą Azure DevOps.

- [Samouczek usługi git Azure Repos](/azure/devops/repos/git/gitworkflow)

- [Informacje o uprawnieniach i grupach](https://docs.microsoft.com/azure/devops/organizations/security/about-permissions?view=azure-devops&amp;tabs=preview-page&amp;preserve-view=true)

- [Integracja między Azure DevTest Labs i przepływem pracy usługi Azure DevOps](devtest-lab-dev-ops.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: bezpieczne przechowywanie niestandardowych obrazów systemu operacyjnego

**Wskazówki**: Jeśli używasz obrazów niestandardowych, użyj kontroli dostępu opartej na rolach (Azure RBAC) na platformie Azure, aby zapewnić dostęp do obrazów tylko autoryzowanym użytkownikom. Za pomocą udostępnionej galerii obrazów możesz udostępnić swoje obrazy do określonych laboratoriów, które ich potrzebują. W przypadku obrazów kontenerów Zapisz je w Azure Container Registry i Użyj usługi Azure RBAC, aby zapewnić dostęp do obrazów tylko autoryzowanym użytkownikom.

- [Opis kontroli RBAC platformy Azure](../role-based-access-control/rbac-and-directory-admin-roles.md)

- [Jak skonfigurować usługę Azure RBAC](../role-based-access-control/quickstart-assign-role-user-portal.md)

- [Konfigurowanie udostępnionej galerii obrazów dla DevTest Labs](configure-shared-image-gallery.md)

- [Informacje na temat usługi Azure RBAC dla Container Registry](../container-registry/container-registry-roles.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: Wdrażanie narzędzi do zarządzania konfiguracją dla zasobów platformy Azure

**Wskazówki**: Definiowanie i implementowanie standardowych konfiguracji zabezpieczeń dla zasobów platformy Azure przy użyciu Azure Policy. Użyj aliasów Azure Policy, aby utworzyć zasady niestandardowe do inspekcji lub wymuszania konfiguracji sieci zasobów platformy Azure utworzonych w ramach DevTest Labs. Mogą również korzystać z wbudowanych definicji zasad związanych z konkretnymi zasobami. Ponadto można użyć Azure Automation do wdrożenia zmian konfiguracji.

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

- [Jak używać aliasów](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure#aliases)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7,8: Wdrażanie narzędzi do zarządzania konfiguracją dla systemów operacyjnych

**Wskazówki**: Azure Automation konfiguracja stanu to usługa zarządzania konfiguracją dla węzłów konfiguracji żądanego stanu (DSC) w dowolnym chmurze lub lokalnym centrum danych. Można łatwo dołączać maszyny, przypisywać do nich konfiguracje deklaracyjne i wyświetlać raporty pokazujące zgodność poszczególnych maszyn z wybranym określonym stanem. Możesz również napisać niestandardowy artefakt, który może zostać zainstalowany na każdej maszynie laboratoryjnej, aby upewnić się, że są one zgodne z zasadami organizacji. 

- [Dołączanie maszyn w celu zarządzania przez Azure Automation konfigurację stanu](../automation/automation-dsc-onboarding.md)

- [Tworzenie niestandardowych artefaktów dla maszyn wirtualnych z DevTest Labs](devtest-lab-artifact-author.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: Zaimplementuj automatyczne monitorowanie konfiguracji dla zasobów platformy Azure

**Wskazówki**: Użyj Azure Security Center, aby przeprowadzić skanowanie linii bazowej dla zasobów platformy Azure utworzonych w ramach DevTest Labs. Ponadto należy używać Azure Policy do powiadamiania i inspekcji konfiguracji zasobów platformy Azure.

- [Jak skorygować zalecenia w Azure Security Center](../security-center/security-center-remediate-recommendations.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: Zaimplementuj automatyczne monitorowanie konfiguracji dla systemów operacyjnych

**Wskazówki**: Użyj Azure Security Center, aby przeprowadzić skanowanie linii bazowej dla ustawień systemu operacyjnego i platformy Docker dla kontenerów, które są uruchamiane w środowisku laboratoryjnym.

- [Omówienie rekomendacji dotyczących kontenera usługi Azure Security Center](../security-center/container-security.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="711-manage-azure-secrets-securely"></a>7,11: bezpieczne zarządzanie wpisami tajnymi platformy Azure

**Wskazówki**: Użyj tożsamość usługi zarządzanej w połączeniu z Azure Key Vault, aby uprościć i zabezpieczyć poufne Zarządzanie aplikacjami w chmurze.

- [Konfigurowanie tożsamości zarządzanej do wdrażania środowisk Azure Resource Manager w usłudze DevTest Labs](use-managed-identities-environments.md)

- [Konfigurowanie tożsamości zarządzanej do wdrażania maszyn wirtualnych w usłudze DevTest Labs](enable-managed-identities-lab-vms.md)

- [Jak utworzyć magazyn kluczy](../key-vault/general/quick-create-portal.md)

- [Jak zapewnić uwierzytelnianie Key Vault przy użyciu tożsamości zarządzanej](../key-vault/general/authentication.md)

- [Jak przypisać zasady dostępu Key Vault](../key-vault/general/assign-access-policy-portal.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: bezpieczne i automatyczne zarządzanie tożsamościami

**Wskazówki**: Użyj tożsamości zarządzanych, aby zapewnić usługom platformy Azure automatyczną tożsamość zarządzaną w usłudze Azure Active Directory (Azure AD). Tożsamości zarządzane umożliwiają uwierzytelnianie w dowolnej usłudze, która obsługuje uwierzytelnianie usługi Azure AD, w tym Key Vault, bez żadnych poświadczeń w kodzie.

- [Konfigurowanie tożsamości zarządzanej do wdrażania środowisk Azure Resource Manager w usłudze DevTest Labs](use-managed-identities-environments.md)

- [Konfigurowanie tożsamości zarządzanej do wdrażania maszyn wirtualnych w usłudze DevTest Labs](enable-managed-identities-lab-vms.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: eliminowanie nieprzewidzianego narażenia na poświadczenia

**Wskazówki**: Implementuj skaner poświadczeń, aby identyfikować poświadczenia w kodzie. Skaner poświadczeń ułatwia również przenoszenie odnalezionych poświadczeń do bezpieczniejszych lokalizacji, takich jak usługa Azure Key Vault.

- [Jak skonfigurować skaner poświadczeń](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

## <a name="malware-defense"></a>Ochrona przed złośliwym oprogramowaniem

*Aby uzyskać więcej informacji, zobacz temat [Azure Security test: Obrona złośliwego oprogramowania](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-antimalware-software"></a>8,1: Użyj centralnie zarządzanego oprogramowania chroniącego przed złośliwym kodem

**Wskazówki**: Użyj programu Microsoft chroniącego przed złośliwym kodem dla platformy Azure w celu ciągłego monitorowania i obrony zasobów. W przypadku systemu Linux należy zastosować rozwiązanie chroniące przed złośliwym oprogramowaniem innej firmy.  Ponadto należy użyć wykrywania zagrożeń Azure Security Center dla usług danych w celu wykrywania złośliwego oprogramowania przekazanego do kont magazynu. 

- [Jak skonfigurować oprogramowanie chroniące przed złośliwym oprogramowaniem firmy Microsoft dla platformy Azure](../security/fundamentals/antimalware.md) 

- [Ochrona przed zagrożeniami w usłudze Azure Security Center](/azure/security-center/threat-protection)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: przeskanuj pliki przed przekazaniem do zasobów platformy Azure, które nie są obliczeniowe

**Wskazówki**: oprogramowanie chroniące przed złośliwym oprogramowaniem firmy Microsoft jest włączone na podstawowym hoście obsługującym usługi platformy Azure (na przykład Azure App Service hostowane w laboratorium), ale nie jest ono uruchamiane w Twojej zawartości. 

Wstępnie Skanuj pliki przekazane do zasobów platformy Azure, które nie są obliczeniowe, takie jak App Service, Data Lake Storage, Blob Storage itd. 

Użyj wykrywania zagrożeń Azure Security Center dla usług danych w celu wykrywania złośliwego oprogramowania przekazanego do kont magazynu. 

- [Informacje na temat ochrony przed złośliwym oprogramowaniem firmy Microsoft dla platformy Azure](../security/fundamentals/antimalware.md) 

- [Zrozumienie wykrywania zagrożeń Azure Security Center dla usług danych](/azure/security-center/security-center-alerts-data-services)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="83-ensure-antimalware-software-and-signatures-are-updated"></a>8,3: Upewnij się, że oprogramowanie chroniące przed złośliwym oprogramowaniem i podpisy zostały zaktualizowane

**Wskazówki**: po wdrożeniu usługa Microsoft chroniąca przed złośliwym kodem na platformie Azure automatycznie zainstaluje najnowsze aktualizacje sygnatur, platform i aparatu. Postępuj zgodnie z zaleceniami w Azure Security Center: "COMPUTE &amp; Apps", aby upewnić się, że wszystkie punkty końcowe dla podstawowych zasobów obliczeniowych DevTest Labs są aktualne przy użyciu najnowszych sygnatur. System operacyjny Windows może być dodatkowo chroniony przy użyciu dodatkowych zabezpieczeń w celu ograniczenia ryzyka ataków na ataki przez wirusy lub złośliwe oprogramowanie w usłudze Microsoft Defender Advanced Threat Protection, która integruje się z Azure Security Center.

- [Jak wdrożyć oprogramowanie chroniące przed złośliwym oprogramowaniem firmy Microsoft dla platformy Azure](../security/fundamentals/antimalware.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

## <a name="data-recovery"></a>Odzyskiwanie danych

*Aby uzyskać więcej informacji, zobacz [test dotyczący zabezpieczeń platformy Azure: odzyskiwanie danych](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: Zapewnij regularne zautomatyzowane przywracanie awaryjne

**Wskazówki**: obecnie Azure DevTest Labs nie obsługuje kopii zapasowych maszyn wirtualnych i migawek. Można jednak włączyć i skonfigurować Azure Backup na podstawowych maszynach wirtualnych platformy Azure hostowanych w usłudze DevTest Labs. Ponadto można skonfigurować żądaną częstotliwość i okres przechowywania automatycznych kopii zapasowych, o ile masz odpowiedni dostęp do podstawowych zasobów obliczeniowych. 

- [Omówienie kopii zapasowej maszyny wirtualnej platformy Azure](../backup/backup-azure-vms-introduction.md)

- [Tworzenie kopii zapasowej maszyny wirtualnej platformy Azure z ustawień maszyny wirtualnej](../backup/backup-azure-vms-first-look-arm.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: wykonaj kompletne kopie zapasowe systemu i Utwórz kopię zapasową wszystkich kluczy zarządzanych przez klienta

**Wskazówki**: obecnie Azure DevTest Labs nie obsługuje kopii zapasowych maszyn wirtualnych i migawek. Można jednak tworzyć migawki podstawowych maszyn wirtualnych platformy Azure hostowanych w laboratoriach DevTest Labs lub dyskach zarządzanych podłączonych do tych wystąpień przy użyciu programu PowerShell lub interfejsów API REST, o ile masz odpowiednie uprawnienia dostępu do podstawowych zasobów obliczeniowych. Można również utworzyć kopię zapasową wszystkich kluczy zarządzanych przez klienta w ramach Azure Key Vault.

Włącz Azure Backup na docelowych maszynach wirtualnych platformy Azure i pożądaną częstotliwość i okres przechowywania. Obejmuje to kompletną kopię zapasową stanu systemu. Jeśli używasz usługi Azure Disk Encryption, kopia zapasowa maszyny wirtualnej platformy Azure automatycznie obsługuje tworzenie kopii zapasowych kluczy zarządzanych przez klienta.

- [Tworzenie kopii zapasowych na maszynach wirtualnych platformy Azure korzystających z szyfrowania](../backup/backup-azure-vms-encryption.md)

- [Omówienie kopii zapasowej maszyny wirtualnej platformy Azure](../backup/backup-azure-vms-introduction.md)

- [Omówienie kopii zapasowej maszyny wirtualnej platformy Azure](../backup/backup-azure-vms-introduction.md)

- [Jak utworzyć kopię zapasową kluczy Key Vault na platformie Azure](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey?view=azps-4.8.0&amp;preserve-view=true)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: Weryfikuj wszystkie kopie zapasowe, w tym klucze zarządzane przez klienta

**Wskazówki**: Zapewnij okresowe wykonywanie operacji przywracania danych w ramach Azure Backup. W razie potrzeby przetestuj przywracanie zawartości do izolowanej sieci wirtualnej lub subskrypcji. Ponadto Przetestuj przywracanie kopii zapasowych kluczy zarządzanych przez klienta.

Jeśli używasz usługi Azure Disk Encryption, możesz przywrócić maszynę wirtualną platformy Azure z kluczami szyfrowania dysku. W przypadku korzystania z szyfrowania dysków można przywrócić maszynę wirtualną platformy Azure z kluczami szyfrowania dysku.

- [Tworzenie kopii zapasowych na maszynach wirtualnych platformy Azure korzystających z szyfrowania](../backup/backup-azure-vms-encryption.md)

- [Jak odzyskać pliki z kopii zapasowej maszyny wirtualnej platformy Azure](../backup/backup-azure-restore-files-from-vm.md)

- [Jak przywrócić klucze magazynu kluczy na platformie Azure](https://docs.microsoft.com/powershell/module/az.keyvault/restore-azkeyvaultkey?view=azps-4.8.0&amp;preserve-view=true)

- [Jak utworzyć kopię zapasową i przywrócić zaszyfrowaną maszynę wirtualną](../backup/backup-azure-vms-encryption.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: zapewnianie ochrony kopii zapasowych i kluczy zarządzanych przez klienta

**Wskazówki**: podczas tworzenia kopii zapasowej dysków zarządzanych przy użyciu Azure Backup maszyny wirtualne są szyfrowane przy użyciu szyfrowanie usługi Storage (SSE). Azure Backup może również tworzyć kopie zapasowe maszyn wirtualnych platformy Azure, które są szyfrowane przy użyciu Azure Disk Encryption. Azure Disk Encryption integruje się z kluczami szyfrowania funkcji BitLocker (BEKs), które są chronione w magazynie kluczy jako wpisy tajne. Azure Disk Encryption integruje się również z kluczami szyfrowania klucza Azure Key Vault (KEKs). Włącz Soft-Delete w Key Vault, aby chronić klucze przed przypadkowym lub złośliwym usunięciem.

- [Usuwanie nietrwałe dla maszyn wirtualnych](../backup/soft-delete-virtual-machines.md)

- [Azure Key Vault — omówienie usuwania nietrwałego](../key-vault/general/soft-delete-overview.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

## <a name="incident-response"></a>Reagowanie na zdarzenia

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: reagowanie na zdarzenia](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: Tworzenie przewodnika odpowiedzi na zdarzenia

**Wskazówki**: Tworzenie przewodnika odpowiedzi na zdarzenia dla organizacji. Upewnij się, że istnieją jednokierunkowe plany reagowania na zdarzenia, które definiują wszystkie role pracowników, a także etapy obsługi zdarzeń i zarządzania z wykrywania na potrzeby przeglądu po zdarzeniu. 

- [Wskazówki dotyczące tworzenia własnego procesu reagowania na zdarzenia zabezpieczeń](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/) 

- [Anatomia incydentu centrum Microsoft Security Response](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/) 

- [Skorzystaj z przewodnika obsługi zdarzeń zabezpieczeń komputera NIST, aby pomóc w tworzeniu własnego planu reagowania na zdarzenia](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: Tworzenie oceny incydentu i procedury priorytetyzacji

**Wskazówki**: Azure Security Center przypisuje ważność do każdego alertu, aby pomóc w ustaleniu, które alerty należy najpierw zbadać. Ważność jest oparta na tym, jak dobrze Security Center znajduje się w wyszukiwaniu lub analitycznym używanym do wystawiania alertu, a także poziom pewności, że istniało złośliwy wpływ na działanie, które prowadziło do alertu.

Ponadto należy oznaczyć subskrypcje przy użyciu tagów i utworzyć system nazewnictwa, aby identyfikować i klasyfikować zasoby platformy Azure, szczególnie te, które przetwarzają poufne dane.  Odpowiedzialność za korygowanie alertów zależy od zagrożenia dla zasobów platformy Azure i środowiska, w którym wystąpiło zdarzenie. 

- [Alerty zabezpieczeń w Centrum zabezpieczeń Azure](../security-center/security-center-alerts-overview.md) 

- [Organizowanie zasobów platformy Azure przy użyciu tagów](/azure/azure-resource-manager/resource-group-using-tags)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="103-test-security-response-procedures"></a>10,3: procedury odpowiedzi na zabezpieczenia testowe

**Wskazówki**: przeprowadzanie ćwiczeń w celu przetestowania możliwości reagowania na zdarzenia systemów w regularnych erzeach w celu ochrony zasobów platformy Azure. Zidentyfikuj słabe punkty i przerwy, a następnie Popraw plan odpowiedzi zgodnie z potrzebami. 

- [Przewodnik po publikacji NIST, który umożliwia testowanie, uczenie i wykonywanie programów dla planów i możliwości IT](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: Podaj szczegóły kontaktu dotyczącego zabezpieczeń i Skonfiguruj powiadomienia dotyczące alertów dotyczących zdarzeń związanych z zabezpieczeniami

**Wskazówki**: informacje kontaktowe dotyczące zdarzenia zabezpieczeń będą używane przez firmę Microsoft do skontaktowania się z Tobą, jeśli firma Microsoft Security Response Center (MSRC) wykryje, że dostęp do danych zostały nadane przez nielegalną lub nieautoryzowaną stronę. Przejrzyj zdarzenia po fakcie, aby upewnić się, że problemy zostały rozwiązane. 

- [Jak ustawić kontakt zabezpieczeń usługi Azure Security Center](../security-center/security-center-provide-security-contact-details.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: Uwzględnij alerty zabezpieczeń w systemie odpowiedzi na zdarzenia

**Wskazówki**: eksportowanie alertów i zaleceń dotyczących Azure Security Center przy użyciu funkcji eksportu ciągłego, która pomaga identyfikować zagrożenia dla zasobów platformy Azure. Eksport ciągły umożliwia wyeksportowanie alertów i zaleceń ręcznie lub w stały sposób ciągły. Możesz użyć łącznika danych Azure Security Center do przesyłania strumieniowego alertów do usługi Azure wskaźnikowej. 

- [Jak skonfigurować eksport ciągły](../security-center/continuous-export.md) 

- [Jak przesłać strumieniowo alerty do usługi Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: Automatyzowanie odpowiedzi na alerty zabezpieczeń

**Wskazówki**: korzystanie z funkcji automatyzacji przepływu pracy Azure Security Center do automatycznego wyzwalania odpowiedzi na alerty zabezpieczeń i zalecenia dotyczące ochrony zasobów platformy Azure. 

- [Jak skonfigurować automatyzację przepływu pracy w Security Center](../security-center/workflow-automation.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

## <a name="penetration-tests-and-red-team-exercises"></a>Testy penetracyjne i ćwiczenia typu „red team”

*Aby uzyskać więcej informacji, zobacz test [porównawczy zabezpieczeń platformy Azure: testy penetracji i czerwone ćwiczenia zespołu](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: Przeprowadź regularne testowanie penetracji zasobów platformy Azure i zadbaj o skorygowanie wszystkich krytycznych ustaleń dotyczących zabezpieczeń

**Wskazówki**: Postępuj zgodnie z regułami testowania penetracji Microsoft Cloud, aby upewnić się, że testy penetracji nie naruszają zasad firmy Microsoft. Korzystaj ze strategii firmy Microsoft i wykonywania testów typu „red team” i testów na żywo w zarządzanej przez firmę Microsoft infrastrukturze, usługach i aplikacjach w chmurze. 

- [Reguły testów penetracyjnych zaangażowania](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Testy typu „red team” w chmurze firmy Microsoft](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Odpowiedzialność**: Współużytkowane

**Monitorowanie Azure Security Center**: brak

## <a name="next-steps"></a>Następne kroki

- Zobacz [Omówienie testu porównawczego zabezpieczeń platformy Azure w wersji 2](/azure/security/benchmarks/overview)
- Dowiedz się więcej o [punktach odniesienia zabezpieczeń platformy Azure](/azure/security/benchmarks/security-baselines-overview)
