---
title: Zarządzanie dostępem użytkowników zewnętrznych w usłudze Azure AD uprawnień zarządzania — Azure Active Directory
description: Informacje o ustawieniach, które można określić, aby zarządzać dostępem użytkowników zewnętrznych w Azure Active Directory zarządzania prawami.
services: active-directory
documentationCenter: ''
author: barclayn
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 06/18/2020
ms.author: barclayn
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9ff683c7c3214be6ae60b5d00d4cd1c2becc32e2
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/29/2020
ms.locfileid: "91447078"
---
# <a name="govern-access-for-external-users-in-azure-ad-entitlement-management"></a>Zarządzanie dostępem użytkowników zewnętrznych w usłudze Azure AD upoważnienia

Zarządzanie prawami w usłudze Azure AD wykorzystuje [usługę Azure AD Business-to-Business (B2B)](../external-identities/what-is-b2b.md) do współpracy z osobami spoza organizacji w innym katalogu. Przy użyciu usługi Azure AD B2B użytkownicy zewnętrzni są uwierzytelniani w katalogu macierzystym, ale mają reprezentację w katalogu. Reprezentacja w katalogu umożliwia użytkownikowi przypisanie dostępu do zasobów.

W tym artykule opisano ustawienia, które można określić, aby zarządzać dostępem użytkowników zewnętrznych.

## <a name="how-entitlement-management-can-help"></a>Jak zarządzanie uprawnieniami może pomóc

W przypadku korzystania z funkcji zapraszania [B2B usługi Azure AD](../external-identities/what-is-b2b.md) musisz znać adresy e-mail użytkowników zewnętrznych Gości, którzy chcą umieścić w katalogu zasobów i pracy z programem. Jest to doskonałe rozwiązanie, gdy pracujesz nad mniejszym lub krótkoterminowym projektem i znasz już wszystkich uczestników, ale jest to trudniejsze do zarządzania, jeśli masz wielu użytkowników, z którymi chcesz pracować, lub jeśli uczestnicy zmieniają się z upływem czasu.  Na przykład użytkownik może pracować z inną organizacją i mieć jeden punkt kontaktu z tą organizacją, ale w miarę upływu czasu dodatkowi użytkownicy z tej organizacji będą musieli również uzyskać dostęp.

Za pomocą zarządzania uprawnieniami można zdefiniować zasady, które umożliwią użytkownikom z określonych organizacji określenie, że możliwość samodzielnego żądania pakietu dostępu. Można określić, czy wymagane jest zatwierdzenie oraz datę wygaśnięcia dostępu. Jeśli jest wymagane zatwierdzenie, można także zaprosić jednego lub kilku użytkowników z organizacji zewnętrznej do katalogu i wyznaczyć ich jako osoby zatwierdzające, ponieważ mogą oni wiedzieć, którzy użytkownicy zewnętrzni z organizacji potrzebują dostępu. Po skonfigurowaniu pakietu dostępu można wysłać link do osoby kontaktowej (sponsora) w organizacji zewnętrznej. Ten kontakt może współdzielić z innymi użytkownikami w organizacji zewnętrznej i może użyć tego linku, aby zażądać pakietu dostępu. Użytkownicy z tej organizacji, którzy zostali już zaproszeni do katalogu, mogą również korzystać z tego linku.

Gdy żądanie zostanie zatwierdzone, zarządzanie prawami zapewni użytkownikowi dostęp do niezbędnego dostępu, który może obejmować zaproszenie użytkownika, jeśli nie znajdują się jeszcze w Twoim katalogu. Usługa Azure AD automatycznie utworzy dla nich konto gościa B2B. Należy pamiętać, że administrator może wcześniej ograniczyć, które organizacje są dozwolone do współpracy, ustawiając [listę dozwolonych lub](../external-identities/allow-deny-list.md) zablokowanych B2B, aby zezwalać na zaproszenia do innych organizacji lub je blokować.  Jeśli użytkownik nie jest dozwolony przez listę dozwolonych lub zablokowanych, nie będzie zapraszany.

Ponieważ nie chcesz, aby użytkownik zewnętrzny miał dostęp do ostatniej nieograniczonej, należy określić datę wygaśnięcia zasad, na przykład 180 dni. Po 180 dniach, jeśli ich dostęp nie zostanie rozszerzony, zarządzanie uprawnieniami spowoduje usunięcie wszystkich dostępu skojarzonych z tym pakietem dostępu. Domyślnie, jeśli użytkownik, który został zaproszony za pośrednictwem usługi zarządzania prawami, nie ma żadnych innych przypisań pakietów dostępu, wówczas po utracie ostatniego przypisania ich konto gościa nie zostanie zalogowany przez 30 dni, a następnie usunięte. Zapobiega to rozmnożenia niepotrzebnych kont. Jak opisano w poniższych sekcjach, te ustawienia można konfigurować.

## <a name="how-access-works-for-external-users"></a>Jak działa dostęp dla użytkowników zewnętrznych

Poniższy diagram i kroki zawierają omówienie sposobu udzielania dostępu użytkownikom zewnętrznym do pakietu dostępu.

![Diagram przedstawiający cykl życia użytkowników zewnętrznych](./media/entitlement-management-external-users/external-users-lifecycle.png)

1. Należy [dodać połączoną organizację](entitlement-management-organization.md) dla katalogu lub domeny usługi Azure AD, z którą chcesz współpracować.

1. W katalogu utworzysz pakiet dostępu zawierający zasady dla użytkowników, które nie znajdują [się w katalogu](entitlement-management-access-package-create.md#for-users-not-in-your-directory).

1. Wysyłasz [link do mojego portalu dostępu](entitlement-management-access-package-settings.md) do kontaktu w organizacji zewnętrznej, który mogą oni udostępniać użytkownikom w celu żądania pakietu dostępu.

1. Użytkownik zewnętrzny (**osoba żądająca A** w tym przykładzie) używa linku Portal dostępu w celu [zażądania dostępu](entitlement-management-request-access.md) do pakietu dostępu. Sposób logowania użytkownika zależy od typu uwierzytelniania katalogu lub domeny zdefiniowanej w połączonej organizacji.

1. Osoba zatwierdzająca [zatwierdza żądanie](entitlement-management-request-approve.md) (lub żądanie jest zatwierdzane z autozatwierdzeniem).

1. Żądanie przechodzi do [stanu dostarczania](entitlement-management-process.md).

1. Przy użyciu procesu zapraszania B2B konto użytkownika-gościa jest tworzone w Twoim katalogu (w tym przykładzie jest to**osoba żądająca a)** . Jeśli zdefiniowano [listę dozwolonych lub Odmów](../external-identities/allow-deny-list.md) , zostanie zastosowane ustawienie listy.

1. Użytkownik-Gość ma przypisany dostęp do wszystkich zasobów w pakiecie dostępu. Wprowadzenie zmian w usłudze Azure AD i innych usługach online firmy Microsoft lub połączonych aplikacjach SaaS może zająć trochę czasu. Aby uzyskać więcej informacji, zobacz [kiedy zmiany są stosowane](entitlement-management-access-package-resources.md#when-changes-are-applied).

1. Użytkownik zewnętrzny otrzymuje wiadomość e-mail informującą o tym, że dostęp został [dostarczony](entitlement-management-process.md).

1. Aby uzyskać dostęp do zasobów, użytkownik zewnętrzny może kliknąć link w wiadomości e-mail lub próbować uzyskać dostęp do dowolnego zasobu katalogu bezpośrednio w celu ukończenia procesu zaproszenia.

1. W zależności od ustawień zasad w miarę upływu czasu, przypisanie pakietu dostępu dla użytkownika zewnętrznego wygasa i zostaje usunięty dostęp użytkownika zewnętrznego.

1. W zależności od cyklu życia ustawień użytkowników zewnętrznych, gdy użytkownik zewnętrzny nie ma już żadnych przypisań pakietów dostępu, użytkownik zewnętrzny nie może się zalogować, a konto użytkownika-Gości zostanie usunięte z katalogu.

## <a name="settings-for-external-users"></a>Ustawienia dla użytkowników zewnętrznych

Aby zapewnić, że osoby spoza organizacji mogą żądać dostępu do pakietów i uzyskać dostęp do zasobów w tych pakietach dostępu, należy sprawdzić, czy zostały prawidłowo skonfigurowane.

### <a name="enable-catalog-for-external-users"></a>Włączanie wykazu dla użytkowników zewnętrznych

- Domyślnie podczas tworzenia [nowego wykazu](entitlement-management-catalog-create.md)jest włączony Zezwalanie użytkownikom zewnętrznym na żądania dostępu do pakietów w wykazie. Upewnij się, że **dla użytkowników zewnętrznych włączono** opcję **tak**.

    ![Edytuj ustawienia katalogu](./media/entitlement-management-shared/catalog-edit.png)

### <a name="configure-your-azure-ad-b2b-external-collaboration-settings"></a>Konfigurowanie ustawień współpracy zewnętrznej usługi Azure AD B2B

- Zezwolenie Gościom na Zapraszanie innych Gości do katalogu oznacza, że zaproszenia gościa mogą wystąpić poza zarządzaniem prawami. Zalecamy ustawienie **Gości** w taki sposób, aby **nie** zezwalać na prawidłowe wykonywanie zaproszeń.
- W przypadku korzystania z listy dozwolonych aplikacji B2B musisz upewnić się, że wszystkie domeny, z którymi chcesz się połączyć za pomocą usługi zarządzania prawami, zostaną dodane do listy. Alternatywnie, jeśli używasz listy Odmów B2B, musisz upewnić się, że wszystkie domeny, z którymi chcesz się połączyć, nie zostaną dodane do listy.
- Jeśli utworzysz zasady zarządzania uprawnieniami dla **wszystkich użytkowników** (wszystkie połączone organizacje + wszyscy użytkownicy zewnętrzni), a użytkownik nie należy do połączonej organizacji w katalogu, połączona organizacja zostanie automatycznie utworzona dla nich po zażądaniu pakietu. Pierwszeństwo mają wszystkie ustawienia listy dozwolonych lub zablokowanych B2B. W związku z tym Pamiętaj o uwzględnieniu domen, które mają zostać uwzględnione w tych zasadach, na liście dozwolonych, jeśli są używane, i wykluczyć je z listy Odmów, jeśli używasz listy Odmów.
- Jeśli chcesz utworzyć zasady zarządzania uprawnieniami, które obejmują **wszystkich użytkowników** (wszystkie połączone organizacje + wszyscy wszyscy użytkownicy zewnętrzni), musisz najpierw włączyć uwierzytelnianie za pośrednictwem poczty e-mail jednorazowo dla katalogu. Aby uzyskać więcej informacji, zobacz [jednorazowe uwierzytelnianie kodu dostępu za pośrednictwem poczty e-mail (wersja zapoznawcza)](../external-identities/one-time-passcode.md#opting-in-to-the-preview).
- Aby uzyskać więcej informacji na temat zewnętrznych ustawień współpracy B2B usługi Azure AD, zobacz [Włączanie współpracy zewnętrznej B2B i zarządzanie osobami, które mogą zapraszać Gości](../external-identities/delegate-invitations.md).

    ![Ustawienia współpracy zewnętrznej usługi Azure AD](./media/entitlement-management-external-users/collaboration-settings.png)

### <a name="review-your-conditional-access-policies"></a>Przeglądanie zasad dostępu warunkowego

- Upewnij się, że nie zostaną wykluczone Goście z zasad dostępu warunkowego, których nowi użytkownicy-Goście nie będą mogli obsłużyć, ponieważ spowoduje to zablokowanie możliwości zalogowania się do katalogu. Na przykład Goście prawdopodobnie nie mają zarejestrowanego urządzenia, nie znajdują się w znanej lokalizacji i nie chcą ponownie rejestrować się w celu uwierzytelniania wieloskładnikowego (MFA), więc dodanie tych wymagań w zasadach dostępu warunkowego uniemożliwi Gościom korzystanie z usługi zarządzania prawami. Aby uzyskać więcej informacji, zobacz [co to są warunki w Azure Active Directory dostęp warunkowy?](../conditional-access/concept-conditional-access-conditions.md).

    ![Ustawienia wykluczania zasad dostępu warunkowego usługi Azure AD](./media/entitlement-management-external-users/conditional-access-exclude.png)

### <a name="review-your-sharepoint-online-external-sharing-settings"></a>Przejrzyj ustawienia udostępniania zewnętrznego usługi SharePoint Online

- Jeśli chcesz dołączyć witryny usługi SharePoint Online do pakietów dostępu dla użytkowników zewnętrznych, upewnij się, że ustawienie udostępniania zewnętrznego na poziomie organizacji jest ustawione na wartość **Wszyscy** (użytkownicy nie wymagają logowania) lub **nowi i istniejący Goście** (Goście muszą się zalogować lub podać kod weryfikacyjny). Aby uzyskać więcej informacji, zobacz [Włączanie lub wyłączanie udostępniania zewnętrznego](/sharepoint/turn-external-sharing-on-or-off#change-the-organization-level-external-sharing-setting).

- Jeśli chcesz ograniczyć udostępnianie zewnętrzne poza zarządzaniem prawami, możesz ustawić ustawienie Udostępnianie zewnętrzne dla **istniejących Gości**. Następnie tylko nowi użytkownicy zapraszani za poorednictwem zarządzania uprawnieniami będą mogli uzyskać dostęp do tych witryn. Aby uzyskać więcej informacji, zobacz [Włączanie lub wyłączanie udostępniania zewnętrznego](/sharepoint/turn-external-sharing-on-or-off#change-the-organization-level-external-sharing-setting).

- Upewnij się, że ustawienia na poziomie witryny umożliwiają dostęp gościa (takie same opcje jak wcześniej wymienione). Aby uzyskać więcej informacji, zobacz [Włączanie lub wyłączanie udostępniania zewnętrznego dla witryny](/sharepoint/change-external-sharing-site).

### <a name="review-your-microsoft-365-group-sharing-settings"></a>Przejrzyj ustawienia udostępniania grupy Microsoft 365

- Jeśli chcesz uwzględnić grupy Microsoft 365 w pakietach dostępu dla użytkowników zewnętrznych, upewnij się, że dla opcji Zezwalaj **użytkownikom na dodawanie nowych Gości do organizacji** jest ustawiona wartość **Zezwalaj na dostęp** gościa. Aby uzyskać więcej informacji, zobacz [Zarządzanie dostępem gościa do grup Microsoft 365](/Microsoft 365/admin/create-groups/manage-guest-access-in-groups?view=Microsoft 365-worldwide#manage-groups-guest-access).

- Jeśli chcesz, aby użytkownicy zewnętrzni mieli dostęp do witryny usługi SharePoint Online i zasobów skojarzonych z grupą Microsoft 365, upewnij się, że włączasz udostępnianie zewnętrzne w usłudze SharePoint Online. Aby uzyskać więcej informacji, zobacz [Włączanie lub wyłączanie udostępniania zewnętrznego](/sharepoint/turn-external-sharing-on-or-off#change-the-organization-level-external-sharing-setting).

- Aby uzyskać informacje na temat sposobu ustawiania zasad gościa dla grup Microsoft 365 na poziomie katalogu w programie PowerShell, zobacz [przykład: Konfigurowanie zasad gościa dla grup na poziomie katalogu](../users-groups-roles/groups-settings-cmdlets.md#example-configure-guest-policy-for-groups-at-the-directory-level).

### <a name="review-your-teams-sharing-settings"></a>Przejrzyj ustawienia udostępniania zespołów

- Jeśli chcesz dołączyć zespoły do pakietów dostępu dla użytkowników zewnętrznych, upewnij się, że ustawienie **Zezwalaj na dostęp gościa w usłudze Microsoft Teams** jest ustawione na wartość **Zezwalaj na dostęp** gościa. Aby uzyskać więcej informacji, zobacz [Konfigurowanie dostępu gościa w centrum administracyjnym Microsoft Teams](/microsoftteams/set-up-guests#configure-guest-access-in-the-teams-admin-center).

## <a name="manage-the-lifecycle-of-external-users"></a>Zarządzanie cyklem życia użytkowników zewnętrznych

Możesz wybrać, co się stanie, gdy użytkownik zewnętrzny, który został zaproszony do katalogu przy użyciu zatwierdzonego żądania pakietu dostępu, nie ma już żadnych przypisań pakietów dostępu. Taka sytuacja może wystąpić, jeśli użytkownik utraci wszystkie przypisania pakietu dostępu lub ich ostatnie przypisanie pakietu dostępu wygaśnie. Domyślnie, gdy użytkownik zewnętrzny nie ma już żadnych przypisań pakietów dostępu, nie może się zalogować do katalogu. Po 30 dniach konto użytkownika-Gość zostanie usunięte z katalogu.

**Rola wymagana wstępnie:** Administrator globalny lub administrator użytkowników

1. W Azure Portal kliknij pozycję **Azure Active Directory** , a następnie kliknij pozycję **Zarządzanie tożsamościami**.

1. W menu po lewej stronie w sekcji **Zarządzanie prawami** kliknij pozycję **Ustawienia**.

1. Kliknij pozycję **Edytuj**.

    ![Ustawienia do zarządzania cyklem życia użytkowników zewnętrznych](./media/entitlement-management-external-users/settings-external-users.png)

1. W sekcji **Zarządzaj cyklem życia użytkowników zewnętrznych** wybierz różne ustawienia dla użytkowników zewnętrznych.

1. Gdy użytkownik zewnętrzny utraci ostatnie przypisanie do dowolnych pakietów dostępu, jeśli chcesz zablokować użytkownikom możliwość logowania się do tego katalogu, ustaw opcję **Zablokuj użytkownikom zewnętrznym na logowanie do tego katalogu** na **wartość tak**.

    > [!NOTE]
    > Jeśli użytkownik nie może się zalogować do tego katalogu, użytkownik nie będzie mógł ponownie zażądać pakietu dostępu lub poprosić o dodatkowy dostęp w tym katalogu. Nie należy konfigurować blokowania logowania, jeśli będą później musieli zażądać dostępu do innych pakietów dostępu.

1. Gdy użytkownik zewnętrzny utraci swoje ostatnie przypisanie do wszystkich pakietów dostępu, jeśli chcesz usunąć swoje konto użytkownika-gościa w tym katalogu, ustaw opcję **Usuń użytkownika zewnętrznego** na **wartość tak**.

    > [!NOTE]
    > Zarządzanie prawami powoduje usunięcie tylko tych kont, które zostały zaproszone przez Zarządzanie uprawnieniami. Należy również pamiętać, że użytkownik zostanie zablokowany przed zalogowaniem się i usunięciem z tego katalogu, nawet jeśli ten użytkownik został dodany do zasobów w tym katalogu, które nie mają dostępu do przypisań pakietów. Jeśli gość znajdował się w tym katalogu przed odebraniem przypisań pakietów dostępu, pozostanie. Jeśli jednak gość został zaproszony przez przypisanie pakietu dostępu, a po jego zaproszeniu również zostanie przypisany do witryny usługi OneDrive dla firm lub SharePoint Online, zostaną one usunięte.

1. Jeśli chcesz usunąć konto użytkownika-gościa w tym katalogu, możesz ustawić liczbę dni, po której ma zostać usunięta. Jeśli chcesz usunąć konto użytkownika-gościa, gdy tylko utracisz swoje ostatnie przypisanie do wszystkich pakietów dostępu, ustaw **liczbę dni przed usunięciem użytkownika zewnętrznego z tego katalogu** do **0**.

1. Kliknij pozycję **Zapisz**.

## <a name="next-steps"></a>Następne kroki

- [Dodawanie połączonej organizacji](entitlement-management-organization.md)
- [Dla użytkowników nieznajdujących się w katalogu](entitlement-management-access-package-request-policy.md#for-users-not-in-your-directory)
- [Rozwiązywanie problemów](entitlement-management-troubleshoot.md)