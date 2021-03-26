---
title: Podstawa zabezpieczeń platformy Azure dla Azure Monitor
description: Linia bazowa zabezpieczeń Azure Monitor zawiera wskazówki i zasoby dotyczące procedur związanych z wdrażaniem zaleceń dotyczących zabezpieczeń określonych w teście zabezpieczeń platformy Azure.
author: msmbaldwin
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: c697180ed9a4b020da392a33d7ce34f6727ec7b3
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105568209"
---
# <a name="azure-security-baseline-for-azure-monitor"></a>Podstawa zabezpieczeń platformy Azure dla Azure Monitor

Ta linia bazowa zabezpieczeń stosuje wskazówki z programu [Azure Security test w wersji 1,0](../security/benchmarks/overview-v1.md) do Azure monitor. Test porównawczy zabezpieczeń platformy Azure zawiera zalecenia dotyczące sposobu zabezpieczania rozwiązań w chmurze na platformie Azure.
Zawartość jest pogrupowana według **kontroli zabezpieczeń** zdefiniowanych przez program Azure Security test i powiązane wskazówki dotyczące Azure monitor. **Kontrolki** nie mają zastosowania do Azure monitor zostały wykluczone. 

 
Aby dowiedzieć się, jak Azure Monitor całkowicie mapować do testu porównawczego zabezpieczeń platformy Azure, zobacz [pełny Azure monitor pliku mapowania linii bazowej zabezpieczeń](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Bezpieczeństwo sieci

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: bezpieczeństwo sieci](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: Ochrona zasobów platformy Azure w ramach sieci wirtualnych

**Wskazówki**: Włącz prywatny link Azure, aby umożliwić dostęp do usług Azure SaaS Services (na przykład Azure monitor) oraz hostowanych usług klienta i partnerskich platformy Azure za pośrednictwem prywatnego punktu końcowego w sieci wirtualnej. Ruch między siecią wirtualną a usługą odbywa się za pośrednictwem sieci szkieletowej firmy Microsoft, eliminując ekspozycję z publicznego Internetu.

Aby zezwolić na dostęp do Azure Monitor, użyj tagów usługi "AzureMonitor", aby zezwalać na ruch przychodzący i wychodzący za pomocą sieciowych grup zabezpieczeń. Aby zezwolić na dostęp do danych testowych monitorowania dostępności do Azure Monitor, Użyj znacznika usługi "ApplicationInsightsAvailability" do całego ruchu przychodzącego za pomocą sieciowych grup zabezpieczeń. Aby umożliwić powiadamianie o alertach w celu uzyskania dostępu do punktów końcowych klienta, należy użyć tagu usługi "ActionName", aby zezwolić na ruch przychodzący przez sieciowe grupy zabezpieczeń

Reguły sieci wirtualnej umożliwiają Azure Monitor tylko akceptowanie komunikacji wysyłanej z wybranych podsieci w sieci wirtualnej.

Za pomocą bramy Log Analytics można wysyłać dane do Log Analytics obszaru roboczego w Azure Monitor w imieniu komputerów, które nie mogą bezpośrednio łączyć się z Internetem, aby zapobiec połączeniu komputerów z Internetem. 

- [Jak skonfigurować link prywatny dla Azure Monitor](./logs/private-link-security.md)

- [Łączenie komputerów bez dostępu do Internetu przy użyciu bramy Log Analytics w programie Azure Monitor](./agents/gateway.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1,2: Monitoruj i Rejestruj konfigurację oraz ruch sieci wirtualnych, podsieci i interfejsów sieciowych

**Wskazówki**: Azure monitor to podstawowa usługa i nie obsługuje wdrażania bezpośrednio w sieci wirtualnej, jej podstawowa infrastruktura jest obsługiwana przez firmę Microsoft. Jednak w przypadku zasobów, które umożliwiają połączenie sieciowe z ofertą Azure Monitor, Zabezpiecz swoją sieć za pomocą sieciowej grupy zabezpieczeń. Włącz dzienniki przepływu sieciowych grup zabezpieczeń i Wyślij dzienniki do konta magazynu na potrzeby inspekcji ruchu. Możesz również wysyłać dzienniki przepływów do obszaru roboczego Log Analytics i używać Analiza ruchu, aby uzyskać wgląd w przepływ ruchu w chmurze platformy Azure. Niektóre zalety Analiza ruchu to możliwość wizualizacji aktywności sieciowej i identyfikowania aktywnych punktów, identyfikowania zagrożeń bezpieczeństwa, zrozumienia wzorców przepływu ruchu i wyznaczania konfiguracji sieci.

W przypadku korzystania z usługi Azure Monitor z linkiem prywatnym uzyskuje się dostęp do rejestrowania w sieci, takiego jak "dane przetworzone przez prywatny punkt końcowy (WE/wychodzącym").

- [Wymagania sieciowe dotyczące Azure Monitor agentów](./agents/log-analytics-agent.md#network-requirements)

- [Łączenie komputerów bez dostępu do Internetu przy użyciu bramy Log Analytics w programie Azure Monitor](./agents/gateway.md)

- [Jak włączyć dzienniki przepływu sieciowych grup zabezpieczeń](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Jak włączyć i używać Analiza ruchu](../network-watcher/traffic-analytics.md)

- [Informacje o zabezpieczeniach sieci zapewnianych przez Azure Security Center](../security-center/security-center-network-recommendations.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: Minimalizacja złożoności i kosztów administracyjnych reguł zabezpieczeń sieci

**Wskazówki**: aby zezwolić na dostęp do Azure monitor, użyj tagów usługi "AzureMonitor", aby zezwolić na ruch przychodzący i wychodzący za pomocą sieciowych grup zabezpieczeń. Aby zezwolić na dostęp do danych testowych monitorowania dostępności do Azure Monitor, Użyj znacznika usługi "ApplicationInsightsAvailability" do całego ruchu przychodzącego za pomocą sieciowych grup zabezpieczeń. Firma Microsoft zarządza prefiksami adresów, które obejmują tag usługi, i automatycznie aktualizuje tag usługi jako adresy.

- [Zrozumienie i używanie tagów usługi](../virtual-network/service-tags-overview.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="110-document-traffic-configuration-rules"></a>1,10: udokumentowanie reguł konfiguracji ruchu

**Wskazówki**: Azure monitor jest częścią usług Azure Core i nie można ich wdrożyć jako usługi oddzielnie. Składniki Azure Monitor, w tym Agent Azure Monitor i zestaw SDK Application Insights można wdrożyć wraz z zasobami. może to mieć wpływ na stan zabezpieczeń tych zasobów.

- [Wymagania sieciowe dotyczące Azure Monitor agentów](./agents/log-analytics-agent.md#network-requirements)

- [Łączenie komputerów bez dostępu do Internetu przy użyciu bramy Log Analytics w programie Azure Monitor](./agents/gateway.md) 

- [Zobacz Rozpoczynanie pracy z usługą Application Insights](./app/app-insights-overview.md#get-started)

- [Jak skonfigurować testy sieci Web dostępności](app/monitor-web-app-availability.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: Użyj zautomatyzowanych narzędzi do monitorowania konfiguracji zasobów sieciowych i wykrywania zmian

**Wskazówki**: Użyj dziennika aktywności platformy Azure do monitorowania konfiguracji zasobów i wykrywania zmian zasobów sieciowych związanych z Azure monitor. Tworzenie alertów w Azure Monitor, które będą wyzwalane, gdy następuje zmiana tych krytycznych zasobów sieciowych.

- [Jak wyświetlać i pobierać zdarzenia dziennika aktywności platformy Azure](./essentials/activity-log.md#view-the-activity-log)

- [Jak utworzyć alerty w Azure Monitor](./alerts/alerts-activity-log.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

## <a name="logging-and-monitoring"></a>Rejestrowanie i monitorowanie

*Aby uzyskać więcej informacji, zobacz temat [Azure Security test: rejestrowanie i monitorowanie](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2,2: Skonfiguruj centralne zarządzanie dziennikami zabezpieczeń

**Wskazówki**: Azure monitor używa dzienników aktywności do rejestrowania zmian w swoich zasobach. Te dzienniki można wyeksportować do usługi Azure Storage, centrum zdarzeń lub obszaru roboczego Log Analytics. Pozyskiwanie dzienników za pośrednictwem Azure Monitor do agregowania danych zabezpieczeń wygenerowanych przez urządzenia, zasoby sieciowe i inne systemy zabezpieczeń. W Azure Monitor można wysyłać zapytania i wykonywać analizy względem danych, korzystając z kont usługi Azure Storage do przechowywania długoterminowych/archiwizowania dzienników.

Alternatywnie możesz włączyć i dołączyć dane do usługi Azure wskaźnikowej lub SIEM innych firm.

- [Jak zbierać dzienniki platformy i metryki za pomocą Azure Monitor](./essentials/diagnostic-settings.md)

- [Jak zbierać dzienniki wewnętrznego hosta maszyny wirtualnej platformy Azure z Azure Monitor](./vm/quick-collect-azurevm.md)

- [Jak dołączyć wskaźnik na platformie Azure](../sentinel/quickstart-onboard.md)

- [Jak rozpocząć pracę z usługą Azure Monitor i integracją SIEM innej firmy](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania**: [wzorzec zabezpieczeń platformy Azure](/azure/governance/policy/samples/azure-security-benchmark) jest domyślną inicjatywy zasad dla Security Center i jest podstawą dla [zaleceń Security Center](/azure/security-center/security-center-recommendations). Definicje Azure Policy powiązane z tym formantem są włączane automatycznie przez Security Center. Alerty związane z tym formantem mogą wymagać planu [usługi Azure Defender](/azure/security-center/azure-defender) dla powiązanych usług.

**Azure Policy wbudowane definicje — Microsoft. Insights**:

[!INCLUDE [Resource Policy for microsoft.insights 2.2](../../includes/policy/standards/asb/rp-controls/microsoft.insights-2-2.md)]

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: Włączanie rejestrowania inspekcji dla zasobów platformy Azure

**Wskazówki**: Azure monitor korzysta z dzienników aktywności, dziennik aktywności jest automatycznie włączany i rejestruje operacje wykonywane na zasobach Azure monitor, takich jak: kto uruchomił operację, po wystąpieniu operacji, stan operacji i inne przydatne informacje inspekcji. 

- [Jak zbierać dzienniki platformy i metryki za pomocą Azure Monitor](./essentials/diagnostic-settings.md)

- [Informacje o rejestrowaniu i różnych typach dzienników na platformie Azure](./essentials/platform-logs-overview.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania**: [wzorzec zabezpieczeń platformy Azure](/azure/governance/policy/samples/azure-security-benchmark) jest domyślną inicjatywy zasad dla Security Center i jest podstawą dla [zaleceń Security Center](/azure/security-center/security-center-recommendations). Definicje Azure Policy powiązane z tym formantem są włączane automatycznie przez Security Center. Alerty związane z tym formantem mogą wymagać planu [usługi Azure Defender](/azure/security-center/azure-defender) dla powiązanych usług.

**Azure Policy wbudowane definicje — Microsoft. Insights**:

[!INCLUDE [Resource Policy for microsoft.insights 2.3](../../includes/policy/standards/asb/rp-controls/microsoft.insights-2-3.md)]

### <a name="25-configure-security-log-storage-retention"></a>2,5: Konfigurowanie przechowywania magazynu dzienników zabezpieczeń

**Wskazówki**: w Azure monitor ustawić okres przechowywania obszaru roboczego log Analytics zgodnie z regulacjami zgodności w organizacji. Używaj kont usługi Azure Storage do przechowywania długoterminowych/archiwizowania dzienników.

- [Zmień okres przechowywania danych w Log Analytics](./logs/manage-cost-storage.md#change-the-data-retention-period)

- [Jak skonfigurować zasady przechowywania dla dzienników kont usługi Azure Storage](../storage/common/manage-storage-analytics-logs.md#configure-logging)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="26-monitor-and-review-logs"></a>2,6: dzienniki monitorowania i przeglądania

**Wskazówki**: analizowanie i monitorowanie dzienników pod kątem nietypowego zachowania i regularne przeglądanie wyników. Użyj Azure Monitor i Log Analytics obszaru roboczego do przeglądania dzienników i wykonywania zapytań dotyczących danych dziennika.

Alternatywnie możesz włączyć i dołączyć dane do usługi Azure wskaźnikowej lub SIEM innej firmy.

- [Jak dołączyć wskaźnik na platformie Azure](../sentinel/quickstart-onboard.md)

- [Wprowadzenie do Log Analytics zapytań](./logs/log-analytics-tutorial.md)

- [Jak wykonywać niestandardowe zapytania w Azure Monitor](./logs/get-started-queries.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: Włączanie alertów dla nietypowych działań

**Wskazówki**: Użyj Azure Security Center z obszarem roboczym log Analytics do monitorowania i generowania alertów dotyczących nietypowego działania znalezionego w dziennikach i zdarzeniach zabezpieczeń. Alternatywnie możesz włączyć i dołączyć dane do usługi Azure wskaźnikowej.

- [Jak dołączyć wskaźnik na platformie Azure](../sentinel/quickstart-onboard.md)

- [Jak zarządzać alertami w Azure Security Center](../security-center/security-center-managing-and-responding-alerts.md)

- [Jak ostrzec dane dziennika usługi log Analytics](./alerts/tutorial-response.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

## <a name="identity-and-access-control"></a>Tożsamość i kontrola dostępu

*Aby uzyskać więcej informacji, zobacz informacje o [teście zabezpieczeń Azure: Identity i Access Control](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: obsługa spisu kont administracyjnych

**Wskazówki**: Kontrola dostępu oparta na rolach (Azure RBAC) umożliwia zarządzanie dostępem do zasobów platformy Azure za pomocą przypisań ról. Te role można przypisać do użytkowników, grup jednostek usługi i zarządzanych tożsamości. Dla niektórych zasobów istnieją wstępnie zdefiniowane wbudowane role i te role można zinwentaryzować lub wykonywać względem nich zapytania za pomocą narzędzi takich jak interfejs wiersza polecenia platformy Azure, program Azure PowerShell lub witryna Azure Portal.

- [Jak uzyskać rolę katalogu w usłudze Azure Active Directory (Azure AD) przy użyciu programu PowerShell](/powershell/module/azuread/get-azureaddirectoryrole?amp;preserve-view=true&view=azureadps-2.0)

- [Jak uzyskać członków roli katalogu w usłudze Azure AD przy użyciu programu PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember?amp;preserve-view=true&view=azureadps-2.0)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: Użyj dedykowanych kont administracyjnych

**Wskazówki**: Tworzenie standardowych procedur operacyjnych dotyczących korzystania z dedykowanych kont administracyjnych. Użyj Azure Security Center Zarządzanie tożsamościami i dostępem, aby monitorować liczbę kont administracyjnych.

Możesz również włączyć dostęp w trybie just-in-Time/tylko za pomocą usługi Azure Active Directory (Azure AD) Privileged Identity Management role uprzywilejowane dla usług firmy Microsoft i Azure Resource Manager.

- [Przegląd Azure AD Privileged Identity Management](../active-directory/privileged-identity-management/pim-configure.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3,4: Użyj Azure Active Directory logowania jednokrotnego (SSO)

**Wskazówki**: wszędzie tam, gdzie to możliwe, użyj Azure Active Directory (Azure AD) SSO zamiast konfigurować indywidualne poświadczenia autonomiczne dla poszczególnych usług. Użyj Azure Security Center zalecenia dotyczące zarządzania tożsamościami i dostępem.

- [Opis logowania jednokrotnego w usłudze Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Użyj uwierzytelniania wieloskładnikowego dla wszystkich Azure Active Directory dostępu opartego na usłudze

**Wskazówki**: Włączanie uwierzytelniania wieloskładnikowego Azure Active Directory (Azure AD) i przestrzeganie Azure Security Center zaleceń dotyczących tożsamości i dostępu.

- [Jak włączyć uwierzytelnianie wieloskładnikowe na platformie Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Jak monitorować tożsamość i dostęp w Azure Security Center](../security-center/security-center-identity-access.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: Używaj dedykowanych maszyn (uprzywilejowany dostęp do stacji roboczych) dla wszystkich zadań administracyjnych

**Wskazówki**: Użyj bezpiecznej, zarządzanej przez platformę Azure stacji roboczej (znanej także jako stacja robocza dostępu uprzywilejowanego lub dostępem uprzywilejowanym) dla zadań administracyjnych, które wymagają podwyższonego poziomu uprawnień.

- [Informacje na temat bezpiecznych stacji roboczych zarządzanych przez platformę Azure](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Jak włączyć uwierzytelnianie wieloskładnikowe w usłudze Azure Active Directory (Azure AD)](../active-directory/authentication/howto-mfa-getstarted.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: Rejestruj i Ostrzegaj o podejrzanych działaniach z kont administracyjnych

**Wskazówki**: Użyj raportów i monitorowania zabezpieczeń Azure Active Directory (Azure AD), aby wykryć, kiedy w środowisku występuje podejrzane lub niebezpieczne działania. Użyj Azure Security Center, aby monitorować działania związane z tożsamościami i dostępem.

- [Identyfikowanie użytkowników usługi Azure AD oflagowanych na skutek ryzykownego działania](../active-directory/identity-protection/overview-identity-protection.md)

- [Jak monitorować działania użytkowników związane z tożsamościami i dostępem w usłudze Azure Security Center](../security-center/security-center-identity-access.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: zarządzanie zasobami platformy Azure tylko z zatwierdzonych lokalizacji

**Wskazówki**: Użyj dostępu warunkowego o nazwie Locations, aby zezwolić na dostęp tylko do określonych logicznych grup zakresów adresów IP lub krajów/regionów.

- [Jak skonfigurować nazwane lokalizacje na platformie Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="39-use-azure-active-directory"></a>3,9: Użyj Azure Active Directory

**Wskazówki**: Użyj Azure Active Directory (Azure AD) jako centralnego systemu uwierzytelniania i autoryzacji. Usługa Azure AD chroni dane przy użyciu silnego szyfrowania danych przechowywanych i przesyłanych. Usługa Azure AD również Sole, skróty i bezpieczne przechowywanie poświadczeń użytkownika.

- [Jak utworzyć i skonfigurować wystąpienie usługi Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: regularnie Przeglądaj i Uzgodnij dostęp użytkowników

**Wskazówki**: Azure Active Directory (Azure AD) zawiera dzienniki ułatwiające wykrywanie starych kont. Ponadto za pomocą przeglądów dostępu do tożsamości platformy Azure można efektywnie zarządzać członkostwem w grupach, dostępem do aplikacji dla przedsiębiorstw i przypisaniami ról. Dostęp użytkowników może być regularnie przeglądany, aby upewnić się, że tylko Ci użytkownicy mają ciągły dostęp.

- [Informacje o raportowaniu usługi Azure AD](../active-directory/reports-monitoring/index.yml)

- [Jak korzystać z przeglądów dostępu do tożsamości platformy Azure](../active-directory/governance/access-reviews-overview.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: Monitor próbuje uzyskać dostęp do zdezaktywowanych poświadczeń

**Wskazówki**: masz dostęp do źródeł zdarzeń związanych z logowaniem do Azure Active Directory (Azure AD), inspekcją i ryzykiem związanym z dziennikami, które umożliwiają integrację z dowolnym narzędziem Siem/monitoring. Proces ten można usprawnić, tworząc ustawienia diagnostyczne dla kont użytkowników usługi Azure AD i wysyłając dzienniki inspekcji i dzienniki logowania do obszaru roboczego Log Analytics. Żądane alerty można skonfigurować w obszarze roboczym Log Analytics.

- [Jak zintegrować dzienniki aktywności platformy Azure z usługą Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: odchylenia zachowania alertu dotyczącego logowania na koncie

**Wskazówki**: Użyj funkcji ryzyka i ochrony tożsamości Azure Active Directory (Azure AD), aby skonfigurować automatyczne odpowiedzi na wykryte podejrzane działania związane z tożsamościami użytkowników. Możesz również pozyskać dane do usługi Azure wskaźnikowej na potrzeby dalszej analizy.

- [Jak wyświetlić ryzykowne logowania w usłudze Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

- [Jak skonfigurować i włączyć zasady dotyczące ryzyka związanego z ochroną tożsamości](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Jak dołączyć wskaźnik na platformie Azure](../sentinel/quickstart-onboard.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

## <a name="data-protection"></a>Ochrona danych

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: ochrona danych](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: przechowywanie spisu poufnych informacji

**Wskazówki**: Użyj tagów, gdy jest to możliwe, aby pomóc w śledzeniu zasobów Azure monitor, które przechowują lub przetwarzają informacje poufne, takie jak log Analytics obszarów roboczych.

- [Tworzenie i używanie tagów](../azure-resource-manager/management/tag-resources.md)

- [Zarządzanie dostępem do danych dziennika i obszarami roboczymi w usłudze Azure Monitor](./logs/manage-access.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: Izoluj systemy przechowujące lub przetwarzające informacje poufne

**Wskazówki**: Zaimplementuj izolację przy użyciu osobnych subskrypcji i grup zarządzania dla poszczególnych domen zabezpieczeń, takich jak typ środowiska i poziom czułości danych. Możesz ograniczyć poziom dostępu do Azure Monitor i powiązanych zasobów, których wymagają Twoje aplikacje i środowiska korporacyjne. Dostęp do Azure Monitor można kontrolować za pomocą kontroli dostępu opartej na rolach (Azure RBAC).

- [Jak utworzyć dodatkowe subskrypcje platformy Azure](../cost-management-billing/manage/create-subscription.md)

- [Tworzenie grup zarządzania](../governance/management-groups/create-management-group-portal.md)

- [Tworzenie i używanie tagów](../azure-resource-manager/management/tag-resources.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: Szyfruj wszystkie poufne informacje podczas przesyłania

**Wskazówki**: Azure monitor domyślnie NEGOCJUJE protokół TLS 1,2. Upewnij się, że wszyscy klienci łączący się z zasobami platformy Azure mogą negocjować protokół TLS 1,2 lub nowszy. 

Application Insights i Log Analytics w dalszym ciągu zezwalają na pozyskiwanie danych TLS 1,1 i TLS 1,0. Dane mogą być ograniczone do protokołu TLS 1,2 przez skonfigurowanie po stronie klienta.

- [Jak bezpiecznie przesyłać dane przy użyciu protokołu TLS 1,2](./logs/data-security.md#sending-data-securely-using-tls-12)

**Odpowiedzialność**: Współużytkowane

**Monitorowanie Azure Security Center**: brak

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: Użyj aktywnego narzędzia do odnajdywania, aby identyfikować poufne dane

**Wskazówki**: funkcje identyfikacji, klasyfikacji i zapobiegania utracie danych nie są jeszcze dostępne dla Azure monitor. Zaimplementuj rozwiązanie innych firm, jeśli jest wymagane na potrzeby zgodności.
W przypadku podstawowej platformy zarządzanej przez firmę Microsoft Firma Microsoft traktuje całą zawartość klienta jako poufną i nadaje im dużą długość, aby chronić przed utratą i narażeniem danych przez klienta. Aby zapewnić bezpieczeństwo danych klienta na platformie Azure, firma Microsoft wdrożyła i utrzymuje pakiet niezawodnych kontroli i możliwości ochrony danych.

- [Informacje na temat ochrony danych klientów na platformie Azure](../security/fundamentals/protection-customer-data.md)

**Odpowiedzialność**: Współużytkowane

**Monitorowanie Azure Security Center**: brak

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4,6: stosowanie kontroli dostępu opartej na rolach w celu kontrolowania dostępu do zasobów

**Wskazówki**: Aby zarządzać dostępem do Azure monitor, użyj kontroli dostępu opartej na ROLACH (RBAC) na platformie Azure.

- [Role, uprawnienia i zabezpieczenia w Azure Monitor](./roles-permissions-security.md)

- [Jak skonfigurować usługę Azure RBAC](../role-based-access-control/role-assignments-portal.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: Szyfruj poufne informacje w spoczynku

**Wskazówki**: Azure Monitor zapewnia szyfrowanie wszystkich danych i zapisanych zapytań przy użyciu kluczy zarządzanych przez firmę Microsoft (MMK). Azure Monitor udostępnia również opcję szyfrowania przy użyciu własnego klucza przechowywanego w Azure Key Vault i dostępnego przez magazyn przy użyciu uwierzytelniania tożsamości zarządzanej przypisanego przez system. Ten klucz zarządzany przez klienta (CMK) może być chroniony za pomocą oprogramowania lub sprzętowego modułu HSM.

- [Azure Monitor klucze zarządzane przez klienta](./logs/customer-managed-keys.md)

- [Zabezpieczenia danych Log Analytics](./logs/data-security.md)

- [Zbieranie, przechowywanie i magazynowanie danych w usłudze Application Insights](app/data-retention-privacy.md)

- [Informacje o szyfrowaniu danych magazynowanych na platformie Azure](../security/fundamentals/encryption-atrest.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: Rejestruj i Ostrzegaj o zmianach krytycznych zasobów platformy Azure

**Wskazówki**: Użyj Azure monitor z dziennikiem aktywności platformy Azure, aby utworzyć alerty dotyczące sytuacji, w których zmiany dotyczą Azure monitor i powiązanych zasobów.

- [Jak utworzyć alerty dla zdarzeń dziennika aktywności platformy Azure](./alerts/alerts-activity-log.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania**: [wzorzec zabezpieczeń platformy Azure](/azure/governance/policy/samples/azure-security-benchmark) jest domyślną inicjatywy zasad dla Security Center i jest podstawą dla [zaleceń Security Center](/azure/security-center/security-center-recommendations). Definicje Azure Policy powiązane z tym formantem są włączane automatycznie przez Security Center. Alerty związane z tym formantem mogą wymagać planu [usługi Azure Defender](/azure/security-center/azure-defender) dla powiązanych usług.

**Azure Policy wbudowane definicje — Microsoft. Insights**:

[!INCLUDE [Resource Policy for microsoft.insights 4.9](../../includes/policy/standards/asb/rp-controls/microsoft.insights-4-9.md)]

## <a name="vulnerability-management"></a>Zarządzanie lukami w zabezpieczeniach

*Aby uzyskać więcej informacji, zobacz temat [Azure Security test: Zarządzanie lukami w zabezpieczeniach](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: Użyj procesu oceny ryzyka, aby określić priorytety korygowania odkrytych luk w zabezpieczeniach

**Wskazówki**: Użyj typowego programu oceny ryzyka (na przykład typowego rozwiązania do oceny luk w zabezpieczeniach) lub domyślnych ocen ryzyka udostępnianych przez narzędzie skanowania innej firmy.

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

## <a name="inventory-and-asset-management"></a>Zarządzanie magazynem i zasobami

*Aby uzyskać więcej informacji, zobacz temat [Azure Security test: Inventory and Asset Management](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: Użyj rozwiązania automatycznego odnajdywania zasobów

**Wskazówki**: korzystanie z interfejsu wiersza polecenia platformy Azure w celu wykonywania zapytań i odnajdywania zasobów Azure monitor w ramach subskrypcji. Upewnij się, że masz odpowiednie uprawnienia (odczyt) w dzierżawie i Wylicz wszystkie subskrypcje platformy Azure oraz zasoby w ramach subskrypcji.

- [Interfejs wiersza polecenia Azure Monitor](/cli/azure/monitor)

- [Jak wyświetlić subskrypcje platformy Azure](/powershell/module/az.accounts/get-azsubscription?preserve-view=true&view=azps-4.8.0)

- [Opis kontroli RBAC platformy Azure](../role-based-access-control/overview.md)

- [Role, uprawnienia i zabezpieczenia w Azure Monitor](./roles-permissions-security.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="62-maintain-asset-metadata"></a>6,2: Konserwowanie metadanych zasobów

**Wskazówki**: Zastosuj znaczniki do Azure Monitor zasobów, dzięki czemu metadane są logicznie zorganizowane w taksonomię.

- [Tworzenie i używanie tagów](../azure-resource-manager/management/tag-resources.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: Usuń nieautoryzowane zasoby platformy Azure

**Wskazówki**: używanie tagowania, grup zarządzania i oddzielnych subskrypcji, gdzie to konieczne, do organizowania i śledzenia Azure monitor powiązanych zasobów. Regularnie Uzgadniaj spis i zapewnij, że nieautoryzowane zasoby są usuwane z subskrypcji w odpowiednim czasie.

- [Jak utworzyć dodatkowe subskrypcje platformy Azure](../cost-management-billing/manage/create-subscription.md)

- [Tworzenie grup zarządzania](../governance/management-groups/create-management-group-portal.md)

- [Tworzenie i używanie tagów](../azure-resource-manager/management/tag-resources.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6,4: Definiowanie i konserwowanie spisu zatwierdzonych zasobów platformy Azure

**Wskazówki**: Tworzenie spisu zatwierdzonych zasobów platformy Azure oraz zatwierdzonego oprogramowania do zasobów obliczeniowych zgodnie z potrzebami organizacji.

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: Monitoruj niezatwierdzone zasoby platformy Azure

**Wskazówki**: Użyj Azure Policy, aby wprowadzić ograniczenia dotyczące typu zasobów, które mogą być tworzone w ramach subskrypcji.

Usługa Azure Resource Graph umożliwia wykonywanie zapytań dotyczących zasobów i odnajdywanie ich w ramach subskrypcji.  Upewnij się, że wszystkie zasoby platformy Azure obecne w środowisku są zatwierdzone.

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

- [Jak tworzyć zapytania za pomocą eksploratora usługi Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: Usuń niezatwierdzone zasoby platformy Azure i aplikacje oprogramowania

**Wskazówki**: regularnie Uzgadniaj spis i zapewnij, że nieautoryzowane Azure monitor powiązane zasoby zostaną usunięte z subskrypcji w odpowiednim czasie.  

- [Usuwanie obszaru roboczego usługi Azure Log Analytics](./logs/delete-workspace.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="69-use-only-approved-azure-services"></a>6,9: Używaj tylko zatwierdzonych usług platformy Azure

**Wskazówki**: Użyj Azure Policy, aby ograniczyć, które Azure monitor powiązane zasoby możesz udostępnić w danym środowisku.

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

- [Jak odmówić określonego typu zasobu za pomocą Azure Policy](../governance/policy/samples/built-in-policies.md#general)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: Ogranicz możliwość korzystania przez użytkowników z Azure Resource Manager

**Wskazówki**: Użyj dostępu warunkowego platformy Azure, aby ograniczyć możliwość korzystania przez użytkowników z Menedżera zasobów platformy Azure przez skonfigurowanie "blokowania dostępu" dla aplikacji "Microsoft Azure Management".

- [Jak skonfigurować dostęp warunkowy, aby blokować dostęp do usługi Azure Resources](../role-based-access-control/conditional-access-azure-management.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

## <a name="secure-configuration"></a>Bezpieczna konfiguracja

*Aby uzyskać więcej informacji, zobacz temat [Azure Security test: bezpieczna konfiguracja](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: Ustanów bezpieczne konfiguracje dla wszystkich zasobów platformy Azure

**Wskazówki**: Użyj niestandardowych definicji Azure Policy, aby przeprowadzić inspekcję lub wymusić konfigurację Azure monitor związanych z nimi zasobów. Możesz również użyć wbudowanych definicji Azure Policy jako dostępnych.

Ponadto Azure Resource Manager ma możliwość eksportowania szablonu w JavaScript Object Notation (JSON), który powinien zostać sprawdzony, aby upewnić się, że konfiguracje spełniają/przekroczą wymagania dotyczące zabezpieczeń organizacji.

Możesz również użyć zaleceń z Azure Security Center jako bezpiecznej linii bazowej konfiguracji dla zasobów platformy Azure.

W przypadku korzystania z funkcji APM przesyłania strumieniowego na żywo, oprócz klucza instrumentacji, należy zabezpieczyć kanał przy użyciu klucza tajnego interfejsu API.

- [Zabezpieczanie Live Metrics Stream APM](./app/live-stream.md#secure-the-control-channel)

- [Jak wyświetlić dostępne aliasy Azure Policy](/powershell/module/az.resources/get-azpolicyalias?amp;preserve-view=true&view=azps-4.8.0)

- [Samouczek: Tworzenie zasad i zarządzanie nimi w celu wymuszenia zgodności](../governance/policy/tutorials/create-and-manage.md)

- [Eksport jednego i kilku zasobów do szablonu w Azure Portal](../azure-resource-manager/templates/export-template-portal.md)

- [Zalecenia dotyczące zabezpieczeń — przewodnik referencyjny](../security-center/recommendations-reference.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: obsługa bezpiecznych konfiguracji zasobów platformy Azure

**Wskazówki**: Użyj zasad platformy Azure [Odmów] i [Wdróż, jeśli nie istnieje], aby wymusić bezpieczne ustawienia dla pokrewnych zasobów Azure monitor.  Ponadto możesz użyć szablonów Azure Resource Manager, aby zachować konfigurację zabezpieczeń dla Azure Monitor powiązanych zasobów wymaganych przez organizację.

- [Zrozumienie efektów Azure Policy](../governance/policy/concepts/effects.md)

- [Tworzenie zasad i zarządzanie nimi w celu wymuszania zgodności](../governance/policy/tutorials/create-and-manage.md)

- [Przegląd szablonów Azure Resource Manager](../azure-resource-manager/templates/overview.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: bezpiecznie przechowuj konfigurację zasobów platformy Azure

**Wskazówki**: Użyj usługi Azure DevOps, aby bezpiecznie przechowywać kod, taki jak niestandardowe zasady platformy Azure i szablony Azure Resource Manager, oraz zarządzać nimi. Aby uzyskać dostęp do zasobów zarządzanych w usłudze Azure DevOps, można udzielić lub odmówić uprawnień określonym użytkownikom, wbudowanym grupom zabezpieczeń lub grupom zdefiniowanym w Azure Active Directory (Azure AD) w przypadku integracji z usługą Azure DevOps lub Active Directory w przypadku integracji z programem TFS.

- [Jak przechowywać kod w usłudze Azure DevOps](/azure/devops/repos/git/gitworkflow?amp;preserve-view=true&view=azure-devops)

- [Informacje o uprawnieniach i grupach na platformie Azure DevOps](/azure/devops/organizations/security/about-permissions)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: Wdrażanie narzędzi do zarządzania konfiguracją dla zasobów platformy Azure

**Wskazówki**: Definiowanie i implementowanie standardowych konfiguracji zabezpieczeń dla Azure monitor powiązanych zasobów przy użyciu Azure Policy. Niestandardowe definicje Azure Policy służą do inspekcji lub wymuszania konfiguracji zabezpieczeń powiązanych Azure Monitor zasobów. Mogą również korzystać z wbudowanych definicji zasad związanych z konkretnymi zasobami.

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

- [Aliasy Azure Policy](../governance/policy/concepts/definition-structure.md#aliases)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: Zaimplementuj automatyczne monitorowanie konfiguracji dla zasobów platformy Azure

**Wskazówki**: Użyj Azure Security Center, aby przeprowadzić skanowanie linii bazowej dla Azure monitor powiązanych zasobów.  Ponadto należy używać Azure Policy do powiadamiania i inspekcji konfiguracji zasobów platformy Azure.

- [Jak skorygować zalecenia w Azure Security Center](../security-center/security-center-remediate-recommendations.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="711-manage-azure-secrets-securely"></a>7,11: bezpieczne zarządzanie wpisami tajnymi platformy Azure

**Wskazówki**: Użyj tożsamość usługi zarządzanej w połączeniu z Azure Key Vault do uproszczenia i bezpiecznego zarządzania kluczami tajnymi dla obsługiwanych zasobów usługi Azure monitor.

- [Jak przeprowadzić integrację z tożsamościami zarządzanymi przez platformę Azure](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

- [Usługi obsługujące zarządzane tożsamości dla zasobów platformy Azure](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)

- [Jak utworzyć Key Vault](../key-vault/secrets/quick-create-portal.md)

- [Jak zapewnić uwierzytelnianie Key Vault przy użyciu tożsamości zarządzanej](/azure/key-vault/general/assign-access-policy-portal)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: bezpieczne i automatyczne zarządzanie tożsamościami

**Wskazówki**: używanie tożsamości zarządzanych do świadczenia usług platformy Azure z automatycznie zarządzaną tożsamością w Azure Active Directory (Azure AD). Zarządzane tożsamości umożliwiają uwierzytelnianie do dowolnej usługi, która obsługuje uwierzytelnianie usługi Azure AD, w tym Azure Monitor zasobów, bez żadnych poświadczeń w kodzie.

- [Jak skonfigurować tożsamości zarządzane dla zasobów platformy Azure](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

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

**Wskazówki**: oprogramowanie chroniące przed złośliwym oprogramowaniem firmy Microsoft jest włączone na podstawowym hoście obsługującym usługi platformy Azure (na przykład Azure monitor powiązanych zasobów), ale nie jest ono uruchamiane w Twojej zawartości. 

Skanuj wstępnie wszystkie pliki, które są przekazywane do odpowiednich Azure Monitor powiązanych zasobów, takich jak Log Analytics obszaru roboczego.

Użyj wykrywania zagrożeń Azure Security Center dla usług danych w celu wykrywania złośliwego oprogramowania przekazanego do kont magazynu. 

- [Informacje na temat ochrony przed złośliwym oprogramowaniem firmy Microsoft dla platformy Azure Cloud Services i Virtual Machines](../security/fundamentals/antimalware.md)

- [Zrozumienie wykrywania zagrożeń Azure Security Center dla usług danych](../security-center/azure-defender.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

## <a name="data-recovery"></a>Odzyskiwanie danych

*Aby uzyskać więcej informacji, zobacz [test dotyczący zabezpieczeń platformy Azure: odzyskiwanie danych](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: zapewnianie regularnych zautomatyzowanych kopii zapasowych

**Wskazówki**: Użyj Azure Resource Manager do eksportowania Azure monitor i powiązanych zasobów w szablonie JavaScript Object Notation (JSON), który może być używany jako kopia zapasowa dla Azure monitor i związanych z nimi konfiguracji.  Użyj Azure Automation, aby automatycznie uruchamiać skrypty kopii zapasowej. 

- [Zarządzanie obszarem roboczym Log Analytics przy użyciu szablonów Azure Resource Manager](./logs/resource-manager-workspace.md)

- [Eksport jednego i kilku zasobów do szablonu w Azure Portal](../azure-resource-manager/templates/export-template-portal.md)

- [Informacje o Azure Automation](../automation/automation-intro.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: wykonaj kompletne kopie zapasowe systemu i Utwórz kopię zapasową wszystkich kluczy zarządzanych przez klienta

**Wskazówki**: Użyj Azure Resource Manager do eksportowania Azure monitor i powiązanych zasobów w szablonie JavaScript Object Notation (JSON), który może być używany jako kopia zapasowa dla Azure monitor i związanych z nimi konfiguracji. Utwórz kopię zapasową kluczy zarządzanych przez klienta w ramach Azure Key Vault, jeśli Azure Monitor powiązane zasoby korzystają z kluczy zarządzanych przez klienta

- [Zarządzanie obszarem roboczym Log Analytics przy użyciu szablonów Azure Resource Manager](./logs/resource-manager-workspace.md)

- [Eksport jednego i kilku zasobów do szablonu w Azure Portal](../azure-resource-manager/templates/export-template-portal.md)

- [Jak utworzyć kopię zapasową kluczy magazynu kluczy na platformie Azure](/powershell/module/az.keyvault/backup-azkeyvaultkey?amp;preserve-view=true&view=azps-4.8.0)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: Weryfikuj wszystkie kopie zapasowe, w tym klucze zarządzane przez klienta

**Wskazówki**: Zapewnij okresowe wykonywanie przywracania przy użyciu Azure Resource Manager plików szablonów kopii zapasowych. Testowanie przywracania kopii zapasowych kluczy zarządzanych przez klienta.

- [Zarządzanie obszarem roboczym Log Analytics przy użyciu szablonów Azure Resource Manager](./logs/resource-manager-workspace.md)

- [Jak przywrócić klucze magazynu kluczy na platformie Azure](/powershell/module/az.keyvault/restore-azkeyvaultkey?amp;preserve-view=true&view=azps-4.8.0)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: zapewnianie ochrony kopii zapasowych i kluczy zarządzanych przez klienta

**Wskazówki**: Użyj usługi Azure DevOps, aby bezpiecznie przechowywać kod, taki jak niestandardowe zasady platformy Azure, szablony Azure Resource Manager i zarządzać nimi. Aby chronić zasoby zarządzane w usłudze Azure DevOps, można udzielić lub odmówić uprawnień określonym użytkownikom, wbudowanym grupom zabezpieczeń lub grupom zdefiniowanym w Azure Active Directory (Azure AD) w przypadku integracji z usługą Azure DevOps lub Active Directory w przypadku integracji z programem TFS.  Za pomocą kontroli dostępu opartej na rolach (Azure RBAC) można chronić klucze zarządzane przez klienta.

Ponadto Włącz ochronę Soft-Delete i przeczyszczanie w Key Vault, aby chronić klucze przed przypadkowym lub złośliwym usunięciem. Jeśli usługa Azure Storage jest używana do przechowywania kopii zapasowych szablonów Azure Resource Manager, należy włączyć funkcję usuwania nietrwałego, aby zapisywać i odzyskiwać dane po usunięciu obiektów blob lub migawek obiektów BLOB.

- [Jak przechowywać kod w usłudze Azure DevOps](/azure/devops/repos/git/gitworkflow?amp;preserve-view=true&view=azure-devops)

- [Informacje o uprawnieniach i grupach na platformie Azure DevOps](/azure/devops/organizations/security/about-permissions)

- [Jak włączyć ochronę Soft-Delete i przeczyszczania w programie Key Vault](../storage/blobs/soft-delete-blob-overview.md)

- [Soft delete for Azure Storage blobs](../storage/blobs/soft-delete-blob-overview.md) (Usuwanie nietrwałe dla obiektów blob usługi Azure Storage)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

## <a name="incident-response"></a>Reagowanie na zdarzenia

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: reagowanie na zdarzenia](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: Tworzenie przewodnika odpowiedzi na zdarzenia

**Wskazówka**: Utwórz przewodnik odpowiedzi na zdarzenia dla swojej organizacji. Upewnij się, że istnieją zarejestrowane plany reakcji na zdarzenia, które definiują wszystkie role pracowników, a także etapy obsługi zdarzeń/zarządzania od wykrywania do oceny po zdarzeniu.

- [Wskazówki dotyczące tworzenia własnego procesu reagowania na zdarzenia zabezpieczeń](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Anatomia incydentu centrum Microsoft Security Response](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [Skorzystaj z przewodnika obsługi zdarzeń związanych z bezpieczeństwem programu NIST, aby pomóc w tworzeniu własnego planu reagowania na zdarzenia](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: Tworzenie oceny incydentu i procedury priorytetyzacji

**Wskazówki**: Security Center przypisuje ważność do każdego alertu, aby pomóc w ustaleniu, które alerty należy najpierw zbadać. Ważność jest oparta na tym, jak dobrze Security Center znajduje się w wyszukiwaniu lub analizach używanych do wystawiania alertu, a także poziom pewności, że istniało złośliwe zamiar w odniesieniu do działania, które doprowadziło do alertu.

Dodatkowo jasno Oznacz subskrypcje (na przykład Produkcja, inne niż prod) przy użyciu tagów i Utwórz system nazewnictwa, aby jasno identyfikować i klasyfikować zasoby platformy Azure, szczególnie te, które przetwarzają dane poufne.  Odpowiedzialność za korygowanie alertów w oparciu o krytyczne znaczenie zasobów platformy Azure i środowisko, w którym wystąpiło zdarzenie, leży po stronie użytkownika.

- [Alerty zabezpieczeń w Centrum zabezpieczeń Azure](../security-center/security-center-alerts-overview.md)

- [Tworzenie i używanie tagów](../azure-resource-manager/management/tag-resources.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="103-test-security-response-procedures"></a>10,3: procedury odpowiedzi na zabezpieczenia testowe

**Wskazówki**: przeprowadzanie ćwiczeń w celu przetestowania możliwości reagowania na zdarzenia systemów w regularnych erzeach w celu ochrony zasobów platformy Azure. Zidentyfikuj słabe punkty i przerwy oraz popraw plan zgodnie z wymaganiami.

- [Przewodnik po publikacji NIST, który umożliwia testowanie, uczenie i wykonywanie programów dla planów i możliwości IT](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: Podaj szczegóły kontaktu dotyczącego zabezpieczeń i Skonfiguruj powiadomienia dotyczące alertów dotyczących zdarzeń związanych z zabezpieczeniami

**Wskazówki**: informacje kontaktowe dotyczące zdarzenia zabezpieczeń będą używane przez firmę Microsoft do skontaktowania się z Tobą, jeśli firma Microsoft Security Response Center (MSRC) wykryje, że dostęp do danych zostały nadane przez nielegalną lub nieautoryzowaną stronę. Przejrzyj zdarzenia po fakcie, aby upewnić się, że problemy zostały rozwiązane.

- [Jak ustawić kontakt z zabezpieczeniami Azure Security Center](../security-center/security-center-provide-security-contact-details.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: Uwzględnij alerty zabezpieczeń w systemie odpowiedzi na zdarzenia

**Wskazówki**: eksportowanie alertów i zaleceń dotyczących Azure Security Center przy użyciu funkcji eksportu ciągłego, która pomaga identyfikować zagrożenia dla zasobów platformy Azure. Eksport ciągły umożliwia wyeksportowanie alertów i zaleceń ręcznie lub w stały sposób ciągły. Możesz użyć łącznika danych Azure Security Center do przesyłania strumieniowego alertów do usługi Azure wskaźnikowej. 

- [Jak skonfigurować eksport ciągły](../security-center/continuous-export.md) 

- [Jak przesłać strumieniowo alerty do usługi Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: Automatyzowanie odpowiedzi na alerty zabezpieczeń

**Wskazówki**: Użyj funkcji automatyzacji przepływu pracy w Azure Security Center, aby automatycznie wyzwalać odpowiedzi poprzez "Logic Apps" dotyczące alertów zabezpieczeń i zaleceń dotyczących ochrony zasobów platformy Azure.

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

- Zobacz [Omówienie testu porównawczego zabezpieczeń platformy Azure w wersji 2](../security/benchmarks/overview.md)
- Dowiedz się więcej o [punktach odniesienia zabezpieczeń platformy Azure](../security/benchmarks/security-baselines-overview.md)