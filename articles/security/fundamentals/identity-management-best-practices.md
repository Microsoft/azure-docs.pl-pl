---
title: Najlepsze rozwiązania dotyczące zabezpieczeń usługi Azure Identity & Access | Microsoft Docs
description: Ten artykuł zawiera zestaw najlepszych rozwiązań związanych z zarządzaniem tożsamościami i kontrolą dostępu przy użyciu wbudowanych funkcji platformy Azure.
services: security
documentationcenter: na
author: terrylanfear
manager: RKarlin
editor: TomSh
ms.assetid: 07d8e8a8-47e8-447c-9c06-3a88d2713bc1
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2019
ms.author: terrylan
ms.openlocfilehash: b609d8389504eb10dd35a5ab74b0c198d3da57a6
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/30/2021
ms.locfileid: "99090496"
---
# <a name="azure-identity-management-and-access-control-security-best-practices"></a>Najlepsze rozwiązania dotyczące zabezpieczeń usługi Azure Identity Management i kontroli dostępu

W tym artykule omówiono zbieranie najlepszych rozwiązań dotyczących zabezpieczeń usługi Azure Identity Management i kontroli dostępu. Te najlepsze rozwiązania wynikają z naszych doświadczeń związanych z [usługą Azure AD](../../active-directory/fundamentals/active-directory-whatis.md) i środowiskami klientów.

Dla każdego najlepszego rozwiązania wyjaśniono:

* Najlepsze rozwiązanie to
* Dlaczego chcesz włączyć to najlepsze rozwiązanie
* Co może być wynikiem, jeśli nie można włączyć najlepszego rozwiązania
* Możliwe alternatywy dla najlepszych rozwiązań
* Jak można dowiedzieć się, jak włączyć najlepsze rozwiązanie

Ten artykuł dotyczący najlepszych rozwiązań w zakresie zabezpieczeń usługi Azure Identity Management i kontroli dostępu jest oparty na jednomyślnych opiniach i zestawach funkcji platformy Azure, ponieważ istnieją one w momencie zapisania tego artykułu.

Zamiarem zapisania tego artykułu jest przedstawienie ogólnych informacji o bardziej niezawodnej stan zabezpieczeń po wdrożeniu zgodnie z naszymi "[5 krokami do zabezpieczenia infrastruktury tożsamości](steps-secure-identity.md)", które przeprowadzą Cię przez niektóre z naszych podstawowych funkcji i usług.

Opinie i technologie zmieniają się wraz z upływem czasu, a ten artykuł zostanie regularnie zaktualizowany w celu odzwierciedlenia tych zmian.

Najlepsze rozwiązania w zakresie zabezpieczeń dotyczące usługi Azure Identity Management i kontroli dostępu omówione w tym artykule obejmują:

* Traktuj tożsamość jako podstawowy obwód zabezpieczeń
* Scentralizowane zarządzanie tożsamościami
* Zarządzanie połączonymi dzierżawcami
* Włącz logowanie jednokrotne
* Włącz dostęp warunkowy
* Planowanie rutynowych usprawnień zabezpieczeń
* Włączanie zarządzania hasłami
* Wymuś weryfikację wieloskładnikowe dla użytkowników
* Korzystanie z kontroli dostępu opartej na rolach
* Mniejsze narażenie na konta uprzywilejowane
* Lokalizacje formantów, w których znajdują się zasoby
* Uwierzytelnianie magazynu za pomocą usługi Azure AD

## <a name="treat-identity-as-the-primary-security-perimeter"></a>Traktuj tożsamość jako podstawowy obwód zabezpieczeń

Wiele rozważy tożsamość jako główny obwód dla bezpieczeństwa. Jest to przesunięcie od tradycyjnego skoncentrowania się na zabezpieczeniach sieci. Sieci obwodów są coraz bardziej porowate i ochrona obwodowa nie może być skuteczna, ponieważ była przed wybuchem urządzeń [BYOD](/mem/intune/fundamentals/byod-technology-decisions) i aplikacji w chmurze.

[Azure Active Directory (Azure AD)](../../active-directory/fundamentals/active-directory-whatis.md) to rozwiązanie platformy Azure służące do zarządzania tożsamościami i dostępem. Azure AD to wielodostępna, oparta na chmurze usługa zarządzania tożsamościami firmy Microsoft. Łączy podstawowe usługi katalogowe z funkcjami zarządzania dostępem do aplikacji i ochrony tożsamości w jednym rozwiązaniu.

W poniższych sekcjach przedstawiono najlepsze rozwiązania dotyczące zabezpieczeń tożsamości i dostępu przy użyciu usługi Azure AD.

**Najlepsze rozwiązanie**: centrum kontroli zabezpieczeń i wykrywanie między tożsamościami użytkowników i usług.
**Szczegóły**: Użyj usługi Azure AD, aby rozmieszczono kontrolki i tożsamości.

## <a name="centralize-identity-management"></a>Scentralizowane zarządzanie tożsamościami

W scenariuszu [tożsamości hybrydowej](https://resources.office.com/ww-landing-M365E-EMS-IDAM-Hybrid-Identity-WhitePaper.html?) zalecamy integrację katalogów lokalnych i w chmurze. Integracja umożliwia zespołowi IT zarządzanie kontami z jednej lokalizacji, niezależnie od tego, gdzie zostało utworzone konto. Integracja pomaga również zwiększyć produktywność użytkowników, zapewniając wspólną tożsamość na potrzeby uzyskiwania dostępu do zasobów w chmurze i lokalnych.

**Najlepsze rozwiązanie**: Ustanów pojedyncze wystąpienie usługi Azure AD. Spójność i pojedyncze źródła autorytatywne spowodują zwiększenie przejrzystości i zmniejszenie zagrożeń bezpieczeństwa przed błędami i złożonością konfiguracji.
**Szczegóły**: Wyznacz pojedynczy katalog usługi Azure AD jako źródło autorytatywne dla kont firmowych i organizacyjnych.

**Najlepsze rozwiązanie**: Integrowanie katalogów lokalnych z usługą Azure AD.  
**Szczegóły**: Użyj [Azure AD Connect](../../active-directory/hybrid/whatis-hybrid-identity.md) do synchronizowania katalogu lokalnego z katalogiem w chmurze.

> [!Note]
> Istnieją [czynniki wpływające na wydajność Azure AD Connect](../../active-directory/hybrid/plan-connect-performance-factors.md). Upewnij się, że Azure AD Connect ma wystarczającą pojemność, aby zapewnić, że systemy nie zakłócają bezpieczeństwa i produktywności. W dużych lub złożonych organizacjach (organizacjach aprowizacji więcej niż 100 000 obiektów) powinna postępować zgodnie z [zaleceniami](../../active-directory/hybrid/whatis-hybrid-identity.md) , aby zoptymalizować ich implementację Azure AD Connect.

**Najlepsze rozwiązanie**: nie Synchronizuj kont z usługą Azure AD, które mają wysokie uprawnienia w istniejącym wystąpieniu Active Directory.
**Szczegóły**: nie zmieniaj domyślnej [konfiguracji Azure AD Connect](../../active-directory/hybrid/how-to-connect-sync-configure-filtering.md) , która filtruje te konta. Ta konfiguracja ogranicza ryzyko źródłami ataków wystawiania z chmury do zasobów lokalnych (co może spowodować utworzenie poważnych zdarzeń).

**Najlepsze rozwiązanie**: włączenie synchronizacji skrótów haseł.  
**Szczegóły**: synchronizacja skrótów haseł jest funkcją służącą do synchronizacji skrótów haseł użytkowników z wystąpienia lokalnego Active Directory do wystąpienia usługi Azure AD opartego na chmurze. Ta synchronizacja pomaga chronić przed nieujawnionymi poświadczeniami odtwarzanymi przed poprzednimi atakami.

Nawet jeśli zdecydujesz się na korzystanie z Federacji z Active Directory Federation Services (AD FS) lub innych dostawców tożsamości, możesz opcjonalnie skonfigurować synchronizację skrótów haseł jako kopię zapasową na wypadek awarii serwerów lokalnych lub przeprowadzenia tymczasowej niedostępności. Ta Synchronizacja umożliwia użytkownikom logowanie się do usługi za pomocą tego samego hasła, którego używają do logowania się do lokalnego wystąpienia Active Directory. Umożliwia także ochronę tożsamości wykrywające złamane poświadczenia, porównując zsynchronizowane skróty haseł z hasłami znanymi jako zagrożone, jeśli użytkownik użył tego samego adresu e-mail i hasła w innych usługach, które nie są połączone z usługą Azure AD.

Aby uzyskać więcej informacji, zobacz [implementowanie synchronizacji skrótów haseł przy użyciu synchronizacji Azure AD Connect](../../active-directory/hybrid/how-to-connect-password-hash-synchronization.md).

**Najlepsze rozwiązanie**: w przypadku tworzenia nowych aplikacji należy użyć usługi Azure AD do uwierzytelniania.
**Szczegóły**: Użyj odpowiednich funkcji do obsługi uwierzytelniania:

  - Usługa Azure AD dla pracowników
  - [Usługa Azure AD B2B](../../active-directory/external-identities/index.yml) dla użytkowników-Gości i partnerów zewnętrznych
  - [Azure AD B2C](../../active-directory-b2c/index.yml) , aby kontrolować sposób tworzenia konta, logowania i zarządzania profilami klientów podczas korzystania z aplikacji

Organizacje, które nie integrują swojej tożsamości lokalnej z tożsamościami w chmurze, mogą mieć większy narzut na zarządzanie kontami. Ten koszt zwiększa prawdopodobieństwo wystąpienia błędów i naruszeń zabezpieczeń.

> [!Note]
> Należy wybrać katalogi, w których znajdują się konta krytyczne, oraz określić, czy używana stacja robocza stacji roboczej jest zarządzana przez nowe usługi w chmurze czy istniejące. Korzystanie z istniejących procesów zarządzania i inicjowania obsługi tożsamości może zmniejszyć ryzyko, ale może również stworzyć ryzyko naruszenia konta lokalnego i przestawienie go do chmury. Możesz chcieć użyć innej strategii dla różnych ról (na przykład administratorów IT i administratorów jednostek roboczych). Dostępne są dwie opcje. Pierwsza opcja polega na utworzeniu kont usługi Azure AD, które nie są zsynchronizowane z lokalnym wystąpieniem Active Directory. Dołącz do usługi Azure AD swoją administracyjną stację roboczą, którą możesz zarządzać i poprawiać przy użyciu Microsoft Intune. Druga opcja polega na użyciu istniejących kont administratora przez synchronizację z lokalnym wystąpieniem Active Directory. Użyj istniejących stacji roboczych w domenie Active Directory na potrzeby zarządzania i zabezpieczeń.

## <a name="manage-connected-tenants"></a>Zarządzanie połączonymi dzierżawcami
Twoja organizacja zabezpieczeń wymaga wglądu w ocenę ryzyka i ustalenia, czy stosowane są zasady organizacji i wymagania prawne. Należy upewnić się, że organizacja zabezpieczeń ma wgląd we wszystkie subskrypcje połączone ze środowiskiem produkcyjnym i siecią (za pośrednictwem [usługi Azure ExpressRoute](../../expressroute/expressroute-introduction.md) lub [sieci VPN typu lokacja-lokacja](../../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)). [Administrator globalny](../../active-directory/roles/permissions-reference.md#global-administrator-permissions) w usłudze Azure AD może podnieść poziom dostępu do roli [administratora dostępu użytkowników](../../role-based-access-control/built-in-roles.md#user-access-administrator) i zobaczyć wszystkie subskrypcje i grupy zarządzane połączone ze środowiskiem.

Zobacz [Podnieś poziom dostępu do zarządzania wszystkimi subskrypcjami i grupami zarządzania platformy Azure](../../role-based-access-control/elevate-access-global-admin.md) , aby upewnić się, że użytkownik i Twoja grupa zabezpieczeń mogą wyświetlać wszystkie subskrypcje lub grupy zarządzania połączone ze środowiskiem. Ten podwyższony poziom dostępu należy usunąć po przeprowadzeniu oceny ryzyka.

## <a name="enable-single-sign-on"></a>Włącz logowanie jednokrotne

W przypadku urządzeń, aplikacji i usług w chmurze mobilnej na urządzeniach przenośnych należy włączyć funkcję logowania jednokrotnego (SSO, Single Sign-on) z dowolnego miejsca, dzięki czemu użytkownicy mogą pracować w dowolnym miejscu i czasie. Jeśli masz wiele rozwiązań do zarządzania tożsamościami, będzie to problem z uprawnieniami administracyjnymi, ale również dla użytkowników, którzy muszą pamiętać wiele haseł.

Korzystając z tego samego rozwiązania tożsamości dla wszystkich aplikacji i zasobów, możesz uzyskać Logowanie jednokrotne. Użytkownicy mogą używać tego samego zestawu poświadczeń do logowania się i uzyskiwania dostępu do potrzebnych zasobów, niezależnie od tego, czy zasoby znajdują się lokalnie, czy w chmurze.

**Najlepsze rozwiązanie**: Włącz logowanie jednokrotne.  
**Szczegóły**: usługa Azure AD [rozszerza Active Directory lokalne](../../active-directory/hybrid/whatis-hybrid-identity.md) do chmury. Użytkownicy mogą używać swoich podstawowych kont służbowych dla urządzeń przyłączonych do domeny, zasobów firmy i wszystkich aplikacji sieci Web i SaaS, których potrzebują do wykonywania swoich zadań. Użytkownicy nie muszą pamiętać wielu zestawów nazw użytkowników i haseł, a dostęp do aplikacji może być inicjowany automatycznie (lub cofany) w zależności od przynależności do grupy organizacji i ich stanu jako pracownika. Możesz również kontrolować dostęp do aplikacji galerii lub własnych aplikacji lokalnych opracowanych i opublikowanych za pośrednictwem [serwera proxy aplikacji usługi Azure AD](../../active-directory/manage-apps/application-proxy.md).

Użyj logowania jednokrotnego, aby umożliwić użytkownikom dostęp do swoich [aplikacji SaaS](../../active-directory/manage-apps/what-is-single-sign-on.md) w oparciu o konto służbowe w usłudze Azure AD. Ma to zastosowanie nie tylko w przypadku aplikacji Microsoft SaaS, ale również innych aplikacji, takich jak [Google Apps](../../active-directory/saas-apps/google-apps-tutorial.md) i [Salesforce](../../active-directory/saas-apps/salesforce-tutorial.md). Aplikację można skonfigurować tak, aby korzystała z usługi Azure AD jako dostawcy [tożsamości opartego na protokole SAML](../../active-directory/fundamentals/active-directory-whatis.md) . Jako kontrola zabezpieczeń usługa Azure AD nie wydaje tokena, który pozwala użytkownikom na logowanie się do aplikacji, chyba że udzielono im dostępu za pomocą usługi Azure AD. Dostęp można udzielić bezpośrednio lub za pomocą grupy, do której użytkownicy są członkami.

Organizacje, które nie tworzą wspólnej tożsamości do nawiązywania rejestracji Jednokrotnej dla swoich użytkowników i aplikacji, są bardziej ujawniane w scenariuszach, w których użytkownicy mają wiele haseł. Te scenariusze zwiększają prawdopodobieństwo ponownego użycia haseł przez użytkowników lub przy użyciu słabych haseł.

## <a name="turn-on-conditional-access"></a>Włącz dostęp warunkowy

Użytkownicy mogą uzyskiwać dostęp do zasobów organizacji przy użyciu różnych urządzeń i aplikacji z dowolnego miejsca. Jako administrator IT chcesz upewnić się, że te urządzenia spełniają standardy dotyczące zabezpieczeń i zgodności. Tylko skoncentrowanie się na tym, kto może uzyskać dostęp do zasobu, nie jest już wystarczające.

Aby zrównoważyć bezpieczeństwo i produktywność, należy wziąć pod uwagę sposób uzyskiwania dostępu do zasobów przed podjęciem decyzji o kontroli dostępu. Za pomocą dostępu warunkowego usługi Azure AD można rozwiązać ten wymóg. Przy użyciu dostępu warunkowego można podejmować zautomatyzowane decyzje dotyczące kontroli dostępu w oparciu o warunki dostępu do aplikacji w chmurze.

**Najlepsze rozwiązanie**: zarządzanie dostępem do zasobów firmy i kontrolowanie dostępu do nich.  
**Szczegóły**: Skonfiguruj typowe [zasady dostępu warunkowego](../../active-directory/conditional-access/concept-conditional-access-policy-common.md) usługi Azure AD na podstawie grupy, lokalizacji i czułości aplikacji dla aplikacji SaaS i usługi Azure AD — połączone aplikacje.

**Najlepsze rozwiązanie**: Blokuj starsze protokoły uwierzytelniania.
**Szczegóły**: osoby atakujące wykorzystują luki w starszych protokołach codziennie, szczególnie w przypadku ataków z rozpylaczem hasła. Skonfiguruj dostęp warunkowy, aby [blokować starsze protokoły](../../active-directory/conditional-access/howto-conditional-access-policy-block-legacy.md).

## <a name="plan-for-routine-security-improvements"></a>Planowanie rutynowych usprawnień zabezpieczeń

Zabezpieczenia są zawsze zmieniane i ważne jest, aby kompilować do struktury zarządzania chmurą i tożsamością w sposób, który umożliwia regularne wyświetlanie wzrostu i odnajdywanie nowych sposobów zabezpieczania środowiska.

Ocena zabezpieczeń tożsamości to zestaw zalecanych kontroli bezpieczeństwa, które firma Microsoft publikuje w celu zapewnienia liczbowego wyniku, aby obiektywnie mierzyć stan zabezpieczeń i ułatwić planowanie przyszłych ulepszeń zabezpieczeń. Możesz również wyświetlić wyniki w porównaniu do tych w innych branżach, a także własne trendy w czasie.

**Najlepsze rozwiązanie**: Planowanie rutynowych przeglądów i ulepszeń zabezpieczeń w oparciu o najlepsze rozwiązania w branży.
**Szczegóły**: funkcja oceny zabezpieczeń tożsamości umożliwia określenie ulepszeń w miarę upływu czasu.

## <a name="enable-password-management"></a>Włączanie zarządzania hasłami

Jeśli masz wielu dzierżawców lub chcesz umożliwić użytkownikom [Resetowanie swoich haseł](../../active-directory/user-help/active-directory-passwords-update-your-own-password.md), ważne jest, aby użyć odpowiednich zasad zabezpieczeń, aby zapobiec nadużyciu.

**Najlepsze rozwiązanie**: Skonfiguruj Samoobsługowe resetowanie hasła (SSPR) dla użytkowników.  
**Szczegóły**: Użyj funkcji samoobsługowego [resetowania hasła](../../active-directory-b2c/user-flow-self-service-password-reset.md) w usłudze Azure AD.

**Najlepsze rozwiązanie**: Monitoruj, jak i czy SSPR jest naprawdę używany.  
**Szczegóły**: monitoruj użytkowników logujących się za pomocą [raportu działanie rejestracji resetowania haseł](../../active-directory/authentication/howto-sspr-reporting.md)usługi Azure AD. Funkcja raportowania dostępna w usłudze Azure AD pomaga odpowiedzieć na pytania przy użyciu prekompilowanych raportów. Jeśli masz odpowiednie licencje, możesz również utworzyć niestandardowe zapytania.

**Najlepsze rozwiązanie**: rozszerzone zasady haseł oparte na chmurze w infrastrukturze lokalnej.
**Szczegóły**: Podnieś poziom zasad haseł w organizacji, wykonując te same operacje sprawdzania dla lokalnych zmian haseł, jak w przypadku zmian haseł opartych na chmurze. Zainstaluj [ochronę hasłem usługi Azure AD](../../active-directory/authentication/concept-password-ban-bad.md) dla agentów systemu Windows Server Active Directory lokalnie, aby zwiększyć listę zabronionych haseł do istniejącej infrastruktury. Użytkownicy i Administratorzy, którzy zmieniają, ustawiają lub resetowania haseł lokalnych, muszą być zgodni z tymi samymi zasadami haseł co użytkownicy tylko w chmurze.

## <a name="enforce-multi-factor-verification-for-users"></a>Wymuś weryfikację wieloskładnikowe dla użytkowników

Zalecamy wymaganie weryfikacji dwuetapowej dla wszystkich użytkowników. Obejmuje to administratorów i innych użytkowników w organizacji, którzy mogą mieć znaczący wpływ na bezpieczeństwo kont (na przykład funkcjonariusze finansów).

Istnieje wiele opcji wymagających weryfikacji dwuetapowej. Najlepsza opcja zależy od Twoich celów, uruchomionej wersji usługi Azure AD i programu licencjonowania. Zobacz, [jak wymagać weryfikacji dwuetapowej dla użytkownika](../../active-directory/authentication/howto-mfa-userstates.md) , aby określić najlepszą dla siebie opcję. Aby uzyskać więcej informacji o licencjach i cenach, zobacz strony z cennikiem usługi [Azure AD](https://azure.microsoft.com/pricing/details/active-directory/) i [usługi Azure AD Multi-Factor Authentication](https://azure.microsoft.com/pricing/details/multi-factor-authentication/) .

Poniżej przedstawiono opcje i korzyści dotyczące włączania weryfikacji dwuetapowej:

**Opcja 1**: Włącz usługę MFA dla wszystkich użytkowników i metod logowania przy **użyciu ustawień domyślnych** zabezpieczeń usługi Azure AD: Ta opcja umożliwia łatwe i szybkie Wymuszanie uwierzytelniania wieloskładnikowego dla wszystkich użytkowników w środowisku przy użyciu rygorystycznych zasad:

* Wyzwania dotyczące kont administracyjnych i administracyjnych mechanizmów logowania
* Wymagaj żądania MFA za pośrednictwem Microsoft Authenticator dla wszystkich użytkowników
* Ogranicz starsze protokoły uwierzytelniania.

Ta metoda jest dostępna dla wszystkich warstw licencjonowania, ale nie może być mieszana z istniejącymi zasadami dostępu warunkowego. Więcej informacji można znaleźć w [ustawieniach zabezpieczeń usługi Azure AD](../../active-directory/fundamentals/concept-fundamentals-security-defaults.md) .

**Opcja 2**: [Włączanie Multi-Factor Authentication przez zmianę stanu użytkownika](../../active-directory/authentication/howto-mfa-userstates.md).   
**Korzyść**: jest to tradycyjna metoda wymagająca weryfikacji dwuetapowej. Działa ona zarówno w przypadku usługi [Azure AD Multi-Factor Authentication w chmurze, jak i na platformie azure serwer Multi-Factor Authentication](../../active-directory/authentication/concept-mfa-howitworks.md). Użycie tej metody wymaga, aby użytkownicy przeprowadzali weryfikację dwuetapową przy każdym logowaniu i zastępują zasady dostępu warunkowego.

Aby określić, gdzie należy włączyć Multi-Factor Authentication, zobacz, [która wersja usługi Azure AD MFA jest odpowiednia dla mojej organizacji?](../../active-directory/authentication/concept-mfa-howitworks.md).

**Opcja 3**: [Włączanie Multi-Factor Authentication przy użyciu zasad dostępu warunkowego](../../active-directory/authentication/howto-mfa-getstarted.md).
**Korzyść**: Ta opcja umożliwia wyświetlenie monitu o weryfikację dwuetapową w określonych warunkach przy użyciu [dostępu warunkowego](../../active-directory/conditional-access/concept-conditional-access-policy-common.md). Określone warunki mogą być logowaniem użytkowników z różnych lokalizacji, niezaufanych urządzeń lub aplikacji, które są uważane za ryzykowne. Definiowanie określonych warunków, w których wymagana jest weryfikacja dwuetapowa, pozwala uniknąć stałego monitowania użytkowników, co może być nieprzyjemnym interfejsem użytkownika.

Jest to najbardziej elastyczny sposób na umożliwienie weryfikacji dwuetapowej dla użytkowników. Włączenie zasad dostępu warunkowego działa tylko w przypadku Multi-Factor Authentication usługi Azure AD w chmurze i jest funkcją Premium usługi Azure AD. Więcej informacji na temat tej metody można znaleźć w temacie [wdrażanie opartego na chmurze usługi Azure AD Multi-Factor Authentication](../../active-directory/authentication/howto-mfa-getstarted.md).

**Opcja 4**: Włącz Multi-Factor Authentication przy użyciu zasad dostępu warunkowego, oceniając [zasady dostępu warunkowego opartego na ryzyku](../../active-directory/conditional-access/howto-conditional-access-policy-risk.md).   
**Korzyść**: Ta opcja umożliwia:

* Wykrywaj potencjalne luki w zabezpieczeniach, które mają wpływ na tożsamości w organizacji.
* Skonfiguruj automatyczne odpowiedzi w celu wykrycia podejrzanych działań, które są powiązane z tożsamościami organizacji.
* Zbadaj podejrzane zdarzenia i podejmij odpowiednie działania, aby je rozwiązać.

Ta metoda używa oceny ryzyka Azure AD Identity Protection, aby określić, czy Weryfikacja dwuetapowa jest wymagana na podstawie ryzyka związanego z logowaniem do wszystkich aplikacji w chmurze. Ta metoda wymaga licencjonowania Azure Active Directory P2. Więcej informacji na temat tej metody można znaleźć w [Azure Active Directory Identity Protection](../../active-directory/identity-protection/overview-identity-protection.md).

> [!Note]
> Opcja 2, Włączanie Multi-Factor Authentication przez zmianę stanu użytkownika, przesłania zasady dostępu warunkowego. Ponieważ opcje 3 i 4 korzystają z zasad dostępu warunkowego, nie można użyć opcji 2 z nimi.

Organizacje, które nie dodawaj dodatkowych warstw ochrony tożsamości, takich jak weryfikacja dwuetapowa, są bardziej podatne na ataki kradzieży poświadczeń. Atak kradzieży poświadczeń może prowadzić do złamania danych.

## <a name="use-role-based-access-control"></a>Korzystanie z kontroli dostępu opartej na rolach

Zarządzanie dostępem do zasobów w chmurze ma kluczowe znaczenie dla każdej organizacji korzystającej z chmury. [Kontrola dostępu oparta na rolach (Azure RBAC)](../../role-based-access-control/overview.md) umożliwia zarządzanie, kto ma dostęp do zasobów platformy Azure, co można zrobić z tymi zasobami i jakie obszary mają do nich dostęp.

Wyznaczanie grup lub poszczególnych ról odpowiadających określonym funkcjom na platformie Azure pomaga uniknąć nieporozumień, które mogą prowadzić do błędów ludzi i automatyzacji, które tworzą zagrożenia bezpieczeństwa. Ograniczanie dostępu na podstawie [konieczności znajomości](https://en.wikipedia.org/wiki/Need_to_know) i [najniższych](https://en.wikipedia.org/wiki/Principle_of_least_privilege) zasad zabezpieczeń jest niezbędne dla organizacji, które chcą wymuszać zasady zabezpieczeń dostępu do danych.

Zespół ds. zabezpieczeń wymaga wglądu w zasoby platformy Azure w celu oceny i skorygowania ryzyka. Jeśli zespół ds. zabezpieczeń ma obowiązki operacyjne, potrzebują dodatkowych uprawnień do wykonywania swoich zadań.

Możesz użyć kontroli [RBAC platformy Azure](../../role-based-access-control/overview.md) do przypisywania uprawnień użytkownikom, grupom i aplikacjom w określonym zakresie. Zakresem przypisania roli może być subskrypcja, grupa zasobów lub pojedynczy zasób.

**Najlepsze rozwiązanie**: Rozdzielenie obowiązków w zespole i przyznanie dostępu użytkownikom, których potrzebują do wykonywania swoich zadań. Zamiast udzielać każdemu nieograniczonyemu dostępowi do subskrypcji lub zasobów platformy Azure, Zezwól tylko na określone akcje w określonym zakresie.
**Szczegóły**: do przypisywania uprawnień użytkownikom można używać [wbudowanych ról platformy Azure](../../role-based-access-control/built-in-roles.md) na platformie Azure.

> [!Note]
> Określone uprawnienia tworzą niepotrzebną złożoność i nieporozumień, co jest trudne do naprawienia, ponieważ nie ma obaw o rozdzielenie czegoś. Unikaj uprawnień specyficznych dla zasobów. Zamiast tego należy użyć grup zarządzania dla uprawnień w całej firmie i grup zasobów dla uprawnień w ramach subskrypcji. Unikaj uprawnień specyficznych dla użytkownika. Zamiast tego przypisz dostęp do grup w usłudze Azure AD.

**Najlepsze rozwiązanie**: Przyznaj zespołom ds. zabezpieczeń dostęp do zasobów platformy Azure, aby umożliwić ocenę i korygowanie ryzyka.
**Szczegóły**: Przyznaj zespołom zabezpieczeń rolę [czytnika zabezpieczeń](../../role-based-access-control/built-in-roles.md#security-reader) usługi Azure RBAC. W zależności od zakresu obowiązków można użyć głównej grupy zarządzania lub grupy zarządzania segmentacją:

* **Główna Grupa zarządzania** dla zespołów odpowiedzialnych za wszystkie zasoby przedsiębiorstwa
* **Grupa zarządzania segmentacją** dla zespołów z ograniczonym zakresem (zazwyczaj ze względu na przepisy prawne lub inne granice organizacyjne)

**Najlepsze rozwiązanie**: Przyznaj odpowiednie uprawnienia zespołom ds. zabezpieczeń, którzy mają bezpośrednie obowiązki operacyjne.
**Szczegóły**: Zapoznaj się z wbudowanymi rolami platformy Azure, aby uzyskać odpowiednie przypisanie roli. Jeśli wbudowane role nie są zgodne z konkretnymi potrzebami organizacji, możesz utworzyć [role niestandardowe platformy Azure](../../role-based-access-control/custom-roles.md). Podobnie jak w przypadku ról wbudowanych, można przypisywać niestandardowe role do użytkowników, grup i jednostek usługi w ramach subskrypcji, grupy zasobów i zakresów zasobów.

**Najlepsze rozwiązania**: przyznaj Azure Security Center dostęp do ról zabezpieczeń, które ich potrzebują. Security Center umożliwia zespołom ds. zabezpieczeń szybkie identyfikowanie i korygowanie zagrożeń.
**Szczegóły**: Dodaj zespoły zabezpieczeń z tymi potrzebami do roli administratora usługi Azure RBAC [Security](../../role-based-access-control/built-in-roles.md#security-admin) , aby mogły wyświetlać zasady zabezpieczeń, wyświetlać Stany zabezpieczeń, edytować zasady zabezpieczeń, wyświetlać alerty i zalecenia oraz odrzucać alerty i zalecenia. W tym celu można użyć głównej grupy zarządzania lub grupy zarządzania segmentu w zależności od zakresu obowiązków.

Organizacje, które nie wymuszają kontroli dostępu do danych za pomocą funkcji, takich jak Azure RBAC, mogą uzyskać więcej uprawnień niż jest to konieczne dla użytkowników. Może to prowadzić do złamania danych przez umożliwienie użytkownikom dostępu do typów danych (na przykład dużego wpływu na działalność), które nie powinny być dostępne.

## <a name="lower-exposure-of-privileged-accounts"></a>Mniejsze narażenie na konta uprzywilejowane

Zabezpieczanie uprzywilejowanego dostępu jest najważniejszym pierwszym krokiem do ochrony zasobów firmy. Minimalizacja liczby osób, które mają dostęp do zabezpieczania informacji lub zasobów, zmniejsza ryzyko uzyskania dostępu przez złośliwego użytkownika lub autoryzowany użytkownik przypadkowo wpływa na poufne zasoby.

Konta uprzywilejowane to konta, które administrują systemami IT i zarządzają nimi. Osoby atakujące cybernetycznymi mogą uzyskać dostęp do danych i systemów organizacji. Aby zabezpieczyć uprzywilejowany dostęp, należy odizolować konta i systemy przed ryzykiem, które ma zostać ujawnione złośliwemu użytkownikowi.

Zalecamy opracowanie i przestrzeganie planu w celu zabezpieczenia uprzywilejowanego dostępu przed osobami atakującymi cybernetycznymi. Informacje o tworzeniu szczegółowego planu zabezpieczania tożsamości i dostępu, które są zarządzane lub zgłaszane w usłudze Azure AD, Microsoft Azure, Microsoft 365 i innych usługach w chmurze, można znaleźć w temacie [Zabezpieczanie uprzywilejowanego dostępu dla wdrożeń hybrydowych i w chmurze w usłudze Azure AD](../../active-directory/roles/security-planning.md).

Poniżej przedstawiono podsumowanie najlepszych rozwiązań w [zakresie zabezpieczania uprzywilejowanego dostępu dla wdrożeń hybrydowych i w chmurze w usłudze Azure AD](../../active-directory/roles/security-planning.md):

**Najlepsze rozwiązanie**: zarządzanie, kontrolowanie i monitorowanie dostępu do kont uprzywilejowanych.   
**Szczegóły**: Włącz [Azure AD Privileged Identity Management](../../active-directory/roles/security-planning.md). Po włączeniu Privileged Identity Management będziesz otrzymywać wiadomości e-mail z powiadomieniami o zmianach roli uprzywilejowanego dostępu. Te powiadomienia zapewniają wczesne ostrzeżenie, gdy dodatkowi użytkownicy są dodawani do ról o wysokim poziomie uprawnień w katalogu.

**Najlepsze rozwiązanie**: Upewnij się, że wszystkie krytyczne konta administratorów są zarządzane konta usługi Azure AD.
**Szczegóły**: Usuń wszystkie konta konsumentów z krytycznych ról administratora (na przykład konta Microsoft, takie jak hotmail.com, live.com i Outlook.com).

**Najlepsze rozwiązanie**: Upewnij się, że wszystkie krytyczne role administratorów mają osobne konto do zadań administracyjnych, aby uniknąć wyłudzania informacji i innych ataków w celu naruszenia uprawnień administracyjnych.
**Szczegóły**: Utwórz oddzielne konto administratora, któremu przypisano uprawnienia, które są konieczne do wykonania zadań administracyjnych. Zablokuj korzystanie z tych kont administracyjnych na potrzeby codziennych narzędzi zwiększających produktywność, takich jak Microsoft 365 e-mail lub dowolne przeglądanie sieci Web.

**Najlepsze rozwiązanie**: Identyfikowanie kont i określanie ich kategorii w rolach o wysokim poziomie uprawnień.   
**Szczegóły**: po włączeniu Azure AD Privileged Identity Management Wyświetl użytkowników, którzy znajdują się w roli Administrator globalny, administrator ról uprzywilejowanych i inne role o wysokim poziomie uprawnień. Usuń wszystkie konta, które nie są już potrzebne w tych rolach, i Kategoryzuj pozostałe konta przypisane do ról administratora:

* Przypisane do użytkowników administracyjnych i mogą być używane do celów innych niż administracyjne (na przykład osobista poczta e-mail)
* Przypisane do użytkowników administracyjnych i wyznaczone wyłącznie do celów administracyjnych
* Współużytkowane przez wielu użytkowników
* W przypadku scenariuszy dostępu awaryjnego
* Dla zautomatyzowanych skryptów
* Dla użytkowników zewnętrznych

**Najlepsze rozwiązanie**: Zaimplementuj dostęp "just in Time" (JIT), aby jeszcze bardziej obniżyć czas ekspozycji uprawnień i zwiększyć widoczność do korzystania z kont uprzywilejowanych.   
**Szczegóły**: Azure AD Privileged Identity Management umożliwia:

* Ogranicz użytkownikom możliwość wykonywania tylko tych uprawnień w trybie JIT.
* Przypisywanie ról w skrócie czasu trwania z zachowaniem pewności, że uprawnienia są automatycznie odwoływane.

**Najlepsze rozwiązanie**: Zdefiniuj co najmniej dwa konta dostępu awaryjnego.   
**Szczegóły**: konta dostępu awaryjnego pomagają organizacjom ograniczyć dostęp uprzywilejowany w istniejącym środowisku Azure Active Directory. Te konta są wysoce uprzywilejowane i nie są przypisane do określonych osób. Konta dostępu awaryjnego są ograniczone do scenariuszy, w których nie można używać zwykłych kont administracyjnych. Organizacje muszą ograniczyć użycie konta awaryjnego tylko do niezbędnego czasu.

Oceń konta, które są przypisane lub kwalifikujące się do roli administratora globalnego. Jeśli nie widzisz żadnych kont tylko w chmurze przy użyciu `*.onmicrosoft.com` domeny (przeznaczonej do dostępu awaryjnego), utwórz je. Aby uzyskać więcej informacji, zobacz [Zarządzanie kontami administracyjnymi dostępu awaryjnego w usłudze Azure AD](../../active-directory/roles/security-emergency-access.md).

**Najlepsze rozwiązanie**: w przypadku sytuacji awaryjnej ma miejsce proces "Break Glass".
**Szczegóły**: Postępuj zgodnie z instrukcjami w temacie [Zabezpieczanie uprzywilejowanego dostępu dla wdrożeń hybrydowych i w chmurze w usłudze Azure AD](../../active-directory/roles/security-planning.md).

**Najlepsze rozwiązanie**: Wymagaj, aby wszystkie krytyczne konta administratorów miały hasła (preferowany) lub wymagają Multi-Factor Authentication.
**Szczegóły**: użyj [aplikacji Microsoft Authenticator](../../active-directory/authentication/howto-authentication-passwordless-phone.md) , aby zalogować się do dowolnego konta usługi Azure AD bez użycia hasła. Podobnie jak w [przypadku usługi Windows Hello dla firm](/windows/security/identity-protection/hello-for-business/hello-identity-verification), Microsoft Authenticator używa uwierzytelniania opartego na kluczach, aby włączyć poświadczenia użytkownika powiązane z urządzeniem i korzystać z uwierzytelniania biometrycznego lub numeru PIN.

Wymagaj usługi Azure AD Multi-Factor Authentication podczas logowania dla wszystkich użytkowników, którzy są trwale przypisani do co najmniej jednej roli administratora usługi Azure AD: Administrator globalny, administrator ról uprzywilejowanych, administrator programu Exchange Online i administrator usługi SharePoint Online. Włącz [Multi-Factor Authentication kont administratorów](../../active-directory/authentication/howto-mfa-userstates.md) i upewnij się, że zarejestrowano użytkowników konta administratora.

**Najlepsze rozwiązanie**: w przypadku krytycznych kont administratorów należy dysponować administratorem stacji roboczej, gdzie zadania produkcyjne nie są dozwolone (na przykład przeglądanie i poczta e-mail). Pozwala to chronić konta administratorów przed atakami wykorzystującymi funkcję przeglądania i poczty e-mail oraz znacząco zmniejszają ryzyko wystąpienia poważnych zdarzeń.
**Szczegóły**: Użyj stacji roboczej administratora. Wybierz poziom zabezpieczeń stacji roboczej:

- Wysoce bezpieczne urządzenia biurowe zapewniają zaawansowane zabezpieczenia związane z przeglądaniem i innymi zadaniami produktywności.
- [Stacje robocze dostępu uprzywilejowanego (dostępem uprzywilejowanym)](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/) zapewniają dedykowany system operacyjny, który jest chroniony przed atakami internetowymi i nosicielami zagrożeń w celu wykonywania poufnych zadań.

**Najlepsze rozwiązanie**: anulowanie aprowizacji kont administratorów, gdy pracownicy opuszczają Twoją organizację.
**Szczegóły**: zawiera proces, który wyłącza lub usuwa konta administratorów, gdy pracownicy opuszczają organizację.

**Najlepsze rozwiązanie**: regularne testowanie kont administratorów przy użyciu bieżących technik ataku.
**Szczegóły**: Użyj symulatora ataków Microsoft 365 lub oferty innej firmy, aby uruchomić realistyczne scenariusze ataków w organizacji. Może to pomóc w znalezieniu narażonych użytkowników przed rzeczywistym atakiem.

**Najlepsze rozwiązanie**: należy podjąć kroki w celu ograniczenia najczęściej używanych technik ataków.  
**Szczegóły**: [Zidentyfikuj konta Microsoft w rolach administracyjnych, które muszą zostać przełączone na konta](../../active-directory/roles/security-planning.md#identify-microsoft-accounts-in-administrative-roles-that-need-to-be-switched-to-work-or-school-accounts) służbowe  

[Upewnij się, że konta użytkowników i przekazywanie poczty są przekazywane na konta administratora globalnego](../../active-directory/roles/security-planning.md)  

[Upewnij się, że hasła kont administracyjnych zostały ostatnio zmienione](../../active-directory/roles/security-planning.md#ensure-the-passwords-of-administrative-accounts-have-recently-changed)  

[Włącz synchronizację skrótów haseł](../../active-directory/roles/security-planning.md#turn-on-password-hash-synchronization)  

[Wymagaj Multi-Factor Authentication dla użytkowników we wszystkich uprzywilejowanych rolach, a także dla narażonych użytkowników](../../active-directory/roles/security-planning.md#require-multi-factor-authentication-for-users-in-privileged-roles-and-exposed-users)  

[Uzyskaj Microsoft 365 zabezpieczonych wyników (Jeśli używasz Microsoft 365)](../../active-directory/roles/security-planning.md#obtain-your-microsoft-365-secure-score-if-using-microsoft-365)  

[Zapoznaj się ze wskazówkami dotyczącymi zabezpieczeń Microsoft 365 (jeśli są używane Microsoft 365)](../../active-directory/roles/security-planning.md#review-the-microsoft-365-security-and-compliance-guidance-if-using-microsoft-365)  

[Skonfiguruj monitorowanie działania Microsoft 365 (jeśli jest używany Microsoft 365)](../../active-directory/roles/security-planning.md#configure-microsoft-365-activity-monitoring-if-using-microsoft-365)  

[Ustalanie właścicieli planu reagowania na zdarzenia/sytuacje awaryjne](../../active-directory/roles/security-planning.md#establish-incidentemergency-response-plan-owners)  

[Zabezpieczone konta administracyjne z uprawnieniami lokalnymi](../../active-directory/roles/security-planning.md#turn-on-password-hash-synchronization)

Jeśli nie masz bezpiecznego dostępu uprzywilejowanego, może się zdarzyć, że masz zbyt wielu użytkowników w uprzywilejowanych rolach i jest bardziej narażony na ataki. Złośliwe podmioty, w tym cybernetycznymi osoby atakujące, często są docelowymi kontami administracyjnymi i innymi elementami uprzywilejowanego dostępu, aby uzyskać dostęp do poufnych danych i systemów za pomocą kradzieży poświadczeń.

## <a name="control-locations-where-resources-are-created"></a>Lokalizacje formantów, w których są tworzone zasoby

Umożliwienie operatorom chmury wykonywania zadań i uniemożliwianie ich z Konwencji podziału, które są potrzebne do zarządzania zasobami organizacji, jest bardzo ważne. Organizacje, które chcą kontrolować lokalizacje, w których tworzone są zasoby, powinny mieć twardy kod.

Za pomocą [Azure Resource Manager](../../azure-resource-manager/management/overview.md) można tworzyć zasady zabezpieczeń, których definicje opisują akcje lub zasoby, które są odrzucane. Te definicje zasad należy przypisać w żądanym zakresie, takim jak subskrypcja, Grupa zasobów lub pojedynczy zasób.

> [!NOTE]
> Zasady zabezpieczeń nie są takie same jak w przypadku usługi Azure RBAC. W rzeczywistości korzystają z usługi Azure RBAC, aby autoryzować użytkowników do tworzenia tych zasobów.
>
>

Organizacje, które nie kontrolują sposobu tworzenia zasobów, są bardziej podatne użytkownikom, którzy mogą nadużyć usługi, przez utworzenie większej ilości zasobów niż potrzebują. Zwiększanie funkcjonalności procesu tworzenia zasobów jest ważnym krokiem do zabezpieczania wielodostępnego scenariusza.

## <a name="actively-monitor-for-suspicious-activities"></a>Aktywnie Monitoruj dla podejrzanych działań

Aktywny system monitorowania tożsamości umożliwia szybkie wykrywanie podejrzanych zachowań i wyzwalanie alertu w celu dalszej analizy. W poniższej tabeli przedstawiono dwie możliwości usługi Azure AD, które mogą pomóc organizacjom monitorować ich tożsamości:

**Najlepsze rozwiązanie**: ma metodę identyfikacji:

- Próbuje zalogować się [bez śledzenia](../../active-directory/reports-monitoring/howto-find-activity-reports.md).
- [Wymuszanie](../../active-directory/reports-monitoring/howto-find-activity-reports.md) ataków na poszczególne konta.
- Próbuje zalogować się z wielu lokalizacji.
- Logowania z [zainfekowanych urządzeń](../../active-directory/reports-monitoring/howto-find-activity-reports.md).
- Podejrzane adresy IP.

**Szczegóły**: Korzystaj z [raportów anomalii](../../active-directory/reports-monitoring/overview-reports.md)Azure AD — wersja Premium. Procesy i procedury stosowane w przypadku administratorów IT do uruchamiania tych raportów codziennie lub na żądanie (zazwyczaj w scenariuszu odpowiedzi na zdarzenia).

**Najlepsze rozwiązanie**: posiadanie aktywnego systemu monitorowania, który powiadamia użytkownika o ryzyku i umożliwia dostosowanie poziomu ryzyka (wysoki, średni lub niski) do wymagań firmy.   
**Szczegóły**: Użyj [Azure AD Identity Protection](../../active-directory/identity-protection/overview-identity-protection.md), który flaguje bieżące ryzyko na swoim własnym pulpicie nawigacyjnym i wysyła codzienne powiadomienia dotyczące podsumowania za pośrednictwem poczty e-mail. Aby pomóc w ochronie tożsamości organizacji, można skonfigurować zasady oparte na ryzyku, które automatycznie reagują na wykryte problemy po osiągnięciu określonego poziomu ryzyka.

Organizacje, które nie monitorują aktywnie systemów tożsamości, są zagrożone naruszeniami poświadczeń użytkownika. Bez wiedzy, że podejrzane działania odbywają się za poorednictwem tych poświadczeń, organizacje nie mogą złagodzić tego rodzaju zagrożenia.

## <a name="use-azure-ad-for-storage-authentication"></a>Uwierzytelnianie magazynu za pomocą usługi Azure AD
[Usługa Azure Storage](../../storage/common/storage-auth-aad.md) obsługuje uwierzytelnianie i autoryzację przy użyciu usługi Azure AD dla magazynu obiektów blob i magazynu kolejek. Za pomocą uwierzytelniania w usłudze Azure AD można używać kontroli dostępu opartej na rolach na platformie Azure do przyznawania określonych uprawnień użytkownikom, grupom i aplikacjom w dół do zakresu pojedynczego kontenera obiektów blob lub kolejki.

Zalecamy używanie [usługi Azure AD do uwierzytelniania dostępu do magazynu](https://azure.microsoft.com/blog/azure-storage-support-for-azure-ad-based-access-control-now-generally-available/).

## <a name="next-step"></a>Następny krok

Zobacz [najlepsze rozwiązania i wzorce dotyczące zabezpieczeń platformy Azure](best-practices-and-patterns.md) , aby uzyskać więcej najlepszych rozwiązań w zakresie zabezpieczeń, które są używane podczas projektowania i wdrażania rozwiązań w chmurze oraz zarządzania nimi przy użyciu platformy Azure.