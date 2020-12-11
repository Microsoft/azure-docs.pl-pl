---
title: Co to jest dostęp warunkowy w usłudze Azure Active Directory?
description: Dowiedz się, jak dostęp warunkowy jest sercem nowej płaszczyzny kontroli sterowanej tożsamością.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: overview
ms.date: 10/16/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.custom: contperf-fy20q4, azuread-video-2020
ms.openlocfilehash: 9c5f7684083ec533c8100785787572ebe4456bcc
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/10/2020
ms.locfileid: "97030250"
---
# <a name="what-is-conditional-access"></a>Co to jest dostęp warunkowy?

Nowoczesnego obwodu zabezpieczeń wykracza poza sieć organizacji w celu uwzględnienia tożsamości użytkowników i urządzeń. Organizacje mogą korzystać z tych sygnałów tożsamości w ramach decyzji dotyczących kontroli dostępu. 

> [!VIDEO https://channel9.msdn.com/Shows/Docs-Azure/Azure-AD-Conditional-Access/player]

Dostęp warunkowy jest narzędziem używanym przez Azure Active Directory do przenoszenia sygnałów ze sobą, podejmowania decyzji i wymuszania zasad organizacji. Dostęp warunkowy jest sercem nowej płaszczyzny kontroli sterowanej tożsamością.

![Koncepcyjny sygnał warunkowy i decyzja o konieczności uzyskania wymuszania](./media/overview/conditional-access-signal-decision-enforcement.png)

Zasady dostępu warunkowego są najprostszą instrukcją If, jeśli użytkownik chce uzyskać dostęp do zasobu, a następnie musi wykonać akcję. Przykład: Menedżer płac chce uzyskać dostęp do aplikacji do obsługi płac i jest wymagany do przeprowadzenia uwierzytelniania wieloskładnikowego w celu uzyskania do niego dostępu.

Administratorzy mają dwie cele podstawowe:

- Umożliwienie użytkownikom produktywnej pracy w dowolnym czasie i miejscu.
- Ochrona zasobów organizacji

Korzystając z zasad dostępu warunkowego, można zastosować odpowiednie metody kontroli dostępu, gdy jest to konieczne, aby zapewnić bezpieczeństwo organizacji i pozostać poza nią, gdy jest to konieczne.

![Przepływ procesu koncepcyjnego dostępu warunkowego](./media/overview/conditional-access-overview-how-it-works.png)

> [!IMPORTANT]
> Zasady dostępu warunkowego są wymuszane po zakończeniu uwierzytelniania pierwszego. Dostęp warunkowy nie jest to pierwszy wiersz obrony organizacji dla scenariuszy takich jak ataki typu "odmowa usługi" (DoS), ale może użyć sygnałów z tych zdarzeń w celu określenia dostępu.

## <a name="common-signals"></a>Typowe sygnały

Typowe sygnały, które mogą być wykonywane przez dostęp warunkowy w przypadku podejmowania decyzji dotyczących zasad, obejmują następujące sygnały:

- Członkostwo użytkowników lub grup
   - Zasady mogą być wskazywane określonym użytkownikom i grupom, które umożliwiają administratorom precyzyjne sterowanie dostępem.
- Informacje o lokalizacji adresu IP
   - Organizacje mogą tworzyć zakresy zaufanych adresów IP, które mogą być używane podczas podejmowania decyzji dotyczących zasad. 
   - Administratorzy mogą określać wszystkie zakresy adresów IP krajów/regionów w celu blokowania lub zezwalania na ruch z programu.
- Urządzenie
   - Podczas wymuszania zasad dostępu warunkowego można używać użytkowników z urządzeniami o określonych platformach lub oznaczonych przy użyciu określonego stanu.
- Aplikacja
   - Użytkownicy próbujący uzyskać dostęp do określonych aplikacji mogą wyzwolić różne zasady dostępu warunkowego. 
- Wykrywanie ryzyka w czasie rzeczywistym i obliczone
   - Program sygnalizuje integrację z Azure AD Identity Protection umożliwia zasadom dostępu warunkowego zidentyfikowanie ryzykownego zachowania logowania. Zasady mogą następnie zmusić użytkowników do wprowadzania zmian haseł lub uwierzytelniania wieloskładnikowego w celu zmniejszenia ich poziomu ryzyka lub uzyskania dostępu do momentu, gdy administrator wykona ręczną akcję.
- Microsoft Cloud App Security (MCAS)
   - Umożliwia monitorowanie i nadzorowanie dostępu do aplikacji przez użytkowników w czasie rzeczywistym, zwiększanie widoczności i kontroli dostępu do i działań wykonywanych w środowisku chmury.

## <a name="common-decisions"></a>Typowe decyzje

- Blokowanie dostępu
   - Najbardziej restrykcyjna decyzja
- Udzielanie dostępu
   - Najmniej restrykcyjna decyzja może nadal wymagać co najmniej jednej z następujących opcji:
      - Wymagaj uwierzytelniania wieloskładnikowego
      - Wymagaj, aby urządzenie było oznaczone jako zgodne
      - Wymagaj hybrydowego urządzenia dołączonego do usługi Azure AD
      - Wymaganie zatwierdzonej aplikacji klienckiej
      - Wymagaj zasad ochrony aplikacji (wersja zapoznawcza)

## <a name="commonly-applied-policies"></a>Najczęściej stosowane zasady

Wiele organizacji ma [typowy dostęp do zasad dostępu warunkowego,](concept-conditional-access-policy-common.md) takich jak:

- Wymaganie uwierzytelniania wieloskładnikowego dla użytkowników z rolami administracyjnymi
- Wymaganie uwierzytelniania wieloskładnikowego dla zadań zarządzania platformy Azure
- Blokowanie logowań użytkowników próbujących korzystać ze starszych protokołów uwierzytelniania
- Wymaganie zaufanych lokalizacji do rejestracji Multi-Factor Authentication usługi Azure AD
- Blokowanie lub udzielanie dostępu z określonych lokalizacji
- Blokowanie ryzykownych zachowań logowania
- Wymaganie urządzeń zarządzanych przez organizację dla określonych aplikacji

## <a name="license-requirements"></a>Wymagania licencyjne

[!INCLUDE [Active Directory P1 license](../../../includes/active-directory-p1-license.md)]

Klienci z [licencjami na Microsoft 365 Business Premium](/office365/servicedescriptions/microsoft-365-service-descriptions/microsoft-365-business-service-description) mają również dostęp do funkcji dostępu warunkowego. 

[Ryzyko związane z logowaniem](concept-conditional-access-conditions.md#sign-in-risk) wymaga dostępu do [ochrony tożsamości](../identity-protection/overview-identity-protection.md)

## <a name="next-steps"></a>Następne kroki

- [Kompilowanie zasad dostępu warunkowego przez element](concept-conditional-access-policies.md)
- [Planowanie wdrażania dostępu warunkowego](plan-conditional-access.md)
- [Informacje na temat ochrony tożsamości](../identity-protection/overview-identity-protection.md)
- [Dowiedz się więcej o Microsoft Cloud App Security](/cloud-app-security/what-is-cloud-app-security)
- [Dowiedz się więcej o Microsoft Intune](/intune/index)
