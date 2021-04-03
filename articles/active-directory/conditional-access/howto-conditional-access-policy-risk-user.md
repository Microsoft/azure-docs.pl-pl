---
title: Dostęp warunkowy oparty na ryzyku użytkownika — Azure Active Directory
description: Tworzenie zasad dostępu warunkowego przy użyciu ryzyka użytkownika ochrony tożsamości
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 07/02/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: d5423a61c8febee72f32935f3dee4e9f9e868630
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "89049081"
---
# <a name="conditional-access-user-risk-based-conditional-access"></a>Dostęp warunkowy: dostęp warunkowy oparty na ryzyku

Firma Microsoft współpracuje z naukowcami, przepisami prawnymi, różnymi zespołami ds. zabezpieczeń w firmie Microsoft i innymi zaufanymi źródłami w celu znalezienia nieujawnionych par nazw i haseł. Organizacje z licencjami na Azure AD — wersja Premium P2 mogą tworzyć zasady dostępu warunkowego obejmujące [Azure AD Identity Protection wykrywania ryzyka użytkownika](../identity-protection/concept-identity-protection-risks.md#user-risk). 

Istnieją dwie lokalizacje, w których te zasady mogą być przypisane. Organizacje powinny wybrać jedną z następujących opcji, aby włączyć zasady dostępu warunkowego opartego na ryzyku użytkownika wymagające bezpiecznej zmiany hasła.

## <a name="enable-with-conditional-access-policy"></a>Włączanie przy użyciu zasad dostępu warunkowego

1. Zaloguj się do **Azure Portal** jako Administrator globalny, administrator zabezpieczeń lub administrator dostępu warunkowego.
1. Przejdź do **Azure Active Directory**  >    >  **dostępu warunkowego** zabezpieczeń.
1. Wybierz pozycję **nowe zasady**.
1. Nadaj zasadom nazwę. Firma Microsoft zaleca, aby organizacje utworzyły znaczący Standard nazw swoich zasad. Aby uzyskać więcej informacji, [Ustaw standardy nazewnictwa dla zasad](./plan-conditional-access.md#set-naming-standards-for-your-policies).
1. W obszarze **Przypisania** wybierz pozycję **Użytkownicy i grupy**.
   1. W obszarze **dołączanie** wybierz pozycję **Wszyscy użytkownicy**.
   1. W obszarze **Wyklucz** wybierz pozycję **Użytkownicy i grupy** , a następnie wybierz opcję dostęp awaryjny lub konta w ramach swojej organizacji. 
   1. Kliknij **Gotowe**.
1. W obszarze **aplikacje w chmurze lub akcje**  >  **Dołącz** wybierz pozycję **wszystkie aplikacje w chmurze**.
1. W obszarze **warunki**  >  **ryzyka użytkownika** ustaw opcję **Skonfiguruj** na **wartość tak**. W obszarze **Konfigurowanie poziomów ryzyka użytkownika wymaganych do wymuszania zasad** wybierz pozycję **wysoka**, a następnie wybierz pozycję **gotowe**.
1. W obszarze **Kontrola dostępu**  >  **Przyznaj** wybierz pozycję **Udziel dostępu**, **Wymagaj zmiany hasła** i wybierz pozycję **Wybierz**.
1. Potwierdź ustawienia, a następnie ustaw opcję **Włącz zasady** na **włączone**.
1. Wybierz pozycję **Utwórz** , aby utworzyć zasady.

## <a name="enable-through-identity-protection"></a>Włącz za poorednictwem programu Identity Protection

1. Zaloguj się w witrynie **Azure Portal**.
1. Wybierz pozycję **wszystkie usługi**, a następnie przejdź do **Azure AD Identity Protection**.
1. Wybierz pozycję **zasady ryzyka dla użytkowników**.
1. W obszarze **przypisania** wybierz pozycję **Użytkownicy**.
   1. W obszarze **dołączanie** wybierz pozycję **Wszyscy użytkownicy**.
   1. W obszarze **Wyklucz** wybierz opcję **Wybierz wykluczonych użytkowników**, wybierz pozycję dostęp awaryjny lub konta w firmie, a następnie wybierz pozycję **Wybierz**.
   1. Kliknij **Gotowe**.
1. W obszarze **warunki** wybierz pozycję **ryzyko użytkownika**, a następnie wybierz pozycję **wysoki**.
   1. Wybierz pozycję **Wybierz**, a następnie pozycję **gotowe**.
1. W obszarze **Kontrola**  >  **dostępu** wybierz pozycję **Zezwalaj na dostęp**, a następnie wybierz pozycję **Wymagaj zmiany hasła**.
   1. Wybierz pozycję **Wybierz**.
1. Ustaw ustawienie **Wymuszaj zasady** na **włączone**.
1. Wybierz pozycję **Zapisz**.

## <a name="next-steps"></a>Następne kroki

[Wspólne zasady dostępu warunkowego](concept-conditional-access-policy-common.md)

[Dostęp warunkowy oparty na ryzyku logowania](howto-conditional-access-policy-risk.md)

[Określanie wpływu przy użyciu trybu tylko Raport z dostępem warunkowym](howto-conditional-access-insights-reporting.md)

[Symulowanie zachowania logowania za pomocą narzędzia What If dostępu warunkowego](troubleshoot-conditional-access-what-if.md)

[Co to jest usługa Azure Active Directory Identity Protection?](../identity-protection/overview-identity-protection.md)