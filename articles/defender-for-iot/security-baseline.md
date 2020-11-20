---
title: Podstawa zabezpieczeń platformy Azure dla usługi Azure Defender dla IoT
description: Linia bazowa zabezpieczeń usługi Azure Defender for IoT zawiera wskazówki i zasoby dotyczące procedur związanych z wdrażaniem zaleceń dotyczących zabezpieczeń określonych w teście zabezpieczeń platformy Azure.
author: msmbaldwin
ms.service: defender-for-iot
ms.topic: conceptual
ms.date: 11/19/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: ad10195c50d3de49ce89c3917ebac5ba76ca4194
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94974971"
---
# <a name="azure-security-baseline-for-azure-defender-for-iot"></a>Podstawa zabezpieczeń platformy Azure dla usługi Azure Defender dla IoT

Ta linia bazowa zabezpieczeń stosuje wskazówki z [wersji 2,0 usługi Azure Security test](../security/benchmarks/overview.md) do Microsoft Azure Defender for IoT. Test porównawczy zabezpieczeń platformy Azure zawiera zalecenia dotyczące sposobu zabezpieczania rozwiązań w chmurze na platformie Azure. Zawartość jest pogrupowana według **kontroli zabezpieczeń** zdefiniowanych przez usługę Azure Security test i powiązane wskazówki dotyczące usługi Azure Defender dla IoT. **Formanty** nie mają zastosowania do usługi Azure Defender dla IoT zostały wykluczone.

Aby dowiedzieć się, jak usługa Azure Defender for IoT jest całkowicie mapowana na test porównawczy zabezpieczeń platformy Azure, zobacz [pełny plik mapowania zabezpieczeń usługi Azure Defender for IoT](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="identity-management"></a>Zarządzanie tożsamością

*Aby uzyskać więcej informacji, zobacz temat [Azure Security test: Zarządzanie tożsamościami](/azure/security/benchmarks/security-controls-v2-identity-management).*

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>Wiadomość błyskawiczna-1: standaryzacja Azure Active Directory jako centralny system tożsamości i uwierzytelniania

**Wskazówki**: usługa Azure Defender for IoT jest zintegrowana z usługami Azure Active Directory (Azure AD), domyślną usługą zarządzania tożsamościami i dostępem platformy Azure. Należy przeprowadzić standaryzację usługi Azure AD, aby zarządzać zarządzaniem tożsamościami i dostępem w organizacji w programie:

- Microsoft Cloud zasoby, takie jak Azure Portal, Azure Storage, Azure Virtual Machine (Linux i Windows), Azure Key Vault, PaaS i SaaS.

- Zasoby organizacji, takie jak aplikacje na platformie Azure lub zasoby sieci firmowej.

Zabezpieczanie usługi Azure AD powinno mieć wysoki priorytet w zakresie zabezpieczeń w chmurze w organizacji. Usługa Azure AD zapewnia bezpieczną ocenę tożsamości, która pomaga ocenić stan zabezpieczeń tożsamości w porównaniu z zaleceniami dotyczącymi najlepszych rozwiązań firmy Microsoft. Skorzystaj z oceny, aby ocenić, jak ściśle Twoja konfiguracja spełnia zalecenia dotyczące najlepszych rozwiązań, i ulepszać stan zabezpieczeń.

Usługa Azure AD obsługuje tożsamość zewnętrzną, która umożliwia użytkownikom bez konto Microsoft logowania się do aplikacji i zasobów przy użyciu tożsamości zewnętrznej.

- [Dzierżawa w Azure Active Directory](../active-directory/develop/single-and-multi-tenant-apps.md) 

- [Jak utworzyć i skonfigurować wystąpienie usługi Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md) 

- [Użyj zewnętrznych dostawców tożsamości dla aplikacji](/azure/active-directory/b2b/identity-providers) 

- [Co to jest bezpieczna ocena tożsamości w Azure Active Directory](../active-directory/fundamentals/identity-secure-score.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="im-3-use-azure-ad-single-sign-on-sso-for-application-access"></a>KOMUNIKATOR-3: używanie logowania jednokrotnego usługi Azure AD na potrzeby dostępu do aplikacji

**Wskazówki**: usługa Azure Defender for IoT używa Azure Active Directory, aby zapewnić zarządzanie tożsamościami i dostępem do zasobów platformy Azure, aplikacji w chmurze i aplikacji lokalnych. Obejmuje to tożsamości przedsiębiorstwa, takie jak pracownicy, a także tożsamości zewnętrzne, takie jak partnerzy, dostawcy i dostawcy. Umożliwia to Logowanie jednokrotne (SSO) w celu zarządzania i bezpiecznego dostępu do danych i zasobów organizacji w środowisku lokalnym i w chmurze. Połącz wszystkich użytkowników, aplikacje i urządzenia z usługą Azure AD w celu zapewnienia bezproblemowego, bezpiecznego dostępu i lepszego wglądu i kontroli.

- [Informacje na temat logowania jednokrotnego aplikacji za pomocą usługi Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>KOMUNIKATOR-4: Użyj kontrolek silnego uwierzytelniania dla wszystkich Azure Active Directory na podstawie dostępu

**Wskazówki**: usługa Azure Defender for IoT używa Azure Active Directory, które obsługują kontrolę silnego uwierzytelniania za pośrednictwem uwierzytelniania wieloskładnikowego (MFA) i silnych metod bezhaseł.

- Uwierzytelnianie wieloskładnikowe — Włącz usługę Azure AD MFA i postępuj zgodnie z zaleceniami dotyczącymi zarządzania tożsamościami i dostępem Azure Security Center, aby zapoznać się z najlepszymi rozwiązaniami w konfiguracji usługi MFA. Usługę MFA można wymusić na wszystkich, wybierać użytkowników lub na poziomie poszczególnych użytkowników na podstawie warunków logowania i czynników ryzyka.
- Uwierzytelnianie bezhasło — dostępne są trzy opcje uwierzytelniania bezhasła: funkcja Windows Hello dla firm, aplikacja Microsoft Authenticator i lokalne metody uwierzytelniania, takie jak karty inteligentne.

W przypadku użytkowników z uprawnieniami administratora i uprzywilejowanego upewnij się, że jest używana najwyższa Metoda silnego uwierzytelniania, a następnie zastąp odpowiednie zasady silnego uwierzytelniania innym użytkownikom.

- [Jak włączyć usługę MFA na platformie Azure](../active-directory/authentication/howto-mfa-getstarted.md) 

- [Wprowadzenie do opcji uwierzytelniania bezhasła dla Azure Active Directory](../active-directory/authentication/concept-authentication-passwordless.md) 

- [Domyślne zasady haseł usługi Azure AD](../active-directory/authentication/concept-sspr-policy.md#password-policies-that-only-apply-to-cloud-user-accounts)

- [Eliminowanie nieprawidłowych haseł przy użyciu ochrony hasłem usługi Azure AD](../active-directory/authentication/concept-password-ban-bad.md)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="im-5-monitor-and-alert-on-account-anomalies"></a>KOMUNIKATOR-5: Monitor i alert dotyczący anomalii kont

**Wskazówki**: usługa Azure Defender for IoT jest zintegrowana z Azure Active Directory, w którym znajdują się następujące źródła danych:

Logowania — raport logowania zawiera informacje na temat użycia zarządzanych aplikacji i działań związanych z logowaniem użytkowników.

Dzienniki inspekcji — udostępnia możliwość śledzenia wszystkich zmian wprowadzanych przez różne funkcje usługi Azure AD za pomocą dzienników. Przykłady dzienników inspekcji obejmują zmiany wprowadzone w zasobach w usłudze Azure AD, takie jak dodawanie lub usuwanie użytkowników, aplikacji, grup, ról i zasad.

Ryzykowne logowania — ryzykowne logowanie jest wskaźnikiem próby logowania, które mogło zostać wykonane przez osobę, która nie jest prawowitym właścicielem konta użytkownika.

Użytkownicy oflagowani w związku z ryzykiem — ryzykowny użytkownik jest wskaźnikiem konta użytkownika, którego bezpieczeństwo mogło zostać naruszone.

Te źródła danych można zintegrować z Azure Monitor, wskaźnikiem produktów platformy Azure lub systemami SIEM innych firm.

Azure Security Center może również otrzymywać alerty dotyczące niektórych podejrzanych działań, takich jak nadmierna liczba nieudanych prób uwierzytelnienia, czyli przestarzałe konta w subskrypcji.

Usługa Azure Advanced Threat Protection (ATP) to rozwiązanie zabezpieczeń, które może używać sygnałów Active Directory do identyfikowania, wykrywania i badania zaawansowanych zagrożeń, złamanych tożsamości i złośliwych działań niejawnych.

- [Raporty dotyczące inspekcji w usłudze Azure AD](../active-directory/reports-monitoring/concept-audit-logs.md) 

- [Jak wyświetlić ryzykowne logowania usługi Azure AD](/azure/active-directory/reports-monitoring/concept-risky-sign-ins) 

- [Identyfikowanie użytkowników usługi Azure AD oflagowanych w celu działania ryzykownego](/azure/active-directory/reports-monitoring/concept-user-at-risk) 

- [Jak monitorować działania związane z tożsamościami i dostępem użytkowników w Azure Security Center](../security-center/security-center-identity-access.md) 

- [Alerty w Azure Security Center module ochrony przed zagrożeniami](../security-center/alerts-reference.md) 

- [Jak zintegrować dzienniki aktywności platformy Azure z usługą Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

## <a name="privileged-access"></a>Uprzywilejowany dostęp

*Aby uzyskać więcej informacji, zobacz [Azure Security test: Privileged Access](/azure/security/benchmarks/security-controls-v2-privileged-access).*

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2: ograniczanie dostępu administracyjnego do systemów o krytycznym znaczeniu dla firmy

**Wskazówki**: usługa Azure Defender for IoT korzysta z usługi Azure RBAC, aby odizolować dostęp do systemów o krytycznym znaczeniu dla firmy, ograniczając konta, które mają przyznane uprzywilejowany dostęp do subskrypcji i grup zarządzania, w których się znajdują.

Upewnij się, że ograniczono również dostęp do systemów zarządzania, tożsamości i zabezpieczeń, które mają dostęp administracyjny do krytycznych dostępów firmy, takich jak kontrolery domena usługi Active Directory (DC), narzędzia zabezpieczeń i narzędzia do zarządzania systemem, z agentami zainstalowanymi w systemach krytycznych dla działalności firmy. Osoby atakujące, którzy naruszają te systemy zarządzania i zabezpieczeń, mogą natychmiast weaponize je w celu naruszenia krytycznych zasobów firmy.

Wszystkie typy kontroli dostępu powinny być wyrównane do strategii segmentacji przedsiębiorstwa w celu zapewnienia spójnej kontroli dostępu.

- [Składniki platformy Azure i Model referencyjny](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151) 

- [Dostęp do grupy zarządzania](../governance/management-groups/overview.md#management-group-access) 

- [Administratorzy subskrypcji platformy Azure](../cost-management-billing/manage/add-change-subscription-administrator.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="pa-4-set-up-emergency-access-in-azure-ad"></a>PA-4: Konfigurowanie dostępu awaryjnego w usłudze Azure AD

**Wskazówki**: usługa Azure Defender for IoT jest zintegrowana z usługą Azure Active Directory w celu zarządzania swoimi zasobami. Aby zapobiec przypadkowemu zablokowaniu organizacji usługi Azure AD, skonfiguruj konto dostępu awaryjnego w celu uzyskania dostępu, gdy nie można używać normalnych kont administracyjnych. Konta dostępu awaryjnego są zwykle wysoce uprzywilejowane i nie powinny być przypisane do konkretnych osób. Konta dostępu awaryjnego są ograniczone do sytuacji awaryjnych lub "szkła Break", w których normalne konta administracyjne nie mogą być używane.

Należy upewnić się, że poświadczenia (takie jak hasło, certyfikat lub karta inteligentna) dla kont dostępu awaryjnego są bezpieczne i znane tylko osobom, które są upoważnione do ich używania tylko w sytuacji awaryjnej.

- [Zarządzanie kontami dostępu awaryjnego w usłudze Azure AD](/azure/active-directory/users-groups-roles/directory-emergency-access)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="pa-5-automate-entitlement-management"></a>PA-5: Automatyzowanie zarządzania uprawnieniami 

**Wskazówki**: usługa Azure Defender for IoT jest zintegrowana z usługą Azure Active Directory w celu zarządzania swoimi zasobami. Użyj funkcji zarządzania prawami usługi Azure AD, aby zautomatyzować przepływy pracy żądania dostępu, w tym przydziały dostępu, przeglądy i wygaśnięcie. Obsługiwane jest również podwójne lub wieloetapowe zatwierdzenie.

- [Co to są przeglądy dostępu do usługi Azure AD](../active-directory/governance/access-reviews-overview.md) 

- [Co to jest zarządzanie prawami w usłudze Azure AD](../active-directory/governance/entitlement-management-overview.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="pa-7-follow-just-enough-administration-least-privilege-principle"></a>PA-7: przestrzeganie wystarczającej liczby czynności administracyjnych (najmniejsza zasada uprawnień) 

**Wskazówki**: usługa Azure Defender for IoT jest zintegrowana z kontrolą dostępu opartą na ROLACH (RBAC) na platformie Azure w celu zarządzania swoimi zasobami. Usługa Azure RBAC umożliwia zarządzanie dostępem do zasobów platformy Azure za pomocą przypisań ról. Te role można przypisać do użytkowników, grup jednostek usługi i zarządzanych tożsamości. Istnieją wstępnie zdefiniowane wbudowane role dla niektórych zasobów i te role można spisować lub odpytać za pomocą narzędzi takich jak interfejs wiersza polecenia platformy Azure, Azure PowerShell lub Azure Portal. Uprawnienia przypisane do zasobów za pomocą funkcji RBAC platformy Azure powinny być zawsze ograniczone do tego, co jest wymagane przez role. To uzupełnia podejście just in Time (JIT) Azure AD Privileged Identity Management (PIM) i powinno być okresowo weryfikowane.

Przy użyciu wbudowanych ról można przydzielić uprawnienia i w razie potrzeby utworzyć tylko rolę niestandardową.

- [Co to jest kontrola dostępu oparta na rolach (Azure RBAC)](../role-based-access-control/overview.md) 

- [Jak skonfigurować RBAC na platformie Azure](../role-based-access-control/role-assignments-portal.md) 

- [Jak korzystać z przeglądów tożsamości i dostępu w usłudze Azure AD](../active-directory/governance/access-reviews-overview.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

## <a name="data-protection"></a>Ochrona danych

*Aby uzyskać więcej informacji, zobacz [Azure Security test: Data Protection](/azure/security/benchmarks/security-controls-v2-data-protection).*

### <a name="dp-1-discovery-classify-and-label-sensitive-data"></a>DP-1: odnajdywanie, klasyfikowanie i etykietowanie danych poufnych

**Wskazówki**: odkrywanie, klasyfikowanie i etykietowanie poufnych danych, dzięki czemu można zaprojektować odpowiednie kontrolki, aby zapewnić, że poufne informacje są przechowywane, przetwarzane i przesyłane bezpiecznie przez systemy technologiczne organizacji. 

Użyj Azure Information Protection (i skojarzonego z nim narzędzia do skanowania), aby uzyskać informacje poufne w dokumentach pakietu Office na platformie Azure, lokalnie, w pakiecie Office 365 i w innych lokalizacjach. 

Możesz użyć usługi Azure SQL Information Protection, aby pomóc w klasyfikacji i etykietowania informacji przechowywanych w bazach danych Azure SQL.

- [Otaguj informacje poufne przy użyciu Azure Information Protection](/azure/information-protection/what-is-information-protection) 

- [Jak wdrożyć usługę Azure SQL Data Discovery](/azure/sql-database/sql-database-data-discovery-and-classification)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="dp-2-protect-sensitive-data"></a>DP-2: Ochrona poufnych danych

**Wskazówki**: Ochrona poufnych danych przez ograniczenie dostępu przy użyciu Access Control opartej na rolach (Azure RBAC), kontroli dostępu opartej na sieci i określonych kontrolek w usługach platformy Azure (takich jak szyfrowanie w SQL i innych bazach danych). 

Aby zapewnić spójną kontrolę dostępu, wszystkie typy kontroli dostępu powinny być wyrównane do strategii segmentacji przedsiębiorstwa. Strategię segmentacji przedsiębiorstwa należy również uzyskać informacje o lokalizacji poufnych lub krytycznych danych i systemów.

W przypadku podstawowej platformy zarządzanej przez firmę Microsoft Firma Microsoft traktuje całą zawartość klienta jako poufną i chroniącą przed utratą i narażeniem danych przez klienta. Aby zapewnić bezpieczeństwo danych klienta na platformie Azure, firma Microsoft zaimplementował pewne domyślne funkcje i możliwości ochrony danych.

- [Access Control oparte na rolach (RBAC) na platformie Azure](../role-based-access-control/overview.md)

- [Zrozumienie ochrony danych klientów na platformie Azure](../security/fundamentals/protection-customer-data.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="dp-4-encrypt-sensitive-information-in-transit"></a>DP-4: Szyfruj poufne informacje podczas przesyłania

**Wskazówki**: aby uzupełnić kontrolę dostępu, przesyłane dane powinny być chronione przed atakami "poza pasmem" (np. przechwytywaniem ruchu) przy użyciu szyfrowania, aby zapewnić, że osoby atakujące nie mogą łatwo odczytać ani zmodyfikować danych. 

Chociaż jest to opcjonalne dla ruchu w sieciach prywatnych, ma to kluczowe znaczenie dla ruchu w sieciach zewnętrznych i publicznych. W przypadku ruchu HTTP upewnij się, że wszyscy klienci łączący się z zasobami platformy Azure mogą negocjować protokół TLS w wersji 1.2 lub nowszej. W celu zarządzania zdalnego należy użyć protokołu SSH (dla systemu Linux) lub protokołu RDP/TLS (w systemie Windows), a nie z nieszyfrowanym protokołem. Przestarzałe protokoły SSL, TLS i SSH oraz słabe szyfry powinny być wyłączone.  

Domyślnie platforma Azure zapewnia szyfrowanie danych przesyłanych między centrami danych platformy Azure. 

- [Informacje o szyfrowaniu podczas przesyłania na platformę Azure](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit)

- [Informacje o zabezpieczeniach protokołu TLS](/security/engineering/solving-tls1-problem)

- [Podwójne szyfrowanie danych na platformie Azure podczas przesyłania](../security/fundamentals/double-encryption.md#data-in-transit)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

## <a name="asset-management"></a>Zarządzanie zasobami

*Aby uzyskać więcej informacji, zobacz temat [Azure Security test: zarządzanie zasobami](/azure/security/benchmarks/security-controls-v2-asset-management).*

### <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>AM-1: Upewnij się, że zespół ds. zabezpieczeń ma wgląd w ryzyko związane z zasobami

**Wskazówki**: Upewnij się, że zespoły zabezpieczeń mają przyznane uprawnienia czytelnika zabezpieczeń w dzierżawie i subskrypcjach platformy Azure, aby umożliwić im monitorowanie zagrożeń bezpieczeństwa przy użyciu Azure Security Center. 

W zależności od tego, jak są strukturalne obowiązki zespołu zabezpieczeń, monitorowanie zagrożeń bezpieczeństwa może być obowiązkiem centralnego zespołu zabezpieczeń lub zespołu lokalnego. Wspomniane powyżej informacje o zabezpieczeniach i zagrożeniach muszą zawsze zostać zagregowane w organizacji. 

Uprawnienia czytelnika zabezpieczeń mogą być stosowane szeroko do całej dzierżawy (głównej grupy zarządzania) lub zakresu do grup zarządzania lub określonych subskrypcji. 

Do uzyskania wglądu w obciążenia i usługi mogą być wymagane dodatkowe uprawnienia. 

- [Omówienie roli czytelnik zabezpieczeń](../role-based-access-control/built-in-roles.md#security-reader)

- [Omówienie Grupy zarządzania platformy Azure](../governance/management-groups/overview.md)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="am-3-use-only-approved-azure-services"></a>AM-3: Używaj tylko zatwierdzonych usług platformy Azure

**Wskazówki**: Użyj Azure Policy do inspekcji i ograniczania usług, które użytkownicy mogą inicjować w Twoim środowisku. Użyj grafu zasobów platformy Azure do wykonywania zapytań dotyczących zasobów i odnajdywania ich w ramach subskrypcji. Za pomocą Azure Monitor można tworzyć reguły wyzwalające alerty w przypadku wykrycia niezatwierdzonej usługi.

- [Konfigurowanie Azure Policy i zarządzanie nimi](../governance/policy/tutorials/create-and-manage.md)

- [Jak odmówić określonego typu zasobu za pomocą Azure Policy](../governance/policy/samples/built-in-policies.md#general)

- [Jak tworzyć zapytania za pomocą Eksploratora Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="am-4-ensure-security-of-asset-lifecycle-management"></a>AM-4: Zapewnij bezpieczeństwo zarządzania cyklem życia zasobów

**Wskazówki**: ustalanie lub aktualizowanie zasad zabezpieczeń, które dotyczą procesów zarządzania cyklem życia zasobów dla modyfikacji o dużym wpływie. Modyfikacje obejmują następujące zmiany: dostawcy tożsamości i dostępu, czułość danych, konfiguracja sieci i przypisanie uprawnień administracyjnych.

Usuń zasoby platformy Azure, gdy nie są już potrzebne.

- [Usuń grupę zasobów i zasób platformy Azure](../azure-resource-manager/management/delete-resource-group.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>AM-5: ograniczanie możliwości korzystania przez użytkowników z Azure Resource Manager

**Wskazówki**: korzystanie z dostępu warunkowego usługi Azure AD w celu ograniczenia możliwości współpracy użytkowników z Azure Resource Manager przez skonfigurowanie "blokowania dostępu" dla aplikacji "Microsoft Azure Management".

- [Jak skonfigurować dostęp warunkowy, aby blokować dostęp do usługi Azure Resources](../role-based-access-control/conditional-access-azure-management.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

## <a name="incident-response"></a>Reagowanie na zdarzenia

*Aby uzyskać więcej informacji, zobacz temat [Azure Security test: odpowiedź na zdarzenia](/azure/security/benchmarks/security-controls-v2-incident-response).*

### <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1: przygotowanie — aktualizacja procesu reagowania na incydenty dla platformy Azure

**Wskazówki**: Upewnij się, że organizacja ma procesy odpowiadające na zdarzenia związane z bezpieczeństwem, Zaktualizowano te procesy na platformie Azure i regularnie wykonuje je w celu zapewnienia gotowości.

- [Implementowanie zabezpieczeń w środowisku przedsiębiorstwa](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Przewodnik odwołuje się do odpowiedzi na zdarzenia](/microsoft-365/downloads/IR-Reference-Guide.pdf)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="ir-2-preparation--setup-incident-notification"></a>IR-2: przygotowanie — powiadomienie o zdarzeniu instalacji

**Wskazówki**: Konfigurowanie informacji kontaktowych dotyczących zdarzeń zabezpieczeń w Azure Security Center. Informacje kontaktowe są używane przez firmę Microsoft do skontaktowania się z Tobą, jeśli centrum Microsoft Security Response Center (MSRC) wykryje, że dostęp do danych jest uzyskiwany przez nielegalną lub nieautoryzowaną osobę. Dostępne są również opcje dostosowywania alertów dotyczących zdarzeń i powiadomień w różnych usługach platformy Azure w zależności od potrzeb związanych z odpowiedzią na zdarzenia. 

- [Jak ustawić kontakt z zabezpieczeniami Azure Security Center](../security-center/security-center-provide-security-contact-details.md)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3: wykrywanie i analiza — Tworzenie zdarzeń na podstawie alertów o wysokiej jakości 

**Wskazówki**: Upewnij się, że masz proces tworzenia alertów o wysokiej jakości i mierzenia jakości alertów. Dzięki temu można uczyć się lekcji od przeszłych zdarzeń i określić priorytety alertów dla analityków, dzięki czemu nie będą one tracić czasu na fałszywie dodatnich. 

Alerty o wysokiej jakości można kompilować na podstawie doświadczeń z przeszłych zdarzeń, sprawdzonych źródeł społeczności i narzędzi przeznaczonych do generowania i czyszczenia alertów przez odmowę i skorelowanie różnorodnych źródeł sygnałów. 

Azure Security Center oferuje alerty wysokiej jakości dla wielu zasobów platformy Azure. Łącznika danych ASC można użyć do przesyłania strumieniowego alertów do usługi Azure wskaźnikowej. Wskaźnik oceny platformy Azure umożliwia tworzenie zaawansowanych reguł alertów w celu automatycznego generowania zdarzeń na potrzeby badania. 

Eksportuj alerty i zalecenia dotyczące Azure Security Center przy użyciu funkcji eksportowania, aby pomóc identyfikować zagrożenia dla zasobów platformy Azure. Eksportuj alerty i zalecenia ręcznie lub w stały sposób ciągły.

- [Jak skonfigurować eksportowanie](../security-center/continuous-export.md)

- [Jak przesłać strumieniowo alerty do usługi Azure wskaźnikowego](../sentinel/connect-azure-security-center.md)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="ir-4-detection-and-analysis--investigate-an-incident"></a>IR-4: wykrywanie i analiza — Zbadaj zdarzenie

**Wskazówki**: Upewnij się, że analitycy mogą wykonywać zapytania i używać różnorodnych źródeł danych podczas badania potencjalnych zdarzeń, aby zbudować pełny wgląd w to, co się stało. Należy zebrać różne dzienniki, aby śledzić działania potencjalnej osoby atakującej w łańcuchu kasowania, aby uniknąć niewidomych plamek.  Należy również upewnić się, że szczegółowe informacje i uczenia są przechwytywane dla innych analityków i w przyszłości.  

Źródła danych do zbadania obejmują scentralizowane źródła rejestrowania, które są już zbierane z usług w zakresie i działających systemów, ale mogą również obejmować:

- Dane sieci — używanie dzienników przepływów sieciowych grup zabezpieczeń, platformy Azure Network Watcher i Azure Monitor do przechwytywania dzienników przepływu sieci i innych informacji analitycznych. 

- Migawki uruchomionych systemów: 

    - Użyj funkcji migawek maszyny wirtualnej platformy Azure, aby utworzyć migawkę dysku działającego systemu. 

    - Użyj możliwości natywnego zrzutu pamięci systemu operacyjnego, aby utworzyć migawkę pamięci uruchomionego systemu.

    - Użyj funkcji Snapshot w ramach usług platformy Azure lub własnej funkcji oprogramowania, aby utworzyć migawki uruchomionych systemów.

Platforma Azure — wskaźnik przedstawia szeroką analizę danych w praktycznie dowolnym źródle dziennika i portalu zarządzania przypadkami do zarządzania pełnym cyklem życia zdarzeń. Informacje o analizie podczas badania mogą być powiązane ze zdarzeniami w celu śledzenia i raportowania. 

- [Tworzenie migawek dysku maszyny z systemem Windows](../virtual-machines/windows/snapshot-copy-managed-disk.md)

- [Tworzenie migawek dysku maszyny z systemem Linux](../virtual-machines/linux/snapshot-copy-managed-disk.md)

- [Microsoft Azure obsługi informacji diagnostycznych i kolekcji zrzutów pamięci](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/) 

- [Zbadaj zdarzenia za pomocą platformy Azure — wskaźnik](../sentinel/tutorial-investigate-cases.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>IR-5: wykrywanie i analiza — określanie priorytetów zdarzeń

**Wskazówki**: Zapewnij kontekst analitykom, na których zdarzenia mają być skoncentrowane na początku, na podstawie ważności alertów i poufności zasobów. 

Azure Security Center przypisuje ważność do każdego alertu, aby pomóc w ustaleniu, które alerty należy najpierw zbadać. Ważność jest oparta na tym, jak dobrze Security Center znajduje się w wyszukiwaniu lub analitycznie używanych do wystawiania alertu, a także poziom pewności, że istniało złośliwe zamiar za działanie, które spowodowało wygenerowanie alertu.

Ponadto należy oznaczyć zasoby przy użyciu tagów i utworzyć system nazewnictwa, aby identyfikować i klasyfikować zasoby platformy Azure, szczególnie te, które przetwarzają dane poufne.  Odpowiedzialność za korygowanie alertów zależy od zagrożenia dla zasobów platformy Azure i środowiska, w którym wystąpiło zdarzenie.

- [Alerty zabezpieczeń w Centrum zabezpieczeń Azure](../security-center/security-center-alerts-overview.md)

- [Organizowanie zasobów platformy Azure przy użyciu tagów](/azure/azure-resource-manager/resource-group-using-tags)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="ir-6-containment-eradication-and-recovery--automate-the-incident-handling"></a>IR-6: zawieranie, likwidowanie i odzyskiwanie — Automatyzacja obsługi zdarzeń

**Wskazówki**: automatyzuje ręczne powtarzające się zadania w celu przyspieszenia czasu reakcji i zmniejszenia obciążenia dla analityków. Wykonanie ręcznych zadań trwa dłużej, spowalnia każde zdarzenie i zmniejsza liczbę incydentów, które może obsłużyć analityk. Zadania ręczne zwiększają również zmęczenie analityków, które zwiększają ryzyko wystąpienia błędu ludzkiego powodującego opóźnienia i obniżają możliwości skoncentrowania się na skomplikowanych zadaniach analityków. Korzystając z funkcji automatyzacji przepływu pracy w Azure Security Center i wskaźnik platformy Azure, można automatycznie wyzwalać akcje lub uruchamiać element PlayBook w celu reagowania na przychodzące alerty zabezpieczeń. Element PlayBook wykonuje akcje, takie jak wysyłanie powiadomień, wyłączanie kont i izolowanie problematycznych sieci. 

- [Konfigurowanie automatyzacji przepływu pracy w Security Center](../security-center/workflow-automation.md)

- [Konfigurowanie zautomatyzowanych odpowiedzi na zagrożenia w Azure Security Center](../security-center/tutorial-security-incident.md#triage-security-alerts)

- [Konfigurowanie zautomatyzowanych odpowiedzi na zagrożenia na platformie Azure — wskaźnik](../sentinel/tutorial-respond-threats-playbook.md)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

## <a name="posture-and-vulnerability-management"></a>Stan i zarządzanie lukami w zabezpieczeniach

*Aby uzyskać więcej informacji, zobacz [test dotyczący zabezpieczeń platformy Azure: stan i zarządzanie lukami w zabezpieczeniach](/azure/security/benchmarks/security-controls-v2-vulnerability-management).*

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8: przeprowadzenie regularnej symulacji ataków

**Wskazówki**: w razie potrzeby przeprowadź testowanie penetracji lub czerwone działania zespołu na Twoich zasobach platformy Azure i zapewnij korektę wszystkich krytycznych ustaleń dotyczących zabezpieczeń.
Postępuj zgodnie z Microsoft Cloud regułami testowania penetracji, aby upewnić się, że testy penetracji nie naruszają zasad firmy Microsoft. Korzystaj z strategii firmy Microsoft i wykonywania testów na żywo z obsługą tworzenia zespołu, usług i aplikacji w chmurze, które są zarządzane przez firmę Microsoft.

- [Testowanie penetracji na platformie Azure](../security/fundamentals/pen-testing.md)

- [Reguły testowania penetracji zaangażowania](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft Cloud Tworzenie zespołu Red.](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: udostępnione

## <a name="governance-and-strategy"></a>Ład i strategia

*Aby uzyskać więcej informacji, zobacz temat [Azure Security Tests: ładu and strategia](/azure/security/benchmarks/security-controls-v2-governance-strategy).*

### <a name="gs-1-define-asset-management-and-data-protection-strategy"></a>GS-1: Definiowanie strategii zarządzania zasobami i ochrony danych 

**Wskazówki**: Zadbaj o to, aby udokumentować i komunikować się z przejrzystą strategią ciągłego monitorowania i ochrony systemów i danych. Ustalanie priorytetów odnajdywania, oceny, ochrony i monitorowania o krytycznym znaczeniu dla firmy. 

Ta strategia powinna obejmować udokumentowane wskazówki, zasady i standardy dla następujących elementów: 

-   Standard klasyfikacji danych zgodny z ryzykiem biznesowym

-   Organizacja zabezpieczeń wgląd w czynniki ryzyka i spis zasobów 

-   Zatwierdzenie przez organizację zabezpieczeń usług platformy Azure do użycia 

-   Bezpieczeństwo zasobów w ramach cyklu życia

-   Wymagana strategia kontroli dostępu zgodnie z klasyfikacją danych organizacji

-   Korzystanie z funkcji ochrony danych natywnych platformy Azure i innych firm

-   Wymagania dotyczące szyfrowania danych dla przypadków użycia w trakcie tranzytu i w czasie spoczynku

-   Odpowiednie standardy kryptograficzne

Aby uzyskać więcej informacji, zobacz następujące odwołania:
- [Zalecenie dotyczące architektury zabezpieczeń platformy Azure — magazyn, dane i szyfrowanie](https://docs.microsoft.com/azure/architecture/framework/security/storage-data-encryption?toc=/security/compass/toc.json&amp;bc=/security/compass/breadcrumb/toc.json)

- [Podstawy zabezpieczeń platformy Azure — zabezpieczenia, szyfrowanie i magazyn danych platformy Azure](../security/fundamentals/encryption-overview.md)

- [Środowisko wdrażania chmury — najlepsze rozwiązania z zakresu zabezpieczeń i szyfrowania danych platformy Azure](https://docs.microsoft.com/azure/security/fundamentals/data-encryption-best-practices?toc=/azure/cloud-adoption-framework/toc.json&amp;bc=/azure/cloud-adoption-framework/_bread/toc.json)

- [Test wydajności Azure Security — zarządzanie zasobami](/azure/security/benchmarks/security-benchmark-v2-asset-management)

- [Test porównawczy zabezpieczeń platformy Azure — Ochrona danych](/azure/security/benchmarks/security-benchmark-v2-data-protection)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="gs-2-define-enterprise-segmentation-strategy"></a>GS-2: Definiowanie strategii segmentacji przedsiębiorstwa 

**Wskazówki**: Ustanów strategię całego przedsiębiorstwa do segmentacji dostępu do zasobów przy użyciu kombinacji tożsamości, sieci, aplikacji, subskrypcji, grupy zarządzania i innych kontrolek.

Należy starannie zrównoważyć potrzebę rozdzielania zabezpieczeń, aby umożliwić codzienne działanie systemów, które muszą komunikować się ze sobą i uzyskiwać dostęp do danych.

Upewnij się, że strategia segmentacji jest zaimplementowana spójnie między typami formantów, w tym zabezpieczeniami sieci, modelami tożsamości i dostępu, a także z kontrolkami dostępu i dostępem do aplikacji.

- [Wskazówki dotyczące strategii segmentacji na platformie Azure (wideo)](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Wskazówki dotyczące strategii segmentacji na platformie Azure (dokument)](/security/compass/governance#enterprise-segmentation-strategy)

- [Wyrównaj segmentację sieci przy użyciu strategii segmentacji przedsiębiorstwa](/security/compass/network-security-containment#align-network-segmentation-with-enterprise-segmentation-strategy)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="gs-3-define-security-posture-management-strategy"></a>GS-3: Definiowanie strategii zarządzania stan zabezpieczeń

**Wskazówki**: stałe mierzenie i łagodzenie ryzyka dla poszczególnych zasobów i środowiska, w którym są hostowane. Ustalanie priorytetów zasobów o wysokiej wartości i wysoce narażonych obszarów ataków, takich jak opublikowane aplikacje, punkty danych przychodzących i wychodzących sieci, punkty końcowe użytkowników i administratorów itp.

- [Azure Security test — stan i zarządzanie lukami w zabezpieczeniach](/azure/security/benchmarks/security-benchmark-v2-posture-vulnerability-management)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="gs-4-align-organization-roles-responsibilities-and-accountabilities"></a>GS-4: Wyrównaj role organizacji, obowiązki i accountabilities

**Wskazówki**: Zadbaj o to, aby udokumentować i komunikować się z przejrzystą strategią dotyczącą ról i obowiązków w organizacji zabezpieczeń. Ustalaj priorytety w celu zapewnienia jasnej odpowiedzialności za decyzje dotyczące zabezpieczeń, przenosząc wszystkie osoby na współużytkowany model odpowiedzialności i przeszkolej zespoły techniczne dotyczące technologii, aby zabezpieczyć chmurę.

- [Najlepsze rozwiązanie w zakresie zabezpieczeń Azure 1 — ludzie: Edukacja zespołów w podróży w zakresie zabezpieczeń w chmurze](/azure/cloud-adoption-framework/security/security-top-10#1-people-educate-teams-about-the-cloud-security-journey)

- [Najlepsze rozwiązanie dotyczące zabezpieczeń platformy Azure 2 — ludzie: Edukacja zespołów w technologii zabezpieczeń chmury](/azure/cloud-adoption-framework/security/security-top-10#2-people-educate-teams-on-cloud-security-technology)

- [Najlepsze rozwiązanie dotyczące zabezpieczeń platformy Azure 3 — proces: Przypisywanie odpowiedzialności za decyzje dotyczące zabezpieczeń chmury](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="gs-5-define-network-security-strategy"></a>GS-5: Definiowanie strategii zabezpieczeń sieci

**Wskazówki**: ustanowienie podejścia zabezpieczeń sieci platformy Azure jako części ogólnej strategii kontroli dostępu zabezpieczeń w organizacji.  

Ta strategia powinna obejmować udokumentowane wskazówki, zasady i standardy dla następujących elementów: 

-   Scentralizowane zarządzanie siecią i odpowiedzialność w zakresie zabezpieczeń

-   Model segmentacji sieci wirtualnej wyrównany do strategii segmentacji przedsiębiorstwa

-   Strategia korygowania w różnych scenariuszach zagrożeń i ataków

-   Strategia internetowa i dotycząca ruchu przychodzącego i wychodzącego

-   Strategia międzyłączności w chmurze hybrydowej i lokalnej

-   Aktualne artefakty zabezpieczeń sieci (np. diagramy sieciowe, Architektura sieci odniesienia)

Aby uzyskać więcej informacji, zobacz następujące odwołania:
- [Najlepsze rozwiązanie dotyczące zabezpieczeń platformy Azure 11 — architektura. Pojedyncza ujednolicona strategia zabezpieczeń](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Test porównawczy zabezpieczeń platformy Azure — Zabezpieczenia sieci](/azure/security/benchmarks/security-benchmark-v2-network-security)

- [Omówienie zabezpieczeń sieci platformy Azure](../security/fundamentals/network-overview.md)

- [Strategia architektury sieci przedsiębiorstwa](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="gs-6-define-identity-and-privileged-access-strategy"></a>GS-6: Definiowanie strategii dostępu Identity i Privileged Access

**Wskazówki**: ustanowienie podejścia do tożsamości platformy Azure i dostępu uprzywilejowanego w ramach ogólnej strategii kontroli dostępu zabezpieczeń w organizacji.  

Ta strategia powinna obejmować udokumentowane wskazówki, zasady i standardy dla następujących elementów: 

-   Scentralizowany system tożsamości i uwierzytelniania oraz połączenie z innymi wewnętrznymi i zewnętrznymi systemami tożsamości

-   Metody silnego uwierzytelniania w różnych przypadkach użycia i warunkach

-   Ochrona użytkowników z wysokim poziomem uprawnień

-   Monitorowanie i obsługa nietypowych działań użytkowników  

-   Przegląd tożsamości i dostępu użytkownika oraz proces uzgadniania

Aby uzyskać więcej informacji, zobacz następujące odwołania:

- [Test porównawczy zabezpieczeń Azure — Zarządzanie tożsamościami](/azure/security/benchmarks/security-benchmark-v2-identity-management)

- [Test porównawczy zabezpieczeń platformy Azure — uprzywilejowany dostęp](/azure/security/benchmarks/security-benchmark-v2-privileged-access)

- [Najlepsze rozwiązanie dotyczące zabezpieczeń platformy Azure 11 — architektura. Pojedyncza ujednolicona strategia zabezpieczeń](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Omówienie zabezpieczeń usługi Azure Identity Management](../security/fundamentals/identity-management-overview.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="gs-7-define-logging-and-threat-response-strategy"></a>GS-7: Definiowanie strategii rejestrowania i reagowania na zagrożenia

**Wskazówki**: Ustanów strategię rejestrowania i reagowania na zagrożenia, aby szybko wykrywać i rozwiązywać zagrożenia oraz spełnić wymagania dotyczące zgodności. Ustalaj priorytety, dostarczając analityków z alertami wysokiej jakości i bezproblemowe środowisko, aby umożliwić im skoncentrowanie się na zagrożeniach, a nie na poziomie integracji i ręcznej. 

Ta strategia powinna obejmować udokumentowane wskazówki, zasady i standardy dla następujących elementów: 

-   Rola i obowiązki organizacji operacji zabezpieczeń (SecOP) 

-   Dobrze zdefiniowany proces reagowania na zdarzenia z Instytutem NIST lub inną platformą branżową 

-   Przechwytywanie i przechowywanie dzienników w celu zapewnienia obsługi wykrywania zagrożeń, reagowania na zdarzenia i zgodności

-   Scentralizowana widoczność i korelacja informacji dotyczących zagrożeń przy użyciu SIEM, natywnych możliwości platformy Azure i innych źródeł 

-   Plan komunikacji i powiadomień z klientami, dostawcami i zainteresowanymi stronami

-   Używanie platform macierzystych i innych firm platformy Azure do obsługi zdarzeń, takich jak rejestrowanie i wykrywanie zagrożeń, dowodowych oraz korygowanie i eliminowanie ataków

-   Procesy obsługi zdarzeń i działań po zdarzeniu, takie jak zdobyte doświadczenia i przechowywanie dowodów

Aby uzyskać więcej informacji, zobacz następujące odwołania:

- [Test porównawczy zabezpieczeń platformy Azure — rejestrowanie i wykrywanie zagrożeń](/azure/security/benchmarks/security-benchmark-v2-logging-threat-detection)

- [Test porównawczy zabezpieczeń platformy Azure — odpowiedź na zdarzenie](/azure/security/benchmarks/security-benchmark-v2-incident-response)

- [Azure Security Best Practice 4 — proces. Aktualizowanie procesów odpowiedzi na zdarzenia dla chmury](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Przewodnik wdrażania platformy Azure, rejestrowania i podejmowania decyzji dotyczących raportowania](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

- [Skalowanie, zarządzanie i monitorowanie w przedsiębiorstwie platformy Azure](/azure/cloud-adoption-framework/ready/enterprise-scale/management-and-monitoring)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

## <a name="next-steps"></a>Następne kroki

- Zobacz [Omówienie usługi Azure Security test w wersji 2](/azure/security/benchmarks/overview)
- Dowiedz się więcej o [punktach odniesienia zabezpieczeń platformy Azure](/azure/security/benchmarks/security-baselines-overview)
