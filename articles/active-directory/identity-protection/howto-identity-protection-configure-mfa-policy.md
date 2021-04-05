---
title: Konfigurowanie zasad rejestracji usługi MFA — Azure Active Directory Identity Protection
description: Dowiedz się, jak skonfigurować Azure AD Identity Protection zasady rejestracji w usłudze uwierzytelniania wieloskładnikowego.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: how-to
ms.date: 06/05/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 072db1d47abd95844075aeedfeddc4f8cf6bf936
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94835870"
---
# <a name="how-to-configure-the-azure-ad-multi-factor-authentication-registration-policy"></a>Instrukcje: Konfigurowanie zasad rejestracji Multi-Factor Authentication usługi Azure AD

Azure AD Identity Protection ułatwia zarządzanie rozwinięciem rejestracji usługi Azure AD Multi-Factor Authentication (MFA) przez skonfigurowanie zasad dostępu warunkowego w taki sposób, aby wymagały rejestracji MFA niezależnie od tego, która aplikacja uwierzytelniania jest zapisywana.

## <a name="what-is-the-azure-ad-multi-factor-authentication-registration-policy"></a>Co to są zasady rejestracji Multi-Factor Authentication usługi Azure AD?

Usługa Azure AD Multi-Factor Authentication udostępnia środki umożliwiające sprawdzenie, kto jest używany więcej niż nazwa użytkownika i hasło. Zapewnia drugą warstwę zabezpieczeń do logowania użytkowników. Aby użytkownicy mogli odpowiadać na monity usługi MFA, muszą najpierw zarejestrować się w usłudze Azure AD Multi-Factor Authentication.

Zalecamy, aby usługa Azure AD Multi-Factor Authentication dla logowania użytkownika była wymagana, ponieważ:

- Zapewnia silne uwierzytelnianie za pomocą zakresu opcji weryfikacji.
- Odgrywa kluczową rolę w przygotowaniu organizacji do samodzielnego korygowania od wykrywania ryzyka w ramach ochrony tożsamości.

Aby uzyskać więcej informacji na temat Multi-Factor Authentication usługi Azure AD, zobacz [co to jest usługa Azure ad Multi-Factor Authentication?](../authentication/howto-mfa-getstarted.md)

## <a name="policy-configuration"></a>Konfiguracja zasad

1. Przejdź do [Azure Portal](https://portal.azure.com).
1. Przejdź do obszaru **Azure Active Directory**  >  **Security**  >  **Identity Protection**  >  **zasady rejestracji usługi MFA**.
   1. W obszarze **przypisania**
      1. **Użytkownicy** — wybierz opcję **Wszyscy użytkownicy** lub **Wybierz osoby i grupy w** przypadku ograniczenia wdrożenia.
         1. Opcjonalnie możesz zdecydować się na wykluczenie użytkowników z zasad.
   1. W obszarze **Controls**
      1. Upewnij się, że pole wyboru **Wymagaj rejestracji w usłudze Azure AD MFA** jest zaznaczone, a następnie wybierz **pozycję Wybierz**.
   1. **Wymuś zasady**  -  **Na**
   1. **Zapisz**

## <a name="user-experience"></a>Środowisko użytkownika

Azure Active Directory Identity Protection będzie monitować użytkowników o zarejestrowanie się przy następnym logowaniu się interaktywnie i przeprowadzenie rejestracji przez 14 dni. W ramach tego 14-dniowego okresu można ominąć rejestrację, ale na koniec okresu, w którym będą one wymagane do ukończenia procesu logowania.

Aby zapoznać się z omówieniem środowiska użytkownika powiązanego, zobacz:

- [Środowisko logowania przy użyciu Azure AD Identity Protection](concept-identity-protection-user-experience.md).  

## <a name="next-steps"></a>Następne kroki

- [Włącz zasady logowania i ryzyka dla użytkowników](howto-identity-protection-configure-risk-policies.md)

- [Włącz Samoobsługowe resetowanie haseł w usłudze Azure AD](../authentication/howto-sspr-deployment.md)

- [Włączanie uwierzytelniania wieloskładnikowego w usłudze Azure AD](../authentication/howto-mfa-getstarted.md)
