---
title: Podstawa zabezpieczeń platformy Azure dla Virtual Network
description: Linia bazowa zabezpieczeń Virtual Network zawiera wskazówki i zasoby dotyczące procedur związanych z wdrażaniem zaleceń dotyczących zabezpieczeń określonych w teście zabezpieczeń platformy Azure.
author: msmbaldwin
ms.service: virtual-network
ms.topic: conceptual
ms.date: 10/01/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: c34ace92fffee3c135cb05e07f06d885751bbce5
ms.sourcegitcommit: d479ad7ae4b6c2c416049cb0e0221ce15470acf6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/01/2020
ms.locfileid: "91629747"
---
# <a name="azure-security-baseline-for-virtual-network"></a>Podstawa zabezpieczeń platformy Azure dla Virtual Network

Ta linia bazowa zabezpieczeń ma zastosowanie do Virtual Network platformy Azure wskazówek dotyczących usługi [Azure Security test w wersji 1,0](../security/benchmarks/overview-v1.md) . Test porównawczy zabezpieczeń platformy Azure zawiera zalecenia dotyczące sposobu zabezpieczania rozwiązań w chmurze na platformie Azure. Zawartość jest pogrupowana według **kontroli zabezpieczeń** zdefiniowanych przez usługę Azure Security test i powiązane wskazówki dotyczące usługi Azure Virtual Network. Nie zostały wykluczone **kontrolki** , które mają zastosowanie do Virtual Network platformy Azure.

Aby dowiedzieć się, jak platforma Azure Virtual Network całkowicie zamapowana na test porównawczy zabezpieczeń platformy Azure, zapoznaj się z [pełnym plikiem mapowania podstawy zabezpieczeń usługi azure Virtual Network](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Bezpieczeństwo sieci

*Aby uzyskać więcej informacji, zobacz [wzorzec zabezpieczeń Azure: zabezpieczenia sieci](../security/benchmarks/security-control-network-security.md).*

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1,2: Monitoruj i Rejestruj konfigurację oraz ruch sieci wirtualnych, podsieci i interfejsów sieciowych

**Wskazówki**: Użyj Security Center i postępuj zgodnie z zaleceniami dotyczącymi ochrony sieci, aby pomóc w zabezpieczeniu zasobów sieciowych na platformie Azure. 

Wyślij dzienniki przepływu sieciowych grup zabezpieczeń do obszaru roboczego Log Analytics i użyj Analiza ruchu, aby uzyskać wgląd w przepływ ruchu do chmury platformy Azure. Analiza ruchu oferuje możliwość wizualizacji aktywności sieciowej i identyfikowania aktywnych punktów, identyfikowania zagrożeń bezpieczeństwa, zrozumienia wzorców przepływu ruchu i lokalizowania w sieci konfiguracji. 

Użyj dzienników Azure Monitor, aby uzyskać wgląd w swoje środowisko. Obszar roboczy powinien być używany do sortowania i analizowania danych oraz integracji z innymi usługami platformy Azure, takimi jak Application Insights i Security Center. 

Wybierz pozycję Dzienniki zasobów, aby wysłać do konta usługi Azure Storage lub centrum zdarzeń. Do analizowania dzienników można także użyć innej platformy. 

- [Jak włączyć dzienniki przepływu sieciowej grupy zabezpieczeń](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Jak włączyć i używać Analiza ruchu](../network-watcher/traffic-analytics.md)

- [Informacje o zabezpieczeniach sieci zapewnianych przez Security Center](../security-center/security-center-network-recommendations.md)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: odmowa komunikacji ze znanymi niezłośliwymi adresami IP

**Wskazówki**: Włącz funkcję rozproszonej ochrony "odmowa usługi" (DDoS) na platformie Virtual Network Azure, aby chronić przed atakami DDoS.

Wdróż zaporę platformy Azure w każdej z granic sieci organizacji przy włączonej filtrowaniu opartym na analizie zagrożeń i skonfigurowanym jako "Alert i Odmów" w przypadku złośliwego ruchu sieciowego.

Użyj funkcji ochrony przed zagrożeniami Security Center, aby wykryć komunikację ze znanymi złośliwymi adresami IP.

Zastosuj Security Center zalecenia dotyczące adaptacyjnych grup zabezpieczeń sieci, które ograniczają porty i źródłowe adresy IP na podstawie rzeczywistego ruchu i analizy zagrożeń. 

- [Zarządzanie standardem Azure DDoS Protection przy użyciu Azure Portal](manage-ddos-protection.md)

- [Filtrowanie na podstawie analizy zagrożeń zapory platformy Azure](../firewall/threat-intel.md)

- [Ochrona przed zagrożeniami w Security Center](/azure/security-center/threat-protection)

- [Adaptacyjne Zabezpieczanie sieci w Azure Security Center](../security-center/security-center-adaptive-network-hardening.md)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="15-record-network-packets"></a>1,5: rejestrowanie pakietów sieciowych

**Wskazówki**: Użyj funkcji przechwytywania pakietów VPN Gateway oprócz powszechnie dostępnych narzędzi przechwytywania pakietów, aby zarejestrować pakiety sieciowe. 

Możesz również przejrzeć rozwiązania oparte na agencie lub urządzenie WUS, które udostępniają funkcje punktu dostępu terminalu (TAP) lub funkcji widoczności sieci za pośrednictwem rozwiązań partnerskich brokera pakietów dostępnych w ofertach w portalu Azure Marketplace.

- [Konfigurowanie przechwytywania pakietów dla bram sieci VPN](../vpn-gateway/packet-capture.md) 

- [Partner wirtualnego urządzenia sieciowego](https://azure.microsoft.com/solutions/network-appliances)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: wdrażanie opartych na sieci systemów zapobiegania wykrywaniem i dostępem intruzów (identyfikatorów/adresów IP)

**Wskazówki**: Użyj zapory platformy Azure wdrożonej w sieci wirtualnej z włączoną funkcją analizy zagrożeń. Użyj filtrowania opartego na analizie zagrożeń platformy Azure, aby zgłosić alert lub aby odmówić ruchu do i ze znanych złośliwych adresów IP i domen. Adresy IP i domeny pochodzą z kanału informacyjnego analizy zagrożeń firmy Microsoft. 

Możesz również wybrać odpowiednią ofertę z witryny Azure Marketplace, która obsługuje funkcje identyfikatorów/adresów IP z możliwościami inspekcji ładunku.

Wdrażaj wybrane rozwiązanie zapory w każdej z granic sieci organizacji, aby wykrywać i/lub odrzucać złośliwy ruch.

- [Jak wdrożyć zaporę platformy Azure](../firewall/tutorial-firewall-deploy-portal.md)

- [Jak skonfigurować alerty za pomocą zapory platformy Azure](../firewall/threat-intel.md)

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: Minimalizacja złożoności i kosztów administracyjnych reguł zabezpieczeń sieci

**Wskazówki**: Użyj tagów usługi Virtual Network, aby zdefiniować kontrolę dostępu do sieci dla sieciowych grup zabezpieczeń lub zapory platformy Azure. Podczas tworzenia reguł zabezpieczeń można używać tagów usługi zamiast określonych adresów IP. Zezwalaj lub Odmawiaj ruchu dla odpowiedniej usługi przez określenie nazwy tagu usługi (na przykład ApiManagement) w odpowiednim polu źródłowym lub docelowym reguły. Firma Microsoft zarządza prefiksami adresów, które obejmują tag usługi, i automatycznie aktualizuje tag usługi jako adresy.

Grupy zabezpieczeń aplikacji ułatwiają uproszczenie skomplikowanej konfiguracji zabezpieczeń. Grupy zabezpieczeń aplikacji umożliwiają skonfigurowanie zabezpieczeń sieci jako naturalnego rozszerzenia struktury aplikacji. Pozwala to na grupowanie maszyn wirtualnych i definiowanie zasad zabezpieczeń sieci na podstawie tych grup.

- [Zrozumienie i używanie tagów usługi](service-tags-overview.md)

- [Zrozumienie i używanie grup zabezpieczeń aplikacji](/azure/virtual-network/security-overview#application-security-groups)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: Obsługa standardowych konfiguracji zabezpieczeń dla urządzeń sieciowych

**Wskazówki**: Definiowanie i implementowanie standardowych konfiguracji zabezpieczeń dla zasobów sieciowych za pomocą Azure Policy i przeglądanie wbudowanych definicji zasad sieciowych dla implementacji.

Zapoznaj się z zasadami domyślnymi dotyczącymi Security Center, które zawierają dostępne zalecenia dotyczące zabezpieczeń dotyczące sieci wirtualnych.

Korzystając z planów platformy Azure, można uprościć wdrożenia platformy Azure na dużą skalę przez pakowanie kluczowych artefaktów środowiska, takich jak szablony Azure Resource Manager, przypisania kontroli dostępu opartej na rolach (Azure RBAC) i zasady, w ramach jednej definicji planu. Azure Blueprint można zastosować do nowych subskrypcji w celu zapewnienia precyzyjnej kontroli i zarządzania przy użyciu wersji. 

- [Jak skonfigurować Azure Policy i zarządzać nimi](../governance/policy/tutorials/create-and-manage.md)

- [Przykłady Azure Policy dla sieci](../governance/policy/samples/built-in-policies.md#network) 

- [Jak utworzyć Azure Blueprint](../governance/blueprints/create-blueprint-portal.md)

- [Włącz monitorowanie w Azure Security Center](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Security%20Center/AzureSecurityCenter.json)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="110-document-traffic-configuration-rules"></a>1,10: udokumentowanie reguł konfiguracji ruchu

**Wskazówki**: używanie tagów dla sieciowych grup zabezpieczeń i innych zasobów związanych z zabezpieczeniami sieci i przepływem ruchu. Użyj pola "Description", aby określić potrzebę biznesową, czas trwania i inne informacje dotyczące reguł zezwalających na ruch do/z sieci dla indywidualnych reguł grupy zabezpieczeń sieci.
Użyj dowolnych wbudowanych definicji Azure Policy związanych ze znakiem, takich jak "Wymagaj tagu i jego wartości", aby upewnić się, że wszystkie zasoby są tworzone przy użyciu tagów i powiadomienia o istniejących nieoznakowanych zasobach.

Wybierz Azure PowerShell lub interfejs wiersza polecenia platformy Azure, aby wyszukiwać lub wykonywać akcje dotyczące zasobów na podstawie ich tagów.

- [Tworzenie i używanie tagów](/azure/azure-resource-manager/resource-group-using-tags)

- [Jak utworzyć Virtual Network](quick-create-portal.md)

- [Jak utworzyć sieciowej grupy zabezpieczeń z konfiguracją zabezpieczeń](tutorial-filter-network-traffic.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: Użyj zautomatyzowanych narzędzi do monitorowania konfiguracji zasobów sieciowych i wykrywania zmian

**Wskazówki**: Użyj dziennika aktywności platformy Azure do monitorowania konfiguracji zasobów i wykrywania zmian w sieci wirtualnej. Tworzenie alertów w Azure Monitor, które będą wyzwalane po wprowadzeniu zmian w zasobach krytycznych.

- [Jak wyświetlać i pobierać zdarzenia dziennika aktywności platformy Azure](/azure/azure-monitor/platform/activity-log-view)

- [Jak utworzyć alerty w Azure Monitor](../azure-monitor/platform/alerts-activity-log.md)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

## <a name="logging-and-monitoring"></a>Rejestrowanie i monitorowanie

*Aby uzyskać więcej informacji, zobacz temat [Azure Security test: rejestrowanie i monitorowanie](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2,2: Skonfiguruj centralne zarządzanie dziennikami zabezpieczeń

**Wskazówki**: umożliwia Azure monitor dostępu do dzienników inspekcji i aktywności, w tym źródła zdarzeń, daty, użytkownika, sygnatury czasowej, adresów źródłowych, adresów docelowych i innych przydatnych elementów. 

W Azure Monitor używać Log Analytics obszarów roboczych do wykonywania zapytań i przeprowadzania analiz oraz używania kont usługi Azure Storage do przechowywania długoterminowego/archiwizowania.
Alternatywnie możesz włączyć i dołączyć dane do usługi Azure wskaźnikowej lub SIEM innych firm. 

- [Jak zbierać dzienniki platformy i metryki za pomocą Azure Monitor](../azure-monitor/platform/diagnostic-settings.md) 

- [Wyświetlanie i pobieranie zdarzeń dziennika aktywności platformy Azure](/azure/azure-monitor/platform/activity-log-view)

- [Jak dołączyć wskaźnik na platformie Azure](../sentinel/quickstart-onboard.md)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: Włączanie rejestrowania inspekcji dla zasobów platformy Azure

**Wskazówki**: umożliwia Azure monitor dostępu do dzienników inspekcji i aktywności, w tym źródła zdarzeń, daty, użytkownika, sygnatury czasowej, adresów źródłowych, adresów docelowych i innych przydatnych elementów.

- [Jak zbierać dzienniki platformy i metryki za pomocą Azure Monitor](../azure-monitor/platform/diagnostic-settings.md) 

- [Wyświetlanie i pobieranie zdarzeń dziennika aktywności platformy Azure](/azure/azure-monitor/platform/activity-log-view)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="25-configure-security-log-storage-retention"></a>2,5: Konfigurowanie przechowywania magazynu dzienników zabezpieczeń

**Wskazówki**: w obszarze Azure monitor Ustaw okres przechowywania log Analytics obszaru roboczego zgodnie z regulacjami zgodności w organizacji. Użyj kont usługi Azure Storage, aby uzyskać długoterminową/archiwalną pamięć masową przechowywania magazynu dzienników zabezpieczeń.

- [Zmień okres przechowywania danych w Log Analytics](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

- [Jak skonfigurować zasady przechowywania dla dzienników kont usługi Azure Storage](../storage/common/storage-monitor-storage-account.md#configure-logging)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="26-monitor-and-review-logs"></a>2,6: dzienniki monitorowania i przeglądania

**Wskazówki**: analizowanie i monitorowanie dzienników pod kątem nietypowego zachowania i regularnego przeglądania wyników. Za pomocą Azure Monitor Log Analytics obszaru roboczego można wykonywać zapytania i przeprowadzać analizy oraz korzystać z kont usługi Azure Storage w celu przechowywania długoterminowego/archiwizowania. 

Alternatywnie możesz włączyć i dołączyć dane do usługi Azure wskaźnikowej lub SIEM innych firm. 

- [Informacje o obszarze roboczym Log Analytics](../azure-monitor/log-query/get-started-portal.md)

- [Jak wykonywać niestandardowe zapytania w Azure Monitor](../azure-monitor/log-query/get-started-queries.md)

- [Jak dołączyć wskaźnik na platformie Azure](../sentinel/quickstart-onboard.md)

- [Jak rozpocząć pracę z usługą Azure Monitor i integracją SIEM innej firmy](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: Włączanie alertów dla nietypowych działań

**Wskazówki**: Użyj Security Center z obszarem roboczym log Analytics do monitorowania i generowania alertów dotyczących nietypowego działania znalezionego w dziennikach i zdarzeniach zabezpieczeń.

Alternatywnie możesz włączyć i dołączyć dane do usługi Azure wskaźnikowej lub SIEM innych firm na potrzeby alertów.

- [Jak zarządzać alertami w Security Center](../security-center/security-center-managing-and-responding-alerts.md)

- [Jak ostrzec dane dziennika usługi log Analytics](../azure-monitor/learn/tutorial-response.md)

- [Jak dołączyć wskaźnik na platformie Azure](../sentinel/quickstart-onboard.md)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="29-enable-dns-query-logging"></a>2,9: Włączanie rejestrowania zapytań DNS

**Wskazówki**: Zaimplementuj rozwiązanie innych firm z witryny Azure Marketplace na potrzeby rejestrowania w systemie DNS zgodnie z potrzebami organizacji.

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

## <a name="identity-and-access-control"></a>Tożsamość i kontrola dostępu

*Aby uzyskać więcej informacji, zobacz [test dotyczący zabezpieczeń platformy Azure: tożsamość i kontrola dostępu](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: obsługa spisu kont administracyjnych

**Wskazówki**: Użyj wbudowanych ról administratora usługi Azure Active Directory (Azure AD), które mogą być jawnie przypisane i queryable. 

Za pomocą modułu Azure AD PowerShell można wykonywać zapytania ad hoc w celu odnajdywania kont należących do grup administracyjnych.

- [Jak uzyskać rolę katalogu w usłudze Azure AD przy użyciu programu PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

- [Jak uzyskać członków roli katalogu w usłudze Azure AD przy użyciu programu PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: Użyj dedykowanych kont administracyjnych

**Wskazówki**: Tworzenie standardowych procedur operacyjnych dotyczących korzystania z dedykowanych kont administracyjnych. Do monitorowania liczby kont administracyjnych służy Security Center Zarządzanie tożsamościami i dostępem.

Włącz dostęp just-in-Time/tylko do odczytu przy użyciu ról uprzywilejowanych Azure AD Privileged Identity Management usług firmy Microsoft i Azure Resource Manager. 

- [Dowiedz się więcej o Privileged Identity Management](/azure/active-directory/privileged-identity-management/)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3,4: Użyj Azure Active Directory logowania jednokrotnego (SSO)

**Wskazówki**: Użyj logowania jednokrotnego w usłudze Azure Active Directory (Azure AD) zamiast konfigurować indywidualne poświadczenia autonomiczne dla poszczególnych usług. Użyj zaleceń dotyczących zarządzania tożsamościami i dostępem Security Center.

- [Single sign-on to applications in Azure Active Directory (Logowanie jednokrotne do aplikacji w usłudze Azure Active Directory)](../active-directory/manage-apps/what-is-single-sign-on.md) 

- [Jak monitorować tożsamość i dostęp w Azure Security Center](../security-center/security-center-identity-access.md)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Użyj uwierzytelniania wieloskładnikowego dla wszystkich Azure Active Directory dostępu opartego na usłudze

**Wskazówki**: włączanie usługi Azure Active Directory (Azure AD) Multi-Factor Authentication (MFA) i postępuj zgodnie z zaleceniami dotyczącymi zarządzania tożsamościami i dostępem w Security Center.

- [Jak włączyć usługę MFA na platformie Azure](../active-directory/authentication/howto-mfa-getstarted.md) 

- [Jak monitorować tożsamość i dostęp w Azure Security Center](../security-center/security-center-identity-access.md)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3,6: Korzystaj z bezpiecznych stacji roboczych zarządzanych przez platformę Azure na potrzeby zadań administracyjnych

**Wskazówki**: Użyj stacji roboczych dostępu uprzywilejowanego (dostępem uprzywilejowanym) z usługą Multi-Factor Authentication (MFA) skonfigurowaną w celu logowania się do zasobów sieciowych platformy Azure i uzyskiwania do nich dostępu.

- [Dowiedz się więcej o stacjach roboczych uprzywilejowanego dostępu](/windows-server/identity/securing-privileged-access/privileged-access-workstations) 

- [Jak włączyć usługę MFA na platformie Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: Rejestruj i Ostrzegaj o podejrzanych działaniach z kont administracyjnych

**Wskazówki**: korzystanie z funkcji wykrywania ryzyka Azure Active Directory (Azure AD) do wyświetlania alertów i raportów na temat ryzykownego zachowania użytkowników. 

Pozyskaj Security Center alerty wykrywania ryzyka w Azure Monitor i skonfiguruj niestandardowe alerty/powiadomienia przy użyciu grup akcji.

- [Zrozumienie Security Center wykrywania ryzyka (podejrzane działanie)](/azure/active-directory/reports-monitoring/concept-risk-events) 

- [Jak zintegrować dzienniki aktywności platformy Azure z usługą Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md) 

- [Jak skonfigurować grupy akcji dla niestandardowych alertów i powiadomień](../azure-monitor/platform/action-groups.md)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: zarządzanie zasobami platformy Azure tylko z zatwierdzonych lokalizacji

**Wskazówki**: Użyj dostępu warunkowego o nazwie Locations, aby zezwolić na dostęp tylko do określonych logicznych grup zakresów adresów IP lub krajów/regionów.

- [Jak skonfigurować nazwane lokalizacje na platformie Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="39-use-azure-active-directory"></a>3,9: Użyj Azure Active Directory

**Wskazówki**: Użyj Azure Active Directory (Azure AD) jako centralnego systemu uwierzytelniania i autoryzacji dla usług. Usługa Azure AD chroni dane przy użyciu silnego szyfrowania danych przechowywanych i przesyłanych, a także Sole, skróty i bezpieczne przechowywanie poświadczeń użytkownika.  

- [Jak utworzyć i skonfigurować wystąpienie usługi Azure AD](../active-directory-domain-services/tutorial-create-instance.md)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: regularnie Przeglądaj i Uzgodnij dostęp użytkowników

**Wskazówki**: Użyj Azure Active Directory (Azure AD) w celu zapewnienia dzienników, aby ułatwić odnajdywanie starych kont. 

Przeglądy dostępu do tożsamości platformy Azure mogą być wykonywane w celu wydajnego zarządzania członkostwem w grupach, dostępem do aplikacji dla przedsiębiorstw i przypisaniami ról. Dostęp użytkowników powinien być regularnie przeglądany, aby upewnić się, że tylko aktywni użytkownicy mają ciągły dostęp.

- [Informacje o raportowaniu usługi Azure AD](/azure/active-directory/reports-monitoring/)

- [Jak korzystać z przeglądów dostępu do tożsamości platformy Azure](../active-directory/governance/access-reviews-overview.md)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: Monitor próbuje uzyskać dostęp do zdezaktywowanych poświadczeń

**Wskazówki**: Integrowanie działań związanych z logowaniem Azure Active Directory (Azure AD), inspekcją i źródłami dzienników zdarzeń z ewentualnym użyciem dowolnego narzędzia Siem lub monitorowania na podstawie dostępu użytkownika. 

Usprawnij ten proces, tworząc ustawienia diagnostyczne dla Azure Active Directory kont użytkowników i wysyłając dzienniki inspekcji i dzienniki logowania do obszaru roboczego Log Analytics. Wszystkie żądane alerty można skonfigurować w obszarze roboczym Log Analytics.

- [Jak zintegrować dzienniki aktywności platformy Azure z usługą Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: odchylenia zachowania alertu dotyczącego logowania na koncie

**Wskazówki**: Użyj funkcji ryzyka i ochrony tożsamości Azure Active Directory (Azure AD), aby skonfigurować automatyczne odpowiedzi na wykryte podejrzane działania związane z tożsamościami użytkowników w sieci wirtualnej. Pozyskiwanie danych na platformie Azure — badanie wskaźnikowe w celu przeprowadzenia dalszych badań.

- [Jak wyświetlić ryzykowne logowania usługi Azure AD](/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

- [Jak skonfigurować i włączyć zasady dotyczące ryzyka związanego z ochroną tożsamości](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Jak dołączyć wskaźnik na platformie Azure](../sentinel/quickstart-onboard.md)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

## <a name="data-protection"></a>Ochrona danych

*Aby uzyskać więcej informacji, zobacz [Azure Security test: Data Protection](../security/benchmarks/security-control-data-protection.md).*

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: Szyfruj wszystkie poufne informacje podczas przesyłania

**Wskazówki**: Szyfruj wszystkie informacje poufne podczas przesyłania. Upewnij się, że wszyscy klienci łączący się z zasobami platformy Azure w sieciach wirtualnych mogą negocjować protokół TLS 1,2 lub nowszy. Postępuj zgodnie z Security Center zalecenia dotyczące szyfrowania podczas przesyłania i szyfrowania. 

Firma Microsoft oferuje kilka opcji, które mogą być używane przez klientów do zabezpieczania danych w ramach przesyłania wewnętrznie w sieci platformy Azure i zewnętrznie przez Internet do użytkownika końcowego. Obejmują one komunikację za pośrednictwem wirtualnych sieci prywatnych (wykorzystujących szyfrowanie IPsec/IKE), Transport Layer Security (TLS) 1,2 lub nowszych (za pośrednictwem składników platformy Azure, takich jak Application Gateway lub drzwi platformy Azure), protokołów bezpośrednio na maszynach wirtualnych platformy Azure (takich jak Windows IPsec lub SMB) i innych.

Ponadto "szyfrowanie domyślnie" przy użyciu MACsec (IEEE standard w warstwie linku danych) jest włączone dla całego ruchu platformy Azure, który jest używany przez usługę Azure datacenters w celu zapewnienia poufności i integralności danych klientów.

- [Informacje o szyfrowaniu podczas przesyłania na platformę Azure](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: udostępnione

### <a name="46-use-role-based-access-control-to-manage-access-to-resources"></a>4,6: Użyj kontroli dostępu opartej na rolach, aby zarządzać dostępem do zasobów 

**Wskazówki**: Aby zarządzać dostępem do danych i zasobów, użyj kontroli dostępu opartej na rolach (Azure RBAC) na platformie Azure. W przeciwnym razie użyj metod kontroli dostępu specyficznych dla usługi. 

Wybierz wbudowane role, takie jak właściciel, współautor lub współautor sieci i przypisz rolę do odpowiedniego zakresu. Można na przykład przypisać podzestaw możliwości sieci wirtualnej z określonymi uprawnieniami wymaganymi dla sieci wirtualnych do dowolnej z tych ról. 

- [Jak skonfigurować RBAC na platformie Azure](../role-based-access-control/role-assignments-portal.md)

- [Planowanie sieci wirtualnych](virtual-network-vnet-plan-design-arm.md#permissions)

- [Przejrzyj wbudowane role platformy Azure](../role-based-access-control/built-in-roles.md#networking)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: Rejestruj i Ostrzegaj o zmianach krytycznych zasobów platformy Azure

**Wskazówki**: Użyj Azure monitor z dziennikami aktywności platformy Azure, aby utworzyć alerty dotyczące sytuacji, w których zmiany dotyczą najważniejszych zasobów platformy Azure, takich jak sieci wirtualne i sieciowe grupy zabezpieczeń.

- [Rejestrowanie diagnostyczne dla sieciowej grupy zabezpieczeń](virtual-network-nsg-manage-log.md)

- [Jak utworzyć alerty dla zdarzeń dziennika aktywności platformy Azure](../azure-monitor/platform/alerts-activity-log.md)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

## <a name="inventory-and-asset-management"></a>Zarządzanie magazynem i zasobami

*Aby uzyskać więcej informacji, zobacz temat [Azure Security test: Inventory and Asset Management](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: Użyj rozwiązania automatycznego odnajdywania zasobów

**Wskazówki**: Użyj grafu zasobów platformy Azure do wykonywania zapytań i odnajdywania wszystkich zasobów sieciowych, takich jak sieci wirtualne, podsieci w ramach subskrypcji. Upewnij się, że masz odpowiednie uprawnienia (odczyt) w dzierżawie i że można wyliczyć wszystkie subskrypcje platformy Azure oraz zasoby w ramach subskrypcji.

- [Jak tworzyć zapytania za pomocą usługi Azure Graph](../governance/resource-graph/first-query-portal.md) 

- [Jak wyświetlić subskrypcje platformy Azure](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0) 

- [Opis kontroli RBAC platformy Azure](../role-based-access-control/overview.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="62-maintain-asset-metadata"></a>6,2: Konserwowanie metadanych zasobów

**Wskazówki**: stosowanie tagów do zasobów platformy Azure, dzięki czemu metadane są logicznie zorganizowane w taksonomię.

- [Tworzenie i używanie tagów](/azure/azure-resource-manager/resource-group-using-tags)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: Usuń nieautoryzowane zasoby platformy Azure

**Wskazówki**: używanie tagowania, grup zarządzania i oddzielnych subskrypcji, gdzie jest to konieczne, do organizowania i śledzenia sieci wirtualnej i powiązanych zasobów. Regularnie Uzgadniaj spis i zapewnij, że nieautoryzowane zasoby są usuwane z subskrypcji w odpowiednim czasie.

- [Jak utworzyć dodatkowe subskrypcje platformy Azure](/azure/billing/billing-create-subscription) 

- [Jak utworzyć Grupy zarządzania](/azure/governance/management-groups/create) 

- [Tworzenie i używanie tagów](/azure/azure-resource-manager/resource-group-using-tags)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6,4: Definiowanie i konserwowanie spisu zatwierdzonych zasobów platformy Azure

**Wskazówki**: należy utworzyć spis zatwierdzonych zasobów platformy Azure i zatwierdzonego oprogramowania dla zasobów obliczeniowych zgodnie z potrzebami organizacji.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: Monitoruj niezatwierdzone zasoby platformy Azure

**Wskazówki**: Użyj Azure Policy, aby umieścić ograniczenia dotyczące typu zasobów, które mogą być tworzone w subskrypcjach klientów, przy użyciu następujących wbudowanych definicji zasad:
- Niedozwolone typy zasobów 

- Dozwolone typy zasobów 

Zapytania lub odnajdywanie zasobów w ramach subskrypcji za pomocą usługi Azure Resource Graph w wysokich środowiskach opartych na zabezpieczeniach, takich jak te z kontami usługi Azure Storage. 

- [Jak skonfigurować Azure Policy i zarządzać nimi](../governance/policy/tutorials/create-and-manage.md) 

- [Jak tworzyć zapytania za pomocą usługi Azure Graph](../governance/resource-graph/first-query-portal.md)

- [Przykładowe zasady platformy Azure dla usługi Virtual Network](/azure/virtual-network/policy-samples)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: Usuń niezatwierdzone zasoby platformy Azure i aplikacje oprogramowania

**Wskazówki**: Zapobiegaj tworzeniu zasobów lub użyciu Azure Policy zgodnie z wymaganiami zasad organizacji. Implementuj procesy usuwania nieautoryzowanych zasobów.

- [Jak skonfigurować Azure Policy i zarządzać nimi](../governance/policy/tutorials/create-and-manage.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="69-use-only-approved-azure-services"></a>6,9: Używaj tylko zatwierdzonych usług platformy Azure

**Wskazówki**: Użyj Azure Policy, aby umieścić ograniczenia dotyczące typu zasobów, które mogą być tworzone w subskrypcjach klientów, przy użyciu następujących wbudowanych definicji zasad:
- Niedozwolone typy zasobów 

- Dozwolone typy zasobów 

- [Jak skonfigurować Azure Policy i zarządzać nimi](../governance/policy/tutorials/create-and-manage.md) 

- [Jak odmówić określonego typu zasobu za pomocą Azure Policy](/azure/governance/policy/samples/not-allowed-resource-types)

- [Przykładowe zasady platformy Azure dla usługi Virtual Network](/azure/virtual-network/policy-samples)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: Ogranicz możliwość korzystania przez użytkowników z Azure Resource Manager

**Wskazówki**: Użyj dostępu warunkowego platformy Azure, aby ograniczyć możliwość współdziałania z Azure Resource Manager przez skonfigurowanie "blokowania dostępu" dla aplikacji "Microsoft Azure Management".

- [Jak skonfigurować dostęp warunkowy w celu blokowania dostępu do Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

## <a name="secure-configuration"></a>Bezpieczna konfiguracja

*Aby uzyskać więcej informacji, zobacz temat [Azure Security test: bezpieczna konfiguracja](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: Ustanów bezpieczne konfiguracje dla wszystkich zasobów platformy Azure

**Wskazówki**: Użyj aliasów Azure Policy do tworzenia zasad niestandardowych w celu inspekcji lub wymuszania konfiguracji zasobów sieciowych platformy Azure, a także używania wbudowanych definicji Azure Policy.

Wyeksportuj wszystkie szablony kompilacji za pomocą formularza Azure Resource Manager w JavaScript Object Notation (JSON) i przejrzyj je, aby upewnić się, że konfiguracje spełniają lub przekroczą wymagania dotyczące zabezpieczeń w organizacji.

Zaimplementuj zalecenia z Security Center jako bezpieczną konfigurację odniesienia dla zasobów platformy Azure.

- [Jak wyświetlić dostępne aliasy Azure Policy](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

- [Samouczek: Tworzenie zasad i zarządzanie nimi w celu wymuszenia zgodności](../governance/policy/tutorials/create-and-manage.md)

- [Przykładowe zasady platformy Azure dla usługi Virtual Network](/azure/virtual-network/policy-samples)

- [Eksport jednego i kilku zasobów do szablonu w Azure Portal](../azure-resource-manager/templates/export-template-portal.md)

- [Zalecenia dotyczące zabezpieczeń — Przewodnik referencyjny](../security-center/recommendations-reference.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: obsługa bezpiecznych konfiguracji zasobów platformy Azure

**Wskazówki**: użyj szablonów Azure Resource Manager i zasad platformy Azure, aby bezpiecznie skonfigurować zasoby platformy Azure skojarzone z siecią wirtualną i powiązanymi zasobami.  Szablony Azure Resource Manager są plikami opartymi na formacie JSON (JavaScript Object Notation) służącymi do wdrażania maszyn wirtualnych wraz z zasobami platformy Azure. Firma Microsoft przeprowadza konserwację szablonów podstawowych.  

Użyj Azure Policy [Odmów] i [Wdróż, jeśli nie istnieje] efektów, aby wymusić bezpieczne ustawienia dla zasobów platformy Azure.

- [Informacje na temat tworzenia szablonów Azure Resource Manager](../virtual-machines/windows/ps-template.md) 

- [Jak skonfigurować Azure Policy i zarządzać nimi](../governance/policy/tutorials/create-and-manage.md) 

- [Zrozumienie Azure Policy efektów](../governance/policy/concepts/effects.md)

- [Przykłady szablonów usługi Azure Resource Manager dla sieci wirtualnej](template-samples.md)

- [Przykładowe zasady platformy Azure dla usługi Virtual Network](/azure/virtual-network/policy-samples)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: bezpiecznie przechowuj konfigurację zasobów platformy Azure

**Wskazówki**: Użyj usługi Azure DevOps, aby bezpiecznie przechowywać kod, taki jak niestandardowe zasady platformy Azure, szablony Azure Resource Manager i skrypty konfiguracji żądanego stanu. i tak dalej.

Użytkownik musi mieć uprawnienia dostępu do zasobów, które mają być zarządzane w usłudze Azure DevOps, takich jak kod, kompilacje i śledzenie pracy. Większość uprawnień uzyskuje się za poorednictwem wbudowanych grup zabezpieczeń. Można udzielić lub odmówić uprawnień określonym użytkownikom, wbudowanym grupom zabezpieczeń lub grupom zdefiniowanym w Azure Active Directory (Azure AD), jeśli jest zintegrowany z usługą Azure DevOps, lub Active Directory, jeśli jest zintegrowany z usługą Team Foundation Server.

- [Jak przechowywać kod w usłudze Azure DevOps](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops) 

- [Informacje o uprawnieniach i grupach na platformie Azure DevOps](/azure/devops/organizations/security/about-permissions)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: Wdrażanie narzędzi do zarządzania konfiguracją dla zasobów platformy Azure

**Wskazówki**: Definiowanie i implementowanie standardowych konfiguracji zabezpieczeń dla zasobów platformy Azure przy użyciu Azure Policy. Użyj aliasów Azure Policy, aby utworzyć niestandardowe zasady inspekcji lub wymuszania konfiguracji sieci zasobów platformy Azure oraz wszelkich wbudowanych definicji zasad związanych z określonymi zasobami. 

- [Jak skonfigurować Azure Policy i zarządzać nimi](../governance/policy/tutorials/create-and-manage.md)

- [Jak używać aliasów](../governance/policy/concepts/definition-structure.md#aliases)

- [Przykładowe zasady platformy Azure dla usługi Virtual Network](/azure/virtual-network/policy-samples)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: Zaimplementuj automatyczne monitorowanie konfiguracji dla zasobów platformy Azure

**Wskazówki**: Użyj Security Center, aby przeprowadzić skanowanie linii bazowej dla Virtual Network platformy Azure i powiązanych zasobów. Użyj Azure Policy, aby zgłaszać alerty i przeprowadzać inspekcję konfiguracji zasobów platformy Azure.

- [Jak skorygować zalecenia w Security Center](../security-center/security-center-remediate-recommendations.md)

- [Jak skonfigurować Azure Policy i zarządzać nimi](../governance/policy/tutorials/create-and-manage.md)

- [Przykładowe zasady platformy Azure dla usługi Virtual Network](/azure/virtual-network/policy-samples)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="711-manage-azure-secrets-securely"></a>7,11: bezpieczne zarządzanie wpisami tajnymi platformy Azure

**Wskazówki**: Użyj tożsamość usługi zarządzanej w połączeniu z Azure Key Vault, aby uprościć i zabezpieczyć poufne zarządzanie zasobami platformy Azure hostowanymi w Virtual Network platformy Azure.

- [Jak przeprowadzić integrację z tożsamościami zarządzanymi przez platformę Azure](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md) 

- [Jak utworzyć Key Vault](/azure/key-vault/quick-create-portal) 

- [Jak zapewnić uwierzytelnianie Key Vault przy użyciu tożsamości zarządzanej](/azure/key-vault/managed-identity)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: eliminowanie nieprzewidzianego narażenia na poświadczenia

**Wskazówki**: Implementuj skaner poświadczeń, aby identyfikować poświadczenia w kodzie. Skaner poświadczeń zachęca również do przemieszczania odnalezionych poświadczeń do bardziej bezpiecznych lokalizacji, takich jak Azure Key Vault.

- [Jak skonfigurować skaner poświadczeń](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

## <a name="data-recovery"></a>Odzyskiwanie danych

*Aby uzyskać więcej informacji, zobacz [test dotyczący zabezpieczeń platformy Azure: odzyskiwanie danych](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: zapewnianie regularnych zautomatyzowanych kopii zapasowych

**Wskazówki**: Użyj Azure Resource Manager do wdrożenia sieci wirtualnej i powiązanych zasobów. Azure Resource Manager zapewnia możliwość eksportowania szablonów, które mogą być używane jako kopie zapasowe do przywracania sieci wirtualnej i powiązanych zasobów.  Użyj Azure Automation, aby regularnie wywoływać interfejs API eksportowania szablonu Azure Resource Manager.

- [Omówienie usługi Azure Resource Manager](../azure-resource-manager/management/overview.md)

- [Przykłady szablonów usługi Azure Resource Manager dla sieci wirtualnej](template-samples.md)

- [Eksport jednego i kilku zasobów do szablonu w Azure Portal](../azure-resource-manager/templates/export-template-portal.md)

- [Grupy zasobów — Eksportowanie szablonu](/rest/api/resources/resourcegroups/exporttemplate)

- [Wprowadzenie do Azure Automation](../automation/automation-intro.md)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: wykonaj kompletne kopie zapasowe systemu i Utwórz kopię zapasową wszystkich kluczy zarządzanych przez klienta

**Wskazówki**: Użyj Azure Resource Manager do wdrożenia sieci wirtualnej i powiązanych zasobów. Azure Resource Manager zapewnia możliwość eksportowania szablonów, które mogą być używane jako kopie zapasowe do przywracania sieci wirtualnej i powiązanych zasobów. Użyj Azure Automation, aby regularnie wywoływać interfejs API eksportowania szablonu Azure Resource Manager. Wykonaj kopię zapasową kluczy zarządzanych przez klienta w ramach Azure Key Vault.

- [Omówienie usługi Azure Resource Manager](../azure-resource-manager/management/overview.md)

- [Przykłady szablonów usługi Azure Resource Manager dla sieci wirtualnej](template-samples.md)

- [Eksport jednego i kilku zasobów do szablonu w Azure Portal](../azure-resource-manager/templates/export-template-portal.md)

- [Grupy zasobów — Eksportowanie szablonu](/rest/api/resources/resourcegroups/exporttemplate)

- [Wprowadzenie do Azure Automation](../automation/automation-intro.md)

- [Jak utworzyć kopię zapasową kluczy magazynu kluczy na platformie Azure](https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: Weryfikuj wszystkie kopie zapasowe, w tym klucze zarządzane przez klienta

**Wskazówki**: okresowe wdrażanie szablonów Azure Resource Manager w odizolowanej subskrypcji i testowanie przywracania kopii zapasowych kluczy zarządzanych przez klienta.

- [Wdrażanie zasobów za pomocą szablonów ARM i Azure Portal](../azure-resource-manager/templates/deploy-portal.md)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: zapewnianie ochrony kopii zapasowych i kluczy zarządzanych przez klienta

**Wskazówki**: Użyj usługi Azure DevOps, aby bezpiecznie przechowywać kod, taki jak niestandardowe definicje Azure Policy i szablony Azure Resource Manager, oraz zarządzać nimi. 

Udziel lub Odmów uprawnień określonym użytkownikom, wbudowanym grupom zabezpieczeń lub grupom zdefiniowanym w Azure Active Directory (Azure AD) w przypadku integracji z usługą Azure DevOps lub Active Directory, jeśli jest zintegrowany z Team Foundation Server.  

Za pomocą kontroli dostępu opartej na rolach (Azure RBAC) można chronić klucze zarządzane przez klienta.   

Włącz ochronę przed usuwaniem i przeczyszczaniem w Key Vault, aby chronić klucze przed przypadkowym lub złośliwym usunięciem.  

- [Jak przechowywać kod w usłudze Azure DevOps](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [Informacje o uprawnieniach i grupach na platformie Azure DevOps](/azure/devops/organizations/security/about-permissions)

- [Jak włączyć ochronę przed usuwaniem i Przeczyszczaniem w programie Key Vault](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal) 

- [Soft delete for Azure Storage blobs](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal) (Usuwanie nietrwałe dla obiektów blob usługi Azure Storage)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

## <a name="incident-response"></a>Reagowanie na zdarzenia

*Aby uzyskać więcej informacji, zobacz temat [Azure Security test: odpowiedź na zdarzenia](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: Tworzenie przewodnika odpowiedzi na zdarzenia

**Wskazówki**: Tworzenie przewodnika odpowiedzi na zdarzenia dla organizacji. Upewnij się, że istnieją zarejestrowane plany reakcji na zdarzenia, które definiują wszystkie role pracowników, a także etapy obsługi zdarzeń/zarządzania z wykrywania do oceny po zdarzeniu.  

- [Wskazówki dotyczące tworzenia własnego procesu reagowania na zdarzenia zabezpieczeń](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Anatomia incydentu centrum Microsoft Security Response](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [Skorzystaj z przewodnika obsługi zdarzeń związanych z bezpieczeństwem programu NIST, aby pomóc w tworzeniu własnego planu reagowania na zdarzenia](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: Tworzenie oceny incydentu i procedury priorytetyzacji

**Wskazówki**: Security Center przypisuje ważność do każdego alertu, aby pomóc w ustaleniu, które alerty należy najpierw zbadać. Ważność jest oparta na tym, jak dobrze Security Center znajduje się w wyszukiwaniu lub analitycznym używanym do wystawiania alertu, a także poziom pewności, że istniało złośliwy wpływ na działanie, które prowadziło do alertu.

Wyraźnie oznaczaj subskrypcje (na przykład produkcyjne lub nieprodukcyjne) przy użyciu tagów i Utwórz system nazewnictwa, aby jasno identyfikować i klasyfikować zasoby platformy Azure, szczególnie te, które przetwarzają dane poufne.  Odpowiedzialność za korygowanie alertów zależy od zagrożenia dla zasobów platformy Azure i środowiska, w którym wystąpiło zdarzenie.

- [Alerty zabezpieczeń w Security Center](../security-center/security-center-alerts-overview.md)

- [Organizowanie zasobów platformy Azure przy użyciu tagów](/azure/azure-resource-manager/resource-group-using-tags)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="103-test-security-response-procedures"></a>10,3: procedury odpowiedzi na zabezpieczenia testowe

**Wskazówki**: przeprowadzanie ćwiczeń w celu przetestowania możliwości reagowania na zdarzenia systemów w regularnych erzeach w celu ochrony zasobów platformy Azure. Zidentyfikuj słabe punkty i przerwy i popraw plan zgodnie z wymaganiami.

- [Przewodnik po publikacji NIST, który umożliwia testowanie, uczenie i wykonywanie programów dla planów i możliwości IT](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: Podaj szczegóły kontaktu dotyczącego zabezpieczeń i Skonfiguruj powiadomienia dotyczące alertów dotyczących zdarzeń związanych z zabezpieczeniami

**Wskazówki**: informacje kontaktowe dotyczące zdarzenia zabezpieczeń będą używane przez firmę Microsoft do skontaktowania się z Tobą, jeśli firma Microsoft Security Response Center (MSRC) wykryje, że dostęp do danych zostały nadane przez nielegalną lub nieautoryzowaną stronę. Przejrzyj zdarzenia po fakcie, aby upewnić się, że problemy zostały rozwiązane.

- [Jak ustawić kontakt z zabezpieczeniami Security Center](../security-center/security-center-provide-security-contact-details.md)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: Uwzględnij alerty zabezpieczeń w systemie odpowiedzi na zdarzenia

**Wskazówki**: eksportowanie alertów i zaleceń dotyczących Security Center przy użyciu funkcji eksportu ciągłego, która pomaga identyfikować zagrożenia dla zasobów platformy Azure. Eksport ciągły umożliwia wyeksportowanie alertów i zaleceń ręcznie lub w stały sposób ciągły. 

Możesz również użyć łącznika danych Security Center do przesyłania strumieniowego alertów do usługi Azure wskaźnikowej.

- [Jak skonfigurować eksport ciągły](../security-center/continuous-export.md)

- [Jak przesłać strumieniowo alerty do usługi Azure wskaźnikowego](../sentinel/connect-azure-security-center.md)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: Automatyzowanie odpowiedzi na alerty zabezpieczeń

**Wskazówki**: Użyj funkcji automatyzacji przepływu pracy w Security Center, aby automatycznie wyzwalać odpowiedzi poprzez "Logic Apps" dotyczące alertów zabezpieczeń i zaleceń dotyczących ochrony zasobów platformy Azure.

- [Jak skonfigurować automatyzację przepływu pracy i Logic Apps](../security-center/workflow-automation.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

## <a name="penetration-tests-and-red-team-exercises"></a>Testy penetracyjne i ćwiczenia typu „red team”

*Aby uzyskać więcej informacji, zobacz test [porównawczy zabezpieczeń platformy Azure: testy penetracji i czerwone ćwiczenia zespołu](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: Przeprowadź regularne testowanie penetracji zasobów platformy Azure i zadbaj o skorygowanie wszystkich krytycznych ustaleń dotyczących zabezpieczeń

**Wskazówki**: Postępuj zgodnie z zasadami firmy Microsoft dotyczącymi zaangażowania, aby upewnić się, że testy penetracji nie naruszają zasad firmy Microsoft. Korzystaj z strategii firmy Microsoft i wykonywania testów na żywo z obsługą tworzenia zespołu, usług i aplikacji w chmurze, które są zarządzane przez firmę Microsoft.

- [Reguły testowania penetracji zaangażowania](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Microsoft Cloud Tworzenie zespołu Red.](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: udostępnione

## <a name="next-steps"></a>Następne kroki

- Zobacz [test porównawczy zabezpieczeń platformy Azure](/azure/security/benchmarks/overview)
- Dowiedz się więcej o [punktach odniesienia zabezpieczeń platformy Azure](/azure/security/benchmarks/security-baselines-overview)
