---
title: Podstawowa usługa Azure Security dla zapory aplikacji sieci Web platformy Azure
description: Linia bazowa zabezpieczeń zapory aplikacji sieci Web platformy Azure zawiera wskazówki i zasoby dotyczące procedur związanych z wdrażaniem zaleceń dotyczących zabezpieczeń określonych w teście zabezpieczeń Azure.
author: msmbaldwin
ms.service: web-application-firewall
ms.topic: conceptual
ms.date: 04/08/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 54582456e5c55f7cbe513f8ab83fbc87ef6a3931
ms.sourcegitcommit: c6a2d9a44a5a2c13abddab932d16c295a7207d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/09/2021
ms.locfileid: "107285415"
---
# <a name="azure-security-baseline-for-azure-web-application-firewall"></a>Podstawowa usługa Azure Security dla zapory aplikacji sieci Web platformy Azure

Ta linia bazowa zabezpieczeń ma zastosowanie do zapory aplikacji sieci Web platformy Azure ze wskazówkami dotyczącymi [usługi Azure Security test w wersji 1,0](../security/benchmarks/overview-v1.md) . Test porównawczy zabezpieczeń platformy Azure zawiera zalecenia dotyczące sposobu zabezpieczania rozwiązań w chmurze na platformie Azure. Zawartość jest pogrupowana według **kontroli zabezpieczeń** zdefiniowanych przez usługę Azure Security test i powiązane wskazówki dotyczące zapory aplikacji sieci Web platformy Azure. 

> [!NOTE]
> **Formanty** nie mają zastosowania do zapory aplikacji sieci Web platformy Azure lub dla których odpowiedzialności są wykluczane przez firmę Microsoft. Aby dowiedzieć się, jak Zapora aplikacji sieci Web platformy Azure jest całkowicie mapowana na test porównawczy zabezpieczeń platformy Azure, zobacz **[pełny plik mapowania zabezpieczeń zapory aplikacji sieci Web platformy Azure](https://github.com/MicrosoftDocs/SecurityBenchmarks/raw/master/Azure%20Offer%20Security%20Baselines/1.1/azure-web-application-firewall-security-baseline-v1.1.xlsx)**.

## <a name="network-security"></a>Bezpieczeństwo sieci

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: bezpieczeństwo sieci](../security/benchmarks/security-control-network-security.md).*

### <a name="13-protect-critical-web-applications"></a>1,3: Ochrona krytycznych aplikacji sieci Web

**Wskazówki**: Użyj Microsoft Azure zapory aplikacji sieci Web (WAF) do scentralizowanej ochrony aplikacji sieci Web przed typowymi programami wykorzystującymi luki w zabezpieczeniach, takimi jak iniekcja kodu SQL i obsługa skryptów między lokacjami. 

- Tryb wykrywania: Użyj tego trybu do uczenia ruchu sieciowego, zrozumienia i przejrzenia fałszywych wyników pozytywnych. Monitoruje i rejestruje wszystkie alerty dotyczące zagrożeń. Upewnij się, że dziennik diagnostyki i WAF jest wybrany i włączony. Należy pamiętać, że WAF nie blokuje żądań przychodzących, gdy działa w trybie wykrywania.
- Tryb zapobiegania: blokuje wtargnięcie i ataki wykryte przez zasady. Osoba atakująca otrzymuje wyjątek "403 nieautoryzowany dostęp", a połączenie jest zamknięte. Tryb zapobiegania rejestruje takie ataki w dziennikach WAF.

Linia bazowa ruchu sieciowego z trybem wykrywania WAF. Po ustaleniu potrzeb związanych z ruchem Skonfiguruj WAF w trybie zapobiegania, aby Bock niepożądany ruch.

Postępuj zgodnie z zaleceniami dotyczącymi wysokiej ważności Security Center wszystkich zasobów z obsługą sieci Web, które nie są chronione przez WAF.  

- [Zasady i reguły reguły KSR zapory aplikacji sieci Web](ag/application-gateway-crs-rulegroups-rules.md) 

- [Tryby WAF na Application Gateway](https://docs.microsoft.com/azure/web-application-firewall/ag/ag-overview#waf-modes)

- [Tryby WAF na przednich drzwiach](https://docs.microsoft.com/azure/web-application-firewall/afds/afds-overview#waf-modes)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: odmowa komunikacji ze znanymi niezłośliwymi adresami IP

**Wskazówki**: Użyj reguł niestandardowych z zaporą aplikacji sieci Web platformy Azure (WAF), aby zezwalać na ruch i blokować go. Na przykład można zablokować cały ruch pochodzący z zakresu adresów IP. Skonfiguruj usługę Azure WAF do uruchamiania w trybie zapobiegania, który blokuje wtargnięcie i ataki wykryte przez zasady. Osoba atakująca otrzymuje wyjątek "403 nieautoryzowany dostęp", a połączenie jest zamknięte. Tryb zapobiegania rejestruje takie ataki w dziennikach WAF.

- [Tryby WAF na Application Gateway](https://docs.microsoft.com/azure/web-application-firewall/ag/ag-overview#waf-modes)

- [Tryby WAF na przednich drzwiach](https://docs.microsoft.com/azure/web-application-firewall/afds/afds-overview#waf-modes)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania**: [wzorzec zabezpieczeń platformy Azure](/azure/governance/policy/samples/azure-security-benchmark) jest domyślną inicjatywy zasad dla Security Center i jest podstawą dla [zaleceń Security Center](/azure/security-center/security-center-recommendations). Definicje Azure Policy powiązane z tym formantem są włączane automatycznie przez Security Center. Alerty związane z tym formantem mogą wymagać planu [usługi Azure Defender](/azure/security-center/azure-defender) dla powiązanych usług.

**Azure Policy wbudowane definicje — Microsoft. Network**:

[!INCLUDE [Resource Policy for Microsoft.Network 1.4](../../includes/policy/standards/asb/rp-controls/microsoft.network-1-4.md)]

### <a name="17-manage-traffic-to-web-applications"></a>1,7: zarządzanie ruchem do aplikacji sieci Web

**Wskazówki**: Zapora aplikacji sieci Web platformy Azure (WAF) jest podstawowym składnikiem ochrony aplikacji sieci Web platformy Azure. Użyj usługi Azure WAF, aby zapewnić scentralizowaną ochronę aplikacji sieci Web przed typowymi programami wykorzystującymi luki w zabezpieczeniach i z wstępnie skonfigurowanym, zarządzanym zestawem reguł w odniesieniu do znanych podpisów ataków z głównych

Dostosuj usługę Azure WAF z regułami i grupami reguł w taki sposób, aby spełniały wymagania aplikacji sieci Web i wyeliminować fałszywe pozytywne. Skojarz zasady usługi Azure WAF dla każdej lokacji za WAF, aby umożliwić konfigurację specyficzną dla lokacji. Usługa Azure WAF obsługuje filtrowanie geograficzne, ograniczanie szybkości, domyślne reguły zestawu reguł zarządzane przez platformę Azure. można też utworzyć reguły niestandardowe, aby odpowiadały potrzebom aplikacji. 

Skonfiguruj usługę Azure WAF do uruchamiania w trybie zapobiegania po określania poziomu odniesienia ruchu sieciowego w trybie wykrywania przez określony czas. Usługa Azure WAF blokuje wtargnięcie i ataki wykryte przez reguły w trybie zapobiegania. Osoba atakująca otrzymuje wyjątek "403 nieautoryzowany dostęp", a połączenie jest zamknięte. Tryb zapobiegania rejestruje takie ataki w dziennikach WAF.

- [Tryby WAF na Application Gateway](https://docs.microsoft.com/azure/web-application-firewall/ag/ag-overview#waf-modes)

- [Tryby WAF na przednich drzwiach](https://docs.microsoft.com/azure/web-application-firewall/afds/afds-overview#waf-modes)

- [Zasady i reguły reguły KSR zapory aplikacji sieci Web](https://docs.microsoft.com/azure/web-application-firewall/ag/application-gateway-crs-rulegroups-rules?tabs=owasp31)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: Minimalizacja złożoności i kosztów administracyjnych reguł zabezpieczeń sieci

**Wskazówki**: Tworzenie, kojarzenie i logicznie organizowanie zasobów w ramach subskrypcji platformy Azure za pomocą tagów służących do wykrywania typowych nieprawidłowych konfiguracji aplikacji (np. Apache i IIS). 

Stosuj reguły i grupy reguł do zasad zapory aplikacji sieci Web platformy Azure (WAF) na podstawie zastosowanych metadanych tagów.

- [Zasady WAF na Application Gateway](/cli/azure/network/application-gateway/waf-policy)

- [Zasady WAFymi na wierzchu drzwi](/cli/azure/ext/front-door/network/front-door/waf-policy)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="110-document-traffic-configuration-rules"></a>1,10: udokumentowanie reguł konfiguracji ruchu

**Wskazówki**: Użyj tagów dla sieciowych grup zabezpieczeń skojarzonych z zaporą aplikacji sieci Web platformy Azure (WAF) w podsieci Application Gateway platformy Azure, a także innych zasobów związanych z zabezpieczeniami sieci i przepływem ruchu. Dla poszczególnych reguł grup zabezpieczeń sieci Użyj pola "opis", aby określić potrzebę biznesową, czas trwania itd., dla każdej reguły, która zezwala na ruch do lub z sieci.

Użyj dowolnych wbudowanych definicji Azure Policy związanych ze znakiem, takich jak "Wymagaj tagu i jego wartości", aby upewnić się, że wszystkie zasoby są tworzone przy użyciu tagów i powiadomienia o istniejących nieoznakowanych zasobach.

Wybierz Azure PowerShell lub interfejs wiersza polecenia platformy Azure, aby wyszukiwać lub wykonywać akcje dotyczące zasobów na podstawie ich tagów.

- [Tworzenie i używanie tagów](../azure-resource-manager/management/tag-resources.md)

- [Jak utworzyć Virtual Network](../virtual-network/quick-create-portal.md)

- [Jak utworzyć sieciowej grupy zabezpieczeń z konfiguracją zabezpieczeń](../virtual-network/tutorial-filter-network-traffic.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: Użyj zautomatyzowanych narzędzi do monitorowania konfiguracji zasobów sieciowych i wykrywania zmian

**Wskazówki**: Użyj dziennika aktywności platformy Azure do monitorowania konfiguracji zasobów sieciowych i wykrywania zmian ustawień sieciowych i zasobów związanych z wdrożeniami zapory aplikacji sieci Web platformy Azure (WAF). Utwórz alerty w Azure Monitor, które będą wyzwalane po wprowadzeniu zmian w krytycznych ustawieniach sieciowych lub zasobach.

- [Jak wyświetlać i pobierać zdarzenia dziennika aktywności platformy Azure](https://docs.microsoft.com/azure/azure-monitor/essentials/activity-log#view-the-activity-log)

- [Jak utworzyć alerty w Azure Monitor](../azure-monitor/alerts/alerts-activity-log.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

## <a name="logging-and-monitoring"></a>Rejestrowanie i monitorowanie

*Aby uzyskać więcej informacji, zobacz temat [Azure Security test: rejestrowanie i monitorowanie](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2,1: Użyj źródeł synchronizacji zatwierdzonego czasu

**Wskazówki**: Tworzenie reguły sieci dla zapory aplikacji sieci Web platformy Azure (WAF) w celu umożliwienia dostępu do serwera NTP przy użyciu odpowiedniego portu i protokołu, takiego jak port 123 za pośrednictwem protokołu UDP.

**Odpowiedzialność**: Współużytkowane

**Monitorowanie Azure Security Center**: brak

### <a name="22-configure-central-security-log-management"></a>2,2: Skonfiguruj centralne zarządzanie dziennikami zabezpieczeń

**Wskazówki**: Skonfiguruj Dzienniki zapory aplikacji sieci Web platformy Azure (WAF), które mają być wysyłane do centralnego rozwiązania do zarządzania dziennikami zabezpieczeń, takiego jak Azure — wskaźnikiem, lub Siem innej firmy. Te dzienniki obejmują dzienniki aktywności, diagnostyki i WAF w czasie rzeczywistym. te dzienniki można następnie wyświetlić w różnych narzędziach, takich jak Azure Monitor, Excel i Power BI. Dzienniki zapory aplikacji internetowych platformy Azure zapewniają wgląd w dane, które usługa Azure WAF ocenia, dopasowuje i blokuje.

Wskaźnik platformy Azure ma wbudowany skoroszyt usługi Azure WAF, który zawiera omówienie zdarzeń zabezpieczeń w usłudze Azure WAF. Ten skoroszyt zawiera zdarzenia, dopasowane i zablokowane reguły oraz wszystkie inne, które są rejestrowane w dziennikach zapory. Te dane telemetryczne mogą służyć do uruchamiania usługi element PlayBook Automation do powiadamiania lub podejmowania akcji korygowania na podstawie zdarzeń WAF zbieranych przez wskaźnik platformy Azure.

- [Wyświetlanie dzienników aktywności](../azure-resource-manager/management/view-activity-logs.md)

- [Dzienniki diagnostyczne dla Application Gateway](../application-gateway/application-gateway-diagnostics.md)

- [Łączenie danych z zapory aplikacji sieci Web firmy Microsoft z platformą Azure — wskaźnikiem](../sentinel/connect-azure-waf.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: Włączanie rejestrowania inspekcji dla zasobów platformy Azure

**Wskazówki**: Włączanie rejestrowania zasobów zapory aplikacji sieci Web platformy Azure (WAF) w celu uzyskania dostępu do dzienników inspekcji, zabezpieczeń i diagnostyki. Zapora aplikacji sieci Web platformy Azure udostępnia szczegółowe raporty na temat wszystkich wykrytych zagrożeń, które są udostępniane w skonfigurowanych dziennikach diagnostycznych. Dzienniki aktywności, które są automatycznie dostępne, obejmują źródło zdarzeń, datę, użytkownika, sygnaturę czasową, adresy źródłowe, adresy docelowe i inne przydatne elementy.

- [Omówienie rejestrowania](https://docs.microsoft.com/azure/web-application-firewall/ag/ag-overview#logging)

- [Przegląd zapytania dziennika Azure Monitor](../azure-monitor/logs/log-query-overview.md)

- [Omówienie dzienników platformy Azure](../azure-monitor/essentials/platform-logs-overview.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="25-configure-security-log-storage-retention"></a>2,5: Konfigurowanie przechowywania magazynu dzienników zabezpieczeń

**Wskazówki**: wysyłanie dzienników zapory aplikacji sieci Web platformy Azure (WAF) do niestandardowego konta magazynu i definiowanie zasad przechowywania. Użyj Azure Monitor, aby ustawić okres przechowywania Log Analytics obszaru roboczego na podstawie wymagań dotyczących zgodności w organizacji.
- [Konfigurowanie monitorowania dla konta magazynu](https://docs.microsoft.com/azure/storage/common/manage-storage-analytics-logs#configure-logging)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="26-monitor-and-review-logs"></a>2,6: dzienniki monitorowania i przeglądania

**Wskazówki**: Zapora aplikacji sieci Web (WAF) platformy Azure udostępnia szczegółowe raporty dotyczące każdego wykrytego zagrożenia. Rejestrowanie jest zintegrowane z dziennikami Diagnostyka Azure, które zawierają bogate informacje o operacjach i błędach, które są ważne w przypadku inspekcji i rozwiązywania problemów. 

Wystąpienia usługi Azure WAF są zintegrowane z Security Center w celu wysyłania alertów i informacji o kondycji związanych z raportowaniem. Użyj zaleceń Security Center do wykrywania niechronionych aplikacji sieci Web i ochrony tych zasobów. 

Wskaźnik platformy Azure ma wbudowany skoroszyt zdarzeń WAF-zapór, który zawiera omówienie zdarzeń zabezpieczeń w WAF. Obejmują one zdarzenia, dopasowane i zablokowane reguły oraz wszystkie inne, które są rejestrowane w dziennikach zapory.

- [Jak włączyć ustawienia diagnostyczne dla dziennika aktywności platformy Azure](/azure/azure-monitor/platform/activity-log)

- [Jak włączyć ustawienia diagnostyczne dla Application Gateway platformy Azure](../application-gateway/application-gateway-diagnostics.md)

- [Monitorowanie metryk i dzienników na platformie Azure — drzwiczki](../frontdoor/front-door-diagnostics.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: Włączanie alertów dla nietypowych działań

**Wskazówki**: Włączanie ustawień diagnostycznych dziennika aktywności platformy Azure oraz ustawień diagnostycznych usługi Azure WAF oraz wysyłanie dzienników do obszaru roboczego log Analytics. Wykonuj zapytania w Log Analytics, aby wyszukiwać terminy, identyfikować trendy, analizować wzorce i udostępniać wiele innych szczegółowych informacji na podstawie zebranych danych. Tworzenie alertów dotyczących nietypowej aktywności opartej na metrykach WAF. Na przykład jeśli zablokowano liczbę żądań przekroczenia "X", "do" Y ".

- [Jak włączyć ustawienia diagnostyczne dla dziennika aktywności platformy Azure](/azure/azure-monitor/essentials/diagnostic-settings-legacy)

- [Jak tworzyć alerty na platformie Azure](../azure-monitor/alerts/tutorial-response.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="28-centralize-anti-malware-logging"></a>2,8: scentralizowanie rejestrowania chroniącego przed złośliwym oprogramowaniem

**Wskazówki**: Wdróż zaporę aplikacji sieci Web platformy Azure (WAF) przed krytycznymi aplikacjami sieci Web, aby uzyskać dodatkową inspekcję ruchu przychodzącego. 

Usługa Azure WAF zapewnia centralną ochronę aplikacji sieci Web przed typowymi programami wykorzystującymi luki w zabezpieczeniach i zabezpiecza aplikacje przez inspekcję ruchu przychodzącego sieci Web w taki sposób, aby blokowały ataki, takie jak iniekcje SQL, skrypty między lokacjami, operacje przekazywania złośliwego oprogramowania i ataki DDoS.

- [Jak wdrożyć usługę Azure WAF](ag/create-waf-policy-ag.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

## <a name="identity-and-access-control"></a>Tożsamość i kontrola dostępu

*Aby uzyskać więcej informacji, zobacz informacje o [teście zabezpieczeń Azure: Identity i Access Control](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: obsługa spisu kont administracyjnych

**Wskazówki**: Azure Active Directory (Azure AD) mają wbudowane role, które są dostępne dla zapytań i muszą być jawnie przypisane. Za pomocą modułu Azure AD PowerShell można wykonywać zapytania ad hoc w celu odnajdywania kont należących do grup administracyjnych.

- [Jak uzyskać rolę katalogu w usłudze Azure AD przy użyciu programu PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0&amp;preserve-view=true)

- [Jak uzyskać członków roli katalogu w usłudze Azure AD przy użyciu programu PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0&amp;preserve-view=true)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: Użyj dedykowanych kont administracyjnych

**Wskazówki**: Brak dostępnych przypisań administratora lokalnego w ramach WAF. Mogą jednak istnieć role administratorów Azure Active Directory (Azure AD) w środowisku, które umożliwiają kontrolę nad zasobami usługi Azure WAF.
Dobrym sposobem jest utworzenie standardowych procedur operacyjnych dotyczących używania dedykowanych kont administracyjnych, które mają dostęp do wystąpień zapory aplikacji sieci Web (WAF) platformy Azure. Użyj funkcji zarządzania tożsamościami i dostępem Security Center, aby monitorować liczbę kont administracyjnych.

- [Informacje o tożsamości i dostępie Azure Security Center](../security-center/security-center-identity-access.md)

- [Informacje na temat tworzenia użytkowników administracyjnych w Azure Database for PostgreSQL](https://docs.microsoft.com/azure/postgresql/howto-create-users#the-server-admin-account)

- [Jak używać Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Użyj uwierzytelniania wieloskładnikowego dla wszystkich Azure Active Directory dostępu opartego na usłudze

**Wskazówki**: Włączanie uwierzytelniania wieloskładnikowego Azure Active Directory (Azure AD) i przestrzeganie zaleceń dotyczących zarządzania tożsamościami i dostępem Security Center.

- [Jak włączyć uwierzytelnianie wieloskładnikowe na platformie Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Jak monitorować tożsamość i dostęp w Azure Security Center](../security-center/security-center-identity-access.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: Używaj dedykowanych maszyn (uprzywilejowany dostęp do stacji roboczych) dla wszystkich zadań administracyjnych

**Wskazówki**: Użyj stacji roboczej dostępu uprzywilejowanego (dostępem uprzywilejowanym) z uwierzytelnianiem wieloskładnikowym skonfigurowanym do logowania się i konfigurowania zapory aplikacji sieci Web platformy Azure (WAF) i powiązanych zasobów.

- [Dowiedz się więcej o stacjach roboczych uprzywilejowanego dostępu](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Jak włączyć uwierzytelnianie wieloskładnikowe na platformie Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: Rejestruj i Ostrzegaj o podejrzanych działaniach z kont administracyjnych

**Wskazówki**: Użyj raportów zabezpieczeń usługi Azure Active Directory (Azure AD) na potrzeby generowania dzienników i alertów w przypadku wystąpienia podejrzanych lub niebezpiecznych działań w środowisku. Użyj Security Center, aby monitorować działania związane z tożsamościami i dostępem.

- [Identyfikowanie użytkowników usługi Azure AD oflagowanych na skutek ryzykownego działania](../active-directory/identity-protection/overview-identity-protection.md)

- [Jak monitorować działania użytkowników związane z tożsamościami i dostępem w usłudze Azure Security Center](../security-center/security-center-identity-access.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: zarządzanie zasobami platformy Azure tylko z zatwierdzonych lokalizacji

**Wskazówki**: Skonfiguruj warunek lokalizacji zasad dostępu warunkowego i Zarządzaj nazwanymi lokalizacjami.

Utwórz logiczne grupowanie zakresów adresów IP lub krajów i regionów z nazwanymi lokalizacjami. Ogranicz dostęp do poufnych zasobów, takich jak Azure Key Vault Secret, do skonfigurowanych nazwanych lokalizacji.

- [Co to jest warunek lokalizacji w usłudze Azure Active Directory (Azure AD) dostęp warunkowy](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="39-use-azure-active-directory"></a>3,9: Użyj Azure Active Directory

**Wskazówki**: Użyj Azure Active Directory (Azure AD) jako centralnego systemu uwierzytelniania i autoryzacji. Usługa Azure AD chroni dane przy użyciu silnego szyfrowania danych przechowywanych i przesyłanych, a także Sole, skróty i bezpieczne przechowywanie poświadczeń użytkownika.
- [Jak utworzyć i skonfigurować wystąpienie usługi Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: regularnie Przeglądaj i Uzgodnij dostęp użytkowników

**Wskazówki**: Azure Active Directory (Azure AD) zawiera dzienniki ułatwiające wykrywanie starych kont. Za pomocą przeglądów dostępu do tożsamości platformy Azure można efektywnie zarządzać członkostwem w grupach, dostępem do aplikacji dla przedsiębiorstw i przypisaniami ról. Regularnie Przeglądaj dostęp użytkowników, aby upewnić się, że tylko aktywni użytkownicy mają ciągły dostęp.

- [Informacje o raportowaniu usługi Azure AD](/azure/active-directory/reports-monitoring)

- [Jak korzystać z przeglądów dostępu do tożsamości platformy Azure](../active-directory/governance/access-reviews-overview.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: Monitor próbuje uzyskać dostęp do zdezaktywowanych poświadczeń

**Wskazówki**: Integrowanie działań związanych z logowaniem Azure Active Directory (Azure AD), inspekcją i ryzykiem dzienników zdarzeń ze źródłami przy użyciu dowolnego narzędzia Siem lub monitorowania, takiego jak Azure — wskaźnik.

Usprawnij ten proces, tworząc ustawienia diagnostyczne dla kont użytkowników usługi Azure AD i wysyłając dzienniki inspekcji i dzienniki logowania do obszaru roboczego Log Analytics. Skonfiguruj żądane alerty w obszarze roboczym Log Analytics.

- [Jak zintegrować dzienniki aktywności platformy Azure z usługą Azure Monitor](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: odchylenia zachowania alertu dotyczącego logowania na koncie

**Wskazówki**: Aby skonfigurować automatyczne reagowanie na wykryte podejrzane działania związane z tożsamościami użytkowników, należy użyć funkcji ryzyka i ochrony tożsamości usługi Azure Active Directory (Azure AD). Pozyskiwanie danych na platformie Azure — Wskaźnikowanie do dalszej analizy.

- [Jak wyświetlić ryzykowne logowania w usłudze Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

- [Jak skonfigurować i włączyć zasady dotyczące ryzyka związanego z ochroną tożsamości](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Jak dołączyć wskaźnik na platformie Azure](../sentinel/quickstart-onboard.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

## <a name="data-protection"></a>Ochrona danych

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: ochrona danych](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: przechowywanie spisu poufnych informacji

**Wskazówki**: Użyj tagów, aby pomóc w śledzeniu zapory aplikacji sieci Web (WAF) platformy Azure i powiązanych zasobów, które przechowują lub przetwarzają informacje poufne.

- [Tworzenie i używanie tagów](../azure-resource-manager/management/tag-resources.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: Izoluj systemy przechowujące lub przetwarzające informacje poufne

**Wskazówki**: Zaimplementuj izolację przy użyciu osobnych subskrypcji i grup zarządzania dla poszczególnych domen zabezpieczeń, takich jak typ środowiska i poziom czułości danych, na przykład programowanie, testowanie i środowiska produkcyjne. 

Kontroluj dostęp do zasobów platformy Azure za pomocą usługi Azure Active Directory (Azure AD) kontroli dostępu opartej na rolach (RBAC na platformie Azure).

- [Jak utworzyć dodatkowe subskrypcje platformy Azure](../cost-management-billing/manage/create-subscription.md)

- [Jak utworzyć Grupy zarządzania](../governance/management-groups/create-management-group-portal.md)

- [Tworzenie i używanie tagów](../azure-resource-manager/management/tag-resources.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: Szyfruj wszystkie poufne informacje podczas przesyłania

**Wskazówki**: Szyfruj wszystkie informacje poufne podczas przesyłania. Upewnij się, że wszyscy klienci łączący się z wystąpieniami zapory aplikacji sieci Web (WAF) platformy Azure i powiązanymi zasobami mogą negocjować protokół TLS 1,2 lub nowszy.

Postępuj zgodnie z zaleceniami Security Center na potrzeby szyfrowania w czasie spoczynku i szyfrowania w przypadku, gdy ma to zastosowanie.

- [Informacje o szyfrowaniu podczas przesyłania na platformę Azure](https://docs.microsoft.com/azure/security/fundamentals/encryption-overview#encryption-of-data-in-transit)

**Odpowiedzialność**: Współużytkowane

**Monitorowanie Azure Security Center**: brak

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4,6: Korzystaj z usługi Azure RBAC, aby zarządzać dostępem do zasobów 

**Wskazówki**: kontrolowanie dostępu do zasobów platformy Azure, takich jak Zapora aplikacji sieci Web, przy użyciu kontroli dostępu opartej na rolach (Azure RBAC).

- [Jak skonfigurować usługę Azure RBAC na platformie Azure](../role-based-access-control/role-assignments-portal.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: Szyfruj poufne informacje w spoczynku

**Wskazówki**: Użyj szyfrowania w stanie spoczynku dla wszystkich zasobów platformy Azure, w tym zapory aplikacji sieci Web (WAF) platformy Azure i powiązanych zasobów. Firma Microsoft zaleca, aby platforma Azure mogła zarządzać kluczami szyfrowania, jednak istnieje możliwość zarządzania własnymi kluczami w niektórych wystąpieniach.

- [Informacje o szyfrowaniu danych magazynowanych na platformie Azure](../security/fundamentals/encryption-atrest.md)

- [Jak skonfigurować zarządzane przez klienta klucze szyfrowania](https://docs.microsoft.com/azure/storage/common/customer-managed-keys-configure-key-vault?tabs=portal)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: Rejestruj i Ostrzegaj o zmianach krytycznych zasobów platformy Azure

**Wskazówki**: Skonfiguruj zaporę aplikacji sieci Web platformy Azure (WAF) do uruchamiania w trybie zapobiegania po określania poziomu odniesienia ruchu sieciowego w trybie wykrywania przez ustalony czas. 

Usługa Azure WAF w trybie zapobiegania blokuje wtargnięcie i ataki wykryte przez zasady. Osoba atakująca otrzymuje wyjątek "403 nieautoryzowany dostęp", a połączenie jest zamknięte. Tryb zapobiegania rejestruje takie ataki w dziennikach WAF.

- [Omówienie integracji między Application Gateway i Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-partner-integration#overview)

- [Tryby WAF na Application Gateway](https://docs.microsoft.com/azure/web-application-firewall/ag/ag-overview#waf-modes)

- [Tryby WAF na przednich drzwiach](https://docs.microsoft.com/azure/web-application-firewall/afds/afds-overview#waf-modes)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

## <a name="inventory-and-asset-management"></a>Zarządzanie magazynem i zasobami

*Aby uzyskać więcej informacji, zobacz temat [Azure Security test: Inventory and Asset Management](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: Użyj rozwiązania automatycznego odnajdywania zasobów

**Wskazówki**: usługa Azure Resource Graph umożliwia wykonywanie zapytań lub odnajdywanie wszystkich zasobów, takich jak zasoby obliczeniowe, magazyn, Sieć, porty i protokoły, i tak dalej w ramach subskrypcji.

Zapewnij odpowiednie uprawnienia (odczyt) w dzierżawie i Wylicz wszystkie subskrypcje platformy Azure oraz zasoby w ramach subskrypcji. Mimo że klasyczne zasoby platformy Azure mogą zostać odnalezione za pośrednictwem grafu zasobów, zdecydowanie zaleca się tworzenie i używanie Azure Resource Manager zasobów do przodu.

- [Jak tworzyć zapytania przy użyciu grafu zasobów platformy Azure](../governance/resource-graph/first-query-portal.md)

- [Jak wyświetlić subskrypcje platformy Azure](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-4.8.0&amp;preserve-view=true)

- [Opis kontroli RBAC platformy Azure](../role-based-access-control/overview.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="62-maintain-asset-metadata"></a>6,2: Konserwowanie metadanych zasobów

**Wskazówki**: używanie tagów w zasadach zapory aplikacji sieci Web platformy Azure (WAF). Tagi mogą być skojarzone z zasobami i stosowane logicznie w celu organizowania dostępu do tych zasobów w ramach subskrypcji klienta. 

- [Tworzenie i używanie tagów](../azure-resource-manager/management/tag-resources.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: Usuń nieautoryzowane zasoby platformy Azure

**Wskazówki**: używanie tagowania, grup zarządzania i oddzielnych subskrypcji, gdzie to konieczne, do organizowania i śledzenia usługi Azure WAF i powiązanych zasobów. Regularnie Uzgadniaj spis i zapewnij, że nieautoryzowane zasoby są usuwane z subskrypcji w odpowiednim czasie.

- [Jak utworzyć dodatkowe subskrypcje platformy Azure](../cost-management-billing/manage/create-subscription.md)

- [Jak utworzyć Grupy zarządzania](../governance/management-groups/create-management-group-portal.md)

- [Tworzenie i używanie tagów](../azure-resource-manager/management/tag-resources.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6,4: Definiowanie i konserwowanie spisu zatwierdzonych zasobów platformy Azure

**Wskazówki**: Tworzenie spisu zatwierdzonych zasobów, w tym konfiguracji w zależności od potrzeb organizacji.

Użyj Azure Policy, aby wprowadzić ograniczenia dotyczące typu zasobów, które mogą być tworzone w ramach subskrypcji. Usługa Azure Resource Graph umożliwia wykonywanie zapytań dotyczących zasobów i odnajdywanie ich w ramach subskrypcji. Upewnij się, że wszystkie zasoby platformy Azure obecne w środowisku zostały zatwierdzone.

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

- [Jak tworzyć zapytania przy użyciu grafu zasobów platformy Azure](../governance/resource-graph/first-query-portal.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: Monitoruj niezatwierdzone zasoby platformy Azure

**Wskazówki**: Użyj Azure Policy, aby wprowadzić ograniczenia dotyczące typu zasobów, które mogą być tworzone w ramach subskrypcji.
Użyj grafu zasobów platformy Azure do wykonywania zapytań lub odnajdywania zasobów usługi Azure Web Application Firewall (WAF) w ramach swoich subskrypcji. Upewnij się, że wszystkie usługi Azure WAF i powiązane zasoby obecne w środowisku są zatwierdzone.

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

- [Jak tworzyć zapytania za pomocą usługi Azure Graph](../governance/resource-graph/first-query-portal.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: Usuń niezatwierdzone zasoby platformy Azure i aplikacje oprogramowania

**Wskazówki**: Monitoruj i usuwaj niezatwierdzone zasoby platformy Azure WAF z Azure Policy, aby odmówić wdrożenia usługi Azure WAF lub określonego typu WAF, na przykład Azure WAF v1 vs v2.

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="69-use-only-approved-azure-services"></a>6,9: Używaj tylko zatwierdzonych usług platformy Azure

**Wskazówki**: Użyj Azure Policy, aby ograniczyć usługi, które możesz udostępnić w danym środowisku.

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

- [Jak odmówić określonego typu zasobu za pomocą Azure Policy](/azure/governance/policy/samples/built-in-policies#general)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: Ogranicz możliwość korzystania przez użytkowników z Azure Resource Manager

**Wskazówki**: Użyj dostępu warunkowego platformy Azure, aby ograniczyć możliwość współdziałania z Menedżerem zasobów platformy Azure przez skonfigurowanie "blokowania dostępu" dla aplikacji "Microsoft Azure Management".

- [Jak skonfigurować dostęp warunkowy, aby blokować dostęp do usługi Azure Resources](../role-based-access-control/conditional-access-azure-management.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: fizyczne lub logiczne rozdzielenie aplikacji wysokiego ryzyka

**Wskazówki**: Zapora aplikacji sieci Web platformy Azure (WAF) może być skojarzona z różnymi środowiskami za pośrednictwem sieci, grup zasobów lub subskrypcji w celu rozdzielenia aplikacji o wysokim ryzyku.

- [Omówienie usługi Azure Virtual Network](../virtual-network/virtual-networks-overview.md)

- [Organizowanie zasobów przy użyciu grup zarządzania platformy Azure](../governance/management-groups/overview.md)

- [Przewodnik po decyzjach związanych z subskrypcjami](/azure/cloud-adoption-framework/decision-guides/subscriptions/)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

## <a name="secure-configuration"></a>Bezpieczna konfiguracja

*Aby uzyskać więcej informacji, zobacz temat [Azure Security test: bezpieczna konfiguracja](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: Ustanów bezpieczne konfiguracje dla wszystkich zasobów platformy Azure

**Wskazówki**: Definiowanie i implementowanie standardowych konfiguracji zabezpieczeń dla ustawień sieciowych związanych ze wdrożeniami zapory aplikacji sieci Web platformy Azure (WAF).

Użyj aliasów Azure Policy w przestrzeni nazw "Microsoft. Network", aby utworzyć zasady niestandardowe do inspekcji lub wymuszania konfiguracji sieci bram aplikacji platformy Azure, sieci wirtualnych, sieciowych grup zabezpieczeń i używania wbudowanych definicji zasad.

- [Jak wyświetlić dostępne aliasy Azure Policy](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-4.8.0&amp;preserve-view=true)

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: obsługa bezpiecznych konfiguracji zasobów platformy Azure

**Wskazówki**: Użyj Azure Policy [Odmów] i [Wdróż, jeśli nie istnieje] efektów, aby wymusić bezpieczne ustawienia w ramach zapory aplikacji sieci Web (WAF) platformy Azure i powiązanych zasobów. 

Użyj szablonów Azure Resource Manager, aby zachować konfigurację zabezpieczeń platformy Azure WAF i powiązanych zasobów wymaganych przez organizację.

- [Zrozumienie efektów Azure Policy](../governance/policy/concepts/effects.md)

- [Tworzenie zasad i zarządzanie nimi w celu wymuszania zgodności](../governance/policy/tutorials/create-and-manage.md)

- [Przegląd szablonów Azure Resource Manager](../azure-resource-manager/templates/overview.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: bezpiecznie przechowuj konfigurację zasobów platformy Azure

**Wskazówki**: Użyj usługi Azure DevOps, aby bezpiecznie przechowywać kod, taki jak niestandardowe zasady platformy Azure i szablony Azure Resource Manager.

Przyznaj lub Odmów uprawnień do określonych użytkowników, wbudowanych grup zabezpieczeń lub grup zdefiniowanych w Azure Active Directory (Azure AD), jeśli są zintegrowane z usługą Azure DevOps lub z usługą Azure AD, jeśli są zintegrowane z usługą Team Foundation Server (TFS).

- [Jak przechowywać kod w usłudze Azure DevOps](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops&amp;preserve-view=true)

- [Informacje o uprawnieniach i grupach na platformie Azure DevOps](/azure/devops/organizations/security/about-permissions)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: Wdrażanie narzędzi do zarządzania konfiguracją dla zasobów platformy Azure

**Wskazówki**: Użyj wbudowanych definicji Azure Policy, a także aliasów Azure Policy w przestrzeni nazw "Microsoft. Network", aby utworzyć zasady niestandardowe na potrzeby alertów, inspekcji i wymuszania konfiguracji systemu. Opracowywanie procesu i potoku w celu zarządzania wyjątkami zasad.

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

- [Dokumentacja usługi Azure Policy](/azure/governance/policy)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: Zaimplementuj automatyczne monitorowanie konfiguracji dla zasobów platformy Azure

**Wskazówki**: Użyj wbudowanych definicji Azure Policy, a także aliasów Azure Policy w przestrzeni nazw "Microsoft. Network", aby utworzyć zasady niestandardowe na potrzeby alertów, inspekcji i wymuszania konfiguracji systemu. 

Użyj Azure Policy [Audit], [Odmów] i [Wdróż, jeśli nie istnieje] powoduje automatyczne wymuszanie konfiguracji zasobów platformy Azure.

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

- [Dokumentacja usługi Azure Policy](/azure/governance/policy)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="711-manage-azure-secrets-securely"></a>7,11: bezpieczne zarządzanie wpisami tajnymi platformy Azure

**Wskazówki**: Użyj Azure Key Vault, aby bezpiecznie przechowywać certyfikaty. Azure Key Vault to magazyn tajny zarządzany przez platformę, którego można użyć do zabezpieczenia kluczy tajnych, klucze i certyfikatów SSL. 

Usługa Azure Application Gateway obsługuje integrację z Key Vault dla certyfikatów serwera, które są dołączone do odbiorników z włączonym protokołem HTTPS. 

- [Jak skonfigurować kończenie połączeń SSL z certyfikatami Key Vault przy użyciu Azure PowerShell](../application-gateway/configure-keyvault-ps.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: eliminowanie nieprzewidzianego narażenia na poświadczenia

**Wskazówki**: Implementuj skaner poświadczeń, aby identyfikować poświadczenia w kodzie, co również pozwala na przeniesienie odnalezionych poświadczeń do bardziej bezpiecznych lokalizacji, takich jak Azure Key Vault.
- [Jak skonfigurować skaner poświadczeń](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

## <a name="data-recovery"></a>Odzyskiwanie danych

*Aby uzyskać więcej informacji, zobacz [test dotyczący zabezpieczeń platformy Azure: odzyskiwanie danych](../security/benchmarks/security-control-data-recovery.md).*

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: zapewnianie ochrony kopii zapasowych i kluczy zarządzanych przez klienta

**Wskazówki**: Upewnij się, że dla Azure Key Vault jest włączona funkcja usuwania nietrwałego. Usuwanie nietrwałe umożliwia Odzyskiwanie usuniętych magazynów kluczy i obiektów magazynu, takich jak klucze, wpisy tajne i certyfikaty.

- [Jak używać usuwania nietrwałego Azure Key Vault](https://docs.microsoft.com/azure/key-vault/general/key-vault-recovery?tabs=azure-powershell&amp;preserve-view=true)

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

Wyraźnie oznaczaj subskrypcje (na przykład produkcyjne, nieprodukcyjne) i Utwórz system nazewnictwa, aby jasno identyfikować i klasyfikować zasoby platformy Azure.

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="103-test-security-response-procedures"></a>10,3: procedury odpowiedzi na zabezpieczenia testowe

**Wskazówki**: przeprowadzanie ćwiczeń w celu przetestowania możliwości reagowania na zdarzenia systemu w regularnych erze. Zidentyfikuj słabe punkty i przerwy oraz popraw plan zgodnie z wymaganiami.
- [Zapoznaj się z przewodnikiem dotyczącym publikacji NIST, aby przetestować, szkolenie i ćwiczenie programów dla planów i możliwości IT](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: Podaj szczegóły kontaktu dotyczącego zabezpieczeń i Skonfiguruj powiadomienia dotyczące alertów dotyczących zdarzeń związanych z zabezpieczeniami

**Wskazówki**: informacje kontaktowe dotyczące zdarzenia zabezpieczeń będą używane przez firmę Microsoft do skontaktowania się z Tobą, jeśli firma Microsoft Security Response Center (MSRC) wykryje, że dostęp do danych klienta został uzyskany przez nielegalną lub nieautoryzowaną osobę. Przejrzyj zdarzenia po fakcie, aby upewnić się, że problemy zostały rozwiązane.
- [Jak ustawić kontakt z zabezpieczeniami Azure Security Center](../security-center/security-center-provide-security-contact-details.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: Uwzględnij alerty zabezpieczeń w systemie odpowiedzi na zdarzenia

**Wskazówki**: eksportowanie alertów i zaleceń dotyczących Security Center przy użyciu funkcji eksportu ciągłego. Eksport ciągły umożliwia wyeksportowanie alertów i zaleceń ręcznie lub w stały sposób ciągły. Użyj łącznika danych Azure Security Center, aby przesyłać strumieniowo alerty do systemu Azure, zgodnie z wymaganiami organizacji.

- [Jak skonfigurować eksport ciągły](../security-center/continuous-export.md)

- [Jak przesłać strumieniowo alerty do usługi Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: Automatyzowanie odpowiedzi na alerty zabezpieczeń

**Wskazówki**: Użyj funkcji automatyzacji przepływu pracy w programie Security Center, aby automatycznie wyzwalać odpowiedzi za pośrednictwem "Logic Apps" na temat alertów zabezpieczeń i zaleceń.
- [Jak skonfigurować automatyzację przepływu pracy i Logic Apps](../security-center/workflow-automation.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

## <a name="penetration-tests-and-red-team-exercises"></a>Testy penetracyjne i ćwiczenia typu „red team”

*Aby uzyskać więcej informacji, zobacz test [porównawczy zabezpieczeń platformy Azure: testy penetracji i czerwone ćwiczenia zespołu](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: Przeprowadź regularne testowanie penetracji zasobów platformy Azure i zadbaj o skorygowanie wszystkich krytycznych ustaleń dotyczących zabezpieczeń

**Wskazówki**: Postępuj zgodnie z zasadami firmy Microsoft dotyczącymi zaangażowania, aby upewnić się, że testy penetracji nie naruszają zasad firmy Microsoft. Korzystaj z strategii firmy Microsoft i wykonywania testów na żywo z obsługą tworzenia zespołu, usług i aplikacji w chmurze, które są zarządzane przez firmę Microsoft. 

- [Reguły testów penetracyjnych zaangażowania](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Testy typu „red team” w chmurze firmy Microsoft](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Odpowiedzialność**: Współużytkowane

**Monitorowanie Azure Security Center**: brak

## <a name="next-steps"></a>Następne kroki

- Zobacz [Omówienie testu porównawczego zabezpieczeń platformy Azure w wersji 2](/azure/security/benchmarks/overview)
- Dowiedz się więcej o [punktach odniesienia zabezpieczeń platformy Azure](/azure/security/benchmarks/security-baselines-overview)
