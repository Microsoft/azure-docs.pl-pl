---
title: Podstawa zabezpieczeń platformy Azure dla Azure Advisor
description: Linia bazowa zabezpieczeń Azure Advisor zawiera wskazówki i zasoby dotyczące procedur związanych z wdrażaniem zaleceń dotyczących zabezpieczeń określonych w teście zabezpieczeń platformy Azure.
author: msmbaldwin
ms.service: advisor
ms.topic: conceptual
ms.date: 11/17/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 24935f1b10ec11bb546cb08e06430d1e0b5c31fa
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/26/2020
ms.locfileid: "96183332"
---
# <a name="azure-security-baseline-for-azure-advisor"></a>Podstawa zabezpieczeń platformy Azure dla Azure Advisor

Ta linia bazowa zabezpieczeń stosuje wskazówki z programu [Azure Security test w wersji 2,0](../security/benchmarks/overview.md) do Azure Advisor. Test porównawczy zabezpieczeń platformy Azure zawiera zalecenia dotyczące sposobu zabezpieczania rozwiązań w chmurze na platformie Azure. Zawartość jest pogrupowana według **kontroli zabezpieczeń** zdefiniowanych przez program Azure Security test i powiązane wskazówki dotyczące Azure Advisor. **Kontrolki** nie mają zastosowania do Azure Advisor zostały wykluczone.

Aby dowiedzieć się, jak Azure Advisor całkowicie mapować do testu porównawczego zabezpieczeń platformy Azure, zobacz [pełny Azure Advisor pliku mapowania linii bazowej zabezpieczeń](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="identity-management"></a>Zarządzanie tożsamością

*Aby uzyskać więcej informacji, zobacz [Test porównawczy zabezpieczeń platformy Azure: zarządzanie tożsamościami](../security/benchmarks/security-controls-v2-identity-management.md).*

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>ZT-1: Ujednolicenie usługi Azure Active Directory jako centralnego systemu obsługi tożsamości i uwierzytelniania

**Wskazówki**: Azure Advisor używa Azure Active Directory (Azure AD) jako domyślnej usługi zarządzania tożsamościami i dostępem. Normalizuj usługę Azure AD, aby zarządzać zarządzaniem tożsamościami i dostępem w organizacji w programie:

- Zasoby Microsoft Cloud, takie jak Azure Portal, Azure Storage, Azure Virtual Machine (Linux i Windows), Azure Key Vault, PaaS i aplikacje SaaS
- Zasoby organizacji, takie jak aplikacje na platformie Azure lub zasoby sieci firmowej

Upewnij się, że ochrona usługi Azure AD jest wysokim priorytetem w zakresie zabezpieczeń w chmurze w organizacji. Usługa Azure AD zapewnia także bezpieczną ocenę tożsamości, która pomaga ocenić stan zabezpieczeń tożsamości w porównaniu z zaleceniami dotyczącymi najlepszych rozwiązań firmy Microsoft. Użyj tego wskaźnika, aby ocenić, jak ściśle Twoja konfiguracja spełnia zalecenia dotyczące najlepszych rozwiązań oraz poprawić stan zabezpieczeń.

Należy pamiętać, że usługa Azure AD obsługuje tożsamości zewnętrzne, które umożliwiają użytkownikom bez konto Microsoft logowania się do aplikacji i zasobów przy użyciu tożsamości zewnętrznej.

- [Dzierżawa w Azure Active Directory](../active-directory/develop/single-and-multi-tenant-apps.md) 

- [Jak utworzyć i skonfigurować wystąpienie usługi Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md) 

- [Użyj zewnętrznych dostawców tożsamości dla aplikacji](../active-directory/external-identities/identity-providers.md) 

- [Co to jest bezpieczna ocena tożsamości w Azure Active Directory](../active-directory/fundamentals/identity-secure-score.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="im-3-use-azure-ad-single-sign-on-sso-for-application-access"></a>ZT-3: Korzystanie z logowania jednokrotnego usługi Azure AD na potrzeby dostępu do aplikacji

**Wskazówki**: Azure Advisor używa Azure Active Directory (Azure AD), aby zapewnić zarządzanie tożsamościami i dostępem do zasobów platformy Azure, aplikacji w chmurze i aplikacji lokalnych. Dotyczy to zarówno tożsamości przedsiębiorstwa, takich jak pracownicy, jak i tożsamości zewnętrznych, takich jak partnerzy, sprzedawcy i dostawcy. 

Logowanie jednokrotne służy do zarządzania i bezpiecznego dostępu do danych i zasobów organizacji w środowisku lokalnym i w chmurze. Połącz wszystkich użytkowników, aplikacje i urządzenia z usługą Azure AD w celu zapewnienia bezproblemowego, bezpiecznego dostępu, lepszego wglądu i kontroli.

- [Informacje na temat logowania jednokrotnego do aplikacji za pomocą usługi Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>ZT-4: Używanie mechanizmów silnego uwierzytelniania dla każdego dostępu opartego na usłudze Azure Active Directory

**Wskazówki**: Azure Advisor używa Azure Active Directory (Azure AD), który obsługuje ścisłą kontrolę uwierzytelniania przez uwierzytelnianie wieloskładnikowe i silne metody bezhasła.
- Uwierzytelnianie wieloskładnikowe — Włącz uwierzytelnianie wieloskładnikowe usługi Azure AD i postępuj zgodnie z zaleceniami Azure Security Center zarządzaniem tożsamościami i dostępem, aby zapoznać się z najlepszymi rozwiązaniami w konfiguracji uwierzytelniania wieloskładnikowego. Uwierzytelnianie wieloskładnikowe można wymusić na wszystkich, wybrać opcję Użytkownicy lub na poziomie użytkownika na podstawie warunków logowania i czynników ryzyka.
- Uwierzytelnianie bezhasło — dostępne są trzy opcje uwierzytelniania bezhasła. Są to usługi Windows Hello dla firm, Microsoft Authenticator App i lokalne metody uwierzytelniania, takie jak karty inteligentne.

W przypadku administratorów i użytkowników uprzywilejowanych należy upewnić się, że używany jest najwyższy poziom metody silnego uwierzytelniania, a następnie wprowadzić odpowiednią politykę silnego uwierzytelniania dla innych użytkowników.

- [Jak włączyć uwierzytelnianie wieloskładnikowe na platformie Azure](../active-directory/authentication/howto-mfa-getstarted.md) 

- [Wprowadzenie do opcji uwierzytelniania bez hasła w przypadku usługi Azure Active Directory](../active-directory/authentication/concept-authentication-passwordless.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="im-5-monitor-and-alert-on-account-anomalies"></a>ZT-5: Monitorowanie anomalii kont i wyświetlanie alertów

**Wskazówki**: Azure Advisor jest zintegrowany z usługą Azure Active Directory (Azure AD), w której znajdują się następujące źródła danych:
- Logowanie — raport dotyczący logowania zawiera informacje na temat użycia zarządzanych aplikacji i działań związanych z logowaniem użytkowników.
- Dzienniki inspekcji — udostępnia możliwość śledzenia wszystkich zmian wprowadzanych przez różne funkcje usługi Azure AD za pomocą dzienników. Działania umieszczane w dziennikach inspekcji to na przykład zmiany wprowadzone w dowolnych zasobach usługi Azure AD, takie jak dodawanie lub usuwanie użytkowników, aplikacji, grup, ról i zasad.
- Ryzykowne logowanie — ryzykowne logowanie jest wskaźnikiem próby logowania, które mogło zostać wykonane przez kogoś, kto nie jest uprawnionym właścicielem konta użytkownika.
- Użytkownicy oflagowani w związku z ryzykiem — ryzykowny użytkownik jest wskaźnikiem konta użytkownika, którego bezpieczeństwo mogło zostać naruszone.

Te źródła danych umożliwiają integrację z Azure Monitor, wskaźnikiem produktów platformy Azure lub systemami SIEM innych firm. Skonfiguruj alerty dotyczące Azure Security Center w przypadku niektórych podejrzanych działań, takich jak nadmierna liczba nieudanych prób uwierzytelnienia, czyli przestarzałe konta w subskrypcji.

Usługa Azure Advanced Threat Protection (ATP) to rozwiązanie z zakresu zabezpieczeń, które może używać sygnałów usługi Active Directory do identyfikowania, wykrywania i badania zaawansowanych zagrożeń, naruszonych tożsamości i złośliwych działań wewnętrznych.

- [Raporty dotyczące inspekcji w usłudze Azure Active Directory](../active-directory/reports-monitoring/concept-audit-logs.md) 

- [Jak monitorować działania użytkowników związane z tożsamościami i dostępem w usłudze Azure Security Center](../security-center/security-center-identity-access.md) 

- [Alerty w module analizy zagrożeń usługi Azure Security Center](../security-center/alerts-reference.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>Wiadomości błyskawiczne — 6: Ogranicz dostęp do zasobów platformy Azure na podstawie warunków

**Wskazówki**: Azure Advisor obsługuje funkcję dostępu warunkowego Azure Active Directory (Azure AD) w celu uzyskania bardziej szczegółowej kontroli dostępu na podstawie warunków zdefiniowanych przez użytkownika. Na przykład Logowanie użytkowników z określonych zakresów adresów IP będzie wymagało użycia uwierzytelniania wieloskładnikowego podczas logowania. Szczegółowe zasady zarządzania sesjami uwierzytelniania mogą być również używane dla różnych przypadków użycia.

- [Omówienie dostępu warunkowego platformy Azure](../active-directory/conditional-access/overview.md) 

- [Typowe zasady dostępu warunkowego](../active-directory/conditional-access/concept-conditional-access-policy-common.md) 

- [Konfigurowanie zarządzania sesjami uwierzytelniania przy użyciu dostępu warunkowego](../active-directory/conditional-access/howto-conditional-access-session-lifetime.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

## <a name="privileged-access"></a>Dostęp uprzywilejowany

*Aby uzyskać więcej informacji, zobacz [Test porównawczy zabezpieczeń platformy Azure: Dostęp uprzywilejowany](../security/benchmarks/security-controls-v2-privileged-access.md).*

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2: ograniczanie dostępu administracyjnego do systemów o krytycznym znaczeniu dla firmy

**Wskazówki**: Azure Advisor używa kontroli dostępu opartej na rolach (Azure RBAC) na platformie Azure, aby odizolować dostęp do systemów o krytycznym znaczeniu dla firmy przez ograniczenie kont, które mają przyznane uprawnienia dostępu do subskrypcji i grup zarządzania, do których należą.

Ogranicz dostęp do systemów zarządzania, tożsamości i zabezpieczeń, które mają dostęp administracyjny do krytycznych dostępu do firmy, takich jak kontrolery domena usługi Active Directory (DC), narzędzia zabezpieczeń i narzędzia do zarządzania systemem, z agentami zainstalowanymi w systemach krytycznych dla firmy. Osoby atakujące, którzy naruszają te systemy zarządzania i zabezpieczeń, mogą natychmiast weaponize je w celu naruszenia krytycznych zasobów firmy.

Wszystkie typy kontroli dostępu powinny być wyrównane do strategii segmentacji przedsiębiorstwa w celu zapewnienia spójnej kontroli dostępu.

- [Składniki platformy Azure i Model referencyjny](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Dostęp do grupy zarządzania](../governance/management-groups/overview.md#management-group-access)

- [Administratorzy subskrypcji platformy Azure](../cost-management-billing/manage/add-change-subscription-administrator.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="pa-3-review-and-reconcile-user-access-regularly"></a>DU-3: Regularny przegląd i uzgadnianie dostępu użytkowników

**Wskazówki**: Azure Advisor używa kont usługi Azure Active Directory (Azure AD) do zarządzania zasobami, przeglądania kont użytkowników i przypisywania dostępu regularnie, aby zapewnić, że konta i ich dostęp są prawidłowe. Zaimplementuj przeglądy dostępu do usługi Azure AD, aby przeglądać członkostwa w grupach, uzyskiwać dostęp do aplikacji dla przedsiębiorstw i przypisań ról. Raportowanie usługi Azure AD może zapewnić dzienniki, aby ułatwić odnajdywanie starych kont. 

Ponadto Użyj funkcji Privileged Identity Management usługi Azure AD, aby utworzyć przepływ pracy raportu przeglądu dostępu w celu ułatwienia procesu recenzowania. Privileged Identity Management można również skonfigurować do zgłaszania alertów w przypadku tworzenia nadmiernej liczby kont administratora oraz do identyfikowania kont administratorów, które są przestarzałe lub nieprawidłowo skonfigurowane.

Należy pamiętać, że niektóre usługi platformy Azure obsługują lokalnych użytkowników i role, które nie są zarządzane za pomocą usługi Azure AD. Klienci będą musieli osobno zarządzać tymi użytkownikami.

- [Tworzenie przeglądu dostępu do ról zasobów platformy Azure w Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-resource-roles-start-access-review.md) 

- [Jak korzystać z przeglądów tożsamości i dostępu w usłudze Azure AD](/azure/active-directory/governance/access-reviews-overvie)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="pa-6-use-privileged-access-workstations"></a>PA-6: używanie uprzywilejowanych stacji roboczych dostępu

**Wskazówki**: zabezpieczone, izolowane stacje robocze mają kluczowe znaczenie dla bezpieczeństwa ról poufnych, takich jak Administratorzy, deweloperzy i krytyczne operatory usług. 

Używaj wysoce bezpiecznych stacji roboczych użytkowników i/lub Azure bastionu na potrzeby zadań administracyjnych. Wybierz Azure Active Directory (Azure AD), Microsoft Defender Advanced Threat Protection (ATP), w tym Microsoft Intune do wdrożenia bezpiecznej i zarządzanej stacji roboczej użytkownika na potrzeby zadań administracyjnych. 

Centralne zarządzanie zabezpieczonymi stacjami roboczymi, aby wymusić bezpieczną konfigurację, w tym silne uwierzytelnianie, oprogramowanie i sprzętowe linie bazowe, ograniczony dostęp logiczny i sieciowy.

- [Informacje na temat stacji roboczych uprzywilejowanego dostępu](../active-directory/devices/concept-azure-managed-workstation.md) 
 
- [Wdrażanie stacji roboczej z dostępem uprzywilejowanym](../active-directory/devices/howto-azure-managed-workstation.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="pa-7-follow-just-enough-administration-least-privilege-principle"></a>DU-7: Przestrzeganie podejścia wystarczającego zakresu administracji (zasada stosowania najniższych uprawnień) 

**Wskazówki**: Azure Advisor jest zintegrowany z kontrolą dostępu opartą na rolach (Azure RBAC) na potrzeby zarządzania swoimi zasobami. Funkcja RBAC platformy Azure umożliwia zarządzanie dostępem do zasobów platformy Azure za pomocą przypisań ról. 

Przypisywanie ról do użytkowników, grup jednostek usługi i zarządzanych tożsamości. Istnieją wstępnie zdefiniowane wbudowane role dla określonych zasobów, które mogą być objęte spisem lub zapytania za pomocą narzędzi takich jak interfejs wiersza polecenia platformy Azure, Azure PowerShell lub Azure Portal. Uprawnienia przypisane do zasobów za pomocą usługi Azure RBAC powinny być zawsze ograniczone do tego, co jest wymagane przez role. To uzupełnia podejście dostępu just in time (JIT) usługi Azure AD Privileged Identity Management (PIM) i powinno być okresowo weryfikowane.

Używaj wbudowanych ról do przydzielenia uprawnień i utwórz własną rolę tylko wtedy, gdy jest to wymagane.

Co to jest kontrola dostępu oparta na rolach (Azure RBAC) /role-based-access-control/overview.md 

- [Jak skonfigurować kontrolę RBAC na platformie Azure](../role-based-access-control/role-assignments-portal.md) 

- [Jak korzystać z przeglądów tożsamości i dostępu w usłudze Azure AD](../active-directory/governance/access-reviews-overview.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

## <a name="asset-management"></a>Zarządzanie zasobami

*Aby uzyskać więcej informacji, zobacz [Test porównawczy zabezpieczeń platformy Azure: Zarządzanie zasobami](../security/benchmarks/security-controls-v2-asset-management.md).*

### <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>ZZ-1: Upewnij się, że zespół ds. zabezpieczeń ma wgląd w ryzyko związane z zasobami

**Wskazówki**: Upewnij się, że zespoły ds. zabezpieczeń mają przyznane uprawnienia czytelnika zabezpieczeń w dzierżawie i subskrypcjach platformy Azure, aby umożliwić im monitorowanie zagrożeń bezpieczeństwa przy użyciu usługi Azure Security Center. 

W zależności od istniejącej struktury obowiązków zespołu ds. zabezpieczeń, monitorowanie zagrożeń bezpieczeństwa może być obowiązkiem centralnego zespołu ds. zabezpieczeń lub zespołu lokalnego. Niemniej jednak informacje na temat zabezpieczeń i ryzyka muszą być zawsze agregowane centralnie w ramach danej organizacji. 

Uprawnienia czytelnika zabezpieczeń mogą być stosowane szeroko do całej dzierżawy (główna grupa zarządzania) lub do zakresu w postaci grup zarządzania lub określonych subskrypcji. 

Uwaga: Do uzyskania wglądu w obciążenia i usługi mogą być wymagane dodatkowe uprawnienia. 

- [Omówienie roli czytelnika zabezpieczeń](../role-based-access-control/built-in-roles.md#security-reader)

- [Omówienie grup zarządzania platformy Azure](../governance/management-groups/overview.md)

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>AM-5: ograniczanie możliwości korzystania przez użytkowników z Azure Resource Manager

**Wskazówki**: Azure Advisor używa Azure Active Directory (Azure AD) na potrzeby tożsamości i uwierzytelniania, podczas gdy Azure Portal i Azure Resource Manager są używane do zarządzania usługą Advisor. 

Użyj dostępu warunkowego platformy Azure, aby ograniczyć możliwość współdziałania z Menedżerem zasobów platformy Azure przez skonfigurowanie "blokowania dostępu" dla aplikacji "Microsoft Azure Management" zgodnie z wymaganiami biznesowymi. 

- [Jak skonfigurować dostęp warunkowy, aby blokować dostęp do usługi Azure Resources](../role-based-access-control/conditional-access-azure-management.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

## <a name="logging-and-threat-detection"></a>Rejestrowanie i wykrywanie zagrożeń

*Aby uzyskać więcej informacji, zobacz [Test porównawczy zabezpieczeń platformy Azure: Rejestrowanie i wykrywanie zagrożeń](/azure/security/benchmarks/security-controls-v2-logging-threat-protection).*

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4: Włączanie rejestrowania dla zasobów platformy Azure

**Wskazówki**: dzienniki aktywności są automatycznie dostępne i zawierają wszystkie operacje zapisu (Put, post, Delete) dla zasobów Azure Advisor z wyjątkiem operacji odczytu (Get). 

Dzienników aktywności można użyć do znalezienia błędu podczas rozwiązywania problemów lub do monitorowania sposobu, w jaki użytkownik w organizacji zmodyfikował zasób.

- [Informacje o rejestrowaniu i różnych typach dzienników na platformie Azure](../azure-monitor/platform/platform-logs-overview.md)

- [Jak zbierać dzienniki platformy i metryki za pomocą Azure Monitor](../azure-monitor/platform/diagnostic-settings.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5: scentralizowane zarządzanie dziennikami zabezpieczeń i analiza

**Wskazówki**: scentralizowany magazyn rejestrowania i analiza w celu włączenia korelacji. Dla każdego źródła dziennika upewnij się, że masz przypisanego właściciela danych, wskazówki dotyczące dostępu, lokalizację magazynu, narzędzia używane do przetwarzania i uzyskiwania dostępu do danych oraz wymagania dotyczące przechowywania danych.

Upewnij się, że masz integrację dzienników aktywności platformy Azure z centralnym logowaniem. Pozyskiwanie dzienników za pośrednictwem Azure Monitor do agregowania danych zabezpieczeń wygenerowanych przez urządzenia, zasoby sieciowe i inne systemy zabezpieczeń. W Azure Monitor używać Log Analytics obszarów roboczych do wykonywania zapytań i przeprowadzania analiz oraz używania kont usługi Azure Storage do przechowywania długoterminowego i archiwizowania.

Ponadto należy włączyć i dołączyć dane do usługi Azure wskaźnikowej lub SIEM innych firm. Wiele organizacji decyduje się na korzystanie z platformy Azure na potrzeby danych "gorąca", które są często używane, i usługi Azure Storage dla danych "zimnych", które są rzadziej używane.

- [Jak zbierać dzienniki platformy i metryki za pomocą Azure Monitor](../azure-monitor/platform/diagnostic-settings.md) 

- [Jak dołączyć wskaźnik na platformie Azure](../sentinel/quickstart-onboard.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="lt-6-configure-log-storage-retention"></a>LT-6: Konfigurowanie przechowywania magazynu dzienników

**Wskazówki**: Upewnij się, że wszystkie konta magazynów lub log Analytics obszary robocze używane do przechowywania dzienników Azure Advisor mają ustawiony okres przechowywania dziennika zgodnie z przepisami obowiązującymi w organizacji.
W Azure Monitor można ustawić okres przechowywania Log Analytics obszaru roboczego zgodnie z regulacjami zgodności w organizacji. Użyj usługi Azure Storage Data Lake lub Log Analytics konta obszaru roboczego do przechowywania długoterminowego i archiwizowania.

- [Jak skonfigurować okres przechowywania obszaru roboczego Log Analytics](../azure-monitor/platform/manage-cost-storage.md) 

- [Przechowywanie dzienników zasobów na koncie usługi Azure Storage](../azure-monitor/platform/resource-logs.md#send-to-azure-storage)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

## <a name="incident-response"></a>Reagowanie na zdarzenia

*Aby uzyskać więcej informacji, zobacz [Test porównawczy zabezpieczeń platformy Azure: Reagowanie na zdarzenia](../security/benchmarks/security-controls-v2-incident-response.md).*

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

- [Organizowanie zasobów platformy Azure przy użyciu tagów](../azure-resource-manager/management/tag-resources.md)

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

### <a name="pv-8-conduct-regular-attack-simulation"></a>SL-8: przeprowadzanie regularnej symulacji ataków

**Wskazówki**: W razie potrzeby przeprowadź testy penetracyjne lub działania typu „red team” na zasobach platformy Azure i zapewnij skorygowanie wszystkich krytycznych ustaleń dotyczących zabezpieczeń.
Postępuj zgodnie z regułami dotyczącymi testów penetracyjnych w chmurze firmy Microsoft, aby upewnić się, że testy penetracyjne nie naruszają zasad firmy Microsoft. Wykorzystaj strategię i podejście do działań typu „red team” firmy Microsoft oraz przeprowadzaj testy penetracyjne aktywnej witryny w odniesieniu do zarządzanej przez firmę Microsoft infrastruktury w chmurze, usług i aplikacji.

- [Testy penetracyjne na platformie Azure](../security/fundamentals/pen-testing.md)

- [Reguły testów penetracyjnych zaangażowania](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Działania typu „red team” w chmurze firmy Microsoft](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Udostępnione

## <a name="governance-and-strategy"></a>Ład i strategia

*Aby uzyskać więcej informacji, zobacz [Test porównawczy zabezpieczeń platformy Azure: Ład i strategia](../security/benchmarks/security-controls-v2-governance-strategy.md).*

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
- [Zalecenia dotyczące architektury zabezpieczeń platformy Azure — magazyn, dane i szyfrowanie](/azure/architecture/framework/security/storage-data-encryption?amp;bc=%2fsecurity%2fcompass%2fbreadcrumb%2ftoc.json&toc=%2fsecurity%2fcompass%2ftoc.json)

- [Podstawy zabezpieczeń platformy Azure — zabezpieczenia, szyfrowanie i magazyn danych platformy Azure](../security/fundamentals/encryption-overview.md)

- [Cloud Adoption Framework — najlepsze rozwiązania z zakresu zabezpieczeń i szyfrowania danych platformy Azure](../security/fundamentals/data-encryption-best-practices.md?amp;bc=%2fazure%2fcloud-adoption-framework%2f_bread%2ftoc.json&toc=%2fazure%2fcloud-adoption-framework%2ftoc.json)

- [Test porównawczy zabezpieczeń platformy Azure — zarządzanie zasobami](/azure/security/benchmarks/security-benchmark-v2-asset-management)

- [Test porównawczy zabezpieczeń platformy Azure — ochrona danych](/azure/security/benchmarks/security-benchmark-v2-data-protection)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="gs-2-define-enterprise-segmentation-strategy"></a>ŁS-2: definiowanie strategii segmentacji przedsiębiorstwa 

**Wskazówki**: Ustanów strategię korporacyjną w celu segmentacji dostępu do zasobów przy użyciu kombinacji tożsamości, sieci, aplikacji, subskrypcji, grupy zarządzania, między innymi kontrolkami.

Należy starannie wyważyć potrzebę rozdzielenia zabezpieczeń z koniecznością umożliwienia codziennej pracy systemów, które muszą komunikować się ze sobą i uzyskiwać dostęp do danych.

Upewnij się, że strategia segmentacji jest zaimplementowana spójnie między typami kontrolek, w tym zabezpieczeniami sieci, modelami tożsamości i dostępu, a także uprawnieniami aplikacji lub modelami dostępu oraz kontrolkami procesów ludzkich.

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

**Wskazówki**: ustalanie tożsamości platformy Azure i uprzywilejowanego dostępu w ramach ogólnej strategii kontroli dostępu przedsiębiorstwa w organizacji.  

Ta strategia powinna obejmować udokumentowane wskazówki, zasady i standardy dla następujących elementów: 

-   Scentralizowany system obsługi tożsamości i uwierzytelniania oraz połączenie z innymi wewnętrznymi i zewnętrznymi systemami obsługi tożsamości

-   Metody silnego uwierzytelniania w ramach różnych przypadków użycia i warunków

-   Ochrona użytkowników o wysokim poziomie uprawnień

-   Monitorowanie i obsługa nietypowych działań użytkowników  

-   Przegląd tożsamości i dostępu użytkowników oraz proces uzgadniania

Przejrzyj linki, do których istnieją odwołania, aby uzyskać więcej informacji.

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

- Zobacz [Omówienie testu porównawczego zabezpieczeń platformy Azure w wersji 2](../security/benchmarks/overview.md)
- Dowiedz się więcej o [punktach odniesienia zabezpieczeń platformy Azure](../security/benchmarks/security-baselines-overview.md)