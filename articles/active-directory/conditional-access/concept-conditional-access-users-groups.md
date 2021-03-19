---
title: Użytkownicy i grupy w zasadach dostępu warunkowego — Azure Active Directory
description: Kto jest użytkownikami i grupami w zasadach dostępu warunkowego usługi Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 03/17/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: b878abbead68b3268891e1f35087e5942969730c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104579063"
---
# <a name="conditional-access-users-and-groups"></a>Dostęp warunkowy: Użytkownicy i grupy

Zasady dostępu warunkowego muszą zawierać przypisanie użytkownika jako jeden z sygnałów w procesie decyzyjnym. Użytkownicy mogą być dołączeni lub wykluczeni z zasad dostępu warunkowego. Azure Active Directory oblicza wszystkie zasady i gwarantuje, że wszystkie wymagania są spełnione przed udzieleniem dostępu użytkownikowi. 

> [!VIDEO https://www.youtube.com/embed/5DsW1hB3Jqs]

## <a name="include-users"></a>Uwzględnij użytkowników

Ta lista użytkowników zazwyczaj obejmuje wszystkich użytkowników, których organizacja ma dla zasad dostępu warunkowego. 

Poniższe opcje są dostępne do uwzględnienia podczas tworzenia zasad dostępu warunkowego.

- Brak
   - Nie wybrano żadnych użytkowników
- Wszyscy użytkownicy
   - Wszyscy użytkownicy, którzy istnieją w katalogu, w tym Goście B2B.
- Wybieranie użytkowników i grup
   - Wszyscy Goście i użytkownicy zewnętrzni
      - Ten wybór obejmuje wszystkich Gości i użytkowników zewnętrznych, w tym dowolny użytkownik z `user type` atrybutem ustawionym na `guest` . Ten wybór dotyczy również wszystkich użytkowników zewnętrznych zalogowanych z innej organizacji, takiej jak dostawca rozwiązań w chmurze (CSP). 
   - Role katalogu
      - Umożliwia administratorom wybranie określonych wbudowanych ról katalogu usługi Azure AD używanych do określania przypisania zasad. Na przykład organizacje mogą tworzyć bardziej restrykcyjne zasady dla użytkowników, którym przypisano rolę administratora globalnego. Inne typy ról nie są obsługiwane, w tym role katalogu administracyjnego z zakresem jednostki, role niestandardowe.
   - Użytkownicy i grupy
      - Umożliwia kierowanie określonych zestawów użytkowników. Na przykład organizacje mogą wybrać grupę, która zawiera wszystkich członków działu kadr, gdy aplikacja usługi kadr zostanie wybrana jako aplikacja w chmurze. Grupa może być dowolnymi grupami w usłudze Azure AD, w tym dynamicznymi lub przypisanymi grupami zabezpieczeń i dystrybucji. Zasady zostaną zastosowane do zagnieżdżonych użytkowników i grup.

> [!IMPORTANT]
> Podczas wybierania użytkowników i grup uwzględnionych w zasadach dostępu warunkowego istnieje ograniczenie liczby indywidualnych użytkowników, które można dodać bezpośrednio do zasad dostępu warunkowego. W przypadku dużej liczby indywidualnych użytkowników, którzy są musieli dodać bezpośrednio do zasad dostępu warunkowego, zalecamy umieszczenie użytkowników w grupie i przypisanie grupy do zasad dostępu warunkowego.

> [!WARNING]
> Jeśli użytkownicy lub grupy są członkami ponad 2048 grup, ich dostęp może być zablokowany. Ten limit dotyczy zarówno bezpośrednich, jak i zagnieżdżonych członkostwa w grupach.

> [!WARNING]
> Zasady dostępu warunkowego nie obsługują użytkowników, którym przypisano rolę katalogu [zakresu do jednostki administracyjnej](../roles/admin-units-assign-roles.md) lub ról katalogu w zakresie bezpośrednio do obiektu, na przykład za pomocą [ról niestandardowych](../roles/custom-create.md).

## <a name="exclude-users"></a>Wyklucz użytkowników

Gdy organizacje obejmują i wykluczają użytkownika lub grupę, użytkownik lub grupa jest wykluczona z zasad, ponieważ akcja wykluczania zastępuje zasady Uwzględnij. Wykluczenia są często używane w przypadku dostępu awaryjnego lub kont ze szkłami do awarii. Więcej informacji o kontach dostępu awaryjnego i o tym, dlaczego są ważne, można znaleźć w następujących artykułach: 

* [Zarządzanie kontami dostępu awaryjnego w usłudze Azure AD](../roles/security-emergency-access.md)
* [Utwórz odporną strategię zarządzania kontrolą dostępu za pomocą Azure Active Directory](../authentication/concept-resilient-controls.md)

Poniższe opcje są dostępne do wykluczenia podczas tworzenia zasad dostępu warunkowego.

- Wszyscy Goście i użytkownicy zewnętrzni
   - Ten wybór obejmuje wszystkich Gości i użytkowników zewnętrznych, w tym dowolny użytkownik z `user type` atrybutem ustawionym na `guest` . Ten wybór dotyczy również wszystkich użytkowników zewnętrznych zalogowanych z innej organizacji, takiej jak dostawca rozwiązań w chmurze (CSP). 
- Role katalogu
   - Umożliwia administratorom wybranie określonych ról katalogu usługi Azure AD używanych do określania przypisania. Na przykład organizacje mogą tworzyć bardziej restrykcyjne zasady dla użytkowników, którym przypisano rolę administratora globalnego.
- Użytkownicy i grupy
   - Umożliwia kierowanie określonych zestawów użytkowników. Na przykład organizacje mogą wybrać grupę, która zawiera wszystkich członków działu kadr, gdy aplikacja usługi kadr zostanie wybrana jako aplikacja w chmurze. Grupa może być dowolnymi grupami w usłudze Azure AD, w tym dynamicznymi lub przypisanymi grupami zabezpieczeń i dystrybucji.

### <a name="preventing-administrator-lockout"></a>Uniemożliwianie blokady administratora

Aby zapobiec blokowaniu przez administratora swoich katalogów podczas tworzenia zasad zastosowanych do **wszystkich użytkowników** i **wszystkich aplikacji**, zobaczy poniższe ostrzeżenie.

> Nie blokuj siebie! Zalecamy stosowanie zasad do małego zestawu użytkowników najpierw w celu sprawdzenia, czy działa zgodnie z oczekiwaniami. Zalecamy także wyłączenie co najmniej jednego administratora z tych zasad. Pozwala to zagwarantować, że nadal masz dostęp, i aktualizować zasady, jeśli wymagana jest zmiana. Przejrzyj odpowiednich użytkowników i aplikacje.

Domyślnie zasady zapewniają opcję wykluczenia bieżącego użytkownika z zasad, ale ta wartość domyślna może zostać przesłonięta przez administratora, jak pokazano na poniższej ilustracji. 

![Ostrzeżenie, nie blokuj siebie!](./media/concept-conditional-access-users-groups/conditional-access-users-and-groups-lockout-warning.png)

Jeśli nie masz zablokować[, co należy zrobić, jeśli nie masz blokady Azure Portal?](troubleshoot-conditional-access.md#what-to-do-if-you-are-locked-out-of-the-azure-portal)

## <a name="next-steps"></a>Następne kroki

- [Dostęp warunkowy: aplikacje lub akcje w chmurze](concept-conditional-access-cloud-apps.md)

- [Wspólne zasady dostępu warunkowego](concept-conditional-access-policy-common.md)
