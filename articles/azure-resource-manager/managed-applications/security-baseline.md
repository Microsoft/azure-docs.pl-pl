---
title: Podstawa zabezpieczeń platformy Azure dla Azure Managed Applications
description: Linia bazowa zabezpieczeń Azure Managed Applications zawiera wskazówki i zasoby dotyczące procedur związanych z wdrażaniem zaleceń dotyczących zabezpieczeń określonych w teście zabezpieczeń platformy Azure.
author: msmbaldwin
ms.service: managed-applications
ms.topic: conceptual
ms.date: 12/01/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 42aea886e38622c773ac1ca0ea2533d8aa1c47bb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101717030"
---
# <a name="azure-security-baseline-for-azure-managed-applications"></a>Podstawa zabezpieczeń platformy Azure dla Azure Managed Applications

Ta linia bazowa zabezpieczeń stosuje wskazówki z programu [Azure Security test w wersji 2,0](../../security/benchmarks/overview.md) do Azure Managed Applications. Test porównawczy zabezpieczeń platformy Azure zawiera zalecenia dotyczące sposobu zabezpieczania rozwiązań w chmurze na platformie Azure. Zawartość jest pogrupowana według **kontroli zabezpieczeń** zdefiniowanych przez program Azure Security test i powiązane wskazówki dotyczące Azure Managed Applications. **Kontrolki** nie mają zastosowania do Azure Managed Applications zostały wykluczone.

Aby dowiedzieć się, jak Azure Managed Applications całkowicie mapować do testu porównawczego zabezpieczeń platformy Azure, zobacz [pełny Azure Managed Applications pliku mapowania linii bazowej zabezpieczeń](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Bezpieczeństwo sieci

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: bezpieczeństwo sieci](../../security/benchmarks/security-controls-v2-network-security.md).*

### <a name="ns-6-simplify-network-security-rules"></a>NS-6: uproszczenie reguł zabezpieczeń sieci

**Wskazówki**: Użyj tagów usługi Azure Virtual Network, aby zdefiniować kontrolę dostępu do sieci dla sieciowych grup zabezpieczeń lub zapory platformy Azure skonfigurowanej dla zasobów aplikacji zarządzanej platformy Azure. Podczas tworzenia reguł zabezpieczeń można użyć tagów usługi zamiast konkretnych adresów IP. Określając nazwę tagu usługi (na przykład: AzureResourceManager) w odpowiednim polu źródłowym lub docelowym reguły, można zezwolić na ruch dla odpowiedniej usługi lub go odrzucić. Firma Microsoft zarządza prefiksami adresów, które obejmują tag usługi, i automatycznie aktualizuje tag usługi jako adresy.

- [Użyj Azure Resource Manager tagów usługi](../../virtual-network/service-tags-overview.md#available-service-tags)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

## <a name="identity-management"></a>Zarządzanie tożsamością

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: zarządzanie tożsamościami](../../security/benchmarks/security-controls-v2-identity-management.md).*

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1: Standaryzacja usługi Azure Active Directory jako centralnego systemu tożsamości i uwierzytelniania

**Wskazówki**: Azure Managed Applications używa Azure Active Directory (Azure AD) jako domyślnej usługi zarządzania tożsamościami i dostępem. Zastandaryzacja usługi Azure AD, aby zarządzać zarządzaniem tożsamościami i dostępem w organizacji.

Platforma Azure udostępnia następujące wbudowane role platformy Azure umożliwiające autoryzowanie dostępu do aplikacji zarządzanych za pomocą usługi Azure AD i uwierzytelniania OAuth:

- Rola współautor aplikacji zarządzanej: umożliwia tworzenie zasobów aplikacji zarządzanej.

- Rola operatora aplikacji zarządzanej: umożliwia odczytywanie i wykonywanie akcji dotyczących zasobów aplikacji zarządzanej

- Czytnik aplikacji zarządzanych: umożliwia odczytywanie zasobów w zarządzanej aplikacji i żądanie dostępu JIT.

Aby uzyskać więcej informacji, zapoznaj się z następującymi dokumentami:

- [Rola współautora aplikacji zarządzanej](../../role-based-access-control/built-in-roles.md#managed-application-contributor-role)

- [Rola operatora aplikacji zarządzanej](../../role-based-access-control/built-in-roles.md#managed-application-contributor-role)

- [Czytnik aplikacji zarządzanych](../../role-based-access-control/built-in-roles.md#managed-applications-reader)

- [Dzierżawa w usłudze Azure Active Directory](../../active-directory/develop/single-and-multi-tenant-apps.md)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="im-2-manage-application-identities-securely-and-automatically"></a>IM-2: Bezpieczne i automatyczne zarządzanie tożsamościami aplikacji

**Wskazówki**: Użyj tożsamości zarządzanych przez platformę Azure do przyznawania uprawnień dla zarządzanych aplikacji. Zalecane jest korzystanie z funkcji tożsamości zarządzanej platformy Azure zamiast tworzenia bardziej zaawansowanych kont ludzkich w celu uzyskania dostępu do zasobów lub ich wykonywania w celu ograniczenia potrzeb zarządzania dodatkowymi poświadczeniami. Do usługi Azure Managed Applications można także przypisać tożsamość zarządzaną w celu natywnego uwierzytelnienia w innych usługach/zasobach platformy Azure, które obsługują uwierzytelnianie usługi Azure AD. Może to być przydatne, aby zapewnić łatwy dostęp z Azure Managed Applications do Azure Key Vault podczas pobierania wpisów tajnych. W przypadku korzystania z tożsamości zarządzanych tożsamość jest zarządzana przez platformę Azure i nie wymaga podawania ani rotacji kluczy tajnych.

Azure Managed Applications obsługuje w aplikacji dwa typy tożsamości:

- Tożsamość przypisana do systemu jest powiązana z zasobem konfiguracji. Jest ona usuwana, jeśli zasób konfiguracji został usunięty. Zasób konfiguracji może mieć tylko jedną tożsamość przypisaną do systemu.

- Tożsamość przypisana przez użytkownika to autonomiczny zasób platformy Azure, który można przypisać do tego zasobu konfiguracji. Zasób konfiguracji może mieć wiele tożsamości przypisanych do użytkownika.

Gdy nie można wykorzystać tożsamości zarządzanych, Utwórz jednostkę usługi z ograniczonymi uprawnieniami na poziomie zasobów aplikacji zarządzanej platformy Azure. Skonfiguruj te jednostki usługi z poświadczeniami certyfikatów i wróć tylko do wpisów tajnych klienta. W obu przypadkach Azure Key Vault mogą być używane w połączeniu z tożsamościami zarządzanymi przez platformę Azure, dzięki czemu środowisko uruchomieniowe (np. funkcja platformy Azure) może pobrać poświadczenie z magazynu kluczy.

- [Jak używać tożsamości zarządzanych do Azure Managed Applications](publish-managed-identity.md)

- [Tożsamości zarządzane przez platformę Azure](../../active-directory/managed-identities-azure-resources/overview.md)

- [Jednostka usługi platformy Azure](/powershell/azure/create-azure-service-principal-azureps) 

- [Użyj Azure Key Vault do rejestracji podmiotu zabezpieczeń](../../key-vault/general/authentication.md#app-identity-and-security-principals)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="im-3-use-azure-ad-single-sign-on-sso-for-application-access"></a>IM-3: Korzystanie z logowania jednokrotnego usługi Azure AD na potrzeby dostępu do aplikacji

**Wskazówki**: Azure Managed Applications używać Azure Active Directory, aby zapewnić zarządzanie tożsamościami i dostępem do zasobów platformy Azure, aplikacji w chmurze i aplikacji lokalnych. Obejmuje to tożsamości przedsiębiorstwa, takie jak pracownicy, a także tożsamości zewnętrzne, takie jak partnerzy, dostawcy i dostawcy. Umożliwia to logowanie jednokrotne w celu zarządzania i bezpiecznego dostępu do danych i zasobów organizacji w środowisku lokalnym i w chmurze. Połącz wszystkich użytkowników, aplikacje i urządzenia z usługą Azure AD w celu zapewnienia bezproblemowego, bezpiecznego dostępu, lepszego wglądu i większej kontroli.

- [Informacje na temat logowania jednokrotnego aplikacji za pomocą usługi Azure AD](../../active-directory/manage-apps/what-is-single-sign-on.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4: Używanie kontrolek silnego uwierzytelniania dla całego dostępu opartego na usłudze Azure Active Directory

**Wskazówki**: Azure Managed Applications używa Azure Active Directory, które obsługuje silne uwierzytelnianie za pomocą uwierzytelniania wieloskładnikowego i silnych metod bezhaseł.
- Uwierzytelnianie wieloskładnikowe — Włącz uwierzytelnianie wieloskładnikowe usługi Azure AD i postępuj zgodnie z zaleceniami Azure Security Center zarządzania tożsamościami i dostępem, aby zapoznać się z najlepszymi rozwiązaniami dotyczącymi konfiguracji uwierzytelniania wieloskładnikowego. Uwierzytelnianie wieloskładnikowe można wymusić na wszystkich, wybrać opcję Użytkownicy lub na poziomie użytkownika na podstawie warunków logowania i czynników ryzyka.
- Uwierzytelnianie bez hasła — dostępne są trzy opcje uwierzytelniania bez hasła: Windows Hello dla firm, aplikacja Microsoft Authenticator i lokalne metody uwierzytelniania, takie jak karty inteligentne.

W przypadku użytkowników z uprawnieniami administratora i uprzywilejowanego upewnij się, że jest używany najwyższy poziom silnego uwierzytelniania.

- [Jak włączyć uwierzytelnianie wieloskładnikowe na platformie Azure](../../active-directory/authentication/howto-mfa-getstarted.md) 

- [Wprowadzenie do opcji uwierzytelniania bez hasła dla usługi Azure Active Directory](../../active-directory/authentication/concept-authentication-passwordless.md)

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="im-5-monitor-and-alert-on-account-anomalies"></a>IM-5: Monitorowanie anomalii kont i wyświetlanie alertów

**Wskazówki**: Azure Managed Applications jest zintegrowany z Azure Active Directory, w którym znajdują się następujące źródła danych:
- Logowania — raporty aktywności logowania zawierają informacje na temat użycia zarządzanych aplikacji i działań użytkownika związane z logowaniem.
- Dzienniki inspekcji — udostępnia możliwość śledzenia wszystkich zmian wprowadzanych przez różne funkcje usługi Azure AD za pomocą dzienników. Działania umieszczane w dziennikach inspekcji to na przykład zmiany wprowadzone w dowolnych zasobach usługi Azure AD, takie jak dodawanie lub usuwanie użytkowników, aplikacji, grup, ról i zasad.
- Ryzykowne logowania — ryzykowne logowanie jest wskaźnikiem próby logowania, które mogło zostać wykonane przez osobę, która nie jest prawowitym właścicielem konta użytkownika.
- Użytkownicy oflagowani w związku z ryzykiem — ryzykowny użytkownik jest wskaźnikiem konta użytkownika, którego bezpieczeństwo mogło zostać naruszone.

Te źródła danych można zintegrować z Azure Monitor, wskaźnikiem produktów platformy Azure lub systemami SIEM innych firm.

Usługa Azure Security Center może również wysyłać alerty dotyczące określonych podejrzanych działań, takich jak nadmierna liczba nieudanych prób uwierzytelnienia, czy przestarzałe konta w subskrypcji.

Usługa Azure Advanced Threat Protection (ATP) to rozwiązanie z zakresu zabezpieczeń, które może używać sygnałów usługi Active Directory do identyfikowania, wykrywania i badania zaawansowanych zagrożeń, naruszonych tożsamości i złośliwych działań wewnętrznych.

- [Raporty dotyczące inspekcji w usłudze Azure Active Directory](../../active-directory/reports-monitoring/concept-audit-logs.md)

- [Jak wyświetlić ryzykowne logowania w usłudze Azure AD](../../active-directory/identity-protection/overview-identity-protection.md)

- [Jak monitorować działania użytkowników związane z tożsamościami i dostępem w usłudze Azure Security Center](../../security-center/security-center-identity-access.md)

- [Jak zintegrować dzienniki aktywności platformy Azure z usługą Azure Monitor](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM-6: Ograniczanie dostępu do zasobów platformy Azure na podstawie warunków

**Wskazówki**: Azure Managed Applications obsługuje dostęp warunkowy do usługi Azure AD w celu uzyskania bardziej szczegółowej kontroli dostępu na podstawie warunków zdefiniowanych przez użytkownika, takich jak logowania użytkowników z określonych zakresów adresów IP, będą musieli używać uwierzytelniania wieloskładnikowego na potrzeby logowania. Szczegółowe zasady zarządzania sesjami uwierzytelniania mogą być również używane dla różnych przypadków użycia.

- [Omówienie dostępu warunkowego platformy Azure](../../active-directory/conditional-access/overview.md) 

- [Typowe zasady dostępu warunkowego](../../active-directory/conditional-access/concept-conditional-access-policy-common.md) 

- [Konfigurowanie zarządzania sesją uwierzytelniania z użyciem dostępu warunkowego](../../active-directory/conditional-access/howto-conditional-access-session-lifetime.md)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

## <a name="privileged-access"></a>Dostęp uprzywilejowany

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: dostęp uprzywilejowany](../../security/benchmarks/security-controls-v2-privileged-access.md).*

### <a name="pa-1-protect-and-limit-highly-privileged-users"></a>PA-1: Ochrona i ograniczanie użytkowników z wysokim poziomem uprawnień

**Wskazówki**: Azure Managed Applications używa Azure Active Directory (Azure AD) w celu uzyskania tożsamości i dostępu. Najważniejsze role wbudowane to usługa Azure AD — Administrator globalny i administrator ról uprzywilejowanych jako użytkownicy przypisani do tych dwóch ról mogą delegować role administratorów:
- Administrator globalny: Użytkownicy z tą rolą mają dostęp do wszystkich funkcji administracyjnych w usłudze Azure AD, a także usług korzystających z tożsamości usługi Azure AD.
- Administrator ról uprzywilejowanych: Użytkownicy z tą rolą mogą zarządzać przypisaniami ról w usłudze Azure AD, a także w Azure AD Privileged Identity Management (PIM). Ponadto ta rola umożliwia zarządzanie wszystkimi aspektami jednostek PIM i administracyjnych.

Uwaga: mogą istnieć inne role krytyczne, które należy zastosować w przypadku używania ról niestandardowych z określonymi przypisanymi uprawnieniami. Można również zastosować podobne kontrolki do konta administratora krytycznych zasobów firmy.

Można włączyć uprzywilejowany dostęp just-in-time (JIT) do zasobów platformy Azure i usługi Azure AD przy użyciu usługi Azure AD Privileged Identity Management (PIM). Dostęp JIT polega na przyznawaniu uprawnień tymczasowych do wykonywania zadań uprzywilejowanych tylko wtedy, gdy użytkownicy ich potrzebują. Usługa PIM może również generować alerty zabezpieczeń w przypadku podejrzanych lub niebezpiecznych działań w ramach organizacji usługi Azure AD.

- [Uprawnienia ról administratora w usłudze Azure AD](../../active-directory/roles/permissions-reference.md)

- [Używanie alertów zabezpieczeń usługi Azure Privileged Identity Management](../../active-directory/privileged-identity-management/pim-how-to-configure-security-alerts.md)

- [Zabezpieczanie uprzywilejowanego dostępu dla wdrożeń hybrydowych i wdrożeń w chmurze w usłudze Azure AD](../../active-directory/roles/security-planning.md)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2: Ogranicz dostęp administracyjny do systemów o krytycznym znaczeniu dla firmy

**Wskazówki**: Azure Managed Applications korzysta z usługi Azure RBAC, aby odizolować dostęp do systemów o krytycznym znaczeniu dla firmy, ograniczając konta, które mają przyznane uprzywilejowany dostęp do subskrypcji i grup zarządzania, do których się znajdują.

Upewnij się, że ograniczono również dostęp do systemów zarządzania, tożsamości i zabezpieczeń, które mają dostęp administracyjny do krytycznych dostępów firmy, takich jak kontrolery domena usługi Active Directory (DC), narzędzia zabezpieczeń i narzędzia do zarządzania systemem, z agentami zainstalowanymi w systemach krytycznych dla działalności firmy. Osoby atakujące, którzy naruszają te systemy zarządzania i zabezpieczeń, mogą natychmiast weaponize je w celu naruszenia krytycznych zasobów firmy.

Wszystkie typy kontroli dostępu powinny być wyrównane do strategii segmentacji przedsiębiorstwa w celu zapewnienia spójnej kontroli dostępu.

- [Składniki platformy Azure i Model referencyjny](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Dostęp do grupy zarządzania](../../governance/management-groups/overview.md#management-group-access)

- [Administratorzy subskrypcji platformy Azure](../../cost-management-billing/manage/add-change-subscription-administrator.md)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="pa-3-review-and-reconcile-user-access-regularly"></a>PA-3: Regularne przeglądanie i uzgadnianie dostępu użytkowników

**Wskazówki**: regularnie Przeglądaj konta użytkowników i przypisywanie dostępu, aby upewnić się, że konta i ich poziom dostępu są prawidłowe.

Azure Managed Applications używa kont usługi Azure Active Directory (AAD) do zarządzania swoimi zasobami, przeglądania kont użytkowników i przypisywania dostępu regularnie, aby zapewnić, że konta i ich dostęp są prawidłowe. Za pomocą przeglądów dostępu w usłudze Azure AD można przeglądać członkostwa w grupach, uzyskiwać dostęp do aplikacji dla przedsiębiorstw i przypisań ról. Raportowanie usługi Azure AD może zapewnić dzienniki, aby ułatwić odnajdywanie starych kont. Możesz również użyć Azure AD Privileged Identity Management, aby utworzyć przepływ pracy raportu przeglądu dostępu, aby ułatwić proces recenzowania.

Ponadto usługa Azure Privileged Identity Management może być również skonfigurowana do zgłaszania alertów w przypadku tworzenia zbyt dużej liczby kont administratorów oraz do identyfikowania kont administratorów, które są przestarzałe lub nieprawidłowo skonfigurowane.

Uwaga: Niektóre usługi platformy Azure obsługują lokalnych użytkowników i role, które nie są zarządzane za pomocą usługi Azure AD. Trzeba będzie osobno zarządzać tymi użytkownikami.

- [Tworzenie przeglądu dostępu do ról zasobów platformy Azure w Privileged Identity Management (PIM)](../../active-directory/privileged-identity-management/pim-resource-roles-start-access-review.md) 

- [Jak korzystać z przeglądów tożsamości i dostępu w usłudze Azure AD](../../active-directory/governance/access-reviews-overview.md)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="pa-4-set-up-emergency-access-in-azure-ad"></a>PA-4: Konfigurowanie dostępu awaryjnego w usłudze Azure AD

**Wskazówki**: Azure Managed Applications używa Azure Active Directory do zarządzania zasobami. Aby zapobiec przypadkowemu zablokowaniu organizacji usługi Azure AD, skonfiguruj konto dostępu awaryjnego w celu uzyskania dostępu, gdy nie można używać normalnych kont administracyjnych. Konta dostępu awaryjnego są zwykle wysoce uprzywilejowane i nie powinny być przypisane do konkretnych osób. Konta dostępu awaryjnego są ograniczone do sytuacji awaryjnych lub nagłych, w których normalne konta administracyjne nie mogą być używane.

Należy upewnić się, że poświadczenia (takie jak hasło, certyfikat lub karta inteligentna) dla kont dostępu awaryjnego są bezpieczne i znane tylko osobom, które są upoważnione do ich używania tylko w sytuacji awaryjnej.

- [Zarządzanie kontami dostępu awaryjnego w usłudze Azure AD](../../active-directory/roles/security-emergency-access.md)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="pa-5-automate-entitlement-management"></a>PA-5: Automatyzowanie zarządzania uprawnieniami 

**Wskazówki**: Azure Managed Applications jest zintegrowany z Azure Active Directory do zarządzania zasobami. Użyj funkcji zarządzania prawami usługi Azure AD, aby zautomatyzować przepływy pracy żądania dostępu, w tym przydziały dostępu, przeglądy i wygaśnięcie. Obsługiwane jest również podwójne lub wieloetapowe zatwierdzenie.

- [Co to są przeglądy dostępu do usługi Azure AD](../../active-directory/governance/access-reviews-overview.md)

- [Co to jest zarządzanie prawami w usłudze Azure AD](../../active-directory/governance/entitlement-management-overview.md)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="pa-6-use-privileged-access-workstations"></a>PA-6: Korzystanie ze stacji roboczych z dostępem uprzywilejowanym

**Wskazówka**: Zabezpieczone, izolowane stacje robocze mają kluczowe znaczenie dla bezpieczeństwa poufnych ról, takich jak administratorzy, deweloperzy i operatorzy usług o kluczowym znaczeniu. Używaj wysoce bezpiecznych stacji roboczych użytkowników i/lub Azure bastionu na potrzeby zadań administracyjnych związanych z zarządzanymi aplikacjami. Użyj usługi Azure Active Directory, zaawansowanej ochrony przed zagrożeniami w usłudze Microsoft Defender i/lub usługi Microsoft Intune do wdrożenia bezpiecznej i zarządzanej stacji roboczej użytkownika na potrzeby zadań administracyjnych. Zabezpieczone stacje robocze mogą być zarządzane centralnie, aby wymusić bezpieczną konfigurację, w tym silne uwierzytelnianie, oprogramowanie i sprzętowe punkty odniesienia, ograniczony dostęp logiczny i sieciowy.

- [Informacje na temat stacji roboczych uprzywilejowanego dostępu](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Wdrażanie stacji roboczej z dostępem uprzywilejowanym](/security/compass/privileged-access-deployment)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="pa-7-follow-just-enough-administration-least-privilege-principle"></a>DU-7: Przestrzeganie podejścia wystarczającego zakresu administracji (zasada stosowania najniższych uprawnień) 

**Wskazówki**: Azure Managed Applications jest zintegrowany z kontrolą dostępu opartą na ROLACH (RBAC) na platformie Azure w celu zarządzania swoimi zasobami. Usługa Azure RBAC umożliwia zarządzanie dostępem do zasobów platformy Azure za pomocą przypisań ról. Te role można przypisać do użytkowników, grup jednostek usługi i zarządzanych tożsamości. Dla niektórych zasobów istnieją wstępnie zdefiniowane wbudowane role i te role można zinwentaryzować lub wykonywać względem nich zapytania za pomocą narzędzi takich jak interfejs wiersza polecenia platformy Azure, program Azure PowerShell lub witryna Azure Portal. Uprawnienia przypisywane do zasobów z użyciem kontroli dostępu opartej na rolach platformy Azure powinny być zawsze ograniczone do tego, co jest wymagane przez te role. To uzupełnia podejście dostępu just in time (JIT) usługi Azure AD Privileged Identity Management (PIM) i powinno być okresowo weryfikowane.

Jeśli to możliwe, Użyj wbudowanych ról, aby przydzielić uprawnienia i utworzyć rolę niestandardową tylko wtedy, gdy jest to wymagane.

Platforma Azure udostępnia następujące wbudowane role platformy Azure umożliwiające autoryzowanie dostępu do aplikacji zarządzanych za pomocą usługi Azure AD i uwierzytelniania OAuth:

- Rola współautor aplikacji zarządzanej: umożliwia tworzenie zasobów aplikacji zarządzanej.

- Rola operatora aplikacji zarządzanej: umożliwia odczytywanie i wykonywanie akcji dotyczących zasobów aplikacji zarządzanej

- Czytnik aplikacji zarządzanych: umożliwia odczytywanie zasobów w zarządzanej aplikacji i żądanie dostępu JIT.

Aby uzyskać więcej informacji, zapoznaj się z następującymi dokumentami:

- [Rola współautora aplikacji zarządzanej](../../role-based-access-control/built-in-roles.md#managed-application-contributor-role)

- [Rola operatora aplikacji zarządzanej](../../role-based-access-control/built-in-roles.md#managed-application-contributor-role)

- [Czytnik aplikacji zarządzanych](../../role-based-access-control/built-in-roles.md#managed-applications-reader)

- [Co to jest kontrola dostępu oparta na rolach (Azure RBAC)](../../role-based-access-control/overview.md)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="pa-8-choose-approval-process-for-microsoft-support"></a>PA-8: wybór procesu zatwierdzania dla pomocy technicznej firmy Microsoft  

**Wskazówki**: implementowanie procesu zatwierdzania w organizacji dla scenariuszy pomocy technicznej, w których firma Microsoft może potrzebować dostępu do danych aplikacji zarządzanych przez platformę Azure. Skrytka klienta nie jest obecnie dostępna dla scenariuszy aplikacji zarządzanych.

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

## <a name="data-protection"></a>Ochrona danych

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: ochrona danych](../../security/benchmarks/security-controls-v2-data-protection.md).*

### <a name="dp-2-protect-sensitive-data"></a>DP-2: Ochrona poufnych danych

**Wskazówki**: w celu przeprowadzenia szyfrowania przy użyciu własnych kluczy możesz użyć własnego konta magazynu do przechowywania plików konfiguracji aplikacji zarządzanych.

- [Ochrona danych zarządzanych plików konfiguracji przy użyciu własnego magazynu](./publish-service-catalog-app.md?tabs=azure-powershell#bring-your-own-storage-for-the-managed-application-definition)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="dp-5-encrypt-sensitive-data-at-rest"></a>DP-5: Szyfrowanie poufnych danych nieużywanych

**Wskazówki**: zarządzane definicje aplikacji definiujące aplikację i jej zasoby mogą być przechowywane na własnych kontach magazynu, które można skonfigurować za pomocą kluczy szyfrowania zarządzanych przez klienta.

W przypadku scenariuszy, w których nie chcesz przenosić własnych magazynów dla definicji aplikacji zarządzanych, platforma Azure domyślnie udostępnia dane z szyfrowaniem Rest.

- [Przenoszenie własnych magazynów dla definicji aplikacji zarządzanych](./publish-service-catalog-app.md?tabs=azure-powershell#bring-your-own-storage-for-the-managed-application-definition)

- [Informacje o szyfrowaniu danych magazynowanych na platformie Azure](../../security/fundamentals/encryption-atrest.md#encryption-at-rest-in-microsoft-cloud-services) 

- [Jak skonfigurować zarządzane przez klienta klucze szyfrowania](../../storage/common/customer-managed-keys-configure-key-vault.md)

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Współużytkowane

## <a name="asset-management"></a>Zarządzanie zasobami

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: zarządzanie elementami zawartości](../../security/benchmarks/security-controls-v2-asset-management.md).*

### <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>ZZ-1: Upewnij się, że zespół ds. zabezpieczeń ma wgląd w ryzyko związane z zasobami

**Wskazówki**: Upewnij się, że zespoły ds. zabezpieczeń mają przyznane uprawnienia czytelnika zabezpieczeń w dzierżawie i subskrypcjach platformy Azure, aby umożliwić im monitorowanie zagrożeń bezpieczeństwa przy użyciu usługi Azure Security Center. 

W zależności od istniejącej struktury obowiązków zespołu ds. zabezpieczeń, monitorowanie zagrożeń bezpieczeństwa może być obowiązkiem centralnego zespołu ds. zabezpieczeń lub zespołu lokalnego. Niemniej jednak informacje na temat zabezpieczeń i ryzyka muszą być zawsze agregowane centralnie w ramach danej organizacji. 

Uprawnienia czytelnika zabezpieczeń mogą być stosowane szeroko do całej dzierżawy (główna grupa zarządzania) lub do zakresu w postaci grup zarządzania lub określonych subskrypcji. 

Uwaga: Do uzyskania wglądu w obciążenia i usługi mogą być wymagane dodatkowe uprawnienia. 

- [Omówienie roli czytelnika zabezpieczeń](../../role-based-access-control/built-in-roles.md#security-reader)

- [Omówienie grup zarządzania platformy Azure](../../governance/management-groups/overview.md)

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="am-2-ensure-security-team-has-access-to-asset-inventory-and-metadata"></a>AM-2: Upewnij się, że zespół ds. zabezpieczeń ma dostęp do spisu elementów zawartości i metadanych

**Wskazówki**: Zastosuj znaczniki do zasobów platformy Azure, grup zasobów i subskrypcji, aby logicznie zorganizować je w taksonomię. Każdy tag składa się z nazwy i pary wartości. Na przykład można zastosować nazwę „Środowisko” i wartość „Produkcyjne” do wszystkich zasobów w środowisku produkcyjnym.

Tagi, które są udostępniane podczas tworzenia zarządzanej aplikacji, są również stosowane do zarządzanej grupy zasobów. Wydawca aplikacji może zapewnić własne dodatkowe oznakowanie zarządzanych zasobów po wdrożeniu.

- [Tagi według elementu interfejsu użytkownika Application zarządzanego przez zasób](microsoft-common-tagsbyresource.md)

- [Jak tworzyć zapytania za pomocą eksploratora usługi Azure Resource Graph](../../governance/resource-graph/first-query-portal.md) 

- [Aby uzyskać więcej informacji na temat tagowania elementów zawartości, zobacz Przewodnik po nazwie i znakowaniu zasobów](/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=%2fazure%2fazure-resource-manager%2fmanagement%2ftoc.json)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="am-3-use-only-approved-azure-services"></a>AM-3: Używanie tylko zatwierdzonych usług platformy Azure

**Wskazówki**: Azure Managed Applications obsługuje wdrożenia oparte na Azure Resource Manager i wymuszanie konfiguracji przy użyciu Azure Policy. Usługa Azure Policy pozwala przeprowadzić inspekcję i ograniczyć liczbę usług, które użytkownicy mogą aprowizować w danym środowisku. Usługa Azure Resource Graph umożliwia wykonywanie zapytań dotyczących zasobów i odnajdywanie ich w ramach subskrypcji. Za pomocą usługi Azure Monitor można tworzyć reguły wyzwalające alerty w przypadku wykrycia niezatwierdzonej usługi.

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../../governance/policy/tutorials/create-and-manage.md)

- [Jak odmówić określonego typu zasobu za pomocą Azure Policy](../../governance/policy/samples/built-in-policies.md#general)

- [Jak tworzyć zapytania za pomocą eksploratora usługi Azure Resource Graph](../../governance/resource-graph/first-query-portal.md)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="am-4-ensure-security-of-asset-lifecycle-management"></a>ZZ-4: zapewnienie bezpieczeństwa zarządzania cyklem życia zasobów

**Wskazówki**: zarządzane zasoby aplikacji i połączonej z nimi grupę zasobów można usunąć, usuwając zasób aplikacji zarządzanej. Usunięcie zasobu aplikacji zarządzanej spowoduje również usunięcie zarządzanej grupy zasobów i jej zawartości. Dodatkowe możliwości cyklu życia są określane przez wydawcę aplikacji, gdzie mogą dać konsumentom dodatkowe prawa w stosunku do cyklu życia zasobów zarządzanych za pomocą dozwolonych akcji. Skontaktuj się z wydawcą aplikacji zarządzanej, aby zarządzać zasobami, które są zarządzane przez klienta.

- [Czyszczenie zasobów aplikacji zarządzanej](./tutorial-create-managed-app-with-custom-provider.md?tabs=azurecli-interactive#clean-up-resources)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>AM-5: ograniczanie możliwości korzystania przez użytkowników z Azure Resource Manager

**Wskazówki**: Użyj dostępu warunkowego platformy Azure, aby ograniczyć możliwość korzystania przez użytkowników z Menedżera zasobów platformy Azure przez skonfigurowanie "blokowania dostępu" dla aplikacji "Microsoft Azure Management".

- [Jak skonfigurować dostęp warunkowy, aby blokować dostęp do usługi Azure Resources](../../role-based-access-control/conditional-access-azure-management.md)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

## <a name="logging-and-threat-detection"></a>Rejestrowanie i wykrywanie zagrożeń

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: rejestrowanie i wykrywanie zagrożeń](../../security/benchmarks/security-controls-v2-logging-threat-detection.md).*

### <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>LT-2: Włącz wykrywanie zagrożeń na potrzeby zarządzania tożsamościami i dostępem na platformie Azure

**Wskazówki**: usługa Azure AD udostępnia następujące dzienniki użytkowników, które mogą być wyświetlane w usłudze Azure AD Reporting lub zintegrowane z Azure Monitorm, wskaźnikiem "platformy Azure" lub innymi narzędziami do Siem/monitorowania, aby uzyskać bardziej zaawansowane przypadki monitorowania i analizy: logowania — raport logowania zawiera informacje na temat użycia zarządzanych aplikacji i działań związanych z logowaniem użytkowników.
Dzienniki inspekcji — udostępnia możliwość śledzenia wszystkich zmian wprowadzanych przez różne funkcje usługi Azure AD za pomocą dzienników. Działania umieszczane w dziennikach inspekcji to na przykład zmiany wprowadzone w dowolnych zasobach usługi Azure AD, takie jak dodawanie lub usuwanie użytkowników, aplikacji, grup, ról i zasad.
Ryzykowne logowania — ryzykowne logowanie jest wskaźnikiem próby logowania, które mogło zostać wykonane przez osobę, która nie jest prawowitym właścicielem konta użytkownika.
Użytkownicy oflagowani w związku z ryzykiem — ryzykowny użytkownik jest wskaźnikiem konta użytkownika, którego bezpieczeństwo mogło zostać naruszone.

Usługa Azure Security Center może również wysyłać alerty dotyczące określonych podejrzanych działań, takich jak nadmierna liczba nieudanych prób uwierzytelnienia, czy przestarzałe konta w subskrypcji. Oprócz podstawowego monitorowania higieny zabezpieczeń moduł ochrony przed zagrożeniami usługi Azure Security Center może również zbierać bardziej szczegółowe alerty zabezpieczeń z poszczególnych zasobów obliczeniowych platformy Azure (maszyn wirtualnych, kontenerów, usługi App Service), zasobów danych (bazy danych SQL i magazynu) oraz warstw usługi platformy Azure. Ta funkcja zapewnia wgląd w anomalie kont wewnątrz poszczególnych zasobów.

- [Raporty dotyczące inspekcji w usłudze Azure Active Directory](../../active-directory/reports-monitoring/concept-audit-logs.md)

- [Włączanie usługi Azure Identity Protection](../../active-directory/identity-protection/overview-identity-protection.md)

- [Ochrona przed zagrożeniami w usłudze Azure Security Center](../../security-center/azure-defender.md)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4: Włącz rejestrowanie zasobów platformy Azure

**Wskazówki**: dzienniki aktywności, które są automatycznie dostępne, zawierają wszystkie operacje zapisu (Put, post, Delete) dla zasobów aplikacji zarządzanej z wyjątkiem operacji odczytu (Get). Dzienników aktywności można użyć do znalezienia błędu podczas rozwiązywania problemów lub do monitorowania sposobu, w jaki użytkownik w organizacji zmodyfikował zasób.

- [Jak zbierać dzienniki platformy i metryki za pomocą Azure Monitor](../../azure-monitor/essentials/diagnostic-settings.md) 

- [Informacje o rejestrowaniu i różnych typach dzienników na platformie Azure](../../azure-monitor/essentials/platform-logs-overview.md)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5: Scentralizowane zarządzanie dziennikami zabezpieczeń i analiza

**Wskazówki**: scentralizowanie rejestrowania, magazynu i analizy w celu włączenia korelacji. Dla każdego źródła dziennika upewnij się, że masz przypisanego właściciela danych, wskazówki dotyczące dostępu, lokalizację magazynu, narzędzia używane do przetwarzania i uzyskiwania dostępu do danych oraz wymagania dotyczące przechowywania danych.
Upewnij się, że masz integrację dzienników aktywności platformy Azure z centralnym logowaniem. Pozyskiwanie dzienników za pośrednictwem Azure Monitor do agregowania danych zabezpieczeń wygenerowanych przez urządzenia, zasoby sieciowe i inne systemy zabezpieczeń. W Azure Monitor używać Log Analytics obszarów roboczych do wykonywania zapytań i przeprowadzania analiz oraz używania kont usługi Azure Storage do przechowywania długoterminowego i archiwizowania.

Ponadto należy włączyć i dołączyć dane do usługi Azure wskaźnikowej lub SIEM innych firm.

Wiele organizacji decyduje się na korzystanie z platformy Azure na potrzeby danych "gorąca", które są często używane, i usługi Azure Storage dla danych "zimnych", które są rzadziej używane.

- [Jak zbierać dzienniki platformy i metryki za pomocą Azure Monitor](../../azure-monitor/essentials/diagnostic-settings.md)

- [Jak dołączyć wskaźnik na platformie Azure](../../sentinel/quickstart-onboard.md)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="lt-6-configure-log-storage-retention"></a>LT-6: Konfigurowanie przechowywania magazynu dzienników

**Wskazówki**: Upewnij się, że wszystkie konta magazynów lub log Analytics obszary robocze używane do przechowywania dzienników utworzonych przez zasoby aplikacji zarządzanej mają ustawiony okres przechowywania dziennika zgodnie z przepisami obowiązującymi w organizacji.
W Azure Monitor można ustawić okres przechowywania Log Analytics obszaru roboczego zgodnie z regulacjami zgodności w organizacji. Użyj usługi Azure Storage Data Lake lub Log Analytics konta obszaru roboczego do przechowywania długoterminowego i archiwizowania.

- [Jak skonfigurować okres przechowywania obszaru roboczego Log Analytics](../../azure-monitor/logs/manage-cost-storage.md)

- [Przechowywanie dzienników zasobów na koncie usługi Azure Storage](../../azure-monitor/essentials/resource-logs.md#send-to-azure-storage)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

## <a name="incident-response"></a>Reagowanie na zdarzenia

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: reagowanie na zdarzenia](../../security/benchmarks/security-controls-v2-incident-response.md).*

### <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1: Przygotowanie — aktualizowanie procesu reagowania na zdarzenia na platformie Azure

**Wskazówka**: Upewnij się, że organizacja ma procesy reagowania na zdarzenia związane z bezpieczeństwem, zaktualizował te procesy na platformie Azure i regularnie wykonuje je w celu zapewnienia gotowości.

- [Implementowanie zabezpieczeń w środowisku przedsiębiorstwa](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Przewodnik po odpowiedziach na zdarzenia](/microsoft-365/downloads/IR-Reference-Guide.pdf)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="ir-2-preparation--setup-incident-notification"></a>IR-2: Przygotowanie — powiadomienie o zdarzeniu konfiguracji

**Wskazówka**: Skonfiguruj informacje kontaktowe dotyczące zdarzenia zabezpieczeń w usłudze Azure Security Center. Informacje kontaktowe są używane przez firmę Microsoft do skontaktowania się z Tobą, jeśli centrum Microsoft Security Response Center (MSRC) wykryje, że dostęp do danych jest uzyskiwany przez nieuprawnioną lub nieautoryzowaną osobę. Dostępne są również opcje dostosowywania alertów dotyczących zdarzeń i powiadomień w różnych usługach platformy Azure w zależności od potrzeb związanych z odpowiedzią na zdarzenia. 

- [Jak ustawić kontakt ds. zabezpieczeń usługi Azure Security Center](../../security-center/security-center-provide-security-contact-details.md)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3: Wykrywanie i analiza — tworzenie zdarzeń na podstawie alertów o wysokiej jakości

**Wskazówka**: Upewnij się, że masz proces tworzenia alertów o wysokiej jakości i mierzenia jakości alertów. Dzięki temu można wyciągać wnioski z przeszłych zdarzeń i określić priorytety alertów dla analityków, dzięki czemu nie będą oni tracić czasu na wyniki fałszywie dodatnie. 

Alerty o wysokiej jakości można tworzyć na podstawie doświadczeń z przeszłych zdarzeń, sprawdzonych źródeł społeczności oraz narzędzi przeznaczonych do generowania i czyszczenia alertów przez połączenie i skorelowanie różnorodnych źródeł sygnałów. 

Azure Security Center oferuje alerty wysokiej jakości dla wielu zasobów platformy Azure. Można użyć łącznika danych usługi ASC do przesyłania strumieniowego alertów do usługi Azure Sentinel. Usługa Azure Sentinel umożliwia tworzenie zaawansowanych reguł alertów w celu automatycznego generowania zdarzeń na potrzeby badania. 

Eksportuj alerty i zalecenia usługi Azure Security Center przy użyciu funkcji eksportowania, aby pomóc identyfikować zagrożenia dla zasobów platformy Azure. Eksportuj alerty i zalecenia ręcznie lub w stały, ciągły sposób.

- [Jak skonfigurować eksportowanie](../../security-center/continuous-export.md)

- [Jak przesłać strumieniowo alerty do usługi Azure Sentinel](../../sentinel/connect-azure-security-center.md)

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

- [Tworzenie migawek dysku maszyny z systemem Windows](../../virtual-machines/windows/snapshot-copy-managed-disk.md)

- [Tworzenie migawek dysku maszyny z systemem Linux](../../virtual-machines/linux/snapshot-copy-managed-disk.md)

- [Informacje diagnostyczne pomocy technicznej oraz kolekcja zrzutów pamięci platformy Microsoft Azure](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/) 

- [Badanie zdarzeń za pomocą usługi Azure Sentinel](../../sentinel/tutorial-investigate-cases.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>IR-5: Wykrywanie i analiza — określanie priorytetów zdarzeń

**Wskazówki**: Zapewnij kontekst analitykom, na których zdarzeniach należy skupić się w pierwszej kolejności w oparciu o poziom ważności alertów i poufność aktywów. 

Usługa Azure Security Center przypisuje poziom ważności do każdego alertu, aby pomóc w ustaleniu, które alerty powinny być zbadane w pierwszej kolejności. Ważność jest oparta na tym, jak dobrze Security Center znajduje się w wyszukiwaniu lub analizie używanych do wystawiania alertu, a także poziom pewności, że istniało złośliwe zamiar w odniesieniu do działania, które prowadziło do alertu.

Ponadto oznacz zasoby przy użyciu tagów i utwórz system nazewnictwa, aby zidentyfikować i sklasyfikować zasoby platformy Azure, szczególnie te, które przetwarzają dane poufne. Odpowiedzialność za korygowanie alertów w oparciu o krytyczne znaczenie zasobów platformy Azure i środowisko, w którym wystąpiło zdarzenie, leży po stronie użytkownika.

- [Alerty zabezpieczeń w Centrum zabezpieczeń Azure](../../security-center/security-center-alerts-overview.md)

- [Organizowanie zasobów platformy Azure przy użyciu tagów](../management/tag-resources.md)

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="ir-6-containment-eradication-and-recovery--automate-the-incident-handling"></a>IR-6: Zawieranie, eliminowanie i odzyskiwanie — automatyzacja obsługi zdarzeń

**Wskazówka**: Automatyzuj ręczne powtarzające się zadania w celu przyspieszenia czasu reakcji i zmniejszenia obciążenia dla analityków. Wykonanie ręcznych zadań trwa dłużej, spowalnia każde zdarzenie i zmniejsza liczbę zdarzeń, które może obsłużyć analityk. Zadania wykonywane ręcznie zwiększają również zmęczenie analityków, co zwiększa ryzyko wystąpienia błędu ludzkiego, który powoduje opóźnienia, a także pogarsza zdolność analityków do skoncentrowania się na złożonych zadaniach. 

Korzystając z funkcji automatyzacji przepływów pracy w usłudze Azure Security Center i Azure Sentinel, można automatycznie wyzwalać akcje lub uruchamiać element playbook w odpowiedzi na przychodzące alerty zabezpieczeń. Element playbook wykonuje akcje, takie jak wysyłanie powiadomień, wyłączanie kont i izolowanie problematycznych sieci. 

- [Konfigurowanie automatyzacji przepływu pracy w usłudze Security Center](../../security-center/workflow-automation.md)

- [Konfigurowanie automatycznych reakcji na zagrożenia w usłudze Azure Security Center](../../security-center/tutorial-security-incident.md#triage-security-alerts)

- [Konfigurowanie automatycznych reakcji na zagrożenia w usłudze Azure Sentinel](../../sentinel/tutorial-respond-threats-playbook.md)

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

## <a name="posture-and-vulnerability-management"></a>Stan zabezpieczeń i zarządzanie lukami w zabezpieczeniach

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: stan zabezpieczeń i zarządzanie lukami w zabezpieczeniach](../../security/benchmarks/security-controls-v2-posture-vulnerability-management.md).*

### <a name="pv-1-establish-secure-configurations-for-azure-services"></a>PV-1: Ustanów bezpieczne konfiguracje dla usług platformy Azure 

**Wskazówki**: Definiowanie guardrails zabezpieczeń dla zespołów infrastruktury i DevOps, dzięki czemu można łatwo bezpiecznie konfigurować wykorzystywane przez nich usługi platformy Azure.

Skonfiguruj Azure Policy inspekcji i wymuszania konfiguracji zasobów związanych z wdrożeniami aplikacji zarządzanych.

Za pomocą planów platformy Azure można zautomatyzować wdrażanie i konfigurację usług i środowisk aplikacji, w tym Azure Resource Manager szablonów, przypisań RBAC platformy Azure i przydziałów Azure Policy, w ramach jednej definicji planu.

- [Strefa wdrożenia Cloud proframework w skali korporacyjnej](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture#landing-zone-expanded-definition)

- [Tworzenie zasad i zarządzanie nimi w celu wymuszania zgodności](../../governance/policy/tutorials/create-and-manage.md)

- [Azure Blueprints](../../governance/blueprints/overview.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="pv-2-sustain-secure-configurations-for-azure-services"></a>PV-2: Utrzymaj bezpieczne konfiguracje dla usług platformy Azure

**Wskazówki**: Użyj Azure Security Center do monitorowania zasobów związanych z Azure Managed Applications i używania Azure Policy [Odmów] i [Wdróż, jeśli nie istnieje] efektów, aby utrzymać bezpieczne konfiguracje.
- [Zrozumienie efektów Azure Policy](../../governance/policy/concepts/effects.md)

- [Tworzenie zasad i zarządzanie nimi w celu wymuszania zgodności](../../governance/policy/tutorials/create-and-manage.md)

- [Azure Blueprints](../../governance/blueprints/overview.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8: Przeprowadzanie regularnej symulacji ataków

**Wskazówka**: W razie potrzeby przeprowadź test penetracyjny lub działania typu „red team” na zasobach platformy Azure i zapewnij korektę wszystkich krytycznych ustaleń dotyczących zabezpieczeń.
Postępuj zgodnie z regułami testowania penetracji w chmurze firmy Microsoft, aby upewnić się, że testy penetracyjne nie naruszają zasad firmy Microsoft. Korzystaj ze strategii firmy Microsoft i wykonywania testów typu „red team” i testów na żywo w zarządzanej przez firmę Microsoft infrastrukturze, usługach i aplikacjach w chmurze.

- [Testy penetracyjne na platformie Azure](../../security/fundamentals/pen-testing.md)

- [Reguły testów penetracyjnych zaangażowania](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Testy typu „red team” w chmurze firmy Microsoft](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Współużytkowane

## <a name="backup-and-recovery"></a>Kopia zapasowa i odzyskiwanie

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: tworzenie i przywracanie kopii zapasowych](../../security/benchmarks/security-controls-v2-backup-recovery.md).*

### <a name="br-3-validate-all-backups-including-customer-managed-keys"></a>BR-3: Weryfikowanie wszystkich kopii zapasowych, w tym kluczy zarządzanych przez klienta

**Wskazówki**: w przypadku przechowywania definicji aplikacji zarządzanych na własnym koncie magazynu należy się upewnić, że można przywrócić wszystkie skojarzone klucze zarządzane przez klienta, które są używane na potrzeby szyfrowania tego konta, które są przechowywane w Azure Key Vault.

- [Przenoszenie własnych magazynów dla definicji aplikacji zarządzanych](./publish-service-catalog-app.md?tabs=azure-powershell#bring-your-own-storage-for-the-managed-application-definition)

- [Jak przywrócić klucze Key Vault na platformie Azure](/powershell/module/az.keyvault/restore-azkeyvaultkey?amp;preserve-view=true&view=azps-5.1.0)

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="br-4-mitigate-risk-of-lost-keys"></a>BR-4: Zmniejszanie ryzyka utraty kluczy

**Wskazówki**: Jeśli używasz własnego magazynu dla definicji aplikacji zarządzanych, upewnij się, że masz zastosowane środki, aby zapobiec utracie kluczy używanych do szyfrowania definicji i odzyskiwać je. Włącz trwałe usuwanie i przeczyszczanie ochrony na Azure Key Vault, w którym są przechowywane klucze zarządzane przez klienta w celu ochrony kluczy przed przypadkowym lub złośliwym usunięciem.  

- [Przenoszenie własnych magazynów dla definicji aplikacji zarządzanych](./publish-service-catalog-app.md?tabs=azure-powershell#bring-your-own-storage-for-the-managed-application-definition)

- [Jak włączyć ochronę w formie usuwania nietrwałego i przeczyszczania w usłudze Key Vault](../../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

## <a name="governance-and-strategy"></a>Ład i strategia

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: ład i strategia](../../security/benchmarks/security-controls-v2-governance-strategy.md).*

### <a name="gs-1-define-asset-management-and-data-protection-strategy"></a>GS-1: Definiowanie strategii zarządzania elementami zawartości i ochrony danych 

**Wskazówka**: Zadbaj o to, aby udokumentować i udostępnić wyraźną strategię monitorowania i ochrony systemów i danych. Ustalaj priorytety odnajdywania, oceny, ochrony i monitorowania danych oraz systemów o krytycznym znaczeniu dla firmy. 

Ta strategia powinna obejmować udokumentowane wskazówki, zasady i standardy dla następujących elementów: 

-   Standard klasyfikacji danych zgodny z ryzykiem biznesowym

-   Wgląd organizacji zabezpieczeń w czynniki ryzyka i spis elementów zawartości 

-   Zatwierdzenie przez organizację zabezpieczeń usług platformy Azure do użycia 

-   Bezpieczeństwo elementów zawartości w całym cyklu życia

-   Wymagana strategia kontroli dostępu zgodnie z klasyfikacją danych organizacji

-   Korzystanie z natywnych i zewnętrznych funkcji ochrony danych platformy Azure

-   Wymagania dotyczące szyfrowania danych dla przypadków użycia w trakcie przesyłania i w czasie spoczynku

-   Odpowiednie standardy kryptograficzne

Aby uzyskać więcej informacji, zapoznaj się z następującymi dokumentami:
- [Zalecenie dotyczące architektury zabezpieczeń platformy Azure — magazyn, dane i szyfrowanie](/azure/architecture/framework/security/storage-data-encryption?amp;bc=%2fsecurity%2fcompass%2fbreadcrumb%2ftoc.json&toc=%2fsecurity%2fcompass%2ftoc.json)

- [Podstawy zabezpieczeń platformy Azure — zabezpieczenia, szyfrowanie i magazynowanie danych platformy Azure](../../security/fundamentals/encryption-overview.md)

- [Cloud Adoption Framework — najlepsze rozwiązania z zakresu zabezpieczeń i szyfrowania danych platformy Azure](../../security/fundamentals/data-encryption-best-practices.md?amp;bc=%2fazure%2fcloud-adoption-framework%2f_bread%2ftoc.json&toc=%2fazure%2fcloud-adoption-framework%2ftoc.json)

- [Test porównawczy zabezpieczeń platformy Azure — zarządzanie elementami zawartości](../../security/benchmarks/security-controls-v2-asset-management.md)

- [Test porównawczy zabezpieczeń platformy Azure — ochrona danych](../../security/benchmarks/security-controls-v2-data-protection.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="gs-2-define-enterprise-segmentation-strategy"></a>GS-2: Definiowanie strategii segmentacji przedsiębiorstwa 

**Wskazówka**: Ustanów strategię całego przedsiębiorstwa w zakresie segmentacji dostępu do elementów zawartości przy użyciu kombinacji tożsamości, sieci, aplikacji, subskrypcji, grupy zarządzania i innych mechanizmów kontroli.

Należy starannie zrównoważyć potrzebę rozdzielania zabezpieczeń, aby umożliwić codzienne działanie systemów, które muszą komunikować się ze sobą i uzyskiwać dostęp do danych.

Upewnij się, że strategia segmentacji jest zaimplementowana spójnie dla różnych typów kontroli, w tym zabezpieczeń sieci, modeli tożsamości i dostępu, a także modeli uprawnień/dostępu i procesów ludzkich.

- [Wskazówki dotyczące strategii segmentacji na platformie Azure (wideo)](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Wskazówki dotyczące strategii segmentacji na platformie Azure (dokument)](/security/compass/governance#enterprise-segmentation-strategy)

- [Wyrównywanie segmentacji sieci przy użyciu strategii segmentacji przedsiębiorstwa](/security/compass/network-security-containment#align-network-segmentation-with-enterprise-segmentation-strategy)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="gs-3-define-security-posture-management-strategy"></a>GS-3: Definiowanie strategii zarządzania stanem zabezpieczeń

**Wskazówka**: Nieustannie mierz i ograniczaj ryzyko dla poszczególnych elementów zawartości i środowiska, w którym są hostowane. Ustalaj priorytety elementów zawartości o wysokiej wartości i wysoce narażonych na ataki obszarów, takich jak opublikowane aplikacje, punkty danych przychodzących i wychodzących sieci, punkty końcowe użytkowników i administratorów itp.

- [Test porównawczy zabezpieczeń platformy Azure — stan i zarządzanie lukami w zabezpieczeniach](../../security/benchmarks/security-controls-v2-posture-vulnerability-management.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="gs-4-align-organization-roles-responsibilities-and-accountabilities"></a>GS-4: Dopasuj role organizacji, obowiązki i odpowiedzialność

**Wskazówka**: Zadbaj o to, aby udokumentować i udostępnić wyraźną strategię dotyczącą ról i obowiązków w organizacji zabezpieczeń. Ustalaj priorytety w celu wyraźnego określenia odpowiedzialności za decyzje dotyczące zabezpieczeń, informując wszystkie osoby o współużytkowanym modelu odpowiedzialności i informując zespoły techniczne o technologii do zabezpieczania chmury.

- [Najlepsze rozwiązanie dotyczące zabezpieczeń platformy Azure 1 — ludzie: informowanie zespołów o podróży zabezpieczeń w chmurze](/azure/cloud-adoption-framework/security/security-top-10#1-people-educate-teams-about-the-cloud-security-journey)

- [Najlepsze rozwiązanie dotyczące zabezpieczeń platformy Azure 2 — ludzie: informowanie zespołów o technologii zabezpieczeń w chmurze](/azure/cloud-adoption-framework/security/security-top-10#2-people-educate-teams-on-cloud-security-technology)

- [Najlepsze rozwiązanie dotyczące zabezpieczeń platformy Azure 3 — proces: przypisywanie odpowiedzialności za decyzje dotyczące zabezpieczeń chmury](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="gs-5-define-network-security-strategy"></a>GS-5: Definiowanie strategii zabezpieczeń sieci

**Wskazówka**: Ustanów metodę zabezpieczeń sieci platformy Azure jako część ogólnej strategii kontroli dostępu zabezpieczeń w organizacji.  

Ta strategia powinna obejmować udokumentowane wskazówki, zasady i standardy dla następujących elementów: 

-   Scentralizowane zarządzanie siecią i odpowiedzialność w zakresie zabezpieczeń

-   Model segmentacji sieci wirtualnej zgodny ze strategią segmentacji przedsiębiorstwa

-   Strategia korygowania w różnych scenariuszach zagrożeń i ataków

-   Strategia internetowa i dotycząca brzegowego ruchu przychodzącego i wychodzącego

-   Strategia międzyłączności w chmurze hybrydowej i środowisku lokalnym

-   Aktualne artefakty zabezpieczeń sieci (np. diagramy sieciowe, architektura sieci referencyjnej)

Aby uzyskać więcej informacji, zapoznaj się z następującymi dokumentami:
- [Najlepsze rozwiązanie dotyczące zabezpieczeń platformy Azure 11 — architektura. Pojedyncza ujednolicona strategia zabezpieczeń](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Test porównawczy zabezpieczeń platformy Azure — zabezpieczenia sieci](../../security/benchmarks/security-controls-v2-network-security.md)

- [Omówienie zabezpieczeń sieci platformy Azure](../../security/fundamentals/network-overview.md)

- [Strategia architektury sieci przedsiębiorstwa](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="gs-6-define-identity-and-privileged-access-strategy"></a>GS-6: Definiowanie strategii dotyczącej tożsamości i dostępu uprzywilejowanego

**Wskazówka**: Ustanów metody obsługi tożsamości i dostępu uprzywilejowanego na platformie Azure jako część ogólnej strategii kontroli dostępu zabezpieczeń w organizacji.  

Ta strategia powinna obejmować udokumentowane wskazówki, zasady i standardy dla następujących elementów: 

-   Scentralizowany system tożsamości i uwierzytelniania oraz połączenie z innymi wewnętrznymi i zewnętrznymi systemami tożsamości

-   Metody silnego uwierzytelniania w różnych przypadkach użycia i warunkach

-   Ochrona użytkowników z wysokim poziomem uprawnień

-   Monitorowanie i obsługa nietypowych działań użytkowników  

-   Przegląd tożsamości i dostępu użytkownika oraz proces uzgadniania

Aby uzyskać więcej informacji, zapoznaj się z następującymi dokumentami:

- [Test porównawczy zabezpieczeń platformy Azure — zarządzanie tożsamością](../../security/benchmarks/security-controls-v2-identity-management.md)

- [Test porównawczy zabezpieczeń platformy Azure — dostęp uprzywilejowany](../../security/benchmarks/security-controls-v2-privileged-access.md)

- [Najlepsze rozwiązanie dotyczące zabezpieczeń platformy Azure 11 — architektura. Pojedyncza ujednolicona strategia zabezpieczeń](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Omówienie zabezpieczeń zarządzania tożsamościami platformy Azure](../../security/fundamentals/identity-management-overview.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="gs-7-define-logging-and-threat-response-strategy"></a>GS-7: Definiowanie strategii rejestrowania i reagowania na zagrożenia

**Wskazówka**: Ustanów strategię rejestrowania i reagowania na zagrożenia, aby szybko wykrywać i eliminować zagrożenia oraz spełnić wymagania dotyczące zgodności. Ustalaj priorytety, dostarczając analitykom alerty wysokiej jakości i bezproblemowe środowisko, aby umożliwić im skoncentrowanie się na zagrożeniach, a nie na integracji i ręcznych działaniach. 

Ta strategia powinna obejmować udokumentowane wskazówki, zasady i standardy dla następujących elementów: 

-   Rola i obowiązki organizacji w zakresie operacji zabezpieczeń (SecOps) 

-   Dobrze zdefiniowany proces reagowania na zdarzenia zgodnie z NIST lub inną platformą branżową 

-   Przechwytywanie i przechowywanie dzienników w celu zapewnienia obsługi wykrywania zagrożeń, reagowania na zdarzenia i zgodności

-   Scentralizowana widoczność i korelacja informacji dotyczących zagrożeń przy użyciu rozwiązania SIEM, natywnych możliwości platformy Azure i innych źródeł 

-   Plan komunikacji i powiadomień z klientami, dostawcami i publicznymi zainteresowanymi stronami

-   Używanie natywnych i zewnętrznych platform Azure do obsługi zdarzeń, takich jak rejestrowanie i wykrywanie zagrożeń, postępowania dowodowe oraz korygowanie i eliminowanie ataków

-   Procesy obsługi zdarzeń i działań po zdarzeniu, takie jak zdobyte doświadczenia i przechowywanie dowodów

Aby uzyskać więcej informacji, zapoznaj się z następującymi dokumentami:

- [Test porównawczy zabezpieczeń platformy Azure — rejestrowanie i wykrywanie zagrożeń](../../security/benchmarks/security-controls-v2-logging-threat-detection.md)

- [Test porównawczy zabezpieczeń platformy Azure — reagowanie na zdarzenia](../../security/benchmarks/security-controls-v2-incident-response.md)

- [Najlepsze rozwiązanie dotyczące zabezpieczeń platformy Azure 4 — proces. Aktualizowanie procesów reagowania na zdarzenia dla chmury](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Przewodnik dotyczący decyzji w zakresie platformy wdrażania Azure, rejestrowania i raportowania](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

- [Skalowanie, zarządzanie i monitorowanie w przedsiębiorstwie na platformie Azure](/azure/cloud-adoption-framework/ready/enterprise-scale/management-and-monitoring)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

## <a name="next-steps"></a>Następne kroki

- Zobacz [Omówienie testu porównawczego zabezpieczeń platformy Azure w wersji 2](../../security/benchmarks/overview.md)
- Dowiedz się więcej o [punktach odniesienia zabezpieczeń platformy Azure](../../security/benchmarks/security-baselines-overview.md)