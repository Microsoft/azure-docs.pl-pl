---
title: Ochrona tożsamości i użytkownicy B2B — Azure Active Directory
description: Korzystanie z usługi Identity Protection z użytkownikami B2B, jak to działa i znane ograniczenia
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
ms.openlocfilehash: c9ec9e110c3a476c9096ae3e216c9780da0e0f49
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "88949200"
---
# <a name="identity-protection-and-b2b-users"></a>Usługa Identity Protection i użytkownicy B2B

Dzięki funkcji współpracy B2B usługi Azure AD organizacje mogą wymuszać zasady oparte na ryzyku dla użytkowników B2B przy użyciu usługi Identity Protection. Te zasady można skonfigurować na dwa sposoby:

- Administratorzy mogą skonfigurować wbudowane zasady oparte na ryzyku dotyczącego ochrony tożsamości, które mają zastosowanie do wszystkich aplikacji, które obejmują użytkowników-Gości.
- Administratorzy mogą skonfigurować zasady dostępu warunkowego, korzystając z ryzyka związanego z logowaniem jako warunek, który obejmuje użytkowników-Gości.

## <a name="how-is-risk-evaluated-for-b2b-collaboration-users"></a>Jak są oceniane ryzyko dla użytkowników współpracy B2B

Ryzyko dla użytkowników związane z użytkownikami współpracy B2B jest oceniane w katalogu macierzystym. Ryzyko związane z logowaniem w czasie rzeczywistym dla tych użytkowników jest oceniane w katalogu zasobów przy próbie uzyskania dostępu do zasobu.

## <a name="limitations-of-identity-protection-for-b2b-collaboration-users"></a>Ograniczenia dotyczące usługi Identity Protection dla użytkowników współpracy B2B

Istnieją pewne ograniczenia w implementacji ochrony tożsamości dla użytkowników współpracy B2B w katalogu zasobów ze względu na ich tożsamość w katalogu macierzystym. Główne ograniczenia są następujące:

- Jeśli użytkownik-Gość wyzwala zasadę ryzyka użytkownika ochrony tożsamości w celu wymuszenia zresetowania hasła, **zostanie zablokowany**. Ten blok jest spowodowany brakiem możliwości resetowania haseł w katalogu zasobów.
- **Użytkownicy-Goście nie są wyświetlani w raporcie ryzykowni użytkownicy**. Ta utrata widoczności jest spowodowana oceną ryzyka występującym w katalogu macierzystym użytkownika B2B.
- Administratorzy **nie mogą odrzucić lub skorygować ryzykownego użytkownika współpracy B2B** w katalogu zasobów. Ta utrata funkcjonalności wynika z tego, że Administratorzy w katalogu zasobów nie mają dostępu do katalogu macierzystego użytkownika B2B.

### <a name="why-cant-i-remediate-risky-b2b-collaboration-users-in-my-directory"></a>Dlaczego nie mogę skorygować ryzykownych użytkowników współpracy B2B w moim katalogu?

Ocena ryzyka i korygowanie użytkowników B2B odbywają się w ich katalogu macierzystym. Z tego względu użytkownicy-Goście nie są wyświetlani w raporcie ryzykowni użytkownicy w katalogu zasobów i Administratorzy w katalogu zasobów nie mogą wymusić bezpiecznego resetowania hasła dla tych użytkowników.

### <a name="what-do-i-do-if-a-b2b-collaboration-user-was-blocked-due-to-a-risk-based-policy-in-my-organization"></a>Co zrobić, jeśli użytkownik współpracy B2B został zablokowany ze względu na zasady oparte na ryzyku w mojej organizacji?

Jeśli ryzykowny użytkownik B2B w katalogu jest blokowany przez zasady oparte na ryzyku, użytkownik będzie musiał skorygować to ryzyko w katalogu macierzystym. Użytkownicy mogą skorygować ich ryzyko przez przeprowadzenie bezpiecznego resetowania hasła w katalogu macierzystym. Jeśli w katalogu macierzystym nie włączono funkcji samoobsługowego resetowania hasła, należy skontaktować się z pracownikami działu IT swojej organizacji, aby ręcznie odrzucić ryzyko lub zresetować swoje hasło.

### <a name="how-do-i-prevent-b2b-collaboration-users-from-being-impacted-by-risk-based-policies"></a>Jak mogę uniemożliwić użytkownikom współpracy B2B wpływ na zasady oparte na ryzyku?

Wyłączenie użytkowników B2B z zasad dostępu warunkowego opartych na ryzyku organizacji uniemożliwi użytkownikom B2B lub zablokowanie ich oceny ryzyka. Aby wykluczyć tych użytkowników B2B, Utwórz grupę w usłudze Azure AD, która zawiera wszystkich użytkowników-Gości w organizacji. Następnie Dodaj tę grupę jako wykluczenie dla wbudowanego ryzyka użytkownika ochrony tożsamości i zasad dotyczących ryzyka związanego z logowaniem, a także wszelkich zasad dostępu warunkowego, które używają ryzyka związanego z logowaniem jako warunku.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z następującymi artykułami dotyczącymi współpracy B2B w usłudze Azure AD:

- [Czym jest współpraca B2B w usłudze Azure AD?](../external-identities/what-is-b2b.md)