---
title: Przewodnik dotyczący operacji zarządzania uwierzytelnianiem Azure Active Directory
description: W tym przewodniku odwołuje się opis operacji sprawdzania i działań, które należy wykonać w celu zabezpieczenia zarządzania uwierzytelnianiem
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
ms.openlocfilehash: 178c54b9726f21775603d67cb0911237aa4caf01
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90601368"
---
# <a name="azure-active-directory-authentication-management-operations-reference-guide"></a>Przewodnik dotyczący operacji zarządzania uwierzytelnianiem Azure Active Directory

W tej części [przewodnika dotyczącego odwołań usługi Azure AD](active-directory-ops-guide-intro.md) opisano testy i akcje, które należy wykonać w celu zabezpieczenia i zarządzania poświadczeniami, Definiowanie środowiska uwierzytelniania, przypisywanie delegatów, użycie miar i definiowanie zasad dostępu w oparciu o stan zabezpieczeń przedsiębiorstwa.

> [!NOTE]
> Te zalecenia są aktualne w dniu opublikowania, ale mogą ulec zmianie w czasie. Organizacje powinny stale sprawdzać swoją tożsamość jako produkty i usługi firmy Microsoft, które są roznoszone wraz z upływem czasu.

## <a name="key-operational-processes"></a>Najważniejsze procesy operacyjne

### <a name="assign-owners-to-key-tasks"></a>Przypisywanie właścicieli do kluczowych zadań

Zarządzanie Azure Active Directory wymaga ciągłego wykonywania kluczowych zadań operacyjnych i procesów, które mogą nie być częścią projektu wdrożenia. Nadal ważne jest, aby skonfigurować te zadania w celu zoptymalizowania środowiska. Najważniejsze zadania i ich zalecani właściciele obejmują:

| Zadanie | Właściciel |
| :- | :- |
| Zarządzanie cyklem życia konfiguracji logowania jednokrotnego (SSO) w usłudze Azure AD | Zespół operacji IAM |
| Projektowanie zasad dostępu warunkowego dla aplikacji usługi Azure AD | Zespół architektury InfoSec |
| Archiwizowanie aktywności logowania w systemie SIEM | Zespół ds. operacji InfoSec |
| Archiwizowanie zdarzeń ryzyka w systemie SIEM | Zespół ds. operacji InfoSec |
| Klasyfikacja i zbadaj raporty zabezpieczeń | Zespół ds. operacji InfoSec |
| Klasyfikacja i zbadaj zdarzenia ryzyka | Zespół ds. operacji InfoSec |
| Klasyfikacja i zbadaj użytkowników oflagowanych pod kątem ryzyka i luk w zabezpieczeniach z Azure AD Identity Protection | Zespół ds. operacji InfoSec |

> [!NOTE]
> Azure AD Identity Protection wymaga licencji na Azure AD — wersja Premium P2. Aby znaleźć odpowiednią licencję dla wymagań, zobacz [porównanie ogólnie dostępnych funkcji wersji Azure AD — wersja bezpłatna i Azure AD — wersja Premium](https://azure.microsoft.com/pricing/details/active-directory/).

Podczas przeglądania listy może być konieczne przypisanie właściciela do zadań, w których brakuje właściciela, lub dostosować własność do zadań z właścicielami, które nie są wyrównane do powyższych zaleceń.

#### <a name="owner-recommended-reading"></a>Odczytywanie zalecanego przez właściciela

- [Przypisywanie ról administratorów w usłudze Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md)
- [Nadzór na platformie Azure](../../governance/index.yml)

## <a name="credentials-management"></a>Zarządzanie poświadczeniami

### <a name="password-policies"></a>Zasady dotyczące haseł

Bezpieczne zarządzanie hasłami jest jednym z najważniejszych części zarządzania tożsamościami i dostępem oraz często największym celem ataków. Usługa Azure AD obsługuje kilka funkcji, które mogą pomóc zapobiec pomyślnym ataku.

Skorzystaj z poniższej tabeli, aby znaleźć zalecane rozwiązanie do ograniczania problemu, który należy rozwiązać:

| Problem | Zalecenie |
| :- | :- |
| Brak mechanizmu ochrony przed słabymi hasłami | Włącz samoobsługowe [Resetowanie hasła (SSPR)](../authentication/concept-sspr-howitworks.md) usługi Azure AD i [ochronę hasłem](../authentication/concept-password-ban-bad-on-premises.md) |
| Brak mechanizmu wykrywania nieujawnionych haseł | Włącz funkcję [synchronizacji skrótów haseł](../hybrid/how-to-connect-password-hash-synchronization.md) (PHS) w celu uzyskania szczegółowych informacji |
| Używanie AD FS i nie można przenieść do uwierzytelniania zarządzanego | Włącz [AD FS ekstranetu inteligentnego blokowania](/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-smart-lockout-protection) i/lub [inteligentnego blokowania usługi Azure AD](../authentication/howto-password-smart-lockout.md) |
| Zasady haseł używają reguł opartych na dużej złożoności, takich jak długość, wiele zestawów znaków lub wygaśnięcie | Zapoznaj się z [zaleceniami zalecanymi przez firmę Microsoft](https://www.microsoft.com/research/publication/password-guidance/?from=http%3A%2F%2Fresearch.microsoft.com%2Fpubs%2F265143%2Fmicrosoft_password_guidance.pdf) i Zmień podejście do zarządzania hasłami i Wdróż [ochronę hasłem usługi Azure AD](../authentication/concept-password-ban-bad.md). |
| Użytkownicy nie są zarejestrowani do korzystania z uwierzytelniania wieloskładnikowego (MFA) | [Zarejestruj informacje o zabezpieczeniach wszystkich użytkowników](../identity-protection/howto-identity-protection-configure-mfa-policy.md) , aby można było ich używać jako mechanizmu do weryfikowania tożsamości użytkownika wraz z ich hasłem |
| Nie istnieje odwołanie hasła na podstawie ryzyka użytkownika | Wdrażanie [zasad ryzyka dla użytkowników](../identity-protection/howto-identity-protection-configure-risk-policies.md) usługi Azure AD Identity Protection w celu wymuszenia zmiany hasła dla przecieków poświadczeń przy użyciu SSPR |
| Nie istnieje mechanizm inteligentnego blokowania, aby chronić złośliwe uwierzytelnianie od nieprawidłowych uczestników pochodzących ze zidentyfikowanych adresów IP | Wdrażanie uwierzytelniania zarządzanego przez chmurę z użyciem synchronizacji skrótów haseł lub [uwierzytelniania przekazywanego](../hybrid/how-to-connect-pta-quick-start.md) (PTA) |

#### <a name="password-policies-recommended-reading"></a>Zalecane odczytywanie zasad haseł

- [Najlepsze rozwiązania dotyczące usługi Azure AD i AD FS: obrona przed atakami polegającymi na rozpylaniu hasła — Enterprise Mobility + Security](https://cloudblogs.microsoft.com/enterprisemobility/2018/03/05/azure-ad-and-adfs-best-practices-defending-against-password-spray-attacks/)

### <a name="enable-self-service-password-reset-and-password-protection"></a>Włącz Samoobsługowe resetowanie haseł i ochronę hasłem

Użytkownicy, którzy chcą zmieniać lub resetować swoje hasła, są jednym z największych źródeł ilości i kosztów wywołań pomocy technicznej. Oprócz kosztów, zmiana hasła jako narzędzia służącego do ograniczenia ryzyka użytkownika to podstawowy etap ulepszania stan zabezpieczeń organizacji.

Zaleca się wdrożenie [funkcji samoobsługowego resetowania haseł](../authentication/concept-sspr-howitworks.md) (SSPR) usługi Azure AD oraz [ochronę hasłem](../authentication/howto-password-ban-bad-on-premises-deploy.md) lokalnym w celu osiągnięcia:

- Odkształcenie połączeń pomocy technicznej.
- Zastąp użycie haseł tymczasowych.
- Zastąp wszelkie istniejące rozwiązanie do samoobsługowego zarządzania hasłami, które opiera się na rozwiązaniu lokalnym.
- [Eliminowanie słabych haseł](../authentication/concept-password-ban-bad.md) w organizacji.

> [!NOTE]
> W przypadku organizacji z subskrypcją Azure AD — wersja Premium P2 zaleca się wdrożenie SSPR i użycie jej w ramach [zasad ryzyka użytkownika ochrony tożsamości](../identity-protection/howto-identity-protection-configure-risk-policies.md).

### <a name="strong-credential-management"></a>Silne zarządzanie poświadczeniami

Hasła nie są wystarczająco bezpieczne, aby zapobiec uzyskiwaniu dostępu do środowiska przez złe podmioty. Na potrzeby uwierzytelniania wieloskładnikowego (MFA) musi być włączony co najmniej każdy użytkownik z kontem uprzywilejowanym. W idealnym przypadku należy włączyć [rejestrację łączną](../authentication/concept-registration-mfa-sspr-combined.md) i wymagać od wszystkich użytkowników rejestracji usługi MFA i SSPR przy użyciu [połączonego środowiska rejestracji](../user-help/security-info-setup-signin.md). Na koniec zalecamy stosowanie strategii w celu [zapewnienia odporności](../authentication/concept-resilient-controls.md) na zmniejszenie ryzyka blokady z powodu nieprzewidzianych okoliczności.

![Przepływ pracy połączonej użytkownika](./media/active-directory-ops-guide/active-directory-ops-img4.png)

### <a name="on-premises-outage-authentication-resiliency"></a>Odporność uwierzytelniania w przypadku awarii lokalnej

Oprócz korzyści płynących z uproszczenia i włączenia wykrywania nieujawnionych poświadczeń, synchronizacja skrótów haseł w usłudze Azure AD (PHS) i usługa Azure MFA umożliwiają użytkownikom dostęp do aplikacji SaaS i Microsoft 365 w przypadku awarii w środowisku lokalnym z powodu cyberattacks, takich jak [NotPetya](https://www.microsoft.com/security/blog/2018/02/05/overview-of-petya-a-rapid-cyberattack/). Możliwe jest również włączenie PHS w połączeniu z Federacją. Włączenie PHS umożliwia rezerwowe uwierzytelnianie, gdy usługi federacyjne nie są dostępne.

Jeśli Twoja organizacja lokalna nie ma strategii odporności na awarie lub ma taką, która nie jest zintegrowana z usługą Azure AD, należy wdrożyć usługę Azure AD PHS i zdefiniować plan odzyskiwania po awarii, który obejmuje PHS. Włączenie usługi Azure AD PHS umożliwi użytkownikom uwierzytelnianie w usłudze Azure AD, jeśli lokalne Active Directory będą niedostępne.

![przepływ synchronizacji skrótów haseł](./media/active-directory-ops-guide/active-directory-ops-img5.png)

Aby lepiej zrozumieć opcje uwierzytelniania, zobacz [Wybieranie odpowiedniej metody uwierzytelniania dla Azure Active Directory rozwiązanie do tworzenia tożsamości hybrydowej](../hybrid/choose-ad-authn.md).

### <a name="programmatic-usage-of-credentials"></a>Programowe użycie poświadczeń

Skrypty usługi Azure AD korzystające z programu PowerShell lub aplikacji korzystających z interfejsu API Microsoft Graph wymagają bezpiecznego uwierzytelniania. Słabe zarządzanie poświadczeniami wykonuje te skrypty i narzędzia zwiększają ryzyko kradzieży poświadczeń. Jeśli używasz skryptów lub aplikacji korzystających z haseł zakodowanych lub monitów o hasło, należy najpierw przejrzeć hasła w plikach konfiguracji lub kodzie źródłowym, zastąpić te zależności i użyć tożsamości zarządzanych przez platformę Azure, uwierzytelniania Integrated-Windows lub [certyfikatów](../reports-monitoring/tutorial-access-api-with-certificates.md) , jeśli jest to możliwe. W przypadku aplikacji, w których poprzednie rozwiązania nie są możliwe, należy rozważyć użycie [Azure Key Vault](https://azure.microsoft.com/services/key-vault/).

Jeśli okaże się, że istnieją jednostki usługi z poświadczeniami hasła i nie masz pewności, jak te poświadczenia hasła są zabezpieczone przez skrypty lub aplikacje, skontaktuj się z właścicielem aplikacji, aby lepiej zrozumieć wzorce użycia.

Firma Microsoft zaleca również kontaktowanie się z właścicielami aplikacji w celu zrozumienia wzorców użycia, jeśli istnieją jednostki usługi z poświadczeniami hasła.

## <a name="authentication-experience"></a>Środowisko uwierzytelniania

### <a name="on-premises-authentication"></a>Uwierzytelnianie lokalne

Uwierzytelnianie federacyjne ze zintegrowanym uwierzytelnianiem systemu Windows (IWA) lub bezproblemowe uwierzytelnianie przy użyciu pojedynczego Sign-On (SSO) z synchronizacją skrótów haseł lub uwierzytelnianiem przekazującym to najlepsze środowisko użytkownika w ramach sieci firmowej z lokalnymi kontrolerami domeny. Minimalizuje to zmęczenie i zmniejsza ryzyko związane z atakami z wykorzystaniem wyłudzania informacji przez użytkowników Prey. Jeśli używasz już uwierzytelniania zarządzanego przez chmurę z PHS lub PTA, ale użytkownicy nadal muszą wpisać swoje hasło podczas uwierzytelniania lokalnego, należy natychmiast [wdrożyć bezproblemową rejestrację jednokrotną](../hybrid/how-to-connect-sso.md). Z drugiej strony, jeśli obecnie są federacyjne z planami, aby ostatecznie migrować do uwierzytelniania zarządzanego przez chmurę, należy zaimplementować bezproblemowe logowanie jednokrotne w ramach projektu migracji.

### <a name="device-trust-access-policies"></a>Zasady dostępu zaufania urządzeń

Podobnie jak w przypadku użytkownika w organizacji, urządzenie to podstawowa tożsamość, która ma być chroniona. Możesz użyć tożsamości urządzenia do ochrony zasobów w dowolnym momencie i z dowolnej lokalizacji.Uwierzytelnianie urządzenia i ich typ zaufania zwiększa stan zabezpieczeń i użyteczność dzięki:

- Unikanie tarcia, na przykład z uwierzytelnianiem MFA, gdy urządzenie jest zaufane
- Blokowanie dostępu z niezaufanych urządzeń
- W przypadku urządzeń z systemem Windows 10 bezpośrednie [Logowanie do zasobów lokalnych jest bezproblemowo](../devices/azuread-join-sso.md).

W tym celu można przenieść tożsamości urządzeń i zarządzać nimi w usłudze Azure AD za pomocą jednej z następujących metod:

- Organizacje mogą używać [Microsoft Intune](/intune/what-is-intune) do zarządzania urządzeniem i wymuszania zasad zgodności, zaświadczania kondycji urządzenia i ustawienia zasad dostępu warunkowego w zależności od tego, czy urządzenie jest zgodne. Microsoft Intune mogą zarządzać urządzeniami z systemem iOS, komputerami Mac (za pośrednictwem integracji JAMF), komputerami stacjonarnymi z systemem Windows (natywnie przy użyciu zarządzania urządzeniami przenośnymi w systemie Windows 10 i współzarządzaniem z programem Microsoft Endpoint Configuration Manager) oraz urządzeniami przenośnymi z systemem Android.
- [Hybrydowe dołączanie usługi Azure AD](../devices/hybrid-azuread-join-managed-domains.md) zapewnia zarządzanie za pomocą zasad grupy lub Configuration Manager Microsoft Endpoint w środowisku z Active Directory urządzeniami przyłączonymi do domeny. Organizacje mogą wdrażać środowisko zarządzane za pomocą PHS lub PTA z bezproblemowym logowaniem jednokrotnym. Przełączenie urządzeń do usługi Azure AD maksymalizuje produktywność użytkowników przez logowanie jednokrotne w chmurze i zasobach lokalnych, umożliwiając jednocześnie bezpieczny dostęp do zasobów w chmurze i lokalnych przy użyciu [dostępu warunkowego](../conditional-access/overview.md)   .

W przypadku urządzeń z systemem Windows przyłączonych do domeny, które nie są zarejestrowane w chmurze, lub urządzeń z systemem Windows przyłączonych do domeny, które są zarejestrowane w chmurze, ale bez zasad dostępu warunkowego, należy zarejestrować niezarejestrowane urządzenia i w obu przypadkach [używać sprzężenia hybrydowego usługi Azure AD jako formantu](../conditional-access/require-managed-devices.md) w zasadach dostępu warunkowego.

![Zrzut ekranu przedstawiający udzielenie zasad dostępu warunkowego wymagający urządzenia hybrydowego](./media/active-directory-ops-guide/active-directory-ops-img6.png)

Jeśli zarządzasz urządzeniami za pomocą rozwiązania MDM lub Microsoft Intune, ale nie korzystasz z formantów urządzeń w zasadach dostępu warunkowego, zalecamy użycie opcji [Wymagaj, aby urządzenie było oznaczone jako zgodne](../conditional-access/require-managed-devices.md#require-device-to-be-marked-as-compliant) jako kontrolka w tych zasadach.

![Zrzut ekranu przydzielenia w zasadach dostępu warunkowego wymagający zgodności urządzenia](./media/active-directory-ops-guide/active-directory-ops-img7.png)

#### <a name="device-trust-access-policies-recommended-reading"></a>Zalecane odczytywanie zasad dostępu zaufania urządzeń

- [Instrukcje: planowanie implementacji dołączania hybrydowego Azure Active Directory](../devices/hybrid-azuread-join-plan.md)
- [Konfiguracje tożsamości i dostępu do urządzeń](/microsoft-365/enterprise/microsoft-365-policies-configurations)

### <a name="windows-hello-for-business"></a>Windows Hello for Business

W systemie Windows 10 funkcja [Windows Hello dla firm](/windows/security/identity-protection/hello-for-business/hello-identity-verification) zastępuje hasła silnym uwierzytelnianiem dwuskładnikowym na komputerach. Funkcja Windows Hello dla firm umożliwia korzystanie z bardziej usprawnień usługi MFA dla użytkowników i zmniejsza zależność od haseł. Jeśli nie rozpoczęto wdrażania urządzeń z systemem Windows 10 lub zostały one tylko częściowo wdrożone, zalecamy uaktualnienie do systemu Windows 10 i [włączenie usługi Windows Hello dla firm](/windows/security/identity-protection/hello-for-business/hello-manage-in-organization) na wszystkich urządzeniach.

Jeśli chcesz dowiedzieć się więcej o uwierzytelnianiu bez hasła, zapoznaj się z artykułem [na świecie, w którym nie ma hasła Azure Active Directory](../authentication/concept-authentication-passwordless.md).

## <a name="application-authentication-and-assignment"></a>Uwierzytelnianie i przypisywanie aplikacji

### <a name="single-sign-on-for-apps"></a>Logowanie jednokrotne dla aplikacji

Zapewnienie standardowego mechanizmu logowania jednokrotnego do całego przedsiębiorstwa ma kluczowe znaczenie dla najlepszego środowiska użytkownika, zmniejszenia ryzyka, możliwości zgłaszania i zarządzania. Jeśli używasz aplikacji obsługujących Logowanie jednokrotne w usłudze Azure AD, ale obecnie są one skonfigurowane do korzystania z kont lokalnych, należy ponownie skonfigurować te aplikacje do korzystania z logowania jednokrotnego w usłudze Azure AD. Podobnie, jeśli korzystasz z aplikacji, które obsługują Logowanie jednokrotne w usłudze Azure AD, ale korzystasz z innego dostawcy tożsamości, należy ponownie skonfigurować te aplikacje do korzystania z logowania jednokrotnego w usłudze Azure AD. W przypadku aplikacji, które nie obsługują protokołów federacyjnych, ale obsługują uwierzytelnianie oparte na formularzach, zalecamy skonfigurowanie aplikacji w taki sposób, aby korzystała z funkcji [magazynowania haseł](../manage-apps/application-proxy-configure-single-sign-on-password-vaulting.md) w usłudze Azure serwer proxy aplikacji usługi Azure AD.

![Logowanie oparte na hasłach AppProxy](./media/active-directory-ops-guide/active-directory-ops-img8.png)

> [!NOTE]
> Jeśli nie masz mechanizmu odnajdywania niezarządzanych aplikacji w organizacji, zalecamy wdrożenie procesu odnajdywania przy użyciu rozwiązania brokera zabezpieczeń dostępu w chmurze (CASB), takiego jak [Microsoft Cloud App Security](https://www.microsoft.com/enterprise-mobility-security/cloud-app-security).

Jeśli masz galerię aplikacji usługi Azure AD i używasz aplikacji, które obsługują Logowanie jednokrotne w usłudze Azure AD, zalecamy [wyświetlanie aplikacji w galerii aplikacji](../azuread-dev/howto-app-gallery-listing.md).

#### <a name="single-sign-on-recommended-reading"></a>Odczyt zalecający Logowanie jednokrotne

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

### <a name="migration-of-ad-fs-applications-to-azure-ad"></a>Migracja aplikacji AD FS do usługi Azure AD

[Migrowanie aplikacji z AD FS do usługi Azure AD](../manage-apps/migrate-adfs-apps-to-azure.md) umożliwia korzystanie z dodatkowych funkcji zabezpieczeń, bardziej spójnego zarządzania i lepszego środowiska współpracy. Jeśli masz skonfigurowane aplikacje w AD FS, które obsługują Logowanie jednokrotne w usłudze Azure AD, należy ponownie skonfigurować te aplikacje do korzystania z logowania jednokrotnego w usłudze Azure AD. Jeśli masz aplikacje skonfigurowane w AD FS z nietypowymi konfiguracjami nieobsługiwanymi przez usługę Azure AD, skontaktuj się z właścicielami aplikacji, aby dowiedzieć się, czy specjalna konfiguracja jest bezwzględnym wymaganiem aplikacji. Jeśli nie jest to wymagane, należy ponownie skonfigurować aplikację do korzystania z logowania jednokrotnego w usłudze Azure AD.

![Usługa Azure AD jako podstawowy dostawca tożsamości](./media/active-directory-ops-guide/active-directory-ops-img9.png)

> [!NOTE]
> [Azure AD Connect Health dla usług AD FS](../hybrid/how-to-connect-health-adfs.md) może służyć do zbierania szczegółowych informacji konfiguracyjnych dotyczących każdej aplikacji, która może zostać poddana migracji do usługi Azure AD.

### <a name="assign-users-to-applications"></a>Przypisywanie użytkowników do aplikacji

[Przypisywanie użytkowników do aplikacji](../manage-apps/assign-user-or-group-access-portal.md) najlepiej jest zamapowane przy użyciu grup, ponieważ umożliwiają one większą elastyczność i możliwość zarządzania w odpowiedniej skali. Korzyści wynikające z używania grup obejmują [dynamiczne członkostwo w grupach](../users-groups-roles/groups-dynamic-membership.md) i [delegowanie do właścicieli aplikacji](../fundamentals/active-directory-accessmanagement-managing-group-owners.md). W związku z tym, jeśli już używasz grup i zarządzasz nimi, zalecamy wykonanie następujących czynności w celu usprawnienia zarządzania w odpowiedniej skali:

- Delegowanie zarządzania grupami i nadzoru do właścicieli aplikacji.
- Zezwalaj na samoobsługowe dostęp do aplikacji.
- Zdefiniuj grupy dynamiczne, jeśli atrybuty użytkownika mogą spójnie określić dostęp do aplikacji.
- Zaimplementuj zaświadczenie grupom używanym do uzyskiwania dostępu do aplikacji za pomocą [przeglądów dostępu do usługi Azure AD](../governance/access-reviews-overview.md).

Z drugiej strony, jeśli znajdziesz aplikacje, które mają przypisanie do poszczególnych użytkowników, pamiętaj, aby zaimplementować [Zarządzanie](../governance/index.yml) tymi aplikacjami.

#### <a name="assign-users-to-applications-recommended-reading"></a>Przypisywanie użytkowników do zalecanych odczytów

- [Przypisywanie użytkowników i grup do aplikacji w Azure Active Directory](../manage-apps/assign-user-or-group-access-portal.md)
- [Delegowanie uprawnień rejestracji aplikacji w Azure Active Directory](../users-groups-roles/roles-delegate-app-roles.md)
- [Reguły członkostwa dynamicznego dla grup w Azure Active Directory](../users-groups-roles/groups-dynamic-membership.md)

## <a name="access-policies"></a>Zasady dostępu

### <a name="named-locations"></a>Nazwane lokalizacje

Z [nazwanymi lokalizacjami](../reports-monitoring/quickstart-configure-named-locations.md) w usłudze Azure AD można oznaczyć zaufane zakresy adresów IP w organizacji. Usługa Azure AD używa nazwanych lokalizacji w następujących celach:

- Zapobiegaj zwracaniu fałszywych zdarzeń o podwyższonym ryzyku. Zalogowanie się z zaufanej lokalizacji sieciowej zmniejsza ryzyko związane z logowaniem użytkownika.
- Konfigurowanie [dostępu warunkowego opartego na lokalizacji](../reports-monitoring/quickstart-configure-named-locations.md).

![Nazwana lokalizacja](./media/active-directory-ops-guide/active-directory-ops-img10.png)

Na podstawie priorytetu Skorzystaj z poniższej tabeli, aby znaleźć zalecane rozwiązanie, które najlepiej spełnia wymagania organizacji:

| **Priority** | **Scenariusz** | **Zalecenie** |
| ------------ | -------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------- |
| 1 | Jeśli używasz PHS lub PTA i nazwane lokalizacje nie zostały zdefiniowane | Definiowanie nazwanych lokalizacji w celu usprawnienia wykrywania zdarzeń o podwyższonym ryzyku |
| 2 | Jeśli jesteś federacyjnym i nie używasz żądania "insideCorporateNetwork" i nazwanych lokalizacji nie zostały zdefiniowane | Definiowanie nazwanych lokalizacji w celu usprawnienia wykrywania zdarzeń o podwyższonym ryzyku |
| 3 | Jeśli nie używasz nazwanych lokalizacji w zasadach dostępu warunkowego i nie ma żadnych elementów w zasadach dostępu warunkowego | Konfigurowanie zasad dostępu warunkowego w celu uwzględnienia nazwanych lokalizacji |
| 4 | Jeśli jesteś federacyjnym i używasz żądania "insideCorporateNetwork" i nazwanych lokalizacji nie zostały zdefiniowane | Definiowanie nazwanych lokalizacji w celu usprawnienia wykrywania zdarzeń o podwyższonym ryzyku |
| 5 | Jeśli używasz zaufanych adresów IP z uwierzytelnianiem MFA zamiast nazwanych lokalizacji i oznaczasz je jako zaufane | Zdefiniuj nazwane lokalizacje i oznacz je jako zaufane, aby poprawić wykrywanie zdarzeń ryzyka |

### <a name="risk-based-access-policies"></a>Zasady dostępu oparte na ryzyku

Usługa Azure AD może obliczyć ryzyko związane z każdym logowaniem i każdym użytkownikiem. Użycie ryzyka jako kryterium w zasadach dostępu może zapewnić lepszy interfejs użytkownika, na przykład mniejszą liczbę monitów uwierzytelniania i lepsze zabezpieczenia, na przykład Monituj użytkowników tylko wtedy, gdy są one używane, i automatyzując odpowiedzi i korekty.

![Zasady ryzyka związanego z logowaniem](./media/active-directory-ops-guide/active-directory-ops-img11.png)

Jeśli masz Azure AD — wersja Premium już stan licencje P2, które obsługują korzystanie z ryzyka w zasadach dostępu, ale nie są używane, zdecydowanie zalecamy dodanie ryzyka do zabezpieczeń.

#### <a name="risk-based-access-policies-recommended-reading"></a>Zalecane odczytywanie zasad dostępu opartych na ryzyku

- [Instrukcje: Konfigurowanie zasad dotyczących ryzyka związanego z logowaniem](../identity-protection/howto-identity-protection-configure-risk-policies.md)
- [Instrukcje: Konfigurowanie zasad ryzyka dla użytkowników](../identity-protection/howto-identity-protection-configure-risk-policies.md)

### <a name="client-application-access-policies"></a>Zasady dostępu aplikacji klienta

Microsoft Intune Zarządzanie aplikacjami (MAM) zapewnia możliwość wypychania kontroli ochrony danych, takich jak szyfrowanie magazynu, kod PIN, czyszczenie zdalnego magazynu itp. do zgodnych aplikacji mobilnych klienta, takich jak Outlook Mobile. Ponadto można utworzyć zasady dostępu warunkowego, aby [ograniczyć dostęp](../conditional-access/app-based-conditional-access.md) do usług w chmurze, takich jak Exchange Online, z zatwierdzonych lub zgodnych aplikacji.

Jeśli pracownicy instalują aplikacje obsługujące MAM, takie jak aplikacje mobilne pakietu Office, aby uzyskiwać dostęp do zasobów firmy, takich jak usługa Exchange Online lub SharePoint Online, a także BYOD (przyłączać własne urządzenie), zalecamy wdrożenie zasad MAM aplikacji w celu zarządzania konfiguracją aplikacji na urządzeniach osobistych bez rejestracji w usłudze MDM, a następnie zaktualizowanie zasad dostępu warunkowego tak, aby zezwalały na dostęp z poziomu

![Kontrola dostępu warunkowego](./media/active-directory-ops-guide/active-directory-ops-img12.png)

Jeśli pracownicy będą instalować aplikacje obsługujące MAM z zasobami firmowymi, a dostęp do nich jest ograniczony na urządzeniach zarządzanych przez usługę Intune, należy rozważyć wdrożenie zasad usługi Application MAM w celu zarządzania konfiguracją aplikacji dla urządzeń osobistych oraz aktualizacji zasad dostępu warunkowego tak, aby zezwalać na dostęp tylko od klientów obsługujących MAM.

### <a name="conditional-access-implementation"></a>Implementacja dostępu warunkowego

Dostęp warunkowy jest ważnym narzędziem do ulepszania stan zabezpieczeń organizacji. W związku z tym ważne są następujące najlepsze rozwiązania:

- Upewnij się, że wszystkie aplikacje SaaS mają zastosowaną co najmniej jedną zasadę
- Należy unikać łączenia filtru **wszystkie aplikacje** z kontrolką **bloku** , aby uniknąć ryzyka związanego z blokowaniem
- Unikaj używania **wszystkich użytkowników** jako filtru i przypadkowo Dodaj **Gości**
- **Przeprowadź migrację wszystkich "starszych" zasad do Azure Portal**
- Przechwyć wszystkie kryteria dla użytkowników, urządzeń i aplikacji
- Korzystanie z zasad dostępu warunkowego w celu [zaimplementowania usługi MFA](../conditional-access/plan-conditional-access.md)zamiast korzystania z usługi **MFA dla poszczególnych użytkowników**
- Mają niewielki zestaw podstawowych zasad, które mogą być stosowane do wielu aplikacji
- Zdefiniuj puste grupy wyjątków i Dodaj je do zasad, aby uzyskać strategię wyjątku
- Planowanie kont [szkła z podziałem](../users-groups-roles/directory-admin-roles-secure.md#break-glass-what-to-do-in-an-emergency) bez kontroli MFA
- Zapewnianie spójnego środowiska Microsoft 365 aplikacji klienckich, na przykład zespołów, OneDrive, Outlook itp.) przez implementację tego samego zestawu formantów dla usług, takich jak Exchange Online i SharePoint Online
- Przypisanie do zasad powinno być implementowane za poorednictwem grup, a nie użytkowników
- Wykonaj regularne przeglądy grup wyjątków używanych w zasadach, aby ograniczyć czas, w którym użytkownicy znajdują się w stan zabezpieczeń. Jeśli jesteś właocicielem usługi Azure AD P2, możesz użyć przeglądów dostępu do zautomatyzowania procesu

#### <a name="conditional-access-recommended-reading"></a>Zalecane odczytywanie dostępu warunkowego

- [Najlepsze rozwiązania dotyczące dostępu warunkowego w Azure Active Directory](../conditional-access/best-practices.md)
- [Konfiguracje tożsamości i dostępu do urządzeń](/microsoft-365/enterprise/microsoft-365-policies-configurations)
- [Azure Active Directory informacje o ustawieniach dostępu warunkowego](../conditional-access/concept-conditional-access-conditions.md)
- [Typowe zasady dostępu warunkowego](../conditional-access/concept-conditional-access-policy-common.md)

## <a name="access-surface-area"></a>Obszar powierzchni dostępu

### <a name="legacy-authentication"></a>Starsza wersja uwierzytelniania

Silne poświadczenia, takie jak MFA, nie mogą chronić aplikacji przy użyciu starszych protokołów uwierzytelniania, co sprawia, że jest to preferowany wektor ataków złośliwych podmiotów. Zablokowanie starszego uwierzytelniania ma kluczowe znaczenie dla poprawy zabezpieczeń dostępu stan.

Starsza wersja uwierzytelniania to termin, który odnosi się do protokołów uwierzytelniania używanych przez aplikacje takie jak:

- Starsi klienci pakietu Office, którzy nie korzystają z nowoczesnego uwierzytelniania (na przykład klient pakietu Office 2010)
- Klienci używający protokołów poczty, takich jak IMAP/SMTP/POP

Osoby atakujące zdecydowanie preferują te protokoły — w rzeczywistości niemal [100% ataki z rozpylaczem hasła korzystają ze](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Your-Pa-word-doesn-t-matter/ba-p/731984) starszych protokołów uwierzytelniania. Hakerzy korzystają ze starszych protokołów uwierzytelniania, ponieważ nie obsługują logowania interaktywnego, co jest potrzebne do dodatkowych wyzwań związanych z zabezpieczeniami, takich jak uwierzytelnianie wieloskładnikowe i uwierzytelnianie urządzeń.

Jeśli starsze uwierzytelnianie jest powszechnie używane w danym środowisku, należy zaplanować migrację starszych klientów do klientów obsługujących [nowoczesne uwierzytelnianie](/office365/enterprise/modern-auth-for-office-2013-and-2016) tak szybko, jak to możliwe. W tym samym tokenie, jeśli niektórzy użytkownicy już używają nowoczesnego uwierzytelniania, ale inni nadal korzystają ze starszego uwierzytelniania, należy wykonać następujące czynności, aby zablokować klientów starszego uwierzytelniania:

1. Używaj [raportów działań związanych z logowaniem](../reports-monitoring/concept-sign-ins.md) , aby identyfikować użytkowników, którzy nadal korzystają ze starszej wersji uwierzytelniania i planowania korygowania:

   a. Uaktualnij do nowoczesnych klientów z możliwością uwierzytelniania do odpowiednich użytkowników.
   
   b. Zaplanuj przedział czasu uruchomienie produkcyjne, aby zablokować na kolejne kroki.
   
   c. Zidentyfikuj starsze aplikacje mające twardą zależność od starszego uwierzytelniania. Zobacz Krok 3 poniżej.

2. Wyłącz starsze protokoły w źródle (na przykład skrzynek pocztowych programu Exchange) dla użytkowników, którzy nie korzystają ze starszej wersji uwierzytelniania, aby uniknąć większej ekspozycji.
3. W przypadku pozostałych kont (najlepiej związanych z tożsamościami nieludzkimi, takimi jak konta usług) Użyj [dostępu warunkowego, aby ograniczyć starsze protokoły](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-Conditional-Access-support-for-blocking-legacy-auth-is/ba-p/245417) po uwierzytelnieniu.

#### <a name="legacy-authentication-recommended-reading"></a>Zalecane odczytywanie starszego uwierzytelniania

- [Włączanie lub wyłączanie dostępu POP3 lub IMAP4 do skrzynek pocztowych w programie Exchange Server](/exchange/clients/pop3-and-imap4/configure-mailbox-access?view=exchserver-2019)

### <a name="consent-grants"></a>Udzielanie zgody

W przypadku ataku z nielegalną zgodą osoba atakująca tworzy aplikację zarejestrowaną w usłudze Azure AD, która żąda dostępu do danych, takich jak informacje kontaktowe, poczta e-mail lub dokumenty. Użytkownicy mogą udzielać zgody na złośliwe aplikacje za pośrednictwem ataków wyłudzaniających podczas wydawania złośliwych witryn sieci Web.

Poniżej znajduje się lista aplikacji z uprawnieniami, które mogą być Scrutinize dla usług w chmurze firmy Microsoft:

- Aplikacje z aplikacją lub delegowane \* . Uprawnienia ReadWrite
- Aplikacje z delegowanymi uprawnieniami mogą odczytywać, wysyłać i zarządzać pocztą e-mail w imieniu użytkownika
- Aplikacje, którym przyznano następujące uprawnienia:

| Zasób | Uprawnienie |
| :- | :- |
| Exchange Online | Posiada. AccessAsUser. All |
| | Interfejs. AccessAsUser. All |
| | Poczta. Przeczytaj |
| Microsoft Graph API | Poczta. Przeczytaj |
| | Mail. Read. Shared |
| | Mail. ReadWrite |

- Aplikacje otrzymują pełną personifikację użytkownika zalogowanego użytkownika. Na przykład:

|Zasób | Uprawnienie |
| :- | :- |
| Microsoft Graph API| Katalog. AccessAsUser. All |
| Interfejs API REST platformy Azure | user_impersonation |

Aby uniknąć tego scenariusza, należy odnieść się do [wykrywania i korygowania nielegalnych dotacji do zgody w pakiecie Office 365](/office365/securitycompliance/detect-and-remediate-illicit-consent-grants) w celu identyfikowania i rozwiązywania wszelkich aplikacji z nielegalnymi dotacjami lub aplikacjami, które mają więcej dotacji niż jest to konieczne. Następnie [Usuń](../manage-apps/configure-user-consent.md) samoobsługowe i [Ustal procedury ładu](../manage-apps/configure-admin-consent-workflow.md). Na koniec Zaplanuj regularne przeglądy uprawnień aplikacji i usuń je, gdy nie są potrzebne.

#### <a name="consent-grants-recommended-reading"></a>Zgoda udziela zalecanego odczytu

- [Uprawnienia interfejsu API Microsoft Graph](/graph/permissions-reference)

### <a name="user-and-group-settings"></a>Ustawienia użytkownika i grupy

Poniżej znajdują się ustawienia użytkownika i grupy, które można zablokować w przypadku niejawnego potrzeby prowadzenia działalności:

#### <a name="user-settings"></a>Ustawienia użytkownika

- **Użytkownicy zewnętrzni** — zewnętrzna współpraca może być organicznie w przedsiębiorstwie z usługami takimi jak zespoły, Power BI, SharePoint Online i Azure Information Protection. Jeśli istnieją jawne ograniczenia dotyczące kontrolowania współpracy zewnętrznej inicjowanej przez użytkownika, zaleca się włączenie użytkowników zewnętrznych przy użyciu funkcji [zarządzania prawami usługi Azure AD](../governance/entitlement-management-overview.md) lub kontrolowanej operacji, takiej jak pomoc techniczna. Jeśli nie chcesz zezwalać na współdziałanie z zewnętrznymi elementami organicznymi dla usług, możesz [zablokować możliwość całkowitego zapraszania użytkowników zewnętrznych](../external-identities/delegate-invitations.md). Alternatywnie można również [zezwalać na określone domeny i blokować](../external-identities/allow-deny-list.md) je w zaproszeniach użytkowników zewnętrznych.
- **Rejestracje aplikacji** — Jeśli rejestracje aplikacji są włączone, użytkownicy końcowi mogą dołączać aplikacje i przyznawać dostęp do swoich danych. Typowym przykładem rejestracji aplikacji jest umożliwienie użytkownikom dodatków plug-in programu Outlook lub asystentów głosowych, takich jak Alexa i Siri, odczytywanie wiadomości e-mail i kalendarza oraz wysyłanie wiadomości e-mail w ich imieniu. Jeśli klient zdecyduje się wyłączyć rejestrację aplikacji, zespoły InfoSec i IAM muszą być objęte zarządzaniem wyjątkami (rejestracje aplikacji, które są potrzebne w oparciu o wymagania biznesowe), ponieważ wymagają one zarejestrowania aplikacji przy użyciu konta administratora, a najprawdopodobniej wymagają projektowania procesu operacjonalizować proces.
- **Portal administracyjny** — organizacje mogą zablokować blok usługi Azure ad w Azure Portal tak, aby inni administratorzy nie mogli uzyskiwać dostępu do usługi Azure ad w Azure Portal i odmylić. Przejdź do ustawień użytkownika w portalu zarządzania usługi Azure AD, aby ograniczyć dostęp:

![Portal administracyjny z ograniczonym dostępem](./media/active-directory-ops-guide/active-directory-ops-img13.png)

> [!NOTE]
> Administratorzy niebędący administratorami mogą nadal uzyskać dostęp do interfejsów zarządzania usługi Azure AD za pośrednictwem wiersza polecenia i innych interfejsów programistycznych.

#### <a name="group-settings"></a>Ustawienia grupy

**Samoobsługowe zarządzanie grupami/użytkownicy mogą tworzyć grupy zabezpieczeń/Microsoft 365 grupy.** Jeśli nie ma żadnej obecnej inicjatywy samoobsługowej dla grup w chmurze, klienci mogą zdecydować się na jej wyłączenie do momentu, gdy nie będą gotowi do korzystania z tej funkcji.

#### <a name="groups-recommended-reading"></a>Zalecane odczytywanie grup

- [Co to jest współpraca w usłudze Azure Active Directory B2B?](../external-identities/what-is-b2b.md)
- [Integrowanie aplikacji z Azure Active Directory](../develop/quickstart-register-app.md)
- [Aplikacje, uprawnienia i zgody w Azure Active Directory.](../develop/quickstart-register-app.md)
- [Używanie grup do zarządzania dostępem do zasobów w Azure Active Directory](./active-directory-manage-groups.md)
- [Konfigurowanie samoobsługowego zarządzania dostępem do aplikacji w Azure Active Directory](../users-groups-roles/groups-self-service-management.md)

### <a name="traffic-from-unexpected-locations"></a>Ruch z nieoczekiwanych lokalizacji

Osoby atakujące pochodzą z różnych części świata. Zarządzanie tym ryzykiem przy użyciu zasad dostępu warunkowego z lokalizacją jako warunek. [Warunek lokalizacji](../conditional-access/location-condition.md) zasad dostępu warunkowego umożliwia zablokowanie dostępu do lokalizacji z tego miejsca, w którym nie istnieje powód biznesowy, z którego można się zalogować.

![Utwórz nową nazwę lokalizacji](./media/active-directory-ops-guide/active-directory-ops-img14.png)

Jeśli jest dostępna, użyj rozwiązania do zarządzania informacjami i zdarzeniami zabezpieczeń (SIEM) w celu analizowania i znajdowania wzorców dostępu między regionami. Jeśli nie korzystasz z produktu SIEM lub nie pozyskasz informacji o uwierzytelnianiu z usługi Azure AD, zalecamy używanie [Azure monitor](../../azure-monitor/overview.md) do identyfikowania wzorców dostępu między regionami.

## <a name="access-usage"></a>Użycie dostępu

### <a name="azure-ad-logs-archived-and-integrated-with-incident-response-plans"></a>Dzienniki usługi Azure AD zarchiwizowane i zintegrowane z planami reagowania na zdarzenia

Mając dostęp do działania związanego z logowaniem, inspekcje i zdarzenia związane z ryzykiem dla usługi Azure AD mają kluczowe znaczenie dla rozwiązywania problemów, analizy użycia i badań dowodowych. Usługa Azure AD zapewnia dostęp do tych źródeł za pomocą interfejsów API REST, które mają ograniczony okres przechowywania. System informacji o zabezpieczeniach i systemie zarządzania zdarzeniami (SIEM) albo równoważna technologia archiwizowania to klucz służący do długoterminowego przechowywania inspekcji i możliwości obsługi. Aby włączyć długoterminowe przechowywanie dzienników usługi Azure AD, należy dodać je do istniejącego rozwiązania SIEM lub użyć [Azure monitor](../reports-monitoring/concept-activity-logs-azure-monitor.md). Archiwizuj dzienniki, które mogą być używane jako część planów i badań odpowiedzi na zdarzenia.

#### <a name="logs-recommended-reading"></a>Rejestruje zalecaną odczyt

- [Dokumentacja interfejsu API inspekcji Azure Active Directory](/graph/api/resources/directoryaudit?view=graph-rest-beta%3fview%3dgraph-rest-beta)
- [Dokumentacja interfejsu API raportów działań związanych z logowaniem Azure Active Directory](/graph/api/resources/signin?view=graph-rest-beta%3fview%3dgraph-rest-beta)
- [Pobieranie danych przy użyciu interfejsu API raportowania usługi Azure AD z certyfikatami](../reports-monitoring/tutorial-access-api-with-certificates.md)
- [Microsoft Graph Azure Active Directory Identity Protection](../identity-protection/howto-identity-protection-graph-api.md)
- [Dokumentacja interfejsu API działania zarządzania pakietu Office 365](/office/office-365-management-api/office-365-management-activity-api-reference)
- [Korzystanie z pakietu zawartości usługi Power BI dla usługi Azure Active Directory](../reports-monitoring/howto-use-azure-monitor-workbooks.md)

## <a name="summary"></a>Podsumowanie

Istnieją 12 aspektów bezpiecznej infrastruktury tożsamości. Ta lista pomoże Ci dodatkowo zabezpieczyć i zarządzać poświadczeniami, definiować środowisko uwierzytelniania, przypisywać delegatów, mierzyć użycie i definiować zasady dostępu w oparciu o stan zabezpieczeń przedsiębiorstwa.

- Przypisywanie właścicieli do kluczowych zadań.
- Zaimplementuj rozwiązania wykrywające słabe lub wycieki hasła, zwiększanie możliwości zarządzania hasłami i ich ochrony oraz bardziej bezpieczny dostęp użytkowników do zasobów.
- Zarządzanie tożsamościami urządzeń w celu ochrony zasobów w dowolnym momencie i z dowolnej lokalizacji.
- Zaimplementuj uwierzytelnianie bezhasłem.
- Zapewnienie standardowego mechanizmu logowania jednokrotnego w całej organizacji.
- Migrowanie aplikacji z AD FS do usługi Azure AD w celu zapewnienia lepszych zabezpieczeń i bardziej spójnego zarządzania.
- Przypisz użytkowników do aplikacji przy użyciu grup, aby zapewnić większą elastyczność i możliwość zarządzania w odpowiedniej skali.
- Konfigurowanie zasad dostępu opartych na ryzyku.
- Zablokuj starsze protokoły uwierzytelniania.
- Wykrywaj i Koryguj nielegalne dotacje zgody.
- Zablokuj ustawienia użytkowników i grup.
- Umożliwia długoterminowe przechowywanie dzienników usługi Azure AD na potrzeby rozwiązywania problemów, analizy użycia i dowodowych.

## <a name="next-steps"></a>Następne kroki

Rozpocznij pracę z [testami operacyjnymi i akcjami nadzoru tożsamości](active-directory-ops-guide-govern.md).