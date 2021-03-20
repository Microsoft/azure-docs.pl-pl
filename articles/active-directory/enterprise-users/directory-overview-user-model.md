---
title: Użytkownicy, grupy, Licencjonowanie i role w Azure Active Directory
description: Relacje między użytkownikami i przypisanymi licencjami, role administratora oraz członkostwo w grupie w usłudze Azure Active Directory
keywords: ''
author: curtand
manager: daveba
ms.author: curtand
ms.reviewer: krbain
ms.date: 12/02/2020
ms.topic: overview
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
services: active-directory
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: ce4330b72ef7e1440689e0ec4af9009f551553a3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96547274"
---
# <a name="what-is-enterprise-user-management"></a>Co to jest zarządzanie użytkownikami w przedsiębiorstwie?

Ten artykuł, przeznaczony dla administratorów usługi Azure AD, zawiera wprowadzenie do relacji między najważniejszymi zadaniami [zarządzania tożsamościami](../fundamentals/active-directory-whatis.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context) użytkowników pod kątem grup, licencji, wdrożonych aplikacji dla przedsiębiorstw i ról administratora. Rozwój organizacji zwiększa przydatność grup i ról administratora usługi Azure AD, które pozwalają wykonywać następujące zadania:

* Przypisywanie licencji grupom, a nie do poszczególnym użytkownikom
* Delegowanie uprawnień w celu rozdzielenia zadań zarządzania usługą Azure AD na mniej uprzywilejowane role
* Przypisywanie grupom dostępu do aplikacji przedsiębiorstwa

## <a name="assign-users-to-groups"></a>Przypisywanie użytkowników do grup

Grup usługi Azure AD można użyć do przypisania licencji wielu użytkownikom lub przypisania użytkownikom dostępu do wdrożonych aplikacji przedsiębiorstwa. Za pomocą grup można przypisywać wszystkie role administratorów z wyjątkiem administratora globalnego w usłudze Azure AD lub można udzielić dostępu do zasobów zewnętrznych, takich jak aplikacje SaaS lub witryny programu SharePoint.

Aby zwiększyć elastyczność i ograniczyć nakład pracy związanej z zarządzaniem członkostwem w grupach, w usłudze Azure AD można używać [grup dynamicznych](groups-create-rule.md), które pozwalają automatycznie zwiększać i zmniejszać liczbę członków w grupie. Dla każdego użytkownika, który jest członkiem co najmniej jednej grupy dynamicznej potrzebna jest licencja usługi Azure AD w wersji Premium P1.

## <a name="assign-licenses-to-groups"></a>Przypisywanie licencji do grup

Przypisywanie lub usuwanie licencji dla poszczególnych użytkowników bywa czasochłonne i wymaga skupienia uwagi. Z kolei [przypisywanie licencji do grup](../fundamentals/license-users-groups.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context) może ułatwić zarządzanie licencjami na dużą skalę.

Dodanie użytkowników do licencjonowanej grupy w usłudze Azure AD powoduje automatyczne przypisanie im odpowiednich licencji. Gdy użytkownicy opuszczają grupę, usługa Azure AD usuwa dla nich przypisania licencji. Jeśli grupy usługi Azure AD nie są używane, zbiorcze dodawanie lub usuwanie licencji użytkowników — którzy są zatrudniani przez organizację lub odchodzą z niej — wymaga napisania skryptu programu PowerShell lub użycia interfejsu API programu Graph.

Jeśli zabraknie licencji lub wystąpi jakiś problem, na przykład brak możliwości jednoczesnego przypisania planów usług, stan licencji grupy można sprawdzić w witrynie Azure Portal.

>[!NOTE]
>Funkcja licencjonowania opartego na grupach jest obecnie dostępna w publicznej wersji zapoznawczej. W wersji zapoznawczej funkcja ta jest dostępna w dowolnym płatnym planie licencjonowania usługi Azure Active Directory (Azure AD) lub w wersji próbnej.

## <a name="delegate-administrator-roles"></a>Delegowanie roli administratorów

Wiele dużych organizacji chce, aby ich użytkownicy mogli uzyskiwać uprawnienia wystarczające do wykonywania zadań służbowych ale bez przypisywania wysoce uprzywilejowanej roli administratora globalnego. Na przykład mogą to być użytkownicy, którzy zajmują się rejestrowaniem aplikacji. Poniżej przedstawiono przykładowe nowe role administratora usługi Azure AD, które ułatwiają bardziej precyzyjne rozdzielenie zadań związanych z zarządzaniem aplikacjami:

 Nazwa roli | Podsumowanie uprawnień
 --------- | -------------------
 **Administrator aplikacji** | Może dodawać aplikacje przedsiębiorstwa i rejestracje aplikacji oraz zarządzać nimi, a także konfigurować ustawienia aplikacji serwera proxy. Administratorzy aplikacji mogą wyświetlać zasady dostępu warunkowego i urządzenia, ale nie zarządzać nimi.
 **Administrator aplikacji w chmurze** | Może dodawać aplikacje przedsiębiorstwa i rejestracje aplikacji przedsiębiorstwa oraz zarządzać nimi. Ta rola ma wszystkie uprawnienia administratora aplikacji, ale nie może zarządzać ustawieniami serwera proxy aplikacji.
**Deweloper aplikacji** | Może dodawać i aktualizować rejestracje aplikacji, ale nie może zarządzać aplikacjami przedsiębiorstwa ani konfigurować serwera proxy aplikacji.

Aktualnie są dodawane nowe role administratora usługi Azure AD. Informacje o aktualnie dostępnych rolach można znaleźć w witrynie Azure Portal lub [dokumentacji uprawnień ról administratora](../roles/permissions-reference.md).

## <a name="assign-app-access"></a>Przyznawanie dostępu do aplikacji

Za pomocą usługi Azure AD można przypisywać dostęp grup do [aplikacji dla przedsiębiorstw wdrożonych w organizacji usługi Azure AD](../manage-apps/assign-user-or-group-access-portal.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context). Połączenie grup dynamicznych z grupowym przypisaniem aplikacji umożliwia automatyzowanie przypisań dostępu użytkowników do aplikacji w miarę rozwoju organizacji. Przyznawanie dostępu do aplikacji przedsiębiorstwa wymaga licencji usługi Azure Active Directory w wersji Premium P1 lub Premium P2.

Usługa Azure AD umożliwia również precyzyjną kontrolę danych przesyłanych między aplikacją a grupami, którym przyznano dostęp. Po otwarciu aplikacji w bloku [Aplikacje dla przedsiębiorstw](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps) i wybraniu pozycji **Inicjowanie obsługi administracyjnej** można wykonywać następujące czynności:

* Konfigurowanie automatycznej aprowizacji dla aplikacji, które ją obsługują
* Podawanie poświadczeń umożliwiających nawiązanie połączenia z interfejsem API aplikacji służącym do zarządzania użytkownikami
* Konfigurowanie mapowań umożliwiających sterowanie przepływem atrybutów użytkowników między aplikacją a usługą Azure AD podczas aprowizowania lub aktualizowania kont użytkowników
* Uruchamianie i zatrzymywanie usługi aprowizacji Azure AD dla aplikacji, czyszczenie pamięci podręcznej aprowizacji oraz ponowne uruchamianie usługi
* Wyświetlanie **raportu o działaniach aprowizacji**, który zawiera rejestr operacji tworzenia, aktualizowania i usuwania wszystkich użytkowników i grup między aplikacją a usługą Azure AD, oraz **raportu o błędach aprowizacji**, który zawiera bardziej szczegółowe komunikaty o błędach

## <a name="next-steps"></a>Następne kroki

Jeśli dopiero zaczynasz administrować usługą Azure AD, zapoznaj się z [podstawową dokumentacją usługi Azure Active Directory](../fundamentals/index.yml).

Możesz również zacząć [tworzyć grupy](../fundamentals/active-directory-groups-create-azure-portal.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context), [przypisywać licencje](../fundamentals/license-users-groups.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context), [przyznawać dostęp do aplikacji](../manage-apps/assign-user-or-group-access-portal.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context) lub [przypisywać role administratora](../roles/permissions-reference.md).