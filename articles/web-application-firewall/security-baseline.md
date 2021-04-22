---
title: Punkt odniesienia zabezpieczeń platformy Azure dla Azure Web Application Firewall
description: Punkt Azure Web Application Firewall zabezpieczeń zawiera wskazówki proceduralne i zasoby dotyczące wdrażania zaleceń dotyczących zabezpieczeń określonych w te testach porównawczych zabezpieczeń platformy Azure.
author: msmbaldwin
ms.service: web-application-firewall
ms.topic: conceptual
ms.date: 04/08/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 6aa3e2b84c4349e2134ddeb2a60fd193f56f84e8
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107875970"
---
# <a name="azure-security-baseline-for-azure-web-application-firewall"></a>Punkt odniesienia zabezpieczeń platformy Azure dla Azure Web Application Firewall

Ten punkt odniesienia zabezpieczeń stosuje wskazówki z testu porównawczego zabezpieczeń platformy [Azure w wersji 1.0](../security/benchmarks/overview-v1.md) do Azure Web Application Firewall. Test porównawczy zabezpieczeń platformy Azure zawiera zalecenia dotyczące sposobu zabezpieczania rozwiązań w chmurze na platformie Azure. Zawartość jest pogrupowana według **kontrolek zabezpieczeń zdefiniowanych** przez test porównawczy zabezpieczeń platformy Azure i powiązanych wskazówek dotyczących Azure Web Application Firewall. 

> [!NOTE]
> **Wykluczono** mechanizmy kontroli Azure Web Application Firewall, w przypadku których odpowiedzialność należy do firmy Microsoft. Aby zobaczyć, Azure Web Application Firewall mapowanie na test porównawczy zabezpieczeń platformy Azure, zobacz pełny **[Azure Web Application Firewall mapowania punktów odniesienia zabezpieczeń.](https://github.com/MicrosoftDocs/SecurityBenchmarks/raw/master/Azure%20Offer%20Security%20Baselines/1.1/azure-web-application-firewall-security-baseline-v1.1.xlsx)**

## <a name="network-security"></a>Bezpieczeństwo sieci

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: bezpieczeństwo sieci](../security/benchmarks/security-control-network-security.md).*

### <a name="13-protect-critical-web-applications"></a>1.3: Ochrona krytycznych aplikacji internetowych

**Wskazówki:** używanie Microsoft Azure Web Application Firewall (WAF) do scentralizowanej ochrony aplikacji internetowych przed powszechnymi lukami w zabezpieczeniach i lukami w zabezpieczeniach, takimi jak iniekcja SQL i wykonywanie skryptów między witrynami. 

- Tryb wykrywania: użyj tego trybu do uczenia ruchu sieciowego, rozumienia i przeglądania wyników fałszywie dodatnich. Monitoruje i rejestruje wszystkie alerty o zagrożeniach. Upewnij się, że wybrano i włączona opcja Diagnostyka i dziennik aplikacji sieci Szkieletowej. Należy pamiętać, że serwer WAF nie blokuje żądań przychodzących, gdy działa w trybie wykrywania.
- Tryb zapobiegania: blokuje włamania i ataki wykryte przez reguły. Osoba atakująca otrzymuje wyjątek "403 brak autoryzacji dostępu" i połączenie jest zamykane. Tryb zapobiegania rejestruje takie ataki w dziennikach WAF.

Przekieruj ruch sieciowy do trybu wykrywania aplikacji sieci Szkieletowej. Po określeniu potrzeb w zakresie ruchu należy skonfigurować w trybie zapobiegania aplikację WAF tak, aby ruch był niepożądany.

Postępuj zgodnie z zaleceniami o wysokiej ważności z witryny Security Center dla wszystkich zasobów internetowych, które nie są chronione przez funkcję WAF.  

- [Web Application Firewall reguły i grupy reguł CRS](ag/application-gateway-crs-rulegroups-rules.md) 

- [Tryby WAF na Application Gateway](https://docs.microsoft.com/azure/web-application-firewall/ag/ag-overview#waf-modes)

- [Tryby WAF na Front Door](https://docs.microsoft.com/azure/web-application-firewall/afds/afds-overview#waf-modes)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Odmowa komunikacji ze znanymi złośliwymi adresami IP

**Wskazówki:** Używaj reguł niestandardowych z regułami Azure Web Application Firewall (WAF), aby zezwalać na ruch i go blokować. Na przykład cały ruch wychodzący z zakresu adresów IP może być blokowany. Skonfiguruj usługę Azure WAF do uruchamiania w trybie zapobiegania, który blokuje włamania i ataki wykryte przez reguły. Osoba atakująca otrzymuje wyjątek "403 brak autoryzacji dostępu" i połączenie jest zamykane. Tryb zapobiegania rejestruje takie ataki w dziennikach WAF.

- [Tryby WAF na Application Gateway](https://docs.microsoft.com/azure/web-application-firewall/ag/ag-overview#waf-modes)

- [Tryby WAF na Front Door](https://docs.microsoft.com/azure/web-application-firewall/afds/afds-overview#waf-modes)

**Odpowiedzialność**: Klient

**Azure Security Center:** Test porównawczy zabezpieczeń platformy [Azure](/azure/governance/policy/samples/azure-security-benchmark) to domyślna inicjatywa zasad dla usługi Security Center i stanowi podstawę rekomendacji Security Center [firmy .](/azure/security-center/security-center-recommendations) Definicje Azure Policy związane z tą kontrolką są włączane automatycznie przez Security Center. Alerty związane z tą kontrolą mogą [wymagać Azure Defender](/azure/security-center/azure-defender) planu dla powiązanych usług.

**Azure Policy wbudowane definicje — Microsoft.Network:**

[!INCLUDE [Resource Policy for Microsoft.Network 1.4](../../includes/policy/standards/asb/rp-controls/microsoft.network-1-4.md)]

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Zarządzanie ruchem do aplikacji internetowych

**Wskazówki:** Azure Web Application Firewall (WAF) to podstawowy składnik ochrony aplikacji internetowych platformy Azure. Użyj usługi Azure WAF, aby zapewnić scentralizowaną ochronę aplikacji internetowych przed powszechnymi lukami w zabezpieczeniach i lukami w zabezpieczeniach dzięki wstępnie skonfigurowanemu zarządzanemu grupowi reguł przed znanymi sygnaturami ataków z OWASP TOP 10 kategorii.

Dostosuj usługę Azure WAF przy użyciu reguł i grup reguł, aby dopasować je do wymagań aplikacji internetowej i wyeliminować wyniki fałszywie dodatnie. Skojarz zasady usługi Azure WAF dla każdej witryny za witryną WAF, aby umożliwić konfigurację specyficzną dla lokacji. Usługa Azure WAF obsługuje filtrowanie geograficzne, ograniczanie szybkości i reguły domyślnego zestawu reguł zarządzane przez platformę Azure. Reguły niestandardowe i można tworzyć zgodnie z potrzebami aplikacji. 

Skonfiguruj usługę Azure WAF do uruchamiania w trybie zapobiegania po ujednaniu ruchu sieciowego w trybie wykrywania przez określony czas. Usługa Azure WAF blokuje włamań i ataki wykryte przez reguły w trybie zapobiegania. Osoba atakująca otrzymuje wyjątek "403 brak autoryzacji dostępu" i połączenie jest zamykane. Tryb zapobiegania rejestruje takie ataki w dziennikach WAF.

- [Tryby WAF na Application Gateway](https://docs.microsoft.com/azure/web-application-firewall/ag/ag-overview#waf-modes)

- [Tryby WAF na Front Door](https://docs.microsoft.com/azure/web-application-firewall/afds/afds-overview#waf-modes)

- [Grupy reguł i reguły CRS zapory aplikacji internetowej](https://docs.microsoft.com/azure/web-application-firewall/ag/application-gateway-crs-rulegroups-rules?tabs=owasp31)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8. Zminimalizuj złożoność i koszty administracyjne związane z regułami zabezpieczeń sieci

**Wskazówki:** Tworzenie, kojarzenie i logiczne organizowanie zasobów w ramach subskrypcji platformy Azure z tagami do wykrywania typowych błędów konfiguracji aplikacji (na przykład Apache i IIS). 

Stosowanie reguł i grup reguł do Azure Web Application Firewall (WAF) na podstawie zastosowanych metadanych tagów.

- [Zasady WAF na Application Gateway](/cli/azure/network/application-gateway/waf-policy)

- [Zasady WAF na Front Door](/cli/azure/network/front-door/waf-policy)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="110-document-traffic-configuration-rules"></a>1.10. Udokumentuj reguły konfiguracji ruchu

**Wskazówki:** Użyj tagów dla sieciowych grup zabezpieczeń skojarzonych z Azure Web Application Firewall (WAF) w podsieci Azure Application Gateway, a także innych zasobów związanych z zabezpieczeniami sieci i przepływem ruchu. W przypadku poszczególnych reguł sieciowej grupy zabezpieczeń użyj pola "Opis", aby określić potrzebę biznesową, czas trwania i tak dalej dla wszystkich reguł, które zezwalają na ruch do lub z sieci.

Użyj dowolnej wbudowanej definicji usługi Azure Policy związanej z tagami, takiej jak "Wymagaj tagu i jego wartości", aby upewnić się, że wszystkie zasoby są tworzone przy użyciu tagów i powiadomić o istniejących nieotagowanych zasobach.

Wybierz Azure PowerShell interfejsu wiersza polecenia platformy Azure, aby szukać lub wykonywać akcje na zasobach na podstawie ich tagów.

- [Jak tworzyć tagi i ich używać](../azure-resource-manager/management/tag-resources.md)

- [Jak utworzyć Virtual Network](../virtual-network/quick-create-portal.md)

- [Tworzenie sieciowej grupy zabezpieczeń za pomocą konfiguracji zabezpieczeń](../virtual-network/tutorial-filter-network-traffic.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11. Używanie zautomatyzowanych narzędzi do monitorowania konfiguracji zasobów sieciowych i wykrywania zmian

**Wskazówki:** Użyj dziennika aktywności platformy Azure do monitorowania konfiguracji zasobów sieciowych i wykrywania zmian ustawień sieciowych i zasobów związanych z wdrożeniami Azure Web Application Firewall (WAF). Utwórz alerty w Azure Monitor, które będą wyzwalane w przypadku zmiany kluczowych ustawień sieciowych lub zasobów sieciowych.

- [Jak wyświetlać i pobierać zdarzenia dziennika aktywności platformy Azure](https://docs.microsoft.com/azure/azure-monitor/essentials/activity-log#view-the-activity-log)

- [Jak tworzyć alerty w Azure Monitor](../azure-monitor/alerts/alerts-activity-log.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

## <a name="logging-and-monitoring"></a>Rejestrowanie i monitorowanie

*Aby uzyskać więcej informacji, zobacz [Test porównawczy zabezpieczeń platformy Azure: rejestrowanie i monitorowanie.](../security/benchmarks/security-control-logging-monitoring.md)*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: Użyj zatwierdzonych źródeł synchronizacji czasu

**Wskazówki:** Utwórz regułę sieci dla usługi Azure Web Application Firewall (WAF), aby zezwolić na dostęp do serwera NTP przy użyciu odpowiedniego portu i protokołu, na przykład portu 123 za pośrednictwem protokołu UDP.

**Odpowiedzialność**: Współużytkowane

**Azure Security Center monitorowania:** Brak

### <a name="22-configure-central-security-log-management"></a>2.2. Konfigurowanie centralnego zarządzania dziennikami zabezpieczeń

**Wskazówki:** Konfigurowanie Azure Web Application Firewall (WAF) do wysłania do centralnego rozwiązania do zarządzania dziennikami zabezpieczeń, takiego jak Azure Sentinel, lub rozwiązania SIEM innej firmy. Dzienniki te obejmują dzienniki aktywności platformy Azure, diagnostyki i WAF w czasie rzeczywistym, które można następnie wyświetlać w różnych narzędziach, takich jak Azure Monitor, Excel i Power BI. Azure Web Application Firewall zapewniają wgląd w dane, które usługa Azure WAF ocenia, dopasowywa i blokuje.

Azure Sentinel ma wbudowany skoroszyt usługi Azure WAF, który zawiera omówienie zdarzeń zabezpieczeń w usłudze Azure WAF. Ten skoroszyt zawiera zdarzenia, dopasowane i zablokowane reguły oraz wszystkie inne elementy, które są rejestrowane w dziennikach zapory. Ta telemetria może służyć do rozpoczynania automatyzacji podręcznika w celu powiadamiania lub podejmowania akcji naprawczych na podstawie zdarzeń WAF zebranych przez Azure Sentinel.

- [Wyświetlanie dzienników aktywności](../azure-resource-manager/management/view-activity-logs.md)

- [Dzienniki diagnostyczne dla Application Gateway](../application-gateway/application-gateway-diagnostics.md)

- [Łączenie danych z zapory aplikacji internetowej firmy Microsoft z Azure Sentinel](../sentinel/connect-azure-waf.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3. Włączanie rejestrowania inspekcji dla zasobów platformy Azure

**Wskazówki:** Włączanie rejestrowania zasobów Azure Web Application Firewall (WAF) w celu uzyskania dostępu do dzienników inspekcji, zabezpieczeń i diagnostycznych. Azure Web Application Firewall udostępnia szczegółowe raporty dotyczące każdego z wykrytych zagrożeń, które są dostępne w skonfigurowanych dziennikach diagnostycznych. Dzienniki aktywności, które są automatycznie dostępne, obejmują źródło zdarzeń, datę, użytkownika, znacznik czasu, adresy źródłowe, adresy docelowe i inne przydatne elementy.

- [Omówienie rejestrowania](https://docs.microsoft.com/azure/web-application-firewall/ag/ag-overview#logging)

- [Azure Monitor zapytania dziennika](../azure-monitor/logs/log-query-overview.md)

- [Omówienie dzienników platformy Azure](../azure-monitor/essentials/platform-logs-overview.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="25-configure-security-log-storage-retention"></a>2.5. Konfigurowanie przechowywania magazynu dzienników zabezpieczeń

**Wskazówki:** wysyłanie dzienników Azure Web Application Firewall (WAF) do niestandardowego konta magazynu i definiowanie zasad przechowywania. Użyj Azure Monitor, aby ustawić okres przechowywania obszaru roboczego usługi Log Analytics na podstawie wymagań dotyczących zgodności w organizacji.
- [Konfigurowanie monitorowania dla konta magazynu](https://docs.microsoft.com/azure/storage/common/manage-storage-analytics-logs#configure-logging)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="26-monitor-and-review-logs"></a>2.6. Monitorowanie i przeglądanie dzienników

**Wskazówki:** Azure Web Application Firewall (WAF) zawiera szczegółowe raporty dotyczące każdego wykrytego zagrożenia. Rejestrowanie jest zintegrowane z Diagnostyka Azure, które zawierają rozbudowane informacje o operacjach i błędach, które są ważne w przypadku inspekcji i rozwiązywania problemów. 

Wystąpienia usługi Azure WAF są zintegrowane z Security Center do wysyłania alertów i informacji o kondycji na temat raportowania. Rekomendacje Security Center, aby wykrywać niechronione aplikacje internetowe i chronić te podatne na zagrożenia zasoby. 

Azure Sentinel ma wbudowany skoroszyt Zapora aplikacji internetowej — zdarzenia zapory, który zawiera omówienie zdarzeń zabezpieczeń w zaporze aplikacji internetowej. Obejmują one zdarzenia, dopasowane i zablokowane reguły oraz wszystkie inne zdarzenia rejestrowane w dziennikach zapory.

- [Jak włączyć ustawienia diagnostyczne dla dziennika aktywności platformy Azure](/azure/azure-monitor/platform/activity-log)

- [Jak włączyć ustawienia diagnostyczne dla Azure Application Gateway](../application-gateway/application-gateway-diagnostics.md)

- [Monitorowanie metryk i dzienników w Azure Front Door](../frontdoor/front-door-diagnostics.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7. Włącz alerty dla działań anomalii

**Wskazówki:** włączanie ustawień diagnostycznych dziennika aktywności platformy Azure, a także ustawień diagnostycznych dla aplikacji internetowej platformy Azure i wysyłanie dzienników do obszaru roboczego usługi Log Analytics. Wykonywanie zapytań w u usługi Log Analytics w celu wyszukiwania terminów, identyfikowania trendów, analizowania wzorców i zapewnienia wielu innych szczegółowych informacji na podstawie zebranych danych. Tworzenie alertów dotyczących anomalii w oparciu o metryki aplikacji sieci Szkieletowej. Jeśli na przykład zablokowana liczba żądań przekracza liczbę "X", wykonaj "Y".

- [Jak włączyć ustawienia diagnostyczne dla dziennika aktywności platformy Azure](/azure/azure-monitor/essentials/diagnostic-settings-legacy)

- [Jak tworzyć alerty na platformie Azure](../azure-monitor/alerts/tutorial-response.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="28-centralize-anti-malware-logging"></a>2.8. Scentralizowanie rejestrowania w poszukiwaniu złośliwego oprogramowania

**Wskazówki:** Wdrażanie Azure Web Application Firewall (WAF) przed krytycznymi aplikacjami internetowymi w celu dodatkowej inspekcji ruchu przychodzącego. 

Usługa Azure WAF zapewnia scentralizowaną ochronę aplikacji internetowych przed powszechnymi lukami w zabezpieczeniach i lukami w zabezpieczeniach oraz zabezpiecza aplikacje przez inspekcję przychodzącego ruchu internetowego w celu blokowania ataków, takich jak wstrzyknięcie kodu SQL, wykonywanie skryptów między witrynami, przekazywanie złośliwego oprogramowania i ataki DDoS.

- [Jak wdrożyć usługę Azure WAF](ag/create-waf-policy-ag.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

## <a name="identity-and-access-control"></a>Tożsamość i kontrola dostępu

*Aby uzyskać więcej informacji, zobacz [Test porównawczy zabezpieczeń platformy Azure: tożsamość i Access Control.](../security/benchmarks/security-control-identity-access-control.md)*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1. Utrzymywanie spisu kont administracyjnych

**Wskazówki:** Azure Active Directory (Azure AD) ma wbudowane role z możliwością wykonywania zapytań i muszą być jawnie przypisane. Moduł Azure AD PowerShell umożliwia wykonywanie zapytań ad hoc w celu odnajdywania kont, które są członkami grup administracyjnych.

- [Jak uzyskać rolę katalogu w usłudze Azure AD przy użyciu programu PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0&amp;preserve-view=true)

- [Jak uzyskać członków roli katalogu w usłudze Azure AD przy użyciu programu PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0&amp;preserve-view=true)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Używanie dedykowanych kont administracyjnych

**Wskazówki:** w ramach aplikacji WAF nie są dostępne przypisania administratora lokalnego. W środowisku mogą jednak Azure Active Directory (Azure AD), które umożliwiają kontrolę zarządzania zasobami usługi Azure WAF.
Dobrym rozwiązaniem jest utworzenie standardowych procedur operacyjnych dotyczących użycia dedykowanych kont administracyjnych, które mają dostęp do Azure Web Application Firewall (WAF). Użyj Security Center zarządzania tożsamościami i dostępem, aby monitorować liczbę kont administracyjnych.

- [Opis Azure Security Center tożsamości i dostępu](../security-center/security-center-identity-access.md)

- [Dowiedz się, jak tworzyć administratorów w Azure Database for PostgreSQL](https://docs.microsoft.com/azure/postgresql/howto-create-users#the-server-admin-account)

- [Jak używać Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Używaj uwierzytelniania wieloskładnikowego dla Azure Active Directory dostępu opartego na danych

**Wskazówki:** włącz Azure Active Directory wieloskładnikowego (Azure AD) i postępuj zgodnie Security Center z zaleceniami dotyczącymi zarządzania tożsamościami i dostępem.

- [Jak włączyć uwierzytelnianie wieloskładnikowe na platformie Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Jak monitorować tożsamość i dostęp w Azure Security Center](../security-center/security-center-identity-access.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6. Użyj dedykowanych maszyn (stacji roboczych z dostępem uprzywilejowanym) do wykonywania wszystkich zadań administracyjnych

**Wskazówki:** Użyj stacji roboczej z dostępem uprzywilejowanym (PAW) z uwierzytelnianiem wieloskładnikowym skonfigurowanym do logowania się i konfigurowania Azure Web Application Firewall (WAF) i powiązanych zasobów.

- [Dowiedz się więcej o stacjach roboczych z dostępem uprzywilejowanym](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Jak włączyć uwierzytelnianie wieloskładnikowe na platformie Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: Rejestrowanie i alerty dotyczące podejrzanych działań z kont administracyjnych

**Wskazówki:** raporty Azure Active Directory (Azure AD) dotyczące generowania dzienników i alertów w przypadku wystąpienia podejrzanej lub niebezpiecznej aktywności w środowisku. Użyj Security Center, aby monitorować działania tożsamości i dostępu.

- [Identyfikowanie użytkowników usługi Azure AD oflagowanych na skutek ryzykownego działania](../active-directory/identity-protection/overview-identity-protection.md)

- [Jak monitorować działania użytkowników związane z tożsamościami i dostępem w usłudze Azure Security Center](../security-center/security-center-identity-access.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Zarządzanie zasobami platformy Azure tylko z zatwierdzonych lokalizacji

**Wskazówki:** Konfigurowanie warunku lokalizacji zasad dostępu warunkowego i zarządzanie nazwanych lokalizacji.

Utwórz logiczne grupy zakresów adresów IP lub krajów i regionów z nazwanych lokalizacji. Ogranicz dostęp do poufnych zasobów, takich jak Azure Key Vault tajne, do skonfigurowanych nazwanych lokalizacji.

- [Jaki jest warunek lokalizacji w dostępie warunkowym Azure Active Directory (Azure AD)](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="39-use-azure-active-directory"></a>3.9: Użyj Azure Active Directory

**Wskazówki:** Użyj Azure Active Directory (Azure AD) jako centralnego systemu uwierzytelniania i autoryzacji. Usługa Azure AD chroni dane przy użyciu silnego szyfrowania danych magazynowanego i podczas ich przesyłania, a także insygnia, skróty i bezpiecznie przechowuje poświadczenia użytkownika.
- [Jak utworzyć i skonfigurować wystąpienie usługi Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Regularne przeglądanie i uzgadnianie dostępu użytkowników

**Wskazówki:** usługa Azure Active Directory (Azure AD) udostępnia dzienniki, które ułatwiają odnajdywanie nieaktywnego konta. Użyj przeglądów dostępu tożsamości platformy Azure, aby efektywnie zarządzać członkostwem w grupach, dostępem do aplikacji dla przedsiębiorstw i przypisań ról. Regularnie sprawdzaj dostęp użytkowników, aby upewnić się, że tylko aktywni użytkownicy nadal mają dostęp.

- [Informacje o raportowaniu w usłudze Azure AD](/azure/active-directory/reports-monitoring)

- [Jak używać przeglądów dostępu tożsamości platformy Azure](../active-directory/governance/access-reviews-overview.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Monitorowanie prób uzyskania dostępu do dezaktywowanych poświadczeń

**Wskazówki:** integracja Azure Active Directory źródłach dzienników aktywności logowania, inspekcji i zdarzeń o ryzyku (Azure AD) z dowolnym narzędziem SIEM lub narzędziem do monitorowania, takim jak Azure Sentinel.

Usprawnij ten proces, tworząc ustawienia diagnostyczne dla kont użytkowników usługi Azure AD i wysyłając dzienniki inspekcji i logowania do obszaru roboczego usługi Log Analytics. Skonfiguruj odpowiednie alerty w obszarze roboczym usługi Log Analytics.

- [Jak zintegrować dzienniki aktywności platformy Azure z usługą Azure Monitor](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12: Odchylenie zachowania alertu podczas logowania na koncie

**Wskazówki:** użyj Azure Active Directory usługi Azure AD (Azure AD) risk and Identity Protection, aby skonfigurować automatyczne odpowiedzi na wykryte podejrzane akcje związane z tożsamościami użytkowników. Pozysuj dane do Azure Sentinel celu dalszego badania.

- [Jak wyświetlić ryzykowne logowania w usłudze Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

- [Jak skonfigurować i włączyć zasady ryzyka usługi Identity Protection](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Jak do dołączać Azure Sentinel](../sentinel/quickstart-onboard.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

## <a name="data-protection"></a>Ochrona danych

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: ochrona danych](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1. Utrzymywanie spisu informacji poufnych

**Wskazówki:** używanie tagów do śledzenia Azure Web Application Firewall (WAF) i powiązanych zasobów, które przechowują lub przetwarzają poufne informacje.

- [Jak tworzyć tagi i ich używać](../azure-resource-manager/management/tag-resources.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Odizoluj systemy przechowujące lub przetwarzające informacje poufne

**Wskazówki:** Implementowanie izolacji przy użyciu oddzielnych subskrypcji i grup zarządzania dla poszczególnych domen zabezpieczeń, takich jak typ środowiska i poziom czułości danych, na przykład środowisk deweloperskich, testowych i produkcyjnych. 

Kontrola dostępu do zasobów platformy Azure za Azure Active Directory kontroli dostępu na podstawie ról (RBAC) usługi Azure AD.

- [Jak utworzyć dodatkowe subskrypcje platformy Azure](../cost-management-billing/manage/create-subscription.md)

- [Jak utworzyć Grupy zarządzania](../governance/management-groups/create-management-group-portal.md)

- [Jak tworzyć tagi i ich używać](../azure-resource-manager/management/tag-resources.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Szyfruj wszystkie poufne informacje podczas przesyłania

**Wskazówki:** Szyfruj wszystkie poufne informacje podczas przesyłania. Upewnij się, że każdy klient łączący się Azure Web Application Firewall wystąpieniami usługi (WAF) i powiązanymi zasobami może negocjować TLS 1.2 lub nowszą.

Postępuj Security Center dotyczące szyfrowania danych w spoczynku i szyfrowania podczas przesyłania, jeśli ma to zastosowanie.

- [Opis szyfrowania podczas przesyłania za pomocą platformy Azure](https://docs.microsoft.com/azure/security/fundamentals/encryption-overview#encryption-of-data-in-transit)

**Odpowiedzialność**: Współużytkowane

**Azure Security Center monitorowania:** Brak

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4.6. Zarządzanie dostępem do zasobów przy użyciu kontroli RBAC platformy Azure 

**Wskazówki:** Kontrolowanie dostępu do zasobów platformy Azure, takich Web Application Firewall pomocą kontroli dostępu na podstawie ról (RBAC) platformy Azure.

- [Jak skonfigurować RBAC platformy Azure na platformie Azure](../role-based-access-control/role-assignments-portal.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8. Szyfrowanie poufnych informacji w spoczynku

**Wskazówki:** Używanie szyfrowania danych magazynowych dla wszystkich zasobów platformy Azure, w tym Azure Web Application Firewall (WAF) i powiązanych zasobów. Firma Microsoft zaleca umożliwienie platformie Azure zarządzania kluczami szyfrowania, jednak w niektórych przypadkach istnieje możliwość zarządzania własnymi kluczami.

- [Informacje o szyfrowaniu danych magazynowanych na platformie Azure](../security/fundamentals/encryption-atrest.md)

- [Jak skonfigurować klucze szyfrowania zarządzane przez klienta](https://docs.microsoft.com/azure/storage/common/customer-managed-keys-configure-key-vault?tabs=portal)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9. Rejestrowanie i alerty dotyczące zmian w krytycznych zasobach platformy Azure

**Wskazówki:** skonfiguruj Azure Web Application Firewall (WAF) do uruchamiania w trybie zapobiegania po wywnioskaniu ruchu sieciowego w trybie wykrywania przez wstępnie określony czas. 

W trybie zapobiegania usługa Azure WAF blokuje włamań i ataki wykrywane przez reguły. Osoba atakująca otrzymuje wyjątek "403 brak autoryzacji dostępu" i połączenie jest zamykane. Tryb zapobiegania rejestruje takie ataki w dziennikach WAF.

- [Omówienie integracji między Application Gateway i Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-partner-integration#overview)

- [Tryby WAF na Application Gateway](https://docs.microsoft.com/azure/web-application-firewall/ag/ag-overview#waf-modes)

- [Tryby WAF na Front Door](https://docs.microsoft.com/azure/web-application-firewall/afds/afds-overview#waf-modes)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

## <a name="inventory-and-asset-management"></a>Zarządzanie magazynem i zasobami

*Aby uzyskać więcej informacji, zobacz [Test porównawczy zabezpieczeń platformy Azure: spis i zarządzanie zasobami.](../security/benchmarks/security-control-inventory-asset-management.md)*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1. Korzystanie z rozwiązania do automatycznego odnajdywania zasobów

**Wskazówki:** użyj Azure Resource Graph do wykonywania zapytań lub odnajdywania wszystkich zasobów, takich jak zasoby obliczeniowe, magazynowe, sieciowe, porty i protokoły itp. w ramach subskrypcji.

Upewnij się, że w dzierżawie są odpowiednie uprawnienia (odczyt) i wyliczenie wszystkich subskrypcji platformy Azure, a także zasobów w ramach twoich subskrypcji. Mimo że klasyczne zasoby platformy Azure można Resource Graph za pośrednictwem usługi , zdecydowanie zaleca się tworzenie i używanie Azure Resource Manager zasobów w przyszłości.

- [Jak tworzyć zapytania za pomocą Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

- [Jak wyświetlić subskrypcje platformy Azure](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-4.8.0&amp;preserve-view=true)

- [Opis kontroli RBAC na platformie Azure](../role-based-access-control/overview.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="62-maintain-asset-metadata"></a>6.2. Obsługa metadanych zasobów

**Wskazówki:** Używanie tagów w Azure Web Application Firewall (WAF). Tagi mogą być kojarzone z zasobami i stosowane logicznie w celu zorganizowania dostępu do tych zasobów w ramach subskrypcji klienta. 

- [Jak tworzyć tagi i ich używać](../azure-resource-manager/management/tag-resources.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Usuwanie nieautoryzowanych zasobów platformy Azure

**Wskazówki:** używaj tagowania, grup zarządzania i oddzielnych subskrypcji, jeśli jest to odpowiednie, do organizowania i śledzenia usługi Azure WAF i powiązanych zasobów. Regularnie uzgadniaj spis i upewnij się, że nieautoryzowane zasoby są usuwane z subskrypcji w terminowy sposób.

- [Jak utworzyć dodatkowe subskrypcje platformy Azure](../cost-management-billing/manage/create-subscription.md)

- [Jak utworzyć Grupy zarządzania](../governance/management-groups/create-management-group-portal.md)

- [Jak tworzyć tagi i ich używać](../azure-resource-manager/management/tag-resources.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4. Definiowanie i utrzymywanie spisu zatwierdzonych zasobów platformy Azure

**Wskazówki:** utwórz spis zatwierdzonych zasobów, w tym konfigurację, na podstawie potrzeb organizacji.

Użyj Azure Policy, aby wprowadzić ograniczenia dotyczące typu zasobów, które można utworzyć w subskrypcjach. Usługa Azure Resource Graph umożliwia wykonywanie zapytań dotyczących zasobów i odnajdywanie ich w ramach subskrypcji. Upewnij się, że wszystkie zasoby platformy Azure obecne w środowisku zostały zatwierdzone.

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

- [Jak tworzyć zapytania za pomocą Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Monitorowanie niezatwierdzonych zasobów platformy Azure

**Wskazówki:** użyj Azure Policy, aby wprowadzić ograniczenia dotyczące typu zasobów, które mogą być tworzone w twoich subskrypcjach.
Użyj Azure Resource Graph do wykonywania zapytań lub odnajdywania Azure Web Application Firewall (WAF) w ramach ich subskrypcji. Upewnij się, że wszystkie zasoby usługi Azure WAF i powiązane zasoby obecne w środowisku zostały zatwierdzone.

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

- [Jak tworzyć zapytania za pomocą usługi Azure Graph](../governance/resource-graph/first-query-portal.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7. Usuwanie niezatwierdzonych zasobów i aplikacji platformy Azure

**Wskazówki:** Monitorowanie i usuwanie niezatwierdzonych zasobów usługi Azure WAF za pomocą usługi Azure Policy w celu odmowy wdrożenia usługi Azure WAF lub określonego typu aplikacji internetowych, na przykład Azure WAF v1 a V2.

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="69-use-only-approved-azure-services"></a>6.9. Używaj tylko zatwierdzonych usług platformy Azure

**Wskazówki:** użyj Azure Policy, aby ograniczyć usługi, które można aprowizować w środowisku.

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

- [Jak odmówić określonego typu zasobu za pomocą Azure Policy](/azure/governance/policy/samples/built-in-policies#general)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Ograniczanie możliwości interakcji użytkowników z Azure Resource Manager

**Wskazówki:** Użyj dostępu warunkowego platformy Azure, aby ograniczyć użytkownikowi możliwość interakcji z usługą Azure Resources Manager, konfigurując opcję "Blokuj dostęp" dla aplikacji "Microsoft Azure Management".

- [Jak skonfigurować dostęp warunkowy, aby zablokować dostęp do usługi Azure Resources Manager](../role-based-access-control/conditional-access-azure-management.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Fizyczne lub logiczne rozdzielenie aplikacji wysokiego ryzyka

**Wskazówki:** Azure Web Application Firewall (WAF) można skojarzyć z różnymi środowiskami za pośrednictwem sieci, grup zasobów lub subskrypcji w celu segregowania aplikacji wysokiego ryzyka.

- [Omówienie usługi Azure Virtual Network](../virtual-network/virtual-networks-overview.md)

- [Organizowanie zasobów przy użyciu grup zarządzania platformy Azure](../governance/management-groups/overview.md)

- [Przewodnik po decyzjach związanych z subskrypcjami](/azure/cloud-adoption-framework/decision-guides/subscriptions/)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

## <a name="secure-configuration"></a>Bezpieczna konfiguracja

*Aby uzyskać więcej informacji, zobacz [Test porównawczy zabezpieczeń platformy Azure: bezpieczna konfiguracja.](../security/benchmarks/security-control-secure-configuration.md)*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1. Ustanów bezpieczne konfiguracje dla wszystkich zasobów platformy Azure

**Wskazówki:** Definiowanie i implementowanie standardowych konfiguracji zabezpieczeń dla ustawień sieciowych związanych Azure Web Application Firewall wdrożeniami aplikacji internetowej.

Użyj Azure Policy w przestrzeni nazw "Microsoft.Network", aby utworzyć niestandardowe zasady inspekcji lub wymuszania konfiguracji sieci usługi Azure Application Gateway, sieci wirtualnych, sieciowych grup zabezpieczeń i używania wbudowanych definicji zasad.

- [Jak wyświetlić dostępne Azure Policy aliasy](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-4.8.0&amp;preserve-view=true)

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Obsługa bezpiecznych konfiguracji zasobów platformy Azure

**Wskazówki:** użyj Azure Policy [odmów] i [wdoń, jeśli nie istnieje], aby wymusić bezpieczne ustawienia w Azure Web Application Firewall (WAF) i powiązanych zasobach. 

Użyj Azure Resource Manager, aby zachować konfigurację zabezpieczeń usługi Azure WAF i powiązanych zasobów wymaganych przez organizację.

- [Zrozumienie Azure Policy danych](../governance/policy/concepts/effects.md)

- [Tworzenie zasad i zarządzanie nimi w celu wymuszania zgodności](../governance/policy/tutorials/create-and-manage.md)

- [omówienie Azure Resource Manager szablonów](../azure-resource-manager/templates/overview.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Bezpieczne przechowywanie konfiguracji zasobów platformy Azure

**Wskazówki:** używaj Azure DevOps do bezpiecznego przechowywania kodu, takiego jak niestandardowe zasady platformy Azure i szablony szablonów Azure Resource Manager zarządzać nimi.

Przyznawanie lub odmawianie uprawnień określonym użytkownikom, wbudowanym grupom zabezpieczeń lub grupom zdefiniowanym w usłudze Azure Active Directory (Azure AD), jeśli są zintegrowane z usługą Azure DevOps lub Azure AD, jeśli są zintegrowane z programem Team Foundation Server (TFS).

- [Jak przechowywać kod w Azure DevOps](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops&amp;preserve-view=true)

- [Informacje o uprawnieniach i grupach w Azure DevOps](/azure/devops/organizations/security/about-permissions)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Wdrażanie narzędzi do zarządzania konfiguracją dla zasobów platformy Azure

**Wskazówki:** Użyj wbudowanych definicji Azure Policy, a także aliasów usługi Azure Policy w przestrzeni nazw "Microsoft.Network", aby utworzyć niestandardowe zasady służące do ostrzegania, inspekcji i wymuszania konfiguracji systemu. Opracowywanie procesu i potoku do zarządzania wyjątkami zasad.

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

- [Dokumentacja usługi Azure Policy](/azure/governance/policy)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Implementowanie zautomatyzowanego monitorowania konfiguracji dla zasobów platformy Azure

**Wskazówki:** Użyj wbudowanych definicji Azure Policy, a także aliasów usługi Azure Policy w przestrzeni nazw "Microsoft.Network", aby utworzyć niestandardowe zasady służące do ostrzegania, inspekcji i wymuszania konfiguracji systemu. 

Użyj Azure Policy [audit], [deny] i [deploy if not exist], aby automatycznie wymusić konfiguracje zasobów platformy Azure.

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

- [Dokumentacja usługi Azure Policy](/azure/governance/policy)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="711-manage-azure-secrets-securely"></a>7.11: Bezpieczne zarządzanie wpisami tajnymi platformy Azure

**Wskazówki:** używanie Azure Key Vault do bezpiecznego przechowywania certyfikatów. Azure Key Vault to zarządzany przez platformę magazyn wpisów tajnych, który umożliwia ochronę wpisów tajnych, kluczy i certyfikatów SSL. 

Azure Application Gateway obsługuje integrację z usługą Key Vault dla certyfikatów serwera dołączonych do odbiorników z włączoną obsługą protokołu HTTPS. 

- [How to configure SSL termination with Key Vault certificates by using Azure PowerShell](../application-gateway/configure-keyvault-ps.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Eliminowanie niezamierzonego narażenia na poświadczenia

**Wskazówki:** Zaim implementuj skaner poświadczeń w celu identyfikowania poświadczeń w kodzie, co będzie również zachęcało do przenoszenia odnalezionych poświadczeń do bezpieczniejszych lokalizacji, takich jak Azure Key Vault.
- [Jak skonfigurować skaner poświadczeń](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

## <a name="data-recovery"></a>Odzyskiwanie danych

*Aby uzyskać więcej informacji, zobacz [Test porównawczy zabezpieczeń platformy Azure: odzyskiwanie danych.](../security/benchmarks/security-control-data-recovery.md)*

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4. Zapewnianie ochrony kopii zapasowych i kluczy zarządzanych przez klienta

**Wskazówki:** Upewnij się, że dla aplikacji włączono usuwanie Azure Key Vault. Usuwanie nie soft umożliwia odzyskiwanie usuniętych magazynów kluczy i obiektów magazynu, takich jak klucze, wpisy tajne i certyfikaty.

- [Jak używać usuwania nie Azure Key Vault firmy](https://docs.microsoft.com/azure/key-vault/general/key-vault-recovery?tabs=azure-powershell&amp;preserve-view=true)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

## <a name="incident-response"></a>Reagowanie na zdarzenia

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: reagowanie na zdarzenia](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1. Tworzenie przewodnika po reagowaniu na zdarzenia

**Wskazówki:** Opracowywanie przewodnika reagowania na zdarzenia dla organizacji. Upewnij się, że istnieją zapisane plany reagowania na zdarzenia, które definiują wszystkie role personelu, a także fazy obsługi zdarzeń i zarządzania od wykrywania do przeglądu po zdarzeniu. 

- [Wskazówki dotyczące tworzenia własnego procesu reagowania na zdarzenia zabezpieczeń](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/) 

- [Centrum zabezpieczeń firmy Microsoft Anatomy of an Incident (Anatomia zdarzenia)](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/) 

- [Użyj przewodnika obsługi zdarzeń zabezpieczeń komputera NIST, aby pomóc w tworzeniu własnego planu reagowania na zdarzenia](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2. Tworzenie procedury oceniania i ustalania priorytetów zdarzeń

**Wskazówki:** Security Center przypisuje ważność do każdego alertu, aby ułatwić priorytetyzowanie alertów, które należy badać w pierwszej kolejności. Ważność jest oparta na tym, jak pewna Security Center znajduje się w znajdowaniu lub metryki użytej do wystawienia alertu, a także na poziomie ufności, że za działaniem, które doprowadziło do tego alertu, znajduje się złośliwe intencje.

Wyraźnie oznaczaj subskrypcje (na przykład produkcyjne lub nieprodukcyjne) i utwórz system nazewnictwa, aby jasno identyfikować i kategoryzować zasoby platformy Azure.

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="103-test-security-response-procedures"></a>10.3: Testowanie procedur reagowania na zabezpieczenia

**Wskazówki:** Przećwiczyj ćwiczenia, aby regularnie testować możliwości reagowania na zdarzenia w systemach. Zidentyfikuj słabe punkty i przerwy oraz popraw plan zgodnie z wymaganiami.
- [Zapoznaj się z publikacją NIST Guide to Test, Training, and Exercise Programs for IT Plans and Capabilities (Przewodnik po programach testowych, szkoleniowych i szkoleniowych dla planów i możliwości IT)](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4. Podaj szczegóły kontaktowe zdarzenia zabezpieczeń i skonfiguruj powiadomienia o alertach dla zdarzeń zabezpieczeń

**Wskazówki:** informacje kontaktowe dotyczące zdarzenia zabezpieczeń będą używane przez firmę Microsoft do kontaktowania się z Tobą, jeśli użytkownik usługi Centrum zabezpieczeń firmy Microsoft (MSRC) odkryje, że dostęp do danych klienta został uzyskany przez nieupoważnioną lub niezgodną z prawem stronę. Przejrzyj zdarzenia po fakcie, aby upewnić się, że problemy zostały rozwiązane.
- [Jak ustawić kontakt Azure Security Center zabezpieczeń](../security-center/security-center-provide-security-contact-details.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5. Dołączanie alertów zabezpieczeń do systemu reagowania na zdarzenia

**Wskazówki:** eksportowanie alertów Security Center i zaleceń przy użyciu funkcji eksportu ciągłego. Eksport ciągły umożliwia eksportowanie alertów i zaleceń ręcznie lub w sposób ciągły. Użyj łącznika Azure Security Center danych, aby przesyłać strumieniowo alerty do Azure Sentinel zgodnie z wymaganiami organizacji.

- [Jak skonfigurować eksport ciągły](../security-center/continuous-export.md)

- [Jak przesłać strumieniowo alerty do usługi Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatyzowanie odpowiedzi na alerty zabezpieczeń

**Wskazówki:** Użyj funkcji automatyzacji przepływu pracy w Security Center, aby automatycznie wyzwalać odpowiedzi za pośrednictwem Logic Apps alertów zabezpieczeń i zaleceń.
- [Jak skonfigurować automatyzację przepływu pracy i Logic Apps](../security-center/workflow-automation.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

## <a name="penetration-tests-and-red-team-exercises"></a>Testy penetracyjne i ćwiczenia typu „red team”

*Aby uzyskać więcej informacji, zobacz [test porównawczy zabezpieczeń platformy Azure: testy penetracyjne i ćwiczenia zespołu red team.](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Przeprowadzaj regularne testy penetrowania zasobów platformy Azure i zapewnij korygowanie wszystkich krytycznych ustaleń zabezpieczeń

**Wskazówki:** postępuj zgodnie z regułami zaangażowania firmy Microsoft, aby upewnić się, że testy penetracyjne nie naruszały zasad firmy Microsoft. Użyj strategii firmy Microsoft i wykonania czerwonego zespołu oraz testów penetracyjnych na żywo w chmurze, które są zarządzane przez firmę Microsoft w zakresie infrastruktury, usług i aplikacji w chmurze. 

- [Reguły testów penetracyjnych zaangażowania](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Testy typu „red team” w chmurze firmy Microsoft](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Odpowiedzialność**: Współużytkowane

**Azure Security Center monitorowania:** Brak

## <a name="next-steps"></a>Następne kroki

- Zobacz [Omówienie testu porównawczego zabezpieczeń platformy Azure w wersji 2](/azure/security/benchmarks/overview)
- Dowiedz się więcej o [punktach odniesienia zabezpieczeń platformy Azure](/azure/security/benchmarks/security-baselines-overview)
