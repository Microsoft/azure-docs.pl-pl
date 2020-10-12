---
title: Podstawa zabezpieczeń platformy Azure dla Event Grid
description: Linia bazowa zabezpieczeń Event Grid zawiera wskazówki i zasoby dotyczące procedur związanych z wdrażaniem zaleceń dotyczących zabezpieczeń określonych w teście zabezpieczeń platformy Azure.
author: msmbaldwin
ms.service: event-grid
ms.topic: conceptual
ms.date: 08/21/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 581e30508b508d5a36f2d5695043ac7bd782a2d5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89394384"
---
# <a name="azure-security-baseline-for-event-grid"></a>Podstawa zabezpieczeń platformy Azure dla Event Grid

Podstawowa baza danych zabezpieczeń Azure dla Microsoft Azure Event Grid zawiera zalecenia, które pomogą ulepszyć stan bezpieczeństwa wdrożenia. Punkt odniesienia dla tej usługi jest rysowany w [wersji 1,0 usługi Azure Security test](../security/benchmarks/overview.md), która zawiera zalecenia dotyczące sposobu zabezpieczania rozwiązań w chmurze na platformie Azure z naszymi najlepszymi wskazówkami. Aby uzyskać więcej informacji, zobacz [podstawy zabezpieczeń platformy Azure — omówienie](../security/benchmarks/security-baselines-overview.md).

## <a name="network-security"></a>Bezpieczeństwo sieci

*Aby uzyskać więcej informacji, zobacz [wzorzec zabezpieczeń Azure: zabezpieczenia sieci](/azure/security/benchmarks/security-control-network-security).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: Ochrona zasobów platformy Azure w ramach sieci wirtualnych

**Wskazówki**: możesz używać prywatnych punktów końcowych, aby zezwalać na ruch przychodzący bezpośrednio z sieci wirtualnej do Event Grid tematów i domen w bezpiecznym miejscu za pośrednictwem prywatnego linku, bez przechodzenia przez publiczny Internet. Gdy tworzysz prywatny punkt końcowy dla tematu Event Grid lub domeny, zapewnia ona bezpieczną łączność między klientami w sieci wirtualnej i zasobem Event Grid. Do prywatnego punktu końcowego jest przypisany adres IP z zakresu adresów IP sieci wirtualnej. Połączenie między prywatnym punktem końcowym a usługą Event Grid używa bezpiecznego linku prywatnego.

Azure Event Grid obsługuje również publicznej kontroli dostępu opartej na protokole IP do publikowania w tematach i domenach. Za pomocą kontrolek opartych na protokole IP można ograniczyć wydawców do tematu lub domeny tylko do zestawu zatwierdzonego zestawu maszyn i usług w chmurze. Ta funkcja uzupełnia mechanizmy uwierzytelniania obsługiwane przez Event Grid. 

- [Więcej szczegółów na temat Event Grid prywatnych punktów końcowych](network-security.md#private-endpoints)

- [Więcej szczegółów na temat zapory Event Grid IP](network-security.md#ip-firewall)

- [Zabezpieczenia sieci Azure Event Grid](network-security.md) 

- [Omówienie usługi Azure Private Link](../private-link/private-link-overview.md)

- [Grupa zabezpieczeń sieci platformy Azure](../virtual-network/security-overview.md)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1,2: Monitoruj i Rejestruj konfigurację oraz ruch sieci wirtualnych, podsieci i kart sieciowych

**Wskazówki**: Użyj Azure Security Center i postępuj zgodnie z zaleceniami dotyczącymi ochrony sieci, aby zabezpieczyć zasoby Event Grid na platformie Azure. Jeśli używasz usługi Azure Virtual Machines do uzyskiwania dostępu do zasobów Event Grid, Włącz dzienniki przepływu sieciowej grupy zabezpieczeń (sieciowej grupy zabezpieczeń) i Wyślij dzienniki do konta magazynu dla ruchu

wizyjn.

- [Jak włączyć dzienniki przepływu sieciowej grupy zabezpieczeń](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Informacje o zabezpieczeniach sieci zapewnianych przez Azure Security Center](../security-center/security-center-network-recommendations.md)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="13-protect-critical-web-applications"></a>1,3: Ochrona krytycznych aplikacji sieci Web

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone dla aplikacji sieci Web działających na Azure App Service lub zasobach obliczeniowych.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: odmowa komunikacji ze znanymi złośliwymi adresami IP

**Wskazówki**: można skonfigurować zaporę IP dla zasobu Event Grid, aby ograniczyć dostęp za pośrednictwem publicznej sieci Internet tylko z poziomu wybranych adresów IP lub zakresów adresów IP.

Można skonfigurować prywatne punkty końcowe, aby ograniczyć dostęp tylko z wybranych sieci wirtualnych.

Włącz usługę DDoS Protection w tych sieciach wirtualnych, aby chronić przed atakami rozproszonymi typu "odmowa usługi" (DDoS). Użyj Azure Security Center zintegrowanej analizy zagrożeń, aby odmówić komunikacji ze znanymi złośliwymi lub nieużywanymi adresami IP. Aby uzyskać więcej informacji zobacz następujące artykuły: 

- [Jak skonfigurować prywatne punkty końcowe dla tematów Azure Event Grid lub domen](configure-private-endpoints.md)

- [Jak skonfigurować ochronę DDoS](../virtual-network/manage-ddos-protection.md)

- [Aby uzyskać więcej informacji na temat Azure Security Center zintegrowanej analizy zagrożeń](/azure/security-center/security-center-alerts-service-layer)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="15-record-network-packets"></a>1,5: rejestrowanie pakietów sieciowych

**Wskazówki**: Jeśli używasz usługi Azure Virtual Machines do uzyskiwania dostępu do zasobów Event Grid, Włącz dzienniki przepływu sieciowej grupy zabezpieczeń (sieciowej grupy zabezpieczeń) i Wyślij dzienniki do konta magazynu na potrzeby inspekcji ruchu. Możesz również wysłać dzienniki przepływu sieciowej grupy zabezpieczeń do obszaru roboczego Log Analytics i użyć Analiza ruchu, aby uzyskać wgląd w przepływ ruchu w chmurze platformy Azure. Niektóre zalety Analiza ruchu to możliwość wizualizacji aktywności sieciowej i identyfikowania aktywnych punktów, identyfikowania zagrożeń bezpieczeństwa, zrozumienia wzorców przepływu ruchu i wyznaczania konfiguracji sieci.

Uwaga zasady sieciowe są domyślnie wyłączone, gdy prywatne punkty końcowe są tworzone dla Event Grid tak, aby przekroczyć przepływ pracy.

W razie potrzeby w celu badania nietypowego działania Włącz funkcję przechwytywania pakietów Network Watcher.

- [Jak włączyć dzienniki przepływu sieciowej grupy zabezpieczeń](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Jak włączyć i używać Analiza ruchu](../network-watcher/traffic-analytics.md)

- [Jak włączyć Network Watcher](../network-watcher/network-watcher-create.md)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: wdrażanie opartych na sieci systemów zapobiegania wykrywaniem i dostępem intruzów (identyfikatorów/adresów IP)

**Wskazówki**: wybierz ofertę z portalu Azure Marketplace, która obsługuje funkcje identyfikatorów/adresów IP z możliwościami inspekcji ładunku.  Gdy inspekcja ładunku nie jest wymagana, można użyć analizy zagrożeń zapory platformy Azure. Filtrowanie oparte na analizie zagrożeń dla zapory platformy Azure służy do wyzwalania alertów dotyczących i/lub blokowania ruchu do i ze znanych złośliwych adresów IP i domen. Adresy IP i domeny pochodzą z kanału informacyjnego analizy zagrożeń firmy Microsoft.

W celu wykrycia i/lub zablokowania złośliwego ruchu należy wdrożyć wybrane rozwiązanie zapory na wszystkich granicach sieci w organizacji.

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

- [Jak wdrożyć zaporę platformy Azure](../firewall/tutorial-firewall-deploy-portal.md)

- [Jak skonfigurować alerty za pomocą zapory platformy Azure](../firewall/threat-intel.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="17-manage-traffic-to-web-applications"></a>1,7: zarządzanie ruchem do aplikacji sieci Web

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone dla aplikacji sieci Web działających na Azure App Service lub zasobach obliczeniowych.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: Minimalizacja złożoności i kosztów administracyjnych reguł zabezpieczeń sieci

**Wskazówki**: dla zasobów w sieciach wirtualnych, które wymagają dostępu do zasobów Azure Event Grid, użyj Virtual Network tagów usługi, aby zdefiniować kontrolę dostępu do sieci w grupach zabezpieczeń sieci lub zaporze platformy Azure. Podczas tworzenia reguł zabezpieczeń można użyć tagów usługi zamiast konkretnych adresów IP. Określając nazwę tagu usługi (na przykład AzureEventGrid) w odpowiednim polu źródłowym lub docelowym reguły, można zezwolić na ruch dla odpowiedniej usługi lub go odrzucić. Firma Microsoft zarządza prefiksami adresów, które obejmują tag usługi, i automatycznie aktualizuje tag usługi jako adresy.

- [Jak używać tagu usługi dla Azure Event Grid](network-security.md#service-tags)

- [Aby uzyskać więcej informacji na temat używania tagów usługi](../virtual-network/service-tags-overview.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: Obsługa standardowych konfiguracji zabezpieczeń dla urządzeń sieciowych

**Wskazówki**: Definiowanie i implementowanie standardowych konfiguracji zabezpieczeń dla zasobów sieciowych skojarzonych z przestrzeniami nazw Azure Event Grid przy użyciu Azure Policy. Użyj aliasów Azure Policy w przestrzeniach nazw "Microsoft. EventGrid" i "Microsoft. Network", aby utworzyć niestandardowe zasady inspekcji lub wymuszania konfiguracji sieci Event Grid zasobów. 

Mogą również używać wbudowanych definicji zasad związanych z Azure Event Grid, takich jak domeny Azure Event Grid powinny używać prywatnych linków — Azure Event Grid tematy powinny używać prywatnych linksAzure
- [wbudowane zasady dla Event Grid zasobów](../governance/policy/samples/built-in-policies.md#event-grid)

- [Jak skonfigurować Azure Policy i zarządzać nimi](../governance/policy/tutorials/create-and-manage.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="110-document-traffic-configuration-rules"></a>1,10: udokumentowanie reguł konfiguracji ruchu

**Wskazówki**: Użyj tagów dla zasobów sieciowych skojarzonych z zasobami Azure Event Grid, aby logicznie zorganizować je w taksonomię.

- [Tworzenie i używanie tagów](/azure/azure-resource-manager/resource-group-using-tags)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: Użyj zautomatyzowanych narzędzi do monitorowania konfiguracji zasobów sieciowych i wykrywania zmian

**Wskazówki**: Użyj dziennika aktywności platformy Azure do monitorowania konfiguracji zasobów sieciowych i wykrywania zmian zasobów sieciowych związanych z Azure Event Grid. Tworzenie alertów w ramach Azure Monitor, które będą wyzwalane po wprowadzeniu zmian w krytycznych zasobach sieciowych.

- [Jak wyświetlać i pobierać zdarzenia dziennika aktywności platformy Azure](/azure/azure-monitor/platform/activity-log-view)

- [Jak utworzyć alerty w Azure Monitor](../azure-monitor/platform/alerts-activity-log.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

## <a name="logging-and-monitoring"></a>Rejestrowanie i monitorowanie

*Aby uzyskać więcej informacji, zobacz temat [Azure Security test: rejestrowanie i monitorowanie](/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="22-configure-central-security-log-management"></a>2,2: Skonfiguruj centralne zarządzanie dziennikami zabezpieczeń

**Wskazówki**: pobieranie dzienników przy użyciu Azure monitor do agregowania danych zabezpieczeń wygenerowanych przez Azure Event Grid. W ramach Azure Monitor należy używać Log Analytics obszarów roboczych do wykonywania zapytań i wykonywania analiz oraz używania kont magazynu do przechowywania długoterminowego/archiwizowania. Alternatywnie możesz włączyć i zarejestrować dane na platformie Azure wskaźnikowej lub zdarzeniu zabezpieczeń innych firm (SIEM).

- [Jak włączyć dzienniki diagnostyczne dla Azure Event Grid](diagnostic-logs.md)

- [Jak dołączyć wskaźnik na platformie Azure](../sentinel/quickstart-onboard.md)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: Włączanie rejestrowania inspekcji dla zasobów platformy Azure

**Wskazówki**: ustawienia diagnostyczne umożliwiają użytkownikom Event Grid przechwytywanie i wyświetlanie dzienników błędów publikowania i dostarczania na koncie magazynu, centrum zdarzeń lub w obszarze roboczym log Analytics.

- [Włączanie dzienników diagnostycznych dla tematów lub domen usługi Azure Event Grid](enable-diagnostic-logs-topic.md)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: Zbierz dzienniki zabezpieczeń z systemów operacyjnych

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone do zasobów obliczeniowych.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="25-configure-security-log-storage-retention"></a>2,5: Konfigurowanie przechowywania magazynu dzienników zabezpieczeń

**Wskazówki**: w Azure monitor ustawić okres przechowywania dziennika dla log Analytics obszarów roboczych skojarzonych z zasobami Azure Event Grid zgodnie z regulacjami zgodności w organizacji.

- [Jak ustawić parametry przechowywania dziennika](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="26-monitor-and-review-logs"></a>2,6: dzienniki monitorowania i przeglądania

**Wskazówki**: analizowanie i monitorowanie dzienników pod kątem nietypowego zachowania i regularne przeglądanie wyników Azure Event Grid. Użyj Azure Monitor i Log Analytics obszaru roboczego do przeglądania dzienników i wykonywania zapytań dotyczących danych dziennika.

Alternatywnie możesz włączyć i dołączyć dane do usługi Azure wskaźnikowej lub SIEM innej firmy. 

- [Włączanie dzienników diagnostycznych dla tematów lub domen usługi Azure Event Grid](enable-diagnostic-logs-topic.md)

- [Jak wykonywać zapytania dotyczące Azure Event Grid w Log Analytics obszarach roboczych](diagnostic-logs.md)

- [Jak dołączyć wskaźnik na platformie Azure](../sentinel/quickstart-onboard.md)

- [Wprowadzenie do Log Analytics zapytań](../azure-monitor/log-query/get-started-portal.md)

- [Jak wykonywać niestandardowe zapytania w Azure Monitor](../azure-monitor/log-query/get-started-queries.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: Włączanie alertów dla nietypowych działań

**Wskazówki**: Włączanie ustawień diagnostycznych w usłudze Event Grid w celu uzyskania dostępu do dzienników błędów publikowania i dostarczania. Dzienniki aktywności, które są automatycznie dostępne, obejmują źródło zdarzeń, datę, użytkownika, sygnaturę czasową, adresy źródłowe, adresy docelowe i inne przydatne elementy. Dzienniki mogą być wysyłane do obszaru roboczego Log Analytics. Użyj Azure Security Center z Log Analytics do monitorowania i wysyłania alertów dotyczących nietypowej aktywności znalezionych w dziennikach i zdarzeniach zabezpieczeń. 

Można również tworzyć alerty dotyczące Azure Event Grid metryk i operacji dziennika aktywności. Możesz tworzyć alerty dotyczące metryk publikowania i dostarczania dla zasobów Azure Event Grid (tematów i domen). 

Ponadto możesz dołączyć obszar roboczy Log Analytics do funkcji wskaźnikowej platformy Azure, ponieważ oferuje ona rozwiązanie zautomatyzowanej reakcji (o) aranżacji zabezpieczeń. Pozwala to na tworzenie i używanie automatycznych rozwiązań elementy PlayBook w celu korygowania problemów z zabezpieczeniami.

- [Jak włączyć dzienniki diagnostyczne dla tematów Azure Event Grid lub domen](enable-diagnostic-logs-topic.md)

- [Ustawianie alertów dotyczących Azure Event Grid metryk i dzienników aktywności](set-alerts.md)

- [Szczegóły schematu dziennika diagnostyki Event Grid](diagnostic-logs.md)

- [Tworzenie i wyświetlanie alertów dzienników oraz zarządzanie nimi za pomocą Azure Monitor](../azure-monitor/platform/alerts-log.md)

- [Jak dołączyć wskaźnik na platformie Azure](../sentinel/quickstart-onboard.md)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="28-centralize-anti-malware-logging"></a>2,8: scentralizowanie rejestrowania chroniącego przed złośliwym oprogramowaniem

**Wskazówki**: nie dotyczy; Azure Event Grid nie przetwarza ani nie tworzy dzienników związanych z oprogramowaniem chroniącym przed złośliwym kodem.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="29-enable-dns-query-logging"></a>2,9: Włączanie rejestrowania zapytań DNS

**Wskazówki**: nie dotyczy; Azure Event Grid nie przetwarza ani nie tworzy dzienników związanych z usługą DNS.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="210-enable-command-line-audit-logging"></a>2,10: Włączanie rejestrowania inspekcji w wierszu polecenia

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone do zasobów obliczeniowych.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

## <a name="identity-and-access-control"></a>Tożsamość i kontrola dostępu

*Aby uzyskać więcej informacji, zobacz [test dotyczący zabezpieczeń platformy Azure: tożsamość i kontrola dostępu](/azure/security/benchmarks/security-control-identity-access-control).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: obsługa spisu kont administracyjnych

**Wskazówki**: Azure Event Grid umożliwia kontrolę poziomu dostępu nadawanego różnym użytkownikom w celu wykonywania różnych operacji zarządzania, takich jak subskrypcje zdarzeń listy, tworzenie nowych i generowanie kluczy. Event Grid używa kontroli dostępu opartej na rolach (RBAC) na platformie Azure. Event Grid obsługuje wbudowane role oraz role niestandardowe.

Kontrola dostępu oparta na rolach (RBAC) na platformie Azure umożliwia zarządzanie dostępem do zasobów platformy Azure za pomocą przypisań ról. Te role można przypisać do użytkowników, grup jednostek usługi i zarządzanych tożsamości. Istnieją wstępnie zdefiniowane wbudowane role dla niektórych zasobów i te role można spisować lub odpytać za pomocą narzędzi takich jak interfejs wiersza polecenia platformy Azure, Azure PowerShell lub Azure Portal.

- [Autoryzowanie dostępu do zasobów Event Grid](security-authorization.md)

- [Jak uzyskać rolę katalogu w usłudze Azure AD przy użyciu programu PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

- [Jak uzyskać członków roli katalogu w usłudze Azure AD przy użyciu programu PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="32-change-default-passwords-where-applicable"></a>3,2: Zmień domyślne hasła, jeśli ma to zastosowanie

**Wskazówki**: zarządzanie dostępem do zasobów Event Grid jest kontrolowane przez Azure Active Directory (AD). Usługa Azure AD nie ma koncepcji domyślnych haseł.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: Użyj dedykowanych kont administracyjnych

**Wskazówki**: Tworzenie standardowych procedur operacyjnych dotyczących korzystania z dedykowanych kont administracyjnych.

Możesz również włączyć dostęp just in Time przy użyciu Azure AD Privileged Identity Management i Azure Resource Manager.

Event Grid może włączyć tożsamość usługi zarządzanej dla tematów lub domen w usłudze Azure Event Grid i używać jej do przekazywania zdarzeń do obsługiwanych miejsc docelowych, takich jak Service Bus kolejek i tematów, centrów zdarzeń i kont magazynu. Token sygnatury dostępu współdzielonego (SAS) służy do publikowania zdarzeń do Azure Event Grid. Utwórz standardową procedurę operacyjną między dostępem do zdarzeń, przekazywaniem i publikowaniem z tymi kontami.

- [Uwierzytelnianie dostarczania zdarzeń do programów obsługi zdarzeń (Azure Event Grid)](security-authentication.md)

- [Uwierzytelnianie klientów publikowania (Azure Event Grid)](security-authenticate-publishing-clients.md)

- [Dowiedz się więcej o Privileged Identity Management](/azure/active-directory/privileged-identity-management/)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3,4: Korzystaj z logowania jednokrotnego (SSO) z usługą Azure Active Directory

**Wskazówki**: nie dotyczy; Usługa Event Grid nie obsługuje logowania jednokrotnego.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Użyj uwierzytelniania wieloskładnikowego, aby uzyskać dostęp oparty na Azure Active Directory

**Wskazówki**: nie dotyczy; Usługa Event Grid nie korzysta z uwierzytelniania wieloskładnikowego

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: Używaj dedykowanych maszyn (uprzywilejowany dostęp do stacji roboczych) dla wszystkich zadań administracyjnych

**Wskazówki**: nie dotyczy; żadne scenariusze Event Grid nie wymagają uprzywilejowanych stacji roboczych dostępu. 

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: Rejestruj i Ostrzegaj o podejrzanych działaniach z kont administracyjnych

**Wskazówki**: Użyj Azure Active Directory raportów i monitorowania zabezpieczeń, aby wykrywać, kiedy w środowisku występuje podejrzane lub niebezpieczne działania. Użyj Azure Security Center, aby monitorować działania związane z tożsamościami i dostępem.

- [Identyfikowanie użytkowników usługi Azure AD oflagowanych w celu działania ryzykownego](/azure/active-directory/reports-monitoring/concept-user-at-risk)

- [Jak monitorować działania związane z tożsamościami i dostępem użytkowników w Azure Security Center](../security-center/security-center-identity-access.md)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="38-manage-azure-resources-only-from-approved-locations"></a>3,8: Zarządzaj zasobami platformy Azure tylko z zatwierdzonych lokalizacji

**Wskazówki**: nie dotyczy. Event Grid nie używa usługi Azure AD do uwierzytelniania klientów publikowania zdarzeń; obsługuje ona uwierzytelnianie za pośrednictwem kluczy SAS.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="39-use-azure-active-directory"></a>3,9: Użyj Azure Active Directory

**Wskazówki**: Użyj Azure Active Directory (Azure AD) jako centralnego systemu uwierzytelniania i autoryzacji. Usługa Azure AD chroni dane przy użyciu silnego szyfrowania danych przechowywanych i przesyłanych. Usługa Azure AD również Sole, skróty i bezpieczne przechowywanie poświadczeń użytkownika.

Event Grid może włączyć tożsamość usługi zarządzanej dla tematów lub domen w usłudze Azure Event Grid i używać jej do przekazywania zdarzeń do obsługiwanych miejsc docelowych, takich jak Service Bus kolejek i tematów, centrów zdarzeń i kont magazynu. Token sygnatury dostępu współdzielonego (SAS) służy do publikowania zdarzeń do Azure Event Grid. 

- [Uwierzytelnianie dostarczania zdarzeń do programów obsługi zdarzeń (Azure Event Grid)](security-authentication.md)

- [Uwierzytelnianie klientów publikowania (Azure Event Grid)](security-authenticate-publishing-clients.md)

- [Jak utworzyć i skonfigurować wystąpienie usługi Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: regularnie Przeglądaj i Uzgodnij dostęp użytkowników

**Wskazówki**: usługa Azure AD udostępnia dzienniki, które ułatwiają odnajdywanie starych kont. Ponadto za pomocą przeglądów tożsamości i dostępu w usłudze Azure AD można efektywnie zarządzać członkostwem w grupach, dostępem do aplikacji dla przedsiębiorstw i przypisaniami ról. Dostęp użytkowników może być regularnie przeglądany, aby upewnić się, że tylko Ci użytkownicy mają ciągły dostęp. 
 
Użyj Azure Active Directory (AD) Privileged Identity Management (PIM) do generowania dzienników i alertów w przypadku wystąpienia podejrzanych lub niebezpiecznych działań w środowisku.

- [Informacje o raportowaniu usługi Azure AD](/azure/active-directory/reports-monitoring)

- [Jak korzystać z przeglądów tożsamości i dostępu w usłudze Azure AD](../active-directory/governance/access-reviews-overview.md)

- [Wdróż Azure AD Privileged Identity Management (PIM)](/azure/active-directory/privileged-identity-management/pim-deployment-plan)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: Monitor próbuje uzyskać dostęp do zdezaktywowanych poświadczeń

**Wskazówki**: masz dostęp do źródeł zdarzeń związanych z logowaniem do usługi Azure AD, inspekcją i ryzykiem związanym z dziennikami, które umożliwiają integrację z dowolnym narzędziem Siem/monitoring.

Proces ten można usprawnić, tworząc ustawienia diagnostyczne dla kont użytkowników usługi Azure AD i wysyłając dzienniki inspekcji i dzienniki logowania do obszaru roboczego Log Analytics. Żądane alerty można skonfigurować w obszarze roboczym Log Analytics.

- [Jak zintegrować dzienniki aktywności platformy Azure z Azure Monitor](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: odchylenia zachowania podczas logowania do konta

**Wskazówki**: użyj funkcji Azure AD Identity Protection, aby skonfigurować automatyczne odpowiedzi na wykryte podejrzane działania związane z tożsamościami użytkowników. Możesz również pozyskać dane do usługi Azure wskaźnikowej na potrzeby dalszej analizy.
 
 
 
- [Jak wyświetlić ryzykowne logowania usługi Azure AD](/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

- [Jak skonfigurować i włączyć zasady dotyczące ryzyka związanego z ochroną tożsamości](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Jak dołączyć wskaźnik na platformie Azure](../sentinel/quickstart-onboard.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: Zapewnij firmie Microsoft dostęp do odpowiednich danych klienta w scenariuszach pomocy technicznej

**Wskazówki**: nie dotyczy; Usługa Event Grid nie obsługuje obecnie Skrytka klienta.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

## <a name="data-protection"></a>Ochrona danych

*Aby uzyskać więcej informacji, zobacz [Azure Security test: Data Protection](/azure/security/benchmarks/security-control-data-protection).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: przechowywanie spisu poufnych informacji

**Wskazówki**: Użyj tagów, aby pomóc w śledzeniu zasobów platformy Azure, które przechowują lub przetwarzają informacje poufne.
 
 
 
- [Tworzenie i używanie tagów](/azure/azure-resource-manager/resource-group-using-tags)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: Izoluj systemy przechowujące lub przetwarzające informacje poufne

**Wskazówki**: Zaimplementuj izolację przy użyciu osobnych subskrypcji i grup zarządzania dla poszczególnych domen zabezpieczeń, takich jak typ środowiska i poziom czułości danych. Możesz ograniczyć poziom dostępu do zasobów platformy Azure, których wymagają aplikacje i środowiska korporacyjne. Dostęp do zasobów platformy Azure można kontrolować za pośrednictwem Azure Active Directory RBAC.

- [Jak utworzyć dodatkowe subskrypcje platformy Azure](/azure/billing/billing-create-subscription)

- [Tworzenie grup zarządzania](../governance/management-groups/create.md)

- [Tworzenie i używanie tagów](/azure/azure-resource-manager/resource-group-using-tags)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: Monitoruj i blokuj nieautoryzowany transfer informacji poufnych

**Wskazówki**: dla podstawowej platformy, która jest zarządzana przez firmę Microsoft, firma Microsoft traktuje całą zawartość klienta jako poufną i nadaje im dużą długość, aby chronić przed utratą i narażeniem danych przez klienta. Aby zapewnić bezpieczeństwo danych klienta na platformie Azure, firma Microsoft wdrożyła i utrzymuje pakiet niezawodnych kontroli i możliwości ochrony danych.

- [Zrozumienie ochrony danych klientów na platformie Azure](../security/fundamentals/protection-customer-data.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: udostępnione

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: Szyfruj wszystkie poufne informacje podczas przesyłania

**Wskazówki**: Azure Event Grid wymaga protokołu HTTPS do publikowania i obsługuje protokół https w celu dostarczania zdarzeń do punktu końcowego elementu webhook. Na platformie Azure Global, Event Grid obsługuje zarówno wersje TLS 1,1, jak i 1,2, ale zdecydowanie zalecamy użycie wersji 1,2. W chmurach narodowych, takich jak Azure Government i platforma Azure obsługiwana przez firmę 21Vianet w Chinach, Event Grid obsługuje tylko 1,2 wersję protokołu TLS. 

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: Użyj aktywnego narzędzia do odnajdywania, aby identyfikować poufne dane

**Wskazówki**: funkcje identyfikacji, klasyfikacji i zapobiegania utracie danych nie są jeszcze dostępne dla Azure Event Grid. Zaimplementuj rozwiązanie innych firm, jeśli jest to niezbędne do celów zgodności.

W przypadku podstawowej platformy zarządzanej przez firmę Microsoft Firma Microsoft traktuje całą zawartość klienta jako poufną i nadaje im dużą długość, aby chronić przed utratą i narażeniem danych przez klienta. Aby zapewnić bezpieczeństwo danych klienta na platformie Azure, firma Microsoft wdrożyła i utrzymuje pakiet niezawodnych kontroli i możliwości ochrony danych.

- [Zrozumienie ochrony danych klientów na platformie Azure](../security/fundamentals/protection-customer-data.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4,6: Korzystaj z usługi Azure RBAC, aby zarządzać dostępem do zasobów

**Wskazówki**: Azure Event Grid obsługuje używanie Azure Active Directory (AD) do autoryzacji żądań Event Grid zasobów. Za pomocą usługi Azure AD można używać kontroli dostępu opartej na rolach (RBAC) do udzielania uprawnień podmiotowi zabezpieczeń, który może być użytkownikiem lub podmiotem usługi aplikacji.

- [Autoryzowanie dostępu do zasobów Event Grid](security-authorization.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: Rejestruj i Ostrzegaj o zmianach krytycznych zasobów platformy Azure

**Wskazówki**: Użyj Azure monitor z dziennikiem aktywności platformy Azure, aby utworzyć alerty dla sytuacji, w których zmiany są wprowadzane do wystąpień produkcyjnych zasobów Azure Event Grid i innych krytycznych lub powiązanych zasobów.

- [Jak utworzyć alerty dla zdarzeń dziennika aktywności platformy Azure](../azure-monitor/platform/alerts-activity-log.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

## <a name="vulnerability-management"></a>Zarządzanie lukami w zabezpieczeniach

*Aby uzyskać więcej informacji, zobacz temat [Azure Security test: Zarządzanie lukami w zabezpieczeniach](/azure/security/benchmarks/security-control-vulnerability-management).*

### <a name="53-deploy-an-automated-patch-management-solution-for-third-party-software-titles"></a>5,3: Wdróż automatyczne rozwiązanie do zarządzania poprawkami dla tytułów oprogramowania innych firm

**Wskazówki**: nie dotyczy; te wytyczne są przeznaczone dla zasobów obliczeniowych.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: porównanie luk w zabezpieczeniach z tyłu do tyłu

**Wskazówki**: nie dotyczy; te wytyczne są przeznaczone dla zasobów obliczeniowych.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: Użyj procesu oceny ryzyka, aby określić priorytety korygowania odkrytych luk w zabezpieczeniach

**Wskazówki**: nie dotyczy; te wytyczne są przeznaczone dla zasobów obliczeniowych.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

## <a name="inventory-and-asset-management"></a>Zarządzanie magazynem i zasobami

*Aby uzyskać więcej informacji, zobacz temat [Azure Security test: Inventory and Asset Management](/azure/security/benchmarks/security-control-inventory-asset-management).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: Użyj rozwiązania automatycznego odnajdywania zasobów

**Wskazówki**: nie dotyczy; te wytyczne są przeznaczone dla zasobów obliczeniowych.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="62-maintain-asset-metadata"></a>6,2: Konserwowanie metadanych zasobów

**Wskazówki**: stosowanie tagów do zasobów platformy Azure, dzięki czemu metadane są logicznie zorganizowane w taksonomię.

- [Tworzenie i używanie tagów](/azure/azure-resource-manager/resource-group-using-tags)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: Usuń nieautoryzowane zasoby platformy Azure

**Wskazówki**: używanie tagowania, grup zarządzania i oddzielnych subskrypcji w miarę potrzeb w celu organizowania i śledzenia zasobów. Regularnie Uzgadniaj spis i zapewnij, że nieautoryzowane zasoby są usuwane z subskrypcji w odpowiednim czasie.
 
 
 
- [Jak utworzyć dodatkowe subskrypcje platformy Azure](/azure/billing/billing-create-subscription)

- [Tworzenie grup zarządzania](../governance/management-groups/create.md)

- [Tworzenie i używanie tagów](/azure/azure-resource-manager/resource-group-using-tags)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6,4: Definiowanie i obsługa spisu zatwierdzonych zasobów platformy Azure

**Wskazówki**: Tworzenie spisu zatwierdzonych zasobów platformy Azure oraz zatwierdzonego oprogramowania do zasobów obliczeniowych zgodnie z potrzebami organizacji.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: Monitoruj niezatwierdzone zasoby platformy Azure

**Wskazówki**: Użyj Azure Policy, aby wprowadzić ograniczenia dotyczące typu zasobów, które można utworzyć w subskrypcjach klientów, przy użyciu następujących wbudowanych definicji zasad:

* Niedozwolone typy zasobów
* Dozwolone typy zasobów

Ponadto Użyj grafu zasobów platformy Azure do wykonywania zapytań/odnajdywania zasobów w ramach subskrypcji.

- [Jak skonfigurować Azure Policy i zarządzać nimi](../governance/policy/tutorials/create-and-manage.md)
- [Jak tworzyć zapytania za pomocą usługi Azure Graph](../governance/resource-graph/first-query-portal.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: Monitoruj niezatwierdzone aplikacje oprogramowania w ramach zasobów obliczeniowych

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone do zasobów obliczeniowych.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: Usuń niezatwierdzone zasoby platformy Azure i aplikacje oprogramowania

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone do zasobów obliczeniowych.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="68-use-only-approved-applications"></a>6,8: Używaj tylko zatwierdzonych aplikacji

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone do zasobów obliczeniowych.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="69-use-only-approved-azure-services"></a>6,9: Używaj tylko zatwierdzonych usług platformy Azure

**Wskazówki**: Użyj Azure Policy, aby wprowadzić ograniczenia dotyczące typu zasobów, które można utworzyć w subskrypcjach klientów, przy użyciu następujących wbudowanych definicji zasad:

* Niedozwolone typy zasobów
* Dozwolone typy zasobów

Ponadto Użyj grafu zasobów platformy Azure do wykonywania zapytań/odnajdywania zasobów w ramach subskrypcji.

- [Jak skonfigurować Azure Policy i zarządzać nimi](../governance/policy/tutorials/create-and-manage.md)

- [Jak tworzyć zapytania za pomocą usługi Azure Graph](../governance/resource-graph/first-query-portal.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6,10: przechowywanie spisu zatwierdzonych tytułów oprogramowania

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone do zasobów obliczeniowych.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: Ogranicz możliwość korzystania przez użytkowników z Azure Resource Manager

**Wskazówki**: Użyj dostępu warunkowego usługi Azure AD, aby ograniczyć możliwość korzystania z usługi Azure Resources przez użytkowników w celu skonfigurowania "blokowania dostępu" dla aplikacji "Microsoft Azure Management".
 
 
 
- [ Jak skonfigurować dostęp warunkowy, aby blokować dostęp do usługi Azure Resources](../role-based-access-control/conditional-access-azure-management.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="612-limit-users-ability-to-execute-scripts-in-compute-resources"></a>6,12: Ogranicz możliwość wykonywania skryptów w zasobach obliczeniowych przez użytkowników

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone do zasobów obliczeniowych.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: fizyczne lub logiczne rozdzielenie aplikacji wysokiego ryzyka

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone dla aplikacji sieci Web działających na Azure App Service lub zasobach obliczeniowych.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

## <a name="secure-configuration"></a>Bezpieczna konfiguracja

*Aby uzyskać więcej informacji, zobacz temat [Azure Security test: bezpieczna konfiguracja](/azure/security/benchmarks/security-control-secure-configuration).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: Ustanów bezpieczne konfiguracje dla wszystkich zasobów platformy Azure

**Wskazówki**: Definiowanie i implementowanie standardowych konfiguracji zabezpieczeń dla usługi Azure Event Grid przy użyciu Azure Policy. Użyj aliasów Azure Policy w przestrzeni nazw "Microsoft. EventGrid", aby utworzyć zasady niestandardowe do inspekcji lub wymuszania konfiguracji usług Azure Event Grid.

Azure Resource Manager może eksportować szablon w JavaScript Object Notation (JSON), który powinien zostać sprawdzony w celu upewnienia się, że konfiguracje spełniają wymagania dotyczące zabezpieczeń organizacji przed wdrożeniami.

- [Jak wyświetlić dostępne aliasy Azure Policy](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

- [Jak skonfigurować Azure Policy i zarządzać nimi](../governance/policy/tutorials/create-and-manage.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: Ustanów bezpieczne konfiguracje systemów operacyjnych

**Wskazówki**: nie dotyczy; te wytyczne są przeznaczone dla zasobów obliczeniowych.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: obsługa bezpiecznych konfiguracji zasobów platformy Azure

**Wskazówki**: Użyj Azure Policy [Odmów] i [Wdróż, jeśli nie istnieje], aby wymusić bezpieczne ustawienia dla zasobów platformy Azure. Ponadto za pomocą szablonów Azure Resource Manager można zachować konfigurację zabezpieczeń zasobów platformy Azure wymaganych przez organizację. 

- [Zrozumienie efektów Azure Policy](../governance/policy/concepts/effects.md)

- [Tworzenie zasad i zarządzanie nimi w celu wymuszania zgodności](../governance/policy/tutorials/create-and-manage.md)

- [Przegląd szablonów Azure Resource Manager](../azure-resource-manager/templates/overview.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: Zachowaj konfiguracje bezpiecznego systemu operacyjnego

**Wskazówki**: nie dotyczy; te wytyczne są przeznaczone dla zasobów obliczeniowych.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: bezpiecznie przechowuj konfigurację zasobów platformy Azure

**Wskazówki**: Jeśli używasz niestandardowych definicji Azure Policy dla Event Grid lub powiązanych zasobów, użyj Azure Repos, aby bezpiecznie przechowywać kod i zarządzać nim.

- [Jak przechowywać kod w usłudze Azure DevOps](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [Dokumentacja Azure Repos](https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: bezpieczne przechowywanie niestandardowych obrazów systemu operacyjnego

**Wskazówki**: nie dotyczy; te wytyczne są przeznaczone dla zasobów obliczeniowych.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: Wdrażanie narzędzi do zarządzania konfiguracją dla zasobów platformy Azure

**Wskazówki**: Użyj aliasów Azure Policy w przestrzeni nazw "Microsoft. EventGrid", aby utworzyć zasady niestandardowe na potrzeby alertów, inspekcji i wymuszania konfiguracji systemu. Dodatkowo opracowuj proces i potok na potrzeby zarządzania wyjątkami zasad.

- [Jak skonfigurować Azure Policy i zarządzać nimi](../governance/policy/tutorials/create-and-manage.md)

- [Jak używać aliasów](../governance/policy/concepts/definition-structure.md#aliases)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7,8: Wdrażanie narzędzi do zarządzania konfiguracją dla systemów operacyjnych

**Wskazówki**: nie dotyczy; te wytyczne są przeznaczone dla zasobów obliczeniowych.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: Zaimplementuj automatyczne monitorowanie konfiguracji dla zasobów platformy Azure

**Wskazówki**: Użyj Azure Security Center, aby przeprowadzić skanowanie linii bazowej dla zasobów platformy Azure. Ponadto należy używać Azure Policy do powiadamiania i inspekcji konfiguracji zasobów platformy Azure.

- [Jak skorygować zalecenia w Azure Security Center](../security-center/security-center-remediate-recommendations.md)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: Zaimplementuj automatyczne monitorowanie konfiguracji dla systemów operacyjnych

**Wskazówki**: nie dotyczy; te wytyczne są przeznaczone dla zasobów obliczeniowych.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="711-manage-azure-secrets-securely"></a>7,11: bezpieczne zarządzanie wpisami tajnymi platformy Azure

**Wskazówki**: Event Grid używa tokenu sygnatury dostępu współdzielonego (SAS) do publikowania zdarzeń do Event Grid tematów lub domen. Generowanie tokenów SAS z dostępem tylko do zasobów, które są konieczne w ograniczonym przedziale czasu.

Korzystaj z tożsamości zarządzanych w połączeniu z Azure Key Vault, aby uprościć zarządzanie tajnymi aplikacjami w chmurze.

- [Uwierzytelnianie klientów publikowania (Azure Event Grid)](security-authenticate-publishing-clients.md)

- [Jak używać tożsamości zarządzanych dla zasobów platformy Azure](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

- [Jak utworzyć Key Vault](/azure/key-vault/quick-create-portal)

- [Jak przeprowadzić uwierzytelnianie w Key Vault](../key-vault/general/authentication.md)

- [Jak przypisać zasady dostępu Key Vault](../key-vault/general/assign-access-policy-portal.md)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: bezpieczne i automatyczne zarządzanie tożsamościami

**Wskazówki**: Event Grid mogą włączyć tożsamość usługi zarządzanej dla tematów lub domen w usłudze Azure Event Grid. Służy do przekazywania zdarzeń do obsługiwanych miejsc docelowych, takich jak kolejki Service Bus i tematy, Centra zdarzeń i konta magazynu.

- [Dostarczanie zdarzeń przy użyciu tożsamości zarządzanej](managed-service-identity.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: eliminowanie nieprzewidzianego narażenia na poświadczenia

**Wskazówki**: Implementuj skaner poświadczeń, aby identyfikować poświadczenia w kodzie. Skaner poświadczeń zachęca również do przemieszczania odnalezionych poświadczeń do bardziej bezpiecznych lokalizacji, takich jak Azure Key Vault. 

- [Jak skonfigurować skaner poświadczeń](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

## <a name="malware-defense"></a>Ochrona przed złośliwym oprogramowaniem

*Aby uzyskać więcej informacji, zobacz temat [Azure Security test: Obrona złośliwego oprogramowania](/azure/security/benchmarks/security-control-malware-defense).*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: przeskanuj pliki przed przekazaniem do zasobów platformy Azure, które nie są obliczeniowe

**Wskazówki**: oprogramowanie chroniące przed złośliwym oprogramowaniem firmy Microsoft jest włączone na podstawowym hoście obsługującym usługi platformy Azure (na przykład Azure Event Grid), ale nie jest ono uruchamiane w treści klienta.

Ponosisz odpowiedzialność za wstępne skanowanie zawartości przekazywanej do zasobów platformy Azure, które nie są obliczeniowe. Firma Microsoft nie może uzyskać dostępu do danych klienta i w związku z tym nie może przeprowadzić skanowania zawartości klienta przez oprogramowanie chroniące przed złośliwym kodem.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

## <a name="data-recovery"></a>Odzyskiwanie danych

*Aby uzyskać więcej informacji, zobacz [test dotyczący zabezpieczeń platformy Azure: odzyskiwanie danych](/azure/security/benchmarks/security-control-data-recovery).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: Zapewnij regularne zautomatyzowane przywracanie awaryjne

**Wskazówki**: Event Grid ma automatyczne odzyskiwanie po awarii geograficznej (GeoDR) metadanych nie tylko dla nowych, ale wszystkie istniejące domeny, tematy i subskrypcje zdarzeń. W przypadku przekroczenia całego regionu platformy Azure Event Grid wszystkie metadane infrastruktury powiązane ze zdarzeniami są synchronizowane z sparowanym regionem.

- [Odzyskiwanie po awarii geograficznej po stronie serwera w Azure Event Grid](geo-disaster-recovery.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: wykonaj kompletne kopie zapasowe systemu i Utwórz kopię zapasową wszystkich kluczy zarządzanych przez klienta

**Wskazówki**: Event Grid ma automatyczne odzyskiwanie po awarii geograficznej (GeoDR) metadanych nie tylko dla nowych, ale wszystkie istniejące domeny, tematy i subskrypcje zdarzeń. W przypadku przekroczenia całego regionu platformy Azure Event Grid wszystkie metadane infrastruktury powiązane ze zdarzeniami są synchronizowane z sparowanym regionem.

Obecnie Event Grid nie obsługuje kluczy zarządzanych przez klienta. 

- [Odzyskiwanie po awarii geograficznej po stronie serwera w Azure Event Grid](geo-disaster-recovery.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: Weryfikuj wszystkie kopie zapasowe, w tym klucze zarządzane przez klienta

**Wskazówki**: Event Grid ma automatyczne odzyskiwanie po awarii geograficznej (GeoDR) metadanych nie tylko dla nowych, ale wszystkie istniejące domeny, tematy i subskrypcje zdarzeń. W przypadku przekroczenia całego regionu platformy Azure Event Grid wszystkie metadane infrastruktury powiązane ze zdarzeniami są synchronizowane z sparowanym regionem.

Obecnie Event Grid nie obsługuje kluczy zarządzanych przez klienta. 

- [Odzyskiwanie po awarii geograficznej po stronie serwera w Azure Event Grid](geo-disaster-recovery.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: zapewnianie ochrony kopii zapasowych i kluczy zarządzanych przez klienta

**Wskazówki**: Włącz trwałe usuwanie i przeczyszczanie ochrony w Key Vault, aby chronić klucze przed przypadkowym lub złośliwym usunięciem. 
 

Obecnie Event Grid nie obsługuje kluczy zarządzanych przez klienta. 

- [Opis kontroli RBAC platformy Azure](../role-based-access-control/overview.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

## <a name="incident-response"></a>Reagowanie na zdarzenia

*Aby uzyskać więcej informacji, zobacz temat [Azure Security test: odpowiedź na zdarzenia](/azure/security/benchmarks/security-control-incident-response).*

### <a name="101-create-an-incident-response-guide"></a>10,1: Tworzenie przewodnika odpowiedzi na zdarzenia

**Wskazówki**: Tworzenie przewodnika odpowiedzi na zdarzenia dla organizacji. Upewnij się, że istnieją jednokierunkowe plany reagowania na zdarzenia, które definiują wszystkie role pracowników, a także etapy obsługi zdarzeń i zarządzania z wykrywania na potrzeby przeglądu po zdarzeniu. 

- [Wskazówki dotyczące tworzenia własnego procesu reagowania na zdarzenia zabezpieczeń](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Anatomia incydentu centrum Microsoft Security Response](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [Skorzystaj z przewodnika obsługi zdarzeń zabezpieczeń komputera NIST, aby pomóc w tworzeniu własnego planu reagowania na zdarzenia](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: Tworzenie oceny incydentu i procedury priorytetyzacji

**Wskazówki**: Azure Security Center przypisuje ważność do każdego alertu, aby pomóc w ustaleniu, które alerty należy najpierw zbadać. Ważność jest oparta na tym, jak dobrze Security Center znajduje się w wyszukiwaniu lub analitycznie używanym do wystawiania alertu, a także poziom pewności, że istniało złośliwy wpływ na działanie, które spowodowało wygenerowanie alertu.

 
 
 
 Ponadto należy oznaczyć subskrypcje przy użyciu tagów i utworzyć system nazewnictwa, aby identyfikować i klasyfikować zasoby platformy Azure, szczególnie te, które przetwarzają poufne dane. Odpowiedzialność za korygowanie alertów zależy od zagrożenia dla zasobów platformy Azure i środowiska, w którym wystąpiło zdarzenie.

- [Alerty zabezpieczeń w Centrum zabezpieczeń Azure](../security-center/security-center-alerts-overview.md)

- [Organizowanie zasobów platformy Azure przy użyciu tagów](/azure/azure-resource-manager/resource-group-using-tags)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="103-test-security-response-procedures"></a>10,3: procedury odpowiedzi na zabezpieczenia testowe

**Wskazówki**: przeprowadzanie ćwiczeń w celu przetestowania możliwości reagowania na zdarzenia systemów w regularnych erzeach w celu ochrony zasobów platformy Azure. Zidentyfikuj słabe punkty i przerwy, a następnie Popraw plan odpowiedzi zgodnie z potrzebami.
 
 
 
- [ Publikacja NIST — Przewodnik dotyczący testowania, uczenia i ćwiczeń programów dla planów i możliwości IT](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: Podaj szczegóły kontaktu dotyczącego zabezpieczeń i Skonfiguruj powiadomienia dotyczące alertów dotyczących zdarzeń związanych z zabezpieczeniami

**Wskazówki**: informacje kontaktowe dotyczące zdarzenia zabezpieczeń będą używane przez firmę Microsoft do skontaktowania się z Tobą, jeśli firma Microsoft Security Response Center (MSRC) wykryje, że dostęp do danych zostały nadane przez nielegalną lub nieautoryzowaną stronę. Przejrzyj zdarzenia po fakcie, aby upewnić się, że problemy zostały rozwiązane.
 
 
 
- [ Jak ustawić kontakt z zabezpieczeniami Azure Security Center](../security-center/security-center-provide-security-contact-details.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: Uwzględnij alerty zabezpieczeń w systemie odpowiedzi na zdarzenia

**Wskazówki**: eksportowanie alertów i zaleceń dotyczących Azure Security Center przy użyciu funkcji eksportu ciągłego, która pomaga identyfikować zagrożenia dla zasobów platformy Azure. Eksport ciągły umożliwia wyeksportowanie alertów i zaleceń ręcznie lub w stały sposób ciągły. Możesz użyć łącznika danych Azure Security Center do przesyłania strumieniowego alertów do usługi Azure wskaźnikowej.

- [Jak skonfigurować eksport ciągły](../security-center/continuous-export.md)

- [Jak przesłać strumieniowo alerty do usługi Azure wskaźnikowego](../sentinel/connect-azure-security-center.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: Automatyzowanie odpowiedzi na alerty zabezpieczeń

**Wskazówki**: korzystanie z funkcji automatyzacji przepływu pracy Azure Security Center do automatycznego wyzwalania odpowiedzi na alerty zabezpieczeń i zalecenia dotyczące ochrony zasobów platformy Azure.

- [Jak skonfigurować automatyzację przepływu pracy w Security Center](../security-center/workflow-automation.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

## <a name="penetration-tests-and-red-team-exercises"></a>Testy penetracyjne i ćwiczenia typu „red team”

*Aby uzyskać więcej informacji, zobacz test [porównawczy zabezpieczeń platformy Azure: testy penetracji i czerwone ćwiczenia zespołu](/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: Przeprowadź regularne testowanie penetracji zasobów platformy Azure i zadbaj o skorygowanie wszystkich krytycznych ustaleń dotyczących zabezpieczeń

**Wskazówki**: Postępuj zgodnie z regułami testowania penetracji Microsoft Cloud, aby upewnić się, że testy penetracji nie naruszają zasad firmy Microsoft. Korzystaj z strategii firmy Microsoft i wykonywania testów na żywo z obsługą tworzenia zespołu, usług i aplikacji w chmurze, które są zarządzane przez firmę Microsoft.

- [Reguły testowania penetracji zaangażowania](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Microsoft Cloud Tworzenie zespołu Red.](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: udostępnione

## <a name="next-steps"></a>Następne kroki

- Zobacz [test porównawczy zabezpieczeń platformy Azure](/azure/security/benchmarks/overview)
- Dowiedz się więcej o [punktach odniesienia zabezpieczeń platformy Azure](/azure/security/benchmarks/security-baselines-overview)
