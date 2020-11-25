---
title: Przypisywanie ról zasobów platformy Azure do Gości w usłudze PIM — Azure AD | Microsoft Docs
description: Dowiedz się, jak zapraszać zewnętrznych użytkowników-Gości i przypisywać role zasobów platformy Azure w Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: f4e2e18f3bb9d1c972d805a60493897d605921e4
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96010643"
---
# <a name="invite-guest-users-and-assign-azure-resource-roles-in-privileged-identity-management"></a>Zapraszanie użytkowników-Gości i przypisywanie ról zasobów platformy Azure w Privileged Identity Management

Użytkownicy-Goście usługi Azure Active Directory (Azure AD) są częścią funkcji współpracy między firmami (B2B, Business-to-Business) w ramach usługi Azure AD, dzięki czemu możesz zarządzać zewnętrznymi użytkownikami i dostawcami Gości jako Gości w usłudze Azure AD. W przypadku łączenia współpracy B2B z usługą Azure AD Privileged Identity Management (PIM) można zwiększyć wymagania dotyczące zgodności i zarządzania dla Gości. Można na przykład użyć tych funkcji Privileged Identity Management dla zadań związanych z tożsamością platformy Azure z Gośćmi:

- Przypisywanie dostępu do określonych zasobów platformy Azure
- Włącz dostęp just in Time
- Określ czas trwania przypisania i datę zakończenia
- Wymagaj uwierzytelniania wieloskładnikowego w aktywnym przypisaniu lub aktywacji
- Wykonaj przeglądy dostępu
- Korzystanie z alertów i dzienników inspekcji

W tym artykule opisano, jak zapraszać gościa do organizacji i zarządzać dostępem do zasobów platformy Azure przy użyciu Privileged Identity Management.

## <a name="when-would-you-invite-guests"></a>Kiedy zapraszasz Gości?

Poniżej przedstawiono kilka przykładów, które mogą zapraszać Gości do organizacji:

- Zezwól zewnętrznemu dostawcy samoobsługowego, który ma dostęp do zasobów platformy Azure dla projektu i ma tylko konto e-mail.
- Zezwól partnerowi zewnętrznemu w dużej organizacji korzystającej z Active Directory Federation Services lokalnych w celu uzyskania dostępu do aplikacji do wydatków.
- Zezwól inżynierom pomocy technicznej, które nie są w organizacji (np. pomocy technicznej firmy Microsoft), aby tymczasowo uzyskiwać dostęp do zasobów platformy Azure w celu rozwiązywania problemów.

## <a name="how-does-collaboration-using-b2b-guests-work"></a>Jak działa współpraca przy użyciu Gości B2B?

Gdy używasz współpracy B2B, możesz zaprosić użytkownika zewnętrznego do organizacji jako gościa. Gość może być zarządzany jako użytkownik w organizacji, ale gość musi być uwierzytelniony w swojej organizacji domowej, a nie w organizacji usługi Azure AD. Oznacza to, że jeśli gość nie ma już dostępu do swojej organizacji domowej, utraci również dostęp do swojej organizacji. Na przykład jeśli gość opuści organizację, automatycznie utraci dostęp do wszystkich zasobów, które zostały Ci udostępnione w usłudze Azure AD bez konieczności wykonywania żadnych czynności. Aby uzyskać więcej informacji na temat współpracy B2B, zobacz temat [co to jest dostęp gościa w Azure Active Directory B2B?](../external-identities/what-is-b2b.md).

![Diagram przedstawiający sposób uwierzytelniania użytkownika-gościa w katalogu macierzystym](./media/pim-resource-roles-external-users/b2b-external-user.png)

## <a name="check-guest-collaboration-settings"></a>Sprawdź ustawienia współpracy gościa

Aby upewnić się, że możesz zapraszać Gości do organizacji, sprawdź ustawienia współpracy gościa.

1. Zaloguj się do [Azure Portal](https://portal.azure.com/).

1. Wybierz pozycję **Azure Active Directory**  >  **Ustawienia użytkownika**.

1. Wybierz pozycję **Zarządzaj ustawieniami współpracy zewnętrznej**.

    ![Strona ustawień współpracy zewnętrznej przedstawiające ustawienia ograniczeń uprawnień, zaproszeń i współpracy](./media/pim-resource-roles-external-users/external-collaboration-settings.png)

1. Upewnij się, że **Administratorzy i użytkownicy w roli osoby zapraszające gościa mogą zapraszać** przełącznik ma ustawioną **wartość tak**.

## <a name="invite-a-guest-and-assign-a-role"></a>Zapraszanie gościa i przypisywanie roli

Korzystając z Privileged Identity Management, możesz zaprosić gościa i uczynić go uprawnionym do roli zasobów platformy Azure.

1. Zaloguj się do [Azure Portal](https://portal.azure.com/) przy użyciu użytkownika, który jest członkiem roli [administrator ról uprzywilejowanych](../roles/permissions-reference.md#privileged-role-administrator) lub [administratora](../roles/permissions-reference.md#user-administrator) .

1. Otwórz **Azure AD Privileged Identity Management**.

1. Wybierz pozycję **zasoby platformy Azure**.

1. Użyj **filtru zasobów** , aby odfiltrować listę zarządzanych zasobów.

1. Wybierz zasób, którym chcesz zarządzać, na przykład zasób, grupę zasobów, subskrypcję lub grupę zarządzania.

    Należy ustawić zakres tylko na potrzeby gościa.

1. W obszarze Zarządzanie wybierz pozycję **role** , aby wyświetlić listę ról dla zasobów platformy Azure.

    ![Lista ról zasobów platformy Azure zawierająca liczbę aktywnych użytkowników i kwalifikujących się](./media/pim-resource-roles-external-users/resources-roles.png)

1. Wybierz minimalną rolę wymaganą przez użytkownika.

    ![Strona wybranej roli z listą bieżących członków tej roli](./media/pim-resource-roles-external-users/selected-role.png)

1. Na stronie Rola wybierz pozycję **Dodaj członka** , aby otworzyć okienko nowe przypisanie.

1. Kliknij pozycję **Wybierz członka lub grupę**.

    ![Nowe przypisanie — Wybierz okienko elementu członkowskiego lub grupy z listą użytkowników i grup wraz z opcją zaproszenia](./media/pim-resource-roles-external-users/select-member-group.png)

1. Aby zaprosić gościa, kliknij pozycję **Zaproś**.

    ![Zapraszanie strony Gościa z polami do wprowadzania adresu e-mail i określania wiadomości osobistej](./media/pim-resource-roles-external-users/invite-guest.png)

1. Po wybraniu gościa kliknij pozycję **Zaproś**.

    Gość powinien zostać dodany jako wybrany element członkowski.

1. W okienku **Wybierz członka lub grupę** kliknij pozycję **Wybierz**.

1. W okienku **Ustawienia członkostwa** wybierz typ przypisania i czas trwania.

    ![Strona Ustawienia nowej przynależności do przypisania z opcjami określającymi typ przypisania, datę początkową i datę końcową](./media/pim-resource-roles-external-users/membership-settings.png)

1. Aby zakończyć przypisanie, wybierz opcję **gotowe** , a następnie **Dodaj**.

    Przypisanie roli gościa zostanie wyświetlone na liście ról.

    ![Strona roli](./media/pim-resource-roles-external-users/role-assignment.png)

## <a name="activate-role-as-a-guest"></a>Aktywuj rolę jako gościa

Jeśli jesteś użytkownikiem zewnętrznym, musisz zaakceptować zaproszenie jako gość w organizacji usługi Azure AD i prawdopodobnie aktywować przypisanie roli.

1. Otwórz wiadomość e-mail z zaproszeniem. Wiadomość e-mail będzie wyglądać podobnie do poniższego.

    ![Wyślij wiadomość e-mail z zaproszeniem z nazwą katalogu, wiadomością osobistą i linkiem wprowadzającym](./media/pim-resource-roles-external-users/email-invite.png)

1. Wybierz link **wprowadzenie** w wiadomości e-mail.

1. Po przejrzeniu uprawnień kliknij przycisk **Akceptuj**.

    ![Przeglądanie strony uprawnień w przeglądarce z listą uprawnień, które organizacja chce przejrzeć](./media/pim-resource-roles-external-users/invite-accept.png)

1. Może zostać wyświetlony monit o zaakceptowanie warunków użytkowania i określenie, czy użytkownik ma pozostać zalogowany. W Azure Portal, jeśli *kwalifikujesz się* do roli, nie masz jeszcze dostępu do zasobów.

1. Aby aktywować przypisanie roli, Otwórz wiadomość e-mail przy użyciu linku Aktywuj rolę. Wiadomość e-mail będzie wyglądać podobnie do poniższego.

    ![Adres e-mail wskazujący, że kwalifikujesz się do roli przy użyciu linku Aktywuj rolę](./media/pim-resource-roles-external-users/email-role-assignment.png)

1. Wybierz pozycję **Aktywuj rolę** , aby otworzyć odpowiednie role w Privileged Identity Management.

    ![Strona Moje role w Privileged Identity Management wystawiania kwalifikujących się ról](./media/pim-resource-roles-external-users/my-roles-eligible.png)

1. W obszarze Akcja wybierz łącze **Aktywuj** .

    W zależności od ustawień roli należy określić kilka informacji w celu aktywowania roli.

1. Po określeniu ustawień dla roli kliknij pozycję **Aktywuj** , aby aktywować rolę.

    ![Aktywuj zakres i opcje wyświetlania strony, aby określić czas rozpoczęcia, czas trwania i powód](./media/pim-resource-roles-external-users/activate-role.png)

    Jeśli administrator nie musi zatwierdzić Twojego żądania, powinien mieć dostęp do określonych zasobów.

## <a name="view-activity-for-a-guest"></a>Wyświetl działania gościa

Możesz wyświetlić dzienniki inspekcji, aby śledzić, co robią Goście.

1. Jako administrator Otwórz Privileged Identity Management i wybierz zasób, który został udostępniony.

1. Wybierz pozycję **Inspekcja zasobów** , aby wyświetlić działanie tego zasobu. Poniżej przedstawiono przykład działania dotyczącego grupy zasobów.

    ![Zasoby platformy Azure — na stronie Inspekcja zasobów na liście czas, Obiekt żądający i akcja](./media/pim-resource-roles-external-users/audit-resource.png)

1. Aby wyświetlić działanie gościa, wybierz pozycję **Azure Active Directory**  >  **Użytkownicy**  >  *Nazwa gość*.

1. Wybierz pozycję **dzienniki inspekcji** , aby wyświetlić dzienniki inspekcji dla organizacji. W razie potrzeby można określić filtry.

    ![Dzienniki inspekcji katalogów wymieniają datę, cel, zainicjowane przez i działanie](./media/pim-resource-roles-external-users/audit-directory.png)

## <a name="next-steps"></a>Następne kroki

- [Przypisywanie ról administratorów usługi Azure AD w Privileged Identity Management](pim-how-to-add-role-to-user.md)
- [Co to jest dostęp gościa w usłudze Azure AD B2B współpraca?](../external-identities/what-is-b2b.md)