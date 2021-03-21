---
title: Podstawa zabezpieczeń platformy Azure dla Event Hubs
description: Linia bazowa zabezpieczeń Event Hubs zawiera wskazówki i zasoby dotyczące procedur związanych z wdrażaniem zaleceń dotyczących zabezpieczeń określonych w teście zabezpieczeń platformy Azure.
author: msmbaldwin
ms.service: event-hubs
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 1e1a8cb24b47277b38bd2d64b733a6f28ee8acff
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101742511"
---
# <a name="azure-security-baseline-for-event-hubs"></a>Podstawa zabezpieczeń platformy Azure dla Event Hubs

Ta linia bazowa zabezpieczeń stosuje wskazówki z programu [Azure Security test w wersji 1,0](../security/benchmarks/overview-v1.md) do Event Hubs. Test porównawczy zabezpieczeń platformy Azure zawiera zalecenia dotyczące sposobu zabezpieczania rozwiązań w chmurze na platformie Azure.
Zawartość jest pogrupowana według **kontroli zabezpieczeń** zdefiniowanych przez program Azure Security test i powiązane wskazówki dotyczące Event Hubs. **Kontrolki** nie mają zastosowania do Event Hubs zostały wykluczone.

 
Aby dowiedzieć się, jak Event Hubs całkowicie mapować do testu porównawczego zabezpieczeń platformy Azure, zobacz [pełny Event Hubs pliku mapowania linii bazowej zabezpieczeń](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Bezpieczeństwo sieci

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: bezpieczeństwo sieci](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: Ochrona zasobów platformy Azure w ramach sieci wirtualnych

**Wskazówki**: integracja Event Hubs platformy Azure z punktami końcowymi usługi sieci wirtualnej umożliwia bezpieczny dostęp do możliwości obsługi komunikatów z obciążeń takich jak maszyny wirtualne, które są powiązane z sieciami wirtualnymi, przy czym ścieżka ruchu sieciowego jest zabezpieczana na obu końcach.

Po powiązaniu z co najmniej jednym punktem końcowym usługi podsieci sieci wirtualnej odpowiednia przestrzeń nazw Event Hubs nie akceptuje już ruchu z dowolnego miejsca, ale autoryzowanych podsieci w sieciach wirtualnych. Z punktu widzenia sieci wirtualnej powiązanie przestrzeni nazw Event Hubs z punktem końcowym usługi konfiguruje odizolowany tunel sieciowy z podsieci sieci wirtualnej do usługi obsługi komunikatów. 

Możesz również utworzyć prywatny punkt końcowy, który jest interfejsem sieciowym, który nawiązuje połączenie prywatne i bezpieczne do Event Hubs usługi przy użyciu usługi link prywatny platformy Azure. Prywatny punkt końcowy używa prywatnego adresu IP z sieci wirtualnej, efektywnie przenosząc usługę do sieci wirtualnej. Cały ruch do usługi może być kierowany przez prywatny punkt końcowy, dlatego nie są konieczne żadne bramy, urządzenia NAT, połączenia ExpressRoute lub sieci VPN ani publiczne adresy IP. 

Możesz również zabezpieczyć przestrzeń nazw platformy Azure Event Hubs przy użyciu zapór. Event Hubs obsługuje kontrolę dostępu opartą na protokole IP na potrzeby obsługi zapory przychodzącej. Reguły zapory można ustawiać przy użyciu Azure Portal, Azure Resource Manager szablonów lub interfejsu wiersza polecenia platformy Azure lub Azure PowerShell.

- [Jak korzystać z punktów końcowych usługi sieci wirtualnej w usłudze Azure Event Hubs](event-hubs-service-endpoints.md)

- [Integrowanie Event Hubs platformy Azure z prywatnym łączem platformy Azure](private-link-service.md)

- [Jak skonfigurować reguły zapory IP dla przestrzeni nazw platformy Azure Event Hubs](event-hubs-ip-filtering.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania**: [wzorzec zabezpieczeń platformy Azure](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) jest domyślną inicjatywy zasad dla Security Center i jest podstawą dla [zaleceń Security Center](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md). Definicje Azure Policy powiązane z tym formantem są włączane automatycznie przez Security Center. Alerty związane z tym formantem mogą wymagać planu [usługi Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) dla powiązanych usług.

**Azure Policy wbudowane definicje — Microsoft. EventHub**:

[!INCLUDE [Resource Policy for Microsoft.EventHub 1.1](../../includes/policy/standards/asb/rp-controls/microsoft.eventhub-1-1.md)]

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1,2: Monitoruj i Rejestruj konfigurację oraz ruch sieci wirtualnych, podsieci i interfejsów sieciowych

**Wskazówki**: Użyj Azure Security Center i postępuj zgodnie z zaleceniami dotyczącymi ochrony sieci, aby zabezpieczyć zasoby Event Hubs na platformie Azure. W przypadku korzystania z usługi Azure Virtual Machines w celu uzyskania dostępu do centrów zdarzeń należy włączyć dzienniki przepływu sieciowych grup zabezpieczeń i wysyłać dzienniki do konta magazynu na potrzeby inspekcji ruchu.

- [Jak włączyć dzienniki przepływu sieciowej grupy zabezpieczeń](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Informacje o zabezpieczeniach sieci zapewnianych przez Azure Security Center](../security-center/security-center-network-recommendations.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: odmowa komunikacji ze znanymi niezłośliwymi adresami IP

**Wskazówki**: Włączanie standardu DDoS Protection w sieciach wirtualnych skojarzonych z centrami zdarzeń, aby chronić przed rozproszonymi atakami typu "odmowa usługi" (DDoS). Użyj Azure Security Center zintegrowanej analizy zagrożeń, aby odmówić komunikacji ze znanymi złośliwymi lub nieużywanymi adresami IP.

- [Jak skonfigurować ochronę DDoS](../ddos-protection/manage-ddos-protection.md)

- [Aby uzyskać więcej informacji na temat Azure Security Center zintegrowanej analizy zagrożeń](/azure/security-center/security-center-alerts-service-layer)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="15-record-network-packets"></a>1,5: rejestrowanie pakietów sieciowych

**Wskazówki**: w przypadku korzystania z maszyn wirtualnych platformy Azure w celu uzyskania dostępu do centrów zdarzeń Włącz dzienniki przepływu sieciowych grup zabezpieczeń i Wyślij dzienniki do konta magazynu na potrzeby inspekcji ruchu. Dzienniki przepływu sieciowych grup zabezpieczeń można także wysyłać do obszaru roboczego Log Analytics i używać Analiza ruchu, aby uzyskać wgląd w przepływ ruchu w chmurze platformy Azure. Niektóre zalety Analiza ruchu to możliwość wizualizacji aktywności sieciowej i identyfikowania aktywnych punktów, identyfikowania zagrożeń bezpieczeństwa, zrozumienia wzorców przepływu ruchu i wyznaczania konfiguracji sieci.

Jeśli jest to wymagane do badania nietypowego działania, Włącz funkcję przechwytywania pakietów Network Watcher.

- [Jak włączyć dzienniki przepływu sieciowej grupy zabezpieczeń](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Jak włączyć i używać Analiza ruchu](../network-watcher/traffic-analytics.md)

- [Jak włączyć Network Watcher](../network-watcher/network-watcher-create.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: wdrażanie opartych na sieci systemów zapobiegania wykrywaniem i dostępem intruzów (identyfikatorów/adresów IP)

**Wskazówki**: Jeśli korzystasz z usługi Azure Virtual Machines w celu uzyskania dostępu do centrów zdarzeń, wybierz ofertę z portalu Azure Marketplace, która obsługuje funkcje identyfikatorów/adresów IP z możliwościami inspekcji ładunku. Jeśli w Twojej organizacji nie jest wymagana funkcja wykrywania i/lub zapobiegania włamaniom, można użyć wbudowanej zapory Event Hubs platformy Azure. Można ograniczyć dostęp do przestrzeni nazw Event Hubs dla ograniczonego zakresu adresów IP lub określonego adresu IP przy użyciu reguł zapory.

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

- [Jak dodać regułę zapory w Event Hubs dla określonego adresu IP](event-hubs-ip-filtering.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: Obsługa standardowych konfiguracji zabezpieczeń dla urządzeń sieciowych

**Wskazówki**: Definiowanie i implementowanie standardowych konfiguracji zabezpieczeń dla zasobów sieciowych skojarzonych z przestrzeniami nazw platformy Azure Event Hubs przy użyciu Azure Policy. Użyj aliasów Azure Policy w przestrzeniach nazw **Microsoft. EventHub** i **Microsoft. Network** , aby utworzyć niestandardowe definicje zasad w celu inspekcji lub wymuszania konfiguracji sieciowej Event Hubs przestrzeni nazw. Możesz również używać wbudowanych definicji zasad związanych z usługą Azure Event Hubs, takich jak:

- Centrum zdarzeń powinno korzystać z punktu końcowego usługi sieci wirtualnej.

Dodatkowe informacje są dostępne w linkach, do których istnieją odwołania.

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

- [Wbudowane zasady platformy Azure dla Event Hubs przestrzeni nazw](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#event-hub)

- [Przykłady Azure Policy dla sieci](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#network)

- [Jak utworzyć Azure Blueprint](../governance/blueprints/create-blueprint-portal.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="110-document-traffic-configuration-rules"></a>1,10: udokumentowanie reguł konfiguracji ruchu

**Wskazówki**: używanie tagów dla sieci wirtualnych i innych zasobów związanych z zabezpieczeniami sieci i przepływem ruchu, które są skojarzone z centrami zdarzeń.

- [Tworzenie i używanie tagów](/azure/azure-resource-manager/resource-group-using-tags)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: Użyj zautomatyzowanych narzędzi do monitorowania konfiguracji zasobów sieciowych i wykrywania zmian

**Wskazówki**: Użyj dziennika aktywności platformy Azure do monitorowania konfiguracji zasobów sieciowych i wykrywania zmian zasobów sieciowych związanych z usługą Azure Event Hubs. Tworzenie alertów w ramach Azure Monitor, które będą wyzwalane po wprowadzeniu zmian w krytycznych zasobach sieciowych.

- [Jak wyświetlać i pobierać zdarzenia dziennika aktywności platformy Azure](/azure/azure-monitor/platform/activity-log-view)

- [Jak utworzyć alerty w Azure Monitor](/azure/azure-monitor/platform/alerts-activity-log)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

## <a name="logging-and-monitoring"></a>Rejestrowanie i monitorowanie

*Aby uzyskać więcej informacji, zobacz temat [Azure Security test: rejestrowanie i monitorowanie](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2,2: Skonfiguruj centralne zarządzanie dziennikami zabezpieczeń

**Wskazówki**: w Azure monitor Skonfiguruj dzienniki dotyczące centrów zdarzeń w ustawieniach diagnostycznych dziennika aktywności i centrum zdarzeń, aby wysyłać dzienniki do log Analytics obszaru roboczego, w którym mają być wysyłane zapytania lub do konta magazynu w celu długoterminowego przechowywania danych archiwalnych.

- [Jak skonfigurować ustawienia diagnostyczne dla usługi Azure Event Hubs](event-hubs-diagnostic-logs.md)

- [Informacje o dzienniku aktywności platformy Azure](/azure/azure-monitor/platform/platform-logs-overview)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: Włączanie rejestrowania inspekcji dla zasobów platformy Azure

**Wskazówki**: Włączanie ustawień diagnostycznych dla przestrzeni nazw platformy Azure Event Hubs. Istnieją trzy kategorie ustawień diagnostycznych dla platformy Azure Event Hubs: dzienniki archiwów, dzienniki operacyjne i dzienniki skalowania automatycznego. Włącz dzienniki operacyjne, aby przechwytywać informacje o tym, co dzieje się podczas operacji Event Hubs, w szczególności o typie operacji, w tym o tworzeniu centrum zdarzeń, używanych zasobach i stanie operacji.

Ponadto możesz włączyć ustawienia diagnostyki dziennika aktywności platformy Azure i wysłać je do konta usługi Azure Storage, centrum zdarzeń lub obszaru roboczego Log Analytics. Dzienniki aktywności zapewniają wgląd w operacje wykonywane na Event Hubsach i innych zasobach platformy Azure. Korzystając z dzienników aktywności, można określić "co, kto i kiedy" dla operacji zapisu (PUT, POST, DELETE) podejmowanych w przestrzeniach nazw platformy Azure Event Hubs.

- [Jak włączyć ustawienia diagnostyczne dla Event Hubs platformy Azure](event-hubs-diagnostic-logs.md)

- [Jak włączyć ustawienia diagnostyczne dla dziennika aktywności platformy Azure](/azure/azure-monitor/platform/diagnostic-settings-legacy)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania**: [wzorzec zabezpieczeń platformy Azure](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) jest domyślną inicjatywy zasad dla Security Center i jest podstawą dla [zaleceń Security Center](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md). Definicje Azure Policy powiązane z tym formantem są włączane automatycznie przez Security Center. Alerty związane z tym formantem mogą wymagać planu [usługi Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) dla powiązanych usług.

**Azure Policy wbudowane definicje — Microsoft. EventHub**:

[!INCLUDE [Resource Policy for Microsoft.EventHub 2.3](../../includes/policy/standards/asb/rp-controls/microsoft.eventhub-2-3.md)]

### <a name="25-configure-security-log-storage-retention"></a>2,5: Konfigurowanie przechowywania magazynu dzienników zabezpieczeń

**Wskazówki**: w Azure monitor ustawić okres przechowywania log Analytics obszaru roboczego zgodnie z regulacjami zgodności organizacji, aby przechwytywać i przeglądać zdarzenia związane z centrum zdarzeń.

- [Jak ustawić parametry przechowywania dzienników dla obszarów roboczych Log Analytics](/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="26-monitor-and-review-logs"></a>2,6: dzienniki monitorowania i przeglądania

**Wskazówki**: analizowanie i monitorowanie dzienników pod kątem nietypowego zachowania oraz regularnego przeglądania wyników związanych z centrami zdarzeń. Użyj Log Analytics Azure Monitor, aby przejrzeć dzienniki i wykonywać zapytania dotyczące danych dziennika. Alternatywnie możesz włączyć i dołączyć dane do usługi Azure wskaźnikowej lub informacji o systemie oraz rozwiązania do zarządzania zdarzeniami innych firm.

- [Aby uzyskać więcej informacji na temat obszaru roboczego Log Analytics](/azure/azure-monitor/log-query/get-started-portal)

- [Jak wykonywać niestandardowe zapytania w Azure Monitor](/azure/azure-monitor/log-query/get-started-queries)

- [Jak dołączyć wskaźnik na platformie Azure](../sentinel/quickstart-onboard.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: Włączanie alertów dla nietypowych działań

**Wskazówki**: w Azure monitor Skonfiguruj dzienniki powiązane z usługą Azure Event Hubs w dzienniku aktywności oraz Event Hubs ustawienia diagnostyczne, aby wysyłać dzienniki do obszaru roboczego log Analytics, aby uzyskać zapytanie lub do konta magazynu w celu zapewnienia długoterminowego przechowywania danych. Użyj Log Analytics obszaru roboczego, aby utworzyć alerty dla nietypowej aktywności Znalezione w dziennikach i zdarzeniach zabezpieczeń.

Alternatywnie możesz włączyć i dołączyć dane do usługi Azure wskaźnikowej. 

- [Informacje o dzienniku aktywności platformy Azure](/azure/azure-monitor/platform/platform-logs-overview)

- [Jak skonfigurować ustawienia diagnostyczne dla usługi Azure Event Hubs](event-hubs-diagnostic-logs.md)

- [Jak otrzymywać alerty dotyczące Log Analytics danych dziennika obszaru roboczego](/azure/azure-monitor/learn/tutorial-response)

- [Jak dołączyć wskaźnik na platformie Azure](../sentinel/quickstart-onboard.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

## <a name="identity-and-access-control"></a>Tożsamość i kontrola dostępu

*Aby uzyskać więcej informacji, zobacz informacje o [teście zabezpieczeń Azure: Identity i Access Control](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: obsługa spisu kont administracyjnych

**Wskazówki**: Azure Active Directory (Azure AD) ma wbudowane role, które muszą być jawnie przypisane i są queryable. Za pomocą modułu Azure AD PowerShell można wykonywać zapytania ad hoc w celu odnajdywania kont należących do grup administracyjnych.

- [Jak uzyskać rolę katalogu w usłudze Azure AD przy użyciu programu PowerShell](/powershell/module/azuread/get-azureaddirectoryrole)

- [Jak uzyskać członków roli katalogu w usłudze Azure AD przy użyciu programu PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="32-change-default-passwords-where-applicable"></a>3,2: Zmień domyślne hasła, jeśli ma to zastosowanie

**Wskazówki**: płaszczyzna kontroli dostęp do Event Hubs jest kontrolowany za pomocą Azure Active Directory (Azure AD). Usługa Azure AD nie ma koncepcji domyślnych haseł.

Dostęp do Event Hubs płaszczyzny danych jest kontrolowany za pomocą usługi Azure AD z tożsamościami zarządzanymi lub Rejestracje aplikacji, a także sygnaturami dostępu współdzielonego. Sygnatury dostępu współdzielonego są używane przez klientów łączących się z centrami zdarzeń i mogą być ponownie generowane w dowolnym momencie.

- [Informacje o sygnaturach dostępu współdzielonego dla Event Hubs](authenticate-shared-access-signature.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: Użyj dedykowanych kont administracyjnych

**Wskazówki**: Tworzenie standardowych procedur operacyjnych dotyczących korzystania z dedykowanych kont administracyjnych. Użyj Azure Security Center Zarządzanie tożsamościami i dostępem, aby monitorować liczbę kont administracyjnych.

Ponadto, aby ułatwić śledzenie dedykowanych kont administracyjnych, można użyć zaleceń z Azure Security Center lub wbudowanych zasad platformy Azure, takich jak:

- Do subskrypcji powinien być przypisany więcej niż jeden właściciel

- Przestarzałe konta z uprawnieniami właściciela powinny zostać usunięte z subskrypcji

- Konta zewnętrzne z uprawnieniami właściciela powinny zostać usunięte z subskrypcji

Dodatkowe informacje są dostępne w linkach, do których istnieją odwołania.

- [Jak używać Azure Security Center do monitorowania tożsamości i dostępu (wersja zapoznawcza)](../security-center/security-center-identity-access.md)

- [Jak używać Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3,4: Użyj Azure Active Directory logowania jednokrotnego (SSO)

**Wskazówki**: Microsoft Azure zapewnia zintegrowane zarządzanie kontrolą dostępu dla zasobów i aplikacji na podstawie Azure Active Directory (Azure AD). Główną zaletą korzystania z usługi Azure AD z usługą Azure Event Hubs jest to, że nie musisz już przechowywać poświadczeń w kodzie. Zamiast tego można zażądać tokenu dostępu OAuth 2,0 z platformy tożsamości firmy Microsoft. Nazwa zasobu do żądania tokenu to https: \/ /eventhubs.Azure.NET/. Usługa Azure AD uwierzytelnia podmiot zabezpieczeń (użytkownika, grupę lub nazwę główną usługi), na którym działa aplikacja. W przypadku pomyślnego uwierzytelnienia usługa Azure AD zwraca token dostępu do aplikacji, a następnie może użyć tokenu dostępu do autoryzowania żądania do zasobów usługi Azure Event Hubs.

- [Jak uwierzytelnić aplikację przy użyciu usługi Azure AD w celu uzyskiwania dostępu do zasobów Event Hubs](authenticate-application.md)

- [Informacje logowania jednokrotnego w usłudze Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Użyj uwierzytelniania wieloskładnikowego dla wszystkich Azure Active Directory dostępu opartego na usłudze

**Wskazówki**: Włączanie uwierzytelniania wieloskładnikowego Azure Active Directory (Azure AD) i przestrzeganie Azure Security Center zalecenia dotyczące zarządzania tożsamościami i dostępem w celu ułatwienia ochrony zasobów z włączonym centrum zdarzeń.

- [Jak włączyć uwierzytelnianie wieloskładnikowe na platformie Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Jak monitorować tożsamość i dostęp w Azure Security Center](../security-center/security-center-identity-access.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: Używaj dedykowanych maszyn (uprzywilejowany dostęp do stacji roboczych) dla wszystkich zadań administracyjnych

**Wskazówki**: Użyj stacji roboczych dostępu uprzywilejowanego (dostępem uprzywilejowanym) z uwierzytelnianiem wieloskładnikowym skonfigurowanym do logowania się i konfigurowania zasobów z obsługą centrum zdarzeń.

- [Dowiedz się więcej o stacjach roboczych uprzywilejowanego dostępu](/security/compass/privileged-access-devices)

- [Jak włączyć uwierzytelnianie wieloskładnikowe na platformie Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: Rejestruj i Ostrzegaj o podejrzanych działaniach z kont administracyjnych

**Wskazówki**: użyj usługi Azure Active Directory (Azure AD) Privileged Identity Management do generowania dzienników i alertów w przypadku wystąpienia podejrzanych lub niebezpiecznych działań w środowisku. Użyj funkcji wykrywania ryzyka usługi Azure AD, aby wyświetlać alerty i raporty na temat ryzykownego zachowania użytkowników. Aby uzyskać dodatkowe rejestrowanie, Wyślij Azure Security Center alerty wykrywania ryzyka do Azure Monitor i skonfiguruj niestandardowe alerty/powiadomienia przy użyciu grup akcji.

- [Omówienie wykrywania ryzyka usługi Azure AD](/azure/active-directory/reports-monitoring/concept-risk-events)

- [Jak skonfigurować grupy akcji dla niestandardowych alertów i powiadomień](/azure/azure-monitor/platform/action-groups)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: zarządzanie zasobami platformy Azure tylko z zatwierdzonych lokalizacji

**Wskazówki**: Użyj dostępu warunkowego o nazwie Locations, aby zezwolić na dostęp tylko do określonych logicznych grup zakresów adresów IP lub krajów/regionów.

- [Jak skonfigurować nazwane lokalizacje na platformie Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="39-use-azure-active-directory"></a>3,9: Użyj Azure Active Directory

**Wskazówki**: Użyj Azure Active Directory (Azure AD) jako centralnego systemu uwierzytelniania i autoryzacji dla zasobów platformy Azure, takich jak Event Hubs. Umożliwia to kontrolę dostępu opartą na rolach (Azure RBAC) na potrzeby administracyjnych zasobów poufnych.

- [ Jak utworzyć i skonfigurować wystąpienie usługi Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

- [Autoryzuj dostęp do zasobów Event Hubs za pomocą usługi Azure AD](authorize-access-azure-active-directory.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: regularnie Przeglądaj i Uzgodnij dostęp użytkowników

**Wskazówki**: Azure Active Directory (Azure AD) zawiera dzienniki ułatwiające odnajdywanie starych kont. Ponadto za pomocą przeglądów dostępu do tożsamości platformy Azure można efektywnie zarządzać członkostwem w grupach, dostępem do aplikacji dla przedsiębiorstw i przypisaniami ról. Dostęp użytkowników może być regularnie przeglądany, aby upewnić się, że tylko Ci użytkownicy mają ciągły dostęp.

Dodatkowo należy regularnie obrócić sygnatury dostępu współdzielonego Event Hubs ".

- [Informacje o raportowaniu usługi Azure AD](/azure/active-directory/reports-monitoring/)

- [Jak korzystać z przeglądów dostępu do tożsamości platformy Azure](../active-directory/governance/access-reviews-overview.md)

- [Informacje o sygnaturach dostępu współdzielonego dla Event Hubs](authenticate-shared-access-signature.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: Monitor próbuje uzyskać dostęp do zdezaktywowanych poświadczeń

**Wskazówki**: masz dostęp do źródeł danych dotyczących logowania Azure Active Directory (Azure AD), inspekcji i zdarzeń związanych z ryzykiem, które umożliwiają integrację z dowolnymi rozwiązaniami do zarządzania informacjami i zdarzeniami systemu innych firm oraz narzędziem do monitorowania.

Proces ten można usprawnić, tworząc ustawienia diagnostyczne dla kont użytkowników usługi Azure AD i wysyłając dzienniki inspekcji i dzienniki logowania do obszaru roboczego Log Analytics. Żądane alerty dzienników można skonfigurować w ramach Log Analytics.

- [Jak zintegrować dzienniki aktywności platformy Azure z usługą Azure Monitor](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

- [Autoryzuj dostęp do zasobów Event Hubs za pomocą usługi Azure AD](authorize-access-azure-active-directory.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: odchylenia zachowania alertu dotyczącego logowania na koncie

**Wskazówki**: Używanie funkcji ochrony tożsamości i wykrywania ryzyka w programie Azure Active Directory (Azure AD) w celu skonfigurowania automatycznych odpowiedzi na wykryte podejrzane działania związane z zasobami z obsługą Event Hubs. Aby zaimplementować odpowiedzi na zabezpieczenia organizacji, należy włączyć automatyczne odpowiedzi za pomocą wskaźnikowego platformy Azure.

- [Jak wyświetlić ryzykowne logowania w usłudze Azure AD](/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

- [Jak skonfigurować i włączyć zasady dotyczące ryzyka związanego z ochroną tożsamości](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Jak dołączyć wskaźnik na platformie Azure](../sentinel/quickstart-onboard.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: Zapewnij firmie Microsoft dostęp do odpowiednich danych klienta w scenariuszach pomocy technicznej

**Wskazówki**: obecnie niedostępne; Skrytka klienta nie jest jeszcze obsługiwana dla Event Hubs.

- [Lista usług obsługiwanych przez Skrytka klienta](https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

## <a name="data-protection"></a>Ochrona danych

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: ochrona danych](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: przechowywanie spisu poufnych informacji

**Wskazówki**: Użyj tagów dotyczących zasobów związanych z Event Hubs, aby pomóc w śledzeniu zasobów platformy Azure, które przechowują lub przetwarzają informacje poufne.

- [Tworzenie i używanie tagów](/azure/azure-resource-manager/resource-group-using-tags)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: Izoluj systemy przechowujące lub przetwarzające informacje poufne

**Wskazówki**: Zaimplementuj osobne subskrypcje, opcjonalnie z grupami zarządzania na potrzeby tworzenia, testowania i produkcji. Przestrzeń nazw Event Hubs powinna być oddzielona przez sieć wirtualną z włączonymi punktami końcowymi usługi i odpowiednio oznakowane.

Możesz również zabezpieczyć przestrzeń nazw platformy Azure Event Hubs przy użyciu zapór. Usługa Azure Event Hubs obsługuje kontrolę dostępu opartą na protokole IP na potrzeby obsługi zapory przychodzącej. Reguły zapory można ustawiać przy użyciu Azure Portal, Azure Resource Manager szablonów lub interfejsu wiersza polecenia platformy Azure lub Azure PowerShell.

- [Jak utworzyć dodatkowe subskrypcje platformy Azure](/azure/billing/billing-create-subscription)

- [Jak utworzyć Grupy zarządzania](/azure/governance/management-groups/create)

- [Konfigurowanie reguł zapory adresów IP dla przestrzeni nazw platformy Azure Event Hubs](event-hubs-ip-filtering.md)

- [Jak tworzyć Tagi i korzystać z nich](/azure/azure-resource-manager/resource-group-using-tags)

- [Jak utworzyć Virtual Network](../virtual-network/quick-create-portal.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: Monitoruj i blokuj nieautoryzowany transfer informacji poufnych

**Wskazówki**: w przypadku korzystania z maszyn wirtualnych w celu uzyskania dostępu do centrów zdarzeń Użyj sieci wirtualnych, punktów końcowych usług, Event Hubs zapory, sieciowych grup zabezpieczeń i tagów usług, aby ograniczyć możliwość eksfiltracjinia danych.

Firma Microsoft zarządza podstawową infrastrukturą dla systemu Azure Event Hubs i ma zaimplementowane ścisłe kontrole, aby zapobiec utracie lub narażeniu danych klientów.

- [Konfigurowanie reguł zapory adresów IP dla przestrzeni nazw platformy Azure Event Hubs](event-hubs-ip-filtering.md)

- [Informacje o punktach końcowych usługi Virtual Network za pomocą usługi Azure Event Hubs](event-hubs-service-endpoints.md)

- [Integrowanie Event Hubs platformy Azure z prywatnym łączem platformy Azure](private-link-service.md)

- [Zrozumienie sieciowych grup zabezpieczeń i tagów usług](/azure/virtual-network/security-overview)

- [Informacje na temat ochrony danych klientów na platformie Azure](../security/fundamentals/protection-customer-data.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: Użyj aktywnego narzędzia do odnajdywania, aby identyfikować poufne dane

**Wskazówki**: funkcje identyfikacji, klasyfikacji i zapobiegania utracie danych nie są jeszcze dostępne dla Event Hubs platformy Azure. Zaimplementuj rozwiązanie innych firm, jeśli jest wymagane na potrzeby zgodności.

W przypadku podstawowej platformy zarządzanej przez firmę Microsoft Firma Microsoft traktuje całą zawartość klienta jako poufną i nadaje im dużą długość, aby chronić przed utratą i narażeniem danych przez klienta. Aby zapewnić bezpieczeństwo danych klienta na platformie Azure, firma Microsoft wdrożyła i utrzymuje pakiet niezawodnych kontroli i możliwości ochrony danych.

- [Informacje na temat ochrony danych klientów na platformie Azure](../security/fundamentals/protection-customer-data.md)

**Odpowiedzialność**: Współużytkowane

**Monitorowanie Azure Security Center**: brak

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4,6: stosowanie kontroli dostępu opartej na rolach w celu kontrolowania dostępu do zasobów

**Wskazówki**: usługa Azure Event Hubs obsługuje używanie Azure Active Directory (Azure AD) do autoryzacji żądań Event Hubs zasobów. Korzystając z usługi Azure AD, możesz użyć kontroli dostępu opartej na rolach (Azure RBAC), aby przyznać uprawnienia podmiotowi zabezpieczeń, który może być użytkownikiem lub podmiotem usługi aplikacji.

- [Informacje o usłudze Azure RBAC i dostępnych rolach dla usługi Azure Event Hubs](authorize-access-azure-active-directory.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: Szyfruj poufne informacje w spoczynku

**Wskazówki**: usługa Azure Event Hubs obsługuje szyfrowanie danych przechowywanych przy użyciu kluczy zarządzanych przez firmę Microsoft lub kluczy zarządzanych przez klienta. Ta funkcja umożliwia tworzenie, obracanie, wyłączanie i odwoływanie dostępu do kluczy zarządzanych przez klienta, które są używane do szyfrowania danych Event Hubs platformy Azure.

- [Jak skonfigurować klucze zarządzane przez klienta do szyfrowania Event Hubs platformy Azure](configure-customer-managed-key.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: Rejestruj i Ostrzegaj o zmianach krytycznych zasobów platformy Azure

**Wskazówki**: Użyj Azure monitor z dziennikiem aktywności platformy Azure, aby utworzyć alerty dla sytuacji, w których zmiany są wprowadzane do wystąpień produkcyjnych platformy Azure Event Hubs i innych krytycznych lub powiązanych zasobów.

- [Jak utworzyć alerty dla zdarzeń dziennika aktywności platformy Azure](/azure/azure-monitor/platform/alerts-activity-log)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

## <a name="inventory-and-asset-management"></a>Zarządzanie magazynem i zasobami

*Aby uzyskać więcej informacji, zobacz temat [Azure Security test: Inventory and Asset Management](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: Użyj rozwiązania automatycznego odnajdywania zasobów

**Wskazówki**: Użyj grafu zasobów platformy Azure do wykonywania zapytań i odnajdywania wszystkich zasobów (w tym przestrzeni nazw platformy Azure Event Hubs) w ramach subskrypcji. Upewnij się, że masz odpowiednie uprawnienia (odczyt) w dzierżawie i że można wyliczyć wszystkie subskrypcje platformy Azure oraz zasoby w ramach subskrypcji.

- [Jak tworzyć zapytania przy użyciu grafu zasobów platformy Azure](../governance/resource-graph/first-query-portal.md)

- [Jak wyświetlić subskrypcje platformy Azure](/powershell/module/az.accounts/get-azsubscription)

- [Opis kontroli RBAC platformy Azure](../role-based-access-control/overview.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="62-maintain-asset-metadata"></a>6,2: Konserwowanie metadanych zasobów

**Wskazówki**: stosowanie tagów do zasobów platformy Azure, dzięki czemu metadane są logicznie zorganizowane w taksonomię.

- [Tworzenie i używanie tagów](/azure/azure-resource-manager/resource-group-using-tags)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: Usuń nieautoryzowane zasoby platformy Azure

**Wskazówki**: używanie tagowania, grup zarządzania i oddzielnych subskrypcji, gdzie to konieczne, do organizowania i śledzenia przestrzeni nazw i powiązanych zasobów platformy Azure Event Hubs. Regularnie Uzgadniaj spis i zapewnij, że nieautoryzowane zasoby są usuwane z subskrypcji w odpowiednim czasie.

- [Jak utworzyć dodatkowe subskrypcje platformy Azure](/azure/billing/billing-create-subscription)

- [Jak utworzyć Grupy zarządzania](/azure/governance/management-groups/create)

- [Tworzenie i używanie tagów](/azure/azure-resource-manager/resource-group-using-tags)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: Monitoruj niezatwierdzone zasoby platformy Azure

**Wskazówki**: Użyj Azure Policy, aby umieścić ograniczenia dotyczące typu zasobów, które mogą być tworzone w subskrypcjach klientów, przy użyciu następujących wbudowanych definicji zasad:

- Niedozwolone typy zasobów

- Dozwolone typy zasobów

Ponadto za pomocą usługi Azure Resource Graph można wysyłać zapytania/odnajdywać zasoby w ramach subskrypcji.

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

- [Jak tworzyć zapytania za pomocą usługi Azure Graph](../governance/resource-graph/first-query-portal.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="69-use-only-approved-azure-services"></a>6,9: Używaj tylko zatwierdzonych usług platformy Azure

**Wskazówki**: Użyj Azure Policy, aby umieścić ograniczenia dotyczące typu zasobów, które mogą być tworzone w subskrypcjach klientów, przy użyciu następujących wbudowanych definicji zasad:

- Niedozwolone typy zasobów

- Dozwolone typy zasobów

Dodatkowe informacje są dostępne w linkach, do których istnieją odwołania.

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

- [Jak odmówić określonego typu zasobu za pomocą Azure Policy](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: Ogranicz możliwość korzystania przez użytkowników z Azure Resource Manager

**Wskazówki**: Skonfiguruj dostęp warunkowy platformy Azure, aby ograniczyć możliwość korzystania przez użytkowników z Azure Resource Manager przez skonfigurowanie "blokowania dostępu" dla aplikacji "Microsoft Azure Management".

- [Jak skonfigurować dostęp warunkowy w celu blokowania dostępu do Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

## <a name="secure-configuration"></a>Bezpieczna konfiguracja

*Aby uzyskać więcej informacji, zobacz temat [Azure Security test: bezpieczna konfiguracja](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: Ustanów bezpieczne konfiguracje dla wszystkich zasobów platformy Azure

**Wskazówki**: Definiowanie i implementowanie standardowych konfiguracji zabezpieczeń dla wdrożeń usługi Azure Event Hubs. Użyj aliasów Azure Policy w przestrzeni nazw **Microsoft. EventHub** , aby utworzyć zasady niestandardowe do inspekcji lub wymuszania konfiguracji. Możesz również używać wbudowanych definicji zasad dla platformy Azure Event Hubs takich jak:

- Dzienniki diagnostyczne w centrum zdarzeń powinny być włączone

- Centrum zdarzeń powinno używać punktu końcowego usługi sieci wirtualnej

Dodatkowe informacje są dostępne w linkach, do których istnieją odwołania.

- [Wbudowane zasady platformy Azure dla Event Hubs przestrzeni nazw](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#event-hub)

- [Jak wyświetlić dostępne aliasy Azure Policy](/powershell/module/az.resources/get-azpolicyalias)

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: obsługa bezpiecznych konfiguracji zasobów platformy Azure

**Wskazówki**: Użyj Azure Policy [Odmów] i [Wdróż, jeśli nie istnieje] efektów, aby wymusić bezpieczne ustawienia dla zasobów z włączoną obsługą Event Hubs. 

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

 
- [Aby uzyskać więcej informacji na temat efektów Azure Policy](../governance/policy/concepts/effects.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: Wdrażanie narzędzi do zarządzania konfiguracją dla zasobów platformy Azure

**Wskazówki**: Użyj aliasów Azure Policy w przestrzeni nazw "Microsoft. EventHub", aby utworzyć zasady niestandardowe na potrzeby alertów, inspekcji i wymuszania konfiguracji systemu. Dodatkowo opracowuj proces i potok na potrzeby zarządzania wyjątkami zasad.

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: Zaimplementuj automatyczne monitorowanie konfiguracji dla zasobów platformy Azure

**Wskazówki**: Użyj aliasów Azure Policy w przestrzeni nazw "Microsoft. EventHub", aby utworzyć zasady niestandardowe na potrzeby alertów, inspekcji i wymuszania konfiguracji systemu. Użyj Azure Policy [Audit], [Odmów] i [Wdróż, jeśli nie istnieje] powoduje automatyczne wymuszanie konfiguracji wdrożeń Event Hubs platformy Azure i powiązanych zasobów.

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="711-manage-azure-secrets-securely"></a>7,11: bezpieczne zarządzanie wpisami tajnymi platformy Azure

**Wskazówki**: w przypadku maszyn wirtualnych platformy Azure lub aplikacji sieci Web działających na Azure App Service używanym do uzyskiwania dostępu do centrów zdarzeń należy używać tożsamość usługi zarządzanej w połączeniu z Azure Key Vault, aby uprościć i zabezpieczyć zarządzanie sygnaturami dostępu współdzielonego dla wdrożeń Event Hubs platformy Azure. Upewnij się, że Key Vault jest skonfigurowany z włączonym niemiękkim usuwaniem.

- [Uwierzytelnianie tożsamości zarządzanej przy użyciu usługi Azure Active Directory (Azure AD) w celu uzyskania dostępu do zasobów Event Hubs](https://docs.microsoft.com/azure/event-hubs/authenticate-managed-identity?tabs=latest)

- [Konfigurowanie kluczy zarządzanych przez klienta dla Event Hubs](configure-customer-managed-key.md)

- [Jak przeprowadzić integrację z tożsamościami zarządzanymi przez platformę Azure](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

- [Jak utworzyć Key Vault](/azure/key-vault/quick-create-portal)

- [Jak zapewnić uwierzytelnianie Key Vault przy użyciu tożsamości zarządzanej](/azure/key-vault/managed-identity)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: bezpieczne i automatyczne zarządzanie tożsamościami

**Wskazówki**: w przypadku maszyn wirtualnych platformy Azure lub aplikacji sieci Web działających na Azure App Service używanym do uzyskiwania dostępu do centrów zdarzeń należy użyć tożsamość usługi zarządzanej w połączeniu z Azure Key Vault, aby uprościć i zabezpieczyć Event Hubs platformy Azure. Upewnij się, że Key Vault jest skonfigurowany z włączonym niemiękkim usuwaniem.

Użyj tożsamości zarządzanych, aby zapewnić usługom platformy Azure automatyczną tożsamość zarządzaną w usłudze Azure Active Directory (Azure AD). Tożsamości zarządzane umożliwiają uwierzytelnianie w dowolnej usłudze, która obsługuje uwierzytelnianie usługi Azure AD, w tym Azure Key Vault, bez żadnych poświadczeń w kodzie.

- [Uwierzytelnianie zarządzanej tożsamości za pomocą usługi Azure AD w celu uzyskiwania dostępu do zasobów Event Hubs](https://docs.microsoft.com/azure/event-hubs/authenticate-managed-identity?tabs=latest)

- [Konfigurowanie kluczy zarządzanych przez klienta dla Event Hubs](configure-customer-managed-key.md)

- [Jak skonfigurować tożsamości zarządzane](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

- [Jak przeprowadzić integrację z tożsamościami zarządzanymi przez platformę Azure](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: eliminowanie nieprzewidzianego narażenia na poświadczenia

**Wskazówki**: Implementuj skaner poświadczeń, aby identyfikować poświadczenia w kodzie. Skaner poświadczeń ułatwia również przenoszenie odnalezionych poświadczeń do bezpieczniejszych lokalizacji, takich jak usługa Azure Key Vault.

- [Jak skonfigurować skaner poświadczeń](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

## <a name="malware-defense"></a>Ochrona przed złośliwym oprogramowaniem

*Aby uzyskać więcej informacji, zobacz temat [Azure Security test: Obrona złośliwego oprogramowania](../security/benchmarks/security-control-malware-defense.md).*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: przeskanuj pliki przed przekazaniem do zasobów platformy Azure, które nie są obliczeniowe

**Wskazówki**: wstępnie Przeskanuj zawartość przekazywaną do zasobów platformy Azure, które nie są obliczeniowe, takich jak Azure Event Hubs, App Service, Data Lake Storage, Blob Storage, Azure Database for PostgreSQL itd. Firma Microsoft nie może uzyskać dostępu do danych w tych wystąpieniach.

Oprogramowanie chroniące przed złośliwym oprogramowaniem firmy Microsoft jest włączone na podstawowym hoście, który obsługuje usługi platformy Azure (na przykład usługa Azure cache for Redis), ale nie jest uruchamiane na treści klienta.

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

## <a name="data-recovery"></a>Odzyskiwanie danych

*Aby uzyskać więcej informacji, zobacz [test dotyczący zabezpieczeń platformy Azure: odzyskiwanie danych](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: zapewnianie regularnych zautomatyzowanych kopii zapasowych

**Wskazówki**: Konfigurowanie odzyskiwania geograficznego po awarii dla Event Hubs platformy Azure. W przypadku awarii całego regionu platformy Azure lub centrów danych (jeśli nie są używane strefy dostępności) nie ma możliwości zapewnienia przestoju w celu przeprowadzenia działania w innym regionie lub w centrum. W związku z tym odzyskiwanie geograficzne i replikacja geograficzna są ważnymi funkcjami dla każdego przedsiębiorstwa. Usługa Azure Event Hubs obsługuje zarówno odzyskiwanie geograficzne po awarii, jak i replikację geograficzną, na poziomie przestrzeni nazw. 

- [Informacje na temat odzyskiwania po awarii geograficznej dla usługi Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/event-hubs-geo-dr#availability-zones)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: wykonaj kompletne kopie zapasowe systemu i Utwórz kopię zapasową wszystkich kluczy zarządzanych przez klienta

**Wskazówki**: usługa Azure Event Hubs zapewnia szyfrowanie danych przechowywanych w usłudze Azure szyfrowanie usługi Storage (SSE platformy Azure). Event Hubs opiera się na usłudze Azure Storage do przechowywania danych i domyślnie wszystkie dane przechowywane w usłudze Azure Storage są szyfrowane przy użyciu kluczy zarządzanych przez firmę Microsoft. W przypadku używania Azure Key Vault do przechowywania kluczy zarządzanych przez klienta należy zapewnić regularne automatyczne tworzenie kopii zapasowych kluczy.

Zadbaj o regularne automatyczne tworzenie kopii zapasowych danych tajnych Key Vault za pomocą następującego polecenia programu PowerShell: Backup-AzKeyVaultSecret

- [Jak skonfigurować klucze zarządzane przez klienta na potrzeby szyfrowania danych usługi Azure Event Hubs magazynowanych](configure-customer-managed-key.md)

- [Jak utworzyć kopię zapasową Key Vault Secret](/powershell/module/azurerm.keyvault/backup-azurekeyvaultsecret)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: Weryfikuj wszystkie kopie zapasowe, w tym klucze zarządzane przez klienta

**Wskazówki**: testowanie przywracania kopii zapasowych kluczy zarządzanych przez klienta.

- [Jak przywrócić klucze magazynu kluczy na platformie Azure](/powershell/module/az.keyvault/restore-azkeyvaultkey)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: zapewnianie ochrony kopii zapasowych i kluczy zarządzanych przez klienta

**Wskazówki**: Włącz usuwanie nietrwałe w Key Vault, aby chronić klucze przed przypadkowym lub złośliwym usunięciem. Usługa Azure Event Hubs wymaga, aby klucze zarządzane przez klienta miały nietrwałe usuwanie i nie zostały skonfigurowane.

Skonfiguruj usuwanie nietrwałe dla konta usługi Azure Storage używanego do przechwytywania danych Event Hubs. Należy pamiętać, że ta funkcja nie jest jeszcze obsługiwana w przypadku Azure Data Lake Storage generacji 2.

- [Jak włączyć usuwanie nietrwałe w Key Vault](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal)

- [Konfigurowanie magazynu kluczy z kluczami](configure-customer-managed-key.md)

- [Soft delete for Azure Storage blobs](https://docs.microsoft.com//azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal) (Usuwanie nietrwałe dla obiektów blob usługi Azure Storage)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

## <a name="incident-response"></a>Reagowanie na zdarzenia

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: reagowanie na zdarzenia](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: Tworzenie przewodnika odpowiedzi na zdarzenia

**Wskazówki**: Upewnij się, że istnieją plany odpowiedzi na zdarzenia, które definiują role pracowników, a także fazy obsługi zdarzeń/zarządzania.

- [Jak skonfigurować automatyzację przepływu pracy w programie Azure Security Center](../security-center/security-center-planning-and-operations-guide.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: Tworzenie oceny incydentu i procedury priorytetyzacji

**Wskazówki**: Security Center przypisuje ważność do alertów, ułatwiając określanie priorytetów kolejności, w której uczestniczy każdy alert, dzięki czemu w przypadku naruszenia zabezpieczeń zasobów można od razu uzyskać dostęp. Ważność jest oparta na tym, jak dobrze Security Center znajduje się w wyszukiwaniu lub analizach używanych do wystawiania alertu, a także poziom pewności, że istniało złośliwe zamiar w odniesieniu do działania, które doprowadziło do alertu.

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="103-test-security-response-procedures"></a>10,3: procedury odpowiedzi na zabezpieczenia testowe

**Wskazówki**: przeprowadzanie ćwiczeń w celu przetestowania możliwości reagowania na zdarzenia systemu w regularnych erze. Zidentyfikuj słabe punkty i przerwy oraz popraw plan zgodnie z wymaganiami.

- [Zapoznaj się z publikacją NIST: Przewodnik dotyczący testowania, uczenia i ćwiczeń programów dla planów i możliwości IT](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: Podaj szczegóły kontaktu dotyczącego zabezpieczeń i Skonfiguruj powiadomienia dotyczące alertów dotyczących zdarzeń związanych z zabezpieczeniami

**Wskazówki**: informacje kontaktowe dotyczące zdarzenia zabezpieczeń będą używane przez firmę Microsoft do skontaktowania się z Tobą, jeśli firma Microsoft Security Response Center (MSRC) wykryje, że dostęp do danych klienta został uzyskany przez nielegalną lub nieautoryzowaną osobę.  Przejrzyj zdarzenia po fakcie, aby upewnić się, że problemy zostały rozwiązane. 

- [Jak ustawić kontakt z zabezpieczeniami Azure Security Center](../security-center/security-center-provide-security-contact-details.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: Uwzględnij alerty zabezpieczeń w systemie odpowiedzi na zdarzenia

**Wskazówki**: eksportowanie alertów i zaleceń dotyczących Azure Security Center przy użyciu funkcji eksportu ciągłego. Eksport ciągły umożliwia wyeksportowanie alertów i zaleceń ręcznie lub w stały sposób ciągły. Możesz użyć łącznika danych Azure Security Center do przesyłania strumieniowego alertów do usługi Azure wskaźnikowej.

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

**Wskazówki**: Postępuj zgodnie z regułami testowania penetracji Microsoft Cloud, aby upewnić się, że testy penetracji nie naruszają zasad firmy Microsoft. Korzystaj z strategii firmy Microsoft i wykonywania testów na żywo z obsługą tworzenia zespołu, usług i aplikacji w chmurze, które są zarządzane przez firmę Microsoft. 

- [Reguły testów penetracyjnych zaangażowania](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Testy typu „red team” w chmurze firmy Microsoft](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

## <a name="next-steps"></a>Następne kroki

- Zobacz [Omówienie testu porównawczego zabezpieczeń platformy Azure w wersji 2](/azure/security/benchmarks/overview)
- Dowiedz się więcej o [punktach odniesienia zabezpieczeń platformy Azure](/azure/security/benchmarks/security-baselines-overview)
