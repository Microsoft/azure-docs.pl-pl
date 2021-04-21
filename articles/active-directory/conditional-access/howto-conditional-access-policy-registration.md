---
title: Dostęp warunkowy — połączone informacje o zabezpieczeniach — Azure Active Directory
description: Tworzenie niestandardowych zasad dostępu warunkowego do rejestracji informacji zabezpieczających
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 04/20/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 05aca853e2eba98d224131c98751b4e2f4200024
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107765651"
---
# <a name="conditional-access-securing-security-info-registration"></a>Dostęp warunkowy: Zabezpieczanie rejestracji informacji zabezpieczających

Zabezpieczanie, kiedy i jak użytkownicy rejestrowiją się w usłudze Azure AD Multi-Factor Authentication i samoobsługowe resetowanie hasła, jest możliwe za pomocą akcji użytkownika w zasadach dostępu warunkowego. Ta funkcja jest dostępna dla organizacji, które włączyły [połączoną rejestrację.](../authentication/concept-registration-mfa-sspr-combined.md) Ta funkcja umożliwia organizacjom traktowanie procesu rejestracji jak każdej aplikacji w zasadach dostępu warunkowego i korzystanie z pełnych możliwości dostępu warunkowego w celu zabezpieczenia środowiska. 

Niektóre organizacje w przeszłości mogą używać zaufanej lokalizacji sieciowej lub zgodności urządzeń jako środka do zabezpieczenia procesu rejestracji. Dzięki dodatku [Tymczasowy kod dostępu](../authentication/howto-authentication-temporary-access-pass.md) usłudze Azure AD administratorzy mogą aprowizować użytkownikom ograniczone czasowo poświadczenia, które umożliwiają im rejestrację z dowolnego urządzenia lub lokalizacji. Tymczasowy kod dostępu spełniają wymagania dostępu warunkowego dla uwierzytelniania wieloskładnikowego.

## <a name="create-a-policy-to-secure-registration"></a>Tworzenie zasad w celu zabezpieczenia rejestracji

Poniższe zasady dotyczą wybranych użytkowników, którzy próbują zarejestrować się przy użyciu połączonego środowiska rejestracji. Zasady wymagają, aby użytkownicy przeprowadzali uwierzytelnianie wieloskładnikowe lub używali Tymczasowy kod dostępu poświadczeń.

1. Na stronie **Azure Portal** przejdź do **Azure Active Directory**  >  **dostępu**  >  **warunkowego zabezpieczeń.**
1. Wybierz **pozycję Nowe zasady.**
1. W nazwa, Wprowadź nazwę dla tych zasad. Na przykład **połączoną rejestrację informacji zabezpieczających za pomocą naciśnięcia .**
1. W **obszarze Przypisania** wybierz pozycję Użytkownicy i **grupy,** a następnie wybierz użytkowników i grupy, których mają dotyczyć te zasady.
   1. W **obszarze Uwzględnij** wybierz **pozycję Wszyscy użytkownicy.**

      > [!WARNING]
      > Użytkownicy muszą mieć włączoną obsługę [połączonej rejestracji.](../authentication/howto-registration-mfa-sspr-combined.md)

   1. W obszarze **Wyklucz**.
      1. Wybierz **pozycję Wszyscy goście i użytkownicy zewnętrzni.**
      
         > [!NOTE]
         > Tymczasowy kod dostępu nie działa w przypadku użytkowników-gości.

      1. Wybierz **pozycję Użytkownicy i grupy** i wybierz konta dostępu awaryjnego lub włamań w organizacji. 
1. W **obszarze Aplikacje lub akcje w chmurze** wybierz pozycję Akcje **użytkownika**, zaznacz pozycję Zarejestruj informacje **o zabezpieczeniach.**
1. W **obszarze Kontrole dostępu**  >  **przyznaj .**
   1. Kliknij opcję **Przyznaj dostęp**.
   1. Wybierz pozycję **Wymagaj uwierzytelniania wieloskładnikowego**.
   1. Kliknij pozycję **Wybierz**.
1. Ustaw przełącznik **Włącz zasady** na wartość **Włączone**.
1. Następnie wybierz pozycję **Utwórz**.

Administratorzy będą teraz Tymczasowy kod dostępu poświadczeń dla nowych użytkowników, aby spełnić wymagania dotyczące uwierzytelniania wieloskładnikowego w celu zarejestrowania. Kroki służące do wykonania tego zadania znajdują się w sekcji Tworzenie Tymczasowy kod dostępu [w portalu usługi Azure AD.](../authentication/howto-authentication-temporary-access-pass.md#create-a-temporary-access-pass)

Organizacje mogą wymagać innych kontroli udzielania oprócz lub w miejsce Wymagaj **uwierzytelniania wieloskładnikowego** w kroku 6b. Podczas wybierania wielu kontrolek należy wybrać odpowiedni  przełącznik przycisku radiowego, aby wymagać wszystkich lub **jednej** z wybranych kontrolek podczas dokonywania tej zmiany.

### <a name="guest-user-registration"></a>Rejestracja użytkownika-gościa

W [przypadku użytkowników-gości,](../external-identities/what-is-b2b.md) którzy muszą zarejestrować się w celu uwierzytelniania [wieloskładnikowego](concept-conditional-access-conditions.md#locations) w katalogu, możesz zablokować rejestrację spoza zaufanych lokalizacji sieciowych, korzystając z poniższego przewodnika.

1. Na stronie **Azure Portal** przejdź **do** Azure Active Directory  >  **dostępu**  >  **warunkowego zabezpieczeń**.
1. Wybierz **pozycję Nowe zasady.**
1. W nazwa, wprowadź nazwę dla tych zasad. Na przykład: **połączona rejestracja informacji zabezpieczających w zaufanych sieciach.**
1. W **obszarze Przypisania** wybierz pozycję Użytkownicy i **grupy,** a następnie wybierz użytkowników i grupy, których mają dotyczyć te zasady.
   1. W **obszarze Uwzględnij** wybierz pozycję **Wszyscy użytkownicy-goście i użytkownicy zewnętrzni.**
1. W **obszarze Aplikacje lub akcje w chmurze** wybierz pozycję Akcje **użytkownika**, zaznacz pozycję Zarejestruj informacje **o zabezpieczeniach.**
1. W **obszarze Warunki**  >  **Lokalizacje**.
   1. Skonfiguruj **pozycję Tak.**
   1. Uwzględnij **dowolną lokalizację**.
   1. **Wyklucz wszystkie zaufane lokalizacje.**
   1. Wybierz **pozycję Gotowe** w bloku Lokalizacje.
   1. Wybierz **pozycję Gotowe** w bloku Warunki.
1. W **obszarze Kontrole dostępu**  >  **przyznaj .**
   1. Wybierz **pozycję Blokuj dostęp.**
   1. Następnie kliknij pozycję **Wybierz**.
1. Ustaw przełącznik **Włącz zasady** na wartość **Włączone**.
1. Następnie wybierz pozycję **Zapisz**.

## <a name="next-steps"></a>Następne kroki

[Typowe zasady dostępu warunkowego](concept-conditional-access-policy-common.md)

[Określanie wpływu przy użyciu trybu tylko do raportu z dostępem warunkowym](howto-conditional-access-insights-reporting.md)

[Symulowanie zachowania logowania przy użyciu narzędzia dostępu What If warunkowego](troubleshoot-conditional-access-what-if.md)

[Wymaganie od użytkowników ponownego potwierdzania informacji o uwierzytelnianiu](../authentication/concept-sspr-howitworks.md#reconfirm-authentication-information)
