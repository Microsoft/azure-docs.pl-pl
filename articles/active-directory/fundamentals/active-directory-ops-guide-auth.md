---
title: Azure Active Directory authentication management operations reference guide (Przewodnik po operacjach zarządzania uwierzytelnianiem przy użyciu uwierzytelniania)
description: W tym przewodniku informacyjnym opisano kontrole i akcje, które należy podjąć w celu zabezpieczenia zarządzania uwierzytelnianiem
services: active-directory
author: martincoetzer
manager: daveba
tags: azuread
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: fundamentals
ms.date: 10/31/2019
ms.author: martinco
ms.openlocfilehash: 26b5331aa9242978f0f097c8e90bc807fc65f745
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107531953"
---
# <a name="azure-active-directory-authentication-management-operations-reference-guide"></a>Azure Active Directory authentication management operations reference guide (Przewodnik po operacjach zarządzania uwierzytelnianiem przy użyciu uwierzytelniania)

W tej sekcji przewodnika po operacjach usługi [Azure AD](active-directory-ops-guide-intro.md) opisano kontrole i akcje, które należy podjąć, aby zabezpieczyć poświadczenia i zarządzać nimi, zdefiniować środowisko uwierzytelniania, delegować przypisanie, zmierzyć użycie i zdefiniować zasady dostępu na podstawie bezpieczeństwa przedsiębiorstwa.

> [!NOTE]
> Te rekomendacje są aktualne w dniu opublikowania, ale mogą ulec zmianie wraz z czasem. Organizacje powinny stale oceniać swoje praktyki dotyczące tożsamości, gdy produkty i usługi firmy Microsoft ewoluują wraz z czasem.

## <a name="key-operational-processes"></a>Kluczowe procesy operacyjne

### <a name="assign-owners-to-key-tasks"></a>Przypisywanie właścicieli do kluczowych zadań

Zarządzanie Azure Active Directory wymaga ciągłego wykonywania kluczowych zadań operacyjnych i procesów, które mogą nie być częścią projektu realizacji. Skonfigurowanie tych zadań w celu zoptymalizowania środowiska jest nadal ważne. Do najważniejszych zadań i ich zalecanych właścicieli należą:

| Zadanie | Właściciel |
| :- | :- |
| Zarządzanie cyklem życia konfiguracji logowania jednokrotnego (SSO) w usłudze Azure AD | Zespół operacyjny IAM |
| Projektowanie zasad dostępu warunkowego dla aplikacji usługi Azure AD | Zespół architektury InfoSec |
| Archiwizowanie działań logowania w systemie SIEM | Zespół operacyjny programu InfoSec |
| Archiwizowanie zdarzeń o ryzyku w systemie SIEM | Zespół operacyjny programu InfoSec |
| Triage and investigate security reports (Ujednanie raportów dotyczących zabezpieczeń i ich badanie) | Zespół operacyjny programu InfoSec |
| Triage and investigate risk events (Ocena i badanie zdarzeń o ryzyku) | Zespół operacyjny programu InfoSec |
| Ocena i badanie użytkowników oflagowanych w przypadku raportów o ryzyku i lukach w zabezpieczeniach z Azure AD Identity Protection | Zespół operacyjny programu InfoSec |

> [!NOTE]
> Azure AD Identity Protection wymaga licencji Azure AD — wersja Premium P2. Aby znaleźć odpowiednią licencję dla swoich wymagań, zobacz Porównanie ogólnie dostępnych funkcji Azure AD — wersja Bezpłatna [i Azure AD — wersja Premium wersji](https://azure.microsoft.com/pricing/details/active-directory/).

Podczas przeglądania listy może okazać się konieczne przypisanie właściciela do zadań, w przypadku których brakuje właściciela, lub dostosowanie własności zadań do właścicieli, którzy nie są dostosowani do powyższych zaleceń.

#### <a name="owner-recommended-reading"></a>Zalecane przeczytanie przez właściciela

- [Przypisywanie ról administratorów w usłudze Azure Active Directory](../roles/permissions-reference.md)
- [Nadzór na platformie Azure](../../governance/index.yml)

## <a name="credentials-management"></a>Zarządzanie poświadczeniami

### <a name="password-policies"></a>Zasady dotyczące haseł

Bezpieczne zarządzanie hasłami jest jedną z najważniejszych części zarządzania tożsamościami i dostępem, a często największym celem ataków. Usługa Azure AD obsługuje kilka funkcji, które mogą pomóc zapobiec pomyślnemu atakowi.

Skorzystaj z poniższej tabeli, aby znaleźć zalecane rozwiązanie problemu, który należy rozwiązać:

| Problem | Zalecenie |
| :- | :- |
| Brak mechanizmu ochrony przed słabymi hasłami | Włączanie samoobsługowego [resetowania haseł (SSPR)](../authentication/concept-sspr-howitworks.md) i ochrony [haseł w usłudze](../authentication/concept-password-ban-bad-on-premises.md) Azure AD |
| Brak mechanizmu wykrywania ujawnionych haseł | Włączanie [synchronizacji skrótów haseł](../hybrid/how-to-connect-password-hash-synchronization.md) w celu uzyskania szczegółowych informacji |
| Używanie AD FS i nie można przejść do uwierzytelniania zarządzanego | Włączanie [AD FS inteligentnej blokady ekstranetu](/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-smart-lockout-protection) i/lub [inteligentnej blokady usługi Azure AD](../authentication/howto-password-smart-lockout.md) |
| Zasady haseł wykorzystują reguły oparte na złożoności, takie jak długość, wiele zestawów znaków lub wygaśnięcie | Rozważ ponownie rozwiązania zalecane przez firmę [Microsoft](https://www.microsoft.com/research/publication/password-guidance/?from=http%3A%2F%2Fresearch.microsoft.com%2Fpubs%2F265143%2Fmicrosoft_password_guidance.pdf) i zmień swoje podejście na zarządzanie hasłami i wd wdrażaj ochronę [haseł w usłudze Azure AD.](../authentication/concept-password-ban-bad.md) |
| Użytkownicy nie są zarejestrowani do korzystania z uwierzytelniania wieloskładnikowego (MFA) | [Zarejestruj wszystkie informacje o zabezpieczeniach](../identity-protection/howto-identity-protection-configure-mfa-policy.md) użytkownika, aby można było ich użyć jako mechanizmu weryfikacji tożsamości użytkownika wraz z jego hasłem |
| Nie ma odwołania haseł w zależności od ryzyka związanego z użytkownikiem | Wdrażanie zasad ryzyka [związanego z użytkownikiem usługi](../identity-protection/howto-identity-protection-configure-risk-policies.md) Azure AD Identity Protection w celu wymuszania zmian haseł w przypadku wycieku poświadczeń przy użyciu funkcji SSPR |
| Nie istnieje mechanizm inteligentnej blokady chroniący złośliwe uwierzytelnianie przed złośliwymi użytkownikami, którzy pochodzą ze zidentyfikowanych adresów IP | Wdrażanie uwierzytelniania zarządzanego przez chmurę przy użyciu synchronizacji skrótów haseł lub [uwierzytelniania pass-through](../hybrid/how-to-connect-pta-quick-start.md) (PTA) |

#### <a name="password-policies-recommended-reading"></a>Zalecane odczytywanie zasad haseł

- [Usługa Azure AD AD FS najlepsze rozwiązania: Obrona przed atakami z atakiem systemowym na hasła — Enterprise Mobility + Security](https://cloudblogs.microsoft.com/enterprisemobility/2018/03/05/azure-ad-and-adfs-best-practices-defending-against-password-spray-attacks/)

### <a name="enable-self-service-password-reset-and-password-protection"></a>Włączanie samoobsługowego resetowania haseł i ochrony hasłem

Użytkownicy, którzy muszą zmienić lub zresetować swoje hasła, są jednym z największych źródeł ilości i kosztów rozmów z kosztami pomocy technicznej. Oprócz kosztów zmiana hasła jako narzędzia w celu ograniczenia ryzyka związanego z użytkownikiem jest podstawowym krokiem w zakresie poprawy poziomu bezpieczeństwa organizacji.

Zaleca się co najmniej wdrożenie samoobsługowego resetowania hasła [](../authentication/concept-sspr-howitworks.md) (SSPR) usługi Azure AD i lokalnej ochrony hasłem w [celu](../authentication/howto-password-ban-bad-on-premises-deploy.md) wykonania:

- Zamieć rozmowy z działu pomocy technicznej.
- Zastąp użycie haseł tymczasowych.
- Zastąp wszystkie istniejące, samoobsługowe rozwiązania do zarządzania hasłami, które opiera się na rozwiązaniu lokalnym.
- [Wyeliminuj słabe](../authentication/concept-password-ban-bad.md) hasła w organizacji.

> [!NOTE]
> W przypadku organizacji Azure AD — wersja Premium subskrypcją P2 zaleca się wdrożenie funkcji SSPR i użycie jej w ramach zasad ryzyka związanego z [użytkownikiem usługi Identity Protection.](../identity-protection/howto-identity-protection-configure-risk-policies.md)

### <a name="strong-credential-management"></a>Silne zarządzanie poświadczeniami

Hasła same w sobie nie są wystarczająco bezpieczne, aby uniemożliwić złym aktorom uzyskanie dostępu do środowiska. Każdy użytkownik z uprzywilejowanym kontem musi mieć co najmniej włączoną obsługę uwierzytelniania wieloskładnikowego (MFA). W idealnym przypadku należy włączyć [rejestrację połączoną](../authentication/concept-registration-mfa-sspr-combined.md) i wymagać od wszystkich użytkowników zarejestrowania się w celu korzystania z uwierzytelniania wieloskładnikowego i funkcji SSPR przy użyciu [połączonego procesu rejestracji.](../user-help/security-info-setup-signin.md) W końcu zalecamy przyjęcie strategii [](../authentication/concept-resilient-controls.md) w celu zapewnienia odporności w celu zmniejszenia ryzyka blokady z powodu nieprzewidzianych okoliczności.

![Połączony przepływ obsługi użytkownika](./media/active-directory-ops-guide/active-directory-ops-img4.png)

### <a name="on-premises-outage-authentication-resiliency"></a>Odporność lokalnego uwierzytelniania na awarie

Oprócz zalet prostoty i włączania wykrywania wycieków poświadczeń, usługa Azure AD Password Hash Sync (PHS) i usługa Azure AD MFA umożliwiają użytkownikom dostęp do aplikacji SaaS i usługi Microsoft 365 pomimo lokalnych outages spowodowanych cyberatakami, takimi jak [NotPetya.](https://www.microsoft.com/security/blog/2018/02/05/overview-of-petya-a-rapid-cyberattack/) Istnieje również możliwość włączenia funkcji PHS w połączeniu z federacją. Włączenie funkcji PHS umożliwia rezerwowe uwierzytelnianie, gdy usługi federacyjnie nie są dostępne.

Jeśli twoja organizacja lokalna nie ma strategii odporności na awarie lub ma strategię, która nie jest zintegrowana z usługą Azure AD, należy wdrożyć usługę Azure AD PHS i zdefiniować plan odzyskiwania po awarii, który obejmuje phS. Włączenie usługi Azure AD PHS umożliwi użytkownikom uwierzytelnianie w usłudze Azure AD, jeśli lokalna usługa Active Directory niedostępna.

![przepływ synchronizacji skrótów haseł](./media/active-directory-ops-guide/active-directory-ops-img5.png)

Aby lepiej zrozumieć opcje uwierzytelniania, zobacz Choose the right authentication method for your Azure Active Directory hybrid identity solution (Wybieranie właściwej metody uwierzytelniania dla rozwiązania do Azure Active Directory [tożsamości hybrydowej).](../hybrid/choose-ad-authn.md)

### <a name="programmatic-usage-of-credentials"></a>Programowe użycie poświadczeń

Skrypty usługi Azure AD korzystające z programu PowerShell lub aplikacji korzystających z interfejsu API Microsoft Graph wymagają bezpiecznego uwierzytelniania. Słabe zarządzanie poświadczeniami podczas wykonywania tych skryptów i narzędzi zwiększa ryzyko kradzieży poświadczeń. Jeśli używasz skryptów lub aplikacji, które opierają się na zakodowanych hasłach lub monitach o hasło, najpierw przejrzyj hasła w plikach konfiguracji lub kodzie [](../reports-monitoring/tutorial-access-api-with-certificates.md) źródłowym, a następnie zastąp te zależności i używaj tożsamości zarządzanych platformy Azure, uwierzytelniania Integrated-Windows lub certyfikatów zawsze, gdy jest to możliwe. W przypadku aplikacji, w których poprzednie rozwiązania nie są możliwe, rozważ [użycie](https://azure.microsoft.com/services/key-vault/)Azure Key Vault .

Jeśli ustalisz, że istnieją jednostki usługi z poświadczeniami hasła i nie masz pewności, jak te poświadczenia hasła są zabezpieczone przez skrypty lub aplikacje, skontaktuj się z właścicielem aplikacji, aby lepiej zrozumieć wzorce użycia.

Firma Microsoft zaleca również skontaktowanie się z właścicielami aplikacji w celu zrozumienia wzorców użycia, jeśli istnieją jednostki usługi z poświadczeniami haseł.

## <a name="authentication-experience"></a>Środowisko uwierzytelniania

### <a name="on-premises-authentication"></a>Uwierzytelnianie lokalne

Federated Authentication with Integrated Windows Authentication (IWA) or Seamless Single Sign-On managed authentication with password hash sync or pass-through authentication is the best user experience when inside the corporate network with line-of-sight to on-to-sight to on-premises domain controllers (Uwierzytelnianie federacyjna za pomocą funkcji synchronizacji skrótów haseł lub uwierzytelniania pass-through) to najlepsze środowisko użytkownika w sieci firmowej z lokalnymi kontrolerami domeny. Minimalizuje to monitowanie o poświadczenia i zmniejsza ryzyko wystąpienia ataków wyłudzających informacje przez użytkowników. Jeśli już używasz uwierzytelniania zarządzanego przez chmurę z phS lub PTA, ale użytkownicy nadal muszą wpisać swoje hasło podczas uwierzytelniania lokalnego, należy natychmiast wdrożyć bezproblemowe [logowanie jednokrotne.](../hybrid/how-to-connect-sso.md) Z drugiej strony, jeśli obecnie masz plany migracji do uwierzytelniania zarządzanego przez chmurę, musisz wdrożyć bezproblemowe logowanie jednokrotne w ramach projektu migracji.

### <a name="device-trust-access-policies"></a>Zasady dostępu do zaufania urządzeń

Podobnie jak użytkownik w organizacji, urządzenie jest podstawową tożsamością, którą chcesz chronić. Tożsamości urządzenia można użyć do ochrony zasobów w dowolnym momencie i z dowolnej lokalizacji. Uwierzytelnianie urządzenia i ewidencjonowanie jego typu zaufania zwiększa poziom bezpieczeństwa i użyteczność dzięki:

- Unikanie problemów, na przykład z uwierzytelniania wieloskładnikowego, gdy urządzenie jest zaufane
- Blokowanie dostępu z niezaufanych urządzeń
- W Windows 10 urządzeń bezproblemowo wyloguj się do [zasobów lokalnych.](../devices/azuread-join-sso.md)

Ten cel można zrealizować, przenosząc tożsamości urządzeń i zarządzając nimi w usłudze Azure AD przy użyciu jednej z następujących metod:

- Organizacje mogą używać [Microsoft Intune](/intune/what-is-intune) urządzenia i wymuszać zasady zgodności, potwierdzać kondycję urządzenia i ustawiać zasady dostępu warunkowego na podstawie tego, czy urządzenie jest zgodne. Microsoft Intune zarządzać urządzeniami z systemem iOS, komputerami Mac (za pośrednictwem integracji jamf), komputerami z systemem Windows (natywnie przy użyciu usługi Mobile Zarządzanie urządzeniami na platformie Windows 10 i współzawłaszczać zarządzanie z systemem Microsoft Endpoint Configuration Manager) oraz urządzeniami przenośnymi z systemem Android.
- [Hybrydowe dołączanie do usługi Azure AD](../devices/hybrid-azuread-join-managed-domains.md) zapewnia zarządzanie przy użyciu zasad grupy lub Microsoft Endpoint Configuration Manager w środowisku z komputerami przyłączonymi do domeny usługi Active Directory. Organizacje mogą wdrażać środowisko zarządzane za pośrednictwem phS lub PTA z bezproblemowym logowaniem jednokrotnym. Wprowadzenie urządzeń do usługi Azure AD maksymalizuje produktywność użytkowników dzięki funkcji logowania jednokrotnego w chmurze [](../conditional-access/overview.md) i zasobach lokalnych, umożliwiając jednocześnie bezpieczny dostęp do zasobów w chmurze i lokalnych przy użyciu dostępu warunkowego.

Jeśli masz urządzenia z systemem Windows przyłączone do domeny, które nie są zarejestrowane w chmurze, lub urządzenia z systemem Windows przyłączone do domeny, które są zarejestrowane w chmurze, ale bez zasad dostępu warunkowego, należy zarejestrować niezarejestrowane urządzenia i w obu przypadkach użyć hybrydowego dołączania do usługi [Azure AD](../conditional-access/require-managed-devices.md) jako kontroli w zasadach dostępu warunkowego.

![Zrzut ekranu przedstawiający przyznawanie uprawnień w zasadach dostępu warunkowego wymagających urządzenia hybrydowego](./media/active-directory-ops-guide/active-directory-ops-img6.png)

Jeśli zarządzasz urządzeniami za pomocą rozwiązania MDM lub usługi Microsoft Intune, ale nie używasz [](../conditional-access/require-managed-devices.md#require-device-to-be-marked-as-compliant) kontrolek urządzeń w zasadach dostępu warunkowego, zalecamy użycie polecenia Wymagaj, aby urządzenie było oznaczone jako zgodne jako kontrolka w tych zasadach.

![Zrzut ekranu przedstawiający przyznawanie uprawnień w zasadach dostępu warunkowego wymagających zgodności urządzeń](./media/active-directory-ops-guide/active-directory-ops-img7.png)

#### <a name="device-trust-access-policies-recommended-reading"></a>Zalecane odczytywanie zasad dostępu do zaufania urządzeń

- [How To: Plan your hybrid Azure Active Directory join implementation (Jak zaplanować implementację dołączania hybrydowego)](../devices/hybrid-azuread-join-plan.md)
- [Konfiguracje tożsamości i dostępu do urządzeń](/microsoft-365/enterprise/microsoft-365-policies-configurations)

### <a name="windows-hello-for-business"></a>Windows Hello for Business

W Windows 10 [Windows Hello dla firm](/windows/security/identity-protection/hello-for-business/hello-identity-verification) hasła silne uwierzytelnianie dwuskładnikowe na komputerach. Windows Hello dla firm usprawnione środowisko uwierzytelniania wieloskładnikowego dla użytkowników i zmniejsza zależność od haseł. Jeśli nie rozpoczęto wdrażania urządzeń Windows 10 lub wdrożono je tylko częściowo, zalecamy uaktualnienie do wersji [](/windows/security/identity-protection/hello-for-business/hello-manage-in-organization) Windows 10 i włączenie Windows Hello dla firm na wszystkich urządzeniach.

Jeśli chcesz dowiedzieć się więcej na temat uwierzytelniania bez hasła, zobacz [A world without passwords with Azure Active Directory](../authentication/concept-authentication-passwordless.md)(Świat bez haseł z Azure Active Directory ).

## <a name="application-authentication-and-assignment"></a>Uwierzytelnianie i przypisywanie aplikacji

### <a name="single-sign-on-for-apps"></a>Logowanie pojedyncze dla aplikacji

Zapewnienie standardowego mechanizmu logowania pojedynczego dla całego przedsiębiorstwa ma kluczowe znaczenie dla najlepszego doświadczenia użytkownika, zmniejszenia ryzyka, możliwości tworzenia raportów i zapewniania ładu. Jeśli używasz aplikacji, które obsługują logowanie jednokrotne z usługą Azure AD, ale są obecnie skonfigurowane do używania kont lokalnych, należy ponownie skonfigurować te aplikacje do używania logowania jednokrotnego z usługą Azure AD. Podobnie jeśli używasz aplikacji, które obsługują logowanie jednokrotne w usłudze Azure AD, ale korzystają z innego dostawcy tożsamości, należy ponownie skonfigurować te aplikacje do korzystania z logowania jednokrotnego w usłudze Azure AD. W przypadku aplikacji, które nie obsługują protokołów federacji, ale obsługują uwierzytelnianie oparte na formularzach, zalecamy skonfigurowanie aplikacji do używania magazynu haseł z usługą Azure AD serwer proxy aplikacji. [](../manage-apps/application-proxy-configure-single-sign-on-password-vaulting.md)

![Logowanie oparte na hasłach w aplikacji AppProxy](./media/active-directory-ops-guide/active-directory-ops-img8.png)

> [!NOTE]
> Jeśli nie masz mechanizmu odnajdywania nieza zarządzania aplikacjami w organizacji, zalecamy zaimplementowanie procesu odnajdywania przy użyciu rozwiązania brokera zabezpieczeń dostępu do chmury (CASB), takiego [jak Microsoft Cloud App Security](https://www.microsoft.com/enterprise-mobility-security/cloud-app-security).

Na koniec, jeśli masz galerię aplikacji usługi Azure AD i używasz aplikacji, które obsługują logowanie jednokrotne z usługą Azure AD, zalecamy wyświetlanie aplikacji [w galerii aplikacji.](../develop/v2-howto-app-gallery-listing.md)

#### <a name="single-sign-on-recommended-reading"></a>Zalecana lektura logowania pojedynczego

- [Co to jest dostęp do aplikacji i logowanie Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

### <a name="migration-of-ad-fs-applications-to-azure-ad"></a>Migracja aplikacji AD FS do usługi Azure AD

[Migrowanie aplikacji z usługi AD FS do](../manage-apps/migrate-adfs-apps-to-azure.md) usługi Azure AD zapewnia dodatkowe możliwości w zakresie zabezpieczeń, bardziej spójnego zarządzania i lepszego środowiska współpracy. Jeśli masz aplikacje skonfigurowane w usłudze AD FS, które obsługują logowanie jednokrotne z usługą Azure AD, należy ponownie skonfigurować te aplikacje do używania logowania jednokrotnego z usługą Azure AD. Jeśli masz aplikacje skonfigurowane w usłudze AD FS z nietypowymi konfiguracjami nieobsługiwanymi przez usługę Azure AD, skontaktuj się z właścicielami aplikacji, aby dowiedzieć się, czy specjalna konfiguracja jest bezwzględnym wymaganiem aplikacji. Jeśli nie jest to wymagane, należy ponownie skonfigurować aplikację do używania logowania jednokrotnego z usługą Azure AD.

![Usługa Azure AD jako podstawowy dostawca tożsamości](./media/active-directory-ops-guide/active-directory-ops-img9.png)

> [!NOTE]
> [Azure AD Connect Health usług ADFS](../hybrid/how-to-connect-health-adfs.md) mogą służyć do zbierania szczegółowych informacji o konfiguracji poszczególnych aplikacji, które mogą być potencjalnie migrowane do usługi Azure AD.

### <a name="assign-users-to-applications"></a>Przypisywanie użytkowników do aplikacji

[Przypisywanie użytkowników do aplikacji najlepiej](../manage-apps/assign-user-or-group-access-portal.md) jest mapować przy użyciu grup, ponieważ zapewniają one większą elastyczność i możliwość zarządzania na dużą skalę. Zalety korzystania z grup obejmują dynamiczne członkostwo w grupach oparte na [atrybutach](../enterprise-users/groups-dynamic-membership.md) [i delegowanie do właścicieli aplikacji.](../fundamentals/active-directory-accessmanagement-managing-group-owners.md) W związku z tym, jeśli już używasz grup i zarządzasz nimi, zalecamy podjęcia następujących działań w celu usprawnienia zarządzania na dużą skalę:

- Delegowanie zarządzania grupą i ładu do właścicieli aplikacji.
- Zezwalaj na dostęp samoobsługowy do aplikacji.
- Zdefiniuj grupy dynamiczne, jeśli atrybuty użytkownika mogą spójnie określać dostęp do aplikacji.
- Zaim implementuj zaświadczenia dla grup używanych do uzyskiwania dostępu do aplikacji przy użyciu [przeglądów dostępu usługi Azure AD.](../governance/access-reviews-overview.md)

Z drugiej strony, jeśli znajdziesz aplikacje, które mają przypisanie do poszczególnych użytkowników, pamiętaj, aby zaimplementować [zarządzanie](../governance/index.yml) tymi aplikacjami.

#### <a name="assign-users-to-applications-recommended-reading"></a>Zalecane odczytywanie przypisań użytkowników do aplikacji

- [Przypisywanie użytkowników i grup do aplikacji w Azure Active Directory](../manage-apps/assign-user-or-group-access-portal.md)
- [Delegowanie uprawnień rejestracji aplikacji w Azure Active Directory](../roles/delegate-app-roles.md)
- [Reguły członkostwa dynamicznego dla grup w Azure Active Directory](../enterprise-users/groups-dynamic-membership.md)

## <a name="access-policies"></a>Zasady dostępu

### <a name="named-locations"></a>Nazwane lokalizacje

Nazwane [lokalizacje w](../reports-monitoring/quickstart-configure-named-locations.md) usłudze Azure AD mogą oznaczać etykietami zakresy zaufanych adresów IP w organizacji. Usługa Azure AD używa nazwanych lokalizacji w następujących celach:

- Zapobiegaj wynikom fałszywie dodatnim w zdarzeniach o ryzyku. Logowanie z zaufanej lokalizacji sieciowej obniża ryzyko związane z logowaniem użytkownika.
- Konfigurowanie [dostępu warunkowego opartego na lokalizacji.](../reports-monitoring/quickstart-configure-named-locations.md)

![Nazwana lokalizacja](./media/active-directory-ops-guide/active-directory-ops-img10.png)

Na podstawie priorytetu skorzystaj z poniższej tabeli, aby znaleźć zalecane rozwiązanie, które najlepiej odpowiada potrzebom Twojej organizacji:

| **Priority** | **Scenariusz** | **Zalecenie** |
| ------------ | -------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------- |
| 1 | Jeśli używasz phS lub PTA i nazwane lokalizacje nie zostały zdefiniowane | Definiowanie nazwanych lokalizacji w celu usprawnienia wykrywania zdarzeń o ryzyku |
| 2 | Jeśli jesteś federowany i nie używasz oświadczenia "insideCorporateNetwork", a nazwane lokalizacje nie zostały zdefiniowane | Definiowanie nazwanych lokalizacji w celu usprawnienia wykrywania zdarzeń o ryzyku |
| 3 | Jeśli w zasadach dostępu warunkowego nie używasz nazwanych lokalizacji i nie ma ryzyka ani kontroli urządzeń w zasadach dostępu warunkowego | Konfigurowanie zasad dostępu warunkowego w celu dołączania nazwanych lokalizacji |
| 4 | Jeśli jesteś federowany i używasz oświadczenia "insideCorporateNetwork", a nazwane lokalizacje nie zostały zdefiniowane | Definiowanie nazwanych lokalizacji w celu usprawnienia wykrywania zdarzeń o ryzyku |
| 5 | Jeśli używasz zaufanych adresów IP z usługą MFA zamiast nazwanych lokalizacji i oznaczasz je jako zaufane | Definiowanie nazwanych lokalizacji i oznaczanie ich jako zaufanych w celu usprawnienia wykrywania zdarzeń o ryzyku |

### <a name="risk-based-access-policies"></a>Zasady dostępu oparte na ryzykach

Usługa Azure AD może obliczyć ryzyko dla każdego logowania i każdego użytkownika. Użycie ryzyka jako kryterium w zasadach dostępu może zapewnić lepsze środowisko użytkownika, na przykład mniejszą liczbę monitów o uwierzytelnienie i lepsze zabezpieczenia, na przykład monitowanie użytkowników tylko wtedy, gdy są potrzebne, oraz automatyzowanie odpowiedzi i korygowania.

![Zasady ryzyka związanego z logowaniem](./media/active-directory-ops-guide/active-directory-ops-img11.png)

Jeśli jesteś już właścicielem Azure AD — wersja Premium P2, które obsługują ryzyko w zasadach dostępu, ale nie są one używane, zdecydowanie zalecamy dodanie ryzyka do twojego bezpieczeństwa.

#### <a name="risk-based-access-policies-recommended-reading"></a>Zalecane odczytywanie zasad dostępu opartych na ryzykach

- [How To: Configure the sign-in risk policy (2. Konfigurować zasady ryzyka związanego z logowaniem)](../identity-protection/howto-identity-protection-configure-risk-policies.md)
- [Instrukcji: konfigurowanie zasad ryzyka związanego z użytkownikiem](../identity-protection/howto-identity-protection-configure-risk-policies.md)

### <a name="client-application-access-policies"></a>Zasady dostępu aplikacji klienckiej

Microsoft Intune Application Management (MAM) umożliwia wypychanie kontrolek ochrony danych, takich jak szyfrowanie magazynu, numer PIN, zdalne czyszczenie magazynu itp. do zgodnych klienckich aplikacji mobilnych, takich jak Outlook Mobile. Ponadto można utworzyć zasady dostępu warunkowego w celu [ograniczenia](../conditional-access/app-based-conditional-access.md) dostępu do usług w chmurze, takich jak Exchange Online, z zatwierdzonych lub zgodnych aplikacji.

Jeśli pracownicy instalują aplikacje z obsługą zarządzania aplikacjami mobilnymi, takie jak aplikacje mobilne pakietu Office w celu uzyskiwania dostępu do zasobów firmowych, takich jak usługa Exchange Online lub SharePoint Online, oraz obsługują model BYOD (bring your own device), zalecamy wdrożenie zasad zarządzania aplikacjami mobilnymi w celu zarządzania konfiguracją aplikacji na urządzeniach osobistych bez rejestracji w usłudze MDM, a następnie zaktualizowanie zasad dostępu warunkowego w celu zezwalania tylko na dostęp z klientów z obsługą zarządzania aplikacjami mobilnymi.

![Kontrola udzielania dostępu warunkowego](./media/active-directory-ops-guide/active-directory-ops-img12.png)

Jeśli pracownicy instalują aplikacje z możliwością zarządzania aplikacjami mobilnymi względem zasobów firmy, a dostęp do nich jest ograniczony na urządzeniach zarządzanych przez usługę Intune, należy rozważyć wdrożenie zasad zarządzania aplikacjami mobilnymi w celu zarządzania konfiguracją aplikacji dla urządzeń osobistych i zaktualizowanie zasad dostępu warunkowego, aby zezwolić na dostęp tylko od klientów z możliwością zarządzania aplikacjami mobilnymi.

### <a name="conditional-access-implementation"></a>Implementacja dostępu warunkowego

Dostęp warunkowy jest niezbędnym narzędziem do poprawy poziomu bezpieczeństwa organizacji. Dlatego ważne jest, aby postępować zgodnie z tymi najlepszymi rozwiązaniami:

- Upewnij się, że wszystkie aplikacje SaaS mają zastosowane co najmniej jedne zasady
- Unikaj łączenia **filtru Wszystkie aplikacje** z **kontrolką bloku,** aby uniknąć ryzyka blokady
- Unikaj **używania filtru Wszyscy** użytkownicy i nieumyślnego dodawania **gości**
- **Migrowanie wszystkich "starszych" zasad do Azure Portal**
- Przechwyć wszystkie kryteria dla użytkowników, urządzeń i aplikacji
- Użyj zasad dostępu warunkowego, [aby zaimplementować usługę MFA](../conditional-access/plan-conditional-access.md)zamiast **uwierzytelniania wieloskładnikowego na użytkownika**
- Mieć niewielki zestaw podstawowych zasad, które mogą być stosowane do wielu aplikacji
- Definiowanie pustych grup wyjątków i dodawanie ich do zasad w celu realizacji strategii wyjątków
- Planowanie kont [z przerwami](../roles/security-planning.md#break-glass-what-to-do-in-an-emergency) bez kontrolek uwierzytelniania wieloskładnikowego
- Zapewnij spójne środowisko dla Microsoft 365 klienckich, na przykład Teams, OneDrive, Outlook itp.) przez zaimplementowanie tego samego zestawu kontrolek dla usług, takich jak Exchange Online i Sharepoint Online
- Przypisywanie do zasad powinno być implementowane za pośrednictwem grup, a nie użytkowników indywidualnych
- Regularnie przeglądaj grupy wyjątków używane w zasadach, aby ograniczyć czas, w jaki użytkownicy nie mają bezpieczeństwa. Jeśli jesteś właścicielem usługi Azure AD P2, możesz zautomatyzować ten proces za pomocą przeglądów dostępu

#### <a name="conditional-access-recommended-reading"></a>Zalecany odczyt dostępu warunkowego

- [Najlepsze rozwiązania dotyczące dostępu warunkowego w Azure Active Directory](../conditional-access/overview.md)
- [Konfiguracje tożsamości i dostępu do urządzeń](/microsoft-365/enterprise/microsoft-365-policies-configurations)
- [Azure Active Directory ustawień dostępu warunkowego](../conditional-access/concept-conditional-access-conditions.md)
- [Typowe zasady dostępu warunkowego](../conditional-access/concept-conditional-access-policy-common.md)

## <a name="access-surface-area"></a>Obszar powierzchni dostępu

### <a name="legacy-authentication"></a>Starsze uwierzytelnianie

Silne poświadczenia, takie jak uwierzytelnianie wieloskładnikowe, nie mogą chronić aplikacji przy użyciu starszych protokołów uwierzytelniania, co oznacza, że jest to preferowany wektor ataku przez złośliwych aktorów. Blokowanie starszego uwierzytelniania ma kluczowe znaczenie dla poprawy bezpieczeństwa dostępu.

Starsze uwierzytelnianie to termin, który odnosi się do protokołów uwierzytelniania używanych przez aplikacje, takich jak:

- Starsi klienci pakietu Office, którzy nie korzystają z nowoczesnego uwierzytelniania (na przykład klienta pakietu Office 2010)
- Klienci, którzy używają protokołów poczty, takich jak IMAP/SMTP/POP

Osoby atakujące zdecydowanie preferują te protokoły — w rzeczywistości niemal [100%](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Your-Pa-word-doesn-t-matter/ba-p/731984) ataków na ataki typu "attack" na hasła korzysta ze starszych protokołów uwierzytelniania. Hakerzy używają starszych protokołów uwierzytelniania, ponieważ nie obsługują logowania interakcyjnego, co jest konieczne w przypadku dodatkowych wyzwań związanych z zabezpieczeniami, takich jak uwierzytelnianie wieloskładnikowe i uwierzytelnianie urządzeń.

Jeśli starsze uwierzytelnianie jest powszechnie używane w środowisku, należy zaplanować [](/office365/enterprise/modern-auth-for-office-2013-and-2016) migrację starszych klientów do klientów, którzy obsługują nowoczesne uwierzytelnianie tak szybko, jak to możliwe. W tym samym tokenie, jeśli niektórzy użytkownicy już używają nowoczesnego uwierzytelniania, ale inni nadal używają starszego uwierzytelniania, należy wykonać następujące kroki, aby zablokować starszych klientów uwierzytelniania:

1. Użyj [raportów działań logowania,](../reports-monitoring/concept-sign-ins.md) aby zidentyfikować użytkowników, którzy nadal używają starszego uwierzytelniania, i zaplanuj korygowanie:

   a. Uaktualnij klientów z możliwością nowoczesnego uwierzytelniania do użytkowników, których to dotyczy.
   
   b. Zaplanuj harmonogram działania w trybie cutover, aby zablokować go na instrukcje opisane poniżej.
   
   c. Zidentyfikuj, które starsze aplikacje mają trudną zależność od starszego uwierzytelniania. Zobacz krok 3 poniżej.

2. Wyłącz starsze protokoły w źródle (na przykład skrzynkę pocztową programu Exchange) dla użytkowników, którzy nie korzystali ze starszego uwierzytelniania, aby uniknąć więcej narażenia.
3. W przypadku pozostałych kont (najlepiej tożsamości innych niż ludzkie, takich jak konta usług), użyj dostępu warunkowego, aby ograniczyć starsze [protokoły](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-Conditional-Access-support-for-blocking-legacy-auth-is/ba-p/245417) po uwierzytelnieniu.

#### <a name="legacy-authentication-recommended-reading"></a>Zalecana lektura starszego uwierzytelniania

- [Włączanie lub wyłączanie dostępu POP3 lub IMAP4 do skrzynek pocztowych w programie Exchange Server](/exchange/clients/pop3-and-imap4/configure-mailbox-access)

### <a name="consent-grants"></a>Granty zgody

W przypadku ataku z niedozwolonym udzieleniem zgody osoba atakująca tworzy aplikację zarejestrowaną w usłudze Azure AD, która żąda dostępu do danych, takich jak informacje kontaktowe, wiadomości e-mail lub dokumenty. Użytkownicy mogą udzielać zgody na złośliwe aplikacje za pośrednictwem ataków wyłudzających informacje podczas trafiania na złośliwe witryny internetowe.

Poniżej znajduje się lista aplikacji z uprawnieniami, które warto sprawdzać w przypadku usług w chmurze firmy Microsoft:

- Aplikacje z aplikacją lub delegowane \* . Uprawnienia ReadWrite
- Aplikacje z delegowanymi uprawnieniami mogą odczytywać i wysyłać wiadomości e-mail w imieniu użytkownika oraz zarządzać nimi
- Aplikacje, które mają przyznane uprawnienia przy użyciu następujących uprawnień:

| Zasób | Uprawnienie |
| :- | :- |
| Exchange Online | Eas. AccessAsUser.All |
| | Ews. AccessAsUser.All |
| | Mail.Read |
| Microsoft Graph API | Mail.Read |
| | Mail.Read.Shared |
| | Mail.ReadWrite |

- Aplikacje, dla których udzielono pełnej personifikacji użytkownika, zalogował się użytkownik. Na przykład:

|Zasób | Uprawnienie |
| :- | :- |
| Microsoft Graph API| Directory.AccessAsUser.All |
| Interfejs API REST platformy Azure | user_impersonation |

Aby uniknąć tego scenariusza, należy odwoływać się do wykrywania i korygowania niedozwolonych grantów w usłudze [Office 365](/office365/securitycompliance/detect-and-remediate-illicit-consent-grants) w celu identyfikowania i naprawiania wszelkich aplikacji z niedozwolonym grantem lub aplikacji, które mają więcej grantów niż jest to konieczne. Następnie całkowicie [usuń samoobsługę i](../manage-apps/configure-user-consent.md) [ustanów procedury ładu.](../manage-apps/configure-admin-consent-workflow.md) Na koniec zaplanuj regularne przeglądy uprawnień aplikacji i usuń je, gdy nie są potrzebne.

#### <a name="consent-grants-recommended-reading"></a>Zgoda udziela zalecanej lekturze

- [Microsoft Graph interfejsu API](/graph/permissions-reference)

### <a name="user-and-group-settings"></a>Ustawienia użytkowników i grup

Poniżej przedstawiono ustawienia użytkownika i grupy, które można zablokować, jeśli nie ma jawnej potrzeby biznesowej:

#### <a name="user-settings"></a>Ustawienia użytkownika

- **Użytkownicy zewnętrzni** — współpraca zewnętrzna może odbywać się organicznie w przedsiębiorstwie za pomocą usług takich jak Teams, Power BI, Sharepoint Online i Azure Information Protection. Jeśli masz jawne ograniczenia dotyczące kontrolowania współpracy zewnętrznej inicjowanej przez użytkownika, zaleca się włączenie użytkowników zewnętrznych za pomocą zarządzania uprawnieniami usługi [Azure AD](../governance/entitlement-management-overview.md) lub kontrolowanej operacji, takiej jak za pośrednictwem działu pomocy technicznej. Jeśli nie chcesz zezwalać na organiczną współpracę zewnętrzną dla usług, możesz całkowicie zablokować członkom [możliwość zapraszania użytkowników zewnętrznych.](../external-identities/delegate-invitations.md) Możesz również zezwalać na określone domeny [lub blokować je w](../external-identities/allow-deny-list.md) zaproszeniach użytkowników zewnętrznych.
- **Rejestracje aplikacji** — po Rejestracje aplikacji użytkownicy końcowi mogą samodzielnie dołączać aplikacje i udzielać dostępu do swoich danych. Typowym przykładem rejestracji aplikacji jest włączanie wtyczek programu Outlook przez użytkowników lub asystentów głosowych, takich jak Alexa i Siri, w celu odczytywania wiadomości e-mail i kalendarza lub wysyłania wiadomości e-mail w ich imieniu. Jeśli klient zdecyduje się wyłączyć rejestrację aplikacji, zespoły programu InfoSec i zarządzania dostępem i dostępem i dostępem muszą uczestniczyć w zarządzaniu wyjątkami (rejestracjami aplikacji, które są wymagane na podstawie wymagań biznesowych), ponieważ muszą zarejestrować aplikacje przy użyciu konta administratora, a najprawdopodobniej wymagają zaprojektowania procesu do obsługi tego procesu.
- **Portal administracyjny** — organizacje mogą zablokować blok usługi Azure AD w usłudze Azure Portal, aby osoby inne niż administratorzy nie mogą uzyskać dostępu do zarządzania w usłudze Azure AD w Azure Portal i pomylić. Przejdź do ustawień użytkownika w portalu zarządzania usługi Azure AD, aby ograniczyć dostęp:

![Ograniczony dostęp do portalu administracyjnego](./media/active-directory-ops-guide/active-directory-ops-img13.png)

> [!NOTE]
> Osoby inne niż administratorzy mogą nadal mieć dostęp do interfejsów zarządzania usługi Azure AD za pośrednictwem wiersza polecenia i innych interfejsów programowych.

#### <a name="group-settings"></a>Ustawienia grupy

**Samoobsługowe zarządzanie grupami / Użytkownicy mogą tworzyć grupy zabezpieczeń/Microsoft 365 grupy.** Jeśli nie ma bieżącej inicjatywy samoobsługowej dla grup w chmurze, klienci mogą zdecydować się na jej wyłączenie, dopóki nie będą gotowi do korzystania z tej funkcji.

#### <a name="groups-recommended-reading"></a>Zalecane odczytywanie przez grupy

- [Co to jest współpraca w usłudze Azure Active Directory B2B?](../external-identities/what-is-b2b.md)
- [Integrowanie aplikacji z usługą Azure Active Directory](../develop/quickstart-register-app.md)
- [Aplikacje, uprawnienia i zgody w Azure Active Directory.](../develop/quickstart-register-app.md)
- [Używanie grup do zarządzania dostępem do zasobów w Azure Active Directory](./active-directory-manage-groups.md)
- [Konfigurowanie samoobsługowego zarządzania dostępem do aplikacji w usłudze Azure Active Directory](../enterprise-users/groups-self-service-management.md)

### <a name="traffic-from-unexpected-locations"></a>Ruch z nieoczekiwanych lokalizacji

Osoby atakujące pochodzą z różnych części świata. Zarządzanie tym ryzykiem przy użyciu zasad dostępu warunkowego z lokalizacją jako warunkiem. Warunek [lokalizacji](../conditional-access/location-condition.md) zasad dostępu warunkowego umożliwia zablokowanie dostępu do lokalizacji, z których nie ma powodów biznesowych do zalogowania się.

![Tworzenie nowej nazwanej lokalizacji](./media/active-directory-ops-guide/active-directory-ops-img14.png)

Jeśli jest dostępna, użyj rozwiązania do zarządzania informacjami i zdarzeniami zabezpieczeń (SIEM), aby analizować i znaleźć wzorce dostępu w różnych regionach. Jeśli nie używasz produktu SIEM lub nie pozyszsz informacji o uwierzytelnianiu z usługi Azure AD, zalecamy użycie rozwiązania [Azure Monitor](../../azure-monitor/overview.md) do identyfikowania wzorców dostępu w różnych regionach.

## <a name="access-usage"></a>Użycie dostępu

### <a name="azure-ad-logs-archived-and-integrated-with-incident-response-plans"></a>Dzienniki usługi Azure AD zarchiwizowane i zintegrowane z planami reagowania na zdarzenia

Dostęp do działań związanych z logowaniem, inspekcji i zdarzeń o ryzyku dla usługi Azure AD ma kluczowe znaczenie dla rozwiązywania problemów, analizy użycia i badania śledczego. Usługa Azure AD zapewnia dostęp do tych źródeł za pośrednictwem interfejsów API REST, które mają ograniczony okres przechowywania. System zarządzania informacjami i zdarzeniami zabezpieczeń (SIEM) lub równoważna technologia archiwalna jest kluczem do długoterminowego przechowywania inspekcji i możliwości obsługi. Aby włączyć długoterminowe przechowywanie dzienników usługi Azure AD, należy dodać je do istniejącego rozwiązania SIEM lub użyć [Azure Monitor](../reports-monitoring/concept-activity-logs-azure-monitor.md). Archiwizowanie dzienników, które mogą być używane w ramach planów i badań reagowania na zdarzenia.

#### <a name="logs-recommended-reading"></a>Zalecane odczytywanie dzienników

- [Azure Active Directory interfejsu API inspekcji](/graph/api/resources/directoryaudit)
- [Azure Active Directory interfejsu API raportu aktywności logowania](/graph/api/resources/signin)
- [Pobieranie danych przy użyciu interfejsu API raportowania usługi Azure AD z certyfikatami](../reports-monitoring/tutorial-access-api-with-certificates.md)
- [Microsoft Graph dla Azure Active Directory Identity Protection](../identity-protection/howto-identity-protection-graph-api.md)
- [Dokumentacja interfejsu API działań zarządzania usługą Office 365](/office/office-365-management-api/office-365-management-activity-api-reference)
- [Korzystanie z pakietu zawartości usługi Power BI dla usługi Azure Active Directory](../reports-monitoring/howto-use-azure-monitor-workbooks.md)

## <a name="summary"></a>Podsumowanie

Bezpieczna infrastruktura tożsamości ma 12 aspektów. Ta lista pomoże Ci dodatkowo zabezpieczyć poświadczenia i zarządzać nimi, zdefiniować środowisko uwierzytelniania, delegować przypisanie, zmierzyć użycie i zdefiniować zasady dostępu na podstawie bezpieczeństwa przedsiębiorstwa.

- Przypisywanie właścicieli do kluczowych zadań.
- Implementowanie rozwiązań w celu wykrywania słabych lub ujawnionych haseł, ulepszania zarządzania hasłami i ich ochrony oraz dalszego zabezpieczania dostępu użytkowników do zasobów.
- Zarządzanie tożsamościami urządzeń w celu ochrony zasobów w dowolnym momencie i z dowolnej lokalizacji.
- Implementowanie uwierzytelniania bez hasła.
- Zapewnianie standardowego mechanizmu logowania pojedynczego w całej organizacji.
- Migrowanie aplikacji z AD FS do usługi Azure AD w celu zapewnienia lepszych zabezpieczeń i bardziej spójnego zarządzania.
- Przypisywanie użytkowników do aplikacji przy użyciu grup w celu zapewnienia większej elastyczności i możliwości zarządzania na dużą skalę.
- Konfigurowanie zasad dostępu opartych na ryzyku.
- Blokowanie starszych protokołów uwierzytelniania.
- Wykrywanie i korygowanie niedozwolonych zgód.
- Zablokuj ustawienia użytkowników i grup.
- Włącz długoterminowe przechowywanie dzienników usługi Azure AD na potrzeby rozwiązywania problemów, analizy użycia i badań śledczych.

## <a name="next-steps"></a>Następne kroki

Wprowadzenie do operacji kontroli [i akcji nadzoru nad tożsamościami.](active-directory-ops-guide-govern.md)
