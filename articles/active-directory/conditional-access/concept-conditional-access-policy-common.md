---
title: Typowe zasady dostępu warunkowego — Azure Active Directory
description: Powszechnie używane zasady dostępu warunkowego dla organizacji
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 07/02/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 114f26c71ee1aecdb1a0ad3d1c01e558843c0969
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91705062"
---
# <a name="common-conditional-access-policies"></a>Typowe zasady dostępu warunkowego

[Wartości domyślne zabezpieczeń](../fundamentals/concept-fundamentals-security-defaults.md) są wspaniałe w przypadku niektórych organizacji, które wymagają większej elastyczności niż oferta. Na przykład w wielu organizacjach wymagana jest możliwość wykluczenia określonych kont, takich jak ich dostęp awaryjny lub konta administracyjne w przypadku awarii z zasad dostępu warunkowego, które wymagają uwierzytelniania wieloskładnikowego. W przypadku tych organizacji typowe zasady, do których odwołuje się ten artykuł, mogą być używane.

![Zasady dostępu warunkowego w Azure Portal](./media/concept-conditional-access-policy-common/conditional-access-policies-azure-ad-listing.png)

## <a name="emergency-access-accounts"></a>Konta dostępu awaryjnego

Więcej informacji o kontach dostępu awaryjnego i o tym, dlaczego są ważne, można znaleźć w następujących artykułach: 

* [Zarządzanie kontami dostępu awaryjnego w usłudze Azure AD](../users-groups-roles/directory-emergency-access.md)
* [Utwórz odporną strategię zarządzania kontrolą dostępu za pomocą Azure Active Directory](../authentication/concept-resilient-controls.md)

## <a name="typical-policies-deployed-by-organizations"></a>Typowe zasady wdrożone przez organizacje

* [Blokuj starsze uwierzytelnianie](howto-conditional-access-policy-block-legacy.md)\*
* [Wymagaj uwierzytelniania wieloskładnikowego dla administratorów](howto-conditional-access-policy-admin-mfa.md)\*
* [Wymagaj uwierzytelniania wieloskładnikowego na potrzeby zarządzania na platformie Azure](howto-conditional-access-policy-azure-management.md)\*
* [Wymagaj uwierzytelniania wieloskładnikowego dla wszystkich użytkowników](howto-conditional-access-policy-all-users-mfa.md)\*

\* Te cztery zasady po połączeniu ze sobą funkcje podśladu włączone przy użyciu [ustawień domyślnych zabezpieczeń](../fundamentals/concept-fundamentals-security-defaults.md).

## <a name="additional-policies"></a>Dodatkowe zasady

* [Dostęp warunkowy oparty na ryzyku związanym z logowaniem (wymaga Azure AD — wersja Premium P2)](howto-conditional-access-policy-risk.md)
* [Dostęp warunkowy oparty na ryzyku (wymaga Azure AD — wersja Premium P2)](howto-conditional-access-policy-risk-user.md)
* [Wymaganie zaufanej lokalizacji na potrzeby rejestracji uwierzytelniania wieloskładnikowego](howto-conditional-access-policy-registration.md)
* [Blokowanie dostępu według lokalizacji](howto-conditional-access-policy-location.md)
* [Wymaganie zgodnego urządzenia](howto-conditional-access-policy-compliant-device.md)
* [Blokuj dostęp poza określonymi aplikacjami](howto-conditional-access-policy-block-access.md)

## <a name="next-steps"></a>Następne kroki

- [Symuluj zachowanie podczas logowania za pomocą narzędzia What If dostępu warunkowego.](troubleshoot-conditional-access-what-if.md)

- [Użyj trybu tylko do raportowania dla dostępu warunkowego, aby określić wpływ nowych decyzji dotyczących zasad.](concept-conditional-access-report-only.md)
