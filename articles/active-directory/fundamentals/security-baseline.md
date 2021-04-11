---
title: Podstawa zabezpieczeń platformy Azure dla Azure Active Directory
description: Linia bazowa zabezpieczeń Azure Active Directory zawiera wskazówki i zasoby dotyczące procedur związanych z wdrażaniem zaleceń dotyczących zabezpieczeń określonych w teście zabezpieczeń platformy Azure.
author: msmbaldwin
ms.service: active-directory
ms.topic: conceptual
ms.date: 04/07/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 8d4203b7b5d7742bf198778864fa4f42b7423596
ms.sourcegitcommit: c6a2d9a44a5a2c13abddab932d16c295a7207d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/09/2021
ms.locfileid: "107286019"
---
# <a name="azure-security-baseline-for-azure-active-directory"></a>Podstawa zabezpieczeń platformy Azure dla Azure Active Directory

Ta linia bazowa zabezpieczeń stosuje wskazówki z programu [Azure Security test w wersji 2,0](../../security/benchmarks/overview.md) do Azure Active Directory. Test porównawczy zabezpieczeń platformy Azure zawiera zalecenia dotyczące sposobu zabezpieczania rozwiązań w chmurze na platformie Azure. Zawartość jest pogrupowana według **kontroli zabezpieczeń** zdefiniowanych przez program Azure Security test i powiązane wskazówki dotyczące Azure Active Directory. 

> [!NOTE]
> **Kontrolki** niemające zastosowania do Azure Active Directory lub dla których odpowiedzialności są oferowane przez firmę Microsoft, zostały wykluczone z tej linii bazowej. Aby dowiedzieć się, jak Azure Active Directory całkowicie mapować do testu porównawczego zabezpieczeń platformy Azure, zobacz [pełny Azure Active Directory pliku mapowania linii bazowej zabezpieczeń](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Bezpieczeństwo sieci

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: bezpieczeństwo sieci](/azure/security/benchmarks/security-controls-v2-network-security).*

### <a name="ns-6-simplify-network-security-rules"></a>NS-6: uproszczenie reguł zabezpieczeń sieci

**Wskazówki**: Użyj tagów usługi Azure Virtual Network, aby zdefiniować kontrolę dostępu do sieci dla sieciowych grup zabezpieczeń lub zapory platformy Azure skonfigurowanej dla zasobów Azure Active Directory. Podczas tworzenia reguł zabezpieczeń można użyć tagów usługi zamiast konkretnych adresów IP. Określając nazwę tagu usługi, np. "usługi azureactivedirectory" w odpowiednim polu źródłowym lub docelowym reguły, można zezwolić na ruch dla odpowiedniej usługi lub go odrzucić. Firma Microsoft zarządza prefiksami adresów, które obejmują tag usługi, i automatycznie aktualizuje tag usługi jako adresy. 

- [Zrozumienie i używanie tagów usługi](../../virtual-network/service-tags-overview.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

## <a name="identity-management"></a>Zarządzanie tożsamością

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: zarządzanie tożsamościami](/azure/security/benchmarks/security-controls-v2-identity-management).*

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1: Standaryzacja usługi Azure Active Directory jako centralnego systemu tożsamości i uwierzytelniania

**Wskazówki**: Użyj Azure Active Directory (Azure AD) jako domyślnej usługi zarządzania tożsamościami i dostępem. Należy przeprowadzić standaryzację usługi Azure AD, aby zarządzać zarządzaniem tożsamościami i dostępem w organizacji w programie: 
- Microsoft Cloud zasoby, takie jak Azure Portal, Azure Storage, Azure Virtual Machine (Linux i Windows), Azure Key Vault, PaaS i SaaS. 
- Zasoby organizacji, takie jak aplikacje na platformie Azure lub formowe zasoby sieciowe. 
 

Zabezpieczanie usługi Azure AD powinno mieć wysoki priorytet w zakresie zabezpieczeń w chmurze w organizacji. Usługa Azure AD zapewnia wskaźnik bezpieczeństwa tożsamości, który pomaga ocenić stan zabezpieczeń tożsamości w porównaniu z zaleceniami dotyczącymi najlepszych rozwiązań firmy Microsoft. Skorzystaj ze wskaźnika, aby ocenić, jak ściśle Twoja konfiguracja spełnia zalecenia dotyczące najlepszych rozwiązań, i ulepszać stan zabezpieczeń. 

Usługa Azure AD obsługuje tożsamość zewnętrzną, która umożliwia użytkownikom bez konto Microsoft logowania się do aplikacji i zasobów przy użyciu tożsamości zewnętrznej. 

- [Dzierżawa w usłudze Azure Active Directory](../develop/single-and-multi-tenant-apps.md)

- [Jak utworzyć i skonfigurować wystąpienie usługi Azure AD](active-directory-access-create-new-tenant.md)

- [Używanie zewnętrznych dostawców tożsamości w aplikacji](/azure/active-directory/b2b/identity-providers)

- [Co to jest wskaźnik bezpieczeństwa tożsamości w usłudze Azure Active Directory](identity-secure-score.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="im-2-manage-application-identities-securely-and-automatically"></a>IM-2: Bezpieczne i automatyczne zarządzanie tożsamościami aplikacji

**Wskazówki**: używanie tożsamości zarządzanej dla zasobów platformy Azure dla kont nieludzkich, takich jak usługi lub Automatyzacja, zaleca się korzystanie z funkcji tożsamości zarządzanej przez platformę Azure zamiast tworzenia bardziej zaawansowanego konta ludzkiego w celu uzyskania dostępu do zasobów lub ich wykonania. Można natywnie uwierzytelniać się w usługach/zasobach platformy Azure, które obsługują uwierzytelnianie Azure Active Directory (Azure AD) za pomocą wstępnie zdefiniowanej reguły udzielania dostępu bez korzystania z poświadczeń zakodowanych w kodzie źródłowym lub w plikach konfiguracji. Nie można przypisywać tożsamości zarządzanych przez platformę Azure do zasobów usługi Azure AD, ale usługa Azure AD jest mechanizmem uwierzytelniania przy użyciu zarządzanych tożsamości przypisanych do zasobów innych usług.

- [Zarządzana tożsamość zasobów platformy Azure](../managed-identities-azure-resources/overview.md)

- [Usługi obsługujące zarządzaną tożsamość zasobów platformy Azure](../managed-identities-azure-resources/services-support-managed-identities.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="im-3-use-azure-ad-single-sign-on-sso-for-application-access"></a>IM-3: Korzystanie z logowania jednokrotnego usługi Azure AD na potrzeby dostępu do aplikacji

**Wskazówki**: Użyj Azure Active Directory, aby zapewnić zarządzanie tożsamościami i dostępem do zasobów platformy Azure, aplikacji w chmurze i aplikacji lokalnych. Obejmuje to tożsamości przedsiębiorstwa, takie jak pracownicy, a także tożsamości zewnętrzne, takie jak partnerzy, dostawcy i dostawcy. Umożliwia to logowanie jednokrotne w celu zarządzania i bezpiecznego dostępu do danych i zasobów organizacji w środowisku lokalnym i w chmurze. Połącz wszystkich użytkowników, aplikacje i urządzenia z usługą Azure AD w celu zapewnienia bezproblemowego, bezpiecznego dostępu, lepszego wglądu i większej kontroli.

 
- [Informacje na temat logowania jednokrotnego aplikacji za pomocą usługi Azure AD](../manage-apps/what-is-single-sign-on.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4: Używanie kontrolek silnego uwierzytelniania dla całego dostępu opartego na usłudze Azure Active Directory

**Wskazówki**: Użyj Azure Active Directory do obsługi kontroli silnego uwierzytelniania za pomocą uwierzytelniania wieloskładnikowego (MFA) i silnych metod bezhaseł.
- Uwierzytelnianie wieloskładnikowe — włącz usługę Azure AD MFA i postępuj zgodnie z zaleceniami dotyczącymi zarządzania tożsamościami i dostępem w usłudze Azure Security Center, aby zapoznać się z najlepszymi rozwiązaniami w konfiguracji usługi MFA. Usługę MFA można wymusić na wszystkich lub wybranych użytkownikach albo na poziomie poszczególnych użytkowników na podstawie warunków logowania i czynników ryzyka.
- Uwierzytelnianie bezhasło — dostępne są trzy opcje uwierzytelniania bezhasła: funkcja Windows Hello dla firm, aplikacja Microsoft Authenticator i lokalne metody uwierzytelniania, takie jak karty inteligentne.

W przypadku użytkowników z uprawnieniami administratora i uprzywilejowanego upewnij się, że jest używana najwyższa Metoda silnego uwierzytelniania, a następnie zastąp odpowiednie zasady silnego uwierzytelniania innym użytkownikom.

Usługa Azure AD obsługuje starsze uwierzytelnianie oparte na hasłach, takie jak konta tylko w chmurze (konta użytkowników utworzone bezpośrednio w usłudze Azure AD), które mają zasady haseł linii bazowej lub konta hybrydowe (konta użytkowników, które pochodzą z lokalnego Active Directory), które będą zgodne z lokalnymi zasadami haseł. W przypadku korzystania z uwierzytelniania opartego na hasłach usługa Azure AD zapewnia funkcję ochrony hasłem, która uniemożliwia użytkownikom ustawianie haseł, które są łatwe do odgadnięcia. Firma Microsoft oferuje globalną listę zakazanych haseł, które są aktualizowane na podstawie danych telemetrycznych, a klienci mogą rozszerzyć listę na podstawie ich potrzeb (np. znakowania, dokumentacji kulturowej itp.). Ta ochrona hasłem może być używana w przypadku kont tylko w chmurze i hybrydowych.

Uwierzytelnianie oparte na poświadczeniach hasła jest podatne na popularne metody ataków. Aby zapewnić wyższy poziom zabezpieczeń, należy użyć silnego uwierzytelniania, takiego jak MFA i zasad silnych haseł. W przypadku aplikacji innych firm i usług portalu Marketplace, które mogą mieć domyślne hasła, należy je zmienić przy początkowej konfiguracji usługi.

 
- [Jak wdrożyć usługę Azure AD MFA](../authentication/howto-mfa-getstarted.md) 

 

 
- [Wprowadzenie do opcji uwierzytelniania bez hasła w przypadku usługi Azure Active Directory](../authentication/concept-authentication-passwordless.md) 

 

 
- [Domyślne zasady haseł usługi Azure AD](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-policy#password-policies-that-only-apply-to-cloud-user-accounts)

- [Eliminowanie nieprawidłowych haseł przy użyciu ochrony haseł w usłudze Azure AD](../authentication/concept-password-ban-bad.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="im-5-monitor-and-alert-on-account-anomalies"></a>IM-5: Monitorowanie anomalii kont i wyświetlanie alertów

**Wskazówki**: Azure Active Directory udostępnia następujące źródła danych:

 
- Logowania — raporty aktywności logowania zawierają informacje na temat użycia zarządzanych aplikacji i działań użytkownika związane z logowaniem.

 
- Dzienniki inspekcji — udostępnia możliwość śledzenia wszystkich zmian wprowadzanych przez różne funkcje usługi Azure AD za pomocą dzienników. Działania umieszczane w dziennikach inspekcji to na przykład zmiany wprowadzone w dowolnych zasobach usługi Azure AD, takie jak dodawanie lub usuwanie użytkowników, aplikacji, grup, ról i zasad.

 
- Ryzykowne logowania — ryzykowne logowanie jest wskaźnikiem próby logowania, które mogło zostać wykonane przez osobę, która nie jest prawowitym właścicielem konta użytkownika.

 
- Użytkownicy oflagowani w związku z ryzykiem — ryzykowny użytkownik jest wskaźnikiem konta użytkownika, którego bezpieczeństwo mogło zostać naruszone.

 

Te źródła danych można zintegrować z Azure Monitor, wskaźnikiem produktów platformy Azure lub systemami SIEM innych firm.

 

 
Usługa Azure Security Center może również wysyłać alerty dotyczące określonych podejrzanych działań, takich jak nadmierna liczba nieudanych prób uwierzytelnienia, czy przestarzałe konta w subskrypcji.

 

 
Usługa Azure Advanced Threat Protection (ATP) to rozwiązanie z zakresu zabezpieczeń, które może używać sygnałów usługi Active Directory do identyfikowania, wykrywania i badania zaawansowanych zagrożeń, naruszonych tożsamości i złośliwych działań wewnętrznych.

 

 
- [Raporty dotyczące inspekcji w usłudze Azure Active Directory](../reports-monitoring/concept-audit-logs.md) 

 

 
- [Jak wyświetlić ryzykowne logowania w usłudze Azure AD](/azure/active-directory/reports-monitoring/concept-risky-sign-ins) 

 

 
- [Identyfikowanie użytkowników usługi Azure AD oflagowanych na skutek ryzykownego działania](/azure/active-directory/reports-monitoring/concept-user-at-risk) 

 

 
- [Jak monitorować działania użytkowników związane z tożsamościami i dostępem w usłudze Azure Security Center](../../security-center/security-center-identity-access.md) 

 

 
- [Alerty w module analizy zagrożeń usługi Azure Security Center](../../security-center/alerts-reference.md) 

 

 
- [Jak zintegrować dzienniki aktywności platformy Azure z usługą Azure Monitor](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM-6: Ograniczanie dostępu do zasobów platformy Azure na podstawie warunków

**Wskazówki**: korzystanie z dostępu warunkowego Azure Active Directory (Azure AD) w celu uzyskania bardziej szczegółowej kontroli dostępu na podstawie warunków zdefiniowanych przez użytkownika, takich jak nazwy logowania użytkowników z określonych zakresów adresów IP, będą musieli używać uwierzytelniania wieloskładnikowego na potrzeby logowania. Szczegółowe zasady zarządzania sesjami uwierzytelniania mogą być również używane dla różnych przypadków użycia.

 
- [Omówienie dostępu warunkowego usługi Azure AD](../conditional-access/overview.md) 

 

 
- [Typowe zasady dostępu warunkowego](../conditional-access/concept-conditional-access-policy-common.md) 

 

 
- [Skonfiguruj zarządzanie sesją uwierzytelniania z użyciem dostępu warunkowego](../conditional-access/howto-conditional-access-session-lifetime.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="im-8-secure-user-access-to-legacy-applications"></a>KOMUNIKATOR-8: bezpieczny dostęp użytkowników do starszych aplikacji

**Wskazówki**: Upewnij się, że masz nowoczesne kontrole dostępu i monitorowanie sesji dla starszych aplikacji oraz przechowywanych i przetwarzanych danych. Gdy sieci VPN są często używane do uzyskiwania dostępu do starszych aplikacji, często mają tylko podstawową kontrolę dostępu i ograniczone monitorowanie sesji.

 
Usługa Azure serwer proxy aplikacji usługi Azure AD umożliwia publikowanie starszych aplikacji lokalnych dla użytkowników zdalnych z logowaniem jednokrotnym w sposób jawny, jednocześnie sprawdzając wiarygodność zarówno zdalnych użytkowników, jak i urządzeń za pomocą dostępu warunkowego usługi Azure AD.

 

 
Alternatywnie Microsoft Cloud App Security to usługa brokera zabezpieczeń dostępu w chmurze (CASB), która może zapewnić kontrolki do monitorowania sesji aplikacji użytkownika i blokowania akcji (dla starszych aplikacji lokalnych i aplikacji w chmurze jako usługi (SaaS)).

 

 
- [serwer proxy aplikacji usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy#what-is-application-proxy) 

 

 
- [Microsoft Cloud App Security najlepszych praktyk](/cloud-app-security/best-practices)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

## <a name="privileged-access"></a>Dostęp uprzywilejowany

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: dostęp uprzywilejowany](/azure/security/benchmarks/security-controls-v2-privileged-access).*

### <a name="pa-1-protect-and-limit-highly-privileged-users"></a>PA-1: Ochrona i ograniczanie użytkowników z wysokim poziomem uprawnień

**Wskazówki**: najważniejsze wbudowane role to usługa Azure AD jest administratorem globalnym i administratorem roli uprzywilejowanej, ponieważ użytkownicy przypisani do tych dwóch ról mogą delegować role administratorów:

- Administrator globalny: Użytkownicy z tą rolą mają dostęp do wszystkich funkcji administracyjnych w usłudze Azure AD, a także usług korzystających z tożsamości usługi Azure AD.

- Administrator ról uprzywilejowanych: Użytkownicy z tą rolą mogą zarządzać przypisaniami ról w usłudze Azure AD, a także w Azure AD Privileged Identity Management (PIM). Ponadto ta rola umożliwia zarządzanie wszystkimi aspektami jednostek PIM i administracyjnych.

Mogą istnieć inne role krytyczne, które należy zastosować w przypadku używania ról niestandardowych z określonymi przypisanymi uprawnieniami. Można również zastosować podobne kontrolki do konta administratora krytycznych zasobów firmy.

Usługa Azure AD ma wysoce uprzywilejowane konta: Użytkownicy i jednostki usługi, które są bezpośrednio lub pośrednio przypisane do, lub uprawnione do ról administratora globalnego lub administrator ról uprzywilejowanych, a także inne role wysoce uprzywilejowane w usłudze Azure AD i platformie Azure.

Ogranicz liczbę kont wysoce uprzywilejowanych i Chroń te konta na poziomie podwyższonego poziomu, ponieważ użytkownicy z tym uprawnieniem mogą bezpośrednio lub pośrednio odczytywać i modyfikować każdy zasób w środowisku platformy Azure.

Można włączyć uprzywilejowany dostęp just-in-time (JIT) do zasobów platformy Azure i usługi Azure AD przy użyciu usługi Azure AD Privileged Identity Management (PIM). Dostęp JIT polega na przyznawaniu uprawnień tymczasowych do wykonywania zadań uprzywilejowanych tylko wtedy, gdy użytkownicy ich potrzebują. Usługa PIM może również generować alerty zabezpieczeń w przypadku podejrzanych lub niebezpiecznych działań w ramach organizacji usługi Azure AD.

- [Uprawnienia ról administratora w usłudze Azure AD](/azure/active-directory/users-groups-roles/directory-assign-admin-roles) 

- [Używanie alertów zabezpieczeń usługi Azure Privileged Identity Management](../privileged-identity-management/pim-how-to-configure-security-alerts.md) 

- [Zabezpieczanie uprzywilejowanego dostępu dla wdrożeń hybrydowych i wdrożeń w chmurze w usłudze Azure AD](/azure/active-directory/users-groups-roles/directory-admin-roles-secure)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2: Ogranicz dostęp administracyjny do systemów o krytycznym znaczeniu dla firmy

**Wskazówki**: Użyj Azure Active Directory Privileged Identity Management i uwierzytelniania wieloskładnikowego, aby ograniczyć dostęp administracyjny do systemów krytycznych dla działania firmy.

- [Privileged Identity Management zatwierdzanie żądań aktywacji roli](../privileged-identity-management/azure-ad-pim-approval-workflow.md)

- [Uwierzytelnianie wieloskładnikowe i dostęp warunkowy](../conditional-access/howto-conditional-access-policy-admin-mfa.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="pa-3-review-and-reconcile-user-access-regularly"></a>PA-3: Regularne przeglądanie i uzgadnianie dostępu użytkowników

**Wskazówki**: regularnie Przeglądaj przypisania dostępu do konta użytkownika, aby upewnić się, że konta i ich dostęp są prawidłowe, szczególnie w przypadku ról o wysokim poziomie uprawnień, takich jak Administrator globalny i administrator ról uprzywilejowanych. Za pomocą przeglądów dostępu w usłudze Azure Active Directory (Azure AD) można przeglądać członkostwa w grupach, uzyskiwać dostęp do aplikacji dla przedsiębiorstw i przypisań ról, zarówno dla ról usługi Azure AD, jak i ról platformy Azure. Raportowanie usługi Azure AD może zapewnić dzienniki, aby ułatwić odnajdywanie starych kont. Możesz również użyć Azure AD Privileged Identity Management, aby utworzyć przepływ pracy raportu przeglądu dostępu, aby ułatwić proces recenzowania.

Ponadto usługa Azure Privileged Identity Management może być również skonfigurowana do zgłaszania alertów w przypadku tworzenia zbyt dużej liczby kont administratorów oraz do identyfikowania kont administratorów, które są przestarzałe lub nieprawidłowo skonfigurowane.

- [Tworzenie przeglądu dostępu do ról usługi Azure AD w Privileged Identity Management (PIM)](../privileged-identity-management/pim-how-to-start-security-review.md)

- [Tworzenie przeglądu dostępu do ról zasobów platformy Azure w Privileged Identity Management (PIM)](../privileged-identity-management/pim-resource-roles-start-access-review.md)

- [Jak korzystać z przeglądów tożsamości i dostępu w usłudze Azure AD](../governance/access-reviews-overview.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="pa-4-set-up-emergency-access-in-azure-ad"></a>PA-4: Konfigurowanie dostępu awaryjnego w usłudze Azure AD

**Wskazówki**: aby zapobiec przypadkowemu zablokowaniu organizacji usługi Azure AD, skonfiguruj konto dostępu awaryjnego w celu uzyskania dostępu, gdy nie można używać zwykłych kont administracyjnych. Konta dostępu awaryjnego są zwykle wysoce uprzywilejowane i nie powinny być przypisane do konkretnych osób.
Konta dostępu awaryjnego są ograniczone do sytuacji awaryjnych lub nagłych, w których normalne konta administracyjne nie mogą być używane.

Należy upewnić się, że poświadczenia (takie jak hasło, certyfikat lub karta inteligentna) dla kont dostępu awaryjnego są bezpieczne i znane tylko osobom, które są upoważnione do ich używania tylko w sytuacji awaryjnej.

- [Zarządzanie kontami dostępu awaryjnego w usłudze Azure AD](/azure/active-directory/users-groups-roles/directory-emergency-access)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="pa-5-automate-entitlement-management"></a>PA-5: Automatyzowanie zarządzania uprawnieniami 

**Wskazówki**: korzystanie z funkcji zarządzania prawami usługi Azure AD w celu zautomatyzowania przepływów pracy żądania dostępu, w tym przypisań, przeglądów i ważności. Obsługiwane jest również podwójne lub wieloetapowe zatwierdzenie.

- [Co to jest zarządzanie prawami w usłudze Azure AD](../governance/entitlement-management-overview.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="pa-6-use-privileged-access-workstations"></a>PA-6: Korzystanie ze stacji roboczych z dostępem uprzywilejowanym

**Wskazówka**: Zabezpieczone, izolowane stacje robocze mają kluczowe znaczenie dla bezpieczeństwa poufnych ról, takich jak administratorzy, deweloperzy i operatorzy usług o kluczowym znaczeniu. Używaj wysoce bezpiecznych stacji roboczych użytkowników i/lub Azure bastionu na potrzeby zadań administracyjnych. Użyj usługi Azure Active Directory, zaawansowanej ochrony przed zagrożeniami w usłudze Microsoft Defender i/lub usługi Microsoft Intune do wdrożenia bezpiecznej i zarządzanej stacji roboczej użytkownika na potrzeby zadań administracyjnych. Zabezpieczone stacje robocze mogą być zarządzane centralnie, aby wymusić bezpieczną konfigurację, w tym silne uwierzytelnianie, oprogramowanie i sprzętowe punkty odniesienia, ograniczony dostęp logiczny i sieciowy.

- [Zabezpieczanie urządzeń w ramach uprzywilejowanego dostępu](/security/compass/privileged-access-devices)

- [Implementowanie dostępu uprzywilejowanego](/security/compass/privileged-access-deployment)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="pa-7-follow-just-enough-administration-least-privilege-principle"></a>DU-7: Przestrzeganie podejścia wystarczającego zakresu administracji (zasada stosowania najniższych uprawnień) 

**Wskazówki**: klienci mogą skonfigurować wdrożenie Azure Active Directory (Azure AD) w celu uzyskania najniższych uprawnień, przypisując użytkownikom role z minimalnymi uprawnieniami wymaganymi przez użytkowników do wykonywania zadań administracyjnych.

- [Uprawnienia ról administratora w usłudze Azure AD](../roles/permissions-reference.md)

- [Przypisywanie ról administracyjnych w usłudze Azure AD](../roles/manage-roles-portal.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="pa-8-choose-approval-process-for-microsoft-support"></a>PA-8: wybór procesu zatwierdzania dla pomocy technicznej firmy Microsoft  

**Wskazówki**: Azure Active Directory nie obsługuje skrytki klienta. Firma Microsoft może współpracować z klientami za pomocą metod innych niż skrytka do zatwierdzania dostępu do danych klienta.

**Odpowiedzialność**: Współużytkowane

**Monitorowanie Azure Security Center**: brak

## <a name="data-protection"></a>Ochrona danych

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: ochrona danych](/azure/security/benchmarks/security-controls-v2-data-protection).*

### <a name="dp-2-protect-sensitive-data"></a>DP-2: Ochrona poufnych danych

**Wskazówki**: należy wziąć pod uwagę następujące wskazówki dotyczące implementowania ochrony poufnych danych:

- **Izolacja:** Katalog to granica danych, za pomocą której usługi Azure Active Directory (Azure AD) przechowują i przetwarzają dane dla klienta. Klienci powinni określić, gdzie mają być potrzebne większość danych klienta usługi Azure AD, ustawiając właściwość Country w katalogu.

- **Segmentacja:** Rola administratora globalnego ma pełną kontrolę nad wszystkimi danymi katalogowymi oraz regułami, które regulują dostęp i instrukcje przetwarzania. Katalog może być podzielony na jednostki administracyjne i udostępniane użytkownikom i grupom, które mają być zarządzane przez administratorów tych jednostek, Administratorzy globalni mogą delegować odpowiedzialność do innych zasad w organizacji, przypisując je do wstępnie zdefiniowanych ról lub ról niestandardowych, które mogą tworzyć.

 
- **Dostęp:** Uprawnienia mogą być stosowane w przypadku użytkowników, grup, ról, aplikacji lub urządzeń. Przypisanie może być trwałe lub czasowo na konfigurację Privileged Identity Management. 
  
- **Szyfrowanie:** Usługa Azure AD szyfruje wszystkie dane przechowywane lub przesyłane. Oferta nie pozwala klientom na szyfrowanie danych katalogu przy użyciu własnego klucza szyfrowania. 

Aby określić, jak ich wybrany kraj jest mapowany do lokalizacji fizycznej swojego katalogu, zobacz artykuł "gdzie są zlokalizowane dane".

- [Gdzie znajdują się dane artykułu](https://www.microsoft.com/trust-center/privacy/data-location)

Gdy klient korzysta z różnych narzędzi, funkcji i aplikacji usługi Azure AD, które współdziałają z ich katalogiem, użyj artykułu Azure Active Directory — gdzie znajdują się Twoje dane?

- [Gdzie znajdują się dane na pulpicie nawigacyjnym](https://msit.powerbi.com/view?r=eyJrIjoiYzEyZTc5OTgtNTdlZS00ZTVkLWExN2ItOTM0OWU4NjljOGVjIiwidCI6IjcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0NyIsImMiOjV9)

- [Dokumentacja ról usługi Azure AD](/azure/active-directory/roles/)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="dp-4-encrypt-sensitive-information-in-transit"></a>DP-4: Szyfrowanie poufnych informacji podczas przesyłania

**Wskazówki**: aby uzupełnić kontrolę dostępu, przesyłane dane powinny być chronione przed atakami "poza pasmem" (np. przechwytywaniem ruchu) przy użyciu szyfrowania, aby zapewnić, że osoby atakujące nie mogą łatwo odczytać ani zmodyfikować danych.

Usługa Azure AD obsługuje szyfrowanie danych podczas przesyłania przy użyciu protokołu TLS w wersji 1.2 lub nowszej.

Chociaż jest to opcjonalne dla ruchu w sieciach prywatnych, ma to kluczowe znaczenie dla ruchu w sieciach zewnętrznych i publicznych. W przypadku ruchu HTTP upewnij się, że wszyscy klienci łączący się z zasobami platformy Azure mogą negocjować protokół TLS w wersji 1.2 lub nowszej. W celu zarządzania zdalnego należy użyć protokołu SSH (dla systemu Linux) lub protokołu RDP/TLS (w systemie Windows), a nie z nieszyfrowanym protokołem. Przestarzałe protokoły SSL, TLS i SSH oraz słabe szyfry powinny być wyłączone.

Domyślnie platforma Azure zapewnia szyfrowanie danych przesyłanych między centrami danych platformy Azure.

- [Informacje o szyfrowaniu podczas przesyłania na platformę Azure](https://docs.microsoft.com/azure/security/fundamentals/encryption-overview#encryption-of-data-in-transit) 

- [Informacje o zabezpieczeniach protokołu TLS](/security/engineering/solving-tls1-problem) 

- [Podwójne szyfrowanie danych na platformie Azure podczas przesyłania](https://docs.microsoft.com/azure/security/fundamentals/double-encryption#data-in-transit)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

## <a name="asset-management"></a>Zarządzanie zasobami

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: zarządzanie elementami zawartości](/azure/security/benchmarks/security-controls-v2-asset-management).*

### <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>ZZ-1: Upewnij się, że zespół ds. zabezpieczeń ma wgląd w ryzyko związane z zasobami

**Wskazówki**: Upewnij się, że zespoły ds. zabezpieczeń mają przyznane uprawnienia czytelnika zabezpieczeń w dzierżawie i subskrypcjach platformy Azure, aby umożliwić im monitorowanie zagrożeń bezpieczeństwa przy użyciu usługi Azure Security Center. 

W zależności od istniejącej struktury obowiązków zespołu ds. zabezpieczeń, monitorowanie zagrożeń bezpieczeństwa może być obowiązkiem centralnego zespołu ds. zabezpieczeń lub zespołu lokalnego. Niemniej jednak informacje na temat zabezpieczeń i ryzyka muszą być zawsze agregowane centralnie w ramach danej organizacji. 

Uprawnienia czytelnika zabezpieczeń mogą być stosowane szeroko do całej dzierżawy (główna grupa zarządzania) lub do zakresu w postaci grup zarządzania lub określonych subskrypcji. 

Do uzyskania wglądu w obciążenia i usługi mogą być wymagane dodatkowe uprawnienia. 

- [Omówienie roli czytelnika zabezpieczeń](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#security-reader)

- [Omówienie grup zarządzania platformy Azure](../../governance/management-groups/overview.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>AM-5: ograniczanie możliwości korzystania przez użytkowników z Azure Resource Manager

**Wskazówki**: korzystanie z dostępu warunkowego Azure Active Directory (Azure AD) w celu ograniczenia możliwości korzystania przez użytkowników z usługi Azure AD za pośrednictwem Azure Portal przez skonfigurowanie "blokowania dostępu" dla aplikacji "Microsoft Azure Management".

- [Jak skonfigurować dostęp warunkowy, aby blokować dostęp do usługi Azure Resources](../../role-based-access-control/conditional-access-azure-management.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

## <a name="logging-and-threat-detection"></a>Rejestrowanie i wykrywanie zagrożeń

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: rejestrowanie i wykrywanie zagrożeń](/azure/security/benchmarks/security-controls-v2-logging-threat-protection).*

### <a name="lt-1-enable-threat-detection-for-azure-resources"></a>LT-1: Włączanie wykrywania zagrożeń dla zasobów platformy Azure

**Wskazówki**: Użyj wbudowanej funkcji wykrywania zagrożeń w usłudze Azure Active Directory (Azure AD) dla zasobów usługi Azure AD. 
 
 
 

 
Usługa Azure AD tworzy dzienniki aktywności, które są często używane do wykrywania zagrożeń i łowiectwa zagrożeń. Dzienniki logowania usługi Azure AD umożliwiają zapisanie działań związanych z uwierzytelnianiem i autoryzacją dla użytkowników, usług i aplikacji. Dzienniki inspekcji usługi Azure AD śledzą zmiany wprowadzone w dzierżawie usługi Azure AD, w tym zmiany, które zwiększają lub zmniejszają bezpieczeństwo stan.

- [Co to jest usługa Azure Active Directory Identity Protection?](../identity-protection/overview-identity-protection.md)

- [Łączenie Azure AD Identity Protection danych z platformą Azure — wskaźnikiem](../../sentinel/connect-azure-ad-identity-protection.md)

- [Łączenie Azure Active Directory danych z platformą Azure — wskaźnikiem](../../sentinel/connect-azure-active-directory.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>LT-2: Włącz wykrywanie zagrożeń na potrzeby zarządzania tożsamościami i dostępem na platformie Azure

**Wskazówki**: Azure Active Directory (Azure AD) udostępniają następujące dzienniki użytkowników, które mogą być wyświetlane w usłudze Azure AD Reporting lub zintegrowane z Azure Monitorm, wskaźnikiem na platformie Azure lub innymi narzędziami Siem/monitorowania, aby uzyskać bardziej zaawansowane przypadki monitorowania i analizy: 
- Logowania — raporty aktywności logowania zawierają informacje na temat użycia zarządzanych aplikacji i działań użytkownika związane z logowaniem. 
- Dzienniki inspekcji — udostępnia możliwość śledzenia wszystkich zmian wprowadzanych przez różne funkcje usługi Azure AD za pomocą dzienników. Działania umieszczane w dziennikach inspekcji to na przykład zmiany wprowadzone w dowolnych zasobach usługi Azure AD, takie jak dodawanie lub usuwanie użytkowników, aplikacji, grup, ról i zasad. 
- Ryzykowne logowania — ryzykowne logowanie jest wskaźnikiem próby logowania, które mogło zostać wykonane przez osobę, która nie jest prawowitym właścicielem konta użytkownika. 
- Ryzykowni użytkownicy — ryzykowny użytkownik jest wskaźnikiem konta użytkownika, które mogło zostać naruszone. 

Wykrywanie ryzyka ochrony tożsamości jest domyślnie włączone i nie wymaga procesu dołączania. Stopień szczegółowości lub ryzyko są określane przez jednostkę SKU licencji. 

- [Raporty dotyczące inspekcji w usłudze Azure Active Directory](../reports-monitoring/concept-audit-logs.md)  

- [Włączanie usługi Azure Identity Protection](../identity-protection/overview-identity-protection.md)  

- [Ochrona przed zagrożeniami w usłudze Azure Security Center](/azure/security-center/threat-protection)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4: Włącz rejestrowanie zasobów platformy Azure

**Wskazówki**: usługa Azure Active Directory (Azure AD) tworzy dzienniki aktywności. W przeciwieństwie do niektórych usług platformy Azure, usługa Azure AD nie różni się od dzienników aktywności i zasobów. Dzienniki aktywności są automatycznie dostępne w sekcji usługi Azure AD Azure Portal i można je wyeksportować do Azure Monitor, Azure Event Hubs, Azure Storage, rozwiązań Siem i innych lokalizacji.
 
- Logowania — raporty aktywności logowania zawierają informacje na temat użycia zarządzanych aplikacji i działań użytkownika związane z logowaniem.  
 
- Dzienniki inspekcji — udostępnia możliwość śledzenia wszystkich zmian wprowadzanych przez różne funkcje usługi Azure AD za pomocą dzienników. Działania umieszczane w dziennikach inspekcji to na przykład zmiany wprowadzone w dowolnych zasobach usługi Azure AD, takie jak dodawanie lub usuwanie użytkowników, aplikacji, grup, ról i zasad.  
 
 

Usługa Azure AD udostępnia również dzienniki związane z zabezpieczeniami, które mogą być wyświetlane w usłudze Azure AD Reporting lub zintegrowane z Azure Monitorm, wskaźnikiem na platformę Azure lub innymi narzędziami SIEM/monitorowania, aby uzyskać bardziej zaawansowane przypadki monitorowania i analizy: 
- Ryzykowne logowania — ryzykowne logowanie jest wskaźnikiem próby logowania, które mogło zostać wykonane przez osobę, która nie jest prawowitym właścicielem konta użytkownika.
- Ryzykowni użytkownicy — ryzykowny użytkownik jest wskaźnikiem konta użytkownika, które mogło zostać naruszone. 

Wykrywanie ryzyka ochrony tożsamości jest domyślnie włączone i nie wymaga procesu dołączania. Stopień szczegółowości lub ryzyko są określane przez jednostkę SKU licencji. 

 
- [Raporty dotyczące działań i zabezpieczeń w Azure Active Directory](../reports-monitoring/overview-reports.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5: Scentralizowane zarządzanie dziennikami zabezpieczeń i analiza

**Wskazówki**: zalecamy następujące wytyczne, gdy klienci chcą scentralizować dzienniki zabezpieczeń w celu łatwiejszego wypróbowania zagrożeń i analizy stan zabezpieczeń:
 
- Scentralizowany magazyn rejestrowania i analiza w celu włączenia korelacji. Dla każdego źródła dziennika w usłudze Azure AD upewnij się, że masz przypisany właściciel danych, wskazówki dotyczące dostępu, lokalizację magazynu, narzędzia służące do przetwarzania i uzyskiwania dostępu do danych oraz wymagania dotyczące przechowywania danych.  
 
- Upewnij się, że masz integrację dzienników aktywności platformy Azure z centralnym logowaniem. Pozyskiwanie dzienników za pośrednictwem Azure Monitor do agregowania danych zabezpieczeń wygenerowanych przez urządzenia, zasoby sieciowe i inne systemy zabezpieczeń. W Azure Monitor używać Log Analytics obszarów roboczych do wykonywania zapytań i przeprowadzania analiz oraz używania kont usługi Azure Storage do przechowywania długoterminowego i archiwizowania.  
 
- Ponadto należy włączyć i dołączyć dane do usługi Azure wskaźnikowej lub SIEM innych firm.  
 

Wiele organizacji decyduje się na korzystanie z platformy Azure na potrzeby danych "gorąca", które są często używane, i usługi Azure Storage dla danych "zimnych", które są rzadziej używane.  
 

- [Jak zbierać dzienniki platformy i metryki za pomocą Azure Monitor](/azure/azure-monitor/platform/diagnostic-settings)   
 

- [Jak dołączyć wskaźnik na platformie Azure](../../sentinel/quickstart-onboard.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="lt-6-configure-log-storage-retention"></a>LT-6: Konfigurowanie przechowywania magazynu dzienników

**Wskazówki**: Upewnij się, że wszystkie konta magazynów lub log Analytics obszary robocze używane do przechowywania Azure Active Directory dzienników logowania, dzienników inspekcji i dzienników danych o podwyższonym ryzyku mają ustawiony okres przechowywania dziennika zgodnie z regulacjami zgodności w organizacji.

W Azure Monitor można ustawić okres przechowywania Log Analytics obszaru roboczego zgodnie z regulacjami zgodności w organizacji. Użyj usługi Azure Storage Data Lake lub Log Analytics konta obszaru roboczego do przechowywania długoterminowego i archiwizowania.

- [Jak skonfigurować okres przechowywania obszaru roboczego Log Analytics](/azure/azure-monitor/platform/manage-cost-storage)  

- [Przechowywanie dzienników zasobów na koncie usługi Azure Storage](/azure/azure-monitor/platform/resource-logs-collect-storage)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

## <a name="incident-response"></a>Reagowanie na zdarzenia

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: reagowanie na zdarzenia](/azure/security/benchmarks/security-controls-v2-incident-response).*

### <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1: Przygotowanie — aktualizowanie procesu reagowania na zdarzenia na platformie Azure

**Wskazówka**: Upewnij się, że organizacja ma procesy reagowania na zdarzenia związane z bezpieczeństwem, zaktualizował te procesy na platformie Azure i regularnie wykonuje je w celu zapewnienia gotowości.

- [Implementowanie zabezpieczeń w środowisku przedsiębiorstwa](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Przewodnik po odpowiedziach na zdarzenia](/microsoft-365/downloads/IR-Reference-Guide.pdf)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="ir-2-preparation--setup-incident-notification"></a>IR-2: Przygotowanie — powiadomienie o zdarzeniu konfiguracji

**Wskazówka**: Skonfiguruj informacje kontaktowe dotyczące zdarzenia zabezpieczeń w usłudze Azure Security Center. Informacje kontaktowe są używane przez firmę Microsoft do skontaktowania się z Tobą, jeśli centrum Microsoft Security Response Center (MSRC) wykryje, że dostęp do danych jest uzyskiwany przez nieuprawnioną lub nieautoryzowaną osobę. Dostępne są również opcje dostosowywania alertów dotyczących zdarzeń i powiadomień w różnych usługach platformy Azure w zależności od potrzeb związanych z odpowiedzią na zdarzenia. 

- [Jak ustawić kontakt zabezpieczeń usługi Azure Security Center](../../security-center/security-center-provide-security-contact-details.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3: wykrywanie i analiza — Tworzenie zdarzeń na podstawie alertów o wysokiej jakości 

**Wskazówka**: Upewnij się, że masz proces tworzenia alertów o wysokiej jakości i mierzenia jakości alertów. Dzięki temu można wyciągać wnioski z przeszłych zdarzeń i określić priorytety alertów dla analityków, dzięki czemu nie będą oni tracić czasu na wyniki fałszywie dodatnie. 

Alerty o wysokiej jakości można tworzyć na podstawie doświadczeń z przeszłych zdarzeń, sprawdzonych źródeł społeczności oraz narzędzi przeznaczonych do generowania i czyszczenia alertów przez połączenie i skorelowanie różnorodnych źródeł sygnałów. 

Azure Security Center oferuje alerty wysokiej jakości dla wielu zasobów platformy Azure. Można użyć łącznika danych usługi ASC do przesyłania strumieniowego alertów do usługi Azure Sentinel. Usługa Azure Sentinel umożliwia tworzenie zaawansowanych reguł alertów w celu automatycznego generowania zdarzeń na potrzeby badania. 

Eksportuj alerty i zalecenia usługi Azure Security Center przy użyciu funkcji eksportowania, aby pomóc identyfikować zagrożenia dla zasobów platformy Azure. Eksportuj alerty i zalecenia ręcznie lub w stały, ciągły sposób.

- [Jak skonfigurować eksportowanie](../../security-center/continuous-export.md)

- [Jak przesłać strumieniowo alerty do usługi Azure Sentinel](../../sentinel/connect-azure-security-center.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

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

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>IR-5: Wykrywanie i analiza — określanie priorytetów zdarzeń

**Wskazówki**: Zapewnij kontekst analitykom, na których zdarzeniach należy skupić się w pierwszej kolejności w oparciu o poziom ważności alertów i poufność aktywów. 

Usługa Azure Security Center przypisuje poziom ważności do każdego alertu, aby pomóc w ustaleniu, które alerty powinny być zbadane w pierwszej kolejności. Ważność jest oparta na tym, jak dobrze Security Center znajduje się w wyszukiwaniu lub analizie używanych do wystawiania alertu, a także poziom pewności, że istniało złośliwe zamiar w odniesieniu do działania, które prowadziło do alertu.

Ponadto oznacz zasoby przy użyciu tagów i utwórz system nazewnictwa, aby zidentyfikować i sklasyfikować zasoby platformy Azure, szczególnie te, które przetwarzają dane poufne.  Odpowiedzialność za korygowanie alertów w oparciu o krytyczne znaczenie zasobów platformy Azure i środowisko, w którym wystąpiło zdarzenie, leży po stronie użytkownika.

- [Alerty zabezpieczeń w Centrum zabezpieczeń Azure](../../security-center/security-center-alerts-overview.md)

- [Organizowanie zasobów platformy Azure przy użyciu tagów](/azure/azure-resource-manager/resource-group-using-tags)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="ir-6-containment-eradication-and-recovery--automate-the-incident-handling"></a>IR-6: Zawieranie, eliminowanie i odzyskiwanie — automatyzacja obsługi zdarzeń

**Wskazówka**: Automatyzuj ręczne powtarzające się zadania w celu przyspieszenia czasu reakcji i zmniejszenia obciążenia dla analityków. Wykonanie ręcznych zadań trwa dłużej, spowalnia każde zdarzenie i zmniejsza liczbę zdarzeń, które może obsłużyć analityk. Zadania wykonywane ręcznie zwiększają również zmęczenie analityków, co zwiększa ryzyko wystąpienia błędu ludzkiego, który powoduje opóźnienia, a także pogarsza zdolność analityków do skoncentrowania się na złożonych zadaniach. Korzystając z funkcji automatyzacji przepływów pracy w usłudze Azure Security Center i Azure Sentinel, można automatycznie wyzwalać akcje lub uruchamiać element playbook w odpowiedzi na przychodzące alerty zabezpieczeń. Element playbook wykonuje akcje, takie jak wysyłanie powiadomień, wyłączanie kont i izolowanie problematycznych sieci. 

- [Konfigurowanie automatyzacji przepływu pracy w usłudze Security Center](../../security-center/workflow-automation.md)

- [Konfigurowanie automatycznych reakcji na zagrożenia w usłudze Azure Security Center](https://docs.microsoft.com/azure/security-center/tutorial-security-incident#triage-security-alerts)

- [Konfigurowanie automatycznych reakcji na zagrożenia w usłudze Azure Sentinel](../../sentinel/tutorial-respond-threats-playbook.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

## <a name="posture-and-vulnerability-management"></a>Stan zabezpieczeń i zarządzanie lukami w zabezpieczeniach

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: stan zabezpieczeń i zarządzanie lukami w zabezpieczeniach](/azure/security/benchmarks/security-controls-v2-vulnerability-management).*

### <a name="pv-1-establish-secure-configurations-for-azure-services"></a>PV-1: Ustanów bezpieczne konfiguracje dla usług platformy Azure 

**Wskazówki**: rozwiązania do zarządzania tożsamościami i dostępem firmy Microsoft pomagają chronić dostęp do aplikacji i zasobów w środowisku lokalnym i w chmurze. Należy pamiętać, że organizacje przestrzegają najlepszych rozwiązań w zakresie zabezpieczeń, aby zapewnić, że ich implementacja zarządzania tożsamościami i dostępem jest bezpieczna i bardziej odporna na ataki. 

Na podstawie strategii implementacji zarządzania tożsamościami i dostępem organizacja powinna przestrzegać wskazówek dotyczących najlepszych rozwiązań firmy Microsoft w celu zabezpieczenia infrastruktury tożsamości. 

Organizacje, które współpracują z partnerami zewnętrznymi, powinni dodatkowo ocenić i wdrożyć odpowiednie konfiguracje ładu, zabezpieczeń i zgodności, aby zmniejszyć ryzyko dla bezpieczeństwa i chronić poufne zasoby.

- [Azure Identity Management and access control security best practices (Zarządzanie tożsamościami na platformie Azure i najlepsze rozwiązania dotyczące kontroli dostępu)](../../security/fundamentals/identity-management-best-practices.md)

- [Pięć kroków związanych z zabezpieczaniem infrastruktury tożsamości](../../security/fundamentals/steps-secure-identity.md)

- [Zabezpieczanie współpracy zewnętrznej w Azure Active Directory i Microsoft 365](secure-external-access-resources.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="pv-2-sustain-secure-configurations-for-azure-services"></a>PV-2: Utrzymaj bezpieczne konfiguracje dla usług platformy Azure

**Wskazówki**: Firma Microsoft Security Score zapewnia organizacjom miarę stan zabezpieczeń i zaleceń, które mogą pomóc w ochronie organizacji przed zagrożeniami. Zaleca się, aby organizacje rutynowo sprawdzali swój Bezpieczny wskaźnik dla sugestii działań naprawczych w celu usprawnienia ich stan zabezpieczeń tożsamości. 

- [Co to jest bezpieczna ocena tożsamości w Azure Active Directory?](identity-secure-score.md)

- [Wskaźnik bezpieczeństwa Microsoft](/microsoft-365/security/mtp/microsoft-secure-score)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8: Przeprowadzanie regularnej symulacji ataków

**Wskazówka**: W razie potrzeby przeprowadź test penetracyjny lub działania typu „red team” na zasobach platformy Azure i zapewnij korektę wszystkich krytycznych ustaleń dotyczących zabezpieczeń.
Postępuj zgodnie z regułami testowania penetracji w chmurze firmy Microsoft, aby upewnić się, że testy penetracyjne nie naruszają zasad firmy Microsoft. Korzystaj ze strategii firmy Microsoft i wykonywania testów typu „red team” i testów na żywo w zarządzanej przez firmę Microsoft infrastrukturze, usługach i aplikacjach w chmurze.

- [Testy penetracyjne na platformie Azure](../../security/fundamentals/pen-testing.md)

- [Reguły testów penetracyjnych zaangażowania](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Testy typu „red team” w chmurze firmy Microsoft](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Odpowiedzialność**: Współużytkowane

**Monitorowanie Azure Security Center**: brak

## <a name="governance-and-strategy"></a>Ład i strategia

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: ład i strategia](/azure/security/benchmarks/security-controls-v2-governance-strategy).*

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
- [Zalecenie dotyczące architektury zabezpieczeń platformy Azure — magazyn, dane i szyfrowanie](/azure/architecture/framework/security/storage-data-encryption)
 

 
- [Podstawy zabezpieczeń platformy Azure — zabezpieczenia, szyfrowanie i magazynowanie danych platformy Azure](../../security/fundamentals/encryption-overview.md) 

 
- [Cloud Adoption Framework — najlepsze rozwiązania z zakresu zabezpieczeń i szyfrowania danych platformy Azure](../../security/fundamentals/data-encryption-best-practices.md) 

 
- [Test porównawczy zabezpieczeń platformy Azure — zarządzanie elementami zawartości](/azure/security/benchmarks/security-benchmark-v2-asset-management) 

 
- [Test porównawczy zabezpieczeń platformy Azure — ochrona danych](/azure/security/benchmarks/security-benchmark-v2-data-protection)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="gs-2-define-enterprise-segmentation-strategy"></a>GS-2: Definiowanie strategii segmentacji przedsiębiorstwa 

**Wskazówka**: Ustanów strategię całego przedsiębiorstwa w zakresie segmentacji dostępu do elementów zawartości przy użyciu kombinacji tożsamości, sieci, aplikacji, subskrypcji, grupy zarządzania i innych mechanizmów kontroli.

Należy starannie zrównoważyć potrzebę rozdzielania zabezpieczeń, aby umożliwić codzienne działanie systemów, które muszą komunikować się ze sobą i uzyskiwać dostęp do danych.

Upewnij się, że strategia segmentacji jest zaimplementowana spójnie dla różnych typów kontroli, w tym zabezpieczeń sieci, modeli tożsamości i dostępu, a także modeli uprawnień/dostępu i procesów ludzkich.

- [Wskazówki dotyczące strategii segmentacji na platformie Azure (wideo)](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Wskazówki dotyczące strategii segmentacji na platformie Azure (dokument)](/security/compass/governance#enterprise-segmentation-strategy)

- [Wyrównywanie segmentacji sieci przy użyciu strategii segmentacji przedsiębiorstwa](/security/compass/network-security-containment#align-network-segmentation-with-enterprise-segmentation-strategy)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="gs-3-define-security-posture-management-strategy"></a>GS-3: Definiowanie strategii zarządzania stanem zabezpieczeń

**Wskazówka**: Nieustannie mierz i ograniczaj ryzyko dla poszczególnych elementów zawartości i środowiska, w którym są hostowane. Ustalaj priorytety elementów zawartości o wysokiej wartości i wysoce narażonych na ataki obszarów, takich jak opublikowane aplikacje, punkty danych przychodzących i wychodzących sieci, punkty końcowe użytkowników i administratorów itp.

- [Test porównawczy zabezpieczeń platformy Azure — stan i zarządzanie lukami w zabezpieczeniach](/azure/security/benchmarks/security-benchmark-v2-posture-vulnerability-management)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="gs-4-align-organization-roles-responsibilities-and-accountabilities"></a>GS-4: Dopasuj role organizacji, obowiązki i odpowiedzialność

**Wskazówka**: Zadbaj o to, aby udokumentować i udostępnić wyraźną strategię dotyczącą ról i obowiązków w organizacji zabezpieczeń. Ustalaj priorytety w celu wyraźnego określenia odpowiedzialności za decyzje dotyczące zabezpieczeń, informując wszystkie osoby o współużytkowanym modelu odpowiedzialności i informując zespoły techniczne o technologii do zabezpieczania chmury.

- [Najlepsze rozwiązanie dotyczące zabezpieczeń platformy Azure 1 — ludzie: informowanie zespołów o podróży zabezpieczeń w chmurze](/azure/cloud-adoption-framework/security/security-top-10#1-people-educate-teams-about-the-cloud-security-journey)

- [Najlepsze rozwiązanie dotyczące zabezpieczeń platformy Azure 2 — ludzie: informowanie zespołów o technologii zabezpieczeń w chmurze](/azure/cloud-adoption-framework/security/security-top-10#2-people-educate-teams-on-cloud-security-technology)

- [Najlepsze rozwiązanie dotyczące zabezpieczeń platformy Azure 3 — proces: przypisywanie odpowiedzialności za decyzje dotyczące zabezpieczeń chmury](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

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

- [Test porównawczy zabezpieczeń platformy Azure — zabezpieczenia sieci](/azure/security/benchmarks/security-benchmark-v2-network-security)

- [Omówienie zabezpieczeń sieci platformy Azure](../../security/fundamentals/network-overview.md)

- [Strategia architektury sieci przedsiębiorstwa](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="gs-6-define-identity-and-privileged-access-strategy"></a>GS-6: Definiowanie strategii dotyczącej tożsamości i dostępu uprzywilejowanego

**Wskazówka**: Ustanów metody obsługi tożsamości i dostępu uprzywilejowanego na platformie Azure jako część ogólnej strategii kontroli dostępu zabezpieczeń w organizacji.  

Ta strategia powinna obejmować udokumentowane wskazówki, zasady i standardy dla następujących elementów: 

-   Scentralizowany system tożsamości i uwierzytelniania oraz połączenie z innymi wewnętrznymi i zewnętrznymi systemami tożsamości

-   Metody silnego uwierzytelniania w różnych przypadkach użycia i warunkach

-   Ochrona użytkowników z wysokim poziomem uprawnień

-   Monitorowanie i obsługa nietypowych działań użytkowników  

-   Przegląd tożsamości i dostępu użytkownika oraz proces uzgadniania

Aby uzyskać więcej informacji, zapoznaj się z następującymi dokumentami:

- [Test porównawczy zabezpieczeń platformy Azure — zarządzanie tożsamością](/azure/security/benchmarks/security-benchmark-v2-identity-management)

- [Test porównawczy zabezpieczeń platformy Azure — dostęp uprzywilejowany](/azure/security/benchmarks/security-benchmark-v2-privileged-access)

- [Najlepsze rozwiązanie dotyczące zabezpieczeń platformy Azure 11 — architektura. Pojedyncza ujednolicona strategia zabezpieczeń](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Omówienie zabezpieczeń zarządzania tożsamościami platformy Azure](../../security/fundamentals/identity-management-overview.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

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

- [Test porównawczy zabezpieczeń platformy Azure — rejestrowanie i wykrywanie zagrożeń](/azure/security/benchmarks/security-benchmark-v2-logging-threat-detection)

- [Test porównawczy zabezpieczeń platformy Azure — reagowanie na zdarzenia](/azure/security/benchmarks/security-benchmark-v2-incident-response)

- [Najlepsze rozwiązanie dotyczące zabezpieczeń platformy Azure 4 — proces. Aktualizowanie procesów reagowania na zdarzenia dla chmury](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Przewodnik dotyczący decyzji w zakresie platformy wdrażania Azure, rejestrowania i raportowania](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

- [Skalowanie, zarządzanie i monitorowanie w przedsiębiorstwie na platformie Azure](/azure/cloud-adoption-framework/ready/enterprise-scale/management-and-monitoring)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

## <a name="next-steps"></a>Następne kroki

- Zobacz [Omówienie testu porównawczego zabezpieczeń platformy Azure w wersji 2](/azure/security/benchmarks/overview)
- Dowiedz się więcej o [punktach odniesienia zabezpieczeń platformy Azure](/azure/security/benchmarks/security-baselines-overview)
