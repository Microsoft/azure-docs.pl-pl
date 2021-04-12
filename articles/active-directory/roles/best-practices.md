---
title: Najlepsze rozwiązania dla ról usługi Azure AD — Azure Active Directory
description: Najlepsze rozwiązania dotyczące korzystania z ról Azure Active Directory.
services: active-directory
author: rolyon
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: conceptual
ms.date: 03/28/2021
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 78f4642b8f9f1ede65766a0026940c0af0f01ec2
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2021
ms.locfileid: "106111778"
---
# <a name="best-practices-for-azure-ad-roles"></a>Najlepsze rozwiązania dla ról usługi Azure AD

W tym artykule opisano niektóre najlepsze rozwiązania dotyczące korzystania z funkcji kontroli dostępu opartej na rolach Azure Active Directory (Azure AD RBAC). Te najlepsze rozwiązania wynikają z naszych rozwiązań związanych z usługą Azure AD RBAC i klientami, takimi jak siebie. Zachęcamy również do zapoznaniu się z naszymi szczegółowymi wskazówkami dotyczącymi zabezpieczeń w [zakresie zabezpieczania dostępu uprzywilejowanego dla wdrożeń hybrydowych i w chmurze w usłudze Azure AD](security-planning.md).

## <a name="1-manage-to-least-privilege"></a>1. Zarządzanie do najniższych uprawnień

Planując strategię kontroli dostępu, najlepszym rozwiązaniem jest zarządzanie najniższymi uprawnieniami. Najniższe uprawnienia oznacza przyznanie administratorom dokładnie uprawnień potrzebnych do wykonywania ich zadań. Istnieją trzy aspekty, które należy wziąć pod uwagę podczas przypisywania roli do administratorów: określony zestaw uprawnień w określonym zakresie, przez określony okres czasu. Należy unikać przypisywania szerszych ról w szerszych zakresach, nawet jeśli początkowo wydaje się to lepiej wygodniejsze. Ograniczając role i zakresy, ograniczasz, jakie zasoby są zagrożone, jeśli zabezpieczenia podmiotu zabezpieczeń zostały kiedykolwiek naruszone. Usługa Azure AD RBAC obsługuje ponad 65 [ról wbudowanych](permissions-reference.md). Istnieją role usługi Azure AD do zarządzania obiektami katalogu, takimi jak użytkownicy, grupy i aplikacje, a także do zarządzania usługami Microsoft 365, takimi jak Exchange, SharePoint i Intune. Aby lepiej zrozumieć wbudowane role usługi Azure AD, zobacz [Opis ról w Azure Active Directory](concept-understand-roles.md). Jeśli nie ma roli wbudowanej, która spełnia Twoje potrzeby, możesz utworzyć własne [role niestandardowe](custom-create.md).  
 
### <a name="finding-the-right-roles"></a>Znajdowanie odpowiednich ról

Wykonaj następujące kroki, aby ułatwić znalezienie odpowiedniej roli.

1. W Azure Portal Otwórz [rolę i administratorów](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RolesAndAdministrators) , aby wyświetlić listę ról usługi Azure AD.

1. Użyj filtru **usługi** , aby zawęzić listę ról.

    ![Strona role i Administratorzy w usłudze Azure AD z otwartym filtrem usługi](./media/best-practices/roles-administrators.png)

1. Zapoznaj się z dokumentacją [wbudowanych ról usługi Azure AD](permissions-reference.md) . Uprawnienia skojarzone z każdą rolą są wyświetlane w celu uzyskania lepszej czytelności. Aby zrozumieć strukturę i znaczenie uprawnień roli, zobacz [Jak zrozumieć uprawnienia roli](permissions-reference.md#how-to-understand-role-permissions).

1. Zapoznaj się z dokumentacją dotyczącą [najmniej uprzywilejowanych ról według zadań](delegate-by-task.md) .

## <a name="2-use-privileged-identity-management-to-grant-just-in-time-access"></a>2. Użyj Privileged Identity Management, aby udzielić dostępu just in Time

Jedną z zasad najniższych uprawnień jest to, że dostęp powinien być udzielany tylko przez określony czas. [Azure AD Privileged Identity Management (PIM)](../privileged-identity-management/pim-configure.md) umożliwia udzielanie użytkownikom dostępu just in Time do administratorów. Firma Microsoft zaleca włączenie usługi PIM w usłudze Azure AD. W przypadku korzystania z usługi PIM użytkownik może być uprawnionym członkiem roli usługi Azure AD. Następnie może aktywować swoją rolę dla ograniczonego okresu, za każdym razem, gdy musi go używać. Dostęp uprzywilejowany jest automatycznie usuwany po upływie limitu czasu. [Ustawienia PIM](../privileged-identity-management/pim-how-to-change-default-settings.md) można także skonfigurować tak, aby wymagały zatwierdzenia lub otrzymywać wiadomości e-mail z powiadomieniem, gdy ktoś aktywuje przypisanie roli. Powiadomienia informują o alertach, gdy nowi użytkownicy są dodawani do ról o wysokim poziomie uprawnień. 

## <a name="3-turn-on-multi-factor-authentication-for-all-your-administrator-accounts"></a>3. Włącz usługę uwierzytelniania wieloskładnikowego dla wszystkich kont administratora

[W oparciu o nasze studia](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/your-pa-word-doesn-t-matter/ba-p/731984)Twoje konto jest 99,9% mniej prawdopodobnie zostanie naruszone, jeśli używasz uwierzytelniania wieloskładnikowego (MFA). 
 
Uwierzytelnianie wieloskładnikowe można włączyć w rolach usługi Azure AD przy użyciu dwóch metod:
- [Ustawienia roli](../privileged-identity-management/pim-how-to-change-default-settings.md) w Privileged Identity Management
- [Dostęp warunkowy](../conditional-access/howto-conditional-access-policy-admin-mfa.md)

## <a name="4-configure-recurring-access-reviews-to-revoke-unneeded-permissions-over-time"></a>4. Skonfiguruj cykliczne przeglądy dostępu, aby odwołać niepotrzebne uprawnienia w czasie

Przeglądy dostępu umożliwiają organizacjom regularne przeglądanie dostępu administratora, aby upewnić się, że tylko odpowiednie osoby mają stały dostęp. Regularne inspekcje administratorów są kluczowe ze względu na następujące przyczyny:
- Złośliwy aktor może naruszyć bezpieczeństwo konta.
- Ludzie przemieszczają zespoły w obrębie firmy. W przypadku braku inspekcji mogą oni amass niepotrzebny dostęp z upływem czasu.
 
Informacje o przeglądach dostępu dla ról można znaleźć [w temacie Tworzenie przeglądu dostępu do ról usługi Azure AD w usłudze PIM](../privileged-identity-management/pim-how-to-start-security-review.md). Informacje o przeglądach dostępu do grup, które są przypisane do ról, można znaleźć [w temacie Tworzenie przeglądu dostępu do grup i aplikacji w przeglądach dostępu usługi Azure AD](../governance/create-access-review.md).

## <a name="5-limit-the-number-of-global-administrators-to-less-than-5"></a>5. Ogranicz liczbę administratorów globalnych do mniej niż 5

Najlepszym rozwiązaniem jest, że firma Microsoft zaleca przypisanie roli administratora globalnego do **mniej niż pięciu** osób w organizacji. Administratorzy globalni przechowują klucze do Królestwa i są w najlepszym interesie, aby zapewnić niską powierzchnię ataku. Jak wspomniano wcześniej, wszystkie te konta należy chronić za pomocą uwierzytelniania wieloskładnikowego.

Domyślnie, gdy użytkownik loguje się do usługi w chmurze firmy Microsoft, zostaje utworzona dzierżawa usługi Azure AD, a użytkownik jest członkiem roli Administratorzy globalni. Użytkownicy, którym przypisano rolę administratora globalnego, mogą odczytywać i modyfikować każde ustawienie administracyjne w organizacji usługi Azure AD. Po kilku wyjątkach Administratorzy globalni mogą również odczytywać i modyfikować wszystkie ustawienia konfiguracji w organizacji Microsoft 365. Administratorzy globalni mogą również podnieść poziom dostępu do odczytu danych.

Firma Microsoft zaleca, aby zachować dwa konta ze szlifem, które są trwale przypisane do roli administratora globalnego. Upewnij się, że te konta nie wymagają tego samego mechanizmu usługi uwierzytelniania wieloskładnikowego co normalne konta administracyjne, aby się zalogować, zgodnie z opisem w temacie [Zarządzanie kontami dostępu awaryjnego w usłudze Azure AD](../roles/security-emergency-access.md). 

## <a name="6-use-groups-for-azure-ad-role-assignments-and-delegate-the-role-assignment"></a>6. Używanie grup do przypisywania ról usługi Azure AD i delegowanie przypisania roli

Jeśli masz zewnętrzny system ładu, który korzysta z grup, należy rozważyć Przypisanie ról do grup usługi Azure AD, a nie do poszczególnych użytkowników. Możesz również zarządzać grupami, które można przypisać do ról w usłudze PIM, aby upewnić się, że nie ma żadnych stałych właścicieli lub członków w tych uprzywilejowanych grupach. Aby uzyskać więcej informacji, zobacz [funkcje zarządzania dla uprzywilejowanego dostępu do grup usługi Azure AD](../privileged-identity-management/groups-features.md).

Można przypisać właściciela do grup przypisanych do ról. Ten właściciel decyduje, kto został dodany do grupy lub z niej usunięty, tak dalej, decyduje o tym, kto pobiera przypisanie roli. W ten sposób Administrator globalny lub administrator ról uprzywilejowanych może delegować Zarządzanie rolami dla poszczególnych ról przy użyciu grup. Aby uzyskać więcej informacji, zobacz [Używanie grup w chmurze do zarządzania przypisaniami ról w Azure Active Directory](groups-concept.md).

## <a name="7-activate-multiple-roles-at-once-using-privileged-access-groups"></a>7. Aktywuj jednocześnie wiele ról, używając uprzywilejowanych grup dostępu

W takim przypadku osoba może mieć pięć lub sześć kwalifikujących się przypisań do ról usługi Azure AD za pomocą programu PIM. Muszą oni osobno aktywować poszczególne role, co może obniżyć produktywność. W dalszym ciągu mogą być również dostępne dziesiątki lub setki zasobów platformy Azure, które pogłębią problem.
 
W takim przypadku należy użyć [uprzywilejowanych grup dostępu](../privileged-identity-management/groups-features.md). Utwórz uprzywilejowaną grupę dostępu i Udziel jej stałego dostępu do wielu ról (Azure AD i/lub Azure). Uczyń tego użytkownika uprawnionym członkiem lub właścicielem tej grupy. W przypadku tylko jednej aktywacji użytkownicy będą mieli dostęp do wszystkich połączonych zasobów.

![Diagram grupy uprzywilejowanego dostępu pokazujący aktywację wielu ról jednocześnie](./media/best-practices/privileged-access-group.png)

## <a name="8-use-cloud-native-accounts-for-azure-ad-roles"></a>8. Użyj natywnych kont w chmurze dla ról usługi Azure AD

Unikaj używania lokalnych kont synchronizowanych dla przypisań ról usługi Azure AD. W przypadku naruszenia zabezpieczeń konta lokalnego może dojść do naruszenia zasobów usługi Azure AD.

## <a name="next-steps"></a>Następne kroki

- [Zabezpieczanie uprzywilejowanego dostępu dla wdrożeń hybrydowych i wdrożeń w chmurze w usłudze Azure AD](security-planning.md)