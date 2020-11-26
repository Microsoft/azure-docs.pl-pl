---
title: Linia bazowa zabezpieczeń Azure dla usługi Azure Signal
description: Punkt odniesienia usługi Azure Signal Security zawiera wskazówki i zasoby dotyczące procedur związanych z wdrażaniem zaleceń dotyczących zabezpieczeń określonych w teście zabezpieczeń platformy Azure.
author: msmbaldwin
ms.service: signalr
ms.topic: conceptual
ms.date: 11/25/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 1760daceb27125add6e6f58cf0dac6cb61ff2c75
ms.sourcegitcommit: 192f9233ba42e3cdda2794f4307e6620adba3ff2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/26/2020
ms.locfileid: "96296441"
---
# <a name="azure-security-baseline-for-azure-signalr-service"></a>Linia bazowa zabezpieczeń Azure dla usługi Azure Signal

Ta linia bazowa zabezpieczeń ma zastosowanie do wskazówek dotyczących usługi Azure [Security test w wersji 2,0](../security/benchmarks/overview.md) do usługi Azure signaler. Test porównawczy zabezpieczeń platformy Azure zawiera zalecenia dotyczące sposobu zabezpieczania rozwiązań w chmurze na platformie Azure.
Zawartość jest pogrupowana według **kontroli zabezpieczeń** zdefiniowanych przez składnik Azure Security test i powiązane wskazówki dotyczące usługi Azure signaler. Nie wyłączono **kontrolek** mających zastosowanie do usługi Azure Signal.

 
Aby dowiedzieć się, jak usługa Azure Signal w całości mapuje na test porównawczy zabezpieczeń platformy Azure, zobacz [pełny plik mapowania zabezpieczeń usługi Azure Signal Security](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Bezpieczeństwo sieci

*Aby uzyskać więcej informacji, zobacz [wzorzec zabezpieczeń Azure: zabezpieczenia sieci](/azure/security/benchmarks/security-controls-v2-network-security).*

### <a name="ns-1-implement-security-for-internal-traffic"></a>NS-1: implementowanie zabezpieczeń dla ruchu wewnętrznego

**Wskazówki**: usługa sygnalizująca Microsoft Azure nie obsługuje wdrażania bezpośrednio w sieci wirtualnej. z tego powodu nie można wykorzystać niektórych funkcji sieciowych z zasobami oferty, takimi jak sieciowe grupy zabezpieczeń, tabele tras lub inne urządzenia zależne od sieci, takie jak Zapora platformy Azure. 

Usługa Azure Signal Service umożliwia jednak tworzenie prywatnych punktów końcowych w celu zabezpieczenia ruchu między zasobami w sieci wirtualnej a usługą Azure Signal.

Możesz również użyć tagów usługi i skonfigurować reguły sieciowej grupy zabezpieczeń, aby ograniczyć ruch przychodzący/wychodzący do usługi Azure Signal.

- [Używanie prywatnych punktów końcowych dla usługi Azure Signal Service](howto-private-endpoints.md)

- [Konfigurowanie kontroli dostępu do sieci](howto-network-access-control.md)

- [Używanie tagów usługi dla usługi Azure Signal Service](howto-service-tags.md)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="ns-2---connect-private-networks-together"></a>NS-2: — łączenie sieci prywatnych razem  

**Wskazówki**: Użyj prywatnych punktów końcowych, aby zabezpieczyć ruch między siecią wirtualną i usługą Azure Signal. Wybierz pozycję Azure ExpressRoute lub wirtualną sieć prywatną (VPN), aby utworzyć prywatne połączenia między centrami danych platformy Azure i infrastrukturą lokalną w środowisku z różnymi lokalizacjami. 

Połączenia ExpressRoute nie przechodzą przez publiczny Internet i oferują większą niezawodność, większe szybkości i krótsze opóźnienia niż typowe połączenia z Internetem. W przypadku połączeń sieci VPN typu punkt-lokacja i sieci VPN typu lokacja-lokacja można połączyć lokalne urządzenia lub sieci z siecią wirtualną przy użyciu dowolnej kombinacji tych opcji sieci VPN i usługi Azure ExpressRoute.

Aby połączyć co najmniej dwie sieci wirtualne na platformie Azure, użyj komunikacji równorzędnej sieci wirtualnej. Ruch sieciowy między równorzędnymi sieciami wirtualnymi jest prywatny i jest przechowywany w sieci szkieletowej platformy Azure.

- [Używanie prywatnych punktów końcowych dla usługi Azure Signal Service](howto-private-endpoints.md)

- [Co to są modele łączności ExpressRoute](../expressroute/expressroute-connectivity-models.md)

- [Omówienie usługi Azure VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md)

- [Komunikacja równorzędna sieci wirtualnych](../virtual-network/virtual-network-peering-overview.md)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="ns-3-establish-private-network-access-to-azure-services"></a>NS-3: Ustanów dostęp do sieci prywatnej do usług platformy Azure

**Wskazówki**: Użyj prywatnego linku platformy Azure, aby umożliwić prywatny dostęp do usługi Azure Signal Service z sieci wirtualnych bez przekraczania Internetu.

Dostęp prywatny to dodatkowa ochrona w miarę jak funkcja uwierzytelniania i zabezpieczeń ruchu oferowana przez usługi platformy Azure.

- [Opis linku prywatnego platformy Azure](../private-link/private-link-overview.md)

- [Używanie prywatnych punktów końcowych dla usługi Azure Signal Service](howto-private-endpoints.md)

- [Konfigurowanie kontroli dostępu do sieci](howto-network-access-control.md)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="ns-6-simplify-network-security-rules"></a>NS-6: uproszczenie reguł zabezpieczeń sieci

**Wskazówki**: Użyj tagów usługi Azure Virtual Network, aby zdefiniować kontrolę dostępu do sieci dla sieciowych grup zabezpieczeń lub zapory platformy Azure skonfigurowanej dla zasobów usługi Azure Signal Service. Podczas tworzenia reguł zabezpieczeń można użyć tagów usługi zamiast konkretnych adresów IP. Określając nazwę tagu usługi (na przykład: AzureSignalR) w odpowiednim polu źródłowym lub docelowym reguły, można zezwolić na ruch dla odpowiedniej usługi lub go odrzucić. Firma Microsoft zarządza prefiksami adresów, które obejmują tag usługi, i automatycznie aktualizuje tag usługi jako adresy.

- [Zrozumienie i używanie tagów usługi](../virtual-network/service-tags-overview.md)

- [Używanie tagów usługi dla usługi Azure Signal Service](howto-service-tags.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

## <a name="identity-management"></a>Zarządzanie tożsamością

*Aby uzyskać więcej informacji, zobacz [Test porównawczy zabezpieczeń platformy Azure: zarządzanie tożsamościami](/azure/security/benchmarks/security-controls-v2-identity-management).*

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>ZT-1: Ujednolicenie usługi Azure Active Directory jako centralnego systemu obsługi tożsamości i uwierzytelniania

**Wskazówki**: usługa Azure sygnalizująca używa usługi Azure Active Directory (Azure AD) jako domyślnej usługi zarządzania tożsamościami i dostępem. Należy przeprowadzić standaryzację usługi Azure AD, aby zarządzać zarządzaniem tożsamościami i dostępem w organizacji w programie:

- Microsoft Cloud zasoby, takie jak Azure Portal, Azure Storage, Azure Virtual Machine (Linux i Windows), Azure Key Vault, PaaS i SaaS.
- Zasoby organizacji, takie jak aplikacje na platformie Azure lub formowe zasoby sieciowe.

Usługa Azure Signal Service obsługuje tylko uwierzytelnianie usługi Azure AD dla płaszczyzny zarządzania, ale nie dla płaszczyzny danych. Poniżej znajduje się lista wbudowanych ról w usłudze Azure Signal Service:

- Współautor sygnalizującego
- Czytnik AccessKeyer sygnalizujący

Zabezpieczenie usługi Azure AD powinno być jednym z najważniejszych priorytetów w zakresie zabezpieczeń w chmurze organizacji. Usługa Azure AD zapewnia bezpieczną ocenę tożsamości, która pomaga ocenić stan zabezpieczeń tożsamości w porównaniu z zaleceniami dotyczącymi najlepszych rozwiązań firmy Microsoft. Użyj tego wskaźnika, aby ocenić, jak ściśle Twoja konfiguracja spełnia zalecenia dotyczące najlepszych rozwiązań oraz poprawić stan zabezpieczeń.

Usługa Azure AD obsługuje tożsamości zewnętrzne, które umożliwiają użytkownikom bez konto Microsoft logowania się do aplikacji i zasobów przy użyciu tożsamości zewnętrznej.

- [Dzierżawa w Azure Active Directory](../active-directory/develop/single-and-multi-tenant-apps.md)

- [Jak utworzyć i skonfigurować wystąpienie usługi Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

- [Użyj zewnętrznych dostawców tożsamości dla aplikacji](/azure/active-directory/b2b/identity-providers)

- [Co to jest bezpieczna ocena tożsamości w Azure Active Directory](../active-directory/fundamentals/identity-secure-score.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="im-2-manage-application-identities-securely-and-automatically"></a>Wiadomości błyskawiczne — 2: Zarządzaj tożsamościami aplikacji bezpiecznie i automatycznie

**Wskazówki**: usługa Azure Signal Service używa tożsamości zarządzanych przez platformę Azure dla kont nieludzkich, takich jak jeden wywołujący element nadrzędny w scenariuszu bezserwerowym. Zalecane jest używanie funkcji tożsamości zarządzanej platformy Azure do uzyskiwania dostępu do innych zasobów platformy Azure. Usługa Azure Signal Service może przeprowadzić natywną autoryzację do usług lub zasobów platformy Azure, które obsługują uwierzytelnianie Azure Active Directory (Azure AD) za pośrednictwem wstępnie zdefiniowanej reguły udzielania dostępu bez korzystania z poświadczeń zakodowanych w kodzie źródłowym lub w plikach konfiguracji.

- [Tożsamości zarządzane przez platformę Azure](../active-directory/managed-identities-azure-resources/overview.md)

- [Usługi obsługujące zarządzane tożsamości dla zasobów platformy Azure](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md) 

- [Zarządzane tożsamości dla usługi Azure Signal Service](howto-use-managed-identity.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="im-3-use-azure-ad-single-sign-on-sso-for-application-access"></a>ZT-3: Korzystanie z logowania jednokrotnego usługi Azure AD na potrzeby dostępu do aplikacji

**Wskazówki**: usługa Azure Signal Service używa usługi Azure Active Directory (Azure AD), aby zapewnić zarządzanie tożsamościami i dostępem do zasobów sygnalizujących. Dotyczy to zarówno tożsamości przedsiębiorstwa, takich jak pracownicy, jak i tożsamości zewnętrznych, takich jak partnerzy, sprzedawcy i dostawcy. Umożliwia to Logowanie jednokrotne w celu zarządzania i bezpiecznego dostępu do danych i zasobów organizacji w środowisku lokalnym i w chmurze. Połącz wszystkich użytkowników, aplikacje i urządzenia z usługą Azure AD w celu zapewnienia bezproblemowego, bezpiecznego dostępu, lepszego wglądu i kontroli.

- [Informacje na temat logowania jednokrotnego do aplikacji za pomocą usługi Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>ZT-4: Używanie mechanizmów silnego uwierzytelniania dla każdego dostępu opartego na usłudze Azure Active Directory

**Wskazówki**: usługa Azure sygnalizująca korzysta z usługi Azure Active Directory (Azure AD), która obsługuje funkcje kontroli silnego uwierzytelniania za pośrednictwem uwierzytelniania wieloskładnikowego i silnych metod bezhaseł.

- Uwierzytelnianie wieloskładnikowe — włączenie uwierzytelniania wieloskładnikowego usługi Azure AD i przestrzeganie zaleceń związanych z zarządzaniem tożsamościami i dostępem Azure Security Center do odpowiednich najlepszych rozwiązań w ramach konfiguracji uwierzytelniania wieloskładnikowego. Uwierzytelnianie wieloskładnikowe może być wymuszane na wszystkich wybranych użytkownikach lub na poziomie poszczególnych użytkowników na podstawie warunków logowania i czynników ryzyka.

- Uwierzytelnianie bez hasła — dostępne są trzy opcje uwierzytelniania bez hasła: usługa Windows Hello dla firm, aplikacja Microsoft Authenticator i lokalne metody uwierzytelniania, takie jak karty inteligentne.

W przypadku administratorów i użytkowników uprzywilejowanych należy upewnić się, że używany jest najwyższy poziom metody silnego uwierzytelniania, a następnie wprowadzić odpowiednią politykę silnego uwierzytelniania dla innych użytkowników.

- [Jak włączyć uwierzytelnianie wieloskładnikowe na platformie Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Wprowadzenie do opcji uwierzytelniania bez hasła w przypadku usługi Azure Active Directory](../active-directory/authentication/concept-authentication-passwordless.md)

- [Domyślne zasady haseł usługi Azure AD](../active-directory/authentication/concept-sspr-policy.md#password-policies-that-only-apply-to-cloud-user-accounts)

- [Eliminowanie nieprawidłowych haseł przy użyciu ochrony hasłem Azure Active Directory](../active-directory/authentication/concept-password-ban-bad.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="im-5-monitor-and-alert-on-account-anomalies"></a>ZT-5: Monitorowanie anomalii kont i wyświetlanie alertów

**Wskazówki**: usługa Azure Signal jest zintegrowana z Azure Active Directory, w którym znajdują się następujące źródła danych:

- Logowanie — raport dotyczący logowania zawiera informacje na temat użycia zarządzanych aplikacji i działań związanych z logowaniem użytkowników.

- Dzienniki inspekcji — udostępnia możliwość śledzenia wszystkich zmian wprowadzanych przez różne funkcje usługi Azure AD za pomocą dzienników. Działania umieszczane w dziennikach inspekcji to na przykład zmiany wprowadzone w dowolnych zasobach usługi Azure AD, takie jak dodawanie lub usuwanie użytkowników, aplikacji, grup, ról i zasad.

- Ryzykowne logowanie — ryzykowne logowanie jest wskaźnikiem próby logowania, które mogło zostać wykonane przez kogoś, kto nie jest uprawnionym właścicielem konta użytkownika.

- Użytkownicy oflagowani w związku z ryzykiem — ryzykowny użytkownik jest wskaźnikiem konta użytkownika, którego bezpieczeństwo mogło zostać naruszone.

Te źródła danych można zintegrować z Azure Monitorm, systemem Azure — wskaźnikiem lub zabezpieczeniami innych firm (SIEM).

Usługa Azure Security Center może również wysyłać alerty dotyczące określonych podejrzanych działań, takich jak nadmierna liczba nieudanych prób uwierzytelnienia, czy przestarzałe konta w subskrypcji.

Usługa Azure Advanced Threat Protection (ATP) to rozwiązanie z zakresu zabezpieczeń, które może używać sygnałów usługi Active Directory do identyfikowania, wykrywania i badania zaawansowanych zagrożeń, naruszonych tożsamości i złośliwych działań wewnętrznych.

- [Raporty dotyczące inspekcji w usłudze Azure Active Directory](../active-directory/reports-monitoring/concept-audit-logs.md)

- [Jak monitorować działania użytkowników związane z tożsamościami i dostępem w usłudze Azure Security Center](../security-center/security-center-identity-access.md)

- [Jak zintegrować dzienniki aktywności platformy Azure z usługą Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>Wiadomości błyskawiczne — 6: Ogranicz dostęp do zasobów platformy Azure na podstawie warunków

**Wskazówki**: usługa Azure Signal Management obsługuje dostęp warunkowy Azure Active Directory (Azure AD) w celu uzyskania bardziej szczegółowej kontroli dostępu na podstawie warunków zdefiniowanych przez użytkownika, takich jak nazwy logowania użytkowników z określonych zakresów adresów IP, będą musieli używać uwierzytelniania wieloskładnikowego na potrzeby logowania. Szczegółowe zasady zarządzania sesjami uwierzytelniania mogą być również używane dla różnych przypadków użycia.

- [Omówienie dostępu warunkowego platformy Azure](../active-directory/conditional-access/overview.md)

- [Typowe zasady dostępu warunkowego](../active-directory/conditional-access/concept-conditional-access-policy-common.md)

- [Konfigurowanie zarządzania sesjami uwierzytelniania przy użyciu dostępu warunkowego](../active-directory/conditional-access/howto-conditional-access-session-lifetime.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

## <a name="privileged-access"></a>Dostęp uprzywilejowany

*Aby uzyskać więcej informacji, zobacz [Test porównawczy zabezpieczeń platformy Azure: Dostęp uprzywilejowany](/azure/security/benchmarks/security-controls-v2-privileged-access).*

### <a name="pa-1-protect-and-limit-highly-privileged-users"></a>DU-1: Ochrona i ograniczanie użytkowników o wysokim poziomie uprawnień

**Wskazówki**: najważniejsze wbudowane role są Azure Active Directory (Azure AD) są administratorami globalnymi i administrator ról uprzywilejowanych jako użytkownicy przypisani do tych dwóch ról mogą delegować role administratorów:

- Administrator globalny/administrator firmy: Użytkownicy z tą rolą mają dostęp do wszystkich funkcji administracyjnych w usłudze Azure AD, a także usług korzystających z tożsamości usługi Azure AD.

- Administrator ról uprzywilejowanych: Użytkownicy z tą rolą mogą zarządzać przypisaniami ról w Azure Active Directory (Azure AD), a także w Azure AD Privileged Identity Management (PIM). Ponadto ta rola umożliwia zarządzanie wszystkimi aspektami jednostek PIM i administracyjnych.

Usługa Azure Signal Service ma wbudowane role o wysokim poziomie uprawnień. Ogranicz liczbę kont lub ról z wysokim poziomem uprawnień i Chroń usługę Azure AD na poziomie podwyższonego poziomu, ponieważ użytkownicy z tym uprawnieniem mogą bezpośrednio lub pośrednio odczytywać i modyfikować każdy zasób w środowisku platformy Azure.

Można włączyć uprzywilejowany dostęp just-in-time (JIT) do zasobów platformy Azure i usługi Azure AD przy użyciu usługi Azure AD Privileged Identity Management (PIM). Dostęp JIT polega na przyznawaniu uprawnień tymczasowych do wykonywania zadań uprzywilejowanych tylko wtedy, gdy użytkownicy ich potrzebują. Usługa PIM może również generować alerty zabezpieczeń w przypadku podejrzanych lub niebezpiecznych działań w ramach organizacji usługi Azure AD.

- [Współautor sygnalizującego](../role-based-access-control/built-in-roles.md#signalr-contributor)

- [Uprawnienia ról administratora w usłudze Azure AD](/azure/active-directory/users-groups-roles/directory-assign-admin-roles)

- [Używanie alertów zabezpieczeń usługi Azure Privileged Identity Management](../active-directory/privileged-identity-management/pim-how-to-configure-security-alerts.md)

- [Zabezpieczanie uprzywilejowanego dostępu dla wdrożeń hybrydowych i wdrożeń w chmurze w usłudze Azure AD](/azure/active-directory/users-groups-roles/directory-admin-roles-secure)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2: ograniczanie dostępu administracyjnego do systemów o krytycznym znaczeniu dla firmy

**Wskazówki**: usługa Azure sygnalizująca korzysta z kontroli dostępu opartej na rolach (Azure RBAC), aby odizolować dostęp do systemów o krytycznym znaczeniu dla firmy, ograniczając konta, które mają przyznane uprzywilejowany dostęp do subskrypcji i grup zarządzania, w których się znajdują.

Upewnij się, że ograniczono również dostęp do systemów zarządzania, tożsamości i zabezpieczeń, które mają dostęp administracyjny do krytycznych dostępu do firmy, takich jak kontrolery domena usługi Active Directory, narzędzia zabezpieczeń i narzędzia do zarządzania systemem, z agentami zainstalowanymi w systemach krytycznych dla działalności firmy. Osoby atakujące, którzy naruszają te systemy zarządzania i zabezpieczeń, mogą natychmiast weaponize je w celu naruszenia krytycznych zasobów firmy.

Wszystkie typy kontroli dostępu powinny być wyrównane do strategii segmentacji przedsiębiorstwa w celu zapewnienia spójnej kontroli dostępu.

- [Składniki platformy Azure i Model referencyjny](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Dostęp do grupy zarządzania](../governance/management-groups/overview.md#management-group-access)

- [Administratorzy subskrypcji platformy Azure](../cost-management-billing/manage/add-change-subscription-administrator.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="pa-3-review-and-reconcile-user-access-regularly"></a>DU-3: Regularny przegląd i uzgadnianie dostępu użytkowników

**Wskazówki**: regularnie Przeglądaj konta użytkowników i przypisywanie dostępu, aby upewnić się, że konta i ich poziom dostępu są prawidłowe.

Usługa Azure Signal Service korzysta z kont Azure Active Directory (Azure AD) do zarządzania zasobami, przeglądania kont użytkowników i przypisywania dostępu regularnie, aby zapewnić, że konta i ich dostęp są prawidłowe. Za pomocą przeglądów dostępu w usłudze Azure AD można przeglądać członkostwa w grupach, uzyskiwać dostęp do aplikacji dla przedsiębiorstw i przypisań ról. Raportowanie usługi Azure AD może zapewnić dzienniki, aby ułatwić odnajdywanie starych kont. Możesz również użyć Azure AD Privileged Identity Management, aby utworzyć przepływ pracy raportu przeglądu dostępu, aby ułatwić proces recenzowania.

Role wbudowane w usłudze Azure Signal Service obejmują:

- Współautor sygnalizującego
- Czytnik AccessKeyer sygnalizujący

Ponadto usługa Azure Privileged Identity Management może być również skonfigurowana do zgłaszania alertów w przypadku tworzenia zbyt dużej liczby kont administratorów oraz do identyfikowania kont administratorów, które są przestarzałe lub nieprawidłowo skonfigurowane.

- [Tworzenie przeglądu dostępu do ról zasobów platformy Azure w Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-resource-roles-start-access-review.md)

- [Jak korzystać z przeglądów tożsamości i dostępu w usłudze Azure AD](../active-directory/governance/access-reviews-overview.md)

- [Współautor sygnalizującego](../role-based-access-control/built-in-roles.md#signalr-contributor)

- [Czytnik AccessKeyer sygnalizujący](../role-based-access-control/built-in-roles.md#signalr-accesskey-reader)

-

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="pa-4-set-up-emergency-access-in-azure-ad"></a>PA-4: Konfigurowanie dostępu awaryjnego w usłudze Azure AD

**Wskazówki**: usługa Azure Signal Service używa usługi Azure Active Directory (Azure AD) do zarządzania swoimi zasobami. Aby zapobiec przypadkowemu zablokowaniu organizacji usługi Azure AD, skonfiguruj konto dostępu awaryjnego w celu uzyskania dostępu, gdy nie można używać normalnych kont administracyjnych. Konta dostępu awaryjnego są zwykle wysoce uprzywilejowane i nie powinny być przypisane do konkretnych osób. Konta dostępu awaryjnego są ograniczone do sytuacji awaryjnych lub "szkła Break", w których normalne konta administracyjne nie mogą być używane.

Należy upewnić się, że poświadczenia (takie jak hasło, certyfikat lub karta inteligentna) dla kont dostępu awaryjnego są bezpieczne i znane tylko osobom, które są upoważnione do ich używania tylko w sytuacji awaryjnej.

- [Zarządzanie kontami dostępu awaryjnego w usłudze Azure AD](/azure/active-directory/users-groups-roles/directory-emergency-access)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="pa-5-automate-entitlement-management"></a>PA-5: Automatyzowanie zarządzania uprawnieniami 

**Wskazówki**: usługa Azure Signal Service jest zintegrowana z usługą Azure Active Directory (Azure AD) w celu zarządzania swoimi zasobami. Użyj funkcji zarządzania prawami usługi Azure AD, aby zautomatyzować przepływy pracy żądania dostępu, w tym przydziały dostępu, przeglądy i wygaśnięcie. Obsługiwane jest również podwójne lub wieloetapowe zatwierdzenie.

- [Co to są przeglądy dostępu do usługi Azure AD](../active-directory/governance/access-reviews-overview.md)

- [Co to jest zarządzanie prawami w usłudze Azure AD](../active-directory/governance/entitlement-management-overview.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="pa-6-use-privileged-access-workstations"></a>PA-6: używanie uprzywilejowanych stacji roboczych dostępu

**Wskazówki**: zabezpieczone, izolowane stacje robocze mają kluczowe znaczenie dla bezpieczeństwa ról poufnych, takich jak Administratorzy, deweloperzy i krytyczne operatory usług. Używaj wysoce bezpiecznych stacji roboczych użytkowników i/lub Azure bastionu na potrzeby zadań administracyjnych. Użyj Azure Active Directory, usługi Microsoft Defender Advanced Threat Protection (ATP) i/lub Microsoft Intune do wdrożenia bezpiecznej i zarządzanej stacji roboczej użytkownika na potrzeby zadań administracyjnych. Zabezpieczone stacje robocze mogą być zarządzane centralnie, aby wymusić bezpieczną konfigurację, w tym silne uwierzytelnianie, oprogramowanie i sprzętowe linie bazowe, ograniczony dostęp logiczny i sieciowy.

- [Informacje na temat stacji roboczych uprzywilejowanego dostępu](../active-directory/devices/concept-azure-managed-workstation.md)

- [Wdrażanie stacji roboczej z dostępem uprzywilejowanym](../active-directory/devices/howto-azure-managed-workstation.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="pa-7-follow-just-enough-administration-least-privilege-principle"></a>DU-7: Przestrzeganie podejścia wystarczającego zakresu administracji (zasada stosowania najniższych uprawnień) 

**Wskazówki**: usługa sygnalizująca jest zintegrowana z kontrolą dostępu opartą na rolach (Azure RBAC) na potrzeby zarządzania swoimi zasobami. Usługa Azure RBAC umożliwia zarządzanie dostępem do zasobów platformy Azure za pomocą przypisań ról. Przypisz te role do użytkowników, grup jednostek usługi i zarządzanych tożsamości. Wstępnie zdefiniowane wbudowane role istnieją dla określonych zasobów, a te role można spisować lub odpytać za pomocą narzędzi takich jak interfejs wiersza polecenia platformy Azure, Azure PowerShell lub Azure Portal. 

Uprawnienia przypisywane do zasobów z użyciem kontroli dostępu opartej na rolach platformy Azure powinny być zawsze ograniczone do tego, co jest wymagane przez te role. To uzupełnia podejście dostępu just in time (JIT) usługi Azure AD Privileged Identity Management (PIM) i powinno być okresowo weryfikowane.

Wbudowane role w usłudze sygnalizującego:

- Współautor sygnalizującego
- Czytnik AccessKeyer sygnalizujący

Przy użyciu wbudowanych ról można przydzielić uprawnienia i tworzyć role niestandardowe tylko wtedy, gdy jest to wymagane.

- [Co to jest kontrola dostępu oparta na rolach (Azure RBAC)](../role-based-access-control/overview.md)

- [Jak skonfigurować kontrolę RBAC na platformie Azure](../role-based-access-control/role-assignments-portal.md)

- [Jak korzystać z przeglądów tożsamości i dostępu w usłudze Azure AD](../active-directory/governance/access-reviews-overview.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

## <a name="data-protection"></a>Ochrona danych

*Aby uzyskać więcej informacji, zobacz [Test porównawczy zabezpieczeń platformy Azure: Ochrona danych](/azure/security/benchmarks/security-controls-v2-data-protection).*

### <a name="dp-2-protect-sensitive-data"></a>OD-2: Ochrona poufnych danych

**Wskazówki**: Ochrona poufnych danych przez ograniczenie dostępu przy użyciu Access Control opartej na rolach (Azure RBAC), kontroli dostępu opartej na sieci i określonych kontrolek w usługach platformy Azure (takich jak szyfrowanie w SQL i innych bazach danych).

Aby zapewnić spójną kontrolę dostępu, wszystkie typy kontroli dostępu powinny być dostosowane do strategii segmentacji przedsiębiorstwa. Strategię segmentacji przedsiębiorstwa powinna być również oparta na lokalizacji poufnych lub krytycznych danych i systemów.

W odniesieniu do platformy podstawowej zarządzanej przez firmę Microsoft, firma Microsoft traktuje całą zawartość kliencką jako poufną i zapewnia ochronę przed utratą i narażeniem danych klientów. Aby zapewnić bezpieczeństwo danych klientów na platformie Azure, firma Microsoft zaimplementowała pewne domyślne mechanizmy kontroli i możliwości ochrony danych.

- [Kontrola dostępu oparta na rolach na platformie Azure (RBAC)](../role-based-access-control/overview.md)

- [Informacje na temat ochrony danych klientów na platformie Azure](../security/fundamentals/protection-customer-data.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Udostępnione

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

### <a name="am-2-ensure-security-team-has-access-to-asset-inventory-and-metadata"></a>AM-2: Upewnij się, że zespół ds. zabezpieczeń ma dostęp do spisu zasobów i metadanych

**Wskazówki**: Zastosuj znaczniki do zasobów platformy Azure, grup zasobów i subskrypcji, aby logicznie zorganizować je w taksonomię. Każdy tag składa się z nazwy i pary wartości. Na przykład można zastosować nazwę „Środowisko” i wartość „Produkcyjne” do wszystkich zasobów w środowisku produkcyjnym.

- [Jak tworzyć zapytania za pomocą Eksploratora Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

- [Azure Security Center zarządzanie spisem zasobów](../security-center/asset-inventory.md)

- [Aby uzyskać więcej informacji na temat tagowania elementów zawartości, zobacz Przewodnik po nazwie i znakowaniu zasobów](https://docs.microsoft.com/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=/azure/azure-resource-manager/management/toc.json)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="am-3-use-only-approved-azure-services"></a>AM-3: Używaj tylko zatwierdzonych usług platformy Azure

**Wskazówki**: Użyj Azure Policy do inspekcji i ograniczania usług, które użytkownicy mogą inicjować w Twoim środowisku. Użyj grafu zasobów platformy Azure do wykonywania zapytań dotyczących zasobów i odnajdywania ich w ramach subskrypcji. Za pomocą Azure Monitor można tworzyć reguły wyzwalające alerty w przypadku wykrycia niezatwierdzonej usługi.

- [Jak skonfigurować Azure Policy i zarządzać nimi](../governance/policy/tutorials/create-and-manage.md)

- [Jak odmówić określonego typu zasobu za pomocą Azure Policy](../governance/policy/samples/built-in-policies.md#general)

- [Jak tworzyć zapytania za pomocą Eksploratora Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="am-4-ensure-security-of-asset-lifecycle-management"></a>ZZ-4: zapewnienie bezpieczeństwa zarządzania cyklem życia zasobów

**Wskazówki**: Utwórz lub zaktualizuj zasady zabezpieczeń, które dotyczą procesów zarządzania cyklem życia zasobów dla modyfikacji o dużym wpływie. Te modyfikacje obejmują zmiany, ale nie są ograniczone do: dostawcy tożsamości i dostępu, czułość danych, konfiguracja sieci i przypisanie uprawnień administracyjnych. W usłudze Azure Signal Service wprowadzono następujące zmiany: Wygeneruj ponownie klucz dostępu, Utwórz/zaktualizuj prywatny punkt końcowy i Zarządzaj kontrolą dostępu do sieci.

Usuń zasoby platformy Azure, gdy nie są już potrzebne.

- [Usuwanie grupy zasobów i zasobów platformy Azure](../azure-resource-manager/management/delete-resource-group.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>AM-5: ograniczanie możliwości korzystania przez użytkowników z Azure Resource Manager

**Wskazówki**: Użyj dostępu warunkowego platformy Azure, aby ograniczyć możliwość korzystania przez użytkowników z Menedżera zasobów platformy Azure przez skonfigurowanie "blokowania dostępu" dla aplikacji "Microsoft Azure Management".

- [Jak skonfigurować dostęp warunkowy, aby blokować dostęp do usługi Azure Resources](../role-based-access-control/conditional-access-azure-management.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

## <a name="logging-and-threat-detection"></a>Rejestrowanie i wykrywanie zagrożeń

*Aby uzyskać więcej informacji, zobacz [Test porównawczy zabezpieczeń platformy Azure: Rejestrowanie i wykrywanie zagrożeń](/azure/security/benchmarks/security-controls-v2-logging-threat-protection).*

### <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>RZ-2: włączanie wykrywania zagrożeń na potrzeby zarządzania tożsamościami i dostępem na platformie Azure

**Wskazówki**: Azure Active Directory (Azure AD) udostępniają następujące dzienniki użytkowników, które mogą być wyświetlane w usłudze Azure AD Reporting lub zintegrowane z Azure Monitorm, wskaźnikiem na platformie Azure lub innymi narzędziami Siem/monitorowania, aby uzyskać bardziej zaawansowane przypadki monitorowania i analizy:

- Logowanie — raport Sign-n zawiera informacje na temat użycia zarządzanych aplikacji i działań związanych z logowaniem użytkowników.

- Dzienniki inspekcji — udostępnia możliwość śledzenia wszystkich zmian wprowadzanych przez różne funkcje usługi Azure AD za pomocą dzienników. Działania umieszczane w dziennikach inspekcji to na przykład zmiany wprowadzone w dowolnych zasobach usługi Azure AD, takie jak dodawanie lub usuwanie użytkowników, aplikacji, grup, ról i zasad.

- Ryzykowne logowania — ryzykowne logowanie jest wskaźnikiem próby logowania, które mogło zostać wykonane przez kogoś, kto nie jest uprawnionym właścicielem konta użytkownika.
- Użytkownicy oflagowani w związku z ryzykiem — ryzykowny użytkownik jest wskaźnikiem konta użytkownika, którego bezpieczeństwo mogło zostać naruszone.

Usługa Azure Security Center może również wysyłać alerty dotyczące określonych podejrzanych działań, takich jak nadmierna liczba nieudanych prób uwierzytelnienia, czy przestarzałe konta w subskrypcji. Oprócz podstawowego monitorowania higieny zabezpieczeń moduł ochrony przed zagrożeniami usługi Azure Security Center może również zbierać bardziej szczegółowe alerty zabezpieczeń z poszczególnych zasobów obliczeniowych platformy Azure (maszyn wirtualnych, kontenerów, usługi App Service), zasobów danych (bazy danych SQL i magazynu) oraz warstw usługi platformy Azure. Ta funkcja umożliwia wgląd w anomalie kont wewnątrz poszczególnych zasobów.

- [Raporty dotyczące inspekcji w usłudze Azure Active Directory](../active-directory/reports-monitoring/concept-audit-logs.md)

- [Włączanie usługi Azure Identity Protection](../active-directory/identity-protection/overview-identity-protection.md)

- [Ochrona przed zagrożeniami w usłudze Azure Security Center](/azure/security-center/threat-protection)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="lt-3-enable-logging-for-azure-network-activities"></a>LT-3: Włączanie rejestrowania dla aktywności sieci platformy Azure

**Wskazówki**: usługa Azure signaler nie jest przeznaczona do wdrożenia w sieciach wirtualnych. z tego powodu nie można włączyć rejestrowania przepływu sieciowych grup zabezpieczeń, skierować ruch przez zaporę lub przechwycić pakiety.

Jednak usługa Azure Signal rejestruje ruch sieciowy, który przetwarza na potrzeby dostępu klientów. Włącz dzienniki zasobów w ramach wdrożonych zasobów oferty i skonfiguruj te dzienniki do wysłania do konta magazynu w celu długoterminowego przechowywania i inspekcji.

- [Dzienniki zasobów usługi Azure Signal Service](signalr-howto-diagnostic-logs.md)

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4: Włączanie rejestrowania dla zasobów platformy Azure

**Wskazówki**: dzienniki aktywności, które są automatycznie dostępne, zawierają wszystkie operacje zapisu (Put, post, Delete) dla zasobów usługi Azure Signal Service, z wyjątkiem operacji odczytu (Get). Dzienników aktywności można użyć do znalezienia błędu podczas rozwiązywania problemów lub do monitorowania sposobu, w jaki użytkownik w organizacji zmodyfikował zasób.

Włącz dzienniki zasobów platformy Azure dla usługi Azure Signal Service. Za pomocą Azure Security Center i Azure Policy można włączyć zbieranie danych dzienników zasobów i dzienników. Te dzienniki mogą być niezbędne do późniejszego badania zdarzeń związanych z bezpieczeństwem i wykonywania ćwiczeń śledczej.

- [Jak zbierać dzienniki platformy i metryki za pomocą Azure Monitor](../azure-monitor/platform/diagnostic-settings.md)

- [Informacje o rejestrowaniu i różnych typach dzienników na platformie Azure](../azure-monitor/platform/platform-logs-overview.md)

- [Dzienniki zasobów usługi Azure Signal Service](signalr-howto-diagnostic-logs.md)

- [Omówienie zbierania danych Azure Security Center](../security-center/security-center-enable-data-collection.md)

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5: scentralizowane zarządzanie dziennikami zabezpieczeń i analiza

**Wskazówki**: scentralizowany magazyn rejestrowania i analiza w celu włączenia korelacji. Dla każdego źródła dziennika upewnij się, że masz przypisanego właściciela danych, wskazówki dotyczące dostępu, lokalizację magazynu, narzędzia używane do przetwarzania i uzyskiwania dostępu do danych oraz wymagania dotyczące przechowywania danych.

Upewnij się, że masz integrację dzienników aktywności platformy Azure z centralnym logowaniem. Pozyskiwanie dzienników za pośrednictwem Azure Monitor do agregowania danych zabezpieczeń wygenerowanych przez urządzenia, zasoby sieciowe i inne systemy zabezpieczeń. W Azure Monitor używać Log Analytics obszarów roboczych do wykonywania zapytań i przeprowadzania analiz oraz używania kont usługi Azure Storage do przechowywania długoterminowego i archiwizowania.

Ponadto należy włączyć i dołączyć dane do usługi Azure wskaźnikowej lub systemu informacji o zabezpieczeniach i zarządzania zdarzeniami innych firm (SIEM).

Wiele organizacji decyduje się na korzystanie z platformy Azure na potrzeby danych "gorąca", które są często używane, i usługi Azure Storage dla danych "zimnych", które są rzadziej używane.

- [Jak zbierać dzienniki platformy i metryki za pomocą Azure Monitor](../azure-monitor/platform/diagnostic-settings.md)

- [Jak dołączyć wskaźnik na platformie Azure](../sentinel/quickstart-onboard.md)

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

### <a name="pv-1-establish-secure-configurations-for-azure-services"></a>WA-1: Ustanów bezpieczne konfiguracje dla usług platformy Azure 

**Wskazówki**: usługa Azure Signal jest obsługiwana poniżej zasad specyficznych dla usługi, które są dostępne w Azure Security Center na potrzeby inspekcji i wymuszania konfiguracji zasobów platformy Azure. Tę konfigurację można skonfigurować w Azure Security Center lub z inicjatywami Azure Policy.

Plany platformy Azure umożliwiają Automatyzowanie wdrażania i konfigurowania usług i środowisk aplikacji, w tym szablonów usługi Azure Resources, kontroli dostępu opartej na rolach (Azure RBAC) i zasad w ramach jednej definicji planu.

- [Praca z zasadami zabezpieczeń w Azure Security Center](../security-center/tutorial-security-policy.md)

- [Inspekcja zgodności zasobów usługi Azure sygnalizacyjnej przy użyciu Azure Policy](signalr-howto-azure-policy.md)

- [Samouczek — Tworzenie zasad i zarządzanie nimi w celu wymuszenia zgodności](../governance/policy/tutorials/create-and-manage.md) 

- [Azure Blueprints](../governance/blueprints/overview.md)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="pv-2-sustain-secure-configurations-for-azure-services"></a>PV-2: utrzymywanie bezpiecznych konfiguracji dla usług platformy Azure

**Wskazówki**: Użyj Azure Security Center do monitorowania linii bazowej konfiguracji i wymuszania przy użyciu Azure Policy [Odmów] i [Wdróż, jeśli nie istnieje], aby wymusić bezpieczną konfigurację usługi Azure Signal Service. Zasady usługi sygnałów platformy Azure obejmują:

Dodatkowe informacje są dostępne w linkach, do których istnieją odwołania.

- [Inspekcja zgodności zasobów usługi Azure sygnalizacyjnej przy użyciu Azure Policy](signalr-howto-azure-policy.md)

- [Zrozumienie efektów Azure Policy](../governance/policy/concepts/effects.md)

- [Tworzenie zasad i zarządzanie nimi w celu wymuszania zgodności](../governance/policy/tutorials/create-and-manage.md)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="pv-3-establish-secure-configurations-for-compute-resources"></a>WA-3: Ustanów bezpieczne konfiguracje dla zasobów obliczeniowych

**Wskazówki**: Użyj Azure Security Center i Azure Policy do ustanowienia bezpiecznych konfiguracji w usłudze Azure Signal Service.

- [Jak monitorować zalecenia dotyczące Azure Security Center](../security-center/security-center-recommendations.md)

- [Zalecenia dotyczące zabezpieczeń — przewodnik referencyjny](../security-center/recommendations-reference.md)

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

## <a name="backup-and-recovery"></a>Kopia zapasowa i odzyskiwanie

*Aby uzyskać więcej informacji, zobacz temat [Azure Security test — tworzenie kopii zapasowych i odzyskiwanie](/azure/security/benchmarks/security-controls-v2-backup-recovery).*

### <a name="br-4-mitigate-risk-of-lost-keys"></a>BR-4: zmniejszanie ryzyka utraty kluczy

**Wskazówki**: Upewnij się, że masz środki, aby zapobiec utracie kluczy i je odzyskać. Włącz trwałe usuwanie i przeczyszczanie ochrony w Azure Key Vault, aby chronić klucze przed przypadkowym lub złośliwym usunięciem.

- [Jak włączyć funkcję usuwania nietrwałego i przeczyszczania w programie Key Vault](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

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

Więcej informacji można znaleźć w łączach, do których istnieją odwołania.

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
