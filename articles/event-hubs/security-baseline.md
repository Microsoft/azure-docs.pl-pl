---
title: Usługa Azure Security Baseline dla centrów zdarzeń
description: Usługa Azure Security Baseline dla centrów zdarzeń
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 1c7e18214ec175b8b21d25643098631a044da19c
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81758322"
---
# <a name="azure-security-baseline-for-event-hubs"></a>Usługa Azure Security Baseline dla centrów zdarzeń

Usługa Azure Security Baseline for Event Hubs zawiera zalecenia, które pomogą Ci poprawić poziom zabezpieczeń wdrożenia.

Punkt odniesienia dla tej usługi jest pobierany z [usługi Azure Security Benchmark w wersji 1.0,](https://docs.microsoft.com/azure/security/benchmarks/overview)która zawiera zalecenia dotyczące sposobu zabezpieczania rozwiązań w chmurze na platformie Azure za pomocą naszych najlepszych wskazówek dotyczących rozwiązań.

Aby uzyskać więcej informacji, zobacz [omówienie planów bazowych zabezpieczeń platformy Azure](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview).

## <a name="network-security"></a>Bezpieczeństwo sieci

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Bezpieczeństwo sieci](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1: Ochrona zasobów przy użyciu sieciowych grup zabezpieczeń lub zapory platformy Azure w sieci wirtualnej

**Wskazówki:** Integracja centrów zdarzeń z punktami końcowymi usługi sieci wirtualnej umożliwia bezpieczny dostęp do funkcji obsługi wiadomości z obciążeń, takich jak maszyny wirtualne, które są powiązane z sieciami wirtualnymi, przy czym ścieżka ruchu sieciowego jest zabezpieczona na obu końcach.

Po związaniu się z co najmniej jednym punktem końcowym usługi podsieci sieci wirtualnej odpowiedni obszar nazw Centrum zdarzeń nie akceptuje już ruchu z dowolnego miejsca, ale autoryzowanych podsieci w sieciach wirtualnych. Z punktu widzenia sieci wirtualnej powiązanie obszaru nazw centrum zdarzeń z punktem końcowym usługi konfiguruje tunel sieci izolowanej z podsieci sieci wirtualnej do usługi obsługi wiadomości. 

Można również utworzyć prywatny punkt końcowy, który jest interfejsem sieciowym, który łączy cię prywatnie i bezpiecznie z usługą Azure Event Hubs przy użyciu usługi Azure Private Link. Prywatny punkt końcowy używa prywatnego adresu IP z sieci wirtualnej, skutecznie wprowadzając usługę do sieci wirtualnej. Cały ruch do usługi może być kierowany przez prywatny punkt końcowy, więc nie są potrzebne żadne bramy, urządzenia NAT, połączenia usługi ExpressRoute lub VPN ani publiczne adresy IP. 

Możesz również zabezpieczyć obszar nazw usługi Azure Event Hubs przy użyciu zapór. Usługa Azure Event Hubs obsługuje formanty dostępu oparte na protokãołowym adresie IP dla obsługi zapory przychodzącej. Reguły zapory można ustawić przy użyciu witryny Azure portal, szablonów usługi Azure Resource Manager lub za pośrednictwem interfejsu wiersza polecenia platformy Azure lub programu Azure PowerShell.

Jak korzystać z punktów końcowych usługi sieci wirtualnej za pomocą usługi Azure Event Hubs:https://docs.microsoft.com/azure/event-hubs/event-hubs-service-endpoints

Aby uzyskać więcej informacji, zobacz Integrowanie https://docs.microsoft.com/azure/event-hubs/private-link-servicecentrów zdarzeń platformy Azure z łączem prywatnym platformy Azure: .

Włącz integrację sieci wirtualnych i zapory w obszarze nazw Centrum zdarzeń:https://docs.microsoft.com/azure/event-hubs/event-hubs-tutorial-virtual-networks-firewalls

Jak skonfigurować reguły zapory IP dla obszarów nazw usługi Azure Event Hubs:https://docs.microsoft.com/azure/event-hubs/event-hubs-ip-filtering

**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2: Monitorowanie i rejestrowanie konfiguracji i ruchu sieci wirtualnych, podsieci i kart sieciowych

**Wskazówki:** Skorzystaj z usługi Azure Security Center i postępuj zgodnie z zaleceniami dotyczącymi ochrony sieci, aby zabezpieczyć zasoby usługi Event Hubs na platformie Azure. Jeśli używasz maszyn wirtualnych platformy Azure, aby uzyskać dostęp do centrów zdarzeń, włącz dzienniki przepływu sieciowej grupy zabezpieczeń (NSG) i wyślij dzienniki do konta magazynu w celu inspekcji ruchu.

Jak włączyć dzienniki przepływu nsg:https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Opis zabezpieczeń sieci dostarczanych przez usługę Azure Security Center:https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

### <a name="13-protect-critical-web-applications"></a>1.3: Ochrona krytycznych aplikacji internetowych

**Wskazówki**: Nie dotyczy; to zalecenie jest przeznaczone dla aplikacji sieci web działających w usłudze Azure App Service lub zasobów obliczeniowych.

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Odmowa komunikacji ze znanymi złośliwymi adresami IP

**Wskazówki:** Włącz standard ochrony przed atakami DDoS w sieciach wirtualnych skojarzonych z centrami zdarzeń, aby chronić się przed rozproszonymi atakami typu "odmowa usługi" (DDoS). Usługa Azure Security Center Integrated Threat Intelligence umożliwia odmawianie komunikacji ze znanymi złośliwymi lub nieużywanym internetowymi adresami IP.

Jak skonfigurować ochronę przed atakami DDoS:[https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection](/azure/virtual-network/manage-ddos-protection)

Aby uzyskać więcej informacji na temat zintegrowanej analizy zagrożeń usługi Azure Security Center:https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer

**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

### <a name="15-record-network-packets-and-flow-logs"></a>1.5: Rejestrowanie pakietów sieciowych i dzienników przepływu

**Wskazówki:** Jeśli używasz maszyn wirtualnych platformy Azure do uzyskiwania dostępu do centrów zdarzeń, włącz dzienniki przepływu sieciowej grupy zabezpieczeń (NSG) i wysyłaj dzienniki do konta magazynu w celu inspekcji ruchu. Można również wysłać dzienniki przepływu nsg do obszaru roboczego usługi Log Analytics i użyć usługi Traffic Analytics, aby zapewnić wgląd w przepływ ruchu w chmurze platformy Azure. Niektóre zalety usługi Traffic Analytics to możliwość wizualizacji aktywności sieciowej i identyfikowania punktów zapalnych, identyfikowania zagrożeń bezpieczeństwa, zrozumienia wzorców przepływu ruchu i określania błędnych konfiguracji sieci.

Jeśli jest to wymagane do badania nietypowej aktywności, włącz przechwytywanie pakietów Obserwatora sieci.

Jak włączyć dzienniki przepływu nsg:https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Jak włączyć i korzystać z analizy ruchu:https://docs.microsoft.com/azure/network-watcher/traffic-analytics

Jak włączyć funkcję Obserwatora sieciowego:https://docs.microsoft.com/azure/network-watcher/network-watcher-create

**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Wdrażanie opartych na sieci systemów wykrywania włamań/włamań (IDS/IPS)

**Wskazówki:** Jeśli używasz maszyn wirtualnych platformy Azure do uzyskiwania dostępu do centrów zdarzeń, wybierz ofertę z portalu Azure Marketplace, która obsługuje funkcje IDS/IPS z możliwościami inspekcji ładunku. Jeśli wykrywanie włamań i/lub zapobieganie na podstawie inspekcji ładunku nie jest wymagane dla twojej organizacji, możesz użyć wbudowanej zapory usługi Azure Event Hubs. Dostęp do obszaru nazw Centrum zdarzeń można ograniczyć dla ograniczonego zakresu adresów IP lub określonego adresu IP przy użyciu reguł zapory.

Azure Marketplace:

https://azuremarketplace.microsoft.com/marketplace/?term=Firewall

Jak dodać regułę zapory w Centrach zdarzeń dla określonego adresu IP:

 https://docs.microsoft.com/azure/event-hubs/event-hubs-ip-filtering

**Monitorowanie usługi Azure Security Center:** jeszcze niedostępne

**Odpowiedzialność**: Klient

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Zarządzanie ruchem do aplikacji internetowych

**Wskazówki**: Nie dotyczy; to zalecenie jest przeznaczone dla aplikacji sieci web działających w usłudze Azure App Service lub zasobów obliczeniowych.

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimalizowanie złożoności i administracyjnych narzutów administracyjnych dotyczących zasad bezpieczeństwa sieci

**Wskazówki:** Nie dotyczy, to zalecenie jest przeznaczone dla aplikacji sieci web działających w usłudze Azure App Service lub zasobów obliczeniowych.

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Obsługa standardowych konfiguracji zabezpieczeń dla urządzeń sieciowych

**Wskazówki:** Definiowanie i implementowanie standardowych konfiguracji zabezpieczeń dla zasobów sieciowych skojarzonych z obszarami nazw usługi Azure Event Hubs za pomocą zasad platformy Azure. Użyj aliasów zasad platformy Azure w obszarach nazw "Microsoft.EventHub" i "Microsoft.Network", aby utworzyć niestandardowe zasady do inspekcji lub wymuszenia konfiguracji sieciowej obszarów nazw centrów zdarzeń. Można również korzystać z wbudowanych definicji zasad związanych z usługi Azure Event Hubs, takich jak:

- Centrum zdarzeń należy użyć punktu końcowego usługi sieci wirtualnej.

Jak skonfigurować zasady platformy Azure i zarządzać nimi:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Wbudowane zasady platformy Azure dla obszaru nazw centrów zdarzeń:https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#event-hub



Przykłady zasad platformy Azure dla sieci:https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#network



Jak utworzyć plan platformy Azure:https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="110-document-traffic-configuration-rules"></a>1.10: Przepisy konfiguracji ruchu dokumentów

**Wskazówki:** Użyj tagów dla sieci wirtualnych i innych zasobów związanych z zabezpieczeniami sieci i przepływem ruchu, które są skojarzone z koncentratorami zdarzeń.

Jak tworzyć i używać tagów:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Użyj zautomatyzowanych narzędzi do monitorowania konfiguracji zasobów sieciowych i wykrywania zmian

**Wskazówki:** Użyj dziennika aktywności platformy Azure do monitorowania konfiguracji zasobów sieciowych i wykrywania zmian zasobów sieciowych związanych z usługą Azure Event Hubs. Tworzenie alertów w usłudze Azure Monitor, które będą wyzwalane po wprowadzeniu zmian w krytycznych zasobach sieciowych.

Jak wyświetlać i pobierać zdarzenia dziennika aktywności platformy Azure:https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view

Jak tworzyć alerty w usłudze Azure Monitor:https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

## <a name="logging-and-monitoring"></a>Rejestrowanie i monitorowanie

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Rejestrowanie i monitorowanie](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: Korzystanie z zatwierdzonych źródeł synchronizacji czasu

**Wskazówki**: Nie dotyczy; Firma Microsoft przechowuje źródło czasu używane dla zasobów platformy Azure, takich jak usługi Azure Event Hubs, dla sygnatur czasowych w dziennikach.

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: Konfigurowanie centralnego zarządzania dziennikami zabezpieczeń

**Wskazówki:** W usłudze Azure Monitor skonfiguruj dzienniki związane z centrami zdarzeń w ustawieniach diagnostycznych dziennika aktywności i centrum zdarzeń, aby wysyłać dzienniki do obszaru roboczego usługi Log Analytics, które mają być wyszukiwane lub do konta magazynu dla długoterminowego magazynu archiwizacji.

Jak skonfigurować ustawienia diagnostyczne dla usługi Azure Event Hubs:https://docs.microsoft.com/azure/event-hubs/event-hubs-diagnostic-logs

Opis dziennika aktywności platformy Azure:https://docs.microsoft.com/azure/azure-monitor/platform/platform-logs-overview

**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Włączanie rejestrowania inspekcji zasobów platformy Azure

**Wskazówki:** Włącz ustawienia diagnostyczne dla obszaru nazw usługi Azure Event Hubs. Istnieją trzy kategorie ustawień diagnostycznych dla usługi Azure Event Hubs: dzienniki archiwum, dzienniki operacyjne i dzienniki skalowania automatycznego. Włącz dzienniki operacyjne, aby przechwycić informacje o tym, co dzieje się podczas operacji centrum zdarzeń, w szczególności typu operacji, w tym tworzenia centrum zdarzeń, używanych zasobów i stanu operacji.

Ponadto można włączyć ustawienia diagnostyczne dziennika aktywności platformy Azure i wysłać je do konta usługi Azure Storage, centrum zdarzeń lub obszaru roboczego usługi Log Analytics. Dzienniki aktywności zapewniają wgląd w operacje, które zostały wykonane w witrynie Azure Event Hubs i innych zasobów. Za pomocą dzienników aktywności można określić "co, kto i kiedy" dla wszelkich operacji zapisu (PUT, POST, DELETE) podjętych w obszarach nazw usługi Azure Event Hubs.

Jak włączyć ustawienia diagnostyczne dla usługi Azure Event Hubs:https://docs.microsoft.com/azure/event-hubs/event-hubs-diagnostic-logs

Jak włączyć ustawienia diagnostyczne dziennika aktywności platformy Azure:https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy

**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: Zbieranie dzienników zabezpieczeń z systemów operacyjnych

**Wskazówki**: Nie dotyczy; to zalecenie jest przeznaczone dla zasobów obliczeniowych.

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

### <a name="25-configure-security-log-storage-retention"></a>2.5: Konfigurowanie przechowywania magazynu dzienników zabezpieczeń

**Wskazówki:** W usłudze Azure Monitor ustaw okres przechowywania obszaru roboczego usługi Log Analytics zgodnie z przepisami dotyczącymi zgodności organizacji, aby przechwytywać i przeglądać zdarzenia związane z centrum zdarzeń.

Jak ustawić parametry przechowywania dzienników dla obszarów roboczych usługi Log Analytics:https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="26-monitor-and-review-logs"></a>2.6: Monitorowanie i przeglądanie dzienników

**Wskazówki:** Analizuj i monitoruj dzienniki pod kątem nietypowego zachowania i regularnie sprawdzaj wyniki związane z centrami zdarzeń. Użyj usługi Azure Monitor Log Analytics do przeglądania dzienników i wykonywania zapytań na dane dziennika. Alternatywnie można włączyć i na pokładzie danych do usługi Azure Sentinel lub innej firmy SIEM.
 

Aby uzyskać więcej informacji na temat obszaru roboczego usługi Log Analytics:https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal

Jak wykonywać zapytania niestandardowe w usłudze Azure Monitor:https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries

Jak ować wobec pokład Azure Wartownik:https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: Włączanie alertów o nietypowej aktywności

**Wskazówki:** W usłudze Azure Monitor skonfiguruj dzienniki związane z centrum zdarzeń platformy Azure w dzienniku aktywności i ustawienia diagnostyczne centrum zdarzeń, aby wysyłać dzienniki do obszaru roboczego usługi Log Analytics, które mają być wyszukiwane lub do konta magazynu dla długoterminowego magazynu archiwizacji. Użyj obszaru roboczego usługi Log Analytics, aby utworzyć alerty dotyczące nietypowej aktywności znalezionej w dziennikach zabezpieczeń i zdarzeniach.

Alternatywnie można włączyć i na pokładzie danych do usługi Azure Sentinel. 

Zrozumienie dziennika aktywności platformy Azure:https://docs.microsoft.com/azure/azure-monitor/platform/platform-logs-overview

Jak skonfigurować ustawienia diagnostyczne dla usługi Azure Event Hubs:https://docs.microsoft.com/azure/event-hubs/event-hubs-diagnostic-logs

Jak alertować dane dziennika obszaru roboczego usługi Log Analytics:https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response

Jak ować wobec pokład Azure Wartownik:https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Monitorowanie usługi Azure Security Center:** jeszcze niedostępne

**Odpowiedzialność**: Klient

### <a name="28-centralize-anti-malware-logging"></a>2.8: Scentralizuj rejestrowanie przed złośliwym oprogramowaniem

**Wskazówki**: Nie dotyczy; Usługa Event Hub nie przetwarza rejestrowania ochrony przed złośliwym oprogramowaniem.

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

### <a name="29-enable-dns-query-logging"></a>2.9: Włączanie rejestrowania zapytań DNS

**Wskazówki**: Nie dotyczy; Centra zdarzeń nie przetwarzają ani nie generują dzienników związanych z systemem DNS.

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

### <a name="210-enable-command-line-audit-logging"></a>2.10: Włącz rejestrowanie inspekcji wiersza polecenia

**Wskazówki**: Nie dotyczy; niniejsza wytyczna jest przeznaczona dla zasobów obliczeniowych.

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

## <a name="identity-and-access-control"></a>Tożsamość i kontrola dostępu

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Kontrola tożsamości i dostępu](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Prowadzenie spisu rachunków administracyjnych

**Wskazówki:** Usługa Azure Active Directory (AD) ma wbudowane role, które muszą być jawnie przypisane i są możliwe do queryable. Użyj modułu programu Azure AD PowerShell do wykonywania zapytań ad hoc w celu odnajdowania kont, które są członkami grup administracyjnych. 

Jak uzyskać rolę katalogu w usłudze Azure AD za pomocą programu PowerShell:https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0

Jak uzyskać członków roli katalogu w usłudze Azure AD za pomocą programu PowerShell:https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0

**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

### <a name="32-change-default-passwords-where-applicable"></a>3.2: W stosownych przypadkach zmieniaj domyślne hasła

**Wskazówki:** Dostęp płaszczyzny sterowania do centrów zdarzeń jest kontrolowany za pośrednictwem usługi Azure Active Directory (AD). Usługa Azure AD nie ma pojęcia haseł domyślnych.

Dostęp do płaszczyzny danych do centrów zdarzeń jest kontrolowany za pośrednictwem usługi Azure AD za pomocą tożsamości zarządzanych lub rejestracji aplikacji, a także podpisów dostępu współdzielonego. Podpisy dostępu współdzielonego są używane przez klientów łączących się z centrum zdarzeń i mogą być ponownie generowane w dowolnym momencie.

Poznaj podpisy dostępu współdzielonego dla Centrów zdarzeń:https://docs.microsoft.com/azure/event-hubs/authenticate-shared-access-signature

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Korzystanie z dedykowanych kont administracyjnych

**Wskazówki**: Tworzenie standardowych procedur operacyjnych dotyczących korzystania z dedykowanych rachunków administracyjnych. Usługa Azure Security Center Identity and Access Management służy do monitorowania liczby kont administracyjnych.

Ponadto, aby ułatwić śledzenie dedykowanych kont administracyjnych, można użyć zaleceń z usługi Azure Security Center lub wbudowanych zasad platformy Azure, takich jak:

- Do subskrypcji powinien być przypisany więcej niż jeden właściciel

- Przestarzałe konta z uprawnieniami właściciela powinny zostać usunięte z subskrypcji

- Konta zewnętrzne z uprawnieniami właściciela powinny zostać usunięte z subskrypcji

Jak używać usługi Azure Security Center do monitorowania tożsamości i dostępu (Wersja zapoznawcza):https://docs.microsoft.com/azure/security-center/security-center-identity-access

Jak korzystać z zasad platformy Azure:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Używanie logowania jednokrotnego (Logowanie jednokrotne) w usłudze Azure Active Directory

**Wskazówki:** Platforma Microsoft Azure zapewnia zintegrowane zarządzanie kontrolą dostępu dla zasobów i aplikacji na podstawie usługi Azure Active Directory (AD). Kluczową zaletą korzystania z usługi Azure AD z usługi Azure Event Hubs jest to, że nie trzeba już przechowywać poświadczenia w kodzie. Zamiast tego można zażądać tokenu dostępu OAuth 2.0 z platformy Microsoft Identity. Nazwa zasobu, aby zażądać\/tokenu jest https: /eventhubs.azure.net/. Usługa Azure AD uwierzytelnia podmiot zabezpieczeń (użytkownika, grupy lub jednostki usługi) z uruchomieniem aplikacji. Jeśli uwierzytelnianie zakończy się pomyślnie, usługa Azure AD zwraca token dostępu do aplikacji, a aplikacja może następnie użyć tokenu dostępu do autoryzowania żądania do zasobów usługi Azure Event Hubs.

Jak uwierzytelnić aplikację za pomocą usługi Azure AD, aby uzyskać dostęp do zasobów usługi Event Hubs:https://docs.microsoft.com/azure/event-hubs/authenticate-application

Opis usługi SSO za pomocą usługi Azure AD:https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Użyj uwierzytelniania wieloskładnikowego dla całego dostępu opartego na usłudze Azure Active Directory

**Wskazówki:** Włącz uwierzytelnianie wieloskładnikowe usługi Azure Active Directory i postępuj zgodnie z zaleceniami dotyczącymi zarządzania tożsamościami i dostępem usługi Azure Security Center, aby chronić zasoby obsługujące centrum zdarzeń.

Jak włączyć usługę MFA na platformie Azure:https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Jak monitorować tożsamość i dostęp w usłudze Azure Security Center:https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Używanie dedykowanych maszyn (stacje robocze z uprzywilejowanym dostępem) do wszystkich zadań administracyjnych

**Wskazówki:** Użyj stacji roboczych dostępu uprzywilejowanego (PAW) z uwierzytelnianiem wieloskładnikowym (MFA) skonfigurowanym do logowania się i konfigurowania zasobów obsługujących centrum zdarzeń.

Dowiedz się więcej o stacjach roboczych z dostępem uprzywilejowanym:https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations

Jak włączyć usługę MFA na platformie Azure:https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7: Rejestrowanie i ostrzeganie o podejrzanych działaniach z kont administracyjnych

**Wskazówki:** Użyj usługi Azure Active Directory (AD) Uprzywilejowane zarządzanie tożsamościami (PIM) do generowania dzienników i alertów, gdy podejrzane lub niebezpieczne działania występuje w środowisku. Użyj wykrywania ryzyka usługi Azure AD, aby wyświetlić alerty i raporty dotyczące ryzykownych zachowań użytkowników. Aby uzyskać dodatkowe rejestrowanie, wyślij alerty wykrywania ryzyka usługi Azure Security Center do usługi Azure Monitor i skonfiguruj niestandardowe alerty/powiadomienia przy użyciu grup akcji.

Jak wdrożyć zarządzanie tożsamościami uprzywilejowanymi (PIM):https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan

Poznaj wykrywanie ryzyka usługi Azure AD:https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events

Jak skonfigurować grupy akcji do niestandardowego alertów i powiadomień:https://docs.microsoft.com/azure/azure-monitor/platform/action-groups

**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Zarządzanie zasobami platformy Azure tylko z zatwierdzonych lokalizacji

**Wskazówki:** Użyj lokalizacji nazwanych dostępu warunkowego, aby zezwolić na dostęp tylko z określonych grup logicznych zakresów adresów IP lub krajów/regionów.



Jak skonfigurować nazwane lokalizacje na platformie Azure:https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="39-use-azure-active-directory"></a>3.9: Korzystanie z usługi Azure Active Directory

**Wskazówki:** Użyj usługi Azure Active Directory (AD) jako centralnego systemu uwierzytelniania i autoryzacji dla zasobów platformy Azure, takich jak Centra zdarzeń. Pozwala to na kontrolę dostępu opartą na rolach (RBAC) do zasobów wrażliwych administracyjnych.

 Jak utworzyć i skonfigurować wystąpienie usługi Azure AD:https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant

Aby dowiedzieć się, jak usługa Azure Event Hubs integruje się z usługą Azure Active Directory (AAD), zobacz Autoryzowanie dostępu do zasobów usługi Event Hubs przy użyciu usługi Azure Active Directory:https://docs.microsoft.com/azure/event-hubs/authorize-access-azure-active-directory

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Regularne przeglądanie i uzgadnianie dostępu użytkowników

**Wskazówki:** Usługa Azure Active Directory (AD) udostępnia dzienniki ułatwiające odnajdowanie starych kont. Ponadto użyj przeglądów dostępu do tożsamości platformy Azure, aby skutecznie zarządzać członkostwem w grupach, dostępem do aplikacji dla przedsiębiorstw i przypisaniami ról. Dostęp użytkownika można regularnie przeglądać, aby upewnić się, że tylko prawo Użytkownicy mają stały dostęp.

Ponadto regularnie obracaj podpisy dostępu współdzielonego w centrach zdarzeń.

Opis raportowania usługi Azure AD:https://docs.microsoft.com/azure/active-directory/reports-monitoring/

Jak korzystać z przeglądów dostępu tożsamości platformy Azure:https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

Opis podpisów dostępu współdzielonego dla Centrów zdarzeń:https://docs.microsoft.com/azure/event-hubs/authenticate-shared-access-signature

**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: Monitorowanie prób uzyskania dostępu do dezaktywowanych kont

**Wskazówki:** Masz dostęp do działania logowania usługi Azure Active Directory (AD), inspekcji i źródeł dziennika zdarzeń ryzyka, które umożliwiają integrację z dowolnym narzędziem SIEM/Monitorowanie.

Możesz usprawnić ten proces, tworząc ustawienia diagnostyczne dla kont użytkowników usługi Azure AD i wysyłając dzienniki inspekcji i dzienniki logowania do obszaru roboczego usługi Log Analytics. Można skonfigurować żądane alerty dziennika w usłudze Log Analytics.

Jak zintegrować dzienniki aktywności platformy Azure z usługą Azure Monitor:https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

Autoryzuj dostęp do zasobów Usługi Event Hubs przy użyciu usługi Azure Active Directory:https://docs.microsoft.com/azure/event-hubs/authorize-access-azure-active-directory

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Alert o odchyleniu zachowania logowania do konta

**Wskazówki:** Użyj funkcji ochrony tożsamości usługi Azure Active Directory i wykrywania ryzyka, aby skonfigurować automatyczne odpowiedzi na wykryte podejrzane akcje związane z zasobami obsługującymi usługi Event Hubs. Należy włączyć automatyczne odpowiedzi za pośrednictwem usługi Azure Sentinel do zaimplementowania odpowiedzi zabezpieczeń organizacji.

Jak wyświetlić ryzykowne logowania usługi Azure AD:https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

Jak skonfigurować i włączyć zasady ryzyka ochrony tożsamości:https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

Jak ować wobec pokład Azure Wartownik:https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Zapewnienie firmie Microsoft dostępu do odpowiednich danych klientów podczas scenariuszy pomocy technicznej

**Wskazówki**: Obecnie niedostępne; Skrytka klienta nie jest jeszcze obsługiwana dla centrów zdarzeń.

Lista usług obsługiwanych przez skrytki klienta:https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Obecnie niedostępne

## <a name="data-protection"></a>Ochrona danych

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Ochrona danych](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Prowadzenie wykazu informacji poufnych

**Wskazówki:** Użyj tagów w zasobach związanych z centrum zdarzeń, aby pomóc w śledzeniu zasobów platformy Azure, które przechowują lub przetwarzają poufne informacje.

Jak tworzyć i używać tagów:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Systemy izolowania przechowujące lub przetwarzające informacje poufne

**Wskazówki**: Implementowanie oddzielnych subskrypcji i/lub grup zarządzania do programowania, testowania i produkcji. Obszary nazw centrum zdarzeń powinny być oddzielone siecią wirtualną z włączonymi punktami końcowymi usługi i odpowiednio oznakowanymi.

Możesz również zabezpieczyć obszar nazw usługi Azure Event Hubs przy użyciu zapór. Usługa Azure Event Hubs obsługuje formanty dostępu oparte na protokãołowym adresie IP dla obsługi zapory przychodzącej. Reguły zapory można ustawić przy użyciu witryny Azure portal, szablonów usługi Azure Resource Manager lub za pośrednictwem interfejsu wiersza polecenia platformy Azure lub programu Azure PowerShell.

Jak utworzyć dodatkowe subskrypcje platformy Azure:https://docs.microsoft.com/azure/billing/billing-create-subscription

Jak utworzyć grupy zarządzania:https://docs.microsoft.com/azure/governance/management-groups/create

Konfigurowanie reguł zapory IP dla obszarów nazw usługi Azure Event Hubs:https://docs.microsoft.com/azure/event-hubs/event-hubs-ip-filtering

Jak tworzyć i wykorzystywać tagi:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Jak utworzyć sieć wirtualną:https://docs.microsoft.com/azure/virtual-network/quick-create-portal

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Monitorowanie i blokowanie nieautoryzowanego przesyłania poufnych informacji

**Wskazówki:** Podczas korzystania z maszyn wirtualnych w celu uzyskania dostępu do centrów zdarzeń, należy korzystać z sieci wirtualnych, punktów końcowych usługi, zapory centrum zdarzeń, sieciowych grup zabezpieczeń i tagów usług, aby ograniczyć możliwość eksfiltracji danych.

Firma Microsoft zarządza podstawową infrastrukturą usługi Azure Event Hubs i wdrożyła ścisłe mechanizmy kontroli, aby zapobiec utracie lub narażeniu danych klientów.

Konfigurowanie reguł zapory IP dla obszarów nazw usługi Azure Event Hubs:https://docs.microsoft.com/azure/event-hubs/event-hubs-ip-filtering

Poznaj punkty końcowe usługi sieci wirtualnej za pomocą usługi Azure Event Hubs:https://docs.microsoft.com/azure/event-hubs/event-hubs-service-endpoints

Zintegruj usługi Azure Event Hubs z łączem prywatnym platformy Azure:https://docs.microsoft.com/azure/event-hubs/private-link-service

Poznaj sieciowe grupy zabezpieczeń i znaczniki usług:https://docs.microsoft.com/azure/virtual-network/security-overview

Poznaj ochronę danych klientów na platformie Azure:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Szyfrowanie wszystkich poufnych informacji podczas przesyłania

**Wskazówki:** Usługa Azure Event Hubs domyślnie wymusza komunikację szyfrowaną przez TLS. TLS wersje 1.0, 1.1 i 1.2 są obecnie obsługiwane. Jednak TLS 1.0 i 1.1 są na ścieżce do deprecation całej branży, więc należy użyć TLS 1.2, jeśli w ogóle możliwe.

Aby zrozumieć funkcje zabezpieczeń usługi Event Hubs, zobacz Zabezpieczenia sieci:https://docs.microsoft.com/azure/event-hubs/network-security

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Microsoft

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Identyfikowanie poufnych danych za pomocą aktywnego narzędzia do wykrywania

**Wskazówki:** Funkcje identyfikacji, klasyfikacji i zapobiegania stratom danych nie są jeszcze dostępne dla usługi Azure Event Hubs. Implementowanie rozwiązania innych firm, jeśli jest to wymagane do celów zgodności.

W przypadku podstawowej platformy, która jest zarządzana przez firmę Microsoft, firma Microsoft traktuje całą zawartość klienta jako wrażliwą i dokłga mierze chroni przed utratą i ekspozycją danych klientów. Aby zapewnić bezpieczeństwo danych klientów na platformie Azure, firma Microsoft wdrożyła i utrzymuje zestaw niezawodnych mechanizmów kontroli i możliwości ochrony danych.

Poznaj ochronę danych klientów na platformie Azure:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Współdzielone

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Sterowanie dostępem do zasobów za pomocą narzędzia Azure RBAC

**Wskazówki:** Usługa Azure Event Hubs obsługuje korzystanie z usługi Azure Active Directory (AD) do autoryzowania żądań do zasobów usługi Event Hubs. Za pomocą usługi Azure AD można użyć kontroli dostępu opartej na rolach (RBAC) do udzielania uprawnień podmiotowi zabezpieczeń, który może być użytkownikiem lub podmiotem usługi aplikacji.

Poznaj usługę Azure AD RBAC i dostępne role w centrach zdarzeń platformy Azure:https://docs.microsoft.com/azure/event-hubs/authorize-access-azure-active-directory

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Wymuszanie kontroli dostępu za pomocą funkcji zapobiegania utracie danych opartych na hoście

**Wskazówki**: Nie dotyczy; niniejsza wytyczna jest przeznaczona dla zasobów obliczeniowych.

Firma Microsoft zarządza podstawową infrastrukturą dla centrów zdarzeń i wdrożyła ścisłe mechanizmy kontroli, aby zapobiec utracie lub ekspozycji danych klientów.

Poznaj ochronę danych klientów na platformie Azure:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Szyfrowanie poufnych informacji w spoczynku

**Wskazówki:** Usługa Azure Event Hubs obsługuje opcję szyfrowania danych w spoczynku za pomocą kluczy zarządzanych przez firmę Microsoft lub kluczy zarządzanych przez klienta. Ta funkcja umożliwia tworzenie, obracanie, wyłączanie i odwoływanie dostępu do kluczy zarządzanych przez klienta, które są używane do szyfrowania danych usługi Azure Event Hubs w spoczynku.

Jak skonfigurować klucze zarządzane przez klienta do szyfrowania usługi Azure Event Hubs:https://docs.microsoft.com/azure/event-hubs/configure-customer-managed-key

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Rejestrowanie i ostrzeganie o zmianach w krytycznych zasobach platformy Azure

**Wskazówki:** Użyj usługi Azure Monitor z dziennika aktywności platformy Azure do tworzenia alertów, gdy zmiany mają miejsce w wystąpieniach produkcyjnych usługi Azure Event Hubs i innych krytycznych lub powiązanych zasobów.

Jak tworzyć alerty dla zdarzeń dziennika aktywności platformy Azure:https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

## <a name="vulnerability-management"></a>Zarządzanie lukami w zabezpieczeniach

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Zarządzanie lukami w zabezpieczeniach](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Uruchamianie zautomatyzowanych narzędzi do skanowania luk w zabezpieczeniach

**Wskazówki**: Nie dotyczy; Firma Microsoft wykonuje zarządzanie lukami w zabezpieczeniach w podstawowych systemach obsługujących centra zdarzeń.

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Microsoft

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: Wdrażanie zautomatyzowanego rozwiązania do zarządzania poprawkami systemu operacyjnego

**Wskazówki**: Nie dotyczy; Firma Microsoft wykonuje zarządzanie poprawkami w podstawowych systemach obsługujących centra zdarzeń.

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Microsoft

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3: Wdrażanie zautomatyzowanego rozwiązania do zarządzania poprawkami oprogramowania innych firm

**Wskazówki**: Nie dotyczy; jest przeznaczony dla zasobów obliczeniowych.

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Microsoft

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: Porównanie skanów luk w zabezpieczeniach typu back-to-back

**Wskazówki**: Nie dotyczy; Firma Microsoft wykonuje zarządzanie lukami w zabezpieczeniach w podstawowych systemach obsługujących centra zdarzeń.

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Microsoft

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Użyj procesu oceny ryzyka, aby nadać priorytet usuwaniu wykrytych luk w zabezpieczeniach

**Wskazówki**: Nie dotyczy; Firma Microsoft wykonuje zarządzanie lukami w zabezpieczeniach w podstawowych systemach obsługujących centra zdarzeń.

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Microsoft

## <a name="inventory-and-asset-management"></a>Zarządzanie magazynem i zasobami

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Zarządzanie zapasami i zasobami](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management).*

### <a name="61-use-azure-asset-discovery"></a>6.1: Korzystanie z odnajdowania zasobów platformy Azure

**Wskazówki:** Użyj usługi Azure Resource Graph do wykonywania zapytań i odnajdywania wszystkich zasobów (w tym obszarów nazw usługi Azure Event Hubs) w ramach subskrypcji. Upewnij się, że masz odpowiednie (odczytu) uprawnienia w dzierżawie i są w stanie wyliczyć wszystkie subskrypcje platformy Azure, a także zasoby w ramach subskrypcji.

Jak tworzyć zapytania za pomocą programu Azure Resource Graph:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Jak wyświetlić subskrypcje platformy Azure:https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

Poznaj usługę Azure RBAC:https://docs.microsoft.com/azure/role-based-access-control/overview

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="62-maintain-asset-metadata"></a>6.2: Obsługa metadanych zasobów

**Wskazówki:** Stosowanie tagów do zasobów platformy Azure, podając metadane logicznie zorganizować je w taksonomię.

Jak tworzyć i używać tagów:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Usuwanie nieautoryzowanych zasobów platformy Azure

**Wskazówki:** Użyj tagowania, grup zarządzania i oddzielnych subskrypcji, w stosownych przypadkach, do organizowania i śledzenia obszarów nazw usługi Azure Event Hubs i powiązanych zasobów. Regularnie uzgadniaj zapasy i upewnij się, że nieautoryzowane zasoby są usuwane z subskrypcji w odpowiednim czasie.

Jak utworzyć dodatkowe subskrypcje platformy Azure:https://docs.microsoft.com/azure/billing/billing-create-subscription

Jak utworzyć grupy zarządzania:https://docs.microsoft.com/azure/governance/management-groups/create

Jak tworzyć i używać tagów:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4: Obsługa spisu zatwierdzonych zasobów i tytułów oprogramowania platformy Azure

**Wskazówki**: Nie dotyczy; to zalecenie jest przeznaczone dla zasobów obliczeniowych i platformy Azure jako całości.

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Monitoruj niezatwierdzone zasoby platformy Azure

**Wskazówki:** Użyj zasad platformy Azure, aby wprowadzić ograniczenia dotyczące typu zasobów, które można utworzyć w subskrypcjach klientów przy użyciu następujących wbudowanych definicji zasad:

- Niedozwolone typy zasobów

- Dozwolone typy zasobów

Ponadto użyj usługi Azure Resource Graph do wykonywania zapytań/odnajdywanie zasobów w ramach subskrypcji.

Jak skonfigurować zasady platformy Azure i zarządzać nimi:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Jak tworzyć zapytania za pomocą programu Azure Graph:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: Monitorowanie niezatwierdzonych aplikacji w zasobach obliczeniowych

**Wskazówki**: Nie dotyczy; to zalecenie jest przeznaczone dla zasobów obliczeniowych.

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Usuwanie niezatwierdzonych zasobów platformy Azure i aplikacji

**Wskazówki**: Nie dotyczy; to zalecenie jest przeznaczone dla zasobów obliczeniowych i platformy Azure jako całości.

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

### <a name="68-use-only-approved-applications"></a>6.8: Używaj wyłącznie zatwierdzonych aplikacji

**Wskazówki**: Nie dotyczy; to zalecenie jest przeznaczone dla zasobów obliczeniowych.

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

### <a name="69-use-only-approved-azure-services"></a>6.9: Korzystaj tylko z zatwierdzonych usług platformy Azure

**Wskazówki:** Użyj zasad platformy Azure, aby wprowadzić ograniczenia dotyczące typu zasobów, które można utworzyć w subskrypcjach klientów przy użyciu następujących wbudowanych definicji zasad:

- Niedozwolone typy zasobów

- Dozwolone typy zasobów

Jak skonfigurować zasady platformy Azure i zarządzać nimi:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Jak odmówić określonego typu zasobu za pomocą usługi Azure Policy:https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="610-implement-approved-application-list"></a>6.10: Wdrożenie listy zatwierdzonych wniosków

**Wskazówki**: Nie dotyczy; to zalecenie jest przeznaczone dla zasobów obliczeniowych.

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

### <a name="611-divlimit-users-ability-to-interact-with-azure-resource-manager-via-scriptsdiv"></a>6.11: <div>Ograniczanie możliwości interakcji użytkowników z usługą Azure Resource Manager za pomocą skryptów</div>

**Wskazówki:** Skonfiguruj dostęp warunkowy platformy Azure, aby ograniczyć użytkownikom możliwość interakcji z usługą Azure Resource Manager, konfigurując "Zablokuj dostęp" dla aplikacji "Microsoft Azure Management".

Jak skonfigurować dostęp warunkowy, aby zablokować dostęp do usługi Azure Resource Manager:https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: Ograniczenie możliwości wykonywania skryptów przez użytkowników w zasobach obliczeniowych

**Wskazówki**: Nie dotyczy; to zalecenie jest przeznaczone dla zasobów obliczeniowych.

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Fizycznie lub logicznie segregować aplikacje wysokiego ryzyka

**Wskazówki**: Nie dotyczy; to zalecenie jest przeznaczone dla aplikacji sieci web działających w usłudze Azure App Service lub zasobów obliczeniowych.

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

## <a name="secure-configuration"></a>Bezpieczna konfiguracja

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Bezpieczna konfiguracja](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Ustanawianie bezpiecznych konfiguracji dla wszystkich zasobów platformy Azure

**Wskazówki:** Definiowanie i implementowanie standardowych konfiguracji zabezpieczeń dla wdrożeń usługi Azure Event Hubs. Użyj aliasów zasad platformy Azure w obszarze nazw "Microsoft.EventHub", aby utworzyć niestandardowe zasady do inspekcji lub wymuszania konfiguracji. Można również korzystać z wbudowanych definicji zasad dla usługi Azure Event Hubs, takich jak:

- Dzienniki diagnostyczne w Centrum zdarzeń powinny być włączone

- Centrum zdarzeń powinno używać punktu końcowego usługi sieci wirtualnej

Wbudowane zasady platformy Azure dla obszaru nazw centrów zdarzeń:https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#event-hub

Jak wyświetlić dostępne aliasy zasad platformy Azure:https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

Jak skonfigurować zasady platformy Azure i zarządzać nimi:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: Ustanawianie bezpiecznych konfiguracji systemów operacyjnych

**Wskazówki**: Nie dotyczy; to zalecenie jest przeznaczone dla zasobów obliczeniowych.

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Obsługa bezpiecznych konfiguracji zasobów platformy Azure

**Wskazówki:** Użyj zasad platformy Azure [odmów] i [wdrożyć, jeśli nie istnieje], aby wymusić bezpieczne ustawienia w zasobach obsługujących usługi Event Hubs. 

Jak skonfigurować zasady platformy Azure i zarządzać nimi:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

 
Aby uzyskać więcej informacji na temat efektów zasad platformy Azure:https://docs.microsoft.com/azure/governance/policy/concepts/effects

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Obsługa bezpiecznych konfiguracji systemów operacyjnych

**Wskazówki**: Nie dotyczy; to zalecenie jest przeznaczone dla zasobów obliczeniowych.

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Bezpieczne przechowywanie konfiguracji zasobów platformy Azure

**Wskazówki:** Jeśli używasz niestandardowych definicji zasad platformy Azure dla centrów zdarzeń lub powiązanych zasobów, użyj aplikacji Repo platformy Azure, aby bezpiecznie przechowywać kod i zarządzać nim.

Jak przechowywać kod w usłudze Azure DevOps:https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

Dokumentacja repozytorium platformy Azure:https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Bezpieczne przechowywanie obrazów niestandardowych systemów operacyjnych

**Wskazówki**: Nie dotyczy; to zalecenie jest przeznaczone dla zasobów obliczeniowych.

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

### <a name="77-deploy-system-configuration-management-tools"></a>7.7: Wdrażanie narzędzi do zarządzania konfiguracją systemu

**Wskazówki:** Użyj aliasów zasad platformy Azure w obszarze nazw "Microsoft.EventHub", aby utworzyć niestandardowe zasady do ostrzegania, inspekcji i wymuszania konfiguracji systemu. Ponadto opracuj proces i potok do zarządzania wyjątkami zasad.

Jak skonfigurować zasady platformy Azure i zarządzać nimi:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8: Wdrażanie narzędzi do zarządzania konfiguracją systemu dla systemów operacyjnych

**Wskazówki**: Nie dotyczy; to zalecenie jest przeznaczone dla zasobów obliczeniowych.

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9: Wdrażanie automatycznego monitorowania konfiguracji usług platformy Azure

**Wskazówki:** Użyj aliasów zasad platformy Azure w obszarze nazw "Microsoft.EventHub", aby utworzyć niestandardowe zasady do ostrzegania, inspekcji i wymuszania konfiguracji systemu. Użyj zasad platformy Azure [inspekcja], [odmówić] i [wdrożyć, jeśli nie istnieje], aby automatycznie wymusić konfiguracje dla wdrożeń usługi Azure Event Hubs i powiązanych zasobów.

Jak skonfigurować zasady platformy Azure i zarządzać nimi:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Wdrożenie automatycznego monitorowania konfiguracji systemów operacyjnych

**Wskazówki**: Nie dotyczy; to zalecenie jest przeznaczone dla zasobów obliczeniowych.

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

### <a name="711-manage-azure-secrets-securely"></a>7.11: Bezpieczne zarządzanie wpisami tajnymi platformy Azure

**Wskazówki:** W przypadku maszyn wirtualnych platformy Azure lub aplikacji sieci web uruchomionych w usłudze Azure App Service używanych do uzyskiwania dostępu do centrów zdarzeń, użyj tożsamości usługi zarządzanej w połączeniu z usługą Azure Key Vault, aby uprościć i zabezpieczyć zarządzanie podpisami dostępu współdzielonego dla wdrożeń usługi Azure Event Hubs. Upewnij się, że usługa soft-delete magazynu kluczy jest włączona.

Uwierzytelnij tożsamość zarządzaną za pomocą usługi Azure Active Directory, aby uzyskać dostęp do zasobów usługi Event Hubs:https://docs.microsoft.com/azure/event-hubs/authenticate-managed-identity?tabs=latest

Konfigurowanie kluczy zarządzanych przez klienta dla centrów zdarzeń:https://docs.microsoft.com/azure/event-hubs/configure-customer-managed-key 

Jak zintegrować się z tożsamościami zarządzanymi platformy Azure:https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

Jak utworzyć przechowalnię kluczy:https://docs.microsoft.com/azure/key-vault/quick-create-portal

Jak zapewnić uwierzytelnianie usługi Key Vault z tożsamością zarządzaną:https://docs.microsoft.com/azure/key-vault/managed-identity

**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Bezpieczne i automatyczne zarządzanie tożsamościami

**Wskazówki:** W przypadku maszyn wirtualnych platformy Azure lub aplikacji sieci web uruchomionych w usłudze Azure App Service używanych do uzyskiwania dostępu do centrów zdarzeń, użyj tożsamości usługi zarządzanej w połączeniu z usługą Azure Key Vault, aby uprościć i zabezpieczyć usługi Azure Event Hubs. Upewnij się, że usługa soft-delete magazynu kluczy jest włączona.

Użyj tożsamości zarządzanych, aby zapewnić usługi platformy Azure z automatycznie zarządzaną tożsamością w usłudze Azure Active Directory (AD). Tożsamości zarządzane umożliwia uwierzytelnianie do dowolnej usługi, która obsługuje uwierzytelnianie usługi Azure AD, w tym usługi Azure Key Vault, bez żadnych poświadczeń w kodzie.

Uwierzytelnij tożsamość zarządzaną za pomocą usługi Azure Active Directory, aby uzyskać dostęp do zasobów usługi Event Hubs:https://docs.microsoft.com/azure/event-hubs/authenticate-managed-identity?tabs=latest 

Konfigurowanie kluczy zarządzanych przez klienta dla centrów zdarzeń:https://docs.microsoft.com/azure/event-hubs/configure-customer-managed-key 

Jak skonfigurować tożsamości zarządzane:https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm

Jak zintegrować się z tożsamościami zarządzanymi platformy Azure:https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Wyeliminuj niezamierzone narażenie na poświadczenia

**Wskazówki:** Implementowanie skanera poświadczeń w celu zidentyfikowania poświadczeń w kodzie. Skaner poświadczeń będzie również zachęcać do przenoszenia wykrytych poświadczeń do bezpieczniejszych lokalizacji, takich jak Usługa Azure Key Vault.

Jak skonfigurować Skaner poświadczeń:https://secdevtools.azurewebsites.net/helpcredscan.html

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

## <a name="malware-defense"></a>Ochrona przed złośliwym oprogramowaniem

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Ochrona przed złośliwym oprogramowaniem](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: Korzystanie z centralnie zarządzanego oprogramowania chroniącego przed złośliwym oprogramowaniem

**Wskazówki**: Nie dotyczy; to zalecenie jest przeznaczone dla zasobów obliczeniowych.

Ochrona przed złośliwym oprogramowaniem firmy Microsoft jest włączona na podstawowym hoście, który obsługuje usługi platformy Azure (na przykład usługa Azure App Service), jednak nie jest uruchamiana na zawartości klienta.

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Przed skanowaniem plików, które mają zostać przesłane do zasobów platformy Azure nieoliczonych

**Wskazówki:** Wstępnie skanuj dowolną zawartość przekazywanych do nie obliczających zasobów platformy Azure, takich jak usługi Azure Event Hubs, Usługa aplikacji, magazyn usługi Data Lake, usługa Blob Storage, usługa Azure Database for PostgreSQL itp. Firma Microsoft nie może uzyskać dostępu do danych użytkownika w takich przypadkach.

Ochrona przed złośliwym oprogramowaniem firmy Microsoft jest włączona na podstawowym hoście, który obsługuje usługi platformy Azure (na przykład Usługa Azure Cache for Redis), jednak nie działa na zawartości klienta.

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: Upewnij się, że oprogramowanie i podpisy chroniące przed złośliwym oprogramowaniem są aktualizowane

**Wskazówki**: Nie dotyczy; to zalecenie jest przeznaczone dla zasobów obliczeniowych.

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

## <a name="data-recovery"></a>Odzyskiwanie danych

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Odzyskiwanie danych](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Zapewnienie regularnych automatycznych kopii zapasowych

**Wskazówki:** Konfigurowanie odzyskiwania po awarii geograficznej dla usługi Azure Event Hubs. Gdy całe regiony platformy Azure lub centra danych (jeśli nie są używane strefy dostępności) występują przestoje, ważne jest, aby przetwarzanie danych nadal działało w innym regionie lub centrum danych. W związku z tym odzyskiwanie po awarii geograficznej i replikacja geograficzna są ważnymi funkcjami dla każdego przedsiębiorstwa. Usługa Azure Event Hubs obsługuje zarówno odzyskiwanie po awarii geograficznej, jak i replikację geograficzną na poziomie obszaru nazw. 

Poznaj odzyskiwanie po awarii geograficznej dla usługi Azure Event Hubs:https://docs.microsoft.com/azure/event-hubs/event-hubs-geo-dr#availability-zones

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Wykonywanie kompletnych kopii zapasowych systemu i tworzenie kopii zapasowych dowolnych kluczy zarządzanych przez klienta

**Wskazówki:** Usługa Azure Event Hubs zapewnia szyfrowanie danych w pozostałej części za pomocą szyfrowania usługi Azure Storage Service (Azure SSE). Usługa Event Hubs polega na usłudze Azure Storage do przechowywania danych i domyślnie wszystkie dane przechowywane w usłudze Azure Storage są szyfrowane przy użyciu kluczy zarządzanych przez firmę Microsoft. Jeśli używasz usługi Azure Key Vault do przechowywania kluczy zarządzanych przez klienta, upewnij się, że regularne automatyczne kopie zapasowe kluczy.

Zapewnij regularne automatyczne tworzenie kopii zapasowych funkcji Key Vault Secrets za pomocą następującego polecenia programu PowerShell: Backup-AzKeyVaultSecret

Jak skonfigurować klucze zarządzane przez klienta do szyfrowania danych usługi Azure Event Hubs w stanie spoczynku:https://docs.microsoft.com/azure/event-hubs/configure-customer-managed-key

Jak wykonać kopię zapasową key vault secrets:https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultsecret

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Sprawdzanie poprawności wszystkich kopii zapasowych, w tym kluczy zarządzanych przez klienta

**Wskazówki:** Przetestuj przywracanie kopii zapasowych kluczy zarządzanych przez klienta.

 

Jak przywrócić klucze magazynu kluczy na platformie Azure:https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Zapewnienie ochrony kopii zapasowych i kluczy zarządzanych przez klienta

**Wskazówki:** Włącz usuwanie programowe w magazynie kluczy, aby chronić klucze przed przypadkowym lub złośliwym usunięciem. Usługa Azure Event Hubs wymaga, aby klucze zarządzane przez klienta były skonfigurowane do usuwania nietrwałego i nie czyścić.

Skonfiguruj usuwanie programowe dla konta usługi Azure Storage używanego do przechwytywania danych usługi Event Hubs. Należy zauważyć, że ta funkcja nie jest jeszcze obsługiwana dla usługi Azure Data Lake Storage Gen 2.

Jak włączyć usuwanie nietrwałe w przechowalni kluczy:https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal

Konfigurowanie magazynu kluczy za pomocą kluczy:https://docs.microsoft.com/azure/event-hubs/configure-customer-managed-key

Usuwanie nietrwałe dla obiektów blob usługi Azure Storage:https://docs.microsoft.com//azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal

**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

## <a name="incident-response"></a>Reagowanie na zdarzenia

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Reagowanie na incydenty](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Tworzenie przewodnika po zdarzeniach

**Wskazówki**: Upewnij się, że istnieją pisemne plany reagowania na incydenty, które określają role personelu, a także fazy obsługi incydentów/zarządzania.

Jak skonfigurować automatyzacje przepływu pracy w usłudze Azure Security Center:https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide



**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Tworzenie procedury oceniania incydentów i ustalania priorytetów

**Wskazówki:** Centrum zabezpieczeń przypisuje ważność alertów, aby ułatwić ustalanie priorytetów kolejności, w jakiej uczestniczysz w każdym alertie, dzięki czemu gdy zasób zostanie naruszony, możesz uzyskać do niego od razu. Ważność opiera się na tym, jak pewny jest usługa Security Center w znalezieniu lub analityczna używana do wystawiania alertu, a także poziom zaufania, że za działaniem, które doprowadziło do alertu, było złośliwe intencje.

**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

### <a name="103-test-security-response-procedures"></a>10.3: Testowanie procedur reagowania na zabezpieczenia

**Wskazówki**: Przeprowadzaj ćwiczenia, aby przetestować możliwości reagowania na incydenty w systemach na regularnym rytmie. Zidentyfikować słabe punkty i luki oraz w razie potrzeby dokonać przeglądu planu.

Zapoznaj się z publikacją NIST: Przewodnik po testach, szkoleniach i programach ćwiczeń dla planów i możliwości IT:https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Podaj dane kontaktowe zdarzeń bezpieczeństwa i konfiguruj powiadomienia o alertach dla incydentów bezpieczeństwa

**Wskazówki:** Informacje kontaktowe dotyczące zdarzenia zabezpieczeń będą używane przez firmę Microsoft do kontaktu się z Użytkownikiem, jeśli Centrum Microsoft Security Response Center (MSRC) odkryje, że dane klienta zostały uzyskiane przez osobę niezgodną z prawem lub nieautoryzowaną.  Przejrzyj incydenty po fakcie, aby upewnić się, że problemy zostały rozwiązane. 

Jak ustawić kontakt zabezpieczeń usługi Azure Security Center:https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Włącz alerty bezpieczeństwa do systemu reagowania na incydenty

**Wskazówki:** Eksportuj alerty i zalecenia usługi Azure Security Center przy użyciu funkcji ciągłego eksportowania. Ciągły eksport umożliwia eksportowanie alertów i zaleceń ręcznie lub w sposób ciągły. Możesz użyć łącznika danych usługi Azure Security Center do przesyłania strumieniowego alertów Sentinel.

Jak skonfigurować eksport ciągły:https://docs.microsoft.com/azure/security-center/continuous-export

Jak przesyłać strumieniowo alerty do usługi Azure Sentinel:https://docs.microsoft.com/azure/sentinel/connect-azure-security-center

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatyzacja reakcji na alerty bezpieczeństwa

**Wskazówki:** Użyj funkcji automatyzacji przepływu pracy w usłudze Azure Security Center, aby automatycznie wyzwalać odpowiedzi za pośrednictwem "Aplikacji logiki" w alertach i zaleceniach dotyczących zabezpieczeń.

Jak skonfigurować automatyzację przepływu pracy i aplikacje logiki:https://docs.microsoft.com/azure/security-center/workflow-automation

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

## <a name="penetration-tests-and-red-team-exercises"></a>Testy penetracyjne i ćwiczenia typu „red team”

*Aby uzyskać więcej informacji, zobacz [Kontrola bezpieczeństwa: Testy penetracyjne i Ćwiczenia zespołu Czerwonego](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11.1: Przeprowadzaj regularne testy penetracyjne zasobów platformy Azure i zapewnij korygowanie wszystkich krytycznych ustaleń dotyczących zabezpieczeń w ciągu 60 dni

**Wskazówki**: Postępuj zgodnie z regułami zaangażowania firmy Microsoft, aby https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1upewnić się, że testy penetracyjne nie naruszają zasad firmy Microsoft: .
Więcej informacji na temat strategii firmy Microsoft i wykonywania testów red teaming i testów penetracji witryn na żywo w oparciu o zarządzał infrastrukturą, usługami i aplikacjami firmy Microsoft w chmurze:https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

## <a name="next-steps"></a>Następne kroki

- Zobacz [wzorzec zabezpieczeń platformy Azure](https://docs.microsoft.com/azure/security/benchmarks/overview)
- Dowiedz się więcej o [planach bazowych zabezpieczeń platformy Azure](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)
