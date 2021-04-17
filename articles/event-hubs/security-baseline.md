---
title: Punkt odniesienia zabezpieczeń platformy Azure dla Event Hubs
description: Punkt Event Hubs zabezpieczeń zawiera wskazówki proceduralne i zasoby dotyczące wdrażania zaleceń dotyczących zabezpieczeń określonych w te testach porównawczych zabezpieczeń platformy Azure.
author: msmbaldwin
ms.service: event-hubs
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 33417a9bda9ad4ce36dd6e14f74a53911f3c3473
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2021
ms.locfileid: "107587157"
---
# <a name="azure-security-baseline-for-event-hubs"></a>Punkt odniesienia zabezpieczeń platformy Azure dla Event Hubs

Ten punkt odniesienia zabezpieczeń stosuje wskazówki z testu porównawczego zabezpieczeń platformy [Azure w wersji 1.0](../security/benchmarks/overview-v1.md) do Event Hubs. Test porównawczy zabezpieczeń platformy Azure zawiera zalecenia dotyczące sposobu zabezpieczania rozwiązań w chmurze na platformie Azure.
Zawartość jest pogrupowana według **kontrolek zabezpieczeń** zdefiniowanych przez test porównawczy zabezpieczeń platformy Azure i powiązanych wskazówek dotyczących Event Hubs. **Kontrolki,** które nie Event Hubs zostały wykluczone.

 
Aby zobaczyć, Event Hubs mapuje całkowicie na test porównawczy zabezpieczeń platformy Azure, zobacz pełny Event Hubs [mapowania punktów odniesienia zabezpieczeń](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Bezpieczeństwo sieci

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: bezpieczeństwo sieci](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1. Ochrona zasobów platformy Azure w sieciach wirtualnych

**Wskazówki:** Integracja usługi Azure Event Hubs z punktami końcowymi usługi dla sieci wirtualnej umożliwia bezpieczny dostęp do funkcji obsługi komunikatów z obciążeń, takich jak maszyny wirtualne powiązane z sieciami wirtualnymi, z zabezpieczoną ścieżką ruchu sieciowego po obu stronach.

Po powiązycie z co najmniej jednym punktem końcowym usługi podsieci sieci wirtualnej odpowiednie Event Hubs nazw nie będą już akceptować ruchu z dowolnego miejsca, ale z autoryzowanych podsieci w sieciach wirtualnych. Z perspektywy sieci wirtualnej powiązanie przestrzeni nazw Event Hubs z punktem końcowym usługi konfiguruje izolowany tunel sieciowy z podsieci sieci wirtualnej do usługi obsługi komunikatów. 

Możesz również utworzyć prywatny punkt końcowy, który jest interfejsem sieciowym, który łączy Cię prywatnie i bezpiecznie z usługą Event Hubs przy użyciu usługi Azure Private Link service. Prywatny punkt końcowy używa prywatnego adresu IP z sieci wirtualnej, efektywnie przenosząc usługę do sieci wirtualnej. Cały ruch do usługi może być przekierowyny przez prywatny punkt końcowy, dlatego nie są potrzebne żadne bramy, urządzenia NAT, połączenia usługi ExpressRoute lub sieci VPN ani publiczne adresy IP. 

Możesz również zabezpieczyć przestrzeń nazw Azure Event Hubs przy użyciu zapór. Event Hubs obsługuje kontrolę dostępu opartą na adresach IP dla obsługi zapory dla ruchu przychodzącego. Reguły zapory można ustawiać przy użyciu szablonów Azure Portal, szablonów Azure Resource Manager lub za pośrednictwem interfejsu wiersza polecenia platformy Azure lub Azure PowerShell.

- [Jak używać punktów końcowych usługi dla sieci wirtualnej z Azure Event Hubs](event-hubs-service-endpoints.md)

- [Integracja Azure Event Hubs z Azure Private Link](private-link-service.md)

- [Jak skonfigurować reguły zapory adresów IP dla Azure Event Hubs nazw](event-hubs-ip-filtering.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Test [porównawczy zabezpieczeń](/azure/governance/policy/samples/azure-security-benchmark) platformy Azure jest domyślną inicjatywą zasad dla usługi Security Center i stanowi podstawę Security Center [rekomendacji firmy](/azure/security-center/security-center-recommendations). Definicje Azure Policy związane z tą kontrolką są włączane automatycznie przez Security Center. Alerty związane z tą kontrolą mogą [wymagać Azure Defender](/azure/security-center/azure-defender) planu dla powiązanych usług.

**Azure Policy wbudowane definicje — Microsoft.EventHub:**

[!INCLUDE [Resource Policy for Microsoft.EventHub 1.1](../../includes/policy/standards/asb/rp-controls/microsoft.eventhub-1-1.md)]

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2: Monitorowanie i rejestrowanie konfiguracji i ruchu sieci wirtualnych, podsieci i interfejsów sieciowych

**Wskazówki:** używaj Azure Security Center i postępuj zgodnie z zaleceniami ochrony sieci, aby ułatwić Event Hubs zasobów na platformie Azure. Jeśli do uzyskiwania dostępu do centrów zdarzeń są używane maszyny wirtualne platformy Azure, włącz dzienniki przepływu sieciowej grupy zabezpieczeń i wyślij dzienniki na konto magazynu w celu inspekcji ruchu.

- [Jak włączyć dzienniki przepływu sieciowej organizacji zabezpieczeń](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Opis zabezpieczeń sieci zapewnianych przez Azure Security Center](../security-center/security-center-network-recommendations.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Odmowa komunikacji ze znanymi złośliwymi adresami IP

**Wskazówki:** włącz DDoS Protection Standardowa w sieciach wirtualnych skojarzonych z centrami zdarzeń, aby chronić przed rozproszonymi atakami typu "odmowa usługi" (DDoS). Użyj Azure Security Center zintegrowanej analizy zagrożeń, aby uniemożliwić komunikację ze znanymi złośliwymi lub nieużywanymi internetowymi adresami IP.

- [Jak skonfigurować ochronę przed DDoS](../ddos-protection/manage-ddos-protection.md)

- [Aby uzyskać więcej informacji na temat Azure Security Center zintegrowanej analizy zagrożeń](../security-center/azure-defender.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="15-record-network-packets"></a>1.5. Rejestrowanie pakietów sieciowych

**Wskazówki:** Jeśli do uzyskiwania dostępu do centrów zdarzeń są używane maszyny wirtualne platformy Azure, włącz dzienniki przepływu sieciowej grupy zabezpieczeń i wyślij dzienniki na konto magazynu w celu inspekcji ruchu. Możesz również wysyłać dzienniki przepływu sieciowych grup zabezpieczeń do obszaru roboczego usługi Log Analytics i używać usługi Analiza ruchu do zapewnienia wglądu w przepływ ruchu w chmurze platformy Azure. Niektóre zalety usługi Analiza ruchu to możliwość wizualizacji aktywności sieciowej i identyfikowania punktów aktywności, identyfikowania zagrożeń bezpieczeństwa, zrozumienia wzorców przepływu ruchu i identyfikowania błędnych konfiguracji sieci.

Jeśli jest to wymagane do badania anomalii działań, włącz Network Watcher przechwytywania pakietów.

- [How to Enable NSG Flow Logs](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Jak włączyć i używać Analiza ruchu](../network-watcher/traffic-analytics.md)

- [Jak włączyć Network Watcher](../network-watcher/network-watcher-create.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6. Wdrażanie sieciowych systemów wykrywania/zapobiegania włamań (IDS/IPS)

**Wskazówki:** W przypadku uzyskiwania dostępu do centrów zdarzeń przy użyciu maszyn wirtualnych platformy Azure wybierz ofertę z witryny Azure Marketplace, która obsługuje funkcje ids/IPS z możliwościami inspekcji ładunku. Jeśli w organizacji nie jest wymagane wykrywanie włamań i/lub zapobieganie im na podstawie inspekcji ładunku, możesz użyć Azure Event Hubs wbudowanej funkcji zapory. Możesz ograniczyć dostęp do Event Hubs nazw dla ograniczonego zakresu adresów IP lub określonego adresu IP przy użyciu reguł zapory.

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

- [Jak dodać regułę zapory w Event Hubs dla określonego adresu IP](event-hubs-ip-filtering.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Obsługa standardowych konfiguracji zabezpieczeń dla urządzeń sieciowych

**Wskazówki:** Definiowanie i implementowanie standardowych konfiguracji zabezpieczeń dla zasobów sieciowych skojarzonych Azure Event Hubs przestrzeniami nazw z Azure Policy. Użyj Azure Policy w przestrzeniach nazw **Microsoft.EventHub** i **Microsoft.Network,** aby utworzyć niestandardowe definicje zasad w celu inspekcji lub wymuszenia konfiguracji sieci Event Hubs nazw. Możesz również korzystać z wbudowanych definicji zasad powiązanych z Azure Event Hubs, takich jak:

- Centrum zdarzeń powinno używać punktu końcowego usługi dla sieci wirtualnej.

Dodatkowe informacje są dostępne pod linkami, do których się odwołujesz.

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

- [Wbudowane zasady platformy Azure dla Event Hubs nazw](../governance/policy/samples/built-in-policies.md#event-hub)

- [Azure Policy przykłady dotyczące sieci](../governance/policy/samples/built-in-policies.md#network)

- [Jak utworzyć usługę Azure Blueprint](../governance/blueprints/create-blueprint-portal.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="110-document-traffic-configuration-rules"></a>1.10: Dokumentuj reguły konfiguracji ruchu

**Wskazówki:** Użyj tagów dla sieci wirtualnych i innych zasobów związanych z zabezpieczeniami sieci i przepływem ruchu skojarzonym z twoimi centrami zdarzeń.

- [Jak tworzyć tagi i ich używać](../azure-resource-manager/management/tag-resources.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11. Monitorowanie konfiguracji zasobów sieciowych i wykrywanie zmian za pomocą zautomatyzowanych narzędzi

**Wskazówki:** Użyj dziennika aktywności platformy Azure do monitorowania konfiguracji zasobów sieciowych i wykrywania zmian zasobów sieciowych związanych z Azure Event Hubs. Utwórz alerty w Azure Monitor, które będą wyzwalane w przypadku zmiany krytycznych zasobów sieciowych.

- [Jak wyświetlać i pobierać zdarzenia dziennika aktywności platformy Azure](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [Jak tworzyć alerty w Azure Monitor](../azure-monitor/alerts/alerts-activity-log.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

## <a name="logging-and-monitoring"></a>Rejestrowanie i monitorowanie

*Aby uzyskać więcej informacji, zobacz [Test porównawczy zabezpieczeń platformy Azure: rejestrowanie i monitorowanie.](../security/benchmarks/security-control-logging-monitoring.md)*

### <a name="22-configure-central-security-log-management"></a>2.2. Konfigurowanie centralnego zarządzania dziennikami zabezpieczeń

**Wskazówki:** w ramach usługi Azure Monitor skonfiguruj dzienniki związane z centrami zdarzeń w ustawieniach diagnostycznych dziennika aktywności i centrum zdarzeń, aby wysyłać dzienniki do obszaru roboczego usługi Log Analytics w celu wysyłania zapytań lub do konta magazynu w celu przechowywania długoterminowego archiwizacji.

- [Jak skonfigurować ustawienia diagnostyczne dla Azure Event Hubs](event-hubs-diagnostic-logs.md)

- [Informacje o dzienniku aktywności platformy Azure](../azure-monitor/essentials/platform-logs-overview.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3. Włączanie rejestrowania inspekcji dla zasobów platformy Azure

**Wskazówki:** włączanie ustawień diagnostycznych dla Azure Event Hubs nazw. Istnieją trzy kategorie ustawień diagnostycznych dla poszczególnych Azure Event Hubs: Dzienniki archiwum, Dzienniki operacyjne i Dzienniki skalowania automatycznego. Włącz dzienniki operacyjne, aby przechwytywać informacje o tym, co dzieje się podczas operacji Event Hubs, w szczególności o typie operacji, w tym o tworzeniu centrum zdarzeń, używanych zasobach i stanie operacji.

Ponadto możesz włączyć ustawienia diagnostyczne dziennika aktywności platformy Azure i wysłać je do konta usługi Azure Storage, centrum zdarzeń lub obszaru roboczego usługi Log Analytics. Dzienniki aktywności zapewniają wgląd w operacje, które zostały wykonane na Azure Event Hubs i innych zasobach. Za pomocą dzienników aktywności można określić "co, kto i kiedy" dla wszystkich operacji zapisu (PUT, POST, DELETE) wykonywanych w przestrzeniach Azure Event Hubs nazw.

- [Jak włączyć ustawienia diagnostyczne dla Azure Event Hubs](event-hubs-diagnostic-logs.md)

- [Jak włączyć ustawienia diagnostyczne dla dziennika aktywności platformy Azure](../azure-monitor/essentials/activity-log.md)

**Odpowiedzialność**: Klient

**Azure Security Center:** Test porównawczy zabezpieczeń platformy [Azure](/azure/governance/policy/samples/azure-security-benchmark) to domyślna inicjatywa zasad dla usługi Security Center i stanowi podstawę rekomendacji Security Center [firmy .](/azure/security-center/security-center-recommendations) Definicje Azure Policy związane z tą kontrolką są włączane automatycznie przez Security Center. Alerty związane z tą kontrolą mogą [wymagać Azure Defender](/azure/security-center/azure-defender) planu dla powiązanych usług.

**Azure Policy wbudowane definicje — Microsoft.EventHub:**

[!INCLUDE [Resource Policy for Microsoft.EventHub 2.3](../../includes/policy/standards/asb/rp-controls/microsoft.eventhub-2-3.md)]

### <a name="25-configure-security-log-storage-retention"></a>2.5. Konfigurowanie przechowywania magazynu dzienników zabezpieczeń

**Wskazówki:** w Azure Monitor ustaw okres przechowywania obszaru roboczego usługi Log Analytics zgodnie z przepisami dotyczącymi zgodności w organizacji, aby przechwytywać i przeglądać zdarzenia związane z centrum zdarzeń.

- [Jak ustawić parametry przechowywania dzienników dla obszarów roboczych usługi Log Analytics](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="26-monitor-and-review-logs"></a>2.6. Monitorowanie i przeglądanie dzienników

**Wskazówki:** Analizowanie i monitorowanie dzienników pod celu zachowania anomalii oraz regularne przeglądanie wyników związanych z centrami zdarzeń. Użyj Azure Monitor Usługi Log Analytics, aby przeglądać dzienniki i wykonywać zapytania dotyczące danych dziennika. Alternatywnie można włączyć i dołączyć dane do Azure Sentinel lub rozwiązania do zarządzania informacjami i zdarzeniami systemu innych firm.

- [Aby uzyskać więcej informacji na temat obszaru roboczego usługi Log Analytics](../azure-monitor/logs/log-analytics-tutorial.md)

- [Jak wykonywać zapytania niestandardowe w Azure Monitor](../azure-monitor/logs/get-started-queries.md)

- [Jak do dołączać Azure Sentinel](../sentinel/quickstart-onboard.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7. Włączanie alertów dla niesłychanych działań

**Wskazówki:** w programie Azure Monitor skonfiguruj dzienniki związane z usługą Azure Event Hubs w dzienniku aktywności oraz ustawienia diagnostyczne usługi Event Hubs w celu wysyłania dzienników do obszaru roboczego usługi Log Analytics w celu wysyłania zapytań lub do konta magazynu w celu długoterminowego przechowywania archiwalnej. Obszar roboczy usługi Log Analytics umożliwia tworzenie alertów dotyczących anomalii działań znalezionych w dziennikach zabezpieczeń i zdarzeniach.

Alternatywnie możesz włączyć i włączyć dane, aby Azure Sentinel. 

- [Informacje o dzienniku aktywności platformy Azure](../azure-monitor/essentials/platform-logs-overview.md)

- [Jak skonfigurować ustawienia diagnostyczne dla Azure Event Hubs](event-hubs-diagnostic-logs.md)

- [Jak rejestrować alerty dotyczące danych dziennika obszaru roboczego usługi Log Analytics](../azure-monitor/alerts/tutorial-response.md)

- [Jak do dołączać Azure Sentinel](../sentinel/quickstart-onboard.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

## <a name="identity-and-access-control"></a>Tożsamość i kontrola dostępu

*Aby uzyskać więcej informacji, zobacz [Test porównawczy zabezpieczeń platformy Azure: tożsamość i Access Control.](../security/benchmarks/security-control-identity-access-control.md)*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Utrzymywanie spisu kont administracyjnych

**Wskazówki:** Azure Active Directory (Azure AD) ma wbudowane role, które muszą być jawnie przypisane i mogą być odpytywalne. Moduł Azure AD PowerShell umożliwia wykonywanie zapytań ad hoc w celu odnajdywania kont, które są członkami grup administracyjnych.

- [Jak uzyskać rolę katalogu w usłudze Azure AD przy użyciu programu PowerShell](/powershell/module/azuread/get-azureaddirectoryrole)

- [Jak uzyskać członków roli katalogu w usłudze Azure AD przy użyciu programu PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Zmień domyślne hasła tam, gdzie ma to zastosowanie

**Wskazówki:** Dostęp do płaszczyzny sterowania Event Hubs jest kontrolowany za pośrednictwem Azure Active Directory (Azure AD). Usługa Azure AD nie ma koncepcji haseł domyślnych.

Dostęp do płaszczyzny Event Hubs jest kontrolowany za pośrednictwem usługi Azure AD z tożsamościami zarządzanymi lub Rejestracje aplikacji a także sygnaturami dostępu współdzielonych. Sygnatury dostępu współdzielonych są używane przez klientów łączących się z centrami zdarzeń i mogą być ponownie wygenerowane w dowolnym momencie.

- [Opis sygnatur dostępu współdzielonych dla Event Hubs](authenticate-shared-access-signature.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Użyj dedykowanych kont administracyjnych

**Wskazówki:** Tworzenie standardowych procedur operacyjnych dotyczących korzystania z dedykowanych kont administracyjnych. Użyj Azure Security Center zarządzania tożsamościami i dostępem, aby monitorować liczbę kont administracyjnych.

Ponadto, aby ułatwić śledzenie dedykowanych kont administracyjnych, możesz skorzystać z zaleceń Azure Security Center lub wbudowanych zasad platformy Azure, takich jak:

- Do Subskrypcji powinien być przypisany więcej niż jeden właściciel

- Przestarzałe konta z uprawnieniami właściciela powinny zostać usunięte z subskrypcji

- Konta zewnętrzne z uprawnieniami właściciela powinny zostać usunięte z subskrypcji

Dodatkowe informacje są dostępne pod linkami, do których się odwołujesz.

- [Jak używać usługi Azure Security Center do monitorowania tożsamości i dostępu (wersja zapoznawcza)](../security-center/security-center-identity-access.md)

- [Jak używać Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4: Używanie Azure Active Directory jednokrotnego (SSO)

**Wskazówki:** Microsoft Azure zintegrowane zarządzanie kontrolą dostępu dla zasobów i aplikacji w oparciu o Azure Active Directory (Azure AD). Kluczową zaletą korzystania z usługi Azure AD Azure Event Hubs jest to, że nie trzeba już przechowywać poświadczeń w kodzie. Zamiast tego możesz zażądać tokenu dostępu OAuth 2.0 z platformy Microsoft Identity. Nazwa zasobu do żądania tokenu to https: \/ /eventhubs.azure.net/. Usługa Azure AD uwierzytelnia podmiot zabezpieczeń (użytkownika, grupę lub jednostkę usługi) uruchamiając aplikację. Jeśli uwierzytelnianie zakończy się pomyślnie, usługa Azure AD zwraca token dostępu do aplikacji, a aplikacja może następnie użyć tokenu dostępu do autoryzowania żądania do Azure Event Hubs zasobów.

- [Jak uwierzytelnić aplikację w usłudze Azure AD w celu uzyskania dostępu do Event Hubs zasobów](authenticate-application.md)

- [Opis logowania jednokrotnego w usłudze Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Używaj uwierzytelniania wieloskładnikowego dla Azure Active Directory dostępu opartego na danych

**Wskazówki:** włącz uwierzytelnianie wieloskładnikowe usługi Azure Active Directory (Azure AD) i postępuj zgodnie z Azure Security Center tożsamościami i zarządzaniem dostępem, aby chronić zasoby z obsługą centrum zdarzeń.

- [Jak włączyć uwierzytelnianie wieloskładnikowe na platformie Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Jak monitorować tożsamość i dostęp w Azure Security Center](../security-center/security-center-identity-access.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6. Użyj dedykowanych maszyn (stacji roboczych z dostępem uprzywilejowanym) do wykonywania wszystkich zadań administracyjnych

**Wskazówki:** Używanie stacji roboczych z dostępem uprzywilejowanym (PAW) z uwierzytelnianiem wieloskładnikowym skonfigurowanym do logowania się i konfigurowania zasobów z obsługą centrum zdarzeń.

- [Dowiedz się więcej o stacjach roboczych z dostępem uprzywilejowanym](/security/compass/privileged-access-devices)

- [Jak włączyć uwierzytelnianie wieloskładnikowe na platformie Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: Rejestrowanie i alerty dotyczące podejrzanych działań z kont administracyjnych

**Wskazówki:** użyj Azure Active Directory (Azure AD) Privileged Identity Management generowania dzienników i alertów w przypadku wystąpienia podejrzanej lub niebezpiecznej aktywności w środowisku. Wykrywanie ryzyka w usłudze Azure AD umożliwia wyświetlanie alertów i raportów dotyczących ryzykownych zachowań użytkowników. Aby uzyskać dodatkowe rejestrowanie, wyślij Azure Security Center alerty dotyczące wykrywania ryzyka do usługi Azure Monitor i skonfiguruj niestandardowe alerty/powiadomienia przy użyciu grup akcji.

- [Opis wykrywania ryzyka w usłudze Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

- [Jak skonfigurować grupy akcji dla niestandardowych alertów i powiadomień](../azure-monitor/alerts/action-groups.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Zarządzanie zasobami platformy Azure tylko z zatwierdzonych lokalizacji

**Wskazówki:** Użyj dostępu warunkowego nazwanych lokalizacji, aby zezwolić na dostęp tylko z określonych grup logicznych zakresów adresów IP lub krajów/regionów.

- [Jak skonfigurować nazwane lokalizacje na platformie Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="39-use-azure-active-directory"></a>3.9. Użyj Azure Active Directory

**Wskazówki:** Użyj usługi Azure Active Directory (Azure AD) jako centralnego systemu uwierzytelniania i autoryzacji dla zasobów platformy Azure, takich jak Event Hubs. Umożliwia to kontrolę dostępu na podstawie ról (RBAC) platformy Azure do poufnych zasobów administracyjnych.

- [ Jak utworzyć i skonfigurować wystąpienie usługi Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

- [Autoryzowanie dostępu do Event Hubs zasobów przy użyciu usługi Azure AD](authorize-access-azure-active-directory.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Regularne przeglądanie i uzgadnianie dostępu użytkowników

**Wskazówki:** Azure Active Directory (Azure AD) zawiera dzienniki, które ułatwiają odnajdywanie nieaktywnego konta. Ponadto używaj przeglądów dostępu do tożsamości platformy Azure, aby efektywnie zarządzać członkostwem w grupach, dostępem do aplikacji dla przedsiębiorstw i przypisań ról. Dostęp użytkowników można regularnie przeglądać, aby upewnić się, że tylko odpowiedni użytkownicy mają dalszy dostęp.

Ponadto regularnie obracaj sygnatury Event Hubs dostępu współdzielonych.

- [Informacje o raportowaniu w usłudze Azure AD](../active-directory/reports-monitoring/index.yml)

- [Jak używać przeglądów dostępu tożsamości platformy Azure](../active-directory/governance/access-reviews-overview.md)

- [Opis sygnatur dostępu współdzielonych dla Event Hubs](authenticate-shared-access-signature.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Monitorowanie prób uzyskania dostępu do dezaktywowanych poświadczeń

**Wskazówki:** masz dostęp do źródeł dzienników Azure Active Directory (Azure AD), inspekcji i ryzyka, które umożliwiają integrację z dowolnymi rozwiązaniami do zarządzania informacjami i zdarzeniami systemu innych firm lub narzędziami do monitorowania.

Ten proces można usprawnić, tworząc ustawienia diagnostyczne dla kont użytkowników usługi Azure AD oraz wysyłając dzienniki inspekcji i dzienniki logowania do obszaru roboczego usługi Log Analytics. Żądane alerty dzienników można skonfigurować w ramach usługi Log Analytics.

- [Jak zintegrować dzienniki aktywności platformy Azure z usługą Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Autoryzowanie dostępu do Event Hubs zasobów przy użyciu usługi Azure AD](authorize-access-azure-active-directory.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12: Odchylenie zachowania alertu podczas logowania na koncie

**Wskazówki:** Użyj usługi Identity Protection i funkcji wykrywania ryzyka w usłudze Azure Active Directory (Azure AD), aby skonfigurować automatyczne odpowiedzi na wykryte podejrzane akcje związane z Event Hubs zasobów z włączoną obsługą usługi. Należy włączyć automatyczne odpowiedzi za pośrednictwem Azure Sentinel, aby zaimplementować odpowiedzi zabezpieczeń organizacji.

- [Jak wyświetlić ryzykowne logowania w usłudze Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

- [Jak skonfigurować i włączyć zasady ryzyka związane z usługą Identity Protection](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Jak do dołączać Azure Sentinel](../sentinel/quickstart-onboard.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Zapewnienie firmie Microsoft dostępu do odpowiednich danych klientów podczas scenariuszy pomocy technicznej

**Wskazówki:** obecnie niedostępne; Skrytka klienta nie jest jeszcze obsługiwany w Event Hubs.

- [Lista Skrytka klienta obsługiwanych przez program](../security/fundamentals/customer-lockbox-overview.md#supported-services-and-scenarios-in-general-availability)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

## <a name="data-protection"></a>Ochrona danych

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: ochrona danych](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1. Utrzymywanie spisu informacji poufnych

**Wskazówki:** Używanie tagów w zasobach związanych z Event Hubs, aby pomóc w śledzeniu zasobów platformy Azure, które przechowują lub przetwarzają poufne informacje.

- [Jak tworzyć tagi i ich używać](../azure-resource-manager/management/tag-resources.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Odizoluj systemy przechowujące lub przetwarzające informacje poufne

**Wskazówki:** Zaim implementuj oddzielne subskrypcje, opcjonalnie z grupami zarządzania dla procesów testowania, testowania i produkcji. Event Hubs przestrzenie nazw powinny być oddzielone siecią wirtualną z włączonymi punktami końcowymi usługi i odpowiednio oznakowane.

Możesz również zabezpieczyć przestrzeń nazw Azure Event Hubs przy użyciu zapór. Azure Event Hubs obsługuje kontrolę dostępu opartą na adresach IP dla obsługi zapory dla ruchu przychodzącego. Reguły zapory można ustawić przy użyciu szablonów Azure Portal, Azure Resource Manager albo za pomocą interfejsu wiersza polecenia platformy Azure lub Azure PowerShell.

- [Jak utworzyć dodatkowe subskrypcje platformy Azure](../cost-management-billing/manage/create-subscription.md)

- [Jak utworzyć Grupy zarządzania](../governance/management-groups/create-management-group-portal.md)

- [Konfigurowanie reguł zapory adresów IP dla Azure Event Hubs nazw](event-hubs-ip-filtering.md)

- [Jak tworzyć i wykorzystywać tagi](../azure-resource-manager/management/tag-resources.md)

- [Jak utworzyć Virtual Network](../virtual-network/quick-create-portal.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Monitorowanie i blokowanie nieautoryzowanego transferu poufnych informacji

**Wskazówki:** W przypadku uzyskiwania dostępu do centrów zdarzeń przy użyciu maszyn wirtualnych należy korzystać z sieci wirtualnych, punktów końcowych usługi, zapory usługi Event Hubs, sieciowych grup zabezpieczeń i tagów usługi, aby ograniczyć ryzyko eksfiltracji danych.

Firma Microsoft zarządza podstawową infrastrukturą dla klientów Azure Event Hubs wdrożyła ścisłe mechanizmy kontroli, aby zapobiec utracie lub narażeniu danych klientów.

- [Konfigurowanie reguł zapory adresów IP dla Azure Event Hubs nazw](event-hubs-ip-filtering.md)

- [Understand Virtual Network Service Endpoints with Azure Event Hubs](event-hubs-service-endpoints.md)

- [Integracja Azure Event Hubs z Azure Private Link](private-link-service.md)

- [Opis sieciowych grup zabezpieczeń i tagów usługi](../virtual-network/network-security-groups-overview.md)

- [Informacje na temat ochrony danych klientów na platformie Azure](../security/fundamentals/protection-customer-data.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5. Identyfikowanie poufnych danych za pomocą aktywnego narzędzia odnajdywania

**Wskazówki:** Funkcje identyfikacji, klasyfikacji i zapobiegania utracie danych nie są jeszcze dostępne dla Azure Event Hubs. Implementowanie rozwiązania innej firmy, jeśli jest to wymagane do celów zgodności.

W przypadku podstawowej platformy zarządzanej przez firmę Microsoft firma Microsoft traktuje całą zawartość klientów jako poufne i bardzo długo chroni przed utratą i utratą danych klientów. Aby zapewnić bezpieczeństwo danych klientów na platformie Azure, firma Microsoft wdrożyła i utrzymuje pakiet niezawodnych funkcji i kontroli ochrony danych.

- [Informacje na temat ochrony danych klientów na platformie Azure](../security/fundamentals/protection-customer-data.md)

**Odpowiedzialność**: Współużytkowane

**Azure Security Center monitorowania:** Brak

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4.6: Kontrola dostępu do zasobów przy użyciu kontroli dostępu opartej na rolach

**Wskazówki:** usługa Azure Event Hubs obsługuje Azure Active Directory (Azure AD) w celu autoryzowania żądań do Event Hubs zasobów. Za pomocą usługi Azure AD można użyć kontroli dostępu opartej na rolach (RBAC) platformy Azure, aby udzielić uprawnień do podmiotu zabezpieczeń, który może być użytkownikiem lub jednostką usługi aplikacji.

- [Informacje o kontroli RBAC platformy Azure i dostępnych rolach dla Azure Event Hubs](authorize-access-azure-active-directory.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8. Szyfrowanie poufnych informacji w spoczynku

**Wskazówki:** Azure Event Hubs obsługuje opcję szyfrowania danych w spoczynku przy użyciu kluczy zarządzanych przez firmę Microsoft lub kluczy zarządzanych przez klienta. Ta funkcja umożliwia tworzenie, obracanie, wyłączanie i odwoływanie dostępu do kluczy zarządzanych przez klienta, które są używane do szyfrowania Azure Event Hubs danych w spoczynku.

- [Jak skonfigurować klucze zarządzane przez klienta na potrzeby szyfrowania Azure Event Hubs](configure-customer-managed-key.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9. Rejestrowanie i alerty dotyczące zmian w krytycznych zasobach platformy Azure

**Wskazówki:** użyj Azure Monitor z dziennika aktywności platformy Azure, aby tworzyć alerty dotyczące sytuacji, w których zmiany zostaną wprowadzone w wystąpieniach produkcyjnych Azure Event Hubs i innych krytycznych lub powiązanych zasobów.

- [Jak tworzyć alerty dla zdarzeń dziennika aktywności platformy Azure](../azure-monitor/alerts/alerts-activity-log.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

## <a name="inventory-and-asset-management"></a>Zarządzanie magazynem i zasobami

*Aby uzyskać więcej informacji, zobacz [Test porównawczy zabezpieczeń platformy Azure: spis i zarządzanie zasobami.](../security/benchmarks/security-control-inventory-asset-management.md)*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1. Korzystanie z rozwiązania do automatycznego odnajdywania zasobów

**Wskazówki:** Użyj Azure Resource Graph do wykonywania zapytań i odnajdywania wszystkich zasobów (w Azure Event Hubs przestrzeni nazw) w ramach subskrypcji. Upewnij się, że masz odpowiednie uprawnienia do odczytu w dzierżawie i możesz wyliczyć wszystkie subskrypcje platformy Azure, a także zasoby w ramach twoich subskrypcji.

- [Jak tworzyć zapytania za pomocą Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

- [Jak wyświetlić subskrypcje platformy Azure](/powershell/module/az.accounts/get-azsubscription)

- [Opis kontroli RBAC na platformie Azure](../role-based-access-control/overview.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="62-maintain-asset-metadata"></a>6.2. Obsługa metadanych zasobów

**Wskazówki:** Stosowanie tagów do zasobów platformy Azure, które dają metadane w celu logicznego zorganizowania ich w taksonomię.

- [Jak tworzyć tagi i ich używać](../azure-resource-manager/management/tag-resources.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Usuwanie nieautoryzowanych zasobów platformy Azure

**Wskazówki:** używaj tagowania, grup zarządzania i oddzielnych subskrypcji, jeśli to konieczne, do organizowania i śledzenia Azure Event Hubs przestrzeni nazw i powiązanych zasobów. Regularnie uzgadniaj spis i upewnij się, że nieautoryzowane zasoby są usuwane z subskrypcji w terminowy sposób.

- [Jak utworzyć dodatkowe subskrypcje platformy Azure](../cost-management-billing/manage/create-subscription.md)

- [Jak utworzyć Grupy zarządzania](../governance/management-groups/create-management-group-portal.md)

- [Jak tworzyć tagi i ich używać](../azure-resource-manager/management/tag-resources.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Monitorowanie niezatwierdzonych zasobów platformy Azure

**Wskazówki:** użyj Azure Policy, aby wprowadzić ograniczenia dotyczące typu zasobów, które mogą być tworzone w subskrypcjach klientów przy użyciu następujących wbudowanych definicji zasad:

- Niedozwolone typy zasobów

- Dozwolone typy zasobów

Ponadto użyj Azure Resource Graph do wykonywania zapytań/odnajdywania zasobów w ramach subskrypcji.

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

- [Jak tworzyć zapytania za pomocą usługi Azure Graph](../governance/resource-graph/first-query-portal.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="69-use-only-approved-azure-services"></a>6.9: Używaj tylko zatwierdzonych usług platformy Azure

**Wskazówki:** użyj Azure Policy, aby wprowadzić ograniczenia dotyczące typu zasobów, które mogą być tworzone w subskrypcjach klientów przy użyciu następujących wbudowanych definicji zasad:

- Niedozwolone typy zasobów

- Dozwolone typy zasobów

Dodatkowe informacje są dostępne pod linkami, do których się odwołujesz.

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

- [Jak odmówić określonego typu zasobu za pomocą Azure Policy](../governance/policy/samples/built-in-policies.md#general)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Ograniczanie możliwości interakcji użytkowników z Azure Resource Manager

**Wskazówki:** Skonfiguruj dostęp warunkowy platformy Azure, aby ograniczyć użytkownikom możliwość interakcji z usługą Azure Resource Manager, konfigurując opcję "Blokuj dostęp" dla aplikacji "Microsoft Azure Management".

- [Jak skonfigurować dostęp warunkowy, aby zablokować dostęp do Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

## <a name="secure-configuration"></a>Bezpieczna konfiguracja

*Aby uzyskać więcej informacji, zobacz [Test porównawczy zabezpieczeń platformy Azure: bezpieczna konfiguracja.](../security/benchmarks/security-control-secure-configuration.md)*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1. Ustanów bezpieczne konfiguracje dla wszystkich zasobów platformy Azure

**Wskazówki:** Definiowanie i implementowanie standardowych konfiguracji zabezpieczeń Azure Event Hubs wdrożeniach. Użyj Azure Policy w przestrzeni nazw **Microsoft.EventHub,** aby utworzyć niestandardowe zasady inspekcji lub wymuszania konfiguracji. Możesz również korzystać z wbudowanych definicji zasad dla różnych Azure Event Hubs takich jak:

- Dzienniki diagnostyczne w centrum zdarzeń powinny być włączone

- Centrum zdarzeń powinno używać punktu końcowego usługi dla sieci wirtualnej

Dodatkowe informacje są dostępne pod linkami, do których się odwołują.

- [Wbudowane zasady platformy Azure dla Event Hubs nazw](../governance/policy/samples/built-in-policies.md#event-hub)

- [Jak wyświetlić dostępne Azure Policy aliasy](/powershell/module/az.resources/get-azpolicyalias)

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Obsługa bezpiecznych konfiguracji zasobów platformy Azure

**Wskazówki:** użyj Azure Policy [odmów] i [wdoń, jeśli nie istnieje], aby wymusić bezpieczne ustawienia w zasobach z włączoną obsługą Event Hubs sieci. 

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

 
- [Aby uzyskać więcej informacji na temat efektów Azure Policy danych](../governance/policy/concepts/effects.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7. Wdrażanie narzędzi do zarządzania konfiguracją dla zasobów platformy Azure

**Wskazówki:** użyj Azure Policy aliasów w przestrzeni nazw "Microsoft.EventHub", aby utworzyć zasady niestandardowe służące do alertów, inspekcji i wymuszania konfiguracji systemu. Ponadto opracuj proces i potok do zarządzania wyjątkami zasad.

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Implementowanie zautomatyzowanego monitorowania konfiguracji dla zasobów platformy Azure

**Wskazówki:** użyj Azure Policy aliasów w przestrzeni nazw "Microsoft.EventHub", aby utworzyć zasady niestandardowe służące do alertów, inspekcji i wymuszania konfiguracji systemu. Użyj Azure Policy [audit], [deny] i [deploy if not exist], aby automatycznie wymuszać konfiguracje dla Azure Event Hubs i powiązanych zasobów.

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="711-manage-azure-secrets-securely"></a>7.11: Bezpieczne zarządzanie wpisami tajnymi platformy Azure

**Wskazówki:** W przypadku maszyn wirtualnych platformy Azure lub aplikacji internetowych działających w usłudze Azure App Service używanych do uzyskiwania dostępu do centrów zdarzeń użyj tożsamości usługi zarządzanej w połączeniu z usługą Azure Key Vault, aby uprościć i zabezpieczyć zarządzanie sygnaturami dostępu współdzieleniowego dla wdrożeń Azure Event Hubs zdarzeń. Upewnij Key Vault, że skonfigurowano usuwanie nie soft-delete.

- [Uwierzytelnianie tożsamości zarządzanej za pomocą usługi Azure Active Directory (Azure AD) w celu uzyskania dostępu Event Hubs zasobów](./authenticate-managed-identity.md?tabs=latest)

- [Konfigurowanie kluczy zarządzanych przez klienta dla Event Hubs](configure-customer-managed-key.md)

- [Jak zintegrować z tożsamościami zarządzanymi platformy Azure](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

- [Jak utworzyć Key Vault](../key-vault/secrets/quick-create-portal.md)

- [Jak zapewnić uwierzytelnianie Key Vault tożsamości zarządzanej](../key-vault/general/assign-access-policy-portal.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Bezpieczne i automatyczne zarządzanie tożsamościami

**Wskazówki:** W przypadku maszyn wirtualnych platformy Azure lub aplikacji internetowych działających w usłudze Azure App Service używanych do uzyskiwania dostępu do centrów zdarzeń użyj tożsamości usługi zarządzanej w połączeniu z usługą Azure Key Vault, aby uprościć i zabezpieczyć Azure Event Hubs. Upewnij Key Vault, że skonfigurowano usuwanie nie soft-delete.

Użyj tożsamości zarządzanych, aby zapewnić usługom platformy Azure automatycznie zarządzaną tożsamość w usłudze Azure Active Directory (Azure AD). Tożsamości zarządzane umożliwiają uwierzytelnianie w dowolnej usłudze obsługującej uwierzytelnianie usługi Azure AD, w tym w Azure Key Vault, bez żadnych poświadczeń w kodzie.

- [Uwierzytelnianie tożsamości zarządzanej w usłudze Azure AD w celu uzyskania dostępu do Event Hubs zasobów](./authenticate-managed-identity.md?tabs=latest)

- [Konfigurowanie kluczy zarządzanych przez klienta dla Event Hubs](configure-customer-managed-key.md)

- [Jak skonfigurować tożsamości zarządzane](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

- [Jak zintegrować usługę z tożsamościami zarządzanymi platformy Azure](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Wyeliminuj niezamierzone narażenie poświadczeń

**Wskazówki:** implementowanie skanera poświadczeń w celu identyfikowania poświadczeń w kodzie. Skaner poświadczeń ułatwia również przenoszenie odnalezionych poświadczeń do bezpieczniejszych lokalizacji, takich jak usługa Azure Key Vault.

- [Jak skonfigurować skaner poświadczeń](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

## <a name="malware-defense"></a>Ochrona przed złośliwym oprogramowaniem

*Aby uzyskać więcej informacji, zobacz [Test porównawczy zabezpieczeń platformy Azure: Malware Defense.](../security/benchmarks/security-control-malware-defense.md)*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Pliki przed skanowaniem, które mają zostać przekazane do zasobów platformy Azure bez obliczeń

**Wskazówki:** wstępne skanowanie zawartości przekazywanej do zasobów platformy Azure, które nie są obliczeniowe, takich jak Azure Event Hubs, App Service, Data Lake Storage, Blob Storage, Azure Database for PostgreSQL itp. Firma Microsoft nie może uzyskać dostępu do twoich danych w tych wystąpieniach.

Ochrona przed złośliwym oprogramowaniem firmy Microsoft jest włączona na podstawowym hoście, który obsługuje usługi platformy Azure (na przykład Azure Cache for Redis), ale nie działa w przypadku zawartości klienta.

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

## <a name="data-recovery"></a>Odzyskiwanie danych

*Aby uzyskać więcej informacji, zobacz [Test porównawczy zabezpieczeń platformy Azure: odzyskiwanie danych.](../security/benchmarks/security-control-data-recovery.md)*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Zapewnianie regularnych automatycznych kopii zapasowej

**Wskazówki:** Konfigurowanie geograficznego odzyskiwania po awarii dla Azure Event Hubs. Jeśli w całych regionach lub centrach danych platformy Azure (jeśli nie są używane strefy dostępności) wystąpi przestój, kluczowe znaczenie ma dalsze działanie przetwarzania danych w innym regionie lub centrum danych. W związku z tym geograficzne odzyskiwanie po awarii i replikacja geograficzna są ważnymi funkcjami dla każdego przedsiębiorstwa. Azure Event Hubs obsługuje zarówno geograficzne odzyskiwanie po awarii, jak i replikację geograficzną na poziomie przestrzeni nazw. 

- [Opis geograficznego odzyskiwania po awarii dla Azure Event Hubs](./event-hubs-geo-dr.md#availability-zones)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2. Wykonywanie pełnych kopii zapasowych systemu i tworzenie kopii zapasowych dowolnych kluczy zarządzanych przez klienta

**Wskazówki:** Azure Event Hubs szyfrowanie danych magazynowych za pomocą usługi Azure szyfrowanie usługi Storage (Azure SSE). Event Hubs usługa Azure Storage przechowuje dane, a domyślnie wszystkie dane przechowywane w usłudze Azure Storage są szyfrowane przy użyciu kluczy zarządzanych przez firmę Microsoft. Jeśli używasz Azure Key Vault do przechowywania kluczy zarządzanych przez klienta, upewnij się, że regularne automatyczne kopie zapasowe kluczy.

Zapewnij regularne automatyczne kopie zapasowe wpisów tajnych Key Vault pomocą następującego polecenia programu PowerShell: Backup-AzKeyVaultSecret

- [Jak skonfigurować klucze zarządzane przez klienta na potrzeby szyfrowania danych Azure Event Hubs danych w spoczynku](configure-customer-managed-key.md)

- [Jak utworzyć kopię zapasową Key Vault tajnych](/powershell/module/azurerm.keyvault/backup-azurekeyvaultsecret)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Zweryfikuj wszystkie kopie zapasowe, w tym klucze zarządzane przez klienta

**Wskazówki:** Testowanie przywracania kopii zapasowej kluczy zarządzanych przez klienta.

- [Jak przywrócić klucze magazynu kluczy na platformie Azure](/powershell/module/az.keyvault/restore-azkeyvaultkey)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4. Zapewnianie ochrony kopii zapasowych i kluczy zarządzanych przez klienta

**Wskazówki:** włącz usuwanie nie softowe w Key Vault, aby chronić klucze przed przypadkowym lub złośliwym usunięciem. Azure Event Hubs kluczy zarządzanych przez klienta musi mieć skonfigurowane usuwanie nieużytkcyjne i nie przeczyszczanie.

Skonfiguruj usuwanie nie soft dla konta usługi Azure Storage, które jest używane do przechwytywania Event Hubs danych. Należy pamiętać, że ta funkcja nie jest jeszcze obsługiwana Azure Data Lake Storage Gen 2.

- [Jak włączyć usuwanie nie soft in Key Vault](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

- [Konfigurowanie magazynu kluczy z kluczami](configure-customer-managed-key.md)

- [Soft delete for Azure Storage blobs](/azure/storage/blobs/soft-delete-blob-overview) (Usuwanie nietrwałe dla obiektów blob usługi Azure Storage)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

## <a name="incident-response"></a>Reagowanie na zdarzenia

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: reagowanie na zdarzenia](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1. Tworzenie przewodnika po reagowaniu na zdarzenia

**Wskazówki:** Upewnij się, że istnieją zapisane plany reagowania na zdarzenia, które definiują role personelu, a także fazy obsługi zdarzeń/zarządzania nimi.

- [Jak skonfigurować automatyzacje przepływu pracy w Azure Security Center](../security-center/security-center-planning-and-operations-guide.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2. Tworzenie procedury oceniania i ustalania priorytetów zdarzeń

**Wskazówki:** Security Center przypisuje ważność alertom, aby ułatwić ustalanie priorytetów kolejności, w jakiej są one związane z każdym alertem, tak aby w przypadku naruszenia bezpieczeństwa zasobu można było uzyskać do niego dostęp od razu. Ważność jest oparta na tym, jak pewna Security Center znajduje się w znajdowaniu lub analizie użytej do wystawienia alertu, a także na poziomie pewności, że za działaniem, które doprowadziło do tego alertu, znajduje się złośliwy zamiar.

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="103-test-security-response-procedures"></a>10.3: Testowanie procedur reagowania na zabezpieczenia

**Wskazówki:** Przećwiczyj ćwiczenia, aby regularnie testować możliwości reagowania na zdarzenia w systemach. Zidentyfikuj słabe punkty i przerwy oraz popraw plan zgodnie z wymaganiami.

- [Zapoznaj się z publikacją NIST: Guide to Test, Training, and Exercise Programs for IT Plans and Capabilities (Przewodnik po programach testowych, szkoleniowych i szkoleniowych dla planów i możliwości IT)](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4. Podaj szczegóły kontaktowe zdarzenia zabezpieczeń i skonfiguruj powiadomienia o alertach dla zdarzeń zabezpieczeń

**Wskazówki:** informacje kontaktowe dotyczące zdarzenia zabezpieczeń będą używane przez firmę Microsoft do kontaktowania się z Tobą, jeśli Centrum zabezpieczeń firmy Microsoft (MSRC) odkryje, że dostęp do danych klienta został uzyskany przez niedozwoloną lub niedozwoloną stronę.  Przejrzyj zdarzenia po fakcie, aby upewnić się, że problemy zostały rozwiązane. 

- [Jak ustawić kontakt Azure Security Center zabezpieczeń](../security-center/security-center-provide-security-contact-details.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5. Dołączanie alertów zabezpieczeń do systemu reagowania na zdarzenia

**Wskazówki:** eksportowanie alertów Azure Security Center i zaleceń przy użyciu funkcji eksportu ciągłego. Eksport ciągły umożliwia ręczne lub ciągłe eksportowanie alertów i zaleceń. Możesz użyć łącznika danych Azure Security Center, aby przesyłać strumieniowo alerty do Azure Sentinel.

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

**Wskazówki:** postępuj zgodnie z regułami testowania penetracyjnego firmy Microsoft, aby upewnić się, że testy penetracyjne nie naruszały zasad firmy Microsoft. Użyj strategii firmy Microsoft i wykonania czerwonego zespołu i testów penetracyjnych na żywo w infrastrukturze, usługach i aplikacjach w chmurze zarządzanych przez firmę Microsoft. 

- [Reguły testów penetracyjnych zaangażowania](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Testy typu „red team” w chmurze firmy Microsoft](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

## <a name="next-steps"></a>Następne kroki

- Zobacz [Omówienie testu porównawczego zabezpieczeń platformy Azure w wersji 2](../security/benchmarks/overview.md)
- Dowiedz się więcej o [punktach odniesienia zabezpieczeń platformy Azure](../security/benchmarks/security-baselines-overview.md)
