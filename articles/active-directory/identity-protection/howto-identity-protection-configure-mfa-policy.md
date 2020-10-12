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
ms.openlocfilehash: d4c6b57eaa2a68906053faade48dd0e63fbf0db7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "84464338"
---
# <a name="how-to-configure-the-azure-multi-factor-authentication-registration-policy"></a>Instrukcje: Konfigurowanie zasad rejestracji w usłudze Azure Multi-Factor Authentication

Azure AD Identity Protection ułatwia zarządzanie rejestracją usługi Azure Multi-Factor Authentication (MFA) przez skonfigurowanie zasad dostępu warunkowego, aby wymagać rejestracji MFA niezależnie od tego, w jaki sposób jest zapisywana aplikacja do uwierzytelniania.

## <a name="what-is-the-azure-multi-factor-authentication-registration-policy"></a>Co to są zasady rejestracji w usłudze Azure Multi-Factor Authentication?

Usługa Azure Multi-Factor Authentication umożliwia sprawdzenie, kto jest używany więcej niż nazwa użytkownika i hasło. Zapewnia drugą warstwę zabezpieczeń do logowania użytkowników. Aby użytkownicy mogli odpowiadać na monity usługi MFA, muszą najpierw zarejestrować się w usłudze Azure Multi-Factor Authentication.

Zalecamy, aby dla logowania użytkownika wymagało Multi-Factor Authentication platformy Azure, ponieważ:

- Zapewnia silne uwierzytelnianie za pomocą zakresu opcji weryfikacji.
- Odgrywa kluczową rolę w przygotowaniu organizacji do samodzielnego korygowania od wykrywania ryzyka w ramach ochrony tożsamości.

Aby uzyskać więcej informacji na temat usługi Azure Multi-Factor Authentication, zobacz [co to jest usługa azure Multi-Factor Authentication?](../authentication/howto-mfa-getstarted.md)

## <a name="policy-configuration"></a>Konfiguracja zasad

1. Przejdź do [Azure Portal](https://portal.azure.com).
1. Przejdź do obszaru **Azure Active Directory**  >  **Security**  >  **Identity Protection**  >  **zasady rejestracji usługi MFA**.
   1. W obszarze **przypisania**
      1. **Użytkownicy** — wybierz opcję **Wszyscy użytkownicy** lub **Wybierz osoby i grupy w** przypadku ograniczenia wdrożenia.
         1. Opcjonalnie możesz zdecydować się na wykluczenie użytkowników z zasad.
   1. W obszarze **Controls**
      1. Upewnij się, że pole wyboru **Wymagaj rejestracji w usłudze Azure MFA** jest zaznaczone, a następnie wybierz **pozycję Wybierz**.
   1. **Wymuś zasady**  -  **Na**
   1. **Zapisz**

## <a name="user-experience"></a>Środowisko użytkownika

Azure Active Directory Identity Protection będzie monitować użytkowników o zarejestrowanie się przy następnym logowaniu się interaktywnie i przeprowadzenie rejestracji przez 14 dni. W ramach tego 14-dniowego okresu można ominąć rejestrację, ale na koniec okresu, w którym będą one wymagane do ukończenia procesu logowania.

Aby zapoznać się z omówieniem środowiska użytkownika powiązanego, zobacz:

- [Środowisko logowania przy użyciu Azure AD Identity Protection](concept-identity-protection-user-experience.md).  

## <a name="next-steps"></a>Następne kroki

- [Włącz zasady logowania i ryzyka dla użytkowników](howto-identity-protection-configure-risk-policies.md)

- [Włącz Samoobsługowe resetowanie haseł w usłudze Azure AD](../authentication/howto-sspr-deployment.md)

- [Włączanie usługi Azure Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md)
