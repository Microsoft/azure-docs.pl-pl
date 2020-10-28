---
title: Przypisywanie ról zasobów platformy Azure w Privileged Identity Management-Azure Active Directory | Microsoft Docs
description: Dowiedz się, jak przypisywać role zasobów platformy Azure w Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 10/23/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 011b50449f0a02b128bc000535f93e1809fd9777
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/27/2020
ms.locfileid: "92673885"
---
# <a name="assign-azure-resource-roles-in-privileged-identity-management"></a>Przypisywanie ról zasobów platformy Azure w Privileged Identity Management

Usługa Azure Active Directory (Azure AD) Privileged Identity Management (PIM) może zarządzać wbudowanymi rolami zasobów platformy Azure, a także rolami niestandardowymi, w tym (ale nie ograniczonymi do):

- Właściciel
- Administrator dostępu użytkowników
- Współautor
- Administrator zabezpieczeń
- Menedżer zabezpieczeń

> [!NOTE]
> Użytkownicy lub członkowie grupy przypisani do ról subskrypcji administrator dostępu do właściciela lub użytkownika oraz Administratorzy globalni usługi Azure AD, którzy umożliwiają zarządzanie subskrypcjami w usłudze Azure AD, domyślnie mają uprawnienia administratora zasobów. Ci Administratorzy mogą przypisywać role, konfigurować ustawienia ról i przeglądać dostęp za pomocą Privileged Identity Management dla zasobów platformy Azure. Użytkownik nie może zarządzać Privileged Identity Management dla zasobów bez uprawnień administratora zasobów. Wyświetl listę [wbudowanych ról platformy Azure](../../role-based-access-control/built-in-roles.md).

## <a name="assign-a-role"></a>Przypisywanie roli

Wykonaj następujące kroki, aby użytkownik mógł kwalifikować się do roli zasobów platformy Azure.

1. Zaloguj się do [Azure Portal](https://portal.azure.com/) przy użyciu uprawnień roli administrator dostępu użytkownika lub właściciela.

    Aby uzyskać informacje na temat udzielania innym administratorom dostępu do zarządzania Privileged Identity Management, zobacz [udzielanie dostępu innym administratorom w celu zarządzania Privileged Identity Management](pim-how-to-give-access-to-pim.md).

1. Otwórz **Azure AD Privileged Identity Management** .

1. Wybierz pozycję **zasoby platformy Azure** .

1. Użyj filtru zasobów, aby znaleźć zarządzane zasoby, których szukasz.

    ![Lista zasobów platformy Azure do zarządzania](./media/pim-resource-roles-assign-roles/resources-list.png)

1. Wybierz zasób, którym chcesz zarządzać, aby otworzyć stronę przegląd zasobów.

1. W obszarze **Zarządzanie** wybierz pozycję **role** , aby wyświetlić listę ról dla zasobów platformy Azure.

    ![Role zasobów platformy Azure](./media/pim-resource-roles-assign-roles/resources-roles.png)

1. Wybierz pozycję **Dodaj przypisania** , aby otworzyć okienko **Dodawanie przypisań** .

1. Wybierz pozycję **Wybierz rolę** , aby otworzyć stronę **Wybierz rolę** .

    ![Nowe okienko przypisania](./media/pim-resource-roles-assign-roles/resources-select-role.png)

1. Wybierz rolę, którą chcesz przypisać, a następnie kliknij przycisk **Wybierz** .

    Zostanie otwarte okienko **Wybierz członka lub grupę** .

1. Wybierz członka lub grupę, która ma zostać przypisana do roli, a następnie kliknij przycisk **Wybierz** .

    ![Wybierz okienko elementu członkowskiego lub grupy](./media/pim-resource-roles-assign-roles/resources-select-member-or-group.png)

1. Na karcie **Ustawienia** na liście **Typ przypisania** wybierz pozycję **kwalifikujące się** lub **aktywne** .

    ![Okienko ustawień członkostw](./media/pim-resource-roles-assign-roles/resources-membership-settings-type.png)

    Privileged Identity Management dla zasobów platformy Azure oferuje dwa różne typy przypisania:

    - **Kwalifikujące się** przypisania wymagają, aby członek roli wykonywał akcję do korzystania z roli. Akcje te mogą obejmować przeprowadzenie uwierzytelniania wieloskładnikowego (MFA), podanie uzasadnienia biznesowego lub żądanie zatwierdzenia od wyznaczonych osób zatwierdzających.

    - **Aktywne** przypisania nie wymagają, aby członkowie mogli wykonać żadną akcję w celu korzystania z roli. Członkowie przypisani jako aktywni mają uprawnienia przypisane do roli przez cały czas.

1. Aby określić określony czas trwania przypisania, Zmień datę i godzinę rozpoczęcia i zakończenia.

1. Po zakończeniu wybierz pozycję **Przypisz** .

1. Po utworzeniu nowego przypisania roli zostanie wyświetlone powiadomienie o stanie.

    ![Nowe przypisanie — powiadomienie](./media/pim-resource-roles-assign-roles/resources-new-assignment-notification.png)

## <a name="update-or-remove-an-existing-role-assignment"></a>Aktualizowanie lub usuwanie istniejącego przypisania roli

Wykonaj następujące kroki, aby zaktualizować lub usunąć istniejące przypisanie roli.

1. Otwórz **Azure AD Privileged Identity Management** .

1. Wybierz pozycję **zasoby platformy Azure** .

1. Wybierz zasób, którym chcesz zarządzać, aby otworzyć jego stronę przeglądu.

1. W obszarze **Zarządzanie** wybierz pozycję **role** , aby wyświetlić listę ról dla zasobów platformy Azure.

    ![Role zasobów platformy Azure — Wybieranie roli](./media/pim-resource-roles-assign-roles/resources-update-select-role.png)

1. Wybierz rolę, którą chcesz zaktualizować lub usunąć.

1. Znajdź przypisanie roli na kartach **kwalifikujące się role** lub **role aktywne** .

    ![Aktualizowanie lub usuwanie przypisania roli](./media/pim-resource-roles-assign-roles/resources-update-remove.png)

1. Wybierz pozycję **Aktualizuj** lub **Usuń** , aby zaktualizować lub usunąć przypisanie roli.

    Informacje o rozszerzaniu przypisania roli znajdują się [w temacie rozszerzanie lub odnawianie ról zasobów platformy Azure w Privileged Identity Management](pim-resource-roles-renew-extend.md).

## <a name="next-steps"></a>Następne kroki

- [Przedłuż lub Odnów role zasobów platformy Azure w Privileged Identity Management](pim-resource-roles-renew-extend.md)
- [Skonfiguruj ustawienia roli zasobów platformy Azure w Privileged Identity Management](pim-resource-roles-configure-role-settings.md)
- [Przypisywanie ról usługi Azure AD w Privileged Identity Management](pim-how-to-add-role-to-user.md)
