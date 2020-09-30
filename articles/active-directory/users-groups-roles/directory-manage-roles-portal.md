---
title: Wyświetlanie i przypisywanie uprawnień roli administratora — usługa Azure AD | Microsoft Docs
description: Teraz można wyświetlać członków roli administratora usługi Azure AD w portalu i zarządzać nimi. Dla osób, które często zarządzają przypisaniami ról.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: how-to
ms.date: 06/15/2020
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: e548e99cc60d67b477fd087b993764bf7f223592
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/29/2020
ms.locfileid: "91541190"
---
# <a name="view-and-assign-administrator-roles-in-azure-active-directory"></a>Wyświetlanie i przypisywanie ról administratora w usłudze Azure Active Directory

Teraz można wyświetlać wszystkich członków ról administratorów w portalu Azure Active Directory i zarządzać nimi. Jeśli często zarządzasz przypisaniami ról, prawdopodobnie wolisz skorzystać z tego środowiska. A jeśli kiedykolwiek zastanawiasz się, że "czego dotyczy ta rola"? ", możesz wyświetlić szczegółową listę uprawnień dla każdej roli administratora usługi Azure AD.

## <a name="view-all-roles"></a>Wyświetl wszystkie role

1. Zaloguj się do [Azure Portal](https://portal.azure.com) i wybierz pozycję **Azure Active Directory**.

1. Wybierz **role i Administratorzy** , aby wyświetlić listę wszystkich dostępnych ról.

1. Wybierz wielokropek po prawej stronie każdego wiersza, aby wyświetlić uprawnienia dla roli. Wybierz rolę, aby wyświetlić użytkowników przypisanych do roli. Jeśli widzisz coś innego od poniższej ilustracji, zapoznaj się z uwagami w temacie [Wyświetlanie przypisań dla ról uprzywilejowanych](#view-assignments-for-privileged-roles) , aby sprawdzić, czy jesteś w PRIVILEGED Identity Management (PIM).

    ![lista ról w portalu usługi Azure AD](./media/directory-manage-roles-portal/view-roles-in-azure-active-directory.png)

## <a name="view-my-roles"></a>Wyświetl moje role

Można również łatwo wyświetlić swoje uprawnienia. Wybierz **rolę** na stronie **role i Administratorzy** , aby wyświetlić role, które są aktualnie przypisane do użytkownika.

## <a name="view-assignments-for-privileged-roles"></a>Wyświetlanie przypisań ról uprzywilejowanych

Możesz wybrać pozycję **Zarządzaj w usłudze PIM** , aby uzyskać dodatkowe możliwości zarządzania. Administratorzy ról uprzywilejowanych mogą zmieniać przypisania "trwałe" (zawsze aktywne w roli) do "kwalifikującego się" (w roli tylko w przypadku podniesionych uprawnień). Jeśli nie masz Privileged Identity Management, nadal możesz wybrać pozycję **Zarządzaj w usłudze PIM** , aby zarejestrować się w celu korzystania z wersji próbnej. Privileged Identity Management wymaga [planu licencji Azure AD — wersja Premium P2](../privileged-identity-management/subscription-requirements.md).

![Lista członków roli administratora](./media/directory-manage-roles-portal/member-list.png)

Jeśli jesteś administratorem globalnym lub administratorem roli uprzywilejowanej, możesz łatwo dodać lub usunąć członków, odfiltrować listę lub wybrać element członkowski, aby wyświetlić aktywne przypisane role.

> [!Note]
> Jeśli masz licencję usługi Azure AD Premium P2 i używasz już Privileged Identity Management, wszystkie zadania związane z zarządzaniem rolami są wykonywane w ramach uprawnień do zarządzania tożsamościami, a nie w usłudze Azure AD.
>
> ![Role usługi Azure AD zarządzane w usłudze PIM dla użytkowników korzystających już z usług PIM i z licencją premium P2](./media/directory-manage-roles-portal/pim-manages-roles-for-p2.png)

## <a name="view-a-users-role-permissions"></a>Wyświetlanie uprawnień roli użytkownika

Gdy przeglądasz członków roli, wybierz pozycję **Opis** , aby wyświetlić pełną listę uprawnień przyznanych przez przypisanie roli. Strona zawiera linki do odpowiedniej dokumentacji ułatwiające zarządzanie rolami w katalogu.

![Zrzut ekranu przedstawiający stronę "Global administrator-Description".](./media/directory-manage-roles-portal/role-description.png)

## <a name="download-role-assignments"></a>Pobieranie przypisań ról

Aby pobrać wszystkie przypisania dla określonej roli, na stronie **role i Administratorzy** wybierz rolę, a następnie wybierz pozycję **Pobierz przypisania ról**. Plik CSV, który wyświetla listę przypisań we wszystkich zakresach dla tej roli, zostanie pobrany.

![pobieranie wszystkich przypisań roli](./media/directory-manage-roles-portal/download-role-assignments.png)

## <a name="assign-a-role"></a>Przypisywanie roli

1. Zaloguj się do [Azure Portal](https://portal.azure.com) z uprawnieniami administratora globalnego lub administratora roli uprzywilejowanej i wybierz pozycję **Azure Active Directory**.

1. Wybierz **role i Administratorzy** , aby wyświetlić listę wszystkich dostępnych ról.

1. Wybierz rolę, aby wyświetlić jej przypisania.

    ![Zrzut ekranu przedstawiający stronę "przydziały administratora użytkownika" z wybraną akcją "Zarządzaj w usłudze PIM".](./media/directory-manage-roles-portal/member-list.png)

1. Wybierz pozycję **Dodaj przypisania** i wybierz role, które chcesz przypisać. Możesz wybrać pozycję **Zarządzaj w usłudze PIM** , aby uzyskać dodatkowe możliwości zarządzania. Jeśli widzisz coś innego od poniższej ilustracji, zapoznaj się z uwagami w temacie [Wyświetlanie przypisań dla ról uprzywilejowanych](#view-assignments-for-privileged-roles) , aby sprawdzić, czy jesteś w usłudze PIM.

    ![Lista uprawnień dla roli administratora](./media/directory-manage-roles-portal/directory-role-select-role.png)

## <a name="next-steps"></a>Następne kroki

* Podziel się z nami na [forum ról administracyjnych usługi Azure AD](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032).
* Aby uzyskać więcej informacji o rolach i przypisaniu roli administratora, zobacz [Przypisywanie ról administratorów](directory-assign-admin-roles.md).
* Domyślne uprawnienia użytkownika można znaleźć w [porównaniu z domyślnymi uprawnieniami gościa i użytkownika](../fundamentals/users-default-permissions.md).
