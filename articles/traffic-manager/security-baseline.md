---
title: Podstawa zabezpieczeń Azure dla usługi Azure Traffic Manager
description: Podstawowa usługa Azure Traffic Manager Security zawiera wskazówki i zasoby związane z wdrażaniem zaleceń dotyczących zabezpieczeń określonych w teście zabezpieczeń platformy Azure.
author: msmbaldwin
ms.service: traffic-manager
ms.topic: conceptual
ms.date: 10/01/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 62be64f3134bbbbbe2b18bef384654168f958287
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101701607"
---
# <a name="azure-security-baseline-for-azure-traffic-manager"></a>Podstawa zabezpieczeń Azure dla usługi Azure Traffic Manager

Podstawą zabezpieczeń platformy Azure dla Microsoft Azure Traffic Manager są zalecenia, które pomogą ulepszyć stan bezpieczeństwa wdrożenia. Punkt odniesienia dla tej usługi jest rysowany w [wersji 1,0 usługi Azure Security test](../security/benchmarks/overview-v1.md), która zawiera zalecenia dotyczące sposobu zabezpieczania rozwiązań w chmurze na platformie Azure z naszymi najlepszymi wskazówkami. Zawartość jest pogrupowana według **kontroli zabezpieczeń** zdefiniowanych przez usługę Azure Security test i powiązane wskazówki dotyczące usługi Azure Traffic Manager. Nie zostały wykluczone **kontrolki** , które mają zastosowanie do Traffic Manager platformy Azure.

Aby dowiedzieć się, jak platforma Azure Traffic Manager całkowicie zamapowana na test porównawczy zabezpieczeń platformy Azure, zapoznaj się z [pełnym plikiem mapowania podstawy zabezpieczeń usługi azure Traffic Manager](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="logging-and-monitoring"></a>Rejestrowanie i monitorowanie

*Aby uzyskać więcej informacji, zobacz temat [Azure Security test: rejestrowanie i monitorowanie](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2,2: Skonfiguruj centralne zarządzanie dziennikami zabezpieczeń

**Wskazówki**: Dziennik aktywności to dziennik platformy platformy Azure, który zapewnia wgląd w zdarzenia na poziomie subskrypcji. Wyślij dzienniki do obszaru roboczego Log Analytics, usługi Azure Event Hub lub konta usługi Azure Storage w celu archiwizacji. Dzienniki aktywności zapewniają wgląd w operacje wykonywane na zasobach Traffic Manager platformy Azure na poziomie płaszczyzny kontroli. Korzystając z danych dziennika aktywności platformy Azure, można określić "co, kto i kiedy" dla każdej operacji zapisu (PUT, POST, DELETE) wykonanej na poziomie płaszczyzny kontroli dla profili Traffic Manager.

Pozyskiwanie dzienników za pośrednictwem Azure Monitor do agregowania danych zabezpieczeń wygenerowanych przez urządzenia, zasoby sieciowe i inne systemy zabezpieczeń. Alternatywnie możesz włączyć i dołączyć dane do usługi Azure wskaźnikowej lub SIEM innej firmy.

- [Jak dołączyć wskaźnik na platformie Azure](../sentinel/quickstart-onboard.md)

- [Jak zbierać dzienniki platformy i metryki za pomocą Azure Monitor](../azure-monitor/essentials/diagnostic-settings.md)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: Włączanie rejestrowania inspekcji dla zasobów platformy Azure

**Wskazówki**: Włączanie rejestrowania zasobów w Traffic Manager. Są to dzienniki zasobów diagnostycznych i dostęp do danych dzienników dla profilu Traffic Manager. Ponadto Traffic Manager zapewnia pewne metryki dla poszczególnych profilów.

Włącz ustawienia diagnostyczne w programie Azure Monitor, aby uzyskać dostęp do dzienników inspekcji, zabezpieczeń i diagnostyki. Dzienniki aktywności, które są automatycznie dostępne, obejmują źródło zdarzeń, datę, użytkownika, sygnaturę czasową, adresy źródłowe, adresy docelowe i inne przydatne elementy.

- [Jak włączyć rejestrowanie zasobów](traffic-manager-diagnostic-logs.md)

- [Traffic Manager metryki i alerty](traffic-manager-metrics-alerts.md)

- [Jak zbierać dzienniki platformy i metryki za pomocą Azure Monitor](../azure-monitor/essentials/diagnostic-settings.md)

- [Informacje o rejestrowaniu i różnych typach dzienników na platformie Azure](../azure-monitor/essentials/platform-logs-overview.md)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="25-configure-security-log-storage-retention"></a>2,5: Konfigurowanie przechowywania magazynu dzienników zabezpieczeń

**Wskazówki**: w Azure monitor ustawić okres przechowywania obszaru roboczego log Analytics zgodnie z regulacjami zgodności w organizacji. Używaj kont usługi Azure Storage do przechowywania długoterminowego i archiwizowania.

- [Zmień okres przechowywania danych w Log Analytics](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

- [Jak skonfigurować zasady przechowywania dla dzienników kont usługi Azure Storage](../storage/common/manage-storage-analytics-logs.md#configure-logging)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="26-monitor-and-review-logs"></a>2,6: dzienniki monitorowania i przeglądania

**Wskazówki**: analizowanie i monitorowanie dzienników pod kątem nietypowego zachowania i regularne przeglądanie wyników. Użyj Azure Monitor i Log Analytics obszaru roboczego do przeglądania dzienników i wykonywania zapytań dotyczących danych dziennika.

Alternatywnie możesz włączyć i dołączyć dane do usługi Azure wskaźnikowej lub SIEM innej firmy. 

- [Jak dołączyć wskaźnik na platformie Azure](../sentinel/quickstart-onboard.md)

- [Wprowadzenie do Log Analytics zapytań](../azure-monitor/logs/log-analytics-tutorial.md)

- [Jak wykonywać niestandardowe zapytania w Azure Monitor](../azure-monitor/logs/get-started-queries.md)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: Włączanie alertów dla nietypowych działań

**Wskazówki**: oprócz przetwarzania i wyświetlania metryk z Traffic Manager, Azure Monitor umożliwia klientom Konfigurowanie i odbieranie alertów skojarzonych z tymi metrykami. Możesz wybrać warunki, które muszą zostać spełnione w tych metrykach, aby alert wystąpił, jak często te warunki muszą być monitorowane i jak powinny być wysyłane do Ciebie alerty.

Użyj Azure Security Center z Log Analytics obszarem roboczym do monitorowania i generowania alertów dotyczących nietypowego działania znalezionego w dziennikach i zdarzeniach zabezpieczeń.

Alternatywnie możesz włączyć i dołączyć dane do usługi Azure wskaźnikowej.

- [Tworzenie i wyświetlanie alertów metryk oraz zarządzanie nimi w usłudze Azure Monitor](traffic-manager-metrics-alerts.md)

- [Jak zarządzać alertami w Azure Security Center](../security-center/security-center-managing-and-responding-alerts.md)

- [Jak ostrzec dane dziennika usługi log Analytics](../azure-monitor/alerts/tutorial-response.md)

- [Jak dołączyć wskaźnik na platformie Azure](../sentinel/quickstart-onboard.md)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

## <a name="identity-and-access-control"></a>Tożsamość i kontrola dostępu

*Aby uzyskać więcej informacji, zobacz [test dotyczący zabezpieczeń platformy Azure: tożsamość i kontrola dostępu](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: obsługa spisu kont administracyjnych

**Wskazówki**: Kontrola dostępu oparta na rolach (Azure RBAC) umożliwia zarządzanie dostępem do zasobów platformy Azure za pomocą przypisań ról. Te role można przypisać do użytkowników, grup, podmiotów usługi i tożsamości zarządzanych. Istnieją wstępnie zdefiniowane wbudowane role dla niektórych zasobów i te role można spisować lub odpytać za pomocą narzędzi, takich jak interfejs wiersza polecenia platformy Azure, Azure PowerShell lub Azure Portal.

W Menedżer zasobów punkty końcowe z dowolnych subskrypcji można dodać do Traffic Manager, o ile osoba konfigurująca profil Traffic Manager ma dostęp do odczytu do punktu końcowego.

- [Przypisywanie ról platformy Azure przy użyciu Azure Portal](../role-based-access-control/role-assignments-portal.md)

- [Jak uzyskać rolę katalogu w usłudze Azure AD przy użyciu programu PowerShell](/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

- [Jak uzyskać członków roli katalogu w usłudze Azure AD przy użyciu programu PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: Użyj dedykowanych kont administracyjnych

**Wskazówki**: Tworzenie standardowych procedur operacyjnych dotyczących korzystania z dedykowanych kont administracyjnych. Użyj Azure Security Center Zarządzanie tożsamościami i dostępem, aby monitorować liczbę kont administracyjnych.

Ponadto, aby ułatwić śledzenie dedykowanych kont administracyjnych, można użyć zaleceń z Azure Security Center lub wbudowanych zasad platformy Azure, takich jak:

- Do subskrypcji powinien być przypisany więcej niż jeden właściciel
- Przestarzałe konta z uprawnieniami właściciela powinny zostać usunięte z subskrypcji
- Konta zewnętrzne z uprawnieniami właściciela powinny zostać usunięte z subskrypcji

Możesz również włączyć dostęp just in Time przy użyciu Azure AD Privileged Identity Management i Azure Resource Manager. 

- [Dowiedz się więcej o Privileged Identity Management](../active-directory/privileged-identity-management/index.yml)

- [Jak używać Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3,4: Korzystaj z logowania jednokrotnego (SSO) z usługą Azure Active Directory

**Wskazówki**: usługa Azure Traffic Manager obsługuje uwierzytelnianie SSO przy użyciu Azure Active Directory. Zmniejsz liczbę tożsamości i poświadczeń, które użytkownicy muszą zarządzać, włączając Logowanie jednokrotne w usłudze przy użyciu istniejących tożsamości w organizacji.

- [Opis logowania jednokrotnego w usłudze Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Użyj uwierzytelniania wieloskładnikowego, aby uzyskać dostęp oparty na Azure Active Directory

**Wskazówki**: włączanie Azure Active Directory Multi-Factor Authentication i postępuj zgodnie z zaleceniami związanymi z zarządzaniem tożsamościami i dostępem Azure Security Center.

- [Jak włączyć usługę MFA na platformie Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Jak monitorować tożsamość i dostęp w Azure Security Center](../security-center/security-center-identity-access.md)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: Używaj dedykowanych maszyn (uprzywilejowany dostęp do stacji roboczych) dla wszystkich zadań administracyjnych

**Wskazówki**: Użyj bezpiecznej, zarządzanej przez platformę Azure stacji roboczej (znanej także jako stacja robocza dostępu uprzywilejowanego lub dostępem uprzywilejowanym) dla zadań administracyjnych, które wymagają podwyższonego poziomu uprawnień.

- [Informacje na temat bezpiecznych stacji roboczych zarządzanych przez platformę Azure](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Jak włączyć usługę MFA na platformie Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: Rejestruj i Ostrzegaj o podejrzanych działaniach z kont administracyjnych

**Wskazówki**: Użyj Azure Active Directory (Azure AD) PRIVILEGED Identity Management (PIM) do generowania dzienników i alertów w przypadku wystąpienia podejrzanych lub niebezpiecznych działań w środowisku.

Ponadto należy użyć funkcji wykrywania ryzyka usługi Azure AD do wyświetlania alertów i raportów na temat ryzykownego zachowania użytkowników.

- [Jak wdrożyć Privileged Identity Management](../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [Informacje o wykrywaniu ryzyka w usłudze Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="38-manage-azure-resources-only-from-approved-locations"></a>3,8: Zarządzaj zasobami platformy Azure tylko z zatwierdzonych lokalizacji

**Wskazówki**: Użyj nazw lokalizacji usługi Azure AD, aby zezwolić na dostęp tylko z określonych logicznych grup zakresów adresów IP lub krajów/regionów.

- [Jak skonfigurować nazwane lokalizacje usługi Azure AD](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="39-use-azure-active-directory"></a>3,9: Użyj Azure Active Directory

**Wskazówki**: Użyj Azure Active Directory (Azure AD) jako centralnego systemu uwierzytelniania i autoryzacji. Usługa Azure AD chroni dane przy użyciu silnego szyfrowania danych przechowywanych i przesyłanych. Usługa Azure AD również Sole, skróty i bezpieczne przechowywanie poświadczeń użytkownika.

- [Jak utworzyć i skonfigurować wystąpienie usługi Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: regularnie Przeglądaj i Uzgodnij dostęp użytkowników

**Wskazówki**: usługa Azure AD udostępnia dzienniki, które ułatwiają odnajdywanie starych kont. Ponadto za pomocą przeglądów tożsamości i dostępu w usłudze Azure AD można efektywnie zarządzać członkostwem w grupach, dostępem do aplikacji dla przedsiębiorstw i przypisaniami ról. Dostęp użytkowników może być regularnie przeglądany, aby upewnić się, że tylko Ci użytkownicy mają ciągły dostęp. 

- [Informacje o raportowaniu usługi Azure AD](../active-directory/reports-monitoring/index.yml)

- [Jak korzystać z przeglądów tożsamości i dostępu w usłudze Azure AD](../active-directory/governance/access-reviews-overview.md)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: Monitor próbuje uzyskać dostęp do zdezaktywowanych poświadczeń

**Wskazówki**: masz dostęp do źródeł zdarzeń związanych z logowaniem do usługi Azure AD, inspekcją i ryzykiem związanym z dziennikami, które umożliwiają integrację z dowolnym narzędziem Siem/monitoring.

Proces ten można usprawnić, tworząc ustawienia diagnostyczne dla kont użytkowników usługi Azure AD i wysyłając dzienniki inspekcji i dzienniki logowania do obszaru roboczego Log Analytics. Żądane alerty można skonfigurować w obszarze roboczym Log Analytics.

- [Jak zintegrować dzienniki aktywności platformy Azure z Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: odchylenia zachowania podczas logowania do konta

**Wskazówki**: użyj funkcji Azure AD Identity Protection, aby skonfigurować automatyczne odpowiedzi na wykryte podejrzane działania związane z tożsamościami użytkowników. Możesz również pozyskać dane do usługi Azure wskaźnikowej na potrzeby dalszej analizy.

- [Jak wyświetlić ryzykowne logowania w usłudze Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

- [Jak skonfigurować i włączyć zasady dotyczące ryzyka związanego z ochroną tożsamości](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Jak dołączyć wskaźnik na platformie Azure](../sentinel/quickstart-onboard.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

## <a name="data-protection"></a>Ochrona danych

*Aby uzyskać więcej informacji, zobacz [Azure Security test: Data Protection](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: przechowywanie spisu poufnych informacji

**Wskazówki**: Użyj tagów, aby pomóc w śledzeniu zasobów platformy Azure, które przechowują lub przetwarzają informacje poufne.

- [Tworzenie i używanie tagów](../azure-resource-manager/management/tag-resources.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4,6: Korzystaj z usługi Azure RBAC, aby zarządzać dostępem do zasobów

**Wskazówki**: Kontrola dostępu oparta na rolach (Azure RBAC) umożliwia zarządzanie dostępem do zasobów platformy Azure za pomocą przypisań ról. Te role można przypisać do użytkowników, grup, podmiotów usługi i tożsamości zarządzanych. Istnieją wstępnie zdefiniowane wbudowane role dla niektórych zasobów i te role można spisować lub odpytać za pomocą narzędzi, takich jak interfejs wiersza polecenia platformy Azure, Azure PowerShell lub Azure Portal.

W Menedżer zasobów punkty końcowe z dowolnych subskrypcji można dodać do Traffic Manager, o ile osoba konfigurująca profil Traffic Manager ma dostęp do odczytu do punktu końcowego.

Usługa Azure Traffic Manager ma wstępnie zdefiniowaną rolę platformy Azure o nazwie "Współautor Traffic Manager", którą można przypisać do użytkowników.

- [Przypisywanie ról platformy Azure przy użyciu Azure Portal](../role-based-access-control/role-assignments-portal.md)

- [Rola współautora Traffic Manager](../role-based-access-control/built-in-roles.md#traffic-manager-contributor)

- [Jak uzyskać rolę katalogu w usłudze Azure AD przy użyciu programu PowerShell](/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

- [Jak uzyskać członków roli katalogu w usłudze Azure AD przy użyciu programu PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: Rejestruj i Ostrzegaj o zmianach krytycznych zasobów platformy Azure

**Wskazówki**: Użyj Azure monitor z dziennikiem aktywności platformy Azure, aby utworzyć alerty dla sytuacji, w których zmiany są wprowadzane do usługi Azure Traffic Manager, a także innych krytycznych lub powiązanych zasobów.

- [Jak utworzyć alerty dla zdarzeń dziennika aktywności platformy Azure](../azure-monitor/alerts/alerts-activity-log.md)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

## <a name="inventory-and-asset-management"></a>Zarządzanie magazynem i zasobami

*Aby uzyskać więcej informacji, zobacz temat [Azure Security test: Inventory and Asset Management](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: Użyj rozwiązania automatycznego odnajdywania zasobów

**Wskazówki**: Użyj grafu zasobów platformy Azure do wykonywania zapytań dotyczących wszystkich zasobów (takich jak obliczenia, magazyn, Sieć, porty i protokoły itp.) w ramach subskrypcji.  Upewnij się, że masz odpowiednie uprawnienia (odczyt) w dzierżawie i Wylicz wszystkie subskrypcje platformy Azure oraz zasoby w Twoich subskrypcjach.

Mimo że klasyczne zasoby platformy Azure mogą zostać odnalezione za pośrednictwem Eksploratora Azure Resource Graph, zdecydowanie zaleca się tworzenie i używanie Azure Resource Manager zasobów do przodu.

- [Jak tworzyć zapytania za pomocą eksploratora usługi Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

- [Jak wyświetlić subskrypcje platformy Azure](/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

- [Opis kontroli RBAC platformy Azure](../role-based-access-control/overview.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="62-maintain-asset-metadata"></a>6,2: Konserwowanie metadanych zasobów

**Wskazówki**: Użyj nazwy zasad, opisu i kategorii, aby logicznie organizować zasoby zgodnie z taksonomią.

- [Aby uzyskać więcej informacji dotyczących tagowania elementów zawartości, zobacz Przewodnik po nazewnictwu zasobów i znakowaniu](/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=%2fazure%2fazure-resource-manager%2fmanagement%2ftoc.json)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: Usuń nieautoryzowane zasoby platformy Azure

**Wskazówki**: używanie tagowania, grup zarządzania i oddzielnych subskrypcji, gdzie to konieczne, do organizowania i śledzenia zasobów platformy Azure. Regularnie Uzgadniaj spis i zapewnij, że nieautoryzowane zasoby są usuwane z subskrypcji w odpowiednim czasie.

Ponadto należy używać Azure Policy do umieszczania ograniczeń dotyczących typu zasobów, które można utworzyć w subskrypcjach klientów, przy użyciu następujących wbudowanych definicji zasad:

- Niedozwolone typy zasobów
- Dozwolone typy zasobów

Możesz również utworzyć niestandardowe definicje Azure Policy, aby ograniczyć bardziej szczegółowe ustawienia zasobów.

- [Jak utworzyć dodatkowe subskrypcje platformy Azure](../cost-management-billing/manage/create-subscription.md)

- [Tworzenie grup zarządzania](../governance/management-groups/create-management-group-portal.md)

- [Tworzenie i używanie tagów](../azure-resource-manager/management/tag-resources.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6,4: Definiowanie i obsługa spisu zatwierdzonych zasobów platformy Azure

**Wskazówki**: Tworzenie spisu zatwierdzonych zasobów platformy Azure oraz zatwierdzonego oprogramowania do zasobów obliczeniowych zgodnie z potrzebami organizacji.

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: Monitoruj niezatwierdzone zasoby platformy Azure

**Wskazówki**: Użyj Azure Policy, aby wprowadzić ograniczenia dotyczące typu zasobów, które mogą być tworzone w ramach subskrypcji.

Usługa Azure Resource Graph umożliwia wykonywanie zapytań dotyczących zasobów i odnajdywanie ich w ramach subskrypcji.  Upewnij się, że wszystkie zasoby platformy Azure obecne w środowisku są zatwierdzone.

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

- [Jak tworzyć zapytania przy użyciu grafu zasobów platformy Azure](../governance/resource-graph/first-query-portal.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="69-use-only-approved-azure-services"></a>6,9: Używaj tylko zatwierdzonych usług platformy Azure

**Wskazówki**: Użyj Azure Policy, aby umieścić ograniczenia dotyczące typu zasobów, które mogą być tworzone w subskrypcjach klientów, przy użyciu następujących wbudowanych definicji zasad:

- Niedozwolone typy zasobów
- Dozwolone typy zasobów

Możesz również utworzyć niestandardowe definicje Azure Policy, aby ograniczyć bardziej szczegółowe ustawienia zasobów.

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

- [Jak odmówić określonego typu zasobu za pomocą Azure Policy](../governance/policy/samples/index.md)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: Ogranicz możliwość korzystania przez użytkowników z Azure Resource Manager

**Wskazówki**: Skonfiguruj dostęp warunkowy platformy Azure, aby ograniczyć możliwość korzystania przez użytkowników z Azure Resource Manager przez skonfigurowanie "blokowania dostępu" dla aplikacji "Microsoft Azure Management".

- [Jak skonfigurować dostęp warunkowy w celu blokowania dostępu do Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

## <a name="secure-configuration"></a>Bezpieczna konfiguracja

*Aby uzyskać więcej informacji, zobacz temat [Azure Security test: bezpieczna konfiguracja](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: Ustanów bezpieczne konfiguracje dla wszystkich zasobów platformy Azure

**Wskazówki**: Definiowanie i implementowanie standardowych konfiguracji zabezpieczeń dla usługi Azure Traffic Manager przy użyciu Azure Policy. Użyj aliasów Azure Policy w przestrzeni nazw "Microsoft. Network", aby utworzyć zasady niestandardowe do inspekcji lub wymuszania konfiguracji magazynów Recovery Services.

- [Jak wyświetlić dostępne aliasy Azure Policy](/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: obsługa bezpiecznych konfiguracji zasobów platformy Azure

**Wskazówki**: Użyj Azure Policy [Odmów] i [Wdróż, jeśli nie istnieje], aby wymusić bezpieczne ustawienia dla zasobów platformy Azure.

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

- [Zrozumienie efektów Azure Policy](../governance/policy/concepts/effects.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: bezpiecznie przechowuj konfigurację zasobów platformy Azure

**Wskazówki**: Jeśli używasz niestandardowych definicji Azure Policy, Użyj usługi Azure DevOps lub Azure Repos, aby bezpiecznie przechowywać kod i zarządzać nim.

- [Jak przechowywać kod w usłudze Azure DevOps](/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [Dokumentacja Azure Repos](/azure/devops/repos/index?view=azure-devops)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: Wdrażanie narzędzi do zarządzania konfiguracją dla zasobów platformy Azure

**Wskazówki**: Użyj wbudowanych definicji Azure Policy, a także aliasów Azure Policy w przestrzeni nazw "Microsoft. Network", aby utworzyć zasady niestandardowe na potrzeby alertów, inspekcji i wymuszania konfiguracji systemu. Dodatkowo opracowuj proces i potok na potrzeby zarządzania wyjątkami zasad.

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: Zaimplementuj automatyczne monitorowanie konfiguracji dla zasobów platformy Azure

**Wskazówki**: Użyj wbudowanych definicji Azure Policy, a także aliasów Azure Policy w przestrzeni nazw "Microsoft. Network", aby utworzyć zasady niestandardowe na potrzeby alertów, inspekcji i wymuszania konfiguracji systemu. Użyj Azure Policy [Audit], [Odmów] i [Wdróż, jeśli nie istnieje], aby automatycznie wymuszać konfiguracje dla zasobów platformy Azure.

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

## <a name="malware-defense"></a>Ochrona przed złośliwym oprogramowaniem

*Aby uzyskać więcej informacji, zobacz temat [Azure Security test: Obrona złośliwego oprogramowania](../security/benchmarks/security-control-malware-defense.md).*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: przeskanuj pliki przed przekazaniem do zasobów platformy Azure, które nie są obliczeniowe

**Wskazówki**: oprogramowanie chroniące przed złośliwym oprogramowaniem firmy Microsoft jest włączone na podstawowym hoście obsługującym usługi platformy Azure (na przykład Azure Traffic Manager), ale nie jest ono uruchamiane w treści klienta.

Ponosisz odpowiedzialność za wstępne skanowanie zawartości przekazywanej do zasobów platformy Azure, które nie są obliczeniowe. Firma Microsoft nie może uzyskać dostępu do danych klienta i w związku z tym nie może przeprowadzić skanowania zawartości klienta przez oprogramowanie chroniące przed złośliwym kodem.

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

## <a name="incident-response"></a>Reagowanie na zdarzenia

*Aby uzyskać więcej informacji, zobacz temat [Azure Security test: odpowiedź na zdarzenia](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: Tworzenie przewodnika odpowiedzi na zdarzenia

**Wskazówki**: Tworzenie przewodnika odpowiedzi na zdarzenia dla organizacji. Upewnij się, że istnieją jednokierunkowe plany reagowania na zdarzenia, które definiują wszystkie role pracowników, a także etapy obsługi zdarzeń i zarządzania z wykrywania na potrzeby przeglądu po zdarzeniu. 

- [Wskazówki dotyczące tworzenia własnego procesu reagowania na zdarzenia zabezpieczeń](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Anatomia incydentu centrum Microsoft Security Response](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [Skorzystaj z przewodnika obsługi zdarzeń zabezpieczeń komputera NIST, aby pomóc w tworzeniu własnego planu reagowania na zdarzenia](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: Tworzenie oceny incydentu i procedury priorytetyzacji

**Wskazówki**: Azure Security Center przypisuje ważność do każdego alertu, aby pomóc w ustaleniu, które alerty należy najpierw zbadać. Ważność jest oparta na tym, jak dobrze Security Center znajduje się w wyszukiwaniu lub analitycznie używanym do wystawiania alertu, a także poziom pewności, że istniało złośliwy wpływ na działanie, które spowodowało wygenerowanie alertu.

Ponadto należy oznaczyć subskrypcje przy użyciu tagów i utworzyć system nazewnictwa, aby identyfikować i klasyfikować zasoby platformy Azure, szczególnie te, które przetwarzają poufne dane.  Odpowiedzialność za korygowanie alertów zależy od zagrożenia dla zasobów platformy Azure i środowiska, w którym wystąpiło zdarzenie.

- [Alerty zabezpieczeń w Centrum zabezpieczeń Azure](../security-center/security-center-alerts-overview.md)

- [Organizowanie zasobów platformy Azure przy użyciu tagów](../azure-resource-manager/management/tag-resources.md)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="103-test-security-response-procedures"></a>10,3: procedury odpowiedzi na zabezpieczenia testowe

**Wskazówki**: przeprowadzanie ćwiczeń w celu przetestowania możliwości reagowania na zdarzenia systemów w regularnych erzeach w celu ochrony zasobów platformy Azure. Zidentyfikuj słabe punkty i przerwy, a następnie Popraw plan odpowiedzi zgodnie z potrzebami.

- [Publikacja NIST — Przewodnik dotyczący testowania, uczenia i ćwiczeń programów dla planów i możliwości IT](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: Podaj szczegóły kontaktu dotyczącego zabezpieczeń i Skonfiguruj powiadomienia dotyczące alertów dotyczących zdarzeń związanych z zabezpieczeniami

**Wskazówki**: informacje kontaktowe dotyczące zdarzenia zabezpieczeń będą używane przez firmę Microsoft do skontaktowania się z Tobą, jeśli firma Microsoft Security Response Center (MSRC) wykryje, że dostęp do danych zostały nadane przez nielegalną lub nieautoryzowaną stronę. Przejrzyj zdarzenia po fakcie, aby upewnić się, że problemy zostały rozwiązane.

- [Jak ustawić kontakt ds. zabezpieczeń usługi Azure Security Center](../security-center/security-center-provide-security-contact-details.md)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: Uwzględnij alerty zabezpieczeń w systemie odpowiedzi na zdarzenia

**Wskazówki**: eksportowanie alertów i zaleceń dotyczących Azure Security Center przy użyciu funkcji eksportu ciągłego, która pomaga identyfikować zagrożenia dla zasobów platformy Azure. Eksport ciągły umożliwia wyeksportowanie alertów i zaleceń ręcznie lub w stały sposób ciągły. Możesz użyć łącznika danych Azure Security Center do przesyłania strumieniowego alertów do usługi Azure wskaźnikowej.

- [Jak skonfigurować eksport ciągły](../security-center/continuous-export.md)

- [Jak przesłać strumieniowo alerty do usługi Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: Automatyzowanie odpowiedzi na alerty zabezpieczeń

**Wskazówki**: korzystanie z funkcji automatyzacji przepływu pracy Azure Security Center do automatycznego wyzwalania odpowiedzi na alerty zabezpieczeń i zalecenia dotyczące ochrony zasobów platformy Azure.

- [Jak skonfigurować automatyzację przepływu pracy w Security Center](../security-center/workflow-automation.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

## <a name="penetration-tests-and-red-team-exercises"></a>Testy penetracyjne i ćwiczenia typu „red team”

*Aby uzyskać więcej informacji, zobacz test [porównawczy zabezpieczeń platformy Azure: testy penetracji i czerwone ćwiczenia zespołu](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: Przeprowadź regularne testowanie penetracji zasobów platformy Azure i zadbaj o skorygowanie wszystkich krytycznych ustaleń dotyczących zabezpieczeń

**Wskazówki**: Postępuj zgodnie z regułami testowania penetracji Microsoft Cloud, aby upewnić się, że testy penetracji nie naruszają zasad firmy Microsoft. Korzystaj ze strategii firmy Microsoft i wykonywania testów typu „red team” i testów na żywo w zarządzanej przez firmę Microsoft infrastrukturze, usługach i aplikacjach w chmurze.

- [Reguły testów penetracyjnych zaangażowania](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Testy typu „red team” w chmurze firmy Microsoft](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Współużytkowane

## <a name="next-steps"></a>Następne kroki

- Zobacz [test porównawczy zabezpieczeń platformy Azure](../security/benchmarks/overview.md)
- Dowiedz się więcej o [punktach odniesienia zabezpieczeń platformy Azure](../security/benchmarks/security-baselines-overview.md)