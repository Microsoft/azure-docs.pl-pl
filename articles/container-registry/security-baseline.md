---
title: Podstawa zabezpieczeń platformy Azure dla Azure Container Registry
description: Podstawa zabezpieczeń platformy Azure dla Azure Container Registry
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 52cc67b1160ae8c9a0120a0a10d0131b167d5776
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98202678"
---
# <a name="azure-security-baseline-for-azure-container-registry"></a>Podstawa zabezpieczeń platformy Azure dla Azure Container Registry

Podstawą zabezpieczeń platformy Azure dla Azure Container Registry są zalecenia, które pomogą ulepszyć stan bezpieczeństwa wdrożenia.

Punkt odniesienia dla tej usługi jest rysowany w [wersji 1,0 usługi Azure Security test](../security/benchmarks/overview.md), która zawiera zalecenia dotyczące sposobu zabezpieczania rozwiązań w chmurze na platformie Azure z naszymi najlepszymi wskazówkami.

Aby uzyskać więcej informacji, zobacz [podstawy zabezpieczeń platformy Azure — omówienie](../security/benchmarks/security-baselines-overview.md).

## <a name="network-security"></a>Bezpieczeństwo sieci

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: zabezpieczenia sieci](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1,1: Ochrona zasobów przy użyciu sieciowych grup zabezpieczeń lub zapory platformy Azure na Virtual Network

**Wskazówki**: usługa Azure Virtual Network zapewnia bezpieczną i prywatną sieć dla zasobów platformy Azure i lokalnych. Ograniczając dostęp do prywatnego rejestru kontenerów platformy Azure z sieci wirtualnej platformy Azure, upewnij się, że tylko zasoby w sieci wirtualnej mają dostęp do rejestru. W przypadku scenariuszy obejmujących wiele lokalizacji można także skonfigurować reguły zapory w taki sposób, aby zezwalały na dostęp do rejestru tylko z określonych adresów IP. Z poziomu zapory Skonfiguruj reguły dostępu zapory i Tagi usług, aby uzyskać dostęp do rejestru kontenerów.

Ograniczanie dostępu do usługi Azure Container Registry przy użyciu sieci wirtualnej platformy Azure lub reguł zapory: https://docs.microsoft.com/azure/container-registry/container-registry-vnet 

Skonfiguruj reguły dostępu do usługi Azure Container Registry za zaporą: https://docs.microsoft.com/azure/container-registry/container-registry-firewall-access-rules


**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1,2: Monitoruj i Rejestruj konfigurację oraz ruch sieci wirtualnych, podsieci i kart sieciowych

**Wskazówki**: Użyj Azure Security Center i skoryguj zalecenia dotyczące ochrony sieci, aby pomóc w ochronie zasobów sieciowych na platformie Azure. Włącz dzienniki przepływu sieciowej grupy zabezpieczeń i Wyślij dzienniki do konta magazynu na potrzeby inspekcji ruchu.

Jak włączyć dzienniki przepływu sieciowej grupy zabezpieczeń: https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Chroń zasoby sieci: https://docs.microsoft.com/azure/security-center/security-center-network-recommendations



**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="13-protect-critical-web-applications"></a>1,3: Ochrona krytycznych aplikacji sieci Web

**Wskazówki**: nie dotyczy. Test porównawczy jest przeznaczony do Azure App Service lub zasobów obliczeniowych obsługujących aplikacje sieci Web.

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: odmowa komunikacji ze znanymi złośliwymi adresami IP

**Wskazówki**: Włącz ochronę standardową DDoS w sieciach wirtualnych w celu ochrony przed atakami DDoS. Użyj Azure Security Center zintegrowanej analizy zagrożeń, aby odmówić komunikacji ze znanymi złośliwymi lub nieużywanymi adresami IP.  Wdróż zaporę platformy Azure w każdej z granic sieci organizacji z włączoną analizą zagrożeń i skonfigurowaną jako "Alert i Odmów" w celu uzyskania złośliwego ruchu sieciowego.

Możesz użyć Azure Security Center dostęp do sieci w czasie w celu skonfigurowania sieciowych grup zabezpieczeń w celu ograniczenia ekspozycji punktów końcowych na zatwierdzone adresy IP przez ograniczony okres. Należy również użyć opcji Azure Security Center adaptacyjnej ochrony sieci, aby zalecać konfiguracje sieciowej grupy zabezpieczeń, które ograniczają porty i źródłowe adresy IP na podstawie rzeczywistego ruchu i analizy zagrożeń.

Jak skonfigurować ochronę DDoS:  https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

Jak wdrożyć zaporę platformy Azure: https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

Opis Azure Security Center zintegrowanej analizy zagrożeń: https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer

Informacje o Azure Security Center adaptacyjnej ograniczania przepustowości sieci: https://docs.microsoft.com/azure/security-center/security-center-adaptive-network-hardening

Azure Security Center Access Control sieci just in Time: https://docs.microsoft.com/azure/security-center/security-center-just-in-time


**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="15-record-network-packets-and-flow-logs"></a>1,5: rejestrowanie pakietów sieciowych i dzienników przepływów

**Wskazówki**: Włączanie dzienników przepływu sieciowych grup zabezpieczeń (sieciowej grupy zabezpieczeń) dla sieciowej grupy zabezpieczeń podłączonych do podsieci używanej do ochrony rejestru kontenerów platformy Azure. Dzienniki przepływu sieciowej grupy zabezpieczeń można zarejestrować na koncie usługi Azure Storage w celu wygenerowania rekordów przepływu. Jeśli jest to wymagane do badania nietypowego działania, Włącz funkcję przechwytywania pakietów Network Watcher platformy Azure.

Jak włączyć dzienniki przepływu sieciowej grupy zabezpieczeń: https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Jak włączyć Network Watcher: https://docs.microsoft.com/azure/network-watcher/network-watcher-create


**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: Wdróż systemy zapobiegania wykrywaniu dostępu do sieci/dostępu intruzów (identyfikatory/adresy IP)

**Wskazówki**: wybierz ofertę z portalu Azure Marketplace, która obsługuje funkcje identyfikatorów/adresów IP z możliwościami inspekcji ładunku. Jeśli wykrywanie i/lub zapobieganie włamaniu nie są wymagane w oparciu o inspekcję ładunku, można użyć zapory platformy Azure z funkcją analizy zagrożeń. Filtrowanie oparte na analizie zagrożeń na platformie Azure może wysyłać alerty i odrzucać ruch do i ze znanych złośliwych adresów IP i domen. Adresy IP i domeny pochodzą z kanału informacyjnego analizy zagrożeń firmy Microsoft.

Wdrażaj wybrane rozwiązanie zapory w każdej z granic sieci organizacji, aby wykrywać i/lub odrzucać złośliwy ruch.

Portal Azure Marketplace:  https://azuremarketplace.microsoft.com/marketplace/?term=Firewall 

Jak wdrożyć zaporę platformy Azure: https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

Jak skonfigurować alerty za pomocą zapory platformy Azure: https://docs.microsoft.com/azure/firewall/threat-intel


**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="17-manage-traffic-to-web-applications"></a>1,7: zarządzanie ruchem do aplikacji sieci Web

**Wskazówki**: nie dotyczy. Test porównawczy jest przeznaczony dla aplikacji sieci Web działających na Azure App Service lub zasobów obliczeniowych.

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: Minimalizacja złożoności i kosztów administracyjnych reguł zabezpieczeń sieci

**Wskazówki**: dla zasobów, które wymagają dostępu do rejestru kontenerów, użyj tagów usługi sieci wirtualnej dla usługi Azure Container Registry, aby zdefiniować kontrolę dostępu do sieci dla sieciowych grup zabezpieczeń lub zapory platformy Azure. Podczas tworzenia reguł zabezpieczeń można użyć tagów usługi zamiast konkretnych adresów IP. Określając nazwę tagu usługi "AzureContainerRegistry" w odpowiednim polu źródłowym lub docelowym reguły, można zezwolić na ruch dla odpowiedniej usługi lub go odrzucić. Firma Microsoft zarządza prefiksami adresów, które obejmują tag usługi, i automatycznie aktualizuje tag usługi jako adresy.

Zezwalaj na dostęp według tagu usługi: https://docs.microsoft.com/azure/container-registry/container-registry-firewall-access-rules#allow-access-by-service-tag


**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: Obsługa standardowych konfiguracji zabezpieczeń dla urządzeń sieciowych

**Wskazówki**: Definiowanie i implementowanie standardowych konfiguracji zabezpieczeń dla zasobów sieciowych skojarzonych z rejestrami kontenerów platformy Azure za pomocą Azure Policy. Użyj aliasów Azure Policy w przestrzeniach nazw "Microsoft. ContainerRegistry" i "Microsoft. Network", aby utworzyć niestandardowe zasady inspekcji lub wymuszania konfiguracji sieciowej rejestrów kontenerów. 

Plany platformy Azure mogą służyć do uproszczenia wdrożeń platformy Azure na dużą skalę przez pakowanie kluczowych artefaktów, takich jak szablony Azure Resource Manager, kontrolki RBAC platformy Azure i zasady, w ramach jednej definicji planu. Łatwo stosuj plan do nowych subskrypcji i Dostosuj kontrolę i zarządzanie za pomocą wersji.

Przeprowadź inspekcję zgodności rejestrów kontenerów platformy Azure przy użyciu Azure Policy:  https://docs.microsoft.com/azure/container-registry/container-registry-azure-policy

Jak utworzyć Azure Blueprint: https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal


**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="110-document-traffic-configuration-rules"></a>1,10: udokumentowanie reguł konfiguracji ruchu

**Wskazówki**: Klient może używać planów platformy Azure, aby uprościć wdrożenia platformy Azure na dużą skalę przez pakowanie kluczowych artefaktów środowiska, takich jak szablony Azure Resource Manager, formanty RBAC platformy Azure i zasady, w ramach jednej definicji planu. Łatwo stosuj plan do nowych subskrypcji i Dostosuj kontrolę i zarządzanie za pomocą wersji.

Jak utworzyć Azure Blueprint: https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal



**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: Użyj zautomatyzowanych narzędzi do monitorowania konfiguracji zasobów sieciowych i wykrywania zmian

**Wskazówki**: Użyj dziennika aktywności platformy Azure do monitorowania konfiguracji zasobów sieciowych i wykrywania zmian zasobów sieciowych związanych z rejestrami kontenerów. Tworzenie alertów w ramach Azure Monitor, które będą wyzwalane po wprowadzeniu zmian w krytycznych zasobach sieciowych.

Jak wyświetlać i pobierać zdarzenia dziennika aktywności platformy Azure:  https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view

Jak utworzyć alerty w Azure Monitor:  https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log



**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

## <a name="logging-and-monitoring"></a>Rejestrowanie i monitorowanie

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: rejestrowanie i monitorowanie](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2,1: Użyj źródeł synchronizacji zatwierdzonego czasu

**Wskazówki**: Firma Microsoft przechowuje źródła czasu dla zasobów platformy Azure, ale istnieje możliwość zarządzania ustawieniami synchronizacji czasu dla zasobów obliczeniowych.

Jak skonfigurować synchronizację czasu dla zasobów obliczeniowych platformy Azure: https://docs.microsoft.com/azure/virtual-machines/windows/time-sync


**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2,2: Skonfiguruj centralne zarządzanie dziennikami zabezpieczeń

**Wskazówki**: pobieranie dzienników przy użyciu Azure monitor do agregowania danych zabezpieczeń wygenerowanych przez usługę Azure Container Registry. W Azure Monitor należy używać Log Analytics obszarów roboczych do wykonywania zapytań i wykonywania analiz oraz używania kont usługi Azure Storage do przechowywania długoterminowego/archiwizowania.

Dzienniki Azure Container Registry na potrzeby oceny i inspekcji diagnostyki:  https://docs.microsoft.com/azure/container-registry/container-registry-diagnostics-audit-logs



**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: Włączanie rejestrowania inspekcji dla zasobów platformy Azure

**Wskazówki**: Azure monitor zbiera dzienniki zasobów (dawniej nazywane dziennikami diagnostycznymi) dla zdarzeń sterowanych przez użytkownika w rejestrze. Zbieraj te dane i korzystaj z nich w celu przeprowadzania inspekcji zdarzeń związanych z uwierzytelnianiem rejestru i zapewniają kompletną aktywność w przypadku artefaktów rejestru, takich jak zdarzenia ściągania i wypychania, aby zdiagnozować problemy z zabezpieczeniami w rejestrze.

Dzienniki Azure Container Registry na potrzeby oceny i inspekcji diagnostyki: https://docs.microsoft.com/azure/container-registry/container-registry-diagnostics-audit-logs


**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: Zbierz dzienniki zabezpieczeń z systemów operacyjnych

**Wskazówki**: nie dotyczy. Testy porównawcze są przeznaczone do zasobów obliczeniowych.

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="25-configure-security-log-storage-retention"></a>2,5: Konfigurowanie przechowywania magazynu dzienników zabezpieczeń

**Wskazówki**: w obszarze Azure monitor Ustaw okres przechowywania log Analytics obszaru roboczego zgodnie z regulacjami zgodności w organizacji. Używaj kont usługi Azure Storage do przechowywania długoterminowego/archiwizowania.

Jak ustawić parametry przechowywania dziennika dla obszarów roboczych Log Analytics: https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period


**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="26-monitor-and-review-logs"></a>2,6: dzienniki monitorowania i przeglądania

**Wskazówki**: analizowanie i monitorowanie dzienników Azure Container Registry pod kątem nietypowego zachowania i regularnego przeglądania wyników. Użyj Azure Monitor Log Analytics obszaru roboczego do przeglądania dzienników i wykonywania zapytań dotyczących danych dziennika.

Dzienniki Azure Container Registry na potrzeby oceny i inspekcji diagnostyki:  https://docs.microsoft.com/azure/container-registry/container-registry-diagnostics-audit-logs

Informacje o obszarze roboczym Log Analytics: https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal

Jak wykonywać niestandardowe zapytania w Azure Monitor: https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries


**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="27-enable-alerts-for-anomalous-activity"></a>2,7: Włączanie alertów dla nietypowego działania

**Wskazówki**: używanie obszaru roboczego usługi Azure log Analytics do monitorowania i generowania alertów dotyczących nietypowych działań w dziennikach zabezpieczeń i zdarzeniach związanych z usługą Azure Container Registry.

Dzienniki Azure Container Registry na potrzeby oceny i inspekcji diagnostyki: https://docs.microsoft.com/azure/container-registry/container-registry-diagnostics-audit-logs

Jak ostrzec dane dziennika usługi log Analytics:  https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response


**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="28-centralize-anti-malware-logging"></a>2,8: scentralizowanie rejestrowania chroniącego przed złośliwym oprogramowaniem

**Wskazówki**: nie dotyczy. Azure Container Registry nie przetwarza ani nie tworzy dzienników związanych z oprogramowaniem chroniącym przed złośliwym kodem.


**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="29-enable-dns-query-logging"></a>2,9: Włączanie rejestrowania zapytań DNS

**Wskazówki**: nie dotyczy. Azure Container Registry jest punktem końcowym i nie inicjuje komunikacji, a usługa nie bada usługi DNS.

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="210-enable-command-line-audit-logging"></a>2,10: Włączanie rejestrowania inspekcji w wierszu polecenia

**Wskazówki**: nie dotyczy. Testy porównawcze są przeznaczone do zasobów obliczeniowych.


**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

## <a name="identity-and-access-control"></a>Tożsamość i kontrola dostępu

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: tożsamość i Access Control](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: obsługa spisu kont administracyjnych

**Wskazówki**: Azure Active Directory (Azure AD) ma wbudowane role, które muszą być jawnie przypisane i są queryable. Za pomocą modułu Azure AD PowerShell można wykonywać zapytania ad hoc w celu odnajdywania kont należących do grup administracyjnych.

W przypadku każdego rejestru kontenerów platformy Azure należy śledzić, czy wbudowane konto administratora jest włączone, czy wyłączone. Wyłącz konto, gdy nie jest używane.

Jak uzyskać rolę katalogu w usłudze Azure AD przy użyciu programu PowerShell: https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0

Jak uzyskać członków roli katalogu w usłudze Azure AD przy użyciu programu PowerShell: https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0

Azure Container Registry konto administratora:  https://docs.microsoft.com/azure/container-registry/container-registry-authentication#admin-account


**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="32-change-default-passwords-where-applicable"></a>3,2: Zmień domyślne hasła, jeśli ma to zastosowanie

**Wskazówki**: Azure Active Directory (Azure AD) nie ma koncepcji domyślnych haseł. Inne zasoby platformy Azure wymagające hasła wymuszają utworzenie hasła przy użyciu wymagań dotyczących złożoności i minimalnej długości hasła, które różnią się w zależności od usługi. Użytkownik jest odpowiedzialny za aplikacje innych firm i usługi Marketplace, które mogą korzystać z domyślnych haseł.

Jeśli jest włączone domyślne konto administratora usługi Azure Container Registry, złożone hasła są tworzone automatycznie i powinny być obracane. Wyłącz konto, gdy nie jest używane.

Azure Container Registry konto administratora: https://docs.microsoft.com/azure/container-registry/container-registry-authentication#admin-account



**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: Użyj dedykowanych kont administracyjnych

**Wskazówki**: Tworzenie standardowych procedur operacyjnych dotyczących korzystania z dedykowanych kont administracyjnych. Użyj Azure Security Center Zarządzanie tożsamościami i dostępem, aby monitorować liczbę kont administracyjnych.

Ponadto Utwórz procedury umożliwiające włączenie wbudowanego konta administratora rejestru kontenerów. Wyłącz konto, gdy nie jest używane.

Informacje o tożsamości i dostępie Azure Security Center:  https://docs.microsoft.com/azure/security-center/security-center-identity-access

Azure Container Registry konto administratora:  https://docs.microsoft.com/azure/container-registry/container-registry-authentication#admin-account



**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3,4: Korzystaj z logowania jednokrotnego (SSO) z usługą Azure Active Directory

**Wskazówki**: wszędzie tam, gdzie to możliwe, użyj Azure Active Directory rejestracji jednokrotnej, zamiast konfigurować indywidualne poświadczenia autonomiczne dla poszczególnych usług. Użyj Azure Security Center zalecenia dotyczące zarządzania tożsamościami i dostępem.

Aby uzyskać dostęp do rejestru kontenerów, należy użyć pojedynczej nazwy logowania zintegrowanej z Azure Active Directory.

Opis logowania jednokrotnego w usłudze Azure AD:  https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on

Indywidualne logowanie do rejestru kontenerów:  https://docs.microsoft.com/azure/container-registry/container-registry-authentication#individual-login-with-azure-ad


**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Użyj uwierzytelniania wieloskładnikowego, aby uzyskać dostęp oparty na Azure Active Directory

**Wskazówki**: Włączanie Azure Active Directory (Azure AD) uwierzytelnianie wieloskładnikowe (MFA) i przestrzeganie Azure Security Center zalecenia dotyczące zarządzania tożsamościami i dostępem.

Jak włączyć usługę MFA na platformie Azure: https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Jak monitorować tożsamość i dostęp w Azure Security Center:  https://docs.microsoft.com/azure/security-center/security-center-identity-access


**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: Używaj dedykowanych maszyn (uprzywilejowany dostęp do stacji roboczych) dla wszystkich zadań administracyjnych

**Wskazówki**: Użyj dostępem uprzywilejowanym (uprzywilejowanych stacji roboczych dostępu) za pomocą usługi MFA skonfigurowanej do logowania się i konfigurowania zasobów platformy Azure.

Dowiedz się więcej o stacjach roboczych uprzywilejowanego dostępu:  https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/

Jak włączyć usługę MFA na platformie Azure: https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted


**Monitorowanie Azure Security Center**: brak

**Odpowiedzialność**: Klient

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3,7: dziennik i alert dotyczący podejrzanego działania z kont administracyjnych

**Wskazówki**: Użyj raportów zabezpieczeń usługi Azure Active Directory (Azure AD) na potrzeby generowania dzienników i alertów w przypadku wystąpienia podejrzanych lub niebezpiecznych działań w środowisku. Użyj Azure Security Center, aby monitorować działania związane z tożsamościami i dostępem.

Identyfikowanie użytkowników usługi Azure AD oflagowanych w celu działania ryzykownego:  https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk

Jak monitorować działania związane z tożsamością i dostępem użytkowników w Azure Security Center:  https://docs.microsoft.com/azure/security-center/security-center-identity-access


**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: zarządzanie zasobami platformy Azure tylko z zatwierdzonych lokalizacji

**Wskazówki**: Użyj dostępu warunkowego o nazwie Locations, aby zezwolić na dostęp tylko do określonych logicznych grup zakresów adresów IP lub krajów/regionów.

Jak skonfigurować nazwane lokalizacje na platformie Azure:  https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations


**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="39-use-azure-active-directory"></a>3,9: Użyj Azure Active Directory

**Wskazówki**: Użyj Azure Active Directory (Azure AD) jako centralnego systemu uwierzytelniania i autoryzacji. Usługa Azure AD chroni dane przy użyciu silnego szyfrowania danych przechowywanych i przesyłanych. Usługa Azure AD również Sole, skróty i bezpieczne przechowywanie poświadczeń użytkownika.

Jak utworzyć i skonfigurować wystąpienie usługi Azure AD: https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant


**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: regularnie Przeglądaj i Uzgodnij dostęp użytkowników

**Wskazówki**: Azure Active Directory (Azure AD) zawiera dzienniki ułatwiające wykrywanie starych kont. Ponadto za pomocą przeglądów dostępu do tożsamości platformy Azure można efektywnie zarządzać członkostwem w grupach, dostępem do aplikacji dla przedsiębiorstw i przypisaniami ról. Dostęp użytkowników może być regularnie przeglądany, aby upewnić się, że tylko Ci użytkownicy mają ciągły dostęp.

Informacje o raportowaniu usługi Azure AD:  https://docs.microsoft.com/azure/active-directory/reports-monitoring/

Jak korzystać z przeglądów dostępu do tożsamości platformy Azure:  https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview



**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3,11: Monitor próbuje uzyskać dostęp do zdezaktywowanych kont

**Wskazówki**: masz dostęp do źródeł danych dotyczących logowania Azure Active Directory (Azure AD), inspekcji i zdarzeń związanych z ryzykiem, które umożliwiają integrację z dowolnymi narzędziami do zarządzania informacjami i zdarzeniami zabezpieczeń (Siem).

Proces ten można usprawnić, tworząc ustawienia diagnostyczne dla Azure Active Directory kont użytkowników i wysyłając dzienniki inspekcji i dzienniki logowania do obszaru roboczego Log Analytics. Żądane alerty można skonfigurować w obszarze roboczym Log Analytics.

Jak zintegrować dzienniki aktywności platformy Azure z Azure Monitor:  https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics


**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: odchylenia zachowania podczas logowania do konta

**Wskazówki**: Użyj funkcji ryzyka i ochrony tożsamości Azure Active Directory (Azure AD), aby skonfigurować automatyczne odpowiedzi na wykryte podejrzane działania związane z tożsamościami użytkowników. 

Jak wyświetlić ryzykowne logowania usługi Azure AD: https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

Jak skonfigurować i włączyć zasady dotyczące ryzyka związanego z ochroną tożsamości: https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies


**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: Zapewnij firmie Microsoft dostęp do odpowiednich danych klienta w scenariuszach pomocy technicznej

**Wskazówki**: niedostępne; Skrytka klienta nie jest obecnie obsługiwane dla Azure Container Registry.

Lista obsługiwanych usług Skrytka klienta: https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability



**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

## <a name="data-protection"></a>Ochrona danych

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Ochrona danych](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: przechowywanie spisu poufnych informacji

**Wskazówki**: Użyj tagów zasobów, aby pomóc w śledzeniu rejestrów kontenerów platformy Azure, które przechowują lub przetwarzają informacje poufne.

Obrazy kontenerów i wersji oraz inne artefakty w rejestrze oraz Zablokuj obrazy lub repozytoria, aby pomóc w śledzeniu obrazów, które przechowują lub przetwarzają informacje poufne.

Tworzenie i używanie tagów:  https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Zalecenia dotyczące tagowania i przechowywania wersji obrazów kontenerów:  https://docs.microsoft.com/azure/container-registry/container-registry-image-tag-version

Zablokuj obraz kontenera w usłudze Azure Container Registry:  https://docs.microsoft.com/azure/container-registry/container-registry-image-lock



**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: Izoluj systemy przechowujące lub przetwarzające informacje poufne

**Wskazówki**: implementowanie oddzielnych rejestrów kontenerów, subskrypcji i/lub grup zarządzania na potrzeby tworzenia, testowania i produkcji. Zasoby przechowujące lub przetwarzające dane poufne powinny być wystarczająco odizolowane.

Zasoby powinny być oddzielone przez sieć wirtualną lub podsieć, odpowiednio otagowane i zabezpieczone przez grupę zabezpieczeń sieci (sieciowej grupy zabezpieczeń) lub zaporę platformy Azure.

Jak utworzyć dodatkowe subskrypcje platformy Azure:  https://docs.microsoft.com/azure/billing/billing-create-subscription

Tworzenie grup zarządzania:  https://docs.microsoft.com/azure/governance/management-groups/create

Tworzenie i używanie tagów: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Ograniczanie dostępu do usługi Azure Container Registry przy użyciu sieci wirtualnej platformy Azure lub reguł zapory: https://docs.microsoft.com/azure/container-registry/container-registry-vnet

Jak utworzyć sieciowej grupy zabezpieczeń z konfiguracją zabezpieczeń: https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

Jak wdrożyć zaporę platformy Azure:

https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

Jak skonfigurować alert lub alert i odmówić przy użyciu zapory platformy Azure:

https://docs.microsoft.com/azure/firewall/threat-intel



**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: Monitoruj i blokuj nieautoryzowany transfer informacji poufnych

**Wskazówki**: wdrażanie zautomatyzowanego narzędzia na obrzeżach sieci, które monitoruje do nieautoryzowanego transferu poufnych informacji i blokuje takie transfery podczas powiadamiania specjalistów ds. bezpieczeństwa informacji.

W przypadku podstawowej platformy zarządzanej przez firmę Microsoft Firma Microsoft traktuje całą zawartość klienta jako poufną i nadaje im dużą długość, aby chronić przed utratą i narażeniem danych przez klienta. Aby zapewnić bezpieczeństwo danych klienta na platformie Azure, firma Microsoft wdrożyła i utrzymuje pakiet niezawodnych kontroli i możliwości ochrony danych.

Zrozumienie ochrony danych klientów na platformie Azure:  https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data


**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Współużytkowane

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: Szyfruj wszystkie poufne informacje podczas przesyłania

**Wskazówki**: Upewnij się, że wszyscy klienci łączący się z Azure Container Registry mogą negocjować protokół TLS 1,2 lub nowszy. Zasoby Microsoft Azure domyślnie negocjują TLS 1,2.

Postępuj zgodnie z zaleceniami Azure Security Center na potrzeby szyfrowania w czasie spoczynku i szyfrowania podczas przesyłania, jeśli ma to zastosowanie.

Informacje o szyfrowaniu podczas przesyłania na platformę Azure:  https://docs.microsoft.com/azure/security/fundamentals/encryption-overview#encryption-of-data-in-transit



**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Współużytkowane

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: Użyj aktywnego narzędzia do odnajdywania, aby identyfikować poufne dane

**Wskazówki**: funkcje identyfikacji, klasyfikacji i zapobiegania utracie danych nie są jeszcze dostępne dla Azure Container Registry. Zaimplementuj rozwiązanie innych firm, jeśli jest wymagane na potrzeby zgodności.

W przypadku podstawowej platformy zarządzanej przez firmę Microsoft Firma Microsoft traktuje całą zawartość klienta jako poufną i nadaje im dużą długość, aby chronić przed utratą i narażeniem danych przez klienta. Aby zapewnić bezpieczeństwo danych klienta na platformie Azure, firma Microsoft wdrożyła i utrzymuje pakiet niezawodnych kontroli i możliwości ochrony danych.

Zrozumienie ochrony danych klientów na platformie Azure: https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data


**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Współużytkowane

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: korzystanie z usługi Azure RBAC do kontrolowania dostępu do zasobów

**Wskazówki**: Funkcja kontroli dostępu opartej na rolach (Azure RBAC) umożliwia kontrolowanie dostępu do danych i zasobów w usłudze Azure Container Registry. 

Jak skonfigurować usługę Azure RBAC:  https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal

Azure Container Registry ról i uprawnień:  https://docs.microsoft.com/azure/container-registry/container-registry-roles



**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: Wymuś kontrolę dostępu przy użyciu ochrony przed utratą danych opartą na hoście

**Wskazówki**: Jeśli jest to wymagane dla zgodności zasobów obliczeniowych, zaimplementuj narzędzie innej firmy, takie jak automatyczne rozwiązanie do ochrony przed utratą danych oparte na hoście, aby wymusić kontrolę dostępu do danych nawet wtedy, gdy dane są kopiowane poza system.

W przypadku podstawowej platformy zarządzanej przez firmę Microsoft Firma Microsoft traktuje całą zawartość klienta jako poufną i nadaje im dużą długość, aby chronić przed utratą i narażeniem danych przez klienta. Aby zapewnić bezpieczeństwo danych klienta na platformie Azure, firma Microsoft wdrożyła i utrzymuje pakiet niezawodnych kontroli i możliwości ochrony danych.

Zrozumienie ochrony danych klientów na platformie Azure:  https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data


**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Współużytkowane

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: Szyfruj poufne informacje w spoczynku

**Wskazówki**: szyfrowanie na wszystkich zasobach platformy Azure. Domyślnie wszystkie dane w usłudze Azure Container Registry są szyfrowane w stanie spoczynku przy użyciu kluczy zarządzanych przez firmę Microsoft.

Informacje o szyfrowaniu na platformie Azure: https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest

Klucze zarządzane przez klienta w Azure Container Registry:  https://aka.ms/acr/cmk



**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: Rejestruj i Ostrzegaj o zmianach krytycznych zasobów platformy Azure

**Wskazówki**: Azure monitor zbiera dzienniki zasobów (dawniej nazywane dziennikami diagnostycznymi) dla zdarzeń sterowanych przez użytkownika w rejestrze. Zbieraj te dane i korzystaj z nich w celu przeprowadzania inspekcji zdarzeń związanych z uwierzytelnianiem rejestru i zapewniają kompletną aktywność dla artefaktów rejestru, takich jak zdarzenia ściągnięcia i ściągania, dzięki czemu można zdiagnozować problemy operacyjne z rejestrem.

Dzienniki Azure Container Registry na potrzeby oceny i inspekcji diagnostyki: https://docs.microsoft.com/azure/container-registry/container-registry-diagnostics-audit-logs


**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

## <a name="vulnerability-management"></a>Zarządzanie lukami w zabezpieczeniach

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Zarządzanie lukami w zabezpieczeniach](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: uruchamianie narzędzi do skanowania automatycznych luk w zabezpieczeniach

**Wskazówki**: Postępuj zgodnie z zaleceniami Azure Security Center na temat przeprowadzania ocen luk w zabezpieczeniach na obrazach kontenerów. Opcjonalnie Wdrażaj rozwiązania innych firm z witryny Azure Marketplace w celu przeprowadzenia oceny luk w zabezpieczeniach obrazu.

Jak zaimplementować zalecenia dotyczące oceny luk w zabezpieczeniach Azure Security Center:  https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations

Integracja Azure Container Registry z Security Center (wersja zapoznawcza):  https://docs.microsoft.com/azure/security-center/azure-container-registry-integration



**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: Wdróż automatyczne rozwiązanie do zarządzania poprawkami systemu operacyjnego

**Wskazówki**: Firma Microsoft wykonuje zarządzanie poprawkami w podstawowych systemach, które obsługują Azure Container Registry.

Automatyzuj aktualizacje obrazów kontenerów w przypadku wykrycia aktualizacji obrazów podstawowych z systemu operacyjnego i innych poprawek.

Informacje o aktualizacjach obrazu podstawowego dla Azure Container Registry zadań:  https://docs.microsoft.com/azure/container-registry/container-registry-tasks-base-images


**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5,3: Wdróż zautomatyzowane rozwiązanie do zarządzania poprawkami oprogramowania innych firm

**Wskazówki**: możesz użyć rozwiązania innej firmy, aby zastosować poprawki do obrazów aplikacji.  Ponadto można uruchamiać zadania Azure Container Registry, aby zautomatyzować aktualizacje obrazów aplikacji w rejestrze kontenerów na podstawie poprawek zabezpieczeń lub innych aktualizacji w obrazach podstawowych.

Informacje o aktualizacjach obrazu podstawowego dla zadań ACR:  https://docs.microsoft.com/azure/container-registry/container-registry-tasks-base-images



**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: porównanie luk w zabezpieczeniach z tyłu do tyłu

**Wskazówki**: Integruj Azure Container Registry (ACR) z Azure Security Center, aby umożliwić okresowe skanowanie obrazów kontenerów pod kątem luk w zabezpieczeniach. Opcjonalnie Wdrażaj rozwiązania innych firm w witrynie Azure Marketplace, aby wykonywać regularne skanowanie w poszukiwaniu luk w zabezpieczeniach obrazów.

Integracja Azure Container Registry z Security Center (wersja zapoznawcza):  https://docs.microsoft.com/azure/security-center/azure-container-registry-integration


**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: Użyj procesu oceny ryzyka, aby określić priorytety korygowania odkrytych luk w zabezpieczeniach

**Wskazówki**: Integruj Azure Container Registry (ACR) z Azure Security Center, aby umożliwić okresowe skanowanie obrazów kontenerów pod kątem luk w zabezpieczeniach oraz klasyfikowanie zagrożeń. Opcjonalnie Wdrażaj rozwiązania innych firm w witrynie Azure Marketplace, aby wykonywać regularne skanowanie luk w zabezpieczeniach obrazów i klasyfikację ryzyka.

Integracja Azure Container Registry z Security Center (wersja zapoznawcza):  https://docs.microsoft.com/azure/security-center/azure-container-registry-integration



**Monitorowanie Azure Security Center**: brak

**Odpowiedzialność**: Klient

## <a name="inventory-and-asset-management"></a>Zarządzanie magazynem i zasobami

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Spis i zarządzanie zasobami](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-azure-asset-discovery"></a>6,1: Użyj odnajdywania zasobów platformy Azure

**Wskazówki**: Użyj grafu zasobów platformy Azure do wykonywania zapytań/odnajdywania wszystkich zasobów (takich jak obliczenia, magazyn, Sieć, porty i protokoły itp.) w ramach subskrypcji.  Upewnij się, że masz odpowiednie uprawnienia (odczyt) w dzierżawie i Wylicz wszystkie subskrypcje platformy Azure oraz zasoby w ramach subskrypcji.

Mimo że klasyczne zasoby platformy Azure mogą zostać odnalezione za pośrednictwem grafu zasobów, zdecydowanie zaleca się tworzenie i używanie Azure Resource Manager zasobów do przodu.

Jak tworzyć zapytania przy użyciu grafu zasobów platformy Azure:  https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Jak wyświetlić subskrypcje platformy Azure:  https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0 

Opis kontroli RBAC platformy Azure:  https://docs.microsoft.com/azure/role-based-access-control/overview



**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="62-maintain-asset-metadata"></a>6,2: Konserwowanie metadanych zasobów

**Wskazówki**: Azure Container Registry przechowuje metadane w tym Tagi i manifesty dla obrazów w rejestrze. Postępuj zgodnie z zaleceniami zalecanymi dla tagowania artefaktów.

Informacje o rejestrach, repozytoriach i obrazach: https://docs.microsoft.com/azure/container-registry/container-registry-concepts

Zalecenia dotyczące tagowania i przechowywania wersji obrazów kontenerów: https://docs.microsoft.com/azure/container-registry/container-registry-image-tag-version


**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: Usuń nieautoryzowane zasoby platformy Azure

**Wskazówki**: Azure Container Registry przechowuje metadane w tym Tagi i manifesty dla obrazów w rejestrze. Postępuj zgodnie z zaleceniami zalecanymi dla tagowania artefaktów.

Informacje o rejestrach, repozytoriach i obrazach: https://docs.microsoft.com/azure/container-registry/container-registry-concepts

Zalecenia dotyczące tagowania i przechowywania wersji obrazów kontenerów:  https://docs.microsoft.com/azure/container-registry/container-registry-image-tag-version



**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6,4: przechowywanie spisu zatwierdzonych zasobów platformy Azure i tytułów oprogramowania

**Wskazówki**: należy utworzyć spis zatwierdzonych zasobów platformy Azure zgodnie z potrzebami organizacji.  

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: Monitoruj niezatwierdzone zasoby platformy Azure

**Wskazówki**: Użyj Azure Policy, aby umieścić ograniczenia dotyczące typu zasobów, które można utworzyć w ramach subskrypcji.

Użyj grafu zasobów platformy Azure do wykonywania zapytań/odnajdywania zasobów w ramach subskrypcji.  Upewnij się, że wszystkie zasoby platformy Azure obecne w środowisku są zatwierdzone.

Przeprowadź inspekcję zgodności rejestrów kontenerów platformy Azure przy użyciu Azure Policy:  https://docs.microsoft.com/azure/container-registry/container-registry-azure-policy

Jak skonfigurować Azure Policy i zarządzać nimi:  https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Jak tworzyć zapytania za pomocą usługi Azure Graph:  https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal


**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: Monitoruj niezatwierdzone aplikacje oprogramowania w ramach zasobów obliczeniowych

**Wskazówki**: analizowanie i monitorowanie dzienników Azure Container Registry pod kątem nietypowego zachowania i regularnego przeglądania wyników. Użyj Azure Monitor Log Analytics obszaru roboczego do przeglądania dzienników i wykonywania zapytań dotyczących danych dziennika.

Dzienniki Azure Container Registry na potrzeby oceny i inspekcji diagnostyki:  https://docs.microsoft.com/azure/container-registry/container-registry-diagnostics-audit-logs

Informacje o obszarze roboczym Log Analytics:  https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal

Jak wykonywać niestandardowe zapytania w Azure Monitor:  https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries


**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: Usuń niezatwierdzone zasoby platformy Azure i aplikacje oprogramowania

**Wskazówki**: Azure Automation zapewnia pełną kontrolę podczas wdrażania, działania i likwidowania obciążeń i zasobów.  Możesz zaimplementować własne rozwiązanie do usuwania nieautoryzowanych zasobów platformy Azure. Wprowadzenie do Azure Automation:  https://docs.microsoft.com/azure/automation/automation-intro


**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="68-use-only-approved-applications"></a>6,8: Używaj tylko zatwierdzonych aplikacji

**Wskazówki**: nie dotyczy. Test porównawczy jest przeznaczony dla zasobów obliczeniowych.


**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="69-use-only-approved-azure-services"></a>6,9: Używaj tylko zatwierdzonych usług platformy Azure

**Wskazówki**: skorzystaj z Azure Policy, aby ograniczyć usługi, które możesz udostępnić w danym środowisku.

Przeprowadź inspekcję zgodności rejestrów kontenerów platformy Azure przy użyciu Azure Policy:  https://docs.microsoft.com/azure/container-registry/container-registry-azure-policy

Jak skonfigurować Azure Policy i zarządzać nimi: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Jak odmówić określonego typu zasobu Azure Policy:  https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types



**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="610-implement-approved-application-list"></a>6,10: Zaimplementuj listę zatwierdzonych aplikacji

**Wskazówki**: nie dotyczy. Test porównawczy jest przeznaczony dla zasobów obliczeniowych.



**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="611-limit-users-ability-to-interact-with-azureresources-manager-via-scripts"></a>6,11: Ogranicz możliwość korzystania przez użytkowników z programu AzureResources Manager za pośrednictwem skryptów

**Wskazówki**: Użyj konfiguracji specyficznych dla systemu operacyjnego lub zasobów innych firm, aby ograniczyć możliwość wykonywania skryptów w ramach zasobów obliczeniowych platformy Azure.

Jak skonfigurować dostęp warunkowy, aby blokować dostęp do usługi Azure Resources:  https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management



**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6,12: Ogranicz możliwość wykonywania skryptów w zasobach obliczeniowych przez użytkowników

**Wskazówki**: Użyj określonych konfiguracji systemu operacyjnego lub zasobów innych firm, aby ograniczyć możliwość wykonywania skryptów w ramach zasobów obliczeniowych platformy Azure.

Na przykład Sterowanie wykonywaniem skryptu programu PowerShell w środowiskach systemu Windows:  https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6


**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: fizyczne lub logiczne rozdzielenie aplikacji wysokiego ryzyka

**Wskazówki**: oprogramowanie, które jest wymagane do działania biznesowego, ale może powodować większe ryzyko dla organizacji, powinno być izolowane w ramach własnej maszyny wirtualnej i/lub sieci wirtualnej i dostatecznie zabezpieczone za pomocą zapory platformy Azure lub sieciowej grupy zabezpieczeń.

Jak utworzyć sieć wirtualną:  https://docs.microsoft.com/azure/virtual-network/quick-create-portal

Jak utworzyć sieciowej grupy zabezpieczeń z konfiguracją zabezpieczeń:  https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic


**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

## <a name="secure-configuration"></a>Bezpieczna konfiguracja

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: bezpieczna konfiguracja](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: Ustanów bezpieczne konfiguracje dla wszystkich zasobów platformy Azure

**Wskazówki**: Użyj Azure Policy lub Azure Security Center, aby zachować konfiguracje zabezpieczeń dla wszystkich zasobów platformy Azure.

Jak skonfigurować Azure Policy i zarządzać nimi:  https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Przeprowadź inspekcję zgodności rejestrów kontenerów platformy Azure przy użyciu Azure Policy:  https://docs.microsoft.com/azure/container-registry/container-registry-azure-policy


**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: Ustanów bezpieczne konfiguracje systemów operacyjnych

**Wskazówki**: Użyj Azure Security Center rekomendacji "Koryguj luki w zabezpieczeniach w konfiguracjach zabezpieczeń na Virtual Machines", aby zachować konfiguracje zabezpieczeń we wszystkich zasobach obliczeniowych.

Jak monitorować zalecenia dotyczące Azure Security Center:  https://docs.microsoft.com/azure/security-center/security-center-recommendations

Jak skorygować Azure Security Center zalecenia:  https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations


**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: obsługa bezpiecznych konfiguracji zasobów platformy Azure

**Wskazówki**: Użyj Azure Policy [Odmów] i [Wdróż, jeśli nie istnieje], aby wymusić bezpieczne ustawienia dla zasobów platformy Azure.

Przeprowadź inspekcję zgodności rejestrów kontenerów platformy Azure przy użyciu Azure Policy:  https://docs.microsoft.com/azure/container-registry/container-registry-azure-policy

Jak skonfigurować Azure Policy i zarządzać nimi: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Opis efektów Azure Policy:  https://docs.microsoft.com/azure/governance/policy/concepts/effects



**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: Zachowaj konfiguracje bezpiecznego systemu operacyjnego

**Wskazówki**: nie dotyczy. Testy porównawcze są przeznaczone do zasobów obliczeniowych.

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Współużytkowane

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: bezpiecznie przechowuj konfigurację zasobów platformy Azure

**Wskazówki**: Jeśli używasz niestandardowych definicji Azure Policy, użyj Azure Repos, aby bezpiecznie przechowywać kod i zarządzać nim.

Jak przechowywać kod w usłudze Azure DevOps:  https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

Dokumentacja Azure Repos:  https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops


**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: bezpieczne przechowywanie niestandardowych obrazów systemu operacyjnego

**Wskazówki**: nie dotyczy. Test porównawczy dotyczy zasobów obliczeniowych.


**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="77-deploy-system-configuration-management-tools"></a>7,7: Wdrażanie narzędzi do zarządzania konfiguracją systemu

**Wskazówki**: Użyj Azure Policy do alertów, inspekcji i wymuszania konfiguracji systemu. Dodatkowo opracowuj proces i potok na potrzeby zarządzania wyjątkami zasad.

Przeprowadź inspekcję zgodności rejestrów kontenerów platformy Azure przy użyciu Azure Policy:  https://docs.microsoft.com/azure/container-registry/container-registry-azure-policy

Jak skonfigurować Azure Policy i zarządzać nimi:  https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage


**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7,8: Wdrażanie narzędzi do zarządzania konfiguracją systemu dla systemów operacyjnych

**Wskazówki**: nie dotyczy. Test porównawczy dotyczy zasobów obliczeniowych.


**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7,9: Zaimplementuj automatyczne monitorowanie konfiguracji dla usług platformy Azure

**Wskazówki**: Użyj Azure Security Center, aby przeprowadzić skanowanie linii bazowej dla zasobów platformy Azure.

Użyj Azure Policy, aby umieścić ograniczenia dotyczące typu zasobów, które można utworzyć w ramach subskrypcji.

Jak skorygować zalecenia w Azure Security Center:  https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations

Przeprowadź inspekcję zgodności rejestrów kontenerów platformy Azure przy użyciu Azure Policy:  https://docs.microsoft.com/azure/container-registry/container-registry-azure-policy



**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: Zaimplementuj automatyczne monitorowanie konfiguracji dla systemów operacyjnych

**Wskazówki**: nie dotyczy. Test porównawczy dotyczy zasobów obliczeniowych.


**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="711-manage-azure-secrets-securely"></a>7,11: bezpieczne zarządzanie wpisami tajnymi platformy Azure

**Wskazówki**: Użyj tożsamość usługi zarządzanej w połączeniu z Azure Key Vault, aby uprościć i zabezpieczyć poufne Zarządzanie aplikacjami w chmurze.

Jak przeprowadzić integrację z tożsamościami zarządzanymi przez platformę Azure:  https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

Jak utworzyć Key Vault: https://docs.microsoft.com/azure/key-vault/general/quick-create-portal

Jak przeprowadzić uwierzytelnianie w Key Vault: https://docs.microsoft.com/azure/key-vault/general/authentication

Jak przypisać zasady dostępu Key Vault: https://docs.microsoft.com/azure/key-vault/general/assign-access-policy-portal

Użyj tożsamości zarządzanej przez platformę Azure w Azure Container Registry zadaniach:  https://docs.microsoft.com/azure/container-registry/container-registry-tasks-authentication-managed-identity


**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: bezpieczne i automatyczne zarządzanie tożsamościami

**Wskazówki**: używanie tożsamości zarządzanych do świadczenia usług platformy Azure z automatyczną tożsamością zarządzaną w usłudze Azure AD. Tożsamości zarządzane umożliwiają uwierzytelnianie w dowolnej usłudze, która obsługuje uwierzytelnianie usługi Azure AD, w tym Key Vault, bez żadnych poświadczeń w kodzie.

Jak skonfigurować tożsamości zarządzane:  https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm

Użyj tożsamości zarządzanej do uwierzytelniania w usłudze Azure Container Registry:  https://docs.microsoft.com/azure/container-registry/container-registry-authentication-managed-identity


**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: eliminowanie nieprzewidzianego narażenia na poświadczenia

**Wskazówki**: Implementuj skaner poświadczeń, aby identyfikować poświadczenia w kodzie. Skaner poświadczeń ułatwia również przenoszenie odnalezionych poświadczeń do bezpieczniejszych lokalizacji, takich jak usługa Azure Key Vault.

Jak skonfigurować skaner poświadczeń:  https://secdevtools.azurewebsites.net/helpcredscan.html


**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

## <a name="malware-defense"></a>Ochrona przed złośliwym oprogramowaniem

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Obrona złośliwego oprogramowania](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8,1: Użyj centralnie zarządzanego oprogramowania chroniącego przed złośliwym oprogramowaniem

**Wskazówki**: Korzystaj z programu Microsoft chroniącego przed złośliwym kodem dla platformy Azure Cloud Services i Virtual Machines do ciągłego monitorowania i obrony zasobów. W przypadku systemu Linux należy zastosować rozwiązanie chroniące przed złośliwym oprogramowaniem innej firmy.

Jak skonfigurować oprogramowanie chroniące przed złośliwym oprogramowaniem firmy Microsoft dla Cloud Services i Virtual Machines:  https://docs.microsoft.com/azure/security/fundamentals/antimalware


**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: przeskanuj pliki przed przekazaniem do zasobów platformy Azure, które nie są obliczeniowe

**Wskazówki**: oprogramowanie chroniące przed złośliwym oprogramowaniem firmy Microsoft jest włączone na podstawowym hoście obsługującym usługi platformy Azure (na przykład Azure Container Registry), ale nie jest uruchamiane na zawartości klienta.

Skanuj wstępnie wszystkie pliki przekazywane do zasobów platformy Azure, które nie są obliczeniowe, takie jak App Service, Data Lake Storage, Blob Storage itd.


**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8,3: Upewnij się, że oprogramowanie chroniące przed złośliwym oprogramowaniem i podpisy zostały zaktualizowane

**Wskazówki**: nie dotyczy. Testy porównawcze są przeznaczone do zasobów obliczeniowych. Firma Microsoft obsługuje oprogramowanie chroniące przed złośliwym kodem dla podstawowej platformy.


**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

## <a name="data-recovery"></a>Odzyskiwanie danych

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: odzyskiwanie danych](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: Zapewnij regularne zautomatyzowane przywracanie awaryjne

**Wskazówki**: dane w rejestrze kontenerów Microsoft Azure są zawsze automatycznie replikowane w celu zapewnienia trwałości i wysokiej dostępności. Azure Container Registry kopiuje dane, aby były chronione przed planowanymi i nieplanowanymi zdarzeniami

Opcjonalnie geograficznie Replikuj rejestr kontenerów w celu przechowywania replik rejestru w wielu regionach świadczenia usługi Azure. 

Replikacja geograficzna w Azure Container Registry:  https://docs.microsoft.com/azure/container-registry/container-registry-geo-replication



**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: wykonaj kompletne kopie zapasowe systemu i Utwórz kopię zapasową wszystkich kluczy zarządzanych przez klienta

**Wskazówki**: Opcjonalnie można wykonać kopię zapasową obrazów kontenera przez zaimportowanie z jednego rejestru do drugiego.

Tworzenie kopii zapasowych kluczy zarządzanych przez klienta w Azure Key Vault przy użyciu narzędzi wiersza polecenia platformy Azure lub zestawów SDK.

Importuj obrazy kontenerów do rejestru kontenerów:  https://docs.microsoft.com/azure/container-registry/container-registry-import-images

Jak utworzyć kopię zapasową kluczy magazynu kluczy na platformie Azure:  https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0


**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: Weryfikuj wszystkie kopie zapasowe, w tym klucze zarządzane przez klienta

**Wskazówki**: testowanie przywracania kopii zapasowych kluczy zarządzanych przez klienta w Azure Key Vault przy użyciu narzędzi wiersza polecenia platformy Azure lub zestawów SDK.

Jak przywrócić klucze Azure Key Vault na platformie Azure:  https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0


**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: zapewnianie ochrony kopii zapasowych i kluczy zarządzanych przez klienta

**Wskazówki**: można włączyć Soft-Delete w Azure Key Vault, aby chronić klucze przed przypadkowym lub złośliwym usunięciem.

Jak włączyć Soft-Delete w Key Vault: https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal


**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

## <a name="incident-response"></a>Reagowanie na zdarzenia

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: odpowiedź na zdarzenia](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: Tworzenie przewodnika odpowiedzi na zdarzenia

**Wskazówka**: Utwórz przewodnik odpowiedzi na zdarzenia dla swojej organizacji. Upewnij się, że istnieją zarejestrowane plany reakcji na zdarzenia, które definiują wszystkie role pracowników, a także etapy obsługi zdarzeń/zarządzania od wykrywania do oceny po zdarzeniu.

Jak skonfigurować automatyzację przepływu pracy w ramach Azure Security Center:  https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide

Wskazówki dotyczące tworzenia własnego procesu reagowania na zdarzenia zabezpieczeń:  https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

Anatomia w centrum Microsoft Security Response:  https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

Klient może także skorzystać z przewodnika obsługi zdarzeń związanych z bezpieczeństwem programu NIST, aby pomóc w tworzeniu własnego planu reagowania na zdarzenia:  https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf



**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: Tworzenie oceny incydentu i procedury priorytetyzacji

**Wskazówki**: Azure Security Center przypisuje ważność do każdego alertu, aby pomóc w ustaleniu, które alerty należy najpierw zbadać. Ważność jest oparta na tym, jak dobrze Security Center znajduje się w wyszukiwaniu lub analitycznym używanym do wystawiania alertu, a także poziom pewności, że istniało złośliwy wpływ na działanie, które prowadziło do alertu.

Dodatkowo jasno Oznacz subskrypcje (na przykład produkcyjny, nieprodukcyjny) i Utwórz system nazewnictwa, aby jasno identyfikować i klasyfikować zasoby platformy Azure.


**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="103-test-security-response-procedures"></a>10,3: procedury odpowiedzi na zabezpieczenia testowe

**Wskazówki**: przeprowadzanie ćwiczeń w celu przetestowania możliwości reagowania na zdarzenia systemu w regularnych erze. Zidentyfikuj słabe punkty i przerwy oraz popraw plan zgodnie z wymaganiami.

Zapoznaj się z publikacją NIST: Przewodnik dotyczący testowania, uczenia i ćwiczeń programów dla planów i możliwości IT:  https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf


**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: Podaj szczegóły kontaktu dotyczącego zabezpieczeń i Skonfiguruj powiadomienia dotyczące alertów dotyczących zdarzeń związanych z zabezpieczeniami

**Wskazówki**: informacje kontaktowe dotyczące zdarzenia zabezpieczeń będą używane przez firmę Microsoft do skontaktowania się z Tobą, jeśli firma Microsoft Security Response Center (MSRC) wykryje, że dostęp do danych klienta został uzyskany przez nielegalną lub nieautoryzowaną osobę.  Przejrzyj zdarzenia po fakcie, aby upewnić się, że problemy zostały rozwiązane.

Jak ustawić Azure Security Center kontaktu zabezpieczeń:  https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details


**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: Uwzględnij alerty zabezpieczeń w systemie odpowiedzi na zdarzenia

**Wskazówki**: eksportowanie alertów i zaleceń dotyczących Azure Security Center przy użyciu funkcji eksportu ciągłego. Eksport ciągły umożliwia wyeksportowanie alertów i zaleceń ręcznie lub w stały sposób ciągły. Możesz użyć łącznika danych Azure Security Center, aby przesłać strumieniowo wskaźnik do alertów.

Jak skonfigurować eksport ciągły:  https://docs.microsoft.com/azure/security-center/continuous-export

Jak przesłać strumieniowo alerty do usługi Azure wskaźnikowej:  https://docs.microsoft.com/azure/sentinel/connect-azure-security-center


**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: Automatyzowanie odpowiedzi na alerty zabezpieczeń

**Wskazówki**: Użyj funkcji automatyzacji przepływu pracy w programie Azure Security Center, aby automatycznie wyzwalać odpowiedzi za pośrednictwem "Logic Apps" na temat alertów zabezpieczeń i zaleceń.

Jak skonfigurować automatyzację przepływu pracy i Logic Apps:  https://docs.microsoft.com/azure/security-center/workflow-automation


**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

## <a name="penetration-tests-and-red-team-exercises"></a>Testy penetracyjne i ćwiczenia typu „red team”

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: testy penetracji i czerwone ćwiczenia zespołu](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11,1: Przeprowadź regularne testowanie penetracji zasobów platformy Azure i zadbaj o skorygowanie wszystkich krytycznych ustaleń dotyczących zabezpieczeń w ciągu 60 dni

**Wskazówki**: Postępuj zgodnie z zasadami firmy Microsoft dotyczącymi zaangażowania, aby upewnić się, że testy penetracji nie naruszają zasad firmy Microsoft:  https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1

Aby uzyskać więcej informacji na temat strategii i sposobu działania tworzenia zespołu Red-and-testowego na żywo w oparciu o infrastrukturę, usługi i aplikacje chmurowe zarządzane przez firmę Microsoft, należy tutaj:  https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e



**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Współużytkowane

## <a name="next-steps"></a>Następne kroki

- Zobacz [test porównawczy zabezpieczeń platformy Azure](../security/benchmarks/overview.md)
- Dowiedz się więcej o [punktach odniesienia zabezpieczeń platformy Azure](../security/benchmarks/security-baselines-overview.md)
