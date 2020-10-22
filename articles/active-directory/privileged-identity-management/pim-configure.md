---
title: Co to jest Privileged Identity Management? — Azure AD | Microsoft Docs
description: Zawiera omówienie Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: pim
ms.topic: overview
ms.date: 09/29/2020
ms.author: curtand
ms.custom: pim ; azuread-video-2020
ms.collection: M365-identity-device-management
ms.openlocfilehash: c3c7003ce3e8c65f6fdb7f3d847947114aee251f
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/22/2020
ms.locfileid: "92370189"
---
# <a name="what-is-azure-ad-privileged-identity-management"></a>Co to jest usługa Azure AD Privileged Identity Management?

 Privileged Identity Management (PIM) to usługa w usłudze Azure Active Directory (Azure AD), która umożliwia zarządzanie, kontrolowanie i monitorowanie dostępu do ważnych zasobów w organizacji. Te zasoby obejmują zasoby w usłudze Azure AD, na platformie Azure oraz w innych usługach online firmy Microsoft, takich jak Microsoft 365 lub Microsoft Intune. Poniższy film wideo przedstawia ważne koncepcje i funkcje programu PIM.
<br><br>

> [!VIDEO https://www.youtube.com/embed/f-0K7mRUPpQ]

## <a name="reasons-to-use"></a>Przyczyny użycia

Organizacje dążą do zminimalizowania liczby osób, które mają dostęp do zabezpieczonych informacji lub zasobów, ponieważ zmniejsza to prawdopodobieństwo uzyskania dostępu do nich przez użytkownika mającego złe zamiary lub przypadkowej modyfikacji poufnych zasobów przez osobę uprawnioną. Jednak użytkownicy nadal muszą wykonać operacje uprzywilejowane w usłudze Azure AD, platformie Azure, Microsoft 365 lub aplikacjach SaaS. Organizacje mogą udzielać użytkownikom uprawnień uprzywilejowanego dostępu do zasobów platformy Azure i usługi Azure AD. Istnieje potrzeba nadzorowania sposobu, w jaki ci użytkownicy korzystają z uprawnień administratora.

## <a name="what-does-it-do"></a>Co robi?

Privileged Identity Management zapewnia aktywację roli opartej na czasie i zatwierdzania, aby ograniczyć ryzyko nadmiernego, niepotrzebnego lub nieużywanego dostępu do zasobów, które Cię interesują. Oto niektóre z najważniejszych funkcji Privileged Identity Management:

- Zapewnianie dostępu uprzywilejowanego **just-in-time** do usługi Azure AD i zasobów platformy Azure
- Przypisywanie **czasowego** dostępu do zasobów przy użyciu daty rozpoczęcia i zakończenia
- Wymaganie **zatwierdzenia** aktywowania ról uprzywilejowanych
- Wymuszanie **uwierzytelniania wieloskładnikowego** w celu aktywowania jakiejkolwiek roli
- Używanie **uzasadnień** w celu uzyskania informacji, dlaczego użytkownicy aktywują role
- Otrzymywanie **powiadomień** o aktywowaniu ról uprzywilejowanych
- Przeprowadzanie **przeglądów dostępu** w celu upewnienia się, że użytkownicy nadal potrzebują ról
- Pobieranie **historii inspekcji** w celu przeprowadzenia inspekcji wewnętrznej lub zewnętrznej

## <a name="what-can-i-do-with-it"></a>Co mogę zrobić z nim?

Po skonfigurowaniu Privileged Identity Management zobaczysz opcje **zadania**, **Zarządzaj**i **działania** w menu nawigacji po lewej stronie. Jako administrator będziesz wybierać między opcjami, takimi jak zarządzanie **rolami usługi Azure AD**, zarządzanie rolami **zasobów platformy Azure** lub uprzywilejowanymi grupami dostępu. Po wybraniu elementów, którymi chcesz zarządzać, zobaczysz odpowiedni zestaw opcji dla tej opcji.

![Zrzut ekranu przedstawiający Privileged Identity Management w Azure Portal](./media/pim-configure/pim-quickstart.png)

## <a name="who-can-do-what"></a>Kto może zrobić?

W przypadku ról usługi Azure AD w Privileged Identity Management tylko użytkownik należący do roli administrator ról uprzywilejowanych lub Administrator globalny może zarządzać przypisaniami innych administratorów. Można [udzielić dostępu innym administratorom do zarządzania Privileged Identity Management](pim-how-to-give-access-to-pim.md). Administratorzy globalni, administratorzy zabezpieczeń, czytelnicy globalni i czytelnicy zabezpieczeń mogą również wyświetlać przypisania do ról usługi Azure AD w Privileged Identity Management.

W przypadku ról zasobów platformy Azure w Privileged Identity Management tylko administrator subskrypcji, właściciel zasobu lub administrator dostępu użytkowników do zasobów może zarządzać przypisaniami innych administratorów. Użytkownicy, którzy są administratorami ról uprzywilejowanych, administratorzy zabezpieczeń lub czytelnicy zabezpieczeń, domyślnie nie mają dostępu do wyświetlania przypisań do ról zasobów platformy Azure w Privileged Identity Management.

## <a name="scenarios"></a>Scenariusze

Usługa Privileged Identity Management obsługuje następujące scenariusze:

### <a name="privileged-role-administrator-permissions"></a>Uprawnienia administratora ról uprzywilejowanych

- Włączanie zatwierdzeń dla określonych ról
- Określ użytkowników lub grupy osoby zatwierdzającej do zatwierdzania żądań
- Wyświetlanie historii żądań i zatwierdzeń dla wszystkich ról uprzywilejowanych

### <a name="approver-permissions"></a>Uprawnienia osoby zatwierdzającej

- Wyświetlanie oczekujących zatwierdzeń (żądań)
- Zatwierdź lub Odrzuć żądania podniesienia uprawnień roli (pojedyncza i zbiorcza)
- Podaj uzasadnienie zatwierdzenia lub odrzucenia

### <a name="eligible-role-user-permissions"></a>Uprawnienia uprawnionych użytkowników roli

- Żądanie aktywacji roli wymagającej zatwierdzenia
- Wyświetlanie stanu żądania uaktywnienia
- Wykonywanie zadania w usłudze Azure AD w przypadku zatwierdzenia uaktywnienia

## <a name="terminology"></a>Terminologia

Aby lepiej zrozumieć Privileged Identity Management i dokumentację, należy zapoznać się z poniższymi postanowieniami.

| Termin lub pojęcie | Kategoria przypisania roli | Opis |
| --- | --- | --- |
| kwalifikowanie się | Typ | Przypisanie roli, które wymaga od użytkownika wykonania jednej lub kilku akcji w celu użycia tej roli. Jeśli użytkownik został zakwalifikowany do roli, oznacza to, że może aktywować tę rolę, kiedy musi wykonać zadanie uprzywilejowane. Nie ma żadnej różnicy między dostępem udzielonym komuś za pomocą trwałego przypisania roli i przypisania kwalifikowania się do roli. Jedyna różnica polega na tym, że niektórzy użytkownicy nie potrzebują tego dostępu przez cały czas. |
| aktywne | Typ | Przypisanie roli, które nie wymaga od użytkownika wykonywania żadnych akcji w celu użycia tej roli. Użytkownicy przypisani jako aktywni mają uprawnienia przypisane do danej roli. |
| aktywuj |  | Proces wykonywania jednej lub kilku akcji w celu użycia roli, do której użytkownik został zakwalifikowany. Akcje te mogą obejmować przeprowadzenie uwierzytelniania wieloskładnikowego (MFA), podanie uzasadnienia biznesowego lub żądanie zatwierdzenia od wyznaczonych osób zatwierdzających. |
| przypisano | Stan | Użytkownik, który ma aktywne przypisanie roli. |
| aktywowano | Stan | Użytkownik, który ma przypisanie kwalifikowania się do roli, wykonał akcje w celu aktywowania roli i jest teraz aktywny.  Po aktywowaniu roli użytkownik może z niej korzystać przez wstępnie skonfigurowany czas, po którym rola będzie musiała być ponownie aktywowana. |
| trwałe kwalifikowanie się | Czas trwania | Przypisanie roli, w którym użytkownik jest zawsze zakwalifikowany do aktywowania roli. |
| trwałe aktywne | Czas trwania | Przypisanie roli, w którym użytkownik zawsze może używać roli bez wykonywania żadnych akcji. |
| kwalifikowanie się z wygaśnięciem | Czas trwania | Przypisanie roli, w którym użytkownik jest zakwalifikowany do aktywowania roli w okresie określonym datą rozpoczęcia i zakończenia. |
| aktywne z wygaśnięciem | Czas trwania | Przypisanie roli, w którym użytkownik może używać roli bez wykonywania żadnych akcji w okresie określonym datą rozpoczęcia i zakończenia. |
| dostęp just-in-time (JIT) |  | Model, w którym użytkownicy uzyskują tymczasowe uprawnienia do wykonywania uprzywilejowanych zadań, dzięki czemu złośliwi lub nieautoryzowani użytkownicy nie mogą uzyskać dostępu po wygaśnięciu uprawnienia. Dostęp jest udzielany tylko wtedy, gdy użytkownicy go potrzebują. |
| zasada dostępu z najniższymi uprawnieniami |  | Zalecane rozwiązanie dotyczące zabezpieczeń, w którym każdy użytkownik ma minimalne uprawnienia wymagane do wykonania zadania, które mu przydzielono. Takie rozwiązanie pozwala zminimalizować liczbę administratorów globalnych i zamiast tego korzystać ze specyficznych ról administratorów dla konkretnych scenariuszy. |

## <a name="license-requirements"></a>Wymagania licencyjne

[!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)]

Aby uzyskać informacje na temat licencji dla użytkowników, zobacz [wymagania dotyczące licencji, aby użyć Privileged Identity Management](subscription-requirements.md).

## <a name="next-steps"></a>Następne kroki

- [Wymagania licencyjne dotyczące używania Privileged Identity Management](subscription-requirements.md)
- [Zabezpieczanie uprzywilejowanego dostępu dla wdrożeń hybrydowych i wdrożeń w chmurze w usłudze Azure AD](../roles/security-planning.md?toc=%2fazure%2factive-directory%2fprivileged-identity-management%2ftoc.json)
- [Wdrażanie usługi Privileged Identity Management](pim-deployment-plan.md)
