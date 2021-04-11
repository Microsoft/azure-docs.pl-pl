---
title: Podstawa zabezpieczeń platformy Azure dla Service Fabric
description: Linia bazowa zabezpieczeń Service Fabric zawiera wskazówki i zasoby dotyczące procedur związanych z wdrażaniem zaleceń dotyczących zabezpieczeń określonych w teście zabezpieczeń platformy Azure.
author: msmbaldwin
ms.service: service-fabric
ms.topic: conceptual
ms.date: 04/08/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: ee294cff85bb71b5c2a238fcf985fc870ed32618
ms.sourcegitcommit: c6a2d9a44a5a2c13abddab932d16c295a7207d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/09/2021
ms.locfileid: "107285466"
---
# <a name="azure-security-baseline-for-service-fabric"></a>Podstawa zabezpieczeń platformy Azure dla Service Fabric

Ta linia bazowa zabezpieczeń stosuje wskazówki dotyczące [usługi Azure Security test w wersji 1.0](../security/benchmarks/overview-v1.md) do Service Fabric. Test porównawczy zabezpieczeń platformy Azure zawiera zalecenia dotyczące sposobu zabezpieczania rozwiązań w chmurze na platformie Azure. Zawartość jest pogrupowana według **kontroli zabezpieczeń** zdefiniowanych przez program Azure Security test i powiązane wskazówki dotyczące Service Fabric.

> [!NOTE]
> Nie zostały wykluczone żadne **kontrolki** , które mają zastosowanie do Service Fabric lub dla których odpowiedzialnością jest firma Microsoft. Aby dowiedzieć się, jak Service Fabric całkowicie mapować do testu porównawczego zabezpieczeń platformy Azure, zobacz **[pełny Service Fabric pliku mapowania linii bazowej zabezpieczeń](https://github.com/MicrosoftDocs/SecurityBenchmarks/raw/master/Azure%20Offer%20Security%20Baselines/1.1/service-fabric-security-baseline-v1.1.xlsx)**.

## <a name="network-security"></a>Bezpieczeństwo sieci

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: bezpieczeństwo sieci](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: Ochrona zasobów platformy Azure w ramach sieci wirtualnych

**Wskazówki**: Upewnij się, że wszystkie wdrożenia podsieci Virtual Network mają grupę zabezpieczeń sieci stosowaną z kontrolami dostępu do sieci, które są specyficzne dla zaufanych portów i źródeł aplikacji. 

- [Wdrażanie usługi Azure Firewall przy użyciu szablonu](../firewall/deploy-template.md)

- [Tworzenie sieci obwodowych przy użyciu sieciowych grup zabezpieczeń (sieciowych grup zabezpieczeń) platformy Azure](https://docs.microsoft.com/azure/security/fundamentals/service-fabric-best-practices#use-network-isolation-and-security-with-azure-service-fabric)

- [Jak zintegrować klaster Service Fabric platformy Azure z istniejącą siecią wirtualną](service-fabric-patterns-networking.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1,2: Monitoruj i Rejestruj konfigurację oraz ruch sieci wirtualnych, podsieci i kart sieciowych

**Wskazówki**: Użyj Security Center i skoryguj zalecenia dotyczące ochrony sieci dla sieci wirtualnej, podsieci i sieciowej grupy zabezpieczeń używanej do zabezpieczania klastra Service Fabric platformy Azure. Włącz dzienniki przepływu sieciowych grup zabezpieczeń i Wyślij dzienniki do konta usługi Azure Storage do inspekcji ruchu. Możesz również wysyłać dzienniki przepływu grup zabezpieczeń sieci do obszaru roboczego usługi Azure Log Analytics i korzystać z usługi Azure Analiza ruchu w celu zapewnienia wglądu w przepływ ruchu w chmurze platformy Azure. Niektóre zalety platformy Azure Analiza ruchu to możliwość wizualizacji aktywności sieciowej i identyfikowania aktywnych punktów, identyfikowania zagrożeń bezpieczeństwa, zrozumienia wzorców przepływu ruchu oraz wyznaczania konfiguracji sieci.

- [Jak włączyć dzienniki przepływu sieciowej grupy zabezpieczeń](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Jak włączyć usługę Azure Analiza ruchu i korzystać z niej](../network-watcher/traffic-analytics.md)

- [Informacje o zabezpieczeniach sieci zapewnianych przez Azure Security Center](../security-center/security-center-network-recommendations.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="13-protect-critical-web-applications"></a>1,3: Ochrona krytycznych aplikacji sieci Web

**Wskazówki**: zapewnianie bramy frontonu w celu zapewnienia pojedynczego punktu ruchu przychodzącego dla użytkowników, urządzeń lub innych aplikacji. Usługa Azure API Management integruje się bezpośrednio z Service Fabric, co pozwala na Zabezpieczanie dostępu do usług zaplecza, zapobieganie atakom na system DOS przy użyciu ograniczania i weryfikowanie kluczy interfejsu API, tokenów JWT, certyfikatów i innych poświadczeń.

Rozważ wdrożenie zapory aplikacji sieci Web platformy Azure (WAF) przed krytycznymi aplikacjami sieci Web, aby uzyskać dodatkową inspekcję ruchu przychodzącego. Włącz ustawienie diagnostyczne dla WAF i pobierania dzienników do konta magazynu, centrum zdarzeń lub Log Analytics obszaru roboczego.

- [Usługi Service Fabric i Azure API Management — omówienie](service-fabric-api-management-overview.md)

- [Integrowanie usługi API Management w wewnętrznej sieci wirtualnej z usługą Application Gateway](../api-management/api-management-howto-integrate-internal-vnet-appgateway.md)

- [Jak wdrożyć usługę Azure WAF](../web-application-firewall/ag/create-waf-policy-ag.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: odmowa komunikacji ze znanymi złośliwymi adresami IP

**Wskazówki**: dla ochrony przed atakami DDoS Włącz ochronę standardową Azure DDoS w sieci wirtualnej, w której wdrożono klaster Service Fabric platformy Azure. Użyj Azure Security Center zintegrowanej analizy zagrożeń, aby odmówić komunikacji ze znanymi złośliwymi lub nieużywanymi adresami IP.

- [Jak skonfigurować ochronę DDoS](../ddos-protection/manage-ddos-protection.md)

- [Opis Azure Security Center zintegrowanej analizy zagrożeń](../security-center/azure-defender.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="15-record-network-packets"></a>1,5: rejestrowanie pakietów sieciowych

**Wskazówki**: Włączanie dzienników przepływu sieciowych grup zabezpieczeń dla sieciowych grup zabezpieczeń podłączonych do podsieci używanej do ochrony klastra Service Fabric. Zapisz dzienniki przepływu sieciowej grupy zabezpieczeń na koncie usługi Azure Storage w celu wygenerowania rekordów przepływu. Jeśli jest to wymagane do badania nietypowego działania, Włącz funkcję przechwytywania pakietów Network Watcher platformy Azure.

- [Jak włączyć dzienniki przepływu sieciowej grupy zabezpieczeń](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Jak włączyć Network Watcher](../network-watcher/network-watcher-create.md)

- [Wizualizowanie dzienników przepływów sieciowej grupy zabezpieczeń za pomocą analizy ruchu](../network-watcher/traffic-analytics.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: wdrażanie opartych na sieci systemów zapobiegania wykrywaniem i dostępem intruzów (identyfikatorów/adresów IP)

**Wskazówki**: wybierz ofertę z portalu Azure Marketplace, która obsługuje funkcje identyfikatorów/adresów IP z możliwościami inspekcji ładunku.  Jeśli wykrywanie i/lub zapobieganie włamaniu nie są wymagane w oparciu o inspekcję ładunku, można użyć zapory platformy Azure z funkcją analizy zagrożeń. Filtrowanie oparte na analizie zagrożeń na platformie Azure może wysyłać alerty i odrzucać ruch do i ze znanych złośliwych adresów IP i domen. Adresy IP i domeny pochodzą z kanału informacyjnego analizy zagrożeń firmy Microsoft. 

Wdrażaj wybrane rozwiązanie zapory w każdej z granic sieci organizacji, aby wykrywać i/lub odrzucać złośliwy ruch. 

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall) 

- [Jak wdrożyć zaporę platformy Azure](../firewall/tutorial-firewall-deploy-portal.md) 

- [Jak skonfigurować alerty za pomocą zapory platformy Azure](../firewall/threat-intel.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="17-manage-traffic-to-web-applications"></a>1,7: zarządzanie ruchem do aplikacji sieci Web

**Wskazówki**: wdrażanie Application Gateway platformy Azure dla aplikacji sieci Web z WŁĄCZONYm protokołem HTTPS/SSL dla zaufanych certyfikatów. 

- [Jak wdrożyć Application Gateway](../application-gateway/quick-create-portal.md)

- [Jak skonfigurować Application Gateway do korzystania z protokołu HTTPS](../application-gateway/create-ssl-portal.md)

- [Omówienie równoważenia obciążenia warstwy 7 za pomocą bram aplikacji sieci Web platformy Azure](../application-gateway/overview.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: Minimalizacja złożoności i kosztów administracyjnych reguł zabezpieczeń sieci

**Wskazówki**: Użyj tagów usługi sieci wirtualnej, aby zdefiniować kontrolę dostępu do sieci w grupach zabezpieczeń sieci (sieciowej grupy zabezpieczeń) dołączonych do podsieci, w której wdrożono klaster Service Fabric platformy Azure. Podczas tworzenia reguł zabezpieczeń można użyć tagów usługi zamiast konkretnych adresów IP. Określając nazwę tagu usługi (np. ApiManagement) w odpowiednim polu źródłowym lub docelowym reguły, można zezwolić na ruch dla odpowiedniej usługi lub go odrzucić. Firma Microsoft zarządza prefiksami adresów, które obejmują tag usługi, i automatycznie aktualizuje tag usługi jako adresy.

- [Tagi usługi sieci wirtualnej](../virtual-network/service-tags-overview.md)

- [Najlepsze rozwiązania dotyczące sieci Service Fabric](service-fabric-best-practices-networking.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: Obsługa standardowych konfiguracji zabezpieczeń dla urządzeń sieciowych

**Wskazówki**: Definiowanie i implementowanie standardowych konfiguracji zabezpieczeń dla zasobów sieciowych związanych z klastrem Service Fabric platformy Azure. Użyj aliasów Azure Policy w przestrzeniach nazw "Microsoft. servicefabric" i "Microsoft. Network", aby utworzyć zasady niestandardowe do inspekcji lub wymuszania konfiguracji sieci klastra usługi Azure Service Fabric.

Możesz również użyć planów platformy Azure, aby uprościć wdrożenia platformy Azure na dużą skalę przez pakowanie kluczowych artefaktów środowiska, takich jak szablony Azure Resource Manager, kontrolki kontroli RBAC platformy Azure i zasady, w ramach jednej definicji planu. Łatwo stosuj plan do nowych subskrypcji i środowisk, a następnie dostosuj kontrolę i zarządzanie przy użyciu wersji.

- [Jak wyświetlić dostępne aliasy Azure Policy](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-4.8.0&amp;preserve-view=true)

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

- [Jak utworzyć Azure Blueprint](../governance/blueprints/create-blueprint-portal.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="110-document-traffic-configuration-rules"></a>1,10: udokumentowanie reguł konfiguracji ruchu

**Wskazówki**: używanie tagów dla sieciowych grup zabezpieczeń i innych zasobów związanych z zabezpieczeniami sieci i przepływem ruchu, które są skojarzone z klastrem Service Fabric. Dla poszczególnych reguł grup zabezpieczeń sieci Użyj pola "opis", aby określić potrzebę biznesową, czas trwania i tak dalej, dla wszystkich reguł zezwalających na ruch do/z sieci.

Użyj dowolnych wbudowanych definicji Azure Policy związanych ze znakiem, takich jak "Wymagaj tagu i jego wartości", aby upewnić się, że wszystkie zasoby są tworzone przy użyciu tagów i powiadomienia o istniejących nieoznakowanych zasobach.

Możesz użyć Azure PowerShell lub interfejsu wiersza polecenia platformy Azure (CLI), aby wyszukiwać lub wykonywać akcje na zasobach na podstawie ich tagów.

- [Tworzenie i używanie tagów](../azure-resource-manager/management/tag-resources.md)

- [Jak utworzyć sieć wirtualną](../virtual-network/quick-create-portal.md)

- [Jak utworzyć sieciowej grupy zabezpieczeń z konfiguracją zabezpieczeń](../virtual-network/tutorial-filter-network-traffic.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: Użyj zautomatyzowanych narzędzi do monitorowania konfiguracji zasobów sieciowych i wykrywania zmian

**Wskazówki**: Użyj dziennika aktywności platformy Azure do monitorowania konfiguracji zasobów sieciowych i wykrywania zmian zasobów sieciowych związanych z wdrożeniami Service Fabric platformy Azure. Tworzenie alertów w ramach Azure Monitor, które będą wyzwalane po wprowadzeniu zmian w krytycznych zasobach sieciowych.

- [Jak wyświetlać i pobierać zdarzenia dziennika aktywności platformy Azure](/azure/azure-monitor/platform/activity-log#view-the-activity-log)

- [Jak utworzyć alerty w Azure Monitor](/azure/azure-monitor/platform/alerts-activity-log)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

## <a name="logging-and-monitoring"></a>Rejestrowanie i monitorowanie

*Aby uzyskać więcej informacji, zobacz temat [Azure Security test: rejestrowanie i monitorowanie](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2,2: Skonfiguruj centralne zarządzanie dziennikami zabezpieczeń

**Wskazówki**: możesz dołączyć klaster Service Fabric platformy Azure, aby Azure monitor do agregowania danych zabezpieczeń wygenerowanych przez klaster. Zobacz przykład problemów diagnostycznych i rozwiązań Service Fabric.

- [Konfigurowanie integracji dzienników Azure Monitor przy użyciu Service Fabric](service-fabric-diagnostics-oms-setup.md)

- [Konfigurowanie dzienników Azure Monitor na potrzeby monitorowania kontenerów na platformie Azure Service Fabric](service-fabric-tutorial-monitoring-wincontainers.md)

- [Diagnozowanie typowych scenariuszy Service Fabric](service-fabric-diagnostics-common-scenarios.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: Włączanie rejestrowania inspekcji dla zasobów platformy Azure

**Wskazówki**: Włączanie Azure monitor dla klastra Service Fabric, kierowanie go do obszaru roboczego log Analytics. Spowoduje to zarejestrowanie odpowiednich informacji o klastrze i metryk systemu operacyjnego dla wszystkich węzłów klastra usługi Azure Service Fabric.

- [Konfigurowanie integracji dzienników Azure Monitor przy użyciu Service Fabric](service-fabric-diagnostics-oms-setup.md)

- [Konfigurowanie dzienników Azure Monitor na potrzeby monitorowania kontenerów na platformie Azure Service Fabric](service-fabric-tutorial-monitoring-wincontainers.md)

- [Jak wdrożyć agenta Log Analytics w węzłach](service-fabric-diagnostics-oms-agent.md)

- [Przeszukiwanie dzienników Log Analytics](/azure/azure-monitor/log-query/log-query-overview)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: Zbierz dzienniki zabezpieczeń z systemów operacyjnych

**Wskazówki**: dołączanie klastra Service Fabric platformy Azure do Azure monitor. Upewnij się, że używany obszar roboczy Log Analytics ma ustawiony okres przechowywania dziennika zgodnie z regulacjami zgodności w organizacji.

- [Konfigurowanie integracji dzienników Azure Monitor przy użyciu Service Fabric](service-fabric-diagnostics-oms-setup.md)

- [Konfigurowanie dzienników Azure Monitor na potrzeby monitorowania kontenerów na platformie Azure Service Fabric](service-fabric-tutorial-monitoring-wincontainers.md)

- [Jak wdrożyć agenta Log Analytics w węzłach](service-fabric-diagnostics-oms-agent.md) 

- [Jak skonfigurować okres przechowywania obszaru roboczego Log Analytics](/azure/azure-monitor/platform/manage-cost-storage)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="25-configure-security-log-storage-retention"></a>2,5: Konfigurowanie przechowywania magazynu dzienników zabezpieczeń

**Wskazówki**: dołączanie klastra Service Fabric do Azure monitor. Upewnij się, że używany obszar roboczy Log Analytics ma ustawiony okres przechowywania dziennika zgodnie z regulacjami zgodności w organizacji.

- [Konfigurowanie integracji dzienników Azure Monitor przy użyciu Service Fabric](service-fabric-diagnostics-oms-setup.md)

- [Konfigurowanie dzienników Azure Monitor na potrzeby monitorowania kontenerów na platformie Azure Service Fabric](service-fabric-tutorial-monitoring-wincontainers.md)

- [Jak wdrożyć agenta Log Analytics w węzłach](service-fabric-diagnostics-oms-agent.md)

- [Jak skonfigurować okres przechowywania obszaru roboczego Log Analytics](/azure/azure-monitor/platform/manage-cost-storage)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="26-monitor-and-review-logs"></a>2,6: dzienniki monitorowania i przeglądania

**Wskazówki**: Użyj zapytań obszaru roboczego usługi Azure log Analytics, aby wykonywać zapytania dotyczące dzienników Service Fabric platformy Azure.

- [Przeszukiwanie dzienników Log Analytics](/azure/azure-monitor/log-query/log-query-overview)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: Włączanie alertów dla nietypowych działań

**Wskazówki**: używanie obszaru roboczego usługi Azure log Analytics do monitorowania i generowania alertów dotyczących nietypowych działań w dziennikach zabezpieczeń i zdarzeniach związanych z klastrem Service Fabric platformy Azure.

- [Jak zarządzać alertami w Azure Security Center](../security-center/security-center-managing-and-responding-alerts.md)

- [Jak ostrzec dane dziennika usługi log Analytics](/azure/azure-monitor/learn/tutorial-response)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="28-centralize-anti-malware-logging"></a>2,8: scentralizowanie rejestrowania chroniącego przed złośliwym oprogramowaniem

**Wskazówki**: Domyślnie usługa Windows Defender jest zainstalowana w systemie windows Server 2016. Jeśli nie korzystasz z usługi Windows Defender, zapoznaj się z dokumentacją dotyczącą programu antymęskiego dotyczącą reguł konfiguracji. Usługa Windows Defender nie jest obsługiwana w systemie Linux.

- [Aby uzyskać szczegółowe informacje, zobacz program antywirusowy Windows Defender w systemie Windows Server 2016](/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-on-windows-server-2016)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="29-enable-dns-query-logging"></a>2,9: Włączanie rejestrowania zapytań DNS

**Wskazówki**: Implementowanie rozwiązania innej firmy na potrzeby rejestrowania w systemie DNS.

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="210-enable-command-line-audit-logging"></a>2,10: Włączanie rejestrowania inspekcji w wierszu polecenia

**Wskazówki**: ręczne Konfigurowanie rejestrowania konsoli na poszczególnych węzłach.

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

## <a name="identity-and-access-control"></a>Tożsamość i kontrola dostępu

*Aby uzyskać więcej informacji, zobacz informacje o [teście zabezpieczeń Azure: Identity i Access Control](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: obsługa spisu kont administracyjnych

**Wskazówki**: zachowywanie rekordu lokalnego konta administracyjnego, które jest tworzone podczas aprowizacji klastra na platformie Azure Service Fabric klastra, a także innych utworzonych kont. Ponadto jeśli jest używana integracja z usługą Azure Active Directory (Azure AD), usługa Azure AD ma wbudowane role, które muszą być jawnie przypisane i dlatego queryable. Moduł Azure AD PowerShell służy do wykonywania zapytań ad hoc w celu odnajdywania kont należących do grup administracyjnych.

Ponadto możesz użyć zaleceń dotyczących zarządzania tożsamościami i dostępem Azure Security Center.

- [Jak uzyskać rolę katalogu w usłudze Azure AD przy użyciu programu PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0&amp;preserve-view=true)

- [Jak uzyskać członków roli katalogu w usłudze Azure AD przy użyciu programu PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0&amp;preserve-view=true)

- [Jak monitorować tożsamość i dostęp przy użyciu Azure Security Center](../security-center/security-center-identity-access.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="32-change-default-passwords-where-applicable"></a>3,2: Zmień domyślne hasła, jeśli ma to zastosowanie

**Wskazówki**: podczas aprowizacji klastra platforma Azure wymaga utworzenia nowych haseł dla portalu internetowego. Nie ma domyślnych haseł do zmiany, można jednak określić różne hasła dla dostępu do portalu internetowego.

- [Tworzenie w witrynie Azure Portal](service-fabric-cluster-creation-via-portal.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: Użyj dedykowanych kont administracyjnych

**Wskazówki**: integracja uwierzytelniania dla Service Fabric z usługą Azure Active Directory (Azure AD). Utwórz zasady i procedury dotyczące korzystania z dedykowanych kont administracyjnych.

Ponadto możesz użyć zaleceń dotyczących zarządzania tożsamościami i dostępem Security Center.

- [Konfigurowanie uwierzytelniania klienta usługi Azure AD](https://docs.microsoft.com/azure/service-fabric/service-fabric-tutorial-create-vnet-and-windows-cluster#set-up-azure-active-directory-client-authentication)

- [Jak monitorować tożsamość i dostęp przy użyciu Azure Security Center](../security-center/security-center-identity-access.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3,4: Korzystaj z logowania jednokrotnego (SSO) z usługą Azure Active Directory

**Wskazówki**: wszędzie tam, gdzie to możliwe, użyj Azure Active Directory (Azure AD) SSO zamiast konfigurować indywidualne poświadczenia autonomiczne dla poszczególnych usług. Użyj Security Center zalecenia dotyczące zarządzania tożsamościami i dostępem. 

- [Opis logowania jednokrotnego w usłudze Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Użyj uwierzytelniania wieloskładnikowego, aby uzyskać dostęp oparty na Azure Active Directory

**Wskazówki**: Włączanie uwierzytelniania wieloskładnikowego Azure Active Directory (Azure AD) i przestrzeganie Security Center zalecenia dotyczące zarządzania tożsamościami i dostępem.

- [Jak włączyć uwierzytelnianie wieloskładnikowe na platformie Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Jak monitorować tożsamość i dostęp w Azure Security Center](../security-center/security-center-identity-access.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: Używaj dedykowanych maszyn (uprzywilejowany dostęp do stacji roboczych) dla wszystkich zadań administracyjnych

**Wskazówki**: Użyj stacji roboczej dostępu uprzywilejowanego (dostępem uprzywilejowanym) z uwierzytelnianiem wieloskładnikowym skonfigurowanym do logowania się i konfigurowania klastrów Service Fabric i powiązanych zasobów.

- [Dowiedz się więcej o stacjach roboczych uprzywilejowanego dostępu](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Jak włączyć uwierzytelnianie wieloskładnikowe na platformie Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: Rejestruj i Ostrzegaj o podejrzanych działaniach z kont administracyjnych

**Wskazówki**: Użyj Azure Active Directory (Azure AD) PRIVILEGED Identity Management (PIM) do generowania dzienników i alertów w przypadku wystąpienia podejrzanych lub niebezpiecznych działań w środowisku. Ponadto za pomocą funkcji wykrywania ryzyka usługi Azure AD można wyświetlać alerty i raporty na temat ryzykownego zachowania użytkowników.

- [Jak wdrożyć Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [Omówienie wykrywania ryzyka usługi Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: zarządzanie zasobami platformy Azure tylko z zatwierdzonych lokalizacji

**Wskazówki**: Użyj dostępu warunkowego o nazwie Locations, aby zezwolić na dostęp tylko do określonych logicznych grup zakresów adresów IP lub krajów/regionów. 

- [Jak skonfigurować nazwane lokalizacje na platformie Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="39-use-azure-active-directory"></a>3,9: Użyj Azure Active Directory

**Wskazówki**: Użyj Azure Active Directory (Azure AD) jako centralnego systemu uwierzytelniania i autoryzacji, aby zabezpieczyć dostęp do punktów końcowych zarządzania klastrami Service Fabric. Usługa Azure AD chroni dane przy użyciu silnego szyfrowania danych przechowywanych i przesyłanych. Usługa Azure AD również Sole, skróty i bezpieczne przechowywanie poświadczeń użytkownika.

- [Jak utworzyć i skonfigurować wystąpienie usługi Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

- [Konfigurowanie usługi Azure AD na potrzeby uwierzytelniania klienta Service Fabric](service-fabric-cluster-creation-setup-aad.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania**: [wzorzec zabezpieczeń platformy Azure](/azure/governance/policy/samples/azure-security-benchmark) jest domyślną inicjatywy zasad dla Security Center i jest podstawą dla [zaleceń Security Center](/azure/security-center/security-center-recommendations). Definicje Azure Policy powiązane z tym formantem są włączane automatycznie przez Security Center. Alerty związane z tym formantem mogą wymagać planu [usługi Azure Defender](/azure/security-center/azure-defender) dla powiązanych usług.

**Azure Policy wbudowane definicje — Microsoft. servicefabric**:

[!INCLUDE [Resource Policy for Microsoft.ServiceFabric 3.9](../../includes/policy/standards/asb/rp-controls/microsoft.servicefabric-3-9.md)]

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: regularnie Przeglądaj i Uzgodnij dostęp użytkowników

**Wskazówki**: użyj uwierzytelniania Azure Active Directory (Azure AD) z klastrem Service Fabric. Usługa Azure AD udostępnia dzienniki, które ułatwiają odnajdywanie starych kont. Ponadto za pomocą przeglądów dostępu do tożsamości platformy Azure można efektywnie zarządzać członkostwem w grupach, dostępem do aplikacji dla przedsiębiorstw i przypisaniami ról. Dostęp użytkownika może być regularnie przeglądany, aby upewnić się, że tylko Ci użytkownicy mają stały dostęp.

- [Jak korzystać z przeglądów dostępu do tożsamości platformy Azure](../active-directory/governance/access-reviews-overview.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="311-alert-on-account-login-behavior-deviation"></a>3,11: odchylenia zachowania podczas logowania do konta

**Wskazówki**: użyj programu Azure Active Directory (Azure AD) logowanie i dzienniki inspekcji, aby monitorować próby dostępu do zdezaktywowanych kont; te dzienniki można zintegrować z dowolnym narzędziem SIEM/monitorowania innych firm.

Aby usprawnić ten proces, można utworzyć ustawienia diagnostyczne dla kont użytkowników usługi Azure AD, wysłać dzienniki inspekcji i dzienniki logowania do obszaru roboczego usługi Azure Log Analytics. Skonfiguruj żądane alerty w obszarze roboczym usługi Azure Log Analytics.

- [Jak zintegrować dzienniki aktywności platformy Azure z usługą Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: odchylenia zachowania alertu dotyczącego logowania na koncie

**Wskazówki**: Użyj funkcji ryzyka i ochrony tożsamości Azure Active Directory (Azure AD), aby skonfigurować automatyczne odpowiedzi na wykryte podejrzane działania związane z tożsamościami użytkowników. Możesz również pozyskać dane do usługi Azure wskaźnikowej na potrzeby dalszej analizy.

- [Jak wyświetlić ryzykowne logowania w usłudze Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

- [Jak skonfigurować i włączyć zasady dotyczące ryzyka związanego z ochroną tożsamości](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Jak dołączyć wskaźnik na platformie Azure](../sentinel/quickstart-onboard.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

## <a name="data-protection"></a>Ochrona danych

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: ochrona danych](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: przechowywanie spisu poufnych informacji

**Wskazówki**: Użyj tagów dotyczących zasobów związanych z wdrożeniami klastra Service Fabric, aby pomóc w śledzeniu zasobów platformy Azure, które przechowują lub przetwarzają informacje poufne.

- [Tworzenie i używanie tagów](../azure-resource-manager/management/tag-resources.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: Izoluj systemy przechowujące lub przetwarzające informacje poufne

**Wskazówki**: implementowanie oddzielnych subskrypcji i/lub grup zarządzania na potrzeby tworzenia, testowania i produkcji. Zasoby powinny być oddzielone przez Virtual Network lub podsieć, odpowiednio otagowane i zabezpieczone przez sieciowe grupy zabezpieczeń lub zaporę platformy Azure. Zasoby przechowujące lub przetwarzające dane poufne powinny być wystarczająco odizolowane. Aby Virtual Machines przechowywać lub przetwarzać dane poufne, należy wdrożyć zasady i procedury, aby je wyłączyć, gdy nie są używane. 

- [Jak utworzyć dodatkowe subskrypcje platformy Azure](../cost-management-billing/manage/create-subscription.md)

- [Jak utworzyć Grupy zarządzania](../governance/management-groups/create-management-group-portal.md)

- [Tworzenie i używanie tagów](../azure-resource-manager/management/tag-resources.md)

- [Jak utworzyć Virtual Network](../virtual-network/quick-create-portal.md)

- [Jak utworzyć sieciowej grupy zabezpieczeń z konfiguracją zabezpieczeń](../virtual-network/tutorial-filter-network-traffic.md)

- [Jak wdrożyć zaporę platformy Azure](../firewall/tutorial-firewall-deploy-portal.md)

- [Jak skonfigurować alert lub alert i odmówić za pomocą zapory platformy Azure](../firewall/threat-intel.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: Monitoruj i blokuj nieautoryzowany transfer informacji poufnych

**Wskazówki**: wdrażanie zautomatyzowanego narzędzia na obrzeżach sieci, które monitoruje do nieautoryzowanego transferu poufnych informacji i blokuje takie transfery podczas powiadamiania specjalistów ds. bezpieczeństwa informacji. 

W przypadku podstawowej platformy zarządzanej przez firmę Microsoft Firma Microsoft traktuje całą zawartość klienta jako poufną i nadaje im dużą długość, aby chronić przed utratą i narażeniem danych przez klienta. Aby zapewnić bezpieczeństwo danych klienta na platformie Azure, firma Microsoft wdrożyła i utrzymuje pakiet niezawodnych kontroli i możliwości ochrony danych. 

- [Informacje na temat ochrony danych klientów na platformie Azure](../security/fundamentals/protection-customer-data.md)

**Odpowiedzialność**: Współużytkowane

**Monitorowanie Azure Security Center**: brak

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: Szyfruj wszystkie poufne informacje podczas przesyłania

**Wskazówki**: Szyfruj wszystkie informacje poufne podczas przesyłania. Upewnij się, że wszyscy klienci łączący się z zasobami platformy Azure mogą negocjować protokół TLS 1,2 lub nowszy. 

W przypadku Service Fabric uwierzytelniania wzajemnego między klientami Użyj certyfikatu X. 509 na potrzeby tożsamości serwera i szyfrowania TLS komunikacji protokołu HTTP. Dowolna liczba dodatkowych certyfikatów może być zainstalowana w klastrze na potrzeby zabezpieczeń aplikacji, w tym szyfrowania i odszyfrowywania wartości konfiguracji aplikacji oraz danych między węzłami podczas replikacji.
Postępuj zgodnie z zaleceniami Security Center na potrzeby szyfrowania w czasie spoczynku i szyfrowania podczas przesyłania, jeśli ma to zastosowanie. 

- [Informacje o szyfrowaniu podczas przesyłania na platformę Azure](https://docs.microsoft.com/azure/security/fundamentals/encryption-overview#encryption-of-data-in-transit)

- [Scenariusze zabezpieczeń klastra Service Fabric](service-fabric-cluster-security.md)

- [Service Fabric Przewodnik rozwiązywania problemów dotyczących konfiguracji protokołu TLS](https://github.com/Azure/Service-Fabric-Troubleshooting-Guides/blob/master/Security/TLS%20Configuration.md)

**Odpowiedzialność**: Współużytkowane

**Monitorowanie Azure Security Center**: brak

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: Użyj aktywnego narzędzia do odnajdywania, aby identyfikować poufne dane

**Wskazówki**: funkcje identyfikacji, klasyfikacji i zapobiegania utracie danych nie są jeszcze dostępne dla usługi Azure Storage i zasobów obliczeniowych. Zaimplementuj rozwiązanie innych firm, jeśli jest wymagane na potrzeby zgodności.

W przypadku podstawowej platformy zarządzanej przez firmę Microsoft Firma Microsoft traktuje całą zawartość klienta jako poufną i nadaje im dużą długość, aby chronić przed utratą i narażeniem danych przez klienta. Aby zapewnić bezpieczeństwo danych klienta na platformie Azure, firma Microsoft wdrożyła i utrzymuje pakiet niezawodnych kontroli i możliwości ochrony danych.

- [Informacje na temat ochrony danych klientów na platformie Azure](../security/fundamentals/protection-customer-data.md)

**Odpowiedzialność**: Współużytkowane

**Monitorowanie Azure Security Center**: brak

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: Wymuś kontrolę dostępu przy użyciu ochrony przed utratą danych opartą na hoście

**Wskazówki**: w przypadku klastrów Service Fabric przechowywanie lub przetwarzanie informacji poufnych, Oznacz klaster i powiązane zasoby jako poufne przy użyciu tagów. Funkcje identyfikacji, klasyfikacji i zapobiegania utracie danych nie są jeszcze dostępne dla usługi Azure Storage i zasobów obliczeniowych. Zaimplementuj rozwiązanie innych firm, jeśli jest wymagane na potrzeby zgodności.

W przypadku podstawowej platformy zarządzanej przez firmę Microsoft Firma Microsoft traktuje całą zawartość klienta jako poufną i nadaje im dużą długość, aby chronić przed utratą i narażeniem danych przez klienta. Aby zapewnić bezpieczeństwo danych klienta na platformie Azure, firma Microsoft wdrożyła i utrzymuje pakiet niezawodnych kontroli i możliwości ochrony danych.

- [Informacje na temat ochrony danych klientów na platformie Azure](../security/fundamentals/protection-customer-data.md)

**Odpowiedzialność**: Współużytkowane

**Monitorowanie Azure Security Center**: brak

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: Szyfruj poufne informacje w spoczynku

**Wskazówki**: szyfrowanie na wszystkich zasobach platformy Azure. Firma Microsoft zaleca, aby platforma Azure mogła zarządzać kluczami szyfrowania, jednak istnieje możliwość zarządzania własnymi kluczami w niektórych wystąpieniach. 

- [Informacje o szyfrowaniu danych magazynowanych na platformie Azure](../security/fundamentals/encryption-atrest.md)  

- [Jak skonfigurować zarządzane przez klienta klucze szyfrowania](../storage/common/customer-managed-keys-configure-key-vault.md)

- [Włączanie szyfrowania dysków dla węzłów klastra usługi Azure Service Fabric w systemie Windows](service-fabric-enable-azure-disk-encryption-windows.md)

- [Włączanie szyfrowania dysków dla węzłów klastra usługi Azure Service Fabric w systemie Linux](service-fabric-enable-azure-disk-encryption-linux.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania**: [wzorzec zabezpieczeń platformy Azure](/azure/governance/policy/samples/azure-security-benchmark) jest domyślną inicjatywy zasad dla Security Center i jest podstawą dla [zaleceń Security Center](/azure/security-center/security-center-recommendations). Definicje Azure Policy powiązane z tym formantem są włączane automatycznie przez Security Center. Alerty związane z tym formantem mogą wymagać planu [usługi Azure Defender](/azure/security-center/azure-defender) dla powiązanych usług.

**Azure Policy wbudowane definicje — Microsoft. servicefabric**:

[!INCLUDE [Resource Policy for Microsoft.ServiceFabric 4.8](../../includes/policy/standards/asb/rp-controls/microsoft.servicefabric-4-8.md)]

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: Rejestruj i Ostrzegaj o zmianach krytycznych zasobów platformy Azure

**Wskazówki**: Użyj Azure monitor z dziennikiem aktywności platformy Azure, aby utworzyć alerty dotyczące sytuacji, w których zmiany są wykonywane do krytycznych zasobów platformy Azure. 

- [Jak utworzyć alerty dla zdarzeń dziennika aktywności platformy Azure](/azure/azure-monitor/platform/alerts-activity-log)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

## <a name="vulnerability-management"></a>Zarządzanie lukami w zabezpieczeniach

*Aby uzyskać więcej informacji, zobacz temat [Azure Security test: Zarządzanie lukami w zabezpieczeniach](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: uruchamianie narzędzi do skanowania automatycznych luk w zabezpieczeniach

**Wskazówki**: regularnie uruchamiaj usługę Service Fabric Fault Analysis i usługi chaos, aby symulować błędy w całym klastrze w celu oceny niezawodności i niezawodności usług.

Postępuj zgodnie z zaleceniami Security Center na temat przeprowadzania ocen luk w zabezpieczeniach na maszynach wirtualnych platformy Azure i obrazach kontenerów. 

Użyj rozwiązania innych firm do przeprowadzania ocen luk w zabezpieczeniach na urządzeniach sieciowych i aplikacjach sieci Web. Podczas przeprowadzania skanowania zdalnego nie należy używać pojedynczego, bezterminowego konta administratora. Rozważ zaimplementowanie metody inicjowania obsługi JIT dla konta skanowania. Poświadczenia dla konta skanowania powinny być chronione, monitorowane i używane tylko w celu skanowania w poszukiwaniu luk w zabezpieczeniach. 

- [Wprowadzenie do usługi Service Fabric Fault Analysis](service-fabric-testability-overview.md)

- [Chaos kontrolowane w klastrach Service Fabric](service-fabric-controlled-chaos.md)

- [Jak zaimplementować zalecenia dotyczące oceny luk w zabezpieczeniach Azure Security Center](../security-center/deploy-vulnerability-assessment-vm.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: Wdróż automatyczne rozwiązanie do zarządzania poprawkami systemu operacyjnego

**Wskazówki**: Włącz automatyczne uaktualnianie obrazu systemu operacyjnego na zestawach skalowania maszyn wirtualnych w klastrze Service Fabric. 

Alternatywnie, aby przetestować poprawki systemu operacyjnego przed przejściem do środowiska produkcyjnego, użyj wyzwalacza ręcznego dla uaktualnień obrazu systemu operacyjnego zestawu skalowania. Należy pamiętać, że opcja ręcznego wyzwalacza nie zapewnia wbudowanego wycofywania. Monitoruj poprawki systemu operacyjnego przy użyciu Update Management z Azure Automation. 

- [Zarządzanie poprawkami dla węzłów klastra Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-best-practices-infrastructure-as-code#azure-virtual-machine-operating-system-automatic-upgrade-configuration)

- [Automatyczne uaktualnianie obrazów systemu operacyjnego przy użyciu zestawów skalowania maszyn wirtualnych platformy Azure](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md)

- [Jak zapewnić aktualność maszyn wirtualnych przy użyciu najnowszego modelu zestawu skalowania](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-upgrade-scale-set#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model)

- [Omówienie Update Management Azure Automation](/azure/automation/update-management/update-mgmt-overview)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5,3: Wdróż rozwiązanie zautomatyzowanego zarządzania poprawkami dla tytułów oprogramowania innych firm

**Wskazówki**: Włączanie automatycznych uaktualnień obrazów systemu operacyjnego na zestawach skalowania maszyn wirtualnych w klastrze Service Fabric platformy Azure. Poprawka Orchestration Application (POA) to alternatywne rozwiązanie, które jest przeznaczone dla klastrów Service Fabric hostowanych poza platformą Azure. POA można używać z klastrami platformy Azure z dodatkowymi kosztami hostingu.

- [Zarządzanie poprawkami dla węzłów klastra Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-best-practices-infrastructure-as-code#azure-virtual-machine-operating-system-automatic-upgrade-configuration)

- [Automatyczne uaktualnianie obrazów systemu operacyjnego przy użyciu zestawów skalowania maszyn wirtualnych platformy Azure](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md)

- [Jak skonfigurować harmonogram stosowania poprawek systemu operacyjnego dla klastrów Service Fabric](service-fabric-patch-orchestration-application.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: porównanie luk w zabezpieczeniach z tyłu do tyłu

**Wskazówki**: Eksportuj wyniki skanowania w regularnych odstępach czasu i Porównaj wyniki, aby sprawdzić, czy luki zostały skorygowane. W przypadku korzystania z zaleceń dotyczących zarządzania lukami zalecanymi przez Security Center można przestawiać w portalu wybranego rozwiązania, aby wyświetlić historyczne dane skanowania.

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: Użyj procesu oceny ryzyka, aby określić priorytety korygowania odkrytych luk w zabezpieczeniach

**Wskazówki**: Użyj typowego programu oceny ryzyka (np. typowego oceniania luk w zabezpieczeniach systemu) lub domyślnych ocen ryzyka udostępnianych przez narzędzie skanowania innej firmy.

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

## <a name="inventory-and-asset-management"></a>Zarządzanie magazynem i zasobami

*Aby uzyskać więcej informacji, zobacz temat [Azure Security test: Inventory and Asset Management](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: Użyj rozwiązania automatycznego odnajdywania zasobów

**Wskazówki**: Użyj grafu zasobów platformy Azure do wykonywania zapytań/odnajdywania wszystkich zasobów (takich jak obliczenia, magazyn, Sieć, porty i protokoły itp.) w ramach subskrypcji. Upewnij się, że masz odpowiednie uprawnienia (odczyt) w dzierżawie i Wylicz wszystkie subskrypcje platformy Azure oraz zasoby w ramach subskrypcji.

Mimo że klasyczne zasoby platformy Azure mogą zostać odnalezione za pośrednictwem grafu zasobów, zdecydowanie zaleca się tworzenie i używanie Azure Resource Manager zasobów do przodu.

- [Jak tworzyć zapytania przy użyciu grafu zasobów platformy Azure](../governance/resource-graph/first-query-portal.md)

- [Jak wyświetlić subskrypcje platformy Azure](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-4.8.0&amp;preserve-view=true)

- [Opis kontroli RBAC platformy Azure](../role-based-access-control/overview.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="62-maintain-asset-metadata"></a>6,2: Konserwowanie metadanych zasobów

**Wskazówki**: stosowanie tagów do zasobów platformy Azure, dzięki czemu metadane są logicznie zorganizowane w taksonomię.

- [Tworzenie i używanie tagów](../azure-resource-manager/management/tag-resources.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: Usuń nieautoryzowane zasoby platformy Azure

**Wskazówki**: używanie tagowania, grup zarządzania i oddzielnych subskrypcji, gdzie to konieczne, do organizowania i śledzenia zasobów. Regularnie Uzgadniaj spis i zapewnij, że nieautoryzowane zasoby są usuwane z subskrypcji w odpowiednim czasie.

- [Jak utworzyć dodatkowe subskrypcje platformy Azure](../cost-management-billing/manage/create-subscription.md)

- [Jak utworzyć Grupy zarządzania](../governance/management-groups/create-management-group-portal.md)

- [Tworzenie i używanie tagów](../azure-resource-manager/management/tag-resources.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6,4: Definiowanie i konserwowanie spisu zatwierdzonych zasobów platformy Azure

**Wskazówki**: Definiowanie zatwierdzonych zasobów platformy Azure i zatwierdzonego oprogramowania dla zasobów obliczeniowych.

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: Monitoruj niezatwierdzone zasoby platformy Azure

**Wskazówki**: Użyj Azure Policy, aby wprowadzić ograniczenia dotyczące typu zasobów, które można utworzyć w subskrypcjach klientów, przy użyciu następujących wbudowanych definicji zasad:

Niedozwolone typy zasobów

Dozwolone typy zasobów

Użyj grafu zasobów platformy Azure, aby wykonywać zapytania/odnajdywać zasoby w ramach subskrypcji. Upewnij się, że wszystkie zasoby platformy Azure obecne w środowisku są zatwierdzone.

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

- [Jak tworzyć zapytania za pomocą usługi Azure Graph](../governance/resource-graph/first-query-portal.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: Monitoruj niezatwierdzone aplikacje oprogramowania w ramach zasobów obliczeniowych

**Wskazówki**: Implementowanie rozwiązania innej firmy w celu monitorowania węzłów klastra dla niezatwierdzonych aplikacji.

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: Usuń niezatwierdzone zasoby platformy Azure i aplikacje oprogramowania

**Wskazówki**: Użyj grafu zasobów platformy Azure do wykonywania zapytań/odnajdywania wszystkich zasobów (takich jak obliczenia, magazyn, Sieć, porty i protokoły itp.), w tym Service Fabric klastrów w ramach subskrypcji. Usuń wszystkie niezatwierdzone zasoby platformy Azure, które zostały wykryte. W przypadku Service Fabric węzłów klastra Zaimplementuj rozwiązanie innej firmy w celu usunięcia lub alertu dotyczącego niezatwierdzonego oprogramowania.

- [Jak tworzyć zapytania za pomocą usługi Azure Graph](../governance/resource-graph/first-query-portal.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="68-use-only-approved-applications"></a>6,8: Używaj tylko zatwierdzonych aplikacji

**Wskazówki**: dla Service Fabric węzłów klastra Zaimplementuj rozwiązanie innych firm, aby zapobiec wykonywaniu nieautoryzowanego oprogramowania.

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="69-use-only-approved-azure-services"></a>6,9: Używaj tylko zatwierdzonych usług platformy Azure

**Wskazówki**: Użyj Azure Policy, aby ograniczyć usługi, które możesz udostępnić w danym środowisku.

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

- [Jak odmówić określonego typu zasobu za pomocą Azure Policy](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6,10: przechowywanie spisu zatwierdzonych tytułów oprogramowania

**Wskazówki**: dla węzłów klastra Service Fabric platformy Azure należy wdrożyć rozwiązanie innych firm, aby zapobiec wykonywaniu nieautoryzowanych typów plików.

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: Ogranicz możliwość korzystania przez użytkowników z Azure Resource Manager

**Wskazówki**: Użyj dostępu warunkowego platformy Azure, aby ograniczyć możliwość korzystania przez użytkowników z Menedżera zasobów platformy Azure przez skonfigurowanie "blokowania dostępu" dla aplikacji "Microsoft Azure Management". 

- [Jak skonfigurować dostęp warunkowy, aby blokować dostęp do usługi Azure Resources](../role-based-access-control/conditional-access-azure-management.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6,12: Ogranicz możliwość wykonywania skryptów w zasobach obliczeniowych przez użytkowników

**Wskazówki**: Użyj konfiguracji specyficznych dla systemu operacyjnego lub zasobów innych firm, aby ograniczyć możliwość wykonywania skryptów w ramach zasobów obliczeniowych platformy Azure.

- [Na przykład Sterowanie wykonywaniem skryptu programu PowerShell w środowiskach systemu Windows](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-7&amp;preserve-view=true)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: fizyczne lub logiczne rozdzielenie aplikacji wysokiego ryzyka

**Wskazówki**: oprogramowanie, które jest wymagane do działania biznesowego, ale może powodować większe ryzyko dla organizacji, powinno być izolowane w ramach własnej maszyny wirtualnej i/lub sieci wirtualnej i dostatecznie zabezpieczone za pomocą zapory platformy Azure lub sieciowej grupy zabezpieczeń. 

- [Jak utworzyć sieć wirtualną](../virtual-network/quick-create-portal.md) 

- [Jak utworzyć sieciowej grupy zabezpieczeń z konfiguracją zabezpieczeń](../virtual-network/tutorial-filter-network-traffic.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

## <a name="secure-configuration"></a>Bezpieczna konfiguracja

*Aby uzyskać więcej informacji, zobacz temat [Azure Security test: bezpieczna konfiguracja](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: Ustanów bezpieczne konfiguracje dla wszystkich zasobów platformy Azure

**Wskazówki**: Użyj aliasów Azure Policy w przestrzeni nazw "Microsoft. servicefabric", aby utworzyć zasady niestandardowe do inspekcji lub wymuszania konfiguracji sieci klastra Service Fabric.

- [Jak wyświetlić dostępne aliasy Azure Policy](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-4.8.0&amp;preserve-view=true)

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: Ustanów bezpieczne konfiguracje systemów operacyjnych

**Wskazówki**: Service Fabric obrazy systemu operacyjnego są zarządzane i obsługiwane przez firmę Microsoft. Klient odpowiedzialny za implementację bezpiecznych konfiguracji dla systemu operacyjnego węzłów klastra.

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: obsługa bezpiecznych konfiguracji zasobów platformy Azure

**Wskazówki**: Użyj Azure Policy [Odmów] i [Wdróż, jeśli nie istnieje] efektów, aby wymusić bezpieczne ustawienia dla klastrów Service Fabric platformy Azure i powiązanych zasobów.

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

- [Zrozumienie efektów Azure Policy](../governance/policy/concepts/effects.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: Zachowaj konfiguracje bezpiecznego systemu operacyjnego

**Wskazówki**: Service Fabric obrazów systemu operacyjnego klastra zarządzanych i obsługiwanych przez firmę Microsoft. Klient jest odpowiedzialny za implementację konfiguracji stanu na poziomie systemu operacyjnego.

**Odpowiedzialność**: Współużytkowane

**Monitorowanie Azure Security Center**: brak

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: bezpiecznie przechowuj konfigurację zasobów platformy Azure

**Wskazówki**: Jeśli używasz niestandardowych definicji Azure Policy, Użyj usługi Azure DevOps lub Azure Repos, aby bezpiecznie przechowywać kod i zarządzać nim.

- [Jak przechowywać kod w usłudze Azure DevOps](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops&amp;preserve-view=true)

- [Dokumentacja Azure Repos](https://docs.microsoft.com/azure/devops/repos/?view=azure-devops&amp;preserve-view=true)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: bezpieczne przechowywanie niestandardowych obrazów systemu operacyjnego

**Wskazówki**: Jeśli używasz obrazów niestandardowych, użyj kontroli dostępu opartej na rolach (Azure RBAC) na platformie Azure, aby zapewnić dostęp do obrazów tylko autoryzowanym użytkownikom. W przypadku obrazów kontenerów Zapisz je w Azure Container Registry i Skorzystaj z usługi Azure RBAC, aby zapewnić dostęp do obrazów tylko autoryzowanym użytkownikom. 

- [Opis kontroli RBAC platformy Azure](../role-based-access-control/rbac-and-directory-admin-roles.md) 

- [Informacje na temat usługi Azure RBAC dla Container Registry](../container-registry/container-registry-roles.md) 

- [Jak skonfigurować usługę Azure RBAC](../role-based-access-control/quickstart-assign-role-user-portal.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: Wdrażanie narzędzi do zarządzania konfiguracją dla zasobów platformy Azure

**Wskazówki**: Użyj aliasów Azure Policy w przestrzeni nazw "Microsoft. servicefabric", aby utworzyć zasady niestandardowe na potrzeby alertów, inspekcji i wymuszania konfiguracji systemu. Dodatkowo opracowuj proces i potok na potrzeby zarządzania wyjątkami zasad.

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: Zaimplementuj automatyczne monitorowanie konfiguracji dla zasobów platformy Azure

**Wskazówki**: Użyj aliasów Azure Policy w przestrzeni nazw "Microsoft. servicefabric", aby utworzyć zasady niestandardowe do inspekcji lub wymuszania konfiguracji klastra Service Fabric.

- [Jak wyświetlić dostępne aliasy Azure Policy](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-4.8.0&amp;preserve-view=true)

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: Zaimplementuj automatyczne monitorowanie konfiguracji dla systemów operacyjnych

**Wskazówki**: Użyj Security Center, aby przeprowadzić skanowanie linii bazowej dla ustawień systemu operacyjnego i platformy Docker dla kontenerów. 

- [Omówienie rekomendacji dotyczących kontenera usługi Azure Security Center](../security-center/container-security.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="711-manage-azure-secrets-securely"></a>7,11: bezpieczne zarządzanie wpisami tajnymi platformy Azure

**Wskazówki**: Użyj tożsamość usługi zarządzanej w połączeniu z Azure Key Vault, aby uprościć i zabezpieczyć poufne Zarządzanie aplikacjami w chmurze. 

- [Korzystanie z tożsamości zarządzanych dla platformy Azure z Service Fabric](concepts-managed-identity.md)

- [Konfigurowanie obsługi tożsamości zarządzanej dla nowego klastra Service Fabric](configure-new-azure-service-fabric-enable-managed-identity.md)

- [Używanie tożsamości zarządzanej z aplikacją Service Fabric](how-to-managed-identity-service-fabric-app-code.md)

- [Obsługa KeyVaultReference dla aplikacji Service Fabric](service-fabric-keyvault-references.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: bezpieczne i automatyczne zarządzanie tożsamościami

**Wskazówki**: tożsamości zarządzane mogą być używane w przypadku klastrów Service Fabric wdrożonych na platformie Azure oraz dla aplikacji wdrożonych jako zasoby platformy Azure. Tożsamości zarządzane umożliwiają uwierzytelnianie do dowolnej usługi, która obsługuje uwierzytelniania Azure Active Directory (Azure AD), w tym Key Vault, bez żadnych poświadczeń w kodzie.

- [Korzystanie z tożsamości zarządzanych dla platformy Azure z Service Fabric](concepts-managed-identity.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: eliminowanie nieprzewidzianego narażenia na poświadczenia

**Wskazówki**: Jeśli używasz dowolnego kodu związanego z wdrożeniem Service Fabric platformy Azure, możesz zaimplementować skaner poświadczeń, aby identyfikować poświadczenia w kodzie. Skaner poświadczeń ułatwia również przenoszenie odnalezionych poświadczeń do bezpieczniejszych lokalizacji, takich jak usługa Azure Key Vault.

Użyj Azure Key Vault, aby automatycznie obrócić Service Fabric certyfikatów klastra.

- [Jak skonfigurować skaner poświadczeń](https://secdevtools.azurewebsites.net/helpcredscan.html)

- [Zarządzanie certyfikatami w klastrach Service Fabric](https://docs.microsoft.com/azure/service-fabric/cluster-security-certificate-management#certificate-rotation)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

## <a name="malware-defense"></a>Ochrona przed złośliwym oprogramowaniem

*Aby uzyskać więcej informacji, zobacz temat [Azure Security test: Obrona złośliwego oprogramowania](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8,1: Użyj centralnie zarządzanego oprogramowania chroniącego przed złośliwym oprogramowaniem

**Wskazówki**: Domyślnie program antywirusowy Windows Defender jest instalowany w systemie windows Server 2016. Interfejs użytkownika jest domyślnie instalowany w przypadku niektórych jednostek SKU, ale nie jest to wymagane.

Jeśli nie korzystasz z usługi Windows Defender, zapoznaj się z dokumentacją dotyczącą ochrony przed złośliwym kodem. Usługa Windows Defender nie jest obsługiwana w systemie Linux.

- [Opis programu antywirusowego Windows Defender w systemie Windows Server 2016](/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-on-windows-server-2016)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

## <a name="data-recovery"></a>Odzyskiwanie danych

*Aby uzyskać więcej informacji, zobacz [test dotyczący zabezpieczeń platformy Azure: odzyskiwanie danych](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: Zapewnij regularne zautomatyzowane przywracanie awaryjne

**Wskazówki**: usługa tworzenia kopii zapasowych i przywracania w programie Service Fabric umożliwia łatwe i automatyczne tworzenie kopii zapasowych informacji przechowywanych w usługach stanowych. Okresowe tworzenie kopii zapasowych danych aplikacji ma podstawowe znaczenie dla ochrony przed utratą danych i niedostępnością usługi. Service Fabric udostępnia opcjonalną usługę tworzenia kopii zapasowych i przywracania, która umożliwia skonfigurowanie okresowej kopii zapasowej Reliable Services (w tym usług aktora) bez konieczności pisania dodatkowego kodu. Ułatwia również przywracanie wykonanych wcześniej kopii zapasowych.

- [Okresowe tworzenie kopii zapasowych i przywracanie w klastrze Service Fabric platformy Azure](service-fabric-backuprestoreservice-quickstart-azurecluster.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: wykonaj kompletne kopie zapasowe systemu i Utwórz kopię zapasową wszystkich kluczy zarządzanych przez klienta

**Wskazówki**: Włączanie usługi przywracania kopii zapasowych w klastrze Service Fabric i tworzenie zasad kopii zapasowych w celu okresowego wykonywania kopii zapasowych usług stanowych i na żądanie. Wykonaj kopię zapasową kluczy zarządzanych przez klienta w ramach Azure Key Vault.

- [Okresowe tworzenie kopii zapasowych i przywracanie w klastrze Service Fabric platformy Azure](service-fabric-backuprestoreservice-quickstart-azurecluster.md)

- [Informacje o konfiguracji okresowej kopii zapasowej na platformie Azure Service Fabric](service-fabric-backuprestoreservice-configure-periodic-backup.md)

- [Jak utworzyć kopię zapasową kluczy magazynu kluczy na platformie Azure](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey?view=azps-4.8.0&amp;preserve-view=true)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: Weryfikuj wszystkie kopie zapasowe, w tym klucze zarządzane przez klienta

**Wskazówki**: Upewnij się, że można przeprowadzić przywracanie z usługi przywracania kopii zapasowych przez okresowe przeglądanie informacji o konfiguracji kopii zapasowych i dostępnych kopii zapasowych. Testowanie przywracania kopii zapasowych kluczy zarządzanych przez klienta.

- [Informacje o konfiguracji okresowej kopii zapasowej na platformie Azure Service Fabric](service-fabric-backuprestoreservice-configure-periodic-backup.md)

- [Przywracanie kopii zapasowej na platformie Azure Service Fabric](service-fabric-backup-restore-service-trigger-restore.md)

- [Jak przywrócić klucze magazynu kluczy na platformie Azure](https://docs.microsoft.com/powershell/module/az.keyvault/restore-azkeyvaultkey?view=azps-4.8.0&amp;preserve-view=true)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: zapewnianie ochrony kopii zapasowych i kluczy zarządzanych przez klienta

**Wskazówki**: Tworzenie kopii zapasowych z Service Fabric usługi przywracania kopii zapasowych przy użyciu konta usługi Azure Storage w ramach subskrypcji. Usługa Azure Storage szyfruje wszystkie dane na koncie magazynu w stanie spoczynku. Domyślnie dane są szyfrowane przy użyciu kluczy zarządzanych przez firmę Microsoft. Aby uzyskać dodatkową kontrolę nad kluczami szyfrowania, można podać klucze zarządzane przez klienta do szyfrowania danych magazynu.

Jeśli używasz kluczy zarządzanych przez klienta, upewnij się, Soft-Delete w Key Vault jest włączona ochrona kluczy przed przypadkowym lub złośliwym usunięciem.

- [Szyfrowanie danych magazynowanych w usłudze Azure Storage](../storage/common/storage-service-encryption.md)

- [Jak włączyć Soft-Delete w Key Vault](../storage/blobs/soft-delete-blob-overview.md)

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

**Wskazówki**: Security Center przypisuje ważność do każdego alertu, aby pomóc w ustaleniu, które alerty należy najpierw zbadać. Ważność jest oparta na tym, jak dobrze Security Center znajduje się w wyszukiwaniu lub metryki używanej do wystawiania alertu, a także poziom pewności, że istniało złośliwy wpływ na działanie, które doprowadziło do alertu.

Ponadto należy oznaczyć subskrypcje przy użyciu tagów i utworzyć system nazewnictwa, aby identyfikować i klasyfikować zasoby platformy Azure, szczególnie te, które przetwarzają poufne dane.  Odpowiedzialność za korygowanie alertów zależy od zagrożenia dla zasobów platformy Azure i środowiska, w którym wystąpiło zdarzenie. 

- [Alerty zabezpieczeń w Centrum zabezpieczeń Azure](../security-center/security-center-alerts-overview.md) 

- [Organizowanie zasobów platformy Azure przy użyciu tagów](../azure-resource-manager/management/tag-resources.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="103-test-security-response-procedures"></a>10,3: procedury odpowiedzi na zabezpieczenia testowe

**Wskazówki**: przeprowadzanie ćwiczeń w celu przetestowania możliwości reagowania na zdarzenia systemu w regularnych erze. Zidentyfikuj słabe punkty i przerwy oraz popraw plan zgodnie z wymaganiami. 

- [Przewodnik NIST dotyczący testowania, uczenia i ćwiczeń programów dla planów i możliwości IT](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: Podaj szczegóły kontaktu dotyczącego zabezpieczeń i Skonfiguruj powiadomienia dotyczące alertów dotyczących zdarzeń związanych z zabezpieczeniami

**Wskazówki**: informacje kontaktowe dotyczące zdarzenia zabezpieczeń będą używane przez firmę Microsoft do skontaktowania się z Tobą, jeśli firma Microsoft Security Response Center (MSRC) wykryje, że dostęp do danych zostały nadane przez nielegalną lub nieautoryzowaną stronę. Przejrzyj zdarzenia po fakcie, aby upewnić się, że problemy zostały rozwiązane. 

- [Jak ustawić kontakt zabezpieczeń usługi Azure Security Center](../security-center/security-center-provide-security-contact-details.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: Uwzględnij alerty zabezpieczeń w systemie odpowiedzi na zdarzenia

**Wskazówki**: eksportowanie alertów i zaleceń dotyczących Security Center przy użyciu funkcji eksportu ciągłego. Eksport ciągły umożliwia wyeksportowanie alertów i zaleceń ręcznie lub w stały sposób ciągły. Możesz użyć łącznika danych Security Center do przesyłania strumieniowego alertów do kontrolki wskaźnikowej.

- [Jak skonfigurować eksport ciągły](../security-center/continuous-export.md)

- [Jak przesłać strumieniowo alerty do usługi Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: Automatyzowanie odpowiedzi na alerty zabezpieczeń

**Wskazówki**: Użyj funkcji automatyzacji przepływu pracy w programie Azure Security Center, aby automatycznie wyzwalać odpowiedzi za pośrednictwem "Logic Apps" na temat alertów zabezpieczeń i zaleceń.

- [Jak skonfigurować automatyzację przepływu pracy i Logic Apps](../security-center/workflow-automation.md)

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
