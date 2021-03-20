---
title: Środowiska użytkownika z Azure AD Identity Protection
description: Środowisko użytkownika Azure AD Identity Protection
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4d4897b9187caab50be4db75bbc0af03e3d35aa4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94835989"
---
# <a name="user-experiences-with-azure-ad-identity-protection"></a>Środowiska użytkownika z Azure AD Identity Protection

Za pomocą Azure Active Directory Identity Protection można:

* Wymagaj, aby użytkownicy rejestrowali się w usłudze Azure AD Multi-Factor Authentication (MFA)
* Automatyzuj korygowanie ryzykownych logowań i narażonych użytkowników

Wszystkie zasady ochrony tożsamości mają wpływ na środowisko logowania użytkowników. Umożliwienie użytkownikom rejestrowania programu i używania narzędzi, takich jak usługa Azure AD MFA i Samoobsługowe resetowanie hasła, może zmniejszyć wpływ. Te narzędzia wraz z odpowiednimi opcjami wyboru zasad zapewniają użytkownikom możliwość samodzielnego korygowania, gdy ich potrzebują.

## <a name="multi-factor-authentication-registration"></a>Rejestracja w usłudze uwierzytelniania wieloskładnikowego

Włączenie zasad ochrony tożsamości wymagające rejestracji w usłudze uwierzytelniania wieloskładnikowego i przekierowania wszystkich użytkowników pozwoli upewnić się, że będą oni mogli korzystać z usługi Azure AD MFA do samodzielnego korygowania w przyszłości. Skonfigurowanie tych zasad spowoduje, że użytkownicy będą mieć 14-dniowy okres, w którym mogą oni wybrać rejestrację, a na koniec będą zmuszeni do rejestracji. Środowisko dla użytkowników zostało opisane poniżej. Więcej informacji można znaleźć w dokumentacji użytkownika końcowego w artykule [Omówienie weryfikacji dwuskładnikowej i konta służbowego](../user-help/multi-factor-authentication-end-user-first-time.md).

### <a name="registration-interrupt"></a>Przerwanie rejestracji

1. Podczas logowania do dowolnej aplikacji zintegrowanej z usługą Azure AD użytkownik otrzymuje powiadomienie o wymaganym skonfigurowaniu konta do uwierzytelniania wieloskładnikowego. Te zasady są również wyzwalane w środowisku systemu Windows 10 w przypadku nowych użytkowników przy użyciu nowego urządzenia.
   
    ![Wymagane są więcej informacji](./media/concept-identity-protection-user-experience/identity-protection-experience-more-info-mfa.png)

1. Wykonaj kroki z przewodnikiem, aby zarejestrować się w usłudze Azure AD Multi-Factor Authentication i ukończyć logowanie.

## <a name="risky-sign-in-remediation"></a>Ryzykowne Rozwiązywanie problemów

Gdy administrator skonfigurował zasady dotyczące ryzyka związanego z logowaniem, użytkownicy, których to dotyczy, są powiadamiani o próbie zalogowania się i wyzwolenie poziomu ryzyka zasad. 

### <a name="risky-sign-in-self-remediation"></a>Samodzielne rozwiązywanie problemów z logowaniem

1. Użytkownik jest informowany o wykryciu nietypowej rejestracji, na przykład w przypadku logowania się z nowej lokalizacji, urządzenia lub aplikacji.
   
    ![Coś nietypowego monitu](./media/concept-identity-protection-user-experience/120.png)

1. Użytkownik musi potwierdzić swoją tożsamość, kończąc usługę Azure AD MFA przy użyciu jednej z wcześniej zarejestrowanych metod. 

### <a name="risky-sign-in-administrator-unblock"></a>Odblokowanie ryzykownego konta administratora

Administratorzy mogą blokować użytkownikom logowanie się w zależności od poziomu ryzyka. Aby uzyskać odblokowywanie, użytkownicy końcowi muszą kontaktować się z pracownikami IT lub mogą próbować zalogować się ze znajomej lokalizacji lub urządzenia. Samoobsługowe korygowanie przez przeprowadzenie uwierzytelniania wieloskładnikowego nie jest opcją w tym przypadku.

![Zablokowane przez zasady dotyczące ryzyka związanego z logowaniem](./media/concept-identity-protection-user-experience/200.png)

Pracownicy działu IT mogą postępować zgodnie z instrukcjami w sekcji [odblokowywanie użytkowników](howto-identity-protection-remediate-unblock.md#unblocking-based-on-sign-in-risk) , aby umożliwić użytkownikom ponowne logowanie.

## <a name="risky-user-remediation"></a>Ryzykowne korygowanie użytkowników

Po skonfigurowaniu zasad ryzyka użytkownika użytkownicy, którzy spełnią prawdopodobieństwo naruszenia poziomu ryzyka dla użytkownika, muszą przejść przez przepływ odzyskiwania przez użytkownika przed zalogowaniem się. 

### <a name="risky-user-self-remediation"></a>Samodzielne korygowanie użytkowników

1. Użytkownik jest informowany, że zabezpieczenia konta są zagrożone ze względu na podejrzane działanie lub nieujawnione poświadczenia.
   
    ![Korekty](./media/concept-identity-protection-user-experience/101.png)

1. Użytkownik musi potwierdzić swoją tożsamość, kończąc usługę Azure AD MFA przy użyciu jednej z wcześniej zarejestrowanych metod. 
1. Na koniec użytkownik jest zmuszony do zmiany hasła przy użyciu funkcji samoobsługowego resetowania hasła, ponieważ ktoś inny mógł uzyskać dostęp do swojego konta.

## <a name="risky-sign-in-administrator-unblock"></a>Odblokowanie ryzykownego konta administratora

Administratorzy mogą blokować użytkownikom logowanie się w zależności od poziomu ryzyka. Aby uzyskać odblokowywanie, użytkownicy końcowi muszą skontaktować się z pracownikami IT. Samoobsługowe korygowanie przez przeprowadzenie uwierzytelniania wieloskładnikowego i samoobsługowego resetowania hasła nie jest opcją w tym przypadku.

![Zablokowane przez zasady ryzyka użytkownika](./media/concept-identity-protection-user-experience/104.png)

Pracownicy działu IT mogą postępować zgodnie z instrukcjami w sekcji [odblokowywanie użytkowników](howto-identity-protection-remediate-unblock.md#unblocking-based-on-user-risk) , aby umożliwić użytkownikom ponowne logowanie.

## <a name="see-also"></a>Zobacz też

- [Korygowanie zagrożeń i odblokowywanie użytkowników](howto-identity-protection-remediate-unblock.md)

- [Azure Active Directory Identity Protection](./overview-identity-protection.md)