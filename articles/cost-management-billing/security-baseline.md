---
title: Punkt odniesienia zabezpieczeń platformy Azure dla usługi Cost Management
description: Punkt odniesienia zabezpieczeń usługi Cost Management zawiera wskazówki proceduralne i zasoby związane z wdrażaniem zaleceń dotyczących zabezpieczeń określonych w teście porównawczym zabezpieczeń platformy Azure.
author: msmbaldwin
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 11/16/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 40baab2a4c577ee96bcebeb4880235a81810b2b4
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94660325"
---
# <a name="azure-security-baseline-for-cost-management"></a>Punkt odniesienia zabezpieczeń platformy Azure dla usługi Cost Management

Ten punkt odniesienia zabezpieczeń stosuje wytyczne z [wersji 2.0 testu porównawczego zabezpieczeń platformy Azure](../security/benchmarks/overview.md) do usługi Azure Cost Management. Test porównawczy zabezpieczeń platformy Azure zawiera zalecenia dotyczące sposobu zabezpieczania rozwiązań w chmurze na platformie Azure. Zawartość jest pogrupowana według **mechanizmów kontroli zabezpieczeń** definiowanych przez test porównawczy zabezpieczeń platformy Azure oraz powiązane wskazówki dotyczące usługi Cost Management. **Mechanizmy kontroli**, które nie mają zastosowania do usługi Cost Management zostały wykluczone.

Aby dowiedzieć się, jak usługa Cost Management jest w pełni mapowana do testu porównawczego zabezpieczeń platformy Azure, zapoznaj się z tematem [pełny plik mapowania punktu odniesienia zabezpieczeń usługi Cost Management](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="identity-management"></a>Zarządzanie tożsamością

*Aby uzyskać więcej informacji, zobacz [Test porównawczy zabezpieczeń platformy Azure: zarządzanie tożsamościami](/azure/security/benchmarks/security-controls-v2-identity-management).*

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>ZT-1: Ujednolicenie usługi Azure Active Directory jako centralnego systemu obsługi tożsamości i uwierzytelniania

**Wskazówki**: usługa Azure Cost Management jest zintegrowana z usługą Azure Active Directory (Azure AD), która jest domyślną usługą zarządzania tożsamościami i dostępem platformy Azure. Należy przeprowadzić standaryzację w oparciu o usługę Azure AD, aby zarządzać tożsamościami i dostępem w organizacji w następujących obszarach:

- Zasoby w chmurze firmy Microsoft, takie jak Azure Portal, Azure Storage, Azure Virtual Machines (Linux i Windows), Azure Key Vault, PaaS i SaaS.

- Zasoby organizacji, takie jak aplikacje na platformie Azure lub formowe zasoby sieciowe.

Zabezpieczenie usługi Azure AD powinno być jednym z najważniejszych priorytetów w zakresie zabezpieczeń w chmurze organizacji. Usługa Azure AD oferuje wskaźnik bezpieczeństwa tożsamości, który pomaga ocenić stan zabezpieczeń tożsamości względem zaleceń firmy Microsoft dotyczących najlepszych rozwiązań. Użyj tego wskaźnika, aby ocenić, jak ściśle Twoja konfiguracja spełnia zalecenia dotyczące najlepszych rozwiązań oraz poprawić stan zabezpieczeń.

Uwaga: usługa Azure AD obsługuje zewnętrznych dostawców tożsamości, którzy umożliwiają użytkownikom nieposiadającym konta Microsoft logowanie się do aplikacji i zasobów za pomocą tożsamości zewnętrznej.

- [Dzierżawa w usłudze Azure AD](../active-directory/develop/single-and-multi-tenant-apps.md)

- [Jak utworzyć i skonfigurować wystąpienie usługi Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

- [Definiowanie dzierżaw usługi Azure AD](https://azure.microsoft.com/resources/securing-azure-environments-with-azure-active-directory/)  

- [Korzystanie z zewnętrznych dostawców tożsamości dla aplikacji](/azure/active-directory/b2b/identity-providers)

- [Co to jest wskaźnik bezpieczeństwa tożsamości w usłudze Azure AD](../active-directory/fundamentals/identity-secure-score.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="im-3-use-azure-ad-single-sign-on-sso-for-application-access"></a>ZT-3: Korzystanie z logowania jednokrotnego usługi Azure AD na potrzeby dostępu do aplikacji

**Wskazówki**: usługa Azure Cost Management używa usługi Azure Active Directory, aby zapewnić zarządzanie tożsamościami i dostępem do zasobów platformy Azure, aplikacji w chmurze i aplikacji lokalnych. Dotyczy to zarówno tożsamości przedsiębiorstwa, takich jak pracownicy, jak i tożsamości zewnętrznych, takich jak partnerzy, sprzedawcy i dostawcy. Umożliwia to zarządzanie dostępem do danych i zasobów organizacji w środowisku lokalnym i w chmurze oraz zabezpieczenie tego dostępu za pomocą logowania jednokrotnego. Połącz wszystkich użytkowników, aplikacje i urządzenia z usługą Azure AD w celu zapewnienia bezproblemowego, bezpiecznego dostępu, lepszego wglądu i kontroli.

- [Informacje na temat logowania jednokrotnego do aplikacji za pomocą usługi Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>ZT-4: Używanie mechanizmów silnego uwierzytelniania dla każdego dostępu opartego na usłudze Azure Active Directory

**Wskazówki**: usługa Azure Cost Management jest zintegrowana z usługą Azure AD, która obsługuje mechanizmy silnego uwierzytelniania za pomocą uwierzytelniania wieloskładnikowego (MFA) i silnych metod bez hasła.

- Uwierzytelnianie wieloskładnikowe — włącz uwierzytelnianie wieloskładnikowe w usłudze Azure AD i postępuj zgodnie z zaleceniami dotyczącymi zarządzania tożsamościami i dostępem w usłudze Azure Security Center, aby zapoznać się z najlepszymi rozwiązaniami w zakresie konfiguracji uwierzytelniania wieloskładnikowego. Uwierzytelnianie wieloskładnikowe można wymusić dla wszystkich, wybranych użytkowników lub na poziomie poszczególnych użytkowników na podstawie warunków logowania i czynników ryzyka.

- Uwierzytelnianie bez hasła — dostępne są trzy opcje uwierzytelniania bez hasła: usługa Windows Hello dla firm, aplikacja Microsoft Authenticator i lokalne metody uwierzytelniania, takie jak karty inteligentne.

W przypadku administratorów i użytkowników uprzywilejowanych należy upewnić się, że używany jest najwyższy poziom metody silnego uwierzytelniania, a następnie wprowadzić odpowiednią politykę silnego uwierzytelniania dla innych użytkowników.

- [Jak włączyć uwierzytelnianie wieloskładnikowe na platformie Azure](../active-directory/authentication/howto-mfa-getstarted.md) 

- [Wprowadzenie do opcji uwierzytelniania bez hasła w przypadku usługi Azure Active Directory](../active-directory/authentication/concept-authentication-passwordless.md) 

- [Domyślne zasady haseł usługi Azure AD](../active-directory/authentication/concept-sspr-policy.md#password-policies-that-only-apply-to-cloud-user-accounts)

- [Eliminowanie nieprawidłowych haseł przy użyciu ochrony haseł w usłudze Azure AD](../active-directory/authentication/concept-password-ban-bad.md)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="im-5-monitor-and-alert-on-account-anomalies"></a>ZT-5: Monitorowanie anomalii kont i wyświetlanie alertów

**Wskazówki**: usługa Azure Cost Management jest zintegrowana z usługą Azure Active Directory, w której zapewnia następujące źródła danych:

- Logowania — raporty aktywności logowania zawierają informacje na temat użycia zarządzanych aplikacji i działań użytkownika związane z logowaniem.

- Dzienniki inspekcji — udostępnia możliwość śledzenia wszystkich zmian wprowadzanych przez różne funkcje usługi Azure AD za pomocą dzienników. Działania umieszczane w dziennikach inspekcji to na przykład zmiany wprowadzone w dowolnych zasobach usługi Azure AD, takie jak dodawanie lub usuwanie użytkowników, aplikacji, grup, ról i zasad.

- Ryzykowne logowania — ryzykowne logowanie jest wskaźnikiem próby logowania, które mogło zostać wykonane przez osobę, która nie jest prawowitym właścicielem konta użytkownika.

- Użytkownicy oflagowani w związku z ryzykiem — ryzykowny użytkownik jest wskaźnikiem konta użytkownika, którego bezpieczeństwo mogło zostać naruszone.

Te źródła danych można zintegrować z usługami Azure Monitor, Azure Sentinel lub systemami SIEM innych firm.

Usługa Azure Security Center może również wysyłać alerty dotyczące określonych podejrzanych działań, takich jak nadmierna liczba nieudanych prób uwierzytelnienia, czy przestarzałe konta w subskrypcji.

Usługa Azure Advanced Threat Protection (ATP) to rozwiązanie z zakresu zabezpieczeń, które może używać sygnałów usługi Active Directory do identyfikowania, wykrywania i badania zaawansowanych zagrożeń, naruszonych tożsamości i złośliwych działań wewnętrznych.

- [Raporty dotyczące inspekcji w usłudze Azure Active Directory](../active-directory/reports-monitoring/concept-audit-logs.md) 

- [Jak wyświetlić ryzykowne logowania w usłudze Azure AD](/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

- [Identyfikowanie użytkowników usługi Azure AD oflagowanych na skutek ryzykownego działania](/azure/active-directory/reports-monitoring/concept-user-at-risk) 

- [Jak monitorować działania użytkowników związane z tożsamościami i dostępem w usłudze Azure Security Center](../security-center/security-center-identity-access.md) 

- [Alerty w module analizy zagrożeń usługi Azure Security Center](../security-center/alerts-reference.md) 

- [Jak zintegrować dzienniki aktywności platformy Azure z usługą Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

## <a name="privileged-access"></a>Dostęp uprzywilejowany

*Aby uzyskać więcej informacji, zobacz [Test porównawczy zabezpieczeń platformy Azure: Dostęp uprzywilejowany](/azure/security/benchmarks/security-controls-v2-privileged-access).*

### <a name="pa-1-protect-and-limit-highly-privileged-users"></a>DU-1: Ochrona i ograniczanie użytkowników o wysokim poziomie uprawnień

**Wskazówki**: usługa Cost Management dla umów Enterprise Agreement (EA) ma poniższe konto o wysokim poziomie uprawnień.

- Administratora przedsiębiorstwa

Zaleca się przeprowadzanie rutynowego przeglądu użytkowników przypisanych do ról w ramach umowy Enterprise Agreement (EA).

Zalecane jest również, aby ograniczyć liczbę kont lub ról z wysokim poziomem uprawnień oraz zapewnić ochronę tych kont na podwyższonym poziomie, ponieważ użytkownicy z tymi uprawnieniami mogą bezpośrednio lub pośrednio odczytywać i modyfikować każdy zasób w środowisku platformy Azure.

Można włączyć uprzywilejowany dostęp just-in-time (JIT) do zasobów platformy Azure i usługi Azure AD przy użyciu usługi Azure AD Privileged Identity Management (PIM). Dostęp JIT polega na przyznawaniu uprawnień tymczasowych do wykonywania zadań uprzywilejowanych tylko wtedy, gdy użytkownicy ich potrzebują. Usługa PIM może również generować alerty zabezpieczeń w przypadku podejrzanych lub niebezpiecznych działań w ramach organizacji usługi Azure AD.

- [Zarządzanie rolami w usłudze Azure Enterprise](manage/understand-ea-roles.md) 

- [Uprawnienia ról administratora w usłudze Azure AD](/azure/active-directory/users-groups-roles/directory-assign-admin-roles) 

- [Używanie alertów zabezpieczeń usługi Azure Privileged Identity Management](../active-directory/privileged-identity-management/pim-how-to-configure-security-alerts.md) 

- [Zabezpieczanie uprzywilejowanego dostępu dla wdrożeń hybrydowych i wdrożeń w chmurze w usłudze Azure AD](/azure/active-directory/users-groups-roles/directory-admin-roles-secure)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="pa-3-review-and-reconcile-user-access-regularly"></a>DU-3: Regularny przegląd i uzgadnianie dostępu użytkowników

**Wskazówki**: usługa Azure Cost Management opiera się na odpowiednim dostępie w celu wyświetlania danych dotyczących kosztów organizacji i zarządzania nimi. Dostęp jest kontrolowany przy użyciu kontroli dostępu opartej na rolach (Azure RBAC) na poziomie subskrypcji i za pośrednictwem ról administracyjnych, z umowami Enterprise Agreement (EA) lub Microsoft Customer Agreement (MCA) dla zakresów rozliczeń. Należy regularnie przeprowadzać inspekcję i przegląd regularnie udzielonego dostępu, aby upewnić się, że użytkownicy lub grupy mają wymagany dostęp.

- [Manage access to billing information for Azure (Zarządzanie dostępem do informacji rozliczeniowych dla platformy Azure)](manage/manage-billing-access.md)

- [Przypisywanie dostępu do danych rozwiązania Cost Management](costs/assign-access-acm-data.md)

- [Zarządzanie rolami w usłudze Azure Enterprise](manage/understand-ea-roles.md)

- [Understand Microsoft Customer Agreement administrative roles in Azure](manage/understand-mca-roles.md) (Omówienie ról administracyjnych dla Umowy klienta firmy Microsoft na platformie Azure)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="pa-7-follow-just-enough-administration-least-privilege-principle"></a>DU-7: Przestrzeganie podejścia wystarczającego zakresu administracji (zasada stosowania najniższych uprawnień) 

**Wskazówki**: usługa Azure Cost Management integruje się z kontrolą dostępu opartą na rolach (RBAC) na platformie Azure w celu zarządzania zasobami (np. budżetami, zapisanymi raportami itp.). Usługa Azure RBAC umożliwia zarządzanie dostępem do zasobów platformy Azure za pomocą przypisań ról. Role te można przypisywać do użytkowników, grup i jednostek usługi. Dla niektórych zasobów istnieją wstępnie zdefiniowane wbudowane role i te role można zinwentaryzować lub wykonywać względem nich zapytania za pomocą narzędzi takich jak interfejs wiersza polecenia platformy Azure, program Azure PowerShell lub witryna Azure Portal. Uprawnienia przypisywane do zasobów z użyciem kontroli dostępu opartej na rolach platformy Azure powinny być zawsze ograniczone do tego, co jest wymagane przez te role. To uzupełnia podejście dostępu just in time (JIT) usługi Azure AD Privileged Identity Management (PIM) i powinno być okresowo weryfikowane.

Używaj wbudowanych ról do przydzielenia uprawnień i utwórz własną rolę tylko wtedy, gdy jest to wymagane.

Usługa Azure Cost Management oferuje wbudowane role, czytelników i współautorów.

- [Czytelnik usługi Azure Cost Management](../role-based-access-control/built-in-roles.md#cost-management-reader)

- [Współautor usługi Azure Cost Management](../role-based-access-control/built-in-roles.md#cost-management-contributor)

Co to jest kontrola dostępu oparta na rolach (Azure RBAC) /role-based-access-control/overview.md 

- [Jak skonfigurować kontrolę RBAC na platformie Azure](../role-based-access-control/role-assignments-portal.md) 

- [Jak korzystać z przeglądów tożsamości i dostępu w usłudze Azure AD](../active-directory/governance/access-reviews-overview.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

## <a name="data-protection"></a>Ochrona danych

*Aby uzyskać więcej informacji, zobacz [Test porównawczy zabezpieczeń platformy Azure: Ochrona danych](/azure/security/benchmarks/security-controls-v2-data-protection).*

### <a name="dp-1-discovery-classify-and-label-sensitive-data"></a>OD-1: Odnajdywanie, klasyfikowanie i etykietowanie danych poufnych

**Wskazówki**: Zalecane jest odnajdywanie, klasyfikowanie i etykietowanie danych poufnych, dzięki czemu można zaprojektować odpowiednie mechanizmy kontroli, aby zapewnić, że informacje poufne są przechowywane, przetwarzane i przesyłane bezpiecznie przez systemy technologiczne organizacji.

Użyj usługi Azure Information Protection (i skojarzonego z nią narzędzia do skanowania), aby zlokalizować informacje poufne w dokumentach pakietu Office na platformie Azure, lokalnie, w pakiecie Office 365 i w innych lokalizacjach.

Usługa Azure SQL Information Protection może pomóc w klasyfikacji i etykietowaniu informacji przechowywanych w bazach danych Azure SQL Database.

- [Tagowanie informacji poufnych przy użyciu usługi Azure Information Protection](/azure/information-protection/what-is-information-protection) 

- [Jak wdrożyć usługę Azure SQL Data Discovery](/azure/sql-database/sql-database-data-discovery-and-classification)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="dp-2-protect-sensitive-data"></a>OD-2: Ochrona poufnych danych

**Wskazówki**: Zaleca się ochronę danych poufnych przez ograniczenie dostępu przy użyciu kontroli dostępu opartej na rolach platformy Azure (RBAC), kontroli dostępu opartej na sieci i określonych mechanizmów kontroli w usługach platformy Azure (takich jak szyfrowanie w SQL i innych bazach danych).

Aby zapewnić spójną kontrolę dostępu, wszystkie typy kontroli dostępu powinny być dostosowane do strategii segmentacji przedsiębiorstwa. Strategię segmentacji przedsiębiorstwa powinna być również oparta na lokalizacji poufnych lub krytycznych danych i systemów.

W odniesieniu do platformy podstawowej zarządzanej przez firmę Microsoft, firma Microsoft traktuje całą zawartość kliencką jako poufną i zapewnia ochronę przed utratą i narażeniem danych klientów. Aby zapewnić bezpieczeństwo danych klientów na platformie Azure, firma Microsoft zaimplementowała pewne domyślne mechanizmy kontroli i możliwości ochrony danych.

- [Przypisywanie dostępu do danych rozwiązania Cost Management](costs/assign-access-acm-data.md)

- [Kontrola dostępu oparta na rolach na platformie Azure (RBAC)](../role-based-access-control/overview.md) 

- [Informacje na temat ochrony danych klientów na platformie Azure](../security/fundamentals/protection-customer-data.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="dp-3-monitor-for-unauthorized-transfer-of-sensitive-data"></a>OD-3: Monitorowanie nieautoryzowanego transferu danych poufnych

**Wskazówki**: Monitoruj pod kątem nieautoryzowanego transferu danych do lokalizacji znajdujących się poza kontrolą i widocznością przedsiębiorstwa. Zazwyczaj obejmuje to monitorowanie pod kątem nietypowych działań (dużych lub nietypowych transferów), które mogą wskazywać na nieautoryzowaną eksfiltrację danych.

Usługa Azure Storage Advanced Threat Protection (ATP) i usługa Azure SQL ATP mogą generować alerty dotyczące nietypowego transferu informacji, które mogą wskazywać na nieautoryzowane transfery informacji poufnych.

Usługa Azure Information Protection (AIP) oferuje funkcje monitorowania informacji, które zostały sklasyfikowane i oznaczone etykietami.

Jeśli jest to wymagane do zapewnienia zgodności pod kątem ochrony przed utratą danych (DLP), można użyć rozwiązania DLP opartego na hoście, aby wymusić wykrywające i/lub prewencyjne mechanizmy kontrolne w celu zapobiegania eksfiltracji danych.

- [Włączanie usługi Azure SQL ATP](../azure-sql/database/threat-detection-overview.md) 

- [Włączanie usługi Azure Storage ATP](https://docs.microsoft.com/azure/storage/common/storage-advanced-threat-protection?tabs=azure-security-center)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="dp-5-encrypt-sensitive-data-at-rest"></a>OD-5: Szyfrowanie magazynowanych danych poufnych

**Wskazówki**: W celu uzupełnienia kontroli dostępu funkcja eksportu usługi Azure Cost Management obsługuje szyfrowanie w usłudze Azure Storage magazynowanych danych, aby chronić przed atakami „poza pasmem” (takimi jak dostęp do magazynu bazowego) za pomocą szyfrowania z użyciem kluczy zarządzanych przez firmę Microsoft. Te ustawienia domyślne pozwalają zagwarantować, że osoby atakujące nie mogą w łatwy sposób odczytywać ani modyfikować danych.

Aby uzyskać więcej informacji, zobacz:

- [Szyfrowanie w usłudze Azure Storage dla danych magazynowanych](../storage/common/storage-service-encryption.md)

- [Informacje o szyfrowaniu danych magazynowanych na platformie Azure](../security/fundamentals/encryption-atrest.md#encryption-at-rest-in-microsoft-cloud-services)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

## <a name="asset-management"></a>Zarządzanie zasobami

*Aby uzyskać więcej informacji, zobacz [Test porównawczy zabezpieczeń platformy Azure: Zarządzanie zasobami](/azure/security/benchmarks/security-controls-v2-asset-management).*

### <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>ZZ-1: Upewnij się, że zespół ds. zabezpieczeń ma wgląd w ryzyko związane z zasobami

**Wskazówki**: Upewnij się, że zespoły ds. zabezpieczeń mają przyznane uprawnienia czytelnika zabezpieczeń w dzierżawie i subskrypcjach platformy Azure, aby umożliwić im monitorowanie zagrożeń bezpieczeństwa przy użyciu usługi Azure Security Center. 

W zależności od istniejącej struktury obowiązków zespołu ds. zabezpieczeń, monitorowanie zagrożeń bezpieczeństwa może być obowiązkiem centralnego zespołu ds. zabezpieczeń lub zespołu lokalnego. Niemniej jednak informacje na temat zabezpieczeń i ryzyka muszą być zawsze agregowane centralnie w ramach danej organizacji. 

Uprawnienia czytelnika zabezpieczeń mogą być stosowane szeroko do całej dzierżawy (główna grupa zarządzania) lub do zakresu w postaci grup zarządzania lub określonych subskrypcji. 

Uwaga: Do uzyskania wglądu w obciążenia i usługi mogą być wymagane dodatkowe uprawnienia. 

- [Omówienie roli czytelnika zabezpieczeń](../role-based-access-control/built-in-roles.md#security-reader)

- [Omówienie grup zarządzania platformy Azure](../governance/management-groups/overview.md)

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="am-4-ensure-security-of-asset-lifecycle-management"></a>ZZ-4: zapewnienie bezpieczeństwa zarządzania cyklem życia zasobów

**Wskazówki**: Utwórz lub zaktualizuj zasady zabezpieczeń, które dotyczą procesów zarządzania cyklem życia zasobów dla modyfikacji o dużym wpływie. Modyfikacje te obejmują zmiany w zakresie: dostawców tożsamości i dostępu, czułości danych, konfiguracji sieci i przypisywania uprawnień administracyjnych.

Usuń zasoby platformy Azure, gdy nie są już potrzebne.

- [Usuwanie grupy zasobów i zasobów platformy Azure](../azure-resource-manager/management/delete-resource-group.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

## <a name="logging-and-threat-detection"></a>Rejestrowanie i wykrywanie zagrożeń

*Aby uzyskać więcej informacji, zobacz [Test porównawczy zabezpieczeń platformy Azure: Rejestrowanie i wykrywanie zagrożeń](/azure/security/benchmarks/security-controls-v2-logging-threat-protection).*

### <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>RZ-2: włączanie wykrywania zagrożeń na potrzeby zarządzania tożsamościami i dostępem na platformie Azure

**Wskazówki**: Usługa Azure AD udostępnia następujące dzienniki użytkowników, które mogą być wyświetlane w ramach raportowania usługi Azure AD lub zintegrowane z usługą Azure Monitor, Azure Sentinel lub innymi narzędziami SIEM/monitorowania pod kątem bardziej zaawansowanych przypadków użycia związanych z monitorowaniem i analizą:

- Logowania — raporty aktywności logowania zawierają informacje na temat użycia zarządzanych aplikacji i działań użytkownika związane z logowaniem.

- Dzienniki inspekcji — udostępnia możliwość śledzenia wszystkich zmian wprowadzanych przez różne funkcje usługi Azure AD za pomocą dzienników. Działania umieszczane w dziennikach inspekcji to na przykład zmiany wprowadzone w dowolnych zasobach usługi Azure AD, takie jak dodawanie lub usuwanie użytkowników, aplikacji, grup, ról i zasad.

- Ryzykowne logowania — ryzykowne logowanie jest wskaźnikiem próby logowania, które mogło zostać wykonane przez osobę, która nie jest prawowitym właścicielem konta użytkownika.

- Użytkownicy oflagowani w związku z ryzykiem — ryzykowny użytkownik jest wskaźnikiem konta użytkownika, którego bezpieczeństwo mogło zostać naruszone.

Usługa Azure Security Center może również wysyłać alerty dotyczące określonych podejrzanych działań, takich jak nadmierna liczba nieudanych prób uwierzytelnienia, czy przestarzałe konta w subskrypcji. Oprócz podstawowego monitorowania higieny zabezpieczeń moduł ochrony przed zagrożeniami usługi Azure Security Center może również zbierać bardziej szczegółowe alerty zabezpieczeń z poszczególnych zasobów obliczeniowych platformy Azure (maszyn wirtualnych, kontenerów, usługi App Service), zasobów danych (bazy danych SQL i magazynu) oraz warstw usługi platformy Azure. Ta funkcja umożliwia wgląd w anomalie kont wewnątrz poszczególnych zasobów.

Zaleca się również dokonywanie rutynowego przeglądu użytkowników przypisanych do ról w ramach umowy Enterprise Agreement (EA). 

- [Zarządzanie rolami w usłudze Azure Enterprise](manage/understand-ea-roles.md)

- [Raporty dotyczące inspekcji w usłudze Azure Active Directory](../active-directory/reports-monitoring/concept-audit-logs.md) 

- [Włączanie usługi Azure Identity Protection](../active-directory/identity-protection/overview-identity-protection.md)

- [Ochrona przed zagrożeniami w usłudze Azure Security Center](/azure/security-center/threat-protection)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

## <a name="incident-response"></a>Reagowanie na zdarzenia

*Aby uzyskać więcej informacji, zobacz [Test porównawczy zabezpieczeń platformy Azure: Reagowanie na zdarzenia](/azure/security/benchmarks/security-controls-v2-incident-response).*

### <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>RZ-1: przygotowanie — aktualizowanie procesu reagowania na zdarzenia dla platformy Azure

**Wskazówki**: Upewnij się, że organizacja ma procesy reagowania na zdarzenia związane z zabezpieczeniami, zaktualizowała te procesy pod kątem platformy Azure i regularnie sprawdza je w celu zapewnienia gotowości.

- [Implementowanie zabezpieczeń w całym środowisku przedsiębiorstwa](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Przewodnik informacyjny dotyczący reagowania na zdarzenia](/microsoft-365/downloads/IR-Reference-Guide.pdf)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="ir-2-preparation--setup-incident-notification"></a>RZ-2: przygotowanie — konfigurowanie powiadamiania o zdarzeniach

**Wskazówki**: Skonfiguruj informacje kontaktowe dotyczące zdarzeń naruszenia zabezpieczeń w usłudze Azure Security Center. Te informacje kontaktowe są używane przez firmę Microsoft do kontaktowania się z Tobą, gdy centrum Microsoft Security Response Center (MSRC) wykryje, że osoby nieupoważnione lub działające niezgodnie z prawem uzyskały dostęp do Twoich danych. Dostępne są również opcje dostosowywania alertów i powiadomień dotyczących zdarzeń w różnych usługach platformy Azure w zależności od potrzeb związanych z reagowaniem na zdarzenia. 

- [Jak ustawić kontakt ds. zabezpieczeń usługi Azure Security Center](../security-center/security-center-provide-security-contact-details.md)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>RZ-3: wykrywanie i analiza — tworzenie zdarzeń na podstawie alertów o wysokiej jakości

**Wskazówki**: Upewnij się, że istnieje proces tworzenia alertów o wysokiej jakości i pomiaru jakości alertów. Pozwala to na wyciągnięcie wniosków z poprzednich zdarzeń i określić priorytety alertów dla analityków, aby nie tracili czasu na alerty fałszywie dodatnie. 

Alerty o wysokiej jakości mogą być tworzone w oparciu o doświadczenia z poprzednich zdarzeń, sprawdzone źródła społecznościowe i narzędzia przeznaczone do generowania i czyszczenia alertów poprzez łączenie i korelację różnych źródeł sygnałów. 

Usługa Azure Security Center oferuje alerty o wysokiej jakości dla wielu zasobów platformy Azure. Można użyć łącznika danych usługi ASC do przesyłania strumieniowego alertów do usługi Azure Sentinel. Usługa Azure Sentinel umożliwia tworzenie zaawansowanych reguł alertów w celu automatycznego generowania zdarzeń na potrzeby badania. 

Eksportuj alerty i zalecenia usługi Azure Security Center przy użyciu funkcji eksportowania, aby pomóc identyfikować zagrożenia dla zasobów platformy Azure. Eksportuj alerty i zalecenia ręcznie lub w stały, ciągły sposób.

- [Jak skonfigurować eksportowanie](../security-center/continuous-export.md)

- [Jak przesłać strumieniowo alerty do usługi Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="ir-4-detection-and-analysis--investigate-an-incident"></a>RZ-4: wykrywanie i analiza — badanie zdarzenia

**Wskazówki**: Upewnij się, że analitycy mogą wykonywać zapytania i używać różnorodnych źródeł danych podczas badania potencjalnych zdarzeń w celu uzyskania pełnego obrazu tego, co się stało. Należy zbierać różne dzienniki, aby śledzić działania potencjalnej osoby atakującej w ramach całego łańcucha zagrożeń, aby uniknąć efektu martwego pola.  Należy również upewnić się, że szczegółowe informacje i wnioski są rejestrowane dla innych analityków i na potrzeby przyszłych badań.  

Źródła danych do badania obejmują scentralizowane źródła dzienników, które są już zbierane w ramach usług i działających systemów, ale mogą również obejmować:

- Dane sieciowe — użyj dzienników przepływów sieciowych grup zabezpieczeń, usługi Azure Network Watcher i usługi Azure Monitor do przechwytywania dzienników przepływu sieci i innych informacji analitycznych. 

- Migawki uruchomionych systemów: 

    - Użyj funkcji migawek maszyny wirtualnej platformy Azure, aby utworzyć migawkę dysku działającego systemu. 

    - Użyj natywnej możliwości zrzutu pamięci systemu operacyjnego, aby utworzyć migawkę pamięci uruchomionego systemu.

    - Użyj funkcji migawki w ramach usług platformy Azure lub w ramach własnego oprogramowania, aby utworzyć migawki uruchomionych systemów.

Usługa Azure Sentinel zapewnia szeroką analizę danych dla praktycznie każdego źródła dzienników i portalu zarządzania przypadkami do zarządzania pełnym cyklem życia zdarzeń. Informacje analityczne podczas badania mogą być powiązane ze zdarzeniami pod kątem śledzenia i raportowania. 

- [Tworzenie migawek dysku maszyny z systemem Windows](../virtual-machines/windows/snapshot-copy-managed-disk.md)

- [Tworzenie migawek dysku maszyny z systemem Linux](../virtual-machines/linux/snapshot-copy-managed-disk.md)

- [Informacje diagnostyczne pomocy technicznej oraz kolekcja zrzutów pamięci platformy Microsoft Azure](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/) 

- [Badanie zdarzeń za pomocą usługi Azure Sentinel](../sentinel/tutorial-investigate-cases.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>RZ-5: wykrywanie i analiza — określanie priorytetów zdarzeń

**Wskazówki**: Zapewnij kontekst analitykom, na których zdarzeniach należy skupić się w pierwszej kolejności w oparciu o poziom ważności alertów i poufność aktywów. 

Usługa Azure Security Center przypisuje poziom ważności do każdego alertu, aby pomóc w ustaleniu, które alerty powinny być zbadane w pierwszej kolejności. Poziom ważności bazuje na ocenie pewności usługi Security Center względem ustalenia lub danych analitycznych użytych do wygenerowania alertu, a także poziomu pewności, że za działaniem, które doprowadziło do alertu, stał złośliwy zamiar.

Ponadto oznacz zasoby przy użyciu tagów i utwórz system nazewnictwa, aby zidentyfikować i sklasyfikować zasoby platformy Azure, szczególnie te, które przetwarzają dane poufne.  Odpowiedzialność za korygowanie alertów w oparciu o krytyczne znaczenie zasobów platformy Azure i środowisko, w którym wystąpiło zdarzenie, leży po stronie użytkownika.

- [Alerty zabezpieczeń w Centrum zabezpieczeń Azure](../security-center/security-center-alerts-overview.md)

- [Organizowanie zasobów platformy Azure przy użyciu tagów](/azure/azure-resource-manager/resource-group-using-tags)

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="ir-6-containment-eradication-and-recovery--automate-the-incident-handling"></a>RZ-6: zawieranie, eliminowanie i odzyskiwanie — automatyzacja obsługi zdarzeń

**Wskazówki**: Zautomatyzuj ręczne powtarzające się zadania, aby przyspieszyć czas reakcji i zmniejszyć obciążenie analityków. Wykonywanie ręcznych zadań trwa dłużej, spowalniając każde zdarzenie i zmniejszając liczbę zdarzeń, które może obsłużyć analityk. Zadania wykonywane ręcznie zwiększają również zmęczenie analityków, co zwiększa ryzyko wystąpienia błędu ludzkiego, który powoduje opóźnienia, a także pogarsza zdolność analityków do skoncentrowania się na złożonych zadaniach. Korzystając z funkcji automatyzacji przepływów pracy w usłudze Azure Security Center i Azure Sentinel, można automatycznie wyzwalać akcje lub uruchamiać element playbook w odpowiedzi na przychodzące alerty zabezpieczeń. Element playbook wykonuje akcje, takie jak wysyłanie powiadomień, wyłączanie kont i izolowanie problematycznych sieci. 

- [Konfigurowanie automatyzacji przepływu pracy w usłudze Security Center](../security-center/workflow-automation.md)

- [Konfigurowanie automatycznych reakcji na zagrożenia w usłudze Azure Security Center](../security-center/tutorial-security-incident.md#triage-security-alerts)

- [Konfigurowanie automatycznych reakcji na zagrożenia w usłudze Azure Sentinel](../sentinel/tutorial-respond-threats-playbook.md)

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

## <a name="posture-and-vulnerability-management"></a>Zarządzanie lukami w zabezpieczeniach i stanem

*Aby uzyskać więcej informacji, zobacz [Test porównawczy zabezpieczeń platformy Azure: Zarządzanie lukami w zabezpieczeniach i stanem](/azure/security/benchmarks/security-controls-v2-vulnerability-management).*

### <a name="pv-7-rapidly-and-automatically-remediate-software-vulnerabilities"></a>SL-7: szybkie i automatyczne korygowanie luk w zabezpieczeniach oprogramowania

**Wskazówki**: Szybko wdrażaj aktualizacje oprogramowania w celu korygowania luk w zabezpieczeniach systemów operacyjnych i aplikacji.

Potraktuj priorytetowo korzystanie ze standardowego programu oceny ryzyka (np. Common Vulnerability Scoring System) lub domyślnych ocen ryzyka udostępnianych przez narzędzia skanujące innych firm i dostosuj je do środowiska z użyciem kontekstu odnośnie tego, które aplikacje stwarzają duże ryzyko dla bezpieczeństwa, a które wymagają wysokiego czasu sprawności.

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="pv-8-conduct-regular-attack-simulation"></a>SL-8: przeprowadzanie regularnej symulacji ataków

**Wskazówki**: W razie potrzeby przeprowadź testy penetracyjne lub działania typu „red team” na zasobach platformy Azure i zapewnij skorygowanie wszystkich krytycznych ustaleń dotyczących zabezpieczeń.
Postępuj zgodnie z regułami dotyczącymi testów penetracyjnych w chmurze firmy Microsoft, aby upewnić się, że testy penetracyjne nie naruszają zasad firmy Microsoft. Wykorzystaj strategię i podejście do działań typu „red team” firmy Microsoft oraz przeprowadzaj testy penetracyjne aktywnej witryny w odniesieniu do zarządzanej przez firmę Microsoft infrastruktury w chmurze, usług i aplikacji.

- [Testy penetracyjne na platformie Azure](../security/fundamentals/pen-testing.md)

- [Reguły testów penetracyjnych zaangażowania](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Działania typu „red team” w chmurze firmy Microsoft](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Udostępnione

## <a name="governance-and-strategy"></a>Ład i strategia

*Aby uzyskać więcej informacji, zobacz [Test porównawczy zabezpieczeń platformy Azure: Ład i strategia](/azure/security/benchmarks/security-controls-v2-governance-strategy).*

### <a name="gs-1-define-asset-management-and-data-protection-strategy"></a>ŁS-1: definiowanie strategii zarządzania zasobami i ochrony danych 

**Wskazówki**: Zadbaj o udokumentowanie i komunikowanie jasnej strategii ciągłego monitorowania i ochrony systemów i danych. Określ priorytety w odniesieniu do odnajdywania, oceny, ochrony i monitorowania danych i systemów o krytycznym znaczeniu dla firmy. 

Ta strategia powinna obejmować udokumentowane wskazówki, zasady i standardy dla następujących elementów: 

-   Standard klasyfikacji danych w zależności od ryzyka biznesowego

-   Wgląd organizacji zabezpieczeń w czynniki ryzyka i spis zasobów 

-   Zatwierdzenie przez organizację zabezpieczeń usług platformy Azure do użycia 

-   Bezpieczeństwo zasobów w ramach ich cyklu życia

-   Wymagana strategia kontroli dostępu zgodnie z klasyfikacją danych organizacji

-   Korzystanie z natywnych dla platformy Azure oraz oferowanych przez inne firmy funkcji ochrony danych

-   Wymagania w zakresie szyfrowania danych dla przypadków użycia dotyczących danych przesyłanych i danych magazynowanych

-   Odpowiednie standardy kryptograficzne

Więcej informacji można znaleźć w następującej dokumentacji:
- [Zalecenia dotyczące architektury zabezpieczeń platformy Azure — magazyn, dane i szyfrowanie](https://docs.microsoft.com/azure/architecture/framework/security/storage-data-encryption?toc=/security/compass/toc.json&amp;bc=/security/compass/breadcrumb/toc.json)

- [Podstawy zabezpieczeń platformy Azure — zabezpieczenia, szyfrowanie i magazyn danych platformy Azure](../security/fundamentals/encryption-overview.md)

- [Cloud Adoption Framework — najlepsze rozwiązania z zakresu zabezpieczeń i szyfrowania danych platformy Azure](https://docs.microsoft.com/azure/security/fundamentals/data-encryption-best-practices?toc=/azure/cloud-adoption-framework/toc.json&amp;bc=/azure/cloud-adoption-framework/_bread/toc.json)

- [Test porównawczy zabezpieczeń platformy Azure — zarządzanie zasobami](/azure/security/benchmarks/security-benchmark-v2-asset-management)

- [Test porównawczy zabezpieczeń platformy Azure — ochrona danych](/azure/security/benchmarks/security-benchmark-v2-data-protection)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="gs-2-define-enterprise-segmentation-strategy"></a>ŁS-2: definiowanie strategii segmentacji przedsiębiorstwa 

**Wskazówki**: Należy stworzyć ogólnofirmową strategię segmentacji dostępu do zasobów przy użyciu kombinacji tożsamości, sieci, aplikacji, subskrypcji, grupy zarządzania i innych mechanizmów kontroli.

Należy starannie wyważyć potrzebę rozdzielenia zabezpieczeń z koniecznością umożliwienia codziennej pracy systemów, które muszą komunikować się ze sobą i uzyskiwać dostęp do danych.

Upewnij się, że strategia segmentacji jest wdrażana spójnie w ramach wszystkich rodzajów mechanizmów kontroli, w tym zabezpieczeń sieci, modeli tożsamości i dostępu, modeli uprawnień/dostępu do aplikacji, a także mechanizmów kontroli procesów ludzkich.

- [Wskazówki dotyczące strategii segmentacji na platformie Azure (wideo)](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Wskazówki dotyczące strategii segmentacji na platformie Azure (dokument)](/security/compass/governance#enterprise-segmentation-strategy)

- [Dostosowanie segmentacji sieci do strategii segmentacji przedsiębiorstwa](/security/compass/network-security-containment#align-network-segmentation-with-enterprise-segmentation-strategy)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="gs-3-define-security-posture-management-strategy"></a>ŁS-3: definiowanie strategii zarządzania stanem zabezpieczeń

**Wskazówki**: Należy nieustannie mierzyć i ograniczać ryzyko dla poszczególnych zasobów i środowiska, w którym są hostowane. Ustal priorytety zasobów o wysokiej wartości i obszarów wysoce narażonych na ataki, takich jak opublikowane aplikacje, sieciowe punkty danych przychodzących i wychodzących, punkty końcowe użytkowników i administratorów itp.

- [Test porównawczy zabezpieczeń platformy Azure — zarządzanie lukami w zabezpieczeniach i stanem](/azure/security/benchmarks/security-benchmark-v2-posture-vulnerability-management)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="gs-4-align-organization-roles-responsibilities-and-accountabilities"></a>ŁS-4: dostosowanie ról, obowiązków i odpowiedzialności organizacji

**Wskazówki**: Zadbaj o udokumentowanie i komunikowanie przejrzystej strategii dotyczącej ról i obowiązków w ramach organizacji zabezpieczeń. Potraktuj priorytetowo udostępnienie jasnej odpowiedzialności za decyzje dotyczące zabezpieczeń, przeszkolenie wszystkich na temat modelu współodpowiedzialności oraz przeszkolenie zespołów technicznych odnośnie technologii zabezpieczeń w chmurze.

- [Najlepsze rozwiązania w zakresie zabezpieczeń platformy Azure 1 — ludzie: szkolenie zespołów z zakresu przejścia na zabezpieczenia w chmurze](/azure/cloud-adoption-framework/security/security-top-10#1-people-educate-teams-about-the-cloud-security-journey)

- [Najlepsze rozwiązania w zakresie zabezpieczeń platformy Azure 2 — ludzie: szkolenie zespołów z zakresu technologii zabezpieczeń chmury](/azure/cloud-adoption-framework/security/security-top-10#2-people-educate-teams-on-cloud-security-technology)

- [Najlepsze rozwiązania w zakresie zabezpieczeń platformy Azure 3 — procesy: przypisywanie odpowiedzialności za decyzje dotyczące zabezpieczeń chmury](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="gs-5-define-network-security-strategy"></a>ŁS-5: definiowanie strategii zabezpieczeń sieci

**Wskazówki**: Należy stworzyć podejście do zabezpieczeń sieci platformy Azure jako część ogólnej strategii zabezpieczeń dotyczącej kontroli dostępu w organizacji.  

Ta strategia powinna obejmować udokumentowane wskazówki, zasady i standardy dla następujących elementów: 

-   Scentralizowane zarządzanie siecią i odpowiedzialność w zakresie zabezpieczeń

-   Model segmentacji sieci wirtualnej dostosowany do strategii segmentacji przedsiębiorstwa

-   Strategia korygowania w ramach różnych scenariuszy zagrożeń i ataków

-   Strategia dotycząca internetowych urządzeń brzegowych, ruchu przychodzącego i wychodzącego

-   Strategia dotycząca łączności w ramach chmury hybrydowej i sieci lokalnej

-   Aktualne artefakty zabezpieczeń sieci (np. diagramy sieciowe, dokumentacja architektury sieci)

Więcej informacji można znaleźć w następującej dokumentacji:
- [Najlepsze rozwiązania w zakresie zabezpieczeń platformy Azure 11 — architektura. Pojedyncza ujednolicona strategia zabezpieczeń](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Test porównawczy zabezpieczeń platformy Azure — zabezpieczenia sieci](/azure/security/benchmarks/security-benchmark-v2-network-security)

- [Omówienie zabezpieczeń sieci na platformie Azure](../security/fundamentals/network-overview.md)

- [Strategia dotycząca architektury sieci przedsiębiorstwa](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="gs-6-define-identity-and-privileged-access-strategy"></a>ŁS-6: definiowanie strategii dotyczącej dostępu uprzywilejowanego i tożsamości

**Wskazówki**: Należy stworzyć podejścia dotyczące uprzywilejowanego dostępu oraz tożsamości platformy Azure jako część ogólnej strategii zabezpieczeń dotyczącej kontroli dostępu w organizacji.  

Ta strategia powinna obejmować udokumentowane wskazówki, zasady i standardy dla następujących elementów: 

-   Scentralizowany system obsługi tożsamości i uwierzytelniania oraz połączenie z innymi wewnętrznymi i zewnętrznymi systemami obsługi tożsamości

-   Metody silnego uwierzytelniania w ramach różnych przypadków użycia i warunków

-   Ochrona użytkowników o wysokim poziomie uprawnień

-   Monitorowanie i obsługa nietypowych działań użytkowników  

-   Przegląd tożsamości i dostępu użytkowników oraz proces uzgadniania

Więcej informacji można znaleźć w następującej dokumentacji:

- [Test porównawczy zabezpieczeń platformy Azure — zarządzanie tożsamościami](/azure/security/benchmarks/security-benchmark-v2-identity-management)

- [Test porównawczy zabezpieczeń platformy Azure — dostęp uprzywilejowany](/azure/security/benchmarks/security-benchmark-v2-privileged-access)

- [Najlepsze rozwiązania w zakresie zabezpieczeń platformy Azure 11 — architektura. Pojedyncza ujednolicona strategia zabezpieczeń](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Omówienie zabezpieczeń zarządzania tożsamościami na platformie Azure](../security/fundamentals/identity-management-overview.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="gs-7-define-logging-and-threat-response-strategy"></a>ŁS-7: definiowanie strategii rejestrowania i reagowania na zagrożenia

**Wskazówki**: Należy stworzyć strategię rejestrowania i reagowania na zagrożenia w celu szybkiego wykrywania i usuwania zagrożeń przy jednoczesnym spełnieniu wymogów zgodności. Potraktuj priorytetowo kwestię udostępnienia analitykom alertów o wysokiej jakości i bezproblemowego środowiska, tak aby mogli skupić się na zagrożeniach, a nie na wdrażaniu i ręcznych krokach. 

Ta strategia powinna obejmować udokumentowane wskazówki, zasady i standardy dla następujących elementów: 

-   Rola i obowiązki zespołu operacyjnego ds. zabezpieczeń (SecOP) w organizacji 

-   Dobrze zdefiniowany proces reagowania na zdarzenia zgodny ze strategią NIST lub inną platformą branżową 

-   Przechwytywanie i przechowywanie dzienników w celu zapewnienia obsługi wykrywania zagrożeń, reagowania na zdarzenia i zgodności

-   Scentralizowana widoczność oraz korelacja informacji dotyczących zagrożeń przy użyciu rozwiązań SIEM, natywnych możliwości platformy Azure i innych źródeł 

-   Plan komunikacji i powiadomień obejmujący klientów, dostawców i zainteresowane strony

-   Używanie natywnych platform Azure i platform innych firm do obsługi zdarzeń, takich jak rejestrowanie i wykrywanie zagrożeń, operacje dowodowe oraz korygowanie i eliminowanie ataków

-   Procesy obsługi zdarzeń i działań po zdarzeniu, takie jak wyciąganie wniosków i przechowywanie dowodów

Więcej informacji można znaleźć w następującej dokumentacji:

- [Test porównawczy zabezpieczeń platformy Azure — rejestrowanie i wykrywanie zagrożeń](/azure/security/benchmarks/security-benchmark-v2-logging-threat-detection)

- [Test porównawczy zabezpieczeń platformy Azure — reagowanie na zdarzenia](/azure/security/benchmarks/security-benchmark-v2-incident-response)

- [Najlepsze rozwiązania w zakresie zabezpieczeń platformy Azure 4 — procesy. Aktualizowanie procesów reagowania na zdarzenia dla chmury](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Przewodnik po decyzjach związanych z wdrażaniem platformy Azure, rejestrowaniem i raportowaniem](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

- [Platforma Azure w skali przedsiębiorstwa, zarządzanie i monitorowanie](/azure/cloud-adoption-framework/ready/enterprise-scale/management-and-monitoring)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

## <a name="next-steps"></a>Następne kroki

- Zobacz [Omówienie testu porównawczego zabezpieczeń platformy Azure w wersji 2](/azure/security/benchmarks/overview)
- Dowiedz się więcej o [punktach odniesienia zabezpieczeń platformy Azure](/azure/security/benchmarks/security-baselines-overview)
