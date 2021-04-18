---
title: Punkt odniesienia zabezpieczeń platformy Azure dla Azure Monitor
description: Punkt Azure Monitor zabezpieczeń zawiera wskazówki proceduralne i zasoby dotyczące wdrażania zaleceń dotyczących zabezpieczeń określonych w te testach porównawczych zabezpieczeń platformy Azure.
author: msmbaldwin
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 03/30/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: f002c7196b864d4a04beda1124d0519af612b716
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2021
ms.locfileid: "107600237"
---
# <a name="azure-security-baseline-for-azure-monitor"></a>Punkt odniesienia zabezpieczeń platformy Azure dla Azure Monitor

Ten punkt odniesienia zabezpieczeń stosuje wskazówki z testu porównawczego zabezpieczeń platformy [Azure w wersji 1.0](../security/benchmarks/overview-v1.md) do Azure Monitor. Test porównawczy zabezpieczeń platformy Azure zawiera zalecenia dotyczące sposobu zabezpieczania rozwiązań w chmurze na platformie Azure. Zawartość jest pogrupowana według **kontrolek zabezpieczeń zdefiniowanych** przez test porównawczy zabezpieczeń platformy Azure i powiązanych wskazówek dotyczących Azure Monitor. **Wykluczono** mechanizmy kontroli Azure Monitor których odpowiedzialnością jest firma Microsoft.

Aby zobaczyć, Azure Monitor mapuje całkowicie na test porównawczy zabezpieczeń platformy Azure, zobacz pełny Azure Monitor [mapowania punktów odniesienia zabezpieczeń.](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)

## <a name="network-security"></a>Bezpieczeństwo sieci

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: bezpieczeństwo sieci](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1. Ochrona zasobów platformy Azure w sieciach wirtualnych

**Wskazówki:** Włącz usługę Azure Private Link, aby zezwolić na dostęp do usług Azure SaaS (na przykład Azure Monitor) i usług klienta/partnera hostowanych na platformie Azure za pośrednictwem prywatnego punktu końcowego w sieci wirtualnej. Ruch między siecią wirtualną a usługą odbywa się za pośrednictwem sieci szkieletowej firmy Microsoft, eliminując ekspozycję z publicznego Internetu.

Aby zezwolić na ruch Azure Monitor, użyj tagów usługi "AzureMonitor", aby zezwolić na ruch przychodzący i wychodzący przez sieciowe grupy zabezpieczeń. Aby zezwolić na ruch testowy monitorowania dostępności do Azure Monitor, użyj tagu usługi "ApplicationInsightsAvailability" do całego ruchu przychodzącego za pośrednictwem sieciowych grup zabezpieczeń. Aby zezwolić na dostęp powiadomień o alertach do punktów końcowych klienta, użyj tagu usługi "ActionGroup", aby zezwolić na ruch przychodzący przez sieciowe grupy zabezpieczeń.

Reguły sieci wirtualnej Azure Monitor akceptować tylko komunikację, która jest wysyłana z wybranych podsieci w sieci wirtualnej.

Użyj bramy usługi Log Analytics, aby wysyłać dane do obszaru roboczego usługi Log Analytics w programie Azure Monitor w imieniu komputerów, które nie mogą bezpośrednio łączyć się z Internetem, uniemożliwiając komputerom połączenie z Internetem. 

- [Jak skonfigurować Private Link dla Azure Monitor](/azure/azure-monitor/platform/private-link-security)

- [Łączenie komputerów bez dostępu do Internetu przy użyciu bramy usługi Log Analytics w Azure Monitor](/azure/azure-monitor/platform/gateway)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2: Monitorowanie i rejestrowanie konfiguracji i ruchu sieci wirtualnych, podsieci i interfejsów sieciowych

**Wskazówki:** Azure Monitor jest usługą podstawową i nie obsługuje wdrażania bezpośrednio w sieci wirtualnej, a jej podstawowa infrastruktura jest obsługiwane przez firmę Microsoft. Jednak w przypadku zasobów, które nakierują połączenia sieciowe z Azure Monitor ofertą, należy zabezpieczyć sieć przy użyciu sieciowej grupy zabezpieczeń. Włącz dzienniki przepływu sieciowej grupy zabezpieczeń i wysyłaj dzienniki na konto magazynu w celu inspekcji ruchu. Możesz również wysyłać dzienniki przepływu do obszaru roboczego usługi Log Analytics i używać Analiza ruchu, aby zapewnić wgląd w przepływ ruchu w chmurze platformy Azure. Niektóre zalety usługi Analiza ruchu to możliwość wizualizacji aktywności sieciowej i identyfikowania punktów aktywności, identyfikowania zagrożeń bezpieczeństwa, zrozumienia wzorców przepływu ruchu i identyfikowania błędnych konfiguracji sieci.

W przypadku Azure Monitor z Private Link dostęp do rejestrowania sieci, takiego jak "Dane przetwarzane przez prywatny punkt końcowy (IN/OUT)".

- [Wymagania dotyczące sieci dla Azure Monitor agentów](/azure/azure-monitor/platform/log-analytics-agent#network-requirements)

- [Łączenie komputerów bez dostępu do Internetu przy użyciu bramy usługi Log Analytics w Azure Monitor](/azure/azure-monitor/platform/gateway)

- [Jak włączyć dzienniki przepływu sieciowej grupy zabezpieczeń](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Jak włączyć i używać Analiza ruchu](../network-watcher/traffic-analytics.md)

- [Informacje o zabezpieczeniach sieciowych zapewnianych przez Azure Security Center](../security-center/security-center-network-recommendations.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimalizowanie złożoności i obciążeń administracyjnych reguł zabezpieczeń sieci

**Wskazówki:** Aby zezwolić na ruch do Azure Monitor, użyj tagów usługi "AzureMonitor", aby zezwolić na ruch przychodzący i wychodzący przez sieciowe grupy zabezpieczeń. Aby zezwolić na ruch testowy monitorowania dostępności do Azure Monitor, użyj tagu usługi "ApplicationInsightsAvailability" do całego ruchu przychodzącego za pośrednictwem sieciowych grup zabezpieczeń. Firma Microsoft zarządza prefiksami adresów obejmującymi tag usługi i automatycznie aktualizuje tag usługi w przypadku zmiany adresów.

- [Opis tagów usługi i korzystanie z nich](../virtual-network/service-tags-overview.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="110-document-traffic-configuration-rules"></a>1.10: Udokumentowanie reguł konfiguracji ruchu

**Wskazówki:** Azure Monitor jest częścią podstawowych usług platformy Azure i nie można go wdrożyć jako usługi oddzielnie. Azure Monitor zasoby mogą zostać wdrożone wraz z zasobami, w tym agent usługi Azure Monitor i zestaw SDK usługi Application Insights, co może mieć wpływ na poziom zabezpieczeń tych zasobów.

- [Wymagania dotyczące sieci dla Azure Monitor agentów](/azure/azure-monitor/platform/log-analytics-agent#network-requirements)

- [Łączenie komputerów bez dostępu do Internetu przy użyciu bramy usługi Log Analytics w Azure Monitor](/azure/azure-monitor/platform/gateway) 

- [Zobacz wprowadzenie do Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview#get-started)

- [Jak skonfigurować testy sieci Web dostępności](app/monitor-web-app-availability.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11. Używanie zautomatyzowanych narzędzi do monitorowania konfiguracji zasobów sieciowych i wykrywania zmian

**Wskazówki:** Użyj dziennika aktywności platformy Azure do monitorowania konfiguracji zasobów i wykrywania zmian zasobów sieciowych związanych z Azure Monitor. Utwórz alerty w Azure Monitor, które będą wyzwalane w przypadku zmiany tych krytycznych zasobów sieciowych.

- [Jak wyświetlać i pobierać zdarzenia dziennika aktywności platformy Azure](/azure/azure-monitor/platform/activity-log#view-the-activity-log)

- [Jak tworzyć alerty w Azure Monitor](/azure/azure-monitor/platform/alerts-activity-log)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

## <a name="logging-and-monitoring"></a>Rejestrowanie i monitorowanie

*Aby uzyskać więcej informacji, zobacz [Test porównawczy zabezpieczeń platformy Azure: rejestrowanie i monitorowanie.](../security/benchmarks/security-control-logging-monitoring.md)*

### <a name="22-configure-central-security-log-management"></a>2.2. Konfigurowanie centralnego zarządzania dziennikami zabezpieczeń

**Wskazówki:** Azure Monitor dzienniki aktywności są używane do rejestrować zmiany w jego zasobach. Te dzienniki można wyeksportować do usługi Azure Storage, Centrum zdarzeń lub obszaru roboczego usługi Log Analytics. Pozyskaj dzienniki za pośrednictwem Azure Monitor, aby agregować dane zabezpieczeń generowane przez urządzenia punktu końcowego, zasoby sieciowe i inne systemy zabezpieczeń. W Azure Monitor można wykonywać zapytania dotyczące danych i wykonywać względem nich analizy, a także używać kont usługi Azure Storage do przechowywania dzienników w perspektywie długoterminowej/archiwalnej.

Alternatywnie można włączyć i dołączyć dane do Azure Sentinel lub rozwiązania SIEM innej firmy.

- [Jak zbierać dzienniki platformy i metryki za pomocą Azure Monitor](/azure/azure-monitor/platform/diagnostic-settings)

- [Jak zbierać dzienniki hostów wewnętrznych maszyny wirtualnej platformy Azure za pomocą Azure Monitor](/azure/azure-monitor/learn/quick-collect-azurevm)

- [Jak do dołączać Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Jak rozpocząć pracę z Azure Monitor integracją rozwiązania SIEM i rozwiązania SIEM innej firmy](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Test [porównawczy zabezpieczeń](/azure/governance/policy/samples/azure-security-benchmark) platformy Azure jest domyślną inicjatywą zasad dla usługi Security Center i stanowi podstawę Security Center [rekomendacji firmy](/azure/security-center/security-center-recommendations). Definicje Azure Policy związane z tą kontrolką są włączane automatycznie przez Security Center. Alerty związane z tą kontrolą mogą [wymagać Azure Defender](/azure/security-center/azure-defender) planu dla powiązanych usług.

**Azure Policy wbudowane definicje — microsoft.insights:**

[!INCLUDE [Resource Policy for microsoft.insights 2.2](../../includes/policy/standards/asb/rp-controls/microsoft.insights-2-2.md)]

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3. Włączanie rejestrowania inspekcji dla zasobów platformy Azure

**Wskazówki:** Azure Monitor dzienniki aktywności, dziennik aktywności jest automatycznie włączany i rejestruje operacje wykonywane na zasobach usługi Azure Monitor, takie jak: kto uruchomił operację, kiedy wystąpiła operacja, stan operacji i inne przydatne informacje inspekcji. 

- [Jak zbierać dzienniki platformy i metryki za pomocą Azure Monitor](/azure/azure-monitor/platform/diagnostic-settings)

- [Informacje o rejestrowaniu i różnych typach dzienników na platformie Azure](/azure/azure-monitor/platform/platform-logs-overview)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Test [porównawczy zabezpieczeń](/azure/governance/policy/samples/azure-security-benchmark) platformy Azure jest domyślną inicjatywą zasad dla usługi Security Center i stanowi podstawę Security Center [rekomendacji firmy](/azure/security-center/security-center-recommendations). Definicje Azure Policy związane z tą kontrolką są włączane automatycznie przez Security Center. Alerty związane z tą kontrolą mogą [wymagać Azure Defender](/azure/security-center/azure-defender) planu dla powiązanych usług.

**Azure Policy wbudowane definicje — microsoft.insights**:

[!INCLUDE [Resource Policy for microsoft.insights 2.3](../../includes/policy/standards/asb/rp-controls/microsoft.insights-2-3.md)]

### <a name="25-configure-security-log-storage-retention"></a>2.5. Konfigurowanie przechowywania magazynu dzienników zabezpieczeń

**Wskazówki:** w Azure Monitor ustaw okres przechowywania obszaru roboczego usługi Log Analytics zgodnie z przepisami organizacji dotyczącymi zgodności. Użyj kont usługi Azure Storage na potrzeby dowolnego długoterminowego/archiwalnej pamięci masowej dzienników.

- [Zmiana okresu przechowywania danych w uciekierach usługi Log Analytics](/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

- [Jak skonfigurować zasady przechowywania dla dzienników konta usługi Azure Storage](/azure/storage/common/storage-monitor-storage-account#configure-logging)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="26-monitor-and-review-logs"></a>2.6. Monitorowanie i przeglądanie dzienników

**Wskazówki:** analizowanie i monitorowanie dzienników pod celu zachowania anomalii oraz regularne przeglądanie wyników. Użyj Azure Monitor i obszaru roboczego usługi Log Analytics, aby przeglądać dzienniki i wykonywać zapytania dotyczące danych dziennika.

Alternatywnie możesz włączyć i dołączyć dane do Azure Sentinel lub rozwiązania SIEM innej firmy.

- [Jak do dołączać Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Wprowadzenie do zapytań usługi Log Analytics](/azure/azure-monitor/log-query/log-analytics-tutorial)

- [Jak wykonywać zapytania niestandardowe w Azure Monitor](/azure/azure-monitor/log-query/get-started-queries)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7. Włącz alerty dla działań anomalii

**Wskazówki:** monitorowanie Azure Security Center obszaru roboczego usługi Log Analytics i alerty dotyczące anomalii działań znalezionych w dziennikach zabezpieczeń i zdarzeniach. Alternatywnie możesz włączyć i włączyć dane, aby Azure Sentinel.

- [Jak do dołączać Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Jak zarządzać alertami w Azure Security Center](../security-center/security-center-managing-and-responding-alerts.md)

- [Jak rejestrować alerty dotyczące danych dzienników analizy dzienników](/azure/azure-monitor/learn/tutorial-response)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

## <a name="identity-and-access-control"></a>Tożsamość i kontrola dostępu

*Aby uzyskać więcej informacji, zobacz [Test porównawczy zabezpieczeń platformy Azure: tożsamość i Access Control.](../security/benchmarks/security-control-identity-access-control.md)*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1. Utrzymywanie spisu kont administracyjnych

**Wskazówki:** Kontrola dostępu na podstawie ról (RBAC) platformy Azure umożliwia zarządzanie dostępem do zasobów platformy Azure za pomocą przypisań ról. Te role można przypisać do użytkowników, grupować jednostki usługi i tożsamości zarządzane. Dla niektórych zasobów istnieją wstępnie zdefiniowane wbudowane role i te role można zinwentaryzować lub wykonywać względem nich zapytania za pomocą narzędzi takich jak interfejs wiersza polecenia platformy Azure, program Azure PowerShell lub witryna Azure Portal.

- [Jak uzyskać rolę katalogu w usłudze Azure Active Directory (Azure AD) przy użyciu programu PowerShell](/powershell/module/azuread/get-azureaddirectoryrole)

- [Jak uzyskać członków roli katalogu w usłudze Azure AD przy użyciu programu PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Użyj dedykowanych kont administracyjnych

**Wskazówki:** Tworzenie standardowych procedur operacyjnych dotyczących korzystania z dedykowanych kont administracyjnych. Użyj Azure Security Center zarządzania tożsamościami i dostępem, aby monitorować liczbę kont administracyjnych.

Dostęp Just-In-Time/Just-Enough-Access można również włączyć przy użyciu usługi Azure Active Directory (Azure AD) Privileged Identity Management Privileged Roles for Microsoft Services i Azure Resource Manager.

- [Azure AD Privileged Identity Management omówienie](../active-directory/privileged-identity-management/pim-configure.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4: Użyj Azure Active Directory jednokrotnego (SSO)

**Wskazówki:** wszędzie tam, gdzie to możliwe, używaj logowania jednokrotnego usługi Azure Active Directory (Azure AD) zamiast konfigurowania pojedynczych poświadczeń autonomicznych dla usługi. Użyj Azure Security Center zaleceń dotyczących zarządzania tożsamościami i dostępem.

- [Understand SSO with Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Używaj uwierzytelniania wieloskładnikowego dla Azure Active Directory dostępu opartego na danych

**Wskazówki:** Włącz uwierzytelnianie wieloskładnikowe usługi Azure Active Directory (Azure AD) i postępuj zgodnie Azure Security Center zaleceniami dotyczącymi tożsamości i dostępu.

- [Jak włączyć uwierzytelnianie wieloskładnikowe na platformie Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Jak monitorować tożsamość i dostęp w Azure Security Center](../security-center/security-center-identity-access.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6. Użyj dedykowanych maszyn (stacji roboczych z dostępem uprzywilejowanym) do wykonywania wszystkich zadań administracyjnych

**Wskazówki:** Użyj bezpiecznej stacji roboczej zarządzanej przez platformę Azure (nazywanej również stacją roboczą z dostępem uprzywilejowanym lub stacją roboczą z dostępem uprzywilejowanym) do wykonywania zadań administracyjnych wymagających podniesionych uprawnień.

- [Opis bezpiecznych stacji roboczych zarządzanych przez platformę Azure](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Jak włączyć uwierzytelnianie wieloskładnikowe Azure Active Directory (Azure AD)](../active-directory/authentication/howto-mfa-getstarted.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: Rejestrowanie i alerty dotyczące podejrzanych działań z kont administracyjnych

**Wskazówki:** użyj Azure Active Directory zabezpieczeń i monitorowania usługi Azure AD, aby wykryć, kiedy w środowisku wystąpi podejrzane lub niebezpieczne działanie. Użyj Azure Security Center, aby monitorować działania tożsamości i dostępu.

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

**Wskazówki:** Użyj Azure Active Directory (Azure AD) jako centralnego systemu uwierzytelniania i autoryzacji. Usługa Azure AD chroni dane przy użyciu silnego szyfrowania danych magazynowych i podczas przesyłania. Usługa Azure AD dodaje również iniekty, skróty i bezpiecznie przechowuje poświadczenia użytkownika.

- [Jak utworzyć i skonfigurować wystąpienie usługi Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Regularne przeglądanie i uzgadnianie dostępu użytkowników

**Wskazówki:** Azure Active Directory (Azure AD) zawiera dzienniki, które ułatwiają odnajdywanie nieaktualnych kont. Ponadto usługa Azure Identity Access Reviews umożliwia efektywne zarządzanie członkostwem w grupach, dostępem do aplikacji dla przedsiębiorstw i przypisań ról. Dostęp użytkowników można regularnie przeglądać, aby upewnić się, że tylko odpowiedni użytkownicy mają dalszy dostęp.

- [Opis raportowania w usłudze Azure AD](/azure/active-directory/reports-monitoring/)

- [Jak używać przeglądów dostępu do tożsamości platformy Azure](../active-directory/governance/access-reviews-overview.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Monitorowanie prób uzyskania dostępu do dezaktywowanych poświadczeń

**Wskazówki:** Masz dostęp do źródeł dzienników aktywności logowania, inspekcji i ryzyka Azure Active Directory (Azure AD), które umożliwiają integrację z dowolnym narzędziem SIEM/Monitoring. Ten proces można uprościć, tworząc ustawienia diagnostyczne dla kont użytkowników usługi Azure AD i wysyłając dzienniki inspekcji i dzienniki logowania do obszaru roboczego usługi Log Analytics. Żądane alerty można skonfigurować w obszarze roboczym usługi Log Analytics.

- [Jak zintegrować dzienniki aktywności platformy Azure z usługą Azure Monitor](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12: Odchylenie zachowania alertu podczas logowania na koncie

**Wskazówki:** korzystanie Azure Active Directory (Azure AD) Risk and Identity Protection w celu konfigurowania automatycznych odpowiedzi na wykryte podejrzane akcje związane z tożsamościami użytkowników. Możesz również pozysać dane do Azure Sentinel w celu dalszego badania.

- [Jak wyświetlić ryzykowne logowania w usłudze Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

- [Jak skonfigurować i włączyć zasady ryzyka związane z usługą Identity Protection](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Jak do dołączać Azure Sentinel](../sentinel/quickstart-onboard.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

## <a name="data-protection"></a>Ochrona danych

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: ochrona danych](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1. Utrzymywanie spisu informacji poufnych

**Wskazówki:** używaj tagów, jeśli to możliwe, aby pomóc w śledzeniu Azure Monitor zasobów, które przechowują lub przetwarzają poufne informacje, takie jak obszary robocze usługi Log Analytics.

- [Jak tworzyć tagi i ich używać](../azure-resource-manager/management/tag-resources.md)

- [Zarządzanie dostępem do danych dziennika i obszarami roboczymi w usłudze Azure Monitor](/azure/azure-monitor/platform/manage-access)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Izolowanie systemów przechowujące lub przetwarzające informacje poufne

**Wskazówki:** Implementowanie izolacji przy użyciu oddzielnych subskrypcji i grup zarządzania dla poszczególnych domen zabezpieczeń, takich jak typ środowiska i poziom czułości danych. Możesz ograniczyć poziom dostępu do zasobów Azure Monitor i powiązanych zasobów, których wymagają aplikacje i środowiska przedsiębiorstwa. Dostęp do aplikacji można kontrolować Azure Monitor kontroli dostępu na podstawie ról (RBAC) platformy Azure.

- [Jak utworzyć dodatkowe subskrypcje platformy Azure](../cost-management-billing/manage/create-subscription.md)

- [Jak tworzyć grupy zarządzania](../governance/management-groups/create-management-group-portal.md)

- [Jak tworzyć tagi i ich używać](../azure-resource-manager/management/tag-resources.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4. Szyfrowanie wszystkich informacji poufnych podczas przesyłania

**Wskazówki:** Azure Monitor domyślnie negocjuje standard TLS 1.2. Upewnij się, że każdy klient łączący się z zasobami platformy Azure może negocjować TLS 1.2 lub nowszą. 

Application Insights i Log Analytics nadal zezwalają na pozysłów danych TLS 1.1 i TLS 1.0. Dane mogą być ograniczone do TLS 1.2 przez skonfigurowanie po stronie klienta.

- [Jak bezpiecznie wysyłać dane przy użyciu TLS 1.2](/azure/azure-monitor/platform/data-security#sending-data-securely-using-tls-12)

**Odpowiedzialność**: Współużytkowane

**Azure Security Center monitorowania:** Brak

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5. Identyfikowanie poufnych danych za pomocą aktywnego narzędzia odnajdywania

**Wskazówki:** Funkcje identyfikacji, klasyfikacji i zapobiegania utracie danych nie są jeszcze dostępne dla Azure Monitor. W razie potrzeby zaim implementuj rozwiązanie innej firmy na potrzeby zgodności.
W przypadku podstawowej platformy zarządzanej przez firmę Microsoft firma Microsoft traktuje całą zawartość klientów jako poufne i bardzo długo chroni przed utratą i narażeniem danych klientów. Aby zapewnić bezpieczeństwo danych klientów na platformie Azure, firma Microsoft wdrożyła i utrzymuje pakiet niezawodnych funkcji i kontroli ochrony danych.

- [Informacje na temat ochrony danych klientów na platformie Azure](../security/fundamentals/protection-customer-data.md)

**Odpowiedzialność**: Współużytkowane

**Azure Security Center monitorowania:** Brak

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4.6. Kontrola dostępu do zasobów przy użyciu kontroli dostępu opartej na rolach

**Wskazówki:** Użyj kontroli dostępu opartej na rolach (RBAC) platformy Azure, aby zarządzać dostępem do Azure Monitor.

- [Role, uprawnienia i zabezpieczenia w Azure Monitor](/azure/azure-monitor/platform/roles-permissions-security)

- [Jak skonfigurować RBAC platformy Azure](../role-based-access-control/role-assignments-portal.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8. Szyfrowanie poufnych informacji w spoczynku

**Wskazówki:** Azure Monitor, że wszystkie dane i zapisane zapytania są szyfrowane podczas przechowywania przy użyciu kluczy zarządzanych przez firmę Microsoft (MMK). Azure Monitor udostępnia również opcję szyfrowania przy użyciu własnego klucza, który jest przechowywany w układzie Azure Key Vault i dostępny za pośrednictwem magazynu przy użyciu przypisanego przez system uwierzytelniania tożsamości zarządzanej. Ten klucz zarządzany przez klienta (CMK) może być chroniony przez oprogramowanie lub sprzętowy moduł HSM.

- [Azure Monitor kluczy zarządzanych przez klienta](/azure/azure-monitor/platform/customer-managed-keys)

- [Zabezpieczenia danych usługi Log Analytics](/azure/azure-monitor/platform/data-security)

- [Zbieranie, przechowywanie i magazynowanie danych w usłudze Application Insights](app/data-retention-privacy.md)

- [Informacje o szyfrowaniu danych magazynowanych na platformie Azure](../security/fundamentals/encryption-atrest.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9. Rejestrowanie i alerty dotyczące zmian w krytycznych zasobach platformy Azure

**Wskazówki:** użyj Azure Monitor z dziennika aktywności platformy Azure, aby tworzyć alerty dotyczące sytuacji, w których zmiany mają Azure Monitor i powiązane zasoby.

- [Jak tworzyć alerty dla zdarzeń dziennika aktywności platformy Azure](/azure/azure-monitor/platform/alerts-activity-log)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Test [porównawczy zabezpieczeń](/azure/governance/policy/samples/azure-security-benchmark) platformy Azure jest domyślną inicjatywą zasad dla usługi Security Center i stanowi podstawę Security Center [rekomendacji firmy](/azure/security-center/security-center-recommendations). Definicje Azure Policy związane z tą kontrolką są włączane automatycznie przez Security Center. Alerty związane z tą kontrolą mogą [wymagać Azure Defender](/azure/security-center/azure-defender) planu dla powiązanych usług.

**Azure Policy wbudowane definicje — microsoft.insights:**

[!INCLUDE [Resource Policy for microsoft.insights 4.9](../../includes/policy/standards/asb/rp-controls/microsoft.insights-4-9.md)]

## <a name="vulnerability-management"></a>Zarządzanie lukami w zabezpieczeniach

*Aby uzyskać więcej informacji, zobacz [Test porównawczy zabezpieczeń platformy Azure: zarządzanie lukami w zabezpieczeniach.](../security/benchmarks/security-control-vulnerability-management.md)*

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5. Określanie priorytetów korygowania wykrytych luk w zabezpieczeniach za pomocą procesu oceny ryzyka

**Wskazówki:** użyj wspólnego programu oceniania ryzyka (na przykład Common Vulnerability Scoring System) lub domyślnych ocen ryzyka dostarczonych przez narzędzie do skanowania innych firm.

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

## <a name="inventory-and-asset-management"></a>Zarządzanie magazynem i zasobami

*Aby uzyskać więcej informacji, zobacz [Test porównawczy zabezpieczeń platformy Azure: spis i zarządzanie zasobami.](../security/benchmarks/security-control-inventory-asset-management.md)*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1. Korzystanie z rozwiązania do automatycznego odnajdywania zasobów

**Wskazówki:** używanie interfejsu wiersza polecenia platformy Azure do wykonywania zapytań Azure Monitor zasobów w ramach subskrypcji. Upewnij się, że w dzierżawie są odpowiednie uprawnienia (odczyt) i wyliczenie wszystkich subskrypcji platformy Azure, a także zasobów w ramach twoich subskrypcji.

- [Azure Monitor wiersza polecenia](https://docs.microsoft.com/cli/azure/monitor?view=azure-cli-latest&amp;preserve-view=true)

- [Jak wyświetlić subskrypcje platformy Azure](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-4.8.0&amp;preserve-view=true)

- [Opis kontroli RBAC na platformie Azure](../role-based-access-control/overview.md)

- [Role, uprawnienia i zabezpieczenia w Azure Monitor](/azure/azure-monitor/platform/roles-permissions-security)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="62-maintain-asset-metadata"></a>6.2. Obsługa metadanych zasobów

**Wskazówki:** Stosowanie tagów do Azure Monitor zasobów, które zawierają metadane w celu logicznego zorganizowania ich w taksonomię.

- [Jak tworzyć tagi i ich używać](../azure-resource-manager/management/tag-resources.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="63-delete-unauthorized-azure-resources"></a>6.3. Usuwanie nieautoryzowanych zasobów platformy Azure

**Wskazówki:** Używaj tagowania, grup zarządzania i oddzielnych subskrypcji, jeśli jest to konieczne, do organizowania i śledzenia Azure Monitor powiązanych zasobów. Regularnie uzgadniaj spis i upewnij się, że nieautoryzowane zasoby zostaną usunięte z subskrypcji w terminowy sposób.

- [Jak utworzyć dodatkowe subskrypcje platformy Azure](../cost-management-billing/manage/create-subscription.md)

- [Jak tworzyć grupy zarządzania](../governance/management-groups/create-management-group-portal.md)

- [Jak tworzyć tagi i ich używać](../azure-resource-manager/management/tag-resources.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4. Definiowanie i utrzymywanie spisu zatwierdzonych zasobów platformy Azure

**Wskazówki:** Utwórz spis zatwierdzonych zasobów platformy Azure i zatwierdzonego oprogramowania dla zasobów obliczeniowych zgodnie z potrzebami organizacji.

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Monitorowanie niezatwierdzonych zasobów platformy Azure

**Wskazówki:** użyj Azure Policy, aby wprowadzić ograniczenia dotyczące typu zasobów, które mogą być tworzone w twoich subskrypcjach.

Usługa Azure Resource Graph umożliwia wykonywanie zapytań dotyczących zasobów i odnajdywanie ich w ramach subskrypcji.  Upewnij się, że wszystkie zasoby platformy Azure obecne w środowisku zostały zatwierdzone.

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

- [Jak tworzyć zapytania za pomocą eksploratora usługi Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7. Usuwanie niezatwierdzonych zasobów i aplikacji platformy Azure

**Wskazówki:** Regularnie uzgadniaj spis i upewnij się, że Azure Monitor powiązane zasoby zostaną usunięte z subskrypcji w terminowy sposób.  

- [Usuwanie obszaru roboczego usługi Azure Log Analytics](/azure/azure-monitor/platform/delete-workspace)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="69-use-only-approved-azure-services"></a>6.9: Używaj tylko zatwierdzonych usług platformy Azure

**Wskazówki:** użyj Azure Policy, aby ograniczyć Azure Monitor zasobów, które można aprowizować w środowisku.

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

- [Jak odmówić określonego typu zasobu za pomocą Azure Policy](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Ograniczanie możliwości interakcji użytkowników z Azure Resource Manager

**Wskazówki:** Użyj dostępu warunkowego platformy Azure, aby ograniczyć użytkownikom możliwość interakcji z usługą Azure Resources Manager, konfigurując opcję "Blokuj dostęp" dla aplikacji "Microsoft Azure Management".

- [Jak skonfigurować dostęp warunkowy, aby zablokować dostęp do usługi Azure Resources Manager](../role-based-access-control/conditional-access-azure-management.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

## <a name="secure-configuration"></a>Bezpieczna konfiguracja

*Aby uzyskać więcej informacji, zobacz [Test porównawczy zabezpieczeń platformy Azure: bezpieczna konfiguracja.](../security/benchmarks/security-control-secure-configuration.md)*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1. Ustanawianie bezpiecznych konfiguracji dla wszystkich zasobów platformy Azure

**Wskazówki:** Użyj niestandardowych Azure Policy do inspekcji lub wymuszania konfiguracji Azure Monitor zasobów. Możesz również użyć wbudowanych definicji Azure Policy dostępnych.

Ponadto Azure Resource Manager szablon można wyeksportować w pliku JavaScript Object Notation (JSON), który należy przejrzeć, aby upewnić się, że konfiguracje spełniają/przekraczają wymagania dotyczące zabezpieczeń w organizacji.

Możesz również użyć zaleceń z witryny Azure Security Center jako bezpiecznej konfiguracji odniesienia dla zasobów platformy Azure.

Jeśli używasz funkcji APM przesyłania strumieniowego na żywo, zabezpiecz kanał za pomocą tajnego klucza interfejsu API oprócz klucza instrumentacji.

- [Zabezpieczanie Live Metrics Stream APM](https://docs.microsoft.com/azure/azure-monitor/app/live-stream#secure-the-control-channel)

- [Jak wyświetlić dostępne Azure Policy aliasy](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-4.8.0&amp;preserve-view=true)

- [Samouczek: tworzenie zasad i zarządzanie nimi w celu wymuszania zgodności](../governance/policy/tutorials/create-and-manage.md)

- [Eksportowanie pojedynczego i wielu zasobów do szablonu w Azure Portal](../azure-resource-manager/templates/export-template-portal.md)

- [Zalecenia dotyczące zabezpieczeń — przewodnik referencyjny](../security-center/recommendations-reference.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Obsługa bezpiecznych konfiguracji zasobów platformy Azure

**Wskazówki:** użyj zasad platformy Azure [deny] i [deploy if not exist] w celu wymuszenia bezpiecznych ustawień Azure Monitor powiązanych zasobów.  Ponadto można używać szablonów Azure Resource Manager, aby zachować konfigurację zabezpieczeń zasobów Azure Monitor wymaganych przez organizację.

- [Zrozumienie Azure Policy danych](../governance/policy/concepts/effects.md)

- [Tworzenie zasad i zarządzanie nimi w celu wymuszania zgodności](../governance/policy/tutorials/create-and-manage.md)

- [omówienie Azure Resource Manager szablonów](../azure-resource-manager/templates/overview.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Bezpieczne przechowywanie konfiguracji zasobów platformy Azure

**Wskazówki:** Użyj Azure DevOps, aby bezpiecznie przechowywać kod i zarządzać jego kodem, na przykład niestandardowymi zasadami platformy Azure i Azure Resource Manager szablonów. Aby uzyskać dostęp do zasobów, które zarządzasz w programie Azure DevOps, możesz udzielić lub odmówić uprawnień określonym użytkownikom, wbudowanym grupom zabezpieczeń lub grupom zdefiniowanym w usłudze Azure Active Directory (Azure AD), jeśli są zintegrowane z programem Azure DevOps, lub usłudze Active Directory, jeśli są zintegrowane z programem TFS.

- [Jak przechowywać kod w Azure DevOps](/azure/devops/repos/git/gitworkflow)

- [Informacje o uprawnieniach i grupach w Azure DevOps](/azure/devops/organizations/security/about-permissions)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7. Wdrażanie narzędzi do zarządzania konfiguracją dla zasobów platformy Azure

**Wskazówki:** Definiowanie i implementowanie standardowych konfiguracji zabezpieczeń dla Azure Monitor powiązanych zasobów przy użyciu Azure Policy. Niestandardowe definicje Azure Policy do inspekcji lub wymuszania konfiguracji zabezpieczeń Azure Monitor powiązanych zasobów. Możesz również korzystać z wbudowanych definicji zasad związanych z określonymi zasobami.

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy aliasów](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure#aliases)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9. Implementowanie zautomatyzowanego monitorowania konfiguracji dla zasobów platformy Azure

**Wskazówki:** Użyj Azure Security Center, aby przeprowadzić skanowanie linii bazowej dla Azure Monitor zasobów.  Ponadto użyj Azure Policy do alertów i inspekcji konfiguracji zasobów platformy Azure.

- [Jak korygować zalecenia w Azure Security Center](../security-center/security-center-remediate-recommendations.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="711-manage-azure-secrets-securely"></a>7.11: Bezpieczne zarządzanie wpisami tajnymi platformy Azure

**Wskazówki:** użyj tożsamości usługi zarządzanej w połączeniu z Azure Key Vault, aby uprościć i zabezpieczyć zarządzanie kluczami tajnymi dla obsługiwanych powiązanych zasobów usługi Azure Monitor.

- [Jak zintegrować z tożsamościami zarządzanymi platformy Azure](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

- [Usługi, które obsługują tożsamości zarządzane dla zasobów platformy Azure](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)

- [Jak utworzyć Key Vault](../key-vault/secrets/quick-create-portal.md)

- [Jak zapewnić uwierzytelnianie Key Vault tożsamości zarządzanej](/azure/active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-nonaad)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Bezpieczne i automatyczne zarządzanie tożsamościami

**Wskazówki:** Korzystanie z tożsamości zarządzanych w celu zapewnienia usługom platformy Azure automatycznie zarządzanej tożsamości w usłudze Azure Active Directory (Azure AD). Tożsamości zarządzane umożliwiają uwierzytelnianie w dowolnej usłudze obsługującej uwierzytelnianie usługi Azure AD, w tym Azure Monitor zasobów, bez żadnych poświadczeń w kodzie.

- [Jak skonfigurować tożsamości zarządzane dla zasobów platformy Azure](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Eliminowanie niezamierzonego narażenia na poświadczenia

**Wskazówki:** implementowanie skanera poświadczeń w celu identyfikowania poświadczeń w kodzie. Skaner poświadczeń ułatwia również przenoszenie odnalezionych poświadczeń do bezpieczniejszych lokalizacji, takich jak usługa Azure Key Vault.

- [Jak skonfigurować skaner poświadczeń](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

## <a name="malware-defense"></a>Ochrona przed złośliwym oprogramowaniem

*Aby uzyskać więcej informacji, zobacz [Test porównawczy zabezpieczeń platformy Azure: Malware Defense.](../security/benchmarks/security-control-malware-defense.md)*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Pliki przed skanowaniem, które mają zostać przekazane do zasobów platformy Azure bez obliczeń

**Wskazówki:** Ochrona firmy Microsoft przed złośliwym kodem jest włączona na podstawowym hoście, który obsługuje usługi platformy Azure (na przykład Azure Monitor powiązane zasoby), ale nie działa na Twojej zawartości. 

Wstępnie przeskanuj wszystkie pliki przekazywane do odpowiednich Azure Monitor powiązanych zasobów, takich jak obszar roboczy usługi Log Analytics.

Wykrywanie Azure Security Center dla usług danych za pomocą usługi wykrywania zagrożeń w celu wykrywania złośliwego oprogramowania przekazanego na konta magazynu. 

- [Informacje o programie Microsoft Anti-malware dla Azure Cloud Services i Virtual Machines](../security/fundamentals/antimalware.md)

- [Understand Azure Security Center's Threat detection for data services (Wykrywanie zagrożeń dla usług danych)](../security-center/azure-defender.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

## <a name="data-recovery"></a>Odzyskiwanie danych

*Aby uzyskać więcej informacji, zobacz [Test porównawczy zabezpieczeń platformy Azure: odzyskiwanie danych.](../security/benchmarks/security-control-data-recovery.md)*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Zapewnianie regularnych automatycznych kopii zapasowej

**Wskazówki:** Użyj programu Azure Resource Manager, aby wyeksportować pliki Azure Monitor i powiązane zasoby w szablonie JavaScript Object Notation (JSON), który może służyć jako kopia zapasowa dla Azure Monitor i powiązanych konfiguracji.  Użyj Azure Automation, aby automatycznie uruchamiać skrypty kopii zapasowej. 

- [Zarządzanie obszarem roboczym usługi Log Analytics przy użyciu Azure Resource Manager szablonów](/azure/azure-monitor/samples/resource-manager-workspace)

- [Eksportowanie pojedynczego zasobu i wielu zasobów do szablonu w Azure Portal](../azure-resource-manager/templates/export-template-portal.md)

- [Informacje o Azure Automation](../automation/automation-intro.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2. Wykonywanie pełnych kopii zapasowych systemu i tworzenie kopii zapasowych wszystkich kluczy zarządzanych przez klienta

**Wskazówki:** Użyj programu Azure Resource Manager, aby wyeksportować pliki Azure Monitor i powiązane zasoby w szablonie JavaScript Object Notation (JSON), który może służyć jako kopia zapasowa dla Azure Monitor i powiązanych konfiguracji. Tworzenie kopii zapasowych kluczy zarządzanych przez klienta w Azure Key Vault, Azure Monitor zasoby pokrewne są przy użyciu kluczy zarządzanych przez klienta.

- [Zarządzanie obszarem roboczym usługi Log Analytics przy użyciu Azure Resource Manager szablonów](/azure/azure-monitor/platform/template-workspace-configuration)

- [Eksportowanie pojedynczego i wielu zasobów do szablonu w Azure Portal](../azure-resource-manager/templates/export-template-portal.md)

- [Jak utworzyć kopię zapasową kluczy magazynu kluczy na platformie Azure](/powershell/module/az.keyvault/backup-azkeyvaultkey)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Zweryfikuj wszystkie kopie zapasowe, w tym klucze zarządzane przez klienta

**Wskazówki:** Zapewnianie możliwości okresowego przeprowadzania przywracania przy Azure Resource Manager plików szablonów kopii zapasowej. Przetestuj przywracanie kopii zapasowej kluczy zarządzanych przez klienta.

- [Zarządzanie obszarem roboczym usługi Log Analytics przy użyciu Azure Resource Manager szablonów](/azure/azure-monitor/samples/resource-manager-workspace)

- [Jak przywrócić klucze magazynu kluczy na platformie Azure](/powershell/module/az.keyvault/restore-azkeyvaultkey)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4. Zapewnianie ochrony kopii zapasowych i kluczy zarządzanych przez klienta

**Wskazówki:** użyj Azure DevOps, aby bezpiecznie przechowywać kod i zarządzać jego kodem, na przykład niestandardowymi zasadami platformy Azure, Azure Resource Manager szablonów. Aby chronić zasoby, które zarządzasz w usłudze Azure DevOps, możesz udzielić lub odmówić uprawnień określonym użytkownikom, wbudowanym grupom zabezpieczeń lub grupom zdefiniowanym w usłudze Azure Active Directory (Azure AD), jeśli są zintegrowane z usługą Azure DevOps, lub usługi Active Directory, jeśli są zintegrowane z programem TFS.  Użyj kontroli dostępu opartej na rolach (RBAC) platformy Azure, aby chronić klucze zarządzane przez klienta.

Ponadto włącz ochronę Soft-Delete przeczyszczania w programie Key Vault, aby chronić klucze przed przypadkowym lub złośliwym usunięciem. Jeśli usługa Azure Storage jest używana do przechowywania Azure Resource Manager kopii zapasowych szablonów, włącz usuwanie nie softowe, aby zapisywać i odzyskiwać dane po usunięciu obiektów blob lub migawek obiektów blob.

- [Jak przechowywać kod w Azure DevOps](/azure/devops/repos/git/gitworkflow)

- [Informacje o uprawnieniach i grupach w Azure DevOps](/azure/devops/organizations/security/about-permissions)

- [Jak włączyć ochronę Soft-Delete przeczyszczania w programie Key Vault](../storage/blobs/soft-delete-blob-overview.md)

- [Soft delete for Azure Storage blobs](../storage/blobs/soft-delete-blob-overview.md) (Usuwanie nietrwałe dla obiektów blob usługi Azure Storage)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

## <a name="incident-response"></a>Reagowanie na zdarzenia

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: reagowanie na zdarzenia](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1. Tworzenie przewodnika po reagowaniu na zdarzenia

**Wskazówka**: Utwórz przewodnik odpowiedzi na zdarzenia dla swojej organizacji. Upewnij się, że istnieją zarejestrowane plany reakcji na zdarzenia, które definiują wszystkie role pracowników, a także etapy obsługi zdarzeń/zarządzania od wykrywania do oceny po zdarzeniu.

- [Wskazówki dotyczące tworzenia własnego procesu reagowania na zdarzenia zabezpieczeń](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Centrum zabezpieczeń firmy Microsoft Anatomy of an Incident (Anatomia zdarzenia)](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [Skorzystaj z przewodnika obsługi zdarzeń zabezpieczeń komputera NIST, aby pomóc w tworzeniu własnego planu reagowania na zdarzenia](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2. Tworzenie procedury oceniania i ustalania priorytetów zdarzeń

**Wskazówki:** Security Center przypisuje ważność do każdego alertu, aby ułatwić priorytetyzowanie alertów, które należy zbadać w pierwszej kolejności. Ważność jest oparta na tym, jak pewna Security Center znajduje się w znajdowaniu lub analizie użytej do wystawienia alertu, a także na poziomie ufności, że za działaniem, które doprowadziło do tego alertu, znajduje się złośliwe intencje.

Ponadto wyraźnie oznaczaj subskrypcje (na przykład production, non-prod) przy użyciu tagów i utwórz system nazewnictwa, aby jasno identyfikować i kategoryzować zasoby platformy Azure, zwłaszcza te, które przetwarzają poufne dane.  Odpowiedzialność za korygowanie alertów w oparciu o krytyczne znaczenie zasobów platformy Azure i środowisko, w którym wystąpiło zdarzenie, leży po stronie użytkownika.

- [Alerty zabezpieczeń w Centrum zabezpieczeń Azure](../security-center/security-center-alerts-overview.md)

- [Jak tworzyć tagi i ich używać](../azure-resource-manager/management/tag-resources.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="103-test-security-response-procedures"></a>10.3: Testowanie procedur reagowania na zabezpieczenia

**Wskazówki:** Przećwiczyj ćwiczenia w celu testowania możliwości reagowania na zdarzenia w systemach w regularnych odstępach czasu, aby ułatwić ochronę zasobów platformy Azure. Zidentyfikuj słabe punkty i przerwy oraz popraw plan zgodnie z wymaganiami.

- [Publikacja NIST — Guide to Test, Training, and Exercise Programs for IT Plans and Capabilities (Przewodnik dotyczący programów testowych, szkoleniowych i szkoleniowych dla planów i możliwości IT)](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4. Podaj szczegóły kontaktowe zdarzenia zabezpieczeń i skonfiguruj powiadomienia o alertach dla zdarzeń zabezpieczeń

**Wskazówki:** informacje kontaktowe dotyczące zdarzeń naruszenia zabezpieczeń będą używane przez firmę Microsoft do kontaktowania się z tobą, jeśli użytkownik Centrum zabezpieczeń firmy Microsoft (MSRC) odkryje, że dostęp do danych użytkownika uzyskał nieupoważniony lub niedozwolony podmiot. Przejrzyj zdarzenia po fakcie, aby upewnić się, że problemy zostały rozwiązane.

- [Jak ustawić kontakt Azure Security Center zabezpieczeń](../security-center/security-center-provide-security-contact-details.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5. Dołączanie alertów zabezpieczeń do systemu reagowania na zdarzenia

**Wskazówki:** eksportowanie alertów Azure Security Center i zaleceń przy użyciu funkcji eksportu ciągłego, aby ułatwić identyfikowanie zagrożeń dla zasobów platformy Azure. Eksport ciągły umożliwia eksportowanie alertów i zaleceń ręcznie lub w ciągły sposób. Możesz użyć łącznika danych Azure Security Center, aby przesyłać strumieniowo alerty do Azure Sentinel. 

- [Jak skonfigurować eksport ciągły](../security-center/continuous-export.md) 

- [Jak przesłać strumieniowo alerty do usługi Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatyzowanie odpowiedzi na alerty zabezpieczeń

**Wskazówki:** Użyj funkcji automatyzacji przepływu pracy w usłudze Azure Security Center, aby automatycznie wyzwalać odpowiedzi za pośrednictwem Logic Apps alertów zabezpieczeń i zaleceń dotyczących ochrony zasobów platformy Azure.

- [Jak skonfigurować automatyzację przepływu pracy i Logic Apps](../security-center/workflow-automation.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

## <a name="penetration-tests-and-red-team-exercises"></a>Testy penetracyjne i ćwiczenia typu „red team”

*Aby uzyskać więcej informacji, zobacz [test porównawczy zabezpieczeń platformy Azure: testy penetracyjne i ćwiczenia zespołu czerwonego.](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Przeprowadzaj regularne testy penetrowania zasobów platformy Azure i zapewniaj korygowanie wszystkich krytycznych ustaleń zabezpieczeń

**Wskazówki:** postępuj zgodnie z regułami zaangażowania firmy Microsoft, aby upewnić się, że testy penetracyjne nie naruszały zasad firmy Microsoft. Użyj strategii firmy Microsoft i wykonania czerwonego zespołu oraz testów penetracyjnych na żywo w chmurze, które są zarządzane przez firmę Microsoft w zakresie infrastruktury, usług i aplikacji w chmurze.

- [Reguły testów penetracyjnych zaangażowania](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Testy typu „red team” w chmurze firmy Microsoft](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Odpowiedzialność**: Współużytkowane

**Azure Security Center monitorowania:** Brak

## <a name="next-steps"></a>Następne kroki

- Zobacz [Omówienie testu porównawczego zabezpieczeń platformy Azure w wersji 2](/azure/security/benchmarks/overview)
- Dowiedz się więcej o [punktach odniesienia zabezpieczeń platformy Azure](/azure/security/benchmarks/security-baselines-overview)
