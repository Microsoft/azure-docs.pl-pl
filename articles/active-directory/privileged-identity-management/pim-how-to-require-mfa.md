---
title: MFA lub funkcji 2FA i Privileged Identity Management — Azure AD | Microsoft Docs
description: Dowiedz się, jak Azure AD Privileged Identity Management (PIM) sprawdza poprawność uwierzytelniania wieloskładnikowego (MFA).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4344e769cc8466287dab1e98e95cc3fbe705ffbd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94835003"
---
# <a name="multi-factor-authentication-and-privileged-identity-management"></a>Uwierzytelnianie wieloskładnikowe i Privileged Identity Management

Zalecamy wymaganie uwierzytelniania wieloskładnikowego (MFA) dla wszystkich administratorów. Zmniejsza to ryzyko ataków spowodowanych przez złamane hasło.

Możesz wymagać, aby użytkownicy ukończyli wyzwanie usługi uwierzytelniania wieloskładnikowego podczas logowania. Możesz również wymagać, aby użytkownicy ukończyli wyzwanie usługi uwierzytelnianie wieloskładnikowe, gdy aktywują rolę w usłudze Azure Active Directory (Azure AD) Privileged Identity Management (PIM). W ten sposób, jeśli użytkownik nie wykonał żądania uwierzytelniania wieloskładnikowego po zalogowaniu się, zostanie wyświetlony monit o wykonanie tej czynności przez Privileged Identity Management.

> [!IMPORTANT]
> Teraz usługa Azure AD Multi-Factor Authentication działa tylko z kontami służbowymi, a nie kontami osobistymi firmy Microsoft (zazwyczaj konta osobistego, które jest używane do logowania się do usług firmy Microsoft, takich jak Skype, Xbox lub Outlook.com). Z tego względu każdy użytkownik korzystający z konta osobistego nie może być uprawnionym administratorem, ponieważ nie może używać uwierzytelniania wieloskładnikowego w celu aktywowania ich ról. Jeśli Ci użytkownicy muszą kontynuować zarządzanie obciążeniami przy użyciu konto Microsoft, w razie potrzeby Podnieś poziom uprawnień do stałych administratorów.

## <a name="how-pim-validates-mfa"></a>Jak PIM sprawdza poprawność uwierzytelniania MFA

Dostępne są dwie opcje weryfikacji uwierzytelniania wieloskładnikowego, gdy użytkownik aktywuje rolę.

Najprostszą opcją jest zagwarantowanie, że usługa Azure AD Multi-Factor Authentication dla użytkowników, którzy aktywują rolę uprzywilejowaną. Aby to zrobić, najpierw sprawdź, czy Ci użytkownicy mają licencję, w razie potrzeby i zarejestrowano w usłudze Azure AD Multi-Factor Authentication. Aby uzyskać więcej informacji o sposobie wdrażania usługi Azure AD Multi-Factor Authentication, zobacz [wdrażanie usługi azure Multi-Factor Authentication AD opartej na chmurze](../authentication/howto-mfa-getstarted.md). Zalecane jest, ale nie wymagane, skonfigurowanie usługi Azure AD w celu wymuszenia uwierzytelniania wieloskładnikowego dla tych użytkowników podczas logowania. Wynika to z faktu, że sprawdzanie uwierzytelniania wieloskładnikowego zostanie przeprowadzone przez Privileged Identity Management samego siebie.

Alternatywnie, jeśli użytkownicy uwierzytelniają się lokalnie, dostawca tożsamości może być odpowiedzialny za uwierzytelnianie wieloskładnikowe. Jeśli na przykład skonfigurowano usługi federacyjne AD do wymagania uwierzytelniania opartego na karcie inteligentnej przed uzyskaniem dostępu do usługi Azure AD, [Zabezpieczanie zasobów w chmurze za pomocą usługi Azure ad Multi-Factor Authentication i AD FS](../authentication/howto-mfa-adfs.md) zawiera instrukcje dotyczące konfigurowania AD FS w celu wysyłania oświadczeń do usługi Azure AD. Gdy użytkownik próbuje aktywować rolę, Privileged Identity Management zaakceptuje, że uwierzytelnianie wieloskładnikowe zostało już zweryfikowane dla użytkownika po odebraniu odpowiednich oświadczeń.

## <a name="next-steps"></a>Następne kroki

- [Konfigurowanie ustawień roli usługi Azure AD w Privileged Identity Management](pim-how-to-change-default-settings.md)
- [Skonfiguruj ustawienia roli zasobów platformy Azure w Privileged Identity Management](pim-resource-roles-configure-role-settings.md)
