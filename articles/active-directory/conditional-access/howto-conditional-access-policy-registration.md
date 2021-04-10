---
title: Dostęp warunkowy — połączone informacje o zabezpieczeniach — Azure Active Directory
description: Tworzenie niestandardowych zasad dostępu warunkowego dla rejestracji informacji zabezpieczających
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 03/24/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 711d4bdf2be2ad3158c12e4690a70fb83fe7a846
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105559506"
---
# <a name="conditional-access-securing-security-info-registration"></a>Dostęp warunkowy: Zabezpieczanie rejestracji informacji zabezpieczających

Zabezpieczanie podczas i sposobu rejestrowania użytkowników w usłudze Azure AD Multi-Factor Authentication i samoobsługowego resetowania hasła jest możliwe w przypadku akcji użytkownika w zasadach dostępu warunkowego. Ta funkcja jest dostępna dla organizacji, które włączyły [rejestrację połączoną](../authentication/concept-registration-mfa-sspr-combined.md). Ta funkcja umożliwia organizacjom traktowanie procesu rejestracji, takiego jak dowolna aplikacja w zasadach dostępu warunkowego, i korzystanie z pełnych możliwości dostępu warunkowego w celu zabezpieczenia środowiska. 

Niektóre organizacje w przeszłości mogły używać zaufanej lokalizacji sieciowej lub zgodności urządzeń jako środka do zabezpieczenia środowiska rejestracji. Po dodaniu [tymczasowego dostępu](../authentication/howto-authentication-temporary-access-pass.md) do usługi Azure AD Administratorzy mogą inicjować poświadczenia ograniczone czasowo dla użytkowników, którzy będą mogli rejestrować się z dowolnego urządzenia lub lokalizacji. Poświadczenia przekazywania dostępu tymczasowego spełniają wymagania dostępu warunkowego dla uwierzytelniania wieloskładnikowego.

## <a name="create-a-policy-to-secure-registration"></a>Tworzenie zasad w celu zabezpieczenia rejestracji

Następujące zasady mają zastosowanie do wybranych użytkowników, którzy próbują zarejestrować się przy użyciu połączonego środowiska rejestracji. Zasady wymagają od użytkowników przeprowadzenia uwierzytelniania wieloskładnikowego lub użycia tymczasowych poświadczeń dostępu.

1. W **Azure Portal** przejdź do **Azure Active Directory**  >  **zabezpieczenia**  >  **dostęp warunkowy**.
1. Wybierz pozycję **nowe zasady**.
1. W polu Nazwa wprowadź nazwę dla tych zasad. Na przykład **zarejestrowano informacje zabezpieczające z programem TAP**.
1. W obszarze **przypisania** wybierz pozycję **Użytkownicy i grupy**, a następnie wybierz użytkowników i grupy, do których te zasady mają być stosowane.
   1. W obszarze **dołączanie** wybierz pozycję **Wszyscy użytkownicy**.

      > [!WARNING]
      > Użytkownicy muszą być włączeni do [rejestracji połączonej](../authentication/howto-registration-mfa-sspr-combined.md).

   1. W obszarze **Wyklucz**.
      1. Wybierz **wszystkich Gości i użytkowników zewnętrznych**.
      
         > [!NOTE]
         > Tymczasowe przekazywanie dostępu nie działa dla użytkowników-Gości.

      1. Wybierz pozycję **Użytkownicy i grupy** , a następnie pozycję dostęp awaryjny lub konta w firmie. 
1. W obszarze **aplikacje lub akcje w chmurze** wybierz pozycję **akcje użytkownika**, a następnie sprawdź pozycję **zarejestruj informacje zabezpieczające**.
1. W obszarze **Kontrola dostępu**  >  **Udziel**.
   1. Kliknij opcję **Przyznaj dostęp**.
   1. Wybierz pozycję **Wymagaj uwierzytelniania wieloskładnikowego**.
   1. Kliknij pozycję **Wybierz**.
1. Ustaw przełącznik **Włącz zasady** na wartość **Włączone**.
1. Następnie wybierz pozycję **Utwórz**.

Administratorzy będą teraz musieli wystawić użytkownikom tymczasowy dostęp do poświadczeń, aby mogli je spełnić wymagania dotyczące uwierzytelniania wieloskładnikowego. Kroki prowadzące do wykonania tego zadania znajdują się w sekcji [Tworzenie tymczasowego dostępu w portalu usługi Azure AD](../authentication/howto-authentication-temporary-access-pass.md#create-a-temporary-access-pass-in-the-azure-ad-portal).

Organizacje mogą zdecydować się na zażądać innych kontroli dotacji oprócz lub zamiast tego **wymagają uwierzytelniania wieloskładnikowego** w kroku 6b. W przypadku wybrania wielu kontrolek upewnij się, że wybrano odpowiedni przełącznik przycisku radiowego, aby wymagać **wszystkich** lub **jednej** z wybranych kontrolek podczas wprowadzania tej zmiany.

### <a name="guest-user-registration"></a>Rejestracja użytkownika-gościa

Dla [użytkowników-Gości](../external-identities/what-is-b2b.md) , którzy muszą zarejestrować się w celu uwierzytelniania wieloskładnikowego w katalogu, można zablokować rejestrację spoza [zaufanych lokalizacji sieciowych](concept-conditional-access-conditions.md#locations) , korzystając z poniższego przewodnika.

1. W **Azure Portal** przejdź do **Azure Active Directory**  >  **zabezpieczenia**  >  **dostęp warunkowy**.
1. Wybierz pozycję **nowe zasady**.
1. W polu Nazwa wprowadź nazwę dla tych zasad. Na przykład **połączona informacja dotycząca zabezpieczeń Rejestracja w zaufanych sieciach**.
1. W obszarze **przypisania** wybierz pozycję **Użytkownicy i grupy**, a następnie wybierz użytkowników i grupy, do których te zasady mają być stosowane.
   1. W obszarze **dołączanie** wybierz pozycję **Wszyscy Goście i użytkownicy zewnętrzni**.
1. W obszarze **aplikacje lub akcje w chmurze** wybierz pozycję **akcje użytkownika**, a następnie sprawdź pozycję **zarejestruj informacje zabezpieczające**.
1. W obszarze **warunków**  >  **lokalizacji**.
   1. Skonfiguruj **tak**.
   1. Uwzględnij **dowolną lokalizację**.
   1. Wyklucz **wszystkie Zaufane lokalizacje**.
   1. Wybierz pozycję **gotowe** w bloku lokalizacje.
   1. Wybierz pozycję **gotowe** w bloku warunki.
1. W obszarze **Kontrola dostępu**  >  **Udziel**.
   1. Wybierz pozycję **Blokuj dostęp**.
   1. Następnie kliknij pozycję **Wybierz**.
1. Ustaw przełącznik **Włącz zasady** na wartość **Włączone**.
1. Następnie wybierz pozycję **Zapisz**.

## <a name="next-steps"></a>Następne kroki

[Wspólne zasady dostępu warunkowego](concept-conditional-access-policy-common.md)

[Określanie wpływu przy użyciu trybu tylko Raport z dostępem warunkowym](howto-conditional-access-insights-reporting.md)

[Symulowanie zachowania logowania za pomocą narzędzia What If dostępu warunkowego](troubleshoot-conditional-access-what-if.md)
