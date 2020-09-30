---
title: Przypisywanie uprawnionych właścicieli i członków do uprzywilejowanych grup dostępu — Azure Active Directory
description: Dowiedz się, jak przypisywać uprawnionym właścicielom lub członkom grupy przypisanej do roli w Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 08/18/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 48c4473cfafce1215219251c47bce1d5730645fc
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/29/2020
ms.locfileid: "91534424"
---
# <a name="assign-eligibility-for-a-privileged-access-group-preview-in-privileged-identity-management"></a>Przypisywanie uprawnień do uprzywilejowanej grupy dostępu (wersja zapoznawcza) w Privileged Identity Management

Usługa Azure Active Directory (Azure AD) Privileged Identity Management (PIM) może pomóc w zarządzaniu przypisaniami do uprzywilejowanych grup dostępu i ich aktywacji w usłudze Azure AD. Można przypisać uprawnienie do członków lub właścicieli grupy.

>[!NOTE]
>Każdy użytkownik, który kwalifikuje się do członkostwa w grupie dostępu uprzywilejowanego lub musi mieć licencję na Azure AD — wersja Premium P2. Aby uzyskać więcej informacji, zobacz [wymagania licencyjne do używania Privileged Identity Management](subscription-requirements.md).

## <a name="assign-an-owner-or-member-of-a-group"></a>Przypisywanie właściciela lub składowej grupy

Wykonaj następujące kroki, aby użytkownik mógł być członkiem lub właścicielem grupy uprzywilejowanych dostępu.

1. [Zaloguj się do usługi Azure AD](https://aad.portal.azure.com) za pomocą uprawnień administratora globalnego lub właściciela grupy.
1. Wybierz pozycję **grupy** , a następnie wybierz grupę, do której chcesz zarządzać. Można wyszukiwać lub filtrować listę.

    ![Znajdowanie grupy przypisanej do roli do zarządzania w usłudze PIM](./media/groups-assign-member-owner/groups-list-in-azure-ad.png)

1. Otwórz grupę i wybierz pozycję **dostęp uprzywilejowany (wersja zapoznawcza)**.

    ![Otwórz środowisko Privileged Identity Management](./media/groups-assign-member-owner/groups-discover-groups.png)

1. Wybierz pozycję **Dodaj przypisania**.

    ![Nowe okienko przypisania](./media/groups-assign-member-owner/groups-add-assignment.png)

1. Wybierz członków lub właścicieli, którzy mają być uprawnieni do uprzywilejowanej grupy dostępu.

    ![Zrzut ekranu przedstawiający stronę "Dodawanie przydziałów" z otwartym okienkiem "Wybierz członka lub grupę" i wyróżnioną przyciskiem "Select".](./media/groups-assign-member-owner/add-assignments.png)

1. Wybierz pozycję **dalej** , aby ustawić czas trwania członkostwa lub własności.

    ![Wybierz okienko elementu członkowskiego lub grupy](./media/groups-assign-member-owner/assignment-duration.png)

1. Na liście **Typ przypisania** wybierz pozycję **kwalifikujące się** lub **aktywne**. Uprzywilejowane grupy dostępu udostępniają dwa różne typy przypisania:

    - **Kwalifikujące się** przypisania wymagają, aby członek roli wykonywał akcję do korzystania z roli. Akcje te mogą obejmować przeprowadzenie uwierzytelniania wieloskładnikowego (MFA), podanie uzasadnienia biznesowego lub żądanie zatwierdzenia od wyznaczonych osób zatwierdzających.

    - **Aktywne** przypisania nie wymagają, aby członkowie mogli wykonać żadną akcję w celu korzystania z roli. Członkowie przypisani jako aktywni mają uprawnienia przypisane do roli przez cały czas.

1. Jeśli przypisanie powinno być trwałe (trwale kwalifikujące się lub trwale przypisane), zaznacz pole wyboru **trwałe** . W zależności od ustawień organizacji pole wyboru może nie być wyświetlane lub może nie być edytowalne.

1. Po zakończeniu wybierz pozycję **Przypisz**.

1. Aby utworzyć nowe przypisanie roli, wybierz pozycję **Dodaj**. Zostanie wyświetlone powiadomienie o stanie.

    ![Nowe przypisanie — powiadomienie](./media/groups-assign-member-owner/groups-assignment-notification.png)

## <a name="update-or-remove-an-existing-role-assignment"></a>Aktualizowanie lub usuwanie istniejącego przypisania roli

Wykonaj następujące kroki, aby zaktualizować lub usunąć istniejące przypisanie roli.

1. [Zaloguj się do usługi Azure AD](https://aad.portal.azure.com) za pomocą uprawnień administratora globalnego lub właściciela grupy.
1. Wybierz pozycję **grupy** , a następnie wybierz grupę, do której chcesz zarządzać. Można wyszukiwać lub filtrować listę.

    ![Znajdowanie grupy przypisanej do roli do zarządzania w usłudze PIM](./media/groups-assign-member-owner/groups-list-in-azure-ad.png)

1. Otwórz grupę i wybierz pozycję **dostęp uprzywilejowany (wersja zapoznawcza)**.

    ![Otwórz środowisko Privileged Identity Management](./media/groups-assign-member-owner/groups-discover-groups.png)

1. Wybierz rolę, którą chcesz zaktualizować lub usunąć.

1. Znajdź przypisanie roli na kartach **kwalifikujące się role** lub **role aktywne** .

    ![Aktualizowanie lub usuwanie przypisania roli](./media/groups-assign-member-owner/groups-bring-under-management.png)

1. Wybierz pozycję **Aktualizuj** lub **Usuń** , aby zaktualizować lub usunąć przypisanie roli.

    Informacje o rozszerzaniu przypisania roli znajdują się [w temacie rozszerzanie lub odnawianie ról zasobów platformy Azure w Privileged Identity Management](pim-resource-roles-renew-extend.md).

## <a name="next-steps"></a>Następne kroki

- [Przedłuż lub Odnów role zasobów platformy Azure w Privileged Identity Management](pim-resource-roles-renew-extend.md)
- [Skonfiguruj ustawienia roli zasobów platformy Azure w Privileged Identity Management](pim-resource-roles-configure-role-settings.md)
- [Przypisywanie ról usługi Azure AD w Privileged Identity Management](pim-how-to-add-role-to-user.md)
