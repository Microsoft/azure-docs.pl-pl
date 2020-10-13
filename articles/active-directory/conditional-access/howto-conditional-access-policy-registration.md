---
title: Dostęp warunkowy — połączone informacje o zabezpieczeniach — Azure Active Directory
description: Tworzenie niestandardowych zasad dostępu warunkowego dla rejestracji informacji zabezpieczających
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 05/26/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: da68e21aa279ea2503a21ce35eee52f8e49d1434
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89049098"
---
# <a name="conditional-access-securing-security-info-registration"></a>Dostęp warunkowy: Zabezpieczanie rejestracji informacji zabezpieczających

Zabezpieczanie, kiedy i jak użytkownicy rejestrują się w usłudze Azure Multi-Factor Authentication i samoobsługowego resetowania hasła jest teraz możliwe z działaniami użytkowników w zasadach dostępu warunkowego. Ta funkcja w wersji zapoznawczej jest dostępna dla organizacji, które włączyły [Podgląd rejestracji połączonej](../authentication/concept-registration-mfa-sspr-combined.md). Ta funkcjonalność może być włączona w organizacjach, w których chcą korzystać z takich warunków, jak w przypadku zaufanej lokalizacji sieciowej, aby ograniczyć dostęp do usługi Azure Multi-Factor Authentication i samoobsługowego resetowania hasła (SSPR). Aby uzyskać więcej informacji o warunkach użytecznych, zobacz artykuł [dostęp warunkowy: warunki](concept-conditional-access-conditions.md).

## <a name="create-a-policy-to-require-registration-from-a-trusted-location"></a>Utwórz zasady, aby wymagać rejestracji z zaufanej lokalizacji

Poniższe zasady mają zastosowanie do wszystkich wybranych użytkowników, którzy próbują zarejestrować się przy użyciu połączonego środowiska rejestracji i blokują dostęp, chyba że nawiązują połączenie z lokalizacji oznaczonej jako zaufane sieci.

1. W **Azure Portal**przejdź do **Azure Active Directory**  >  **zabezpieczenia**  >  **dostęp warunkowy**.
1. Wybierz pozycję **nowe zasady**.
1. W polu Nazwa wprowadź nazwę dla tych zasad. Na przykład **połączona informacja dotycząca zabezpieczeń Rejestracja w zaufanych sieciach**.
1. W obszarze **przypisania**wybierz pozycję **Użytkownicy i grupy**, a następnie wybierz użytkowników i grupy, do których te zasady mają być stosowane.

   > [!WARNING]
   > Użytkownicy muszą być włączeni do [rejestracji połączonej](../authentication/howto-registration-mfa-sspr-combined.md).

1. W obszarze **aplikacje lub akcje w chmurze**wybierz pozycję **akcje użytkownika**, a następnie sprawdź pozycję **zarejestruj informacje zabezpieczające**.
1. W obszarze **warunków**  >  **lokalizacji**.
   1. Skonfiguruj **tak**.
   1. Uwzględnij **dowolną lokalizację**.
   1. Wyklucz **wszystkie Zaufane lokalizacje**.
   1. Wybierz pozycję **gotowe** w bloku lokalizacje.
   1. Wybierz pozycję **gotowe** w bloku warunki.
1. W obszarze **warunki**  >  **aplikacje klienckie (wersja zapoznawcza)** ustaw wartość opcji **Konfiguruj** na **tak**, a następnie wybierz pozycję **gotowe**.
1. W obszarze **Kontrola dostępu**  >  **Udziel**.
   1. Wybierz pozycję **Blokuj dostęp**.
   1. Następnie kliknij pozycję **Wybierz**.
1. Ustaw przełącznik **Włącz zasady** na wartość **Włączone**.
1. Następnie wybierz przycisk **Zapisz**.

W kroku 6 tych zasad organizacje mają odpowiednie opcje. Powyższe zasady wymagają rejestracji z zaufanej lokalizacji sieciowej. Organizacje mogą zdecydować się na korzystanie z jakichkolwiek dostępnych warunków zamiast **lokalizacji**. Należy pamiętać, że te zasady są zasadami blokowania, aby wszystkie dołączone elementy były blokowane i wszystkie elementy, które nie pasują do dołączenia, są dozwolone. 

Niektóre z nich mogą korzystać z stanu urządzenia zamiast lokalizacji w kroku 6 powyżej:

6. W obszarze **warunki**  >  **stan urządzenia (wersja zapoznawcza)**.
   1. Skonfiguruj **tak**.
   1. Uwzględnij **wszystkie Stany urządzeń**.
   1. Wykluczenie **hybrydowej urządzenia z usługą Azure AD** i/lub **urządzenia oznaczonego jako zgodne**
   1. Wybierz pozycję **gotowe** w bloku lokalizacje.
   1. Wybierz pozycję **gotowe** w bloku warunki.

> [!WARNING]
> Jeśli używasz stanu urządzenia jako warunku w zasadach, może to mieć wpływ na użytkowników-Gości w katalogu. [Tryb tylko do raportowania](concept-conditional-access-report-only.md) może pomóc w ustaleniu wpływu decyzji dotyczących zasad.
> Należy pamiętać, że tryb tylko do raportowania nie dotyczy zasad urzędu certyfikacji z zakresem "akcje użytkownika".

## <a name="next-steps"></a>Następne kroki

[Wspólne zasady dostępu warunkowego](concept-conditional-access-policy-common.md)

[Określanie wpływu przy użyciu trybu tylko Raport z dostępem warunkowym](howto-conditional-access-insights-reporting.md)

[Symulowanie zachowania logowania za pomocą narzędzia What If dostępu warunkowego](troubleshoot-conditional-access-what-if.md)
