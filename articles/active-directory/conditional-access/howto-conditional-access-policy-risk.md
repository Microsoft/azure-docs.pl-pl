---
title: Dostęp warunkowy oparty na ryzyku logowania — Azure Active Directory
description: Tworzenie zasad dostępu warunkowego przy użyciu ryzyka związanego z logowaniem usługi Identity Protection
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
ms.openlocfilehash: a09c4513206bea3462577ecba49b5d77b655b0e0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91628268"
---
# <a name="conditional-access-sign-in-risk-based-conditional-access"></a>Dostęp warunkowy: Logowanie warunkowe oparte na ryzyku

Większości użytkowników dotyczy zachowanie normalne, które można śledzić. Gdy ta norma zostanie przekroczona, zezwolenie tym użytkownikom na zwykłe logowanie się może stanowić ryzyko. Możesz chcieć zablokować tego użytkownika lub po prostu poproś go o przeprowadzenie uwierzytelniania wieloskładnikowego, aby udowodnić, że są one rzeczywiście napisane. 

Ryzyko związane z logowaniem reprezentuje prawdopodobieństwo, że dane żądanie uwierzytelnienia nie jest autoryzowane przez właściciela tożsamości. Organizacje z licencjami na Azure AD — wersja Premium P2 mogą tworzyć zasady dostępu warunkowego, w tym [Azure AD Identity Protection wykrywania ryzyka logowania](../identity-protection/concept-identity-protection-risks.md#sign-in-risk).

Istnieją dwie lokalizacje, w których te zasady mogą być przypisane. Organizacje powinny wybrać jedną z następujących opcji, aby włączyć zasady dostępu warunkowego opartego na zagrożeniu, które wymagają bezpiecznej zmiany hasła.

## <a name="enable-with-conditional-access-policy"></a>Włączanie przy użyciu zasad dostępu warunkowego

1. Zaloguj się do **Azure Portal** jako Administrator globalny, administrator zabezpieczeń lub administrator dostępu warunkowego.
1. Przejdź do **Azure Active Directory**  >    >  **dostępu warunkowego** zabezpieczeń.
1. Wybierz pozycję **nowe zasady**.
1. Nadaj zasadom nazwę. Firma Microsoft zaleca, aby organizacje utworzyły znaczący Standard nazw swoich zasad.
1. W obszarze **Przypisania** wybierz pozycję **Użytkownicy i grupy**.
   1. W obszarze **dołączanie** wybierz pozycję **Wszyscy użytkownicy**.
   1. W obszarze **Wyklucz** wybierz pozycję **Użytkownicy i grupy** , a następnie wybierz opcję dostęp awaryjny lub konta w ramach swojej organizacji. 
   1. Kliknij **Gotowe**.
1. W obszarze **aplikacje w chmurze lub akcje**  >  **Dołącz** wybierz pozycję **wszystkie aplikacje w chmurze**.
1. W obszarze **warunki**  >  **ryzyka związanego z logowaniem** ustaw wartość **tak**.  W obszarze **Wybierz poziom ryzyka związanego z logowaniem ta zasada będzie miała zastosowanie** 
   1. Wybierz opcję **wysoki** i **Średni**.
   1. Kliknij **Gotowe**.
1. W obszarze **Kontrola dostępu**  >  **przyznawanie** wybierz pozycję **Udziel dostępu**, **Wymagaj uwierzytelniania wieloskładnikowego**, a następnie wybierz pozycję **Wybierz**.
1. Potwierdź ustawienia i ustaw opcję **Włącz zasady** na **włączone**.
1. Wybierz pozycję **Utwórz** , aby utworzyć zasady.

## <a name="enable-through-identity-protection"></a>Włącz za poorednictwem programu Identity Protection

1. Zaloguj się w witrynie **Azure Portal**.
1. Wybierz pozycję **wszystkie usługi**, a następnie przejdź do **Azure AD Identity Protection**.
1. Wybierz pozycję **zasady dotyczące ryzyka związanego z logowaniem**.
1. W obszarze **przypisania** wybierz pozycję **Użytkownicy**.
   1. W obszarze **dołączanie** wybierz pozycję **Wszyscy użytkownicy**.
   1. W obszarze **Wyklucz** wybierz opcję **Wybierz wykluczonych użytkowników**, wybierz pozycję dostęp awaryjny lub konta w firmie, a następnie wybierz pozycję **Wybierz**.
   1. Kliknij **Gotowe**.
1. W obszarze **warunki** wybierz pozycję **ryzyko związane z logowaniem**, a następnie wybierz pozycję **średni i powyżej**.
   1. Wybierz pozycję **Wybierz**, a następnie pozycję **gotowe**.
1. W obszarze **Kontrola**  >  **dostępu** wybierz pozycję **Zezwalaj na dostęp**, a następnie wybierz pozycję **Wymagaj uwierzytelniania wieloskładnikowego**.
   1. Wybierz pozycję **Wybierz**.
1. Ustaw ustawienie **Wymuszaj zasady** na **włączone**.
1. Wybierz pozycję **Zapisz**.

## <a name="next-steps"></a>Następne kroki

[Wspólne zasady dostępu warunkowego](concept-conditional-access-policy-common.md)

[Dostęp warunkowy oparty na ryzyku użytkownika](howto-conditional-access-policy-risk-user.md)

[Określanie wpływu przy użyciu trybu tylko Raport z dostępem warunkowym](howto-conditional-access-insights-reporting.md)

[Symulowanie zachowania logowania za pomocą narzędzia What If dostępu warunkowego](troubleshoot-conditional-access-what-if.md)

[Co to jest usługa Azure Active Directory Identity Protection?](../identity-protection/overview-identity-protection.md)
