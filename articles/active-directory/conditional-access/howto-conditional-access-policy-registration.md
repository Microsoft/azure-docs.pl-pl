---
title: Dostęp warunkowy — połączone informacje o zabezpieczeniach — usługa Azure Active Directory
description: Tworzenie niestandardowej zasady dostępu warunkowego do rejestracji informacji zabezpieczających
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9c8081bb8145a6654c168fb2d664e1666b32dc18
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81457913"
---
# <a name="conditional-access-securing-security-info-registration"></a>Dostęp warunkowy: zabezpieczanie rejestracji informacji zabezpieczających

Zabezpieczanie, kiedy i jak użytkownicy rejestrują się w celu autoryzacji wieloskładnikowej platformy Azure i samoobsługowego resetowania hasła jest teraz możliwe dzięki działaniom użytkownika w zasadach dostępu warunkowego. Ta funkcja podglądu jest dostępna dla organizacji, które włączyły [połączoną wersję zapoznawczą rejestracji](../authentication/concept-registration-mfa-sspr-combined.md). Ta funkcja może być włączona w organizacjach, w których chcą używać warunków, takich jak zaufana lokalizacja sieciowa, aby ograniczyć dostęp do rejestracji w celu rejestrowania uwierzytelniania wieloskładnikowego platformy Azure i samoobsługowego resetowania haseł (SSPR). Aby uzyskać więcej informacji na temat warunków użytkowych, zobacz artykuł [Dostęp warunkowy: Warunki](concept-conditional-access-conditions.md).

## <a name="create-a-policy-to-require-registration-from-a-trusted-location"></a>Tworzenie zasad wymagających rejestracji z zaufanej lokalizacji

Poniższe zasady mają zastosowanie do wszystkich wybranych użytkowników, którzy próbują zarejestrować się przy użyciu połączonego środowiska rejestracji i blokują dostęp, chyba że łączą się z lokalizacji oznaczonej jako zaufana sieć.

1. W **witrynie Azure portal**przejdź do **usługi Azure Active Directory** > **Security** > **Conditional Access**.
1. Wybierz **pozycję Nowa zasada**.
1. W nazwie wprowadź nazwę dla tej zasady. Na przykład **połączone informacje o bezpieczeństwie rejestracja w zaufanych sieciach**.
1. W obszarze **Przydziały**wybierz **pozycję Użytkownicy i grupy**i wybierz użytkowników i grupy, do których ma zostać zastosowane ta zasada.

   > [!WARNING]
   > Użytkownicy muszą być włączeni dla [połączonej rejestracji](../authentication/howto-registration-mfa-sspr-combined.md).

1. W obszarze **Aplikacje lub akcje w chmurze**wybierz pozycję **Akcje użytkownika**, zaznacz pozycję **Zarejestruj informacje o zabezpieczeniach**.
1. W **warunkach** > **Lokalizacje**.
   1. Skonfiguruj **Tak**.
   1. Uwzględnij **dowolną lokalizację**.
   1. Wyklucz **wszystkie zaufane lokalizacje**.
   1. Wybierz **opcję Gotowe** na bloku Lokalizacje.
   1. Wybierz **Gotowe** na ostrze Warunki.
1. W **obszarze Warunki** > **aplikacje klienckie (Wersja zapoznawcza)** ustaw **pozycję Konfiguruj** na **Tak**i wybierz opcję **Gotowe**.
1. W obszarze **Kontrola** > dostępu**Grant**.
   1. Wybierz **pozycję Blokuj dostęp**.
   1. Następnie kliknij pozycję **Wybierz**.
1. Ustaw pozycję **Włącz zasady** na wartość **Włączone**.
1. Następnie wybierz pozycję **Zapisz**.

W kroku 6 w tej polityce organizacje mają wybór, jaki mogą dokonać. Powyższe zasady wymagają rejestracji z zaufanej lokalizacji sieciowej. Organizacje mogą zdecydować się na wykorzystanie wszelkich dostępnych warunków zamiast **lokalizacji**. Pamiętaj, że ta zasada jest zasadą blokowania, więc wszystko, co zostało uwzględnione, jest zablokowane, a wszystko, co nie pasuje do include, jest dozwolone. 

Niektórzy mogą zdecydować się na użycie stanu urządzenia zamiast lokalizacji w kroku 6 powyżej:

6. W **obszarze Stan** > urządzenia warunków **(podgląd)**.
   1. Skonfiguruj **Tak**.
   1. Uwzględnij **cały stan urządzenia**.
   1. Wyklucz **sprzężenie** i/lub urządzenie z hybrydową **usługą** Azure AD oznaczone jako zgodne
   1. Wybierz **opcję Gotowe** na bloku Lokalizacje.
   1. Wybierz **Gotowe** na ostrze Warunki.

> [!WARNING]
> Jeśli używasz stanu urządzenia jako warunku w zasadach, może to mieć wpływ na użytkowników-gościa w katalogu. [Tryb tylko do raportu](concept-conditional-access-report-only.md) może pomóc w określeniu wpływu decyzji dotyczących zasad.

## <a name="next-steps"></a>Następne kroki

[Wspólne zasady dostępu warunkowego](concept-conditional-access-policy-common.md)

[Określanie wpływu przy użyciu trybu tylko dla dostępu warunkowego](howto-conditional-access-report-only.md)

[Symulowanie zachowania logowania za pomocą narzędzia Co jeśli dostęp warunkowy](troubleshoot-conditional-access-what-if.md)
