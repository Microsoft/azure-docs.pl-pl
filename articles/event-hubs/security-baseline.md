---
title: Podstawa zabezpieczeń platformy Azure dla Event Hubs
description: Podstawa zabezpieczeń platformy Azure dla Event Hubs
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 9386b154148cf5b5b6b7cd0b43f5a4b4aa741059
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96339697"
---
# <a name="azure-security-baseline-for-event-hubs"></a>Podstawa zabezpieczeń platformy Azure dla Event Hubs

Podstawą zabezpieczeń platformy Azure dla Event Hubs są zalecenia, które pomogą ulepszyć stan bezpieczeństwa wdrożenia.

Punkt odniesienia dla tej usługi jest rysowany w [wersji 1,0 usługi Azure Security test](../security/benchmarks/overview.md), która zawiera zalecenia dotyczące sposobu zabezpieczania rozwiązań w chmurze na platformie Azure z naszymi najlepszymi wskazówkami.

Aby uzyskać więcej informacji, zobacz [podstawy zabezpieczeń platformy Azure — omówienie](../security/benchmarks/security-baselines-overview.md).

## <a name="network-security"></a>Bezpieczeństwo sieci

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: zabezpieczenia sieci](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1,1: Ochrona zasobów przy użyciu sieciowych grup zabezpieczeń lub zapory platformy Azure na Virtual Network

**Wskazówki**: integracja centrów zdarzeń z punktami końcowymi usługi sieci wirtualnej umożliwia bezpieczny dostęp do funkcji obsługi komunikatów z obciążeń takich jak maszyny wirtualne, które są powiązane z sieciami wirtualnymi, przy czym ścieżka ruchu sieciowego jest zabezpieczana na obu końcach.

Po powiązaniu z co najmniej jednym punktem końcowym usługi podsieci sieci wirtualnej odpowiednia przestrzeń nazw Event Hubs nie akceptuje już ruchu z dowolnego miejsca, ale autoryzowanych podsieci w sieciach wirtualnych. Z punktu widzenia sieci wirtualnej powiązanie przestrzeni nazw Event Hubs z punktem końcowym usługi konfiguruje odizolowany tunel sieciowy z podsieci sieci wirtualnej do usługi obsługi komunikatów. 

Możesz również utworzyć prywatny punkt końcowy, który jest interfejsem sieciowym, który łączy Cię prywatnie i bezpiecznie z usługą Azure Event Hubs Service przy użyciu usługi link prywatny platformy Azure. Prywatny punkt końcowy używa prywatnego adresu IP z sieci wirtualnej, co skutecznie doprowadza usługę do sieci wirtualnej. Cały ruch do usługi może być kierowany przez prywatny punkt końcowy, dlatego nie są konieczne żadne bramy, urządzenia NAT, połączenia ExpressRoute lub sieci VPN ani publiczne adresy IP. 

Możesz również zabezpieczyć przestrzeń nazw platformy Azure Event Hubs przy użyciu zapór. Usługa Azure Event Hubs obsługuje kontrolę dostępu opartą na protokole IP na potrzeby obsługi zapory przychodzącej. Reguły zapory można ustawiać przy użyciu Azure Portal, Azure Resource Manager szablonów lub interfejsu wiersza polecenia platformy Azure lub Azure PowerShell.

Jak korzystać z punktów końcowych usługi sieci wirtualnej w usłudze Azure Event Hubs: https://docs.microsoft.com/azure/event-hubs/event-hubs-service-endpoints

Aby uzyskać więcej informacji, zobacz Integrowanie usługi Azure Event Hubs z prywatnym łączem platformy Azure: https://docs.microsoft.com/azure/event-hubs/private-link-service .

Włącz integrację i zapory sieci wirtualnych w Event Hubs przestrzeni nazw: https://docs.microsoft.com/azure/event-hubs/event-hubs-tutorial-virtual-networks-firewalls

Jak skonfigurować reguły zapory IP dla przestrzeni nazw platformy Azure Event Hubs: https://docs.microsoft.com/azure/event-hubs/event-hubs-ip-filtering

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1,2: Monitoruj i Rejestruj konfigurację oraz ruch sieci wirtualnych, podsieci i kart sieciowych

**Wskazówki**: Użyj Azure Security Center i postępuj zgodnie z zaleceniami dotyczącymi ochrony sieci, aby zabezpieczyć zasoby Event Hubs na platformie Azure. W przypadku korzystania z usługi Azure Virtual Machines w celu uzyskania dostępu do centrów zdarzeń Włącz dzienniki przepływu sieciowej grupy zabezpieczeń (sieciowej grupy zabezpieczeń) i Wyślij dzienniki do konta magazynu na potrzeby inspekcji ruchu.

Jak włączyć dzienniki przepływu sieciowej grupy zabezpieczeń: https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Informacje o zabezpieczeniach sieci zapewnianych przez Azure Security Center: https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="13-protect-critical-web-applications"></a>1,3: Ochrona krytycznych aplikacji sieci Web

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone dla aplikacji sieci Web działających na Azure App Service lub zasobach obliczeniowych.

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: odmowa komunikacji ze znanymi złośliwymi adresami IP

**Wskazówki**: Włączanie standardu DDoS Protection w sieciach wirtualnych skojarzonych z centrami zdarzeń, aby chronić przed rozproszonymi atakami typu "odmowa usługi" (DDoS). Użyj Azure Security Center zintegrowanej analizy zagrożeń, aby odmówić komunikacji ze znanymi złośliwymi lub nieużywanymi adresami IP.

Jak skonfigurować ochronę DDoS: [https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection](../ddos-protection/manage-ddos-protection.md)

Aby uzyskać więcej informacji na temat Azure Security Center zintegrowanej analizy zagrożeń: https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="15-record-network-packets-and-flow-logs"></a>1,5: rejestrowanie pakietów sieciowych i dzienników przepływów

**Wskazówki**: w przypadku korzystania z maszyn wirtualnych platformy Azure w celu uzyskania dostępu do centrów zdarzeń Włącz dzienniki przepływu sieciowych grup zabezpieczeń (sieciowej grupy zabezpieczeń) i Wyślij dzienniki do konta magazynu na potrzeby inspekcji ruchu. Możesz również wysłać dzienniki przepływu sieciowej grupy zabezpieczeń do obszaru roboczego Log Analytics i użyć Analiza ruchu, aby uzyskać wgląd w przepływ ruchu w chmurze platformy Azure. Niektóre zalety Analiza ruchu to możliwość wizualizacji aktywności sieciowej i identyfikowania aktywnych punktów, identyfikowania zagrożeń bezpieczeństwa, zrozumienia wzorców przepływu ruchu i wyznaczania konfiguracji sieci.

Jeśli jest to wymagane do badania nietypowego działania, Włącz funkcję przechwytywania pakietów Network Watcher.

Jak włączyć dzienniki przepływu sieciowej grupy zabezpieczeń: https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Jak włączyć i używać Analiza ruchu: https://docs.microsoft.com/azure/network-watcher/traffic-analytics

Jak włączyć Network Watcher: https://docs.microsoft.com/azure/network-watcher/network-watcher-create

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: Wdróż systemy zapobiegania wykrywaniu dostępu do sieci/dostępu intruzów (identyfikatory/adresy IP)

**Wskazówki**: Jeśli korzystasz z usługi Azure Virtual Machines w celu uzyskania dostępu do centrów zdarzeń, wybierz ofertę z portalu Azure Marketplace, która obsługuje funkcje identyfikatorów/adresów IP z możliwościami inspekcji ładunku. Jeśli w Twojej organizacji nie jest wymagana funkcja wykrywania i/lub zapobiegania włamaniom, można użyć wbudowanej zapory Event Hubs platformy Azure. Można ograniczyć dostęp do przestrzeni nazw Event Hubs dla ograniczonego zakresu adresów IP lub określonego adresu IP przy użyciu reguł zapory.

Portal Azure Marketplace:

https://azuremarketplace.microsoft.com/marketplace/?term=Firewall

Jak dodać regułę zapory w Event Hubs dla określonego adresu IP:

 https://docs.microsoft.com/azure/event-hubs/event-hubs-ip-filtering

**Monitorowanie Azure Security Center**: nie jest jeszcze dostępne

**Odpowiedzialność**: Klient

### <a name="17-manage-traffic-to-web-applications"></a>1,7: zarządzanie ruchem do aplikacji sieci Web

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone dla aplikacji sieci Web działających na Azure App Service lub zasobach obliczeniowych.

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: Minimalizacja złożoności i kosztów administracyjnych reguł zabezpieczeń sieci

**Wskazówki**: nie dotyczy, to zalecenie jest przeznaczone dla aplikacji sieci Web działających na Azure App Service lub zasobach obliczeniowych.

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: Obsługa standardowych konfiguracji zabezpieczeń dla urządzeń sieciowych

**Wskazówki**: Definiowanie i implementowanie standardowych konfiguracji zabezpieczeń dla zasobów sieciowych skojarzonych z przestrzeniami nazw platformy Azure Event Hubs przy użyciu Azure Policy. Użyj aliasów Azure Policy w przestrzeniach nazw "Microsoft. EventHub" i "Microsoft. Network", aby utworzyć zasady niestandardowe do inspekcji lub wymuszania konfiguracji sieciowej Event Hubs przestrzeni nazw. Mogą również korzystać z wbudowanych definicji zasad związanych z usługą Azure Event Hubs, takich jak:

- Centrum zdarzeń powinno korzystać z punktu końcowego usługi sieci wirtualnej.

Jak skonfigurować Azure Policy i zarządzać nimi: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Wbudowane zasady platformy Azure dla Event Hubs przestrzeni nazw:  https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#event-hub



Przykłady Azure Policy dla sieci: https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#network



Jak utworzyć Azure Blueprint: https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="110-document-traffic-configuration-rules"></a>1,10: udokumentowanie reguł konfiguracji ruchu

**Wskazówki**: używanie tagów dla sieci wirtualnych i innych zasobów związanych z zabezpieczeniami sieci i przepływem ruchu, które są skojarzone z centrami zdarzeń.

Tworzenie i używanie tagów: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: Użyj zautomatyzowanych narzędzi do monitorowania konfiguracji zasobów sieciowych i wykrywania zmian

**Wskazówki**: Użyj dziennika aktywności platformy Azure do monitorowania konfiguracji zasobów sieciowych i wykrywania zmian zasobów sieciowych związanych z usługą Azure Event Hubs. Tworzenie alertów w ramach Azure Monitor, które będą wyzwalane po wprowadzeniu zmian w krytycznych zasobach sieciowych.

Jak wyświetlać i pobierać zdarzenia dziennika aktywności platformy Azure: https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view

Jak utworzyć alerty w Azure Monitor: https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

## <a name="logging-and-monitoring"></a>Rejestrowanie i monitorowanie

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: rejestrowanie i monitorowanie](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2,1: Użyj źródeł synchronizacji zatwierdzonego czasu

**Wskazówki**: nie dotyczy; Firma Microsoft utrzymuje źródło czasu używane dla zasobów platformy Azure, takie jak Azure Event Hubs, dla sygnatur czasowych w dziennikach.

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2,2: Skonfiguruj centralne zarządzanie dziennikami zabezpieczeń

**Wskazówki**: w Azure monitor Skonfiguruj dzienniki dotyczące centrów zdarzeń w ustawieniach diagnostycznych dziennika aktywności i centrum zdarzeń, aby wysyłać dzienniki do log Analytics obszaru roboczego, w którym mają być wysyłane zapytania lub do konta magazynu w celu długoterminowego przechowywania danych archiwalnych.

Jak skonfigurować ustawienia diagnostyczne dla Event Hubs platformy Azure: https://docs.microsoft.com/azure/event-hubs/event-hubs-diagnostic-logs

Informacje o dzienniku aktywności platformy Azure: https://docs.microsoft.com/azure/azure-monitor/platform/platform-logs-overview

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: Włączanie rejestrowania inspekcji dla zasobów platformy Azure

**Wskazówki**: Włączanie ustawień diagnostycznych dla przestrzeni nazw platformy Azure Event Hubs. Istnieją trzy kategorie ustawień diagnostycznych dla platformy Azure Event Hubs: dzienniki archiwów, dzienniki operacyjne i dzienniki skalowania automatycznego. Włącz dzienniki operacyjne, aby przechwytywać informacje o tym, co dzieje się podczas operacji Event Hubs, w szczególności o typie operacji, w tym o tworzeniu centrum zdarzeń, używanych zasobach i stanie operacji.

Ponadto możesz włączyć ustawienia diagnostyki dziennika aktywności platformy Azure i wysłać je do konta usługi Azure Storage, centrum zdarzeń lub obszaru roboczego Log Analytics. Dzienniki aktywności zapewniają wgląd w operacje wykonywane na Event Hubsach i innych zasobach platformy Azure. Korzystając z dzienników aktywności, można określić "co, kto i kiedy" dla operacji zapisu (PUT, POST, DELETE) podejmowanych w przestrzeniach nazw platformy Azure Event Hubs.

Jak włączyć ustawienia diagnostyczne dla Event Hubs platformy Azure: https://docs.microsoft.com/azure/event-hubs/event-hubs-diagnostic-logs

Jak włączyć ustawienia diagnostyczne dla dziennika aktywności platformy Azure: https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: Zbierz dzienniki zabezpieczeń z systemów operacyjnych

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone do zasobów obliczeniowych.

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="25-configure-security-log-storage-retention"></a>2,5: Konfigurowanie przechowywania magazynu dzienników zabezpieczeń

**Wskazówki**: w Azure monitor ustawić okres przechowywania log Analytics obszaru roboczego zgodnie z regulacjami zgodności organizacji, aby przechwytywać i przeglądać zdarzenia związane z centrum zdarzeń.

Jak ustawić parametry przechowywania dziennika dla obszarów roboczych Log Analytics: https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="26-monitor-and-review-logs"></a>2,6: dzienniki monitorowania i przeglądania

**Wskazówki**: analizowanie i monitorowanie dzienników pod kątem nietypowego zachowania oraz regularnego przeglądania wyników związanych z centrami zdarzeń. Użyj Log Analytics Azure Monitor, aby przejrzeć dzienniki i wykonywać zapytania dotyczące danych dziennika. Alternatywnie możesz włączyć i dołączyć dane do usługi Azure wskaźnikowej lub SIEM innych firm.
 

Aby uzyskać więcej informacji na temat obszaru roboczego Log Analytics: https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal

Jak wykonywać niestandardowe zapytania w Azure Monitor: https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries

Jak dołączyć wskaźnik na platformie Azure: https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="27-enable-alerts-for-anomalous-activity"></a>2,7: Włączanie alertów dla nietypowego działania

**Wskazówki**: w Azure monitor Skonfiguruj dzienniki powiązane z usługą Azure Event Hubs w dzienniku aktywności oraz Event Hubs ustawienia diagnostyczne, aby wysyłać dzienniki do obszaru roboczego log Analytics, aby uzyskać zapytanie lub do konta magazynu w celu zapewnienia długoterminowego przechowywania danych. Użyj Log Analytics obszaru roboczego, aby utworzyć alerty dla nietypowej aktywności Znalezione w dziennikach i zdarzeniach zabezpieczeń.

Alternatywnie możesz włączyć i dołączyć dane do usługi Azure wskaźnikowej. 

Informacje o dzienniku aktywności platformy Azure: https://docs.microsoft.com/azure/azure-monitor/platform/platform-logs-overview

Jak skonfigurować ustawienia diagnostyczne dla Event Hubs platformy Azure: https://docs.microsoft.com/azure/event-hubs/event-hubs-diagnostic-logs

Jak otrzymywać alerty dotyczące Log Analytics danych dziennika obszaru roboczego: https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response

Jak dołączyć wskaźnik na platformie Azure: https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Monitorowanie Azure Security Center**: nie jest jeszcze dostępne

**Odpowiedzialność**: Klient

### <a name="28-centralize-anti-malware-logging"></a>2,8: scentralizowanie rejestrowania chroniącego przed złośliwym oprogramowaniem

**Wskazówki**: nie dotyczy; Centrum zdarzeń nie przetwarza rejestrowania chroniącego przed złośliwym oprogramowaniem.

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="29-enable-dns-query-logging"></a>2,9: Włączanie rejestrowania zapytań DNS

**Wskazówki**: nie dotyczy; Event Hubs nie przetwarza ani nie tworzy dzienników związanych z usługą DNS.

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="210-enable-command-line-audit-logging"></a>2,10: Włączanie rejestrowania inspekcji w wierszu polecenia

**Wskazówki**: nie dotyczy; te wytyczne są przeznaczone dla zasobów obliczeniowych.

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: nie dotyczy

## <a name="identity-and-access-control"></a>Tożsamość i kontrola dostępu

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: tożsamość i Access Control](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: obsługa spisu kont administracyjnych

**Wskazówki**: Azure Active Directory (AD) ma wbudowane role, które muszą być jawnie przypisane i są queryable. Za pomocą modułu Azure AD PowerShell można wykonywać zapytania ad hoc w celu odnajdywania kont należących do grup administracyjnych. 

Jak uzyskać rolę katalogu w usłudze Azure AD przy użyciu programu PowerShell: https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0

Jak uzyskać członków roli katalogu w usłudze Azure AD przy użyciu programu PowerShell: https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="32-change-default-passwords-where-applicable"></a>3,2: Zmień domyślne hasła, jeśli ma to zastosowanie

**Wskazówki**: płaszczyzna kontroli dostęp do Event Hubs jest kontrolowany za poorednictwem Azure Active Directory (AD). Usługa Azure AD nie ma koncepcji domyślnych haseł.

Dostęp do Event Hubs płaszczyzny danych jest kontrolowany za pomocą usługi Azure AD z tożsamościami zarządzanymi lub Rejestracje aplikacji, a także sygnaturami dostępu współdzielonego. Sygnatury dostępu współdzielonego są używane przez klientów łączących się z centrami zdarzeń i mogą być ponownie generowane w dowolnym momencie.

Informacje o sygnaturach dostępu współdzielonego dla Event Hubs: https://docs.microsoft.com/azure/event-hubs/authenticate-shared-access-signature

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: Użyj dedykowanych kont administracyjnych

**Wskazówki**: Tworzenie standardowych procedur operacyjnych dotyczących korzystania z dedykowanych kont administracyjnych. Użyj Azure Security Center Zarządzanie tożsamościami i dostępem, aby monitorować liczbę kont administracyjnych.

Ponadto, aby ułatwić śledzenie dedykowanych kont administracyjnych, można użyć zaleceń z Azure Security Center lub wbudowanych zasad platformy Azure, takich jak:

- Do subskrypcji powinien być przypisany więcej niż jeden właściciel

- Przestarzałe konta z uprawnieniami właściciela powinny zostać usunięte z subskrypcji

- Konta zewnętrzne z uprawnieniami właściciela powinny zostać usunięte z subskrypcji

Jak używać Azure Security Center do monitorowania tożsamości i dostępu (wersja zapoznawcza): https://docs.microsoft.com/azure/security-center/security-center-identity-access

Jak używać Azure Policy: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3,4: Korzystaj z logowania jednokrotnego (SSO) z usługą Azure Active Directory

**Wskazówki**: Microsoft Azure zapewnia zintegrowane zarządzanie kontrolą dostępu dla zasobów i aplikacji na podstawie Azure Active Directory (AD). Główną zaletą korzystania z usługi Azure AD z usługą Azure Event Hubs jest to, że nie musisz już przechowywać poświadczeń w kodzie. Zamiast tego można zażądać tokenu dostępu OAuth 2,0 z platformy tożsamości firmy Microsoft. Nazwa zasobu do żądania tokenu to https: \/ /eventhubs.Azure.NET/. Usługa Azure AD uwierzytelnia podmiot zabezpieczeń (użytkownika, grupę lub nazwę główną usługi), na którym działa aplikacja. W przypadku pomyślnego uwierzytelnienia usługa Azure AD zwraca token dostępu do aplikacji, a następnie może użyć tokenu dostępu do autoryzowania żądania do zasobów usługi Azure Event Hubs.

Jak uwierzytelnić aplikację przy użyciu usługi Azure AD w celu uzyskania dostępu do Event Hubs zasobów: https://docs.microsoft.com/azure/event-hubs/authenticate-application

Informacje o logowaniu jednokrotne za pomocą usługi Azure AD: https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Użyj uwierzytelniania wieloskładnikowego, aby uzyskać dostęp oparty na Azure Active Directory

**Wskazówki**: włączanie usługi Azure Active Directory Multi-Factor Authentication (MFA) i przestrzeganie Azure Security Center zaleceń dotyczących zarządzania tożsamościami i dostępem w celu ułatwienia ochrony zasobów z obsługą centrum zdarzeń.

Jak włączyć usługę MFA na platformie Azure: https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Jak monitorować tożsamość i dostęp w Azure Security Center: https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: Używaj dedykowanych maszyn (uprzywilejowany dostęp do stacji roboczych) dla wszystkich zadań administracyjnych

**Wskazówki**: Użyj stacji roboczych dostępu uprzywilejowanego (dostępem uprzywilejowanym) z usługą Multi-Factor Authentication (MFA) skonfigurowaną w celu logowania się i konfigurowania zasobów z obsługą centrum zdarzeń.

Dowiedz się więcej o stacjach roboczych uprzywilejowanego dostępu: https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations

Jak włączyć usługę MFA na platformie Azure: https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3,7: dziennik i alert dotyczący podejrzanego działania z kont administracyjnych

**Wskazówki**: Użyj Azure Active Directory (AD) PRIVILEGED Identity Management (PIM) do generowania dzienników i alertów w przypadku wystąpienia podejrzanych lub niebezpiecznych działań w środowisku. Użyj funkcji wykrywania ryzyka usługi Azure AD, aby wyświetlać alerty i raporty na temat ryzykownego zachowania użytkowników. Aby uzyskać dodatkowe rejestrowanie, Wyślij Azure Security Center alerty wykrywania ryzyka do Azure Monitor i skonfiguruj niestandardowe alerty/powiadomienia przy użyciu grup akcji.

Jak wdrożyć Privileged Identity Management (PIM): https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan

Informacje o wykrywaniu ryzyka usługi Azure AD: https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events

Jak skonfigurować grupy akcji dla niestandardowych alertów i powiadomień: https://docs.microsoft.com/azure/azure-monitor/platform/action-groups

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: zarządzanie zasobami platformy Azure tylko z zatwierdzonych lokalizacji

**Wskazówki**: Użyj dostępu warunkowego o nazwie Locations, aby zezwolić na dostęp tylko do określonych logicznych grup zakresów adresów IP lub krajów/regionów.



Jak skonfigurować nazwane lokalizacje na platformie Azure: https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="39-use-azure-active-directory"></a>3,9: Użyj Azure Active Directory

**Wskazówki**: Użyj Azure Active Directory (AD) jako centralnego systemu uwierzytelniania i autoryzacji dla zasobów platformy Azure, takich jak Event Hubs. Umożliwia to kontrolę dostępu opartą na rolach (Azure RBAC) na potrzeby administracyjnych zasobów poufnych.

 Jak utworzyć i skonfigurować wystąpienie usługi Azure AD: https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant

Aby dowiedzieć się, jak platforma Azure Event Hubs integruje się z usługą Azure Active Directory (AAD), zobacz temat autoryzowanie dostępu do Event Hubs zasobów przy użyciu Azure Active Directory: https://docs.microsoft.com/azure/event-hubs/authorize-access-azure-active-directory

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: regularnie Przeglądaj i Uzgodnij dostęp użytkowników

**Wskazówki**: Azure Active Directory (AD) zawiera dzienniki ułatwiające odnajdywanie starych kont. Ponadto za pomocą przeglądów dostępu do tożsamości platformy Azure można efektywnie zarządzać członkostwem w grupach, dostępem do aplikacji dla przedsiębiorstw i przypisaniami ról. Dostęp użytkowników może być regularnie przeglądany, aby upewnić się, że tylko Ci użytkownicy mają ciągły dostęp.

Dodatkowo należy regularnie obrócić sygnatury dostępu współdzielonego Event Hubs ".

Informacje o raportowaniu usługi Azure AD: https://docs.microsoft.com/azure/active-directory/reports-monitoring/

Jak korzystać z przeglądów dostępu do tożsamości platformy Azure: https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

Informacje o sygnaturach dostępu współdzielonego dla Event Hubs: https://docs.microsoft.com/azure/event-hubs/authenticate-shared-access-signature

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3,11: Monitor próbuje uzyskać dostęp do zdezaktywowanych kont

**Wskazówki**: masz dostęp do źródeł działań związanych z logowaniem do Azure Active Directory (AD), inspekcją i ryzykiem dzienników zdarzeń, które umożliwiają integrację z dowolnym narzędziem Siem/monitorowania.

Proces ten można usprawnić, tworząc ustawienia diagnostyczne dla kont użytkowników usługi Azure AD i wysyłając dzienniki inspekcji i dzienniki logowania do obszaru roboczego Log Analytics. Żądane alerty dzienników można skonfigurować w ramach Log Analytics.

Jak zintegrować dzienniki aktywności platformy Azure z Azure Monitor: https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

Autoryzuj dostęp do zasobów Event Hubs przy użyciu Azure Active Directory: https://docs.microsoft.com/azure/event-hubs/authorize-access-azure-active-directory

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: odchylenia zachowania podczas logowania do konta

**Wskazówki**: Użyj funkcji ochrony tożsamości i wykrywania ryzyka Azure Active Directory, aby skonfigurować automatyczne odpowiedzi na wykryte podejrzane działania związane z zasobami z obsługą Event Hubs. Aby zaimplementować odpowiedzi na zabezpieczenia organizacji, należy włączyć automatyczne odpowiedzi za pomocą wskaźnikowego platformy Azure.

Jak wyświetlić ryzykowne logowania usługi Azure AD: https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

Jak skonfigurować i włączyć zasady dotyczące ryzyka związanego z ochroną tożsamości: https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

Jak dołączyć wskaźnik na platformie Azure: https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: Zapewnij firmie Microsoft dostęp do odpowiednich danych klienta w scenariuszach pomocy technicznej

**Wskazówki**: obecnie niedostępne; Skrytka klienta nie jest jeszcze obsługiwana dla Event Hubs.

Lista usług obsługiwanych przez Skrytka klienta: https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: obecnie niedostępna

## <a name="data-protection"></a>Ochrona danych

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Ochrona danych](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: przechowywanie spisu poufnych informacji

**Wskazówki**: Użyj tagów dotyczących zasobów związanych z Event Hubs, aby pomóc w śledzeniu zasobów platformy Azure, które przechowują lub przetwarzają informacje poufne.

Tworzenie i używanie tagów: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: Izoluj systemy przechowujące lub przetwarzające informacje poufne

**Wskazówki**: implementowanie oddzielnych subskrypcji i/lub grup zarządzania na potrzeby tworzenia, testowania i produkcji. Przestrzeń nazw Event Hubs powinna być oddzielona przez sieć wirtualną z włączonymi punktami końcowymi usługi i odpowiednio oznakowane.

Możesz również zabezpieczyć przestrzeń nazw platformy Azure Event Hubs przy użyciu zapór. Usługa Azure Event Hubs obsługuje kontrolę dostępu opartą na protokole IP na potrzeby obsługi zapory przychodzącej. Reguły zapory można ustawiać przy użyciu Azure Portal, Azure Resource Manager szablonów lub interfejsu wiersza polecenia platformy Azure lub Azure PowerShell.

Jak utworzyć dodatkowe subskrypcje platformy Azure: https://docs.microsoft.com/azure/billing/billing-create-subscription

Jak utworzyć Grupy zarządzania: https://docs.microsoft.com/azure/governance/management-groups/create

Skonfiguruj reguły zapory adresów IP dla przestrzeni nazw platformy Azure Event Hubs: https://docs.microsoft.com/azure/event-hubs/event-hubs-ip-filtering

Jak tworzyć Tagi i korzystać z nich: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Jak utworzyć Virtual Network: https://docs.microsoft.com/azure/virtual-network/quick-create-portal

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: Monitoruj i blokuj nieautoryzowany transfer informacji poufnych

**Wskazówki**: w przypadku korzystania z maszyn wirtualnych w celu uzyskania dostępu do centrów zdarzeń Użyj sieci wirtualnych, punktów końcowych usług, Event Hubs zapory, sieciowych grup zabezpieczeń i tagów usług, aby ograniczyć możliwość eksfiltracjinia danych.

Firma Microsoft zarządza podstawową infrastrukturą dla systemu Azure Event Hubs i ma zaimplementowane ścisłe kontrole, aby zapobiec utracie lub narażeniu danych klientów.

Skonfiguruj reguły zapory adresów IP dla przestrzeni nazw platformy Azure Event Hubs: https://docs.microsoft.com/azure/event-hubs/event-hubs-ip-filtering

Informacje na temat punktów końcowych usługi Virtual Network za pomocą usługi Azure Event Hubs: https://docs.microsoft.com/azure/event-hubs/event-hubs-service-endpoints

Integracja usługi Azure Event Hubs z usługą Azure Private link: https://docs.microsoft.com/azure/event-hubs/private-link-service

Poznaj sieciowe grupy zabezpieczeń i Tagi usług: https://docs.microsoft.com/azure/virtual-network/security-overview

Zrozumienie ochrony danych klientów na platformie Azure: https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: Szyfruj wszystkie poufne informacje podczas przesyłania

**Wskazówki**: usługa Azure Event Hubs domyślnie wymusza komunikację z szyfrowanym protokołem TLS. Protokoły TLS w wersji 1,0, 1,1 i 1,2 są obecnie obsługiwane. Jednak protokoły TLS 1,0 i 1,1 znajdują się na ścieżce, aby wycofać całe branże, więc Użyj protokołu TLS 1,2, jeśli jest to możliwe.

Aby zrozumieć funkcje zabezpieczeń Event Hubs, zobacz Zabezpieczenia sieci:  https://docs.microsoft.com/azure/event-hubs/network-security

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Microsoft

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: Użyj aktywnego narzędzia do odnajdywania, aby identyfikować poufne dane

**Wskazówki**: funkcje identyfikacji, klasyfikacji i zapobiegania utracie danych nie są jeszcze dostępne dla Event Hubs platformy Azure. Zaimplementuj rozwiązanie innych firm, jeśli jest wymagane na potrzeby zgodności.

W przypadku podstawowej platformy zarządzanej przez firmę Microsoft Firma Microsoft traktuje całą zawartość klienta jako poufną i nadaje im dużą długość, aby chronić przed utratą i narażeniem danych przez klienta. Aby zapewnić bezpieczeństwo danych klienta na platformie Azure, firma Microsoft wdrożyła i utrzymuje pakiet niezawodnych kontroli i możliwości ochrony danych.

Zrozumienie ochrony danych klientów na platformie Azure: https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Współużytkowane

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: korzystanie z usługi Azure RBAC do kontrolowania dostępu do zasobów

**Wskazówki**: usługa Azure Event Hubs obsługuje używanie Azure Active Directory (AD) do autoryzacji żądań Event Hubs zasobów. Korzystając z usługi Azure AD, możesz użyć kontroli dostępu opartej na rolach (Azure RBAC), aby przyznać uprawnienia podmiotowi zabezpieczeń, który może być użytkownikiem lub podmiotem usługi aplikacji.

Poznaj usługę Azure RBAC i dostępne role dla Event Hubs platformy Azure: https://docs.microsoft.com/azure/event-hubs/authorize-access-azure-active-directory

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: Wymuś kontrolę dostępu przy użyciu ochrony przed utratą danych opartą na hoście

**Wskazówki**: nie dotyczy; te wytyczne są przeznaczone dla zasobów obliczeniowych.

Firma Microsoft zarządza podstawową infrastrukturą dla Event Hubs i ma zaimplementowane ścisłe kontrole, aby zapobiec utracie lub narażeniu danych klientów.

Zrozumienie ochrony danych klientów na platformie Azure: https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: Szyfruj poufne informacje w spoczynku

**Wskazówki**: usługa Azure Event Hubs obsługuje szyfrowanie danych przechowywanych przy użyciu kluczy zarządzanych przez firmę Microsoft lub kluczy zarządzanych przez klienta. Ta funkcja umożliwia tworzenie, obracanie, wyłączanie i odwoływanie dostępu do kluczy zarządzanych przez klienta, które są używane do szyfrowania danych Event Hubs platformy Azure.

Jak skonfigurować klucze zarządzane przez klienta do szyfrowania Event Hubs platformy Azure: https://docs.microsoft.com/azure/event-hubs/configure-customer-managed-key

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: Rejestruj i Ostrzegaj o zmianach krytycznych zasobów platformy Azure

**Wskazówki**: Użyj Azure monitor z dziennikiem aktywności platformy Azure, aby utworzyć alerty dla sytuacji, w których zmiany są wprowadzane do wystąpień produkcyjnych platformy Azure Event Hubs i innych krytycznych lub powiązanych zasobów.

Jak utworzyć alerty dla zdarzeń dziennika aktywności platformy Azure: https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

## <a name="vulnerability-management"></a>Zarządzanie lukami w zabezpieczeniach

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Zarządzanie lukami w zabezpieczeniach](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: uruchamianie narzędzi do skanowania automatycznych luk w zabezpieczeniach

**Wskazówki**: nie dotyczy; Firma Microsoft przeprowadza zarządzanie lukami w systemach podstawowych, które obsługują Event Hubs.

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Microsoft

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: Wdróż automatyczne rozwiązanie do zarządzania poprawkami systemu operacyjnego

**Wskazówki**: nie dotyczy; Firma Microsoft wykonuje zarządzanie poprawkami w systemach podstawowych, które obsługują Event Hubs.

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Microsoft

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5,3: Wdróż zautomatyzowane rozwiązanie do zarządzania poprawkami oprogramowania innych firm

**Wskazówki**: nie dotyczy; testy porównawcze są przeznaczone do zasobów obliczeniowych.

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Microsoft

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: porównanie luk w zabezpieczeniach z tyłu do tyłu

**Wskazówki**: nie dotyczy; Firma Microsoft przeprowadza zarządzanie lukami w systemach podstawowych, które obsługują Event Hubs.

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Microsoft

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: Użyj procesu oceny ryzyka, aby określić priorytety korygowania odkrytych luk w zabezpieczeniach

**Wskazówki**: nie dotyczy; Firma Microsoft przeprowadza zarządzanie lukami w systemach podstawowych, które obsługują Event Hubs.

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Microsoft

## <a name="inventory-and-asset-management"></a>Zarządzanie magazynem i zasobami

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Spis i zarządzanie zasobami](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-azure-asset-discovery"></a>6,1: Użyj odnajdywania zasobów platformy Azure

**Wskazówki**: Użyj grafu zasobów platformy Azure do wykonywania zapytań i odnajdywania wszystkich zasobów (w tym przestrzeni nazw platformy Azure Event Hubs) w ramach subskrypcji. Upewnij się, że masz odpowiednie uprawnienia (odczyt) w dzierżawie i że można wyliczyć wszystkie subskrypcje platformy Azure oraz zasoby w ramach subskrypcji.

Jak tworzyć zapytania przy użyciu grafu zasobów platformy Azure: https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Jak wyświetlić subskrypcje platformy Azure: https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

Opis kontroli RBAC platformy Azure: https://docs.microsoft.com/azure/role-based-access-control/overview

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="62-maintain-asset-metadata"></a>6,2: Konserwowanie metadanych zasobów

**Wskazówki**: stosowanie tagów do zasobów platformy Azure, dzięki czemu metadane są logicznie zorganizowane w taksonomię.

Tworzenie i używanie tagów: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: Usuń nieautoryzowane zasoby platformy Azure

**Wskazówki**: używanie tagowania, grup zarządzania i oddzielnych subskrypcji, gdzie to konieczne, do organizowania i śledzenia przestrzeni nazw i powiązanych zasobów platformy Azure Event Hubs. Regularnie Uzgadniaj spis i zapewnij, że nieautoryzowane zasoby są usuwane z subskrypcji w odpowiednim czasie.

Jak utworzyć dodatkowe subskrypcje platformy Azure: https://docs.microsoft.com/azure/billing/billing-create-subscription

Jak utworzyć Grupy zarządzania: https://docs.microsoft.com/azure/governance/management-groups/create

Tworzenie i używanie tagów: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6,4: przechowywanie spisu zatwierdzonych zasobów platformy Azure i tytułów oprogramowania

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone do zasobów obliczeniowych i platformy Azure jako całości.

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: Monitoruj niezatwierdzone zasoby platformy Azure

**Wskazówki**: Użyj Azure Policy, aby wprowadzić ograniczenia dotyczące typu zasobów, które można utworzyć w subskrypcjach klientów, przy użyciu następujących wbudowanych definicji zasad:

- Niedozwolone typy zasobów

- Dozwolone typy zasobów

Ponadto za pomocą usługi Azure Resource Graph można wykonywać zapytania/odnajdywać zasoby w ramach subskrypcji.

Jak skonfigurować Azure Policy i zarządzać nimi: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Jak tworzyć zapytania za pomocą usługi Azure Graph: https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: Monitoruj niezatwierdzone aplikacje oprogramowania w ramach zasobów obliczeniowych

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone do zasobów obliczeniowych.

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: Usuń niezatwierdzone zasoby platformy Azure i aplikacje oprogramowania

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone do zasobów obliczeniowych i platformy Azure jako całości.

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="68-use-only-approved-applications"></a>6,8: Używaj tylko zatwierdzonych aplikacji

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone do zasobów obliczeniowych.

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="69-use-only-approved-azure-services"></a>6,9: Używaj tylko zatwierdzonych usług platformy Azure

**Wskazówki**: Użyj Azure Policy, aby wprowadzić ograniczenia dotyczące typu zasobów, które można utworzyć w subskrypcjach klientów, przy użyciu następujących wbudowanych definicji zasad:

- Niedozwolone typy zasobów

- Dozwolone typy zasobów

Jak skonfigurować Azure Policy i zarządzać nimi: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Jak odmówić określonego typu zasobu Azure Policy: https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="610-implement-approved-application-list"></a>6,10: Zaimplementuj listę zatwierdzonych aplikacji

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone do zasobów obliczeniowych.

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="611-divlimit-users-ability-to-interact-with-azure-resource-manager-via-scriptsdiv"></a>6,11: <div>Ogranicz możliwość korzystania przez użytkowników z Azure Resource Manager za pośrednictwem skryptów</div>

**Wskazówki**: Skonfiguruj dostęp warunkowy platformy Azure, aby ograniczyć możliwość korzystania przez użytkowników z Azure Resource Manager przez skonfigurowanie "blokowania dostępu" dla aplikacji "Microsoft Azure Management".

Jak skonfigurować dostęp warunkowy w celu blokowania dostępu do Azure Resource Manager: https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6,12: Ogranicz możliwość wykonywania skryptów w zasobach obliczeniowych przez użytkowników

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone do zasobów obliczeniowych.

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: fizyczne lub logiczne rozdzielenie aplikacji wysokiego ryzyka

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone dla aplikacji sieci Web działających na Azure App Service lub zasobach obliczeniowych.

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: nie dotyczy

## <a name="secure-configuration"></a>Bezpieczna konfiguracja

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: bezpieczna konfiguracja](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: Ustanów bezpieczne konfiguracje dla wszystkich zasobów platformy Azure

**Wskazówki**: Definiowanie i implementowanie standardowych konfiguracji zabezpieczeń dla wdrożeń usługi Azure Event Hubs. Użyj aliasów Azure Policy w przestrzeni nazw "Microsoft. EventHub", aby utworzyć zasady niestandardowe do inspekcji lub wymuszania konfiguracji. Możesz również używać wbudowanych definicji zasad dla platformy Azure Event Hubs takich jak:

- Dzienniki diagnostyczne w centrum zdarzeń powinny być włączone

- Centrum zdarzeń powinno używać punktu końcowego usługi sieci wirtualnej

Wbudowane zasady platformy Azure dla Event Hubs przestrzeni nazw: https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#event-hub

Jak wyświetlić dostępne aliasy Azure Policy: https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

Jak skonfigurować Azure Policy i zarządzać nimi: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: Ustanów bezpieczne konfiguracje systemów operacyjnych

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone do zasobów obliczeniowych.

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: obsługa bezpiecznych konfiguracji zasobów platformy Azure

**Wskazówki**: Użyj Azure Policy [Odmów] i [Wdróż, jeśli nie istnieje], aby wymusić bezpieczne ustawienia w ramach zasobów z włączoną obsługą Event Hubs. 

Jak skonfigurować Azure Policy i zarządzać nimi:  https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

 
Aby uzyskać więcej informacji na temat efektów Azure Policy:  https://docs.microsoft.com/azure/governance/policy/concepts/effects

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: Zachowaj konfiguracje bezpiecznego systemu operacyjnego

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone do zasobów obliczeniowych.

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: bezpiecznie przechowuj konfigurację zasobów platformy Azure

**Wskazówki**: Jeśli używasz niestandardowych definicji Azure Policy dla Event Hubs lub powiązanych zasobów, użyj Azure Repos, aby bezpiecznie przechowywać kod i zarządzać nim.

Jak przechowywać kod w usłudze Azure DevOps: https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

Dokumentacja Azure Repos: https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: bezpieczne przechowywanie niestandardowych obrazów systemu operacyjnego

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone do zasobów obliczeniowych.

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="77-deploy-system-configuration-management-tools"></a>7,7: Wdrażanie narzędzi do zarządzania konfiguracją systemu

**Wskazówki**: Użyj aliasów Azure Policy w przestrzeni nazw "Microsoft. EventHub", aby utworzyć zasady niestandardowe na potrzeby alertów, inspekcji i wymuszania konfiguracji systemu. Dodatkowo opracowuj proces i potok na potrzeby zarządzania wyjątkami zasad.

Jak skonfigurować Azure Policy i zarządzać nimi: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7,8: Wdrażanie narzędzi do zarządzania konfiguracją systemu dla systemów operacyjnych

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone do zasobów obliczeniowych.

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7,9: Zaimplementuj automatyczne monitorowanie konfiguracji dla usług platformy Azure

**Wskazówki**: Użyj aliasów Azure Policy w przestrzeni nazw "Microsoft. EventHub", aby utworzyć zasady niestandardowe na potrzeby alertów, inspekcji i wymuszania konfiguracji systemu. Użyj Azure Policy [Audit], [Odmów] i [Wdróż, jeśli nie istnieje], aby automatycznie wymuszać konfiguracje dla wdrożeń Event Hubs platformy Azure i powiązanych zasobów.

Jak skonfigurować Azure Policy i zarządzać nimi: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: Zaimplementuj automatyczne monitorowanie konfiguracji dla systemów operacyjnych

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone do zasobów obliczeniowych.

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="711-manage-azure-secrets-securely"></a>7,11: bezpieczne zarządzanie wpisami tajnymi platformy Azure

**Wskazówki**: w przypadku maszyn wirtualnych platformy Azure lub aplikacji sieci Web działających na Azure App Service używanym do uzyskiwania dostępu do centrów zdarzeń należy używać tożsamość usługi zarządzanej w połączeniu z Azure Key Vault, aby uprościć i zabezpieczyć zarządzanie sygnaturami dostępu współdzielonego dla wdrożeń Event Hubs platformy Azure. Upewnij się, Key Vault jest włączona funkcja usuwania nietrwałego.

Uwierzytelniaj tożsamość zarządzaną za pomocą Azure Active Directory w celu uzyskania dostępu do zasobów Event Hubs: https://docs.microsoft.com/azure/event-hubs/authenticate-managed-identity?tabs=latest

Skonfiguruj klucze zarządzane przez klienta dla Event Hubs: https://docs.microsoft.com/azure/event-hubs/configure-customer-managed-key 

Jak przeprowadzić integrację z tożsamościami zarządzanymi przez platformę Azure: https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

Jak utworzyć Key Vault: https://docs.microsoft.com/azure/key-vault/general/quick-create-portal

Jak przeprowadzić uwierzytelnianie w Key Vault: https://docs.microsoft.com/azure/key-vault/general/authentication

Jak przypisać zasady dostępu Key Vault: https://docs.microsoft.com/azure/key-vault/general/assign-access-policy-portal

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: bezpieczne i automatyczne zarządzanie tożsamościami

**Wskazówki**: w przypadku maszyn wirtualnych platformy Azure lub aplikacji sieci Web działających na Azure App Service używanym do uzyskiwania dostępu do centrów zdarzeń należy użyć tożsamość usługi zarządzanej w połączeniu z Azure Key Vault, aby uprościć i zabezpieczyć Event Hubs platformy Azure. Upewnij się, Key Vault jest włączona funkcja usuwania nietrwałego.

Użyj tożsamości zarządzanych, aby zapewnić usługom platformy Azure automatyczną tożsamość zarządzaną w usłudze Azure Active Directory (AD). Tożsamości zarządzane umożliwiają uwierzytelnianie w dowolnej usłudze, która obsługuje uwierzytelnianie usługi Azure AD, w tym Azure Key Vault, bez żadnych poświadczeń w kodzie.

Uwierzytelniaj tożsamość zarządzaną za pomocą Azure Active Directory w celu uzyskania dostępu do zasobów Event Hubs: https://docs.microsoft.com/azure/event-hubs/authenticate-managed-identity?tabs=latest 

Skonfiguruj klucze zarządzane przez klienta dla Event Hubs: https://docs.microsoft.com/azure/event-hubs/configure-customer-managed-key 

Jak skonfigurować tożsamości zarządzane: https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm

Jak przeprowadzić integrację z tożsamościami zarządzanymi przez platformę Azure: https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: eliminowanie nieprzewidzianego narażenia na poświadczenia

**Wskazówki**: Implementuj skaner poświadczeń, aby identyfikować poświadczenia w kodzie. Skaner poświadczeń ułatwia również przenoszenie odnalezionych poświadczeń do bezpieczniejszych lokalizacji, takich jak usługa Azure Key Vault.

Jak skonfigurować skaner poświadczeń: https://secdevtools.azurewebsites.net/helpcredscan.html

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

## <a name="malware-defense"></a>Ochrona przed złośliwym oprogramowaniem

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Obrona złośliwego oprogramowania](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8,1: Użyj centralnie zarządzanego oprogramowania chroniącego przed złośliwym oprogramowaniem

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone do zasobów obliczeniowych.

Oprogramowanie chroniące przed złośliwym oprogramowaniem firmy Microsoft jest włączone na podstawowym hoście, który obsługuje usługi platformy Azure (na przykład Azure App Service), ale nie jest uruchamiane w treści klienta.

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: przeskanuj pliki przed przekazaniem do zasobów platformy Azure, które nie są obliczeniowe

**Wskazówki**: wstępnie Przeskanuj zawartość przekazywaną do zasobów platformy Azure, które nie są obliczeniowe, takich jak Azure Event Hubs, App Service, Data Lake Storage, Blob Storage, Azure Database for PostgreSQL itd. Firma Microsoft nie może uzyskać dostępu do danych w tych wystąpieniach.

Oprogramowanie chroniące przed złośliwym oprogramowaniem firmy Microsoft jest włączone na podstawowym hoście, który obsługuje usługi platformy Azure (na przykład usługa Azure cache for Redis), ale nie jest uruchamiane na treści klienta.

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8,3: Upewnij się, że oprogramowanie chroniące przed złośliwym oprogramowaniem i podpisy zostały zaktualizowane

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone do zasobów obliczeniowych.

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: nie dotyczy

## <a name="data-recovery"></a>Odzyskiwanie danych

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: odzyskiwanie danych](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: Zapewnij regularne zautomatyzowane przywracanie awaryjne

**Wskazówki**: Konfigurowanie odzyskiwania geograficznego po awarii dla Event Hubs platformy Azure. W przypadku awarii całego regionu platformy Azure lub centrów danych (jeśli nie są używane strefy dostępności) nie ma możliwości zapewnienia przestoju w celu przeprowadzenia działania w innym regionie lub w centrum. W związku z tym odzyskiwanie geograficzne i replikacja geograficzna są ważnymi funkcjami dla każdego przedsiębiorstwa. Usługa Azure Event Hubs obsługuje zarówno odzyskiwanie geograficzne po awarii, jak i replikację geograficzną, na poziomie przestrzeni nazw. 

Informacje na temat odzyskiwania po awarii geograficznej dla Event Hubs platformy Azure: https://docs.microsoft.com/azure/event-hubs/event-hubs-geo-dr#availability-zones

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: wykonaj kompletne kopie zapasowe systemu i Utwórz kopię zapasową wszystkich kluczy zarządzanych przez klienta

**Wskazówki**: usługa Azure Event Hubs zapewnia szyfrowanie danych przechowywanych w usłudze Azure szyfrowanie usługi Storage (SSE platformy Azure). Event Hubs opiera się na usłudze Azure Storage do przechowywania danych i domyślnie wszystkie dane przechowywane w usłudze Azure Storage są szyfrowane przy użyciu kluczy zarządzanych przez firmę Microsoft. W przypadku używania Azure Key Vault do przechowywania kluczy zarządzanych przez klienta należy zapewnić regularne automatyczne tworzenie kopii zapasowych kluczy.

Zadbaj o regularne automatyczne tworzenie kopii zapasowych danych tajnych Key Vault za pomocą następującego polecenia programu PowerShell: Backup-AzKeyVaultSecret

Jak skonfigurować klucze zarządzane przez klienta do szyfrowania danych Event Hubs platformy Azure w spoczynku: https://docs.microsoft.com/azure/event-hubs/configure-customer-managed-key

Jak utworzyć kopię zapasową Key Vault Secret: https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultsecret

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: Weryfikuj wszystkie kopie zapasowe, w tym klucze zarządzane przez klienta

**Wskazówki**: testowanie przywracania kopii zapasowych kluczy zarządzanych przez klienta.

 

Jak przywrócić klucze magazynu kluczy na platformie Azure: https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: zapewnianie ochrony kopii zapasowych i kluczy zarządzanych przez klienta

**Wskazówki**: Włącz usuwanie nietrwałe w Key Vault, aby chronić klucze przed przypadkowym lub złośliwym usunięciem. Usługa Azure Event Hubs wymaga, aby klucze zarządzane przez klienta miały nietrwałe usuwanie i nie zostały skonfigurowane.

Skonfiguruj nietrwałe usuwanie dla konta usługi Azure Storage używanego do przechwytywania danych Event Hubs. Należy pamiętać, że ta funkcja nie jest jeszcze obsługiwana w przypadku Azure Data Lake Storage generacji 2.

Jak włączyć usuwanie nietrwałe w Key Vault: https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal

Skonfiguruj Magazyn kluczy z kluczami: https://docs.microsoft.com/azure/event-hubs/configure-customer-managed-key

Usuwanie nietrwałe dla obiektów BLOB usługi Azure Storage: https://docs.microsoft.com//azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

## <a name="incident-response"></a>Reagowanie na zdarzenia

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: odpowiedź na zdarzenia](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: Tworzenie przewodnika odpowiedzi na zdarzenia

**Wskazówki**: Upewnij się, że istnieją plany odpowiedzi na zdarzenia, które definiują role pracowników, a także fazy obsługi zdarzeń/zarządzania.

Jak skonfigurować automatyzację przepływu pracy w ramach Azure Security Center: https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide



**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: Tworzenie oceny incydentu i procedury priorytetyzacji

**Wskazówki**: Security Center przypisuje ważność do alertów, ułatwiając określanie priorytetów kolejności, w której uczestniczy każdy alert, dzięki czemu w przypadku naruszenia zabezpieczeń zasobów można od razu uzyskać dostęp. Ważność jest oparta na tym, jak dobrze Security Center znajduje się w wyszukiwaniu lub analitycznym używanym do wystawiania alertu, a także poziom pewności, że istniało złośliwy wpływ na działanie, które prowadziło do alertu.

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="103-test-security-response-procedures"></a>10,3: procedury odpowiedzi na zabezpieczenia testowe

**Wskazówki**: przeprowadzanie ćwiczeń w celu przetestowania możliwości reagowania na zdarzenia systemu w regularnych erze. Zidentyfikuj słabe punkty i przerwy oraz popraw plan zgodnie z wymaganiami.

Zapoznaj się z publikacją NIST: Przewodnik dotyczący testowania, uczenia i ćwiczeń programów dla planów i możliwości IT: https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: Podaj szczegóły kontaktu dotyczącego zabezpieczeń i Skonfiguruj powiadomienia dotyczące alertów dotyczących zdarzeń związanych z zabezpieczeniami

**Wskazówki**: informacje kontaktowe dotyczące zdarzenia zabezpieczeń będą używane przez firmę Microsoft do skontaktowania się z Tobą, jeśli firma Microsoft Security Response Center (MSRC) wykryje, że dostęp do danych klienta został uzyskany przez nielegalną lub nieautoryzowaną osobę.  Przejrzyj zdarzenia po fakcie, aby upewnić się, że problemy zostały rozwiązane. 

Jak ustawić Azure Security Center kontaktu zabezpieczeń: https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: Uwzględnij alerty zabezpieczeń w systemie odpowiedzi na zdarzenia

**Wskazówki**: eksportowanie alertów i zaleceń dotyczących Azure Security Center przy użyciu funkcji eksportu ciągłego. Eksport ciągły umożliwia wyeksportowanie alertów i zaleceń ręcznie lub w stały sposób ciągły. Możesz użyć łącznika danych Azure Security Center, aby przesłać strumieniowo wskaźnik do alertów.

Jak skonfigurować eksport ciągły: https://docs.microsoft.com/azure/security-center/continuous-export

Jak przesłać strumieniowo alerty do usługi Azure wskaźnikowej: https://docs.microsoft.com/azure/sentinel/connect-azure-security-center

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: Automatyzowanie odpowiedzi na alerty zabezpieczeń

**Wskazówki**: Użyj funkcji automatyzacji przepływu pracy w programie Azure Security Center, aby automatycznie wyzwalać odpowiedzi za pośrednictwem "Logic Apps" na temat alertów zabezpieczeń i zaleceń.

Jak skonfigurować automatyzację przepływu pracy i Logic Apps: https://docs.microsoft.com/azure/security-center/workflow-automation

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

## <a name="penetration-tests-and-red-team-exercises"></a>Testy penetracyjne i ćwiczenia typu „red team”

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: testy penetracji i czerwone ćwiczenia zespołu](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11,1: Przeprowadź regularne testowanie penetracji zasobów platformy Azure i zadbaj o skorygowanie wszystkich krytycznych ustaleń dotyczących zabezpieczeń w ciągu 60 dni

**Wskazówki**: Postępuj zgodnie z zasadami firmy Microsoft dotyczącymi zaangażowania, aby upewnić się, że testy penetracji nie naruszają zasad firmy Microsoft: https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1 .
Aby uzyskać więcej informacji na temat strategii firmy Microsoft i wykonywania testów z zakresu tworzenia zespołu Red-site i testowania aplikacji na żywo, należy wykonać następujące działania: https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

## <a name="next-steps"></a>Następne kroki

- Zobacz [test porównawczy zabezpieczeń platformy Azure](../security/benchmarks/overview.md)
- Dowiedz się więcej o [punktach odniesienia zabezpieczeń platformy Azure](../security/benchmarks/security-baselines-overview.md)