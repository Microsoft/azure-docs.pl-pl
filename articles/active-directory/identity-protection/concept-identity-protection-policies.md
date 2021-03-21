---
title: Zasady Azure AD Identity Protection
description: Identyfikowanie trzech zasad włączonych przy użyciu programu Identity Protection
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 05/20/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 28a9080ce878e262573adf0b3c79394079c09ca2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94835972"
---
# <a name="identity-protection-policies"></a>Zasady ochrony tożsamości

Azure Active Directory Identity Protection obejmuje trzy domyślne zasady, które Administratorzy mogą włączyć. Te zasady obejmują ograniczone dostosowanie, ale mają zastosowanie w przypadku większości organizacji. Wszystkie zasady zezwalają na wykluczenie użytkowników, takich jak [dostęp awaryjny lub konta administratora z przerwaniem](../roles/security-emergency-access.md).

![Zasady ochrony tożsamości](./media/concept-identity-protection-policies/identity-protection-policies.png)

## <a name="azure-ad-mfa-registration-policy"></a>Zasady rejestracji w usłudze Azure AD MFA

Usługa Identity Protection może pomóc organizacjom w przewróceniu usługi Azure AD Multi-Factor Authentication (MFA) przy użyciu zasad dostępu warunkowego, które wymagają rejestracji podczas logowania. Włączenie tych zasad jest świetnym sposobem zapewnienia, że nowi użytkownicy w organizacji zostali zarejestrowani na potrzeby uwierzytelniania wieloskładnikowego w pierwszym dniu. Uwierzytelnianie wieloskładnikowe to jedna z metod samoobsługowego rozwiązywania problemów dotyczących zdarzeń związanych z ryzykiem w ramach usługi Identity Protection. Samokorygowanie umożliwia użytkownikom samodzielne podejmowanie działań w celu zmniejszenia liczby wywołań pomocy technicznej.

Więcej informacji na temat usługi Azure AD Multi-Factor Authentication można znaleźć w artykule, [jak to działa: usługa Azure AD Multi-Factor Authentication](../authentication/concept-mfa-howitworks.md).

## <a name="sign-in-risk-policy"></a>Zasady ryzyka związanego z logowaniem

Program Identity Protection analizuje sygnały z każdego logowania, zarówno w czasie rzeczywistym, jak i offline, a następnie oblicza ocenę ryzyka na podstawie prawdopodobieństwa, że logowanie nie zostało wykonane przez użytkownika. Administratorzy mogą podejmować decyzje na podstawie tego sygnału oceny ryzyka, aby wymusić wymagania organizacyjne. Administratorzy mogą wybrać blokowanie dostępu, zezwalanie na dostęp lub zezwalanie na dostęp, ale wymagają uwierzytelniania wieloskładnikowego.

W przypadku wykrycia ryzyka użytkownicy mogą wykonywać uwierzytelnianie wieloskładnikowe w celu samokorygowania i zamknąć ryzykowne zdarzenie logowania, aby zapobiec niepotrzebnemu hałasowi dla administratorów.

> [!NOTE] 
> Przed wyzwoleniem zasad dotyczących ryzyka związanego z logowaniem użytkownicy muszą wcześniej zarejestrować się w usłudze Azure AD Multi-Factor Authentication.

### <a name="custom-conditional-access-policy"></a>Niestandardowe zasady dostępu warunkowego

Administratorzy mogą również utworzyć niestandardowe zasady dostępu warunkowego, w tym ryzyko logowania jako warunek przypisania. Więcej informacji na temat ryzyka jako warunku w zasadach dostępu warunkowego można znaleźć w artykule, [dostęp warunkowy: warunki](../conditional-access/concept-conditional-access-conditions.md#sign-in-risk)

![Zasady dotyczące ryzyka związanego z logowaniem niestandardowym dostępu warunkowego](./media/concept-identity-protection-policies/identity-protection-custom-sign-in-policy.png)

## <a name="user-risk-policy"></a>Zasady ryzyka związanego z użytkownikiem

Program Identity Protection może obliczyć, co jest bardzo zrozumiałe w przypadku zachowania użytkownika i którego należy użyć do podejmowania decyzji o podstawowym zagrożeniu. Ryzyko związane z użytkownikiem to obliczenie prawdopodobieństwa naruszenia zabezpieczeń tożsamości. Administratorzy mogą podejmować decyzje na podstawie tego sygnału oceny ryzyka, aby wymusić wymagania organizacyjne. Administratorzy mogą wybrać blokowanie dostępu, zezwalanie na dostęp lub zezwalanie na dostęp, ale wymagać zmiany hasła przy użyciu funkcji samoobsługowego [resetowania hasła w usłudze Azure AD](../authentication/howto-sspr-deployment.md).

W przypadku wykrycia ryzyka użytkownicy mogą przeprowadzić Samoobsługowe resetowanie haseł w celu samoobsługowego rozwiązywania problemów i zamknąć zdarzenie związane z ryzykiem użytkownika, aby zapobiec niepotrzebnemu hałasowi dla administratorów.

> [!NOTE] 
> Przed wyzwoleniem zasad ryzyka dla użytkowników należy wcześniej zarejestrować się do samoobsługowego resetowania hasła.

## <a name="next-steps"></a>Następne kroki

- [Włącz Samoobsługowe resetowanie haseł w usłudze Azure AD](../authentication/howto-sspr-deployment.md)

- [Włączanie uwierzytelniania wieloskładnikowego w usłudze Azure AD](../authentication/howto-mfa-getstarted.md)

- [Włącz zasady rejestracji Multi-Factor Authentication usługi Azure AD](howto-identity-protection-configure-mfa-policy.md)

- [Włącz zasady logowania i ryzyka dla użytkowników](howto-identity-protection-configure-risk-policies.md)
