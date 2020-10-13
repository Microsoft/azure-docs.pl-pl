---
title: Prześlij opinię na temat ryzyka w Azure Active Directory Identity Protection
description: Jak i dlaczego należy przekazać informacje zwrotne na temat wykrywania ryzyka ochrony tożsamości.
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
ms.openlocfilehash: 939ffc9e2a8fb8ce3a25dc212e3df34dc6bb2ec1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88950361"
---
# <a name="how-to-give-risk-feedback-in-azure-ad-identity-protection"></a>Instrukcje: wyrażanie opinii o ryzyku w Azure AD Identity Protection

Azure AD Identity Protection pozwala przesłać opinię na temat oceny ryzyka. W poniższym dokumencie przedstawiono scenariusze, w których chcesz przesłać opinię na temat oceny ryzyka Azure AD Identity Protection i sposobu jej dołączenia.

## <a name="what-is-a-detection"></a>Co to jest wykrywanie?

Wykrywanie ochrony tożsamości jest wskaźnikiem podejrzanych działań z perspektywy ryzyka związanego z tożsamościami. Te podejrzane działania są nazywane wykryciami ryzyka. Te wykrywane tożsamości mogą opierać się na algorytmach heurystycznych, uczeniu maszynowym lub w produktach partnerskich. Te wykrycia służą do określania ryzyka związanego z logowaniem i ryzykiem użytkownika,

* Ryzyko użytkownika reprezentuje prawdopodobieństwo naruszenia zabezpieczeń tożsamości.
* Ryzyko związane z logowaniem reprezentuje prawdopodobieństwo naruszenia zabezpieczeń logowania (na przykład logowanie nie jest autoryzowane przez właściciela tożsamości).

## <a name="why-should-i-give-risk-feedback-to-azure-ads-risk-assessments"></a>Dlaczego warto przekazać opinie o ryzyku do oceny ryzyka związanego z usługą Azure AD? 

Istnieje kilka powodów, dla których należy dać opinię o ryzyku usługi Azure AD:

- **Znaleziono nieprawidłową ocenę ryzyka związanego z użytkownikiem lub logowaniem usługi Azure AD**. Na przykład logowanie wyświetlane w raporcie "ryzykowne logowania" było nieszkodliwe i wszystkie wykrycia na tym zalogowaniu były fałszywe.
- **Sprawdzono, że Ocena ryzyka dla użytkownika lub logowania usługi Azure AD była poprawna**. Na przykład logowanie wyświetlane w raporcie "ryzykowne logowania" było rzeczywiście złośliwe i chcesz, aby usługa Azure AD wiedziała, że wszystkie wykrycia na tym zalogowaniu były prawdziwe.
- **Skorygowano ryzyko dla tego użytkownika poza Azure AD Identity Protection** i chcesz zaktualizować poziom ryzyka użytkownika.

## <a name="how-does-azure-ad-use-my-risk-feedback"></a>Jak usługa Azure AD używa mojej opinii o ryzyku?

Usługa Azure AD używa Twoich opinii, aby aktualizować ryzyko związane z użytkownikiem i/lub logowaniem i dokładnością tych zdarzeń. Ta opinia pomaga w zabezpieczeniu użytkownika końcowego. Na przykład po potwierdzeniu, że logowanie zostało naruszone, usługa Azure AD natychmiast zwiększy ryzyko użytkownika i zagregowane ryzyko związane z logowaniem (ryzyko w czasie rzeczywistym). Jeśli ten użytkownik zostanie uwzględniony w zasadach ryzyka użytkownika w celu wymuszenia bezpiecznego resetowania haseł przez użytkowników o wysokim ryzyku, użytkownik zostanie automatycznie skorygowany przy następnym logowaniu.

## <a name="how-should-i-give-risk-feedback-and-what-happens-under-the-hood"></a>Jak mogę przekazać opinię na temat ryzyka i co się dzieje w ramach tego okapu?

Poniżej przedstawiono scenariusze i mechanizmy zapewniające zwrot informacji o ryzyku do usługi Azure AD.

| Scenariusz | Jak przekazać opinię? | Co się stanie pod wyciągiem? | Uwagi |
| --- | --- | --- | --- |
| **Logowanie nie zostało naruszone (Wynik fałszywie dodatni)** <br> Raport "ryzykowne logowania" pokazuje logowanie na ryzyko [stan ryzyka = zagrożone], ale nie naruszono naruszenia zabezpieczeń logowania. | Wybierz Logowanie i kliknij pozycję "Potwierdź bezpieczne logowanie". | Usługa Azure AD przeniesie zagregowane ryzyko związane z logowaniem do nie [stan ryzyka = potwierdzone bezpieczne; Poziom ryzyka (zagregowany) =-] i zmieni wpływ na ryzyko użytkownika. | Obecnie opcja "Potwierdź bezpieczne logowanie" jest dostępna tylko w raporcie "ryzykowne logowania". |
| **Złamane logowanie (prawdziwe pozytywnie)** <br> Raport "ryzykowne logowania" pokazuje logowanie zagrożone ryzykiem [stan ryzyka = zagrożone] z niskim ryzykiem [poziom ryzyka (zagregowany) = niski], a zalogowanie zostało rzeczywiście naruszone. | Wybierz Logowanie i kliknij pozycję "Potwierdź naruszenie zabezpieczeń logowania". | Usługa Azure AD przeniesie zagregowane ryzyko związane z logowaniem i ryzyko dla użytkownika wysokie [stan ryzyka = potwierdzone zabezpieczenia; Poziom ryzyka = wysoki]. | Obecnie opcja "Potwierdź naruszenie zabezpieczeń logowania" jest dostępna tylko w raporcie "ryzykowne logowania". |
| **Naruszone przez użytkownika (true pozytywna)** <br> Raport "ryzykowni użytkownicy" pokazuje użytkownika o podwyższonym ryzyku [stan ryzyka = zagrożone] z niskim ryzykiem [poziom ryzyka = niski] i ten użytkownik rzeczywiście naruszony. | Wybierz użytkownika i kliknij opcję "Potwierdź naruszenie zabezpieczeń przez użytkownika". | Usługa Azure AD przeniesie ryzyko użytkownika do wysokiego [stanu ryzyka = potwierdzone zabezpieczenia; Poziom ryzyka = wysoki] i zostanie dodany nowy wykrycie "Administrator potwierdził". | Obecnie opcja "Potwierdź złamany użytkownik" jest dostępna tylko w raporcie "ryzykowne użytkownicy". <br> Wykrywanie "zabezpieczenia potwierdzone przez administratora" zostało pokazane na karcie "wykrycia ryzyka, które nie są połączone z logowaniem" w raporcie "ryzykowne użytkownicy". |
| **Skorygowano użytkownika poza Azure AD Identity Protection (prawdziwe pozytywne + skorygowane)** <br> Raport "ryzykowni użytkownicy" pokazuje użytkownika o podwyższonym ryzyku, a następnie koryguje użytkownika poza Azure AD Identity Protection. | 1. Wybierz użytkownika i kliknij opcję "Potwierdź naruszenie zabezpieczeń. (Ten proces umożliwia potwierdzenie, że użytkownik rzeczywiście naruszony usługi Azure AD). <br> 2. Poczekaj, aż użytkownik "poziom ryzyka" przejdzie do pozycji wysoki. (Ten czas umożliwia usłudze Azure AD konieczny czas na przejęcie powyższej opinii do aparatu ryzyka). <br> 3. Wybierz użytkownika, a następnie kliknij pozycję "Odrzuć ryzyko użytkownika". (Ten proces potwierdza, że usługa Azure AD nie jest już naruszona.) |  Usługa Azure AD przenosi ryzyko użytkownika na brak [stan ryzyka = odrzucone; Poziom ryzyka =-] i zamyka ryzyko związane ze wszystkimi istniejącymi logowaniami z aktywnym ryzykiem. | Kliknięcie przycisku "Odrzuć ryzyko użytkownika" spowoduje zamknięcie wszystkich zagrożeń dotyczących użytkownika i poprzednich logowań. Tej akcji nie można cofnąć. |
| **Użytkownik nie został naruszony (fałszywie dodatni)** <br> Raport "ryzykowni użytkownicy" pojawia się na użytkowniku, ale nie został naruszony. | Wybierz użytkownika, a następnie kliknij pozycję "Odrzuć ryzyko użytkownika". (Ten proces służy do potwierdzenia, że użytkownik nie został naruszony, za pomocą usługi Azure AD). | Usługa Azure AD przenosi ryzyko użytkownika na brak [stan ryzyka = odrzucone; Poziom ryzyka =-]. | Kliknięcie przycisku "Odrzuć ryzyko użytkownika" spowoduje zamknięcie wszystkich zagrożeń dotyczących użytkownika i poprzednich logowań. Tej akcji nie można cofnąć. |
| Chcę zamknąć ryzyko związane z użytkownikiem, ale nie mam pewności, czy użytkownik został złamany/bezpieczny. | Wybierz użytkownika, a następnie kliknij pozycję "Odrzuć ryzyko użytkownika". (Ten proces potwierdza, że usługa Azure AD nie jest już naruszona.) | Usługa Azure AD przenosi ryzyko użytkownika na brak [stan ryzyka = odrzucone; Poziom ryzyka =-]. | Kliknięcie przycisku "Odrzuć ryzyko użytkownika" spowoduje zamknięcie wszystkich zagrożeń dotyczących użytkownika i poprzednich logowań. Tej akcji nie można cofnąć. Zalecamy skorygowanie użytkownika, klikając pozycję "Resetuj hasło" lub poprosić użytkownika o bezpieczne zresetowanie/zmianę ich poświadczeń. |

Opinie dotyczące wykrywania ryzyka użytkownika w usłudze Identity Protection są przetwarzane w trybie offline i może upłynąć trochę czasu na aktualizację. Kolumna stan przetwarzania ryzyka zapewnia bieżący stan przetwarzania informacji zwrotnych.

![Stan przetwarzania ryzyka dla ryzykownego raportu użytkownika](./media/howto-identity-protection-risk-feedback/risky-users-provide-feedback.png)

## <a name="next-steps"></a>Następne kroki

- [Informacje dotyczące wykrywania ryzyka Azure Active Directory Identity Protection](./concept-identity-protection-risks.md)