---
title: Szybkie reagowanie na bezpieczne tożsamości za pomocą Azure Active Directory
description: Szybkie reagowanie na zagrożenia przy użyciu tożsamości opartych na chmurze usługi Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 04/27/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: davidspo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 43b883cac7b970488a30116bc06efc8663766629
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/22/2020
ms.locfileid: "92370206"
---
# <a name="rapidly-respond-to-secure-identities-with-azure-ad"></a>Szybkie reagowanie na bezpieczne tożsamości za pomocą usługi Azure AD

Może wydawać się zniechęcające próba zabezpieczenia Twoich pracowników w dzisiejszym świecie, szczególnie w przypadku konieczności szybkiego reagowania i zapewnienia szybkiego dostępu do wielu usług. Ten artykuł ma na celu przedstawienie zwięzłej listy wszystkich działań, które należy podjąć, co ułatwia identyfikację i określanie priorytetów kolejności wdrażania funkcji usługi Azure AD na podstawie posiadanego typu licencji. Usługa Azure AD oferuje wiele funkcji i oferuje wiele warstw zabezpieczeń dla tożsamości, które mogą być czasami przeciążone. Wiele organizacji znajduje się już w chmurze lub szybko się przenosi do chmury. ten dokument ma na celu umożliwienie szybkiego wdrażania usług przy użyciu zabezpieczania tożsamości jako głównej uwagi. 

Każda tabela zawiera spójne zalecenie dotyczące zabezpieczeń, chroniąc zarówno tożsamości administratorów, jak i użytkowników z głównych ataków w zabezpieczeniach (powtarzanie naruszenia, phishing i rozpylanie hasła) przy jednoczesnym zminimalizowaniu wpływu użytkowników i ulepszaniu środowiska użytkownika. 

Wskazówki te umożliwiają także administratorom skonfigurowanie dostępu do SaaS i aplikacji lokalnych w bezpieczny i chroniony sposób i ma zastosowanie do tożsamości w chmurze lub hybrydowej (zsynchronizowanych) i ma zastosowanie do użytkowników pracujących zdalnie lub w biurze.

Ta lista kontrolna ułatwi szybkie wdrożenie krytycznych zalecanych akcji w celu natychmiastowego zapewnienia ochrony organizacji przez wyjaśnienie, jak:

- Wzmocnij swoje poświadczenia.
- Zmniejsz obszar narażony na ataki.
- Automatyzowanie odpowiedzi na zagrożenia.
- Korzystaj z analizy chmury.
- Włącz samoobsługowe korzystanie z użytkowników końcowych.

## <a name="prerequisites"></a>Wymagania wstępne

W tym przewodniku przyjęto założenie, że w usłudze Azure AD już istnieją tożsamości tylko w chmurze lub hybrydowe. Aby uzyskać pomoc dotyczącą wybierania typu tożsamości, zobacz artykuł [Wybierz właściwą metodę uwierzytelniania dla Azure Active Directory rozwiązanie do tworzenia tożsamości hybrydowej](../hybrid/choose-ad-authn.md) 

## <a name="summary"></a>Podsumowanie

Istnieje wiele aspektów bezpiecznej infrastruktury tożsamości, ale ta lista kontrolna koncentruje się na bezpiecznej i bezpiecznej infrastrukturze tożsamości umożliwiającej użytkownikom zdalne działanie. Zabezpieczanie tożsamości jest tylko częścią scenariusza zabezpieczeń, dlatego Ochrona danych, aplikacji i urządzeń powinna być uwzględniana.

### <a name="guidance-for-azure-ad-free-office-365-or-microsoft-365-customers"></a>Wskazówki dotyczące Azure AD — wersja Bezpłatna, Office 365 lub Microsoft 365 klientów.

Istnieją pewne zalecenia, które Azure AD — wersja Bezpłatna, klienci pakietu Office 365 lub aplikacje Microsoft 365 powinni podjąć działania w celu ochrony tożsamości użytkowników. w poniższej tabeli zawarto podkreślić kluczowe akcje dla następujących subskrypcji licencji:

- Office 365 (Office 365 E1, E3, E5, F1, a1, A3, A5)
- Microsoft 365 (Business Basic, aplikacje dla firm, Business Standard, Business Premium, a1)
- Azure AD — wersja Bezpłatna (dołączone do platformy Azure, Dynamics 365, Intune i platformę energetyczną)

| Zalecana akcja | Szczegóły |
| --- | --- |
| [Włącz ustawienia domyślne zabezpieczeń](concept-fundamentals-security-defaults.md) | Ochrona wszystkich tożsamości i aplikacji użytkowników przez włączenie usługi MFA i blokowanie starszego uwierzytelniania |
| [Włącz synchronizację skrótów haseł](../hybrid/how-to-connect-password-hash-synchronization.md) (jeśli są używane tożsamości hybrydowe) | Zapewniaj nadmiarowość uwierzytelniania i zwiększaj bezpieczeństwo (w tym blokadę inteligentną, blokadę adresów IP oraz możliwość odnajdywania przecieków poświadczeń). |
| [Włącz inteligentne Blokowanie usług AD FS](/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-smart-lockout-protection) (jeśli dotyczy) | Chroni użytkowników przed złośliwymi działaniami przy użyciu blokady konta ekstranetu. |
| [Włącz Azure Active Directory inteligentnego blokowania](../authentication/howto-password-smart-lockout.md) (jeśli są używane tożsamości zarządzane) | Inteligentna blokada pomaga w zablokowaniu nieprawidłowych uczestników próbujących odgadnąć hasła użytkowników lub użyć metod w celu uzyskania dostępu do programu. |
| [Wyłącz zgodę użytkowników końcowych na aplikacje](../manage-apps/configure-user-consent.md) | Przepływ pracy zgody administratora zapewnia administratorom bezpieczny sposób udzielania dostępu do aplikacji, które wymagają zatwierdzenia przez administratora, aby użytkownicy końcowi nie ujawniali danych firmowych. Firma Microsoft zaleca wyłączenie w przyszłości operacji wyrażania zgody użytkowników na zmniejszenie obszaru powierzchni i uniknięcie tego ryzyka. |
| [Integruj obsługiwane aplikacje SaaS z galerii do usługi Azure AD i Włącz logowanie jednokrotne](../manage-apps/add-application-portal.md) | Usługa Azure AD zawiera galerię zawierającą tysiące wstępnie zintegrowanych aplikacji. Niektóre aplikacje używane przez organizację są prawdopodobnie dostępne w galerii bezpośrednio z Azure Portal. Zapewnianie zdalnego i bezpiecznego dostępu do firmowych aplikacji SaaS przy użyciu ulepszonego środowiska użytkownika (SSO) |
| [Automatyzowanie aprowizacji użytkowników i anulowanie obsługi aplikacji SaaS](../app-provisioning/user-provisioning.md) (jeśli dotyczy) | Automatycznie twórz tożsamości i role użytkowników w aplikacjach w chmurze (SaaS), do których użytkownicy potrzebują dostępu. Oprócz tworzenia tożsamości użytkowników automatyczne Inicjowanie obsługi obejmuje konserwację i usuwanie tożsamości użytkowników jako zmiany stanu lub ról, zwiększając bezpieczeństwo organizacji. |
| [Włącz bezpieczny dostęp hybrydowy: bezpieczne starsze aplikacje z istniejącymi kontrolerami i sieciami dostarczania aplikacji](../manage-apps/secure-hybrid-access.md) (jeśli dotyczy) | Publikuj i Chroń starsze aplikacje do uwierzytelniania lokalnego i w chmurze, łącząc je z usługą Azure AD przy użyciu istniejącego kontrolera dostarczania aplikacji lub sieci. |
| Włącz samoobsługowe [Resetowanie hasła](../authentication/tutorial-enable-sspr.md) (dotyczy tylko kont w chmurze) | Ta możliwość zmniejsza liczbę wywołań pomocy technicznej i utratę produktywności, gdy użytkownik nie może zalogować się do swoich urządzeń lub aplikacji. |
| [W miarę możliwości używaj nieglobalnych ról administracyjnych](../roles/permissions-reference.md) | Nadaj administratorom tylko dostęp do tych obszarów, do których potrzebują dostępu. Nie wszyscy administratorzy muszą być administratorami globalnymi. |
| [Włącz wskazówki dotyczące haseł firmy Microsoft](https://www.microsoft.com/research/publication/password-guidance/) | Zatrzymaj wymaganie od użytkowników zmiany hasła zgodnie z ustalonym harmonogramem, wyłącz wymagania dotyczące złożoności, a użytkownicy są bardziej apt, aby zapamiętać swoje hasła i zapewnić, że są one bezpieczne. |


### <a name="guidance-for-azure-ad-premium-plan-1-customers"></a>Wskazówki dotyczące Azure AD — wersja Premium planu 1.

Poniższa tabela służy do wyróżniania kluczowych akcji dla następujących subskrypcji licencji:

- Azure Active Directory — wersja Premium P1 (Azure AD P1)
- Enterprise Mobility + Security (EMS E3)
- Microsoft 365 (M365 E3, A3, F1, F3)

| Zalecana akcja | Szczegóły |
| --- | --- |
| [Włącz połączone środowisko rejestracji usługi Azure MFA i SSPR, aby uprościć rejestrację użytkowników](../authentication/howto-registration-mfa-sspr-combined.md) | Zezwól użytkownikom na rejestrację z jednego typowego środowiska dla usługi Azure Multi-Factor Authentication i samoobsługowego resetowania hasła. |
| [Konfigurowanie ustawień usługi MFA dla organizacji](../authentication/howto-mfa-getstarted.md) | Upewnij się, że konta są chronione przed naruszeniem zabezpieczeń za pomocą uwierzytelniania wieloskładnikowego |
| [Włączanie samoobsługowego resetowania hasła](../authentication/tutorial-enable-sspr.md) | Pozwala to zmniejszyć liczbę wywołań pomocy technicznej i utratę produktywności, gdy użytkownik nie może zalogować się na urządzeniu lub w aplikacji |
| [Implementuj zapisywanie zwrotne haseł](../authentication/tutorial-enable-sspr-writeback.md) (jeśli są używane tożsamości hybrydowe) | Zezwalaj na zapisywanie zmian haseł w chmurze z powrotem do lokalnego środowiska Active Directory systemu Windows Server. |
| Tworzenie i Włączanie zasad dostępu warunkowego | [Uwierzytelnianie wieloskładnikowe dla administratorów programu chroniące konta z przypisanymi prawami administracyjnymi.](../conditional-access/howto-conditional-access-policy-admin-mfa.md) <br><br> [Zablokuj starsze protokoły uwierzytelniania ze względu na zwiększone ryzyko związane ze starszymi protokołami uwierzytelniania.](../conditional-access/howto-conditional-access-policy-block-legacy.md) <br><br> [Uwierzytelnianie wieloskładnikowe dla wszystkich użytkowników i aplikacji w celu utworzenia zasad usługi MFA o zrównoważonym znaczeniu dla danego środowiska, zabezpieczania użytkowników i aplikacji.](../conditional-access/howto-conditional-access-policy-all-users-mfa.md) <br><br> [Wymagaj uwierzytelniania wieloskładnikowego na potrzeby zarządzania platformą Azure w celu ochrony zasobów uprzywilejowanych przez dowolnych użytkowników uzyskujących dostęp do zasobów platformy Azure.](../conditional-access/howto-conditional-access-policy-azure-management.md) |
| [Włącz synchronizację skrótów haseł](../hybrid/how-to-connect-password-hash-synchronization.md) (jeśli są używane tożsamości hybrydowe) | Zapewniaj nadmiarowość uwierzytelniania i zwiększaj bezpieczeństwo (w tym blokadę inteligentną, blokadę adresów IP oraz możliwość odnajdywania przecieków poświadczeń). |
| [Włącz inteligentne Blokowanie usług AD FS](/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-smart-lockout-protection) (jeśli dotyczy) | Chroni użytkowników przed złośliwymi działaniami przy użyciu blokady konta ekstranetu. |
| [Włącz Azure Active Directory inteligentnego blokowania](../authentication/howto-password-smart-lockout.md) (jeśli są używane tożsamości zarządzane) | Inteligentna blokada pomaga w zablokowaniu nieprawidłowych uczestników próbujących odgadnąć hasła użytkowników lub użyć metod w celu uzyskania dostępu do programu. |
| [Wyłącz zgodę użytkowników końcowych na aplikacje](../manage-apps/configure-user-consent.md) | Przepływ pracy zgody administratora zapewnia administratorom bezpieczny sposób udzielania dostępu do aplikacji, które wymagają zatwierdzenia przez administratora, aby użytkownicy końcowi nie ujawniali danych firmowych. Firma Microsoft zaleca wyłączenie w przyszłości operacji wyrażania zgody użytkowników na zmniejszenie obszaru powierzchni i uniknięcie tego ryzyka. |
| [Włączanie dostępu zdalnego do lokalnych starszych aplikacji przy użyciu serwera proxy aplikacji](../manage-apps/application-proxy-add-on-premises-application.md) | Włącz usługę Azure serwer proxy aplikacji usługi Azure AD i Zintegruj ją ze starszymi aplikacjami, aby użytkownicy mogli bezpiecznie uzyskiwać dostęp do aplikacji lokalnych, logując się przy użyciu konta usługi Azure AD. |
| [Włącz bezpieczny dostęp hybrydowy: bezpieczne starsze aplikacje z istniejącymi kontrolerami i sieciami dostarczania aplikacji](../manage-apps/secure-hybrid-access.md) (jeśli dotyczy). | Publikuj i Chroń starsze aplikacje do uwierzytelniania lokalnego i w chmurze, łącząc je z usługą Azure AD przy użyciu istniejącego kontrolera dostarczania aplikacji lub sieci. |
| [Integruj obsługiwane aplikacje SaaS z galerii do usługi Azure AD i Włącz logowanie jednokrotne](../manage-apps/add-application-portal.md) | Usługa Azure AD zawiera galerię zawierającą tysiące wstępnie zintegrowanych aplikacji. Niektóre aplikacje używane przez organizację są prawdopodobnie dostępne w galerii bezpośrednio z Azure Portal. Zapewnij zdalnie i bezpieczny dostęp do firmowych aplikacji SaaS przy użyciu ulepszonego środowiska użytkownika (SSO). |
| [Automatyzowanie aprowizacji użytkowników i anulowanie obsługi aplikacji SaaS](../app-provisioning/user-provisioning.md) (jeśli dotyczy) | Automatycznie twórz tożsamości i role użytkowników w aplikacjach w chmurze (SaaS), do których użytkownicy potrzebują dostępu. Oprócz tworzenia tożsamości użytkowników automatyczne Inicjowanie obsługi obejmuje konserwację i usuwanie tożsamości użytkowników jako zmiany stanu lub ról, zwiększając bezpieczeństwo organizacji. |
| [Włączanie dostępu warunkowego — oparte na urządzeniach](../conditional-access/require-managed-devices.md) | Zwiększ bezpieczeństwo i środowisko użytkownika przy użyciu dostępu warunkowego opartego na urządzeniach. Ten krok zapewnia użytkownikom dostęp tylko z urządzeń spełniających Twoje standardy dotyczące zabezpieczeń i zgodności. Te urządzenia są również znane jako urządzenia zarządzane. Urządzeniami zarządzanymi mogą być zgodne z usługą Intune lub hybrydowe urządzenia przyłączone do usługi Azure AD. |
| [Włącz ochronę hasłem](../authentication/howto-password-ban-bad-on-premises-deploy.md) | Ochrona użytkowników przed użyciem słabych i łatwych do odgadnięcia haseł. |
| [Wyznacz więcej niż jednego administratora globalnego](../roles/security-emergency-access.md) | Należy przypisać co najmniej dwa stałe konta administratora globalnego tylko w chmurze do użycia w przypadku wystąpienia sytuacji awaryjnej. Te konta nie są używane codziennie i powinny mieć długie i złożone hasła. Konta szkła awaryjnego zapewniają dostęp do usługi w nagłym stanie. |
| [W miarę możliwości używaj nieglobalnych ról administracyjnych](../roles/permissions-reference.md) | Nadaj administratorom tylko dostęp do tych obszarów, do których potrzebują dostępu. Nie wszyscy administratorzy muszą być administratorami globalnymi. |
| [Włącz wskazówki dotyczące haseł firmy Microsoft](https://www.microsoft.com/research/publication/password-guidance/) | Zatrzymaj wymaganie od użytkowników zmiany hasła zgodnie z ustalonym harmonogramem, wyłącz wymagania dotyczące złożoności, a użytkownicy są bardziej apt, aby zapamiętać swoje hasła i zapewnić, że są one bezpieczne. |
| [Utwórz plan dla dostępu gościa](../external-identities/what-is-b2b.md) | Współpracuj z użytkownikami-Gośćmi, umożliwiając im logowanie do aplikacji i usług przy użyciu własnych tożsamości służbowych i służbowych. |

### <a name="guidance-for-azure-ad-premium-plan-2-customers"></a>Wskazówki dotyczące Azure AD — wersja Premiumych klientów planu 2.

Poniższa tabela służy do wyróżniania kluczowych akcji dla następujących subskrypcji licencji:

- Azure Active Directory — wersja Premium P2 (Azure AD P2)
- Enterprise Mobility + Security (EMS E5)
- Microsoft 365 (M365 E5, A5)

| Zalecana akcja | Szczegóły |
| --- | --- |
| [Włącz połączone środowisko rejestracji usługi Azure MFA i SSPR, aby uprościć rejestrację użytkowników](../authentication/howto-registration-mfa-sspr-combined.md) | Zezwól użytkownikom na rejestrację z jednego typowego środowiska dla usługi Azure Multi-Factor Authentication i samoobsługowego resetowania hasła. |
| [Konfigurowanie ustawień usługi MFA dla organizacji](../authentication/howto-mfa-getstarted.md) | Upewnij się, że konta są chronione przed naruszeniem zabezpieczeń za pomocą uwierzytelniania wieloskładnikowego |
| [Włączanie samoobsługowego resetowania hasła](../authentication/tutorial-enable-sspr.md) | Pozwala to zmniejszyć liczbę wywołań pomocy technicznej i utratę produktywności, gdy użytkownik nie może zalogować się na urządzeniu lub w aplikacji |
| [Implementuj zapisywanie zwrotne haseł](../authentication/tutorial-enable-sspr-writeback.md) (jeśli są używane tożsamości hybrydowe) | Zezwalaj na zapisywanie zmian haseł w chmurze z powrotem do lokalnego środowiska Active Directory systemu Windows Server. |
| [Włączenie zasad ochrony tożsamości w celu wymuszenia rejestracji usługi MFA](../identity-protection/howto-identity-protection-configure-mfa-policy.md) | Zarządzanie rozwinięciem usługi Azure Multi-Factor Authentication (MFA). |
| [Włącz zasady dotyczące użytkowników i zagrożeń logowania dla ochrony tożsamości](../identity-protection/howto-identity-protection-configure-risk-policies.md) | Włączanie zasad logowania i użytkownika ochrony tożsamości. Zalecanymi zasadami logowania jest ukierunkowanie na logowanie średnie ryzyko i wymaganie uwierzytelniania wieloskładnikowego. Zasady użytkownika powinny być przeznaczone dla użytkowników o wysokim ryzyku wymagającym akcji zmiany hasła. |
| Tworzenie i Włączanie zasad dostępu warunkowego | [Uwierzytelnianie wieloskładnikowe dla administratorów programu chroniące konta z przypisanymi prawami administracyjnymi.](../conditional-access/howto-conditional-access-policy-admin-mfa.md) <br><br> [Zablokuj starsze protokoły uwierzytelniania ze względu na zwiększone ryzyko związane ze starszymi protokołami uwierzytelniania.](../conditional-access/howto-conditional-access-policy-block-legacy.md) <br><br> [Wymagaj uwierzytelniania wieloskładnikowego na potrzeby zarządzania platformą Azure w celu ochrony zasobów uprzywilejowanych przez dowolnych użytkowników uzyskujących dostęp do zasobów platformy Azure.](../conditional-access/howto-conditional-access-policy-azure-management.md) |
| [Włącz synchronizację skrótów haseł](../hybrid/how-to-connect-password-hash-synchronization.md) (jeśli są używane tożsamości hybrydowe) | Zapewniaj nadmiarowość uwierzytelniania i zwiększaj bezpieczeństwo (w tym blokadę inteligentną, blokadę adresów IP oraz możliwość odnajdywania przecieków poświadczeń). |
| [Włącz inteligentne Blokowanie usług AD FS](/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-smart-lockout-protection) (jeśli dotyczy) | Chroni użytkowników przed złośliwymi działaniami przy użyciu blokady konta ekstranetu. |
| [Włącz Azure Active Directory inteligentnego blokowania](../authentication/howto-password-smart-lockout.md) (jeśli są używane tożsamości zarządzane) | Inteligentna blokada pomaga w zablokowaniu nieprawidłowych uczestników próbujących odgadnąć hasła użytkowników lub użyć metod w celu uzyskania dostępu do programu. |
| [Wyłącz zgodę użytkowników końcowych na aplikacje](../manage-apps/configure-user-consent.md) | Przepływ pracy zgody administratora zapewnia administratorom bezpieczny sposób udzielania dostępu do aplikacji, które wymagają zatwierdzenia przez administratora, aby użytkownicy końcowi nie ujawniali danych firmowych. Firma Microsoft zaleca wyłączenie w przyszłości operacji wyrażania zgody użytkowników na zmniejszenie obszaru powierzchni i uniknięcie tego ryzyka. |
| [Włączanie dostępu zdalnego do lokalnych starszych aplikacji przy użyciu serwera proxy aplikacji](../manage-apps/application-proxy-add-on-premises-application.md) | Włącz usługę Azure serwer proxy aplikacji usługi Azure AD i Zintegruj ją ze starszymi aplikacjami, aby użytkownicy mogli bezpiecznie uzyskiwać dostęp do aplikacji lokalnych, logując się przy użyciu konta usługi Azure AD. |
| [Włącz bezpieczny dostęp hybrydowy: bezpieczne starsze aplikacje z istniejącymi kontrolerami i sieciami dostarczania aplikacji](../manage-apps/secure-hybrid-access.md) (jeśli dotyczy). | Publikuj i Chroń starsze aplikacje do uwierzytelniania lokalnego i w chmurze, łącząc je z usługą Azure AD przy użyciu istniejącego kontrolera dostarczania aplikacji lub sieci. |
| [Integruj obsługiwane aplikacje SaaS z galerii do usługi Azure AD i Włącz logowanie jednokrotne](../manage-apps/add-application-portal.md) | Usługa Azure AD zawiera galerię zawierającą tysiące wstępnie zintegrowanych aplikacji. Niektóre aplikacje używane przez organizację są prawdopodobnie dostępne w galerii bezpośrednio z Azure Portal. Zapewnij zdalnie i bezpieczny dostęp do firmowych aplikacji SaaS przy użyciu ulepszonego środowiska użytkownika (SSO). |
| [Automatyzowanie aprowizacji użytkowników i anulowanie obsługi aplikacji SaaS](../app-provisioning/user-provisioning.md) (jeśli dotyczy) | Automatycznie twórz tożsamości i role użytkowników w aplikacjach w chmurze (SaaS), do których użytkownicy potrzebują dostępu. Oprócz tworzenia tożsamości użytkowników automatyczne Inicjowanie obsługi obejmuje konserwację i usuwanie tożsamości użytkowników jako zmiany stanu lub ról, zwiększając bezpieczeństwo organizacji. |
| [Włączanie dostępu warunkowego — oparte na urządzeniach](../conditional-access/require-managed-devices.md) | Zwiększ bezpieczeństwo i środowisko użytkownika przy użyciu dostępu warunkowego opartego na urządzeniach. Ten krok zapewnia użytkownikom dostęp tylko z urządzeń spełniających Twoje standardy dotyczące zabezpieczeń i zgodności. Te urządzenia są również znane jako urządzenia zarządzane. Urządzeniami zarządzanymi mogą być zgodne z usługą Intune lub hybrydowe urządzenia przyłączone do usługi Azure AD. |
| [Włącz ochronę hasłem](../authentication/howto-password-ban-bad-on-premises-deploy.md) | Ochrona użytkowników przed użyciem słabych i łatwych do odgadnięcia haseł. |
| [Wyznacz więcej niż jednego administratora globalnego](../roles/security-emergency-access.md) | Należy przypisać co najmniej dwa stałe konta administratora globalnego tylko w chmurze do użycia w przypadku wystąpienia sytuacji awaryjnej. Te konta nie są używane codziennie i powinny mieć długie i złożone hasła. Konta szkła awaryjnego zapewniają dostęp do usługi w nagłym stanie. |
| [W miarę możliwości używaj nieglobalnych ról administracyjnych](../roles/permissions-reference.md) | Nadaj administratorom tylko dostęp do tych obszarów, do których potrzebują dostępu. Nie wszyscy administratorzy muszą być administratorami globalnymi. |
| [Włącz wskazówki dotyczące haseł firmy Microsoft](https://www.microsoft.com/research/publication/password-guidance/) | Zatrzymaj wymaganie od użytkowników zmiany hasła zgodnie z ustalonym harmonogramem, wyłącz wymagania dotyczące złożoności, a użytkownicy są bardziej apt, aby zapamiętać swoje hasła i zapewnić, że są one bezpieczne. |
| [Utwórz plan dla dostępu gościa](../external-identities/what-is-b2b.md) | Współpracuj z użytkownikami-Gośćmi, umożliwiając im logowanie do aplikacji i usług przy użyciu własnych tożsamości służbowych i służbowych. |
| [Włącz Privileged Identity Management](../privileged-identity-management/pim-configure.md) | Umożliwia zarządzanie, kontrolowanie i monitorowanie dostępu do ważnych zasobów w organizacji, dzięki czemu administratorzy mają dostęp tylko w razie potrzeby i z zatwierdzeniem |

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać szczegółowe wskazówki dotyczące wdrażania poszczególnych funkcji usługi Azure AD, zapoznaj się z [planami wdrażania projektu usługi Azure AD](active-directory-deployment-plans.md).

- Aby uzyskać kompleksową listę kontrolną wdrożenia usługi Azure AD, zobacz artykuł [Azure Active Directory Podręcznik wdrażania funkcji](active-directory-deployment-checklist-p2.md)