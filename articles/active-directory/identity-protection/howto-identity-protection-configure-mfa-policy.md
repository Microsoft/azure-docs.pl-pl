---
title: Konfigurowanie zasad rejestracji usługi MFA — Azure Active Directory Identity Protection
description: Dowiedz się, jak skonfigurować Azure AD Identity Protection zasady rejestracji w usłudze uwierzytelniania wieloskładnikowego.
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
ms.openlocfilehash: 2fa6a4cf184b426355f62117ea51642127eee529
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "74382141"
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
1. Przejdź do obszaru **Azure Active Directory** > **Security** > **Identity Protection** > **zasady rejestracji usługi MFA**.
   1. W obszarze **przypisania**
      1. **Użytkownicy** — wybierz opcję **Wszyscy użytkownicy** lub **Wybierz osoby i grupy w** przypadku ograniczenia wdrożenia.
         1. Opcjonalnie możesz zdecydować się na wykluczenie użytkowników z zasad.
   1. W obszarze **Controls**
      1. Upewnij się, że pole wyboru **Wymagaj rejestracji w usłudze Azure MFA** jest zaznaczone, a następnie wybierz **pozycję Wybierz**.
   1. **Wymuś zasady** - **On**
   1. **Zapisz**

## <a name="user-experience"></a>Środowisko użytkownika

Azure Active Directory Identity Protection będzie monitować użytkowników o zarejestrowanie się przy następnym logowaniu się interaktywnie i przeprowadzenie rejestracji przez 14 dni. W ramach tego 14-dniowego okresu można ominąć rejestrację, ale na koniec okresu, w którym będą one wymagane do ukończenia procesu logowania.

Aby zapoznać się z omówieniem środowiska użytkownika powiązanego, zobacz:

- [Środowisko logowania przy użyciu Azure AD Identity Protection](concept-identity-protection-user-experience.md).  

## <a name="next-steps"></a>Następne kroki

- [Włącz zasady logowania i ryzyka dla użytkowników](howto-identity-protection-configure-risk-policies.md)

- [Włącz Samoobsługowe resetowanie haseł w usłudze Azure AD](../authentication/howto-sspr-deployment.md)

- [Włączanie usługi Azure Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md)
