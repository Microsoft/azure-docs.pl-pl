---
title: Punkt odniesienia zabezpieczeń platformy Azure dla Container Instances
description: Punkt Container Instances zabezpieczeń zawiera wskazówki proceduralne i zasoby dotyczące wdrażania zaleceń dotyczących zabezpieczeń określonych w te testach porównawczych zabezpieczeń platformy Azure.
author: msmbaldwin
ms.service: container-instances
ms.topic: conceptual
ms.date: 03/30/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 8790e05edbaeb40debd997ea9b35d31b25947761
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2021
ms.locfileid: "107598792"
---
# <a name="azure-security-baseline-for-container-instances"></a>Punkt odniesienia zabezpieczeń platformy Azure dla Container Instances

Ten punkt odniesienia zabezpieczeń stosuje wskazówki z testu porównawczego zabezpieczeń platformy [Azure w wersji 1.0](../security/benchmarks/overview-v1.md) do Container Instances. Test porównawczy zabezpieczeń platformy Azure zawiera zalecenia dotyczące sposobu zabezpieczania rozwiązań w chmurze na platformie Azure. Zawartość jest pogrupowana według **kontrolek zabezpieczeń zdefiniowanych** przez test porównawczy zabezpieczeń platformy Azure i powiązanych wskazówek dotyczących Container Instances. **Wykluczono** mechanizmy kontroli Container Instances, za które odpowiada firma Microsoft.

Aby zobaczyć, Container Instances mapowanie na test porównawczy zabezpieczeń platformy Azure, zobacz pełny Container Instances [mapowania punktów odniesienia zabezpieczeń.](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)

## <a name="network-security"></a>Bezpieczeństwo sieci

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: bezpieczeństwo sieci](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1. Ochrona zasobów platformy Azure w sieciach wirtualnych

**Wskazówki:** Integrowanie grup kontenerów w Azure Container Instances z siecią wirtualną platformy Azure.  Sieci wirtualne platformy Azure umożliwiają umieść wiele zasobów platformy Azure, takich jak grupy kontenerów, w sieci routowalnej bez Internetu.

Kontroluj wychodzący dostęp sieciowy z podsieci delegowane do Azure Container Instances przy użyciu Azure Firewall. 

- [Wdrażanie wystąpień kontenerów w sieci wirtualnej platformy Azure](/azure/container-instances/container-instances-vnet)

- [Jak wdrożyć i skonfigurować Azure Firewall](../firewall/tutorial-firewall-deploy-portal.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2: Monitorowanie i rejestrowanie konfiguracji i ruchu sieci wirtualnych, podsieci i interfejsów sieciowych

**Wskazówki:** użyj Azure Security Center i postępuj zgodnie z zaleceniami ochrony sieci, aby ułatwić zabezpieczanie zasobów sieciowych na platformie Azure. Włącz dzienniki przepływu sieciowej organizacji zabezpieczeń i wysyłaj dzienniki do konta magazynu w celu inspekcji ruchu. Możesz również wysyłać dzienniki przepływu sieciowych grup zabezpieczeń do obszaru roboczego usługi Log Analytics i używać Analiza ruchu, aby zapewnić wgląd w przepływ ruchu w chmurze platformy Azure. Niektóre zalety usługi Analiza ruchu to możliwość wizualizacji aktywności sieciowej i identyfikowania punktów aktywności, identyfikowania zagrożeń bezpieczeństwa, zrozumienia wzorców przepływu ruchu i identyfikowania błędnych konfiguracji sieci. 

- [How to Enable NSG Flow Logs](../network-watcher/network-watcher-nsg-flow-logging-portal.md) 

- [Jak włączyć i używać Analiza ruchu](../network-watcher/traffic-analytics.md) 

- [Opis zabezpieczeń sieci zapewnianych przez Azure Security Center](../security-center/security-center-network-recommendations.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="13-protect-critical-web-applications"></a>1.3. Ochrona krytycznych aplikacji internetowych

**Wskazówki:** Nie dotyczy. Test porównawczy jest przeznaczony dla Azure App Service zasobów obliczeniowych hostowania aplikacji internetowych.

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Odmowa komunikacji ze znanymi złośliwymi adresami IP

**Wskazówki:** Włącz standardową ochronę przed atakami DDoS w sieciach wirtualnych w celu ochrony przed atakami DDoS. Użyj Azure Security Center zintegrowanej analizy zagrożeń, aby uniemożliwić komunikację ze znanymi złośliwymi lub nieużywanymi internetowymi adresami IP.  Wdrażanie Azure Firewall na poszczególnych granicach sieci organizacji z włączoną analizą zagrożeń i skonfigurowaną na "Alert i odmowa" dla złośliwego ruchu sieciowego.

Aby skonfigurować sieciowe Azure Security Center dostępu just in time sieci, można użyć funkcji dostępu just in time, aby ograniczyć narażenie punktów końcowych na zatwierdzone adresy IP przez ograniczony czas. Ponadto użyj funkcji adaptacyjnego Azure Security Center sieci, aby zalecić konfiguracje sieciowej organizacji zabezpieczeń, które ograniczają porty i źródłowe ip na podstawie rzeczywistego ruchu i analizy zagrożeń.

- [Jak skonfigurować ochronę przed DDoS](/azure/virtual-network/manage-ddos-protection)

- [Jak wdrożyć Azure Firewall](../firewall/tutorial-firewall-deploy-portal.md)

- [Understand Azure Security Center Integrated Threat Intelligence](../security-center/azure-defender.md)

- [Understand Azure Security Center Adaptive Network Hardening](../security-center/security-center-adaptive-network-hardening.md)

- [Azure Security Center sieć just in time Access Control](../security-center/security-center-just-in-time.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="15-record-network-packets"></a>1.5. Rejestrowanie pakietów sieciowych

**Wskazówki:** W przypadku korzystania z opartego na chmurze rejestru prywatnego, takiego jak rejestr kontenerów platformy Azure z usługą Azure Container Instances, można włączyć dzienniki przepływu sieciowej grupy zabezpieczeń dla sieciowej grupy zabezpieczeń dołączonej do podsieci używanej do ochrony rejestru kontenerów platformy Azure. Dzienniki przepływu sieciowej organizacji zabezpieczeń można rejestrować na koncie usługi Azure Storage, aby generować rekordy przepływów. Jeśli jest to wymagane do badania anomalii działań, włącz usługę Azure Network Watcher przechwytywanie pakietów.

- [Jak włączyć dzienniki przepływu sieciowej sieciowej organizacji zabezpieczeń](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Jak włączyć Network Watcher](../network-watcher/network-watcher-create.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6. Wdrażanie opartych na sieci systemów wykrywania włamań/zapobiegania włamań (IDS/IPS)

**Wskazówki:** wybierz ofertę z Azure Marketplace, która obsługuje funkcje ids/IPS z możliwościami inspekcji ładunku. Jeśli wykrywanie włamań i/lub zapobieganie na podstawie inspekcji ładunku nie jest wymagane, można użyć Azure Firewall analizy zagrożeń. Azure Firewall filtrowanie oparte na analizie zagrożeń może powiadamiać i odrzucać ruch do i ze znanych złośliwych adresów IP i domen. Adresy IP i domeny pochodzą z kanału informacyjnego analizy zagrożeń firmy Microsoft.

Wd wdrażaj wybrane rozwiązanie zapory na poszczególnych granicach sieci organizacji, aby wykrywać i/lub odrzucać złośliwy ruch.

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

- [Jak wdrożyć Azure Firewall](../firewall/tutorial-firewall-deploy-portal.md)

- [Jak skonfigurować alerty za pomocą Azure Firewall](../firewall/threat-intel.md)

- [Wdrażanie w sieci wirtualnej — Azure Container Instances](container-instances-vnet.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Zarządzanie ruchem do aplikacji internetowych

**Wskazówki:** Nie dotyczy. Test porównawczy jest przeznaczony dla aplikacji internetowych działających w Azure App Service zasobów obliczeniowych.

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8. Zminimalizuj złożoność i koszty administracyjne związane z regułami zabezpieczeń sieci

**Wskazówki:** W przypadku korzystania z opartego na chmurze rejestru prywatnego, takiego jak rejestr kontenerów platformy Azure z usługą Azure Container Instances, w przypadku zasobów, które wymagają dostępu do rejestru kontenerów, użyj tagów usługi sieci wirtualnej dla usługi Azure Container Registry, aby zdefiniować kontrolę dostępu do sieci w sieciowych grupach zabezpieczeń lub Azure Firewall. Podczas tworzenia reguł zabezpieczeń można użyć tagów usługi zamiast konkretnych adresów IP. Określając nazwę tagu usługi "AzureContainerRegistry" w odpowiednim polu źródła lub miejsca docelowego reguły, można zezwolić na ruch dla odpowiedniej usługi lub go odrzucić. Firma Microsoft zarządza prefiksami adresów obejmującymi tag usługi i automatycznie aktualizuje tag usługi w przypadku zmiany adresów.

- [Zezwalaj na dostęp według tagu usługi](https://docs.microsoft.com/azure/container-registry/container-registry-firewall-access-rules#allow-access-by-service-tag)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Obsługa standardowych konfiguracji zabezpieczeń dla urządzeń sieciowych

**Wskazówki:** W przypadku korzystania Azure Container Registry z usługą Azure Container Instances zalecamy zdefiniowanie i zaimplementowanie standardowych konfiguracji zabezpieczeń dla zasobów sieciowych skojarzonych z rejestrem kontenerów platformy Azure.

Użyj Azure Policy w przestrzeniach nazw **Microsoft.ContainerRegistry** i **Microsoft.Network,** aby utworzyć niestandardowe zasady inspekcji lub wymuszania konfiguracji sieci rejestrów kontenerów.

Za pomocą usługi Azure Blueprints można uprościć wdrożenia platformy Azure na dużą skalę przez pakowanie kluczowych artefaktów środowiska, takich jak szablony Azure Resource Manager, kontrolki RBAC platformy Azure i definicje zasad, w pojedynczą definicję strategii. Łatwe stosowanie strategii do nowych subskrypcji oraz dostrajanie kontroli i zarządzania za pomocą wersji.

- [Inspekcja zgodności rejestrów kontenerów platformy Azure przy użyciu Azure Policy](../container-registry/container-registry-azure-policy.md)

- [Jak utworzyć usługę Azure Blueprint](../governance/blueprints/create-blueprint-portal.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="110-document-traffic-configuration-rules"></a>1.10: Udokumentowanie reguł konfiguracji ruchu

**Wskazówki:** Klient może użyć usługi Azure Blueprints, aby uprościć wdrożenia platformy Azure na dużą skalę przez pakowanie kluczowych artefaktów środowiska, takich jak szablony Azure Resource Manager, kontrolki RBAC platformy Azure i zasady, w pojedynczej definicji strategii. Łatwe stosowanie strategii do nowych subskrypcji oraz dostrajanie kontroli i zarządzania za pomocą wersji.

- [Jak utworzyć usługę Azure Blueprint](../governance/blueprints/create-blueprint-portal.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11. Monitorowanie konfiguracji zasobów sieciowych i wykrywanie zmian za pomocą zautomatyzowanych narzędzi

**Wskazówki:** Użyj dziennika aktywności platformy Azure do monitorowania konfiguracji zasobów sieciowych i wykrywania zmian zasobów sieciowych związanych z rejestrami kontenerów. Utwórz alerty w Azure Monitor, które będą wyzwalane w przypadku zmiany krytycznych zasobów sieciowych.

- [Jak wyświetlać i pobierać zdarzenia dziennika aktywności platformy Azure](/azure/azure-monitor/platform/activity-log#view-the-activity-log)

- [Jak tworzyć alerty w Azure Monitor](/azure/azure-monitor/platform/alerts-activity-log)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

## <a name="logging-and-monitoring"></a>Rejestrowanie i monitorowanie

*Aby uzyskać więcej informacji, zobacz [Test porównawczy zabezpieczeń platformy Azure: rejestrowanie i monitorowanie.](../security/benchmarks/security-control-logging-monitoring.md)*

### <a name="22-configure-central-security-log-management"></a>2.2. Konfigurowanie centralnego zarządzania dziennikami zabezpieczeń

**Wskazówki:** pozyskaj dzienniki za pośrednictwem Azure Monitor, aby agregować dane zabezpieczeń wygenerowane przez wystąpienie kontenera platformy Azure. W Azure Monitor obszaru roboczego usługi Log Analytics można wykonywać zapytania i przeprowadzać analizy, a także używać kont usługi Azure Storage do przechowywania długoterminowego/archiwalnej.

- [Rejestrowanie grup kontenerów i wystąpień przy użyciu Azure Monitor kontenerów](container-instances-log-analytics.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3. Włączanie rejestrowania inspekcji dla zasobów platformy Azure

**Wskazówki:** Azure Monitor zbiera dzienniki zasobów (dawniej dzienniki diagnostyczne) dla zdarzeń sterowanych przez użytkownika. Zbieranie i wykorzystanie tych danych w celu inspekcji zdarzeń uwierzytelniania kontenerów i zapewnienia pełnego dziennika aktywności artefaktów, takich jak zdarzenia ściągania i wypychania, aby można było diagnozować problemy z zabezpieczeniami grupy kontenerów.

- [Rejestrowanie grup kontenerów i wystąpień przy użyciu Azure Monitor kontenerów](container-instances-log-analytics.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="25-configure-security-log-storage-retention"></a>2.5. Konfigurowanie przechowywania magazynu dzienników zabezpieczeń

**Wskazówki:** w Azure Monitor ustaw okres przechowywania obszaru roboczego usługi Log Analytics zgodnie z przepisami organizacji dotyczącymi zgodności. Użyj kont usługi Azure Storage na potrzeby magazynu długoterminowego/archiwalnej.

- [Jak ustawić parametry przechowywania dzienników dla obszarów roboczych usługi Log Analytics](/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="26-monitor-and-review-logs"></a>2.6. Monitorowanie i przeglądanie dzienników

**Wskazówki:** Analizowanie i Azure Container Instances dzienników pod Azure Container Instances zachowania i regularne przeglądanie wyników. Użyj Azure Monitor usługi Log Analytics, aby przeglądać dzienniki i wykonywać zapytania dotyczące danych dziennika.

- [Opis obszaru roboczego usługi Log Analytics](/azure/azure-monitor/log-query/log-analytics-tutorial)

- [Jak wykonywać zapytania niestandardowe w Azure Monitor](/azure/azure-monitor/log-query/get-started-queries)

- [Jak utworzyć grupę kontenerów z włączoną obsługą dzienników i dzienniki zapytań](container-instances-log-analytics.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7. Włącz alerty dla działań anomalii

**Wskazówki:** W przypadku korzystania z opartego na chmurze rejestru prywatnego, takiego jak rejestr kontenerów platformy Azure z usługą Azure Container Instances, użyj obszaru roboczego usługi Azure Log Analytics do monitorowania i alertów dotyczących anomalii w dziennikach zabezpieczeń i zdarzeniach związanych z rejestrem kontenerów platformy Azure.

- [Azure Container Registry do oceny diagnostycznej i inspekcji](../container-registry/container-registry-diagnostics-audit-logs.md)

- [Jak rejestrować alerty dotyczące danych dzienników analizy dzienników](/azure/azure-monitor/learn/tutorial-response)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="28-centralize-anti-malware-logging"></a>2.8. Scentralizowanie rejestrowania w poszukiwaniu złośliwego oprogramowania

**Wskazówki:** Nie dotyczy. W przypadku korzystania z opartego na chmurze rejestru prywatnego, takiego jak rejestr kontenerów platformy Azure z usługą Azure Container Instances, usługa Azure Container Registry nie przetwarza ani nie tworzyć dzienników związanych ze złośliwym oprogramowaniem.

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="29-enable-dns-query-logging"></a>2.9: Włączanie rejestrowania zapytań DNS

**Wskazówki:** Nie dotyczy. W przypadku korzystania z opartego na chmurze rejestru prywatnego, takiego jak rejestr kontenerów platformy Azure z usługą Azure Container Instances, usługa Azure Container Registry jest punktem końcowym i nie inicjuje komunikacji, a usługa nie odpytuje systemu DNS.

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

## <a name="identity-and-access-control"></a>Tożsamość i kontrola dostępu

*Aby uzyskać więcej informacji, zobacz [Test porównawczy zabezpieczeń platformy Azure: tożsamość i Access Control.](../security/benchmarks/security-control-identity-access-control.md)*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1. Utrzymywanie spisu kont administracyjnych

**Wskazówki:** Azure Active Directory (Azure AD) ma wbudowane role, które muszą być jawnie przypisane i mogą być odpytywalne. Moduł Azure AD PowerShell umożliwia wykonywanie zapytań ad hoc w celu odnajdywania kont, które są członkami grup administracyjnych.

W przypadku korzystania z opartego na chmurze rejestru prywatnego, takiego jak rejestr kontenerów platformy Azure z usługą Azure Container Instances, dla każdego rejestru kontenerów platformy Azure śledź, czy wbudowane konto administratora jest włączone, czy wyłączone. Wyłącz konto, gdy nie jest w użyciu.

- [Jak uzyskać rolę katalogu w usłudze Azure AD przy użyciu programu PowerShell](/powershell/module/azuread/get-azureaddirectoryrole)

- [Jak uzyskać członków roli katalogu w usłudze Azure AD przy użyciu programu PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember)

- [Azure Container Registry administratora](https://docs.microsoft.com/azure/container-registry/container-registry-authentication#admin-account)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Zmień domyślne hasła tam, gdzie ma to zastosowanie

**Wskazówki:** Azure Active Directory (Azure AD) nie ma koncepcji haseł domyślnych. Inne zasoby platformy Azure wymagające hasła wymuszają, aby hasło było tworzone z wymaganiami co do złożoności i minimalną długością hasła, które różnią się w zależności od usługi. Użytkownik jest odpowiedzialny za aplikacje innych firm i usługi witryny Marketplace, które mogą używać domyślnych haseł.

W przypadku korzystania z opartego na chmurze rejestru prywatnego, takiego jak rejestr kontenerów platformy Azure z usługą Azure Container Instances, jeśli jest włączone domyślne konto administratora rejestru kontenerów platformy Azure, złożone hasła są tworzone automatycznie i powinny być obracane. Wyłącz konto, gdy nie jest w użyciu.

- [Azure Container Registry administratora](https://docs.microsoft.com/azure/container-registry/container-registry-authentication#admin-account)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Użyj dedykowanych kont administracyjnych

**Wskazówki:** Tworzenie standardowych procedur operacyjnych dotyczących korzystania z dedykowanych kont administracyjnych. Użyj Azure Security Center zarządzania tożsamościami i dostępem, aby monitorować liczbę kont administracyjnych.

W przypadku korzystania z opartego na chmurze rejestru prywatnego, takiego jak rejestr kontenerów platformy Azure z usługą Azure Container Instances, należy utworzyć procedury umożliwiające włączenie wbudowanego konta administratora rejestru kontenerów. Wyłącz konto, gdy nie jest w użyciu.

- [Opis Azure Security Center tożsamości i dostępu](../security-center/security-center-identity-access.md)

- [Azure Container Registry administratora](https://docs.microsoft.com/azure/container-registry/container-registry-authentication#admin-account)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4: Użyj Azure Active Directory jednokrotnego (SSO)

**Wskazówki:** wszędzie tam, gdzie Azure Active Directory, używaj logowania jednokrotnego usługi (Azure AD) zamiast konfigurowania pojedynczych poświadczeń autonomicznych dla usługi. Użyj Azure Security Center zaleceń dotyczących zarządzania tożsamościami i dostępem.

W przypadku korzystania z opartego na chmurze rejestru prywatnego, takiego jak rejestr kontenerów platformy Azure z usługą Azure Container Instances, w celu uzyskania indywidualnego dostępu do rejestru kontenerów użyj logowania indywidualnegowedytowanych z usługą Azure AD.

- [Understand SSO with Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

- [Indywidualne logowanie do rejestru kontenerów](https://docs.microsoft.com/azure/container-registry/container-registry-authentication#individual-login-with-azure-ad)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Używaj uwierzytelniania wieloskładnikowego dla Azure Active Directory dostępu opartego na Azure Active Directory danych

**Wskazówki:** włączanie Azure Active Directory wieloskładnikowego (Azure AD) i postępuj zgodnie z Azure Security Center tożsamości i zarządzania dostępem.

- [Jak włączyć uwierzytelnianie wieloskładnikowe na platformie Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Jak monitorować tożsamość i dostęp w Azure Security Center](../security-center/security-center-identity-access.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6. Użyj dedykowanych maszyn (stacji roboczych z dostępem uprzywilejowanym) do wykonywania wszystkich zadań administracyjnych

**Wskazówki:** Używanie stacji roboczych z dostępem uprzywilejowanym z uwierzytelnianiem wieloskładnikowym skonfigurowanym do logowania się do zasobów platformy Azure i konfigurowania ich.

- [Dowiedz się więcej o stacjach roboczych z dostępem uprzywilejowanym](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Jak włączyć uwierzytelnianie wieloskładnikowe na platformie Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: Rejestrowanie i alerty dotyczące podejrzanych działań z kont administracyjnych

**Wskazówki:** raporty Azure Active Directory (Azure AD) dotyczące generowania dzienników i alertów w przypadku wystąpienia podejrzanej lub niebezpiecznej aktywności w środowisku. Użyj Azure Security Center, aby monitorować działania tożsamości i dostępu.

- [Identyfikowanie użytkowników usługi Azure AD oflagowanych na skutek ryzykownego działania](../active-directory/identity-protection/overview-identity-protection.md)

- [Jak monitorować działania użytkowników związane z tożsamościami i dostępem w usłudze Azure Security Center](../security-center/security-center-identity-access.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Zarządzanie zasobami platformy Azure tylko z zatwierdzonych lokalizacji

**Wskazówki:** Użyj dostępu warunkowego nazwanych lokalizacji, aby zezwolić na dostęp tylko z określonych grup logicznych zakresów adresów IP lub krajów/regionów.

- [Jak skonfigurować nazwane lokalizacje na platformie Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="39-use-azure-active-directory"></a>3.9: Użyj Azure Active Directory

**Wskazówki:** Użyj Azure Active Directory (Azure AD) jako centralnego systemu uwierzytelniania i autoryzacji. Usługa Azure AD chroni dane przy użyciu silnego szyfrowania danych magazynowany i podczas przesyłania. Usługa Azure AD dodaje również iniekty, skróty i bezpiecznie przechowuje poświadczenia użytkownika.

- [Jak utworzyć i skonfigurować wystąpienie usługi Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Regularne przeglądanie i uzgadnianie dostępu użytkowników

**Wskazówki:** Azure Active Directory (Azure AD) zawiera dzienniki, które ułatwiają odnajdywanie nieaktualnych kont. Ponadto usługa Azure Identity Access Reviews umożliwia wydajne zarządzanie członkostwem w grupach, dostępem do aplikacji dla przedsiębiorstw i przypisań ról. Dostęp użytkowników można regularnie przeglądać, aby upewnić się, że tylko odpowiedni użytkownicy mają dalszy dostęp.

- [Opis raportowania w usłudze Azure AD](/azure/active-directory/reports-monitoring/)

- [Jak używać przeglądów dostępu tożsamości na platformie Azure](../active-directory/governance/access-reviews-overview.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Monitorowanie prób uzyskania dostępu do dezaktywowanych poświadczeń

**Wskazówki:** Masz dostęp do źródeł dzienników aktywności, inspekcji i zdarzeń o ryzykach logowania usługi Azure Active Directory (Azure AD), które umożliwiają integrację z dowolnym narzędziem do zarządzania informacjami i zdarzeniami zabezpieczeń (SIEM).

Ten proces można usprawnić, tworząc ustawienia diagnostyczne dla kont użytkowników usługi Azure AD i wysyłając dzienniki inspekcji i logują się do obszaru roboczego usługi Log Analytics. Żądane alerty można skonfigurować w obszarze roboczym usługi Log Analytics.

- [Jak zintegrować dzienniki aktywności platformy Azure z usługą Azure Monitor](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12: Odchylenie zachowania alertu podczas logowania na koncie

**Wskazówki:** korzystanie Azure Active Directory (Azure AD) Risk and Identity Protection w celu konfigurowania automatycznych odpowiedzi na wykryte podejrzane akcje związane z tożsamościami użytkowników.

- [Jak wyświetlić ryzykowne logowania w usłudze Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

- [Jak skonfigurować i włączyć zasady ryzyka związane z usługą Identity Protection](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Zapewnienie firmie Microsoft dostępu do odpowiednich danych klientów podczas scenariuszy pomocy technicznej

**Wskazówki:** Niedostępne; Skrytka klienta obecnie obsługiwane w przypadku Azure Container Instances.

- [Lista Skrytka klienta obsługiwanych usług](https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

## <a name="data-protection"></a>Ochrona danych

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: ochrona danych](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1. Utrzymywanie spisu informacji poufnych

**Wskazówki:** użyj tagów zasobów, aby pomóc w śledzeniu rejestrów kontenerów platformy Azure, które przechowują lub przetwarzają poufne informacje.

Tagowanie i przechowywanie wersji obrazów kontenerów lub innych artefaktów w rejestrze oraz blokowanie obrazów lub repozytoriów w celu śledzenia obrazów, które przechowują lub przetwarzają poufne informacje.

- [Jak tworzyć tagi i ich używać](../azure-resource-manager/management/tag-resources.md)

- [Zalecenia dotyczące tagowania i wersjonarowania obrazów kontenerów](../container-registry/container-registry-image-tag-version.md)

- [Blokowanie obrazu kontenera w rejestrze kontenerów platformy Azure](../container-registry/container-registry-image-lock.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Izolowanie systemów przechowujące lub przetwarzające informacje poufne

**Wskazówki:** Implementowanie oddzielnych rejestrów kontenerów, subskrypcji i/lub grup zarządzania w celu testowania, testowania i produkcji. Zasoby przechowujące lub przetwarzające poufne dane powinny być wystarczająco odizolowane.

Zasoby powinny być oddzielone siecią wirtualną lub podsiecią, odpowiednio otagowane i zabezpieczone przez sieciową grupę zabezpieczeń lub Azure Firewall.

- [Jak utworzyć dodatkowe subskrypcje platformy Azure](../cost-management-billing/manage/create-subscription.md)

- [Jak tworzyć grupy zarządzania](../governance/management-groups/create-management-group-portal.md)

- [Jak tworzyć tagi i ich używać](../azure-resource-manager/management/tag-resources.md)

- [Ograniczanie dostępu do rejestru kontenerów platformy Azure przy użyciu sieci wirtualnej platformy Azure lub reguł zapory](../container-registry/container-registry-vnet.md)

- [Jak utworzyć sieciowej grupy zabezpieczeń za pomocą konfiguracji zabezpieczeń](../virtual-network/tutorial-filter-network-traffic.md)

- [Jak wdrożyć Azure Firewall](../firewall/tutorial-firewall-deploy-portal.md)

- [Jak skonfigurować alert lub alert i odmówić przy użyciu Azure Firewall](../firewall/threat-intel.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Monitorowanie i blokowanie nieautoryzowanego transferu poufnych informacji

**Wskazówki:** Wdrożenie zautomatyzowanego narzędzia na obwodach sieci, które monitoruje nieautoryzowany transfer poufnych informacji i blokuje takie transfery podczas powiadamiania specjalistów ds. zabezpieczeń informacji.

W przypadku podstawowej platformy zarządzanej przez firmę Microsoft firma Microsoft traktuje wszystkie dane klientów jako poufne i bardzo długo chroni przed utratą i utratą danych klientów. Aby zapewnić bezpieczeństwo danych klientów na platformie Azure, firma Microsoft wdrożyła i utrzymuje pakiet niezawodnych funkcji i kontroli ochrony danych.

- [Informacje na temat ochrony danych klientów na platformie Azure](../security/fundamentals/protection-customer-data.md)

**Odpowiedzialność**: Współużytkowane

**Azure Security Center monitorowania:** Brak

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Szyfruj wszystkie poufne informacje podczas przesyłania

**Wskazówki:** Upewnij się, że każdy klient łączący się z Azure Container Registry może negocjować TLS 1.2 lub nowszą. Microsoft Azure domyślnie negocjują negocjują standard TLS 1.2.

Postępuj Azure Security Center dotyczących szyfrowania danych w spoczynku i szyfrowania podczas przesyłania, jeśli ma to zastosowanie.

- [Opis szyfrowania podczas przesyłania za pomocą platformy Azure](https://docs.microsoft.com/azure/security/fundamentals/encryption-overview#encryption-of-data-in-transit)

**Odpowiedzialność**: Współużytkowane

**Azure Security Center monitorowania:** Brak

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5. Identyfikowanie poufnych danych za pomocą aktywnego narzędzia odnajdywania

**Wskazówki:** W przypadku korzystania z opartego na chmurze rejestru prywatnego, takiego jak rejestr kontenerów platformy Azure, z usługą Azure Container Instances funkcje identyfikacji danych, klasyfikacji i zapobiegania utracie nie są jeszcze dostępne dla Azure Container Registry. Implementowanie rozwiązania innej firmy, jeśli jest to wymagane do celów zgodności.

W przypadku podstawowej platformy zarządzanej przez firmę Microsoft firma Microsoft traktuje wszystkie dane klientów jako poufne i bardzo długo chroni przed utratą i utratą danych klientów. Aby zapewnić bezpieczeństwo danych klientów na platformie Azure, firma Microsoft wdrożyła i utrzymuje pakiet niezawodnych funkcji i kontroli ochrony danych.

- [Szyfrowanie danych wdrożenia przy użyciu Azure Container Instances](container-instances-encrypt-data.md)

- [Informacje na temat ochrony danych klientów na platformie Azure](../security/fundamentals/protection-customer-data.md)

**Odpowiedzialność**: Współużytkowane

**Azure Security Center monitorowania:** Brak

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4.6: Kontrola dostępu do zasobów przy użyciu kontroli dostępu opartej na rolach

**Wskazówki:** Jeśli korzystasz z opartego na chmurze rejestru prywatnego, takiego jak Azure Container Registry z usługą Azure Container Instances, użyj kontroli dostępu opartej na rolach (RBAC) platformy Azure, aby zarządzać dostępem do danych i zasobów w rejestrze kontenerów platformy Azure.

- [Jak skonfigurować RBAC platformy Azure na platformie Azure](../role-based-access-control/role-assignments-portal.md)

- [Azure Container Registry ról i uprawnień](../container-registry/container-registry-roles.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7. Wymuszanie kontroli dostępu przy użyciu ochrony przed utratą danych opartej na hoście

**Wskazówki:** jeśli jest to wymagane w celu zapewnienia zgodności zasobów obliczeniowych, należy zaimplementować narzędzie innej firmy, takie jak zautomatyzowane rozwiązanie do ochrony przed utratą danych oparte na hoście, aby wymusić kontrolę dostępu do danych nawet wtedy, gdy dane są kopiowane z systemu.

W przypadku podstawowej platformy zarządzanej przez firmę Microsoft firma Microsoft traktuje wszystkie dane klientów jako poufne i bardzo długo chroni przed utratą i narażeniem danych klientów. Aby zapewnić bezpieczeństwo danych klientów na platformie Azure, firma Microsoft wdrożyła i utrzymuje pakiet niezawodnych funkcji i kontroli ochrony danych.

- [Informacje na temat ochrony danych klientów na platformie Azure](../security/fundamentals/protection-customer-data.md)

**Odpowiedzialność**: Współużytkowane

**Azure Security Center monitorowania:** Brak

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Szyfrowanie poufnych informacji w spoczynku

**Wskazówki:** Używanie szyfrowania danych magazynowych we wszystkich zasobach platformy Azure. W przypadku korzystania z opartego na chmurze rejestru prywatnego, takiego jak rejestr kontenerów platformy Azure z usługą Azure Container Instances, domyślnie wszystkie dane w rejestrze kontenerów platformy Azure są szyfrowane podczas przechowywania przy użyciu kluczy zarządzanych przez firmę Microsoft.

- [Informacje o szyfrowaniu danych magazynowanych na platformie Azure](../security/fundamentals/encryption-atrest.md)

- [Klucze zarządzane przez klienta w Azure Container Registry](https://aka.ms/acr/cmk)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9. Rejestrowanie zmian krytycznych zasobów platformy Azure i alerty dotyczące zmian

**Wskazówki:** Obszary robocze usługi Log Analytics zapewniają scentralizowaną lokalizację do przechowywania i wykonywania zapytań dotyczących danych dzienników nie tylko z zasobów platformy Azure, ale także zasobów lokalnych i zasobów w innych chmurach. Azure Container Instances wbudowaną obsługę wysyłania dzienników i danych zdarzeń do Azure Monitor dzienników.

- [Rejestrowanie grup kontenerów i wystąpień przy użyciu Azure Monitor kontenerów](container-instances-log-analytics.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

## <a name="vulnerability-management"></a>Zarządzanie lukami w zabezpieczeniach

*Aby uzyskać więcej informacji, zobacz [Test porównawczy zabezpieczeń platformy Azure: zarządzanie lukami w zabezpieczeniach.](../security/benchmarks/security-control-vulnerability-management.md)*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1. Uruchamianie zautomatyzowanych narzędzi do skanowania luk w zabezpieczeniach

**Wskazówki:** Skorzystaj z rozwiązań do skanowania obrazów kontenerów w rejestrze prywatnym i identyfikowania potencjalnych luk w zabezpieczeniach. Ważne jest, aby zrozumieć głębokość wykrywania zagrożeń zapewnianą przez różne rozwiązania. Postępuj zgodnie z zaleceniami Azure Security Center oceny luk w zabezpieczeniach na obrazach kontenerów. Opcjonalnie wdrażaj rozwiązania innych firm z Azure Marketplace, aby przeprowadzać oceny luk w zabezpieczeniach obrazów.

- [Zalecenia dotyczące zabezpieczeń kontenerów dotyczące monitorowania i skanowania pod Azure Container Instances](container-instances-image-security.md)

- [Azure Container Registry integracja z Security Center](/azure/security-center/azure-container-registry-integration)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2. Wdrażanie zautomatyzowanego rozwiązania do zarządzania poprawkami systemu operacyjnego

**Wskazówki:** Firma Microsoft wykonuje zarządzanie poprawkami w systemach bazowych, które obsługują uruchomione kontenery.

Automatyzowanie aktualizacji obrazu kontenera po wykryciu aktualizacji obrazów podstawowych z systemu operacyjnego i innych poprawek.

- [Informacje o aktualizacjach obrazu podstawowego Azure Container Registry zadań](../container-registry/container-registry-tasks-base-images.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5.3. Wdrażanie zautomatyzowanego rozwiązania do zarządzania poprawkami dla tytułów oprogramowania innych firm

**Wskazówki:** do poprawiania obrazów aplikacji można użyć rozwiązania innej firmy. Ponadto w przypadku korzystania z opartego na chmurze rejestru prywatnego, takiego jak rejestr kontenerów platformy Azure z usługą Azure Container Instances, można uruchamiać zadania usługi Azure Container Registry w celu zautomatyzowania aktualizacji obrazów aplikacji w rejestrze kontenerów na podstawie poprawek zabezpieczeń lub innych aktualizacji w obrazach podstawowych.

- [Informacje o aktualizacjach obrazu podstawowego dla zadania usługi ACR](../container-registry/container-registry-tasks-base-images.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: Porównanie skanów luk w zabezpieczeniach typu wstecz

**Wskazówki:** W przypadku korzystania z opartego na chmurze rejestru prywatnego, takiego jak rejestr kontenerów platformy Azure z usługą Azure Container Instances, zintegruj usługę Azure Container Registry (ACR) z usługą Azure Security Center, aby umożliwić okresowe skanowanie obrazów kontenerów w celu luki w zabezpieczeniach. Opcjonalnie wdrażaj rozwiązania innych firm z Azure Marketplace w celu okresowego skanowania luk w zabezpieczeniach obrazów.

- [Azure Container Registry integracja z Security Center](../security-center/defender-for-container-registries-introduction.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5. Określanie priorytetów korygowania wykrytych luk w zabezpieczeniach za pomocą procesu oceny ryzyka

**Wskazówki:** Jeśli korzystasz z opartego na chmurze rejestru prywatnego, takiego jak rejestr kontenerów platformy Azure, z usługą Azure Container Instances, zintegruj usługę Azure Container Registry (ACR) z usługą Azure Security Center, aby umożliwić okresowe skanowanie obrazów kontenerów w celu oceny luk w zabezpieczeniach i klasyfikowania zagrożeń. Opcjonalnie wdrażaj rozwiązania innych firm z Azure Marketplace, aby przeprowadzać okresowe skanowanie luk w zabezpieczeniach obrazów i klasyfikację ryzyka.

- [Azure Container Registry integracja z Security Center](../security-center/defender-for-container-registries-introduction.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

## <a name="inventory-and-asset-management"></a>Zarządzanie magazynem i zasobami

*Aby uzyskać więcej informacji, zobacz [Test porównawczy zabezpieczeń platformy Azure: spis i zarządzanie zasobami.](../security/benchmarks/security-control-inventory-asset-management.md)*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1. Korzystanie z rozwiązania do automatycznego odnajdywania zasobów

**Wskazówki:** użyj Azure Resource Graph do wykonywania zapytań i odnajdywania wszystkich zasobów (takich jak zasoby obliczeniowe, magazynowe, sieciowe, porty i protokoły itp.) w ramach subskrypcji. Upewnij się, że w dzierżawie są odpowiednie uprawnienia (odczyt) i wyliczenie wszystkich subskrypcji platformy Azure, a także zasobów w ramach twoich subskrypcji.

Mimo że klasyczne zasoby platformy Azure można Resource Graph za pośrednictwem usługi , zdecydowanie zaleca się tworzenie i używanie Azure Resource Manager zasobów w przyszłości.

- [Jak tworzyć zapytania za pomocą Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

- [Jak wyświetlić subskrypcje platformy Azure](/powershell/module/az.accounts/get-azsubscription)

- [Opis kontroli RBAC na platformie Azure](../role-based-access-control/overview.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="62-maintain-asset-metadata"></a>6.2. Obsługa metadanych zasobów

**Wskazówki:** W przypadku korzystania z opartego na chmurze rejestru prywatnego, takiego jak Azure Container Registry (ACR) z usługą Azure Container Instances, usługa ACR przechowuje metadane, w tym tagi i manifesty dla obrazów w rejestrze. Postępuj zgodnie z zalecanymi rozwiązaniami w zakresie tagowania artefaktów.

- [Informacje o rejestrach, repozytoriach i obrazach](../container-registry/container-registry-concepts.md)

- [Zalecenia dotyczące tagowania i wersjonarowania obrazów kontenerów](../container-registry/container-registry-image-tag-version.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="63-delete-unauthorized-azure-resources"></a>6.3. Usuwanie nieautoryzowanych zasobów platformy Azure

**Wskazówki:** W przypadku korzystania z opartego na chmurze rejestru prywatnego, takiego jak Azure Container Registry (ACR) z usługą Azure Container Instances, usługa ACR przechowuje metadane, w tym tagi i manifesty dla obrazów w rejestrze. Postępuj zgodnie z zalecanymi rozwiązaniami w zakresie tagowania artefaktów.

- [Informacje o rejestrach, repozytoriach i obrazach](../container-registry/container-registry-concepts.md)

- [Zalecenia dotyczące tagowania i wersjonarowania obrazów kontenerów](../container-registry/container-registry-image-tag-version.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4: Definiowanie i utrzymywanie spisu zatwierdzonych zasobów platformy Azure

**Wskazówki:** należy utworzyć spis zatwierdzonych zasobów platformy Azure zgodnie z potrzebami organizacji.

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Monitorowanie niezatwierdzonych zasobów platformy Azure

**Wskazówki:** użyj Azure Policy, aby wprowadzić ograniczenia dotyczące typu zasobów, które mogą być tworzone w twoich subskrypcjach.

Użyj Azure Resource Graph, aby odpytywać/odnajdywać zasoby w ramach swoich subskrypcji. Upewnij się, że wszystkie zasoby platformy Azure obecne w środowisku zostały zatwierdzone.

- [Inspekcja zgodności rejestrów kontenerów platformy Azure przy użyciu Azure Policy](../container-registry/container-registry-azure-policy.md)

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

- [Jak tworzyć zapytania za pomocą usługi Azure Graph](../governance/resource-graph/first-query-portal.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: Monitorowanie niezatwierdzonych aplikacji w ramach zasobów obliczeniowych

**Wskazówki:** W przypadku korzystania z opartego na chmurze rejestru prywatnego, takiego jak Azure Container Registry (ACR) z usługą Azure Container Instances, należy analizować i monitorować dzienniki usługi Azure Container Registry pod Azure Container Registry pod celu zachowania anomalii i regularnie przeglądać wyniki. Użyj Azure Monitor usługi Log Analytics, aby przeglądać dzienniki i wykonywać zapytania dotyczące danych dziennika.

- [Azure Container Registry do oceny diagnostycznej i inspekcji](../container-registry/container-registry-diagnostics-audit-logs.md)

- [Opis obszaru roboczego usługi Log Analytics](/azure/azure-monitor/log-query/log-analytics-tutorial)

- [Jak wykonywać zapytania niestandardowe w Azure Monitor](/azure/azure-monitor/log-query/get-started-queries)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7. Usuwanie niezatwierdzonych zasobów i aplikacji platformy Azure

**Wskazówki:** Azure Automation zapewnia pełną kontrolę podczas wdrażania, obsługi i likwidowania obciążeń i zasobów. Możesz zaimplementować własne rozwiązanie do usuwania nieautoryzowanych zasobów platformy Azure. 

- [Wprowadzenie do usługi Azure Automation](../automation/automation-intro.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="68-use-only-approved-applications"></a>6.8: Używaj tylko zatwierdzonych aplikacji

**Wskazówki:** Nie dotyczy. Test porównawczy został zaprojektowany dla zasobów obliczeniowych.

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="69-use-only-approved-azure-services"></a>6.9. Używaj tylko zatwierdzonych usług platformy Azure

**Wskazówki:** Skorzystaj Azure Policy, aby ograniczyć usługi, które można aprowizować w swoim środowisku.

- [Inspekcja zgodności rejestrów kontenerów platformy Azure przy użyciu Azure Policy](../container-registry/container-registry-azure-policy.md)

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

- [Jak odmówić określonego typu zasobu za pomocą Azure Policy](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10. Utrzymywanie spisu zatwierdzonych tytułów oprogramowania

**Wskazówki:** Nie dotyczy. Test porównawczy został zaprojektowany dla zasobów obliczeniowych.

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Ograniczanie możliwości interakcji użytkowników z Azure Resource Manager

**Wskazówki:** Użyj konfiguracji specyficznych dla systemu operacyjnego lub zasobów innych firm, aby ograniczyć użytkownikom możliwość wykonywania skryptów w ramach zasobów obliczeniowych platformy Azure.

- [Jak skonfigurować dostęp warunkowy, aby zablokować dostęp do usługi Azure Resources Manager](../role-based-access-control/conditional-access-azure-management.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: Ogranicz użytkownikom możliwość wykonywania skryptów w ramach zasobów obliczeniowych

**Wskazówki:** Użyj konfiguracji specyficznych dla systemu operacyjnego lub zasobów innych firm, aby ograniczyć użytkownikom możliwość wykonywania skryptów w ramach zasobów obliczeniowych platformy Azure.

- [Na przykład jak kontrolować wykonywanie skryptów programu PowerShell w środowiskach windows](/powershell/module/microsoft.powershell.security/set-executionpolicy)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Fizyczne lub logiczne rozdzielenie aplikacji wysokiego ryzyka

**Wskazówki:** Oprogramowanie, które jest wymagane dla operacji biznesowych, ale może ponosić wyższe ryzyko dla organizacji, powinno być izolowane we własnej maszynie wirtualnej i/lub sieci wirtualnej i wystarczająco zabezpieczone za pomocą sieciowej grupy zabezpieczeń Azure Firewall lub sieciowej.

- [Jak utworzyć sieć wirtualną](../virtual-network/quick-create-portal.md)

- [Jak utworzyć sieciowej grupy zabezpieczeń za pomocą konfiguracji zabezpieczeń](../virtual-network/tutorial-filter-network-traffic.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

## <a name="secure-configuration"></a>Bezpieczna konfiguracja

*Aby uzyskać więcej informacji, zobacz [Test porównawczy zabezpieczeń platformy Azure: bezpieczna konfiguracja.](../security/benchmarks/security-control-secure-configuration.md)*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1. Ustanów bezpieczne konfiguracje dla wszystkich zasobów platformy Azure

**Wskazówki:** Użyj Azure Policy lub Azure Security Center, aby zachować konfiguracje zabezpieczeń dla wszystkich zasobów platformy Azure.

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

- [Inspekcja zgodności rejestrów kontenerów platformy Azure przy użyciu Azure Policy](../container-registry/container-registry-azure-policy.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="72-establish-secure-operating-system-configurations"></a>7.2. Ustanawianie bezpiecznych konfiguracji systemu operacyjnego

**Wskazówki:** skorzystaj Azure Security Center zalecenia "Korygowanie luk w zabezpieczeniach konfiguracji zabezpieczeń na komputerze Virtual Machines", aby zachować konfiguracje zabezpieczeń dla wszystkich zasobów obliczeniowych.

- [Jak monitorować Azure Security Center zalecenia](../security-center/security-center-recommendations.md)

- [Jak skorygować Azure Security Center zalecenia](../security-center/security-center-remediate-recommendations.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Obsługa bezpiecznych konfiguracji zasobów platformy Azure

**Wskazówki:** Użyj efektów zasad platformy Azure [deny] i [deploy if not exist], aby wymusić bezpieczne ustawienia w zasobach platformy Azure.

W przypadku korzystania z opartego na chmurze rejestru prywatnego, takiego jak Azure Container Registry (ACR) z usługą Azure Container Instances, przejmij zgodność rejestrów kontenerów platformy Azure przy użyciu Azure Policy:.

- [Inspekcja zgodności rejestrów kontenerów platformy Azure przy użyciu Azure Policy](../container-registry/container-registry-azure-policy.md)

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

- [Zrozumienie Azure Policy danych](../governance/policy/concepts/effects.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Obsługa bezpiecznych konfiguracji systemu operacyjnego

**Wskazówki:** Nie dotyczy; Ta kontrolka jest przeznaczona dla zasobów obliczeniowych.

**Odpowiedzialność**: Współużytkowane

**Azure Security Center monitorowania:** Brak

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Bezpieczne przechowywanie konfiguracji zasobów platformy Azure

**Wskazówki:** Jeśli używasz niestandardowych definicji zasad platformy Azure, użyj Azure Repos, aby bezpiecznie przechowywać kod i zarządzać jego kodem.

- [Jak przechowywać kod w Azure DevOps](/azure/devops/repos/git/gitworkflow)

- [Azure Repos dokumentacji](/azure/devops/repos/)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Bezpieczne przechowywanie niestandardowych obrazów systemu operacyjnego

**Wskazówki:** Nie dotyczy; Ta kontrolka ma zastosowanie tylko do zasobów obliczeniowych.

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Wdrażanie narzędzi do zarządzania konfiguracją dla zasobów platformy Azure

**Wskazówki:** użyj Azure Policy do alertów, inspekcji i wymuszania konfiguracji systemu. Ponadto opracuj proces i potok do zarządzania wyjątkami zasad.

W przypadku korzystania z opartego na chmurze rejestru prywatnego, takiego jak Azure Container Registry (ACR) z usługą Azure Container Instances, przejmij zgodność rejestrów kontenerów platformy Azure przy użyciu Azure Policy:.

- [Inspekcja zgodności rejestrów kontenerów platformy Azure przy użyciu Azure Policy](../container-registry/container-registry-azure-policy.md)

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

- [Opis Azure Policy danych](../governance/policy/concepts/effects.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8: Wdrażanie narzędzi do zarządzania konfiguracją dla systemów operacyjnych

**Wskazówki:** Nie dotyczy. Test porównawczy dotyczy zasobów obliczeniowych.

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Implementowanie zautomatyzowanego monitorowania konfiguracji dla zasobów platformy Azure

**Wskazówki:** użyj Azure Security Center, aby przeprowadzić skanowanie bazowe zasobów platformy Azure. 

Zastosuj Azure Policy, aby wprowadzić ograniczenia dotyczące typu zasobów, które można utworzyć w subskrypcjach.

W przypadku korzystania z opartego na chmurze rejestru prywatnego, takiego jak Azure Container Registry (ACR) z usługą Azure Container Instances, przejmij zgodność rejestrów kontenerów platformy Azure przy użyciu Azure Policy:.

- [Jak skorygować zalecenia w Azure Security Center](../security-center/security-center-remediate-recommendations.md)

- [Inspekcja zgodności rejestrów kontenerów platformy Azure przy użyciu Azure Policy](../container-registry/container-registry-azure-policy.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Implementowanie zautomatyzowanego monitorowania konfiguracji dla systemów operacyjnych

**Wskazówki:** Użyj Azure Security Center do przeprowadzania skanowania linii bazowej dla systemu operacyjnego i ustawień platformy Docker dla kontenerów. 

- [Omówienie rekomendacji dotyczących kontenera usługi Azure Security Center](../security-center/container-security.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="711-manage-azure-secrets-securely"></a>7.11: Bezpieczne zarządzanie wpisami tajnymi platformy Azure

**Wskazówki:** używanie tożsamości usługi zarządzanej w połączeniu z Azure Key Vault, aby uprościć i zabezpieczyć zarządzanie kluczami tajnymi dla aplikacji w chmurze.

- [Jak zintegrować usługę z tożsamościami zarządzanymi platformy Azure](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

- [Jak utworzyć Key Vault](../key-vault/secrets/quick-create-portal.md)

- [Jak uwierzytelnić się w Key Vault](container-instances-managed-identity.md)

- [Jak przypisać zasady Key Vault dostępu](../key-vault/general/assign-access-policy-portal.md)

- [Jak używać tożsamości zarządzanych z usługą Azure Container Instances](../container-registry/container-registry-tasks-authentication-managed-identity.md)

- [Jak szyfrować dane za pomocą Container Instances](container-instances-encrypt-data.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Bezpieczne i automatyczne zarządzanie tożsamościami

**Wskazówki:** Korzystanie z tożsamości zarządzanych w celu zapewnienia usługom platformy Azure automatycznie zarządzanej tożsamości w usłudze Azure Active Directory (Azure AD). Tożsamości zarządzane umożliwiają uwierzytelnianie w dowolnej usłudze obsługującej uwierzytelnianie usługi Azure AD, w tym Azure Key Vault, bez żadnych poświadczeń w kodzie.

W przypadku korzystania z opartego na chmurze rejestru prywatnego, takiego jak Azure Container Registry (ACR) z usługą Azure Container Instances, przejmij zgodność rejestrów kontenerów platformy Azure przy użyciu Azure Policy:.

- [Inspekcja zgodności rejestrów kontenerów platformy Azure przy użyciu Azure Policy](../container-registry/container-registry-azure-policy.md)

- [Jak skonfigurować tożsamości zarządzane](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

- [Jak używać tożsamości zarządzanych z usługą Azure Container Instances](container-instances-managed-identity.md)

- [Używanie tożsamości zarządzanej platformy Azure do uwierzytelniania w rejestrze kontenerów platformy Azure](../container-registry/container-registry-authentication-managed-identity.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Eliminowanie niezamierzonego narażenia na poświadczenia

**Wskazówki:** implementowanie skanera poświadczeń w celu identyfikowania poświadczeń w kodzie. Skaner poświadczeń ułatwia również przenoszenie odnalezionych poświadczeń do bezpieczniejszych lokalizacji, takich jak usługa Azure Key Vault.

- [Jak skonfigurować skaner poświadczeń](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

## <a name="malware-defense"></a>Ochrona przed złośliwym oprogramowaniem

*Aby uzyskać więcej informacji, zobacz [Test porównawczy zabezpieczeń platformy Azure: Malware Defense.](../security/benchmarks/security-control-malware-defense.md)*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1. Korzystanie z centralnie zarządzanego oprogramowania chroniącego przed złośliwym oprogramowaniem

**Wskazówki:** używaj Microsoft Antimalware dla Azure Cloud Services i Virtual Machines, aby stale monitorować i chronić zasoby. W przypadku systemu Linux użyj rozwiązania do ochrony przed złośliwym oprogramowaniem innej firmy.

- [Jak skonfigurować Microsoft Antimalware dla Cloud Services i Virtual Machines](../security/fundamentals/antimalware.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Wstępne skanowanie plików do przesłania do zasobów platformy Azure bez obliczeń

**Wskazówki:** Microsoft Antimalware na podstawowym hoście, który obsługuje usługi platformy Azure (na przykład Azure Container Instances), ale nie jest uruchamiany na danych klienta.

Wstępnie przeskanuj wszystkie pliki przekazywane do zasobów platformy Azure, które nie są obliczeniowe, takich jak App Service, Data Lake Storage, Blob Storage itp.

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

## <a name="data-recovery"></a>Odzyskiwanie danych

*Aby uzyskać więcej informacji, zobacz [Test porównawczy zabezpieczeń platformy Azure: odzyskiwanie danych.](../security/benchmarks/security-control-data-recovery.md)*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Zapewnianie regularnych automatycznych kopii zapasowej

**Wskazówki:** W przypadku korzystania z opartego na chmurze rejestru prywatnego, takiego jak Azure Container Registry (ACR) z usługą Azure Container Instances, dane w rejestrze kontenerów usługi Microsoft Azure są zawsze automatycznie replikowane w celu zapewnienia trwałości i wysokiej dostępności. Azure Container Registry kopiuje dane, aby chronić je przed planowanymi i nieplanowanych zdarzeniami.

Opcjonalnie replikuj geograficznie rejestr kontenerów, aby zachować repliki rejestru w wielu regionach platformy Azure.

- [Replikacja geograficzna w usłudze Azure Container Registry](../container-registry/container-registry-geo-replication.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2. Wykonywanie pełnych kopii zapasowych systemu i tworzenie kopii zapasowych wszystkich kluczy zarządzanych przez klienta

**Wskazówki:** opcjonalnie możesz wrócić do kopii zapasowej obrazów kontenerów, importując dane z jednego rejestru do innego.

Kopię zapasową kluczy zarządzanych przez klienta w Azure Key Vault przy użyciu narzędzi wiersza polecenia lub zestawów SDK platformy Azure.

- [Importowanie obrazów kontenerów do rejestru kontenerów](../container-registry/container-registry-import-images.md)

- [Jak utworzyć kopię zapasową kluczy magazynu kluczy na platformie Azure](/powershell/module/az.keyvault/backup-azkeyvaultkey)

- [Szyfrowanie danych wdrożenia za pomocą Container Instances](container-instances-encrypt-data.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Zweryfikuj wszystkie kopie zapasowe, w tym klucze zarządzane przez klienta

**Wskazówki:** Testowanie przywracania kopii zapasowej kluczy zarządzanych przez klienta w usłudze Azure Key Vault użyciu narzędzi wiersza polecenia lub zestawów SDK platformy Azure.

- [Jak przywrócić klucze Azure Key Vault na platformie Azure](/powershell/module/az.keyvault/restore-azkeyvaultkey)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4. Zapewnianie ochrony kopii zapasowych i kluczy zarządzanych przez klienta

**Wskazówki:** możesz włączyć ochronę Soft-Delete w Azure Key Vault, aby chronić klucze przed przypadkowym lub złośliwym usunięciem.

- [Jak włączyć Soft-Delete w Key Vault](../storage/blobs/soft-delete-blob-overview.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

## <a name="incident-response"></a>Reagowanie na zdarzenia

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: reagowanie na zdarzenia](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1. Tworzenie przewodnika po reagowaniu na zdarzenia

**Wskazówka**: Utwórz przewodnik odpowiedzi na zdarzenia dla swojej organizacji. Upewnij się, że istnieją zarejestrowane plany reakcji na zdarzenia, które definiują wszystkie role pracowników, a także etapy obsługi zdarzeń/zarządzania od wykrywania do oceny po zdarzeniu.

- [Jak skonfigurować automatyzacje przepływu pracy w Azure Security Center](../security-center/security-center-planning-and-operations-guide.md)

- [Wskazówki dotyczące tworzenia własnego procesu reagowania na zdarzenia zabezpieczeń](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Centrum zabezpieczeń firmy Microsoft Anatomy of an Incident (Anatomia zdarzenia)](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Przewodnik obsługi zdarzeń zabezpieczeń komputera NIST](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2. Tworzenie procedury oceniania i ustalania priorytetów zdarzeń

**Wskazówki:** Azure Security Center przypisuje ważność do każdego alertu, aby ułatwić określanie priorytetów alertów, które powinny być badane w pierwszej kolejności. Ważność jest oparta na tym, jak pewna Security Center znajduje się w znajdowaniu lub analizie użytej do wystawienia alertu, a także na poziomie pewności, że za działaniem, które doprowadziło do tego alertu, znajduje się złośliwy zamiar.

Ponadto oznacz subskrypcje przy użyciu tagów i utwórz system nazewnictwa w celu identyfikowania i kategoryzowania zasobów platformy Azure, szczególnie tych przetwarzających poufne dane.  Twoim zadaniem jest ustalanie priorytetów korygowania alertów na podstawie krytyczności zasobów platformy Azure i środowiska, w którym wystąpiło zdarzenie. 

- [Alerty zabezpieczeń w Centrum zabezpieczeń Azure](../security-center/security-center-alerts-overview.md) 

- [Organizowanie zasobów platformy Azure przy użyciu tagów](/azure/azure-resource-manager/resource-group-using-tags)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="103-test-security-response-procedures"></a>10.3: Testowanie procedur reagowania na zabezpieczenia

**Wskazówki:** Przećwiczyj ćwiczenia, aby regularnie testować możliwości reagowania na zdarzenia w systemach. Zidentyfikuj słabe punkty i przerwy oraz popraw plan zgodnie z wymaganiami.

- [Publikacja NIST — Przewodnik po programach testowych, szkoleniowych i szkoleniowych dla planów i możliwości IT](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4. Podaj szczegóły kontaktowe zdarzenia zabezpieczeń i skonfiguruj powiadomienia o alertach dla zdarzeń zabezpieczeń

**Wskazówki:** informacje kontaktowe dotyczące zdarzenia zabezpieczeń będą używane przez firmę Microsoft do kontaktowania się z Tobą, jeśli Centrum zabezpieczeń firmy Microsoft (MSRC) odkryje, że dostęp do danych klienta został uzyskany przez niedozwoloną lub niedozwoloną stronę. Przejrzyj zdarzenia po fakcie, aby upewnić się, że problemy zostały rozwiązane.

- [Jak ustawić kontakt zabezpieczeń usługi Azure Security Center](../security-center/security-center-provide-security-contact-details.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5. Dołączanie alertów zabezpieczeń do systemu reagowania na zdarzenia

**Wskazówki:** eksportowanie alertów Azure Security Center i zaleceń przy użyciu funkcji eksportu ciągłego. Eksport ciągły umożliwia eksportowanie alertów i zaleceń ręcznie lub w sposób ciągły. Możesz użyć łącznika danych Azure Security Center, aby przesyłać strumieniowo alerty do Azure Sentinel.

- [Jak skonfigurować eksport ciągły](../security-center/continuous-export.md)

- [Jak przesłać strumieniowo alerty do usługi Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatyzowanie odpowiedzi na alerty zabezpieczeń

**Wskazówki:** Użyj funkcji automatyzacji przepływu pracy w Azure Security Center, aby automatycznie wyzwalać odpowiedzi za pośrednictwem Logic Apps alertów zabezpieczeń i zaleceń.

- [Jak skonfigurować automatyzację przepływu pracy i Logic Apps](../security-center/workflow-automation.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

## <a name="penetration-tests-and-red-team-exercises"></a>Testy penetracyjne i ćwiczenia typu „red team”

*Aby uzyskać więcej informacji, zobacz [test porównawczy zabezpieczeń platformy Azure: testy penetracyjne i ćwiczenia zespołu red team.](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1. Przeprowadzanie regularnych testów penetracyjnych zasobów platformy Azure i zapewnianie korygowania wszystkich krytycznych ustaleń zabezpieczeń

**Wskazówki:** postępuj zgodnie z regułami testowania penetracyjne firmy Microsoft, aby upewnić się, że testy penetracyjne nie naruszały zasad firmy Microsoft. Korzystaj ze strategii firmy Microsoft i wykonywania testów typu „red team” i testów na żywo w zarządzanej przez firmę Microsoft infrastrukturze, usługach i aplikacjach w chmurze. 

- [Reguły testów penetracyjnych zaangażowania](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Testy typu „red team” w chmurze firmy Microsoft](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Odpowiedzialność**: Współużytkowane

**Azure Security Center monitorowania:** Brak

## <a name="next-steps"></a>Następne kroki

- Zobacz [Omówienie testu porównawczego zabezpieczeń platformy Azure w wersji 2](/azure/security/benchmarks/overview)
- Dowiedz się więcej o [punktach odniesienia zabezpieczeń platformy Azure](/azure/security/benchmarks/security-baselines-overview)
